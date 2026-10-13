# 在 VS Code 中使用 AI 开发

介绍理解代码、修复缺陷和构建功能的协作过程，比较编辑器内工作与任务委派，并强调审查修改和验证结果。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/overview.md>)；源码提交（存档版）：[2026-10-08](<https://github.com/microsoft/vscode-docs/commit/a88484276b0f7e053f9145da14de6a98f32b61c5>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/overview.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/在 VS Code 中使用 AI 开发.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 7c4b8b5e-2d3f-4e8a-9b2c-1a5d6f8e9c0b
DateApproved: 10/7/2026
MetaDescription: Explore AI in {% data variables.product.prodname_vscode %}, from code suggestions to agents that plan, edit, and test changes.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- GitHub Copilot
- AI
- agents
- autonomous
- agentic
- multi-file editing
- architecture
- refactoring
- "{% data variables.copilot.copilot_cloud_agent_short %}"
- copilot cli
- third-party agents
- language models
- MCP
- plugins
- enterprise
- overview
- getting started
- free
- copilot free
- bring your own key
- BYOK
- local models
- agent harness
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 在 {% data variables.product.prodname_vscode_shortname %} 中使用 AI 构建

使用 {% data variables.product.prodname_vscode %} 中的 AI 来理解不熟悉的代码、修复错误和构建功能。对于跨文件和工具的任务，agent 可以查找相关代码、进行更改并运行检查，而无需您指导每次搜索、编辑和测试运行。您描述结果、检查更改并验证结果。

在与编辑器、终端、测试和调试器相同的工作区中使用 agent。您可以检查其更改并调查故障，而无需将代码和命令输出移动到单独的聊天应用程序。对于问题或重点编辑，请使用聊天、内联聊天或建议，而无需委派整个任务。

对于日常编码和 agent 任务，请从 [{% data variables.product.prodname_copilot_short %} 工具 ](<https://github.com/docs/agents/run/agent-harnesses.md#use-the-copilot-harness>) 开始探索代码、计划更改、实现功能和运行测试。该线束基于 {% data variables.copilot.copilot_sdk %} 构建，有助于在 {% data variables.product.prodname_vscode_shortname %}、{% data variables.copilot.copilot_cli %} 和 {% data variables.copilot.github_copilot_app %} 之间提供更一致的行为。您可以在 {% data variables.copilot.chat_view %} 或 {% data variables.copilot.agents_window %} 中继续相同的实时会话，并且 [ 发现并继续在 {% data variables.copilot.copilot_cli %} 或 {% data variables.copilot.github_copilot_app %}](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) 中创建的受支持会话。

当您需要工具或工作流程时，选择另一个 [agent 工具 ](<https://github.com/docs/agents/concepts/agent-harnesses.md>)，或者将独立任务委托给 [ 云 agent](<https://github.com/docs/agents/run/agent-harnesses.md#start-a-cloud-session>)。

<div class="docs-action" data-show-in-doc="true" data-show-in-sidebar="false" title="Try your first agent task">
在{% data variables.copilot.chat_view %}中构建并验证一个小应用程序，然后查看结果。

* [启动agents快速入门](<https://github.com/docs/agents/quickstart.md>)

</div>

如果您对 {% data variables.product.prodname_vscode_shortname %} 和 AI 都不熟悉，请首先 [ 安装编辑器并打开工作区 ](<https://github.com/docs/getstarted/overview.md>)。如果您已经使用agents，请跳过示例项目并按照[经验丰富-agent快速通道](<https://github.com/docs/agents/guides/overview.md#experienced-agent-fast-track>)进行操作。如需在不委派任务的情况下获得 AI 帮助，[可以在建议、重点编辑、问题和 agents](<https://github.com/docs/agents/reference/ai-features-cheat-sheet.md#choose-a-feature>) 之间进行选择。

快速入门使用 [{% data variables.product.prodname_copilot %} 线束 ](<https://github.com/docs/agents/run/agent-harnesses.md#use-the-copilot-harness>)。有关帐户、使用情况和数据处理要求，请参阅 [{% data variables.product.prodname_copilot_short %} 设置指南 ](<https://github.com/docs/setup/copilot.md>)。如果提供商、登录或执行位置影响您的设置，请[了解会话控制](<https://github.com/docs/agents/run/agent-harnesses.md#understand-the-session-controls>)并[选择agent线束](<https://github.com/docs/agents/run/agent-harnesses.md#choose-a-session-target>)。

## 你可以用AI做什么

根据任务选择工作流程，从单行帮助到整个项目的协调变更。

|你的目标|AI 如何提供帮助|
|-----------|--------------|
|实现功能、修复错误或跨文件重构|[与 agent](<#您如何使用-agent>) 一起查找相关代码、进行更改并运行测试。|
|了解代码库或探索方法|[在聊天中提问](<https://github.com/docs/chat/chat-overview.md>)以获得基于您的代码的解释并讨论替代方案。|
|在编辑之前研究复杂的更改|[使用 agent](<https://github.com/docs/agents/run/planning.md>) 进行计划并在实施前审查提议的方法。|
|无需留下代码即可进行集中编辑|使用[内联聊天](<https://github.com/docs/chat/inline-chat.md>)来描述编辑器中的变化。|
|键入时获取代码建议|使用 [ 内联建议 ](<https://github.com/docs/editing/ai-powered-suggestions.md>) 进行补全并建议下一步编辑。|
|完成共同开发任务|使用 [ 智能操作 ](<https://github.com/docs/editing/copilot-smart-actions.md>) 执行生成提交消息或解释所选代码等任务。|

您可以使用聊天来提问和指导 agents。您可以在编辑前要求 **Agent** 提出方法，或者选择 ** 计划 **（如果可用）以进行专用规划工作流程。

为了进行紧凑比较，请使用 [AI 功能备忘单 ](<https://github.com/docs/agents/reference/ai-features-cheat-sheet.md#choose-a-feature>) 选择适合您的任务的最不自主的选项。

## 您如何使用 agent

agent 使用语言模型来推理您的请求，并使用 [ 工具 ](<https://github.com/docs/agents/concepts/tools.md>) 来对您的开发环境进行操作。它收集 [ 上下文 ](<https://github.com/docs/agents/concepts/context.md>)，例如代码、错误消息和测试结果，而不是仅依赖提示中的单词。

例如，要求 agent 修复失败的测试，同时保留预期的行为：

1. agent找到测试并读取相关代码以了解故障。
1. 它根据任务和您的约束来编辑实施或测试。
1. 它运行测试、检查结果并根据需要修改其更改。
1. 您查看差异和验证结果，亲自验证行为，并决定保留哪些更改。

这种重复的推理、行动和验证就是**agent循环**。您可以提供更多上下文、重定向工作或停止 agent。详细了解 [ agents 的工作原理 ](<https://github.com/docs/agents/concepts/agents.md>)。

任务的对话和工作属于 ** 会话 **。会话将相关上下文和更改保存在一起，以便您可以返回任务或单独管理独立任务。了解 [ 会话和切换 ](<https://github.com/docs/agents/concepts/sessions.md>)。

## 使用 agents 的方式

从适合您想要的工作方式的界面开始。您可以在 {% data variables.copilot.chat_view %} 和 {% data variables.copilot.agents_window %} 之间继续支持的会话，而不是为每项任务选择一个接口。

### 与您的代码一起工作

当您在开放项目中工作时，请使用 [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>)。将对话保留在编辑器旁边，检查发生的更改，并使用调试器、终端和测试作为同一工作流程的一部分。这是 agents 快速入门的起点。

![显示 {% data variables.copilot.chat_view %} 以及会话列表、对话和聊天输入的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/chat-view-expanded.png>)

对于键盘、屏幕阅读器和低视力工作流程，了解如何使用 [ 辅助视图和其他辅助功能 ](<https://github.com/docs/configure/accessibility/accessibility.md#accessible-view>)。

### 委派和管理任务

当您的重点是分配任务并查看其结果时，请使用 [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>)。跨项目管理多个会话，跟踪其进度，并在您想要直接使用代码时打开编辑器。

![屏幕截图显示如何通过选择 {% data variables.copilot.agents_window %} 侧边栏顶部的“新建”来启动新的 agent 会话。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/agents-window-hero.png>)

### 其他访问方式agents

对于基于终端的工作，请使用 [{% data variables.copilot.copilot_cli %}](<https://github.com/docs/agents/run/agent-harnesses.md#use-copilot-cli-from-the-terminal>)。 [{% data variables.copilot.github_copilot_app %}](<https://github.com/features/copilot>) 提供专用的桌面体验。您可以[打开并继续在{% data variables.product.prodname_vscode_shortname %}中的任一应用程序](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>)中创建的受支持的本地会话。要在 CLI 中继续 {% data variables.product.prodname_copilot_short %} 会话，请右键单击该会话并选择 **在终端中恢复 **。

要远离机器工作，请探索 [云 agents 返回拉取请求](<https://github.com/docs/agents/run/agent-harnesses.md#start-a-cloud-session>) 或 [远程会话和浏览器访问](<https://github.com/docs/agents/run/remote-agent-sessions.md>).

## 选择您的型号、agents 和工具

从快速入门的建议设置开始，然后调整各个选择以适合您的任务和项目：

* **模型**：根据任务所需的推理能力、速度和成本选择[语言模型](<https://github.com/docs/agents/concepts/language-models.md>)。
* **Agent 线束 **：为其工具和工作流程使用受支持的 [agent 线束 ](<https://github.com/docs/agents/run/agent-harnesses.md>)，例如 {% data variables.product.prodname_copilot_short %}、{% data variables.product.prodname_anthropic_claude %} 或 {% data variables.product.prodname_openai_codex %}。线束将模型连接到工具并管理会话，因此更改线束与切换模型不同。
* **模型访问**：使用{% data variables.product.prodname_copilot %}计划中的模型，[自带API密钥(BYOK)](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>)，或连接支持的本地模型。这些选项允许您使用现有的模型提供商帐户或将模型处理保留在本地。
* **工具和定制**：通过[项目说明](<https://github.com/docs/agents/guides/customize-copilot-guide.md>)分享您的编码标准和测试命令。通过[Model Context Protocol（MCP）服务器](<https://github.com/docs/agent-customization/mcp-servers.md>)连接外部系统，将重复任务打包为[agent skills](<https://github.com/docs/agent-customization/agent-skills.md>)，或安装捆绑工具和工作流程的[插件](<https://github.com/docs/agent-customization/agent-plugins.md>)。比较[agent定制概念](<https://github.com/docs/agents/concepts/customization.md>)中的选项。

可用的模型、工具和自定义项取决于所选的工具、您的帐户和组织的策略。

工具运行的位置和模型托管的位置是单独的选择。 agent 可以编辑您计算机上的文件，同时向托管提供商发送模型请求。

## 保持控制

AI 可能会生成不正确的代码或误解您的意图。您仍然负责决定哪些更改会到达您的代码库。

* **集成前检查。** Agents可以将编辑直接保存到会话文件夹或工作树。 [在提交或合并更改之前查看差异并验证结果](<https://github.com/docs/agents/run/review-code-edits.md>)。
* **控制操作并重定向工作。**使用[权限和批准](<https://github.com/docs/agents/run/approvals.md>)来决定哪些操作需要确认。您可以[引导或停止请求](<https://github.com/docs/chat/chat-overview.md#send-messages-while-a-request-is-running>)，但停止不会撤消已完成的操作或对外部服务的更改。
* **了解隔离。** Git 工作树将代码更改分开，但不是安全边界。有关 agent 运行终端命令的文件系统和网络限制，请查看 [ 平台特定的沙箱选项 ](<https://github.com/docs/agents/run/agent-sandboxing.md>)。

在现有项目上使用 agents 之前，请查看 [ 推荐的安全基线 ](<https://github.com/docs/agents/run/security.md#recommended-security-baseline>)。在托管设备上，您的组织可能通过 [企业 AI 策略 ](<https://github.com/docs/enterprise/manage-ai-settings.md>) 限制可用的 agents、模型和工具。如果 agents 未出现在聊天中，请查看 [ 可用性故障排除步骤 ](<https://github.com/docs/agents/agent-troubleshooting/faq.md#agents-are-not-available-in-chat>)。

## 后续步骤

* **{% data variables.product.prodname_vscode_shortname %}和AI新增功能：** [安装编辑器，打开工作区，然后选择第一个任务](<https://github.com/docs/getstarted/overview.md>)。
* **已使用 {% data variables.product.prodname_vscode_shortname %}，但对 agents 不熟悉：** [使用建议的起始配置完成第一个 agent 任务 ](<https://github.com/docs/agents/quickstart.md>)。
* **已经使用AI agents：**按照[经验丰富的agent快速通道](<https://github.com/docs/agents/guides/overview.md#experienced-agent-fast-track>)在您自己的存储库中工作。
* **更喜欢轻量级 AI:** [选择 AI 功能 ](<https://github.com/docs/agents/reference/ai-features-cheat-sheet.md#choose-a-feature>) 以获取建议、重点编辑、提问或委派工作。
* **准备改进项目工作流程：** [根据您的项目调整 agents](<https://github.com/docs/agents/guides/customize-copilot-guide.md>)，然后[比较质量、可靠性和使用情况](<https://github.com/docs/agents/guides/optimize-usage.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Build with AI in {% data variables.product.prodname_vscode_shortname %}

Use AI in {% data variables.product.prodname_vscode %} to understand unfamiliar code, fix bugs, and build features. For a task that spans files and tools, an agent can find relevant code, make changes, and run checks without you directing each search, edit, and test run. You describe the outcome, review the changes, and verify the result.

Work with an agent in the same workspace as your editor, terminal, tests, and debugger. You can inspect its changes and investigate failures without moving code and command output to a separate chat application. For a question or focused edit, use chat, inline chat, or suggestions without delegating an entire task.

For day-to-day coding and agent tasks, start with the [{% data variables.product.prodname_copilot_short %} harness](<https://github.com/docs/agents/run/agent-harnesses.md#use-the-copilot-harness>) to explore code, plan changes, implement features, and run tests. Built on the {% data variables.copilot.copilot_sdk %}, the harness helps provide more consistent behavior across {% data variables.product.prodname_vscode_shortname %}, {% data variables.copilot.copilot_cli %}, and the {% data variables.copilot.github_copilot_app %}. You can continue the same live session in the {% data variables.copilot.chat_view %} or {% data variables.copilot.agents_window %}, and [discover and continue supported sessions created in {% data variables.copilot.copilot_cli %} or the {% data variables.copilot.github_copilot_app %}](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>).

Choose another [agent harness](<https://github.com/docs/agents/concepts/agent-harnesses.md>) when you need its tools or workflows, or delegate an independent task to a [cloud agent](<https://github.com/docs/agents/run/agent-harnesses.md#start-a-cloud-session>).

<div class="docs-action" data-show-in-doc="true" data-show-in-sidebar="false" title="Try your first agent task">
Build and validate a small app in the {% data variables.copilot.chat_view %}, then review the result.

* [Start the agents quickstart](<https://github.com/docs/agents/quickstart.md>)

</div>

If you're new to both {% data variables.product.prodname_vscode_shortname %} and AI, first [install the editor and open a workspace](<https://github.com/docs/getstarted/overview.md>). If you already use agents, skip the sample project and follow the [experienced-agent fast track](<https://github.com/docs/agents/guides/overview.md#experienced-agent-fast-track>). For AI help without delegating a task, [choose between suggestions, focused edits, questions, and agents](<https://github.com/docs/agents/reference/ai-features-cheat-sheet.md#choose-a-feature>).

The quickstart uses the [{% data variables.product.prodname_copilot %} harness](<https://github.com/docs/agents/run/agent-harnesses.md#use-the-copilot-harness>). See the [{% data variables.product.prodname_copilot_short %} setup guide](<https://github.com/docs/setup/copilot.md>) for account, usage, and data-handling requirements. If provider, sign-in, or execution location affects your setup, [understand the session controls](<https://github.com/docs/agents/run/agent-harnesses.md#understand-the-session-controls>) and [choose an agent harness](<https://github.com/docs/agents/run/agent-harnesses.md#choose-a-session-target>).

## What you can do with AI

Choose a workflow based on the task, from help with a single line to a coordinated change across your project.

| Your goal | How AI helps |
|-----------|--------------|
| Implement a feature, fix a bug, or refactor across files | [Work with an agent](<#how-you-work-with-an-agent>) to find relevant code, make changes, and run tests. |
| Understand a codebase or explore an approach | [Ask questions in chat](<https://github.com/docs/chat/chat-overview.md>) to get explanations grounded in your code and discuss alternatives. |
| Research a complex change before editing | [Plan with an agent](<https://github.com/docs/agents/run/planning.md>) and review the proposed approach before implementation. |
| Make a focused edit without leaving your code | Use [inline chat](<https://github.com/docs/chat/inline-chat.md>) to describe the change in the editor. |
| Get code suggestions while you type | Use [inline suggestions](<https://github.com/docs/editing/ai-powered-suggestions.md>) for completions and suggested next edits. |
| Complete a common development task | Use [smart actions](<https://github.com/docs/editing/copilot-smart-actions.md>) for tasks such as generating a commit message or explaining selected code. |

You use chat both to ask questions and to direct agents. You can ask **Agent** to propose an approach before editing, or select **Plan**, where available, for a dedicated planning workflow.

For a compact comparison, use the [AI features cheat sheet](<https://github.com/docs/agents/reference/ai-features-cheat-sheet.md#choose-a-feature>) to choose the least autonomous option that fits your task.

## How you work with an agent

An agent uses a language model to reason about your request and [tools](<https://github.com/docs/agents/concepts/tools.md>) to act on your development environment. It gathers [context](<https://github.com/docs/agents/concepts/context.md>), such as code, error messages, and test results, rather than relying only on the words in your prompt.

For example, ask an agent to fix a failing test while preserving the intended behavior:

1. The agent finds the test and reads the related code to understand the failure.
1. It edits the implementation or tests based on the task and your constraints.
1. It runs the tests, inspects the results, and revises its changes if needed.
1. You review the diff and validation results, verify the behavior yourself, and decide which changes to keep.

This repeated reasoning, action, and validation is the **agent loop**. You can provide more context, redirect the work, or stop the agent along the way. Learn more about [how agents work](<https://github.com/docs/agents/concepts/agents.md>).

The conversation and work for a task belong to a **session**. Sessions keep related context and changes together so you can return to a task or manage independent tasks separately. Learn about [sessions and handoff](<https://github.com/docs/agents/concepts/sessions.md>).

## Ways to work with agents

Start with the interface that fits how you want to work. You can continue supported sessions between the {% data variables.copilot.chat_view %} and the {% data variables.copilot.agents_window %}, rather than choosing one interface for every task.

### Work alongside your code

Use the [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>) when you're working in an open project. Keep the conversation beside your editor, inspect changes as they happen, and use the debugger, terminal, and tests as part of the same workflow. This is the starting point for the agents quickstart.

![Screenshot showing the {% data variables.copilot.chat_view %} with the sessions list, conversation, and chat input.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/chat-view-expanded.png>)

For keyboard, screen reader, and low-vision workflows, learn how to use the [Accessible View and other accessibility features](<https://github.com/docs/configure/accessibility/accessibility.md#accessible-view>).

### Delegate and manage tasks

Use the [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) when your focus is assigning tasks and reviewing their results. Manage multiple sessions across projects, follow their progress, and open the editor when you want to work directly with the code.

![Screenshot showing how to start a new agent session by selecting New at the top of the sidebar in the {% data variables.copilot.agents_window %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-overview/agents-window-hero.png>)

### Other ways to access agents

For terminal-based work, use [{% data variables.copilot.copilot_cli %}](<https://github.com/docs/agents/run/agent-harnesses.md#use-copilot-cli-from-the-terminal>). The [{% data variables.copilot.github_copilot_app %}](<https://github.com/features/copilot>) provides a dedicated desktop experience. You can [open and continue supported local sessions created in either application](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) in {% data variables.product.prodname_vscode_shortname %}. To continue a {% data variables.product.prodname_copilot_short %} session in the CLI, right-click the session and select **Resume in Terminal**.

For work away from your machine, explore [cloud agents that return pull requests](<https://github.com/docs/agents/run/agent-harnesses.md#start-a-cloud-session>) or [remote sessions and browser access](<https://github.com/docs/agents/run/remote-agent-sessions.md>).

## Choose your models, agents, and tools

Start with the quickstart's recommended setup, then adjust individual choices to fit your task and project:

* **Models**: choose a [language model](<https://github.com/docs/agents/concepts/language-models.md>) based on the reasoning capabilities, speed, and cost your task requires.
* **Agent harnesses**: use a supported [agent harness](<https://github.com/docs/agents/run/agent-harnesses.md>), such as {% data variables.product.prodname_copilot_short %}, {% data variables.product.prodname_anthropic_claude %}, or {% data variables.product.prodname_openai_codex %}, for its tools and workflows. The harness connects a model to tools and manages the session, so changing harnesses is different from switching models.
* **Model access**: use models from your {% data variables.product.prodname_copilot %} plan, [bring your own API key (BYOK)](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>), or connect a supported local model. These options let you use an existing model provider account or keep model processing local.
* **Tools and customization**: share your coding standards and test commands through [project instructions](<https://github.com/docs/agents/guides/customize-copilot-guide.md>). Connect external systems through [Model Context Protocol (MCP) servers](<https://github.com/docs/agent-customization/mcp-servers.md>), package recurring tasks as [agent skills](<https://github.com/docs/agent-customization/agent-skills.md>), or install [plugins](<https://github.com/docs/agent-customization/agent-plugins.md>) that bundle tools and workflows. Compare the options in [agent customization concepts](<https://github.com/docs/agents/concepts/customization.md>).

Available models, tools, and customizations depend on the selected harness, your account, and your organization's policies.

Where tools run and where the model is hosted are separate choices. An agent can edit files on your machine while sending model requests to a hosted provider.

## Stay in control

AI can produce incorrect code or misunderstand your intent. You remain responsible for deciding which changes reach your codebase.

* **Review before integrating.** Agents can save edits directly to the session folder or worktree. [Review the diff and validate the result](<https://github.com/docs/agents/run/review-code-edits.md>) before you commit or merge changes.
* **Control actions and redirect work.** Use [permissions and approvals](<https://github.com/docs/agents/run/approvals.md>) to decide which actions require confirmation. You can [steer or stop a request](<https://github.com/docs/chat/chat-overview.md#send-messages-while-a-request-is-running>), but stopping doesn't undo completed actions or changes to external services.
* **Understand isolation.** A Git worktree keeps code changes separate, but isn't a security boundary. For file system and network restrictions on agent-run terminal commands, review the [platform-specific sandboxing options](<https://github.com/docs/agents/run/agent-sandboxing.md>).

Before using agents on an existing project, review the [recommended security baseline](<https://github.com/docs/agents/run/security.md#recommended-security-baseline>). On a managed device, your organization might restrict available agents, models, and tools through [enterprise AI policies](<https://github.com/docs/enterprise/manage-ai-settings.md>). If agents don't appear in chat, review the [availability troubleshooting steps](<https://github.com/docs/agents/agent-troubleshooting/faq.md#agents-are-not-available-in-chat>).

## Next steps

* **New to {% data variables.product.prodname_vscode_shortname %} and AI:** [Install the editor, open a workspace, and choose a first task](<https://github.com/docs/getstarted/overview.md>).
* **Already use {% data variables.product.prodname_vscode_shortname %}, but new to agents:** [Complete a first agent task](<https://github.com/docs/agents/quickstart.md>) with a recommended starting configuration.
* **Already use AI agents:** Follow the [experienced-agent fast track](<https://github.com/docs/agents/guides/overview.md#experienced-agent-fast-track>) to work in your own repository.
* **Prefer lighter-weight AI:** [Choose an AI feature](<https://github.com/docs/agents/reference/ai-features-cheat-sheet.md#choose-a-feature>) for suggestions, focused edits, questions, or delegated work.
* **Ready to improve a project workflow:** [Adapt agents to your project](<https://github.com/docs/agents/guides/customize-copilot-guide.md>), then [compare quality, reliability, and usage](<https://github.com/docs/agents/guides/optimize-usage.md>).


</details>
