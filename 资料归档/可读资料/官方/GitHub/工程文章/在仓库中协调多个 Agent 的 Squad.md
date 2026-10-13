# 在仓库中协调多个 Agent 的 Squad

介绍仓库内任务编排、共享记忆、上下文复制与显式状态等架构模式，说明多 Agent 协作的设计方式。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-03-19](<https://github.blog/ai-and-ml/github-copilot/how-squad-runs-coordinated-ai-agents-inside-your-repository/>)；更新：[2026-03-20](<https://github.blog/ai-and-ml/github-copilot/how-squad-runs-coordinated-ai-agents-inside-your-repository/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/how-squad-runs-coordinated-ai-agents-inside-your-repository/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/在仓库中协调多个 Agent 的 Squad.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Squad 如何在存储库内协调运行 AI agents

如果您以前使用过 AI 编码工具，您就会知道其中的模式。你写一个提示，模型会误解，你会对其进行改进，然后你会得到更好的输出。进展更多地取决于模型的引导而不是软件的构建。

随着项目的发展，挑战不再是“我如何提示？”并开始变成“我如何协调设计、实施、测试和审查，同时又不丢失上下文。”

多 agent 系统是跨越这个平台的好方法，但通常需要大量的设置。人们花费数小时构建编排层、连接框架和配置矢量数据库，然后才能委派单个任务。

[Squad](<https://github.com/bradygaster/squad>) 是一个基于 GitHub Copilot 构建的开源项目，直接在您的存储库中初始化预配置的 AI 团队。我们相信，多agent开发可以是可访问的、清晰的和有用的，而不需要繁重的编排基础设施或深厚的提示工程专业知识。

两个命令 - `npm install -g @bradygaster/squad-cli` 全局一次，`squad init` 每个存储库一次 - 并且 Squad 会删除一个专门的 AI 团队：直接针对您的存储库的领导、前端开发人员、后端开发人员和测试人员。

**Squad 不是单个聊天机器人切换角色，而是演示了存储库本机多 agent 编排，无需重型集中式基础设施 **。

## 小队如何协调 agents

您用自然语言描述需要完成的工作。从这里开始，Squad 内的协调员 agent 确定路线，加载存储库上下文，并产生具有特定任务指令的专家。

例如，您输入：“*团队，我需要JWT身份验证-刷新令牌，bcrypt，作品。*”然后您会看到团队并行旋转。后端专家负责实施。测试人员开始编写附带的测试套件。文档专家打开拉取请求。几分钟之内，文件就被写入并创建了分支。这些专家已经知道您的命名约定以及您上周二对数据库连接的决定 - 不是因为您将其放入提示中，而是因为 agents 从共享团队决策和提交到存储库的他们自己的项目历史文件中加载。

Squad 不会强制您手动测试输出并提示模型进行多轮修复，而是在内部处理迭代。一旦后端专家起草了初始实现，测试人员就针对它运行测试套件。如果这些测试失败，测试人员将拒绝该代码。至关重要的是，编排层阻止原始的 agent 修改自己的工作。 Squad 的审稿人协议可以阻止原作者修改被拒绝的作品，并且必须有不同的 agent 介入来修复它。 **这迫使真正的独立审查具有单独的上下文窗口和新鲜的视角，而不是要求单个AI审查自己的错误**。在启用审阅者自动化的工作流程中，您将审阅在此内部循环中幸存下来的拉取请求，而不是每次中间尝试。

它不是自动驾驶仪，在第一节课上也不是魔法。 Agents 会提出澄清问题，有时会做出合理但错误的假设。您仍然可以审查并合并每个拉取请求。这是协作编排，而不是自主执行。

## 存储库本机编排背后的架构模式

无论您使用 Squad 还是构建自己的多 agent 工作流程，我们都从构建存储库本机编排中学到了一些架构模式。这些模式使架构从“黑匣子”行为转向在存储库级别可检查和可预测的行为。

### 1。共享内存的“Drop-box”模式

大多数 AI 编排依赖于实时聊天或复杂的矢量数据库查找来保持 agents 同步。我们发现这往往太脆弱了；在实时 agents 之间同步状态是一件傻事。

相反，Squad 使用“投递箱”模式。每个架构选择（例如选择特定库或命名约定）都会作为结构化块附加到存储库中的版本化 `decisions.md` 文件中。这是一个赌注，存储库内的异步知识共享比实时同步具有更好的扩展性。通过将 Markdown 文件视为团队的共享大脑，您可以获得持久性、易读性以及对团队所做的每个决定的完美审计跟踪。由于此内存位于项目文件中而不是实时会话中，因此团队还可以在断开连接或重新启动后恢复上下文，并从中断处继续。

### 2。上下文复制优于上下文分割

AI 开发中最大的障碍之一是上下文窗口限制。当一个agent试图做所有事情时，“工作记忆”就会被元管理挤满，从而导致幻觉。

Squad 通过确保协调器 agent 仍然是瘦路由器来解决这个问题。它不做工作；它催生了专家。因为每个专家都作为一个单独的推理调用运行，具有自己的大上下文窗口（例如，在支持的模型上最多 200K 个令牌），所以您不会将一个上下文拆分为四个 agents，而是在它们之间复制存储库上下文。

并行运行多个专家可以让您同时运行多个独立的推理上下文。这使得每个 agent 都可以“查看”存储库的相关部分，而无需与其他 agents 的想法竞争空间。

### 3。提示中的显性记忆与权重中的隐性记忆

我们相信 AI 团队的记忆应该清晰且有版本。您不必怀疑 agent 对您的项目“了解”什么。

在 Squad 中，agent 的身份主要建立在两个存储库文件上：章程（他们是谁）和历史记录（他们做了什么），以及共享的团队决策。这些是纯文本。因为它们位于您的 `.squad/` 文件夹中，所以 AI 的内存与您的代码一起进行版本控制。当您克隆存储库时，您不仅获得了代码，还获得了代码。您将获得一个已经“加入”的 AI 团队，因为他们的记忆与代码一起直接存储在存储库中。

## 降低多 agent 工作流程的障碍

我们对 Squad 的最大胜利是，它让任何人都可以轻松地以低接触、低仪式的方式开始代理开发。您不必花费数小时来研究基础设施、学习复杂的提示工程或管理复杂的 CLI 交互，只是为了让 AI 团队帮助您编写代码。

要了解存储库本机编排的感觉，请查看 [Squad 存储库 ](<https://github.com/bradygaster/squad>) 并让一个小队解决问题，看看工作流程如何演变。

---

## 标签：

* [AI](<https://github.blog/tag/ai/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [多agent工作流程](<https://github.blog/tag/multi-agent-workflows/>)

## 撰写者

![布雷迪·加斯特](<https://secure.gravatar.com/avatar/c4c1d68f4766046384a4c7ee192c152e0bf240745b6b84a0baa656e9b0abd0b1?s=200&d=mm&r=g>)

### [布雷迪·加斯特](<https://github.blog/author/bradyg/>)

[@bradygaster](<https://github.com/bradygaster>)

校长 PM 建筑师于 CoreAI 应用程序& Agents 在 Microsoft，我在那里构建一些东西，让人们更容易参加聚会 Copilot 在云中。


## 原文对照

<details>
<summary>展开完整原文</summary>

# How Squad runs coordinated AI agents inside your repository

If you’ve used AI coding tools before, you know the pattern. You write a prompt, the model misunderstands, you refine it, and you coax better output. Progress depends more on steering the model than on building the software.

As projects grow, the challenge stops being “how do I prompt?” and starts becoming “how do I coordinate design, implementation, testing, and review without losing context along the way.”

Multi-agent systems are a great way to move past this plateau, but usually require a massive amount of setup. People spend hours building orchestration layers, wiring up frameworks, and configuring vector databases before they can delegate a single task.

[Squad](<https://github.com/bradygaster/squad>), an open source project built on GitHub Copilot, initializes a preconfigured AI team directly inside your repository. It is a bet that multi-agent development can be accessible, legible, and useful without requiring heavy orchestration infrastructure or deep prompt engineering expertise.

Two commands—`npm install -g @bradygaster/squad-cli` once globally, `squad init` once per repo—and Squad drops a specialized AI team: a lead, frontend developer, backend developer, and tester directly against your repository.

Instead of a single chatbot switching roles, **Squad demonstrates repository native multi-agent orchestration without heavy centralized infrastructure**.

## How Squad coordinates work across agents

You describe the work you need done in natural language. From there, a coordinator agent inside Squad figures out the routing, loads repository context, and spawns specialists with task-specific instructions.

For example, you type: “*Team, I need JWT auth—refresh tokens, bcrypt, the works.*” Then you watch the team spin up in parallel. The backend specialist takes the implementation. The tester starts writing the accompanying test suite. A documentation specialist opens a pull request. Within minutes, files are written and branches are created. These specialists already know your naming conventions and what you decided about database connections last Tuesday—not because you put it in the prompt, but because agents load from shared team decisions and their own project history files committed to the repository.

Instead of forcing you to manually test the output and prompt the model through multiple rounds of fixes, Squad handles iteration internally. Once the backend specialist drafts the initial implementation, the tester runs their test suite against it. If those tests fail, the tester rejects the code. Crucially, the orchestration layer prevents the original agent from revising its own work. Squad’s reviewer protocol can prevent the original author from revising rejected work, and a different agent must step in to fix it. **This forces genuine independent review with a separate context window and a fresh perspective, rather than asking a single AI to review its own mistakes**. In workflows where reviewer automation is enabled, you review the pull request that survives this internal loop rather than every intermediate attempt.

It’s not autopilot, and it’s not magic on session one. Agents will ask clarifying questions and sometimes make reasonable but wrong assumptions. You still review and merge every pull request. It is collaborative orchestration, not autonomous execution.

## Architectural patterns behind repository-native orchestration

Whether you use Squad or build your own multi-agent workflows, there are a few architectural patterns we’ve learned from building repository-native orchestration. These patterns move the architecture away from “black box” behavior toward something inspectable and predictable at the repository level.

### 1. The “Drop-box” pattern for shared memory

Most AI orchestration relies on real-time chat or complex vector database lookups to keep agents in sync. We’ve found that this is often too fragile; synchronizing state across live agents is a fool’s errand.

Instead, Squad uses a “drop-box” pattern. Every architectural choice, like choosing a specific library or a naming convention, is appended as a structured block to a versioned `decisions.md` file in the repository. This is a bet that asynchronous knowledge sharing inside the repository scales better than real-time synchronization. By treating a markdown file as the team’s shared brain, you get persistence, legibility, and a perfect audit trail of every decision the team has made. Because this memory lives in project files rather than a live session, the team can also recover context after disconnects or restarts and continue from where it left off.

### 2. Context replication over context splitting

One of the biggest hurdles in AI development is the context window limit. When a single agent tries to do everything, the “working memory” gets crowded with meta-management, leading to hallucinations.

Squad solves this by ensuring the coordinator agent remains a thin router. It doesn’t do the work; it spawns specialists. Because each specialist runs as a separate inference call with its own large context window (e.g., up to 200K tokens on supported models), you aren’t splitting one context among four agents, you’re replicating repository context across them.

Running multiple specialists in parallel gives you multiple independent reasoning contexts operating simultaneously. This allows each agent to “see” the relevant parts of the repository without competing for space with the other agents’ thoughts.

### 3. Explicit memory in the prompt vs. implicit memory in the weights

We believe an AI team’s memory should be legible and versioned. You shouldn’t have to wonder what an agent “knows” about your project.

In Squad, an agent’s identity is built primarily on two repository files: a charter (who they are) and a history (what they’ve done), alongside shared team decisions. These are plain text. Because these live in your `.squad/` folder, the AI’s memory is versioned right alongside your code. When you clone a repo, you aren’t just getting the code; you are getting an already “onoboarded” AI team because their memory lives alongside the code directly in the repository.

## Lowering the barrier to multi-agent workflows

Our biggest win with Squad is that it makes it easy for anyone to get started with agentic development in a low-touch, low-ceremony way. You shouldn’t have to spend hours wrestling with infrastructure, learning complex prompt engineering, or managing convoluted CLI interactions just to get an AI team to help you write code.

To see what repository-native orchestration feels like, check out the [Squad repository](<https://github.com/bradygaster/squad>) and throw a squad at a problem to see how the workflow evolves.

---

## Tags:

* [AI](<https://github.blog/tag/ai/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [multi-agent workflows](<https://github.blog/tag/multi-agent-workflows/>)

## Written by

![Brady Gaster](<https://secure.gravatar.com/avatar/c4c1d68f4766046384a4c7ee192c152e0bf240745b6b84a0baa656e9b0abd0b1?s=200&d=mm&r=g>)

### [Brady Gaster](<https://github.blog/author/bradyg/>)

[@bradygaster](<https://github.com/bradygaster>)

Principal PM Architect in CoreAI Apps & Agents at Microsoft, where I build stuff to make it easier for people to party with Copilot in the cloud.


</details>
