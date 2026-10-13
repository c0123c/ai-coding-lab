# Copilot 定制功能速查

对比自定义指令、提示文件、Agent、Skills、Hooks 等配置的用途、作用范围和支持界面，帮助为开发需求选择扩展机制。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/reference/customization-cheat-sheet.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 定制功能速查（存档be38ec5d-494）.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Copilot customization cheat sheet
shortTitle: Customization cheat sheet
intro: 'Compare the different customization options for {% data variables.product.prodname_copilot %}.'
versions:
  feature: copilot
category:
  - Author and optimize with Copilot
contentType: reference
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data variables.product.prodname_copilot %} 提供了多种自定义功能，可让您根据工作流程、团队标准和项目需求定制其行为。使用下表找到适合您的用例的表格。

## 功能概述

此表显示了每个自定义功能的含义及其所在位置。

|特点|它是什么|文件名和位置|
|---------|-----------|-------------------|
|[定制说明](<https://github.com/copilot/concepts/prompting/response-customization>)|始终在线的上下文，自动应用于其定义范围内的每次交互|`.github/copilot-instructions.md`（存储库范围）、`.github/instructions/*.instructions.md`（特定于路径）、`AGENTS.md`（第三方 agents）或通过 {% data variables.product.github %} 上的 UI 的 personal/org 设置|
|[提示文件](<https://github.com/copilot/concepts/prompting/response-customization?tool=vscode#about-prompt-files>)|带有输入变量的可重复使用的独立提示模板| `.github/prompts/*.prompt.md` |
| [{% data variables.copilot.custom_agents_caps_short %}](<https://github.com/copilot/concepts/agents/cloud-agent/about-custom-agents>) |具有自己的说明、工具限制和上下文的专家角色|`.github/agents/AGENT-NAME.md`（存储库）、组织的 `.github` 或 `.github-private` 存储库（组织级）中的 `/agents/AGENT-NAME.md`、指定的 `.github-private` 存储库（企业级）中的 `/agents/AGENT-NAME.md` 或用户配置文件|
| [{% data variables.copilot.subagents_caps_short %}](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>) |由主 agent 生成的独立 agent 用于在隔离上下文中处理委托工作|N/A（运行时进程，不是用户配置文件）|
| [Agent skills](<https://github.com/copilot/concepts/agents/about-agent-skills>) |{% data variables.product.prodname_copilot_short %} 与任务相关时加载的指令、脚本和资源的文件夹|`.github/skills/<skill-name>/SKILL.md`、`.claude/skills/<skill-name>/SKILL.md` 或 `.agents/skills/<skill-name>/SKILL.md`（项目）； `~/.copilot/skills/<skill-name>/SKILL.md` 或 `~/.agents/skills/<skill-name>/SKILL.md`（个人）|
|[挂钩](<https://github.com/copilot/concepts/agents/hooks>)|在 agent 工作流程中的特定点确定性执行的自定义 shell 命令| `.github/hooks/*.json` |
|[MCP 服务器](<https://github.com/copilot/concepts/context/mcp>)|连接到外部系统、API 和数据库|`mcp.json`（路径因 IDE 而异）、{% data variables.product.github %} 上的存储库 MCP 设置（适用于 {% data variables.copilot.copilot_cloud_agent_short %} 和 {% data variables.copilot.copilot_code-review_short %}）或 {% data variables.copilot.copilot_custom_agent_short %} 配置中的 `mcp-servers` 属性|

## 使用对比

此表可帮助您决定使用哪种自定义功能。

|特点|如何触发|最适合|示例用例|
|---------|---------------|-------------|-------------------|
|[定制说明](<https://github.com/copilot/concepts/prompting/response-customization>)|自动|广泛适用于整个环境的标准、指南或期望|执行编码标准、可访问性规则、审查清单|
|[提示文件](<https://github.com/copilot/concepts/prompting/response-customization?tool=vscode#about-prompt-files>)|手动：直接在聊天中参考或使用提示文件选择器|专注于单个任务，每次使用不同的输入运行一次|生成单元测试，运行代码审查清单|
| [{% data variables.copilot.custom_agents_caps_short %}](<https://github.com/copilot/concepts/agents/cloud-agent/about-custom-agents>) |手动：从 IDE、{% data variables.product.github %} 或 {% data variables.copilot.copilot_cli_short %} 的 agent 下拉列表中选择|具有不同阶段、需要专业能力或严格交接的项目或流程|React审稿人agent，只读审核agent|
| [{% data variables.copilot.subagents_caps_short %}](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>) |自动，或直接在提示中引用 {% data variables.copilot.subagent_short %}|应与主任务隔离运行的复杂子任务 agent|代码库研究，运行测试套件|
| [Agent skills](<https://github.com/copilot/concepts/agents/about-agent-skills>) |自动：与您的提示相关时由 {% data variables.product.prodname_copilot_short %} 选择|具有应根据需要加载的捆绑资产的多步骤工作流程|{% data variables.product.prodname_actions %} 故障调试、部署程序、发行说明起草|
|[挂钩](<https://github.com/copilot/concepts/agents/hooks>)|自动：在配置的生命周期事件中|需要在 agent 生命周期中的特定点运行并保证执行的任务|每次文件编辑后运行格式化程序，批准或拒绝工具执行，使用 {% data variables.product.prodname_secret_scanning %} 防止凭证泄漏|
|[MCP 服务器](<https://github.com/copilot/concepts/context/mcp>)|自动，或按名称询问特定工具|需要访问外部工具或实时数据的任务|管理问题和 PR（{% data variables.product.github %} MCP 服务器），自动化浏览器测试（Playwright MCP 服务器）|

## IDE和表面支撑

此表显示每个 IDE 和表面支持哪些自定义功能。有关完整的 {% data variables.product.prodname_copilot_short %} 特征矩阵，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/copilot-feature-matrix#features-by-ide>)。

{% data variables.product.company_short %}建议使用最新稳定的IDE、{% data variables.copilot.copilot_cli_short %}和{% data variables.product.prodname_copilot_short %}扩展版本以获得最佳的{% data variables.product.prodname_copilot_short %}体验。

**密钥：**

* ✓ = 支持
* ✗ = 不支持
* P = 预览中

|特点| {% data variables.product.prodname_vscode_shortname %} | {% data variables.product.prodname_vs %} |JetBrains IDE|日食|Xcode|{% data variables.product.prodname_dotcom %}.com| {% data variables.copilot.copilot_cli_short %} |
|---------|:-------:|:-------------:|:---------:|:-------:|:-----:|:-------:|:---:|
|定制说明| ✓ | ✓ | P | P | P | ✓ | ✓ |
|提示文件| ✓ | ✓ | P | ✗ | P | ✗ | ✗ |
| {% data variables.copilot.custom_agents_caps_short %} | ✓ | ✓ | P | P | P | ✓ | ✓ |
| {% data variables.copilot.subagents_caps_short %} | ✓ | ✗ | P | P | P | ✗ | ✓ |
| Agent skills | ✓ | ✓ | P | ✗ | ✗ | ✓ | ✓ |
|挂钩| P | ✗ | ✗ | ✗ | ✗ | ✓ | ✓ |
|MCP 服务器| ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |

有关每个 IDE 和表面支持哪些类型的自定义指令的详细信息，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/custom-instructions-support>)。

## Agent JetBrains IDE 中的自定义编辑器

在 JetBrains IDE 中，您可以使用 Agent 自定义编辑器从一处管理多个 {% data variables.product.prodname_copilot_short %} 自定义。在{% data variables.copilot.copilot_chat %}面板中，单击右上角的设置图标，然后单击**自定义**。

从编辑器中，您可以：

* 查看和编辑{% data variables.copilot.custom_agents_short %}
* 管理可重复使用的技能
* 配置工作区或个人说明
* 管理提示文件
* 选择当前项目的**工作区**自定义项或跨项目跟随您的**个人**自定义项

有关在 JetBrains IDE 中创建存储库指令和提示文件的步骤，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/customize-copilot/configure-custom-instructions/add-repository-instructions-in-your-ide>)。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library>) - 精选示例集合


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data variables.product.prodname_copilot %} offers several customization features that let you tailor its behavior to your workflow, your team's standards, and your project's needs. Use the tables below to find the right one for your use case.

## Feature overview

This table shows what each customization feature is and where it lives.

| Feature | What it is | Filename and location |
|---------|-----------|-------------------|
| [Custom instructions](<https://github.com/copilot/concepts/prompting/response-customization>) | Always-on context that automatically applies to every interaction within its defined scope | `.github/copilot-instructions.md` (repo-wide), `.github/instructions/*.instructions.md` (path-specific), `AGENTS.md` (third-party agents), or personal/org settings via UI on {% data variables.product.github %} |
| [Prompt files](<https://github.com/copilot/concepts/prompting/response-customization?tool=vscode#about-prompt-files>) | Reusable, standalone prompt template with input variables | `.github/prompts/*.prompt.md` |
| [{% data variables.copilot.custom_agents_caps_short %}](<https://github.com/copilot/concepts/agents/cloud-agent/about-custom-agents>) | Specialist persona with its own instructions, tool restrictions, and context | `.github/agents/AGENT-NAME.md` (repository), `/agents/AGENT-NAME.md` in the organization's `.github` or `.github-private` repository (org-level), `/agents/AGENT-NAME.md` in a designated `.github-private` repository (enterprise-level), or user profile |
| [{% data variables.copilot.subagents_caps_short %}](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>) | Separate agent spawned by the main agent to handle delegated work in an isolated context | N/A (runtime process, not a user-configured file) |
| [Agent skills](<https://github.com/copilot/concepts/agents/about-agent-skills>) | Folder of instructions, scripts, and resources that {% data variables.product.prodname_copilot_short %} loads when relevant to a task | `.github/skills/<skill-name>/SKILL.md`, `.claude/skills/<skill-name>/SKILL.md`, or `.agents/skills/<skill-name>/SKILL.md` (project); `~/.copilot/skills/<skill-name>/SKILL.md` or `~/.agents/skills/<skill-name>/SKILL.md` (personal) |
| [Hooks](<https://github.com/copilot/concepts/agents/hooks>) | Custom shell commands that execute deterministically at specific points in an agent's workflow | `.github/hooks/*.json` |
| [MCP servers](<https://github.com/copilot/concepts/context/mcp>) | Connection to external systems, APIs, and databases | `mcp.json` (path varies by IDE), repository MCP settings on {% data variables.product.github %} (applies to {% data variables.copilot.copilot_cloud_agent_short %} and {% data variables.copilot.copilot_code-review_short %}), or `mcp-servers` property in {% data variables.copilot.copilot_custom_agent_short %} configurations |

## Usage comparison

This table helps you decide which customization feature to use.

| Feature | How to trigger | Best for | Example use cases |
|---------|---------------|-------------|-------------------|
| [Custom instructions](<https://github.com/copilot/concepts/prompting/response-customization>) | Automatic | Standards, guidelines, or expectations that apply broadly across a context | Enforce coding standards, accessibility rules, review checklists |
| [Prompt files](<https://github.com/copilot/concepts/prompting/response-customization?tool=vscode#about-prompt-files>) | Manual: reference directly in chat or use the prompt file picker | Focused single tasks you run once with different inputs each time | Generate unit tests, run a code review checklist |
| [{% data variables.copilot.custom_agents_caps_short %}](<https://github.com/copilot/concepts/agents/cloud-agent/about-custom-agents>) | Manual: select from the agent dropdown in your IDE, on {% data variables.product.github %}, or in {% data variables.copilot.copilot_cli_short %} | Projects or processes with distinct stages that need specialized capabilities or strict handoffs | React reviewer agent, read-only auditing agent |
| [{% data variables.copilot.subagents_caps_short %}](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>) | Automatic, or reference a {% data variables.copilot.subagent_short %} directly in your prompt | Complex subtasks that should run in isolation from the main agent | Codebase research, running test suites |
| [Agent skills](<https://github.com/copilot/concepts/agents/about-agent-skills>) | Automatic: chosen by {% data variables.product.prodname_copilot_short %} when relevant to your prompt | Multi-step workflows with bundled assets that should be loaded as needed | {% data variables.product.prodname_actions %} failure debugging, deployment procedures, release note drafting |
| [Hooks](<https://github.com/copilot/concepts/agents/hooks>) | Automatic: at configured lifecycle events | Tasks that need to run at a specific point in the agent lifecycle, with guaranteed execution | Run a formatter after every file edit, approve or deny tool executions, prevent credential leaks with {% data variables.product.prodname_secret_scanning %} |
| [MCP servers](<https://github.com/copilot/concepts/context/mcp>) | Automatic, or ask for a specific tool by name | Tasks that require access to external tools or real-time data | Manage issues and PRs ({% data variables.product.github %} MCP server), automate browser testing (Playwright MCP server) |

## IDE and surface support

This table shows which customization features are supported in each IDE and surface. For the full {% data variables.product.prodname_copilot_short %} feature matrix, see [AUTOTITLE](<https://github.com/copilot/reference/copilot-feature-matrix#features-by-ide>).

{% data variables.product.company_short %} recommends using the latest stable IDE, {% data variables.copilot.copilot_cli_short %}, and {% data variables.product.prodname_copilot_short %} extension versions to get the best {% data variables.product.prodname_copilot_short %} experience.

**Key:**

* ✓ = supported
* ✗ = not supported
* P = under preview

| Feature | {% data variables.product.prodname_vscode_shortname %} | {% data variables.product.prodname_vs %} | JetBrains IDEs | Eclipse | Xcode | {% data variables.product.prodname_dotcom %} .com | {% data variables.copilot.copilot_cli_short %} |
|---------|:-------:|:-------------:|:---------:|:-------:|:-----:|:-------:|:---:|
| Custom instructions | ✓ | ✓ | P | P | P | ✓ | ✓ |
| Prompt files | ✓ | ✓ | P | ✗ | P | ✗ | ✗ |
| {% data variables.copilot.custom_agents_caps_short %} | ✓ | ✓ | P | P | P | ✓ | ✓ |
| {% data variables.copilot.subagents_caps_short %} | ✓ | ✗ | P | P | P | ✗ | ✓ |
| Agent skills | ✓ | ✓ | P | ✗ | ✗ | ✓ | ✓ |
| Hooks | P | ✗ | ✗ | ✗ | ✗ | ✓ | ✓ |
| MCP servers | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |

For a detailed breakdown of which types of custom instructions are supported in each IDE and surface, see [AUTOTITLE](<https://github.com/copilot/reference/custom-instructions-support>).

## Agent Customizations editor in JetBrains IDEs

In JetBrains IDEs, you can use the Agent Customizations editor to manage multiple {% data variables.product.prodname_copilot_short %} customizations from one place. In the {% data variables.copilot.copilot_chat %} panel, click the settings icon in the top-right, then click **Customizations**.

From the editor, you can:

* View and edit {% data variables.copilot.custom_agents_short %}
* Manage reusable skills
* Configure workspace or personal instructions
* Manage prompt files
* Choose **Workspace** customizations for the current project or **Personal** customizations that follow you across projects

For steps to create repository instructions and prompt files in JetBrains IDEs, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/customize-copilot/configure-custom-instructions/add-repository-instructions-in-your-ide>).

## Further reading

* [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library>)—a curated collection of examples


</details>
