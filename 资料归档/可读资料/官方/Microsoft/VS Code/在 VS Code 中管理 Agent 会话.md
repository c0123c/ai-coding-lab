# 在 VS Code 中管理 Agent 会话

介绍上下文压缩、多聊天、会话分叉、归档、导出、工作树清理和跨应用查看，帮助组织持续开发任务。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/sessions/manage-sessions.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/sessions/manage-sessions.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/在 VS Code 中管理 Agent 会话.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 7a2e5f8d-4c9b-41e6-b3a8-9d7f2e4c1b8a
DateApproved: 10/7/2026
MetaDescription: Create, orchestrate, and manage agent sessions in {% data variables.product.prodname_vscode %}, including multiple chats, archiving, and forking.
MetaSocialImage: ../../../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 管理 {% data variables.product.prodname_vscode_shortname %} 中的 agent 会话

[会话](<https://github.com/docs/agents/concepts/sessions.md>)是{% data variables.product.prodname_vscode %}中agent的工作单元。它将工作区、代码更改以及一项任务的一个或多个聊天汇集在一起​​。每个聊天都有自己的对话历史记录和上下文。本文介绍如何跨 [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>) 和 [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) 管理这些对话并组织会话。

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Get started with agents">
按照实践教程使用AI agents在{% data variables.product.prodname_vscode_shortname %}中构建应用程序。

* [开始agentic编码教程](<https://github.com/docs/agents/agents-tutorial.md>)

</div>

## 开始 agent 会话

当您开始独立任务或需要不同的工作区或配置时，启动新会话。对于共享现有会话工作区和代码更改的新对话，[在该会话中开始另一个聊天](<#在一个会话中运行多个聊天>)（如果支持）。

选择与您的工作流程相匹配的表面：

* 使用 [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md#start-a-session>) 在编辑器旁边进行工作区范围的代码优先会话。
* 使用 [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md#start-an-agent-session>) 跨工作区启动和监控会话、附加相关项目或 GitHub 项目，或从现有拉取请求开始。在Windows上，您还可以通过右键单击任务栏中的{% data variables.product.prodname_vscode_shortname %}应用程序图标并选择**Agents窗口**来直接启动{% data variables.copilot.agents_window %}。

这些表面共享相同的基础会话，因此您可以在开始后在它们之间进行切换。要比较所有 agent 接口，请参阅 [使用 agents](<https://github.com/docs/agents/overview.md#ways-to-work-with-agents>) 的方法。要在会话开始后发送和引导请求，请参阅 [在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>) 中使用聊天。

## 管理会话上下文

聊天输入中的上下文窗口控件显示活动聊天正在使用模型的上下文窗口的大小。将鼠标悬停在控件上可查看令牌计数、按类别划分的使用情况明细以及会话消耗的总 AI 积分。

![{% data variables.product.prodname_vscode_shortname %} {% data variables.copilot.chat_view %} 的屏幕截图，显示聊天输入框中的上下文窗口使用控件。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/chat/images/copilot-chat/chat-context-window-control.png>)

随着对话的增长，控件会更新以反映不断增加的上下文使用情况。可用上下文取决于所选模型。

上下文如何在轮流中变化也会影响提示缓存。稳定的上下文允许模型提供者重用早期请求中的令牌，从而降低成本和延迟。使用[缓存浏览器](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>)检查您的缓存命中率。

### 紧凑的对话上下文

上下文压缩总结了早期的对话历史记录，以释放上下文窗口中的空间。压缩可以让您继续进行相同的聊天，减少不相关的历史记录，并减少后续请求发送的令牌。

{% data variables.product.prodname_vscode_shortname %} 当上下文窗口填满时自动压缩对话。要关闭自动压缩，请将 `setting(github.copilot.chat.summarizeAgentConversationHistory.enabled)` 设置为 `false`。

要手动压缩对话：

* 在聊天输入中输入 `/compact`。 （可选）添加摘要应保留的内容的说明，例如 `/compact focus on the database schema decisions`。
* 选择上下文窗口控件，然后选择**紧凑对话**。

手动压缩可用于本地、后台和 Claude agent 会话。要完全重置上下文，[启动一个新会话](<#开始-agent-会话>)。

了解更多关于[AI信用消费](<https://github.com/docs/agents/guides/optimize-usage.md>)。

## 在一个会话中运行多个聊天

在受支持的 Agent 主持人会话中，使用多个聊天来处理相关任务，而无需中断正在进行的对话。该会话有一个主聊天，并且可以包含其他交互式 ** 对等聊天 **。每个聊天都有自己的对话历史记录、标题和 agent 或语言模型选择，但所有聊天共享会话的工作区和工作树。

新聊天开始时是空白的，并且不会继承其他聊天的历史记录。要保留源对话的历史记录，请改为 [ 分叉对话 ](<#分叉聊天会话>)。详细了解 [ 选择聊天、分叉或新会话 ](<https://github.com/docs/agents/concepts/sessions.md#choose-a-new-chat-a-fork-or-a-new-session>)。

> [!NOTE]
> 会话中所有聊天的更改都会转到同一文件夹或工作树，并一起显示在会话更改中。当任务不得修改相同文件时，启动单独的 [ 工作树隔离会话 ](<https://github.com/docs/agents/run/agent-harnesses.md#choose-code-isolation>)。

{% tabs id="chat-surface" %}
{% 标签标签="{% data variables.copilot.agents_window %}" %}

在 {% data variables.copilot.agents_window %} 中，创建对等聊天并使用 `setting(sessions.showChatTabs)` 选择对话的显示方式：

* **多个**：将每个打开的聊天显示为选项卡。您可以按分组排列选项卡以并排监控对话。
* **单**：将活动聊天显示为会话视图。选择另一个对等聊天将替换取消固定的聊天窗格。您明确打开到侧面的聊天将保持可见，并且关闭最后一个对等聊天窗格不会重新打开主聊天。

要创建对等聊天：

1. 右键单击会话列表中的活动会话，然后选择**此会话中的新聊天**。您还可以从会话标题的 **...** 菜单中使用此操作，或者在会话具有焦点时按 `kb(sessions.chatCompositeBar.addChat)`。

    <!-- TODO: Replace the outdated agents-window-new-subsession.png screenshot with one showing New Chat in This Session in the session context menu. -->

将打开一个空白聊天。当会话有多个聊天时，聊天区域中会出现一个选项卡栏。新聊天还会显示在会话列表中其所属会话下方。

1. 输入提示并按 `kbstyle(Enter)` 开始聊天。

该操作需要支持多个聊天的线束。它不可用于快速聊天或存档会话。

在 **Multiple** 演示文稿中，使用聊天选项卡及其上下文菜单可以：

* **切换聊天**：选择一个选项卡以显示其对话。进度和未读指示器适用于该聊天。
* **选择agent或模型**：在每个聊天中使用控件。兄弟姐妹聊天可以使用不同的 agents 或模型。
* **跟踪更改**：当任何聊天正在进行时，会话仍在进行中。会话标题 **更改** 丸合并了所有聊天的编辑。
* **重命名聊天**：从选项卡的上下文菜单中选择**重命名**。聊天标题独立于会话标题。
* **关闭或重新打开聊天**：关闭选项卡以隐藏它而不删除它。使用 ** 聊天 ** 下拉菜单可显示或隐藏聊天，或按 `kb(sessions.chatCompositeBar.reopenLastClosedChat)` 重新打开最近关闭的聊天。
* **删除聊天**：从选项卡的上下文菜单中选择**删除聊天**，或者在聊天获得焦点时按`kb(sessions.chatCompositeBar.deleteChat)`。删除是永久性的。

要将聊天安排在多个组中：

* 将聊天选项卡拖到另一个组的中心以将其移动到该组中。
* 将聊天选项卡拖动到组的左侧、右侧、顶部或底部边缘，以沿该方向创建组。
* 运行 **会话：向右拆分聊天组**、**会话：向下拆分聊天组**、**会话：将聊天移至上一个组**或**会话：将聊天移至命令面板 (`kb(workbench.action.showCommands)`) 中的下一个组 ** 用于通过键盘安排聊天。
* 按 `kb(sessions.focusPreviousChatGroup)` 或 `kb(sessions.focusNextChatGroup)` 在组之间移动键盘焦点。

当您关闭或移动组中的最后一个聊天时，空组将关闭，其余组将展开以填充可用空间。

当您重新加载窗口并重新打开会话时，可见和隐藏的聊天（包括其对话历史记录）将被恢复。当您切换会话或重新加载窗口时，聊天组分配、活动聊天和分割大小也会恢复。

{% /tab %}
{% 标签标签="{% data variables.copilot.chat_view %}" %}

在编辑器窗口中，使用 **Sessions** 视图继续主聊天或代码旁边的对等聊天：

1. 打开 {% data variables.copilot.chat_view %} 中现有的 Agent 主机会话。如果您从 {% data variables.copilot.agents_window %} 开始，请选择标题栏中的 **在编辑器中打开 **，以在编辑器窗口中打开会话的工作区。

1. 使用并排布局在 {% data variables.copilot.chat_view %} 旁边显示 [ 会话列表 ](<#会议列表>)。

1. 展开主聊天行以显示其下方的同行聊天。

1. 选择一个同伴聊天以在现有的 {% data variables.copilot.chat_view %} 中打开其对话。选择主聊天行以返回主对话。

当您切换聊天时，每个聊天都会保留自己的消息和未发送的草稿。返回聊天会恢复草稿，以便您可以从上次中断的地方继续。

要在编辑器选项卡中打开对等聊天，请右键单击其行并选择 **作为编辑器打开**。要在另一个编辑器组中打开它，请选择**打开到侧面**。

<!-- TODO: Add a screenshot showing the editor's Sessions view with an expanded main chat, two nested peer chats, and the selected peer's conversation in the Chat view. -->

编辑器的嵌套列表不包括 [ 旁聊 ](<#问一些附带问题>) 或 [ 只读子代理聊天 ](<https://github.com/docs/agents/run/subagents.md#agents-window>)。使用{% data variables.copilot.agents_window %}查看这些聊天并将它们排列在聊天组中。

{% /tab %}
{% /tabs %}

要练习分离和监控独立工作，请遵循 [委派两项任务而不混合其更改](<https://github.com/docs/agents/guides/delegate-two-tasks.md>)。

## 问一些附带问题

使用副聊天询问有关当前对话的问题，而无需将问题或回复添加到主聊天中。边聊天会在源聊天旁边的组中打开，因此两个对话都保持可见。它私下继承源对话作为上下文。旁聊倾向于解释而不是行动，除非您要求 agent 进行更改或执行任务。

通过以下方式之一开始侧聊：

* 在聊天输入中输入 `/btw <question>`。侧聊从最新的回合开始分支，包括仍在进行中的响应。
* 选择聊天回复中的文本，在 ** 提问 ** 输入中输入问题，然后按 `kbstyle(Enter)`。所选文本及其响应成为侧聊的上下文。

每个问题都会创建一个新的侧聊。侧边聊天继承了源聊天的 agent 和语言模型，但继承的消息在其记录中仍然隐藏。

![显示如何从选定的响应文本在 {% data variables.copilot.agents_window %} 中启动侧聊的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-side-chat.png>)

> [!NOTE]
> 私聊仅在 {% data variables.copilot.agents_window %} 中的 Copilot 和 Claude 会话中可用。它们不适用于 Codex 会话或 {% data variables.copilot.chat_view %}。

## 会议列表

会话列表是您管理所有聊天会话的中心枢纽，无论您在何处启动它们或它们在何处运行。会话列表显示您的会话及其状态、类型和文件更改的信息。

![会话列表的屏幕截图，显示具有不同状态、类型和文件更改统计信息的多个会话。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-sessions/chat-view-sessions-list.png>)

将鼠标悬停在会话上可查看固定或 [ 归档 ](<#存档会议>) 的操作。右键单击列表中的会话可查看其他操作，例如删除或更改会话的状态。某些操作特定于会话的工具和状态。例如，您可以查看云会话的拉取请求。

使用固定操作可以将重要的会话保留在列表顶部，以便轻松访问。无论其活动或状态如何，固定会话都会保留在列表顶部，因此您可以快速找到并返回它们。

{% tabs id="chat-surface" %}
{% 标签标签="{% data variables.copilot.agents_window %}" %}

在**{% data variables.copilot.agents_window %}**中，会话列表位于左侧边栏中。它显示来自所有工作区的会话，因此您可以从一个位置监控跨项目的工作。每个会话项都会显示关键信息，例如会话名称、工作区、工具和文件更改统计信息。

![{% data variables.copilot.agents_window %} 中的会话列表的屏幕截图，显示具有不同工具和文件更改统计信息的多个会话。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-sessions/agents-window-sessions-list.png>)

默认情况下，列表被过滤为仅显示活动会话。您可以更改过滤器以显示不同状态的会话，例如已完成或已存档。

默认情况下，会话按工作区分组，您可以将分组切换为按时间范围组织。在{% data variables.copilot.agents_window %}中，您还可以创建自定义组以将相关会话保留在一起。当您想要减少会话列表时，请折叠组标题。

要组织 {% data variables.copilot.agents_window %} 中的会话列表：

1. 从会话列表控件创建自定义组。

1. 将一个或多个会话拖到组中。插入线显示会话的着陆位置。

1. 将鼠标悬停在组标题上可在该组中启动新会话，或将组中的所有会话标记为已完成。

您还可以向上或向下拖动会话以重新排序，拖动组和工作区标题以重新排列部分，或将会话放在 ** 固定 ** 部分上以固定它。选择多个会话将它们移动到一起。

您可以通过选择{% data variables.copilot.agents_window %}左上角的**切换侧边栏**按钮或使用`kb(workbench.action.toggleSidebarVisibility)`键盘快捷键来隐藏左侧边栏。

{% /tab %}
{% 标签标签="{% data variables.copilot.chat_view %}" %}

在 **{% data variables.copilot.chat_view %}** 中，会话列表的范围为您当前的工作区，并按时间段对会话进行分组，例如 **今天** 或 **上周**。如果您没有打开工作区，该列表将显示工作区中的所有会话。

默认情况下，列表被过滤为仅显示活动会话。您可以更改过滤器以显示不同状态的会话，例如已完成或已存档。

要在 {% data variables.copilot.chat_view %} 中隐藏会话列表，请右键单击空聊天并取消选择 ** 显示会话 ** 或使用 `setting(chat.viewSessions.enabled)` 设置。

{% data variables.copilot.chat_view %} 以两种模式运行：紧凑型和并排型。您可以使用 {% data variables.copilot.chat_view %} 右上角的切换控件在紧凑模式和并排模式之间手动切换。

* **紧凑**：会话列表嵌入在{% data variables.copilot.chat_view %}中。当您从列表中选择一个会话时，{% data variables.copilot.chat_view %} 会切换到该会话。使用后退按钮返回到会话列表。

* **并排**：会话列表与{% data variables.copilot.chat_view %}并排显示。从列表中选择一个会话，即可在{% data variables.copilot.chat_view %}中查看其详细信息。您可以使用 `setting(chat.viewSessions.orientation)` 设置进一步配置方向。

对于支持的 Agent 主持人会话，展开主聊天行以查看其对等聊天。选择一个对等点会打开该对话，而不是一个单独的会话。有关工作流程和界面之间的差异，请参阅[在会话中运行多个聊天](<#在一个会话中运行多个聊天>)。

> [！NOTE]
> 扩展开发人员可以通过使用建议的API [`chatSessionsProvider`](<https://github.com/microsoft/vscode/blob/main/src/vscode-dts/vscode.proposed.chatSessionsProvider.d.ts>)来学习如何与会话视图集成。 API 目前处于提议状态，可能会发生变化。

{% /tab %}
{% /tabs %}

### 查看来自其他应用程序的会话

{% data variables.product.prodname_vscode_shortname %} 可以发现 {% data variables.product.prodname_vscode_shortname %} 外部受支持的应用程序创建的本地 agent 会话。您可以从{% data variables.copilot.chat_view %}或{% data variables.copilot.agents_window %}中的{% data variables.copilot.copilot_cli_short %}、{% data variables.copilot.github_copilot_app %}、Claude Code和Codex打开并继续会话。

对于 Copilot 会话，发现包括与存储库关联并在过去 7 天内更新的会话。

默认情况下，其他应用程序中创建的会话是隐藏的。要控制显示哪些会话，请打开会话列表过滤器，选择 ** 外部 **，然后选择以下选项之一：

* **无**：隐藏所有外部会话。
* **最近**：显示过去 7 天内最近更新的两个外部会话。
* **过去 24 小时**：显示过去 24 小时内更新的外部会话。
* **过去 7 天**：显示过去 7 天内更新的外部会话。
* **全部**：显示所有发现的外部会话。

该过滤器适用于 {% data variables.copilot.chat_view %} 和 {% data variables.copilot.agents_window %} 中的会话列表。您还可以使用 `setting(chat.agentSessions.showExternal)` 设置进行配置。

当您在 {% data variables.copilot.agents_window %} 中打开外部会话时，一次性横幅指示该会话是在另一个应用程序中创建的。您可以从横幅中选择要显示的外部会话。如果您选择隐藏打开的会话，{% data variables.product.prodname_vscode_shortname %} 会要求您确认更改。

当您打开外部 Codex 会话时，一次只有一个应用程序可以写入聊天。如果 ChatGPT 或 Codex CLI 中的聊天仍处于打开状态，则 {% data variables.product.prodname_vscode_shortname %} 会保留文字记录、草稿和附件可用，但会禁用发送、排队和重新发送。 **此聊天已在另一个应用程序中打开** 横幅出现在聊天输入上方。

要继续在 {% data variables.product.prodname_vscode_shortname %} 中聊天，请完全退出其他应用程序，然后在横幅中选择 **重试 **。重试会检查聊天是否可用，而无需发送草稿或添加记录条目。横幅消失后，您自己发送草稿。

当您在外部会话中发送消息时，Agent 主机会采用该消息。该会话不再是外部的，因此 **External** 过滤器不再影响其可见性。

## 存档会议

要保持会话列表井井有条，请在会话完成或不再需要它们时将其存档或标记为已完成。存档会话不会将其删除。您可以随时取消存档会话以将其恢复到活动会话列表中。

当您存档（或标记为完成）会话时，其状态会发生变化，因此它会从活动会话列表中移出。对于工作树会话，{% data variables.product.prodname_vscode_shortname %} 在删除工作树文件夹之前将未提交的更改提交到会话分支。如果 {% data variables.product.prodname_vscode_shortname %} 无法保留更改或删除工作树，则工作树将保留。分支及其提交会被保留，因此恢复会话会从该分支重新创建工作树。

对于 [Dev 容器会话 ](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>)，将会话标记为完成会删除容器，而无需等待空闲关闭时间段，但仅当没有其他会话或未发送的草稿使用它时。恢复会话会启动替换容器并保留对话历史记录。

要存档会话，请将鼠标悬停在会话列表中的会话上，然后选择 ** 存档 ** ({% data variables.copilot.chat_view %}) 或 ** 标记为完成 ** ({% data variables.copilot.agents_window %}) 选项。

![在会话视图中存档 agent 会话的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/agent-sessions-archive-v2.png>)

要查看存档的会话，请使用会话列表中的筛选器选项，然后选择 **Archived** ({% data variables.copilot.chat_view %}) 或 **Done** ({% data variables.copilot.agents_window %}) 筛选器。

### 将个人聊天标记为已完成

在 {% data variables.copilot.agents_window %} 中，您可以将 Agent 主机会话中的附加聊天标记为已完成，而不影响主聊天、其他聊天或所属会话：

1. 展开会话列表中的所属会话。

1. 右键单击聊天，然后选择**标记为完成**。

将聊天标记为完成会将其从活动会话列表中隐藏并关闭其选项卡，但不会将其删除。重新加载窗口后，聊天仍保持完成，并且其标题和记录将被保留。

要查找已完成的聊天，请在全局会话列表中选择 **已完成** 过滤器。或者，右键单击所属会话并选择 **显示已完成的聊天 ** 以在其下方显示其已完成的聊天。右键单击已完成的聊天并选择**恢复**。聊天将再次激活并可用，其标题和文字记录完好无损，因此您可以继续发送消息。

您无法将主聊天、副聊天或子代理聊天标记为独立完成。

## 删除会话

要永久删除会话，请右键单击会话列表中的会话，然后选择 **删除**。删除会话会将其永久删除且无法撤消。对于 [Copilot 会话 ](<https://github.com/docs/agents/run/agent-harnesses.md#copilot>)，删除会话还会删除为该会话创建的任何关联工作树。

如果多个 Copilot 会话共享同一工作树，例如在分叉会话后，删除一个会话不会删除共享工作树，而另一个会话仍在使用它。仅当删除或归档最后一个链接会话后，才会删除工作树。

对于 [Dev 容器会话 ](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>)，仅当没有其他会话或未发送的草稿使用该容器时，删除该会话才会删除该容器。当自动怠速关闭关闭时，此清理也适用。

> [!CAUTION]
> 删除会话是不可逆的。在删除会话之前集成或提交工作树更改，因为仅存在于已删除工作树中的未提交文件可能会丢失。如果您只想隐藏会话，请[存档](<#存档会议>)它。

## 清理会话工作树

工作树隔离的 agent 会话可能会累积并使用大量磁盘空间。运行 **Chat：打开工作树清理 ** 以查看非活动会话工作树并删除不再需要的工作树。

<!-- TODO: Add a screenshot showing the worktree cleanup editor with the storage summary, inactivity filter, and selectable sessions. -->

在清理编辑器中：

1. 选择会话必须处于非活动状态的时间长度。您可以筛选在 7、15、30、60 或 90 天不活动的会话。

1. 查看每个工作树的估计大小。默认情况下会选择符合条件的工作树。清除您想要保留的所有工作树。

1. 确认所选工作树的清理。

清理将每个选定的会话标记为已完成，并安排其工作树进行删除。在删除工作树之前，{% data variables.product.prodname_vscode_shortname %} 将未提交的更改提交到会话分支。如果无法保留更改或无法删除工作树，则工作树将保留。会话及其分支历史记录将被保留，因此恢复会话将重新创建工作树。

清理编辑器排除活动、正在运行、等待输入、固定、完成、存档、无标题、处于错误状态或比所选非活动时间段更新的会话。它还排除了无法测量大小的工作树。

### 获取存储清理建议（实验）

设置 `setting(chat.agentSessions.sessionStorageCleanupSuggestion.enabled)` 以在非活动工作树至少使用 5、GiB 或 20 工作树符合清理条件时在 {% data variables.copilot.agents_window %} 中接收建议。选择建议将打开工作树清理编辑器。

忽略该建议会将其隐藏在当前窗口中。要关闭未来的建议，请在建议中选择 **不再显示** 或运行 **聊天：禁用会话存储清理建议**。

### 自动清理合并的会话

`feature(automatic-session-cleanup)`

配置自动清理以防止不活动的 Agent 主机会话在拉取请求合并后累积。默认情况下，这两个设置均处于禁用状态：

* `setting(chat.agentSessions.autoMarkAsDoneMergedSessionsAfterDays)` 控制合格会话自动标记为完成之前经过多少非活动天数。
* `setting(chat.agentSessions.autoDeleteMarkedAsDoneMergedSessionsAfterDays)` 控制自动将符合条件的会话标记为已完成和永久删除它之间的单独宽限期。

将每个设置设置为正整数天数。推荐值为`15`。将设置设置为 `0` 以禁用清理生命周期的该部分。

当会话未进行、其上次修改时间超过配置的阈值、至少有一个合并的拉取请求并且没有任何相关拉取请求打开时，该会话符合资格。外部会议不符合资格。 {% data variables.product.prodname_vscode_shortname %} 在启动时以及启用任一设置时每小时检查一次符合条件的会话。

永久删除仅适用于 {% data variables.product.prodname_vscode_shortname %} 自动标记为已完成的会话。您手动标记为完成的会话不会自动删除。恢复自动完成的会话将清除其删除资格。

当清理将会话标记为完成或删除它时，仅当分支跟踪上游并且没有传出提交或未提交的更改时，{% data variables.product.prodname_vscode_shortname %} 才会删除会话工作树。如果 Git 状态未知或工作树不满足这些条件，则保留工作树。清理永远不会强制删除工作树。

当合并的拉取请求使会话符合条件时，请从 ** 标记为完成 ** 建议中选择 ** 配置自动清理 ** 以打开这两个设置而不启用它们。

## 分叉聊天会话

分叉聊天会话会分支对话并继承原始会话的对话历史记录。在单聊天会话和不使用 agent 主机的会话中，分叉将作为新的独立会话打开。对话是独立的，但仅当分支使用不同的文件夹或工作树时，其代码更改才会被隔离。新的会话标题带有“Forked:”前缀，以帮助您识别它。

对于 {% data variables.copilot.agents_window %} 中的多聊天 [Copilot](<https://github.com/docs/agents/run/agent-harnesses.md#use-the-copilot-harness>) 会话，分叉将在同一会话中作为对等聊天打开。对等聊天会自动生成标题，并独立于兄弟聊天运行。

对于使用工作树隔离的 Copilot 会话，分叉继续使用与原始会话相同的工作树。

当您想要探索替代方法、提出附带问题或将长对话向不同方向分支而不丢失原始上下文时，分叉非常有用。

有两种方法可以分叉聊天会话：

* **分叉整个会话**：在聊天输入框中输入`/fork`，然后按`kbstyle(Enter)`。该分支将打开并显示从当前会话复制的完整对话历史记录。

* **从检查点分叉**：将鼠标悬停在对话中的聊天请求上，然后选择**分叉对话**按钮。分叉仅包含直到并包括该检查点的请求。

![{% data variables.copilot.chat_view %} 中检查点工具栏中的 Fork Conversation 按钮的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-checkpoints/chat-fork-conversation.png>)

> [！TIP]
> 分叉会话继承原始会话历史记录，从而保留提示缓存并降低下一个请求的成本。使用 [Cache Explorer](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>) 比较跨会话的缓存命中率。

## 从 agent 主机会话编排会话

在 agent 主机会话中，例如 [Copilot](<https://github.com/docs/agents/run/agent-harnesses.md#use-the-copilot-harness>) 和 Claude，agents 可以使用内置会话管理工具来协调多个会话和聊天的工作。当 Codex [ 在 Agent 主机 ](<https://github.com/docs/agents/concepts/agent-host.md#process-architecture>) 上运行时，这些工具也可用于 Codex 会话。

借助这些工具，agent 可以：

* 列出您的会话并检查状态、工作区和文件更改等元数据。
* 为子任务创建新会话，或在现有会话中创建新聊天。
* 在继续工作之前，请阅读另一个会话中最近的对话上下文。
* 向另一个会话或聊天发送消息以启动或引导后续任务。

先选择会话关系，然后选择工作空间和代码隔离：

* **相关工作**：使用`currentSession`在当前会话中创建对等聊天。同行聊天共享当前工作区和结账，因此使用此选项进行相关研究、规划或其他不需要单独文件更改的工作。
* **不相关的工作或单独的可交付成果**：使用`independent`创建单独的会话。当任务不需要存储库文件时省略工作区。如果是，请选择受信任的文件夹或工作树。当文件更改必须与当前结帐隔离时，请求新的工作树。

在提示中，说明会话关系和工作区选择。对于需要当前存储库上下文的相关只读工作：

```prompt
Create a peer chat in this session that reuses the current workspace and checkout to review the authentication flow. Do not modify files.
```

对于不需要存储库文件的无关工作：

```prompt
Create an independent session with no workspace to research licensing options for a separate project.
```

当任务需要存储库文件时，您可以通过项目名称引用工作区，而不是提供绝对路径或工作区 URI。例如：

```prompt
Create an independent session in the vscode workspace with a new worktree to implement and test the authentication change.
```

如果多个工作区具有相同的项目名称，agent 会报告可能的匹配项，而不是选择一个。会话管理工具还支持远程工作区 URI 并保留项目 URI 和多根工作区的工作目录。

当工具创建或定位会话时，{% data variables.product.prodname_vscode_shortname %} 会在聊天中显示 ** 打开会话 ** 药丸，以便您可以直接跳转到它。

为了保持此工作流程的安全性和可预测性：

* 向另一个会话发送消息始终需要您的确认。
* Agents 无法将消息发送到当前正在运行的同一个聊天室。
* 突发发送受到限制以避免无限制的扇出。
* 除非明确要求，否则存档的会话不包含在列表中。

`setting(chat.agentHost.agentOrchestrationLimits)` 控制 agents 创建的会话和聊天、它们发送的消息以及递归会话创建的进程范围限制。默认 `on` 值强制执行支持协调繁重工作流程的限制。将其设置为 `off` 以删除限制。更改无需重新启动 Agent 主机即可应用，并且达到限制不会中断已在运行的工作。

删除编排限制不会改变确认要求或输入验证。本节中的安全指南继续适用。

## 保存并导出聊天会话

您可以保存聊天会话以保留重要的对话，或稍后重复使用它们来执行类似的任务。

### 将聊天会话导出为 JSON 文件

您可以导出聊天会话以保存供以后参考或与其他人共享。导出聊天会话会创建一个 JSON 文件，其中包含会话中的所有提示和响应。

导出聊天会话：

1. 在{% data variables.copilot.chat_view %}中打开要导出的聊天会话。

1. 从命令面板 (`kb(workbench.action.showCommands)`) 运行 **聊天：导出聊天...** 命令。

1. 选择保存 JSON 文件的位置。

### 将聊天消息复制为 Markdown

{% data variables.copilot.chat_view %} 支持将聊天消息作为 Markdown 复制到剪贴板的不同选项，当您右键单击消息或聊天背景时，可以通过上下文菜单使用这些选项。

* **复制**：将单个提示或响应复制到剪贴板 - Markdown 包含响应文本、思考步骤和工具调用。

* **全部复制**：以Markdown格式复制整个聊天会话，包括所有提示、回复、思考步骤和工具调用。

* **复制最终响应**：在上次工具调用后仅复制agent响应的最终Markdown部分。这对于共享或重用最终输出而不需要中间步骤非常有用。

## 从应用程序图标监视会话（预览）

应用程序图标可以显示{% data variables.copilot.agents_window %}中有多少未存档的会话需要您关注。该徽章出现在 macOS 上的停靠栏图标、Linux 上的启动器图标或 Windows 上的任务栏图标上。

![屏幕截图显示 Windows 任务栏中徽章计数为 1 的 {% data variables.product.prodname_vscode_shortname %} 应用程序图标。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/application-icon-session-badge.png>)

徽章对以下会话进行计数：

* 有未读结果且不再进行中。
* 正在等待您的输入。
* 对开放的非草稿拉取请求的 CI 检查失败，并且不再进行中。

不包括仍在进行中的未读会话。计数随着会话状态的改变而更新。当会话不再需要您的关注时，读取或存档会话会将其从计数中删除。

使用 `setting(sessions.showApplicationBadge)` 设置来控制应用程序徽章。该设置在 Insiders 中默认为 `true`，在 Stable 中默认为 `false`。

## 会话状态指示器（实验）

会话状态指示器可让您直接从标题栏中的命令中心快速访问会话。该指示器显示未读消息和正在进行的会话的可视徽章，因此您无需切换视图即可随时了解 AI 活动。

![显示命令中心中的会话状态指示器的屏幕截图，带有未读和正在进行的徽章。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/agent-status-indicator-v2.png>)

指标显示：

* **未读会话徽章**：显示包含新消息的聊天会话计数。选择徽章以过滤会话列表以仅显示未读会话。
* **正在进行的会话徽章**：显示正在运行的会话数agents。选择徽章以过滤会话列表以仅显示正在进行的会话。
* **闪光图标**：提供对聊天和会话管理选项的快速访问。

您可以使用 `setting(chat.agentsControl.enabled)` 设置来配置状态指示器的可见性。

## 在 {% data variables.product.prodname_vscode_shortname %} 欢迎页面上查看会话

{% data variables.product.prodname_vscode_shortname %} 欢迎页面可以作为您使用聊天会话的启动体验。它提供对最近聊天会话的快速访问、用于启动新任务的嵌入式聊天小部件以及常见任务的快速操作。

要将 {% data variables.product.prodname_vscode_shortname %} 欢迎页面配置为启动体验，请将 `setting(workbench.startupEditor)` 设置为 `agentSessionsWelcomePage`。

## 相关资源

* [在{% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>)中使用聊天
* [Agents概述](<https://github.com/docs/agents/overview.md>)
* [添加聊天上下文](<https://github.com/docs/chat/copilot-chat-context.md>)
* [使用AI](<https://github.com/docs/agents/best-practices.md>)的最佳实践


## 原文对照

<details>
<summary>展开完整原文</summary>

# Manage agent sessions in {% data variables.product.prodname_vscode_shortname %}

A [session](<https://github.com/docs/agents/concepts/sessions.md>) is the unit of work with an agent in {% data variables.product.prodname_vscode %}. It brings together the workspace, code changes, and one or more chats for a task. Each chat has its own conversation history and context. This article describes how to manage these conversations and organize sessions across the [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>) and the [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>).

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Get started with agents">
Follow a hands-on tutorial to build an app with AI agents in {% data variables.product.prodname_vscode_shortname %}.

* [Start agentic coding tutorial](<https://github.com/docs/agents/agents-tutorial.md>)

</div>

## Start an agent session

Start a new session when you begin an independent task or need a different workspace or configuration. For a fresh conversation that shares an existing session's workspace and code changes, [start another chat in that session](<#run-multiple-chats-in-a-session>), where supported.

Choose the surface that matches your workflow:

* Use the [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md#start-a-session>) for a workspace-scoped, code-first session beside the editor.
* Use the [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md#start-an-agent-session>) to start and monitor sessions across workspaces, attach related projects or GitHub items, or start from an existing pull request. On Windows, you can also launch the {% data variables.copilot.agents_window %} directly by right-clicking the {% data variables.product.prodname_vscode_shortname %} application icon in the taskbar and selecting **Agents Window**.

The surfaces share the same underlying sessions, so you can switch between them after you start. To compare all agent interfaces, see [Ways to work with agents](<https://github.com/docs/agents/overview.md#ways-to-work-with-agents>). To send and steer requests after a session starts, see [Use chat in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>).

## Manage session context

The context window control in the chat input shows how much of the model's context window the active chat is using. Hover over the control to see the token count, a usage breakdown by category, and the total AI credits consumed by the session.

![Screenshot of {% data variables.product.prodname_vscode_shortname %} {% data variables.copilot.chat_view %}, showing the context window usage control in the chat input box.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/chat/images/copilot-chat/chat-context-window-control.png>)

As the conversation grows, the control updates to reflect increasing context usage. The available context depends on the selected model.

How context changes across turns also affects prompt caching. Stable context lets the model provider reuse tokens from earlier requests, which lowers cost and latency. Use the [Cache Explorer](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>) to check your cache hit rate.

### Compact conversation context

Context compaction summarizes earlier conversation history to free space in the context window. Compaction lets you continue the same chat with less irrelevant history and reduces the tokens sent with subsequent requests.

{% data variables.product.prodname_vscode_shortname %} automatically compacts the conversation when the context window fills. To turn off automatic compaction, set `setting(github.copilot.chat.summarizeAgentConversationHistory.enabled)` to `false`.

To compact the conversation manually:

* Type `/compact` in the chat input. Optionally, add instructions for what the summary should retain, for example `/compact focus on the database schema decisions`.
* Select the context window control, and then select **Compact Conversation**.

Manual compaction is available for local, background, and Claude agent sessions. To reset the context entirely, [start a new session](<#start-an-agent-session>).

Learn more about [AI credit consumption](<https://github.com/docs/agents/guides/optimize-usage.md>).

## Run multiple chats in a session

In a supported Agent Host session, use multiple chats to work on related tasks without interrupting an ongoing conversation. The session has a main chat and can contain additional interactive **peer chats**. Each chat has its own conversation history, title, and agent or language model selection, but all chats share the session's workspace and worktree.

A new chat starts blank and doesn't inherit the history of the other chats. To retain the source conversation's history, [fork the conversation](<#fork-a-chat-session>) instead. Learn more about [choosing a chat, a fork, or a new session](<https://github.com/docs/agents/concepts/sessions.md#choose-a-new-chat-a-fork-or-a-new-session>).

> [!NOTE]
> Changes from all chats in a session go to the same folder or worktree and appear together in the session changes. Start separate [worktree-isolated sessions](<https://github.com/docs/agents/run/agent-harnesses.md#choose-code-isolation>) when tasks must not modify the same files.

{% tabs id="chat-surface" %}
{% tab label="{% data variables.copilot.agents_window %}" %}

In the {% data variables.copilot.agents_window %}, create peer chats and use `setting(sessions.showChatTabs)` to choose how conversations appear:

* **Multiple**: Show each open chat as a tab. You can arrange tabs in split groups to monitor conversations side by side.
* **Single**: Show the active chat as the session view. Selecting another peer chat replaces the unpinned chat pane. A chat that you explicitly open to the side stays visible, and closing the last peer chat pane doesn't reopen the main chat.

To create a peer chat:

1. Right-click an active session in the sessions list and select **New Chat in This Session**. You can also use this action from the session header's **...** menu, or press `kb(sessions.chatCompositeBar.addChat)` while the session has focus.

    <!-- TODO: Replace the outdated agents-window-new-subsession.png screenshot with one showing New Chat in This Session in the session context menu. -->

    A blank chat opens. When the session has more than one chat, a tab strip appears in the chat area. The new chat also appears beneath its owning session in the sessions list.

1. Type a prompt and press `kbstyle(Enter)` to start the chat.

The action requires a harness that supports multiple chats. It isn't available for quick chats or archived sessions.

In the **Multiple** presentation, use the chat tabs and their context menus to:

* **Switch chats**: select a tab to show its conversation. Progress and unread indicators apply to that chat.
* **Choose an agent or model**: use the controls in each chat. Sibling chats can use different agents or models.
* **Track changes**: the session remains in progress while any chat is working. The session header **Changes** pill combines edits from all chats.
* **Rename a chat**: select **Rename** from the tab's context menu. Chat titles are independent of the session title.
* **Close or reopen a chat**: close a tab to hide it without deleting it. Use the **Chats** dropdown to show or hide chats, or press `kb(sessions.chatCompositeBar.reopenLastClosedChat)` to reopen the most recently closed chat.
* **Delete a chat**: select **Delete Chat** from the tab's context menu, or press `kb(sessions.chatCompositeBar.deleteChat)` while the chat has focus. Deletion is permanent.

To arrange chats in multiple groups:

* Drag a chat tab to the center of another group to move it into that group.
* Drag a chat tab to the left, right, top, or bottom edge of a group to create a group in that direction.
* Run **Sessions: Split Chat Group Right**, **Sessions: Split Chat Group Down**, **Sessions: Move Chat to Previous Group**, or **Sessions: Move Chat to Next Group** from the Command Palette (`kb(workbench.action.showCommands)`) to arrange chats with the keyboard.
* Press `kb(sessions.focusPreviousChatGroup)` or `kb(sessions.focusNextChatGroup)` to move keyboard focus between groups.

When you close or move the last chat in a group, the empty group closes and the remaining groups expand to fill the available space.

Visible and hidden chats, including their conversation history, are restored when you reload the window and reopen the session. Chat group assignments, active chats, and split sizes are also restored when you switch sessions or reload the window.

{% /tab %}
{% tab label="{% data variables.copilot.chat_view %}" %}

In the editor window, use the **Sessions** view to continue the main chat or a peer chat beside your code:

1. Open the existing Agent Host session in the {% data variables.copilot.chat_view %}. If you started in the {% data variables.copilot.agents_window %}, select **Open in Editor** in its title bar to open the session's workspace in an editor window.

1. Show the [sessions list](<#sessions-list>) beside the {% data variables.copilot.chat_view %} by using the side-by-side layout.

1. Expand the main chat row to show its peer chats beneath it.

1. Select a peer chat to open its conversation in the existing {% data variables.copilot.chat_view %}. Select the main chat row to return to the main conversation.

When you switch chats, each chat keeps its own messages and unsent draft. Returning to a chat restores its draft so you can continue where you left off.

To open a peer chat in an editor tab, right-click its row and select **Open as Editor**. To open it in another editor group, select **Open to the Side**.

<!-- TODO: Add a screenshot showing the editor's Sessions view with an expanded main chat, two nested peer chats, and the selected peer's conversation in the Chat view. -->

The editor's nested list doesn't include [side chats](<#ask-side-questions>) or [read-only subagent chats](<https://github.com/docs/agents/run/subagents.md#agents-window>). Use the {% data variables.copilot.agents_window %} to view those chats and arrange them in chat groups.

{% /tab %}
{% /tabs %}

To practice separating and monitoring independent work, follow [Delegate two tasks without mixing their changes](<https://github.com/docs/agents/guides/delegate-two-tasks.md>).

## Ask side questions

Use a side chat to ask a question about the current conversation without adding the question or response to the main chat. A side chat opens in a group beside the source chat, so both conversations remain visible. It privately inherits the source conversation as context. Side chats favor explanation over action unless you ask the agent to make changes or perform a task.

Start a side chat in one of these ways:

* Type `/btw <question>` in the chat input. The side chat branches from the latest turn, including a response that is still in progress.
* Select text in a chat response, enter a question in the **Ask Question** input, and press `kbstyle(Enter)`. The selected text and its response become context for the side chat.

Each question creates a new side chat. The side chat inherits the agent and language model from the source chat, but inherited messages remain hidden from its transcript.

![Screenshot showing how to start a side chat in the {% data variables.copilot.agents_window %} from selected response text.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-side-chat.png>)

> [!NOTE]
> Side chats are available only in the {% data variables.copilot.agents_window %} for Copilot and Claude sessions. They aren't available for Codex sessions or in the {% data variables.copilot.chat_view %}.

## Sessions list

The sessions list is your central hub for managing all your chat sessions, regardless of where you started them or where they are running. The sessions list shows your sessions with information about their status, type, and file changes.

![Screenshot of the sessions list showing multiple sessions with different statuses, types, and file change stats.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-sessions/chat-view-sessions-list.png>)

Hover over a session to see actions for pinning or [archiving](<#archive-sessions>) it. Right-click a session in the list to see additional actions like deleting or changing the session's state. Some actions are specific to the session's harness and state. For example, you can check out a pull request for a cloud session.

Use the pinning action to keep important sessions easily accessible at the top of your list. Pinned sessions stay at the top of the list regardless of their activity or state, so you can quickly find and return to them.

{% tabs id="chat-surface" %}
{% tab label="{% data variables.copilot.agents_window %}" %}

In the **{% data variables.copilot.agents_window %}**, the sessions list is located in the left sidebar. It shows sessions from all your workspaces, so you can monitor work across projects from a single place. Each session item surfaces key information such as session name, workspace, harness, and file change stats.

![Screenshot of the sessions list in the {% data variables.copilot.agents_window %}, showing multiple sessions with different harnesses and file change stats.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-sessions/agents-window-sessions-list.png>)

By default, the list is filtered to only show active sessions. You can change the filter to show sessions of different states, such as completed or archived.

Sessions are grouped by workspace by default, and you can switch the grouping to organize by timeframe instead. In the {% data variables.copilot.agents_window %}, you can also create custom groups to keep related sessions together. Collapse a group header when you want to reduce the sessions list.

To organize the sessions list in the {% data variables.copilot.agents_window %}:

1. Create a custom group from the sessions list controls.

1. Drag one or more sessions onto the group. An insertion line shows where the sessions land.

1. Hover over a group header to start a new session in that group, or mark all sessions in the group as done.

You can also drag sessions up or down to reorder them, drag group and workspace headers to rearrange sections, or drop a session on the **Pinned** section to pin it. Select multiple sessions to move them together.

You can hide the left sidebar by selecting the **Toggle Sidebar** button in the top-left corner of the {% data variables.copilot.agents_window %} or by using the `kb(workbench.action.toggleSidebarVisibility)` keyboard shortcut.

{% /tab %}
{% tab label="{% data variables.copilot.chat_view %}" %}

In the **{% data variables.copilot.chat_view %}**, the sessions list is scoped to your current workspace and groups sessions by time period, such as **Today** or **Last Week**. If you don't have a workspace open, the list shows all sessions across your workspaces.

By default, the list is filtered to only show active sessions. You can change the filter to show sessions of different states, such as completed or archived.

To hide the session list from the {% data variables.copilot.chat_view %}, right-click in an empty chat and unselect **Show Sessions** or use the `setting(chat.viewSessions.enabled)` setting.

The {% data variables.copilot.chat_view %} operates in two modes: compact and side-by-side. You can manually switch between compact and side-by-side mode by using the toggle control in the top-right corner of the {% data variables.copilot.chat_view %}.

* **Compact**: The list of sessions is embedded in the {% data variables.copilot.chat_view %}. When you select a session from the list, the {% data variables.copilot.chat_view %} switches to that session. Use the back button to return to the sessions list.

* **Side-by-side**: The list of sessions is shown side-by-side with the {% data variables.copilot.chat_view %}. Select a session from the list to view its details in the {% data variables.copilot.chat_view %}. You can further configure the orientations with the `setting(chat.viewSessions.orientation)` setting.

For supported Agent Host sessions, expand the main chat row to see its peer chats. Selecting a peer opens that conversation rather than a separate session. See [Run multiple chats in a session](<#run-multiple-chats-in-a-session>) for the workflow and the differences between interfaces.

> [!NOTE]
> Extension developers can learn how to integrate with the sessions view by using the proposed API [`chatSessionsProvider`](<https://github.com/microsoft/vscode/blob/main/src/vscode-dts/vscode.proposed.chatSessionsProvider.d.ts>). The API is currently in a proposed state and subject to change.

{% /tab %}
{% /tabs %}

### View sessions from other applications

{% data variables.product.prodname_vscode_shortname %} can discover local agent sessions created by supported applications outside {% data variables.product.prodname_vscode_shortname %}. You can open and continue sessions from {% data variables.copilot.copilot_cli_short %}, the {% data variables.copilot.github_copilot_app %}, Claude Code, and Codex in the {% data variables.copilot.chat_view %} or {% data variables.copilot.agents_window %}.

For Copilot sessions, discovery includes sessions that are associated with a repository and were updated within the last seven days.

By default, sessions created in other applications are hidden. To control which sessions appear, open the sessions list filter, select **External**, and choose one of these options:

* **None**: hide all external sessions.
* **Recent**: show the two most recently updated external sessions from the last seven days.
* **Last 24 Hours**: show external sessions updated within the last 24 hours.
* **Last 7 Days**: show external sessions updated within the last seven days.
* **All**: show all discovered external sessions.

The filter applies to the sessions lists in both the {% data variables.copilot.chat_view %} and {% data variables.copilot.agents_window %}. You can also configure it with the `setting(chat.agentSessions.showExternal)` setting.

When you open an external session in the {% data variables.copilot.agents_window %}, a one-time banner indicates that the session was created in another application. You can choose which external sessions to show from the banner. If your choice hides the open session, {% data variables.product.prodname_vscode_shortname %} asks you to confirm the change.

When you open an external Codex session, only one application can write to the chat at a time. If the chat is still open in ChatGPT or Codex CLI, {% data variables.product.prodname_vscode_shortname %} keeps the transcript, draft, and attachments available, but disables sending, queueing, and resending. The **This chat is open in another app** banner appears above the chat input.

To continue the chat in {% data variables.product.prodname_vscode_shortname %}, fully quit the other application, and then select **Retry** in the banner. Retrying checks whether the chat is available without sending the draft or adding a transcript entry. After the banner disappears, send the draft yourself.

When you send a message in an external session, the Agent Host adopts it. The session is no longer external, so the **External** filter no longer affects its visibility.

## Archive sessions

To keep the sessions list organized, archive or mark sessions as done when they're completed or you no longer need them. Archiving a session does not delete it. At any time, you can unarchive a session to restore it to the active sessions list.

When you archive (or mark as done) a session, its status changes so it moves out of the active sessions list. For a worktree session, {% data variables.product.prodname_vscode_shortname %} commits uncommitted changes to the session branch before it removes the worktree folder. If {% data variables.product.prodname_vscode_shortname %} can't preserve the changes or remove the worktree, the worktree remains. The branch and its commits are preserved, so restoring the session re-creates the worktree from that branch.

For a [Dev Container session](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>), marking a session as done removes the container without waiting for the idle shutdown period, but only when no other session or unsent draft uses it. Restoring the session starts a replacement container and preserves the conversation history.

To archive a session, hover over the session in the sessions list and select the **Archive** ({% data variables.copilot.chat_view %}) or **Mark as Done** ({% data variables.copilot.agents_window %}) option.

![Screenshot of archiving an agent session in the sessions view.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/agent-sessions-archive-v2.png>)

To view your archived sessions, use the filter options in the sessions list and select the **Archived** ({% data variables.copilot.chat_view %}) or **Done** ({% data variables.copilot.agents_window %}) filter.

### Mark an individual chat as done

In the {% data variables.copilot.agents_window %}, you can mark an additional chat within an Agent Host session as done without affecting the main chat, other chats, or the owning session:

1. Expand the owning session in the sessions list.

1. Right-click the chat, and then select **Mark as Done**.

Marking a chat as done hides it from the active sessions list and closes its tab, but does not delete it. The chat remains done after you reload the window, and its title and transcript are preserved.

To find a done chat, select the **Done** filter in the global sessions list. Alternatively, right-click the owning session and select **Show Done Chats** to show its done chats beneath it. Right-click the done chat and select **Restore**. The chat becomes active and usable again, with its title and transcript intact, so you can continue sending messages.

You can't mark the main chat, side chats, or subagent chats as done independently.

## Delete sessions

To permanently delete a session, right-click the session in the sessions list and select **Delete**. Deleting a session removes it permanently and can't be undone. For [Copilot sessions](<https://github.com/docs/agents/run/agent-harnesses.md#copilot>), deleting the session also removes any associated worktrees created for that session.

If multiple Copilot sessions share the same worktree, such as after you fork a session, deleting one session does not remove the shared worktree while another session still uses it. The worktree is removed only after the last linked session is deleted or archived.

For a [Dev Container session](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>), deleting the session removes the container only when no other session or unsent draft uses it. This cleanup also applies when automatic idle shutdown is turned off.

> [!CAUTION]
> Deleting a session is irreversible. Integrate or commit worktree changes before you delete the session because uncommitted files that exist only in a removed worktree can be lost. If you only want to hide a session, [archive](<#archive-sessions>) it instead.

## Clean up session worktrees

Worktree-isolated agent sessions can accumulate and use significant disk space. Run **Chat: Open Worktree Cleanup** to review inactive session worktrees and remove the ones you no longer need.

<!-- TODO: Add a screenshot showing the worktree cleanup editor with the storage summary, inactivity filter, and selectable sessions. -->

In the cleanup editor:

1. Choose how long sessions must be inactive. You can filter for sessions that have been inactive for 7, 15, 30, 60, or 90 days.

1. Review the estimated size of each worktree. Eligible worktrees are selected by default. Clear any worktrees that you want to keep.

1. Confirm the cleanup for the selected worktrees.

Cleanup marks each selected session as done and schedules its worktree for removal. Before removing a worktree, {% data variables.product.prodname_vscode_shortname %} commits uncommitted changes to the session branch. If the changes can't be preserved or the worktree can't be removed, the worktree remains. The session and its branch history are preserved, so restoring the session re-creates the worktree.

The cleanup editor excludes sessions that are active, running, waiting for input, pinned, done, archived, untitled, in an error state, or more recent than the selected inactivity period. It also excludes worktrees whose size can't be measured.

### Get storage cleanup suggestions (Experimental)

Set `setting(chat.agentSessions.sessionStorageCleanupSuggestion.enabled)` to receive a suggestion in the {% data variables.copilot.agents_window %} when inactive worktrees use at least 5 GiB or 20 worktrees are eligible for cleanup. Selecting the suggestion opens the worktree cleanup editor.

Dismissing the suggestion hides it for the current window. To turn off future suggestions, select **Don't Show Again** in the suggestion or run **Chat: Disable Session Storage Cleanup Suggestions**.

### Automatically clean up merged sessions

`feature(automatic-session-cleanup)`

Configure automatic cleanup to keep inactive Agent Host sessions from accumulating after their pull requests merge. Both settings are disabled by default:

* `setting(chat.agentSessions.autoMarkAsDoneMergedSessionsAfterDays)` controls how many inactive days pass before an eligible session is automatically marked as done.
* `setting(chat.agentSessions.autoDeleteMarkedAsDoneMergedSessionsAfterDays)` controls the separate grace period between automatically marking an eligible session as done and permanently deleting it.

Set each setting to a positive whole number of days. The recommended value is `15`. Set a setting to `0` to disable that part of the cleanup lifecycle.

A session is eligible when it isn't in progress, its last-modified time exceeds the configured threshold, it has at least one merged pull request, and none of its related pull requests are open. External sessions aren't eligible. {% data variables.product.prodname_vscode_shortname %} checks for eligible sessions when it starts and every hour while either setting is enabled.

Permanent deletion applies only to sessions that {% data variables.product.prodname_vscode_shortname %} automatically marked as done. Sessions that you mark as done manually aren't deleted automatically. Restoring an automatically completed session clears its deletion eligibility.

When cleanup marks a session as done or deletes it, {% data variables.product.prodname_vscode_shortname %} removes the session worktree only when the branch tracks an upstream and has no outgoing commits or uncommitted changes. If Git state is unknown or the worktree doesn't meet these conditions, the worktree is retained. Cleanup never force-removes a worktree.

When a merged pull request makes a session eligible, select **Configure Automatic Cleanup** from the **Mark as Done** suggestion to open both settings without enabling them.

## Fork a chat session

Forking a chat session branches off the conversation and inherits the conversation history from the original session. In single-chat sessions and sessions that don't use an agent host, the fork opens as a new independent session. The conversation is separate, but its code changes are isolated only if the fork uses a different folder or worktree. The new session title is prefixed with "Forked:" to help you identify it.

For multi-chat [Copilot](<https://github.com/docs/agents/run/agent-harnesses.md#use-the-copilot-harness>) sessions in the {% data variables.copilot.agents_window %}, the fork opens as a peer chat in the same session. The peer chat gets an automatically generated title and runs independently from sibling chats.

For Copilot sessions that use worktree isolation, the fork continues to use the same worktree as the original session.

Forking is useful when you want to explore an alternative approach, ask a side question, or branch a long conversation in a different direction without losing the original context.

There are two ways to fork a chat session:

* **Fork the entire session**: type `/fork` in the chat input box and press `kbstyle(Enter)`. The fork opens with the full conversation history copied from the current session.

* **Fork from a checkpoint**: hover over a chat request in the conversation and select the **Fork Conversation** button. The fork includes only the requests up to and including that checkpoint.

    ![Screenshot of the Fork Conversation button in the checkpoint toolbar in the {% data variables.copilot.chat_view %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-checkpoints/chat-fork-conversation.png>)

> [!TIP]
> A forked session inherits the conversation history of the original, which preserves the prompt cache and reduces cost on the next request. Use the [Cache Explorer](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>) to compare cache hit rates across sessions.

## Orchestrate sessions from agent host sessions

In agent host sessions, such as [Copilot](<https://github.com/docs/agents/run/agent-harnesses.md#use-the-copilot-harness>) and Claude, agents can use built-in session-management tools to coordinate work across multiple sessions and chats. These tools are also available for Codex sessions when Codex [runs on the Agent Host](<https://github.com/docs/agents/concepts/agent-host.md#process-architecture>).

With these tools, an agent can:

* List your sessions and inspect metadata like status, workspace, and file changes.
* Create a new session for a sub-task, or create a new chat in an existing session.
* Read recent conversation context from another session before continuing work.
* Send a message to another session or chat to start or steer a follow-up task.

Choose the session relationship first, and then choose the workspace and code isolation:

* **Related work**: Use `currentSession` to create a peer chat in the current session. The peer chat shares the current workspace and checkout, so use this option for related research, planning, or other work that doesn't need isolated file changes.
* **Unrelated work or a separate deliverable**: Use `independent` to create a separate session. Omit the workspace when the task doesn't need repository files. If it does, choose a trusted folder or worktree. Request a new worktree when file changes must be isolated from your current checkout.

In your prompt, state both the session relationship and the workspace choice. For related, read-only work that needs the current repository context:

```prompt
Create a peer chat in this session that reuses the current workspace and checkout to review the authentication flow. Do not modify files.
```

For unrelated work that doesn't need repository files:

```prompt
Create an independent session with no workspace to research licensing options for a separate project.
```

When the task needs repository files, you can refer to a workspace by its project name instead of providing an absolute path or workspace URI. For example:

```prompt
Create an independent session in the vscode workspace with a new worktree to implement and test the authentication change.
```

If multiple workspaces have the same project name, the agent reports the possible matches instead of choosing one. Session-management tools also support remote workspace URIs and preserve the project URI and working directories for multi-root workspaces.

When a tool creates or targets a session, {% data variables.product.prodname_vscode_shortname %} shows an **Open Session** pill in chat so you can jump directly to it.

To keep this workflow safe and predictable:

* Sending a message to another session always requires your confirmation.
* Agents cannot send messages to the same chat they are currently running in.
* Burst sends are capped to avoid unbounded fan-out.
* Archived sessions are excluded from listings unless explicitly requested.

`setting(chat.agentHost.agentOrchestrationLimits)` controls the process-wide limits for sessions and chats that agents create, messages they send, and recursive session creation. The default `on` value enforces limits that support coordination-heavy workflows. Set it to `off` to remove the limits. Changes apply without restarting the Agent Host, and reaching a limit doesn't interrupt work that is already running.

Removing orchestration limits doesn't change confirmation requirements or input validation. The safety guidance in this section continues to apply.

## Save and export chat sessions

You can save chat sessions to preserve important conversations or reuse them later for similar tasks.

### Export a chat session as a JSON file

You can export a chat session to save it for later reference or share it with others. Exporting a chat session creates a JSON file that contains all prompts and responses from the session.

To export a chat session:

1. Open the chat session you want to export in the {% data variables.copilot.chat_view %}.

1. Run the **Chat: Export Chat...** command from the Command Palette (`kb(workbench.action.showCommands)`).

1. Choose a location to save the JSON file.

### Copy chat messages as Markdown

The {% data variables.copilot.chat_view %} supports different options for copying chat messages as Markdown to the clipboard, available through the context menu when you right-click a message or the chat background.

* **Copy**: Copy an individual prompt or response to the clipboard - the Markdown contains the response text, thinking steps, and tool calls.

* **Copy All**: Copy the entire chat session in Markdown format, including all prompts, responses, thinking steps, and tool calls.

* **Copy Final Response**: Copy just the final Markdown section of the agent's response, after the last tool call. This is useful for sharing or reusing the final output without the intermediate steps.

## Monitor sessions from the application icon (Preview)

The application icon can show how many unarchived sessions in the {% data variables.copilot.agents_window %} need your attention. The badge appears on the dock icon on macOS, the launcher icon on Linux, or the taskbar icon on Windows.

![Screenshot showing the {% data variables.product.prodname_vscode_shortname %} application icon with a badge count of one in the Windows taskbar.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/application-icon-session-badge.png>)

The badge counts sessions that:

* Have unread results and are no longer in progress.
* Are waiting for your input.
* Have failing CI checks on an open, non-draft pull request and are no longer in progress.

Unread sessions that are still in progress aren't included. The count updates as sessions change state. Reading or archiving a session removes it from the count when it no longer needs your attention.

Use the `setting(sessions.showApplicationBadge)` setting to control the application badge. The setting defaults to `true` in Insiders and `false` in Stable.

## Session status indicator (Experimental)

The session status indicator provides quick access to your sessions directly from the command center in the title bar. The indicator displays visual badges for unread messages and in-progress sessions, so you can stay informed about AI activity without switching views.

![Screenshot showing the session status indicator in the command center with unread and in-progress badges.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/agent-status-indicator-v2.png>)

The indicator shows:

* **Unread sessions badge**: shows the count of chat sessions with new messages. Select the badge to filter the sessions list to show only unread sessions.
* **In-progress sessions badge**: shows the count of sessions with running agents. Select the badge to filter the sessions list to show only in-progress sessions.
* **Sparkle icon**: provides quick access to chat and session management options.

You can configure the status indicator's visibility by using the `setting(chat.agentsControl.enabled)` setting.

## View sessions on the {% data variables.product.prodname_vscode_shortname %} welcome page

The {% data variables.product.prodname_vscode_shortname %} welcome page can act as your startup experience for working with chat sessions. It provides quick access to your recent chat sessions, an embedded chat widget for starting new tasks, and quick actions for common tasks.

To configure the {% data variables.product.prodname_vscode_shortname %} welcome page as your startup experience, set `setting(workbench.startupEditor)` to `agentSessionsWelcomePage`.

## Related resources

* [Use chat in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>)
* [Agents overview](<https://github.com/docs/agents/overview.md>)
* [Add context to chat](<https://github.com/docs/chat/copilot-chat-context.md>)
* [Best practices for using AI](<https://github.com/docs/agents/best-practices.md>)


</details>
