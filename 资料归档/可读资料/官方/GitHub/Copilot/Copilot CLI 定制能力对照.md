# Copilot CLI 定制能力对照

比较自定义指令、Skills、工具、MCP 等扩展方式，说明各自解决的问题和适用时机，帮助选择项目配置方式。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/agents/copilot-cli/comparing-cli-features.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot CLI 定制能力对照.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Comparing GitHub Copilot CLI customization features
shortTitle: Comparing CLI features
intro: 'Find out about the various ways you can customize {% data variables.product.prodname_copilot_short %}: what they do, and when to use them.'
product: '{% data reusables.gated-features.copilot-cli %}'
versions:
  feature: copilot
contentType: concepts
category:
  - Learn about Copilot # Copilot discovery page
  - Learn about Copilot CLI # Copilot CLI bespoke page
docsTeamMetrics:
  - copilot-cli
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 简介

{% data variables.copilot.copilot_cli_short %} 是基于终端的 AI agent，可以代表您回答问题、计划工作和完成任务。它被设计为高度可扩展的，您可以通过多种方式自定义其行为并扩展其功能。

本文解释了以下之间的区别：

* **定制说明**

这些告诉{% data variables.product.prodname_copilot_short %} **一般如何表现**。例如，确保{% data variables.product.prodname_copilot_short %}编写的任何代码都符合您的编码标准。 [了解更多](<#定制说明>)。

* **技能**

这些告诉{% data variables.product.prodname_copilot_short %} **如何处理特定类型的任务**。例如，在处理特定类型的任务时使用特定工具。 [了解更多](<#技能>)。

* **工具**

这些**提供能力**。例如，用于查找和修改文件，或与 {% data variables.product.github %} 的某些部分进行交互。 [了解更多](<#工具>)。

* **MCP 服务器**

这些 ** 添加了工具集合 **，允许 {% data variables.product.prodname_copilot_short %} 与外部服务交互。 [了解更多](<#mcp-服务器>)。

* **挂钩**

这些让您**在特定的生命周期时刻运行自己的逻辑**。例如，您可以在每次 CLI 会话开始或结束时运行特定脚本。 [了解更多](<#挂钩>)。

* **子代理**

这些是 ** 委托的 agent 进程 **，与主 agent 绑定，用于与主 agent 进程分开执行特定任务。它们有自己的上下文窗口，可以在不影响主 agent 上下文的情况下填充该窗口。 [了解更多](<#子代理>)。

* **定制 agents**

这些是**专门能力的定义**，旨在执行特定任务。主 CLI agent 可以使用自定义 agent 配置文件将任务委托给子代理，以应用专业知识和特定方法来执行任务。例如，自定义 agent 可能扮演 React 审阅者、文档编写者、安全审核员或测试生成器的角色。 [了解更多](<#定制agents>)。

* **插件**

这些是 ** 软件包 **，可以提供预配置的自定义项，例如技能、挂钩、自定义 agents 和 MCP 服务器。 [了解更多](<#插件>)。

## 定制说明

### 什么是自定义指令？

**自定义指令**是{% data variables.copilot.copilot_cli_short %}在会话开始时从指令文件加载的持续指导。

{% data variables.product.prodname_copilot_short %} 将从存储库中的许多默认位置查找并加载指令文件，例如 `AGENTS.md` 和 `.github/copilot-instructions.md`，或从位于 `$HOME/.copilot/copilot-instructions.md` 的主目录。

您可以使用 `--no-custom-instructions` 标志来避免加载这些指令。

### 自定义指令解决什么问题？

定制说明可以帮助您：
* 保持 {% data variables.product.prodname_copilot_short %} 与您的编码约定和偏好保持一致。
* 一致地应用团队或组织标准。
* 避免在每个提示中都包含对 {% data variables.product.prodname_copilot_short %} 的重复提醒。

### 什么时候应该使用自定义指令？

使用自定义指令：

* 风格和质量规则

示例：“更喜欢小型 PR，编写测试，并避免未经讨论就更改公共 API。”

* 存储库约定

示例：“使用 `pnpm`，将变更日志条目保留在 `CHANGELOG.md` 中，在提交之前运行 `pnpm test`。”

* 通讯偏好

示例：“简要解释权衡，然后提供推荐的选择。”

### 什么时候不应该使用自定义指令？

在以下情况下避免或尽量减少它们：

* 您只需要一个工作流程中的行为（请改用 ** 技能 **）。
* 你的指令large/specific会分散{% data variables.product.prodname_copilot_short %}当前任务的注意力（更喜欢**技能**或**自定义agent**）。

### 了解有关自定义说明的更多信息

参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions>)。

## 技能

### 什么是技能？

**技能**至少是一个Markdown文件，其中包含{% data variables.product.prodname_copilot_short %}可用于在特定上下文中执行任务的指令。名称和技能描述允许 {% data variables.product.prodname_copilot_short %} 确定是否应该为给定任务使用该技能。如果它决定使用该技能，它将加载指令并按照它们完成任务。

技能可以选择引用存储在技能目录中的其他文件。这些可以包括使用该技能时 {% data variables.product.prodname_copilot_short %} 可以运行的脚本。

### 技能解决什么问题？

技能可以帮助您：
* 标准化 {% data variables.product.prodname_copilot_short %} 在特定上下文中执行任务的方式（例如，执行代码审查时）。
* 提供“即时”指令，而不永久改变 {% data variables.product.prodname_copilot_short %} 的行为。
* 避免使用与当前任务无关的指令使 {% data variables.product.prodname_copilot_short %} 的上下文窗口过载。

### 你如何获得技能？

您可以使用斜杠命令手动调用技能。例如，`/Markdown-Checker check README.md`。使用`/skills list`列出可用的技能。

{% data variables.copilot.copilot_cli_short %} 当检测到与当前任务相关的技能时，会自动调用技能。

### 什么时候应该使用技能？

当你需要时使用技能：

* 可用于某类任务的一组可重复的指令或功能。

示例：一种文档技能，用于在前端代码更改时检查面向用户的文档是否已更新。

* 一致的输出格式。

示例：“发行说明草稿”技能，确保 {% data variables.product.prodname_copilot_short %} 使用模板创建发行说明。

* 您有时需要但并非总是需要的工作流程。

示例：仅在迁移期间启用的“深度重构”技能。

### 什么时候不应该使用技能？

在以下情况下避免使用技能：

* 该指南应**适用于您所做的一切**（请改用**自定义说明**）。
* 您需要新功能（您可能需要 **MCP 服务器 ** 来添加工具，或 ** 自定义 agent** 进行专业化）。

### 了解更多有关agent skills的信息

参见[AUTOTITLE](<https://github.com/copilot/concepts/agents/about-agent-skills>)。

## 工具

### 什么是工具？

**工具**是{% data variables.product.prodname_copilot_short %}用来完成某些任务的能力，例如搜索文件、查看文件内容、编辑、运行任务或调用技能。有些工具是内置的，其他工具可以通过 MCP 服务器添加。

### 工具解决什么问题？

工具让CLI：

* 收集准确的上下文（使用 read/search 工具）。
* 安全地进行更改（使用编辑工具）。
* 执行命令并验证结果（可能使用子代理）。

### 什么时候应该使用工具？

您通常不会直接调用工具 - {% data variables.product.prodname_copilot_short %} 决定根据需要使用工具。您可以允许或拒绝对特定任务、当前会话或所有 {% data variables.copilot.copilot_cli_short %} 会话使用工具。

当您执行以下操作时，您将看到 {% data variables.product.prodname_copilot_short %} 使用工具：

* 要求 {% data variables.product.prodname_copilot_short %} 在存储库中搜索某些内容、更新文件或运行测试。
* 调用一项技能——触发`skill`工具。
* 要求 {% data variables.product.prodname_copilot_short %} 执行一项需要使用 MCP 服务器提供的工具的任务。
* 任务 {% data variables.product.prodname_copilot_short %} 完成一项复杂的任务，它决定委托给子代理，这会触发 `task` 工具。

### 了解有关允许或拒绝工具的更多信息

参见[AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli#allowed-tools>)。

## MCP 服务器

### 什么是MCP服务器？

**MCP 服务器 ** 是一项允许 AI 应用程序（例如 {% data variables.copilot.copilot_cli_short %}）连接到外部数据源和工具的服务。

将 MCP 服务器添加到 {% data variables.copilot.copilot_cli_short %} 可以提供附加功能，允许您使用该 MCP 服务器提供的工具。例如，您可以添加一个 MCP 服务器，该服务器提供与在线日历应用程序或支持票务系统交互的工具。

### MCP服务器解决什么问题？

当内置工具不够用时，MCP 服务器可以提供帮助。他们可以：

* 将 {% data variables.copilot.copilot_cli_short %} 连接到外部系统。
* 添加专用工具（例如，用于使用 API、数据库或图像生成）。
* 标准化非存储库资源的安全访问模式。

### 什么时候应该使用 MCP 服务器？

当您需要时，请使用 MCP 服务器：

* 与外部数据或系统集成。

示例：`How many support tickets have been opened this month for Product X?`

* 您希望 CLI 代表您执行的域特定操作。

示例：`Message the bug-watch channel: Only 2 support tickets raised this month for Product X.`

### 什么时候不应该使用 MCP 服务器？

在以下情况下避免添加 MCP 服务器：

* 内置工具已经满足您的需求。

### 了解有关 MCP 服务器的更多信息

参见[AUTOTITLE](<https://github.com/copilot/concepts/context/mcp>)。

## 挂钩

### 什么是钩子？

**Hooks** 允许您指定在会话生命周期的给定点，{% data variables.copilot.copilot_cli_short %} 将执行您定义的 shell 命令。

|钩子|当它运行时|
| ---- | ------------ |
| `preToolUse` / `postToolUse` |Before/after 工具运行。|
| `userPromptSubmitted` |当用户提交提示时。|
| `sessionStart` / `sessionEnd` |在会话的 start/end 处。|
| `errorOccurred` |当发生错误时。|
| `agentStop` |当主agent无错误地停止时。|
| `subagentStop` |当子代理完成时。|

### hooks 解决什么问题？

当您想要围绕 {% data variables.copilot.copilot_cli_short %} 行为进行 ** 可编程控制或可观察性 ** 时，挂钩会有所帮助，例如：

* **加强护栏**——在某些工具运行之前阻止或警告。
* **添加logging/telemetry**
* **自定义可恢复错误的 retry/abort 行为 **
* **添加“策略”检查** - 例如，防止编辑受保护的路径。
* **拦截子代理完成的时刻**——在结果返回父代理之前agent。

当您需要比技能或自定义指令所能提供的更多控制权时，挂钩非常有用。虽然技能和说明通过提示指导 {% data variables.product.prodname_copilot_short %} 的行为，但挂钩可确保您定义的操作将在特定时刻执行 - 例如，阻止工具运行，或在会话结束时记录活动。

### 什么时候应该使用hooks？

当你需要时使用钩子：

* **工具护栏**

  * 示例：在 `bash` 运行之前，要求特定命令与白名单匹配。
  * 示例：在 `edit` 运行之前，除非存在票证 ID，否则在 `infra/` 下阻止更改。

* **会话生命周期自动化**

  * 示例：当 agent 停止时，将会话的记录存档到存储位置。

* **错误处理策略**

  * 示例：对于速率限制错误，自动选择具有上限重试次数的“重试”。

* **子代理工作流程控制**

  * 示例：当子代理完成时，在将结果传递回主代理之前验证其输出 agent。

### 什么时候不应该使用钩子？

在以下情况下避免使用钩子：

* 您只需要一致的提示或工作流程说明（使用**技能**）。
* 您需要持久的偏好和标准（使用**自定义说明**）。
* 您需要新的外部功能（使用**MCP服务器**和工具）。
* 维护可能影响每个会话的配置可能会给您带来麻烦。

### 了解有关钩子的更多信息

参见：
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/use-hooks>)
* [AUTOTITLE](<https://github.com/copilot/reference/hooks-reference>)

## 子代理

### 什么是子代理？

**子代理**是一个单独的AI agent的执行，{% data variables.copilot.copilot_cli_short %}会话的主agent旋转起来以完成特定的工作。

当主 agent 决定将一大块工作委托给单独的 agent 是完成用户请求的最佳方式时，{% data variables.copilot.copilot_cli_short %} 使用子代理。

### 子代理解决什么问题？

子代理帮助{% data variables.product.prodname_copilot_short %}：

* 通过将一大块工作卸载到单独的 agent，保持 CLI 会话中主 agent 的上下文窗口的焦点。
* 必要时，通过在后台运行某些任务来并行工作。
* 与主 agent 分开运行自定义 agent，以与主 agent 执行的工作不同的方法执行专业工作。

### 什么时候使用子代理？

{% data variables.product.prodname_copilot_short %} 可能将子代理用于：

* 代码库探索

例如，列出 API 中的所有端点。

* 复杂任务的命令执行

例如，运行测试套件，或构建大型项目并分析结果。

* 审查变更

例如，审查分阶段的变更并识别潜在的安全问题。

* 复杂的多步骤工作

例如，通过多次更改来实现某个功能。

* 用于使用自定义agents

如果您定义了自定义 agent 并且它符合推理条件（`infer` 未设置为 `false`），{% data variables.product.prodname_copilot_short %} 可以选择通过使用自定义 agent 配置启动子代理，将工作委派给该自定义 agent。

## 定制【agents】

### 什么是自定义agent？

**自定义agents**为{% data variables.product.prodname_copilot_short %}提供有关特定主题的专业知识，并定义{% data variables.product.prodname_copilot_short %}在该领域工作时应使用的特定方法。您可以将自定义 agent 视为 {% data variables.product.prodname_copilot_short %} 在执行某些任务时可以采用的“角色”。

{% data variables.copilot.copilot_cli_short %}内置了多个自定义agents。例如，`explore`、`task`、`research`、`code-review`、`rubber-duck`和`general-purpose` agents。您还可以定义自己的自定义agents，以满足您的特定需求。

您可以在带有 YAML frontmatter 的 Markdown 文件中定义自定义 agent。该文件包含：

* agent 的角色和专业知识的描述
* 允许的工具列表（或所有工具）
* 可选的 MCP 服务器连接
* 可选的 `infer` 设置—启用后， {% data variables.product.prodname_copilot_short %} 会自动委托给这个 agent 当它检测到匹配的任务时 agent的专长。

### 定制【agents】解决什么问题？

当您需要时自定义 agents 帮助：

* 在特定环境中一致应用的专业知识。
* 不同的工具权限适用于不同的工作，如自定义 agent 配置中所定义。
* 允许主 agent 的上下文窗口专注于主要任务，而自定义 agent 自己的上下文窗口则用于其执行的专业工作。

### 什么时候应该使用自定义 agent？

当您需要时，请使用自定义 agent：

* 专门的审稿人或帮助者

示例：创建一个“react-reviewer”自定义 agent，专注于涉及 React 模式的工作。

* 更安全的权限

示例：自定义agent只能`view/grep/glob`（只读）进行审核。

* 可选的自动委派

示例：在自定义agent配置中设置`infer: true`，以便{% data variables.product.prodname_copilot_short %}可以在适当的时候自动使用此自定义agent。

### 什么时候不应该使用自定义 agent？

在以下情况下避免自定义 agents：

* 您只需要指导文本（**技能**可以是一个更轻量级的解决方案）。
* 您不需要专门化，默认的 agent 可以很好地执行任务。

### 了解更多关于定制的信息 agents

参见[AUTOTITLE](<https://github.com/copilot/reference/custom-agents-configuration>)。

## 插件

### 什么是插件？

**插件**是一个可安装的软件包，可以向{% data variables.product.prodname_copilot_short %}提供一组功能。插件可以包含其他自定义功能的任意组合。例如，技能、自定义 agents、挂钩和 MCP 服务器配置。

{% data variables.product.prodname_copilot_short %} 包括插件管理命令（安装、更新、列出、卸载），并支持从市场或直接从 GitHub 存储库安装。

### 插件解决什么问题？

插件可以帮助您：

* 轻松向 {% data variables.product.prodname_copilot_short %} 添加一组功能，而无需手动配置每个部分。
* 将自定义配置（可能是技能、自定义 agents、挂钩和 MCP 服务器的组合）打包并分发给您的团队或公众。
* 无需手动将文件复制到目录中即可更改可用功能。

### 什么时候应该使用插件？

在以下情况下使用插件：

* 您想要一个团队范围的捆绑包

示例：公司范围内的工程插件，包括：

  * 事件响应技能。
  * 用于代码审查的自定义 agent。
  * 用于内部服务的MCP服务器。

* 您想要轻松安装和更新

示例：首先安装插件，然后使用 `/plugin update PLUGIN-NAME` 定期更新。

### 什么时候不应该使用插件？

在以下情况下避免使用插件：

* 您正在本地进行试验，不需要分发（使用本地技能、自定义指令或自定义 agents）。
* 您只需要一个小型的一次性工作流程。单个技能文件可能更简单。

## 放在一起：选择正确的选项

|要求|最佳选择|
|---|---|
|我希望 {% data variables.product.prodname_copilot_short %} 始终遵循我们的存储库约定。|**定制说明**|
|我想要一个可以按需调用的可重复工作流程。|**技能**|
|我希望 {% data variables.product.prodname_copilot_short %} 回答问题并在我的存储库中开展工作。|{% data variables.product.prodname_copilot_short %} 请求使用适当的 ** 工具 ** 的权限|
|我想要围绕工具使用和会话事件的护栏、策略或自动化。|**挂钩**|
|我需要 {% data variables.product.prodname_copilot_short %} 才能使用外部服务提供的工具。|**MCP 服务器**|
|在处理特定任务时，我希望 {% data variables.product.prodname_copilot_short %} 作为专家使用有限的工具集进行操作。|**定制 agent**|
|我希望 {% data variables.product.prodname_copilot_short %} 代表我执行一项复杂的任务。|{% data variables.product.prodname_copilot_short %} 在适当的时候自动使用 ** 子代理 **。|
|我想向 {% data variables.copilot.copilot_cli_short %} 添加一个功能包，而无需自己手动配置。|**插件**|

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## Introduction

{% data variables.copilot.copilot_cli_short %} is a terminal-based AI agent that can answer questions, plan work, and complete tasks on your behalf. It’s designed to be highly extensible, and there are various ways in which you can customize its behavior and extend its capabilities.

This article explains the difference between:

* **Custom instructions**

  These tell {% data variables.product.prodname_copilot_short %} **how to behave** in general. For example, to ensure any code that {% data variables.product.prodname_copilot_short %} writes conforms to your coding standards. [Find out more](<#custom-instructions>).

* **Skills**

  These tell {% data variables.product.prodname_copilot_short %} **how to handle a specific kind of task**. For example, to use a particular tool when working on a specific type of task. [Find out more](<#skills>).

* **Tools**

  These **provide abilities**. For example, for finding and modifying files, or for interacting with parts of {% data variables.product.github %}. [Find out more](<#tools>).

* **MCP servers**

  These **add collections of tools** that allow {% data variables.product.prodname_copilot_short %} to interact with external services. [Find out more](<#mcp-servers>).

* **Hooks**

  These let you **run your own logic at specific lifecycle moments**. For example, you can run a specific script every time a CLI session starts or ends. [Find out more](<#hooks>).

* **Subagents**

  These are **delegated agent processes**, tied to the main agent and used to perform specific tasks separately from the main agent process. They have their own context window, which can be populated without affecting the main agent's context. [Find out more](<#subagents>).

* **Custom agents**

  These are **definitions of specialized abilities**, designed to perform specific tasks. The main CLI agent can delegate a task to a subagent, using a custom agent profile, to apply specialist knowledge and a particular approach to the task. For example, a custom agent might perform the role of a React reviewer, a docs writer, a security auditor, or a test generator. [Find out more](<#custom-agents>).

* **Plugins**

  These are **packages** that can deliver preconfigured customizations such as skills, hooks, custom agents, and MCP servers. [Find out more](<#plugins>).

## Custom instructions

### What are custom instructions?

**Custom instructions** are persistent guidance that the {% data variables.copilot.copilot_cli_short %} loads from instruction files at the start of a session.

{% data variables.product.prodname_copilot_short %} will find and load instruction files from a number of default locations in the repository, such as `AGENTS.md` and `.github/copilot-instructions.md`, or from your home directory at `$HOME/.copilot/copilot-instructions.md`.

You can use the `--no-custom-instructions` flag to avoid loading these instructions.

### What problem do custom instructions solve?

Custom instructions help you:
* Keep {% data variables.product.prodname_copilot_short %} aligned with your coding conventions and preferences.
* Apply team or organization standards consistently.
* Avoid having to include repetitive reminders to {% data variables.product.prodname_copilot_short %} in every prompt.

### When should you use custom instructions?

Use custom instructions for:

* Style and quality rules

  Example: "Prefer small PRs, write tests, and avoid changing public APIs without discussion."

* Repository conventions

  Example: "Use `pnpm`, keep changelog entries in `CHANGELOG.md`, run `pnpm test` before committing."

* Communication preferences

  Example: “Explain tradeoffs briefly, then provide the recommended choice.”

### When shouldn't you use custom instructions?

Avoid or keep them minimal when:

* You only want the behavior in one workflow (use a **skill** instead).
* Your instructions are so large/specific they distract {% data variables.product.prodname_copilot_short %} from the immediate task (prefer a **skill** or a **custom agent**).

### Find out more about custom instructions

See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions>).

## Skills

### What is a skill?

A **skill** is, minimally, a Markdown file containing instructions that {% data variables.product.prodname_copilot_short %} can use to perform tasks in a specific context. The name and skill description allow {% data variables.product.prodname_copilot_short %} to determine whether it should use the skill for a given task. If it decides to use the skill, it will load the instructions and follow them to complete the task.

Skills can optionally reference other files, stored within the skill directory. These can include scripts that {% data variables.product.prodname_copilot_short %} can run when the skill is used.

### What problem does a skill solve?

Skills help you:
* Standardize how {% data variables.product.prodname_copilot_short %} performs tasks in a specific context (for example, when performing a code review).
* Provide "just-in-time" instructions without permanently changing {% data variables.product.prodname_copilot_short %}'s behavior.
* Avoid overloading {% data variables.product.prodname_copilot_short %}'s context window with instructions that are not relevant to the current task.

### How do you access skills?

You can manually invoke a skill by using a slash command. For example, `/Markdown-Checker check README.md`. Use `/skills list` to list the available skills.

{% data variables.copilot.copilot_cli_short %} automatically invokes skills when it detects one that is relevant to the current task.

### When should you use a skill?

Use a skill when you want:

* A repeatable set of instructions or functionality to be available for a type of task.

  Example: a documentation skill that checks that user-facing documentation is updated when frontend code is changed.

* A consistent output format.

  Example: a "release note draft" skill that ensures {% data variables.product.prodname_copilot_short %} uses a template to create a release note.

* A workflow you sometimes need, but not always.

  Example: a “deep refactor” skill you only enable during migrations.

### When shouldn't you use a skill?

Avoid skills when:

* The guidance should **apply to everything** you do (use **custom instructions** instead).
* You need new capabilities (you may need an **MCP server** to add tools, or a **custom agent** for specialization).

### Find out more about agent skills

See [AUTOTITLE](<https://github.com/copilot/concepts/agents/about-agent-skills>).

## Tools

### What is a tool?

A **tool** is an ability that {% data variables.product.prodname_copilot_short %} uses to get something done—like searching files, viewing file contents, editing, running a task, or invoking a skill. Some tools are built in, and others can be added through MCP servers.

### What problem do tools solve?

Tools let the CLI:

* Gather accurate context (using read/search tools).
* Make changes safely (using edit tools).
* Execute commands and validate outcomes (potentially using subagents).

### When should you use tools?

You typically don't call tools directly—{% data variables.product.prodname_copilot_short %} decides to use tools as needed. You can allow or deny use of tools, either for a specific task, for the current session, or for all of your {% data variables.copilot.copilot_cli_short %} sessions.

You’ll see {% data variables.product.prodname_copilot_short %} using tools when you:

* Ask {% data variables.product.prodname_copilot_short %} to search the repository for something, update a file, or run tests.
* Invoke a skill—which triggers the `skill` tool.
* Ask {% data variables.product.prodname_copilot_short %} to perform a task that requires it to use a tool supplied by an MCP server.
* Task {% data variables.product.prodname_copilot_short %} to complete a complex task and it decides to delegate to a subagent—which triggers the `task` tool.

### Find out more about allowing or denying tools

See [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli#allowed-tools>).

## MCP servers

### What is an MCP server?

An **MCP server** is a service that allows AI applications, such as {% data variables.copilot.copilot_cli_short %}, to connect to external data sources and tools.

Adding an MCP server to {% data variables.copilot.copilot_cli_short %} provides additional capabilities, by allowing you to use tools supplied by that MCP server. For example, you could add an MCP server that provides tools for interacting with an online calendar application, or a support ticketing system.

### What problem do MCP servers solve?

MCP servers help when the built-in tools aren’t enough. They can:

* Connect {% data variables.copilot.copilot_cli_short %} to external systems.
* Add purpose-built tools (for example, for working with APIs, databases, or image generation).
* Standardize safe access patterns for non-repository resources.

### When should you use an MCP server?

Use an MCP server when you need:

* Integration with external data or systems.

  Example: `How many support tickets have been opened this month for Product X?`

* Domain-specific actions that you want the CLI to perform on your behalf.

  Example: `Message the bug-watch channel: Only 2 support tickets raised this month for Product X.`

### When shouldn't you use an MCP server?

Avoid adding MCP servers when:

* Built-in tools already cover your needs.

### Find out more about MCP servers

See [AUTOTITLE](<https://github.com/copilot/concepts/context/mcp>).

## Hooks

### What is a hook?

**Hooks** allow you to specify that, at a given point in a session lifecycle, {% data variables.copilot.copilot_cli_short %} will execute a shell command you have defined.

| Hook | When it runs |
| ---- | ------------ |
| `preToolUse` / `postToolUse` | Before/after a tool runs. |
| `userPromptSubmitted` | When a user submits a prompt. |
| `sessionStart` / `sessionEnd` | At the start/end of a session. |
| `errorOccurred` | When an error occurs. |
| `agentStop` | When the main agent stops without an error. |
| `subagentStop` | When a subagent completes. |

### What problem do hooks solve?

Hooks help when you want **programmable control or observability** around {% data variables.copilot.copilot_cli_short %} behavior, such as:

* **Enforcing guardrails**—block or warn before certain tools run.
* **Adding logging/telemetry**
* **Customizing retry/abort behavior on recoverable errors**
* **Adding "policy" checks**—for example, to prevent edits to protected paths.
* **Intercepting the moment a subagent finishes**—before results return to the parent agent.

Hooks are useful when you need more control than skills or custom instructions can provide. While skills and instructions guide {% data variables.product.prodname_copilot_short %}'s behavior through prompts, hooks ensure that operations you have defined will be performed at specific moments—for example, to block a tool from running, or to log activity when a session ends.

### When should you usehooks?

Use hooks when you want:

* **Tool guardrails**

  * Example: before `bash` runs, require that the specific command matches an allowlist.
  * Example: before `edit` runs, block changes under `infra/` unless a ticket ID is present.

* **Session lifecycle automation**

  * Example: when the agent stops, archive the transcript of the session to a storage location.

* **Error handling policy**

  * Example: on rate limit errors, automatically choose "retry" with a capped retry count.

* **Subagent workflow control**

  * Example: when a subagent finishes, validate its output before passing results back to the main agent.

### When shouldn't you use hooks?

Avoid hooks when:

* You just need consistent prompting or workflow instructions (use **skills**).
* You want persistent preferences and standards (use **custom instructions**).
* You need new external capabilities (use **MCP servers** and tools).
* Maintaining configuration that can affect every session may be problematic for you.

### Find out more about hooks

See:
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/use-hooks>)
* [AUTOTITLE](<https://github.com/copilot/reference/hooks-reference>)

## Subagents

### What is a subagent?

A **subagent** is the execution of a separate AI agent that the main agent of a {% data variables.copilot.copilot_cli_short %} session spins up to do a specific piece of work.

{% data variables.copilot.copilot_cli_short %} uses a subagent when the main agent decides that delegating a chunk of work to a separate agent is the best way to complete the user’s request.

### What problem do subagents solve?

Subagents help {% data variables.product.prodname_copilot_short %}:

* Keep the context window of the main agent in a CLI session focused, by offloading a chunk of work to a separate agent.
* Parallelize work, where necessary, by running certain tasks in the background.
* Run a custom agent separately from the main agent, performing specialist work with a different approach to the work carried out by the main agent.

### When are subagents used?

{% data variables.product.prodname_copilot_short %} is likely to use a subagent for:

* Codebase exploration

  For example, listing all endpoints in an API.

* Command execution for complex tasks

  For example, running a test suite, or building a large project and analyzing the results.

* Reviewing changes

  For example, reviewing staged changes and identifying potential security issues.

* Complex multi-step work

  For example, implementing a feature with several changes.

* For using custom agents

  If you’ve defined a custom agent and it’s eligible for inference (`infer` is not set to `false`), {% data variables.product.prodname_copilot_short %} may choose to delegate work to that custom agent by spinning up a subagent with the custom agent's configuration.

## Custom agents

### What is a custom agent?

**Custom agents** provide {% data variables.product.prodname_copilot_short %} with specialist knowledge about a particular subject, and define a particular approach that {% data variables.product.prodname_copilot_short %} should use when working in that area. You can think of a custom agent as a "persona" that {% data variables.product.prodname_copilot_short %} can adopt when working on certain tasks.

{% data variables.copilot.copilot_cli_short %} has several built-in custom agents. For example, the `explore`, `task`, `research`, `code-review`, `rubber-duck`, and `general-purpose` agents. You can also define your own custom agents, to meet your specific needs.

You define a custom agent in a Markdown file with YAML frontmatter. The file contains:

* A description of the agent's role and expertise
* A list of allowed tools (or all tools)
* Optional MCP server connections
* An optional `infer` setting—when enabled, {% data variables.product.prodname_copilot_short %} will automatically delegate to this agent when it detects a task that matches the agent's specialty.

### What problem do custom agents solve?

Custom agents help when you need:

* Specialist knowledge to be applied consistently in a particular context.
* Different tool permissions for different work, as defined in the custom agent configuration.
* To allow the main agent's context window to stay focused on the main task, with the custom agent's own context window being used for the specialist work it performs.

### When should you use a custom agent?

Use a custom agent when you want:

* A specialized reviewer or helper

  Example: Create a "react-reviewer" custom agent that focuses on work involving React patterns.

* Safer permissions

  Example: A custom agent that can only `view/grep/glob` (read-only) for auditing.

* Optional auto-delegation

  Example: Set `infer: true` in the custom agent configuration so that {% data variables.product.prodname_copilot_short %} can automatically use this custom agent when appropriate.

### When shouldn't you use a custom agent?

Avoid custom agents when:

* You only need guidance text (a **skill** can be a lighter-weight solution).
* You don't need specialization and the default agent performs tasks well.

### Find out more about custom agents

See [AUTOTITLE](<https://github.com/copilot/reference/custom-agents-configuration>).

## Plugins

### What is a plugin?

A **plugin** is an installable package that can deliver a bundle of functionality to {% data variables.product.prodname_copilot_short %}. A plugin can include any combination of the other customization features. For example, skills, custom agents, hooks, and MCP server configurations.

{% data variables.product.prodname_copilot_short %} includes plugin management commands (install, update, list, uninstall) and supports installing from a marketplace or directly from a GitHub repository.

### What problem do plugins solve?

Plugins help you:

* Easily add a bundle of functionality to {% data variables.product.prodname_copilot_short %} without having to manually configure each piece.
* Package and distribute a custom configuration—potentially a combination of skills, custom agents, hooks, and MCP servers—to your team, or to the public.
* Alter available functionality without having to manually copy files into directories.

### When should you use a plugin?

Use a plugin when:

* You want a team-wide bundle

  Example: A company-wide engineering plugin that includes:

  * Skills for incident response.
  * A custom agent for code review.
  * An MCP server for internal services.

* You want easy installation and updates

  Example: Install a plugin initially, then update it regularly using `/plugin update PLUGIN-NAME`.

### When shouldn't you use a plugin?

Avoid plugins when:

* You're experimenting locally and don't need distribution (use local skills, custom instructions, or custom agents).
* You only need a small one-off workflow. A single skill file may be simpler.

## Putting it together: choosing the right option

| Requirement | Best option |
|---|---|
| I want {% data variables.product.prodname_copilot_short %} to always follow our repository conventions.  | **Custom instructions** |
| I want a repeatable workflow I can invoke on demand. | **Skills** |
| I want {% data variables.product.prodname_copilot_short %} to answer questions and carry out work in my repository. | {% data variables.product.prodname_copilot_short %} requests permission to use the appropriate **tools** |
| I want guardrails, policy, or automation around tool use and session events. | **Hooks** |
| I need {% data variables.product.prodname_copilot_short %} to be able to use tools provided by an external service. | **MCP servers** |
| When working on particular tasks, I want {% data variables.product.prodname_copilot_short %} to operate as a specialist with a constrained toolset. | **Custom agent** |
| I want {% data variables.product.prodname_copilot_short %} to carry out a complex task on my behalf. | {% data variables.product.prodname_copilot_short %} automatically uses **subagents** when appropriate. |
| I want to add a package of functionality to {% data variables.copilot.copilot_cli_short %} without configuring it manually myself. | **Plugin** |

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli>)


</details>
