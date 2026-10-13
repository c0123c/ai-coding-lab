# 通过 MCP 连接 Claude Code 与外部工具

介绍 HTTP、SSE、stdio 等 MCP 接入方式，涵盖认证、作用范围、配置导入、工具搜索及权限管理，支持连接数据源和开发服务。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-10](<https://code.claude.com/docs/en/mcp>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/mcp.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/通过 MCP 连接 Claude Code 与外部工具.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 通过MCP将Claude Code连接到工具

> 了解如何使用 Model Context Protocol 将 Claude Code 连接到您的工具。

Claude Code 可以通过 [Model Context Protocol (MCP)](<https://modelcontextprotocol.io/introduction>) 连接到数百个外部工具和数据源，这是 AI- 工具集成的开源标准。 MCP 服务器允许 Claude Code 访问您的工具、数据库和 API。

当您发现自己从其他工具（例如问题跟踪器或监控仪表板）将数据复制到聊天中时，请连接服务器。连接后，Claude 可以直接读取该系统并对其进行操作，而不是根据您粘贴的内容进行操作。

如果您要连接第一台服务器，请从 [MCP 快速入门 ](<https://code.claude.com/docs/en/mcp-quickstart>) 开始逐步演练。此页面是完整的参考。

## 你可以用MCP做什么

连接MCP服务器后，您可以要求Claude Code：

* **实现问题跟踪器的功能**：“添加JIRA问题ENG-4521中描述的功能，并在GitHub上创建PR。”
* **分析监控数据**：“检查Sentry和Statsig，检查ENG-4521中描述的功能的使用情况。”
* **查询数据库**：“根据我们的PostgreSQL数据库查找使用功能ENG-4521的10随机用户的电子邮件。”
* **集成设计**：“根据 Slack 中发布的新 Figma 设计更新我们的标准电子邮件模板”
* **自动化工作流程**：“创建 Gmail 草稿，邀请这些 10 用户参加有关新功能的反馈会议。”
* **React 到外部事件 **：MCP 服务器还可以充当 [ 通道 ](<https://code.claude.com/docs/en/channels>)，将消息推送到您的会话中，因此 Claude 在您离开时对 Telegram 消息、Discord 聊天或 webhook 事件做出反应。

## 查找并构建 MCP 服务器

浏览[Anthropic目录](<https://claude.ai/directory>)中已审查的连接器。目录连接器使用与 Claude Code 相同的 MCP 基础设施，因此您可以使用 `claude mcp add` 添加其中列出的任何远程服务器。



**注意：**

连接之前验证您信任每台服务器。获取外部内容的服务器可能会让您面临[提示注入风险](<https://code.claude.com/docs/en/security#protect-against-prompt-injection>)。


要构建您自己的服务器，请参阅 [MCP 服务器指南 ](<https://modelcontextprotocol.io/docs/develop/build-server>) 了解协议基础知识，并参阅 [Claude 连接器构建文档 ](<https://claude.com/docs/connectors/building>) 了解身份验证、测试和目录提交。

您还可以使用官方[`mcp-server-dev`插件](<https://github.com/anthropics/claude-plugins-official/tree/main/plugins/mcp-server-dev>)为您搭建一个Claude服务器。



  

#### 安装插件

在VS Code扩展程序或桌面应用程序中，按照[安装插件](<https://code.claude.com/docs/en/plugins/install#install-a-plugin>)而不是此步骤。在终端中，通过运行 `claude` 启动 Claude Code，然后在提示符下输入：

    ```
    /plugin install mcp-server-dev@claude-plugins-official
    ```

如果安装失败，请匹配消息 Claude Code 报告：

    * `Marketplace "claude-plugins-official" not found`：使用 `/plugin marketplace add anthropics/claude-plugins-official` 添加市场，然后重试安装。
    * 该插件[在市场中找不到](<https://code.claude.com/docs/en/plugins/install#install-a-plugin>)：检查插件名称。

如果安装摘要报告 `Run /reload-plugins to apply.`，则 Claude Code 会为您运行重新加载。如果重新加载警告您的下一条消息将重新读取对话，请运行 `/reload-plugins --force`。
  


  

#### 运行构建技能

    ```
    /mcp-server-dev:build-mcp-server
    ```

Claude 询问您的用例并搭建远程 HTTP 或本地 stdio 服务器。
  


## 安装 MCP 服务器

MCP服务器可以根据您的需要通过多种方式进行配置：

### 选项1：添加远程HTTP服务器

HTTP 服务器是连接到远程 MCP 服务器的推荐选项。这是基于云的服务最广泛支持的传输。

```bash theme={null}
# Basic syntax
claude mcp add --transport http <name> <url>

# Real example: Connect to Notion
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Example with Bearer token
claude mcp add --transport http secure-api https://api.example.com/mcp \
  --header "Authorization: Bearer your-token"
```

通过 `.mcp.json`、`~/.claude.json` 或 `claude mcp add-json` 中的 JSON 配置 MCP 服务器时，`type` 字段接受 `streamable-http` 作为 `http` 的别名。 MCP 规范使用名称 `streamable-http` 进行此传输，因此从服务器文档复制的配置无需修改即可工作。

具有 `url` 但没有 `type` 的 JSON 条目是配置错误，因为 Claude Code 读取没有 `type` 的条目作为 stdio 服务器。 Claude Code 跳过该服务器并报告 `MCP server "<name>" has a "url" but no "type"; add "type": "http" (or "sse" / "ws") to this entry`。在v2.1.202之前，Claude Code将此错误配置报告为`command: expected string, received undefined`。

只有 SDK 主机应用程序，例如 [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/mcp>) 应用程序或 [ 桌面应用程序 ](<https://code.claude.com/docs/en/desktop>)，可以注册进程内 `"type": "sdk"` 服务器。 Claude Code 跳过 `.mcp.json`、`~/.claude.json` 或设置和报告 `Skipped — MCP server "<name>" declares type "sdk", which only an SDK host application can register` 中的 `"type": "sdk"` 条目。

在 `--output-format stream-json` 运行中，Claude Code 还会报告 `system/init` 事件的 [`mcp_server_errors` 字段 ](<https://code.claude.com/docs/en/headless#stream-responses>) 中跳过的 `--mcp-config` 条目，因此脚本可以检测到服务器从未加载。这需要 Claude Code v2.1.219 或更高版本。

### 选项2：添加远程SSE服务器



**注意：**

SSE（服务器发送事件）传输已弃用。如果可用，请改用 HTTP 服务器。


某些服务仍然仅公开 SSE 端点。使用与 [ 和 HTTP 服务器 ](<#选项1添加远程http服务器>) 相同的 `claude mcp add --transport http <name> <url>` 命令添加这些内容。 Claude Code 首先尝试 HTTP 传输，当服务器不接受时切换到 SSE。自动切换需要 Claude Code v2.1.265 或更高版本。

在早期版本中，或者要直接通过 SSE 连接，请改为传递 `--transport sse`：

```bash theme={null}
# Basic syntax
claude mcp add --transport sse <name> <url>

# Real example: Connect to Asana
claude mcp add --transport sse asana https://mcp.asana.com/sse

# Example with authentication header
claude mcp add --transport sse private-api https://api.company.com/sse \
  --header "X-API-Key: your-key-here"
```

### 选项 3：添加本地 stdio 服务器

Stdio 服务器在您的计算机上作为本地进程运行。它们非常适合需要直接系统访问或自定义脚本的工具。

Claude Code 将生成的服务器环境中的 `CLAUDE_PROJECT_DIR` 设置为项目根目录，以便您的服务器可以解析项目相对路径，而不依赖于工作目录。这与挂钩在其 `CLAUDE_PROJECT_DIR` 变量中接收的目录相同。从服务器进程内部读取它，例如 Node 中的 `process.env.CLAUDE_PROJECT_DIR` 或 Python 中的 `os.environ["CLAUDE_PROJECT_DIR"]`。

`CLAUDE_PROJECT_DIR` 是稳定的项目根目录，当您在会话中添加或删除工作目录时不会更改。将自己的文件系统访问限制为一组允许的目录的服务器应该改为实现 MCP `roots/list` 请求。 Claude Code 使用会话的启动目录以及您通过 `--add-dir`、`/add-dir` 或 `additionalDirectories` 设置授予的每个 [ 附加工作目录 ](<https://code.claude.com/docs/en/permissions#working-directories>) 来回答 `roots/list`。当该集合更改时，Claude Code 发送 `notifications/roots/list_changed`。在v2.1.203之前，`roots/list`只返回启动目录，Claude Code没有发送`notifications/roots/list_changed`。

该变量是在服务器环境中设置的，而不是在 Claude Code 自己的环境中设置，因此可以通过项目范围 `.mcp.json` 条目或本地或用户范围服务器条目的 `command` 或 `args` 中的 `${VAR}` 扩展来引用它`~/.claude.json` 需要默认值，例如 `${CLAUDE_PROJECT_DIR:-.}`。插件提供的MCP配置直接替换`${CLAUDE_PROJECT_DIR}`，不需要默认。

```bash theme={null}
# Basic syntax
claude mcp add [options] <name> -- <command> [args...]

# Real example: Add Airtable server
claude mcp add --env AIRTABLE_API_KEY=YOUR_KEY --transport stdio airtable \
  -- npx -y airtable-mcp-server
```



**注释：**

对于 stdio 服务器，`--`（双破折号）将 Claude 自己的选项（例如 `--transport`、`--env` 和 `--scope`）与运行服务器的命令和参数分开。 `--` 之后的所有内容都会原封不动地传递到服务器。

例如：

  * `claude mcp add --transport stdio myserver -- npx server` → 运行 `npx server`
  * `claude mcp add --env KEY=value --transport stdio myserver -- python server.py --port 8080` → 在环境中使用 `KEY=value` 运行 `python server.py --port 8080`

如果没有`--`，Claude Code将尝试解析服务器的标志，如上面的`--port`，作为它自己的选项。

`--env` 接受多个 `KEY=value` 对。如果服务器名称直接位于 `--env` 之后，CLI 会将该名称作为另一对读取并拒绝它，因此在 `--env` 和服务器名称之间至少放置一个其他选项，例如 `--transport stdio`。


### 选项4：添加远程WebSocket服务器

WebSocket 服务器保持持久的双向连接，适合将事件自动推送到 Claude 的远程 MCP 服务器。当您的服务器仅响应请求时，请使用 HTTP，因为 HTTP 支持 OAuth 和 `claude mcp add --transport` 标志，而 WebSocket 两者都不支持。

在 `.mcp.json` 中或使用 `claude mcp add-json` 配置 WebSocket 服务器：

```bash theme={null}
claude mcp add-json events-server \
  '{"type":"ws","url":"wss://mcp.example.com/socket","headers":{"Authorization":"Bearer YOUR_TOKEN"}}'
```

`type: "ws"` 条目接受与 `http` 相同的 `url`、`headers`、`headersHelper`、`timeout` 和 `alwaysLoad` 字段。身份验证仅限标头，因此在 `headers` 中传递静态令牌，或在连接时使用 [`headersHelper`](<#使用动态标头进行自定义身份验证>) 生成一个静态令牌。 `claude mcp add --transport` 标志不接受 `ws`。

### 根据为另一个客户端编写的设置说明添加服务器

MCP 服务器并非特定于 Claude Code，因此服务器的设置指令可能是为 Claude 桌面、Cursor 或其他 MCP 客户端编写的，并且不给出 `claude mcp add` 命令。无论如何，要添加服务器，请在这些说明中查找 URL、启动命令或 JSON 块：

* **A URL**如`https://mcp.example.com/mcp`：服务器是远程的。
* **启动命令**，例如`npx -y @example/mcp-server`：服务器在您的计算机上运行。
* **一个`mcpServers` JSON块**：为另一个客户端的设置文件编写的配置。

每个都是[安装MCP服务器](<#安装-mcp-服务器>)中四个选项的输入之一。找到下面的形状，将其转换为 Claude Code 接受的命令。每个命令都会写入 [ 本地范围 ](<#本地范围>)，除非您添加 `--scope project` 或 `--scope user`。

<span id="from-a-url" />

#### 从 URL 添加服务器

URL 表示服务器是远程的。对于 `https://` 端点，请使用 `--transport http` 添加它，或者当说明表明端点使用 SSE 时，遵循 [ 选项 2](<#选项2添加远程sse服务器>)。对于 `wss://` 端点，请使用 [ 选项 4](<#选项4添加远程websocket服务器>)，因为 `--transport` 不接受 `ws`：

```bash theme={null}
claude mcp add --transport http example https://mcp.example.com/mcp
```

如果指令还给出了 API 密钥或令牌标头，请使用 `--header` 传递它，如 [ 选项 1](<#选项1添加远程http服务器>) 中所示。

<span id="from-an-npx-uvx-or-binary-command" />

#### 从 `npx`、`uvx` 或二进制命令添加服务器

启动命令意味着服务器作为本地 stdio 进程运行。将整个命令放在后面 `--`，所以 Claude Code 传递标志，例如 `-y` 到启动服务器的命令，而不是将它们作为自己的选项读取。传递指令要求的任何环境变量 `--env`，服务器名称之后和之前 `--`:

```bash theme={null}
claude mcp add example --env API_KEY=your-key -- npx -y @example/mcp-server
```

[选项3](<#选项-3添加本地-stdio-服务器>)完全覆盖`--`分隔符。

<span id="from-an-mcpservers-json-block" />

#### 从 `mcpServers` JSON 块添加服务器

为另一个 MCP 客户端（例如 Claude 桌面）编写的 `mcpServers` 块使用包装器密钥和条目形状 Claude Code 读取。传递 `claude mcp add-json` 内部的对象 `mcpServers`，而不是包装器。首先需要修复两个条目：

* **A `url`，不带 `type`**：添加 `"type": "http"`、`"type": "sse"` 或 `"type": "ws"` 以匹配端点。 Claude Code 读取没有 `type` 的条目作为 stdio 服务器，因此没有 `type` 的 `url` 条目会失败。
* **包含字母、数字、连字符和下划线以外的字符的键**：选择仅使用这些字符的服务器名称。否则，键是服务器名称。

例如，这个块：

```json theme={null}
{
  "mcpServers": {
    "example": {
      "command": "npx",
      "args": ["-y", "@example/mcp-server"]
    }
  }
}
```

变成这个命令：

```bash theme={null}
claude mcp add-json example '{"command":"npx","args":["-y","@example/mcp-server"]}'
```

[从JSON配置添加MCP服务器](<#从-json-配置添加-mcp-服务器>)涵盖shell转义和`add-json`的`--scope`标志。要与您的团队共享服务器，请添加 `--scope project`，或在项目根目录的 `.mcp.json` 中的 `mcpServers` 下添加条目并提交。 [项目范围](<#项目范围>)涵盖Claude Code如何加载和批准该文件。

每个 `claude mcp add` 和 `claude mcp add-json` 命令在成功时打印 `Added ...` 行。要检查 Claude Code 是否已连接，请运行 `claude mcp get <name>`； [服务器状态](<#服务器状态>)涵盖了它显示的状态以及`.mcp.json`服务器的批准步骤。

### 管理您的服务器

配置完成后，您可以使用以下命令管理您的 MCP 服务器：

```bash theme={null}
# List all configured servers
claude mcp list

# Get details for a specific server
claude mcp get notion

# Remove a server
claude mcp remove notion

# (within Claude Code) Check server status
/mcp
```

当您删除远程服务器时，Claude Code 还会删除为该服务器存储的 OAuth 令牌和客户端注册。

#### 服务器状态

`claude mcp add` 通过打印 `Added ...` 行确认添加成功，这意味着配置已写入。如果该命令打印 `was not saved` 消息，请参阅 [MCP 服务器未保存或删除 ](<https://code.claude.com/docs/en/errors#mcp-server-was-not-saved-or-removed>);对于 `may not have been saved` 消息，请参阅 [MCP 服务器可能尚未保存或删除 ](<https://code.claude.com/docs/en/errors#mcp-server-may-not-have-been-saved-or-removed>)。

`claude mcp list` 在其列出的每个服务器旁边显示运行状况，例如 `✔ Connected`、`! Needs authentication` 或 `✘ Failed to connect`。失败状态意味着 Claude Code 无法连接到该服务器，而不是 list 命令失败。

此列表中的状态报告配置决策而不是连接尝试，因此 Claude Code 会在不连接到服务器的情况下打印它们：

* ``⏸ Pending approval (run `claude` to approve)``：来自 `.mcp.json` 的项目范围服务器，您尚未批准。 Claude Code在`claude mcp list`和`claude mcp get <name>`中都显示了它。以交互方式运行 `claude` 以进行审核和批准。
* `✘ Rejected (see disabledMcpjsonServers in settings)`：[`disabledMcpjsonServers`](<https://code.claude.com/docs/en/settings-reference#disabledmcpjsonservers>)条目拒绝的`.mcp.json`服务器。 Claude Code仅在`claude mcp get <name>`中显示。
* `⊘ Disabled for this project (re-enable via /mcp)`：项目的[`disabledMcpServers`](<#禁用服务器而不删除它>)列表名称的服务器。 Claude Code在`claude mcp list`和`claude mcp get <name>`中都显示了它。从 `/mcp` 面板重新打开服务器。

#### 项目服务器批准和工作区信任

从 v2.1.196、`claude mcp list` 和 `claude mcp get` 开始，仅从未签入存储库的设置文件中读取 `.mcp.json` 批准，直到您通过在其中运行 `claude` 并接受工作区信任对话框来信任工作区。克隆存储库无法批准自己的服务器：提交到项目的 `.claude/settings.json` 的 [`enableAllProjectMcpServers`](<https://code.claude.com/docs/en/settings-reference#enableallprojectmcpservers>) 或 [`enabledMcpjsonServers`](<https://code.claude.com/docs/en/settings-reference#enabledmcpjsonservers>) 在不受信任的文件夹中被忽略，并且服务器停留在 `⏸ Pending approval`，而不是进行连接和运行状况检查。

来自这些来源的批准仍然适用于不受信任的文件夹：

* 您的用户 `~/.claude/settings.json`
* 托管设置
* 使用 `--settings` 传递的设置

Claude Code 还应用来自未跟踪的 `.claude/settings.local.json` 的批准，但它运行 git 来检查文件是否被跟踪，并且仅在 [ 受信任文件夹 ](<https://code.claude.com/docs/en/permissions#project-allow-rules-and-workspace-trust>) 中运行该检查。在您从未信任的文件夹中，Claude Code 会在应用文件批准之前等待信任对话框，除非该文件夹是您自己的配置主目录：您的主目录，或者您将 `.claude` 设置为 [`CLAUDE_CONFIG_DIR`](<https://code.claude.com/docs/en/env-vars>) 的目录。在 v2.1.207 之前，Claude Code 甚至在您从未信任的文件夹中应用了未跟踪的 `.claude/settings.local.json` 的批准。

任何设置文件中的 `disabledMcpjsonServers` 条目仍然会拒绝服务器。

#### 服务器状态详细信息

在`/mcp`中，包括服务器的菜单，以及在[`/plugin`](<https://code.claude.com/docs/en/plugins/install>)管理器中，您以前使用过的远程HTTP或SSE服务器可以显示`cached`状态，例如`cached 2h ago · connects on first use · 5 tools`。 Claude Code 从其发现缓存中加载服务器的工具列表（保存在先前的会话中），而不是在启动时连接，并且 Claude Code 在第一次 Claude 调用服务器工具之一时连接服务器。这些工具可从您的第一条消息中获取，因此您无需执行任何操作。发现缓存及其 `cached` 状态需要 Claude Code v2.1.221 或更高版本。

发现缓存默认处于关闭状态，除非逐步推出已为您的帐户启用它。设置 [`MCP_DISCOVERY_CACHE=1`](<https://code.claude.com/docs/en/env-vars>) 将其打开，或设置 `0` 将其保持关闭，即使推出已启用它。在v2.1.238之前，缓存默认开启。

当您从`/mcp`中的服务器菜单中选择**禁用**或**清除身份验证**时，Claude Code也会丢弃该服务器的缓存条目。 **重新连接**也会在已连接或发生故障的服务器上丢弃它；在 `cached` 服务器上，**重新连接** 现在连接服务器并保留条目。丢弃该条目后，下次 Claude Code 连接到服务器时，它会从服务器而不是从缓存中获取工具列表。

当服务器的状态为 `✘ Failed to connect` 时，`claude mcp list` 将故障详细信息附加到该状态行，并且 `claude mcp get <name>` 在 `Issue:` 行上显示它：HTTP 状态或错误代码，以及服务器返回的任何错误文本。 `/mcp` 中服务器的详细视图在其 `Issue:` 行中包含相同的服务器报告文本。 Claude Code 从此细节中编辑类似凭证的文本，并且从不包含可以携带秘密的扩展服务器 URL。 Claude Code 不会向 `✘ Connection error` 状态附加任何详细信息，因为它将在那里打印的异常文本可以嵌入该 URL。在v2.1.219之前，这两个命令仅显示裸露的故障状态，没有状态代码或服务器的错误文本。

当您从 `/mcp` 完成身份验证，但连接仍然失败并显示 HTTP 状态或传输错误代码时，Claude Code 会将该代码和服务器的 URL 的来源添加到尝试后打印的消息中。 origin 是方案和主机，加上 URL 命名时的端口，例如 `https://mcp.example.com`。

* 路径和查询从未出现在该消息中。
* 对于本地、项目或用户 [ 范围 ](<#mcp安装范围>) 或托管 MCP 配置中的服务器，源显示该配置中写入的主机，因此主机中的 `${VAR}` 引用不会在消息中展开。
* 对于没有状态或错误代码的故障，Claude Code 显示没有来源的错误文本。

配置为空 `url` 的远程服务器在 `/mcp`、`claude mcp list` 和 [`/plugin`](<https://code.claude.com/docs/en/plugins/install>) 管理器中显示为 `not configured`，并且 Claude Code 不会尝试连接到它。插件可以为您稍后配置的连接器包含这样的占位符条目，因此 Claude Code 不会将其报告为错误或设置问题。 `/mcp` 中服务器的详细视图显示为 `No URL configured for this server`；设置条目的`url`来连接它。在 v2.1.208 之前，Claude Code 报告了一个空的 `url` 作为配置问题，并提示重新连接。

#### 配置警告

Claude Code 警告以下配置问题。每个条目都说明了 Claude Code 检查的内容以及如何清除警告：

* **隐藏空格**：Claude Code在MCP配置值携带隐藏的前导或尾随空格时发出警告，这通常来自粘贴带有尾随换行符的令牌。 Claude Code 检查 `command`、`url`、每个 `args` 条目以及 `env` 和 `headers` 下的值和键名称。 Claude Code 在 `claude mcp list` 输出和 `/mcp` 中显示警告，命名受影响的字段而不回显其值，例如 `Leading or trailing whitespace in: headers.Authorization`。 Claude Code 不会修剪空格并使用与写入完全相同的值，因此请编辑配置以将其删除。
* **多个作用域中的名称相同**：如果您在具有不同端点的多个 [作用域](<#mcp安装范围>) 中定义相同的服务器名称，Claude Code 会警告 `claude mcp list` 输出和中的冲突`/mcp`。 Claude Code 存储每个端点的 OAuth 登录，因此当您对一个项目中加载的定义进行身份验证时，您仍然需要在加载不同定义的项目中单独登录。保留所需的端点并使用 `claude mcp remove <name> --scope <scope>` 删除其他端点。在警告中，Claude Code 引用配置中写入的每个范围的端点，[`${VAR}` 引用](<#environment-variable-expansion-in-mcp-json>) 未展开，因此它永远不会显示解析值，例如 API 键。
* **保留名称**：Claude Code保留其内置服务器的名称，包括`workspace`、`claude-in-chrome`、`computer-use`、`Claude Preview`和`Claude Browser`。如果您的配置定义了具有保留名称的服务器，Claude Code 会在加载时跳过它，并显示一条警告，要求您重命名它。 `claude mcp add` 拒绝保留名称并出现错误。 `Claude Preview` 和 `Claude Browser` 均指定 [Claude Code 桌面应用程序的预览窗格 ](<https://code.claude.com/docs/en/desktop#preview-your-app>) 使用的内置服务器。
* **缺少环境变量**：如果服务器配置中的[`${VAR}`引用](<#environment-variable-expansion-in-mcp-json>)命名了一个未设置且没有`:-default`的变量，则Claude Code在`claude mcp list`输出中发出警告，并且在`/mcp`中，命名变量，并且仍然加载服务器，其中`${VAR}`文本未展开。设置变量或添加 `${VAR:-default}` 后备。在远程服务器的 `url` 和 `headers` 中，某些凭证变量 [ 读取为空 ](<#读取为空的凭证变量>)，没有任何警告。

#### 工具可用性

`/mcp` 面板显示每个连接的服务器旁边的工具计数，并标记宣传工具功能但不公开任何工具的服务器。

如果您的请求需要来自仍在后台连接的服务器的工具，Claude 会在继续之前等待该服务器。等待的方式取决于您的配置：

* **使用[工具搜索](<#使用-mcp-工具搜索进行缩放>)，默认**：等待发生在`ToolSearch`调用内。
* **不带工具搜索**：Claude使用`WaitForMcpServers`工具代替。不带工具搜索的配置包括自定义 `ANTHROPIC_BASE_URL`、`ENABLE_TOOL_SEARCH=false` 以及 Google 云 Agent 平台上早于 Claude 4.5 代的型号。
* **上一个 Microsoft 铸造厂 [Azure 上托管的部署](<https://platform.claude.com/docs/en/build-with-claude/claude-in-microsoft-foundry#hosting-options>)**: Claude 从工具搜索路径开始，而不是从 `WaitForMcpServers`，因为 Claude Code 仅从以下位置发现部署的服务器端拒绝 API。之后 Claude Code 将该部署切换到 [预先加载](<#使用-mcp-工具搜索进行缩放>)，来自完成连接的服务器的工具可在 Claude的下一个请求。

启用工具搜索后，当服务器在 Claude 工作时完成连接时，Claude Code 在同一回合的下一个请求中将服务器的工具名称列出到 Claude。 Claude 然后可以搜索并调用这些工具，而无需等待您的下一条消息。

恢复会话后，Claude 可以从保存的对话中调用工具，同时该工具的 MCP 服务器仍在连接。当服务器第一次尝试连接时，Claude Code 会将调用保留最多 10 秒，并在工具可用后运行它。如果服务器未及时连接，或者已经在[尝试失败后重试](<#自动重连>)，则调用会失败并显示`No such tool available`[工具错误](<https://code.claude.com/docs/en/errors#no-such-tool-available>)。

### 禁用服务器而不删除它

在 `/mcp` 面板中关闭服务器以阻止 Claude Code 连接到该服务器，而不会丢失其配置。 Claude Code 仍列出 `/mcp` 中的服务器，标记为已禁用。

当您切换服务器时，Claude Code 会将您在 `~/.claude.json` 中每个项目的选择记录在涵盖不相交服务器集的两个列表之一中：

* `disabledMcpServers`：用户配置的服务器、插件服务器、组织服务器 [ 通过托管设置 ](<https://code.claude.com/docs/en/managed-mcp#provide-servers-through-managed-settings>) 提供的选择退出列表，claude.ai 连接器 Claude Code [ 获取自身 ](<#连接器如何达到claude-code>)，以及默认为的内置服务器上。 Claude Code 无法连接到您在此处列出的服务器。当您使用 [禁用 claude.ai 连接器 ](<#disable-claude-ai-connectors>) 中所述的每个项目 `/mcp` 开关禁用 claude.ai 连接器时，Claude Code 会将其写入此列表的显示名称下，例如 `claude.ai Slack`。
* `enabledMcpServers`：默认关闭的内置服务器的选择列表，例如`computer-use`。 Claude Code 仅当您在此处列出时才连接到默认关闭的服务器。

Claude Code 只查询每个服务器的两个列表之一，因此两个列表都不会覆盖另一个列表。如果将常规服务器添加到`enabledMcpServers`，或将默认关闭的内置服务器添加到`disabledMcpServers`，则Claude Code将忽略该条目。

`disabledMcpServers` 和 `enabledMcpServers` 与 [`enabledMcpjsonServers`](<https://code.claude.com/docs/en/settings-reference#enabledmcpjsonservers>) 和 [`disabledMcpjsonServers`](<https://code.claude.com/docs/en/settings-reference#disabledmcpjsonservers>) 无关，后者控制项目的 `.mcp.json` 文件中定义的服务器的审批。

### MCP 客户端运行时

Claude Code 通过两个客户端运行时之一连接到 MCP 服务器。 v1 运行时构建于 MCP TypeScript SDK 1.x 之上。 v2 运行时与 [MCP TypeScript SDK 2.0](<https://ts.sdk.modelcontextprotocol.io/v2/>) 上的代码相同，其中添加了 MCP 协议修订版2026-07-28。本页的其余部分适用于两个运行时，但命名为 v2 运行时的部分除外。

Claude Code 每次启动时都会选择一个运行时并保留它直到退出。在 [ 获取功能标志 ](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>) 的会话中，它使用 Claude Code v2.1.232 或更高版本上的 v2 运行时。

在不获取功能标志的会话中，Claude Code 在 Claude Code v2.1.274 或更高版本上默认使用 v2 运行时：

* Amazon Bedrock、AWS 上的 Claude 平台、Google Cloud 的 Agent 平台或 Microsoft Foundry 上的会话，除非嵌入 Claude Code 集 [`CLAUDE_CODE_PROVIDER_MANAGED_BY_HOST`](<https://code.claude.com/docs/en/env-vars>) 的主机平台
* 通过 [Claude 应用网关](<https://code.claude.com/docs/en/claude-apps-gateway>) 登录的会话
* 您关闭遥测或功能标志获取的会话，例如使用 `DISABLE_TELEMETRY`

在 v2、Claude Code 上还：

* 询问 HTTP、stdio 和 claude.ai 连接器服务器是否支持较新的版本，并将其与支持的服务器一起使用。它像 v1 一样连接到所有其他服务器。
* 通过其保持打开的 [ 流接收来自服务器的有关较新版本的 `list_changed` 通知 ](<#v2-运行时上的通知流>)。
* 不注册连接到较新版本的 [ 通道 ](<#通过渠道推送消息>) 服务器，因为该版本无法携带通道消息。
* [MCP OAuth 登录 ](<#使用远程-mcp-服务器进行身份验证>) 失败，其授权响应指定了意外的颁发者。
* 仅将 [MCP OAuth](<#使用远程-mcp-服务器进行身份验证>) 凭据发送到通过 HTTPS 或 `localhost`、`127.0.0.1` 或 `::1` 提供服务的令牌端点。对于令牌端点为普通 `http://` 的服务器（例如本地网络上的设备），登录失败。请参阅 [拒绝将凭证发送到非 https 令牌端点 ](<https://code.claude.com/docs/en/errors#refusing-to-send-credentials-to-non-https-token-endpoint>)。

Anthropic 可以通过功能标志 Claude Code 获取，将特定服务器保留在早期协议上，或保持在该流之外。

要自行选择运行时，请将 [`MCP_SDK_GENERATION`](<https://code.claude.com/docs/en/env-vars>) 设置为 `v1` 或 `v2`。要决定是否询问 Claude Code，请将 [`MCP_PROTOCOL_NEGOTIATION`](<https://code.claude.com/docs/en/env-vars>) 设置为 `auto` 或 `legacy`。

### 动态工具更新

MCP 服务器可以更改其在连接时提供的工具、提示或资源，并发送 `list_changed` 通知。当一个人到达时：

* **在交互式终端会话**中，Claude Code从该服务器获取更新的列表，因此您不需要重新连接它。
* **在带有 `-p` 标志的 [非交互模式 ](<https://code.claude.com/docs/en/headless>) 和 [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>)** 中，Claude Code 仅刷新这些通知上的工具列表。

如果刷新请求失败，Claude Code 会保留服务器先前发现的工具、提示和资源，直到稍后刷新成功。在 v2.1.214 之前，刷新期间的暂时性错误将服务器的工具、提示和资源替换为空列表。

#### v2 运行时上的通知流

在 [v2 运行时 ](<#mcp-客户端运行时>) 上，Claude Code 通过其保持打开的流从服务器接收有关较新协议修订版的 `list_changed` 通知。当流关闭时，Claude Code 重新打开它，有两个限制：

* **流在10秒内再次关闭**：Claude Code重新打开它最多3次，然后停止该连接。
* **流保持打开状态的时间超过 10 秒，然后关闭 **，就像流向无服务器主机的流通常所做的那样：在一小时内重新打开五次后，Claude Code 在下一次重新打开之前等待大约六个小时。

在流重新打开之前，您将保留服务器上次获取的工具、提示和资源。要更快地获取更改，请从 `/mcp` 重新连接服务器。

### 自动重连

Claude Code 重新连接在会话中途断开的远程服务器，并在发生暂时性错误后重试 HTTP 或 SSE 服务器的首次连接。 Stdio 服务器是本地进程，Claude Code 不会自动重新连接它们。

#### 远程服务器的会话中期丢弃

Claude Code 使用指数退避重新连接已断开的远程服务器：最多尝试五次，从一秒延迟开始，每次延迟加倍。您看到的内容取决于您的运行方式 Claude Code：

* **在交互式会话中**：`/mcp` 显示服务器处于挂起状态，而 Claude Code 重新连接。五次失败尝试后，Claude Code 将服务器标记为失败，或者当服务器需要再次授权时标记为需要身份验证。当它将服务器标记为失败时，您会看到一条 `MCP server "<name>" disconnected · open /mcp to reconnect` 通知。您可以从`/mcp`手动重试。
* **在[`claude -p`](<https://code.claude.com/docs/en/headless>)中运行，[AgentSDK](<https://code.claude.com/docs/en/agent-sdk/overview>)会话**：Claude Code按相同的时间表重新连接，没有`/mcp`面板来显示尝试。

#### 首次连接失败

当 HTTP 或 SSE 服务器的首次连接因暂时性错误（例如 5xx 响应、连接被拒绝或超时）而失败时，Claude Code 最多会重试 3 次。如果连接仍然失败，Claude Code 将服务器标记为失败。

Claude Code 在这些情况下不会重试：

* WebSocket服务器的第一次连接
* 身份验证或未找到错误，因为它需要更改配置才能解决。当 [`headersHelper`](<#使用动态标头进行自定义身份验证>) 是服务器的 `Authorization` 标头的唯一来源时，Claude Code 无论如何都会重试身份验证错误，因为它会在每次尝试时重新运行帮助程序并可以获取新的凭据

#### 失败的发现请求

服务器连接后，Claude Code向其发送`tools/list`、`prompts/list`、`resources/list`等能力发现请求。 Claude Code 在出现暂时性网络或服务器错误后，会重试这些请求最多 3 次，并进行短暂的退避。它不会重试身份验证错误、4xx 响应或请求超时。

#### 自己重试失败的服务器

要重试每个失败或需要身份验证的服务器，请运行 `/mcp reconnect all`。在交互式终端中，这需要 Claude Code v2.1.284 或更高版本，早期版本在那里打印 `MCP server "all" not found`。

#### Claude 如何得知服务器出现故障

Claude Code是否告诉Claude已配置的连接失败的服务器取决于[工具搜索](<#使用-mcp-工具搜索进行缩放>)，默认情况下处于打开状态：

* 通过工具搜索，Claude Code告诉Claude哪台服务器发生故障及其连接错误，因此Claude在其响应中报告连接失败。 Claude Code 在 `ToolSearch` 结果中包含相同的信息，但未找到匹配的工具。
* 在任何 [无需工具搜索即可配置](<#配置工具搜索>), Claude Code 不报告失败的服务器连接 Claude.

### 通过渠道推送消息

MCP 服务器还可以将消息直接推送到您的会话中，以便 Claude 可以对外部事件做出反应，例如 CI 结果、监控警报或聊天消息。要启用此功能，您的服务器声明 `claude/channel` 功能，并在启动时使用 `--channels` 标志选择它。请参阅 [ 频道 ](<https://code.claude.com/docs/en/channels>) 以使用官方支持的频道，或 [ 频道参考 ](<https://code.claude.com/docs/en/channels-reference>) 来构建您自己的频道。

在 [v2 运行时 ](<#mcp-客户端运行时>) 上，协商 MCP 协议修订版 2026-07-28 的通道服务器无法传递通道消息，因此 Claude Code 不会将其注册为通道。不支持该修订版本的通道服务器会在较早的握手中进行连接并像以前一样进行注册。

默认情况下，Claude Code 向 stdio 服务器询问该版本。要将 stdio 通道服务器保留在早期握手中，请将 [`MCP_PROTOCOL_NEGOTIATION`](<https://code.claude.com/docs/en/env-vars>) 设置为 `legacy`，这会将每个服务器保留在其上。



**提示：**

温馨提示：

  * 使用 `-s` 或 `--scope` 标志指定配置的存储位置：
    * `local`（默认）：仅在当前项目中可用
    * `project`：通过`.mcp.json`文件与项目中的所有人共享
    * `user`：适用于所有项目
  * 使用 `-e` 或 `--env` 标志设置环境变量（例如，`-e KEY=value`）
  * `--transport` 和 `--header` 标志也接受 `-t` 和 `-H` 缩写形式
  * 使用 `MCP_TIMEOUT` 环境变量配置 MCP 服务器启动超时（例如，`MCP_TIMEOUT=10000 claude` 设置 10 秒超时）
  * 通过将 `timeout` 字段（以毫秒为单位）添加到该服务器的 `.mcp.json` 条目来设置每个服务器工具的执行超时，例如 `"timeout": 600000` 十分钟。这仅覆盖该服务器的 `MCP_TOOL_TIMEOUT` 环境变量
  * 当 MCP 工具输出超过 10,000 令牌时，Claude Code 显示警告，并默认限制输出为 25,000 令牌。要更改令牌限制，请设置 `MAX_MCP_OUTPUT_TOKENS` 环境变量，例如 `MAX_MCP_OUTPUT_TOKENS=50000`。警告阈值是固定的。除非服务器提高工具自身的限制，否则无论此变量如何，长度超过 50,000 字符的成功文本结果都会保存到文件中。请参阅 [MCP 输出限制和警告 ](<#mcp-输出限制和警告>)
  * 使用 `/mcp` 向需要 OAuth 2.0 身份验证的远程服务器进行身份验证



每个服务器 `timeout` 是每个工具调用的硬挂钟限制，来自服务器的进度通知不会扩展它。低于 1000 的值将被忽略，并下降到 `MCP_TOOL_TIMEOUT`，或者在未设置该变量时下降到默认值大约 28 小时。对于 HTTP、SSE 或 [claude.ai 连接器 ](<https://code.claude.com/docs/en/mcp#use-mcp-servers-from-claude-ai>) 服务器，还有第二个按请求计时器，涵盖每个请求直到服务器的第一个响应字节。 Claude Code 将该计时器设置为三个值中的最大值：60 秒、适用于服务器的工具超时和 `MCP_TIMEOUT`。未设置的 `MCP_TOOL_TIMEOUT` 的 28 小时默认值不会进入该比较，并且低于 60 秒的值不会缩短计时器。 Stdio 和 WebSocket 服务器没有每个请求计时器。

至少为 1000 的每服务器 `timeout` 也充当空闲超时的下限，如下所述：Claude Code 永远不会比每服务器 `timeout` 更早中止该服务器的工具调用空闲状态。需要 Claude Code v2.1.203 或更高版本。

对 MCP 服务器的工具调用如果未向空闲窗口发送任何响应和进度通知，则会因错误而中止，而不是等待挂钟限制。空闲超时适用于除 IDE 服务器和 SDK 进程内服务器之外的所有服务器类型。对于 HTTP、SSE、WebSocket 和 [claude.ai 连接器](<#use-mcp-servers-from-claude-ai>) 服务器，空闲窗口默认为五分钟；对于 stdio 服务器，空闲窗口默认为 30 分钟。在 v2.1.203 之前，stdio 服务器不受空闲超时的影响。

设置 [`CLAUDE_CODE_MCP_TOOL_IDLE_TIMEOUT`](<https://code.claude.com/docs/en/env-vars>) 环境变量（以毫秒为单位）以更改空闲窗口，或将其设置为 `0` 以禁用检查。

这些超时限制了调用可以运行的时间，而不总是限制了它阻止会话的时间：运行超过两分钟的主对话调用将首先移至后台任务。请参阅[长工具调用的自动后台处理](<#长工具调用的自动后台处理>)。

### 长工具调用的自动后台处理

主对话中的 MCP 工具调用在两分钟后仍在运行，并将移至后台任务而不是阻止会话。 Claude立即接收任务ID并继续工作，并在通话结束时以任务通知的形式到达结果。自动背景需要 Claude Code v2.1.212 或更高版本。

该任务出现在 [`/tasks`](<https://code.claude.com/docs/en/commands#all-commands>) 中，您也可以在其中停止它，并且它不会在退出会话后继续存在。任务的条目显示服务器报告的最新进度。

当调用在后台运行时，每次调用限制仍然适用：每个服务器 `timeout` 或 [`MCP_TOOL_TIMEOUT`](<https://code.claude.com/docs/en/env-vars>) 设置的挂钟限制，以及 [`CLAUDE_CODE_MCP_TOOL_IDLE_TIMEOUT`](<https://code.claude.com/docs/en/env-vars>) 设置的空闲超时。

设置 [`CLAUDE_CODE_MCP_AUTO_BACKGROUND_MS`](<https://code.claude.com/docs/en/env-vars>) 环境变量（以毫秒为单位）以更改阈值，或将其设置为 `0` 以关闭自动后台处理。将 `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS` 设置为 `1` 也会将其关闭，以及所有其他后台任务功能。

有些调用永远不会移至后台：

* 来自[子代理](<https://code.claude.com/docs/en/sub-agents>)的呼叫； Claude Code 仅后台主对话调用
* 调用 IDE 服务器
* 在 [ 非交互模式 ](<https://code.claude.com/docs/en/headless>) 下调用，除非 `CLAUDE_AUTO_BACKGROUND_TASKS` 设置为 `1`，因为一次性运行可能会在结果到达之前结束

在打开的 [ 诱导对话框 ](<#回复-mcp-启发请求>) 上等待的呼叫在对话框打开时不会进入后台；服务器在您的输入上被阻止，速度并不慢，因此 Claude Code 推迟移动直到对话框关闭。

### 插件提供的MCP服务器

[插件](<https://code.claude.com/docs/en/plugins/overview>)可以捆绑MCP服务器，当您启用插件时，这些服务器提供工具和集成。

**插件MCP服务器如何工作**：

* 插件在插件根目录的 `.mcp.json` 中定义 MCP 服务器或在 `plugin.json` 中内联
* 当您启用插件时，Claude Code会自动启动其MCP服务器
* Claude Code 提供插件 MCP 工具以及手动配置的 MCP 工具
* 您可以通过安装或卸载插件来添加和删除插件服务器，而不是使用 `/mcp` 命令。您仍然可以[关闭`/mcp`中已安装的插件服务器](<#禁用服务器而不删除它>)，这会阻止Claude Code连接到它，而无需删除插件

**示例插件MCP配置**：

在插件根目录的 `.mcp.json` 中：

```json theme={null}
{
  "mcpServers": {
    "database-tools": {
      "command": "${CLAUDE_PLUGIN_ROOT}/servers/db-server",
      "args": ["--config", "${CLAUDE_PLUGIN_ROOT}/config.json"],
      "env": {
        "DB_URL": "${DB_URL}"
      }
    }
  }
}
```

或者内嵌在`plugin.json`中：

```json theme={null}
{
  "name": "my-plugin",
  "mcpServers": {
    "plugin-api": {
      "command": "${CLAUDE_PLUGIN_ROOT}/servers/api-server",
      "args": ["--port", "8080"]
    }
  }
}
```

**插件MCP功能**：

* **自动生命周期**：服务器在这些点连接和断开连接：
  * 在会话启动时，Claude Code 自动连接已启用插件的服务器。在`/mcp`中，您以前使用过的远程（HTTP或SSE）插件服务器可以显示[`cached`状态](<#服务器状态详细信息>)； Claude Code 在 Claude 第一次调用其工具之一时连接它
  * 如果您在会话期间启用或禁用插件，Claude Code 将在更改应用时连接或断开其 MCP 服务器。 [应用插件更改而不重新启动](<https://code.claude.com/docs/en/plugins/cli-reference#reload-plugins>)描述了何时。在没有交互式终端的会话中，`/reload-plugins`不会连接或断开插件MCP服务器；这些更改将在您的下一个会话中生效
  * 当您重新加载时，Claude Code会保留配置未更改的插件服务器的实时连接，并且当您[从AgentSDK替换会话的MCP服务器列表](<https://code.claude.com/docs/en/agent-sdk/typescript#mcpsetserversresult>)而不命名它们时，也会执行相同的操作
  * 当您[在v2.1.246或更高版本上移动与`/cd`](<https://code.claude.com/docs/en/permissions#move-the-session-to-another-directory>)的会话时，Claude Code会连接新目录设置启用的插件的服务器，并断开不再启用的插件的服务器，因此您无需在移动后运行`/reload-plugins`
  * 在 [ 云会话 ](<https://code.claude.com/docs/en/claude-code-on-the-web>) 中，对尚未连接的插件服务器的 MCP 调用，例如在空闲会话唤醒后，按需启动服务器并等待其连接
* **路径占位符**：`${CLAUDE_PLUGIN_ROOT}`解析为插件的安装目录，`${CLAUDE_PLUGIN_DATA}`解析为其[持久状态](<https://code.claude.com/docs/en/plugins/components#path-variables-and-persistent-data>)目录，`${CLAUDE_PROJECT_DIR}`解析为稳定项目根目录。替代适用于：
  * `stdio`服务器：`command`、`args`、`env`
  * `http`、`sse`和`ws`服务器：`url`、`headers`和`headersHelper`
* **用户环境访问**：访问与手动配置服务器相同的环境变量
* **多种传输类型**：支持 stdio、SSE、HTTP 和 WebSocket 传输，但传输支持可能因服务器而异

插件服务器出现在`/mcp`中，并带有指示符显示它们来自插件。

对于插件的 stdio 服务器，`claude mcp get` 打印 `Command: stdio`、一个空的 `Args:` 行，以及每个环境变量作为 `NAME=[REDACTED]`。这些值是隐藏的，因为它们可以携带凭据。

**插件MCP工具名称**：

插件捆绑的 MCP 服务器中的工具在其可调用名称中包含插件名称和服务器密钥。完整形式为 `mcp__plugin_<plugin-name>_<server-name>__<tool-name>`，其中 `A-Z`、`a-z`、`0-9`、`_` 和 `-` 之外的任何字符都替换为 `_`。对于捆绑在名为 `my-plugin` 的插件中的 `database-tools` 服务器，`query` 工具可调用为：

```
mcp__plugin_my-plugin_database-tools__query
```

在 [ 权限规则 ](<https://code.claude.com/docs/en/permissions>)、技能的 `allowed-tools` 列表、[ 子代理的 `tools` 字段 ](<https://code.claude.com/docs/en/sub-agents#available-tools>) 或 [ 挂钩中引用工具时，请使用此全名匹配器](<https://code.claude.com/docs/en/hooks#match-mcp-tools>)。针对裸服务器密钥（例如 `mcp__database-tools__.*`）编写的钩子匹配器永远不会为插件捆绑服务器触发。

服务器本身以范围名称 `plugin:<plugin-name>:<server-name>` 进行注册，例如 `plugin:my-plugin:database-tools`。在需要配置的服务器名称的地方使用该名称，例如 [`mcp_tool` 挂钩的 `server` 字段 ](<https://code.claude.com/docs/en/hooks#mcp-tool-hook-fields>)。

有关将 MCP 服务器与插件捆绑在一起的详细信息，请参阅 [ 插件组件参考 ](<https://code.claude.com/docs/en/plugins/components#mcp-servers>)。

## MCP安装范围

MCP 服务器可以在三个范围内进行配置。您选择的范围控制服务器加载的项目以及是否与您的团队共享配置。管理员还可以通过[托管配置](<#托管-mcp-配置>)为每个用户部署或提供服务器。

|适用范围|加载于|与团队共享|存储在|
| - | - | - | - |
|[本地](<#本地范围>)|仅当前项目|否| `~/.claude.json` |
|[项目](<#项目范围>)|仅当前项目|是的，通过版本控制|`.mcp.json` 在项目根目录中|
|[用户](<#用户范围>)|您的所有项目|否| `~/.claude.json` |

### 本地范围

本地范围是默认范围。本地范围的服务器仅在您添加它的项目中加载，并且对您保持私有。 Claude Code将其存储在该项目路径下的`~/.claude.json`中，因此同一服务器不会出现在您的其他项目中。将本地范围用于个人开发服务器、实验配置或具有您不希望在版本控制中使用的凭据的服务器。



**注释：**

MCP 服务器的术语“本地范围”与一般本地设置不同。 MCP 本地范围的服务器存储在 `~/.claude.json`（您的主目录）中，而常规本地设置使用 `.claude/settings.local.json`（在项目目录中）。有关设置文件位置的详细信息，请参阅[设置](<https://code.claude.com/docs/en/settings#where-settings-live>)。


```bash theme={null}
# Add a local-scoped server (default)
claude mcp add --transport http stripe https://mcp.stripe.com

# Explicitly specify local scope
claude mcp add --transport http stripe --scope local https://mcp.stripe.com
```

该命令将服务器写入 `~/.claude.json` 内当前项目的条目中。下面的示例显示了从 `/path/to/your/project` 运行它时的结果：

```json theme={null}
{
  "projects": {
    "/path/to/your/project": {
      "mcpServers": {
        "stripe": {
          "type": "http",
          "url": "https://mcp.stripe.com"
        }
      }
    }
  }
}
```

### 项目范围

项目范围的服务器通过将配置存储在项目根目录的 `.mcp.json` 文件中来实现团队协作。当您添加项目范围的服务器时，Claude Code 会自动使用适当的配置结构创建或更新此文件。将 `.mcp.json` 签入版本控制，以便团队中的每个人都获得相同的 MCP 工具和服务。

```bash theme={null}
# Add a project-scoped server
claude mcp add --transport http shared-server --scope project https://example.com/mcp
```

生成的 `.mcp.json` 文件遵循标准化格式：

```json theme={null}
{
  "mcpServers": {
    "shared-server": {
      "type": "http",
      "url": "https://example.com/mcp"
    }
  }
}
```

出于安全原因，在使用 `.mcp.json` 文件中的项目范围服务器之前，Claude Code 会在交互式会话中提示您进行批准。要重置这些批准选择，请运行 `claude mcp reset-project-choices`。

在 `claude -p` 运行中，[Agent SDK](<https://code.claude.com/docs/en/headless>) 会话和 [ 云会话 ](<https://code.claude.com/docs/en/claude-code-on-the-web>)、Claude Code 无法显示该提示：它会在不询问的情况下加载项目范围的服务器。 Claude Code 还会在您以 `bypassPermissions` 模式启动且在用户设置或托管设置中设置 [`skipDangerousModePermissionPrompt`](<https://code.claude.com/docs/en/settings-reference#skipdangerousmodepermissionprompt>) 的会话中跳过提示。无论如何都要阻止服务器：

* 将其添加到[`disabledMcpjsonServers`](<https://code.claude.com/docs/en/settings-reference#disabledmcpjsonservers>)，这会在每个权限模式下阻止它。
* 使用 [`--setting-sources`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 或 SDK 的 `settingSources` 选项完全排除项目设置。
* 使用 [`--strict-mcp-config`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 开始会话。 Claude Code 然后仅使用您通过 `--mcp-config` 传递的 MCP 服务器。跳过项目范围服务器 Claude Code 未加载的审批提示需要 Claude Code v2.1.246 或更高版本；在 v2.1.246 之前，严格的会话仍在等待批准，这导致后台会话在启动时等待。请参阅 [ 使用 managed-mcp.json](<https://code.claude.com/docs/en/managed-mcp#exclusive-control-with-managed-mcp-json>) 进行独占控制，了解该标志在托管 MCP 文件下的作用。

[项目服务器批准和工作区信任](<#项目服务器批准和工作区信任>)介绍了提交到存储库的批准如何与工作区信任交互。

### 用户范围

用户范围的服务器存储在 `~/.claude.json` 中并提供跨项目可访问性，使它们在您计算机上的所有项目中可用，同时对您的用户帐户保持私有。此范围非常适合您在不同项目中经常使用的个人实用服务器、开发工具或服务。

```bash theme={null}
# Add a user server
claude mcp add --transport http hubspot --scope user https://mcp.hubspot.com/anthropic
```

### 范围层次结构和优先级

当同一服务器在多个位置定义时，Claude Code 将使用最高优先级源中的定义连接到该服务器一次。使用来自该源的整个服务器条目；字段不会跨范围合并。

1. 本地范围
2. 项目范围
3. 用户范围
4. [插件提供的服务器](<https://code.claude.com/docs/en/plugins/components#mcp-servers>)
5. [claude.ai 连接器](<#use-mcp-servers-from-claude-ai>)

Claude Code 按名称匹配三个范围内的重复项。它按端点匹配插件和连接器，因此指向相同 URL 或命令作为上述启用的服务器的端点被视为重复项。

当两个 URL 拼写仅在方案或主机的字母大小写、方案的默认端口（例如 `https` 上的 `:443` 或尾部斜杠）不同时，它们被视为同一端点。不同的路径、查询字符串、用户信息或非默认端口构成两个服务器。

您的组织通过 [`managedMcpServers`](<https://code.claude.com/docs/en/managed-mcp#provide-servers-through-managed-settings>) 托管设置提供的服务器排名高于所有这些设置，因此当其中一个服务器重复它时，Claude Code 将连接组织的定义。需要 Claude Code v2.1.259 或更高版本。

如果您在 [ 桌面应用程序的“代码”选项卡 ](<https://code.claude.com/docs/en/desktop#mcp-servers-from-the-claude-desktop-chat-app>) 中在 `~/.claude.json`（用户范围）顶层和 `.mcp.json` 中使用相同的 stdio 服务器名称打开本地会话，则“代码”选项卡将使用 `~/.claude.json` 定义。

### `.mcp.json` 中的环境变量扩展

Claude Code 支持 `.mcp.json` 文件中的环境变量扩展，允许团队共享配置，同时保持机器特定路径和敏感值（如 API 键）的灵活性。

#### 支持的语法

* `${VAR}`：扩展为环境变量`VAR`的值
* `${VAR:-default}`：如果设置，则扩展为 `VAR`，否则使用 `default`

#### 扩张地点

环境变量可以扩展为：

* `command`：服务器可执行路径
* `args`：命令行参数
* `env`：传递到服务器的环境变量
* `url`：适用于 HTTP 服务器类型
* `headers`：用于HTTP服务器身份验证

#### 变量扩展示例

```json theme={null}
{
  "mcpServers": {
    "api-server": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": {
        "Authorization": "Bearer ${API_KEY}"
      }
    }
  }
}
```

#### 取消设置没有默认值的变量

如果未设置引用的环境变量并且没有默认值，则配置仍会加载：Claude Code 在 `claude mcp list` 输出中报告该服务器的缺少变量警告，并按原样使用未展开的 `${VAR}` 文本。设置变量或添加 `:-default` 后备，以便服务器以您想要的值启动。在远程服务器的 `url` 和 `headers` 中，某些凭证变量 [ 读取为空 ](<#读取为空的凭证变量>)，没有任何警告。

#### 读取为空的凭证变量

在远程服务器的 `url` 和 `headers` 中，Claude Code 从您的环境中读取凭证变量为空，而不是扩展它们。这可以防止项目的 `.mcp.json` 或插件将您的 Claude Code 或云提供商凭据发送到它指定的服务器。如果您写入 `Bearer ${ANTHROPIC_AUTH_TOKEN}`，服务器会收到没有凭据的 `Bearer ` 并拒绝请求，通常会带有 `401`。 Claude Code 报告连接失败。

涵盖的名称是：

* Claude Code自己的凭据，例如`ANTHROPIC_API_KEY`和`ANTHROPIC_AUTH_TOKEN`
* 您的云提供商的凭据，例如 `AWS_BEARER_TOKEN_BEDROCK`
* 您的环境携带的其他凭据，例如 `HTTPS_PROXY` 和 `NPM_TOKEN`

无论您是否设置了变量，覆盖的名称都会读为空，并且忽略它的 `:-default` 后备。提供者基础 URL（例如 `ANTHROPIC_BASE_URL`）仍在扩展，因此 `"url": "${ANTHROPIC_BASE_URL}/mcp"` 可以工作，除非 URL 的值本身嵌入了用户名和密码等凭证。

该集合之外的名称（例如 `API_KEY`）将按书写方式扩展。要为服务器提供所涵盖的凭据之一，请将其复制到具有您自己的名称的变量中，并引用该名称。

当远程服务器的 `url` 或 `headers` 引用您设置的覆盖变量时，Claude Code 在调试日志行中对其进行命名。要读取该行，请运行 `claude --debug-file /tmp/claude-debug.log` 并在该文件中搜索 `never expanded toward a remote server`。

#### 引用如何出现在 `/mcp` 和 CLI 输出中

对于本地、项目或用户 [ 范围 ](<#mcp安装范围>) 中的服务器，以下表面按名称而不是其解析值显示 `${VAR}` 引用：

* 服务器的 `/mcp` 详细视图中的 URL 或命令行
* `claude mcp list` 和 `claude mcp get` 输出

`/mcp` 详细视图以这种方式显示 Claude Code v2.1.268 或更高版本中的引用。

对于您的组织通过 `managedMcpServers` 设置提供的服务器，这些表面仅显示 [ URL 的主机 ](<https://code.claude.com/docs/en/managed-mcp#what-users-can-see-and-change>)。

要检查连接失败时`claude mcp list`、`claude mcp get`和`/mcp`显示的内容，请参阅[服务器状态详细信息](<#服务器状态详细信息>)。

## 实际例子

### 示例：连接到 GitHub 进行代码审查

GitHub 的远程 MCP 服务器使用作为标头传递的 GitHub 个人访问令牌进行身份验证。要获得一个，请打开您的 [GitHub 令牌设置 ](<https://github.com/settings/personal-access-tokens>)，生成一个新的细粒度令牌，可以访问您想要 Claude 使用的存储库，然后添加服务器：

```bash theme={null}
claude mcp add --transport http github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer YOUR_GITHUB_PAT"
```

将 `YOUR_GITHUB_PAT` 替换为您的个人访问令牌。 `claude mcp add` 命令保存配置而不验证凭据，因此此处接受占位符值，但服务器稍后无法连接。要验证连接，请运行 `/mcp` 并检查服务器是否显示 `connected`。凭据错误的服务器显示 `failed`，故障详细信息包括服务器返回的 HTTP 状态，例如 401。

然后使用GitHub：

```text wrap theme={null}
Review PR #456 and suggest improvements
```

```text wrap theme={null}
Create a new issue for the bug we just found
```

```text wrap theme={null}
Show me all open PRs assigned to me
```

### 示例：查询您的PostgreSQL数据库

[DBHub](<https://github.com/bytebase/dbhub>)，即`@bytebase/dbhub`包，是一个MCP服务器，它通过您在`--dsn`中传递的连接字符串将Claude连接到关系数据库。在连接字符串中使用只读数据库用户，以便查询 Claude 运行无法修改数据：

```bash theme={null}
claude mcp add --transport stdio db -- npx -y @bytebase/dbhub \
  --dsn "postgresql://readonly:pass@prod.db.com:5432/analytics"
```

要确认服务器启动，请运行`/mcp`并检查`db`是否显示`connected`。

然后自然地查询你的数据库：

```text wrap theme={null}
What's our total revenue this month?
```

```text wrap theme={null}
Show me the schema for the orders table
```

```text wrap theme={null}
Find customers who haven't made a purchase in 90 days
```

## 使用远程 MCP 服务器进行身份验证

许多基于云的 MCP 服务器需要身份验证。 Claude Code 支持 OAuth 2.0 以实现安全连接。

当服务器响应 `401 Unauthorized` 或 `403 Forbidden` 时，Claude Code 将远程服务器标记为需要身份验证。 Claude Code 显示的内容取决于服务器：

* 对于您尚未登录的服务器，任一状态代码都会在 `/mcp` 中对其进行标记，以便您可以完成 OAuth 流程。
* 对于 [claude.ai 连接器 ](<#use-mcp-servers-from-claude-ai>)，由于 claude.ai 拒绝您的会话令牌而导致的 `401` 不会标记连接器，因为重新授权连接器无法修复您的登录。 Claude Code 显示 [ 会话令牌拒绝状态 ](<https://code.claude.com/docs/en/errors#claude-ai-rejected-the-session-token>)。
* 对于您配置了 `Authorization` 标头的服务器，在 `headers` 中或通过 [`headersHelper`](<#使用动态标头进行自定义身份验证>)、`401` 或 `403` 连接时不会标记服务器，因为要修复的凭据是您配置的凭据。 Claude Code 报告连接失败。如果您从 `${VAR}` 引用设置该标头，请检查该变量是否是一个 Claude Code [ 读为空 ](<#读取为空的凭证变量>)。
* 对于连接器 [传送到云会话](<#连接器如何达到claude-code>), Claude Code 不运行登录流程，因为会话的代理使用您在 claude.ai 中授予的授权对连接器进行身份验证。当那里的连接器需要再次授权时，重新连接 [claude.ai/customize/connectors](<https://claude.ai/customize/connectors>) 而不是来自会话。

当对您已登录的 OAuth 服务器的请求返回 `401 Unauthorized` 时，Claude Code 会刷新存储的令牌、重新连接并重试该请求一次。仅当重试也失败时，它才会在 `/mcp` 中标记服务器。在 v2.1.206 之前，由于暂时性原因（例如网络错误）而失败的令牌刷新会将 OAuth 服务器标记为需要对会话的其余部分进行身份验证，即使其刷新令牌仍然有效。

当服务器拒绝存储的刷新令牌时，Claude Code立即显示指向`/mcp`的通知。打开`/mcp`并选择**在服务器上重新验证**，以便在下一次工具调用失败之前再次登录。

返回指向其授权服务器的 `WWW-Authenticate` 标头的自定义服务器将获得与任何其他远程服务器相同的自动发现功能。

当一台或多台配置的服务器需要身份验证时，Claude Code 还会显示启动通知，因此您不必打开 `/mcp` 来发现哪些服务器需要登录。该通知仅计算您可以从 Claude Code 登录的服务器。在 v2.1.218 之前，它还计算了 claude.ai 中未连接的 [claude.ai 连接器 ](<#use-mcp-servers-from-claude-ai>)，您只能从 claude.ai 设置中连接。

该通知会公布每个服务器一次，并在以后启动时将其排除在计数之外，直到该服务器已连接并需要再次登录。 `/mcp` 仍然列出了每个需要登录的服务器。

在非交互模式下，没有 `/mcp` 面板，因此 Claude Code 无法为您运行 OAuth 流程。从v2.1.196开始，当配置的服务器在启用[工具搜索](<#使用-mcp-工具搜索进行缩放>)（默认情况下）的`claude -p`或AgentSDK期间需要身份验证时，Claude Code告诉Claude服务器的工具在您授权之前不可用。然后，Claude 可以命名需要登录的服务器，而不是像未配置服务器一样进行响应。使用 `/mcp` 或 `claude mcp login <name>` 从交互式会话完成登录。

如果您为服务器配置了 `headers.Authorization` 并且服务器拒绝该标头，则 Claude Code 会将连接报告为失败，而不是回退到 OAuth。检查令牌对于 MCP 端点是否有效，或删除标头以使用 OAuth 流。



  

#### 添加需要认证的服务器

如果您已在 [MCP 快速入门 ](<https://code.claude.com/docs/en/mcp-quickstart#connect-a-server-that-requires-sign-in>) 中添加 `sentry` 服务器，请跳过此步骤：在相同范围内使用相同服务器名称再次运行 `claude mcp add` 失败，并显示 `MCP server sentry already exists in local config`。否则，运行：

    ```bash theme={null}
    claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
    ```
  


  

#### 在Claude Code内使用/mcp命令

在Claude Code中，使用命令：

    ```text wrap theme={null}
    /mcp
    ```

然后按照浏览器中的步骤进行登录。
  




**提示：**

温馨提示：

  * 身份验证令牌安全存储并自动刷新
  * 使用`/mcp`菜单中的“清除身份验证”来撤销访问权限
  * 如果您的浏览器没有自动打开，请复制提供的URL并手动打开
  * 如果身份验证后浏览器重定向失败并出现连接错误，请将浏览器地址栏中的完整回调 URL 粘贴到 Claude Code 中显示的 URL 提示中
  * OAuth 身份验证适用于 HTTP 服务器



### 从命令行进行身份验证

`claude mcp login <name>` 命令直接从 shell 运行已配置服务器的 OAuth 流程，因此您无需在会话内打开 `/mcp` 面板。对于 claude.ai 连接器，请按照 [从 shell 再次授权连接器 ](<https://code.claude.com/docs/en/remote-control#authorize-a-connector-again-from-your-shell>)。

```bash theme={null}
claude mcp login sentry
```

要稍后清除存储的凭据，请运行 `claude mcp logout <name>`。

`claude mcp login` 检测本地浏览器何时不可用，例如在 SSH 会话期间或在没有显示服务器的 Linux 上，并打印授权 URL，而不是尝试打开浏览器。在本地计算机上打开 URL，然后根据提示将浏览器地址栏中的完整重定向 URL 粘贴回。该命令需要一个交互式终端来执行粘贴步骤，因此请使用 `ssh -t` 连接。即使检测到本地浏览器，传递 `--no-browser` 也会强制显示 URL 提示。

```bash theme={null}
claude mcp login sentry --no-browser
```

### 使用固定的 OAuth 回调端口

某些MCP服务器需要提前注册特定的重定向URI。默认情况下，Claude Code 为 OAuth 回调选择一个随机可用端口。使用 `--callback-port` 修复端口，使其与 `http://localhost:PORT/callback` 形式的预注册重定向 URI 匹配。如果登录 Claude Code v2.1.229 失败且重定向 URI 不匹配，请参阅 [使用预配置的 OAuth 凭据 ](<#使用预配置的-oauth-凭据>) 下的版本说明。

您可以单独使用 `--callback-port`（通过动态客户端注册）或与 `--client-id` 一起使用（通过预配置的凭据）。

```bash theme={null}
# Fixed callback port with dynamic client registration
claude mcp add --transport http \
  --callback-port 8080 \
  my-server https://mcp.example.com/mcp
```

### 使用预配置的 OAuth 凭据

某些 MCP 服务器不支持通过动态客户端注册进行自动 OAuth 设置。如果您看到类似“不兼容的身份验证服务器：不支持动态客户端注册”的错误，则服务器需要预先配置的凭据。 Claude Code 还支持使用客户端 ID 元数据文档 (CIMD) 而不是动态客户端注册的服务器，并自动发现这些服务器。如果自动发现失败，请先通过服务器的开发人员门户注册 OAuth 应用程序，然后在添加服务器时提供凭据。



  

#### 向服务器注册 OAuth 应用程序

通过服务器的开发人员门户创建应用程序，并记下您的客户端 ID 和客户端密钥。

如果注册表要求重定向 URI，请选择任何可用端口并输入带有该端口的 `http://localhost:PORT/callback`。您将在下一步中使用相同的端口。

在 v2.1.229 中，Claude Code 发送了 `http://127.0.0.1:PORT/callback`，而与注册的重定向 URI 完全匹配的服务器拒绝了登录，并且重定向 URI 不匹配。 Claude Code v2.1.231恢复了`localhost`形式。要在 v2.1.229 上恢复，请升级 Claude Code，或临时将 `http://127.0.0.1:PORT/callback` 表单添加到服务器的已注册重定向 URI。
  


  

#### 使用您的凭据添加服务器

这些选项卡涵盖两个命令：`claude mcp add` 将您的客户端 ID 和回调端口作为标志，而 `claude mcp add-json` 将它们放在 `oauth` 对象中。如果您注册了重定向URI，请将回调端口设置为该URI中的端口。

    

      

#### 克劳德·MCP 添加

使用 `--client-id` 传递应用程序的客户端 ID。 `--client-secret` 标志提示输入带有屏蔽输入的秘密：

        ```bash theme={null}
        claude mcp add --transport http \
          --client-id your-client-id --client-secret --callback-port 8080 \
          my-server https://mcp.example.com/mcp
        ```
      


      

#### 克劳德 mcp add-json

将 `oauth` 对象包含在 JSON 配置中，并将 `--client-secret` 作为单独的标志传递：

        ```bash theme={null}
        claude mcp add-json my-server \
          '{"type":"http","url":"https://mcp.example.com/mcp","oauth":{"clientId":"your-client-id","callbackPort":8080}}' \
          --client-secret
        ```
      


      

#### claude mcp add-json（仅限回调端口）

要仅修复回调端口并让 Claude Code 自动注册客户端，请自行设置 `callbackPort`：

        ```bash theme={null}
        claude mcp add-json my-server \
          '{"type":"http","url":"https://mcp.example.com/mcp","oauth":{"callbackPort":8080}}'
        ```
      


      

#### CI / 环境变量

通过环境变量设置秘密以跳过交互式提示：

        ```bash theme={null}
        MCP_CLIENT_SECRET=your-secret claude mcp add --transport http \
          --client-id your-client-id --client-secret --callback-port 8080 \
          my-server https://mcp.example.com/mcp
        ```
      

    

  


  

#### 在Claude Code中进行身份验证

运行Claude Code中的`/mcp`并按照浏览器登录流程操作。
  




**提示：**

温馨提示：

  * 客户端密钥安全地存储在您的系统钥匙串 (macOS) 或凭证文件中，而不是存储在您的配置中
  * 仅当添加服务器时才可以设置客户端密钥。当您使用 `claude mcp login` 或从 `/mcp` 进行身份验证时，Claude Code 使用存储的机密，并且不会提示输入或读取 `MCP_CLIENT_SECRET`
  * 要稍后添加或更改密钥，请使用 `claude mcp remove <name>` 删除服务器，然后使用 `--client-secret` 和相同的 `--scope` 再次添加它
  * 如果服务器使用没有密钥的公共 OAuth 客户端，则仅使用 `--client-id`，而不使用 `--client-secret`
  * 这些标志仅适用于 HTTP 和 SSE 传输。它们对 stdio 服务器没有影响
  * 使用 `claude mcp get <name>` 验证是否为服务器配置了 OAuth 凭据



### 覆盖 OAuth 元数据发现

将 Claude Code 指向特定的 OAuth 授权服务器元数据 URL 以绕过默认发现链。当 MCP 服务器的标准端点出现错误时，或者当您想要通过内部代理路由发现时，请设置 `authServerMetadataUrl`。默认情况下，Claude Code 首先检查 `/.well-known/oauth-protected-resource` 处的 RFC 9728 受保护资源元数据，然后回退到 `/.well-known/oauth-authorization-server` 处的 RFC 8414 授权服务器元数据。

在 `.mcp.json` 中服务器配置的 `oauth` 对象中设置 `authServerMetadataUrl`：

```json theme={null}
{
  "mcpServers": {
    "my-server": {
      "type": "http",
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "authServerMetadataUrl": "https://auth.example.com/.well-known/openid-configuration"
      }
    }
  }
}
```

URL必须使用`https://`。元数据 URL 的 `scopes_supported` 覆盖上游服务器通告的范围。

### 限制 OAuth 范围

设置 `oauth.scopes` 以在授权流程期间固定范围 Claude Code 请求。当上游授权服务器公布的范围多于您想要授予的范围时，这是将 MCP 服务器限制为安全团队批准的子集的受支持方法。该值为单个空格分隔的字符串，与 RFC 6749 §3.3 中的 `scope` 参数格式匹配。

```json theme={null}
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp",
      "oauth": {
        "scopes": "channels:read chat:write search:read"
      }
    }
  }
}
```

`oauth.scopes` 优先于 `authServerMetadataUrl` 以及服务器在 `/.well-known` 处发现的范围。将其保留为未设置，以便让 MCP 服务器确定请求的范围集。

从 v2.1.196 开始，当 `oauth.scopes` 未设置时，Claude Code 请求服务器的 `WWW-Authenticate` 标头或其受保护的资源元数据提供的范围，并且当两者均未提供时，不发送 `scope` 参数。它不再请求自动发现的授权服务器元数据中的完整 `scopes_supported` 目录。请求该目录会使宣传仅限管理或模板范围的身份提供商拒绝授权请求，并出现 `invalid_scope` 错误。从配置的 `authServerMetadataUrl` 获取的元数据仍然提供其 `scopes_supported` 作为请求的范围。

如果授权服务器在 `scopes_supported` 中通告 `offline_access`，Claude Code 会将其附加到固定范围，以便无需重新登录浏览器即可刷新访问令牌。

如果服务器稍后为工具调用返回 403 `insufficient_scope`，则调用将失败，并显示一条 [`needs additional permissions`](<https://code.claude.com/docs/en/errors#mcp-server-needs-you-to-sign-in-again>) 消息，该消息指定服务器请求的范围。服务器在`/mcp`中显示需要身份验证。

如果该范围不在您固定的 `oauth.scopes` 中，请添加它，然后运行 `/mcp` 并再次验证服务器。 Claude Code 请求固定范围而不是服务器指定的范围，因此如果您在不添加它的情况下再次进行身份验证，您获得的令牌仍然缺少它。

### 使用动态标头进行自定义身份验证

如果您的 MCP 服务器使用 OAuth 以外的身份验证方案，例如 Kerberos、短期令牌或内部 SSO，请使用 `headersHelper` 在连接时生成请求标头。 Claude Code 运行该命令并将其输出合并到连接标头中。

```json theme={null}
{
  "mcpServers": {
    "internal-api": {
      "type": "http",
      "url": "https://mcp.internal.example.com",
      "headersHelper": "/opt/bin/get-mcp-auth-headers.sh"
    }
  }
}
```

该命令也可以是内联的：

```json theme={null}
{
  "mcpServers": {
    "internal-api": {
      "type": "http",
      "url": "https://mcp.internal.example.com",
      "headersHelper": "echo '{\"Authorization\": \"Bearer '\"$(get-token)\"'\"}'"
    }
  }
}
```

**要求：**

* 该命令必须将字符串键值对的 JSON 对象写入 stdout
* Claude Code 在 shell 中运行该命令，并在 10 秒后放弃它
* Claude Code 通过配置服务器的 [ 选择命令的工作目录 ](<#助手运行的地方>)，因此将脚本指定为绝对路径或将其放在 `PATH` 上
* 动态标头会覆盖任何同名的静态 `headers`

一旦项目和本地范围服务器的 [ 信任规则 ](<#在-headershelper-运行之前信任文件夹>) 允许帮助程序运行，Claude Code 在每个连接、会话启动和重新连接时运行全新的帮助程序。它不会缓存结果，因此您的脚本负责任何令牌重用。

如果工具调用返回 `401 Unauthorized` 或 `403 Forbidden`，Claude Code 会自动在相同规则下重新运行帮助器，重新连接新标头，并重试调用一次。仅当重试也失败时，Claude Code 才会在 `/mcp` 中将服务器标记为需要身份验证。

当帮助程序的输出包含 `Authorization` 标头时，Claude Code 使用该凭据作为服务器的身份验证，并且不会回退到服务器的 OAuth。

如果服务器在连接时拒绝助手的凭据，Claude Code 会将连接报告为失败，而不是将服务器标记为需要身份验证。修复助手返回的凭据，然后从 `/mcp` 重新连接以重新运行助手。

Claude Code 在执行帮助程序时设置这些环境变量：

|变量|价值|
| :- | :- |
| `CLAUDE_CODE_MCP_SERVER_NAME` |MCP服务器的名称|
| `CLAUDE_CODE_MCP_SERVER_URL` |MCP 服务器的 URL|
| `CLAUDE_PLUGIN_ROOT` |插件的根目录。仅当[插件](<https://code.claude.com/docs/en/plugins/components#mcp-servers>)提供服务器时设置|

使用它们编写一个为多个 MCP 服务器提供服务的帮助程序脚本。

插件提供的 `headersHelper` 无法引用插件的 [`${user_config.*}`](<https://code.claude.com/docs/en/plugins/manifest-reference#user-configuration>) 值，因为该命令通过 shell 运行。 Claude Code 报告服务器配置错误，并显示 [ 错误 ](<https://code.claude.com/docs/en/errors#plugin-command-references-user-config>)，并且不替换该值。将 `${user_config.KEY}` 放入服务器的 `headers` 字段中，该字段不是 shell 解析的，或者让帮助程序脚本从配置文件中读取值。在 v2.1.207 之前，`headersHelper` 替换了 `${user_config.*}` 值。

#### 助手运行的地方

Claude Code 从声明服务器的配置中选择 `headersHelper` 命令的工作目录。 Claude 在 Bash 中运行的 `cd` 不会移动它，而 [`/cd`](<https://code.claude.com/docs/en/permissions#move-the-session-to-another-directory>) 仅针对从会话的主工作目录运行的服务器移动它。下面的每一行给出了 `headersHelper` 命令中的相对路径解析的目录。

|您配置服务器的位置|工作目录|
| :- | :- |
|一个[插件](<https://code.claude.com/docs/en/plugins/components#mcp-servers>)|插件的根目录|
|项目 `.mcp.json` 或 [ 本地范围 ](<#本地范围>) 服务器|声明服务器的项目目录|
|项目中的 agent 文件、来自 SDK 的 `mcpServers` 选项或 `setMcpServers()` 方法的服务器，或 [`--mcp-config`](<https://code.claude.com/docs/en/cli-reference>)|会话的[主工作目录](<https://code.claude.com/docs/en/permissions#working-directories>)|
| [用户范围](<#用户范围>), [管理的 MCP](<https://code.claude.com/docs/en/managed-mcp>), a [克劳德.ai 连接器](<#use-mcp-servers-from-claude-ai>)，或一个 agent 来自项目外部的文件，包括来自 `--add-dir` 目录 |你的配置目录，`~/.claude`，除非你设置了[`CLAUDE_CONFIG_DIR`](<https://code.claude.com/docs/en/env-vars>)|

在 v2.1.238 之前，Claude Code 还从项目外部的启动目录运行用户范围、托管和 claude.ai 连接器服务器以及 agent 文件的帮助程序。

#### 助手可以读取哪些变量

存储库或插件提供的 `headersHelper` 是您没有编写的命令，因此 Claude Code 运行它时不需要环境中的凭据变量，例如 `ANTHROPIC_API_KEY`。您配置服务器的位置决定这是否适用：

* **删除了**：项目`.mcp.json`或插件中的服务器，以及项目或`--add-dir`目录中agent文件中的内联服务器
* **未删除**：位于[用户](<#用户范围>)或[本地范围](<#本地范围>)的服务器，在[管理的MCP](<https://code.claude.com/docs/en/managed-mcp>)中，来自[claude.ai 连接器 ](<#use-mcp-servers-from-claude-ai>)，或由 SDK 或 [`--mcp-config`](<https://code.claude.com/docs/en/cli-reference>) 提供，以及来自 `~/.claude/agents/` 的 agent 文件中的内联服务器，来自托管设置，或通过 `--agents` 传递

除了 Git 的 `GIT_CONFIG_KEY_<n>` 变量之外，Claude Code 还会从环境中删除名称看起来像凭据的每个变量，例如名称为 `TOKEN`、`SECRET`、`PASSWORD`、`KEY` 或 `AUTH`无论哪种字母大小写，因此 `ANTHROPIC_API_KEY` 和 `MY_REGISTRY_TOKEN` 都被删除。 Claude Code 还删除名称不遵循该模式的凭证变量的固定列表，例如 `ANTHROPIC_CUSTOM_HEADERS`。

当这适用于您的助手时，让脚本从文件或凭证存储中读取其凭证。如果服务器的 `url` [ 包含这些变量 ](<#environment-variable-expansion-in-mcp-json>) 之一的实时值，例如 `MY_REGISTRY_TOKEN`，则帮助程序收到的 `CLAUDE_CODE_MCP_SERVER_URL` 值也会将该部分替换为 `REDACTED`。

#### 在 headersHelper 运行之前信任文件夹

Claude Code 将 `headersHelper` 作为任意 shell 命令执行。对于项目 `.mcp.json` 或 [ 本地范围 ](<#本地范围>) 中的服务器，只有在您接受声明服务器的项目目录的 [ 信任对话框 ](<https://code.claude.com/docs/en/permissions#project-allow-rules-and-workspace-trust>) 后，它才会运行帮助程序。在 v2.1.238 之前， `claude -p` 或 SDK 会话在不检查信任的情况下运行这些帮助程序，一旦您信任父文件夹，交互式会话就会运行它们。

* **信任不算数**：父文件夹的信任，以及自动信任 `claude -p` 或 SDK 会话获取时间为 [设置文件中的挂钩](<https://code.claude.com/docs/en/permissions#what-runs-before-you-trust-a-folder>)
* **直到您信任文件夹**：Claude Code单独使用其静态`headers`连接服务器。在 `claude -p` 或 SDK 会话中，它还会将每个服务器打印一个 [`headersHelper not run`](<https://code.claude.com/docs/en/errors#headershelper-not-run>) 行到 stderr，告诉您如何授予信任。
* **无需对话即可信任**：将`~/.claude.json`中的`projects["<path>"].hasTrustDialogAccepted`设置为`true`。 `<path>` 是文件夹 [项目允许规则和工作区信任 ](<https://code.claude.com/docs/en/permissions#project-allow-rules-and-workspace-trust>) 表示 Claude Code 开启信任。

Claude Code 将相同的规则应用于在 [agent 文件 ](<https://code.claude.com/docs/en/sub-agents#scope-mcp-servers-to-a-subagent>) 中声明为内联的服务器，检查 agent 文件的来源：您的项目、其 `.claude/agents/` 目录或 `--add-dir` 目录中的文件。除非您[信任该项目或目录本身](<https://code.claude.com/docs/en/permissions#what-runs-before-you-trust-a-folder>)，Claude Code根本不会加载服务器，因此它的帮助程序也永远不会运行。

## 从 JSON 配置添加 MCP 服务器

如果您有 MCP 服务器的 JSON 配置，则可以直接添加它：



  

#### 从 JSON 添加 MCP 服务器

    ```bash theme={null}
    # Basic syntax
    claude mcp add-json <name> '<json>'

    # Example: Adding an HTTP server with JSON configuration
    claude mcp add-json weather-api '{"type":"http","url":"https://api.weather.com/mcp","headers":{"Authorization":"Bearer token"}}'

    # Example: Adding a stdio server with JSON configuration
    claude mcp add-json local-weather '{"type":"stdio","command":"/path/to/weather-cli","args":["--api-key","abc123"],"env":{"CACHE_DIR":"/tmp"}}'

    # Example: Adding an HTTP server with pre-configured OAuth credentials
    claude mcp add-json my-server '{"type":"http","url":"https://mcp.example.com/mcp","oauth":{"clientId":"your-client-id","callbackPort":8080}}' --client-secret
    ```
  


  

#### 验证服务器已添加

    ```bash theme={null}
    claude mcp get weather-api
    ```
  




**提示：**

温馨提示：

  * 确保 JSON 在 shell 中正确转义
  * JSON 必须符合 MCP 服务器配置架构
  * 您可以使用 `--scope user` 将服务器添加到您的用户配置中，而不是特定于项目的配置



## 从 Claude 桌面导入 MCP 服务器

如果您已经在Claude桌面中配置了MCP服务器，则可以导入它们：



  

#### 从 Claude 桌面导入服务器

    ```bash theme={null}
    # Basic syntax
    claude mcp add-from-claude-desktop
    ```
  


  

#### 选择要导入的服务器

运行该命令后，您将看到一个交互式对话框，允许您选择要导入的服务器。
  


  

#### 验证服务器是否已导入

    ```bash theme={null}
    claude mcp list
    ```
  


通过`claude mcp`命令添加的服务器名称只能包含字母、数字、连字符和下划线。 Claude 桌面版不应用该限制，因此无法导入名称中包含任何其他字符（例如空格）的 Claude 桌面版服务器。导入会报告它拒绝的每个名称，但仍会导入您选择的其他服务器。在 v2.1.205 之前，第一个无效名称会停止导入，并且不会添加任何选定的服务器。



**提示：**

温馨提示：

  * 此功能仅适用于 macOS 和适用于 Linux 的 Windows 子系统 (WSL)
  * 它从这些平台上的标准位置读取 Claude 桌面配置文件
  * 使用 `--scope user` 标志将服务器添加到您的用户配置中
  * 当名称仅包含字母、数字、连字符和下划线时，导入的服务器将保留与 Claude 桌面中相同的名称。 Claude Code 报告名称包含任何其他字符的服务器并跳过它
  * 如果已存在具有相同名称的服务器，它们将获得数字后缀（例如，`server_1`）



## 使用 claude.ai 的 MCP 服务器

如果您使用 [claude.ai](<https://claude.ai>) 帐户登录 Claude Code，则您在 claude.ai 中添加的 MCP 服务器（称为 [连接器 ](<https://claude.com/docs/connectors>)）会自动在Claude Code：



  

#### 在 claude.ai 中配置 MCP 服务器

在 [claude.ai/customize/connectors](<https://claude.ai/customize/connectors>) 添加服务器。在团队和企业计划中，只有管理员可以添加服务器。
  


  

#### 验证MCP服务器

在 claude.ai 中完成任何所需的身份验证步骤。
  


  

#### 查看和管理Claude Code中的服务器

在Claude Code中，使用命令：

    ```text wrap theme={null}
    /mcp
    ```

来自 claude.ai 的服务器出现在列表中，并带有指示符显示它们来自 claude.ai。
  


Anthropic 本身还提供了一些连接器，无需您或管理员添加它们。在 [Claude 文档 ](<https://code.claude.com/docs/en/artifacts#write-a-document-with-claude-docs>) 可用的帐户上，`/mcp` 会在没有设置的情况下列出 `claude.ai Claude Docs`，并且 Claude 在您请求供其他人使用的文档时使用它。要关闭它，请将 `"claude.ai Claude Docs"` 的 `serverName` 条目添加到 `deniedMcpServers` 或使用 `/mcp` 切换开关，两者均在 [禁用 claude.ai 连接器 ](<#disable-claude-ai-connectors>) 中进行了描述。

当您的组织在 claude.ai 中管理其身份验证时，Claude Code 在 `/mcp` 和 [`/plugin`](<https://code.claude.com/docs/en/plugins/install>) 管理器中标记连接器 `managed`。托管状态不会更改 Claude Code 连接到连接器的方式或应用组织的 [ 工具控件 ](<#连接器工具的组织控制>)。

您从未登录过的连接器会折叠到 claude.ai 部分末尾的 `Show unused connectors` 行后面，因此组织配置的列表不会填充面板。选择行以展开它们。即使当前需要重新身份验证，您之前登录的连接器仍然可见。

仅当您的活动 [ 身份验证方法 ](<https://code.claude.com/docs/en/authentication#authentication-precedence>) 是 claude.ai 订阅登录名时，才会从 claude.ai 获取连接器。即使您之前运行过 `/login`，在以下情况下它们也不会加载：

* `ANTHROPIC_API_KEY`、`ANTHROPIC_AUTH_TOKEN` 或 `apiKeyHelper` 处于活动状态
* 第三方提供商（例如 Amazon Bedrock 或 Google Cloud 的 Agent 平台）处于活动状态
* `ANTHROPIC_PROFILE`、联合变量或活动的 [Anthropic 配置文件 ](<https://code.claude.com/docs/en/authentication#anthropic-profiles-and-federation-credentials>) 提供凭据
* `CLAUDE_CODE_OAUTH_TOKEN`持有[`claude setup-token`](<https://code.claude.com/docs/en/authentication#generate-a-long-lived-token>)的token，只能发出模型请求

如果 `/mcp` 未列出您添加的连接器，请运行 `/status` 以确认哪种身份验证方法处于活动状态。取消设置该环境变量，删除 `apiKeyHelper` 设置，或 [ 关闭配置文件 ](<https://code.claude.com/docs/en/authentication#anthropic-profiles-and-federation-credentials>)，然后运行 ​​`/login` 以选择您的 claude.ai 帐户。

如果临时网络问题导致会话启动时无法加载连接器列表，Claude Code 会在后台重试提取操作最多 3 次，并且在重试成功后会显示连接器。如果仍未出现，请重新启动Claude Code以再次获取列表。

如果 `/mcp` 将连接器显示为 `session token rejected`，或其详细视图显示 [`claude.ai rejected the session token`](<https://code.claude.com/docs/en/errors#claude-ai-rejected-the-session-token>)，则 claude.ai 拒绝了您的 Claude Code 登录的令牌。再次授权连接器不会清除此状态，因为连接器自己在 claude.ai 中的授权并不是被拒绝的授权。要清除它：

1. 运行`/login`重新登录。
2. 重新连接 `/mcp` 的连接器。

在 v2.1.222 之前，Claude Code 将连接器标记为需要身份验证，并且对它们进行授权并不能解决问题。

您在 Claude Code 中添加的服务器比指向同一 URL 的 claude.ai 连接器具有 [ 优先级 ](<#范围层次结构和优先级>)。发生这种情况时，`/mcp` 会将连接器列为隐藏连接器，并显示如何删除重复项（如果您希望使用该连接器）。

某些 Anthropic 托管的连接器，例如 Microsoft、365、Gmail 和 Google 日历，不支持来自 Claude Code 的本地 OAuth，因为上游身份提供商仅接受 claude.ai 注册的重定向 URL。当您使用 `claude mcp add` 或 `.mcp.json` 添加的服务器指向这些主机之一，并且您从 `/mcp` 或使用 `claude mcp login` 登录到该服务器时，Claude Code 显示 [`is Anthropic-hosted and doesn't support local OAuth`](<https://code.claude.com/docs/en/errors#anthropic-hosted-and-doesnt-support-local-oauth>)，指示您连接以下地址的服务：改为[claude.ai/customize/connectors](<https://claude.ai/customize/connectors>)。

当您删除`claude mcp remove <name>`条目并连接claude.ai上的服务后，连接器会自动出现在Claude Code中。

### 连接器如何达到Claude Code

哪些设置管理 claude.ai 连接器取决于您的会话运行位置，因为只有某些会话从 claude.ai 本身获取连接器。下面的每一行都列出了连接器如何到达一种会话以及在那里控制它们的因素。桌面应用程序的 [WSL 会话 ](<https://code.claude.com/docs/en/desktop-wsl#what-works-in-a-wsl-session>) 没有行，因为连接器在其中尚不可用。

|会话运行的位置|连接器如何到达|是什么支配着他们|
| :- | :- | :- |
|终端、[VS Code](<https://code.claude.com/docs/en/vs-code>)、[JetBrains](<https://code.claude.com/docs/en/jetbrains>) 和 [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/claude-code-features>) 会话|Claude Code 从 claude.ai 获取它们|本节中的设置和[托管MCP配置](<https://code.claude.com/docs/en/managed-mcp>)|
|[云会话](<https://code.claude.com/docs/en/claude-code-on-the-web>)|云主机传入|您的 claude.ai 组织设置，加上到达会话的 [ 允许列表和拒绝列表 ](<https://code.claude.com/docs/en/managed-mcp#policy-based-control-with-allowlists-and-denylists>) 设置以及运行该会话的主机上的任何 `managed-mcp.json`|
|[桌面应用程序](<https://code.claude.com/docs/en/desktop>)的本地和SSH会话|桌面应用程序在进程中交付它们|您组织的 [ 连接器工具控件中的 `blocked` 条目 ](<#连接器工具的组织控制>)|

[`disableClaudeAiConnectors`](<#disable-claude-ai-connectors>)、`ENABLE_CLAUDEAI_MCP_SERVERS` 和 [`allowAllClaudeAiMcps`](<https://code.claude.com/docs/en/settings-reference#allowallclaudeaimcps>) 仅作用于第一行，连接器 Claude Code 获取自身。其他两行在以下方面与它不同：

* **云会话**：到达会话的`allowedMcpServers`和`deniedMcpServers`条目（例如通过[服务器管理的设置](<https://code.claude.com/docs/en/server-managed-settings>)）也会过滤提供的连接器。会话的代理重写每个连接器的 URL，因此为连接器自己的 URL 编写的 `serverUrl` 模式与其不匹配。要在自托管环境中接纳已交付的连接器以及 URL 允许列表，请添加 [连接器流量离开您的网络 ](<https://code.claude.com/docs/en/self-hosted-environments-deploy#connector-traffic-leaves-your-network>) 下列出的 `serverUrl` 条目。当运行会话的主机（例如 [ 自托管运行器主机 ](<https://code.claude.com/docs/en/self-hosted-environments-configuration#mcp-servers>)）上存在 `managed-mcp.json` 时，Claude Code 会删除传递的连接器，无论您是否设置 `allowAllClaudeAiMcps`。
* **桌面应用程序本地和 SSH 会议**：桌面应用程序将连接器注册为进程内 `type: "sdk"` 服务器，并且没有 MCP 设置或 `managed-mcp.json` 到达他们。用户通过在以下位置断开连接器来使连接器脱离自己的会话： [claude.ai/customize/connectors](<https://claude.ai/customize/connectors>)。组织阻止连接器 [工具](<#连接器工具的组织控制>) 或关闭 [Claude Code 在桌面应用程序中](<https://code.claude.com/docs/en/desktop#admin-console-controls>) 完全。

### 连接器工具的组织控制

您的组织可以在 [claude.ai 连接器 ](<https://claude.com/docs/connectors>) 上设置每个工具的控件。 Claude Code 在启动时读取这些设置并在本地强制执行它们，但桌面应用程序的 [ 本地和 SSH 会话 ](<#连接器如何达到claude-code>) 中除外。在那里，桌面应用程序在提供连接器之前会保留 `blocked` 工具，并且 `ask` 设置未达到 Claude Code，因此它将会话的普通 [ 权限规则 ](<https://code.claude.com/docs/en/permissions>) 应用于这些工具，而不是在每次调用时进行提示。在 Claude Code 获取连接器本身的会话中，运行 `/mcp` 以查看哪个设置适用于连接器上的每个工具。

* **工具设置为`ask`**：Claude Code每次通话时都会提示原因`Your organization requires approval for this tool`。即使在 `acceptEdits`、`auto` 和 `bypassPermissions` [ 权限模式 ](<https://code.claude.com/docs/en/permissions#permission-modes>) 中也会出现提示，并且从不提供记住您的选择的选项。 [允许与该工具匹配的规则](<https://code.claude.com/docs/en/permissions>)也不会跳过提示。在 `dontAsk` 模式下，从不提示，Claude Code 会拒绝呼叫。
* **设置为 `blocked`** 的工具：Claude Code 在 Claude 看到该工具之前将其过滤掉，因此它永远不会出现在 Claude 的工具列表中。在 Claude Code 获取连接器本身的会话中，`/mcp` 工具列表仍显示标记为 `disabled by your organization` 的工具。

桌面应用程序和 claude.ai 聊天应用相同的 `blocked` 设置，因此 Claude 也无法在其中使用被阻止的工具，并且您无法在保持工具在聊天中可用的同时从桌面应用程序的会话中保留该工具。桌面应用程序会跳过工具全部被阻止的连接器。

### 禁用 claude.ai 连接器

Claude Code 仅将 [`disableClaudeAiConnectors`](<https://code.claude.com/docs/en/settings-reference#disableclaudeaiconnectors>) 应用到它 [ 自身获取的连接器 ](<#连接器如何达到claude-code>)，而不应用到云主机或桌面应用程序提供的连接器。要关闭它获取的连接器，请在任何设置范围中将设置设置为 `true`：

```json theme={null}
{
  "disableClaudeAiConnectors": true
}
```

此设置使用 any-source-true 语义：任何设置中的 `true` 源优先。签入的项目 `.claude/settings.json` 可以从连接器 Claude Code 获取自身中选择存储库，但项目级 `false` 无法重新启用用户级或策略级 `true` 已禁用的连接器。通过`--mcp-config`显式传递的服务器不受影响。

还可以将 `ENABLE_CLAUDEAI_MCP_SERVERS` 环境变量设置为 `false`，这对于当前 shell 会话具有相同的效果：

```bash theme={null}
ENABLE_CLAUDEAI_MCP_SERVERS=false claude
```

要阻止单个 claude.ai 连接器而不是全部连接器，请按名称或按 URL 模式将它们添加到 [`deniedMcpServers`](<https://code.claude.com/docs/en/managed-mcp>)。例如，`"claude.ai Slack"` 的 `serverName` 条目会阻止 Slack 连接器。您还可以运行 `/mcp` 来打开或关闭仅针对当前项目的任何连接器 Claude Code 提取。

## 使用Claude Code作为MCP服务器

您可以使用 Claude Code 本身作为其他应用程序可以连接到的 MCP 服务器：

```bash theme={null}
# Start Claude as a stdio MCP server
claude mcp serve
```

该命令启动时不打印任何内容。 stdio MCP 服务器通过 stdin 和 stdout 进行通信，因此静默、阻塞的终端意味着服务器正在运行并等待客户端连接。

您可以通过将此配置添加到 claude\_desktop\_config.json 来在 Claude 桌面中使用此功能：

```json theme={null}
{
  "mcpServers": {
    "claude-code": {
      "type": "stdio",
      "command": "claude",
      "args": ["mcp", "serve"],
      "env": {}
    }
  }
}
```



**注意：**

**配置可执行文件路径**：`command`字段必须引用Claude Code可执行文件。如果 `claude` 命令不在系统的 PATH 中，则需要指定可执行文件的完整路径。

要查找完整路径：

  ```bash theme={null}
  which claude
  ```

然后在配置中使用完整路径：

  ```json theme={null}
  {
    "mcpServers": {
      "claude-code": {
        "type": "stdio",
        "command": "/full/path/to/claude",
        "args": ["mcp", "serve"],
        "env": {}
      }
    }
  }
  ```

如果没有正确的可执行路径，您将遇到类似 `spawn claude ENOENT` 的错误。




**提示：**

温馨提示：

  * 在 Claude 桌面中，尝试要求 Claude 读取目录中的文件、进行编辑等。
  * 此 MCP 服务器仅向您的 MCP 客户端公开 Claude Code 的工具，因此您自己的客户端负责实现各个工具调用的用户确认。



## MCP 输出限制和警告

当 MCP 工具产生大量输出时，Claude Code 帮助管理令牌的使用，以防止压倒您的对话上下文：

* **输出警告阈值**：当任何MCP工具输出超过10,000标记时，Claude Code显示警告
* **可配置限制**：您可以使用`MAX_MCP_OUTPUT_TOKENS`环境变量调整允许的最大MCP输出令牌
* **默认限制**：默认最大值为25,000个代币
* **范围**：环境变量适用于未声明自身限制的工具。设置 [`anthropic/maxResultSizeChars`](<#提高特定工具的限制>) 的工具使用该值代替文本内容，无论 `MAX_MCP_OUTPUT_TOKENS` 设置为什么。返回图像数据的工具仍受 `MAX_MCP_OUTPUT_TOKENS` 的约束
* **超出限制**：当没有图像内容的成功结果超出令牌限制时，Claude Code将其保存到文件中，并在对话中将其替换为命名文件路径的消息，因此Claude在需要内容时读取文件。该文件位于会话的 [`~/.claude/projects/`](<https://code.claude.com/docs/en/claude-directory#cleaned-up-automatically>) 下的 `tool-results` 目录中。
* **响应大小来自 HTTP 和 SSE 服务器**: Claude Code 停止读取响应 [HTTP](<#选项1添加远程http服务器>) 或 [SSE](<#选项2添加远程sse服务器>) 服务器一次一次 JSON 响应正文或事件流的一个事件通过 16 MB 解压后。响应应答的请求失败。如果您维护服务器，请减少每个响应返回的数据以保持在限制以下，例如通过对结果进行分页

Claude Code已[移动到后台任务](<#长工具调用的自动后台处理>)的调用通过任务通知报告其结果。还有两个限制适用于在前台完成的调用：

* **文本结果的字符限制**：对于未声明[`anthropic/maxResultSizeChars`](<#提高特定工具的限制>)的工具，一旦长度超过50,000个字符，Claude Code就会将没有图像内容的成功结果保存到文件中，无论其令牌数量如何。设置 `MAX_MCP_OUTPUT_TOKENS` 不会更改此阈值
* **错误结果**：当工具返回标记为`isError: true`的结果时，Claude接收结果的文本作为工具的错误消息。长度超过 11,000 个字符的错误文本仅保留其第一个 5,000 和最后一个 5,000 个字符，它们之间有一个标记，指示删除了多少个字符

要更改令牌限制，请在启动 Claude Code 之前在 shell 中设置 `MAX_MCP_OUTPUT_TOKENS`：

```bash theme={null}
export MAX_MCP_OUTPUT_TOKENS=50000
claude
```

### 提高特定工具的限制

如果您正在构建 MCP 服务器，则可以通过在工具的 `tools/list` 响应条目中设置 `_meta["anthropic/maxResultSizeChars"]` 来允许各个工具返回大于 50,000 个字符的默认持久到磁盘阈值的结果。 Claude Code 将该工具的阈值提高到注释值，最高可达 500,000 个字符的硬上限。

这对于返回本身较大但必要的输出（例如数据库模式或完整文件树）的工具非常有用。如果没有注释，超过默认阈值的成功结果将保留到磁盘并替换为对话中的文件引用。

```json theme={null}
{
  "name": "get_schema",
  "description": "Returns the full database schema",
  "_meta": {
    "anthropic/maxResultSizeChars": 200000
  }
}
```

该注释独立于文本内容的 `MAX_MCP_OUTPUT_TOKENS` 应用，因此用户无需为声明它的工具提高环境变量。返回图像数据的工具仍然受到令牌限制。

### 工具结果中的图像

当 MCP 工具返回 PNG、JPEG、GIF 或 WebP 图像时，Claude 会在对话中看到内联图像。内嵌副本可以按比例缩小或压缩以适应模型的图像大小限制。 Claude Code 还将原始字节保存到会话的 `tool-results` 目录中 [`~/.claude/projects/`](<https://code.claude.com/docs/en/claude-directory#cleaned-up-automatically>) 下的文件中，并给出 Claude 路径。然后，Claude 可以使用 Bash 等工具裁剪、转换或重复使用全分辨率文件。

如果使用 [`--no-session-persistence`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 或 [`CLAUDE_CODE_SKIP_PROMPT_HISTORY`](<https://code.claude.com/docs/en/env-vars>) 禁用会话持久性，Claude Code 不会写入任何图像文件，并且 Claude 仅接收内联副本。

将 MCP 图像结果保存到文件需要 Claude Code v2.1.283 或更高版本。

## 具有根级组合器的工具输入模式

某些 MCP 服务器将工具的输入架构声明为 JSON 架构联合，其中 `anyOf`、`oneOf` 或 `allOf` 位于架构的顶层。 Claude API 不接受架构根处的这些关键字。它确实接受嵌套在 `properties` 内的组合器，Claude Code 发送不变。

具有根级组合器的工具仍然可用。在将工具发送到 API 之前，Claude Code 将架构扁平化为单个对象，并在工具的描述前面添加一个句子，告诉 Claude 哪些参数组属于一起：

* `allOf`：合并每个分支的属性，并且每个分支的 `required` 列表仍然适用
* `anyOf` 和 `oneOf`：合并每个分支的属性，并且每个分支的 `required` 列表在工具描述中进行描述，而不是由架构强制执行

您的服务器接收 Claude 选择的任何参数，因此请继续验证服务器端的组合。

当 Claude Code 无法生成 API 接受的架构时，或者在未收到启用重写的远程配置的部署中，它会跳过该工具，将原因记录在服务器日志中，并让服务器的其他工具可用。

## 输入模式无效的工具

Claude API 检查请求中每个工具的输入架构，并在任何一个架构失败时拒绝整个请求，因此具有格式错误架构的单个 MCP 工具将使包含它的每个请求失败并出现 400 错误。 Claude Code 在加载服务器工具时运行两个 API 的自我检查，并排除会使它们失败的每个工具，因此服务器的其他工具继续工作：

* 顶级属性名称的长度必须为 1 到 64 个字符，并且只能使用 ASCII 个字母和数字、`_`、`.` 和 `-`
* 该架构必须针对 JSON 架构草案 2020-12 元架构有效。 Claude Code 将此检查应用于声明无 `$schema` 的架构和声明草稿 2020-12 的架构。声明任何其他方言的模式会跳过此检查，但上面的属性名称检查仍然适用

当Claude Code排除某个工具时，它会在服务器日志中记录原因，并告诉Claude它排除了哪些工具以及原因，因此您可以询问Claude为什么缺少工具。如果您修复服务器上的架构，该工具会在下次 Claude Code 加载服务器工具时返回。

Claude Code 通过从 Anthropic 获取的功能标志打开排除。在标记获取关闭的 [ 部署中 ](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>)，或者在标记从未到达的机器上（例如气隙机器），Claude Code 仍会在服务器日志中运行检查并记录哪个工具将被拒绝，但无论如何都会将该工具的架构发送到 API。 API 拒绝包含该架构的请求，并且 [ 出现 400 错误，通过位置 ](<https://code.claude.com/docs/en/errors#tool-input-schema-is-invalid>) 命名该工具。在 v2.1.216 之前，没有部署运行这些检查。

[根级组合器处理](<#具有根级组合器的工具输入模式>)是独立的，并且当标志获取关闭或标志从未到达时保持其自己的行为。

## 需要批准特定工具

如果您正在构建 MCP 服务器，则可以通过在工具的 `tools/list` 响应条目中将 `_meta["anthropic/requiresUserInteraction"]` 设置为 `true`，将工具标记为每次调用都需要显式批准。该值必须是JSON布尔值`true`；任何其他值都将被忽略。

Claude Code 在每次调用时都会显示该工具的权限提示，即使在 `acceptEdits`、`auto` 和 `bypassPermissions` [ 权限模式 ](<https://code.claude.com/docs/en/permissions#permission-modes>) 中也是如此，并且不提供“不再询问”选项。 [允许与该工具匹配的规则](<https://code.claude.com/docs/en/permissions#permission-rule-syntax>)也不会跳过提示。在 `dontAsk` 模式下，从不提示，Claude Code 会拒绝呼叫。

提示必须传达给一个人。在 [`--permission-prompt-tool`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 的非交互模式下，标记工具的提示工具的 `allow` 结果将转换为带有消息 `MCP tool requires user interaction; not supported via --permission-prompt-tool` 的拒绝。 Agent SDK 的 [`canUseTool` 回调 ](<https://code.claude.com/docs/en/agent-sdk/permissions>) 确实收到这些调用并可以批准它们，因为您的 SDK 应用程序预计会将它们显示给用户。

将此用于权限提示本身就是重点的工具，例如同意或访问授予步骤，其中自动批准意味着没有人同意。来自同一服务器的其他工具保持其正常的权限行为。

以下 `tools/list` 条目将一种工具标记为始终需要批准。

```json theme={null}
{
  "name": "grant_access",
  "description": "Requests access to a protected resource",
  "_meta": {
    "anthropic/requiresUserInteraction": true
  }
}
```

某些界面，例如 [远程控制 ](<https://code.claude.com/docs/en/remote-control>) 和基于 [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>) 构建的应用程序，通常允许您一键批准工具调用。对于标有此注释的工具，Claude Code 会保留一键操作并显示该工具的完整权限提示，因此批准仍然来自于回答提示的人而不是点击。

Claude Code 对于只有终端对话框可以完整呈现的任何权限请求（例如带有安全警告或远程表面无法显示的始终允许选项的请求），以相同的方式拒绝一键批准。您可以在终端对话框中而不是从远程控制中回答该请求。需要 Claude Code v2.1.214 或更高版本。

## 回复 MCP 启发请求

MCP 服务器可以使用启发向您请求中间任务的结构化输入。当服务器需要它自己无法获取的信息时，Claude Code 会显示一个交互式对话框并将您的响应传递回服务器。您无需进行任何配置：当服务器请求时，会自动出现诱导对话框。

服务器可以通过两种方式请求输入：

* **表单模式**：Claude Code显示一个对话框，其中包含服务器定义的表单字段（例如，用户名和密码提示）。填写字段并提交。
* **URL模式**：Claude Code询问是否在浏览器中打开链接。服务器将此模式用于在终端外部完成的流程，例如登录。

在 URL 模式下，Claude Code 将 URL 作为命令行参数传递给系统的 URL 处理程序，并限制该参数的长度。当 URL 在命令行转义后超过该上限时，您只能拒绝该请求。每个需要转义的字符（例如 `%` 或 `&`）在上限中计数四次：其自身字符加上三个转义字符。一个 URL，其中没有一个达到大约 8,000 个字符的上限。一个主要由百分比转义构成的 URL，其中每三个字符是一个 `%`，大约在 4,000 处达到它。

要自动响应诱导请求而不显示对话框，请使用 [`Elicitation` 挂钩 ](<https://code.claude.com/docs/en/hooks#elicitation>)。

如果您正在构建使用诱导的 MCP 服务器，请参阅 [MCP 诱导规范 ](<https://modelcontextprotocol.io/docs/learn/client-concepts#elicitation>) 以获取协议详细信息和架构示例。

在使用 [ 协议修订版 2026-07-28](<#mcp-客户端运行时>) 的连接上，Claude Code 在其客户端功能中声明 `elicitation: {form: {}, url: {}}`，因此服务器可以通过协议的标准引出请求请求任一模式。

## 使用MCP资源

MCP 服务器可以公开您可以使用@提及引用的资源，类似于引用文件的方式。

### 参考【MCP】资源



  

#### 列出可用资源

在提示中键入 `@` 以查看所有连接的 MCP 服务器的可用资源。资源与文件一起显示在自动完成菜单中。
  


  

#### 参考特定资源

使用格式 `@server:protocol://resource/path` 来引用资源：

    ```text wrap theme={null}
    Can you analyze @github:issue://123 and suggest a fix?
    ```

    ```text wrap theme={null}
    Please review the API documentation at @docs:file://api/authentication
    ```
  


  

#### 多种资源参考

您可以在单个提示中引用多个资源：

    ```text wrap theme={null}
    Compare @postgres:schema://users with @docs:file://database/user-model
    ```
  




**提示：**

温馨提示：

  * 引用时会自动获取资源并作为附件包含在内
  * 资源路径在@提及自动完成中可模糊搜索
  * 当服务器支持时，Claude Code自动提供列出和读取MCP资源的工具
  * 资源可以包含MCP服务器提供的任何类型的内容（文本、JSON、结构化数据等）



MCP 应用程序 UI 资源是具有 `ui://` URI 或 `text/html;profile=mcp-app` 媒体类型的条目：供主机应用程序呈现的页面，而不是供 Claude 读取的内容。它们不会出现在 `@` 建议或资源列表工具的结果中，并且仅提供 UI 资源的服务器显示空资源列表。通过 URI 读取 UI 资源仍然有效。

## 使用 MCP 工具搜索进行缩放

工具搜索通过推迟工具定义直到 Claude 需要它们来保持 MCP 上下文使用率较低。在会话启动时仅加载工具名称和服务器指令，因此添加更多 MCP 服务器对上下文窗口的影响最小。 Claude Code 不强制规定固定的每服务器工具上限；实际限制是您的上下文窗口预算。



**注释：**

  不支持工具搜索 Microsoft 铸造厂 [Azure 上托管的部署](<https://platform.claude.com/docs/en/build-with-claude/claude-in-microsoft-foundry#hosting-options>)，它在服务器端拒绝它： Claude Code 检测拒绝和负载 MCP 而是为该部署预先提供工具。 [`ENABLE_TOOL_SEARCH`](<#配置工具搜索>) 无法覆盖这一点，因为拒绝来自部署本身。


<span id="for-mcp-server-authors" />

### MCP 服务器作者的工具搜索

如果您正在构建 MCP 服务器，则启用工具搜索后，服务器说明字段将变得更加有用。服务器指令帮助Claude了解何时搜索您的工具，类似于[技能](<https://code.claude.com/docs/en/skills>)的工作方式。

添加清晰的、描述性的服务器指令来解释：

* 您的工具处理什么类别的任务
* 当Claude应该搜索你的工具时
* 您的服务器提供的关键功能

默认情况下，Claude Code 将每个工具描述和每个服务器的指令截断为 2,048 个字符。保持简洁，并将关键细节放在开头附近。

要更改会话中每个 MCP 服务器的限制，请将 [`CLAUDE_CODE_MAX_MCP_DESCRIPTION_LENGTH`](<https://code.claude.com/docs/en/env-vars#variables>) 设置为字符数。此变量需要 Claude Code v2.1.280 或更高版本。

<h4 id="per-tool-alwaysload">
 将工具标记为预先加载或保持延迟
</h4>

要控制服务器工具之一的加载方式，请在该工具的 `_meta` 对象中设置 `"anthropic/alwaysLoad"`。将您的服务器添加到 Claude Code 的人还可以在其配置中为整个服务器设置 [`alwaysLoad`](<#免除服务器的延迟>)，并且他们的设置可以覆盖您的设置：

|您的工具的价值|会发生什么|
| :- | :- |
| `true` |该工具预先加载。由于此值，启动不会等待您的服务器。配置您服务器的人仍然可以[推迟其所有工具](<#defer-a-servers-tools>)|
| `false` |当其配置设置为 `"alwaysLoad": true` 时，该工具将保持延迟状态。当您的服务器通过 [`--mcp-config`](<https://code.claude.com/docs/en/cli-reference#cli-flags>)、由 [Agent SDK 应用程序 ](<https://code.claude.com/docs/en/agent-sdk/mcp#in-code>) 提供或由 [ 插件 ](<#插件提供的mcp服务器>) 提供时，这适用。在其他服务器上，该工具会预先加载。需要 Claude Code v2.1.285 或更高版本|

以下 `tools/list` 条目要求预先加载一个工具：

```json theme={null}
{
  "name": "search_tickets",
  "description": "Searches the ticket tracker by keyword",
  "_meta": {
    "anthropic/alwaysLoad": true
  }
}
```

### 配置工具搜索

默认情况下启用工具搜索：MCP 工具会延迟并按需发现。当 `ANTHROPIC_BASE_URL` 指向非第一方主机时，Claude Code 会禁用它，因为大多数代理不会转发 `tool_reference` 块。显式设置 `ENABLE_TOOL_SEARCH` 以覆盖该回退。

设置 [`CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS`](<https://code.claude.com/docs/en/env-vars>) 会关闭刀具搜索。您无法通过自行设置 `ENABLE_TOOL_SEARCH` 来覆盖它。您的组织可以通过 [ 托管设置 ](<https://code.claude.com/docs/en/managed-settings>)、Claude Code v2.1.227 或更高版本继续进行工具搜索。 [禁用预发布功能](<https://code.claude.com/docs/en/llm-gateway-protocol#disable-pre-release-capabilities>)涵盖了覆盖应用的位置以及变量剥离的内容。

工具搜索需要一个支持的模型 `tool_reference` 块： Claude 十四行诗 4.5, Claude 俳句 4.5, Claude 作品 4.5，以及后来的型号。参见 [模型兼容性 API 文档](<https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool#model-compatibility>) 对于当前列表。

在Google云的Agent平台上，Claude Code由模型生成决定：

* **Claude Opus 4.5、Sonnet 4.5、Haiku 4.5 和更高版本的 **：工具搜索默认打开，与 Anthropic API 相同。
* **早期Agent平台模型**：Claude Code预先加载所有MCP工具，因为它们的服务堆栈拒绝所需的测试标头。 `ENABLE_TOOL_SEARCH=true` 不会覆盖这一点。

在v2.1.221之前，Claude Code禁用工具搜索Google云的Agent平台上的所有型号，除非您设置`ENABLE_TOOL_SEARCH=true`。

使用 `ENABLE_TOOL_SEARCH` 环境变量控制工具搜索行为：

|价值|行为|
| :- | :- |
|（未设置）|所有 MCP 工具均延迟并按需加载。当 `ANTHROPIC_BASE_URL` 是非第一方主机时，或者在 Azure 上托管的 Microsoft Foundry 部署上时，会回退到早于 Claude 4.5 代的 Google 云 Agent 平台模型上预先加载|
| `true` |所有 MCP 工具均被推迟，但在 Azure 上托管的 Microsoft Foundry 部署上（其中服务器端拒绝仍然强制预先加载）以及早于 Claude 4.5 一代的 Google 云 Agent 平台模型上除外，其中Claude Code 保持预先加载工具。 Claude Code 通过代理发送 beta 标头，并且请求在不支持 `tool_reference` 块的代理上失败|
| `auto` |阈值模式：Claude Code 加载原本会在其定义总数小于上下文窗口的 10% 时提前推迟的工具，并在定义达到 10% 后推迟所有工具|
| `auto:N` |具有自定义百分比的阈值模式，其中 `N` 是 0-100。例如，`auto:5` 代表 5%|
| `false` |所有 MCP 工具均预先加载，无延迟|

```bash theme={null}
# Use a custom 5% threshold
ENABLE_TOOL_SEARCH=auto:5 claude

# Disable tool search entirely
ENABLE_TOOL_SEARCH=false claude
```

或者在 [settings.json `env` 字段 ](<https://code.claude.com/docs/en/settings-reference#env>) 中设置值。

您还可以专门禁用`ToolSearch`工具：

```json theme={null}
{
  "permissions": {
    "deny": ["ToolSearch"]
  }
}
```

### 免除服务器的延迟

如果服务器的工具应始终对 Claude 可见，而无需搜索步骤，请在该服务器的配置中将 `alwaysLoad` 设置为 `true`。然后，无论 `ENABLE_TOOL_SEARCH` 设置如何，服务器的工具都会加载到上下文中。将此用于 Claude 每次都需要的少量工具，因为每个前期工具都会消耗可用于您的对话的上下文。

以下 `.mcp.json` 条目免除一台 HTTP 服务器，同时保留其他服务器延迟：

```json theme={null}
{
  "mcpServers": {
    "core-tools": {
      "type": "http",
      "url": "https://mcp.example.com/mcp",
      "alwaysLoad": true
    }
  }
}
```

`alwaysLoad` 字段在所有服务器类型上均可用。

设置 `alwaysLoad: true` 还会使启动等待服务器工具，上限为标准 5 秒连接超时，因为在构建第一个提示时它们必须存在。具有有效 [`cached` 条目 ](<#服务器状态详细信息>) 的远程服务器无需连接即可从缓存中提供其工具，因此它不会保持启动。其他服务器默认在后台连接；设置[`MCP_CONNECTION_NONBLOCKING=0`](<https://code.claude.com/docs/en/env-vars>)以使启动也等待它们。

<h3 id="defer-a-servers-tools">
 延迟服务器工具
</h3>

要将服务器的所有工具保留在工具搜索后面，请在 MCP 配置中的该服务器条目中设置 `"alwaysLoad": false`。其中包括服务器作者[标记为预先加载的工具](<#per-tool-alwaysload>)。如果您将 `alwaysLoad` 排除在外，那些标记的工具会预先加载。需要 Claude Code v2.1.287 或更高版本。

## 使用 MCP 提示符作为命令

MCP 服务器可以公开可作为 Claude Code 中的命令使用的提示。

来自名为 `anthropic-skills` 的服务器的提示不会出现，因为 Claude Code [ 为从 claude.ai 同步的技能保留了该名称 ](<https://code.claude.com/docs/en/skills#names-reserved-for-synced-skills>)。服务器的工具仍然有效。重命名 MCP 配置中的服务器以列​​出其提示。

### 执行【MCP】提示



  

#### 发现可用的提示

输入 `/` 查看可用的命令，包括来自 MCP 服务器的命令。 Claude Code 将每个 MCP 提示符列为 `/servername:promptname (MCP)`。输入 `/mcp__servername__promptname` 也会运行它。
  


  

#### 执行不带参数的提示

    ```text wrap theme={null}
    /mcp__github__list_prs
    ```
  


  

#### 执行带有参数的提示

许多提示接受参数。在命令后通过空格分隔它们。 Claude Code 将参数分割为空格，因此每个参数都是一个标记：

    ```text wrap theme={null}
    /mcp__github__pr_review 456
    ```

    ```text wrap theme={null}
    /mcp__jira__create_issue login-bug high
    ```
  




**提示：**

温馨提示：

  * MCP 提示是从连接的服务器动态发现的
  * 根据提示定义的参数来解析参数
  * 提示结果直接注入对话中
  * 在 `/mcp__servername__promptname` 形式中，Claude Code 将服务器名称中 `A-Z`、`a-z`、`0-9`、`_` 和 `-` 之外的任何字符替换为 `_`，并且使用服务器声明的提示名称



## 托管 MCP 配置

对于需要集中控制用户可以连接到的 MCP 服务器的组织，请参阅[托管 MCP 配置 ](<https://code.claude.com/docs/en/managed-mcp>)。它包括使用`managed-mcp.json`部署固定服务器集，使用`managedMcpServers`向每个用户提供服务器，使用`allowedMcpServers`和`deniedMcpServers`限制服务器，以及当服务器被阻止时用户看到的内容。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connect Claude Code to tools via MCP

> Learn how to connect Claude Code to your tools with the Model Context Protocol.

Claude Code can connect to hundreds of external tools and data sources through the [Model Context Protocol (MCP)](<https://modelcontextprotocol.io/introduction>), an open source standard for AI-tool integrations. MCP servers give Claude Code access to your tools, databases, and APIs.

Connect a server when you find yourself copying data into chat from another tool, like an issue tracker or a monitoring dashboard. Once connected, Claude can read and act on that system directly instead of working from what you paste.

If you're connecting your first server, start with the [MCP quickstart](<https://code.claude.com/docs/en/mcp-quickstart>) for a step-by-step walkthrough. This page is the full reference.

## What you can do with MCP

With MCP servers connected, you can ask Claude Code to:

* **Implement features from issue trackers**: "Add the feature described in JIRA issue ENG-4521 and create a PR on GitHub."
* **Analyze monitoring data**: "Check Sentry and Statsig to check the usage of the feature described in ENG-4521."
* **Query databases**: "Find emails of 10 random users who used feature ENG-4521, based on our PostgreSQL database."
* **Integrate designs**: "Update our standard email template based on the new Figma designs that were posted in Slack"
* **Automate workflows**: "Create Gmail drafts inviting these 10 users to a feedback session about the new feature."
* **React to external events**: an MCP server can also act as a [channel](<https://code.claude.com/docs/en/channels>) that pushes messages into your session, so Claude reacts to Telegram messages, Discord chats, or webhook events while you're away.

## Find and build MCP servers

Browse reviewed connectors in the [Anthropic Directory](<https://claude.ai/directory>). Directory connectors use the same MCP infrastructure as Claude Code, so you can add any remote server listed there with `claude mcp add`.



**注意：**

  Verify you trust each server before connecting it. Servers that fetch external content can expose you to [prompt injection risk](<https://code.claude.com/docs/en/security#protect-against-prompt-injection>).


To build your own server, see the [MCP server guide](<https://modelcontextprotocol.io/docs/develop/build-server>) for protocol fundamentals and the [Claude connector building docs](<https://claude.com/docs/connectors/building>) for authentication, testing, and Directory submission.

You can also have Claude scaffold a server for you with the official [`mcp-server-dev` plugin](<https://github.com/anthropics/claude-plugins-official/tree/main/plugins/mcp-server-dev>).



  

#### Install the plugin

    In the VS Code extension or the desktop app, follow [Install a plugin](<https://code.claude.com/docs/en/plugins/install#install-a-plugin>) instead of this step. In a terminal, start Claude Code by running `claude`, then enter this at its prompt:

    ```
    /plugin install mcp-server-dev@claude-plugins-official
    ```

    If the install fails, match the message Claude Code reports:

    * `Marketplace "claude-plugins-official" not found`: add the marketplace with `/plugin marketplace add anthropics/claude-plugins-official`, then retry the install.
    * The plugin is [not found in the marketplace](<https://code.claude.com/docs/en/plugins/install#install-a-plugin>): check the plugin name.

    If the install summary reports `Run /reload-plugins to apply.`, Claude Code then runs that reload for you. If the reload warns that your next message would re-read the conversation, run `/reload-plugins --force`.
  


  

#### Run the build skill

    ```
    /mcp-server-dev:build-mcp-server
    ```

    Claude asks about your use case and scaffolds a remote HTTP or local stdio server.
  


## Installing MCP servers

MCP servers can be configured in several ways depending on your needs:

### Option 1: Add a remote HTTP server

HTTP servers are the recommended option for connecting to remote MCP servers. This is the most widely supported transport for cloud-based services.

```bash theme={null}
# Basic syntax
claude mcp add --transport http <name> <url>

# Real example: Connect to Notion
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Example with Bearer token
claude mcp add --transport http secure-api https://api.example.com/mcp \
  --header "Authorization: Bearer your-token"
```

When configuring MCP servers via JSON in `.mcp.json`, `~/.claude.json`, or `claude mcp add-json`, the `type` field accepts `streamable-http` as an alias for `http`. The MCP specification uses the name `streamable-http` for this transport, so configurations copied from server documentation work without modification.

A JSON entry that has a `url` but no `type` is a configuration error, because Claude Code reads an entry with no `type` as a stdio server. Claude Code skips that server and reports `MCP server "<name>" has a "url" but no "type"; add "type": "http" (or "sse" / "ws") to this entry`. Before v2.1.202, Claude Code reported this misconfiguration as `command: expected string, received undefined`.

Only an SDK host application, such as an [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/mcp>) application or the [desktop app](<https://code.claude.com/docs/en/desktop>), can register an in-process `"type": "sdk"` server. Claude Code skips a `"type": "sdk"` entry in `.mcp.json`, `~/.claude.json`, or settings and reports `Skipped — MCP server "<name>" declares type "sdk", which only an SDK host application can register`.

In `--output-format stream-json` runs, Claude Code also reports a skipped `--mcp-config` entry in the `system/init` event's [`mcp_server_errors` field](<https://code.claude.com/docs/en/headless#stream-responses>), so scripts can detect that the server never loaded. This requires Claude Code v2.1.219 or later.

### Option 2: Add a remote SSE server



**注意：**

  The SSE (Server-Sent Events) transport is deprecated. Use HTTP servers instead, where available.


Some services still expose only an SSE endpoint. Add these with the same `claude mcp add --transport http <name> <url>` command as [an HTTP server](<#option-1-add-a-remote-http-server>). Claude Code tries the HTTP transport first and switches to SSE when the server doesn't accept it. The automatic switch requires Claude Code v2.1.265 or later.

On an earlier version, or to connect over SSE directly, pass `--transport sse` instead:

```bash theme={null}
# Basic syntax
claude mcp add --transport sse <name> <url>

# Real example: Connect to Asana
claude mcp add --transport sse asana https://mcp.asana.com/sse

# Example with authentication header
claude mcp add --transport sse private-api https://api.company.com/sse \
  --header "X-API-Key: your-key-here"
```

### Option 3: Add a local stdio server

Stdio servers run as local processes on your machine. They're ideal for tools that need direct system access or custom scripts.

Claude Code sets `CLAUDE_PROJECT_DIR` in the spawned server's environment to the project root, so your server can resolve project-relative paths without depending on the working directory. This is the same directory hooks receive in their `CLAUDE_PROJECT_DIR` variable. Read it from inside your server process, for example `process.env.CLAUDE_PROJECT_DIR` in Node or `os.environ["CLAUDE_PROJECT_DIR"]` in Python.

`CLAUDE_PROJECT_DIR` is the stable project root and doesn't change when you add or remove working directories mid-session. A server that limits its own filesystem access to a set of allowed directories should implement the MCP `roots/list` request instead. Claude Code answers `roots/list` with the session's launch directory plus every [additional working directory](<https://code.claude.com/docs/en/permissions#working-directories>) you've granted with `--add-dir`, `/add-dir`, or the `additionalDirectories` setting. Claude Code sends `notifications/roots/list_changed` when that set changes. Before v2.1.203, `roots/list` returned only the launch directory and Claude Code didn't send `notifications/roots/list_changed`.

This variable is set in the server's environment, not in Claude Code's own environment, so referencing it via `${VAR}` expansion in the `command` or `args` of a project-scoped `.mcp.json` entry or a local- or user-scoped server entry in `~/.claude.json` requires a default such as `${CLAUDE_PROJECT_DIR:-.}`. Plugin-provided MCP configurations substitute `${CLAUDE_PROJECT_DIR}` directly and don't need the default.

```bash theme={null}
# Basic syntax
claude mcp add [options] <name> -- <command> [args...]

# Real example: Add Airtable server
claude mcp add --env AIRTABLE_API_KEY=YOUR_KEY --transport stdio airtable \
  -- npx -y airtable-mcp-server
```



**注释：**

  For stdio servers, the `--` (double dash) separates Claude's own options, such as `--transport`, `--env`, and `--scope`, from the command and arguments that run the server. Everything after `--` is passed to the server untouched.

  For example:

  * `claude mcp add --transport stdio myserver -- npx server` → runs `npx server`
  * `claude mcp add --env KEY=value --transport stdio myserver -- python server.py --port 8080` → runs `python server.py --port 8080` with `KEY=value` in environment

  Without `--`, Claude Code would try to parse the server's flags, like `--port` above, as its own options.

  `--env` accepts multiple `KEY=value` pairs. If the server name comes directly after `--env`, the CLI reads the name as another pair and rejects it, so place at least one other option, such as `--transport stdio`, between `--env` and the server name.


### Option 4: Add a remote WebSocket server

WebSocket servers hold a persistent bidirectional connection, which suits remote MCP servers that push events to Claude unprompted. Use HTTP instead when your server only responds to requests, since HTTP supports OAuth and the `claude mcp add --transport` flag, while WebSocket supports neither.

Configure WebSocket servers in `.mcp.json` or with `claude mcp add-json`:

```bash theme={null}
claude mcp add-json events-server \
  '{"type":"ws","url":"wss://mcp.example.com/socket","headers":{"Authorization":"Bearer YOUR_TOKEN"}}'
```

The `type: "ws"` entry accepts the same `url`, `headers`, `headersHelper`, `timeout`, and `alwaysLoad` fields as `http`. Authentication is header-only, so pass a static token in `headers` or generate one at connect time with [`headersHelper`](<#use-dynamic-headers-for-custom-authentication>). The `claude mcp add --transport` flag doesn't accept `ws`.

### Add a server from setup instructions written for another client

MCP servers aren't specific to Claude Code, so a server's setup instructions may be written for Claude Desktop, Cursor, or another MCP client and give no `claude mcp add` command. To add the server anyway, look in those instructions for a URL, a launch command, or a JSON block:

* **A URL** such as `https://mcp.example.com/mcp`: the server is remote.
* **A launch command** such as `npx -y @example/mcp-server`: the server runs on your machine.
* **An `mcpServers` JSON block**: configuration written for another client's settings file.

Each is one of the inputs the four options in [Installing MCP servers](<#installing-mcp-servers>) take. Find the shape you have below to turn it into the command Claude Code accepts. Each command writes to [local scope](<#local-scope>) unless you add `--scope project` or `--scope user`.

<span id="from-a-url" />

#### Add a server from a URL

A URL means the server is remote. For an `https://` endpoint, add it with `--transport http`, or follow [Option 2](<#option-2-add-a-remote-sse-server>) when the instructions say the endpoint uses SSE. For a `wss://` endpoint, use [Option 4](<#option-4-add-a-remote-websocket-server>) instead, since `--transport` doesn't accept `ws`:

```bash theme={null}
claude mcp add --transport http example https://mcp.example.com/mcp
```

If the instructions also give an API key or token header, pass it with `--header` as shown in [Option 1](<#option-1-add-a-remote-http-server>).

<span id="from-an-npx-uvx-or-binary-command" />

#### Add a server from an `npx`, `uvx`, or binary command

A launch command means the server runs as a local stdio process. Put the whole command after `--`, so Claude Code passes flags such as `-y` to the command that starts the server instead of reading them as its own options. Pass any environment variables the instructions ask for with `--env`, after the server name and before `--`:

```bash theme={null}
claude mcp add example --env API_KEY=your-key -- npx -y @example/mcp-server
```

[Option 3](<#option-3-add-a-local-stdio-server>) covers the `--` separator in full.

<span id="from-an-mcpservers-json-block" />

#### Add a server from an `mcpServers` JSON block

An `mcpServers` block written for another MCP client, such as Claude Desktop, uses the wrapper key and entry shape Claude Code reads. Pass `claude mcp add-json` the object inside `mcpServers`, not the wrapper. Two entries need a repair first:

* **A `url` with no `type`**: add `"type": "http"`, `"type": "sse"`, or `"type": "ws"` to match the endpoint. Claude Code reads an entry with no `type` as a stdio server, so a `url` entry without a `type` fails.
* **A key with characters other than letters, numbers, hyphens, and underscores**: pick a server name that uses only those characters. Otherwise the key is the server name.

For example, this block:

```json theme={null}
{
  "mcpServers": {
    "example": {
      "command": "npx",
      "args": ["-y", "@example/mcp-server"]
    }
  }
}
```

becomes this command:

```bash theme={null}
claude mcp add-json example '{"command":"npx","args":["-y","@example/mcp-server"]}'
```

[Add MCP servers from JSON configuration](<#add-mcp-servers-from-json-configuration>) covers shell escaping and the `--scope` flag for `add-json`. To share the server with your team instead, add `--scope project`, or add the entry under `mcpServers` in `.mcp.json` at your project root and commit it. [Project scope](<#project-scope>) covers how Claude Code loads and approves that file.

Each `claude mcp add` and `claude mcp add-json` command prints an `Added ...` line on success. To check that Claude Code connected, run `claude mcp get <name>`; [Server status](<#server-status>) covers the statuses it shows and the approval step for `.mcp.json` servers.

### Managing your servers

Once configured, you can manage your MCP servers with these commands:

```bash theme={null}
# List all configured servers
claude mcp list

# Get details for a specific server
claude mcp get notion

# Remove a server
claude mcp remove notion

# (within Claude Code) Check server status
/mcp
```

When you remove a remote server, Claude Code also deletes the OAuth tokens and client registration it stored for that server.

#### Server status

`claude mcp add` confirms a successful add by printing an `Added ...` line, which means the configuration was written. If the command prints a `was not saved` message instead, see [MCP server was not saved or removed](<https://code.claude.com/docs/en/errors#mcp-server-was-not-saved-or-removed>); for a `may not have been saved` message, see [MCP server may not have been saved or removed](<https://code.claude.com/docs/en/errors#mcp-server-may-not-have-been-saved-or-removed>).

`claude mcp list` shows a health status next to each server it lists, such as `✔ Connected`, `! Needs authentication`, or `✘ Failed to connect`. A failure status means Claude Code couldn't connect to that server, not that the list command failed.

The statuses in this list report a configuration decision rather than a connection attempt, so Claude Code prints them without connecting to the server:

* ``⏸ Pending approval (run `claude` to approve)``: a project-scoped server from `.mcp.json` that you haven't approved yet. Claude Code shows it in both `claude mcp list` and `claude mcp get <name>`. Run `claude` interactively to review and approve it.
* `✘ Rejected (see disabledMcpjsonServers in settings)`: a `.mcp.json` server that a [`disabledMcpjsonServers`](<https://code.claude.com/docs/en/settings-reference#disabledmcpjsonservers>) entry rejects. Claude Code shows it only in `claude mcp get <name>`.
* `⊘ Disabled for this project (re-enable via /mcp)`: a server that the project's [`disabledMcpServers`](<#disable-a-server-without-removing-it>) list names. Claude Code shows it in both `claude mcp list` and `claude mcp get <name>`. Turn the server back on from the `/mcp` panel.

#### Project server approvals and workspace trust

As of v2.1.196, `claude mcp list` and `claude mcp get` read `.mcp.json` approvals only from settings files that aren't checked into the repository until you trust the workspace by running `claude` in it and accepting the workspace trust dialog. A cloned repository can't approve its own servers: [`enableAllProjectMcpServers`](<https://code.claude.com/docs/en/settings-reference#enableallprojectmcpservers>) or [`enabledMcpjsonServers`](<https://code.claude.com/docs/en/settings-reference#enabledmcpjsonservers>) committed to the project's `.claude/settings.json` is ignored in an untrusted folder, and the server stays at `⏸ Pending approval` instead of being connected and health-checked.

Approvals from these sources still apply in an untrusted folder:

* your user `~/.claude/settings.json`
* managed settings
* settings passed with `--settings`

Claude Code also applies approvals from an untracked `.claude/settings.local.json`, but it runs git to check whether the file is tracked, and it runs that check only in a [trusted folder](<https://code.claude.com/docs/en/permissions#project-allow-rules-and-workspace-trust>). In a folder you've never trusted, Claude Code waits for the trust dialog before applying the file's approvals, unless the folder is your own configuration home: your home directory, or a directory whose `.claude` you've set as [`CLAUDE_CONFIG_DIR`](<https://code.claude.com/docs/en/env-vars>). Before v2.1.207, Claude Code applied approvals from an untracked `.claude/settings.local.json` even in a folder you'd never trusted.

A `disabledMcpjsonServers` entry in any settings file still rejects the server.

#### Server status detail

In `/mcp`, including a server's menu there, and in the [`/plugin`](<https://code.claude.com/docs/en/plugins/install>) manager, a remote HTTP or SSE server you've used before can show a `cached` status such as `cached 2h ago · connects on first use · 5 tools`. Claude Code loaded the server's tool list from its discovery cache, saved in a previous session, instead of connecting at startup, and Claude Code connects the server the first time Claude calls one of the server's tools. The tools are available from your first message, so you don't need to do anything. The discovery cache and its `cached` status require Claude Code v2.1.221 or later.

The discovery cache is off by default unless a gradual rollout has enabled it for your account. Set [`MCP_DISCOVERY_CACHE=1`](<https://code.claude.com/docs/en/env-vars>) to turn it on, or `0` to keep it off even when the rollout has enabled it. Before v2.1.238, the cache was on by default.

When you select **Disable** or **Clear authentication** from a server's menu in `/mcp`, Claude Code also discards that server's cache entry. **Reconnect** discards it too on a connected or failed server; on a `cached` server, **Reconnect** connects the server now and keeps the entry. The next time Claude Code connects to the server after discarding the entry, it fetches the tool list from the server instead of from the cache.

When a server's status is `✘ Failed to connect`, `claude mcp list` appends the failure detail to that status line, and `claude mcp get <name>` shows it on an `Issue:` line: the HTTP status or error code, plus any error text the server returned. The server's detail view in `/mcp` includes the same server-reported text in its `Issue:` row. Claude Code redacts credential-like text from this detail and never includes the expanded server URL, which can carry secrets. Claude Code appends no detail to a `✘ Connection error` status, because the exception text it would print there can embed that URL. Before v2.1.219, both commands showed only the bare failure status, without the status code or the server's error text.

When you complete authentication from `/mcp` and the connection still fails with an HTTP status or a transport error code, Claude Code adds that code and the origin of the server's URL to the message it prints after the attempt. The origin is the scheme and host, plus the port when the URL names one, such as `https://mcp.example.com`.

* The path and query never appear in that message.
* For a server in the local, project, or user [scope](<#mcp-installation-scopes>) or in managed MCP configuration, the origin shows the host as written in that configuration, so a `${VAR}` reference in the host isn't expanded in the message.
* For a failure with no status or error code, Claude Code shows the error text without the origin.

A remote server whose configuration has an empty `url` shows as `not configured` in `/mcp`, in `claude mcp list`, and in the [`/plugin`](<https://code.claude.com/docs/en/plugins/install>) manager, and Claude Code doesn't attempt to connect to it. A plugin can include a placeholder entry like this for a connector you configure later, so Claude Code doesn't report it as an error or a setup issue. The server's detail view in `/mcp` reads `No URL configured for this server`; set the entry's `url` to connect it. Before v2.1.208, Claude Code reported an empty `url` as a configuration issue with a prompt to reconnect.

#### Configuration warnings

Claude Code warns about the configuration problems below. Each entry says what Claude Code checks and how to clear the warning:

* **Hidden whitespace**: Claude Code warns when an MCP config value carries hidden leading or trailing whitespace, which often comes from pasting a token with a trailing newline. Claude Code checks `command`, `url`, each `args` entry, and the values and key names under `env` and `headers`. Claude Code shows the warning in `claude mcp list` output and in `/mcp`, naming the affected fields without echoing their values, for example `Leading or trailing whitespace in: headers.Authorization`. Claude Code doesn't trim the whitespace and uses the values exactly as written, so edit the configuration to remove it.
* **Same name in more than one scope**: if you define the same server name in more than one [scope](<#mcp-installation-scopes>) with different endpoints, Claude Code warns about the conflict in `claude mcp list` output and in `/mcp`. Claude Code stores OAuth sign-ins per endpoint, so when you authenticate the definition that loads in one project, you still need to sign in separately in a project where a different definition loads. Keep the endpoint you want and remove the others with `claude mcp remove <name> --scope <scope>`. In the warning, Claude Code quotes each scope's endpoint as written in your configuration, with [`${VAR}` references](<#environment-variable-expansion-in-mcp-json>) unexpanded, so it never shows a resolved value such as an API key.
* **Reserved names**: Claude Code reserves the names of its built-in servers, including `workspace`, `claude-in-chrome`, `computer-use`, `Claude Preview`, and `Claude Browser`. If your configuration defines a server with a reserved name, Claude Code skips it at load time and shows a warning asking you to rename it. `claude mcp add` rejects a reserved name with an error. `Claude Preview` and `Claude Browser` both name the built-in server that the [Claude Code desktop app's preview pane](<https://code.claude.com/docs/en/desktop#preview-your-app>) uses.
* **Missing environment variable**: if a [`${VAR}` reference](<#environment-variable-expansion-in-mcp-json>) in a server's configuration names a variable that isn't set and has no `:-default`, Claude Code warns in `claude mcp list` output and in `/mcp`, naming the variable, and still loads the server with the `${VAR}` text unexpanded. Set the variable or add a `${VAR:-default}` fallback. In a remote server's `url` and `headers`, some credential variables [read as empty](<#credential-variables-that-read-as-empty>) instead, with no warning.

#### Tool availability

The `/mcp` panel shows the tool count next to each connected server and flags servers that advertise the tools capability but expose no tools.

If your request needs tools from a server that is still connecting in the background, Claude waits for that server before continuing. How the wait happens depends on your configuration:

* **With [tool search](<#scale-with-mcp-tool-search>), the default**: the wait happens inside the `ToolSearch` call.
* **Without tool search**: Claude uses the `WaitForMcpServers` tool instead. Configurations without tool search include a custom `ANTHROPIC_BASE_URL`, `ENABLE_TOOL_SEARCH=false`, and a model earlier than the Claude 4.5 generation on Google Cloud's Agent Platform.
* **On a Microsoft Foundry [deployment hosted on Azure](<https://platform.claude.com/docs/en/build-with-claude/claude-in-microsoft-foundry#hosting-options>)**: Claude starts on the tool-search path rather than with `WaitForMcpServers`, since Claude Code discovers the deployment's server-side rejection only from the API. After Claude Code switches that deployment to [upfront loading](<#scale-with-mcp-tool-search>), tools from a server that finishes connecting become available on Claude's next request.

With tool search enabled, when a server finishes connecting while Claude is working, Claude Code lists the server's tool names to Claude on its next request in the same turn. Claude can then search for and call those tools without waiting for your next message.

After you resume a session, Claude can call a tool from the saved conversation while the tool's MCP server is still connecting. While the server is on its first connection attempt, Claude Code holds the call for up to 10 seconds and runs it once the tool is available. If the server doesn't connect in time, or it is already [retrying after a failed attempt](<#automatic-reconnection>), the call fails with the `No such tool available` [tool error](<https://code.claude.com/docs/en/errors#no-such-tool-available>).

### Disable a server without removing it

Toggle a server off in the `/mcp` panel to stop Claude Code from connecting to it without losing its configuration. Claude Code still lists the server in `/mcp`, marked as disabled.

When you toggle a server, Claude Code records your choice per project in `~/.claude.json`, in one of two lists that cover disjoint sets of servers:

* `disabledMcpServers`: an opt-out list for user-configured servers, plugin servers, servers your organization [provides through managed settings](<https://code.claude.com/docs/en/managed-mcp#provide-servers-through-managed-settings>), the claude.ai connectors Claude Code [fetches itself](<#how-connectors-reach-claude-code>), and built-in servers that default to on. Claude Code doesn't connect to a server you list here. When you disable a claude.ai connector with the per-project `/mcp` toggle described in [Disable claude.ai connectors](<#disable-claude-ai-connectors>), Claude Code writes it to this list under its display name, for example `claude.ai Slack`.
* `enabledMcpServers`: an opt-in list for built-in servers that default to off, such as `computer-use`. Claude Code connects to a default-off server only when you list it here.

Claude Code consults exactly one of the two lists for each server, so neither list overrides the other. If you add a regular server to `enabledMcpServers`, or a default-off built-in server to `disabledMcpServers`, Claude Code ignores the entry.

`disabledMcpServers` and `enabledMcpServers` are unrelated to [`enabledMcpjsonServers`](<https://code.claude.com/docs/en/settings-reference#enabledmcpjsonservers>) and [`disabledMcpjsonServers`](<https://code.claude.com/docs/en/settings-reference#disabledmcpjsonservers>), which control approval of servers defined in a project's `.mcp.json` file.

### MCP client runtimes

Claude Code connects to MCP servers through one of two client runtimes. The v1 runtime is built on MCP TypeScript SDK 1.x. The v2 runtime is the same code on [MCP TypeScript SDK 2.0](<https://ts.sdk.modelcontextprotocol.io/v2/>), which adds MCP protocol revision 2026-07-28. The rest of this page applies to both runtimes, except where a section names the v2 runtime.

Claude Code picks a runtime each time you start it and keeps it until you exit. In sessions where it [fetches feature flags](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>), it uses the v2 runtime on Claude Code v2.1.232 or later.

In the sessions where it doesn't fetch feature flags, Claude Code uses the v2 runtime by default on Claude Code v2.1.274 or later:

* Sessions on Amazon Bedrock, Claude Platform on AWS, Google Cloud's Agent Platform, or Microsoft Foundry, unless a host platform that embeds Claude Code sets [`CLAUDE_CODE_PROVIDER_MANAGED_BY_HOST`](<https://code.claude.com/docs/en/env-vars>)
* Sessions signed in through a [Claude apps gateway](<https://code.claude.com/docs/en/claude-apps-gateway>)
* Sessions where you turn off telemetry or feature-flag fetching, for example with `DISABLE_TELEMETRY`

On v2, Claude Code also:

* Asks HTTP, stdio, and claude.ai connector servers whether they support the newer revision, and uses it with those that do. It connects to every other server as v1 does.
* Receives `list_changed` notifications from servers on the newer revision over a [stream it holds open](<#notification-streams-on-the-v2-runtime>).
* Doesn't register a [channel](<#push-messages-with-channels>) server that connects on the newer revision, because that revision can't carry channel messages.
* Fails an [MCP OAuth sign-in](<#authenticate-with-remote-mcp-servers>) whose authorization response names an unexpected issuer.
* Sends [MCP OAuth](<#authenticate-with-remote-mcp-servers>) credentials only to a token endpoint served over HTTPS or at `localhost`, `127.0.0.1`, or `::1`. Sign-in fails for a server whose token endpoint is plain `http://` anywhere else, such as a device on your local network. See [Refusing to send credentials to non-https token endpoint](<https://code.claude.com/docs/en/errors#refusing-to-send-credentials-to-non-https-token-endpoint>).

Anthropic can keep a specific server on the earlier protocol, or off that stream, with a feature flag Claude Code fetches.

To pick the runtime yourself, set [`MCP_SDK_GENERATION`](<https://code.claude.com/docs/en/env-vars>) to `v1` or `v2`. To decide whether Claude Code asks, set [`MCP_PROTOCOL_NEGOTIATION`](<https://code.claude.com/docs/en/env-vars>) to `auto` or `legacy`.

### Dynamic tool updates

An MCP server can change the tools, prompts, or resources it offers while connected and send a `list_changed` notification. When one arrives:

* **In an interactive terminal session**, Claude Code fetches the updated list from that server, so you don't need to reconnect it.
* **In [non-interactive mode](<https://code.claude.com/docs/en/headless>) with the `-p` flag and in the [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>)**, Claude Code refreshes only the tool list on these notifications.

If a refresh request fails, Claude Code keeps the server's previously discovered tools, prompts, and resources until a later refresh succeeds. Before v2.1.214, a transient error during the refresh replaced the server's tools, prompts, and resources with an empty list.

#### Notification streams on the v2 runtime

On the [v2 runtime](<#mcp-client-runtimes>), Claude Code receives `list_changed` notifications from a server on the newer protocol revision over a stream it holds open. When the stream closes, Claude Code reopens it, with two limits:

* **The stream closes again within 10 seconds**: Claude Code reopens it up to three times, then stops for that connection.
* **The stream stays open longer than 10 seconds, then closes**, as streams to serverless hosts commonly do: after five reopens in an hour, Claude Code waits about six hours before the next one.

Until the stream reopens, you keep the server's last fetched tools, prompts, and resources. To pick up its changes sooner, reconnect the server from `/mcp`.

### Automatic reconnection

Claude Code reconnects a remote server that drops mid-session and retries an HTTP or SSE server's first connection after a transient error. Stdio servers are local processes, and Claude Code doesn't reconnect them automatically.

#### Mid-session drops of a remote server

Claude Code reconnects a dropped remote server with exponential backoff: up to five attempts, starting at a one-second delay and doubling it each time. What you see depends on how you're running Claude Code:

* **In an interactive session**: `/mcp` shows the server as pending while Claude Code reconnects. After five failed attempts, Claude Code marks the server as failed, or as needing authentication when the server needs authorizing again. When it marks the server as failed, you see an `MCP server "<name>" disconnected · open /mcp to reconnect` notification. You can retry manually from `/mcp`.
* **In [`claude -p`](<https://code.claude.com/docs/en/headless>) runs and [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>) sessions**: Claude Code reconnects on the same schedule, with no `/mcp` panel to show the attempts.

#### Failed first connections

When an HTTP or SSE server's first connection fails with a transient error, such as a 5xx response, a connection refused, or a timeout, Claude Code retries up to three times. If the connection still fails, Claude Code marks the server as failed.

Claude Code doesn't retry in these cases:

* A WebSocket server's first connection
* An authentication or not-found error, because it requires a configuration change to resolve. When a [`headersHelper`](<#use-dynamic-headers-for-custom-authentication>) is the server's only source of the `Authorization` header, Claude Code retries an authentication error anyway, because it re-runs the helper on each attempt and can pick up a fresh credential

#### Failed discovery requests

After a server connects, Claude Code sends it capability discovery requests such as `tools/list`, `prompts/list`, and `resources/list`. Claude Code retries those requests up to three times with short backoff after a transient network or server error. It doesn't retry authentication errors, 4xx responses, or request timeouts.

#### Retry failed servers yourself

To retry every server that failed or needs authentication, run `/mcp reconnect all`. In the interactive terminal this requires Claude Code v2.1.284 or later, and earlier versions print `MCP server "all" not found` there.

#### How Claude learns that a server failed

Whether Claude Code tells Claude about a configured server that failed to connect depends on [tool search](<#scale-with-mcp-tool-search>), which is on by default:

* With tool search, Claude Code tells Claude which server failed and its connection error, so Claude reports the connection failure in its response. Claude Code includes the same information in `ToolSearch` results that find no matching tool.
* In any [configuration without tool search](<#configure-tool-search>), Claude Code doesn't report failed server connections to Claude.

### Push messages with channels

An MCP server can also push messages directly into your session so Claude can react to external events like CI results, monitoring alerts, or chat messages. To enable this, your server declares the `claude/channel` capability and you opt it in with the `--channels` flag at startup. See [Channels](<https://code.claude.com/docs/en/channels>) to use an officially supported channel, or [Channels reference](<https://code.claude.com/docs/en/channels-reference>) to build your own.

On the [v2 runtime](<#mcp-client-runtimes>), a channel server that negotiates MCP protocol revision 2026-07-28 can't deliver channel messages, so Claude Code doesn't register it as a channel. A channel server that doesn't support that revision connects on the earlier handshake and registers as before.

Claude Code asks stdio servers for that revision by default. To keep a stdio channel server on the earlier handshake, set [`MCP_PROTOCOL_NEGOTIATION`](<https://code.claude.com/docs/en/env-vars>) to `legacy`, which keeps every server on it.



**提示：**

  Tips:

  * Use the `-s` or `--scope` flag to specify where the configuration is stored:
    * `local` (default): available only to you in the current project
    * `project`: shared with everyone in the project via the `.mcp.json` file
    * `user`: available to you across all projects
  * Set environment variables with `-e` or `--env` flags (for example, `-e KEY=value`)
  * The `--transport` and `--header` flags also accept `-t` and `-H` short forms
  * Configure MCP server startup timeout using the `MCP_TIMEOUT` environment variable (for example, `MCP_TIMEOUT=10000 claude` sets a 10-second timeout)
  * Set a per-server tool execution timeout by adding a `timeout` field in milliseconds to that server's `.mcp.json` entry, for example `"timeout": 600000` for ten minutes. This overrides the `MCP_TOOL_TIMEOUT` environment variable for that server only
  * Claude Code displays a warning when MCP tool output exceeds 10,000 tokens and limits output to 25,000 tokens by default. To change the token limit, set the `MAX_MCP_OUTPUT_TOKENS` environment variable, for example `MAX_MCP_OUTPUT_TOKENS=50000`. The warning threshold is fixed. Unless the server raises a tool's own limit, successful text results longer than 50,000 characters are saved to a file regardless of this variable. See [MCP output limits and warnings](<#mcp-output-limits-and-warnings>)
  * Use `/mcp` to authenticate with remote servers that require OAuth 2.0 authentication



The per-server `timeout` is a hard wall-clock limit per tool call, and progress notifications from the server don't extend it. Values below 1000 are ignored and fall through to `MCP_TOOL_TIMEOUT`, or to its default of about 28 hours when that variable is unset. For an HTTP, SSE, or [claude.ai connector](<https://code.claude.com/docs/en/mcp#use-mcp-servers-from-claude-ai>) server there is also a second, per-request timer that covers each request through to the server's first response byte. Claude Code sets that timer to the greatest of three values: 60 seconds, the tool timeout that applies to the server, and `MCP_TIMEOUT`. The 28-hour default of an unset `MCP_TOOL_TIMEOUT` doesn't enter that comparison, and a value below 60 seconds doesn't shorten the timer. Stdio and WebSocket servers have no per-request timer.

A per-server `timeout` of at least 1000 also acts as a floor on the idle timeout described below: Claude Code never aborts that server's tool calls for idleness sooner than the per-server `timeout`. Requires Claude Code v2.1.203 or later.

A tool call to an MCP server that sends no response and no progress notification for the idle window aborts with an error instead of waiting for the wall-clock limit. The idle timeout applies to every server type except IDE servers and SDK in-process servers. The idle window defaults to five minutes for HTTP, SSE, WebSocket, and [claude.ai connector](<#use-mcp-servers-from-claude-ai>) servers, and to 30 minutes for stdio servers. Before v2.1.203, stdio servers were exempt from the idle timeout.

Set the [`CLAUDE_CODE_MCP_TOOL_IDLE_TIMEOUT`](<https://code.claude.com/docs/en/env-vars>) environment variable in milliseconds to change the idle window, or set it to `0` to disable the check.

These timeouts bound how long a call can run, not always how long it blocks the session: a main-conversation call that runs past two minutes moves to a background task first. See [Automatic backgrounding of long tool calls](<#automatic-backgrounding-of-long-tool-calls>).

### Automatic backgrounding of long tool calls

An MCP tool call in the main conversation that is still running after two minutes moves to a background task instead of blocking the session. Claude receives the task ID immediately and keeps working, and the result arrives as a task notification when the call settles. Automatic backgrounding requires Claude Code v2.1.212 or later.

The task appears in [`/tasks`](<https://code.claude.com/docs/en/commands#all-commands>), where you can also stop it, and it doesn't survive exiting the session. The task's entry shows the latest progress the server has reported.

The per-call limits still apply while the call runs in the background: the wall-clock limit set by the per-server `timeout` or [`MCP_TOOL_TIMEOUT`](<https://code.claude.com/docs/en/env-vars>), and the idle timeout set by [`CLAUDE_CODE_MCP_TOOL_IDLE_TIMEOUT`](<https://code.claude.com/docs/en/env-vars>).

Set the [`CLAUDE_CODE_MCP_AUTO_BACKGROUND_MS`](<https://code.claude.com/docs/en/env-vars>) environment variable in milliseconds to change the threshold, or set it to `0` to turn automatic backgrounding off. Setting `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS` to `1` also turns it off, along with all other background task features.

Some calls never move to the background:

* Calls from [subagents](<https://code.claude.com/docs/en/sub-agents>); Claude Code backgrounds only main-conversation calls
* Calls to IDE servers
* Calls in [non-interactive mode](<https://code.claude.com/docs/en/headless>), unless `CLAUDE_AUTO_BACKGROUND_TASKS` is set to `1`, since a one-shot run can end before the result arrives

A call waiting on an open [elicitation dialog](<#respond-to-mcp-elicitation-requests>) isn't backgrounded while the dialog is open; the server is blocked on your input, not slow, so Claude Code defers the move until the dialog closes.

### Plugin-provided MCP servers

[Plugins](<https://code.claude.com/docs/en/plugins/overview>) can bundle MCP servers that provide tools and integrations when you enable the plugin.

**How plugin MCP servers work**:

* Plugins define MCP servers in `.mcp.json` at the plugin root or inline in `plugin.json`
* When you enable a plugin, Claude Code starts its MCP servers automatically
* Claude Code offers plugin MCP tools alongside manually configured MCP tools
* You add and remove plugin servers by installing or uninstalling the plugin, not with `/mcp` commands. You can still [toggle an installed plugin server off](<#disable-a-server-without-removing-it>) in `/mcp`, which stops Claude Code from connecting to it without removing the plugin

**Example plugin MCP configuration**:

In `.mcp.json` at plugin root:

```json theme={null}
{
  "mcpServers": {
    "database-tools": {
      "command": "${CLAUDE_PLUGIN_ROOT}/servers/db-server",
      "args": ["--config", "${CLAUDE_PLUGIN_ROOT}/config.json"],
      "env": {
        "DB_URL": "${DB_URL}"
      }
    }
  }
}
```

Or inline in `plugin.json`:

```json theme={null}
{
  "name": "my-plugin",
  "mcpServers": {
    "plugin-api": {
      "command": "${CLAUDE_PLUGIN_ROOT}/servers/api-server",
      "args": ["--port", "8080"]
    }
  }
}
```

**Plugin MCP features**:

* **Automatic lifecycle**: servers connect and disconnect at these points:
  * At session startup, Claude Code connects the servers for enabled plugins automatically. In `/mcp`, a remote (HTTP or SSE) plugin server you've used before can show the [`cached` status](<#server-status-detail>) instead; Claude Code connects it when Claude first calls one of its tools
  * If you enable or disable a plugin during a session, Claude Code connects or disconnects its MCP servers when the change applies. [Apply plugin changes without restarting](<https://code.claude.com/docs/en/plugins/cli-reference#reload-plugins>) describes when that is. In a session without an interactive terminal, `/reload-plugins` doesn't connect or disconnect plugin MCP servers; those changes take effect in your next session
  * When you reload, Claude Code keeps the live connections of plugin servers whose configuration is unchanged, and does the same when you [replace the session's MCP server list](<https://code.claude.com/docs/en/agent-sdk/typescript#mcpsetserversresult>) from the Agent SDK without naming them
  * When you [move the session with `/cd`](<https://code.claude.com/docs/en/permissions#move-the-session-to-another-directory>) on v2.1.246 or later, Claude Code connects the servers of plugins the new directory's settings enable and disconnects the servers of plugins that are no longer enabled, so you don't need to run `/reload-plugins` after the move
  * In [cloud sessions](<https://code.claude.com/docs/en/claude-code-on-the-web>), an MCP call to a plugin server that isn't connected yet, such as right after an idle session wakes, starts the server on demand and waits for it to connect
* **Path placeholders**: `${CLAUDE_PLUGIN_ROOT}` resolves to the plugin's installation directory, `${CLAUDE_PLUGIN_DATA}` to its [persistent state](<https://code.claude.com/docs/en/plugins/components#path-variables-and-persistent-data>) directory, and `${CLAUDE_PROJECT_DIR}` to the stable project root. Substitution applies to:
  * `stdio` servers: `command`, `args`, `env`
  * `http`, `sse`, and `ws` servers: `url`, `headers`, and `headersHelper`
* **User environment access**: access to the same environment variables as manually configured servers
* **Multiple transport types**: support for stdio, SSE, HTTP, and WebSocket transports, though transport support may vary by server

Plugin servers appear in `/mcp` with indicators showing they come from plugins.

For a plugin's stdio server, `claude mcp get` prints `Command: stdio`, an empty `Args:` line, and each environment variable as `NAME=[REDACTED]`. The values are hidden because they can carry credentials.

**Plugin MCP tool names**:

Tools from a plugin-bundled MCP server include both the plugin name and the server key in their callable name. The full form is `mcp__plugin_<plugin-name>_<server-name>__<tool-name>`, where any character outside `A-Z`, `a-z`, `0-9`, `_`, and `-` is replaced with `_`. For the `database-tools` server bundled in a plugin named `my-plugin`, a `query` tool is callable as:

```
mcp__plugin_my-plugin_database-tools__query
```

Use this full name when referencing the tool in [permission rules](<https://code.claude.com/docs/en/permissions>), a skill's `allowed-tools` list, a [subagent's `tools` field](<https://code.claude.com/docs/en/sub-agents#available-tools>), or a [hook matcher](<https://code.claude.com/docs/en/hooks#match-mcp-tools>). A hook matcher written against the bare server key, such as `mcp__database-tools__.*`, never fires for a plugin-bundled server.

The server itself registers under the scoped name `plugin:<plugin-name>:<server-name>`, such as `plugin:my-plugin:database-tools`. Use that name where a configured server name is expected, such as an [`mcp_tool` hook's `server` field](<https://code.claude.com/docs/en/hooks#mcp-tool-hook-fields>).

See the [plugin components reference](<https://code.claude.com/docs/en/plugins/components#mcp-servers>) for details on bundling MCP servers with plugins.

## MCP installation scopes

MCP servers can be configured at three scopes. The scope you choose controls which projects the server loads in and whether the configuration is shared with your team. Administrators can also deploy or provide servers for every user via [managed configuration](<#managed-mcp-configuration>).

| Scope | Loads in | Shared with team | Stored in |
| - | - | - | - |
| [Local](<#local-scope>) | Current project only | No | `~/.claude.json` |
| [Project](<#project-scope>) | Current project only | Yes, via version control | `.mcp.json` in project root |
| [User](<#user-scope>) | All your projects | No | `~/.claude.json` |

### Local scope

Local scope is the default. A local-scoped server loads only in the project where you added it and stays private to you. Claude Code stores it in `~/.claude.json` under that project's path, so the same server won't appear in your other projects. Use local scope for personal development servers, experimental configurations, or servers with credentials you don't want in version control.



**注释：**

  The term "local scope" for MCP servers differs from general local settings. MCP local-scoped servers are stored in `~/.claude.json` (your home directory), while general local settings use `.claude/settings.local.json` (in the project directory). See [Settings](<https://code.claude.com/docs/en/settings#where-settings-live>) for details on settings file locations.


```bash theme={null}
# Add a local-scoped server (default)
claude mcp add --transport http stripe https://mcp.stripe.com

# Explicitly specify local scope
claude mcp add --transport http stripe --scope local https://mcp.stripe.com
```

The command writes the server into the entry for your current project inside `~/.claude.json`. The example below shows the result when you run it from `/path/to/your/project`:

```json theme={null}
{
  "projects": {
    "/path/to/your/project": {
      "mcpServers": {
        "stripe": {
          "type": "http",
          "url": "https://mcp.stripe.com"
        }
      }
    }
  }
}
```

### Project scope

Project-scoped servers enable team collaboration by storing configurations in a `.mcp.json` file at your project's root directory. When you add a project-scoped server, Claude Code automatically creates or updates this file with the appropriate configuration structure. Check `.mcp.json` into version control so everyone on your team gets the same MCP tools and services.

```bash theme={null}
# Add a project-scoped server
claude mcp add --transport http shared-server --scope project https://example.com/mcp
```

The resulting `.mcp.json` file follows a standardized format:

```json theme={null}
{
  "mcpServers": {
    "shared-server": {
      "type": "http",
      "url": "https://example.com/mcp"
    }
  }
}
```

For security reasons, Claude Code prompts for approval in interactive sessions before using project-scoped servers from `.mcp.json` files. To reset those approval choices, run `claude mcp reset-project-choices`.

In `claude -p` runs, [Agent SDK](<https://code.claude.com/docs/en/headless>) sessions, and [cloud sessions](<https://code.claude.com/docs/en/claude-code-on-the-web>), Claude Code can't show that prompt: it loads project-scoped servers without asking. Claude Code also skips the prompt in a session you start in `bypassPermissions` mode with [`skipDangerousModePermissionPrompt`](<https://code.claude.com/docs/en/settings-reference#skipdangerousmodepermissionprompt>) set in your user settings or in managed settings. To keep a server out anyway:

* Add it to [`disabledMcpjsonServers`](<https://code.claude.com/docs/en/settings-reference#disabledmcpjsonservers>), which blocks it in every permission mode.
* Exclude project settings entirely with [`--setting-sources`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) or the SDK's `settingSources` option.
* Start the session with [`--strict-mcp-config`](<https://code.claude.com/docs/en/cli-reference#cli-flags>). Claude Code then uses only the MCP servers you pass with `--mcp-config`. Skipping the approval prompt for the project-scoped servers Claude Code isn't loading requires Claude Code v2.1.246 or later; before v2.1.246, a strict session still waited on approval for them, which left background sessions waiting at startup. See [Exclusive control with managed-mcp.json](<https://code.claude.com/docs/en/managed-mcp#exclusive-control-with-managed-mcp-json>) for what the flag does under a managed MCP file.

[Project server approvals and workspace trust](<#project-server-approvals-and-workspace-trust>) covers how approvals committed to the repository interact with workspace trust.

### User scope

User-scoped servers are stored in `~/.claude.json` and provide cross-project accessibility, making them available across all projects on your machine while remaining private to your user account. This scope works well for personal utility servers, development tools, or services you frequently use across different projects.

```bash theme={null}
# Add a user server
claude mcp add --transport http hubspot --scope user https://mcp.hubspot.com/anthropic
```

### Scope hierarchy and precedence

When the same server is defined in more than one place, Claude Code connects to it once, using the definition from the highest-precedence source. The entire server entry from that source is used; fields are not merged across scopes.

1. Local scope
2. Project scope
3. User scope
4. [Plugin-provided servers](<https://code.claude.com/docs/en/plugins/components#mcp-servers>)
5. [claude.ai connectors](<#use-mcp-servers-from-claude-ai>)

Claude Code matches duplicates across the three scopes by name. It matches plugins and connectors by endpoint, so one that points at the same URL or command as an enabled server above counts as a duplicate.

Two URL spellings count as the same endpoint when they differ only in the letter case of the scheme or host, the scheme's default port, such as `:443` on `https`, or a trailing slash. A different path, query string, userinfo, or non-default port makes two servers.

A server your organization provides through the [`managedMcpServers`](<https://code.claude.com/docs/en/managed-mcp#provide-servers-through-managed-settings>) managed setting ranks above all of these, so when one of them duplicates it, Claude Code connects the organization's definition. Requires Claude Code v2.1.259 or later.

If you open a local session in the [Desktop app's Code tab](<https://code.claude.com/docs/en/desktop#mcp-servers-from-the-claude-desktop-chat-app>) with the same stdio server name at the top level of `~/.claude.json` (user scope) and in `.mcp.json`, the Code tab uses the `~/.claude.json` definition.

### Environment variable expansion in `.mcp.json`

Claude Code supports environment variable expansion in `.mcp.json` files, allowing teams to share configurations while maintaining flexibility for machine-specific paths and sensitive values like API keys.

#### Supported syntax

* `${VAR}`: expands to the value of environment variable `VAR`
* `${VAR:-default}`: expands to `VAR` if set, otherwise uses `default`

#### Expansion locations

Environment variables can be expanded in:

* `command`: the server executable path
* `args`: command-line arguments
* `env`: environment variables passed to the server
* `url`: for HTTP server types
* `headers`: for HTTP server authentication

#### Example with variable expansion

```json theme={null}
{
  "mcpServers": {
    "api-server": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": {
        "Authorization": "Bearer ${API_KEY}"
      }
    }
  }
}
```

#### Unset variables without a default

If a referenced environment variable isn't set and has no default value, the config still loads: Claude Code reports a missing-variable warning for that server in `claude mcp list` output and uses the unexpanded `${VAR}` text as-is. Set the variable or add a `:-default` fallback so the server starts with the value you intend. In a remote server's `url` and `headers`, some credential variables [read as empty](<#credential-variables-that-read-as-empty>) instead, with no warning.

#### Credential variables that read as empty

In a remote server's `url` and `headers`, Claude Code reads credential variables from your environment as empty rather than expanding them. This keeps a project's `.mcp.json` or a plugin from sending your Claude Code or cloud provider credentials to a server it names. If you write `Bearer ${ANTHROPIC_AUTH_TOKEN}`, the server receives `Bearer ` with no credential and rejects the request, usually with a `401`. Claude Code reports that as a failed connection.

The covered names are:

* Claude Code's own credentials, such as `ANTHROPIC_API_KEY` and `ANTHROPIC_AUTH_TOKEN`
* Your cloud provider's credentials, such as `AWS_BEARER_TOKEN_BEDROCK`
* Other credentials your environment carries, such as `HTTPS_PROXY` and `NPM_TOKEN`

A covered name reads as empty whether or not you have set the variable, and a `:-default` fallback on it is ignored. A provider base URL such as `ANTHROPIC_BASE_URL` still expands, so `"url": "${ANTHROPIC_BASE_URL}/mcp"` works, unless the URL's value itself embeds a credential such as a username and password.

A name outside this set, such as `API_KEY`, expands as written. To give the server one of the covered credentials, copy it into a variable with a name of your own and reference that name instead.

When a remote server's `url` or `headers` references a covered variable you have set, Claude Code names it in a debug-log line. To read the line, run `claude --debug-file /tmp/claude-debug.log` and search that file for `never expanded toward a remote server`.

#### How references appear in `/mcp` and CLI output

For a server in the local, project, or user [scope](<#mcp-installation-scopes>), the following surfaces show a `${VAR}` reference by name rather than as its resolved value:

* The URL or command line in a server's `/mcp` detail view
* `claude mcp list` and `claude mcp get` output

The `/mcp` detail view shows references this way in Claude Code v2.1.268 or later.

For a server your organization provides through the `managedMcpServers` setting, these surfaces show [the URL's host only](<https://code.claude.com/docs/en/managed-mcp#what-users-can-see-and-change>).

To check what `claude mcp list`, `claude mcp get`, and `/mcp` show when a connection fails, see [Server status detail](<#server-status-detail>).

## Practical examples

### Example: Connect to GitHub for code reviews

GitHub's remote MCP server authenticates with a GitHub personal access token passed as a header. To get one, open your [GitHub token settings](<https://github.com/settings/personal-access-tokens>), generate a new fine-grained token with access to the repositories you want Claude to work with, then add the server:

```bash theme={null}
claude mcp add --transport http github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer YOUR_GITHUB_PAT"
```

Replace `YOUR_GITHUB_PAT` with your personal access token. The `claude mcp add` command saves the configuration without validating credentials, so a placeholder value is accepted here but the server fails to connect later. To verify the connection, run `/mcp` and check that the server shows `connected`. A server with bad credentials shows `failed`, and the failure detail includes the HTTP status the server returned, such as a 401.

Then work with GitHub:

```text wrap theme={null}
Review PR #456 and suggest improvements
```

```text wrap theme={null}
Create a new issue for the bug we just found
```

```text wrap theme={null}
Show me all open PRs assigned to me
```

### Example: Query your PostgreSQL database

[DBHub](<https://github.com/bytebase/dbhub>), the `@bytebase/dbhub` package, is an MCP server that connects Claude to a relational database through the connection string you pass in `--dsn`. Use a read-only database user in the connection string so the queries Claude runs can't modify data:

```bash theme={null}
claude mcp add --transport stdio db -- npx -y @bytebase/dbhub \
  --dsn "postgresql://readonly:pass@prod.db.com:5432/analytics"
```

To confirm the server starts, run `/mcp` and check that `db` shows `connected`.

Then query your database naturally:

```text wrap theme={null}
What's our total revenue this month?
```

```text wrap theme={null}
Show me the schema for the orders table
```

```text wrap theme={null}
Find customers who haven't made a purchase in 90 days
```

## Authenticate with remote MCP servers

Many cloud-based MCP servers require authentication. Claude Code supports OAuth 2.0 for secure connections.

Claude Code marks a remote server as needing authentication when the server responds with `401 Unauthorized` or `403 Forbidden`. What Claude Code shows depends on the server:

* For a server you haven't signed in to, either status code flags it in `/mcp` so you can complete the OAuth flow.
* For a [claude.ai connector](<#use-mcp-servers-from-claude-ai>), a `401` caused by claude.ai rejecting your session token doesn't flag the connector, because re-authorizing the connector can't fix your login. Claude Code shows the [session-token-rejected state](<https://code.claude.com/docs/en/errors#claude-ai-rejected-the-session-token>) instead.
* For a server whose `Authorization` header you configured, in `headers` or through a [`headersHelper`](<#use-dynamic-headers-for-custom-authentication>), a `401` or `403` while connecting doesn't flag the server, because the credential to fix is the one you configured. Claude Code reports the connection as failed instead. If you set that header from a `${VAR}` reference, check whether that variable is one Claude Code [reads as empty](<#credential-variables-that-read-as-empty>).
* For a connector [delivered to a cloud session](<#how-connectors-reach-claude-code>), Claude Code doesn't run a sign-in flow, because the session's proxy authenticates to the connector with the authorization you granted in claude.ai. When a connector there needs authorizing again, reconnect it at [claude.ai/customize/connectors](<https://claude.ai/customize/connectors>) rather than from the session.

When a request to an OAuth server you already signed in to returns `401 Unauthorized`, Claude Code refreshes the stored token, reconnects, and retries the request once. It flags the server in `/mcp` only if that retry also fails. Before v2.1.206, a token refresh that failed for a transient reason, such as a network error, flagged an OAuth server as needing authentication for the rest of the session even though its refresh token was still valid.

When the server rejects the stored refresh token, Claude Code immediately shows a notice pointing at `/mcp`. Open `/mcp` and select **Re-authenticate** on the server to sign in again before the next tool call fails.

A custom server that returns a `WWW-Authenticate` header pointing to its authorization server gets the same automatic discovery as any other remote server.

Claude Code also shows a startup notice when one or more configured servers need authentication, so you don't have to open `/mcp` to discover which servers need sign-in. The notice counts only servers you can sign in to from Claude Code. Before v2.1.218, it also counted [claude.ai connectors](<#use-mcp-servers-from-claude-ai>) that weren't connected in claude.ai, which you can connect only from claude.ai settings.

The notice announces each server once and leaves it out of the count at later launches until that server has connected and needs sign-in again. `/mcp` still lists every server that needs sign-in.

In non-interactive mode there's no `/mcp` panel, so Claude Code can't run the OAuth flow for you. As of v2.1.196, when a configured server needs authentication during a `claude -p` or Agent SDK run with [tool search](<#scale-with-mcp-tool-search>) enabled, which is the default, Claude Code tells Claude that the server's tools are unavailable until you authorize it. Claude can then name the server that needs sign-in instead of responding as if the server weren't configured. Complete the sign-in from an interactive session with `/mcp` or `claude mcp login <name>`.

If you configured `headers.Authorization` for the server and the server rejects that header, Claude Code reports the connection as failed instead of falling back to OAuth. Check that the token is valid for the MCP endpoint, or remove the header to use the OAuth flow.



  

#### Add the server that requires authentication

    If you already added the `sentry` server in the [MCP quickstart](<https://code.claude.com/docs/en/mcp-quickstart#connect-a-server-that-requires-sign-in>), skip this step: running `claude mcp add` again with the same server name at the same scope fails with `MCP server sentry already exists in local config`. Otherwise, run:

    ```bash theme={null}
    claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
    ```
  


  

#### Use the /mcp command within Claude Code

    In Claude Code, use the command:

    ```text wrap theme={null}
    /mcp
    ```

    Then follow the steps in your browser to log in.
  




**提示：**

  Tips:

  * Authentication tokens are stored securely and refreshed automatically
  * Use "Clear authentication" in the `/mcp` menu to revoke access
  * If your browser doesn't open automatically, copy the provided URL and open it manually
  * If the browser redirect fails with a connection error after authenticating, paste the full callback URL from your browser's address bar into the URL prompt that appears in Claude Code
  * OAuth authentication works with HTTP servers



### Authenticate from the command line

The `claude mcp login <name>` command runs a configured server's OAuth flow directly from your shell, so you don't need to open the `/mcp` panel inside a session. For a claude.ai connector, follow [Authorize a connector again from your shell](<https://code.claude.com/docs/en/remote-control#authorize-a-connector-again-from-your-shell>).

```bash theme={null}
claude mcp login sentry
```

To clear stored credentials later, run `claude mcp logout <name>`.

`claude mcp login` detects when no local browser is available, such as during an SSH session or on Linux without a display server, and prints the authorization URL instead of trying to open a browser. Open the URL on your local machine, then paste the full redirect URL from your browser's address bar back at the prompt. The command needs an interactive terminal for the paste step, so connect with `ssh -t`. Pass `--no-browser` to force the URL prompt even when a local browser is detected.

```bash theme={null}
claude mcp login sentry --no-browser
```

### Use a fixed OAuth callback port

Some MCP servers require a specific redirect URI registered in advance. By default, Claude Code picks a random available port for the OAuth callback. Use `--callback-port` to fix the port so it matches a pre-registered redirect URI of the form `http://localhost:PORT/callback`. If sign-in on Claude Code v2.1.229 fails with a redirect URI mismatch, see the version note under [Use pre-configured OAuth credentials](<#use-pre-configured-oauth-credentials>).

You can use `--callback-port` on its own (with dynamic client registration) or together with `--client-id` (with pre-configured credentials).

```bash theme={null}
# Fixed callback port with dynamic client registration
claude mcp add --transport http \
  --callback-port 8080 \
  my-server https://mcp.example.com/mcp
```

### Use pre-configured OAuth credentials

Some MCP servers don't support automatic OAuth setup via Dynamic Client Registration. If you see an error like "Incompatible auth server: does not support dynamic client registration," the server requires pre-configured credentials. Claude Code also supports servers that use a Client ID Metadata Document (CIMD) instead of Dynamic Client Registration, and discovers these automatically. If automatic discovery fails, register an OAuth app through the server's developer portal first, then provide the credentials when adding the server.



  

#### Register an OAuth app with the server

    Create an app through the server's developer portal and note your client ID and client secret.

    If the registration form asks for a redirect URI, pick any available port and enter `http://localhost:PORT/callback` with that port. You'll use the same port in the next step.

    In v2.1.229, Claude Code sent `http://127.0.0.1:PORT/callback` instead, and servers that exact-match the registered redirect URI rejected the sign-in with a redirect URI mismatch. Claude Code v2.1.231 restored the `localhost` form. To recover on v2.1.229, upgrade Claude Code, or temporarily add the `http://127.0.0.1:PORT/callback` form to the server's registered redirect URIs.
  


  

#### Add the server with your credentials

    The tabs cover both commands: `claude mcp add` takes your client ID and callback port as flags, and `claude mcp add-json` takes them in an `oauth` object. If you registered a redirect URI, set the callback port to the port in that URI.

    

      

#### claude mcp add

        Use `--client-id` to pass your app's client ID. The `--client-secret` flag prompts for the secret with masked input:

        ```bash theme={null}
        claude mcp add --transport http \
          --client-id your-client-id --client-secret --callback-port 8080 \
          my-server https://mcp.example.com/mcp
        ```
      


      

#### claude mcp add-json

        Include the `oauth` object in the JSON config and pass `--client-secret` as a separate flag:

        ```bash theme={null}
        claude mcp add-json my-server \
          '{"type":"http","url":"https://mcp.example.com/mcp","oauth":{"clientId":"your-client-id","callbackPort":8080}}' \
          --client-secret
        ```
      


      

#### claude mcp add-json (callback port only)

        To fix only the callback port and let Claude Code register the client automatically, set `callbackPort` on its own:

        ```bash theme={null}
        claude mcp add-json my-server \
          '{"type":"http","url":"https://mcp.example.com/mcp","oauth":{"callbackPort":8080}}'
        ```
      


      

#### CI / env var

        Set the secret via environment variable to skip the interactive prompt:

        ```bash theme={null}
        MCP_CLIENT_SECRET=your-secret claude mcp add --transport http \
          --client-id your-client-id --client-secret --callback-port 8080 \
          my-server https://mcp.example.com/mcp
        ```
      

    

  


  

#### Authenticate in Claude Code

    Run `/mcp` in Claude Code and follow the browser login flow.
  




**提示：**

  Tips:

  * The client secret is stored securely in your system keychain (macOS) or a credentials file, not in your config
  * You can set the client secret only when you add the server. When you authenticate with `claude mcp login` or from `/mcp`, Claude Code uses the stored secret and doesn't prompt for one or read `MCP_CLIENT_SECRET`
  * To add or change the secret later, remove the server with `claude mcp remove <name>`, then add it again with `--client-secret` and the same `--scope`
  * If the server uses a public OAuth client with no secret, use only `--client-id` without `--client-secret`
  * These flags only apply to HTTP and SSE transports. They have no effect on stdio servers
  * Use `claude mcp get <name>` to verify that OAuth credentials are configured for a server



### Override OAuth metadata discovery

Point Claude Code at a specific OAuth authorization server metadata URL to bypass the default discovery chain. Set `authServerMetadataUrl` when the MCP server's standard endpoints error, or when you want to route discovery through an internal proxy. By default, Claude Code first checks RFC 9728 Protected Resource Metadata at `/.well-known/oauth-protected-resource`, then falls back to RFC 8414 authorization server metadata at `/.well-known/oauth-authorization-server`.

Set `authServerMetadataUrl` in the `oauth` object of your server's config in `.mcp.json`:

```json theme={null}
{
  "mcpServers": {
    "my-server": {
      "type": "http",
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "authServerMetadataUrl": "https://auth.example.com/.well-known/openid-configuration"
      }
    }
  }
}
```

The URL must use `https://`. The metadata URL's `scopes_supported` overrides the scopes the upstream server advertises.

### Restrict OAuth scopes

Set `oauth.scopes` to pin the scopes Claude Code requests during the authorization flow. This is the supported way to restrict an MCP server to a security-team-approved subset when the upstream authorization server advertises more scopes than you want to grant. The value is a single space-separated string, matching the `scope` parameter format in RFC 6749 §3.3.

```json theme={null}
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp",
      "oauth": {
        "scopes": "channels:read chat:write search:read"
      }
    }
  }
}
```

`oauth.scopes` takes precedence over both `authServerMetadataUrl` and the scopes the server discovers at `/.well-known`. Leave it unset to let the MCP server determine the requested scope set.

As of v2.1.196, when `oauth.scopes` isn't set, Claude Code requests the scope provided by the server's `WWW-Authenticate` header or its protected resource metadata, and sends no `scope` parameter when neither provides one. It no longer requests the full `scopes_supported` catalog from automatically discovered authorization server metadata. Requesting that catalog made identity providers that advertise admin-only or template scopes reject the authorization request with an `invalid_scope` error. Metadata fetched from a configured `authServerMetadataUrl` still supplies its `scopes_supported` as the requested scopes.

If the authorization server advertises `offline_access` in `scopes_supported`, Claude Code appends it to the pinned scopes so the access token can be refreshed without a new browser sign-in.

If the server later returns a 403 `insufficient_scope` for a tool call, the call fails with a [`needs additional permissions`](<https://code.claude.com/docs/en/errors#mcp-server-needs-you-to-sign-in-again>) message that names the scope the server asks for. The server shows as needing authentication in `/mcp`.

If that scope isn't in your pinned `oauth.scopes`, add it, then run `/mcp` and authenticate the server again. Claude Code requests the pinned scopes rather than the scope the server named, so if you authenticate again without adding it, the token you get still lacks it.

### Use dynamic headers for custom authentication

If your MCP server uses an authentication scheme other than OAuth, such as Kerberos, short-lived tokens, or an internal SSO, use `headersHelper` to generate request headers at connection time. Claude Code runs the command and merges its output into the connection headers.

```json theme={null}
{
  "mcpServers": {
    "internal-api": {
      "type": "http",
      "url": "https://mcp.internal.example.com",
      "headersHelper": "/opt/bin/get-mcp-auth-headers.sh"
    }
  }
}
```

The command can also be inline:

```json theme={null}
{
  "mcpServers": {
    "internal-api": {
      "type": "http",
      "url": "https://mcp.internal.example.com",
      "headersHelper": "echo '{\"Authorization\": \"Bearer '\"$(get-token)\"'\"}'"
    }
  }
}
```

**Requirements:**

* The command must write a JSON object of string key-value pairs to stdout
* Claude Code runs the command in a shell and gives up on it after 10 seconds
* Claude Code picks the command's working directory by [where you configured the server](<#where-the-helper-runs>), so give the script as an absolute path or put it on `PATH`
* Dynamic headers override any static `headers` with the same name

Claude Code runs the helper fresh on each connection, at session start and on reconnect, once the [trust rule for project and local-scope servers](<#trust-a-folder-before-its-headershelper-runs>) lets it run. It doesn't cache the result, so your script is responsible for any token reuse.

If a tool call returns `401 Unauthorized` or `403 Forbidden`, Claude Code automatically re-runs the helper under the same rule, reconnects with the fresh headers, and retries the call once. Claude Code marks the server as needing authentication in `/mcp` only if that retry also fails.

When the helper's output includes an `Authorization` header, Claude Code uses that credential as the server's authentication and doesn't fall back to OAuth for the server.

If the server rejects the helper's credential while connecting, Claude Code reports the connection as failed rather than marking the server as needing authentication. Fix the credential your helper returns, then reconnect from `/mcp` to re-run the helper.

Claude Code sets these environment variables when executing the helper:

| Variable | Value |
| :- | :- |
| `CLAUDE_CODE_MCP_SERVER_NAME` | the name of the MCP server |
| `CLAUDE_CODE_MCP_SERVER_URL` | the URL of the MCP server |
| `CLAUDE_PLUGIN_ROOT` | the plugin's root directory. Set only when a [plugin](<https://code.claude.com/docs/en/plugins/components#mcp-servers>) provides the server |

Use these to write a single helper script that serves multiple MCP servers.

A plugin-provided `headersHelper` can't reference the plugin's [`${user_config.*}`](<https://code.claude.com/docs/en/plugins/manifest-reference#user-configuration>) values, because the command runs through a shell. Claude Code reports the server as misconfigured with an [error](<https://code.claude.com/docs/en/errors#plugin-command-references-user-config>) and doesn't substitute the value. Put `${user_config.KEY}` in the server's `headers` field instead, which isn't shell-parsed, or have the helper script read the value from a config file. Before v2.1.207, `headersHelper` substituted `${user_config.*}` values.

#### Where the helper runs

Claude Code picks the `headersHelper` command's working directory from the configuration that declares the server. A `cd` that Claude runs in Bash doesn't move it, and [`/cd`](<https://code.claude.com/docs/en/permissions#move-the-session-to-another-directory>) moves it only for servers that run from the session's primary working directory. Each row below gives the directory that a relative path in your `headersHelper` command resolves against.

| Where you configured the server | Working directory |
| :- | :- |
| A [plugin](<https://code.claude.com/docs/en/plugins/components#mcp-servers>) | The plugin's root directory |
| A project `.mcp.json` or a [local-scope](<#local-scope>) server | The project directory the server is declared in |
| An agent file in your project, a server from the SDK's `mcpServers` option or `setMcpServers()` method, or [`--mcp-config`](<https://code.claude.com/docs/en/cli-reference>) | The session's [primary working directory](<https://code.claude.com/docs/en/permissions#working-directories>) |
| [User scope](<#user-scope>), [managed MCP](<https://code.claude.com/docs/en/managed-mcp>), a [claude.ai connector](<#use-mcp-servers-from-claude-ai>), or an agent file from outside your project, including one from an `--add-dir` directory | Your configuration directory, `~/.claude` unless you set [`CLAUDE_CONFIG_DIR`](<https://code.claude.com/docs/en/env-vars>) |

Before v2.1.238, Claude Code also ran the helpers of user-scope, managed, and claude.ai connector servers, and of agent files from outside your project, from the directory you started it in.

#### Which variables a helper can read

A `headersHelper` that a repository or plugin supplies is a command you didn't write, so Claude Code runs it without the credential variables from your environment, such as `ANTHROPIC_API_KEY`. Where you configured the server decides whether this applies:

* **Removed**: a server in a project `.mcp.json` or in a plugin, and an inline server in an agent file from your project or from an `--add-dir` directory
* **Not removed**: a server at [user](<#user-scope>) or [local scope](<#local-scope>), in [managed MCP](<https://code.claude.com/docs/en/managed-mcp>), from a [claude.ai connector](<#use-mcp-servers-from-claude-ai>), or supplied by the SDK or [`--mcp-config`](<https://code.claude.com/docs/en/cli-reference>), and an inline server in an agent file from `~/.claude/agents/`, from managed settings, or passed with `--agents`

Apart from Git's `GIT_CONFIG_KEY_<n>` variables, Claude Code removes every variable from your environment whose name looks like a credential, such as a name with `TOKEN`, `SECRET`, `PASSWORD`, `KEY`, or `AUTH` in it in either letter case, so `ANTHROPIC_API_KEY` and `MY_REGISTRY_TOKEN` are both removed. Claude Code also removes a fixed list of credential variables whose names don't follow that pattern, such as `ANTHROPIC_CUSTOM_HEADERS`.

When this applies to your helper, have the script read its credential from a file or a credential store. If the server's `url` [carries the live value of one of these variables](<#environment-variable-expansion-in-mcp-json>), such as `MY_REGISTRY_TOKEN`, the `CLAUDE_CODE_MCP_SERVER_URL` value the helper receives has that part replaced with `REDACTED` as well.

#### Trust a folder before its headersHelper runs

Claude Code executes a `headersHelper` as an arbitrary shell command. For a server in a project `.mcp.json` or at [local scope](<#local-scope>), it runs the helper only after you accept the [trust dialog](<https://code.claude.com/docs/en/permissions#project-allow-rules-and-workspace-trust>) for the project directory the server is declared in. Before v2.1.238, a `claude -p` or SDK session ran these helpers without checking trust, and an interactive session ran them once you had trusted a parent folder.

* **Trust that doesn't count**: a parent folder's trust, and the automatic trust a `claude -p` or SDK session gets for [hooks in settings files](<https://code.claude.com/docs/en/permissions#what-runs-before-you-trust-a-folder>)
* **Until you trust the folder**: Claude Code connects the server with its static `headers` alone. In a `claude -p` or SDK session it also prints one [`headersHelper not run`](<https://code.claude.com/docs/en/errors#headershelper-not-run>) line per server to stderr, telling you how to grant the trust.
* **Trust without a dialog**: set `projects["<path>"].hasTrustDialogAccepted` to `true` in `~/.claude.json`. `<path>` is the folder [Project allow rules and workspace trust](<https://code.claude.com/docs/en/permissions#project-allow-rules-and-workspace-trust>) says Claude Code keys the trust on.

Claude Code applies the same rule to a server declared inline in an [agent file](<https://code.claude.com/docs/en/sub-agents#scope-mcp-servers-to-a-subagent>), checking where that agent file came from: your project, for a file in its `.claude/agents/` directory, or an `--add-dir` directory. Until you [trust that project or directory itself](<https://code.claude.com/docs/en/permissions#what-runs-before-you-trust-a-folder>), Claude Code doesn't load the server at all, so its helper never runs either.

## Add MCP servers from JSON configuration

If you have a JSON configuration for an MCP server, you can add it directly:



  

#### Add an MCP server from JSON

    ```bash theme={null}
    # Basic syntax
    claude mcp add-json <name> '<json>'

    # Example: Adding an HTTP server with JSON configuration
    claude mcp add-json weather-api '{"type":"http","url":"https://api.weather.com/mcp","headers":{"Authorization":"Bearer token"}}'

    # Example: Adding a stdio server with JSON configuration
    claude mcp add-json local-weather '{"type":"stdio","command":"/path/to/weather-cli","args":["--api-key","abc123"],"env":{"CACHE_DIR":"/tmp"}}'

    # Example: Adding an HTTP server with pre-configured OAuth credentials
    claude mcp add-json my-server '{"type":"http","url":"https://mcp.example.com/mcp","oauth":{"clientId":"your-client-id","callbackPort":8080}}' --client-secret
    ```
  


  

#### Verify the server was added

    ```bash theme={null}
    claude mcp get weather-api
    ```
  




**提示：**

  Tips:

  * Make sure the JSON is properly escaped in your shell
  * The JSON must conform to the MCP server configuration schema
  * You can use `--scope user` to add the server to your user configuration instead of the project-specific one



## Import MCP servers from Claude Desktop

If you've already configured MCP servers in Claude Desktop, you can import them:



  

#### Import servers from Claude Desktop

    ```bash theme={null}
    # Basic syntax
    claude mcp add-from-claude-desktop
    ```
  


  

#### Select which servers to import

    After running the command, you'll see an interactive dialog that allows you to select which servers you want to import.
  


  

#### Verify the servers were imported

    ```bash theme={null}
    claude mcp list
    ```
  


Server names added through `claude mcp` commands can contain only letters, numbers, hyphens, and underscores. Claude Desktop doesn't apply that restriction, so a Claude Desktop server whose name contains any other character, such as a space, can't be imported. The import reports each name it rejects and still imports the other servers you selected. Before v2.1.205, the first invalid name stopped the import and none of the selected servers were added.



**提示：**

  Tips:

  * This feature only works on macOS and Windows Subsystem for Linux (WSL)
  * It reads the Claude Desktop configuration file from its standard location on those platforms
  * Use the `--scope user` flag to add servers to your user configuration
  * Imported servers keep the same names as in Claude Desktop when the name contains only letters, numbers, hyphens, and underscores. Claude Code reports a server whose name contains any other character and skips it
  * If servers with the same names already exist, they get a numerical suffix (for example, `server_1`)



## Use MCP servers from claude.ai

If you've logged into Claude Code with a [claude.ai](<https://claude.ai>) account, MCP servers you've added in claude.ai, known as [connectors](<https://claude.com/docs/connectors>), are automatically available in Claude Code:



  

#### Configure MCP servers in claude.ai

    Add servers at [claude.ai/customize/connectors](<https://claude.ai/customize/connectors>). On Team and Enterprise plans, only admins can add servers.
  


  

#### Authenticate the MCP server

    Complete any required authentication steps in claude.ai.
  


  

#### View and manage servers in Claude Code

    In Claude Code, use the command:

    ```text wrap theme={null}
    /mcp
    ```

    Servers from claude.ai appear in the list with indicators showing they come from claude.ai.
  


Anthropic also provides some connectors itself, without you or an admin adding them. On accounts where [Claude Docs](<https://code.claude.com/docs/en/artifacts#write-a-document-with-claude-docs>) is available, `/mcp` lists `claude.ai Claude Docs` with no setup, and Claude uses it when you ask for a document meant for other people. To turn it off, add a `serverName` entry of `"claude.ai Claude Docs"` to `deniedMcpServers` or use the `/mcp` toggle, both described in [Disable claude.ai connectors](<#disable-claude-ai-connectors>).

Claude Code marks a connector `managed` in `/mcp` and in the [`/plugin`](<https://code.claude.com/docs/en/plugins/install>) manager when your organization manages its authentication in claude.ai. Managed status doesn't change how Claude Code connects to the connector or applies your organization's [tool controls](<#organization-controls-on-connector-tools>).

Connectors you have never signed in to are collapsed behind a `Show unused connectors` row at the end of the claude.ai section, so an organization-provisioned list doesn't fill the panel. Select the row to expand them. A connector you signed in to before stays visible even when it currently needs re-authentication.

Connectors from claude.ai are fetched only when your active [authentication method](<https://code.claude.com/docs/en/authentication#authentication-precedence>) is a claude.ai subscription login. They aren't loaded, even if you previously ran `/login`, when:

* `ANTHROPIC_API_KEY`, `ANTHROPIC_AUTH_TOKEN`, or `apiKeyHelper` is active
* A third-party provider such as Amazon Bedrock or Google Cloud's Agent Platform is active
* `ANTHROPIC_PROFILE`, the federation variables, or an active [Anthropic profile](<https://code.claude.com/docs/en/authentication#anthropic-profiles-and-federation-credentials>) supplies the credential
* `CLAUDE_CODE_OAUTH_TOKEN` holds a token from [`claude setup-token`](<https://code.claude.com/docs/en/authentication#generate-a-long-lived-token>), which can only make model requests

If `/mcp` doesn't list a connector you added, run `/status` to confirm which authentication method is active. Unset that environment variable, remove the `apiKeyHelper` setting, or [switch off the profile](<https://code.claude.com/docs/en/authentication#anthropic-profiles-and-federation-credentials>), then run `/login` to select your claude.ai account.

If a temporary network problem keeps the connector list from loading when your session starts, Claude Code retries the fetch up to three times in the background, and the connectors appear once a retry succeeds. If they still haven't appeared, restart Claude Code to fetch the list again.

If `/mcp` shows a connector as `session token rejected`, or its detail view shows [`claude.ai rejected the session token`](<https://code.claude.com/docs/en/errors#claude-ai-rejected-the-session-token>), claude.ai rejected the token from your Claude Code login. Authorizing the connector again doesn't clear this state, because the connector's own authorization in claude.ai isn't what was rejected. To clear it:

1. Run `/login` to sign in again.
2. Reconnect the connector from `/mcp`.

Before v2.1.222, Claude Code marked connectors as needing authentication instead, and authorizing them didn't resolve it.

A server you've added in Claude Code takes [precedence](<#scope-hierarchy-and-precedence>) over a claude.ai connector that points at the same URL. When this happens, `/mcp` lists the connector as hidden and shows how to remove the duplicate if you'd rather use the connector.

Some Anthropic-hosted connectors, such as Microsoft 365, Gmail, and Google Calendar, don't support local OAuth from Claude Code because the upstream identity provider only accepts the redirect URL that claude.ai registered. When a server you added with `claude mcp add` or in `.mcp.json` points at one of these hosts and you sign in to it from `/mcp` or with `claude mcp login`, Claude Code shows [`is Anthropic-hosted and doesn't support local OAuth`](<https://code.claude.com/docs/en/errors#anthropic-hosted-and-doesnt-support-local-oauth>), directing you to connect the service at [claude.ai/customize/connectors](<https://claude.ai/customize/connectors>) instead.

After you remove your entry with `claude mcp remove <name>` and connect the service on claude.ai, the connector appears in Claude Code automatically.

### How connectors reach Claude Code

Which settings govern a claude.ai connector depends on where your session runs, because only some sessions fetch connectors from claude.ai themselves. Each row below names how connectors arrive in one kind of session and what controls them there. The desktop app's [WSL sessions](<https://code.claude.com/docs/en/desktop-wsl#what-works-in-a-wsl-session>) have no row because connectors aren't available in them yet.

| Where the session runs | How connectors arrive | What governs them |
| :- | :- | :- |
| Terminal, [VS Code](<https://code.claude.com/docs/en/vs-code>), [JetBrains](<https://code.claude.com/docs/en/jetbrains>), and [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/claude-code-features>) sessions | Claude Code fetches them from claude.ai | The settings in this section and [managed MCP configuration](<https://code.claude.com/docs/en/managed-mcp>) |
| [Cloud sessions](<https://code.claude.com/docs/en/claude-code-on-the-web>) | The cloud host passes them in | Your claude.ai organization settings, plus the [allowlist and denylist](<https://code.claude.com/docs/en/managed-mcp#policy-based-control-with-allowlists-and-denylists>) settings that reach the session and any `managed-mcp.json` on the host that runs it |
| The [desktop app](<https://code.claude.com/docs/en/desktop>)'s local and SSH sessions | The desktop app delivers them in-process | `blocked` entries in your organization's [connector tool controls](<#organization-controls-on-connector-tools>) |

[`disableClaudeAiConnectors`](<#disable-claude-ai-connectors>), `ENABLE_CLAUDEAI_MCP_SERVERS`, and [`allowAllClaudeAiMcps`](<https://code.claude.com/docs/en/settings-reference#allowallclaudeaimcps>) act only on the first row, the connectors Claude Code fetches itself. The other two rows differ from it in these ways:

* **Cloud sessions**: `allowedMcpServers` and `deniedMcpServers` entries that reach the session, for example through [server-managed settings](<https://code.claude.com/docs/en/server-managed-settings>), filter the delivered connectors too. The session's proxy rewrites each connector's URL, so a `serverUrl` pattern written for the connector's own URL doesn't match it. To admit delivered connectors alongside a URL allowlist in a self-hosted environment, add the `serverUrl` entries listed under [Connector traffic leaves your network](<https://code.claude.com/docs/en/self-hosted-environments-deploy#connector-traffic-leaves-your-network>). Claude Code drops the delivered connectors when a `managed-mcp.json` is present on the host that runs the session, such as a [self-hosted runner host](<https://code.claude.com/docs/en/self-hosted-environments-configuration#mcp-servers>), whether or not you set `allowAllClaudeAiMcps`.
* **Desktop app local and SSH sessions**: the desktop app registers the connectors as in-process `type: "sdk"` servers, and no MCP setting or `managed-mcp.json` reaches them. A user keeps a connector out of their own sessions by disconnecting it at [claude.ai/customize/connectors](<https://claude.ai/customize/connectors>). An organization blocks a connector's [tools](<#organization-controls-on-connector-tools>) or turns off [Claude Code in the desktop app](<https://code.claude.com/docs/en/desktop#admin-console-controls>) entirely.

### Organization controls on connector tools

Your organization can set per-tool controls on [claude.ai connectors](<https://claude.com/docs/connectors>). Claude Code reads these settings at startup and enforces them locally, except in the desktop app's [local and SSH sessions](<#how-connectors-reach-claude-code>). There, the desktop app withholds `blocked` tools before it delivers a connector, and the `ask` setting doesn't reach Claude Code, so it applies the session's ordinary [permission rules](<https://code.claude.com/docs/en/permissions>) to those tools instead of prompting on every call. In sessions where Claude Code fetches connectors itself, run `/mcp` to see which setting applies to each tool on a connector.

* **Tool set to `ask`**: Claude Code prompts on every call with the reason `Your organization requires approval for this tool`. The prompt appears even in `acceptEdits`, `auto`, and `bypassPermissions` [permission modes](<https://code.claude.com/docs/en/permissions#permission-modes>), and never offers an option to remember your choice. [Allow rules](<https://code.claude.com/docs/en/permissions>) that match the tool don't skip the prompt either. In `dontAsk` mode, which never prompts, Claude Code denies the call instead.
* **Tool set to `blocked`**: Claude Code filters the tool out before Claude sees it, so it never appears in Claude's tool list. In sessions where Claude Code fetches connectors itself, the `/mcp` tool list still shows the tool, marked `disabled by your organization`.

The desktop app and claude.ai chat apply the same `blocked` setting, so Claude can't use a blocked tool there either, and you can't withhold a tool from the desktop app's sessions while keeping it available in chat. The desktop app skips a connector whose tools are all blocked.

### Disable claude.ai connectors

Claude Code applies [`disableClaudeAiConnectors`](<https://code.claude.com/docs/en/settings-reference#disableclaudeaiconnectors>) only to the connectors it [fetches itself](<#how-connectors-reach-claude-code>), not to the connectors a cloud host or the desktop app delivers. To turn off the connectors it fetches, set the setting to `true` in any settings scope:

```json theme={null}
{
  "disableClaudeAiConnectors": true
}
```

This setting uses any-source-true semantics: `true` in any settings source takes precedence. A checked-in project `.claude/settings.json` can opt a repository out of the connectors Claude Code fetches itself, but a project-level `false` can't re-enable connectors that a user- or policy-level `true` has disabled. Servers passed explicitly via `--mcp-config` are unaffected.

You can also set the `ENABLE_CLAUDEAI_MCP_SERVERS` environment variable to `false`, which has the same effect for the current shell session:

```bash theme={null}
ENABLE_CLAUDEAI_MCP_SERVERS=false claude
```

To block individual claude.ai connectors instead of all of them, add them to [`deniedMcpServers`](<https://code.claude.com/docs/en/managed-mcp>) by name or by URL pattern. For example, a `serverName` entry of `"claude.ai Slack"` blocks the Slack connector. You can also run `/mcp` to toggle any connector Claude Code fetches on or off for the current project only.

## Use Claude Code as an MCP server

You can use Claude Code itself as an MCP server that other applications can connect to:

```bash theme={null}
# Start Claude as a stdio MCP server
claude mcp serve
```

The command prints nothing when it starts. A stdio MCP server communicates over stdin and stdout, so a silent, blocked terminal means the server is running and waiting for a client to connect.

You can use this in Claude Desktop by adding this configuration to claude\_desktop\_config.json:

```json theme={null}
{
  "mcpServers": {
    "claude-code": {
      "type": "stdio",
      "command": "claude",
      "args": ["mcp", "serve"],
      "env": {}
    }
  }
}
```



**注意：**

  **Configuring the executable path**: the `command` field must reference the Claude Code executable. If the `claude` command is not in your system's PATH, you'll need to specify the full path to the executable.

  To find the full path:

  ```bash theme={null}
  which claude
  ```

  Then use the full path in your configuration:

  ```json theme={null}
  {
    "mcpServers": {
      "claude-code": {
        "type": "stdio",
        "command": "/full/path/to/claude",
        "args": ["mcp", "serve"],
        "env": {}
      }
    }
  }
  ```

  Without the correct executable path, you'll encounter errors like `spawn claude ENOENT`.




**提示：**

  Tips:

  * In Claude Desktop, try asking Claude to read files in a directory, make edits, and more.
  * This MCP server only exposes Claude Code's tools to your MCP client, so your own client is responsible for implementing user confirmation for individual tool calls.



## MCP output limits and warnings

When MCP tools produce large outputs, Claude Code helps manage the token usage to prevent overwhelming your conversation context:

* **Output warning threshold**: Claude Code displays a warning when any MCP tool output exceeds 10,000 tokens
* **Configurable limit**: you can adjust the maximum allowed MCP output tokens using the `MAX_MCP_OUTPUT_TOKENS` environment variable
* **Default limit**: the default maximum is 25,000 tokens
* **Scope**: the environment variable applies to tools that don't declare their own limit. Tools that set [`anthropic/maxResultSizeChars`](<#raise-the-limit-for-a-specific-tool>) use that value instead for text content, regardless of what `MAX_MCP_OUTPUT_TOKENS` is set to. Tools that return image data are still subject to `MAX_MCP_OUTPUT_TOKENS`
* **Over the limit**: when a successful result with no image content exceeds the token limit, Claude Code saves it to a file and replaces it in the conversation with a message that names the file path, so Claude reads the file when it needs the content. The file goes in the session's `tool-results` directory under [`~/.claude/projects/`](<https://code.claude.com/docs/en/claude-directory#cleaned-up-automatically>).
* **Response size from HTTP and SSE servers**: Claude Code stops reading a response from an [HTTP](<#option-1-add-a-remote-http-server>) or [SSE](<#option-2-add-a-remote-sse-server>) server once one JSON response body, or one event of an event stream, passes 16 MB after decompression. The request that response answers fails. If you maintain the server, return less data per response to stay under the limit, for example by paginating results

A call that Claude Code has [moved to a background task](<#automatic-backgrounding-of-long-tool-calls>) reports its result through the task notification. Two more limits apply to a call that completes in the foreground:

* **Character limit for text results**: for a tool that doesn't declare [`anthropic/maxResultSizeChars`](<#raise-the-limit-for-a-specific-tool>), Claude Code saves a successful result with no image content to a file once it's longer than 50,000 characters, whatever its token count. Setting `MAX_MCP_OUTPUT_TOKENS` doesn't change this threshold
* **Error results**: when a tool returns a result marked `isError: true`, Claude receives the result's text as the tool's error message. Error text longer than about 11,000 characters keeps only its first 5,000 and last 5,000 characters, with a marker between them that says how many characters were removed

To change the token limit, set `MAX_MCP_OUTPUT_TOKENS` in your shell before starting Claude Code:

```bash theme={null}
export MAX_MCP_OUTPUT_TOKENS=50000
claude
```

### Raise the limit for a specific tool

If you're building an MCP server, you can allow individual tools to return results larger than the default persist-to-disk threshold of 50,000 characters by setting `_meta["anthropic/maxResultSizeChars"]` in the tool's `tools/list` response entry. Claude Code raises that tool's threshold to the annotated value, up to a hard ceiling of 500,000 characters.

This is useful for tools that return inherently large but necessary outputs, such as database schemas or full file trees. Without the annotation, successful results that exceed the default threshold are persisted to disk and replaced with a file reference in the conversation.

```json theme={null}
{
  "name": "get_schema",
  "description": "Returns the full database schema",
  "_meta": {
    "anthropic/maxResultSizeChars": 200000
  }
}
```

The annotation applies independently of `MAX_MCP_OUTPUT_TOKENS` for text content, so users don't need to raise the environment variable for tools that declare it. Tools that return image data are still subject to the token limit.

### Images in tool results

When an MCP tool returns a PNG, JPEG, GIF, or WebP image, Claude sees the image inline in the conversation. The inline copy may be scaled down or compressed to fit the model's image size limits. Claude Code also saves the original bytes to a file in the session's `tool-results` directory under [`~/.claude/projects/`](<https://code.claude.com/docs/en/claude-directory#cleaned-up-automatically>) and gives Claude the path. Claude can then crop, convert, or reuse the full-resolution file with tools such as Bash.

If you disable session persistence with [`--no-session-persistence`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) or [`CLAUDE_CODE_SKIP_PROMPT_HISTORY`](<https://code.claude.com/docs/en/env-vars>), Claude Code writes no image file and Claude receives only the inline copy.

Saving MCP image results to a file requires Claude Code v2.1.283 or later.

## Tool input schemas with a root-level combinator

Some MCP servers declare a tool's input schema as a JSON Schema union, with `anyOf`, `oneOf`, or `allOf` at the top level of the schema. The Claude API doesn't accept those keywords at the schema root. It does accept combinators nested inside `properties`, which Claude Code sends unchanged.

Tools with a root-level combinator stay available. Before sending the tool to the API, Claude Code flattens the schema into a single object and prepends a sentence to the tool's description that tells Claude which parameter groups belong together:

* `allOf`: properties from every branch are merged, and each branch's `required` list still applies
* `anyOf` and `oneOf`: properties from every branch are merged, and each branch's `required` list is described in the tool description instead of enforced by the schema

Your server receives whichever arguments Claude chose, so keep validating the combination server-side.

When Claude Code can't produce a schema the API accepts, or on a deployment that doesn't receive the remote configuration that enables the rewrite, it skips that one tool, records the reason in the server's log, and leaves the server's other tools available.

## Tools with invalid input schemas

The Claude API checks every tool's input schema in a request and rejects the whole request when any one schema fails, so a single MCP tool with a malformed schema would make every request that includes it fail with a 400 error. Claude Code runs two of the API's checks itself when it loads a server's tools and excludes each tool that would fail them, so the server's other tools keep working:

* Top-level property names must be 1 to 64 characters long and use only ASCII letters and digits, `_`, `.`, and `-`
* The schema must be valid against the JSON Schema draft 2020-12 meta-schema. Claude Code applies this check to schemas that declare no `$schema` and schemas that declare draft 2020-12. A schema that declares any other dialect skips this check, though the property-name check above still applies

When Claude Code excludes a tool, it records the reason in the server's log and tells Claude which tools it excluded and why, so you can ask Claude why a tool is missing. If you fix the schema on the server, the tool comes back the next time Claude Code loads the server's tools.

Claude Code turns the exclusion on through a feature flag it fetches from Anthropic. On a [deployment where flag fetching is off](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>), or on a machine whose flags have never arrived, such as an air-gapped machine, Claude Code still runs the checks and records in the server's log which tool would be rejected, but sends the tool's schema to the API anyway. The API rejects a request that includes that schema with [a 400 error naming the tool by its position](<https://code.claude.com/docs/en/errors#tool-input-schema-is-invalid>). Before v2.1.216, no deployment ran these checks.

The [root-level combinator handling](<#tool-input-schemas-with-a-root-level-combinator>) is separate and keeps its own behavior when flag fetching is off or the flags have never arrived.

## Require approval for a specific tool

If you're building an MCP server, you can mark a tool as requiring explicit approval on every call by setting `_meta["anthropic/requiresUserInteraction"]` to `true` in the tool's `tools/list` response entry. The value must be the JSON Boolean `true`; any other value is ignored.

Claude Code shows that tool's permission prompt on every call, even in `acceptEdits`, `auto`, and `bypassPermissions` [permission modes](<https://code.claude.com/docs/en/permissions#permission-modes>), and doesn't offer a "don't ask again" option for it. [Allow rules](<https://code.claude.com/docs/en/permissions#permission-rule-syntax>) that match the tool don't skip the prompt either. In `dontAsk` mode, which never prompts, Claude Code denies the call instead.

The prompt has to reach a person. In non-interactive mode with [`--permission-prompt-tool`](<https://code.claude.com/docs/en/cli-reference#cli-flags>), an `allow` result from the prompt tool for a flagged tool is converted to a deny with the message `MCP tool requires user interaction; not supported via --permission-prompt-tool`. The Agent SDK's [`canUseTool` callback](<https://code.claude.com/docs/en/agent-sdk/permissions>) does receive these calls and can approve them, because your SDK application is expected to show them to a user.

Use this for tools whose permission prompt is itself the point, such as a consent or access-grant step where auto-approval would mean no human ever agreed. Other tools from the same server keep their normal permission behavior.

The following `tools/list` entry marks one tool as always requiring approval.

```json theme={null}
{
  "name": "grant_access",
  "description": "Requests access to a protected resource",
  "_meta": {
    "anthropic/requiresUserInteraction": true
  }
}
```

Some surfaces, such as [Remote Control](<https://code.claude.com/docs/en/remote-control>) and applications built on the [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>), normally let you approve tool calls with one tap. For a tool marked with this annotation, Claude Code withholds the one-tap action and shows the tool's full permission prompt instead, so approval still comes from a person answering the prompt rather than a tap.

Claude Code withholds one-tap approval the same way for any permission request that only the terminal dialog can render in full, such as one that carries a safety warning or an always-allow option the remote surface can't show. You answer that request in the terminal dialog rather than from Remote Control. Requires Claude Code v2.1.214 or later.

## Respond to MCP elicitation requests

MCP servers can request structured input from you mid-task using elicitation. When a server needs information it can't get on its own, Claude Code displays an interactive dialog and passes your response back to the server. No configuration is required on your side: elicitation dialogs appear automatically when a server requests them.

Servers can request input in two ways:

* **Form mode**: Claude Code shows a dialog with form fields defined by the server (for example, a username and password prompt). Fill in the fields and submit.
* **URL mode**: Claude Code asks whether to open a link in your browser. Servers use this mode for a flow that finishes outside the terminal, such as sign-in.

In URL mode, Claude Code passes the URL as a command-line argument to your system's URL handler, and caps how long that argument can be. When the URL, once escaped for the command line, is over that cap, you can only decline the request. Every character that needs escaping, such as `%` or `&`, counts four times toward the cap: its own character plus three escape characters. A URL with none of them reaches the cap at about 8,000 characters. A URL built largely of percent-escapes, where every third character is a `%`, reaches it at roughly 4,000.

To auto-respond to elicitation requests without showing a dialog, use the [`Elicitation` hook](<https://code.claude.com/docs/en/hooks#elicitation>).

If you're building an MCP server that uses elicitation, see the [MCP elicitation specification](<https://modelcontextprotocol.io/docs/learn/client-concepts#elicitation>) for protocol details and schema examples.

On connections that use [protocol revision 2026-07-28](<#mcp-client-runtimes>), Claude Code declares `elicitation: {form: {}, url: {}}` in its client capabilities, so a server there can request either mode through the protocol's standard elicitation request.

## Use MCP resources

MCP servers can expose resources that you can reference using @ mentions, similar to how you reference files.

### Reference MCP resources



  

#### List available resources

    Type `@` in your prompt to see available resources from all connected MCP servers. Resources appear alongside files in the autocomplete menu.
  


  

#### Reference a specific resource

    Use the format `@server:protocol://resource/path` to reference a resource:

    ```text wrap theme={null}
    Can you analyze @github:issue://123 and suggest a fix?
    ```

    ```text wrap theme={null}
    Please review the API documentation at @docs:file://api/authentication
    ```
  


  

#### Multiple resource references

    You can reference multiple resources in a single prompt:

    ```text wrap theme={null}
    Compare @postgres:schema://users with @docs:file://database/user-model
    ```
  




**提示：**

  Tips:

  * Resources are automatically fetched and included as attachments when referenced
  * Resource paths are fuzzy-searchable in the @ mention autocomplete
  * Claude Code automatically provides tools to list and read MCP resources when servers support them
  * Resources can contain any type of content that the MCP server provides (text, JSON, structured data, etc.)



MCP Apps UI resources are entries with a `ui://` URI or the `text/html;profile=mcp-app` media type: pages for a host application to render rather than content for Claude to read. They don't appear in the `@` suggestions or in the resource list tool's results, and a server that offers only UI resources shows an empty resource list. Reading a UI resource by its URI still works.

## Scale with MCP tool search

Tool search keeps MCP context usage low by deferring tool definitions until Claude needs them. Only tool names and server instructions load at session start, so adding more MCP servers has minimal impact on your context window. Claude Code doesn't impose a fixed per-server tool cap; the practical limit is your context window budget.



**注释：**

  Tool search isn't supported on Microsoft Foundry [deployments hosted on Azure](<https://platform.claude.com/docs/en/build-with-claude/claude-in-microsoft-foundry#hosting-options>), which reject it server-side: Claude Code detects the rejection and loads MCP tools upfront for that deployment instead. [`ENABLE_TOOL_SEARCH`](<#configure-tool-search>) can't override this, since the rejection comes from the deployment itself.


<span id="for-mcp-server-authors" />

### Tool search for MCP server authors

If you're building an MCP server, the server instructions field becomes more useful with tool search enabled. Server instructions help Claude understand when to search for your tools, similar to how [skills](<https://code.claude.com/docs/en/skills>) work.

Add clear, descriptive server instructions that explain:

* What category of tasks your tools handle
* When Claude should search for your tools
* Key capabilities your server provides

Claude Code truncates each tool description and each server's instructions at 2,048 characters by default. Keep them concise, and put critical details near the start.

To change the limit for every MCP server in your session, set [`CLAUDE_CODE_MAX_MCP_DESCRIPTION_LENGTH`](<https://code.claude.com/docs/en/env-vars#variables>) to a number of characters. This variable requires Claude Code v2.1.280 or later.

<h4 id="per-tool-alwaysload">
  Mark a tool to load upfront or stay deferred
</h4>

To control how one of your server's tools loads, set `"anthropic/alwaysLoad"` in that tool's `_meta` object. The person who adds your server to Claude Code can also set [`alwaysLoad`](<#exempt-a-server-from-deferral>) for the whole server in their configuration, and their setting can override yours:

| Your tool's value | What happens |
| :- | :- |
| `true` | The tool loads upfront. Startup doesn't wait for your server because of this value. The person configuring your server can still [defer all of its tools](<#defer-a-servers-tools>) |
| `false` | The tool stays deferred when their configuration sets `"alwaysLoad": true`. This applies when your server is passed with [`--mcp-config`](<https://code.claude.com/docs/en/cli-reference#cli-flags>), supplied by an [Agent SDK application](<https://code.claude.com/docs/en/agent-sdk/mcp#in-code>), or provided by a [plugin](<#plugin-provided-mcp-servers>). On other servers the tool loads upfront. Requires Claude Code v2.1.285 or later |

The following `tools/list` entry asks for one tool to load upfront:

```json theme={null}
{
  "name": "search_tickets",
  "description": "Searches the ticket tracker by keyword",
  "_meta": {
    "anthropic/alwaysLoad": true
  }
}
```

### Configure tool search

Tool search is enabled by default: MCP tools are deferred and discovered on demand. Claude Code disables it when `ANTHROPIC_BASE_URL` points to a non-first-party host, since most proxies don't forward `tool_reference` blocks. Set `ENABLE_TOOL_SEARCH` explicitly to override that fallback.

Setting [`CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS`](<https://code.claude.com/docs/en/env-vars>) keeps tool search off. You can't override it by setting `ENABLE_TOOL_SEARCH` yourself. Your organization can keep tool search on through [managed settings](<https://code.claude.com/docs/en/managed-settings>), on Claude Code v2.1.227 or later. [Disable pre-release capabilities](<https://code.claude.com/docs/en/llm-gateway-protocol#disable-pre-release-capabilities>) covers where the override applies and what the variable strips.

Tool search requires a model that supports `tool_reference` blocks: Claude Sonnet 4.5, Claude Haiku 4.5, Claude Opus 4.5, and later models. See [model compatibility in the API docs](<https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool#model-compatibility>) for the current list.

On Google Cloud's Agent Platform, Claude Code decides by model generation:

* **Claude Opus 4.5, Sonnet 4.5, Haiku 4.5, and later**: tool search is on by default, the same as on the Anthropic API.
* **Earlier Agent Platform models**: Claude Code loads all MCP tools upfront, because their serving stacks reject the required beta header. `ENABLE_TOOL_SEARCH=true` doesn't override this.

Before v2.1.221, Claude Code disabled tool search for all models on Google Cloud's Agent Platform unless you set `ENABLE_TOOL_SEARCH=true`.

Control tool search behavior with the `ENABLE_TOOL_SEARCH` environment variable:

| Value | Behavior |
| :- | :- |
| (unset) | All MCP tools deferred and loaded on demand. Falls back to loading upfront on Google Cloud's Agent Platform models earlier than the Claude 4.5 generation, when `ANTHROPIC_BASE_URL` is a non-first-party host, or on a Microsoft Foundry deployment hosted on Azure |
| `true` | All MCP tools deferred, except on a Microsoft Foundry deployment hosted on Azure, where the server-side rejection still forces upfront loading, and on Google Cloud's Agent Platform models earlier than the Claude 4.5 generation, where Claude Code keeps loading tools upfront. Claude Code sends the beta header through proxies, and requests fail on proxies that don't support `tool_reference` blocks |
| `auto` | Threshold mode: Claude Code loads the tools it would otherwise defer upfront while their definitions total less than 10% of the context window, and defers all of them once the definitions reach 10% |
| `auto:N` | Threshold mode with a custom percentage, where `N` is 0-100. For example, `auto:5` for 5% |
| `false` | All MCP tools loaded upfront, no deferral |

```bash theme={null}
# Use a custom 5% threshold
ENABLE_TOOL_SEARCH=auto:5 claude

# Disable tool search entirely
ENABLE_TOOL_SEARCH=false claude
```

Or set the value in your [settings.json `env` field](<https://code.claude.com/docs/en/settings-reference#env>).

You can also disable the `ToolSearch` tool specifically:

```json theme={null}
{
  "permissions": {
    "deny": ["ToolSearch"]
  }
}
```

### Exempt a server from deferral

If a server's tools should always be visible to Claude without a search step, set `alwaysLoad` to `true` in that server's configuration. The server's tools then load into context regardless of the `ENABLE_TOOL_SEARCH` setting. Use this for a small number of tools that Claude needs on every turn, since each upfront tool consumes context that would otherwise be available for your conversation.

The following `.mcp.json` entry exempts one HTTP server while leaving other servers deferred:

```json theme={null}
{
  "mcpServers": {
    "core-tools": {
      "type": "http",
      "url": "https://mcp.example.com/mcp",
      "alwaysLoad": true
    }
  }
}
```

The `alwaysLoad` field is available on all server types.

Setting `alwaysLoad: true` also makes startup wait for the server's tools, capped at the standard 5-second connect timeout, since they must be present when the first prompt is built. A remote server with a valid [`cached` entry](<#server-status-detail>) supplies its tools from the cache without connecting, so it doesn't hold startup. Other servers connect in the background by default; set [`MCP_CONNECTION_NONBLOCKING=0`](<https://code.claude.com/docs/en/env-vars>) to make startup wait for them too.

<h3 id="defer-a-servers-tools">
  Defer a server's tools
</h3>

To keep all of a server's tools behind tool search, set `"alwaysLoad": false` in that server's entry in your MCP configuration. That includes tools that the server's author has [marked to load upfront](<#per-tool-alwaysload>). If you leave `alwaysLoad` out, those marked tools load upfront. Requires Claude Code v2.1.287 or later.

## Use MCP prompts as commands

MCP servers can expose prompts that become available as commands in Claude Code.

Prompts from a server named `anthropic-skills` don't appear, because Claude Code [reserves that name](<https://code.claude.com/docs/en/skills#names-reserved-for-synced-skills>) for skills synced from claude.ai. The server's tools still work. Rename the server in your MCP configuration to list its prompts.

### Execute MCP prompts



  

#### Discover available prompts

    Type `/` to see the commands available to you, including those from MCP servers. Claude Code lists each MCP prompt as `/servername:promptname (MCP)`. Typing `/mcp__servername__promptname` also runs it.
  


  

#### Execute a prompt without arguments

    ```text wrap theme={null}
    /mcp__github__list_prs
    ```
  


  

#### Execute a prompt with arguments

    Many prompts accept arguments. Pass them space-separated after the command. Claude Code splits the arguments on whitespace, so each argument is a single token:

    ```text wrap theme={null}
    /mcp__github__pr_review 456
    ```

    ```text wrap theme={null}
    /mcp__jira__create_issue login-bug high
    ```
  




**提示：**

  Tips:

  * MCP prompts are dynamically discovered from connected servers
  * Arguments are parsed based on the prompt's defined parameters
  * Prompt results are injected directly into the conversation
  * In the `/mcp__servername__promptname` form, Claude Code replaces any character in the server name outside `A-Z`, `a-z`, `0-9`, `_`, and `-` with `_`, and uses the prompt name as the server declares it



## Managed MCP configuration

For organizations that need centralized control over which MCP servers users can connect to, see [Managed MCP configuration](<https://code.claude.com/docs/en/managed-mcp>). It covers deploying a fixed server set with `managed-mcp.json`, providing servers to every user with `managedMcpServers`, restricting servers with `allowedMcpServers` and `deniedMcpServers`, and what users see when a server is blocked.


</details>
