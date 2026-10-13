# 通过子 Agent 管理 Google ADK 的 MCP

说明工具封装、范围过滤、传输、连接生命周期与架构职责，减少大型协作系统中的工具混杂。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/google/adk-docs/commit/b9c4a37b6e0f80f99bc458d89abbff4a3979076c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/tools-custom/mcp-tools/agent-managed.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/docs/tools-custom/mcp-tools/agent-managed.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用子Agents管理MCP

您已经了解了如何使用 
**直接 MCP 工具集成 ** 将您的 agent 连接到外部资源，以及如何通过 **Agent 公开向外部客户端 
 提供您的 agent 服务MCP服务器**。然而，随着您的 ADK 多 agent 
 能力的增长，您可能需要一种方法来隔离复杂的多步骤推理 
，而不会使单个模型的上下文窗口过载。

## 开始使用

```python
import os
from google.adk.agents.llm_agent import LlmAgent
from google.adk.tools.agent_tool import AgentTool
from google.adk.tools.mcp_tool import McpToolset
from google.adk.tools.mcp_tool.mcp_session_manager import StdioConnectionParams
from mcp import StdioServerParameters

# Step 1: Initialize your MCP connection 
mcp_connection = StdioConnectionParams(
    server_params=StdioServerParameters(
        command='npx',
        args=['-y', '@modelcontextprotocol/server-postgres', 'postgres://user:pass@localhost:5432/db'],
    )
)

# Step 2: Create the child Sub-Agent and attach the McpToolset
database_sub_agent = LlmAgent(
    name="database_sub_agent",
    model="gemini-flash-latest",
    description="Delegates tasks to a database specialist...",
    instruction="You are a SQL expert...",
    tools=[McpToolset(connection_params=mcp_connection, tool_filter=['query_db', 'list_tables'])]
)
database_tool = AgentTool(agent=database_sub_agent)


# Step 3: Provide the AgentTool to your primary root agent
root_agent = LlmAgent(
    name="primary_orchestrator",
    model="gemini-pro-latest",
    instruction="You are the main assistant. You have access to specialized agents. Delegate data retrieval tasks to your database tool.",
    tools=[database_tool]
)
```

## 架构角色和工具适配
在 ADK 框架中，`AgentTool` 包装了 `LlmAgent`，将其作为本机 
 工具公开给父协调器。子级 agent 通过 `McpToolset`、
 使用工具，连接到底层 MCP 服务器（通过 `list_tools`），将 
 外部 MCP 工具定义转换为ADK-兼容`BaseTool`实例，并且
异步代理所有执行调用(`call_tool`)。

## 工具范围和认知过滤（`tool_filter`）
将 `McpToolset` 分配给专用子 agent 时，您可以使用 `tool_filter` 参数限制 
 可用的工具 agent：
* **认知焦点：**仅公开与子agent相关的具体操作
授权，例如“read_file”、“list_directory”。
* **安全和沙箱：**防止意外访问危险工具
由 MCP 服务器公开，将执行表面与不可预测的 
 输入隔离。

## 连接传输模式
子 agent 的 `McpToolset` 必须配置两种主要 
 连接模式之一，具体取决于部署架构：
* **本地子进程（`StdioConnectionParams`）：**生成本地可执行文件或
例如，通过 `npx` 或 `python` 进行进程，通过标准 input/output. 
 进行通信 最适合独立的单容器环境。
* **远程网络（`StreamableHTTPConnectionParams` / `SseConnectionParams`）：**
通过 HTTP/SSE 与标头连接，例如 `X-Goog-Api-Key` 或 `Authorization`。 
 最适合可扩展、多租户或独立管理的 MCP 后端。

## 定义和生命周期规则
* **生产同步实例化：**部署多个agent时
设置、Cloud Run、GKE、Agent 引擎，子 agent 及其 `McpToolset` 
 必须在 `agent.py` 中同步实例化，而不是在异步 
 工厂函数内实例化。
* **会话保持和恢复：** `McpToolset`支持序列化
通过 `getstate` 和 `setstate`。虽然会话状态在 
 生命周期事件中保留，但当 agent 进程恢复时，MCP socket/stdio 连接会动态重新初始化 
。
* **清理管理：**在自定义运行时中在`adk web`之外运行时，
通过 `toolset.close()` 或退出堆栈显式拆卸可确保 
 后台服务器子进程和网络连接完全终止。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Manage MCP with Sub-Agents

You have already seen how to connect your agent to external resources using 
**Direct MCP Tool Integration** and how to serve your agent to outside clients 
via an **Agent-Exposed MCP Server**. However, as your ADK multi-agent 
capabilities grow, you may need a way to isolate complex, multi-step reasoning 
without overloading a single model's context window.

## Get started

```python
import os
from google.adk.agents.llm_agent import LlmAgent
from google.adk.tools.agent_tool import AgentTool
from google.adk.tools.mcp_tool import McpToolset
from google.adk.tools.mcp_tool.mcp_session_manager import StdioConnectionParams
from mcp import StdioServerParameters

# Step 1: Initialize your MCP connection 
mcp_connection = StdioConnectionParams(
    server_params=StdioServerParameters(
        command='npx',
        args=['-y', '@modelcontextprotocol/server-postgres', 'postgres://user:pass@localhost:5432/db'],
    )
)

# Step 2: Create the child Sub-Agent and attach the McpToolset
database_sub_agent = LlmAgent(
    name="database_sub_agent",
    model="gemini-flash-latest",
    description="Delegates tasks to a database specialist...",
    instruction="You are a SQL expert...",
    tools=[McpToolset(connection_params=mcp_connection, tool_filter=['query_db', 'list_tables'])]
)
database_tool = AgentTool(agent=database_sub_agent)


# Step 3: Provide the AgentTool to your primary root agent
root_agent = LlmAgent(
    name="primary_orchestrator",
    model="gemini-pro-latest",
    instruction="You are the main assistant. You have access to specialized agents. Delegate data retrieval tasks to your database tool.",
    tools=[database_tool]
)
```

## Architectural role and tool adaptation
In the ADK framework, `AgentTool` wraps an `LlmAgent` to expose it as a native 
tool to a parent orchestrator. The child agent consumes tools via `McpToolset`, 
which connects to the underlying MCP server (via `list_tools`), converts the 
external MCP tool definitions into ADK-compatible `BaseTool` instances, and 
proxies all execution calls (`call_tool`) asynchronously.

## Tool scoping and cognitive filtering (`tool_filter`)
When assigning an `McpToolset` to a specialized sub-agent, you can restrict the 
exact tools made available to that agent using the `tool_filter` parameter:
* **Cognitive Focus:** Exposes only the specific actions relevant to the sub-agent's 
  mandate, such as, 'read_file', 'list_directory'.
* **Security and sandboxing:** Prevents unintended access to dangerous tools 
  exposed by the MCP server, isolating the execution surface from unpredictable 
  inputs.

## Connection Transport Modes
The sub-agent's `McpToolset` must be configured with one of two primary 
connection modes depending on deployment architecture:
* **Local Subprocess (`StdioConnectionParams`):** Spawns a local executable or 
  process, for example, via `npx` or `python`, communicating over standard input/output. 
  Best for self-contained single-container environments.
* **Remote Network (`StreamableHTTPConnectionParams` / `SseConnectionParams`):** 
  Connects over HTTP/SSE with headers, such as `X-Goog-Api-Key` or `Authorization`. 
  Best for scalable, multi-tenant, or independently managed MCP backends.

## Definition and lifecycle rules
* **Synchronous Instantiation for Production:** When deploying multi-agent 
  setups, Cloud Run, GKE, Agent Engine, both the sub-agent and its `McpToolset` 
  must be instantiated synchronously in `agent.py` rather than inside an async 
  factory function.
* **Session Persistence and restoration:** `McpToolset` supports serialization 
  via `getstate` and `setstate`. While session state is preserved across 
  lifecycle events, MCP socket/stdio connections are re-initialized dynamically 
  when the agent process restores.
* **Cleanup Management:** When running outside `adk web` in custom runtimes, 
  explicit teardown via `toolset.close()` or an exit stack ensures that 
  background server subprocesses and network connections are terminated cleanly.


</details>
