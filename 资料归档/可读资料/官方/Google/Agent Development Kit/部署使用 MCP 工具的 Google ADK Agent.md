# 部署使用 MCP 工具的 Google ADK Agent

解释容器和分布式部署中的同步定义要求、标准输入输出或 HTTP 连接、边车模式、生命周期及监控。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/google/adk-docs/commit/b9c4a37b6e0f80f99bc458d89abbff4a3979076c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/tools-custom/mcp-tools/deployment.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/部署使用 MCP 工具的 Google ADK Agent.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 MCP 工具部署 Agents

将使用 MCP 工具的 ADK agents 部署到 Cloud Run、GKE 或 Agent Runtime 等生产环境时，您需要考虑 MCP 连接如何在容器化和分布式环境中工作。

## 关键部署要求：同步 Agent 定义

！！！警告

    部署时 agents 与 MCP 工具、 agent 和它的 McpToolset 必须定义 **同步地** 在你的 `agent.py` 文件。同时 `adk web` 允许异步 agent 创建、部署环境需要同步实例化。

```python
# CORRECT: Synchronous agent definition for deployment
import os
from google.adk.agents.llm_agent import LlmAgent
from google.adk.tools.mcp_tool import McpToolset
from google.adk.tools.mcp_tool.mcp_session_manager import StdioConnectionParams
from mcp import StdioServerParameters

_allowed_path = os.path.dirname(os.path.abspath(__file__))

root_agent = LlmAgent(
    model='gemini-flash-latest',
    name='enterprise_assistant',
    instruction=f'Help user accessing their file systems. Allowed directory: {_allowed_path}',
    tools=[
        McpToolset(
            connection_params=StdioConnectionParams(
                server_params=StdioServerParameters(
                    command='npx',
                    args=['-y', '@modelcontextprotocol/server-filesystem', _allowed_path],
                ),
                timeout=5,  # Configure appropriate timeouts
            ),
            # Filter tools for security in production
            tool_filter=[
                'read_file', 'read_multiple_files', 'list_directory',
                'directory_tree', 'search_files', 'get_file_info',
                'list_allowed_directories',
            ],
        )
    ],
)
```

```python
# WRONG: Asynchronous patterns don't work in deployment
async def get_agent():  # This won't work for deployment
    toolset = await create_mcp_toolset_async()
    return LlmAgent(tools=[toolset])
```

## 快速部署命令

### Agent 运行时
```bash
uv run adk deploy agent_engine \
  --project=<your-gcp-project-id> \
  --region=<your-gcp-region> \
  --display_name="My MCP Agent" \
  ./path/to/your/agent_directory
```

### 云跑
```bash
uv run adk deploy cloud_run \
  --project=<your-gcp-project-id> \
  --region=<your-gcp-region> \
  --service_name=<your-service-name> \
  ./path/to/your/agent_directory
```

## 部署模式

### 模式 1：独立 Stdio MCP 服务器

对于可以打包为 npm 包或 Python 模块（如 `@modelcontextprotocol/server-filesystem`）的 MCP 服务器，您可以将它们直接包含在 agent 容器中：

**集装箱要求：**
```dockerfile
# Example for npm-based MCP servers
FROM python:3.13-slim

# Install Node.js and npm for MCP servers
RUN apt-get update && apt-get install -y nodejs npm && rm -rf /var/lib/apt/lists/*

# Install your Python dependencies
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copy your agent code
COPY . .

# Your agent can now use StdioConnectionParams with 'npx' commands
CMD ["python", "main.py"]
```

**Agent 配置：**
```python
# This works in containers because npx and the MCP server run in the same environment
McpToolset(
    connection_params=StdioConnectionParams(
        server_params=StdioServerParameters(
            command='npx',
            args=["-y", "@modelcontextprotocol/server-filesystem", "/app/data"],
        ),
    ),
)
```

### 模式 2：远程 MCP 服务器（可流式传输 HTTP）

对于需要可扩展性的生产部署，请将 MCP 服务器部署为单独的服务并通过 Streamable HTTP 连接：

**MCP 服务器部署（云运行）：**
```python
# deploy_mcp_server.py - Separate Cloud Run service using Streamable HTTP
import contextlib
import logging
from collections.abc import AsyncIterator
from typing import Any

import mcp.types as types
from mcp.server.lowlevel import Server
from mcp.server.streamable_http_manager import StreamableHTTPSessionManager
from starlette.applications import Starlette
from starlette.routing import Mount
from starlette.types import Receive, Scope, Send

logger = logging.getLogger(__name__)

def create_mcp_server():
    """Create and configure the MCP server."""
    app = Server("adk-mcp-streamable-server")

    @app.call_tool()
    async def call_tool(name: str, arguments: dict[str, Any]) -> list[types.ContentBlock]:
        """Handle tool calls from MCP clients."""
        # Example tool implementation - replace with your actual ADK tools
        if name == "example_tool":
            result = arguments.get("input", "No input provided")
            return [
                types.TextContent(
                    type="text",
                    text=f"Processed: {result}"
                )
            ]
        else:
            raise ValueError(f"Unknown tool: {name}")

    @app.list_tools()
    async def list_tools() -> list[types.Tool]:
        """List available tools."""
        return [
            types.Tool(
                name="example_tool",
                description="Example tool for demonstration",
                inputSchema={
                    "type": "object",
                    "properties": {
                        "input": {
                            "type": "string",
                            "description": "Input text to process"
                        }
                    },
                    "required": ["input"]
                }
            )
        ]

    return app

def main(port: int = 8080, json_response: bool = False):
    """Main server function."""
    logging.basicConfig(level=logging.INFO)

    app = create_mcp_server()

    # Create session manager with stateless mode for scalability
    session_manager = StreamableHTTPSessionManager(
        app=app,
        event_store=None,
        json_response=json_response,
        stateless=True,  # Important for Cloud Run scalability
    )

    async def handle_streamable_http(scope: Scope, receive: Receive, send: Send) -> None:
        await session_manager.handle_request(scope, receive, send)

    @contextlib.asynccontextmanager
    async def lifespan(app: Starlette) -> AsyncIterator[None]:
        """Manage session manager lifecycle."""
        async with session_manager.run():
            logger.info("MCP Streamable HTTP server started!")
            try:
                yield
            finally:
                logger.info("MCP server shutting down...")

    # Create ASGI application
    starlette_app = Starlette(
        debug=False,  # Set to False for production
        routes=[
            Mount("/mcp", app=handle_streamable_http),
        ],
        lifespan=lifespan,
    )

    import uvicorn
    uvicorn.run(starlette_app, host="0.0.0.0", port=port)

if __name__ == "__main__":
    main()
```

**Agent 远程配置 MCP:**

=== "Python"

    ```python
    # Your ADK agent connects to the remote MCP service via Streamable HTTP
    McpToolset(
        connection_params=StreamableHTTPConnectionParams(
            url="https://your-mcp-server-url.run.app/mcp",
            headers={"Authorization": "Bearer your-auth-token"}
        ),
    )
    ```

=== "Java"

    ```java
    import java.util.Map;
    import com.google.adk.tools.mcp.StreamableHttpServerParameters;
    import com.google.adk.tools.mcp.McpToolset;

    // Your ADK agent connects to the remote MCP service via Streamable HTTP
    StreamableHttpServerParameters streamableParams = StreamableHttpServerParameters.builder()
            .url("https://your-mcp-server-url.run.app/mcp")
            .headers(Map.of("Authorization", "Bearer your-auth-token"))
            .build();

    McpToolset toolset = new McpToolset(streamableParams);
    ```

=== "Kotlin"

    ```kotlin
    import com.google.adk.kt.tools.mcp.McpConnectionParameters
    import com.google.adk.kt.tools.mcp.McpToolset

    // Your ADK agent connects to the remote MCP service via Streamable HTTP
    // headerProvider is suspend, so fetchToken() can await a fresh token per request;
    // it also disables session reuse, so use StreamableHttp(headers = ...) for a fixed one.
    val toolset =
        McpToolset.McpToolsetConfig(
            streamableHttpConnectionParams =
                McpConnectionParameters.StreamableHttp(
                    url = "https://your-mcp-server-url.run.app/mcp",
                ),
        ).toToolset(headerProvider = { mapOf("Authorization" to "Bearer ${fetchToken()}") })
    ```

### 模式 3：Sidecar MCP 服务器 (GKE)

在 Kubernetes 环境中，您可以将 MCP 服务器部署为 sidecar 容器：

```yaml
# deployment.yaml - GKE with MCP sidecar
apiVersion: apps/v1
kind: Deployment
metadata:
  name: adk-agent-with-mcp
spec:
  template:
    spec:
      containers:
      # Main ADK agent container
      - name: adk-agent
        image: your-adk-agent:latest
        ports:
        - containerPort: 8080
        env:
        - name: MCP_SERVER_URL
          value: "http://localhost:8081"

      # MCP server sidecar
      - name: mcp-server
        image: your-mcp-server:latest
        ports:
        - containerPort: 8081
```

## 连接管理注意事项

根据您的扩展和基础设施需求选择连接类型。

### 立体声连接
*   **优点：**设置简单，进程隔离，并且在容器中运行良好。
*   **缺点：**进程开销；不适合大规模部署。
*   **最适合：**开发、单租户部署和简单的MCP服务器。

### SSE/HTTP 连接
*   **优点：**基于网络，可扩展，并处理多个客户端。
*   **缺点：**需要网络基础设施和身份验证复杂性。
*   **最适合：**生产部署、多租户系统、外部MCP服务和大流量。

## 生产部署指南

使用 MCP 工具将 agents 部署到生产环境时，请遵循这些核心准则。

### 连接生命周期
*   使用标准退出堆栈模式正确清理 MCP 连接。
*   为连接建立和请求配置适当的超时。
*   实现重试逻辑以优雅地处理瞬时连接失败。

### 资源管理
*   密切监视 stdio MCP 服务器的内存使用情况，因为每个连接都会生成一个新进程。
*   为 MCP 服务器进程配置适当的 CPU 和内存限制。
*   为远程 MCP 服务器实施连接池以优化资源消耗。

### 安全性
！！！重要的“安全最佳实践”
    *   对所有远程 MCP 连接使用严格的身份验证标头。
    *   严格限制 ADK agents 和 MCP 服务器之间的网络访问。
    *   使用 `tool_filter` 过滤 MCP 工具以严格限制公开的功能。
    *   验证所有 MCP 工具输入以防止提示或命令注入攻击。
    *   对文件系统 MCP 服务器使用限制性绝对文件路径（例如，`os.path.dirname(os.path.abspath(__file__))`）。
    *   尽可能在生产环境中应用只读工具过滤器。

### 监控和可观察性
*   记录所有 MCP 连接建立和拆除事件。
*   监控 MCP 工具执行时间和总体成功率。
*   针对重复出现的 MCP 连接失败设置自动警报。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Deploy Agents with MCP Tools

When deploying ADK agents that use MCP tools to production environments like Cloud Run, GKE, or Agent Runtime, you need to consider how MCP connections will work in containerized and distributed environments.

## Critical Deployment Requirement: Synchronous Agent Definition

!!! warning

    When deploying agents with MCP tools, the agent and its McpToolset must be defined **synchronously** in your `agent.py` file. While `adk web` allows for asynchronous agent creation, deployment environments require synchronous instantiation.

```python
# CORRECT: Synchronous agent definition for deployment
import os
from google.adk.agents.llm_agent import LlmAgent
from google.adk.tools.mcp_tool import McpToolset
from google.adk.tools.mcp_tool.mcp_session_manager import StdioConnectionParams
from mcp import StdioServerParameters

_allowed_path = os.path.dirname(os.path.abspath(__file__))

root_agent = LlmAgent(
    model='gemini-flash-latest',
    name='enterprise_assistant',
    instruction=f'Help user accessing their file systems. Allowed directory: {_allowed_path}',
    tools=[
        McpToolset(
            connection_params=StdioConnectionParams(
                server_params=StdioServerParameters(
                    command='npx',
                    args=['-y', '@modelcontextprotocol/server-filesystem', _allowed_path],
                ),
                timeout=5,  # Configure appropriate timeouts
            ),
            # Filter tools for security in production
            tool_filter=[
                'read_file', 'read_multiple_files', 'list_directory',
                'directory_tree', 'search_files', 'get_file_info',
                'list_allowed_directories',
            ],
        )
    ],
)
```

```python
# WRONG: Asynchronous patterns don't work in deployment
async def get_agent():  # This won't work for deployment
    toolset = await create_mcp_toolset_async()
    return LlmAgent(tools=[toolset])
```

## Quick Deployment Commands

### Agent Runtime
```bash
uv run adk deploy agent_engine \
  --project=<your-gcp-project-id> \
  --region=<your-gcp-region> \
  --display_name="My MCP Agent" \
  ./path/to/your/agent_directory
```

### Cloud Run
```bash
uv run adk deploy cloud_run \
  --project=<your-gcp-project-id> \
  --region=<your-gcp-region> \
  --service_name=<your-service-name> \
  ./path/to/your/agent_directory
```

## Deployment Patterns

### Pattern 1: Self-Contained Stdio MCP Servers

For MCP servers that can be packaged as npm packages or Python modules (like `@modelcontextprotocol/server-filesystem`), you can include them directly in your agent container:

**Container Requirements:**
```dockerfile
# Example for npm-based MCP servers
FROM python:3.13-slim

# Install Node.js and npm for MCP servers
RUN apt-get update && apt-get install -y nodejs npm && rm -rf /var/lib/apt/lists/*

# Install your Python dependencies
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copy your agent code
COPY . .

# Your agent can now use StdioConnectionParams with 'npx' commands
CMD ["python", "main.py"]
```

**Agent Configuration:**
```python
# This works in containers because npx and the MCP server run in the same environment
McpToolset(
    connection_params=StdioConnectionParams(
        server_params=StdioServerParameters(
            command='npx',
            args=["-y", "@modelcontextprotocol/server-filesystem", "/app/data"],
        ),
    ),
)
```

### Pattern 2: Remote MCP Servers (Streamable HTTP)

For production deployments requiring scalability, deploy MCP servers as separate services and connect via Streamable HTTP:

**MCP Server Deployment (Cloud Run):**
```python
# deploy_mcp_server.py - Separate Cloud Run service using Streamable HTTP
import contextlib
import logging
from collections.abc import AsyncIterator
from typing import Any

import mcp.types as types
from mcp.server.lowlevel import Server
from mcp.server.streamable_http_manager import StreamableHTTPSessionManager
from starlette.applications import Starlette
from starlette.routing import Mount
from starlette.types import Receive, Scope, Send

logger = logging.getLogger(__name__)

def create_mcp_server():
    """Create and configure the MCP server."""
    app = Server("adk-mcp-streamable-server")

    @app.call_tool()
    async def call_tool(name: str, arguments: dict[str, Any]) -> list[types.ContentBlock]:
        """Handle tool calls from MCP clients."""
        # Example tool implementation - replace with your actual ADK tools
        if name == "example_tool":
            result = arguments.get("input", "No input provided")
            return [
                types.TextContent(
                    type="text",
                    text=f"Processed: {result}"
                )
            ]
        else:
            raise ValueError(f"Unknown tool: {name}")

    @app.list_tools()
    async def list_tools() -> list[types.Tool]:
        """List available tools."""
        return [
            types.Tool(
                name="example_tool",
                description="Example tool for demonstration",
                inputSchema={
                    "type": "object",
                    "properties": {
                        "input": {
                            "type": "string",
                            "description": "Input text to process"
                        }
                    },
                    "required": ["input"]
                }
            )
        ]

    return app

def main(port: int = 8080, json_response: bool = False):
    """Main server function."""
    logging.basicConfig(level=logging.INFO)

    app = create_mcp_server()

    # Create session manager with stateless mode for scalability
    session_manager = StreamableHTTPSessionManager(
        app=app,
        event_store=None,
        json_response=json_response,
        stateless=True,  # Important for Cloud Run scalability
    )

    async def handle_streamable_http(scope: Scope, receive: Receive, send: Send) -> None:
        await session_manager.handle_request(scope, receive, send)

    @contextlib.asynccontextmanager
    async def lifespan(app: Starlette) -> AsyncIterator[None]:
        """Manage session manager lifecycle."""
        async with session_manager.run():
            logger.info("MCP Streamable HTTP server started!")
            try:
                yield
            finally:
                logger.info("MCP server shutting down...")

    # Create ASGI application
    starlette_app = Starlette(
        debug=False,  # Set to False for production
        routes=[
            Mount("/mcp", app=handle_streamable_http),
        ],
        lifespan=lifespan,
    )

    import uvicorn
    uvicorn.run(starlette_app, host="0.0.0.0", port=port)

if __name__ == "__main__":
    main()
```

**Agent Configuration for Remote MCP:**

=== "Python"

    ```python
    # Your ADK agent connects to the remote MCP service via Streamable HTTP
    McpToolset(
        connection_params=StreamableHTTPConnectionParams(
            url="https://your-mcp-server-url.run.app/mcp",
            headers={"Authorization": "Bearer your-auth-token"}
        ),
    )
    ```

=== "Java"

    ```java
    import java.util.Map;
    import com.google.adk.tools.mcp.StreamableHttpServerParameters;
    import com.google.adk.tools.mcp.McpToolset;

    // Your ADK agent connects to the remote MCP service via Streamable HTTP
    StreamableHttpServerParameters streamableParams = StreamableHttpServerParameters.builder()
            .url("https://your-mcp-server-url.run.app/mcp")
            .headers(Map.of("Authorization", "Bearer your-auth-token"))
            .build();

    McpToolset toolset = new McpToolset(streamableParams);
    ```

=== "Kotlin"

    ```kotlin
    import com.google.adk.kt.tools.mcp.McpConnectionParameters
    import com.google.adk.kt.tools.mcp.McpToolset

    // Your ADK agent connects to the remote MCP service via Streamable HTTP
    // headerProvider is suspend, so fetchToken() can await a fresh token per request;
    // it also disables session reuse, so use StreamableHttp(headers = ...) for a fixed one.
    val toolset =
        McpToolset.McpToolsetConfig(
            streamableHttpConnectionParams =
                McpConnectionParameters.StreamableHttp(
                    url = "https://your-mcp-server-url.run.app/mcp",
                ),
        ).toToolset(headerProvider = { mapOf("Authorization" to "Bearer ${fetchToken()}") })
    ```

### Pattern 3: Sidecar MCP Servers (GKE)

In Kubernetes environments, you can deploy MCP servers as sidecar containers:

```yaml
# deployment.yaml - GKE with MCP sidecar
apiVersion: apps/v1
kind: Deployment
metadata:
  name: adk-agent-with-mcp
spec:
  template:
    spec:
      containers:
      # Main ADK agent container
      - name: adk-agent
        image: your-adk-agent:latest
        ports:
        - containerPort: 8080
        env:
        - name: MCP_SERVER_URL
          value: "http://localhost:8081"

      # MCP server sidecar
      - name: mcp-server
        image: your-mcp-server:latest
        ports:
        - containerPort: 8081
```

## Connection management considerations

Choose your connection type based on your scaling and infrastructure needs. 

### Stdio connections
*   **Pros:** Simple setup, process isolation, and works well in containers.
*   **Cons:** Process overhead; not suitable for high-scale deployments.
*   **Best for:** Development, single-tenant deployments, and simple MCP servers.

### SSE/HTTP connections
*   **Pros:** Network-based, scalable, and handles multiple clients.
*   **Cons:** Requires network infrastructure and authentication complexity.
*   **Best for:** Production deployments, multi-tenant systems, external MCP services, and high-volume traffic.

## Production deployment guidelines

Follow these core guidelines when deploying agents with MCP tools to production environments.

### Connection lifecycle
*   Clean up MCP connections properly using standard exit stack patterns.
*   Configure appropriate timeouts for connection establishment and requests.
*   Implement retry logic to handle transient connection failures gracefully.

### Resource management
*   Monitor memory usage closely for stdio MCP servers, as each connection spawns a new process.
*   Configure appropriate CPU and memory limits for MCP server processes.
*   Implement connection pooling for remote MCP servers to optimize resource consumption.

### Security
!!! important "Security Best Practices"
    *   Use strict authentication headers for all remote MCP connections.
    *   Restrict network access tightly between ADK agents and MCP servers.
    *   Filter MCP tools using `tool_filter` to strictly limit exposed functionality.
    *   Validate all MCP tool inputs to prevent prompt or command injection attacks.
    *   Use restrictive, absolute file paths for filesystem MCP servers (for example, `os.path.dirname(os.path.abspath(__file__))`).
    *   Apply read-only tool filters in production environments whenever possible.

### Monitoring and observability
*   Log all MCP connection establishment and teardown events.
*   Monitor MCP tool execution times and overall success rates.
*   Set up automated alerts for recurring MCP connection failures.


</details>
