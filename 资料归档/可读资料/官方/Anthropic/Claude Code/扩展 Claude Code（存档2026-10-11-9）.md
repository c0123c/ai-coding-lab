# 扩展 Claude Code

比较项目指令、Skills、子 Agent、Hooks、MCP 和插件的用途、加载方式及上下文成本，说明如何按任务组合这些能力并逐步完善配置。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-04](<https://code.claude.com/docs/en/features-overview>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/features-overview.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/扩展 Claude Code（存档2026-10-11-9）.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 延长Claude Code

> 了解何时使用 CLAUDE.md、技能、子代理、挂钩、MCP 和插件。

Claude Code 将推理代码的模型与 [ 内置工具 ](<https://code.claude.com/docs/en/how-claude-code-works#tools>) 相结合，用于文件操作、搜索、执行和 Web 访问。内置工具涵盖了大多数编码任务。本指南涵盖了扩展层：您添加的功能可以自定义 Claude 所知道的内容、将其连接到外部服务以及自动化工作流程。



**注释：**

有关核心代理循环的工作原理，请参阅[Claude Code如何工作](<https://code.claude.com/docs/en/how-claude-code-works>)。


**Claude Code新功能？**从[CLAUDE.md](<https://code.claude.com/docs/en/memory>)开始进行项目约定，然后在出现特定触发器时添加其他扩展[](<#随着时间的推移建立您的设置>)。

## 概述

扩展插入代理循环的不同部分：

* **[CLAUDE.md](<https://code.claude.com/docs/en/memory>)** 添加持久上下文 Claude 查看每个会话
* **[输出样式](<https://code.claude.com/docs/en/output-styles>)**为会话中的每个响应设置Claude的角色、语气和响应格式
* **[技能](<https://code.claude.com/docs/en/skills>)**添加可重用的知识和可调用的工作流程
* **[代码智能](<https://code.claude.com/docs/en/tools-reference#lsp-tool-behavior>)**将Claude连接到语言服务器以进行符号级导航和实时类型错误
* **[MCP](<https://code.claude.com/docs/en/mcp>)** 将 Claude 连接到外部服务和工具
* **[子代理](<https://code.claude.com/docs/en/sub-agents>)**在隔离上下文中运行自己的循环，返回摘要
* **[动态工作流程](<https://code.claude.com/docs/en/workflows>)**从脚本Claude写入运行多个子代理，返回一个结果
* **[跨会话消息传递](<https://code.claude.com/docs/en/cross-session-messaging>)**允许Claude将消息从一个会话传递到另一个会话
* 当 Claude Code 到达生命周期事件时，**[Hooks](<https://code.claude.com/docs/en/hooks-guide>)** 运行脚本、HTTP 请求、MCP 工具调用、提示或子代理
* **[插件](<https://code.claude.com/docs/en/plugins/overview>)**和**[市场](<https://code.claude.com/docs/en/plugins/overview>)**打包并分发这些功能

[技能](<https://code.claude.com/docs/en/skills>)是最灵活的扩展。技能是包含知识、工作流程或说明的 Markdown 文件。您可以使用 `/deploy` 等命令调用技能，或者 Claude 可以在相关时自动加载它们。技能可以在您当前的对话中运行，也可以通过子代理在隔离的上下文中运行。

## 将功能与您的目标相匹配

功能范围从 Claude 看到每个会话的始终在线上下文，到您或 Claude 可以调用的按需功能，再到在特定事件上运行的后台自动化。下表显示了可用的内容以及每一项何时有意义。

|特点|它的作用|何时使用它|示例|
| - | - | - | - |
|**CLAUDE.md**|每个对话都会加载持久上下文|项目惯例，“总是做 X”规则|“使用 pnpm，而不是 npm。在提交之前运行测试。”|
|**[输出方式](<https://code.claude.com/docs/en/output-styles>)**|设置 Claude 整个会话的角色、语气和响应格式的说明|您想要在每个响应中使用的语音、长度或格式，或者 Claude 作为软件工程师以外的人员工作|内置简洁风格，用于较短的响应；首先用图表回答每个问题的自定义样式|
|**技能**|说明、知识和工作流程 Claude 可以使用|可重复使用的内容、参考文档、可重复的任务|`/deploy` 运行您的部署清单； API 端点模式的文档技能|
|**子代理**|返回汇总结果的隔离执行上下文|上下文隔离、并行任务、专门的工作人员|研究任务读取许多文件但仅返回关键发现|
|**[动态工作流程](<https://code.claude.com/docs/en/workflows>)**|脚本 Claude 写入在后台运行许多子代理|一些子代理无法完成的工作，或者您想要交叉检查的发现|审核整个代码库，并使用第二组 agents 验证每个发现|
|**[跨会话消息传递](<https://code.claude.com/docs/en/cross-session-messaging>)**|Claude 将消息从您的一个会话传递到另一个会话|您自己运行的会话需要彼此在任务中发现结果|一个会话警告另一个会话，它所做的更改破坏了另一个会话的基础|
|**[代码智能](<https://code.claude.com/docs/en/tools-reference#lsp-tool-behavior>)**|语言服务器导航和诊断|类型化语言、大型代码库，其中 grep 速度慢或不精确|跳转到符号的定义而不是读取整个文件|
| **MCP** |连接到外部服务|外部数据或操作|查询数据库、发布到 Slack、控制浏览器|
|**挂钩**|脚本、HTTP 请求、MCP 工具调用、提示或由事件触发的子代理|必须在每个匹配事件上运行的自动化|每次文件编辑后运行 ESLint|
|**[神器](<https://code.claude.com/docs/en/artifacts>)**|将会话输出发布为私有交互式网页|您希望以可视方式查看或共享的输出，而不是作为终端文本|随着 Claude 调查而更新的事件时间表|

**[插件](<https://code.claude.com/docs/en/plugins/overview>)**是包装层。插件将技能、挂钩、子代理和 MCP 服务器捆绑到单个可安装单元中。插件技能是命名空间的（如 `/my-plugin:review`），因此多个插件可以共存。当您想要在多个存储库中重复使用相同的设置或通过 **[ 市场 ](<https://code.claude.com/docs/en/plugins/overview>)** 分发给其他人时，请使用插件。

### 随着时间的推移建立您的设置

您无需预先配置所有内容。每个功能都有一个可识别的触发器，大多数团队大致按以下顺序添加它们：

|触发|添加|
| :- | :- |
|Claude 约定或命令错误两次|将其添加到[CLAUDE.md](<https://code.claude.com/docs/en/memory>)|
|您不断要求 Claude 更短，解释更多，或以相同的格式回答|设置 [ 输出样式 ](<https://code.claude.com/docs/en/output-styles>)|
|您不断输入相同的提示来启动任务|将其保存为用户可调用的[技能](<https://code.claude.com/docs/en/skills>)|
|您第三次将相同的剧本或多步骤过程粘贴到聊天中|将其捕获为 [ 技能 ](<https://code.claude.com/docs/en/skills>)|
|您不断从浏览器选项卡复制数据 Claude 看不到|将该系统连接为 [MCP 服务器 ](<https://code.claude.com/docs/en/mcp>)|
|Claude 读取许多文件以查找定义或使用符号的位置|为您的语言安装 [ 代码智能插件 ](<https://code.claude.com/docs/en/plugins/code-intelligence>)|
|附带任务会用您不会再次引用的输出淹没您的对话|通过 [ 子代理 ](<https://code.claude.com/docs/en/sub-agents>) 路由它|
|你希望每次都有事发生而不需询问|写一个[钩子](<https://code.claude.com/docs/en/hooks-guide>)|
|第二个存储库需要相同的设置|将其打包为[插件](<https://code.claude.com/docs/en/plugins/overview>)|

相同的触发器会告诉您何时更新已有的内容。重复的错误或重复出现的审阅评论是 CLAUDE.md 编辑，而不是聊天中的一次性更正。您不断手动调整的工作流程是一项需要再次修改的技能。

### 比较相似的功能

有些功能看起来很相似。有关在它们之间进行选择的更深入的演练，请参阅博客上的 [Steering Claude Code：何时使用 CLAUDE.md、技能、挂钩和子代理 ](<https://claude.com/blog/steering-claude-code-skills-hooks-rules-subagents-and-more>)。以下是如何区分它们的方法。



  

#### 技能与子代理

技能和子代理解决不同的问题：

    * **技能**是可重用的内容，您可以加载到任何上下文中
    * **子代理**是独立的工作人员，与您的主要对话分开运行

    |方面|技能|子代理|
    | - | - | - |
    |**这是什么**|可重复使用的指令、知识或工作流程|有自己的背景的孤立工人|
    |**主要优势**|跨上下文共享内容|上下文隔离。工作是分开进行的，只有汇总回报|
    |**[上下文窗口](<https://code.claude.com/docs/en/context-window>)影响**|添加到您的主窗口|使用具有自己的输入和输出标记的单独窗口|
    |**最适合**|参考资料、可调用的工作流程|读取多个文件的任务、并行工作、专门的工作人员|

**技能可以是参考或操作。**参考技能提供Claude在整个会话中使用的知识（例如您的API风格指南）。操作技能告诉 Claude 执行特定操作（例如运行部署工作流程的 `/deploy`）。

**当您需要上下文隔离或上下文窗口已满时，请使用子代理**。子代理可能会读取数十个文件或运行广泛的搜索，但您的主要对话仅收到摘要。由于子代理工作不会消耗您的主上下文，因此当您不需要中间工作保持可见时，这也很有用。自定义子代理可以有自己的指令并且可以预加载技能。

**他们可以组合。**子代理可以预加载特定技能（`skills:`字段）。技能可以使用 `context: fork` 在隔离上下文中运行。详情请参见[技能](<https://code.claude.com/docs/en/skills>)。
  


  

#### CLAUDE.md vs 技能

两者都存储指令，但加载方式不同且用途不同。

    |方面|CLAUDE.md|技能|
    | - | - | - |
    |**负载**|每次会话都会自动|按需|
    |**可以包含文件**|是的，使用 `@path` 进口|是的，使用 `@path` 进口|
    |**可以触发工作流程**|否|是的，使用 `/<name>`|
    |**最适合**|“总是做X”规则|参考资料、可调用的工作流程|

**将其放入CLAUDE.md**中，如果Claude应该始终知道它：编码约定，构建命令，项目结构，“永远不要做X”规则。

**将其放入技能**中，如果它是Claude有时需要的参考材料（API文档、风格指南）或使用`/<name>`触发的工作流程（部署、审查、发布）。

**经验法则：**将CLAUDE.md保留在200行下。如果它在增长，请将参考内容移至技能或拆分为 [`.claude/rules/`](<https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/>) 文件。
  


  

#### CLAUDE.md 与输出样式

两者都给出了 Claude 常规指示。 CLAUDE.md 携带 Claude 应该知道的内容，输出样式设置 Claude 如何响应。

    |方面|CLAUDE.md|输出方式|
    | - | - | - |
    |**持有**|有关您的项目的事实和规则|角色、语气和响应格式|
    |**切换**|始终加载|一次一个活动； [随时切换风格](<https://code.claude.com/docs/en/output-styles#change-your-output-style>)|
    |**最适合**|构建命令、约定、“绝不做 X”规则|较短的响应、代码旁边的解释、非工程角色|

**将其放入 CLAUDE.md** 中，如果该项目属实，无论您采用哪种风格：编码约定、构建命令、项目结构。

**如果与响应本身有关并且您可能希望再次关闭它，请使用输出样式**：长度、格式、解释量Claude或不同的角色，例如写作助理。 Claude Code包括[内置样式](<https://code.claude.com/docs/en/output-styles#built-in-output-styles>)，您可以编写自己的样式。

**它们结合在一起。** CLAUDE.md 无论您选择哪种样式都会保持加载状态。 Claude 遵循这两个指令，因此两者都不强制执行。对于每次都必须发生的事情，请使用 [ 钩子 ](<https://code.claude.com/docs/en/hooks-guide>)。
  


  

#### CLAUDE.md vs 规则 vs 技能

所有三个存储指令，但加载方式不同：

    |方面|CLAUDE.md| `.claude/rules/` |技能|
    | - | - | - | - |
    |**负载**|每节课|每次会话或打开匹配文件时|按需、调用或相关时|
    |**范围**|整个项目|范围可以限定为文件路径|特定任务|
    |**最适合**|核心约定和构建命令|特定于语言或特定于目录的指南|参考材料、可重复的工作流程|

**使用 CLAUDE.md** 获取每个会话所需的说明：构建命令、测试约定、项目架构。

**使用规则**来保持CLAUDE.md的焦点。具有 [`paths` frontmatter](<https://code.claude.com/docs/en/memory#path-specific-rules>) 的规则仅在 Claude 与匹配文件一起使用时加载，并保存上下文。

**使用技能**来获取内容Claude有时只需要，例如API文档或使用`/<name>`触发的部署清单。
  


  

#### 子代理与动态工作流程

两者都在你的主要对话之外起作用。通过子代理，Claude 轮流决定接下来运行的内容。在工作流程中，脚本决定：

    * **子代理**是Claude生成的工作人员，每个子代理都会向生成它的对话返回一个摘要
    * **[动态工作流程](<https://code.claude.com/docs/en/workflows>)**是脚本Claude写入，在后台运行多个子代理并返回一个结果

**当您需要快速、专注的工作人员时，请使用子代理**：研究问题、验证索赔、审查文件。子代理完成工作并返回摘要，因此您的主要对话保持干净。 Claude 在生成子代理时命名的子代理也可以 [ 互相发送消息 ](<https://code.claude.com/docs/en/sub-agents#what-loads-at-startup>)。

    **使用动态工作流程** 当一份工作 [数量已经超过了几个子代理的规模](<https://code.claude.com/docs/en/workflows#when-to-use-a-workflow>)，或者当您希望在看到结果之前对其进行交叉检查时，例如代码库范围内的审计、大型迁移或从多个角度起草的计划。首先， [在提示中询问工作流程](<https://code.claude.com/docs/en/workflows#ask-for-a-workflow-in-your-prompt>).

**要将结果从一个会话传递到另一个会话**，请要求第一个会话的 Claude 发送它。 Claude 通过 [ 跨会话消息传递 ](<https://code.claude.com/docs/en/cross-session-messaging>) 传递它。 [并行运行agents](<https://code.claude.com/docs/en/agents>)比较了同时运行多个Claude的其他方法，包括您切换并稍后检查的会话。
  


  

#### MCP vs 技能

MCP 将 Claude 连接到外部服务。技能扩展了 Claude 的知识，包括如何有效地使用这些服务。

    |方面| MCP |技能|
    | - | - | - |
    |**这是什么**|连接外部服务的协议|知识、工作流程和参考资料|
    |**提供**|工具和数据访问|知识、工作流程、参考资料|
    |**示例**|Slack 集成、数据库查询、浏览器控制|代码审查清单、部署工作流程、API 风格指南|

这些解决了不同的问题并且可以很好地协同工作：

**MCP** 为外部系统提供 Claude 专用工具，连接和身份验证由服务器处理。

**技能**为Claude提供有关如何有效使用这些工具的知识，以及您可以使用`/<name>`触发的工作流程。技能可能包括您团队的数据库架构和查询模式，或具有团队消息格式规则的 `/post-to-slack` 工作流程。
  


  

#### 钩子与技巧

Claude Code 在生命周期事件中运行钩子；它将技能加载到上下文中以供 Claude 应用。

    |方面|钩子|技能|
    | - | - | - |
    |**运行**|shell 命令、HTTP 请求、MCP 工具调用、LLM 提示符或子代理|指令 Claude 读取并遵循|
    |**由**触发|[生命周期事件](<https://code.claude.com/docs/en/hooks#hook-events>)，例如`PostToolUse`或`SessionStart`|您输入 `/<name>` 或 Claude 与您的任务描述相匹配|
    |**决定论**|总是在其事件发生时触发；触发有保证|Claude解释指令；结果可能会有所不同|
    |**上下文成本**|除非钩子返回输出，否则为零|描述加载每个会话；使用时加载完整内容|
    |**最适合**|编辑后检查、阻止不安全命令、日志记录、通知|需要推理、参考资料、多步骤任务的工作流程|

**当动作必须每次都以相同的方式发生并且不需要Claude思考时，使用钩子**。例如：保存时格式化、拒绝 `rm -rf /`、会话结束时发布 Slack 消息。

**当Claude应该决定如何应用这些步骤时，或者当内容是知识而不是脚本时，使用技能**。例如：`/release`清单、您的API风格指南、调试手册。

**将护栏放在挂钩上。** CLAUDE.md 中的“永远不要编辑`.env`”这样的指令或技能是请求，而不是保证。阻止编辑的 `PreToolUse` 挂钩是强制执行。如果一条规则必须每次都适用，那就让它成为一个钩子而不是一个提示指令。

**钩子输出落在上下文中。**运行linter的`PostToolUse`钩子将结果反馈回文本Claude读取； `/fix-lint`技能告诉Claude如何解决它们。
  


### 了解特征层如何

功能可以在多个级别上定义：用户范围、每个项目、通过插件或通过托管策略。您还可以将 CLAUDE.md 文件嵌套在子目录中或将技能放置在 monorepo 的特定包中。当同一功能存在于多个级别时，它们的分层方式如下：

* **CLAUDE.md 文件** 是附加的：所有级别同时向Claude 的上下文贡献内容。启动时加载工作目录及以上目录中的文件；当您在其中工作时，子目录会加载。当指令冲突时，Claude使用判断来协调它们。请参阅[如何加载CLAUDE.md 文件](<https://code.claude.com/docs/en/memory#how-claude-md-files-load>)。
* **技能和子代理**按名称覆盖：当多个级别存在相同名称时，根据优先级，一个定义获胜（托管 > 用户 > 技能项目；托管 > CLI 标志 > 项目 > 用户 > 子代理插件）。插件技能是[命名空间](<https://code.claude.com/docs/en/plugins/components#skills>)以避免冲突。请参阅[技能发现](<https://code.claude.com/docs/en/skills#resolve-skills-that-share-a-name>)和[子代理范围](<https://code.claude.com/docs/en/sub-agents#choose-the-subagent-scope>)。
* **MCP 服务器** 按名称覆盖：本地 > 项目 > 用户。请参阅[MCP范围](<https://code.claude.com/docs/en/mcp#scope-hierarchy-and-precedence>)。
* **Hooks** 合并：所有已注册的钩子都会为其匹配事件触发，无论来源如何。请参阅[挂钩](<https://code.claude.com/docs/en/hooks>)。

### 组合功能

每个扩展解决不同的问题：CLAUDE.md 处理始终在线的上下文，技能处理按需知识和工作流程，MCP 处理外部连接，子代理处理隔离，挂钩处理自动化。真实的设置会根据您的工作流程将它们组合起来。

例如，您可以使用 CLAUDE.md 进行项目约定、部署工作流程的技能、使用 MCP 连接到数据库，以及在每次编辑后运行 linting 的挂钩。每个功能都可以处理其最擅长的内容。

|图案|它是如何运作的|示例|
| - | - | - |
|**技能+MCP**|MCP提供连接；一项技能教Claude如何用好它|MCP 连接到您的数据库，一项技能记录您的架构和查询模式|
|**技能+副特工**|一项技能会产生子代理以进行并行工作|`/audit` 技能启动在隔离环境中工作的安全性、性能和样式子代理|
|**CLAUDE.md + 技能**|CLAUDE.md 持有永远在线的规则；技能保留按需加载的参考材料|CLAUDE.md 表示“遵循我们的 API 约定”，该技能包含完整的 API 风格指南|
|**挂钩+MCP**|钩子通过MCP触发外部动作|当 Claude 修改关键文件时，后期编辑挂钩会发送 Slack 通知|

## 了解背景成本

您添加的每个功能都会消耗一些 Claude 的上下文。太多会填满你的上下文窗口，但它也会增加噪音，从而降低 Claude 的效果；技能可能无法正确触发，或者Claude可能会失去你的惯例。了解这些权衡有助于您建立有效的设置。有关这些功能如何在运行会话中组合的交互式视图，请参阅 [探索上下文窗口 ](<https://code.claude.com/docs/en/context-window>)。

### 按功能划分的上下文成本

每个功能都有不同的加载策略和上下文成本：

|特点|当它加载时|加载什么|上下文成本|
| - | - | - | - |
|**CLAUDE.md**|会话开始|完整内容|每个请求|
|**输出样式**|会话开始，当您切换样式时再次开始|主动风格的完整说明；默认样式没有任何内容|每个请求|
|**技能**|会话开始+使用时|开头说明，使用时完整内容|低（每个请求的描述）\*|
|**MCP 服务器**|会话开始|工具名称；按需提供完整模式|低直至使用工具|
|**代码智能**|文件编辑后并按需|编辑后诊断；查找时的符号位置|低；减少其他地方的文件读取|
|**子代理**|生成时|具有指定技能的新鲜上下文，或 [fork](<https://code.claude.com/docs/en/sub-agents#fork-the-current-conversation>) 的父对话|与主会话隔离|
|**挂钩**|触发时|没有（在外部运行）|零，除非钩子返回附加上下文|

\*在技能的 frontmatter 中设置 [`disable-model-invocation: true`](<https://code.claude.com/docs/en/skills#control-who-invokes-a-skill>)，以使其描述脱离 Claude 的上下文。对于您没有编写的技能，请在设置中设置 [`skillOverrides`](<https://code.claude.com/docs/en/skills#override-skill-visibility-from-settings>) 以执行相同的操作，而无需编辑其文件。

### 了解功能如何加载

每个功能都会在会话中的不同点加载。下面的选项卡解释了每个选项卡的加载时间以及上下文中的内容。



![上下文加载：CLAUDE.md 在会话开始时加载并保留在每个请求中。 MCP 工具名称在开始时加载，完整架构推迟到使用为止。技能在开始时加载描述，在调用时加载完整内容。子代理获得隔离的上下文。挂钩在外部运行。](<https://mintcdn.com/claude-code/ikqp3_70mqIahteV/images/context-loading.svg?fit=max&auto=format&n=ikqp3_70mqIahteV&q=85&s=aab139e750494a237ae2e0c8f9139b0a>)




![上下文加载：CLAUDE.md 在会话开始时加载并保留在每个请求中。 MCP 工具名称在开始时加载，完整架构推迟到使用为止。技能在开始时加载描述，在调用时加载完整内容。子代理获得隔离的上下文。挂钩在外部运行。](<https://mintcdn.com/claude-code/_xqph1dUOslCOwsj/images/context-loading-dark.svg?fit=max&auto=format&n=_xqph1dUOslCOwsj&q=85&s=b274089ef9612d9c760bca9838557626>)




  

#### CLAUDE.md

**时间：** 会话开始

**加载内容：**所有CLAUDE.md文件的完整内容（托管、用户和项目级别）。

**继承：** Claude 从工作目录读取 CLAUDE.md 文件直至根目录，并在访问这些文件时发现子目录中的嵌套文件。有关详细信息，请参阅[CLAUDE.md 文件如何加载](<https://code.claude.com/docs/en/memory#how-claude-md-files-load>)。

    

**提示：**

保留 CLAUDE.md 下 200 线。将参考材料移至技能，按需加载。为了得到 [修剪签到的建议 CLAUDE.md](<https://code.claude.com/docs/en/memory#my-claude-md-is-too-large>)，运行 `/doctor`.

  


  

#### 技能

技能是Claude工具包中的额外功能。它们可以是参考材料（如 API 风格指南）或使用 `/<name>` 触发的可调用工作流程（如 `/deploy`）。 Claude Code 包括 [ 捆绑技能 ](<https://code.claude.com/docs/en/commands>)，例如 `/code-review`、`/batch` 和 `/debug`，无需设置即可使用。您也可以创建自己的。

**何时：**取决于技能的配置。默认情况下，描述在会话开始时加载，并在使用时加载完整内容。对于仅限用户的技能 (`disable-model-invocation: true`)，在调用它们之前不会加载任何内容。

**加载内容：** 对于模型可调用的技能，Claude 在每个请求中查看名称和描述。当您使用 `/<name>` 或 Claude 调用技能时，会自动加载该技能，完整内容将加载到您的对话中。

**Claude如何选择技能：** Claude将您的任务与技能描述进行匹配，以确定哪些是相关的。如果描述模糊或重叠，Claude 可能会加载错误的技能或错过一项有帮助的技能。要告诉 Claude 使用特定技能，请使用 `/<name>` 调用它。

**上下文成本：**使用前较低。仅限用户使用的技能在调用之前成本为零。

**在子代理中：**技能在子代理中的工作方式不同。子代理的 `skills` 字段中列出的技能不是按需加载，而是在启动时完全预加载到其上下文中。子代理仍然可以通过技能工具发现和调用未列出的项目、用户和插件技能。

    

**提示：**

对于有副作用的技能，请使用`disable-model-invocation: true`。这可以保存上下文并确保它们仅在您命名时运行。

  


  

#### MCP 服务器

**时间：**会话开始。

**加载内容：**来自连接的服务器的工具名称和服务器指令。完整的 JSON 架构将被推迟，直到 Claude 需要特定工具为止。

**上下文成本：** [工具搜索](<https://code.claude.com/docs/en/mcp#scale-with-mcp-tool-search>)默认处于启用状态，因此空闲的MCP工具消耗最少的上下文。

    

**提示：**

运行`/mcp`查看每个服务器的连接状态。运行 `/context all` 以查看每个加载的 MCP 工具使用了多少个令牌。 Claude Code [自动重新连接到远程服务器](<https://code.claude.com/docs/en/mcp#automatic-reconnection>)（如果它们断开），并且您可以断开不经常使用的服务器的连接。

  


  

#### 代码智能

**何时：** 文件编辑后，以及当 Claude 导航代码时按需。

**加载内容：**每次编辑文件后键入错误和警告。 Claude 查找符号时的定义、引用和类型信息。

**上下文成本：**低。符号查找通常会取代广泛的文件读取，因此网络上下文的使用可能会减少。

    

**提示：**

在您为您的语言安装 [ 代码智能插件 ](<https://code.claude.com/docs/en/plugins/code-intelligence>) 之前，LSP 工具处于非活动状态。

  


  

#### 子代理

**何时：** 按需，当您或 Claude 为一项任务生成一个任务时。

**加载内容：**新鲜、独立的上下文包含：

    * agent自己的系统提示符，而不是Claude Code系统提示符
    * agent的`skills:`字段中列出的技能完整内容
    * CLAUDE.md 和 git status，除了内置的 Explore 和 Plan agents [ 省略两者 ](<https://code.claude.com/docs/en/sub-agents#what-loads-at-startup>)，以及其定义设置 [`omitClaudeMd`](<https://code.claude.com/docs/en/sub-agents#supported-frontmatter-fields>) 跳过用户、项目和本地的 agent CLAUDE.md 文件
    * 无论线索 agent 在提示中传递什么上下文

对于 [fork](<https://code.claude.com/docs/en/sub-agents#fork-the-current-conversation>)，Claude Code 会加载到目前为止的父级对话、系统提示符和工具。

**上下文成本：**与主会话隔离。

    

**提示：**

使用子代理来完成不需要完整对话上下文的工作。它们的隔离可以防止您的主要会话变得臃肿。

  


  

#### 挂钩

**当：** 触发时。 Claude Code 在特定的生命周期事件（例如工具执行、会话边界、提示提交、权限请求和压缩）上运行挂钩。有关完整列表，请参阅 [Hooks](<https://code.claude.com/docs/en/hooks>)。

**加载什么：**默认情况下什么都没有。钩子在主对话之外执行。

**上下文成本：** 零，除非挂钩返回作为消息添加到对话中的输出。

    

**提示：**

挂钩非常适合消除不需要影响 Claude 上下文的副作用（linting、日志记录）。

  


## 了解更多

每个功能都有自己的指南，其中包含设置说明、示例和配置选项。



  

#### [CLAUDE.md](<https://code.claude.com/docs/en/memory>)

存储项目上下文、约定和说明
  


  

#### [技能](<https://code.claude.com/docs/en/skills>)

提供 Claude 领域专业知识和可重用的工作流程
  


  

#### [子代理](<https://code.claude.com/docs/en/sub-agents>)

将工作卸载到隔离的上下文中
  


  

#### [动态工作流程](<https://code.claude.com/docs/en/workflows>)

从一个脚本运行多个子代理
  


  

#### [跨会话消息传递](<https://code.claude.com/docs/en/cross-session-messaging>)

让 Claude 向您的其他会话发送消息
  


  

#### [MCP](<https://code.claude.com/docs/en/mcp>)

将 Claude 连接到外部服务
  


  

#### [挂钩](<https://code.claude.com/docs/en/hooks-guide>)

使用钩子自动执行操作
  


  

#### [插件](<https://code.claude.com/docs/en/plugins/overview>)

捆绑和共享功能集
  


  

#### [市场](<https://code.claude.com/docs/en/plugins/create-marketplace>)

托管和分发插件集合
  


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Extend Claude Code

> Understand when to use CLAUDE.md, Skills, subagents, hooks, MCP, and plugins.

Claude Code combines a model that reasons about your code with [built-in tools](<https://code.claude.com/docs/en/how-claude-code-works#tools>) for file operations, search, execution, and web access. The built-in tools cover most coding tasks. This guide covers the extension layer: features you add to customize what Claude knows, connect it to external services, and automate workflows.



**注释：**

  For how the core agentic loop works, see [How Claude Code works](<https://code.claude.com/docs/en/how-claude-code-works>).


**New to Claude Code?** Start with [CLAUDE.md](<https://code.claude.com/docs/en/memory>) for project conventions, then add other extensions [as specific triggers come up](<#build-your-setup-over-time>).

## Overview

Extensions plug into different parts of the agentic loop:

* **[CLAUDE.md](<https://code.claude.com/docs/en/memory>)** adds persistent context Claude sees every session
* **[Output styles](<https://code.claude.com/docs/en/output-styles>)** set Claude's role, tone, and response format for every response in a session
* **[Skills](<https://code.claude.com/docs/en/skills>)** add reusable knowledge and invocable workflows
* **[Code intelligence](<https://code.claude.com/docs/en/tools-reference#lsp-tool-behavior>)** connects Claude to a language server for symbol-level navigation and live type errors
* **[MCP](<https://code.claude.com/docs/en/mcp>)** connects Claude to external services and tools
* **[Subagents](<https://code.claude.com/docs/en/sub-agents>)** run their own loops in isolated context, returning summaries
* **[Dynamic workflows](<https://code.claude.com/docs/en/workflows>)** run many subagents from a script Claude writes, returning one result
* **[Cross-session messaging](<https://code.claude.com/docs/en/cross-session-messaging>)** lets Claude pass a message from one of your sessions to another
* **[Hooks](<https://code.claude.com/docs/en/hooks-guide>)** run your script, HTTP request, MCP tool call, prompt, or subagent when Claude Code reaches a lifecycle event
* **[Plugins](<https://code.claude.com/docs/en/plugins/overview>)** and **[marketplaces](<https://code.claude.com/docs/en/plugins/overview>)** package and distribute these features

[Skills](<https://code.claude.com/docs/en/skills>) are the most flexible extension. A skill is a markdown file containing knowledge, workflows, or instructions. You can invoke skills with a command like `/deploy`, or Claude can load them automatically when relevant. Skills can run in your current conversation or in an isolated context via subagents.

## Match features to your goal

Features range from always-on context that Claude sees every session, to on-demand capabilities you or Claude can invoke, to background automation that runs on specific events. The table below shows what's available and when each one makes sense.

| Feature | What it does | When to use it | Example |
| - | - | - | - |
| **CLAUDE.md** | Persistent context loaded every conversation | Project conventions, "always do X" rules | "Use pnpm, not npm. Run tests before committing." |
| **[Output style](<https://code.claude.com/docs/en/output-styles>)** | Instructions that set Claude's role, tone, and response format for a whole session | A voice, length, or format you want in every response, or Claude working as something other than a software engineer | The built-in Concise style for shorter responses; a custom style that answers every question with a diagram first |
| **Skill** | Instructions, knowledge, and workflows Claude can use | Reusable content, reference docs, repeatable tasks | `/deploy` runs your deployment checklist; API docs skill with endpoint patterns |
| **Subagent** | Isolated execution context that returns summarized results | Context isolation, parallel tasks, specialized workers | Research task that reads many files but returns only key findings |
| **[Dynamic workflow](<https://code.claude.com/docs/en/workflows>)** | Script Claude writes that runs many subagents in the background | Work that outgrows a handful of subagents, or findings you want cross-checked | Audit a whole codebase, with a second set of agents verifying each finding |
| **[Cross-session messaging](<https://code.claude.com/docs/en/cross-session-messaging>)** | Claude delivers a message from one of your sessions to another | Sessions you run yourself that need each other's findings mid-task | One session warns another that a change it made breaks what the other is building on |
| **[Code intelligence](<https://code.claude.com/docs/en/tools-reference#lsp-tool-behavior>)** | Language-server navigation and diagnostics | Typed languages, large codebases where grep is slow or imprecise | Jump to a symbol's definition instead of reading the whole file |
| **MCP** | Connect to external services | External data or actions | Query your database, post to Slack, control a browser |
| **Hook** | Script, HTTP request, MCP tool call, prompt, or subagent triggered by events | Automation that must run on every matching event | Run ESLint after every file edit |
| **[Artifact](<https://code.claude.com/docs/en/artifacts>)** | Publish session output as a private, interactive web page | Output you want to see or share visually rather than as terminal text | An incident timeline that updates as Claude investigates |

**[Plugins](<https://code.claude.com/docs/en/plugins/overview>)** are the packaging layer. A plugin bundles skills, hooks, subagents, and MCP servers into a single installable unit. Plugin skills are namespaced (like `/my-plugin:review`) so multiple plugins can coexist. Use plugins when you want to reuse the same setup across multiple repositories or distribute to others via a **[marketplace](<https://code.claude.com/docs/en/plugins/overview>)**.

### Build your setup over time

You don't need to configure everything up front. Each feature has a recognizable trigger, and most teams add them in roughly this order:

| Trigger | Add |
| :- | :- |
| Claude gets a convention or command wrong twice | Add it to [CLAUDE.md](<https://code.claude.com/docs/en/memory>) |
| You keep asking Claude to be shorter, explain more, or answer in the same format | Set an [output style](<https://code.claude.com/docs/en/output-styles>) |
| You keep typing the same prompt to start a task | Save it as a user-invocable [skill](<https://code.claude.com/docs/en/skills>) |
| You paste the same playbook or multi-step procedure into chat for the third time | Capture it as a [skill](<https://code.claude.com/docs/en/skills>) |
| You keep copying data from a browser tab Claude can't see | Connect that system as an [MCP server](<https://code.claude.com/docs/en/mcp>) |
| Claude reads many files to find where a symbol is defined or used | Install a [code intelligence plugin](<https://code.claude.com/docs/en/plugins/code-intelligence>) for your language |
| A side task floods your conversation with output you won't reference again | Route it through a [subagent](<https://code.claude.com/docs/en/sub-agents>) |
| You want something to happen every time without asking | Write a [hook](<https://code.claude.com/docs/en/hooks-guide>) |
| A second repository needs the same setup | Package it as a [plugin](<https://code.claude.com/docs/en/plugins/overview>) |

The same triggers tell you when to update what you already have. A repeated mistake or a recurring review comment is a CLAUDE.md edit, not a one-off correction in chat. A workflow you keep tweaking by hand is a skill that needs another revision.

### Compare similar features

Some features can seem similar. For a deeper walkthrough of choosing between them, see [Steering Claude Code: when to use CLAUDE.md, skills, hooks, and subagents](<https://claude.com/blog/steering-claude-code-skills-hooks-rules-subagents-and-more>) on the blog. Here's how to tell them apart.



  

#### Skill vs Subagent

    Skills and subagents solve different problems:

    * **Skills** are reusable content you can load into any context
    * **Subagents** are isolated workers that run separately from your main conversation

    | Aspect | Skill | Subagent |
    | - | - | - |
    | **What it is** | Reusable instructions, knowledge, or workflows | Isolated worker with its own context |
    | **Key benefit** | Share content across contexts | Context isolation. Work happens separately, only summary returns |
    | **[Context window](<https://code.claude.com/docs/en/context-window>) impact** | Adds to your main window | Uses a separate window with its own input and output tokens |
    | **Best for** | Reference material, invocable workflows | Tasks that read many files, parallel work, specialized workers |

    **Skills can be reference or action.** Reference skills provide knowledge Claude uses throughout your session (like your API style guide). Action skills tell Claude to do something specific (like `/deploy` that runs your deployment workflow).

    **Use a subagent** when you need context isolation or when your context window is getting full. The subagent might read dozens of files or run extensive searches, but your main conversation only receives a summary. Since subagent work doesn't consume your main context, this is also useful when you don't need the intermediate work to remain visible. Custom subagents can have their own instructions and can preload skills.

    **They can combine.** A subagent can preload specific skills (`skills:` field). A skill can run in isolated context using `context: fork`. See [Skills](<https://code.claude.com/docs/en/skills>) for details.
  


  

#### CLAUDE.md vs Skill

    Both store instructions, but they load differently and serve different purposes.

    | Aspect | CLAUDE.md | Skill |
    | - | - | - |
    | **Loads** | Every session, automatically | On demand |
    | **Can include files** | Yes, with `@path` imports | Yes, with `@path` imports |
    | **Can trigger workflows** | No | Yes, with `/<name>` |
    | **Best for** | "Always do X" rules | Reference material, invocable workflows |

    **Put it in CLAUDE.md** if Claude should always know it: coding conventions, build commands, project structure, "never do X" rules.

    **Put it in a skill** if it's reference material Claude needs sometimes (API docs, style guides) or a workflow you trigger with `/<name>` (deploy, review, release).

    **Rule of thumb:** Keep CLAUDE.md under 200 lines. If it's growing, move reference content to skills or split into [`.claude/rules/`](<https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/>) files.
  


  

#### CLAUDE.md vs Output style

    Both give Claude standing instructions. CLAUDE.md carries what Claude should know, and an output style sets how Claude responds.

    | Aspect | CLAUDE.md | Output style |
    | - | - | - |
    | **Holds** | Facts and rules about your project | A role, tone, and response format |
    | **Switching** | Always loaded | One active at a time; [switch styles](<https://code.claude.com/docs/en/output-styles#change-your-output-style>) whenever you want |
    | **Best for** | Build commands, conventions, "never do X" rules | Shorter responses, explanations alongside code, a non-engineering role |

    **Put it in CLAUDE.md** if it's true of the project whatever style you're in: coding conventions, build commands, project structure.

    **Use an output style** if it's about the response itself and you might want it off again: length, format, how much Claude explains, or a different role such as a writing assistant. Claude Code includes [built-in styles](<https://code.claude.com/docs/en/output-styles#built-in-output-styles>), and you can write your own.

    **They combine.** CLAUDE.md stays loaded whichever style you pick. Claude follows both as instructions, so neither is enforced. For anything that has to happen every time, use a [hook](<https://code.claude.com/docs/en/hooks-guide>).
  


  

#### CLAUDE.md vs Rules vs Skills

    All three store instructions, but they load differently:

    | Aspect | CLAUDE.md | `.claude/rules/` | Skill |
    | - | - | - | - |
    | **Loads** | Every session | Every session, or when matching files are opened | On demand, when invoked or relevant |
    | **Scope** | Whole project | Can be scoped to file paths | Task-specific |
    | **Best for** | Core conventions and build commands | Language-specific or directory-specific guidelines | Reference material, repeatable workflows |

    **Use CLAUDE.md** for instructions every session needs: build commands, test conventions, project architecture.

    **Use rules** to keep CLAUDE.md focused. Rules with [`paths` frontmatter](<https://code.claude.com/docs/en/memory#path-specific-rules>) only load when Claude works with matching files, saving context.

    **Use skills** for content Claude only needs sometimes, like API documentation or a deployment checklist you trigger with `/<name>`.
  


  

#### Subagent vs Dynamic workflow

    Both do work outside your main conversation. With subagents, Claude decides turn by turn what runs next. In a workflow, the script decides:

    * **Subagents** are workers Claude spawns, each returning a summary to the conversation that spawned it
    * **[Dynamic workflows](<https://code.claude.com/docs/en/workflows>)** are scripts Claude writes that run many subagents in the background and return one result

    **Use a subagent** when you need a quick, focused worker: research a question, verify a claim, review a file. The subagent does the work and returns a summary, so your main conversation stays clean. Subagents that Claude named when it spawned them can also [message each other](<https://code.claude.com/docs/en/sub-agents#what-loads-at-startup>).

    **Use a dynamic workflow** when a job [outgrows a handful of subagents](<https://code.claude.com/docs/en/workflows#when-to-use-a-workflow>), or when you want the findings cross-checked before you see them, such as a codebase-wide audit, a large migration, or a plan drafted from several angles. To start one, [ask for a workflow in your prompt](<https://code.claude.com/docs/en/workflows#ask-for-a-workflow-in-your-prompt>).

    **To pass a finding from one of your sessions to another**, ask the first session's Claude to send it. Claude delivers it with [cross-session messaging](<https://code.claude.com/docs/en/cross-session-messaging>). [Run agents in parallel](<https://code.claude.com/docs/en/agents>) compares the other ways to run more than one Claude at once, including sessions you hand off and check back on later.
  


  

#### MCP vs Skill

    MCP connects Claude to external services. Skills extend what Claude knows, including how to use those services effectively.

    | Aspect | MCP | Skill |
    | - | - | - |
    | **What it is** | Protocol for connecting to external services | Knowledge, workflows, and reference material |
    | **Provides** | Tools and data access | Knowledge, workflows, reference material |
    | **Examples** | Slack integration, database queries, browser control | Code review checklist, deploy workflow, API style guide |

    These solve different problems and work well together:

    **MCP** gives Claude purpose-built tools for an external system, with the connection and authentication handled by the server.

    **Skills** give Claude knowledge about how to use those tools effectively, plus workflows you can trigger with `/<name>`. A skill might include your team's database schema and query patterns, or a `/post-to-slack` workflow with your team's message formatting rules.
  


  

#### Hook vs Skill

    Claude Code runs a hook at a lifecycle event; it loads a skill into context for Claude to apply.

    | Aspect | Hook | Skill |
    | - | - | - |
    | **Runs** | A shell command, HTTP request, MCP tool call, LLM prompt, or subagent | Instructions Claude reads and follows |
    | **Triggered by** | [Lifecycle events](<https://code.claude.com/docs/en/hooks#hook-events>) such as `PostToolUse` or `SessionStart` | You typing `/<name>`, or Claude matching the description to your task |
    | **Determinism** | Always fires on its event; the trigger is guaranteed | Claude interprets the instructions; outcome can vary |
    | **Context cost** | Zero unless the hook returns output | Description loads each session; full content loads when used |
    | **Best for** | Linting after edits, blocking unsafe commands, logging, notifications | Workflows that need reasoning, reference material, multi-step tasks |

    **Use a hook** when the action must happen the same way every time and doesn't need Claude to think. For example: format on save, reject `rm -rf /`, post a Slack message when a session ends.

    **Use a skill** when Claude should decide how to apply the steps, or when the content is knowledge rather than a script. For example: a `/release` checklist, your API style guide, a debugging playbook.

    **Put guardrails in hooks.** An instruction like "never edit `.env`" in CLAUDE.md or a skill is a request, not a guarantee. A `PreToolUse` hook that blocks the edit is enforcement. If a rule must hold every time, make it a hook rather than a prompt instruction.

    **Hook output lands in context.** A `PostToolUse` hook that runs your linter feeds results back as text Claude reads; a `/fix-lint` skill tells Claude how to resolve them.
  


### Understand how features layer

Features can be defined at multiple levels: user-wide, per-project, via plugins, or through managed policies. You can also nest CLAUDE.md files in subdirectories or place skills in specific packages of a monorepo. When the same feature exists at multiple levels, here's how they layer:

* **CLAUDE.md files** are additive: all levels contribute content to Claude's context simultaneously. Files from your working directory and above load at launch; subdirectories load as you work in them. When instructions conflict, Claude uses judgment to reconcile them. See [how CLAUDE.md files load](<https://code.claude.com/docs/en/memory#how-claude-md-files-load>).
* **Skills and subagents** override by name: when the same name exists at multiple levels, one definition wins based on priority (managed > user > project for skills; managed > CLI flag > project > user > plugin for subagents). Plugin skills are [namespaced](<https://code.claude.com/docs/en/plugins/components#skills>) to avoid conflicts. See [skill discovery](<https://code.claude.com/docs/en/skills#resolve-skills-that-share-a-name>) and [subagent scope](<https://code.claude.com/docs/en/sub-agents#choose-the-subagent-scope>).
* **MCP servers** override by name: local > project > user. See [MCP scope](<https://code.claude.com/docs/en/mcp#scope-hierarchy-and-precedence>).
* **Hooks** merge: all registered hooks fire for their matching events regardless of source. See [hooks](<https://code.claude.com/docs/en/hooks>).

### Combine features

Each extension solves a different problem: CLAUDE.md handles always-on context, skills handle on-demand knowledge and workflows, MCP handles external connections, subagents handle isolation, and hooks handle automation. Real setups combine them based on your workflow.

For example, you might use CLAUDE.md for project conventions, a skill for your deployment workflow, MCP to connect to your database, and a hook to run linting after every edit. Each feature handles what it's best at.

| Pattern | How it works | Example |
| - | - | - |
| **Skill + MCP** | MCP provides the connection; a skill teaches Claude how to use it well | MCP connects to your database, a skill documents your schema and query patterns |
| **Skill + Subagent** | A skill spawns subagents for parallel work | `/audit` skill kicks off security, performance, and style subagents that work in isolated context |
| **CLAUDE.md + Skills** | CLAUDE.md holds always-on rules; skills hold reference material loaded on demand | CLAUDE.md says "follow our API conventions," a skill contains the full API style guide |
| **Hook + MCP** | A hook triggers external actions through MCP | Post-edit hook sends a Slack notification when Claude modifies critical files |

## Understand context costs

Every feature you add consumes some of Claude's context. Too much can fill up your context window, but it can also add noise that makes Claude less effective; skills may not trigger correctly, or Claude may lose track of your conventions. Understanding these trade-offs helps you build an effective setup. For an interactive view of how these features combine in a running session, see [Explore the context window](<https://code.claude.com/docs/en/context-window>).

### Context cost by feature

Each feature has a different loading strategy and context cost:

| Feature | When it loads | What loads | Context cost |
| - | - | - | - |
| **CLAUDE.md** | Session start | Full content | Every request |
| **Output styles** | Session start, and again when you switch styles | The active style's full instructions; nothing for the Default style | Every request |
| **Skills** | Session start + when used | Descriptions at start, full content when used | Low (descriptions every request)\* |
| **MCP servers** | Session start | Tool names; full schemas on demand | Low until a tool is used |
| **Code intelligence** | After file edits and on demand | Diagnostics after edits; symbol locations on lookup | Low; reduces file reads elsewhere |
| **Subagents** | When spawned | Fresh context with specified skills, or the parent conversation for a [fork](<https://code.claude.com/docs/en/sub-agents#fork-the-current-conversation>) | Isolated from main session |
| **Hooks** | On trigger | Nothing (runs externally) | Zero, unless hook returns additional context |

\*Set [`disable-model-invocation: true`](<https://code.claude.com/docs/en/skills#control-who-invokes-a-skill>) in a skill's frontmatter to keep its description out of Claude's context. For a skill you didn't write, set [`skillOverrides`](<https://code.claude.com/docs/en/skills#override-skill-visibility-from-settings>) in settings to do the same without editing its file.

### Understand how features load

Each feature loads at different points in your session. The tabs below explain when each one loads and what goes into context.



![Context loading: CLAUDE.md loads at session start and stays in every request. MCP tool names load at start with full schemas deferred until use. Skills load descriptions at start, full content on invocation. Subagents get isolated context. Hooks run externally.](<https://mintcdn.com/claude-code/ikqp3_70mqIahteV/images/context-loading.svg?fit=max&auto=format&n=ikqp3_70mqIahteV&q=85&s=aab139e750494a237ae2e0c8f9139b0a>)




![Context loading: CLAUDE.md loads at session start and stays in every request. MCP tool names load at start with full schemas deferred until use. Skills load descriptions at start, full content on invocation. Subagents get isolated context. Hooks run externally.](<https://mintcdn.com/claude-code/_xqph1dUOslCOwsj/images/context-loading-dark.svg?fit=max&auto=format&n=_xqph1dUOslCOwsj&q=85&s=b274089ef9612d9c760bca9838557626>)




  

#### CLAUDE.md

    **When:** Session start

    **What loads:** Full content of all CLAUDE.md files (managed, user, and project levels).

    **Inheritance:** Claude reads CLAUDE.md files from your working directory up to the root, and discovers nested ones in subdirectories as it accesses those files. See [How CLAUDE.md files load](<https://code.claude.com/docs/en/memory#how-claude-md-files-load>) for details.

    

**提示：**

Keep CLAUDE.md under 200 lines. Move reference material to skills, which load on demand. To get [trim proposals for a checked-in CLAUDE.md](<https://code.claude.com/docs/en/memory#my-claude-md-is-too-large>), run `/doctor`.

  


  

#### Skills

    Skills are extra capabilities in Claude's toolkit. They can be reference material (like an API style guide) or invocable workflows you trigger with `/<name>` (like `/deploy`). Claude Code includes [bundled skills](<https://code.claude.com/docs/en/commands>) like `/code-review`, `/batch`, and `/debug` that work without setup. You can also create your own.

    **When:** Depends on the skill's configuration. By default, descriptions load at session start and full content loads when used. For user-only skills (`disable-model-invocation: true`), nothing loads until you invoke them.

    **What loads:** For model-invocable skills, Claude sees names and descriptions in every request. When you invoke a skill with `/<name>` or Claude loads it automatically, the full content loads into your conversation.

    **How Claude chooses skills:** Claude matches your task against skill descriptions to decide which are relevant. If descriptions are vague or overlap, Claude may load the wrong skill or miss one that would help. To tell Claude to use a specific skill, invoke it with `/<name>`.

    **Context cost:** Low until used. User-only skills have zero cost until invoked.

    **In subagents:** Skills work differently in subagents. Instead of on-demand loading, skills listed in the subagent's `skills` field are fully preloaded into its context at launch. Subagents can still discover and invoke unlisted project, user, and plugin skills through the Skill tool.

    

**提示：**

Use `disable-model-invocation: true` for skills with side effects. This saves context and ensures they run only when you name them.

  


  

#### MCP servers

    **When:** Session start.

    **What loads:** Tool names and server instructions from connected servers. Full JSON schemas stay deferred until Claude needs a specific tool.

    **Context cost:** [Tool search](<https://code.claude.com/docs/en/mcp#scale-with-mcp-tool-search>) is on by default, so idle MCP tools consume minimal context.

    

**提示：**

Run `/mcp` to see each server's connection status. Run `/context all` to see how many tokens each loaded MCP tool uses. Claude Code [reconnects to remote servers automatically](<https://code.claude.com/docs/en/mcp#automatic-reconnection>) if they drop, and you can disconnect servers you're not actively using.

  


  

#### Code intelligence

    **When:** After file edits, and on demand when Claude navigates code.

    **What loads:** Type errors and warnings after each file edit. Definition, reference, and type information when Claude looks up a symbol.

    **Context cost:** Low. Symbol lookups often replace broad file reads, so net context use can go down.

    

**提示：**

The LSP tool is inactive until you install a [code intelligence plugin](<https://code.claude.com/docs/en/plugins/code-intelligence>) for your language.

  


  

#### Subagents

    **When:** On demand, when you or Claude spawns one for a task.

    **What loads:** Fresh, isolated context containing:

    * The agent's own system prompt, not the Claude Code system prompt
    * Full content of skills listed in the agent's `skills:` field
    * CLAUDE.md and git status, except the built-in Explore and Plan agents [omit both](<https://code.claude.com/docs/en/sub-agents#what-loads-at-startup>), and an agent whose definition sets [`omitClaudeMd`](<https://code.claude.com/docs/en/sub-agents#supported-frontmatter-fields>) skips the user, project, and local CLAUDE.md files
    * Whatever context the lead agent passes in the prompt

    For a [fork](<https://code.claude.com/docs/en/sub-agents#fork-the-current-conversation>), Claude Code loads the parent's conversation so far, system prompt, and tools instead.

    **Context cost:** Isolated from main session.

    

**提示：**

Use subagents for work that doesn't need your full conversation context. Their isolation prevents bloating your main session.

  


  

#### Hooks

    **When:** On trigger. Claude Code runs hooks at specific lifecycle events like tool execution, session boundaries, prompt submission, permission requests, and compaction. See [Hooks](<https://code.claude.com/docs/en/hooks>) for the full list.

    **What loads:** Nothing by default. Hooks execute outside the main conversation.

    **Context cost:** Zero, unless the hook returns output that gets added as messages to your conversation.

    

**提示：**

Hooks are ideal for side effects (linting, logging) that don't need to affect Claude's context.

  


## Learn more

Each feature has its own guide with setup instructions, examples, and configuration options.



  

#### [CLAUDE.md](<https://code.claude.com/docs/en/memory>)

    Store project context, conventions, and instructions
  


  

#### [Skills](<https://code.claude.com/docs/en/skills>)

    Give Claude domain expertise and reusable workflows
  


  

#### [Subagents](<https://code.claude.com/docs/en/sub-agents>)

    Offload work to isolated context
  


  

#### [Dynamic workflows](<https://code.claude.com/docs/en/workflows>)

    Run many subagents from one script
  


  

#### [Cross-session messaging](<https://code.claude.com/docs/en/cross-session-messaging>)

    Let Claude message your other sessions
  


  

#### [MCP](<https://code.claude.com/docs/en/mcp>)

    Connect Claude to external services
  


  

#### [Hooks](<https://code.claude.com/docs/en/hooks-guide>)

    Automate actions with hooks
  


  

#### [Plugins](<https://code.claude.com/docs/en/plugins/overview>)

    Bundle and share feature sets
  


  

#### [Marketplaces](<https://code.claude.com/docs/en/plugins/create-marketplace>)

    Host and distribute plugin collections
  


</details>
