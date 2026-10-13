# Copilot 桌面应用入门

介绍项目、多个会话、交互画布和 Agent 合并等操作入口，帮助开始在桌面应用中组织开发任务。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-07-27](<https://github.blog/ai-and-ml/github-copilot/github-copilot-app-for-beginners-getting-started/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/github-copilot-app-for-beginners-getting-started/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/Copilot 桌面应用入门.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub Copilot 适合初学者的应用程序：入门

AI 编码工具通常以聊天窗口开始。虽然这适用于快速提问或生成代码，但真正的软件开发很少是直线进行的。前一分钟你还在修复错误，下一分钟你就在审查拉取请求，深入研究代码库中不熟悉的部分，或者探索一个新想法。

[GitHub Copilot 应用程序](<https://github.com/features/ai/github-app?utm_source=blog-app-for-beginners-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>) 专为此类工作流程而设计。它不是将 AI 视为单个对话，而是为您提供了一个工作区，您可以在其中管理多个 agent 会话、在任务之间切换而不失去动力，并在工作流程的不同部分中使用 AI agents。

## 你的工作从一个项目开始

Agent 会话连接到一个项目，为每个会话提供特定任务所需的存储库上下文。

在 GitHub Copilot 应用程序主屏幕中，您可以选择以前使用过的项目，也可以从 GitHub 或本地计算机添加新项目。选择项目后，您可以使用开始处理任务所需的代码库、文件和工具启动会话。

例如，您可能想要向现有应用程序添加面包屑导航组件。您无需手动搜索文件来找到进行更改的正确位置，而是可以描述要进行的更新。该会话可以检查项目、识别相关文件、进行更改并运行测试以帮助验证工作。

通过在已连接的项目的情况下开始每个会话，您可以花更多的时间工作，并减少准备环境的时间。

## 保持多个线程移动

选择项目并开始 agent 会话后，您可以为其他问题、想法或任务创建其他会话，而无需中断现有工作。 **快速聊天**可让您从Copilot应用程序主屏幕开始新对话。每次对话都可以专注于不同的工作领域，为您提供专门的空间来探索想法、提出问题或进行变革。

例如，您可以打开 ** 快速聊天 ** 会话，向 Copilot 询问 Copilot，例如工作树在应用程序中的功能，而另一个 agent 会话则继续处理项目更新。您还可以使用 ** 快速聊天 ** 来调查您的代码库的工作原理、探索潜在的方法或在决定如何继续之前收集上下文。

当您返回原始会话时，您可以从上次中断的位置继续，查看更改，并进行推进任务所需的任何更新。

多个会话可让您跟踪不同的工作线程，而不会丢失每个任务的位置。

## 让您的作品与画布互动

在进行 UI 更改时，查看结果与检查其背后的代码同样重要。 GitHub Copilot 应用程序可让您直接在工作流程中打开浏览器画布，为您提供预览应用程序并根据您所看到的内容进行更改的方法。

画布是围绕工作工件构建的共享交互式空间，例如计划、看板、清单或正在运行的应用程序。它提供了您的工作和对话的可视化表示，因此您可以超越基于文本的任务视图。

您可以使用 `/create-canvas` 斜杠命令在 GitHub Copilot 应用程序中创建浏览器画布，而不是打开新终端并启动单独的浏览器。

例如，在要求 Copilot 更新 UI 组件后，您可以使用以下命令创建画布： `/create-canvas Open this app in a browser canvas`

这将在画布中打开您的应用程序，您可以在其中预览结果。如果需要调整，您可以**启用画布开发模式**并使用**挑选和抛光**直接在画布中选择元素并将它们用作下一个请求的上下文。您可以指向页面的特定部分，请求更新，并继续优化结果。

## 通过 Agent 合并保持工作顺利进行

更改准备就绪后，下一步是创建拉取请求并完成标准 CI 和审核流程。 Agent 合并通过监视拉取请求并协助完成审查期间出现的任务，有助于将工作流程扩展到初始代码更改之外。

要启动 Agent 合并工作流程，请在 Copilot 应用中打开拉取请求选项，然后选择 **Agent 合并 **。从那里，您可以选择 Agent 合并可以采取哪些操作，例如解决审核反馈、帮助解决 CI 失败或处理合并冲突。

启用 **Agent 合并 ** 后，它会在拉取请求通过审核和 CI 流程时对其进行监控。如果出现问题，它可以帮助解决请求的更改并准备合并拉取请求。一旦通过了所需的检查，您就可以选择合并拉取请求。

## 开始探索 Copilot 应用程序

Copilot 应用程序将开发工作流程的不同部分汇集到一处。从一个项目开始。从那里，您可以为不同的工作线程创建单独的会话，创建画布来可视化和优化您的工作，并使用 **Agent 合并 ** 在拉取请求流程中保持更改的进展。

还有更多内容可供探索，包括自定义和扩展工作流程的其他方法。学习GitHub Copilot应用程序的最佳方法就是亲自尝试。

您的积压工作中有一项任务吗？尝试使用 [GitHub Copilot 应用程序 ](<https://github.com/features/ai/github-app?utm_source=blog-app-for-beginners-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>)，看看如何使用 AI agents 来探索、构建和发布。

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot 应用程序](<https://github.blog/tag/github-copilot-app/>)
* [GitHub Copilot 适合初学者的应用程序](<https://github.blog/tag/github-copilot-app-for-beginners/>)

## 撰写者

![克里斯托弗·哈里森](<https://avatars.githubusercontent.com/u/6109729?v=4&s=200>)

### [克里斯托弗·哈里森](<https://github.blog/author/geektrainer/>)

[@geektrainer](<https://github.com/geektrainer>)

高级开发者倡导者，GitHub


## 原文对照

<details>
<summary>展开完整原文</summary>

# GitHub Copilot app for Beginners: Getting started

AI coding tools often begin with a chat window. While that works for quick questions or generating code, real software development rarely happens in a straight line. One minute you’re fixing a bug, the next you’re reviewing a pull request, digging into an unfamiliar part of the codebase, or exploring a new idea.

The [GitHub Copilot app](<https://github.com/features/ai/github-app?utm_source=blog-app-for-beginners-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>) is designed for that kind of workflow. Instead of treating AI as a single conversation, it gives you a workspace where you can manage multiple agent sessions, switch between tasks without losing momentum, and work with AI agents across the different parts of your workflow.

## Your work starts with a project

Agent sessions are connected to a project, giving each session the repository context needed for a specific task.

From the GitHub Copilot app home screen, you can choose a project you’ve worked with before or add a new one from GitHub or your local machine. Once a project is selected, you can start a session with the codebase, files, and tools needed to begin working on a task.

For example, you might want to add a breadcrumb navigation component to an existing application. Instead of manually searching through files to find the right place to make the change, you can describe the update you want to make. The session can examine the project, identify relevant files, make the changes, and run tests to help validate the work.

By starting each session with the project already connected, you can spend more time working and less time preparing your environment.

## Keep multiple threads moving

Once you’ve selected a project and started an agent session, you can create additional sessions for other questions, ideas, or tasks without interrupting your existing work. **Quick Chat** lets you start a new conversation from the Copilot app home screen. Each conversation can focus on a different area of work, giving you a dedicated space to explore ideas, ask questions, or work through changes.

For example, you can open a **Quick Chat** session to ask Copilot about Copilot, such as how worktrees function in the app, while another agent session continues working on a project update. You can also use **Quick Chat** to investigate how your codebase works, explore potential approaches, or gather context before deciding how to move forward.

When you return to your original session, you can pick up where you left off, review the changes, and make any updates needed to move the task forward.

Multiple sessions let you follow different threads of work without losing track of where each task stands.

## Make your work interactive with canvas

When working on a UI change, seeing the result can be just as important as reviewing the code behind it. The GitHub Copilot app lets you open a browser canvas directly within your workflow, giving you a way to preview your application and make changes based on what you see.

A canvas is a shared, interactive space built around work artifacts, such as a plan, a kanban board, a checklist, or a running application. It provides a visual representation of your work alongside your conversation, so you can move beyond a text-based view of the task.

Rather than opening a new terminal and launching a separate browser, you can create a browser canvas in the GitHub Copilot app using the `/create-canvas` slash command.

For example, after asking Copilot to update a UI component, you can create a canvas with: `/create-canvas Open this app in a browser canvas`

This opens your application in a canvas where you can preview the result. If something needs adjusting, you can **Enable Canvas Dev Mode** and use **Pick & Polish** to select elements directly in the canvas and use them as context for your next request. You can point to a specific part of the page, request an update, and continue refining the result.

## Keep work moving with Agent Merge

Once your changes are ready, the next step is creating a pull request and moving through the standard CI and review process. Agent Merge helps extend that workflow beyond the initial code change by monitoring the pull request and assisting with tasks that come up during review.

To start an Agent Merge workflow, open the pull request options in the Copilot app and select **Agent Merge**. From there, you can choose which actions Agent Merge can take, such as addressing review feedback, helping resolve CI failures, or handling merge conflicts.

After **Agent Merge** is enabled, it monitors the pull request as it moves through the review and CI process. If issues come up, it can help address requested changes and prepare the pull request for merge. Once the required checks have passed, you can choose to merge the pull request.

## Start exploring the Copilot app

The Copilot app brings together the different parts of your development workflow in one place. Start with a project. From there, you can create separate sessions for different threads of work, create a canvas to visualize and refine your work, and keep changes moving through the pull request process with **Agent Merge**.

There’s more to explore, including additional ways to customize and extend your workflow. The best way to learn the GitHub Copilot app is to try it yourself.

Have a task that’s been sitting in your backlog? Try it with the [GitHub Copilot app](<https://github.com/features/ai/github-app?utm_source=blog-app-for-beginners-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>) and see how you can explore, build, and ship with AI agents.

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot app](<https://github.blog/tag/github-copilot-app/>)
* [GitHub Copilot app for Beginners](<https://github.blog/tag/github-copilot-app-for-beginners/>)

## Written by

![Christopher Harrison](<https://avatars.githubusercontent.com/u/6109729?v=4&s=200>)

### [Christopher Harrison](<https://github.blog/author/geektrainer/>)

[@geektrainer](<https://github.com/geektrainer>)

Senior Developer Advocate, GitHub


</details>
