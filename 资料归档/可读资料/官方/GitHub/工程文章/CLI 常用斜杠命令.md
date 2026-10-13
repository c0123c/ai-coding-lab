# CLI 常用斜杠命令

按模型选择、上下文、会话、代码变更、工作目录和权限整理命令，便于控制终端 Agent 的状态与工作范围。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-06-15](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-overview-of-common-slash-commands/>)；更新：[2026-06-15](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-overview-of-common-slash-commands/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-overview-of-common-slash-commands/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/CLI 常用斜杠命令.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub Copilot CLI 初学者：常用斜杠命令概述

欢迎回到初学者的GitHub CopilotCLI！在本系列中（以 [ 视频 ](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>) 和 [ 博客 ](<https://github.blog/tag/github-copilot-cli-for-beginners/>) 格式提供），我们将为您提供开始使用 [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep1-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) 所需的一切。到目前为止，在本系列中，我们已经介绍了[如何开始](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/>)和[何时使用交互和非交互模式](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-interactive-v-non-interactive-mode/>)。在本版本中，我们将了解什么是斜杠命令、它们为何重要以及如何使用斜杠命令有效地控制 GitHub Copilot。您可以直接从终端完成切换模型、检查令牌使用情况以及恢复过去的会话等任务。

让我们深入了解一下！

## 了解 GitHub Copilot CLI 中的斜杠命令

在使用 Copilot CLI 时，早期需要学习的最强大的概念之一是 ** 斜线命令。** 斜线命令是内置控件，您可以直接从命令行访问。作为 Copilot CLI 中的控制界面，斜杠命令允许您：

* 指导Copilot的行为
* 检查变更
* 管理上下文
* 跨会议和项目高效移动
* 保持权限整洁

斜杠命令可以被认为是与 Copilot CLI 交互的命令中心。要查看所有可用选项，只需在命令行中键入 `/` 即可获取当前支持的所有斜杠命令的可滚动列表。

让我们来看看一些最受欢迎的。

## 选择正确的型号

不同的模型针对不同类型的工作进行了优化。如果要切换型号，请在命令行中输入`/model`。这将显示可用模型的列表，以及关键详细信息，例如：

* **功能**：有些功能更适合快速、轻量级的任务，例如重构，而其他功能则更有效地处理更深层次的推理，例如功能规划。
* **可用性**：该列表可能会有所不同，具体取决于您的计划或组织的设置。
* **成本**：每个型号右侧显示的数字表示成本乘数，帮助您在与您的计划相关的性能和使用量之间选择适当的平衡。

选择正确的模型可以显着影响速度和结果。

## 管理上下文和令牌使用

Copilot CLI 在上下文窗口中运行，该窗口决定它在会话期间可以“记住”多少信息。如果您想检查当前的使用情况，请输入 `/context` 以了解您还剩下多少令牌，以及系统使用情况和可用缓冲区。

如果您发现空间不足，可以通过在命令行中键入 `/compact` 来释放空间。这总结了您当前的对话，以便您可以继续，而无需开始新的会话。 Copilot CLI 将在您接近限制时自动执行此操作，但如果您想转换到新任务或在会话中清理上下文，您也可以手动执行此操作。

如果您想重新开始并完全重置您的环境，您可以使用 `/clear` 完全清除会话。

## 跨会话工作

如果您想恢复之前的会话，可以输入 `/resume`。这将显示您之前进行过的会话列表，包括本地和远程会话。输入上一个会话将显示您的会话历史记录，您可以从上次中断的地方继续。

## 检查变更

当您与 Copilot 一起对项目进行更改时，跟踪更改的内容非常重要。如果您想查看更改内容，请运行 `/diff` 查看最近的更新。这使您可以清楚地了解会话期间进行的修改，以便您可以在继续之前验证更改。

## 导航项目和目录

如果您想跨存储库或目录工作，则不必退出 Copilot。您可以输入 `/cwd` 将工作目录更改为另一个存储库。这使您可以将 Copilot 的工作范围限制在项目的特定部分，并帮助您在跨代码库执行多任务时保持高效。

## 管理工具权限

过去，您可能已授予 Copilot CLI 执行编辑文件等操作的权限。假设您要切换到一个想要更加小心的存储库并希望重置这些权限：您可以通过运行 `/reset-allowed-tools` 来做到这一点。

## 随身携带这个

使用这些斜线命令可以让您更好地控制 Copilot CLI，并且您对它们越熟悉，您的工作流程就会变得越谨慎。

无论您是切换模型、管理上下文还是跨项目导航，使用 CLI 中的斜线命令都可以为您提供保持控制所需的工具。如果您还没有：打开终端，输入 `/`，然后探索！还有更多斜杠命令有待发现。

快乐编码！

**想尝试GitHub Copilot CLI？** [立即阅读文档](<https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli>)和[开始使用](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep1-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>)。

**更多资源可供探索：**

* [GitHub Copilot CLI 初学者视频系列](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>)
* [GitHub Copilot CLI 初学者：开始使用 GitHub Copilot CLI](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/?utm_source=blog-announcement-cli-tutorial&utm_medium=blog&utm_campaign=universe25post>)
* [GitHub Copilot CLI 初学者：交互模式与非交互模式](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-interactive-v-non-interactive-mode/>)
* [GitHub Copilot CLI 101：如何从命令行使用GitHub Copilot](<https://github.blog/ai-and-ml/github-copilot-cli-101-how-to-use-github-copilot-from-the-command-line/?utm_source=blog-announcement-cli-tutorial&utm_medium=blog&utm_campaign=universe25post>)
* [GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/cli-best-practices?utm_campaign=copilot-brand&utm_medium=sem&utm_source=google&ocid=AIDcmmh2h80ugd_SEM__k_CjwKCAjw-dfOBhAjEiwAq0RwI0TIeyL9bjDmXlY26JKPbDvHGzBcaZUa4LR8u8SJuGbIke6e7U2YXRoCzGQQAvD_BwE_k_>) 的最佳实践

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)
* [GitHub Copilot CLI 初学者用](<https://github.blog/tag/github-copilot-cli-for-beginners/>)

## 撰写者

![凯拉肉桂](<https://avatars.githubusercontent.com/u/48369326?v=4&s=200>)

### [凯拉肉桂](<https://github.blog/author/cinnamonmsft/>)

[@cinnamon-msft](<https://github.com/cinnamon-msft>)

高级 AI 开发者工具倡导者


## 原文对照

<details>
<summary>展开完整原文</summary>

# GitHub Copilot CLI for Beginners: Overview of common slash commands

Welcome back to GitHub Copilot CLI for Beginners! In this series (available in [video](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>) and [blog](<https://github.blog/tag/github-copilot-cli-for-beginners/>) format), we’ll give you everything you need to get started using [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep1-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>). So far in this series, we’ve covered [how to get started](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/>) and [when to use interactive and non-interactive modes](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-interactive-v-non-interactive-mode/>). In this edition, we’ll learn what slash commands are, why they matter, and how to use slash commands to control GitHub Copilot efficiently. You can complete tasks like switching models, checking token usage, and resuming past sessions right from your terminal.

Let’s dive in!

## Understanding slash commands in GitHub Copilot CLI

When working in Copilot CLI, one of the most powerful concepts to learn early on is **slash commands.** Slash commands are built-in controls that you can access directly from the command line. Acting as your control surface within Copilot CLI, slash commands allow you to:

* Guide Copilot’s behavior
* Inspect changes
* Manage context
* Move efficiently across sessions and projects
* Keep permissions tidy

Slash commands can be thought of as your command center for interacting with Copilot CLI. To look at all of the options available, just type `/` in the command line for a scrollable list of all currently supported slash commands.

Let’s take a look at some of the most popular ones.

## Choosing the right model

Different models are optimized for different kinds of work. If you want to switch models, type `/model` into the command line. This will display a list of available models, along with key details like:

* **Capabilities**: Some are better for quick, lightweight tasks like refactoring, while others more efficiently handle deeper reasoning such as feature planning.
* **Availability**: The list may vary depending on your plan or organization’s settings.
* **Cost**: Numbers shown on the right of each model indicate cost multiplier, helping you choose the right balance between performance and usage in relation to your plan.

Choosing the right model can significantly impact both speed and results.

## Managing context and token usage

Copilot CLI operates within a context window, which determines how much information it can “remember” during a session. If you want to check your current usage, type `/context` to learn how many tokens you have left, along with system usage and available buffer.

If you find that you’re running low on space, you can free up space by typing `/compact` in the command line. This summarizes your current conversation so you can continue without having to start a new session. Copilot CLI will do this automatically when you approach the limit, but you can also do this manually if you want to transition to a new task or clean up context mid-session.

If you’d rather start fresh and completely reset your environment, you can use `/clear` to clear the session entirely.

## Working across sessions

If you want to resume a previous session, you can type `/resume`. This will bring up a list of previous sessions you’ve had, including both local and remote sessions. Entering a previous session will show you your session history, and you can pick up right where you left off.

## Inspecting changes

As you work with Copilot to make changes to your project, it’s important to keep track of what’s changed. If you want to see what the changes are, run `/diff` to see recent updates. This gives you a clear view of what modifications were made during your session, so you can validate changes before moving forward.

## Navigating projects and directories

If you want to work across repositories or directories, you don’t have to exit Copilot. You can type `/cwd` to change your working directory to another repository. This allows you to scope Copilot’s work to a specific part of your project and helps you stay efficient while multitasking across codebases.

## Managing tool permissions

In the past, you might have granted Copilot CLI permission to perform actions like editing files. Say you’re switching to a repository you want to be more careful in and want to reset those permissions: you can do so by running `/reset-allowed-tools`.

## Take this with you

Using these slash commands gives you even better control over Copilot CLI—and the more familiar you become with them, the more deliberate your workflow becomes.

Whether you’re switching models, managing context, or navigating across projects, using slash commands in CLI gives you the tools you need to stay in control. And if you haven’t already: open up your terminal, type `/`, and explore! There are many more slash commands to discover.

Happy coding!

**Looking to try GitHub Copilot CLI?** [Read the docs](<https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli>) and [get started](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep1-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) today.

**More resources to explore:**

* [GitHub Copilot CLI for Beginners video series](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>)
* [GitHub Copilot CLI for Beginners: Getting started with GitHub Copilot CLI](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/?utm_source=blog-announcement-cli-tutorial&utm_medium=blog&utm_campaign=universe25post>)
* [GitHub Copilot CLI for Beginners: Interactive v. non-interactive mode](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-interactive-v-non-interactive-mode/>)
* [GitHub Copilot CLI 101: How to use GitHub Copilot from the command line](<https://github.blog/ai-and-ml/github-copilot-cli-101-how-to-use-github-copilot-from-the-command-line/?utm_source=blog-announcement-cli-tutorial&utm_medium=blog&utm_campaign=universe25post>)
* [Best practices for GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/cli-best-practices?utm_campaign=copilot-brand&utm_medium=sem&utm_source=google&ocid=AIDcmmh2h80ugd_SEM__k_CjwKCAjw-dfOBhAjEiwAq0RwI0TIeyL9bjDmXlY26JKPbDvHGzBcaZUa4LR8u8SJuGbIke6e7U2YXRoCzGQQAvD_BwE_k_>)

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)
* [GitHub Copilot CLI for Beginners](<https://github.blog/tag/github-copilot-cli-for-beginners/>)

## Written by

![Kayla Cinnamon](<https://avatars.githubusercontent.com/u/48369326?v=4&s=200>)

### [Kayla Cinnamon](<https://github.blog/author/cinnamonmsft/>)

[@cinnamon-msft](<https://github.com/cinnamon-msft>)

Senior AI Developer Tools Advocate


</details>
