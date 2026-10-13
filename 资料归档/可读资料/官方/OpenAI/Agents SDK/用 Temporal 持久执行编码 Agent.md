# 用 Temporal 持久执行编码 Agent

介绍以持久工作流运行对话式编码 Agent、选择沙箱后端、终端操作及本地冒烟测试。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-07-04](<https://github.com/openai/openai-agents-python/commit/a898061dfa72415e97620880a9eae005e390c92c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/temporal/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/用 Temporal 持久执行编码 Agent.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 时态沙箱 Agent

对话式编码 agent，作为持久的临时工作流程运行，支持多个沙箱后端（Daytona、Docker、E2B、本地 unix）。

## 快速入门

**先决条件：** Docker（用于 Docker 后端）和 API 用于您要使用的任何云后端的密钥。本地和 Docker 沙箱无需任何云提供商 API 密钥即可工作。

## 局部烟雾测试

如果您只想确认临时工作流程在本地运行，请首先使用最小的
示例：

```
export OPENAI_API_KEY="sk-..."
# Optional: export EXAMPLES_TEMPORAL_MODEL="gpt-5.4-mini"
# Optional: export EXAMPLES_TEMPORAL_TRACE="openai"
uv run --extra temporal python -m examples.sandbox.extensions.temporal.local_hello_workflow
```

这将启动临时 Python SDK 测试服务器，运行一个工作流和一个模型活动，将工作流连接到本地 Unix 沙箱，然后关闭。它不需要临时 CLI、已运行的临时开发服务器或沙箱后端凭据。

本地冒烟测试默认启用 OpenAI Agents 跟踪。设置 `EXAMPLES_TEMPORAL_TRACE=none` 以禁用跟踪，或设置 `EXAMPLES_TEMPORAL_TRACE=openai_with_temporal_spans` 以要求临时插件添加临时跨度。时间跨度模式取决于时间插件行为，并且可能会省略某些插件版本的常规 Agents 跨度；当您需要标准 OpenAI 跟踪范围时，请使用默认的 `openai` 模式。

1. 如果您还没有安装 [，只需安装 ](<https://just.systems/man/en/packages.html>) 和 [临时 CLI](<https://docs.temporal.io/cli/setup-cli#install-the-cli>)。

2. 切换到示例目录：

   ```
   cd examples/sandbox/extensions/temporal
   ```

3. 使用您的 API 密钥在此目录中创建一个 `.env` 文件：

   ```
   OPENAI_API_KEY="sk-..."
   DAYTONA_API_KEY="dtn_..."   # optional, for Daytona backend
   E2B_API_KEY="e2b_..."       # optional, for E2B backend
   ```

4. 启动临时开发服务器：

   ```
   just temporal
   ```

5. 在第二个终端中，启动工作程序：

   ```
   just worker
   ```

6. 在第三个终端中，启动 TUI：

   ```
   just tui
   ```

`just worker` 和 `just tui` 命令在启动前自动安装依赖项。

## TUI命令

|命令|描述|
|--------------------|--------------------------------------------------------|
| `/switch`          |将当前会话切换到不同的沙箱后端|
| `/fork [title]`    |将会话分叉到（可能不同的）后端|
| `/title <name>`    |重命名当前会话|
| `/done`            |退出TUI|

`/switch` 和 `/fork` 都会打开一个交互式后端选择器。切换到本地后端时可以指定工作空间根目录。

## 它是如何运作的

单个 Temporal Worker 通过 `SandboxClientProvider` 注册所有沙箱后端，因此每个后端的活动都可在一个任务队列上使用。工作流程通过在其 `RunConfig` 中调用 `temporal_sandbox_client(name)` 来选择每个回合的目标后端。

**文件：**

- `temporal_sandbox_agent.py` -- `AgentWorkflow` 定义和工作入口点。每个对话轮流调用 `Runner.run()` 以及针对活动后端的 `SandboxRunConfig`。工作流程是
寿命长：它在回合之间闲置，并在时间中无限期地持续存在。
- `temporal_session_manager.py` —— 一个单例 `SessionManagerWorkflow`，用于跟踪活动会话并处理创建、分叉、切换和销毁操作。
- `temporal_sandbox_tui.py` - 连接到会话管理器并通过信号、更新和查询驱动对话的 [ 文本 ](<https://textual.textualize.io/>) TUI。
- `examples/sandbox/misc/workspace_shell.py` -- 一个共享的 `Capability`，它为 agent 提供了一个用于在沙箱工作区中运行命令的 shell 工具。

**切换后端**是一项就地操作：工作流接收`switch_backend`更新，更改其后端和清单，清除后端特定的会话状态，然后下一轮在新后端上创建一个新会话。便携式快照会被保留，因此工作区文件会继续存在。

**Forking** 暂停源工作流程，快照其状态和对话历史记录，并在所选后端启动新的子工作流程。分叉获得工作区和对话的独立副本。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Temporal Sandbox Agent

A conversational coding agent that runs as a durable Temporal workflow with support for multiple sandbox backends (Daytona, Docker, E2B, local unix).

## Quickstart

**Prerequisites:** Docker (for the Docker backend) and API keys for any cloud backends you want to use. The local and Docker sandboxes work without any cloud provider API keys.

## Local smoke test

If you only want to confirm that Temporal workflows run locally, use the minimal
example first:

```
export OPENAI_API_KEY="sk-..."
# Optional: export EXAMPLES_TEMPORAL_MODEL="gpt-5.4-mini"
# Optional: export EXAMPLES_TEMPORAL_TRACE="openai"
uv run --extra temporal python -m examples.sandbox.extensions.temporal.local_hello_workflow
```

This starts the Temporal Python SDK test server, runs one workflow and one model activity, connects the workflow to a local Unix sandbox, and then shuts down. It does not require the Temporal CLI, an already running Temporal dev server, or sandbox backend credentials.

The local smoke test enables OpenAI Agents tracing by default. Set `EXAMPLES_TEMPORAL_TRACE=none` to disable tracing, or `EXAMPLES_TEMPORAL_TRACE=openai_with_temporal_spans` to also ask the Temporal plugin to add Temporal spans. The Temporal span mode depends on Temporal plugin behavior and may omit regular Agents spans with some plugin versions; use the default `openai` mode when you want standard OpenAI trace spans.

1. Install [just](<https://just.systems/man/en/packages.html>) and the [Temporal CLI](<https://docs.temporal.io/cli/setup-cli#install-the-cli>) if you don't have them already.

2. Change into the example directory:

   ```
   cd examples/sandbox/extensions/temporal
   ```

3. Create a `.env` file in this directory with your API keys:

   ```
   OPENAI_API_KEY="sk-..."
   DAYTONA_API_KEY="dtn_..."   # optional, for Daytona backend
   E2B_API_KEY="e2b_..."       # optional, for E2B backend
   ```

4. Start the Temporal dev server:

   ```
   just temporal
   ```

5. In a second terminal, start the worker:

   ```
   just worker
   ```

6. In a third terminal, start the TUI:

   ```
   just tui
   ```

The `just worker` and `just tui` commands automatically install dependencies before starting.

## TUI commands

| Command            | Description                                            |
|--------------------|--------------------------------------------------------|
| `/switch`          | Switch the current session to a different sandbox backend |
| `/fork [title]`    | Fork the session onto a (possibly different) backend   |
| `/title <name>`    | Rename the current session                             |
| `/done`            | Exit the TUI                                           |

Both `/switch` and `/fork` open an interactive backend picker. When switching to the local backend you can specify the workspace root directory.

## How it works

A single Temporal worker registers all sandbox backends via `SandboxClientProvider`, so every backend's activities are available on one task queue. The workflow picks which backend to target each turn by calling `temporal_sandbox_client(name)` in its `RunConfig`.

**Files:**

- `temporal_sandbox_agent.py` -- The `AgentWorkflow` definition and worker entrypoint. Each conversation turn calls `Runner.run()` with a `SandboxRunConfig` that targets the active backend. The workflow is
  long-lived: it idles between turns and persists indefinitely in Temporal.
- `temporal_session_manager.py` -- A singleton `SessionManagerWorkflow` that tracks active sessions and handles create, fork, switch, and destroy operations.
- `temporal_sandbox_tui.py` -- A [Textual](<https://textual.textualize.io/>) TUI that connects to the session manager and drives conversations via signals, updates, and queries.
- `examples/sandbox/misc/workspace_shell.py` -- A shared `Capability` that gives the agent a shell tool for running commands in the sandbox workspace.

**Switching backends** is an in-place operation: the workflow receives a `switch_backend` update, changes its backend and manifest, clears the backend-specific session state, and the next turn creates a fresh session on the new backend. The portable snapshot is preserved so workspace files carry over.

**Forking** pauses the source workflow, snapshots its state and conversation history, and starts a new child workflow on the chosen backend. The fork gets an independent copy of the workspace and conversation.


</details>
