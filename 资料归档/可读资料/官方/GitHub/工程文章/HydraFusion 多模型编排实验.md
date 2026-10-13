# HydraFusion 多模型编排实验

介绍按任务选择模型的编码流程和评估结果，讨论多模型协作、基准迭代与研究预览条件；结果适用于文章中的实验设置。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-09-04](<https://github.blog/ai-and-ml/github-copilot/project-hydrafusion-frontier-quality-via-multi-model-orchestration/>)；更新：[2026-09-04](<https://github.blog/ai-and-ml/github-copilot/project-hydrafusion-frontier-quality-via-multi-model-orchestration/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/project-hydrafusion-frontier-quality-via-multi-model-orchestration/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/HydraFusion 多模型编排实验.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 项目 HydraFusion：通过多模型编排实现前沿质量

为开发人员提供适合手头任务的最佳模型一直是我们的目标。今年早些时候，我们推出了[自动模型选择，](<https://docs.github.com/copilot/concepts/models/auto-model-selection>)，它会审查您的任务并将其与最适合该任务的模型相匹配，从而使这一过程变得更加容易。

今天，我们将介绍项目 HydraFusion，这是一个通过运行时编排提供前沿智能的研究预览。它创建一个完整的执行计划，从多个提供商的模型中进行选择来起草、批评和修改，或级联到更强大的模型来完成您的任务。

HydraFusion 在我们的整体战略中发挥着关键作用，以在本地、云和复合模型之间提供自动化语义路由。对于开发人员来说，这种复杂性仍然存在于幕后：您像任何其他模型一样选择 HydraFusion，它会选择一个平衡每个任务的性能、成本和延迟的工作流程。

HydraFusion将工作流选择视为优化问题。它使用推理、代码生成、调试和工具使用的能力信号来选择最有效的执行模式来满足质量标准。

对于每个请求，HydraFusion 当前选择三种执行模式之一：

* **单。**一个选定的模型直接解决任务。
* **级联。**高效的模型起草解决方案，质量门决定是接受它还是升级到更强的模型。
* **批评。**一个模型起草一个结果，来自不同模型系列的独立只读评论家对其进行审查（遵循与[Rubber Duck](<https://docs.github.com/en/copilot/concepts/agents/copilot-cli/rubber-duck>)相同的审查模式），并且起草模型修改一次。

![](<https://github.blog/wp-content/uploads/2026/09/HydraFusion_architecture_02.png?resize=1024%2C334>)

图1。 HydraFusion 架构

每种模式都解决不同的质量与成本权衡问题。 **单一** 当一个模型可以直接解决任务时保持速度和效率。 **Cascade** 在第一次尝试时给出了一个有效的模型，同时在候选人未清除接受门时保留了更强的推理路径。 **批判**为任务添加了独立的视角，其中审查比其他独立尝试更有用。

在三个代理编码基准的离线评估中，HydraFusion 始终表现出前沿水平的质量，并且预计可节省大量成本。在 TerminalBench 2.1 上，与 Claude Opus 5 相比，它以 67% 较低的估计成本将验证任务质量提高了 4.9 个百分点。

让我们深入探讨方法、结果和基准。

## 自适应多模型编排

开发人员已经手动协调模型：为一项任务选择一个模型，要求另一个模型审查工作，或者将一个难题升级为功能更强大的模型。 HydraFusion 将熟悉的流程引入运行时。您选择 HydraFusion 一次，即可专注于您的任务，同时它在幕后管理模型和工作流程。

关键是选择性。有些编码任务可以直接解决，而另一些则可以从审查、修订或升级中受益。 HydraFusion 评估每个请求并选择最简单的工作流程来满足其需求，仅在可能改善结果时使用额外的模型调用。这种自适应方法可以平衡模型之间的质量、成本和延迟。

随着模型前沿的进步，HydraFusion 也在进步。当 GitHub Copilot 中出现新模型时，我们可以评估它们并将其合并到模型池中，将它们的优势用于最适合它们的任务。

## 大楼HydraFusion

将自适应多模型编排转变为一种可靠的编码体验需要仔细控制执行、审查、成本和存储库状态。 HydraFusion 围绕五个操作原则构建：

* **完整的核算。**汇总每个工作流环节的成本和使用情况，包括起草、批评、修订、升级、重试和回退。
* **有界执行。**为每个分支提供明确的超时和取消行为，以将执行和成本保持在定义的限制内。
* **隔离审核。** 在隔离的无工具上下文中运行审核步骤，而求解器步骤使用共享工作区和正常的权限感知 agent 循环。这允许模型独立评估工作，而无需修改存储库。
* **故障安全应用程序。**当工作流程取消或验证失败时不应用补丁，从而防止不完整的更改到达存储库。
* **已验证路由。**在执行开始之前验证工作流定义、模型绑定、回退行为和模型可用性。

这些原则共同使得多模型编排对于存储库级别的工作来说非常实用。在内部，运行时记录每个分支的角色、结果、成本、延迟和诊断，以便在执行后可以理解工作流程。在外部，开发人员会收到一份一致的响应和一份许可感知的变更集。

## 基准测试结果

固定的 HydraFusion 策略在三个代理编码基准上进行了评估 - TerminalBench 2.1、DeepSWE 和 CheckpointBench，我们的内部基准基于真实的 GitHub Copilot 会话 - 使用 Claude Opus 5 和 GPT-5.6 Sol 作为比较基线。每个策略都使用相同的任务输入、工具、执行限制、定价假设、评分条件和缺失结果的处理。该评估衡量了已验证的任务质量（即确认正确回答的任务的比例）以及完整的估计工作流程成本。成本核算包括每个调用的环节，例如起草、批评、修订、升级、重试和后备。下面的结果显示了最佳调整的 HydraFusion 配置。

|**基准**|**成本与 Opus 5**|**质量与 Opus 5**|
| --- | --- | --- |
| **TerminalBench 2.1** |67% 较低|+4.9 积分|
| **DeepSWE** |36% 下|-1.5 积分|
| **CheckpointBench** |65% 下|-0.1 积分|

表1。 HydraFusion 相对于 Opus 5 而言，三个代理基准的质量和成本。

这些受控离线结果特定于评估的基准修订、工作流配置、模型池和定价假设，所有模型都在相同的中等推理水平上评估。通过此研究预览，我们将验证这些结果如何转化为实际的开发人员工作负载，并使用这些结果进一步优化 HydraFusion 的生产质量、延迟、可靠性、缓存效率、成本和安全性。

### TerminalBench 2.1

TerminalBench 2.1 评估终端环境中复杂、多步骤任务的编码 agents。

图 2 比较了 HydraFusion 和 Opus 5 在验证的任务质量和估计的工作流程成本方面的情况。

### DeepSWE

DeepSWE 评估具有挑战性的存储库级软件工程任务，这些任务需要导航大型代码库、了解跨文件依赖关系并生成端到端修复。在此基准测试中，HydraFusion 与 Opus 5 相差 1.5 个百分点，同时将成本降低 36%，展示了复杂的实际工程任务的令人信服的质量成本权衡。

### CheckpointBench

CheckpointBench 是根据真实的 GitHub Copilot 代理编码会话策划的内部多轮基准测试。每个对话都锚定到特定的公共存储库和不可变的提交，确保每个会话都是可重播的。该基准在语言、任务类型、难度方面进行了平衡，并针对质量进行了优化，从而产生了一个密切反映生产代理会话的现实评估集。在此基准上，HydraFusion 的成本比 Opus 5 低 0.1 个百分点，且成本较低 65%。

早期的内部测试也呼应了这一结果。

> 到目前为止，推理和解决任务的能力 [的 HydraFusion] 等于或优于 Opus。
>
> 首席软件工程师 Microsoft

## 爬山HydraFusion

HydraFusion 的路由策略是根据开发人员在实际编码任务中使用 GitHub Copilot 的方式制定的。为了使这些工作流程可重复，我们从真实的 Copilot 编码会话轨迹中策划了 CheckpointBench。我们在 CheckpointBench、DeepSWE 和 TerminalBench 2.1 中反复细化 HydraFusion，跨评估集而不是针对任何单个基准进行优化。

HydraFusion 的每项功能得分为比较候选路由策略提供了一致的基础。我们没有手动调整阈值，而是使用波束搜索来构建最佳决策策略。每个候选人都根据质量、成本和故障模式的冻结基线进行衡量，因此在稳定的基础上评估改进。

TerminalBench 2.1 提供了最完整的运行序列，使其成为此迭代改进的最清晰视图。进展不是线性的。在 8 月 11 和 8 月 25 之间，评估工具中的两次操作故障导致了无效运行。这些故障被排除在性能趋势之外并得到纠正，然后 HydraFusion 配置持续获得收益。到八月25，HydraFusion已达到记录系列中最强的操作点。

这个发展记录显示了政策是如何通过反复试验而改进的。 TerminalBench 2.1 是开发过程中使用的几个基准之一。它的相对饱和度使得更广泛的验证变得重要，因此三基准评估还包括 DeepSWE 要求更高的存储库级任务。研究预览将学习循环扩展到实际的开发人员工作负载。

## 尝试研究预览

对于此预览版，第一轮单提示编码任务是最好的起点。接下来我们将重点关注强大的多轮性能以及更长的迭代会话。

此预览旨在了解哪些任务受益于复合工作流程以及编排如何影响实践中的延迟和成本。为了获得今天的最佳体验，请从大量的、范围广泛的编码任务开始，您可以在自动驾驶模式下在单个提示中将这些任务交给 Copilot。通过 Copilot CLI 中的 `/feedback` 或 [GitHub 社区讨论 ](<https://github.com/orgs/community/discussions/206492>) 中分享您的发现，包括它的优点、不足之处以及您接下来想看到的内容。

HydraFusion 仍然是一项积极的研究工作。随着我们从预览中了解到，结果、模型、工作流程、可用性、名称和产品行为可能会发生变化。我们相信编码 agents 的下一个真正收益将来自前沿智能与运行时编排的结合。 HydraFusion 是我们对这个想法的第一个赌注：从选择最佳模型转向动态构建解决每个任务的最佳方法。

## 致谢

非常感谢 GitHub 和 Microsoft 的研究人员、工程师、产品经理和设计师，他们整理了培训数据并构建了培训管道、评估套件、客户体验和服务堆栈。我们特别感谢 GitHub Copilot CLI、Copilot API 和 VS Code 团队克服了众多挑战，为我们的客户带来了这项研究预览。

## 认识团队

![](<https://github.blog/wp-content/uploads/2026/09/2555061.jpeg?resize=150%2C150>) [Aashna Garg](<https://github.com/aashna>)，首席应用科学家，代码 AI

![](<https://github.blog/wp-content/uploads/2026/09/23369389.jpeg?resize=150%2C150>) [Shengyu Fu](<https://github.com/shengyfu>)，合作伙伴应用科学经理，代码 AI

![](<https://github.blog/wp-content/uploads/2026/09/21318528.jpeg?resize=150%2C150>) [Carlos Castro](<https://github.com/carlosscastro>)，合伙人架构师，GitHub Copilot

![](<https://github.blog/wp-content/uploads/2026/09/42297087.jpeg?resize=150%2C150>) [Siddharth Singha Roy](<https://github.com/sid200026>)，研究科学家 II，代码 AI

![](<https://github.blog/wp-content/uploads/2026/09/4208418.jpeg?resize=150%2C150>) [Andy Salerno](<https://github.com/andysalerno>)，首席软件工程师，GitHub Copilot

---

## 标签：

* [基准测试](<https://github.blog/tag/benchmarking/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [法学硕士](<https://github.blog/tag/llms/>)

## 撰写者

![GitHub 工作人员](<https://avatars.githubusercontent.com/u/9919?v=4&s=200>)

### [GitHub 工作人员](<https://github.blog/author/staff/>)

[@github](<https://github.com/github>)

GitHub 是世界上最好的开发者体验，也是唯一一个将安全性融入到每一步的 AI- 支持平台，因此您可以充满信心地进行创新。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Project HydraFusion: Frontier quality via multi-model orchestration

Providing developers the best model for the task at hand has always been our goal. Earlier this year, we made that easier by launching [Auto model selection,](<https://docs.github.com/copilot/concepts/models/auto-model-selection>) which reviews your task and matches it to the best-suited model for that task.

Today, we’re introducing Project HydraFusion, a research preview that delivers frontier intelligence through runtime orchestration. It creates a full execution plan, choosing from models across multiple providers to draft, critique and revise, or cascade to more powerful models to complete your task.

HydraFusion fills a key role in our overall strategy to deliver automated semantic routing between local, cloud, and compound models. For developers, that complexity stays behind the scenes: you select HydraFusion like any other model, and it chooses a workflow that balances performance, cost, and latency for each task.

HydraFusion treats workflow selection as an optimization problem. It uses capability signals for reasoning, code generation, debugging, and tool use to select the most efficient execution pattern to meet the quality bar.

For each request, HydraFusion currently chooses one of three execution patterns:

* **Single.** One selected model solves the task directly.
* **Cascade.** An efficient model drafts a solution and a quality gate decides whether to accept it or escalate to a stronger model.
* **Critique.** One model drafts a result, an independent read-only critic from a different model family reviews it (following the same review pattern as [Rubber Duck](<https://docs.github.com/en/copilot/concepts/agents/copilot-cli/rubber-duck>)), and the drafting model revises once.

![](<https://github.blog/wp-content/uploads/2026/09/HydraFusion_architecture_02.png?resize=1024%2C334>)

Figure 1. HydraFusion architecture

Each pattern addresses a different quality-to-cost trade-off. **Single** preserves speed and efficiency when one model can solve the task directly. **Cascade** gives an efficient model the first attempt while retaining a path to stronger inference when the candidate does not clear the acceptance gate. **Critique** adds an independent perspective for tasks where review is more useful than another unaided attempt.

In offline evaluations across three agentic coding benchmarks, HydraFusion consistently demonstrated frontier-level quality with substantial estimated cost savings. On TerminalBench 2.1, it improved verified task quality by 4.9 percentage points at 67% lower estimated cost compared with Claude Opus 5.

Let’s dive into the approach, the results, and the benchmarks.

## Adaptive multi-model orchestration

Developers already coordinate models manually: choosing one for a task, asking another to review the work, or escalating a difficult problem to a more capable model. HydraFusion brings that familiar process into the runtime. You choose HydraFusion once and stay focused on your task while it manages the models and workflow behind the scenes.

The key is selectivity. Some coding tasks can be solved directly, while others benefit from review, revision, or escalation. HydraFusion evaluates each request and chooses the least complex workflow expected to meet its needs, using additional model calls only when they are likely to improve the result. This adaptive approach balances quality, cost, and latency across models.

As the model frontier advances, so does HydraFusion. When new models become available in GitHub Copilot, we can evaluate and incorporate them into its model pool, bringing their strengths to the tasks best suited to them.

## Building HydraFusion

Turning adaptive multi-model orchestration into one dependable coding experience requires careful control of execution, review, cost, and repository state. HydraFusion is built around five operating principles:

* **Complete accounting.** Aggregate cost and usage across every workflow leg, including drafting, critique, revision, escalation, retry, and fallback.
* **Bounded execution.** Give each leg explicit timeout and cancellation behavior to keep execution and cost within defined limits.
* **Isolated review.** Run review steps in isolated, tool-less contexts, while solver steps use the shared workspace and normal permission-aware agent loop. This allows models to assess the work independently without modifying the repository.
* **Fail-safe application.** Apply no patch when the workflow is cancelled or fails validation, preventing incomplete changes from reaching the repository.
* **Validated routing.** Verify workflow definitions, model bindings, fallback behavior, and model availability before execution begins.

Together, these principles make multi-model orchestration practical for repository-level work. Internally, the runtime records the role, outcome, cost, latency, and diagnostics of each leg so the workflow can be understood after execution. Externally, the developer receives one coherent response and one permission-aware change set.

## Benchmarking results

Fixed HydraFusion policies were evaluated across three agentic coding benchmarks — TerminalBench 2.1, DeepSWE, and CheckpointBench, our internal benchmark based on real GitHub Copilot sessions — using Claude Opus 5 and GPT-5.6 Sol as comparison baselines. Each policy used the same task inputs, tools, execution limits, pricing assumptions, grading conditions, and treatment of missing results. The evaluation measured verified task quality, which is the share of tasks confirmed as correctly answered, and the complete estimated workflow cost. Cost accounting included every invoked leg, such as drafting, critique, revision, escalation, retry, and fallback. The results below show the best tuned HydraFusion configuration.

| **Benchmarks** | **Cost  vs. Opus 5** | **Quality  vs. Opus 5** |
| --- | --- | --- |
| **TerminalBench 2.1** | 67% lower | +4.9 points |
| **DeepSWE** | 36% lower | -1.5 points |
| **CheckpointBench** | 65% lower | -0.1 points |

Table 1. HydraFusion quality and cost across three agentic benchmarks, relative to Opus 5.

These controlled offline results are specific to the evaluated benchmark revisions, workflow configurations, model pool, and pricing assumptions, with all models evaluated at the same medium reasoning level. Through this research preview, we’ll validate how these results translate to real developer workloads and use the findings to further optimize HydraFusion for production quality, latency, reliability, caching efficiency, cost, and safety.

### TerminalBench 2.1

TerminalBench 2.1 evaluates coding agents on complex, multi-step tasks in terminal environments.

Figure 2 compares HydraFusion and Opus 5 across verified task quality and estimated workflow cost.

### DeepSWE

DeepSWE evaluates challenging repository-level software engineering tasks that require navigating large codebases, understanding cross-file dependencies, and producing end-to-end fixes. On this benchmark, HydraFusion comes within 1.5 percentage points of Opus 5 while reducing cost by 36%, demonstrating a compelling quality-cost tradeoff for complex real-world engineering tasks.

### CheckpointBench

CheckpointBench is an internal multi-turn benchmark curated from real GitHub Copilot agentic coding sessions. Each conversation is anchored to a specific public repository and immutable commit, ensuring every session is replayable. The benchmark is balanced across language, task type, difficulty, scrubbed for quality, resulting in a realistic evaluation set that closely mirrors production agentic sessions. On this benchmark, HydraFusion comes within 0.1 percentage points of Opus 5 at 65% lower cost.

Early internal testing has echoed that result.

> So far, the reasoning and task solving capability [of HydraFusion] is at or better than Opus.
>
> Principal Software Engineer at Microsoft

## Hill-climbing HydraFusion

HydraFusion’s routing policies were shaped by how developers use GitHub Copilot on real coding tasks. To make those workflows reproducible, we curated CheckpointBench from real Copilot coding-session trajectories. We refined HydraFusion repeatedly across CheckpointBench, DeepSWE, and TerminalBench 2.1, optimizing across the evaluation sets rather than for any single benchmark.

HydraFusion’s per-capability scores provided a consistent basis for comparing candidate routing policies. Instead of manually tuning thresholds, we used beam search to build the optimal decision policy. Each candidate was measured against a frozen baseline on quality, cost, and failure modes, so improvements were evaluated on stable ground.

TerminalBench 2.1 provides the most complete sequence of runs, making it the clearest view of this iterative improvement. The progression was not linear. Between August 11 and August 25, two operational failures in the evaluation harness produced invalid runs. Those failures were excluded from the performance trend, corrected, and followed by continued gains in the HydraFusion configurations. By August 25, HydraFusion had reached its strongest operating points in the recorded series.

This development record shows how the policies improved from repeated experiments. TerminalBench 2.1 was one of several benchmarks used during development. Its relative saturation makes broader validation important, so the three-benchmark evaluation also includes DeepSWE’s more demanding repository-level tasks. The research preview extends that learning loop to real developer workloads.

## Try the research preview

For this preview, first-turn, single-prompt coding tasks are the best place to start. We’ll be focusing on strong multi-turn performance with longer, iterative sessions next.

This preview is designed to learn which tasks benefit from compound workflows and how orchestration affects latency and cost in practice. For the best experience today, start with substantial, well-scoped coding tasks that you can hand to Copilot in autopilot mode in a single prompt. Share what you find, including where it excels, where it falls short, and what you’d want to see next, through `/feedback` in Copilot CLI or in the [GitHub Community discussion](<https://github.com/orgs/community/discussions/206492>).

HydraFusion remains an active research effort. Results, models, workflows, availability, names, and product behavior may change as we learn from the preview. We believe the next real gain in coding agents will come from combining frontier intelligence with runtime orchestration. HydraFusion is our first bet on that idea: moving from choosing the best model to dynamically constructing the best way to solve each task.

## Acknowledgments

A huge thank-you to the researchers, engineers, product managers, and designers across GitHub and Microsoft who curated the training data and built the training pipeline, evaluation suites, client experience, and serving stack. We are especially grateful to the GitHub Copilot CLI, Copilot API and VS Code team for overcoming numerous challenges to bring this research preview to our customers.

## Meet the Team

![](<https://github.blog/wp-content/uploads/2026/09/2555061.jpeg?resize=150%2C150>) [Aashna Garg](<https://github.com/aashna>), Principal Applied Scientist, Code AI

![](<https://github.blog/wp-content/uploads/2026/09/23369389.jpeg?resize=150%2C150>) [Shengyu Fu](<https://github.com/shengyfu>), Partner Applied Science Manager, Code AI

![](<https://github.blog/wp-content/uploads/2026/09/21318528.jpeg?resize=150%2C150>) [Carlos Castro](<https://github.com/carlosscastro>), Partner Architect, GitHub Copilot

![](<https://github.blog/wp-content/uploads/2026/09/42297087.jpeg?resize=150%2C150>) [Siddharth Singha Roy](<https://github.com/sid200026>), Research Scientist II, Code AI

![](<https://github.blog/wp-content/uploads/2026/09/4208418.jpeg?resize=150%2C150>) [Andy Salerno](<https://github.com/andysalerno>), Principal Software Engineer, GitHub Copilot

---

## Tags:

* [benchmarking](<https://github.blog/tag/benchmarking/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [LLMs](<https://github.blog/tag/llms/>)

## Written by

![GitHub Staff](<https://avatars.githubusercontent.com/u/9919?v=4&s=200>)

### [GitHub Staff](<https://github.blog/author/staff/>)

[@github](<https://github.com/github>)

GitHub is the world's best developer experience and the only AI-powered platform with security incorporated into every step, so you can innovate with confidence.


</details>
