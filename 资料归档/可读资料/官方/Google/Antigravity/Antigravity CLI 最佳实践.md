# Antigravity CLI 最佳实践

围绕任务提示、上下文、计划、迭代和结果检查介绍终端开发建议，帮助组织可控制的 Agent 操作流程。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/cli/best-practices/>)）；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://antigravity.google/docs/cli/best-practices/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/Antigravity CLI 最佳实践.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

最佳实践 - 反重力 CLI | Google 反重力 DocsSkip 到 contentGoogle 反重力 DocsSearchCtrlKCancel🚀 远程 ControlDownloadSelect themeDarkLightAutoGet StartedFeaturesSDKChangelogBlog ↗
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
生命周期和hooksSelectthemeDarkLightAutoOn本页
概述
建立验证循环
探索、计划、然后执行
丰富您的提示上下文
目标文件自动完成
附加视觉证据
配置工作区环境
编写代码库规则文件
建立结构化权限
管理TUI会话主动
尽早进行课程修正(esc)
使用/rewind
回顾历史记录/fork
自动化和脚本
运行非交互式命令(-p)
使用并行扇出子代理
相关资源
在此页面
概述
建立验证循环
探索、计划，然后执行
丰富您的提示上下文
目标文件自动完成
附加视觉证据
配置工作区环境
编写代码库规则文件
建立结构化权限
管理TUI会话主动
尽早进行课程校正(esc)
使用/rewind
回顾历史记录/fork
自动化和脚本
运行非交互式命令(-p)
使用并行扇出子代理
相关resourcesGet StartedCLIBest practicesMarkdownCopy MarkdownView Markdown
反重力最佳实践 CLISection 标题为“反重力最佳实践 CLI”

掌握工作流程、提示架构和本地配置选择，以最大限度地提高 agent 速度，同时保持稳健的控制。

建立标题为“建立验证循环”的验证 loopsSection

确保自主 agent 进行可靠、正确修改的最有效方法是为 agent 提供本地验证机制（例如单元测试、构建命令或格式化脚本）。

在要求 agent 实施代码更改之前，请按照以下步骤操作：

确保您的工作区目录已准备好测试套件。

如果测试不存在，则指示agent首先编写标准测试块。

一旦 agent 提出代码，指示它运行本地测试命令以验证其工作。

观察 agent 执行命令并自动迭代测试输出。

> 在main.py中实现功能X。随后运行 npm 测试以验证构建。

探索，计划，然后 executeSection 标题为“探索，计划，然后执行”

当复杂的变更被划分为不同的探索、规划和执行阶段时，自主本地 agents 将以最高精度运行：

探索：在编写任何更改之前，请 agent 解释目标代码库如何解决特定问题或定义接口的位置。

规划：请求实施计划。 agent 列出了实施计划工件中的目标文件、所需的依赖项和逻辑覆盖。

执行：批准结构化计划后，指示 agent 应用编辑。

> 探索我们的路由器如何解析`/docs/:page`。写下实施计划添加`/docs/best-practices`。

丰富您的提示 contextSection，标题为“丰富您的提示上下文”

提供本地 agents 高保真指标，以缩小推理边界并最大限度地减少令牌开销。

标题为“目标文件自动完成”的目标文件 autocompletionSection

在提示框中键入 @ 以触发交互式路径建议叠加层。突出显示并选择路径会将绝对工作区文件路径直接导入到提示中。这有助于 agent 确定其代码搜索的目标。

附加标题为“附加视觉证据”的视觉效果 evidenceSection

如果您正在调试视觉 UI 问题、渲染错误或前端布局不一致，请捕获屏幕截图或视频录制，复制它，然后在提示框中按 ctrl+v 将其附加。 agent 查阅媒体文件来诊断问题。

配置您的工作区 environmentSection，标题为“配置您的工作区环境”

优化您的本地工作站规则和安全边界以匹配您的工程流程。

编写标题为“编写代码库规则文件”的代码库规则 fileSection

在工作区根目录中创建 GEMINI.md 或 AGENTS.md 文件，以概述特定的目录标准、样式范例、测试命令参数和弃用警告。 agent 在启动时自动解析这些规则，并在建议更改之前查阅它们。

建立标题为“建立结构化权限”的结构化 permissionsSection

根据您的项目风险级别调整 ~/.gemini/antigravity-cli/settings.json 中的安全屏障：

request-review（默认）：在执行任何写入操作、Bash 命令或远程网络调用之前提示您。

proceed-in-sandbox：将所有终端执行限制在安全沙箱遏制环中。安全命令自主执行，而有风险的命令则提示进行审查。

strict：始终提示所有非读操作，提供完整的逐行透明度。

{
 "toolPermission"：“进入沙箱”，
 "enableTerminalSandbox"：true
}

管理标题为“主动管理 TUI 会话”的 TUI 会话 proactivelySection

使用活动会话导航工具从工程死胡同或路线正确的中间 agent 循环中恢复。

早期航向修正 (esc) 标题为“早期航向修正 (esc)”的部分

如果您看到 agent 执行了错误的搜索模式或编写了偏离您意图的代码，请立即按全局逃生舱口键 esc 以中断转弯并重新获得干净提示的焦点。

使用 /rewindSection 倒回历史记录，标题为“使用 /rewind 倒回历史记录”

如果 agent 进行了多次连续更改，从而引入了构建错误，则无需丢弃会话。输入 /rewind（或 /undo）将对话线程回滚到之前的稳定结帐。

/forkSection 的分支实验标题为“/fork 的分支实验”

如果您不确定最佳实施路径，请按照以下步骤操作：

达到稳定的基线线程。

键入 /fork 以启动重复的并行会话。

在分支会话中测试您的推测代码修改。

如果该方法失败，请运行 /resume 以交换回稳定的主分支。

自动化和 scriptSection 标题为“自动化和脚本”

Antigravity CLI 旨在在标准外壳管道工具中无缝运行。

运行非交互式命令 (-p)标题为“运行非交互式命令 (-p)”的部分

要自动执行快速查询或将 agents 集成到 Git 挂钩中，请使用一次性提示标志 -p：

agy -p“查看此 git diff 并起草常规提交消息”--cwd $(pwd)

使用并行扇出 subagentsSection，标题为“使用并行子代理扇出”

对于大规模扫描或多文件重构，请指示主 agent 生成并发后台子代理。当您继续在主屏幕上工作时，agent 管理器会自主处理后台线程。

标题为“相关资源”的相关 resourcesSection

了解如何配置设置和自定义视觉布局：

设置、渲染和键绑定：自定义键盘热键和缓冲区。

权限和沙箱：强制文件系统遏制。

插件和技巧：创建您自己的自定义斜线命令。
PreviousFeaturesNextTroubleshootingAbout GoogleGoogle ProductsPrivacyTermsManage cookies


## 原文对照

<details>
<summary>展开完整原文</summary>

Best practices - Antigravity CLI | Google Antigravity DocsSkip to contentGoogle Antigravity DocsSearchCtrlKCancel🚀 Remote ControlDownloadSelect themeDarkLightAutoGet StartedFeaturesSDKChangelogBlog ↗
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
Establish verification loops
Explore, plan, then execute
Enrich your prompting context
Target file autocompletion
Attaching visual evidence
Configure your workspace environment
Write a codebase rule file
Establish structured permissions
Manage TUI sessions proactively
Course-correct early (esc)
Rewind history with /rewind
Branch experiments with /fork
Automate and script
Run non-interactive commands (-p)
Fan out using parallel subagents
Related resources
On this page
Overview
Establish verification loops
Explore, plan, then execute
Enrich your prompting context
Target file autocompletion
Attaching visual evidence
Configure your workspace environment
Write a codebase rule file
Establish structured permissions
Manage TUI sessions proactively
Course-correct early (esc)
Rewind history with /rewind
Branch experiments with /fork
Automate and script
Run non-interactive commands (-p)
Fan out using parallel subagents
Related resourcesGet StartedCLIBest practicesMarkdownCopy MarkdownView Markdown
Best practices for Antigravity CLISection titled “Best practices for Antigravity CLI”

Master the workflows, prompt architectures, and local configuration choices to maximize agent velocity while maintaining robust control.

Establish verification loopsSection titled “Establish verification loops”

The single most effective way to ensure reliable, correct modifications from an autonomous agent is to provide the agent with a local verification mechanism (such as unit tests, build commands, or formatting scripts).

Before asking the agent to implement a code change, follow these steps:

Ensure your workspace directory has a test suite ready.

If tests don’t exist, direct the agent to write a standard test block first.

Once the agent proposes code, instruct it to run the local test command to verify its work.

Watch the agent execute the command and iterate on the test outputs automatically.

> Implement feature X in main.py. Run npm test afterward to verify the build.

Explore, plan, then executeSection titled “Explore, plan, then execute”

Autonomous local agents operate with the highest accuracy when complex changes are partitioned into distinct exploration, planning, and execution phases:

Exploration: Ask the agent to explain how the target codebase resolves a particular problem or where an interface is defined before writing any changes.

Planning: Request an implementation plan. The agent lists targeted files, required dependencies, and logic overrides in an implementation plan artifact.

Execution: Once you approve the structured plan, direct the agent to apply the edits.

> Explore how our router resolves `/docs/:page`. Write down an implementation plan to add `/docs/best-practices`.

Enrich your prompting contextSection titled “Enrich your prompting context”

Give local agents high-fidelity indicators to narrow down reasoning boundaries and minimize token overhead.

Target file autocompletionSection titled “Target file autocompletion”

Type @ within your prompt box to trigger the Interactive Path Suggestion overlay. Highlighting and selecting a path imports the absolute workspace file path directly into your prompt. This helps the agent target its code searches.

Attaching visual evidenceSection titled “Attaching visual evidence”

If you’re debugging visual UI issues, rendering bugs, or frontend layout inconsistencies, capture a screenshot or video recording, copy it, and press ctrl+v inside the prompt box to attach it. The agent consults the media file to diagnose the issue.

Configure your workspace environmentSection titled “Configure your workspace environment”

Optimize your local workstation rules and security boundaries to match your engineering flow.

Write a codebase rule fileSection titled “Write a codebase rule file”

Create a GEMINI.md or AGENTS.md file at your workspace root to outline specific directory standards, styling paradigms, test command parameters, and deprecation warnings. The agent automatically parses these rules on startup and consults them before suggesting changes.

Establish structured permissionsSection titled “Establish structured permissions”

Tune your safety barriers in ~/.gemini/antigravity-cli/settings.json based on your project risk level:

request-review (default): Prompts you before executing any write operations, Bash commands, or remote network calls.

proceed-in-sandbox: Restricts all terminal executions to a secure sandbox containment ring. Safe commands execute autonomously, while risky commands prompt for reviews.

strict: Always prompts for all non-read operations, providing complete line-by-line transparency.

{
    "toolPermission": "proceed-in-sandbox",
    "enableTerminalSandbox": true
}

Manage TUI sessions proactivelySection titled “Manage TUI sessions proactively”

Use active session navigation tools to recover from engineering dead-ends or course-correct intermediate agent loops.

Course-correct early (esc)Section titled “Course-correct early (esc)”

If you watch an agent execute an incorrect search pattern or write code that deviates from your intentions, press the global escape hatch key esc immediately to interrupt the turn and regain focus of a clean prompt.

Rewind history with /rewindSection titled “Rewind history with /rewind”

If an agent has made several successive changes that introduce build errors, you don’t need to discard the session. Type /rewind (or /undo) to roll back your conversation thread to a previous stable checkout.

Branch experiments with /forkSection titled “Branch experiments with /fork”

If you’re unsure of the best implementation path, follow these steps:

Reach a stable baseline thread.

Type /fork to spin up a duplicate parallel session.

Test your speculative code modifications in the branched session.

If the approach fails, run /resume to swap back to your stable main branch.

Automate and scriptSection titled “Automate and script”

Antigravity CLI is designed to operate seamlessly within standard shell pipeline tools.

Run non-interactive commands (-p)Section titled “Run non-interactive commands (-p)”

To automate quick queries or integrate agents into Git hooks, use the one-shot prompt flag -p:

agy -p "Review this git diff and draft a conventional commit message" --cwd $(pwd)

Fan out using parallel subagentsSection titled “Fan out using parallel subagents”

For large-scale sweeps or multi-file refactoring, direct the primary agent to spawn concurrent background subagents. The agent manager handles background threads autonomously while you continue working on your primary screen.

Related resourcesSection titled “Related resources”

Learn how to configure settings and customize visual layouts:

Settings, rendering, and keybindings: Customize keyboard hotkeys and buffers.

Permissions and sandbox: Enforce filesystem containment.

Plugins and skills: Create your own custom slash commands.
PreviousFeaturesNextTroubleshootingAbout GoogleGoogle ProductsPrivacyTermsManage cookies


</details>
