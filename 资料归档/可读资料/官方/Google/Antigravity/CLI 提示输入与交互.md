# CLI 提示输入与交互

介绍输入框、多行内容、外部编辑器、会话中断和媒体粘贴的操作方式，帮助在终端中表达和调整开发任务。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/cli/prompting/>)）；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://antigravity.google/docs/cli/prompting/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/CLI 提示输入与交互.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 提示与互动

掌握主要交互模式、多行合成工作流程、会话中断控制和终端媒体粘贴。

## 提示框

反重力 CLI 具有位于终端屏幕底部的粘性提示面板。该面板处理标准用户输入、多行脚本和直接媒体粘贴。

```
───────────────────────────────────────────────────────────────────────────\n
\n> Describe your next engineering task here...\n
\n───────────────────────────────────────────────────────────────────────────
```

### 提交提示

要启动 agent 回合，请在提示面板中输入指令，然后按 Enter 。 agent立即分析您当前的目录工作空间，读取所需的配置，并开始制定执行计划。

### 中断活动会话

如果 agent 在命令执行期间启动不需要的任务或循环，请按 Esc 立即停止会话。

## 多行组合

对于复杂的指令、结构化测试场景或多段指令，请使用内置的多行功能。

### 速记换行符插入

您可以使用以下任意快捷方式插入换行符：

- 标准：按 Shift+Enter 或 ctrl+j 在活动提示窗口中插入干净的换行符而不提交。

- macOS 终端后备：如果您使用的是 Apple 终端（默认情况下不转发 Shift+Enter），请按 Option+Enter 。确保在终端首选项配置文件中选中“使用选项作为元键”。

- 通用斜杠转义：在活动行末尾键入尾随反斜杠 \，然后按 Enter。 CLI 自动删除反斜杠并插入换行符。

### 编辑 $EDITOR 中的提示

要在主开发编辑器中起草或编辑广泛的提示结构，请按照以下步骤操作：

- 在空提示面板内按 ctrl+g。

- CLI 启动系统的默认文本编辑器（例如 vim 、 nano 或 code ，使用 /config 或环境的 $EDITOR 变量进行配置）。

- 在临时编辑器缓冲区内起草多行指令。

- 保存并退出编辑器。 CLI 自动将编辑后的缓冲区直接导入回终端提示符中。

## 附加媒体

Antigravity CLI 支持直接从系统剪贴板粘贴富媒体格式。在提示面板中按 ctrl+v（或本机终端粘贴）可附加屏幕截图模型或视频记录。

### 支持的文件类型

提示框支持以下媒体文件格式：

- 图片：PNG、JPEG、GIF、WebP、BMP、TIFF 和 SVG。

- 视频：MP4、MOV、WebM 和 AVI。

## 后续步骤

掌握交互模式后，探索agent如何呈现操作并请求验证：

- 审查工件：学习检查和管理文件编辑、计划和测试执行。

- 管理对话：恢复之前的线程并分叉活动会话。

- 后台任务和子代理：监视异步后台agents。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Prompting and interaction

Master primary interaction patterns, multiline composition workflows, session interruption controls, and terminal media pasting.

## The prompt box

Antigravity CLI features a sticky prompt panel positioned at the bottom of your terminal screen. This panel handles standard user entries, multiline scripts, and direct media pasting.

```
───────────────────────────────────────────────────────────────────────────\n
\n> Describe your next engineering task here...\n
\n───────────────────────────────────────────────────────────────────────────
```

### Submitting prompts

To initiate an agent turn, type your instruction into the prompt panel and press Enter . The agent immediately analyzes your current directory workspace, reads required configurations, and begins formulating an execution plan.

### Interrupting active sessions

If the agent initiates an undesired task or loops during command execution, press Esc to immediately halt the session.

## Multiline composition

For complex directives, structured test scenarios, or multi-paragraph instructions, use the built-in multiline features.

### Shorthand newline insertions

You can insert newlines using any of the following shortcuts:

- Standard : Press Shift+Enter or ctrl+j to insert a clean newline within your active prompt window without submitting.

- macOS Terminal fallback : If you’re using Apple Terminal (which doesn’t forward Shift+Enter by default), press Option+Enter . Ensure you check Use Option as Meta key in your Terminal Preferences profile.

- Universal slash escape : Type a trailing backslash \ at the end of your active line and press Enter . The CLI automatically removes the backslash and inserts a newline.

### Editing prompts in $EDITOR

To draft or edit extensive prompt structures in your primary development editor, follow these steps:

- Press ctrl+g inside the empty prompt panel.

- The CLI launches your system’s default text editor (such as vim , nano , or code , configured using /config or your environment’s $EDITOR variable).

- Draft your multi-line instruction inside the temporary editor buffer.

- Save and exit the editor. The CLI automatically imports the edited buffer directly back into the terminal prompt.

## Attaching media

Antigravity CLI supports pasting rich media formats directly from your system clipboard. Press ctrl+v (or native terminal paste) inside the prompt panel to attach screenshot mockups or video recordings.

### Supported file types

The prompt box supports the following media file formats:

- Images : PNG, JPEG, GIF, WebP, BMP, TIFF, and SVG.

- Videos : MP4, MOV, WebM, and AVI.

## Next steps

After mastering interaction patterns, explore how the agent presents actions and requests verification:

- Reviewing artifacts : Learn to inspect and manage file edits, plans, and test executions.

- Managing conversations : Resume prior threads and fork active sessions.

- Background tasks and subagents : Monitor asynchronous background agents.


</details>
