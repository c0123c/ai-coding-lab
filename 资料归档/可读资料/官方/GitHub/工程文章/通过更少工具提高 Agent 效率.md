# 通过更少工具提高 Agent 效率

讨论工具过多带来的选择与上下文负担，介绍动态筛选、聚类和基于上下文的工具发现，以减少无效操作。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-11-19](<https://github.blog/ai-and-ml/github-copilot/how-were-making-github-copilot-smarter-with-fewer-tools/>)；更新：[2025-11-19](<https://github.blog/ai-and-ml/github-copilot/how-were-making-github-copilot-smarter-with-fewer-tools/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/how-were-making-github-copilot-smarter-with-fewer-tools/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/通过更少工具提高 Agent 效率.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 我们如何用更少的工具让 GitHub Copilot 变得更智能

在 VS Code 中，GitHub Copilot Chat 可以通过 Model Context Protocol (MCP) 访问数百种工具，从代码库分析工具到 Azure 特定的实用程序。但为 agent 提供太多工具并不总是让它变得更聪明。有时它只会让它变得更慢。

如果您在 VS Code 中见过这个旋转器，那么您已经达到了试图同时对太多工具进行推理的模型的极限。

![“优化工具选择...”旁边的旋转屏幕截图](<https://github.blog/wp-content/uploads/2025/11/tool-selection.png?resize=385%2C60>)

为了解决这个问题，我们构建了两个新系统——嵌入引导工具路由和自适应工具集群——并且我们正在推出一个简化的工具集，将默认的 40 内置工具缩减为 13 核心工具。在 SWE-Lancer 和 SWEbench 等基准测试中，通过 GPT-5 和 Sonnet 4.5 进行验证，这些更改将成功率提高了 2-5 个百分点。在在线 A/B 测试中，它平均将响应延迟减少 400 毫秒。

## 太多工具阻碍了 agent 智能

VS Code 中的默认工具集由大约 40 个内置工具组成，范围从通用命令行实用程序到 Jupyter Notebook 的专用工具。如果包含 MCP 服务器，这个数字可能会增加到数百个。通常，MCP 服务器会引入如此多的工具，以至于可能超出某些型号的 API 限制。

我们探索了筛选工具集的方法，以仅提供与用户查询最相关的工具，同时不限制 agent 的功能。 具体来说，我们需要确保不会牺牲用户体验来实现更低的延迟。

为了实现这一目标，我们设计了一种中间方法：“虚拟工具”。这包括在功能上将类似的工具分组到一个“虚拟工具”下，聊天 agent 可以根据需要进行扩展。将它们视为包含相关工具的目录。这使模型能够大致了解可用的内容，而无需使用数百个工具名称来淹没它。 如果模型搜索单个工具，它还会降低我们预期的缓存未命中率，因为很可能会一起使用和激活类似的工具。

## 对 MCP 工具应用无损动态工具选择

### 自适应工具聚类

最初，我们将所有可用的工具输入到 LLM 中，并要求它对它们进行分组和总结。但这有两个大问题：

* 我们无法控制创建的组的数量，有时会超出模型限制
* 它非常慢并且产生了巨大的代币成本。该模型有时也会“忘记”对某些工具进行分类，从而迫使重试

为了解决这个问题，我们应用 [ 针对语义相似性任务优化的内部 Copilot 嵌入模型 ](<https://github.blog/news-insights/product-news/copilot-new-embedding-model-vs-code/>) 为每个工具生成嵌入，并使用余弦相似度对它们进行分组。这种聚类方法可以实现精确、稳定和可重复的分组。例如，以下是嵌入空间中 GitHub MCP 服务器工具的一种可能的嵌入分组：

![聚类图表，显示 GitHub MCP 服务器工具的嵌入分组：create_pending_pull_request_review、get_issue_comments、create_branch、get_me 和list_tags.](<https://github.blog/wp-content/uploads/2025/11/tool-use.png?resize=1024%2C512>)

我们仍然使用模型调用来总结每个集群，但此步骤比要求模型从头开始对所有内容进行分类要快得多且便宜得多。工具嵌入和组摘要在本地缓存，因此重新计算它们相对便宜。

## 根据上下文选择工具

一旦工具被分组，我们就面临另一个问题：模型如何知道要打开哪个组而不检查所有工具？ 我们看到，大多数时候，模型会*最终*找到适合其任务的工具。然而，每次调用虚拟工具仍然会导致缓存未命中、额外的往返以及一小部分 agent 操作失败的机会。

例如，当用户说：*“修复此错误并将其合并到开发分支中。”*

该模型经常打开**搜索工具**，然后是**文档工具**，然后是**本地Git工具**，最后意识到它实际上需要内部的**合并工具GitHub MCP 工具组 ** 完成操作。

每个不正确的组查找都会增加延迟和开销，即使从上下文中可以明显看出正确的组。

为了解决这个问题，我们引入了**嵌入引导工具路由**。在扩展任何工具组之前，系统会将查询嵌入与所有工具（及其集群）的向量表示进行比较，从而允许它预先选择语义上最相关的候选者 - 即使它们深埋在组内。

通过上下文感知路由，我们可以从一开始就推断模型很可能需要 GitHub MCP 工具组中的 * 合并工具 *，并将其直接包含在其候选集中，从而消除不必要的探索性调用并显着降低延迟和故障率。 
   
通过仅显示最有希望的匹配，我们使模型的搜索更有针对性和可靠，同时减少了多余的探索。

## 基于嵌入的选择（由 Copilot 嵌入模型提供支持）

我们通过以下方式计算基于嵌入的选择过程的成功 **工具使用范围，** 它衡量模型在需要时已经拥有可见的正确工具的频率。

在基准测试中，基于嵌入的方法实现了 94.5% 工具使用覆盖率，优于基于 LLM- 的选择 (87.5%) 和默认静态工具列表 (69.0%)。

**离线**，这种方法导致27.5%覆盖率的绝对提高，明显超越基于LLM-的方法，同时帮助agent更快地推理并保持高效。

**在线测试**显示了相同的模式：只有**19%**的稳定工具调用使用旧方法成功预扩展，而**72%**的Insiders工具调用由于基于嵌入的匹配而被预扩展。这证实了离线观察到的收益始终反映在现实世界的使用中。

![显示覆盖率的条形图。基于嵌入的 94.5% 最高，其次是基于 87.5% 的 GPT-4.1，默认工具位于 69%.](<https://github.blog/wp-content/uploads/2025/11/HumanEvalInfilling_1200x630.png?resize=1024%2C538>)

## 少即是多：缩小默认工具集

即使没有达到大规模 MCP 服务器可以触发的模型限制，过大的内置工具集仍然会降低性能。 在离线基准测试中，当 agent 有权访问完整的内置工具集时，我们观察到 2–5 基准测试（包括 SWE-Lancer）的解决率下降了一个百分点。 从行为上来说，agent 最终会忽略明确的指令，错误地使用工具，并调用对于当前任务来说不必要的工具。

因此，我们精简了清单。根据工具使用统计数据和性能数据，我们确定了 13 基本工具的核心工具集。这些工具包括高级存储库结构解析、文件读取和编辑、上下文搜索和终端使用。

其余的非核心内置工具分为四个虚拟类别：Jupyter Notebook 工具、Web 交互工具、VS Code 工作区工具和测试工具。这样，模型就会看到预先设置的较小核心，并且仅在必要时才可以扩展组。 因此，使用缩小工具集的用户在 TTFT（首次令牌时间）中平均减少了 190 毫秒，在 TTFT（最终令牌时间或完成模型响应时间）中平均减少了 400 毫秒。

更小的工具集使 agent 更加有效：推理更简单、响应时间更快、性能更好。

## 未来方向：从工具选择到长上下文推理

随着 MCP 系统的发展，挑战不仅仅是选择正确的工具，而是跨时间、上下文和交互进行推理。

真正的智能模型不应该仅仅对查询做出反应；而应该对查询做出反应。它应该*记住以前的工具使用情况，从历史记录中推断意图，并在长时间会话中规划多步骤操作*。 从这个意义上说，工具选择是长上下文推理的早期形式。今天帮助模型找到正确工具的机制在未来可以帮助他们进行数千次推理，帮助他们决定何时采取行动、何时委托以及何时停止。

我们的下一步是探索如何将嵌入、记忆和强化信号结合起来，创建上下文感知的 agents，学习 * 如何 * 使用工具，而不仅仅是 * 选择哪个 *。

**想了解 Copilot 如何实际使用 MCP 工具？** [立即尝试 GitHub Copilot >](<https://github.com/features/copilot/?utm_source=blog-vs-code-updates&utm_medium=blog&utm_campaign=universe25post>)

**致谢**

衷心感谢我们的开发者社区继续向我们提供反馈并推动我们通过 GitHub Copilot 提供尽可能最佳的 agent 体验。还要非常感谢 [Zijian Jin ](<https://www.linkedin.com/in/zijianjin/>)，他是团队中的一名研究员，帮助撰写了此博客，并感谢 VS Code 和 GitHub Copilot 的研究人员、工程师、产品经理所做的这项工作。  （另外：我们正在招聘应用研究人员和软件工程师，所以请随意申请！）

---

## 标签：

* [代理工作流程](<https://github.blog/tag/agentic-workflows/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [MCP](<https://github.blog/tag/mcp/>)
* [VS Code](<https://github.blog/tag/vs-code/>)

## 撰写者

![阿尼莎·阿加瓦尔](<https://avatars.githubusercontent.com/u/20461397?v=4&s=200>)

### [阿尼莎·阿加瓦尔](<https://github.blog/author/24anisha/>)

[@24anisha](<https://github.com/24anisha>)

应用科学家 II

![康纳·皮特](<https://avatars.githubusercontent.com/u/2230985?v=4&s=200>)

### [康纳·皮特](<https://github.blog/author/connor4312/>)

[@connor4312](<https://github.com/connor4312>)

首席软件工程师


## 原文对照

<details>
<summary>展开完整原文</summary>

# How we’re making GitHub Copilot smarter with fewer tools

In VS Code, GitHub Copilot Chat can access hundreds of tools through the Model Context Protocol (MCP) that range from codebase analysis tools to Azure-specific utilities. But giving an agent too many tools doesn’t always make it smarter. Sometimes it just makes it slower.

If you’ve ever seen this spinner in VS Code, you’ve hit the limits of a model that’s trying to reason across too many tools at once.

![Screenshot of a spinner next to the words "Optimizing tool selection..."](<https://github.blog/wp-content/uploads/2025/11/tool-selection.png?resize=385%2C60>)

To fix that, we’ve built two new systems—embedding-guided tool routing and adaptive tool clustering—and we’re rolling out a reduced toolset that trims the default 40 built-in tools down to 13 core ones. Across benchmarks like SWE-Lancer and SWEbench-Verified with both GPT-5 and Sonnet 4.5, these changes improve success rates by 2-5 percentage points. In online A/B testing, it reduces response latency by an average of 400 milliseconds.

## Too many tools impede agent intelligence

The default toolset in VS Code consists of about 40 built-in tools, ranging from general command-line utilities to specialized tools for Jupyter Notebooks. With MCP servers included, that number can grow into the hundreds. Often, MCP servers bring in so many tools that they can exceed the API limits of some models.

We’ve explored ways to filter down our toolset to provide only the tools most relevant to the user’s query, while not restricting the agent’s capabilities. Specifically, we needed to make sure we didn’t sacrifice the user’s experience to achieve lower latency.

To accomplish this, we designed a middle-ground approach: “virtual tools.” This includes functionally grouping similar tools under one “virtual tool” the chat agent can expand as needed. Think of these as directories that contain related tools. This gives the model a general sense of what’s available without flooding it with hundreds of tool names. It also reduces the cache miss rate we’d expect if the model searched for individual tools, since it’s likely that similar tools are used and activated together.

## Applying lossless dynamic tool selection for MCP tools

### Adaptive tool clustering

Initially we fed all the available tools into an LLM and asked it to group and summarize them. But this had two big issues:

* We couldn’t control the number of groups created, and it sometimes exceeded model limits
* It was extremely slow and incurred a huge token cost. The model would also sometimes ‘forget’ to categorize certain tools, forcing retries

To tackle this issue, we applied [our internal Copilot embedding model optimized for semantic similarity tasks](<https://github.blog/news-insights/product-news/copilot-new-embedding-model-vs-code/>) to generate embeddings for each tool and group them using cosine similarity. This clustering method allowed precise, stable, and reproducible groups. As an example, here is one possible grouping of embeddings for the GitHub MCP server’s tools in the embedding space:

![A clustering chart showing groupings of embeddings for the GitHub MCP server's tools: create_pending_pull_request_review, get_issue_comments, create_branch, get_me, and list_tags.](<https://github.blog/wp-content/uploads/2025/11/tool-use.png?resize=1024%2C512>)

We still use a model call to summarize each cluster, but this step is much faster and cheaper than asking the model to categorize everything from scratch. Tool embeddings and group summaries are cached locally, so recomputing them is comparatively cheap.

## Context-guided tool selection

Once tools were grouped, we faced another problem: how does the model know which group to open without checking them all? We saw that, most of the time, the model would *eventually*find the right tool for its task. However, each call to a virtual tool still results in a cache miss, an extra round trip, and an opportunity for a small percentage of agent operations to fail.

For example, when the user says: *“Fix this bug and merge it into the dev branch.”*

The model often opens **search tools**, then **documentation tools**, then **local Git tools**, before finally realizing that it actually needs the **merge tool inside the GitHub MCP tool group** to complete the operation.

Each incorrect group lookup adds latency and overhead, even though the correct group is fairly obvious from the context.

To address this, we introduced **Embedding-Guided Tool Routing**. Before any tool group is expanded, the system compares the query embedding against vector representations of all tools (and their clusters), allowing it to pre-select the most semantically relevant candidates—even if they’re buried deep inside a group.

With context-aware routing, we can infer from the beginning that the model is very likely to need the *merge tool* inside the GitHub MCP tool group, and include it directly in its candidate set—eliminating unnecessary exploratory calls and significantly reducing latency and failure rates.   
   
By surfacing only the most promising matches, we make the model’s search more targeted and reliable, while reducing redundant exploration.

## Embedding-based selection (powered by the Copilot Embedding model)

We calculate the success of our embedding-based selection process via **Tool Use Coverage,** which measures how often the model already has the right tool visible when it needs it.

In benchmarks, the embedding-based approach achieved 94.5% Tool Use Coverage, outperforming both LLM-based selection (87.5%) and the default static tool list (69.0%).

**Offline**, this approach resulted in a 27.5% absolute improvement in coverage, clearly surpassing the LLM-based method while helping the agent reason faster and stay efficient.

**Online testing** shows the same pattern: only **19%** of Stable tool calls were successfully pre-expanded using the old method, whereas **72%** of Insiders tool calls were pre-expanded thanks to the embedding-based matching. This confirms that the gains observed offline are consistently reflected in real-world usage.

![A bar graph showing coverage rates. Embedding based is highest with 94.5%, followed by GPT-4.1 based at 87.5% and Default tools at 69%.](<https://github.blog/wp-content/uploads/2025/11/HumanEvalInfilling_1200x630.png?resize=1024%2C538>)

## Less is more: shrinking the default toolset

Even without hitting the model limits that massive MCP servers can trigger, an oversized built-in toolset still degrades performance. In offline benchmarks, we observed a 2–5 percentage point decrease in resolution rate on benchmarks including SWE-Lancer when the agent had access to the full built-in toolset. Behaviorally, the agent ends up ignoring explicit instructions, using tools incorrectly, and calling tools that are unnecessary to the task at hand.

So, we trimmed the list. Based on tool usage statistics and performance data, we identified a core toolset of 13 essential tools. These tools encompass high-level repository structure parsing, file reading and editing, context searching, and terminal usage.

The remaining, non-core built-in tools are grouped into four virtual categories: Jupyter Notebook Tools, Web Interaction Tools, VS Code Workspace Tools, and Testing Tools. This way, the model sees the smaller core set up-front and can expand groups only if necessary. As a result, users with the shrunken toolset experience an average decrease of 190 milliseconds in TTFT (Time To First Token), and an average decrease of 400 milliseconds in TTFT (Time to Final Token, or time to complete model response).

A smaller toolset enables the agent to be more effective: simpler reasoning, faster response times, and better performance.

## Future directions: from tool selection to long-context reasoning

As MCP systems evolve, the challenge isn’t just picking the right tool—it’s reasoning across time, context, and interactions.

A truly intelligent model shouldn’t just react to queries; it should *remember previous tool usage, infer intent from history, and plan multi-step actions* over long sessions. In this sense, tool selection is an early form of long-context reasoning. The same mechanisms that help models route to the right tool today could, in the future, help them reason across thousands of turns helping them decide when to act, when to delegate, and when to stop.

Our next step is to explore how embeddings, memory, and reinforcement signals can combine to create context-aware agents that learn *how* to use tools, not just *which* ones to pick.

**Want to see how Copilot uses MCP tools in action?** [Try GitHub Copilot now >](<https://github.com/features/copilot/?utm_source=blog-vs-code-updates&utm_medium=blog&utm_campaign=universe25post>)

**Acknowledgments**

A big shoutout to our developer community for continuing to give us feedback and push us to deliver the best possible agent experiences with GitHub Copilot. A huge thanks also to [Zijian Jin](<https://www.linkedin.com/in/zijianjin/>), a researcher on the team who helped to write this blog—and to the researchers, engineers, product managers across VS Code and GitHub Copilot for this work.  (Also: We’re hiring applied researchers and software engineers, so feel free to apply!)

---

## Tags:

* [agentic workflows](<https://github.blog/tag/agentic-workflows/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [MCP](<https://github.blog/tag/mcp/>)
* [VS Code](<https://github.blog/tag/vs-code/>)

## Written by

![Anisha Agarwal](<https://avatars.githubusercontent.com/u/20461397?v=4&s=200>)

### [Anisha Agarwal](<https://github.blog/author/24anisha/>)

[@24anisha](<https://github.com/24anisha>)

Applied Scientist II

![Connor Peet](<https://avatars.githubusercontent.com/u/2230985?v=4&s=200>)

### [Connor Peet](<https://github.blog/author/connor4312/>)

[@connor4312](<https://github.com/connor4312>)

Principal Software Engineer


</details>
