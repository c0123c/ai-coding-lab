# 通过 ACP 在其他编辑器中使用 Cline

产品指南。展示 Zed、JetBrains、Neovim、Emacs 的客户端配置、命令路径、功能范围、自动审批和环境变量，帮助把 Cline CLI 接入支持 ACP 的编辑器。

- 来源：Cline
- 资料类型：产品指南
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-08-05](<https://github.com/cline/cline/commit/2f58bbe4eda148b4e5575a3983c231b40de9c947>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：需要兼容 ACP 的客户端和可用 CLI，权限行为按该界面判断。

[原文发布页](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/usage/acp.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/通过 ACP 在其他编辑器中使用 Cline.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "ACP"
sidebarTitle: "ACP"
description: "Use Cline as the coding agent in Zed, JetBrains IDEs, Neovim, Emacs, and any other ACP-capable client."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

Cline CLI 采用 [Agent 客户端协议 (ACP)](<https://agentclientprotocol.com>)，这是一种开放标准，可让编辑器和其他工具驱动基于终端的编码 agents。任何具有 ACP- 功能的客户端都可以使用 Cline 作为其编码 agent，无需专用扩展 - 与您在终端中使用的相同 agent，嵌入到您已经使用的工具中。

## 先决条件

- 已安装 Cline CLI（通过 `npm i -g cline` 安装）
- 凭据是可选的 - 大多数客户端允许您在第一个会话开始时登录，并且 `cline auth` 保存的凭据会自动重用

## 它是如何运作的

ACP 模式以 `--acp` 标志启动：

```bash
cline --acp
```

您不需要自己运行它 - 客户端启动命令并通过 stdio 与 Cline 对话。配置客户端来运行它，然后从其 agent UI 启动一个线程。

**提示：**

如果客户端无法生成 agent，它可能不会继承您的 shell 的 `PATH`。使用`which cline`中的绝对路径作为命令。

## 客户

ACP 得到不断发展的编辑器、IDE 和其他工具生态系统的支持。最受欢迎的客户的设置如下； [ACP 客户端目录](<https://agentclientprotocol.com/get-started/clients>) 维护完整列表。

### 泽德

在 Zed 的 `settings.json` 中添加 Cline 作为自定义 agent：

```json
{
  "agent_servers": {
    "Cline": {
      "type": "custom",
      "command": "cline",
      "args": ["--acp"],
      "env": {}
    }
  }
}
```

打开Agent面板，启动一个新线程，然后选择**Cline**。

### JetBrains IDE

AI JetBrains IDE（IntelliJ、IDEA、PyCharm、WebStorm 等）中的助手支持 [ACP agents](<https://www.jetbrains.com/help/ai-assistant/acp.html>)。在 AI 聊天工具窗口中，选择 **添加自定义 Agent** — 这将创建 `~/.jetbrains/acp.json` — 然后添加 Cline：

```json
{
  "agent_servers": {
    "Cline": {
      "command": "cline",
      "args": ["--acp"],
      "env": {}
    }
  }
}
```

Cline 随后出现在 AI 聊天 agent 选择器中。

### 尼奥维姆

[CodeCompanion](<https://codecompanion.olimorris.dev/configuration/adapters-acp>) 插件附带一个运行 `cline --acp` 的内置 `cline_cli` ACP 适配器。将其设置为您的聊天适配器：

```lua
require("codecompanion").setup({
  strategies = {
    chat = { adapter = "cline_cli" },
  },
})
```

其他 Neovim ACP 客户端，例如 [avante.nvim](<https://github.com/yetone/avante.nvim>) 和 [agentic.nvim](<https://github.com/carlos-algms/agentic.nvim>)，也可以通过 `cline --acp` 启动 Cline。

### Emacs

[agent-shell](<https://github.com/xenodium/agent-shell>) 从本机 Emacs 缓冲区驱动 ACP agents。根据 agent-shell 文档配置运行 `cline --acp` 的自定义 agent。

### 其他客户

任何使用 ACP 的东西都可以以同样的方式运行 Cline — 将其指向 `cline --acp`。 [客户端目录](<https://agentclientprotocol.com/get-started/clients>)包括Obsidian等笔记应用程序、marimo等笔记本、移动客户端以及Slack、Discord和Telegram的聊天桥。

## ACP 有何作用

- **从客户端登录** — 如果未找到凭据，客户端会提示您使用 Cline、ClinePass 或 ChatGPT 订阅登录。 `cline auth` 保存的凭证会自动重复使用。
- **Plan/Act 模式** — 从客户端的模式选择器在 [ 计划和行动 ](<https://docs.cline.bot/core-workflows/plan-and-act>) 之间切换。
- **模型和提供商选择** — 从活动提供商的目录中选择任何模型，或从客户端的模型选择器中切换提供商。
- **权限提示** — 文件编辑和命令通过客户端权限批准UI。默认情况下，不会自动批准任何工具，但您可以在每个会话或启动时[自动批准所有工具](<#自动批准工具>)。
- **会话恢复** - 会话被保留，因此支持会话加载的客户端可以在重新启动后恢复线程。
- **图像** — 提示可以包含具有视觉功能的模型的图像。
- **组织切换** — Cline团队帐户可以在个人和组织计费之间切换。

## 自动批准工具

默认情况下，每个文件编辑和命令都会经过客户端的权限UI。让 Cline 无需询问即可运行工具：

- **每个会话** - 会话在客户端会话设置中公开**自动批准工具**切换（支持ACP配置选项的客户端，如Zed，将其与模型和模式选择器一起渲染）。翻转它会在下一次工具调用时生效。
- **启动时** — 在客户端的 agent 配置中传递 `--auto-approve true` 以启动每个会话并自动批准：

```json
{
  "agent_servers": {
    "Cline": {
      "type": "custom",
      "command": "cline",
      "args": ["--acp", "--auto-approve", "true"],
      "env": {}
    }
  }
}
```

**注意：**

启用自动批准后，Cline 无需询问即可编辑文件并运行命令。仅在您愿意进行无人值守更改的工作区中启用它。

## 环境变量

在客户端的 agent 服务器配置中的 `env` 下设置这些以预配置 agent：

|变量|效果|
|---|---|
| `CLINE_API_KEY` |使用 API 密钥进行身份验证，而不是交互式登录|
| `CLINE_PROVIDER` |固定提供商（禁用客户端中的提供商切换）|
| `CLINE_MODEL` |新会话的默认模型|

## 下一步

- [CLI 概述](<https://docs.cline.bot/usage/cli-overview>)
- [CLI 参考](<https://docs.cline.bot/cli/cli-reference>)


## 原文对照

<details>
<summary>展开完整原文</summary>

Cline CLI speaks the [Agent Client Protocol (ACP)](<https://agentclientprotocol.com>), an open standard that lets editors and other tools drive terminal-based coding agents. Any ACP-capable client can use Cline as its coding agent without a dedicated extension — the same agent you use in the terminal, embedded in the tool you're already working in.

## Prerequisites

- Cline CLI installed (install via `npm i -g cline`)
- Credentials are optional up front — most clients let you sign in when the first session starts, and credentials saved by `cline auth` are reused automatically

## How it works

ACP mode is started with the `--acp` flag:

```bash
cline --acp
```

You don't run this yourself — the client launches the command and talks to Cline over stdio. Configure the client to run it, then start a thread from its agent UI.

**提示：**

If a client can't spawn the agent, it may not inherit your shell's `PATH`. Use the absolute path from `which cline` as the command.

## Clients

ACP is supported by a growing ecosystem of editors, IDEs, and other tools. Setup for the most popular clients is below; the [ACP client directory](<https://agentclientprotocol.com/get-started/clients>) maintains the full list.

### Zed

Add Cline as a custom agent in Zed's `settings.json`:

```json
{
  "agent_servers": {
    "Cline": {
      "type": "custom",
      "command": "cline",
      "args": ["--acp"],
      "env": {}
    }
  }
}
```

Open the Agent Panel, start a new thread, and select **Cline**.

### JetBrains IDEs

AI Assistant in JetBrains IDEs (IntelliJ IDEA, PyCharm, WebStorm, and others) supports [ACP agents](<https://www.jetbrains.com/help/ai-assistant/acp.html>). In the AI Chat tool window, select **Add Custom Agent** — this creates `~/.jetbrains/acp.json` — then add Cline:

```json
{
  "agent_servers": {
    "Cline": {
      "command": "cline",
      "args": ["--acp"],
      "env": {}
    }
  }
}
```

Cline then appears in the AI Chat agent selector.

### Neovim

The [CodeCompanion](<https://codecompanion.olimorris.dev/configuration/adapters-acp>) plugin ships a built-in `cline_cli` ACP adapter that runs `cline --acp`. Set it as your chat adapter:

```lua
require("codecompanion").setup({
  strategies = {
    chat = { adapter = "cline_cli" },
  },
})
```

Other Neovim ACP clients, such as [avante.nvim](<https://github.com/yetone/avante.nvim>) and [agentic.nvim](<https://github.com/carlos-algms/agentic.nvim>), can also launch Cline via `cline --acp`.

### Emacs

[agent-shell](<https://github.com/xenodium/agent-shell>) drives ACP agents from a native Emacs buffer. Configure a custom agent that runs `cline --acp` per the agent-shell docs.

### Other clients

Anything that speaks ACP can run Cline the same way — point it at `cline --acp`. The [client directory](<https://agentclientprotocol.com/get-started/clients>) includes note-taking apps like Obsidian, notebooks like marimo, mobile clients, and chat bridges for Slack, Discord, and Telegram.

## What works over ACP

- **Sign-in from the client** — if no credentials are found, the client prompts you to sign in with Cline, ClinePass, or a ChatGPT subscription. Credentials saved by `cline auth` are reused automatically.
- **Plan/Act modes** — switch between [Plan and Act](<https://docs.cline.bot/core-workflows/plan-and-act>) from the client's mode selector.
- **Model and provider selection** — pick any model from the active provider's catalog, or switch providers, from the client's model picker.
- **Permission prompts** — file edits and commands are approved through the client's permission UI. Nothing is auto-approved by default, but you can [auto-approve all tools](<#auto-approving-tools>) per session or at launch.
- **Session resume** — conversations are persisted, so clients that support session loading can restore a thread after a restart.
- **Images** — prompts can include images for vision-capable models.
- **Organization switching** — Cline team accounts can switch between personal and organization billing.

## Auto-approving tools

By default every file edit and command goes through the client's permission UI. To let Cline run tools without asking:

- **Per session** — sessions expose an **Auto-approve tools** toggle in the client's session settings (clients that support ACP config options, like Zed, render it alongside the model and mode pickers). Flipping it takes effect on the next tool call.
- **At launch** — pass `--auto-approve true` in the client's agent config to start every session with auto-approval on:

```json
{
  "agent_servers": {
    "Cline": {
      "type": "custom",
      "command": "cline",
      "args": ["--acp", "--auto-approve", "true"],
      "env": {}
    }
  }
}
```

**注意：**

With auto-approval on, Cline edits files and runs commands without asking. Only enable it in workspaces where you're comfortable with unattended changes.

## Environment variables

Set these under `env` in the client's agent server config to preconfigure the agent:

| Variable | Effect |
|---|---|
| `CLINE_API_KEY` | Authenticate with an API key instead of interactive sign-in |
| `CLINE_PROVIDER` | Pin the provider (disables provider switching in the client) |
| `CLINE_MODEL` | Default model for new sessions |

## Next Steps

- [CLI Overview](<https://docs.cline.bot/usage/cli-overview>)
- [CLI Reference](<https://docs.cline.bot/cli/cli-reference>)


</details>
