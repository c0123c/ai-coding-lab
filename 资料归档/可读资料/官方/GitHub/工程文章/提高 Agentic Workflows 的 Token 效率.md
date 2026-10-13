# 提高 Agentic Workflows 的 Token 效率

介绍用日志追踪用量、让工作流辅助分析自身、去除无用 MCP 工具和改用 CLI 的实验，讨论衡量实际效率提升的难点。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-05-07](<https://github.blog/ai-and-ml/github-copilot/improving-token-efficiency-in-github-agentic-workflows/>)；更新：[2026-05-13](<https://github.blog/ai-and-ml/github-copilot/improving-token-efficiency-in-github-agentic-workflows/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/improving-token-efficiency-in-github-agentic-workflows/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/提高 Agentic Workflows 的 Token 效率.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 提高 GitHub 代理工作流程中的令牌效率

GitHub Agentic Workflows 就像一支街道清扫工团队，负责清理存储库中的小混乱。这些团队显着提高了回购协议的卫生和质量，但与所有代理工作一样，成本是开发人员日益关注的问题。而且，由于像代理工作流程这样的 CI 作业是自动计划和触发的，因此成本可能会在视图之外累积。

值得庆幸的是，使自动化更高效比对交互式桌面会话进行同样的操作更容易。开发人员会话期间完成的工作可能很难预测，但代理工作流的工作在 YAML 中完全指定，并重复每次执行。

因为我们在自己的 GitHub 存储库中维护和使用 GitHub 代理工作流程，所以我们和用户一样担心令牌效率。这就是为什么在四月份 2026，我们开始系统地优化我们每天依赖的许多工作流程的令牌使用。这篇文章描述了我们检测的内容、我们应用的优化以及我们的初步结果。

## 记录令牌使用情况

我们依赖存储库中的数百个代理工作流程进行维护和 CI。所有工作流程都作为针对实际 API 速率限制的 GitHub 操作运行。我们一边飞行一边建造飞机，一边飞行一边燃烧喷气燃料。

在优化代币消耗之前，我们需要知道代币是如何消耗的。我们面临的第一个挑战是每个 agent 框架（Claude CLI、Copilot CLI、Codex CLI）以不同的格式发出日志，并且历史运行的使用数据可能不完整。值得庆幸的是，代理工作流安全架构使用 API 代理来防止 agents 直接访问身份验证凭据。该代理为我们提供了一种以单一标准化格式捕获所有运行中令牌使用情况的方法，无论 agent 框架如何。

现在，每个工作流程都会输出一个 `token-usage.jsonl` 工件，每个 API 调用包含一条记录，其中包含输入令牌、输出令牌、缓存读取令牌、缓存写入令牌、模型、提供程序和时间戳。将这些数据与工作流程日志的其余部分相结合，可以提供代币通常如何使用的历史视图，并允许我们针对未来的运行进行优化。

## 工作流程优化工作流程

有了手中的代币数据，我们构建了两个日常优化工作流程。

**每日令牌使用审核器**从最近的工作流程运行中读取令牌使用工件，按工作流程汇总消耗，并发布结构化报告。它的工作是标记最近使用量显着增加的任何工作流程，显示最昂贵的工作流程，并记录异常运行（例如，通常需要四轮LLM完成的工作流程需要18）。

当审核员标记工作流程时，每日令牌优化器会查看工作流程的源和最近的日志，以创建一个 GitHub 问题，描述具体的低效率问题并提出具体的优化建议。优化器发现了许多我们本来会错过的低效率问题。

当然，审计器和优化器本身就是代理工作流程，它们的代币使用情况也会出现在每日报告中，以创建一个小的良性循环。

## 消除未使用的 MCP 工具

根据我们最初的审核器和优化器结果，最常见的低效率问题是未使用的 MCP 工具注册。

由于 LLM API 是无状态的，agent 运行时通常在每个请求中包含 MCP 工具函数名称和 JSON 架构。实际上，这意味着全套工具可以成为每个呼叫上下文的一部分。对于具有 40 工具的 GitHub MCP 服务器，这可以每回合添加 10–15 KB 模式。如果agent仅使用两个工具，则剩余的38是添加到每个请求的纯粹开销。

工作流程作者自然会从完整的工具集开始，因为这是阻力最小的路径，并且 agent 可以找出它需要哪些工具。但随着时间的推移，大多数工作流程都依赖于一组有限的、稳定的工具。优化器通过对照实际工具调用交叉引用工具清单来识别此模式，并建议从配置中删除未使用的工具。

在我们的冒烟测试工作流程中，从 MCP 配置中删除未使用的工具，将每次调用上下文大小减少了 8–12 KB，每次运行节省了数千个令牌，而行为没有变化。

## 将 GitHub MCP 替换为 GitHub CLI

删除未使用的 MCP 工具是一个相对简单的胜利。一个更大的结构机会是用对 GitHub CLI 的调用来替换对数据获取操作的 GitHub MCP 调用，例如检索拉取请求差异、文件内容和审查评论。

此更改不仅仅减少了未使用工具的开销，因为 MCP 工具调用是除数据检索之外的推理步骤。 agent 必须决定调用该工具，制定其参数，并接收其输出作为上下文的一部分。这是一个完整的往返 LLM API 调用，消耗工具使用 JSON 模式、参数块和响应的令牌。相比之下，调用 `gh pr diff` 是对 GitHub 的 REST API 的确定性 HTTP 请求，不涉及 LLM。

我们为此迁移使用了两种策略：

**代理前数据下载**。对于 agent 始终需要的数据（例如拉取请求差异或已更改文件列表），我们在工作流程中添加了设置步骤，在 agent 启动之前运行 `gh` 命令并将结果写入工作区文件。 agent 读取这些文件而不是进行 MCP 调用。这消除了工具调用开销，并允许 agent 利用其在 bash 脚本方面的广泛培训来有效地处理数据。

**在-agent CLI代理替换**。如果 agent 决定在运行时获取什么，则无法进行预下载。在这些情况下，我们依靠轻量级透明的 HTTP 代理将 CLI 流量路由到 GitHub 的 API 服务器，而不会将身份验证令牌暴露给 agent。 agent 运行 `gh pr view –json` 并获取结构化数据，就像用户从终端进行操作一样。这可以减少令牌的使用，而不会影响我们对 agent 的零秘密安全要求。

这些技术共同将大部分 GitHub 数据获取移出了 LLM 推理循环。

## 衡量效率提升并不容易

一旦我们开始优化工作流程，我们就遇到了一个更微妙的问题：您如何知道更改是否使事情变得更加高效，或者只是使工作流程完成更少（甚至更糟）的工作？

存在三个混杂因素。

**并非所有代币都是一样的**。在 Claude Haiku 和 Claude Sonnet 上运行相同的工作流程会产生类似的令牌计数，但成本却截然不同。 Haiku 的每个令牌的成本大约比 Sonnet 低 4×，因此切换模型的工作流程在原始令牌计数方面似乎没有变化，但代表了显着的成本降低。为了解决这个问题，我们使用有效代币 (ET) 指标，将模型乘数应用于每种代币类型：

```
ET = m × (1.0 × I + 0.1 × C + 4.0 × O)
```

其中 *m* 是模型成本乘数（Haiku = 0.25×，Sonnet = 1.0×，Opus = 5.0×），*I* 是新处理的输入令牌，*C*是缓存读取令牌，*O*是输出令牌。输出代币具有 4× 权重，因为它们是所有主要提供商中最昂贵的代币类型。缓存读取令牌仅携带 0.1× 权重，因为它们是从缓存提供的，而成本只是新输入的一小部分。此公式使跨型号层的消耗标准化，因此，无论使用哪种型号，10% ET 减少都意味着真正的 10% 成本降低。

**工作负载是一个实时存储库。**据我们所知，没有可用于优化令牌使用的代理工作流基准。当我们开始查看工作流程的令牌使用情况时，我们发现工作流程在一次运行中将处理五行修复，而在下一次运行中它将处理 200 行拉取请求。第一次运行自然使用较少的代币，但差异并不是由于效率的突然变化。原始令牌计数可能会将工作负载变化与效率波动混淆。我们尝试通过跟踪 LLM API 调用计数和代币计数来标准化这一点；恒定的 LLM 每次运行轮数和每次调用下降的令牌表明真正的效率提高。两者同时出现可能表明正在完成的工作较少。

**质量会改变吗？**了解输出质量是最难考虑的。运行更受限工作流程的较轻模型可能会产生质量较低的输出。我们查看了流程级信号，例如每个 LLM 调用的输出令牌、每次运行的轮数以及工具调用完成率以估算质量。对于我们优化的 Smoke Copilot 工作流程，即使代币消耗下降，所有三个在优化期间仍保持稳定。每次运行（优化前后），工作流程大约需要五轮 LLM 即可完成。当然，这些是过程信号，而不是结果信号。我们无法直接观察质量是否提高、下降或稳定，因为不存在真实的“正确性”。衡量每个正确工作单元的代币需要额外的仪器和思考。

## 初步结果

在 `gh-aw` 和 `gh-aw-firewall` 存储库中的十几个生产工作流程中部署审核器和优化器后，我们在每次运行优化和计算之前和之后下载了运行的令牌使用工件 ET。其中九个 12 工作流程收到了优化器建议的更改。我们仅包含在优化前和优化后期间至少运行八次的工作流程的结果。这些是：自动分类问题、每日编译器质量、社区归因、安全防护和烟雾 Claude。

![图表显示自动分类问题、每日编译器质量、社区归因、安全防护和烟雾等方面的代币节省 Claude。](<https://github.blog/wp-content/uploads/2026/05/graph.png?resize=1024%2C504>)

自动分类问题显示 109 修复后运行期间 62% 明显持续减少。每日编译器质量显示比 12 修复后运行有 19% 改进，每日社区归因显示比八次修复后运行有 37% 改进。在 `gh-aw-firewall` 存储库中，Security Guard 审核每个拉取请求以进行安全敏感的更改，而 Smoke Claude 是一项执行防火墙 Claude CLI 路径的集成测试，具有最多的修复后运行，并显示了 43% 和分别为59%。

运行频率与每次运行节省的费用同样重要。自动分类问题会在每个新问题上触发（平均每天运行 6.8，最多运行 15），而每日编译器质量每天最多运行一次。 62% 节省和 6.8 runs/day 迅速复合：在观察期内，自动分类的优化总共节省了大约 7.8 M ET（假设预优化率）。 Security Guard 和 Smoke Claude 运行更加频繁。在确定要优化的工作流程的优先级时，运行频率与每次运行的消耗同样重要。

值得注意的是，并非 agent 建议的每项优化都会转化为可衡量的 ET 节省，特别是在工作量每天都在变化的实时存储库上的短观察窗口中。例如，贡献检查工作流程经历了 ET 的 5% 增加，我们将在下面更详细地讨论它。

## 外卖

基于这些结果，我们强调了三种模式。

**许多agent回合都是确定性数据收集**。自动分类问题显示了 `gh-aw`（在 109 修复后运行中的−62%）中最强的持续改进，因为优化消除了结构性低效率：许多 agent 轮次都花在不需要推理的读取上，例如获取问题元数据和扫描标签。在 agent 开始之前将这些读取移动到预代理 CLI 步骤中，将它们完全从 LLM 推理循环中删除。同样的模式推动了 Security Guard 的 -43% 减少 `gh-aw-firewall`：对于不涉及安全敏感文件的拉取请求，相关性门现在完全跳过 LLM。最便宜的 LLM 电话是您不拨打的电话。

贡献检查说明了一个混杂因素：输入令牌的 82–83% 是缓存读取（数据收集），但平均 ET 增加了 5%。这是由于工作负载转移而不是优化失败：在运行的预优化期间 41% 处理了小型拉取请求 (ET < 100K) 和 39% 处理了大型拉取请求 (ET > 300K)。优化后时期恰逢开发活动爆发，工作流程处理 9% 小型拉取请求和 65% 大型拉取请求。随着 agent 审查更大的差异，在 ET 公式中带有 4× 权重的输出令牌上升 14%。这种优化可能会提高每回合的效率，但向更重的工作负载的转变掩盖了总体数量的增长。

**未使用的工具携带起来很昂贵。**在排除的`gh-aw`工作流程中，术语表维护者是一个有启发性的案例。单个工具 - `search_repositories` - 在一次运行中被调用 342 次，占所有工具调用的 58%，尽管对于仅扫描本地文件更改的工作流程来说完全没有必要。优化器建议将其从工具集中删除。在 `gh-aw-firewall` 中，Smoke Claude 的 -59% 减少部分是由积极的 MCP 工具修剪与模型层切换到俳句相结合所驱动的。每日社区归因工作流程说明了此方法的局限性：它配置了八个 GitHub MCP 工具，并在整个运行过程中对其中任何一个工具进行了零调用，但删除它们并没有减少 ET。工具清单只是此工作流程整体上下文的一小部分。

**单个错误配置的规则可能会导致失控循环**。此外，在排除的工作流程中，每日语法错误质量是优化前项目中最高的 - ET 工作流程。根本原因是单行错误配置：工作流程将测试文件复制到 /tmp/，然后调用 `gh aw compile`\*，但沙箱的 bash 白名单只允许相对路径 glob 模式。每次编译尝试都被阻止。由于无法使用所需的工具，agent 陷入了 64 转向回退循环，在该循环中，它手动读取源代码以重建编译器将告诉它的内容。对允许的 bash 模式的一项修复消除了循环。我们没有足够的基线运行来精确量化改进，但病理学是清楚的，修复方法也很明确。

## 接下来是什么？

我们用来优化工作流程的工具，包括 API- 级别可观察性、自动审核工作流程、MCP 工具修剪和 CLI 替换，现在都可以在 GitHub 代理工作流程框架中使用。另一个即将到来的优化是使用更小、更便宜的模型将整体 agents 重构为子代理团队。

下一步是从工作流程级优化转向系统级优化。工作流运行实际上并不是一个扁平的 API 调用序列。这是一系列的情节：短暂的工作阶段，例如收集上下文、阅读工件、失败后重试或综合最终答案。一旦你能清楚地看到这些情节，你就可以提出更好的问题。哪一集实际上导致了昂贵的运行？哪些情节主要是重复工作、受阻工作或失败工作？哪些步骤应该完全不再是代理性的，而成为确定性的前期步骤？

同样的逻辑也适用于投资组合层面。存储库不会孤立地运行一个工作流程。他们运行一系列代理自动化，这些自动化通常会触发相同的事件，检查相同的差异和日志，并产生相邻的判断。这意味着成本不仅是单个工作流程的属性，而且是整个产品组合的重叠属性。我们想要的下一个分析是组合级别的分析：其中工作流程重复读取，应整合多个工作流程，以及应缓存共享中间工件而不是每次运行时重新发现。

这些开放性问题确实很难。衡量产出仍然需要结果仪器，而这对于代理 CI 工作流程来说尚不存在，并且了解情节和投资组合效率需要比当今大多数系统收集的更丰富的谱系数据。但这才是重要的方向。代理级可观察性和优化器工作流程已经改变了我们开发和部署新代理自动化的方式。我们从第一天开始就添加了令牌监控，而不是稍后对其进行改造，并且我们越来越多地考虑整个自动化团队中可避免的工作，而不仅仅是昂贵的孤立运行。

如果您在 CI 中运行代理工作流程，并且想知道您是否花费了超出需要的费用，那么第一步与我们的相同：添加 API 代理，打开日志记录，让数据告诉您去哪里查找。

如果您想添加此处提到的工作流程，只需使用 [`gh-aw` CLI](<https://github.github.com/gh-aw/setup/cli/#installation>) 将它们放入您的存储库中即可：

```
gh extensions install github/gh-aw
gh aw add githubnext/agentic-ops/copilot-token-audit githubnext/agentic-ops/copilot-token-optimizer
```

将它们与您现有的 CI 一起运行将使您能够立即了解使用情况，并有助于随着时间的推移不断优化您的工作流程。

我们很想听听其他人如何解决这个问题。在 [ 社区讨论 ](<https://github.com/orgs/community/discussions/186451>) 中分享您的想法，或加入 [GitHub Next Discord](<https://gh.io/next-discord>) 的 #agentic-workflows 频道。

探索 [GitHub 代理工作流程存储库 >](<http://github.com/github/gh-aw>)

*编者注：这篇文章于五月更新13，2026，以澄清一些折扣率数字。*

---

## 标签：

* [代理工作流程](<https://github.blog/tag/agentic-workflows/>)

## 撰写者

![兰登·考克斯](<https://avatars.githubusercontent.com/u/15877973?v=4&s=200>)

### [兰登·考克斯](<https://github.blog/author/lpcox/>)

[@lpcox](<https://github.com/lpcox>)

高级首席研究员，Microsoft 研究

![玛拉·基弗](<https://avatars.githubusercontent.com/u/8320933?v=4&s=200>)

### [玛拉·基弗](<https://github.blog/author/mnkiefer/>)

[@mnkiefer](<https://github.com/mnkiefer>)

高级研究工程师


## 原文对照

<details>
<summary>展开完整原文</summary>

# Improving token efficiency in GitHub Agentic Workflows

GitHub Agentic Workflows is like a team of street sweepers that clean up little messes in your repo. These teams significantly improve repo hygiene and quality, but as with all agentic work, cost is a growing concern for developers. And because CI jobs like agentic workflows are automatically scheduled and triggered, costs can accumulate out of view.

Thankfully, making automations more efficient is easier than doing the same for interactive desktop sessions. Work done during a developer session can be hard to predict, but agentic workflows’ work is fully specified in YAML and repeats every execution.

Because we maintain and use GitHub Agentic Workflows in our own GitHub repositories, we worry about token efficiency as much as our users. That is why in April 2026, we began to systematically optimize the token usage of many of the workflows that we rely on every day. This post describes what we instrumented, the optimizations we applied, and our preliminary results.

## Logging token usage

We rely on hundreds of agentic workflows in our repos for maintenance and CI. All workflows run as GitHub Actions against real API rate limits. We are building the plane as we fly it and burning jet fuel as we go.

Before we could optimize our token consumption, we needed to know how tokens were consumed. The first challenge we faced was that each agent framework (Claude CLI, Copilot CLI, Codex CLI) emitted logs in a different format, and usage data could be incomplete for historical runs. Thankfully, the agentic-workflows security architecture uses an API proxy to prevent agents from directly accessing authentication credentials. This proxy gave us a way to capture token usage across all runs in a single normalized format, regardless of agent framework.

Every workflow now outputs a `token-usage.jsonl` artifact with one record per API call that contains input tokens, output tokens, cache-read tokens, cache-write tokens, model, provider, and timestamps. Combining this data with the rest of the workflow’s logs gave a historical view of how tokens were typically spent and allowed us to optimize for future runs.

## Workflows optimizing workflows

With token data in hand, we built two daily optimization workflows.

A **Daily Token Usage Auditor** reads token usage artifacts from recent workflow runs, aggregates consumption by workflow, and posts a structured report. Its job is to flag any workflow that has significantly increased its recent usage, surface the most expensive workflows, and take note of anomalous runs (e.g., a workflow that normally completes in four LLM turns taking 18).

When an Auditor flags a workflow, a Daily Token Optimizer looks at the workflow’s source and recent logs to create a GitHub Issue describing concrete inefficiencies and proposing specific optimization. The Optimizer has found many inefficiencies that we would have otherwise missed.

Of course, the Auditor and Optimizer are agentic workflows themselves, and their token usages also appear in daily reports to create a small virtuous cycle.

## Eliminating unused MCP tools

Based on our initial Auditor and Optimizer results, the most common inefficiency is unused MCP tool registrations.

Because LLM APIs are stateless, agent runtimes typically include the MCP tool function names and JSON schemas with each request. In practice, this means the full set of tools can become part of every call’s context. For a GitHub MCP server with 40 tools, this can add 10–15 KB of schema per turn. If the agent only uses two tools, the remaining 38 are pure overhead added to every request.

Workflow authors naturally start with a full tool-set since it is the path of least resistance, and the agent can figure out which tools it needs. But as time goes on, most workflows rely on a narrow, stable set of tools. The Optimizer identifies this pattern by cross-referencing tool manifests against actual tool calls and recommends pruning unused tools from the configuration.

In our smoke-test workflows, removing unused tools from the MCP configuration reduced per-call context size by 8–12 KB, saving several thousand tokens per run with no change in behavior.

## Replacing GitHub MCP with GitHub CLI

Removing unused MCP tools is a relatively simple win. A larger structural opportunity was replacing GitHub MCP calls for data-fetching operations like retrieving pull request diffs, file contents, and review comments with calls to the GitHub CLI.

This change did more than reduce the overhead of unused tools because an MCP tool call is a reasoning step in addition to data retrieval. The agent must decide to call the tool, formulate its arguments, and receive its output as part of the context. That’s a full round-trip LLM API call, consuming tokens for the tool-use JSON schema, the argument block, and the response. Calling `gh pr diff`, by contrast, is a deterministic HTTP request to GitHub’s REST API with no LLM involvement.

We used two strategies for this migration:

**Pre-agentic data downloads**. For data that an agent will always need like a pull request diff or the list of changed files, we added setup steps in the workflow that run `gh` commands before the agent starts and writes the results to workspace files. The agent reads those files instead of making MCP calls. This eliminates tool-call overhead and allows the agent to take advantage of its extensive training in bash scripting to efficiently process the data.

**In-agent CLI proxy substitution**. Pre-downloading isn’t possible in cases where the agent determines what to fetch at runtime. In these cases we rely on a lightweight transparent HTTP proxy that routes CLI traffic to GitHub’s API servers without exposing an authentication token to the agent. The agent runs `gh pr view –json` and gets structured data back, just as a user would from a terminal. This reduces token usage without compromising our zero-secrets security requirement for the agent.

Together, these techniques move the majority of GitHub data-fetching out of the LLM reasoning loop.

## Measuring efficiency gains is not easy

Once we began to optimize our workflows, we ran into a more nuanced problem: how do you know whether a change made things more efficient, or just made the workflow do less (and perhaps worse) work?

There are three confounding factors.

**Not all tokens are created equal**. Running the same workflow on Claude Haiku versus Claude Sonnet produces similar token counts but cost very differently. Haiku costs roughly 4× less per token than Sonnet, so a workflow that switches models appears unchanged in raw token count but represents a significant cost reduction. To account for this, we use an Effective Tokens (ET) metric that applies model multipliers to each token type:

```
ET = m × (1.0 × I + 0.1 × C + 4.0 × O)
```

where *m* is a model cost multiplier (Haiku = 0.25×, Sonnet = 1.0×, Opus = 5.0×), *I* is newly-processed input tokens, *C* is cache-read tokens, and *O* is output tokens. Output tokens carry 4× weight because they are the most expensive token type across all major providers. Cache-read tokens carry only 0.1× weight because they are served from cache at a fraction of the cost of fresh input. This formula normalizes consumption across model tiers so that a 10% ET reduction means a genuine 10% cost reduction regardless of which model is in use.

**The workload is a live repository.** As far as we know, there is no agentic-workflow benchmark that we can use to optimize our token usage. When we began looking at token usage by our workflows, we found that in one run a workflow would handle a five-line fix, and in the next run it would handle a 200-line pull request. The first run naturally uses fewer tokens, but the difference is not due to a sudden change in efficiency. Raw token counts can confuse workload variation with fluctuations in efficiency. We try to normalize this by tracking LLM API call counts alongside token counts; constant LLM turns-per-run and falling tokens-per-call indicate genuine efficiency improvement. Both falling together may indicate that less work is being done.

**Does quality change?** Understanding output quality is the hardest consideration. A lighter model running a more constrained workflow might produce lower-quality output. We looked at the process-level signals like output tokens per LLM call, turn counts per run, and tool-call completion rates to approximate quality. For our optimized Smoke Copilot workflow, all three remained stable across the optimization period even as token consumption fell. The workflow completes in roughly five LLM turns every run, before and after the optimizations. Of course, these are process signals, not outcome signals. We cannot directly observe whether the quality improved, degraded, or was stable, because there is no ground-truth “correctness.” Measuring tokens-per-unit-of-correct-work requires additional instrumentation and thought.

## Initial results

After deploying the auditor and optimizer across a dozen production workflows in the `gh-aw` and `gh-aw-firewall` repos, we downloaded token-usage artifacts for runs before and after each was optimized and computed ET for each run. Nine of the 12 workflows received optimizer-recommended changes. We include results only for workflows with at least eight runs in both the pre- and post-optimization periods. These are: Auto-Triage Issues, Daily Compiler Quality, Community Attribution, Security Guard, and Smoke Claude.

![Graph showing token savings across Auto-Triage Issues, Daily Compiler Quality, Community Attribution, Security Guard, adn Smoke Claude.](<https://github.blog/wp-content/uploads/2026/05/graph.png?resize=1024%2C504>)

Auto-Triage Issues shows a clear, sustained reduction of 62% across 109 post-fix runs. Daily Compiler Quality shows 19% improvement over 12 post-fix runs, and Daily Community Attribution shows 37% improvement over eight post-fix runs. In the `gh-aw-firewall` repo, Security Guard, which audits every pull request for security-sensitive changes, and Smoke Claude an integration test that exercises the firewall’s Claude CLI path, had the most post-fix runs and show improvements of 43% and 59%, respectively.

Run frequency matters as much as per-run savings. Auto-Triage Issues fires on every new issue (averaging 6.8 runs per day with a max of 15) while Daily Compiler Quality runs at most once per day. 62% savings and 6.8 runs/day compounds quickly: over the observation period, Auto-Triage’s optimization saved roughly 7.8 M ET in aggregate, assuming the pre-optimization rate. Security Guard and Smoke Claude run even more frequently. When prioritizing which workflows to optimize, run frequency is as important as per-run consumption.

It is important to note that not every optimization that the agent recommends translates into measurable ET savings, especially over short observation windows on a live repository where workload varies day to day. For example, the Contribution Check workflow experienced a 5% increase in ET, and we will discuss it in greater detail below.

## Take aways

Based on these results, we highlight three patterns.

**Many agent turns are deterministic data-gathering**. Auto-Triage Issues shows the strongest sustained improvement in `gh-aw` (−62% across 109 post-fix runs) because the optimization eliminated structural inefficiency: many agent turns were spent on reads that required no inference, such as fetching issue metadata and scanning labels. Moving those reads into pre-agentic CLI steps before the agent starts removed them from the LLM reasoning loop entirely. The same pattern drove Security Guard’s −43% reduction in `gh-aw-firewall`: a relevance gate now skips the LLM entirely for pull requests that don’t touch security-sensitive files. The cheapest LLM call is the one you don’t make.

Contribution Check illustrates a confounding factor: 82–83% of input tokens were cache reads (data-gathering), but average ET increased 5%. This is due to a workload shift rather than optimization failure: in the pre-optimization period 41% of runs processed small pull requests (ET < 100K) and 39% processed large pull requests (ET > 300K). The post-optimization period coincided with a burst of development activity, and the workflow processed 9% small pull requests and 65% large pull requests. Output tokens, which carry a 4× weight in the ET formula, rose 14% as the agent reviewed bigger diffs. The optimization likely improved per-turn efficiency, but the shift toward heavier workloads masks that gain in the aggregate numbers.

**Unused tools are expensive to carry.** Among the excluded `gh-aw` workflows, the Glossary Maintainer is an instructive case. A single tool—`search_repositories`—was called 342 times in one run, accounting for 58% of all tool calls, despite being completely unnecessary for a workflow that only scans local file changes. Removing it from the toolset was the optimizer’s recommendation. In `gh-aw-firewall`, Smoke Claude’s −59% reduction was driven in part by aggressive MCP tool pruning combined with a model-tier switch to Haiku. The Daily Community Attribution workflow illustrates the limits of this approach: it was configured with eight GitHub MCP tools and made zero calls to any of them across an entire run, but removing them did not reduce ET. Tool manifests were a small fraction of this workflow’s overall context.

**A single misconfigured rule can cause runaway loops**. Also among the excluded workflows, Daily Syntax Error Quality was the highest-ET workflow in the project before optimization. The root cause was a one-line misconfiguration: the workflow copied test files to /tmp/ then called `gh aw compile`\*, but the sandbox’s bash allowlist only permitted relative-path glob patterns. Every compile attempt was blocked. Unable to use the tool it needed, the agent fell into a 64-turn fallback loop in which it manually read source code to reconstruct what the compiler would have told it. One fix to the allowed bash patterns eliminated the loop. We did not have enough baseline runs to precisely quantify the improvement, but the pathology was clear and the fix was unambiguous.

## What’s next?

The tools we use to optimize our workflows including API-level observability, automated auditing workflows, MCP tool pruning, and CLI substitution are all available today in the GitHub Agentic Workflows framework. Another upcoming optimization is refactoring monolithic agents into teams of subagents using smaller and cheaper models.

The next step is to move from workflow-level optimization to system-level optimization. A workflow run is not really one flat sequence of API calls. It is a chain of episodes: short phases of work like gathering context, reading artifacts, retrying after a failure, or synthesizing a final answer. Once you can see those episodes clearly, you can ask much better questions. Which episode actually caused a costly run? Which episodes are mostly repeated work, blocked work, or failed work? Which ones should stop being agentic entirely and become deterministic pre-steps?

That same logic applies at the portfolio level. Repositories do not run one workflow in isolation. They run a fleet of agentic automations that often trigger on the same events, inspect the same diffs and logs, and produce adjacent judgments. That means cost is not just a property of a single workflow, but also of overlap across the portfolio. The next analyses we want are portfolio-level ones: where workflows are duplicating reads, where several workflows should be consolidated, and where shared intermediate artifacts should be cached instead of rediscovered by each run.

Those open questions are genuinely hard. Measuring goodput still requires outcome instrumentation that does not yet exist at scale for agentic CI workflows, and understanding episode and portfolio efficiency requires richer lineage data than most systems collect today. But that is the direction that matters. The proxy-level observability and optimizer workflows have already changed how we develop and deploy new agentic automations. We add token monitoring from day one rather than retrofitting it later, and increasingly we think in terms of avoidable work across the whole automation fleet, not just expensive runs in isolation.

If you’re running agentic workflows in CI and wondering whether you’re spending more than you need to, the first step is the same as ours: add the API proxy, turn on logging, and let the data tell you where to look.

If you want to add the workflows mentioned here, you can simply drop them into your repo using the [`gh-aw` CLI](<https://github.github.com/gh-aw/setup/cli/#installation>):

```
gh extensions install github/gh-aw
gh aw add githubnext/agentic-ops/copilot-token-audit githubnext/agentic-ops/copilot-token-optimizer
```

Running them alongside your existing CI will give you immediate visibility into usage and help continuously optimize your workflows over time.

We’d love to hear how others are approaching this problem. Share your thoughts in the [community discussion](<https://github.com/orgs/community/discussions/186451>) or join the #agentic-workflows channel of the [GitHub Next Discord](<https://gh.io/next-discord>).

Explore the [GitHub Agentic Workflows repo >](<http://github.com/github/gh-aw>)

*Editor’s note: This post was updated on May 13, 2026, to clarify some reduction rates numbers.*

---

## Tags:

* [agentic workflows](<https://github.blog/tag/agentic-workflows/>)

## Written by

![Landon Cox](<https://avatars.githubusercontent.com/u/15877973?v=4&s=200>)

### [Landon Cox](<https://github.blog/author/lpcox/>)

[@lpcox](<https://github.com/lpcox>)

Senior Principal Researcher, Microsoft Research

![Mara Kiefer](<https://avatars.githubusercontent.com/u/8320933?v=4&s=200>)

### [Mara Kiefer](<https://github.blog/author/mnkiefer/>)

[@mnkiefer](<https://github.com/mnkiefer>)

Senior Research Engineer


</details>
