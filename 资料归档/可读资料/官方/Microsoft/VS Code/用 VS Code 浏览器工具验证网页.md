# 用 VS Code 浏览器工具验证网页

讲解运行应用、页面交互、检查结果与修复的反馈过程，包含会话选择、页面反馈、提示与故障排查。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/browser-tools.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/browser-tools.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/用 VS Code 浏览器工具验证网页.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 21b8fb7a-a3e9-4cdf-9d88-ba7b9146dcc2
DateApproved: 10/7/2026
MetaDescription: Use browser tools with AI agents in {% data variables.product.prodname_vscode_shortname %} to interact with web apps, verify user flows, inspect results, and fix issues in a closed feedback loop.
MetaSocialImage: ../../images/shared/github-copilot-social.png
Keywords:
- ai
- agents
- browser tools
- integrated browser
- browser automation
- web app testing
- playwright
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 使用浏览器工具 agents

浏览器工具为 agents 提供了用于 Web 开发的可视化和交互式反馈循环。 agent 可以更改您的代码、运行应用程序、在集成浏览器中与其交互、检查结果并修复它发现的问题。这个闭环有助于 agent 验证实际行为，而不是仅根据源代码进行推理。

浏览器工具内置于 {% data variables.product.prodname_vscode %} 中，不需要外部 Model Context Protocol (MCP) 服务器。例如，你可以问：

```prompt
Start the application, open it in the browser, test the sign-up flow, and fix any issues you find. Verify the fix before finishing.
```

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Try browser tools in action">
启动聊天提示来启动您的应用，在浏览器中打开它，并检查视觉问题。

* [打开于{% data variables.product.prodname_vscode_shortname %}](<vscode://GitHub.Copilot-Chat/chat?agent=agent%26prompt=Start%20the%20app%2C%20open%20it%20in%20the%20browser%2C%20and%20take%20a%20screenshot%20of%20the%20homepage.>)

</div>

## 浏览器反馈循环如何工作

当agent使用浏览器工具时，它可以连续循环地完成和验证一个Web开发任务：

1. agent 更改应用程序代码。
1. agent启动开发服务器或定位正在运行的应用程序。
1. 浏览器工具打开应用程序并与其交互。
1. agent 分析页面内容、屏幕截图、控制台错误和交互结果。
1. agent 修复所有问题并重复检查。

agent 报告其测试内容和结果。在保留更改之前，请检查其代码更改和验证结果。

![显示浏览器反馈循环的图表](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/browser-tools/browser-feedback-loop.png>)

## agents 在浏览器中可以做什么

浏览器工具支持常见的 Web 开发和验证任务：

|目标|agent 可以做什么|
|------|-----------------------|
|导航|打开页面、导航到 URL 并跟踪路由之间的链接。|
|了解|读取页面内容和可访问元素，并检查控制台错误。|
|互动|选择按钮、输入文本、将鼠标悬停在元素上、拖动元素以及处理对话框。|
|目视验证|捕获屏幕截图以检查布局和视觉结果。|
|自动化复杂流程|当流程需要更多控制时，运行集中的 Playwright 代码。|

内置的浏览器工具包括：

* **页面导航**：`openBrowserPage`、`navigatePage`。
* **页面内容和外观**：`readPage`、`screenshotPage`。
* **用户交互**：`clickElement`、`hoverElement`、`dragElement`、`typeInPage`、`handleDialog`。
* **自定义浏览器自动化**：`runPlaywrightCode`。

agent 根据您的提示和可用上下文选择工具。您可以在聊天对话中查看工具调用及其参数。

## 开始使用

默认情况下，通过 `setting(workbench.browser.enableChatTools)` 设置启用浏览器工具。

1. 在 {% data variables.copilot.chat_view %} 或 {% data variables.copilot.agents_window %} 中启动新的 agent 会话。

1. 要求 agent 启动或定位您的 Web 应用程序、测试特定用户流程并报告结果。

1. 包括预期的行为并告诉 agent 是否修复和验证它发现的任何问题。

例如：

```prompt
Start the development server and open the app in the browser.
Add a task named "Review browser tools", mark it complete, and reload the page.
Verify that the completed task persists. If any step fails, fix the issue and repeat the flow.
```

> [！TIP]
> 仅选择与您的任务相关的工具。了解有关 [ 为 agents](<https://github.com/docs/agents/run/tools.md#select-tools-for-a-request>) 选择工具的更多信息。

## 选择浏览器会话

agent可以在隔离会话中打开新页面，也可以共享您打开的浏览器页面。根据任务是否需要您现有的浏览器状态进行选择。

|会议|agent 可用的数据|最适合|
|---------|-----------------------------|----------|
|Agent-打开页面|私有的内存会话，不与其他浏览器选项卡共享 cookie 或存储。|本地应用程序、公共页面和未经身份验证的流。|
|您分享的页面|该选项卡的现有会话，包括其 cookie、存储和登录状态。|需要您现有会话的经过身份验证的或有状态的流。|

### 让agent打开一个页面

要求agent打开URL或测试正在运行的应用程序。 agent 打开的页面使用隔离的临时会话，这可以保护其他浏览器选项卡中的数据。

在 [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) 中，浏览器选项卡是按会话隔离的。 agent 只能读取属于其会话的选项卡并与之交互。

### 共享现有页面

默认情况下，agent 无法访问您打开的浏览器页面。要授予对页面的访问权限：

1. 在[集成浏览器](<https://github.com/docs/debugtest/integrated-browser.md>)中打开页面。
1. 在浏览器工具栏中选择**与Agent**共享。
1. 确认您要共享该页面。
1. 要求 agent 阅读该页面或与该页面交互。

视觉指示器显示该页面已共享。再次选择**与Agent**共享可立即撤销访问权限。

如果 agent 需要访问未共享的选项卡，它会要求您共享一个选项卡。在自动驾驶模式下，{% data variables.product.prodname_vscode_shortname %}会自动拒绝这些请求以保护您的隐私。

## 从网页提供反馈

集成浏览器允许您指向渲染结果，而不是仅用文本描述视觉或运行时问题。您可以选择页面内容、附加诊断信息，并为 agent 提供要实施的元素级指令。

使用浏览器工具栏中的 **添加到聊天 ** 操作来提供：

* **页面元素**：选择渲染的元素并附加其HTML、CSS和可选的屏幕截图。
* **元素注释**：选择一个或多个元素并将特定变更请求与每个元素相关联。
* **屏幕截图**：捕获当前视口、选定区域或整个页面。
* **控制台日志**：附加运行时错误和其他控制台输出。

例如，对页面的多个部分提供反馈，然后让 agent 验证其工作：

1. 在集成浏览器中打开您的应用程序。
1. 打开 **添加到聊天 ** 下拉列表并选择 **对元素进行评论 **。
1. 选择导航菜单，输入`Collapse this navigation into a menu button on narrow screens.`
1. 选择表格并输入`Align these fields and show validation errors below each input.`
1. 在聊天输入中添加任何总体说明并发送提示。
1. 要求 agent 实施反馈并使用浏览器工具在桌面和移动视口尺寸下验证结果。

您还可以选择**与Agent**共享，让agent继续与同一页面交互，包括其当前的浏览器状态。这创建了一个协作循环：您确定呈现的应用程序中的问题，agent 实现更改，浏览器工具验证结果。

有关详细过程和浏览器设置，请参阅[将浏览器上下文添加到AI聊天](<https://github.com/docs/debugtest/integrated-browser.md#add-context-to-ai-chat>)。

## 比较浏览器和聊天上下文工作流程

浏览器工具与手动将浏览器内容添加到聊天提示不同：

|工作流程|谁控制浏览器|最适合|
|----------|--------------------------|----------|
|浏览器工具|agent 自主导航和交互。|端到端验证和迭代修复。|
|添加到聊天|您可以选择要附加到提示的元素、屏幕截图或控制台日志。|指出特定的视觉或运行时问题。|
|分享给Agent|您授予 agent 对现有浏览器选项卡的访问权限。|经过身份验证或有状态的浏览器会话。|

当您想自己选择确切的浏览器上下文时，请使用 **添加到聊天 ** 操作。了解有关 [向聊天添加浏览器上下文 ](<https://github.com/docs/debugtest/integrated-browser.md#add-context-to-ai-chat>) 的更多信息。

## 编写有效的浏览器提示

描述一个可观察的结果，以便 agent 知道要练习什么以及如何确定任务是否完成。包括：

* 如何启动应用程序以及打开哪个URL。
* 要遵循的用户旅程。
* 预期的结果。
* 重要的边缘情况或视口大小。
* agent 是否应修复发现的问题。
* 修复后应重复检查 agent。

以下模式涵盖常见的浏览器工作流程。



<summary>构建并验证功能</summary>

```prompt
Implement the profile form from the requirements. Start the app and use the browser to verify that valid changes save, invalid input shows an error, and cancel restores the original values. Fix any issues and verify the complete flow again.
```






<summary>重现并修复一个错误</summary>

```prompt
Open http://localhost:3000 and reproduce this issue: submitting the checkout form twice creates duplicate orders. Find the cause, fix it, and repeat the same browser steps to verify that only one order is created.
```






<summary>查看响应行为</summary>

```prompt
Open the dashboard and inspect it at desktop and mobile viewport sizes. Verify that the navigation remains usable and no content overflows. Fix layout issues and capture screenshots of the final result.
```






<summary>检查可访问性</summary>

```prompt
Review the sign-in page for accessible names, heading structure, and keyboard operation. Exercise the form without a mouse, fix issues you find, and verify the flow again.
```




浏览器工具在 agent 会话期间提供交互式验证。当检查需要在持续集成中一致运行或防止回归时，请在存储库中保留可重复的 Playwright 或其他自动化测试。

## 隐私和企业控制

{% data variables.product.prodname_vscode_shortname %} 提供对浏览器访问的控制：

* 您明确批准共享您打开的浏览器选项卡。
* Agent-打开的选项卡使用隔离的临时存储。
* 在不受信任的工作空间中，集成浏览器始终使用临时存储。
* 管理员可以使用 `BrowserChatTools` 策略关闭浏览器工具。
* 管理员可以使用agent网络过滤来限制agent工具可以到达的域。

详细了解 [ 批准和权限 ](<https://github.com/docs/agents/run/approvals.md>)、[ 浏览器会话存储 ](<https://github.com/docs/debugtest/integrated-browser.md#session-storage>) 和 [ AI](<https://github.com/docs/enterprise/manage-ai-settings.md>) 的企业控制。

## 浏览器工具故障排除



<summary>为什么缺少浏览器工具？</summary>

确认 `setting(workbench.browser.enableChatTools)` 已打开。然后在聊天输入中打开【**】配置工具【**】，选择【**】内置【**】>【**】浏览器【**】下的工具。组织策略也可以关闭这些工具。






<summary>为什么agent无法访问我的登录页面？</summary>

agent 打开的页面使用隔离会话，并且不会继承您的登录状态。在集成浏览器中打开页面并使用 **与 Agent** 共享以授予对该经过身份验证的选项卡的访问权限。






<summary>为什么agent打开一个单独的浏览器选项卡？</summary>

除非您共享现有选项卡，否则 agent 将打开其自己的独立选项卡。当打开的选项卡与请求的域和端口匹配时，{% data variables.product.prodname_vscode_shortname %} 可以提示您共享它。






<summary>为什么agent无法到达域名？</summary>

Agent 网络过滤或组织策略可能会阻止该域。检查工具调用中的错误，如果该设置由您的组织管理，请联系您的管理员。




## 后续步骤

* 按照 [ 使用浏览器工具教程构建和验证 Web 应用程序 ](<https://github.com/docs/agents/guides/browser-agent-testing-guide.md>)。
* 了解 [ 集成浏览器 ](<https://github.com/docs/debugtest/integrated-browser.md>)。
* 探索 [测试用 AI](<https://github.com/docs/agents/guides/test-code-with-ai.md>).
* 了解 [ 工具如何在 agent 循环 ](<https://github.com/docs/agents/concepts/tools.md>) 中工作。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Use browser tools with agents

Browser tools give agents a visual and interactive feedback loop for web development. An agent can change your code, run the application, interact with it in the integrated browser, inspect the result, and fix problems it finds. This closed loop helps the agent verify actual behavior instead of reasoning only from source code.

Browser tools are built into {% data variables.product.prodname_vscode %} and don't require an external Model Context Protocol (MCP) server. For example, you can ask:

```prompt
Start the application, open it in the browser, test the sign-up flow, and fix any issues you find. Verify the fix before finishing.
```

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Try browser tools in action">
Launch a chat prompt that starts your app, opens it in the browser, and checks for visual issues.

* [Open in {% data variables.product.prodname_vscode_shortname %}](<vscode://GitHub.Copilot-Chat/chat?agent=agent%26prompt=Start%20the%20app%2C%20open%20it%20in%20the%20browser%2C%20and%20take%20a%20screenshot%20of%20the%20homepage.>)

</div>

## How the browser feedback loop works

When an agent uses browser tools, it can complete and verify a web development task in a continuous loop:

1. The agent changes the application code.
1. The agent starts the development server or locates the running application.
1. Browser tools open the application and interact with it.
1. The agent analyzes page content, screenshots, console errors, and interaction results.
1. The agent fixes any problems and repeats the checks.

The agent reports what it tested and the result. Review its code changes and verification results before you keep the changes.

![Diagram showing the browser feedback loop](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/browser-tools/browser-feedback-loop.png>)

## What agents can do in the browser

Browser tools support common web development and validation tasks:

| Goal | What the agent can do |
|------|-----------------------|
| Navigate | Open pages, navigate to URLs, and follow links between routes. |
| Understand | Read page content and accessible elements, and inspect console errors. |
| Interact | Select buttons, enter text, hover over elements, drag elements, and handle dialogs. |
| Verify visually | Capture screenshots to inspect layout and the visual result. |
| Automate complex flows | Run focused Playwright code when a flow needs more control. |

The built-in browser tools include:

* **Page navigation**: `openBrowserPage`, `navigatePage`.
* **Page content and appearance**: `readPage`, `screenshotPage`.
* **User interaction**: `clickElement`, `hoverElement`, `dragElement`, `typeInPage`, `handleDialog`.
* **Custom browser automation**: `runPlaywrightCode`.

The agent chooses tools based on your prompt and the available context. You can review the tool calls and their parameters in the chat conversation.

## Get started

Browser tools are enabled by default with the `setting(workbench.browser.enableChatTools)` setting.

1. Start a new agent session in the {% data variables.copilot.chat_view %} or the {% data variables.copilot.agents_window %}.

1. Ask the agent to start or locate your web application, test a specific user flow, and report the result.

1. Include the expected behavior and tell the agent whether to fix and verify any problems it finds.

For example:

```prompt
Start the development server and open the app in the browser.
Add a task named "Review browser tools", mark it complete, and reload the page.
Verify that the completed task persists. If any step fails, fix the issue and repeat the flow.
```

> [!TIP]
> Select only the tools that are relevant to your task. Learn more about [selecting tools for agents](<https://github.com/docs/agents/run/tools.md#select-tools-for-a-request>).

## Choose a browser session

The agent can open a new page in an isolated session, or you can share a browser page that you opened. Choose based on whether the task needs your existing browser state.

| Session | Data available to the agent | Best for |
|---------|-----------------------------|----------|
| Agent-opened page | A private, in-memory session that doesn't share cookies or storage with your other browser tabs. | Local apps, public pages, and unauthenticated flows. |
| Page shared by you | The existing session for that tab, including its cookies, storage, and sign-in state. | Authenticated or stateful flows that require your existing session. |

### Let the agent open a page

Ask the agent to open a URL or test a running application. Pages opened by the agent use isolated ephemeral sessions, which protects the data in your other browser tabs.

In the [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>), browser tabs are isolated per session. An agent can only read and interact with tabs that belong to its session.

### Share an existing page

By default, an agent can't access browser pages that you opened. To grant access to a page:

1. Open the page in the [integrated browser](<https://github.com/docs/debugtest/integrated-browser.md>).
1. Select **Share with Agent** in the browser toolbar.
1. Confirm that you want to share the page.
1. Ask the agent to read or interact with the page.

A visual indicator shows that the page is shared. Select **Share with Agent** again to revoke access immediately.

If an agent needs access to an unshared tab, it can ask you to share one. In autopilot mode, {% data variables.product.prodname_vscode_shortname %} declines these requests automatically to protect your privacy.

## Give feedback from a web page

The integrated browser lets you point to the rendered result instead of describing a visual or runtime problem only in text. You can select page content, attach diagnostic information, and give element-level instructions for the agent to implement.

Use the **Add to Chat** actions in the browser toolbar to provide:

* **Page elements**: select a rendered element and attach its HTML, CSS, and optional screenshot.
* **Element comments**: select one or more elements and associate a specific change request with each one.
* **Screenshots**: capture the current viewport, a selected area, or the full page.
* **Console logs**: attach runtime errors and other console output.

For example, give feedback on multiple parts of a page and then let the agent validate its work:

1. Open your application in the integrated browser.
1. Open the **Add to Chat** dropdown and select **Comment on Elements**.
1. Select the navigation menu and enter `Collapse this navigation into a menu button on narrow screens.`
1. Select the form and enter `Align these fields and show validation errors below each input.`
1. Add any overall instructions in the chat input and send the prompt.
1. Ask the agent to implement the feedback and use browser tools to verify the result at desktop and mobile viewport sizes.

You can also select **Share with Agent** to let the agent continue interacting with the same page, including its current browser state. This creates a collaborative loop: you identify a problem in the rendered application, the agent implements the change, and browser tools verify the result.

For detailed procedures and browser settings, see [adding browser context to AI chat](<https://github.com/docs/debugtest/integrated-browser.md#add-context-to-ai-chat>).

## Compare browser and chat context workflows

Browser tools are different from manually adding browser content to a chat prompt:

| Workflow | Who controls the browser | Best for |
|----------|--------------------------|----------|
| Browser tools | The agent navigates and interacts autonomously. | End-to-end validation and iterative fixes. |
| Add to Chat | You select elements, screenshots, or console logs to attach to a prompt. | Pointing out a specific visual or runtime issue. |
| Share with Agent | You grant the agent access to an existing browser tab. | Authenticated or stateful browser sessions. |

Use the **Add to Chat** actions when you want to choose the exact browser context yourself. Learn more about [adding browser context to chat](<https://github.com/docs/debugtest/integrated-browser.md#add-context-to-ai-chat>).

## Write effective browser prompts

Describe an observable outcome so the agent knows what to exercise and how to decide whether the task is complete. Include:

* How to start the application and which URL to open.
* The user journey to follow.
* The expected result.
* Important edge cases or viewport sizes.
* Whether the agent should fix discovered problems.
* The checks the agent should repeat after a fix.

The following patterns cover common browser workflows.



<summary>Build and verify a feature</summary>

```prompt
Implement the profile form from the requirements. Start the app and use the browser to verify that valid changes save, invalid input shows an error, and cancel restores the original values. Fix any issues and verify the complete flow again.
```






<summary>Reproduce and fix a bug</summary>

```prompt
Open http://localhost:3000 and reproduce this issue: submitting the checkout form twice creates duplicate orders. Find the cause, fix it, and repeat the same browser steps to verify that only one order is created.
```






<summary>Review responsive behavior</summary>

```prompt
Open the dashboard and inspect it at desktop and mobile viewport sizes. Verify that the navigation remains usable and no content overflows. Fix layout issues and capture screenshots of the final result.
```






<summary>Check accessibility</summary>

```prompt
Review the sign-in page for accessible names, heading structure, and keyboard operation. Exercise the form without a mouse, fix issues you find, and verify the flow again.
```




Browser tools provide interactive validation during an agent session. Keep repeatable Playwright or other automated tests in your repository when the checks need to run consistently in continuous integration or guard against regressions.

## Privacy and enterprise controls

{% data variables.product.prodname_vscode_shortname %} provides controls over browser access:

* You explicitly approve sharing a browser tab that you opened.
* Agent-opened tabs use isolated ephemeral storage.
* In untrusted workspaces, the integrated browser always uses ephemeral storage.
* Administrators can turn off browser tools with the `BrowserChatTools` policy.
* Administrators can use agent network filtering to restrict the domains that agent tools can reach.

Learn more about [approvals and permissions](<https://github.com/docs/agents/run/approvals.md>), [browser session storage](<https://github.com/docs/debugtest/integrated-browser.md#session-storage>), and [enterprise controls for AI](<https://github.com/docs/enterprise/manage-ai-settings.md>).

## Troubleshoot browser tools



<summary>Why are browser tools missing?</summary>

Verify that `setting(workbench.browser.enableChatTools)` is turned on. Then open **Configure Tools** in the chat input and select the tools under **Built-in** > **Browser**. An organization policy can also turn off these tools.






<summary>Why can't the agent access my signed-in page?</summary>

Pages opened by an agent use an isolated session and don't inherit your sign-in state. Open the page in the integrated browser and use **Share with Agent** to grant access to that authenticated tab.






<summary>Why did the agent open a separate browser tab?</summary>

An agent opens its own isolated tab unless you share an existing tab. When an open tab matches the requested domain and port, {% data variables.product.prodname_vscode_shortname %} can prompt you to share it instead.






<summary>Why can't the agent reach a domain?</summary>

Agent network filtering or an organization policy might block the domain. Check the error in the tool call and contact your administrator if the setting is managed by your organization.




## Next steps

* Follow the [build and validate a web app with browser tools tutorial](<https://github.com/docs/agents/guides/browser-agent-testing-guide.md>).
* Learn about the [integrated browser](<https://github.com/docs/debugtest/integrated-browser.md>).
* Explore [testing with AI](<https://github.com/docs/agents/guides/test-code-with-ai.md>).
* Learn [how tools work in the agent loop](<https://github.com/docs/agents/concepts/tools.md>).


</details>
