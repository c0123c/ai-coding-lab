# OpenCode MCP 连接、OAuth 与工具范围

产品指南。展示本地、远端服务和 OAuth，说明认证排查、按 Agent 管理工具及 Sentry、Context7 等配置；提醒过多工具会增加上下文。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-06-11](<https://github.com/anomalyco/opencode/commit/7e7ad377364d2d6c9f63d79cb8481298abaf0756>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：第三方服务的授权和工具范围需单独核对。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/mcp-servers.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode MCP 连接、OAuth 与工具范围.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: MCP servers
description: Add local and remote MCP tools.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

您可以使用 _ 模型上下文协议 _ 或 MCP 将外部工具添加到 OpenCode。 OpenCode支持本地和远程服务器。

添加后，MCP 工具与内置工具一起自动可供 LLM 使用。

---

#### 注意事项

当您使用 MCP 服务器时，它会添加到上下文中。如果您有很多工具，这会很快增加。因此，我们建议您谨慎使用 MCP 服务器。

**提示：**

MCP 服务器会添加到您的上下文中，因此您需要小心启用哪些服务器。

某些 MCP 服务器（例如 GitHub MCP 服务器）往往会添加大量令牌，并且很容易超出上下文限制。

---

## 启用

您可以在[OpenCode配置](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>)中的`mcp`下定义MCP服务器。为每个 MCP 添加唯一的名称。当提示LLM时，您可以通过名称引用MCP。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "name-of-mcp-server": {
      // ...
      "enabled": true,
    },
    "name-of-other-mcp-server": {
      // ...
    },
  },
}
```

您还可以通过将 `enabled` 设置为 `false` 来禁用服务器。如果您想暂时禁用服务器而不将其从配置中删除，这非常有用。

---

### 覆盖远程默认值

组织可以通过其 `.well-known/opencode` 端点提供默认 MCP 服务器。这些服务器可能默认被禁用，允许用户选择他们需要的服务器。

要从组织的远程配置启用特定服务器，请使用 `enabled: true` 将其添加到本地配置：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "jira": {
      "type": "remote",
      "url": "https://jira.example.com/mcp",
      "enabled": true
    }
  }
}
```

您的本地配置值会覆盖远程默认值。有关更多详细信息，请参阅[配置优先级](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#precedence-order>)。

---

## 本地

使用 `type` 将本地 MCP 服务器添加到 MCP 对象内的 `"local"`。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-local-mcp-server": {
      "type": "local",
      // Or ["bun", "x", "my-mcp-command"]
      "command": ["npx", "-y", "my-mcp-command"],
      "enabled": true,
      "environment": {
        "MY_ENV_VAR": "my_env_var_value",
      },
    },
  },
}
```

该命令是本地MCP服务器的启动方式。您还可以传入环境变量列表。

例如，以下是添加测试 [`@modelcontextprotocol/server-everything`](<https://www.npmjs.com/package/@modelcontextprotocol/server-everything>) MCP 服务器的方法。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "mcp_everything": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-everything"],
    },
  },
}
```

要使用它，我可以将 `use the mcp_everything tool` 添加到我的提示中。

```txt
use the mcp_everything tool to add the number 3 and 4
```

---

#### 选项

以下是配置本地 MCP 服务器的所有选项。

|选项|类型|必填|描述|
| ------------- | ------- | -------- | ---------------------------------------------------------------------------------------- |
| `type`        |字符串| Y        |MCP服务器连接的类型，必须是`"local"`。|
| `command`     |数组| Y        |运行 MCP 服务器的命令和参数。|
| `cwd`         |字符串|          |MCP 服务器进程的工作目录。从工作区解析相对路径。|
| `environment` |对象|          |运行服务器时设置的环境变量。|
| `enabled`     |布尔值|          |启动时启用或禁用 MCP 服务器。|
| `timeout`     |数量|          |从 MCP 服务器获取工具的超时（以毫秒为单位）。默认为 5000（5 秒）。|

---

## 远程

通过将`type`设置为`"remote"`来添加远程MCP服务器。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-remote-mcp": {
      "type": "remote",
      "url": "https://my-mcp-server.com",
      "enabled": true,
      "headers": {
        "Authorization": "Bearer MY_API_KEY"
      }
    }
  }
}
```

`url` 是远程 MCP 服务器的 URL，使用 `headers` 选项，您可以传入标头列表。

---

#### 选项

|选项|类型|必填|描述|
| --------- | ------- | -------- | ----------------------------------------------------------------------------------- |
| `type`    |字符串| Y        |MCP服务器连接的类型，必须是`"remote"`。|
| `url`     |字符串| Y        |远程 MCP 服务器的 URL。|
| `enabled` |布尔值|          |启动时启用或禁用 MCP 服务器。|
| `headers` |对象|          |随请求一起发送的标头。|
| `oauth`   |对象|          |OAuth 身份验证配置。请参阅下面的 [OAuth](<#开放认证>) 部分。|
| `timeout` |数量|          |从 MCP 服务器获取工具的超时（以毫秒为单位）。默认为 5000（5 秒）。|

---

## 开放认证

OpenCode 自动处理远程 MCP 服务器的 OAuth 身份验证。当服务器需要身份验证时，OpenCode将：

1. 检测 401 响应并启动 OAuth 流程
2. 如果服务器支持，请使用 **动态客户端注册 (RFC 7591)**
3. 安全地存储令牌以供将来的请求

---

### 自动

对于大多数支持 OAuth 的 MCP 服务器，不需要特殊配置。只需配置远程服务器：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-oauth-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp"
    }
  }
}
```

如果服务器需要身份验证，OpenCode在您第一次尝试使用时会提示您进行身份验证。如果没有，您可以通过[手动触发流程](<#正在验证>)和`opencode mcp auth <server-name>`。

---

### 预登记

如果您有来自 MCP 服务器提供商的客户端凭据，您可以配置它们：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-oauth-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "clientId": "{env:MY_MCP_CLIENT_ID}",
        "clientSecret": "{env:MY_MCP_CLIENT_SECRET}",
        "scope": "tools:read tools:execute"
      }
    }
  }
}
```

---

### 正在验证

您可以手动触发身份验证或管理凭据。

使用特定的 MCP 服务器进行身份验证：

```bash
opencode mcp auth my-oauth-server
```

列出所有MCP服务器及其身份验证状态：

```bash
opencode mcp list
```

删除存储的凭据：

```bash
opencode mcp logout my-oauth-server
```

`mcp auth`命令将打开您的浏览器进行授权。您授权后，【OpenCode】会将代币安全地存储在【`~/.local/share/opencode/mcp-auth.json`】中。

---

#### 禁用 OAuth

如果要禁用服务器的自动 OAuth（例如，对于使用 API 密钥的服务器），请将 `oauth` 设置为 `false`：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-api-key-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp",
      "oauth": false,
      "headers": {
        "Authorization": "Bearer {env:MY_API_KEY}"
      }
    }
  }
}
```

---

#### OAuth 选项

|选项|类型|描述|
| -------------- | --------------- | -------------------------------------------------------------------------------- |
| `oauth`        |对象 \| false|OAuth 配置对象，或 `false` 以禁用 OAuth 自动检测。|
| `clientId`     |字符串|OAuth 客户端 ID。如果未提供，将尝试动态客户端注册。|
| `clientSecret` |字符串|OAuth 客户端密钥（如果授权服务器需要）。|
| `scope`        |字符串|授权期间请求的 OAuth 范围。|

#### 调试

如果远程 MCP 服务器无法进行身份验证，您可以通过以下方式诊断问题：

```bash
# View auth status for all OAuth-capable servers
opencode mcp auth list

# Debug connection and OAuth flow for a specific server
opencode mcp debug my-oauth-server
```

`mcp debug` 命令显示当前身份验证状态，测试 HTTP 连接性，并尝试 OAuth 发现流程。

---

## 管理

您的 MCP 可作为 OpenCode 中的工具以及内置工具使用。因此，您可以像任何其他工具一样通过 OpenCode 配置来管理它们。

---

### 全球

这意味着您可以全局启用或禁用它们。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-mcp-foo": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command-foo"]
    },
    "my-mcp-bar": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command-bar"]
    }
  },
  "tools": {
    "my-mcp-foo": false
  }
}
```

我们还可以使用 glob 模式来禁用所有匹配的 MCP。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-mcp-foo": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command-foo"]
    },
    "my-mcp-bar": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command-bar"]
    }
  },
  "tools": {
    "my-mcp*": false
  }
}
```

这里我们使用 glob 模式 `my-mcp*` 来禁用所有 MCP。

---

### 根据 agent

如果您有大量 MCP 服务器，您可能只想针对每个 agent 启用它们并全局禁用它们。为此：

1. 全局禁用它作为工具。
2. 在您的 [agent 配置 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#tools>) 中，启用 MCP 服务器作为工具。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-mcp": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command"],
      "enabled": true
    }
  },
  "tools": {
    "my-mcp*": false
  },
  "agent": {
    "my-agent": {
      "tools": {
        "my-mcp*": true
      }
    }
  }
}
```

---

#### 全局模式

glob 模式使用简单的正则表达式 globbing 模式：

- `*` 匹配零个或多个任意字符（例如，`"my-mcp*"` 匹配 `my-mcp_search`、`my-mcp_list` 等）
- `?` 恰好匹配一个字符
- 所有其他字符均按字面意思匹配

**注释：**

MCP 服务器工具以服务器名称作为前缀注册，因此要禁用服务器的所有工具，只需使用：

```
"mymcpservername_*": false
```

---

## 示例

以下是一些常见 MCP 服务器的示例。如果您想记录其他服务器，您可以提交PR。

---

### 哨兵

添加 [Sentry MCP 服务器 ](<https://mcp.sentry.dev>) 以与您的 Sentry 项目和问题进行交互。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "sentry": {
      "type": "remote",
      "url": "https://mcp.sentry.dev/mcp",
      "oauth": {}
    }
  }
}
```

添加配置后，使用Sentry进行身份验证：

```bash
opencode mcp auth sentry
```

这将打开一个浏览器窗口以完成 OAuth 流程并将 OpenCode 连接到您的 Sentry 帐户。

通过身份验证后，您可以在提示中使用 Sentry 工具来查询问题、项目和错误数据。

```txt
Show me the latest unresolved issues in my project. use sentry
```

---

### 背景7

添加 [Context7 MCP 服务器 ](<https://github.com/upstash/context7>) 以搜索文档。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp"
    }
  }
}
```

如果您已注册免费帐户，则可以使用您的 API 密钥并获得更高的速率限制。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "{env:CONTEXT7_API_KEY}"
      }
    }
  }
}
```

这里我们假设您设置了 `CONTEXT7_API_KEY` 环境变量。

将 `use context7` 添加到提示中以使用 Context7 MCP 服务器。

```txt
Configure a Cloudflare Worker script to cache JSON API responses for five minutes. use context7
```

或者，您可以将类似的内容添加到您的 [AGENTS.md](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode AGENTS.md、规则作用域与外部指令.md>) 中。

**代码示例：** `AGENTS.md`

```md
When you need to search docs, use `context7` tools.
```

---

### Vercel 的 Grep

添加 [Grep by Vercel](<https://grep.app>) MCP 服务器以搜索 GitHub 上的代码片段。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "gh_grep": {
      "type": "remote",
      "url": "https://mcp.grep.app"
    }
  }
}
```

由于我们将 MCP 服务器命名为 `gh_grep`，因此您可以将 `use the gh_grep tool` 添加到提示中以获取 agent 来使用它。

```txt
What's the right way to set a custom domain in an SST Astro component? use the gh_grep tool
```

或者，您可以将类似的内容添加到您的 [AGENTS.md](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode AGENTS.md、规则作用域与外部指令.md>) 中。

**代码示例：** `AGENTS.md`

```md
If you are unsure how to do something, use `gh_grep` to search code examples from GitHub.
```


## 原文对照

<details>
<summary>展开完整原文</summary>

You can add external tools to OpenCode using the _Model Context Protocol_, or MCP. OpenCode supports both local and remote servers.

Once added, MCP tools are automatically available to the LLM alongside built-in tools.

---

#### Caveats

When you use an MCP server, it adds to the context. This can quickly add up if you have a lot of tools. So we recommend being careful with which MCP servers you use.

**提示：**

MCP servers add to your context, so you want to be careful with which ones you enable.

Certain MCP servers, like the GitHub MCP server, tend to add a lot of tokens and can easily exceed the context limit.

---

## Enable

You can define MCP servers in your [OpenCode Config](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>) under `mcp`. Add each MCP with a unique name. You can refer to that MCP by name when prompting the LLM.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "name-of-mcp-server": {
      // ...
      "enabled": true,
    },
    "name-of-other-mcp-server": {
      // ...
    },
  },
}
```

You can also disable a server by setting `enabled` to `false`. This is useful if you want to temporarily disable a server without removing it from your config.

---

### Overriding remote defaults

Organizations can provide default MCP servers via their `.well-known/opencode` endpoint. These servers may be disabled by default, allowing users to opt-in to the ones they need.

To enable a specific server from your organization's remote config, add it to your local config with `enabled: true`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "jira": {
      "type": "remote",
      "url": "https://jira.example.com/mcp",
      "enabled": true
    }
  }
}
```

Your local config values override the remote defaults. See [config precedence](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#precedence-order>) for more details.

---

## Local

Add local MCP servers using `type` to `"local"` within the MCP object.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-local-mcp-server": {
      "type": "local",
      // Or ["bun", "x", "my-mcp-command"]
      "command": ["npx", "-y", "my-mcp-command"],
      "enabled": true,
      "environment": {
        "MY_ENV_VAR": "my_env_var_value",
      },
    },
  },
}
```

The command is how the local MCP server is started. You can also pass in a list of environment variables as well.

For example, here's how you can add the test [`@modelcontextprotocol/server-everything`](<https://www.npmjs.com/package/@modelcontextprotocol/server-everything>) MCP server.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "mcp_everything": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-everything"],
    },
  },
}
```

And to use it I can add `use the mcp_everything tool` to my prompts.

```txt
use the mcp_everything tool to add the number 3 and 4
```

---

#### Options

Here are all the options for configuring a local MCP server.

| Option        | Type    | Required | Description                                                                              |
| ------------- | ------- | -------- | ---------------------------------------------------------------------------------------- |
| `type`        | String  | Y        | Type of MCP server connection, must be `"local"`.                                        |
| `command`     | Array   | Y        | Command and arguments to run the MCP server.                                             |
| `cwd`         | String  |          | Working directory for the MCP server process. Relative paths resolve from the workspace. |
| `environment` | Object  |          | Environment variables to set when running the server.                                    |
| `enabled`     | Boolean |          | Enable or disable the MCP server on startup.                                             |
| `timeout`     | Number  |          | Timeout in ms for fetching tools from the MCP server. Defaults to 5000 (5 seconds).      |

---

## Remote

Add remote MCP servers by setting `type` to `"remote"`.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-remote-mcp": {
      "type": "remote",
      "url": "https://my-mcp-server.com",
      "enabled": true,
      "headers": {
        "Authorization": "Bearer MY_API_KEY"
      }
    }
  }
}
```

The `url` is the URL of the remote MCP server and with the `headers` option you can pass in a list of headers.

---

#### Options

| Option    | Type    | Required | Description                                                                         |
| --------- | ------- | -------- | ----------------------------------------------------------------------------------- |
| `type`    | String  | Y        | Type of MCP server connection, must be `"remote"`.                                  |
| `url`     | String  | Y        | URL of the remote MCP server.                                                       |
| `enabled` | Boolean |          | Enable or disable the MCP server on startup.                                        |
| `headers` | Object  |          | Headers to send with the request.                                                   |
| `oauth`   | Object  |          | OAuth authentication configuration. See [OAuth](<#oauth>) section below.              |
| `timeout` | Number  |          | Timeout in ms for fetching tools from the MCP server. Defaults to 5000 (5 seconds). |

---

## OAuth

OpenCode automatically handles OAuth authentication for remote MCP servers. When a server requires authentication, OpenCode will:

1. Detect the 401 response and initiate the OAuth flow
2. Use **Dynamic Client Registration (RFC 7591)** if supported by the server
3. Store tokens securely for future requests

---

### Automatic

For most OAuth-enabled MCP servers, no special configuration is needed. Just configure the remote server:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-oauth-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp"
    }
  }
}
```

If the server requires authentication, OpenCode will prompt you to authenticate when you first try to use it. If not, you can [manually trigger the flow](<#authenticating>) with `opencode mcp auth <server-name>`.

---

### Pre-registered

If you have client credentials from the MCP server provider, you can configure them:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-oauth-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "clientId": "{env:MY_MCP_CLIENT_ID}",
        "clientSecret": "{env:MY_MCP_CLIENT_SECRET}",
        "scope": "tools:read tools:execute"
      }
    }
  }
}
```

---

### Authenticating

You can manually trigger authentication or manage credentials.

Authenticate with a specific MCP server:

```bash
opencode mcp auth my-oauth-server
```

List all MCP servers and their auth status:

```bash
opencode mcp list
```

Remove stored credentials:

```bash
opencode mcp logout my-oauth-server
```

The `mcp auth` command will open your browser for authorization. After you authorize, OpenCode will store the tokens securely in `~/.local/share/opencode/mcp-auth.json`.

---

#### Disabling OAuth

If you want to disable automatic OAuth for a server (e.g., for servers that use API keys instead), set `oauth` to `false`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-api-key-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp",
      "oauth": false,
      "headers": {
        "Authorization": "Bearer {env:MY_API_KEY}"
      }
    }
  }
}
```

---

#### OAuth Options

| Option         | Type            | Description                                                                      |
| -------------- | --------------- | -------------------------------------------------------------------------------- |
| `oauth`        | Object \| false | OAuth config object, or `false` to disable OAuth auto-detection.                 |
| `clientId`     | String          | OAuth client ID. If not provided, dynamic client registration will be attempted. |
| `clientSecret` | String          | OAuth client secret, if required by the authorization server.                    |
| `scope`        | String          | OAuth scopes to request during authorization.                                    |

#### Debugging

If a remote MCP server is failing to authenticate, you can diagnose issues with:

```bash
# View auth status for all OAuth-capable servers
opencode mcp auth list

# Debug connection and OAuth flow for a specific server
opencode mcp debug my-oauth-server
```

The `mcp debug` command shows the current auth status, tests HTTP connectivity, and attempts the OAuth discovery flow.

---

## Manage

Your MCPs are available as tools in OpenCode, alongside built-in tools. So you can manage them through the OpenCode config like any other tool.

---

### Global

This means that you can enable or disable them globally.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-mcp-foo": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command-foo"]
    },
    "my-mcp-bar": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command-bar"]
    }
  },
  "tools": {
    "my-mcp-foo": false
  }
}
```

We can also use a glob pattern to disable all matching MCPs.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-mcp-foo": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command-foo"]
    },
    "my-mcp-bar": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command-bar"]
    }
  },
  "tools": {
    "my-mcp*": false
  }
}
```

Here we are using the glob pattern `my-mcp*` to disable all MCPs.

---

### Per agent

If you have a large number of MCP servers you may want to only enable them per agent and disable them globally. To do this:

1. Disable it as a tool globally.
2. In your [agent config](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#tools>), enable the MCP server as a tool.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-mcp": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command"],
      "enabled": true
    }
  },
  "tools": {
    "my-mcp*": false
  },
  "agent": {
    "my-agent": {
      "tools": {
        "my-mcp*": true
      }
    }
  }
}
```

---

#### Glob patterns

The glob pattern uses simple regex globbing patterns:

- `*` matches zero or more of any character (e.g., `"my-mcp*"` matches `my-mcp_search`, `my-mcp_list`, etc.)
- `?` matches exactly one character
- All other characters match literally

**注释：**

MCP server tools are registered with server name as prefix, so to disable all tools for a server simply use:

```
"mymcpservername_*": false
```

---

## Examples

Below are examples of some common MCP servers. You can submit a PR if you want to document other servers.

---

### Sentry

Add the [Sentry MCP server](<https://mcp.sentry.dev>) to interact with your Sentry projects and issues.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "sentry": {
      "type": "remote",
      "url": "https://mcp.sentry.dev/mcp",
      "oauth": {}
    }
  }
}
```

After adding the configuration, authenticate with Sentry:

```bash
opencode mcp auth sentry
```

This will open a browser window to complete the OAuth flow and connect OpenCode to your Sentry account.

Once authenticated, you can use Sentry tools in your prompts to query issues, projects, and error data.

```txt
Show me the latest unresolved issues in my project. use sentry
```

---

### Context7

Add the [Context7 MCP server](<https://github.com/upstash/context7>) to search through docs.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp"
    }
  }
}
```

If you have signed up for a free account, you can use your API key and get higher rate-limits.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "{env:CONTEXT7_API_KEY}"
      }
    }
  }
}
```

Here we are assuming that you have the `CONTEXT7_API_KEY` environment variable set.

Add `use context7` to your prompts to use Context7 MCP server.

```txt
Configure a Cloudflare Worker script to cache JSON API responses for five minutes. use context7
```

Alternatively, you can add something like this to your [AGENTS.md](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode AGENTS.md、规则作用域与外部指令.md>).

**代码示例：** `AGENTS.md`

```md
When you need to search docs, use `context7` tools.
```

---

### Grep by Vercel

Add the [Grep by Vercel](<https://grep.app>) MCP server to search through code snippets on GitHub.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "gh_grep": {
      "type": "remote",
      "url": "https://mcp.grep.app"
    }
  }
}
```

Since we named our MCP server `gh_grep`, you can add `use the gh_grep tool` to your prompts to get the agent to use it.

```txt
What's the right way to set a custom domain in an SST Astro component? use the gh_grep tool
```

Alternatively, you can add something like this to your [AGENTS.md](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode AGENTS.md、规则作用域与外部指令.md>).

**代码示例：** `AGENTS.md`

```md
If you are unsure how to do something, use `gh_grep` to search code examples from GitHub.
```


</details>
