# Antigravity Agent Skills 使用指南

介绍技能目录和 SKILL.md 如何扩展开发能力，说明加载方式及使用条件，并提供代码审查等技能配置示例。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/skills/>)）；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://antigravity.google/docs/skills/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/Antigravity Agent Skills 使用指南.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

Agent skills | Google 反重力 DocsSkip 至 contentGoogle 反重力 DocsSearchCtrlKCancel🚀 远程 ControlDownloadSelect themeDarkLightAutoGet StartedFeaturesSDKChangelogBlog ↗
GET STARTED
主页
入门
ANTIGRAVITY 2.0
概述
构建Google
型号
CLI
CLI概述
安装和授权
教程
使用反重力CLI
功能
最佳实践
疑难解答
CLI参考
ANTIGRAVITYFORIDES
反重力IDE
IDE扩展
概述
Visual Studio Code
视觉Studio
JetBrains
Zed
Xcode
RESOURCES
FAQ
计划和 AI 制作人员
企业概述
迁移
Firebase Studio迁移
技能迁移的工作流程
GCLI迁移
LEARN
概述
远程控制
市场
插件
终端沙箱
审查更改
终端
执行模式
无头模式
状态行自定义
终端标题自定义
浏览器
概述
浏览器录音
允许列表和拒绝列表
单独的Chrome简介
工件
概述
实施计划
演练
视觉屏幕截图
审查工件(CLI)
项目
概述
提示和交互
管理对话
权限
概述
AI制作人员
编辑器功能
选项卡完成
Agent侧面板
Agent概述
任务组
设置
概述
Agent设置
神器评测
Vim编辑器模式
SLASHCOMMANDS
概述
Boost(/boost)
计划(/plan)
团队合作 (/teamwork-preview)
Agents (/agents)
代码搜索 (/codesearch)
AI 积分(/credits)
差异 (/diff)
权限 (/permissions)
恢复 (/resume)
状态行(/statusline)
窗口标题 (/title)
模型配额 (/usage)
语音听写(/voice)
EXTEND
MCP
技能
Agent skills
规则
IDE工作流程
自定义子代理
生命周期挂钩
Sidecars
ANTIGRAVITY SDK
概述和快速入门
本地模型
角色
工具和技能
MCP
策略
子代理
结构化输出
生命周期和hooksSelect themeDarkLightAutoOn本页
概述
什么是技能？
技能剖析
清单格式(SKILL.md)
Frontmatter 字段
agent如何使用技能
最佳实践
保持技能聚焦
写清楚描述
使用脚本作为黑匣子
包括决策树
表面技能
反重力2.0技能位置
调用反重力技能2.0
CLI技能位置
斜线命令转换
使用插件管理技能
反重力IDE技能位置
在此页面
概述
什么技能？
技能剖析
清单格式（SKILL.md）
Frontmatter 字段
agent如何使用技能
最佳实践
集中技能
编写清晰的描述
将脚本用作黑匣子
包括决策树
表面技能
反重力2.0技能位置
反重力中调用技能2.0
CLI技能位置
斜杠命令转换
使用插件管理技能
反重力IDE技能locationsFeaturesExtendSkillsAgent skillsMarkdownCopy MarkdownView MarkdownAvailable 上：反重力 2.0反重力 CLI 反重力 IDE
Agent skillsSection 标题为“Agent skills”

技能是扩展 agent
 功能的开放标准。技能是一个包含 SKILL.md 文件的文件夹，其中包含 
 的说明，agent 在执行特定任务时可以遵循该说明。

什么是技能？标题为“什么是技能？”的部分

技能是可重复使用的知识包，扩展了 agent 的功能。
每项技能包含：

说明：关于如何完成特定任务的明确协议。

最佳实践：要遵循的惯例、风格指南和清单。

脚本和资源：agent
可以执行的可选帮助程序脚本和数据模式。

当您开始对话时，agent 会看到可用技能列表以及
 的名称和描述。如果某项技能看起来与您的任务相关，agent
 会阅读完整的说明并遵循它们。

注意
注意：反重力默认为.agents/skills，但仍保持对.agent/skills.的向后
兼容性

标题为“技能剖析”的 skillSection 剖析

技能被组织为包含所需的 SKILL.md 文件的目录包：

my-skill/
├── SKILL.md # 必需的技能说明和元数据
├── scripts/ # 可选的可执行脚本
├── Examples/ # 可选的参考实现
└── resources/ # 可选的模板、模式或数据文件

清单格式 (SKILL.md)标题为“清单格式 (SKILL.md)”的部分

每个 SKILL.md 文件都以 YAML frontmatter 开头，定义其名称和 
 触发条件：

---
名称：code-review
 描述：审查代码更改的错误、样式问题和最佳实践。在审查拉取请求或检查代码质量时使用。
---

# 代码审查技巧

检查代码时，请按照下列步骤操作：

## 审查清单

1. **正确性**：验证代码是否满足规范。
2. **边缘情况**：确保处理错误条件和边界。
3. **风格**：遵循项目命名和架构模式。
4. **性能**：识别潜在瓶颈或效率低下。

Frontmatter fieldsSection 标题为“Frontmatter 领域”

YAML frontmatter 支持以下字段：

FieldRequiredDescriptionnameNoA 技能的唯一标识符（小写，空格连字符）。如果未提供，则默认为文件夹名称。descriptionYesA 清楚描述该技能的作用以及何时使用它。这是agent在决定是否应用该技能时看到的。

提示
以第三人称撰写描述，并包含有助于agent
识别技能何时相关的关键字。例如：“使用 pytest 约定为 
Python 代码生成单元测试。”

agent 如何使用 skillsSection 标题为“agent 如何使用技能”

技能遵循渐进的披露模式：

发现：当对话开始时，agent 会看到可用的 
 技能列表及其名称和描述。

激活：如果某项技能看起来与您的任务相关，则 agent 会读取 
 完整的 SKILL.md 内容。

执行：agent在执行
你的任务时遵循技能的指示。

你不需要明确告诉agent使用技能——它根据
上下文决定。但是，如果您想确保
被使用，您可以提及技能的名称。

最佳 practicesSection 标题为“最佳实践”

保持技能 focusedSection 标题为“保持技能聚焦”

每项技能都应该做好一件事。不要使用“做所有事情”的技能，而是为不同的任务创建
单独的技能。

写清楚descriptionsSection，标题为“写清楚的描述”

描述的是agent如何决定是否使用你的技能。使其
具体说明该技能的用途以及何时有用。

使用脚本作为黑色 boxesSection 标题为“使用脚本作为黑匣子”

如果您的技能包括脚本，请鼓励 agent 首先使用 --help
 运行它们，而不是阅读整个源代码。这使得 agent 的上下文 
 专注于任务。

包含标题为“包含决策树”的决策 treesSection

对于复杂技能，添加一个部分，帮助agent根据情况选择正确的方法
。

surfaceSection 的技能，标题为“表面技能”

探索如何在您喜欢的表面上创建和管理技能：

反重力2.0
反重力CLI
反重力IDE
反重力2.0标题为“反重力2.0”的部分
反重力2.0技能locationsSection 标题为“反重力 2.0 技能位置”
反重力 2.0 从两个主要位置加载技能：

LocationScope<workspace-root>/.agents/skills/<skill-folder>/Workspace-specific~/.gemini/config/skills/<skill-folder>/Global（所有工作区）
每个范围都有不同的用途：

工作空间技能：范围仅限于单个项目，并致力于版本 
 控制以在您的工程团队中共享。

全局技能：适用于工作站上的所有项目。

调用反重力技能2.0标题为“调用反重力技能2.0”的部分
您可以通过两种方式调用反重力技能2.0：

自主调用：agent根据您的提示自动读取并遵循相关的
技能。

手动斜线命令：在提示面板中输入/<skill-name>即可显式调用技能
。

反重力 CLISection 标题为“反重力 CLI”
CLI 技能 locationsSection 标题为“CLI 技能位置”
反重力 CLI 完全支持本地和全局 agent skills：

LocationScope<workspace-root>/.agents/skills/<skill-folder>/Workspace-specific~/.gemini/antigravity-cli/skills/<skill-folder>/Global（所有工作区）~/.gemini/antigravity-cli/plugins/<name>/skills/Plugin-provided技能
斜线命令conversionSection标题为“斜线命令转换”
每当您定义技能时，CLI都会自动将其转换为交互式 TUI 内的斜线 
 命令。例如，创建一个名为
deploy-staging的技能会立即使/deploy-staging在提示框中可用。
使用标题为“使用插件管理技能”的pluginsSection管理技能
技能也可以打包在插件内并使用agy插件进行管理：
# 列出所有活动插件及其捆绑技能
agy 插件列表

# 从官方市场安装插件（默认为@antigravity-plugins-official）
agy插件安装<plugin-name>

# 从 GitHub URL 或本地插件路径安装技能包
agy插件安装https://github.com/<owner>/<repo>
agy插件安装。/my-skills-plugin
反重力IDE部分，标题为“反重力IDE”
反重力IDE技能locationsSection “反重力IDE技能位置”
在独立的反重力IDE中，可以从以下位置发现技能：

LocationScope<workspace-root>/.agents/skills/<skill-folder>/Workspace-specific~/.gemini/config/skills/<skill-folder>/Global（所有工作区；还支持旧版 ~/.gemini/antigravity/skills/）
您可以从 agent
 侧面板的自定义菜单中检查所有活动技能。PreviousMCPNextRulesAbout GoogleGoogle ProductsPrivacyTermsManage 饼干


## 原文对照

<details>
<summary>展开完整原文</summary>

Agent skills | Google Antigravity DocsSkip to contentGoogle Antigravity DocsSearchCtrlKCancel🚀 Remote ControlDownloadSelect themeDarkLightAutoGet StartedFeaturesSDKChangelogBlog ↗
GET STARTED
Home
Getting started
ANTIGRAVITY 2.0
Overview
Build with Google
Models
CLI
CLI overview
Installation and auth
Tutorial
Using Antigravity CLI
Features
Best practices
Troubleshooting
CLI reference
ANTIGRAVITY FOR IDES
Antigravity IDE
IDE extensions
Overview
Visual Studio Code
Visual Studio
JetBrains
Zed
Xcode
RESOURCES
FAQ
Plans and AI credits
Enterprise overview
Migrations
Firebase Studio migration
Workflows to skills migration
GCLI migration
LEARN
Overview
Remote Control
Marketplace
Plugins
Terminal sandbox
Review changes
Terminal
Execution modes
Headless mode
Status line customization
Terminal title customization
Browser
Overview
Browser recordings
Allowlist and denylist
Separate Chrome profile
Artifacts
Overview
Implementation plan
Walkthrough
Visual screenshots
Reviewing artifacts (CLI)
Projects
Overview
Prompting and interaction
Managing conversations
Permissions
Overview
AI credits
Editor features
Tab completion
Agent side panel
Agent overview
Task groups
Settings
Overview
Agent settings
Artifact review
Vim editor mode
SLASH COMMANDS
Overview
Boost (/boost)
Plan (/plan)
Teamwork (/teamwork-preview)
Agents (/agents)
Code search (/codesearch)
AI credits (/credits)
Diff (/diff)
Permissions (/permissions)
Resume (/resume)
Status line (/statusline)
Window title (/title)
Model quotas (/usage)
Voice dictation (/voice)
EXTEND
MCP
Skills
Agent skills
Rules
IDE workflows
Custom subagents
Lifecycle hooks
Sidecars
ANTIGRAVITY SDK
Overview and quickstart
Local models
Personas
Tools and skills
MCP
Policies
Subagents
Structured output
Lifecycle and hooksSelect themeDarkLightAutoOn this page
Overview
What are skills?
Anatomy of a skill
Manifest format (SKILL.md)
Frontmatter fields
How the agent uses skills
Best practices
Keep skills focused
Write clear descriptions
Use scripts as black boxes
Include decision trees
Skills by surface
Antigravity 2.0 skill locations
Invoking skills in Antigravity 2.0
CLI skill locations
Slash command conversion
Managing skills with plugins
Antigravity IDE skill locations
On this page
Overview
What are skills?
Anatomy of a skill
Manifest format (SKILL.md)
Frontmatter fields
How the agent uses skills
Best practices
Keep skills focused
Write clear descriptions
Use scripts as black boxes
Include decision trees
Skills by surface
Antigravity 2.0 skill locations
Invoking skills in Antigravity 2.0
CLI skill locations
Slash command conversion
Managing skills with plugins
Antigravity IDE skill locationsFeaturesExtendSkillsAgent skillsMarkdownCopy MarkdownView MarkdownAvailable on:Antigravity 2.0Antigravity CLIAntigravity IDE
Agent skillsSection titled “Agent skills”

Skills are an open standard for extending agent
capabilities. A skill is a folder containing a SKILL.md file with instructions
that the agent can follow when working on specific tasks.

What are skills?Section titled “What are skills?”

Skills are reusable packages of knowledge that extend what the agent can do.
Each skill contains:

Instructions: Explicit protocols for how to approach a specific task.

Best practices: Conventions, style guidelines, and checklists to follow.

Scripts and resources: Optional helper scripts and data schemas the agent
can execute.

When you start a conversation, the agent sees a list of available skills with
their names and descriptions. If a skill looks relevant to your task, the agent
reads the full instructions and follows them.

Note
Note: Antigravity defaults to .agents/skills, but still maintains backward
compatibility for .agent/skills.

Anatomy of a skillSection titled “Anatomy of a skill”

Skills are organized as directory bundles containing a required SKILL.md file:

my-skill/
├── SKILL.md       # Required skill instructions and metadata
├── scripts/       # Optional executable scripts
├── examples/      # Optional reference implementations
└── resources/     # Optional templates, schemas, or data files

Manifest format (SKILL.md)Section titled “Manifest format (SKILL.md)”

Every SKILL.md file begins with YAML frontmatter defining its name and
triggering criteria:

---
name: code-review
description: Reviews code changes for bugs, style issues, and best practices. Use when reviewing pull requests or checking code quality.
---

# Code Review Skill

When reviewing code, follow these steps:

## Review checklist

1. **Correctness**: Verify that the code satisfies specifications.
2. **Edge cases**: Ensure error conditions and boundaries are handled.
3. **Style**: Follow project naming and architectural patterns.
4. **Performance**: Identify potential bottlenecks or inefficiencies.

Frontmatter fieldsSection titled “Frontmatter fields”

The YAML frontmatter supports the following fields:

FieldRequiredDescriptionnameNoA unique identifier for the skill (lowercase, hyphens for spaces). Defaults to the folder name if not provided.descriptionYesA clear description of what the skill does and when to use it. This is what the agent sees when deciding whether to apply the skill.

Tip
Write your description in third person and include keywords that help the agent
recognize when the skill is relevant. For example: “Generates unit tests for
Python code using pytest conventions.”

How the agent uses skillsSection titled “How the agent uses skills”

Skills follow a progressive disclosure pattern:

Discovery: When a conversation starts, the agent sees a list of available
skills with their names and descriptions.

Activation: If a skill looks relevant to your task, the agent reads the
full SKILL.md content.

Execution: The agent follows the skill’s instructions while working on
your task.

You don’t need to explicitly tell the agent to use a skill—it decides based on
context. However, you can mention a skill by name if you want to ensure it’s
used.

Best practicesSection titled “Best practices”

Keep skills focusedSection titled “Keep skills focused”

Each skill should do one thing well. Instead of a “do everything” skill, create
separate skills for distinct tasks.

Write clear descriptionsSection titled “Write clear descriptions”

The description is how the agent decides whether to use your skill. Make it
specific about what the skill does and when it’s useful.

Use scripts as black boxesSection titled “Use scripts as black boxes”

If your skill includes scripts, encourage the agent to run them with --help
first rather than reading the entire source code. This keeps the agent’s context
focused on the task.

Include decision treesSection titled “Include decision trees”

For complex skills, add a section that helps the agent choose the right approach
based on the situation.

Skills by surfaceSection titled “Skills by surface”

Explore how to create and manage skills on your preferred surface:

Antigravity 2.0
Antigravity CLI
Antigravity IDE
Antigravity 2.0Section titled “Antigravity 2.0”
Antigravity 2.0 skill locationsSection titled “Antigravity 2.0 skill locations”
Antigravity 2.0 loads skills from two primary locations:

LocationScope<workspace-root>/.agents/skills/<skill-folder>/Workspace-specific~/.gemini/config/skills/<skill-folder>/Global (all workspaces)
Each scope serves a distinct purpose:

Workspace skills: Scoped to a single project and committed to version
control to share across your engineering team.

Global skills: Available across all projects on your workstation.

Invoking skills in Antigravity 2.0Section titled “Invoking skills in Antigravity 2.0”
You can invoke skills in Antigravity 2.0 in two ways:

Autonomous invocation: The agent automatically reads and follows relevant
skills based on your prompt.

Manual slash command: Type /<skill-name> in the prompt panel to
explicitly invoke a skill.

Antigravity CLISection titled “Antigravity CLI”
CLI skill locationsSection titled “CLI skill locations”
The Antigravity CLI features full support for local and global agent skills:

LocationScope<workspace-root>/.agents/skills/<skill-folder>/Workspace-specific~/.gemini/antigravity-cli/skills/<skill-folder>/Global (all workspaces)~/.gemini/antigravity-cli/plugins/<name>/skills/Plugin-provided skills
Slash command conversionSection titled “Slash command conversion”
Whenever you define a skill, the CLI automatically converts it into a slash
command inside the interactive TUI. For example, creating a skill named
deploy-staging immediately makes /deploy-staging available in the prompt box.
Managing skills with pluginsSection titled “Managing skills with plugins”
Skills can also be packaged inside plugins and managed with agy plugin:
# List all active plugins and their bundled skills
agy plugin list

# Install a plugin from the official marketplace (defaults to @antigravity-plugins-official)
agy plugin install <plugin-name>

# Install a skill bundle from a GitHub URL or local plugin path
agy plugin install https://github.com/<owner>/<repo>
agy plugin install ./my-skills-plugin
Antigravity IDESection titled “Antigravity IDE”
Antigravity IDE skill locationsSection titled “Antigravity IDE skill locations”
In the standalone Antigravity IDE, skills are discovered from the following locations:

LocationScope<workspace-root>/.agents/skills/<skill-folder>/Workspace-specific~/.gemini/config/skills/<skill-folder>/Global (all workspaces; legacy ~/.gemini/antigravity/skills/ is also supported)
You can inspect all active skills from the Customizations menu in the agent
side panel.PreviousMCPNextRulesAbout GoogleGoogle ProductsPrivacyTermsManage cookies


</details>
