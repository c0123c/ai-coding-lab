# 用 Chrome 测试与调试网页

介绍浏览器扩展的连接和授权流程，让 Claude 操作网页、检查控制台、测试应用和提取数据；同时说明网站权限与计划模式行为。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-10](<https://code.claude.com/docs/en/chrome>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/chrome.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/用 Chrome 测试与调试网页.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 将 Claude Code 与 Chrome 结合使用

> 将 Claude Code 连接到 Chrome 浏览器以测试 Web 应用程序、使用控制台日志进行调试、自动填写表单以及从网页中提取数据。

Claude Code 与集成 [Claude 在 Chrome 浏览器扩展中](<https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn>) 为您提供浏览器自动化功能 CLI 或 [VS Code 延伸](<https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome>)。构建代码，然后在浏览器中进行测试和调试，而无需切换上下文。

Claude 为浏览器任务打开新选项卡并共享浏览器的登录状态，以便它可以访问您已登录的任何网站。浏览器操作在可见的 Chrome 窗口中实时运行。当Claude遇到登录页面或CAPTCHA时，它会暂停并要求您手动处理。

该扩展程序将打开的选项卡 Claude 收集到与您的会话关联的 Chrome 选项卡组中。在本地会话中，Claude Code 在会话结束时是否关闭该组取决于其结束方式：

* 当您输入 `/clear` 时，Claude Code 会关闭组，打开包含的页面，除非清除后的工作仍在运行
* 当您使用 `/resume` 之类的命令切换会话、退出 Claude Code 或运行 `/clear` 而幸存的工作仍在运行时，Claude Code 仅当该组仅包含空的新选项卡时才会关闭该组，因此您可能仍在阅读的页面保持打开状态



**注释：**

Chrome 集成适用于 Google Chrome 和 Microsoft Edge。 Claude Code 还会检测该扩展并在其他基于 Chromium 的浏览器（包括 Brave、Arc、Vivaldi 和 Opera）中设置连接。适用于 Linux 的 Windows 子系统 (WSL) 不支持 Chrome 集成。


## 能力

连接 Chrome 后，您可以在单个工作流程中将浏览器操作与编码任务链接起来：

* **实时调试**：直接读取控制台错误和DOM状态，然后修复导致它们的代码
* **设计验证**：从 Figma 模拟构建 UI，然后在浏览器中打开它以验证其匹配
* **Web 应用测试**：测试表单验证、检查视觉回归或验证用户流程
* **经过身份验证的网络应用**：与 Google 文档、Gmail、Notion 或您在没有 API 连接器的情况下登录的任何应用进行交互
* **数据提取**：从网页中拉取结构化信息并保存在本地
* **任务自动化**：自动执行重复性浏览器任务，例如数据输入、表单填写或多站点工作流程
* **文件上传**：附加计算机中的文件以上传网页上的字段
* **会话记录**：将浏览器交互记录为 GIF，以记录或分享发生的情况

## 先决条件

在 Chrome 中使用 Claude Code 之前，您需要：

* [Google Chrome](<https://www.google.com/chrome/>)、[Microsoft Edge](<https://www.microsoft.com/edge>) 或其他基于 Chromium 的浏览器，例如 Brave、Arc、Vivaldi 或 Opera
* [Claude 在 Chrome 扩展程序中](<https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn>) 版本 1.0.36 或更高版本，可在 Chrome 网上应用店购买
* [Claude Code](<https://code.claude.com/docs/en/quickstart#step-1-install-claude-code>)
* 直接 Anthropic 计划（Pro、Max、Team 或 Enterprise）

在具有以下特征的企业组织中 HIPAA 启用， Claude Chrome 中默认处于关闭状态，并且 [业主](<https://code.claude.com/docs/en/server-managed-settings#access-control>) 可以将其打开 [**组织设置 > Claude 在 Chrome 中**](<https://claude.ai/admin-settings/browser-extension>)。您的业务伙伴协议（BAA）与 Anthropic 不包括通过以下方式发送到第三方网站的数据 Claude 在 Chrome 中。请参阅 [实施指南](<https://trust.anthropic.com/resources?s=l1wrssd9hsbi4gak0tp5a6\&name=%5Banthropic%5D-hipaa-ready-offering-implementation-guide>) 获取合格服务的列表。

Chrome 集成还需要使用 `/login` 登录。如果您使用 API 密钥或来自 [`claude setup-token`](<https://code.claude.com/docs/en/authentication#generate-a-long-lived-token>) 的长期令牌进行身份验证，即使您通过 `--chrome`，Claude Code 也会使 Chrome 集成保持关闭状态，因为浏览器扩展程序无法使用这些凭据进行身份验证。在 v2.1.216 之前，这些会话可以启用 Chrome 集成，但每次尝试连接到浏览器扩展都会失败，并出现 403 错误。



**注释：**

Chrome 集成无法通过 Amazon Bedrock、Google Cloud 的 Agent Platform 或 Microsoft Foundry 等第三方提供商提供。如果您仅通过第三方提供商访问 Claude，则需要单独的 claude.ai 帐户才能使用此功能。


## 从 CLI 开始



  

#### 使用 Chrome 启动 Claude Code

使用 `--chrome` 标志启动 Claude Code：

    ```bash theme={null}
    claude --chrome
    ```

第一次使用 Chrome 启动时，Claude Code 会显示一个一次性对话框，介绍集成并解释网站权限的工作原理。按 Enter 键继续。

要在不使用该标志的情况下为将来的会话启用 Chrome，请参阅[默认启用 Chrome](<#默认启用-chrome>)。
  


  

#### 要求Claude使用浏览器

此示例导航到一个页面，与其交互，并报告它找到的内容，所有这些都来自您的终端或编辑器：

    ```text wrap theme={null}
    Go to code.claude.com/docs, click on the search box,
    type "hooks", and tell me what results appear
    ```

如果 Claude Code 在浏览器操作之前请求许可，请批准它。该对话框以 `Claude in Chrome wants to` 开头，并提供一个允许该站点上的会话执行所有操作的选项。 Claude 打开一个新选项卡并启动任务。
  


随时运行`/chrome`来检查连接状态、管理权限、重新连接扩展程序或选择要使用的连接浏览器。当状态面板显示“状态：已启用”和“扩展：已安装”时，集成正在运行。

如果连接了多个浏览器，您可以选择 Claude 使用哪一个。当浏览器操作在您选择之前启动时，Claude 会提示您选择一个。要稍后切换浏览器，请运行 `/chrome` 并选择 **选择浏览器...**。即使连接其他浏览器，Claude 也会继续使用您的选择。

对于 VS Code，参见 [浏览器自动化 VS Code](<https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome>).

### 当 Claude 询问时安装扩展

当 Claude 在交互式会话中需要您的浏览器并且 Claude Code 未检测到扩展程序时，Claude Code 会显示标题为“Claude 想要使用您的浏览器”的安装提示。 Claude Code 每个会话最多询问一次。

提示提供了三个选择：

* **安装扩展**：在浏览器中打开扩展安装页面并开始引导安装。 Claude Code 等待安装、连接扩展程序并在同一会话中启用浏览器工具。连接就绪后，选择“Continue 使用浏览器工具”，Claude 将在浏览器中恢复任务。您可以通过选择“Continue，无需浏览器工具”来退出设置，并稍后使用`/chrome`完成。
* **现在不行**：无需浏览器工具即可继续执行任务。 Claude Code可以在稍后的会话中再次询问。
* **不再询问**：在以后的会话中停止提示。您仍然可以随时使用 `/chrome` 设置集成。

两个托管 MCP 策略会关闭提示：

* 如果您的组织使用 [`deniedMcpServers` 托管设置 ](<https://code.claude.com/docs/en/managed-mcp#policy-based-control-with-allowlists-and-denylists>) 阻止 `claude-in-chrome` MCP 服务器，则 Claude Code 不会显示安装提示。
* 如果您的组织在 Chrome 中部署了 [`managed-mcp.json`](<https://code.claude.com/docs/en/managed-mcp#exclusive-control-with-managed-mcp-json>) 文件，而没有 [ 允许 Claude 以及托管集 ](<https://code.claude.com/docs/en/managed-mcp#allow-claude-in-chrome-alongside-the-managed-set>)，则 Claude Code 不会显示安装提示。

### 默认启用 Chrome

要在每次不传递 `--chrome` 的情况下连接 Chrome，请在 CLI 会话中运行 `/chrome`，然后选择 ** 默认启用 **。在【[VS Code】扩展【](<https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome>)】中，在提示框中输入【`/chrome`】，打开【**】默认启用【**】开关。两者共用一个设置，因此在任一位置打开它都会同时打开 CLI 和 VS Code。

设置为开启且 Claude Code v2.1.287 或更高版本时，每个 VS Code 会话在启动时都会连接到您的浏览器，因此 Claude 可以在您键入 `@browser` 之前使用它。在早期版本中，或者在设置关闭的情况下，当您键入 `@browser` 时，VS Code 会话将连接。

Chrome 未运行时，Claude Code 正常启动。在 v2.1.211 之前，当启用 Chrome 集成但 Chrome 未运行时，启动可能会挂起。



**注释：**

默认情况下启用 Chrome 会增加上下文使用，因为浏览器工具及其指令始终会加载。如果您发现上下文消耗增加，请关闭该设置并仅在需要时连接，在 CLI 中使用 `--chrome` 或在 VS Code 提示框中键入 [`@browser`](<https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome>)。


### 管理站点权限

站点级权限继承自 Chrome 扩展程序。管理 Chrome 扩展程序设置中的权限，以控制哪些网站 Claude 可以浏览、单击和输入内容。在 [ 自动模式 ](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) 中，当自动模式分类器本身批准对网站的浏览器调用时，扩展程序会跳过对该调用的每个网站检查，除非您的权限规则拒绝 Chrome 中的任何网站对 Claude 的访问。

### VS Code 会话中的权限提示

在 VS Code 会话中，当 Claude Code 在浏览器操作之前询问时，提示会在聊天面板中显示为卡片。当操作针对您未允许的网站时，该卡还会允许该网站。

在由于 [ 默认启用 ](<#默认启用-chrome>) 开启而在启动时连接到浏览器的会话中，Claude Code 会在您不允许的网站上执行浏览器操作之前询问您，处于手动、自动编辑、自动和绕过权限模式。在自动和绕过权限模式下，这将适用，直到您在该会话中键入 `@browser`。

### 计划模式下的浏览器工具

在[计划模式](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>)下，Claude记录GIF、打开新选项卡或运行快捷方式之前会出现权限提示。在交互式 CLI 会话中，如果 [ 绕过权限模式可用 ](<https://code.claude.com/docs/en/permission-modes#skip-all-checks-with-bypasspermissions-mode>) 且 [ 功能标志获取 ](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>) 已关闭，则这些调用将在没有提示的情况下运行。

`tabs_context_mcp` 调用在设置 `createIfEmpty` 时也会提示，包含任何这些操作的 `browser_batch` 调用也会提示。

## 工作流程示例

这些示例展示了将浏览器操作与编码任务相结合的常见方法。运行`/mcp`，选择`claude-in-chrome`，然后选择**查看工具**以查看可用浏览器工具的完整列表。

### 测试本地 Web 应用程序

开发 Web 应用程序时，请要求 Claude 验证您的更改是否正常工作：

```text wrap theme={null}
I just updated the login form validation. Can you open localhost:3000,
try submitting the form with invalid data, and check if the error
messages appear correctly?
```

Claude 导航到您的本地服务器，与表单交互，并报告其观察到的内容。

### 使用控制台日志进行调试

Claude 可以读取控制台输出以帮助诊断问题。告诉 Claude 要查找哪些模式，而不是询问所有控制台输出，因为日志可能很详细：

```text wrap theme={null}
Open the dashboard page and check the console for any errors when
the page loads.
```

Claude 读取控制台消息并可以过滤特定模式或错误类型。

### 自动填写表格

加快重复数据输入任务的速度：

```text wrap theme={null}
I have a spreadsheet of customer contacts in contacts.csv. For each row,
go to the CRM at crm.example.com, click "Add Contact", and fill in the
name, email, and phone fields.
```

Claude 读取本地文件、导航 Web 界面并输入每条记录的数据。

### 上传文件到网页

Claude 可以附加您机器上的文件以上传页面上的字段。 Claude Code 读取文件并将其内容发送到浏览器，因此可以在本地和远程会话中上传工作。需要 Claude Code v2.1.211 或更高版本。

此示例将日志文件附加到表单：

```text wrap theme={null}
Open the bug tracker at bugs.example.com, create a new issue,
and attach logs/session.log to it
```

如果 Claude 拒绝附加文件或上传失败，请检查以下原因：

* **权限**：Claude仅在允许会话读取文件时才能上传文件，因此拒绝`Read`访问文件的[权限规则](<https://code.claude.com/docs/en/settings-reference#permission-settings>)也会阻止上传该文件。
* **大小**：单次上传总共最多可以包含10 MB个文件。
* **硬链接**：Claude拒绝具有多个硬链接的文件，这在像`node_modules`这样的包管理器商店中很常见。复制文件并上传副本。
* **凭据名称**：Claude拒绝保存凭据的名称或文件夹的文件，例如`.env`、`.pem`或`.key`文件或`.ssh`下的任何文件。需要 Claude Code v2.1.293 或更高版本。

### Google 文档中的草稿内容

使用Claude直接在文档中写入，无需API设置：

```text wrap theme={null}
Draft a project update based on the recent commits and add it to my
Google Doc at docs.google.com/document/d/abc123
```

Claude 打开文档，单击编辑器，然后键入内容。这适用于您登录的任何网络应用程序：Gmail、Notion、表格等。

### 从网页中提取数据

从网站中提取结构化信息：

```text wrap theme={null}
Go to the product listings page and extract the name, price, and
availability for each item. Save the results as a CSV file.
```

Claude 导航到页面，读取内容，并将数据编译为结构化格式。

### 运行多站点工作流程

跨多个网站协调任务：

```text wrap theme={null}
Check my calendar for meetings tomorrow, then for each meeting with
an external attendee, look up their company website and add a note
about what they do.
```

Claude 跨选项卡工作以收集信息并完成工作流程。

### 录制演示 GIF

创建可共享的浏览器交互记录：

```text wrap theme={null}
Record a GIF showing how to complete the checkout flow, from adding
an item to the cart through to the confirmation page.
```

Claude记录交互序列并将其保存为GIF文件。该记录捕获浏览器中可见的所有内容，包括登录页面上的帐户详细信息，因此在与团队外部共享之前请先查看该记录。

### 将屏幕截图保存到磁盘

要求Claude将屏幕截图保存为文件：

```text wrap theme={null}
Take a screenshot of the checkout page and save it to disk
```

Claude 将图像保存到磁盘并报告文件路径。在v2.1.211之前，截图工具的`save_to_disk`选项没有写入文件。

## 故障排除

### 未检测到扩展名

如果 Claude Code 无法检测到 Chrome 扩展程序：

1. 验证 Chrome 扩展程序是否已安装并在 `chrome://extensions` 中启用
2. 通过运行 `claude --version` 验证 Claude Code 是最新的
3. 检查 Chrome 是否正在运行
4. 运行【`/chrome`】并选择“重新连接分机”以重新建立连接
5. 如果问题仍然存在，请重新启动 Claude Code 和 Chrome

首次启用 Chrome 集成时，Claude Code 会安装本机消息传递主机配置文件。 Chrome 会在启动时读取此文件，因此如果第一次尝试时未检测到该扩展程序，请重新启动 Chrome 以获取新配置。

Claude Code 打开一个浏览器选项卡，提示您仅在首次安装时连接扩展程序。当稍后的会话重写配置文件时，例如在切换构建或配置目录之后，Claude Code 不会重新打开它。

如果连接仍然失败，请验证主机配置文件是否存在：

对于 Chrome：

* **macOS**: `~/Library/Application Support/Google/Chrome/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
* **Linux**：`~/.config/google-chrome/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
* **Windows**：检查Windows注册表中的`HKCU\Software\Google\Chrome\NativeMessagingHosts\`

对于边缘：

* **macOS**: `~/Library/Application Support/Microsoft Edge/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
* **Linux**：`~/.config/microsoft-edge/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
* **Windows**：检查Windows注册表中的`HKCU\Software\Microsoft\Edge\NativeMessagingHosts\`

其他基于 Chromium 的浏览器从自己的配置目录中读取相同的文件，以浏览器命名。例如，macOS 上的 Brave 使用 `~/Library/Application Support/BraveSoftware/Brave-Browser/NativeMessagingHosts/`，而在 Windows 上，每个浏览器都有自己的注册表项，例如 `HKCU\Software\BraveSoftware\Brave-Browser\NativeMessagingHosts\`。

### 项目设置无法启用 Chrome

终端中的此警告意味着您正在处理的项目尝试打开 Chrome 集成，但 Claude Code 不允许这样做：

```text wrap theme={null}
Claude Code ignored CLAUDE_CODE_ENABLE_CFC in this project's settings: a project can't turn on Claude in Chrome. To turn it on yourself, run /chrome or start with --chrome.
```

该项目的 `.claude/settings.json` 或 `.claude/settings.local.json` 在其 `env` 块中将 [`CLAUDE_CODE_ENABLE_CFC`](<https://code.claude.com/docs/en/env-vars#variables>) 设置为 `1` 以打开 Chrome 集成。 Claude Code 没有应用该设置，因此 Chrome 集成在此会话中关闭，并且 Claude 没有浏览器工具。

Claude Code 会跳过该设置，因为这些文件存储在项目目录中，并且您签出的存储库必须无法将 Claude 连接到您的浏览器。

您可以继续按原样工作。如果您需要浏览器工具，或者希望消失警告，请执行以下操作之一：

* **要立即获取浏览器工具**：退出并在 shell 中使用 `claude --chrome` 重新启动。
* **要在以后的会话中获取浏览器工具**：在Claude Code提示符下运行`/chrome`并选择[**默认启用**](<#默认启用-chrome>)。这适用于您随后启动的会话，而不是正在运行的会话。
* **要在没有浏览器工具的情况下停止警告**：从项目的设置文件中删除`CLAUDE_CODE_ENABLE_CFC`行。

### 浏览器无响应

如果 Claude 的浏览器命令停止工作：

1. 检查模式对话框（警报、确认、提示）是否阻塞了页面。 JavaScript 对话框会阻止浏览器事件并阻止 Claude 接收命令。手动关闭对话框，然后告诉 Claude 继续。
2. 请Claude创建一个新选项卡并重试
3. 通过在 `chrome://extensions` 中禁用并重新启用 Chrome 扩展来重新启动它

### 长时间会话期间连接断开

Chrome 扩展程序的 Service Worker 可能会在长时间会话期间处于空闲状态，从而中断连接。如果浏览器工具在一段时间不活动后停止工作，请运行 `/chrome` 并选择“重新连接扩展程序”。

当您运行`/chrome`时，检查其`Status`行。如果显示“未连接”，则表明正在运行的会话自身与 Chrome 的连接失败。选择“重新连接分机”以重新启动该连接。连接成功后，扩展程序的重新连接页面将在 Chrome 中打开。在 v2.1.290 之前，“重新连接扩展程序”仅打开该页面并且不会重新启动失败的连接，因此如果浏览器工具未返回早期版本，请更新 Claude Code。

### 扩展程序已登录到不同的组织

如果您属于多个 claude.ai 组织，则扩展程序必须登录到与 Claude Code 相同的组织。如果两者不同，即使两者使用相同的 claude.ai 帐户，Claude 的浏览器工具也会返回“浏览器扩展未连接”。

要查看 Claude Code 登录到哪个组织，请在 Claude Code 提示符下运行 [`/status`](<https://code.claude.com/docs/en/commands>) 并读取 `Organization` 行。



**注意：**

如果您注销扩展程序，您将丢失其中保存的快捷方式和计划任务。首先尝试[常见错误消息](<#常见错误消息>)下的其他修复。


要更改扩展程序的组织，请在扩展程序的设置中注销，然后登录并选择 `/status` 显示的组织。

### Windows 特定问题

在 Windows 上，您可能会遇到：

* **命名管道冲突(EADDRINUSE)**：如果另一个进程正在使用相同的命名管道，请重新启动Claude Code。关闭可能正在使用 Chrome 的任何其他 Claude Code 会话。
* **本机消息传递主机错误**：如果本机消息传递主机在启动时崩溃，请尝试重新安装 Claude Code 以重新生成主机配置。
* **设置页面无法打开**：更新Claude Code。在 v2.1.211 之前，提示您连接扩展程序的浏览器选项卡可能无法在 Windows 上打开。

### 常见错误消息

以下是最常遇到的错误以及解决方法：

|错误|原因|修复|
| - | - | - |
|“浏览器扩展程序未连接”|该扩展程序未在 Chrome 中安装和运行，该扩展程序登录到与 Claude Code 不同的 claude.ai 帐户或组织，或者您组织的 IP 白名单拒绝与 `bridge.claudeusercontent.com` 的连接|检查扩展程序是否与 Claude Code 登录到相同的 claude.ai 帐户和 [组织 ](<#扩展程序已登录到不同的组织>)，重新启动 Chrome 和 Claude Code，然后运行 `/chrome` 重新连接。如果您的组织使用 IP 白名单且错误仍然存在，请参阅 [组织 IP 白名单和代理出口 ](<https://code.claude.com/docs/en/network-config#organization-ip-allowlists-and-proxy-egress>)|
|扩展程序在 `/chrome` 中显示“未检测到”|Chrome 扩展程序未安装或被禁用|在 `chrome://extensions` 中安装或启用扩展|
|“没有可用的选项卡”|Claude 尝试在选项卡准备就绪之前执行操作|要求 Claude 创建新选项卡并重试|
|“接收端不存在”|分机服务人员闲置|运行`/chrome`并选择“重新连接分机”|

## 另请参阅

* [计算机使用](<https://code.claude.com/docs/en/computer-use>)：当任务无法在浏览器中完成时控制本机macOS应用程序
* [在VS Code](<https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome>)中使用Claude Code：VS Code扩展中的浏览器自动化
* [CLI参考](<https://code.claude.com/docs/en/cli-reference>)：命令行标志包括`--chrome`
* [常用工作流程](<https://code.claude.com/docs/en/common-workflows>)：更多使用方式Claude Code
* [数据和隐私](<https://code.claude.com/docs/en/data-usage>)：Claude Code如何处理您的数据
* [Chrome 中的 Claude 入门](<https://support.claude.com/en/articles/12012173-getting-started-with-claude-in-chrome>)：Chrome 扩展程序的完整文档，包括快捷方式、计划和权限


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Use Claude Code with Chrome

> Connect Claude Code to your Chrome browser to test web apps, debug with console logs, automate form filling, and extract data from web pages.

Claude Code integrates with the [Claude in Chrome browser extension](<https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn>) to give you browser automation capabilities from the CLI or the [VS Code extension](<https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome>). Build your code, then test and debug in the browser without switching contexts.

Claude opens new tabs for browser tasks and shares your browser's login state, so it can access any site you're already signed into. Browser actions run in a visible Chrome window in real time. When Claude encounters a login page or CAPTCHA, it pauses and asks you to handle it manually.

The extension collects the tabs Claude opens into a Chrome tab group tied to your session. In local sessions, whether Claude Code closes that group when the session ends depends on how it ends:

* When you type `/clear`, Claude Code closes the group, open pages included, unless work that survives the clear is still running
* When you switch sessions with a command like `/resume`, exit Claude Code, or run a `/clear` while work that survives it is still running, Claude Code closes the group only if it holds nothing but empty new tabs, so pages you may still be reading stay open



**注释：**

  Chrome integration works with Google Chrome and Microsoft Edge. Claude Code also detects the extension and sets up the connection in other Chromium-based browsers, including Brave, Arc, Vivaldi, and Opera. Chrome integration isn't supported in Windows Subsystem for Linux (WSL).


## Capabilities

With Chrome connected, you can chain browser actions with coding tasks in a single workflow:

* **Live debugging**: read console errors and DOM state directly, then fix the code that caused them
* **Design verification**: build a UI from a Figma mock, then open it in the browser to verify it matches
* **Web app testing**: test form validation, check for visual regressions, or verify user flows
* **Authenticated web apps**: interact with Google Docs, Gmail, Notion, or any app you're logged into without API connectors
* **Data extraction**: pull structured information from web pages and save it locally
* **Task automation**: automate repetitive browser tasks like data entry, form filling, or multi-site workflows
* **File uploads**: attach files from your machine to upload fields on web pages
* **Session recording**: record browser interactions as GIFs to document or share what happened

## Prerequisites

Before using Claude Code with Chrome, you need:

* [Google Chrome](<https://www.google.com/chrome/>), [Microsoft Edge](<https://www.microsoft.com/edge>), or another Chromium-based browser such as Brave, Arc, Vivaldi, or Opera
* [Claude in Chrome extension](<https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn>) version 1.0.36 or later, available in the Chrome Web Store
* [Claude Code](<https://code.claude.com/docs/en/quickstart#step-1-install-claude-code>)
* A direct Anthropic plan (Pro, Max, Team, or Enterprise)

In Enterprise organizations that have HIPAA enabled, Claude in Chrome is off by default, and an [Owner](<https://code.claude.com/docs/en/server-managed-settings#access-control>) can turn it on in [**Organization settings > Claude in Chrome**](<https://claude.ai/admin-settings/browser-extension>). Your Business Associate Agreement (BAA) with Anthropic doesn't cover data sent to third-party sites through Claude in Chrome. See the [Implementation Guide](<https://trust.anthropic.com/resources?s=l1wrssd9hsbi4gak0tp5a6\&name=%5Banthropic%5D-hipaa-ready-offering-implementation-guide>) for a list of Eligible Services.

Chrome integration also requires signing in with `/login`. If you authenticate with an API key or a long-lived token from [`claude setup-token`](<https://code.claude.com/docs/en/authentication#generate-a-long-lived-token>), Claude Code keeps Chrome integration off, even when you pass `--chrome`, because the browser extension can't authenticate with those credentials. Before v2.1.216, these sessions could enable Chrome integration, but every attempt to connect to the browser extension failed with a 403 error.



**注释：**

  Chrome integration is not available through third-party providers like Amazon Bedrock, Google Cloud's Agent Platform, or Microsoft Foundry. If you access Claude exclusively through a third-party provider, you need a separate claude.ai account to use this feature.


## Get started in the CLI



  

#### Launch Claude Code with Chrome

    Start Claude Code with the `--chrome` flag:

    ```bash theme={null}
    claude --chrome
    ```

    The first time you launch with Chrome, Claude Code shows a one-time dialog that introduces the integration and explains how site permissions work. Press Enter to continue.

    To enable Chrome for future sessions without the flag, see [Enable Chrome by default](<#enable-chrome-by-default>).
  


  

#### Ask Claude to use the browser

    This example navigates to a page, interacts with it, and reports what it finds, all from your terminal or editor:

    ```text wrap theme={null}
    Go to code.claude.com/docs, click on the search box,
    type "hooks", and tell me what results appear
    ```

    If Claude Code asks for permission before a browser action, approve it. The dialog starts with `Claude in Chrome wants to` and offers an option to allow all actions on that site for the session. Claude opens a new tab and starts the task.
  


Run `/chrome` at any time to check the connection status, manage permissions, reconnect the extension, or choose which connected browser to use. The integration is working when the status panel shows "Status: Enabled" and "Extension: Installed".

If more than one browser is connected, you choose which one Claude uses. When a browser action starts before you've chosen, Claude prompts you to pick one. To switch browsers later, run `/chrome` and select **Select browser…**. Claude keeps using your choice even when another browser connects.

For VS Code, see [browser automation in VS Code](<https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome>).

### Install the extension when Claude asks

When Claude needs your browser in an interactive session and Claude Code doesn't detect the extension, Claude Code shows an install prompt titled "Claude wants to use your browser". Claude Code asks at most once per session.

The prompt offers three choices:

* **Install extension**: opens the extension install page in your browser and starts a guided setup. Claude Code waits for the install, connects the extension, and enables browser tools in the same session. When the connection is ready, select "Continue with browser tools" and Claude resumes the task in your browser. You can leave setup by selecting "Continue without browser tools" and finish later with `/chrome`.
* **Not now**: continues the task without browser tools. Claude Code can ask again in a later session.
* **Don't ask again**: stops the prompt in future sessions. You can still set up the integration anytime with `/chrome`.

Two managed MCP policies turn the prompt off:

* If your organization blocks the `claude-in-chrome` MCP server with the [`deniedMcpServers` managed setting](<https://code.claude.com/docs/en/managed-mcp#policy-based-control-with-allowlists-and-denylists>), Claude Code doesn't show the install prompt.
* If your organization deploys a [`managed-mcp.json`](<https://code.claude.com/docs/en/managed-mcp#exclusive-control-with-managed-mcp-json>) file without [allowing Claude in Chrome alongside the managed set](<https://code.claude.com/docs/en/managed-mcp#allow-claude-in-chrome-alongside-the-managed-set>), Claude Code doesn't show the install prompt.

### Enable Chrome by default

To connect Chrome without passing `--chrome` each time, run `/chrome` in a CLI session and select **Enabled by default**. In the [VS Code extension](<https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome>), type `/chrome` in the prompt box and turn on the **Enabled by default** switch. The two share one setting, so turning it on in either place turns it on for both the CLI and VS Code.

With the setting on and Claude Code v2.1.287 or later, each VS Code session connects to your browser as it starts, so Claude can use it before you type `@browser`. On earlier versions, or with the setting off, a VS Code session connects when you type `@browser`.

Claude Code starts normally when Chrome isn't running. Before v2.1.211, startup could hang when Chrome integration was enabled but Chrome wasn't running.



**注释：**

  Enabling Chrome by default increases context usage since the browser tools and their instructions are always loaded. If you notice increased context consumption, turn the setting off and connect only when needed, with `--chrome` in the CLI or by typing [`@browser`](<https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome>) in the VS Code prompt box.


### Manage site permissions

Site-level permissions are inherited from the Chrome extension. Manage permissions in the Chrome extension settings to control which sites Claude can browse, click, and type on. In [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>), when the auto mode classifier itself approves a browser call to a site, the extension skips its own per-site check for that call, unless your permission rules deny any site to Claude in Chrome.

### Permission prompts in VS Code sessions

In a VS Code session, when Claude Code asks before a browser action, the prompt appears as a card in the chat panel. When the action targets a site you haven't allowed, the card also offers to allow that site.

In a session that connected to your browser at start because [Enabled by default](<#enable-chrome-by-default>) is on, Claude Code asks you before browser actions on a site you haven't allowed, in Manual, Edit automatically, Auto, and Bypass permissions modes. In Auto and Bypass permissions modes, this applies until you type `@browser` in that session.

### Browser tools in plan mode

In [plan mode](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>), a permission prompt appears before Claude records a GIF, opens a new tab, or runs a shortcut. In an interactive CLI session, if [bypass permissions mode is available](<https://code.claude.com/docs/en/permission-modes#skip-all-checks-with-bypasspermissions-mode>) and [feature-flag fetching](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>) is off, these calls run without a prompt.

A `tabs_context_mcp` call also prompts when it sets `createIfEmpty`, and so does a `browser_batch` call that includes any of these actions.

## Example workflows

These examples show common ways to combine browser actions with coding tasks. Run `/mcp`, select `claude-in-chrome`, then select **View tools** to see the full list of available browser tools.

### Test a local web application

When developing a web app, ask Claude to verify your changes work correctly:

```text wrap theme={null}
I just updated the login form validation. Can you open localhost:3000,
try submitting the form with invalid data, and check if the error
messages appear correctly?
```

Claude navigates to your local server, interacts with the form, and reports what it observes.

### Debug with console logs

Claude can read console output to help diagnose problems. Tell Claude what patterns to look for rather than asking for all console output, since logs can be verbose:

```text wrap theme={null}
Open the dashboard page and check the console for any errors when
the page loads.
```

Claude reads the console messages and can filter for specific patterns or error types.

### Automate form filling

Speed up repetitive data entry tasks:

```text wrap theme={null}
I have a spreadsheet of customer contacts in contacts.csv. For each row,
go to the CRM at crm.example.com, click "Add Contact", and fill in the
name, email, and phone fields.
```

Claude reads your local file, navigates the web interface, and enters the data for each record.

### Upload files to web pages

Claude can attach files from your machine to upload fields on a page. Claude Code reads the file and sends its contents to the browser, so uploads work in both local and remote sessions. Requires Claude Code v2.1.211 or later.

This example attaches a log file to a form:

```text wrap theme={null}
Open the bug tracker at bugs.example.com, create a new issue,
and attach logs/session.log to it
```

If Claude refuses to attach a file or an upload fails, check for these causes:

* **Permissions**: Claude can upload a file only when the session is allowed to read it, so [permission rules](<https://code.claude.com/docs/en/settings-reference#permission-settings>) that deny `Read` access to a file also block uploading it.
* **Size**: a single upload can include up to 10 MB of files in total.
* **Hard links**: Claude refuses files that have multiple hard links, which is common inside package-manager stores like `node_modules`. Copy the file and upload the copy.
* **Credential names**: Claude refuses a file whose name or folder is one that credentials are kept under, such as `.env`, a `.pem` or `.key` file, or anything under `.ssh`. Requires Claude Code v2.1.293 or later.

### Draft content in Google Docs

Use Claude to write directly in your documents without API setup:

```text wrap theme={null}
Draft a project update based on the recent commits and add it to my
Google Doc at docs.google.com/document/d/abc123
```

Claude opens the document, clicks into the editor, and types the content. This works with any web app you're logged into: Gmail, Notion, Sheets, and more.

### Extract data from web pages

Pull structured information from websites:

```text wrap theme={null}
Go to the product listings page and extract the name, price, and
availability for each item. Save the results as a CSV file.
```

Claude navigates to the page, reads the content, and compiles the data into a structured format.

### Run multi-site workflows

Coordinate tasks across multiple websites:

```text wrap theme={null}
Check my calendar for meetings tomorrow, then for each meeting with
an external attendee, look up their company website and add a note
about what they do.
```

Claude works across tabs to gather information and complete the workflow.

### Record a demo GIF

Create shareable recordings of browser interactions:

```text wrap theme={null}
Record a GIF showing how to complete the checkout flow, from adding
an item to the cart through to the confirmation page.
```

Claude records the interaction sequence and saves it as a GIF file. The recording captures everything visible in the browser, including account details on logged-in pages, so review it before sharing it outside your team.

### Save screenshots to disk

Ask Claude to keep a screenshot as a file:

```text wrap theme={null}
Take a screenshot of the checkout page and save it to disk
```

Claude saves the image to disk and reports the file path. Before v2.1.211, the screenshot tool's `save_to_disk` option didn't write a file.

## Troubleshooting

### Extension not detected

If Claude Code can't detect the Chrome extension:

1. Verify the Chrome extension is installed and enabled in `chrome://extensions`
2. Verify Claude Code is up to date by running `claude --version`
3. Check that Chrome is running
4. Run `/chrome` and select "Reconnect extension" to re-establish the connection
5. If the issue persists, restart both Claude Code and Chrome

The first time you enable Chrome integration, Claude Code installs a native messaging host configuration file. Chrome reads this file on startup, so if the extension isn't detected on your first attempt, restart Chrome to pick up the new configuration.

Claude Code opens a browser tab prompting you to connect the extension only on that first install. Claude Code doesn't reopen it when a later session rewrites the configuration file, for example after switching builds or config directories.

If the connection still fails, verify the host configuration file exists at:

For Chrome:

* **macOS**: `~/Library/Application Support/Google/Chrome/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
* **Linux**: `~/.config/google-chrome/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
* **Windows**: check `HKCU\Software\Google\Chrome\NativeMessagingHosts\` in the Windows Registry

For Edge:

* **macOS**: `~/Library/Application Support/Microsoft Edge/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
* **Linux**: `~/.config/microsoft-edge/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
* **Windows**: check `HKCU\Software\Microsoft\Edge\NativeMessagingHosts\` in the Windows Registry

Other Chromium-based browsers read the same file from their own configuration directory, named after the browser. For example, Brave on macOS uses `~/Library/Application Support/BraveSoftware/Brave-Browser/NativeMessagingHosts/`, and on Windows each browser has its own registry key, such as `HKCU\Software\BraveSoftware\Brave-Browser\NativeMessagingHosts\`.

### Project settings can't turn on Chrome

This warning in your terminal means the project you're working in tried to turn on Chrome integration, and Claude Code didn't allow it:

```text wrap theme={null}
Claude Code ignored CLAUDE_CODE_ENABLE_CFC in this project's settings: a project can't turn on Claude in Chrome. To turn it on yourself, run /chrome or start with --chrome.
```

The project's `.claude/settings.json` or `.claude/settings.local.json` sets [`CLAUDE_CODE_ENABLE_CFC`](<https://code.claude.com/docs/en/env-vars#variables>) to `1` in its `env` block to turn Chrome integration on. Claude Code didn't apply that setting, so Chrome integration is off in this session and Claude has no browser tools.

Claude Code skips the setting because those files are stored in the project directory, and a repository you check out must not be able to connect Claude to your browser.

You can keep working as you are. If you want browser tools, or want the warning gone, do one of these:

* **To get browser tools now**: exit and start again with `claude --chrome` in your shell.
* **To get browser tools in later sessions**: run `/chrome` at the Claude Code prompt and select [**Enabled by default**](<#enable-chrome-by-default>). This applies to sessions you start afterward, not the one that's running.
* **To stop the warning without browser tools**: remove the `CLAUDE_CODE_ENABLE_CFC` line from the project's settings file.

### Browser not responding

If Claude's browser commands stop working:

1. Check if a modal dialog (alert, confirm, prompt) is blocking the page. JavaScript dialogs block browser events and prevent Claude from receiving commands. Dismiss the dialog manually, then tell Claude to continue.
2. Ask Claude to create a new tab and try again
3. Restart the Chrome extension by disabling and re-enabling it in `chrome://extensions`

### Connection drops during long sessions

The Chrome extension's service worker can go idle during extended sessions, which breaks the connection. If browser tools stop working after a period of inactivity, run `/chrome` and select "Reconnect extension".

When you run `/chrome`, check its `Status` line. If it reads "Not connected", the running session's own connection to Chrome has failed. Select "Reconnect extension" to restart that connection. After the connection succeeds, the extension's reconnect page opens in Chrome. Before v2.1.290, "Reconnect extension" only opened that page and didn't restart a failed connection, so if browser tools don't return on an earlier version, update Claude Code.

### Extension signed in to a different organization

If you belong to more than one claude.ai organization, the extension must be signed in to the same organization as Claude Code. If the two differ, Claude's browser tools return "Browser extension is not connected", even when both use the same claude.ai account.

To see which organization Claude Code is signed in to, run [`/status`](<https://code.claude.com/docs/en/commands>) at the Claude Code prompt and read the `Organization` row.



**注意：**

  If you log out of the extension, you lose the shortcuts and scheduled tasks saved in it. Try the other fixes under [Common error messages](<#common-error-messages>) first.


To change the extension's organization, log out in the extension's settings, then log in and select the organization that `/status` shows.

### Windows-specific issues

On Windows, you may encounter:

* **Named pipe conflicts (EADDRINUSE)**: if another process is using the same named pipe, restart Claude Code. Close any other Claude Code sessions that might be using Chrome.
* **Native messaging host errors**: if the native messaging host crashes on startup, try reinstalling Claude Code to regenerate the host configuration.
* **Setup pages fail to open**: update Claude Code. Before v2.1.211, the browser tab prompting you to connect the extension could fail to open on Windows.

### Common error messages

These are the most frequently encountered errors and how to resolve them:

| Error | Cause | Fix |
| - | - | - |
| "Browser extension is not connected" | The extension isn't installed and running in Chrome, the extension is signed in to a different claude.ai account or organization than Claude Code, or your organization's IP allowlist rejects the connection to `bridge.claudeusercontent.com` | Check that the extension is signed in to the same claude.ai account and [organization](<#extension-signed-in-to-a-different-organization>) as Claude Code, restart Chrome and Claude Code, then run `/chrome` to reconnect. If your organization uses IP allowlisting and the error persists, see [Organization IP allowlists and proxy egress](<https://code.claude.com/docs/en/network-config#organization-ip-allowlists-and-proxy-egress>) |
| Extension shows "Not detected" in `/chrome` | Chrome extension is not installed or is disabled | Install or enable the extension in `chrome://extensions` |
| "No tab available" | Claude tried to act before a tab was ready | Ask Claude to create a new tab and retry |
| "Receiving end does not exist" | Extension service worker went idle | Run `/chrome` and select "Reconnect extension" |

## See also

* [Computer use](<https://code.claude.com/docs/en/computer-use>): control native macOS apps when a task can't be done in a browser
* [Use Claude Code in VS Code](<https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome>): browser automation in the VS Code extension
* [CLI reference](<https://code.claude.com/docs/en/cli-reference>): command-line flags including `--chrome`
* [Common workflows](<https://code.claude.com/docs/en/common-workflows>): more ways to use Claude Code
* [Data and privacy](<https://code.claude.com/docs/en/data-usage>): how Claude Code handles your data
* [Getting started with Claude in Chrome](<https://support.claude.com/en/articles/12012173-getting-started-with-claude-in-chrome>): full documentation for the Chrome extension, including shortcuts, scheduling, and permissions


</details>
