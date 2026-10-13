# 构建 Copilot Agent 记忆系统

说明记忆创建、检索、验证与跨 Agent 共享的机制，讨论隐私、故障韧性和评估方式，以及对开发工作流的影响。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-01-15](<https://github.blog/ai-and-ml/github-copilot/building-an-agentic-memory-system-for-github-copilot/>)；更新：[2026-01-15](<https://github.blog/ai-and-ml/github-copilot/building-an-agentic-memory-system-for-github-copilot/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/building-an-agentic-memory-system-for-github-copilot/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/构建 Copilot Agent 记忆系统.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 为 GitHub Copilot 构建代理记忆系统

我们的愿景是将 [GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-copilot-memory&utm_medium=blog&utm_campaign=dec25postuniverse>) 发展为 agents 的生态系统，该生态系统在从编码和代码审查到安全、调试、部署和维护的整个开发生命周期中进行协作。为了释放多 agent 工作流程的全部潜力，我们需要超越孤立的交互（每个会话从头开始），并转向随着每次使用而增长的累积知识库。

**跨agent内存允许agents记住并学习整个开发工作流程的经验，而无需依赖明确的用户指令。**

每次交互都会让 Copilot 更多地了解您的代码库和约定，使其随着时间的推移变得越来越有效。例如，如果Copilot编码agent了解您的存储库在修复安全漏洞时如何处理数据库连接，那么Copilot代码审查可以使用该知识来发现未来拉取请求中不一致的模式。或者，如果Copilot代码审查注意到某些文件必须保持同步，将来Copilot编码agent将在生成新代码时自动将它们一起更新。

# 挑战：记住什么以及何时忘记

我们的 agents 在提取特定任务所需的上下文方面不断改进。内存系统的核心挑战不是信息检索，而是确保任何存储的知识随着代码跨分支和时间的演变而保持有效。

实际上，这意味着内存系统必须处理代码更改、废弃的分支和冲突的观察，同时确保 agents 只对与当前任务和代码状态相关的信息起作用。例如，在一个分支中观察到的日志记录约定稍后可能会被修改、取代，或者根本不会合并。

一种选择是实施离线管理服务来删除重复、解决冲突、跟踪分支状态并使陈旧信息过期。然而，在 GitHub 的规模下，这种方法会带来巨大的工程复杂性和 LLM 成本，同时仍然需要在读取时协调更改的机制。我们首先探索一种更简单、更有效的方法。

# 我们的解决方案：及时验证

信息检索是一个不对称问题：很难解决，但很容易验证。通过使用实时验证，我们获得了预存记忆的力量，同时避免了过时或误导性信息的风险。

我们不是离线记忆管理，而是通过**引用**来存储记忆：对支持每个事实的特定代码位置的引用。当 agent 遇到存储的内存时，它会实时验证引用，在使用之前验证信息是否准确且与当前分支相关。此验证归结为少量简单的读取操作，在我们的测试中不会给 agent 会话增加明显的延迟。

## 作为工具调用的内存创建

我们将内存创建实现为一种工具，当他们发现可能对未来任务产生可操作影响的内容时，agents 可以调用该工具。

![流程图，显示 Copilot agents 在执行任务时如何存储值得记住的知识。](<https://github.blog/wp-content/uploads/2026/01/01_Memory_generation-Blog-1200x630_15-12-25_1.jpg?resize=2400%2C1260>)

Copilot agents 在执行任务时如何存储值得记住的知识。

考虑这个例子：在审查经验丰富的开发人员的拉取请求时，Copilot代码审查发现API版本跟踪必须在代码库的不同部分之间保持同步。它可能会在同一个拉取请求中遇到这三个更新：

* 在src/client/sdk/constants.ts中：

```
export const API_VERSION = "v2.1.4";
```

* 在server/routes/api.go中：

```
const APIVersion = "v2.1.4"
```

* 在docs/api-reference.md中：

```
Version: v2.1.4
```

作为响应，Copilot代码审查可以调用内存存储工具来创建如下内存：

```
{
  subject: "API version synchronization", 
  fact: "API version must match between client SDK, server routes, and documentation.",
  citations: ["src/client/sdk/constants.ts:12", "server/routes/api.go:8", "docs/api-reference.md:37"], 
  reason: "If the API version is not kept properly synchronized, the integration can fail or exhibit subtle bugs. Remembering these locations will help ensure they are kept syncronized in future updates."
}
```

**结果：** 下次 agent 在任何这些位置更新 API 版本时，它将看到此内存并意识到它也必须更新其他位置，从而防止可能破坏集成的版本控制不匹配。同样，如果经验不足的开发人员打开仅更新其中一个位置的拉取请求，Copilot 代码审查将标记遗漏并建议缺失的更新，*自动将知识从更有经验的团队成员转移到较新的团队成员。* 💥

## 内存使用情况

### 检索

当 agent 开始新会话时，我们会检索目标存储库的最新记忆并将其包含在提示中。未来的实现将启用额外的检索技术，例如搜索工具和加权优先级。

![](<https://github.blog/wp-content/uploads/2026/01/02_Memory_usage-Blog-1200x630_15-12-25_1.jpg?resize=2400%2C1550>)

Copilot 如何通过之前任务的记忆来丰富 agent 提示。

### 验证

在应用任何内存之前，系统会提示 agent 通过检查引用的代码位置来验证其准确性和相关性。如果代码与记忆相矛盾，或者引用无效（例如指向不存在的位置），则鼓励 agent 存储反映新证据的记忆的正确版本。如果引用检查并且内存被认为有用，则鼓励 agent 再次存储它以刷新其时间戳。

### 隐私和安全

值得注意的是，记忆的范围很窄。给定存储库的内存只能响应具有写入权限的贡献者在该存储库中采取的操作而创建，并且只能用于具有读取权限的用户在同一存储库上启动的任务。就像源代码本身一样，有关存储库的记忆保留在该存储库中，确保隐私和安全。

## 跨agent内存共享

当不同的 Copilot agents 互相学习时，我们的记忆系统的全部力量就会显现出来。

1. **Copilot 代码审查 ** 在审查拉取请求时发现日志记录约定：“日志文件名应遵循模式‘app-YYYYMMDD.log’。使用 Winston 进行日志记录，格式为：时间戳、错误代码、用户 ID。”
2. **Copilot编码agent**稍后被分配一项任务来实现新的微服务。它查看并验证内存并自动应用相同的记录格式。
3. **Copilot CLI** 帮助开发人员调试问题，高效检索正确的日志文件，并根据代码审查了解到的日志格式查找相关时间戳 agent。

每个 agent 都对共享知识库做出贡献并从中受益，从而允许 agents 跨任务重用经过验证的存储库知识。随着其他 agents 采用内存（无论是用于开发工作流程、调试还是安全分析），它们都将有助于对代码库的不断发展的理解并从中受益。

# 评价

## 压力测试 agent 弹性

我们最担心的是过时的、不正确的、甚至恶意注入的记忆的影响。为了测试系统的弹性，我们故意在存储库中植入对抗性记忆（与代码库相矛盾的事实），并引用指向不相关或不存在的代码位置。

在所有测试用例中，agents 始终如一地验证引文、发现矛盾并更新不正确的记忆。内存池会自我修复，因为 agents 根据他们的观察存储了更正的版本。引文验证机制有力地防止了记忆误导的风险。

## 模拟真实的内存池

对于我们评估集中的每个存储库，我们在不同的历史任务（早于我们的目标评估任务）上运行 agents，并让它们使用我们提供的“store\_内存”工具有机地填充内存数据库。为了模拟最坏的情况，我们过度再现了被放弃或关闭但未合并的分支的记忆，以确保真实的嘈杂记忆。

**当我们对评估集中的拉取请求运行 Copilot 代码审查时，内存使用导致 3% 精度提高，4% 召回率提高。**

## 衡量对开发人员的影响

我们的内存系统的最终测试是它对实际开发人员日常工作流程的影响。我们对前两个 Copilot agents 进行了 A/B 测试以部署内存、Copilot 代码审查和 Copilot 编码 agent，衡量对关键用户指标的影响。

* **Copilot 编码 agent**：7% 拉取请求合并率增加（带内存的 90% 与不带内存的 83%）。这意味着开发人员在将任务分配给 Copilot 时可以节省更多时间并更频繁地获得所需结果。
* **Copilot 代码审查**：2% 评论的积极反馈增加（77% 有记忆 vs 75% 无记忆）。这意味着自动代码审查正在提高质量保证。
* 两次增加均 ** 具有高度统计显着性 **，p 值 <0.00001

这些结果表明，跨 agent 内存为开发人员的日常工作流程提供了可衡量的价值。

# 接下来是什么

我们在 Copilot CLI、Copilot 编码 agent 和 Copilot 代码审查中基于选择加入部署了存储库范围的内存存储和使用。当我们迭代并准备在更多 Copilot 工作流程中进行更广泛的部署时，我们正在倾听用户反馈并密切跟踪性能指标。我们还在探索一系列调整内存生成、管理、优先级和使用的方法。

跨 agent 内存允许在代理工作流程中保留经过验证的信息，从而减少在每个任务开始时重新建立上下文的需要。我们对内存释放的可能性感到兴奋，而我们才刚刚开始。我们[期待您的反馈](<https://github.com/orgs/community/discussions/184415>)，以便我们确保GitHub Copilot继续以最能满足您需求的方式发展。快乐编码！

**[阅读我们的文档](<https://docs.github.com/copilot/how-tos/use-copilot-agents/copilot-memory?utm_source=blog-copilot-memory&utm_medium=blog&utm_campaign=dec25postuniverse>)**以了解如何在Copilot中启用内存>

---

## 标签：

* [代理内存](<https://github.blog/tag/agentic-memory/>)
* [代理工作流程](<https://github.blog/tag/agentic-workflows/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)
* [GitHub Copilot 代码审查](<https://github.blog/tag/github-copilot-code-review/>)
* [GitHub Copilot编码agent](<https://github.blog/tag/github-copilot-coding-agent/>)

## 撰写者

![蒂费雷特·加齐特](<https://avatars.githubusercontent.com/u/13252556?v=4&s=200>)

### [Tiferet Gazit](<https://github.blog/author/tiferet/>)

[@tiferet](<https://github.com/tiferet>)

Tiferet Gazit 是 GitHub 的首席机器学习工程师，负责构建 AI agents，以提高代码安全性、代码质量和开发人员的工作效率。她拥有医学计算机视觉和深度学习背景，热衷于开发影响人们生活的智能产品。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Building an agentic memory system for GitHub Copilot

Our vision is to evolve [GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-copilot-memory&utm_medium=blog&utm_campaign=dec25postuniverse>) into an ecosystem of agents that collaborate across the entire development lifecycle from coding and code review to security, debugging, deployment, and maintenance. To unlock the full potential of multi-agent workflows, we need to move beyond isolated interactions—that start from scratch each session—and toward a cumulative knowledge base that grows with every use.

**Cross-agent memory allows agents to remember and learn from experiences across your development workflow, without relying on explicit user instructions.**

Each interaction teaches Copilot more about your codebase and conventions, making it increasingly effective over time. For example, if Copilot coding agent learns how your repository handles database connections as it’s fixing a security vulnerability, Copilot code review can then use that knowledge to spot inconsistent patterns in future pull requests. Or if Copilot code review notices that certain files must stay synchronized, in the future Copilot coding agent will automatically update them together when generating new code.

# The challenge: What to remember and when to forget

Our agents continuously improve at extracting the context needed for specific tasks. The core challenge for memory systems isn’t about information retrieval, but ensuring that any stored knowledge remains valid as code evolves across branches and time.

In practice, this means a memory system must handle changes to code, abandoned branches, and conflicting observations—all while ensuring that agents only act on information that’s relevant to the current task and code state. For example, a logging convention observed in one branch may later be modified, superseded, or never merged at all.

One option would be to implement an offline curation service to deduplicate, resolve conflicts, track branch status, and expire stale information. At GitHub’s scale, however, such an approach would introduce significant engineering complexity and LLM costs, while still requiring mechanisms to reconcile changes at read time. We started by exploring a simpler, more efficient approach.

# Our solution: just-in-time verification

Information retrieval is an asymmetrical problem: It’s hard to solve, but easy to verify. By using real-time verification, we gain the power of pre-stored memories while avoiding the risk of outdated or misleading information.

Instead of offline memory curation, we store memories with **citations**: references to specific code locations that support each fact. When an agent encounters a stored memory, it verifies the citations in real-time, validating that the information is accurate and relevant to the current branch before using it. This verification boils down to a small number of simple read operations, adding no significant latency to agent sessions in our testing.

## Memory creation as a tool call

We implemented memory creation as a tool that agents can invoke when they discover something that’s likely to have actionable implications for future tasks.

![A flow chart showing how Copilot agents store learnings worth remembering as they carry out their tasks.](<https://github.blog/wp-content/uploads/2026/01/01_Memory_generation-Blog-1200x630_15-12-25_1.jpg?resize=2400%2C1260>)

How Copilot agents store learnings worth remembering as they carry out their tasks.

Consider this example: While reviewing a pull request from an experienced developer, Copilot code review discovers that API version tracking must stay synchronized across different parts of a codebase. It might encounter these three updates in the same pull request:

* In src/client/sdk/constants.ts:

```
export const API_VERSION = "v2.1.4";
```

* In server/routes/api.go:

```
const APIVersion = "v2.1.4"
```

* In docs/api-reference.md:

```
Version: v2.1.4
```

In response, Copilot code review can invoke the memory storage tool to create a memory like this:

```
{
  subject: "API version synchronization", 
  fact: "API version must match between client SDK, server routes, and documentation.",
  citations: ["src/client/sdk/constants.ts:12", "server/routes/api.go:8", "docs/api-reference.md:37"], 
  reason: "If the API version is not kept properly synchronized, the integration can fail or exhibit subtle bugs. Remembering these locations will help ensure they are kept syncronized in future updates."
}
```

**The result:** The next time an agent updates the API version in any of these locations, it will see this memory and realize that it must update the other locations too, preventing a versioning mismatch that could break integrations. Similarly, if an inexperienced developer opens a pull request that updates only one of these locations, Copilot code review will flag the omission and suggest the missing updates, *automatically transferring knowledge from a more experienced team member to a newer one.* 💥

## Memory usage

### Retrieval

When an agent starts a new session, we retrieve the most recent memories for the target repository and include them in the prompt. Future implementations will enable additional retrieval techniques, such as a search tool and weighted prioritization.

![](<https://github.blog/wp-content/uploads/2026/01/02_Memory_usage-Blog-1200x630_15-12-25_1.jpg?resize=2400%2C1550>)

How Copilot enriches agent prompts with memories from previous tasks.

### Verification

Before applying any memory, the agent is prompted to verify its accuracy and relevance by checking the cited code locations. If the code contradicts the memory, or if the citations are invalid (e.g. point to nonexistent locations), the agent is encouraged to store a corrected version of the memory reflecting the new evidence. If the citations check out and the memory is deemed useful, the agent is encouraged to store it again in order to refresh its timestamp.

### Privacy and security

It’s important to note that memories are tightly scoped. Memories for a given repository can only be created in response to actions taken within that repository by contributors with write permissions, and can only be used in tasks on that same repository initiated by users with read permissions. Much like the source code itself, memories about a repository stay within that repository, ensuring privacy and security.

## Cross-agent memory sharing

The full power of our memory system emerges as different Copilot agents learn from one another.

1. **Copilot code review** discovers a logging convention while reviewing a pull request: “Log file names should follow pattern ‘app-YYYYMMDD.log’. Use Winston for logging with format: timestamp, error code, user ID.”
2. **Copilot coding agent** is later assigned a task to implement a new microservice. It sees and verifies the memory and automatically applies the same logging format.
3. **Copilot CLI** helps a developer debug an issue, efficiently retrieving the correct log file and finding the relevant timestamps based on the logging format learned by the code review agent.

Each agent contributes to and benefits from the shared knowledge base, allowing agents to reuse validated repository knowledge across tasks. As additional agents adopt memory—whether for development workflows, debugging, or security analysis—they’ll contribute to and benefit from the same evolving understanding of your codebase.

# Evaluation

## Stress-testing agent resilience

Our biggest concern was the impact of outdated, incorrect, or even maliciously injected memories. To test the system’s resilience, we deliberately seeded repositories with adversarial memories–facts that contradicted the codebase–with citations pointing to irrelevant or nonexistent code locations.

Across all test cases, agents consistently verified citations, discovered contradictions, and updated incorrect memories. The memory pool self-healed as agents stored corrected versions based on their observations. The citation verification mechanism robustly prevented the risk of misleading memories.

## Simulating a realistic memory pool

For each repository in our evaluation set, we ran agents on diverse historical tasks (predating our target evaluation tasks) and let them populate the memory database organically, using the “store\_memory” tool we provided. To simulate worst-case conditions, we overrepresented memories from branches that were abandoned or closed without merging, ensuring realistically noisy memories.

**When we ran Copilot code review on the pull requests in our evaluation set, memory usage led to 3% increase in precision and 4% increase in recall.**

## Measuring impact on developers

The ultimate test of our memory system was its impact on real developers in their everyday workflows. We ran A/B tests on the first two Copilot agents to deploy memory, Copilot code review and Copilot coding agent, measuring the impact on key user metrics.

* **Copilot coding agent**: 7% increase in pull request merge rates (90% with memories vs. 83% without). This means developers are saving more time and getting the desired results more often when they assign tasks to Copilot.
* **Copilot code review**: 2% increase in positive feedback on comments (77% with memories vs 75% without). This means automated code review is yielding improved quality assurance.
* Both increases are **highly statistically significant**, with p-value <0.00001

These results demonstrate that cross-agent memory delivers measurable value to developers in their daily workflows.

# What’s next

We’ve deployed repository-scoped memory storage and usage in Copilot CLI, Copilot coding agent, and Copilot code review on an opt-in basis. We’re listening to user feedback and tracking performance metrics closely as we iterate and prepare for a wider rollout across more Copilot workflows. We’re also exploring a range of approaches to tuning memory generation, curation, prioritization, and usage.

Cross-agent memory reduces the need to re-establish context at the start of each task by allowing validated information to persist across agentic workflows. We’re excited about the possibilities memory will unlock, and we’re just getting started. We [look forward to your feedback](<https://github.com/orgs/community/discussions/184415>) so we can ensure GitHub Copilot continues to evolve in ways that best support your needs. Happy coding!

**[Read our Docs](<https://docs.github.com/copilot/how-tos/use-copilot-agents/copilot-memory?utm_source=blog-copilot-memory&utm_medium=blog&utm_campaign=dec25postuniverse>)** to learn how to enable memory in Copilot >

---

## Tags:

* [agentic memory](<https://github.blog/tag/agentic-memory/>)
* [agentic workflows](<https://github.blog/tag/agentic-workflows/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)
* [GitHub Copilot code review](<https://github.blog/tag/github-copilot-code-review/>)
* [GitHub Copilot coding agent](<https://github.blog/tag/github-copilot-coding-agent/>)

## Written by

![Tiferet Gazit](<https://avatars.githubusercontent.com/u/13252556?v=4&s=200>)

### [Tiferet Gazit](<https://github.blog/author/tiferet/>)

[@tiferet](<https://github.com/tiferet>)

Tiferet Gazit is a principal machine learning engineer at GitHub building AI agents for code security, code quality, and developer productivity. With a background in medical computer vision and deep learning, she’s passionate about developing intelligent products that impact people’s lives.


</details>
