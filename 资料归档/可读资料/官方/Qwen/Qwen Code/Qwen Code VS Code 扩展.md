# Qwen Code VS Code 扩展

介绍侧栏、原生差异和实时修改的使用入口、安装前提与故障排查；正文标明扩展为测试版。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-07-10](<https://github.com/QwenLM/qwen-code/commit/7a9ee09f49a74918ea9035fb5a3ace8d682be8cb>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/integration-vscode.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code VS Code 扩展.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Visual Studio Code

> VS Code扩展（测试版）可让您通过直接集成到IDE中的本机图形界面实时查看Qwen的变化，从而更轻松地访问Qwen Code并与之交互。

<br/>

<video src="https://cloud.video.taobao.com/vod/IKKwfM-kqNI3OJjM_U8uMCSMAoeEcJhs6VNCQmZxUfk.mp4" controls width="800">
您的浏览器不支持video标签。
</video>

### 特点

- **原生 IDE 体验 **：通过 Qwen 图标访问专用 Qwen Code 侧边栏面板
- **自动接受编辑模式**：自动应用Qwen的更改
- **文件管理**：@-提及文件或使用系统文件选择器附加文件和图像
- **对话历史记录**：访问过去的对话
- **多个会话**：同时运行多个Qwen Code会话

### 要求

- VS Code 1.96.0 或更高

### 安装

从 [Visual Studio Code 扩展市场 ](<https://marketplace.visualstudio.com/items?itemName=qwenlm.qwen-code-vscode-ide-companion>) 下载并安装扩展。

## 故障排除

### 扩展未安装

- 确保您拥有 VS Code 1.96.0 或更高版本
- 检查VS Code是否有安装扩展的权限
- 尝试直接从 Marketplace 网站安装

### Qwen Code 没有响应

- 检查您的互联网连接
- 开始新对话以查看问题是否仍然存在
- [如果问题仍然存在，请在 GitHub](<https://github.com/qwenlm/qwen-code/issues>) 上提出问题


## 原文对照

<details>
<summary>展开完整原文</summary>

# Visual Studio Code

> The VS Code extension (Beta) lets you see Qwen's changes in real-time through a native graphical interface integrated directly into your IDE, making it easier to access and interact with Qwen Code.

<br/>

<video src="https://cloud.video.taobao.com/vod/IKKwfM-kqNI3OJjM_U8uMCSMAoeEcJhs6VNCQmZxUfk.mp4" controls width="800">
  Your browser does not support the video tag.
</video>

### Features

- **Native IDE experience**: Dedicated Qwen Code sidebar panel accessed via the Qwen icon
- **Auto-accept edits mode**: Automatically apply Qwen's changes as they're made
- **File management**: @-mention files or attach files and images using the system file picker
- **Conversation history**: Access to past conversations
- **Multiple sessions**: Run multiple Qwen Code sessions simultaneously

### Requirements

- VS Code 1.96.0 or higher

### Installation

Download and install the extension from the [Visual Studio Code Extension Marketplace](<https://marketplace.visualstudio.com/items?itemName=qwenlm.qwen-code-vscode-ide-companion>).

## Troubleshooting

### Extension not installing

- Ensure you have VS Code 1.96.0 or higher
- Check that VS Code has permission to install extensions
- Try installing directly from the Marketplace website

### Qwen Code not responding

- Check your internet connection
- Start a new conversation to see if the issue persists
- [File an issue on GitHub](<https://github.com/qwenlm/qwen-code/issues>) if the problem continues


</details>
