# 在 JetBrains 中接入 Qwen Code

介绍 ACP 注册表或手动安装、版本前提和故障排查，帮助在 IDE 中使用 Agent 与代码上下文。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-08-11](<https://github.com/QwenLM/qwen-code/commit/e6a327227164c6f0720f76ce56806344e5bcea1b>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/integration-jetbrains.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/在 JetBrains 中接入 Qwen Code.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# JetBrains IDE

> JetBrains IDE 通过 Agent 客户端协议 (ACP) 为 AI 编码助手提供本机支持。通过此集成，您可以直接在 JetBrains IDE 中使用 Qwen Code，并提供实时代码建议。

### 特点

- **原生 agent 体验 **：JetBrains IDE 中集成 AI 助手面板
- **Agent 客户端协议**：完全支持 ACP，实现高级 IDE 交互
- **符号管理**：#-提及文件以将其添加到对话上下文中
- **对话历史记录**：访问IDE内过去的对话
- **推理难度**：从agent的会话选项中选择默认、低、中、高、超高或最大；每个提供商都会映射或限制活动模型的请求层
- **上下文使用情况**：在Qwen Code工作时查看当前上下文窗口占用情况

### 要求

- JetBrains IDE，支持 ACP（IntelliJ、IDEA、WebStorm、PyCharm 等）
- Qwen Code CLI 已安装

### 安装

#### 从 ACP 注册表安装（推荐）

1. 安装Qwen CodeCLI：

   ```bash
   npm install -g @qwen-code/qwen-code
   ```

2. 打开您的 JetBrains IDE 并导航至 AI 聊天工具窗口。

3. 单击**添加ACPAgent**，然后单击**安装**。

![安装](<https://img.alicdn.com/imgextra/i4/O1CN01qNdPCW1y8AcqxRgCy_!!6000000006533-2-tps-2490-1788.png>)

对于使用JetBrainsAI助手and/or其他ACPagents的用户，单击**从ACP注册表安装** Agents 列出，然后安装Qwen Code ACP。

![从Agents列表添加](<https://img.alicdn.com/imgextra/i2/O1CN01ZyOugP26BOKzNgZXx_!!6000000007623-2-tps-479-523.png>)

4. Qwen Code agent 现在应该在 AI 助手面板中可用。

![Qwen Code 在 JetBrains AI 聊天 ](<https://img.alicdn.com/imgextra/i4/O1CN013kAVE41XVzbIZOxyv_!!6000000002930-2-tps-3188-2170.png>)

#### 手动安装（适用于旧版本的 JetBrains IDE）

1. 安装Qwen CodeCLI：

   ```bash
   npm install -g @qwen-code/qwen-code
   ```

2. 打开您的 JetBrains IDE 并导航至 AI 聊天工具窗口。

3. 单击右上角的3点菜单，然后选择**配置ACPAgent**并使用以下设置配置Qwen Code：

```json
{
  "agent_servers": {
    "qwen": {
      "command": "/path/to/qwen",
      "args": ["--acp"],
      "env": {}
    }
  }
}
```

4. Qwen Code agent 现在应该在 AI 助手面板中可用

![Qwen Code 在 JetBrains AI 聊天 ](<https://img.alicdn.com/imgextra/i3/O1CN01ZxYel21y433Ci6eg0_!!6000000006524-2-tps-2774-1494.png>)

## 故障排除

### Agent 未出现

- 在终端中运行`qwen --version`以验证安装
- 确保您的 JetBrains IDE 版本支持 ACP
- 重新启动您的JetBrains IDE

### Qwen Code 没有响应

- 检查您的互联网连接
- 通过在终端中运行 `qwen` 验证 CLI 是否有效
- [如果问题仍然存在，请在 GitHub](<https://github.com/qwenlm/qwen-code/issues>) 上提出问题


## 原文对照

<details>
<summary>展开完整原文</summary>

# JetBrains IDEs

> JetBrains IDEs provide native support for AI coding assistants through the Agent Client Protocol (ACP). This integration allows you to use Qwen Code directly within your JetBrains IDE with real-time code suggestions.

### Features

- **Native agent experience**: Integrated AI assistant panel within your JetBrains IDE
- **Agent Client Protocol**: Full support for ACP enabling advanced IDE interactions
- **Symbol management**: #-mention files to add them to the conversation context
- **Conversation history**: Access to past conversations within the IDE
- **Reasoning effort**: Choose Default, Low, Medium, High, Extra high, or Max from the agent's session options; each provider maps or clamps the requested tier for the active model
- **Context usage**: See the current context-window occupancy while Qwen Code works

### Requirements

- JetBrains IDE with ACP support (IntelliJ IDEA, WebStorm, PyCharm, etc.)
- Qwen Code CLI installed

### Installation

#### Install from ACP Registry (Recommend)

1. Install Qwen Code CLI:

   ```bash
   npm install -g @qwen-code/qwen-code
   ```

2. Open your JetBrains IDE and navigate to AI Chat tool window.

3. Click **Add ACP Agent**, then click **Install**.

   ![Install](<https://img.alicdn.com/imgextra/i4/O1CN01qNdPCW1y8AcqxRgCy_!!6000000006533-2-tps-2490-1788.png>)

   For users using JetBrains AI Assistant and/or other ACP agents, click **Install From ACP Registry** in Agents List, then install Qwen Code ACP.

   ![Add from Agents List](<https://img.alicdn.com/imgextra/i2/O1CN01ZyOugP26BOKzNgZXx_!!6000000007623-2-tps-479-523.png>)

4. The Qwen Code agent should now be available in the AI Assistant panel.

   ![Qwen Code in JetBrains AI Chat](<https://img.alicdn.com/imgextra/i4/O1CN013kAVE41XVzbIZOxyv_!!6000000002930-2-tps-3188-2170.png>)

#### Manual Install (for older version of JetBrains IDEs)

1. Install Qwen Code CLI:

   ```bash
   npm install -g @qwen-code/qwen-code
   ```

2. Open your JetBrains IDE and navigate to AI Chat tool window.

3. Click the 3-dot menu in the upper-right corner and select **Configure ACP Agent** and configure Qwen Code with the following settings:

```json
{
  "agent_servers": {
    "qwen": {
      "command": "/path/to/qwen",
      "args": ["--acp"],
      "env": {}
    }
  }
}
```

4. The Qwen Code agent should now be available in the AI Assistant panel

![Qwen Code in JetBrains AI Chat](<https://img.alicdn.com/imgextra/i3/O1CN01ZxYel21y433Ci6eg0_!!6000000006524-2-tps-2774-1494.png>)

## Troubleshooting

### Agent not appearing

- Run `qwen --version` in terminal to verify installation
- Ensure your JetBrains IDE version supports ACP
- Restart your JetBrains IDE

### Qwen Code not responding

- Check your internet connection
- Verify CLI works by running `qwen` in terminal
- [File an issue on GitHub](<https://github.com/qwenlm/qwen-code/issues>) if the problem persists


</details>
