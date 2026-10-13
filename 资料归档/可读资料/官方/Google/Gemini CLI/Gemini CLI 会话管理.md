# Gemini CLI 会话管理

说明保存、恢复和切换历史任务的方式，帮助在不同开发会话间持续工作并管理记录。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-06-18](<https://geminicli.com/docs/cli/session-management/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/session-management/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 会话管理.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

会话管理 | Gemini CLI
 跳到内容 
未付费层和 Google 一名用户：Gemini CLI 于 6 月 18 日被反重力 
 CLI 取代， 2026。要了解更多信息，请参阅我们的博客文章。
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
 自动保存 
 恢复会话 
 从命令行 
 从交互界面 
 手动聊天检查点 
 与 Git 工作树的并行会话
 管理会话 
 列出会话 
 删除会话 
 配置 
 会话保留 
 会话限制 
 后续步骤 
在此页面
 简介 
 自动保存 
 恢复会话 
 从命令行 
 从交互界面 
 手动聊天检查点 
 与 Git 工作树的并行会话
 管理会话 
 列出会话 
删除会话 
 配置 
 会话保留 
 会话限制 
 后续步骤 
 会话管理 复制为 Markdown 已复制！             
会话管理会保存您的对话历史记录，以便您可以从中断处继续工作
。使用这些功能可以查看过去的交互、管理不同项目的
历史记录以及配置数据的保留时间。

自动 savingSection 标题为“自动保存”

当您与模型交互时，系统会自动记录您的会话历史记录。
此后台进程可确保即使您中断
会话，您的工作也会得到保留。

保存的内容：完整的对话历史记录，包括：

您的提示和模特的回应。

所有工具执行（输入和输出）。

令牌使用统计（输入、输出、缓存等）。

辅助想法和推理摘要（如果有）。

位置：会话存储在 ~/.gemini/tmp/<project_hash>/chats/、
 中，其中 <project_hash> 是基于项目根 
 目录的唯一标识符。

范围：会议针对特定项目。将目录切换到不同的
项目会切换到该项目的会话历史记录。

恢复 sessionsSection 标题为“恢复会话”

您可以恢复之前的会话以继续对话，同时恢复所有先前的 
 上下文。通过命令行标志和
交互式浏览器支持恢复。

来自标题为“来自命令行”的命令 lineSection

启动 Gemini CLI 时，使用 --resume（或 -r）标志加载现有的 
 会话。

最新简历：
终端窗口
gemini --resume

这会立即加载最近的会话。

按索引恢复：首先列出可用会话（参见
列出会话），然后使用索引号：
终端窗口
gemini --resume 1

通过ID恢复：您还可以提供完整的会话UUID：
终端窗口
gemini --resume a1b2c3d4-e5f6-7890-abcd-ef1234567890

来自标题为“来自交互界面”的交互 interfaceSection

当 CLI 运行时，使用 /resume 斜线命令打开会话 
 浏览器：

/resume

在斜杠补全中键入 /resume（或 /chat）时，命令将在标题分隔符下分组为 
：

-- 自动 --（会话浏览器）

列表可选择并打开会话浏览器

-- 检查点 --（手动标记检查点命令）

/resum 和 /cha 等唯一前缀解析为同一分组菜单。

会话浏览器提供了一个交互式界面，您可以在其中执行
以下操作：

浏览：滚动浏览您过去的会话列表。

预览：查看会话日期、消息计数和第一个 
 用户提示等详细信息。

搜索：按 / 进入搜索模式，然后键入按 ID
 或内容过滤会话。

选择：按 Enter 键恢复选定的会话。

Esc：按 Esc 退出会话浏览器。

手动聊天 checkpointsSection 标题为“手动聊天检查点”

对于会话内的命名分支点，请使用聊天检查点：

/resume 保存 decision-point/resume list/resume 恢复决策点

兼容性别名：

/chat ...适用于相同的命令。

/resume 检查点...在迁移期间也仍然受支持。

与 Git 的并行会话 worktreesSection，标题为“与 Git 工作树的并行会话”

当同时处理多个任务时，您可以使用 
Git 工作树为每个 Gemini 会话提供自己的 
 代码库副本。这可以防止一个会话中的更改与另一个会话发生冲突。

管理标题为“管理会话”的 sessionsSection

您可以列出和删除会话以组织历史记录并管理磁盘
空间。

上市 sessionsSection 标题为“上市会议”

要从命令
行查看当前项目的所有可用会话的列表，请使用--list-sessions标志：
终端窗口
gemini --list-sessions

输出示例：

该项目的可用会话（3）：
  1. 修复身份验证中的错误（2天前）[a1b2c3d4]2。重构数据库架构（5 小时前）[e5f67890] 3。更新文档（刚刚）[abcd1234]

删除标题为“删除会话”的 sessionsSection

您可以删除旧的或不需要的会话以释放空间或整理您的
历史记录。

从命令行：使用带索引的 --delete-session 标志或 ID:
终端窗口
gemini --delete-session 2

从会话浏览器：

用/resume.打开浏览器

导航到您要删除的会话。

按 x。

ConfigurationSection 标题为“配置”

您可以在
settings.json文件中配置Gemini CLI如何管理您的会话历史记录。这些设置可让您控制保留策略和
会话长度。

标题为“会话保留”的会话 retentionSection

默认情况下，Gemini CLI会自动清理旧的会话数据，以防止您的
历史记录无限期增长。删除会话时，Gemini CLI 还会
 删除所有关联数据，包括实施计划、任务跟踪器、工具
输出和活动日志。

默认策略是将会话保留 30 天。

ConfigurationSection 标题为“配置”

您可以使用 /settings 命令或手动 
 编辑 settings.json 文件来自定义这些策略：

{  "general": {    "sessionRetention": {      "enabled": true,      "maxAge": "30d",      "maxCount": 50    }  }}

启用：（布尔值）会话清理的主开关。默认为
true。

maxAge：（字符串）保持会话的持续时间（例如，“24h”、“7d”、
“4w”）。早于此的会话将被删除。默认为“30d”。

maxCount：（数字）要保留的最大会话数。超过此计数的最旧的
会话将被删除。默认为未定义（无限制）。

minRetention：（字符串）最短保留期限（安全限制）。默认
为“1d”。自动
清理永远不会删除比该时间段更新的会话。

标题为“会话限制”的会话 limitsSection

您可以限制单个会话的长度，以防止上下文窗口
变得太大和昂贵。

{  "model": {    "maxSessionTurns": 100  }}

maxSessionTurns：（数字）单个会话中允许的最大轮数（用户和模型
交换）。设置为 -1 表示无限制（默认）。

达到限制时的行为：

交互模式：CLI 显示一条信息性消息并停止 
 向模型发送请求。您必须手动启动新会话。

非交互模式：CLI错误退出。

下一个 stepsSection 标题为“后续步骤”

探索内存工具以跨会话保存持久信息
。

了解如何检查会话状态。

查看所有命令行标志的 CLI 参考。
 最后更新：Jun 18、2026 
本网站使用 Google 的 cookie 来提供和增强其服务质量，并分析
流量。
 
我明白。
 条款 | 隐私


## 原文对照

<details>
<summary>展开完整原文</summary>

Session management | Gemini CLI
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
  Automatic saving   
  Resuming sessions   
  From the command line   
  From the interactive interface   
  Manual chat checkpoints      
  Parallel sessions with Git worktrees   
  Managing sessions   
  Listing sessions   
  Deleting sessions      
  Configuration   
  Session retention   
  Session limits      
  Next steps     
On this page 
  Introduction   
  Automatic saving   
  Resuming sessions   
  From the command line   
  From the interactive interface   
  Manual chat checkpoints      
  Parallel sessions with Git worktrees   
  Managing sessions   
  Listing sessions   
  Deleting sessions      
  Configuration   
  Session retention   
  Session limits      
  Next steps                 
 Session management     Copy as Markdown Copied!             
Session management saves your conversation history so you can resume your work
where you left off. Use these features to review past interactions, manage
history across different projects, and configure how long data is retained.

Automatic savingSection titled “Automatic saving”

Your session history is recorded automatically as you interact with the model.
This background process ensures your work is preserved even if you interrupt a
session.

What is saved: The complete conversation history, including:

Your prompts and the model’s responses.

All tool executions (inputs and outputs).

Token usage statistics (input, output, cached, etc.).

Assistant thoughts and reasoning summaries (when available).

Location: Sessions are stored in ~/.gemini/tmp/<project_hash>/chats/,
where <project_hash> is a unique identifier based on your project’s root
directory.

Scope: Sessions are project-specific. Switching directories to a different
project switches to that project’s session history.

Resuming sessionsSection titled “Resuming sessions”

You can resume a previous session to continue the conversation with all prior
context restored. Resuming is supported both through command-line flags and an
interactive browser.

From the command lineSection titled “From the command line”

When starting Gemini CLI, use the --resume (or -r) flag to load existing
sessions.

Resume latest:
Terminal window
gemini --resume

This immediately loads the most recent session.

Resume by index: List available sessions first (see
Listing sessions), then use the index number:
Terminal window
gemini --resume 1

Resume by ID: You can also provide the full session UUID:
Terminal window
gemini --resume a1b2c3d4-e5f6-7890-abcd-ef1234567890

From the interactive interfaceSection titled “From the interactive interface”

While the CLI is running, use the /resume slash command to open the Session
Browser:

/resume

When typing /resume (or /chat) in slash completion, commands are grouped
under titled separators:

-- auto -- (session browser)

list is selectable and opens the session browser

-- checkpoints -- (manual tagged checkpoint commands)

Unique prefixes such as /resum and /cha resolve to the same grouped menu.

The Session Browser provides an interactive interface where you can perform the
following actions:

Browse: Scroll through a list of your past sessions.

Preview: See details like the session date, message count, and the first
user prompt.

Search: Press / to enter search mode, then type to filter sessions by ID
or content.

Select: Press Enter to resume the selected session.

Esc: Press Esc to exit the Session Browser.

Manual chat checkpointsSection titled “Manual chat checkpoints”

For named branch points inside a session, use chat checkpoints:

/resume save decision-point/resume list/resume resume decision-point

Compatibility aliases:

/chat ... works for the same commands.

/resume checkpoints ... also remains supported during migration.

Parallel sessions with Git worktreesSection titled “Parallel sessions with Git worktrees”

When working on multiple tasks at once, you can use
Git worktrees to give each Gemini session its own copy of
the codebase. This prevents changes in one session from colliding with another.

Managing sessionsSection titled “Managing sessions”

You can list and delete sessions to keep your history organized and manage disk
space.

Listing sessionsSection titled “Listing sessions”

To see a list of all available sessions for the current project from the command
line, use the --list-sessions flag:
Terminal window
gemini --list-sessions

Output example:

Available sessions for this project (3):
  1. Fix bug in auth (2 days ago) [a1b2c3d4]  2. Refactor database schema (5 hours ago) [e5f67890]  3. Update documentation (Just now) [abcd1234]

Deleting sessionsSection titled “Deleting sessions”

You can remove old or unwanted sessions to free up space or declutter your
history.

From the command line: Use the --delete-session flag with an index or ID:
Terminal window
gemini --delete-session 2

From the Session Browser:

Open the browser with /resume.

Navigate to the session you want to remove.

Press x.

ConfigurationSection titled “Configuration”

You can configure how Gemini CLI manages your session history in your
settings.json file. These settings let you control retention policies and
session lengths.

Session retentionSection titled “Session retention”

By default, Gemini CLI automatically cleans up old session data to prevent your
history from growing indefinitely. When a session is deleted, Gemini CLI also
removes all associated data, including implementation plans, task trackers, tool
outputs, and activity logs.

The default policy is to retain sessions for 30 days.

ConfigurationSection titled “Configuration”

You can customize these policies using the /settings command or by manually
editing your settings.json file:

{  "general": {    "sessionRetention": {      "enabled": true,      "maxAge": "30d",      "maxCount": 50    }  }}

enabled: (boolean) Master switch for session cleanup. Defaults to
true.

maxAge: (string) Duration to keep sessions (for example, “24h”, “7d”,
“4w”). Sessions older than this are deleted. Defaults to "30d".

maxCount: (number) Maximum number of sessions to retain. The oldest
sessions exceeding this count are deleted. Defaults to undefined (unlimited).

minRetention: (string) Minimum retention period (safety limit). Defaults
to "1d". Sessions newer than this period are never deleted by automatic
cleanup.

Session limitsSection titled “Session limits”

You can limit the length of individual sessions to prevent context windows from
becoming too large and expensive.

{  "model": {    "maxSessionTurns": 100  }}

maxSessionTurns: (number) The maximum number of turns (user and model
exchanges) allowed in a single session. Set to -1 for unlimited (default).

Behavior when limit is reached:

Interactive mode: The CLI shows an informational message and stops
sending requests to the model. You must manually start a new session.

Non-interactive mode: The CLI exits with an error.

Next stepsSection titled “Next steps”

Explore the Memory tool to save persistent information
across sessions.

Learn how to Checkpoint your session state.

Check out the CLI reference for all command-line flags.
   Last updated: Jun 18, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy


</details>
