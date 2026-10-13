# Gemini CLI 检查点

介绍工具修改前保存状态、查看与恢复检查点的流程，说明检查点的作用范围和限制。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-04-10](<https://geminicli.com/docs/cli/checkpointing/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/checkpointing/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 检查点.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

Checkpointing | Gemini CLI
  Skip to content   
Unpaid tier and Google One users: Gemini CLI was replaced by Antigravity
    CLI on June 18th, 2026。要了解更多信息，请参阅我们的博客文章。
 Gemini CLI 
 主页 
 计划 
 扩展 
 浏览扩展 
 关于扩展 
文档 
 参考 
 资源 
 变更日志搜索 CtrlK 取消反馈 GitHub GitHub 选择主题 DarkLightAuto 
 主页
 计划 
 扩展 
 浏览扩展 
 关于扩展 
 文档 
 开始使用 
 概述 
 快速入门
 安装 
 身份验证 
 CLI 备忘单 
 Gemini 3 在 Gemini CLI 
 使用Gemini CLI 
 文件管理 
 开始使用 Agent Skills 
 管理上下文和内存 
 执行 shell 命令 
 管理会话和历史记录 
 规划任务待办事项 
 使用计划模式进行模型引导 🔬 
 网络搜索和获取 
 设置 MCP 服务器 
 自动执行任务 
 功能 
 扩展
 概述 
 用户指南：安装和管理 
 开发人员指南：构建扩展 
 开发人员指南：最佳实践 
 开发人员指南：发布 
 开发人员指南：参考 
 Agent Skills 
 概述 
 开始使用 Agent Skills 
 创建 Agent Skills 
 使用 Agent Skills 
 开发人员指南：最佳实践 
 自动内存 🔬 
 检查点 
 无头模式 
 Git 工作树 🔬 
 钩子 
 概述 
 参考
 IDE 集成 
 概述 
 开发人员指南：ACP 模式 
 MCP 服务器 
 概述 
 资源工具
 模型路由 
 模型选择 
 模型转向 🔬 
 通知 🔬 
 计划模式 
 子代理 
 远程子代理
 快退 
 沙箱 
 设置 
 遥测 
 令牌缓存 
 配置 
 自定义命令 
 企业配置
 忽略文件 (.geminiignore) 
 模型配置 
 项目上下文 (GEMINI.md) 
 设置 
 系统提示覆盖 
 主题
 可信文件夹 
 开发 
 行为评估 
 贡献指南 
 集成测试 
 问题和 PR 自动化 
 本地开发
 NPM 封装结构 
 参考 
 命令参考 
 配置参考 
 键盘快捷键 
 内存导入处理器 
 策略引擎
 工具参考 
 资源 
 FAQ 
 配额和定价 
 条款和隐私 
 疑难解答 
 卸载
 变更日志 
 发行说明 
 稳定版本 
 预览版本 
 开始使用 
 概述 
 快速入门 
 安装
 认证 
CLI 备忘单 
 Gemini 3 上 Gemini CLI 
 使用 Gemini CLI 
 文件管理 
 开始使用Agent Skills 
 管理上下文和内存 
 执行 shell 命令 
 管理会话和历史记录 
 使用待办事项计划任务 
 使用计划模式进行模型控制 🔬 
 Web 搜索和获取
 设置 MCP 服务器 
 自动化任务 
 功能 
 扩展 
 概述 
 用户指南：安装和管理 
 开发人员指南：构建扩展 
 开发者指南：最佳实践 
 开发者指南：发布 
 开发者指南：参考 
 Agent Skills 
 概述 
 开始使用 Agent Skills 
 创建 Agent Skills 
 使用 Agent Skills 
 开发人员指南：最佳实践 
 自动内存 🔬 
 检查点 
 无头模式
 Git 工作树🔬 
 挂钩 
 概述 
 参考 
 IDE 集成 
 概述 
 开发人员指南： ACP 模式 
 MCP 服务器 
 概述 
 资源工具 
 模型路由 
 模型选择 
 模型转向 🔬 
 通知 🔬 
 计划模式 
 子代理 
 远程子代理 
 倒带 
 沙盒 
 设置 
遥测 
 令牌缓存 
 配置 
 自定义命令 
 企业配置 
 忽略文件 (.geminiignore) 
 模型配置 
 项目上下文(GEMINI.md) 
 设置 
 系统提示覆盖 
 主题 
 受信任文件夹 
 开发 
 行为评估 
 贡献指南 
 集成测试 
 问题和 PR 自动化 
 本地开发 
 NPM 包结构 GitHub 选择主题 DarkLightAuto 
 参考 
 命令参考 
 配置参考 
 键盘快捷键 
 内存导入处理器 
 策略引擎 
 工具参考 GitHub 选择主题DarkLightAuto 
 资源 
 FAQ 
 配额和定价 
 条款和隐私 
 疑难解答 
 卸载GitHub 选择主题 DarkLightAuto 
 发布 
 发行说明 
 稳定版本 
 预览版本 GitHub 选择主题 DarkLightAuto 在此页面
 简介 
 工作原理 
 启用该功能 
 使用 /restore 命令 
 列出可用检查点 
 恢复特定检查点
本页 
 简介 
 工作原理 
 启用该功能 
 使用 /restore 命令 
 列出可用检查点 
 恢复具体检查点 
 检查点复制为 Markdown 复制！             
Gemini CLI 包括检查点功能，在由 AI- 驱动的 
 工具进行任何文件修改之前，该功能会自动保存项目状态的快照 
。这可以让您安全地试验和应用代码更改，了解您
可以立即恢复到工具运行之前的状态。

它是如何工作的 worksSection 标题为“它是如何工作的”

当您批准修改文件系统的工具（例如write_file或
替换）时，CLI会自动创建一个“检查点”。该检查点
包括：

Git 快照：提交是在位于您的主目录 (~/.gemini/history/<project_hash>) 中的特殊影子 Git 存储库 
 中进行的。此
快照捕获了您项目文件当时的完整状态。
它不会干扰您自己项目的Git存储库。

对话历史记录：保存您迄今为止与 agent
 的整个对话。

工具调用：还存储了即将执行的特定工具调用
。

如果您想撤消更改或只是返回，可以使用 /restore
 命令。恢复检查点将：

将项目中的所有文件恢复到快照中捕获的状态。

恢复CLI中的对话历史记录。

重新提出原始工具调用，允许您再次运行它、修改它，或者
简单地忽略它。

所有检查点数据，包括 Git 快照和对话历史记录，都
本地存储在您的计算机上。 Git 快照存储在影子 
 存储库中，而对话历史记录和工具调用则保存在项目临时目录中的 JSON
 文件中，通常位于 
~/.gemini/tmp/<project_hash>/checkpoints.

启用标题为“启用该功能”的 featureSection

默认情况下禁用检查点功能。要启用它，您需要编辑
您的settings.json文件。

危险
版本
0.11.0中删除了--checkpointing命令行标志。现在只能通过 settings.json
 配置文件启用检查点。

将以下密钥添加到您的settings.json：

{  "general": {    "checkpointing": {      "enabled": true    }  }}

使用标题为“使用 /restore 命令”的 /restore commandSection

启用后，将自动创建检查点。要管理它们，您可以使用
/restore命令。

标题为“列出可用检查点”的可用列表 checkpointsSection

要查看当前项目的所有已保存检查点的列表，只需运行：

/restore

CLI 将显示可用检查点文件的列表。这些文件名是
，通常由时间戳、正在修改的文件的名称和即将运行的工具的
名称组成（例如例如，
2025-06-22T10-00-00_000Z-my-file.txt-write_file）。

恢复标题为“恢复特定检查点”的特定 checkpointSection

要将项目恢复到特定检查点，请使用 
 列表中的检查点文件：

/restore <checkpoint_file>

例如：

/restore 2025-06-22T10-00-00_000Z-my-file.txt-write_file

运行该命令后，您的文件和对话将立即
恢复到创建检查点时的状态，并且
原始工具提示符将重新出现。   最后更新时间：4 月 10、2026 
本网站使用 Google 的 cookie 来提供和提高其服务质量并分析 
 流量。
 
我明白。
 条款 | 隐私


## 原文对照

<details>
<summary>展开完整原文</summary>

Checkpointing | Gemini CLI
  Skip to content   
Unpaid tier and Google One users: Gemini CLI was replaced by Antigravity
    CLI on June 18th, 2026. To learn more, see our blog post.
                    Gemini CLI      
  Home  
  Plans  
   Extensions    
  Browse extensions  
  About Extensions     
  Docs  
  Reference  
  Resources  
  Changelog              Search  CtrlK       Cancel                 Feedback  GitHub  GitHub       Select theme   DarkLightAuto                      
  Home   
  Plans   
    Extensions       
  Browse extensions   
  About Extensions       
    Docs       
    Get started       
  Overview   
  Quickstart   
  Installation   
  Authentication   
  CLI cheatsheet   
  Gemini 3 on Gemini CLI       
    Use Gemini CLI       
  File management   
  Get started with Agent Skills   
  Manage context and memory   
  Execute shell commands   
  Manage sessions and history   
  Plan tasks with todos   
  Use Plan Mode with model steering 🔬  
  Web search and fetch   
  Set up an MCP server   
  Automate tasks       
    Features       
    Extensions       
  Overview   
  User guide: Install and manage   
  Developer guide: Build extensions   
  Developer guide: Best practices   
  Developer guide: Releasing   
  Developer guide: Reference       
    Agent Skills       
  Overview   
  Get started with Agent Skills   
  Creating Agent Skills   
  Using Agent Skills   
  Developer guide: Best practices       
  Auto Memory 🔬  
  Checkpointing   
  Headless mode   
  Git worktrees 🔬  
    Hooks       
  Overview   
  Reference       
    IDE integration       
  Overview   
  Developer guide: ACP mode       
    MCP servers       
  Overview   
  Resource tools       
  Model routing   
  Model selection   
  Model steering 🔬  
  Notifications 🔬  
  Plan mode   
  Subagents   
  Remote subagents   
  Rewind   
  Sandboxing   
  Settings   
  Telemetry   
  Token caching       
    Configuration       
  Custom commands   
  Enterprise configuration   
  Ignore files (.geminiignore)   
  Model configuration   
  Project context (GEMINI.md)   
  Settings   
  System prompt override   
  Themes   
  Trusted folders       
    Development       
  Behavioral evaluations   
  Contribution guide   
  Integration testing   
  Issue and PR automation   
  Local development   
  NPM package structure           
    Reference       
  Command reference   
  Configuration reference   
  Keyboard shortcuts   
  Memory import processor   
  Policy engine   
  Tools reference       
    Resources       
  FAQ   
  Quota and pricing   
  Terms and privacy   
  Troubleshooting   
  Uninstall       
    Changelog       
  Release notes   
  Stable release   
  Preview release                
    Get started       
  Overview   
  Quickstart   
  Installation   
  Authentication   
  CLI cheatsheet   
  Gemini 3 on Gemini CLI       
    Use Gemini CLI       
  File management   
  Get started with Agent Skills   
  Manage context and memory   
  Execute shell commands   
  Manage sessions and history   
  Plan tasks with todos   
  Use Plan Mode with model steering 🔬  
  Web search and fetch   
  Set up an MCP server   
  Automate tasks       
    Features       
    Extensions       
  Overview   
  User guide: Install and manage   
  Developer guide: Build extensions   
  Developer guide: Best practices   
  Developer guide: Releasing   
  Developer guide: Reference       
    Agent Skills       
  Overview   
  Get started with Agent Skills   
  Creating Agent Skills   
  Using Agent Skills   
  Developer guide: Best practices       
  Auto Memory 🔬  
  Checkpointing   
  Headless mode   
  Git worktrees 🔬  
    Hooks       
  Overview   
  Reference       
    IDE integration       
  Overview   
  Developer guide: ACP mode       
    MCP servers       
  Overview   
  Resource tools       
  Model routing   
  Model selection   
  Model steering 🔬  
  Notifications 🔬  
  Plan mode   
  Subagents   
  Remote subagents   
  Rewind   
  Sandboxing   
  Settings   
  Telemetry   
  Token caching       
    Configuration       
  Custom commands   
  Enterprise configuration   
  Ignore files (.geminiignore)   
  Model configuration   
  Project context (GEMINI.md)   
  Settings   
  System prompt override   
  Themes   
  Trusted folders       
    Development       
  Behavioral evaluations   
  Contribution guide   
  Integration testing   
  Issue and PR automation   
  Local development   
  NPM package structure                 GitHub     Select theme   DarkLightAuto                
    Reference       
  Command reference   
  Configuration reference   
  Keyboard shortcuts   
  Memory import processor   
  Policy engine   
  Tools reference                 GitHub     Select theme   DarkLightAuto                
    Resources       
  FAQ   
  Quota and pricing   
  Terms and privacy   
  Troubleshooting   
  Uninstall                 GitHub     Select theme   DarkLightAuto                
    Releases       
  Release notes   
  Stable release   
  Preview release                 GitHub     Select theme   DarkLightAuto                         On this page 
  Introduction   
  How it works   
  Enabling the feature   
  Using the /restore command   
  List available checkpoints   
  Restore a specific checkpoint        
On this page 
  Introduction   
  How it works   
  Enabling the feature   
  Using the /restore command   
  List available checkpoints   
  Restore a specific checkpoint                    
 Checkpointing     Copy as Markdown Copied!             
Gemini CLI includes a Checkpointing feature that automatically saves a snapshot
of your project’s state before any file modifications are made by AI-powered
tools. This lets you safely experiment with and apply code changes, knowing you
can instantly revert back to the state before the tool was run.

How it worksSection titled “How it works”

When you approve a tool that modifies the file system (like write_file or
replace), the CLI automatically creates a “checkpoint.” This checkpoint
includes:

A Git snapshot: A commit is made in a special, shadow Git repository
located in your home directory (~/.gemini/history/<project_hash>). This
snapshot captures the complete state of your project files at that moment.
It does not interfere with your own project’s Git repository.

Conversation history: The entire conversation you’ve had with the agent
up to that point is saved.

The tool call: The specific tool call that was about to be executed is
also stored.

If you want to undo the change or simply go back, you can use the /restore
command. Restoring a checkpoint will:

Revert all files in your project to the state captured in the snapshot.

Restore the conversation history in the CLI.

Re-propose the original tool call, allowing you to run it again, modify it, or
simply ignore it.

All checkpoint data, including the Git snapshot and conversation history, is
stored locally on your machine. The Git snapshot is stored in the shadow
repository while the conversation history and tool calls are saved in a JSON
file in your project’s temporary directory, typically located at
~/.gemini/tmp/<project_hash>/checkpoints.

Enabling the featureSection titled “Enabling the feature”

The Checkpointing feature is disabled by default. To enable it, you need to edit
your settings.json file.

Danger
The --checkpointing command-line flag was removed in version
0.11.0. Checkpointing can now only be enabled through the settings.json
configuration file.

Add the following key to your settings.json:

{  "general": {    "checkpointing": {      "enabled": true    }  }}

Using the /restore commandSection titled “Using the /restore command”

Once enabled, checkpoints are created automatically. To manage them, you use the
/restore command.

List available checkpointsSection titled “List available checkpoints”

To see a list of all saved checkpoints for the current project, simply run:

/restore

The CLI will display a list of available checkpoint files. These file names are
typically composed of a timestamp, the name of the file being modified, and the
name of the tool that was about to be run (for example,
2025-06-22T10-00-00_000Z-my-file.txt-write_file).

Restore a specific checkpointSection titled “Restore a specific checkpoint”

To restore your project to a specific checkpoint, use the checkpoint file from
the list:

/restore <checkpoint_file>

For example:

/restore 2025-06-22T10-00-00_000Z-my-file.txt-write_file

After running the command, your files and conversation will be immediately
restored to the state they were in when the checkpoint was created, and the
original tool prompt will reappear.   Last updated: Apr 10, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy


</details>
