# 用画布管理可见且可引导的 Agent 工作流

通过现代化改造与站点构建案例，说明将对话意图变成持久交互界面的模式，并讨论设计投入和运行效率。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-08-17](<https://github.blog/ai-and-ml/github-copilot/how-canvases-make-agentic-workflows-visible-steerable-and-cost-efficient/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/how-canvases-make-agentic-workflows-visible-steerable-and-cost-efficient/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/用画布管理可见且可引导的 Agent 工作流.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 画布如何使代理工作流程可见、可操纵且经济高效

当我在大学时，我加入了 VS Code 中的 AI 内联完成的第一个版本的测试版。这感觉就像是游戏规则的改变者。从那时起，GenAI 从根本上改变了软件开发：agents 和人类协同工作的混合团队，以开发人员为中心，充当远见者和协调者。我们现在正生活在这种转变之中。

![软件生命周期时间线 — 计划、构建、审查、交付 — 具有交织的绿色和紫色路径，代表人类和 AI agent 协同工作。](<https://github.blog/wp-content/uploads/2026/08/623371402-ddb0772d-e566-4895-b030-6be9b0fe304c.png?resize=1024%2C312>)

作为 GenAI 创新快速发展的自然副产品，我们现在拥有帮助我们规划、构建、审查和发布代码的工具。但在目前的状态下，许多工作流程仍然感觉脱节。上下文在线程和表面上丢失，并且花费太多时间来审查 agent 生成的工作。 Agents 生成更改的速度比任何人查看它们的速度都要快，并且大多数开发人员工具最初并不是为多 agent 编排而设计的。人们很容易忘记什么运行了，什么改变了，什么被验证了，什么仍然需要人类判断。

GitHub Copilot 应用程序是解决这个问题的重要一步。我特别喜欢并几乎每天使用的一项功能是**画布**。画布让开发人员和 agents 在耐用的共享表面上进行交互。画布不再将聊天视为工作发生的唯一场所，而是让工作在展开时变得可见、可引导和可批准。

## 聊天对于意图很有帮助，但对于持久执行却很弱

我仍然相信聊天是我们实现意图的最佳界面之一。在这里你可以思考、完善和指导。它快速且灵活，尤其是当问题仍然不明确时。

但是，一旦 agent 开始做实际工作，聊天就会变成一长串指令、日志、枢轴和更正。重要的部分在技术上已经存在，但被隐藏起来：计划、决策点、验证和批准时刻。如果你必须从历史中重建所有这些，那么你已经在缴纳协调税了。

画布通过为工作流程提供一个家来解决这个问题。它们使状态变得明确且持久。人类可以检查和指导。 Agents可以更新和进度。两者都可以保持一致，而无需不断重播上下文。

## 第一个版本：Java现代化工作室

我制作的第一批画布之一是**Java现代化工作室**。 Java 现代化正是可见性和治理至关重要的工作流程：评估、规划、迁移任务、验证门和交付准备。

在仅限聊天的体验中，这些步骤变得模糊在一起。您仍然可以继续前进，但大规模审计和信任变得更加困难，尤其是对于多个贡献者而言。团队不断问同样昂贵的问题：我们处于哪个阶段？做出了哪些决定？什么被阻止了？什么还需要人类的认可？

工作室使每个阶段变得明确且可检查。团队可以直接查看操作状态，而不是解析叙述历史。他们不用猜测发生了什么，而是可以验证它。人类审核员可以专注于高信号判断，而 agents 则保持执行在检查点之间移动。

![Java现代化工作室画布概述选项卡，显示现代化之旅（评估、修复、验证、交付）、编译阻止程序卡、自动驾驶选项、P0–P3严重性计数的评估结果以及检测到的堆栈。](<https://github.blog/wp-content/uploads/2026/08/623375232-cbf590d3-74d6-471d-be21-76add276959b.png?resize=2200%2C2494>)

[**探索 Java 现代化工作室画布 >**](<https://awesome-copilot.github.com/extension/appmod-cockpit/>)

## 第二个构建：Site Studio

之后，我为一个非常不同的工作流程构建了 **Site Studio**：创建和管理个人网站内容。它的内容较多，而不是迁移较多，但编排挑战是相似的：部分进度、迭代编辑、审查循环和状态转换。

在仅限聊天的流程中，内容可能会快速变化。一个部分被修改，然后再次修改，人们对当前内容的信心下降。反馈变得分散，草稿重复，势头减慢，因为每次迭代都是从重建上下文开始的。

Site Studio 使该状态保持持久。部分状态可见。当工作发生时，草稿值会被保留。人工审核点是明确的。 agent 可以继续移动，而人类可以在不丢失线索的情况下转向、批准或重定向。

![100% 完成时的 Site Studio 画布内容选项卡，包含可编辑部分 — 设计系统、英雄、导航、关于、会议演讲、视频和联系人 — 每个部分都显示已填充的字段和准备进行审核操作的标记。](<https://github.blog/wp-content/uploads/2026/08/623387227-87abbc64-0005-4816-a6f5-3c86756e1651.png?resize=1185%2C2623>)

**[探索 Site Studio 画布 >](<https://awesome-copilot.github.com/extension/site-studio/>)**

## 可重复的模式

在这两张画布上，我发现了相同的可重复蓝图：

1. 明确定义工作流程状态。
2. 提出重要的决策。
3. 坚持进度并立即起草。
4. 保持明确的人类认可点。

这将模型从逐提示交互转变为持久的协作工作流程。您不再将每个回合视为一个新的开始，而是开始将每个工作流程视为具有内存、结构和控制的系统。

## 成本和效率：是的，画布是一项投资

我还想明确说明成本：画布可以是一项投资。例如，Site Studio 花费了我大约 2,000 AI 积分，现代化画布花费了我大约 3,000 AI 积分。他们需要努力去设计和塑造好。

但从长远来看，尤其是对于重复的工作流程，这种投资是有回报的。耐用的表面可减少重复提示、减少上下文丢失、减少不必要的来回操作并减少返工。随着时间的推移，这可以节省时间和金钱，同时提高信任度和吞吐量。

所以对我来说，这不是“花更多的代币来获得更好的UX”。它“投资于更好的工作流程架构，使重复性工作变得更加高效、可预测和可管理。”

## 现已上市，真棒-copilot

我构建的画布 - **Java Modernization Studio ** 和 **Site Studio ** - [ 可以在 **awesome - copilot**](<https://awesome-copilot.github.com/extensions/>) 中提供给任何想要使用它们、改编它们或向他们学习。

如果您已经在使用 Copilot agents，实际的下一步是选择一个重复的工作流程，并使用 `/create-canvas` 围绕它构建一个最小的画布。从小事做起，开展实际工作，并从实际使用中迭代。如果它对您的团队有帮助，请将其回馈给 **awesome-copilot**，以便其他人也能受益。

我们仍处于这一转变的早期阶段，但方向很明确。 Agents可以加速执行。人类提供远见、判断力和责任感。画布是使这种伙伴关系真实、持久和可扩展的一种方式。

[使用 /create-canvas](<https://awesome-copilot.github.com/extensions/>) 构建您自己的画布并将其回馈给 Awesome-copilot >

---

## 标签：

* [太棒了-copilot](<https://github.blog/tag/awesome-copilot/>)
* [画布](<https://github.blog/tag/canvases/>)
* [开发人员生产力](<https://github.blog/tag/developer-productivity/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot 应用程序](<https://github.blog/tag/github-copilot-app/>)

## 撰写者

![阿扬·古普塔](<https://avatars.githubusercontent.com/u/74832088?v=4&s=200>)

### [阿扬·古普塔](<https://github.blog/author/ayangupt/>)

[@ayangupt](<https://github.com/ayangupt>)

Ayan Gupta 是 GitHub 的开发人员倡导者，他帮助开发人员和团队在 GitHub Copilot、Azure 和开源领域采用实用的、由 AI- 支持的开发工作流程。福布斯 30 在 30 获奖者之下，他之前与他人共同创办了一家专注于扩大健康和教育机会的科技企业。他今天的工作重点是使 agent 驱动的工作流程变得透明、可操纵且可重复 - 除此之外，他还喜欢木工和志愿服务。


## 原文对照

<details>
<summary>展开完整原文</summary>

# How canvases make agentic workflows visible, steerable, and cost-efficient

When I was in college, I joined the beta for one of the first versions of AI inline completions in VS Code. It felt like a game changer. Since then, GenAI has fundamentally changed software development: hybrid teams where agents and humans work in tandem, with the developer at the center as visionary and orchestrator. We are living in that transition right now.

![A software lifecycle timeline — Plan, Build, Review, Ship — with intertwining green and purple paths representing a human and an AI agent working in tandem.](<https://github.blog/wp-content/uploads/2026/08/623371402-ddb0772d-e566-4895-b030-6be9b0fe304c.png?resize=1024%2C312>)

As a natural byproduct of how fast innovation in GenAI has moved, we now have tools to help us plan, build, review, and ship code. But in the current state, many workflows still feel disjointed. Context gets lost across threads and surfaces, and too much time gets spent reviewing agent-generated work. Agents can produce changes faster than any human can review them, and most developer tools were not originally designed for multi-agent orchestration. It becomes easy to lose track of what ran, what changed, what was validated, and what still needs human judgment.

The GitHub Copilot app is a major step toward addressing this. One feature in particular that I’ve learned to love and use almost every day is **canvases**. Canvases let developers and agents interact on a durable, shared surface. Instead of treating chat as the only place where work happens, canvases make work visible, steerable, and approvable as it unfolds.

## Chat is great for intent, but weak for durable execution

I still believe chat is one of the best interfaces we have for intent. It’s where you can think, refine, and direct. It’s fast and flexible, especially when the problem is still ambiguous.

But once an agent starts doing real work, chat becomes a long scroll of instructions, logs, pivots, and corrections. The important parts are technically there, but buried: the plan, decision points, validations, and approval moments. If you have to reconstruct all of that from history, you’re already paying a coordination tax.

Canvases solve that by giving workflows a home. They make state explicit and persistent. Humans can inspect and guide. Agents can update and progress. Both can stay aligned without constantly replaying context.

## The first build: Java Modernization Studio

One of the first canvases I built was **Java Modernization Studio**. Java modernization is exactly the kind of workflow where visibility and governance matter: assessment, planning, migration tasks, validation gates, and readiness to ship.

In a chat-only experience, those steps blur together. You can still move forward, but it gets harder to audit and harder to trust at scale, especially with multiple contributors. Teams keep asking the same expensive questions: What stage are we in? What decisions were made? What is blocked? What still needs human approval?

The studio made each phase explicit and inspectable. Instead of parsing narrative history, teams could see operational state directly. Instead of guessing what happened, they could verify it. Human reviewers could focus on high-signal judgments while agents kept execution moving between checkpoints.

![The Java Modernization Studio canvas Overview tab, showing the modernization journey (Assess, Remediate, Validate, Ship), a compile-blocker card, a Run on autopilot option, assessment findings with P0–P3 severity counts, and the detected stack.](<https://github.blog/wp-content/uploads/2026/08/623375232-cbf590d3-74d6-471d-be21-76add276959b.png?resize=2200%2C2494>)

[**Explore the Java Modernization Studio canvas >**](<https://awesome-copilot.github.com/extension/appmod-cockpit/>)

## The second build: Site Studio

After that, I built **Site Studio** for a very different workflow: creating and managing personal site content. It’s content-heavy rather than migration-heavy, but the orchestration challenge is similar: section progress, iterative edits, review loops, and status transitions.

In a chat-only flow, content can drift quickly. A section gets revised, then revised again, and confidence drops in what is current. Feedback gets scattered, drafts repeat, and momentum slows because each iteration starts by rebuilding context.

Site Studio keeps that state durable. Section status is visible. Draft values are persisted as work happens. Human review points are explicit. The agent can keep moving while the human can steer, approve, or redirect without losing the thread.

![The Site Studio canvas Content tab at 100% completion, with editable sections — Design System, Hero, Navigation, About, Conference Talks, Videos, and Contact — each showing filled fields and a Mark ready for review action.](<https://github.blog/wp-content/uploads/2026/08/623387227-87abbc64-0005-4816-a6f5-3c86756e1651.png?resize=1185%2C2623>)

**[Explore the Site Studio canvas >](<https://awesome-copilot.github.com/extension/site-studio/>)**

## The repeatable pattern

Across both canvases, I found the same repeatable blueprint:

1. Define workflow states clearly.
2. Surface the decisions that matter.
3. Persist progress and drafts immediately.
4. Keep explicit human approval points.

This shifts the model from prompt-by-prompt interaction to durable collaborative workflows. You stop treating each turn like a fresh start and start treating each workflow like a system with memory, structure, and control.

## Cost and efficiency: yes, canvases are an investment

I also want to be explicit about cost: canvases can be an investment. For instance, Site Studio cost me about 2,000 AI credits, and the modernization canvas cost me about 3,000 AI credits. They take effort to design and shape well.

But in the long run, especially for repeated workflows, that investment pays back. Durable surfaces reduce repeated prompting, reduce context loss, reduce unnecessary back-and-forth, and reduce rework. Over time, that can save both time and money while improving trust and throughput.

So for me, this is not “spend more tokens for nicer UX.” It’s “invest in better workflow architecture so recurring work becomes more efficient, predictable, and governable.”

## Available now in awesome-copilot

The canvases I built—**Java Modernization Studio** and **Site Studio**—[are available in **awesome-copilot**](<https://awesome-copilot.github.com/extensions/>) for anyone who wants to use them, adapt them, or learn from them.

If you are already using Copilot agents, a practical next step is to pick one repeated workflow and build a minimal canvas around it with `/create-canvas`. Start small, run real work, and iterate from actual usage. If it helps your team, contribute it back to **awesome-copilot** so others can benefit too.

We’re still early in this transition, but the direction is clear. Agents can accelerate execution. Humans provide vision, judgment, and accountability. Canvases are one way to make that partnership real, durable, and scalable.

[Build your own canvas with /create-canvas](<https://awesome-copilot.github.com/extensions/>) and contribute it back to awesome-copilot >

---

## Tags:

* [awesome-copilot](<https://github.blog/tag/awesome-copilot/>)
* [canvases](<https://github.blog/tag/canvases/>)
* [developer productivity](<https://github.blog/tag/developer-productivity/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot app](<https://github.blog/tag/github-copilot-app/>)

## Written by

![Ayan Gupta](<https://avatars.githubusercontent.com/u/74832088?v=4&s=200>)

### [Ayan Gupta](<https://github.blog/author/ayangupt/>)

[@ayangupt](<https://github.com/ayangupt>)

Ayan Gupta is a Developer Advocate at GitHub, where he helps developers and teams adopt practical, AI-powered development workflows across GitHub Copilot, Azure, and open source. A Forbes 30 Under 30 honoree, he previously co-founded technology ventures focused on expanding access to wellness and education. His work today centers on making agent-driven workflows transparent, steerable, and repeatable — and outside of it, he enjoys woodworking and volunteering.


</details>
