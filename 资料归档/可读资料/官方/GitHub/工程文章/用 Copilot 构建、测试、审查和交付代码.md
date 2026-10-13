# 用 Copilot 构建、测试、审查和交付代码

通过实际提示展示 Agent 模式、CLI、任务管理和跨文件推理，串起代码开发、验证、审查与交付。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-11-05](<https://github.blog/ai-and-ml/github-copilot/a-developers-guide-to-writing-debugging-reviewing-and-shipping-code-faster-with-github-copilot/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/a-developers-guide-to-writing-debugging-reviewing-and-shipping-code-faster-with-github-copilot/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/用 Copilot 构建、测试、审查和交付代码.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub Copilot 教程：如何更快地构建、测试、审查和交付代码（带有真实提示）

如果您在任务控制推出之前就没有使用过[GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post>)，那么您还没有体验过它现在的功能。

Copilot 曾经是一个自动完成工具。现在，它是一个完整的 AI 编码助手，可以直接在 VS Code 或 GitHub 内运行多步骤工作流程、修复失败的测试、审查拉取请求和交付代码。

回到 2021，Copilot 更改了您 * 编辑 * 代码的方式。 [如今，Agent HQ 和任务控制正在改变您构建、审查、保护和交付软件的方式 ](<https://github.blog/news-insights/company-news/welcome-home-agents/>)。

这是一个例子：

```
// Before
"Write tests for this module" = manual setup, fixtures, and edge cases

// Now
Ask Copilot: "Generate Jest tests for userSessionService with cache-enabled branch coverage"
Full test suite + explanations in record time
```

在底层，Copilot 在多个模型上运行，这些模型针对推理、速度和代码理解进行了调整。它可以查看您项目的更多内容，生成更准确的结果，并在编辑器、终端和 GitHub 之间自然移动。

本指南通过工作示例、最佳实践和提示逐步介绍了新的 Copilot 体验的各个部分，您现在就可以尝试（您应该这样做）。

## Copilot 有什么新变化

### 更大的上下文+跨文件推理（现在通过任务控制浮出水面）

Copilot 的早期版本只能看到您输入的内容。现在，它可以读取多个文件，帮助它理解模块之间的意图和关系。

**询问**：在任务控制中：“使用过时的加密库查找每个功能并将其重构为新的API。打开草稿PR。”

Copilot 可以跟踪代码库中的模式、进行更新并解释发生了什么变化。

### 您可以选择适合工作的型号

现在，您可以根据需要选择模型：一个模型针对原型设计时的速度进行了优化，另一种模型则用于在复杂重构过程中进行更深入的推理。

### 它超出了代码完成的范围

Copilot 现在是为工作流程的每个步骤构建的一套工具：

* [**任务控制**](<https://github.blog/changelog/2025-10-28-a-mission-control-to-assign-steer-and-track-copilot-coding-agent-tasks/>)**：**运行多步骤任务，生成测试并打开拉取请求。
* [**Agent 模式**](<https://github.blog/ai-and-ml/github-copilot/agent-mode-101-all-about-github-copilots-powerful-mode/?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025>)**：** 定义结果，Copilot 确定最佳方法，根据需要寻求您的反馈，测试自己的解决方案，并实时改进其工作。
* [**Copilot CLI**](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/use-copilot-cli>)**:** 直接从终端自动化并探索您的存储库。
* [**编码agent**](<https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent>)**：**将例程修复或脚手架卸载到Copilot。
* [**代码审查**](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)**：**在合并之前让Copilot突出显示有风险的差异或缺失的测试。
* **作用域agents：**卸载例程修复、重构、文档或测试生成。

## 如何使用GitHub Copilot**(附范例)**

以下是 Copilot 每种模式的可操作项目，以及代码片段和提示示例。

### 使用任务控制和 VS Code 中的 agent 模式更快地构建

[![](<https://github.blog/wp-content/uploads/2025/11/Screenshot-2025-11-04-at-2.22.51-PM.png>)](<https://github.blog/wp-content/uploads/2025/11/Custom-agent-mission-control-CAPS.mp4>)

一旦你安装了 Copilot 延伸, [启用 *agent 模式* 在设置中](<https://github.blog/ai-and-ml/github-copilot/agent-mode-101-all-about-github-copilots-powerful-mode/?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025>) 并从侧边栏打开任务控制。首先选择工作流程（测试、重构、文档）或运行自定义提示。

**提示图案：**

```
# Add caching to userSessionService to reduce DB hits
```

在任务控制中：“将 Redis 缓存层添加到 userSessionService，生成 hit/miss 测试，并打开草稿 PR。”

Copilot 将创建一个新文件、更新服务、添加测试并打开包含更改摘要的草稿拉取请求。

**提示：**撰写评论来解释*为什么*，而不仅仅是*什么*。

```
// Cache responses by userId for 30s to reduce DB hits >1000/min
```

简短、具体的评论使 Copilot 工作得更好。

### 使用 Copilot CLI 闯入终端

[![](<https://github.blog/wp-content/uploads/2025/11/Screenshot-2025-11-04-at-2.38.31-PM.png>)](<https://github.blog/wp-content/uploads/2025/11/GitHUb-Copilot-CLI-final-with-caps.mp4>)

[Copilot CLI 为您的终端带来同样的智能](<https://github.com/github/copilot-cli?utm_source=blog-prom-release-oct-2025&utm_campaign=copilot-generic-promotion-blog-launch-oct-2025>)。  要安装它，请在终端中使用以下命令：

```
npm install -g @github/copilot-cli
copilot /login
```

安装并通过身份验证后：

```
npm install -g @github/copilot-cli
copilot /login
```

然后运行：

```
copilot explain .
```

您将获得存储库、依赖项、测试覆盖率和潜在问题的结构化摘要。

**以下是一些常用、有用的命令：**

```
copilot explain .
copilot fix tests
copilot setup project
copilot edit src/**/*.py
```

**试试这个：**

CI 运行失败后，使用以下命令让 Copilot 找到问题，解释失败的原因，并提出修复方案以供审核。

```
copilot fix tests
```

### **使用Copilot代码审查**

Copilot 现在可以直接在 GitHub 中审查拉取请求 - 无需插件。它可以识别有风险的差异、缺失的测试覆盖率和潜在的错误。

通过您的存储库设置启用 [Copilot 代码审查 ](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025>) 即可开始。

创建拉取请求时，Copilot 可以评论：

* 缺少测试覆盖率
* 潜力 bug/edge-case
* 安全漏洞

**这是一个示例**

在您的拉取请求聊天中，尝试编写：

```
Summarize the potential risks in this diff and suggest missing test coverage.
```

Copilot 将回复内联注释，您可以接受或忽略。它不是在这里为你合并的。它可以帮助您更快地思考问题和概念。

### 使用 Copilot 编码 agent 设置异步任务

[Copilot 编码 agent](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025>) 可以处理结构化问题、编写代码并打开草稿拉取请求 - 所有这些都是异步的。

**这是一个示例问题：**

```
### Feature Request: CSV Import for User Sessions  
- File: import_user_sessions.py  
- Parse CSV with headers userId, timestamp, action  
- Validate: action in {login, logout, timeout}  
- Batch size: up to 10k rows  
- On success: append to session table  
- Include: tests, docs, API endpoint
```

将该问题分配给 Copilot。它将克隆存储库，实现该功能，并打开草稿拉取请求供您审核。

**编码agent最适合：**

* 重复重构
* 样板或脚手架
* 文档和测试生成

您总是在合并之前进行审查，但是 Copilot 加速了导致合并的一切。

### 最佳实践和护栏

* **查看所有内容。** AI 编写代码；你批准了。发货前务必检查逻辑、风格、文档。
* **提示上下文。**提示越好（原因、方式、限制），输出就越好。
* **使用小增量。** 对于 agent 模式或 CLI 编辑，一次执行一个模块。避免“一次性重写整个应用程序”。
* **让开发人员了解情况。** 特别是对于安全、架构、设计决策。
* **记录提示和决定。**维护日志：“使用提示X，结果good/bad，进行调整”。这有助于完善您的使用。
* **慢慢建立信任。**首先将Copilot用于非关键路径（测试、重构），然后扩展到核心工作流程。
* **牢记上下文限制。**尽管Copilot现在处理更多上下文，但非常大的整体存储库仍然可能暴露限制。

## 为什么这很重要

[今年有超过 36 百万开发者加入 GitHub](<https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/?utm_source=octoverse-homepage&utm_medium=blog&utm_campaign=universe25>)（每秒不止一个！），并且 80% 在第一周就使用了 Copilot。

AI-动力编码不再是实验性的。这是工作的一部分。

[像TypeScript和Python这样的类型化语言如今在GitHub中占主导地位](<https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/?utm_source=blog-prom-release-oct-2025&utm_medium=blog&utm_campaign=copilot-generic-promotion-blog-launch-oct-2025#the-top-programming-languages-of-2025-typescript-jumps-to-1-while-python-takes-2>)，它们的结构使它们成为Copilot的理想合作伙伴。强类型加上智能建议等于更快的反馈循环和更少的回归。

现在有了任务控制，一切都集中在一处。您不需要五个 AI 工具、十个浏览器选项卡或单独的审核机器人。一切都发生在您已经构建软件的地方。

## 随身携带这个

如果您一直在等待了解 Copilot 的真正功能，那么任务控制就是您的最佳时机。

借助 GitHub Copilot（在编辑器中、在终端中、在评论中以及在团队后台中），您将获得一个工具包，旨在帮助您更快、更智能地按照您的方式完成实际工作。

您决定架构。您编写测试（或者至少编写您想要编写的测试）。您合并拉取请求。 Copilot 帮助完成样板文件、脚手架和日常任务，以便您可以将注意力集中在真正重要的问题上。

本周选择你的堆栈的一部分——测试、文档、重构——并通过任务控制运行它。看看它在哪里节省了时间，然后扩大规模。

本指南就是您的地图。工具在你手中。现在轮到你来建造了。

开始使用 [GitHub Copilot >](<https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post>)

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## 撰写者

![亚伦·温斯顿](<https://avatars.githubusercontent.com/u/87450290?v=4&s=200>)

### [亚伦·温斯顿](<https://github.blog/author/aaronwinston/>)

[@aaronwinston](<https://github.com/aaronwinston>)

Aaron 帮助领导 GitHub 的内容策略，重点关注开发人员需要了解的一切，以保持领先地位。此外，尽管新发现的坏名声，他仍然喜欢 em dash。


## 原文对照

<details>
<summary>展开完整原文</summary>

# GitHub Copilot tutorial: How to build, test, review, and ship code faster (with real prompts)

If you haven’t used [GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post>) since before mission control launched, you haven’t experienced what it can do now.

Copilot used to be an autocomplete tool. Now, it’s a full AI coding assistant that can run multi-step workflows, fix failing tests, review pull requests, and ship code—directly inside VS Code or GitHub.

Back in 2021, Copilot changed how you *edited* code. [Today with Agent HQ and mission control, it’s changing how you build, review, secure and ship software](<https://github.blog/news-insights/company-news/welcome-home-agents/>).

Here’s one example:

```
// Before
"Write tests for this module" = manual setup, fixtures, and edge cases

// Now
Ask Copilot: "Generate Jest tests for userSessionService with cache-enabled branch coverage"
Full test suite + explanations in record time
```

Under the hood, Copilot runs on multiple models tuned for reasoning, speed, and code understanding. It can see more of your project, generate more accurate results, and move naturally between your editor, terminal, and GitHub.

This guide walks through every part of the new Copilot experience with working examples, best practices, and prompts you can try right now (which you should).

## What’s new with Copilot

### Larger context + cross-file reasoning (now surfaced through mission control)

Early versions of Copilot saw only what you were typing. Now, it can read across multiple files, helping it understand intent and relationships between modules.

**Ask**: In mission control: “Find every function using outdated crypto libraries and refactor them to the new API. Open a draft PR.”

Copilot can trace patterns across your codebase, make updates, and explain what changed.

### You can choose the right model for the job

You can now choose models based on your needs: one optimized for speed when prototyping, another for deeper reasoning during complex refactors.

### It goes beyond code completion

Copilot is now a suite of tools built for every step of the workflow:

* [**Mission control**](<https://github.blog/changelog/2025-10-28-a-mission-control-to-assign-steer-and-track-copilot-coding-agent-tasks/>)**:** Run multi-step tasks, generate tests, and open pull requests.
* [**Agent mode**](<https://github.blog/ai-and-ml/github-copilot/agent-mode-101-all-about-github-copilots-powerful-mode/?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025>)**:** Define the outcome, and Copilot determines the best approach seeking feedback from you as needed, testing its own solutions, and refining its work in real time.
* [**Copilot CLI**](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/use-copilot-cli>)**:** Automate and explore your repository directly from the terminal.
* [**Coding agent**](<https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent>)**:** Offload routine fixes or scaffolding to Copilot.
* [**Code review**](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)**:** Let Copilot highlight risky diffs or missing tests before you merge.
* **Scoped agents:** Offload routine fixes, refactors, docs, or test generation.

## How to use GitHub Copilot **(with examples)**

Here are actionable items for each mode of Copilot, with code snippets and prompt examples.

### Build faster with mission control and agent mode in VS Code

[![](<https://github.blog/wp-content/uploads/2025/11/Screenshot-2025-11-04-at-2.22.51-PM.png>)](<https://github.blog/wp-content/uploads/2025/11/Custom-agent-mission-control-CAPS.mp4>)

Once you’ve installed the Copilot extension, [enable *agent mode* in settings](<https://github.blog/ai-and-ml/github-copilot/agent-mode-101-all-about-github-copilots-powerful-mode/?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025>) and open mission control from the sidebar. Start by selecting a workflow (tests, refactor, documentation) or run a custom prompt.

**Prompt pattern:**

```
# Add caching to userSessionService to reduce DB hits
```

In mission control: “Add a Redis caching layer to userSessionService, generate hit/miss tests, and open a draft PR.”

Copilot will create a new file, update the service, add tests, and open a draft pull request with a summary of changes.

**Tip:** Write comments that explain *why*, not just *what*.

```
// Cache responses by userId for 30s to reduce DB hits >1000/min
```

Short, specific comments make Copilot work better.

### Break into the terminal with Copilot CLI

[![](<https://github.blog/wp-content/uploads/2025/11/Screenshot-2025-11-04-at-2.38.31-PM.png>)](<https://github.blog/wp-content/uploads/2025/11/GitHUb-Copilot-CLI-final-with-caps.mp4>)

[Copilot CLI brings the same intelligence to your terminal](<https://github.com/github/copilot-cli?utm_source=blog-prom-release-oct-2025&utm_campaign=copilot-generic-promotion-blog-launch-oct-2025>).  To install it, use the following command in your terminal:

```
npm install -g @github/copilot-cli
copilot /login
```

Once installed and authenticated:

```
npm install -g @github/copilot-cli
copilot /login
```

Then run:

```
copilot explain .
```

You’ll get a structured summary of your repository, dependencies, test coverage, and potential issues.

**Here are some common, useful commands:**

```
copilot explain .
copilot fix tests
copilot setup project
copilot edit src/**/*.py
```

**Try this:**

After a failing CI run, use the following command to have Copilot locate the issue, explain why it’s failing, and propose a fix for review.

```
copilot fix tests
```

### **Use Copilot code review**

Copilot can now review pull requests directly in GitHub—no plugins required. It identifies risky diffs, missing test coverage, and potential bugs.

Enable [Copilot code review](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025>) via your repository settings to get started.

When a pull request is created, Copilot can comment on:

* Missing test coverage
* Potential bug/edge-case
* Security vulnerabilities

**Here’s an example**

In your pull request chat, try writing:

```
Summarize the potential risks in this diff and suggest missing test coverage.
```

Copilot will reply inline with notes you can accept or ignore. It’s not here to merge for you. It’s here to help you think through issues and concepts faster.

### Setting up async tasks with Copilot coding agent

[Copilot coding agent](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025>) can take a structured issue, write code, and open a draft pull request—all asynchronously.

**Here’s an example issue:**

```
### Feature Request: CSV Import for User Sessions  
- File: import_user_sessions.py  
- Parse CSV with headers userId, timestamp, action  
- Validate: action in {login, logout, timeout}  
- Batch size: up to 10k rows  
- On success: append to session table  
- Include: tests, docs, API endpoint
```

Assign that issue to Copilot. It will clone the repo, implement the feature, and open a draft pull request for your review.

**Coding agent is best for:**

* Repetitive refactors
* Boilerplate or scaffolding
* Docs and test generation

You always review before merge, but Copilot accelerates everything leading up to it.

### Best practices and guardrails

* **Review everything.** AI writes code; you approve it. Always check logic, style, docs before you ship.
* **Prompt with context.** The better your prompt (why, how, constraints), the better the output.
* **Use small increments.** For agent mode or CLI edits, do one module at a time. Avoid “rewrite entire app in one shot.”
* **Keep developers in the loop.** Especially for security, architecture, design decisions.
* **Document prompts and decisions.** Maintain a log: “Used prompt X, result good/bad, adjustments made”. This helps refine your usage.
* **Build trust slowly.** Use Copilot for non-critical paths first (tests, refactors), then expand to core workflows.
* **Keep context limits in mind.** Although Copilot handles more context now, extremely large monolithic repos may still expose limitations.

## Why this matters

[More than 36 million developers joined GitHub this year](<https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/?utm_source=octoverse-homepage&utm_medium=blog&utm_campaign=universe25>) (that’s more than one every second!), and 80% used Copilot in their first week.

AI-powered coding is no longer experimental. It’s part of the job.

[Typed languages like TypeScript and Python dominate GitHub today](<https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/?utm_source=blog-prom-release-oct-2025&utm_medium=blog&utm_campaign=copilot-generic-promotion-blog-launch-oct-2025#the-top-programming-languages-of-2025-typescript-jumps-to-1-while-python-takes-2>), and their structure makes them ideal partners for Copilot. Strong types plus smart suggestions equals faster feedback loops and fewer regressions.

And now with mission control, everything’s in one place. You don’t need five AI tools, ten browser tabs, or a separate review bot. Everything happens where you already build software.

## Take this with you

If you’ve been waiting to see what Copilot can really do, mission control is the moment.

With GitHub Copilot—in the editor, in your terminal, in your reviews, and in the background of your team—you’re getting a toolkit designed to help you do real work faster, smarter, and on your terms.

You decide the architecture. You write the tests (or at least the ones you want to write). You merge the pull requests. Copilot helps with boilerplate, scaffolding, and routine tasks so you can keep your focus on the problem that really matters.

Pick one part of your stack this week—tests, docs, refactor—and run it through mission control. See where it saves time, then scale up.

This guide is your map. The tools are in your hands. Now it’s your turn to build.

Start using [GitHub Copilot >](<https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post>)

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## Written by

![Aaron Winston](<https://avatars.githubusercontent.com/u/87450290?v=4&s=200>)

### [Aaron Winston](<https://github.blog/author/aaronwinston/>)

[@aaronwinston](<https://github.com/aaronwinston>)

Aaron helps lead content strategy at GitHub with a focus on everything developers need to know to stay ahead of what's next. Also, he still likes the em dash despite its newfound bad rap.


</details>
