# 把 Google ADK Agent 暴露为 MCP 服务

讲解通过服务器开放 Agent 或工具能力、连接与传输方式及客户端测试，使其他编码工具可访问这些能力。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/google/adk-docs/commit/b2bf03de977b097338858dd10806b4fe6fa98be3>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/tools-custom/mcp-tools/agent-as-server.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/docs/tools-custom/mcp-tools/agent-as-server.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 将ADKagents配置为MCP服务器

您可以通过托管您的 ADK，使您的 ADK agent 的功能可供外部 MCP 客户端访问，例如 
Antigravity、Claude Code 或自定义 agents agent 在 MCP 服务器内的功能。
有两种主要方法可以实现此目的：

* **公开整个Agent：**包含完整的多轮agent推理和
使用简单的单行转换将内部工具执行到服务器中。

* **公开单独的工具：**涉及手动构建轻量级MCP
服务器包装特定的、独立的 ADK 工具（如 `FunctionTool`），无需 
 agent 的推理循环。

## 开始使用
最有效的方法是公开整个 `LlmAgent`。通过使用 
`to_mcp_server()` 实用程序，您可以将 agent 转换为标准 FastMCP 
 服务器。这允许外部客户端与 agent 的完整认知 
 功能及其内部工具包进行交互。

```python
from google.adk.agents import LlmAgent
from google.adk.tools.load_web_page import load_web_page
from google.adk.tools.mcp_tool import to_mcp_server

# 1. Define your ADK agent
agent = LlmAgent(
    model="gemini-flash-latest", 
    name="web_reader_agent", 
    instruction="Fetch and summarize web content for the user.", 
    tools=[load_web_page], 
)

# 2. Convert the agent into an MCP server
app = to_mcp_server(agent)

if __name__ == "__main__":
    # Runs the agent as a standard stdio MCP server
    app.run()
```

## 暴露个人ADK工具
如果您只想公开单个功能（例如特定的 `FunctionTool`）
，而不需要完整的 agent 推理循环，则必须手动构建 MCP 服务器。

**先决条件：**
您必须在 ADK 环境中安装 MCP 服务器库：
```bash
pip install mcp
```

**实施步骤：**

1. **初始化工具：**实例化想要暴露的ADK工具，如：
   `FunctionTool(load_web_page)`.
2. **列出工具处理程序：**实现MCP服务器的`@app.list_tools()`
处理程序来宣传该工具。您使用 `google.adk.tools.mcp_tool.conversion_utils` 中的 `adk_to_mcp_tool_type` 实用程序 
 将 ADK 工具 
 定义转换为 MCP 架构格式。
3. **调用工具处理程序：**实现`@app.call_tool()`处理程序来接收
客户要求。此处理程序必须确定请求是否与您的 
 包装工具匹配，执行 ADK 工具的 `.run_async()` 方法（传递 
 `tool_context=None`），并将响应格式化为 MCP- 兼容的 
 结构，例如`mcp.types.TextContent`。

## 使用ADK工具构建MCP服务器

1. 为您的MCP服务器创建一个新的Python文件，例如：`my_adk_mcp_server.py`。
2. 使用以下代码在新文件中实现服务器逻辑。以下脚本设置一个公开 ADK `load_web_page` 工具的 MCP 服务器。

```python
import asyncio
import json
import os
from dotenv import load_dotenv

# MCP Server Imports
from mcp import types as mcp_types
from mcp.server.lowlevel import Server, NotificationOptions
from mcp.server.models import InitializationOptions
import mcp.server.stdio 

# ADK Tool Imports
from google.adk.tools.function_tool import FunctionTool
from google.adk.tools.load_web_page import load_web_page 
from google.adk.tools.mcp_tool.conversion_utils import adk_to_mcp_tool_type

load_dotenv()

# 1. Prepare the ADK Tool
adk_tool_to_expose = FunctionTool(load_web_page)

# 2. Create the MCP Server instance
app = Server("adk-tool-exposing-mcp-server")

# 3. Implement the list_tools handler
@app.list_tools()
async def list_mcp_tools() -> list[mcp_types.Tool]:
    mcp_tool_schema = adk_to_mcp_tool_type(adk_tool_to_expose)
    return [mcp_tool_schema]

# 4. Implement the call_tool handler
@app.call_tool()
async def call_mcp_tool(name: str, arguments: dict) -> list[mcp_types.Content]:
    if name == adk_tool_to_expose.name:
        try:
            # Execute the ADK tool
            adk_tool_response = await adk_tool_to_expose.run_async(
                args=arguments, 
                tool_context=None,
            )
            # Format into MCP TextContent
            response_text = json.dumps(adk_tool_response, indent=2)
            return [mcp_types.TextContent(type="text", text=response_text)]
        except Exception as e:
            error_text = json.dumps({"error": str(e)})
            return [mcp_types.TextContent(type="text", text=error_text)]
    else:
        error_text = json.dumps({"error": f"Tool '{name}' not found."})
        return [mcp_types.TextContent(type="text", text=error_text)]

# 5. MCP Server Runner (Stdio)
async def run_mcp_stdio_server():
    async with mcp.server.stdio.stdio_server() as (read_stream, write_stream):
        await app.run(
            read_stream,
            write_stream,
            InitializationOptions(
                server_name=app.name,
                server_version="0.1.0",
                capabilities=app.get_capabilities(
                    notification_options=NotificationOptions(),
                    experimental_capabilities={},
                ),
            ),
        )

if __name__ == "__main__":
    asyncio.run(run_mcp_stdio_server())
```

## 连接和运输方式
当通过这些 MCP 服务器公开您的 ADK 功能时，您通常使用标准 input/output 连接 (`mcp.server.stdio`) 运行 
 它们。此连接允许 
 作为父进程运行的外部客户端应用程序生成您的 
 脚本并直接通过 stdio 流与其进行通信，从而保持 
 连接独立且隔离。

## 使用 ADK Agent 测试您的自定义 MCP 服务器

要测试您的自定义服务器，您需要构建一个充当客户端的 ADK agent。此 agent 使用 `McpToolset` 建立与您刚刚创建的服务器脚本的连接。

1. 在新目录中设置 agent，例如 `./adk_agent_samples/mcp_client_agent/`。创建一个 `agent.py` 文件并在其旁边包含一个 `__init__.py` 以使其可被发现。

```python
from google.adk.agents import LlmAgent
from google.adk.tools.mcp_tool import McpToolset
from google.adk.tools.mcp_tool.mcp_session_manager import StdioConnectionParams
from mcp import StdioServerParameters

# IMPORTANT: Provide the absolute path to the server script you built previously
MCP_SERVER_SCRIPT = "/path/to/your/my_adk_mcp_server.py"

root_agent = LlmAgent(
    model='gemini-flash-latest',
    name='web_reader_mcp_client_agent',
    instruction="Use the 'load_web_page' tool to fetch content from a URL provided by the user.",
    tools=[
        McpToolset(
            connection_params=StdioConnectionParams(
                server_params=StdioServerParameters(
                    command='python3', 
                    args=[MCP_SERVER_SCRIPT], 
                )
            )
        )
    ],
)
```

2. 在终端中导航到 agent 的父目录：

```bash
cd ./adk_agent_samples
adk web
```

3. 打开ADK网页UI并选择web_reader_mcp_client_agent。
4. 使用提示测试连接，例如：*从“https://example.com”*加载内容。

## MCP Google Cloud Genmedia 的服务器

[MCP Genmedia 服务工具 ](<https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio/tree/main/experiments/mcp-genmedia>)
 是一组开源 MCP 服务器，使您能够集成 Google 云 
 生成媒体服务 — 例如 Imagen、Veo、Chirp 3 HD 声音，以及 Lyria — 进入 
 你的 AI 应用程序。

Agent 开发套件 (ADK) 和 [Genkit](<https://genkit.dev/>) 为这些 MCP 工具提供内置 
 支持，让您的 AI agents 有效地编排
生成媒体工作流程。有关实施指南，请参阅[ADK
示例
agent](<https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio/tree/main/experiments/mcp-genmedia/sample-agents/adk>)
和
[Genkit示例](<https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio/tree/main/experiments/mcp-genmedia/sample-agents/genkit>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Configure ADK agents as MCP servers

You can make your ADK agent's capabilities accessible to external MCP clients, such as 
Antigravity, Claude Code, or custom agents, by hosting your ADK agent's capabilities within an MCP server.
There are two primary ways to achieve this:

* **Expose an entire Agent:** Wraps the full multi-turn agent reasoning and 
  internal tool execution into a server using a simple one-line conversion.

* **Expose individual Tools:** Involves manually building a lightweight MCP 
  server to wrap specific, standalone ADK tools (like a `FunctionTool`) without 
  the agent's reasoning loop.

## Get started
The most powerful approach is to expose an entire `LlmAgent`. By using the 
`to_mcp_server()` utility, you can convert your agent into a standard FastMCP 
server. This allows external clients to interact with the agent's full cognitive 
capabilities and its internal toolkit.

```python
from google.adk.agents import LlmAgent
from google.adk.tools.load_web_page import load_web_page
from google.adk.tools.mcp_tool import to_mcp_server

# 1. Define your ADK agent
agent = LlmAgent(
    model="gemini-flash-latest", 
    name="web_reader_agent", 
    instruction="Fetch and summarize web content for the user.", 
    tools=[load_web_page], 
)

# 2. Convert the agent into an MCP server
app = to_mcp_server(agent)

if __name__ == "__main__":
    # Runs the agent as a standard stdio MCP server
    app.run()
```

## Expose individual ADK Tools
If you only want to expose individual capabilities (like a specific `FunctionTool`) 
without the full agent reasoning loop, you must manually build an MCP server.

**Prerequisites:**
You must install the MCP Server library in your ADK environment:
```bash
pip install mcp
```

**Implementation Steps:**

1. **Initialize the Tool:** Instantiate the ADK tool you want to expose, such as:
   `FunctionTool(load_web_page)`.
2. **List Tools Handler:** Implement the MCP server's `@app.list_tools()` 
   handler to advertise the tool. You use the `adk_to_mcp_tool_type` utility 
   from `google.adk.tools.mcp_tool.conversion_utils` to convert the ADK tool 
   definition into the MCP schema format.
3. **Call Tool Handler:** Implement the `@app.call_tool()` handler to receive 
   client requests. This handler must identify if the request matches your 
   wrapped tool, execute the ADK tool's `.run_async()` method (passing 
   `tool_context=None`), and format the response into an MCP-compliant 
   structure, for example `mcp.types.TextContent`.

## Build the MCP server with ADK tools

1. Create a new Python file for your MCP server, for example: `my_adk_mcp_server.py`.
2. Implement server logic with the following code to your new file. This following script sets up an MCP server that exposes the ADK `load_web_page` tool.

```python
import asyncio
import json
import os
from dotenv import load_dotenv

# MCP Server Imports
from mcp import types as mcp_types
from mcp.server.lowlevel import Server, NotificationOptions
from mcp.server.models import InitializationOptions
import mcp.server.stdio 

# ADK Tool Imports
from google.adk.tools.function_tool import FunctionTool
from google.adk.tools.load_web_page import load_web_page 
from google.adk.tools.mcp_tool.conversion_utils import adk_to_mcp_tool_type

load_dotenv()

# 1. Prepare the ADK Tool
adk_tool_to_expose = FunctionTool(load_web_page)

# 2. Create the MCP Server instance
app = Server("adk-tool-exposing-mcp-server")

# 3. Implement the list_tools handler
@app.list_tools()
async def list_mcp_tools() -> list[mcp_types.Tool]:
    mcp_tool_schema = adk_to_mcp_tool_type(adk_tool_to_expose)
    return [mcp_tool_schema]

# 4. Implement the call_tool handler
@app.call_tool()
async def call_mcp_tool(name: str, arguments: dict) -> list[mcp_types.Content]:
    if name == adk_tool_to_expose.name:
        try:
            # Execute the ADK tool
            adk_tool_response = await adk_tool_to_expose.run_async(
                args=arguments, 
                tool_context=None,
            )
            # Format into MCP TextContent
            response_text = json.dumps(adk_tool_response, indent=2)
            return [mcp_types.TextContent(type="text", text=response_text)]
        except Exception as e:
            error_text = json.dumps({"error": str(e)})
            return [mcp_types.TextContent(type="text", text=error_text)]
    else:
        error_text = json.dumps({"error": f"Tool '{name}' not found."})
        return [mcp_types.TextContent(type="text", text=error_text)]

# 5. MCP Server Runner (Stdio)
async def run_mcp_stdio_server():
    async with mcp.server.stdio.stdio_server() as (read_stream, write_stream):
        await app.run(
            read_stream,
            write_stream,
            InitializationOptions(
                server_name=app.name,
                server_version="0.1.0",
                capabilities=app.get_capabilities(
                    notification_options=NotificationOptions(),
                    experimental_capabilities={},
                ),
            ),
        )

if __name__ == "__main__":
    asyncio.run(run_mcp_stdio_server())
```

## Connection and transport modes
When exposing your ADK capabilities via these MCP servers, you typically run 
them using a standard input/output connection (`mcp.server.stdio`). This connection allows 
the external client application, running as a parent process, to spawn your 
script and communicate with it directly over the stdio streams, keeping 
the connection self-contained and isolated.

## Test your custom MCP server with an ADK Agent

To test your custom server, you need to build an ADK agent that acts as a client. This agent uses the `McpToolset` to establish a connection to the server script you just created.

1. Set up your agent in a new directory such as `./adk_agent_samples/mcp_client_agent/`. Create an `agent.py` file and include an `__init__.py` alongside it to make it discoverable.

```python
from google.adk.agents import LlmAgent
from google.adk.tools.mcp_tool import McpToolset
from google.adk.tools.mcp_tool.mcp_session_manager import StdioConnectionParams
from mcp import StdioServerParameters

# IMPORTANT: Provide the absolute path to the server script you built previously
MCP_SERVER_SCRIPT = "/path/to/your/my_adk_mcp_server.py"

root_agent = LlmAgent(
    model='gemini-flash-latest',
    name='web_reader_mcp_client_agent',
    instruction="Use the 'load_web_page' tool to fetch content from a URL provided by the user.",
    tools=[
        McpToolset(
            connection_params=StdioConnectionParams(
                server_params=StdioServerParameters(
                    command='python3', 
                    args=[MCP_SERVER_SCRIPT], 
                )
            )
        )
    ],
)
```

2. Navigate to your agent's parent directory in the terminal:

```bash
cd ./adk_agent_samples
adk web
```

3. Open the ADK Web UI and select the web_reader_mcp_client_agent.
4. Test the connection with a prompt such as: *Load the content from "https://example.com"*.

## MCP Servers for Google Cloud Genmedia

[MCP Tools for Genmedia Services](<https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio/tree/main/experiments/mcp-genmedia>)
is a set of open-source MCP servers that enable you to integrate Google Cloud
generative media services—such as Imagen, Veo, Chirp 3 HD voices, and Lyria—into
your AI applications.

Agent Development Kit (ADK) and [Genkit](<https://genkit.dev/>) provide built-in
support for these MCP tools, allowing your AI agents to effectively orchestrate
generative media workflows. For implementation guidance, refer to the [ADK
example
agent](<https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio/tree/main/experiments/mcp-genmedia/sample-agents/adk>)
and the
[Genkit example](<https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio/tree/main/experiments/mcp-genmedia/sample-agents/genkit>).


</details>
