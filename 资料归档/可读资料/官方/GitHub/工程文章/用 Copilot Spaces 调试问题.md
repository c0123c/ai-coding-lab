# 用 Copilot Spaces 调试问题

从 Issue 创建项目空间、添加资料和规则，再让 Agent 定位问题、生成拉取请求并迭代，支持共享调试上下文。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-12-04](<https://github.blog/ai-and-ml/github-copilot/how-to-use-github-copilot-spaces-to-debug-issues-faster/>)；更新：[2025-12-04](<https://github.blog/ai-and-ml/github-copilot/how-to-use-github-copilot-spaces-to-debug-issues-faster/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/how-to-use-github-copilot-spaces-to-debug-issues-faster/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/用 Copilot Spaces 调试问题.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 如何使用 GitHub Copilot Spaces 更快地调试问题

每个开发人员都知道这种痛苦：你提出了一个问题，在你可以编写一行代码之前，你就在寻找。您正在挖掘旧的拉取请求，搜索三个月前的设计文档，试图记住哪个文件具有安全指南。

那个狩猎阶段？这需要永远。这甚至不是实际的工作。即使你*想要*将AI带入图片中，[GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-copilot-spaces-checkout&utm_medium=blog&utm_campaign=dec25postuniverse>)仍然需要你做的同样的事情：上下文。如果没有它，您将得到不理解您的代码库的通用答案。

[GitHub Copilot Spaces](<https://docs.github.com/en/copilot/how-tos/provide-context/use-copilot-spaces/create-copilot-spaces>) 修复了这个问题。

Spaces 为 GitHub Copilot 提供了所需的项目知识——文件、拉取请求、问题、存储库——因此它的响应基于您的实际代码，而不是猜测。

*你是视觉学习者吗？观看下面的完整演示。 👇*

## 如何调试空格问题：

### 1。从一个问题开始

贡献者提出了一个问题，报告您的项目中 `check_call` 的使用不安全。

作为维护人员，您可能不知道立即修复它的最佳方法。就你自己而言，你首先要搜索存储库，检查过去的拉取请求，并梳理安全准则，以找出从哪里开始。

使用 Spaces，您无需手动执行此操作。创建一个空间，添加问题和关键文件或文档，然后让 Copilot 立即推理所有内容。

### 2。为您的项目创建一个空间

在空间内添加：

* 设计模式（例如，`/docs/security/check-patterns.md`、`/docs/design/architecture-overview.md`）
* 安全指南
* 无障碍建议
* 整个存储库（用于广泛覆盖）或针对您的特定用例精选的一组最相关的文件。当你有意识地考虑所包含的内容时，空间效果最好。
* URL 问题本身

### 3。添加Copilot的说明

每个空间都包含一个**说明**面板。您可以在此处告诉 Copilot 您希望它如何在项目中工作。

以下是一些有助于完成我们手头任务的示例说明：

```
You are an experienced engineer working on this codebase.
Always ground your answers in the linked docs and sources in this space.
Before writing code, produce a 3–5 step plan that includes:
  - The goal
  - The approach
  - The execution steps
Cite the exact files that justify your recommendations.
After I approve a plan, use the Copilot coding agent to propose a PR.
```

这些说明使 Copilot 保持一致。它不会产生你的存储库中不存在的模式，因为你已经告诉它引用其来源。

### 4。请Copilot调试问题

一切设置完毕后，询问 Copilot：“帮我调试这个问题。”

Copilot 已经知道您指的是哪个问题，因为它与空间相关。它解析所有来源，然后返回一个清晰的计划：

**目标：**修复`runBinaryCheck`的不安全使用，以确保输入路径得到验证。

**方法：**

1. 在存储库中搜索 `runBinaryCheck` 的用法
2. 将每种用法与安全文档中的安全模式进行比较
3. 确定所需的重构
4. 为每个不安全使用的文件准备一个差异

这不是一个通用的 LLM 答案。它立足于实际的项目背景。

### 5。生成拉取请求

一旦您批准该计划，请告诉 Copilot：“使用 Copilot 编码 agent 提出代码更改。”

agent 生成一个拉取请求：

* 之前版本和之后版本
* 对更改内容的解释
* 对通知修复的确切文件的引用
* 指导其选择的指令

拉取请求中的每个文件都会显示哪个来源通知了该建议。您可以在合并之前审核推理。

### 6。如果需要，请迭代

对某件事不满意？在拉取请求注释中提及 `@copilot` 以迭代现有的拉取请求，或者返回空间生成新的拉取请求。继续使用 Copilot，直到获得所需的内容。

### 7。与您的团队分享您的空间

默认情况下，空间是私有的。但您可以与特定个人、整个团队或整个组织共享它们（如果管理员允许）。

企业管理员控制谁可以共享什么内容，以便您与公司的安全策略保持一致。

## 使用 IDE 中的 GitHub Copilot 空间

现在可以通过 GitHub MCP 服务器在您的 IDE 中使用空间。

安装MCP服务器，您可以直接从编辑器调用您的空间。相同的策划背景，相同的接地答案，但就在您已经工作的地方。

能够从 IDE 呼叫空间对我来说是一个改变。它让我能够保持专注，无需在浏览器和编辑器之间切换，从而减少了调试过程中的大量摩擦。

## 即将推出

以下是路线图上的内容：

* **公开 API**
* **图片支持**
* **其他文件类型**，例如doc/docx和PDF

## 团队目前使用空间的三种方式

**1。代码生成和调试。** 使用带有 Copilot 编码 agent 的空格来生成与您的模式、安全规则和架构一致的拉取请求。

**2。规划功能。** 链接问题、设计文档和存储库以规划功能和草案需求。向 Copilot 询问技术计划，它会生成拉取请求。

**3。知识共享和入门。** 空间成为活的知识库。新工程师入职速度更快。现有工程师不再重复回答相同的问题。

## 在下一期中尝试一下

这是我向你提出的挑战：

1. 创建一个GitHub Copilot空间。
2. 添加一期和三到四个关键文件。
3. 添加简单的说明。
4. 请Copilot分析问题并提出调试方案。
5. 批准该计划。
6. 触发编码agent生成拉取请求。

当 Copilot 真正了解您的项目时，您将确切地看到节省了多少时间。 **你的AI助手永远不应该缺乏正确的上下文。**这就是空间的用途。

**想看完整的演示吗？** 观看 [GitHub 结帐剧集于 Copilot 空间](<https://www.youtube.com/watch?v=noVdaj7sEWA>) 和 [尝试 GitHub Copilot 空间](<https://gh.io/tryspaces>).

---

## 标签：

* [GitHub 结帐](<https://github.blog/tag/github-checkout/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot 空间](<https://github.blog/tag/github-copilot-spaces/>)

## 撰写者

![安德里亚·格里菲斯](<https://github.blog/wp-content/uploads/2025/08/Andrea-Griffiths_avatar_1755783168-200x200.jpeg>)

### [安德里亚·格里菲斯](<https://github.blog/author/andreagriffiths11/>)

[@AndreaGriffiths11](<https://github.com/andreagriffiths11>)

Andrea 是 GitHub 的高级开发人员倡导者，在开发人员工具方面拥有十多年的经验。她将技术深度与让先进技术更容易获得的使命结合起来。从陆军服役和施工管理转向软件开发后，她以独特的视角将复杂的工程概念与实际实施联系起来。她与威尔士伴侣、两个儿子和两只狗住在佛罗里达州，在那里她继续通过 GitHub 的全球计划推动创新并支持开源。在线查找她@acolombiadev。


## 原文对照

<details>
<summary>展开完整原文</summary>

# How to use GitHub Copilot Spaces to debug issues faster

Every developer knows this pain: you open an issue, and before you can write a single line of code, you’re hunting. You’re digging through old pull requests, searching for that design doc from three months ago, trying to remember which file has the security guidelines.

That hunting phase? It takes forever. And it’s not even the actual work. And even if you *want* to bring AI into the picture, [GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-copilot-spaces-checkout&utm_medium=blog&utm_campaign=dec25postuniverse>) still needs the same thing you do: context. Without it, you get generic answers that don’t understand your codebase.

[GitHub Copilot Spaces](<https://docs.github.com/en/copilot/how-tos/provide-context/use-copilot-spaces/create-copilot-spaces>) fixes that.

Spaces gives GitHub Copilot the project knowledge it needs—files, pull requests, issues, repos—so its responses are grounded in your actual code, not guesses.

*Are you a visual learner? Watch the full demo below. 👇*

## How to debug issues with spaces:

### 1. Start with an issue

A contributor opened an issue reporting an unsafe usage of `check_call` in your project.

As a maintainer, you might not know the best way to fix it immediately. On your own, you’d start by searching the repo, checking past pull requests, and combing through security guidelines just to figure out where to begin.

With Spaces, you don’t have to do that manually. Create a space, add the issue and the key files or docs, and let Copilot reason across everything at once.

### 2. Create a space for your project

Inside the space, add:

* Design patterns (e.g., `/docs/security/check-patterns.md`, `/docs/design/architecture-overview.md`)
* Security guidelines
* Accessibility recommendations
* The entire repository (for broad coverage) or a curated set of the most relevant files for your specific use case. Spaces work best when you’re intentional about what you include.
* The URL to the issue itself

### 3. Add Instructions for Copilot

Each space includes an **Instructions** panel. This is where you tell Copilot how you want it to work inside your project.

Here are some example instructions that will help with our task at hand:

```
You are an experienced engineer working on this codebase.
Always ground your answers in the linked docs and sources in this space.
Before writing code, produce a 3–5 step plan that includes:
  - The goal
  - The approach
  - The execution steps
Cite the exact files that justify your recommendations.
After I approve a plan, use the Copilot coding agent to propose a PR.
```

These instructions keep Copilot consistent. It won’t hallucinate patterns that don’t exist in your repo because you’ve told it to cite its sources.

### 4. Ask Copilot to debug the issue

With everything set up, ask Copilot: “Help me debug this issue.”

Copilot already knows which issue you mean because it’s linked to the space. It parses through all the sources, then returns a clear plan:

**Goal:** Fix unsafe usage of `runBinaryCheck` to ensure input paths are validated.

**Approach:**

1. Search the repo for usages of `runBinaryCheck`
2. Compare each usage to the safe pattern in the security docs
3. Identify the required refactor
4. Prepare a diff for each file with unsafe usage

This isn’t a generic LLM answer. It’s grounded in the actual project context.

### 5. Generate the pull request

Once you approve the plan, tell Copilot: “Propose code changes using Copilot coding agent.”

The agent generates a pull request with:

* The before version and the after version
* An explanation of what changed
* References to the exact files that informed the fix
* The instructions that guided its choices

Every file in the pull request shows which source informed the suggestion. You can audit the reasoning before you merge.

### 6. Iterate if you need to

Not happy with something? Mention `@copilot` in the pull request comments to iterate on the existing pull request, or go back to the space to generate a fresh one. Keep working with Copilot until you get exactly what you need.

### 7. Share your space with your team

Spaces are private by default. But you can share them with specific individuals, your entire team, or your whole organization (if admins allow it).

Enterprise admins control who can share what, so you stay aligned with your company’s security policies.

## Use GitHub Copilot Spaces from your IDE

Spaces are now available in your IDE via the GitHub MCP Server.

Install the MCP server, and you can call your spaces directly from your editor. Same curated context, same grounded answers, but right where you’re already working.

Being able to call a space from the IDE has been a game changer for me. It lets me stay focused without switching between the browser and my editor, which cuts out a ton of friction in debugging.

## Coming soon

Here’s what’s on the roadmap:

* **Public API**
* **Image support**
* **Additional file types** like doc/docx and PDFs

## Three ways teams are using spaces right now

**1. Code generation and debugging.** Use spaces with Copilot coding agent to generate pull requests aligned with your patterns, security rules, and architecture.

**2. Planning features.** Link issues, design docs, and repos to plan features and draft requirements. Ask Copilot for a technical plan and it generates a pull request.

**3. Knowledge sharing and onboarding.** Spaces become living knowledge bases. New engineers onboard faster. Existing engineers stop answering the same questions repeatedly.

## Try it on your next issue

Here’s my challenge to you:

1. Create a GitHub Copilot Space.
2. Add one issue and three to four key files.
3. Add simple instructions.
4. Ask Copilot to analyze the issue and propose a debugging plan.
5. Approve the plan.
6. Trigger the coding agent to generate a pull request.

You’ll see exactly how much time you save when Copilot actually knows your project. **Your AI assistant should never lack the right context.** That’s what spaces are for.

**Want to see the full demo?** Watch the [GitHub Checkout episode on Copilot Spaces](<https://www.youtube.com/watch?v=noVdaj7sEWA>) and [try GitHub Copilot Spaces](<https://gh.io/tryspaces>).

---

## Tags:

* [GitHub Checkout](<https://github.blog/tag/github-checkout/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot Spaces](<https://github.blog/tag/github-copilot-spaces/>)

## Written by

![Andrea Griffiths](<https://github.blog/wp-content/uploads/2025/08/Andrea-Griffiths_avatar_1755783168-200x200.jpeg>)

### [Andrea Griffiths](<https://github.blog/author/andreagriffiths11/>)

[@AndreaGriffiths11](<https://github.com/andreagriffiths11>)

Andrea is a Senior Developer Advocate at GitHub with over a decade of experience in developer tools. She combines technical depth with a mission to make advanced technologies more accessible. After transitioning from Army service and construction management to software development, she brings a unique perspective to bridging complex engineering concepts with practical implementation. She lives in Florida with her Welsh partner, two sons, and two dogs, where she continues to drive innovation and support open source through GitHub's global initiatives. Find her online @acolombiadev.


</details>
