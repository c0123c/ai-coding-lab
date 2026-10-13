# Qwen Code Python SDK 指南

介绍实验性接口、同步与异步调用、配置、权限、多轮会话、恢复和错误处理，包含真实运行测试入口。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-08-22](<https://github.com/QwenLM/qwen-code/commit/867ded5bd5cd6af690980fb64e8f004d6f53e456>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/developers/sdk-python.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/developers/sdk-python.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Python SDK

## `qwen-code-sdk`

`qwen-code-sdk` 是 Qwen Code 的实验性 Python SDK。 v1 针对 
 现有 `stream-json` CLI 协议，并保持传输表面较小且 
 可测试。

## 适用范围

- 包装名称：`qwen-code-sdk`
- 导入路径：`qwen_code_sdk`
- 运行时要求：Python `>=3.10`
- CLI 依赖项：v1 中需要外部 `qwen` 可执行文件
- 运输范围：仅限过程运输
- 不包含在 v1 中：ACP 传输、SDK- 嵌入式 MCP 服务器

## 安装

```bash
pip install qwen-code-sdk
```

对于预览版：

```bash
pip install --pre qwen-code-sdk
```

如果 `qwen` 不在 `PATH` 上，则显式传递 `path_to_qwen_executable`。

在编写 SDK 代码之前，请确保 CLI 在同一个 shell 中工作：

```bash
qwen --version
```

## 快速入门

```python
import asyncio

from qwen_code_sdk import (
    is_sdk_assistant_message,
    is_sdk_result_message,
    query,
)


def extract_text(message):
    content = message.get("message", {}).get("content", [])
    if not isinstance(content, list):
        return repr(content)
    texts = [
        block.get("text", "")
        for block in content
        if isinstance(block, dict) and block.get("type") == "text"
    ]
    return "".join(texts) if texts else "[no text content]"


def print_result(message):
    if message.get("is_error"):
        error = message.get("error") or {}
        print(f"Error: {error.get('message', 'Unknown error')}")
        return
    print(message.get("result", ""))


async def main() -> None:
    async with query(
        "Explain the repository structure.",
        {
            "cwd": "/path/to/project",
            "path_to_qwen_executable": "qwen",
        },
    ) as result:
        async for message in result:
            if is_sdk_assistant_message(message):
                print(extract_text(message))
            elif is_sdk_result_message(message):
                print_result(message)


asyncio.run(main())
```

`asyncio.run()` 适用于独立脚本。如果您的应用程序
已经运行事件循环，例如Jupyter，FastAPI或pytest-asyncio，请改为调用
`await main()`。

## 同步使用情况

当您的主机应用程序不是异步时，请使用 `query_sync`：

```python
from qwen_code_sdk import is_sdk_result_message, query_sync


with query_sync(
    "Summarize this repository in one paragraph.",
    {
        "cwd": "/path/to/project",
        "path_to_qwen_executable": "qwen",
    },
) as result:
    for message in result:
        if is_sdk_result_message(message):
            if message.get("is_error"):
                error = message.get("error") or {}
                print(f"Error: {error.get('message', 'Unknown error')}")
            else:
                print(message.get("result", ""))
```

## API 表面

### 顶级入口点

- `query(prompt, options=None) -> Query`
- `query_sync(prompt, options=None) -> SyncQuery`

`prompt` 支持：

- `str` 用于单轮请求
- `AsyncIterable[SDKUserMessage]` 用于多轮流

### `Query`

- 可通过 SDK 消息进行异步迭代
- `close()`
- `interrupt()`
- `set_model(model)`
- `set_permission_mode(mode)`
- `supported_commands()`
- `mcp_server_status()`
- `get_session_id()`
- `is_closed()`

### `QueryOptions`

|选项|类型/值|描述|
| -------------------------- | ---------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cwd`                      | `str`                                                      |CLI 进程的工作目录。|
| `model`                    | `str`                                                      |此 SDK 会话的模型覆盖。|
| `path_to_qwen_executable`  | `str`                                                      |`qwen`、显式二进制路径或 `.js` CLI 捆绑包。|
| `permission_mode`          | `default`, `plan`, `auto-edit`, `auto`, `yolo`             |工具执行审批模式。 `auto` 让 LLM 分类器批准工具调用； `yolo`自动批准所有工具；仅在受信任或沙盒环境中使用它。|
| `can_use_tool`             |异步回调|工具请求的自定义权限回调。|
| `env`                      | `dict[str, str]`                                           |传递给 CLI 进程的额外环境变量。|
| `system_prompt`            | `str`                                                      |覆盖系统提示。|
| `append_system_prompt`     | `str`                                                      |将额外的说明附加到系统提示符中。|
| `debug`                    | `bool`                                                     |当不存在 `stderr` 挂钩时，将 CLI stderr 转发到 stderr。|
| `max_session_turns`        | `int`                                                      |CLI 结束会话之前的最大轮数。|
| `core_tools`               | `list[str]`                                                |限制可用的工具集。|
| `exclude_tools`            | `list[str]`                                                |排除匹配工具。|
| `allowed_tools`            | `list[str]`                                                |允许在没有回调批准的情况下匹配工具。|
| `auth_type`                | `openai`, `anthropic`, `qwen-oauth`, `gemini`, `vertex-ai` |身份验证模式传递给CLI。|
| `include_partial_messages` | `bool`                                                     |发出部分辅助流事件。|
| `resume`                   |UUID 字符串|恢复已知的会话 ID。|
| `continue_session`         | `bool`                                                     |Continue最新的CLI会话。|
| `session_id`               |UUID 字符串|使用已知 ID 启动或关联会话。|
| `timeout`                  |映射|超时以秒为单位。|
| `stderr`                   |可调用的|接收 CLI stderr 行。|

在请求中仅使用 `resume`、`continue_session` 或 `session_id` 之一。如果这些会话选项组合在一起，
SDK 会引发 `ValidationError`。

v1 中不支持：

- `mcp_servers`

### 通用配置

```python
options = {
    "cwd": "/path/to/project",
    "path_to_qwen_executable": "qwen",
    "model": "qwen-plus",
    "permission_mode": "plan",
    "max_session_turns": 1,
    "env": {
        "OPENAI_MODEL": "qwen-plus",
    },
    "timeout": {
        "control_request": 60,
        "can_use_tool": 60,
        "stream_close": 60,
    },
}
```

超时值为秒。 `env` 合并在父进程 
 环境之上，因此您只需传递与此 SDK
 会话不同的变量。在父环境中设置诸如 `OPENAI_API_KEY` 的机密或 
 机密管理器，而不是在源中将它们硬编码。

## 权限处理

当CLI发出`can_use_tool`控制请求时，SDK将其路由到
`can_use_tool(tool_name, tool_input, context)`。

- 默认行为：拒绝
- 默认超时：60秒，可通过`timeout.can_use_tool`进行配置
- 超时后备：拒绝
- 回调异常：转换为拒绝并带有错误消息
- 回调上下文：`cancel_event`、`suggestions` 和 `blocked_path`
- 回调合约：`can_use_tool` 必须与 3 位置参数异步；
`stderr` 必须接受 1 位置字符串参数

示例：

```python
import asyncio
from pathlib import Path

from qwen_code_sdk import is_sdk_result_message, query

PROJECT_ROOT = Path("/path/to/project").resolve()


def project_path(tool_name, tool_input):
    key = "path" if tool_name == "list_directory" else "file_path"
    raw_path = tool_input.get(key)
    if not isinstance(raw_path, str) or not raw_path:
        return None

    resolved = (PROJECT_ROOT / raw_path).resolve()
    try:
        resolved.relative_to(PROJECT_ROOT)
    except ValueError:
        return None
    return resolved


async def can_use_tool(tool_name, tool_input, context):
    if tool_name in {"read_file", "list_directory", "write_file"}:
        resolved = project_path(tool_name, tool_input)
        if resolved is None:
            return {
                "behavior": "deny",
                "message": "Only project-local paths are allowed",
            }

        if tool_name == "write_file" and resolved.suffix != ".md":
            return {"behavior": "deny", "message": "Only .md files can be written"}

        return {"behavior": "allow", "updatedInput": tool_input}

    return {
        "behavior": "deny",
        "message": f"{tool_name} is not allowed by this application",
    }


async def main():
    async with query(
        "Update README.md with a short summary.",
        {
            "cwd": str(PROJECT_ROOT),
            "path_to_qwen_executable": "qwen",
            "can_use_tool": can_use_tool,
        },
    ) as result:
        async for message in result:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))


asyncio.run(main())
```

如果你没有通过 `can_use_tool`, 的 SDK 拒绝许可请求
默认。

## 多轮会议

对于多轮会话，传递 `SDKUserMessage` 对象的异步迭代：

```python
import asyncio

from qwen_code_sdk import SDKUserMessage, is_sdk_result_message, query

SESSION_ID = "123e4567-e89b-12d3-a456-426614174000"


async def prompts():
    first: SDKUserMessage = {
        "type": "user",
        "session_id": SESSION_ID,
        "message": {
            "role": "user",
            "content": "Create a concise project summary.",
        },
        "parent_tool_use_id": None,
    }
    yield first

    second: SDKUserMessage = {
        "type": "user",
        "session_id": SESSION_ID,
        "message": {
            "role": "user",
            "content": "Also list the test files.",
        },
        "parent_tool_use_id": None,
    }
    yield second


async def main():
    async with query(
        prompts(),
        {
            "cwd": "/path/to/project",
            "path_to_qwen_executable": "qwen",
            "session_id": SESSION_ID,
        },
    ) as result:
        async for message in result:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))


asyncio.run(main())
```

异步迭代中的所有消息都必须预先已知。 SDK 将它们 
 按顺序发送到 CLI，但无法将先前的响应反馈回生成器。
如果您需要对话轮流，请将每个轮次作为单独的 `query()`
 呼叫进行管理。

## 运行时控制

返回的`Query`对象可以控制正在运行的CLI进程：

```python
import asyncio

from qwen_code_sdk import is_sdk_result_message, query


async def main():
    async with query(
        "Inspect this repository and explain the test layout.",
        {
            "cwd": "/path/to/project",
            "path_to_qwen_executable": "qwen",
        },
    ) as result:
        commands = await result.supported_commands()
        print(commands)

        await result.set_permission_mode("plan")
        await result.set_model("qwen-plus")

        async for message in result:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))


asyncio.run(main())
```

使用 `interrupt()` 取消当前操作，使用 `close()` 清理 
 底层进程，使用 `get_session_id()` 保留会话 ID 以供以后使用。

## 会议恢复

```python
import asyncio

from qwen_code_sdk import is_sdk_result_message, query


async def main():
    # Resume a known session by its id.
    async with query(
        "Continue from this session.",
        {
            "path_to_qwen_executable": "qwen",
            "resume": "123e4567-e89b-12d3-a456-426614174000",
        },
    ) as known:
        async for message in known:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))


asyncio.run(main())
```

要继续最新的会话：

```python
import asyncio

from qwen_code_sdk import is_sdk_result_message, query


async def main():
    async with query(
        "Continue the latest session.",
        {
            "path_to_qwen_executable": "qwen",
            "continue_session": True,
        },
    ) as latest:
        async for message in latest:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))


asyncio.run(main())
```

当您的应用程序存储会话 ID 时，`resume` 非常有用。 `continue_session`
将最新会话的选择委托给CLI。

## 误差模型

- `ValidationError`：无效选项、无效 UUID、不支持的组合
- `ControlRequestTimeoutError`：初始化、中断或其他控制请求
超时
- `ProcessExitError`：CLI 退出非零
- `AbortError`：控制请求或会话被取消

```python
from qwen_code_sdk import (
    ProcessExitError,
    ValidationError,
    is_sdk_result_message,
    query_sync,
)

try:
    with query_sync("Say hello", {"path_to_qwen_executable": "qwen"}) as result:
        for message in result:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))
except ValidationError as exc:
    print(f"Invalid SDK options: {exc}")
except ProcessExitError as exc:
    print(f"qwen exited with {exc.exit_code}: {exc}")
```

## 故障排除

如果【SDK】无法启动【CLI】：

- 验证 `qwen --version` 在目标环境中工作
- 如果您的 shell 使用 `nvm`、`pyenv` 或其他，则传递 `path_to_qwen_executable`
非标准 PATH 设置
- 调试时使用 `debug=True` 或 `stderr=print` 来显示 CLI stderr

如果会话控制调用超时：

- 检查目标`qwen`版本是否支持`--input-format stream-json`
- 增加`timeout.control_request`
- 验证没有包装器脚本正在吞咽 stdout/stderr

## 存储库集成

存储库级帮助程序命令：

- `npm run test:sdk:python`
- `npm run lint:sdk:python`
- `npm run typecheck:sdk:python`
- `npm run smoke:sdk:python -- --qwen qwen`

## 真实E2E烟雾

对于真正的运行时检查（实际 `qwen` 进程 + 真实模型调用），请从 
 存储库根运行。 npm 帮助器使用 `python3`，因此请确保它解析为 
Python `>=3.10` 解释器：

```bash
npm run smoke:sdk:python -- --qwen qwen
```

该脚本运行：

- 异步单轮查询
- 异步控制流（`supported_commands`，权限模式更新）
- 同步`query_sync`查询

它打印 JSON 并在失败时返回非零。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Python SDK

## `qwen-code-sdk`

`qwen-code-sdk` is an experimental Python SDK for Qwen Code. v1 targets the
existing `stream-json` CLI protocol and keeps the transport surface small and
testable.

## Scope

- Package name: `qwen-code-sdk`
- Import path: `qwen_code_sdk`
- Runtime requirement: Python `>=3.10`
- CLI dependency: external `qwen` executable is required in v1
- Transport scope: process transport only
- Not included in v1: ACP transport, SDK-embedded MCP servers

## Install

```bash
pip install qwen-code-sdk
```

For preview releases:

```bash
pip install --pre qwen-code-sdk
```

If `qwen` is not on `PATH`, pass `path_to_qwen_executable` explicitly.

Before writing SDK code, make sure the CLI works in the same shell:

```bash
qwen --version
```

## Quick Start

```python
import asyncio

from qwen_code_sdk import (
    is_sdk_assistant_message,
    is_sdk_result_message,
    query,
)


def extract_text(message):
    content = message.get("message", {}).get("content", [])
    if not isinstance(content, list):
        return repr(content)
    texts = [
        block.get("text", "")
        for block in content
        if isinstance(block, dict) and block.get("type") == "text"
    ]
    return "".join(texts) if texts else "[no text content]"


def print_result(message):
    if message.get("is_error"):
        error = message.get("error") or {}
        print(f"Error: {error.get('message', 'Unknown error')}")
        return
    print(message.get("result", ""))


async def main() -> None:
    async with query(
        "Explain the repository structure.",
        {
            "cwd": "/path/to/project",
            "path_to_qwen_executable": "qwen",
        },
    ) as result:
        async for message in result:
            if is_sdk_assistant_message(message):
                print(extract_text(message))
            elif is_sdk_result_message(message):
                print_result(message)


asyncio.run(main())
```

`asyncio.run()` is appropriate for standalone scripts. If your application
already runs an event loop, such as Jupyter, FastAPI, or pytest-asyncio, call
`await main()` instead.

## Sync Usage

Use `query_sync` when your host application is not async:

```python
from qwen_code_sdk import is_sdk_result_message, query_sync


with query_sync(
    "Summarize this repository in one paragraph.",
    {
        "cwd": "/path/to/project",
        "path_to_qwen_executable": "qwen",
    },
) as result:
    for message in result:
        if is_sdk_result_message(message):
            if message.get("is_error"):
                error = message.get("error") or {}
                print(f"Error: {error.get('message', 'Unknown error')}")
            else:
                print(message.get("result", ""))
```

## API Surface

### Top-level entry points

- `query(prompt, options=None) -> Query`
- `query_sync(prompt, options=None) -> SyncQuery`

`prompt` supports either:

- `str` for single-turn requests
- `AsyncIterable[SDKUserMessage]` for multi-turn streams

### `Query`

- Async iterable over SDK messages
- `close()`
- `interrupt()`
- `set_model(model)`
- `set_permission_mode(mode)`
- `supported_commands()`
- `mcp_server_status()`
- `get_session_id()`
- `is_closed()`

### `QueryOptions`

| Option                     | Type / values                                              | Description                                                                                                                                                       |
| -------------------------- | ---------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cwd`                      | `str`                                                      | Working directory for the CLI process.                                                                                                                            |
| `model`                    | `str`                                                      | Model override for this SDK session.                                                                                                                              |
| `path_to_qwen_executable`  | `str`                                                      | `qwen`, an explicit binary path, or a `.js` CLI bundle.                                                                                                           |
| `permission_mode`          | `default`, `plan`, `auto-edit`, `auto`, `yolo`             | Tool execution approval mode. `auto` lets an LLM classifier approve tool calls; `yolo` auto-approves all tools; use it only in trusted or sandboxed environments. |
| `can_use_tool`             | async callback                                             | Custom permission callback for tool requests.                                                                                                                     |
| `env`                      | `dict[str, str]`                                           | Extra environment variables passed to the CLI process.                                                                                                            |
| `system_prompt`            | `str`                                                      | Override the system prompt.                                                                                                                                       |
| `append_system_prompt`     | `str`                                                      | Append extra instructions to the system prompt.                                                                                                                   |
| `debug`                    | `bool`                                                     | Forward CLI stderr to stderr when no `stderr` hook exists.                                                                                                        |
| `max_session_turns`        | `int`                                                      | Maximum turns before the CLI ends the session.                                                                                                                    |
| `core_tools`               | `list[str]`                                                | Restrict the available tool set.                                                                                                                                  |
| `exclude_tools`            | `list[str]`                                                | Exclude matching tools.                                                                                                                                           |
| `allowed_tools`            | `list[str]`                                                | Allow matching tools without callback approval.                                                                                                                   |
| `auth_type`                | `openai`, `anthropic`, `qwen-oauth`, `gemini`, `vertex-ai` | Authentication mode passed to the CLI.                                                                                                                            |
| `include_partial_messages` | `bool`                                                     | Emit partial assistant stream events.                                                                                                                             |
| `resume`                   | UUID string                                                | Resume a known session id.                                                                                                                                        |
| `continue_session`         | `bool`                                                     | Continue the latest CLI session.                                                                                                                                  |
| `session_id`               | UUID string                                                | Start or correlate a session with a known id.                                                                                                                     |
| `timeout`                  | mapping                                                    | Timeouts in seconds.                                                                                                                                              |
| `stderr`                   | callable                                                   | Receives CLI stderr lines.                                                                                                                                        |

Use only one of `resume`, `continue_session`, or `session_id` in a request. The
SDK raises `ValidationError` if these session options are combined.

Unsupported in v1:

- `mcp_servers`

### Common Configuration

```python
options = {
    "cwd": "/path/to/project",
    "path_to_qwen_executable": "qwen",
    "model": "qwen-plus",
    "permission_mode": "plan",
    "max_session_turns": 1,
    "env": {
        "OPENAI_MODEL": "qwen-plus",
    },
    "timeout": {
        "control_request": 60,
        "can_use_tool": 60,
        "stream_close": 60,
    },
}
```

Timeout values are seconds. `env` is merged on top of the parent process
environment, so you only need to pass variables that should differ for this SDK
session. Set secrets such as `OPENAI_API_KEY` in the parent environment or a
secrets manager rather than hardcoding them in source.

## Permission Handling

When the CLI emits a `can_use_tool` control request, the SDK routes it through
`can_use_tool(tool_name, tool_input, context)`.

- Default behavior: deny
- Default timeout: 60 seconds, configurable with `timeout.can_use_tool`
- Timeout fallback: deny
- Callback exceptions: converted to deny with an error message
- Callback context: `cancel_event`, `suggestions`, and `blocked_path`
- Callback contract: `can_use_tool` must be async with 3 positional arguments;
  `stderr` must accept 1 positional string argument

Example:

```python
import asyncio
from pathlib import Path

from qwen_code_sdk import is_sdk_result_message, query

PROJECT_ROOT = Path("/path/to/project").resolve()


def project_path(tool_name, tool_input):
    key = "path" if tool_name == "list_directory" else "file_path"
    raw_path = tool_input.get(key)
    if not isinstance(raw_path, str) or not raw_path:
        return None

    resolved = (PROJECT_ROOT / raw_path).resolve()
    try:
        resolved.relative_to(PROJECT_ROOT)
    except ValueError:
        return None
    return resolved


async def can_use_tool(tool_name, tool_input, context):
    if tool_name in {"read_file", "list_directory", "write_file"}:
        resolved = project_path(tool_name, tool_input)
        if resolved is None:
            return {
                "behavior": "deny",
                "message": "Only project-local paths are allowed",
            }

        if tool_name == "write_file" and resolved.suffix != ".md":
            return {"behavior": "deny", "message": "Only .md files can be written"}

        return {"behavior": "allow", "updatedInput": tool_input}

    return {
        "behavior": "deny",
        "message": f"{tool_name} is not allowed by this application",
    }


async def main():
    async with query(
        "Update README.md with a short summary.",
        {
            "cwd": str(PROJECT_ROOT),
            "path_to_qwen_executable": "qwen",
            "can_use_tool": can_use_tool,
        },
    ) as result:
        async for message in result:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))


asyncio.run(main())
```

If you do not pass `can_use_tool`, the SDK denies permission requests by
default.

## Multi-Turn Sessions

For multi-turn sessions, pass an async iterable of `SDKUserMessage` objects:

```python
import asyncio

from qwen_code_sdk import SDKUserMessage, is_sdk_result_message, query

SESSION_ID = "123e4567-e89b-12d3-a456-426614174000"


async def prompts():
    first: SDKUserMessage = {
        "type": "user",
        "session_id": SESSION_ID,
        "message": {
            "role": "user",
            "content": "Create a concise project summary.",
        },
        "parent_tool_use_id": None,
    }
    yield first

    second: SDKUserMessage = {
        "type": "user",
        "session_id": SESSION_ID,
        "message": {
            "role": "user",
            "content": "Also list the test files.",
        },
        "parent_tool_use_id": None,
    }
    yield second


async def main():
    async with query(
        prompts(),
        {
            "cwd": "/path/to/project",
            "path_to_qwen_executable": "qwen",
            "session_id": SESSION_ID,
        },
    ) as result:
        async for message in result:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))


asyncio.run(main())
```

All messages in the async iterable must be known upfront. The SDK sends them
sequentially to the CLI but cannot feed a prior response back into the generator.
If you need conversational turn-taking, manage each turn as a separate `query()`
call.

## Runtime Controls

The returned `Query` object can control the running CLI process:

```python
import asyncio

from qwen_code_sdk import is_sdk_result_message, query


async def main():
    async with query(
        "Inspect this repository and explain the test layout.",
        {
            "cwd": "/path/to/project",
            "path_to_qwen_executable": "qwen",
        },
    ) as result:
        commands = await result.supported_commands()
        print(commands)

        await result.set_permission_mode("plan")
        await result.set_model("qwen-plus")

        async for message in result:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))


asyncio.run(main())
```

Use `interrupt()` to cancel the current operation, `close()` to clean up the
underlying process, and `get_session_id()` to persist a session id for later.

## Session Resume

```python
import asyncio

from qwen_code_sdk import is_sdk_result_message, query


async def main():
    # Resume a known session by its id.
    async with query(
        "Continue from this session.",
        {
            "path_to_qwen_executable": "qwen",
            "resume": "123e4567-e89b-12d3-a456-426614174000",
        },
    ) as known:
        async for message in known:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))


asyncio.run(main())
```

To continue the latest session instead:

```python
import asyncio

from qwen_code_sdk import is_sdk_result_message, query


async def main():
    async with query(
        "Continue the latest session.",
        {
            "path_to_qwen_executable": "qwen",
            "continue_session": True,
        },
    ) as latest:
        async for message in latest:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))


asyncio.run(main())
```

`resume` is useful when your application stores session ids. `continue_session`
delegates the selection of the latest session to the CLI.

## Error Model

- `ValidationError`: invalid options, invalid UUIDs, unsupported combinations
- `ControlRequestTimeoutError`: initialize, interrupt, or other control request
  timed out
- `ProcessExitError`: CLI exited non-zero
- `AbortError`: control request or session was cancelled

```python
from qwen_code_sdk import (
    ProcessExitError,
    ValidationError,
    is_sdk_result_message,
    query_sync,
)

try:
    with query_sync("Say hello", {"path_to_qwen_executable": "qwen"}) as result:
        for message in result:
            if is_sdk_result_message(message):
                if message.get("is_error"):
                    error = message.get("error") or {}
                    print(f"Error: {error.get('message', 'Unknown error')}")
                else:
                    print(message.get("result", ""))
except ValidationError as exc:
    print(f"Invalid SDK options: {exc}")
except ProcessExitError as exc:
    print(f"qwen exited with {exc.exit_code}: {exc}")
```

## Troubleshooting

If the SDK cannot start the CLI:

- Verify `qwen --version` works in the target environment
- Pass `path_to_qwen_executable` if your shell uses `nvm`, `pyenv`, or other
  non-standard PATH setup
- Use `debug=True` or `stderr=print` to surface CLI stderr while debugging

If session control calls time out:

- Check that the target `qwen` version supports `--input-format stream-json`
- Increase `timeout.control_request`
- Verify that no wrapper script is swallowing stdout/stderr

## Repository Integration

Repository-level helper commands:

- `npm run test:sdk:python`
- `npm run lint:sdk:python`
- `npm run typecheck:sdk:python`
- `npm run smoke:sdk:python -- --qwen qwen`

## Real E2E Smoke

For a real runtime check (actual `qwen` process + real model call), run from
the repository root. The npm helper uses `python3`, so ensure it resolves to a
Python `>=3.10` interpreter:

```bash
npm run smoke:sdk:python -- --qwen qwen
```

This script runs:

- async single-turn query
- async control flow (`supported_commands`, permission mode updates)
- sync `query_sync` query

It prints JSON and returns non-zero on failure.


</details>
