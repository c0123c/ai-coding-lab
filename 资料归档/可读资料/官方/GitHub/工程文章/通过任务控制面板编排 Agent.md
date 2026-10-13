# 通过任务控制面板编排 Agent

介绍并行与顺序工作的选择、清晰任务提示、角色配置、进度观察与偏差纠正，以及怎样高效审查多个任务的结果。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-12-01](<https://github.blog/ai-and-ml/github-copilot/how-to-orchestrate-agents-using-mission-control/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/how-to-orchestrate-agents-using-mission-control/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/通过任务控制面板编排 Agent.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 如何使用任务控制来编排agents

我们最近发布了 [Agent HQ 的任务控制 ](<https://github.blog/changelog/2025-10-28-a-mission-control-to-assign-steer-and-track-copilot-coding-agent-tasks/>)，这是一个用于管理 [GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-mission-control&utm_medium=blog&utm_campaign=universe25post>) 编码 agent 任务的统一界面。

现在，您现在可以跨存储库将任务分配给 Copilot，选择自定义 agent，观察实时会话日志，引导运行中（暂停、优化或重新启动），并直接跳转到生成的拉取请求 - 所有这些都在一个地方。任务控制不是在页面之间跳转来查看状态、理由和更改，而是集中分配、监督和审查。

拥有工具是一回事。知道如何有效地使用它是另一回事。本指南向您展示如何协调多个 agents、何时进行干预以及如何高效地审查他们的工作。擅长编排 agents 意味着在完成一项任务的同一时间范围内解除并行工作的阻碍，在日志显示偏差、测试失败或范围扩大时介入。

## 心智模式转变

### 从顺序到并行

如果您已经习惯一次使用一个 agent，您就会知道它本质上是顺序的。您提交提示，等待响应，查看提示，进行调整，然后转到下一个任务。

任务控制改变了这一点。您可以在几分钟内启动多个任务——跨一个或多个存储库。以前，您需要导航到不同的存储库，在每个存储库中打开问题，然后分别分配 Copilot。现在，您可以在一处输入提示，并且 Copilot 编码 agent 适用于所有提示。

话虽这么说，但需要记住一个权衡：您的 agents 可能会花几分钟到一个小时来完成草稿，而不是每项任务需要 30 秒到几分钟才能完成。但你不再只是等待。你正在编排。

### 何时保持顺序

并非所有事物都属于并行。在以下情况下使用顺序工作流程：

* 任务具有依赖性
* 你正在探索陌生的领域
* 复杂的问题需要在步骤之间验证假设

从同一存储库分配多个任务时，请考虑重叠。 Agents 并行工作如果接触相同的文件可能会产生合并冲突。考虑划分工作。

通常并行运行良好的任务：

* 研究工作（查找功能标志、配置选项）
* 分析（日志分析、性能分析）
* 文档生成
* 安全审查
* 在不同的模块或组件中工作

## 入门提示

这种转变很简单：您从等待单次运行转变为监督多个并行进展，介入失败的测试、范围漂移或纠正不明确的意图，从而节省时间。

### 写出带有上下文的清晰提示

特异性很重要。准确描述任务。良好的背景对于取得良好的结果仍然至关重要。

有用的上下文包括：

* 显示问题的屏幕截图
* 说明您想要的模式的代码片段
* 相关文档或示例的链接

**弱提示**：“修复认证bug。”

**强提示**：“用户在30分钟的活动后报告‘无效令牌’错误。JWT令牌在auth.config.js中配置为1小时过期。调查令牌提前过期的原因并修复验证逻辑。在 api-gateway 存储库中创建拉取请求。”

### 使用自定义 agents 以保持一致性

任务控制允许您选择使用所选存储库中的 agents.md 文件的自定义 agents。这些文件为您的 agent 提供了角色和预先编写的上下文，消除了不断提供相同示例或说明的负担。

如果您管理团队经常使用 agents 的存储库，请考虑创建适合您的常用工作流程的 agents.md 文件。这确保了任务之间的一致性，并减少了每次制作详细提示的认知负担。

编写完提示并选择自定义 agent（如果适用）后，即可开始任务。您的 agent 立即开始工作。

## 主动编排的技巧

你现在是agents的指挥。每个任务可能需要一分钟或一个小时，具体取决于复杂性。您有两种选择：观察您的 agents 工作，以便在需要时进行干预，或者离开并在完成后回来。

### 读取信号

以下是一些常见指标，表明您的 agent 未走上正轨，需要额外指导：

* **测试、集成或获取失败**：agent无法获取依赖项、身份验证失败或单元测试反复中断。
* **创建意外文件**：范围之外的文件出现在差异中，或者agent修改共享配置。
* **范围超出了您的要求**：agent开始重构相邻代码或“改进”您没有要求的东西。
* **误解了您的意图**：会话日志显示agent对您的提示的解释与您的意思不同。
* **循环行为**：agent多次尝试相同的失败方法而不进行调整。

当您发现问题时，评估其严重性。失败的测试很重要吗？该集成点对于这项任务重要吗？会话日志通常会在采取行动之前显示意图，让您有机会在监控时进行干预。

### 转向的艺术

当您需要重定向 agent 时，请具体说明。解释为什么要重定向以及您希望重定向如何进行。

**转向不良**：“这看起来不对。”

**良好的指导**：“不要修改database.js——该文件是跨服务共享的。相反，在api/config/db-pool.js.中添加连接池配置，这可以将更改隔离到API层。”

时机很重要。五分钟内发现问题，您可能会节省一个小时的无效工作。不要等到 agent 完成才提供反馈。

您还可以停止任务中的 agent 并为其提供精确的指令。以更好的方向重新启动很简单，而且通常比让未对准的 agent 继续运行更快。

### 为什么会话日志很重要

会话日志显示推理，而不仅仅是操作。它们会在误解成为拉取请求之前揭示它们，并改进您未来的提示和编排实践。当 Copilot 说“我要重构整个身份验证系统”时，这就是你要引导的提示。

## 审查阶段的提示

当您的 agents 完成后，您将有拉取请求需要审核。以下是如何有效地做到这一点。确保您审查：

1. **会话日志**：了解agent做了什么以及为什么。在推理错误成为合并代码之前查找它们。 agent 是否误解了您的意图？它是否错误地假设了什么？
2. **文件已更改**：查看实际的代码更改。重点关注：
   * 您不希望看到修改的文件
   * 涉及共享、有风险或关键代码路径的更改
   * 与您团队的 standards/practices 不匹配的模式
   * 缺少边缘情况处理
3. **检查**：验证测试是否通过（您的单元测试、Playwright、CI/CD等）。当检查失败时，不要只是重新启动agent。调查原因。失败的测试可能会揭示 agent 误解的需求，而不仅仅是编写有错误的代码。

该模式为您提供了完整的情况：意图、实现和验证。

### 请Copilot审查其自己的工作

agent 完成任务后，询问它：

* “我错过了哪些边缘情况？”
* “哪些测试覆盖范围不完整？”
* “我应该如何修复这个失败的测试？”

Copilot 通常可以找出自己工作中的差距，从而节省您的时间并改善最终结果。像对待愿意解释其推理的初级开发人员一样对待它。

### 批量相似评论

使用 agents 生成代码非常简单。审查该代码（确保它符合您的标准，执行您想要的操作，并且可以由您的团队维护）仍然需要人为判断。

通过将类似的工作分组在一起来改进您的审核流程。在一次会话中查看所有 API 更改。在另一个文档中查看所有文档更改。你的大脑上下文切换更少，你会更容易发现模式和不一致之处。

## 哪些方面发生了更好的变化

任务控制中心让您从照看单次 agent 运行转变为编排小型舰队。您定义明确的、范围明确的任务。您提供了足够的背景信息。您启动了多个 agents。速度增益并不是每个任务完成得更快；而是每个任务完成得更快。而是您在同一时间范围内解锁更多工作。

使这一切成为可能的是纪律：具体的提示，而不是模糊的要求。 agents.md 中的自定义 agents 带有您的图案，这样您就不会重复自己。当会话日志显示漂移时进行早期转向。将日志视为您挖掘的推理工件，以编写更清晰的下一个提示。并批量评论，让你的大脑停留在一种心理模型足够长的时间，以发现微妙的不一致。带领你自己的agents团队创造伟大的事情！

准备好开始了吗？ [访问任务控制中心](<https://github.com/copilot/agents?utm_source=blog-mission-control&utm_medium=blog&utm_campaign=universe25post>)或[了解有关您的组织的GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-mission-control&utm_medium=blog&utm_campaign=universe25post>)的更多信息。

---

## 标签：

* [AI agents](<https://github.blog/tag/ai-agents/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot编码agent](<https://github.blog/tag/github-copilot-coding-agent/>)
* [任务控制](<https://github.blog/tag/mission-control/>)

## 撰写者

![马特·奈伊](<https://avatars.githubusercontent.com/u/5862416?v=4&s=200>)

### [马特·奈伊](<https://github.blog/author/mattnigh/>)

[@mattnigh](<https://github.com/mattnigh>)

项目经理总监，我领导 GitHub 的 AI forEveryone 项目。


## 原文对照

<details>
<summary>展开完整原文</summary>

# How to orchestrate agents using mission control

We recently shipped [Agent HQ’s mission control](<https://github.blog/changelog/2025-10-28-a-mission-control-to-assign-steer-and-track-copilot-coding-agent-tasks/>), a unified interface for managing [GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-mission-control&utm_medium=blog&utm_campaign=universe25post>) coding agent tasks.

Now, you can now assign tasks to Copilot across repos, pick a custom agent, watch real‑time session logs, steer mid-run (pause, refine, or restart), and jump straight into the resulting pull requests—all in one place. Instead of bouncing between pages to see status, rationale, and changes, mission control centralizes assignment, oversight, and review.

Having the tool is one thing. Knowing how to use it effectively is another. This guide shows you how to orchestrate multiple agents, when to intervene, and how to review their work efficiently. Being great at orchestrating agents means unblocking parallel work in the same timeframe you’d spend on one task, stepping in when logs show drift, tests fail, or scope creeps.

## The mental model shift

### From sequential to parallel

If you’re already used to working with an agent one at a time, you know it’s inherently sequential. You submit a prompt, wait for a response, review it, make adjustments, and move to the next task.

Mission control changes this. You can kick off multiple tasks in minutes—across one repo or many. Previously, you’d navigate to different repos, open issues in each one, and assign Copilot separately. Now you can enter prompts in one place, and Copilot coding agent goes to work across all of them.

That being said, there is a trade-off to keep in mind: Instead of each task taking30 seconds to a few minutes to complete, your agents might spend a few minutes to an hour on a draft. But you’re no longer just waiting. You’re orchestrating.

### When to stay sequential

Not everything belongs in parallel. Use sequential workflows when:

* Tasks have dependencies
* You’re exploring unfamiliar territory
* Complex problems require validating assumptions between steps

When assigning multiple tasks from the same repo, consider overlap. Agents working in parallel can create merge conflicts if they touch the same files. Be thoughtful about partitioning work.

Tasks that typically run well in parallel:

* Research work (finding feature flags, configuration options)
* Analysis (log analysis, performance profiling)
* Documentation generation
* Security reviews
* Work in different modules or components

## Tips for getting started

The shift is simple: you move from waiting on a single run to overseeing multiple progressing in parallel, stepping in for failed tests, scope drift, or correcting unclear intent where guidance will save time.

### Write clear prompts with context

Specificity matters. Describe the task precisely. Good context remains critical for good results.

Helpful context includes:

* Screenshots showing the problem
* Code snippets illustrating the pattern you want
* Links to relevant documentation or examples

**Weak prompt**: “Fix the authentication bug.”

**Strong prompt**: “Users report ‘Invalid token’ errors after 30 minutes of activity. JWT tokens are configured with 1-hour expiration in auth.config.js. Investigate why tokens expire early and fix the validation logic. Create the pull request in the api-gateway repo.”

### Use custom agents for consistency

Mission control lets you select custom agents that use agents.md files from your selected repo. These files give your agent a persona and pre-written context, removing the burden of constantly providing the same examples or instructions.

If you manage repos where your team regularly uses agents, consider creating agents.md files tailored to your common workflows. This ensures consistency across tasks and reduces the cognitive load of crafting detailed prompts each time.

Once you’ve written your prompt and selected your custom agent (if applicable), kick off the task. Your agent gets to work immediately.

## Tips for active orchestration

You’re now a conductor of agents. Each task might take a minute or an hour, depending on complexity. You have two choices: watch your agents work so you can intervene if needed, or step away and come back when they’re done.

### Reading the signals

Below are some common indicators that your agent is not on the right track and needs additional guidance:

* **Failing tests, integrations, or fetches**: The agent can’t fetch dependencies, authentication fails, or unit tests break repeatedly.
* **Unexpected files being created**: Files outside the scope appear in the diff, or the agent modifies shared configuration.
* **Scope creep beyond what you requested**: The agent starts refactoring adjacent code or “improving” things you didn’t ask for.
* **Misunderstanding your intent**: The session log reveals the agent interpreted your prompt differently than you meant.
* **Circular behavior**: The agent tries the same failing approach multiple times without adjusting.

When you spot issues, evaluate their severity. Is that failing test critical? Does that integration point matter for this task? The session log typically shows intent before action, giving you a chance to intervene if you’re monitoring.

### The art of steering

When you need to redirect an agent, be specific. Explain why you’re redirecting and how you want it to proceed.

**Bad steering**: “This doesn’t look right.”

**Good steering**: “Don’t modify database.js—that file is shared across services. Instead, add the connection pool configuration in api/config/db-pool.js. This keeps the change isolated to the API layer.”

Timing matters. Catch a problem five minutes in, and you might save an hour of ineffective work. Don’t wait until the agent finishes to provide feedback.

You can also stop an agent mid-task and give it refined instructions. Restarting with better direction is simple and often faster than letting a misaligned agent continue.

### Why session logs matter

Session logs show reasoning, not just actions. They reveal misunderstandings before they become pull requests, and they improve your future prompts and orchestration practices. When Copilot says “I’m going to refactor the entire authentication system,” that’s your cue to steer.

## Tips for the review phase

When your agents finish, you’ll have pull requests to review. Here’s how to do it efficiently. Ensure you review:

1. **Session logs**: Understand what the agent did and why. Look for reasoning errors before they become merged code. Did the agent misinterpret your intent? Did it assume something incorrectly?
2. **Files changed**: Review the actual code changes. Focus on:
   * Files you didn’t expect to see modified
   * Changes that touch shared, risky, or critical code paths
   * Patterns that don’t match your team’s standards/practices
   * Missing edge case handling
3. **Checks**: Verify that tests pass (your unit tests, Playwright, CI/CD, etc.). When checks fail, don’t just restart the agent. Investigate why. A failing test might reveal the agent misunderstood requirements, not just wrote buggy code.

This pattern gives you the full picture: intent, implementation, and validation.

### Ask Copilot to review its own work

After an agent completes a task, ask it:

* “What edge cases am I missing?”
* “What test coverage is incomplete?”
* “How should I fix this failing test?”

Copilot can often identify gaps in its own work, saving you time and improving the final result. Treat it like a junior developer who’s willing to explain their reasoning.

### Batch similar reviews

Generating code with agents is straightforward. Reviewing that code—ensuring it meets your standards, does what you want, and that it can be maintained by your team—still requires human judgment.

Improve your review process by grouping similar work together. Review all API changes in one session. Review all documentation changes in another. Your brain context-switches less, and you’ll spot patterns and inconsistencies more easily.

## What’s changed for the better

Mission control moves you from babysitting single agent runs to orchestrating a small fleet. You define clear, scoped tasks. You supply just enough context. You launch several agents. The speed gain is not that each task finishes faster; it’s that you unblock more work in the same timeframe.

What makes this possible is discipline: specific prompts, not vague requests. Custom agents in agents.md that carry your patterns so you don’t repeat yourself. Early steering when session logs show drift. Treating logs as reasoning artifacts you mine to write a sharper next prompt. And batching reviews so your brain stays in one mental model long enough to spot subtle inconsistencies. Lead your own team of agents to create something great!

Ready to start? [Visit mission control](<https://github.com/copilot/agents?utm_source=blog-mission-control&utm_medium=blog&utm_campaign=universe25post>) or [learn more about GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-mission-control&utm_medium=blog&utm_campaign=universe25post>) for your organization.

---

## Tags:

* [AI agents](<https://github.blog/tag/ai-agents/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot coding agent](<https://github.blog/tag/github-copilot-coding-agent/>)
* [mission control](<https://github.blog/tag/mission-control/>)

## Written by

![Matt Nigh](<https://avatars.githubusercontent.com/u/5862416?v=4&s=200>)

### [Matt Nigh](<https://github.blog/author/mattnigh/>)

[@mattnigh](<https://github.com/mattnigh>)

Program Manager Director, I lead the AI for Everyone program at GitHub.


</details>
