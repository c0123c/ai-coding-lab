# 排查 VS Code AI 功能问题

从症状选择基础检查、定制诊断、聊天日志、网络和 MCP 检查，说明收集证据及报告问题的方式。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/agent-troubleshooting/troubleshooting.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/agent-troubleshooting/troubleshooting.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/排查 VS Code AI 功能问题.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: f8e4b2c1-9d3a-4e5f-b6c7-8a9d0e1f2b3c
DateApproved: 10/7/2026
MetaDescription: Diagnose GitHub Copilot and agent issues in {% data variables.product.prodname_vscode %} with targeted checks, logs, and network diagnostics.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- ai
- copilot
- troubleshooting
- diagnostics
- logs
- debugging
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 对 {% data variables.product.prodname_vscode %} 中的 AI 进行故障排除

当聊天、agents 或内联建议未按预期运行时，请从您看到的症状开始。本文可帮助您选择正确的诊断工具、解释结果并在需要报告问题时收集有用的证据。

## 选择故障排除路径

|症状|从这里开始|下一步|
|---------|------------|-----------|
|聊天或 agents 不可用|检查[聊天和agent要求](<https://github.com/docs/agents/agent-troubleshooting/faq.md#chat-features-arent-working-for-me>)。|确认您的帐户、计划、设置和组织策略。|
|内嵌建议不出现|检查[内联建议要求](<https://github.com/docs/agents/agent-troubleshooting/faq.md#inline-suggestions-are-not-working-in-the-editor>)。|检查Copilot日志是否满足要求。|
|Copilot 无法连接或登录|[收集网络诊断信息](<#收集网络诊断信息>)。|检查您的防火墙、代理或 VPN 配置。|
|指令被忽略|[检查自定义诊断](<#检查定制诊断>)。|检查会话以验证哪些指令到达了模型。|
|响应中缺少文件、工具或预期步骤|[检查聊天交互](<#检查聊天互动>)。|将会话事件与发送到模型的请求进行比较。|
|响应速度慢或使用的令牌比预期多|打开 Agent 调试日志摘要视图。|仅当会话报告缓存重用率较低时才使用缓存资源管理器。|
|MCP 服务器或工具不可用|[对 MCP 服务器 ](<#对-mcp-服务器进行故障排除>) 进行故障排除。|检查服务器输出和可用于请求的工具。|

## 从基本检查开始

在收集详细日志之前：

1. 运行 ** 代码：从命令面板检查更新 ** 以更新 {% data variables.product.prodname_vscode_shortname %}。
1. 确认您的访问方法。使用具有 Copilot 套餐的 GitHub 帐户登录，或选择已配置的自带密钥模型以获取支持的聊天功能。某些功能需要 GitHub Copilot 服务。详情参见【[GitHub Copilot】订阅问题【](<https://github.com/docs/agents/agent-troubleshooting/faq.md#github-copilot-subscription>)】。
1. 打开状态栏中的Copilot状态仪表板，检查您是否已达到限额。临时费率限制与您的每月津贴是分开的。
1. 检查工作区设置或组织策略是否关闭受影响的功能。有关 agent 的可用性，请参阅 [Agents 在聊天中不可用 ](<https://github.com/docs/agents/agent-troubleshooting/faq.md#agents-are-not-available-in-chat>)。
1. 在新的聊天会话中重现该问题。比较两次运行时，请保持提示、模型和可用工具一致。

## 检查定制诊断

当说明文件似乎不适用时，请使用聊天自定义诊断视图。

1. 打开{% data variables.copilot.chat_view %}。
1. 在 {% data variables.copilot.chat_view %} 中，打开上下文菜单并选择 ** 诊断 **。
1. 查看发现的指令文件和任何报告的错误。

使用结果来选择下一步操作：

* **文件丢失**：验证其文件名、位置以及控制指令发现的设置。
* **文件有错误**：修复报告的前言或内容错误，然后重新打开诊断视图。
* **文件已加载但未应用**：对于 `*.instructions.md` 文件，验证其 `applyTo` 模式是否与您正在处理的文件匹配。另请检查聊天响应的 ** 参考文献 ** 部分，以查看使用了哪些指令。
* **请求中缺少提示文件**：在聊天中手动调用提示文件。提示文件不使用`applyTo`，并且在Agent主机上运行的agents不使用提示文件。

有关文件位置、适用性规则和设置，请参阅[使用自定义指令](<https://github.com/docs/agent-customization/custom-instructions.md>)。要验证发送到模型的确切指令，请使用 [聊天调试视图 ](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md#chat-debug-view>)。

## 检查聊天互动

当基本检查通过但 agent 行为异常时，请使用以下工具：

|工具|用它来|
|------|-----------|
| `/troubleshoot` |要求 AI 分析捕获的会话事件并回答特定问题。|
|**Agent 调试日志** 面板（预览）|遵循模型请求、工具调用、自定义发现、错误和 agent 活动的顺序。|
|**聊天调试视图**|检查请求的确切系统提示、用户提示、上下文、工具定义和工具负载。|
|**缓存浏览器**|在会话指标表明缓存重用率较低后，比较连续的模型请求。|

在重现问题之前准备会话：

* 对于主 {% data variables.product.prodname_vscode_shortname %} 窗口中的本地聊天会话，请启用实验性 `setting(github.copilot.chat.agentDebugLog.fileLogging.enabled)` 设置并在使用 `/troubleshoot` 之前重新加载窗口。
* 对于在 [Agent 主机 ](<https://github.com/docs/agents/concepts/agent-host.md>) 上运行的会话，请在要检查的活动之前启用实验性 `setting(chat.agentHost.agentDebugLog.enabled)` 设置。捕获不具有追溯力。
* 重现问题，然后在打开日志或使用 `/troubleshoot` 时选择该会话。

要通过聊天分析 Agent 主持人会话：

1. 打开{% data variables.copilot.agents_window %}。
1. 在聊天输入中输入`/troubleshoot #session`。
1. 选择包含问题的本地或远程会话。
1. 添加重点问题，例如 `which tool failed and what error did it return?`，然后发送请求。

有关详细过程和基于场景的指导，请参阅[调试聊天交互](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md>)。

## 查看 GitHub Copilot 的日志

GitHub Copilot输出通道记录连接失败、扩展错误和其他运行时信息。仅在重现问题时才提高日志级别，因为跟踪日志记录会生成详细的输出。

1. 打开命令面板（`kb(workbench.action.showCommands)`）。
1. 运行**开发人员：设置日志级别**并将GitHub Copilot和{% data variables.copilot.copilot_chat %}扩展设置为**Trace**。
1. 重现问题。
1. 运行**输出：显示输出通道**并选择**GitHub Copilot**或**{% data variables.copilot.copilot_chat %}**。
1. 查看操作失败期间的错误和警告。
1. 运行**开发者：再次设置日志级别**并恢复之前的日志级别。

要切换输出通道，请使用输出面板右侧的下拉菜单。

## 收集网络诊断信息

如果 Copilot 无法连接，请收集诊断信息以识别失败的连接检查以及相关的代理或网络配置。

1. 打开命令面板（`kb(workbench.action.showCommands)`）。
1. 运行**GitHub Copilot：聊天诊断**。
1. 查看在编辑器中打开的诊断报告。

如果检查失败，请将结果与您的防火墙、代理和 VPN 配置进行比较。没有失败检查的报告仅排除报告涵盖的连接，因此如果问题仍然存在，请继续使用 Copilot 输出通道。

有关所需的域和特定于网络的指南，请参阅 [Copilot](<https://github.com/docs/agents/agent-troubleshooting/faq.md#network-and-firewall-configuration-for-copilot>) 的网络和防火墙配置。

## 对 MCP 服务器进行故障排除

如果 MCP 服务器或其工具之一不可用：

1. 打开命令面板并运行 **MCP：列出服务器**。
1. 选择服务器并查看其状态。
1. 选择 ** 显示输出 ** 并检查失败请求中的错误。
1. 更正报告的配置或连接问题。
1. 选择**重新启动服务器**，然后重试该请求。

如果服务器正在运行，但模型未调用其工具，请按照[未调用MCP工具](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md#an-mcp-tool-is-not-being-invoked>)。

有关设置和特定于服务器的检查，请参阅[配置和调试MCP服务器](<https://github.com/docs/agent-customization/mcp-servers.md#troubleshoot-and-debug-mcp-servers>)。

## 报告未解决的问题

如果问题仍然存在，请提供足够的证据来重现和诊断它：

> [!CAUTION]
> Agent 调试日志导出、聊天调试输出和 Copilot 日志可以包含提示、源代码、文件路径、工具输入和输出以及其他敏感数据。在共享报告之前查看并删除敏感内容。切勿包含凭据或秘密。

* **幽灵文本建议**：将鼠标悬停在建议上并选择**发送Copilot完成反馈**。
* **{% data variables.copilot.next_edit_suggestions_caps %}**：从编辑器装订线的{% data variables.copilot.next edit suggestions %}菜单中选择**反馈**。
* **聊天和 agent 问题**：打开 **帮助** > **报告问题**，选择 **{% data variables.product.prodname_vscode_shortname %}分机号**，然后选择**{% data variables.copilot.copilot_chat %}**。

包括{% data variables.product.prodname_vscode_shortname %}版本、清晰的重现步骤、预期和实际结果以及相关的诊断输出。

## 相关资源

* [调试聊天交互](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md>)
* [GitHub Copilot常见问题](<https://github.com/docs/agents/agent-troubleshooting/faq.md>)
* [故障排除 GitHub Copilot](<https://docs.github.com/en/copilot/troubleshooting-github-copilot>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Troubleshoot AI in {% data variables.product.prodname_vscode %}

When chat, agents, or inline suggestions do not behave as expected, start with the symptom you see. This article helps you choose the right diagnostic tool, interpret the result, and collect useful evidence if you need to report the issue.

## Choose a troubleshooting path

| Symptom | Start here | Next step |
|---------|------------|-----------|
| Chat or agents are unavailable | Check [chat and agent requirements](<https://github.com/docs/agents/agent-troubleshooting/faq.md#chat-features-arent-working-for-me>). | Confirm your account, plan, settings, and organization policies. |
| Inline suggestions do not appear | Check the [inline suggestion requirements](<https://github.com/docs/agents/agent-troubleshooting/faq.md#inline-suggestions-are-not-working-in-the-editor>). | Inspect the Copilot logs if the requirements are met. |
| Copilot cannot connect or sign in | [Collect network diagnostics](<#collect-network-diagnostics>). | Check your firewall, proxy, or VPN configuration. |
| Instructions are ignored | [Check customization diagnostics](<#check-customization-diagnostics>). | Inspect the session to verify which instructions reached the model. |
| A file, tool, or expected step is missing from a response | [Inspect chat interactions](<#inspect-chat-interactions>). | Compare the session events with the request sent to the model. |
| A response is slow or uses more tokens than expected | Open the Agent Debug Logs Summary view. | Use the Cache Explorer only if the session reports low cache reuse. |
| An MCP server or tool is unavailable | [Troubleshoot MCP servers](<#troubleshoot-mcp-servers>). | Inspect the server output and the tools available to the request. |

## Start with basic checks

Before you collect detailed logs:

1. Run **Code: Check for Updates** from the Command Palette to update {% data variables.product.prodname_vscode_shortname %}.
1. Confirm your access method. Sign in with the GitHub account that has your Copilot plan, or select a configured bring-your-own-key model for supported chat features. Some features require the GitHub Copilot service. For details, see [GitHub Copilot subscription questions](<https://github.com/docs/agents/agent-troubleshooting/faq.md#github-copilot-subscription>).
1. Open the Copilot status dashboard in the Status Bar and check whether you have reached an allowance. A temporary rate limit is separate from your monthly allowance.
1. Check whether a workspace setting or organization policy turns off the affected feature. For agent availability, see [Agents are not available in chat](<https://github.com/docs/agents/agent-troubleshooting/faq.md#agents-are-not-available-in-chat>).
1. Reproduce the issue in a new chat session. When you compare two runs, keep the prompt, model, and available tools consistent.

## Check customization diagnostics

Use the chat customization diagnostics view when an instructions file does not appear to apply.

1. Open the {% data variables.copilot.chat_view %}.
1. In the {% data variables.copilot.chat_view %}, open the context menu and select **Diagnostics**.
1. Review the discovered instruction files and any reported errors.

Use the result to choose the next action:

* **The file is missing**: Verify its file name, location, and the settings that control instruction discovery.
* **The file has an error**: Fix the reported frontmatter or content error, and then reopen the diagnostics view.
* **The file is loaded but not applied**: For a `*.instructions.md` file, verify that its `applyTo` pattern matches the file you are working on. Also check the **References** section of the chat response to see which instructions were used.
* **A prompt file is missing from the request**: Invoke the prompt file manually in chat. Prompt files do not use `applyTo`, and agents that run on the Agent Host do not use prompt files.

For file locations, applicability rules, and settings, see [Use custom instructions](<https://github.com/docs/agent-customization/custom-instructions.md>). To verify the exact instructions sent to the model, use the [Chat Debug view](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md#chat-debug-view>).

## Inspect chat interactions

Use the following tools when the basic checks pass but an agent behaves unexpectedly:

| Tool | Use it to |
|------|-----------|
| `/troubleshoot` | Ask the AI to analyze captured session events and answer a specific question. |
| **Agent Debug Logs** panel (Preview) | Follow the sequence of model requests, tool calls, customization discovery, errors, and agent activity. |
| **Chat Debug view** | Inspect the exact system prompt, user prompt, context, tool definitions, and tool payloads for a request. |
| **Cache Explorer** | Compare consecutive model requests after session metrics indicate low cache reuse. |

Prepare the session before you reproduce the issue:

* For local chat sessions in the main {% data variables.product.prodname_vscode_shortname %} window, enable the experimental `setting(github.copilot.chat.agentDebugLog.fileLogging.enabled)` setting and reload the window before you use `/troubleshoot`.
* For sessions that run on the [Agent Host](<https://github.com/docs/agents/concepts/agent-host.md>), enable the experimental `setting(chat.agentHost.agentDebugLog.enabled)` setting before the activity that you want to inspect. Capture is not retroactive.
* Reproduce the issue, and then select that session when you open the logs or use `/troubleshoot`.

To analyze an Agent Host session with chat:

1. Open the {% data variables.copilot.agents_window %}.
1. Enter `/troubleshoot #session` in the chat input.
1. Select the local or remote session that contains the issue.
1. Add a focused question, such as `which tool failed and what error did it return?`, and send the request.

For detailed procedures and scenario-based guidance, see [Debug chat interactions](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md>).

## View logs for GitHub Copilot

The GitHub Copilot output channels record connection failures, extension errors, and other runtime information. Increase the log level only while you reproduce an issue because trace logging produces detailed output.

1. Open the Command Palette (`kb(workbench.action.showCommands)`).
1. Run **Developer: Set Log Level** and set the GitHub Copilot and {% data variables.copilot.copilot_chat %} extensions to **Trace**.
1. Reproduce the issue.
1. Run **Output: Show Output Channels** and select **GitHub Copilot** or **{% data variables.copilot.copilot_chat %}**.
1. Review errors and warnings around the time of the failed action.
1. Run **Developer: Set Log Level** again and restore the previous log levels.

To switch output channels, use the dropdown on the right side of the Output panel.

## Collect network diagnostics

If Copilot cannot connect, collect diagnostics to identify failed connectivity checks and relevant proxy or network configuration.

1. Open the Command Palette (`kb(workbench.action.showCommands)`).
1. Run **GitHub Copilot: Chat Diagnostics**.
1. Review the diagnostic report that opens in an editor.

If a check fails, compare the result with your firewall, proxy, and VPN configuration. A report without failed checks rules out only the connections covered by the report, so continue with the Copilot output channels if the problem persists.

For required domains and network-specific guidance, see [Network and firewall configuration for Copilot](<https://github.com/docs/agents/agent-troubleshooting/faq.md#network-and-firewall-configuration-for-copilot>).

## Troubleshoot MCP servers

If an MCP server or one of its tools is unavailable:

1. Open the Command Palette and run **MCP: List Servers**.
1. Select the server and review its status.
1. Select **Show Output** and inspect errors from the failed request.
1. Correct the reported configuration or connection problem.
1. Select **Restart Server**, and then retry the request.

If the server is running but the model does not invoke its tool, follow [An MCP tool is not being invoked](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md#an-mcp-tool-is-not-being-invoked>).

For setup and server-specific checks, see [Configure and debug MCP servers](<https://github.com/docs/agent-customization/mcp-servers.md#troubleshoot-and-debug-mcp-servers>).

## Report an unresolved issue

If the issue persists, include enough evidence to reproduce and diagnose it:

> [!CAUTION]
> Agent Debug Log exports, Chat Debug output, and Copilot logs can contain prompts, source code, file paths, tool inputs and outputs, and other sensitive data. Review and remove sensitive content before sharing a report. Never include credentials or secrets.

* **Ghost text suggestions**: Hover over a suggestion and select **Send Copilot Completion Feedback**.
* **{% data variables.copilot.next_edit_suggestions_caps %}**: Select **Feedback** from the {% data variables.copilot.next edit suggestions %} menu in the editor gutter.
* **Chat and agent issues**: Open **Help** > **Report Issue**, select **{% data variables.product.prodname_vscode_shortname %} Extension**, and then select **{% data variables.copilot.copilot_chat %}**.

Include the {% data variables.product.prodname_vscode_shortname %} version, clear reproduction steps, the expected and actual result, and the relevant diagnostic output.

## Related resources

* [Debug chat interactions](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md>)
* [GitHub Copilot frequently asked questions](<https://github.com/docs/agents/agent-troubleshooting/faq.md>)
* [Troubleshooting GitHub Copilot](<https://docs.github.com/en/copilot/troubleshooting-github-copilot>)


</details>
