# 通过 ACP 将 OpenCode 接入编辑器

产品指南。说明 opencode acp 子进程和标准输入输出通信，给出 Zed、JetBrains、Avante.nvim 和 CodeCompanion.nvim 配置及支持的功能范围。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-08-24](<https://github.com/anomalyco/opencode/commit/105b398c2a9ff2f16eaae409836e1dbc4d37671a>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：适用于兼容 ACP 的客户端，配置以归档版本为准。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/acp.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/通过 ACP 将 OpenCode 接入编辑器.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: ACP Support
description: Use OpenCode in any ACP-compatible editor.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode支持[Agent客户端协议](<https://agentclientprotocol.com>)或(ACP)，允许您直接在兼容的编辑器和IDE中使用它。

**提示：**

有关支持 ACP 的编辑器和工具列表，请查看 [ACP 进度报告 ](<https://zed.dev/blog/acp-progress-report#available-now>)。

ACP 是一种开放协议，标准化代码编辑器和 AI 编码 agents 之间的通信。

---

## 配置

要通过 ACP 使用 OpenCode，请将编辑器配置为运行 `opencode acp` 命令。

该命令将 OpenCode 作为 ACP- 兼容子进程启动，通过 stdio 通过 JSON-RPC 与编辑器通信。

以下是支持 ACP 的流行编辑器的示例。

---

### 泽德

通过在命令面板中运行 `zed: acp registry`，从 [Zed ACP 注册表 ](<https://zed.dev/docs/ai/external-agents#registry>) 安装 OpenCode。

要使用自定义的 OpenCode 可执行文件，请将其添加到您的 [Zed](<https://zed.dev>) 配置 (`~/.config/zed/settings.json`)：

**代码示例：** `~/.config/zed/settings.json`

```json
{
  "agent_servers": {
    "OpenCode": {
      "type": "custom",
      "command": "opencode",
      "args": ["acp"]
    }
  }
}
```

要打开它，请使用 ** 命令面板 ** 中的 `agent: new thread` 操作。

您还可以通过编辑`keymap.json`来绑定键盘快捷键：

**代码示例：** `keymap.json`

```json
[
  {
    "bindings": {
      "cmd-alt-o": [
        "agent::NewExternalAgentThread",
        {
          "agent": {
            "custom": {
              "name": "OpenCode",
              "command": {
                "command": "opencode",
                "args": ["acp"]
              }
            }
          }
        }
      ]
    }
  }
]
```

---

### JetBrains IDE

添加到您的 [JetBrains IDE](<https://www.jetbrains.com/>) acp.json 根据 [文档](<https://www.jetbrains.com/help/ai-assistant/acp.html>):

**代码示例：** `acp.json`

```json
{
  "agent_servers": {
    "OpenCode": {
      "command": "/absolute/path/bin/opencode",
      "args": ["acp"]
    }
  }
}
```

要打开它，请使用 AI 聊天 agent 选择器中的新“OpenCode”agent。

---

### 前卫

添加到您的 [Avante.nvim](<https://github.com/yetone/avante.nvim>) 配置：

```lua
{
  acp_providers = {
    ["opencode"] = {
      command = "opencode",
      args = { "acp" }
    }
  }
}
```

如果需要传递环境变量：

```lua
{
  acp_providers = {
    ["opencode"] = {
      command = "opencode",
      args = { "acp" },
      env = {
        OPENCODE_API_KEY = os.getenv("OPENCODE_API_KEY")
      }
    }
  }
}
```

---

### CodeCompanion.nvim

要将 OpenCode 用作 [CodeCompanion.nvim](<https://github.com/olimorris/codecompanion.nvim>) 中的 ACP agent，请将以下内容添加到 Neovim 配置中：

```lua
require("codecompanion").setup({
  interactions = {
    chat = {
      adapter = {
        name = "opencode",
        model = "claude-sonnet-4",
      },
    },
  },
})
```

此配置将 CodeCompanion 设置为使用 OpenCode 作为 ACP agent 进行聊天。

如果您需要传递环境变量（如 `OPENCODE_API_KEY`），请参阅 CodeCompanion.nvim 文档中的 [配置适配器：环境变量 ](<https://codecompanion.olimorris.dev/getting-started#setting-an-api-key>) 以获取完整详细信息。

## 支持

OpenCode 通过 ACP 的工作方式与在终端中的工作方式相同。支持所有功能：

**注释：**

目前不支持某些内置斜线命令，例如 `/undo` 和 `/redo`。

- 内置工具（文件操作、终端命令等）
- 自定义工具和斜线命令
- 在您的 OpenCode 配置中配置的 MCP 服务器
- 来自 `AGENTS.md` 的项目特定规则
- 自定义格式化程序和 linter
- Agents及权限系统


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode supports the [Agent Client Protocol](<https://agentclientprotocol.com>) or (ACP), allowing you to use it directly in compatible editors and IDEs.

**提示：**

For a list of editors and tools that support ACP, check out the [ACP progress report](<https://zed.dev/blog/acp-progress-report#available-now>).

ACP is an open protocol that standardizes communication between code editors and AI coding agents.

---

## Configure

To use OpenCode via ACP, configure your editor to run the `opencode acp` command.

The command starts OpenCode as an ACP-compatible subprocess that communicates with your editor over JSON-RPC via stdio.

Below are examples for popular editors that support ACP.

---

### Zed

Install OpenCode from the [Zed ACP Registry](<https://zed.dev/docs/ai/external-agents#registry>) by running `zed: acp registry` in the Command Palette.

To use a custom OpenCode executable instead, add it to your [Zed](<https://zed.dev>) configuration (`~/.config/zed/settings.json`):

**代码示例：** `~/.config/zed/settings.json`

```json
{
  "agent_servers": {
    "OpenCode": {
      "type": "custom",
      "command": "opencode",
      "args": ["acp"]
    }
  }
}
```

To open it, use the `agent: new thread` action in the **Command Palette**.

You can also bind a keyboard shortcut by editing your `keymap.json`:

**代码示例：** `keymap.json`

```json
[
  {
    "bindings": {
      "cmd-alt-o": [
        "agent::NewExternalAgentThread",
        {
          "agent": {
            "custom": {
              "name": "OpenCode",
              "command": {
                "command": "opencode",
                "args": ["acp"]
              }
            }
          }
        }
      ]
    }
  }
]
```

---

### JetBrains IDEs

Add to your [JetBrains IDE](<https://www.jetbrains.com/>) acp.json according to the [documentation](<https://www.jetbrains.com/help/ai-assistant/acp.html>):

**代码示例：** `acp.json`

```json
{
  "agent_servers": {
    "OpenCode": {
      "command": "/absolute/path/bin/opencode",
      "args": ["acp"]
    }
  }
}
```

To open it, use the new 'OpenCode' agent in the AI Chat agent selector.

---

### Avante.nvim

Add to your [Avante.nvim](<https://github.com/yetone/avante.nvim>) configuration:

```lua
{
  acp_providers = {
    ["opencode"] = {
      command = "opencode",
      args = { "acp" }
    }
  }
}
```

If you need to pass environment variables:

```lua
{
  acp_providers = {
    ["opencode"] = {
      command = "opencode",
      args = { "acp" },
      env = {
        OPENCODE_API_KEY = os.getenv("OPENCODE_API_KEY")
      }
    }
  }
}
```

---

### CodeCompanion.nvim

To use OpenCode as an ACP agent in [CodeCompanion.nvim](<https://github.com/olimorris/codecompanion.nvim>), add the following to your Neovim config:

```lua
require("codecompanion").setup({
  interactions = {
    chat = {
      adapter = {
        name = "opencode",
        model = "claude-sonnet-4",
      },
    },
  },
})
```

This config sets up CodeCompanion to use OpenCode as the ACP agent for chat.

If you need to pass environment variables (like `OPENCODE_API_KEY`), refer to [Configuring Adapters: Environment Variables](<https://codecompanion.olimorris.dev/getting-started#setting-an-api-key>) in the CodeCompanion.nvim documentation for full details.

## Support

OpenCode works the same via ACP as it does in the terminal. All features are supported:

**注释：**

Some built-in slash commands like `/undo` and `/redo` are currently unsupported.

- Built-in tools (file operations, terminal commands, etc.)
- Custom tools and slash commands
- MCP servers configured in your OpenCode config
- Project-specific rules from `AGENTS.md`
- Custom formatters and linters
- Agents and permissions system


</details>
