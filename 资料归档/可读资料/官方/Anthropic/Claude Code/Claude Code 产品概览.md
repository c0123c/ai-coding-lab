# Claude Code 产品概览

介绍产品可以理解代码、修改文件、运行命令和自动化开发任务的能力，以及终端、IDE、桌面和浏览器入口，并提供开始使用的路径。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-08](<https://code.claude.com/docs/en/overview>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/overview>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Claude Code 产品概览.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 概述

> Claude Code 是一种代理编码工具，可以读取您的代码库、编辑文件、运行命令并与您的开发工具集成。可在您的终端、IDE、桌面应用程序和浏览器中使用。

Claude Code 是一个由 AI- 支持的编码助手，可帮助您构建功能、修复错误和自动化开发任务。它了解您的整个代码库，并且可以跨多个文件和工具来完成工作。

## 开始使用

Claude Code 在多个表面上运行：终端、IDE 扩展、桌面应用程序和网络。从下面的选项卡中选择一个即可开始。大多数表面需要 [Claude 订阅 ](<https://claude.com/pricing?utm_source=claude_code\&utm_medium=docs\&utm_content=overview_pricing>) 或 [Anthropic 控制台 ](<https://platform.claude.com/>) 帐户。终端CLI、VS Code和JetBrains还支持[第三方提供商](<https://code.claude.com/docs/en/third-party-integrations>)。



  

#### 终端

全功能的 CLI 可直接在您的终端中使用 Claude Code。从命令行编辑文件、运行命令并管理整个项目。

要安装 Claude Code，请打开终端并运行适用于您的系统的命令。如果您以前没有使用过终端，[终端指南](<https://code.claude.com/docs/en/terminal-guide>)将展示如何打开终端并粘贴命令。

    

      

#### 本机安装（推荐）

**macOS, Linux, WSL:**

        ```bash theme={null}
        curl -fsSL https://claude.ai/install.sh | bash
        ```

在 Windows 上，当您处于 PowerShell 时，提示会显示 `PS C:\`；当您处于 CMD 时，提示会显示 `C:\`，而不会显示 `PS`。

**Windows PowerShell:**

        ```powershell theme={null}
        irm https://claude.ai/install.ps1 | iex
        ```

**Windows CMD:**

        ```batch theme={null}
        curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
        ```

安装命令在下载 Claude Code 时没有显示任何进度。安装程序完成后，打开一个新的终端窗口并运行 `claude --version`。正常安装会打印版本号。如果您的 shell 显示 `claude` 未找到或无法识别，则安装目录不在您的 PATH 上：请参阅 [ 修复您的 PATH](<https://code.claude.com/docs/en/troubleshoot-install#command-not-found-claude-after-installation>)。

如果您看到 `The token '&&' is not a valid statement separator`，则您位于 PowerShell，而不是 CMD。如果您看到 `'irm' is not recognized as an internal or external command`，则您位于 CMD，而不是 PowerShell。

如果安装命令失败并出现 `syntax error near unexpected token '<'`、`403` 或任何其他错误，请参阅 [ 安装疑难解答 ](<https://code.claude.com/docs/en/troubleshoot-install#find-your-error>) 以将错误与修复程序相匹配并了解替代安装方法。

[Git for Windows](<https://git-scm.com/downloads/win>) 建议在本机 Windows 上使用，以便 Claude Code 可以使用 Bash 工具。如果未安装 Git for Windows，Claude Code 将使用 PowerShell 作为 shell 工具。 WSL Windows 版安装不需要 Git。

        

**说明：**

本机安装会在后台自动更新，让您保持最新版本。
        

      


      

#### 自制

        ```bash theme={null}
        brew install --cask claude-code
        ```

Homebrew 提供两个木桶。 `claude-code` 跟踪稳定的发布渠道，该渠道通常晚于一周左右，并跳过具有重大回归的发布。 `claude-code@latest` 跟踪最新频道并在新版本发布后立即接收。

        

**说明：**

自制安装不会自动更新。根据您安装的 cask，运行 `brew upgrade claude-code` 或 `brew upgrade claude-code@latest` 以获取最新功能和安全修复程序。
        

      


      

#### WinGet

        ```powershell theme={null}
        winget install Anthropic.ClaudeCode
        ```

        

**说明：**

WinGet 安装不会自动更新。定期运行 `winget upgrade Anthropic.ClaudeCode` 以获取最新功能和安全修复程序。
        

      

    


您还可以在 Debian、Fedora、RHEL 和 Alpine 上使用 [apt、dnf 或 apk](<https://code.claude.com/docs/en/setup#install-with-linux-package-managers>) 进行安装。

然后在任意项目中启动Claude Code。将 `your-project` 替换为计算机上项目目录的路径：

    ```bash theme={null}
    cd your-project
    claude
    ```

Claude Code 首次使用时提示您登录。如果您已设置 `ANTHROPIC_API_KEY` 环境变量，并且当 Claude Code 询问是否使用该密钥时批准该密钥，则 Claude Code 会跳过登录提示。 [Continue 快速入门 →](<https://code.claude.com/docs/en/quickstart>)

    

**提示：**

请参阅 [ 高级设置 ](<https://code.claude.com/docs/en/setup>) 了解安装选项、手动更新或卸载说明。如果遇到问题，请访问[安装故障排除](<https://code.claude.com/docs/en/troubleshoot-install>)。
    

  


  

#### VS Code

VS Code 扩展直接在编辑器中提供内联差异、@-提及、计划审查和对话历史记录。

    * [安装VS Code](<vscode:extension/anthropic.claude-code>)
    * [安装Cursor](<cursor:extension/anthropic.claude-code>)

或者在扩展视图中搜索“Claude Code”（Mac 上为 `Cmd+Shift+X`，Windows/Linux 上为 `Ctrl+Shift+X`）。安装后，打开命令面板（`Cmd+Shift+P` / `Ctrl+Shift+P`），输入“Claude Code”，然后选择**在新选项卡中打开**。

[开始使用VS Code→](<https://code.claude.com/docs/en/vs-code#get-started>)
  


  

#### 桌面应用程序

用于在 IDE 或终端之外运行 Claude Code 的独立应用程序。直观地查看差异、并行运行多个会话、安排重复任务并启动云会话。

下载并安装：

    * [macOS](<https://claude.ai/api/desktop/darwin/universal/dmg/latest/redirect?utm_source=claude_code\&utm_medium=docs>)（英特尔和苹果芯片）
    * [Windows](<https://claude.ai/api/desktop/win32/x64/setup/latest/redirect?utm_source=claude_code\&utm_medium=docs>) (x64)
    * [Windows ARM64](<https://claude.ai/api/desktop/win32/arm64/setup/latest/redirect?utm_source=claude_code\&utm_medium=docs>)
    * 在 Ubuntu 或 Debian 上（该应用程序处于测试阶段），请按照 [Linux 安装说明 ](<https://code.claude.com/docs/en/desktop-linux>) 使用 apt 安装它

安装后，启动Claude，登录，然后单击**代码**选项卡开始编码。该应用程序包含Claude Code，因此您无需单独安装CLI。需要[付费订阅](<https://claude.com/pricing?utm_source=claude_code\&utm_medium=docs\&utm_content=overview_desktop_pricing>)。

[了解有关桌面应用程序的更多信息 →](<https://code.claude.com/docs/en/desktop-quickstart>)
  


  

#### 网络

在浏览器中运行 Claude Code，无需本地设置。启动长时间运行的任务并在完成后检查，处理本地没有的存储库，或并行运行多个任务。对于较长的工作，请创建一个 [ 项目 ](<https://code.claude.com/docs/en/claude-projects>) 并让 Claude 为您协调并行会话。可在桌面浏览器和 [ 适用于 iOS 和 Android ](<https://code.claude.com/docs/en/mobile>) 的 Claude 应用程序上使用。

从[claude.ai/code](<https://claude.ai/code>)开始编码。

[开始使用→](<https://code.claude.com/docs/en/web-quickstart>)
  


  

#### JetBrains

适用于 IntelliJ IDEA、PyCharm、WebStorm 和其他 JetBrains IDE 的插件，具有交互式差异查看和选择上下文共享功能。

从 JetBrains 市场安装 [Claude Code 插件 ](<https://plugins.jetbrains.com/plugin/27310-claude-code-beta->) 并重新启动 IDE。该插件需要Claude CodeCLI，单独安装；请参阅[JetBrains设置步骤](<https://code.claude.com/docs/en/jetbrains#installation>)。

[开始使用JetBrains→](<https://code.claude.com/docs/en/jetbrains>)
  


## 你能做什么

以下是使用 Claude Code 的一些方法：



  

#### 自动化你一直拖延的工作

Claude Code 处理耗费您一天时间的繁琐任务：为未经测试的代码编写测试、修复项目中的 lint 错误、解决合并冲突、更新依赖项以及编写发行说明。

    ```bash theme={null}
    claude "write tests for the auth module, run them, and fix any failures"
    ```
  


  

#### 构建功能并修复错误

用通俗易懂的语言描述你想要什么。 Claude Code 规划该方法，跨多个文件编写代码，并验证其是否有效。

对于错误，请粘贴错误消息或描述症状。 Claude Code 通过您的代码库跟踪问题，确定根本原因，并实施修复。有关更多示例，请参阅[常见工作流程](<https://code.claude.com/docs/en/common-workflows>)。
  


  

#### 创建提交和拉取请求

Claude Code 直接与 git 一起工作。它暂存更改、写入提交消息、创建分支并打开拉取请求。

    ```bash theme={null}
    claude "commit my changes with a descriptive message"
    ```

在 CI 中，您可以使用 [GitHub 操作 ](<https://code.claude.com/docs/en/github-actions>) 或 [GitLab CI/CD](<https://code.claude.com/docs/en/gitlab-ci-cd>) 自动进行代码审查和问题分类。
  


  

#### 使用 MCP 连接您的工具

[Model Context Protocol (MCP)](<https://code.claude.com/docs/en/mcp>) 是用于将 AI 工具连接到外部数据源的开放标准。借助 MCP，Claude Code 可以读取 Google Drive 中的设计文档、更新 Jira 中的票证、从 Slack 中提取数据或使用您自己的自定义工具。 [MCP 快速入门](<https://code.claude.com/docs/en/mcp-quickstart>) 将您的第一个服务器端到端连接。
  


  

#### 通过说明、技能和挂钩进行定制

[`CLAUDE.md`](<https://code.claude.com/docs/en/memory>) 是添加到项目根目录中的 Markdown 文件，Claude Code 在每个会话开始时读取。使用它来设置编码标准、架构决策、首选库和审查清单。如果您的存储库已有用于其他编码 agents 的 `AGENTS.md`，则 Claude Code [ 可以读取 ](<https://code.claude.com/docs/en/memory#agents-md>) 来代替 `CLAUDE.md`。 Claude 还构建了 [ 自动记忆 ](<https://code.claude.com/docs/en/memory#auto-memory>)，无需您编写任何内容即可保存跨会话的学习内容。

创建 [ 技能 ](<https://code.claude.com/docs/en/skills>) 以打包您的团队可以共享的可重复工作流程，例如 `/review-pr` 或 `/deploy-staging`。

[Hooks](<https://code.claude.com/docs/en/hooks>) 允许您在 Claude Code 操作之前或之后运行 shell 命令，例如在每次文件编辑后自动格式化或在提交前运行 lint。
  


  

#### 并行运行 agents 并构建自定义 agents

生成 [ 多个 Claude Code agents](<https://code.claude.com/docs/en/sub-agents>)，同时处理任务的不同部分。主管 agent 协调工作、分配子任务并合并结果。

要并行运行多个完整会话并从一个屏幕观看它们，请使用 [ 背景 agents](<https://code.claude.com/docs/en/agent-view>)。对于完全自定义的工作流程，[Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>) 允许您构建自己的 agents，由 Claude Code 的工具和功能提供支持，并完全控制编排、工具访问和权限。
  


  

#### 使用 CLI 进行管道、脚本和自动化

Claude Code 是可组合的并遵循 Unix 哲学。通过管道登录到其中，在 CI 中运行它，或将其与其他工具链接：

    ```bash theme={null}
    # Analyze recent log output
    tail -200 app.log | claude -p "Slack me if you see any anomalies"

    # Automate translations in CI
    claude -p "translate new strings into French and raise a PR for review"

    # Bulk operations across files
    git diff main --name-only | claude -p "review these changed files for security issues"
    ```

有关完整的命令和标志集，请参阅 [CLI 参考文献 ](<https://code.claude.com/docs/en/cli-reference>)。
  


  

#### 安排重复任务

按计划运行 Claude 以自动化重复的工作：早上 PR 审核、夜间 CI 故障分析、每周依赖项审核或 PR 合并后同步文档。

    * [例程](<https://code.claude.com/docs/en/routines>)在云端运行，因此即使您的计算机关闭，它们也会继续运行。它们还可以在 API 调用或 GitHub 事件上触发。从网络、桌面应用程序或通过运行 CLI 中的 `/schedule` 创建它们。
    * [桌面计划任务](<https://code.claude.com/docs/en/desktop-scheduled-tasks>)在您的计算机上运行，可以直接访问您的本地文件和工具
    * [`/loop`](<https://code.claude.com/docs/en/scheduled-tasks>) 在 CLI 会话中重复提示以进行快速轮询
  


  

#### 随时随地工作

会话不绑定到单个表面。当您的环境发生变化时，在它们之间转移工作：

    * 离开办公桌，使用 [ 远程控制 ](<https://code.claude.com/docs/en/remote-control>) 通过手机或任何浏览器继续工作
    * 消息 [ 从您的手机调度 ](<https://code.claude.com/docs/en/desktop#sessions-from-dispatch>) 任务并打开它创建的桌面会话
    * 在 [ 网页 ](<https://code.claude.com/docs/en/claude-code-on-the-web>) 或 [Claude 移动应用程序 ](<https://code.claude.com/docs/en/mobile>) 上启动长时间运行的任务，然后使用 `claude --teleport` 将其拉入您的终端。 Teleport 需要订阅 claude.ai。
    * 运行 `/desktop` 以在 [桌面应用程序 ](<https://code.claude.com/docs/en/desktop>) 中继续当前终端会话，您可以在其中直观地查看差异。 `/desktop` 切换需要 claude.ai 订阅。可在 macOS 和 x64 Windows 上使用。
    * 通过团队聊天路由任务：在 [Slack](<https://code.claude.com/docs/en/slack>) 中提及 `@Claude` 并提供错误报告并获取拉取请求
  


## 到处使用Claude Code

每个 [ 表面 ](<https://code.claude.com/docs/en/glossary#surface>) 连接到相同的底层 Claude Code 引擎，因此您的存储库的 CLAUDE.md 文件、设置和 MCP 服务器可以在所有这些引擎上工作。

除了上面的[终端](<https://code.claude.com/docs/en/quickstart>)、[VS Code](<https://code.claude.com/docs/en/vs-code>)、[JetBrains](<https://code.claude.com/docs/en/jetbrains>)、[桌面](<https://code.claude.com/docs/en/desktop>)和[Web](<https://code.claude.com/docs/en/claude-code-on-the-web>)表面之外， Claude Code 与 CI/CD、聊天和浏览器工作流程集成：

|我想做的事|最佳选择|
| - | - |
|Continue 来自我的手机或其他设备的本地会话|[遥控器](<https://code.claude.com/docs/en/remote-control>)|
|将来自 Telegram、Discord、iMessage 或我自己的 webhook 的事件推送到会话中|[频道](<https://code.claude.com/docs/en/channels>)|
|在本地启动任务，在移动设备上继续|[`claude --cloud`](<https://code.claude.com/docs/en/claude-code-on-the-web#from-terminal-to-cloud>)，然后是[Claude移动应用程序](<https://code.claude.com/docs/en/mobile>)|
|按定期计划运行 Claude|[例程](<https://code.claude.com/docs/en/routines>)或[桌面计划任务](<https://code.claude.com/docs/en/desktop-scheduled-tasks>)|
|自动执行 PR 审核和问题分类|[GitHub 操作 ](<https://code.claude.com/docs/en/github-actions>) 或 [GitLab CI/CD](<https://code.claude.com/docs/en/gitlab-ci-cd>)|
|对每个 PR 进行自动代码审查|[GitHub 代码审查](<https://code.claude.com/docs/en/code-review>)|
|将错误报告从 Slack 路由到拉取请求|[松弛](<https://code.claude.com/docs/en/slack>)|
|调试实时 Web 应用程序|[镀铬](<https://code.claude.com/docs/en/chrome>)|
|为您自己的工作流程构建自定义 agents| [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>) |

## 后续步骤

一旦您安装了 Claude Code，这些指南将帮助您深入了解。

* [快速入门](<https://code.claude.com/docs/en/quickstart>)：逐步完成您的第一个实际任务，从探索代码库到提交修复
* [存储指令和内存](<https://code.claude.com/docs/en/memory>)：使用CLAUDE.md文件和自动内存提供Claude持久指令
* [常见工作流程](<https://code.claude.com/docs/en/common-workflows>)和[最佳实践](<https://code.claude.com/docs/en/best-practices>)：充分利用Claude Code的模式
* [Claude 学院](<https://academy.claude.com/>)：免费自定进度课程，包括 [Claude Code 101](<https://academy.claude.com/courses/claude-code-101>) 和 [Claude Code 实际操作](<https://academy.claude.com/courses/claude-code-in-action>)
* [适用于每项任务的工具](<https://claude.com/blog/a-harness-for-every-task-dynamic-workflows-in-claude-code>)：Claude Code团队如何使用[动态工作流程](<https://code.claude.com/docs/en/workflows>)同时协调多个子代理
* [设置](<https://code.claude.com/docs/en/settings>)：为您的工作流程自定义Claude Code
* [故障排除](<https://code.claude.com/docs/en/troubleshooting>)：常见问题的解决方案
* [code.claude.com](<https://code.claude.com/>)：演示、定价和产品详细信息


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Overview

> Claude Code is an agentic coding tool that reads your codebase, edits files, runs commands, and integrates with your development tools. Available in your terminal, IDE, desktop app, and browser.

Claude Code is an AI-powered coding assistant that helps you build features, fix bugs, and automate development tasks. It understands your entire codebase and can work across multiple files and tools to get things done.

## Get started

Claude Code runs on several surfaces: the terminal, IDE extensions, a desktop app, and the web. Choose one from the tabs below to get started. Most surfaces require a [Claude subscription](<https://claude.com/pricing?utm_source=claude_code\&utm_medium=docs\&utm_content=overview_pricing>) or [Anthropic Console](<https://platform.claude.com/>) account. The Terminal CLI, VS Code, and JetBrains also support [third-party providers](<https://code.claude.com/docs/en/third-party-integrations>).



  

#### Terminal

    The full-featured CLI for working with Claude Code directly in your terminal. Edit files, run commands, and manage your entire project from the command line.

    To install Claude Code, open a terminal and run the command for your system. If you haven't used a terminal before, the [terminal guide](<https://code.claude.com/docs/en/terminal-guide>) shows how to open one and paste the command.

    

      

#### Native Install (Recommended)

        **macOS, Linux, WSL:**

        ```bash theme={null}
        curl -fsSL https://claude.ai/install.sh | bash
        ```

        On Windows, your prompt shows `PS C:\` when you're in PowerShell and `C:\` without the `PS` when you're in CMD.

        **Windows PowerShell:**

        ```powershell theme={null}
        irm https://claude.ai/install.ps1 | iex
        ```

        **Windows CMD:**

        ```batch theme={null}
        curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
        ```

        The install command shows no progress while it downloads Claude Code. When the installer finishes, open a new terminal window and run `claude --version`. A working installation prints a version number. If your shell says `claude` isn't found or isn't recognized, the install directory isn't on your PATH yet: see [Fix your PATH](<https://code.claude.com/docs/en/troubleshoot-install#command-not-found-claude-after-installation>).

        If you see `The token '&&' is not a valid statement separator`, you're in PowerShell, not CMD. If you see `'irm' is not recognized as an internal or external command`, you're in CMD, not PowerShell.

        If the install command fails with `syntax error near unexpected token '<'`, a `403`, or any other error, see [Troubleshoot installation](<https://code.claude.com/docs/en/troubleshoot-install#find-your-error>) to match the error to a fix and for alternative install methods.

        [Git for Windows](<https://git-scm.com/downloads/win>) is recommended on native Windows so Claude Code can use the Bash tool. If Git for Windows is not installed, Claude Code uses PowerShell as the shell tool instead. WSL setups do not need Git for Windows.

        

**说明：**

          Native installations automatically update in the background to keep you on the latest version.
        

      


      

#### Homebrew

        ```bash theme={null}
        brew install --cask claude-code
        ```

        Homebrew offers two casks. `claude-code` tracks the stable release channel, which is typically about a week behind and skips releases with major regressions. `claude-code@latest` tracks the latest channel and receives new versions as soon as they ship.

        

**说明：**

          Homebrew installations do not auto-update. Run `brew upgrade claude-code` or `brew upgrade claude-code@latest`, depending on which cask you installed, to get the latest features and security fixes.
        

      


      

#### WinGet

        ```powershell theme={null}
        winget install Anthropic.ClaudeCode
        ```

        

**说明：**

          WinGet installations do not auto-update. Run `winget upgrade Anthropic.ClaudeCode` periodically to get the latest features and security fixes.
        

      

    


    You can also install with [apt, dnf, or apk](<https://code.claude.com/docs/en/setup#install-with-linux-package-managers>) on Debian, Fedora, RHEL, and Alpine.

    Then start Claude Code in any project. Replace `your-project` with the path to a project directory on your machine:

    ```bash theme={null}
    cd your-project
    claude
    ```

    Claude Code prompts you to log in on first use. If you've set the `ANTHROPIC_API_KEY` environment variable and you approve the key when Claude Code asks whether to use it, Claude Code skips the login prompt. [Continue with the Quickstart →](<https://code.claude.com/docs/en/quickstart>)

    

**提示：**

      See [advanced setup](<https://code.claude.com/docs/en/setup>) for installation options, manual updates, or uninstallation instructions. Visit [installation troubleshooting](<https://code.claude.com/docs/en/troubleshoot-install>) if you hit issues.
    

  


  

#### VS Code

    The VS Code extension provides inline diffs, @-mentions, plan review, and conversation history directly in your editor.

    * [Install for VS Code](<vscode:extension/anthropic.claude-code>)
    * [Install for Cursor](<cursor:extension/anthropic.claude-code>)

    Or search for "Claude Code" in the Extensions view (`Cmd+Shift+X` on Mac, `Ctrl+Shift+X` on Windows/Linux). After installing, open the Command Palette (`Cmd+Shift+P` / `Ctrl+Shift+P`), type "Claude Code", and select **Open in New Tab**.

    [Get started with VS Code →](<https://code.claude.com/docs/en/vs-code#get-started>)
  


  

#### Desktop app

    A standalone app for running Claude Code outside your IDE or terminal. Review diffs visually, run multiple sessions side by side, schedule recurring tasks, and start cloud sessions.

    Download and install:

    * [macOS](<https://claude.ai/api/desktop/darwin/universal/dmg/latest/redirect?utm_source=claude_code\&utm_medium=docs>) (Intel and Apple Silicon)
    * [Windows](<https://claude.ai/api/desktop/win32/x64/setup/latest/redirect?utm_source=claude_code\&utm_medium=docs>) (x64)
    * [Windows ARM64](<https://claude.ai/api/desktop/win32/arm64/setup/latest/redirect?utm_source=claude_code\&utm_medium=docs>)
    * On Ubuntu or Debian, where the app is in beta, install it with apt by following the [Linux install instructions](<https://code.claude.com/docs/en/desktop-linux>)

    After installing, launch Claude, sign in, and click the **Code** tab to start coding. The app includes Claude Code, so you don't need to install the CLI separately. A [paid subscription](<https://claude.com/pricing?utm_source=claude_code\&utm_medium=docs\&utm_content=overview_desktop_pricing>) is required.

    [Learn more about the desktop app →](<https://code.claude.com/docs/en/desktop-quickstart>)
  


  

#### Web

    Run Claude Code in your browser with no local setup. Kick off long-running tasks and check back when they're done, work on repos you don't have locally, or run multiple tasks in parallel. For a longer body of work, create a [project](<https://code.claude.com/docs/en/claude-projects>) and let Claude coordinate the parallel sessions for you. Available on desktop browsers and [the Claude app for iOS and Android](<https://code.claude.com/docs/en/mobile>).

    Start coding at [claude.ai/code](<https://claude.ai/code>).

    [Get started →](<https://code.claude.com/docs/en/web-quickstart>)
  


  

#### JetBrains

    A plugin for IntelliJ IDEA, PyCharm, WebStorm, and other JetBrains IDEs with interactive diff viewing and selection context sharing.

    Install the [Claude Code plugin](<https://plugins.jetbrains.com/plugin/27310-claude-code-beta->) from the JetBrains Marketplace and restart your IDE. The plugin requires the Claude Code CLI, installed separately; see the [JetBrains setup steps](<https://code.claude.com/docs/en/jetbrains#installation>).

    [Get started with JetBrains →](<https://code.claude.com/docs/en/jetbrains>)
  


## What you can do

Here are some of the ways you can use Claude Code:



  

#### Automate the work you keep putting off

    Claude Code handles the tedious tasks that eat up your day: writing tests for untested code, fixing lint errors across a project, resolving merge conflicts, updating dependencies, and writing release notes.

    ```bash theme={null}
    claude "write tests for the auth module, run them, and fix any failures"
    ```
  


  

#### Build features and fix bugs

    Describe what you want in plain language. Claude Code plans the approach, writes the code across multiple files, and verifies it works.

    For bugs, paste an error message or describe the symptom. Claude Code traces the issue through your codebase, identifies the root cause, and implements a fix. See [common workflows](<https://code.claude.com/docs/en/common-workflows>) for more examples.
  


  

#### Create commits and pull requests

    Claude Code works directly with git. It stages changes, writes commit messages, creates branches, and opens pull requests.

    ```bash theme={null}
    claude "commit my changes with a descriptive message"
    ```

    In CI, you can automate code review and issue triage with [GitHub Actions](<https://code.claude.com/docs/en/github-actions>) or [GitLab CI/CD](<https://code.claude.com/docs/en/gitlab-ci-cd>).
  


  

#### Connect your tools with MCP

    The [Model Context Protocol (MCP)](<https://code.claude.com/docs/en/mcp>) is an open standard for connecting AI tools to external data sources. With MCP, Claude Code can read your design docs in Google Drive, update tickets in Jira, pull data from Slack, or use your own custom tooling. The [MCP quickstart](<https://code.claude.com/docs/en/mcp-quickstart>) connects your first server end to end.
  


  

#### Customize with instructions, skills, and hooks

    [`CLAUDE.md`](<https://code.claude.com/docs/en/memory>) is a markdown file you add to your project root that Claude Code reads at the start of every session. Use it to set coding standards, architecture decisions, preferred libraries, and review checklists. If your repository already has an `AGENTS.md` for other coding agents, Claude Code [can read that](<https://code.claude.com/docs/en/memory#agents-md>) in place of a `CLAUDE.md`. Claude also builds [auto memory](<https://code.claude.com/docs/en/memory#auto-memory>) as it works, saving learnings across sessions without you writing anything.

    Create [skills](<https://code.claude.com/docs/en/skills>) to package repeatable workflows your team can share, like `/review-pr` or `/deploy-staging`.

    [Hooks](<https://code.claude.com/docs/en/hooks>) let you run shell commands before or after Claude Code actions, like auto-formatting after every file edit or running lint before a commit.
  


  

#### Run agents in parallel and build custom agents

    Spawn [multiple Claude Code agents](<https://code.claude.com/docs/en/sub-agents>) that work on different parts of a task simultaneously. A lead agent coordinates the work, assigns subtasks, and merges results.

    To run several full sessions in parallel and watch them from one screen, use [background agents](<https://code.claude.com/docs/en/agent-view>). For fully custom workflows, the [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>) lets you build your own agents powered by Claude Code's tools and capabilities, with full control over orchestration, tool access, and permissions.
  


  

#### Pipe, script, and automate with the CLI

    Claude Code is composable and follows the Unix philosophy. Pipe logs into it, run it in CI, or chain it with other tools:

    ```bash theme={null}
    # Analyze recent log output
    tail -200 app.log | claude -p "Slack me if you see any anomalies"

    # Automate translations in CI
    claude -p "translate new strings into French and raise a PR for review"

    # Bulk operations across files
    git diff main --name-only | claude -p "review these changed files for security issues"
    ```

    See the [CLI reference](<https://code.claude.com/docs/en/cli-reference>) for the full set of commands and flags.
  


  

#### Schedule recurring tasks

    Run Claude on a schedule to automate work that repeats: morning PR reviews, overnight CI failure analysis, weekly dependency audits, or syncing docs after PRs merge.

    * [Routines](<https://code.claude.com/docs/en/routines>) run in the cloud, so they keep running even when your computer is off. They can also trigger on API calls or GitHub events. Create them from the web, the Desktop app, or by running `/schedule` in the CLI.
    * [Desktop scheduled tasks](<https://code.claude.com/docs/en/desktop-scheduled-tasks>) run on your machine, with direct access to your local files and tools
    * [`/loop`](<https://code.claude.com/docs/en/scheduled-tasks>) repeats a prompt within a CLI session for quick polling
  


  

#### Work from anywhere

    Sessions aren't tied to a single surface. Move work between them as your context changes:

    * Step away from your desk and keep working from your phone or any browser with [Remote Control](<https://code.claude.com/docs/en/remote-control>)
    * Message [Dispatch](<https://code.claude.com/docs/en/desktop#sessions-from-dispatch>) a task from your phone and open the Desktop session it creates
    * Start a long-running task on the [web](<https://code.claude.com/docs/en/claude-code-on-the-web>) or the [Claude mobile app](<https://code.claude.com/docs/en/mobile>), then pull it into your terminal with `claude --teleport`. Teleport requires a claude.ai subscription.
    * Run `/desktop` to continue your current terminal session in the [Desktop app](<https://code.claude.com/docs/en/desktop>), where you can review diffs visually. The `/desktop` handoff requires a claude.ai subscription. Available on macOS and x64 Windows.
    * Route tasks from team chat: mention `@Claude` in [Slack](<https://code.claude.com/docs/en/slack>) with a bug report and get a pull request back
  


## Use Claude Code everywhere

Each [surface](<https://code.claude.com/docs/en/glossary#surface>) connects to the same underlying Claude Code engine, so your repo's CLAUDE.md files, settings, and MCP servers work across all of them.

Beyond the [Terminal](<https://code.claude.com/docs/en/quickstart>), [VS Code](<https://code.claude.com/docs/en/vs-code>), [JetBrains](<https://code.claude.com/docs/en/jetbrains>), [Desktop](<https://code.claude.com/docs/en/desktop>), and [Web](<https://code.claude.com/docs/en/claude-code-on-the-web>) surfaces above, Claude Code integrates with CI/CD, chat, and browser workflows:

| What I want to do | Best option |
| - | - |
| Continue a local session from my phone or another device | [Remote Control](<https://code.claude.com/docs/en/remote-control>) |
| Push events from Telegram, Discord, iMessage, or my own webhooks into a session | [Channels](<https://code.claude.com/docs/en/channels>) |
| Start a task locally, continue on mobile | [`claude --cloud`](<https://code.claude.com/docs/en/claude-code-on-the-web#from-terminal-to-cloud>), then the [Claude mobile app](<https://code.claude.com/docs/en/mobile>) |
| Run Claude on a recurring schedule | [Routines](<https://code.claude.com/docs/en/routines>) or [Desktop scheduled tasks](<https://code.claude.com/docs/en/desktop-scheduled-tasks>) |
| Automate PR reviews and issue triage | [GitHub Actions](<https://code.claude.com/docs/en/github-actions>) or [GitLab CI/CD](<https://code.claude.com/docs/en/gitlab-ci-cd>) |
| Get automatic code review on every PR | [GitHub Code Review](<https://code.claude.com/docs/en/code-review>) |
| Route bug reports from Slack to pull requests | [Slack](<https://code.claude.com/docs/en/slack>) |
| Debug live web applications | [Chrome](<https://code.claude.com/docs/en/chrome>) |
| Build custom agents for your own workflows | [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>) |

## Next steps

Once you've installed Claude Code, these guides help you go deeper.

* [Quickstart](<https://code.claude.com/docs/en/quickstart>): walk through your first real task, from exploring a codebase to committing a fix
* [Store instructions and memories](<https://code.claude.com/docs/en/memory>): give Claude persistent instructions with CLAUDE.md files and auto memory
* [Common workflows](<https://code.claude.com/docs/en/common-workflows>) and [best practices](<https://code.claude.com/docs/en/best-practices>): patterns for getting the most out of Claude Code
* [Claude Academy](<https://academy.claude.com/>): free self-paced courses, including [Claude Code 101](<https://academy.claude.com/courses/claude-code-101>) and [Claude Code in Action](<https://academy.claude.com/courses/claude-code-in-action>)
* [A harness for every task](<https://claude.com/blog/a-harness-for-every-task-dynamic-workflows-in-claude-code>): how the Claude Code team uses [dynamic workflows](<https://code.claude.com/docs/en/workflows>) to orchestrate many subagents at once
* [Settings](<https://code.claude.com/docs/en/settings>): customize Claude Code for your workflow
* [Troubleshooting](<https://code.claude.com/docs/en/troubleshooting>): solutions for common issues
* [code.claude.com](<https://code.claude.com/>): demos, pricing, and product details


</details>
