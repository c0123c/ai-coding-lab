# 在编辑器中接入 Mistral Vibe ACP

按 Zed、JetBrains、Xcode 和 Neovim 介绍 Agent Client Protocol 配置，区分不同版本的连接方式。

- 来源：Mistral／Vibe
- 资料类型：存档文档
- 存档版本：`4ae5c5955da8`。
- 文档时间：源码提交（存档版）：[2026-10-06](<https://github.com/mistralai/mistral-vibe/commit/376f6a33413a3eec9b3795b0c0e004066c47b5c1>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/mistralai/mistral-vibe/blob/main/docs/acp-setup.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Mistral/Vibe/docs/acp-setup.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# ACP 设置

Mistral Vibe 可在支持 [Agent 客户端协议 ](<https://agentclientprotocol.com/overview/clients>) 的文本编辑器和 IDE 中使用。 Mistral Vibe 包含 `vibe-acp` 工具。
使用 API 键设置 `vibe` 后，即可在编辑器中使用 `vibe-acp`。以下是一些支持 ACP 的编辑器的设置说明。

## 泽德

对于在 Zed 中使用，我们建议使用 [Mistral Vibe Zed ACP agent](<https://zed.dev/acp/agent/mistral-vibe>)。或者，您可以按如下方式设置本地安装：

1. Go 到 `~/.config/zed/settings.json`，并在 `agent_servers` JSON 对象下添加以下键值对以调用 `vibe-acp` 命令。这是片段：

```json
{
   "agent_servers": {
      "Mistral Vibe": {
         "type": "custom",
         "command": "vibe-acp",
         "args": [],
         "env": {}
      }
   }
}
```

2. 在 `Agent Panel` 视图中，选择 `Mistral Vibe` agent 并开始对话。

## JetBrains IDE

要在 JetBrains IDE 中使用 Mistral Vibe，您需要安装 [Jetbrains AI Assistant 扩展 ](<https://plugins.jetbrains.com/plugin/22282-jetbrains-ai-assistant>)

### 版本 2025.3 或更高版本

1. 打开设置，然后转到`Tools > AI Assistant > Agents`。搜索`Mistral Vibe`，点击安装

2. 打开【AI】助手。您应该能够从 agent 选择器中选择 Mistral Vibe（如果您尚未通过身份验证，系统将提示您这样做）。

### 旧方法

1. 将以下代码段添加到您的JetBrains IDE acp.json（[文档](<https://www.jetbrains.com/help/ai-assistant/acp.html>)）中：

```json
{
  "agent_servers": {
    "Mistral Vibe": {
      "command": "vibe-acp",
    }
  }
}
```

1. 在 AI 聊天 agent 选择器中，选择新的 Mistral Vibe agent 并开始对话。

## Xcode

1. 找到 `vibe-acp` 可执行文件的绝对路径：

```shell
command -v vibe-acp
```

2. 在 Xcode 中，打开 `Xcode > Settings > Intelligence`。

3. 在`Agents`下，单击`Add an Agent`。

4. 将名称设置为 `Mistral Vibe`，将可执行文件设置为步骤 1 中的绝对路径。将解释器、参数和环境变量留空。

5. 单击 `Add`，然后在编码助手的 agent 选择器中选择 Mistral Vibe 并开始对话。

有关添加 ACP agents 的更多信息，请参阅 [Apple 的 Xcode 文档 ](<https://developer.apple.com/documentation/xcode/setting-up-coding-intelligence#Enable-agents>)。

## Neovim（使用 avante.nvim）

在配置的 acp_providers 部分添加 Mistral Vibe

```lua
{
  acp_providers = {
    ["mistral-vibe"] = {
      command = "vibe-acp",
      env = {
         MISTRAL_API_KEY = os.getenv("MISTRAL_API_KEY"), -- necessary if you setup Mistral Vibe manually
      },
    }
  }
}
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# ACP Setup

Mistral Vibe can be used in text editors and IDEs that support [Agent Client Protocol](<https://agentclientprotocol.com/overview/clients>). Mistral Vibe includes the `vibe-acp` tool.
Once you have set up `vibe` with the API keys, you are ready to use `vibe-acp` in your editor. Below are the setup instructions for some editors that support ACP.

## Zed

For usage in Zed, we recommend using the [Mistral Vibe Zed ACP agent](<https://zed.dev/acp/agent/mistral-vibe>). Alternatively, you can set up a local install as follows:

1. Go to `~/.config/zed/settings.json` and, under the `agent_servers` JSON object, add the following key-value pair to invoke the `vibe-acp` command. Here is the snippet:

```json
{
   "agent_servers": {
      "Mistral Vibe": {
         "type": "custom",
         "command": "vibe-acp",
         "args": [],
         "env": {}
      }
   }
}
```

2. In the `Agent Panel` view, select the `Mistral Vibe` agent and start the conversation.

## JetBrains IDEs

For using Mistral Vibe in JetBrains IDEs, you'll need to have the [Jetbrains AI Assistant extension](<https://plugins.jetbrains.com/plugin/22282-jetbrains-ai-assistant>) installed

### Version 2025.3 or later

1. Open settings, then go to `Tools > AI Assistant > Agents`. Search for `Mistral Vibe`, click install

2. Open AI Assistant. You should be able to select Mistral Vibe from the agent selector (if you're not authenticated yet, you will be prompted to do so).

### Legacy method

1. Add the following snippet to your JetBrains IDE acp.json ([documentation](<https://www.jetbrains.com/help/ai-assistant/acp.html>)):

```json
{
  "agent_servers": {
    "Mistral Vibe": {
      "command": "vibe-acp",
    }
  }
}
```

1. In the AI Chat agent selector, select the new Mistral Vibe agent and start the conversation.

## Xcode

1. Find the absolute path to the `vibe-acp` executable:

```shell
command -v vibe-acp
```

2. In Xcode, open `Xcode > Settings > Intelligence`.

3. Under `Agents`, click `Add an Agent`.

4. Set the name to `Mistral Vibe` and the executable to the absolute path from step 1. Leave the interpreter, arguments, and environment variables empty.

5. Click `Add`, then select Mistral Vibe in the coding assistant's agent picker and start a conversation.

See [Apple's Xcode documentation](<https://developer.apple.com/documentation/xcode/setting-up-coding-intelligence#Enable-agents>) for more information about adding ACP agents.

## Neovim (using avante.nvim)

Add Mistral Vibe in the acp_providers section of your configuration

```lua
{
  acp_providers = {
    ["mistral-vibe"] = {
      command = "vibe-acp",
      env = {
         MISTRAL_API_KEY = os.getenv("MISTRAL_API_KEY"), -- necessary if you setup Mistral Vibe manually
      },
    }
  }
}
```


</details>
