# 调试 VS Code 聊天交互

通过日志、流程图、缓存与聊天调试视图定位文件忽略、工具未调用、回复中断和指令失效，并检查修正是否生效。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/agent-troubleshooting/chat-debug-view.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/agent-troubleshooting/chat-debug-view.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/调试 VS Code 聊天交互.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 2f4a8e9d-3c5b-4f6e-a7d8-1c2b3e4f5a6b
DateApproved: 10/7/2026
MetaDescription: Diagnose agent behavior in {% data variables.product.prodname_vscode %} by inspecting requests, context, tool calls, token usage, and agent flows.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 调试聊天交互

当 agent 行为异常时，请在更改提示或配置之前检查受影响聊天会话的证据。本文帮助您选择诊断视图、跟踪发生的情况并验证更改是否可以解决问题。

## 选择诊断工具

{% data variables.product.prodname_vscode_shortname %}针对不同问题提供了补充工具：

|问题|工具|
|----------|------|
|发生了什么，发生的顺序是什么？|使用 Agent 调试日志面板中的 [ 日志视图 ](<#日志视图>)。|
|会话花费了多长时间，使用了多少令牌，是否存在错误？|使用[摘要视图](<#摘要视图>)。|
|agents 和子代理如何交互？|使用[Agent流程图视图](<#agent流程图查看>)。|
|向模型发送了哪些提示、上下文和工具？|使用 [聊天调试视图 ](<#聊天调试视图>)。|
|请求之间的更改是否减少了报告的缓存重用？|使用[缓存浏览器](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>)。|
|AI可以为我总结捕获的事件吗？|[附加调试事件以聊天或使用`/troubleshoot`](<#通过聊天分析调试事件>)。|

对于帐户、连接或功能可用性问题，请从 [故障排除 AI 在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md>) 相反。

## 开始之前

> [！NOTE]
> Agent 调试日志面板处于预览状态。

在重现问题之前准备日志记录：

* 对于主 {% data variables.product.prodname_vscode_shortname %} 窗口中的本地聊天会话，请启用实验性 `setting(github.copilot.chat.agentDebugLog.fileLogging.enabled)` 设置并在使用 `/troubleshoot` 之前重新加载窗口。
* 对于在 [Agent 主机 ](<https://github.com/docs/agents/concepts/agent-host.md>) 上运行的会话，请在要检查的活动之前启用实验性 `setting(chat.agentHost.agentDebugLog.enabled)` 设置。
* 重现问题并记下受影响的会话。捕获不具有追溯力。

> [!CAUTION]
> 调试视图和导出的日志可以包含提示、源代码、文件路径、工具输入和输出以及其他敏感数据。在共享此内容之前请先查看该内容，并且切勿包含凭据或机密。

## 调查常见场景

### AI 忽略工作区文件

如果响应是通用的或者不使用预期的代码：

1. 打开“聊天调试”视图并检查 **Context** 部分中受影响的请求。
1. 如果文件不存在，请确认您打开了正确的工作区，并且 [ 工作区索引 ](<https://github.com/docs/agents/reference/workspace-context.md>) 可用。
1. 将相关文件或代码添加为显式 [ 聊天上下文 ](<https://github.com/docs/chat/copilot-chat-context.md>)，然后重复请求。
1. 重新打开“聊天调试”视图并验证是否存在预期的上下文。

### 未调用 MCP 工具

如果模型没有调用预期的工具：

1. 运行 **MCP：从命令面板列出服务器 ** 并验证服务器是否正在运行。
1. 打开“聊天调试”视图并检查该工具是否出现在可用工具定义中。
1. 如果该工具不存在，请[对MCP服务器](<https://github.com/docs/agent-customization/mcp-servers.md#troubleshoot-and-debug-mcp-servers>)进行故障排除。
1. 如果该工具可用，请检查 Agent 调试日志以了解工具调用和错误。
1. 在提示中说明工具名称及其应执行的操作，然后重复该请求。

### AI 响应不完整

如果响应提前停止或忽略部分任务：

1. 检查 Agent 调试日志以了解模型请求错误和令牌使用情况。
1. 检查“聊天调试”视图中的 **响应** 和 **工具响应** 部分，以确定模型响应或工具结果是否不完整。
1. 如果对话包含不相关或过时的上下文，请使用 `/compact` 或启动新会话并重复请求。
1. 如果日志显示错误，请在您[报告问题](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#report-an-unresolved-issue>)时包含该错误。

### 不应用说明或提示文件

自定义指令和提示文件以不同的方式输入请求：

1. 打开[聊天自定义诊断视图](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#check-customization-diagnostics>)检查是否发现指令文件以及是否有错误。
1. 对于 `*.instructions.md` 文件，验证其 `applyTo` 模式是否与您正在处理的文件匹配。
1. 对于提示文件，请验证您是否手动调用了它。在 Agent 主机上运行的 Agents 不使用提示文件。详情请参见[使用提示文件](<https://github.com/docs/agent-customization/prompt-files.md>)。
1. 检查 Agent 调试日志中的 **Discovery** 事件，查看自定义是否已加载、跳过或拒绝。
1. 在聊天调试视图中，检查 ** 系统提示 ** 中的自定义指令，以及 ** 用户提示 ** 中调用的提示文件。
1. 纠正发现或适用性问题，重复请求，再次验证相应的提示内容。

## Agent 调试日志面板

Agent 调试日志面板显示聊天会话的时间记录和聚合指标。它还提供了 agent 编排和访问缓存浏览器的流程图。

通过以下任一方式打开面板：

* 选择{% data variables.copilot.chat_view %}中的省略号(**...**)菜单，然后选择**显示Agent调试日志**。
* 运行 ** 开发人员：从命令面板打开 Agent 调试日志 **。

该小组包括当前和历史会议。持久日志存储在本地。

### 日志视图

日志视图列出了会话事件及其时间戳、类型和摘要。展开事件以检查详细信息，例如模型请求的系统提示或工具调用的输入和输出。

![显示 Agent 调试日志面板中的事件的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-debug-view/agent-logs.png>)

在平面列表和按子代理分组的树之间切换。使用过滤器重点关注模型请求、工具调用、发现或错误等事件。

### 摘要视图

摘要视图报告聚合信息，例如工具调用、令牌使用情况、错误和总持续时间。使用这些指标来确定会话的哪一部分需要更深入的调查。

![显示 Agent 调试日志摘要视图中聚合会话指标的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-debug-view/agent-logs-summary-v3.png>)

要打开摘要视图：

1. 打开受影响会话的 Agent 调试日志面板。
1. 在面板顶部的面包屑中选择会话描述。

在摘要视图中，选择**查看日志**、**Agent流程图**或**缓存浏览器**以打开相应的视图。

### Agent流程图查看

Agent 流程图视图可视化 agents 和子代理之间的交互顺序。使用它来识别哪个 agent 委托了操作以及工作流程停止的位置。

![显示 agent 和 Agent 流程图中的子代理交互的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-debug-view/agent-flow-chart-v2.png>)

打开流程图：

1. 打开受影响会话的摘要视图。
1. 选择**Agent流程图**。
1. 平移或缩放图表，然后选择一个节点来检查该事件。

### 缓存浏览器视图

缓存资源管理器比较连续的模型请求并标记它们的第一个分歧。仅在“摘要”视图报告低缓存重用以及意外的持续时间或输入令牌使用情况后才使用它。

![显示两个模型请求的缓存资源管理器比较的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/cache-explorer/cache-explorer.png>)

有关解释和验证步骤，请参阅[使用缓存资源管理器诊断提示缓存](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>)。

### 通过聊天分析调试事件

当您希望 AI 回答有关令牌使用、加载的自定义项、工具调用、错误或持续时间的重点问题时，请附加当前会话事件的快照以进行聊天。

1. 打开受影响会话的日志视图。
1. 选择 Agent 调试日志面板右上角的闪烁图标。
1. 提出有关附加事件的重点问题。

或者，输入 `/troubleshoot`，然后输入问题。例如：

```prompt
/troubleshoot Which customizations were loaded, and did any fail?
```

对于 {% data variables.copilot.agents_window %} 中的会话，输入 `/troubleshoot #session`，选择本地或远程会话，然后添加您的问题。

### 导出和导入会话

当您需要保留证据或使用其他支持的工具对其进行分析时，将会话导出为 OTLP 格式的 OpenTelemetry JSON 文件。

导出会话：

1. 打开 Agent 调试日志面板并选择会话。
1. 选择右上角工具栏中的**导出**图标。
1. 选择 JSON 文件的位置。
1. 在共享之前查看导出的内容。

要检查以前导出的会话：

1. 选择右上角工具栏中的**导入**图标。
1. 选择导出的 Agent 调试日志 JSON 文件。
1. 使用“摘要”和“日志”视图检查导入的会话。

## 聊天调试视图

聊天调试视图显示每个模型交互的请求和响应详细信息。使用它来验证哪些指令、上下文、消息和工具定义到达了模型。

### 打开聊天调试视图

通过以下任一方式打开视图：

* 选择{% data variables.copilot.chat_view %}中的溢出菜单，然后选择**显示聊天调试视图**。
* 运行 **开发人员：从命令面板显示聊天调试视图**。

![显示聊天调试视图中的提示、上下文和响应详细信息的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-debug-view/chat-debug-view.png>)

### 读取调试输出

每个交互都包含可扩展的部分：

|部分|它显示了什么|要检查什么|
|---------|---------------|---------------|
|**系统提示**|定义模型的行为、功能和约束的指令。|验证是否出现预期的自定义指令和 agent 定义。|
|**用户提示**|发送到模型的提示。|确认提示文本和解析的引用存在。|
|**上下文**|文件、符号和其他附加上下文。|检查任务所需的上下文是否存在且相关。|
|**回复**|模型返回的响应。|确定模型是否返回完整内容或错误。|
|**工具响应**|请求期间调用的工具的输入和输出。|验证每个工具是否收到了预期的输入并返回了可用的结果。|

## 验证诊断

确定可能的原因后：

1. 更改一项相关条件，例如文件模式、服务器配置、上下文附件或工具选择。
1. 重复类似的请求。
1. 检查相同的事件或请求部分。
1. 确认诊断证据和用户可见结果均按预期发生变化。

如果证据没有改变，请恢复以前的配置并调查下一个可能的原因。

## 相关资源

* [对 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md>) 中的 AI 进行故障排除
* [添加聊天上下文](<https://github.com/docs/chat/copilot-chat-context.md>)
* [使用AI](<https://github.com/docs/agents/run/security.md>)的安全注意事项


## 原文对照

<details>
<summary>展开完整原文</summary>

# Debug chat interactions

When an agent behaves unexpectedly, inspect the evidence from the affected chat session before changing your prompt or configuration. This article helps you select a diagnostic view, trace what happened, and verify whether a change resolves the issue.

## Choose a diagnostic tool

{% data variables.product.prodname_vscode_shortname %} provides complementary tools for different questions:

| Question | Tool |
|----------|------|
| What happened, and in what order? | Use the [Logs view](<#logs-view>) in the Agent Debug Logs panel. |
| How long did the session take, how many tokens did it use, and were there errors? | Use the [Summary view](<#summary-view>). |
| How did agents and subagents interact? | Use the [Agent flow chart view](<#agent-flow-chart-view>). |
| What prompt, context, and tools were sent to the model? | Use the [Chat Debug view](<#chat-debug-view>). |
| Did changes between requests reduce reported cache reuse? | Use the [Cache Explorer](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>). |
| Can the AI summarize the captured events for me? | [Attach debug events to chat or use `/troubleshoot`](<#analyze-debug-events-with-chat>). |

For account, connectivity, or feature-availability problems, start with [Troubleshoot AI in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md>) instead.

## Before you start

> [!NOTE]
> The Agent Debug Logs panel is in Preview.

Prepare logging before you reproduce the issue:

* For local chat sessions in the main {% data variables.product.prodname_vscode_shortname %} window, enable the experimental `setting(github.copilot.chat.agentDebugLog.fileLogging.enabled)` setting and reload the window before you use `/troubleshoot`.
* For sessions that run on the [Agent Host](<https://github.com/docs/agents/concepts/agent-host.md>), enable the experimental `setting(chat.agentHost.agentDebugLog.enabled)` setting before the activity that you want to inspect.
* Reproduce the issue and note the affected session. Capture is not retroactive.

> [!CAUTION]
> Debug views and exported logs can contain prompts, source code, file paths, tool inputs and outputs, and other sensitive data. Review this content before sharing it, and never include credentials or secrets.

## Investigate common scenarios

### The AI ignores workspace files

If the response is generic or does not use the expected code:

1. Open the Chat Debug view and inspect the **Context** section for the affected request.
1. If a file is absent, confirm that you opened the correct workspace and that [workspace indexing](<https://github.com/docs/agents/reference/workspace-context.md>) is available.
1. Add the relevant file or code as explicit [chat context](<https://github.com/docs/chat/copilot-chat-context.md>), and then repeat the request.
1. Reopen the Chat Debug view and verify that the expected context is present.

### An MCP tool is not being invoked

If the model does not call an expected tool:

1. Run **MCP: List Servers** from the Command Palette and verify that the server is running.
1. Open the Chat Debug view and check whether the tool appears in the available tool definitions.
1. If the tool is absent, [troubleshoot the MCP server](<https://github.com/docs/agent-customization/mcp-servers.md#troubleshoot-and-debug-mcp-servers>).
1. If the tool is available, check the Agent Debug Logs for tool calls and errors.
1. State the tool name and the action it should perform in your prompt, and then repeat the request.

### The AI response is incomplete

If a response stops early or omits part of the task:

1. Check the Agent Debug Logs for model request errors and token usage.
1. Check the **Response** and **Tool responses** sections in the Chat Debug view to determine whether the model response or a tool result is incomplete.
1. If the conversation contains unrelated or outdated context, use `/compact` or start a new session and repeat the request.
1. If the logs show an error, include that error when you [report the issue](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#report-an-unresolved-issue>).

### Instructions or a prompt file are not applied

Custom instructions and prompt files enter a request in different ways:

1. Open the [chat customization diagnostics view](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#check-customization-diagnostics>) to check whether an instructions file was discovered and whether it has errors.
1. For a `*.instructions.md` file, verify that its `applyTo` pattern matches the file you are working on.
1. For a prompt file, verify that you invoked it manually. Agents that run on the Agent Host do not use prompt files. For details, see [Use prompt files](<https://github.com/docs/agent-customization/prompt-files.md>).
1. Check **Discovery** events in the Agent Debug Logs to see whether a customization was loaded, skipped, or rejected.
1. In the Chat Debug view, inspect the **System prompt** for custom instructions and the **User prompt** for an invoked prompt file.
1. Correct the discovery or applicability issue, repeat the request, and verify the corresponding prompt content again.

## Agent Debug Logs panel

The Agent Debug Logs panel shows a chronological record and aggregate metrics for chat sessions. It also provides a flow chart for agent orchestration and access to the Cache Explorer.

Open the panel in either of these ways:

* Select the ellipsis (**...**) menu in the {% data variables.copilot.chat_view %}, and then select **Show Agent Debug Logs**.
* Run **Developer: Open Agent Debug Logs** from the Command Palette.

The panel includes current and historical sessions. Persisted logs are stored locally.

### Logs view

The Logs view lists session events with their timestamp, type, and summary. Expand an event to inspect details such as the system prompt for a model request or the input and output for a tool call.

![Screenshot showing events in the Agent Debug Logs panel.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-debug-view/agent-logs.png>)

Switch between a flat list and a tree grouped by subagent. Use the filters to focus on events such as model requests, tool calls, discovery, or errors.

### Summary view

The Summary view reports aggregate information such as tool calls, token usage, errors, and total duration. Use these metrics to identify which part of a session needs deeper investigation.

![Screenshot showing aggregate session metrics in the Agent Debug Logs Summary view.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-debug-view/agent-logs-summary-v3.png>)

To open the Summary view:

1. Open the Agent Debug Logs panel for the affected session.
1. Select the session description in the breadcrumb at the top of the panel.

From the Summary view, select **View Logs**, **Agent Flow Chart**, or **Cache Explorer** to open the corresponding view.

### Agent flow chart view

The Agent flow chart view visualizes the sequence of interactions between agents and subagents. Use it to identify which agent delegated an action and where a workflow stopped.

![Screenshot showing agent and subagent interactions in the Agent Flow Chart.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-debug-view/agent-flow-chart-v2.png>)

To open the flow chart:

1. Open the Summary view for the affected session.
1. Select **Agent Flow Chart**.
1. Pan or zoom the chart, and select a node to inspect that event.

### Cache Explorer view

The Cache Explorer compares consecutive model requests and marks their first divergence. Use it only after the Summary view reports low cache reuse together with unexpected duration or input token usage.

![Screenshot showing the Cache Explorer comparison for two model requests.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/cache-explorer/cache-explorer.png>)

For interpretation and verification steps, see [Diagnose prompt caching with the Cache Explorer](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>).

### Analyze debug events with chat

Attach a snapshot of the current session events to chat when you want the AI to answer a focused question about token usage, loaded customizations, tool calls, errors, or duration.

1. Open the Logs view for the affected session.
1. Select the sparkle icon in the upper-right corner of the Agent Debug Logs panel.
1. Ask a focused question about the attached events.

Alternatively, enter `/troubleshoot` followed by a question. For example:

```prompt
/troubleshoot Which customizations were loaded, and did any fail?
```

For a session in the {% data variables.copilot.agents_window %}, enter `/troubleshoot #session`, select the local or remote session, and then add your question.

### Export and import sessions

Export a session to an OpenTelemetry JSON file in OTLP format when you need to retain the evidence or analyze it with another supported tool.

To export a session:

1. Open the Agent Debug Logs panel and select the session.
1. Select the **Export** icon in the upper-right toolbar.
1. Choose a location for the JSON file.
1. Review the exported content before sharing it.

To inspect a previously exported session:

1. Select the **Import** icon in the upper-right toolbar.
1. Select an exported Agent Debug Log JSON file.
1. Use the Summary and Logs views to inspect the imported session.

## Chat Debug view

The Chat Debug view shows the request and response details for each model interaction. Use it to verify which instructions, context, messages, and tool definitions reached the model.

### Open the Chat Debug view

Open the view in either of these ways:

* Select the overflow menu in the {% data variables.copilot.chat_view %}, and then select **Show Chat Debug View**.
* Run **Developer: Show Chat Debug View** from the Command Palette.

![Screenshot showing the prompt, context, and response details in the Chat Debug view.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-debug-view/chat-debug-view.png>)

### Read the debug output

Each interaction contains expandable sections:

| Section | What it shows | What to check |
|---------|---------------|---------------|
| **System prompt** | Instructions that define the model's behavior, capabilities, and constraints. | Verify that expected custom instructions and agent definitions appear. |
| **User prompt** | The prompt sent to the model. | Confirm that prompt text and resolved references are present. |
| **Context** | Files, symbols, and other attached context. | Check whether the task's required context is present and relevant. |
| **Response** | The response returned by the model. | Determine whether the model returned complete content or an error. |
| **Tool responses** | Inputs and outputs for tools invoked during the request. | Verify that each tool received the expected input and returned a usable result. |

## Verify the diagnosis

After you identify a likely cause:

1. Change one relevant condition, such as a file pattern, server configuration, context attachment, or tool selection.
1. Repeat a comparable request.
1. Inspect the same event or request section.
1. Confirm that both the diagnostic evidence and the user-visible result changed as expected.

If the evidence does not change, restore the previous configuration and investigate the next likely cause.

## Related resources

* [Troubleshoot AI in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md>)
* [Add context to chat](<https://github.com/docs/chat/copilot-chat-context.md>)
* [Security considerations for using AI](<https://github.com/docs/agents/run/security.md>)


</details>
