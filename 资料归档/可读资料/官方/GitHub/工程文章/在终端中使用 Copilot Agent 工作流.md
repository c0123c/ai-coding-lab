# 在终端中使用 Copilot Agent 工作流

介绍 CLI 的交互与非交互能力以及脚本自动化，通过操作展示如何把 Agent 接入终端中的开发流程。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-01-26](<https://github.blog/ai-and-ml/github-copilot/power-agentic-workflows-in-your-terminal-with-github-copilot-cli/>)；更新：[2026-01-29](<https://github.blog/ai-and-ml/github-copilot/power-agentic-workflows-in-your-terminal-with-github-copilot-cli/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/power-agentic-workflows-in-your-terminal-with-github-copilot-cli/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/在终端中使用 Copilot Agent 工作流.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 GitHub Copilot CLI 在终端中支持代理工作流程

自从 [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-agentic-wflows-cli-features-cta&utm_medium=blog&utm_campaign=jan26postuniverse>) 于 9 月份公开预览版 2025 推出以来，我们一直在定期发布频繁的更新和改进。下面，我们将向您展示是什么让 Copilot CLI 如此特别，为什么在您的终端中拥有一个代理 AI 助手真是太棒了，以及我们如何构建 Copilot CLI 以更广泛地连接到 GitHub Copilot 的其余部分生态系统。

***注意：*** *此博客基于 GitHub Universe 2025 演示。请观看下面的内容以了解正在运行的功能。 👇*

## 将 CLI 带到您的工作地点

如果您在 VS Code 或类似的 IDE 中使用 GitHub Copilot，请考虑您整个工作日在 IDE 中度过的频率，尽量避免在任何其他工作环境中执行任何操作。当我们概念化 GitHub Copilot CLI 时，我们将这一想法放在首位。

开发人员花时间使用 `ssh` 连接到服务器、调试容器中的内容、在 [github.com](<http://github.com>) 上分类问题、管理 CI/CD 管道以及编写部署脚本。有很多工作并没有完全映射到单独的 IDE 甚至是像 VS Code 这样的多用途代码编辑器中。

为了确保我们将 GitHub CLI 带给开发人员，通过终端是有意义的。毕竟，终端超越了计算机上的所有不同应用程序，并且在正确的手中，您可以通过细粒度控制完成任何任务。将 GitHub Copilot 引入 CLI 并赋予其访问更广泛的 GitHub 生态系统的权限，让您可以花更多的时间来完成工作，而不是花更少的时间寻找手册页和浏览文档来了解如何做某事。

## 展示 GitHub CLI 功能

通常，项目的第一步是加快进度。让我们考虑一个例子，你正在代替朋友参与一个项目，但你对此一无所知——你不知道代码库、语言，甚至框架。

您收到了更新反馈表单的请求，因为 UI 元素布局不正确。具体来说，**提交反馈**按钮与表单本身重叠，遮盖了一些字段。提交错误的人包含了显示 UI 错误的屏幕截图。

首先，您可以启动 GitHub CLI 并要求它克隆存储库。

```
Clone the feedback repo and set us up to run it
```

发送此提示后，Copilot 将为您提供所需的一切：它将引用与存储库关联的文档，并找出成功运行它所需的任何依赖项。即使您不熟悉所需的依赖项，这也是一种快速入门方法。

Copilot 会在运行任何命令之前提示您，以确保它具有执行此操作的权限。它会告诉您它正在做什么，并确保您在运行任何命令之前对其进行授权。

现在假设您的存储库已设置并且您要运行服务器，但您收到一条错误消息，表明该端口已在使用中。这可能是工作流程杀手。您知道可以在终端中运行一些命令来识别使用该端口的进程并安全地将其关闭，但您可能不记得执行此操作的确切语法。为了使这更容易，您可以将任务交给Copilot。

```
What is using port 3000?
```

无需查找命令，Copilot就可以使用端口确定PID。然后，您可以自己终止该进程或将该任务交给 Copilot，以便您可以专注于其他任务。

```
Find and kill the process on port 3000
```

继续我们的示例，您现在已启动并运行存储库，并且可以使用 **提交反馈** 按钮验证错误。但是，您不想查看所有代码文件来尝试查找可能存在的错误。为什么不让 Copilot 先看一下，看看它是否可以识别任何明显的问题？

Copilot 可以分析图像，因此您可以使用错误报告中提供的图像。将显示错误的屏幕截图上传到存储库，并询问 Copilot 是否有任何关于如何修复该错误的想法。

```
Fix the big shown in @FIX-THIS.PNG
```

Copilot 将尝试查找并修复问题，并提供建议更改的列表。然后，您可以查看更改并决定是否让 Copilot 自动应用修复。借助 GitHub CLI，我们能够在终端中完成所有这些操作。

然而，在将这些更改上传到存储库之前，团队有非常严格的可访问性要求。您可能不熟悉这些是什么，但在本示例中，团队有一个定义它们的自定义 agent。它拥有所有正确的 MCP 工具来检查护栏，因此您可以利用 agent 对任何建议的更改进行可访问性审查。

```
/agent
```

此命令提供了可用自定义 agents 的列表，因此您可以选择要使用的合适的。选择适当的 agent 后，只需要求它查看建议的更改即可。

```
Review our changes
```

此提示将编码 agent 设置为工作，并查看您的更改。如果发现任何问题，它会通知您并建议更新，以确保您的更改符合其说明。通过适当的 agents 可以非常强大地利用它来对您的代码进行检查。

最后，假设您想知道是否有任何未解决的问题与您已完成的工作相对应，但您不想手动搜索所有未解决的问题。幸运的是，Copilot CLI 附带 GitHub MCP 服务器，因此您可以在 GitHub 存储库上查找任何内容，而无需手动转到 [github.com](<http://github.com>)。

```
Are there any open issues that map to the work we're doing?
```

然后，GitHub MCP 服务器将遍历并搜索所有问题，并识别任何可能与您已解决的工作相匹配的问题。如果它提出的问题尚未通过您所做的工作完全解决，您仍然可以将这项工作委托给编码 agent，以便您可以继续从事您关注的任何事情。

```
/delegate Finish fixing the issue outlined in #1 and use the playright MCP server to ensure that it's fixed
```

`/delegate` 命令会调度一个编码 agent 在后台为您处理任务，同时您将注意力转移到其他区域。它将针对编码 agent 执行的未来工作打开拉取请求。这与 [ 标准 Copilot 编码 agent 工作流程 ](<https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent>) 相同 - 刚刚通过 GitHub Copilot CLI 开始。

## 用于脚本编写和自动化的无头操作

GitHub Copilot CLI 的功能比我们之前展示的还要多。您现在可以在 Copilot CLI 中无头执行任务。还记得我们讨论的识别和终止端口 3000 上运行的进程的示例吗？您可以通过 CLI 使用以下命令来执行此操作。

```
copilot --allow-all-tools -p "Kill the process using port 3000"
```

Copilot 然后将使用适当的命令来识别并终止该进程。虽然这是一个简单的示例，但您可以考虑更复杂的场景，您可以将其连接到脚本或操作工作流程中并一遍又一遍地重用它。

请注意，这包括标志 –allow-all-tools，除非您在容器中运行，否则您可能不希望在实际环境中包含该标志。幸运的是，我们提供了几个标志，您可以传递这些标志以仅允许访问某些目录和工具。您甚至可以限制 Copilot 使用特定命令，这样您就可以保证始终有人参与，例如推送到存储库。

要查看可能的标志列表，请运行以下命令。

```
copilot --help
```

您可以使用登录命令或使用个人访问令牌以交互方式进行身份验证。这样，您就可以将其与自动化一起使用。我们还积极研究更多对企业更友好的身份验证方法。

## 亲自尝试 Copilot CLI

我们不断发布更新，并始终寻求用户的反馈。我们有几个未解决的问题，正在跟踪客户想要查看的项目。如果您想了解我们正在做什么并提供反馈，请查看 [ 我们的公共 GitHub Copilot CLI 存储库 ](<https://github.com/github/copilot-cli>)。

如果您想开始，那非常简单。它适用于 Windows（WSL 和本机 PowerShell）、Mac OS 和 Linux。我们提供了几种特定于平台的方法来在 [ Copilot CLI README](<https://github.com/github/copilot-cli?tab=readme-ov-file#installation>) 中安装 CLI。

尝试一下，加入我们公共存储库上的对话，以帮助我们尽可能构建最好的基于终端的 AI 系统。我们期待听到您的反馈！

开始使用 [GitHub Copilot CLI >](<https://github.com/features/copilot/cli>)

---

## 标签：

* [GitHub CLI](<https://github.blog/tag/github-cli/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)

## 撰写者

![迪伦·贝尔托洛](<https://avatars.githubusercontent.com/u/39363041?v=4&s=200>)

### [迪伦·贝尔托洛](<https://github.blog/author/dbirtolo/>)

[@dbirtolo](<https://github.com/dbirtolo>)

Dylan Birtolo 是 GitHub 的高级内容作家，他致力于分享 GitHub 提供的所有好东西。他担任技术作家已近 20 年，其中很大一部分时间是在 Microsoft 的各个团队工作。在业余时间，他与​​动物一起工作，玩很多游戏，还参加职业比武。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Power agentic workflows in your terminal with GitHub Copilot CLI

Since [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-agentic-wflows-cli-features-cta&utm_medium=blog&utm_campaign=jan26postuniverse>) launched in public preview in September 2025, we’ve been shipping frequent regular updates and advancements. Below, we’ll show you what makes Copilot CLI so special, why it’s great to have an agentic AI assistant right in your terminal, and how we’re building the Copilot CLI to connect more broadly to the rest of the GitHub Copilot ecosystem.

***Note:*** *This blog is based on a GitHub Universe 2025 presentation. Watch below to see the functionality in action. 👇*

## Bringing the CLI to where you work

If you use GitHub Copilot in VS Code or in a similar IDE, consider how often you spend your entire working day in the IDE, trying to avoid doing anything in any other working environment. We kept this thought top of mind when we conceptualized the GitHub Copilot CLI.

Developers spend time using `ssh` to connect to servers, debug things in containers, triage issues on [github.com](<http://github.com>), manage CI/CD pipelines, and write deployment scripts. There’s a lot of work that doesn’t neatly map into an individual IDE or even a multipurpose code editor like VS Code.

To make sure that we brought the GitHub CLI to developers where they already are, it made sense to go through the terminal. After all, the terminal transcends all the different applications on your computer and, in the right hands, is where you can accomplish any task with fine-grained control. Bringing GitHub Copilot into the CLI and giving it access to the broader GitHub ecosystem lets you spend more time getting your work done, and less time hunting down man pages and scouring through documentation to learn how to do something.

## Showcasing the GitHub CLI functionality

Often, the first step with a project is getting up to speed on it. Let’s consider an example where you’re filling in for a friend on a project, but you don’t know anything about it—you don’t know the codebase, the language, or even the framework.

You’ve received a request to update a feedback form because the UI elements are not laid out correctly. Specifically, the **Submit Feedback** button overlaps the form itself, obscuring some fields. Whoever submitted the bug included a screenshot showing the UI error.

To get started, you can launch the GitHub CLI and ask it to clone the repository.

```
Clone the feedback repo and set us up to run it
```

After sending this prompt, Copilot will get you everything you need: It will reference the documentation associated with the repository and figure out any dependencies you need in order to successfully run it. It’s a fast way to get started, even if you’re not familiar with the dependencies required.

Copilot will prompt you before running any commands to make sure that it has permission to do so. It will tell you what it’s doing and make sure that you authorize any commands before it runs them.

Now let’s say that your repository is set up and you go to run the server, but you receive an error that the port is already in use. This can be a workflow killer. You know that there are commands you can run in the terminal to identify the process using the port and safely shut it down, but you might not remember the exact syntax to do so. To make this much easier, you can just hand the task over to Copilot.

```
What is using port 3000?
```

Without you needing to look up the commands, Copilot can determine the PID using the port. You can then either kill the process yourself or hand that task over to Copilot so you can focus on other tasks.

```
Find and kill the process on port 3000
```

Continuing with our example, you now have the repository up and running and can verify the error with the **Submit Feedback** button. However, you don’t want to look through all of the code files to try and find what the bug might be. Why not have Copilot take a look first and see if it can identify any obvious issues?

Copilot can analyze images, so you can use the image supplied in the bug report. Upload the screenshot showing the error to the repository, and ask Copilot if it has any ideas on how to fix the bug.

```
Fix the big shown in @FIX-THIS.PNG
```

Copilot will attempt to find and fix the issue, supplying a list of suggested changes. You can then review the changes and decide whether or not to have Copilot automatically apply the fixes. And we’re able to do all of this in the terminal thanks to the GitHub CLI.

However, before uploading these changes to the repository, the team has very strict accessibility requirements. You might not be familiar with what these are, but in this example, the team has a custom agent that defines them. It has all the right MCP tools to check on the guardrails, so you can leverage the agent to do an accessibility review of any proposed changes.

```
/agent
```

This command provides a list of available custom agents, so you can select the appropriate one you want to use. Once you select the appropriate agent, simply ask it to look over the proposed changes.

```
Review our changes
```

This prompt sets the coding agent to work, looking at your changes. If it finds any issues, it will let you know and suggest updates to make sure your changes are aligned with its instructions. This can be immensely powerful with the appropriate agents to leverage to provide checks on your code.

Finally, let’s say you want to know if there are any open issues that map to the work that you’ve done, but you don’t want to manually search through all of the open issues. Luckily, Copilot CLI ships with the GitHub MCP server, so you can look up anything on the GitHub repository without needing to manually go to [github.com](<http://github.com>).

```
Are there any open issues that map to the work we're doing?
```

The GitHub MCP server will then go through and search through all of the issues and identify any that might match the work that you’ve addressed. If it pulls up issues that aren’t completely resolved by the work that you’ve done, you can still delegate this work to a coding agent so that you can continue working on whatever you’re focused on.

```
/delegate Finish fixing the issue outlined in #1 and use the playright MCP server to ensure that it's fixed
```

The `/delegate` command dispatches a coding agent to work on the task for you in the background while you turn your attention to other areas. It will open a pull request for future work that the coding agent performs. This is identical to [the standard Copilot coding agent workflow](<https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent>)—just started through GitHub Copilot CLI.

## Headless operation for scripting and automation

GitHub Copilot CLI has even more functionality than what we’ve previously showcased. You can now perform tasks headlessly in the Copilot CLI. Remember the example where we talked about identifying and killing the process running on port 3000? You could do this through the CLI with the following command.

```
copilot --allow-all-tools -p "Kill the process using port 3000"
```

Copilot will then use the appropriate commands to identify and kill that process. While this is a simple example, you can think of more complex scenarios where you could hook this up into a script or actions workflow and reuse it over and over again.

Note that this included the flag –allow-all-tools, which is probably not something you want to include in an actual environment unless you’re running in a container. Luckily, we provide several flags that you can pass to only allow access to certain directories and tools. You can even restrict Copilot from using specific commands, so you can guarantee that a human is always involved, such as with pushing up to a repository.

To see a list of possible flags, run the following command.

```
copilot --help
```

You can authenticate interactively with a login command or by using a personal access token. This way, you can use this with automations. We’re also actively working on more authentication methods that are more enterprise friendly.

## Trying the Copilot CLI yourself

We’re constantly shipping updates and are always looking for feedback from our users. We have several open issues and are tracking the items that customers want to see. If you want to see what we’re working on and provide feedback, check out [our public GitHub Copilot CLI repository](<https://github.com/github/copilot-cli>).

And if you want to get started, it’s incredibly easy. It’s available for Windows (both WSL and natively in PowerShell), Mac OS, and Linux. We provide several platform-specific ways to install the CLI in [the Copilot CLI README](<https://github.com/github/copilot-cli?tab=readme-ov-file#installation>).

Give it a try and come join the conversation on our public repository to help us build the best terminal-based AI system we possibly can. We look forward to hearing your feedback!

Get started with [GitHub Copilot CLI >](<https://github.com/features/copilot/cli>)

---

## Tags:

* [GitHub CLI](<https://github.blog/tag/github-cli/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)

## Written by

![Dylan Birtolo](<https://avatars.githubusercontent.com/u/39363041?v=4&s=200>)

### [Dylan Birtolo](<https://github.blog/author/dbirtolo/>)

[@dbirtolo](<https://github.com/dbirtolo>)

Dylan Birtolo is a senior content writer at GitHub, where he works on sharing all the good things that GitHub has to offer. He's been a technical writer for almost 20 years, a large portion of which was working on various teams across Microsoft. In his off time, he works with animals, plays a lot of games, and professionally jousts.


</details>
