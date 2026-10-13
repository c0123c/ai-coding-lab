# VS Code 本地 Hook 配置参考

列出本地执行框架的事件、输入输出、退出码和控制方式；与由其他提供方执行的 Hook 分开说明。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/hooks-reference.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/hooks-reference.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code 本地 Hook 配置参考.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 1f6b2d94-7c3a-4e85-9a1d-5b8c0e2f7a63
DateApproved: 10/7/2026
MetaDescription: Look up Local harness hook configuration and event schemas in {% data variables.product.prodname_vscode %}.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- copilot
- ai
- agents
- hooks
- reference
- preToolUse
- postToolUse
- lifecycle
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 本地钩子参考

本文是 {% data variables.product.prodname_vscode_shortname %} 中 **Local** 线束中挂钩的配置和事件架构参考。本地线束在扩展主机中运行，并使用此处记录的 PascalCase 事件名称和有效负载。

对于由提供程序工具执行的挂钩，请使用相应的提供程序文档：

* 对于 Agent 主机上的 **Copilot** 会话，请使用 [GitHub Copilot 挂钩参考 ](<https://docs.github.com/en/copilot/reference/hooks-reference>)。
* 对于 **Claude** 会话，请使用 [Claude 挂钩参考 ](<https://code.claude.com/docs/en/hooks>)。
* 对于 **Codex** 会话，请使用 [Codex 挂钩文档 ](<https://developers.openai.com/codex/hooks/>)。

如果“本地”是选定的会话目标，则即使挂钩文件使用 Copilot 或 Claude 兼容格式，也请使用此引用。本地解析器映射配置，然后发送此处记录的本地有效负载。

如需选择运行时、创建挂钩或迁移现有挂钩的帮助，请参阅[配置agent挂钩](<https://github.com/docs/agent-customization/hooks.md>)。

## 配置格式

本地挂钩文件包含 `hooks` 对象。每个属性都是受支持的事件名称，其值是命令条目数组：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "type": "command",
        "command": "./scripts/validate-tool.sh",
        "windows": "powershell -File scripts\\validate-tool.ps1",
        "timeout": 15
      }
    ]
  }
}
```

### 命令属性

在本地本地格式中，每个命令条目必须具有 `type: "command"` 和至少一个命令属性：

|财产|类型|描述|
|----------|------|-------------|
| `type` |字符串|必须是`"command"`。|
| `command` |字符串|默认跨平台命令。|
| `windows` |字符串|Windows 特定的命令覆盖。|
| `linux` |字符串|Linux 特定的命令覆盖。|
| `osx` |字符串|macOS特定命令覆盖。|
| `cwd` |字符串|工作目录，相对于存储库根目录。|
| `env` |对象|其他环境变量。|
| `timeout` |数量|超时（以秒为单位）。默认值为 30 秒。|

本地线束从扩展主机平台选择操作系统覆盖。如果没有为该平台定义覆盖，它将使用 `command`。 Copilot 和 Claude 源格式在本地解析器将它们映射到此格式之前使用不同的属性名称和默认值。

## 公共输入

每个本地挂钩都会在标准输入 (stdin) 上接收一个 JSON 对象。除了事件字段之外，该对象还包含以下公共字段：

|领域|类型|描述|
|-------|------|-------------|
| `timestamp` |字符串|ISO 8601 钩子触发时的时间戳。|
| `cwd` |字符串|agent 执行的可选工作目录。|
| `session_id` |字符串|当前 agent 会话的可选标识符。|
| `hook_event_name` |字符串|挂钩事件名称，例如`PreToolUse`。|
| `transcript_path` |字符串|包含对话记录的文件的可选绝对路径。|

> [！NOTE]
> 转录文件格式不是稳定的挂钩 API，并且可能在 {% data variables.product.prodname_vscode_shortname %} 版本之间发生变化。尽可能使用记录的事件字段，例如 `tool_name`、`tool_input` 或 `prompt`。

## 公共输出

本地挂钩可以将 JSON 对象写入标准输出 (stdout)。所有事件都支持这些顶级字段：

```json
{
  "continue": false,
  "stopReason": "Security policy violation",
  "systemMessage": "Review the hook result."
}
```

|领域|类型|描述|
|-------|------|-------------|
| `continue` |布尔值|处理是否继续。默认值为 `true`。设置为`false`可停止agent执行。|
| `stopReason` |字符串|当 `continue` 为 `false` 时向用户显示的原因。|
| `systemMessage` |字符串|向用户显示警告消息。|

事件还可以支持 `decision` 或 `hookSpecificOutput` 等字段。事件部分描述了这些字段。

### 退出代码

hook 命令的退出代码控制本地线束如何处理其结果：

|退出代码|行为|
|-----------|----------|
| `0` |将命令视为成功并处理标准输出。|
| `2` |将 stderr 视为阻塞错误并将其提供给模型。|
|任何其他值|向用户显示非阻塞警告并继续处理。|

### 选择控制机制

使用满足要求的干扰最小的输出：

* 使用退出代码 `2` 阻止当前操作并向模型提供 stderr。
* 将 `continue: false` 与 `stopReason` 一起使用可停止整个 agent 执行。
* 使用 `hookSpecificOutput` 进行特定于事件的控制，例如拒绝一个工具调用或添加上下文。
* 使用 `systemMessage` 显示警告而不更改事件决策。

当输出发生冲突时，限制性最强的结果优先。例如，即使 `PreToolUse` 输出也允许工具调用，`continue: false` 也会停止 agent 执行。

## `PreToolUse`

`PreToolUse` 挂钩在本地 agent 调用工具之前触发。

### 输入

除了 [ 公共输入 ](<#公共输入>) 之外，钩子还接收：

```json
{
  "tool_name": "<local-tool-name>",
  "tool_input": {},
  "tool_use_id": "tool-123"
}
```

|领域|类型|描述|
|-------|------|-------------|
| `tool_name` |字符串|本地工具的名称。|
| `tool_input` |对象|工具调用的参数。|
| `tool_use_id` |字符串|工具调用的标识符。|

工具名称和输入模式因线束而异。在过滤或修改工具调用之前，打开 [agent 调试日志 ](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md#agent-debug-logs-panel>) 以检查本地工具架构。

### 输出

使用`hookSpecificOutput`控制工具调用：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "deny",
    "permissionDecisionReason": "Destructive command blocked by policy.",
    "additionalContext": "Production files are read-only."
  }
}
```

|领域|值或类型|描述|
|-------|----------------|-------------|
| `hookEventName` | `"PreToolUse"` |标识特定于事件的输出。|
| `permissionDecision` |`"allow"`、`"deny"` 或 `"ask"`|允许工具调用、拒绝工具调用或需要用户确认。|
| `permissionDecisionReason` |字符串|向用户显示的原因。|
| `updatedInput` |对象|可选的替换工具输入。该值必须与本地工具架构匹配。|
| `additionalContext` |字符串|模型的附加上下文。|

当多个挂钩返回同一工具调用的权限决策时，限制性最强的决策获胜：

1. `deny` 阻止工具调用。
1. `ask`需要用户确认。
1. `allow` 批准工具调用。

如果 `updatedInput` 与本地工具架构不匹配，本地线束将忽略它。

## `PostToolUse`

本地工具成功完成后，将触发 `PostToolUse` 挂钩。

### 输入

除了 [ 公共输入 ](<#公共输入>) 之外，钩子还接收：

```json
{
  "tool_name": "<local-tool-name>",
  "tool_input": {},
  "tool_use_id": "tool-123",
  "tool_response": "<tool-result>"
}
```

|领域|类型|描述|
|-------|------|-------------|
| `tool_name` |字符串|本地工具的名称。|
| `tool_input` |对象|工具调用的参数。|
| `tool_use_id` |字符串|工具调用的标识符。|
| `tool_response` |字符串或对象|工具返回的结果。|

### 输出

该钩子可以添加上下文或阻止进一步处理：

```json
{
  "decision": "block",
  "reason": "Post-processing validation failed.",
  "hookSpecificOutput": {
    "hookEventName": "PostToolUse",
    "additionalContext": "The edited file has lint errors."
  }
}
```

|领域|值或类型|描述|
|-------|----------------|-------------|
| `decision` | `"block"` |阻止进一步处理的可选决定。|
| `reason` |字符串|当处理被阻止时向模型提供的原因。|
| `hookSpecificOutput.hookEventName` | `"PostToolUse"` |标识特定于事件的输出。|
| `hookSpecificOutput.additionalContext` |字符串|模型的附加上下文。|

## `UserPromptSubmit`

当用户提交提示时，`UserPromptSubmit` 挂钩会触发。

### 输入

除了 [ 公共输入 ](<#公共输入>) 之外，钩子还接收：

```json
{
  "prompt": "Add input validation to the sign-up form."
}
```

|领域|类型|描述|
|-------|------|-------------|
| `prompt` |字符串|用户提交的文本。|

该钩子支持[公共输出](<#公共输出>)。

## `SessionStart`

当第一个提示启动本地 agent 会话时，`SessionStart` 挂钩将触发。

### 输入

除了 [ 公共输入 ](<#公共输入>) 之外，钩子还接收：

```json
{
  "source": "new"
}
```

|领域|类型|描述|
|-------|------|-------------|
| `source` |字符串|会议如何开始。目前始终为`"new"`。|

### 输出

该钩子可以为对话添加上下文：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "SessionStart",
    "additionalContext": "Project: my-app 2.1.0 | Branch: main"
  }
}
```

|领域|值或类型|描述|
|-------|----------------|-------------|
| `hookEventName` | `"SessionStart"` |标识特定于事件的输出。|
| `additionalContext` |字符串|上下文已添加到对话中。|

## `Stop`

当当前本地 agent 执行即将停止时，`Stop` 钩子会触发。该事件并不表示会话已结束或变为非活动状态。

当自定义 agent 作为子代理运行时，其 `Stop` 挂钩将被视为 `SubagentStop`。

### 输入

除了 [ 公共输入 ](<#公共输入>) 之外，钩子还接收：

```json
{
  "stop_hook_active": false
}
```

|领域|类型|描述|
|-------|------|-------------|
| `stop_hook_active` |布尔值|`true` 当 agent 已经继续时，因为之前的 `Stop` 钩子阻止了它。|

### 输出

该钩子可能需要 agent 才能继续：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "Stop",
    "decision": "block",
    "reason": "Run the test suite before finishing."
  }
}
```

|领域|值或类型|描述|
|-------|----------------|-------------|
| `hookEventName` | `"Stop"` |标识特定于事件的输出。|
| `decision` | `"block"` |防止 agent 执行停止。|
| `reason` |字符串|当`decision`为`"block"`时需要。解释为什么 agent 应继续。|

> [！IMPORTANT]
> 当`Stop`钩子阻挡agent时，额外的回合会消耗[AI积分](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>)。检查`stop_hook_active`以防止agent无限期地继续。

## `SubagentStart`

当本地 agent 启动子代理时，`SubagentStart` 挂钩将触发。

### 输入

除了 [ 公共输入 ](<#公共输入>) 之外，钩子还接收：

```json
{
  "agent_id": "subagent-456",
  "agent_type": "Plan"
}
```

|领域|类型|描述|
|-------|------|-------------|
| `agent_id` |字符串|子代理的标识符。|
| `agent_type` |字符串|Agent 名称，例如 `"Plan"` 或自定义 agent 名称。|

### 输出

该钩子可以向子代理添加上下文：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "SubagentStart",
    "additionalContext": "Follow the project coding guidelines."
  }
}
```

|领域|值或类型|描述|
|-------|----------------|-------------|
| `hookEventName` | `"SubagentStart"` |标识特定于事件的输出。|
| `additionalContext` |字符串|添加到子代理对话的上下文。|

## `SubagentStop`

当本地子代理即将停止时，`SubagentStop` 挂钩将触发。

### 输入

除了 [ 公共输入 ](<#公共输入>) 之外，钩子还接收：

```json
{
  "agent_id": "subagent-456",
  "agent_type": "Plan",
  "stop_hook_active": false
}
```

|领域|类型|描述|
|-------|------|-------------|
| `agent_id` |字符串|子代理的标识符。|
| `agent_type` |字符串|Agent 名称，例如 `"Plan"` 或自定义 agent 名称。|
| `stop_hook_active` |布尔值|`true` 当子代理已经继续运行时，因为之前的 `SubagentStop` 挂钩阻止了它。|

### 输出

钩子可以要求子代理继续：

```json
{
  "decision": "block",
  "reason": "Verify the results before completing."
}
```

|领域|值或类型|描述|
|-------|----------------|-------------|
| `decision` | `"block"` |防止子代理停止。|
| `reason` |字符串|当`decision`为`"block"`时需要。解释为什么子代理应该继续。|

## `PreCompact`

`PreCompact` 挂钩在本地线束压缩对话上下文之前触发。

### 输入

除了 [ 公共输入 ](<#公共输入>) 之外，钩子还接收：

```json
{
  "trigger": "auto"
}
```

|领域|类型|描述|
|-------|------|-------------|
| `trigger` |字符串|压缩是如何开始的。当会话超出提示预算时，值为`"auto"`。|

该钩子支持[公共输出](<#公共输出>)。

## 相关资源

* [配置agent挂钩](<https://github.com/docs/agent-customization/hooks.md>)
* [创建自定义agents](<https://github.com/docs/agent-customization/custom-agents.md>)
* [使用子代理](<https://github.com/docs/agents/run/subagents.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Local hooks reference

This article is the configuration and event-schema reference for hooks in the **Local** harness in {% data variables.product.prodname_vscode_shortname %}. The Local harness runs in the extension host and uses the PascalCase event names and payloads documented here.

For hooks executed by a provider harness, use the corresponding provider documentation:

* For **Copilot** sessions on Agent Host, use the [GitHub Copilot hooks reference](<https://docs.github.com/en/copilot/reference/hooks-reference>).
* For **Claude** sessions, use the [Claude hooks reference](<https://code.claude.com/docs/en/hooks>).
* For **Codex** sessions, use the [Codex hooks documentation](<https://developers.openai.com/codex/hooks/>).

If Local is the selected session target, use this reference even when the hook file uses a Copilot or Claude-compatible format. The Local parser maps the configuration and then sends the Local payloads documented here.

For help choosing a runtime, creating a hook, or migrating an existing hook, see [Configure agent hooks](<https://github.com/docs/agent-customization/hooks.md>).

## Configuration format

A Local hook file contains a `hooks` object. Each property is a supported event name and its value is an array of command entries:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "type": "command",
        "command": "./scripts/validate-tool.sh",
        "windows": "powershell -File scripts\\validate-tool.ps1",
        "timeout": 15
      }
    ]
  }
}
```

### Command properties

In the native Local format, each command entry must have `type: "command"` and at least one command property:

| Property | Type | Description |
|----------|------|-------------|
| `type` | string | Must be `"command"`. |
| `command` | string | Default cross-platform command. |
| `windows` | string | Windows-specific command override. |
| `linux` | string | Linux-specific command override. |
| `osx` | string | macOS-specific command override. |
| `cwd` | string | Working directory, relative to the repository root. |
| `env` | object | Additional environment variables. |
| `timeout` | number | Timeout in seconds. The default is 30 seconds. |

The Local harness selects an operating system override from the extension host platform. If an override is not defined for that platform, it uses `command`. Copilot and Claude source formats use different property names and defaults before the Local parser maps them to this format.

## Common input

Every Local hook receives a JSON object on standard input (stdin). The object contains these common fields in addition to the fields for the event:

| Field | Type | Description |
|-------|------|-------------|
| `timestamp` | string | ISO 8601 timestamp for when the hook fired. |
| `cwd` | string | Optional working directory for the agent execution. |
| `session_id` | string | Optional identifier for the current agent session. |
| `hook_event_name` | string | Hook event name, such as `PreToolUse`. |
| `transcript_path` | string | Optional absolute path to a file that contains the conversation transcript. |

> [!NOTE]
> The transcript file format is not a stable hook API and might change between {% data variables.product.prodname_vscode_shortname %} releases. Use documented event fields such as `tool_name`, `tool_input`, or `prompt` when possible.

## Common output

A Local hook can write a JSON object to standard output (stdout). All events support these top-level fields:

```json
{
  "continue": false,
  "stopReason": "Security policy violation",
  "systemMessage": "Review the hook result."
}
```

| Field | Type | Description |
|-------|------|-------------|
| `continue` | boolean | Whether processing continues. The default is `true`. Set to `false` to stop the agent execution. |
| `stopReason` | string | Reason shown to the user when `continue` is `false`. |
| `systemMessage` | string | Warning message shown to the user. |

Events can also support fields such as `decision` or `hookSpecificOutput`. The event sections describe those fields.

### Exit codes

The hook command's exit code controls how the Local harness processes its result:

| Exit code | Behavior |
|-----------|----------|
| `0` | Treat the command as successful and process stdout. |
| `2` | Treat stderr as a blocking error and provide it to the model. |
| Any other value | Show a non-blocking warning to the user and continue processing. |

### Choose a control mechanism

Use the least disruptive output that meets the requirement:

* Use exit code `2` to block the current operation and provide stderr to the model.
* Use `continue: false` with `stopReason` to stop the entire agent execution.
* Use `hookSpecificOutput` for event-specific control, such as denying one tool call or adding context.
* Use `systemMessage` to show a warning without changing the event decision.

When outputs conflict, the most restrictive outcome takes precedence. For example, `continue: false` stops the agent execution even if a `PreToolUse` output also permits the tool call.

## `PreToolUse`

The `PreToolUse` hook fires before the Local agent invokes a tool.

### Input

In addition to the [common input](<#common-input>), the hook receives:

```json
{
  "tool_name": "<local-tool-name>",
  "tool_input": {},
  "tool_use_id": "tool-123"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `tool_name` | string | Name of the Local tool. |
| `tool_input` | object | Arguments for the tool call. |
| `tool_use_id` | string | Identifier for the tool call. |

Tool names and input schemas differ between harnesses. Open the [agent debug logs](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md#agent-debug-logs-panel>) to inspect the Local tool schema before you filter or modify a tool call.

### Output

Use `hookSpecificOutput` to control the tool call:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "deny",
    "permissionDecisionReason": "Destructive command blocked by policy.",
    "additionalContext": "Production files are read-only."
  }
}
```

| Field | Values or type | Description |
|-------|----------------|-------------|
| `hookEventName` | `"PreToolUse"` | Identifies the event-specific output. |
| `permissionDecision` | `"allow"`, `"deny"`, or `"ask"` | Allows the tool call, denies it, or requires user confirmation. |
| `permissionDecisionReason` | string | Reason shown to the user. |
| `updatedInput` | object | Optional replacement tool input. The value must match the Local tool schema. |
| `additionalContext` | string | Additional context for the model. |

When multiple hooks return a permission decision for the same tool call, the most restrictive decision wins:

1. `deny` blocks the tool call.
1. `ask` requires user confirmation.
1. `allow` approves the tool call.

If `updatedInput` does not match the Local tool schema, the Local harness ignores it.

## `PostToolUse`

The `PostToolUse` hook fires after a Local tool completes successfully.

### Input

In addition to the [common input](<#common-input>), the hook receives:

```json
{
  "tool_name": "<local-tool-name>",
  "tool_input": {},
  "tool_use_id": "tool-123",
  "tool_response": "<tool-result>"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `tool_name` | string | Name of the Local tool. |
| `tool_input` | object | Arguments for the tool call. |
| `tool_use_id` | string | Identifier for the tool call. |
| `tool_response` | string or object | Result returned by the tool. |

### Output

The hook can add context or block further processing:

```json
{
  "decision": "block",
  "reason": "Post-processing validation failed.",
  "hookSpecificOutput": {
    "hookEventName": "PostToolUse",
    "additionalContext": "The edited file has lint errors."
  }
}
```

| Field | Values or type | Description |
|-------|----------------|-------------|
| `decision` | `"block"` | Optional decision that blocks further processing. |
| `reason` | string | Reason provided to the model when processing is blocked. |
| `hookSpecificOutput.hookEventName` | `"PostToolUse"` | Identifies the event-specific output. |
| `hookSpecificOutput.additionalContext` | string | Additional context for the model. |

## `UserPromptSubmit`

The `UserPromptSubmit` hook fires when the user submits a prompt.

### Input

In addition to the [common input](<#common-input>), the hook receives:

```json
{
  "prompt": "Add input validation to the sign-up form."
}
```

| Field | Type | Description |
|-------|------|-------------|
| `prompt` | string | Text submitted by the user. |

The hook supports the [common output](<#common-output>).

## `SessionStart`

The `SessionStart` hook fires when the first prompt starts a Local agent session.

### Input

In addition to the [common input](<#common-input>), the hook receives:

```json
{
  "source": "new"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `source` | string | How the session started. Currently always `"new"`. |

### Output

The hook can add context to the conversation:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "SessionStart",
    "additionalContext": "Project: my-app 2.1.0 | Branch: main"
  }
}
```

| Field | Values or type | Description |
|-------|----------------|-------------|
| `hookEventName` | `"SessionStart"` | Identifies the event-specific output. |
| `additionalContext` | string | Context added to the conversation. |

## `Stop`

The `Stop` hook fires when the current Local agent execution is about to stop. The event does not indicate that the session ended or became inactive.

When a custom agent runs as a subagent, its `Stop` hook is treated as `SubagentStop`.

### Input

In addition to the [common input](<#common-input>), the hook receives:

```json
{
  "stop_hook_active": false
}
```

| Field | Type | Description |
|-------|------|-------------|
| `stop_hook_active` | boolean | `true` when the agent is already continuing because a previous `Stop` hook blocked it. |

### Output

The hook can require the agent to continue:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "Stop",
    "decision": "block",
    "reason": "Run the test suite before finishing."
  }
}
```

| Field | Values or type | Description |
|-------|----------------|-------------|
| `hookEventName` | `"Stop"` | Identifies the event-specific output. |
| `decision` | `"block"` | Prevents the agent execution from stopping. |
| `reason` | string | Required when `decision` is `"block"`. Explains why the agent should continue. |

> [!IMPORTANT]
> When a `Stop` hook blocks the agent, the additional turns consume [AI credits](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>). Check `stop_hook_active` to prevent the agent from continuing indefinitely.

## `SubagentStart`

The `SubagentStart` hook fires when the Local agent starts a subagent.

### Input

In addition to the [common input](<#common-input>), the hook receives:

```json
{
  "agent_id": "subagent-456",
  "agent_type": "Plan"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `agent_id` | string | Identifier for the subagent. |
| `agent_type` | string | Agent name, such as `"Plan"` or a custom agent name. |

### Output

The hook can add context to the subagent:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "SubagentStart",
    "additionalContext": "Follow the project coding guidelines."
  }
}
```

| Field | Values or type | Description |
|-------|----------------|-------------|
| `hookEventName` | `"SubagentStart"` | Identifies the event-specific output. |
| `additionalContext` | string | Context added to the subagent conversation. |

## `SubagentStop`

The `SubagentStop` hook fires when a Local subagent is about to stop.

### Input

In addition to the [common input](<#common-input>), the hook receives:

```json
{
  "agent_id": "subagent-456",
  "agent_type": "Plan",
  "stop_hook_active": false
}
```

| Field | Type | Description |
|-------|------|-------------|
| `agent_id` | string | Identifier for the subagent. |
| `agent_type` | string | Agent name, such as `"Plan"` or a custom agent name. |
| `stop_hook_active` | boolean | `true` when the subagent is already continuing because a previous `SubagentStop` hook blocked it. |

### Output

The hook can require the subagent to continue:

```json
{
  "decision": "block",
  "reason": "Verify the results before completing."
}
```

| Field | Values or type | Description |
|-------|----------------|-------------|
| `decision` | `"block"` | Prevents the subagent from stopping. |
| `reason` | string | Required when `decision` is `"block"`. Explains why the subagent should continue. |

## `PreCompact`

The `PreCompact` hook fires before the Local harness compacts conversation context.

### Input

In addition to the [common input](<#common-input>), the hook receives:

```json
{
  "trigger": "auto"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `trigger` | string | How compaction started. The value is `"auto"` when the conversation exceeds the prompt budget. |

The hook supports the [common output](<#common-output>).

## Related resources

* [Configure agent hooks](<https://github.com/docs/agent-customization/hooks.md>)
* [Create custom agents](<https://github.com/docs/agent-customization/custom-agents.md>)
* [Use subagents](<https://github.com/docs/agents/run/subagents.md>)


</details>
