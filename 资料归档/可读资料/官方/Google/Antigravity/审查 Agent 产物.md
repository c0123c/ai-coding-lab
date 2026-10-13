# 审查 Agent 产物

介绍计划和其他任务产物的审查方式，比较请求用户审查与自动继续策略，帮助决定执行前的检查点。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/artifact-review/>)）；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://antigravity.google/docs/artifact-review/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/审查 Agent 产物.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 神器评测

开始新的 agent 对话时，您可以在两种主要执行模式之间进行选择，以确定如何建议和审查更改：

- 计划模式：agent在执行任务之前进行彻底的计划。在这种模式下，agent 以任务组的形式组织工作，生成称为工件的结构化实施计划，并彻底研究代码库以获得最佳质量。

- 快速模式：agent 直接执行任务，无需专门的规划阶段。使用它可以快速完成简单、高度本地化的任务，例如变量重命名、运行特定的 Bash 命令或小型重构。

在计划模式下工作时，工件审核策略控制在对代码库进行更改之前如何与这些计划交互并批准这些计划。

## 工件审查政策

您可以在“设置”窗格的 Agent 选项卡中自定义审核工作流程。在两项政策之间进行选择：

### 请求审核（推荐）

agent 总是会停止并请求您的明确批准，然后再继续建议的更改：

- 当 agent 生成实施计划或代码差异时，它会暂停执行并通知您。

- 这使您可以彻底审查建议的更改、添加内联注释并验证工作区中的计划。

- 一旦您满意，您就可以批准该计划以让 agent 继续进行。

### 始终继续

agent 永远不会停止进行手动审核，并立即继续执行其计划：

- 当agent决定请求审查时，它会立即绕过暂停并继续实施。

- 如果您想要完全自主的工作流程并且不需要在修改代码之前手动验证计划，请使用此选项。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Artifact review

When starting a new agent conversation, you can choose between two primary execution modes that determine how changes are proposed and reviewed:

- Planning mode : The agent plans thoroughly before executing tasks. In this mode, the agent organizes its work in task groups , produces structured implementation plans called artifacts , and thoroughly researches the codebase for optimal quality.

- Fast mode : The agent executes tasks directly without a dedicated planning phase. Use this for simple, highly localized tasks that can be completed quickly, such as variable renaming, running a specific Bash command, or small refactors.

When working in Planning mode , the artifact review policy controls how you interact with and approve these plans before changes are made to your codebase.

## Artifact review policy

You can customize the review workflow in the Agent tab of the Settings pane. Choose between two policies:

### Request review (recommended)

The agent always halts and requests your explicit approval before proceeding with proposed changes:

- When the agent generates an implementation plan or code diff, it pauses execution and notifies you.

- This allows you to thoroughly review the proposed changes, add inline comments, and verify the plan in your workspace.

- Once you’re satisfied, you can approve the plan to let the agent proceed.

### Always proceed

The agent never halts for manual review and immediately proceeds with executing its plans:

- When the agent decides to request a review, it immediately bypasses the pause and continues with the implementation.

- Use this if you want a fully autonomous workflow and don’t need to manually verify plans before code is modified.


</details>
