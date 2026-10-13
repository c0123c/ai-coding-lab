# Copilot CLI 安装与入门

介绍安装、认证、启动和基本任务操作，展示终端 AI Agent 可以帮助执行的开发工作。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-04-10](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/>)；更新：[2026-04-10](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/Copilot CLI 安装与入门.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub Copilot CLI 初学者：开始使用 GitHub Copilot CLI

欢迎来到GitHub CopilotCLI初学者！在本系列中（[以视频格式](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>)和GitHub博客上提供），我们将探索如何直接在终端中使用AI编码助手，以及如何像专业人士一样导航命令行的提示和技巧！

在本博客中，我们将介绍 [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep1-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) 是什么、它是如何工作的以及如何开始。您将学习如何使用 npm 安装它，使用您的 GitHub 帐户进行身份验证，授予文件夹权限，以及运行第一个提示以直接从终端生成代码。

让我们开始吧！

## 什么是GitHub CopilotCLI？

GitHub Copilot CLI 将 Copilot 的代理 AI 功能直接引入命令行界面 (CLI)，变得像您使用的任何基于终端或控制台的工具一样（具有存储库的完整上下文）！

agents 如此特别的原因在于它们能够执行自主构建代码和运行测试等任务，因此您可以迭代构建。他们甚至可以自我纠正和修复错误，而无需人工提示。

这意味着您可以将任务分配给 Copilot，专注于其他待办事项列表项，然后查看结果并从终端请求其他更改 - 所有这些都无需中断您的工作流程或切换工具。 （您甚至可以从 CLI 内部将任务委托给 Copilot 云 agent，稍后会详细介绍。）

## 安装CopilotCLI

毫不奇怪，使用CopilotCLI的第一步是安装。核心跨平台方法（如果您已经有节点）是通过 npm 执行此操作，使用：

```
npm install -g @github/copilot
```

如果您使用 WinGet 或 Homebrew 等包管理器，您也可以通过这些工具安装 Copilot CLI。 （您需要查阅这些工具的文档，了解如何执行此操作的具体细节。）

## 开始使用 GitHub Copilot CLI

安装产品后，您可以通过在命令行中键入“Copilot”来启动它。您可以使用许多开关，我们将在本系列后面介绍。

如果这是您第一次进入终端，您需要使用 GitHub 凭据登录。

```
/login
```

这将：

* 将客户绑定到您的 Copilot 帐户。
* 连接只读 GitHub MCP 服务器，该服务器授予对 GitHub 上资源的访问权限。 （别担心，我们也会在本系列后面介绍 MCP！）

使用 Copilot 时，您需要授予 Copilot 文件夹访问权限，以便能够浏览并可能修改文件。您可以仅针对此会话执行此操作，也可以保存此设置以应用于以后的会话。这将允许您将来再次启动 Copilot，而不必为同一项目不断批准它。

完成此操作后，您可以开始与 Copilot 交谈，向其提问，并请求代码或其他任务。

## 用例：您可以使用 Copilot CLI 做什么

这里只是一些 GitHub Copilot CLI 用例。 （[GitHub Copilot CLI 101 博客](<https://github.blog/ai-and-ml/github-copilot-cli-101-how-to-use-github-copilot-from-the-command-line/>) 中还介绍了更多内容！）

**询问项目概述**：Copilot将探索、打开重要文件，并报告其发现。

```
Give me an overview of this project
```

**请求代码，例如新端点**：Copilot将查看该项目，查找现有文档和示例，并尝试遵循它所看到的实践。再次，它会请求创建文件的权限。

```
Let’s add a new endpoint to return all categories
```

**将任务委托给Copilot云agent**：对于定义明确的任务，您可以直接从CLI委托给Copilot云agent。 Copilot 将保留当前会话的上下文、创建新分支、打开草稿拉取请求，并在请求您进行审核之前在后台进行所需的更改。

```
/delegate Let’s deal with issue #14 to add the rest of the CRUD endpoints to games
```

当然，探索 Copilot CLI 的功能的最佳方法之一就是直接询问 Copilot。它可以查看自己的文档并提供有关与其交互和探索的最佳方式的指导。

请收听本系列的下一课，其中涵盖使用两种不同的模式：交互模式让 GitHub Copilot 在本地运行项目，或非交互模式，使用 `-p` 标志进行快速摘要，而无需离开 shell 上下文。

## 随身携带这个

将代理 AI 直接带到您的终端，开启了一种全新的学习、实验和完成工作的方式，而不会中断您的流程。请密切关注 GitHub Copilot CLI 初学者系列中的更多 [ 视频 ](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>)，我们将在其中了解：

* 交互模式与非交互模式
* Copilot CLI 斜线命令
* 将 MCP 服务器与 Copilot CLI 一起使用
* 还有更多！

快乐编码！

**想要尝试GitHub Copilot CLI？** [阅读文档](<https://docs.github.com/copilot/concepts/agents/about-copilot-cli>)和[立即开始](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep1-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>)。

**更多资源可供探索：**

* [GitHub Copilot CLI 初学者视频系列](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>)
* [GitHub Copilot CLI 101：如何从命令行使用GitHub Copilot](<https://github.blog/ai-and-ml/github-copilot-cli-101-how-to-use-github-copilot-from-the-command-line/?utm_source=blog-announcement-cli-tutorial&utm_medium=blog&utm_campaign=universe25post>)
* [GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/cli-best-practices?utm_campaign=copilot-brand&utm_medium=sem&utm_source=google&ocid=AIDcmmh2h80ugd_SEM__k_CjwKCAjw-dfOBhAjEiwAq0RwI0TIeyL9bjDmXlY26JKPbDvHGzBcaZUa4LR8u8SJuGbIke6e7U2YXRoCzGQQAvD_BwE_k_>) 的最佳实践

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)
* [GitHub Copilot CLI 初学者用](<https://github.blog/tag/github-copilot-cli-for-beginners/>)

## 撰写者

![克里斯托弗·哈里森](<https://avatars.githubusercontent.com/u/6109729?v=4&s=200>)

### [克里斯托弗·哈里森](<https://github.blog/author/geektrainer/>)

[@geektrainer](<https://github.com/geektrainer>)

高级开发者倡导者，GitHub


## 原文对照

<details>
<summary>展开完整原文</summary>

# GitHub Copilot CLI for Beginners: Getting started with GitHub Copilot CLI

Welcome to GitHub Copilot CLI for Beginners! In this series ([available in video format](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>) and on the GitHub Blog), we’ll explore how to use your AI coding assistant directly in your terminal, along with tips and tricks on how to navigate the command line like a pro!

In this blog, we’ll walk through what [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep1-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) is, how it works, and how to get started. You’ll learn how to install it using npm, authenticate with your GitHub account, grant folder permissions, and run your first prompts to generate code directly from the terminal.

Let’s get started!

## What is GitHub Copilot CLI?

The GitHub Copilot CLI brings Copilot’s agentic AI capabilities right into the command-line interface (CLI), becoming like any terminal or console based tool you use (with the full context of your repos)!

What makes agents so special is their ability to perform tasks like building code and running tests autonomously, so you can build iteratively. They can even self-correct and fix errors without needing a human to prompt them.

This means you can assign tasks to Copilot, focus on other to-do list items, and then review the results and request additional changes from the terminal—all without having to interrupt your workflow or switch tools. (You can even delegate tasks to Copilot Cloud agent from inside the CLI, more on this later.)

## Installing Copilot CLI

Not surprisingly, the first step to using Copilot CLI is installation. The core cross-platform way—if you already have node—to do this is via npm, using:

```
npm install -g @github/copilot
```

If you’re using a package manager like WinGet or Homebrew, you can install Copilot CLI through those tools as well. (You’ll want to consult those tools’ documentation for the exact specifics on how to do this.)

## Getting started with GitHub Copilot CLI

Once you install the product, you can launch it by typing “Copilot” in your command line. There are numerous switches you can use, which we’ll cover later in the series.

If it’s your first time in the terminal, you’ll need to log in with your GitHub credentials.

```
/login
```

This will:

* Tie the client to your Copilot account.
* Connect the readonly GitHub MCP server, which grants access to resources on GitHub. (Don’t worry, we’ll cover MCP later in the series as well!)

When using Copilot, you need to grant access to the folder for Copilot to be able to explore and potentially modify files. You can do this for only this session or save this setting to apply to later sessions, too. This will allow you to launch Copilot again in the future, without having to keep approving it for the same project.

Once you’ve done that, you can start talking to Copilot, asking it questions, and request code or other tasks.

## Use cases: What you can do with Copilot CLI

Here are just a few GitHub Copilot CLI use cases. (There are many more covered in the [GitHub Copilot CLI 101 blog](<https://github.blog/ai-and-ml/github-copilot-cli-101-how-to-use-github-copilot-from-the-command-line/>)!)

**Ask for an overview of the project**: Copilot will explore, open important files, and report back with its findings.

```
Give me an overview of this project
```

**Ask for code, such as a new endpoint**: Copilot will look at the project, find existing documentation and examples, and try to follow the practices it sees. Again, it will ask for permission to create the file.

```
Let’s add a new endpoint to return all categories
```

**Delegate tasks to Copilot cloud agent**: For well-defined tasks, you can delegate to Copilot cloud agent right from the CLI. Copilot will preserve the context from your current session, create a new branch, open a draft pull request, and make the requested changes in the background before requesting your review.

```
/delegate Let’s deal with issue #14 to add the rest of the CRUD endpoints to games
```

Of course, one of the best ways to explore what you can do with Copilot CLI is to simply ask Copilot. It can look through its own documentation and provide guidance on the best ways to interact with it and explore.

Tune in for the next lesson in the series which covers using two different modes: interactive mode to have GitHub Copilot run your project locally or non-interactive mode with the `-p` flag for quick summaries without leaving your shell context.

## Take this with you

Bringing agentic AI right to your terminal opens a whole new way to learn, experiment, and get things done without ever breaking your flow. Keep an eye out for more [videos](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>) in the GitHub Copilot CLI for Beginners series, where we’ll learn about:

* Interactive vs non-interactive modes
* Copilot CLI slash commands
* Using MCP servers with Copilot CLI
* And more!

Happy coding!

**Looking to try GitHub Copilot CLI?** [Read the Docs](<https://docs.github.com/copilot/concepts/agents/about-copilot-cli>) and [get started today](<https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep1-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>).

**More resources to explore:**

* [GitHub Copilot CLI for Beginners video series](<https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl>)
* [GitHub Copilot CLI 101: How to use GitHub Copilot from the command line](<https://github.blog/ai-and-ml/github-copilot-cli-101-how-to-use-github-copilot-from-the-command-line/?utm_source=blog-announcement-cli-tutorial&utm_medium=blog&utm_campaign=universe25post>)
* [Best practices for GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/cli-best-practices?utm_campaign=copilot-brand&utm_medium=sem&utm_source=google&ocid=AIDcmmh2h80ugd_SEM__k_CjwKCAjw-dfOBhAjEiwAq0RwI0TIeyL9bjDmXlY26JKPbDvHGzBcaZUa4LR8u8SJuGbIke6e7U2YXRoCzGQQAvD_BwE_k_>)

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)
* [GitHub Copilot CLI for Beginners](<https://github.blog/tag/github-copilot-cli-for-beginners/>)

## Written by

![Christopher Harrison](<https://avatars.githubusercontent.com/u/6109729?v=4&s=200>)

### [Christopher Harrison](<https://github.blog/author/geektrainer/>)

[@geektrainer](<https://github.com/geektrainer>)

Senior Developer Advocate, GitHub


</details>
