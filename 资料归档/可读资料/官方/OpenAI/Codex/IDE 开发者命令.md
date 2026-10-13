# IDE 开发者命令

汇总扩展命令、键绑定和斜杠命令的入口与用途，帮助从编辑器控制 Codex 任务。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/developer-commands>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/developer-commands.md?surface=ide>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/IDE 开发者命令.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 开发者命令

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

使用这些命令从 VS Code 命令面板控制 Codex。您还可以将它们绑定到键盘快捷键。

## 分配键绑定

要分配或更改 Codex 命令的键绑定：

1. 打开命令面板（macOS 上的 **Cmd+Shift+P** 或 Windows/Linux 上的 **Ctrl+Shift+P**）。
2. 运行**首选项：打开键盘快捷键**。
3. 搜索 `Codex` 或命令 ID（例如，`chatgpt.newChat`）。
4. 选择铅笔图标，然后输入所需的快捷方式。

## 扩展命令

|命令|默认键绑定|描述|
| ------------------------- | ------------------------------------------ | ------------------------------------------------------- |
| `chatgpt.addToThread`     | -                                          |添加选定的文本范围作为当前聊天的上下文|
| `chatgpt.addFileToThread` | -                                          |添加整个文件作为当前聊天的上下文|
| `chatgpt.newChat`         | macOS: `Cmd+N`<br />Windows/Linux: `Ctrl+N` |创建新聊天|
| `chatgpt.newCodexPanel`   | -                                          |创建一个新的Codex面板|
| `chatgpt.openCommandMenu` | -                                          |打开Codex命令菜单|
| `chatgpt.openSidebar`     | -                                          |打开Codex侧边栏面板|

斜杠命令让您无需离开编辑器即可控制 Codex。使用它们来检查状态、在本地和云模式之间切换或发送反馈。

## 使用斜杠命令

1. 在 Codex 编辑器中，输入 `/`。
2. 从列表中选择一个命令，或继续键入进行过滤（例如，`/status`）。
3. 按**输入**。

## 可用的斜杠命令

|斜线命令|描述|
| -------------------- | --------------------------------------------------------------------------------------- |
| `/approve`           |当自动审核处于活动状态时，批准最近一次自动审核拒绝的重试。|
| `/cloud`             |当云执行可用时，在云中运行聊天。|
| `/cloud-environment` |选择聊天的云环境。|
| `/compact`           |压缩当前聊天的上下文。|
| `/fast`              |打开或关闭目录提供的快速服务层（如果可用）。|
| `/feedback`          |打开反馈对话框以提交反馈并可选择包含日志。|
| `/fork`              |将本地聊天复制到新的本地聊天中。|
| `/goal`              |为Codex设定一个要努力实现的持久目标。|
| `/ide-context`       |打开或关闭自动 IDE 上下文。|
| `/init`              |为当前项目生成一个`AGENTS.md`脚手架。|
| `/local`             |在本地工作区中运行聊天。|
| `/mcp`               |打开【MCP】状态即可查看已连接的服务器。|
| `/memories`          |配置当记忆可用时聊天是否可以使用或生成记忆。|
| `/model`             |选择当前聊天的模型。|
| `/personality`       |选择当当前模型支持个性时，Codex 如何响应。|
| `/plan`              |切换计划模式以进行多步骤计划。|
| `/project`           |选择新聊天的项目。|
| `/reasoning`         |选择当前聊天的推理努力。|
| `/review`            |启动代码审查模式以审查未提交的更改或与基础分支进行比较。|
| `/side`              |开始临时侧聊而不中断主聊天。|
| `/status`            |显示聊天 ID、上下文使用情况和速率限制。|
| `/worktree`          |在新的 Git 工作树中运行聊天。|


## 原文对照

<details>
<summary>展开完整原文</summary>

# Developer commands

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Use these commands to control Codex from the VS Code Command Palette. You can also bind them to keyboard shortcuts.

## Assign a key binding

To assign or change a key binding for a Codex command:

1. Open the Command Palette (**Cmd+Shift+P** on macOS or **Ctrl+Shift+P** on Windows/Linux).
2. Run **Preferences: Open Keyboard Shortcuts**.
3. Search for `Codex` or the command ID (for example, `chatgpt.newChat`).
4. Select the pencil icon, then enter the shortcut you want.

## Extension commands

| Command                   | Default key binding                        | Description                                             |
| ------------------------- | ------------------------------------------ | ------------------------------------------------------- |
| `chatgpt.addToThread`     | -                                          | Add selected text range as context for the current chat |
| `chatgpt.addFileToThread` | -                                          | Add the entire file as context for the current chat     |
| `chatgpt.newChat`         | macOS: `Cmd+N`<br />Windows/Linux: `Ctrl+N` | Create a new chat                                       |
| `chatgpt.newCodexPanel`   | -                                          | Create a new Codex panel                                |
| `chatgpt.openCommandMenu` | -                                          | Open the Codex command menu                             |
| `chatgpt.openSidebar`     | -                                          | Open the Codex sidebar panel                            |

Slash commands let you control Codex without leaving the composer. Use them to check status, switch between local and cloud mode, or send feedback.

## Use a slash command

1. In the Codex composer, type `/`.
2. Select a command from the list, or keep typing to filter (for example, `/status`).
3. Press **Enter**.

## Available slash commands

| Slash command        | Description                                                                             |
| -------------------- | --------------------------------------------------------------------------------------- |
| `/approve`           | Approve one retry of a recent automatic-review denial, when automatic review is active. |
| `/cloud`             | Run the chat in the cloud, when cloud execution is available.                           |
| `/cloud-environment` | Choose the cloud environment for the chat.                                              |
| `/compact`           | Compact the current chat's context.                                                     |
| `/fast`              | Turn a catalog-provided Fast service tier on or off, when available.                    |
| `/feedback`          | Open the feedback dialog to submit feedback and optionally include logs.                |
| `/fork`              | Copy a local chat into a new local chat.                                                |
| `/goal`              | Set a persistent goal for Codex to work toward.                                         |
| `/ide-context`       | Turn automatic IDE context on or off.                                                   |
| `/init`              | Generate an `AGENTS.md` scaffold for the current project.                               |
| `/local`             | Run the chat in your local workspace.                                                   |
| `/mcp`               | Open MCP status to view connected servers.                                              |
| `/memories`          | Configure whether the chat can use or generate memories, when Memories is available.    |
| `/model`             | Choose the model for the current chat.                                                  |
| `/personality`       | Choose how Codex responds, when the current model supports personalities.               |
| `/plan`              | Toggle plan mode for multi-step planning.                                               |
| `/project`           | Choose a project for new chats.                                                         |
| `/reasoning`         | Choose the reasoning effort for the current chat.                                       |
| `/review`            | Start code review mode to review uncommitted changes or compare against a base branch.  |
| `/side`              | Start a temporary side chat without interrupting the main chat.                         |
| `/status`            | Show the chat ID, context usage, and rate limits.                                       |
| `/worktree`          | Run the chat in a new Git worktree.                                                     |


</details>
