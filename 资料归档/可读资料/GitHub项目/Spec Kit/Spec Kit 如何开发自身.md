# Spec Kit 如何开发自身

介绍该项目内部的 Agent 软件开发生命周期，展示任务组织和验证方式；属于项目实践，需按自身场景选择参考。

- 来源：Spec Kit
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-10-10](<https://github.com/github/spec-kit/commit/0443760542e7e5269724744a2ac55c4bd386d095>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.github.com/spec-kit/guides/agentic-sdlc.html>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 如何开发自身.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

规格套件如何开发规格套件：Agentic SDLC | 规格套件文档
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      

      

      

  

  
    
      
        
          
            
规格套件
          
          
            
          
          
            
              
                
                
              
            
          
        
      
    

    

      
        

          
        

        

Spec Kit 如何开发 Spec Kit：Agentic SDLC

Spec Kit 自己的开发将代理工作与传统软件
交付相结合。 Agents 评估功能请求，帮助开发重大更改，
 并调查报告的错误。该项目还使用规范套件本身，其中
其流程适合，而确定性测试和发布保持普通
GitHub操作和维护人员决定继续或合并。

公共工作流程和历史特征为本案例
研究提供了证据。它们显示了项目 SDLC 的不同路径，包括项目使用 Spec Kit 本身的位置 
。

项目的软件开发生命周期

传统阶段将项目的实践定位在其 SDLC 中。

流程图 LR
 P[“规划”] --> Q[“需求”] --> D[“设计”] --> I[“开发”]
 I --> T[“测试”] --> R[“部署”] --> M["维护"]

这些阶段只是一张地图，而不是必需的顺序。工作可以在任何
起始材料和目标需要的地方开始：规划中的功能请求、
测试中的现有更改或维护中的错误报告。阶段可以
重叠、重复或跳过；一个功能问题可以提供评估，
记录需求，并进行设计讨论。箭头显示一条
熟悉的路径，而不是SDLC阶段之间的强制转换。
维护的反馈可以为下一个规划周期提供信息。

谁来做这项工作

这些模式说明了这里的工作是如何完成的；它们并不是详尽的
分类法或活动清单。一个舞台可以将它们结合起来。

定期自动化。脚本和机器人遵循预定义的规则来运行 
 检查、建议依赖项更新并发布版本。他们不
解释功能请求或选择设计。

Agent 自动化。编码 agent 解释上下文并生成 
 评估、SDD 工件、建议的更改或测试报告。它可以通过贡献者调用的 Spec Kit 命令、贡献者自己的 agent、
 或标签触发的存储库工作流程来运行 
。在 GitHub
Actions 上运行 agent 不会使其推理成为确定性脚本。

星号 (★) 标记代理作品：散文段落之前和
时间线项目之后。一个人 (👤) 强调了人类在 
 舞台叙事中的贡献，包括在 agent 的帮助下完成的工作。

人类的工作。无论有或没有 agent 的帮助，人们都可以构建问题、讨论方法、编写或修改 
 更改以及审查证据。维护者
应用标签来启动存储库工作流程并决定是否继续
或合并​​。这些交接是人为决定，而不是证明人们
撰写了每个评估、规范、修复或测试报告。

项目在每个阶段如何运作

1. 规划：评估功能请求

功能可以通过
功能请求表进入规划。
它询问问题、建议的解决方案、替代方案、组件和使用
案例。由此产生的问题始于需求分类；提交它不会
自动启动agent。

★ 当功能请求被标记为评估时，
功能评估代理工作流程
从结帐中提供指定CLI，安装规格套件捆绑的
评估扩展，并遵循其接收、研究、定义、
形状并决定阶段。 agent 发布了证据并表示，
 需要澄清，否则就取消对该问题的裁决。这是使用规格套件本身的代理
工作流程。

👤 贡献者提出请求；维护者决定是否
启动评估以及如何根据评估结果采取行动。一次发现通知
该规划决定；它不会自动打开功能PR或
启动SDD。

2. 要求：记录问题或规范中的意图

👤 贡献者可以通过问题陈述、用例和验收标准在同一功能问题
中记录早期需求，然后
在讨论过程中澄清或修改意图。对于有限的变化，
可能就足够了；它不必成为 SDD spec.md。

★ 对于重大更改，贡献者可以根据项目的构成，使用 Spec Kit 的核心 SDD 命令扩展需求 
。规范套件 
 在 
 历史捆绑器工作中使用了此过程：
 提交包含 
 指定捆绑命令的构成和功能规范。这是 SDD 对 
 功能进行测试的证据；捆绑器的工作与当前的功能评估
工作流程不同。 
贡献指南
要求贡献者使用SDD测试相关更改，同时允许小修复
使用正常问题和PR流程。生成的规格/工件
通常会被忽略；链接提交保留历史快照。

3. 设计：选择合适规模的方法

👤 特征形式提出的解决方案和替代方案可以引发
设计讨论；贡献者和维护者可以在 PR 审核期间完善该方法 
。并非每个问题都需要单独的 SDD 计划。
较大的更改需要事先与维护者讨论并达成一致，如 
 贡献指南 
 所解释的那样。当决策在更改中保持有用时，存储库
会保留CLI、
集成、
和工作流程步骤
设计文档。

★ 捆绑器 SDD 快照 
 展示了更深层次的路径：其计划、研究、数据模型、合同和 
 任务通过 /speckit.plan 和 
/speckit.tasks. 使设计可操作。这显示了规格套件本身的使用情况，而没有 
 呈现每个更改的默认详细程度。

4. 开发：进行可审查的更改

捆绑器实现
在记录的SDD工作后添加了指定捆绑。这是项目中具体的
规范主导的功能。其他有界变更使用
普通问题、PR、审查和测试流程。

👤 贡献者可以直接或在
agent的帮助下实施和修改更改。维护者会审查生成的 PR 及其证据，甚至 
 当 agent 产生建议的更改时也是如此。

★ 贡献者可以使用自己的agents，独立于存储库的
工作流程。由于该工作可能在 diff 中不可见，因此 
 贡献政策 
 要求披露工具、模型、设置或模式以及 AI
 援助的范围。 Agent 创作的提交和评论需要自己的归属；
 已知的错误修复和社区目录工作流程可以豁免，因为它们的 
agent 身份是固有的。披露提供了出处，而不是较低的
证据或审查栏。

5. 测试：验证意图和行为

★ 捆绑器工作包括附加了缺失任务的
收敛通道
。这是规范套件的 SDD 流程 
 根据意图检查实现而不是将第一遍 
 视为完成的一个示例。 
错误测试工作流程
使用agent来选择相关测试，但测试命令本身
仍然确定性地运行。

另外，传统的 GitHub 操作运行 
Python 测试和 Ruff，
Markdown linting 用于文档和 ShellCheck 用于 shell 脚本，
 和 CodeQL
 在 PR 上运行并推送到 main。代理收敛补充了那些
独立检查。

👤 贡献者提供更改的测试和复制证据；
维护者评估补丁和证据是否满足规定的需求，
而不仅仅是检查是否通过。

6. 部署：在没有 agent 的情况下发布

规格套件存储库使用常规 GitHub 操作进行交付。 
手动调度发布触发器
设置版本，创建标签并打开发布PR。该标签触发
常规
GitHub发布。
单独调度的工作流程
构建并发布到PyPI；
主触发器上的文档/更改
DocFX部署。
这些常规操作无需 agent
 或 Spec Kit 命令即可处理确定性交付。

👤 维护者选择何时开始发布以及是否指定
版本而不是使用自动补丁增量。他们随后发布了该标签的 
PyPI 发布并审核了发布 PR。

7. 维护：调查、修复和学习

👤 记者通过
错误报告表提供观察到的和预期的行为和重现
步骤。
维护人员对报告进行分类，选择何时请求每个代理步骤，
并在合并之前审查建议的修复和测试证据。报告可以
发布之前或之后出现；新发现的需求可以满足下一个
计划周期，而不是折叠到维修中。

★ 存储库对问题使用单独的
错误评估、
错误修复、
和错误测试
代理工作流程。他们通过人工控制的切换将诊断、修复和
验证分开；该修复建议作为草案
PR供维护者审查。如今，这些项目 
 工作流程不消耗 Spec Kit 捆绑的 
 错误扩展，尽管它为用户提供了相应的 
 评估、修复和测试流程。让工作流使用该扩展 
 是未来工作的方向，而不是当前的功能或承诺的 
 版本。

代理和传统工作流程均在 GitHub 操作上运行。 
 的变化在于 agent 是否解释证据或提出更改。
Dependabot 
 还建议每周 pip 和 GitHub 操作为 
 维护者更新 PR审查；这是传统的自动化，而不是代理工作流程。

可扩展性和社区实践

可扩展性贯穿了这个SDLC：该项目构建和分发
进程以及使用它们。该团队提供了
评估包
和错误修复包
，每个包都将扩展与可恢复的规范套件工作流程和人工
审查门相结合。它还提供了
精益预设
来演示塑造核心SDD指导的替代方法。这些是 
Spec Kit 自己的扩展、预设、工作流程和捆绑包，
而不是存储库的 GitHub 操作工作流程。

★ 除了核心功能交付之外，代理社区提交工作流程
扩展，
预设，
和捆绑
验证提交元数据并在 PR 草案中提出目录更改建议
维护者审查。

目录发现不审核或认可社区代码；用户在使用前必须
检查第三方组件。

我们如何从 SDLC 变成代理人 SDLC

代理实践被分层到现有的 SDLC 中，而不是取代 
 传统的检查和发布。提交历史记录显示了这种组合
是如何随着时间的推移而出现的。这些是选定的里程碑，而不是详尽的
变更日志。同一颗星标志着代理里程碑，包括使用 Spec
Kit 的 SDD 流程；传统的自动化和策略没有标记。

月份
发生了什么变化

八月 2025

GitHub 添加了发布自动化。

九月 2025

添加了文档部署。
贡献指南开始要求披露 AI 协助，包括贡献者运行的 agents。

十月 2025

Markdown linting 已添加到 PR 和主要检查中。

二月 2026

Dependabot 开始提议每周 pip 和 GitHub 操作依赖项更新 PR。
添加了 CodeQL 工作流程用于代码扫描。
Pytest CI 开始对 PR 运行测试并推送到main.
Ruff linting 已添加到 Python CI 工作流程中。

五月2026

扩展提交获得了目录验证和 PR 草案。 ★
预设提交已获得目录验证和草稿 PR。 ★

六月 2026

添加了代理错误评估工作流程。 ★
捆绑器功能使用Spec Kit的SDD流程来生成其规范、计划和任务。 ★
PyPI发布获得了常规工作流程。
ShellCheck开始检查CI中的shell脚本。

七月 2026

错误修复通过人工切换扩展了代理问题工作流程。 ★
Bug-test增加了单独的验证阶段。 ★
捆绑提交获得了目录自动化。 ★

八月 2026

功能评估工作流程开始使用 Spec Kit 的功能请求评估扩展。 ★

九月 2026

AI 披露开始要求 agent、模型、设置和范围。
贡献指南添加了 agent 创作的提交和评论的归属。

这并不是从传统自动化到 agents 的切换。测试、lint、
扫描和发布在GitHub操作中保持可重复的工作，而
披露使贡献者运行的agents可见。该项目为有界任务添加了代理 
 目录和 bug 工作流程，在大量 
 功能上使用了 SDD，随后使用 Spec Kit 的评估 
 扩展进行功能评估。这些是单独的添加，而不是每个问题都必须遵循的步骤。

他们一起给出项目选择。对于功能，评估可以告知
人类关于请求的决定；大量的实施工作可以使用 SDD
 来指定和规划它，就像捆绑器所做的那样。这些进程之间没有自动切换
。一个小的变化可能会停留在一个问题和PR中，而
一个错误可以通过人工评估、修复和验证。
代码更改仍然面临常规检查和维护人员审查；问题
判决和目录提案都有自己的人为大门。

生产实践，而不是规定的配方

对于这个公共开源项目，一个新问题是不受信任的输入，而不是运行 agent 的 
 权限。维护者使用其标签激活每个存储库拥有的
代理工作流程，并决定工作何时进展；贡献者
也可以自带agents，但须接受披露和审查。这些是
此项目的故意边界，而不是规格套件的固有限制。

具有不同信任模型的封闭项目可以自动化评估、规范、实施和测试之间的更多交接
，同时
保持适合风险的权限、证据和审查。 Spec Kit 的 
 生产组合不是规定的配方：团队可以在 
 不同的地方采用 agents，无论有或没有 Spec Kit 的流程。这种组合可以根据项目的需求和实践而改变
。

在这里，代理并不意味着无人：贡献者仍然会带来问题、
需求和变化；维护人员引导产品方向，指导
交付并审查证据。传统的自动化处理可重复的
检查和发布，而agents帮助完成需要
解释的有限工作。该部门的目标是将人类时间集中在判断上，尽管
时间表显示的是采用情况，而不是衡量的时间节省情况。

        
编辑本页
        

        

      

      
        
      
    

    

    
      
        
Spec Kit - 规范驱动的开发工具包


## 原文对照

<details>
<summary>展开完整原文</summary>

How Spec Kit Develops Spec Kit: An Agentic SDLC | Spec Kit Documentation 
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      

      

      

  

  
    
      
        
          
            
            Spec Kit
          
          
            
          
          
            
              
                
                
              
            
          
        
      
    

    

      
        

          
        

        

How Spec Kit Develops Spec Kit: An Agentic SDLC

Spec Kit's own development combines agentic work with conventional software
delivery. Agents assess feature requests, help develop substantial changes,
and investigate reported bugs. The project also uses Spec Kit itself where
its processes fit, while deterministic tests and releases remain ordinary
GitHub Actions and maintainers make the decisions to proceed or merge.

Public workflows and a historical feature provide the evidence for this case
study. They show different paths through the project's SDLC, including where
the project uses Spec Kit itself.

The project's software development life cycle

The traditional stages locate the project's practices across its SDLC.

flowchart LR
    P["Planning"] --> Q["Requirements"] --> D["Design"] --> I["Development"]
    I --> T["Testing"] --> R["Deployment"] --> M["Maintenance"]

These stages are a map, not a required sequence. Work can begin wherever
its starting material and goal call for it: a feature request in planning,
an existing change in testing, or a bug report in maintenance. Stages can
overlap, repeat, or be skipped; one feature issue can feed an assessment,
record requirements, and carry design discussion. The arrows show one
familiar path, not mandatory transitions between SDLC stages.
Feedback from maintenance can inform the next planning cycle.

Who does the work

These modes illustrate how work gets done here; they are not an exhaustive
taxonomy or inventory of activities. A stage can combine them.

Regular automation. Scripts and bots follow predefined rules to run
checks, propose dependency updates, and publish releases. They do not
interpret feature requests or choose designs.

Agent automation. A coding agent interprets context and produces an
assessment, SDD artifacts, a proposed change, or a test report. It may run
through a contributor-invoked Spec Kit command, a contributor's own agent,
or a label-triggered repository workflow. Running an agent on GitHub
Actions does not make its reasoning a deterministic script.

A star (★) marks agentic work: before prose paragraphs and after
timeline items. A person (👤) highlights human contributions in the
stage narratives, including work done with an agent's help.

Human work. People can frame issues, discuss approaches, write or revise
changes, and review evidence, with or without an agent's help. Maintainers
apply labels to start repository workflows and decide whether to proceed
or merge. Those handoffs are human decisions, not evidence that people
authored every assessment, specification, fix, or test report.

How the project works at each stage

1. Planning: assess feature requests

A feature can enter planning through the
feature request form.
It asks for the problem, proposed solution, alternatives, component, and use
cases. The resulting issue starts with needs-triage; filing it does not
automatically launch an agent.

★ When a feature request is labeled for assessment, the
feature-assess agentic workflow
provisions the Specify CLI from the checkout, installs Spec Kit's bundled
assess extension, and follows its intake, research, define,
shape, and decide stages. The agent posts its evidence and a go,
needs-clarification, or kill verdict to the issue. This is an agentic
workflow using Spec Kit itself.

👤 Contributors frame the request; maintainers decide whether to
initiate assessment and how to act on its verdict. A go finding informs
that planning decision; it does not automatically open a feature PR or
start SDD.

2. Requirements: record the intent in issues or specs

👤 Contributors can record early requirements in the same feature issue
through its problem statement, use cases, and acceptance criteria, then
clarify or revise the intent during discussion. For a bounded change, that
may be enough; it does not have to become an SDD spec.md.

★ For substantial changes, contributors can instead expand the requirements
with Spec Kit's core SDD commands against the project's constitution. Spec Kit
used this process on itself in the
historical bundler work:
the commit contains a constitution and feature specification for the
specify bundle command. This is evidence of SDD dogfooding for that
feature; the bundler work is distinct from the current feature-assess
workflow. The
contribution guide
asks contributors to test relevant changes with SDD while allowing small fixes
to use the normal issue and PR process. Generated specs/ artifacts are
normally gitignored; the linked commit preserves a historical snapshot.

3. Design: choose an approach at the right scale

👤 The feature form's proposed solution and alternatives can seed a
design discussion; contributors and maintainers can refine the approach
during PR review. A separate SDD plan is not required for every issue.
Larger changes need prior discussion and agreement with maintainers, as the
contribution guide
explains. When decisions should remain useful across changes, the repository
keeps CLI,
integration,
and workflow-step
design documents.

★ The bundler SDD snapshot
illustrates the deeper path: its plan, research, data model, contracts, and
tasks made the design actionable through /speckit.plan and
/speckit.tasks. This shows where Spec Kit itself was used without
presenting that level of detail as the default for every change.

4. Development: make reviewable changes

The bundler implementation
added specify bundle after the recorded SDD work. This is a concrete
specification-led feature in the project. Other bounded changes use the
ordinary issue, PR, review, and test process.

👤 Contributors can implement and revise changes directly or with an
agent's help. Maintainers review the resulting PR and its evidence, even
when an agent produced the proposed change.

★ Contributors can use their own agents, independently of the repository's
workflows. Because that work may not be visible in a diff, the
contribution policy
requires disclosure of the tool, model, settings or mode, and extent of AI
assistance. Agent-authored commits and comments need their own attribution;
the known bug-fix and community-catalog workflows are exempt because their
agent identity is inherent. Disclosure provides provenance, not a lower
evidence or review bar.

5. Testing: verify both intent and behavior

★ The bundler work includes a
convergence pass
that appended a missing task. This is one example of Spec Kit's SDD process
checking implementation against intent rather than treating the first pass
as complete. The
bug-test workflow
uses an agent to select relevant tests, but the test commands themselves
still run deterministically.

Separately, conventional GitHub Actions run
Python tests and Ruff,
Markdown linting for documentation and ShellCheck for shell scripts,
and CodeQL
on PRs and pushes to main. Agentic convergence complements those
independent checks.

👤 Contributors supply tests and reproduction evidence for changes;
maintainers assess whether the patch and evidence address the stated need,
not just whether the checks passed.

6. Deployment: publish without an agent

The Spec Kit repository uses regular GitHub Actions for delivery. The
manually dispatched release trigger
sets the version, creates a tag, and opens a release PR. The tag triggers a
conventional
GitHub Release.
A separately dispatched workflow
builds and publishes to PyPI;
a docs/ change on main triggers
DocFX deployment.
These conventional Actions handle deterministic delivery without an agent
or a Spec Kit command.

👤 Maintainers choose when to start a release and whether to specify
a version rather than use the automatic patch increment. They later dispatch
PyPI publishing for that tag and review the release PR.

7. Maintenance: investigate, repair, and learn

👤 Reporters supply observed and expected behavior and reproduction
steps through the
bug report form.
Maintainers triage the report, choose when to request each agentic step,
and review the proposed fix and test evidence before merging. Reports can
arise before or after release; a newly discovered need can feed the next
planning cycle instead of being folded into a repair.

★ The repository uses separate
bug-assess,
bug-fix,
and bug-test
agentic workflows on issues. They separate diagnosis, remediation, and
verification with human-controlled handoffs; the fix is proposed as a draft
PR for maintainer review. Today these project
workflows do not consume Spec Kit's bundled
bug extension, even though it offers a corresponding
assess, fix, test process for users. Having the workflows use that extension
is a direction for future work, not a current capability or a promised
release.

Agentic and conventional workflows both run on GitHub Actions. What changes
is whether an agent interprets evidence or proposes a change.
Dependabot
also proposes weekly pip and GitHub Actions dependency-update PRs for
maintainer review; it is conventional automation, not an agentic workflow.

Extensibility and community practice

Extensibility cuts across this SDLC: the project builds and distributes
processes as well as using them. The team ships the
assess bundle
and bugfix bundle,
each combining an extension with a resumable Spec Kit workflow and a human
review gate. It also ships the
lean preset
to demonstrate an alternative way of shaping core SDD guidance. These are
Spec Kit's own extensions, presets, workflows, and bundles,
not the repository's GitHub Actions workflows.

★ Beyond core feature delivery, agentic community-submission workflows for
extensions,
presets,
and bundles
validate submission metadata and propose catalog changes in draft PRs for
maintainer review.

Catalog discovery does not audit or endorse community code; users must
review third-party components before use.

How we went from SDLC to an agentic SDLC

Agentic practices were layered into an existing SDLC, not substituted for
conventional checks and releases. The commit history shows how that mix
emerged over time. These are selected milestones, not an exhaustive
changelog. The same star marks agentic milestones, including use of Spec
Kit's SDD process; conventional automation and policies are unmarked.

Month
What changed

August 2025

GitHub Release automation was added.

September 2025

Documentation deployment was added.
Contribution guidelines began requiring disclosure of AI assistance, including contributor-run agents.

October 2025

Markdown linting was added to PR and main checks.

February 2026

Dependabot began proposing weekly pip and GitHub Actions dependency-update PRs.
A CodeQL workflow was added for code scanning.
Pytest CI began running tests on PRs and pushes to main.
Ruff linting was added to the Python CI workflow.

May 2026

Extension submissions gained catalog validation and draft PRs. ★
Preset submissions gained catalog validation and draft PRs. ★

June 2026

An agentic bug-assess workflow was added. ★
The bundler feature used Spec Kit's SDD process to produce its specification, plan, and tasks. ★
PyPI publishing gained a conventional workflow.
ShellCheck began checking shell scripts in CI.

July 2026

Bug-fix extended the agentic issue workflow with a human handoff. ★
Bug-test added a separate verification stage. ★
Bundle submissions gained catalog automation. ★

August 2026

The feature-assess workflow began using Spec Kit's assess extension on feature requests. ★

September 2026

AI disclosure began requiring agent, model, settings, and extent.
Contribution guidance added attribution for agent-authored commits and comments.

This was not a handoff from conventional automation to agents. Tests, lint,
scanning, and publishing kept repeatable work in GitHub Actions, while
disclosure made contributor-run agents visible. The project added agentic
catalog and bug workflows for bounded tasks, used SDD on a substantial
feature, and later made feature assessment consume Spec Kit's assess
extension. These were separate additions, not steps every issue must follow.

Together they give the project choices. For features, assess can inform a
human decision about a request; substantial implementation work can use SDD
to specify and plan it, as the bundler did. There is no automatic handoff
between those processes. A small change can stay in an issue and PR, while
a bug can move through human-gated assessment, repair, and verification.
Code changes still face conventional checks and maintainer review; issue
verdicts and catalog proposals have their own human gates.

A production practice, not a prescribed recipe

For this public open-source project, a new issue is untrusted input, not
permission to run an agent. A maintainer activates each repository-owned
agentic workflow with its label and decides when work advances; contributors
can also bring their own agents, subject to disclosure and review. These are
deliberate boundaries for this project, not inherent limits of Spec Kit.

A closed project with a different trust model could automate more handoffs
between assessment, specification, implementation, and testing, while
keeping permissions, evidence, and review appropriate to the risk. Spec Kit's
production mix is not a prescribed recipe: teams can adopt agents in
different places, with or without Spec Kit's processes. That mix can change
with the project's needs and practices.

Here, agentic does not mean human-free: contributors still bring problems,
requirements, and changes; maintainers steer product direction, guide
delivery, and review evidence. Conventional automation handles repeatable
checks and publishing, while agents help with bounded work that requires
interpretation. This division aims to focus human time on judgment, though
the timeline shows adoption rather than measured time savings.

        
          Edit this page
        

        

      

      
        
      
    

    

    
      
        
          Spec Kit - A specification-driven development toolkit


</details>
