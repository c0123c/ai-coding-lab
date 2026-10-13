# Codex 最佳实践

介绍清晰任务与上下文、困难工作先规划、AGENTS.md、配置、测试和审查、MCP 及可重复操作，帮助建立稳定开发流程。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/guides/best-practices>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/guides/best-practices>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex 最佳实践.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 最佳实践 | ChatGPT 学习

# 最佳实践

开始使用 Codex 和经过验证的实践以获得更好的结果

如果您是 Codex 或编码 agents 的新手，本指南将帮助您更快地获得更好的结果。它涵盖了使 Codex 在 CLI、IDE 扩展程序和 ChatGPT 桌面应用程序中更有效的核心习惯，从提示和计划到验证、MCP、技能和计划任务。

当您不再把它当作一次性助手，而更像是您随着时间的推移进行配置和改进的队友时，Codex 效果最佳。

思考这个问题的一个有用方法是：从任务的正确上下文开始，使用 AGENTS.md 提供持久指导，配置 Codex 以匹配您的工作流程，使用 MCP 连接外部系统，将重复的工作转化为技能，并自动化稳定的工作流程。

## 强烈的首次使用：上下文和提示

Codex 已经足够强大，即使您的提示不完美也很有用。您通常可以用最少的设置来解决一个难题，但仍然可以获得很好的结果。获得价值并不需要明确的提示，但它确实使结果更加可靠，尤其是在更大的代码库或更高风险的任务中。

如果您在大型或复杂的存储库中工作，最大的解锁就是为 Codex 提供正确的任务上下文以及您想要完成的工作的清晰结构。

一个好的默认设置是在提示中包含四件事：

- 目标：你想改变或建立什么？

- 上下文：哪些文件、文件夹、文档、示例或错误对于此任务很重要？您可以@提及某些文件作为上下文。

- 约束：Codex 应遵循哪些标准、架构、安全要求或约定？

- 完成时间：任务完成之前应该发生什么，例如测试通过、行为改变或不再重现错误？

这有助于 Codex 保持范围，减少假设，并产生更容易审查的工作。

当您的账户和客户可用时，请使用 GPT-6.1 Sol，从您的客户默认可用的推理工作开始。从 GPT-6 Luna 的 High 或 GPT-6 Astra 的 Light 开始（低配置）。根据任务和结果进行调整。请参阅模型了解可用的推理级别。

- 低，适合更快、范围更广的任务

- 中或高用于更复杂的更改或调试

- 特高，适合长期、代理、推理繁重的任务

为了更快地提供上下文，请尝试在 ChatGPT
 桌面应用程序中使用语音听写来指示您想要 Codex 执行的操作，而不是打字。

## 首先计划困难的任务

如果任务复杂、不明确或难以描述，请要求 Codex 在开始编码之前进行计划。

有几种方法效果很好：

使用计划模式：对于大多数用户来说，这是最简单且最有效的选项。计划模式让 Codex 收集背景信息，提出澄清问题，并在实施之前制定更强有力的计划。使用 /plan 或 Shift + Tab 进行切换。

请Codex采访您：如果您大致了解自己想要什么，但不确定如何描述它，请先请Codex向您提问。在编写代码之前，告诉它挑战您的假设并将模糊的想法变成具体的东西。

使用 PLANS.md 模板：对于更高级的工作流程，您可以配置 Codex 以遵循 PLANS.md 或执行计划模板，以进行长时间运行或多步骤的工作。有关更多详细信息，请参阅执行计划指南。

## 使用 AGENTS.md 使指南可重复使用

一旦提示模式起作用，下一步就是停止手动重复它。这就是 AGENTS.md 的用武之地。

将 AGENTS.md 视为 agents 的开放格式 README。它会自动加载到上下文中，并且是对您和您的团队希望 Codex 在存储库中工作的方式进行编码的最佳位置。

一个好的 AGENTS.md 涵盖：

- 仓库布局和重要目录

- 如何运行项目

- 构建、测试和 lint 命令

- 工程惯例和 PR 期望

- 限制和禁止规则

- 完成意味着什么以及如何验证工作

CLI 中的 /init 斜杠命令是快速启动命令，用于在当前目录中构建启动器 AGENTS.md。这是一个很好的起点，但您应该编辑结果以匹配您的团队实际构建、测试、审查和发布代码的方式。

您可以在不同级别创建 AGENTS.md 文件：位于 ~/.codex 中的用于个人默认设置的全局 AGENTS.md、用于共享标准的存储库级别文件以及用于本地规则的子目录中的更具体文件。如果有更具体的文件更接近您当前的目录，则该指导有效。

保持实用。简短而准确的 AGENTS.md 比充满模糊规则的长文件更有用。从基础开始，只有在发现重复的错误后才添加新规则。

如果 AGENTS.md 开始变得太大，请保持主文件简洁，并参考特定于任务的 Markdown 文件以进行规划、代码审查或架构等。

当 Codex 犯同样的错误两次时，要求其回顾并更新 AGENTS.md 。指导保持实用并基于真实的摩擦。

## 配置 Codex 以保持一致性

配置是使 Codex 在会话和表面上表现更加一致的主要方法之一。例如，您可以设置模型选择、推理工作、沙盒模式、审批策略、配置文件和 MCP 设置的默认值。

一个好的起始模式是：

- 保留 ~/.codex/config.toml 中的个人默认值（设置 > 配置 > 在 ChatGPT 桌面应用程序中打开 config.toml）

- 将特定于存储库的行为保留在 .codex/config.toml 中

- 仅在一次性情况下使用命令行覆盖（如果您使用 CLI）

config.toml 是您定义持久首选项的位置，例如 MCP 服务器、多 agent 设置和功能标志。配置文件特定的覆盖位于单独的 $CODEX_HOME/profile-name.config.toml 文件中。

Codex 附带操作级别沙箱，并有两个可以控制的按键旋钮。批准模式确定 Codex 何时请求您运行命令的权限，沙盒模式确定 Codex 是否可以在目录中读取或写入以及 agent 可以访问哪些文件。

如果您不熟悉编码 agents，请从默认权限开始。默认情况下保持严格的审批和沙箱，然后在需求明确后仅放宽受信任的存储库或特定工作流程的权限。

请注意，CLI、IDE 扩展和 ChatGPT 桌面应用程序都共享相同的配置层。在示例配置页面上了解更多信息。

尽早为您的真实环境配置Codex。许多质量问题实际上是
设置问题，例如错误的工作目录、缺少写入访问权限、
错误的模型默认值或缺少工具和连接器。

## 通过测试和审查提高可靠性

不要停止要求 Codex 做出改变。要求它在需要时创建测试，运行相关检查，确认结果，并在接受之前审查工作。

Codex 可以为您执行此循环，但前提是它知道“好”是什么样子。该指导可以来自提示或 AGENTS.md 。

这可以包括：

- 为变更编写或更新测试

- 运行正确的测试套件

- 检查 lint、格式或类型检查

- 确认最终行为符合请求

- 检查差异是否存在错误、回归或风险模式

切换 ChatGPT 桌面应用程序中的差异面板，以直接在本地查看 
 更改。单击特定行可
提供反馈，该反馈将作为上下文提供给下一个Codex回合。

这里一个有用的选项是斜杠命令 /review ，它为您提供了几种检查代码的方法：

- 针对 PR- 风格审核的基础分支进行审核

- 审查未提交的更改

- 审查提交

- 使用自定义审核说明

如果您和您的团队有 code_review.md 文件并从 AGENTS.md 引用它，Codex 也可以在审核期间遵循该指南。对于希望审查行为在存储库和贡献者之间保持一致的团队来说，这是一个强大的模式。

Codex 不应该只是生成代码。通过正确的说明，它还可以帮助测试、检查和审查。

如果您使用 GitHub 云，您可以设置 Codex 为您的 PR 运行代码审查。在 OpenAI，Codex 审核了 PR 的 100%。您可以启用自动审阅，或在 @Codex 时让 Codex 进行被动审阅。

## 使用 MCP 作为外部上下文

当上下文 Codex 需要位于存储库之外时，请使用 MCP。它允许 Codex 连接到您已经使用的工具和系统，因此您不必不断将实时信息复制并粘贴到提示中。

Model Context Protocol 或 MCP 是用于将 Codex 连接到外部工具和系统的开放标准。

在以下情况下使用 MCP：

- 所需的上下文位于存储库之外

- 数据经常变化

- 您希望 Codex 使用工具而不是依赖粘贴的说明

- 您需要跨用户或项目的可重复集成

Codex 支持具有 OAuth 的 STDIO 和可流式 HTTP 服务器。

在 ChatGPT 桌面应用程序中，转到设置 > MCP 服务器以查看自定义和推荐的服务器。通常，Codex可以帮助您安装所需的服务器。您所需要做的就是询问。您还可以使用 CLI 中的 codex mcp add 命令来添加名称、URL 和其他详细信息的自定义服务器。

仅当工具解锁真正的工作流程时才添加工具。不要从
您使用的每个工具中接线开始。从一两个工具开始，这些工具可以清楚地删除您经常使用的手动
循环，然后从那里进行扩展。

## 将可重复的工作转化为技能

一旦工作流程变得可重复，就停止依赖长提示或来回重复。使用技能将指令打包在 SKILL.md 文件中，上下文和支持逻辑 Codex 应一致应用。技能适用于 CLI、IDE 扩展程序和 ChatGPT 桌面应用程序。

让每项技能只适用于一项工作。从 2 到 3 具体用例开始，定义清晰的输入和输出，并编写描述，以便说明该技能的作用以及何时使用它。包括用户实际会说的触发短语类型。

不要试图预先涵盖所有边缘情况。从一项代表性任务开始，让它顺利运行，然后将该工作流程转化为一项技能，并从那里开始改进。仅当脚本或额外资产提高可靠性时才包含它们。

一个好的经验法则：如果您不断重复使用相同的提示或纠正相同的工作流程，它可能应该成为一项技能。

技能对于重复性工作特别有用，例如：

- 日志分类

- 起草发行说明

- PR 根据清单进行审查

- 迁移规划

- 遥测或事件摘要

- 标准调试流程

$skill-creator 技能是构建技能第一个版本的最佳起点。迭代时将第一个版本保留在本地。当准备好广泛共享时，将其打包为插件。技能最重要的部分之一是描述。它应该说明该技能的作用以及何时使用它。

个人技能存储在 $HOME/.agents/skills 中，共享团队技能 
 可以签入存储库内的 .agents/skills 中。这对于新队友的入职特别
很有帮助。

## 使用计划任务进行重复工作

一旦工作流程稳定，您可以安排 Codex 在后台为您运行它。在 ChatGPT 桌面应用程序中，计划任务允许您选择重复工作的项目、提示、节奏和执行环境。

从“计划”页面创建计划任务。选择项目、提示、
节奏，以及任务是在专用 Git 工作树中运行还是在本地 
 环境中运行。提示可以调用技能。了解有关 Git 工作树的更多信息。

好的候选人包括：

- 总结最近的提交

- 扫描可能的错误

- 起草发行说明

- 检查 CI 故障

- 制作站立摘要

- 按计划运行可重复的分析工作流程

一个有用的规则是，技能定义方法，计划任务定义时间表。如果工作流程仍然需要大量指导，请首先将其转变为技能。一旦可以预测，安排它可以节省时间。

使用计划任务进行反思和维护，而不仅仅是执行。查看
最近的聊天，总结重复的摩擦，并随着时间的推移改进提示、说明、
或工作流程设置。

## 组织长时间的聊天

随着时间的推移，聊天会积累上下文、决策和操作，因此良好的管理它们对质量有很大影响。

ChatGPT 桌面应用程序可让您固定聊天并创建工作树。如果您使用
CLI，这些斜杠命令特别有用：

- /experimental 切换实验性功能并添加到您的 config.toml

- /resume 恢复已保存的聊天

- /fork 创建新聊天，同时保留原始记录

- /compact 当聊天时间变长并且您想要早期上下文的摘要版本时。 Codex 还会自动压缩聊天

- /agent 当您并行运行 agents 并想要在活动 agent 线程之间切换时

- /theme 选择语法高亮主题

- /apps直接在Codex中使用ChatGPT应用程序

- /status 检查当前会话状态

每个连贯的工作单元保持一次聊天。如果工作仍然是同一
问题的一部分，那么留在同一个聊天中通常会更好，因为它保留了
推理线索。仅当工作真正分支时才进行分叉。

使用 Codex 的子代理工作流程来 
 从主线程卸载有限工作。将主要 agent 集中在 
 核心问题上，并使用子代理来执行探索、测试或分类等任务。

## 常见错误

首次使用Codex时要避免的一些常见错误：

- 使用持久规则重载提示，而不是将它们移至 AGENTS.md 或技能中

- 通过不提供有关如何最好地运行构建和测试命令的详细信息，不让 agent 查看其工作

- 跳过多步骤和复杂任务的计划

- 在了解工作流程之前，先授予 Codex 对您的计算机的完全权限

- 在不使用 Git 工作树的情况下在相同文件上运行实时任务

- 在手动可靠之前安排重复性任务

- 将 Codex 视为您必须逐步观看的东西，而不是将其与您自己的工作并行使用

- 对整个项目使用一次聊天，而不是针对每个连贯的结果进行一次聊天。随着时间的推移，这会导致上下文臃肿和更糟糕的结果


## 原文对照

<details>
<summary>展开完整原文</summary>

# Best practices | ChatGPT Learn

# Best practices

Getting started with Codex and proven practices for better results

If you’re new to Codex or coding agents in general, this guide will help you get better results faster. It covers the core habits that make Codex more effective across the CLI , IDE extension , and the ChatGPT desktop app , from prompting and planning to validation, MCP, skills, and scheduled tasks.

Codex works best when you treat it less like a one-off assistant and more like a teammate you configure and improve over time.

A useful way to think about this: start with the right context for the task, use AGENTS.md for durable guidance, configure Codex to match your workflow, connect external systems with MCP, turn repeated work into skills, and automate stable workflows.

## Strong first use: Context and prompts

Codex is already strong enough to be useful even when your prompt isn’t perfect. You can often hand it a hard problem with minimal setup and still get a strong result. Clear prompting isn’t required to get value, but it does make results more reliable, especially in larger codebases or higher-stakes tasks.

If you work in a large or complex repository, the biggest unlock is giving Codex the right context for the task and a clear structure for what you want done.

A good default is to include four things in your prompt:

- Goal: What are you trying to change or build?

- Context: Which files, folders, docs, examples, or errors matter for this task? You can @ mention certain files as context.

- Constraints: What standards, architecture, safety requirements, or conventions should Codex follow?

- Done when: What should be true before the task is complete, such as tests passing, behavior changing, or a bug no longer reproducing?

This helps Codex stay scoped, make fewer assumptions, and produce work that’s easier to review.

Use GPT-6.1 Sol when available to your account and client, starting with the reasoning effort available by default in your client. Start with High for GPT-6 Luna or Light for GPT-6 Astra ( low in configuration). Adjust based on the task and the result. See Models for available reasoning levels.

- Low for faster, well-scoped tasks

- Medium or High for more complex changes or debugging

- Extra High for long, agentic, reasoning-heavy tasks

To provide context faster, try using speech dictation inside the ChatGPT
desktop app to dictate what you want Codex to do rather than typing it.

## Plan first for difficult tasks

If the task is complex, ambiguous, or hard to describe well, ask Codex to plan before it starts coding.

A few approaches work well:

Use Plan mode: For most users, this is the easiest and most effective option. Plan mode lets Codex gather context, ask clarifying questions, and build a stronger plan before implementation. Toggle with /plan or Shift + Tab .

Ask Codex to interview you: If you have a rough idea of what you want but aren’t sure how to describe it well, ask Codex to question you first. Tell it to challenge your assumptions and turn the fuzzy idea into something concrete before writing code.

Use a PLANS.md template: For more advanced workflows, you can configure Codex to follow a PLANS.md or execution-plan template for longer-running or multi-step work. For more detail, see the execution plans guide .

## Make guidance reusable with AGENTS.md

Once a prompting pattern works, the next step is to stop repeating it manually. That’s where AGENTS.md comes in.

Think of AGENTS.md as an open-format README for agents. It loads into context automatically and is the best place to encode how you and your team want Codex to work in a repository.

A good AGENTS.md covers:

- repo layout and important directories

- How to run the project

- Build, test, and lint commands

- Engineering conventions and PR expectations

- Constraints and do-not rules

- What done means and how to verify work

The /init slash command in the CLI is the quick-start command to scaffold a starter AGENTS.md in the current directory. It’s a great starting point, but you should edit the result to match how your team actually builds, tests, reviews, and ships code.

You can create AGENTS.md files at different levels: a global AGENTS.md for personal defaults that sits in ~/.codex , a repo-level file for shared standards, and more specific files in subdirectories for local rules. If there’s a more specific file closer to your current directory, that guidance wins.

Keep it practical. A short, accurate AGENTS.md is more useful than a long file full of vague rules. Start with the basics, then add new rules only after you notice repeated mistakes.

If AGENTS.md starts getting too large, keep the main file concise and reference task-specific markdown files for things like planning, code review, or architecture.

When Codex makes the same mistake twice, ask it for a retrospective and update AGENTS.md . Guidance stays practical and based on real friction.

## Configure Codex for consistency

Configuration is one of the main ways to make Codex behave more consistently across sessions and surfaces. For example, you can set defaults for model choice, reasoning effort, sandbox mode, approval policy, profiles, and MCP setup.

A good starting pattern is:

- Keep personal defaults in ~/.codex/config.toml ( Settings > Configuration > Open config.toml in the ChatGPT desktop app)

- Keep repo-specific behavior in .codex/config.toml

- Use command-line overrides only for one-off situations (if you use the CLI)

config.toml is where you define durable preferences such as MCP servers, multi-agent setup, and feature flags. Profile-specific overrides live in separate $CODEX_HOME/profile-name.config.toml files.

Codex ships with operating level sandboxing and has two key knobs that you can control. Approval mode determines when Codex asks for your permission to run a command and sandbox mode determines if Codex can read or write in the directory and what files the agent can access.

If you’re new to coding agents, start with the default permissions. Keep approval and sandboxing tight by default, then loosen permissions only for trusted repos or specific workflows once the need is clear.

Note that the CLI, IDE extension, and ChatGPT desktop app all share the same configuration layers. Learn more on the sample configuration page.

Configure Codex for your real environment early. Many quality issues are
really setup issues, like the wrong working directory, missing write access,
wrong model defaults, or missing tools and connectors.

## Improve reliability with testing and review

Don’t stop at asking Codex to make a change. Ask it to create tests when needed, run the relevant checks, confirm the result, and review the work before you accept it.

Codex can do this loop for you, but only if it knows what “good” looks like. That guidance can come from either the prompt or AGENTS.md .

That can include:

- Writing or updating tests for the change

- Running the right test suites

- Checking lint, formatting, or type checks

- Confirming the final behavior matches the request

- Reviewing the diff for bugs, regressions, or risky patterns

Toggle the diff panel in the ChatGPT desktop app to directly review
changes locally. Click on a specific row to
provide feedback that gets fed as context to the next Codex turn.

A useful option here is the slash command /review , which gives you a few ways to review code:

- Review against a base branch for PR-style review

- Review uncommitted changes

- Review a commit

- Use custom review instructions

If you and your team have a code_review.md file and reference it from AGENTS.md , Codex can follow that guidance during review as well. This is a strong pattern for teams that want review behavior to stay consistent across repositories and contributors.

Codex shouldn’t just generate code. With the right instructions, it can also help test it, check it, and review it .

If you use GitHub Cloud, you can set up Codex to run code reviews for your PRs . At OpenAI, Codex reviews 100% of PRs. You can enable automatic reviews or have Codex reactively review when you @Codex.

## Use MCPs for external context

Use MCPs when the context Codex needs lives outside the repo. It lets Codex connect to the tools and systems you already use, so you don’t have to keep copying and pasting live information into prompts.

Model Context Protocol , or MCP, is an open standard for connecting Codex to external tools and systems.

Use MCP when:

- The needed context lives outside the repo

- The data changes frequently

- You want Codex to use a tool rather than rely on pasted instructions

- You need a repeatable integration across users or projects

Codex supports both STDIO and Streamable HTTP servers with OAuth.

In the ChatGPT desktop app, go to Settings > MCP servers to see custom and recommended servers. Often, Codex can help you install the needed servers. All you need to do is ask. You can also use the codex mcp add command in the CLI to add your custom servers with a name, URL, and other details.

Add tools only when they unlock a real workflow. Do not start by wiring in
every tool you use. Start with one or two tools that clearly remove a manual
loop you already do often, then expand from there.

## Turn repeatable work into skills

Once a workflow becomes repeatable, stop relying on long prompts or repeated back-and-forth. Use a skill to package the instructions in a SKILL.md file, context, and supporting logic Codex should apply consistently. Skills work across the CLI, IDE extension, and ChatGPT desktop app.

Keep each skill scoped to one job. Start with 2 to 3 concrete use cases, define clear inputs and outputs, and write the description so it says what the skill does and when to use it. Include the kinds of trigger phrases a user would actually say.

Don’t try to cover every edge case up front. Start with one representative task, get it working well, then turn that workflow into a skill and improve from there. Include scripts or extra assets only when they improve reliability.

A good rule of thumb: if you keep reusing the same prompt or correcting the same workflow, it should probably become a skill.

Skills are especially useful for recurring jobs like:

- Log triage

- Release note drafting

- PR review against a checklist

- Migration planning

- Telemetry or incident summaries

- Standard debugging flows

The $skill-creator skill is the best place to start to scaffold the first version of a skill. Keep the first version local while you iterate. When it’s ready to share broadly, package it as a plugin . One of the most important parts of a skill is the description. It should say what the skill does and when to use it.

Personal skills are stored in $HOME/.agents/skills , and shared team skills
can be checked into .agents/skills inside a repository. This is especially
helpful for onboarding new teammates.

## Use scheduled tasks for repeated work

Once a workflow is stable, you can schedule Codex to run it in the background for you. In the ChatGPT desktop app, scheduled tasks let you choose the project, prompt, cadence, and execution environment for recurring work.

Create a scheduled task from the Scheduled page. Choose the project, prompt,
cadence, and whether the task runs in a dedicated Git worktree or in your local
environment. The prompt can invoke skills. Learn more about Git worktrees .

Good candidates include:

- Summarizing recent commits

- Scanning for likely bugs

- Drafting release notes

- Checking CI failures

- Producing standup summaries

- Running repeatable analysis workflows on a schedule

A useful rule is that skills define the method and scheduled tasks define the schedule. If a workflow still needs a lot of steering, turn it into a skill first. Once it’s predictable, scheduling it can save time.

Use scheduled tasks for reflection and maintenance, not just execution. Review
recent chats, summarize repeated friction, and improve prompts, instructions,
or workflow setup over time.

## Organize long-running chats

Chats accumulate context, decisions, and actions over time, so managing them well has a big impact on quality.

The ChatGPT desktop app lets you pin chats and create worktrees. If you use the
CLI, these slash commands are especially useful:

- /experimental to toggle experimental features and add to your config.toml

- /resume to resume a saved chat

- /fork to create a new chat while preserving the original transcript

- /compact when the chat is getting long and you want a summarized version of earlier context. Codex also compacts chats automatically

- /agent when you are running parallel agents and want to switch between the active agent thread

- /theme to choose a syntax highlighting theme

- /apps to use ChatGPT apps directly in Codex

- /status to inspect the current session state

Keep one chat per coherent unit of work. If the work is still part of the same
problem, staying in the same chat is often better because it preserves the
reasoning trail. Fork only when the work truly branches.

Use Codex’s subagent workflows to
offload bounded work from the main thread. Keep the main agent focused on the
core problem, and use subagents for tasks like exploration, tests, or triage.

## Common mistakes

A few common mistakes to avoid when first using Codex:

- Overloading the prompt with durable rules instead of moving them into AGENTS.md or a skill

- Not letting the agent see its work by not giving details on how to best run build and test commands

- Skipping planning on multi-step and complex tasks

- Giving Codex full permission to your computer before you understand the workflow

- Running live tasks on the same files without using Git worktrees

- Scheduling a recurring task before it’s reliable manually

- Treating Codex like something you have to watch step by step instead of using it in parallel with your own work

- Using one chat for an entire project instead of one chat per coherent outcome. This leads to bloated context and worse results over time


</details>
