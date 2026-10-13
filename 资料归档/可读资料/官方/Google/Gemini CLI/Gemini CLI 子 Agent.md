# Gemini CLI 子 Agent

介绍将专门任务交给独立 Agent 的机制、配置与调用方式，帮助分离任务上下文并接收结果。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-06-08](<https://geminicli.com/docs/core/subagents/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/core/subagents/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 子 Agent.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

子代理 | Gemini CLI
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
 身份验证 
 CLI 备忘单 
Gemini 3 在 Gemini CLI 
 使用 Gemini CLI 
 文件管理 
 开始使用 Agent Skills 
 管理上下文和内存 
 执行 shell 命令 
 管理会话和历史记录 
 使用待办事项计划任务 
 使用计划模式进行模型引导 🔬 
 Web 搜索和获取 
 设置MCP 服务器 
 自动化任务 
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
 创建Agent Skills 
 使用 Agent Skills 
 开发者指南：最佳实践 
 自动内存 🔬 
 检查点 
 无头模式 
 Git工作树 🔬 
 挂钩 
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
 倒回 
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
 受信任文件夹 
 开发 
 行为评估 
 贡献指南 
 集成测试
问题和PR自动化
本地开发
NPM包结构GitHub选择主题DarkLightAuto
参考
命令参考 
 配置参考 
 键盘快捷键 
 内存导入处理器 
 策略引擎 
 工具参考 GitHub 选择主题 DarkLightAuto 
 资源
 FAQ 
 配额和定价 
 条款和隐私 
 疑难解答 
 卸载 GitHub 选择主题 DarkLightAuto 
 发布 
 发行说明 
 稳定版本 
 预览版本 GitHub 选择主题 DarkLightAuto 本页 
 简介 
 什么是子代理？   
 如何使用子代理 
 自动委派 
 强制子代理（@ 语法） 
 内置子代理 
 代码库调查器 
 CLI 帮助Agent 
 通才 Agent 
 浏览器 Agent 
 创建自定义子代理 
 Agent 定义文件 
文件格式 
 配置架构 
 工具通配符 
 隔离和递归保护 
 子代理工具隔离 
 配置隔离的工具和服务器 
 子代理特定的策略 
 管理子代理
 交互管理（/agents） 
 持久配置（settings.json） 
优化子代理 
 远程子代理 (Agent2Agent) 
 扩展子代理 
 禁用子代理 
在此页 
 简介 
 什么是子代理？   
 如何使用子代理 
 自动委派 
 强制子代理（@ 语法） 
 内置子代理 
 代码库调查器 
 CLI 帮助Agent 
 通才 Agent 
 浏览器 Agent 
 创建自定义子代理 
 Agent 定义文件 
文件格式 
 配置架构 
 工具通配符 
 隔离和递归保护 
 子代理工具隔离 
 配置隔离的工具和服务器 
 子代理特定的策略 
 管理子代理
 交互式管理 (/agents) 
 持久配置 (settings.json) 
 优化子代理 
 远程子代理 (Agent2Agent) 
 扩展子代理
 禁用子代理 
 子代理复制为 Markdown 复制！             
子代理是专门的agents，在您的主Gemini CLI
会话中运行。它们旨在处理特定的复杂任务，例如深度代码库 
 分析、文档查找或特定领域的推理，而不会扰乱 
 主要 agent 的上下文或工具集。

什么是子代理？标题为“什么是子代理？”的部分

子代理是主要 Gemini agent 可以为特定 
 工作雇用的“专家”。

重点上下文：每个子代理都有自己的系统提示和角色。

专用工具：子代理可以拥有一组受限或专用的
工具。

独立上下文窗口：与子代理的交互发生在
单独的上下文循环中，这将令牌保存在主对话历史记录中。

子代理作为同名工具暴露给主 agent。当
主agent调用该工具时，它将任务委托给子代理。一旦
子代理完成其任务，它就会向主agent报告其
发现。

如何使用subagentsSection标题为“如何使用子代理”

您可以通过自动委派或在提示中显式强制使用子代理
。

自动 delegationSection 标题为“自动委派”

Gemini CLI 的主要 agent 被指示在任务 
 与其专业知识相匹配时使用专门的子代理。例如，如果您询问“身份验证系统
如何工作？”，主agent可能会决定调用codebase_investigator子代理
来执行研究。

强制子代理（@ 语法）标题为“强制子代理（@ 语法）”的部分

您可以通过在提示符开头使用 @ 符号 
 后跟子代理名称，将任务显式定向到特定子代理。当您想绕过主要 agent 的决策并直接去找 
 专家时，这很有用 
。

示例：
终端窗口
@codebase_investigator 绘制AgentRegistry和LocalAgentExecutor之间的关系。

当您使用 @ 语法时，CLI 会注入一个系统注释，推动 
 主模型立即使用该特定的子代理工具。

内置 subagentsSection 标题为“内置子代理”

Gemini CLI 附带以下内置子代理：

代码库 InvestigatorSection 标题为“代码库调查员”

名称： codebase_investigator

目的：分析代码库、逆向工程并了解复杂的
依赖关系。

何时使用：“身份验证系统如何工作？”、“映射 AgentRegistry 类的 
 依赖关系。”

配置：默认启用。您可以覆盖 agents.overrides 下的 
settings.json 中的设置。示例（强制使用特定模型 
 并增加匝数）：

{  "agents": {    "overrides": {      "codebase_investigator": {        "modelConfig": { "model": "gemini-3-flash-preview" },        "runConfig": { "maxTurns": 50 }      }    }  }}

CLI 帮助 AgentSection 标题为“CLI 帮助 Agent”

名称： cli_help

目的：获取有关 Gemini CLI 本身、其命令、
 配置和文档的专业知识。

何时使用：“如何配置代理？”、“/rewind
命令有什么作用？”

配置：默认启用。

多面手 AgentSection 标题为“多面手 Agent”

姓名： 多面手

用途：通用的通用子代理，使用从主 agent 继承的工具 
 访问和配置。对于在隔离对话中执行广泛的
资源密集型子任务非常有用，通过仅返回给定任务的最终结果来优化主要
agent的上下文。

何时使用：当任务需要许多步骤、处理大量
信息或需要与主
agent相同的完整功能时，请使用此agent。它非常适合：

多文件修改：一次在
多个文件中应用重构或修复错误。

高容量执行：运行产生大量
终端输出的命令或测试。

面向行动的研究：agent 需要
 搜索代码并运行命令或进行编辑以找到解决方案的调查。通过委派
这些任务，您可以防止主要对话变得混乱或
缓慢。您可以使用@generalist显式调用它。

配置：默认启用。

浏览器 AgentSection 标题为“浏览器 Agent”

名称： browser_agent

目的：使用 
 辅助树自动执行 Web 浏览器任务 - 浏览网站、填写表单、
 单击按钮以及从网页中提取信息。

何时使用：“Go 访问 example.com 并填写联系表格”、“从此页面提取 
 定价表”、“单击登录按钮并输入我的 
 凭据。”

PrerequisitesSection 标题为“先决条件”

浏览器agent需要：

Chrome 版本 144 或更高版本（任何最新的稳定版本都可以）。

底层 
chrome-devtools-mcp
 服务器与 Gemini CLI 捆绑在一起并自动启动 - 无需单独安装 
。

启用浏览器 agentSection 标题为“启用浏览器 agent”

默认情况下禁用浏览器agent。在您的 settings.json 中启用它：

{  "agents": {    "overrides": {      "browser_agent": {        "enabled": true      }    }  }}

会议 modesSection 标题为“会话模式”

sessionMode 设置控制 Chrome 的启动和管理方式。在agents.browser下设置
：

{  "agents": {    "overrides": {      "browser_agent": {        "enabled": true      }    },    "browser": {      "sessionMode": "persistent"    }  }}

可用的模式有：

ModeDescriptionpersistent（默认）启动 Chrome，并使用存储在 ~/.gemini/cli-browser-profile/. 的持久配置文件在会话之间保留 Cookie、历史记录和设置。isolatedLaunches 使用临时配置文件的 Chrome，每次会话后都会将其删除。将此用于干净状态自动化。existingAttaches 到已运行的 Chrome 实例。您必须首先在 Chrome 中导航至 chrome://inspect/#remote-debugging 来启用远程调试。没有启动新的浏览器进程。

首次运行 consentSection 标题为“首次运行同意”

第一次调用浏览器agent时，Gemini CLI会显示同意
对话框。您必须在浏览器会话开始之前接受。该对话框仅
出现一次。

配置referenceSection标题为“配置参考”

所有特定于浏览器的设置都位于 settings.json 中的 agents.browser 下。
有关完整详细信息，请参阅 
agents.browser 配置参考。

SettingTypeDefaultDescriptionsessionModestring“持久”Chrome 的管理方式：“持久”、“隔离”或“现有”。headlessbooleanfalseRun Chrome 处于无头模式（无可见窗口）。profilePathstring - 浏览器配置文件目录的自定义路径。visualModelstring - 视觉对象的模型覆盖agent.allowedDomainsstring[] - 限制导航到特定域（例如，["github.com"]）。disableUserInputbooleantrueDisable 自动化期间浏览器窗口上的用户输入（仅限非无头）。maxActionsPerTasknumber100Maximum 每个任务的工具调用。 agent达到限制时终止。confirmSensitiveActionsbooleanfalseRequire手动确认upload_file和evaluate_script。blockFileUploadsbooleanfalseHard-阻止来自agent的所有文件上传请求。

自动化覆盖和输入 blockingSection 标题为“自动化覆盖和输入阻止”

在非无头模式下，浏览器 agent 将视觉覆盖注入到 
 浏览器窗口中，以指示自动化正在进行中。默认情况下，用户
输入（键盘和鼠标）也会被阻止，以防止意外干扰。
您可以通过将disableUserInput设置为 false 来禁用此功能。

SecuritySection 标题为“安全”

浏览器 agent 强制实施多层安全：

域限制：当设置allowedDomains时，agent只能
导航到列出的域（以及使用*.前缀时的子域）。
尝试访问不允许的域会引发致命错误立即
终止agent。 agent 还尝试检测并阻止使用 
 允许的域作为代理（例如，通过查询参数或片段）来访问 
 受限内容。

阻止的 URL 模式：底层 MCP 服务器阻止危险的 URL
 模式，包括 file://、javascript:、data:text/html、
chrome://extensions 和铬：//settings/passwords.

敏感操作确认：表单填写（填写，fill_form）始终
需要用户通过策略引擎进行确认，无论批准
模式如何。当confirmSensitiveActions为真时，upload_file和
evaluate_script也需要确认。

文件上传阻止：将blockFileUploads设置为true，将硬阻止所有
文件上传请求，阻止agent上传任何文件。

操作速率限制：maxActionsPerTask 设置（默认值：100）
 限制每个任务的工具调用总数，以防止执行失控。

视觉 agentSection 标题为“视觉 agent”

默认情况下，浏览器 agent 使用元素 uid 值通过可访问性 
 树与页面交互。对于需要视觉识别
的任务（例如，“点击黄色按钮”或“找到红色错误消息”），您
可以通过设置visualModel来启用视觉agent：

{  "agents": {    "overrides": {      "browser_agent": {        "enabled": true      }    },    "browser": {      "visualModel": "gemini-2.5-computer-use-preview-10-2025"    }  }}

启用后，agent 可以访问 analyze_screenshot 工具，该工具 
 捕获屏幕截图并将其发送到视觉模型进行分析。模型 
 返回浏览器 agent 与 
 click_at 工具一起使用的坐标和元素描述，以实现基于坐标的精确交互。

注意
视觉agent需要API密钥或Vertex AI身份验证。使用“使用Google登录”时
不可用。

沙盒 supportSection 标题为“沙盒支持”

浏览器 agent 在 
 沙箱内运行时会自动调整其行为。
macOS 安全带 (sandbox-exec) 标题为“macOS 安全带 (sandbox-exec)”的部分

当 CLI 在 macOS 安全带沙箱下运行时，持久和隔离 
 会话模式将在启用无头的情况下强制隔离。这可以避免由于安全带文件系统对持久性 
 浏览器配置文件的限制而导致的 
 权限错误。如果 sessionMode 设置为现有，则不会应用覆盖。
容器沙箱 (Docker / Podman) 标题为“容器沙箱 (Docker / Podman)”的部分

Chrome 在容器内不可用，因此浏览器 agent 被 
 禁用，除非 sessionMode 设置为“现有”。当使用 
 现有模式启用时，agent 通过 host.docker.internal:9222 的 
 解析的 IP 自动连接到主机上的 Chrome，而不是使用本地管道 
 发现。端口 9222 目前是硬编码的，无法自定义。

要在 Docker 沙箱中使用浏览器 agent：

在启用远程调试的主机上启动 Chrome：
终端窗口
# 选项 A：从命令行启动 Chromegoogle-chrome --remote-debugging-port=9222
# 选项 B：在 Chrome 设置中启用# 导航到 chrome://inspect/#remote-debugging 并启用

在项目的
.gemini/settings.json中配置sessionMode和允许的域：

{  "agents": {    "overrides": {      "browser_agent": { "enabled": true }    },    "browser": {      "sessionMode": "existing",      "allowedDomains": ["example.com"]    }  }}

启动带有端口转发的CLI：
终端窗口
GEMINI_SANDBOX=docker SANDBOX_PORTS=9222 gemini

创建标题为“创建自定义子代理”的自定义 subagentsSection

您可以创建自己的子代理来自动化特定工作流程或强制执行
特定角色。

Agent 定义 filesSection 标题为“Agent 定义文件”

自定义 agents 被定义为带有 YAML frontmatter 的 Markdown 文件 (.md)。您
可以将它们放置在：

项目级别：.gemini/agents/*.md（与您的团队共享）

用户级别：~/.gemini/agents/*.md（个人agents）

文件 formatSection 标题为“文件格式”

文件 MUST 以 YAML 开头，并用三重破折号括起来 ---。
markdown 文件的正文成为 agent 的系统提示符。

示例：.gemini/agents/security-auditor.md

---name: security-auditordescription: 专门查找 code.kind 中的安全漏洞: localtools: - read_file - grep_searchmodel: gemini-3-flash-previewtemperature: 0.2max_ 转: 10---
你是一个无情的安全审计员。您的工作是分析代码中的潜在漏洞。
重点关注：
1.  SQL注射2。  XSS（跨站脚本）3。  硬编码凭证4。  不安全的文件操作
当您发现漏洞时，请清楚地解释并提出修复建议。不要自己修理；只需报告即可。

配置 schemaSection 标题为“配置模式”

FieldTypeRequiredDescriptionnamestringYesUnique 标识符（slug）用作 agent 的工具名称。仅小写字母、数字、连字符和下划线。descriptionstringYesShort 描述 agent 的功能。这对于主 agent 是可见的，以帮助它决定何时调用此子代理。kindstringNolocal（默认）或远程。toolsarrayNoList 工具名称 agent 可以使用。支持通配符：*（所有工具）、mcp_*（所有MCP工具）、mcp_server_*（来自服务器的所有工具）。如果省略，它将继承父会话中的所有工具。mcpServersobjectNoConfiguration 用于隔离到此特定 agent.modelstringNoSpecific 模型的内联 Model Context Protocol (MCP) 服务器以使用（例如，gemini-3-preview）。默认继承（使用主会话模型）。temperaturenumberNoModel 温度（0.0 - 2.0）。默认为 1。max_turnsnumberNoMaximum 此 agent 在必须返回之前允许的对话轮数。默认为 30.timeout_minsnumberNoMaximum 执行时间（以分钟为单位）。默认为 10。

工具 wildcardsSection 标题为“工具通配符”

为子代理定义工具时，您可以使用通配符快速授予 
 对工具组的访问权限：

*：授予对所有可用内置和已发现工具的访问权限。

mcp_*：授予所有连接的 MCP 服务器对所有工具的访问权限。

mcp_my-server_*：授予对名为 
my-server 的特定 MCP 服务器的所有工具的访问权限。

隔离与递归protectionSection标题为“隔离与递归保护”

每个子代理在其自己的隔离上下文循环中运行。这意味着：

独立历史记录：子代理的对话历史记录不会使
主agent的上下文变得臃肿。

隔离工具：子代理只能访问您明确
授予它的工具。

递归保护：为了防止无限循环和过度使用令牌，
子代理不能调用其他子代理。如果子代理被授予 *
 工具通配符，它​​仍然无法查看或调用其他 agents。

子代理工具 isolationSection 标题为“子代理工具隔离”

子代理工具隔离将 Gemini CLI 移离单个全局工具 
 注册表。通过提供隔离的执行环境，您可以确保
子代理仅与它们设计的系统部分进行交互。这
可以防止意外的副作用，通过避免状态
污染来提高可靠性，并实现细粒度的权限控制。

通过此功能，您可以：

指定工具访问权限：使用 agent 定义中的 
 工具列表准确定义 agent 可以访问哪些工具。

定义内联 MCP 服务器：直接在子代理的 markdown frontmatter 中配置 Model Context Protocol (MCP) 服务器 
（提供将 AI 模型连接到外部工具和 
 数据源的标准化方法），隔离他们
到那个特定的agent。

维护状态隔离：确保子代理仅与其
自己的一组工具和服务器交互，防止副作用和状态污染。

应用特定于子代理的策略：根据 
 执行子代理的名称，在 
 策略引擎 TOML 配置中实施细化规则。

配置隔离工具和标题为“配置隔离工具和服务器”的 serversSection

您可以通过更新其 markdown
frontmatter 来为子代理配置工具隔离。这使您可以明确指定子代理可以使用哪些工具，
，而不是依赖全局注册表。

添加 mcpServers 对象来定义 
agent 特有的内联 MCP 服务器。

示例：

---name：我的隔离代理工具：-grep_search-read_filemcpServers：我的自定义服务器：命令：'节点'参数：['path/to/server.js']---

子代理特定 policiesSection 标题为“子代理特定策略”

您可以使用 
 策略引擎的 TOML 配置对子代理实施细粒度控制。这允许
您专门授予或限制agent的权限，而
不会影响您的CLI会话的其余部分。

要将策略规则限制为特定子代理，请将子代理属性添加到
的 [[规则]] 阻止你的 policy.toml 文件。

示例：

[[rules]]name =“允许pr-creator推送代码”subagent =“pr-creator”description =“允许pr-creator自动推送分支。”action =“允许”toolName =“run_shell_command”commandPrefix =“git推送”

在此配置中，仅当执行子代理的 
 名称与 pr-creator 匹配时才会触发策略规则。没有子代理属性的规则
普遍适用于所有agents。

管理 subagentsSection 标题为“管理子代理”

您可以使用 /agents 命令或通过 settings.json 永久使用 
 以交互方式管理子代理。

交互式管理 (/agents)标题为“交互式管理 (/agents)”的部分

如果您处于交互式 CLI 会话中，则可以使用 /agents 命令来 
 管理子代理，而无需手动编辑配置文件。这是
推荐的快速启用、禁用或重新配置agents的方法。

有关子命令和用法的完整列表，请参阅
/agents命令参考。

持久配置 (settings.json)标题为“持久配置 (settings.json)”的部分

虽然 /agents 命令和 agent 定义文件提供了一个起点，
您可以使用 settings.json 进行全局、持久覆盖。这对于
在所有会话中强制执行特定模型或执行限制非常有用。

agents.overridesSection 标题为“agents.覆盖”

使用它来启用或禁用特定的 agents 或覆盖其运行 
 配置。

{ "agents": { "overrides": { "security-auditor": { "enabled": false, "runConfig": {          "maxTurns": 20,          "maxTimeMinutes": 10        } } } }}

modelConfigs.overridesSection 标题为“modelConfigs.覆盖”

您可以使用自定义模型设置（例如系统
指令前缀或特定安全设置）使用 overrideScope
场。

{  "modelConfigs": {    "overrides": [      {        "match": { "overrideScope": "security-auditor" },        "modelConfig": {          "generateContentConfig": {            "temperature": 0.1          }        }      }    ]  }}

安全政策 (TOML) 标题为“安全政策 (TOML)”的部分

您可以使用 CLI 的策略引擎限制对特定子代理的访问。
出于策略匹配目的，子代理被视为虚拟工具名称。

要管理对子代理的访问，请在策略目录 
（例如，~/.gemini/policies/）中创建一个 .toml 文件：

[[rule]]toolName = "codebase_investigator"decision = "拒绝"deny_message = "此会话限制深度代码库分析。"

有关设置细粒度安全护栏的更多信息，请参阅
策略引擎参考。

优化您的 subagentSection，标题为“优化您的子代理”

主 agent 的系统提示鼓励它在 
 可用时使用专家子代理。它根据
agent的描述来判断agent是否为相关专家。您可以通过更新描述以更清楚地指示来提高使用 agent 
 的可靠性：

其专业领域。

什么时候应该使用它。

一些示例场景。

例如，对于 Git 操作，以下子代理描述应一致地调用相当
。

Git Expert agent 应该用于所有本地和远程 git 操作。
例如：

进行提交

使用二等分搜索回归

与源代码控制和问题提供者交互，例如 GitHub。

如果您需要进一步调整您的子代理，您可以通过选择要使用 /model 进行优化的模型 
，然后询问模型为什么它不认为 
 您的子代理是通过特定提示和给定描述来调用的。

远程子代理 (Agent2Agent) 标题为“远程子代理 (Agent2Agent)”的部分

Gemini CLI 还可以使用 Agent 到 Agent
(A2A) 协议将任务委派给远程子代理。

有关详细的 
 配置、身份验证和使用说明，请参阅远程子代理文档。

扩展 subagentsSection 标题为“扩展子代理”

扩展可以捆绑和分发子代理。有关如何
将agents打包在扩展中的详细信息，请参阅
扩展文档。

禁用标题为“禁用子代理”的 subagentsSection

默认情况下启用子代理。要禁用它们，请将 settings.json 中的 enableAgents 设置为 false
：

{ "experimental": { "enableAgents": false }} 上次更新时间：6 月 8、2026 
本网站使用 Google 的 cookie 来提供和提高其服务质量并分析 
 流量。
 
我明白。
 条款 | 隐私


## 原文对照

<details>
<summary>展开完整原文</summary>

Subagents | Gemini CLI
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
  What are subagents?   
  How to use subagents   
  Automatic delegation   
  Forcing a subagent (@ syntax)      
  Built-in subagents   
  Codebase Investigator   
  CLI Help Agent   
  Generalist Agent   
  Browser Agent      
  Creating custom subagents   
  Agent definition files   
  File format   
  Configuration schema   
  Tool wildcards   
  Isolation and recursion protection      
  Subagent tool isolation   
  Configuring isolated tools and servers   
  Subagent-specific policies      
  Managing subagents   
  Interactive management (/agents)   
  Persistent configuration (settings.json)   
  Optimizing your subagent      
  Remote subagents (Agent2Agent)   
  Extension subagents   
  Disabling subagents     
On this page 
  Introduction   
  What are subagents?   
  How to use subagents   
  Automatic delegation   
  Forcing a subagent (@ syntax)      
  Built-in subagents   
  Codebase Investigator   
  CLI Help Agent   
  Generalist Agent   
  Browser Agent      
  Creating custom subagents   
  Agent definition files   
  File format   
  Configuration schema   
  Tool wildcards   
  Isolation and recursion protection      
  Subagent tool isolation   
  Configuring isolated tools and servers   
  Subagent-specific policies      
  Managing subagents   
  Interactive management (/agents)   
  Persistent configuration (settings.json)   
  Optimizing your subagent      
  Remote subagents (Agent2Agent)   
  Extension subagents   
  Disabling subagents                 
 Subagents     Copy as Markdown Copied!             
Subagents are specialized agents that operate within your main Gemini CLI
session. They are designed to handle specific, complex tasks—like deep codebase
analysis, documentation lookup, or domain-specific reasoning—without cluttering
the main agent’s context or toolset.

What are subagents?Section titled “What are subagents?”

Subagents are “specialists” that the main Gemini agent can hire for a specific
job.

Focused context: Each subagent has its own system prompt and persona.

Specialized tools: Subagents can have a restricted or specialized set of
tools.

Independent context window: Interactions with a subagent happen in a
separate context loop, which saves tokens in your main conversation history.

Subagents are exposed to the main agent as a tool of the same name. When the
main agent calls the tool, it delegates the task to the subagent. Once the
subagent completes its task, it reports back to the main agent with its
findings.

How to use subagentsSection titled “How to use subagents”

You can use subagents through automatic delegation or by explicitly forcing them
in your prompt.

Automatic delegationSection titled “Automatic delegation”

Gemini CLI’s main agent is instructed to use specialized subagents when a task
matches their expertise. For example, if you ask “How does the auth system
work?”, the main agent may decide to call the codebase_investigator subagent
to perform the research.

Forcing a subagent (@ syntax)Section titled “Forcing a subagent (@ syntax)”

You can explicitly direct a task to a specific subagent by using the @ symbol
followed by the subagent’s name at the beginning of your prompt. This is useful
when you want to bypass the main agent’s decision-making and go straight to a
specialist.

Example:
Terminal window
@codebase_investigator Map out the relationship between the AgentRegistry and the LocalAgentExecutor.

When you use the @ syntax, the CLI injects a system note that nudges the
primary model to use that specific subagent tool immediately.

Built-in subagentsSection titled “Built-in subagents”

Gemini CLI comes with the following built-in subagents:

Codebase InvestigatorSection titled “Codebase Investigator”

Name: codebase_investigator

Purpose: Analyze the codebase, reverse engineer, and understand complex
dependencies.

When to use: “How does the authentication system work?”, “Map out the
dependencies of the AgentRegistry class.”

Configuration: Enabled by default. You can override its settings in
settings.json under agents.overrides. Example (forcing a specific model
and increasing turns):

{  "agents": {    "overrides": {      "codebase_investigator": {        "modelConfig": { "model": "gemini-3-flash-preview" },        "runConfig": { "maxTurns": 50 }      }    }  }}

CLI Help AgentSection titled “CLI Help Agent”

Name: cli_help

Purpose: Get expert knowledge about Gemini CLI itself, its commands,
configuration, and documentation.

When to use: “How do I configure a proxy?”, “What does the /rewind
command do?”

Configuration: Enabled by default.

Generalist AgentSection titled “Generalist Agent”

Name: generalist

Purpose: A general, all-purpose subagent that uses the inherited tool
access and configurations from the main agent. Useful for executing broad,
resource-heavy subtasks in an isolated conversation, optimizing your main
agent’s context by returning only the final result of that given task.

When to use: Use this agent when a task requires many steps, handles large
volumes of information, or requires the same full capabilities as the main
agent. It is ideal for:

Multi-file modifications: Applying refactors or fixing errors across
several files at once.

High-volume execution: Running commands or tests that produce extensive
terminal output.

Action-oriented research: Investigations where the agent needs to both
search code and run commands or make edits to find a solution. By delegating
these tasks, you prevent your main conversation from becoming cluttered or
slow. You can invoke it explicitly using @generalist.

Configuration: Enabled by default.

Browser AgentSection titled “Browser Agent”

Name: browser_agent

Purpose: Automate web browser tasks — navigating websites, filling forms,
clicking buttons, and extracting information from web pages — using the
accessibility tree.

When to use: “Go to example.com and fill out the contact form,” “Extract
the pricing table from this page,” “Click the login button and enter my
credentials.”

PrerequisitesSection titled “Prerequisites”

The browser agent requires:

Chrome version 144 or later (any recent stable release works).

The underlying
chrome-devtools-mcp
server is bundled with Gemini CLI and launched automatically — no separate
installation is needed.

Enabling the browser agentSection titled “Enabling the browser agent”

The browser agent is disabled by default. Enable it in your settings.json:

{  "agents": {    "overrides": {      "browser_agent": {        "enabled": true      }    }  }}

Session modesSection titled “Session modes”

The sessionMode setting controls how Chrome is launched and managed. Set it
under agents.browser:

{  "agents": {    "overrides": {      "browser_agent": {        "enabled": true      }    },    "browser": {      "sessionMode": "persistent"    }  }}

The available modes are:

ModeDescriptionpersistent(Default) Launches Chrome with a persistent profile stored at ~/.gemini/cli-browser-profile/. Cookies, history, and settings are preserved between sessions.isolatedLaunches Chrome with a temporary profile that is deleted after each session. Use this for clean-state automation.existingAttaches to an already-running Chrome instance. You must enable remote debugging first by navigating to chrome://inspect/#remote-debugging in Chrome. No new browser process is launched.

First-run consentSection titled “First-run consent”

The first time the browser agent is invoked, Gemini CLI displays a consent
dialog. You must accept before the browser session starts. This dialog only
appears once.

Configuration referenceSection titled “Configuration reference”

All browser-specific settings go under agents.browser in your settings.json.
For full details, see the
agents.browser configuration reference.

SettingTypeDefaultDescriptionsessionModestring"persistent"How Chrome is managed: "persistent", "isolated", or "existing".headlessbooleanfalseRun Chrome in headless mode (no visible window).profilePathstring—Custom path to a browser profile directory.visualModelstring—Model override for the visual agent.allowedDomainsstring[]—Restrict navigation to specific domains (for example, ["github.com"]).disableUserInputbooleantrueDisable user input on the browser window during automation (non-headless only).maxActionsPerTasknumber100Maximum tool calls per task. The agent is terminated when the limit is reached.confirmSensitiveActionsbooleanfalseRequire manual confirmation for upload_file and evaluate_script.blockFileUploadsbooleanfalseHard-block all file upload requests from the agent.

Automation overlay and input blockingSection titled “Automation overlay and input blocking”

In non-headless mode, the browser agent injects a visual overlay into the
browser window to indicate that automation is in progress. By default, user
input (keyboard and mouse) is also blocked to prevent accidental interference.
You can disable this by setting disableUserInput to false.

SecuritySection titled “Security”

The browser agent enforces several layers of security:

Domain restrictions: When allowedDomains is set, the agent can only
navigate to the listed domains (and their subdomains when using *. prefix).
Attempting to visit a disallowed domain throws a fatal error that immediately
terminates the agent. The agent also attempts to detect and block the use of
allowed domains as proxies (e.g., via query parameters or fragments) to access
restricted content.

Blocked URL patterns: The underlying MCP server blocks dangerous URL
schemes including file://, javascript:, data:text/html,
chrome://extensions, and chrome://settings/passwords.

Sensitive action confirmation: Form filling (fill, fill_form) always
requires user confirmation through the policy engine, regardless of approval
mode. When confirmSensitiveActions is true, upload_file and
evaluate_script also require confirmation.

File upload blocking: Set blockFileUploads to true to hard-block all
file upload requests, preventing the agent from uploading any files.

Action rate limiting: The maxActionsPerTask setting (default: 100)
limits the total number of tool calls per task to prevent runaway execution.

Visual agentSection titled “Visual agent”

By default, the browser agent interacts with pages through the accessibility
tree using element uid values. For tasks that require visual identification
(for example, “click the yellow button” or “find the red error message”), you
can enable the visual agent by setting a visualModel:

{  "agents": {    "overrides": {      "browser_agent": {        "enabled": true      }    },    "browser": {      "visualModel": "gemini-2.5-computer-use-preview-10-2025"    }  }}

When enabled, the agent gains access to the analyze_screenshot tool, which
captures a screenshot and sends it to the vision model for analysis. The model
returns coordinates and element descriptions that the browser agent uses with
the click_at tool for precise, coordinate-based interactions.

Note
The visual agent requires API key or Vertex AI authentication. It is
not available when using “Sign in with Google”.

Sandbox supportSection titled “Sandbox support”

The browser agent adjusts its behavior automatically when running inside a
sandbox.
macOS seatbelt (sandbox-exec)Section titled “macOS seatbelt (sandbox-exec)”

When the CLI runs under the macOS seatbelt sandbox, persistent and isolated
session modes are forced to isolated with headless enabled. This avoids
permission errors caused by seatbelt file-system restrictions on persistent
browser profiles. If sessionMode is set to existing, no override is applied.
Container sandboxes (Docker / Podman)Section titled “Container sandboxes (Docker / Podman)”

Chrome is not available inside the container, so the browser agent is
disabled unless sessionMode is set to "existing". When enabled with
existing mode, the agent automatically connects to Chrome on the host via the
resolved IP of host.docker.internal:9222 instead of using local pipe
discovery. Port 9222 is currently hardcoded and cannot be customized.

To use the browser agent in a Docker sandbox:

Start Chrome on the host with remote debugging enabled:
Terminal window
# Option A: Launch Chrome from the command linegoogle-chrome --remote-debugging-port=9222
# Option B: Enable in Chrome settings# Navigate to chrome://inspect/#remote-debugging and enable

Configure sessionMode and allowed domains in your project’s
.gemini/settings.json:

{  "agents": {    "overrides": {      "browser_agent": { "enabled": true }    },    "browser": {      "sessionMode": "existing",      "allowedDomains": ["example.com"]    }  }}

Launch the CLI with port forwarding:
Terminal window
GEMINI_SANDBOX=docker SANDBOX_PORTS=9222 gemini

Creating custom subagentsSection titled “Creating custom subagents”

You can create your own subagents to automate specific workflows or enforce
specific personas.

Agent definition filesSection titled “Agent definition files”

Custom agents are defined as Markdown files (.md) with YAML frontmatter. You
can place them in:

Project-level: .gemini/agents/*.md (Shared with your team)

User-level: ~/.gemini/agents/*.md (Personal agents)

File formatSection titled “File format”

The file MUST start with YAML frontmatter enclosed in triple-dashes ---.
The body of the markdown file becomes the agent’s System Prompt.

Example: .gemini/agents/security-auditor.md

---name: security-auditordescription: Specialized in finding security vulnerabilities in code.kind: localtools:  - read_file  - grep_searchmodel: gemini-3-flash-previewtemperature: 0.2max_turns: 10---
You are a ruthless Security Auditor. Your job is to analyze code for potentialvulnerabilities.
Focus on:
1.  SQL Injection2.  XSS (Cross-Site Scripting)3.  Hardcoded credentials4.  Unsafe file operations
When you find a vulnerability, explain it clearly and suggest a fix. Do not fixit yourself; just report it.

Configuration schemaSection titled “Configuration schema”

FieldTypeRequiredDescriptionnamestringYesUnique identifier (slug) used as the tool name for the agent. Only lowercase letters, numbers, hyphens, and underscores.descriptionstringYesShort description of what the agent does. This is visible to the main agent to help it decide when to call this subagent.kindstringNolocal (default) or remote.toolsarrayNoList of tool names this agent can use. Supports wildcards: * (all tools), mcp_* (all MCP tools), mcp_server_* (all tools from a server). If omitted, it inherits all tools from the parent session.mcpServersobjectNoConfiguration for inline Model Context Protocol (MCP) servers isolated to this specific agent.modelstringNoSpecific model to use (for example, gemini-3-preview). Defaults to inherit (uses the main session model).temperaturenumberNoModel temperature (0.0 - 2.0). Defaults to 1.max_turnsnumberNoMaximum number of conversation turns allowed for this agent before it must return. Defaults to 30.timeout_minsnumberNoMaximum execution time in minutes. Defaults to 10.

Tool wildcardsSection titled “Tool wildcards”

When defining tools for a subagent, you can use wildcards to quickly grant
access to groups of tools:

*: Grant access to all available built-in and discovered tools.

mcp_*: Grant access to all tools from all connected MCP servers.

mcp_my-server_*: Grant access to all tools from a specific MCP server named
my-server.

Isolation and recursion protectionSection titled “Isolation and recursion protection”

Each subagent runs in its own isolated context loop. This means:

Independent history: The subagent’s conversation history does not bloat
the main agent’s context.

Isolated tools: The subagent only has access to the tools you explicitly
grant it.

Recursion protection: To prevent infinite loops and excessive token usage,
subagents cannot call other subagents. If a subagent is granted the *
tool wildcard, it will still be unable to see or invoke other agents.

Subagent tool isolationSection titled “Subagent tool isolation”

Subagent tool isolation moves Gemini CLI away from a single global tool
registry. By providing isolated execution environments, you can ensure that
subagents only interact with the parts of the system they are designed for. This
prevents unintended side effects, improves reliability by avoiding state
contamination, and enables fine-grained permission control.

With this feature, you can:

Specify tool access: Define exactly which tools an agent can access using
a tools list in the agent definition.

Define inline MCP servers: Configure Model Context Protocol (MCP) servers
(which provide a standardized way to connect AI models to external tools and
data sources) directly in the subagent’s markdown frontmatter, isolating them
to that specific agent.

Maintain state isolation: Ensure that subagents only interact with their
own set of tools and servers, preventing side effects and state contamination.

Apply subagent-specific policies: Enforce granular rules in your
Policy Engine TOML configuration based on the
executing subagent’s name.

Configuring isolated tools and serversSection titled “Configuring isolated tools and servers”

You can configure tool isolation for a subagent by updating its markdown
frontmatter. This lets you explicitly state which tools the subagent can use,
rather than relying on the global registry.

Add an mcpServers object to define inline MCP servers that are unique to the
agent.

Example:

---name: my-isolated-agenttools:  - grep_search  - read_filemcpServers:  my-custom-server:    command: 'node'    args: ['path/to/server.js']---

Subagent-specific policiesSection titled “Subagent-specific policies”

You can enforce fine-grained control over subagents using the
Policy Engine’s TOML configuration. This allows
you to grant or restrict permissions specifically for an agent, without
affecting the rest of your CLI session.

To restrict a policy rule to a specific subagent, add the subagent property to
the [[rules]] block in your policy.toml file.

Example:

[[rules]]name = "Allow pr-creator to push code"subagent = "pr-creator"description = "Permit pr-creator to push branches automatically."action = "allow"toolName = "run_shell_command"commandPrefix = "git push"

In this configuration, the policy rule only triggers if the executing subagent’s
name matches pr-creator. Rules without the subagent property apply
universally to all agents.

Managing subagentsSection titled “Managing subagents”

You can manage subagents interactively using the /agents command or
persistently via settings.json.

Interactive management (/agents)Section titled “Interactive management (/agents)”

If you are in an interactive CLI session, you can use the /agents command to
manage subagents without editing configuration files manually. This is the
recommended way to quickly enable, disable, or re-configure agents on the fly.

For a full list of sub-commands and usage, see the
/agents command reference.

Persistent configuration (settings.json)Section titled “Persistent configuration (settings.json)”

While the /agents command and agent definition files provide a starting point,
you can use settings.json for global, persistent overrides. This is useful for
enforcing specific models or execution limits across all sessions.

agents.overridesSection titled “agents.overrides”

Use this to enable or disable specific agents or override their run
configurations.

{  "agents": {    "overrides": {      "security-auditor": {        "enabled": false,        "runConfig": {          "maxTurns": 20,          "maxTimeMinutes": 10        }      }    }  }}

modelConfigs.overridesSection titled “modelConfigs.overrides”

You can target specific subagents with custom model settings (like system
instruction prefixes or specific safety settings) using the overrideScope
field.

{  "modelConfigs": {    "overrides": [      {        "match": { "overrideScope": "security-auditor" },        "modelConfig": {          "generateContentConfig": {            "temperature": 0.1          }        }      }    ]  }}

Safety policies (TOML)Section titled “Safety policies (TOML)”

You can restrict access to specific subagents using the CLI’s Policy Engine.
Subagents are treated as virtual tool names for policy matching purposes.

To govern access to a subagent, create a .toml file in your policy directory
(e.g., ~/.gemini/policies/):

[[rule]]toolName = "codebase_investigator"decision = "deny"deny_message = "Deep codebase analysis is restricted for this session."

For more information on setting up fine-grained safety guardrails, see the
Policy Engine reference.

Optimizing your subagentSection titled “Optimizing your subagent”

The main agent’s system prompt encourages it to use an expert subagent when one
is available. It decides whether an agent is a relevant expert based on the
agent’s description. You can improve the reliability with which an agent is used
by updating the description to more clearly indicate:

Its area of expertise.

When it should be used.

Some example scenarios.

For example, the following subagent description should be called fairly
consistently for Git operations.

Git expert agent which should be used for all local and remote git operations.
For example:

Making commits

Searching for regressions with bisect

Interacting with source control and issues providers such as GitHub.

If you need to further tune your subagent, you can do so by selecting the model
to optimize for with /model and then asking the model why it does not think
that your subagent was called with a specific prompt and the given description.

Remote subagents (Agent2Agent)Section titled “Remote subagents (Agent2Agent)”

Gemini CLI can also delegate tasks to remote subagents using the Agent-to-Agent
(A2A) protocol.

See the Remote Subagents documentation for detailed
configuration, authentication, and usage instructions.

Extension subagentsSection titled “Extension subagents”

Extensions can bundle and distribute subagents. See the
Extensions documentation for details on how
to package agents within an extension.

Disabling subagentsSection titled “Disabling subagents”

Subagents are enabled by default. To disable them, set enableAgents to false
in your settings.json:

{  "experimental": { "enableAgents": false }}   Last updated: Jun 8, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy


</details>
