# 为代码库配置 AI 的操作指南

从具体项目问题和基线任务入手，创建检查指令、验证改进、共享配置，并按需要增加技能与专门 Agent。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/customize-copilot-guide.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/customize-copilot-guide.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/为代码库配置 AI 的操作指南.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 2e8a4b9c-3d1f-5e7a-9c2b-4f6d8e1a3b5c
DateApproved: 10/7/2026
MetaDescription: Configure project instructions, skills, and custom agents for multiple harnesses in {% data variables.product.prodname_vscode_shortname %}.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- customization
- instructions
- custom agents
- skills
- copilot
- claude
- codex
- ai
- tutorial
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 为您的代码库配置 AI

AI agents 当他们了解您的代码库的结构、要运行哪些命令以及要遵循哪些约定时，可以产生更好的结果。将此信息配置为存储库自定义一次，而不是在每个提示中重复它。

本指南可帮助您在 {% data variables.product.prodname_vscode %} 中配置 {% data variables.product.prodname_copilot %}、{% data variables.product.prodname_anthropic_claude %} 或 {% data variables.product.prodname_openai_codex %} 线束。从观察到的项目问题和代表性任务开始。进行最小的有用自定义，确认其适用，重复任务，并在共享配置之前比较结果。工作流程是共享的，带有用于特定于线束的格式和行为的选项卡。

要了解自定义类型如何不同以及如何协同工作，请参阅[Agent自定义](<https://github.com/docs/agents/concepts/customization.md>)。

## 先决条件

* [下载并安装{% data variables.product.prodname_vscode %}](<https://github.com/download>)。
* 完成 [为您的安全带进行设置和身份验证](<https://github.com/docs/agents/run/agent-harnesses.md#configure-an-agent-harness>).
* 打开您要配置的存储库根目录。
* [启动会话](<https://github.com/docs/agents/run/agent-harnesses.md#start-a-session>)并从**会话目标**控件中选择**{% data variables.product.prodname_copilot_short %}**、**Claude**或**Codex**。创建和测试其定制时使用相同的线束。

对于本演练，请在当前文件夹中工作。如果您使用 {% data variables.copilot.agents_window %}，请保留 **New Worktree** 未选择，以便新会话可以访问您未提交的自定义文件。

选择适合您的 **harness** 的选项卡，而不是您的语言模型。例如，{% data variables.product.prodname_copilot_short %} 会话中的 Claude 模型仍使用 {% data variables.product.prodname_copilot_short %} 线束。

> [!NOTE]
> **Local** 工具也支持本指南中显示的 {% data variables.product.prodname_copilot_short %} 文件布局，但工具名称和某些激活行为有所不同。对于本地特定配置，请参阅[自定义说明](<https://github.com/docs/agent-customization/custom-instructions.md>)、[agent skills](<https://github.com/docs/agent-customization/agent-skills.md>)和[自定义agents](<https://github.com/docs/agent-customization/custom-agents.md>)。

## 建立基线

选择一个重复出现的特定于项目的问题，例如使用错误的测试命令、将文件放在错误的目录中或选择项目不使用的库。一次性需求属于当前请求而不是共享项目配置。

定义一个具有明确成功标准的小型代表性任务。记录当前结果或使用最近任务的证据：

* agent 创建或更改哪些文件。
* 它遵循哪些项目模式和库。
* 它运行哪些命令和测试，包括失败或跳过的检查。
* 您必须提供哪些更正。

如果结果已经满足您的成功标准，请停止。您不需要针对该行为进行自定义。对于编码任务，请参阅[添加功能指南](<https://github.com/docs/agents/guides/add-a-feature.md>)以获取有关建立基线测试和验证结果的指导。

## 步骤1：创建项目指令

从项目说明文件开始，获取适用于您的代码库的信息。将初始更改集中在 agent 无法从存储库可靠推断出的观察到的问题和决策上。每个线束都有自己的发现规则，因此请使用其预期的文件名和位置。

1. 打开所选线束的 [Agent 自定义编辑器 ](<https://github.com/docs/agent-customization/overview.md#agent-customizations-editor>)。在 {% data variables.copilot.chat_view %} 中，运行 **Chat：从命令面板 (`kb(workbench.action.showCommands)`) 打开自定义 **。

1. 在 ** 概述 ** 部分中，在下面输入您的安全带的提示，然后按 `kbstyle(Enter)` 提交。

1. Continue 在打开的聊天中。确认它使用预期的工具和存储库，并回答任何澄清问题。

1. 查看更改并将说明保存在预期位置。如果该文件已存在，请查看更新，而不是在不检查的情况下替换现有指南。

{% tabs id="customization-harness" %}
{% 标签标签="{% data variables.product.prodname_copilot_short %}" %}

在存储库根目录中使用 `.github/copilot-instructions.md` 来获取项目范围的说明。

```prompt
Analyze this codebase and create or update .github/copilot-instructions.md with project-wide instructions. Cover architecture, important directories, build and test commands, coding conventions, and requirements for completing a change. Preserve useful existing guidance, avoid duplication, and ask about anything you cannot determine from the repository.
```

{% /tab %}
{% tab label="Claude" %}

在存储库根目录中使用 `CLAUDE.md` 来获取项目范围的说明。

```prompt
Analyze this codebase and create or update CLAUDE.md at the repository root with project-wide instructions. Cover architecture, important directories, build and test commands, coding conventions, and requirements for completing a change. Preserve useful existing guidance, avoid duplication, and ask about anything you cannot determine from the repository.
```

{% /tab %}
{% tab label="Codex" %}

在存储库根目录中使用 `AGENTS.md` 来获取项目范围的说明。

```prompt
Analyze this codebase and create or update AGENTS.md at the repository root with project-wide instructions. Cover architecture, important directories, build and test commands, coding conventions, and requirements for completing a change. Preserve useful existing guidance, avoid duplication, and ask about anything you cannot determine from the repository.
```

{% /tab %}
{% /tabs %}

> [！TIP]
> 项目指令在记录agent无法仅从代码中可靠推断的决策时最有用。避免适用于任何项目的通用建议。

## 步骤 2：查看生成的指令

将生成的指令视为起点。检查它们是否包含准确的、特定于项目的信息：

* **观察到的问题**：仅包括改进基线行为所需的指导。
* **架构**：描述重要的目录、组件边界以及在哪里添加不同类型的代码。
* **命令**：包括正确的构建、测试、lint 和格式化命令。
* **技术选择**：确定首选框架、库和模式。
* **约定**：捕获命名、错误处理、测试、安全和文档要求。
* **完成的定义**：说明在完成任务之前应运行agent的验证。

删除文件中其他地方通用、过时或重复的信息。解决相互冲突的指令并保持每条规则的简洁。

例如，特定于项目的说明可能包括：

```markdown
## Project structure

* Add API routes under `src/api/routes`.
* Put shared validation schemas in `src/schemas`.
* Keep database access in the repository layer.

## Validation

* Run the unit tests for the changed package.
* Run the linter before completing a code change.
* Add or update tests for every behavior change.
```

了解有关编写有效的 [自定义指令 ](<https://github.com/docs/agent-customization/custom-instructions.md>) 的更多信息。

<a name="step-3-verify-the-improvement"></a>

## 步骤 3：验证改进

确认说明适用，然后从您的基线重复代表性任务。在可行的情况下保持线束、模型、工具、任务和相关上下文相同，以便定制是主要的更改。

1. 在 Agent 自定义编辑器中，为您的线束选择 ** 指令 ** 并确认列出了项目指令文件。这会检查发现，而不是检查 agent 是否遵循每条指令。
1. 使用相同的工具和存储库开始新的聊天，以便比较不依赖于创建对话的指导。
1. 展开响应中的 ** 参考文献 ** 并确认包含预期的指令。
1. 以相同的成功标准重复代表性任务。
1. 将结果与基线进行比较。检查文件放置、项目模式、库、工具活动、命令、测试和错误，而不是依赖 agent 的摘要。

如果文件丢失、无效或包含但未遵循，请使用 [agent 自定义故障排除指南 ](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md>) 来确定故障，然后再添加更多说明。

如果包含了说明，但结果仍然未达到成功标准，请澄清一条相关规则并在另一个新聊天中重复该任务。当任务满足标准时停止。更多指令会消耗上下文，并且可能会引入冲突，而不会改善结果。

要进行更广泛的受控比较，包括质量、可靠性、信用、令牌、持续时间和工具调用，请使用 [ 使用优化循环 ](<https://github.com/docs/agents/guides/optimize-usage.md#inspect-token-usage-and-caching>)。

## 步骤4：共享配置

提交项目说明文件并通过存储库的正常拉取请求或审核流程共享它。获取更新文件并使用兼容工具的贡献者可以重复使用该指南，而无需重新创建它。

像其他开发配置一样查看说明。使用您现有的所有权和审查流程来明确文件的责任。当架构、命令、依赖项或团队实践发生变化时进行更新，并删除陈旧或重复的规则。

如果您的团队使用多个工具，请保持共享指南的一致性并避免相互矛盾的副本。一个线束支持的文件不会自动受到所有其他线束的支持。

如果代表性任务现在满足成功标准，则停止。您的存储库有一个有用的基线自定义。其余步骤是可选的。当代码库的不同部分需要不同的指导或当您的团队重复执行相同的工作流程时，添加它们。

<a name="step-5-add-targeted-instructions"></a>

## 步骤5：添加针对性指令（可选）

当代码库的不同部分需要不同的指导时，添加有针对性的说明。例如，前端代码和基础设施代码可能遵循不同的约定。将项目范围的标准保留在步骤 1 的说明文件中。

在 Agent 自定义编辑器的 ** 概述 ** 部分中，提交请求以在下面为您的线束创建文件。描述它应该涵盖的约定并使用存储库中的现有文件类型或目录。

{% tabs id="customization-harness" %}
{% 标签标签="{% data variables.product.prodname_copilot_short %}" %}

创建`.github/instructions/frontend.instructions.md`。对于 TypeScript React 文件，请在 Markdown 说明之前包含此 YAML frontmatter：

```yaml
---
applyTo: "**/*.tsx"
---
```

该模式与整个存储库中的 `.tsx` 文件匹配。通过要求 agent 对特定匹配文件进行小更改来检查模式并测试说明。

了解有关 [基于文件的指令 ](<https://github.com/docs/agent-customization/custom-instructions.md#use-instructionsmd-files>) 的更多信息。

{% /tab %}
{% tab label="Claude" %}

创建`.claude/rules/frontend.md`。 Claude 使用 `paths` 而不是 `applyTo` 作为路径范围的规则：

```yaml
---
paths:
  - "**/*.tsx"
---
```

通过要求 agent 对特定匹配文件进行小更改来检查模式并测试规则。如果没有 `paths`，则规则文件普遍适用，而不仅仅是匹配文件。

了解有关 [Claude 路径特定规则 ](<https://code.claude.com/docs/en/memory#path-specific-rules>) 的更多信息。

{% /tab %}
{% tab label="Codex" %}

在需要自己引导的目录中使用`AGENTS.md`文件，例如`packages/frontend/AGENTS.md`。 Codex 发现从存储库根目录到会话工作目录的指令。这是基于目录的指导，而不是 `applyTo` 或 `paths` glob。

要测试嵌套指令，请启动一个新的 Codex 会话，并将该子目录作为其工作文件夹。在编辑器中打开文件不会更改会话的工作目录。

了解有关 [Codex 指令发现 ](<https://developers.openai.com/codex/guides/agents-md>) 的更多信息。

{% /tab %}
{% /tabs %}

查看并保存生成的文件，然后使用相同的工具在新聊天中对其进行测试。确认结果遵循目标约定。如果没有，请在修改说明之前检查发现和范围。

如果您更改了会话的工作文件夹来测试嵌套指令，请在继续之前返回到存储库根目录。

<a name="step-6-package-a-recurring-workflow"></a>

## 步骤 6：打包重复工作流程（可选）

当您的团队重复解释相同的多步骤流程时，创建 agent 技能。技能可以包括 agent 相关时加载的指令、脚本、模板、示例和其他资源。

例如，创建一个 `validate-change` 技能，选择并运行代码更改的相关检查，然后报告通过、失败或未运行的内容。在 Agent 自定义编辑器的 ** 概述 ** 部分中提交针对您的线束的提示：

{% tabs id="customization-harness" %}
{% 标签标签="{% data variables.product.prodname_copilot_short %}" %}

```prompt
Create a workspace skill named validate-change at .github/skills/validate-change/SKILL.md. It should inspect a code change, choose and run the relevant tests and lint checks using this repository's commands, and report what passed, failed, or was not run. Describe when to use it in its frontmatter.
```

{% /tab %}
{% tab label="Claude" %}

```prompt
Create a workspace skill named validate-change at .claude/skills/validate-change/SKILL.md. It should inspect a code change, choose and run the relevant tests and lint checks using this repository's commands, and report what passed, failed, or was not run. Describe when to use it in its frontmatter.
```

{% /tab %}
{% tab label="Codex" %}

```prompt
Create a workspace skill named validate-change at .agents/skills/validate-change/SKILL.md. It should inspect a code change, choose and run the relevant tests and lint checks using this repository's commands, and report what passed, failed, or was not run. Describe when to use it in its frontmatter.
```

{% /tab %}
{% /tabs %}

生成技能后：

1. 查看 `SKILL.md` 文件。检查其 `name` 是否与目录名称匹配，其 `description` 解释了它的作用以及何时使用它。
1. 查看技能目录中的所有脚本、模板或其他资源，然后保存更改。
1. 使用相同的工具和存储库开始新的聊天。在聊天输入中键入 `/` 并选择 **validate-change** 以显式调用该技能。识别要验证的代码更改。
1. 查看工具活动和结果。检查 agent 是否加载技能、选择适当的检查并准确报告失败或跳过的检查。

您还可以通过要求 agent 验证更改而不命名技能，在单独的聊天中测试自动选择。如果未选择技能，请检查描述和调用设置。显式调用和模型选择使用是单独的检查。

了解有关 [agent skills](<https://github.com/docs/agent-customization/agent-skills.md>) 的更多信息。

<a name="step-7-add-a-specialized-agent"></a>

## 步骤7：添加专门的agent（可选）

当角色需要集中指令时，创建自定义 agent。例如，代码库研究人员可以在您决定更改内容之前调查实施细节并报告结果。工具限制取决于工具，因此不要假设角色描述本身会阻止编辑。

在 Agent 自定义编辑器的 ** 概述 ** 部分中，提交针对您的线束的提示：

{% tabs id="customization-harness" %}
{% 标签标签="{% data variables.product.prodname_copilot_short %}" %}

```prompt
Create a workspace custom agent named researcher at .github/agents/researcher.agent.md. It should trace how features work and report findings with file locations, without changing the repository. Configure its tools to use only the {% data variables.product.prodname_copilot_short %} harness's file-reading and code-search tools. Exclude tools that can edit files, run shell commands, or delegate to other agents.
```

查看生成文件中的 `tools` 白名单。确认它仅包含预期的阅读和搜索工具，而不是不受限制的工具访问。

{% /tab %}
{% tab label="Claude" %}

```prompt
Create a workspace custom agent named researcher at .claude/agents/researcher.md. It should trace how features work and report findings with file locations, without changing the repository. Restrict its tools to Read, Grep, and Glob.
```

查看生成的 agent 的 `tools` frontmatter，以确认它将 agent 限制为这些阅读和搜索工具。了解有关 [Claude 自定义 agents 和工具访问 ](<https://code.claude.com/docs/en/sub-agents#available-tools>) 的更多信息。

{% /tab %}
{% tab label="Codex" %}

```prompt
Create a workspace custom agent named researcher at .github/agents/researcher.agent.md for Codex in {% data variables.product.prodname_vscode_shortname %}. It should trace how features work and report findings with file locations. Instruct it not to edit files or run commands that change the repository.
```

{% data variables.product.prodname_vscode_shortname %} 中的 Codex 集成从该文件加载角色指令，但不使用其 `tools` frontmatter 作为工具白名单。将禁止编辑指令视为指导，而不是强制执行。使用agent之前，请检查[Codex权限](<https://github.com/docs/agents/run/agent-harnesses.md#codex>)。

{% /tab %}
{% /tabs %}

生成agent后：

1. 检查并保存文件。检查说明是否定义了明确的角色，并且不与您的项目指南相冲突。
1. 使用相同的工具和存储库开始新的聊天，然后从 agents 下拉列表中选择 ** 研究员 **。
1. 要求它研究某个功能的工作原理。检查它是否识别相关文件、用证据支持调查结果并遵循其配置的角色。
1. 检查任何更改和工具活动，而不是假设 agent 的名称或说明使其只读。

了解有关 [ 自定义 agents](<https://github.com/docs/agent-customization/custom-agents.md>) 的更多信息。

## 你配置了什么

如果您完成了可选步骤，您的存储库可以包含所选线束的以下文件。前端目录是一个例子；使用存储库的结构。

{% tabs id="customization-harness" %}
{% 标签标签="{% data variables.product.prodname_copilot_short %}" %}

```text
your-project/
  .github/
    copilot-instructions.md
    instructions/
      frontend.instructions.md
    skills/
      validate-change/
        SKILL.md
    agents/
      researcher.agent.md
```

{% /tab %}
{% tab label="Claude" %}

```text
your-project/
  CLAUDE.md
  .claude/
    rules/
      frontend.md
    skills/
      validate-change/
        SKILL.md
    agents/
      researcher.md
```

{% /tab %}
{% tab label="Codex" %}

```text
your-project/
  AGENTS.md
  packages/
    frontend/
      AGENTS.md
  .agents/
    skills/
      validate-change/
        SKILL.md
  .github/
    agents/
      researcher.agent.md
```

{% /tab %}
{% /tabs %}

项目说明提供了基线。有针对性的指令集中指导部分代码库。技能包重复工作流程和自定义 agents 定义具有特定于线束的功能的专门角色。随着存储库需求的增长，您可以独立采用每一层。

通过与项目说明相同的流程查看和共享任何可选的自定义文件。包括技能的支持资源，以便其他贡献者可以使用它。

## 后续步骤

* [从 Agent 自定义编辑器创建和管理自定义](<https://github.com/docs/agent-customization/overview.md>)。
* 添加[MCP服务器](<https://github.com/docs/agent-customization/mcp-servers.md>)以将agent连接到外部工具和服务。
* 当操作必须在特定的 agent 生命周期事件中运行时，设置 [ 挂钩 ](<https://github.com/docs/agent-customization/hooks.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Configure AI for your codebase

AI agents can produce better results when they understand how your codebase is structured, which commands to run, and which conventions to follow. Configure this information once as repository customizations instead of repeating it in every prompt.

This guide helps you configure the {% data variables.product.prodname_copilot %}, {% data variables.product.prodname_anthropic_claude %}, or {% data variables.product.prodname_openai_codex %} harness in {% data variables.product.prodname_vscode %}. Start with an observed project problem and a representative task. Make the smallest useful customization, confirm that it applies, repeat the task, and compare the results before you share the configuration. The workflow is shared, with tabs for harness-specific formats and behavior.

To understand how the customization types differ and work together, see [Agent customization](<https://github.com/docs/agents/concepts/customization.md>).

## Prerequisites

* [Download and install {% data variables.product.prodname_vscode %}](<https://github.com/download>).
* Complete the [setup and authentication for your harness](<https://github.com/docs/agents/run/agent-harnesses.md#configure-an-agent-harness>).
* Open the repository root that you want to configure.
* [Start a session](<https://github.com/docs/agents/run/agent-harnesses.md#start-a-session>) and select **{% data variables.product.prodname_copilot_short %}**, **Claude**, or **Codex** from the **Session Target** control. Use the same harness when creating and testing its customizations.

For this walkthrough, work in the current folder. If you're using the {% data variables.copilot.agents_window %}, leave **New Worktree** unselected so new sessions can access your uncommitted customization files.

Choose the tabs for your **harness**, not your language model. For example, a Claude model in a {% data variables.product.prodname_copilot_short %} session still uses the {% data variables.product.prodname_copilot_short %} harness.

> [!NOTE]
> The **Local** harness also supports the {% data variables.product.prodname_copilot_short %} file layout shown in this guide, but tool names and some activation behavior differ. For Local-specific configuration, see [custom instructions](<https://github.com/docs/agent-customization/custom-instructions.md>), [agent skills](<https://github.com/docs/agent-customization/agent-skills.md>), and [custom agents](<https://github.com/docs/agent-customization/custom-agents.md>).

## Establish a baseline

Choose one project-specific problem that occurs repeatedly, such as using the wrong test command, placing files in the wrong directory, or selecting a library that your project doesn't use. A one-time requirement belongs in the current request rather than in shared project configuration.

Define a small representative task with a clear success criterion. Record the current result or use evidence from a recent task:

* Which files the agent creates or changes.
* Which project patterns and libraries it follows.
* Which commands and tests it runs, including failures or skipped checks.
* Which corrections you have to provide.

If the result already meets your success criterion, stop. You don't need a customization for that behavior. For a coding task, see the [add a feature guide](<https://github.com/docs/agents/guides/add-a-feature.md>) for guidance on establishing baseline tests and verifying the outcome.

## Step 1: Create project instructions

Start with a project instructions file for information that applies across your codebase. Focus the initial change on the observed problem and decisions the agent can't reliably infer from the repository. Each harness has its own discovery rules, so use its expected file name and location.

1. Open the [Agent Customizations editor](<https://github.com/docs/agent-customization/overview.md#agent-customizations-editor>) for the selected harness. In the {% data variables.copilot.chat_view %}, run **Chat: Open Customizations** from the Command Palette (`kb(workbench.action.showCommands)`).

1. In the **Overview** section, enter the prompt for your harness below and press `kbstyle(Enter)` to submit it.

1. Continue in the chat that opens. Confirm that it uses the intended harness and repository, and answer any clarifying questions.

1. Review the changes and save the instructions in the expected location. If the file already exists, review the update rather than replacing existing guidance without checking it.

{% tabs id="customization-harness" %}
{% tab label="{% data variables.product.prodname_copilot_short %}" %}

Use `.github/copilot-instructions.md` at the repository root for project-wide instructions.

```prompt
Analyze this codebase and create or update .github/copilot-instructions.md with project-wide instructions. Cover architecture, important directories, build and test commands, coding conventions, and requirements for completing a change. Preserve useful existing guidance, avoid duplication, and ask about anything you cannot determine from the repository.
```

{% /tab %}
{% tab label="Claude" %}

Use `CLAUDE.md` at the repository root for project-wide instructions.

```prompt
Analyze this codebase and create or update CLAUDE.md at the repository root with project-wide instructions. Cover architecture, important directories, build and test commands, coding conventions, and requirements for completing a change. Preserve useful existing guidance, avoid duplication, and ask about anything you cannot determine from the repository.
```

{% /tab %}
{% tab label="Codex" %}

Use `AGENTS.md` at the repository root for project-wide instructions.

```prompt
Analyze this codebase and create or update AGENTS.md at the repository root with project-wide instructions. Cover architecture, important directories, build and test commands, coding conventions, and requirements for completing a change. Preserve useful existing guidance, avoid duplication, and ask about anything you cannot determine from the repository.
```

{% /tab %}
{% /tabs %}

> [!TIP]
> Project instructions are most useful when they document decisions the agent cannot reliably infer from the code alone. Avoid generic advice that applies to any project.

## Step 2: Review the generated instructions

Treat generated instructions as a starting point. Check that they contain accurate, project-specific information:

* **Observed problem**: include only the guidance needed to improve the baseline behavior.
* **Architecture**: describe important directories, component boundaries, and where to add different types of code.
* **Commands**: include the correct build, test, lint, and formatting commands.
* **Technology choices**: identify preferred frameworks, libraries, and patterns.
* **Conventions**: capture naming, error-handling, testing, security, and documentation requirements.
* **Definition of done**: state which validations the agent should run before completing a task.

Remove information that is generic, obsolete, or duplicated elsewhere in the file. Resolve conflicting instructions and keep each rule concise.

For example, project-specific instructions might include:

```markdown
## Project structure

* Add API routes under `src/api/routes`.
* Put shared validation schemas in `src/schemas`.
* Keep database access in the repository layer.

## Validation

* Run the unit tests for the changed package.
* Run the linter before completing a code change.
* Add or update tests for every behavior change.
```

Learn more about writing effective [custom instructions](<https://github.com/docs/agent-customization/custom-instructions.md>).

<a name="step-3-verify-the-improvement"></a>

## Step 3: Verify the improvement

Confirm that the instructions apply, and then repeat the representative task from your baseline. Keep the harness, model, tools, task, and relevant context the same where practical so that the customization is the main change.

1. In the Agent Customizations editor, select **Instructions** for your harness and confirm that the project instructions file is listed. This checks discovery, not whether the agent follows every instruction.
1. Start a new chat with the same harness and repository so the comparison doesn't rely on guidance from the creation conversation.
1. Expand **References** in the response and confirm that the expected instructions were included.
1. Repeat the representative task with the same success criterion.
1. Compare the result with the baseline. Review file placement, project patterns, libraries, tool activity, commands, tests, and errors rather than relying on the agent's summary.

If the file is missing, invalid, or included but not followed, use the [agent customization troubleshooting guide](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md>) to identify the failure before adding more instructions.

If the instructions are included but the result still misses the success criterion, clarify one relevant rule and repeat the task in another new chat. Stop when the task meets the criterion. More instructions consume context and can introduce conflicts without improving the result.

For a broader controlled comparison that includes quality, reliability, credits, tokens, duration, and tool calls, use the [usage optimization loop](<https://github.com/docs/agents/guides/optimize-usage.md#inspect-token-usage-and-caching>).

## Step 4: Share the configuration

Commit the project instructions file and share it through your repository's normal pull request or review process. Contributors who obtain the updated files and use a compatible harness can reuse the guidance without recreating it.

Review the instructions like other development configuration. Use your existing ownership and review process to make responsibility for the file clear. Update it when the architecture, commands, dependencies, or team practices change, and remove stale or duplicate rules.

If your team uses multiple harnesses, keep shared guidance consistent and avoid contradictory copies. A file supported by one harness isn't automatically supported by every other harness.

If the representative task now meets the success criterion, stop. Your repository has a useful baseline customization. The remaining steps are optional. Add them when different parts of the codebase need distinct guidance or when your team repeatedly performs the same workflow.

<a name="step-5-add-targeted-instructions"></a>

## Step 5: Add targeted instructions (optional)

Add targeted instructions when different parts of your codebase need different guidance. For example, frontend code and infrastructure code might follow different conventions. Keep project-wide standards in the instructions file from step 1.

In the Agent Customizations editor's **Overview** section, submit a request to create the file for your harness below. Describe the conventions it should cover and use an existing file type or directory from your repository.

{% tabs id="customization-harness" %}
{% tab label="{% data variables.product.prodname_copilot_short %}" %}

Create `.github/instructions/frontend.instructions.md`. For TypeScript React files, include this YAML frontmatter before the Markdown instructions:

```yaml
---
applyTo: "**/*.tsx"
---
```

The pattern matches `.tsx` files throughout the repository. Review the pattern and test the instructions by asking the agent to make a small change to a specific matching file.

Learn more about [file-based instructions](<https://github.com/docs/agent-customization/custom-instructions.md#use-instructionsmd-files>).

{% /tab %}
{% tab label="Claude" %}

Create `.claude/rules/frontend.md`. Claude uses `paths` rather than `applyTo` for path-scoped rules:

```yaml
---
paths:
  - "**/*.tsx"
---
```

Review the pattern and test the rules by asking the agent to make a small change to a specific matching file. Without `paths`, the rule file applies generally rather than only to matching files.

Learn more about [Claude path-specific rules](<https://code.claude.com/docs/en/memory#path-specific-rules>).

{% /tab %}
{% tab label="Codex" %}

Use an `AGENTS.md` file in the directory that needs its own guidance, such as `packages/frontend/AGENTS.md`. Codex discovers instructions from the repository root down to the session's working directory. This is directory-based guidance, not an `applyTo` or `paths` glob.

To test the nested instructions, start a new Codex session with that subdirectory as its working folder. Opening a file in the editor doesn't change the session's working directory.

Learn more about [Codex instruction discovery](<https://developers.openai.com/codex/guides/agents-md>).

{% /tab %}
{% /tabs %}

Review and save the generated file, then test it in a new chat with the same harness. Confirm that the result follows the targeted conventions. If it doesn't, check discovery and scope before revising the instructions.

If you changed the session's working folder to test nested instructions, return to the repository root before continuing.

<a name="step-6-package-a-recurring-workflow"></a>

## Step 6: Package a recurring workflow (optional)

Create an agent skill when your team repeatedly explains the same multi-step process. A skill can include instructions, scripts, templates, examples, and other resources that the agent loads when relevant.

For example, create a `validate-change` skill that selects and runs the relevant checks for a code change, then reports what passed, failed, or wasn't run. Submit the prompt for your harness in the Agent Customizations editor's **Overview** section:

{% tabs id="customization-harness" %}
{% tab label="{% data variables.product.prodname_copilot_short %}" %}

```prompt
Create a workspace skill named validate-change at .github/skills/validate-change/SKILL.md. It should inspect a code change, choose and run the relevant tests and lint checks using this repository's commands, and report what passed, failed, or was not run. Describe when to use it in its frontmatter.
```

{% /tab %}
{% tab label="Claude" %}

```prompt
Create a workspace skill named validate-change at .claude/skills/validate-change/SKILL.md. It should inspect a code change, choose and run the relevant tests and lint checks using this repository's commands, and report what passed, failed, or was not run. Describe when to use it in its frontmatter.
```

{% /tab %}
{% tab label="Codex" %}

```prompt
Create a workspace skill named validate-change at .agents/skills/validate-change/SKILL.md. It should inspect a code change, choose and run the relevant tests and lint checks using this repository's commands, and report what passed, failed, or was not run. Describe when to use it in its frontmatter.
```

{% /tab %}
{% /tabs %}

After generating the skill:

1. Review the `SKILL.md` file. Check that its `name` matches the directory name and its `description` explains what it does and when to use it.
1. Review any scripts, templates, or other resources in the skill directory, and save your changes.
1. Start a new chat with the same harness and repository. Type `/` in the chat input and select **validate-change** to invoke the skill explicitly. Identify the code change to validate.
1. Review the tool activity and results. Check that the agent loads the skill, selects appropriate checks, and reports failures or skipped checks accurately.

You can also test automatic selection in a separate chat by asking the agent to validate a change without naming the skill. If it doesn't select the skill, review the description and invocation settings. Explicit invocation and model-selected use are separate checks.

Learn more about [agent skills](<https://github.com/docs/agent-customization/agent-skills.md>).

<a name="step-7-add-a-specialized-agent"></a>

## Step 7: Add a specialized agent (optional)

Create a custom agent when a role needs focused instructions. For example, a codebase researcher can investigate implementation details and report findings before you decide what to change. Tool restrictions depend on the harness, so don't assume a role description alone prevents edits.

In the Agent Customizations editor's **Overview** section, submit the prompt for your harness:

{% tabs id="customization-harness" %}
{% tab label="{% data variables.product.prodname_copilot_short %}" %}

```prompt
Create a workspace custom agent named researcher at .github/agents/researcher.agent.md. It should trace how features work and report findings with file locations, without changing the repository. Configure its tools to use only the {% data variables.product.prodname_copilot_short %} harness's file-reading and code-search tools. Exclude tools that can edit files, run shell commands, or delegate to other agents.
```

Review the `tools` allowlist in the generated file. Confirm that it contains only the intended reading and search tools, rather than unrestricted tool access.

{% /tab %}
{% tab label="Claude" %}

```prompt
Create a workspace custom agent named researcher at .claude/agents/researcher.md. It should trace how features work and report findings with file locations, without changing the repository. Restrict its tools to Read, Grep, and Glob.
```

Review the generated agent's `tools` frontmatter to confirm that it limits the agent to those reading and search tools. Learn more about [Claude custom agents and tool access](<https://code.claude.com/docs/en/sub-agents#available-tools>).

{% /tab %}
{% tab label="Codex" %}

```prompt
Create a workspace custom agent named researcher at .github/agents/researcher.agent.md for Codex in {% data variables.product.prodname_vscode_shortname %}. It should trace how features work and report findings with file locations. Instruct it not to edit files or run commands that change the repository.
```

The Codex integration in {% data variables.product.prodname_vscode_shortname %} loads the role instructions from this file, but doesn't use its `tools` frontmatter as a tool allowlist. Treat the no-edit instruction as guidance, not enforcement. Review [Codex permissions](<https://github.com/docs/agents/run/agent-harnesses.md#codex>) before using the agent.

{% /tab %}
{% /tabs %}

After generating the agent:

1. Review and save the file. Check that the instructions define a clear role and don't conflict with your project guidance.
1. Start a new chat with the same harness and repository, and select **researcher** from the agents dropdown.
1. Ask it to research how a feature works. Check that it identifies relevant files, supports findings with evidence, and follows its configured role.
1. Review any changes and tool activity rather than assuming the agent's name or instructions make it read-only.

Learn more about [custom agents](<https://github.com/docs/agent-customization/custom-agents.md>).

## What you configured

If you completed the optional steps, your repository can contain the following files for your selected harness. The frontend directory is an example; use the structure of your repository.

{% tabs id="customization-harness" %}
{% tab label="{% data variables.product.prodname_copilot_short %}" %}

```text
your-project/
  .github/
    copilot-instructions.md
    instructions/
      frontend.instructions.md
    skills/
      validate-change/
        SKILL.md
    agents/
      researcher.agent.md
```

{% /tab %}
{% tab label="Claude" %}

```text
your-project/
  CLAUDE.md
  .claude/
    rules/
      frontend.md
    skills/
      validate-change/
        SKILL.md
    agents/
      researcher.md
```

{% /tab %}
{% tab label="Codex" %}

```text
your-project/
  AGENTS.md
  packages/
    frontend/
      AGENTS.md
  .agents/
    skills/
      validate-change/
        SKILL.md
  .github/
    agents/
      researcher.agent.md
```

{% /tab %}
{% /tabs %}

Project instructions provide the baseline. Targeted instructions focus guidance on part of the codebase. Skills package recurring workflows, and custom agents define specialized roles with harness-specific capabilities. You can adopt each layer independently as your repository's needs grow.

Review and share any optional customization files through the same process as the project instructions. Include a skill's supporting resources so other contributors can use it.

## Next steps

* [Create and manage customizations](<https://github.com/docs/agent-customization/overview.md>) from the Agent Customizations editor.
* Add [MCP servers](<https://github.com/docs/agent-customization/mcp-servers.md>) to connect the agent to external tools and services.
* Set up [hooks](<https://github.com/docs/agent-customization/hooks.md>) when an action must run at a specific agent lifecycle event.


</details>
