# 提高 AI 使用效率并降低成本

围绕模型与推理级别选择、清晰提示、精简上下文和执行约束给出建议，帮助减少重复工作和用量消耗。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/tutorials/optimize-ai-usage>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/提高 AI 使用效率并降低成本.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 优化您的 AI 使用情况以最大限度地提高效率并降低成本

了解如何选择正确的模型、构建提示以及添加护栏，以便 Copilot 更高效地完成任务并使用更少的 AI 积分。

## 简介

本文概述的策略向您展示如何提高 Copilot 效率，从而减少使用 AI 积分。

## 1。为正确的任务选择正确的模型

通过为您的任务选择正确的功能级别、适当配置推理以及针对特定工作负载利用自动模型选择和更便宜的模型，您可以在保持质量的同时显着减少令牌消耗。

### 选择正确的型号

型号选择是提高成本效率最快的方法之一，但它常常被忽视。一种常见的模式是为每个任务默认使用最有能力的模型，但这通常会增加令牌的使用而不会改善结果。在某些执行繁重的场景中，过度使用推理模型可能会降低质量，因为模型可能会过度考虑任务或引入不必要的更改。

根据所涉及的工作选择型号：

* **推理模型**：最适合架构决策、复杂调试、系统设计和需要更深入分析的任务。
* **中层型号**：当计划已经明确并且agent需要高效执行时最好。
* **更轻的模型**：最适合重构、格式化、文档更新和其他常规的、范围广泛的更改。

根据任务需要使用尽可能多的功能，并根据需要使用尽可能少的功能。将能力与任务相匹配可以改善结果并直接大规模控制成本。

有关模型和任务类型的详细信息，请参阅[AI模型比较](<https://docs.github.com/en/copilot/reference/ai-models/model-comparison#recommended-models-by-task>)。

### 配置模型的推理级别

某些模型还支持可配置的推理级别，它控制模型在响应之前推理的程度。更高的级别可以改善复杂问题的答案，但它会消耗更多的代币，因此会消耗更多的积分，因此您应该默认使用常规级别，并且仅在完成更困难的任务时才提高它。可配置推理适用于受支持型号的 Visual Studio Code 和 Copilot CLI。

参见 [支持 AI 模型在 GitHub Copilot](<https://docs.github.com/en/copilot/reference/ai-models/supported-models#models-with-extended-capabilities>).

### 使用 Copilot 自动模型选择作为默认值

自动模型选择会根据您的任务意图为您选择一个有能力的模型。

一个小型路由器会查看您的提示并将其发送到可以**最有效地处理它的模型**，为复杂问题保留昂贵的推理模型。它还避免了快速耗尽代币预算的模型。

自动模型选择还 ** 保护您的缓存 **。当新会话启动或运行 `/compact` 后，它会在自然缓存边界更改模型。随着对话的发展，它将在质量改进超过缓存损失的点重新路由。要了解有关其重要性的更多信息，请参阅 [4。保留缓存](<#4保留缓存>)。

自动模型选择还会绕过降级或繁忙的模型，因此您会遇到更少的速率限制和错误。

如果您使用付费 Copilot 套餐，则在 Copilot 聊天、Copilot CLI、GitHub Copilot 应用程序或 Copilot 云中使用自动模型选择时，您有资格获得模型费用 10% 折扣agent。

有关该功能及其可用性的信息，请参阅[关于Copilot自动模型选择](<https://docs.github.com/en/copilot/concepts/models/auto-model-selection#copilot-chat-in-ides>)。

### 为子代理使用更便宜的模型

在更便宜的型号上运行子代理。子代理在自己的会话中运行，并且不继承主 agent 的对话历史记录。因为它们的上下文范围仅限于单一的重点任务，所以较轻的模型通常就足够了，并且分配一个模型不会像会话中模型切换那样影响主 agent 的缓存。

## 2。在提示中提供明确的指导

您的提示为 agent 所做的一切设定了方向。当提示模糊时，agent 必须推断意图，探索更多上下文，并做出判断。这通常会导致重试、范围漂移和不必要的令牌使用。

结构良好的提示具有三个品质：

* **清晰的任务定义。**不要“解决这个问题”，而是解释问题是什么、问题发生在哪里以及预期结果是什么样的。
* **预先提供相关上下文。**如果您已经知道哪些文件、服务、日志、错误或输入很重要，请将它们包括在内。这有助于 agent 避免不必要的探索。
* **明确的停止条件。**告诉agent“完成”是什么样子。如果没有停止点，agents 可以通过添加额外的提交、重构不相关的代码或扩展范围来继续超越目标。

此附加指南不会显着增加令牌使用量，但可以显着减少达到正确结果所需的 agent 运行次数。

有关提示工程最佳实践，请参阅 [GitHub Copilot 聊天的提示工程](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>)。

## 3。保持上下文简洁

Copilot 将其有权访问的上下文作为输入令牌发送，并且该上下文加起来：打开的编辑器选项卡、附加文件以及长对话的完整来回都算作上下文。

为了控制上下文，请考虑执行以下操作：

### 当您切换问题时开始新的对话

长线程将其整个历史记录携带到每个新请求中。当您继续执行不相关的任务时，开始新的对话。例如：

* 在Copilot CLI中使用`/new`（或`/clear`）
* 在 Copilot 聊天中，开始新的聊天会话。

### 您想要继续的紧凑型长 Copilot CLI 会话

当您需要线程继续运行但它已经变大时，请在 Copilot CLI 中运行 `/compact` 来总结历史记录并缩小上下文窗口，可选择聚焦摘要（例如，`/compact focus on the auth module`）。

另外，您可以随时使用【`/context`】查看当前使用情况。

参见 [管理上下文 GitHub Copilot CLI](<https://docs.github.com/en/copilot/concepts/agents/copilot-cli/context-management>).

### 向 Copilot 提供您的项目地图

维护良好的自定义指令文件，例如 `AGENTS.md` 或 `.github/copilot-instructions.md` 文件，可以为 agents 提供存储库的结构概览，因此他们不必为了定位自己而读取大量文件。参见[对不同类型自定义指令的支持](<https://docs.github.com/en/copilot/reference/custom-instructions-support>)。

### 只带入您需要的工具

大型工具集（例如，完整的 MCP 服务器的工具）会添加到每个请求的上下文中。如果适合您的工作流程，则仅启用与任务相关的工具集。

请参阅 [为 GitHub MCP 服务器 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/customize-copilot/extend-copilot-with-tools-and-context/configure-toolsets>) 配置工具集。

## 4。保留缓存

缓存允许 AI 模型存储对话上下文的部分内容，这样就不需要对每个请求进行重新处理。在代理编码中，相同的大型上下文（系统提示、文件内容、工具定义）会多次重复发送，缓存会产生影响：先前响应的缓存部分会被重用而不是重新处理，并且缓存的令牌通常按正常输入价格的 10% 计费。请参阅 [ GitHub Copilot](<https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing>) 的型号和定价。

但是，以下操作会使缓存无效，导致重新发送完整上下文并按新输入令牌计费：

* **会话中切换模型**。不同的模型无法重用另一个模型的缓存，因此下一个请求将从头开始重建它。选择一个模型（或使用 Copilot 自动模型选择）并在会话中坚持使用它。
* **回到旧会话**。缓存会在一段时间不活动后过期（OpenAI 型号为 24 小时，大多数其他型号为 1 小时）。如果您离开了一段时间，请开始一个新会话，或运行 `/compact`（在 Copilot CLI 中），这样重建的只是一个简短的摘要，而不是完整的历史记录。
* **在会话中更改推理**。在会话期间更改推理工作级别、上下文大小或启用的工具集和 MCP 服务器会使缓存失效。在开始之前配置这些设置，并在会话中保持它们不变。

## 5。设置 AI 信用会话限额

AI 信用会话限制限制 Copilot 在单个会话上执行的工作量。

在Copilot CLI和Copilot SDK中，您可以在启动任务之前设置会话限制，当达到限制时，agent会干净地停止，通知您，并让您选择是继续还是提高限制。

AI 信用会话限制在以下情况下最有用：

* 您希望限制单个会话的 AI 积分使用量，以避免意外费用。
* 您正在调整 agent 效率，并希望找到仍能产生良好结果的最小 AI 积分。

会话限制是软限制，可帮助您控制任何单个任务消耗的 AI 积分数量，但它们不会取代用户级预算或支出限制，后者控制您的每月总体消耗。对于这些控件，请参阅 [基于使用情况的计费的预算 ](<https://docs.github.com/en/copilot/concepts/billing-and-usage/organizations-and-enterprises/budgets>)。

有关如何在 Copilot CLI 中设置会话限制的信息，请参阅 [在 GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/set-session-limit>) 中设置 AI 信用会话限制。

## 6。研究、计划、然后实施

使用 agents 进行有效工作的最大转变之一是不再在单个会话中完成所有操作。当研究、规划和实施同时发生时，背景会迅速增长，不相关的信息也会积累。

将工作分成明确的阶段：

* **研究：** 使用 agent 探索代码库、识别相关文件并了解依赖关系。
* **计划：**在进行更改之前创建详细的结构化计划或规范。这就是推理模型最有价值的地方——始终使用强大的推理模型进行规划，然后使用更便宜的模型来实施工作。
  * 在CopilotCLI中，使用`/plan`。
  * 在 Copilot 中的 Visual Studio Code 聊天中，从 agent 下拉列表中选择“计划”，或在上下文窗口中键入 `plan`。
* **实施：**使用重点上下文和适合执行的模型来执行计划。

在阶段之间启动新会话可以防止您向前传递不必要的上下文，这会增加令牌的使用并降低 agent 的清晰度。每个阶段应该仅根据其需要进行操作。有关有效范围界定会话的指导，请参阅 [使用 GitHub Copilot 处理任务的最佳实践 ](<https://docs.github.com/en/copilot/tutorials/cloud-agent/get-the-best-results>)。

## 7。利用所学知识在每个环节提高效率

### 使用 `/chronicle` 生成见解

在 Copilot CLI 中，`/chronicle` 可以从您的会话历史记录中生成有用的见解。

* 使用 `/chronicle tips` 分析您最近的会话历史记录并寻找更有效地使用 Copilot 的机会。
* 使用 `/chronicle cost-tips` 了解您的代币使用模式并深入了解如何降低成本。

请参阅[使用GitHub CopilotCLI会话数据](<https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle#using-the-chronicle-slash-command>)。

### 将见解输入 `copilot-instructions.md` 文件

存储库级别的 `copilot-instructions.md` 文件是对特定于存储库的指南进行编码的最直接方法。个人和组织级别的指示可以分层以实现更广泛的一致性。

当 `/chronicle` 出现重复出现的模式（过度使用的工具、不断被误读的提示）时，直接将该观察结果编码到您的 `copilot-instructions.md` 文件中。这会将一次性的见解转变为适用于未来每次会议的长期指导，而无需重复。

有关更多信息，请参阅 [为 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>) 添加存储库自定义指令。

### 保持 `copilot-instructions.md` 文件具体且接地气

持久指令提高了 agent 交互的一致性，但它们的价值完全取决于它们的编写方式。最好的说明是简短、具体的，并且基于真实观察到的 agent 行为，而不是听起来不错但不适用于您的系统的通用最佳实践。

**包含内容：**

* 所需的框架、库或设计模式
* agent 容易重复的已知陷阱
* 输出期望，例如“简洁”或“仅返回代码”
* agent 必须遵循的团队特定约定
* 构建、测试和 lint 命令

**要避免什么：**

* 长而通用的文档
* AI-生成的指南并不反映您的实际系统
* 一次性偏好或很少使用的细节
* 超载的指令使上下文变得嘈杂

随着代码库、架构、标准和工作流程的发展，不断更新说明。由于这些指令在每次运行时都包含在 agent 的上下文中，因此即使是很小的改进也可以减少重复错误并随着时间的推移减少浪费的令牌使用。

## 8。添加确定性护栏

Agents 是不确定的，不会每次都正确，尤其是在多步骤工作流程中。如果没有护栏，小错误可能会迅速复合：agents 建立在不正确的输出上，偏离目标更远，并使调试更加昂贵和耗时。

确定性控制引入清晰的 pass/fail 信号：

* **单元测试**验证agent的更改产生了预期的行为。
* **Linters** 强制结构和一致性，防止格式问题、样式漂移和可避免的清理工作。
* **安全扫描**尽早发现风险模式，避免它们变得更难解除。

这些控件共同创建了一个紧密的反馈循环：agent 进行更改、测试、规则或扫描对其进行评估，而 agent 在继续之前进行调整。这可以防止长链错误更改，这是造成代币浪费的最大因素之一。

投资这些护栏的团队会看到更少的重试、更快的任务完成以及更可预测的 agent 行为。即使各个步骤预先使用稍微多的令牌，它们通常也会减少总令牌消耗。

## 后续步骤

监控和管理您的支出，以充分利用您的 AI 积分：

* **使用仪表板和预算控件**。 <https://github.com/settings/billing> 下的“AI 使用情况”页面详细列出了每个功能和型号的消耗情况，以便您可以查看您的积分实际去向并进行相应调整。请参阅 [监控您的 GitHub AI 积分使用情况 ](<https://docs.github.com/en/copilot/how-tos/manage-and-track-spending/monitor-ai-usage>)。
* **升级以获得更大的津贴**。如果您经常接近每月限额，更高的计划可能比支付额外的使用费用更经济，因为更高的计划有更多的 AI 信用额度。请参阅 [GitHub Copilot](<https://docs.github.com/en/copilot/get-started/plans#github-ai-credits-allowance-by-plan>) 计划和 [查看和更改您的 GitHub Copilot 计划 ](<https://docs.github.com/en/copilot/how-tos/manage-your-account/view-and-change-your-copilot-plan>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Optimizing your AI usage to maximize efficiency and reduce cost

Learn how to choose the right models, structure your prompts, and add guardrails so that Copilot completes tasks more efficiently and uses fewer AI credits.

## Introduction

The strategies outlined in this article show you how to improve Copilot efficiency and, as a result, use fewer AI credits.

## 1. Choose the right model for the right task

By selecting the right capability level for your task, configuring reasoning appropriately, and leveraging auto model selection and cheaper models for specific workloads, you can maintain quality while significantly reducing token consumption.

### Select the right model

Model choice is one of the fastest ways to improve cost efficiency, but it is often overlooked. A common pattern is to default to the most capable model for every task—but this often increases token usage without improving the outcome. In some execution-heavy scenarios, overusing reasoning models can reduce quality because the model may overthink the task or introduce unnecessary changes.

Choose the model based on the work involved:

* **Reasoning models**: Best for architecture decisions, complex debugging, system design, and tasks that require deeper analysis.
* **Mid-tier models**: Best when the plan is already clear and the agent needs to execute efficiently.
* **Lighter models**: Best for refactoring, formatting, documentation updates, and other routine, well-scoped changes.

Use as much capability as the task requires, and as little as necessary. Matching capability to task improves outcomes and directly controls costs at scale.

For a breakdown by model and task type, see [AI model comparison](<https://docs.github.com/en/copilot/reference/ai-models/model-comparison#recommended-models-by-task>).

### Configure the reasoning level of the model

Some models also support configurable reasoning levels, which control how much the model reasons before it responds. A higher level can improve answers to complex problems, but it consumes more tokens, and therefore more credits, so you should use the regular level by default and raise it only for harder tasks. Configurable reasoning is available for Visual Studio Code and Copilot CLI for supported models.

See [Supported AI models in GitHub Copilot](<https://docs.github.com/en/copilot/reference/ai-models/supported-models#models-with-extended-capabilities>).

### Use Copilot auto model selection as your default

Auto model selection chooses a capable model for you, based on the intent of your task.

A small router looks at your prompt and sends it to the model that can **handle it most efficiently**, reserving expensive reasoning models for complex problems. It also avoids models that burn through a token budget quickly.

Auto model selection also **protects your cache**. It changes models at natural cache boundaries, when a new session starts or after you run `/compact`. As conversations evolve, it will re-route at points where quality improvements outweigh cache loss. To understand more about why this matters, see [4. Preserve the cache](<#4-preserve-the-cache>).

Auto model selection also routes around degraded or busy models, so you hit fewer rate limits and errors.

If you are on a paid Copilot plan, you qualify for a 10% discount on model costs while using auto model selection in Copilot Chat, Copilot CLI, GitHub Copilot app, or Copilot cloud agent.

For information on the feature and its availability, see [About Copilot auto model selection](<https://docs.github.com/en/copilot/concepts/models/auto-model-selection#copilot-chat-in-ides>).

### Use cheaper models for subagents

Run subagents on cheaper models. Subagents run in their own session and don't inherit the main agent's conversation history. Because their context is scoped to a single focused task, a lighter model is often sufficient—and assigning one doesn't affect the main agent's cache the way a mid-session model switch would.

## 2. Provide clear guidance in your prompts

Your prompt sets the direction for everything the agent does. When a prompt is vague, the agent has to infer intent, explore more context, and make judgment calls. That often leads to retries, scope drift, and unnecessary token usage.

Well-structured prompts have three qualities:

* **A clear task definition.** Instead of "fix this issue," explain what the issue is, where it occurs, and what the expected outcome looks like.
* **Relevant context provided upfront.** If you already know which files, services, logs, errors, or inputs matter, include them. This helps the agent avoid unnecessary exploration.
* **A clear stopping condition.** Tell the agent what "done" looks like. Without a stopping point, agents can continue beyond the goal by adding extra commits, refactoring unrelated code, or expanding scope.

This added guidance doesn't meaningfully increase token usage, but it can significantly reduce the number of agent runs needed to reach the right outcome.

For prompt engineering best practices, see [Prompt engineering for GitHub Copilot Chat](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>).

## 3. Keep your context lean

Copilot sends the context it has access to as input tokens, and that context adds up: open editor tabs, attached files, and the full back-and-forth of a long conversation all count as context.

To keep context under control, consider doing the following:

### Start a new conversation when you switch problems

A long thread carries its entire history into every new request. When you move on to an unrelated task, start a new conversation. For example:

* In Copilot CLI use `/new` (or `/clear`)
* In Copilot Chat, start a new chat session.

### Compact long Copilot CLI sessions that you want to continue

When you need the thread to keep going but it has grown large, run `/compact` in Copilot CLI to summarize the history and shrink the context window, optionally focusing the summary (for example, `/compact focus on the auth module`).

In addition, you can use `/context` to check current usage at any time.

See [Managing context in GitHub Copilot CLI](<https://docs.github.com/en/copilot/concepts/agents/copilot-cli/context-management>).

### Give Copilot a map of your project

A well-maintained custom instructions file, such as an `AGENTS.md` or `.github/copilot-instructions.md` file, gives agents a structural overview of your repository so they don't have to read large numbers of files just to orient themselves. See [Support for different types of custom instructions](<https://docs.github.com/en/copilot/reference/custom-instructions-support>).

### Bring in only the tools you need

Large tool sets (for example, a full MCP server's worth of tools) add to the context on every request. Where it fits your workflow, enable only the toolsets relevant to the task.

See [Configuring toolsets for the GitHub MCP Server](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/customize-copilot/extend-copilot-with-tools-and-context/configure-toolsets>).

## 4. Preserve the cache

Caching lets an AI model store portions of a conversation's context so they don't need to be reprocessed on every request. In agentic coding, where the same large context—system prompt, file contents, tool definitions—is sent repeatedly across many turns, caching has an impact: the cached portion from the previous response is reused rather than reprocessed, and cached tokens are typically billed at 10% of the normal input price. See [Models and pricing for GitHub Copilot](<https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing>).

However, the following actions invalidate the cache, causing the full context to be re-sent and billed as fresh input tokens:

* **Switching models mid-session**. A different model can't reuse another model's cache, so the next request rebuilds it from scratch. Pick a model (or use Copilot auto model selection) and stick with it for the session.
* **Coming back to an old session**. Caches expire after a period of inactivity (24 hours for OpenAI models and 1 hour for most others). If you've been away a while, start a new session, or run `/compact` (in Copilot CLI) so what gets rebuilt is a short summary rather than the full history.
* **Changing reasoning mid-session**. Changing the reasoning effort level, context size, or the set of enabled tools and MCP servers during a session invalidates the cache. Configure these settings before you start and leave them unchanged for the session.

## 5. Set AI credit session limits

An AI credit session limit caps the amount of work Copilot performs on a single session.

In Copilot CLI and Copilot SDK, you can set a session limit before initiating a task and when the limit is reached, the agent stops cleanly, notifies you, and lets you choose whether to continue or raise the limit.

AI credit session limits are most useful when:

* You want to cap AI credits usage on a single session to avoid unexpected costs.
* You're tuning agent efficiency and want to find the minimum AI credits that still produces a good result.

Session limits are soft limits that help you control how many AI credits any single task consumes, but they don't replace user-level budgets or spending limits, which govern your overall monthly consumption. For those controls, see [Budgets for usage-based billing](<https://docs.github.com/en/copilot/concepts/billing-and-usage/organizations-and-enterprises/budgets>).

For information on how to set a session limit in Copilot CLI, see [Setting an AI credit session limit in GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/set-session-limit>).

## 6. Research, plan, then implement

One of the biggest shifts in working effectively with agents is moving away from doing everything in a single session. When research, planning, and implementation all happen together, context grows quickly and irrelevant information accumulates.

Break work into clear phases:

* **Research:** Use the agent to explore the codebase, identify relevant files, and understand dependencies.
* **Plan:** Create a detailed, structured plan or specification before making changes. This is where reasoning models are most valuable—always plan with a strong reasoning model, then implement the work with a cheaper model.
  * In Copilot CLI, use `/plan`.
  * In Copilot Chat in Visual Studio Code, select "Plan" from the agent dropdown, or type `plan` in the context window.
* **Implement:** Execute against the plan using focused context and a model suited for execution.

Starting a new session between phases prevents you from carrying unnecessary context forward, which can increase token usage and reduce clarity for the agent. Each phase should operate with only what it needs. For guidance on scoping sessions effectively, see [Best practices for using GitHub Copilot to work on tasks](<https://docs.github.com/en/copilot/tutorials/cloud-agent/get-the-best-results>).

## 7. Utilize learnings to be more efficient at every turn

### Use `/chronicle` to generate insights

In Copilot CLI, `/chronicle` can generate useful insights from your session history.

* Use `/chronicle tips` to analyze your recent session history and surface opportunities to use Copilot more efficiently.
* Use `/chronicle cost-tips` to understand your token usage patterns and get insights into how to reduce cost.

See [Using GitHub Copilot CLI session data](<https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle#using-the-chronicle-slash-command>).

### Feed insights into a `copilot-instructions.md` file

A `copilot-instructions.md` file at the repository level is the most direct way to encode guidance that's specific to your repository. Personal and organization-level instructions can layer on top for broader consistency.

When `/chronicle` surfaces a recurring pattern—a tool being over-used, a prompt that keeps being misread—encode that observation directly in your `copilot-instructions.md` file. This turns a one-time insight into standing guidance that applies to every future session, without you having to repeat it.

For more information, see [Adding repository custom instructions for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

### Keep the `copilot-instructions.md` file specific and grounded

Persistent instructions improve consistency across agent interactions, but their value depends entirely on how they are written. The best instructions are short, specific, and grounded in real observed agent behavior—not generic best practices that sound good but don't apply to your system.

**What to include:**

* Required frameworks, libraries, or design patterns
* Known pitfalls the agent tends to repeat
* Output expectations such as "be concise" or "only return code"
* Team-specific conventions the agent must follow
* Build, test, and lint commands

**What to avoid:**

* Long, generic documentation
* AI-generated guidance that doesn't reflect your actual system
* One-off preferences or rarely used details
* Overloaded instructions that make the context noisy

Keep instructions updated as your codebase, architecture, standards, and workflows evolve. Because these instructions are included in the agent's context on every run, even small improvements can reduce repeated errors and lower wasted token usage over time.

## 8. Add deterministic guardrails

Agents are non-deterministic and won't be correct every time, especially in multi-step workflows. Without guardrails, small errors can compound quickly: agents build on incorrect outputs, drift further from the goal, and make debugging more expensive and time-consuming.

Deterministic controls introduce clear pass/fail signals:

* **Unit tests** verify the agent's changes produced the expected behavior.
* **Linters** enforce structure and consistency, preventing formatting issues, style drift, and avoidable cleanup work.
* **Security scans** catch risky patterns early, before they are harder to unwind.

Together, these controls create a tight feedback loop: the agent makes a change, a test, rule, or scan evaluates it, and the agent adjusts before moving forward. This prevents long chains of incorrect changes, which are one of the biggest drivers of token waste.

Teams that invest in these guardrails see fewer retries, faster task completion, and more predictable agent behavior. They often reduce total token consumption even if individual steps use slightly more tokens upfront.

## Next steps

Monitor and manage your spending to get the most out of your AI credits:

* **Use your dashboard and budget controls**. The "AI usage" page, under <https://github.com/settings/billing>, breaks down consumption across every feature and model, so you can see where your credits are actually going and adjust accordingly. See [Monitoring your GitHub AI Credits usage](<https://docs.github.com/en/copilot/how-tos/manage-and-track-spending/monitor-ai-usage>).
* **Upgrade for a larger allowance**. If you regularly approach your monthly limit, a higher plan may be more economical than paying for additional usage, as higher plans have more AI credit allowance. See [Plans for GitHub Copilot](<https://docs.github.com/en/copilot/get-started/plans#github-ai-credits-allowance-by-plan>) and [Viewing and changing your GitHub Copilot plan](<https://docs.github.com/en/copilot/how-tos/manage-your-account/view-and-change-your-copilot-plan>).


</details>
