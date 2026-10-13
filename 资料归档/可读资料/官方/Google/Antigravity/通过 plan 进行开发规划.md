# 通过 /plan 进行开发规划

介绍先探索代码、澄清需求和创建可审查计划，再由用户决定进入实现的流程，说明计划产物与运行模式的区别。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/plan/>)）；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://antigravity.google/docs/plan/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/通过 plan 进行开发规划.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 计划（/plan）

/plan 命令有助于结构化规划、代码库探索、
 和需求发现，然后直接跳转到代码执行。对于
不平凡的任务，/plan确保在修改文件之前可以很好地理解复杂的更改、
正确确定范围并进行组织。

## 概述

在澄清边缘情况之前编写代码可能会导致浪费精力、
不必要的差异或回归。虽然标准提示会立即开始
修改文件，但/plan引入了一个有意的规划阶段，该阶段会探索
工作区、识别依赖关系并为
您的审阅提供架构决策。

/plan 工作流程有助于弥合初始想法和代码执行之间的差距：

- 无副作用的探索：agent 读取文件、检查调用
图表，并分析依赖关系，而无需过早编写代码。

- 交互式发现：如果未指定要求，agent 会询问
有针对性的问题来澄清权衡。

- 可审查的工件：agent 起草了一份结构化的实施计划
具有清晰任务分解和
验证检查点的工件。

- 用户引导执行：您保留完全控制权来留下内嵌评论，
请求调整，或单击“继续”开始实施。

## /plan 如何运作

当您调用 /plan 时，Antigravity 会运行一个多阶段规划和发现 
 工作流程，旨在生成可操作的实施计划：

- 分析和发现：agent 分析您的提示、检查工作区配置并检查
受影响的文件。

- 澄清和访谈：如果规范不明确或多种技术方法可行，
agent 使用交互式问题提示和 
 结构化选项发起重点访谈。

- 结构化计划创建：需求明确后，agent 会生成实施计划工件。

- 用户审核和批准：agent 在审核窗格中显示计划工件。您可以查看
建议的步骤、留下内联注释或调整单个任务。

## 何时使用/plan

/plan命令在以下开发场景中特别有价值：

- 复杂的重构：重构模块、升级库或
跨多个文件重构接口。

- 不明确的需求：当以开放性问题开始一项功能时，
未知的限制或竞争的架构方法。

- 高风险更改：用于数据库迁移、身份验证检修或
安全敏感补丁，其中预先检查表至关重要。

- 协作对齐：当您想要查看和塑造 agent 的
发生任何代码修改之前的策略。

## 如何使用/plan

您可以使用特定任务提示或不带参数调用 /plan 来启动 
 开放式发现会话。

- 反重力2.0

- 反重力CLI

### 反重力2.0

在提示输入中输入 /plan，然后输入您的目标：

```
/plan Refactor our authentication middleware to support OIDC token verification with JWKS caching
```

要在没有预定义提示的情况下启动当前工作区上下文 
 的交互式发现会话：

```
/plan
```

agent 检查您当前的活动文件和最近的对话历史记录，
 然后提出澄清问题以帮助制定计划。

### 反重力CLI

直接在交互式提示中输入 /plan：

```
/plan\n Add\n rate\n limiting\n middleware\n to\n all\n public\n API\n endpoints\n using\n Redis\n token\n buckets
```

您还可以不带参数运行 /plan：

```
/plan
```

CLI进入规划模式，执行代码库分析，并直接在终端界面中渲染
结构化规划。

## 与计划工件交互

/plan 生成的计划使用标准反重力工件格式。您可以
通过多种方式与该计划互动：

- 内嵌注释：突出显示文本或选择要添加的特定计划步骤
有针对性的反馈。

- 范围调整：要求 agent 将任务分解为更小的步骤或
在继续之前删除可选工作项目。

- 工件审核策略：配置 agent 是否暂停执行计划
使用项目的工件审查政策进行批准。

- 继续：单击继续将 agent 转换为执行模式。的
agent 逐步遵循批准的计划，通过 
 测试验证每个里程碑。

有关工件导航和评论工作流程的详细说明，请参阅
实施计划指南。

## 规划与推理模式比较

反重力根据任务范围和
复杂性提供补充命令：

命令主要焦点 最适合计划层 /plan 需求发现和可审查的计划 在修改代码之前确定复杂任务、重构和多文件更改的范围 所有计划 /grill-me 苏格拉底式设计访谈 在起草设计之前深入需求调整和边缘情况探测 所有计划 /boost 多重 agent 深度推理 硬并发错误、竞争条件、和算法优化 付费计划 /teamwork-preview 协作 agent 团队 长期活动、存储库迁移和开放式研究 付费计划

## 后续步骤

探索相关规划和自动化文档：

- 实施计划神器：学习如何审查、
评论并批准计划工件。

- 工件审核策略：配置审核行为和
自动继续设置。

- 斜线命令目录：探索完整的目录
斜线命令。

- 增强深度推理（/boost）：利用多agent推理
循环应对严峻的工程挑战。

- 团队合作 agent 团队 (/teamwork-preview)：协作运行
agent 存储库规模项目的团队。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Plan (/plan)

The /plan command facilitates structured planning, codebase exploration,
and requirements discovery before jumping directly into code execution. For
non-trivial tasks, /plan ensures complex changes are well-understood,
properly scoped, and organized before files are modified.

## Overview

Writing code before clarifying edge cases may lead to wasted effort,
unnecessary diffs, or regressions. While standard prompts immediately begin
modifying files, /plan introduces an intentional planning phase that explores
the workspace, identifies dependencies, and surfaces architectural decisions for
your review.

The /plan workflow helps bridge the gap between initial idea and code execution:

- Exploration without side effects : The agent reads files, inspects call
graphs, and analyzes dependencies without writing code prematurely.

- Interactive discovery : If requirements are underspecified, the agent asks
targeted questions to clarify trade-offs.

- Reviewable artifact : The agent drafts a structured Implementation Plan
artifact with clear task breakdowns and
verification checkpoints.

- User-guided execution : You retain full control to leave inline comments,
request adjustments, or click Proceed to begin implementation.

## How /plan works

When you invoke /plan , Antigravity runs a multi-phase planning and discovery
workflow designed to produce an actionable implementation plan:

- Analysis and discovery : The agent analyzes your prompt, inspects workspace configuration, and examines
affected files.

- Clarification and interviewing : If specifications are ambiguous or multiple technical approaches are viable,
the agent initiates a focused interview using interactive question prompts and
structured options.

- Structured plan creation : Once requirements are clear, the agent generates an Implementation Plan artifact.

- User review and approval : The agent presents the plan artifact in the review pane. You can review the
proposed steps, leave inline annotations, or adjust individual tasks.

## When to use /plan

The /plan command is especially valuable in the following development scenarios:

- Complex refactors : When restructuring modules, upgrading libraries, or
refactoring interfaces across multiple files.

- Ambiguous requirements : When starting a feature with open questions,
unknown constraints, or competing architectural approaches.

- High-risk changes : For database migrations, authentication overhauls, or
security-sensitive patches where an upfront checklist is critical.

- Collaborative alignment : When you want to review and shape the agent’s
strategy before any code modifications occur.

## How to use /plan

You can invoke /plan with a specific task prompt or without arguments to start an
open-ended discovery session.

- Antigravity 2.0

- Antigravity CLI

### Antigravity 2.0

Type /plan followed by your goal in the prompt input:

```
/plan Refactor our authentication middleware to support OIDC token verification with JWKS caching
```

To start an interactive discovery session for the current workspace context
without a predefined prompt:

```
/plan
```

The agent inspects your current active file and recent conversation history,
then asks clarifying questions to help formulate a plan.

### Antigravity CLI

Type /plan directly into the interactive prompt:

```
/plan\n Add\n rate\n limiting\n middleware\n to\n all\n public\n API\n endpoints\n using\n Redis\n token\n buckets
```

You can also run /plan without arguments:

```
/plan
```

The CLI enters planning mode, performs codebase analysis, and renders the
structured plan directly in the terminal interface.

## Interacting with plan artifacts

Plans generated by /plan use the standard Antigravity artifact format. You can
interact with the plan in several ways:

- Inline comments : Highlight text or select specific plan steps to add
targeted feedback.

- Scope adjustments : Ask the agent to break down tasks into smaller steps or
remove optional work items before proceeding.

- Artifact review policies : Configure whether the agent pauses for plan
approval using your project’s artifact review policy .

- Proceed : Click Proceed to transition the agent into execution mode. The
agent follows the approved plan step-by-step, validating each milestone with
tests.

For detailed instructions on artifact navigation and commenting workflows, refer
to the Implementation plan guide .

## Comparison of planning and reasoning modes

Antigravity provides complementary commands depending on task scope and
complexity:

Command Primary Focus Best For Plan Tier /plan Requirements discovery and reviewable plans Scoping complex tasks, refactors, and multi-file changes before modifying code All plans /grill-me Socratic design interviews Deep requirement alignment and edge-case probing before drafting designs All plans /boost Multi-agent deep reasoning Hard concurrency bugs, race conditions, and algorithmic optimizations Paid plans /teamwork-preview Collaborative agent teams Long-horizon campaigns, repository migrations, and open-ended research Paid plans

## Next steps

Explore related planning and automation documentation:

- Implementation plan artifact : Learn how to review,
comment on, and approve plan artifacts.

- Artifact review policies : Configure review behavior and
auto-proceed settings.

- Slash commands catalog : Explore the complete catalog of
slash commands.

- Boost deep reasoning ( /boost ) : Leverage multi-agent reasoning
loops for tough engineering challenges.

- Teamwork agent teams ( /teamwork-preview ) : Run collaborative
agent teams for repository-scale projects.


</details>
