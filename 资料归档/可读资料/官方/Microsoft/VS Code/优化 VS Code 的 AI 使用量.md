# 优化 VS Code 的 AI 使用量

讨论模型选择、先规划后委派、新任务换会话、控制工具与上下文、批量操作和使用量检查，以减少重试与返工。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/optimize-usage.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/optimize-usage.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/优化 VS Code 的 AI 使用量.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: d4e5f6a7-8b9c-0d1e-2f3a-4b5c6d7e8f9a
DateApproved: 10/7/2026
MetaDescription: Reduce AI credit usage in {% data variables.product.prodname_vscode_shortname %} with efficient models, scoped tools, and focused context.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 优化{% data variables.product.prodname_vscode_shortname %}中的AI积分使用情况

每个 GitHub Copilot 计划包含每月 [AI 积分 ](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>) 的津贴。根据模型和处理的代币数量，不同的操作以不同的速率消耗积分。本指南涵盖了充分利用 {% data variables.product.prodname_vscode %} 中的 AI 积分的实用方法。

## 根据证据选择模型

成本最低的模型并不总是产生成本最低的结果。需要重试或创建返工的模型可能比可靠完成任务的有能力的模型花费更多。反之亦然：并非每个任务都需要复杂的推理模型，将简单或重复的任务路由到更轻的模型可以避免为不需要的功能付费。

从 [AI 模型比较表 ](<https://docs.github.com/en/copilot/reference/ai-models/model-comparison>) 和 [ 模型选择最佳实践 ](<https://github.com/docs/agents/best-practices.md#choose-the-right-model>) 开始作为一般指导，然后使用您自己工作中的代表性任务来比较信用使用情况与响应质量：

1. 定义预期结果以及如何评估其质量。
1. 由于模型输出是不确定的，因此多次运行每个任务。
1. 比较任务完成情况、输出质量、可靠性、持续时间和信用消耗。
1. 选择始终满足您的要求的最低成本模型，并随着模型的变化重新评估。

对于低维护默认值，请使用 ** 自动模型选择 ** 让 {% data variables.product.prodname_vscode_shortname %} 根据任务复杂性、模型运行状况和可用性将每个请求路由到高效模型。对于可重复的工作流程，请在 [ 自定义 agent](<https://github.com/docs/agent-customization/custom-agents.md>) 中配置首选模型。

聊天中的模型选择器在悬停菜单中显示成本详细信息，包括每个代币类型的成本和通用成本层标签（低、中、高）。使用 [Agent 调试日志 ](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md>) 比较测试运行期间的令牌使用情况、工具调用、错误和持续时间。

有关更多信息，请参阅[选择和配置语言模型](<https://github.com/docs/agent-customization/language-models.md>)。

## 在实施之前进行计划和授权

如果方法错误，直接跳到代码生成可能会浪费学分。单独的规划、实施和审查，以便每个阶段都使用适当的模型：

1. 使用 [ 计划 agent](<https://github.com/docs/agents/run/planning.md>) 和强大的推理模型来研究任务并创建结构化的实施计划。
1. 在 agent 编写代码之前审查并完善计划。
1. 将批准的计划划分为范围明确的实施任务。
1. 将这些任务委派给 [定制 agents 作为子代理运行](<https://github.com/docs/agents/run/subagents.md#run-a-custom-agent-as-a-subagent>)。为每个工作人员配置具有成本效益的模型、针对性的说明以及其所需的工具。
1. 让主要 agent 评估结果并解决需要更广泛背景或更强推理的工作。

这种协调员和工作人员模式使主要的 agent 专注于规划和评估，同时较低成本的模型执行有限的任务。委托本身使用令牌，因此当隔离和更便宜的模型无法抵消编排开销时，请将小任务保留在主会话中。

有关详细信息，请先参阅[规划，然后实施](<https://github.com/docs/agents/best-practices.md#plan-first-then-implement>)。

## 使用思维努力默认值

思考努力控制模型对每个请求应用的推理量。更高的努力水平会产生更多的思考令牌，这会增加延迟和信用消耗。 {% data variables.product.prodname_vscode_shortname %} 根据评估设置默认工作级别，并启用自适应推理，其中模型根据每个请求的复杂性动态决定思考多少。

对于大多数任务，默认值就足够了。仅增加对真正复杂问题（例如架构规划或多步骤调试）的思考力度。

有关更多信息，请参阅[配置思维工作](<https://github.com/docs/agent-customization/language-models.md#configure-thinking-effort>)。

## 为新任务开始新聊天

随着对话的发展，它会从之前的消息、工具输出和文件内容中积累上下文。当您在同一会话中切换到不相关的任务时，模型仍然会处理所有不相关的历史记录，这会消耗令牌而不会改善结果。

当您更改主题时，启动 [新的 agent 会话 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md>) (`kb(workbench.action.chat.newChat)`)。这为模型提供了一个专注于当前任务的干净上下文窗口。

## 分叉对话

当您想探索替代方法或提出附带问题时，[分叉对话](<https://github.com/docs/agents/run/sessions/manage-sessions.md#fork-a-chat-session>)，而不是从头开始重新提示。分叉会创建一个继承现有对话历史记录的新会话，因此您无需重新建立上下文。

* 在聊天输入中键入 `/fork` 以分叉整个会话直至当前消息。
* 将鼠标悬停在上一条消息上，然后选择 **Fork Conversation** 以从特定检查点进行 fork。

## 保持工具目录的重点

大型工具目录增加了输入上下文并使工具选择更加复杂。工具调用还将其输出添加到 [ 上下文窗口 ](<https://github.com/docs/agents/concepts/language-models.md#context-window>)。仅公开与当前任务相关的工具：

* 使用聊天输入字段中的**配置工具**按钮为当前请求选择单个工具或MCP服务器。搜索工具选择器并在任务需要时添加另一个工具。
* 在 [ 自定义 agents](<https://github.com/docs/agent-customization/custom-agents.md>) 中，使用 `tools` 属性为每个 agent 提供一个狭窄的、特定于任务的工具集。

对于拥有大量内部工具目录的组织，请考虑为常见工作流程提供精心设计的命令行界面。 agent可以通过终端工具调用命令，而无需将每个操作作为单独的MCP工具加载。此方法要求您的组织维护和保护命令行界面。

有关更多信息，请参阅[将工具与agents](<https://github.com/docs/agents/run/tools.md>)一起使用。

## 批量重复操作

重复的提交、轮询和检索工具调用将中间结果添加到模型上下文中。对于确定性或批量工作流程，请使用脚本或命令行工具在 agent 对话之外运行循环并仅返回最终结果。

例如，不要要求 agent 一次发出并检查许多类似的数据库查询，而是使用经过审查的脚本来运行查询批处理并生成简洁的摘要。使用终端工具运行脚本，然后将摘要提供给模型进行分析。对工作流程的脚本化和交互式版本进行基准测试，因为节省的费用取决于工具、结果和任务。

## 在广泛探索之前先接地 agent

相关上下文有助于 agent 避免失败的搜索、不必要的工具调用和不正确的更改。在广泛探索之前：

* 确保 [ 工作空间语义索引 ](<https://github.com/docs/agents/reference/workspace-context.md#semantic-search>) 可用，并在您想要在索引代码库中显式接地请求时使用 `#codebase`。
* 在提示中提供相关文件、错误、约束和成功标准。
* 使用符号感知搜索和集中的 MCP 或命令行工具来获取权威的项目和组织数据。
* 将稳定的项目约定添加到[自定义指令](<https://github.com/docs/agent-customization/custom-instructions.md>)，以便agent不会在每个会话中重新发现它们。

接地应该缩小搜索空间，而不是预加载每个可用的源。包括让 agent 完成任务的最小权威上下文集。

## 从聊天上下文中排除文件

生成的大型文件、构建输出或不相关的目录可能会增加令牌的使用，但不会增加价值。排除这些文件以保持 agent 上下文集中：

* 使用 `.gitignore` 文件从 [ 工作空间索引 ](<https://github.com/docs/agents/reference/workspace-context.md#what-content-is-included-in-the-semantic-index>) 以及 agent 文本搜索和 grep 中排除文件。
* 使用 `setting(files.exclude)` 设置完全隐藏 {% data variables.product.prodname_vscode_shortname %} 中的文件，这也会将它们从索引和 agent 搜索工具中排除。
* 使用 `setting(search.exclude)` 设置从 agent 文本搜索和 grep 中排除文件，同时保持它们在资源管理器中可见，例如您想要手动打开但不包含在搜索结果中的日志文件。

即使 agent 未打开匹配的文件，搜索匹配片段也会计入上下文窗口。排除噪声路径可以减少搜索结果中不相关的标记。

有关更多信息和示例配置，请参阅 [使用排除设置改进 agent 搜索 ](<https://github.com/docs/agents/reference/workspace-context.md#improve-agent-search-with-exclusion-settings>)。

## 通过压缩管理上下文

当对话变长时，使用 `/compact` 总结对话的较旧部分并回收上下文窗口空间。您可以选择添加说明来指导摘要，例如 `/compact focus on the API design decisions`。

有关更多信息，请参阅[上下文压缩](<https://github.com/docs/agents/run/sessions/manage-sessions.md#compact-conversation-context>)。

## 监控您的使用情况

您可以在{% data variables.product.prodname_vscode_shortname %}中监控特定聊天请求的AI积分使用情况、会话的累计使用情况以及您每月的总体消耗情况。这可以帮助您了解哪些请求成本更高，发现成本高昂的对话，并随着时间的推移管理您的使用情况。

要查看单个请求的成本，请将鼠标悬停在聊天响应上以查看该回合的信用消耗。这可以帮助您了解哪些请求更昂贵，并相应地调整您的提示或模型选择。

要查看整个会话的累积成本和令牌细分，请将鼠标悬停在聊天输入中的上下文窗口控件上或选择该控件。会话信息弹出窗口显示整个会话的总信用成本和累积上下文窗口令牌使用情况，补充了已为各个请求显示的每轮信用消耗。

![{% data variables.product.prodname_vscode_shortname %} {% data variables.copilot.chat_view %} 的屏幕截图，显示聊天输入框中的上下文窗口使用控件。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/chat/images/copilot-chat/chat-context-window-control.png>)

要查看每月的总体消耗量，请从 {% data variables.product.prodname_vscode_shortname %} 状态栏打开 Copilot 状态仪表板。仪表板显示您用于 AI 积分的每月津贴百分比（以及 {% data variables.copilot.copilot_free_short %} 计划的内嵌建议）。

![Copilot 状态仪表板的屏幕截图，显示每月使用的 AI 积分的百分比以及用于查看 GitHub 使用详细信息的链接。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/optimize-usage-guide/copilot-status-dashboard.png>)

请访问 GitHub Copilot 文档，了解有关 [ 监控使用情况和权利 ](<https://docs.github.com/en/copilot/managing-copilot/monitoring-usage-and-entitlements/monitoring-your-copilot-usage-and-entitlements>) 的更多信息。

您还可以在任何聊天会话中运行 `/chronicle:cost-tips` 命令，以获得根据您最近的活动优化您的 AI 积分使用情况的个性化建议。了解有关 [ 会话见解和历史记录命令 ](<https://github.com/docs/agents/run/sessions/session-history.md#query-session-history-with-chronicle>) 的更多信息。

## 检查令牌使用和缓存

使用 [Agent 调试日志 ](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md>) 了解会话中消耗积分的内容：

* **摘要视图**显示会话的聚合令牌使用情况，包括工具调用总数和总持续时间。
* **[缓存资源管理器视图](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>)**显示提示缓存命中率以及重复使用的输入令牌数量。提示缓存允许模型提供者重用与前一个请求匹配的请求的前缀，从而减少延迟和令牌成本。

使用这些测量作为优化循环：

1. 运行代表性任务并记录其质量、可靠性、信用、令牌、持续时间、工具调用和错误。
1. 更改一个变量，例如模型、可用工具、授权策略或接地源。
1. 重复该任务并比较结果。
1. 以最低的成本保持满足您的质量阈值的配置。

## 相关内容

* [AI 积分和模型成本](<https://github.com/docs/agents/concepts/language-models.md#ai-credits-and-model-costs>)
* [选择和配置语言模型](<https://github.com/docs/agent-customization/language-models.md>)
* [GitHub Copilot](<https://github.com/docs/agents/best-practices.md>)的最佳实践
* [GitHub Copilot 计划](<https://docs.github.com/en/copilot/get-started/plans>)
* [基于使用量的计费](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Optimize AI credit usage in {% data variables.product.prodname_vscode_shortname %}

Each GitHub Copilot plan includes a monthly allowance of [AI credits](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>). Different actions consume credits at different rates, based on the model and the number of tokens processed. This guide covers practical ways to get the most out of your AI credits in {% data variables.product.prodname_vscode %}.

## Choose models based on evidence

The lowest-cost model does not always produce the lowest-cost result. A model that needs retries or creates rework can cost more than a capable model that completes the task reliably. The inverse is also true: not every task requires a complex, reasoning model, and routing simple or repetitive tasks to a lighter model avoids paying for capability you don't need.

Start from the [AI model comparison table](<https://docs.github.com/en/copilot/reference/ai-models/model-comparison>) and [best practices for model selection](<https://github.com/docs/agents/best-practices.md#choose-the-right-model>) for general guidance, then use representative tasks from your own work to compare credit usage against response quality:

1. Define the expected result and how you will assess its quality.
1. Run each task more than once because model output is nondeterministic.
1. Compare task completion, output quality, reliability, duration, and credit consumption.
1. Choose the lowest-cost model that consistently meets your requirements, and reevaluate as models change.

For a low-maintenance default, use **auto model selection** to let {% data variables.product.prodname_vscode_shortname %} route each request to an efficient model based on task complexity, model health, and availability. For repeatable workflows, configure a preferred model in a [custom agent](<https://github.com/docs/agent-customization/custom-agents.md>).

The model picker in chat shows cost details in the hover menu, including cost per token type and a generic cost tier label (Low, Medium, High). Use the [Agent Debug Logs](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md>) to compare token usage, tool calls, errors, and duration across test runs.

For more information, see [choosing and configuring language models](<https://github.com/docs/agent-customization/language-models.md>).

## Plan and delegate before you implement

Jumping straight into code generation can waste credits if the approach is wrong. Separate planning, implementation, and review so that each phase uses an appropriate model:

1. Use the [Plan agent](<https://github.com/docs/agents/run/planning.md>) with a capable reasoning model to research the task and create a structured implementation plan.
1. Review and refine the plan before the agent writes code.
1. Divide the approved plan into well-scoped implementation tasks.
1. Delegate those tasks to [custom agents running as subagents](<https://github.com/docs/agents/run/subagents.md#run-a-custom-agent-as-a-subagent>). Configure each worker with a cost-effective model, focused instructions, and only the tools it needs.
1. Have the primary agent evaluate the results and resolve work that requires broader context or stronger reasoning.

This coordinator-and-worker pattern keeps the primary agent focused on planning and evaluation while lower-cost models perform bounded tasks. Delegation itself uses tokens, so keep small tasks in the primary session when isolation and a cheaper model would not offset the orchestration overhead.

For more information, see [plan first, then implement](<https://github.com/docs/agents/best-practices.md#plan-first-then-implement>).

## Use thinking effort defaults

Thinking effort controls how much reasoning a model applies to each request. Higher effort levels produce more thinking tokens, which increases both latency and credit consumption. {% data variables.product.prodname_vscode_shortname %} sets default effort levels based on evaluations and has adaptive reasoning enabled, where the model dynamically decides how much to think based on the complexity of each request.

For most tasks, the defaults are sufficient. Only increase thinking effort for genuinely complex problems like architectural planning or multi-step debugging.

For more information, see [configure thinking effort](<https://github.com/docs/agent-customization/language-models.md#configure-thinking-effort>).

## Start new chats for new tasks

As a conversation grows, it accumulates context from previous messages, tool outputs, and file contents. When you switch to an unrelated task in the same session, the model still processes all that irrelevant history, which consumes tokens without improving results.

Start a [new agent session](<https://github.com/docs/agents/run/sessions/manage-sessions.md>) (`kb(workbench.action.chat.newChat)`) when you change topics. This gives the model a clean context window focused on the current task.

## Fork conversations

When you want to explore an alternative approach or ask a side question, [fork the conversation](<https://github.com/docs/agents/run/sessions/manage-sessions.md#fork-a-chat-session>) instead of re-prompting from scratch. Forking creates a new session that inherits the existing conversation history, so you don't need to re-establish context.

* Type `/fork` in the chat input to fork the entire session up to the current message.
* Hover over a previous message and select **Fork Conversation** to fork from a specific checkpoint.

## Keep the tool catalog focused

Large tool catalogs increase the input context and make tool selection more complex. Tool calls also add their output to the [context window](<https://github.com/docs/agents/concepts/language-models.md#context-window>). Expose only the tools relevant to the current task:

* Use the **Configure Tools** button in the chat input field to select individual tools or MCP servers for the current request. Search the tools picker and add another tool when the task needs it.
* In [custom agents](<https://github.com/docs/agent-customization/custom-agents.md>), use the `tools` property to give each agent a narrow, task-specific tool set.

For organizations with a large internal tool catalog, consider providing a curated command-line interface for common workflows. The agent can invoke the commands through the terminal tool without loading every operation as a separate MCP tool. This approach requires your organization to maintain and secure the command-line interface.

For more information, see [use tools with agents](<https://github.com/docs/agents/run/tools.md>).

## Batch repetitive operations

Repeated submit, poll, and retrieve tool calls add intermediate results to the model context. For deterministic or bulk workflows, use a script or command-line tool to run the loop outside the agent conversation and return only the final result.

For example, instead of asking the agent to issue and inspect many similar database queries one at a time, use a reviewed script that runs the query batch and produces a concise summary. Run the script with the terminal tool, then give the summary to the model for analysis. Benchmark the scripted and interactive versions of your workflow because the savings depend on the tools, results, and task.

## Ground the agent before broad exploration

Relevant context helps an agent avoid failed searches, unnecessary tool calls, and incorrect changes. Before broad exploration:

* Make sure the [workspace semantic index](<https://github.com/docs/agents/reference/workspace-context.md#semantic-search>) is available, and use `#codebase` when you want to explicitly ground a request in the indexed codebase.
* Provide the relevant files, errors, constraints, and success criteria in the prompt.
* Use symbol-aware search and focused MCP or command-line tools for authoritative project and organization data.
* Add stable project conventions to [custom instructions](<https://github.com/docs/agent-customization/custom-instructions.md>) so the agent does not rediscover them in every session.

Grounding should narrow the search space, not preload every available source. Include the smallest set of authoritative context that lets the agent complete the task.

## Exclude files from chat context

Large generated files, build outputs, or irrelevant directories can increase token usage without adding value. Exclude these files to keep agent context focused:

* Use a `.gitignore` file to exclude files from the [workspace index](<https://github.com/docs/agents/reference/workspace-context.md#what-content-is-included-in-the-semantic-index>) and from agent text search and grep.
* Use the `setting(files.exclude)` setting to hide files from {% data variables.product.prodname_vscode_shortname %} entirely, which also excludes them from the index and agent search tools.
* Use the `setting(search.exclude)` setting to exclude files from agent text search and grep while keeping them visible in the Explorer, for example log files you want to open manually but not include in search results.

Search match snippets count toward the context window even when the agent doesn't open the matched file. Excluding noisy paths reduces irrelevant tokens in search results.

For more information and example configurations, see [improve agent search with exclusion settings](<https://github.com/docs/agents/reference/workspace-context.md#improve-agent-search-with-exclusion-settings>).

## Manage context with compaction

When a conversation grows long, use `/compact` to summarize older parts of the conversation and reclaim context window space. You can optionally add instructions to guide the summary, for example `/compact focus on the API design decisions`.

For more information, see [context compaction](<https://github.com/docs/agents/run/sessions/manage-sessions.md#compact-conversation-context>).

## Monitor your usage

You can monitor your AI credit usage in {% data variables.product.prodname_vscode_shortname %} for a specific chat request, the cumulative usage for a session, and your overall monthly consumption. This helps you understand which requests are more expensive, spot expensive conversations, and manage your usage over time.

To view the cost for a single request, hover over the chat response to see the credit consumption for that turn. This helps you understand which requests are more expensive and adjust your prompts or model selection accordingly.

To view the cumulative cost and token breakdown for the entire session, hover over or select the context window control in the chat input. The session info popover shows the total cost in credits and the cumulative context window token usage for the whole session, complementing the per-turn credit consumption already shown for individual requests.

![Screenshot of {% data variables.product.prodname_vscode_shortname %} {% data variables.copilot.chat_view %}, showing the context window usage control in the chat input box.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/chat/images/copilot-chat/chat-context-window-control.png>)

To view your overall monthly consumption, open the Copilot status dashboard from the {% data variables.product.prodname_vscode_shortname %} Status Bar. The dashboard shows the percentage of your monthly allowance you have used for AI credits (and inline suggestions for the {% data variables.copilot.copilot_free_short %} plan).

![Screenshot of the Copilot status dashboard, showing the percentage of monthly AI credits used and a link to view usage details on GitHub.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/optimize-usage-guide/copilot-status-dashboard.png>)

Visit the GitHub Copilot documentation for more information about [monitoring usage and entitlements](<https://docs.github.com/en/copilot/managing-copilot/monitoring-usage-and-entitlements/monitoring-your-copilot-usage-and-entitlements>).

You can also run the `/chronicle:cost-tips` command in any chat session to get personalized recommendations for optimizing your AI credit usage based on your recent activity. Learn more about [session insights and the chronicle command](<https://github.com/docs/agents/run/sessions/session-history.md#query-session-history-with-chronicle>).

## Inspect token usage and caching

Use the [Agent Debug Logs](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md>) to understand what is consuming credits in a session:

* The **Summary view** shows aggregate token usage for the session, including total tool calls and overall duration.
* The **[Cache Explorer view](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>)** shows prompt cache hit rates and how many input tokens were reused. Prompt caching lets model providers reuse the prefix of a request that matches a previous one, which reduces latency and token costs.

Use these measurements as an optimization loop:

1. Run a representative task and record its quality, reliability, credits, tokens, duration, tool calls, and errors.
1. Change one variable, such as the model, available tools, delegation strategy, or grounding source.
1. Repeat the task and compare the results.
1. Keep the configuration that meets your quality threshold at the lowest cost.

## Related content

* [AI credits and model costs](<https://github.com/docs/agents/concepts/language-models.md#ai-credits-and-model-costs>)
* [Choose and configure language models](<https://github.com/docs/agent-customization/language-models.md>)
* [Best practices for GitHub Copilot](<https://github.com/docs/agents/best-practices.md>)
* [GitHub Copilot plans](<https://docs.github.com/en/copilot/get-started/plans>)
* [Usage-based billing](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>)


</details>
