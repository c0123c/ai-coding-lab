# OpenCode 编辑器集成与安装排查

产品指南。介绍 VS Code 和相关编辑器中的终端集成、快捷操作、手动安装与常见问题，帮助在现有编辑器使用 OpenCode。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2025-10-03](<https://github.com/anomalyco/opencode/commit/6378e6c06f3296ec8fdea13271c092e9830401ea>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：具体支持和快捷键按编辑器及归档版本核对。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/ide.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 编辑器集成与安装排查.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: IDE
description: The OpenCode extension for VS Code, Cursor, and other IDEs
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 与 VS Code、Cursor 或任何支持终端的 IDE 集成。只需在终端中运行 `opencode` 即可开始。

---

## 用途

- **快速启动**：使用`Cmd+Esc` (Mac) 或`Ctrl+Esc` (Windows/Linux) 在拆分终端视图中打开OpenCode，或者聚焦现有终端会话（如果已有终端会话正在运行）。
- **新会话**：使用 `Cmd+Shift+Esc` (Mac) 或 `Ctrl+Shift+Esc` (Windows/Linux) 启动新的 OpenCode 终端会话，即使该会话已打开。您也可以单击UI中的OpenCode按钮。
- **上下文感知**：自动与OpenCode共享您当前的选择或选项卡。
- **文件引用快捷方式**：使用`Cmd+Option+K` (Mac) 或`Alt+Ctrl+K` (Linux/Windows) 插入文件引用。例如，`@File#L37-42`。

---

## 安装

要在 VS Code 和流行的分叉（如 Cursor、Windsurf、VSCodium）上安装 OpenCode：

1. 打开VS Code
2. 打开集成终端
3. 运行 `opencode` - 扩展自动安装

另一方面，如果您想在从 TUI 运行 `/editor` 或 `/export` 时使用自己的 IDE，则需要设置 `export EDITOR="code --wait"`。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md#editor-setup>)。

---

### 手动安装

在扩展市场中搜索**OpenCode**，然后单击**安装**。

---

### 故障排除

如果扩展无法自动安装：

- 确保您正在集成终端中运行 `opencode`。
- 确认您的 IDE 已安装 CLI：
  - 对于VS Code：`code`命令
  - 对于Cursor：`cursor`命令
  - 对于Windsurf：`windsurf`命令
  - 对于 VSCodium：`codium` 命令
  - 如果没有，请运行 `Cmd+Shift+P` (Mac) 或 `Ctrl+Shift+P` (Windows/Linux) 并搜索“Shell Command: Install 'code' command in PATH”（或 IDE 的等效命令）
- 确保VS Code有权安装扩展


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode integrates with VS Code, Cursor, or any IDE that supports a terminal. Just run `opencode` in the terminal to get started.

---

## Usage

- **Quick Launch**: Use `Cmd+Esc` (Mac) or `Ctrl+Esc` (Windows/Linux) to open OpenCode in a split terminal view, or focus an existing terminal session if one is already running.
- **New Session**: Use `Cmd+Shift+Esc` (Mac) or `Ctrl+Shift+Esc` (Windows/Linux) to start a new OpenCode terminal session, even if one is already open. You can also click the OpenCode button in the UI.
- **Context Awareness**: Automatically share your current selection or tab with OpenCode.
- **File Reference Shortcuts**: Use `Cmd+Option+K` (Mac) or `Alt+Ctrl+K` (Linux/Windows) to insert file references. For example, `@File#L37-42`.

---

## Installation

To install OpenCode on VS Code and popular forks like Cursor, Windsurf, VSCodium:

1. Open VS Code
2. Open the integrated terminal
3. Run `opencode` - the extension installs automatically

If on the other hand you want to use your own IDE when you run `/editor` or `/export` from the TUI, you'll need to set `export EDITOR="code --wait"`. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md#editor-setup>).

---

### Manual Install

Search for **OpenCode** in the Extension Marketplace and click **Install**.

---

### Troubleshooting

If the extension fails to install automatically:

- Ensure you’re running `opencode` in the integrated terminal.
- Confirm the CLI for your IDE is installed:
  - For VS Code: `code` command
  - For Cursor: `cursor` command
  - For Windsurf: `windsurf` command
  - For VSCodium: `codium` command
  - If not, run `Cmd+Shift+P` (Mac) or `Ctrl+Shift+P` (Windows/Linux) and search for "Shell Command: Install 'code' command in PATH" (or the equivalent for your IDE)
- Ensure VS Code has permission to install extensions


</details>
