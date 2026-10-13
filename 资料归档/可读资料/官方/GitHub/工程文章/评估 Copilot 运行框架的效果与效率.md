# 评估 Copilot 运行框架的效果与效率

介绍跨模型和任务的基准评估、Token 效率、完成率与结果波动，说明评测方法和运行框架的优化依据。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-06-25](<https://github.blog/ai-and-ml/github-copilot/evaluating-performance-and-efficiency-of-the-github-copilot-agentic-harness-across-models-and-tasks/>)；更新：[2026-06-25](<https://github.blog/ai-and-ml/github-copilot/evaluating-performance-and-efficiency-of-the-github-copilot-agentic-harness-across-models-and-tasks/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/evaluating-performance-and-efficiency-of-the-github-copilot-agentic-harness-across-models-and-tasks/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/评估 Copilot 运行框架的效果与效率.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 跨模型和任务评估 GitHub Copilot 代理工具的性能和效率

虽然模型提供了原始情报，但线束决定了情报的应用效率。 GitHub Copilot 代理线束是 [GitHub Copilot SDK](<https://github.com/github/copilot-sdk?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>) 的单个共享组件，它为 [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>)、[GitHub Copilot 应用程序](<https://github.com/features/ai/github-app?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>) 和 [Copilot 代码提供动力回顾](<https://docs.github.com/copilot/concepts/agents/code-review?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>)，以及GitHub和Microsoft的各种经验。改进线束，让每个表面都受益。

![图表显示代理线束为 GitHub Copilot CLI、GitHub Copilot 应用程序、其他 IDE（如 VS Code 和 Xcode）以及使用 SDK 构建的其他 IDE 提供支持。](<https://github.blog/wp-content/uploads/2026/06/architecture-harness-surfaces.png?resize=1024%2C592>)

*GitHub Copilot 代理安全带的力量 GitHub Copilot 体验。*

工具、上下文和工作流程由线束精心编排。对于开发人员来说，工具应该是快速的、令牌高效的并且是可预测的。这就是我们设计 GitHub Copilot 代理安全带的目的。

在这篇文章中，我们将提供显示 GitHub Copilot 代理工具在各种代理软件工程任务中的效率和性能的数据。

## 我们如何迭代基准

我们通过结合公共和内部开发的基准不断评估 GitHub Copilot 代理工具的功能和效率。我们的公共基准包括行业标准，而一些内部基准则源自 GitHub 和 Microsoft 内的大型代码库。我们用现实世界的指标和在线实验来补充这一点，以确保我们了解安全带在受控环境中的性能及其对代理问题解决和任务完成的实际影响。

我们控制尽可能多的变量来评估GitHub Copilot的工具与模型提供者的工具相比的性能：使用**相同的模型**，**相同的基准任务**，在上下文窗口、推理工作、工具选择上标准化，以及MCP服务器。

下面我们报告了我们跟踪的基准子集的最新结果，涉及四个领先模型：**Claude Sonnet 4.6**、**Claude Opus 4.7**、**GPT-5.4** 和**GPT-5.5**：

|**基准**|**域名**|**用途**|
| --- | --- | --- |
|SWE-工作台已验证|500 来自开源 Python 存储库的人工验证的错误修复任务|建立了编码的行业标准基准 agents|
|SWE-bench Pro|更困难、多步骤的工程任务需要更深入的推理和更广泛的代码更改|更好地反映复杂的、真实的软件工程工作|
| SkillsBench |agent 如何有效地使用技能来解决任务|评估可扩展性、技能使用和触发能力|
| TerminalBench |Agent 基于终端的任务的性能|衡量开发人员使用的命令行工作流程的有效性|
|温希尔|Windows 容器内运行的任务的内部基准测试|验证性能在操作系统和环境中通用|

在整个过程中，我们将 **GitHub** **Copilot CLI** 与本地交付这些模型的模型供应商线束进行比较：**Claude Code** 用于 Sonnet 4.6 和 Opus 4.7，以及 **Codex CLI** 用于 GPT-5.4 和 GPT-5.5。

## 代币效率

在多个基准测试结果中保持模型和任务固定，GitHub Copilot 线束实现的任务完成率与其他模型供应商线束相当，同时在大多数配置中显示出较低的令牌消耗。

![图表显示 Copilot CLI 与使用 SWE-bench Verified、SWE-bench Pro、SkillsBench、Win-Hill 和 TerminalBench2 测试的模型供应商线束的比较。对于 Sonnet 4.6 和 Opus 4.7，Copilot CLI 在所有情况下都表现更好，使用的令牌更少。对于 GPT 5.4 和 GPT 5.5，CLI 在所有情况下都表现更好，除了 SWE- 基准验证，其中 7% 和 4% 表现更差，分别。](<https://github.blog/wp-content/uploads/2026/06/token-efficiency.png?resize=1024%2C683>)

*令牌效率：GitHub Copilot CLI 与其他模型供应商线束 *

## 任务解决

只有当工作真正完成时，代币效率才重要。

当与固定模型和基准任务一起使用时，GitHub Copilot代理线束跨这些基准的任务解决率与模型供应商线束相当。 这确保了底层模型的全部潜力可用，以及多模型灵活性、令牌效率以及内存和上下文功能。

![任务分辨率基准测试结果 Copilot CLI 与模型供应商线束相比。对于 SWE-台架验证测试， Copilot CLI Sonnet 表现更好 4.6 和作品 4.7，但更糟糕的是 GPT 5.4 和 GPT 5.5。对于 SWE-长凳临， Copilot CLI 仅在 Sonnet 中表现稍差一些 4.6，并且对于其他模型表现更好。对于 SkillsBench, Copilot CLI Sonnet 的表现较差 4.6 和作品 4.7，但更好的是 GPT 模型。对于温希尔来说， Copilot CLI 所有模型的表现相同或更好。对于 TerminalBench 2, Copilot CLI 在十四行诗中表现更好 4.6 和作品 4.7，等于 GPT 5.5，更糟糕的是 GPT 5.4.](<https://github.blog/wp-content/uploads/2026/06/task-resolution.png?resize=1024%2C786>)

*任务解析：GitHub Copilot CLI 与 * 模型供应商线束

这些结果反映了有效的同等性，因为由于模型的随机性质，任一方向的差异都在方差之内，从而使跨线束性能达到同等水平。

## TerminalBench：代币效率、任务完成度和方差

为了不断改进 GitHub Copilot 代理在任务完成和代币效率方面的利用，我们定期跨基准进行彻底的分析。下面是对TerminalBench2.0进行方差分析的示例，它不仅突出了GitHub Copilot在任务完成和代币效率方面的优势，而且还显示了此类基准测试固有的运行间方差。

![该图显示了每个任务的平均成本与解决率的比较。 Copilot CLI 的性能等于或优于模型供应商线束。](<https://github.blog/wp-content/uploads/2026/06/resolution-rate-vs-cost-variance-noarrow.png?resize=1024%2C676>)

*解决率与每个任务的成本。 向左上方更好：解决更多问题，花费更少。*

每个标记都是 TerminalBench 2.0 上的一个 agent 和模型配置，纵轴为分辨率，横轴为每个任务的美元成本。每个点周围的阴影椭圆显示 ±1σ 运行间差异，显示每个配置在运行之间的差异程度。

有三件事很突出：

1. 在我们评估的配置**中，**GitHub Copilot的代理安全带在任务完成度和每项任务成本方面与其他agents相当或领先。对于几乎每个模型，紫色 (Copilot) 标记及其同型号竞争对手都位于两个轴上重叠的椭圆内 - 差异位于运行间方差内。 Copilot 在完成度或成本方面永远不会低于竞争对手。
2. **运行间变异性**。我们运行每个 agent 模型组合至少五次。椭圆标记了这些运行的 1σ 分布；图表中的椭圆越窄，意味着结果的可重复性越高，而椭圆越宽，则表明每次运行的结果在成本和任务完成情况上的波动都更大。
3. **GitHub Copilot 型号选择的好处：** 该图表显示了真正的权衡：GPT 型号（左）提供最佳价值：以最低的成本实现强大的分辨率。 Claude Opus（右上）以溢价达到最高分辨率。 GitHub Copilot 将两者都放在桌面上，因此您可以选择每个任务的效率或峰值质量。

## 一根线束，多种型号

GitHub Copilot 代理线束支持 **20+ 跨 GPT、Claude、Gemini 和 MAI 系列的前沿模型 **，并为开源和本地模型带来您自己的密钥。您可以根据每个任务的能力和成本情况选择合适的模型，或者让[**自动模型选择**](<https://docs.github.com/en/copilot/concepts/models/auto-model-selection>)为您选择，平衡任务意图和模型健康状况以优化代币效率。

多模型架构还解锁了模型供应商线束根本无法提供的线束级功能。例如，[**Rubber Duck**](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-combines-model-families-for-a-second-opinion/>) 使用跨模型系列批评，其中一个模型审查另一个模型的工作，以改善超出任何单一模型单独产生的结果。

## 结论

基准只是众多信号之一。我们不断努力提高基准、实际使用指标和在线实验的质量，同时努力有效地充分利用每个代币。

GitHub Copilot 提供与领先模型供应商工具同等的任务分辨率，同时在多种配置中使用更少的令牌，而不会通过其多模型架构将您锁定在单个模型中。对于开发人员来说，这意味着您可以以较低的代币成本完成类似的任务，同时仍然选择最适合您的任务的模型。

## 自己尝试一下

使用您选择的模型尝试 GitHub Copilot，比较您每天运行的任务的方法，并了解不同模型和 agent 策略在您的环境中的执行情况。

了解更多：

* [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>)
* [GitHub Copilot 应用程序](<https://github.com/features/ai/github-app?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>)
* [GitHub Copilot SDK](<https://github.com/github/copilot-sdk?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>)

同样的代理驾驭为这些体验提供了动力。我们正在继续提高其质量、效率和灵活性。

## 方法论

为了使比较尽可能受控和可重复，我们在模型、任务和环境中使用等效设置运行每个 agent。

所有跑步都有两个小时的暂停。所有 agents 以非交互方式单轮运行，禁用网络工具，并允许使用所有工具。

**TerminalBench2分析**：为agents启用默认设置，推理工作设置为中等（例如，为Claude Code和Copilot启用工具搜索CLI使用github-mcp-server）。 Codex 和 Claude Code 使用直接 Anthropic 和 OpenAI 端点。为了确保结果完整可靠，任何丢失的数据或与基础设施相关的故障都会重新运行，直到所有 89 TerminalBench2 任务产生结果。模型生成的误差被保留，并且不被排除在分析之外。每个模型都经过五次独立运行的评估，并且 Copilot 在两个单独的评估批次中进行了测试，以便与 Claude Code 和 Codex 进行比较。

**所有基准测试**：所有agent模型对均标准化为相同的上下文窗口大小、相同的提示标记限制、推理工作（中等）和设置 - 无工具搜索，无MCP服务器。保留线束的默认内置工具。所有 agents 中都排除了与基础设施相关的异常和网络访问影响作为基准，以确保公平比较。为了减少运行间差异对较小基准（<100实例）的影响，进行了五次独立运行，并报告了最佳得分的运行。所有指标均以 pass@1 形式呈现。这些标准化意味着结果与公共基准提交不同，后者通常使用更高的推理工作和其他调整设置。

---

## 标签：

* [代理 AI](<https://github.blog/tag/agentic-ai/>)
* [基准测试](<https://github.blog/tag/benchmarking/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## 撰写者

![芝巴尼巴萨瓦](<https://avatars.githubusercontent.com/u/26466942?v=4&s=200>)

### [芝巴尼巴萨瓦](<https://github.blog/author/shibbas/>)

[@shibbas](<https://github.com/shibbas>)

CodeAI 和 GitHub Copilot 编码 Agents 的首席软件工程师

![卡洛斯·卡斯特罗](<https://avatars.githubusercontent.com/u/21318528?v=4&s=200>)

### [卡洛斯·卡斯特罗](<https://github.blog/author/carlosscastro/>)

[@carlosscastro](<https://github.com/carlosscastro>)

GitHub Copilot 合伙人架构师


## 原文对照

<details>
<summary>展开完整原文</summary>

# Evaluating performance and efficiency of the GitHub Copilot agentic harness across models and tasks

While the model provides the raw intelligence, the harness shapes how effectively that intelligence is applied. The GitHub Copilot agentic harness is a single shared component of the [GitHub Copilot SDK](<https://github.com/github/copilot-sdk?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>), which powers the [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>), [GitHub Copilot app](<https://github.com/features/ai/github-app?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>), and [Copilot code review](<https://docs.github.com/copilot/concepts/agents/code-review?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>), along with a wide variety of experiences across GitHub and Microsoft. Improve the harness, and every surface benefits.

![Diagram showing the agentic harness powers the GitHub Copilot CLI, the GitHub Copilot app, other IDEs like VS Code and Xcode, and others built with the SDK.](<https://github.blog/wp-content/uploads/2026/06/architecture-harness-surfaces.png?resize=1024%2C592>)

*The GitHub Copilot agentic harness powers GitHub Copilot experiences.*

The tools, context, and workflow are orchestrated by the harness. A harness should be fast, token-efficient, and predictable for developers. That’s what we designed GitHub Copilot’s agentic harness to do.

In this post, we’ll present data showing the efficiency and performance of the GitHub Copilot agentic harness across a wide range of agentic software engineering tasks.

## How we iterate with benchmarks

We continuously evaluate the capability and efficiency of the GitHub Copilot agentic harness through a combination of public and internally developed benchmarks. Our public benchmarks include industry standards, while several internal benchmarks are derived from large codebases inside GitHub and Microsoft. We complement this with real-world metrics and online experiments to ensure we understand the harness’s performance in controlled environments and its practical impact on agentic problem solving and task completion.

We control as many variables as possible to evaluate the performance of GitHub Copilot’s harness compared to the model provider’s harness: use the **same model**, the **same benchmark task**, normalized on context window, reasoning efforts, tool selection, and MCP servers.

Below we report our latest results for a subset of the benchmarks we track, across four leading models: **Claude Sonnet 4.6**, **Claude Opus 4.7**, **GPT‑5.4**, and **GPT‑5.5**:

| **Benchmark** | **Domain** | **Purpose** |
| --- | --- | --- |
| SWE-bench Verified | 500 human-validated bug-fix tasks from open-source Python repositories | Established industry-standard benchmark for coding agents |
| SWE-bench Pro | More difficult, multi-step engineering tasks requiring deeper reasoning and broader code changes | Better reflects complex, real-world software engineering work |
| SkillsBench | How effectively an agent uses skills to solve tasks | Evaluates extensibility and skill use and triggering capabilities |
| TerminalBench | Agent performance on terminal-based tasks | Measures effectiveness in command-line workflows used by developers |
| Win-Hill | Internal benchmark for tasks running inside Windows containers | Validates that performance generalizes across operating systems and environments |

Throughout, we compare **GitHub** **Copilot CLI** against the model-vendor harnesses that ship those models natively: **Claude Code** for Sonnet 4.6 and Opus 4.7, and **Codex CLI** for GPT‑5.4 and GPT‑5.5.

## Token efficiency

Holding the model and task fixed, across multiple benchmark results, the GitHub Copilot harness achieves task completion rates on par with other model-vendor harnesses, while showing lower token consumption across most configurations.

![Chart showing Copilot CLI versus model-vendor harnesses using SWE-bench Verified, SWE-bench Pro, SkillsBench, Win-Hill, and TerminalBench2 tests. For Sonnet 4.6 and Opus 4.7, Copilot CLI performed better in all cases, using fewer tokens. For GPT 5.4 and GPT 5.5, CLI performed better in all cases except SWE-bench Verified, where it did 7% and 4% worse, respectively.](<https://github.blog/wp-content/uploads/2026/06/token-efficiency.png?resize=1024%2C683>)

*Token efficiency: GitHub Copilot CLI vs. other model-vendor harnesses*

## Task resolution

Token efficiency only matters if the work actually gets done.

Task resolution rates for the GitHub Copilot agentic harness across these benchmarks are on-par with model-vendor harnesses when used with a fixed model and benchmark task. This ensures that the full potential of the underlying model is available, along with multi-model flexibility, token efficiency, and memory and context capabilities.

![Task resolution benchmarking test results for Copilot CLI versus model-vendor harnesses. For SWE-bench Verified tests, Copilot CLI performed better with Sonnet 4.6 and Opus 4.7, but worse with GPT 5.4 and GPT 5.5. For SWE-bench Pro, Copilot CLI only performed slightly worse with Sonnet 4.6, and performed better for other models. For SkillsBench, Copilot CLI performed worse for Sonnet 4.6 and Opus 4.7, but better for GPT models. For Win-Hill, Copilot CLI performed equal or better for all models. For TerminalBench 2, Copilot CLI performed better for Sonnet 4.6 and Opus 4.7, equal for GPT 5.5, and worse for GPT 5.4.](<https://github.blog/wp-content/uploads/2026/06/task-resolution.png?resize=1024%2C786>)

*Task resolution: GitHub Copilot CLI vs. the*model-vendor harnesses

These results reflect effective parity, since the differences in either direction are within the variance due to the stochastic nature of the models, making the cross-harness performance on-par.

## TerminalBench: Token efficiency, task completion, and variance

To continuously improve the GitHub Copilot agentic harness on task completion and token efficiency, we regularly perform thorough analyses across benchmarks. Below is an example of variance analysis on TerminalBench 2.0, which not only highlights GitHub Copilot’s strength on task completion and token efficiency, but also shows the run-to-run variance intrinsic to this kind of benchmark.

![A diagram showing mean cost per task compared to the resolution rate. Copilot CLI performs equal to or better than model-vendor harnesses.](<https://github.blog/wp-content/uploads/2026/06/resolution-rate-vs-cost-variance-noarrow.png?resize=1024%2C676>)

*Resolution rate vs. cost per task. Up and to the left is better: solve more, spend less.*

Every marker is one agent-and-model configuration on TerminalBench 2.0, with resolution rate on the vertical axis and dollar cost per task on the horizontal axis. The shaded ellipse around each point shows the ±1σ run-to-run spread, displaying how much each configuration varies between runs.

Three things stand out:

1. **GitHub Copilot’s agentic harness is on par with or ahead of other agents on task completion and cost per task across the configurations we evaluated**. Purple (Copilot) markers and their same-model competitors sit within overlapping ellipses on both axes for nearly every model—the differences are inside run-to-run variance. Copilot is never below a competitor on completion or to the right on cost.
2. **Run-to-run variability**. We ran each agent-model combination at least five times. The ellipse marks the 1σ spread of those runs; a tighter ellipse in the chart means more reproducible results, while a wider one shows results swinging further from run to run on both cost and task completion.
3. **The benefit of GitHub Copilot’s model choice:** The chart shows a real trade-off: GPT models (left) deliver the best value: strong resolution at the lowest cost. Claude Opus (upper right) reaches the highest resolution at a premium. GitHub Copilot puts both on the table, so you can pick efficiency or peak quality per task.

## One harness, many models

The GitHub Copilot agentic harness supports **20+ frontier models** across the GPT, Claude, Gemini, and MAI families, plus bring your own key for open‑source and local models. You can choose the right model for the capability and cost profile of each task, or let [**Auto model selection**](<https://docs.github.com/en/copilot/concepts/models/auto-model-selection>) choose for you, balancing task intent and model health to optimize token efficiency.

A multi‑model architecture also unlocks harness‑level capabilities a model-vendor harness simply can’t offer. [**Rubber Duck**](<https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-combines-model-families-for-a-second-opinion/>), for example, uses cross‑model‑family critique, where one model reviews another’s work to improve outcomes beyond what any single model produces alone.

## Conclusion

Benchmarks are just one signal among several. We are constantly working to improve quality across benchmarks, real-world usage metrics, and online experiments, while pushing to efficiently make the most out of every token.

GitHub Copilot delivers task‑resolution on par with leading model-vendor harnesses while using fewer tokens across several configurations, without locking you into a single model through its multi‑model architecture. For developers, this means you can get comparable task completion with lower token cost, while still choosing the model that best fits your task.

## Try it yourself

Try GitHub Copilot with the model of your choice, compare approaches on the tasks you run every day, and see how different models and agent strategies perform in your environment.

Learn more about:

* [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>)
* [GitHub Copilot app](<https://github.com/features/ai/github-app?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>)
* [GitHub Copilot SDK](<https://github.com/github/copilot-sdk?utm_source=blog-benchmarking-1&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>)

The same agentic harness powers these experience. We’re continuing to improve its quality, efficiency, and flexibility.

## Methodology

To make the comparison as controlled and reproducible as possible, we run each agent with equivalent settings across models, tasks, and environments.

All runs have a two-hour timeout. All agents run non-interactively single-turn, with web-tools disabled, and all tools allowed.

**TerminalBench2 analysis**: Default settings enabled for agents with reasoning effort set to medium (e.g. tool search is enabled for Claude Code and Copilot CLI uses github-mcp-server). Codex and Claude Code use direct Anthropic and OpenAI endpoints. To ensure complete and reliable results, any missing data or infrastructure-related failures were re-run until all 89 TerminalBench2 tasks produced results. Model-generated errors were retained and not excluded from the analysis. Each model was evaluated across five independent runs, and Copilot was tested in two separate evaluation batches to enable comparison with Claude Code and Codex.

**All benchmarks**: All agent model pairs normalized to same context window size, same prompt token limits, reasoning effort (medium) and settings—no tool search, no MCP servers. Keeping the harness’s default built-in tools. Infrastructure-related anomalies and network-access effects are excluded across all agents for a benchmark to ensure fair comparisons. To reduce the impact of run-to-run variability on smaller benchmarks (<100 instances), five independent runs were conducted, and the best scored run is reported. All metrics are presented as pass@1. These normalizations mean results differ from public benchmark submissions, which typically use higher reasoning effort and other tuned settings.

---

## Tags:

* [agentic AI](<https://github.blog/tag/agentic-ai/>)
* [benchmarking](<https://github.blog/tag/benchmarking/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## Written by

![Shibani Basava](<https://avatars.githubusercontent.com/u/26466942?v=4&s=200>)

### [Shibani Basava](<https://github.blog/author/shibbas/>)

[@shibbas](<https://github.com/shibbas>)

Principal Software Engineer in CodeAI and GitHub Copilot Coding Agents

![Carlos Castro](<https://avatars.githubusercontent.com/u/21318528?v=4&s=200>)

### [Carlos Castro](<https://github.blog/author/carlosscastro/>)

[@carlosscastro](<https://github.com/carlosscastro>)

Partner Architect at GitHub Copilot


</details>
