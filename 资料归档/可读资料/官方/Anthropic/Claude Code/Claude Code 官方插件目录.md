# Claude Code 官方插件目录

介绍仓库中的插件、安装、目录结构和贡献方式，说明如何通过命令、Agent 与工作流扩展能力。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-25](<https://github.com/anthropics/claude-code/commit/4ae2cb4e5eabca5bcb457b71e500833f737a782b>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/plugins/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Claude Code 插件

该目录包含一些官方 Claude Code 插件，可通过自定义命令、agents 和工作流程扩展功能。这些是 Claude Code 插件系统可能实现的功能示例——社区市场还提供更多插件。

## Claude Code 插件是什么？

Claude Code 插件是通过自定义斜杠命令、专用 agents、挂钩和 MCP 服务器增强 Claude Code 的扩展。插件可以在项目和团队之间共享，提供一致的工具和工作流程。

请参阅 [ 官方插件文档 ](<https://docs.claude.com/en/docs/claude-code/plugins>) 了解更多信息。

## 该目录中的插件

|名称|描述|内容|
|------|-------------|----------|
|[agent-sdk-dev](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/agent-sdk-dev>)|用于与 Claude Agent SDK 配合使用的开发套件|**命令：** `/new-sdk-app` - 新的 Agent SDK 项目的交互式设置<br>**Agents:** `agent-sdk-verifier-py`, `agent-sdk-verifier-ts` - 根据最佳实践验证 SDK 应用程序|
|[克劳德作品-4-5-迁移](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/claude-opus-4-5-migration>)|将代码和提示从 Sonnet 4.x 和 Opus 4.1 迁移到 Opus 4.5|**技能：** `claude-opus-4-5-migration` - 自动迁移模型字符串、测试标头和提示调整|
|[代码审查](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/code-review>)|使用多个专门的 agents 进行自动 PR 代码审查，并通过基于置信度的评分来过滤误报|**命令：** `/code-review` - 自动 PR 审核工作流程 <br>**Agents：** 5 并行十四行诗 agents CLAUDE.md 合规性、错误检测、历史上下文、PR 历史记录和代码注释|
|[提交命令](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/commit-commands>)|用于提交、推送和创建拉取请求的 Git 工作流程自动化|**命令：** `/commit`、`/commit-push-pr`、`/clean_gone` - 简化的 git 操作|
|[解释输出样式](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/explanatory-output-style>)|添加有关实施选择和代码库模式的教育见解（模仿已弃用的解释性输出样式）|**Hook:** SessionStart - 在每个会话开始时注入教育背景|
|[功能开发](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/feature-dev>)|采用结构化 7 阶段方法的综合功能开发工作流程|**命令：** `/feature-dev` - 引导功能开发工作流程<br>**Agents：** `code-explorer`、`code-architect`、`code-reviewer` - 用于代码库分析、架构设计和质量审查|
|[前端设计](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/frontend-design>)|创建独特的生产级前端界面，避免通用 AI 美学|**技能：** `frontend-design` - 自动调用前端工作，提供有关大胆设计选择、排版、动画和视觉细节的指导|
|[hookify](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/hookify>)|通过分析对话模式或显式指令，轻松创建自定义挂钩以防止不良行为|**命令：** `/hookify`、`/hookify:list`、`/hookify:configure`、`/hookify:help`<br>**Agent：** `conversation-analyzer` - 分析有问题的对话行为<br>**技能：** `writing-rules` - hookify 规则语法指南|
|[学习输出式](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/learning-output-style>)|交互式学习模式，在决策点请求有意义的代码贡献（模仿未交付的学习输出风格）|**Hook:** SessionStart - 鼓励用户在决策点编写有意义的代码（5-10 行），同时获得教育见解|
|[插件开发](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/plugin-dev>)|用于利用 7 专业技能和 AI- 辅助创建来开发 Claude Code 插件的综合工具包|**命令：** `/plugin-dev:create-plugin` - 8构建插件的阶段指导工作流程<br>**Agents：** `agent-creator`，`plugin-validator`， `skill-reviewer`<br>**技能：** Hook开发，MCP集成，插件结构，设置，命令，agents和技能开发|
|[预审工具包](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/pr-review-toolkit>)|全面的 PR 审查 agents 专门从事注释、测试、错误处理、类型设计、代码质量和代码简化|**命令：** `/pr-review-toolkit:review-pr` - 使用可选的审核方面运行（注释、测试、错误、类型、代码、简化、全部）<br>**Agents：** `comment-analyzer`、`pr-test-analyzer`、 `silent-failure-hunter`、`type-design-analyzer`、`code-reviewer`、`code-simplifier`|
|[拉尔夫-维古姆](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/ralph-wiggum>)|用于迭代开发的交互式自引用 AI 循环。 Claude 重复执行同一任务直至完成|**命令：** `/ralph-loop`、`/cancel-ralph` - Start/stop自主迭代循环<br>**挂钩：**停止 - 拦截继续迭代的退出尝试|
|[安全指导](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/security-guidance>)|安全提醒挂钩，在编辑文件时警告潜在的安全问题|**Hook:** PreToolUse - 监视 9 安全模式，包括命令注入、XSS、评估使用、危险 HTML、pickle 反序列化和 os.system 调用|

## 安装

这些插件包含在 Claude Code 存储库中。要在您自己的项目中使用它们：

1. 全局安装Claude Code：
```bash
npm install -g @anthropic-ai/claude-code
```

2. 导航到您的项目并运行 Claude Code：
```bash
claude
```

3. 使用 `/plugin` 命令从市场安装插件，或在项目的 `.claude/settings.json` 中配置它们。

详细的插件安装和配置请参见[官方文档](<https://docs.claude.com/en/docs/claude-code/plugins>)。

## 插件结构

每个插件都遵循标准的 Claude Code 插件结构：

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── commands/                # Slash commands (optional)
├── agents/                  # Specialized agents (optional)
├── skills/                  # Agent Skills (optional)
├── hooks/                   # Event handlers (optional)
├── .mcp.json                # External tool configuration (optional)
└── README.md                # Plugin documentation
```

## 贡献

将新插件添加到此目录时：

1. 遵循标准插件结构
2. 包括一个全面的README.md
3. 在 `.claude-plugin/plugin.json` 中添加插件元数据
4. 记录所有命令和 agents
5. 提供使用示例

## 了解更多

- [Claude Code 文档](<https://docs.claude.com/en/docs/claude-code/overview>)
- [插件系统文档](<https://docs.claude.com/en/docs/claude-code/plugins>)
- [Agent SDK 文档](<https://docs.claude.com/en/api/agent-sdk/overview>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Claude Code Plugins

This directory contains some official Claude Code plugins that extend functionality through custom commands, agents, and workflows. These are examples of what's possible with the Claude Code plugin system—many more plugins are available through community marketplaces.

## What are Claude Code Plugins?

Claude Code plugins are extensions that enhance Claude Code with custom slash commands, specialized agents, hooks, and MCP servers. Plugins can be shared across projects and teams, providing consistent tooling and workflows.

Learn more in the [official plugins documentation](<https://docs.claude.com/en/docs/claude-code/plugins>).

## Plugins in This Directory

| Name | Description | Contents |
|------|-------------|----------|
| [agent-sdk-dev](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/agent-sdk-dev>) | Development kit for working with the Claude Agent SDK | **Command:** `/new-sdk-app` - Interactive setup for new Agent SDK projects<br>**Agents:** `agent-sdk-verifier-py`, `agent-sdk-verifier-ts` - Validate SDK applications against best practices |
| [claude-opus-4-5-migration](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/claude-opus-4-5-migration>) | Migrate code and prompts from Sonnet 4.x and Opus 4.1 to Opus 4.5 | **Skill:** `claude-opus-4-5-migration` - Automated migration of model strings, beta headers, and prompt adjustments |
| [code-review](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/code-review>) | Automated PR code review using multiple specialized agents with confidence-based scoring to filter false positives | **Command:** `/code-review` - Automated PR review workflow<br>**Agents:** 5 parallel Sonnet agents for CLAUDE.md compliance, bug detection, historical context, PR history, and code comments |
| [commit-commands](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/commit-commands>) | Git workflow automation for committing, pushing, and creating pull requests | **Commands:** `/commit`, `/commit-push-pr`, `/clean_gone` - Streamlined git operations |
| [explanatory-output-style](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/explanatory-output-style>) | Adds educational insights about implementation choices and codebase patterns (mimics the deprecated Explanatory output style) | **Hook:** SessionStart - Injects educational context at the start of each session |
| [feature-dev](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/feature-dev>) | Comprehensive feature development workflow with a structured 7-phase approach | **Command:** `/feature-dev` - Guided feature development workflow<br>**Agents:** `code-explorer`, `code-architect`, `code-reviewer` - For codebase analysis, architecture design, and quality review |
| [frontend-design](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/frontend-design>) | Create distinctive, production-grade frontend interfaces that avoid generic AI aesthetics | **Skill:** `frontend-design` - Auto-invoked for frontend work, providing guidance on bold design choices, typography, animations, and visual details |
| [hookify](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/hookify>) | Easily create custom hooks to prevent unwanted behaviors by analyzing conversation patterns or explicit instructions | **Commands:** `/hookify`, `/hookify:list`, `/hookify:configure`, `/hookify:help`<br>**Agent:** `conversation-analyzer` - Analyzes conversations for problematic behaviors<br>**Skill:** `writing-rules` - Guidance on hookify rule syntax |
| [learning-output-style](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/learning-output-style>) | Interactive learning mode that requests meaningful code contributions at decision points (mimics the unshipped Learning output style) | **Hook:** SessionStart - Encourages users to write meaningful code (5-10 lines) at decision points while receiving educational insights |
| [plugin-dev](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/plugin-dev>) | Comprehensive toolkit for developing Claude Code plugins with 7 expert skills and AI-assisted creation | **Command:** `/plugin-dev:create-plugin` - 8-phase guided workflow for building plugins<br>**Agents:** `agent-creator`, `plugin-validator`, `skill-reviewer`<br>**Skills:** Hook development, MCP integration, plugin structure, settings, commands, agents, and skill development |
| [pr-review-toolkit](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/pr-review-toolkit>) | Comprehensive PR review agents specializing in comments, tests, error handling, type design, code quality, and code simplification | **Command:** `/pr-review-toolkit:review-pr` - Run with optional review aspects (comments, tests, errors, types, code, simplify, all)<br>**Agents:** `comment-analyzer`, `pr-test-analyzer`, `silent-failure-hunter`, `type-design-analyzer`, `code-reviewer`, `code-simplifier` |
| [ralph-wiggum](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/ralph-wiggum>) | Interactive self-referential AI loops for iterative development. Claude works on the same task repeatedly until completion | **Commands:** `/ralph-loop`, `/cancel-ralph` - Start/stop autonomous iteration loops<br>**Hook:** Stop - Intercepts exit attempts to continue iteration |
| [security-guidance](<https://github.com/anthropics/claude-code/blob/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/security-guidance>) | Security reminder hook that warns about potential security issues when editing files | **Hook:** PreToolUse - Monitors 9 security patterns including command injection, XSS, eval usage, dangerous HTML, pickle deserialization, and os.system calls |

## Installation

These plugins are included in the Claude Code repository. To use them in your own projects:

1. Install Claude Code globally:
```bash
npm install -g @anthropic-ai/claude-code
```

2. Navigate to your project and run Claude Code:
```bash
claude
```

3. Use the `/plugin` command to install plugins from marketplaces, or configure them in your project's `.claude/settings.json`.

For detailed plugin installation and configuration, see the [official documentation](<https://docs.claude.com/en/docs/claude-code/plugins>).

## Plugin Structure

Each plugin follows the standard Claude Code plugin structure:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── commands/                # Slash commands (optional)
├── agents/                  # Specialized agents (optional)
├── skills/                  # Agent Skills (optional)
├── hooks/                   # Event handlers (optional)
├── .mcp.json                # External tool configuration (optional)
└── README.md                # Plugin documentation
```

## Contributing

When adding new plugins to this directory:

1. Follow the standard plugin structure
2. Include a comprehensive README.md
3. Add plugin metadata in `.claude-plugin/plugin.json`
4. Document all commands and agents
5. Provide usage examples

## Learn More

- [Claude Code Documentation](<https://docs.claude.com/en/docs/claude-code/overview>)
- [Plugin System Documentation](<https://docs.claude.com/en/docs/claude-code/plugins>)
- [Agent SDK Documentation](<https://docs.claude.com/en/api/agent-sdk/overview>)


</details>
