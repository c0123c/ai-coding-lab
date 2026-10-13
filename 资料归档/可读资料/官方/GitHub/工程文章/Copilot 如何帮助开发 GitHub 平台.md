# Copilot 如何帮助开发 GitHub 平台

介绍内部清理、缺陷修复、测试维护、新功能、迁移与代码库审计等案例，说明可交给 Agent 的工程工作。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-11-12](<https://github.blog/ai-and-ml/github-copilot/how-copilot-helps-build-the-github-platform/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/how-copilot-helps-build-the-github-platform/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/Copilot 如何帮助开发 GitHub 平台.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Copilot如何帮助构建GitHub平台

作为 GitHub 的工程师，“dogfooding”是我们文化的核心。我们使用我们向开发人员提供的相同工具在 GitHub 上构建 GitHub。但随着 [GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post>) 从自动完成建议演变为复杂的 AI 助手，我们自己对它的使用也发生了变化。

它不再只是我们编辑器中的一个工具。我们已将 Copilot 直接集成到我们的开发生命周期中。

在我们的核心存储库（我们用来构建 github.com 的存储库）中，`@Copilot` 不仅仅是建议代码；它还提供了建议。它是一个积极的贡献者。它接受人类工程师分配的问题，打开拉取请求，并完成分配的工作。

Copilot 是 GitHub 内一位多产的工程师，它承担了我们一些最耗时、最乏味的任务。在本文中，我们分析了 Copilot 在我们的核心存储库中创建的一个月的拉取请求。这是我们发现的。

### Copilot 做简单的工作，节省时间

在我们开始复杂的架构工作之前，值得注意的是 Copilot 的日常工作有多少是为了加速加起来的小任务。这些是使工程师免于不断切换环境的快速修复方法。

* **UI 和复制调整：** 它的任务是修复较小的 UI 错误，例如重新对齐未对齐的图标，或更新过滤栏中的占位符文本以使其更加准确。
* **文档和清理：**它还可以处理简单的清理。在一项拉取请求中，`@Copilot` 被分配修复 100 不同文件中注释和文档字符串中的 **161 拼写错误 **。这是一项简单的任务，但没有人类工程师愿意花一个下午的时间来做。

### 关键的清理和维护

健康的代码库需要持续的维护。这就是`@Copilot`的闪光点。拉取请求的很大一部分集中在代码维护、现代化和大规模重构上，这些对于人类工程师来说非常耗时，包括：

* **删除功能标志：**这是一个巨大的标志。 `@Copilot` 不断被分配来清理已弃用的功能标志，删除整个代码库中的条件逻辑、过时的代码和旧的测试。
* **大规模重构：**当我们需要重命名跨应用程序使用的类时，`@Copilot`可以承担。它甚至编写了大量的、存储库范围内的拉取请求来重命名核心内部类，这对于任何开发人员来说都是乏味且耗时的任务。
* **性能优化：**它发现并修复常见的性能反模式，在高流量区域用更优化的替代方案替换低效代码。

### 修复错误和不稳定的测试

`@Copilot`还帮助我们解决了一些错误。它修复了生产问题并提高了我们的 CI/CD 管道的稳定性。

* **生产错误：**这不仅仅是修复简单的错误；它解决了核心逻辑中棘手的 `NoMethodError` 问题，甚至解决了我们的缓存基础设施中复杂的错误屏蔽问题。
* **性能瓶颈：**其最有影响力的贡献之一是一个拉取请求，该请求修复了一个严重的性能问题，其中 git Push 为 Codespaces 中的工程师花费了 **~15 分钟**。
* **修复不稳定的测试：**我们都知道测试破坏构建的痛苦。 `@Copilot` 被定期指派调查和修复这些问题。

### 构建新功能

这就是事情变得非常有趣的地方。 `@Copilot` 不仅仅是维护旧代码；它正在积极构建新功能。人类工程师指定问题中的工作，将其分配给 `@Copilot`，然后它就开始工作。

它正在添加：

* **新的API端点：**一个拉取请求添加了一个新的RESTAPI端点来列出存储库安全咨询评论。
* **新的内部工具：**这不仅仅是添加生产功能。 Copilot 已被广泛用于改进我们的内部工具。我们的内部内联网、培训站点、入职培训等。所有这些都由 Copilot 进行了大量维护。

### 迁移和安全

除了日常任务之外，我们还将 `@Copilot` 分配给对平台未来至关重要的高风险、复杂项目。

* **安全门：** `@Copilot` 的任务是添加安全门，以防止我们自己的内部集成执行敏感操作，例如修改版本或发布资产。
* **数据库迁移：**它甚至可以处理数据库架构迁移。我们为其分配了关键的高精度任务，例如迁移色谱柱类型以支持新标准。
* **文档创建：**用于使文档与快速移动的代码保持同步。例如，它编写了许多拉取请求，以向我们的速率限制代码添加注释，以确保开发人员轻松记住使其与其他服务保持同步。

### 代码库范围内的审计和分析：发现问题，然后解决它们

在一些最高级的任务中，`@Copilot` 充当研究员。我们可以给它分配一个不明确的任务，它会分析代码库并在拉取请求中报告其发现。

在一项拉取请求中，它的任务是审核我们所有的 Codespaces 功能标志。它返回了一个拉取请求，其中包含一份综合报告，对整个代码中的所有标志及其引用进行了分类。

在另一个例子中，它执行了“授权查询的综合分析”，以确定性能和安全改进的机会。这将 Copilot 的角色从代码生成转移到系统级架构分析，帮助开发人员直接解决复杂问题，而不是花费数天时间来识别问题。

### 一种新的协作方式

如果您在存储库中检查 Copilot 的合并拉取请求率，您会发现它低于人类贡献者。这是预料之中的——而且很有用。

这是我们发现的模式：

* 您为 `@Copilot` 分配了一个问题
* 它使用第一遍解决方案打开拉取请求
* 您可以像其他拉取请求一样审查它
* **然后您选择**来合并它，在分支上迭代，或者关闭它并采取不同的方法。

价值不在于盲目合并。 *这不是从零开始。*你会立即得到一个具体的实现来进行批评。所有样板和基本脚手架都已处理完毕。

这将我们的注意力从编写*所有*代码转移开。相反，您可以直接跳到工程中最关键的部分：解决核心问题，完善 Copilot 的建议，在不适合 AI 的代码库区域工作，并拥有架构、安全性和用户体验。这不是为了让我们的工作自动化；而是为了让我们的工作自动化。这是关于让Copilot处理乏味的80%工作。这使我们能够将我们的专业知识奉献给真正重要的关键 20%。

开始使用 [GitHub Copilot >](<https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post>)

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## 撰写者

![马特·奈伊](<https://avatars.githubusercontent.com/u/5862416?v=4&s=200>)

### [马特·奈伊](<https://github.blog/author/mattnigh/>)

[@mattnigh](<https://github.com/mattnigh>)

项目经理总监，我领导 GitHub 的 AI forEveryone 项目。


## 原文对照

<details>
<summary>展开完整原文</summary>

# How Copilot helps build the GitHub platform

As engineers at GitHub, “dogfooding” is core to our culture. We build GitHub on GitHub, using the same tools we ship to developers. But as [GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post>) has evolved from an autocomplete suggestion to a sophisticated AI assistant, our own use of it has evolved, too.

It’s no longer just a tool in our editors. We’ve integrated Copilot directly into our development lifecycle.

Inside our core repository, the one we use to build github.com, `@Copilot` isn’t just suggesting code; it’s an active contributor. It gets assigned issues by human engineers, opens pull requests, and does the work assigned.

Copilot is a prolific engineer within GitHub, and it’s taking on some of our most time-consuming and tedious tasks. For this article, we analyzed a month of pull requests created within our core repo by Copilot. Here’s what we found.

### Copilot does simple work that saves time

Before we get to the complex, architectural work, it’s worth noting how much of Copilot’s day-to-day work is about accelerating the small tasks that add up. These are the quick fixes that save engineers from constant context-switching.

* **UI and copy tweaks:** It gets tasked with fixing minor UI bugs, like realigning a misaligned icon, or updating placeholder text in a filter bar to be more accurate.
* **Documentation and cleanup:** It also handles straightforward cleanup. In one pull request, `@Copilot` was assigned to fix **161 typos** in comments and documentation strings across 100 different files. It’s a simple task, but one that no human engineer wants to spend an afternoon doing.

### Critical cleanup and maintenance

A healthy codebase requires constant care. This is where `@Copilot` shines. A significant portion of the pull requests were focused on code maintenance, modernization, and large-scale refactors that would have been time-consuming for human engineers, including:

* **Removing feature flags:** This is a huge one. `@Copilot` is constantly assigned to clean up deprecated feature flags, removing the conditional logic, stale code, and old tests across the entire codebase.
* **Large-scale refactoring:** When we need to rename a class used across the application, `@Copilot` can take it on. It’s even authored massive, repository-wide pull requests to rename core internal classes, a task that would be tedious and time-consuming for any developer.
* **Performance optimization:** It finds and fixes common performance anti-patterns, replacing inefficient code with more optimized alternatives in high-traffic areas.

### Fixing bugs and flaky tests

`@Copilot` also helped us solve a number of bugs. It’s patched production issues and improved the stability of our CI/CD pipeline.

* **Production errors:** It’s not just fixing simple bugs; it’s resolving tricky `NoMethodError` issues in core logic and even fixing complex error masking issues in our caching infrastructure.
* **Performance bottlenecks:** One of its most impactful contributions was a pull request that fixed a severe performance issue where git push was taking **~15 minutes** for engineers in Codespaces.
* **Fixing flaky tests:** We all know the pain of a test breaking a build. `@Copilot` was regularly assigned to investigate and fix them.

### Building new features

This is where it gets really interesting. `@Copilot` isn’t just maintaining old code; it’s actively building new functionality. Human engineers spec out the work in an issue, assign it to `@Copilot`, and it gets to work.

It’s adding:

* **New API endpoints:** One pull request added a new REST API endpoint to list repository security advisory comments.
* **New internal tools:** It’s not just adding production features. Copilot has been extensively used to improve our internal tools. Our internal intranet, training sites, onboarding, etc. All of these have been maintained significantly by Copilot.

### Migrations and security

Beyond daily tasks, we’re also assigning `@Copilot` to high-stakes, complex projects that are critical for the platform’s future.

* **Security gating:** `@Copilot` has been tasked with adding security gates to prevent our own internal integrations from performing sensitive actions, such as modifying releases or release assets.
* **Database migrations:** It’s even handling database schema migrations. We’ve assigned it critical, high-precision tasks like migrating column types to support new standards.
* **Documentation creation:** It’s used to keep documentation in sync with fast-moving code. For example, it has authored numerous pull requests to add comments to our rate-limiting code to ensure developers easily remember to keep it synchronized with another service.

### Codebase-wide audits and analysis: Discerning problems, then solving them

In some of its most advanced tasks, `@Copilot` acts as a researcher. We can assign it an ambiguous task, and it will analyze the codebase and report back with its findings in a pull request.

In one pull request, it was tasked with auditing all our Codespaces feature flags. It returned a pull request with a comprehensive report categorizing all the flags and their references throughout the code.

In another, it performed a “comprehensive analysis of authorization queries” to identify opportunities for performance and safety improvements. This moves Copilot’s role from code generation to systems-level architectural analysis, helping developers to jump straight to solving a complex problem rather than spending days just identifying it.

### A new way to collaborate

If you check Copilot’s merged pull request rate in your repo, you’ll notice it’s lower than human contributors. That’s expected—and useful.

Here’s the pattern we discovered:

* You assign `@Copilot` an issue
* It opens a pull request with a first-pass solution
* You review it like any other pull request
* **Then you choose** to merge it, iterate on the branch, or close it and take a different approach.

The value isn’t in blindly merging. *It’s in not starting from zero.* You’ll get a concrete implementation to critique right away. All the boilerplate and the basic scaffolding is already handled.

This shifts our focus away from writing *all* the code. Instead, you get to jump straight to the most critical parts of engineering: solving the core problem, refining Copilot’s suggestions, working in areas of the codebase not fit for AI, and owning the architecture, security, and user experience. It’s not about automating our jobs; it’s about letting Copilot handle the tedious 80% of the work. This frees us up to dedicate our expertise to the critical 20% that truly matters.

Start using [GitHub Copilot >](<https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post>)

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## Written by

![Matt Nigh](<https://avatars.githubusercontent.com/u/5862416?v=4&s=200>)

### [Matt Nigh](<https://github.blog/author/mattnigh/>)

[@mattnigh](<https://github.com/mattnigh>)

Program Manager Director, I lead the AI for Everyone program at GitHub.


</details>
