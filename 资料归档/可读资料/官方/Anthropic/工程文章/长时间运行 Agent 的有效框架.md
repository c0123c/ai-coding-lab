# 长时间运行 Agent 的有效框架

介绍初始化 Agent 与编码 Agent 的分工，用功能清单、启动脚本、进度记录、Git 提交和端到端测试支持跨会话推进，防止过早宣告完成。

- 来源：Anthropic／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-11-26](<https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents>)；更新：[2025-11-26](<https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/工程文章/长时间运行 Agent 的有效框架.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

适合长时间跑步的有效背带 agents \ AnthropicSkip 到主要 contentSkip 到页脚
研究
政策
承诺
学习
NewsTry ClaudeEngineering 在 Anthropic
适合长时间跑步的有效背带 agents
十一月出版 26, 2025
Agents 仍然面临跨多个上下文窗口工作的挑战。我们向人类工程师寻求灵感，为长时间跑步创造更有效的安全带 agents.

随着 AI agents 的能力越来越强，开发人员越来越多地要求他们承担需要花费数小时甚至数天时间的复杂任务。然而，让agents在多个上下文窗口中取得一致的进展仍然是一个悬而未决的问题。
长期运行的agents的核心挑战是它们必须在离散的会话中工作，并且每个新会话开始时都不记得之前发生了什么。想象一下一个由工程师轮班工作的软件项目，每个新工程师到达时都不记得上一个班次发生了什么。由于上下文窗口有限，并且大多数复杂项目无法在单个窗口内完成，agents 需要一种方法来弥合编码会话之间的差距。
我们开发了一种两重解决方案，使 Claude Agent SDK 能够跨多个上下文窗口有效工作：一个初始化程序 agent，用于在第一次运行时设置环境，以及一个编码agent 的任务是在每个会话中取得增量进展，同时为下一个会话留下清晰的工件。您可以在随附的快速入门中找到代码示例。
长期运行的 agent 问题
Claude Agent SDK 是一个功能强大的通用 agent 工具，擅长编码以及需要模型使用工具收集上下文、计划和执行的其他任务。它具有压缩等上下文管理功能，使 agent 能够在不耗尽上下文窗口的情况下处理任务。理论上，考虑到这种设置，agent应该可以在任意长时间内继续做有用的工作。
但是，压缩还不够。即使像 Opus 4.5 这样的前沿编码模型在跨多个上下文窗口的循环中在 Claude Agent SDK 上运行，如果只给出高级提示（例如“构建 claude.ai 的克隆”），即使是开箱即用的前沿编码模型也无法构建生产质量的 Web 应用程序。
Claude 的失败表现为两种模式。首先，agent 倾向于尝试一次做太多事情——本质上是试图一次性完成应用程序。通常，这会导致模型在实现过程中脱离上下文，从而使下一个会话以半实现且未记录的功能开始。然后，agent 必须猜测发生了什么，并花费大量时间尝试让基本应用程序再次运行。即使使用压缩也会发生这种情况，压缩并不总是将完全清晰的指令传递给下一个agent。
第二种故障模式通常会在项目后期发生。在构建了一些功能后，稍后的 agent 实例会环顾四周，看到已取得进展，并声明工作已完成。
这将问题分解为两部分。首先，我们需要设置一个初始环境，为给定提示所需的所有功能奠定基础，从而设置 agent 逐步、逐个功能地工作。其次，我们应该促使每个 agent 朝着其目标逐步取得进展，同时在会话结束时使环境处于清洁状态。我们所说的“干净状态”是指适合合并到主分支的代码：没有重大错误，代码有序且文档齐全，一般来说，开发人员可以轻松地开始开发新功能，而无需先清理不相关的混乱。
在内部进行实验时，我们使用两部分解决方案解决了这些问题：
初始化程序agent：第一个 agent 会话使用专门的提示，要求模型设置初始环境：一个 init.sh 脚本、一个 claude-progress.txt 文件（用于记录 agents 所做的操作）以及一个初始 git 提交（显示哪些文件）添加。
编码agent：每个后续会话都要求模型取得增量进展，然后留下结构化更新。1
这里的关键见解是找到一种方法让agents在从新的上下文窗口开始时快速了解工作状态，这是通过claude-progress.txt文件和git历史记录来完成的。这些实践的灵感来自于了解有效的软件工程师每天所做的事情。
环境管理
在更新的Claude4提示指南中，我们分享了一些多上下文窗口工作流程的最佳实践，包括“为第一个上下文窗口使用不同提示”的线束结构。这种“不同的提示”要求初始化器 agent 设置具有未来编码 agents 有效工作所需的所有必要上下文的环境。在这里，我们对这种环境的一些关键组件进行了更深入的探讨。
功能列表
为了解决agent一次性开发应用程序或过早地认为项目完成的问题，我们提示初始化程序agent编写一份扩展用户初始提示的功能需求的综合文件。在 claude.ai 克隆示例中，这意味着 200 功能，例如“用户可以打开新聊天，输入查询，按 Enter 键，然后查看 AI 响应。”这些功能最初都被标记为“失败”，以便以后的编码 agents 可以清楚地了解完整功能的轮廓。

{
 "category"：“功能”，
 "description"：“新聊天按钮创建新的对话”，
 "steps"：[
“导航到主窗口界面",
 "点击'新聊天'按钮",
 "验证是否已创建新对话",
 "检查聊天区域是否显示欢迎状态",
 "验证对话是否显示在侧边栏中"
 ],
 "passes": false
 }
复制
我们提示编码agents仅通过更改通行证字段的状态来编辑此文件，并且我们使用措辞强硬的指令，例如“删除或编辑测试是不可接受的，因为这可能会导致功能缺失或出现错误。”经过一些实验，我们最终决定使用 JSON 来实现此目的，因为与 Markdown 文件相比，模型不太可能不当更改或覆盖 JSON 文件。
增量进度
考虑到这个初始环境脚手架，然后询问编码的下一次迭代 agent一次只处理一项功能。事实证明，这种增量方法对于解决 agent 一次做太多事情的倾向至关重要。
一旦增量工作，模型在进行代码更改后使环境保持干净状态仍然很重要。在我们的实验中，我们发现引发此行为的最佳方法是要求模型使用描述性提交消息将其进度提交到 git，并将其进度摘要写入进度文件中。这允许模型使用 git 恢复错误的代码更改并恢复代码库的工作状态。
这些方法还提高了效率，因为它们消除了 agent 必须猜测发生了什么并花时间尝试让基本应用程序再次工作的需要。
测试
我们观察到的最后一个主要故障模式是Claude 倾向于在没有适当测试的情况下将功能标记为完整。如果没有明确的提示，Claude往往会更改代码，甚至使用单元测试或针对开发服务器的curl命令进行测试，但无法识别该功能无法端到端工作。
在构建Web应用程序的情况下，一旦明确提示使用浏览器自动化工具并将所有测试作为一个网络应用程序，Claude在端到端验证功能方面表现出色。人类用户会的。 Claude 在测试 claude.ai 克隆时通过 Puppeteer MCP 服务器拍摄的屏幕截图。 
为Claude提供这些类型的测试工具极大地提高了性能，因为agent能够识别并修复仅从代码中不明显的错误。
仍然存在一些问题，例如Claude的愿景和浏览器自动化工具的限制，使其难以识别每种类型错误。例如，Claude 无法通过 Puppeteer MCP 查看浏览器本机警报模式，因此依赖这些模式的功能往往会出现更多错误。
加快速度
上述所有内容都到位后，每个编码agent 会被提示执行一系列步骤来了解其方向，其中一些非常基本，但仍然很有帮助：
运行 pwd 以查看您正在工作的目录。您只能编辑此目录中的文件。
阅读 git 日志和进度文件以了解最近所做的工作。
阅读功能列表文件并选择尚未完成的最高优先级功能。
这种方法可以在每个会话中保存Claude一些令牌，因为它不必弄清楚如何测试代码。它还有助于要求初始化器 agent 编写一个可以运行开发服务器的 init.sh 脚本，然后在实现新功能之前运行基本的端到端测试。
在 claude.ai 克隆的情况下，这意味着 agent 始终启动本地开发服务器并使用 Puppeteer MCP 开始新聊天、发送消息并接收响应。这确保了 Claude 可以快速识别应用程序是否处于损坏状态，并立即修复任何现有错误。如果 agent 开始实现新功能，则可能会使问题变得更糟。
考虑到所有这些，典型的会话会以以下助理消息开始：
[助理] 我将首先了解情况并了解项目的当前状态。
[工具使用] <bash - pwd>
[工具 使用] <read - claude-progress.txt>
[工具 使用] <read - feature_list.json>
[助手] 让我检查一下git日志，看看最近的工作。
[工具使用] <bash - git log --oneline -20>
[助手] 现在让我检查是否有init.sh脚本来重新启动服务器。
<Starts the development server>
[助手] 太棒了！现在让我导航到应用程序并验证一些基本功能是否仍然有效。
<Tests basic functionality>
[助手] 根据我的验证测试，我可以看到基本功能运行良好。核心聊天功能、主题切换、对话加载和错误处理都运行正常。现在让我更全面地回顾一下tests.json文件，了解下一步需要实现什么。
<Starts work on a new feature>复制
Agent故障模式和解决方案ProblemInitializerAgent行为编码AgentBehaviorClaude宣告整个胜利项目太早。设置功能列表文件：根据输入规范，设置一个结构化的 JSON 文件，其中包含端到端功能描述列表。在会话开始时阅读功能列表文件。选择一个功能来开始工作。Claude 使环境处于有错误或未记录的进度的状态。写入初始 git 存储库和进度注释文件。通过读取进度注释文件和 git 提交日志来启动会话，并在开发服务器上运行基本测试以捕获任何未记录的错误。通过编写 git 提交和进度更新来结束会话。Claude 将功能标记为过早完成。设置功能列表文件。自我验证所有功能。仅在仔细测试后将功能标记为“通过”。Claude必须花时间弄清楚如何运行应用程序。编写可以运行开发服务器的init.sh脚本。通过阅读init.sh开始会话。总结长时间运行中的四种常见故障模式和解决方案AI agents。
未来的工作
这项研究展示了长期运行的agent工具中的一组可能的解决方案，使模型能够在许多上下文窗口中取得增量进展。然而，仍然存在悬而未决的问题。
最值得注意的是，目前尚不清楚单一通用编码agent是否在跨上下文中表现最佳，或者是否可以通过多agent架构实现更好的性能。似乎合理的是，专门的 agents（例如测试 agent、质量保证 agent 或代码清理 agent）可以在整个软件开发生命周期的子任务上做得更好。
此外，此演示针对全栈 Web 应用程序开发进行了优化。未来的方向是将这些发现推广到其他领域。这些经验教训中的部分或全部很可能可以应用于科学研究或金融建模等所需的长期运行的代理任务类型。

致谢
由 Justin Young 撰写。特别感谢 David Hershey、Prithvi Rajasakeran、Jeremy Hadfield、Naia Bouscal、Michael Tingley、Jesse Mu、Jake Eaton、Marius Buleandara、Maggie Vo、Pedram Navid、Nadine Yasser 和 Alex Notov 的贡献。
这项工作反映了 Anthropic 多个团队的集体努力，他们使Claude 安全地进行长期自主软件工程，尤其是代码 RL 和 Claude Code 团队。欢迎有兴趣贡献的候选人通过anthropic.com/careers.申请

脚注
1. 在这种情况下，我们将它们称为单独的 agents，只是因为它们具有不同的初始用户提示。系统提示、工具集和整体 agent 工具在其他方面是相同的。
获取开发者通讯
产品更新、操作方法、社区焦点等等。每月发送到您的收件箱。
如果您想接收我们的每月开发者通讯，请提供您的电子邮件地址。您可以随时取消订阅。
产品展示
Claude
Claude Code
Claude Code 企业
Claude 协同办公
@Claude
Claude 设计
Claude 科学
Claude 安全性
Claude 在 Chrome 中
Claude 为了 Microsoft 365
技能
下载应用程序
定价
登录到 Claude
型号
神话
寓言
作品
十四行诗
俳句
解决方案
AI agents
代码现代化
编码
商业
客户支持
网络安全
企业
财务顾问
金融服务
政府
医疗保健
高等教育
K-12 教师
法律
生命科学
非营利组织
销售
小型企业
Claude 平台
概述
开发者文档
定价
生态系统
市场
区域合规性
Claude 上 AWS
Google 云
Microsoft 铸造厂
控制台登录
资源
博客
Claude 合作伙伴网络
社区
连接器
课程
客户案例
开发者博客
工程于 Anthropic
活动
插件
技术支持： Claude
服务合作伙伴
教程
使用案例
节目
校园
前沿学院
科学家
初创公司
帮助和安全
可用性
状态
支持中心
公司简介
Anthropic
职业机会
领导力
政策
经济期货
研究
新闻动态
Claude的宪法
Claude 军团
继续思考
政策 AI 指数
负责任的扩展政策
安全性和合规性
透明度
条款和政策
隐私政策
消费者健康数据隐私政策
负责任的披露政策
服务条款：商业
服务条款：消费者
服务条款： US K-12
数据处理协议： US K-12
使用政策© 2026 Anthropic PBC


## 原文对照

<details>
<summary>展开完整原文</summary>

Effective harnesses for long-running agents \ AnthropicSkip to main contentSkip to footer
Research
Policy
Commitments
Learn
NewsTry ClaudeEngineering at Anthropic
Effective harnesses for long-running agents
Published Nov 26, 2025
Agents still face challenges working across many context windows. We looked to human engineers for inspiration in creating a more effective harness for long-running agents.

As AI agents become more capable, developers are increasingly asking them to take on complex tasks requiring work that spans hours, or even days. However, getting agents to make consistent progress across multiple context windows remains an open problem.
The core challenge of long-running agents is that they must work in discrete sessions, and each new session begins with no memory of what came before. Imagine a software project staffed by engineers working in shifts, where each new engineer arrives with no memory of what happened on the previous shift. Because context windows are limited, and because most complex projects cannot be completed within a single window, agents need a way to bridge the gap between coding sessions.
We developed a two-fold solution to enable the Claude Agent SDK to work effectively across many context windows: an initializer agent that sets up the environment on the first run, and a coding agent that is tasked with making incremental progress in every session, while leaving clear artifacts for the next session. You can find code examples in the accompanying quickstart.
The long-running agent problem
The Claude Agent SDK is a powerful, general-purpose agent harness adept at coding, as well as other tasks that require the model to use tools to gather context, plan, and execute. It has context management capabilities such as compaction, which enables an agent to work on a task without exhausting the context window. Theoretically, given this setup, it should be possible for an agent to continue to do useful work for an arbitrarily long time.
However, compaction isn’t sufficient. Out of the box, even a frontier coding model like Opus 4.5 running on the Claude Agent SDK in a loop across multiple context windows will fall short of building a production-quality web app if it’s only given a high-level prompt, such as “build a clone of claude.ai.”
Claude’s failures manifested in two patterns. First, the agent tended to try to do too much at once—essentially to attempt to one-shot the app. Often, this led to the model running out of context in the middle of its implementation, leaving the next session to start with a feature half-implemented and undocumented. The agent would then have to guess at what had happened, and spend substantial time trying to get the basic app working again. This happens even with compaction, which doesn’t always pass perfectly clear instructions to the next agent.
A second failure mode would often occur later in a project. After some features had already been built, a later agent instance would look around, see that progress had been made, and declare the job done.
This decomposes the problem into two parts. First, we need to set up an initial environment that lays the foundation for all the features that a given prompt requires, which sets up the agent to work step-by-step and feature-by-feature. Second, we should prompt each agent to make incremental progress towards its goal while also leaving the environment in a clean state at the end of a session. By “clean state” we mean the kind of code that would be appropriate for merging to a main branch: there are no major bugs, the code is orderly and well-documented, and in general, a developer could easily begin work on a new feature without first having to clean up an unrelated mess.
When experimenting internally, we addressed these problems using a two-part solution:
Initializer agent: The very first agent session uses a specialized prompt that asks the model to set up the initial environment: an init.sh script, a claude-progress.txt file that keeps a log of what agents have done, and an initial git commit that shows what files were added.
Coding agent: Every subsequent session asks the model to make incremental progress, then leave structured updates.1
The key insight here was finding a way for agents to quickly understand the state of work when starting with a fresh context window, which is accomplished with the claude-progress.txt file alongside the git history. Inspiration for these practices came from knowing what effective software engineers do every day.
Environment management 
In the updated Claude 4 prompting guide, we shared some best practices for multi-context window workflows, including a harness structure that uses “a different prompt for the very first context window.” This “different prompt” requests that the initializer agent set up the environment with all the necessary context that future coding agents will need to work effectively. Here, we provide a deeper dive on some of the key components of such an environment.
Feature list
To address the problem of the agent one-shotting an app or prematurely considering the project complete, we prompted the initializer agent to write a comprehensive file of feature requirements expanding on the user’s initial prompt. In the claude.ai clone example, this meant over 200 features, such as “a user can open a new chat, type in a query, press enter, and see an AI response.” These features were all initially marked as “failing” so that later coding agents would have a clear outline of what full functionality looked like.

{
    "category": "functional",
    "description": "New chat button creates a fresh conversation",
    "steps": [
      "Navigate to main interface",
      "Click the 'New Chat' button",
      "Verify a new conversation is created",
      "Check that chat area shows welcome state",
      "Verify conversation appears in sidebar"
    ],
    "passes": false
  }
Copy
We prompt coding agents to edit this file only by changing the status of a passes field, and we use strongly-worded instructions like “It is unacceptable to remove or edit tests because this could lead to missing or buggy functionality.” After some experimentation, we landed on using JSON for this, as the model is less likely to inappropriately change or overwrite JSON files compared to Markdown files.
Incremental progress
Given this initial environment scaffolding, the next iteration of the coding agent was then asked to work on only one feature at a time. This incremental approach turned out to be critical to addressing the agent’s tendency to do too much at once.
Once working incrementally, it’s still essential that the model leaves the environment in a clean state after making a code change. In our experiments, we found that the best way to elicit this behavior was to ask the model to commit its progress to git with descriptive commit messages and to write summaries of its progress in a progress file. This allowed the model to use git to revert bad code changes and recover working states of the code base.
These approaches also increased efficiency, as they eliminated the need for an agent to have to guess at what had happened and spend its time trying to get the basic app working again.
Testing
One final major failure mode that we observed was Claude’s tendency to mark a feature as complete without proper testing. Absent explicit prompting, Claude tended to make code changes, and even do testing with unit tests or curl commands against a development server, but would fail recognize that the feature didn’t work end-to-end.
In the case of building a web app, Claude mostly did well at verifying features end-to-end once explicitly prompted to use browser automation tools and do all testing as a human user would. Screenshots taken by Claude through the Puppeteer MCP server as it tested the claude.ai clone. 
Providing Claude with these kinds of testing tools dramatically improved performance, as the agent was able to identify and fix bugs that weren’t obvious from the code alone.
Some issues remain, like limitations to Claude’s vision and to browser automation tools making it difficult to identify every kind of bug. For example, Claude can’t see browser-native alert modals through the Puppeteer MCP, and features relying on these modals tended to be buggier as a result.
Getting up to speed
With all of the above in place, every coding agent is prompted to run through a series of steps to get its bearings, some quite basic but still helpful:
Run pwd to see the directory you’re working in. You’ll only be able to edit files in this directory.
Read the git logs and progress files to get up to speed on what was recently worked on.
Read the features list file and choose the highest-priority feature that’s not yet done to work on.
This approach saves Claude some tokens in every session since it doesn’t have to figure out how to test the code. It also helps to ask the initializer agent to write an init.sh script that can run the development server, and then run through a basic end-to-end test before implementing a new feature.
In the case of the claude.ai clone, this meant that the agent always started the local development server and used the Puppeteer MCP to start a new chat, send a message, and receive a response. This ensured that Claude could quickly identify if the app had been left in a broken state, and immediately fix any existing bugs. If the agent had instead started implementing a new feature, it would likely make the problem worse.
Given all this, a typical session starts off with the following assistant messages:
[Assistant] I'll start by getting my bearings and understanding the current state of the project.
[Tool Use] <bash - pwd>
[Tool Use] <read - claude-progress.txt>
[Tool Use] <read - feature_list.json>
[Assistant] Let me check the git log to see recent work.
[Tool Use] <bash - git log --oneline -20>
[Assistant] Now let me check if there's an init.sh script to restart the servers.
<Starts the development server>
[Assistant] Excellent! Now let me navigate to the application and verify that some fundamental features are still working.
<Tests basic functionality>
[Assistant] Based on my verification testing, I can see that the fundamental functionality is working well. The core chat features, theme switching, conversation loading, and error handling are all functioning correctly. Now let me review the tests.json file more comprehensively to understand what needs to be implemented next.
<Starts work on a new feature>Copy
Agent failure modes and solutions ProblemInitializer Agent Behavior Coding Agent BehaviorClaude declares victory on the entire project too early.Set up a feature list file: based on the input spec, set up a structured JSON file with a list of end-to-end feature descriptions.Read the feature list file at the beginning of a session. Choose a single feature to start working on.Claude leaves the environment in a state with bugs or undocumented progress.An initial git repo and progress notes file is written.Start the session by reading the progress notes file and git commit logs, and run a basic test on the development server to catch any undocumented bugs. End the session by writing a git commit and progress update.Claude marks features as done prematurely.Set up a feature list file.Self-verify all features. Only mark features as “passing” after careful testing.Claude has to spend time figuring out how to run the app.Write an init.sh script that can run the development server.Start the session by reading init.sh.Summarizing four common failure modes and solutions in long-running AI agents.
Future work
This research demonstrates one possible set of solutions in a long-running agent harness to enable the model to make incremental progress across many context windows. However, there remain open questions.
Most notably, it’s still unclear whether a single, general-purpose coding agent performs best across contexts, or if better performance can be achieved through a multi-agent architecture. It seems reasonable that specialized agents like a testing agent, a quality assurance agent, or a code cleanup agent, could do an even better job at sub-tasks across the software development lifecycle.
Additionally, this demo is optimized for full-stack web app development. A future direction is to generalize these findings to other fields. It’s likely that some or all of these lessons can be applied to the types of long-running agentic tasks required in, for example, scientific research or financial modeling.

Acknowledgements
Written by Justin Young. Special thanks to David Hershey, Prithvi Rajasakeran, Jeremy Hadfield, Naia Bouscal, Michael Tingley, Jesse Mu, Jake Eaton, Marius Buleandara, Maggie Vo, Pedram Navid, Nadine Yasser, and Alex Notov for their contributions.
This work reflects the collective efforts of several teams across Anthropic who made it possible for Claude to safely do long-horizon autonomous software engineering, especially the code RL & Claude Code teams. Interested candidates who would like to contribute are welcome to apply at anthropic.com/careers.

Footnotes
1. We refer to these as separate agents in this context only because they have different initial user prompts. The system prompt, set of tools, and overall agent harness was otherwise identical.
Get the developer newsletter
Product updates, how-tos, community spotlights, and more. Delivered monthly to your inbox.
Please provide your email address if you'd like to receive our monthly developer newsletter. You can unsubscribe at any time.
Products
Claude
Claude Code
Claude Code Enterprise
Claude Cowork
@Claude
Claude Design
Claude Science
Claude Security
Claude in Chrome
Claude for Microsoft 365
Skills
Download app
Pricing
Log in to Claude
Models
Mythos
Fable
Opus
Sonnet
Haiku
Solutions
AI agents
Code modernization
Coding
Commerce
Customer support
Cybersecurity
Enterprise
Financial advisors
Financial services
Government
Healthcare
Higher education
K-12 teachers
Legal
Life sciences
Nonprofits
Sales
Small business
Claude Platform
Overview
Developer docs
Pricing
Ecosystem
Marketplace
Regional compliance
Claude on AWS
Google Cloud
Microsoft Foundry
Console login
Resources
Blog
Claude partner network
Community
Connectors
Courses
Customer stories
Developer blog
Engineering at Anthropic
Events
Plugins
Powered by Claude
Service partners
Tutorials
Use cases
Programs
Campus
Frontier Academy
Scientists
Startups
Help and security
Availability
Status
Support center
Company
Anthropic
Careers
Leadership
Policy
Economic Futures
Research
News
Claude’s Constitution
Claude Corps
Keep thinking
Policy on the AI Exponential
Responsible Scaling Policy
Security and compliance
Transparency
Terms and policies
Privacy policy
Consumer health data privacy policy
Responsible disclosure policy
Terms of service: Commercial
Terms of service: Consumer
Terms of Service: US K-12
Data Processing Agreement: US K-12
Usage policy© 2026 Anthropic PBC


</details>
