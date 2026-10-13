# Gemini CLI 文档入口

概括使用文档、配置、功能和工具的阅读入口，适合定位具体操作指南；属于站点导航。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-04-17](<https://github.com/google-gemini/gemini-cli/commit/220888ac2dfd40733bc51ea6b8550c3a0777673c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/index.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/index.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Gemini CLI 文档

Gemini CLI 将 Gemini 型号的功能直接带入您的终端。使用它
来理解代码、自动执行任务以及使用本地项目
上下文构建工作流程。

## 安装

```bash
npm install -g @google/gemini-cli
```

## 开始使用

跳转到Gemini CLI。

- **[快速入门](<Gemini CLI 入门.md>)：**您与Gemini CLI的第一次会话。
- **[安装](<Gemini CLI 安装、运行与发布渠道.md>):**如何安装Gemini CLI
在您的系统上。
- **[身份验证](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/get-started/authentication.mdx>):**设置说明
个人和企业账户。
- **[CLI 备忘单](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/cli-reference.md>):** 常见问题的快速参考
命令和选项。
- **[Gemini 3 于 Gemini CLI](<在 Gemini CLI 中使用 Gemini 3 系列.md>):** 了解 Gemini 3
支持Gemini CLI。

## 使用Gemini CLI

针对日常开发工作流程的以用户为中心的指南和教程。

- **[文件管理](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/file-management.md>):**如何使用
本地文件和目录。
- **[开始使用Agent skills](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/skills-getting-started.md>)：**
从专业知识开始。
- **[管理上下文和内存](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/memory-management.md>):**
管理持久的指令和事实。
- **[执行shell命令](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/shell-commands.md>):**正在执行
安全地执行系统命令。
- **[管理会话和历史记录](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/session-management.md>):**
恢复、管理和倒带对话。
- **[使用待办事项计划任务](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/task-planning.md>):**使用待办事项
复杂的工作流程。
- **[网页搜索并获取](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/web-tools.md>):**搜索并获取
从网络获取内容。
- **[设置 MCP 服务器](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/mcp-setup.md>):** 设置 MCP
服务器。
- **[自动化任务](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/automation.md>)：**自动化任务。

## 特点

Gemini CLI 各项功能的技术文档。

- **[扩展](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/extensions/index.md>)：**使用新工具扩展Gemini CLI
能力。
- **[Agent Skills](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/skills.md>):** 使用专门的 agents 进行特定
任务。
- **[检查点](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/checkpointing.md>)：**自动会话快照。
- **[无头模式](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/headless.md>)：**编程和脚本接口。
- **[Hooks](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/hooks/index.md>):** 使用脚本自定义 Gemini CLI 行为。
- **[IDE 集成](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/ide-integration/index.md>):** 将 Gemini CLI 与
你最喜欢的IDE。
- **[MCP 服务器](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/mcp-server.md>)：** 连接并使用远程agents。
- **[模型路由](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model-routing.md>)：**自动回退弹性。
- **[型号选择](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model.md>)：**选择最适合您需求的型号。
- **[计划模式 🔬](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/plan-mode.md>):** 使用安全的只读模式
规划复杂的变化。
- **[子代理🔬](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/core/subagents.md>)：**使用专门的agents进行特定
任务。
- **[远程子代理 🔬](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/core/remote-agents.md>):** 连接并使用
远程agents。
- **[快退](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/rewind.md>):**快退并重播会话。
- **[沙箱](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/sandbox.md>)：**隔离工具执行。
- **[设置](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/settings.md>):**完整配置参考。
- **[遥测](<Gemini CLI 的 OpenTelemetry 可观测性.md>)：**使用情况和性能指标详细信息。
- **[令牌缓存](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/token-caching.md>)：**性能优化。

## 配置

Gemini CLI 的设置和自定义选项。

- **[自定义命令](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/custom-commands.md>)：**个性化快捷方式。
- **[企业配置](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/enterprise.md>)：**专业环境
控制。
- **[忽略文件 (.geminiignore)](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/gemini-ignore.md>):** 排除模式
参考。
- **[模型配置](<Gemini CLI 高级模型配置（存档9b6e0265-569）.md>):**微调生成
温度和思维预算等参数。
- **[项目上下文 (GEMINI.md)](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/gemini-md.md>):** 的技术层次结构
上下文文件。
- **[系统提示覆盖](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/system-prompt.md>):**指令替换
逻辑。
- **[主题](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/themes.md>)：** UI个性化技术指南。
- **[受信任文件夹](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/trusted-folders.md>)：**安全权限逻辑。

## 参考

深入的技术文档和 API 规范。

- **[命令参考](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md>):**斜杠命令详细
指导。
- **[配置参考](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/configuration.md>):**设置与
环境变量。
- **[键盘快捷键](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/keyboard-shortcuts.md>)：** 生产力
提示。
- **[内存导入处理器](<GEMINI.md 的记忆导入处理.md>)：**如何Gemini CLI
处理来自各种来源的内存。
- **[策略引擎](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/policy-engine.md>):**细粒度执行
控制。
- **[工具参考](<Gemini CLI 工具参考.md>)：**有关工具使用方式的信息
定义、注册和使用。

## 资源

支持、发布历史记录和法律信息。

- **[FAQ](<Gemini CLI 常见问题排查.md>)：**常见问题解答。
- **[配额和定价](<Gemini CLI 配额与计费说明.md>)：**限制和计费
详细信息。
- **[条款和隐私](<Gemini CLI 许可、条款与隐私说明.md>)：**官方通知和
条款。
- **[故障排除](<Gemini CLI 故障排查指南.md>)：**常见问题和
解决方案。
- **[卸载](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/resources/uninstall.md>):**如何卸载Gemini CLI。

## 发展

- **[贡献指南](<https://github.com/docs/contributing>)：**如何为Gemini CLI做出贡献。
- **[集成测试](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/integration-tests.md>)：**运行集成测试。
- **[问题和PR自动化](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/issue-and-pr-automation.md>)：**自动化
问题和拉取请求。
- **[本地开发](<Gemini CLI 本地开发与链路追踪.md>):**设置本地
开发环境。
- **[NPM 包结构](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/npm.md>):** NPM 包的结构。

## 发布

- **[发行说明](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/changelogs/index.md>)：**所有版本的发行说明。
- **[稳定版本](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/changelogs/latest.md>)：**最新的稳定版本。
- **[预览版](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/changelogs/preview.md>)：**最新预览版。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Gemini CLI documentation

Gemini CLI brings the power of Gemini models directly into your terminal. Use it
to understand code, automate tasks, and build workflows with your local project
context.

## Install

```bash
npm install -g @google/gemini-cli
```

## Get started

Jump in to Gemini CLI.

- **[Quickstart](<Gemini CLI 入门.md>):** Your first session with Gemini CLI.
- **[Installation](<Gemini CLI 安装、运行与发布渠道.md>):** How to install Gemini CLI
  on your system.
- **[Authentication](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/get-started/authentication.mdx>):** Setup instructions for
  personal and enterprise accounts.
- **[CLI cheatsheet](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/cli-reference.md>):** A quick reference for common
  commands and options.
- **[Gemini 3 on Gemini CLI](<在 Gemini CLI 中使用 Gemini 3 系列.md>):** Learn about Gemini 3
  support in Gemini CLI.

## Use Gemini CLI

User-focused guides and tutorials for daily development workflows.

- **[File management](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/file-management.md>):** How to work with
  local files and directories.
- **[Get started with Agent skills](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/skills-getting-started.md>):**
  Getting started with specialized expertise.
- **[Manage context and memory](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/memory-management.md>):**
  Managing persistent instructions and facts.
- **[Execute shell commands](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/shell-commands.md>):** Executing
  system commands safely.
- **[Manage sessions and history](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/session-management.md>):**
  Resuming, managing, and rewinding conversations.
- **[Plan tasks with todos](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/task-planning.md>):** Using todos for
  complex workflows.
- **[Web search and fetch](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/web-tools.md>):** Searching and
  fetching content from the web.
- **[Set up an MCP server](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/mcp-setup.md>):** Set up an MCP
  server.
- **[Automate tasks](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/automation.md>):** Automate tasks.

## Features

Technical documentation for each capability of Gemini CLI.

- **[Extensions](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/extensions/index.md>):** Extend Gemini CLI with new tools and
  capabilities.
- **[Agent Skills](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/skills.md>):** Use specialized agents for specific
  tasks.
- **[Checkpointing](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/checkpointing.md>):** Automatic session snapshots.
- **[Headless mode](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/headless.md>):** Programmatic and scripting interface.
- **[Hooks](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/hooks/index.md>):** Customize Gemini CLI behavior with scripts.
- **[IDE integration](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/ide-integration/index.md>):** Integrate Gemini CLI with
  your favorite IDE.
- **[MCP servers](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/mcp-server.md>):** Connect to and use remote agents.
- **[Model routing](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model-routing.md>):** Automatic fallback resilience.
- **[Model selection](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model.md>):** Choose the best model for your needs.
- **[Plan mode 🔬](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/plan-mode.md>):** Use a safe, read-only mode for
  planning complex changes.
- **[Subagents 🔬](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/core/subagents.md>):** Using specialized agents for specific
  tasks.
- **[Remote subagents 🔬](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/core/remote-agents.md>):** Connecting to and using
  remote agents.
- **[Rewind](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/rewind.md>):** Rewind and replay sessions.
- **[Sandboxing](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/sandbox.md>):** Isolate tool execution.
- **[Settings](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/settings.md>):** Full configuration reference.
- **[Telemetry](<Gemini CLI 的 OpenTelemetry 可观测性.md>):** Usage and performance metric details.
- **[Token caching](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/token-caching.md>):** Performance optimization.

## Configuration

Settings and customization options for Gemini CLI.

- **[Custom commands](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/custom-commands.md>):** Personalized shortcuts.
- **[Enterprise configuration](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/enterprise.md>):** Professional environment
  controls.
- **[Ignore files (.geminiignore)](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/gemini-ignore.md>):** Exclusion pattern
  reference.
- **[Model configuration](<Gemini CLI 高级模型配置（存档9b6e0265-569）.md>):** Fine-tune generation
  parameters like temperature and thinking budget.
- **[Project context (GEMINI.md)](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/gemini-md.md>):** Technical hierarchy of
  context files.
- **[System prompt override](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/system-prompt.md>):** Instruction replacement
  logic.
- **[Themes](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/themes.md>):** UI personalization technical guide.
- **[Trusted folders](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/trusted-folders.md>):** Security permission logic.

## Reference

Deep technical documentation and API specifications.

- **[Command reference](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md>):** Detailed slash command
  guide.
- **[Configuration reference](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/configuration.md>):** Settings and
  environment variables.
- **[Keyboard shortcuts](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/keyboard-shortcuts.md>):** Productivity
  tips.
- **[Memory import processor](<GEMINI.md 的记忆导入处理.md>):** How Gemini CLI
  processes memory from various sources.
- **[Policy engine](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/policy-engine.md>):** Fine-grained execution
  control.
- **[Tools reference](<Gemini CLI 工具参考.md>):** Information on how tools are
  defined, registered, and used.

## Resources

Support, release history, and legal information.

- **[FAQ](<Gemini CLI 常见问题排查.md>):** Answers to frequently asked questions.
- **[Quota and pricing](<Gemini CLI 配额与计费说明.md>):** Limits and billing
  details.
- **[Terms and privacy](<Gemini CLI 许可、条款与隐私说明.md>):** Official notices and
  terms.
- **[Troubleshooting](<Gemini CLI 故障排查指南.md>):** Common issues and
  solutions.
- **[Uninstall](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/resources/uninstall.md>):** How to uninstall Gemini CLI.

## Development

- **[Contribution guide](<https://github.com/docs/contributing>):** How to contribute to Gemini CLI.
- **[Integration testing](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/integration-tests.md>):** Running integration tests.
- **[Issue and PR automation](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/issue-and-pr-automation.md>):** Automation for
  issues and pull requests.
- **[Local development](<Gemini CLI 本地开发与链路追踪.md>):** Setting up a local
  development environment.
- **[NPM package structure](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/npm.md>):** The structure of the NPM packages.

## Releases

- **[Release notes](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/changelogs/index.md>):** Release notes for all versions.
- **[Stable release](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/changelogs/latest.md>):** The latest stable release.
- **[Preview release](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/changelogs/preview.md>):** The latest preview release.


</details>
