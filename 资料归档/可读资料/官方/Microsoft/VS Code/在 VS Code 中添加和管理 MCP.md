# 在 VS Code 中添加和管理 MCP

介绍服务发现、mcp.json 配置、启停、沙箱化和工具使用，让 Agent 接入外部开发数据与服务。

- 来源：Microsoft／VS Code
- 资料类型：存档文档
- 文档时间：官方审核：[2026-10-07](<https://code.visualstudio.com/docs/agent-customization/mcp-servers>)；官方文件提交（截至抓取）：[2026-10-07](<https://github.com/microsoft/vscode-docs/commit/a2f9dd4e642a53cf17ecda8d9568611404ce2740>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.visualstudio.com/docs/agent-customization/mcp-servers>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/在 VS Code 中添加和管理 MCP.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 在VS Code中添加和管理MCP服务器

* [以 Markdown 形式查看](<https://code.visualstudio.com/docs/agent-customization/mcp-servers.md>)
* [在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fmcp-servers%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问
* [在VS Code内部人士中提问](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fmcp-servers%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fmcp-servers%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问

---

当编码任务需要 agent 的现有工具无法提供的信息或操作时，添加 MCP 服务器。例如，服务器可以让 agent 查询数据库或更新外部服务中的问题，而不是要求您手动执行这些步骤。

[Model Context Protocol (MCP)](<https://modelcontextprotocol.io/>) 是将 AI 模型连接到外部工具和服务的开放标准。在Visual Studio Code中，服务器将这些功能公开为[工具](<https://code.visualstudio.com/docs/agents/run/tools>)。 MCP服务器还可以提供[资源、提示和交互式应用程序](<#_other-mcp-capabilities>)。

如果现有工具可以满足您的需求，则不需要额外的服务器。添加一项意味着配置访问并决定是否[信任服务器](<#_mcp-server-trust>)。

有关 MCP 如何融入 AI 自定义框架的背景信息，请参阅[自定义概念](<https://code.visualstudio.com/docs/agents/concepts/customization>) 和 [工具概念](<https://code.visualstudio.com/docs/agents/concepts/tools>)。

本文介绍如何添加、配置和管理 MCP 服务器。要了解agents如何调用工具，请参阅[将工具与agents](<https://code.visualstudio.com/docs/agents/run/tools>)一起使用。

提示

使用 [Agent 自定义编辑器 ](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>)（预览版）在一处发现、创建和管理所有 agent 自定义。运行 **Chat：从命令面板打开自定义 **。

## 快速入门：在聊天中使用 MCP 服务器

本演练演示使用 [Playwright](<https://github.com/microsoft/playwright-mcp>) MCP 服务器添加外部工具提供程序。如果您的目标是浏览器交互而不是学习 MCP 设置，请先检查 [ 内置浏览器工具 ](<https://code.visualstudio.com/docs/agents/run/browser-tools>)。他们不需要 MCP 服务器。

1. 打开扩展视图 (⇧⌘X (Windows、Linux Ctrl+Shift+X)) 并在搜索字段中输入 `@mcp playwright`。
2. 选择 **安装** 在您的用户配置文件中安装 Playwright MCP 服务器。
3. 出现提示时，确认您信任服务器来启动它。 VS Code 发现服务器的工具并使其在聊天中可用。
4. 打开聊天视图 (⌃⌘I (Windows、Linux Ctrl+Alt+I)) 并输入使用 Playwright 工具的提示。例如：

   ```
   Go to code.visualstudio.com, decline the cookie banner, and give me a screenshot of the homepage.
   ```

VS Code 调用 Playwright 工具在浏览器中打开页面并截取屏幕截图。可能会要求您确认每个工具调用。

提示

在聊天输入中选择 ** 配置工具 ** 按钮，查看 Playwright MCP 服务器的所有可用工具，并打开或关闭特定工具。

## 添加 MCP 服务器

要从 MCP 服务器库安装 MCP 服务器：

1. 打开扩展视图 (⇧⌘X (Windows、Linux Ctrl+Shift+X)) 并在搜索字段中输入 `@mcp`。这显示了库中可用的 MCP 服务器的列表。
2. 您可以在您的用户配置文件或工作区中安装 MCP 服务器：

   * 要在您的用户配置文件中安装，请选择**安装**。
   * 要在工作区中安装，请右键单击 MCP 服务器并选择 **在工作区中安装 **。这将更新工作区中的 `.vscode/mcp.json` 文件。
3. 要查看MCP服务器详细信息，请在列表中选择MCP服务器以打开详细信息页面。

注意事项

本地 MCP 服务器可以在您的计算机上运行任意代码。仅添加来自[可信来源](<#_mcp-server-trust>)的服务器，并在启动之前检查发布者和服务器配置。阅读 [ 安全文档 ](<https://code.visualstudio.com/docs/agents/run/security>)，了解在 VS Code 中使用 AI 的含义。

### 配置`mcp.json`文件

您可以在以下位置手动配置 MCP 服务器：

* **工作区，VS Code格式**：在项目中创建或打开`.vscode/mcp.json`。此格式在顶级 `servers` 对象中定义服务器。
* **工作区，可移植格式**：在项目的根目录下创建`.mcp.json`。此格式在顶级 `mcpServers` 对象中定义服务器，并可跨兼容工具使用。
* **用户配置文件**：运行**MCP：打开用户配置**命令以打开[用户配置文件](<https://code.visualstudio.com/docs/configure/profiles>)文件夹中的`mcp.json`文件。此处配置的服务器可在您的所有工作区中使用。当您使用多个配置文件时，每个配置文件都可以有自己的 MCP 服务器配置。
* **用户，可移植格式**：创建`$COPILOT_HOME/mcp-config.json`，或在未设置`COPILOT_HOME`时创建`~/.copilot/mcp-config.json`。此格式在顶级 `mcpServers` 对象中定义服务器，并可跨兼容的 Copilot 工具工作。

在源代码管理中包含工作区配置，以便与您的团队共享 MCP 服务器。

您还可以在命令面板（⇧⌘P（Windows、Linux Ctrl+Shift+P））中运行**MCP：添加服务器**，通过引导流程添加服务器。选择 **.mcp.json** 将可移植配置保存在工作区根目录，或选择 **Copilot 全局 ** 将其保存在 `$COPILOT_HOME/mcp-config.json` 中，并以 `~/.copilot/mcp-config.json` 作为后备位置。该流程还列出了已弃用的 `.vscode/mcp.json` 和 VS Code 用户配置文件目标以实现兼容性。首选新服务器的便携式目的地。

对于在 [Agent 主机 ](<https://code.visualstudio.com/docs/agents/concepts/agent-host>) 上运行的会话，Agent 主机不会直接读取 `.vscode/mcp.json`。相反，VS Code 将您的 MCP 服务器配置转发到 Agent 主机，需要交互式输入的服务器除外（例如，`${input:...}` 变量）。对于可跨 Agent 主机和其他 Copilot 工具移植的 MCP 配置，请使用 Agent 主机本机读取的工作区 `.mcp.json` 文件或用户 `~/.copilot/mcp-config.json` 文件。详细了解 [ 扩展主机 ](<https://code.visualstudio.com/docs/agents/concepts/agent-host#_behavior-on-the-extension-host>) 上的行为。

重要

避免对 API 键等敏感信息进行硬编码。请改用[输入变量](<https://code.visualstudio.com/docs/agents/reference/mcp-configuration#_input-variables-for-sensitive-data>)或环境文件。

以下示例显示了一个配置远程 MCP 服务器和本地 MCP 服务器的 `mcp.json` 文件：

```
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp"
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@microsoft/mcp-server-playwright"]
    }
  }
}
```

VS Code提供配置文件IntelliSense。有关完整的配置架构和字段参考，请参阅[MCP配置参考](<https://code.visualstudio.com/docs/agents/reference/mcp-configuration>)。

注意事项

MCP 服务器在任何配置的地方运行。您的用户配置文件中的服务器在本地运行。如果您连接到 [ 远程 ](<https://code.visualstudio.com/docs/remote/remote-overview>) 并希望服务器在远程计算机上运行，​​请在工作区设置或远程用户设置中定义它（**MCP：打开远程用户配置**）。

### 添加 MCP 服务器的其他选项

将 MCP 服务器添加到开发容器

可以通过 `devcontainer.json` 文件在开发容器中配置 MCP 服务器。这允许您将 MCP 服务器配置作为容器化开发环境的一部分。

要在开发容器中配置 MCP 服务器，请将服务器配置添加到 `customizations.vscode.mcp` 部分：

```
{
  "image": "mcr.microsoft.com/devcontainers/typescript-node:latest",
  "customizations": {
    "vscode": {
      "mcp": {
        "servers": {
          "playwright": {
            "command": "npx",
            "args": ["-y", "@microsoft/mcp-server-playwright"]
          }
        }
      }
    }
  }
}
```

创建开发容器后，VS Code 会自动将 MCP 服务器配置写入远程 `mcp.json` 文件，使它们在您的容器化开发环境中可用。


从自定义市场安装 MCP 服务器（实验性）

自定义市场提供了另一种从配置的源查找 MCP 服务器的方法：

1. 打开


chat.customizations.marketplace.enabled

在 VS Code
 中打开 在 VS Code 内部人士 
 设置中打开。
2. 运行 **Chat：从命令面板 (⇧⌘P (Windows、Linux Ctrl+Shift+P)) 打开自定义 **，然后选择 ** 发现 **。
3. 从源菜单中，选择 **GitHub Feed**，然后按名称搜索服务器或将 `@type:mcp` 添加到搜索中。
4. 选择服务器并查看其来源、产品映像、发布者详细信息和安装要求。
5. 选择**安装**。

有些服务器不支持自动安装。请按照发布者提供的设置说明进行操作。


自动发现MCP服务器

VS Code 可以自动检测并重用其他应用程序（例如 Claude 桌面）的 MCP 服务器配置。

随着


聊天.mcp.discovery.启用

在VS Code
中打开 在VS Code Insiders
设置中打开，您可以选择一个或多个工具来发现其MCP服务器配置。

支持的源包括 Claude 桌面、GitHub Copilot、CLI、Cursor 和 Windsurf。详细了解 [自动 MCP 服务器发现 ](<https://code.visualstudio.com/docs/agents/reference/mcp-configuration#_automatic-mcp-server-discovery>)，包括配置位置和远程行为。


从命令行安装 MCP 服务器

您还可以使用 VS Code 命令行界面将 MCP 服务器添加到您的用户配置文件或工作区。

要将 MCP 服务器添加到您的用户配置文件，请使用 `--add-mcp` VS Code 命令行选项，并以 `{\"name\":\"server-name\",\"command\":...}` 形式提供 JSON 服务器配置。

```
code --add-mcp "{\"name\":\"my-server\",\"command\": \"uvx\",\"args\": [\"mcp-server-fetch\"]}"
```

## 其他 MCP 功能

除了工具之外，MCP服务器还可以提供其他功能：

|能力|描述|如何使用|
| --- | --- | --- |
|**资源**|从 MCP 服务器访问数据作为提示中的上下文，例如文件、数据库表或 API 响应。资源提供附加到聊天请求的只读上下文。|在聊天视图中，选择 **添加上下文** > **MCP 资源**。您还可以使用**MCP：浏览资源**命令。|
|**提示**|使用 MCP 服务器中的预配置提示模板来标准化常见任务。每个 MCP 服务器都可以公开根据其功能定制的一组提示。|在聊天输入中输入 `/<MCP server>.<prompt>`。|
|**MCP 应用程序**|获取直接在聊天中呈现的交互式 UI 组件，例如表单、可视化和拖放列表。 MCP 应用程序可实现除文本响应之外的更丰富的交互。请参阅 [MCP 应用博客文章 ](<https://code.visualstudio.com/blogs/2026/01/26/mcp-apps-support>) 了解更多信息。|MCP 当 MCP 服务器支持应用程序时，应用程序会内联显示。|

## 沙盒 MCP 服务器

在 macOS 和 Linux 上，您可以为本地运行的 stdio MCP 服务器启用沙箱，以限制它们对文件系统和网络的访问。沙盒服务器在隔离环境中运行，只能访问您明确允许的文件路径和网络域。

注意事项

此每服务器沙箱与 Agent 主机启动的 MCP 服务器的自动沙箱是分开的。当Agent主机沙箱开启时，


聊天.agent.沙箱.mcpServers

在 VS Code
中打开 在 VS Code 内部人员中打开 
 默认情况下适用，并具有自己的配置和范围。了解有关 [agent 沙箱 ](<https://code.visualstudio.com/docs/agents/run/agent-sandboxing>) 的更多信息。

要为服务器启用沙箱，请在 `mcp.json` 文件的服务器配置中设置 `"sandboxEnabled": true`。您可以通过添加具有特定文件系统和网络规则的顶级 `sandbox` 对象来进一步自定义沙箱限制。

以下示例显示如何为本地 MCP 服务器启用沙箱，并将其访问权限限制为仅写入工作区中的文件并访问特定的 API 域：

```
{
  "servers": {
    "myServer": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@example/mcp-server"],
      "sandboxEnabled": true
    }
  },
  "sandbox": {
    "filesystem": {
      "allowWrite": ["${workspaceFolder}"]
    },
    "network": {
      "allowedDomains": ["api.example.com"]
    }
  }
}
```

启用沙箱后，来自服务器的工具调用会自动批准，因为它们在受控环境中运行。

有关完整的沙箱配置架构，请参阅[沙箱配置](<https://code.visualstudio.com/docs/agents/reference/mcp-configuration#_sandbox-configuration>)参考。

注意事项

沙盒目前在 Windows 上不可用。

## 管理MCP服务器

VS Code 提供了多个选项来管理您的 MCP 服务器，例如启动或停止服务器、查看日志、卸载或清除缓存工具。

|方法|描述|  |
| --- | --- | --- |
|**Agent 自定义编辑器**|打开 **MCP 服务器 ** 部分，选择服务器旁边的省略号 (**...**)，然后选择一个操作。|  |
|**扩展视图**|右键单击 **MCP SERVERS - INSTALLED** 部分中的服务器或选择齿轮图标。|显示“扩展”视图中的 MCP 服务器的屏幕截图。|
|**`mcp.json` 编辑**|打开配置文件并使用内联操作（代码镜头）。使用**MCP：打开用户配置**或**MCP：打开工作区文件夹配置**来打开文件。|MCP服务器配置，带有管理服务器的镜头。|
|**命令面板**|运行**MCP：列出服务器**，选择服务器，然后选择操作。|显示命令面板中 MCP 服务器操作的屏幕截图。|

## 启用或禁用 MCP 服务器

您可以全局或针对特定工作区启用或禁用 MCP 服务器。当 MCP 服务器被禁用时，它不会启动，并且其工具、提示、资源和 MCP 应用程序将被排除在聊天之外。

要启用或禁用 MCP 服务器：

* 右键单击扩展视图的 **MCP SERVERS - INSTALLED** 部分中的服务器，然后选择 **启用 ** 或 **禁用 **。
* 运行**MCP：从命令面板列出服务器**，选择一个服务器，然后选择**启用**或**禁用**。
* 使用 [Agent 自定义编辑器 ](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>) 切换服务器的启用状态。

enable/disable状态与`mcp.json`中的服务器配置分开存储，因此不会影响共享配置文件。

## 集中管理对 VS Code 中的 MCP 服务器的访问

组织可以使用 Copilot 企业管理设置集中管理对 MCP 服务器的访问。了解有关 [ MCP 服务器 ](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-mcp-usage/configure-enterprise-allowlist>) 的企业管理的更多信息。

## 自动启动MCP服务器

当您提交聊天消息时，VS Code可以自动启动MCP服务器，以便其工具可用于聊天。使用


聊天.mcp.自动启动

在 VS Code
 中打开 在 VS Code 内部人员 
 设置中打开，以控制在此自动启动过程中启动哪些服务器 VS Code：

* `never`：不自动启动MCP服务器。
* `onlyNew`：启动从未运行过的服务器。
* `newAndOutdated`（默认）：启动从未运行过的服务器和配置已更改的服务器。

已禁用的服务器和处于错误状态的服务器不包括在自动启动过程中。

重要

对于 [Agent 主机 ](<https://code.visualstudio.com/docs/agents/concepts/agent-host>) 会话，


聊天.mcp.自动启动

在 VS Code
 中打开 在 VS Code 内部人员中打开 
 仅控制 VS Code MCP 自动启动通道。 Agent 主机和 Copilot SDK 独立管理其 MCP 服务器进程。 Agent 主机从工作区 `.mcp.json` 和用户 `~/.copilot/mcp-config.json` 文件中发现 MCP 配置。 VS Code 还转发来自受支持来源的合格服务器配置，包括 `.vscode/mcp.json`。结果，设置


聊天.mcp.自动启动

在 VS Code
 中打开 在 VS Code 内部人员 
 到 `never` 中打开不会阻止 Agent 主机会话启动其配置的 MCP 服务器。

## MCP服务器信任

工作区 MCP 服务器继承 [ 工作区信任 ](<https://code.visualstudio.com/docs/editing/workspaces/workspace-trust>)。当您信任工作区时，`.vscode/mcp.json` 和工作区根目录 `.mcp.json` 中的服务器可以在没有单独的 MCP 服务器信任提示的情况下启动，包括在其配置更改之后。在受限模式下，工作区 MCP 配置被阻止，并且这些服务器不会启动。

在信任存储库之前请检查工作区 MCP 配置，因为本地 MCP 服务器可以在您的计算机上运行代码。

MCP 来自其他来源的服务器可以使用单独的信任决策。对于这些服务器，VS Code 在服务器首次启动或其配置更改时显示一个对话框。在对话框中，选择 MCP 服务器的链接以查看其配置。

![显示 MCP 服务器信任提示的屏幕截图。](<https://code.visualstudio.com/assets/docs/agent-customization/mcp-servers/mcp-server-trust-dialog.png>)

如果您不信任MCP服务器，它将不会启动，并且聊天请求将继续，而不使用服务器提供的工具。

要重置单独的 MCP 服务器信任决策，请从命令面板运行 **MCP：重置信任 ** 命令。此命令不会更改工作区信任。

## 跨设备同步 MCP 配置

启用[设置同步](<https://code.visualstudio.com/docs/configure/settings-sync>)后，您可以跨设备同步设置和配置，包括MCP服务器配置。这使您能够维护一致的开发环境并在所有设备上访问相同的 MCP 服务器。

要将 MCP 服务器配置与设置同步同步：

1. 从命令面板运行 ** 设置同步：配置 ** 命令
2. 在同步配置列表中启用**MCP服务器**选项

## 对 MCP 服务器进行故障排除和调试

### MCP输出日志

当 VS Code 遇到 MCP 服务器问题时，它会在聊天视图中显示错误指示器。

![MCP 服务器错误](<https://code.visualstudio.com/assets/docs/agent-customization/mcp-servers/mcp-error-loading-tool.png>)

要从 Agent 自定义编辑器查看日志，请打开 **MCP 服务器 **，选择服务器的省略号 (**...**) 菜单，然后选择 ** 显示输出**。编辑器关闭，输出视图打开，并选择服务器的输出通道。

您还可以在聊天视图中选择错误通知，然后选择 ** 显示输出 **。或者，从命令面板运行 **MCP：列出服务器 **，选择服务器，然后选择 ** 显示输出 **。

![MCP 服务器错误输出](<https://code.visualstudio.com/assets/docs/agent-customization/mcp-servers/mcp-server-error-output.png>)

## 常见问题

使用 Docker 时 MCP 服务器未启动

验证命令参数是否正确以及容器是否未在分离模式下运行（`-d` 选项）。您还可以检查 MCP 服务器输出中是否有任何错误消息（请参阅 [ 故障排除 ](<#_troubleshoot-and-debug-mcp-servers>)）。

## 相关资源

* [MCP配置参考](<https://code.visualstudio.com/docs/agents/reference/mcp-configuration>)
* [使用具有agents](<https://code.visualstudio.com/docs/agents/run/tools>)的工具
* [Model Context Protocol 文档](<https://modelcontextprotocol.io/>)
* [MCP VS Code](<https://code.visualstudio.com/blogs/2026/01/26/mcp-apps-support>) 中的应用程序支持
* [发现和管理 agent 插件 ](<https://code.visualstudio.com/docs/agent-customization/agent-plugins>)，包括插件中的 [MCP 服务器 ](<https://code.visualstudio.com/docs/agent-customization/agent-plugins#_mcp-servers-in-plugins>)

10/7/2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Add and manage MCP servers in VS Code

* [View as Markdown](<https://code.visualstudio.com/docs/agent-customization/mcp-servers.md>)
* [Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fmcp-servers%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)
* [Ask in VS Code Insiders](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fmcp-servers%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fmcp-servers%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

---

Add an MCP server when a coding task needs information or actions that your agent's existing tools don't provide. For example, a server can let the agent query a database or update an issue in an external service instead of asking you to perform those steps manually.

[Model Context Protocol (MCP)](<https://modelcontextprotocol.io/>) is an open standard for connecting AI models to external tools and services. In Visual Studio Code, servers expose these capabilities as [tools](<https://code.visualstudio.com/docs/agents/run/tools>). MCP servers can also provide [resources, prompts, and interactive apps](<#_other-mcp-capabilities>).

If the tools already available meet your needs, you don't need an additional server. Adding one means configuring access and deciding whether to [trust the server](<#_mcp-server-trust>).

For background on how MCP fits into the AI customization framework, see [Customization concepts](<https://code.visualstudio.com/docs/agents/concepts/customization>) and [Tools concepts](<https://code.visualstudio.com/docs/agents/concepts/tools>).

This article covers how to add, configure, and manage MCP servers. To learn how agents invoke tools, see [Use tools with agents](<https://code.visualstudio.com/docs/agents/run/tools>).

Tip

Use the [Agent Customizations editor](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>) (Preview) to discover, create, and manage all your agent customizations in one place. Run **Chat: Open Customizations** from the Command Palette.

## Quickstart: use an MCP server in chat

This walkthrough demonstrates adding an external tool provider with the [Playwright](<https://github.com/microsoft/playwright-mcp>) MCP server. If your goal is browser interaction rather than learning MCP setup, check the [built-in browser tools](<https://code.visualstudio.com/docs/agents/run/browser-tools>) first. They don't require an MCP server.

1. Open the Extensions view (⇧⌘X (Windows, Linux Ctrl+Shift+X)) and enter `@mcp playwright` in the search field.
2. Select **Install** to install the Playwright MCP server in your user profile.
3. When prompted, confirm that you trust the server to start it. VS Code discovers the server's tools and makes them available in chat.
4. Open the Chat view (⌃⌘I (Windows, Linux Ctrl+Alt+I)) and enter a prompt that uses the Playwright tools. For example:

   ```
   Go to code.visualstudio.com, decline the cookie banner, and give me a screenshot of the homepage.
   ```

   VS Code invokes the Playwright tools to open the page in a browser, and take a screenshot. You might be asked to confirm each tool invocation.

Tip

Select the **Configure Tools** button in the chat input to see all available tools for the Playwright MCP server and toggle specific tools on or off.

## Add an MCP server

To install an MCP server from the MCP server gallery:

1. Open the Extensions view (⇧⌘X (Windows, Linux Ctrl+Shift+X)) and enter `@mcp` in the search field. This shows the list of available MCP servers in the gallery.
2. You can install an MCP server in your user profile or in your workspace:

   * To install in your user profile, select **Install**.
   * To install in your workspace, right-click the MCP server and select **Install in Workspace**. This updates the `.vscode/mcp.json` file in your workspace.
3. To view the MCP server details, select the MCP server in the list to open the details page.

Caution

Local MCP servers can run arbitrary code on your machine. Only add servers from [trusted sources](<#_mcp-server-trust>), and review the publisher and server configuration before starting it. Read the [Security documentation](<https://code.visualstudio.com/docs/agents/run/security>) for using AI in VS Code to understand the implications.

### Configure the `mcp.json` file

You can manually configure MCP servers in the following locations:

* **Workspace, VS Code format**: create or open `.vscode/mcp.json` in your project. This format defines servers in a top-level `servers` object.
* **Workspace, portable format**: create `.mcp.json` at the root of your project. This format defines servers in a top-level `mcpServers` object and works across compatible tools.
* **User profile**: run the **MCP: Open User Configuration** command to open the `mcp.json` file in your [user profile](<https://code.visualstudio.com/docs/configure/profiles>) folder. Servers configured here are available across all your workspaces. When you use multiple profiles, each profile can have its own MCP server configuration.
* **User, portable format**: create `$COPILOT_HOME/mcp-config.json`, or `~/.copilot/mcp-config.json` when `COPILOT_HOME` is not set. This format defines servers in a top-level `mcpServers` object and works across compatible Copilot tools.

Include workspace configuration in source control to share MCP servers with your team.

You can also run **MCP: Add Server** in the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)) to add a server through a guided flow. Choose **.mcp.json** to save a portable configuration at the workspace root, or **Copilot Global** to save it in `$COPILOT_HOME/mcp-config.json` with `~/.copilot/mcp-config.json` as the fallback location. The flow also lists the deprecated `.vscode/mcp.json` and VS Code user-profile destinations for compatibility. Prefer the portable destinations for new servers.

For sessions that run on [Agent Host](<https://code.visualstudio.com/docs/agents/concepts/agent-host>), the Agent Host doesn't read `.vscode/mcp.json` directly. Instead, VS Code forwards your MCP server configuration to the Agent Host, except servers that require interactive input (for example, `${input:...}` variables). For MCP configuration that is portable across the Agent Host and other Copilot tools, use a workspace `.mcp.json` file or a user `~/.copilot/mcp-config.json` file, which the Agent Host reads natively. Learn more about [behavior on the extension host](<https://code.visualstudio.com/docs/agents/concepts/agent-host#_behavior-on-the-extension-host>).

Important

Avoid hardcoding sensitive information like API keys. Use [input variables](<https://code.visualstudio.com/docs/agents/reference/mcp-configuration#_input-variables-for-sensitive-data>) or environment files instead.

The following example shows an `mcp.json` file that configures a remote MCP server and a local MCP server:

```
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp"
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@microsoft/mcp-server-playwright"]
    }
  }
}
```

VS Code provides IntelliSense for the configuration file. For the full configuration schema and field reference, see the [MCP configuration reference](<https://code.visualstudio.com/docs/agents/reference/mcp-configuration>).

Note

MCP servers run wherever they are configured. Servers in your user profile run locally. If you're connected to a [remote](<https://code.visualstudio.com/docs/remote/remote-overview>) and want a server to run on the remote machine, define it in the workspace settings or remote user settings (**MCP: Open Remote User Configuration**).

### Other options to add an MCP server

Add an MCP server to a dev container

MCP servers can be configured in Dev Containers through the `devcontainer.json` file. This allows you to include MCP server configurations as part of your containerized development environment.

To configure MCP servers in a Dev Container, add the server configuration to the `customizations.vscode.mcp` section:

```
{
  "image": "mcr.microsoft.com/devcontainers/typescript-node:latest",
  "customizations": {
    "vscode": {
      "mcp": {
        "servers": {
          "playwright": {
            "command": "npx",
            "args": ["-y", "@microsoft/mcp-server-playwright"]
          }
        }
      }
    }
  }
}
```

When the Dev Container is created, VS Code automatically writes the MCP server configurations to the remote `mcp.json` file, making them available in your containerized development environment.


Install an MCP server from the Customization Marketplace (Experimental)

The Customization Marketplace provides an alternative way to find MCP servers from configured sources:

1. Turn on the 


   chat.customizations.marketplace.enabled

   Open in VS Code
   Open in VS Code Insiders
    setting.
2. Run **Chat: Open Customizations** from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)), and then select **Discover**.
3. From the source menu, select **GitHub Feed**, and then search for a server by name or add `@type:mcp` to your search.
4. Select a server and review its source, product image, publisher details, and installation requirements.
5. Select **Install**.

Some servers don't support automatic installation. Follow the publisher's setup instructions when they are provided.


Automatically discover MCP servers

VS Code can automatically detect and reuse MCP server configurations from other applications, such as Claude Desktop.

With the 


chat.mcp.discovery.enabled

Open in VS Code
Open in VS Code Insiders
 setting, you can select one or more tools from which to discover their MCP server configuration.

Supported sources include Claude Desktop, GitHub Copilot CLI, Cursor, and Windsurf. Learn more about [automatic MCP server discovery](<https://code.visualstudio.com/docs/agents/reference/mcp-configuration#_automatic-mcp-server-discovery>), including configuration locations and remote behavior.


Install an MCP server from the command line

You can also use the VS Code command-line interface to add an MCP server to your user profile or to a workspace.

To add an MCP server to your user profile, use the `--add-mcp` VS Code command line option, and provide the JSON server configuration in the form `{\"name\":\"server-name\",\"command\":...}`.

```
code --add-mcp "{\"name\":\"my-server\",\"command\": \"uvx\",\"args\": [\"mcp-server-fetch\"]}"
```

## Other MCP capabilities

Beyond tools, MCP servers can provide other capabilities:

| Capability | Description | How to use |
| --- | --- | --- |
| **Resources** | Access data from MCP servers as context in your prompts, such as files, database tables, or API responses. Resources provide read-only context that you attach to a chat request. | In the Chat view, select **Add Context** > **MCP Resources**. You can also use the **MCP: Browse Resources** command. |
| **Prompts** | Use preconfigured prompt templates from MCP servers to standardize common tasks. Each MCP server can expose its own set of prompts tailored to its capabilities. | Type `/<MCP server>.<prompt>` in the chat input. |
| **MCP Apps** | Get interactive UI components like forms, visualizations, and drag-and-drop lists rendered directly in chat. MCP Apps enable richer interactions beyond text responses. Learn more in the [MCP Apps blog post](<https://code.visualstudio.com/blogs/2026/01/26/mcp-apps-support>). | MCP Apps appear inline when an MCP server supports them. |

## Sandbox MCP servers

On macOS and Linux, you can enable sandboxing for locally-running stdio MCP servers to restrict their access to the file system and network. Sandboxed servers run in an isolated environment and can only access the file paths and network domains that you explicitly permit.

Note

This per-server sandbox is separate from automatic sandboxing for MCP servers launched by the Agent Host. When Agent Host sandboxing is on, 


chat.agent.sandbox.mcpServers

Open in VS Code
Open in VS Code Insiders
 applies by default and has its own configuration and scope. Learn more about [agent sandboxing](<https://code.visualstudio.com/docs/agents/run/agent-sandboxing>).

To enable sandboxing for a server, set `"sandboxEnabled": true` in the server configuration in your `mcp.json` file. You can further customize the sandbox restrictions by adding a top-level `sandbox` object with specific file system and network rules.

The following example shows how to enable sandboxing for a local MCP server and restrict its access to only write to files in the workspace and access a specific API domain:

```
{
  "servers": {
    "myServer": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@example/mcp-server"],
      "sandboxEnabled": true
    }
  },
  "sandbox": {
    "filesystem": {
      "allowWrite": ["${workspaceFolder}"]
    },
    "network": {
      "allowedDomains": ["api.example.com"]
    }
  }
}
```

When sandboxing is enabled, tool calls from the server are auto-approved because they run in a controlled environment.

For the full sandbox configuration schema, see the [Sandbox configuration](<https://code.visualstudio.com/docs/agents/reference/mcp-configuration#_sandbox-configuration>) reference.

Note

Sandboxing is currently not available on Windows.

## Manage MCP servers

VS Code provides several options to manage your MCP servers, such as starting or stopping a server, viewing logs, uninstalling, or clearing cached tools.

| Method | Description |  |
| --- | --- | --- |
| **Agent Customizations editor** | Open the **MCP Servers** section, select the ellipsis (**...**) next to a server, and choose an action. |  |
| **Extensions view** | Right-click a server in the **MCP SERVERS - INSTALLED** section or select the gear icon. | Screenshot showing the MCP servers in the Extensions view. |
| **`mcp.json` editor** | Open the configuration file and use the inline actions (code lenses). Use **MCP: Open User Configuration** or **MCP: Open Workspace Folder Configuration** to open the file. | MCP server configuration with lenses to manage server. |
| **Command Palette** | Run **MCP: List Servers**, select a server, and choose an action. | Screenshot showing the actions for an MCP server in the Command Palette. |

## Enable or disable MCP servers

You can enable or disable an MCP server globally or for a specific workspace. When an MCP server is disabled, it does not start and its tools, prompts, resources, and MCP apps are excluded from chat.

To enable or disable an MCP server:

* Right-click a server in the **MCP SERVERS - INSTALLED** section of the Extensions view and select **Enable** or **Disable**.
* Run **MCP: List Servers** from the Command Palette, select a server, and choose **Enable** or **Disable**.
* Use the [Agent Customizations editor](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>) to toggle the server's enabled state.

The enable/disable state is stored separately from the server configuration in `mcp.json`, so it does not affect shared configuration files.

## Centrally manage access to MCP servers in VS Code

Organizations can centrally manage access to MCP servers with Copilot enterprise-managed settings. Learn more about [enterprise management of MCP servers](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-mcp-usage/configure-enterprise-allowlist>).

## Automatically start MCP servers

When you submit a chat message, VS Code can automatically start MCP servers so that their tools are available to the chat. Use the 


chat.mcp.autostart

Open in VS Code
Open in VS Code Insiders
 setting to control which servers VS Code starts during this autostart pass:

* `never`: Don't automatically start MCP servers.
* `onlyNew`: Start servers that have never run.
* `newAndOutdated` (default): Start servers that have never run and servers whose configuration has changed.

Disabled servers and servers in an error state are excluded from the autostart pass.

Important

For [Agent Host](<https://code.visualstudio.com/docs/agents/concepts/agent-host>) sessions, 


chat.mcp.autostart

Open in VS Code
Open in VS Code Insiders
 only controls the VS Code MCP autostart pass. The Agent Host and Copilot SDK manage their MCP server processes independently. The Agent Host discovers MCP configuration from workspace `.mcp.json` and user `~/.copilot/mcp-config.json` files. VS Code also forwards eligible server configurations from supported sources, including `.vscode/mcp.json`. As a result, setting 


chat.mcp.autostart

Open in VS Code
Open in VS Code Insiders
 to `never` doesn't prevent an Agent Host session from starting its configured MCP servers.

## MCP server trust

Workspace MCP servers inherit [Workspace Trust](<https://code.visualstudio.com/docs/editing/workspaces/workspace-trust>). When you trust a workspace, servers in `.vscode/mcp.json` and workspace-root `.mcp.json` can start without a separate MCP server trust prompt, including after their configuration changes. In restricted mode, workspace MCP configuration is blocked and these servers don't start.

Review workspace MCP configuration before you trust a repository because local MCP servers can run code on your machine.

MCP servers from other sources can use a separate trust decision. For these servers, VS Code shows a dialog when a server first starts or its configuration changes. In the dialog, select the link to the MCP server to review its configuration.

![Screenshot showing the MCP server trust prompt.](<https://code.visualstudio.com/assets/docs/agent-customization/mcp-servers/mcp-server-trust-dialog.png>)

If you don't trust the MCP server, it will not be started, and chat requests will continue without using the tools provided by the server.

To reset separate MCP server trust decisions, run the **MCP: Reset Trust** command from the Command Palette. This command doesn't change Workspace Trust.

## Synchronize MCP configuration across devices

With [Settings Sync](<https://code.visualstudio.com/docs/configure/settings-sync>) enabled, you can synchronize settings and configurations across devices, including MCP server configurations. This enables you to maintain a consistent development environment and access the same MCP servers on all your devices.

To synchronize MCP server configuration with Settings Sync:

1. Run the **Settings Sync: Configure** command from the Command Palette
2. Enable the **MCP Servers** option in the list of synchronized configurations

## Troubleshoot and debug MCP servers

### MCP output log

When VS Code encounters an issue with an MCP server, it shows an error indicator in the Chat view.

![MCP Server Error](<https://code.visualstudio.com/assets/docs/agent-customization/mcp-servers/mcp-error-loading-tool.png>)

To view the logs from the Agent Customizations editor, open **MCP Servers**, select the server's ellipsis (**...**) menu, and then select **Show Output**. The editor closes and the Output view opens with the server's output channel selected.

You can also select the error notification in the Chat view and then select **Show Output**. Alternatively, run **MCP: List Servers** from the Command Palette, select the server, and then choose **Show Output**.

![MCP Server Error Output](<https://code.visualstudio.com/assets/docs/agent-customization/mcp-servers/mcp-server-error-output.png>)

## Frequently asked questions

The MCP server is not starting when using Docker

Verify that the command arguments are correct and that the container is not running in detached mode (`-d` option). You can also check the MCP server output for any error messages (see [Troubleshooting](<#_troubleshoot-and-debug-mcp-servers>)).

## Related resources

* [MCP configuration reference](<https://code.visualstudio.com/docs/agents/reference/mcp-configuration>)
* [Use tools with agents](<https://code.visualstudio.com/docs/agents/run/tools>)
* [Model Context Protocol Documentation](<https://modelcontextprotocol.io/>)
* [MCP Apps support in VS Code](<https://code.visualstudio.com/blogs/2026/01/26/mcp-apps-support>)
* [Discover and manage agent plugins](<https://code.visualstudio.com/docs/agent-customization/agent-plugins>), including [MCP servers in plugins](<https://code.visualstudio.com/docs/agent-customization/agent-plugins#_mcp-servers-in-plugins>)

10/7/2026


</details>
