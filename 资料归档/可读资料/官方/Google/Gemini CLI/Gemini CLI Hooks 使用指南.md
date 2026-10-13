# Gemini CLI Hooks 使用指南

介绍生命周期事件、配置方式和自动操作，帮助在工具执行及会话过程加入校验、记录和其他开发操作。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-04-13](<https://geminicli.com/docs/hooks/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/hooks/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI Hooks 使用指南.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

Gemini CLI 挂钩 | Gemini CLI
 跳到内容 
未付费层和 Google 一名用户：Gemini CLI 已被反重力 
 CLI 取代6 月 18 日，2026。要了解更多信息，请参阅我们的博客文章。
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
 什么是钩子？   
 入门 
 核心概念 
 挂钩事件 
 全局机制 
 配置 
 配置架构 
 环境变量 
 安全与风险
 管理挂钩 
在此页 
 简介 
 什么是挂钩？   
 入门 
 核心概念 
 挂钩事件 
 全局机制 
 配置 
 配置架构 
 环境变量 
 安全与风险
 管理挂钩 
 Gemini CLI 挂钩 复制为 Markdown 已复制！             
挂钩是 Gemini CLI 在 
 代理循环中的特定点执行的脚本或程序，允许您拦截和自定义行为，而无需修改 
 CLI 的源代码。

什么是钩子？标题为“什么是钩子？”的部分

挂钩作为 agent 循环的一部分同步运行 - 当挂钩事件触发时，
Gemini CLI 会等待所有匹配的挂钩完成，然后再继续。

使用钩子，您可以：

添加上下文：在 
 模型处理请求之前注入相关信息（如 git 历史记录）。

验证操作：检查工具参数并阻止潜在危险的
操作。

执行策略：实施安全扫描和合规性检查。

记录交互：跟踪工具使用情况和模型响应以进行审核。

优化行为：动态过滤可用工具或调整模型
参数。

获得标题为“入门”的 startedSection

编写钩子指南：有关创建
您的第一个钩子的教程，包含全面的示例。

最佳实践：安全性、
性能和调试指南。

挂钩参考：I/O 模式和退出代码的权威技术 
 规范。

核心 conceptsSection 标题为“核心概念”

Hook eventsSection 标题为“Hook 事件”

挂钩由 Gemini CLI 生命周期中的特定事件触发。

EventWhen 它 FiresImpactCommon 使用 CasesSessionStartWhen 会话开始（启动、恢复、清除）注入 ContextInitialize 资源，加载 contextSessionEndWhen 会话结束（退出、清除）AdvisoryClean 起，保存 stateBeforeAgentAfter 用户提交提示，在 planningBlock 之前转 / ContextAdd 上下文，验证提示，阻止 turnsAfterAgentWhen agent 循环 endsRetry / HaltReview 输出，强制重试或停止 executionBeforeModelBefore 向 LLMBlock 发送请求转 / MockModify提示，交换模型，模拟responsesAfterModelAfter接收LLMresponseBlock转/RedactFilter/redact响应，记录interactionsBeforeToolSelectionBeforeLLM选择toolsFilter ToolsFilter 可用工具，优化 selectionBeforeToolBefore 工具 executesBlock 工具 / RewriteValidate 参数，阻止危险 opsAfterToolAfter 工具 executesBlock 结果 / ContextProcess 结果，运行测试，隐藏resultsPreCompressBefore 上下文 compressionAdvisorySave 状态，通知 userNotificationWhen 系统通知 occursAdvisoryForward 到桌面警报、日志记录

全球 mechanicsSection 标题为“全球机械师”

了解这些核心原则对于构建强大的钩子至关重要。

严格 JSON 要求（“黄金法则”）标题为“严格 JSON 要求（“黄金法则”）”的部分

Hooks 通过 stdin（输入）和 stdout（输出）进行通信。

必须保持沉默：除了最终的 JSON 对象之外，您的脚本不得将任何纯文本打印到 
stdout。即使在 JSON 之前进行一次 echo 或 print
 调用也会中断解析。

污染=失败：如果stdout包含非JSON文本，解析将
失败。 CLI 将默认为“允许”，并将整个输出视为 
systemMessage。

通过 Stderr 进行调试：使用 stderr 进行所有日志记录和调试（对于 
 示例，echo "debug" >&2）。 Gemini CLI 捕获 stderr，但从未尝试 
 将其解析为 JSON。

标题为“退出代码”的退出 codesSection

Gemini CLI 使用退出代码来确定钩子 
 执行的高级结果：

退出CodeLabelBehavioral影响0成功标准输出被解析为JSON。所有逻辑的首选代码，包括有意块（例如，{"decision": "deny"}）。2System BlockCritical 块。目标操作（刀具、转动或停止）被中止。 stderr 用作拒绝原因。严重程度高；用于安全停止或脚本失败。OtherWarningNon-致命失败。将显示警告，但交互将使用原始参数继续进行。

MatchersSection 标题为“匹配者”

您可以使用
匹配器字段过滤哪些特定工具或触发器触发您的挂钩。

工具事件（BeforeTool、AfterTool）：匹配器是正则
表达式。 （例如，“写入_。*”）。

生命周期事件：匹配器是精确字符串。 （例如，
“启动”）。

通配符：“*”或“”（空字符串）匹配所有出现的情况。

ConfigurationSection 标题为“配置”

钩子在settings.json中配置。 Gemini CLI 按以下优先顺序（从最高到最低）合并来自 
 多个层的配置：

项目设置：当前目录中的.gemini/settings.json。

用户设置：~/.gemini/settings.json.

系统设置：/etc/gemini-cli/settings.json.

扩展：由已安装的扩展定义的挂钩。

配置 schemaSection 标题为“配置模式”

{ "hooks": { "BeforeTool": [ { "matcher": "write_file|替换", "hooks": [ {            "name": "security-check",            "type": "command",            "command": "$GEMINI_PROJECT_DIR/.gemini/hooks/security.sh",            "timeout": 5000          } ] } ] }}

挂钩配置 fieldsSection 标题为“挂钩配置字段”

FieldTypeRequiredDescriptiontypestringYesThe执行引擎。目前仅支持“command”。commandstringYes*要执行的shell命令。 （类型为“command”时必需）。namestringNoA 用于识别日志中的钩子和 CLI 命令的友好名称。timeoutnumberNoExecution 超时（以毫秒为单位）（默认值：60000）。descriptionstringNoA 钩子用途的简要说明。

环境 variablesSection 标题为“环境变量”

挂钩是在经过净化的环境中执行的。

GEMINI_PROJECT_DIR：项目根目录的绝对路径。

GEMINI_PLANS_DIR：计划目录的绝对路径。

GEMINI_SESSION_ID：当前会话唯一的ID。

GEMINI_CWD：当前工作目录。

CLAUDE_PROJECT_DIR：（别名）为兼容性而提供。

安全性和 risksSection 标题为“安全性和风险”

注意
Hooks 使用您的用户权限执行任意代码。通过
配置挂钩，您可以允许脚本在
机器上运行shell命令。

打开不受信任的项目时，项目级挂钩尤其危险。
Gemini CLI 指纹项目挂钩。如果钩子的名称或命令更改
（例如，通过 git pull），它将被视为新的、不受信任的钩子，并且
您将在执行之前收到警告。

有关详细的威胁模型，请参阅安全注意事项 
。

管理 hooksSection 标题为“管理挂钩”

使用 CLI 命令管理挂钩，无需手动编辑 JSON：

查看挂钩：/hooks面板

Enable/Disable全部：/hooks全部启用或/hooks全部禁用

切换个人：/hooks启用<name>或/hooks禁用<name>
最后更新：四月13，2026
此网站使用来自Google的cookie来传递提高服务质量并分析
流量。
 
我明白。
 条款 | 隐私


## 原文对照

<details>
<summary>展开完整原文</summary>

Gemini CLI hooks | Gemini CLI
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
  What are hooks?   
  Getting started      
  Core concepts   
  Hook events   
  Global mechanics      
  Configuration   
  Configuration schema   
  Environment variables      
  Security and risks   
  Managing hooks     
On this page 
  Introduction   
  What are hooks?   
  Getting started      
  Core concepts   
  Hook events   
  Global mechanics      
  Configuration   
  Configuration schema   
  Environment variables      
  Security and risks   
  Managing hooks                 
 Gemini CLI hooks     Copy as Markdown Copied!             
Hooks are scripts or programs that Gemini CLI executes at specific points in the
agentic loop, allowing you to intercept and customize behavior without modifying
the CLI’s source code.

What are hooks?Section titled “What are hooks?”

Hooks run synchronously as part of the agent loop—when a hook event fires,
Gemini CLI waits for all matching hooks to complete before continuing.

With hooks, you can:

Add context: Inject relevant information (like git history) before the
model processes a request.

Validate actions: Review tool arguments and block potentially dangerous
operations.

Enforce policies: Implement security scanners and compliance checks.

Log interactions: Track tool usage and model responses for auditing.

Optimize behavior: Dynamically filter available tools or adjust model
parameters.

Getting startedSection titled “Getting started”

Writing hooks guide: A tutorial on creating
your first hook with comprehensive examples.

Best practices: Guidelines on security,
performance, and debugging.

Hooks reference: The definitive technical
specification of I/O schemas and exit codes.

Core conceptsSection titled “Core concepts”

Hook eventsSection titled “Hook events”

Hooks are triggered by specific events in Gemini CLI’s lifecycle.

EventWhen It FiresImpactCommon Use CasesSessionStartWhen a session begins (startup, resume, clear)Inject ContextInitialize resources, load contextSessionEndWhen a session ends (exit, clear)AdvisoryClean up, save stateBeforeAgentAfter user submits prompt, before planningBlock Turn / ContextAdd context, validate prompts, block turnsAfterAgentWhen agent loop endsRetry / HaltReview output, force retry or halt executionBeforeModelBefore sending request to LLMBlock Turn / MockModify prompts, swap models, mock responsesAfterModelAfter receiving LLM responseBlock Turn / RedactFilter/redact responses, log interactionsBeforeToolSelectionBefore LLM selects toolsFilter ToolsFilter available tools, optimize selectionBeforeToolBefore a tool executesBlock Tool / RewriteValidate arguments, block dangerous opsAfterToolAfter a tool executesBlock Result / ContextProcess results, run tests, hide resultsPreCompressBefore context compressionAdvisorySave state, notify userNotificationWhen a system notification occursAdvisoryForward to desktop alerts, logging

Global mechanicsSection titled “Global mechanics”

Understanding these core principles is essential for building robust hooks.

Strict JSON requirements (The “Golden Rule”)Section titled “Strict JSON requirements (The “Golden Rule”)”

Hooks communicate via stdin (Input) and stdout (Output).

Silence is Mandatory: Your script must not print any plain text to
stdout other than the final JSON object. Even a single echo or print
call before the JSON will break parsing.

Pollution = Failure: If stdout contains non-JSON text, parsing will
fail. The CLI will default to “Allow” and treat the entire output as a
systemMessage.

Debug via Stderr: Use stderr for all logging and debugging (for
example, echo "debug" >&2). Gemini CLI captures stderr but never attempts
to parse it as JSON.

Exit codesSection titled “Exit codes”

Gemini CLI uses exit codes to determine the high-level outcome of a hook
execution:

Exit CodeLabelBehavioral Impact0SuccessThe stdout is parsed as JSON. Preferred code for all logic, including intentional blocks (for example, {"decision": "deny"}).2System BlockCritical Block. The target action (tool, turn, or stop) is aborted. stderr is used as the rejection reason. High severity; used for security stops or script failures.OtherWarningNon-fatal failure. A warning is shown, but the interaction proceeds using original parameters.

MatchersSection titled “Matchers”

You can filter which specific tools or triggers fire your hook using the
matcher field.

Tool events (BeforeTool, AfterTool): Matchers are Regular
Expressions. (for example, "write_.*").

Lifecycle events: Matchers are Exact Strings. (for example,
"startup").

Wildcards: "*" or "" (empty string) matches all occurrences.

ConfigurationSection titled “Configuration”

Hooks are configured in settings.json. Gemini CLI merges configurations from
multiple layers in the following order of precedence (highest to lowest):

Project settings: .gemini/settings.json in the current directory.

User settings: ~/.gemini/settings.json.

System settings: /etc/gemini-cli/settings.json.

Extensions: Hooks defined by installed extensions.

Configuration schemaSection titled “Configuration schema”

{  "hooks": {    "BeforeTool": [      {        "matcher": "write_file|replace",        "hooks": [          {            "name": "security-check",            "type": "command",            "command": "$GEMINI_PROJECT_DIR/.gemini/hooks/security.sh",            "timeout": 5000          }        ]      }    ]  }}

Hook configuration fieldsSection titled “Hook configuration fields”

FieldTypeRequiredDescriptiontypestringYesThe execution engine. Currently only "command" is supported.commandstringYes*The shell command to execute. (Required when type is "command").namestringNoA friendly name for identifying the hook in logs and CLI commands.timeoutnumberNoExecution timeout in milliseconds (default: 60000).descriptionstringNoA brief explanation of the hook’s purpose.

Environment variablesSection titled “Environment variables”

Hooks are executed with a sanitized environment.

GEMINI_PROJECT_DIR: The absolute path to the project root.

GEMINI_PLANS_DIR: The absolute path to the plans directory.

GEMINI_SESSION_ID: The unique ID for the current session.

GEMINI_CWD: The current working directory.

CLAUDE_PROJECT_DIR: (Alias) Provided for compatibility.

Security and risksSection titled “Security and risks”

Caution
Hooks execute arbitrary code with your user privileges. By
configuring hooks, you are allowing scripts to run shell commands on your
machine.

Project-level hooks are particularly risky when opening untrusted projects.
Gemini CLI fingerprints project hooks. If a hook’s name or command changes
(for example, via git pull), it is treated as a new, untrusted hook and
you will be warned before it executes.

See Security Considerations
for a detailed threat model.

Managing hooksSection titled “Managing hooks”

Use the CLI commands to manage hooks without editing JSON manually:

View hooks: /hooks panel

Enable/Disable all: /hooks enable-all or /hooks disable-all

Toggle individual: /hooks enable <name> or /hooks disable <name>
   Last updated: Apr 13, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy


</details>
