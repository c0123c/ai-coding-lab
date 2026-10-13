# Copilot 代码审查的规模与设计

介绍审查系统对准确性、有效信号和速度的设计，以及 Agent 架构和结果展示方式；属于产品工程经验文章。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-03-05](<https://github.blog/ai-and-ml/github-copilot/60-million-copilot-code-reviews-and-counting/>)；更新：[2026-03-05](<https://github.blog/ai-and-ml/github-copilot/60-million-copilot-code-reviews-and-counting/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/60-million-copilot-code-reviews-and-counting/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/Copilot 代码审查的规模与设计.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 60 百万 Copilot 代码审查和计数

自去年 4 月我们首次推出 Copilot 代码审查 (CCR) 以来，使用量不断增长 10X，目前占 GitHub 代码审查的五分之一以上。

在幕后，我们一直在进行持续的实验来提高评论质量。我们还转向了代理架构，用于检索存储库上下文和更改原因。在整个过程中的每一步，我们都听取了您的反馈：您的调查答案，甚至您对评论的简单的赞成和反对反应，都帮助我们识别关键问题并迭代我们的 UX 以提供全面的评论体验。

> Copilot 代码审查处理拉取请求审查和摘要，使团队能够专注于更复杂的任务。
>
> Suvarna Rane，通用汽车软件开发经理

## 重新定义“良好”的代码审查

随着 Copilot 代码审查随着时间的推移而发展，我们对“良好代码审查”的定义也在不断变化。当我们开始在 2024 中构建它时，我们的目标是简单彻底。从那时起，我们了解到开发人员真正重视的是高信号反馈，可以帮助他们快速推进拉取请求。如今，Copilot 代码审查利用最佳模型、内存和代理工具调用来进行全面审查。为此，我们使用连续评估循环来调整 agent 的判断，重点关注塑造体验的三个品质：准确性、信号和速度。

### 准确度

我们的目标是 Copilot 代码审查提供合理的判断，优先考虑相应的逻辑和可维护性问题。我们通过两种方式评估性能：通过针对已知代码问题的内部测试，以及通过来自真实拉取请求的生产信号。在生产中，我们跟踪两个关键指标：

* **开发者反馈**：对评论的赞成和反对反应有助于我们了解建议是否有帮助。
* **生产信号**：我们在合并之前衡量标记的问题是否得到解决。

这些信号共同有助于确保 Copilot 代码审查能够发现重要的问题，并且更快的合并来自于自信的修复，而不是更少的审查。

![Copilot 代码审查注释，识别 React useCallback 挂钩中缺少的依赖项，并建议更改代码以将 handleKeyboardDrag 添加到依赖项数组。](<https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-05-at-10.29.41-AM.png?resize=1024%2C435>)

### 信号

在代码审查中，更多的注释并不一定意味着更好的审查。我们的目标不是最大化评论量，而是揭露真正重要的问题。

高信号注释可以帮助开发人员了解问题和解决方案：

![Copilot 代码审查注释警告，当 API 返回 HTTP 429 且没有 Retry-After 标头时，重试循环可能会无限期运行，并建议添加重试限制和退避。](<https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-05-at-11.07.14-AM.png?resize=1024%2C289>)

沉默比喧闹更好。在 71% 评论中，Copilot 代码审查提供了可操作的反馈。在剩下的29%中，agent什么也没说。

随着我们识别高信号发现的能力的提高，我们也能够更加自信地发表评论，现在每次评论平均有 5.1 条评论，而不会增加评论流失或降低我们的质量阈值。

### 速度

在代码审查中，速度很重要，但信号更重要。 Copilot 代码审查旨在在拉取请求打开后不久提供可靠的首次通过。话虽如此，有意义的评论仍然需要分析。随着推理能力的提高，揭示更深层次问题所需的计算能力也在提高。

我们将此视为一种有意的权衡。在最近的一项更改中，采用更先进的推理模型将积极反馈率提高了 6%，尽管审核延迟时间增加了 16%。

对我们来说，这是正确的交换。稍微慢一点的审查可以揭示真正的问题，这比增加噪音的即时反馈更有价值。我们继续尽可能减少延迟，但绝不以牺牲开发人员可以信任的高信号结果为代价。

## 关于代理架构

鉴于我们对“好”的新定义，我们重新开发了代码审查系统。今天的代理设计可以智能地检索上下文并探索存储库以理解逻辑、架构和特定的不变量。

仅这一转变就推动了正反馈的初始 8.1% 增加。

原因如下：

* **它在阅读时捕获问题，而不仅仅是在最后**：以前，agents要等到审查结束才能最终确定结果，这常常导致“忘记”早期的发现。
* **它可以跨评论维护内存**：现在，每个拉取请求不需要是一个孤立的事件。如果它在代码库的一部分中标记了一种模式，那么它可以在将来的审查中重用该上下文。
* **它通过明确的计划保持长拉取请求的可审查性**：它可以提前制定审查策略，显着提高其在长而复杂的拉取请求（上下文很容易丢失）上的性能。
* **它读取链接的问题和拉取请求**：额外的上下文有助于它标记细微的差距。这包括代码单独看起来合理但不符合项目要求的情况。

## 让评论更容易浏览

通过迭代 agent 如何与拉取请求交互，我们减少了噪音并使反馈更具可操作性。这对你来说意味着什么。

* **通过多行注释快速了解反馈（和修复）**：我们不再将注释固定到单行。通过将反馈附加到逻辑代码范围，Copilot 可以更轻松地查看它所指的内容并应用建议的更改。

![Copilot 对 GitHub Actions 工作流的代码审查注释，识别出缺少的 use_caches 输入参数，并建议更改代码以将布尔输入添加到工作流配置中。](<https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-02-01-at-3.30.41-PM.png?resize=1024%2C1010>)

* **保持拉取请求时间线的可读性**：agent不会对同一模式错误进行多个单独的评论，这可能会让人不知所措，而是将它们聚集成一个单一的、有凝聚力的单元，以减少认知负荷。
* **通过批量自动修复一次性修复整类问题**：批量应用建议的修复，一次性解决整类逻辑错误或风格问题，而不是通过十几个单独的建议进行上下文切换。

## 随身携带这个

随着 AI 不断加速软件开发，帮助团队大规模审查和信任代码比以往任何时候都更加重要。 Copilot 代码审查通过直接在拉取请求中呈现高信号反馈来帮助团队跟上步伐，使开发人员能够更早地发现问题并更有信心地进行合并。

现在，超过 12,000 个组织会针对每个拉取请求自动运行 Copilot 代码审查。在 WEX，这种向默认 AI 辅助审核的转变有助于扩大 Copilot 在整个工程组织中的采用：

如今，三分之二的开发人员正在使用 Copilot，其中包括该组织最活跃的贡献者。此后，WEX 通过将 Copilot 代码审查设为每个存储库的默认值，扩大了采用范围。开发人员还大量利用 agent 模式和编码 agent 来驱动自主性，帮助 WEX 看到部署的巨大提升，并交付了约 30% 更多代码。 — [WEX 客户案例](<https://github.com/customer-stories/wex>)

展望未来，我们将专注于更深入的个性化和高保真交互性，完善 agent 以了解您团队不成文的偏好，同时启用双向对话，让您在合并之前完善修复并探索替代方案。

随着 Copilot 功能不断发展，从编码和规划到审查和自动化，目标很简单：帮助开发人员更快地前进，同时保持优秀软件所需的信任和质量。

## 今天开始

Copilot 代码审查是 Copilot Pro、Copilot Pro+、Copilot Business 和 Copilot Enterprise 提供的一项高级功能。请参阅以下资源：

* [选择套餐](<https://docs.github.com/en/copilot/get-started/plans#ready-to-choose-a-plan>)
* [在没有 Copilot 许可证的情况下启用 Copilot 代码审查](<https://docs.github.com/en/copilot/concepts/agents/code-review#copilot-code-review-without-a-copilot-license>)
* [观看演示视频](<https://youtu.be/HDEGFNAUkX8?si=s9DauqsFZCdtpCtI>)

已启用 Copilot 代码审查？请参阅这些文档，以 [ 对存储库或组织内的每个拉取请求 ](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/configure-automatic-review>) 设置自动 Copilot 代码审查。

有想法或反馈吗？请在我们的 [ 社区讨论帖子 ](<https://github.com/orgs/community/discussions/186303>) 中告知我们。

---

## 标签：

* [代码质量](<https://github.blog/tag/code-quality/>)
* [GitHub 行动](<https://github.blog/tag/github-actions/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot 代码审查](<https://github.blog/tag/github-copilot-code-review/>)

## 撰写者

![莉亚·戈普](<https://avatars.githubusercontent.com/u/189655821?v=4&s=200>)

### [里亚戈普](<https://github.blog/author/ria-gopu/>)

[@ria-gopu](<https://github.com/ria-gopu>)

GitHub 产品经理

![大卫·阿皮里安](<https://avatars.githubusercontent.com/u/3904462?v=4&s=200>)

### [大卫·阿皮里安](<https://github.blog/author/dapirian/>)

[@dapirian](<https://github.com/dapirian>)

软件工程高级总监


## 原文对照

<details>
<summary>展开完整原文</summary>

# 60 million Copilot code reviews and counting

Since our initial launch of Copilot code review (CCR) last April, usage has grown 10X, now accounting for more than one in five code reviews on GitHub.

Behind the scenes, we’ve been running continuous experiments to enhance comment quality. We also moved to an agentic architecture that retrieves repository context and reasons across changes. At every step of the way, we’ve listened to your feedback: your survey answers and even your simple thumbs-up and thumbs-down reactions on comments have helped us identify key issues and iterate on our UX to provide a comprehensive review experience.

> Copilot code review handles pull request reviews and summaries, allowing teams to focus on more complex tasks.
>
> Suvarna Rane, Software Development Manager, General Motors

## Redefining a “good” code review

As Copilot code review evolved over time, so has our definition of a “good code review.” When we started building it in 2024, our goal was simple thoroughness. Since then, we’ve learned that what developers actually value is high-signal feedback that helps them move a pull request forward quickly. Today, Copilot code review leverages the best models, memory, and agentic tool-calling to conduct comprehensive reviews. To get here, we’ve used a continuous evaluation loop to tune the agent’s judgment, focusing on three qualities that shape that experience: accuracy, signal, and speed.

### Accuracy

Our aim has been for Copilot code review to deliver sound judgment, prioritizing consequential logic and maintainability issues. We evaluate performance in two ways: through internal testing against known code issues, and through production signals from real pull requests. In production, we track two key indicators:

* **Developer feedback**: Thumbs-up and thumbs-down reactions on comments help us understand whether suggestions are helpful.
* **Production signals**: We measure whether flagged issues are resolved before merging.

Together, these signals help ensure that Copilot code review surfaces issues that matter, and that faster merges come from confident fixes, not less scrutiny.

![Copilot code review comment identifying a missing dependency in a React useCallback hook and suggesting a code change to add handleKeyboardDrag to the dependency array.](<https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-05-at-10.29.41-AM.png?resize=1024%2C435>)

### Signal

In code review, more comments don’t necessarily mean a better review. Our goal isn’t to maximize comment volume, but to surface issues that actually matter.

A high-signal comment helps a developer understand both the problem and the fix:

![Copilot code review comment warning that a retry loop could run indefinitely when an API returns HTTP 429 without a Retry-After header and suggesting adding a retry limit and backoff.](<https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-05-at-11.07.14-AM.png?resize=1024%2C289>)

Silence is better than noise. In 71% of the reviews, Copilot code review surfaces actionable feedback. In the remaining 29%, the agent says nothing at all.

As our ability to identify high-signal findings improves, we’re also able to comment more confidently, now averaging about 5.1 comments per review without increasing review churn or lowering our quality threshold.

### Speed

In code review, speed matters, but signal matters more. Copilot code review is designed to provide a reliable first pass shortly after a pull request is opened. That being said, meaningful reviews still require analysis. As reasoning capabilities improve, so does the computation required to surface deeper issues.

We treat this as a deliberate trade-off. In one recent change, adopting a more advanced reasoning model improved positive feedback rates by 6%, even though review latency increased by 16%.

For us, that’s the right exchange. A slightly slower review that surfaces real issues is far more valuable than instant feedback that adds noise. We continue to reduce latency wherever possible, but never at the expense of high-signal findings developers can trust.

## About the agentic architecture

Given our new definition of “good,” we redeveloped our code review system. Today’s agentic design can retrieve context intelligently and explore the repository to understand logic, architecture, and specific invariants.

This shift alone has driven an initial 8.1% increase in positive feedback.

Here’s why:

* **It catches issues as it reads, not just at the end**: Previously, agents waited until the end of a review to finalize results, which often led to “forgetting” early discoveries.
* **It can maintain memory across reviews**: Now, every pull request doesn’t need to be an isolated event. If it flags a pattern in one part of the codebase, it can reuse that context in future reviews.
* **It keeps long pull requests reviewable with an explicit plan**: It can map out its review strategy ahead of time, significantly improving its performance on long, complex pull requests, where context is easily lost.
* **It reads linked issues and pull requests**: That extra context helps it flag subtle gaps. This includes cases where the code looks reasonable in isolation but doesn’t match the project’s requirements.

## Making reviews easier to navigate

By iterating on how the agent interacts with pull requests, we’ve reduced noise and made feedback more actionable. Here’s what that means for you.

* **Quickly understand feedback (and the fix) with multi-line comments**: We moved away from pinning comments to single lines. By attaching feedback to logical code ranges, Copilot makes it easier to see what it’s referring to and apply the suggested change.

![Copilot code review comment on a GitHub Actions workflow identifying a missing use_caches input parameter and suggesting a code change to add the boolean input to the workflow configuration.](<https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-02-01-at-3.30.41-PM.png?resize=1024%2C1010>)

* **Keep your pull request timeline readable**: Instead of multiple separate comments for the same pattern error, which can be overwhelming, the agent clusters them into a single, cohesive unit to reduce cognitive load.
* **Fix whole classes of issues at once with batch autofixes**: Apply suggested fixes in batches, resolving an entire class of logic bugs or style issues at once, rather than context-switching through a dozen individual suggestions.

## Take this with you

As AI continues to accelerate software development, it’s more important than ever to help teams review and trust code at scale. Copilot code review helps teams keep pace by surfacing high-signal feedback directly in pull requests, enabling developers to catch issues earlier and merge with greater confidence.

More than 12,000 organizations now run Copilot code review automatically on every pull request. At WEX, this shift toward default AI –assisted reviews has helped scale Copilot adoption across the engineering organization:

Today, two-thirds of developers are using Copilot — including the organization’s most active contributors. WEX has since expanded adoption by making Copilot code review a default across every repository. Developers are also heavily utilizing agent mode and the coding agent to drive autonomy, helping WEX see a huge lift in deployments, with ~30% more code shipped. — [WEX customer story](<https://github.com/customer-stories/wex>)

Going forward, we’re focused on deeper personalization and high-fidelity interactivity, refining the agent to learn your team’s unwritten preferences while enabling two-way conversations that let you refine fixes and explore alternatives before merging.

As Copilot capabilities continue to evolve, from coding and planning to review and automation, the goal is simple: help developers move faster while maintaining the trust and quality that great software demands.

## Get started today

Copilot code review is a premium feature available with Copilot Pro, Copilot Pro+, Copilot Business, and Copilot Enterprise. See the following resources to:

* [Choose a plan](<https://docs.github.com/en/copilot/get-started/plans#ready-to-choose-a-plan>)
* [Enable Copilot code review without a Copilot license](<https://docs.github.com/en/copilot/concepts/agents/code-review#copilot-code-review-without-a-copilot-license>)
* [Watch a demo video](<https://youtu.be/HDEGFNAUkX8?si=s9DauqsFZCdtpCtI>)

Already enabled Copilot code review? See these docs to [set up automatic Copilot code reviews on every pull request](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/configure-automatic-review>) within your repository or organization.

Have thoughts or feedback? Please let us know in our [community discussion post](<https://github.com/orgs/community/discussions/186303>).

---

## Tags:

* [code quality](<https://github.blog/tag/code-quality/>)
* [GitHub Actions](<https://github.blog/tag/github-actions/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot code review](<https://github.blog/tag/github-copilot-code-review/>)

## Written by

![Ria Gopu](<https://avatars.githubusercontent.com/u/189655821?v=4&s=200>)

### [Ria Gopu](<https://github.blog/author/ria-gopu/>)

[@ria-gopu](<https://github.com/ria-gopu>)

GitHub Product Manager

![David Apirian](<https://avatars.githubusercontent.com/u/3904462?v=4&s=200>)

### [David Apirian](<https://github.blog/author/dapirian/>)

[@dapirian](<https://github.com/dapirian>)

Senior Director, Software Engineering


</details>
