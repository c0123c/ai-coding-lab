# VS Code MCP 配置参考

介绍配置文件、标准输入输出与 HTTP 服务、沙箱、敏感输入变量、开发模式、发现机制和相关命令。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/mcp-configuration.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/mcp-configuration.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code MCP 配置参考.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: a3e1f7c2-8d4b-4f9a-b6e5-2c8d3f1a9b7e
DateApproved: 10/7/2026
MetaDescription: Configure MCP servers in {% data variables.product.prodname_vscode %} with reference details for file formats, commands, settings, and sandboxing.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- mcp
- model context protocol
- mcp.json
- configuration
- sandbox
- tools
- copilot
- reference
- ai
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# MCP配置参考

本文提供了MCP服务器配置文件格式、相关命令以及{% data variables.product.prodname_vscode_shortname %}中的设置的参考。有关添加和管理 MCP 服务器的信息，请参阅[添加和管理 MCP 服务器](<https://github.com/docs/agent-customization/mcp-servers.md>)。

## 配置文件

MCP 服务器配置使用以下文件格式之一：

* {% data variables.product.prodname_vscode_shortname %} 格式存储在您工作区的 `.vscode/mcp.json` 或 [ 用户配置文件 ](<https://github.com/docs/configure/profiles.md>) 中。它在顶级 `servers` 对象中定义服务器。 {% data variables.product.prodname_vscode_shortname %}为此格式提供了IntelliSense。
* 可移植格式存储在工作区根目录的 `.mcp.json` 中，或者存储在用户的 `~/.copilot/mcp-config.json` 中。它在顶级 `mcpServers` 对象中定义服务器。

[Agent主机](<https://github.com/docs/agents/concepts/agent-host.md>)直接读取可移植格式。 {% data variables.product.prodname_vscode_shortname %} 将服务器从 `.vscode/mcp.json` 转发到 Agent 主机，需要交互式输入的服务器除外，例如 `${input:...}` 变量。请参阅 [ 分机主机 ](<https://github.com/docs/agents/concepts/agent-host.md#behavior-on-the-extension-host>) 上的行为。

### 配置结构

配置文件包含三个主要部分：

* **`"servers": {}`**：将服务器名称映射到其配置的对象。每个键是服务器名称，值是服务器配置对象。根据服务器类型，需要不同的字段。

* **`"inputs": []`**：敏感信息（如 API 键）的输入变量定义的可选数组。

* **`"sandbox": {}`**：一个可选对象，定义沙盒服务器的文件系统和网络访问规则。请参阅[沙箱配置](<#沙盒配置>)。仅适用于 macOS 和 Linux。

您可以在服务器配置中使用[预定义变量](<https://github.com/docs/reference/variables-reference.md>)，例如引用工作区文件夹(`${workspaceFolder}`)。

### 标准 I/O (stdio) 服务器

对于通过标准输入和输出流进行通信的服务器，请使用此配置。这是本地运行的 MCP 服务器最常见的类型。

|领域|必填|描述|示例|
|-------|----------|-------------|----------|
| `type` |是的|服务器连接类型| `"stdio"` |
| `command` |是的|启动服务器可执行文件的命令。必须在您的系统路径上可用或包含其完整路径。| `"npx"`, `"node"`, `"python"`, `"docker"` |
| `args` |否|传递给命令的参数数组| `["server.py", "--port", "3000"]` |
| `cwd` |否|服务器命令的工作目录。在工作区中运行时默认为工作区文件夹。| `"${workspaceFolder}"` |
| `env` |否|服务器的环境变量。值可以是字符串、数字或 null。| `{"API_KEY": "${input:api-key}"}` |
| `envFile` |否|用于加载更多变量的环境文件的路径| `"${workspaceFolder}/.env"` |
| `dev` |否|用于监视文件更改和调试服务器的开发模式设置。参见【[】开发模式【](<#发展模式>)】。| `{"watch": "src/**/*.ts"}` |
| `sandboxEnabled` |否|在沙盒环境中运行服务器。仅在 macOS 和 Linux 上受支持。| `true` |

> [！NOTE]
> 将 Docker 与 stdio 服务器结合使用时，请勿使用分离选项 (`-d`)。服务器必须在前台运行才能与{% data variables.product.prodname_vscode_shortname %}通信。



<summary>本地服务器配置示例</summary>

此示例显示使用 `npx` 的基本本地 MCP 服务器的最小配置：

```json
{
    "servers": {
        "memory": {
            "command": "npx",
            "args": [
            "-y",
            "@modelcontextprotocol/server-memory"
            ]
        }
    }
}
```




### 沙盒配置

您可以为本地运行的 stdio MCP 服务器启用沙箱，以限制它们对文件系统和网络的访问。沙盒服务器只能访问您明确允许的文件系统路径和网络域。沙盒仅在 macOS 和 Linux 上可用。

要为服务器启用沙箱，请在其配置中设置 `\"sandboxEnabled\": true`。然后，定义一个顶级`sandbox`对象来指定文件系统和网络访问规则。 `sandbox` 对象是 `servers` 和 `inputs` 的同级对象，其规则适用于所有沙盒服务器。当沙盒服务器需要当前规则不允许的访问时，请检查服务器输出中是否有错误消息并相应更新 `sandbox` 配置。

> [!NOTE]
> 启用沙箱后，工具确认会自动批准，因为服务器在受控环境中运行。

`sandbox` 对象支持以下属性：

|财产|类型|描述|
|----------|------|-------------|
| `filesystem.allowWrite` |字符串[]|允许服务器写入的文件路径。|
| `filesystem.denyRead` |字符串[]|服务器不允许读取的文件路径。|
| `filesystem.denyWrite` |字符串[]|服务器不允许写入的文件路径。|
| `network.allowedDomains` |字符串[]|允许服务器访问的域。支持通配符，例如 `*.example.com`。|
| `network.deniedDomains` |字符串[]|服务器不允许访问的域。|

您可以在文件系统路径值中使用[预定义变量](<https://github.com/docs/reference/variables-reference.md>)，例如`${workspaceFolder}`。



<summary>沙箱配置示例</summary>

此示例启用沙箱并授予对工作区的写入权限，拒绝对 `.ssh` 目录的读取权限，并允许对特定域的网络访问：

```json
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
            "allowWrite": ["${workspaceFolder}"],
            "denyRead": ["${userHome}/.ssh"]
        },
        "network": {
            "allowedDomains": ["api.example.com", "*.cdn.example.com"]
        }
    }
}
```




### HTTP 和服务器发送事件 (SSE) 服务器

对于通过 HTTP 进行通信的服务器，请使用此配置。 {% data variables.product.prodname_vscode_shortname %} 首先尝试 HTTP 流传输，如果不支持 HTTP，则回退到 SSE。

|领域|必填|描述|示例|
|-------|----------|-------------|----------|
| `type` |是的|服务器连接类型| `"http"`, `"sse"` |
| `url` |是的|服务器的URL| `"http://localhost:3000"`, `"https://api.example.com/mcp"` |
| `headers` |否|HTTP 用于身份验证或配置的标头| `{"Authorization": "Bearer ${input:api-token}"}` |
| `oauth` |否|用于与服务器进行身份验证的 OAuth 配置| `{"clientId": "example-client-id"}` |

除了网络上可用的服务器之外，{% data variables.product.prodname_vscode_shortname %} 还可以通过在 Windows 上以 `unix:///path/to/server.sock` 或 `pipe:///pipe/named-pipe` 形式指定套接字或管道路径，连接到侦听 Unix 套接字或 Windows 命名管道上的 HTTP 流量的 MCP 服务器。您可以使用 URL 片段指定子路径，例如 `unix:///tmp/server.sock#/mcp/subpath`。

`oauth` 对象支持以下属性：

|财产|类型|必填|描述|
|----------|------|----------|-------------|
| `clientId` |字符串|是的|与服务器进行身份验证时使用的 OAuth 客户端 ID。|
| `enterpriseManaged` |布尔值|否|（预览）使用 OAuth 身份断言授权授予 (ID-JAG)，通过 `mcp.enterpriseManagedAuth.idp` 设置配置的企业单点登录 (SSO) 颁发者进行身份验证。一次性登录后，后续企业管理的服务器将以静默方式连接。默认为 `false`。|

> [!NOTE]
> 配置`oauth`时，{% data variables.product.prodname_vscode_shortname %}自动处理OAuth流程。首次连接到服务器时将打开一个浏览器窗口以进行授权。



<summary>远程服务器配置示例</summary>

此示例显示了无需身份验证的远程 MCP 服务器的最小配置：

```json
{
    "servers": {
        "context7": {
            "type": "http",
            "url": "https://mcp.context7.com/mcp"
        }
    }
}
```






<summary>具有 OAuth 的示例 HTTP 服务器 </summary>

此示例显示使用 OAuth 进行身份验证的 MCP 服务器的配置。首次使用时，{% data variables.product.prodname_vscode_shortname %} 将打开浏览器窗口以完成 OAuth 流程。

```json
{
    "servers": {
        "slack": {
            "type": "http",
            "url": "https://mcp.slack.com/mcp",
            "oauth": {
                "clientId": "example-client-id"
            }
        }
    }
}
```




### 敏感数据的输入变量

输入变量可让您定义配置值的占位符，从而无需直接在服务器配置中对 API 键或密码等敏感信息进行硬编码。

当您使用 `${input:variable-id}` 引用输入变量时，{% data variables.product.prodname_vscode_shortname %} 会在服务器首次启动时提示您输入值。然后该值被安全地存储以供后续使用。在 {% data variables.product.prodname_vscode_shortname %} 中了解有关 [ 输入变量 ](<https://github.com/docs/reference/variables-reference.md#input-variables>) 的更多信息。

每个输入变量都有一个 `type`，用于确定 {% data variables.product.prodname_vscode_shortname %} 如何提示输入值。支持以下输入类型：

* `promptString`：打开一个输入框以询问用户自由文本值。
* `pickString`：显示选项列表供用户选择。
* `command`：运行命令并使用其结果作为输入值。

**常用属性：**

|领域|必填|描述|示例|
|-------|----------|-------------|---------|
| `type` |是的|输入提示类型：`promptString`、`pickString`或`command`| `"promptString"` |
| `id` |是的|在服务器配置中引用的唯一标识符| `"api-key"`, `"database-url"` |

**`promptString` 属性：**

|领域|必填|描述|示例|
|-------|----------|-------------|---------|
| `description` |是的|用户友好的提示文字| `"GitHub Personal Access Token"` |
| `default` |否|输入的默认值| `"https://localhost"` |
| `password` |否|隐藏键入的输入（默认值： false）|`true` 用于 API 密钥和密码|

**`pickString` 属性：**

|领域|必填|描述|示例|
|-------|----------|-------------|---------|
| `description` |是的|用户友好的提示文字| `"Select an environment"` |
| `options` |是的|一系列选项可供选择。每个选项都是一个字符串，或者是具有 `label` 和 `value` 属性的对象。| `["dev", "prod"]` |
| `default` |否|输入的默认值| `"dev"` |

**`command` 属性：**

|领域|必填|描述|示例|
|-------|----------|-------------|---------|
| `command` |是的|运行命令ID获取输入值| `"myExtension.getApiKey"` |
| `args` |否|传递给命令的参数。可以是字符串、数组或对象。| `{ "scope": "global" }` |



<summary>带有输入变量的服务器配置示例</summary>

此示例配置需要 API 密钥的本地服务器：

```json
{
    "inputs": [
        {
            "type": "promptString",
            "id": "perplexity-key",
            "description": "Perplexity API Key",
            "password": true
        }
    ],
    "servers": {
        "perplexity": {
            "type": "stdio",
            "command": "npx",
            "args": [
                "-y",
                "server-perplexity-ask"
            ],
            "env": {
                "PERPLEXITY_API_KEY": "${input:perplexity-key}"
            }
        }
    }
}
```




### 发展模式

您可以通过在服务器配置中添加 `dev` 密钥来为 MCP 服务器启用 _ 开发模式 _。这是一个具有两个属性的对象：

* `watch`：一个 glob 模式或一组 glob 模式，用于监视重新启动 MCP 服务器的文件更改。适用于所有服务器类型。
* `debug`：使您能够使用 MCP 服务器设置调试器。目前，{% data variables.product.prodname_vscode_shortname %}支持调试Node.js和PythonMCP服务器。仅适用于 stdio 服务器。

在 MCP 开发指南中了解有关 [MCP 开发模式 ](<https://github.com/api/extension-guides/ai/mcp.md#mcp-development-mode-in-vs-code>) 的更多信息。

### 服务器命名约定

定义 MCP 服务器时，请遵循以下服务器名称命名约定：

* 使用 camelCase 作为服务器名称，例如“uiTesting”或“githubIntegration”
* 避免使用空格或特殊字符
* 为每个服务器使用唯一的名称以避免冲突
* 使用反映服务器功能或品牌的描述性名称，例如“github”或“database”

## 自动 MCP 服务器发现

{% data variables.product.prodname_vscode_shortname %} 可以自动检测并重用受支持应用程序中的 MCP 服务器配置。使用 `setting(chat.mcp.discovery.enabled)` 选择发现源。

|来源|设置属性|配置位置|
|--------|------------------|------------------------|
|Claude 桌面| `claude-desktop` |Windows：`%APPDATA%\Claude\claude_desktop_config.json`<br/>macOS：`~/Library/Application Support/Claude/claude_desktop_config.json`<br/>Linux：`$XDG_CONFIG_HOME/Claude/claude_desktop_config.json`，如果未设置`XDG_CONFIG_HOME`，则为`~/.config/Claude/claude_desktop_config.json`|
| {% data variables.copilot.copilot_cli %} | `copilot` |`<COPILOT_HOME>/mcp-config.json`，如果未设置 `COPILOT_HOME`，则为 `~/.copilot/mcp-config.json`|
|Cursor（全球）| `cursor-global` | `~/.cursor/mcp.json` |
|Cursor（工作区）| `cursor-workspace` | `<workspace>/.cursor/mcp.json` |
| Windsurf | `windsurf` | `~/.codeium/windsurf/mcp_config.json` |

默认情况下，所有发现源均处于关闭状态。在远程窗口中，{% data variables.product.prodname_vscode_shortname %} 解析远程环境中的配置位置和环境变量。

对于 {% data variables.copilot.copilot_cli %}，设置 `COPILOT_HOME` 会替换默认的 `~/.copilot` 位置。 {% data variables.product.prodname_vscode_shortname %} 不检查这两个位置。

> [!NOTE]
> Agent 主持人会议阅读 {% data variables.copilot.copilot_cli %} MCP 独立配置。发现设置不会改变 Agent 主机收到 MCP 服务器。了解更多关于 [MCP 扩展主机上的配置行为](<https://github.com/docs/agents/concepts/agent-host.md#behavior-on-the-extension-host>).

## 命令

下表列出了命令面板 (`kb(workbench.action.showCommands)`) 中可用的 MCP- 相关命令。

|命令|描述|
|---------|-------------|
|**MCP：添加服务器**|将新的 MCP 服务器添加到您的工作区或用户配置文件。|
|**MCP：浏览MCP服务器**|在扩展视图中打开 MCP 服务器库。|
|**MCP：浏览资源**|浏览MCP服务器提供的资源。|
|**MCP：从清单安装服务器**|从 MCP 清单文件安装 MCP 服务器。|
|**MCP：列出服务器**|列出所有已配置的 MCP 服务器并执行启动、停止、重新启动或显示输出等操作。|
|**MCP：打开远程用户配置**|打开远程环境的 `mcp.json` 文件。|
|**MCP：打开用户配置**|打开用户配置文件中的 `mcp.json` 文件。|
|**MCP：打开工作区文件夹MCP配置**|在工作区中打开 `.vscode/mcp.json` 文件。|
|**MCP：重置缓存工具**|清除 MCP 服务器的工具缓存列表。当服务器的工具发生更改时使用此选项。|
|**MCP：重置信任**|从非工作区源重置 MCP 服务器的单独信任决策。 Workspace MCP 服务器继承 Workspace Trust 并且不受影响。|
|**MCP：显示已安装的服务器**|显示所有已安装的 MCP 服务器的列表。|

## 设置

有关 {% data variables.product.prodname_vscode_shortname %} AI 设置的完整列表，请参阅 [AI 设置参考 ](<https://github.com/docs/agents/reference/ai-settings.md>)。以下设置特定于 MCP 服务器。

|设置|描述|
|---------|-------------|
| `setting(chat.mcp.access)` |管理哪些MCP服务器可以在{% data variables.product.prodname_vscode_shortname %}中使用。|
| `setting(chat.mcp.discovery.enabled)` |配置从其他应用程序自动发现 MCP 服务器配置。|
| `setting(chat.mcp.autostart)` `feature(mcp-autostart)` |控制当您提交聊天消息时自动启动哪些MCP服务器{% data variables.product.prodname_vscode_shortname %}。此设置不控制 Agent 主机管理的服务器。|
| `setting(chat.mcp.serverSampling)` |配置哪些模型暴露给 MCP 服务器进行采样（在后台发出请求）。|
| `setting(chat.mcp.apps.enabled)` `feature(mcp-apps)` |启用或禁用MCP应用程序，它们是MCP服务器提供的丰富的用户界面。|

## 相关资源

* [添加和管理MCP服务器](<https://github.com/docs/agent-customization/mcp-servers.md>)
* [Model Context Protocol 文档](<https://modelcontextprotocol.io/>)
* [MCP工具扩展性概述](<https://github.com/api/extension-guides/ai/ai-extensibility-overview.md#mcp-tool>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# MCP configuration reference

This article provides a reference for the MCP server configuration file format, related commands, and settings in {% data variables.product.prodname_vscode_shortname %}. For information about adding and managing MCP servers, see [Add and manage MCP servers](<https://github.com/docs/agent-customization/mcp-servers.md>).

## Configuration file

MCP server configuration uses one of the following file formats:

* The {% data variables.product.prodname_vscode_shortname %} format is stored in `.vscode/mcp.json` in your workspace or in your [user profile](<https://github.com/docs/configure/profiles.md>). It defines servers in a top-level `servers` object. {% data variables.product.prodname_vscode_shortname %} provides IntelliSense for this format.
* The portable format is stored in `.mcp.json` at the root of your workspace or in `~/.copilot/mcp-config.json` for your user. It defines servers in a top-level `mcpServers` object.

The [Agent Host](<https://github.com/docs/agents/concepts/agent-host.md>) reads the portable format directly. {% data variables.product.prodname_vscode_shortname %} forwards servers from `.vscode/mcp.json` to the Agent Host, except servers that require interactive input, such as `${input:...}` variables. See [behavior on the extension host](<https://github.com/docs/agents/concepts/agent-host.md#behavior-on-the-extension-host>).

### Configuration structure

The configuration file has three main sections:

* **`"servers": {}`**: an object that maps server names to their configurations. Each key is the server name, and the value is the server configuration object. Depending on the server type, different fields are required.

* **`"inputs": []`**: an optional array of input variable definitions for sensitive information like API keys.

* **`"sandbox": {}`**: an optional object that defines file system and network access rules for sandboxed servers. See [Sandbox configuration](<#sandbox-configuration>). Only applies on macOS and Linux.

You can use [predefined variables](<https://github.com/docs/reference/variables-reference.md>) in the server configuration, for example to refer to the workspace folder (`${workspaceFolder}`).

### Standard I/O (stdio) servers

Use this configuration for servers that communicate through standard input and output streams. This is the most common type for locally-run MCP servers.

| Field | Required | Description | Examples |
|-------|----------|-------------|----------|
| `type` | Yes | Server connection type | `"stdio"` |
| `command` | Yes | Command to start the server executable. Must be available on your system path or contain its full path. | `"npx"`, `"node"`, `"python"`, `"docker"` |
| `args` | No | Array of arguments passed to the command | `["server.py", "--port", "3000"]` |
| `cwd` | No | Working directory for the server command. Defaults to the workspace folder when run in a workspace. | `"${workspaceFolder}"` |
| `env` | No | Environment variables for the server. Values can be strings, numbers, or null. | `{"API_KEY": "${input:api-key}"}` |
| `envFile` | No | Path to an environment file to load more variables | `"${workspaceFolder}/.env"` |
| `dev` | No | Development mode settings to watch for file changes and debug the server. See [Development mode](<#development-mode>). | `{"watch": "src/**/*.ts"}` |
| `sandboxEnabled` | No | Run the server in a sandboxed environment. Only supported on macOS and Linux. | `true` |

> [!NOTE]
> When using Docker with stdio servers, don't use the detach option (`-d`). The server must run in the foreground to communicate with {% data variables.product.prodname_vscode_shortname %}.



<summary>Example local server configuration</summary>

This example shows the minimal configuration for a basic, local MCP server using `npx`:

```json
{
    "servers": {
        "memory": {
            "command": "npx",
            "args": [
            "-y",
            "@modelcontextprotocol/server-memory"
            ]
        }
    }
}
```




### Sandbox configuration

You can enable sandboxing for locally-running stdio MCP servers to restrict their access to the file system and network. Sandboxed servers can only access the file system paths and network domains that you explicitly permit. Sandboxing is available on macOS and Linux only.

To enable sandboxing for a server, set `\"sandboxEnabled\": true` in its configuration. Then, define a top-level `sandbox` object to specify the file system and network access rules. The `sandbox` object is a sibling of `servers` and `inputs`, and its rules apply to all sandboxed servers. When a sandboxed server needs access that the current rules don't permit, check the server output for error messages and update the `sandbox` configuration accordingly.

> [!NOTE]
> When sandboxing is enabled, tool confirmations are auto-approved because the server runs in a controlled environment.

The `sandbox` object supports the following properties:

| Property | Type | Description |
|----------|------|-------------|
| `filesystem.allowWrite` | string[] | File paths that the server is allowed to write to. |
| `filesystem.denyRead` | string[] | File paths that the server is not allowed to read. |
| `filesystem.denyWrite` | string[] | File paths that the server is not allowed to write to. |
| `network.allowedDomains` | string[] | Domains that the server is allowed to access. Wildcards are supported, for example `*.example.com`. |
| `network.deniedDomains` | string[] | Domains that the server is not allowed to access. |

You can use [predefined variables](<https://github.com/docs/reference/variables-reference.md>), such as `${workspaceFolder}`, in file system path values.



<summary>Example sandbox configuration</summary>

This example enables sandboxing and grants write access to the workspace, denies read access to the `.ssh` directory, and allows network access to specific domains:

```json
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
            "allowWrite": ["${workspaceFolder}"],
            "denyRead": ["${userHome}/.ssh"]
        },
        "network": {
            "allowedDomains": ["api.example.com", "*.cdn.example.com"]
        }
    }
}
```




### HTTP and Server-Sent Events (SSE) servers

Use this configuration for servers that communicate over HTTP. {% data variables.product.prodname_vscode_shortname %} first tries the HTTP Stream transport and falls back to SSE if HTTP is not supported.

| Field | Required | Description | Examples |
|-------|----------|-------------|----------|
| `type` | Yes | Server connection type | `"http"`, `"sse"` |
| `url` | Yes | URL of the server | `"http://localhost:3000"`, `"https://api.example.com/mcp"` |
| `headers` | No | HTTP headers for authentication or configuration | `{"Authorization": "Bearer ${input:api-token}"}` |
| `oauth` | No | OAuth configuration for authenticating with the server | `{"clientId": "example-client-id"}` |

In addition to servers available over the network, {% data variables.product.prodname_vscode_shortname %} can connect to MCP servers listening for HTTP traffic on Unix sockets or Windows named pipes by specifying the socket or pipe path in the form `unix:///path/to/server.sock` or `pipe:///pipe/named-pipe` on Windows. You can specify subpaths by using a URL fragment, such as `unix:///tmp/server.sock#/mcp/subpath`.

The `oauth` object supports the following properties:

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `clientId` | string | Yes | The OAuth client ID to use when authenticating with the server. |
| `enterpriseManaged` | boolean | No | (Preview) Authenticate through the enterprise single sign-on (SSO) issuer configured by the `mcp.enterpriseManagedAuth.idp` setting, using the OAuth Identity Assertion Authorization Grant (ID-JAG). After a one-time sign-in, subsequent enterprise-managed servers connect silently. Default is `false`. |

> [!NOTE]
> When `oauth` is configured, {% data variables.product.prodname_vscode_shortname %} handles the OAuth flow automatically. A browser window opens for authorization on the first connection to the server.



<summary>Example remote server configuration</summary>

This example shows the minimal configuration for a remote MCP server without authentication:

```json
{
    "servers": {
        "context7": {
            "type": "http",
            "url": "https://mcp.context7.com/mcp"
        }
    }
}
```






<summary>Example HTTP server with OAuth</summary>

This example shows the configuration for an MCP server that uses OAuth for authentication. On first use, {% data variables.product.prodname_vscode_shortname %} opens a browser window to complete the OAuth flow.

```json
{
    "servers": {
        "slack": {
            "type": "http",
            "url": "https://mcp.slack.com/mcp",
            "oauth": {
                "clientId": "example-client-id"
            }
        }
    }
}
```




### Input variables for sensitive data

Input variables let you define placeholders for configuration values, avoiding the need to hardcode sensitive information like API keys or passwords directly in the server configuration.

When you reference an input variable using `${input:variable-id}`, {% data variables.product.prodname_vscode_shortname %} prompts you for the value when the server starts for the first time. The value is then securely stored for subsequent use. Learn more about [input variables](<https://github.com/docs/reference/variables-reference.md#input-variables>) in {% data variables.product.prodname_vscode_shortname %}.

Each input variable has a `type` that determines how {% data variables.product.prodname_vscode_shortname %} prompts for the value. The following input types are supported:

* `promptString`: opens an input box to ask the user for a free-text value.
* `pickString`: shows a list of options for the user to choose from.
* `command`: runs a command and uses its result as the input value.

**Common properties:**

| Field | Required | Description | Example |
|-------|----------|-------------|---------|
| `type` | Yes | Type of input prompt: `promptString`, `pickString`, or `command` | `"promptString"` |
| `id` | Yes | Unique identifier to reference in server config | `"api-key"`, `"database-url"` |

**`promptString` properties:**

| Field | Required | Description | Example |
|-------|----------|-------------|---------|
| `description` | Yes | User-friendly prompt text | `"GitHub Personal Access Token"` |
| `default` | No | Default value for the input | `"https://localhost"` |
| `password` | No | Hide typed input (default: false) | `true` for API keys and passwords |

**`pickString` properties:**

| Field | Required | Description | Example |
|-------|----------|-------------|---------|
| `description` | Yes | User-friendly prompt text | `"Select an environment"` |
| `options` | Yes | Array of options to choose from. Each option is a string, or an object with `label` and `value` properties. | `["dev", "prod"]` |
| `default` | No | Default value for the input | `"dev"` |

**`command` properties:**

| Field | Required | Description | Example |
|-------|----------|-------------|---------|
| `command` | Yes | Command ID to run to obtain the input value | `"myExtension.getApiKey"` |
| `args` | No | Arguments passed to the command. Can be a string, array, or object. | `{ "scope": "global" }` |



<summary>Example server configuration with input variables</summary>

This example configures a local server that requires an API key:

```json
{
    "inputs": [
        {
            "type": "promptString",
            "id": "perplexity-key",
            "description": "Perplexity API Key",
            "password": true
        }
    ],
    "servers": {
        "perplexity": {
            "type": "stdio",
            "command": "npx",
            "args": [
                "-y",
                "server-perplexity-ask"
            ],
            "env": {
                "PERPLEXITY_API_KEY": "${input:perplexity-key}"
            }
        }
    }
}
```




### Development mode

You can enable _development mode_ for MCP servers by adding a `dev` key to the server configuration. This is an object with two properties:

* `watch`: A glob pattern, or an array of glob patterns, to watch for file changes that restart the MCP server. Available for all server types.
* `debug`: Enables you to set up a debugger with the MCP server. Currently, {% data variables.product.prodname_vscode_shortname %} supports debugging Node.js and Python MCP servers. Available for stdio servers only.

Learn more about [MCP development mode](<https://github.com/api/extension-guides/ai/mcp.md#mcp-development-mode-in-vs-code>) in the MCP Dev Guide.

### Server naming conventions

When defining MCP servers, follow these naming conventions for the server name:

* Use camelCase for the server name, such as "uiTesting" or "githubIntegration"
* Avoid using whitespace or special characters
* Use a unique name for each server to avoid conflicts
* Use a descriptive name that reflects the server's functionality or brand, such as "github" or "database"

## Automatic MCP server discovery

{% data variables.product.prodname_vscode_shortname %} can automatically detect and reuse MCP server configurations from supported applications. Use `setting(chat.mcp.discovery.enabled)` to select the discovery sources.

| Source | Setting property | Configuration location |
|--------|------------------|------------------------|
| Claude Desktop | `claude-desktop` | Windows: `%APPDATA%\Claude\claude_desktop_config.json`<br/>macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`<br/>Linux: `$XDG_CONFIG_HOME/Claude/claude_desktop_config.json`, or `~/.config/Claude/claude_desktop_config.json` if `XDG_CONFIG_HOME` is not set |
| {% data variables.copilot.copilot_cli %} | `copilot` | `<COPILOT_HOME>/mcp-config.json`, or `~/.copilot/mcp-config.json` if `COPILOT_HOME` is not set |
| Cursor (global) | `cursor-global` | `~/.cursor/mcp.json` |
| Cursor (workspace) | `cursor-workspace` | `<workspace>/.cursor/mcp.json` |
| Windsurf | `windsurf` | `~/.codeium/windsurf/mcp_config.json` |

All discovery sources are off by default. In a remote window, {% data variables.product.prodname_vscode_shortname %} resolves configuration locations and environment variables in the remote environment.

For {% data variables.copilot.copilot_cli %}, setting `COPILOT_HOME` replaces the default `~/.copilot` location. {% data variables.product.prodname_vscode_shortname %} does not check both locations.

> [!NOTE]
> Agent Host sessions read the {% data variables.copilot.copilot_cli %} MCP configuration independently. The discovery setting does not change how the Agent Host receives MCP servers. Learn more about [MCP configuration behavior on the extension host](<https://github.com/docs/agents/concepts/agent-host.md#behavior-on-the-extension-host>).

## Commands

The following table lists the MCP-related commands available in the Command Palette (`kb(workbench.action.showCommands)`).

| Command | Description |
|---------|-------------|
| **MCP: Add Server** | Add a new MCP server to your workspace or user profile. |
| **MCP: Browse MCP Servers** | Open the MCP server gallery in the Extensions view. |
| **MCP: Browse Resources** | Browse resources provided by MCP servers. |
| **MCP: Install Server from Manifest** | Install an MCP server from an MCP manifest file. |
| **MCP: List Servers** | List all configured MCP servers and perform actions like start, stop, restart, or show output. |
| **MCP: Open Remote User Configuration** | Open the `mcp.json` file for the remote environment. |
| **MCP: Open User Configuration** | Open the `mcp.json` file in your user profile. |
| **MCP: Open Workspace Folder MCP Configuration** | Open the `.vscode/mcp.json` file in your workspace. |
| **MCP: Reset Cached Tools** | Clear the cached list of tools for MCP servers. Use this when a server's tools have changed. |
| **MCP: Reset Trust** | Reset separate trust decisions for MCP servers from non-workspace sources. Workspace MCP servers inherit Workspace Trust and are unaffected. |
| **MCP: Show Installed Servers** | Show a list of all installed MCP servers. |

## Settings

For a full list of {% data variables.product.prodname_vscode_shortname %} AI settings, see the [AI Settings Reference](<https://github.com/docs/agents/reference/ai-settings.md>). The following settings are specific to MCP servers.

| Setting | Description |
|---------|-------------|
| `setting(chat.mcp.access)` | Manage which MCP servers can be used in {% data variables.product.prodname_vscode_shortname %}. |
| `setting(chat.mcp.discovery.enabled)` | Configure automatic discovery of MCP server configuration from other applications. |
| `setting(chat.mcp.autostart)` `feature(mcp-autostart)` | Control which MCP servers {% data variables.product.prodname_vscode_shortname %} starts automatically when you submit a chat message. This setting doesn't control servers managed by the Agent Host. |
| `setting(chat.mcp.serverSampling)` | Configure which models are exposed to MCP servers for sampling (making requests in the background). |
| `setting(chat.mcp.apps.enabled)` `feature(mcp-apps)` | Enable or disable MCP Apps, which are rich user interfaces provided by MCP servers. |

## Related resources

* [Add and manage MCP servers](<https://github.com/docs/agent-customization/mcp-servers.md>)
* [Model Context Protocol Documentation](<https://modelcontextprotocol.io/>)
* [MCP tool extensibility overview](<https://github.com/api/extension-guides/ai/ai-extensibility-overview.md#mcp-tool>)


</details>
