# Claude Code 官方仓库说明

介绍安装方式、功能与插件、问题反馈及数据使用说明，提供项目与产品文档入口。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2026-01-12](<https://github.com/anthropics/claude-code/commit/b640d94a49629ef004ecceb5e2bd6d8aebf067ce>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Claude Code 官方仓库说明.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Claude Code

![](<https://img.shields.io/badge/Node.js-18%2B-brightgreen?style=flat-square>) [![npm]](<https://www.npmjs.com/package/@anthropic-ai/claude-code>)

[npm]: <https://img.shields.io/npm/v/@anthropic-ai/claude-code.svg?style=flat-square>

Claude Code 是一种代理编码工具，它位于您的终端中，了解您的代码库，并通过执行例行任务、解释复杂代码和处理 git 工作流程来帮助您更快地编码 - 所有这些都通过自然语言命令进行。在您的终端中使用它，IDE，或在 Github 上标记@claude。

**在[官方文档](<https://code.claude.com/docs/en/overview>)**中了解更多信息。



![原文图片](<https://raw.githubusercontent.com/anthropics/claude-code/2301018b1f61073c501a8e7a4813ef48c239163b/demo.gif>)


## 开始使用
> [！NOTE]
> 通过 npm 安装已被弃用。使用下面推荐的方法之一。

有关更多安装选项、卸载步骤和故障排除，请参阅 [ 安装文档 ](<https://code.claude.com/docs/en/setup>)。

1. 安装Claude Code：

**MacOS/Linux（推荐）：**
    ```bash
    curl -fsSL https://claude.ai/install.sh | bash
    ```

**自制程序（MacOS/Linux）：**
    ```bash
    brew install --cask claude-code
    ```

**Windows（推荐）：**
    ```powershell
    irm https://claude.ai/install.ps1 | iex
    ```

**WinGet（Windows）：**
    ```powershell
    winget install Anthropic.ClaudeCode
    ```

**NPM（已弃用）：**
    ```bash
    npm install -g @anthropic-ai/claude-code
    ```

2. 导航到您的项目目录并运行 `claude`。

## 插件

该存储库包含多个 Claude Code 插件，可通过自定义命令和 agents 扩展功能。有关可用插件的详细文档，请参阅 [ 插件目录 ](<Claude Code 官方插件目录.md>)。

## 报告错误

我们欢迎您的反馈。使用 `/bug` 命令直接在 Claude Code 内报告问题，或提交 [GitHub 问题 ](<https://github.com/anthropics/claude-code/issues>)。

## 在 Discord 上连接

加入 [Claude 开发者 Discord](<https://anthropic.com/discord>)，使用 Claude Code 与其他开发者联系。获取帮助、分享反馈并与社区讨论您的项目。

## 数据收集、使用和保留

当您使用 Claude Code 时，我们会收集反馈，其中包括使用数据（例如代码接受或拒绝）、关联的对话数据以及通过 `/bug` 命令提交的用户反馈。

### 我们如何使用您的数据

请参阅我们的[数据使用政策](<https://code.claude.com/docs/en/data-usage>)。

### 隐私保障

我们实施了多项保护措施来保护您的数据，包括限制敏感信息的保留期限、限制对用户会话数据的访问，以及禁止使用反馈进行模型训练的明确政策。

如需了解完整详情，请查看我们的[商业服务条款](<https://www.anthropic.com/legal/commercial-terms>)和[隐私政策](<https://www.anthropic.com/legal/privacy>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Claude Code

![](<https://img.shields.io/badge/Node.js-18%2B-brightgreen?style=flat-square>) [![npm]](<https://www.npmjs.com/package/@anthropic-ai/claude-code>)

[npm]: <https://img.shields.io/npm/v/@anthropic-ai/claude-code.svg?style=flat-square>

Claude Code is an agentic coding tool that lives in your terminal, understands your codebase, and helps you code faster by executing routine tasks, explaining complex code, and handling git workflows -- all through natural language commands. Use it in your terminal, IDE, or tag @claude on Github.

**Learn more in the [official documentation](<https://code.claude.com/docs/en/overview>)**.



![原文图片](<https://raw.githubusercontent.com/anthropics/claude-code/2301018b1f61073c501a8e7a4813ef48c239163b/demo.gif>)


## Get started
> [!NOTE]
> Installation via npm is deprecated. Use one of the recommended methods below.

For more installation options, uninstall steps, and troubleshooting, see the [setup documentation](<https://code.claude.com/docs/en/setup>).

1. Install Claude Code:

    **MacOS/Linux (Recommended):**
    ```bash
    curl -fsSL https://claude.ai/install.sh | bash
    ```

    **Homebrew (MacOS/Linux):**
    ```bash
    brew install --cask claude-code
    ```

    **Windows (Recommended):**
    ```powershell
    irm https://claude.ai/install.ps1 | iex
    ```

    **WinGet (Windows):**
    ```powershell
    winget install Anthropic.ClaudeCode
    ```

    **NPM (Deprecated):**
    ```bash
    npm install -g @anthropic-ai/claude-code
    ```

2. Navigate to your project directory and run `claude`.

## Plugins

This repository includes several Claude Code plugins that extend functionality with custom commands and agents. See the [plugins directory](<Claude Code 官方插件目录.md>) for detailed documentation on available plugins.

## Reporting Bugs

We welcome your feedback. Use the `/bug` command to report issues directly within Claude Code, or file a [GitHub issue](<https://github.com/anthropics/claude-code/issues>).

## Connect on Discord

Join the [Claude Developers Discord](<https://anthropic.com/discord>) to connect with other developers using Claude Code. Get help, share feedback, and discuss your projects with the community.

## Data collection, usage, and retention

When you use Claude Code, we collect feedback, which includes usage data (such as code acceptance or rejections), associated conversation data, and user feedback submitted via the `/bug` command.

### How we use your data

See our [data usage policies](<https://code.claude.com/docs/en/data-usage>).

### Privacy safeguards

We have implemented several safeguards to protect your data, including limited retention periods for sensitive information, restricted access to user session data, and clear policies against using feedback for model training.

For full details, please review our [Commercial Terms of Service](<https://www.anthropic.com/legal/commercial-terms>) and [Privacy Policy](<https://www.anthropic.com/legal/privacy>).


</details>
