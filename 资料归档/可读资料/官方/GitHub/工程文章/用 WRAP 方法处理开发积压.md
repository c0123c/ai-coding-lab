# 用 WRAP 方法处理开发积压

围绕清晰 Issue、完善指令、原子任务和与 Agent 协作给出实践建议，帮助把积压事项整理成可执行任务。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-12-26](<https://github.blog/ai-and-ml/github-copilot/wrap-up-your-backlog-with-github-copilot-coding-agent/>)；更新：[2025-12-26](<https://github.blog/ai-and-ml/github-copilot/wrap-up-your-backlog-with-github-copilot-coding-agent/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/wrap-up-your-backlog-with-github-copilot-coding-agent/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/用 WRAP 方法处理开发积压.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# WRAP 使用 GitHub Copilot 编码 agent 增加您的积压工作

GitHub 的工程师已经使用 GitHub Copilot 编码 agent 一年多了，通过经验研究它可以帮助开发人员节省实时时间和精力。通过我们的工作，我们开发了一个方便的缩写词：WRAP，它代表：

* W – 写出有效的问题
* R——完善你的指令
* A – 原子任务
* P – 与编码 agent 配对

WRAP 将帮助您充分利用编码 agent。例如，您可能积压了大量难以确定优先级的问题。也许您不得不推迟一些技术债务改进以支持发布新功能。或者，您的开发时间可能被分配在修复客户错误和更大的热情项目之间。 WRAP 可以帮助您快速上手，使用编码 agent 来解决您过去可能没有时间完成的任务。

## 写出有效的问题

WRAP 的第一步是确保您正在编写有效的问题以分配给 GitHub Copilot 编码 agent。本质上，您试图通过为 agent 添加上下文来设置编码 agent 以获得成功，就像您为新团队成员所做的那样。以下是您应该考虑的一组准则：

* **写一个问题，就好像它是为代码库的新手写的一样。**如果您考虑新手可能需要什么上下文，您可能会为编码agent提供足够的上下文，以有效地完成任务。
* **制作一个描述性标题，解释工作在哪里完成。**编码agent最好的事情之一是您可以根据需要为其分配尽可能多的任务。但是分配许多任务意味着您可能需要处理大量拉取请求审查！确保您的问题标题清楚地描述了您的工作性质以及工作发生的地点，以便您的 agent 面板易于导航。
* **添加您想要的示例。**如果您已经知道您想要编码agent实现什么，例如特定的错误处理模式，请在问题中包含一个示例！这个额外的上下文将使您更有可能获得您想要的输出。

以下是一些入门问题示例：

而不是：

> 更新整个存储库以使用async/await

做类似的事情：

> 更新身份验证中间件以使用较新的async/await模式，如下例所示。添加单元测试来验证这项工作，确保考虑边缘情况。

```
>

async function exampleFunction() {

  let result = await promise;

  console.log(result); //”done!”

}
```

## 完善您的指示

WRAP 的下一步是确保优化 GitHub Copilot 自定义指令，以改进编码 agent 创建的拉取请求的结果。您可以创建各种不同的自定义指令，以及使用它们的各种不同情况。

* **存储库自定义说明。** 这是存储可能适用于整个存储库的任何信息的好地方。例如，如果您有一个 Go 应用程序并且更喜欢编写 Go 代码的特定方式，那么这应该包含在您的存储库自定义指令中。随着时间的推移添加这些指令将改善与该存储库中的 GitHub Copilot 的任何交互的所有响应。 [以下是有关如何开始添加这些存储库自定义指令的指南](<https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions>)。
  + **注意**：这是GitHub Copilot编码agent的一个很好的第一个用例！尝试要求它为您的存储库生成自定义存储库指令。
* **组织自定义指令。** 就像您为整个存储库创建一组 GitHub Copilot 自定义指令一样，您也可以为整个组织创建一组 GitHub Copilot 自定义指令。这对于适用于组织中所有存储库的任何内容来说都是理想的选择。例如，您可能对应该为所有应用程序创建的测试有要求。 [以下是有关如何开始添加组织自定义指令的指南](<https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-organization-instructions>)。
* **编码 agent 自定义 agents.** 与存储库和组织自定义指令类似，编码 agent 自定义 agents 可以使用自然语言文本文件创建，并且适用于企业、组织或存储库级别。这些对于可能经常发生的任何重复性开发任务来说是理想的选择，但可能不适用于每组更改。例如，您可以创建“集成 Agent”，用于将新产品与特定存储库集成。 [以下是有关如何开始创建自定义 agents](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents>) 的指南。

## 原子任务

编码 agent 非常擅长处理小型、原子和定义明确的任务。然而，它对于解决大问题也很有用！如果您希望它处理更大的问题，诀窍是将大问题分解为多个独立的较小任务。

例如，您不一定要向 GitHub Copilot 分配一个问题，要求它“将 3 百万行代码从 Java 重写到 Golang”。对于特定任务来说，这可能太大了，并且审查这些更改可能会非常痛苦。

相反，您可以将较大的问题分解为较小的问题，供 Copilot 解决：

* 将身份验证模块迁移到 Golang，确保所有现有单元测试通过。
* 将数据验证实用程序包转换为 Golang，同时保持相同的 API 接口。
* 将用户管理控制器重写为 Golang，保留现有的 REST 端点和响应。

通过将大问题分解为更小的原子任务，测试和验证工作的各个部分将变得更加容易，并且审查各个拉取请求也变得更加容易。

## 与编码 agent 配对

在使用编码 agent 时，重要的是要记住它作为编码 agent 的优势以及你作为人类的优势。如果编码 agent 的表现不如您所希望或期望的那样，这将减少后续令人沮丧的体验。

例如，人类非常擅长以下方面：

* **理解“为什么”：**人类很擅长知道为什么创建任务。他们还可以确定对任务的更改是否能够真正解决最初导致问题产生的根本问题。
* **消除歧义：**人类比AI更擅长消除歧义。例如，作为人类，您可能能够省略问题的某些部分，而这些部分是您无法通过 GitHub Copilot 来完成的，例如您希望完成哪些类型的测试和遥测。
* **跨系统思维：**人类更善于思考一个系统中所做的更改对另一个系统的影响。当编码 agent 在一个存储库中完成一项特定任务时，它不太可能知道该任务将如何影响其他存储库。考虑跨系统影响对于人类来说是一项比 GitHub Copilot 更好的任务。

另一方面，编码 agent 非常擅长以下方面：

* **不知疲倦的执行：**您现在可以分配十个不同的任务来编码agent，并期望它能够处理每个任务。
* **重复性任务：**人类在执行重复性任务（例如更新多个文件的命名约定）时经常会感到无聊或错过一些事情。不过，GitHub Copilot 非常适合完成这些事情！
* **探索可能性：**如果您正在考虑用几种不同的方式解决问题，您可以将每种不同的方式分配给编码agent。这可以让您快速了解每种策略的实施方式，而无需使用大量开发资源。ij

## 随身携带这个

当您配备了 GitHub Copilot 和 WRAP 后，挥之不去的积压问题不再有可能。

有需要更新的依赖项吗？您可以在哪里使用更多测试覆盖率？您希望在代码库中采用新的错误处理模式？或者您可能想快速开始添加存储库指令并使用 GitHub Copilot 编码 agent 来执行此操作？

使用WRAP来完成您的积压工作！

开始使用 [GitHub Copilot](<https://github.com/features/copilot>) >

---

## 标签：

* [agent 模式](<https://github.blog/tag/agent-mode/>)
* [代理 AI](<https://github.blog/tag/agentic-ai/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## 撰写者

![布列塔尼·埃利奇](<https://avatars.githubusercontent.com/u/25957322?v=4&s=200>)

### [布列塔尼·埃利奇](<https://github.blog/author/brittanyellich/>)

[@brittanyellich](<https://github.com/brittanyellich>)

Brittany 是 GitHub 的软件工程师，从事平台和企业工作。

![杰森·艾特科维奇](<https://avatars.githubusercontent.com/u/10660468?v=4&s=200>)

### [杰森·埃特科维奇](<https://github.blog/author/jasonetco/>)

[@JasonEtco](<https://github.com/jasonetco>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# WRAP up your backlog with GitHub Copilot coding agent

Engineers at GitHub have been using GitHub Copilot coding agent for over a year now, studying through experience where it can help save developers real time and energy. Through our work, we have developed a handy acronym: WRAP, which stands for:

* W – Write effective issues
* R – Refine your instructions
* A – Atomic tasks
* P – Pair with the coding agent

WRAP will help you get the most out of coding agent. For example, you likely have a backlog full of issues that have been tough to prioritize. Perhaps you’ve had to push off some tech debt improvements in favor of shipping a new feature. Or perhaps your development time has been split between fixing customer bugs and larger passion projects. WRAP can help you quickly get up to speed, using coding agent to tackle tasks that you may not have had time for in the past.

## Write effective issues

The first step of WRAP is to make sure you are writing effective issues to assign to GitHub Copilot coding agent. In essence, you are trying to set coding agent up for success by adding context for the agent, just like you would for a new team member. Here are a set of guidelines you should consider:

* **Write an issue as though it’s for someone brand new to the codebase.** If you consider what context a newcomer might need, you’re likely to include enough context for the coding agent to complete the task effectively.
* **Craft a descriptive title that explains where the work is being done.** One of the best things about the coding agent is that you can assign it as many tasks as you want. But assigning many tasks means you might be juggling a lot of pull request reviews! Make sure your issue title clearly describes the nature of your work and where it occurs so you can keep your agent panel easy to navigate.
* **Add examples of what you want.** If you already know what you’d like the coding agent to implement, such as a specific error-handling pattern, include an example in the issue! This additional context will make it much more likely that you’ll get the output you want.

Here are some example issues to get started:

Instead of:

> Update the entire repository to use async/await

Do something like:

> Update the authentication middleware to use the newer async/await pattern, as shown in the example below. Add unit tests for verification of this work, ensuring edge cases are considered.

```
>

async function exampleFunction() {

  let result = await promise;

  console.log(result); //”done!”

}
```

## Refine your instructions

The next step of WRAP is to make sure that you refine the GitHub Copilot custom instructions to improve the results of your coding agent-created pull requests. There are a variety of different custom instructions that you can create, as well as a variety of different cases where it makes sense to use them.

* **Repository custom instructions.** This is a great place to store any information that might be applicable to your repository as a whole. For example, if you have a Go application and prefer specific ways of writing Go code, that should go in your repository custom instructions. Adding to these instructions over time will improve all the responses from any interactions with GitHub Copilot in that repository. [Here’s a guide on how to get started adding these repository custom instructions](<https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions>).
  + **Note**: This is a great first use case for GitHub Copilot coding agent! Try asking it to generate custom repository instructions for your repository.
* **Organization custom instructions.** Much like how you can create a set of GitHub Copilot custom instructions for an entire repository, you can also create a set of GitHub Copilot custom instructions for your entire organization. This is ideal for anything that applies to all repositories in your organization. For example, you may have requirements around tests that should be created for all applications. [Here’s a guide on how to get started adding organization custom instructions](<https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-organization-instructions>).
* **Coding agent custom agents.** Similar to the repository and organization custom instructions, coding agent custom agents can be created with a natural language text file, and are applicable at either the enterprise, organization, or repository level. These are ideal for any repetitive development task that may occur often, but may not be applicable to every single set of changes. For example, you can create an “Integration Agent” for integrating new products with a specific repository. [Here’s a guide on how to get started creating custom agents](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents>).

## Atomic tasks

Coding agent is very good at handling small, atomic, and well-defined tasks. However, it can also be useful for large problems! If you want it to handle a larger problem, the trick is to break that large problem down into multiple, independent, smaller tasks.

For example, you wouldn’t necessarily want to assign an issue to GitHub Copilot asking it to “Rewrite 3 million lines of code from Java to Golang.” That would probably be too large of a scope for a specific task, and reviewing those changes would likely be pretty painful.

Instead, you could break that larger problem into smaller issues for Copilot to tackle:

* Migrate the authentication module to Golang, ensuring all existing unit tests pass.
* Convert the data validation utilities package to Golang while maintaining the same API interface.
* Rewrite the user management controllers to Golang, preserving existing REST endpoints and responses.

By breaking that large problem into smaller atomic tasks, it will be much easier to test and validate the individual parts of the work and much easier to review the individual pull requests.

## Pair with coding agent

When working with coding agent, it’s important to remember its strengths as a coding agent and your strengths as a human. This will lead to less frustrating experiences down the line if coding agent doesn’t perform like you might want or expect it to.

For example, humans are very good at the following:

* **Understanding the “Why:”** Humans are great at knowing why a task was created. They can also determine whether the changes to a task will actually solve the underlying problem that caused the issue to be created in the first place.
* **Navigating ambiguity:** Humans are much better at navigating ambiguity than AI. For example, as a human, you might be able to omit some parts of an issue that you wouldn’t be able to omit for GitHub Copilot to complete it, such as what sorts of tests and telemetry you expect to be done.
* **Cross-system thinking:** Humans are much better at thinking about the implications of changes made in one system on another system. When coding agent completes a particular task in one repository, it’s unlikely to know about how that task will impact other repositories. Considering cross-system implications is a much better task for humans than for GitHub Copilot.

On the other hand, coding agent is very good at the following:

* **Tireless execution:** You can assign ten different tasks to coding agent right now and expect it to work on each of them.
* **Repetitive tasks:** Humans often get bored or miss things when doing repetitive tasks, such as updating naming conventions across many files. GitHub Copilot is great for completing these things, though!
* **Exploring possibilities:** If you’re considering tackling a problem a couple of different ways, you can assign each one of those different ways to coding agent. This gives you a quick idea of how each of those strategies might play out, without using a lot of development resources.ij

## Take this with you

Lingering backlog issues no longer stand a chance when you are equipped with GitHub Copilot and WRAP.

Have a dependency that needs to be updated? Somewhere that you could use more test coverage? New error handling patterns that you’d like to adopt across your codebase? Or perhaps you’d like to get a jumpstart on adding repository instructions and use the GitHub Copilot coding agent to do so?

Use WRAP to wrap up your backlog!

Get started with [GitHub Copilot](<https://github.com/features/copilot>) >

---

## Tags:

* [agent mode](<https://github.blog/tag/agent-mode/>)
* [agentic AI](<https://github.blog/tag/agentic-ai/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## Written by

![Brittany Ellich](<https://avatars.githubusercontent.com/u/25957322?v=4&s=200>)

### [Brittany Ellich](<https://github.blog/author/brittanyellich/>)

[@brittanyellich](<https://github.com/brittanyellich>)

Brittany is a software engineer at GitHub, working in Platform and Enterprise.

![Jason Etcovitch](<https://avatars.githubusercontent.com/u/10660468?v=4&s=200>)

### [Jason Etcovitch](<https://github.blog/author/jasonetco/>)

[@JasonEtco](<https://github.com/jasonetco>)


</details>
