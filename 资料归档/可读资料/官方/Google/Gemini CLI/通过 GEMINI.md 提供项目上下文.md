# 通过 GEMINI.md 提供项目上下文

介绍项目说明文件的位置、作用范围和加载机制，使用实例描述开发约定与编码规范。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-06-18](<https://geminicli.com/docs/cli/gemini-md/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/gemini-md/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/gemini-md.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

提供 GEMINI.md 文件的上下文 | Gemini CLI
 跳到内容 
未付费层和 Google 一名用户：Gemini CLI 已被 Antigravity 取代 
 CLI 6 月 18 日，2026。要了解更多信息，请参阅我们的博客文章。
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
 了解上下文层次结构 
 示例 GEMINI.md 文件 
 使用 /memory 命令管理上下文 
 通过导入模块化上下文 
 自定义上下文文件名 
 后续步骤 
本页 
 简介 
 了解上下文层次结构 
 示例 GEMINI.md 文件 
 使用/memory 命令 
 通过导入模块化上下文 
 自定义上下文文件名 
 后续步骤 
 使用 GEMINI.md 文件提供上下文 复制为 Markdown 已复制！             
上下文文件使用默认名称GEMINI.md，是一个强大的功能
用于为 Gemini 模型提供教学背景。您可以使用这些文件
提供特定于项目的说明、定义角色或提供编码风格
指南，以使AI的响应更加准确并根据您的需求进行定制。

您可以在 
 上下文文件中定义一次指令，而不是在每个提示中重复指令。

理解上下文 hierarchySection，标题为“理解上下文层次结构”

CLI 使用分层系统来获取上下文。它从多个位置加载各种上下文
文件，连接所有找到的文件的内容，并且
在每次提示时将它们发送到模型。 CLI按以下
顺序加载文件：

全局上下文文件：

位置：~/.gemini/GEMINI.md（在您的用户主目录中）。

范围：为您的所有项目提供默认说明。

环境和工作区上下文文件：

位置：CLI 在您配置的
工作空间目录及其父目录中搜索GEMINI.md 文件。

范围：提供与您当前
正在处理的项目相关的上下文。

即时 (JIT) 上下文文件：

位置：当工具访问文件或目录时，CLI
 会自动扫描该目录中的 GEMINI.md 文件及其 
 祖先，直至受信任的根目录。

范围：让模型仅在需要时发现针对 
 特定组件的高度具体的指令。

CLI 页脚显示加载的上下文文件的数量，这为您提供了 
 活动教学上下文的快速视觉提示。

示例 GEMINI.md fileSection 标题为“示例 GEMINI.md 文件”

以下是您可以在 
a TypeScript 项目根部的 GEMINI.md 文件中包含的内容的示例：

# 项目：我的TypeScript图书馆
## 一般说明
- 当您生成新的 TypeScript 代码时，请遵循现有的编码风格。- 确保所有新函数和类都有 JSDoc 注释。- 在适当的情况下首选函数式编程范例。
## 编码风格
- 使用 2 空格进行缩进。- 在接口名称前添加 `I` 前缀（例如，`IUserService`）。- 始终使用严格相等（`===` 和 `!==`）。

使用标题为“使用 /memory 命令管理上下文”的 /memory commandSection 管理上下文

您可以使用 /memory 命令与加载的上下文文件进行交互。

/memory show：显示当前
分层内存的完整串联内容。这使您可以检查提供给模型的确切教学上下文 
。

/memory 重新加载：强制从所有配置位置重新扫描并重新加载所有 GEMINI.md 文件 
。

使用标题为“通过导入模块化上下文”的 importsSection 来模块化上下文

您可以使用 @file.md 语法从其他文件导入内容，从而将大型 GEMINI.md 文件分解为更小、更易于管理的 
 组件。
此功能支持相对路径和绝对路径。

包含导入的示例 GEMINI.md：

# 主GEMINI.md文件
这是主要内容。
@./components/instructions.md
更多内容在这里。
@../shared/style-guide.md

有关更多详细信息，请参阅内存导入处理器
文档。

自定义标题为“自定义上下文文件名”的上下文文件 nameSection

虽然 GEMINI.md 是默认文件名，但您可以在 
settings.json 文件中配置它。要指定不同的名称或名称列表，请使用
context.fileName属性。

示例settings.json：

{  "context": {    "fileName": ["AGENTS.md", "CONTEXT.md", "GEMINI.md"]  }}

下一个 stepsSection 标题为“后续步骤”

了解忽略文件以从 
 上下文系统中排除内容。

探索内存工具来保存持久记忆。

了解如何使用自定义命令来自动执行常见的 
 提示。
 最后更新时间：Jun 18、2026 
此网站使用来自 Google 的 cookie 来提供和增强其服务质量，并分析
流量。
 
我明白。
 条款 | 隐私


## 原文对照

<details>
<summary>展开完整原文</summary>

Provide context with GEMINI.md files | Gemini CLI
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
  Understand the context hierarchy   
  Example GEMINI.md file      
  Manage context with the /memory command   
  Modularize context with imports   
  Customize the context file name   
  Next steps     
On this page 
  Introduction   
  Understand the context hierarchy   
  Example GEMINI.md file      
  Manage context with the /memory command   
  Modularize context with imports   
  Customize the context file name   
  Next steps                 
 Provide context with GEMINI.md files     Copy as Markdown Copied!             
Context files, which use the default name GEMINI.md, are a powerful feature
for providing instructional context to the Gemini model. You can use these files
to give project-specific instructions, define a persona, or provide coding style
guides to make the AI’s responses more accurate and tailored to your needs.

Instead of repeating instructions in every prompt, you can define them once in a
context file.

Understand the context hierarchySection titled “Understand the context hierarchy”

The CLI uses a hierarchical system to source context. It loads various context
files from several locations, concatenates the contents of all found files, and
sends them to the model with every prompt. The CLI loads files in the following
order:

Global context file:

Location: ~/.gemini/GEMINI.md (in your user home directory).

Scope: Provides default instructions for all your projects.

Environment and workspace context files:

Location: The CLI searches for GEMINI.md files in your configured
workspace directories and their parent directories.

Scope: Provides context relevant to the projects you are currently
working on.

Just-in-time (JIT) context files:

Location: When a tool accesses a file or directory, the CLI
automatically scans for GEMINI.md files in that directory and its
ancestors up to a trusted root.

Scope: Lets the model discover highly specific instructions for
particular components only when they are needed.

The CLI footer displays the number of loaded context files, which gives you a
quick visual cue of the active instructional context.

Example GEMINI.md fileSection titled “Example GEMINI.md file”

Here is an example of what you can include in a GEMINI.md file at the root of
a TypeScript project:

# Project: My TypeScript Library
## General Instructions
- When you generate new TypeScript code, follow the existing coding style.- Ensure all new functions and classes have JSDoc comments.- Prefer functional programming paradigms where appropriate.
## Coding Style
- Use 2 spaces for indentation.- Prefix interface names with `I` (for example, `IUserService`).- Always use strict equality (`===` and `!==`).

Manage context with the /memory commandSection titled “Manage context with the /memory command”

You can interact with the loaded context files by using the /memory command.

/memory show: Displays the full, concatenated content of the current
hierarchical memory. This lets you inspect the exact instructional context
being provided to the model.

/memory reload: Forces a re-scan and reload of all GEMINI.md files
from all configured locations.

Modularize context with importsSection titled “Modularize context with imports”

You can break down large GEMINI.md files into smaller, more manageable
components by importing content from other files using the @file.md syntax.
This feature supports both relative and absolute paths.

Example GEMINI.md with imports:

# Main GEMINI.md file
This is the main content.
@./components/instructions.md
More content here.
@../shared/style-guide.md

For more details, see the Memory Import Processor
documentation.

Customize the context file nameSection titled “Customize the context file name”

While GEMINI.md is the default filename, you can configure this in your
settings.json file. To specify a different name or a list of names, use the
context.fileName property.

Example settings.json:

{  "context": {    "fileName": ["AGENTS.md", "CONTEXT.md", "GEMINI.md"]  }}

Next stepsSection titled “Next steps”

Learn about Ignoring files to exclude content from the
context system.

Explore the Memory tool to save persistent memories.

See how to use Custom commands to automate common
prompts.
   Last updated: Jun 18, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy


</details>
