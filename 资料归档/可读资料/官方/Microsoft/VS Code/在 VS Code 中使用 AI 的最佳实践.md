# 在 VS Code 中使用 AI 的最佳实践

介绍选择合适工作流、写聚焦的提示、提供上下文、选择模型、先规划和再验证结果的方法。

- 来源：Microsoft／VS Code
- 资料类型：存档文档
- 文档时间：官方审核：[2026-10-07](<https://code.visualstudio.com/docs/agents/best-practices>)；官方文件提交（截至抓取）：[2026-10-08](<https://github.com/microsoft/vscode-docs/commit/a88484276b0f7e053f9145da14de6a98f32b61c5>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.visualstudio.com/docs/agents/best-practices>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/在 VS Code 中使用 AI 的最佳实践.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 在 VS Code 中使用 AI 的最佳实践

* [以 Markdown 形式查看](<https://code.visualstudio.com/docs/agents/best-practices.md>)
* [在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fbest-practices%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问
* [在VS Code内部人士中提问](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fbest-practices%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fbest-practices%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问

---

本文介绍了在 Visual Studio Code 中使用 AI 的良好实践。从有界任务开始，提供相关上下文并验证结果。稍后当您有重复需求时配置自定义指令、agents 或工具。

## 为任务选择正确的工具

VS Code中的AI提供了多种交互模式。为手头的任务选择合适的工具可以节省时间并产生更好的结果。

|工具|最适合|示例|
| --- | --- | --- |
|[内嵌建议](<https://code.visualstudio.com/docs/editing/ai-powered-suggestions>)|编写代码时保持流畅|内联建议、变量名称、样板文件|
|[内嵌聊天](<https://code.visualstudio.com/docs/chat/inline-chat>)|有针对性的就地编辑，无需切换上下文|重构函数，添加错误处理|
| [Agents](<https://code.visualstudio.com/docs/agents/overview>) |需要自主规划和使用工具的多文件更改|端到端实现功能|
|[计划](<https://code.visualstudio.com/docs/agents/run/planning>)|实施前的结构化规划|设计架构或迁移策略|
|[智能动作](<https://code.visualstudio.com/docs/editing/copilot-smart-actions>)|内置的、专门的一步任务|生成提交消息、修复错误、重命名符号|

## 将工作流程应用到您的项目

当您准备好在自己的项目中使用 agent 时，请从您知道的项目中的一项有界任务开始：

1. 打开受信任的项目。提交或隐藏不相关的工作，以便您可以区分 agent 的编辑和您自己的更改。
2. 选择一个小任务并定义一个可观察的结果。如果方法不清楚，请在聊天中提问或在实施之前使用[计划角色](<https://code.visualstudio.com/docs/agents/run/planning>)。
3. 提供相关文件、错误、约束和现有测试命令。例如：

   ```
   Add empty-state text to the results view. Limit changes to the existing
   view and its tests. Follow the current component patterns, run the
   existing tests for this area, and summarize the behavior you verified.
   ```
4. 使用 [聊天视图 ](<https://code.visualstudio.com/docs/agents/run/chat-view>)、**Copilot** 会话目标、**Agent** 角色和 **手动权限 ** 来执行交互式第一个任务。
5. 审查每个批准请求。当agent完成后，检查每个更改的文件并自行运行相关测试。
6. 仅提交预期的更改。如果结果错误，请提供重点反馈或[恢复检查点](<https://code.visualstudio.com/docs/agents/run/review-code-edits#_restore-a-checkpoint>)。

检查点会恢复受影响的工作区文件和聊天历史记录，但不会反转已完成的终端命令、部署或对外部服务的更改。使用 Git 和外部服务的恢复控制来实现这些效果。

有关现有项目中的分步指南，[探索代码库](<https://code.visualstudio.com/docs/agents/guides/explore-a-codebase>)、[添加功能](<https://code.visualstudio.com/docs/agents/guides/add-a-feature>)或[重构而不更改行为](<https://code.visualstudio.com/docs/agents/guides/refactor-safely>)。

## 写出有效的提示

AI 回复的质量取决于提示的清晰度和具体性。这些技术可以帮助您获得更好的结果。

* **具体说明输入、输出和约束。** 说明您要使用的编程语言、框架和库。描述预期行为或包括示例输入和输出。

  ```
  Write a TypeScript function that validates email addresses.
  Return true for valid addresses, false otherwise. Don't use regex.
  Example: validateEmail("user@example.com") returns true
  Example: validateEmail("invalid") returns false
  ```
* **分解复杂的任务。**不要立即要求整个功能，而是将其分解为更小的、范围明确的步骤。这种方法可以产生更可靠的结果，并且更容易及早发现问题。
* **包括用于验证的预期输出。**提供测试用例、预期结果或验收标准，以便AI可以验证其自己的工作。这一步是您可以做的最有效的事情之一。

  ```
  Implement a rate limiter using the token bucket algorithm.
  Write unit tests that verify: 10 requests/second allowed,
  11th request rejected, bucket refills after 1 second.
  Run the tests after implementing.
  ```
* **避免模糊的提示。**像“让这变得更好”这样的提示给AI没有方向。相反，请指定“更好”的含义：“降低时间复杂度”或“添加空值的输入验证”。
* **迭代后续提示。**通过在后续消息中添加约束或更正来优化响应，而不是重写整个提示。
* **尽早修正航向。**如果AI朝错误方向行驶，[用后续消息引导它](<https://code.visualstudio.com/docs/chat/chat-overview#_send-messages-while-a-request-is-running>)以重定向当前请求，对后续请求进行排队，或停止并发送新提示。

如需帮助决定是否引导、恢复更改或重新开始，请参阅 [让 agent 回到正轨 ](<https://code.visualstudio.com/docs/agents/guides/get-agent-back-on-track>)。
* **告诉AI提出澄清问题。**如果任务不明确，请指示AI在继续之前向您提问。与猜测需求相比，这会带来更准确的结果。
* **并行任务。**如果您有多个独立任务，请要求AI并行运行它们以节省时间。例如，“并行对 X 和 Y 进行单独研究并总结结果。”

有关更多信息，请在 GitHub Copilot 文档中查找实用的 [ 提示示例 ](<https://docs.github.com/en/copilot/copilot-chat-cookbook>)。

## 提供正确的背景

当 AI 具有相关上下文时，它的响应会更准确。使用这些技术将 AI 指向正确的信息：

* AI 自动执行代码搜索以收集相关上下文。当您的提示不明确时，您可以通过使用 `#<file>`、`#<folder>` 或 `#<symbol>` 引用提示中的特定文件、文件夹或符号来引导 AI。
* 要从网页或 GitHub 存储库中提取信息，请使用 `#fetch` 向 AI 提供代码库之外的最新信息，或使用 MCP 服务器中的工具，例如 GitHub MCP。
* 参考 VS Code 环境上下文，例如源代码控制更改、终端输出或测试失败，以帮助 AI 了解项目的当前状态并提供更相关的响应。
* 添加图像或屏幕截图，让 AI 分析视觉内容。
* 使用 [ 集成浏览器 ](<https://code.visualstudio.com/docs/debugtest/integrated-browser>) 预览您的应用程序并手动添加页面元素、屏幕截图或控制台日志作为上下文。
* 提供agents [浏览器工具](<https://code.visualstudio.com/docs/agents/run/browser-tools>)来练习用户流程、检查结果、修复问题并自主验证其更改。

有关更多信息，请参阅[向聊天提示添加上下文](<https://code.visualstudio.com/docs/chat/copilot-chat-context>)和[配置工具](<https://code.visualstudio.com/docs/agents/run/tools>)。

## 选择合适的型号

每个 AI 型号都有不同的优势。有些人擅长推理，另一些人擅长代码生成或更快的响应。为您的任务选择正确的模型可以提高结果。

* **将模型与任务复杂性相匹配。**使用快速模型进行简单的完成和样板。切换到推理优化模型进行规划、调试或架构决策。
* **使用最新型号。** 较新的型号通常具有改进的功能。 VS Code不断增加对新模型和模型版本的支持。检查[可用型号](<https://code.visualstudio.com/docs/agent-customization/language-models>)并使用最新型号。
* **在提示文件和 agents 中固定模型。** 在提示文件或自定义 agent 定义中指定首选模型，以确保针对特定任务一致使用正确的模型。
* **实验并比较。**如果您对响应不满意，请尝试其他模型。对于相同的提示，不同的模型可能会产生明显不同的结果。
* **调整推理模型的思维努力。**使用模型选择器中的[思维努力控制](<https://code.visualstudio.com/docs/agent-customization/language-models#_configure-thinking-effort>)来增加复杂任务的努力或减少简单任务的努力。
* **使用BYOK进行额外控制。**带上您自己的API密钥以获得更多型号选择和托管选项。
* **考虑信用消耗。**能力更强的模型每个代币消耗更多的[AI信用](<https://code.visualstudio.com/docs/agents/concepts/language-models#_ai-credits-and-model-costs>)。自动选型自动平衡质量和成本。有关更多提示，请参阅[优化AI积分使用](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>)。

有关更多信息，请参阅[选择AI模型](<https://code.visualstudio.com/docs/agent-customization/language-models>)和[Copilot聊天](<https://docs.github.com/en/copilot/using-github-copilot/ai-models/changing-the-ai-model-for-copilot-chat>)的可用模型。

## 先计划，后实施

对于跨越多个文件的复杂更改，请将规划与实施分开。这种方法可以防止 AI 解决错误的问题，并避免在需要丢弃的代码上花费 [AI 积分 ](<https://code.visualstudio.com/docs/agents/concepts/language-models#_ai-credits-and-model-costs>)。

1. **探索。**使用聊天来提问或使用子代理来阅读相关代码并在进行更改之前了解其工作原理。
2. **计划。** 使用[计划agent](<https://code.visualstudio.com/docs/agents/run/planning>)创建结构化实施计划。在执行之前审查并完善计划。
3. **实施。**切换到agent模式并从计划实施。包括测试或预期输出，以便 agent 可以验证其自身的工作。并行运行独立的 Copilot、Claude 或 Codex 会话，或移交给 [ 云 agent](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_start-a-cloud-session>) 进行远程执行。
4. **查看。**使用[检查点](<https://code.visualstudio.com/docs/agents/run/review-code-edits#_edit-requests-and-restore-checkpoints>)查看进度，如果agent偏离轨道则回退，或者[请求Copilot代码查看 ](<https://docs.github.com/en/copilot/concepts/agents/code-review>) 生成的拉取请求。

有关更多信息，请参阅 [ 上下文工程工作流程 ](<https://code.visualstudio.com/docs/agents/guides/context-engineering-guide>)。

## 检查并验证 AI 输出

AI-生成的代码可能包含错误、安全问题或微妙的逻辑错误。始终将 AI 输出视为需要审查的起点。

* **集成之前进行审查。**在提交或合并更改之前通读生成的代码。注意边缘情况、错误处理和 AI 可能做出的假设。
* **在 AI 更改后运行测试。** 在提示中包含测试用例，以便 AI 可以验证其自身的工作。如果 AI 没有自动运行测试，请在继续之前自行运行它们。
* **使用检查点回退文件编辑和聊天。** 如果 agent 偏离轨道，请使用 [ 检查点 ](<https://code.visualstudio.com/docs/agents/run/review-code-edits#_edit-requests-and-restore-checkpoints>) 恢复受影响的工作区文件和聊天历史记录。检查点不会撤消已完成的命令或对外部服务的更改。
* **检查安全问题。**检查AI-生成的代码是否存在常见漏洞，例如注入缺陷、硬编码机密或缺少输入验证。避免将凭据或敏感数据粘贴到提示中。

有关更多信息，请参阅 [GitHub Copilot 安全性 ](<https://code.visualstudio.com/docs/agents/run/security>) 和 [GitHub Copilot 信任中心 ](<https://copilot.github.trust.page/faq>)。

## 选择合适的 agent 安全带

使用 agents 时，请选择与您的任务和工作流程相匹配的线束。每个工具都提供不同的提供商功能、工具和执行环境。

* **使用 Copilot 进行日常编码。** Copilot 在您的计算机上运行工具，并可以访问您的工作区和运行时上下文。对于大多数编码任务来说，这是一个很好的默认值。工具执行位置与所选语言模型的运行位置分开。
* **使用 Claude 或 Codex 来获取特定于提供商的功能。** 这些工具还在您的计算机上运行工具，并通过相同的 VS Code 会话体验提供自己的 SDK 功能。
* **使用云目标进行团队协作。** [云会话](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_start-a-cloud-session>)远程运行并创建拉取请求，使它们非常适合从团队审查中受益的任务，或者当您想要将GitHub问题直接分配给agent时。
* **为独立任务运行并行会话。**启动多个 Copilot、Claude、Codex 或云会话以同时处理不相关的任务。从 [ 会话列表 ](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_sessions-list>) 监视它们。
* **当另一个目标更适合下一步时切换。**将会话目标更改为[切换](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_hand-off-a-session>)，同时保留对话历史记录和上下文。

有关更多信息，请参阅[选择agent线束](<https://code.visualstudio.com/docs/agents/run/agent-harnesses>)和[代理编码教程](<https://code.visualstudio.com/docs/agents/agents-tutorial>)。

## 针对 AI 优化您的项目

项目定制是可选的。当您的约定、项目上下文或工作流程 AI 无法从您的代码可靠地推断出时，请添加它。简洁的定制还可以通过减少重复的上下文收集、更正和重试来帮助[优化AI信用使用](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>)。

VS Code 支持多种机制来为您的项目配置 AI 行为。在聊天中输入`/init`以生成入门配置。

|机制|最适合|开始使用|
| --- | --- | --- |
|[定制说明](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>)|项目范围的编码标准和架构上下文|输入 `/init` 为您的项目生成始终在线的指令|
|[定制 agents](<https://code.visualstudio.com/docs/agent-customization/custom-agents>)|专门的工作流程或角色（TDD，安全审计）|输入 `/create-agent <description>` 生成自定义 agent|
|[技能](<https://code.visualstudio.com/docs/agent-customization/agent-skills>)|特定领域的能力（测试、部署）|输入`/create-skill <description>`生成技能|
|[工具和MCP服务器](<https://code.visualstudio.com/docs/agents/run/tools>)|连接到外部系统（数据库、API、CLI）|在`mcp.json`中配置|

有效项目配置的提示：

* **保持说明文件简洁。**它们会在每次聊天交互中加载。重点关注 AI 无法从代码中推断出的信息，例如非默认约定、架构决策或环境设置。
* **具有 `applyTo` 模式的范围指令。** 输入 `/instructions` 以创建特定于语言或特定于文件夹的指令文件，而不是将所有内容都放在一个文件中。
* **限制启用的工具。**活动工具越少意味着响应更快、更相关。仅在任务需要时才启用工具。
* **从搜索中排除生成的嘈杂文件。** 配置


搜索.排除

在 VS Code
 中打开 在 VS Code 内部人员 
 中打开并且


文件.排除

在 VS Code
 中打开 在 VS Code Insiders
 中打开，因此 agent 文本搜索和 grep 始终专注于源代码。请参阅 [ 使用排除设置改进 agent 搜索 ](<https://code.visualstudio.com/docs/agents/reference/workspace-context#_improve-agent-search-with-exclusion-settings>)。

有关完整设置的详细信息，请参阅 [在 VS Code](<https://code.visualstudio.com/docs/agent-customization/overview>) 中自定义 agent 行为。

## 管理上下文和会话

当对话充满不相关的上下文时，AI 响应可能会降低。主动管理您的会话。

* **针对不相关的任务启动新会话。**不要在一次对话中不断堆积不相关的问题。上下文污染会降低响应质量并在不相关的历史记录上浪费代币。
* **删除不相关的历史记录。**删除不再相关的过去的问题和回复，或开始新的会话。
* **压缩上下文。** 使用 [/compact](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_compact-conversation-context>) 并提供说明来有选择地压缩上下文并仅保留最相关的信息。压缩减少了每个后续请求发送的令牌，这有助于[管理AI信用使用](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>)。
* **验证缓存性能。**保持提示的早期部分在轮流中保持稳定，以重用提示缓存并降低成本和延迟。使用 [Cache Explorer](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/cache-explorer>) 检查缓存命中率并查找缓存损坏的位置。
* **使用子代理进行调查。** 提示 AI 使用 [ 子代理 ](<https://code.visualstudio.com/docs/agents/run/subagents>) 单独执行研究和探索，以便结果不会扰乱您的主要上下文。
* **选择正确的会话目标和执行环境。**从[Copilot工具](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_use-the-copilot-harness>)开始在文件夹或隔离工作树中进行日常工作。选择“本地”作为当前工作区中的扩展主机工作流程，或选择“云”以在返回拉取请求的提供商管理的基础设施上进行独立工作。
* **通过并行会话进行扩展。**在单独的文件夹或工作树中运行多个会话，以节省时间并保持上下文独立。您可以从 VS Code 中的 [ 会话列表 ](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_sessions-list>) 在您的计算机、连接的主机、开发容器中或提供商管理的云基础设施上运行的会话之间进行切换。
* **分叉而不是重新提示。**使用[`/fork`](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_fork-a-chat-session>)在不丢失上下文的情况下探索替代方案，而不是从头开始并重新建立上下文。

有关详细信息，请参阅[会话管理](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions>)、[工作区索引](<https://code.visualstudio.com/docs/agents/reference/workspace-context>)和[优化AI积分使用情况](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>)。

## 优化AI积分使用

本文中的许多做法也可以帮助您控制成本。更强大的模型、大型上下文和一次性工作都会消耗 [AI 积分 ](<https://code.visualstudio.com/docs/agents/concepts/language-models#_ai-credits-and-model-costs>)。有几个杠杆影响最大：

* **将模型与任务相匹配。**使用更快、更便宜的模型来完成简单的工作，并保留高级模型来进行规划、调试和架构决策。自动选型为您平衡质量和成本。
* **保持上下文精简。**仅发送相关上下文，[紧凑](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_compact-conversation-context>)长对话，并为不相关的任务启动新的会话，以避免为每个请求的不相关历史记录付费。
* **在实施之前进行计划。**将计划与实施分开可以避免在您丢弃的代码上花费积分。

有关完整的技术集，请参阅[优化AI积分使用](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>)。

## 使用大型代码库

Copilot 旨在有效地处理大型、复杂和多根工作空间。使用这些实践来大规模获得最佳结果。

* **使用工作区索引。** VS Code 使用语义搜索、语言智能和 GitHub 的代码搜索自动索引您的项目以进行深入的跨文件推理。这适用于小型项目和大型企业代码库。对于大型存储库，请使用 [ 远程索引 ](<https://code.visualstudio.com/docs/agents/reference/workspace-context#_semantic-index-sources>) 在您的存储库和 GitHub 上的相关存储库中获得快速、全面的结果。
* **多根工作区的范围工作。** 对于单一存储库或具有多个服务的项目，请使用 [ 多根工作区 ](<https://code.visualstudio.com/docs/editing/workspaces/multi-root-workspaces>) 为 AI 提供清晰的边界和集中的上下文。
* **提供项目级指令。**使用[自定义指令](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>)来描述项目的架构、模块边界和约定，这些是AI无法单独从代码推断出来的。这为 AI 提供了架构级别更改所需的上下文。
* **运行并行会话以进行独立更改。**将大型任务分解为独立的子任务，并在[并行会话](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_sessions-list>)中运行它们，每个子任务都专注于代码库的不同区域。
* **使用计划 agent 进行横切更改。** 对于跨越多个文件或模块的更改，请从 [ 计划 agent](<https://code.visualstudio.com/docs/agents/run/planning>) 开始，在执行之前创建结构化实施计划。

有关详细信息，请参阅 [ 工作区上下文 ](<https://code.visualstudio.com/docs/agents/reference/workspace-context>) 和 [agents](<https://code.visualstudio.com/docs/agents/overview>)。

## 相关资源

* [上下文工程指南](<https://code.visualstudio.com/docs/agents/guides/context-engineering-guide>)
* [优化AI积分使用](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>)
* [自定义 VS Code](<https://code.visualstudio.com/docs/agent-customization/overview>) 中的 agent 行为
* [备忘单](<https://code.visualstudio.com/docs/agents/reference/ai-features-cheat-sheet>)
* [GitHub Copilot 安全](<https://code.visualstudio.com/docs/agents/run/security>)
* [使用GitHub Copilot文档中的GitHub Copilot](<https://docs.github.com/en/copilot/using-github-copilot/best-practices-for-using-github-copilot>)的最佳实践

10/7/2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Best practices for using AI in VS Code

* [View as Markdown](<https://code.visualstudio.com/docs/agents/best-practices.md>)
* [Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fbest-practices%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)
* [Ask in VS Code Insiders](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fbest-practices%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fbest-practices%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

---

This article covers good practices for using AI in Visual Studio Code. Start with a bounded task, provide relevant context, and verify the result. Configure custom instructions, agents, or tools later when you have a recurring need.

## Pick the right tool for the task

AI in VS Code offers several interaction modes. Choosing the right one for the task at hand saves time and produces better results.

| Tool | Best for | Example |
| --- | --- | --- |
| [Inline suggestions](<https://code.visualstudio.com/docs/editing/ai-powered-suggestions>) | Staying in the flow while writing code | Inline suggestions, variable names, boilerplate |
| [Inline chat](<https://code.visualstudio.com/docs/chat/inline-chat>) | Targeted, in-place edits without switching context | Refactoring a function, adding error handling |
| [Agents](<https://code.visualstudio.com/docs/agents/overview>) | Multi-file changes that require autonomous planning and tool use | Implementing a feature end-to-end |
| [Plan](<https://code.visualstudio.com/docs/agents/run/planning>) | Structured planning before implementation | Designing an architecture or migration strategy |
| [Smart actions](<https://code.visualstudio.com/docs/editing/copilot-smart-actions>) | Built-in, specialized one-step tasks | Generating commit messages, fixing errors, renaming symbols |

## Apply the workflow to your project

When you're ready to use an agent in your own project, start with one bounded task in a project you know:

1. Open a trusted project. Commit or stash unrelated work so that you can distinguish the agent's edits from your own changes.
2. Choose a small task and define an observable result. If the approach is unclear, ask questions in chat or use the [Plan role](<https://code.visualstudio.com/docs/agents/run/planning>) before implementation.
3. Provide the relevant files, errors, constraints, and existing test commands. For example:

   ```
   Add empty-state text to the results view. Limit changes to the existing
   view and its tests. Follow the current component patterns, run the
   existing tests for this area, and summarize the behavior you verified.
   ```
4. Use the [Chat view](<https://code.visualstudio.com/docs/agents/run/chat-view>), the **Copilot** session target, the **Agent** role, and **Manual permissions** for an interactive first task.
5. Review each approval request. When the agent finishes, inspect every changed file and run the relevant tests yourself.
6. Commit only the intended changes. If the result is wrong, provide focused feedback or [restore a checkpoint](<https://code.visualstudio.com/docs/agents/run/review-code-edits#_restore-a-checkpoint>).

Checkpoints restore affected workspace files and chat history, but they don't reverse completed terminal commands, deployments, or changes to external services. Use Git and the external service's recovery controls for those effects.

For step-by-step guides in an existing project, [explore a codebase](<https://code.visualstudio.com/docs/agents/guides/explore-a-codebase>), [add a feature](<https://code.visualstudio.com/docs/agents/guides/add-a-feature>), or [refactor without changing behavior](<https://code.visualstudio.com/docs/agents/guides/refactor-safely>).

## Write effective prompts

The quality of AI responses depends on the clarity and specificity of your prompt. These techniques help you get better results.

* **Be specific about inputs, outputs, and constraints.** State the programming language, frameworks, and libraries you want to use. Describe expected behavior or include example input and output.

  ```
  Write a TypeScript function that validates email addresses.
  Return true for valid addresses, false otherwise. Don't use regex.
  Example: validateEmail("user@example.com") returns true
  Example: validateEmail("invalid") returns false
  ```
* **Break down complex tasks.** Instead of asking for an entire feature at once, decompose it into smaller, well-scoped steps. This approach produces more reliable results and makes it easier to catch problems early.
* **Include expected output for verification.** Provide test cases, expected results, or acceptance criteria so the AI can verify its own work. This step is one of the highest-leverage things you can do.

  ```
  Implement a rate limiter using the token bucket algorithm.
  Write unit tests that verify: 10 requests/second allowed,
  11th request rejected, bucket refills after 1 second.
  Run the tests after implementing.
  ```
* **Avoid vague prompts.** A prompt like "make this better" gives the AI no direction. Instead, specify what "better" means: "reduce the time complexity" or "add input validation for null values."
* **Iterate with follow-up prompts.** Refine responses by adding constraints or corrections in follow-up messages rather than rewriting the entire prompt.
* **Course-correct early.** If the AI is heading in the wrong direction, [steer it](<https://code.visualstudio.com/docs/chat/chat-overview#_send-messages-while-a-request-is-running>) with a follow-up message to redirect the current request, queue a follow-up request, or stop and send a new prompt.

  For help deciding whether to steer, restore changes, or start fresh, see [Get an agent back on track](<https://code.visualstudio.com/docs/agents/guides/get-agent-back-on-track>).
* **Tell the AI to ask clarifying questions.** If a task is ambiguous, instruct the AI to ask you questions before proceeding. This leads to more accurate results than guessing at requirements.
* **Parallel tasks.** If you have multiple independent tasks, ask the AI to run them in parallel to save time. For example, "Perform isolated research about X and Y in parallel and summarize the findings."

For more information, find practical [prompt examples](<https://docs.github.com/en/copilot/copilot-chat-cookbook>) in the GitHub Copilot documentation.

## Provide the right context

The AI responds more accurately when it has relevant context. Use these techniques to point the AI at the right information:

* The AI automatically performs code search to gather relevant context. When your prompt is ambiguous, you can guide the AI by referencing specific files, folders, or symbols in your prompt with `#<file>`, `#<folder>`, or `#<symbol>`.
* To pull information from web pages or GitHub repositories, use `#fetch` to provide the AI with up-to-date information beyond your codebase or use tools from MCP servers like GitHub MCP.
* Reference VS Code environment context such as source control changes, terminal output, or test failures to help the AI understand the current state of your project and provide more relevant responses.
* Add images or screenshots to let the AI analyze visual content.
* Use the [integrated browser](<https://code.visualstudio.com/docs/debugtest/integrated-browser>) to preview your app and manually add page elements, screenshots, or console logs as context.
* Give agents [browser tools](<https://code.visualstudio.com/docs/agents/run/browser-tools>) to exercise user flows, inspect the result, fix problems, and verify their changes autonomously.

For more information, see [adding context to chat prompts](<https://code.visualstudio.com/docs/chat/copilot-chat-context>) and [configuring tools](<https://code.visualstudio.com/docs/agents/run/tools>).

## Choose the right model

Each AI model has different strengths. Some are better at reasoning, others excel at code generation or faster responses. Choosing the right model for your task improves results.

* **Match model to task complexity.** Use fast models for simple completions and boilerplate. Switch to reasoning-optimized models for planning, debugging, or architectural decisions.
* **Use latest models.** Newer models often have improved capabilities. VS Code continuously adds support for new models and model versions. Check the [available models](<https://code.visualstudio.com/docs/agent-customization/language-models>) and use the latest models.
* **Pin models in prompt files and agents.** Specify preferred models in your prompt file or custom agent definitions to ensure the right model is used consistently for specific tasks.
* **Experiment and compare.** If you're not satisfied with a response, try a different model. Different models can produce significantly different results for the same prompt.
* **Adjust thinking effort for reasoning models.** Use the [thinking effort control](<https://code.visualstudio.com/docs/agent-customization/language-models#_configure-thinking-effort>) in the model picker to increase effort for complex tasks or reduce it for simpler ones.
* **Use BYOK for additional control.** Bring your own API key for more model choices and hosting options.
* **Consider credit consumption.** More capable models consume more [AI credits](<https://code.visualstudio.com/docs/agents/concepts/language-models#_ai-credits-and-model-costs>) per token. Auto model selection balances quality and cost automatically. For more tips, see [optimize AI credit usage](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>).

For more information, see [selecting AI models](<https://code.visualstudio.com/docs/agent-customization/language-models>) and [available models for Copilot Chat](<https://docs.github.com/en/copilot/using-github-copilot/ai-models/changing-the-ai-model-for-copilot-chat>).

## Plan first, then implement

For complex changes that span multiple files, separate planning from implementation. This approach prevents the AI from solving the wrong problem and avoids spending [AI credits](<https://code.visualstudio.com/docs/agents/concepts/language-models#_ai-credits-and-model-costs>) on code that needs to be thrown away.

1. **Explore.** Use chat for questions or use a subagent to read the relevant code and understand how it works before making changes.
2. **Plan.** Use the [Plan agent](<https://code.visualstudio.com/docs/agents/run/planning>) to create a structured implementation plan. Review and refine the plan before executing.
3. **Implement.** Switch to agent mode and implement from the plan. Include tests or expected outputs so the agent can verify its own work. Run independent Copilot, Claude, or Codex sessions in parallel, or hand off to a [cloud agent](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_start-a-cloud-session>) for remote execution.
4. **Review.** Use [checkpoints](<https://code.visualstudio.com/docs/agents/run/review-code-edits#_edit-requests-and-restore-checkpoints>) to review progress, rewind if the agent goes off track, or [request a Copilot code review](<https://docs.github.com/en/copilot/concepts/agents/code-review>) on the resulting pull request.

For more information, see the [context engineering workflow](<https://code.visualstudio.com/docs/agents/guides/context-engineering-guide>).

## Review and verify AI output

AI-generated code can contain bugs, security issues, or subtle logic errors. Always treat AI output as a starting point that needs review.

* **Review before integrating.** Read through generated code before you commit or merge the changes. Pay attention to edge cases, error handling, and assumptions the AI might have made.
* **Run tests after AI changes.** Include test cases in your prompt so the AI can verify its own work. If the AI doesn't run tests automatically, run them yourself before moving on.
* **Use checkpoints to rewind file edits and chat.** If the agent goes off track, use [checkpoints](<https://code.visualstudio.com/docs/agents/run/review-code-edits#_edit-requests-and-restore-checkpoints>) to restore affected workspace files and chat history. Checkpoints don't reverse completed commands or changes to external services.
* **Check for security issues.** Review AI-generated code for common vulnerabilities such as injection flaws, hardcoded secrets, or missing input validation. Avoid pasting credentials or sensitive data into prompts.

For more information, see [GitHub Copilot security](<https://code.visualstudio.com/docs/agents/run/security>) and the [GitHub Copilot Trust Center](<https://copilot.github.trust.page/faq>).

## Choose the right agent harness

When working with agents, choose the harness that matches your task and workflow. Each harness offers different provider capabilities, tools, and execution environments.

* **Use Copilot for day-to-day coding.** Copilot runs tools on your machine with access to your workspace and run-time context. It is a good default for most coding tasks. Tool execution location is separate from where the selected language model runs.
* **Use Claude or Codex for provider-specific capabilities.** These harnesses also run tools on your machine and provide their own SDK capabilities through the same VS Code session experience.
* **Use the Cloud target for team collaboration.** [Cloud sessions](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_start-a-cloud-session>) run remotely and create pull requests, making them well suited to tasks that benefit from team review or when you want to assign a GitHub issue directly to an agent.
* **Run parallel sessions for independent tasks.** Start multiple Copilot, Claude, Codex, or cloud sessions to work on unrelated tasks simultaneously. Monitor them from the [sessions list](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_sessions-list>).
* **Hand off when another target better fits the next step.** Change the session target to [hand off](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_hand-off-a-session>) while preserving the conversation history and context.

For more information, see [choosing an agent harness](<https://code.visualstudio.com/docs/agents/run/agent-harnesses>) and the [agentic coding tutorial](<https://code.visualstudio.com/docs/agents/agents-tutorial>).

## Optimize your project for AI

Project customization is optional. Add it when you have conventions, project context, or workflows that the AI can't reliably infer from your code. Concise customization can also help [optimize AI credit usage](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>) by reducing repeated context gathering, corrections, and retries.

VS Code supports several mechanisms to configure AI behavior for your project. Enter `/init` in chat to generate a starter configuration.

| Mechanism | Best for | Get started |
| --- | --- | --- |
| [Custom instructions](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>) | Project-wide coding standards and architectural context | Type `/init` to generate always-on instructions for your project |
| [Custom agents](<https://code.visualstudio.com/docs/agent-customization/custom-agents>) | Specialized workflows or personas (TDD, security audit) | Type `/create-agent <description>` to generate a custom agent |
| [Skills](<https://code.visualstudio.com/docs/agent-customization/agent-skills>) | Domain-specific capabilities (testing, deployment) | Type `/create-skill <description>` to generate a skill |
| [Tools and MCP servers](<https://code.visualstudio.com/docs/agents/run/tools>) | Connecting to external systems (databases, APIs, CLIs) | Configure in `mcp.json` |

Tips for effective project configuration:

* **Keep instruction files concise.** They load on every chat interaction. Focus on information the AI can't infer from code, such as non-default conventions, architectural decisions, or environment setup.
* **Scope instructions with `applyTo` patterns.** Enter `/instructions` to create language-specific or folder-specific instruction files instead of putting everything in one file.
* **Limit enabled tools.** Fewer active tools means faster, more relevant responses. Enable tools only when the task needs them.
* **Exclude generated and noisy files from search.** Configure 


  search.exclude

  Open in VS Code
  Open in VS Code Insiders
   and 


  files.exclude

  Open in VS Code
  Open in VS Code Insiders
   so agent text search and grep stay focused on source code. See [improve agent search with exclusion settings](<https://code.visualstudio.com/docs/agents/reference/workspace-context#_improve-agent-search-with-exclusion-settings>).

For full setup details, see [Customize agent behavior in VS Code](<https://code.visualstudio.com/docs/agent-customization/overview>).

## Manage context and sessions

AI responses might degrade as the conversation fills with irrelevant context. Manage your sessions proactively.

* **Start new sessions for unrelated tasks.** Don't keep piling unrelated questions into one conversation. Context pollution reduces response quality and wastes tokens on irrelevant history.
* **Remove irrelevant history.** Delete past questions and responses that are no longer relevant, or start a fresh session.
* **Compact context.** Use [/compact](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_compact-conversation-context>) and provide instructions to selectively compact the context and retain only the most relevant information. Compacting reduces the tokens sent with each subsequent request, which helps [manage AI credit usage](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>).
* **Verify cache performance.** Keep the early parts of your prompt stable across turns to reuse the prompt cache and reduce cost and latency. Use the [Cache Explorer](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/cache-explorer>) to check cache hit rates and find where the cache breaks.
* **Use subagents for investigation.** Hint the AI to perform research and exploration in isolation by using [subagents](<https://code.visualstudio.com/docs/agents/run/subagents>) so the findings don't clutter your main context.
* **Choose the right session target and execution environment.** Start with the [Copilot harness](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_use-the-copilot-harness>) for day-to-day work in a folder or isolated worktree. Choose Local for the extension-host workflow in your current workspace, or Cloud for independent work on provider-managed infrastructure that returns a pull request.
* **Scale with parallel sessions.** Run multiple sessions in separate folders or worktrees to save time and keep contexts separate. You can switch among sessions that run on your machine, a connected host, in a Dev Container, or on provider-managed cloud infrastructure from the [sessions list](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_sessions-list>) in VS Code.
* **Fork instead of re-prompting.** Use [`/fork`](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_fork-a-chat-session>) to explore alternatives without losing context, instead of starting over and re-establishing context from scratch.

For more information, see [session management](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions>), [workspace indexing](<https://code.visualstudio.com/docs/agents/reference/workspace-context>), and [optimize AI credit usage](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>).

## Optimize AI credit usage

Many of the practices in this article also help you control cost. More capable models, large contexts, and throwaway work all consume [AI credits](<https://code.visualstudio.com/docs/agents/concepts/language-models#_ai-credits-and-model-costs>). A few levers have the biggest impact:

* **Match the model to the task.** Use faster, cheaper models for simple work and reserve premium models for planning, debugging, and architectural decisions. Auto model selection balances quality and cost for you.
* **Keep context lean.** Send only relevant context, [compact](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_compact-conversation-context>) long conversations, and start fresh sessions for unrelated tasks to avoid paying for irrelevant history on every request.
* **Plan before you implement.** Separating planning from implementation avoids spending credits on code you throw away.

For a complete set of techniques, see [optimize AI credit usage](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>).

## Work with large codebases

Copilot is designed to work effectively with large, complex, and multi-root workspaces. Use these practices to get the best results at scale.

* **Use workspace indexing.** VS Code automatically indexes your project using semantic search, language intelligence, and GitHub's code search for deep cross-file reasoning. This works for both small projects and large enterprise codebases. For large repositories, use [remote indexing](<https://code.visualstudio.com/docs/agents/reference/workspace-context#_semantic-index-sources>) for fast, comprehensive results across your repository and related repositories on GitHub.
* **Scope work with multi-root workspaces.** For monorepos or projects with multiple services, use [multi-root workspaces](<https://code.visualstudio.com/docs/editing/workspaces/multi-root-workspaces>) to give the AI clear boundaries and focused context.
* **Provide project-level instructions.** Use [custom instructions](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>) to describe your project's architecture, module boundaries, and conventions that the AI can't infer from code alone. This gives the AI the context it needs for architecture-level changes.
* **Run parallel sessions for independent changes.** Break large tasks into independent subtasks and run them in [parallel sessions](<https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_sessions-list>), each focused on a different area of the codebase.
* **Use the Plan agent for cross-cutting changes.** For changes that span many files or modules, start with the [Plan agent](<https://code.visualstudio.com/docs/agents/run/planning>) to create a structured implementation plan before executing.

For more information, see [workspace context](<https://code.visualstudio.com/docs/agents/reference/workspace-context>) and [agents](<https://code.visualstudio.com/docs/agents/overview>).

## Related resources

* [Context engineering guide](<https://code.visualstudio.com/docs/agents/guides/context-engineering-guide>)
* [Optimize AI credit usage](<https://code.visualstudio.com/docs/agents/guides/optimize-usage>)
* [Customize agent behavior in VS Code](<https://code.visualstudio.com/docs/agent-customization/overview>)
* [Cheat sheet](<https://code.visualstudio.com/docs/agents/reference/ai-features-cheat-sheet>)
* [GitHub Copilot security](<https://code.visualstudio.com/docs/agents/run/security>)
* [Best Practices for using GitHub Copilot](<https://docs.github.com/en/copilot/using-github-copilot/best-practices-for-using-github-copilot>) in the GitHub Copilot documentation

10/7/2026


</details>
