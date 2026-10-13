# Project HydraFusion: Frontier quality via multi-model orchestration

Providing developers the best model for the task at hand has always been our goal. Earlier this year, we made that easier by launching [Auto model selection,](https://docs.github.com/copilot/concepts/models/auto-model-selection) which reviews your task and matches it to the best-suited model for that task.

Today, we’re introducing Project HydraFusion, a research preview that delivers frontier intelligence through runtime orchestration. It creates a full execution plan, choosing from models across multiple providers to draft, critique and revise, or cascade to more powerful models to complete your task.

HydraFusion fills a key role in our overall strategy to deliver automated semantic routing between local, cloud, and compound models. For developers, that complexity stays behind the scenes: you select HydraFusion like any other model, and it chooses a workflow that balances performance, cost, and latency for each task.

HydraFusion treats workflow selection as an optimization problem. It uses capability signals for reasoning, code generation, debugging, and tool use to select the most efficient execution pattern to meet the quality bar.

For each request, HydraFusion currently chooses one of three execution patterns:

* **Single.** One selected model solves the task directly.
* **Cascade.** An efficient model drafts a solution and a quality gate decides whether to accept it or escalate to a stronger model.
* **Critique.** One model drafts a result, an independent read-only critic from a different model family reviews it (following the same review pattern as [Rubber Duck](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/rubber-duck)), and the drafting model revises once.

![](https://github.blog/wp-content/uploads/2026/09/HydraFusion_architecture_02.png?resize=1024%2C334)

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

This preview is designed to learn which tasks benefit from compound workflows and how orchestration affects latency and cost in practice. For the best experience today, start with substantial, well-scoped coding tasks that you can hand to Copilot in autopilot mode in a single prompt. Share what you find, including where it excels, where it falls short, and what you’d want to see next, through `/feedback` in Copilot CLI or in the [GitHub Community discussion](https://github.com/orgs/community/discussions/206492).

HydraFusion remains an active research effort. Results, models, workflows, availability, names, and product behavior may change as we learn from the preview. We believe the next real gain in coding agents will come from combining frontier intelligence with runtime orchestration. HydraFusion is our first bet on that idea: moving from choosing the best model to dynamically constructing the best way to solve each task.

## Acknowledgments

A huge thank-you to the researchers, engineers, product managers, and designers across GitHub and Microsoft who curated the training data and built the training pipeline, evaluation suites, client experience, and serving stack. We are especially grateful to the GitHub Copilot CLI, Copilot API and VS Code team for overcoming numerous challenges to bring this research preview to our customers.

## Meet the Team

![](https://github.blog/wp-content/uploads/2026/09/2555061.jpeg?resize=150%2C150) [Aashna Garg](https://github.com/aashna), Principal Applied Scientist, Code AI

![](https://github.blog/wp-content/uploads/2026/09/23369389.jpeg?resize=150%2C150) [Shengyu Fu](https://github.com/shengyfu), Partner Applied Science Manager, Code AI

![](https://github.blog/wp-content/uploads/2026/09/21318528.jpeg?resize=150%2C150) [Carlos Castro](https://github.com/carlosscastro), Partner Architect, GitHub Copilot

![](https://github.blog/wp-content/uploads/2026/09/42297087.jpeg?resize=150%2C150) [Siddharth Singha Roy](https://github.com/sid200026), Research Scientist II, Code AI

![](https://github.blog/wp-content/uploads/2026/09/4208418.jpeg?resize=150%2C150) [Andy Salerno](https://github.com/andysalerno), Principal Software Engineer, GitHub Copilot

---

## Tags:

* [benchmarking](https://github.blog/tag/benchmarking/)
* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [LLMs](https://github.blog/tag/llms/)

## Written by

![GitHub Staff](https://avatars.githubusercontent.com/u/9919?v=4&s=200)

### [GitHub Staff](https://github.blog/author/staff/)

[@github](https://github.com/github)

GitHub is the world's best developer experience and the only AI-powered platform with security incorporated into every step, so you can innovate with confidence.
