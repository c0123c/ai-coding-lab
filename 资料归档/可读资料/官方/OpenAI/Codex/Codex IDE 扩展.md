# Codex IDE 扩展

介绍安装与启动、使用编辑器已有上下文执行任务，以及进入日常编码会话的基本步骤。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/codex/ide>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/codex/ide.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex IDE 扩展.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Codex IDE 扩展

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

## 使用编辑器中已有的上下文进行构建

使用代码旁边的 Codex。将打开的文件和选择带入提示中，检查适当的编辑，并在不中断流程的情况下移交较长的工作。

> 插图：代码编辑器旁边的交互式 Codex IDE 扩展

### 从这里开始

- [安装扩展](<https://marketplace.visualstudio.com/items?itemName=openai.chatgpt>)
- [扩展快速入门](<#开始使用>)

### 为什么使用CodexIDE扩展

- **使用已打开的上下文：**直接从作曲家引用打开的文件、选定的代码和最近的聊天记录。 Codex 从您已经看过的代码开始，因此您可以花更少的时间重述问题。
- **查看代码旁边的更改：**阅读摘要，检查重点差异，并在同一聊天中跟进。仅保留您想要的更改，同时源和理由保持可见。
- **任务增长时进行委托：**在本地保持快速迭代，或者在任务需要更多时间和空间时连接Codex网络。从同一编辑器工作流程返回可审阅的结果。

## 开始使用

**开始使用您的IDE。**

安装或启用 Codex，登录并与编辑器中已打开的上下文开始聊天。

### 1。安装或启用Codex

选择您的IDE。 VS Code和兼容编辑器使用Codex扩展； Xcode 和 JetBrains IDE 提供自己的集成。

- [Visual Studio Code](<vscode:extension/openai.chatgpt>)
- [Cursor](<cursor:extension/openai.chatgpt>)
- [Windsurf](<windsurf:extension/openai.chatgpt>)
- [Visual Studio Code 内部人士](<https://marketplace.visualstudio.com/items?itemName=openai.chatgpt>)
- [Xcode](<https://developer.apple.com/documentation/Xcode/setting-up-coding-intelligence>)
- [JetBrains IDE](<https://www.jetbrains.com/help/ai-assistant/codex-agent.html>)

### 2。打开Codex

**VS Code、Cursor 或 Windsurf:** 选择 Codex 图标。如果不可见，请打开命令面板并运行 **Codex：打开 Codex 侧边栏 **。

**Xcode：**打开编码助手，开始新的聊天，选择Codex作为agent。

**JetBrains IDE：** 打开 AI 聊天并选择 Codex。

### 3。开始您的第一次聊天

打开一个项目并要求 Codex 解释代码库、进行重点更改或帮助您调试问题。在任务之前和之后创建 Git 检查点，以便您可以恢复更改。

[阅读最佳实践](<https://learn.chatgpt.com/guides/best-practices>)

### 后续步骤

- [提示编辑器上下文](<https://learn.chatgpt.com/docs/prompting#use-editor-context>)
- [探索IDE命令](<https://learn.chatgpt.com/docs/developer-commands?surface=ide>)
- [配置扩展](<https://learn.chatgpt.com/docs/developer-settings?surface=ide>)

## 看看 Codex 在你的 IDE 中可以做什么

在 Codex 解释、编辑、审查和委托时，请密切关注代码。

- [使用已打开的上下文](<https://learn.chatgpt.com/docs/prompting#use-editor-context>)：向作曲家添加打开的文件、选择或最近的聊天，然后要求Codex解释或编辑已附加该上下文的代码。
- [在代码旁边查看更改](<https://learn.chatgpt.com/docs/prompting>)：无需额外的导航窗格即可查看简明摘要和更改的行。检查两个受影响的文件，保留所需的编辑，并要求从同一视图进行后续操作。
- [当任务变大时进行委派](<https://learn.chatgpt.com/docs/cloud#delegate-from-the-ide-extension>)：选择本地工作以进行快速、动手迭代，或连接Codex网络以委派较长的任务。当您返回查看结果时，聊天仍然可用。

## 当…时使用 Codex IDE 扩展

- [您正在进行集中编辑](<https://learn.chatgpt.com/docs/prompting#use-editor-context>)：将相关文件和Codex保留在同一视图中。
- [您正在学习不熟悉的代码](<https://learn.chatgpt.com/docs/prompting#use-editor-context>)：询问编辑器中已打开的文件和符号。
- [您想要查看适当的更改](<https://learn.chatgpt.com/docs/prompting>)：检查并应用源代码旁边的编辑。
- [您想要委派更大的任务](<https://learn.chatgpt.com/docs/cloud#delegate-from-the-ide-extension>)：从IDE开始云工作并返回结果。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Codex IDE extension

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

## Build with the context already in your editor

Work with Codex beside your code. Bring open files and selections into the prompt, review edits in place, and hand off longer work without breaking your flow.

> Illustration: Interactive Codex IDE extension beside a code editor

### Start here

- [Install the extension](<https://marketplace.visualstudio.com/items?itemName=openai.chatgpt>)
- [Extension quickstart](<#getting-started>)

### Why use Codex IDE extension

- **Use the context already open:** Reference open files, selected code, and recent chats directly from the composer. Codex starts with the code you are already looking at, so you spend less time restating the problem.
- **Review changes beside your code:** Read the summary, inspect a focused diff, and follow up in the same chat. Keep only the changes you want while the source and rationale stay visible together.
- **Delegate when the task grows:** Keep quick iterations local, or connect Codex web when a task needs more time and room. Return to a reviewable result from the same editor workflow.

## Getting started

**Get started in your IDE.**

Install or enable Codex, sign in, and start a chat with the context already open in your editor.

### 1. Install or enable Codex

Choose your IDE. VS Code and compatible editors use the Codex extension; Xcode and JetBrains IDEs provide their own integrations.

- [Visual Studio Code](<vscode:extension/openai.chatgpt>)
- [Cursor](<cursor:extension/openai.chatgpt>)
- [Windsurf](<windsurf:extension/openai.chatgpt>)
- [Visual Studio Code Insiders](<https://marketplace.visualstudio.com/items?itemName=openai.chatgpt>)
- [Xcode](<https://developer.apple.com/documentation/Xcode/setting-up-coding-intelligence>)
- [JetBrains IDEs](<https://www.jetbrains.com/help/ai-assistant/codex-agent.html>)

### 2. Open Codex

**VS Code, Cursor, or Windsurf:** choose the Codex icon. If it is not visible, open the Command Palette and run **Codex: Open Codex Sidebar**.

**Xcode:** open the coding assistant, start a new chat, and choose Codex as the agent.

**JetBrains IDEs:** open AI Chat and select Codex.

### 3. Start your first chat

Open a project and ask Codex to explain the codebase, make a focused change, or help you debug an issue. Create Git checkpoints before and after a task so you can revert changes.

[Read the best practices](<https://learn.chatgpt.com/guides/best-practices>)

### Next steps

- [Prompt with editor context](<https://learn.chatgpt.com/docs/prompting#use-editor-context>)
- [Explore IDE commands](<https://learn.chatgpt.com/docs/developer-commands?surface=ide>)
- [Configure the extension](<https://learn.chatgpt.com/docs/developer-settings?surface=ide>)

## See what Codex can do in your IDE

Stay close to the code while Codex explains, edits, reviews, and delegates.

- [Use the context already open](<https://learn.chatgpt.com/docs/prompting#use-editor-context>): Add an open file, a selection, or a recent chat to the composer, then ask Codex to explain or edit the code with that context already attached.
- [Review changes beside your code](<https://learn.chatgpt.com/docs/prompting>): Review a concise summary and the changed lines without an extra navigation pane. Inspect the two affected files, keep the edits you want, and ask for a follow-up from the same view.
- [Delegate when the task gets bigger](<https://learn.chatgpt.com/docs/cloud#delegate-from-the-ide-extension>): Choose local work for fast, hands-on iteration, or connect Codex web to delegate a longer task. The chat stays available when you return to review the result.

## Use Codex IDE extension when…

- [You are making focused edits](<https://learn.chatgpt.com/docs/prompting#use-editor-context>): Keep the relevant files and Codex in the same view.
- [You are learning unfamiliar code](<https://learn.chatgpt.com/docs/prompting#use-editor-context>): Ask about the files and symbols already open in the editor.
- [You want to review changes in place](<https://learn.chatgpt.com/docs/prompting>): Inspect and apply edits alongside the source.
- [You want to delegate a larger task](<https://learn.chatgpt.com/docs/cloud#delegate-from-the-ide-extension>): Start cloud work from the IDE and return to the result.


</details>
