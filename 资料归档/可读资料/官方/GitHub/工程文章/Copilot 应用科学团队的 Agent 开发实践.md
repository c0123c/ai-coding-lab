# Copilot 应用科学团队的 Agent 开发实践

复盘用编码 Agent 构建自动化系统的过程，介绍任务提示、架构组织与迭代策略，以及实际协作经验。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-03-31](<https://github.blog/ai-and-ml/github-copilot/agent-driven-development-in-copilot-applied-science/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/agent-driven-development-in-copilot-applied-science/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/Copilot 应用科学团队的 Agent 开发实践.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Agent 应用科学领域的 Copilot 驱动开发

我可能只是让自己自动化从事一份完全不同的工作......

这是软件工程师熟悉的模式，他们经常通过灵感、挫折，有时甚至是懒惰，构建系统来消除辛劳并专注于更具创造性的工作。然后，我们最终拥有并维护这些系统，为我们周围的其他人释放自动化的好处。

作为一名 AI 研究员，我最近超越了以前的可能性，并自动化地摆脱了我的 * 智力 * 辛劳。现在，我发现自己正在维护这个工具，以使 Copilot 应用科学团队的所有同事都能做同样的事情。

在这个过程中，我学到了很多关于如何使用[GitHub Copilot](<https://github.com/features/copilot>)进行有效创建和协作的知识。应用这些知识为我自己开启了一个令人难以置信的快速开发循环，并使我的团队成员能够构建满足他们需求的解决方案。

在我开始解释如何实现这一点之前，让我为这个项目的产生做好准备，以便您更好地了解您可以使用 GitHub Copilot 做的事情的范围。

## 动力

我的大部分工作涉及根据标准化评估基准（例如 [TerminalBench2](<https://www.tbench.ai/>) 或 [SWEBench-Pro](<https://scale.com/leaderboard/swe_bench_pro_public>)）来分析编码 agent 性能。这通常涉及仔细研究大量所谓的轨迹，这些轨迹本质上是执行任务时所采取的思维过程和行动的列表agents。

评估数据集中的每个任务都会产生自己的轨迹，显示 agent 如何尝试解决该任务。这些轨迹通常是包含数百行代码的 `.json` 文件。将其乘以基准测试集中的数十个任务，再乘以任何一天需要分析的许多基准运行，我们正在讨论数十万行代码需要分析。

这是一项不可能单独完成的任务，所以我通常会寻求 AI 的帮助。在分析新的基准测试运行时，我发现我不断重复相同的循环：我使用 GitHub Copilot 来显示轨迹中的模式，然后自己研究它们 - 将我必须读取的代码行数从数十万行减少到几百行。

然而，我的工程师看到了这个重复性的任务，并说：“我想自动化它。” Agents为我们提供了自动化这种智力工作的手段，因此`eval-agents`诞生了。

## 计划

工程和科学团队可以更好地合作。这是我着手解决这一新挑战时的指导原则。

因此，我在制定该项目的设计和实施策略时考虑了以下几个目标：

1. 让这些 agents 易于分享和使用
2. 轻松创作新作品 agents
3. 让编码 agents 成为贡献的主要工具

第一个和第二个项目是 GitHub 的命脉，是我在整个职业生涯中获得的价值观和技能，特别是在我担任 GitHub CLI 的 OSS 维护者期间。

然而，目标三对项目影响最大。我注意到，当我设置 GitHub Copilot 来帮助我有效地构建该工具时，它也使该项目更易于使用和协作。那次经历教会了我一些重要的教训，最终帮助我以意想不到的方式推动了第一个和第二个目标的实现。

## 让编码 agents 成为你的主要贡献者

我将首先描述我的代理编码设置：

* **编码agent**：Copilot CLI
* **使用的型号**：Claude Opus 4.6
* **IDE**：VSCode

还值得注意的是，我利用 Copilot SDK 来加速 agent 的创建，该创建由 Copilot CLI 提供支持。这让我可以访问现有的工具和 MCP 服务器，一种注册新工具和技能的方法，以及一大堆其他开箱即用的代理优点，我不必重新发明自己。

解决了这个问题后，我可以通过遵循一些核心原则来非常快速地简化整个开发过程：

* **提示策略**：agents在您以对话方式、冗长的方式以及在agent模式之前利用计划模式时效果最佳。
* **架构策略**：经常重构，经常更新文档，经常清理。
* **迭代策略**：“信任但验证”现在是“指责过程，而不是agents。”

发现并遵循这些策略导致了令人难以置信的现象：添加新的 agents 和功能既快速又简单。我们有五个人首次参与该项目，并且在不到三天的时间里，我们总共创建了 11 新 agents、四项新技能以及评估概念 - agent 工作流程（想想科学家的推理流）。这相当于在 345 文件中更改了 **+28,858/-2,884 行代码 **。

天哪！

下面，我将详细介绍这三个原则以及它们如何实现这一令人惊叹的协作和创新壮举。

## 提示策略

我们知道，AI 编码 agents 确实擅长解决范围广泛的问题，但需要掌握更复杂的问题，您只能将其委托给更高级的工程师。

因此，如果您希望您的 agent 像工程师一样行事，请将其视为工程师。引导其思维，过度解释你的假设，并利用其研究速度在做出改变之前进行计划。我发现将一些关于我正在思考的问题的意识流思考放入提示中并在计划模式下使用 Copilot 比给出简洁的问题陈述或解决方案更有效。

以下是我编写的提示示例，用于向该工具添加更强大的回归测试：

```
> /plan I've recently observed Copilot happily updating tests to fit its new paradigms even though those tests shouldn't be updated. How can I create a reserved test space that Copilot can't touch or must reserve to protect against regressions?
```

这导致了来回的结果，最终导致了一系列类似于合同测试的护栏，只能由人类更新。我知道了自己想要什么，通过对话，Copilot 帮助我找到了正确的解决方案。

事实证明，使人类工程师最有效地完成工作的因素与使这些 agents 有效地完成工作的因素是相同的。

## 架构策略

工程师们，有福了！还记得您想要进行的所有那些重构以使代码库更具可读性、您从未有时间编写的测试以及您希望在入职时存在的文档吗？现在，在构建 agent 优先的存储库时，它们是您可以做的最重要的事情。

必须将这项工作优先于新功能工作的日子已经一去不复返了，因为当您拥有一个维护良好的、agent优先的项目时，使用Copilot交付功能变得微不足道。

我花了大部分时间在这个项目上重构名称和文件结构，记录新功能或模式，并为我发现的问题添加测试用例。我什至花了几个周期来清理agents（像你的初级工程师）在实现所有这些新功能和更改时可能错过的无用代码。

这项工作使 Copilot 可以轻松浏览代码库并理解模式，就像任何其他工程师一样。

我什至可以问：“根据我现在所知道的，我将如何以不同的方式设计它？”然后我可以证明实际上返回并重新构建整个项目是合理的（当然，在 Copilot 的帮助下）。

这是梦想成真！

这引出了我的最后一点指导。

## 迭代策略

随着 agents 和模型的改进，我已经从“信任但验证”的心态转变为更信任而不是怀疑的心态。这反映了该行业对待人类团队的方式：“归咎于流程，而不是人。”这就是最有效的团队的运作方式，因为人们会犯错误，所以我们围绕这一现实构建系统。

这种[无责文化](<https://circleci.com/blog/value-of-blameless-culture/.>)的理念为团队迭代和创新提供了心理安全感，因为他们知道如果犯了错误，他们不会受到指责。核心原则是我们实施流程和护栏来防止错误，如果确实发生错误，我们会从中吸取教训并引入新的流程和护栏，以便我们的团队不会再犯同样的错误。

将同样的理念应用于 agent 驱动的开发对于解锁这个令人难以置信的快速迭代管道至关重要。这意味着我们添加流程和护栏来帮助防止 agent 犯错误，但当它确实犯错误时，我们会添加额外的护栏和流程（例如更强大的测试和更好的提示），以便 agent 不会再犯同样的错误。更进一步意味着必须实践良好的[CI/CD原则](<https://github.com/resources/articles/ci-cd>)。

严格打字等实践确保 agent 符合接口。强大的 linter 对 agent 施加实施规则，使其遵循良好的模式和实践。集成、端到端和合同测试（手动构建的成本可能很高）在 agent 的帮助下实施起来会变得便宜得多，同时让您确信新的更改不会破坏现有功能。

当Copilot在其开发循环中提供这些工具时，它可以检查自己的工作。您正在为成功做好准备，就像您为项目的成功而准备初级工程师一样。

## 把它们放在一起

当您为 agent 驱动的开发设置代码库时，这一切对您的开发循环意味着什么：

1. 使用 `/plan` 规划 Copilot 的新功能。
   * 迭代计划。
   * 确保测试包含在计划中。
   * 确保文档更新包含在计划中并在代码实施之前完成。这些可以作为您计划之外的附加指南。
2. 让Copilot在`/autopilot`上实现该功能。
3. 提示 Copilot 使用 Copilot 代码审查 agent 启动审查循环。对我来说，通常是这样的：`request Copilot Code Review, wait for the review to finish, address any relevant comments, and then re-request review. Continue this loop until there are no more relevant comments.`
4. 人工审查。这是我强制执行前几节中讨论的模式的地方。

此外，在功能循环之外，请确保尽早并经常使用以下内容提示 Copilot：

* `/plan Review the code for any missing tests, any tests that may be broken, and dead code`
* `/plan Review the code for any duplication or opportunities for abstraction`
* `/plan Review the documentation and code to identify any documentation gaps. Be sure to update the copilot-instructions.md to reflect any relevant changes`

我让这些每周自动运行一次，但我经常发现自己整周都在运行它们，因为新功能和修复会进入以维护我的 agent 驱动的开发环境。

## 随身携带这个

最初对不可能重复的分析任务感到沮丧，后来变成了更有趣的事情：一种思考我们如何构建软件、如何协作以及如何成长为工程师的新方式。

以编码 agent 为先的心态构建 agents 从根本上改变了我的工作方式。这不仅仅是关于自动化的胜利——尽管在三天之内观看四位科学家交付 11 agents、四项技能和一个全新概念，这绝对是了不起的。这是关于这种开发风格迫使您优先考虑的事情：干净的架构、完整的文档、有意义的测试和深思熟虑的设计——我们一直知道这些事情很重要，但从未有时间去做。

初级工程师的类比不断得到证明。你要很好地引导他们，给他们清晰的背景，建立护栏，这样他们的错误就不会变成灾难，然后相信他们会成长。如果出了问题，你就会责怪流程。不是agent。如果我希望您从中学到一件事，那就是让您成为一名出色的工程师和出色的队友的技能与让您擅长使用 Copilot 进行构建的技能相同。这项技术是新的。原则则不然。

因此，去清理该代码库，编写您一直推迟的文档，然后开始像团队中的最新成员一样对待您的 Copilot。您可能会自动从事职业生涯中最有趣的工作。

认为我疯了？好吧，试试这个：

1. 下载[CopilotCLI](<https://github.com/features/copilot/cli>)
2. 在任何存储库中激活 Copilot CLI：`cd <repo_path> && copilot`
3. 粘贴以下提示：`/plan Read <link to this blog post> and help me plan how I could best improve this repo for agent-first development`

---

## 标签：

* [AI agents](<https://github.blog/tag/ai-agents/>)
* [自动化](<https://github.blog/tag/automation/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)

## 撰写者

![泰勒 McGoffin](<https://avatars.githubusercontent.com/u/5891697?v=4&s=200>)

### [泰勒 McGoffin](<https://github.blog/author/jtmcg/>)

[@jtmcg](<https://github.com/jtmcg>)

Tyler 是 Copilot 应用科学团队的高级应用研究员。他在科学研究、教育、游戏 design/development 和软件方面拥有不拘一格的背景。对于他目前的职位，他最喜欢的部分是加速他的团队和整个组织的学习和研究。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Agent-driven development in Copilot Applied Science

I may have just automated myself into a completely different job…

This is a familiar pattern among software engineers, who often, through inspiration, frustration, or sometimes even laziness, build systems to remove toil and focus on more creative work. We then end up owning and maintaining those systems, unlocking that automated goodness for the rest of those around us.

As an AI researcher, I recently took this beyond what was previously possible and have automated away my *intellectual* toil. And now I find myself maintaining this tool to enable all my peers on the Copilot Applied Science team to do the same.

During this process, I learned a lot about how to effectively create and collaborate using [GitHub Copilot](<https://github.com/features/copilot>). Applying these learnings has unlocked an incredibly fast development loop for myself as well as enabled my team mates to build solutions to fit their needs.

Before I get into explaining how I made this possible, let me set the stage for what spawned this project so you better understand the scope of what you can do with GitHub Copilot.

## The impetus

A large part of my job involves analyzing coding agent performance as measured against standardized evaluation benchmarks, like [TerminalBench2](<https://www.tbench.ai/>) or [SWEBench-Pro](<https://scale.com/leaderboard/swe_bench_pro_public>). This often involves poring through tons of what are called trajectories, which are essentially lists of the thought processes and actions agents take while performing tasks.

Each task in an evaluation dataset produces its own trajectory, showing how the agent attempted to solve that task. These trajectories are often `.json` files with hundreds of lines of code. Multiply that over dozens of tasks in a benchmark set and again over the many benchmark runs needing analysis on any given day, and we’re talking hundreds of thousands of lines of code to analyze.

It’s an impossible task to do alone, so I would typically turn to AI to help. When analyzing new benchmark runs, I found that I kept repeating the same loop: I used GitHub Copilot to surface patterns in the trajectories then investigated them myself—reducing the number of lines of code I had to read from hundreds of thousands to a few hundred.

However, the engineer in me saw this repetitive task and said, “I want to automate that.” Agents provide us with the means to automate this kind of intellectual work, and thus `eval-agents` was born.

## The plan

Engineering and science teams work better together. That was my guiding principle as I set about solving this new challenge.

Thus, I approached the design and implementation strategy of this project with a couple of goals in mind:

1. Make these agents easy to share and use
2. Make it easy to author new agents
3. Make coding agents the primary vehicle for contributions

Bullets one and two are in GitHub’s lifeblood and are values and skills I’ve gained throughout my career, especially during my stint as an OSS maintainer on the GitHub CLI.

However, goal three shaped the project the most. I noticed that when I set GitHub Copilot up to help me build the tool effectively, it also made the project easier to use and collaborate on. That experience taught me a few key lessons, which ultimately helped push the first and second goals forward in ways I didn’t expect.

## Making coding agents your primary contributor

I’ll start by describing my agentic coding setup:

* **Coding agent**: Copilot CLI
* **Model used**: Claude Opus 4.6
* **IDE**: VSCode

It’s also noteworthy that I leveraged the Copilot SDK to accelerate agent creation, which is powered under the hood by the Copilot CLI. This gave me access to existing tools and MCP servers, a way to register new tools and skills, and a whole bunch of other agentic goodness out of the box that I didn’t have to reinvent myself.

With that out of the way, I could streamline the whole development process very quickly by following a few core principles:

* **Prompting strategies**: agents work best when you’re conversational, verbose, and when you leverage planning modes before agent modes.
* **Architectural strategies**: refactor often, update docs often, clean up often.
* **Iteration strategies**: “trust but verify” is now “blame process, not agents.”

Uncovering and following these strategies led to an incredible phenomenon: adding new agents and features was fast and easy. We had five folks jump into the project for the first time, and we created a total of 11 new agents, four new skills, and the concept of eval-agent workflows (think scientist streams of reasoning) in less than three days. That amounted to a change of **+28,858/-2,884 lines of code** across 345 files.

Holy crap!

Below, I’ll go into detail about these three principles and how they enabled this amazing feat of collaboration and innovation.

## Prompting strategies

We know that AI coding agents are really good at solving well-scoped problems but need handholding for the more complex problems you’d only entrust to your more senior engineers.

So, if you want your agent to act like an engineer, treat it like one. Guide its thinking, over-explain your assumptions, and leverage its research speed to plan before jumping into changes. I found it far more effective to put some stream-of-consciousness musings about a problem I was chewing on into a prompt and working with Copilot in planning mode than to give it a terse problem statement or solution.

Here’s an example of a prompt I wrote to add more robust regression tests to the tool:

```
> /plan I've recently observed Copilot happily updating tests to fit its new paradigms even though those tests shouldn't be updated. How can I create a reserved test space that Copilot can't touch or must reserve to protect against regressions?
```

This resulted in a back and forth that ultimately led to a series of guardrails akin to contract testing that can only be updated by humans. I had an idea of what I wanted, and through conversation, Copilot helped me get to the right solution.

It turns out that the things that make human engineers the most effective at doing their jobs are the same things that make these agents effective at doing theirs.

## Architectural strategies

Engineers, rejoice! Remember all those refactors you wanted to do to make the codebase more readable, the tests you never had time to write, and the docs you wish had existed when you onboarded? They’re now the most important thing you can be working on when building an agent-first repository.

Gone are the days where deprioritizing this work over new feature work was necessary, because delivering features with Copilot becomes trivial when you have a well-maintained, agent-first project.

I’ve spent most of my time on this project refactoring names and file structures, documenting new features or patterns, and adding test cases for problems that I’ve uncovered as I go. I’ve even spent a few cycles cleaning up the dead code that the agents (like your junior engineers) may have missed while implementing all these new features and changes.

This work makes it easy for Copilot to navigate the codebase and understand the patterns, just like it would for any other engineer.

I can even ask, “Knowing what I know now, how would I design this differently?” And I can then justify actually going back and rearchitecting the whole project (with the help of Copilot, of course).

It’s a dream come true!

And this leads me to my last bit of guidance.

## Iteration strategies

As agents and models have improved, I have moved from a “trust but verify” mindset to one that is more trusting than doubtful. This mirrors how the industry treats human teams: “blame process, not people.” It’s how the most effective teams operate, because people make mistakes, so we build systems around that reality.

This idea of [blameless culture](<https://circleci.com/blog/value-of-blameless-culture/.>) provides psychological safety for teams to iterate and innovate, knowing that they won’t be blamed if they make a mistake. The core principle is that we implement processes and guardrails to protect against mistakes, and if a mistake does happen, we learn from it and introduce new processes and guardrails so that our teams won’t make the same mistake again.

Applying this same philosophy to agent-driven development has been fundamental to unlocking this incredibly rapid iteration pipeline. That means we add processes and guardrails to help prevent the agent from making mistakes, but when it does make a mistake, we add additional guardrails and processes—like more robust tests and better prompts—so the agent can’t make the same mistake again. Taking this one step further means that practicing good [CI/CD principles](<https://github.com/resources/articles/ci-cd>) is a must.

Practices like strict typing ensure the agent conforms to interfaces. Robust linters impose implementation rules on the agent that keep it following good patterns and practices. And integration, end-to-end, and contract tests—which can be expensive to build manually—become much cheaper to implement with agent assistance, while giving you confidence that new changes don’t break existing features.

When Copilot has these tools available in its development loop, it can check its own work. You’re setting it up for success, much in the same way you’d set up a junior engineer for success in your project.

## Putting it all together

Here’s what all this means for your development loop when you’ve got your codebase set up for agent-driven development:

1. Plan a new feature with Copilot using `/plan`.
   * Iterate on the plan.
   * Ensure that testing is included in the plan.
   * Ensure that docs updates are included in the plan and done before code is implemented. These can serve as additional guidelines that live beside your plan.
2. Let Copilot implement the feature on `/autopilot`.
3. Prompt Copilot to initiate a review loop with the Copilot Code Review agent. For me, it’s often something like: `request Copilot Code Review, wait for the review to finish, address any relevant comments, and then re-request review. Continue this loop until there are no more relevant comments.`
4. Human review. This is where I enforce the patterns I discussed in the previous sections.

Additionally, outside of your feature loop, be sure you’re prompting Copilot early and often with the following:

* `/plan Review the code for any missing tests, any tests that may be broken, and dead code`
* `/plan Review the code for any duplication or opportunities for abstraction`
* `/plan Review the documentation and code to identify any documentation gaps. Be sure to update the copilot-instructions.md to reflect any relevant changes`

I have these run automatically once a week, but I often find myself running them throughout the week as new features and fixes go in to maintain my agent-driven development environment.

## Take this with you

What started as a frustration with an impossibly repetitive analysis task turned into something far more interesting: a new way of thinking about how we build software, how we collaborate, and how we grow as engineers.

Building agents with a coding agent-first mindset has fundamentally changed how I work. It’s not just about the automation wins—though watching four scientists ship 11 agents, four skills, and a brand-new concept in under three days is nothing short of remarkable. It’s about what this style of development forces you to prioritize: clean architecture, thorough documentation, meaningful tests, and thoughtful design—the things we always knew mattered but never had time for.

The analogy to a junior engineer keeps proving itself out. You onboard them well, give them clear context, build guardrails so their mistakes don’t become disasters, and then trust them to grow. If something goes wrong, you blame the process. Not the agent. If there’s one thing I want you to take away from this, it’s that the skills that make you a great engineer and a great teammate are the same skills that make you great at building with Copilot. The technology is new. The principles aren’t.

So go clean up that codebase, write that documentation you’ve been putting off, and start treating your Copilot like the newest member of your team. You might just automate yourself into the most interesting work of your career.

Think I’m crazy? Well, try this:

1. Download [Copilot CLI](<https://github.com/features/copilot/cli>)
2. Activate Copilot CLI in any repo: `cd <repo_path> && copilot`
3. Paste in the following prompt: `/plan Read <link to this blog post> and help me plan how I could best improve this repo for agent-first development`

---

## Tags:

* [AI agents](<https://github.blog/tag/ai-agents/>)
* [automation](<https://github.blog/tag/automation/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)

## Written by

![Tyler McGoffin](<https://avatars.githubusercontent.com/u/5891697?v=4&s=200>)

### [Tyler McGoffin](<https://github.blog/author/jtmcg/>)

[@jtmcg](<https://github.com/jtmcg>)

Tyler is a Sr. Applied Researcher on the Copilot Applied Science team. He has an eclectic background in scientific research, education, game design/development, and software. His favorite part about his current position is accelerating learning and research for his team and the organization as a whole.


</details>
