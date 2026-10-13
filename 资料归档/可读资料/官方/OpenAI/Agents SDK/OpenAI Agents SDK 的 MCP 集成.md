# OpenAI Agents SDK 的 MCP 集成

比较托管、HTTP、SSE 和标准输入输出服务，说明认证审批、工具过滤、输出、元数据和生命周期管理。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-10-04](<https://github.com/openai/openai-agents-python/commit/d3304cff56f1be2325d00e0f172534d613697f88>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/docs/mcp.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/docs/mcp.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Model context protocol (MCP)

[Model context protocol](<https://modelcontextprotocol.io/introduction>) (MCP) 标准化了应用程序如何向语言模型公开工具和 
 上下文。来自官方文档：

> MCP 是一个开放协议，它标准化了应用程序如何向 LLM 提供上下文。将 MCP 想象为 AI
>  应用程序的 USB-C 端口。正如 USB-C 提供了将您的设备连接到各种外围设备和配件的标准化方法一样，MCP
>  也提供了将 AI 模型连接到不同数据源和工具的标准化方法。

Agents Python SDK 理解多种 MCP 传输。这使您可以重用现有的 MCP 服务器或构建自己的服务器以向 agent 公开文件系统、HTTP 或连接器支持的工具。

！！！警告“连接前信任 MCP 服务器”

MCP 工具可以公开模型上下文中的数据并使用您提供的凭据执行操作。仅连接到您信任的服务器，使用最低权限凭据，将访问令牌保留在授权字段或标头而不是 URL 中，并且敏感操作需要批准。请参阅 [OpenAI MCP 安全指南 ](<https://developers.openai.com/api/docs/guides/tools-connectors-mcp#risks-and-safety>)。

## 选择 MCP 集成

在将 MCP 服务器连接到 agent 之前，请确定工具调用应在何处执行以及您可以访问哪些传输。下面的矩阵总结了 Python SDK 支持的选项。

|你需要什么|推荐选项|
| ------------------------------------------------------------------------------------ | ----------------------------------------------------- |
|让 OpenAI 的响应 API 代表模型调用可公开访问的 MCP 服务器| **主办 MCP 服务器工具** 通过 [`HostedMCPTool`][agents.工具。HostedMCPTool] |
|连接到您本地或远程运行的 Streamable HTTP 服务器|**可流式传输 HTTP MCP 服务器 ** 通过 [`MCPServerStreamableHttp`][agents.mcp.server.MCPServerStreamableHttp]|
|通过服务器发送事件与实现 HTTP 的服务器对话|**HTTP 与 SSE MCP 服务器 ** 通过 [`MCPServerSse`][agents.mcp.server.MCPServerSse]|
|启动本地进程并通过 stdin/stdout 进行通信|**stdio MCP 服务器** 通过 [`MCPServerStdio`][agents.mcp.server.MCPServerStdio]|

下面的部分将介绍每个选项、如何配置它以及何时优先选择一种传输而不是另一种。

对于远程 MCP 服务器，请从服务器提供商的文档中获取端点 URL 和身份验证要求。您还可以在[官方MCP注册表](<https://registry.modelcontextprotocol.io/>)中找到已发布的服务器。使用提供商的 MCP 端点 URL 配置 SDK，该端点可能与提供商的网站 URL 不同。

## MCP Python SDK v1 和 v2

Agents SDK 通过依赖范围 `mcp>=1.19.0,<3` 支持 `mcp` Python 包的两个主要版本。安装的`mcp`软件包版本与与服务器协商的MCP协议版本不同。 Agents SDK 会检测安装的软件包主要版本并自动适应 stdio、SSE 和 Streamable HTTP 连接，因此普通服务器配置不需要版本切换。

当安装MCP Python SDK v2时，Agents SDK会创建v2 `mcp.Client`，周围有`mode="auto"`配置的本地传输。客户端首先以已安装的MCP SDK支持的最新协议版本发送`server/discover`探测。现代服务器应答探测，客户端采用结果。如果较旧的服务器不支持 `server/discover`，则客户端将回退到旧版 `initialize` 握手并使用在那里协商的协议版本。因此，安装 MCP Python SDK v2 不会强制每个连接使用最新的 MCP 协议版本。参见MCPPythonSDK的[协议版本协商指南](<https://py.sdk.modelcontextprotocol.io/protocol-versions/>)。

大多数应用程序应该让其依赖项解析器选择兼容版本。如果您的应用程序必须保留一个主要版本，请在 `openai-agents` 旁边添加显式约束：

```bash
# MCP Python SDK v1
pip install "mcp>=1.19.0,<2"

# MCP Python SDK v2
pip install "mcp>=2,<3"
```

HTTP 传输自定义必须使用已安装的 MCP 包拥有的 HTTP 堆栈：

|定制化| MCP Python SDK v1 | MCP Python SDK v2 |
| --- | --- | --- |
| `params["auth"]` | `httpx.Auth` | `httpx2.Auth` |
|`params["httpx_client_factory"]`返回值| `httpx.AsyncClient` | `httpx2.AsyncClient` |
| `MCPServerStreamableHttp` `params["ignore_initialized_notification_failure"] = True` |支持|不支持；连接前被拒绝|

尽可能使用 `Authorization` 标头，如下面的 Streamable HTTP 示例所示； `Authorization` 标头在两个软件包版本中均保持不变。当应用程序提供 `params["auth"]` 或 `params["httpx_client_factory"]` 时，这些值必须使用已安装的 `mcp` 软件包主要版本的 HTTP 类型。当应用程序设置`MCPServerStreamableHttp`的`params["ignore_initialized_notification_failure"] = True`时，应用程序在升级之前必须保留`mcp<2`或禁用该选项。

这些本地 `mcp` 依赖性要求不适用于 [`HostedMCPTool`][agents.tool.HostedMCPTool]，因为 OpenAI 响应 API 拥有远程 MCP 连接。

## Agent级MCP配置

除了选择传输之外，您还可以通过设置 `Agent.mcp_config` 来调整 MCP 工具的准备方式。

```python
from agents import Agent

agent = Agent(
    name="Assistant",
    mcp_servers=[server],
    mcp_config={
        # Try to convert MCP tool schemas to strict JSON schema.
        "convert_schemas_to_strict": True,
        # If None, MCP tool failures are raised as exceptions instead of
        # returning model-visible error text.
        "failure_error_function": None,
        # Prefix local MCP tool names with their server name.
        "include_server_in_tool_names": True,
    },
)
```

注意事项：

- `convert_schemas_to_strict` 已尽力而为。如果无法转换架构，则使用原始架构。
- `failure_error_function` 控制如何向模型显示 MCP 工具调用失败。
- 当`failure_error_function`未设置时，SDK使用默认的工具错误格式化程序。
- 服务器级 `failure_error_function` 覆盖该服务器的 `Agent.mcp_config["failure_error_function"]`。
- `include_server_in_tool_names` 已选择加入。启用后，每个本地 MCP 工具都会向具有确定性服务器前缀名称的模型公开，这有助于避免多个 MCP 服务器发布具有相同名称的工具时发生冲突。生成的名称是 ASCII- 安全的，保持在 `FunctionTool` 实例的名称长度限制内，并且不会与本地 `FunctionTool` 实例的配置名称或同一 agent 上启用的切换发生冲突。 SDK仍然调用原始服务器上的原始MCP工具名称。

## 跨传输的共享模式

选择传输方式后，大多数集成都需要相同的后续决策：

- 如何仅公开工具的子集（[工具过滤](<#工具过滤>)）。
- 服务器是否还提供可重用的提示（[Prompts](<#提示>)）。
- 是否应缓存`list_tools()`（[缓存](<#缓存>)）。
- MCP 活动如何显示在迹线中 ([ 跟踪 ](<#追踪>))。

对于本地 MCP 服务器（`MCPServerStdio`、`MCPServerSse`、`MCPServerStreamableHttp`），批准策略和每次调用 `_meta` 有效负载也是共享概念。 Streamable HTTP 部分显示了最完整的示例，相同的模式适用于其他本地传输。

## 1。托管MCP服务器工具

托管工具将整个工具往返推送到 OpenAI 的基础设施中。 [`HostedMCPTool`][agents.tool.HostedMCPTool] 将服务器标签（和可选的连接器元数据）转发到响应 API，而不是列出代码和调用工具。该模型列出了远程服务器的工具并调用它们，而无需额外回调您的 Python 进程。托管工具目前可与支持响应 API 的托管 MCP 集成的 OpenAI 模型配合使用。

### 基本托管 MCP 工具

通过将 [`HostedMCPTool`][agents.tool.HostedMCPTool] 添加到 agent 的 `tools` 列表来创建托管工具。 `tool_config`
dict 镜像您将发送到 REST API 的 JSON：

```python
import asyncio

from agents import Agent, HostedMCPTool, Runner


async def main() -> None:
    agent = Agent(
        name="Assistant",
        instructions="Use the DeepWiki hosted MCP server to inspect openai/openai-agents-python.",
        tools=[
            HostedMCPTool(
                tool_config={
                    "type": "mcp",
                    "server_label": "deepwiki",
                    "server_url": "https://mcp.deepwiki.com/mcp",
                    "require_approval": "never",
                }
            )
        ],
    )

    result = await Runner.run(
        agent,
        "Which language is the repository openai/openai-agents-python written in?",
    )
    print(result.final_output)


asyncio.run(main())
```

托管服务器自动公开其工具；您不将其添加到`mcp_servers`。

如果您希望托管工具搜索延迟加载托管MCP服务器，请设置`tool_config["defer_loading"] = True`并将[`ToolSearchTool`][agents.tool.ToolSearchTool]添加到agent。仅 OpenAI 响应模型支持此功能。有关完整的工具搜索设置和约束，请参阅[工具](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/tools.md#hosted-tool-search>)。

### 流式传输托管的 MCP 结果

托管工具支持流结果的方式与函数工具完全相同。当模型仍在工作时，使用 `Runner.run_streamed` 来 
 消耗增量 MCP 输出：

```python
result = Runner.run_streamed(agent, "Summarise this repository's top languages")
async for event in result.stream_events():
    if event.type == "run_item_stream_event":
        print(f"Received: {event.item}")
print(result.final_output)
```

### 可选的审批流程

如果服务器可以执行敏感操作，则在每个工具执行之前可能需要人工或程序批准。使用单个策略（`"always"`、`"never"`）或字典映射工具名称到策略来配置 `tool_config` 中的 `require_approval`。要在 Python 内做出决定，请提供 `on_approval_request` 回调。

```python
from agents import MCPToolApprovalFunctionResult, MCPToolApprovalRequest

SAFE_TOOLS = {"read_wiki_structure", "read_wiki_contents", "ask_question"}


def approve_tool(request: MCPToolApprovalRequest) -> MCPToolApprovalFunctionResult:
    if request.data.name in SAFE_TOOLS:
        return {"approve": True}
    return {"approve": False, "reason": "Escalate to a human reviewer"}


agent = Agent(
    name="Assistant",
    tools=[
        HostedMCPTool(
            tool_config={
                "type": "mcp",
                "server_label": "deepwiki",
                "server_url": "https://mcp.deepwiki.com/mcp",
                "require_approval": "always",
            },
            on_approval_request=approve_tool,
        )
    ],
)
```

回调可以是同步的，也可以是异步的，只要模型需要批准数据才能继续运行，就会调用该回调。

### 连接器支持的托管服务器

托管MCP还支持OpenAI连接器。不要指定 `server_url`，而是提供 `connector_id` 和访问令牌。响应 API 处理身份验证，托管服务器公开连接器的工具。

```python
import os

HostedMCPTool(
    tool_config={
        "type": "mcp",
        "server_label": "google_calendar",
        "connector_id": "connector_googlecalendar",
        "authorization": os.environ["GOOGLE_CALENDAR_AUTHORIZATION"],
        "require_approval": "never",
    }
)
```

完全运行的托管工具示例（包括流式传输、批准和连接器）位于 [`examples/hosted_mcp`](<https://github.com/openai/openai-agents-python/tree/main/examples/hosted_mcp>) 中。

## 2。可流式传输 HTTP MCP 服务器

当您想要自己管理网络连接时，请使用 [`MCPServerStreamableHttp`][agents.mcp.server.MCPServerStreamableHttp]。当您控制传输或希望在自己的基础设施内运行服务器同时保持低延迟时，流式 HTTP 服务器是理想的选择。

```python
import asyncio
import os

from agents import Agent, Runner
from agents.mcp import MCPServerStreamableHttp
from agents.model_settings import ModelSettings


async def main() -> None:
    token = os.environ["MCP_SERVER_TOKEN"]
    async with MCPServerStreamableHttp(
        name="Streamable HTTP Python Server",
        params={
            "url": "http://localhost:8000/mcp",
            "headers": {"Authorization": f"Bearer {token}"},
            "timeout": 10,
        },
        cache_tools_list=True,
        max_retry_attempts=3,
    ) as server:
        agent = Agent(
            name="Assistant",
            instructions="Use the MCP tools to answer the questions.",
            mcp_servers=[server],
            model_settings=ModelSettings(tool_choice="required"),
        )

        result = await Runner.run(agent, "Add 7 and 22.")
        print(result.final_output)


asyncio.run(main())
```

构造函数接受附加选项：

- `client_session_timeout_seconds` 控制 MCP ClientSession 读取超时。由`datetime.timedelta`表示的正有限值和至少一微秒设置有限超时； `None` 和 `0` 禁用它。其他值在服务器构建时会被拒绝。
- `use_structured_content` 切换 `tool_result.structured_content` 是否优先于文本输出。
- `max_retry_attempts`和`retry_backoff_seconds_base`为`list_tools()`和`call_tool()`添加自动重试。
- `tool_filter` 允许您仅公开工具的子集（请参阅[工具过滤](<#工具过滤>)）。
- `require_approval` 在本地 MCP 工具上启用人机交互审批策略。
- `failure_error_function`自定义模型可见的MCP工具失败消息；将其设置为 `None` 以引发错误。
- `tool_meta_resolver` 在 `call_tool()` 之前注入每次调用 MCP `_meta` 有效负载。

### 本地 MCP 服务器的批准策略

`MCPServerStdio`、`MCPServerSse` 和 `MCPServerStreamableHttp` 均接受 `require_approval`。

支持的表格：

- `"always"` 或 `"never"` 适用于所有工具。
- `True` 需要批准所有工具，`False` 不需要批准任何工具（分别相当于 `"always"` 和 `"never"`）。
- 每个工具的映射，例如 `{"delete_file": "always", "read_file": "never"}`。
- 分组对象：`{"always": {"tool_names": [...]}, "never": {"tool_names": [...]}}`。

```python
async with MCPServerStreamableHttp(
    name="Filesystem MCP",
    params={"url": "http://localhost:8000/mcp"},
    require_approval={"always": {"tool_names": ["delete_file"]}},
) as server:
    ...
```

有关完整的 pause/resume 流程，请参阅 [ 人机交互 ](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/human_in_the_loop.md>) 和 `examples/mcp/get_all_mcp_tools_example/main.py`。

### 每次调用元数据 `tool_meta_resolver`

当您的 MCP 服务器需要 `_meta` 中的请求元数据（例如，租户 ID 或跟踪上下文）时，请使用 `tool_meta_resolver`。下面的示例假设您将 `dict` 作为 `context` 传递给 `Runner.run(...)`。

```python
from agents.mcp import MCPServerStreamableHttp, MCPToolMetaContext


def resolve_meta(context: MCPToolMetaContext) -> dict[str, str] | None:
    run_context_data = context.run_context.context or {}
    tenant_id = run_context_data.get("tenant_id")
    if tenant_id is None:
        return None
    return {"tenant_id": str(tenant_id), "source": "agents-sdk"}


server = MCPServerStreamableHttp(
    name="Metadata-aware MCP",
    params={"url": "http://localhost:8000/mcp"},
    tool_meta_resolver=resolve_meta,
)
```

如果您的运行上下文是 Pydantic 模型、数据类或自定义类，请改为读取具有属性访问权限的租户 ID。

### MCP工具输出：文本、图像和其他内容

当 MCP 结果使用其内容块时，SDK 将文本内容作为文本输出转发，并将图像内容映射到工具输出中的图像类型条目。对于其他 MCP 内容块类型，包括音频和资源块，SDK 转发文本输出，其值是块的有效 JSON 序列化。包含多个内容块的响应将作为输出项列表转发。如果 `use_structured_content=True` 选择非空、非错误 `structuredContent` 有效负载，则该结构化有效负载优先于这些内容块。缺失或空的结构化内容会退回到内容块。

## 3。 HTTP 和 SSE MCP 服务器

！！！警告

MCP 项目已弃用服务器发送事件传输。对于新集成，首选 Streamable HTTP 或 stdio，并仅针对旧服务器保留 SSE。

如果 MCP 服务器通过 SSE 传输实现 HTTP，则实例化 [`MCPServerSse`][agents.mcp.server.MCPServerSse]。除了传输之外，API 与 Streamable HTTP 服务器相同。

```python
from agents import Agent, Runner
from agents.model_settings import ModelSettings
from agents.mcp import MCPServerSse

workspace_id = "demo-workspace"

async with MCPServerSse(
    name="SSE Python Server",
    params={
        "url": "http://localhost:8000/sse",
        "headers": {"X-Workspace": workspace_id},
    },
    cache_tools_list=True,
) as server:
    agent = Agent(
        name="Assistant",
        mcp_servers=[server],
        model_settings=ModelSettings(tool_choice="required"),
    )
    result = await Runner.run(agent, "What's the weather in Tokyo?")
    print(result.final_output)
```

## 4。 stdio MCP 服务器

对于作为本地子进程运行的 MCP 服务器，请使用 [`MCPServerStdio`][agents.mcp.server.MCPServerStdio]。 SDK 生成进程，保持管道打开，并在上下文管理器退出时自动关闭它们。此选项有助于快速证明概念或当服务器仅公开命令行入口点时。

```python
from pathlib import Path
from agents import Agent, Runner
from agents.mcp import MCPServerStdio

current_dir = Path(__file__).parent
samples_dir = current_dir / "sample_files"

async with MCPServerStdio(
    name="Filesystem Server via npx",
    params={
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", str(samples_dir)],
    },
) as server:
    agent = Agent(
        name="Assistant",
        instructions="Use the files in the sample directory to answer questions.",
        mcp_servers=[server],
    )
    result = await Runner.run(agent, "List the files available to you.")
    print(result.final_output)
```

## 5。 MCP服务器管理器

当您有多个 MCP 服务器时，请使用 `MCPServerManager` 预先连接它们，并将这些服务器的成功连接子集公开给您的 agents。有关构造函数选项和重新连接行为，请参阅 [MCPServerManager API 参考 ](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/ref/mcp/manager.md>)。

```python
from agents import Agent, Runner
from agents.mcp import MCPServerManager, MCPServerStreamableHttp

servers = [
    MCPServerStreamableHttp(name="calendar", params={"url": "http://localhost:8000/mcp"}),
    MCPServerStreamableHttp(name="docs", params={"url": "http://localhost:8001/mcp"}),
]

async with MCPServerManager(servers) as manager:
    agent = Agent(
        name="Assistant",
        instructions="Use MCP tools when they help.",
        mcp_servers=manager.active_servers,
    )
    result = await Runner.run(agent, "Which MCP tools are available?")
    print(result.final_output)
```

关键行为：

- `active_servers` 仅包含 `drop_failed_servers=True`（默认值）时成功连接的服务器。
- 如果输入迭代重复相同的服务器对象，则管理器拥有该服务器一次：`all_servers` 和 `active_servers` 包含一个条目，并且该服务器的连接和清理运行一次。
- 故障在 `failed_servers` 和 `errors` 中进行跟踪。
- 设置 `strict=True` 以在第一次连接失败时引发。
- 调用`reconnect(failed_only=True)`重试失败的服务器，或调用`reconnect(failed_only=False)`重新启动所有服务器。
- 对 `connect_all()`、`reconnect()` 和 `cleanup_all()` 的调用是序列化的。如果一个生命周期操作已在运行，则另一个生命周期操作会等待其完成，而不是同时连接或清理相同的服务器。
- 设置 `connect_timeout_seconds`、`cleanup_timeout_seconds` 和 `connect_in_parallel` 以调整生命周期行为。两个生命周期超时默认为 10 秒。它们接受正有限秒，或 `None` 来禁用它们，并在构造和分配期间进行验证；零被拒绝，因为它会立即产生最后期限。

## 通用服务器功能

以下部分适用于 MCP 服务器传输（具体 API 表面取决于服务器类别）。

## 工具过滤

每个 MCP 服务器都支持工具过滤器，以便您可以仅公开 agent 需要的功能。过滤可以在构造时发生，也可以在每次运行时动态发生。

### 静态工具过滤

使用 [`create_static_tool_filter`][agents.mcp.create_static_tool_filter] 配置简单的 allow/block 列表：

```python
from pathlib import Path

from agents.mcp import MCPServerStdio, create_static_tool_filter

samples_dir = Path("/path/to/files")

filesystem_server = MCPServerStdio(
    params={
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", str(samples_dir)],
    },
    tool_filter=create_static_tool_filter(allowed_tool_names=["read_file", "write_file"]),
)
```

当同时提供 `allowed_tool_names` 和 `blocked_tool_names` 时，SDK 首先应用允许列表，然后从剩余集中删除所有被阻止的工具。

### 动态工具过滤

对于更复杂的逻辑，传递一个接收 [`ToolFilterContext`][agents.mcp.ToolFilterContext] 的可调用函数。可调用可以是同步或异步的，并在应公开工具时返回 `True`。

```python
from pathlib import Path

from agents.mcp import MCPServerStdio, ToolFilterContext

samples_dir = Path("/path/to/files")


async def context_aware_filter(context: ToolFilterContext, tool) -> bool:
    if context.agent.name == "Code Reviewer" and tool.name.startswith("danger_"):
        return False
    return True


async with MCPServerStdio(
    params={
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", str(samples_dir)],
    },
    tool_filter=context_aware_filter,
) as server:
    ...
```

过滤器上下文公开活动的 `run_context`、请求工具的 `agent` 和 `server_name`。

## 工具护栏

本地 MCP 服务器类接受 `tool_input_guardrails` 和 `tool_output_guardrails`。 SDK 将这些服务器范围的护栏附加到过滤后剩余的每个 MCP 工具。输入护栏可以阻止 MCP 服务器调用并提供替换内容，而输出护栏在 SDK 将结果发送回模型之前检查转换后的 MCP 结果。这些护栏使用[工具护栏](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/guardrails.md#tool-guardrails>)中描述的相同功能工具执行管道、批准顺序、结果跟踪和绊线异常。

```python
import json

from agents import ToolGuardrailFunctionOutput
from agents.decorators import tool_input_guardrail
from agents.mcp import MCPServerStdio


@tool_input_guardrail
def block_secret_arguments(data):
    arguments = json.loads(data.context.tool_arguments or "{}")
    if "secret" in arguments:
        return ToolGuardrailFunctionOutput.reject_content(
            "Remove secrets before calling this MCP tool."
        )
    return ToolGuardrailFunctionOutput.allow()


filesystem_server = MCPServerStdio(
    params={
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", "."],
    },
    tool_input_guardrails=[block_secret_arguments],
)
```

此配置仅适用于本地 MCP 服务器对象公开的工具，例如 `MCPServerStdio`、`MCPServerSse` 和 `MCPServerStreamableHttp`。它不会向 [`HostedMCPTool`][agents.tool.HostedMCPTool] 添加客户端工具护栏，响应 API 将其作为托管工具执行。

## 提示

MCP服务器还可以提供动态生成agent指令的提示。支持提示的服务器公开两种
方法：

- `list_prompts()` 枚举可用的提示模板。
- `get_prompt(name, arguments)` 获取具体提示，可选地带有参数。

```python
from agents import Agent

prompt_result = await server.get_prompt(
    "generate_code_review_instructions",
    {"focus": "security vulnerabilities", "language": "python"},
)
instructions = prompt_result.messages[0].content.text

agent = Agent(
    name="Code Reviewer",
    instructions=instructions,
    mcp_servers=[server],
)
```

## 分页

列出工具和提示时，内置本地 MCP 服务器类自动遵循 `nextCursor`。 `list_tools()` 在应用过滤器或填充其缓存之前收集完整的工具列表，并且 `list_prompts()` 返回一个与 `nextCursor=None` 的组合结果。如果后面的页面失败或服务器重复 cursor，则该操作会引发错误，而不是公开或缓存部分结果。

资源仍显式分页。将 `list_resources()` 或 `list_resource_templates()` 中的 `nextCursor` 作为 `cursor` 参数传回以检索下一页。

## 缓存

每个 agent 运行都会调用每个 MCP 服务器上的 `list_tools()`。远程服务器可能会引入明显的延迟，因此所有 MCP 服务器类都会公开 `cache_tools_list` 选项。仅当您确信工具定义不会频繁更改时，才将其设置为 `True`。要稍后强制使用新列表，请在服务器实例上调用 `invalidate_tools_cache()`。

启用缓存后，每个 `list_tools()` 结果都包含缓存工具定义的独立副本，包括嵌套输入架构。动态工具过滤器回调还会检查分离的副本。因此，改变返回的工具或过滤器接收的工具不会更改服务器的缓存架构或以后的 `list_tools()` 结果。

## 追踪

[跟踪](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/tracing.md>)自动捕获MCP活动，包括：

1. 调用 MCP 服务器来列出工具。
2. MCP-工具调用的相关信息。

![MCP追踪截图](<https://raw.githubusercontent.com/openai/openai-agents-python/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/assets/images/mcp-tracing.jpg>)

## 进一步阅读

- [Model Context Protocol](<https://modelcontextprotocol.io/>) – 规格和设计指南。
- [examples/mcp](<https://github.com/openai/openai-agents-python/tree/main/examples/mcp>) – 可运行的 stdio、SSE 和可流式传输的 HTTP 示例。
- [examples/hosted_mcp](<https://github.com/openai/openai-agents-python/tree/main/examples/hosted_mcp>) – 完整托管的 MCP 演示，包括批准和连接器。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Model context protocol (MCP)

The [Model context protocol](<https://modelcontextprotocol.io/introduction>) (MCP) standardises how applications expose tools and
context to language models. From the official documentation:

> MCP is an open protocol that standardizes how applications provide context to LLMs. Think of MCP like a USB-C port for AI
> applications. Just as USB-C provides a standardized way to connect your devices to various peripherals and accessories, MCP
> provides a standardized way to connect AI models to different data sources and tools.

The Agents Python SDK understands multiple MCP transports. This lets you reuse existing MCP servers or build your own to expose filesystem, HTTP, or connector backed tools to an agent.

!!! warning "Trust MCP servers before connecting"

    MCP tools can expose data from the model context and perform actions with the credentials you provide. Connect only to servers you trust, use least-privilege credentials, keep access tokens in authorization fields or headers rather than URLs, and require approval for sensitive operations. See the [OpenAI MCP security guidance](<https://developers.openai.com/api/docs/guides/tools-connectors-mcp#risks-and-safety>).

## Choosing an MCP integration

Before wiring an MCP server into an agent decide where the tool calls should execute and which transports you can reach. The matrix below summarises the options that the Python SDK supports.

| What you need                                                                        | Recommended option                                    |
| ------------------------------------------------------------------------------------ | ----------------------------------------------------- |
| Let OpenAI's Responses API call a publicly reachable MCP server on the model's behalf| **Hosted MCP server tools** via [`HostedMCPTool`][agents.tool.HostedMCPTool] |
| Connect to Streamable HTTP servers that you run locally or remotely                  | **Streamable HTTP MCP servers** via [`MCPServerStreamableHttp`][agents.mcp.server.MCPServerStreamableHttp] |
| Talk to servers that implement HTTP with Server-Sent Events                          | **HTTP with SSE MCP servers** via [`MCPServerSse`][agents.mcp.server.MCPServerSse] |
| Launch a local process and communicate over stdin/stdout                             | **stdio MCP servers** via [`MCPServerStdio`][agents.mcp.server.MCPServerStdio] |

The sections below walk through each option, how to configure it, and when to prefer one transport over another.

For a remote MCP server, obtain the endpoint URL and authentication requirements from the server provider's documentation. You can also find published servers in the [official MCP Registry](<https://registry.modelcontextprotocol.io/>). Configure the SDK with the provider's MCP endpoint URL, which may differ from the provider's website URL.

## MCP Python SDK v1 and v2

The Agents SDK supports both major versions of the `mcp` Python package through the dependency range `mcp>=1.19.0,<3`. The installed `mcp` package version is separate from the MCP protocol version negotiated with a server. The Agents SDK detects the installed package major version and adapts stdio, SSE, and Streamable HTTP connections automatically, so ordinary server configuration does not need a version switch.

When MCP Python SDK v2 is installed, the Agents SDK creates the v2 `mcp.Client` with `mode="auto"` around the configured local transport. The client first sends a `server/discover` probe at the newest protocol version supported by the installed MCP SDK. A modern server answers the probe, and the client adopts the result. If an older server does not support `server/discover`, the client falls back to the legacy `initialize` handshake and uses the protocol version negotiated there. Installing MCP Python SDK v2 therefore does not force every connection to use the newest MCP protocol version. See the MCP Python SDK's [protocol version negotiation guide](<https://py.sdk.modelcontextprotocol.io/protocol-versions/>).

Most applications should let their dependency resolver select a compatible version. If your application must stay on one major version, add an explicit constraint alongside `openai-agents`:

```bash
# MCP Python SDK v1
pip install "mcp>=1.19.0,<2"

# MCP Python SDK v2
pip install "mcp>=2,<3"
```

HTTP transport customization must use the HTTP stack owned by the installed MCP package:

| Customization | MCP Python SDK v1 | MCP Python SDK v2 |
| --- | --- | --- |
| `params["auth"]` | `httpx.Auth` | `httpx2.Auth` |
| `params["httpx_client_factory"]` return value | `httpx.AsyncClient` | `httpx2.AsyncClient` |
| `MCPServerStreamableHttp` `params["ignore_initialized_notification_failure"] = True` | Supported | Not supported; rejected before connecting |

Use an `Authorization` header when possible, as shown in the Streamable HTTP example below; an `Authorization` header works unchanged with both package versions. When an application supplies `params["auth"]` or `params["httpx_client_factory"]`, those values must use the HTTP types for the installed `mcp` package major version. When an application sets `MCPServerStreamableHttp`'s `params["ignore_initialized_notification_failure"] = True`, the application must keep `mcp<2` or disable the option before upgrading.

These local `mcp` dependency requirements do not apply to [`HostedMCPTool`][agents.tool.HostedMCPTool] because the OpenAI Responses API owns the remote MCP connection.

## Agent-level MCP configuration

In addition to choosing a transport, you can tune how MCP tools are prepared by setting `Agent.mcp_config`.

```python
from agents import Agent

agent = Agent(
    name="Assistant",
    mcp_servers=[server],
    mcp_config={
        # Try to convert MCP tool schemas to strict JSON schema.
        "convert_schemas_to_strict": True,
        # If None, MCP tool failures are raised as exceptions instead of
        # returning model-visible error text.
        "failure_error_function": None,
        # Prefix local MCP tool names with their server name.
        "include_server_in_tool_names": True,
    },
)
```

Notes:

- `convert_schemas_to_strict` is best-effort. If a schema cannot be converted, the original schema is used.
- `failure_error_function` controls how MCP tool call failures are surfaced to the model.
- When `failure_error_function` is unset, the SDK uses the default tool error formatter.
- Server-level `failure_error_function` overrides `Agent.mcp_config["failure_error_function"]` for that server.
- `include_server_in_tool_names` is opt-in. When enabled, each local MCP tool is exposed to the model with a deterministic server-prefixed name, which helps avoid collisions when multiple MCP servers publish tools with the same name. Generated names are ASCII-safe, stay within the name-length limit for `FunctionTool` instances, and do not collide with the configured names of local `FunctionTool` instances or enabled handoffs on the same agent. The SDK still invokes the original MCP tool name on the original server.

## Shared patterns across transports

After you choose a transport, most integrations need the same follow-up decisions:

- How to expose only a subset of tools ([Tool filtering](<#tool-filtering>)).
- Whether the server also provides reusable prompts ([Prompts](<#prompts>)).
- Whether `list_tools()` should be cached ([Caching](<#caching>)).
- How MCP activity appears in traces ([Tracing](<#tracing>)).

For local MCP servers (`MCPServerStdio`, `MCPServerSse`, `MCPServerStreamableHttp`), approval policies and per-call `_meta` payloads are also shared concepts. The Streamable HTTP section shows the most complete examples, and the same patterns apply to the other local transports.

## 1. Hosted MCP server tools

Hosted tools push the entire tool round-trip into OpenAI's infrastructure. Instead of your code listing and calling tools, the [`HostedMCPTool`][agents.tool.HostedMCPTool] forwards a server label (and optional connector metadata) to the Responses API. The model lists the remote server's tools and invokes them without an extra callback to your Python process. Hosted tools currently work with OpenAI models that support the Responses API's hosted MCP integration.

### Basic hosted MCP tool

Create a hosted tool by adding a [`HostedMCPTool`][agents.tool.HostedMCPTool] to the agent's `tools` list. The `tool_config`
dict mirrors the JSON you would send to the REST API:

```python
import asyncio

from agents import Agent, HostedMCPTool, Runner


async def main() -> None:
    agent = Agent(
        name="Assistant",
        instructions="Use the DeepWiki hosted MCP server to inspect openai/openai-agents-python.",
        tools=[
            HostedMCPTool(
                tool_config={
                    "type": "mcp",
                    "server_label": "deepwiki",
                    "server_url": "https://mcp.deepwiki.com/mcp",
                    "require_approval": "never",
                }
            )
        ],
    )

    result = await Runner.run(
        agent,
        "Which language is the repository openai/openai-agents-python written in?",
    )
    print(result.final_output)


asyncio.run(main())
```

The hosted server exposes its tools automatically; you do not add it to `mcp_servers`.

If you want hosted tool search to load a hosted MCP server lazily, set `tool_config["defer_loading"] = True` and add [`ToolSearchTool`][agents.tool.ToolSearchTool] to the agent. This is supported only on OpenAI Responses models. See [Tools](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/tools.md#hosted-tool-search>) for the complete tool-search setup and constraints.

### Streaming hosted MCP results

Hosted tools support streaming results in exactly the same way as function tools. Use `Runner.run_streamed` to
consume incremental MCP output while the model is still working:

```python
result = Runner.run_streamed(agent, "Summarise this repository's top languages")
async for event in result.stream_events():
    if event.type == "run_item_stream_event":
        print(f"Received: {event.item}")
print(result.final_output)
```

### Optional approval flows

If a server can perform sensitive operations you can require human or programmatic approval before each tool execution. Configure `require_approval` in the `tool_config` with either a single policy (`"always"`, `"never"`) or a dict mapping tool names to policies. To make the decision inside Python, provide an `on_approval_request` callback.

```python
from agents import MCPToolApprovalFunctionResult, MCPToolApprovalRequest

SAFE_TOOLS = {"read_wiki_structure", "read_wiki_contents", "ask_question"}


def approve_tool(request: MCPToolApprovalRequest) -> MCPToolApprovalFunctionResult:
    if request.data.name in SAFE_TOOLS:
        return {"approve": True}
    return {"approve": False, "reason": "Escalate to a human reviewer"}


agent = Agent(
    name="Assistant",
    tools=[
        HostedMCPTool(
            tool_config={
                "type": "mcp",
                "server_label": "deepwiki",
                "server_url": "https://mcp.deepwiki.com/mcp",
                "require_approval": "always",
            },
            on_approval_request=approve_tool,
        )
    ],
)
```

The callback can be synchronous or asynchronous and is invoked whenever the model needs approval data to keep running.

### Connector-backed hosted servers

Hosted MCP also supports OpenAI connectors. Instead of specifying a `server_url`, supply a `connector_id` and an access token. The Responses API handles authentication and the hosted server exposes the connector's tools.

```python
import os

HostedMCPTool(
    tool_config={
        "type": "mcp",
        "server_label": "google_calendar",
        "connector_id": "connector_googlecalendar",
        "authorization": os.environ["GOOGLE_CALENDAR_AUTHORIZATION"],
        "require_approval": "never",
    }
)
```

Fully working hosted tool samples—including streaming, approvals, and connectors—live in [`examples/hosted_mcp`](<https://github.com/openai/openai-agents-python/tree/main/examples/hosted_mcp>).

## 2. Streamable HTTP MCP servers

When you want to manage the network connection yourself, use [`MCPServerStreamableHttp`][agents.mcp.server.MCPServerStreamableHttp]. Streamable HTTP servers are ideal when you control the transport or want to run the server inside your own infrastructure while keeping latency low.

```python
import asyncio
import os

from agents import Agent, Runner
from agents.mcp import MCPServerStreamableHttp
from agents.model_settings import ModelSettings


async def main() -> None:
    token = os.environ["MCP_SERVER_TOKEN"]
    async with MCPServerStreamableHttp(
        name="Streamable HTTP Python Server",
        params={
            "url": "http://localhost:8000/mcp",
            "headers": {"Authorization": f"Bearer {token}"},
            "timeout": 10,
        },
        cache_tools_list=True,
        max_retry_attempts=3,
    ) as server:
        agent = Agent(
            name="Assistant",
            instructions="Use the MCP tools to answer the questions.",
            mcp_servers=[server],
            model_settings=ModelSettings(tool_choice="required"),
        )

        result = await Runner.run(agent, "Add 7 and 22.")
        print(result.final_output)


asyncio.run(main())
```

The constructor accepts additional options:

- `client_session_timeout_seconds` controls MCP ClientSession read timeouts. Positive finite values representable by `datetime.timedelta` and at least one microsecond set a finite timeout; `None` and `0` disable it. Other values are rejected when the server is constructed.
- `use_structured_content` toggles whether `tool_result.structured_content` is preferred over textual output.
- `max_retry_attempts` and `retry_backoff_seconds_base` add automatic retries for `list_tools()` and `call_tool()`.
- `tool_filter` lets you expose only a subset of tools (see [Tool filtering](<#tool-filtering>)).
- `require_approval` enables human-in-the-loop approval policies on local MCP tools.
- `failure_error_function` customizes model-visible MCP tool failure messages; set it to `None` to raise errors instead.
- `tool_meta_resolver` injects per-call MCP `_meta` payloads before `call_tool()`.

### Approval policies for local MCP servers

`MCPServerStdio`, `MCPServerSse`, and `MCPServerStreamableHttp` all accept `require_approval`.

Supported forms:

- `"always"` or `"never"` for all tools.
- `True` requires approval for all tools, and `False` requires approval for none (equivalent to `"always"` and `"never"`, respectively).
- A per-tool map, for example `{"delete_file": "always", "read_file": "never"}`.
- A grouped object: `{"always": {"tool_names": [...]}, "never": {"tool_names": [...]}}`.

```python
async with MCPServerStreamableHttp(
    name="Filesystem MCP",
    params={"url": "http://localhost:8000/mcp"},
    require_approval={"always": {"tool_names": ["delete_file"]}},
) as server:
    ...
```

For a full pause/resume flow, see [Human-in-the-loop](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/human_in_the_loop.md>) and `examples/mcp/get_all_mcp_tools_example/main.py`.

### Per-call metadata with `tool_meta_resolver`

Use `tool_meta_resolver` when your MCP server expects request metadata in `_meta` (for example, tenant IDs or trace context). The example below assumes you pass a `dict` as `context` to `Runner.run(...)`.

```python
from agents.mcp import MCPServerStreamableHttp, MCPToolMetaContext


def resolve_meta(context: MCPToolMetaContext) -> dict[str, str] | None:
    run_context_data = context.run_context.context or {}
    tenant_id = run_context_data.get("tenant_id")
    if tenant_id is None:
        return None
    return {"tenant_id": str(tenant_id), "source": "agents-sdk"}


server = MCPServerStreamableHttp(
    name="Metadata-aware MCP",
    params={"url": "http://localhost:8000/mcp"},
    tool_meta_resolver=resolve_meta,
)
```

If your run context is a Pydantic model, dataclass, or custom class, read the tenant ID with attribute access instead.

### MCP tool outputs: text, images, and other content

When an MCP result uses its content blocks, the SDK forwards text content as text output and maps image content to image-type entries in the tool output. For other MCP content block types, including audio and resource blocks, the SDK forwards a text output whose value is the block's valid JSON serialization. Responses that contain multiple content blocks are forwarded as a list of output items. If `use_structured_content=True` selects a non-empty, non-error `structuredContent` payload, that structured payload takes precedence over these content blocks. Missing or empty structured content falls back to the content blocks.

## 3. HTTP with SSE MCP servers

!!! warning

    The MCP project has deprecated the Server-Sent Events transport. Prefer Streamable HTTP or stdio for new integrations and keep SSE only for legacy servers.

If the MCP server implements the HTTP with SSE transport, instantiate [`MCPServerSse`][agents.mcp.server.MCPServerSse]. Apart from the transport, the API is identical to the Streamable HTTP server.

```python
from agents import Agent, Runner
from agents.model_settings import ModelSettings
from agents.mcp import MCPServerSse

workspace_id = "demo-workspace"

async with MCPServerSse(
    name="SSE Python Server",
    params={
        "url": "http://localhost:8000/sse",
        "headers": {"X-Workspace": workspace_id},
    },
    cache_tools_list=True,
) as server:
    agent = Agent(
        name="Assistant",
        mcp_servers=[server],
        model_settings=ModelSettings(tool_choice="required"),
    )
    result = await Runner.run(agent, "What's the weather in Tokyo?")
    print(result.final_output)
```

## 4. stdio MCP servers

For MCP servers that run as local subprocesses, use [`MCPServerStdio`][agents.mcp.server.MCPServerStdio]. The SDK spawns the process, keeps the pipes open, and closes them automatically when the context manager exits. This option is helpful for quick proofs of concept or when the server only exposes a command line entry point.

```python
from pathlib import Path
from agents import Agent, Runner
from agents.mcp import MCPServerStdio

current_dir = Path(__file__).parent
samples_dir = current_dir / "sample_files"

async with MCPServerStdio(
    name="Filesystem Server via npx",
    params={
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", str(samples_dir)],
    },
) as server:
    agent = Agent(
        name="Assistant",
        instructions="Use the files in the sample directory to answer questions.",
        mcp_servers=[server],
    )
    result = await Runner.run(agent, "List the files available to you.")
    print(result.final_output)
```

## 5. MCP server manager

When you have multiple MCP servers, use `MCPServerManager` to connect them up front and expose the successfully connected subset of those servers to your agents. See the [MCPServerManager API reference](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/ref/mcp/manager.md>) for constructor options and reconnect behavior.

```python
from agents import Agent, Runner
from agents.mcp import MCPServerManager, MCPServerStreamableHttp

servers = [
    MCPServerStreamableHttp(name="calendar", params={"url": "http://localhost:8000/mcp"}),
    MCPServerStreamableHttp(name="docs", params={"url": "http://localhost:8001/mcp"}),
]

async with MCPServerManager(servers) as manager:
    agent = Agent(
        name="Assistant",
        instructions="Use MCP tools when they help.",
        mcp_servers=manager.active_servers,
    )
    result = await Runner.run(agent, "Which MCP tools are available?")
    print(result.final_output)
```

Key behaviors:

- `active_servers` includes only successfully connected servers when `drop_failed_servers=True` (the default).
- If the input iterable repeats the same server object, the manager owns that server once: `all_servers` and `active_servers` contain one entry, and connection and cleanup run once for that server.
- Failures are tracked in `failed_servers` and `errors`.
- Set `strict=True` to raise on the first connection failure.
- Call `reconnect(failed_only=True)` to retry failed servers, or `reconnect(failed_only=False)` to restart all servers.
- Calls to `connect_all()`, `reconnect()`, and `cleanup_all()` are serialized. If one lifecycle operation is already running, another lifecycle operation waits for it to finish instead of connecting or cleaning up the same servers concurrently.
- Set `connect_timeout_seconds`, `cleanup_timeout_seconds`, and `connect_in_parallel` to tune lifecycle behavior. Both lifecycle timeouts default to 10 seconds. They accept positive finite seconds, or `None` to disable them, and are validated both during construction and assignment; zero is rejected because it would create an immediate deadline.

## Common server capabilities

The sections below apply across MCP server transports (with the exact API surface depending on the server class).

## Tool filtering

Each MCP server supports tool filters so that you can expose only the functions that your agent needs. Filtering can happen at construction time or dynamically per run.

### Static tool filtering

Use [`create_static_tool_filter`][agents.mcp.create_static_tool_filter] to configure simple allow/block lists:

```python
from pathlib import Path

from agents.mcp import MCPServerStdio, create_static_tool_filter

samples_dir = Path("/path/to/files")

filesystem_server = MCPServerStdio(
    params={
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", str(samples_dir)],
    },
    tool_filter=create_static_tool_filter(allowed_tool_names=["read_file", "write_file"]),
)
```

When both `allowed_tool_names` and `blocked_tool_names` are supplied the SDK applies the allow-list first and then removes any blocked tools from the remaining set.

### Dynamic tool filtering

For more elaborate logic pass a callable that receives a [`ToolFilterContext`][agents.mcp.ToolFilterContext]. The callable can be synchronous or asynchronous and returns `True` when the tool should be exposed.

```python
from pathlib import Path

from agents.mcp import MCPServerStdio, ToolFilterContext

samples_dir = Path("/path/to/files")


async def context_aware_filter(context: ToolFilterContext, tool) -> bool:
    if context.agent.name == "Code Reviewer" and tool.name.startswith("danger_"):
        return False
    return True


async with MCPServerStdio(
    params={
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", str(samples_dir)],
    },
    tool_filter=context_aware_filter,
) as server:
    ...
```

The filter context exposes the active `run_context`, the `agent` requesting the tools, and the `server_name`.

## Tool guardrails

Local MCP server classes accept `tool_input_guardrails` and `tool_output_guardrails`. The SDK attaches these server-wide guardrails to every MCP tool that remains after filtering. Input guardrails can prevent the MCP server call and supply replacement content, while output guardrails inspect the converted MCP result before the SDK sends that result back to the model. These guardrails use the same function-tool execution pipeline, approval ordering, result tracking, and tripwire exceptions described in [Tool guardrails](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/guardrails.md#tool-guardrails>).

```python
import json

from agents import ToolGuardrailFunctionOutput
from agents.decorators import tool_input_guardrail
from agents.mcp import MCPServerStdio


@tool_input_guardrail
def block_secret_arguments(data):
    arguments = json.loads(data.context.tool_arguments or "{}")
    if "secret" in arguments:
        return ToolGuardrailFunctionOutput.reject_content(
            "Remove secrets before calling this MCP tool."
        )
    return ToolGuardrailFunctionOutput.allow()


filesystem_server = MCPServerStdio(
    params={
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", "."],
    },
    tool_input_guardrails=[block_secret_arguments],
)
```

This configuration applies only to tools exposed by local MCP server objects such as `MCPServerStdio`, `MCPServerSse`, and `MCPServerStreamableHttp`. It does not add client-side tool guardrails to [`HostedMCPTool`][agents.tool.HostedMCPTool], which the Responses API executes as a hosted tool.

## Prompts

MCP servers can also provide prompts that dynamically generate agent instructions. Servers that support prompts expose two
methods:

- `list_prompts()` enumerates the available prompt templates.
- `get_prompt(name, arguments)` fetches a concrete prompt, optionally with parameters.

```python
from agents import Agent

prompt_result = await server.get_prompt(
    "generate_code_review_instructions",
    {"focus": "security vulnerabilities", "language": "python"},
)
instructions = prompt_result.messages[0].content.text

agent = Agent(
    name="Code Reviewer",
    instructions=instructions,
    mcp_servers=[server],
)
```

## Pagination

The built-in local MCP server classes automatically follow `nextCursor` when listing tools and prompts. `list_tools()` collects the complete tool list before applying filters or populating its cache, and `list_prompts()` returns one combined result with `nextCursor=None`. If a later page fails or a server repeats a cursor, the operation raises an error instead of exposing or caching partial results.

Resources remain explicitly paginated. Pass the `nextCursor` from `list_resources()` or `list_resource_templates()` back as the `cursor` argument to retrieve the next page.

## Caching

Every agent run calls `list_tools()` on each MCP server. Remote servers can introduce noticeable latency, so all of the MCP server classes expose a `cache_tools_list` option. Set it to `True` only if you are confident that the tool definitions do not change frequently. To force a fresh list later, call `invalidate_tools_cache()` on the server instance.

When caching is enabled, each `list_tools()` result contains detached copies of the cached tool definitions, including nested input schemas. Dynamic tool-filter callbacks also inspect detached copies. Mutating a returned tool or a tool received by a filter therefore does not change the server's cached schema or later `list_tools()` results.

## Tracing

[Tracing](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/tracing.md>) automatically captures MCP activity, including:

1. Calls to the MCP server to list tools.
2. MCP-related information on tool calls.

![MCP Tracing Screenshot](<https://raw.githubusercontent.com/openai/openai-agents-python/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/assets/images/mcp-tracing.jpg>)

## Further reading

- [Model Context Protocol](<https://modelcontextprotocol.io/>) – the specification and design guides.
- [examples/mcp](<https://github.com/openai/openai-agents-python/tree/main/examples/mcp>) – runnable stdio, SSE, and Streamable HTTP samples.
- [examples/hosted_mcp](<https://github.com/openai/openai-agents-python/tree/main/examples/hosted_mcp>) – complete hosted MCP demonstrations including approvals and connectors.


</details>
