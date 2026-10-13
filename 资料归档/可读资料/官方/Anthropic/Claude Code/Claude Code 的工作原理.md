# Claude Code 的工作原理

解释模型与工具组成的执行循环，以及读取代码、编辑、运行命令和验证的过程；介绍环境、界面、会话与权限如何影响 Agent 行为。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-07](<https://code.claude.com/docs/en/how-claude-code-works>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/how-claude-code-works.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Claude Code 的工作原理.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# Claude Code 如何运作

> 了解代理循环、内置工具以及Claude Code如何与您的项目交互。

Claude Code 是在您的终端中运行的代理助手。虽然它擅长编码，但它可以帮助您从命令行执行任何操作：编写文档、运行构建、搜索文件、研究主题等等。

本指南涵盖核心架构、内置功能和[有效工作的技巧](<#与-claude-code-有效合作>)。有关分步演练，请参阅 [常见工作流程 ](<https://code.claude.com/docs/en/common-workflows>)。有关技能、MCP 和挂钩等扩展功能，请参阅[扩展Claude Code](<https://code.claude.com/docs/en/features-overview>)。

## 代理循环

当您给Claude一个任务时，它会经历三个阶段：**收集上下文**、**采取行动**和**验证结果**。这些阶段混合在一起。 Claude 始终使用工具，无论是搜索文件以了解代码、编辑以进行更改，还是运行测试以检查其工作。



![代理循环图：您的提示导致Claude收集上下文、采取行动、验证结果并重复直到任务完成。您可以随时中断。](<https://mintcdn.com/claude-code/ikqp3_70mqIahteV/images/agentic-loop.svg?fit=max&auto=format&n=ikqp3_70mqIahteV&q=85&s=4a30fb7ce2815012a9f27c955e2c6bb0>)




![代理循环图：您的提示导致Claude收集上下文、采取行动、验证结果并重复直到任务完成。您可以随时中断。](<https://mintcdn.com/claude-code/_xqph1dUOslCOwsj/images/agentic-loop-dark.svg?fit=max&auto=format&n=_xqph1dUOslCOwsj&q=85&s=75e1d55ed76857a952f9a2dffbab02df>)


循环会适应您的要求。有关代码库的问题可能只需要收集上下文。错误修复重复地循环经历所有三个阶段。重构可能涉及广泛的验证。 Claude 根据从上一步中学到的知识来决定每一步需要什么，将数十个操作链接在一起并一路修正路线。

你也是这个循环的一部分。您可以随时中断以将 Claude 引向不同的方向，提供额外的上下文，或要求它尝试不同的方法。 Claude 自主工作，但对您的输入保持响应。

代理循环由两个组件提供动力：[模型](<#型号>)负责推理，[工具](<#工具>)负责执行。 Claude Code 是模型周围的层，它提供工具并管理模型所看到的上下文。这个周围层就是术语“代理安全带”所指的。

### 型号

Claude Code 使用 Claude 模型来理解您的代码并推理任务。 Claude 可以阅读任何语言的代码，了解组件如何连接，并找出需要更改哪些内容才能实现您的目标。对于复杂的任务，它会将工作分解为步骤，执行这些步骤，并根据所学到的知识进行调整。

[多种型号](<https://code.claude.com/docs/en/model-config>)可提供不同的权衡。 Sonnet 可以很好地处理大多数编码任务。 Opus 为复杂的架构决策提供了更强有力的推理。在会话期间切换为 `/model` 或以 `claude --model <name>` 开始。

当本指南说“Claude 选择”或“Claude 决定”时，是模型在进行推理。

### 工具

工具使Claude Code变得有活力。在没有工具的情况下，Claude只能用文字回复。借助工具，Claude 可以执行以下操作：读取代码、编辑文件、运行命令、搜索网络以及与外部服务交互。每个工具的使用都会返回反馈到循环中的信息，通知 Claude 的下一个决定。

内置工具通常分为五类，每一类代表不同类型的机构。

|类别|Claude 可以做什么|
| - | - |
|**文件操作**|读取文件、编辑代码、创建新文件、重命名和重新组织|
|**搜索**|按模式查找文件，使用正则表达式搜索内容，探索代码库|
|**执行**|运行 shell 命令、启动服务器、运行测试、使用 git|
|**网页**|搜索网络、获取文档、查找错误消息|
|**代码智能**|编辑后查看类型错误和警告、跳转到定义、查找引用（需要 [ 代码智能插件 ](<https://code.claude.com/docs/en/plugins/code-intelligence>)）|

这些是主要能力。 Claude 还具有用于生成子代理、询问问题和其他编排任务的工具。请参阅 [Claude](<https://code.claude.com/docs/en/tools-reference>) 可用的工具以获取完整列表。

Claude 根据您的提示以及一路上学到的内容选择要使用的工具。当您说“修复失败的测试”时，Claude 可能：

1. 运行测试套件以查看失败的原因
2. 读取错误输出
3. 搜索相关源文件
4. 阅读这些文件以理解代码
5. 编辑文件以解决问题
6. 再次运行测试以验证

每个工具的使用都会提供 Claude 指导下一步的新信息。这就是作用中的代理循环。

**扩展基础功能：**内置工具是基础。您可以使用 [ 技能 ](<https://code.claude.com/docs/en/skills>) 扩展 Claude 的知识，使用 [MCP](<https://code.claude.com/docs/en/mcp>) 连接到外部服务，使用 [ 挂钩 ](<https://code.claude.com/docs/en/hooks>) 自动执行工作流程，并将任务卸载到[子代理](<https://code.claude.com/docs/en/sub-agents>)。这些扩展在核心代理循环之上形成了一层。请参阅 [ 扩展 Claude Code](<https://code.claude.com/docs/en/features-overview>)，了解如何选择适合您需求的扩展。

## Claude 可以访问什么

当您在目录中运行 `claude` 时，Claude Code 可以访问：

* **您的项目。**您的目录和子目录中的文件，以及经您许可的其他位置的文件。
* **您的终端。**您可以运行的任何命令：构建工具、git、包管理器、系统实用程序、脚本。如果您可以从命令行执行此操作，Claude 也可以。
* **您的 git 状态。** 当前分支、未提交的更改和最近的提交历史记录。
* **您的[CLAUDE.md](<https://code.claude.com/docs/en/memory>).**一个markdown文件，您可以在其中存储Claude应该知道每个会话的项目特定指令、约定和上下文。如果您的存储库具有用于其他编码 agents 的 AGENTS.md，则 Claude [ 可以读取 ](<https://code.claude.com/docs/en/memory#agents-md>) 来代替 CLAUDE.md。
* **[自动记忆](<https://code.claude.com/docs/en/memory#auto-memory>)。**学习内容Claude在您工作时自动保存，就像您的偏好一样。前 200 行或 MEMORY.md 的 25KB（以先到者为准）在每个会话开始时加载。
* **您配置的扩展。** [MCP 用于外部服务的服务器 ](<https://code.claude.com/docs/en/mcp>)，用于工作流的 [ 技能 ](<https://code.claude.com/docs/en/skills>)，[ 子代理 ](<https://code.claude.com/docs/en/sub-agents>)用于委派工作，Chrome 中的 [Claude ](<https://code.claude.com/docs/en/chrome>) 用于浏览器交互。

因为 Claude 可以看到您的整个项目，所以它可以在其中工作。当您要求 Claude“修复身份验证错误”时，它会搜索相关文件，读取多个文件以了解上下文，对它们进行协调编辑，运行测试以验证修复，并根据您的要求提交更改。这与仅查看当前文件的内联代码助手不同。

## 环境和接口

[代理循环](<#代理循环>)、[工具](<#工具>)和功能在您使用的任何地方都是相同的Claude Code。改变的是代码的执行位置以及与之交互的方式。

### 执行环境

Claude Code 在三种环境中运行，每种环境对于代码的执行位置都有不同的权衡。

|环境|代码运行的地方|使用案例|
| - | - | - |
|**本地**|你的机器|默认。对您的文件、工具和环境的完全访问权限|
|**云**|您的组织运行的 Anthropic 托管虚拟机，或 [ 自托管环境 ](<https://code.claude.com/docs/en/self-hosted-environments>)|卸载任务，处理本地没有的存储库|
|**遥控器**|您的机器，由浏览器控制|执行时使用网络 UI 并且您的文件保留在本地|

### 接口

您可以通过终端访问Claude Code、[桌面应用](<https://code.claude.com/docs/en/desktop>)、[IDE扩展](<https://code.claude.com/docs/en/vs-code>)、[claude.ai/code](<https://claude.ai/code>)、[远程控制](<https://code.claude.com/docs/en/remote-control>)、 [Slack](<https://code.claude.com/docs/en/slack>)和[CI/CD管道](<https://code.claude.com/docs/en/github-actions>)。界面决定了您如何查看并与 Claude 交互，但底层的代理循环是相同的。请参阅[在任何地方使用Claude Code](<https://code.claude.com/docs/en/overview#use-claude-code-everywhere>)以获取完整列表。

## 使用会话

Claude Code 在您工作时将您的对话保存在本地。每条消息、工具使用和结果都写入明文 JSONL 文件下 `~/.claude/projects/`，这使得 [倒带](<#通过检查点撤消更改>), [恢复和分叉](<#恢复或分叉会话>) 会议。之前 Claude 进行代码更改时，它还会对受影响的文件进行快照，以便您可以在需要时恢复。有关路径、保留以及如何清除此数据的信息，请参阅 [应用程序数据在 `~/.claude`](<https://code.claude.com/docs/en/claude-directory#application-data>).

**会话是独立的。**每个新会话都以新的上下文窗口开始，没有以前会话的对话历史记录。 Claude可以使用[自动记忆](<https://code.claude.com/docs/en/memory#auto-memory>)跨会话持久学习，并且您可以在[CLAUDE.md](<https://code.claude.com/docs/en/memory>)中添加自己的持久指令。

### 跨分支机构工作

每个 Claude Code 对话都是与您当前目录相关的会话。默认情况下，`/resume` 选择器显示当前工作树中的会话，并使用键盘快捷键将列表扩展到其他工作树或项目。有关选择器快捷方式的完整列表以及名称解析的工作原理，请参阅[管理会话](<https://code.claude.com/docs/en/sessions#use-the-session-picker>)。

Claude 查看您当前分支的文件。当您切换分支时，Claude 会看到新分支的文件，但您的对话历史记录保持不变。 Claude即使切换后也记得你们讨论过的内容。

由于会话与目录相关联，因此您可以使用 [git worktrees](<https://code.claude.com/docs/en/worktrees>) 运行并行 Claude 会话，这会为各个分支创建单独的目录。

### 恢复或分叉会话

恢复与 `claude --continue` 或 `claude --resume` 的会话会在同一会话 ID 下重新打开它，并将新消息附加到现有对话。使用 `--fork-session` 或 `/branch` 进行分叉会将历史记录复制到新会话 ID 中，保持原始会话不变。



![会话连续性图：resume继续相同的会话，fork使用新的ID创建一个新分支。](<https://mintcdn.com/claude-code/ikqp3_70mqIahteV/images/session-continuity.svg?fit=max&auto=format&n=ikqp3_70mqIahteV&q=85&s=04ed0984a58e4127e05b3640265241a3>)




![会话连续性图：resume继续相同的会话，fork使用新的ID创建一个新分支。](<https://mintcdn.com/claude-code/_xqph1dUOslCOwsj/images/session-continuity-dark.svg?fit=max&auto=format&n=_xqph1dUOslCOwsj&q=85&s=886a384bce8298594e43f124617ea665>)


有关恢复标志、`/resume` 选择器、命名以及在两个终端中打开同一会话时会发生什么情况，请参阅[管理会话](<https://code.claude.com/docs/en/sessions>)。

### 上下文窗口

Claude的上下文窗口包含您的对话历史记录、文件内容、命令输出、[CLAUDE.md](<https://code.claude.com/docs/en/memory>)、[自动记忆](<https://code.claude.com/docs/en/memory#auto-memory>)、加载的技能和系统指令。当你工作时，上下文就会被填满。 Claude 自动压缩，但对话早期的指令可能会丢失。将持久性规则放入 CLAUDE.md 中，然后运行 ​​`/context` 以查看哪些内容正在使用空间。

有关加载内容和加载时间的交互式演练，请参阅 [探索上下文窗口 ](<https://code.claude.com/docs/en/context-window>)。

#### 上下文 Claude Code 自行添加

如果 Claude 遵循您未编写的规则，例如向提交添加 `Co-Authored-By` 预告片，则该规则可能来自 [ 系统提醒 ](<https://code.claude.com/docs/en/glossary#system-reminder>)。当您工作时，Claude Code 将其自己的上下文与您的消息一起添加到对话中：

* 您的 CLAUDE.md 文件
* 您的[输出风格](<https://code.claude.com/docs/en/output-styles>)的说明
* 文件 Claude 读取磁盘上早期更改时的注释
* 提交和拉取请求属性行

要更改或删除归属线，请设置 [`attribution`](<https://code.claude.com/docs/en/settings-reference#attribution>)。要删除Claude Code的内置提交和拉取请求指令，请将[`includeGitInstructions`](<https://code.claude.com/docs/en/settings-reference#includegitinstructions>)设置为`false`。对于其他开关，请参阅[关闭agent替换](<https://code.claude.com/docs/en/agent-sdk/modifying-system-prompts#turn-off-the-context-your-agent-replaces>)的上下文。

#### 当上下文填满时

当您接近限制时，Claude Code 会自动管理上下文。它首先清除旧工具的输出，然后在需要时总结对话。您的请求和关键代码片段将被保留；对话初期的详细说明可能会丢失。将持久规则放在 CLAUDE.md 中，而不是依赖于对话历史记录。

要控制压缩过程中保留的内容，请将“紧凑指令”部分添加到 CLAUDE.md 或运行 `/compact`（如 `/compact focus on the API changes`）。

如果单个文件或工具输出太大，以至于上下文在每次摘要后立即重新填充，Claude Code 会在几次尝试后停止自动压缩，并显示错误而不是循环。有关恢复步骤，请参阅 [自动压缩因抖动错误而停止 ](<https://code.claude.com/docs/en/troubleshooting#auto-compaction-stops-with-a-thrashing-error>)。

运行 `/context` 查看正在使用空间的内容。默认情况下，MCP 工具定义被推迟，并通过 [ 工具搜索 ](<https://code.claude.com/docs/en/mcp#scale-with-mcp-tool-search>) 按需加载，因此只有工具名称和服务器指令会消耗上下文，直到 Claude 使用特定工具。

#### 使用技能和子代理管理上下文

除了压缩之外，您还可以使用其他功能来控制加载到上下文中的内容。

[技能](<https://code.claude.com/docs/en/skills>)按需加载。 Claude 在会话开始时看到技能描述，但完整内容仅在使用技能时加载。对于手动调用的技能，请设置 `disable-model-invocation: true` 以保持描述脱离上下文，直到您需要它们为止。对于您未编写的技能，请使用 [`skillOverrides`](<https://code.claude.com/docs/en/skills#override-skill-visibility-from-settings>) 从设置中执行相同操作。

[子代理](<https://code.claude.com/docs/en/sub-agents>)在自己的上下文窗口中工作。子代理会重新开始，除非它是一个 [ 分支 ](<https://code.claude.com/docs/en/sub-agents#fork-the-current-conversation>)，它以迄今为止的对话副本开始。无论哪种方式，子代理的工具调用都会脱离您的上下文，并且 Claude 在子代理完成时会返回摘要。

请参阅 [ 上下文成本 ](<https://code.claude.com/docs/en/features-overview#understand-context-costs>) 了解每个功能的成本，并 [ 减少令牌使用 ](<https://code.claude.com/docs/en/costs#reduce-token-usage>) 了解管理上下文的提示。

## 通过检查点和权限确保安全

Claude 有两种安全机制：检查点让您撤消文件更改，权限控制 Claude 无需询问即可执行的操作。

### 通过检查点撤消更改

**文件编辑是可逆的。**在Claude编辑文件之前，它会快照当前内容。如果出现问题，请按 `Esc` 两次返回到之前的状态，或要求 Claude 撤消。

检查点与 git 是分开的，并且在您恢复对话时仍然可用。它们仅涵盖文件更改，并且恢复 [ 会跳过符号链接和硬链接文件 ](<https://code.claude.com/docs/en/checkpointing#symlinked-and-hard-linked-paths-not-restored>)。影响远程系统（数据库、API、部署）的操作无法设置检查点。您可以通过权限模式和权限规则来控制它们。

### 控制 Claude 可以做什么

选择一种权限模式来设置Claude可以执行的操作，而无需询问您。按`Shift+Tab`循环切换权限模式：

* **自动**：分类器会在后台审查大多数操作并阻止有风险的操作，而不是询问您。对于 Claude Code v2.1.283 或更高版本，它是用于交互式终端和 VS Code 会话的 [ 内置启动权限模式 ](<https://code.claude.com/docs/en/permission-modes#which-mode-a-session-starts-in>)，并且在早期版本中仅适用于 Pro、Max 和 Team 计划
* **手册**：Claude在文件编辑和shell命令之前询问
* **接受编辑**：Claude编辑文件并运行常见的文件系统命令，如`mkdir`和`mv`而不询问，仍然要求其他命令
* **计划**：Claude探索并提出计划，而无需编辑源文件

您还可以允许 `.claude/settings.json` 中的特定命令，这样 Claude 就不会每次都询问。这对于 `npm test` 或 `git status` 等可信命令很有用。设置的范围可以从组织范围的策略到个人偏好。详细信息请参见[权限](<https://code.claude.com/docs/en/permissions>)。

***

## 与 Claude Code 有效合作

这些提示可帮助您从 Claude Code 获得更好的结果。有关具体提示、验证和规划的更多信息，请参阅[最佳实践](<https://code.claude.com/docs/en/best-practices>)。

### 向Claude Code寻求帮助

Claude Code可以教你如何使用。提出诸如“如何设置挂钩？”之类的问题。或“构建我的 CLAUDE.md 的最佳方式是什么？”和Claude将解释。

内置命令还指导您完成设置：

* `/init` 为您的项目生成启动器 CLAUDE.md
* `/doctor` 运行安装检查来诊断安装和配置问题并修复它们

### 这是一场对话

Claude Code 是会话式的。您不需要完美的提示。从你想要的开始，然后完善：

```text theme={null}
Fix the login bug
```

\[Claude 调查，尝试一些东西]

```text theme={null}
That's not quite right. The issue is in the session handling.
```

\[Claude调整方法]

当第一次尝试不正确时，你就不会重新开始。你迭代。

#### 中断和引导

您可以随时重定向 Claude，而无需重新开始。执行以下任一操作：

* **按`Esc`**立即停止Claude。正在运行的工具调用被取消，Claude等待您的下一条指令。如果您有消息排队，Claude Code [ 接下来发送它们](<https://code.claude.com/docs/en/interactive-mode#queue-messages-while-claude-works>)。
* **键入更正并按`Enter`**，不要停止Claude。该消息显示为在对话中排队。如果 Claude 正在运行工具调用，则在这些调用完成后，它会在同一回合内立即读取消息，并在下一步之前进行调整。 [Claude 工作时对消息进行排队](<https://code.claude.com/docs/en/interactive-mode#queue-messages-while-claude-works>) 涵盖发送其他排队条目的时间。

### 代表，而不是发号施令

考虑委托给一位有能力的同事。给出上下文和方向，然后相信 Claude 来弄清楚细节：

```text theme={null}
The checkout flow is broken for users with expired cards.
The relevant code is in src/payments/. Can you investigate and fix it?
```

您不需要指定要读取哪些文件或要运行哪些命令。 Claude 弄清楚了。

## 接下来是什么



  

#### [扩展功能](<https://code.claude.com/docs/en/features-overview>)

添加技能和 MCP 联系
  


  

#### [常用工作流程](<https://code.claude.com/docs/en/common-workflows>)

典型任务的分步指南
  


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# How Claude Code works

> Understand the agentic loop, built-in tools, and how Claude Code interacts with your project.

Claude Code is an agentic assistant that runs in your terminal. While it excels at coding, it can help with anything you can do from the command line: writing docs, running builds, searching files, researching topics, and more.

This guide covers the core architecture, built-in capabilities, and [tips for working effectively](<#work-effectively-with-claude-code>). For step-by-step walkthroughs, see [Common workflows](<https://code.claude.com/docs/en/common-workflows>). For extensibility features like skills, MCP, and hooks, see [Extend Claude Code](<https://code.claude.com/docs/en/features-overview>).

## The agentic loop

When you give Claude a task, it works through three phases: **gather context**, **take action**, and **verify results**. These phases blend together. Claude uses tools throughout, whether searching files to understand your code, editing to make changes, or running tests to check its work.



![Diagram of the agentic loop: Your prompt leads to Claude gathering context, taking action, verifying results, and repeating until task complete. You can interrupt at any point.](<https://mintcdn.com/claude-code/ikqp3_70mqIahteV/images/agentic-loop.svg?fit=max&auto=format&n=ikqp3_70mqIahteV&q=85&s=4a30fb7ce2815012a9f27c955e2c6bb0>)




![Diagram of the agentic loop: Your prompt leads to Claude gathering context, taking action, verifying results, and repeating until task complete. You can interrupt at any point.](<https://mintcdn.com/claude-code/_xqph1dUOslCOwsj/images/agentic-loop-dark.svg?fit=max&auto=format&n=_xqph1dUOslCOwsj&q=85&s=75e1d55ed76857a952f9a2dffbab02df>)


The loop adapts to what you ask. A question about your codebase might only need context gathering. A bug fix cycles through all three phases repeatedly. A refactor might involve extensive verification. Claude decides what each step requires based on what it learned from the previous step, chaining dozens of actions together and course-correcting along the way.

You're part of this loop too. You can interrupt at any point to steer Claude in a different direction, provide additional context, or ask it to try a different approach. Claude works autonomously but stays responsive to your input.

The agentic loop is powered by two components: [models](<#models>) that reason and [tools](<#tools>) that act. Claude Code is the layer around the model that provides the tools and manages the context the model sees. This surrounding layer is what the term agentic harness refers to.

### Models

Claude Code uses Claude models to understand your code and reason about tasks. Claude can read code in any language, understand how components connect, and figure out what needs to change to accomplish your goal. For complex tasks, it breaks work into steps, executes them, and adjusts based on what it learns.

[Multiple models](<https://code.claude.com/docs/en/model-config>) are available with different tradeoffs. Sonnet handles most coding tasks well. Opus provides stronger reasoning for complex architectural decisions. Switch with `/model` during a session or start with `claude --model <name>`.

When this guide says "Claude chooses" or "Claude decides," it's the model doing the reasoning.

### Tools

Tools are what make Claude Code agentic. Without tools, Claude can only respond with text. With tools, Claude can act: read your code, edit files, run commands, search the web, and interact with external services. Each tool use returns information that feeds back into the loop, informing Claude's next decision.

The built-in tools generally fall into five categories, each representing a different kind of agency.

| Category | What Claude can do |
| - | - |
| **File operations** | Read files, edit code, create new files, rename and reorganize |
| **Search** | Find files by pattern, search content with regex, explore codebases |
| **Execution** | Run shell commands, start servers, run tests, use git |
| **Web** | Search the web, fetch documentation, look up error messages |
| **Code intelligence** | See type errors and warnings after edits, jump to definitions, find references (requires [code intelligence plugins](<https://code.claude.com/docs/en/plugins/code-intelligence>)) |

These are the primary capabilities. Claude also has tools for spawning subagents, asking you questions, and other orchestration tasks. See [Tools available to Claude](<https://code.claude.com/docs/en/tools-reference>) for the complete list.

Claude chooses which tools to use based on your prompt and what it learns along the way. When you say "fix the failing tests," Claude might:

1. Run the test suite to see what's failing
2. Read the error output
3. Search for the relevant source files
4. Read those files to understand the code
5. Edit the files to fix the issue
6. Run the tests again to verify

Each tool use gives Claude new information that informs the next step. This is the agentic loop in action.

**Extending the base capabilities:** The built-in tools are the foundation. You can extend what Claude knows with [skills](<https://code.claude.com/docs/en/skills>), connect to external services with [MCP](<https://code.claude.com/docs/en/mcp>), automate workflows with [hooks](<https://code.claude.com/docs/en/hooks>), and offload tasks to [subagents](<https://code.claude.com/docs/en/sub-agents>). These extensions form a layer on top of the core agentic loop. See [Extend Claude Code](<https://code.claude.com/docs/en/features-overview>) for guidance on choosing the right extension for your needs.

## What Claude can access

When you run `claude` in a directory, Claude Code gains access to:

* **Your project.** Files in your directory and subdirectories, plus files elsewhere with your permission.
* **Your terminal.** Any command you could run: build tools, git, package managers, system utilities, scripts. If you can do it from the command line, Claude can too.
* **Your git state.** Current branch, uncommitted changes, and recent commit history.
* **Your [CLAUDE.md](<https://code.claude.com/docs/en/memory>).** A markdown file where you store project-specific instructions, conventions, and context that Claude should know every session. If your repository has an AGENTS.md for other coding agents, Claude [can read that](<https://code.claude.com/docs/en/memory#agents-md>) in place of a CLAUDE.md.
* **[Auto memory](<https://code.claude.com/docs/en/memory#auto-memory>).** Learnings Claude saves automatically as you work, like your preferences. The first 200 lines or 25KB of MEMORY.md, whichever comes first, load at the start of each session.
* **Extensions you configure.** [MCP servers](<https://code.claude.com/docs/en/mcp>) for external services, [skills](<https://code.claude.com/docs/en/skills>) for workflows, [subagents](<https://code.claude.com/docs/en/sub-agents>) for delegated work, and [Claude in Chrome](<https://code.claude.com/docs/en/chrome>) for browser interaction.

Because Claude sees your whole project, it can work across it. When you ask Claude to "fix the authentication bug," it searches for relevant files, reads multiple files to understand context, makes coordinated edits across them, runs tests to verify the fix, and commits the changes if you ask. This is different from inline code assistants that only see the current file.

## Environments and interfaces

The [agentic loop](<#the-agentic-loop>), [tools](<#tools>), and capabilities are the same everywhere you use Claude Code. What changes is where the code executes and how you interact with it.

### Execution environments

Claude Code runs in three environments, each with different tradeoffs for where your code executes.

| Environment | Where code runs | Use case |
| - | - | - |
| **Local** | Your machine | Default. Full access to your files, tools, and environment |
| **Cloud** | Anthropic-managed VMs, or [self-hosted environments](<https://code.claude.com/docs/en/self-hosted-environments>) your organization operates | Offload tasks, work on repos you don't have locally |
| **Remote Control** | Your machine, controlled from a browser | Use the web UI while execution and your files stay local |

### Interfaces

You can access Claude Code through the terminal, the [desktop app](<https://code.claude.com/docs/en/desktop>), [IDE extensions](<https://code.claude.com/docs/en/vs-code>), [claude.ai/code](<https://claude.ai/code>), [Remote Control](<https://code.claude.com/docs/en/remote-control>), [Slack](<https://code.claude.com/docs/en/slack>), and [CI/CD pipelines](<https://code.claude.com/docs/en/github-actions>). The interface determines how you see and interact with Claude, but the underlying agentic loop is identical. See [Use Claude Code everywhere](<https://code.claude.com/docs/en/overview#use-claude-code-everywhere>) for the full list.

## Work with sessions

Claude Code saves your conversation locally as you work. Each message, tool use, and result is written to a plaintext JSONL file under `~/.claude/projects/`, which enables [rewinding](<#undo-changes-with-checkpoints>), [resuming, and forking](<#resume-or-fork-sessions>) sessions. Before Claude makes code changes, it also snapshots the affected files so you can revert if needed. For paths, retention, and how to clear this data, see [application data in `~/.claude`](<https://code.claude.com/docs/en/claude-directory#application-data>).

**Sessions are independent.** Each new session starts with a fresh context window, without the conversation history from previous sessions. Claude can persist learnings across sessions using [auto memory](<https://code.claude.com/docs/en/memory#auto-memory>), and you can add your own persistent instructions in [CLAUDE.md](<https://code.claude.com/docs/en/memory>).

### Work across branches

Each Claude Code conversation is a session tied to your current directory. The `/resume` picker shows sessions from the current worktree by default, with keyboard shortcuts to widen the list to other worktrees or projects. See [Manage sessions](<https://code.claude.com/docs/en/sessions#use-the-session-picker>) for the full list of picker shortcuts and how name resolution works.

Claude sees your current branch's files. When you switch branches, Claude sees the new branch's files, but your conversation history stays the same. Claude remembers what you discussed even after switching.

Since sessions are tied to directories, you can run parallel Claude sessions by using [git worktrees](<https://code.claude.com/docs/en/worktrees>), which create separate directories for individual branches.

### Resume or fork sessions

Resuming a session with `claude --continue` or `claude --resume` reopens it under the same session ID and appends new messages to the existing conversation. Forking with `--fork-session` or `/branch` copies the history into a new session ID, leaving the original unchanged.



![Diagram of session continuity: resume continues the same session, fork creates a new branch with a new ID.](<https://mintcdn.com/claude-code/ikqp3_70mqIahteV/images/session-continuity.svg?fit=max&auto=format&n=ikqp3_70mqIahteV&q=85&s=04ed0984a58e4127e05b3640265241a3>)




![Diagram of session continuity: resume continues the same session, fork creates a new branch with a new ID.](<https://mintcdn.com/claude-code/_xqph1dUOslCOwsj/images/session-continuity-dark.svg?fit=max&auto=format&n=_xqph1dUOslCOwsj&q=85&s=886a384bce8298594e43f124617ea665>)


For the resume flags, the `/resume` picker, naming, and what happens when the same session is open in two terminals, see [Manage sessions](<https://code.claude.com/docs/en/sessions>).

### The context window

Claude's context window holds your conversation history, file contents, command outputs, [CLAUDE.md](<https://code.claude.com/docs/en/memory>), [auto memory](<https://code.claude.com/docs/en/memory#auto-memory>), loaded skills, and system instructions. As you work, context fills up. Claude compacts automatically, but instructions from early in the conversation can get lost. Put persistent rules in CLAUDE.md, and run `/context` to see what's using space.

For an interactive walkthrough of what loads and when, see [Explore the context window](<https://code.claude.com/docs/en/context-window>).

#### Context Claude Code adds on its own

If Claude follows a rule you didn't write, such as adding a `Co-Authored-By` trailer to a commit, the rule may have come from a [system reminder](<https://code.claude.com/docs/en/glossary#system-reminder>). As you work, Claude Code adds its own context to the conversation alongside your messages:

* Your CLAUDE.md files
* The instructions of your [output style](<https://code.claude.com/docs/en/output-styles>)
* A note when a file Claude read earlier changes on disk
* The commit and pull request attribution lines

To change or remove the attribution lines, set [`attribution`](<https://code.claude.com/docs/en/settings-reference#attribution>). To remove Claude Code's built-in commit and pull request instructions, set [`includeGitInstructions`](<https://code.claude.com/docs/en/settings-reference#includegitinstructions>) to `false`. For the other switches, see [Turn off the context your agent replaces](<https://code.claude.com/docs/en/agent-sdk/modifying-system-prompts#turn-off-the-context-your-agent-replaces>).

#### When context fills up

Claude Code manages context automatically as you approach the limit. It clears older tool outputs first, then summarizes the conversation if needed. Your requests and key code snippets are preserved; detailed instructions from early in the conversation may be lost. Put persistent rules in CLAUDE.md rather than relying on conversation history.

To control what's preserved during compaction, add a "Compact Instructions" section to CLAUDE.md or run `/compact` with a focus (like `/compact focus on the API changes`).

If a single file or tool output is so large that context refills immediately after each summary, Claude Code stops auto-compacting after a few attempts and shows an error instead of looping. See [Auto-compaction stops with a thrashing error](<https://code.claude.com/docs/en/troubleshooting#auto-compaction-stops-with-a-thrashing-error>) for recovery steps.

Run `/context` to see what's using space. MCP tool definitions are deferred by default and loaded on demand via [tool search](<https://code.claude.com/docs/en/mcp#scale-with-mcp-tool-search>), so only tool names and server instructions consume context until Claude uses a specific tool.

#### Manage context with skills and subagents

Beyond compaction, you can use other features to control what loads into context.

[Skills](<https://code.claude.com/docs/en/skills>) load on demand. Claude sees skill descriptions at session start, but the full content only loads when a skill is used. For skills you invoke manually, set `disable-model-invocation: true` to keep descriptions out of context until you need them. For skills you didn't write, use [`skillOverrides`](<https://code.claude.com/docs/en/skills#override-skill-visibility-from-settings>) to do the same from settings.

[Subagents](<https://code.claude.com/docs/en/sub-agents>) work in their own context window. A subagent starts fresh unless it's a [fork](<https://code.claude.com/docs/en/sub-agents#fork-the-current-conversation>), which starts with a copy of your conversation so far. Either way, the subagent's tool calls stay out of your context, and Claude gets back a summary when the subagent finishes.

See [context costs](<https://code.claude.com/docs/en/features-overview#understand-context-costs>) for what each feature costs, and [reduce token usage](<https://code.claude.com/docs/en/costs#reduce-token-usage>) for tips on managing context.

## Stay safe with checkpoints and permissions

Claude has two safety mechanisms: checkpoints let you undo file changes, and permissions control what Claude can do without asking.

### Undo changes with checkpoints

**File edits are reversible.** Before Claude edits a file, it snapshots the current contents. If something goes wrong, press `Esc` twice to rewind to a previous state, or ask Claude to undo.

Checkpoints are separate from git and remain available when you resume a conversation. They only cover file changes, and a restore [skips symlinked and hard-linked files](<https://code.claude.com/docs/en/checkpointing#symlinked-and-hard-linked-paths-not-restored>). Actions that affect remote systems (databases, APIs, deployments) can't be checkpointed. You control those with your permission mode and permission rules.

### Control what Claude can do

Choose a permission mode to set what Claude can do without asking you. Press `Shift+Tab` to cycle through the permission modes:

* **Auto**: a classifier reviews most actions in the background and blocks the risky ones instead of asking you. With Claude Code v2.1.283 or later, it's the [built-in starting permission mode](<https://code.claude.com/docs/en/permission-modes#which-mode-a-session-starts-in>) for interactive terminal and VS Code sessions, and on earlier versions only on Pro, Max, and Team plans
* **Manual**: Claude asks before file edits and shell commands
* **Accept edits**: Claude edits files and runs common filesystem commands like `mkdir` and `mv` without asking, still asks for other commands
* **Plan**: Claude explores and proposes a plan without editing your source files

You can also allow specific commands in `.claude/settings.json` so Claude doesn't ask each time. This is useful for trusted commands like `npm test` or `git status`. Settings can be scoped from organization-wide policies down to personal preferences. See [Permissions](<https://code.claude.com/docs/en/permissions>) for details.

***

## Work effectively with Claude Code

These tips help you get better results from Claude Code. For more on specific prompts, verification, and planning, see [Best practices](<https://code.claude.com/docs/en/best-practices>).

### Ask Claude Code for help

Claude Code can teach you how to use it. Ask questions like "how do I set up hooks?" or "what's the best way to structure my CLAUDE.md?" and Claude will explain.

Built-in commands also guide you through setup:

* `/init` generates a starter CLAUDE.md for your project
* `/doctor` runs a setup checkup that diagnoses installation and configuration issues and can fix them

### It's a conversation

Claude Code is conversational. You don't need perfect prompts. Start with what you want, then refine:

```text theme={null}
Fix the login bug
```

\[Claude investigates, tries something]

```text theme={null}
That's not quite right. The issue is in the session handling.
```

\[Claude adjusts approach]

When the first attempt isn't right, you don't start over. You iterate.

#### Interrupt and steer

You can redirect Claude at any point without starting over. Do either of these:

* **Press `Esc`** to stop Claude immediately. The running tool call is canceled and Claude waits for your next instruction. If you have messages queued, Claude Code [sends them next](<https://code.claude.com/docs/en/interactive-mode#queue-messages-while-claude-works>).
* **Type a correction and press `Enter`** without stopping Claude. The message shows as queued in the conversation. If Claude is running tool calls, it reads the message as soon as those calls finish, within the same turn, and adjusts before its next step. [Queue messages while Claude works](<https://code.claude.com/docs/en/interactive-mode#queue-messages-while-claude-works>) covers when other queued entries are sent.

### Delegate, don't dictate

Think of delegating to a capable colleague. Give context and direction, then trust Claude to figure out the details:

```text theme={null}
The checkout flow is broken for users with expired cards.
The relevant code is in src/payments/. Can you investigate and fix it?
```

You don't need to specify which files to read or what commands to run. Claude figures that out.

## What's next



  

#### [Extend with features](<https://code.claude.com/docs/en/features-overview>)

    Add skills and MCP connections
  


  

#### [Common workflows](<https://code.claude.com/docs/en/common-workflows>)

    Step-by-step guides for typical tasks
  


</details>
