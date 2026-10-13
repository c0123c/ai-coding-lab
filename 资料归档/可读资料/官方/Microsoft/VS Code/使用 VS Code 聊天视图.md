# 使用 VS Code 聊天视图

介绍编辑器旁的项目会话、布局、聊天切换和 Agent 操作，使代码、调试与测试能和对话配合。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/chat-view.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/chat-view.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/使用 VS Code 聊天视图.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: d5f8a2c1-3e7b-4a9d-b6c4-8f2e1a3d5c7b
DateApproved: 10/7/2026
MetaDescription: Use AI beside your code in the {% data variables.copilot.chat_view %}, ask questions, run agents, and choose a layout.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 使用{% data variables.copilot.chat_view %}

{% data variables.copilot.chat_view %} 是一个代码优先界面，用于在主 {% data variables.product.prodname_vscode %} 窗口中提出问题并使用 agents。当您的任务集中在当前项目并且您希望编辑器、调试器、测试、扩展和笔记本与对话一起使用时，请使用它。

在本文中，您将了解如何打开 {% data variables.copilot.chat_view %}、选择布局、了解其界面以及启动工作区范围的会话。要将其与其他接口进行比较，请参阅[使用方法agents](<https://github.com/docs/agents/overview.md#ways-to-work-with-agents>)。有关与 [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) 共享的对话机制，请参阅 [在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>) 中使用聊天。

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Get started with agents">
按照实践教程使用AI agents在{% data variables.product.prodname_vscode_shortname %}中构建应用程序。

* [开始agentic编码教程](<https://github.com/docs/agents/agents-tutorial.md>)

</div>

## 先决条件

* {% data variables.product.prodname_vscode %}已安装。 [下载{% data variables.product.prodname_vscode_shortname %}](<https://github.com/download>)。
* 通过支持的设置访问 AI 模型：
    * 对于{% data variables.product.prodname_copilot %}提供的型号，[在{% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/setup/copilot.md>)中设置{% data variables.product.prodname_copilot %}。
    * 要使用 {% data variables.product.prodname_anthropic_claude %}、{% data variables.product.prodname_openai_codex %} 或您自己的模型凭据来支持受支持的工作流程，请查看 [agent 线束身份验证选项 ](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>) 和 [ 语言模型设置 ](<https://github.com/docs/agent-customization/language-models.md>)。

## 打开{% data variables.copilot.chat_view %}

{% data variables.copilot.chat_view %} 在编辑器旁边的辅助侧栏中打开。要打开{% data variables.copilot.chat_view %}，请使用以下方法之一：

* 选择{% data variables.product.prodname_vscode_shortname %}标题栏中的**聊天**菜单，然后选择**打开聊天**。

* 使用键盘快捷键 `kb(workbench.action.chat.open)`。

* 从命令行运行 `code chat` 以从外部 {% data variables.product.prodname_vscode_shortname %} 开始聊天。了解有关 [从命令行开始聊天 ](<https://github.com/docs/configure/command-line.md#start-chat-from-the-command-line>) 的更多信息。

### 布局选项

{% data variables.copilot.chat_view %} 在主 {% data variables.product.prodname_vscode_shortname %} 窗口中提供了多种布局选项。选择最适合您的工作流程的布局：

* **侧栏**（默认）：选择**新聊天（+）** > **新聊天**，或运行**聊天：新建聊天**命令。最适合让聊天与代码一起可见。

* **编辑器选项卡**：选择**新建聊天(+)** > **新建聊天编辑器**，或运行**聊天：新建聊天编辑**命令。最适合为聊天提供更多空间或并排比较会话。

* **单独窗口**：选择**新聊天(+)** > **新聊天窗口**，或运行**聊天：新聊天窗口**命令。最适合多显示器设置。

## 界面概览

{% data variables.copilot.chat_view %} 将 agent 保留在您的代码旁边，以便您可以在同一窗口中提示、查看和编辑。 {% data variables.copilot.chat_view %}有以下主要区域：

1. **会话列表**：位于视图顶部，您可以在其中查看和管理当前工作区的会话。了解有关 [ 会话列表 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md#sessions-list>) 的更多信息。

1. **聊天对话**：在中间，您可以在其中查看对话历史记录和agent的响应，包括它对代码所做的更改。

1. **聊天输入**：在底部，您可以在其中键入提示并使用**会话目标**、**Agent**、**语言模型**和配置会话**权限**控制。

![显示 {% data variables.copilot.chat_view %} 以及会话列表、对话和聊天输入的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/chat-view-expanded.png>)

> [!TIP]
> 要使用键盘或屏幕阅读器检查聊天响应，请运行 **打开可访问视图** (`kb(editor.action.accessibleView)`)。了解有关 [ 无障碍视图 ](<https://github.com/docs/configure/accessibility/accessibility.md#accessible-view>) 的更多信息。

{% data variables.copilot.chat_view %} 以两种模式运行：紧凑型和并排型。使用{% data variables.copilot.chat_view %}右上角的切换控件在它们之间进行切换。在紧凑模式下，会话列表和对话共享同一面板。在并排模式下，会话列表在对话旁边保持可见。了解有关 [ 会话列表布局选项 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md#sessions-list>) 的更多信息。

### 在会话中切换聊天

支持的 Agent 主机会话可以包含主聊天和其他对等聊天。在 ** 会话 ** 视图中，展开主聊天行并选择一个对等点以在 {% data variables.copilot.chat_view %} 中打开其对话。

您可以继续在 {% data variables.copilot.agents_window %} 中创建的对等聊天，而无需开始新会话。有关创建步骤、导航选项和特定于界面的控件，请参阅[在会话中运行多个聊天](<https://github.com/docs/agents/run/sessions/manage-sessions.md#run-multiple-chats-in-a-session>)。

## 开始会话

要启动工作区范围的会话，请选择 **新聊天 ** (`+`) 或按 `kb(workbench.action.chat.newChat)`。在发送第一个提示之前，请使用聊天输入控件选择 **会话目标**、**Agent**、**语言模型** 和 **权限**。对于第一个编码任务，使用**Copilot**、**Agent**、**自动**和**手动权限**，如[agents中所示快速入门](<https://github.com/docs/agents/quickstart.md>)。可用的选择取决于您的帐户、安装的提供商和组织策略。了解有关 [ 配置 agent 会话 ](<https://github.com/docs/agents/run/agent-harnesses.md#understand-the-session-controls>) 的更多信息。

输入提示并按 `kb(workbench.action.chat.submit)`。有关共享请求、上下文和对话控件，请参阅 [在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>) 中使用聊天。有关会话上下文、历史记录和组织，请参阅[管理agent会话](<https://github.com/docs/agents/run/sessions/manage-sessions.md>)。

## 与 agent 一起工作

由于 {% data variables.copilot.chat_view %} 在主 {% data variables.product.prodname_vscode_shortname %} 窗口中运行，因此您可以在编辑器中检查 agent 编辑、跟踪终端活动、调试应用程序以及运行任务或测试，而无需切换界面。 agent 还可以访问受支持的扩展，并且可以 [ 与 Jupyter 笔记本 ](<https://github.com/docs/agents/guides/notebooks-with-ai.md>) 配合使用。当您连接到 [ 远程工作区 ](<https://github.com/docs/remote/remote-overview.md>) 时，会话使用相同的远程项目上下文和工具。

**Copilot** 会话目标应用编辑并将其直接保存到选定的文件夹或工作树。在响应中选择更改的文件以检查其差异，或设置 `setting(chat.checkpoints.showFileChanges)` 以在每次请求后显示更改的文件摘要。有关修订、检查点、特定于目标的审核工作流程和集成更改，请参阅[审核AI-生成的代码编辑](<https://github.com/docs/agents/run/review-code-edits.md>)。

## 后续步骤

* [聊天交互控件](<https://github.com/docs/chat/chat-overview.md>) - 发送和引导请求、添加上下文以及导航对话。
* [管理 agent 会话](<https://github.com/docs/agents/run/sessions/manage-sessions.md>) - 组织、存档和分叉会话。
* [使用 {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) - 跨多个项目使用 agents。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Use the {% data variables.copilot.chat_view %}

The {% data variables.copilot.chat_view %} is a code-first interface for asking questions and working with agents in the main {% data variables.product.prodname_vscode %} window. Use it when your task centers on the current project and you want the editor, debugger, tests, extensions, and notebooks available alongside the conversation.

In this article, you learn how to open the {% data variables.copilot.chat_view %}, choose a layout, understand its interface, and start a workspace-scoped session. To compare it with other interfaces, see [Ways to work with agents](<https://github.com/docs/agents/overview.md#ways-to-work-with-agents>). For conversation mechanics shared with the [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>), see [Use chat in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>).

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Get started with agents">
Follow a hands-on tutorial to build an app with AI agents in {% data variables.product.prodname_vscode_shortname %}.

* [Start agentic coding tutorial](<https://github.com/docs/agents/agents-tutorial.md>)

</div>

## Prerequisites

* {% data variables.product.prodname_vscode %} installed. [Download {% data variables.product.prodname_vscode_shortname %}](<https://github.com/download>).
* Access to an AI model through a supported setup:
    * For models provided by {% data variables.product.prodname_copilot %}, [set up {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/setup/copilot.md>).
    * To use {% data variables.product.prodname_anthropic_claude %}, {% data variables.product.prodname_openai_codex %}, or your own model credentials for supported workflows, review the [agent harness authentication options](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>) and [language model setup](<https://github.com/docs/agent-customization/language-models.md>).

## Open the {% data variables.copilot.chat_view %}

The {% data variables.copilot.chat_view %} opens in the Secondary Side Bar, next to your editor. To open the {% data variables.copilot.chat_view %}, use one of the following methods:

* Select the **Chat** menu in the {% data variables.product.prodname_vscode_shortname %} title bar, and then select **Open Chat**.

* Use the keyboard shortcut `kb(workbench.action.chat.open)`.

* Run `code chat` from the command line to start chat from outside {% data variables.product.prodname_vscode_shortname %}. Learn more about [starting chat from the command line](<https://github.com/docs/configure/command-line.md#start-chat-from-the-command-line>).

### Layout options

The {% data variables.copilot.chat_view %} offers several layout options within the main {% data variables.product.prodname_vscode_shortname %} window. Choose the layout that best fits your workflow:

* **Side bar** (default): select **New Chat (+)** > **New Chat**, or run the **Chat: New Chat** command. Best for keeping chat visible alongside your code.

* **Editor tab**: select **New Chat (+)** > **New Chat Editor**, or run the **Chat: New Chat Editor** command. Best for giving chat more space or comparing sessions side by side.

* **Separate window**: select **New Chat (+)** > **New Chat Window**, or run the **Chat: New Chat Window** command. Best for multi-monitor setups.

## Interface overview

The {% data variables.copilot.chat_view %} keeps the agent next to your code, so you can prompt, review, and edit in the same window. The {% data variables.copilot.chat_view %} has the following main areas:

1. **Sessions list**: at the top of the view, where you can view and manage your sessions for the current workspace. Learn more about the [sessions list](<https://github.com/docs/agents/run/sessions/manage-sessions.md#sessions-list>).

1. **Chat conversation**: in the center, where you see the conversation history and the agent's responses, including the changes it makes to your code.

1. **Chat input**: at the bottom, where you type prompts and configure the session with the **Session Target**, **Agent**, **Language model**, and **Permissions** controls.

![Screenshot showing the {% data variables.copilot.chat_view %} with the sessions list, conversation, and chat input.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/chat-view-expanded.png>)

> [!TIP]
> To inspect a chat response with a keyboard or screen reader, run **Open Accessible View** (`kb(editor.action.accessibleView)`). Learn more about the [Accessible View](<https://github.com/docs/configure/accessibility/accessibility.md#accessible-view>).

The {% data variables.copilot.chat_view %} operates in two modes: compact and side-by-side. Use the toggle control in the top-right corner of the {% data variables.copilot.chat_view %} to switch between them. In compact mode, the sessions list and conversation share the same panel. In side-by-side mode, the sessions list stays visible next to the conversation. Learn more about [sessions list layout options](<https://github.com/docs/agents/run/sessions/manage-sessions.md#sessions-list>).

### Switch chats within a session

A supported Agent Host session can contain a main chat and additional peer chats. In the **Sessions** view, expand the main chat row and select a peer to open its conversation in the {% data variables.copilot.chat_view %}.

You can continue peer chats created in the {% data variables.copilot.agents_window %} without starting a new session. For creation steps, navigation options, and interface-specific controls, see [Run multiple chats in a session](<https://github.com/docs/agents/run/sessions/manage-sessions.md#run-multiple-chats-in-a-session>).

## Start a session

To start a workspace-scoped session, select **New Chat** (`+`) or press `kb(workbench.action.chat.newChat)`. Before you send the first prompt, use the chat input controls to choose a **Session Target**, **Agent**, **Language model**, and **Permissions**. For a first coding task, use **Copilot**, **Agent**, **Auto**, and **Manual permissions**, as shown in the [agents quickstart](<https://github.com/docs/agents/quickstart.md>). Available choices depend on your account, installed providers, and organization policies. Learn more about [configuring an agent session](<https://github.com/docs/agents/run/agent-harnesses.md#understand-the-session-controls>).

Type a prompt and press `kb(workbench.action.chat.submit)`. For shared request, context, and conversation controls, see [Use chat in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>). For session context, history, and organization, see [Manage agent sessions](<https://github.com/docs/agents/run/sessions/manage-sessions.md>).

## Work with the agent

Because the {% data variables.copilot.chat_view %} runs in the main {% data variables.product.prodname_vscode_shortname %} window, you can inspect agent edits in the editor, follow terminal activity, debug the application, and run tasks or tests without switching surfaces. The agent also has access to supported extensions and can [work with Jupyter notebooks](<https://github.com/docs/agents/guides/notebooks-with-ai.md>). When you connect to a [remote workspace](<https://github.com/docs/remote/remote-overview.md>), the session uses the same remote project context and tools.

The **Copilot** session target applies and saves edits directly to the selected folder or worktree. Select a changed file in the response to inspect its diff, or set `setting(chat.checkpoints.showFileChanges)` to show a changed-files summary after each request. For revisions, checkpoints, target-specific review workflows, and integrating changes, see [Review AI-generated code edits](<https://github.com/docs/agents/run/review-code-edits.md>).

## Next steps

* [Chat interaction controls](<https://github.com/docs/chat/chat-overview.md>) - send and steer requests, add context, and navigate a conversation.
* [Manage agent sessions](<https://github.com/docs/agents/run/sessions/manage-sessions.md>) - organize, archive, and fork sessions.
* [Use the {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) - work with agents across multiple projects.


</details>
