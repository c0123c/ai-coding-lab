# Gemini CLI Git 工作树

介绍实验性的工作树隔离和并行开发方式，说明启动、使用和清理时的条件。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-03-20](<https://geminicli.com/docs/cli/git-worktrees/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/git-worktrees/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI Git 工作树.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

Git Worktrees（实验性）| Gemini CLI
 跳到内容 
未付费层和 Google 一名用户：Gemini CLI 于 6 月被 Antigravity 
 CLI 取代18日，2026。要了解更多信息，请参阅我们的博客文章。
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
 如何启用 Git 工作树 
 如何使用 Git 工作树 
 如何退出 Git 工作树会话 
 在 Git 工作树中恢复工作 
 手动管理 Git 工作树
在此页 
 简介 
 如何启用 Git 工作树 
 如何使用 Git 工作树 
 如何退出 Git 工作树会话 
 在 Git 工作树中恢复工作
 手动管理 Git 工作树 
 Git 工作树（实验）复制为 Markdown 复制！             
同时处理多个任务时，您可以使用 Git 工作树为每个 
Gemini 会话提供自己的代码库副本。 Git 工作树创建单独的
每个工作目录都有自己的文件和分支，同时共享
相同的存储库历史记录。这可以防止一个会话中的更改与另一个会话发生冲突 
。

了解有关会话管理的更多信息。

注意
这是一项目前正在积极开发的实验性功能。在我们完善此功能时，您的
反馈非常宝贵。如果您有想法、
建议，或遇到问题：

在 GitHub 上打开问题。

使用 Gemini CLI 中的 /bug 命令来提交问题。

请参阅官方 Git 工作树 
 文档了解更多信息。

如何启用 Git worktreesSection，标题为“如何启用 Git 工作树”

Git 工作树是一项实验性功能。您必须使用 /settings 命令或手动编辑 settings.json 文件在设置 
 中启用它们。

使用/settings命令。

搜索启用 Git 工作树并将其设置为 true。

或者，将以下内容添加到您的 settings.json：

{  "experimental": {    "worktrees": true  }}

如何使用 Git worktreesSection，标题为“如何使用 Git 工作树”

使用 --worktree (-w) 标志创建一个独立的工作树并在其中启动 Gemini
CLI。

以特定名称开头：您传递的值将成为目录
名称（在.gemini/worktrees/内）和分支名称。
终端窗口
gemini --worktree功能搜索

从随机名称开始：如果省略名称，Gemini 会自动生成一个随机的
（例如，worktree-a1b2c3d4）。
终端窗口
gemini --worktree

注意
请记住根据您的项目设置在每个新的
工作树中初始化您的开发环境。根据您的堆栈，此
可能包括运行依赖项安装（npm安装，yarn），设置
虚拟环境，或遵循项目的标准构建过程。

如何退出 Git 工作树 sessionSection 标题为“如何退出 Git 工作树会话”

当您退出工作树会话时（使用 /quit 或 Ctrl+C），Gemini 会完整保留 
 工作树，因此您的工作不会丢失。这包括您未提交的更改
（修改的文件、暂存的更改或未跟踪的文件）以及您
所做的任何新提交。

Gemini 优先考虑快速安全的退出：它不会自动删除
您的工作树或分支。完成工作树后，您有责任手动清理工作树
。

退出时，Gemini 会显示有关如何恢复工作或如何
 手动删除工作树（如果不再需要）的说明。

在 Git 中恢复工作 worktreeSection，标题为“在 Git 工作树中恢复工作”

要恢复工作树中的会话，请导航到工作树目录并启动
Gemini CLI 与 --resume 标志和会话 ID:
终端窗口
光盘。gemini/worktrees/feature-searchgemini --resume <session_id>

管理 Git 工作树 manuallySection 标题为“手动管理 Git 工作树”

为了更好地控制工作树位置和分支配置，或者清理
保留的工作树，您可以直接使用 Git：

清理保留的 Git 工作树：
终端窗口
git worktree remove .gemini/worktrees/feature-search --forcegitbranch -D worktree-feature-search

手动创建 Git 工作树：
Terminal window
git worktree add ../project-feature-search -b feature-searchcd ../project-feature-search && gemini

最后更新时间：3 月 20、2026 
本网站使用 Google 的 cookie 来提供和提高其服务质量并分析 
 流量。
 
我明白。
 条款 | 隐私


## 原文对照

<details>
<summary>展开完整原文</summary>

Git Worktrees (experimental) | Gemini CLI
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
  How to enable Git worktrees   
  How to use Git worktrees   
  How to exit a Git worktree session   
  Resuming work in a Git worktree   
  Managing Git worktrees manually     
On this page 
  Introduction   
  How to enable Git worktrees   
  How to use Git worktrees   
  How to exit a Git worktree session   
  Resuming work in a Git worktree   
  Managing Git worktrees manually                 
 Git Worktrees (experimental)     Copy as Markdown Copied!             
When working on multiple tasks at once, you can use Git worktrees to give each
Gemini session its own copy of the codebase. Git worktrees create separate
working directories that each have their own files and branch while sharing the
same repository history. This prevents changes in one session from colliding
with another.

Learn more about session management.

Note
This is an experimental feature currently under active development. Your
feedback is invaluable as we refine this feature. If you have ideas,
suggestions, or encounter issues:

Open an issue on GitHub.

Use the /bug command within Gemini CLI to file an issue.

Learn more in the official Git worktree
documentation.

How to enable Git worktreesSection titled “How to enable Git worktrees”

Git worktrees are an experimental feature. You must enable them in your settings
using the /settings command or by manually editing your settings.json file.

Use the /settings command.

Search for and set Enable Git Worktrees to true.

Alternatively, add the following to your settings.json:

{  "experimental": {    "worktrees": true  }}

How to use Git worktreesSection titled “How to use Git worktrees”

Use the --worktree (-w) flag to create an isolated worktree and start Gemini
CLI in it.

Start with a specific name: The value you pass becomes both the directory
name (within .gemini/worktrees/) and the branch name.
Terminal window
gemini --worktree feature-search

Start with a random name: If you omit the name, Gemini generates a random
one automatically (for example, worktree-a1b2c3d4).
Terminal window
gemini --worktree

Note
Remember to initialize your development environment in each new
worktree according to your project’s setup. Depending on your stack, this
might include running dependency installation (npm install, yarn), setting
up virtual environments, or following your project’s standard build process.

How to exit a Git worktree sessionSection titled “How to exit a Git worktree session”

When you exit a worktree session (using /quit or Ctrl+C), Gemini leaves the
worktree intact so your work is not lost. This includes your uncommitted changes
(modified files, staged changes, or untracked files) and any new commits you
have made.

Gemini prioritizes a fast and safe exit: it does not automatically delete
your worktree or branch. You are responsible for cleaning up your worktrees
manually once you are finished with them.

When you exit, Gemini displays instructions on how to resume your work or how to
manually remove the worktree if you no longer need it.

Resuming work in a Git worktreeSection titled “Resuming work in a Git worktree”

To resume a session in a worktree, navigate to the worktree directory and start
Gemini CLI with the --resume flag and the session ID:
Terminal window
cd .gemini/worktrees/feature-searchgemini --resume <session_id>

Managing Git worktrees manuallySection titled “Managing Git worktrees manually”

For more control over worktree location and branch configuration, or to clean up
a preserved worktree, you can use Git directly:

Clean up a preserved Git worktree:
Terminal window
git worktree remove .gemini/worktrees/feature-search --forcegit branch -D worktree-feature-search

Create a Git worktree manually:
Terminal window
git worktree add ../project-feature-search -b feature-searchcd ../project-feature-search && gemini

   Last updated: Mar 20, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy


</details>
