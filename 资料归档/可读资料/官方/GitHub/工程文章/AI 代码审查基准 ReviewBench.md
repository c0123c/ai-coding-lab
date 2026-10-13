# AI 代码审查基准 ReviewBench

介绍由真实拉取请求、多个证据来源和结构化发现组成的审查基准，说明问题分类、严重度及标注方法。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-10-05](<https://github.blog/ai-and-ml/github-copilot/reviewbench-an-open-benchmark-for-ai-code-review/>)；更新：[2026-10-05](<https://github.blog/ai-and-ml/github-copilot/reviewbench-an-open-benchmark-for-ai-code-review/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/reviewbench-an-open-benchmark-for-ai-code-review/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/AI 代码审查基准 ReviewBench.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# ReviewBench：AI代码审查的开放基准

代理代码审查正在成为开发过程中的一个重要组成部分。它可以帮助您检查拉取请求、发现问题并在代码发布之前决定哪些内容值得关注。

但是现有的 AI 审稿人的质量可能很难衡量，你需要先了解审稿人的优势，然后才能知道它是否会对你有帮助。一些审阅者提出了更多问题，一些审阅者产生了更少的噪音，一些审阅者更善于发现关键问题，而另一些审阅者也提出了较小的改进。您可能需要代码审查才能在工作流程中执行不同的操作。

这使得了解评审者实际上如何进行比较变得很重要：不同的系统捕捉到了什么，错过了什么，以及他们做出的权衡。一个好的代码审查基准应该反映真实拉取请求的多样性，捕获广泛的审查结果，并支持按严重性、类别和精确召回偏好进行有意义的细分。对于构建代码审查 agents 的团队来说，基准测试还应该提供离线信号，可靠地跟踪更改是否可能改善生产体验。现有的基准测试经常在标签质量、覆盖范围以及它们代表现实世界代码审查的程度之间进行权衡，从而为将这些部分整合在一起的严格且可重复的评估方法留下了空白。

我们构建了 [**ReviewBench**](<https://review-bench.ai/>)**，一个新的代码审查离线基准 **，以解决这一差距，并且今天就可供您使用。它遵循拉取请求的语言、存储库大小和大小分布，以 GitHub 上超过 100 百万个真实拉取请求为模型。它使用多源黄金集和一致的评估标准，并经过高级工程师的独立验证。同样重要的是，在 ReviewBench 的帮助下，我们对 Copilot 代码审查 (CCR) 的离线评估在预测生产实验的方向方面变得更加有效，使我们更有信心测量到的改进反映了对用户有意义的收益。

在这篇文章中，我们将介绍 ReviewBench 的构建方式、它如何建立可靠的地面事实和评分，以及如何加入您自己的代码审查系统并提交结果。

## ReviewBench一览

1

### 我们建造了什么

用于 AI 代码审查的现实、全面的基准 agents

103.9M

### GitHub 拉取请求

按语言、存储库大小和变化形式分析分布。

### 代表性基准语料库

219 跨 19 语言的公开拉取请求，与 GitHub 范围的分发保持一致，同时保留实质性审查案例。

### 多源黄金套装

* 人工审核员
* 前沿法学硕士
* 静态分析

### 结构化调查结果

每个发现都标有严重性和类别，从而实现用户定制的切片。

#### 严重性

* 关键
* 中等
* 低

#### 类别

* 正确性
* 安全性
* 可靠性
* 可维护性
* 测试
* ......

### 评估指标

四个指标衡量已知和新发现的问题。

* 接地精度
* 接地召回
* 提高精度
* 增强回忆

### 客观评价

客观地衡量改进并比较 agents。帮助用户选择最适合他们需求的审稿人。

2

### 我们如何保持其值得信赖

从标题到专家验证和生产检查的可审核链

### 已发布的标题

所有发现的一个明确标准。

### 人工标记的开发集

高级工程师建立基本事实。

### 校准平地机

与人类的判断一致。

### 统一标签

所有来源均采用相同标准。

### 已发布协议

基准质量的专家审核。

### 可端到端审核

### 96.6%协议

高级工程师在发布前独立标记了黄金真实结果。

### 预测生产的离线信号

基准运动根据在线实验进行检查。

* 改进往往会出现在网上
* 回归也往往会出现在网上

JavaScript 绘制连接这些卡的箭头。 ReviewBench 概览
1 我们构建的内容
用于 AI 代码审查的现实、全面的基准 agents
103.9M GitHub 拉取请求
按语言、存储库大小和更改分析分布
代表性基准语料库
219跨19语言的公共拉取请求，与GitHub范围内的分布保持一致，同时保留实质性审查案例。
多源黄金集
人类审查员；前沿法学硕士；静态分析
结构化结果
每个结果都标记了严重性和类别，支持用户定制切片。
严重性：严重
中
低
类别：正确性
安全性
可靠性
可维护性
测试
......
评估指标
四个指标衡量已知和新发现的指标
接地精度；接地召回；提高精度；增强召回
客观评估
客观地衡量agents的改进和比较。帮助用户选择最适合他们需求的审稿人。
2我们如何保持可信度
从标题到专家验证和生产检查的可审核链
发布的标题
所有发现的一个明确标准。
人工标记的开发设置
高级工程师建立地面真相。
校准分级机
与人类判断一致。
统一标签
所有来源的标准相同。
已发布协议
基准质量的专家审核。
可审核的端到端
96.6%协议
高级工程师在发布前独立标记黄金真阳性。
预测生产的离线信号
基准运动与在线检查实验。
改进往往会在网上显现出来；回归也往往会出现在网上。
连接：GitHub向代表性基准语料库拉取请求。
连接：代表性基准语料库到多源黄金集。
连接：多源黄金集到结构化结果。
连接：结构化结果到评估指标。
连接：评估指标到客观评估。
连接：发布的标题到人工标记的开发集。
连接：人工标记的开发集到校准的评分器。
连接：校准的评分器到统一标签。
连接：统一标签到已发布的协议。

## ReviewBench 如何运作

我们的基准围绕五个原则建立：

**1。代表性拉取请求，而不是演示集**

我们分析了 103.9 百万个 GitHub 个拉取请求，以描述代码审查工作负载的真实分布。 ReviewBench 包含来自 187 跨 19 语言的公共开源许可存储库的 219 拉取请求，其语言和存储库大小分布总体上与 GitHub 紧密匹配。完整的基准数据集是公开的。

我们对此分布进行了一项特意的调整：虽然语言和存储库大小直接镜像 GitHub，但拉取请求大小偏重于可审查的中间和尾部。这减少了微小的单文件更改的过度呈现，同时保留了审查质量最重要的更实质性的多文件拉取请求。

快速语料库快照：

**2。广泛的地面实况发现，独立判断**

没有任何一个审阅者，无论是人类还是模型，都可以识别出拉取请求中值得查找的所有内容。为了构建更广泛、更可靠的真实结果黄金集，我们遵循三个阶段的过程：

* **从不同来源收集候选发现。**我们收集来自真实人类审稿人的发现、从作者后续提交中推断出的问题、确定性分析工具以及跨模型系列的多个前沿法学硕士。
* **从语义上消除重复的重叠发现。**我们合并识别相同潜在问题的发现，扩大覆盖范围，而不允许生产者之间达成一致，人为地夸大黄金集或使其依赖于任何一个来源的盲点。
* **在共享的标题下验证结果。**结果的来源并不能确定它是否正确：只有当结果真实、相关且重要时，才算作真阳性。我们使用 Claude Sonnet 5 作为 LLM 评分者，在所有提交的内容中应用一致的评估标准。为了透明度和可重复性，我们公布了评估标准和用于应用该标准的法官。

**3。衡量已知和新发现问题的指标**

大多数基准测试都会针对固定的黄金集报告精确度和召回率。 ReviewBench 报告两个系列的六个指标：

* 接地精度、召回率和 F1 分数仅使用现有的黄金设置标签。它们提供了严格的同类比较：在我们已经知道的问题中，agent 发现了多少问题，以及其发现结果中有多少比例与已知问题相匹配？
* 增强精度、召回率和 F1 分数还可以评估与黄金集中任何内容都不匹配的结果。法官独立确定这些不匹配的发现是真实的还是误报，从而允许审阅者获得黄金组中没有生产者出现的有效问题的信用

随着审查 agents 变得更加强大，这种区别变得更加重要。当系统发现其创建者没有预料到的问题时，固定的黄金集不可避免地会变得不完整。增强指标让 ReviewBench 识别这种行为，而不是自动惩罚它。由于增强召回率根据每个 agent 发现的内容扩展了分母，因此我们使用扎根召回率作为标题跨系统比较，并使用增强指标作为附加的每个系统诊断。

**4。针对不同评论偏好的可配置评估**

不存在单一的普遍最佳的审阅体验。一些开发人员可能只想关注关键问题，而其他开发人员也重视严重性较低、非重大问题的发现。有些人喜欢更广泛的覆盖范围，而另一些人则优先考虑精度和最小噪音。其他人可能有特殊需求，例如以安全或隐私为重点的审查。

ReviewBench 让结果按严重性和类别进行切片，而精确度和召回率则捕获不同的操作偏好。用户还可以调整 Fβ 分数中的 β，以更加重视召回率以获得更广泛的覆盖范围或降低精度以降低噪音。随着这些偏好的变化，排行榜会相应地重新排名，帮助用户识别最符合其审核优先级的系统。

**5。内部审核和可重复评估**

在发布之前，我们要求未参与构建基准数据集的高级工程师从头开始独立地重新标记每个地面实况发现。他们的true/false-positive判断与当时的ReviewBench96.6%一致。我们对每次评估中使用的基准数据集、判断器和匹配器进行版本控制，因此可以在相同的基准配置下比较结果，并在基准发生变化时重新验证结果。我们还发布了验证方法、一致性测量和已知的有效性威胁，以便读者可以了解如何评估基准质量以及仍然存在不确定性。

## 探索ReviewBench

ReviewBench 的研究预览版现已通过 [ ReviewBench 网站 ](<https://review-bench.ai/>) 提供，您可以在其中探索完整的基准测试、比较代码审查 agents，并使用您自己的 agent 进行评估和迭代。

使用ReviewBench，您可以：

* **探索完整的基准数据集。**完整的ReviewBench数据集是公开的，包括拉取请求、调查结果、标签、严重性和类别注释。这使您可以准确检查评估哪些系统并重现基准测试结果。
* **比较排行榜上的系统。**使用完整基准数据进行评估的代码审查agents的结果发布在公共排行榜上，其中包含总体性能、严重性、类别和不同精确召回偏好的视图。
* **带上你自己的agent和爬山。**完整的基准数据集、评估方法、LLM判断提示、判断模型配置和自助运行器都是公开的，因此您可以评估自己的代码审查agent，检查其优点和差距，并针对相同的基准配置。

## 我们如何使用 ReviewBench

我们使用 ReviewBench 来评估连续迭代中的 [Copilot 代码审查 (CCR)](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)，为我们提供了一种一致的方法来衡量进度、捕获回归并优先考虑有希望的更改。随着时间的推移，这帮助我们改进了产品。 ReviewBench 最有价值的好处之一是，它提供了有关产品更改可能如何在生产中执行的早期离线信号。在 A/B 测试之前使用 ReviewBench 评估的实验中，离线更改始终指向与我们稍后在生产中看到的方向相同的方向。

最近的一项精简级实验提供了这种更广泛模式的具体示例。我们引入了多模型集成评审，将多个独立模型运行组合成单个评审，而不是依赖于单个运行。 ReviewBench 预测了更高的精确度、召回率和评论量，以及更低的每次评论成本。

为了比较离线和生产结果，我们使用相应的在线信号。解决率，我们在线对应的精度，是 LLM 确定的 CCR 评论的百分比，提示开发人员根据差异、线程、反应、解决状态和审核后代码进行相应的代码更改。为了召回，我们衡量还需要多少额外的人工审核。

在线A/B测试的进展与ReviewBench预测的方向相同：解决率（精确度）上升8.0%，召回率上升13.6%，评论量上升61%，而每次评论成本下降8.0%，所有这些都与生产控制有关。

然而，仅凭评论量并不能反映评论质量。更重要的发现意味着与低严重程度的尼特截然不同的东西。 ReviewBench 的严重性级别评估也抓住了这一点：与在线 262% 相比，它预测批评评论 227% 会增加，并且同样更广泛地转向更温和的评论和更少的尼特。

这在运行生产实验之前为我们提供了快速且可重复的信号。在线实验仍然是衡量用户影响的最终标准，但 ReviewBench 让我们更有信心进行哪些更改值得在那里进行。

## 如何提交自己的跑步

1. **在[ReviewBench](<https://review-bench.ai/>)网站上使用GitHub**登录。
2. **注册您的agent。**提供容器映像、您的配置和您自己的模型密钥。我们提供法官。
3. **在测试集上尝试。**针对25-PR测试集运行每个PR详细信息，并在调整配置时重复。
4. **进行最后一次运行。**准备好后，运行全套219拉取请求（三轮），由与其他参赛作品相同的评委评分。
5. **发布到排行榜。** 在维护者审核并批准提交之前，您的分数将保持私密。仅当分数优于 agent 的当前排行榜分数，或者这是 agent 的第一个排行榜条目时，分数才会发布到排行榜。

我们邀请您探索 [ReviewBench](<https://review-bench.ai/>)，评估您自己的系统，挑战我们的假设，并帮助我们改进基准。我们很高兴与研究人员和从业者合作，使代码审查评估更加开放、可靠和有用，并最终帮助推动 AI 代码审查向前发展。

## 致谢

ReviewBench 是 GitHub 和 Microsoft 团队的努力。我们感谢构建它的研究人员和工程师：他们设计了方法，策划了拉取请求，构建了黄金集和评估管道，并使基准测试成为任何人都可以运行的。

---

## 标签：

* [AI](<https://github.blog/tag/ai/>)
* [基准测试](<https://github.blog/tag/benchmarking/>)
* [代码审查](<https://github.blog/tag/code-review/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## 撰写者

![周妍希](<https://avatars.githubusercontent.com/u/78101617?v=4&s=200>)

### [周妍希](<https://github.blog/author/miichellezhou/>)

[@miichellezhou](<https://github.com/miichellezhou>)

Michelle 开发和评估用于代码审查的代理 AI 系统，重点关注存储库级上下文检索、审查和修复质量，以及 AI 代码审查者的严格基准测试。

![亚历杭德罗·卡德雷拉·德迭戈](<https://avatars.githubusercontent.com/u/56440520?v=4&s=200>)

### [亚历杭德罗·卡德勒拉·德迭戈](<https://github.blog/author/alejandrocarderera/>)

[@alejandro-carderera](<https://github.com/alejandro-carderera>)

Alejandro 的工作重点是评估编码 agents，改进他们对代码更改和存储库上下文的推理方式，以及优化模型质量、覆盖范围、延迟和令牌成本。


## 原文对照

<details>
<summary>展开完整原文</summary>

# ReviewBench: An open benchmark for AI code review

Agentic code review is becoming an essential piece of how development happens. It helps you inspect pull requests, catch issues, and decide what deserves attention before code ships.

But the quality of existing AI reviewers can be hard to measure, and you need to know the strengths of a reviewer before you know if it will help you. Some reviewers surface more issues, some produce less noise, and some are stronger at catching critical problems while others surface smaller improvements, too. You may need code review to do different things within your workflow.

That makes it important to understand how reviewers actually compare: what different systems catch, what they miss, and the tradeoffs they make. A good code review benchmark should reflect the diversity of real pull requests, capture a broad set of review findings, and support meaningful breakdowns by severity, category, and precision-recall preferences. For teams building code review agents, the benchmark should also provide an offline signal that reliably tracks whether changes are likely to improve the experience in production. Existing benchmarks often make tradeoffs between label quality, coverage, and how well they represent real-world code review, leaving a gap for a rigorous and reproducible evaluation methodology that brings these pieces together.

We built [**ReviewBench**](<https://review-bench.ai/>)**, a new code review offline benchmark**, to address that gap, and it is available for you to use today. It follows the language, repo size, and size distribution of pull requests, modeled after over 100 million real pull requests on GitHub. It uses a multi-source golden set and a consistent evaluation rubric and has been independently validated by senior engineers. Just as important, with the help of ReviewBench, our offline evaluation of Copilot code review (CCR) has become more effective at anticipating the direction of production experiments, giving us greater confidence that measured improvements reflect meaningful gains for users.

In this post, we’ll walk through how ReviewBench is constructed, how it establishes reliable ground truth and scoring, and how to onboard your own code review system and submit results.

## ReviewBench at a glance

1

### What we built

A realistic, comprehensive benchmark for AI code review agents

103.9M

### GitHub pull requests

Analyze distributions by language, repository size, and change shape.

### Representative benchmark corpus

219 public pull requests across 19 languages, aligned to GitHub-wide distributions while preserving substantive review cases.

### Multi-source golden set

* Human reviewers
* Frontier LLMs
* Static analysis

### Structured findings

Every finding is labeled for severity and category, enabling user-tailored slices.

#### Severity

* Critical
* Medium
* Low

#### Category

* Correctness
* Security
* Reliability
* Maintainability
* Testing
* ......

### Evaluation metrics

Four metrics measure both known and newly discovered issues.

* Grounded precision
* Grounded recall
* Augmented precision
* Augmented recall

### Objective evaluation

Measure improvement and compare across agents objectively. Help users choose the reviewer that fits their needs the best.

2

### How we keep it trustworthy

An auditable chain from rubric to expert validation and production checks

### Published rubric

One explicit standard for all findings.

### Human-labeled dev set

Senior engineers establish ground truth.

### Calibrated grader

Aligned with human judgment.

### Uniform labeling

Same standard across all sources.

### Published agreement

Expert audit of benchmark quality.

### Auditable end to end

### 96.6% agreement

Senior engineers independently labeled golden true-positives before release.

### Offline signals that anticipate production

Benchmark movement is checked against online experiments.

* Improvements tend to show up online
* Regressions tend to show up online too

JavaScript draws the arrows connecting these cards. ReviewBench at a glance
1 What we built
A realistic, comprehensive benchmark for AI code review agents
103.9M GitHub pull requests
Analyze distributions by language, repository size, and change shape.
Representative benchmark corpus
219 public pull requests across 19 languages, aligned to GitHub-wide distributions while preserving substantive review cases.
Multi-source golden set
Human reviewers; Frontier LLMs; Static analysis
Structured findings
Every finding is labeled for severity and category, enabling user-tailored slices.
Severity: Critical
Medium
Low
Category: Correctness
Security
Reliability
Maintainability
Testing
......
Evaluation metrics
Four metrics measure both known and newly discovered issues.
Grounded precision; Grounded recall; Augmented precision; Augmented recall
Objective evaluation
Measure improvement and compare across agents objectively. Help users choose the reviewer that fits their needs the best.
2 How we keep it trustworthy
An auditable chain from rubric to expert validation and production checks
Published rubric
One explicit standard for all findings.
Human-labeled dev set
Senior engineers establish ground truth.
Calibrated grader
Aligned with human judgment.
Uniform labeling
Same standard across all sources.
Published agreement
Expert audit of benchmark quality.
Auditable end to end
96.6% agreement
Senior engineers independently labeled golden true-positives before release.
Offline signals that anticipate production
Benchmark movement is checked against online experiments.
Improvements tend to show up online; Regressions tend to show up online too
Connection: GitHub pull requests to Representative benchmark corpus.
Connection: Representative benchmark corpus to Multi-source golden set.
Connection: Multi-source golden set to Structured findings.
Connection: Structured findings to Evaluation metrics.
Connection: Evaluation metrics to Objective evaluation.
Connection: Published rubric to Human-labeled dev set.
Connection: Human-labeled dev set to Calibrated grader.
Connection: Calibrated grader to Uniform labeling.
Connection: Uniform labeling to Published agreement.

## How ReviewBench works

Our benchmark is built around five principles:

**1. Representative pull requests, not a demo set**

We analyzed 103.9 million GitHub pull requests to characterize the real-world distribution of code review workloads. ReviewBench contains 219 pull requests from 187 public open source licensed repositories spanning 19 languages, with its language and repository-size distributions closely matching GitHub overall. The complete benchmark dataset is publicly available.

We make one deliberate adjustment to this distribution: while language and repository size mirror GitHub directly, pull request size is weighted toward the reviewable middle and tail. This reduces the overrepresentation of tiny, single-file changes while preserving more substantive, multi-file pull requests where review quality matters most.

Quick corpus snapshot:

**2. Broad ground truth discovery, independently judged**

No single reviewer, whether human or model, can identify everything worth finding in a pull request. To build a broader and more reliable golden set for ground truth findings, we follow a three-stage process:

* **Gather candidate findings from diverse sources.** We collect findings from real human reviewers, issues inferred from author follow-up commits, deterministic analysis tools, and multiple frontier LLMs across model families.
* **Semantically deduplicate overlapping findings.** We merge findings that identify the same underlying issue, broadening coverage without allowing agreement across producers to artificially inflate the golden set or making it dependent on any one source’s blind spots.
* **Validate findings under a shared rubric.** The source of a finding does not determine whether it is correct: a finding counts as a true positive only if it is true, relevant, and non-trivial. We use Claude Sonnet 5 as the LLM grader, applying a consistent evaluation rubric across all submissions. For transparency and reproducibility, we publish both the evaluation rubric and the judge used to apply it.

**3. Metrics that measure both known and newly discovered issues**

Most benchmarks report precision and recall against a fixed golden set. ReviewBench reports six metrics in two families:

* Grounded precision, recall, and F1 score use only the existing gold-set labels. They provide the strict, apples-to-apples comparison: of the issues we already know about, how many did the agent find, and what share of its findings matched a known issue?
* Augmented precision, recall and F1 score also evaluate findings that do not match anything in the golden set. The judge independently determines whether those unmatched findings are true or false positives, allowing a reviewer to receive credit for valid issues that no producer in the golden set surfaced

That distinction becomes more important as review agents become more capable. A fixed golden set inevitably becomes incomplete as systems discover issues its creators did not anticipate. Augmented metrics let ReviewBench recognize that behavior rather than automatically penalizing it. Because augmented recall expands the denominator based on what each agent discovers, we use grounded recall as the headline cross-system comparison and augmented metrics as an additional per-system diagnostic.

**4. Configurable evaluation for different review preferences**

There is no single universally optimal review experience. Some developers may want to focus only on critical issues, while others also value lower-severity, non-breaking findings. Some prefer broader coverage, while others prioritize precision and minimal noise. Others may have specialized needs, such as security- or privacy-focused review.

ReviewBench lets results be sliced by severity and category, while precision and recall capture different operating preferences. Users can also adjust β in the Fβ score to place more weight on recall for broader coverage or precision for lower noise. As these preferences change, the leaderboard is re-ranked accordingly, helping users identify the systems that best match their review priorities.

**5. Internally audited and reproducibly evaluated**

Before release, we asked senior engineers who had not participated in building the benchmark dataset to independently re-label every ground-truth finding from scratch. Their true/false-positive judgments agreed with ReviewBench 96.6% of the time. We version the benchmark dataset, judge, and matcher used in every evaluation, so results can be compared under the same benchmark configuration and revalidated when the benchmark changes. We also publish the validation methodology, agreement measurements, and known threats to validity, so readers can see how benchmark quality is assessed and where uncertainty remains.

## Explore ReviewBench

ReviewBench’s research preview version is now available through [the ReviewBench website](<https://review-bench.ai/>), where you can explore the full benchmark, compare code review agents, and bring your own agent to evaluate and iterate.

With ReviewBench, you can:

* **Explore the full benchmark dataset.** The complete ReviewBench dataset is publicly available, including the pull requests, findings, labels, severity and category annotations. This allows you to inspect exactly what systems are evaluated on and reproduce benchmark results.
* **Compare systems on the leaderboard.** Results from evaluated code review agents using the full benchmark data are published on a common leaderboard, with views across overall performance, severity, category, and different precision–recall preferences.
* **Bring your own agent and hill-climb.** The full benchmark dataset, evaluation methodology, LLM judge prompt, judge model configuration, and self-serve runner are publicly available, so you can evaluate your own code review agent, inspect its strengths and gaps, and iterate against the same benchmark configuration.

## How we’ve used ReviewBench

We have used ReviewBench to evaluate [Copilot code review (CCR)](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>) across successive iterations, giving us a consistent way to measure progress, catch regressions, and prioritize promising changes. Over time, this has helped us improve the product. One of the most valuable benefits of ReviewBench is that it provides an early offline signal of how a change to the product is likely to perform in production. Across experiments evaluated with ReviewBench before A/B testing, offline changes have consistently pointed in the same direction as what we see later in production.

A recent lite-tier experiment provides a concrete example of this broader pattern. We introduced a multi-model ensemble review that combines several independent model runs into a single review rather than relying on a single run. ReviewBench predicted higher precision, recall, and comment volume, along with lower cost per review.

To compare offline and production results, we use corresponding online signals. Addressed rate, our online counterpart to precision, is the percentage of CCR comments that an LLM determines prompted a developer to make a corresponding code change, based on the diff, thread, reactions, resolution state, and post-review code. For recall, we measure how much additional human review is still needed.

The online A/B test moved in the same direction as ReviewBench predicted: addressed rate (precision) rose 8.0%, recall rose 13.6%, and comment volume rose 61%, while cost per review fell 8.0%, all relative to the production control.

Comment volume alone, however, does not capture comment quality. More critical findings mean something very different from low-severity nits. ReviewBench’s severity-level evaluation captured this too: it predicted a 227% increase in critical comments, compared with 262% online, along with the same broader shift toward more moderate comments and fewer nits.

This gives us a fast and repeatable signal before running production experiments. Online experiments remain the ultimate measure of user impact, but ReviewBench gives us greater confidence in which changes are worth taking there.

## How to submit your own run

1. **Sign in with GitHub** on the [ReviewBench](<https://review-bench.ai/>) website.
2. **Register your agent.** Provide a container image, your configuration, and your own model key. We provide the judge.
3. **Try it on the test set.** Run against a 25-PR test set with per-PR detail and repeat as you tune your configuration.
4. **Do a final run.** When you’re ready, run the full set of 219 pull requests (three rounds), scored by the same judge as every other entry.
5. **Publish to the leaderboard.** Your scores remain private until a maintainer reviews and approves the submission. Scores are published to the leaderboard only if they outperform the agent’s current leaderboard score, or if this is the agent’s first leaderboard entry.

We invite you to explore [ReviewBench](<https://review-bench.ai/>), evaluate your own system, challenge our assumptions, and help us improve the benchmark. We’re excited to collaborate with researchers and practitioners to make code review evaluation more open, reliable, and useful—and ultimately help move AI code review forward.

## Acknowledgments

ReviewBench was a team effort across GitHub and Microsoft. We’re grateful to the researchers and engineers who built it: those who designed the methodology, curated the pull requests, built the golden set and the evaluation pipeline, and made the benchmark something anyone can run.

---

## Tags:

* [AI](<https://github.blog/tag/ai/>)
* [benchmarking](<https://github.blog/tag/benchmarking/>)
* [code review](<https://github.blog/tag/code-review/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## Written by

![Michelle Zhou](<https://avatars.githubusercontent.com/u/78101617?v=4&s=200>)

### [Michelle Zhou](<https://github.blog/author/miichellezhou/>)

[@miichellezhou](<https://github.com/miichellezhou>)

Michelle develops and evaluates agentic AI systems for code review, focusing on repository-level context retrieval, review and fix quality, and rigorous benchmarking of AI code reviewers.

![Alejandro Carderera de Diego](<https://avatars.githubusercontent.com/u/56440520?v=4&s=200>)

### [Alejandro Carderera de Diego](<https://github.blog/author/alejandrocarderera/>)

[@alejandro-carderera](<https://github.com/alejandro-carderera>)

Alejandro's work focuses on evaluating coding agents, improving how they reason over code changes and repository context, and optimizing model quality, coverage, latency, and token cost.


</details>
