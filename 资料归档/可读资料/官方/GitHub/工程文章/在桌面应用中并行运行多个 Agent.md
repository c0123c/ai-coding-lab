# 在桌面应用中并行运行多个 Agent

说明会话、Git 工作树与上下文的关系，用实际流程展示如何同时推进多个任务并保持修改隔离。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-09-03](<https://github.blog/ai-and-ml/github-copilot/github-copilot-app-for-beginners-run-several-agents-at-once/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/github-copilot-app-for-beginners-run-several-agents-at-once/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/在桌面应用中并行运行多个 Agent.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 适合初学者的 GitHub Copilot 应用程序：一次运行多个 agents

在同一个项目上运行多个 AI agents 看起来完全是混乱的，因为你的开发厨房里有太多厨师。但使用 [GitHub Copilot 应用程序 ](<https://github.com/features/ai/github-app?utm_source=blog-copilot-app-ep5&utm_medium=blog&utm_campaign=copilot-app-for-beginners-series>)，这些 agents 可以单独工作并且不会相互干扰，让您在更短的时间内完成更多的工作。

将并行 agent 会话想象成去自助洗衣店。您可以在各自的机器中同时启动多批衣物。您可以使用自己的设置来设置每个洗衣机，并且不会影响其他负载。最重要的是，您不必等待一个任务完成才能开始另一个任务。

让我们看看它是如何工作的。

## Agent 会话、Git 工作树和上下文

agent 会话是您从头到尾分配给 Copilot 的任务。

在GitHub Copilot应用程序中，您可以通过会话视图管理多个会话。每张卡片都会显示其标题以及 agent 完成其任务的进度。

每个会话独立于其他会话运行。这意味着您可以随时开始新的会话，并且已经运行的会话不会受到干扰。

但这才是真正的魔法发生的地方。 GitHub Copilot 应用程序中的每个 agent 会话都可以在其自己的 Git 工作树上运行。由于每个会话都是隔离的，因此它们可以同时并行运行。

这意味着您将时间花在审查和做出决定上，而不是在工作中观察您的 agents。

另外，每个会话都保留自己的上下文，因此您可以在它们之间自由切换。每个都准确地从你离开的地方继续，你永远不需要重新解释你在做什么。更少的上下文切换意味着你感觉不那么分散。

## 这看起来像什么

让我们看一个示例存储库，`tailspin-toys`。今天我想对这个项目做三件事：添加资助排序、执行可访问性审查以及对该项目运行一些测试。

第一步是要求 Copilot 构建资助排序功能。刚开始时，打开一个新会话并要求 Copilot 执行可访问性审核。当这个过程开始时，开始提示 Copilot 在第三个会话中运行一些测试。

在会话视图中，您可以跟踪所有这些进展并在完成时查看结果。或者，您可以完全离开，喝杯咖啡，知道您的工作正在顺利进行，而无需您照顾。

## 开始使用

准备好感受并行 agent 会话的力量吗？

尝试在[GitHub Copilot应用程序](<https://github.com/features/ai/github-app?utm_source=blog-copilot-app-ep5&utm_medium=blog&utm_campaign=copilot-app-for-beginners-series>)中同时启动两个小任务。这是一种独立观察任务进展的低风险方式。

开始使用[GitHub Copilot应用程序>](<https://github.com/features/ai/github-app?utm_source=blog-copilot-app-ep5&utm_medium=blog&utm_campaign=copilot-app-for-beginners-series>)

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot 应用程序](<https://github.blog/tag/github-copilot-app/>)
* [GitHub Copilot 适合初学者的应用程序](<https://github.blog/tag/github-copilot-app-for-beginners/>)

## 撰写者

![凯拉肉桂](<https://avatars.githubusercontent.com/u/48369326?v=4&s=200>)

### [凯拉肉桂](<https://github.blog/author/cinnamonmsft/>)

[@cinnamon-msft](<https://github.com/cinnamon-msft>)

高级 AI 开发者工具倡导者


## 原文对照

<details>
<summary>展开完整原文</summary>

# GitHub Copilot app for Beginners: Run several agents at once

Running multiple AI agents on the same project seems like pure chaos, with too many cooks in your development kitchen. But with the [GitHub Copilot app](<https://github.com/features/ai/github-app?utm_source=blog-copilot-app-ep5&utm_medium=blog&utm_campaign=copilot-app-for-beginners-series>), these agents work separately and don’t interfere with each other, allowing you to get more done in less time.

Think of parallel agent sessions like a trip to the laundromat. You can start multiple loads of laundry at the same time, in their own machines. You can set each washer with its own settings, and it won’t impact the other loads. Most importantly, you don’t have to wait for one to finish before you can start another one.

Let’s look at how it works.

## Agent sessions, Git worktrees, and context

An agent session is a task you’ve given Copilot, start to finish.

In the GitHub Copilot app, you can manage several sessions through the sessions view. Each card shows its title and how far along the agent is in completing its task.

Each session runs independently of the others. That means you can start a new session whenever you want, and the ones already running won’t be disturbed.

But here’s where the real magic happens. Each agent session in the GitHub Copilot app can run on its own Git worktree. Since each session is isolated, they can run in parallel, all at the same time.

That means you spend your time reviewing and making decisions rather than watching your agents at work.

Plus, each session keeps its own context, so you can switch between them freely. Each picks up exactly where you left it, and you never have to re-explain what you were doing. Less context switching means you feel less scattered.

## What this looks like

Let’s look at an example repository, `tailspin-toys`. There are three things I want to do to this project today: add funded sort, perform an accessibility review, and run some tests on this project.

The first step is to ask Copilot to build the funded sort feature. Just as it gets started, open a new session and ask Copilot to perform an accessibility review. As that gets under way, start prompting Copilot to run some tests in a third session.

In the session view, you can keep track of all of these as they progress and review the results as they finish. Or you can step away entirely, grab a cup of coffee, and know your work is happening without you babysitting it.

## Get started

Ready to feel the power of parallel agent sessions?

Try starting two small tasks at the same time in the [GitHub Copilot app](<https://github.com/features/ai/github-app?utm_source=blog-copilot-app-ep5&utm_medium=blog&utm_campaign=copilot-app-for-beginners-series>). It’s a low-stakes way to watch your tasks progress independently.

Start using the [GitHub Copilot app >](<https://github.com/features/ai/github-app?utm_source=blog-copilot-app-ep5&utm_medium=blog&utm_campaign=copilot-app-for-beginners-series>)

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot app](<https://github.blog/tag/github-copilot-app/>)
* [GitHub Copilot app for Beginners](<https://github.blog/tag/github-copilot-app-for-beginners/>)

## Written by

![Kayla Cinnamon](<https://avatars.githubusercontent.com/u/48369326?v=4&s=200>)

### [Kayla Cinnamon](<https://github.blog/author/cinnamonmsft/>)

[@cinnamon-msft](<https://github.com/cinnamon-msft>)

Senior AI Developer Tools Advocate


</details>
