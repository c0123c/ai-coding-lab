# Antigravity Agent 设置的归档副本

说明权限预设、自动执行、工作区外访问及不同平台设置；来自已有归档，阅读时以该副本内容为准。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/agent-settings/>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://www.antigravity.google/docs/agent-settings>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/Antigravity Agent 设置的归档副本.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

Markdown 
 复制 Markdown 查看 Markdown 
 可用于：
 反重力 2.0

 
 
Agent 设置 标题为“Agent 设置”的部分

 
 
macOS 和 Linux 标题为“macOS 和 Linux”的部分
 
权限设置 标题为“权限设置”的部分 
 此设置控制如何使用权限预设批准 agent 操作（终端命令、文件访问、MCP 工具和网页读取）： 
 默认：命令在终端沙箱内运行而不提示；在沙箱外运行需要批准。 agent 可以读取和写入工作区和临时目录，并且需要批准才能执行其他操作。 
 请求审核：沙箱已关闭，每个终端命令都需要批准。 agent 可以读取和写入工作区，并且需要批准才能执行其他操作。 
 Turbo ：所有命令都在没有提示的情况下运行，没有隔离或限制，并且 agent 对您的文件系统具有完全的读写访问权限。 
 您可以在 Settings > General > Permission Settings 下配置预设，并在 Settings > Projects 下按项目覆盖它。项目默认为 Inherit General ，它遵循您的全局预设。 
 您配置的允许、拒绝和询问权限规则位于预设之上，并且始终优先。要了解更多信息，请参阅Agent权限。
 
Windows 部分标题为“Windows”
 
终端命令自动执行 标题为“终端命令自动执行”的部分 
 此设置控制 agent 如何执行生成的 shell 命令： 
 请求审核：agent 绝不会在没有提示的情况下执行终端命令（明确添加到可配置允许列表中的命令除外）。 
 在沙箱中继续：命令在终端沙箱内运行而不提示；需要在其外部运行的命令仍然需要审查。 
 始终继续：agent 执行命令而不提示（明确添加到可配置拒绝列表中的命令除外）。
 
Agent 非工作区文件访问 标题为“Agent 非工作区文件访问”的部分 
 此设置允许 agent 查看和编辑活动项目文件夹之外的文件： 
 默认情况下，agent 只能访问项目和应用程序的本地应用程序数据目录 ~/.gemini/antigravity/（其中包含工件、知识项等）。 
 强制实施此边界可以保护您的本地敏感数据。谨慎启用非工作空间访问。
 
终端沙箱模式 标题为“终端沙箱模式”的部分 
 此设置将 agent 终端命令限制到隔离的沙箱： 
 启用后，shell 命令在沙箱内执行，而无需访问敏感系统路径或未经授权的网络。 
 您可以在应用程序首选项中全局配置此设置或按项目覆盖它。 
 要了解更多信息，请参阅终端沙箱指南。


## 原文对照

<details>
<summary>展开完整原文</summary>

Markdown 
 Copy Markdown View Markdown 
 Available on: 
 Antigravity 2.0 

 
 
 Agent settings Section titled “Agent settings” 

 
 
 macOS and Linux Section titled “macOS and Linux” 
 
 Permission settings Section titled “Permission settings” 
 This setting controls how agent actions—terminal commands, file access, MCP tools, and web page reads—are approved using a permission preset : 
 Default : Commands run without prompting inside the Terminal sandbox ; running outside the sandbox requires approval. The agent can read and write the workspace and temp directories, and needs approval for anything else. 
 Request Review : The sandbox is off and every terminal command requires approval. The agent can read and write the workspace, and needs approval for anything else. 
 Turbo : All commands run without prompting with no isolation or restrictions, and the agent has full read and write access to your filesystem. 
 You can configure the preset under Settings > General > Permission Settings , and override it per project under Settings > Projects . Projects default to Inherit General , which follows your global preset. 
 Your configured allow, deny, and ask permission rules are layered on top of the preset and always take precedence. To learn more, refer to Agent permissions . 
 
 Windows Section titled “Windows” 
 
 Terminal command auto execution Section titled “Terminal command auto execution” 
 This setting controls how the agent executes generated shell commands: 
 Request Review : The agent never executes terminal commands without prompting (except those explicitly added to your configurable Allow list). 
 Proceed in Sandbox : Commands run without prompting inside the Terminal sandbox ; commands that need to run outside it still require review. 
 Always Proceed : The agent executes commands without prompting (except those explicitly added to your configurable Deny list). 
 
 Agent non-workspace file access Section titled “Agent non-workspace file access” 
 This setting allows the agent to view and edit files outside of the active project folders: 
 By default, the agent only has access to the folders inside your project and the application’s local app data directory ~/.gemini/antigravity/ (which contains artifacts, knowledge items, and more). 
 Enforcing this boundary protects your local sensitive data. Enable non-workspace access with caution. 
 
 Terminal sandbox mode Section titled “Terminal sandbox mode” 
 This setting restricts agent terminal commands to an isolated sandbox: 
 When enabled, shell commands execute inside the sandbox without access to sensitive system paths or unauthorized networks. 
 You can configure this setting globally in application preferences or override it per project. 
 To learn more, refer to the Terminal sandbox guide.


</details>
