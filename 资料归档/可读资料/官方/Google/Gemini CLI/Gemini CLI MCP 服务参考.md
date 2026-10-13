# Gemini CLI MCP 服务参考

说明服务配置、传输与认证、工具发现和使用，以及连接故障排查，支持把外部系统接入开发会话。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-10-06](<https://geminicli.com/docs/tools/mcp-server/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/tools/mcp-server/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI MCP 服务参考.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

MCP 服务器具有 Gemini CLI | Gemini CLI
 跳到内容 
未付费层和 Google 一名用户：Gemini CLI 已被反重力取代 
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
 网页搜索和获取 
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
什么是MCP服务器？   
 核心集成架构 
 发现层 (mcp-client.ts) 
 执行层 (mcp-tool.ts) 
 传输机制 
 使用 MCP 资源
 发现和列出 
 在对话中引用资源 
 如何设置 MCP 服务器 
 在 settings.json 中配置 MCP 服务器 
 配置结构
 配置属性 
 环境变量扩展 
 安全和环境清理 
 对远程 MCP 服务器的 OAuth 支持 
 示例配置 
 SSE MCP 具有 SA 模拟 
 的服务器深入探索发现过程
  1. 服务器迭代和连接
  2. 工具发现
  3. 工具命名和命名空间
  4. 模式处理
  5. 连接管理
工具执行流程
  1. 工具调用
  2. 确认流程
  3. 执行
  4. 响应处理
如何与 MCP 服务器交互 
 使用 /mcp 命令 
 示例 /mcp 输出 
 工具使用 
 状态监控和故障排除 
 连接状态
 常见问题及解决方案 
 调试技巧 
 重要说明 
 安全注意事项 
 性能和资源管理 
 架构兼容性 
 从工具返回丰富内容
 工作原理 
 示例：返回文本和图像 
 MCP 以斜杠命令提示 
 在服务器上定义提示 
 调用提示 
 管理MCP 具有 Gemini mcp 的服务器 
 添加服务器 (gemini mcp add) 
 列出服务器 (gemini mcp list) 
 故障排除和诊断 
 删除服务器 (gemini mcp remove) 
 Enabling/disabling 服务器（gemini mcp 启用，gemini mcp 禁用） 
 说明 
在此页上 
 简介 
 什么是 MCP 服务器？   
 核心集成架构 
 发现层 (mcp-client.ts) 
 执行层 (mcp-tool.ts) 
 传输机制 
 使用 MCP 资源
 发现和列出 
 在对话中引用资源 
 如何设置 MCP 服务器 
 在 settings.json 中配置 MCP 服务器 
 配置结构
 配置属性 
 环境变量扩展 
 安全和环境清理 
 对远程 MCP 服务器的 OAuth 支持 
 示例配置 
 SSE MCP 服务器与 SA 模拟 
 发现过程深入探讨
  1. 服务器迭代和连接
  2. 工具发现
  3. 工具命名和命名空间
  4. 模式处理
  5. 连接管理
工具执行流程
  1. 工具调用
  2. 确认流程
  3. 执行
  4. 响应处理
如何与 MCP 服务器交互 
 使用 /mcp 命令 
 示例 /mcp 输出 
 工具使用 
 状态监控和故障排除 
 连接状态
 常见问题及解决方案 
 调试技巧 
 重要说明 
 安全注意事项 
 性能和资源管理 
 架构兼容性 
 从工具返回丰富内容
 工作原理 
 示例：返回文本和图像 
 MCP 以斜杠命令提示 
 在服务器上定义提示 
 调用提示 
 管理MCP 具有 Gemini mcp 的服务器 
 添加服务器 (gemini mcp add) 
 列出服务器 (gemini mcp list) 
 故障排除和诊断 
 删除服务器 (gemini mcp remove) 
 Enabling/disabling 一台服务器（gemini mcp 启用，gemini mcp 禁用） 
 说明 
 MCP 服务器与 Gemini CLI 复制为 Markdown 已复制！             
本文档提供了通过Gemini CLI配置和使用Model Context Protocol
(MCP)服务器的指南。

什么是 MCP 服务器？标题为“什么是 MCP 服务器？”的部分

MCP服务器是一个通过Model Context Protocol向Gemini
CLI公开工具和资源的应用程序，允许其与外部
系统和数据源进行交互。 MCP 服务器充当 Gemini 模型 
 和您的本地环境或其他服务（例如 API）之间的桥梁。

MCP 服务器使 Gemini CLI 能够：

发现工具：通过标准化模式定义列出可用工具、其描述和参数
。

执行工具：使用定义的参数调用特定工具并接收
结构化响应。

访问资源：从服务器
公开的特定资源（文件、API负载、报告等）读取数据。

借助 MCP 服务器，您可以扩展 Gemini CLI 的功能来执行其内置功能之外的操作 
，例如与数据库、API、自定义 
 脚本或专用工作流程交互。

核心集成architectureSection标题为“核心集成架构”

Gemini CLI 通过核心包 (packages/core/src/tools/) 中内置的复杂发现和 
 执行系统与 MCP 服务器集成：

发现层 (mcp-client.ts) 标题为“发现层 (mcp-client.ts)”的部分

发现过程由 discoverMcpTools() 精心安排，其中：

从您的 settings.json
mcpServers 配置中迭代配置的服务器

使用适当的传输机制（Stdio、
SSE 或 Streamable HTTP）建立连接

使用 MCP 协议从每个服务器获取工具定义

清理并验证工具架构与 Gemini
API 的兼容性

在全局工具注册表中注册工具并解决冲突

如果服务器公开任何资源，则获取并注册资源

执行层 (mcp-tool.ts)标题为“执行层 (mcp-tool.ts)”的部分

每个发现的 MCP 工具都包含在一个 DiscoveredMCPTool 实例中，该实例：

根据服务器信任设置和用户
首选项处理确认逻辑

通过使用适当的参数调用 MCP 服务器来管理工具执行

处理 LLM 上下文和用户显示的响应

维护连接状态并处理超时

运输 mechanismsSection 标题为“运输机制”

Gemini CLI支持三种MCP传输类型：

Stdio Transport：生成子进程并通过 stdin/stdout 进行通信

SSE 传输：连接到服务器发送的事件端点

可流式传输 HTTP：使用 HTTP 流式传输进行通信

使用 MCP resourcesSection 标题为“使用 MCP 资源”

除了工具和
提示之外，一些MCP服务器还公开上下文“资源”。 Gemini CLI 会自动发现这些内容，并让您可以
 在聊天中引用它们。有关用于
与这些资源交互的工具的更多信息，请参阅MCP资源工具。

发现和listingSection标题为“发现和上市”

当发现运行时，CLI 获取每个服务器的 resources/list 结果。

/mcp 命令为每个连接的服务器显示资源部分以及工具和提示 
。

这将返回 URI 和元数据的简洁纯文本列表。

引用标题为“在对话中引用资源”的 conversationSection 中的资源

您可以使用已知的相同 @ 语法来引用本地文件：

@服务器：//resource/path

资源 URI 与文件系统路径一起出现在完成菜单中。当
您提交消息时，CLI 会调用resources/read 并在对话中注入内容
。

如何设置您的 MCP serverSection 标题为“如何设置您的 MCP 服务器”

Gemini CLI 使用 settings.json 文件中的 mcpServers 配置来 
 定位并连接到 MCP 服务器。此配置支持具有不同传输机制的多个服务器
。

在设置中配置MCP服务器。jsonSection标题为“在settings.json中配置MCP服务器”

您可以通过两种主要方式在 settings.json 文件中配置 MCP 服务器：
通过用于特定服务器定义的顶级 mcpServers 对象，以及 
通过用于控制服务器发现和 
 执行的全局设置的 mcp 对象。

全局 MCP 设置 (mcp)标题为“全局 MCP 设置 (mcp)”的部分

settings.json 中的 mcp 对象允许您为所有 
MCP 服务器定义全局规则。

mcp.serverCommand（字符串）：启动 MCP 服务器的全局命令。

mcp.allowed（字符串数组）：允许的 MCP 服务器名称列表。如果设置了
，则仅连接此列表中的服务器（与
mcpServers对象中的键匹配）。

mcp.excluded（字符串数组）：要排除的 MCP 服务器名称列表。
 将不会连接到此列表中的服务器。

示例：

{  "mcp": {    "allowed": ["my-trusted-server"],    "excluded": ["experimental-server"]  }}

服务器特定配置 (mcpServers)标题为“服务器特定配置 (mcpServers)”的部分

mcpServers 对象是您定义要连接到 
 CLI 的每个单独 MCP 服务器的位置。

配置structureSection标题为“配置结构”

将 mcpServers 对象添加到您的 settings.json 文件中：

{ ...文件包含其他配置对象 "mcpServers": { "serverName": { "command": "path/to/server", "args": ["--arg1", “值1”]，"env"：{        "API_KEY": "$MY_API_TOKEN"      }，"cwd"：“。/server-directory”，"timeout"：30000，"trust"：假}}}

配置 propertiesSection 标题为“配置属性”

每个服务器配置都支持以下属性：

必填（以下之一）标题为“必填（以下之一）”的部分

命令（字符串）：Stdio 传输的可执行文件的路径

url（字符串）：SSE端点URL（例如，
“http://localhost:8080/sse”）

httpUrl（字符串）：HTTP流端点URL

OptionalSection 标题为“可选”

args (string[])：Stdio 传输的命令行参数

headers（对象）：使用 url 或 httpUrl 时自定义 HTTP 标头

env（对象）：服务器进程的环境变量。价值观可以
使用 $ 引用环境变量VAR_NAME 或 ${VAR_NAME} 语法（全部
平台），或%VAR_NAME%（仅限 Windows）。

cwd（字符串）：Stdio 传输的工作目录

timeout (number): 请求超时时间，以毫秒为单位（默认：600,000ms =
10 分钟）

trust (boolean): 当 true 时，绕过 
 此服务器的所有工具调用确认（默认值： false）

includeTools（字符串[]）：要包含在此MCP
服务器中的工具名称列表。指定后，此 
 服务器只能使用此处列出的工具（白名单行为）。如果未指定，则默认情况下启用服务器中的所有工具
。

excludeTools（字符串[]）：要从此MCP
服务器中排除的工具名称列表。此处列出的工具将不可用于模型，即使它们是
由服务器公开的。 excludeTools 优先于 includeTools。如果
某个工具同时存在于两个列表中，则它将被排除。

targetAudience（字符串）：OAuth 客户端 ID 已列入您尝试访问的 
IAP- 受保护应用程序的白名单。与
authProviderType一起使用：“service_account_impersonation”。

targetServiceAccount（字符串）：要模拟的 Google 云 
 服务帐户的电子邮件地址。与
authProviderType一起使用：“service_account_impersonation”。

环境变量expansionSection标题为“环境变量扩展”

Gemini CLI 自动扩展 
 您的 MCP 服务器配置的 env 块中的环境变量。这使您可以安全地引用在 shell 或环境中定义的变量 
，而无需直接在 settings.json 文件中对敏感信息 
 进行硬编码。

扩展实用程序支持：

POSIX/Bash 语法：$VARIABLE_NAME 或 ${VARIABLE_NAME}（
所有平台均支持）

Windows 语法：%VARIABLE_NAME%（仅在 Windows 上运行时支持）

如果当前环境中未定义变量，则它将解析为空的
字符串。

示例：

"env": {  "API_KEY": "$MY_EXTERNAL_TOKEN",  "LOG_LEVEL": "$LOG_LEVEL",  "TEMP_DIR": "%TEMP%"}

安全与环境sanitizationSection标题为“安全与环境清理”

为了保护您的凭据，Gemini CLI 在 
 生成 MCP 服务器进程时执行环境清理。

自动 redactionSection 标题为“自动编辑”

默认情况下，CLI 会从基础
 环境（从主机进程继承）中编辑敏感环境变量。这可以防止敏感主机环境变量（如 AWS 密钥或 GitHub 令牌）
 意外 
 泄漏到可能执行恶意代码或记录 
 您的环境的任意第三方 MCP 服务器。这包括：

核心项目密钥：GEMINI_API_KEY、GOOGLE_API_KEY等

与敏感模式匹配的变量：*TOKEN*、*SECRET*、*PASSWORD*、
*KEY*、*AUTH*、*CREDENTIAL*。

证书和私钥模式。

显式 overridesSection 标题为“显式覆盖”

如果必须将环境变量传递给 MCP 服务器，您必须明确
在服务器配置的 env 属性中声明它 settings.json
（或 mcp_config.json（如果配置标准） MCP 客户或远程技能）。
显式定义的变量（包括来自扩展的变量）是可信的并且
不受自动编辑过程的影响。

这遵循以下安全原则：如果用户为特定服务器显式配置了变量
，则构成与该服务器共享该
特定数据的知情同意。

注意
即使显式定义，您也应该避免硬编码机密。
相反，请使用环境变量扩展
（例如，"MY_KEY"：“$MY_KEY”）在运行时安全地从主机
环境中提取值。

示例：传递一个 GitHub 令牌安全地发送到
官方 GitHub MCP 服务器通过
mcp_config.json

{  "mcpServers": {    "github": {      "command": "npx",      "args": ["-y", "@github/github-mcp-server"],      "env": {        "GITHUB_PERSONAL_ACCESS_TOKEN": "$GITHUB_PERSONAL_ACCESS_TOKEN"      }    }  }}

对远程 MCP serversSection 的 OAuth 支持，标题为“对远程 MCP 服务器的 OAuth 支持”

Gemini CLI 支持使用 SSE 或 
HTTP 传输对远程 MCP 服务器进行 OAuth 2.0 身份验证。这样可以安全访问需要
身份验证的MCP服务器。

自动 OAuth discoverySection 标题为“自动 OAuth 发现”

对于支持OAuth发现的服务器，您可以省略OAuth配置
并让CLI自动发现它：

{  "mcpServers": {    "discoveredServer": {      "url": "https://api.example.com/sse"    }  }}

CLI 将自动：

检测服务器何时需要 OAuth 身份验证（401 响应）

从服务器元数据发现 OAuth 端点

如果支持，执行动态客户端注册

处理 OAuth 流程和令牌管理

身份验证 flowSection 标题为“身份验证流程”

连接到启用 OAuth 的服务器时：

初始连接尝试失败，并显示 401 未经授权

OAuth 发现查找授权和令牌端点

打开浏览器进行用户身份验证（需要本地浏览器访问）

授权代码交换访问令牌

令牌被安全存储以供将来使用

连接重试成功并具有有效令牌

浏览器重定向 requirementsSection 标题为“浏览器重定向要求”

注意
OAuth 身份验证要求您的本地计算机可以：

打开网络浏览器进行身份验证

在 http://localhost:<random-port>/oauth/callback（或特定端口，如果通过 redirectUri 配置）上接收重定向

此功能不适用于：

无需浏览器访问的无头环境

没有 X11 转发的远程 SSH 会话

没有浏览器支持的容器化环境

授权服务器要求 (RFC 9207)标题为“授权服务器要求 (RFC 9207)”的部分

注意事项
Gemini CLI 验证 OAuth 授权中的 iss（颁发者）参数
每回复 RFC 9207 和 MCP
规格：

每当回调重定向中存在 iss 参数并且发现或配置了 
 预期颁发者时，iss 必须与 
 预期颁发者 URL 匹配。

当授权服务器元数据设置
authorization_response_iss_parameter_supported: true时，回调
重定向必须包含iss参数。

当发现的授权服务器元数据省略 
authorization_response_iss_parameter_supported 或将其设置为 false（对于 
 示例，未声明 RFC 9207 支持的提供程序）时，接受不带 
iss 的回调。

当您在 settings.json 中显式配置颁发者并且 Gemini CLI
 未发现授权服务器元数据时，回调重定向 
 必须包含 iss 参数。要接受 
 这样的服务器的无 ISS 回调，请在 oauth 配置中将 authorizationResponseIssParameterSupported 设置为 false
。

预期授权回调 exampleSection 标题为“预期授权回调示例”

当授权服务器声明
authorization_response_iss_parameter_supported: true时，重定向URI必须
包含 iss 参数：

HTTP/1.1 302 FoundLocation: http://localhost:<port>/oauth/callback?code=AUTH_CODE&state=STATE&iss=https%3A%2F%2Fauth.example.com

有效响应（已接受）：iss 与配置或发现的 
 颁发者 (https://auth.example.com) 匹配，或者当发现的 
 元数据未将 authorization_response_iss_parameter_supported 设置为 
true 时，忽略 iss。

需要时缺少 iss（被拒绝）：
http://localhost:<port>/oauth/callback？code=AUTH_CODE&state=STATE 当 
authorization_response_iss_parameter_supported 为 true 时，或者在没有发现元数据的情况下显式配置颁发者 
 时（失败并显示 HTTP 400：
响应中缺少颁发者参数）。

不匹配的 iss（被拒绝）：iss 指向不同的域或 
 包含用户信息（因 HTTP 400 失败：颁发者不匹配）。

具有显式 issuerSection 的配置示例，标题为“具有显式发行者的配置示例”

如果您的远程 MCP 服务器使用具有已知颁发者 URL 的授权服务器：

{  "mcpServers": {    "secureRemoteServer": {      "url": "https://mcp.example.com/sse",      "oauth": {        "enabled": true,        "issuer": "https://auth.example.com",        "authorizationUrl": "https://auth.example.com/oauth/authorize",        "tokenUrl": "https://auth.example.com/oauth/token",        "clientId": "gemini-cli-client",        "scopes": ["mcp:read", "mcp:write"]      }    }  }}

通过此配置，Gemini CLI 需要 
 授权回调中的 iss 参数。如果授权服务器在其 
 重定向中未返回 iss，请将 "authorizationResponseIssParameterSupported": false 添加到 
oauth 块中。 Gemini CLI 然后接受没有 iss 的回调，同时仍然 
 拒绝任何与配置的发行者不匹配的 iss 值。

管理 OAuth authenticationSection，标题为“管理 OAuth 身份验证”

使用/mcp auth命令管理OAuth身份验证：
终端窗口
# 列出需要 authentication/mcp 身份验证的服务器
# 使用特定的 server/mcp 身份验证 serverName 进行身份验证
# 重新验证 if tokens expire/mcp auth serverName

OAuth 配置 propertiesSection 标题为“OAuth 配置属性”

已启用（布尔值）：为此服务器启用 OAuth

clientId（字符串）：OAuth 客户端标识符（动态 
 注册时可选）

clientSecret（字符串）：OAuth 客户端密钥（公共客户端可选）

authorizationUrl（字符串）：OAuth 授权端点（自动发现的 
 如果省略）

颁发者（字符串）：授权服务器颁发者 URL（如果省略 
，则自动发现；根据 RFC 9207 进行验证）

authorizationResponseIssParameterSupported (boolean)：
授权服务器是否在授权响应
(RFC 9207)中返回iss参数。从授权服务器元数据自动发现；当在没有发现元数据的情况下显式配置颁发者时，默认
为 true。
设置为 false 以接受此类服务器的回调，而无需 ISS。

tokenUrl（字符串）：OAuth 令牌端点（如果省略，则自动发现）

范围（字符串[]）：必需的 OAuth 范围

redirectUri（字符串）：自定义重定向 URI（默认为 OS- 分配的 
 随机端口，例如 http://localhost:<random-port>/oauth/callback）

tokenParamName（字符串）：SSE URL 中令牌的查询参数名称

Audiences (string[])：令牌有效的受众

代币 managementSection 标题为“代币管理”

OAuth 令牌会自动：

安全地存储在~/.gemini/mcp-oauth-tokens.json中

过期时刷新（如果刷新令牌可用）

在每次连接尝试之前进行验证

无效或过期时清理

身份验证提供程序 typeSection 标题为“身份验证提供程序类型”

您可以使用 authProviderType
 属性指定身份验证提供程序类型：

authProviderType（字符串）：指定身份验证提供程序。可以是
以下之一：

dynamic_discovery（默认）：CLI将自动从服务器发现
OAuth配置。

google_credentials：CLI 将使用 Google 应用程序默认 
 凭据 (ADC) 与服务器进行身份验证。使用此提供程序时，
您必须指定所需的范围。

service_account_impersonation：CLI将模拟Google云
服务帐户以向服务器进行身份验证。这对于
访问IAP-受保护的服务非常有用（这是专门为云
运行服务设计的）。

Google credentialsSection 标题为“Google 凭据”

{  "mcpServers": {    "googleCloudServer": {      "httpUrl": "https://my-gcp-service.run.app/mcp",      "authProviderType": "google_credentials",      "oauth": {        "scopes": ["https://www.googleapis.com/auth/userinfo.email"]      }    }  }}

服务帐户 impersonationSection 标题为“服务帐户模拟”

要使用服务帐户模拟向服务器进行身份验证，您必须将 
 将 authProviderType 设置为 service_account_impersonation 并提供 
 以下属性：

targetAudience（字符串）：OAuth 客户端 ID 已列入您尝试访问的 
IAP- 受保护应用程序的白名单。

targetServiceAccount（字符串）：要模拟的 Google 云 
 服务帐户的电子邮件地址。

CLI 将使用您的本地应用程序默认凭据 (ADC) 为指定的服务帐户和受众生成 
OIDC ID 令牌。该令牌将
用于与MCP服务器进行身份验证。

设置 instructionsSection 标题为“设置说明”

创建或使用 
 现有 OAuth 2.0 客户端 ID。要使用现有的 OAuth 2.0 客户端 ID、
，请按照
如何共享 OAuth 客户端中的步骤操作。

将 OAuth ID 添加到应用程序的 
 编程访问 
 的允许列表中。由于 Cloud Run 尚不是 gcloud iap 中受支持的资源类型 
，因此您必须将项目上的客户端 ID 列入白名单。

创建服务帐号。
文档、
云控制台链接

将服务帐户和用户添加到 Cloud Run 服务本身或通过 gcloud 的 
“安全”选项卡中的 IAP 策略。

向所有将访问 MCP 服务器的用户和组授予必要的 
 权限，以 
 模拟服务帐户 
（例如，roles/iam.serviceAccountTokenCreator）。

为您的项目启用
IAM凭证API。

标题为“示例配置”的示例 configurationsSection

Python MCP 服务器 (stdio)标题为“Python MCP 服务器 (stdio)”的部分

{ "mcpServers": { "pythonTools": { "command": "python", "args": ["-m", "my_mcp_server", "--port", "8080"], "cwd": "./mcp-servers/python", "env": { "DATABASE_URL": "$DB_CONNECTION_STRING", "API_KEY": "${EXTERNAL_API_KEY}" }, "timeout": 15000 } }}

Node.js MCP 服务器 (stdio)标题为“Node.js MCP 服务器 (stdio)”的部分

{  "mcpServers": {    "nodeServer": {      "command": "node",      "args": ["dist/server.js", "--verbose"],      "cwd": "./mcp-servers/node",      "trust": true    }  }}

基于 Docker 的 MCP serverSection 标题为“基于 Docker 的 MCP 服务器”

{ "mcpServers"：{ "dockerizedServer"：{ "command"：“docker”，"args"：[“运行”，“-i”，“--rm”，“-e”，“API_KEY”， “-v”，“${PWD}：/workspace”，“my-mcp-server：最新”]，"env"：{        "API_KEY": "$EXTERNAL_SERVICE_TOKEN"      }}}}

基于 HTTP- MCP serverSection 标题为“HTTP- 基于 MCP 服务器”

{  "mcpServers": {    "httpServer": {      "httpUrl": "http://localhost:3000/mcp",      "timeout": 5000    }  }}

基于 HTTP- 的 MCP 服务器，具有自定义 headersSection，标题为“HTTP- 基于 MCP 服务器，具有自定义标头”

{  "mcpServers": {    "httpServerWithAuth": {      "httpUrl": "http://localhost:3000/mcp",      "headers": {        "Authorization": "Bearer your-api-token",        "X-Custom-Header": "custom-value",        "Content-Type": "application/json"      },      "timeout": 5000    }  }}

MCP 带有工具 filteringSection 的服务器，标题为“MCP 带有工具过滤的服务器”

{  "mcpServers": {    "filteredServer": {      "command": "python",      "args": ["-m", "my_mcp_server"],      "includeTools": ["safe_tool", "file_reader", "data_processor"],      // "excludeTools": ["dangerous_tool", "file_deleter"],      "timeout": 30000    }  }}

SSE MCP 服务器，SA impersonationSection 标题为“SSE MCP 服务器，具有 SA 模拟”

{  "mcpServers": {    "myIapProtectedServer": {      "url": "https://my-iap-service.run.app/sse",      "authProviderType": "service_account_impersonation",      "targetAudience": "YOUR_IAP_CLIENT_ID.apps.googleusercontent.com",      "targetServiceAccount": "your-sa@your-project.iam.gserviceaccount.com"    }  }}

深入发现过程 diveSection 标题为“深入探索发现过程”

当Gemini CLI启动时，它通过以下
详细过程执行MCP服务器发现：

1. 服务器迭代和 connectionSection 标题为“1。服务器迭代和连接”

对于 mcpServers 中每个配置的服务器：

状态跟踪开始：服务器状态设置为 CONNECTING

传输选择：基于配置属性：

httpUrl → StreamableHTTPClientTransport

url → SSEClientTransport

命令 → StdioClientTransport

连接建立：MCP客户端尝试在
配置的超时时间内进行连接

错误处理：记录连接失败，服务器状态
设置为DISCONNECTED

2. 工具 discoverySection 标题为“2。工具发现”

连接成功后：

工具列表：客户端调用MCP服务器的工具列表端点

模式验证：每个工具的函数声明都经过验证

工具过滤：根据includeTools和
excludeTools配置过滤工具

名称清理：工具名称已清理以满足 Gemini API
 要求：

除字母、数字、下划线（_）、连字符（-）、点
（.）和冒号（:）以外的字符均替换为下划线

长度超过 63 个字符的名称将被截断并用中间替换 
(...)

3. 工具命名和 namespacesSection 标题为“3。工具命名和命名空间”

为了防止多个服务器之间发生冲突或内置工具发生冲突，
每个发现的MCP工具都被分配了一个严格的命名空间。

自动 FQN：使用格式 mcp_{serverName}_{toolName} 无条件为所有 MCP 工具分配一个完全 
 限定名称 (FQN)。

注册表跟踪：工具注册表维护 
 这些 FQN 与其原始服务器身份之间的元数据映射。

覆盖：如果两台服务器在您的 
 配置中共享完全相同的别名，并提供具有完全相同名称的工具，则最后注册的 
 工具将覆盖前一个。

策略：要配置 MCP
 工具的权限（例如自动批准或拒绝），请参阅策略引擎文档中的 
MCP 工具的特殊语法 
。

注意事项
不要使用下划线 (_）在你的 MCP 服务器名称（例如，使用
我的服务器而不是 my_server）。策略解析器拆分完全限定的
姓名（mcp_server_tool) 在 mcp 后面的第一个下划线上_
前缀。如果您的服务器名称包含下划线，解析器将
误解服务器身份，这可能会导致通配符规则和安全性
政策会悄然失败。

4. 架构 processingSection 标题为“4。架构处理”

工具参数模式经过清理以实现 Gemini API 兼容性：

$schema 属性已删除

additionalProperties 已被剥离

默认情况下的 anyOf 已删除默认值（Vertex AI
 兼容性）

递归处理适用于嵌套模式

5. 连接 managementSection 标题为“5。连接管理”

发现后：

持久连接：成功注册工具的服务器维持
其连接

清理：不提供可用工具的服务器的连接
已关闭

状态更新：最终服务器状态设置为CONNECTED或
DISCONNECTED

工具执行 flowSection 标题为“工具执行流程”

当Gemini模型决定使用MCP工具时，会发生以下执行流程
：

1. 工具 invocationSection 标题为“1。工具调用”

该模型生成一个 FunctionCall：

工具名称：注册名称（可能带有前缀）

参数：JSON 与工具参数模式匹配的对象

2. 确认 processSection 标题为“2。确认流程”

每个 DiscoveredMCPTool 都实现复杂的确认逻辑：

基于信任的 bypassSection 标题为“基于信任的旁路”

if (this.trust) {  return false; // No confirmation needed}

动态允许-listingSection 标题为“动态允许列表”

系统维护以下内容的内部允许列表：

服务器级：serverName → 该服务器上的所有工具都是可信的

工具级别：serverName.toolName → 该特定工具受信任

用户选择 handlingSection 标题为“用户选择处理”

当需要确认时，用户可以选择：

进行一次：仅执行本次

始终允许此工具：添加到工具级别允许列表

始终允许此服务器：添加到服务器级别允许列表

取消：中止执行

3. ExecutionSection 标题为“3。执行”

确认后（或信任绕过）：

参数准备：根据工具的模式验证参数

MCP 调用：底层 CallableTool 通过以下方式调用服务器：

const functionCalls = [ {    name: this.serverToolName, // Original server tool name    args: params,  },];

响应处理：结果针对 LLM 上下文和用户 
 显示进行格式化

4. 响应 handlingSection 标题为“4。响应处理”

执行结果包含：

llmContent：语言模型上下文的原始响应部分

returnDisplay：用于用户显示的格式化输出（通常是 markdown
 代码块中的 JSON）

如何与您的 MCP serverSection 交互，标题为“如何与您的 MCP 服务器交互”

使用标题为“使用 /mcp 命令”的 /mcp commandSection

/mcp 命令提供有关 MCP 服务器 
 设置的全面信息：
 终端窗口 
/mcp

这显示：

服务器列表：所有已配置的MCP服务器

连接状态：CONNECTED、CONNECTING或DISCONNECTED

服务器详细信息：配置摘要（不包括敏感数据）

可用工具：每个服务器的工具列表及其说明

Discovery state：整体发现过程状态

示例 /mcp outputSection 标题为“示例 /mcp 输出”

MCP 服务器状态：
📡 pythonTools (CONNECTED) 命令：python -m my_mcp_server --port 8080 工作目录：./mcp-servers/python超时：15000ms 工具：calculate_sum、file_analyzer、data_processor
🔌 nodeServer (DISCONNECTED) 命令：节点 dist/server.js --verbose 错误：连接拒绝
🐳 dockerizedServer (CONNECTED) 命令: docker run -i --rm -e API_KEY my-mcp-server:最新工具: mcp_dockerizedServer_docker_deploy, mcp_dockerizedServer_docker_status
发现状态: COMPLETED

工具 usageSection 标题为“工具使用”

一旦发现，MCP 工具就可用于 Gemini 模型，就像内置 
 工具一样。该模型将自动：

根据您的要求选择合适的工具

显示确认对话框（除非服务器受信任）

使用正确的参数执行工具

以用户友好的格式显示结果

状态监控和troubleshootingSection标题为“状态监控和故障排除”

连接 statesSection 标题为“连接状态”

MCP 集成跟踪多个状态：

覆盖扩展 configurationsSection 标题为“覆盖扩展配置”

如果 MCP 服务器由扩展程序（例如 
google-workspace 扩展程序）提供，您仍然可以在本地 
settings.json 中覆盖其设置。 Gemini CLI 将您的本地配置与扩展的 
 默认值合并：

工具列表：安全地合并工具列表，以确保最严格的
策略获胜：

排除 (excludeTools)：数组被组合（联合）。如果任一
源阻止了工具，则该工具将保持禁用状态。

包含 (includeTools)：数组相交。如果两个来源 
 都提供允许列表，则仅启用两个列表中存在的工具。如果
只有一个来源提供允许列表，则遵守该列表。

优先级：excludeTools 始终优先于 includeTools。

这可确保您始终对扩展程序 
 提供的工具拥有否决权，并且扩展程序无法重新启用您从 
 个人白名单中忽略的工具。

环境变量： env 对象被合并。如果在两个地方都定义了相同的变量
，则您的本地值优先。

标量属性：命令、url 和超时等属性将被 
 替换为您的本地值（如果提供）。

覆盖示例：

{  "mcpServers": {    "google-workspace": {      "excludeTools": ["gmail.send"]    }  }}

服务器状态 (MCPServerStatus) 标题为“服务器状态 (MCPServerStatus)”的部分

DISCONNECTED：服务器未连接或有错误

CONNECTING：正在进行连接尝试

CONNECTED：服务器已连接并准备就绪

发现状态 (MCPDiscoveryState) 标题为“发现状态 (MCPDiscoveryState)”的部分

NOT_STARTED：发现尚未开始

IN_PROGRESS：当前正在发现服务器

COMPLETED：发现完成（有或没有错误）

常见问题和 solutionsSection 标题为“常见问题和解决方案”

服务器不会 connectSection 标题为“服务器无法连接”

症状：服务器显示 DISCONNECTED 状态

故障排除：

检查配置：验证命令、args 和 cwd 是否正确

手动测试：直接运行服务器命令以确保其有效

检查依赖关系：确保安装了所有必需的包

查看日志：在 CLI 输出中查找错误消息

验证权限：确保CLI可以执行服务器命令

没有标题为“未发现任何工具”的工具 discoveredSection

症状：服务器已连接但没有可用的工具

故障排除：

验证工具注册：确保您的服务器实际注册了工具

检查MCP协议：确认您的服务器正确实现MCP工具列表

查看服务器日志：检查 stderr 输出中是否存在服务器端错误

测试工具列表：手动测试服务器的工具发现端点

工具不是 executingSection 标题为“工具未执行”

症状：发现工具但在执行过程中失败

故障排除：

参数验证：确保您的工具接受预期的参数

架构兼容性：验证您的输入架构是否有效 JSON 架构

错误处理：检查您的工具是否抛出未处理的异常

超时问题：考虑增加超时设置

沙盒 compatibilitySection 标题为“沙盒兼容性”

症状：启用沙箱时，MCP 服务器出现故障

解决方案：

基于 Docker 的服务器：使用包含所有依赖项的 Docker 容器

路径可访问性：确保服务器可执行文件在
沙箱中可用

网络访问：配置沙箱以允许必要的网络连接

环境变量：验证所需的环境变量已通过
通过

调试 tipsSection 标题为“调试技巧”

启用调试模式：使用 --debug 运行 CLI 以获得详细输出（使用 F12
 以交互模式打开调试控制台）

检查stderr：MCP服务器stderr被捕获并记录（INFO消息
已过滤）

测试隔离：在集成之前独立测试您的 MCP 服务器

增量设置：从简单的工具开始，然后添加复杂的
功能

经常使用【/mcp】：开发过程中监控服务器状态

标题为“重要说明”的重要 notesSection

安全 considerationsSection 标题为“安全注意事项”

信任设置：信任选项绕过所有确认对话框。谨慎使用
并且仅用于您完全控制的服务器

访问令牌：配置包含 API 密钥或令牌的环境变量 
 时，请注意安全。有关 Gemini CLI 如何保护您的凭据的详细信息，请参阅
安全和环境清理
。

沙箱兼容性：使用沙箱时，确保MCP服务器
在沙箱环境中可用

私人数据：使用范围广泛的个人访问令牌可能会导致存储库之间的
信息泄漏。

性能和资源 managementSection 标题为“性能和资源管理”

连接持久性：CLI 与成功注册工具的 
 服务器保持持久连接

自动清理：与不提供工具的服务器的连接
自动关闭

超时管理：根据服务器的
响应特征配置适当的超时

资源监控：MCP服务器作为单独的进程运行并消耗
系统资源

架构 compatibilitySection 标题为“架构兼容性”

属性剥离：系统自动删除某些 schema
 属性（$schema，additionalProperties）以实现 Gemini API 兼容性

名称清理：工具名称会自动清理以满足 API
 要求

冲突解决：通过自动添加前缀解决服务器之间的工具名称冲突

这种全面的集成使 MCP 服务器成为扩展 
Gemini CLI 功能的强大方式，同时保持安全性、可靠性和易用性 
。

从 toolsSection 返回丰富的内容，标题为“从工具返回丰富的内容”

MCP 工具不仅限于返回简单文本。您可以在
单个工具响应中返回丰富的
多部分内容，包括文本、图像、音频和其他二进制数据。这使您可以构建强大的工具，可以一次性为模型提供
多种信息。

从工具返回的所有数据都会经过处理并作为下一代的上下文 
 发送到模型，使其能够推理或总结所提供的 
 信息。

它是如何工作的 worksSection 标题为“它是如何工作的”

要返回丰富的内容，您的工具的响应必须遵循 MCP
规格
CallToolResult.
结果的内容字段应该是一个数组 ContentBlock 对象。
Gemini CLI 将正确处理该数组，将文本与二进制数据分开
并将其包装为模型。

您可以在内容数组中混合和匹配不同的内容块类型。 
支持的块类型包括：

文字

图像

音频

资源（嵌入内容）

resource_link

示例：返回文本和标题为“示例：返回文本和图像”的 imageSection

以下是来自 MCP 工具的有效 JSON 响应示例，该工具返回 
 文本描述和图像：

{  "content": [    {      "type": "text",      "text": "Here is the logo you requested."    },    {      "type": "image",      "data": "BASE64_ENCODED_IMAGE_DATA_HERE",      "mimeType": "image/png"    },    {      "type": "text",      "text": "The logo was created in 2025."    }  ]}

当Gemini CLI收到此响应时，它将：

提取所有文本并将其组合成模型的单个 functionResponse 部分 
。

将图像数据呈现为单独的 inlineData 部分。

在 CLI 中提供清晰、用户友好的摘要，表明已收到文本 
 和图像。

这使您能够构建复杂的工具，为 Gemini 模型提供丰富的多模态 
 上下文。

MCP 提示为斜杠 commandsSection 标题为“MCP 提示为斜杠命令”

除了工具之外，MCP服务器还可以公开预定义的提示，这些提示可以
作为Gemini CLI中的斜杠命令执行。这使您可以为
常见或复杂的查询创建快捷方式，可以通过名称轻松调用。

在标题为“在服务器上定义提示”的 serverSection 上定义提示

下面是一个定义提示的 stdio MCP 服务器的小示例：


<details>
<summary>页面展示组件源码（保留原样）</summary>

~~~jsx
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';import { z } from 'zod';
~~~

</details>
const server = new McpServer({  name: 'prompt-server',  version: '1.0.0',});
server.registerPrompt( '诗歌作家', { title: '诗歌作家', description: '写一首漂亮的俳句', argsSchema: { title: z.string(), mood: z.string().optional() }, }, ({ title, mood }) => ({ messages: [ { role: 'user', content: { type: 'text', text: `Write a haiku${mood ? ` 心情 ${mood}` : ''} called ${title}. Note that a haiku is 5 syllables followed by 7 syllables followed by 5 syllables `, }, }, ],  }),);
const transport = new StdioServerTransport();await server.connect(transport);

这可以包含在 mcpServers 下的 settings.json 中：

{  "mcpServers": {    "nodeServer": {      "command": "node",      "args": ["filename.ts"]    }  }}

调用标题为“调用提示”的 promptsSection

Once a prompt is discovered, you can invoke it using its name as a slash
command. CLI 将自动处理解析参数。
终端窗口
/poem-writer --title="Gemini CLI" --mood="reverent"

或者，使用位置参数：
终端窗口
/poem-writer“Gemini CLI”尊敬

当您运行此命令时，Gemini CLI 使用提供的参数在 
MCP 服务器上执行 prompts/get 方法。 The server is responsible for
substituting the arguments into the prompt template and returning the final
prompt text.然后，CLI 将此提示发送到模型以执行。 This
provides a convenient way to automate and share common workflows.

使用 Gemini mcpSection 管理 MCP 服务器，标题为“使用 Gemini mcp 管理 MCP 服务器”

虽然您始终可以通过手动编辑 
settings.json 文件来配置 MCP 服务器，但 Gemini CLI 提供了一组方便的命令来以编程方式管理 
 您的服务器配置。这些命令简化了添加、列出和删除 MCP 服务器的 
 过程，无需直接 
 编辑 JSON 文件。

添加服务器 (gemini mcp add)标题为“添加服务器 (gemini mcp add)”的部分

add 命令会在您的 settings.json 中配置一个新的 MCP 服务器。基于
范围（-s，--scope），它将被添加到用户配置
~/.gemini/settings.json或项目配置.gemini/settings.json文件中。

命令：
终端窗口
gemini mcp add [选项] <name> <commandOrUrl> [args...]

<name>：服务器的唯一名称。

<commandOrUrl>：要执行的命令（对于 stdio）或 URL（对于 
http/sse）。

[args...]: <https://geminicli.com/docs/tools/mcp-server/Optional> arguments for a stdio command.

Options (flags):

-s，--scope：配置范围（用户或项目）。 [默认：“项目”]

-t，--transport：传输类型（stdio、sse、http）。 [默认：“stdio”]

-e, --env：设置环境变量（例如-e KEY=value）。

-H, --header：为 SSE 和 HTTP 传输设置 HTTP 标头（例如，
-H "X-Api-Key: abc123" -H "Authorization: Bearer abc123").

--timeout：设置连接超时时间（以毫秒为单位）。

--trust：信任服务器（绕过所有工具调用确认提示）。

--description：设置服务器的描述。

--include-tools：要包含的以逗号分隔的工具列表。

--exclude-tools：要排除的以逗号分隔的工具列表。

添加标题为“添加 stdio 服务器”的 stdio serverSection

这是运行本地服务器的默认传输。
终端窗口
# 基本语法gemini mcp add [options] <name> <command> [args...]
# 示例：添加本地 servergemini mcp add -e API_KEY=123 -e DEBUG=true my-stdio-server /path/to/server arg1 arg2 arg3
# 示例：添加本地 python servergemini mcp add python-server python server.py -- --server-arg my-value

添加标题为“添加 HTTP 服务器”的 HTTP serverSection

此传输适用于使用可流传输的 HTTP 传输的服务器。
终端窗口
# 基本语法gemini mcp add --transport http <name> <url>
# 示例：添加 HTTP servergemini mcp add --transport http http-server https://api.example.com/mcp/
# 示例：添加带有身份验证标头的 HTTP 服务器gemini mcp add --transport http --header "Authorization: Bearer abc123" secure-http https://api.example.com/mcp/

添加标题为“添加 SSE 服务器”的 SSE serverSection

此传输适用于使用服务器发送事件 (SSE) 的服务器。
终端窗口
# 基本语法gemini mcp add --transport sse <name> <url>
# 示例：添加 SSE servergemini mcp add --transport sse sse-server https://api.example.com/sse/
# 示例：添加带有身份验证标头的 SSE 服务器gemini mcp add --transport sse --header "Authorization: Bearer abc123" secure-sse https://api.example.com/sse/

列出服务器（gemini mcp 列表）标题为“列出服务器（gemini mcp 列表）”的部分

要查看当前配置的所有 MCP 服务器，请使用 list 命令。它
显示每个服务器的名称、配置详细信息和连接状态。此
命令没有标志。

命令：
终端窗口
gemini mcp list

注意
为了安全起见，如果
当前文件夹受信任，则仅对stdio MCP服务器（使用
命令属性的服务器）进行测试并显示为“已连接”。如果该文件夹不受信任，它们将显示为
“已断开连接”。使用gemini trust信任当前文件夹。

输出示例：
终端窗口
✓ stdio-server: 命令：python3 server.py (stdio) - 已连接✓ http-server: https://api.example.com/mcp (http) - 已连接✗ sse-server: https://api.example.com/sse (sse) - 已断开连接

故障排除和 DiagnosticsSection 标题为“故障排除和诊断”

为了最大限度地减少启动期间的噪音，后台服务器 
 的 MCP 连接错误“默认情况下是静默的”。如果在启动过程中检测到问题，将显示一个 
 信息提示：“检测到 MCP 问题。运行 /mcp 列表以获取 
 状态。”

在以下情况下，将自动
重新启用针对特定服务器的详细、可操作的诊断：

您运行交互式命令，例如 /mcp list、/mcp auth 等。

该模型尝试从该服务器执行工具。

您从该服务器调用 MCP 提示符。

您还可以在 shell 中使用 Gemini mcp list 来查看 
 所有已配置服务器的连接错误。

删除服务器 (gemini mcp remove) 标题为“删除服务器 (gemini mcp remove)”的部分

要从配置中删除服务器，请使用带有 
 服务器名称的删除命令。

命令：
终端窗口
gemini mcp删除<name>

Options (flags):

-s，--scope：配置范围（用户或项目）。 [默认：“项目”]

示例：
终端窗口
gemini mcp 删除我的服务器

这将根据范围（-s，--scope）从
适当的settings.json文件中的mcpServers对象中查找并删除“my-server”条目。

Enabling/disabling 一台服务器（gemini mcp 启用，gemini mcp 禁用）标题为“Enabling/disabling 一台服务器（gemini mcp 启用，gemini mcp 禁用）”的部分

暂时禁用 MCP 服务器而不删除其配置，或 
 重新启用之前禁用的服务器。

命令：
终端窗口
gemini mcp启用<name>[--session]gemini mcp禁用<name>[--session]

Options (flags):

--session：仅对此会话应用更改（不保留到文件）。

已禁用的服务器在/mcp状态中显示为“已禁用”，但不会连接或
提供工具。启用状态存储在
~/.gemini/mcp-server-enablement.json.中

在活动会话期间，相同的命令可用作斜杠命令：
/mcp 启用 <name> 和 /mcp 禁用 <name>。

InstructionsSection 标题为“说明”

Gemini CLI支持
MCP服务器指令，
将附加到系统指令中。   最后更新时间：10 月 6、2026 
本网站使用 Google 的 cookie 来提供和提高其服务质量并分析 
 流量。
 
我明白。
 条款 | 隐私


## 原文对照

<details>
<summary>展开完整原文</summary>

MCP servers with Gemini CLI | Gemini CLI
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
  What is an MCP server?   
  Core integration architecture   
  Discovery Layer (mcp-client.ts)   
  Execution layer (mcp-tool.ts)   
  Transport mechanisms      
  Working with MCP resources   
  Discovery and listing   
  Referencing resources in a conversation      
  How to set up your MCP server   
  Configure the MCP server in settings.json   
  Configuration structure   
  Configuration properties   
  Environment variable expansion   
  Security and environment sanitization   
  OAuth support for remote MCP servers   
  Example configurations   
  SSE MCP server with SA impersonation      
  Discovery process deep dive   
  1. Server iteration and connection   
  2. Tool discovery   
  3. Tool naming and namespaces   
  4. Schema processing   
  5. Connection management      
  Tool execution flow   
  1. Tool invocation   
  2. Confirmation process   
  3. Execution   
  4. Response handling      
  How to interact with your MCP server   
  Using the /mcp command   
  Example /mcp output   
  Tool usage      
  Status monitoring and troubleshooting   
  Connection states   
  Common issues and solutions   
  Debugging tips      
  Important notes   
  Security considerations   
  Performance and resource management   
  Schema compatibility      
  Returning rich content from tools   
  How it works   
  Example: Returning text and an image      
  MCP prompts as slash commands   
  Defining prompts on the server   
  Invoking prompts      
  Managing MCP servers with gemini mcp   
  Adding a server (gemini mcp add)   
  Listing servers (gemini mcp list)      
  Troubleshooting and Diagnostics   
  Removing a server (gemini mcp remove)   
  Enabling/disabling a server (gemini mcp enable, gemini mcp disable)      
  Instructions     
On this page 
  Introduction   
  What is an MCP server?   
  Core integration architecture   
  Discovery Layer (mcp-client.ts)   
  Execution layer (mcp-tool.ts)   
  Transport mechanisms      
  Working with MCP resources   
  Discovery and listing   
  Referencing resources in a conversation      
  How to set up your MCP server   
  Configure the MCP server in settings.json   
  Configuration structure   
  Configuration properties   
  Environment variable expansion   
  Security and environment sanitization   
  OAuth support for remote MCP servers   
  Example configurations   
  SSE MCP server with SA impersonation      
  Discovery process deep dive   
  1. Server iteration and connection   
  2. Tool discovery   
  3. Tool naming and namespaces   
  4. Schema processing   
  5. Connection management      
  Tool execution flow   
  1. Tool invocation   
  2. Confirmation process   
  3. Execution   
  4. Response handling      
  How to interact with your MCP server   
  Using the /mcp command   
  Example /mcp output   
  Tool usage      
  Status monitoring and troubleshooting   
  Connection states   
  Common issues and solutions   
  Debugging tips      
  Important notes   
  Security considerations   
  Performance and resource management   
  Schema compatibility      
  Returning rich content from tools   
  How it works   
  Example: Returning text and an image      
  MCP prompts as slash commands   
  Defining prompts on the server   
  Invoking prompts      
  Managing MCP servers with gemini mcp   
  Adding a server (gemini mcp add)   
  Listing servers (gemini mcp list)      
  Troubleshooting and Diagnostics   
  Removing a server (gemini mcp remove)   
  Enabling/disabling a server (gemini mcp enable, gemini mcp disable)      
  Instructions                 
 MCP servers with Gemini CLI     Copy as Markdown Copied!             
This document provides a guide to configuring and using Model Context Protocol
(MCP) servers with Gemini CLI.

What is an MCP server?Section titled “What is an MCP server?”

An MCP server is an application that exposes tools and resources to the Gemini
CLI through the Model Context Protocol, allowing it to interact with external
systems and data sources. MCP servers act as a bridge between the Gemini model
and your local environment or other services like APIs.

An MCP server enables Gemini CLI to:

Discover tools: List available tools, their descriptions, and parameters
through standardized schema definitions.

Execute tools: Call specific tools with defined arguments and receive
structured responses.

Access resources: Read data from specific resources that the server
exposes (files, API payloads, reports, etc.).

With an MCP server, you can extend Gemini CLI’s capabilities to perform actions
beyond its built-in features, such as interacting with databases, APIs, custom
scripts, or specialized workflows.

Core integration architectureSection titled “Core integration architecture”

Gemini CLI integrates with MCP servers through a sophisticated discovery and
execution system built into the core package (packages/core/src/tools/):

Discovery Layer (mcp-client.ts)Section titled “Discovery Layer (mcp-client.ts)”

The discovery process is orchestrated by discoverMcpTools(), which:

Iterates through configured servers from your settings.json
mcpServers configuration

Establishes connections using appropriate transport mechanisms (Stdio,
SSE, or Streamable HTTP)

Fetches tool definitions from each server using the MCP protocol

Sanitizes and validates tool schemas for compatibility with the Gemini
API

Registers tools in the global tool registry with conflict resolution

Fetches and registers resources if the server exposes any

Execution layer (mcp-tool.ts)Section titled “Execution layer (mcp-tool.ts)”

Each discovered MCP tool is wrapped in a DiscoveredMCPTool instance that:

Handles confirmation logic based on server trust settings and user
preferences

Manages tool execution by calling the MCP server with proper parameters

Processes responses for both the LLM context and user display

Maintains connection state and handles timeouts

Transport mechanismsSection titled “Transport mechanisms”

Gemini CLI supports three MCP transport types:

Stdio Transport: Spawns a subprocess and communicates via stdin/stdout

SSE Transport: Connects to Server-Sent Events endpoints

Streamable HTTP Transport: Uses HTTP streaming for communication

Working with MCP resourcesSection titled “Working with MCP resources”

Some MCP servers expose contextual “resources” in addition to the tools and
prompts. Gemini CLI discovers these automatically and gives you the possibility
to reference them in the chat. For more information on the tools used to
interact with these resources, see MCP resource tools.

Discovery and listingSection titled “Discovery and listing”

When discovery runs, the CLI fetches each server’s resources/list results.

The /mcp command displays a Resources section alongside Tools and Prompts
for every connected server.

This returns a concise, plain-text list of URIs plus metadata.

Referencing resources in a conversationSection titled “Referencing resources in a conversation”

You can use the same @ syntax already known for referencing local files:

@server://resource/path

Resource URIs appear in the completion menu together with filesystem paths. When
you submit the message, the CLI calls resources/read and injects the content
in the conversation.

How to set up your MCP serverSection titled “How to set up your MCP server”

Gemini CLI uses the mcpServers configuration in your settings.json file to
locate and connect to MCP servers. This configuration supports multiple servers
with different transport mechanisms.

Configure the MCP server in settings.jsonSection titled “Configure the MCP server in settings.json”

You can configure MCP servers in your settings.json file in two main ways:
through the top-level mcpServers object for specific server definitions, and
through the mcp object for global settings that control server discovery and
execution.

Global MCP settings (mcp)Section titled “Global MCP settings (mcp)”

The mcp object in your settings.json lets you define global rules for all
MCP servers.

mcp.serverCommand (string): A global command to start an MCP server.

mcp.allowed (array of strings): A list of MCP server names to allow. If
this is set, only servers from this list (matching the keys in the
mcpServers object) will be connected to.

mcp.excluded (array of strings): A list of MCP server names to exclude.
Servers in this list will not be connected to.

Example:

{  "mcp": {    "allowed": ["my-trusted-server"],    "excluded": ["experimental-server"]  }}

Server-specific configuration (mcpServers)Section titled “Server-specific configuration (mcpServers)”

The mcpServers object is where you define each individual MCP server you want
the CLI to connect to.

Configuration structureSection titled “Configuration structure”

Add an mcpServers object to your settings.json file:

{ ...file contains other config objects  "mcpServers": {    "serverName": {      "command": "path/to/server",      "args": ["--arg1", "value1"],      "env": {        "API_KEY": "$MY_API_TOKEN"      },      "cwd": "./server-directory",      "timeout": 30000,      "trust": false    }  }}

Configuration propertiesSection titled “Configuration properties”

Each server configuration supports the following properties:

Required (one of the following)Section titled “Required (one of the following)”

command (string): Path to the executable for Stdio transport

url (string): SSE endpoint URL (for example,
"http://localhost:8080/sse")

httpUrl (string): HTTP streaming endpoint URL

OptionalSection titled “Optional”

args (string[]): Command-line arguments for Stdio transport

headers (object): Custom HTTP headers when using url or httpUrl

env (object): Environment variables for the server process. Values can
reference environment variables using $VAR_NAME or ${VAR_NAME} syntax (all
platforms), or %VAR_NAME% (Windows only).

cwd (string): Working directory for Stdio transport

timeout (number): Request timeout in milliseconds (default: 600,000ms =
10 minutes)

trust (boolean): When true, bypasses all tool call confirmations for
this server (default: false)

includeTools (string[]): List of tool names to include from this MCP
server. When specified, only the tools listed here will be available from this
server (allowlist behavior). If not specified, all tools from the server are
enabled by default.

excludeTools (string[]): List of tool names to exclude from this MCP
server. Tools listed here will not be available to the model, even if they are
exposed by the server. excludeTools takes precedence over includeTools. If
a tool is in both lists, it will be excluded.

targetAudience (string): The OAuth Client ID allowlisted on the
IAP-protected application you are trying to access. Used with
authProviderType: 'service_account_impersonation'.

targetServiceAccount (string): The email address of the Google Cloud
Service Account to impersonate. Used with
authProviderType: 'service_account_impersonation'.

Environment variable expansionSection titled “Environment variable expansion”

Gemini CLI automatically expands environment variables in the env block of
your MCP server configuration. This lets you securely reference variables
defined in your shell or environment without hardcoding sensitive information
directly in your settings.json file.

The expansion utility supports:

POSIX/Bash syntax: $VARIABLE_NAME or ${VARIABLE_NAME} (supported on
all platforms)

Windows syntax: %VARIABLE_NAME% (supported only when running on Windows)

If a variable is not defined in the current environment, it resolves to an empty
string.

Example:

"env": {  "API_KEY": "$MY_EXTERNAL_TOKEN",  "LOG_LEVEL": "$LOG_LEVEL",  "TEMP_DIR": "%TEMP%"}

Security and environment sanitizationSection titled “Security and environment sanitization”

To protect your credentials, Gemini CLI performs environment sanitization when
spawning MCP server processes.

Automatic redactionSection titled “Automatic redaction”

By default, the CLI redacts sensitive environment variables from the base
environment (inherited from the host process). This prevents the accidental
leakage of sensitive host environment variables (like AWS keys or GitHub tokens)
to arbitrary third-party MCP servers that might execute malicious code or log
your environment. This includes:

Core project keys: GEMINI_API_KEY, GOOGLE_API_KEY, etc.

Variables matching sensitive patterns: *TOKEN*, *SECRET*, *PASSWORD*,
*KEY*, *AUTH*, *CREDENTIAL*.

Certificates and private key patterns.

Explicit overridesSection titled “Explicit overrides”

If an environment variable must be passed to an MCP server, you must explicitly
state it in the env property of the server configuration in settings.json
(or mcp_config.json if configuring standard MCP clients or remote skills).
Explicitly defined variables (including those from extensions) are trusted and
are not subjected to the automatic redaction process.

This follows the security principle that if a variable is explicitly configured
by the user for a specific server, it constitutes informed consent to share that
specific data with that server.

Note
Even when explicitly defined, you should avoid hardcoding secrets.
Instead, use environment variable expansion
(for example, "MY_KEY": "$MY_KEY") to securely pull the value from your host
environment at runtime.

Example: Passing a GitHub Token securely to the
official GitHub MCP server via
mcp_config.json

{  "mcpServers": {    "github": {      "command": "npx",      "args": ["-y", "@github/github-mcp-server"],      "env": {        "GITHUB_PERSONAL_ACCESS_TOKEN": "$GITHUB_PERSONAL_ACCESS_TOKEN"      }    }  }}

OAuth support for remote MCP serversSection titled “OAuth support for remote MCP servers”

Gemini CLI supports OAuth 2.0 authentication for remote MCP servers using SSE or
HTTP transports. This enables secure access to MCP servers that require
authentication.

Automatic OAuth discoverySection titled “Automatic OAuth discovery”

For servers that support OAuth discovery, you can omit the OAuth configuration
and let the CLI discover it automatically:

{  "mcpServers": {    "discoveredServer": {      "url": "https://api.example.com/sse"    }  }}

The CLI will automatically:

Detect when a server requires OAuth authentication (401 responses)

Discover OAuth endpoints from server metadata

Perform dynamic client registration if supported

Handle the OAuth flow and token management

Authentication flowSection titled “Authentication flow”

When connecting to an OAuth-enabled server:

Initial connection attempt fails with 401 Unauthorized

OAuth discovery finds authorization and token endpoints

Browser opens for user authentication (requires local browser access)

Authorization code is exchanged for access tokens

Tokens are stored securely for future use

Connection retry succeeds with valid tokens

Browser redirect requirementsSection titled “Browser redirect requirements”

Caution
OAuth authentication requires that your local machine can:

Open a web browser for authentication

Receive redirects on http://localhost:<random-port>/oauth/callback (or a specific port if configured via redirectUri)

This feature will not work in:

Headless environments without browser access

Remote SSH sessions without X11 forwarding

Containerized environments without browser support

Authorization server requirements (RFC 9207)Section titled “Authorization server requirements (RFC 9207)”

Caution
Gemini CLI validates the iss (issuer) parameter in OAuth authorization
responses per RFC 9207 and the MCP
specification:

Whenever the iss parameter is present in the callback redirect and an
expected issuer is discovered or configured, iss must match the
expected issuer URL.

When the authorization server metadata sets
authorization_response_iss_parameter_supported: true, the callback
redirect must include the iss parameter.

When the discovered authorization server metadata omits
authorization_response_iss_parameter_supported or sets it to false (for
example, providers that do not declare RFC 9207 support), callbacks without
iss are accepted.

When you configure issuer explicitly in settings.json and Gemini CLI
doesn’t discover authorization server metadata, the callback redirect
must include the iss parameter. To accept callbacks without iss for
such a server, set authorizationResponseIssParameterSupported to false
in the oauth configuration.

Expected authorization callback exampleSection titled “Expected authorization callback example”

When the authorization server declares
authorization_response_iss_parameter_supported: true, the redirect URI must
include the iss parameter:

HTTP/1.1 302 FoundLocation: http://localhost:<port>/oauth/callback?code=AUTH_CODE&state=STATE&iss=https%3A%2F%2Fauth.example.com

Valid response (accepted): iss matches the configured or discovered
issuer (https://auth.example.com), or iss is omitted when the discovered
metadata doesn’t set authorization_response_iss_parameter_supported to
true.

Missing iss when required (rejected):
http://localhost:<port>/oauth/callback?code=AUTH_CODE&state=STATE when
authorization_response_iss_parameter_supported is true, or when issuer
is configured explicitly without discovered metadata (fails with HTTP 400:
Missing issuer parameter in response).

Mismatched iss (rejected): iss points to a different domain or
includes userinfo (fails with HTTP 400: Issuer mismatch).

Configuration example with explicit issuerSection titled “Configuration example with explicit issuer”

If your remote MCP server uses an authorization server with a known issuer URL:

{  "mcpServers": {    "secureRemoteServer": {      "url": "https://mcp.example.com/sse",      "oauth": {        "enabled": true,        "issuer": "https://auth.example.com",        "authorizationUrl": "https://auth.example.com/oauth/authorize",        "tokenUrl": "https://auth.example.com/oauth/token",        "clientId": "gemini-cli-client",        "scopes": ["mcp:read", "mcp:write"]      }    }  }}

With this configuration, Gemini CLI requires the iss parameter in the
authorization callback. If the authorization server doesn’t return iss in its
redirects, add "authorizationResponseIssParameterSupported": false to the
oauth block. Gemini CLI then accepts callbacks without iss while still
rejecting any iss value that doesn’t match the configured issuer.

Managing OAuth authenticationSection titled “Managing OAuth authentication”

Use the /mcp auth command to manage OAuth authentication:
Terminal window
# List servers requiring authentication/mcp auth
# Authenticate with a specific server/mcp auth serverName
# Re-authenticate if tokens expire/mcp auth serverName

OAuth configuration propertiesSection titled “OAuth configuration properties”

enabled (boolean): Enable OAuth for this server

clientId (string): OAuth client identifier (optional with dynamic
registration)

clientSecret (string): OAuth client secret (optional for public clients)

authorizationUrl (string): OAuth authorization endpoint (auto-discovered
if omitted)

issuer (string): Authorization server issuer URL (auto-discovered if
omitted; validated per RFC 9207)

authorizationResponseIssParameterSupported (boolean): Whether the
authorization server returns the iss parameter in authorization responses
(RFC 9207). Auto-discovered from the authorization server metadata; defaults
to true when issuer is configured explicitly without discovered metadata.
Set to false to accept callbacks without iss for such a server.

tokenUrl (string): OAuth token endpoint (auto-discovered if omitted)

scopes (string[]): Required OAuth scopes

redirectUri (string): Custom redirect URI (defaults to an OS-assigned
random port, e.g., http://localhost:<random-port>/oauth/callback)

tokenParamName (string): Query parameter name for tokens in SSE URLs

audiences (string[]): Audiences the token is valid for

Token managementSection titled “Token management”

OAuth tokens are automatically:

Stored securely in ~/.gemini/mcp-oauth-tokens.json

Refreshed when expired (if refresh tokens are available)

Validated before each connection attempt

Cleaned up when invalid or expired

Authentication provider typeSection titled “Authentication provider type”

You can specify the authentication provider type using the authProviderType
property:

authProviderType (string): Specifies the authentication provider. Can be
one of the following:

dynamic_discovery (default): The CLI will automatically discover the
OAuth configuration from the server.

google_credentials: The CLI will use the Google Application Default
Credentials (ADC) to authenticate with the server. When using this provider,
you must specify the required scopes.

service_account_impersonation: The CLI will impersonate a Google Cloud
Service Account to authenticate with the server. This is useful for
accessing IAP-protected services (this was specifically designed for Cloud
Run services).

Google credentialsSection titled “Google credentials”

{  "mcpServers": {    "googleCloudServer": {      "httpUrl": "https://my-gcp-service.run.app/mcp",      "authProviderType": "google_credentials",      "oauth": {        "scopes": ["https://www.googleapis.com/auth/userinfo.email"]      }    }  }}

Service account impersonationSection titled “Service account impersonation”

To authenticate with a server using Service Account Impersonation, you must set
the authProviderType to service_account_impersonation and provide the
following properties:

targetAudience (string): The OAuth Client ID allowlisted on the
IAP-protected application you are trying to access.

targetServiceAccount (string): The email address of the Google Cloud
Service Account to impersonate.

The CLI will use your local Application Default Credentials (ADC) to generate an
OIDC ID token for the specified service account and audience. This token will
then be used to authenticate with the MCP server.

Setup instructionsSection titled “Setup instructions”

Create or use an
existing OAuth 2.0 client ID. To use an existing OAuth 2.0 client ID,
follow the steps in
How to share OAuth Clients.

Add the OAuth ID to the allowlist for
programmatic access
for the application. Since Cloud Run is not yet a supported resource type
in gcloud iap, you must allowlist the Client ID on the project.

Create a service account.
Documentation,
Cloud Console Link

Add both the service account and users to the IAP Policy in the
“Security” tab of the Cloud Run service itself or via gcloud.

Grant all users and groups who will access the MCP Server the necessary
permissions to
impersonate the service account
(for example, roles/iam.serviceAccountTokenCreator).

Enable
the IAM Credentials API for your project.

Example configurationsSection titled “Example configurations”

Python MCP server (stdio)Section titled “Python MCP server (stdio)”

{  "mcpServers": {    "pythonTools": {      "command": "python",      "args": ["-m", "my_mcp_server", "--port", "8080"],      "cwd": "./mcp-servers/python",      "env": {        "DATABASE_URL": "$DB_CONNECTION_STRING",        "API_KEY": "${EXTERNAL_API_KEY}"      },      "timeout": 15000    }  }}

Node.js MCP server (stdio)Section titled “Node.js MCP server (stdio)”

{  "mcpServers": {    "nodeServer": {      "command": "node",      "args": ["dist/server.js", "--verbose"],      "cwd": "./mcp-servers/node",      "trust": true    }  }}

Docker-based MCP serverSection titled “Docker-based MCP server”

{  "mcpServers": {    "dockerizedServer": {      "command": "docker",      "args": [        "run",        "-i",        "--rm",        "-e",        "API_KEY",        "-v",        "${PWD}:/workspace",        "my-mcp-server:latest"      ],      "env": {        "API_KEY": "$EXTERNAL_SERVICE_TOKEN"      }    }  }}

HTTP-based MCP serverSection titled “HTTP-based MCP server”

{  "mcpServers": {    "httpServer": {      "httpUrl": "http://localhost:3000/mcp",      "timeout": 5000    }  }}

HTTP-based MCP Server with custom headersSection titled “HTTP-based MCP Server with custom headers”

{  "mcpServers": {    "httpServerWithAuth": {      "httpUrl": "http://localhost:3000/mcp",      "headers": {        "Authorization": "Bearer your-api-token",        "X-Custom-Header": "custom-value",        "Content-Type": "application/json"      },      "timeout": 5000    }  }}

MCP server with tool filteringSection titled “MCP server with tool filtering”

{  "mcpServers": {    "filteredServer": {      "command": "python",      "args": ["-m", "my_mcp_server"],      "includeTools": ["safe_tool", "file_reader", "data_processor"],      // "excludeTools": ["dangerous_tool", "file_deleter"],      "timeout": 30000    }  }}

SSE MCP server with SA impersonationSection titled “SSE MCP server with SA impersonation”

{  "mcpServers": {    "myIapProtectedServer": {      "url": "https://my-iap-service.run.app/sse",      "authProviderType": "service_account_impersonation",      "targetAudience": "YOUR_IAP_CLIENT_ID.apps.googleusercontent.com",      "targetServiceAccount": "your-sa@your-project.iam.gserviceaccount.com"    }  }}

Discovery process deep diveSection titled “Discovery process deep dive”

When Gemini CLI starts, it performs MCP server discovery through the following
detailed process:

1. Server iteration and connectionSection titled “1. Server iteration and connection”

For each configured server in mcpServers:

Status tracking begins: Server status is set to CONNECTING

Transport selection: Based on configuration properties:

httpUrl → StreamableHTTPClientTransport

url → SSEClientTransport

command → StdioClientTransport

Connection establishment: The MCP client attempts to connect with the
configured timeout

Error handling: Connection failures are logged and the server status is
set to DISCONNECTED

2. Tool discoverySection titled “2. Tool discovery”

Upon successful connection:

Tool listing: The client calls the MCP server’s tool listing endpoint

Schema validation: Each tool’s function declaration is validated

Tool filtering: Tools are filtered based on includeTools and
excludeTools configuration

Name sanitization: Tool names are cleaned to meet Gemini API
requirements:

Characters other than letters, numbers, underscore (_), hyphen (-), dot
(.), and colon (:) are replaced with underscores

Names longer than 63 characters are truncated with middle replacement
(...)

3. Tool naming and namespacesSection titled “3. Tool naming and namespaces”

To prevent collisions across multiple servers or conflicting built-in tools,
every discovered MCP tool is assigned a strict namespace.

Automatic FQN: All MCP tools are unconditionally assigned a fully
qualified name (FQN) using the format mcp_{serverName}_{toolName}.

Registry tracking: The tool registry maintains metadata mappings between
these FQNs and their original server identities.

Overwrites: If two servers share the exact same alias in your
configuration and provide tools with the exact same name, the last registered
tool overwrites the previous one.

Policies: To configure permissions (like auto-approval or denial) for MCP
tools, see
Special syntax for MCP tools
in the Policy Engine documentation.

Caution
Do not use underscores (_) in your MCP server names (for example, use
my-server rather than my_server). The policy parser splits Fully Qualified
Names (mcp_server_tool) on the first underscore following the mcp_
prefix. If your server name contains an underscore, the parser will
misinterpret the server identity, which can cause wildcard rules and security
policies to fail silently.

4. Schema processingSection titled “4. Schema processing”

Tool parameter schemas undergo sanitization for Gemini API compatibility:

$schema properties are removed

additionalProperties are stripped

anyOf with default have their default values removed (Vertex AI
compatibility)

Recursive processing applies to nested schemas

5. Connection managementSection titled “5. Connection management”

After discovery:

Persistent connections: Servers that successfully register tools maintain
their connections

Cleanup: Servers that provide no usable tools have their connections
closed

Status updates: Final server statuses are set to CONNECTED or
DISCONNECTED

Tool execution flowSection titled “Tool execution flow”

When the Gemini model decides to use an MCP tool, the following execution flow
occurs:

1. Tool invocationSection titled “1. Tool invocation”

The model generates a FunctionCall with:

Tool name: The registered name (potentially prefixed)

Arguments: JSON object matching the tool’s parameter schema

2. Confirmation processSection titled “2. Confirmation process”

Each DiscoveredMCPTool implements sophisticated confirmation logic:

Trust-based bypassSection titled “Trust-based bypass”

if (this.trust) {  return false; // No confirmation needed}

Dynamic allow-listingSection titled “Dynamic allow-listing”

The system maintains internal allow-lists for:

Server-level: serverName → All tools from this server are trusted

Tool-level: serverName.toolName → This specific tool is trusted

User choice handlingSection titled “User choice handling”

When confirmation is required, users can choose:

Proceed once: Execute this time only

Always allow this tool: Add to tool-level allow-list

Always allow this server: Add to server-level allow-list

Cancel: Abort execution

3. ExecutionSection titled “3. Execution”

Upon confirmation (or trust bypass):

Parameter preparation: Arguments are validated against the tool’s schema

MCP call: The underlying CallableTool invokes the server with:

const functionCalls = [  {    name: this.serverToolName, // Original server tool name    args: params,  },];

Response processing: Results are formatted for both LLM context and user
display

4. Response handlingSection titled “4. Response handling”

The execution result contains:

llmContent: Raw response parts for the language model’s context

returnDisplay: Formatted output for user display (often JSON in markdown
code blocks)

How to interact with your MCP serverSection titled “How to interact with your MCP server”

Using the /mcp commandSection titled “Using the /mcp command”

The /mcp command provides comprehensive information about your MCP server
setup:
Terminal window
/mcp

This displays:

Server list: All configured MCP servers

Connection status: CONNECTED, CONNECTING, or DISCONNECTED

Server details: Configuration summary (excluding sensitive data)

Available tools: List of tools from each server with descriptions

Discovery state: Overall discovery process status

Example /mcp outputSection titled “Example /mcp output”

MCP Servers Status:
📡 pythonTools (CONNECTED)  Command: python -m my_mcp_server --port 8080  Working Directory: ./mcp-servers/python  Timeout: 15000ms  Tools: calculate_sum, file_analyzer, data_processor
🔌 nodeServer (DISCONNECTED)  Command: node dist/server.js --verbose  Error: Connection refused
🐳 dockerizedServer (CONNECTED)  Command: docker run -i --rm -e API_KEY my-mcp-server:latest  Tools: mcp_dockerizedServer_docker_deploy, mcp_dockerizedServer_docker_status
Discovery State: COMPLETED

Tool usageSection titled “Tool usage”

Once discovered, MCP tools are available to the Gemini model like built-in
tools. The model will automatically:

Select appropriate tools based on your requests

Present confirmation dialogs (unless the server is trusted)

Execute tools with proper parameters

Display results in a user-friendly format

Status monitoring and troubleshootingSection titled “Status monitoring and troubleshooting”

Connection statesSection titled “Connection states”

The MCP integration tracks several states:

Overriding extension configurationsSection titled “Overriding extension configurations”

If an MCP server is provided by an extension (for example, the
google-workspace extension), you can still override its settings in your local
settings.json. Gemini CLI merges your local configuration with the extension’s
defaults:

Tool lists: Tool lists are merged securely to ensure the most restrictive
policy wins:

Exclusions (excludeTools): Arrays are combined (unioned). If either
source blocks a tool, it remains disabled.

Inclusions (includeTools): Arrays are intersected. If both sources
provide an allowlist, only tools present in both lists are enabled. If
only one source provides an allowlist, that list is respected.

Precedence: excludeTools always takes precedence over includeTools.

This ensures you always have veto power over tools provided by an extension
and that an extension cannot re-enable tools you have omitted from your
personal allowlist.

Environment variables: The env objects are merged. If the same variable
is defined in both places, your local value takes precedence.

Scalar properties: Properties like command, url, and timeout are
replaced by your local values if provided.

Example override:

{  "mcpServers": {    "google-workspace": {      "excludeTools": ["gmail.send"]    }  }}

Server status (MCPServerStatus)Section titled “Server status (MCPServerStatus)”

DISCONNECTED: Server is not connected or has errors

CONNECTING: Connection attempt in progress

CONNECTED: Server is connected and ready

Discovery state (MCPDiscoveryState)Section titled “Discovery state (MCPDiscoveryState)”

NOT_STARTED: Discovery hasn’t begun

IN_PROGRESS: Currently discovering servers

COMPLETED: Discovery finished (with or without errors)

Common issues and solutionsSection titled “Common issues and solutions”

Server won’t connectSection titled “Server won’t connect”

Symptoms: Server shows DISCONNECTED status

Troubleshooting:

Check configuration: Verify command, args, and cwd are correct

Test manually: Run the server command directly to ensure it works

Check dependencies: Ensure all required packages are installed

Review logs: Look for error messages in the CLI output

Verify permissions: Ensure the CLI can execute the server command

No tools discoveredSection titled “No tools discovered”

Symptoms: Server connects but no tools are available

Troubleshooting:

Verify tool registration: Ensure your server actually registers tools

Check MCP protocol: Confirm your server implements the MCP tool listing
correctly

Review server logs: Check stderr output for server-side errors

Test tool listing: Manually test your server’s tool discovery endpoint

Tools not executingSection titled “Tools not executing”

Symptoms: Tools are discovered but fail during execution

Troubleshooting:

Parameter validation: Ensure your tool accepts the expected parameters

Schema compatibility: Verify your input schemas are valid JSON Schema

Error handling: Check if your tool is throwing unhandled exceptions

Timeout issues: Consider increasing the timeout setting

Sandbox compatibilitySection titled “Sandbox compatibility”

Symptoms: MCP servers fail when sandboxing is enabled

Solutions:

Docker-based servers: Use Docker containers that include all dependencies

Path accessibility: Ensure server executables are available in the
sandbox

Network access: Configure sandbox to allow necessary network connections

Environment variables: Verify required environment variables are passed
through

Debugging tipsSection titled “Debugging tips”

Enable debug mode: Run the CLI with --debug for verbose output (use F12
to open debug console in interactive mode)

Check stderr: MCP server stderr is captured and logged (INFO messages
filtered)

Test isolation: Test your MCP server independently before integrating

Incremental setup: Start with simple tools before adding complex
functionality

Use /mcp frequently: Monitor server status during development

Important notesSection titled “Important notes”

Security considerationsSection titled “Security considerations”

Trust settings: The trust option bypasses all confirmation dialogs. Use
cautiously and only for servers you completely control

Access tokens: Be security-aware when configuring environment variables
containing API keys or tokens. See
Security and environment sanitization
for details on how Gemini CLI protects your credentials.

Sandbox compatibility: When using sandboxing, ensure MCP servers are
available within the sandbox environment

Private data: Using broadly scoped personal access tokens can lead to
information leakage between repositories.

Performance and resource managementSection titled “Performance and resource management”

Connection persistence: The CLI maintains persistent connections to
servers that successfully register tools

Automatic cleanup: Connections to servers providing no tools are
automatically closed

Timeout management: Configure appropriate timeouts based on your server’s
response characteristics

Resource monitoring: MCP servers run as separate processes and consume
system resources

Schema compatibilitySection titled “Schema compatibility”

Property stripping: The system automatically removes certain schema
properties ($schema, additionalProperties) for Gemini API compatibility

Name sanitization: Tool names are automatically sanitized to meet API
requirements

Conflict resolution: Tool name conflicts between servers are resolved
through automatic prefixing

This comprehensive integration makes MCP servers a powerful way to extend the
Gemini CLI’s capabilities while maintaining security, reliability, and ease of
use.

Returning rich content from toolsSection titled “Returning rich content from tools”

MCP tools are not limited to returning simple text. You can return rich,
multi-part content, including text, images, audio, and other binary data in a
single tool response. This lets you build powerful tools that can provide
diverse information to the model in a single turn.

All data returned from the tool is processed and sent to the model as context
for its next generation, enabling it to reason about or summarize the provided
information.

How it worksSection titled “How it works”

To return rich content, your tool’s response must adhere to the MCP
specification for a
CallToolResult.
The content field of the result should be an array of ContentBlock objects.
Gemini CLI will correctly process this array, separating text from binary data
and packaging it for the model.

You can mix and match different content block types in the content array. The
supported block types include:

text

image

audio

resource (embedded content)

resource_link

Example: Returning text and an imageSection titled “Example: Returning text and an image”

Here is an example of a valid JSON response from an MCP tool that returns both a
text description and an image:

{  "content": [    {      "type": "text",      "text": "Here is the logo you requested."    },    {      "type": "image",      "data": "BASE64_ENCODED_IMAGE_DATA_HERE",      "mimeType": "image/png"    },    {      "type": "text",      "text": "The logo was created in 2025."    }  ]}

When Gemini CLI receives this response, it will:

Extract all the text and combine it into a single functionResponse part
for the model.

Present the image data as a separate inlineData part.

Provide a clean, user-friendly summary in the CLI, indicating that both text
and an image were received.

This enables you to build sophisticated tools that can provide rich, multi-modal
context to the Gemini model.

MCP prompts as slash commandsSection titled “MCP prompts as slash commands”

In addition to tools, MCP servers can expose predefined prompts that can be
executed as slash commands within Gemini CLI. This lets you create shortcuts for
common or complex queries that can be easily invoked by name.

Defining prompts on the serverSection titled “Defining prompts on the server”

Here’s a small example of a stdio MCP server that defines prompts:


<details>
<summary>页面展示组件源码（保留原样）</summary>

~~~jsx
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';import { z } from 'zod';
~~~

</details>
const server = new McpServer({  name: 'prompt-server',  version: '1.0.0',});
server.registerPrompt(  'poem-writer',  {    title: 'Poem Writer',    description: 'Write a nice haiku',    argsSchema: { title: z.string(), mood: z.string().optional() },  },  ({ title, mood }) => ({    messages: [      {        role: 'user',        content: {          type: 'text',          text: `Write a haiku${mood ? ` with the mood ${mood}` : ''} called ${title}. Note that a haiku is 5 syllables followed by 7 syllables followed by 5 syllables `,        },      },    ],  }),);
const transport = new StdioServerTransport();await server.connect(transport);

This can be included in settings.json under mcpServers with:

{  "mcpServers": {    "nodeServer": {      "command": "node",      "args": ["filename.ts"]    }  }}

Invoking promptsSection titled “Invoking prompts”

Once a prompt is discovered, you can invoke it using its name as a slash
command. The CLI will automatically handle parsing arguments.
Terminal window
/poem-writer --title="Gemini CLI" --mood="reverent"

or, using positional arguments:
Terminal window
/poem-writer "Gemini CLI" reverent

When you run this command, Gemini CLI executes the prompts/get method on the
MCP server with the provided arguments. The server is responsible for
substituting the arguments into the prompt template and returning the final
prompt text. The CLI then sends this prompt to the model for execution. This
provides a convenient way to automate and share common workflows.

Managing MCP servers with gemini mcpSection titled “Managing MCP servers with gemini mcp”

While you can always configure MCP servers by manually editing your
settings.json file, Gemini CLI provides a convenient set of commands to manage
your server configurations programmatically. These commands streamline the
process of adding, listing, and removing MCP servers without needing to directly
edit JSON files.

Adding a server (gemini mcp add)Section titled “Adding a server (gemini mcp add)”

The add command configures a new MCP server in your settings.json. Based on
the scope (-s, --scope), it will be added to either the user config
~/.gemini/settings.json or the project config .gemini/settings.json file.

Command:
Terminal window
gemini mcp add [options] <name> <commandOrUrl> [args...]

<name>: A unique name for the server.

<commandOrUrl>: The command to execute (for stdio) or the URL (for
http/sse).

[args...]: <https://geminicli.com/docs/tools/mcp-server/Optional> arguments for a stdio command.

Options (flags):

-s, --scope: Configuration scope (user or project). [default: “project”]

-t, --transport: Transport type (stdio, sse, http). [default: “stdio”]

-e, --env: Set environment variables (for example, -e KEY=value).

-H, --header: Set HTTP headers for SSE and HTTP transports (for example,
-H "X-Api-Key: abc123" -H "Authorization: Bearer abc123").

--timeout: Set connection timeout in milliseconds.

--trust: Trust the server (bypass all tool call confirmation prompts).

--description: Set the description for the server.

--include-tools: A comma-separated list of tools to include.

--exclude-tools: A comma-separated list of tools to exclude.

Adding an stdio serverSection titled “Adding an stdio server”

This is the default transport for running local servers.
Terminal window
# Basic syntaxgemini mcp add [options] <name> <command> [args...]
# Example: Adding a local servergemini mcp add -e API_KEY=123 -e DEBUG=true my-stdio-server /path/to/server arg1 arg2 arg3
# Example: Adding a local python servergemini mcp add python-server python server.py -- --server-arg my-value

Adding an HTTP serverSection titled “Adding an HTTP server”

This transport is for servers that use the streamable HTTP transport.
Terminal window
# Basic syntaxgemini mcp add --transport http <name> <url>
# Example: Adding an HTTP servergemini mcp add --transport http http-server https://api.example.com/mcp/
# Example: Adding an HTTP server with an authentication headergemini mcp add --transport http --header "Authorization: Bearer abc123" secure-http https://api.example.com/mcp/

Adding an SSE serverSection titled “Adding an SSE server”

This transport is for servers that use Server-Sent Events (SSE).
Terminal window
# Basic syntaxgemini mcp add --transport sse <name> <url>
# Example: Adding an SSE servergemini mcp add --transport sse sse-server https://api.example.com/sse/
# Example: Adding an SSE server with an authentication headergemini mcp add --transport sse --header "Authorization: Bearer abc123" secure-sse https://api.example.com/sse/

Listing servers (gemini mcp list)Section titled “Listing servers (gemini mcp list)”

To view all MCP servers currently configured, use the list command. It
displays each server’s name, configuration details, and connection status. This
command has no flags.

Command:
Terminal window
gemini mcp list

Note
For security, stdio MCP servers (those using the
command property) are only tested and displayed as “Connected” if the
current folder is trusted. If the folder is untrusted, they will show as
“Disconnected”. Use gemini trust to trust the current folder.

Example output:
Terminal window
✓ stdio-server: command: python3 server.py (stdio) - Connected✓ http-server: https://api.example.com/mcp (http) - Connected✗ sse-server: https://api.example.com/sse (sse) - Disconnected

Troubleshooting and DiagnosticsSection titled “Troubleshooting and Diagnostics”

To minimize noise during startup, MCP connection errors for background servers
are “silent by default.” If issues are detected during startup, a single
informational hint will be shown: “MCP issues detected. Run /mcp list for
status.”

Detailed, actionable diagnostics for a specific server are automatically
re-enabled when:

You run an interactive command like /mcp list, /mcp auth, etc.

The model attempts to execute a tool from that server.

You invoke an MCP prompt from that server.

You can also use gemini mcp list from your shell to see connection errors for
all configured servers.

Removing a server (gemini mcp remove)Section titled “Removing a server (gemini mcp remove)”

To delete a server from your configuration, use the remove command with the
server’s name.

Command:
Terminal window
gemini mcp remove <name>

Options (flags):

-s, --scope: Configuration scope (user or project). [default: “project”]

Example:
Terminal window
gemini mcp remove my-server

This will find and delete the “my-server” entry from the mcpServers object in
the appropriate settings.json file based on the scope (-s, --scope).

Enabling/disabling a server (gemini mcp enable, gemini mcp disable)Section titled “Enabling/disabling a server (gemini mcp enable, gemini mcp disable)”

Temporarily disable an MCP server without removing its configuration, or
re-enable a previously disabled server.

Commands:
Terminal window
gemini mcp enable <name> [--session]gemini mcp disable <name> [--session]

Options (flags):

--session: Apply change only for this session (not persisted to file).

Disabled servers appear in /mcp status as “Disabled” but won’t connect or
provide tools. Enablement state is stored in
~/.gemini/mcp-server-enablement.json.

The same commands are available as slash commands during an active session:
/mcp enable <name> and /mcp disable <name>.

InstructionsSection titled “Instructions”

Gemini CLI supports
MCP server instructions,
which will be appended to the system instructions.   Last updated: Oct 6, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy


</details>
