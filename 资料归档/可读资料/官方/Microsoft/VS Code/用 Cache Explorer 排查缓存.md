# 用 Cache Explorer 排查缓存

说明提示缓存原理、比较请求前缀、定位缓存未命中和保持上下文稳定的方法，用于理解重复请求成本。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方审核：[2026-10-07](<https://raw.githubusercontent.com/microsoft/vscode-docs/main/docs/agents/agent-troubleshooting/cache-explorer.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/microsoft/vscode-docs/main/docs/agents/agent-troubleshooting/cache-explorer.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/用 Cache Explorer 排查缓存.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 8b3d1c2f-6a94-4e7b-9f21-5c8d0a1e2b34
DateApproved: 10/7/2026
MetaDescription: Use the Cache Explorer view in {% data variables.product.prodname_vscode %} to diagnose prompt cache misses and reduce token cost and latency in AI chat sessions.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- prompt caching
- cache explorer
- cache hit rate
- token usage
- agent debug logs
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 使用缓存资源管理器诊断提示缓存

某些语言模型提供程序可以重用先前请求中未更改的前缀，而不是再次处理这些输入标记。缓存资源管理器会比较聊天会话中的连续模型请求，以帮助您调查对该前缀的更改是否会导致更高的延迟或令牌使用。

在 Agent 调试日志摘要视图报告低缓存重用以及意外的持续时间或输入令牌使用情况后，使用缓存资源管理器。对于一般 agent 故障、缺少上下文或工具错误，请从 [调试聊天交互 ](<https://raw.githubusercontent.com/docs/agents/agent-troubleshooting/chat-debug-view.md>) 开始。

## 了解提示缓存

提示缓存取决于提供者和模型。当它受支持时，提供程序可能会重用与缓存内容匹配的请求的开头。请求早期的更改可以减少可重用的前缀的数量。

缓存资源管理器显示连续请求之间的差异并识别它们的第一个差异。差异有助于解释报告的缓存指标，但它不能证明模型提供者如何处理或计费请求。

> [！IMPORTANT]
> 请勿仅为了增加缓存重用而删除必要的指令、工具或上下文。响应质量和任务正确性优先于缓存指标。

## 开始之前

为了使比较有用：

* 使用至少包含两个模型请求的会话。
* 打开 Agent 调试日志摘要视图，并确认对于也具有意外持续时间或输入令牌使用情况的请求，缓存重用率较低。
* 比较来自同一会话的请求。验证更改时保持模型和任务具有可比性。

如果“摘要”视图不报告缓存信息，则所选模型或提供程序可能不会公开它。使用日志视图来调查请求持续时间、错误和工具调用。

## 打开缓存浏览器

1. 通过选择 {% data variables.copilot.chat_view %} 中的省略号 (**...**) 菜单并选择 ** 显示 Agent 调试日志 **，打开 Agent 调试日志面板。
1. 选择面包屑中的会话描述以打开“摘要”视图。
1. 选择**缓存资源管理器**。

![显示 Agent 日志中的缓存资源管理器视图的屏幕截图，并排显示两个模型请求的差异。](<https://raw.githubusercontent.com/microsoft/vscode-docs//docs/agents/images/cache-explorer/cache-explorer.png>)

## 阅读比较

侧面板列出了按用户请求分组的型号。每回合都可以显示缓存命中百分比、持续时间、模型名称和时间戳。选择一个回合，将其请求与前一个请求进行比较。

主要区域包含：

* **缓存性能**：报告的缓存百分比和重用输入令牌的数量。
* **提示签名**：请求组件的摘要，例如系统指令、工具定义和消息。第一个分歧标记连续请求停止匹配的位置。
* **组件**：请求组件的可扩展文本差异。

如果轮次没有先前的模型请求，则没有连续的请求可供缓存浏览器进行比较。

## 调查缓存未命中

1. 选择报告的缓存重用率较低且持续时间或输入令牌使用情况意外的回合。
1. 找到提示签名中的第一个分歧。
1. 展开该组件并确定发生了什么变化。
1. 确定更改是否必要且在您的控制范围内。
1. 进行一项合理的调整。
1. 使用调整后的配置启动新会话，并重复相同的两个请求序列。
1. 比较第二个请求的缓存指标、持续时间、输入令牌使用情况和响应质量。仅当整体结果有所改善时才保留调整。

例如，假设第一个分歧出现在工具定义中，因为您在轮次之间启用了 MCP 服务器。如果任务需要服务器，则保留它并接受较低的重用。如果不需要，请在开始新会话之前选择您的工具，并重复相同的两个请求序列。 diff 的变化是有关请求结构的证据，而不是缓存导致所有观察到的延迟的证据。

## 保持请求上下文稳定

以下做法可以减少轮次之间不必要的变化：

* **开始之前选择会话选项**：如果可能，在第一个请求之前选择模型、推理工作、上下文大小和所需的工具。
* **保持共享指令稳定**：在比较回合时避免编辑指令文件或自定义agent定义。有关指导，请参阅 [ 上下文工程指南 ](<https://raw.githubusercontent.com/docs/agents/guides/context-engineering-guide.md>)。
* **仅附加相关上下文**：重复添加或替换附件会更改请求。优先选择任务所需的重点上下文。
* **隔离独立研究**：将[子代理](<https://raw.githubusercontent.com/docs/agents/run/subagents.md>)用于单独的研究任务，同时将探索排除在父对话之外可以提高清晰度。
* **有意重置长对话**：当累积的历史记录不再对任务有帮助时，开始新会话或使用`/compact`。

一些请求组件由{% data variables.product.prodname_vscode_shortname %}、扩展或模型提供者组装，并且不能直接控制。如果第一个分歧超出了您的配置，请使用比较作为诊断证据，而不是尝试围绕它重写提示。

## 相关资源

* [调试聊天交互](<https://raw.githubusercontent.com/docs/agents/agent-troubleshooting/chat-debug-view.md>)
* [优化您的AI使用情况](<https://raw.githubusercontent.com/docs/agents/guides/optimize-usage.md>)
* [上下文工程指南](<https://raw.githubusercontent.com/docs/agents/guides/context-engineering-guide.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Diagnose prompt caching with the Cache Explorer

Some language model providers can reuse an unchanged prefix from an earlier request instead of processing those input tokens again. The Cache Explorer compares consecutive model requests in a chat session to help you investigate whether changes to that prefix contribute to higher latency or token usage.

Use the Cache Explorer after the Agent Debug Logs Summary view reports low cache reuse together with unexpected duration or input token usage. For general agent failures, missing context, or tool errors, start with [Debug chat interactions](<https://raw.githubusercontent.com/docs/agents/agent-troubleshooting/chat-debug-view.md>).

## Understand prompt caching

Prompt caching is provider and model dependent. When it is supported, the provider might reuse the beginning of a request that matches cached content. A change early in the request can reduce how much of that prefix is reusable.

The Cache Explorer shows a diff between consecutive requests and identifies their first divergence. The diff helps explain a reported cache metric, but it does not prove how the model provider processed or billed the request.

> [!IMPORTANT]
> Do not remove necessary instructions, tools, or context only to increase cache reuse. Response quality and task correctness take priority over the cache metric.

## Before you start

To make the comparison useful:

* Use a session with at least two model requests.
* Open the Agent Debug Logs Summary view and confirm that cache reuse is low for a request that also has unexpected duration or input token usage.
* Compare requests from the same session. Keep the model and task comparable when you verify a change.

If the Summary view does not report cache information, the selected model or provider might not expose it. Use the Logs view to investigate request duration, errors, and tool calls instead.

## Open the Cache Explorer

1. Open the Agent Debug Logs panel by selecting the ellipsis (**...**) menu in the {% data variables.copilot.chat_view %} and selecting **Show Agent Debug Logs**.
1. Select the session description in the breadcrumb to open the Summary view.
1. Select **Cache Explorer**.

![Screenshot showing the Cache Explorer view in Agent Logs, with a side-by-side diff of two model requests.](<https://raw.githubusercontent.com/microsoft/vscode-docs//docs/agents/images/cache-explorer/cache-explorer.png>)

## Read the comparison

The side panel lists model turns grouped by user request. Each turn can show the cache hit percentage, duration, model name, and timestamp. Select a turn to compare its request with the preceding request.

The main area contains:

* **Cache performance**: The reported cache percentage and the number of reused input tokens.
* **Prompt signature**: A summary of request components, such as system instructions, tool definitions, and messages. The first divergence marks where the consecutive requests stop matching.
* **Components**: Expandable text diffs for the request components.

If a turn has no preceding model request, there is no consecutive request for the Cache Explorer to compare.

## Investigate a cache miss

1. Select a turn with low reported cache reuse and unexpected duration or input token usage.
1. Find the first divergence in the prompt signature.
1. Expand that component and identify what changed.
1. Decide whether the change was necessary and under your control.
1. Make one justified adjustment.
1. Start a new session with the adjusted configuration and repeat the same two-request sequence.
1. Compare the second request's cache metric, duration, input token usage, and response quality. Keep the adjustment only if the overall result improves.

For example, suppose the first divergence is in the tool definitions because you enabled an MCP server between turns. If the server is required for the task, keep it and accept the lower reuse. If it is not required, choose your tools before starting a new session and repeat the same two-request sequence. A change in the diff is evidence about the request structure, not proof that caching caused all observed latency.

## Keep request context stable

The following practices can reduce unnecessary changes between turns:

* **Choose session options before starting**: Select the model, reasoning effort, context size, and required tools before the first request when possible.
* **Keep shared instructions stable**: Avoid editing instructions files or custom agent definitions while comparing turns. For guidance, see the [context engineering guide](<https://raw.githubusercontent.com/docs/agents/guides/context-engineering-guide.md>).
* **Attach only relevant context**: Repeatedly adding or replacing attachments changes the request. Prefer focused context that the task needs.
* **Isolate independent research**: Use a [subagent](<https://raw.githubusercontent.com/docs/agents/run/subagents.md>) for a separate research task when keeping that exploration out of the parent conversation improves clarity.
* **Reset long conversations intentionally**: Start a new session or use `/compact` when accumulated history no longer helps the task.

Some request components are assembled by {% data variables.product.prodname_vscode_shortname %}, extensions, or the model provider and are not directly controllable. If the first divergence is outside your configuration, use the comparison as diagnostic evidence rather than attempting to rewrite your prompt around it.

## Related resources

* [Debug chat interactions](<https://raw.githubusercontent.com/docs/agents/agent-troubleshooting/chat-debug-view.md>)
* [Optimize your AI usage](<https://raw.githubusercontent.com/docs/agents/guides/optimize-usage.md>)
* [Context engineering guide](<https://raw.githubusercontent.com/docs/agents/guides/context-engineering-guide.md>)


</details>
