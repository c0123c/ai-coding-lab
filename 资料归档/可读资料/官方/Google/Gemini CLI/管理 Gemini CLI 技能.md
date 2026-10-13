# 管理 Gemini CLI 技能

介绍查看、安装、启停和使用技能的流程，以及技能发现和作用范围的规则。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-04-30](<https://geminicli.com/docs/cli/using-agent-skills/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/using-agent-skills/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/管理 Gemini CLI 技能.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

管理 Agent Skills | Gemini CLI
 跳到内容 
未付费层和 Google 一名用户：Gemini CLI 于 6 月被反重力 
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
 发现层 
 会话中管理 
 终端实用程序 
 安装技能 
 开发链接 
 卸载技能 
安全和同意 
 后续步骤 
本页 
 简介 
 发现层 
 会话中管理 
 终端实用程序 
 安装技能
 开发链接 
 卸载技能 
 安全和同意 
 后续步骤 
 管理 Agent Skills 复制为 Markdown 已复制！             
Agent Skills 为Gemini CLI 按需提供专业知识。本指南
涵盖高级管理技术，包括使用斜杠命令、终端
实用程序，并了解发现层。

Discovery tiersSection 标题为“Discovery tiers”

Gemini CLI 从多个位置发现技能，遵循特定的优先顺序 
（从最低到最高）：

内置技能：包含在Gemini CLI中并且始终可用。

扩展技能：捆绑在扩展中。

用户技能：位于~/.gemini/skills/或~/.agents/skills/
别名中。这些在您的所有项目中都可用。

工作区技能：位于当前目录中的 .gemini/skills/ 或 .agents/skills/
 别名中。这些是特定于项目的。

提示：如果多个技能共享相同的名称，则使用 
 优先级较高位置的版本。

会话中 managementSection 标题为“会话中管理”

在交互式会话期间使用 /skills 斜线命令来管理您的 
 可用专业知识。

列表技能：/skills列表（显示已发现的技能）。

使用/skills列出所有包含内部内置技能。

使用 /skills 列表节点隐藏描述。

重新加载技能：/skills重新加载（或/skills刷新）以扫描新的或
修改的技能，而无需重新启动CLI。

切换状态：

/skills禁用<name>：防止技能被触发。

/skills启用<name>：重新启用禁用的技能。

链接本地技能：/skills链接<path>[--scope用户|工作空间]到
立即使用您正在开发的技能。

终端 utilitiesSection 标题为“终端实用程序”

Gemini Skills 命令直接从您的 
 系统 shell 提供管理实用程序。

安装标题为“安装技能”的 skillSection

要从远程存储库或本地 .skill 包安装技能：
终端窗口
gemini Skills install https://github.com/user/my-awesome-skill

默认情况下，这会安装到您的用户配置文件中。使用--scope工作空间
仅为当前项目安装它。

developmentSection 标题为“开发链接”的链接

如果您正在开发技能，请使用 link 命令创建对 
本地目录的引用：
终端窗口
gemini 技能链接。/path/to/my-skill

卸载标题为“卸载技能”的skillSection

要完全删除已安装或链接的技能：
终端窗口
gemini 技能卸载 <name>

安全和 consentSection 标题为“安全和同意”

Agent Skills 可以执行脚本并访问您的文件。为了保护您的
环境：

安装同意：从远程URL安装时，系统将
要求您确认来源。

激活同意：每次在会话期间触发技能时，
agent 必须请求许可才能激活它并获得对其
资源的访问权限。

下一个 stepsSection 标题为“后续步骤”

开始使用 Agent Skills：创建第一个技能的 
 演练。

创建 Agent Skills：有关捆绑 
 脚本和资产的详细指南。

技能最佳实践：建立
可靠专业知识的策略。
最后更新：四月30，2026
该网站使用来自Google的cookie来提供和提高其服务质量，并分析
流量。
 
我明白。
 条款 | 隐私


## 原文对照

<details>
<summary>展开完整原文</summary>

Managing Agent Skills | Gemini CLI
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
  Discovery tiers   
  In-session management   
  Terminal utilities   
  Install a skill   
  Link for development   
  Uninstall a skill      
  Security and consent   
  Next steps     
On this page 
  Introduction   
  Discovery tiers   
  In-session management   
  Terminal utilities   
  Install a skill   
  Link for development   
  Uninstall a skill      
  Security and consent   
  Next steps                 
 Managing Agent Skills     Copy as Markdown Copied!             
Agent Skills provide Gemini CLI with specialized expertise on demand. This guide
covers advanced management techniques, including using slash commands, terminal
utilities, and understanding discovery tiers.

Discovery tiersSection titled “Discovery tiers”

Gemini CLI discovers skills from several locations, following a specific order
of precedence (lowest to highest):

Built-in Skills: Included with Gemini CLI and always available.

Extension Skills: Bundled within extensions.

User Skills: Located in ~/.gemini/skills/ or the ~/.agents/skills/
alias. These are available across all your projects.

Workspace Skills: Located in .gemini/skills/ or the .agents/skills/
alias within your current directory. These are project-specific.

Tip: If multiple skills share the same name, the version from the
higher-precedence location is used.

In-session managementSection titled “In-session management”

Use the /skills slash command during an interactive session to manage your
available expertise.

List skills: /skills list (shows discovered skills).

Use /skills list all to include internal built-in skills.

Use /skills list nodesc to hide descriptions.

Reload skills: /skills reload (or /skills refresh) to scan for new or
modified skills without restarting the CLI.

Toggle status:

/skills disable <name>: Prevents a skill from being triggered.

/skills enable <name>: Re-enables a disabled skill.

Link local skills: /skills link <path> [--scope user|workspace] to
immediately use a skill you are developing.

Terminal utilitiesSection titled “Terminal utilities”

The gemini skills command provides management utilities directly from your
system shell.

Install a skillSection titled “Install a skill”

To install a skill from a remote repository or a local .skill package:
Terminal window
gemini skills install https://github.com/user/my-awesome-skill

By default, this installs to your user profile. Use --scope workspace to
install it only for the current project.

Link for developmentSection titled “Link for development”

If you are developing a skill, use the link command to create a reference to
your local directory:
Terminal window
gemini skills link ./path/to/my-skill

Uninstall a skillSection titled “Uninstall a skill”

To completely remove an installed or linked skill:
Terminal window
gemini skills uninstall <name>

Security and consentSection titled “Security and consent”

Agent Skills can execute scripts and access your files. To protect your
environment:

Installation consent: When installing from a remote URL, you will be
asked to confirm the source.

Activation consent: Every time a skill is triggered during a session,
the agent must ask for permission to activate it and gain access to its
resources.

Next stepsSection titled “Next steps”

Get started with Agent Skills: A
walkthrough for creating your first skill.

Creating Agent Skills: Detailed guide on bundling
scripts and assets.

Skill best practices: Strategies for building
reliable expertise.
   Last updated: Apr 30, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy


</details>
