# 在 Zed 中接入 Qwen Code

通过 ACP 注册表或手动方式安装，说明前提、Agent 面板和故障排查。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-03-16](<https://github.com/QwenLM/qwen-code/commit/93a12539764cd2b37d128aeb75e7acd8325e8dea>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/integration-zed.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/在 Zed 中接入 Qwen Code.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 泽德编辑

> Zed Editor 通过 Agent 客户端协议 (ACP) 为 AI 编码助手提供本机支持。这种集成允许您直接在 Zed 的界面中使用 Qwen Code 并提供实时代码建议。

![Zed 编辑器概述](<https://img.alicdn.com/imgextra/i1/O1CN01aAhU311GwEoNh27FP_!!6000000000686-2-tps-3024-1898.png>)

### 特点

- **原生 agent 体验 **：Zed 界面中集成的 AI 助手面板
- **Agent 客户端协议**：完全支持 ACP，实现高级 IDE 交互
- **文件管理**：@-提及文件以将其添加到对话上下文中
- **对话历史记录**：访问 Zed 中过去的对话

### 要求

- Zed Editor（推荐最新版本）
- Qwen Code CLI 已安装

### 安装

#### 从 ACP 注册表安装（推荐）

1. 安装Qwen CodeCLI：

```bash
npm install -g @qwen-code/qwen-code
```

2. 下载并安装[Zed编辑器](<https://zed.dev/>)

3. 在Zed中，点击右上角的**设置按钮**，选择**“添加agent”**，选择**“从注册表安装”**，找到**Qwen Code**，然后单击**安装**。

![ACP 注册表](<https://img.alicdn.com/imgextra/i4/O1CN0186ybL61EeG35fHFjy_!!6000000000376-2-tps-3056-1705.png>)

![Qwen Code ACP 已安装](<https://img.alicdn.com/imgextra/i1/O1CN01OXHhoR1J8irAvjs8F_!!6000000000984-2-tps-1247-703.png>)

#### 手动安装

1. 安装Qwen CodeCLI：

```bash
npm install -g @qwen-code/qwen-code
```

2. 下载并安装[Zed编辑器](<https://zed.dev/>)

3. 在Zed中，点击右上角的**设置按钮**，选择**“添加agent”**，选择**“创建自定义agent"**，并添加以下配置：

```json
"Qwen Code": {
  "type": "custom",
  "command": "qwen",
  "args": ["--acp"],
  "env": {}
}
```

![Qwen Code 集成](<https://img.alicdn.com/imgextra/i1/O1CN013s61L91dSE1J7MTgO_!!6000000003734-2-tps-2592-1234.png>)

## 故障排除

### Agent 未出现

- 在终端中运行`qwen --version`以验证安装
- 检查JSON配置是否有效
- 重新启动 Zed 编辑器

### Qwen Code 没有响应

- 检查您的互联网连接
- 通过在终端中运行 `qwen` 验证 CLI 是否有效
- [如果问题仍然存在，请在 GitHub](<https://github.com/qwenlm/qwen-code/issues>) 上提出问题


## 原文对照

<details>
<summary>展开完整原文</summary>

# Zed Editor

> Zed Editor provides native support for AI coding assistants through the Agent Client Protocol (ACP). This integration allows you to use Qwen Code directly within Zed's interface with real-time code suggestions.

![Zed Editor Overview](<https://img.alicdn.com/imgextra/i1/O1CN01aAhU311GwEoNh27FP_!!6000000000686-2-tps-3024-1898.png>)

### Features

- **Native agent experience**: Integrated AI assistant panel within Zed's interface
- **Agent Client Protocol**: Full support for ACP enabling advanced IDE interactions
- **File management**: @-mention files to add them to the conversation context
- **Conversation history**: Access to past conversations within Zed

### Requirements

- Zed Editor (latest version recommended)
- Qwen Code CLI installed

### Installation

#### Install from ACP Registry (Recommend)

1. Install Qwen Code CLI:

```bash
npm install -g @qwen-code/qwen-code
```

2. Download and install [Zed Editor](<https://zed.dev/>)

3. In Zed, click the **settings button** in the top right corner, select **"Add agent"**, choose **"Install from Registry"**, find **Qwen Code**, then click **Install**.

   ![ACP Registry](<https://img.alicdn.com/imgextra/i4/O1CN0186ybL61EeG35fHFjy_!!6000000000376-2-tps-3056-1705.png>)

   ![Qwen Code ACP Installed](<https://img.alicdn.com/imgextra/i1/O1CN01OXHhoR1J8irAvjs8F_!!6000000000984-2-tps-1247-703.png>)

#### Manual Install

1. Install Qwen Code CLI:

```bash
npm install -g @qwen-code/qwen-code
```

2. Download and install [Zed Editor](<https://zed.dev/>)

3. In Zed, click the **settings button** in the top right corner, select **"Add agent"**, choose **"Create a custom agent"**, and add the following configuration:

```json
"Qwen Code": {
  "type": "custom",
  "command": "qwen",
  "args": ["--acp"],
  "env": {}
}
```

![Qwen Code Integration](<https://img.alicdn.com/imgextra/i1/O1CN013s61L91dSE1J7MTgO_!!6000000003734-2-tps-2592-1234.png>)

## Troubleshooting

### Agent not appearing

- Run `qwen --version` in terminal to verify installation
- Check that the JSON configuration is valid
- Restart Zed Editor

### Qwen Code not responding

- Check your internet connection
- Verify CLI works by running `qwen` in terminal
- [File an issue on GitHub](<https://github.com/qwenlm/qwen-code/issues>) if the problem persists


</details>
