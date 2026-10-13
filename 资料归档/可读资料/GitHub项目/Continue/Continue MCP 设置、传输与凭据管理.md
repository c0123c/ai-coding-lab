# Continue MCP 设置、传输与凭据管理

产品指南。介绍首个 MCP 服务、Continue 文档检索、JSON 配置兼容、服务器属性以及 STDIO、SSE、Streamable HTTP 和秘密值配置。

- 来源：Continue
- 资料类型：产品指南
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-25](<https://github.com/continuedev/continue/commit/f14f6ef7b09a930c2fe61320ecc8cb24fc64b6e2>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：服务端协议、认证和实际支持需按所用版本核对。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/mcp.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/Continue MCP 设置、传输与凭据管理.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "How to Set Up Model Context Protocol (MCP) in Continue"
description: MCP use and customization
keywords: [tool, use, function calling, claude, automatic]
sidebarTitle: "Model Context Protocol (MCP)"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

随着 AI 系统变得更好，它们仍然受到训练数据的阻碍，并且 
 无法访问实时信息或专用工具。 [Model Context
Protocol](<https://modelcontextprotocol.io/introduction>) (MCP) 通过 
 让 AI 模型与外部数据源、工具和环境连接来修复此问题。
这允许 AI 系统和 
 更广泛的系统之间顺利共享信息和能力数字世界。该标准由 Anthropic 创建，旨在将 
 提示、上下文和工具使用结合在一起，是构建可使用自定义工具设置的真正有用的 AI 体验 
 的关键。

## MCP 如何在 Continue 中工作

目前可以使用 Model Context
Protocol 标准配置自定义工具，以统一提示、上下文和工具使用。

MCP 可以使用 `mcpServers` 将服务器添加到您的配置中。

**说明：**

MCP只能在**agent**模式下使用。

有关流行 MCP 服务器的即用型配置，请参阅 [示例 MCP 服务器 ](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_Continue 常见 MCP 服务配置示例.md>)。

## 快速入门：如何设置您的第一台 MCP 服务器

以下是设置新的 MCP 服务器以在您的配置中使用的快速示例：

1. 在工作区的顶层创建一个名为 `.continue/mcpServers` 的文件夹
2. 将名为 `playwright-mcp.yaml` 的文件添加到此文件夹
3. 写入以下内容并保存

**代码示例：** `.continue/mcpServers/playwright-mcp.yaml`

```yaml
name: Playwright mcpServer
version: 0.0.1
schema: v1
mcpServers:
  - name: Browser search
    command: npx
    args:
      - "@playwright/mcp@latest"
```

现在通过提示以下命令来测试您的 MCP 服务器：

```
Open the browser and navigate Hacker News. Save the top 10 headlines in a hn.txt file.
```

结果将在当前工作目录中生成一个名为 `hn.txt` 的文件。

![剧作家 mcp](<https://raw.githubusercontent.com/continuedev/continue/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/images/mcp-playwright.png>)

## 如何使用 MCP 设置 Continue 文档搜索

您可以设置 MCP 服务器来直接从您的配置搜索 Continue 文档。这对于获取 Continue 配置和功能的帮助特别有用。

有关完整的设置说明、故障排除和使用示例，请参阅 [Continue MCP 参考 ](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_Continue 文档 MCP 检索服务的配置和使用.md>)。

## 使用 JSON MCP 格式 Claude、Cursor、Cline 等

**说明：**

如果您来自使用 JSON MCP 格式配置文件的其他工具（例如 Claude Desktop、Cursor 或 Cline），则可以将这些 JSON 配置文件直接复制到 `.continue/mcpServers/` 目录中（注意复数“服务器”）和 Continue 将自动选择它们。

例如，将 JSON MCP 配置文件放置在工作区的 `.continue/mcpServers/mcp.json` 中。

## 如何配置 MCP 服务器

要设置您自己的 MCP 服务器，请阅读 [MCP
 快速入门 ](<https://modelcontextprotocol.io/quickstart>)，然后将 MCP
 服务器块添加到您的 [ 配置文件 ](<https://docs.continue.dev/customize/deep-dives/configuration.md>)：

**代码示例：** `config.yaml`

```yaml
# ...
mcpServers:
  - name: SQLite MCP
    command: npx
    args:
      - "-y"
      - "mcp-sqlite"
      - "/path/to/your/database.db"
# ...
```

**注释：**

在 `.continue/mcpServers/` 中创建独立块文件时，请记住包含所需的元数据字段（`name`、`version`、`schema`），如上面的快速入门示例所示。

### 如何配置 MCP 服务器属性

MCP 组件包括一些特定于 MCP 服务器的附加属性。

- `name`：MCP 服务器的显示名称。
- `type`：MCP服务器的类型：`sse`、`stdio`、`streamable-http`
- `command`：运行以启动MCP服务器的命令。
- `args`：传递给命令的参数。
- `env`：要作为环境变量注入到命令中的秘密。

### 如何选择MCP传输类型

MCP 现在支持通过基于 HTTP- 的传输方式进行远程服务器连接，超越了传统的本地 stdio 传输方法。这可以与云托管的 MCP 服务器和分布式架构集成。

#### 如何使用服务器发送的事件传输 (`sse`)

对于实时流通信，请使用 SSE 传输：

```yaml
# ...
mcpServers:
  - name: Name
    type: sse
    url: https://....
# ...
```

#### 如何使用标准 Input/Output (`stdio`)

对于通过标准输入和输出进行通信的本地 MCP 服务器：

```yaml
# ...
mcpServers:
  - name: Name
    type: stdio
    command: npx
    args:
      - "@modelcontextprotocol/server-sqlite"
      - "/path/to/your/database.db"
# ...
```

#### 如何使用 Streamable HTTP 传输

对于具有流功能的基于标准 HTTP- 的通信：

```yaml
# ...
mcpServers:
  - name: Name
    type: streamable-http
    url: https://....
# ...
```

这些远程传输选项允许您连接到远程基础设施上托管的 MCP 服务器，从而实现更灵活的部署架构并跨多个客户端共享服务器资源。

有关传输机制及其用例的详细信息，请参阅[传输](<https://modelcontextprotocol.io/docs/concepts/transports#server-sent-events-sse>)上的官方MCP文档。

### 如何使用 MCP 服务器中的机密

对于某些 MCP 服务器，您将需要使用 API 密钥或其他秘密。您可以利用本地存储的环境机密。

```yaml
# ...
mcpServers:
  - name: Supabase MCP
    command: npx
    args:
      - -y
      - "@supabase/mcp-server-supabase@latest"
      - --access-token
      - ${{ secrets.SUPABASE_TOKEN }}
    env:
      SUPABASE_TOKEN: ${{ secrets.SUPABASE_TOKEN }}
  - name: GitHub
    command: npx
    args:
      - "-y"
      - "@modelcontextprotocol/server-github"
    env:
      GITHUB_PERSONAL_ACCESS_TOKEN: ${{ secrets.GITHUB_PERSONAL_ACCESS_TOKEN }}
# ...
```


## 原文对照

<details>
<summary>展开完整原文</summary>

As AI systems get better, they're still held back by their training data and
can't access real-time information or specialized tools. The [Model Context
Protocol](<https://modelcontextprotocol.io/introduction>) (MCP) fixes this by
letting AI models connect with outside data sources, tools, and environments.
This allows smooth sharing of information and abilities between AI systems and
the wider digital world. This standard, created by Anthropic to bring together
prompts, context, and tool use, is key for building truly useful AI experiences
that can be set up with custom tools.

## How MCP Works in Continue

Currently custom tools can be configured using the Model Context
Protocol standard to unify prompts, context, and tool use.

MCP Servers can be added to your config using `mcpServers`.

**说明：**

MCP can only be used in the **agent** mode.

For ready-to-use configurations for popular MCP servers, see [Example MCP Servers](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_Continue 常见 MCP 服务配置示例.md>).

## Quick Start: How to Set Up Your First MCP Server

Below is a quick example of setting up a new MCP server for use in your config:

1. Create a folder called `.continue/mcpServers` at the top level of your workspace
2. Add a file called `playwright-mcp.yaml` to this folder
3. Write the following contents and save

**代码示例：** `.continue/mcpServers/playwright-mcp.yaml`

```yaml
name: Playwright mcpServer
version: 0.0.1
schema: v1
mcpServers:
  - name: Browser search
    command: npx
    args:
      - "@playwright/mcp@latest"
```

Now test your MCP server by prompting the following command:

```
Open the browser and navigate Hacker News. Save the top 10 headlines in a hn.txt file.
```

The result will be a generated file called `hn.txt` in the current working directory.

![playwright mcp](<https://raw.githubusercontent.com/continuedev/continue/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/images/mcp-playwright.png>)

## How to Set Up Continue Documentation Search with MCP

You can set up an MCP server to search the Continue documentation directly from your config. This is particularly useful for getting help with Continue configuration and features.

For complete setup instructions, troubleshooting, and usage examples, see the [Continue MCP Reference](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_Continue 文档 MCP 检索服务的配置和使用.md>).

## Using JSON MCP Format from Claude, Cursor, Cline, etc

**说明：**

If you're coming from another tool that uses JSON MCP format configuration files (like Claude Desktop, Cursor, or Cline), you can copy those JSON config files directly into your `.continue/mcpServers/` directory (note the plural "Servers") and Continue will automatically pick them up.

For example, place your JSON MCP config file at `.continue/mcpServers/mcp.json` in your workspace.

## How to Configure MCP Servers

To set up your own MCP server, read the [MCP
quickstart](<https://modelcontextprotocol.io/quickstart>) and then add an MCP
server block to your [config file](<https://docs.continue.dev/customize/deep-dives/configuration.md>):

**代码示例：** `config.yaml`

```yaml
# ...
mcpServers:
  - name: SQLite MCP
    command: npx
    args:
      - "-y"
      - "mcp-sqlite"
      - "/path/to/your/database.db"
# ...
```

**注释：**

When creating a standalone block file in `.continue/mcpServers/`, remember to include the required metadata fields (`name`, `version`, `schema`) as shown in the Quick Start example above.

### How to Configure MCP Server Properties

MCP components include a few additional properties specific to MCP servers.

- `name`: A display name for the MCP server.
- `type`: The type of the MCP server: `sse`, `stdio`, `streamable-http`
- `command`: The command to run to start the MCP server.
- `args`: Arguments to pass to the command.
- `env`: Secrets to be injected into the command as environment variables.

### How to Choose MCP Transport Types

MCP now supports remote server connections through HTTP-based transports, expanding beyond the traditional local stdio transport method. This enables integration with cloud-hosted MCP servers and distributed architectures.

#### How to Use Server-Sent Events Transport (`sse`)

For real-time streaming communication, use the SSE transport:

```yaml
# ...
mcpServers:
  - name: Name
    type: sse
    url: https://....
# ...
```

#### How to Use Standard Input/Output (`stdio`)

For local MCP servers that communicate via standard input and output:

```yaml
# ...
mcpServers:
  - name: Name
    type: stdio
    command: npx
    args:
      - "@modelcontextprotocol/server-sqlite"
      - "/path/to/your/database.db"
# ...
```

#### How to Use Streamable HTTP Transport

For standard HTTP-based communication with streaming capabilities:

```yaml
# ...
mcpServers:
  - name: Name
    type: streamable-http
    url: https://....
# ...
```

These remote transport options allow you to connect to MCP servers hosted on remote infrastructure, enabling more flexible deployment architectures and shared server resources across multiple clients.

For detailed information about transport mechanisms and their use cases, refer to the official MCP documentation on [transports](<https://modelcontextprotocol.io/docs/concepts/transports#server-sent-events-sse>).

### How to Work with Secrets in MCP Servers

With some MCP servers you will need to use API keys or other secrets. You can leverage locally stored environment secrets.

```yaml
# ...
mcpServers:
  - name: Supabase MCP
    command: npx
    args:
      - -y
      - "@supabase/mcp-server-supabase@latest"
      - --access-token
      - ${{ secrets.SUPABASE_TOKEN }}
    env:
      SUPABASE_TOKEN: ${{ secrets.SUPABASE_TOKEN }}
  - name: GitHub
    command: npx
    args:
      - "-y"
      - "@modelcontextprotocol/server-github"
    env:
      GITHUB_PERSONAL_ACCESS_TOKEN: ${{ secrets.GITHUB_PERSONAL_ACCESS_TOKEN }}
# ...
```


</details>
