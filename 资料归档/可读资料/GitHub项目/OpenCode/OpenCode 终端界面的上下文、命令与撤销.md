# OpenCode 终端界面的上下文、命令与撤销

产品指南。展示文件引用、Shell 调用、会话、压缩、初始化、导出、分享、撤销和重做，以及外部编辑器配置和界面选项。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-08-07](<https://github.com/anomalyco/opencode/commit/8bf5062b89928edd088254ee79ec8fcc1f4b77b7>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：终端操作和撤销范围以归档版本及项目环境为准。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/tui.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 终端界面的上下文、命令与撤销.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: TUI
description: Using the OpenCode terminal user interface.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 提供交互式终端界面或 TUI，以便使用 LLM 处理您的项目。

运行 OpenCode 会启动当前目录的 TUI。

```bash
opencode
```

或者您可以为特定的工作目录启动它。

```bash
opencode /path/to/project
```

进入 TUI 后，您可以通过消息进行提示。

```text
Give me a quick summary of the codebase.
```

---

## 文件参考

您可以使用 `@` 在消息中引用文件。这会在当前工作目录中进行模糊文件搜索。

**提示：**

您还可以使用 `@` 来引用消息中的文件。

```text
How is auth handled in @packages/functions/src/api/index.ts?
```

文件的内容会自动添加到对话中。

配置的 [ 引用 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 跨目录和跨仓库项目参考.md>) 也会出现在 `@` 自动完成中。键入 `@alias` 将引用根添加为上下文，或键入 `@alias/` 以自动完成该引用内的文件。

```text
Compare our setup with @docs/README.md
```

---

## bash 命令

以 `!` 开始一条消息以运行 shell 命令。

```bash
!ls -la
```

命令的输出作为工具结果添加到对话中。

---

## 命令

当使用OpenCodeTUI时，您可以键入`/`后跟命令名称来快速执行操作。例如：

```bash
/help
```

大多数命令还具有使用 `ctrl+x` 作为默认前导键的键盘快捷键。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 快捷键与终端输入设置.md>)。

以下是所有可用的斜杠命令：

---

### 连接

将提供商添加到 OpenCode。允许您从可用的提供商中进行选择并添加其 API 密钥。

```bash
/connect
```

---

### 紧凑型

压缩当前会话。 _别名_：`/summarize`

```bash
/compact
```

**按键绑定：** `ctrl+x c`

---

### 详情

切换工具执行详细信息。

```bash
/details
```

---

### 编辑

打开外部编辑器来撰写消息。使用 `EDITOR` 环境变量中设置的编辑器。 [了解更多](<#编辑器设置>)。

```bash
/editor
```

**按键绑定：** `ctrl+x e`

---

### 退出

退出OpenCode。 _别名_：`/quit`、`/q`

```bash
/exit
```

**按键绑定：** `ctrl+x q`

---

### 出口

将当前对话导出到 Markdown 并在默认编辑器中打开。使用 `EDITOR` 环境变量中设置的编辑器。 [了解更多](<#编辑器设置>)。

```bash
/export
```

**按键绑定：** `ctrl+x x`

---

### 帮助

显示帮助对话框。

```bash
/help
```

---

### 初始化

创建或更新 `AGENTS.md` 的引导设置。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode AGENTS.md、规则作用域与外部指令.md>)。

```bash
/init
```

---

### 型号

列出可用型号。

```bash
/models
```

**按键绑定：** `ctrl+x m`

---

### 新的

开始新的会话。 _别名_：`/clear`

```bash
/new
```

**按键绑定：** `ctrl+x n`

---

### 重做

重做之前撤消的消息。仅在使用`/undo`后可用。

**提示：**

任何文件更改也将被恢复。

在内部，这使用 Git 来管理文件更改。所以你的项目**需要
是一个Git存储库**。

```bash
/redo
```

**按键绑定：** `ctrl+x r`

---

### 会议

列出会话并在会话之间切换。 _别名_：`/resume`、`/continue`

```bash
/sessions
```

**按键绑定：** `ctrl+x l`

---

### 分享

共享当前会话。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 会话分享、取消与数据边界.md>)。

```bash
/share
```

---

### 主题

列出可用的主题。

```bash
/themes
```

**按键绑定：** `ctrl+x t`

---

### 思考

切换对话中 thinking/reasoning 块的可见性。启用后，您可以看到支持扩展思维的模型的推理过程。

**注释：**

该命令仅控制是否**显示思维块** - 它不会启用或禁用模型的推理功能。要切换实际推理功能，请使用 `ctrl+t` 循环切换模型变体。

```bash
/thinking
```

---

### 撤消

撤消对话中的最后一条消息。删除最近的用户消息、所有后续响应以及任何文件更改。

**提示：**

所做的任何文件更改也将被恢复。

在内部，这使用 Git 来管理文件更改。所以你的项目**需要
是一个Git存储库**。

```bash
/undo
```

**按键绑定：** `ctrl+x u`

---

### 取消共享

取消共享当前会话。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 会话分享、取消与数据边界.md#un-sharing>)。

```bash
/unshare
```

---

## 编辑器设置

`/editor` 和 `/export` 命令都使用 `EDITOR` 环境变量中指定的编辑器。

#### Linux/macOS

```bash
# Example for nano or vim
export EDITOR=nano
export EDITOR=vim

# For GUI editors, VS Code, Cursor, VSCodium, Windsurf, Zed, etc.
# include --wait
export EDITOR="code --wait"
```

要使其永久化，请将其添加到您的 shell 配置文件中；
`~/.bashrc`、`~/.zshrc` 等。

#### Windows（CMD）

```bash
set EDITOR=notepad

# For GUI editors, VS Code, Cursor, VSCodium, Windsurf, Zed, etc.
# include --wait
set EDITOR=code --wait
```

要使其永久化，请使用 **系统属性** > **环境
变量**。

#### Windows（PowerShell）

```powershell
$env:EDITOR = "notepad"

# For GUI editors, VS Code, Cursor, VSCodium, Windsurf, Zed, etc.
# include --wait
$env:EDITOR = "code --wait"
```

要使其永久生效，请将其添加到您的 PowerShell 个人资料中。

流行的编辑器选项包括：

- `code` - Visual Studio Code
- `cursor` - Cursor
- `windsurf` - Windsurf
- `nvim` - Neovim 编辑器
- `vim` - Vim 编辑器
- `nano` - 纳米编辑器
- `notepad` - Windows 记事本
- `subl` - Sublime Text

**注释：**

有些编辑器如 VS Code 需要以 `--wait` 标志启动。

某些编辑器需要命令行参数才能在阻止模式下运行。 `--wait` 标志使编辑器进程阻塞直到关闭。

---

## 配置

您可以通过`tui.json`（或`tui.jsonc`）自定义TUI行为。

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "theme": "opencode",
  "leader_timeout": 2000,
  "keybinds": {
    "leader": "ctrl+x",
    "command_list": "ctrl+p"
  },
  "scroll_speed": 3,
  "scroll_acceleration": {
    "enabled": false
  },
  "diff_style": "auto",
  "cursor": {
    "style": "block",
    "blinking": true
  },
  "mouse": true,
  "attention": {
    "enabled": true,
    "notifications": true,
    "sound": true,
    "volume": 0.4,
    "sound_pack": "opencode.default",
    "sounds": {
      "error": "./sounds/error.mp3"
    }
  }
}
```

这与配置 server/runtime 行为的 `opencode.json` 是分开的。

`keybinds` 与内置默认值合并，因此您只需配置要更改的快捷方式。

### 选项

- `theme` - 设置您的 UI 主题。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端主题与显示配置.md>)。
- `keybinds` - 自定义键盘快捷键。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 快捷键与终端输入设置.md>)。
- `leader_timeout` - 控制 OpenCode 在领导者密钥之后等待的时间。默认为 `2000`。
- `scroll_acceleration.enabled` - 启用 macOS 式滚动加速，实现平滑、自然的滚动。启用后，滚动速度会随着快速滚动手势而增加，并在较慢的移动时保持精确。 **此设置优先于`scroll_speed`，并在启用时覆盖它。**
- `scroll_speed` - 控制使用滚动命令时 TUI 滚动的速度（最小值：`0.001`，支持十进制值）。默认为 `3`。 **注意：如果`scroll_acceleration.enabled`设置为`true`，则忽略此项。**
- `diff_style` - 控制差异渲染。 `"auto"` 适应终端宽度，`"stacked"` 始终显示单列布局。
- `cursor` - 控制 TUI 输入字段中的端子 cursor。 `style`默认为`"block"`，可以是`"underline"`、`"line"`或`"default"`； `blinking` 默认为 `true`。当`style`为`"default"`时，恢复终端默认cursor，因此`blinking`无效。
- `mouse` - 在 TUI 中启用或禁用鼠标捕获（默认值：`true`）。禁用时，将保留终端的本机鼠标 selection/scrolling 行为。
- `attention` - 配置 TUI 桌面通知和声音。默认禁用。

使用 `OPENCODE_TUI_CONFIG` 加载自定义 TUI 配置路径。

### 注意

TUI 可以请求关注问题、权限、会话错误和已完成的会话。使用`attention.enabled`启用它；内置事件在触发时播放声音，非子代理事件仅在终端模糊时请求桌面通知。

- `enabled` - 启用所有注意通知和声音。默认为 `false`。
- `notifications` - 启用注意力时允许终端介导的桌面通知。默认为 `true`。
- `sound` - 启用提醒时允许提醒声音。默认为 `true`。
- `volume` - 默认音量从 `0` 到 `1`。默认为 `0.4`。
- `sound_pack` - 使用声音包 ID。默认为 `opencode.default`。
- `sounds` - 覆盖 `default`、`question`、`permission`、`error`、`done` 或 `subagent_done` 的声音文件。路径可以是绝对路径、`file://` URL 或相对于 `tui.json` 的路径。

---

## 定制化

您可以使用命令选项板 (`ctrl+p`) 自定义 TUI 视图的各个方面。这些设置在重新启动后仍然存在。

---

#### 用户名显示

切换您的用户名是否出现在聊天消息中。通过以下方式访问：

- 命令面板：搜索“用户名”或“隐藏用户名”
- 该设置会自动保留并在 TUI 会话中被记住


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode provides an interactive terminal interface or TUI for working on your projects with an LLM.

Running OpenCode starts the TUI for the current directory.

```bash
opencode
```

Or you can start it for a specific working directory.

```bash
opencode /path/to/project
```

Once you're in the TUI, you can prompt it with a message.

```text
Give me a quick summary of the codebase.
```

---

## File references

You can reference files in your messages using `@`. This does a fuzzy file search in the current working directory.

**提示：**

You can also use `@` to reference files in your messages.

```text
How is auth handled in @packages/functions/src/api/index.ts?
```

The content of the file is added to the conversation automatically.

Configured [references](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 跨目录和跨仓库项目参考.md>) also appear in `@` autocomplete. Type `@alias` to add the reference root as context, or type `@alias/` to autocomplete files inside that reference.

```text
Compare our setup with @docs/README.md
```

---

## Bash commands

Start a message with `!` to run a shell command.

```bash
!ls -la
```

The output of the command is added to the conversation as a tool result.

---

## Commands

When using the OpenCode TUI, you can type `/` followed by a command name to quickly execute actions. For example:

```bash
/help
```

Most commands also have keyboard shortcuts using `ctrl+x` as the default leader key. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 快捷键与终端输入设置.md>).

Here are all available slash commands:

---

### connect

Add a provider to OpenCode. Allows you to select from available providers and add their API keys.

```bash
/connect
```

---

### compact

Compact the current session. _Alias_: `/summarize`

```bash
/compact
```

**Keybind:** `ctrl+x c`

---

### details

Toggle tool execution details.

```bash
/details
```

---

### editor

Open external editor for composing messages. Uses the editor set in your `EDITOR` environment variable. [Learn more](<#editor-setup>).

```bash
/editor
```

**Keybind:** `ctrl+x e`

---

### exit

Exit OpenCode. _Aliases_: `/quit`, `/q`

```bash
/exit
```

**Keybind:** `ctrl+x q`

---

### export

Export current conversation to Markdown and open in your default editor. Uses the editor set in your `EDITOR` environment variable. [Learn more](<#editor-setup>).

```bash
/export
```

**Keybind:** `ctrl+x x`

---

### help

Show the help dialog.

```bash
/help
```

---

### init

Guided setup for creating or updating `AGENTS.md`. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode AGENTS.md、规则作用域与外部指令.md>).

```bash
/init
```

---

### models

List available models.

```bash
/models
```

**Keybind:** `ctrl+x m`

---

### new

Start a new session. _Alias_: `/clear`

```bash
/new
```

**Keybind:** `ctrl+x n`

---

### redo

Redo a previously undone message. Only available after using `/undo`.

**提示：**

Any file changes will also be restored.

Internally, this uses Git to manage the file changes. So your project **needs to
be a Git repository**.

```bash
/redo
```

**Keybind:** `ctrl+x r`

---

### sessions

List and switch between sessions. _Aliases_: `/resume`, `/continue`

```bash
/sessions
```

**Keybind:** `ctrl+x l`

---

### share

Share current session. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 会话分享、取消与数据边界.md>).

```bash
/share
```

---

### themes

List available themes.

```bash
/themes
```

**Keybind:** `ctrl+x t`

---

### thinking

Toggle the visibility of thinking/reasoning blocks in the conversation. When enabled, you can see the model's reasoning process for models that support extended thinking.

**注释：**

This command only controls whether thinking blocks are **displayed** - it does not enable or disable the model's reasoning capabilities. To toggle actual reasoning capabilities, use `ctrl+t` to cycle through model variants.

```bash
/thinking
```

---

### undo

Undo last message in the conversation. Removes the most recent user message, all subsequent responses, and any file changes.

**提示：**

Any file changes made will also be reverted.

Internally, this uses Git to manage the file changes. So your project **needs to
be a Git repository**.

```bash
/undo
```

**Keybind:** `ctrl+x u`

---

### unshare

Unshare current session. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 会话分享、取消与数据边界.md#un-sharing>).

```bash
/unshare
```

---

## Editor setup

Both the `/editor` and `/export` commands use the editor specified in your `EDITOR` environment variable.

#### Linux/macOS

```bash
# Example for nano or vim
export EDITOR=nano
export EDITOR=vim

# For GUI editors, VS Code, Cursor, VSCodium, Windsurf, Zed, etc.
# include --wait
export EDITOR="code --wait"
```

To make it permanent, add this to your shell profile;
`~/.bashrc`, `~/.zshrc`, etc.

#### Windows (CMD)

```bash
set EDITOR=notepad

# For GUI editors, VS Code, Cursor, VSCodium, Windsurf, Zed, etc.
# include --wait
set EDITOR=code --wait
```

To make it permanent, use **System Properties** > **Environment
Variables**.

#### Windows (PowerShell)

```powershell
$env:EDITOR = "notepad"

# For GUI editors, VS Code, Cursor, VSCodium, Windsurf, Zed, etc.
# include --wait
$env:EDITOR = "code --wait"
```

To make it permanent, add this to your PowerShell profile.

Popular editor options include:

- `code` - Visual Studio Code
- `cursor` - Cursor
- `windsurf` - Windsurf
- `nvim` - Neovim editor
- `vim` - Vim editor
- `nano` - Nano editor
- `notepad` - Windows Notepad
- `subl` - Sublime Text

**注释：**

Some editors like VS Code need to be started with the `--wait` flag.

Some editors need command-line arguments to run in blocking mode. The `--wait` flag makes the editor process block until closed.

---

## Configure

You can customize TUI behavior through `tui.json` (or `tui.jsonc`).

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "theme": "opencode",
  "leader_timeout": 2000,
  "keybinds": {
    "leader": "ctrl+x",
    "command_list": "ctrl+p"
  },
  "scroll_speed": 3,
  "scroll_acceleration": {
    "enabled": false
  },
  "diff_style": "auto",
  "cursor": {
    "style": "block",
    "blinking": true
  },
  "mouse": true,
  "attention": {
    "enabled": true,
    "notifications": true,
    "sound": true,
    "volume": 0.4,
    "sound_pack": "opencode.default",
    "sounds": {
      "error": "./sounds/error.mp3"
    }
  }
}
```

This is separate from `opencode.json`, which configures server/runtime behavior.

`keybinds` is merged with built-in defaults, so you only need to configure the shortcuts you want to change.

### Options

- `theme` - Sets your UI theme. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端主题与显示配置.md>).
- `keybinds` - Customizes keyboard shortcuts. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 快捷键与终端输入设置.md>).
- `leader_timeout` - Controls how long OpenCode waits after the leader key. Defaults to `2000`.
- `scroll_acceleration.enabled` - Enable macOS-style scroll acceleration for smooth, natural scrolling. When enabled, scroll speed increases with rapid scrolling gestures and stays precise for slower movements. **This setting takes precedence over `scroll_speed` and overrides it when enabled.**
- `scroll_speed` - Controls how fast the TUI scrolls when using scroll commands (minimum: `0.001`, supports decimal values). Defaults to `3`. **Note: This is ignored if `scroll_acceleration.enabled` is set to `true`.**
- `diff_style` - Controls diff rendering. `"auto"` adapts to terminal width, `"stacked"` always shows a single-column layout.
- `cursor` - Controls the terminal cursor in TUI input fields. `style` defaults to `"block"`, can be `"underline"`, `"line"`, or `"default"`; `blinking` defaults to `true`. When `style` is `"default"`, the terminal default cursor is restored, so `blinking` has no effect.
- `mouse` - Enable or disable mouse capture in the TUI (default: `true`). When disabled, the terminal's native mouse selection/scrolling behavior is preserved.
- `attention` - Configures TUI desktop notifications and sounds. Disabled by default.

Use `OPENCODE_TUI_CONFIG` to load a custom TUI config path.

### Attention

The TUI can request attention for questions, permissions, session errors, and completed sessions. Enable it with `attention.enabled`; built-in events play sounds when triggered, and non-subagent events request desktop notifications only when the terminal is blurred.

- `enabled` - Enable all attention notifications and sounds. Defaults to `false`.
- `notifications` - Allow terminal-mediated desktop notifications when attention is enabled. Defaults to `true`.
- `sound` - Allow attention sounds when attention is enabled. Defaults to `true`.
- `volume` - Default sound volume from `0` to `1`. Defaults to `0.4`.
- `sound_pack` - Sound pack ID to use. Defaults to `opencode.default`.
- `sounds` - Override sound files for `default`, `question`, `permission`, `error`, `done`, or `subagent_done`. Paths can be absolute, `file://` URLs, or relative to `tui.json`.

---

## Customization

You can customize various aspects of the TUI view using the command palette (`ctrl+p`). These settings persist across restarts.

---

#### Username display

Toggle whether your username appears in chat messages. Access this through:

- Command palette: Search for "username" or "hide username"
- The setting persists automatically and will be remembered across TUI sessions


</details>
