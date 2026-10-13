# Codex Hooks 使用指南

介绍 Hook 发现、信任、配置、MCP 工具事件、参数展开和执行生命周期，为固定开发检查提供配置依据。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/hooks>)）；本地抓取：2026-10-09
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/hooks.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex Hooks 使用指南.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 挂钩

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

Hooks 是 Codex 的可扩展框架。它们允许您在代理循环期间运行脚本或 MCP
 工具，从而启用以下功能：

- 将聊天发送到自定义 logging/analytics 引擎
- 扫描团队的提示以阻止意外粘贴 API 键
- 总结聊天记录，自动创建持久记忆
- 当聊天停止时运行自定义验证检查，执行标准
- 自定义在某个目录时的提示

钩子如何运行：

- 来自多个文件的匹配钩子全部运行。
- 同时启动同一事件的多个匹配命令挂钩，
所以一个钩子不能阻止另一个匹配钩子的启动。
- 非托管挂钩在运行之前必须经过审查和信任。

钩子在对话中的不同点运行：

|当|挂钩|
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
|转弯时| `PreToolUse`, `PermissionRequest`, `PostToolUse`, `PreCompact`, `PostCompact`, `UserPromptSubmit`, `SubagentStop`, `Stop` |
|当您中断当前转弯时|`Interrupt`（不为子代理运行）|
|当会话或子代理启动时| `SessionStart`, `SubagentStart`                                                                                           |
|当主线程结束时|`SessionEnd`（不为子代理运行）|

## Codex 在哪里寻找钩子

Codex 以以下任一形式发现活动配置层旁边的钩子：

- `hooks.json`
- `config.toml` 内联 `[hooks]` 表

安装的插件还可以通过其插件捆绑生命周期配置
清单或默认 `hooks/hooks.json` 文件。参见 [构建
插件](<https://developers.openai.com/plugins/build/plugins#bundled-mcp-servers-and-lifecycle-hooks>) 为
插件打包规则。

实际上，四个最有用的位置是：

- `~/.codex/hooks.json`
- `~/.codex/config.toml`
- `<repo>/.codex/hooks.json`
- `<repo>/.codex/config.toml`

如果存在多个钩子源，Codex加载所有匹配的钩子。
较高优先级的配置层不会替换较低优先级的钩子。
如果单个层同时包含`hooks.json`和内联`[hooks]`， Codex
合并它们并在启动时发出警告。更喜欢每层一个表示。

Codex 还可以发现与启用的插件捆绑在一起的钩子。插件捆绑的 
 挂钩与其他挂钩源一起加载，并使用与 
 其他非托管挂钩相同的信任审核流程。

仅当项目 `.codex/` 层受信任时，项目本地挂钩才会加载。在
不受信任的项目中，Codex仍然从自己的
活动配置层加载用户和系统挂钩。

## 审查和信任挂钩

Codex 在决定哪些可以运行之前列出已配置的挂钩。在 
 非托管挂钩运行之前，Codex 要求您查看并信任确切的挂钩 
 定义。 Codex 记录对钩子当前哈希的信任，因此新的或
更改的钩子将被标记以供审查并跳过，直到受信任为止。

使用 CLI 中的 `/hooks` 检查挂钩源、查看新的或更改的挂钩、
信任挂钩或禁用单个非托管挂钩。如果挂钩需要在 
 启动时进行检查，Codex 会打印一条警告，告诉您打开 `/hooks`。

来自系统、MDM、云或 `requirements.toml` 源的托管挂钩被标记为 
 为托管、受策略信任，并且无法从用户挂钩浏览器禁用。

对于已经审查 Codex 之外的钩子源的一次性自动化，请传递 
`--dangerously-bypass-hook-trust` 来运行启用的钩子，而不需要 
 为该调用保留钩子信任。

## 配置形状

钩子分为三个级别：

- 挂钩事件，例如`PreToolUse`、`PostToolUse`、`PreCompact`、
`SubagentStart`，或`Stop`
- 决定事件何时匹配的匹配器组
- 当匹配器组匹配时运行的一个或多个钩子处理程序

```json
{
  "description": "Optional lifecycle hooks for this workspace.",
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup|resume",
        "hooks": [
          {
            "type": "command",
            "command": "python3 ~/.codex/hooks/session_start.py",
            "statusMessage": "Loading session notes",
            "additionalContextLimit": 5000
          }
        ]
      }
    ],
    "SessionEnd": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "python3 ~/.codex/hooks/session_end.py",
            "timeout": 3
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/usr/bin/python3 \"$(git rev-parse --show-toplevel)/.codex/hooks/pre_tool_use_policy.py\"",
            "statusMessage": "Checking Bash command"
          }
        ]
      }
    ],
    "PermissionRequest": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/usr/bin/python3 \"$(git rev-parse --show-toplevel)/.codex/hooks/permission_request.py\"",
            "statusMessage": "Checking approval request"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/usr/bin/python3 \"$(git rev-parse --show-toplevel)/.codex/hooks/post_tool_use_review.py\"",
            "statusMessage": "Reviewing Bash output"
          }
        ]
      }
    ],
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/usr/bin/python3 \"$(git rev-parse --show-toplevel)/.codex/hooks/user_prompt_submit_data_flywheel.py\""
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/usr/bin/python3 \"$(git rev-parse --show-toplevel)/.codex/hooks/stop_continue.py\"",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

注意事项：

- `description` 是 `hooks.json` 文件的可选顶级元数据。它
不会改变运行的钩子。
- `timeout` 以秒为单位。
- 如果省略 `timeout`，则 Codex 对于大多数挂钩使用 `600` 秒。
  - `SessionEnd`和`Interrupt`默认使用`1`秒，最多支持`3`秒。
- `statusMessage` 是可选的。
- `additionalContextLimit` 设置命令挂钩可以多少 `additionalContext`
在 Codex 将全文保存到磁盘之前发送到模型，并发送较短的 
 预览。参见[大钩输出](<#大钩输出>)。
- `commandWindows` 是可选的仅限 Windows 的命令覆盖。在TOML中，使用
`command_windows` 或 `commandWindows`。
- 将 `async` 设置为 `true` 到 [ 在
背景](#run-hooks-in-the-background)。
- 支持 `command` 和 `mcp_tool` 处理程序。 `prompt` 和 `agent`
处理程序被解析但被跳过。
- 命令以会话 `cwd` 作为工作目录运行。
- 对于 repo-local 挂钩，更喜欢从 git root 解析，而不是使用
相对路径，例如`.codex/hooks/...`。 Codex 可以从 
 子目录启动，并且基于 git-root 的路径保持钩子位置稳定。

`config.toml` 中的等效内联 TOML：

```toml
[[hooks.SessionStart]]
matcher = "^compact$"

[[hooks.SessionStart.hooks]]
type = "command"
command = '/usr/bin/python3 "$(git rev-parse --show-toplevel)/.codex/hooks/session_start.py"'
additionalContextLimit = 5000

[[hooks.PreToolUse]]
matcher = "^Bash$"

[[hooks.PreToolUse.hooks]]
type = "command"
command = '/usr/bin/python3 "$(git rev-parse --show-toplevel)/.codex/hooks/pre_tool_use_policy.py"'
timeout = 30
statusMessage = "Checking Bash command"

[[hooks.PostToolUse]]
matcher = "^Bash$"

[[hooks.PostToolUse.hooks]]
type = "command"
command = '/usr/bin/python3 "$(git rev-parse --show-toplevel)/.codex/hooks/post_tool_use_review.py"'
timeout = 30
statusMessage = "Reviewing Bash output"
```

## MCP 工具挂钩

MCP 工具挂钩允许生命周期事件调用已连接的 MCP
 服务器上的工具。它将结构化参数直接发送到工具，并使用相同的 
 信任审查和输出合约作为命令挂钩。

### 配置 MCP 工具挂钩

此挂钩要求 `scanner` MCP 服务器在 Codex 写入或 
 编辑文件后扫描每个补丁：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "mcp_tool",
            "server": "scanner",
            "tool": "scan_patch",
            "input": { "patch": "${tool_input.command}" },
            "timeout": 30,
            "statusMessage": "Scanning edited files"
          }
        ]
      }
    ]
  }
}
```

|领域|含义|
| --------------- | ---------------------------------------------------------------- |
| `type`          |必须是`mcp_tool`。|
| `server`        |已连接的 MCP 服务器所需的名称。|
| `tool`          |该服务器公开的工具的必需名称。|
| `input`         |参数模板的可选 JSON 对象。默认为 `{}`。|
| `timeout`       |可选的活动执行超时（以秒为单位）。默认为 `600`。|
| `statusMessage` |挂钩运行时显示的可选消息。|

### 扩展钩子事件的参数

使用 `${field.nested}` 从钩子事件中读取虚线字段。填充整个值的占位符 
 保持其 JSON 类型。较大的 
 字符串内的占位符将呈现为文本。 Codex 递归扩展对象和数组。

对于包含 `{"tool_input":{"file_path":"src/main.rs","count":3}}`,
此参数模板的事件：

```json
{
  "path": "${tool_input.file_path}",
  "count": "${tool_input.count}",
  "message": "Scanning ${tool_input.file_path}"
}
```

变成：

```json
{
  "path": "src/main.rs",
  "count": 3,
  "message": "Scanning src/main.rs"
}
```

### 执行和生命周期

- 挂钩使用现有的 MCP 连接。他们不会启动或重新连接服务器。
- 当工具返回阻止决策时，挂钩可以阻止操作。
错误、丢失的服务器和不可用的工具不会阻止操作。
- MCP工具挂钩同步运行。他们不请求工具批准或触发
其他钩子。
- 适用较短的挂钩或服务器超时。等待 MCP 所花费的时间
诱导响应不计入超时时间。
- `SessionStart` 挂钩可以在 MCP 服务器准备就绪之前运行。如果发生这种情况，
他们不会阻止会话。
- `SessionEnd` 不支持 MCP 工具挂钩。

## 关闭挂钩

默认情况下启用挂钩。要在 `config.toml` 中关闭它们，请设置：

```toml
[features]
hooks = false
```

使用 `hooks` 作为功能键。 `codex_hooks` 仍可用作 
 已弃用的别名。管理员可以在
`requirements.toml`和`[features].hooks = false`中以相同的方式强制挂钩。

## 来自 `requirements.toml` 的托管挂钩

启用托管策略和远程挂钩后，具有本地访问权限和点的 Work Cloud 在云编排器上使用管理员管理的远程 MCP 挂钩。在全局 `requirements.toml` 中配置 `mcp_tool` 处理程序。没有本地访问权限和个人帐户的 Work Cloud 不使用这些企业挂钩。 Command/shell、提示符和 agent 处理程序；来自本地配置、插件或本地目录的挂钩；环境范围的钩子；云编排不支持 `SessionEnd` MCP 挂钩，即使工具在本地执行也是如此。当编排和执行都是本地时，现有支持的挂钩将继续在仅限本地的工作和 Codex 线程中工作。管理员仍然可以在 Agent 安全性中为这些工作流程配置受支持的托管挂钩。

在依赖这些钩子之前，请测试回调连接性、所需事件和失败行为。显式支持的拒绝可以阻止操作，但 `PreToolUse` 回调错误、超时或格式错误的响应可能会使挂钩失败，而不会阻止工具。 MCP 挂钩不提供完整的合规性 API 审核跟踪。

本节中的 Codex 说明仍然适用于支持的 Codex 工作流程。在启用 Work Cloud 本地计算机访问之前，请查看同步设置和 **Agent 安全性 ** 中的任何兼容性警告。请参阅[托管配置](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>)。

在 Codex 中，企业管理的需求可以在 `[hooks]` 下定义内联挂钩。
下面的命令挂钩示例仅适用于 Codex。
当管理员希望在 
 通过传递实际脚本时强制执行挂钩配置时，这非常有用MDM 或其他设备管理系统。
要对本地禁用挂钩的用户强制实施托管挂钩，请将 
`[features].hooks = true` 与 `requirements.toml` 一起固定在 `[hooks]` 旁边。要忽略 
 用户、项目、会话和插件挂钩，同时仍允许管理员 
 管理挂钩，请设置 `allow_managed_hooks_only = true`。

```toml
allow_managed_hooks_only = true

[features]
hooks = true

[hooks]
managed_dir = "/enterprise/hooks"
windows_managed_dir = 'C:\enterprise\hooks'

[[hooks.PreToolUse]]
matcher = "^Bash$"

[[hooks.PreToolUse.hooks]]
type = "command"
command = "python3 /enterprise/hooks/pre_tool_use_policy.py"
command_windows = 'py -3 C:\enterprise\hooks\pre_tool_use_policy.py'
timeout = 30
statusMessage = "Checking managed Bash command"
```

托管钩子的注意事项：

- `managed_dir` 用于 macOS 和 Linux。
- `windows_managed_dir` 用于 Windows。
- Codex不分发`managed_dir`中的脚本；您的企业
工具必须单独安装和更新。
- 托管挂钩命令应使用配置下的绝对脚本路径
托管目录。
- `allow_managed_hooks_only = true` 跳过用户、项目、会话和
插件源，但仍然从 `requirements.toml` 和 
 其他托管配置层加载托管挂钩。

## 插件捆绑钩子

启用插件后，Codex 可以从该插件
 加载生命周期挂钩以及用户、项目和托管挂钩。

默认情况下，Codex 在插件根目录中查找`hooks/hooks.json`。插件
清单可以使用
`.codex-plugin/plugin.json`中的`hooks`条目覆盖默认值。清单条目可以是 `./` 前缀路径、
 前缀 `./` 路径数组、内联钩子对象或内联 
 钩子对象数组。

```json
{
  "name": "repo-policy",
  "hooks": "./hooks/hooks.json"
}
```

清单挂钩路径是相对于插件根解析的，并且必须保留在该根内
。如果清单定义了 `hooks`，则 Codex 使用这些清单 
 条目而不是默认的 `hooks/hooks.json`。

插件挂钩命令接收这些环境变量：

- `PLUGIN_ROOT` 是 Codex 特定的扩展，指向已安装的
插件根。
- `PLUGIN_DATA` 是 Codex 特定的扩展，指向插件的
可写数据目录。
- Codex 还设置 `CLAUDE_PLUGIN_ROOT` 和 `CLAUDE_PLUGIN_DATA`
与现有插件挂钩的兼容性。

插件挂钩使用与其他挂钩相同的事件模式。安装或启用 
 插件不会自动信任其挂钩； Codex 会跳过插件捆绑的挂钩 
，直到您查看并信任当前的挂钩定义。

## 匹配器模式

`matcher` 字段是一个正则表达式字符串，用于在钩子触发时进行过滤。使用 `"*"`、
`""` 或完全省略 `matcher` 以匹配受支持的 
 事件的每次出现。

当前只有一些 Codex 事件荣誉 `matcher`：

|活动|`matcher` 过滤什么|注释|
| ------------------- | ---------------------- | ------------------------------------------------------------ |
| `PermissionRequest` |工具名称|支持包括 `Bash`、`apply_patch`\* 和 MCP 工具名称|
| `PostToolUse`       |工具名称|参见[工具覆盖范围](<#工具覆盖范围>)|
| `PostCompact`       |压实触发器|值为 `manual` 或 `auto`|
| `PreCompact`        |压实触发器|值为 `manual` 或 `auto`|
| `PreToolUse`        |工具名称|参见[工具覆盖范围](<#工具覆盖范围>)|
| `SessionEnd`        |结束原因|目前只有`other`|
| `SessionStart`      |启动源|值为 `startup`、`resume`、`clear` 和 `compact`|
| `SubagentStart`     |子代理类型|值取决于启动的子代理|
| `SubagentStop`      |子代理类型|值取决于停止的子代理|
| `UserPromptSubmit`  |不支持|此事件将忽略任何已配置的 `matcher`|
| `Stop`              |不支持|此事件将忽略任何已配置的 `matcher`|
| `Interrupt`         |不支持|此事件将忽略任何已配置的 `matcher`|

\*对于`apply_patch`，`matcher`值也可以使用`Edit`或`Write`。

示例：

- `Bash`
- `^apply_patch$`
- `Edit|Write`
- `mcp__filesystem__read_file`
- `mcp__filesystem__.*`
- `startup|resume|clear|compact`
- `manual|auto`

### 工具覆盖范围

`PreToolUse`和`PostToolUse`可以观察到多个shell和MCP调用。大多数
本地函数工具使用相同的钩子路径，因此您可以匹配它们的工具名称，
检查它们的JSON参数，并且对于`PreToolUse`，阻止或重写调用。

|刀具路径| `PreToolUse` | `PostToolUse` |注释|
| --------------------------------- | ------------ | ------------- | ------------------------------------------------------------------------------------------------------------------------ |
|外壳命令|是的|是的|匹配为 `Bash`。|
|统一执行（`exec_command`）|是的|是的|匹配为 `Bash`。当命令完成时，稍后的 `write_stdin` 轮询可以传递原始命令的 `PostToolUse`。|
| `apply_patch`                     |是的|是的|匹配为 `apply_patch`、`Edit` 或 `Write`。|
|MCP 工具|是的|是的|匹配MCP工具名称，例如`mcp__filesystem__read_file`。|
|其他本地功能工具|是的|是的|匹配功能工具名称，如`update_plan`。 `spawn_agent` 也匹配 `Agent`。|
|托管工具，例如 `WebSearch`|否|否|这些不使用本地函数工具挂钩路径。|

`write_stdin` 是现有统一执行会话的传输。当它发送输入或轮询已经通过
`PreToolUse`的命令时，它不会再次运行
`PreToolUse`。

一些专用工具路径可以选择退出默认挂钩路径。将工具
挂钩视为有用的护栏，而不是完整的强制边界。

## 常用输入字段

每个命令挂钩都会在 `stdin` 上接收一个 JSON 对象。

这些是您通常会使用的共享字段：

|领域|类型|含义|
| ----------------- | ---------------- | ------------------------------------------------------------------- |
| `session_id`      | `string`         |当前 Codex 会话 ID。子代理挂钩使用父会话 ID。|
| `transcript_path` | `string \| null` |会话记录文件的路径（如果有）|
| `cwd`             | `string`         |会话的工作目录|
| `hook_event_name` | `string`         |当前挂钩事件名称|
| `model`           | `string`         |Codex特定扩展。活动模型蛞蝓|

回合范围挂钩将 `turn_id` 列为其 
 事件特定表中的 Codex 特定扩展。

`SessionStart`、`PreToolUse`、`PermissionRequest`、`PostToolUse`、
`UserPromptSubmit`、`SubagentStart`、`SubagentStop`、`Stop`和`Interrupt`还包括
`permission_mode`，描述当前的权限模式为`default`、
`acceptEdits`、`plan`、`dontAsk`或`bypassPermissions`。

为了方便起见，`transcript_path` 指向聊天记录，但 
 记录格式不是挂钩的稳定接口，并且可能会随着时间而改变。

如果您需要完整的线路格式，请参阅[Schemas](<#模式>)。

## 公共输出字段

`SessionStart`、`PreCompact`、`PostCompact`、`UserPromptSubmit`、
`SubagentStop` 和 `Stop` 支持这些共享 JSON 字段。 `SubagentStart`
 接受 `systemMessage` 和特定于钩子的上下文的相同形状，但 
`continue: false` 不会停止子代理：

```json
{
  "continue": true,
  "stopReason": "optional",
  "systemMessage": "optional",
  "suppressOutput": false
}
```

|领域|效果|
| ---------------- | ----------------------------------------------- |
| `continue`       |如果 `false`，则将该钩子运行标记为已停止|
| `stopReason`     |记录为停止原因|
| `systemMessage`  |在 UI 或事件流中作为警告出现|
| `suppressOutput` |今天解析但尚未实现|

退出`0`且无输出视为成功，Codex继续。

`PreToolUse` 和 `PermissionRequest` 支持 `systemMessage`，但这些事件当前不支持 `continue`、
`stopReason` 和 `suppressOutput`。
如果 `PreToolUse` 挂钩返回 1在这些不受支持的字段中，Codex 将
 挂钩运行标记为失败，报告错误并继续工具调用。

`PostToolUse` 支持 `systemMessage`、`continue: false` 和 `stopReason`。
`suppressOutput` 已解析，但当前不支持该事件。

### 大钩输出

默认情况下，Codex 将每个模型可见的钩子输出消息限制为大致
2,500 标记。如果挂钩返回更多内容，Codex 会将全文保存在 
`<temp_dir>/hook_outputs/<session_id>/<uuid>.txt` 下，并为模型提供 
 头尾预览以及保存的文件路径。这种行为称为
**溢出**：Codex将超大输出存储在磁盘上，并将其替换为
较短的模型可见预览。如果无法写入文件，模型仍然
收到截断的预览。

保持钩子和插件上下文简洁。来自多个钩子和插件的上下文 
 累积起来可能会降低模型性能。提高 `additionalContextLimit`
 会增加这种风险。避免将限制设置为 `0`，除非挂钩强制执行 
 严格的输出上限；否则，单个钩子可以消耗整个上下文
窗口。

对于返回 `additionalContext` 的任何命令挂钩，请在处理程序上设置 
`additionalContextLimit` 以自定义近似令牌 
 阈值：

```json
{
  "type": "command",
  "command": "python3 ~/.codex/hooks/session_start.py",
  "additionalContextLimit": 5000
}
```

省略 `additionalContextLimit` 以使用默认的 `2500` 令牌阈值。使用 
 正整数选择不同的阈值，或使用 `0` 将处理程序的 
 完整附加上下文直接传递给模型。 Codex 独立评估每个
匹配处理程序。对于无法生成附加 
 上下文的事件，Codex 会忽略 `additionalContextLimit` 并报告配置 
 警告。

该设置仅适用于`additionalContext`。工具反馈和继续
提示保持默认限制。

由于超大输出可以写入磁盘，因此请避免在挂钩输出中返回机密或
其他敏感数据。

## 在后台运行钩子

默认情况下，Codex 会等待命令挂钩完成，然后再继续触发它的 
 操作。将 `async` 设置为 `true` 以在 
 后台运行命令挂钩，同时 Codex 继续。

### 配置后台钩子

将 `"async": true` 添加到 `hooks.json` 中的命令处理程序：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "python3 ~/.codex/hooks/post_tool_use.py",
            "async": true,
            "timeout": 120
          }
        ]
      }
    ]
  }
}
```

对于 `config.toml` 中的内联挂钩，设置 `async = true`：

```toml
[[hooks.PostToolUse]]
matcher = "Bash"

[[hooks.PostToolUse.hooks]]
type = "command"
command = "python3 ~/.codex/hooks/post_tool_use.py"
async = true
timeout = 120
```

后台挂钩使用与同步命令挂钩相同的输入、匹配器、信任审查、超时和
[大输出处理](<#大钩输出>)。与
和其他命令挂钩一样，`timeout`以秒为单位，默认为
`600`。 `Interrupt` 挂钩使用一秒默认值和三秒最大值，
 包括当它们在后台运行时。

### 后台钩子如何运行

当后台挂钩完成时，Codex 在对话中的下一个安全点提供支持的信息输出
：

- 如果转弯处于活动状态，Codex 等待当前模型请求和工具调用
完成，然后使输出可用于该 
 回合中的下一个模型请求。
- 如果没有激活的回合，Codex将等待直到下一个用户回合。完成一个
背景挂钩不会开始新一轮。

使用相同的特定于事件的 JSON 输出作为同步挂钩。 Codex 将 
`additionalContext` 添加到模型的上下文和曲面 `systemMessage` 作为 
 警告。

后台挂钩无法阻止、批准、重写或以其他方式控制触发它们的 
 操作。对工具策略、
 权限决策、提示拒绝或转向继续使用同步挂钩。

### 局限性

- Codex 每个会话最多同时运行 8 个后台挂钩。附加
钩子等待运行的钩子完成。
- 每个匹配的调用独立运行，后台钩子可以完成
以与开始时不同的顺序。
- 当会话结束时，Codex取消未完成的后台挂钩并丢弃
尚未交付的输出。
- `SessionEnd` 挂钩始终同步运行。

## 挂钩

### SessionStart

对于此事件，`matcher` 应用于 `source`。

[常用输入字段](<#常用输入字段>)之外的字段：

|领域|类型|含义|
| -------- | -------- | ------------------------------------------------------------------- |
| `source` | `string` |会话如何开始：`startup`、`resume`、`clear` 或 `compact`|

`stdout` 上的纯文本被添加为额外的开发人员上下文。

`stdout` 上的 JSON 支持 [ 通用输出字段 ](<#公共输出字段>) 和 
 这个钩子特定的形状：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "SessionStart",
    "additionalContext": "Load the workspace conventions before editing."
  }
}
```

该 `additionalContext` 文本被添加为额外的开发人员上下文。

Codex 压缩根会话后，`SessionStart` 会在下一个模型请求之前挂钩与 
`source: "compact"` 匹配的运行。这也适用于当
自动压缩发生在回合中间时：Codex将钩子的
附加上下文传递到立即延续，而不是等待
稍后的用户回合。如果钩子返回`continue: false`，则Codex结束回合
，而不发送另一个模型请求。

### SessionEnd

`SessionEnd` 允许您在会话结束时运行命令，例如保存最终的 
 笔记或清理文件。当您归档或
删除仍然打开的对话时，当Codex正常关闭时，或者在
对话处于空闲状态并且在任何连接的客户端中未在30
分钟内打开之后，它在主线程运行。它不会为子代理运行。

退出对话或呼叫 `thread/unsubscribe` 不会立即结束 
 会话，因此它不会立即运行 `SessionEnd`。您的钩子在运行时仍然可以
读取会话记录。

`matcher` 筛选此事件的 `reason`。目前，`reason` 始终为 `other`。
您可以省略 `matcher` 或使用 `other` 在每个 `SessionEnd` 事件上运行。

[常用输入字段](<#常用输入字段>)之外的字段：

|领域|类型|含义|
| -------- | -------- | ------------------------------ |
| `reason` | `string` |会话结束原因：`other`|

例如，`SessionEnd` 命令接收：

```json
{
  "session_id": "thr_123",
  "transcript_path": "/workspace/.codex/rollout.jsonl",
  "cwd": "/workspace",
  "hook_event_name": "SessionEnd",
  "reason": "other"
}
```

`SessionEnd` 挂钩始终同步运行，即使 `async` 为 `true` 时也是如此。它们
是建议性的，因此它们的输出不会引导Codex或保持线程打开。如果 
 命令超时或因错误退出，Codex 会将其报告为挂钩失败。

### SubagentStart

对于此事件，`matcher` 应用于 `agent_type`。

[常用输入字段](<#常用输入字段>)之外的字段：

|领域|类型|含义|
| ----------------- | -------- | ---------------------------------------------- |
| `turn_id`         | `string` |Codex特定扩展。活动 Codex 回合 ID|
| `agent_id`        | `string` |子代理的标识符|
| `agent_type`      | `string` |子代理类型或配置文件|
| `permission_mode` | `string` |当前权限模式|

`stdout` 上的纯文本被添加为子代理的额外开发人员上下文。

`stdout` 上的 JSON 支持 `systemMessage` 以及此钩子特定形状：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "SubagentStart",
    "additionalContext": "Review the repository test conventions first."
  }
}
```

该 `additionalContext` 文本被添加为 
 子代理的额外开发人员上下文。 `continue: false` 已进行兼容性分析，但不会阻止 
 子代理启动。

### PreToolUse

`PreToolUse`可以拦截Bash、通过`apply_patch`、
MCP工具调用执行的文件编辑以及其他本地功能工具。请参阅[工具
覆盖率](<#工具覆盖范围>)了解支持的路径和例外情况。

`matcher` 应用于 `tool_name` 和匹配器别名。对于通过
`apply_patch`进行文件编辑，`matcher`值可以使用`apply_patch`、`Edit`或`Write`；挂钩输入
仍然报告`tool_name: "apply_patch"`。

[常用输入字段](<#常用输入字段>)之外的字段：

|领域|类型|含义|
| ------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| `turn_id`     | `string`     |Codex特定扩展。活动 Codex 回合 ID|
| `tool_name`   | `string`     |规范挂钩工具名称，例如 `Bash`、`apply_patch` 或 MCP 名称，如 `mcp__fs__read`|
| `tool_use_id` | `string`     |此调用的工具调用 ID|
| `tool_input`  | `JSON value` |特定于工具的输入。 `Bash`和`apply_patch`使用`tool_input.command`。 MCP 和其他本地函数工具发送它们的参数。|

`stdout` 上的纯文本将被忽略。

`stdout`上的JSON可以使用`systemMessage`。要拒绝支持的工具调用，请返回
此特定于钩子的形状：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "deny",
    "permissionDecisionReason": "Destructive command blocked by hook."
  }
}
```

Codex 也接受这种旧的块形状：

```json
{
  "decision": "block",
  "reason": "Destructive command blocked by hook."
}
```

您还可以使用退出代码`2`并将阻止原因写入`stderr`。

要添加模型可见上下文而不阻塞，请返回
`hookSpecificOutput.additionalContext`：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "additionalContext": "The pending command touches generated files."
  }
}
```

要在不阻塞的情况下重写支持的工具调用，请返回 
`permissionDecision: "allow"` 和 `updatedInput`：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",
    "updatedInput": {
      "command": "echo rewritten"
    }
  }
}
```

对于 Bash 命令和 `apply_patch`、`updatedInput` 必须包含字符串 
`command` 字段。对于 MCP 和其他本地函数工具，`updatedInput` 是 
 替换参数对象。仅使用
`permissionDecision: "allow"`返回`updatedInput`；其他 `updatedInput` 形状报告为 
 错误。

`permissionDecision: "ask"`、旧版 `decision: "approve"`、`continue: false`、
`stopReason` 和 `suppressOutput` 已解析，但尚不支持。 Codex 将 
 挂钩运行标记为失败，报告错误，并继续工具调用。

### PermissionRequest

`PermissionRequest` 在 Codex 即将请求批准时运行，例如 
 shell 升级或托管网络批准。它可以允许请求、拒绝
请求或拒绝决定并让正常批准提示继续。
它不会为不需要批准的命令运行。

`matcher` 应用于 `tool_name` 和匹配器别名。当前规范的
值包括`Bash`、`apply_patch`和MCP工具名称，例如
`mcp__server__tool`； `apply_patch` 也匹配 `Edit` 和 `Write`。

[常用输入字段](<#常用输入字段>)之外的字段：

|领域|类型|含义|
| ------------------------ | ---------------- | -------------------------------------------------------------------------------------------------------------- |
| `turn_id`                | `string`         |Codex特定扩展。活动 Codex 回合 ID|
| `tool_name`              | `string`         |规范挂钩工具名称，例如 `Bash`、`apply_patch` 或 MCP 名称，如 `mcp__fs__read`|
| `tool_input`             | `JSON value`     |特定于工具的输入。 `Bash` 和 `apply_patch` 使用 `tool_input.command`，而 MCP 工具发送所有参数。|
| `tool_input.description` | `string \| null` |当 Codex 有一个时，人类可读的批准原因|

`stdout` 上的纯文本将被忽略。

某些工具输入可能包括人类可读的描述，但不要依赖每个工具的 
`tool_input.description` 字段。

要批准请求，请返回：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PermissionRequest",
    "decision": {
      "behavior": "allow"
    }
  }
}
```

要拒绝请求，请返回：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PermissionRequest",
    "decision": {
      "behavior": "deny",
      "message": "Blocked by repository policy."
    }
  }
}
```

如果多个匹配的钩子返回决策，则任何 `deny` 获胜。否则，
`allow` 会允许请求继续进行，而不显示批准提示。如果没有
匹配的钩子决定，Codex使用正常的批准流程。

不要为
`PermissionRequest`返回`updatedInput`、`updatedPermissions`或`interrupt`；这些字段是为将来的行为保留的，并且今天失败
关闭。

### PostToolUse

`PostToolUse` 在支持的工具产生输出后运行，包括 Bash、
`apply_patch`、MCP 工具调用和其他本地函数工具。对于 Bash，它
也在以非零状态退出的命令之后运行。它无法撤消已运行工具的副作用
。请参阅[工具覆盖率](<#工具覆盖范围>)了解
支持的路径和例外。

`matcher` 应用于 `tool_name` 和匹配器别名。对于通过
`apply_patch`进行文件编辑，`matcher`值可以使用`apply_patch`、`Edit`或`Write`；挂钩输入
仍然报告`tool_name: "apply_patch"`。

[常用输入字段](<#常用输入字段>)之外的字段：

|领域|类型|含义|
| --------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| `turn_id`       | `string`     |Codex特定扩展。活动 Codex 回合 ID|
| `tool_name`     | `string`     |规范挂钩工具名称，例如 `Bash`、`apply_patch` 或 MCP 名称，如 `mcp__fs__read`|
| `tool_use_id`   | `string`     |此调用的工具调用 ID|
| `tool_input`    | `JSON value` |特定于工具的输入。 `Bash`和`apply_patch`使用`tool_input.command`。 MCP 和其他本地函数工具发送它们的参数。|
| `tool_response` | `JSON value` |特定于工具的输出。 MCP工具发送MCP调用结果。其他本地函数工具通常发送其面向模型的输出。|

`stdout` 上的纯文本将被忽略。

`stdout` 上的 JSON 可以使用 `systemMessage` 以及此钩子特定形状：

```json
{
  "decision": "block",
  "reason": "The Bash output needs review before continuing.",
  "hookSpecificOutput": {
    "hookEventName": "PostToolUse",
    "additionalContext": "The command updated generated files."
  }
}
```

该 `additionalContext` 文本被添加为额外的开发人员上下文。

对于此事件，`decision: "block"` 不会撤消已完成的 Bash 命令。
相反，Codex 会记录反馈，用该
反馈替换工具结果，并从钩子提供的消息继续模型。

您还可以使用退出代码`2`并将反馈原因写入`stderr`。

要在命令 
 已运行后停止对原始工具结果的正常处理，请返回 `continue: false`。 Codex 会将工具结果替换为 
 您的反馈或停止文本并从那里继续。

`updatedMCPToolOutput` 和 `suppressOutput` 已解析，但尚不支持。
Codex 将挂钩运行标记为失败，报告错误，并继续正常
处理工具结果。

#### 从代码模式调用工具

当模型使用代码模式从 JavaScript 调用工具时，挂钩决策将 
 应用于该嵌套调用。 `PreToolUse` 可以在工具运行之前停止该工具或重写 
 其输入。阻塞`PostToolUse`无法消除该工具的副作用，但它
可以阻止原始结果到达正在运行的脚本。

|挂钩结果|代码模式看到什么|
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
|`PreToolUse` 块|工具承诺在工具运行之前被拒绝。|
|`PreToolUse` 返回 `updatedInput`|该工具使用重写的输入运行，并且承诺以该结果解决。|
|`PostToolUse` 返回 `decision: "block"` 或退出并返回代码 `2`|该工具运行，然后承诺因钩子原因而拒绝。|
|`PostToolUse` 返回 `continue: false`|Codex 使用钩子反馈来获得模型可见的结果，但不拒绝嵌套工具承诺。|

### PreCompact

`PreCompact` 在 Codex 压缩聊天之前运行。 `matcher` 将 
 应用于 `trigger`，其值为 `manual` 和 `auto`。

[常用输入字段](<#常用输入字段>)之外的字段：

|领域|类型|含义|
| --------- | -------- | ---------------------------------------------- |
| `turn_id` | `string` |Codex特定扩展。活动 Codex 回合 ID|
| `trigger` | `string` |触发压缩的因素：`manual` 或 `auto`|

`stdout` 上的纯文本将被忽略。

`stdout`上的JSON支持[公共输出字段](<#公共输出字段>)。如果 
 匹配 `PreCompact` 挂钩返回 `continue: false`，则 Codex 在 
 压缩之前停止。

### PostCompact

`PostCompact` 在 Codex 压缩聊天后运行。 `matcher` 将 
 应用于 `trigger`，其值为 `manual` 和 `auto`。

[常用输入字段](<#常用输入字段>)之外的字段：

|领域|类型|含义|
| --------- | -------- | ---------------------------------------------- |
| `turn_id` | `string` |Codex特定扩展。活动 Codex 回合 ID|
| `trigger` | `string` |触发压缩的因素：`manual` 或 `auto`|

`stdout` 上的纯文本将被忽略。

`stdout`上的JSON支持[公共输出字段](<#公共输出字段>)。如果
匹配`PostCompact`钩子返回`continue: false`，则Codex在
压缩后停止。

### UserPromptSubmit

`matcher` 当前未用于此活动。

[常用输入字段](<#常用输入字段>)之外的字段：

|领域|类型|含义|
| --------- | -------- | ---------------------------------------------- |
| `turn_id` | `string` |Codex特定扩展。活动 Codex 回合 ID|
| `prompt`  | `string` |即将发送的用户提示|

`stdout` 上的纯文本被添加为额外的开发人员上下文。

`stdout` 上的 JSON 支持 [ 通用输出字段 ](<#公共输出字段>) 和 
 这个钩子特定的形状：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "UserPromptSubmit",
    "additionalContext": "Ask for a clearer reproduction before editing files."
  }
}
```

该 `additionalContext` 文本被添加为额外的开发人员上下文。

要阻止提示，请返回：

```json
{
  "decision": "block",
  "reason": "Ask for confirmation before doing that."
}
```

您还可以使用退出代码`2`并将阻止原因写入`stderr`。

### SubagentStop

对于此事件，`matcher` 应用于 `agent_type`。

[常用输入字段](<#常用输入字段>)之外的字段：

|领域|类型|含义|
| ------------------------ | ---------------- | ----------------------------------------------- |
| `turn_id`                | `string`         |Codex特定扩展。活动 Codex 回合 ID|
| `agent_id`               | `string`         |子代理的标识符|
| `agent_type`             | `string`         |子代理类型或配置文件|
| `agent_transcript_path`  | `string \| null` |子代理转录文件的路径（如果有）|
| `stop_hook_active`       | `boolean`        |该子代理是否已继续|
| `last_assistant_message` | `string \| null` |最新的子代理助理消息（如果有）|

`SubagentStop` 期望在退出 `0` 时在 `stdout` 上找到 JSON。纯文本输出
对此事件无效。

`stdout`上的JSON支持[公共输出字段](<#公共输出字段>)。要要求 
Codex 继续子代理流程，请返回：

```json
{
  "decision": "block",
  "reason": "Run one more focused pass inside the subagent."
}
```

您还可以使用退出代码 `2` 并将继续原因写入 `stderr`。

如果任何匹配的 `SubagentStop` 挂钩返回 `continue: false`，则 
 优先于其他匹配的 `SubagentStop`
 挂钩的延续决策。

### 停止

`matcher` 当前未用于此活动。

[常用输入字段](<#常用输入字段>)之外的字段：

|领域|类型|含义|
| ------------------------ | ---------------- | ------------------------------------------------- |
| `turn_id`                | `string`         |Codex特定扩展。活动 Codex 回合 ID|
| `stop_hook_active`       | `boolean`        |此回合是否已由 `Stop` 继续|
| `last_assistant_message` | `string \| null` |最新的助理消息文本（如果有）|

`Stop` 期望在退出 `0` 时在 `stdout` 上找到 JSON。纯文本输出对此事件无效
。

`stdout`上的JSON支持[公共输出字段](<#公共输出字段>)。要保持 
Codex 继续运行，请返回：

```json
{
  "decision": "block",
  "reason": "Run one more pass over the failing tests."
}
```

您还可以使用退出代码 `2` 并将继续原因写入 `stderr`。

对于此事件，`decision: "block"` 不会拒绝该回合。相反，它告诉
Codex继续并自动创建一个新的继续提示，将
用作新的用户提示，使用您的`reason`作为提示文本。

如果任何匹配的 `Stop` 挂钩返回 `continue: false`，则其优先级 
 高于其他匹配的 `Stop` 挂钩的延续决策。

### 中断

当您中断主线程上的活动回合时，`Interrupt` 就会运行。使用它
来记录由钩子启动的中断或清理工作。它不会为空闲线程或子代理运行
，并且任何配置的`matcher`都会被忽略。

事件除了[公共输入字段](<#常用输入字段>)外，还包括
`turn_id`、中断回合的id和`permission_mode`。

命令挂钩默认为一秒超时。配置的超时
限制为一到三秒。挂机输出无法阻止
中断或重新开始回合。退出 `0` 且无输出，或返回 JSON 并带有 
 可选的 `systemMessage` 以显示警告。纯文本输出对此事件无效
。

```json
{ "systemMessage": "Saved the interrupted turn to the local audit log." }
```

## 模式

链接的 `main` 分支模式可能包含 
 当前版本中不存在的挂钩字段。使用此页面作为发布行为参考。

如果您需要准确的当前传输格式，请参阅 
[Codex GitHub 存储库 ](<https://github.com/openai/codex/tree/main/codex-rs/hooks/schema/generated>) 中生成的模式。

### 纯文本别名

- 字符串 | 空


## 原文对照

<details>
<summary>展开完整原文</summary>

# Hooks

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Hooks are an extensibility framework for Codex. They let you run scripts or MCP
tools during the agentic loop, enabling features such as:

- Send the chat to a custom logging/analytics engine
- Scan your team's prompts to block accidentally pasting API keys
- Summarize chats to create persistent memories automatically
- Run a custom validation check when a chat turn stops, enforcing standards
- Customize prompting when in a certain directory

How hooks run:

- Matching hooks from multiple files all run.
- Multiple matching command hooks for the same event are launched concurrently,
  so one hook can't prevent another matching hook from starting.
- Non-managed hooks must be reviewed and trusted before they run.

Hooks run at different points in a conversation:

| When                              | Hooks                                                                                                                     |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| During a turn                     | `PreToolUse`, `PermissionRequest`, `PostToolUse`, `PreCompact`, `PostCompact`, `UserPromptSubmit`, `SubagentStop`, `Stop` |
| When you interrupt an active turn | `Interrupt` (doesn't run for subagents)                                                                                   |
| When a session or subagent starts | `SessionStart`, `SubagentStart`                                                                                           |
| When the main thread ends         | `SessionEnd` (doesn't run for subagents)                                                                                  |

## Where Codex looks for hooks

Codex discovers hooks next to active config layers in either of these forms:

- `hooks.json`
- inline `[hooks]` tables inside `config.toml`

Installed plugins can also bundle lifecycle config through their plugin
manifest or a default `hooks/hooks.json` file. See [Build
plugins](<https://developers.openai.com/plugins/build/plugins#bundled-mcp-servers-and-lifecycle-hooks>) for the
plugin packaging rules.

In practice, the four most useful locations are:

- `~/.codex/hooks.json`
- `~/.codex/config.toml`
- `<repo>/.codex/hooks.json`
- `<repo>/.codex/config.toml`

If more than one hook source exists, Codex loads all matching hooks.
Higher-precedence config layers don't replace lower-precedence hooks.
If a single layer contains both `hooks.json` and inline `[hooks]`, Codex
merges them and warns at startup. Prefer one representation per layer.

Codex can also discover hooks bundled with enabled plugins. Plugin-bundled
hooks load alongside other hook sources and use the same trust-review flow as
other non-managed hooks.

Project-local hooks load only when the project `.codex/` layer is trusted. In
untrusted projects, Codex still loads user and system hooks from their own
active config layers.

## Review and trust hooks

Codex lists configured hooks before deciding which ones can run. Before a
non-managed hook can run, Codex requires you to review and trust the exact hook
definition. Codex records trust against the hook's current hash, so new or
changed hooks are marked for review and skipped until trusted.

Use `/hooks` in the CLI to inspect hook sources, review new or changed hooks,
trust hooks, or disable individual non-managed hooks. If hooks need review at
startup, Codex prints a warning that tells you to open `/hooks`.

Managed hooks from system, MDM, cloud, or `requirements.toml` sources are marked
as managed, trusted by policy, and can't be disabled from the user hook browser.

For one-off automation that already vets hook sources outside Codex, pass
`--dangerously-bypass-hook-trust` to run enabled hooks without requiring
persisted hook trust for that invocation.

## Config shape

Hooks are organized in three levels:

- A hook event such as `PreToolUse`, `PostToolUse`, `PreCompact`,
  `SubagentStart`, or `Stop`
- A matcher group that decides when that event matches
- One or more hook handlers that run when the matcher group matches

```json
{
  "description": "Optional lifecycle hooks for this workspace.",
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup|resume",
        "hooks": [
          {
            "type": "command",
            "command": "python3 ~/.codex/hooks/session_start.py",
            "statusMessage": "Loading session notes",
            "additionalContextLimit": 5000
          }
        ]
      }
    ],
    "SessionEnd": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "python3 ~/.codex/hooks/session_end.py",
            "timeout": 3
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/usr/bin/python3 \"$(git rev-parse --show-toplevel)/.codex/hooks/pre_tool_use_policy.py\"",
            "statusMessage": "Checking Bash command"
          }
        ]
      }
    ],
    "PermissionRequest": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/usr/bin/python3 \"$(git rev-parse --show-toplevel)/.codex/hooks/permission_request.py\"",
            "statusMessage": "Checking approval request"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/usr/bin/python3 \"$(git rev-parse --show-toplevel)/.codex/hooks/post_tool_use_review.py\"",
            "statusMessage": "Reviewing Bash output"
          }
        ]
      }
    ],
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/usr/bin/python3 \"$(git rev-parse --show-toplevel)/.codex/hooks/user_prompt_submit_data_flywheel.py\""
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/usr/bin/python3 \"$(git rev-parse --show-toplevel)/.codex/hooks/stop_continue.py\"",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

Notes:

- `description` is optional top-level metadata for a `hooks.json` file. It
  doesn't change which hooks run.
- `timeout` is in seconds.
- If `timeout` is omitted, Codex uses `600` seconds for most hooks.
  - `SessionEnd` and `Interrupt` use `1` second by default and support up to `3` seconds.
- `statusMessage` is optional.
- `additionalContextLimit` sets how much `additionalContext` a command hook can
  send to the model before Codex saves the full text to disk and sends a shorter
  preview instead. See [Large hook output](<#large-hook-output>).
- `commandWindows` is an optional Windows-only command override. In TOML, use
  `command_windows` or `commandWindows`.
- Set `async` to `true` to [run a command hook in the
  background](#run-hooks-in-the-background).
- `command` and `mcp_tool` handlers are supported. `prompt` and `agent`
  handlers are parsed but skipped.
- Commands run with the session `cwd` as their working directory.
- For repo-local hooks, prefer resolving from the git root instead of using a
  relative path such as `.codex/hooks/...`. Codex may be started from a
  subdirectory, and a git-root-based path keeps the hook location stable.

Equivalent inline TOML in `config.toml`:

```toml
[[hooks.SessionStart]]
matcher = "^compact$"

[[hooks.SessionStart.hooks]]
type = "command"
command = '/usr/bin/python3 "$(git rev-parse --show-toplevel)/.codex/hooks/session_start.py"'
additionalContextLimit = 5000

[[hooks.PreToolUse]]
matcher = "^Bash$"

[[hooks.PreToolUse.hooks]]
type = "command"
command = '/usr/bin/python3 "$(git rev-parse --show-toplevel)/.codex/hooks/pre_tool_use_policy.py"'
timeout = 30
statusMessage = "Checking Bash command"

[[hooks.PostToolUse]]
matcher = "^Bash$"

[[hooks.PostToolUse.hooks]]
type = "command"
command = '/usr/bin/python3 "$(git rev-parse --show-toplevel)/.codex/hooks/post_tool_use_review.py"'
timeout = 30
statusMessage = "Reviewing Bash output"
```

## MCP tool hooks

An MCP tool hook lets a lifecycle event call a tool on an already-connected MCP
server. It sends structured arguments directly to the tool and uses the same
trust review and output contract as a command hook.

### Configure an MCP tool hook

This hook asks the `scanner` MCP server to scan each patch after Codex writes or
edits files:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "mcp_tool",
            "server": "scanner",
            "tool": "scan_patch",
            "input": { "patch": "${tool_input.command}" },
            "timeout": 30,
            "statusMessage": "Scanning edited files"
          }
        ]
      }
    ]
  }
}
```

| Field           | Meaning                                                          |
| --------------- | ---------------------------------------------------------------- |
| `type`          | Must be `mcp_tool`.                                              |
| `server`        | Required name of an already-connected MCP server.                |
| `tool`          | Required name of a tool exposed by that server.                  |
| `input`         | Optional JSON object of argument templates. Defaults to `{}`.    |
| `timeout`       | Optional active execution timeout in seconds. Defaults to `600`. |
| `statusMessage` | Optional message shown while the hook runs.                      |

### Expand arguments from the hook event

Use `${field.nested}` to read a dotted field from the hook event. A placeholder
that fills an entire value keeps its JSON type. A placeholder inside a larger
string is rendered as text. Codex expands objects and arrays recursively.

For an event containing `{"tool_input":{"file_path":"src/main.rs","count":3}}`,
this argument template:

```json
{
  "path": "${tool_input.file_path}",
  "count": "${tool_input.count}",
  "message": "Scanning ${tool_input.file_path}"
}
```

becomes:

```json
{
  "path": "src/main.rs",
  "count": 3,
  "message": "Scanning src/main.rs"
}
```

### Execution and lifecycle

- Hooks use an existing MCP connection. They don't start or reconnect servers.
- A hook can block an operation when the tool returns a blocking decision.
  Errors, missing servers, and unavailable tools don't block the operation.
- MCP tool hooks run synchronously. They don't request tool approval or trigger
  other hooks.
- The shorter hook or server timeout applies. Time spent waiting for an MCP
  elicitation response doesn't count against the timeout.
- `SessionStart` hooks can run before an MCP server is ready. If that happens,
  they don't block the session.
- `SessionEnd` doesn't support MCP tool hooks.

## Turn hooks off

Hooks are enabled by default. To turn them off in `config.toml`, set:

```toml
[features]
hooks = false
```

Use `hooks` as the feature key. `codex_hooks` still works as a
deprecated alias. Admins can force hooks off the same way in
`requirements.toml` with `[features].hooks = false`.

## Managed hooks from `requirements.toml`

When managed policy and remote hooks are enabled, Work Cloud with local access and dots use admin-managed remote MCP hooks on the cloud orchestrator. Configure `mcp_tool` handlers in Global `requirements.toml`. Work Cloud without local access and personal accounts do not use these enterprise hooks. Command/shell, prompt, and agent handlers; hooks from local configuration, plugins, or local directories; environment-scoped hooks; and `SessionEnd` MCP hooks are not supported with cloud orchestration, even when tools execute locally. When both orchestration and execution are local, existing supported hooks continue to work in local-only Work and Codex threads. Admins can still configure supported managed hooks in Agent Security for those workflows.

Before relying on these hooks, test callback connectivity, required events, and failure behavior. An explicit supported denial can block an action, but a `PreToolUse` callback error, timeout, or malformed response can fail the hook without blocking the tool. MCP hooks do not provide a complete Compliance API audit trail.

The Codex instructions in this section still apply to supported Codex workflows. Before enabling Local computer access with Work Cloud, review any compatibility warning in the sync setting and **Agent Security**. See [Managed configuration](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>).

In Codex, enterprise-managed requirements can define hooks inline under `[hooks]`.
The command-hook example below applies only to Codex.
This is useful when admins want to enforce the hook configuration while
delivering the actual scripts through MDM or another device-management system.
To enforce managed hooks even for users who disabled hooks locally, pin
`[features].hooks = true` in `requirements.toml` alongside `[hooks]`. To ignore
user, project, session, and plugin hooks while still allowing administrator
managed hooks, set `allow_managed_hooks_only = true`.

```toml
allow_managed_hooks_only = true

[features]
hooks = true

[hooks]
managed_dir = "/enterprise/hooks"
windows_managed_dir = 'C:\enterprise\hooks'

[[hooks.PreToolUse]]
matcher = "^Bash$"

[[hooks.PreToolUse.hooks]]
type = "command"
command = "python3 /enterprise/hooks/pre_tool_use_policy.py"
command_windows = 'py -3 C:\enterprise\hooks\pre_tool_use_policy.py'
timeout = 30
statusMessage = "Checking managed Bash command"
```

Notes for managed hooks:

- `managed_dir` is used on macOS and Linux.
- `windows_managed_dir` is used on Windows.
- Codex doesn't distribute the scripts in `managed_dir`; your enterprise
  tooling must install and update them separately.
- Managed hook commands should use absolute script paths under the configured
  managed directory.
- `allow_managed_hooks_only = true` skips hooks from user, project, session, and
  plugin sources, but still loads managed hooks from `requirements.toml` and
  other managed config layers.

## Plugin-bundled hooks

When a plugin is enabled, Codex can load lifecycle hooks from that plugin
alongside user, project, and managed hooks.

By default, Codex looks for `hooks/hooks.json` inside the plugin root. A plugin
manifest can override that default with a `hooks` entry in
`.codex-plugin/plugin.json`. The manifest entry can be a `./`-prefixed path, an
array of `./`-prefixed paths, an inline hooks object, or an array of inline
hooks objects.

```json
{
  "name": "repo-policy",
  "hooks": "./hooks/hooks.json"
}
```

Manifest hook paths are resolved relative to the plugin root and must stay
inside that root. If a manifest defines `hooks`, Codex uses those manifest
entries instead of the default `hooks/hooks.json`.

Plugin hook commands receive these environment variables:

- `PLUGIN_ROOT` is a Codex-specific extension that points to the installed
  plugin root.
- `PLUGIN_DATA` is a Codex-specific extension that points to the plugin's
  writable data directory.
- Codex also sets `CLAUDE_PLUGIN_ROOT` and `CLAUDE_PLUGIN_DATA` for
  compatibility with existing plugin hooks.

Plugin hooks use the same event schema as other hooks. Installing or enabling a
plugin doesn't automatically trust its hooks; Codex skips plugin-bundled hooks
until you review and trust the current hook definition.

## Matcher patterns

The `matcher` field is a regex string that filters when hooks fire. Use `"*"`,
`""`, or omit `matcher` entirely to match every occurrence of a supported
event.

Only some current Codex events honor `matcher`:

| Event               | What `matcher` filters | Notes                                                        |
| ------------------- | ---------------------- | ------------------------------------------------------------ |
| `PermissionRequest` | tool name              | Support includes `Bash`, `apply_patch`\*, and MCP tool names |
| `PostToolUse`       | tool name              | See [Tool coverage](<#tool-coverage>)                          |
| `PostCompact`       | compaction trigger     | Values are `manual` or `auto`                                |
| `PreCompact`        | compaction trigger     | Values are `manual` or `auto`                                |
| `PreToolUse`        | tool name              | See [Tool coverage](<#tool-coverage>)                          |
| `SessionEnd`        | end reason             | Currently only `other`                                       |
| `SessionStart`      | start source           | Values are `startup`, `resume`, `clear`, and `compact`       |
| `SubagentStart`     | subagent type          | Values depend on the subagent that starts                    |
| `SubagentStop`      | subagent type          | Values depend on the subagent that stops                     |
| `UserPromptSubmit`  | not supported          | Any configured `matcher` is ignored for this event           |
| `Stop`              | not supported          | Any configured `matcher` is ignored for this event           |
| `Interrupt`         | not supported          | Any configured `matcher` is ignored for this event           |

\*For `apply_patch`, `matcher` values can also use `Edit` or `Write`.

Examples:

- `Bash`
- `^apply_patch$`
- `Edit|Write`
- `mcp__filesystem__read_file`
- `mcp__filesystem__.*`
- `startup|resume|clear|compact`
- `manual|auto`

### Tool coverage

`PreToolUse` and `PostToolUse` can observe more than shell and MCP calls. Most
local function tools use the same hook path, so you can match their tool name,
inspect their JSON arguments, and, for `PreToolUse`, block or rewrite the call.

| Tool path                         | `PreToolUse` | `PostToolUse` | Notes                                                                                                                    |
| --------------------------------- | ------------ | ------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Shell commands                    | Yes          | Yes           | Match as `Bash`.                                                                                                         |
| Unified exec (`exec_command`)     | Yes          | Yes           | Match as `Bash`. A later `write_stdin` poll can deliver the original command's `PostToolUse` when that command finishes. |
| `apply_patch`                     | Yes          | Yes           | Match as `apply_patch`, `Edit`, or `Write`.                                                                              |
| MCP tools                         | Yes          | Yes           | Match the MCP tool name, such as `mcp__filesystem__read_file`.                                                           |
| Other local function tools        | Yes          | Yes           | Match the function tool name, such as `update_plan`. `spawn_agent` also matches `Agent`.                                 |
| Hosted tools, such as `WebSearch` | No           | No            | These don't use the local function-tool hook path.                                                                       |

`write_stdin` is transport for an existing unified-exec session. It doesn't run
`PreToolUse` again when it sends input or polls a command that already passed
`PreToolUse`.

Some specialized tool paths can opt out of the default hook path. Treat tool
hooks as a useful guardrail, not a complete enforcement boundary.

## Common input fields

Every command hook receives one JSON object on `stdin`.

These are the shared fields you will usually use:

| Field             | Type             | Meaning                                                             |
| ----------------- | ---------------- | ------------------------------------------------------------------- |
| `session_id`      | `string`         | Current Codex session id. Subagent hooks use the parent session id. |
| `transcript_path` | `string \| null` | Path to the session transcript file, if any                         |
| `cwd`             | `string`         | Working directory for the session                                   |
| `hook_event_name` | `string`         | Current hook event name                                             |
| `model`           | `string`         | Codex-specific extension. Active model slug                         |

Turn-scoped hooks list `turn_id` as a Codex-specific extension in their
event-specific tables.

`SessionStart`, `PreToolUse`, `PermissionRequest`, `PostToolUse`,
`UserPromptSubmit`, `SubagentStart`, `SubagentStop`, `Stop`, and `Interrupt` also include
`permission_mode`, which describes the current permission mode as `default`,
`acceptEdits`, `plan`, `dontAsk`, or `bypassPermissions`.

`transcript_path` points to a chat transcript for convenience, but the
transcript format isn't a stable interface for hooks and may change over time.

If you need the full wire format, see [Schemas](<#schemas>).

## Common output fields

`SessionStart`, `PreCompact`, `PostCompact`, `UserPromptSubmit`,
`SubagentStop`, and `Stop` support these shared JSON fields. `SubagentStart`
accepts the same shape for `systemMessage` and hook-specific context, but
`continue: false` doesn't stop the subagent:

```json
{
  "continue": true,
  "stopReason": "optional",
  "systemMessage": "optional",
  "suppressOutput": false
}
```

| Field            | Effect                                          |
| ---------------- | ----------------------------------------------- |
| `continue`       | If `false`, marks that hook run as stopped      |
| `stopReason`     | Recorded as the reason for stopping             |
| `systemMessage`  | Surfaced as a warning in the UI or event stream |
| `suppressOutput` | Parsed today but not yet implemented            |

Exit `0` with no output is treated as success and Codex continues.

`PreToolUse` and `PermissionRequest` support `systemMessage`, but `continue`,
`stopReason`, and `suppressOutput` aren't currently supported for those events.
If a `PreToolUse` hook returns one of those unsupported fields, Codex marks
that hook run as failed, reports the error, and continues the tool call.

`PostToolUse` supports `systemMessage`, `continue: false`, and `stopReason`.
`suppressOutput` is parsed but not currently supported for that event.

### Large hook output

By default, Codex limits each model-visible hook-output message to roughly
2,500 tokens. If a hook returns more, Codex saves the full text under
`<temp_dir>/hook_outputs/<session_id>/<uuid>.txt` and gives the model a
head-and-tail preview with the saved-file path. This behavior is called
**spilling**: Codex stores oversized output on disk and replaces it with a
shorter, model-visible preview. If the file can't be written, the model still
receives a truncated preview.

Keep hook and plugin context concise. Context from multiple hooks and plugins
  adds up and can degrade model performance. Raising `additionalContextLimit`
  increases that risk. Avoid setting the limit to `0` unless the hook enforces a
  strict output cap; otherwise, a single hook can consume the entire context
  window.

For any command hook that returns `additionalContext`, set
`additionalContextLimit` on the handler to customize the approximate token
threshold:

```json
{
  "type": "command",
  "command": "python3 ~/.codex/hooks/session_start.py",
  "additionalContextLimit": 5000
}
```

Omit `additionalContextLimit` to use the default `2500`-token threshold. Use a
positive integer to select a different threshold, or `0` to pass the handler's
complete additional context directly to the model. Codex evaluates each
matching handler independently. For events that can't produce additional
context, Codex ignores `additionalContextLimit` and reports a configuration
warning.

The setting applies only to `additionalContext`. Tool feedback and continuation
prompts keep the default limit.

Because oversized output can be written to disk, avoid returning secrets or
other sensitive data in hook output.

## Run hooks in the background

By default, Codex waits for a command hook to finish before continuing the
operation that triggered it. Set `async` to `true` to run a command hook in the
background while Codex continues.

### Configure a background hook

Add `"async": true` to a command handler in `hooks.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "python3 ~/.codex/hooks/post_tool_use.py",
            "async": true,
            "timeout": 120
          }
        ]
      }
    ]
  }
}
```

For an inline hook in `config.toml`, set `async = true`:

```toml
[[hooks.PostToolUse]]
matcher = "Bash"

[[hooks.PostToolUse.hooks]]
type = "command"
command = "python3 ~/.codex/hooks/post_tool_use.py"
async = true
timeout = 120
```

Background hooks use the same input, matcher, trust review, timeout, and
[large-output handling](<#large-hook-output>) as synchronous command hooks. As
with other command hooks, `timeout` is measured in seconds and defaults to
`600`. `Interrupt` hooks use a one-second default and a three-second maximum,
including when they run in the background.

### How background hooks run

When a background hook finishes, Codex delivers supported informational output
at the next safe point in the conversation:

- If a turn is active, Codex waits for the current model request and tool calls
  to finish, then makes the output available to the next model request in that
  turn.
- If no turn is active, Codex waits until the next user turn. Finishing a
  background hook doesn't start a new turn.

Use the same event-specific JSON output as a synchronous hook. Codex adds
`additionalContext` to the model's context and surfaces `systemMessage` as a
warning.

Background hooks can't block, approve, rewrite, or otherwise control the
  operation that triggered them. Use synchronous hooks for tool policies,
  permission decisions, prompt rejection, or turn continuation.

### Limitations

- Codex runs up to eight background hooks concurrently per session. Additional
  hooks wait until a running hook finishes.
- Each matching invocation runs independently, and background hooks can finish
  in a different order than they started.
- When the session ends, Codex cancels unfinished background hooks and discards
  output that hasn't been delivered.
- `SessionEnd` hooks always run synchronously.

## Hooks

### SessionStart

`matcher` is applied to `source` for this event.

Fields in addition to [Common input fields](<#common-input-fields>):

| Field    | Type     | Meaning                                                             |
| -------- | -------- | ------------------------------------------------------------------- |
| `source` | `string` | How the session started: `startup`, `resume`, `clear`, or `compact` |

Plain text on `stdout` is added as extra developer context.

JSON on `stdout` supports [Common output fields](<#common-output-fields>) and
this hook-specific shape:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "SessionStart",
    "additionalContext": "Load the workspace conventions before editing."
  }
}
```

That `additionalContext` text is added as extra developer context.

After Codex compacts a root session, `SessionStart` hooks that match
`source: "compact"` run before the next model request. This also applies when
automatic compaction happens in the middle of a turn: Codex delivers the hook's
additional context to the immediate continuation instead of waiting for a
later user turn. If the hook returns `continue: false`, Codex ends the turn
without sending another model request.

### SessionEnd

`SessionEnd` lets you run a command when a session ends, such as saving final
notes or cleaning up files. It runs for the main thread when you archive or
delete a conversation that's still open, when Codex closes normally, or after a
conversation has been idle and isn't open in any connected client for 30
minutes. It won't run for subagents.

Switching away from a conversation or calling `thread/unsubscribe` doesn't end
the session right away, so it won't immediately run `SessionEnd`. Your hook can
still read the session transcript while it runs.

`matcher` filters `reason` for this event. For now, `reason` is always `other`.
You can omit `matcher` or use `other` to run on every `SessionEnd` event.

Fields in addition to [Common input fields](<#common-input-fields>):

| Field    | Type     | Meaning                        |
| -------- | -------- | ------------------------------ |
| `reason` | `string` | Why the session ended: `other` |

For example, a `SessionEnd` command receives:

```json
{
  "session_id": "thr_123",
  "transcript_path": "/workspace/.codex/rollout.jsonl",
  "cwd": "/workspace",
  "hook_event_name": "SessionEnd",
  "reason": "other"
}
```

`SessionEnd` hooks always run synchronously, even when `async` is `true`. They
are advisory, so their output won't steer Codex or keep the thread open. If a
command times out or exits with an error, Codex reports it as a hook failure.

### SubagentStart

`matcher` is applied to `agent_type` for this event.

Fields in addition to [Common input fields](<#common-input-fields>):

| Field             | Type     | Meaning                                        |
| ----------------- | -------- | ---------------------------------------------- |
| `turn_id`         | `string` | Codex-specific extension. Active Codex turn id |
| `agent_id`        | `string` | Identifier for the subagent                    |
| `agent_type`      | `string` | Subagent type or profile                       |
| `permission_mode` | `string` | Current permission mode                        |

Plain text on `stdout` is added as extra developer context for the subagent.

JSON on `stdout` supports `systemMessage` and this hook-specific shape:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "SubagentStart",
    "additionalContext": "Review the repository test conventions first."
  }
}
```

That `additionalContext` text is added as extra developer context for the
subagent. `continue: false` is parsed for compatibility, but it doesn't stop the
subagent from starting.

### PreToolUse

`PreToolUse` can intercept Bash, file edits performed through `apply_patch`,
MCP tool calls, and other local function tools. See [Tool
coverage](<#tool-coverage>) for the supported paths and exceptions.

`matcher` is applied to `tool_name` and matcher aliases. For file edits through
`apply_patch`, `matcher` values can use `apply_patch`, `Edit`, or `Write`; hook input
still reports `tool_name: "apply_patch"`.

Fields in addition to [Common input fields](<#common-input-fields>):

| Field         | Type         | Meaning                                                                                                                          |
| ------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| `turn_id`     | `string`     | Codex-specific extension. Active Codex turn id                                                                                   |
| `tool_name`   | `string`     | Canonical hook tool name, such as `Bash`, `apply_patch`, or an MCP name like `mcp__fs__read`                                     |
| `tool_use_id` | `string`     | Tool-call id for this invocation                                                                                                 |
| `tool_input`  | `JSON value` | Tool-specific input. `Bash` and `apply_patch` use `tool_input.command`. MCP and other local function tools send their arguments. |

Plain text on `stdout` is ignored.

JSON on `stdout` can use `systemMessage`. To deny a supported tool call, return
this hook-specific shape:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "deny",
    "permissionDecisionReason": "Destructive command blocked by hook."
  }
}
```

Codex also accepts this older block shape:

```json
{
  "decision": "block",
  "reason": "Destructive command blocked by hook."
}
```

You can also use exit code `2` and write the blocking reason to `stderr`.

To add model-visible context without blocking, return
`hookSpecificOutput.additionalContext`:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "additionalContext": "The pending command touches generated files."
  }
}
```

To rewrite a supported tool call without blocking, return
`permissionDecision: "allow"` with `updatedInput`:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",
    "updatedInput": {
      "command": "echo rewritten"
    }
  }
}
```

For Bash commands and `apply_patch`, `updatedInput` must include a string
`command` field. For MCP and other local function tools, `updatedInput` is the
replacement arguments object. Return `updatedInput` only with
`permissionDecision: "allow"`; other `updatedInput` shapes are reported as
errors.

`permissionDecision: "ask"`, legacy `decision: "approve"`, `continue: false`,
`stopReason`, and `suppressOutput` are parsed but not supported yet. Codex marks
the hook run as failed, reports the error, and continues the tool call.

### PermissionRequest

`PermissionRequest` runs when Codex is about to ask for approval, such as a
shell escalation or managed-network approval. It can allow the request, deny
the request, or decline to decide and let the normal approval prompt continue.
It doesn't run for commands that don't need approval.

`matcher` is applied to `tool_name` and matcher aliases. Current canonical
values include `Bash`, `apply_patch`, and MCP tool names such as
`mcp__server__tool`; `apply_patch` also matches `Edit` and `Write`.

Fields in addition to [Common input fields](<#common-input-fields>):

| Field                    | Type             | Meaning                                                                                                        |
| ------------------------ | ---------------- | -------------------------------------------------------------------------------------------------------------- |
| `turn_id`                | `string`         | Codex-specific extension. Active Codex turn id                                                                 |
| `tool_name`              | `string`         | Canonical hook tool name, such as `Bash`, `apply_patch`, or an MCP name like `mcp__fs__read`                   |
| `tool_input`             | `JSON value`     | Tool-specific input. `Bash` and `apply_patch` use `tool_input.command` while MCP tools send all the arguments. |
| `tool_input.description` | `string \| null` | Human-readable approval reason, when Codex has one                                                             |

Plain text on `stdout` is ignored.

Some tool inputs may include a human-readable description, but don't rely on a
`tool_input.description` field for every tool.

To approve the request, return:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PermissionRequest",
    "decision": {
      "behavior": "allow"
    }
  }
}
```

To deny the request, return:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PermissionRequest",
    "decision": {
      "behavior": "deny",
      "message": "Blocked by repository policy."
    }
  }
}
```

If multiple matching hooks return decisions, any `deny` wins. Otherwise, an
`allow` lets the request proceed without surfacing the approval prompt. If no
matching hook decides, Codex uses the normal approval flow.

Don't return `updatedInput`, `updatedPermissions`, or `interrupt` for
`PermissionRequest`; those fields are reserved for future behavior and fail
closed today.

### PostToolUse

`PostToolUse` runs after supported tools produce output, including Bash,
`apply_patch`, MCP tool calls, and other local function tools. For Bash, it
also runs after commands that exit with a non-zero status. It can't undo side
effects from a tool that already ran. See [Tool coverage](<#tool-coverage>) for
the supported paths and exceptions.

`matcher` is applied to `tool_name` and matcher aliases. For file edits through
`apply_patch`, `matcher` values can use `apply_patch`, `Edit`, or `Write`; hook input
still reports `tool_name: "apply_patch"`.

Fields in addition to [Common input fields](<#common-input-fields>):

| Field           | Type         | Meaning                                                                                                                          |
| --------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| `turn_id`       | `string`     | Codex-specific extension. Active Codex turn id                                                                                   |
| `tool_name`     | `string`     | Canonical hook tool name, such as `Bash`, `apply_patch`, or an MCP name like `mcp__fs__read`                                     |
| `tool_use_id`   | `string`     | Tool-call id for this invocation                                                                                                 |
| `tool_input`    | `JSON value` | Tool-specific input. `Bash` and `apply_patch` use `tool_input.command`. MCP and other local function tools send their arguments. |
| `tool_response` | `JSON value` | Tool-specific output. MCP tools send the MCP call result. Other local function tools normally send their model-facing output.    |

Plain text on `stdout` is ignored.

JSON on `stdout` can use `systemMessage` and this hook-specific shape:

```json
{
  "decision": "block",
  "reason": "The Bash output needs review before continuing.",
  "hookSpecificOutput": {
    "hookEventName": "PostToolUse",
    "additionalContext": "The command updated generated files."
  }
}
```

That `additionalContext` text is added as extra developer context.

For this event, `decision: "block"` doesn't undo the completed Bash command.
Instead, Codex records the feedback, replaces the tool result with that
feedback, and continues the model from the hook-provided message.

You can also use exit code `2` and write the feedback reason to `stderr`.

To stop normal processing of the original tool result after the command has
already run, return `continue: false`. Codex will replace the tool result with
your feedback or stop text and continue from there.

`updatedMCPToolOutput` and `suppressOutput` are parsed but not supported yet.
Codex marks the hook run as failed, reports the error, and continues normal
processing of the tool result.

#### Tool calls from code mode

When a model uses code mode to call a tool from JavaScript, hook decisions apply
to that nested call. `PreToolUse` can stop the tool before it runs or rewrite
its input. A blocking `PostToolUse` can't undo the tool's side effects, but it
can keep the original result from reaching the running script.

| Hook result                                                      | What code mode sees                                                                                    |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `PreToolUse` blocks                                              | The tool promise rejects before the tool runs.                                                         |
| `PreToolUse` returns `updatedInput`                              | The tool runs with the rewritten input and the promise resolves with that result.                      |
| `PostToolUse` returns `decision: "block"` or exits with code `2` | The tool runs, then the promise rejects with the hook reason.                                          |
| `PostToolUse` returns `continue: false`                          | Codex uses the hook feedback for the model-visible result, but doesn't reject the nested tool promise. |

### PreCompact

`PreCompact` runs before Codex compacts the chat. `matcher` is applied
to `trigger`, whose values are `manual` and `auto`.

Fields in addition to [Common input fields](<#common-input-fields>):

| Field     | Type     | Meaning                                        |
| --------- | -------- | ---------------------------------------------- |
| `turn_id` | `string` | Codex-specific extension. Active Codex turn id |
| `trigger` | `string` | What triggered compaction: `manual` or `auto`  |

Plain text on `stdout` is ignored.

JSON on `stdout` supports [Common output fields](<#common-output-fields>). If a
matching `PreCompact` hook returns `continue: false`, Codex stops before
compacting.

### PostCompact

`PostCompact` runs after Codex compacts the chat. `matcher` is applied
to `trigger`, whose values are `manual` and `auto`.

Fields in addition to [Common input fields](<#common-input-fields>):

| Field     | Type     | Meaning                                        |
| --------- | -------- | ---------------------------------------------- |
| `turn_id` | `string` | Codex-specific extension. Active Codex turn id |
| `trigger` | `string` | What triggered compaction: `manual` or `auto`  |

Plain text on `stdout` is ignored.

JSON on `stdout` supports [Common output fields](<#common-output-fields>). If a
matching `PostCompact` hook returns `continue: false`, Codex stops after
compacting.

### UserPromptSubmit

`matcher` isn't currently used for this event.

Fields in addition to [Common input fields](<#common-input-fields>):

| Field     | Type     | Meaning                                        |
| --------- | -------- | ---------------------------------------------- |
| `turn_id` | `string` | Codex-specific extension. Active Codex turn id |
| `prompt`  | `string` | User prompt that's about to be sent            |

Plain text on `stdout` is added as extra developer context.

JSON on `stdout` supports [Common output fields](<#common-output-fields>) and
this hook-specific shape:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "UserPromptSubmit",
    "additionalContext": "Ask for a clearer reproduction before editing files."
  }
}
```

That `additionalContext` text is added as extra developer context.

To block the prompt, return:

```json
{
  "decision": "block",
  "reason": "Ask for confirmation before doing that."
}
```

You can also use exit code `2` and write the blocking reason to `stderr`.

### SubagentStop

`matcher` is applied to `agent_type` for this event.

Fields in addition to [Common input fields](<#common-input-fields>):

| Field                    | Type             | Meaning                                         |
| ------------------------ | ---------------- | ----------------------------------------------- |
| `turn_id`                | `string`         | Codex-specific extension. Active Codex turn id  |
| `agent_id`               | `string`         | Identifier for the subagent                     |
| `agent_type`             | `string`         | Subagent type or profile                        |
| `agent_transcript_path`  | `string \| null` | Path to the subagent transcript file, if any    |
| `stop_hook_active`       | `boolean`        | Whether this subagent was already continued     |
| `last_assistant_message` | `string \| null` | Latest subagent assistant message, if available |

`SubagentStop` expects JSON on `stdout` when it exits `0`. Plain text output is
invalid for this event.

JSON on `stdout` supports [Common output fields](<#common-output-fields>). To ask
Codex to continue the subagent flow, return:

```json
{
  "decision": "block",
  "reason": "Run one more focused pass inside the subagent."
}
```

You can also use exit code `2` and write the continuation reason to `stderr`.

If any matching `SubagentStop` hook returns `continue: false`, that takes
precedence over continuation decisions from other matching `SubagentStop`
hooks.

### Stop

`matcher` isn't currently used for this event.

Fields in addition to [Common input fields](<#common-input-fields>):

| Field                    | Type             | Meaning                                           |
| ------------------------ | ---------------- | ------------------------------------------------- |
| `turn_id`                | `string`         | Codex-specific extension. Active Codex turn id    |
| `stop_hook_active`       | `boolean`        | Whether this turn was already continued by `Stop` |
| `last_assistant_message` | `string \| null` | Latest assistant message text, if available       |

`Stop` expects JSON on `stdout` when it exits `0`. Plain text output is invalid
for this event.

JSON on `stdout` supports [Common output fields](<#common-output-fields>). To keep
Codex going, return:

```json
{
  "decision": "block",
  "reason": "Run one more pass over the failing tests."
}
```

You can also use exit code `2` and write the continuation reason to `stderr`.

For this event, `decision: "block"` doesn't reject the turn. Instead, it tells
Codex to continue and automatically creates a new continuation prompt that acts
as a new user prompt, using your `reason` as that prompt text.

If any matching `Stop` hook returns `continue: false`, that takes precedence
over continuation decisions from other matching `Stop` hooks.

### Interrupt

`Interrupt` runs when you interrupt an active turn on the main thread. Use it
to record the interruption or clean up work started by a hook. It doesn't run
for idle threads or subagents, and any configured `matcher` is ignored.

In addition to [Common input fields](<#common-input-fields>), the event includes
`turn_id`, the interrupted turn's id, and `permission_mode`.

Command hooks default to a one-second timeout. Configured timeouts are
limited to one through three seconds. Hook output can't prevent the
interruption or restart the turn. Exit `0` with no output, or return JSON with
an optional `systemMessage` to surface a warning. Plain text output is invalid
for this event.

```json
{ "systemMessage": "Saved the interrupted turn to the local audit log." }
```

## Schemas

The linked `main` branch schemas may include hook fields that are not in the
  current release. Use this page as the release behavior reference.

If you need the exact current wire format, see the generated schemas in the
[Codex GitHub repository](<https://github.com/openai/codex/tree/main/codex-rs/hooks/schema/generated>).

### Plain-text aliases

- string | null


</details>
