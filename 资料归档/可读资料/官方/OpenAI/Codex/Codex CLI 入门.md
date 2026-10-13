# Codex CLI 入门

介绍安装、启动、读取代码、修改和运行命令等终端能力，说明基本使用入口和平台配置。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/codex/cli>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/codex/cli.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex CLI 入门.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Codex CLI

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

## 从终端检查、编辑和运行代码

无需离开终端即可检查代码、进行更改、运行命令并自动执行可重复的工作。

### 从这里开始

- [安装Codex](<#开始使用>)
- [CLI参考](<https://learn.chatgpt.com/docs/developer-commands?surface=cli>)

### 为什么使用CodexCLI

- **针对本地存储库进行工作：** 让 Codex 检查文件、进行编辑并运行计算机上已安装的工具。
- **保持控制：**选择适合任务的模型、推理工作、权限和命令。
- **使用脚本和 CI:** 交互使用 Codex 或从可重复的工作流程和管道中调用 codex exec。

## 开始使用

**开始使用Codex CLI。**

安装 Codex、登录并从项目目录运行您的第一个任务。

### 1。安装Codex

选择以下安装方法之一：

#### macOS/Linux

使用 macOS 和 Linux 的独立安装程序安装 Codex CLI。

**安装：**

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

**更新：**

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

#### 窗户

使用适用于 Windows 的独立安装程序安装 Codex CLI。
从新的 PowerShell 窗口启动它们。

**安装：**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

**更新：**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

#### npm

您还可以使用npm安装Codex CLI。

**安装：**

```bash
npm install -g @openai/codex
```

**更新：**

```bash
npm install -g @openai/codex
```

#### 自制

您还可以使用 Homebrew 安装 Codex CLI。

**安装：**

```bash
brew install --cask codex
```

**更新：**

```bash
brew upgrade --cask codex
```

### 2。运行Codex并登录

打开项目目录并运行`codex`。首次运行Codex时，请选择**使用ChatGPT**或其他可用的登录方法登录。

[查看身份验证选项](<https://learn.chatgpt.com/docs/auth>)

### 3。开始你的第一个任务

描述您想要实现的目标。例如，要求 Codex 解释该项目、进行重点更改或帮助调试问题。

```text
Tell me about this project
```

在任务之前和之后创建 Git 检查点，以便您可以恢复更改。请参阅[最佳实践](<https://learn.chatgpt.com/guides/best-practices>)。

### 后续步骤

- [探索CLI参考](<https://learn.chatgpt.com/docs/developer-commands?surface=cli>)
- [配置Codex](<https://learn.chatgpt.com/docs/configuration?surface=cli>)
- [使用 codex exec 实现自动化](<https://learn.chatgpt.com/docs/non-interactive-mode>)

## 看看CodexCLI能做什么

使用一个集中的终端循环进行交互式工作、自动化、审查和委派。

- [在终端中保留编码循环](<https://learn.chatgpt.com/docs/developer-commands?surface=cli>)：在存储库中启动Codex以探索不熟悉的代码、计划更改、编辑文件并运行本地开发工具。控制主动转弯，检查出现的命令和差异，并在同一会话中进行后续工作。
- [使用技能和插件](<https://learn.chatgpt.com/docs/skills-and-plugins?surface=cli>)：将可重复的指令打包为技能，然后添加插件将Codex连接到团队的工具和数据，而无需离开CLI。
- [在发布之前审核更改](<https://learn.chatgpt.com/docs/code-review?surface=cli>)：针对未提交的更改、提交或基础分支运行专门的审核。 Codex 在不修改工作树的情况下报告优先结果，因此您可以在提交或打开拉取请求之前解决风险。

## 围绕 Codex 构建终端工作流程

了解可用于恢复会话、添加视觉和 Web 上下文、分解复杂工作以及将 Codex 连接到您的开发工具的 CLI 功能。

- [**返回已保存的聊天**](<https://learn.chatgpt.com/docs/developer-commands?surface=cli#codex-resume>) — `codex resume`：从当前存储库重新打开最近的聊天，或者在需要返回旧工作时在本地聊天中搜索。
- [**将视觉上下文带入提示中**](<https://learn.chatgpt.com/docs/image-inputs?surface=cli>) — `codex --image`：通过第一个提示传递错误屏幕截图、架构图或设计参考，或将图像粘贴到交互式编辑器中。
- [**拆分更大的调查**](<https://learn.chatgpt.com/docs/agent-configuration/subagents>) - `subagents`：要求Codex将重点工作委托给专门的agents，然后将他们的发现带回主终端会话。
- [**搜索当前上下文**](<https://learn.chatgpt.com/docs/web-search?surface=cli>) - `codex --search`：当任务依赖于当前版本、文档或外部行为时，将运行切换到实时网络搜索。搜索活动在记录中保持可见。
- [**将工作移至Codex云**](<https://learn.chatgpt.com/docs/cloud#use-codex-cloud-from-the-cli>) — `codex cloud`：浏览活动和已完成的聊天，将工作提交到配置的环境，并将结果从终端应用到本地存储库。
- [**使用MCP**](<https://learn.chatgpt.com/docs/extend/mcp?surface=cli>)连接外部工具 - `codex mcp`：添加本地或远程MCP服务器，在需要时进行身份验证，并在Codex使用它们之前检查当前会话可用的工具。
- [**设置每次运行的边界**](<https://learn.chatgpt.com/docs/agent-approvals-security>) - `/permissions`：选择Codex何时可以编辑文件或运行命令而不询问，并在继续之前检查活动沙箱和可写根。
- [**将Codex安装到您的终端**](<https://learn.chatgpt.com/docs/cli-customization>) - `codex completion`：为您的shell生成补全，选择语法主题，并在VISUAL或EDITOR配置的编辑器中打开更长的提示。

## 使用 Codex CLI 当…

- [您从终端工作](<https://learn.chatgpt.com/docs/developer-commands?surface=cli>)：在一个集中循环中探索、编辑和运行存储库。
- [您需要编写脚本或CI](<https://learn.chatgpt.com/docs/non-interactive-mode>)：在可重复的工作流程中运行非交互式命令。
- [您想要进行本地代码审查](<https://learn.chatgpt.com/docs/code-review?surface=cli>)：在提交或打开拉取请求之前检查更改。
- [您想将工作交给云端](<https://learn.chatgpt.com/docs/cloud#use-codex-cloud-from-the-cli>)：启动云聊天，稍后返回终端。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Codex CLI

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

## Inspect, edit, and run code from your terminal

Inspect code, make changes, run commands, and automate repeatable work without leaving your terminal.

### Start here

- [Install Codex](<#getting-started>)
- [CLI reference](<https://learn.chatgpt.com/docs/developer-commands?surface=cli>)

### Why use Codex CLI

- **Work against your local repository:** Let Codex inspect files, make edits, and run the tools already installed on your machine.
- **Stay in control:** Choose the model, reasoning effort, permissions, and commands that fit the task.
- **Compose with scripts and CI:** Use Codex interactively or call codex exec from repeatable workflows and pipelines.

## Getting started

**Get started with Codex CLI.**

Install Codex, sign in, and run your first task from a project directory.

### 1. Install Codex

Choose one of these install methods:

#### macOS/Linux

Install the Codex CLI with the standalone installer for macOS and Linux.

**Install:**

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

**Update:**

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

#### Windows

Install the Codex CLI with the standalone installer for Windows.
Start these from a new PowerShell window.

**Install:**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

**Update:**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

#### npm

You can also install the Codex CLI with npm.

**Install:**

```bash
npm install -g @openai/codex
```

**Update:**

```bash
npm install -g @openai/codex
```

#### Homebrew

You can also install the Codex CLI with Homebrew.

**Install:**

```bash
brew install --cask codex
```

**Update:**

```bash
brew upgrade --cask codex
```

### 2. Run Codex and sign in

Open a project directory and run `codex`. The first time you run Codex, choose **Sign in with ChatGPT** or another available sign-in method.

[Review authentication options](<https://learn.chatgpt.com/docs/auth>)

### 3. Start your first task

Describe what you want to accomplish. For example, ask Codex to explain the project, make a focused change, or help debug an issue.

```text
Tell me about this project
```

Create Git checkpoints before and after a task so you can revert changes. See the [best practices](<https://learn.chatgpt.com/guides/best-practices>).

### Next steps

- [Explore the CLI reference](<https://learn.chatgpt.com/docs/developer-commands?surface=cli>)
- [Configure Codex](<https://learn.chatgpt.com/docs/configuration?surface=cli>)
- [Automate with codex exec](<https://learn.chatgpt.com/docs/non-interactive-mode>)

## See what Codex CLI can do

Use one focused terminal loop for interactive work, automation, review, and delegation.

- [Keep the coding loop in your terminal](<https://learn.chatgpt.com/docs/developer-commands?surface=cli>): Start Codex in a repository to explore unfamiliar code, plan a change, edit files, and run your local development tools. Steer the active turn, inspect commands and diffs as they appear, and keep follow-up work in the same session.
- [Use skills and plugins](<https://learn.chatgpt.com/docs/skills-and-plugins?surface=cli>): Package repeatable instructions as skills, then add plugins to connect Codex to your team's tools and data without leaving the CLI.
- [Review changes before they ship](<https://learn.chatgpt.com/docs/code-review?surface=cli>): Run a dedicated review against uncommitted changes, a commit, or a base branch. Codex reports prioritized findings without modifying your working tree, so you can address risks before you commit or open a pull request.

## Build a terminal workflow around Codex

Learn about the CLI features you can use to resume sessions, add visual and web context, split up complex work, and connect Codex to your development tools.

- [**Return to a saved chat**](<https://learn.chatgpt.com/docs/developer-commands?surface=cli#codex-resume>) — `codex resume`: Reopen a recent chat from the current repository, or search across local chats when you need to return to older work.
- [**Bring visual context into the prompt**](<https://learn.chatgpt.com/docs/image-inputs?surface=cli>) — `codex --image`: Pass an error screenshot, architecture diagram, or design reference with the first prompt, or paste an image into the interactive composer.
- [**Split up a larger investigation**](<https://learn.chatgpt.com/docs/agent-configuration/subagents>) — `subagents`: Ask Codex to delegate focused work to specialized agents, then bring their findings back into the main terminal session.
- [**Search for current context**](<https://learn.chatgpt.com/docs/web-search?surface=cli>) — `codex --search`: Switch a run to live web search when a task depends on current releases, documentation, or external behavior. Search activity stays visible in the transcript.
- [**Move work to Codex cloud**](<https://learn.chatgpt.com/docs/cloud#use-codex-cloud-from-the-cli>) — `codex cloud`: Browse active and completed chats, submit work to a configured environment, and apply the result to your local repository from the terminal.
- [**Connect external tools with MCP**](<https://learn.chatgpt.com/docs/extend/mcp?surface=cli>) — `codex mcp`: Add local or remote MCP servers, authenticate when needed, and inspect the tools available to the current session before Codex uses them.
- [**Set the boundaries for each run**](<https://learn.chatgpt.com/docs/agent-approvals-security>) — `/permissions`: Choose when Codex can edit files or run commands without asking, and inspect the active sandbox and writable roots before you continue.
- [**Fit Codex to your terminal**](<https://learn.chatgpt.com/docs/cli-customization>) — `codex completion`: Generate completions for your shell, choose a syntax theme, and open longer prompts in the editor configured by VISUAL or EDITOR.

## Use Codex CLI when…

- [You work from the terminal](<https://learn.chatgpt.com/docs/developer-commands?surface=cli>): Explore, edit, and run a repository in one focused loop.
- [You need scripting or CI](<https://learn.chatgpt.com/docs/non-interactive-mode>): Run a non-interactive command in a repeatable workflow.
- [You want a local code review](<https://learn.chatgpt.com/docs/code-review?surface=cli>): Inspect changes before you commit or open a pull request.
- [You want to hand work to the cloud](<https://learn.chatgpt.com/docs/cloud#use-codex-cloud-from-the-cli>): Launch a cloud chat and return to the terminal later.


</details>
