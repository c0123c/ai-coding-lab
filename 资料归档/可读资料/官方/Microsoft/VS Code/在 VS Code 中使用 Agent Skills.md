# 在 VS Code 中使用 Agent Skills

介绍创建或生成技能、安装共享技能、SKILL.md 格式和附属资源，比较技能与项目指令的用途。

- 来源：Microsoft／VS Code
- 资料类型：存档文档
- 文档时间：官方审核：[2026-10-07](<https://code.visualstudio.com/docs/agent-customization/agent-skills>)；官方文件提交（截至抓取）：[2026-10-07](<https://github.com/microsoft/vscode-docs/commit/eeb6997081a1b674fb894129737e40ffbe7dfc05>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.visualstudio.com/docs/agent-customization/agent-skills>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/在 VS Code 中使用 Agent Skills.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 在VS Code中使用Agent Skills

* [以 Markdown 形式查看](<https://code.visualstudio.com/docs/agent-customization/agent-skills.md>)
* [在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fagent-skills%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问
* [在VS Code内部人士中提问](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fagent-skills%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fagent-skills%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问

---

Agent Skills 是 AI agents 可以在与执行专门任务相关时加载的指令、脚本和资源的文件夹。 Agent Skills 是一个 [ 开放标准 ](<https://agentskills.io>)，适用于多个 AI agents，包括 VS Code 中的 GitHub Copilot、GitHub Copilot CLI、GitHub Copilot 应用程序、Copilot 云 agent 和 OpenAI Codex [ 通过 Agent 主机 ](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_codex>) （实验性）。

与主要定义编码指南的[自定义指令](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>)不同，技能支持专门的功能和工作流程，其中可以包括脚本、示例和其他资源。您创建的技能可以在技能兼容的 agents 之间移植，但支持的位置和可选功能因 agent 而异。除非另有说明，本页的配置选项和控件适用于Copilot。

有关技能与其他自定义选项的比较，请参阅[自定义概念](<https://code.visualstudio.com/docs/agents/concepts/customization>)。

Agent Skills 的主要优点：

* **专业化Copilot**：为特定领域的任务定制功能，无需重复上下文
* **减少重复**：创建一次，在所有对话中自动使用
* **撰写功能**：结合多种技能来构建复杂的工作流程
* **高效加载**：仅在需要时将相关内容加载到上下文中

提示

使用 [Agent 自定义编辑器 ](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>)（预览版）在一处发现、创建和管理所有 agent 自定义。运行 **Chat：从命令面板打开自定义 **。

## Agent Skills 与自定义指令

虽然 Agent Skills 和自定义指令都有助于自定义 Copilot 的行为，但它们有不同的用途：

|特点| Agent Skills |定制说明|
| --- | --- | --- |
|**用途**|教授专业能力和工作流程|定义编码标准和指南|
|**便携性**|适用于 VS Code、Copilot CLI、GitHub Copilot 应用程序、Copilot 云 agent 和 OpenAI Codex Agent 主机（实验）|可在支持所选 [ 指令格式 ](<https://code.visualstudio.com/docs/agent-customization/custom-instructions#_choose-a-format>) 的体验中重复使用|
|**内容**|说明、脚本、示例和资源|仅说明|
|**范围**|特定于任务、按需加载|始终应用（或通过全局模式）|
|**标准型**|开放标准（[agentskills.io](<https://agentskills.io>)）|多种格式，例如`AGENTS.md`和`.github/copilot-instructions.md`|

例如，您可以在 VS Code 和 [GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions>) 之间共享存储库的 `.github/copilot-instructions.md`。检查每种体验支持的文件位置和激活规则。

当您想要执行以下操作时，请使用 Agent Skills：

* 创建跨不同 AI 工具工作的可重用功能
* 包括脚本、示例或其他资源以及说明
* 与更广泛的 AI 社区分享功能
* 定义专门的工作流程，例如测试、调试或部署流程

当您想要执行以下操作时，请使用自定义说明：

* 定义项目特定的编码标准
* 设置语言或框架约定
* 指定代码审查或提交消息指南
* 使用 glob 模式根据文件类型应用规则

## 创建技能

提示

在聊天输入中输入`/skills`即可快速打开**配置技能**菜单。

技能存储在具有定义技能行为的 `SKILL.md` 文件的目录中。 VS Code支持两种技能：

|技能类型|地点|
| --- | --- |
|项目技能，存储在您的存储库中| `.github/skills/`, `.claude/skills/`, `.agents/skills/` |
|个人技能，存储在您的用户个人资料中| `~/.copilot/skills/`, `~/.claude/skills/`, `~/.agents/skills/` |

注意事项

要使用 OpenAI Codex 重用存储库技能，[在 Agent 主机 ](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_codex>) 上设置 Codex（实验）。此集成会在您第一次提示之前发现 `.github/skills/` 中的工作区技能，无需额外的技能位置配置。 Codex 本身也发现了 `.agents/skills/`。

如果 `.github/skills/` 中的技能在工作区根中具有重复名称，则主根优先。 Discovery 使技能可供模型使用，但不能保证它为每个相关提示调用技能。

注意事项

的


聊天。agentSkillsLocations

在 VS Code
 中打开 在 VS Code 内部人员中打开 
 设置已弃用，仅由本地 agent 使用。如果您使用此设置配置了其他技能位置，[会将技能迁移到支持的位置](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_move-customizations-from-configured-locations>)。

提示

在 monorepo 中，启用


聊天。useCustomizationsInParentRepositories

在 VS Code
中打开 在 VS Code Insiders
 中打开，以从父存储库根中发现技能。了解有关 [ 父存储库发现 ](<https://code.visualstudio.com/docs/agent-customization/overview#_use-customizations-in-a-monorepo>) 的更多信息。

创建技能：

1. 在聊天视图中，选择 ** 配置聊天 **（齿轮图标）以打开 Agent 自定义编辑器，然后选择 ** 技能 ** 选项卡。
2. 从下拉列表中选择 **新技能（工作区）** 或 **新技能（用户）**，具体取决于您要存储技能的位置。

![Agent 自定义编辑器的屏幕截图，显示“技能”选项卡和用于创建新技能的下拉列表。](<https://code.visualstudio.com/assets/docs/agent-customization/customization/create-skill-v2.png>)
3. 选择位置并输入技能的名称。
4. 通过填写 YAML frontmatter 并在文件正文中添加说明来完成 `SKILL.md` 文件。

   ```
   ---
   name: skill-name
   description: Description of what the skill does and when to use it
   ---

   # Skill Instructions

   Your detailed instructions, guidelines, and examples go here...
   ```
5. （可选）将脚本、示例或其他资源添加到您的技能目录中。

例如，测试 Web 应用程序的技能可能包括：

   * `SKILL.md` - 运行测试的说明
   * `test-template.js` - 模板测试文件
   * `examples/` - 示例测试场景

注意事项

确保引用 `SKILL.md` 中的任何其他文件，以便 agent 拾取它们。使用 Markdown 链接或 `#file:` 引用以及相对于 `SKILL.md` 文件的路径，例如 `[test template](./test-template.js)` 或 `#file:./test-template.js`。

### 使用 AI 生成技能

您可以使用 AI 根据能力描述生成技能。在聊天中输入 `/create-skill` 并描述您想要的技能（例如，“运行和调试集成测试的技能”）。 agent 提出澄清问题并生成包含目录结构、指令和 frontmatter 的 `SKILL.md` 文件。

您还可以从正在进行的对话中提取可重复使用的技能。例如，在调试复杂问题的多回合会话之后，询问“根据我们刚刚调试的方式创建技能”，以将多步骤过程捕获为可重用技能。

您还可以通过从下拉列表中选择**生成技能**，从Agent自定义编辑器生成技能。

### 从自定义市场安装技能（实验性）

使用自定义市场从配置的源查找并安装完整的技能包：

1. 打开


chat.customizations.marketplace.enabled

在 VS Code
 中打开 在 VS Code 内部人士 
 设置中打开。
2. 打开 Agent 自定义编辑器并选择 ** 发现 **。
3. 从源菜单中，选择 **GitHub Feed**，然后按名称搜索技能或将 `@type:skill` 添加到搜索中。
4. 选择一项技能并查看其发布者、来源和包含的文件。
5. 选择 ** 安装 **，然后选择要存储技能的用户或工作区技能目录。

VS Code 不会覆盖所选目的地的现有技能。如果另一个受支持的位置包含同名的技能，则这两项技能都会显示在 **技能** 页面上。源或发布者映像标识市场安装，**复制相对路径**显示其确切位置。

市场所有权适用于确切安装的技能目录。从 **技能 ** 页面删除市场安装的技能只会卸载该技能，而不会删除其他位置的同名本地技能。

如果市场安装的技能中的文件丢失，**Discover** 将显示 **Missing files**。选择**修复**仅恢复丢失的打包文件。您更改或添加的文件保持不变。

有关浏览源和安装状态的更多信息，请参阅[发现市场自定义](<https://code.visualstudio.com/docs/agent-customization/overview#_discover-marketplace-customizations-experimental>)。

## SKILL.md 文件格式

`SKILL.md` 文件是一个带有 YAML frontmatter 的 Markdown 文件，用于定义技能的元数据和行为。

### 标题（必填）

标头格式为 YAML frontmatter，包含以下字段：

|领域|必填|描述|
| --- | --- | --- |
| `name` |是的|技能的唯一标识符。只允许使用小写字母、数字和连字符（例如，`webapp-testing`）。不要使用斜杠、冒号、点或命名空间前缀。必须与父目录名称匹配。最多 64 个字符。名称中包含无效字符会导致技能加载失败。|
| `description` |是的|该技能的作用**以及何时使用它的描述**。具体说明功能和用例，以帮助 Copilot 决定何时加载技能。最多 1024 个字符。|
| `argument-hint` |否|当技能作为斜线命令调用时，聊天输入字段中显示的提示文本。帮助用户了解需要提供哪些附加信息（例如，`[test file] [options]`）。|
| `user-invocable` |否|控制技能是否在聊天菜单中显示为斜线命令。默认为 `true`。设置为 `false` 可从 `/` 菜单中隐藏该技能，同时仍允许 agent 自动加载该技能。|
| `disable-model-invocation` |否|控制agent是否可以根据相关性自动加载技能。默认为 `false`。设置为 `true` 仅需要通过 `/` 斜杠命令手动调用。|
|`context` **分叉技能上下文 ** 是实验性的，可能会更改或删除。|否|控制技能的加载方式。默认为内联（技能的指令添加到父级 agent 的上下文中）。设置为 `fork` 以在专用子代理上下文中运行技能。请参阅[在分叉上下文中运行技能](<#_run-a-skill-in-a-forked-context>)。|

重要

当技能通过[插件](<https://code.visualstudio.com/docs/agent-customization/agent-plugins>)分发时，插件名称将自动用作命令前缀（例如，`/my-plugin:test-runner`）。不要手动将命名空间前缀添加到技能 `name` 字段。使用 `myorg/skillname` 或 `myorg:skillname` 等前缀会导致技能加载失败。

### 身体

技能正文包含Copilot使用此技能时应遵循的说明、指南和示例。写出清晰、具体的说明来描述：

* 该技能有助于完成什么
* 何时使用该技能
* 需要遵循的分步程序
* 预期输入和输出的示例
* 对任何包含的脚本或资源的引用

您可以使用 Markdown 链接或 `#file:` 语法来引用文件。对于技能目录中的文件，请使用相对于 `SKILL.md` 文件的路径，例如 `[test script](./test-template.js)`。要引用您的环境用户主文件夹，请使用 `~`，或以 `~/` 开头的路径，例如 `#file:~/skill-resources/test-template.js`。使用 Unix 风格的 `/` 路径分隔符来保持技能在操作系统之间的可移植性。

### 在分叉上下文中运行技能

默认情况下，当VS Code加载技能时，该技能的指令将添加到父级agent的上下文窗口中。对于大型技能或中间推理与对话的其余部分不相关的技能，您可以在 ** 分叉上下文 ** 中运行该技能。在分叉上​​下文中，技能在专用子代理中执行，并且仅将其最终结果返回给父代理 agent。这可以让你的主要对话的上下文保持干净。

要在分叉上下文中运行技能，请将 `SKILL.md` frontmatter 中的 `context` 字段设置为 `fork`：

```
---
name: review-pr
description: Review a pull request for code quality, style, and correctness. Use when asked to review a PR.
context: fork
---

# PR review

Follow these steps to review the pull request...
```

使用 `context: fork` 来获得以下技能：

* 阅读许多文件或进行冗长的调查，其详细信息不需要保留在主要对话中
* 生成父级 agent 可以直接采取行动的重点结果（例如摘要、报告或一小组编辑）
* 不应影响父级 agent 超出其最终输出的行为

注意事项

要在分叉上下文中运行技能，请启用


github.copilot.chat.skillTool.enabled

在VS Code
中打开在VS Code中的VS Code内部人员
设置中打开。

## 技能示例

以下示例演示了您可以创建的不同类型的技能。

示例：Web应用程序测试技巧

````
---
name: webapp-testing
description: Guide for testing web applications using Playwright. Use this when asked to create or run browser-based tests.
---

# Web Application Testing with Playwright

This skill helps you create and run browser-based tests for web applications using Playwright.

## When to use this skill

Use this skill when you need to:
- Create new Playwright tests for web applications
- Debug failing browser tests
- Set up test infrastructure for a new project

## Creating tests

1. Review the [test template](./test-template.js) for the standard test structure
2. Identify the user flow to test
3. Create a new test file in the `tests/` directory
4. Use Playwright's locators to find elements (prefer role-based selectors)
5. Add assertions to verify expected behavior

## Running tests

To run tests locally:
```bash
npx playwright test
```

To debug tests:
```bash
npx playwright test --debug
```

## Best practices

- Use data-testid attributes for dynamic content
- Keep tests independent and atomic
- Use Page Object Model for complex pages
- Take screenshots on failure
````


示例：GitHub Actions 调试技巧

```
---
name: github-actions-debugging
description: Guide for debugging failing GitHub Actions workflows. Use this when asked to debug failing GitHub Actions workflows.
---

# GitHub Actions Debugging

This skill helps you debug failing GitHub Actions workflows in pull requests.

## Process

1. Use the `list_workflow_runs` tool to look up recent workflow runs for the pull request and their status
2. Use the `summarize_job_log_failures` tool to get an AI summary of the logs for failed jobs
3. If you need more information, use the `get_job_logs` or `get_workflow_run_logs` tool to get the full failure logs
4. Try to reproduce the failure locally in your environment
5. Fix the failing build and verify the fix before committing changes

## Common issues

- **Missing environment variables**: Check that all required secrets are configured
- **Version mismatches**: Verify action versions and dependencies are compatible
- **Permission issues**: Ensure the workflow has the necessary permissions
- **Timeout issues**: Consider splitting long-running jobs or increasing timeout values
```

## 使用技能作为斜线命令

技能可以在聊天中以斜杠命令的形式使用，以及[提示文件](<https://code.visualstudio.com/docs/agent-customization/prompt-files>)。在聊天输入字段中键入 `/` 以查看可用技能和提示的列表，然后选择一项技能来调用它。

您可以在斜杠命令后添加额外的上下文。例如，`/webapp-testing for the login page` 或 `/github-actions-debugging PR #42`。

默认情况下，所有技能都会出现在`/`菜单中。使用 `user-invocable` 和 `disable-model-invocation` frontmatter 属性来控制如何访问每个技能：

|配置|斜线命令|由Copilot自动加载|使用案例|
| --- | --- | --- | --- |
|默认（省略两个属性）|是的|是的|通用技能|
| `user-invocable: false` |否|是的|模型在相关时加载的背景知识技能|
| `disable-model-invocation: true` |是的|否|您只想按需运行的技能|
|两套|否|否|残疾人技能|

## Copilot如何使用技能

技能逐步加载内容以保持上下文高效。以下是 Copilot 如何使用 `webapp-testing` 技能的示例：

1. **发现**：Copilot从YAML前面读取技能的`name`和`description`。当您询问“帮我测试登录页面”时，Copilot 根据其描述将其与 `webapp-testing` 技能相匹配。
2. **加载指令**：Copilot将`SKILL.md`主体加载到其上下文中，使其能够访问详细的测试程序和指南。您也可以通过在聊天中输入`/webapp-testing`直接触发此步骤。
3. **资源访问**：当Copilot按照指令工作时，它仅在引用技能目录中的其他文件时访问它们，例如`test-template.js`或示例场景。如果说明中未引用文件，则不会加载该文件。

这种三级加载系统意味着您可以在不消耗上下文的情况下安装许多技能。 Copilot 仅加载与每个任务相关的内容。

选择加入 [ 分叉上下文 ](<#_run-a-skill-in-a-forked-context>) 的技能遵循相同的发现步骤，但它们的指令和它们读取的任何文件都会加载到单独的子代理中。仅技能的最终结果返回给父项 agent。

## 使用共享技能

你可以使用别人创造的技能来增强Copilot的能力。 [github/awesome-copilot](<https://github.com/github/awesome-copilot>) 存储库包含不断增长的社区技能集合、自定义 agents、说明和提示。 [anthropics/skills](<https://github.com/anthropics/skills>) 存储库包含其他参考技能。

您还可以发现并安装[agent插件](<https://code.visualstudio.com/docs/agent-customization/agent-plugins>)中捆绑的技能。已安装插件的技能与您本地定义的技能一起显示在 **配置技能** 菜单中。

要使用共享技能：

1. 浏览存储库中的可用技能
2. 将技能目录复制到你的`.github/skills/`文件夹中
3. 根据您的需求查看并自定义 `SKILL.md` 文件
4. （可选）根据需要修改或添加资源

提示

在使用共享技能之前，请务必先检查它们，以确保它们满足您的要求和安全标准。 VS Code 的 [ 终端工具 ](<https://code.visualstudio.com/docs/agents/run/tools#_run-terminal-commands>) 提供脚本执行控制，包括 [ 自动批准选项 ](<https://code.visualstudio.com/docs/agents/run/approvals#_automatically-approve-terminal-commands>)，具有可配置的允许列表和对代码运行的严格控制。详细了解自动批准功能的 [安全注意事项 ](<https://code.visualstudio.com/docs/agents/run/security#_approvals-and-review>)。

## 通过扩展贡献技能

分机可以使用 `package.json` 中的 `chatSkills` 贡献点来贡献技能。该路径必须指向包含 `SKILL.md` 文件的目录，遵循 [Agent Skills 规范 ](<https://agentskills.io/specification>)。

### 所需的文件夹结构

技能目录必须遵循以下结构：

```
extension-root/
└── skills/
    └── my-skill/           # Directory name must match the `name` field in SKILL.md
        └── SKILL.md         # Required
```

### 在package.json中注册技能

在您的扩展程序的 `package.json` 中添加 `chatSkills` 贡献点。 `path` 属性必须指向相应的 `SKILL.md` 文件：

```
{
  "contributes": {
    "chatSkills": [
      {
        "path": "./skills/my-skill/SKILL.md"
      }
    ]
  }
}
```

重要

`SKILL.md` frontmatter 中的 `name` 字段必须与父目录名称匹配。例如，如果目录为`skills/my-skill/`，则`name`字段必须为`my-skill`。如果名称不匹配，则不会加载技能。

`SKILL.md` 文件遵循与 [ 项目和个人技能 ](<#_create-a-skill>) 相同的格式。例如：

```
---
name: my-skill
description: Description of what the skill does and when to use it.
---

# My Skill

Detailed instructions for the skill...
```

## Agent Skills标准

Agent Skills 是一种开放标准，可实现不同 AI agents 之间的可移植性。您在 VS Code 中创建的技能可与多个 agents 配合使用，包括：

* VS Code**中的**GitHub Copilot：在聊天和agent模式下可用
* **GitHub Copilot CLI**：在终端中工作时可访问
* **GitHub Copilot 应用程序**：可在专用桌面体验中使用。
* **Copilot 云 agent**：在自动化编码任务期间使用
* **OpenAI Codex 到 Agent 主机（实验）**：在 [ 设置集成 ](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_codex>) 后发现 `.github/skills/` 中的工作区技能。

如需了解有关 Agent Skills 标准的更多信息，请访问 [agentskills.io](<https://agentskills.io>)。

## 相关资源

* [自定义 AI 回复概述](<https://code.visualstudio.com/docs/agent-customization/overview>)
* [创建自定义指令](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>)
* [创建可重复使用的提示文件](<https://code.visualstudio.com/docs/agent-customization/prompt-files>)
* [创建自定义agents](<https://code.visualstudio.com/docs/agent-customization/custom-agents>)
* [Agent Skills 规格](<https://agentskills.io>)
* [参考技能库](<https://github.com/anthropics/skills>)
* [发现和管理 agent 插件](<https://code.visualstudio.com/docs/agent-customization/agent-plugins>)

10/7/2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Use Agent Skills in VS Code

* [View as Markdown](<https://code.visualstudio.com/docs/agent-customization/agent-skills.md>)
* [Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fagent-skills%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)
* [Ask in VS Code Insiders](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fagent-skills%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fagent-skills%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

---

Agent Skills are folders of instructions, scripts, and resources that AI agents can load when relevant to perform specialized tasks. Agent Skills is an [open standard](<https://agentskills.io>) that works across multiple AI agents, including GitHub Copilot in VS Code, GitHub Copilot CLI, the GitHub Copilot app, Copilot cloud agent, and OpenAI Codex [through Agent Host](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_codex>) (Experimental).

Unlike [custom instructions](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>) that primarily define coding guidelines, skills enable specialized capabilities and workflows that can include scripts, examples, and other resources. Skills you create are portable across skills-compatible agents, but supported locations and optional features vary by agent. Unless otherwise noted, the configuration options and controls on this page apply to Copilot.

For how skills compare with the other customization options, see [Customization concepts](<https://code.visualstudio.com/docs/agents/concepts/customization>).

Key benefits of Agent Skills:

* **Specialize Copilot**: Tailor capabilities for domain-specific tasks without repeating context
* **Reduce repetition**: Create once, use automatically across all conversations
* **Compose capabilities**: Combine multiple skills to build complex workflows
* **Efficient loading**: Only relevant content loads into context when needed

Tip

Use the [Agent Customizations editor](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>) (Preview) to discover, create, and manage all your agent customizations in one place. Run **Chat: Open Customizations** from the Command Palette.

## Agent Skills vs custom instructions

While both Agent Skills and custom instructions help customize Copilot's behavior, they serve different purposes:

| Feature | Agent Skills | Custom Instructions |
| --- | --- | --- |
| **Purpose** | Teach specialized capabilities and workflows | Define coding standards and guidelines |
| **Portability** | Works across VS Code, Copilot CLI, the GitHub Copilot app, Copilot cloud agent, and OpenAI Codex through Agent Host (Experimental) | Reusable across experiences that support the selected [instruction format](<https://code.visualstudio.com/docs/agent-customization/custom-instructions#_choose-a-format>) |
| **Content** | Instructions, scripts, examples, and resources | Instructions only |
| **Scope** | Task-specific, loaded on-demand | Always applied (or via glob patterns) |
| **Standard** | Open standard ([agentskills.io](<https://agentskills.io>)) | Multiple formats, such as `AGENTS.md` and `.github/copilot-instructions.md` |

For example, you can share a repository's `.github/copilot-instructions.md` between VS Code and [GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions>). Check the supported file locations and activation rules for each experience.

Use Agent Skills when you want to:

* Create reusable capabilities that work across different AI tools
* Include scripts, examples, or other resources alongside instructions
* Share capabilities with the wider AI community
* Define specialized workflows like testing, debugging, or deployment processes

Use custom instructions when you want to:

* Define project-specific coding standards
* Set language or framework conventions
* Specify code review or commit message guidelines
* Apply rules based on file types using glob patterns

## Create a skill

Tip

Type `/skills` in the chat input to quickly open the **Configure Skills** menu.

Skills are stored in directories with a `SKILL.md` file that defines the skill's behavior. VS Code supports two types of skills:

| Skill type | Location |
| --- | --- |
| Project skills, stored in your repository | `.github/skills/`, `.claude/skills/`, `.agents/skills/` |
| Personal skills, stored in your user profile | `~/.copilot/skills/`, `~/.claude/skills/`, `~/.agents/skills/` |

Note

To reuse repository skills with OpenAI Codex, [set up Codex on Agent Host](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_codex>) (Experimental). This integration discovers workspace skills in `.github/skills/` before your first prompt, without additional skill-location configuration. Codex also discovers `.agents/skills/` natively.

If skills in `.github/skills/` have duplicate names across workspace roots, the primary root takes precedence. Discovery makes skills available to the model but does not guarantee that it invokes a skill for every relevant prompt.

Note

The 


chat.agentSkillsLocations

Open in VS Code
Open in VS Code Insiders
 setting is deprecated and only used by the Local agent. If you configured other skill locations with this setting, [migrate the skills to supported locations](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_move-customizations-from-configured-locations>).

Tip

In a monorepo, enable 


chat.useCustomizationsInParentRepositories

Open in VS Code
Open in VS Code Insiders
 to discover skills from the parent repository root. Learn more about [parent repository discovery](<https://code.visualstudio.com/docs/agent-customization/overview#_use-customizations-in-a-monorepo>).

To create a skill:

1. In the Chat view, select **Configure Chat** (gear icon) to open the Agent Customizations editor and then select the **Skills** tab.
2. Select **New Skill (Workspace)** or **New Skill (User)** from the dropdown, depending on where you want to store the skill.

   ![Screenshot of the Agent Customizations editor, showing the Skills tab and the dropdown to create a new skill.](<https://code.visualstudio.com/assets/docs/agent-customization/customization/create-skill-v2.png>)
3. Select the location and enter a name for the skill.
4. Complete the `SKILL.md` file by filling in the YAML frontmatter and adding instructions in the body of the file.

   ```
   ---
   name: skill-name
   description: Description of what the skill does and when to use it
   ---

   # Skill Instructions

   Your detailed instructions, guidelines, and examples go here...
   ```
5. Optionally, add scripts, examples, or other resources to your skill's directory.

   For example, a skill for testing web applications might include:

   * `SKILL.md` - Instructions for running tests
   * `test-template.js` - A template test file
   * `examples/` - Example test scenarios

   Note

   Make sure to reference any additional files in your `SKILL.md` for them to be picked up by the agent. Use Markdown links or `#file:` references with paths relative to the `SKILL.md` file, such as `[test template](./test-template.js)` or `#file:./test-template.js`.

### Generate a skill with AI

You can use AI to generate a skill based on a description of the capability. Type `/create-skill` in chat and describe the skill you want (for example, "a skill for running and debugging integration tests"). The agent asks clarifying questions and generates a `SKILL.md` file with the directory structure, instructions, and frontmatter.

You can also extract a reusable skill from an ongoing conversation. For example, after a multi-turn session where you debugged a complex issue, ask "create a skill from how we just debugged that" to capture the multi-step procedure as a reusable skill.

You can also generate a skill from the Agent Customizations editor by selecting **Generate Skill** from the dropdown.

### Install a skill from the Customization Marketplace (Experimental)

Use the Customization Marketplace to find and install complete skill packages from configured sources:

1. Turn on the 


   chat.customizations.marketplace.enabled

   Open in VS Code
   Open in VS Code Insiders
    setting.
2. Open the Agent Customizations editor and select **Discover**.
3. From the source menu, select **GitHub Feed**, and then search for a skill by name or add `@type:skill` to your search.
4. Select a skill and review its publisher, source, and included files.
5. Select **Install**, and then choose the user or workspace skills directory where you want to store the skill.

VS Code doesn't overwrite an existing skill at the selected destination. If another supported location contains a skill with the same name, both skills appear on the **Skills** page. The source or publisher image identifies the Marketplace installation, and **Copy Relative Path** shows its exact location.

Marketplace ownership applies to the exact installed skill directory. Deleting a Marketplace-installed skill from the **Skills** page uninstalls only that skill and doesn't remove a same-name local skill in another location.

If files from a Marketplace-installed skill are missing, **Discover** shows **Missing files**. Select **Repair** to restore only the missing packaged files. Files that you changed or added remain unchanged.

For more information about browsing sources and installation states, see [Discover marketplace customizations](<https://code.visualstudio.com/docs/agent-customization/overview#_discover-marketplace-customizations-experimental>).

## SKILL.md file format

The `SKILL.md` file is a Markdown file with YAML frontmatter that defines the skill's metadata and behavior.

### Header (required)

The header is formatted as YAML frontmatter with the following fields:

| Field | Required | Description |
| --- | --- | --- |
| `name` | Yes | A unique identifier for the skill. Only lowercase letters, numbers, and hyphens are allowed (for example, `webapp-testing`). Do not use slashes, colons, dots, or namespace prefixes. Must match the parent directory name. Maximum 64 characters. Names with invalid characters cause the skill to silently fail to load. |
| `description` | Yes | A description of what the skill does **and when to use it**. Be specific about both capabilities and use cases to help Copilot decide when to load the skill. Maximum 1024 characters. |
| `argument-hint` | No | Hint text shown in the chat input field when the skill is invoked as a slash command. Helps users understand what additional information to provide (for example, `[test file] [options]`). |
| `user-invocable` | No | Controls whether the skill appears as a slash command in the chat menu. Defaults to `true`. Set to `false` to hide the skill from the `/` menu while still allowing the agent to load it automatically. |
| `disable-model-invocation` | No | Controls whether the agent can automatically load the skill based on relevance. Defaults to `false`. Set to `true` to require manual invocation through the `/` slash command only. |
| `context`  **Forked skill context** is experimental and might change or be removed. | No | Controls how the skill is loaded. Defaults to inline (the skill's instructions are added to the parent agent's context). Set to `fork` to run the skill in a dedicated subagent context. See [Run a skill in a forked context](<#_run-a-skill-in-a-forked-context>). |

Important

When a skill is distributed through a [plugin](<https://code.visualstudio.com/docs/agent-customization/agent-plugins>), the plugin name is automatically used as a command prefix (for example, `/my-plugin:test-runner`). Do not manually add namespace prefixes to the skill `name` field. Using prefixes like `myorg/skillname` or `myorg:skillname` causes the skill to silently fail to load.

### Body

The skill body contains the instructions, guidelines, and examples that Copilot should follow when using this skill. Write clear, specific instructions that describe:

* What the skill helps accomplish
* When to use the skill
* Step-by-step procedures to follow
* Examples of the expected input and output
* References to any included scripts or resources

You can reference files by using Markdown links or the `#file:` syntax. For files within the skill directory, use paths relative to the `SKILL.md` file, such as `[test script](./test-template.js)`. To reference your environment user home folder, use `~`, or start a path with `~/`, such as `#file:~/skill-resources/test-template.js`. Use Unix-style `/` path separators to keep skills portable across operating systems.

### Run a skill in a forked context

By default, when VS Code loads a skill, the skill's instructions are added to the parent agent's context window. For large skills, or skills whose intermediate reasoning isn't relevant to the rest of your conversation, you can instead run the skill in a **forked context**. In a forked context, the skill executes in a dedicated subagent and only its final result is returned to the parent agent. This keeps your main conversation's context clean.

To run a skill in a forked context, set the `context` field in the `SKILL.md` frontmatter to `fork`:

```
---
name: review-pr
description: Review a pull request for code quality, style, and correctness. Use when asked to review a PR.
context: fork
---

# PR review

Follow these steps to review the pull request...
```

Use `context: fork` for skills that:

* Read many files or run lengthy investigations whose details don't need to stay in the main conversation
* Produce a focused result (such as a summary, a report, or a small set of edits) that the parent agent can act on directly
* Should not influence the parent agent's behavior beyond their final output

Note

To run a skill in a forked context, enable the 


github.copilot.chat.skillTool.enabled

Open in VS Code
Open in VS Code Insiders
 setting in VS Code.

## Example skills

The following examples demonstrate different types of skills you can create.

Example: Web application testing skill

````
---
name: webapp-testing
description: Guide for testing web applications using Playwright. Use this when asked to create or run browser-based tests.
---

# Web Application Testing with Playwright

This skill helps you create and run browser-based tests for web applications using Playwright.

## When to use this skill

Use this skill when you need to:
- Create new Playwright tests for web applications
- Debug failing browser tests
- Set up test infrastructure for a new project

## Creating tests

1. Review the [test template](./test-template.js) for the standard test structure
2. Identify the user flow to test
3. Create a new test file in the `tests/` directory
4. Use Playwright's locators to find elements (prefer role-based selectors)
5. Add assertions to verify expected behavior

## Running tests

To run tests locally:
```bash
npx playwright test
```

To debug tests:
```bash
npx playwright test --debug
```

## Best practices

- Use data-testid attributes for dynamic content
- Keep tests independent and atomic
- Use Page Object Model for complex pages
- Take screenshots on failure
````


Example: GitHub Actions debugging skill

```
---
name: github-actions-debugging
description: Guide for debugging failing GitHub Actions workflows. Use this when asked to debug failing GitHub Actions workflows.
---

# GitHub Actions Debugging

This skill helps you debug failing GitHub Actions workflows in pull requests.

## Process

1. Use the `list_workflow_runs` tool to look up recent workflow runs for the pull request and their status
2. Use the `summarize_job_log_failures` tool to get an AI summary of the logs for failed jobs
3. If you need more information, use the `get_job_logs` or `get_workflow_run_logs` tool to get the full failure logs
4. Try to reproduce the failure locally in your environment
5. Fix the failing build and verify the fix before committing changes

## Common issues

- **Missing environment variables**: Check that all required secrets are configured
- **Version mismatches**: Verify action versions and dependencies are compatible
- **Permission issues**: Ensure the workflow has the necessary permissions
- **Timeout issues**: Consider splitting long-running jobs or increasing timeout values
```

## Use skills as slash commands

Skills are available as slash commands in chat, alongside [prompt files](<https://code.visualstudio.com/docs/agent-customization/prompt-files>). Type `/` in the chat input field to see a list of available skills and prompts, and select a skill to invoke it.

You can add extra context after the slash command. For example, `/webapp-testing for the login page` or `/github-actions-debugging PR #42`.

By default, all skills appear in the `/` menu. Use the `user-invocable` and `disable-model-invocation` frontmatter properties to control how each skill is accessed:

| Configuration | Slash command | Auto-loaded by Copilot | Use case |
| --- | --- | --- | --- |
| Default (both properties omitted) | Yes | Yes | General-purpose skills |
| `user-invocable: false` | No | Yes | Background knowledge skills that the model loads when relevant |
| `disable-model-invocation: true` | Yes | No | Skills you only want to run on demand |
| Both set | No | No | Disabled skills |

## How Copilot uses skills

Skills load content progressively to keep your context efficient. Here is an example of how Copilot uses the `webapp-testing` skill:

1. **Discovery**: Copilot reads the skill's `name` and `description` from the YAML frontmatter. When you ask "help me test the login page", Copilot matches this to the `webapp-testing` skill based on its description.
2. **Instructions loading**: Copilot loads the `SKILL.md` body into its context, giving it access to the detailed testing procedures and guidelines. You can also trigger this step directly by typing `/webapp-testing` in chat.
3. **Resource access**: As Copilot works through the instructions, it accesses additional files in the skill directory, such as `test-template.js` or example scenarios, only when it references them. If a file isn't referenced in the instructions, it won't be loaded.

This three-level loading system means you can install many skills without consuming context. Copilot loads only what is relevant for each task.

Skills that opt in to a [forked context](<#_run-a-skill-in-a-forked-context>) follow the same discovery step, but their instructions and any files they read are loaded into a separate subagent. Only the skill's final result is returned to the parent agent.

## Use shared skills

You can use skills created by others to enhance Copilot's capabilities. The [github/awesome-copilot](<https://github.com/github/awesome-copilot>) repository contains a growing community collection of skills, custom agents, instructions, and prompts. The [anthropics/skills](<https://github.com/anthropics/skills>) repository contains additional reference skills.

You can also discover and install skills that are bundled in [agent plugins](<https://code.visualstudio.com/docs/agent-customization/agent-plugins>). Skills from installed plugins appear alongside your locally defined skills in the **Configure Skills** menu.

To use a shared skill:

1. Browse the available skills in the repository
2. Copy the skill directory to your `.github/skills/` folder
3. Review and customize the `SKILL.md` file for your needs
4. Optionally, modify or add resources as needed

Tip

Always review shared skills before using them to ensure they meet your requirements and security standards. VS Code's [terminal tool](<https://code.visualstudio.com/docs/agents/run/tools#_run-terminal-commands>) provides controls for script execution, including [auto-approve options](<https://code.visualstudio.com/docs/agents/run/approvals#_automatically-approve-terminal-commands>) with configurable allow-lists and tight controls over which code runs. Learn more about [security considerations](<https://code.visualstudio.com/docs/agents/run/security#_approvals-and-review>) for auto-approval features.

## Contribute skills from extensions

Extensions can contribute skills using the `chatSkills` contribution point in their `package.json`. The path must point to a directory that contains a `SKILL.md` file, following the [Agent Skills specification](<https://agentskills.io/specification>).

### Required folder structure

The skill directory must follow this structure:

```
extension-root/
└── skills/
    └── my-skill/           # Directory name must match the `name` field in SKILL.md
        └── SKILL.md         # Required
```

### Register the skill in package.json

Add the `chatSkills` contribution point in your extension's `package.json`. The `path` property must point to the corresponding `SKILL.md` file:

```
{
  "contributes": {
    "chatSkills": [
      {
        "path": "./skills/my-skill/SKILL.md"
      }
    ]
  }
}
```

Important

The `name` field in the `SKILL.md` frontmatter must match the parent directory name. For example, if the directory is `skills/my-skill/`, the `name` field must be `my-skill`. If the name does not match, the skill is not loaded.

The `SKILL.md` file follows the same format as [project and personal skills](<#_create-a-skill>). For example:

```
---
name: my-skill
description: Description of what the skill does and when to use it.
---

# My Skill

Detailed instructions for the skill...
```

## Agent Skills standard

Agent Skills is an open standard that enables portability across different AI agents. Skills you create in VS Code work with multiple agents, including:

* **GitHub Copilot in VS Code**: Available in chat and agent mode
* **GitHub Copilot CLI**: Accessible when working in the terminal
* **GitHub Copilot app**: Available in the dedicated desktop experience.
* **Copilot cloud agent**: Used during automated coding tasks
* **OpenAI Codex through Agent Host (Experimental)**: Discovers workspace skills in `.github/skills/` after you [set up the integration](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_codex>).

Learn more about the Agent Skills standard at [agentskills.io](<https://agentskills.io>).

## Related resources

* [Customize AI responses overview](<https://code.visualstudio.com/docs/agent-customization/overview>)
* [Create custom instructions](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>)
* [Create reusable prompt files](<https://code.visualstudio.com/docs/agent-customization/prompt-files>)
* [Create custom agents](<https://code.visualstudio.com/docs/agent-customization/custom-agents>)
* [Agent Skills specification](<https://agentskills.io>)
* [Reference skills repository](<https://github.com/anthropics/skills>)
* [Discover and manage agent plugins](<https://code.visualstudio.com/docs/agent-customization/agent-plugins>)

10/7/2026


</details>
