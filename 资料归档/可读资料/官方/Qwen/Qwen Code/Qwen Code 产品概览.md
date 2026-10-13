# Qwen Code 产品概览

简述终端安装启动和代码开发能力，提供后续指南入口；属于产品入门概览。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-16](<https://github.com/QwenLM/qwen-code/commit/9853adf0df1fa9a69be04011931c7a3eb1c2afa1>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/overview.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code 产品概览.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Qwen Code概述

[![@qwen-code/qwen-code下载](<https://img.shields.io/npm/dw/@qwen-code/qwen-code.svg>)](<https://npm-compare.com/@qwen-code/qwen-code>)
[![@qwen-code/qwen-code版本](<https://img.shields.io/npm/v/@qwen-code/qwen-code.svg>)](<https://www.npmjs.com/package/@qwen-code/qwen-code>)

> 了解 Qwen Code，Qwen 的代理编码工具，它位于您的终端中，可帮助您比以往更快地将想法转化为代码。

## 30 秒内开始使用

### 安装Qwen Code：

当独立存档可用于
您的平台时，推荐的安装程序使用独立存档。如果回落到 npm，Node.js 22 或更高版本的 npm 必须是 
 在 PATH 上可用。

**Linux / macOS**

```sh
curl -fsSL https://qwen-code-assets.oss-cn-hangzhou.aliyuncs.com/installation/install-qwen-standalone.sh | bash
```

**Windows**

```powershell
irm https://qwen-code-assets.oss-cn-hangzhou.aliyuncs.com/installation/install-qwen-standalone.ps1 | iex
```

> [！注意]
>
> 如果出现以下情况，建议安装后重新启动终端 `qwen` 不是
> 立即可用 PATH。如果安装失败，请参考
> [手动安装](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/quickstart#manual-installation>) 在快速入门中
> 指导。对于离线安装，请下载发布存档并运行
> 安装程序与 `--archive PATH`;保留 `SHA256SUMS` 档案旁边。

### 开始使用Qwen Code：

```bash
cd your-project
qwen
```

首次启动时，系统会提示您连接模型提供者。菜单提供**阿里巴巴ModelStudio**（官方推荐设置）、**第三方提供商**（内置提供商如DeepSeek、MiniMax、Z.AI、 Kimi 和 OpenRouter，使用 API 键连接），以及 ** 自定义提供程序 **（本地服务器、代理或不受支持的提供程序）。对于[阿里云通证计划](<https://bailian.console.aliyun.com/cn-beijing?tab=doc#/doc/?type=model&url=3028856>)（[intl](<https://modelstudio.console.alibabacloud.com/ap-southeast-1?tab=doc#/doc/?type=model>)），选择**阿里巴巴ModelStudio→通证计划**；要使用 ModelStudio API 密钥，请选择 **Alibaba ModelStudio → 标准 API 密钥 ** 并按照 API 设置指南进行操作（[北京](<https://bailian.console.aliyun.com/cn-beijing/?tab=doc#/doc/?type=model&url=3023091>) / [国际](<https://modelstudio.console.alibabacloud.com/ap-southeast-1?tab=doc#/doc/?type=model&url=2974721>)）。然后让我们开始了解您的代码库。尝试以下命令之一：

```
what does this project do?
```

![](<https://cloud.video.taobao.com/vod/j7-QtQScn8UEAaEdiv619fSkk5p-t17orpDbSqKVL5A.mp4>)

首次使用时系统会提示您登录。就是这样！ [Continue 快速入门（5 分钟）→](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/quickstart>)

> [！提示]
>
> 如果遇到问题，请参阅[故障排除](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/support/troubleshooting>)。

> [！注意]
>
> **新的VS Code扩展（测试版）**：更喜欢图形界面？我们新的 **VS Code 扩展 ** 提供易于使用的本机 IDE 体验，无需熟悉终端。只需从市场安装并直接在侧边栏中使用 Qwen Code 开始编码即可。立即下载并安装 [Qwen Code 伴侣 ](<https://marketplace.visualstudio.com/items?itemName=qwenlm.qwen-code-vscode-ide-companion>)。

## Qwen Code 能为您做什么

- **根据描述构建功能**：用简单的语言告诉Qwen Code您想要构建什么。它将制定计划、编写代码并确保其有效。
- **调试并修复问题**：描述错误或粘贴错误消息。 Qwen Code 将分析您的代码库、识别问题并实施修复。
- **浏览任何代码库**：询问有关您团队的代码库的任何问题，并得到深思熟虑的答复。 Qwen Code 保持对整个项目结构的了解，可以从网络上查找最新信息，并且使用 [MCP](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/mcp>) 可以从 Google Drive、Figma 和 Slack 等外部数据源中提取数据。
- **自动执行繁琐的任务**：修复繁琐的 lint 问题，解决合并冲突，并编写发行说明。在您的开发人员机器上通过单个命令即可完成所有这些操作，或者在 CI 中自动完成所有这些操作。
- **[后续建议](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/followup-suggestions>)**：Qwen Code预测您接下来要输入的内容并将其显示为幽灵文本。按 T​​ab 接受，或者继续输入即可关闭。

## 为什么开发者喜欢Qwen Code

- **在您的终端中工作**：不是另一个聊天窗口。不是另一个IDE。 Qwen Code 在您已经工作的地方使用您喜欢的工具与您会面。
- **执行操作**：Qwen Code可以直接编辑文件、运行命令和创建提交。需要更多吗？ [MCP](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/mcp>) 让 Qwen Code 阅读您在 Google Drive 中的设计文档，在 Jira 中更新您的票证，或使用 _ 您的 _ 自定义开发人员工具。
- **Unix 哲学**：Qwen Code 是可组合和可编写脚本的。 `tail -f app.log | qwen -p "Slack me if you see any anomalies appear in this log stream"` _ 工作 _。您的CI可以运行`qwen -p "If there are new text strings, translate them into French and raise a PR for @lang-fr-team to review"`。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Qwen Code overview

[![@qwen-code/qwen-code downloads](<https://img.shields.io/npm/dw/@qwen-code/qwen-code.svg>)](<https://npm-compare.com/@qwen-code/qwen-code>)
[![@qwen-code/qwen-code version](<https://img.shields.io/npm/v/@qwen-code/qwen-code.svg>)](<https://www.npmjs.com/package/@qwen-code/qwen-code>)

> Learn about Qwen Code, Qwen's agentic coding tool that lives in your terminal and helps you turn ideas into code faster than ever before.

## Get started in 30 seconds

### Install Qwen Code:

The recommended installer uses a standalone archive when one is available for
your platform. If it falls back to npm, Node.js 22 or later with npm must be
available on PATH.

**Linux / macOS**

```sh
curl -fsSL https://qwen-code-assets.oss-cn-hangzhou.aliyuncs.com/installation/install-qwen-standalone.sh | bash
```

**Windows**

```powershell
irm https://qwen-code-assets.oss-cn-hangzhou.aliyuncs.com/installation/install-qwen-standalone.ps1 | iex
```

> [!note]
>
> It's recommended to restart your terminal after installation if `qwen` is not
> immediately available on PATH. If the installation fails, please refer to
> [Manual Installation](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/quickstart#manual-installation>) in the Quickstart
> guide. For offline installation, download a release archive and run the
> installer with `--archive PATH`; keep `SHA256SUMS` next to the archive.

### Start using Qwen Code:

```bash
cd your-project
qwen
```

On first launch you'll be prompted to connect a model provider. The menu offers **Alibaba ModelStudio** (the official recommended setup), **Third-party Providers** (built-in providers such as DeepSeek, MiniMax, Z.AI, Kimi, and OpenRouter, connected with an API key), and **Custom Provider** (a local server, proxy, or unsupported provider). For the [Alibaba Cloud Token Plan](<https://bailian.console.aliyun.com/cn-beijing?tab=doc#/doc/?type=model&url=3028856>) ([intl](<https://modelstudio.console.alibabacloud.com/ap-southeast-1?tab=doc#/doc/?type=model>)), choose **Alibaba ModelStudio → Token Plan**; to use a ModelStudio API key, choose **Alibaba ModelStudio → Standard API Key** and follow the API setup guide ([Beijing](<https://bailian.console.aliyun.com/cn-beijing/?tab=doc#/doc/?type=model&url=3023091>) / [intl](<https://modelstudio.console.alibabacloud.com/ap-southeast-1?tab=doc#/doc/?type=model&url=2974721>)). Then let's start with understanding your codebase. Try one of these commands:

```
what does this project do?
```

![](<https://cloud.video.taobao.com/vod/j7-QtQScn8UEAaEdiv619fSkk5p-t17orpDbSqKVL5A.mp4>)

You'll be prompted to log in on first use. That's it! [Continue with Quickstart (5 mins) →](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/quickstart>)

> [!tip]
>
> See [troubleshooting](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/support/troubleshooting>) if you hit issues.

> [!note]
>
> **New VS Code Extension (Beta)**: Prefer a graphical interface? Our new **VS Code extension** provides an easy-to-use native IDE experience without requiring terminal familiarity. Simply install from the marketplace and start coding with Qwen Code directly in your sidebar. Download and install the [Qwen Code Companion](<https://marketplace.visualstudio.com/items?itemName=qwenlm.qwen-code-vscode-ide-companion>) now.

## What Qwen Code does for you

- **Build features from descriptions**: Tell Qwen Code what you want to build in plain language. It will make a plan, write the code, and ensure it works.
- **Debug and fix issues**: Describe a bug or paste an error message. Qwen Code will analyze your codebase, identify the problem, and implement a fix.
- **Navigate any codebase**: Ask anything about your team's codebase, and get a thoughtful answer back. Qwen Code maintains awareness of your entire project structure, can find up-to-date information from the web, and with [MCP](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/mcp>) can pull from external datasources like Google Drive, Figma, and Slack.
- **Automate tedious tasks**: Fix fiddly lint issues, resolve merge conflicts, and write release notes. Do all this in a single command from your developer machines, or automatically in CI.
- **[Followup suggestions](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/followup-suggestions>)**: Qwen Code predicts what you want to type next and shows it as ghost text. Press Tab to accept, or just keep typing to dismiss.

## Why developers love Qwen Code

- **Works in your terminal**: Not another chat window. Not another IDE. Qwen Code meets you where you already work, with the tools you already love.
- **Takes action**: Qwen Code can directly edit files, run commands, and create commits. Need more? [MCP](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/mcp>) lets Qwen Code read your design docs in Google Drive, update your tickets in Jira, or use _your_ custom developer tooling.
- **Unix philosophy**: Qwen Code is composable and scriptable. `tail -f app.log | qwen -p "Slack me if you see any anomalies appear in this log stream"` _works_. Your CI can run `qwen -p "If there are new text strings, translate them into French and raise a PR for @lang-fr-team to review"`.


</details>
