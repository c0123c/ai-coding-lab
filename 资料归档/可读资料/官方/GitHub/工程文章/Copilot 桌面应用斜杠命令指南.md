# Copilot 桌面应用斜杠命令指南

介绍规划、反向讨论、自动执行、画布和任务编排等命令，将聊天扩展为可操作的开发流程。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-08-06](<https://github.blog/ai-and-ml/github-copilot/a-guide-to-slash-commands-in-the-github-copilot-app/>)；更新：[2026-08-06](<https://github.blog/ai-and-ml/github-copilot/a-guide-to-slash-commands-in-the-github-copilot-app/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/a-guide-to-slash-commands-in-the-github-copilot-app/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/Copilot 桌面应用斜杠命令指南.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub Copilot 应用程序中的斜线命令指南

如果您在 GitHub Copilot CLI 中使用过斜杠命令，您已经知道快速的 `/` 有多强大。在 GitHub Copilot 应用程序中，斜杠命令进一步发展了这一想法，为您提供管理会话、导航项目和自定义 Copilot 工作流程的快捷方式。

## 什么是斜杠命令？

斜杠命令是您直接在 GitHub Copilot 应用程序的聊天编辑器中键入的文本快捷方式。首先输入 `/`，然后会出现一个自动完成菜单，显示当前上下文中可用的命令。这是一个潜力巨大的小角色，打开了快捷方式之门，帮助您以新的方式使用 Copilot。

如果您来自 CLI，那么主要区别如下：CLI 斜杠命令是围绕终端优先的工作流程设计的。添加目录、设置工作目录和管理终端访问等操作都是通过命令进行的。这是有道理的，因为 CLI 位于您的终端内，没有可视界面。

|  |
| --- |
|💡**提示**：如果你在CopilotCLI中使用过斜杠命令，你会注意到一些熟悉的面孔。 `/clear` 和 `/model` 等命令在这两个地方都有效。但 GitHub Copilot 应用程序特定命令是为桌面应用程序提供的多会话工作流程量身定制的。|

另一方面，该应用程序提供了用于管理上下文的可视化界面。由于应用程序会自动管理项目上下文，因此不需要 `/add-dir` 或 `/cwd` 等文件访问命令。应用程序斜线命令更多的是关于工作流程。您可以在会话之间导航、管理项目以及控制 agent 的工作方式。

## 为什么要使用斜杠命令？

斜杠命令可能看起来像简单的快捷方式，但它们可以改变您与 Copilot 交互的方式。它们可以帮助您更快地行动、保持专注并快速访问您需要的工作流程。您可以键入命令并继续前进，而无需挖掘选项或分散注意力来寻找正确的工具。单个 `/` 打开斜线命令列表，可以帮助您更快地移动、探索新想法并充分利用该应用程序。

让我们看一下 GitHub Copilot 应用程序中可用的一些斜杠命令以及它们如何适合您的日常工作流程。

## 在编写代码之前，先创建一个 `/plan`

好的代码始于好的计划。 `/plan` 帮助您在开始编写代码之前分解任务，思考您的方法，识别潜在的挑战，并决定接下来需要发生什么。它还会将您的会话切换到 **Plan** 模式，您也可以从聊天编辑器的 **Mode** 下拉列表中进行选择。

* **计划一个新功能。**在开始实施之前分解新功能的想法。让 Copilot 识别文件、组件和依赖项，以便您有更清晰的前进道路。
* `/plan I need to add two-factor authentication to our application. Help me break down the work involved, identify what files need to change, and outline an implementation approach.`
* **为大型重构做好准备。**在接触代码之前先规划出复杂的更改。发现潜在风险并开发渐进方法以安全地进行重大更改。
* `/plan We want to refactor our notification system code to make it easier to support new channels like push notifications. Help me understand the changes needed and create an incremental migration plan.`
* **分类并修复错误。**如果您知道出现问题但不确定从哪里开始，`/plan`可以帮助您探索可能的原因并概述诊断和解决问题所需的步骤。
* `/plan Users are reporting that our checkout flow randomly fails after payment processing. Help me investigate possible causes and create a plan to diagnose and fix the issue.`

## 让Copilot与`/spar`唱反调

有时验证一个想法的最好方法就是挑战它。 `/spar`就像一位队友举手问：“我们有没有想过如果出了问题会发生什么？”在您承诺解决方案之前，它可以让 Copilot 质疑您的假设并指出潜在的风险或权衡，从而帮助您对您的方法进行压力测试。您可以通过以下几种方式使用它：

* **验证架构选择。**提出您使用 Redis 进行缓存的计划，并让 Copilot质疑您的失效策略、可扩展性或其他方法是否更适合您的工作负载。
* `/spar I'm planning to use Redis as a caching layer for our product API. Challenge my approach and point out any scalability or consistency concerns I may have missed.`
* **比较实现选项。**要求Copilot根据应用程序的要求讨论REST与GraphQL或同步与异步处理的优缺点。
* `/spar Help me decide between REST and GraphQL for a customer-facing API. Ask questions, challenge my assumptions, and recommend which approach fits best for an app with mobile clients.`
* **查看迁移计划。** 逐步完成数据库迁移或基础架构更改，并让 Copilot 在开始之前识别边缘案例、风险或推出问题。
* `/spar I'm migrating our database to a new managed service with minimal downtime. Poke holes in my migration plan and identify any risks or edge cases I should account for.`
* **挑战性能优化。**分享您正在考虑的优化，并要求Copilot指出隐藏的瓶颈、意想不到的副作用或更简单的替代方案。
* `/spar I'm planning to lazy load most of the components on my site to improve initial load time. Critique my approach and tell me where it could hurt user experience or introduce unnecessary complexity.`

## `/autopilot` 拿方向盘

一旦您有了 `/plan`，下一步就是将这个想法转化为工作代码。 `/autopilot` 帮助您完成实施、进行更改并根据需要进行迭代。不要管理每个单独的步骤，而是给 Copilot 一个目标，并让它完成完成任务所需的步骤。它还会将您的会话切换到 **Autopilot** 模式，您也可以从聊天编辑器中的 **Mode** 下拉列表中进行选择。

* **实现新功能。** 移交任务并让 Copilot 完成实现步骤。
* `/autopilot Add support for exporting user reports as CSV files. Identify the files that need changes, implement the feature, and update any relevant tests.`
* **完成更大的维护任务。** 使用 `/autopilot` 执行需要多个步骤的任务，例如更新依赖项、重构代码或改进文档。
* `/autopilot Update this project to the latest version of React. Identify breaking changes, update the code where needed, and make sure the test suite passes.`

## 与 `/rubber-duck` 讨论一下

我从个人经验中了解到，与你的猫讨论问题并不总是有帮助。他们的倾听能力充其量是值得怀疑的，他们的调试建议通常以他们坐在我的键盘上或咀嚼我的电线而告终。 `/rubber-duck` 为您提供更好的东西：一双新鲜的眼睛。

它使用不同的模型来独立审查您的工作，帮助发现盲点、质疑假设并发现主要模型可能遗漏的问题。它对于复杂的重构、架构决策、迁移计划或任何您在继续之前需要第二意见的时候特别有用。

* **获得对计划的第二意见。**在开始实现复杂功能之前，让不同的模型独立审查您的计划并指出假设、遗漏的步骤或潜在风险。
  `/rubber-duck Review the implementation plan we've created for adding two-factor authentication. Identify any blind spots, edge cases, or risks that we may have overlooked.`
* **审查大型重构。**进行重大更改后，要求/rubber-duck批评该方法并突出显示任何可以简化、改进或以不同方式处理的内容。
  `/rubber-duck Review the refactoring we've completed for the notification system. Look for architectural concerns, unnecessary complexity, or areas that could be improved before I open a pull request.`
* **验证迁移策略。**在推出复杂的迁移之前，使用第二个模型来独立评估您的方法并确定推出或可靠性问题。
  `/rubber-duck Review our database migration plan and implementation. Point out any blind spots, rollback concerns, or edge cases we should address before deployment.`

## 使用 `/create-canvas` 将对话变成互动体验

并非所有问题都可以通过聊天窗口得到最好的解决。 `/create-canvas` 可让您直接通过与 Copilot 的对话创建交互式界面。您可以将其转换为可以交互的可视化、仪表板或自定义工作流程，而不是在长时间的聊天中处理信息。例如：

* `/create-canvas Create an interactive diagram showing how services in this application connect.`
* `/create-canvas Create an issue triage board that lets me review and categorize open issues.`

有关创建画布的更多信息和示例，[查看我们的博客文章](<https://github.blog/ai-and-ml/github-copilot/how-to-build-interactive-experiences-with-canvases/>)。

## 当一项任务变成多项任务时，使用`/orchestrate`

并非每项任务都完全适合单个工作流程。有时您需要跨多个存储库进行更改或同时处理多个相关任务。 `/orchestrate` 通过将较大的工作分解为可以并行推进的较小任务，帮助您协调跨会话和存储库的工作。

* **协调存储库之间的更改。**当某个功能涉及多个代码库时，`/orchestrate`可以帮助您跟踪工作并协调每个存储库的更新。
* `/orchestrate I need to add support for a new authentication flow across our frontend, backend, and shared repositories. Help me break down the work and coordinate the changes needed in each codebase.`
* **管理并行开发任务。** 对于较大的项目，Copilot 可以将工作划分为集中的工作，以便多个部分可以一起推进。
* `/orchestrate Prepare this feature for release. Identify the work needed for implementation, testing, documentation, and deployment, then help coordinate each task.`

## 您的下一个工作流程以 `/` 开始

您无需记住任何命令即可开始。选择一些与您的工作方式相匹配的斜杠命令并从那里开始构建。自动完成菜单会为您提供其余的支持。输入 `/` 并开始探索！您还可以[阅读我们的文档](<https://docs.github.com/copilot/concepts/agents/github-copilot-app>)以了解有关GitHub Copilot应用程序的更多信息。

[尝试在GitHub Copilot应用程序](<https://github.com/features/ai/github-app>)中使用斜杠命令，或[查看我们的文档](<https://docs.github.com/copilot/reference/github-copilot-app-reference/slash-commands>)以获取可用命令的列表。

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot 应用程序](<https://github.blog/tag/github-copilot-app/>)
* [斜杠命令](<https://github.blog/tag/slash-commands/>)

## 撰写者

![杰克琳·卡罗尔](<https://avatars.githubusercontent.com/u/13090072?v=4&s=200>)

### [杰克琳·卡罗尔](<https://github.blog/author/jacklynlee31/>)

[@jacklynlee31](<https://github.com/jacklynlee31>)

Jacklyn 是 GitHub 的内容作家，也是一位热爱讲好故事的技术作家。


## 原文对照

<details>
<summary>展开完整原文</summary>

# A guide to slash commands in the GitHub Copilot app

If you’ve used slash commands in the GitHub Copilot CLI, you already know how powerful a quick `/` can be. In the GitHub Copilot app, slash commands take that idea further, giving you shortcuts for managing sessions, navigating projects, and customizing your Copilot workflow.

## What are slash commands?

Slash commands are text shortcuts you type directly into the GitHub Copilot app’s chat composer. Start by typing `/` and an autocomplete menu appears, showing the commands available in your current context. It’s a small character with a lot of potential, opening the door to shortcuts that help you work with Copilot in new ways.

If you’re coming from the CLI, here’s the key difference: CLI slash commands are designed around a terminal-first workflow. Things like adding directories, setting your working directory, and managing terminal access happen through commands. This makes sense, because the CLI lives inside your terminal where there’s no visual interface.

|  |
| --- |
| 💡 **Tip**: If you’ve used slash commands in the Copilot CLI, you’ll notice some familiar faces. Commands like `/clear` and `/model` work in both places. But the GitHub Copilot app-specific commands are tailored for the multi-session workflow that the desktop app provides. |

The app, on the other hand, provides a visual interface for managing context. File access commands like `/add-dir` or `/cwd` aren’t needed since the app manages project context automatically. App slash commands are more about workflows. You can navigate between sessions, manage projects, and control how the agent works.

## Why use slash commands?

Slash commands may look like simple shortcuts, but they can change how you interact with Copilot. They help you move faster, stay focused, and quickly access the workflows you need. Instead of digging through options or breaking your focus to find the right tool, you can type a command and keep moving. A single `/` opens up the list of slash commands that can help you move faster, explore new ideas, and get the most out of the app.

Let’s take a look at some of the slash commands available in the GitHub Copilot app and how they can fit into your everyday workflows.

## Before you write code, make a `/plan`

Good code starts with a good plan. `/plan` helps you break down a task before you start writing code, think through your approach, identify potential challenges, and decide what needs to happen next. It also switches your session into **Plan** mode, which you can also select from the **Mode** dropdown in the chat composer.

* **Plan a new feature.** Break down new feature ideas before jumping into implementation. Have Copilot identify files, components, and dependencies so you have a clearer path forward.
* `/plan I need to add two-factor authentication to our application. Help me break down the work involved, identify what files need to change, and outline an implementation approach.`
* **Prepare for a large refactor.** Map out complex changes before touching your code. Uncover potential risks and develop an incremental approach for making large changes safely.
* `/plan We want to refactor our notification system code to make it easier to support new channels like push notifications. Help me understand the changes needed and create an incremental migration plan.`
* **Triage and fix bugs.** If you know something is wrong but aren’t sure where to start, `/plan` can help you explore possible causes and outline the steps needed to diagnose and resolve the problem.
* `/plan Users are reporting that our checkout flow randomly fails after payment processing. Help me investigate possible causes and create a plan to diagnose and fix the issue.`

## Let Copilot play devil’s advocate with `/spar`

Sometimes the best way to validate an idea is to challenge it. `/spar` is like that one teammate who raises their hand and asks, “Have we thought about what happens when this goes wrong?” It helps you pressure-test your approach by having Copilot question your assumptions and point out potential risks or tradeoffs before you commit to a solution. Here are a few ways you can use it:

* **Validate an architecture choice.** Pitch your plan to use Redis for caching and have Copilot question your invalidation strategy, scalability, or whether another approach better fits your workload.
* `/spar I'm planning to use Redis as a caching layer for our product API. Challenge my approach and point out any scalability or consistency concerns I may have missed.`
* **Compare implementation options.** Ask Copilot to debate the pros and cons of REST versus GraphQL, or synchronous versus asynchronous processing, based on your application’s requirements.
* `/spar Help me decide between REST and GraphQL for a customer-facing API. Ask questions, challenge my assumptions, and recommend which approach fits best for an app with mobile clients.`
* **Review a migration plan.** Walk through a database migration or infrastructure change and have Copilot identify edge cases, risks, or rollout concerns before you begin.
* `/spar I'm migrating our database to a new managed service with minimal downtime. Poke holes in my migration plan and identify any risks or edge cases I should account for.`
* **Challenge a performance optimization.** Share an optimization you’re considering and ask Copilot to point out hidden bottlenecks, unintended side effects, or simpler alternatives.
* `/spar I'm planning to lazy load most of the components on my site to improve initial load time. Critique my approach and tell me where it could hurt user experience or introduce unnecessary complexity.`

## `/autopilot` take the wheel

Once you have a `/plan`, the next step is turning that idea into working code. `/autopilot` helps you work through implementation, make changes, and iterate as needed. Instead of managing each individual step, give Copilot a goal and let it work through the steps needed to complete the task. It also switches your session into **Autopilot** mode, which you can also select from the **Mode** dropdown in the chat composer.

* **Implement a new feature.** Hand off a task and let Copilot work through the implementation steps.
* `/autopilot Add support for exporting user reports as CSV files. Identify the files that need changes, implement the feature, and update any relevant tests.`
* **Complete a larger maintenance task.** Use `/autopilot` for tasks that require multiple steps, such as updating dependencies, refactoring code, or improving documentation.
* `/autopilot Update this project to the latest version of React. Identify breaking changes, update the code where needed, and make sure the test suite passes.`

## Talk it through with `/rubber-duck`

I’ve learned from personal experience that talking things through with your cat isn’t always helpful. Their listening skills are questionable at best, and their debugging advice usually ends with them sitting on my keyboard or chewing my wires. `/rubber-duck` gives you something even better: a fresh set of eyes.

It uses a different model to independently review your work, helping surface blind spots, question assumptions, and catch issues your primary model may have missed. It’s especially useful for complex refactors, architectural decisions, migration plans, or any time you want a second opinion before moving forward.

* **Get a second opinion on a plan.** Before you start implementing a complex feature, have a different model independently review your plan and point out assumptions, missing steps, or potential risks.  
  `/rubber-duck Review the implementation plan we've created for adding two-factor authentication. Identify any blind spots, edge cases, or risks that we may have overlooked.`
* **Review a large refactor.** After making significant changes, ask /rubber-duck to critique the approach and highlight anything that could be simplified, improved, or handled differently.  
  `/rubber-duck Review the refactoring we've completed for the notification system. Look for architectural concerns, unnecessary complexity, or areas that could be improved before I open a pull request.`
* **Validate a migration strategy.** Before rolling out a complex migration, use a second model to independently evaluate your approach and identify rollout or reliability concerns.  
  `/rubber-duck Review our database migration plan and implementation. Point out any blind spots, rollback concerns, or edge cases we should address before deployment.`

## Turn conversations into interactive experiences with `/create-canvas`

Not every problem is best solved through a chat window. `/create-canvas` lets you create interactive interfaces directly from a conversation with Copilot. Instead of working through information in a long chat, you can turn it into a visualization, dashboard, or custom workflow you can interact with. For example:

* `/create-canvas Create an interactive diagram showing how services in this application connect.`
* `/create-canvas Create an issue triage board that lets me review and categorize open issues.`

For more information and examples on creating canvases, [check out our blog post](<https://github.blog/ai-and-ml/github-copilot/how-to-build-interactive-experiences-with-canvases/>).

## When one task becomes many, use `/orchestrate`

Not every task fits neatly into a single workflow. Sometimes you need to make changes across multiple repositories or tackle several related tasks at once. `/orchestrate` helps you coordinate work across sessions and repositories by breaking larger efforts into smaller tasks that can move forward in parallel.

* **Coordinate changes across repositories.** When a feature touches multiple codebases, `/orchestrate` can help you track the work and coordinate updates across each repository.
* `/orchestrate I need to add support for a new authentication flow across our frontend, backend, and shared repositories. Help me break down the work and coordinate the changes needed in each codebase.`
* **Manage parallel development tasks.** For larger projects, Copilot can divide the work into focused efforts so multiple pieces can move forward together.
* `/orchestrate Prepare this feature for release. Identify the work needed for implementation, testing, documentation, and deployment, then help coordinate each task.`

## Your next workflow starts with `/`

You don’t need to memorize any commands to get started. Pick a few slash commands that match how you work and build from there. The autocomplete menu has your back for the rest. Type `/` and start exploring! You can also [read our docs](<https://docs.github.com/copilot/concepts/agents/github-copilot-app>) to learn more about the GitHub Copilot app.

[Try slash commands in the GitHub Copilot app](<https://github.com/features/ai/github-app>), or [check out our docs](<https://docs.github.com/copilot/reference/github-copilot-app-reference/slash-commands>) for a list of available commands.

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot app](<https://github.blog/tag/github-copilot-app/>)
* [slash commands](<https://github.blog/tag/slash-commands/>)

## Written by

![Jacklyn Carroll](<https://avatars.githubusercontent.com/u/13090072?v=4&s=200>)

### [Jacklyn Carroll](<https://github.blog/author/jacklynlee31/>)

[@jacklynlee31](<https://github.com/jacklynlee31>)

Jacklyn is a Content Writer at GitHub and a technical writer with a love for good storytelling.


</details>
