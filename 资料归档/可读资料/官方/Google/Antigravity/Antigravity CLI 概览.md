# Antigravity CLI 概览

介绍终端界面的能力、适用工作方式、与桌面产品共享的配置及会话迁移，并说明从 Gemini CLI 导入的路径。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/cli/overview/>)）；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://www.antigravity.google/docs/cli/overview/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/Antigravity CLI 概览.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

Markdown 复制 Markdown 查看 Markdown 反重力 CLI 概述 标题为“反重力 CLI 概述”的部分 反重力 CLI 是反重力的轻量级终端用户界面 (TUI) 表面。它将与 Antigravity 2.0 相同的核心代理功能（例如多步推理、多文件编辑、工具调用和对话历史记录）直接带到您的终端。为什么是反重力CLI？标题为“为什么要反重力 CLI？”的部分Antigravity CLI 将共享 agent 工具的推理、执行和编排功能直接引入到您的本地 shell 中。虽然 Antigravity 2.0 提供了全面的可视化编辑器界面，但 CLI 是为速度、轻量级操作以及与终端优先工作流程的无缝集成而定制的。平台比较 标题为“平台比较”的部分 功能 反重力 CLI 反重力 2.0 主界面 键盘驱动 TUI 可视化桌面编辑器 / IDE 性能开销 接近于零，极其轻量 标准桌面 IDE 占用空间 工作流程重点 快速本地迭代， SSH，无头 完整的项目管理、可视化工作区导航 通用键盘快捷键 鼠标和多面板布局 远程可用性 本机 SSH、tmux 和终端多路复用器 本地工作区或远程开发容器 集成功能 标题为“集成功能”的部分 反重力 CLI 与反重力 2.0 协同工作，共享配置并实现无摩擦接口之间的转换：共享 agent 线束：两个环境都在同一 agent 核心上运行。对多步推理、工具使用或代码理解的任何增强都适用于这两个平台。共享设置同步：您的核心首选项、权限和安全配置在两个界面上自动同步。在一个平台中更新权限规则或标准配置会立即更新另一个平台。对话导出：在平台之间无缝移动活动对话。如果终端会话变得越来越复杂并且需要可视化编排，请将对话导出到 Antigravity 2.0 以继续使用可视化编辑器界面。从 Gemini CLI 迁移 标题为“从 Gemini CLI 迁移”的部分如果您要从 Gemini CLI 迁移，入职流程支持一次性导入，以自动迁移现有的 Gemini CLI 扩展、技能和设置。要了解更多信息，请参阅从 Gemini CLI 迁移。后续步骤 标题为“后续步骤”的部分浏览以下指南来设置您的环境并开始使用自主 agents： 安装和身份验证：设置 CLI、配置企业参数并完成静默身份验证。入门：探索入门路线图、首次发布设置和核心概念模型。教程：使用活动的 agent 运行您的第一个多文件生成任务。提示和交互：掌握多行排版、提示编辑和粘贴终端媒体。审查工件：审查 agent 计划、差异和测试运行。 AI 积分：配置和监控 AI 高级积分回退、定价链接和设置。插件和技能：创建您自己的自定义技能斜线命令、管理挂钩并配置 MCP 服务器。最佳实践：掌握工作流程管道、验证循环和会话过程修正。


## 原文对照

<details>
<summary>展开完整原文</summary>

Markdown Copy Markdown View Markdown Antigravity CLI overview Section titled “Antigravity CLI overview” The Antigravity CLI is the lightweight Terminal User Interface (TUI) surface of Antigravity. It brings the same core agentic capabilities as Antigravity 2.0 (such as multi-step reasoning, multi-file editing, tool calling, and conversation history) directly to your terminal. Why Antigravity CLI? Section titled “Why Antigravity CLI?” Antigravity CLI brings the reasoning, execution, and orchestration capabilities of the shared agent harness directly into your local shell. While Antigravity 2.0 offers a comprehensive visual editor interface, the CLI is custom-built for speed, lightweight operation, and seamless integration with terminal-first workflows. Platform comparison Section titled “Platform comparison” Feature Antigravity CLI Antigravity 2.0 Primary interface Keyboard-driven TUI Visual desktop editor / IDE Performance overhead Near-zero, extremely lightweight Standard desktop IDE footprint Workflow focus Fast local iterations, SSH, headless Complete project management, visual workspace Navigation Universal keyboard shortcuts Mouse and multi-panel layout Remote usability Native SSH, tmux, and terminal multiplexers Local workspace or remote development containers Integration features Section titled “Integration features” Antigravity CLI operates in tandem with Antigravity 2.0, sharing configurations and enabling frictionless transitions between interfaces: Shared agent harness : Both environments run on the same agent core. Any enhancements to multi-step reasoning, tool usage, or code comprehension apply across both platforms. Shared settings sync : Your core preferences, permissions, and security configurations synchronize automatically across both interfaces. Updating a permission rule or standard configuration in one platform immediately updates the other. Conversation export : Seamlessly move active conversations between platforms. If a terminal session grows in complexity and requires visual orchestration, export the conversation to Antigravity 2.0 to continue with the visual editor interface. Migrating from Gemini CLI Section titled “Migrating from Gemini CLI” If you’re transitioning from Gemini CLI, the onboarding process supports a one-time import to automatically migrate your existing Gemini CLI extensions, skills, and settings. To learn more, refer to Migrating from Gemini CLI . Next steps Section titled “Next steps” Explore the following guides to set up your environment and begin working with autonomous agents: Installation and auth : Set up the CLI, configure enterprise parameters, and complete silent authentication. Getting started : Explore the onboarding roadmap, first-launch setups, and core conceptual models. Tutorial : Run your first multi-file generation task with an active agent. Prompting and interaction : Master multiline composing, prompt editing, and pasting terminal media. Reviewing artifacts : Review agent plans, diffs, and test runs. AI credits : Configure and monitor AI Premium credits fallback, pricing links, and settings. Plugins and skills : Create your own custom skills slash commands, manage hooks, and configure MCP servers. Best practices : Master workflow pipelines, verification loops, and session course-corrections.


</details>
