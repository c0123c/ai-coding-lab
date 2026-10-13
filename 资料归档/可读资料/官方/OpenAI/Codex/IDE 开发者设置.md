# IDE 开发者设置

说明打开 Codex 和编辑器设置、修改选项及具体字段参考，帮助定制扩展行为。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/developer-settings>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/developer-settings.md?surface=ide>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/IDE 开发者设置.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 开发者设置

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

Codex IDE 扩展有两个设置层：

- **Codex 设置 ** 控制与 Codex CLI 共享的 agent 行为，包括
模型、推理工作、权限、沙箱、MCP 服务器和 
 个性化。 Codex 从 `config.toml` 读取这些设置。
- **编辑器设置**控制扩展在VS Code内的行为方式以及
兼容的编辑器。这些设置使用编辑器的
设置系统中的`chatgpt.*`键。

## 打开【Codex】设置

选择 Codex 侧栏中的齿轮图标，然后选择 **Codex 设置 **。使用
常用agent控件的设置面板，或选择**打开config.toml**直接
编辑活动配置层。

配置层顺序和常用键请参见[Config
basics](<https://learn.chatgpt.com/docs/config-file/config-basic>)。对于每个支持的 `config.toml` 密钥，请参阅 
[ 配置参考 ](<https://learn.chatgpt.com/docs/config-file/config-reference>)。

## 更改编辑器设置

要更改设置，请按照下列步骤操作：

1. 打开您的编辑器设置。
2. 搜索 `@ext:openai.chatgpt`、`Codex` 或设置名称。
3. 更新值。

该扩展还支持 VS Code 的 Codex 聊天界面的内置聊天字体设置。

## 编辑器设置参考

|设置|默认|描述|
| -------------------------------------------- | -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `chatgpt.commentCodeLensEnabled`             | `true`         |在 `TODO` 评论上方显示 CodeLens，以便 Codex 可以解决这些问题。|
| `chatgpt.openOnStartup`                      | `false`        |扩展完成启动后，将焦点置于 Codex 侧边栏。|
| `chatgpt.followUpQueueMode`                  | `queue`        |选择运行期间发送的消息是等待下一次运行 (`queue`) 还是引导当前运行 (`steer`)。该扩展将旧的 `interrupt` 值视为 `steer`。按 <kbd>Cmd</kbd>/<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>Enter</kbd> 可反转一条消息的行为。|
| `chatgpt.composerEnterBehavior`              | `enter`        |选择<kbd>输入</kbd>是否始终发送(`enter`)， <kbd>Cmd</kbd>/<kbd>Ctrl</kbd>+<kbd>Enter</kbd> 发送多行提示（`cmdIfMultiline`），或者始终需要修饰符（`cmdAlways`）。|
| `chatgpt.reviewDelivery`                     | `inline`       |尽可能在当前聊天中运行 `/review` (`inline`) 或启动单独的审核聊天 (`detached`)。|
| `chatgpt.localeOverride`                     |汽车|设置 Codex UI 的首选语言。留空以自动检测。|
| `chatgpt.runCodexInWindowsSubsystemForLinux` | `false`        |仅 Windows：当 WSL 可用时，运行 WSL 中的 Codex。当您的存储库和工具位于 WSL2 中或需要 Linux 原生工具时，请使用此选项。更改此设置会重新加载 VS Code。|
| `chatgpt.cliExecutable`                      |取消设置|仅开发：设置 Codex CLI 可执行文件的路径。除非您正在开发Codex CLI，否则不需要此设置；手动覆盖捆绑的可执行文件可能会导致部分扩展无法工作。|
| `chat.fontSize`                              |编辑器默认|控制Codex侧边栏中的聊天文本，包括聊天内容和撰写者。|
| `chat.editor.fontSize`                       |编辑器默认|控制 Codex 聊天中代码呈现的内容，包括代码片段和差异。|

上面的`chatgpt.*`键属于IDE扩展，不要进入
`config.toml`。对于共享 agent 设置，请使用 [ 配置 
 基本配置 ](<https://learn.chatgpt.com/docs/config-file/config-basic>)、[ 高级配置 ](<https://learn.chatgpt.com/docs/config-file/config-advanced>)、
 和 [ 配置参考](<https://learn.chatgpt.com/docs/config-file/config-reference>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Developer settings

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

The Codex IDE extension has two settings layers:

- **Codex settings** control agent behavior shared with Codex CLI, including the
  model, reasoning effort, permissions, sandbox, MCP servers, and
  personalization. Codex reads these settings from `config.toml`.
- **Editor settings** control how the extension behaves inside VS Code and
  compatible editors. These settings use `chatgpt.*` keys in the editor's
  settings system.

## Open Codex settings

Select the gear icon in the Codex sidebar, then select **Codex Settings**. Use
the settings panel for common agent controls, or select **Open config.toml** to
edit the active configuration layer directly.

For the configuration layer order and common keys, see [Config
basics](<https://learn.chatgpt.com/docs/config-file/config-basic>). For every supported `config.toml` key, see the
[Configuration reference](<https://learn.chatgpt.com/docs/config-file/config-reference>).

## Change an editor setting

To change a setting, follow these steps:

1. Open your editor settings.
2. Search for `@ext:openai.chatgpt`, `Codex`, or the setting name.
3. Update the value.

The extension also honors VS Code's built-in chat font settings for Codex chat surfaces.

## Editor settings reference

| Setting                                      | Default        | Description                                                                                                                                                                                                                                                                                |
| -------------------------------------------- | -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `chatgpt.commentCodeLensEnabled`             | `true`         | Show CodeLens above `TODO` comments so Codex can address them.                                                                                                                                                                                                                             |
| `chatgpt.openOnStartup`                      | `false`        | Focus the Codex sidebar when the extension finishes starting.                                                                                                                                                                                                                              |
| `chatgpt.followUpQueueMode`                  | `queue`        | Choose whether messages sent during a run wait for the next run (`queue`) or steer the current run (`steer`). The extension treats the legacy `interrupt` value as `steer`. Press <kbd>Cmd</kbd>/<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>Enter</kbd> to invert the behavior for one message. |
| `chatgpt.composerEnterBehavior`              | `enter`        | Choose whether <kbd>Enter</kbd> always sends (`enter`), <kbd>Cmd</kbd>/<kbd>Ctrl</kbd>+<kbd>Enter</kbd> sends multiline prompts (`cmdIfMultiline`), or the modifier is always required (`cmdAlways`).                                                                                      |
| `chatgpt.reviewDelivery`                     | `inline`       | Run `/review` in the current chat when possible (`inline`) or start a separate review chat (`detached`).                                                                                                                                                                                   |
| `chatgpt.localeOverride`                     | Auto           | Set the preferred language for the Codex UI. Leave empty to detect it automatically.                                                                                                                                                                                                       |
| `chatgpt.runCodexInWindowsSubsystemForLinux` | `false`        | Windows only: Run Codex in WSL when WSL is available. Use this when your repositories and tooling live in WSL2 or when you need Linux-native tooling. Changing this setting reloads VS Code.                                                                                               |
| `chatgpt.cliExecutable`                      | Unset          | Development only: Set the path to the Codex CLI executable. You don't need this setting unless you're developing the Codex CLI; manually overriding the bundled executable can prevent parts of the extension from working.                                                                |
| `chat.fontSize`                              | Editor default | Control chat text in the Codex sidebar, including chat content and the composer.                                                                                                                                                                                                           |
| `chat.editor.fontSize`                       | Editor default | Control code-rendered content in Codex chats, including code snippets and diffs.                                                                                                                                                                                                           |

The `chatgpt.*` keys above belong to the IDE extension and don't go in
`config.toml`. For shared agent settings, use [Config
basics](<https://learn.chatgpt.com/docs/config-file/config-basic>), [Advanced configuration](<https://learn.chatgpt.com/docs/config-file/config-advanced>),
and the [Configuration reference](<https://learn.chatgpt.com/docs/config-file/config-reference>).


</details>
