# 从想法到拉取请求的 CLI 开发流程

依次介绍明确意图、建立项目、定位失败、批量修改、进入编辑器整理代码和提交审查，展示从终端到 GitHub 的衔接。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-02-27](<https://github.blog/ai-and-ml/github-copilot/from-idea-to-pull-request-a-practical-guide-to-building-with-github-copilot-cli/>)；更新：[2026-04-14](<https://github.blog/ai-and-ml/github-copilot/from-idea-to-pull-request-a-practical-guide-to-building-with-github-copilot-cli/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/from-idea-to-pull-request-a-practical-guide-to-building-with-github-copilot-cli/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/从想法到拉取请求的 CLI 开发流程.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 从想法到拉取请求：使用 GitHub Copilot CLI 进行构建的实用指南

大多数开发人员已经在终端中进行了实际工作。

我们在那里初始化项目，在那里运行测试，在那里调试CI故障，并在准备好审查之前进行快速的机械更改。 [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-idea-to-pull-request-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) 通过帮助您直接在终端中从意图转变为可审查的差异来适应这一现实，然后将该工作带到您的编辑器或拉取请求中。

本博客介绍了使用 Copilot CLI 创建和发展应用程序 [ 的实用工作流程，该应用程序基于新的 GitHub 技能练习 ](<https://github.com/skills/create-applications-with-the-copilot-cli>)。技能练习提供了指导性的实践演练；这篇文章重点介绍*为什么*每个步骤有效以及*何时*在实际项目中使用它。

## Copilot CLI 是什么（和不是）

Copilot CLI 是终端中的 GitHub 感知编码 agent。您可以用自然语言描述您想要的内容，在接触代码之前使用 `/plan` 概述工作，然后在运行任何内容之前查看具体命令或差异。 Copilot 可能会在内部推理，但它仅在您明确批准后才执行命令或应用更改。

在实践中，Copilot CLI可以帮助您：

* 根据您的意图探索问题
* 使用 `/plan` 提出结构化计划（或者您可以点击 `Shift` + `Tab` 进入计划模式），或建议您可以查看的具体命令和差异
* 生成或修改文件
* 解释发生故障的地方

它*不*做什么：

* 未经您的批准，静默运行命令或应用更改
* 取代精心设计的工作
* 消除审查的需要

您可以控制运行的内容、更改的内容以及发布的内容。

## 步骤 1：从意图开始，而不是搭建脚手架

不要从选择框架或复制模板开始，而是从说明您想要构建的内容开始。

从空目录运行：

```
copilot
> Create a small web service with a single JSON endpoint and basic tests
```

如果您想在单个提示中生成提案而不是进入交互模式，您还可以运行：

```
copilot -p "Create a small web service with a single JSON endpoint and basic tests"
```

在技能练习中，会重复使用此模式：首先描述意图，然后决定您实际想要运行哪些建议命令。

在这个阶段，Copilot CLI正在探索问题空间。它可能：

* 建议堆栈
* 大纲文件
* 建议设置命令

没有什么是自动运行的。在决定执行什么之前，您会检查所有内容。这使得 CLI 成为在进行设计之前进行实验的好地方。

## 步骤2：只搭建你准备拥有的东西

一旦您找到了自己满意的方向，请询问 Copilot CLI 来帮助搭建脚手架：

```
> Scaffold this as a minimal Node.js project with a test runner and README
```

这是 Copilot CLI 最直接有用的地方。它可以：

* 创建目录和配置，
* Wire 基本项目结构，
* 生成样板文件，否则您需要手动键入或复制。

Copilot CLI 不“拥有”项目结构。它建议基于通用约定的脚手架，您应该将其视为起点，而不是处方。

重要的限制是你始终要对结果负责。像处理来自队友的代码一样对待输出：检查它、编辑它或丢弃它。

## 步骤 3：在故障点迭代

直接在 Copilot CLI 内运行测试：

```
Run all my tests and make sure they pass
```

当出现故障时，请向 Copilot 询问同一会话中的确切故障：

```
> Why are these tests failing?
```

如果您想要具体的建议而不是解释，请尝试：

```
> Fix this test failure and show the diff
```

这种模式（运行（`!command`）、检查、询问、审查差异）使 agent 立足于实际输出而不是抽象提示。

💡**专业提示：**在实践中，当您想要理解时，`explain`很有用，而当您想要一个可以查看的具体提案时，`suggest`会更好。 [在我们的指南](<https://github.blog/ai-and-ml/github-copilot/a-cheat-sheet-to-slash-commands-in-github-copilot-cli/>)中的Copilot CLI中了解有关斜杠命令的更多信息。

## 步骤 4：进行机械或回购范围的更改

Copilot CLI 也非常适合易于描述但执行起来繁琐的更改：

```
> Rename all instances of X to Y across the repository and update tests
```

由于这些更改是机械性的且有范围的，因此很容易审查并且易于回滚。 CLI 为您提供了具体的差异，而不是生成的文本墙。

## 步骤 5：当您需要开始调整代码时，移至编辑器

最终，速度比精度更重要。

这是到您的编辑器或 IDE 的自然切换点，因此它可以：

* 关于边缘情况的原因
* 完善API
* 做出设计决策

Copilot在那里也可以工作，但关键点是*为什么*你切换环境。 CLI 可以帮助您快速获得真实的结果。在 IDE 中，您可以将代码塑造成您想要的样子。

一个好的经验法则：

* **CLI：**使用`/plan`，生成`/diff`，低礼快速行动
* **IDE：** 当您需要完善逻辑并做出您将在审查中辩护的决策时，请使用 `/IDE`
* **GitHub:** 提交，使用命令 `/delegate` 打开拉取请求，并异步协作

## 步骤 6：在 GitHub 上发货

一旦更改看起来不错，请提交并打开拉取请求，您可以通过自然语言的 Copilot CLI 执行此操作：

```
Add and commit all files with a applicable descriptive messages, push the changes.
```

```
Create a pull request and add Copilot as a reviewer
```

现在工作变得持久：

* 可由队友审核
* 可在 CI 中测试
* 准备好异步迭代

这就是 Copilot 的价值复合的地方，作为以运输结束的流程的一部分，而不是仅仅作为一个单一的表面。技能练习有意在此结束，因为这是 Copilot 的价值变得持久的地方：在提交、拉取请求和审查（不仅仅是建议）中。

## 一个工作流程，三个时刻

Copilot 的一个有用的心理模型如下所示：

* **CLI**：以低仪式快速证明价值
* **IDE**：塑造和完善您的代码
* **GitHub**：审核、协作和交付

Copilot CLI 之所以强大，正是因为它适合这个系统，而不是试图取代它。

## 随身携带这个

当您将 Copilot CLI 视为动力工具而不是判断的替代品时，它是最有用的。

如果使用得当，它可以帮助您更快地将意图转变为具体的改变：探索想法、搭建项目、诊断故障以及直接在终端中处理机械工作。当精度很重要时，您就需要使用编辑器。当工作准备好共享时，它会作为拉取请求登陆 GitHub——可审查、可测试和可交付。

该流程比任何单个命令都更重要。

如果您从本指南中拿走一件事，那就是：Copilot 当它自然地适合开发人员构建软件的方式时效果最好。从CLI开始，摆脱困境或快速行动，在IDE放慢速度，做出可以支持的决定，并依靠GitHub使工作持久。

开始使用 [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-idea-to-pull-request-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) 或参加 [技能课程 >](<https://github.com/skills/create-applications-with-the-copilot-cli>)

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)
* [GitHub 技能](<https://github.blog/tag/github-skills/>)

## 撰写者

![阿里 LiVigni](<https://avatars.githubusercontent.com/u/4140343?v=4&s=200>)

### [阿里 LiVigni](<https://github.blog/author/arilivigni/>)

[@arilivigni](<https://github.com/arilivigni>)

高级服务交付工程师，GitHub


## 原文对照

<details>
<summary>展开完整原文</summary>

# From idea to pull request: A practical guide to building with GitHub Copilot CLI

Most developers already do real work in the terminal.

We initialize projects there, run tests there, debug CI failures there, and make fast, mechanical changes there before anything is ready for review. [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-idea-to-pull-request-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) fits into that reality by helping you move from intent to reviewable diffs directly in your terminal—and then carry that work into your editor or pull request.

This blog walks through a practical workflow for using Copilot CLI to create and evolve an application, [based on a new GitHub Skills exercise](<https://github.com/skills/create-applications-with-the-copilot-cli>). The Skills exercise provides a guided, hands-on walkthrough; this post focuses on *why* each step works and *when* to use it in real projects.

## What Copilot CLI is (and is not)

Copilot CLI is a GitHub-aware coding agent in your terminal. You can describe what you want in natural language, use `/plan` to outline the work before touching code, and then review concrete commands or diffs before anything runs. Copilot may reason internally, but it only executes commands or applies changes after you explicitly approve them.

In practice, Copilot CLI helps you:

* Explore a problem based on your intent
* Propose structured plans using `/plan` (or you can hit `Shift` + `Tab` to enter planning mode), or suggest concrete commands and diffs you can review
* Generate or modify files
* Explain failures where they occur

What it does *not* do:

* Silently run commands or apply changes without your approval
* Replace careful design work
* Eliminate the need for review

You stay in control of what runs, what changes, and what ships.

## Step 1: Start with intent, not scaffolding

Instead of starting by choosing a framework or copying a template, start by stating what you want to build.

From an empty directory, run:

```
copilot
> Create a small web service with a single JSON endpoint and basic tests
```

If you want to generate a proposal in a single prompt instead of entering interactive mode, you can also run:

```
copilot -p "Create a small web service with a single JSON endpoint and basic tests"
```

In the Skills exercise, this pattern is used repeatedly: describe intent first, then decide which suggested commands you actually want to run.

At this stage, Copilot CLI is exploring the problem space. It may:

* Suggest a stack
* Outline files
* Propose setup commands

Nothing runs automatically. You inspect everything before deciding what to execute. This makes the CLI a good place to experiment before committing to a design.

## Step 2: Scaffold only what you’re ready to own

Once you see a direction you’re comfortable with, ask Copilot CLI to help scaffold:

```
> Scaffold this as a minimal Node.js project with a test runner and README
```

This is where Copilot CLI is most immediately useful. It can:

* Create directories and config,
* Wire basic project structure,
* Generate boilerplate you would otherwise type or copy by hand.

Copilot CLI does not “own” the project structure. It suggests scaffolding based on common conventions, which you should treat as a starting point, not a prescription.

The important constraint is that you’re always responsible for the result. Treat the output like code from a teammate: review it, edit it, or discard it.

## Step 3: Iterate at the point of failure

Run your tests directly inside Copilot CLI:

```
Run all my tests and make sure they pass
```

When something fails, ask Copilot about that exact failure in the same session:

```
> Why are these tests failing?
```

If you want a concrete proposal instead of an explanation, try:

```
> Fix this test failure and show the diff
```

This pattern—run (`!command`), inspect, ask, review diff—keeps the agent grounded in real output instead of abstract prompts.

💡**Pro tip:** In practice, `explain` is useful when you want understanding, while `suggest` is better when you want a concrete proposal you can review. [Learn more about slash commands in Copilot CLI in our guide](<https://github.blog/ai-and-ml/github-copilot/a-cheat-sheet-to-slash-commands-in-github-copilot-cli/>).

## Step 4: Make mechanical or repo-wide changes

Copilot CLI is also well suited to changes that are easy to describe but tedious to execute:

```
> Rename all instances of X to Y across the repository and update tests
```

Because these changes are mechanical and scoped, they’re easy to review and easy to roll back. The CLI gives you a concrete diff instead of a wall of generated text.

## Step 5: Move into your editor when you need to start shaping your code

Eventually, speed matters less than precision.

This is the natural handoff point to your editor or IDE, so it can:

* Reason about edge cases
* Refine APIs
* Make design decisions

Copilot works there too, but the key point is *why* you switch environments. The CLI helps you quickly get to something real. The IDE is where you can shape your code into exactly what you want.

A good rule of thumb:

* **CLI:** use `/plan`, generate a `/diff`, and move quickly with low ceremony
* **IDE:** use `/IDE` when you need to refine logic and make decisions you’ll defend in review
* **GitHub:** commit, open a pull request with the command `/delegate`, and collaborate asynchronously

## Step 6: Ship on GitHub

Once the changes look good, commit and open a pull request which you can do through the Copilot CLI in natural language:

```
Add and commit all files with a applicable descriptive messages, push the changes.
```

```
Create a pull request and add Copilot as a reviewer
```

Now the work becomes durable:

* Reviewable by teammates
* Testable in CI
* Ready for async iteration

This is where Copilot’s value compounds as part of a flow that ends with shipping versus just being a single surface. The Skills exercise intentionally ends here, because this is where Copilot’s value becomes durable: in commits, pull requests, and review (not just suggestions).

## One workflow, three moments

A helpful mental model for Copilot looks like this:

* **CLI**: prove value quickly with low ceremony
* **IDE**: shape and refine your code
* **GitHub**: review, collaborate, and ship

Copilot CLI is powerful precisely because it fits into this system instead of trying to replace it.

## Take this with you

Copilot CLI is most useful when you treat it like a tool for momentum, not a replacement for judgment.

Used well, it helps you move from intent to concrete changes faster: exploring ideas, scaffolding projects, diagnosing failures, and handling mechanical work directly in the terminal. When precision matters, you move into your editor. When the work is ready to share, it lands on GitHub as a pull request—reviewable, testable, and shippable.

That flow matters more than any single command.

If you take one thing away from this guide, it’s this: Copilot works best when it fits naturally into how developers already build software. Start in the CLI to get unstuck or move quickly, slow down in the IDE to make decisions you can stand behind, and rely on GitHub to make the work durable.

Get started with [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-idea-to-pull-request-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) or take the [Skills course >](<https://github.com/skills/create-applications-with-the-copilot-cli>)

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)
* [GitHub Skills](<https://github.blog/tag/github-skills/>)

## Written by

![Ari LiVigni](<https://avatars.githubusercontent.com/u/4140343?v=4&s=200>)

### [Ari LiVigni](<https://github.blog/author/arilivigni/>)

[@arilivigni](<https://github.com/arilivigni>)

Senior Service Delivery Engineer, GitHub


</details>
