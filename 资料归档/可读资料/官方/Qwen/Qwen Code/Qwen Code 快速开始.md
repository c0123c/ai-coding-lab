# Qwen Code 快速开始

依次完成安装、认证和首次会话，以问答、修改代码、Git、修复及常用命令展示基础开发操作。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/QwenLM/qwen-code/commit/d33cd4ddcb5ce4c8f98df42210ca967707b3b9cd>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/quickstart.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/users/quickstart.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 快速入门

> 👏欢迎来到Qwen Code！

本快速入门指南将让您在短短几分钟内使用 AI- 支持的编码帮助。最后，您将了解如何使用 Qwen Code 执行常见的开发任务。

## 开始之前

确保您有：

- 打开 ** 终端 ** 或命令提示符
- 一个可以使用的代码项目
- 来自阿里云 ModelStudio（[北京](<https://bailian.console.aliyun.com/>) / [intl](<https://modelstudio.console.alibabacloud.com/>)）的 API 密钥，或阿里云令牌计划([北京](<https://bailian.console.aliyun.com/cn-beijing?tab=doc#/doc/?type=model&url=3028856>) / [国际](<https://modelstudio.console.alibabacloud.com/ap-southeast-1?tab=doc#/doc/?type=model>)) 订阅

## 步骤1：安装Qwen Code

要安装Qwen Code，请使用以下方法之一：

### 快速安装（推荐）

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
> 建议安装后重新启动终端，以确保环境变量生效。

### 手动安装

**先决条件**

确保您安装了 Node.js 22 或更高版本。从 [nodejs.org](<https://nodejs.org/en/download>) 下载它。

**NPM**

```bash
npm install -g @qwen-code/qwen-code@latest
```

**自制程序（macOS，Linux）**

```bash
brew install qwen-code
```

## 步骤2：设置身份验证

当您使用 `qwen` 命令启动交互式会话时，系统将提示您配置身份验证：

```bash
# You'll be prompted to set up authentication on first use
qwen
```

```bash
# Or run /auth anytime to change authentication method
/auth
```

首次运行菜单可让您连接模型提供者。选择以下一项：

- **阿里巴巴 ModelStudio** — 推荐设置。打开子菜单：
  - **令牌计划**：具有专用端点的基于使用情况的计费，针对团队和公司。请参阅 [ 令牌计划指南 ](<https://bailian.console.aliyun.com/cn-beijing?tab=doc#/doc/?type=model&url=3028856>) ([intl](<https://modelstudio.console.alibabacloud.com/ap-southeast-1?tab=doc#/doc/?type=model>)) 了解设置说明。
  - **标准API密钥**：连接阿里云现有的API密钥ModelStudio（[北京](<https://bailian.console.aliyun.com/>)/ [国际](<https://modelstudio.console.alibabacloud.com/>)）。详情请参阅API设置指南（[北京](<https://bailian.console.aliyun.com/cn-beijing/?tab=doc#/doc/?type=model&url=3023091>) / [国际](<https://modelstudio.console.alibabacloud.com/ap-southeast-1?tab=doc#/doc/?type=model&url=2974721>)）。
- **第三方提供商** — 选择内置提供商（DeepSeek、Grok、MiniMax、Z.AI、Kimi、Idealab、ModelScope、OpenRouter、请求等）并使用 API 键进行连接。
- **自定义提供程序** — 手动连接本地服务器、代理或不受支持的提供程序。

> ⚠️ **注意**：Qwen OAuth已于4月停止使用15、2026。如果您之前使用过 Qwen OAuth，请切换到上述方法之一。

> [！注意]
>
> Qwen Code 将其配置和自定义存储在 `.qwen` 目录中：`~/.qwen/` 用于用户级设置（例如 `settings.json`）和全局自定义命令，`<project>/.qwen/` 用于项目特定文件，例如自定义命令和摘要。

> [!提示]
>
> 要配置身份验证，请启动Qwen Code并运行`/auth`。使用【`/doctor`】随时查看您当前的配置。详情请参见[认证](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/auth>)页面。

## 步骤 3：开始您的第一个会话

在任意项目目录中打开终端并启动 Qwen Code：

```bash
# optional
cd /path/to/your/project
# start qwen
qwen
```

您将看到 Qwen Code 欢迎屏幕，其中包含您的会话信息、最近的对话和最新更新。键入 `/help` 以获取可用命令。

## 与 Qwen Code 聊天

### 问你的第一个问题

Qwen Code 将分析您的文件并提供摘要。您还可以提出更具体的问题：

```
explain the folder structure
```

您还可以向Qwen Code询问其自身的功能：

```
what can Qwen Code do?
```

> [！注意]
>
> Qwen Code 根据需要读取您的文件 - 您无需手动添加上下文。 Qwen Code 还可以访问其自己的文档，并可以回答有关其特性和功能的问题。

### 进行第一次代码更改

现在让 Qwen Code 进行一些实际的编码。尝试一个简单的任务：

```
add a hello world function to the main file
```

Qwen Code 将：

1. 找到合适的文件
2. 向您展示建议的更改
3. 征求您的批准
4. 进行编辑

> [！注意]
>
> Qwen Code 在修改文件之前始终请求许可。您可以批准个别更改或为会话启用“全部接受”模式。

### 将 Git 与 Qwen Code 一起使用

Qwen Code 使 Git 操作成为对话式的：

```
what files have I changed?
```

```
commit my changes with a descriptive message
```

您还可以提示更复杂的 Git 操作：

```
create a new branch called feature/quickstart
```

```
show me the last 5 commits
```

```
help me resolve merge conflicts
```

### 修复错误或添加功能

Qwen Code 精通调试和功能实现。

用自然语言描述你想要什么：

```
add input validation to the user registration form
```

或者修复现有问题：

```
there's a bug where users can submit empty forms - fix it
```

Qwen Code 将：

- 找到相关代码
- 了解上下文
- 实施解决方案
- 运行测试（如果可用）

### 测试其他常见工作流程

有多种方法可以使用 Qwen Code：

**重构代码**

```
refactor the authentication module to use async/await instead of callbacks
```

**编写测试**

```
write unit tests for the calculator functions
```

**更新文档**

```
update the README with installation instructions
```

**代码审查**

```
review my changes and suggest improvements
```

> [！提示]
>
> **记住**：Qwen Code是你的AI配对程序员。像与乐于助人的同事一样与它交谈 - 描述您想要实现的目标，它将帮助您实现目标。

## 基本命令

以下是日常使用中最重要的命令：

|命令|它的作用|示例|
| --------------------- | ------------------------------------------------ | ---------------------------------- |
| `qwen`                |开始Qwen Code| `qwen`                             |
| `/auth`               |更改身份验证方法（在会话中）| `/auth`                            |
| `/doctor`             |检查当前的身份验证和环境| `/doctor`                          |
| `/help`               |显示可用命令的帮助信息|`/help` 或 `/?`|
| `/compress`           |用摘要替换聊天记录以节省令牌| `/compress`                        |
| `/clear`              |清除对话历史记录并释放上下文|`/clear`（别名：`reset`、`new`）|
| `/theme`              |更改 Qwen Code 视觉主题| `/theme`                           |
| `/language`           |查看或更改语言设置| `/language`                        |
| → `ui [language]`     |设置【UI】界面语言| `/language ui zh-CN`               |
| → `output [language]` |设置LLM输出语言| `/language output Chinese`         |
| `/quit`               |立即退出Qwen Code|`/quit` 或 `/exit`|

有关命令的完整列表，请参阅 [CLI 参考文献 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/commands>)。

## 给初学者的专业提示

**具体说明您的要求**

- 而不是：“修复错误”
- 尝试：“修复用户输入错误凭据后看到空白屏幕的登录错误”

**使用分步说明**

- 将复杂的任务分解为步骤：

```
1. create a new database table for user profiles
2. create an API endpoint to get and update user profiles
3. build a webpage that allows users to see and edit their information
```

**让Qwen Code先探索一下**

- 在进行更改之前，让Qwen Code了解您的代码：

```
analyze the database schema
```

```
build a dashboard showing products that are most frequently returned by our UK customers
```

**通过快捷方式节省时间**

- 按 `?` 查看所有可用的键盘快捷键
- 使用 Tab 完成命令
- 按 ↑ 查看命令历史记录
- 输入 `/` 查看所有斜杠命令

## 寻求帮助

- **在Qwen Code**中：输入`/help`或询问“我如何...”
- **文档**：你来了！浏览其他指南
- **社区**：加入我们的[GitHub讨论](<https://github.com/QwenLM/qwen-code/discussions>)以获得提示和支持


## 原文对照

<details>
<summary>展开完整原文</summary>

# Quickstart

> 👏 Welcome to Qwen Code!

This quickstart guide will have you using AI-powered coding assistance in just a few minutes. By the end, you'll understand how to use Qwen Code for common development tasks.

## Before you begin

Make sure you have:

- A **terminal** or command prompt open
- A code project to work with
- An API key from Alibaba Cloud ModelStudio ([Beijing](<https://bailian.console.aliyun.com/>) / [intl](<https://modelstudio.console.alibabacloud.com/>)), or an Alibaba Cloud Token Plan ([Beijing](<https://bailian.console.aliyun.com/cn-beijing?tab=doc#/doc/?type=model&url=3028856>) / [intl](<https://modelstudio.console.alibabacloud.com/ap-southeast-1?tab=doc#/doc/?type=model>)) subscription

## Step 1: Install Qwen Code

To install Qwen Code, use one of the following methods:

### Quick Install (Recommended)

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
> It's recommended to restart your terminal after installation to ensure environment variables take effect.

### Manual Installation

**Prerequisites**

Make sure you have Node.js 22 or later installed. Download it from [nodejs.org](<https://nodejs.org/en/download>).

**NPM**

```bash
npm install -g @qwen-code/qwen-code@latest
```

**Homebrew (macOS, Linux)**

```bash
brew install qwen-code
```

## Step 2: Set up authentication

When you start an interactive session with the `qwen` command, you'll be prompted to configure authentication:

```bash
# You'll be prompted to set up authentication on first use
qwen
```

```bash
# Or run /auth anytime to change authentication method
/auth
```

The first-run menu lets you connect a model provider. Choose one of:

- **Alibaba ModelStudio** — the recommended setup. Opens a sub-menu:
  - **Token Plan**: usage-based billing with a dedicated endpoint, aimed at teams and companies. See the [Token Plan guide](<https://bailian.console.aliyun.com/cn-beijing?tab=doc#/doc/?type=model&url=3028856>) ([intl](<https://modelstudio.console.alibabacloud.com/ap-southeast-1?tab=doc#/doc/?type=model>)) for setup instructions.
  - **Standard API Key**: connect with an existing API key from Alibaba Cloud ModelStudio ([Beijing](<https://bailian.console.aliyun.com/>) / [intl](<https://modelstudio.console.alibabacloud.com/>)). See the API setup guide ([Beijing](<https://bailian.console.aliyun.com/cn-beijing/?tab=doc#/doc/?type=model&url=3023091>) / [intl](<https://modelstudio.console.alibabacloud.com/ap-southeast-1?tab=doc#/doc/?type=model&url=2974721>)) for details.
- **Third-party Providers** — choose a built-in provider (DeepSeek, Grok, MiniMax, Z.AI, Kimi, Idealab, ModelScope, OpenRouter, Requesty, and more) and connect with an API key.
- **Custom Provider** — manually connect a local server, proxy, or unsupported provider.

> ⚠️ **Note**: Qwen OAuth was discontinued on April 15, 2026. If you were previously using Qwen OAuth, please switch to one of the methods above.

> [!note]
>
> Qwen Code stores its configuration and customizations in `.qwen` directories: `~/.qwen/` for user-level settings (e.g. `settings.json`) and global custom commands, and `<project>/.qwen/` for project-specific files such as custom commands and summaries.

> [!tip]
>
> To configure authentication, start Qwen Code and run `/auth`. Use `/doctor` to check your current configuration at any time. See the [Authentication](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/auth>) page for details.

## Step 3: Start your first session

Open your terminal in any project directory and start Qwen Code:

```bash
# optional
cd /path/to/your/project
# start qwen
qwen
```

You'll see the Qwen Code welcome screen with your session information, recent conversations, and latest updates. Type `/help` for available commands.

## Chat with Qwen Code

### Ask your first question

Qwen Code will analyze your files and provide a summary. You can also ask more specific questions:

```
explain the folder structure
```

You can also ask Qwen Code about its own capabilities:

```
what can Qwen Code do?
```

> [!note]
>
> Qwen Code reads your files as needed - you don't have to manually add context. Qwen Code also has access to its own documentation and can answer questions about its features and capabilities.

### Make your first code change

Now let's make Qwen Code do some actual coding. Try a simple task:

```
add a hello world function to the main file
```

Qwen Code will:

1. Find the appropriate file
2. Show you the proposed changes
3. Ask for your approval
4. Make the edit

> [!note]
>
> Qwen Code always asks for permission before modifying files. You can approve individual changes or enable "Accept all" mode for a session.

### Use Git with Qwen Code

Qwen Code makes Git operations conversational:

```
what files have I changed?
```

```
commit my changes with a descriptive message
```

You can also prompt for more complex Git operations:

```
create a new branch called feature/quickstart
```

```
show me the last 5 commits
```

```
help me resolve merge conflicts
```

### Fix a bug or add a feature

Qwen Code is proficient at debugging and feature implementation.

Describe what you want in natural language:

```
add input validation to the user registration form
```

Or fix existing issues:

```
there's a bug where users can submit empty forms - fix it
```

Qwen Code will:

- Locate the relevant code
- Understand the context
- Implement a solution
- Run tests if available

### Test out other common workflows

There are a number of ways to work with Qwen Code:

**Refactor code**

```
refactor the authentication module to use async/await instead of callbacks
```

**Write tests**

```
write unit tests for the calculator functions
```

**Update documentation**

```
update the README with installation instructions
```

**Code review**

```
review my changes and suggest improvements
```

> [!tip]
>
> **Remember**: Qwen Code is your AI pair programmer. Talk to it like you would a helpful colleague - describe what you want to achieve, and it will help you get there.

## Essential commands

Here are the most important commands for daily use:

| Command               | What it does                                     | Example                            |
| --------------------- | ------------------------------------------------ | ---------------------------------- |
| `qwen`                | start Qwen Code                                  | `qwen`                             |
| `/auth`               | Change authentication method (in session)        | `/auth`                            |
| `/doctor`             | Check current authentication and environment     | `/doctor`                          |
| `/help`               | Display help information for available commands  | `/help` or `/?`                    |
| `/compress`           | Replace chat history with summary to save Tokens | `/compress`                        |
| `/clear`              | Clear conversation history and free up context   | `/clear` (aliases: `reset`, `new`) |
| `/theme`              | Change Qwen Code visual theme                    | `/theme`                           |
| `/language`           | View or change language settings                 | `/language`                        |
| → `ui [language]`     | Set UI interface language                        | `/language ui zh-CN`               |
| → `output [language]` | Set LLM output language                          | `/language output Chinese`         |
| `/quit`               | Exit Qwen Code immediately                       | `/quit` or `/exit`                 |

See the [CLI reference](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/commands>) for a complete list of commands.

## Pro tips for beginners

**Be specific with your requests**

- Instead of: "fix the bug"
- Try: "fix the login bug where users see a blank screen after entering wrong credentials"

**Use step-by-step instructions**

- Break complex tasks into steps:

```
1. create a new database table for user profiles
2. create an API endpoint to get and update user profiles
3. build a webpage that allows users to see and edit their information
```

**Let Qwen Code explore first**

- Before making changes, let Qwen Code understand your code:

```
analyze the database schema
```

```
build a dashboard showing products that are most frequently returned by our UK customers
```

**Save time with shortcuts**

- Press `?` to see all available keyboard shortcuts
- Use Tab for command completion
- Press ↑ for command history
- Type `/` to see all slash commands

## Getting help

- **In Qwen Code**: Type `/help` or ask "how do I..."
- **Documentation**: You're here! Browse other guides
- **Community**: Join our [GitHub Discussion](<https://github.com/QwenLM/qwen-code/discussions>) for tips and support


</details>
