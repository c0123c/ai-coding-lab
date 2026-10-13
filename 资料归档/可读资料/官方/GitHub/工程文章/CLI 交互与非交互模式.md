# CLI 交互与非交互模式

比较对话式使用和脚本式执行，介绍两种模式的调用方式与历史会话恢复，帮助选择合适运行方式。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-04-30](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-interactive-v-non-interactive-mode/>)；更新：[2026-04-30](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-interactive-v-non-interactive-mode/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-interactive-v-non-interactive-mode/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/CLI 交互与非交互模式.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub Copilot CLI 初学者：交互模式与非交互模式

欢迎来到GitHub CopilotCLI初学者！在本系列中（以 [ 视频 ](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>) 和 [ 博客 ](<https://github.blog/tag/github-copilot-cli-for-beginners/>) 格式提供），我们将为您提供开始使用 [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) 所需的一切，从您的第一个提示到像专业人士一样浏览命令行的提示！

在本博客中，我们将介绍 CLI 的两种主要模式：交互式和非交互式。您将了解这两种模式之间的差异、如何进入它们以及它们最有用的用途。

让我们深入了解一下！

## GitHub Copilot CLI交互模式是什么？

交互模式是一种来回的、类似聊天的体验。当您使用 Copilot 启动 Copilot CLI 时，您已经处于交互模式 - 这是默认模式。当您希望在不进入会话的情况下快速获得一次性答案时，非交互模式是一个单独的选项。 （稍后将详细介绍非交互模式！）

在交互模式下，您可以询问 GitHub Copilot 一个问题，查看其响应，然后跟进问题或其他提示 - 所有这些都在同一个会话中进行。这种模式适合那些想要亲自使用 Copilot 并随时进行迭代的人。

进入交互模式的方法如下：

* 在命令行中，输入 `copilot` 并点击 `Enter`。
* Copilot 可能会要求您信任此文件夹，因为它需要读取和修改文件的权限。
* 询问 Copilot 一个问题，例如“我如何在本地运行这个项目？”
* Copilot会给你指示，你可以自己做。但如果你想协同工作，你可以问Copilot：“你能为我运行它吗？”
* Copilot将分析您的项目，然后启动服务器。
* 我们可以审查我们的项目，决定我们想要哪些更改，并继续使用 Copilot，所有这些都在同一个会话中进行。

## 什么是GitHub CopilotCLI非交互模式？

另一方面，非交互模式是为了速度和简单性而设计的。您无需进入完整的会话，只需在命令行中传递一个提示即可几乎立即获得响应，而无需跟进 Copilot。

这种模式被设计为一种在线体验，非常适合快速、一次性提示，例如总结存储库、生成代码片段或将 Copilot 插入自动化工作流程，而无需离开 shell 上下文。一旦得到答案，您就会回到终端流程。

进入非交互模式的方法如下：

* 从常规命令行开始（如果您位于 Copilot，则需要退出）。
* 输入 `copilot -p` 并提示 agent 之类的内容，例如“快速总结此存储库的功能和关键文件夹”。
* Copilot 将筛选您的项目文件以提供答案。哒哒！ ✨

这两种模式共同帮助您有效地处理各种类型的项目：交互式用于探索性、更深入的工作，非交互式用于在您已经确切知道自己需要什么时快速、集中的结果。

## 如何恢复之前的 Copilot 会话

有时，您可能想要从上一个 Copilot 会话中中断的地方继续，同时保留该对话中的所有上下文。

如果您处于交互模式，您可以在命令行中输入 `/resume`，Copilot 会让您从列表中选择上一个会话。如果您想从非交互模式直接启动到上一个会话选择器，请使用 `copilot --resume`。

只需一个命令即可使用 Copilot 进行备份，如果您已经知道要在哪个会话中工作，这将非常有用。

随身携带 GitHub Copilot CLI 交互式和非交互式模式是直接从终端提示 Copilot 的最快方法。可以选择在来回编码和快速提示之间进行选择，这意味着您可以按照您想要的方式使用 Copilot。

请密切关注 GitHub Copilot CLI 初学者系列中的更多视频，我们将在其中探索：

* Copilot CLI 斜线命令
* 将 MCP 服务器与 Copilot CLI 一起使用
* 还有更多！

快乐编码！

**想尝试 GitHub Copilot CLI？** [立即阅读文档](<https://docs.github.com/copilot/concepts/agents/about-copilot-cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) 和 [开始使用](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>)。

### 更多资源可供探索：

* [GitHub Copilot CLI 初学者视频系列](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>)
* [GitHub Copilot CLI 初学者：开始使用 GitHub Copilot CLI](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/>)
* [GitHub Copilot CLI 101：如何从命令行使用GitHub Copilot](<https://github.blog/ai-and-ml/github-copilot-cli-101-how-to-use-github-copilot-from-the-command-line/?utm_source=blog-announcement-cli-tutorial&utm_medium=blog&utm_campaign=universe25post>)
* [GitHub Copilot CLI](<https://docs.github.com/copilot/how-tos/copilot-cli/cli-best-practices?utm_campaign=copilot-brand&utm_medium=sem&utm_source=google&ocid=AIDcmmh2h80ugd_SEM__k_CjwKCAjw-dfOBhAjEiwAq0RwI0TIeyL9bjDmXlY26JKPbDvHGzBcaZUa4LR8u8SJuGbIke6e7U2YXRoCzGQQAvD_BwE_k_cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) 的最佳实践

---

## 标签：

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

# GitHub Copilot CLI for Beginners: Interactive v. non-interactive mode

Welcome to GitHub Copilot CLI for Beginners! In this series (available in [video](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>) and [blog](<https://github.blog/tag/github-copilot-cli-for-beginners/>) format), we’ll give you everything you need to get started using [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>), from your first prompt to tips for navigating the command line like a pro!

In this blog, we’ll cover the two main modes of the CLI: interactive and non-interactive. You’ll learn the differences between the two modes, how to enter them, and what they’re most useful for.

Let’s dive in!

## What is GitHub Copilot CLI interactive mode?

Interactive mode is a back-and-forth, chat-like experience. When you launch Copilot CLI with Copilot, you’re already in interactive mode—that’s the default. Non-interactive mode is a separate option for when you want a quick, one-off answer without entering a session. (More on non-interactive mode later!)

In interactive mode, you can ask GitHub Copilot a question, review its response, and then either follow up with questions or another prompt—all within the same session. This is the mode for those who want to work hands-on with Copilot and iterate as you go.

Here’s how to enter interactive mode:

* From the command line, type `copilot` and hit `Enter`.
* Copilot may ask you to trust this folder, because it needs permission to read and modify files.
* Ask Copilot a question, like “How do I run this project locally?”
* Copilot will give you instructions, which you can do on your own. But if you want to work collaboratively, you can ask Copilot: “Can you run it for me?”
* Copilot will analyze your project and then start the server.
* We can review our project, decide what changes we want, and continue working with Copilot, all in the same session.

## What is GitHub Copilot CLI non-interactive mode?

On the other hand, non-interactive mode is designed for speed and simplicity. Instead of having to enter a full session, you pass a single prompt right in the command line and get a response almost immediately, without needing to follow up with Copilot.

Designed as an in-line experience, this mode is perfect for quick, one-shot prompts like summarizing a repository, generating code snippets, or plugging Copilot into automated workflows, without leaving your shell context. Once you get an answer, you’re right back in your terminal flow.

Here’s how to enter non-interactive mode:

* Start at the regular command line (if you’re in Copilot, you’ll need to exit).
* Type `copilot -p` and prompt the agent with something like “Quickly summarize what this repository does and the key folders.”
* Copilot will sift through your project files to provide an answer. Ta-da! ✨

Together, these two modes help you tackle all kinds of projects efficiently: interactive for explorative, deeper work, and non-interactive for fast, focused results when you already know exactly what you need.

## How to resume a previous Copilot session

Sometimes, you may want to pick up right where you left off in a previous Copilot session, while retaining all the context from that conversation.

If you’re in interactive mode, you can type `/resume` into the command line and Copilot will let you choose a previous session from a list. If you want to launch directly into the previous session picker from non-interactive mode, use `copilot --resume`.

It only takes one command to pick back up with Copilot, which is super useful if you already know what session you want to work in.

Take this with you GitHub Copilot CLI interactive and non-interactive modes are the fastest ways to prompt Copilot directly from your terminal. Having the option to pick between back-and-forth coding and quick prompting means you can work with Copilot, the way you want.

Keep an eye out for more videos in the GitHub Copilot CLI for Beginners series, where we’ll explore:

* Copilot CLI slash commands
* Using MCP servers with Copilot CLI
* And more!

Happy coding!

**Looking to try GitHub Copilot CLI?** [Read the Docs](<https://docs.github.com/copilot/concepts/agents/about-copilot-cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) and [get started](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) today.

### More resources to explore:

* [GitHub Copilot CLI for Beginners video series](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>)
* [GitHub Copilot CLI for Beginners: Getting started with GitHub Copilot CLI](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/>)
* [GitHub Copilot CLI 101: how to use GitHub Copilot from the command line](<https://github.blog/ai-and-ml/github-copilot-cli-101-how-to-use-github-copilot-from-the-command-line/?utm_source=blog-announcement-cli-tutorial&utm_medium=blog&utm_campaign=universe25post>)
* [Best practices for GitHub Copilot CLI](<https://docs.github.com/copilot/how-tos/copilot-cli/cli-best-practices?utm_campaign=copilot-brand&utm_medium=sem&utm_source=google&ocid=AIDcmmh2h80ugd_SEM__k_CjwKCAjw-dfOBhAjEiwAq0RwI0TIeyL9bjDmXlY26JKPbDvHGzBcaZUa4LR8u8SJuGbIke6e7U2YXRoCzGQQAvD_BwE_k_cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>)

---

## Tags:

* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)
* [GitHub Copilot CLI for Beginners](<https://github.blog/tag/github-copilot-cli-for-beginners/>)

## Written by

![Kayla Cinnamon](<https://avatars.githubusercontent.com/u/48369326?v=4&s=200>)

### [Kayla Cinnamon](<https://github.blog/author/cinnamonmsft/>)

[@cinnamon-msft](<https://github.com/cinnamon-msft>)

Senior AI Developer Tools Advocate


</details>
