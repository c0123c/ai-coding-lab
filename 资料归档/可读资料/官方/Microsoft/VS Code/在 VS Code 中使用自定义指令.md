# 在 VS Code 中使用自定义指令

说明全局、仓库、路径级规则的格式、作用范围和激活方式，帮助配置项目规范及开发约定。

- 来源：Microsoft／VS Code
- 资料类型：存档文档
- 文档时间：官方审核：[2026-10-07](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>)；官方文件提交（截至抓取）：[2026-10-07](<https://github.com/microsoft/vscode-docs/commit/eeb6997081a1b674fb894129737e40ffbe7dfc05>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/在 VS Code 中使用自定义指令.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用VS Code中的自定义指令

* [以 Markdown 形式查看](<https://code.visualstudio.com/docs/agent-customization/custom-instructions.md>)
* [在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-instructions%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问
* [在VS Code内部人士中提问](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-instructions%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-instructions%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问

---

自定义指令提供可重用的上下文，帮助 AI 遵循您的编码实践和项目要求。不要在每个聊天提示中重复指南，而是将它们存储在 Markdown 文件中，您可以与团队共享或跨项目重复使用。

本文可帮助您选择指令类型和位置、在应用指令时进行配置，并验证您的 agent 是否使用它们。有关指导设置工作流程，请参阅 [为您的代码库配置 AI](<https://code.visualstudio.com/docs/agents/guides/customize-copilot-guide>)。

指令支持取决于为您的会话选择的 [agent 线束 ](<https://code.visualstudio.com/docs/agents/concepts/agent-harnesses>)。 Agent 主机会话使用所选线束的发现规则和文件格式。本地agent使用本文中描述的VS Code指令设置。

提示

使用 [Agent 自定义编辑器 ](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>) 发现、创建和管理所选 agent 线束的自定义。运行 **Chat：从命令面板打开自定义 **。

注意事项

当您在编辑器中键入时，[内联建议](<https://code.visualstudio.com/docs/editing/ai-powered-suggestions>)不会考虑自定义指令。

## 指令文件的类型

在打开 Agent 自定义编辑器之前选择所需的线束。编辑器显示可用于该线束的自定义设置。

### 选择格式

使用所选线束支持的指令格式。 Local agent 还支持兼容格式。

|安全带|推荐项目说明|有针对性的指示|
| --- | --- | --- |
| Copilot |[`.github/copilot-instructions.md`](<#_use-a-githubcopilot-instructionsmd-file>) 或 [`AGENTS.md`](<#_use-an-agentsmd-file>)| [`.github/instructions/**/*.instructions.md`](<#_use-instructionsmd-files>) |
| Anthropic Claude | [`CLAUDE.md`](<#_use-a-claudemd-file>) |`.claude/rules` 中的 Markdown 文件|
| OpenAI Codex | [`AGENTS.md`](<#_use-an-agentsmd-file>) |`AGENTS.md` 子文件夹中的文件|
|本地|`.github/copilot-instructions.md`、`AGENTS.md` 或 `CLAUDE.md`|`.github/instructions/**/*.instructions.md` 或 `.claude/rules` 中的 Markdown 文件|

`AGENTS.md` 不特定于 Codex。它是一种跨 agent 格式，当您选择的线束支持时，您可以将其用作共享项目说明文件。一些线束还可以识别其他格式。

如果您的团队使用多个工具，请尽可能使用他们都支持的共享格式。当您需要单独的文件时，请保持共享需求一致并避免相互矛盾的副本。

### 选择范围

将指令存储在与您想要的使用和共享方式相匹配的最窄范围内。

|适用范围|用它来|存储|
| --- | --- | --- |
|工作空间|与贡献者共享的项目约定|存储库或会话工作文件夹中受支持的文件夹|
|用户|跨项目的个人偏好|Agent 主机的线束特定用户文件夹，或本地 agent 的 VS Code 配置文件|
|组织机构|支持的 Copilot 会话的集中管理要求|GitHub组织设置|

Agent 主机文件夹中的用户指令（例如 `~/.copilot/instructions` 和 `~/.claude/rules`）不会通过 Settings Sync 漫游。您的 VS Code 个人资料中存储的本地 agent 说明可以通过“设置同步”漫游。

### 选择激活行为

根据所选线束自动包含项目说明。当指南仅适用于特定文件或任务时，请使用有针对性的说明：

* **文件模式**：当`applyTo`模式与agent创建或修改的文件匹配时，VS Code自动附加`.instructions.md`文件。
* **任务相关性**：描述性`description`帮助agent决定是否加载当前任务的指令文件。
* **手册**：将说明文件附加到个人聊天请求。

对于 Claude 规则，请使用 `paths` frontmatter 属性而不是 `applyTo`。

要在说明中引用文件或 URL，请使用 Markdown 链接。相对文件路径从说明文件解析。使用 `/` 路径分隔符来保持指令在操作系统之间的可移植性。

## 使用 `.github/copilot-instructions.md` 文件

对于 Copilot Agent 主持人会话，请使用 `.github/copilot-instructions.md` 获取项目范围的指导。将文件存储在存储库根目录的 `.github` 文件夹中。

本地 agent 也会在以下情况下发现此工作区文件：


github.copilot.聊天.codeGeneration.useInstructionFiles

在 VS Code
 中打开 在 VS Code 内部人员中打开 
 已启用。

使用 `copilot-instructions.md` 用于：

* 适用于整个项目的编码风格和命名约定。
* 技术堆栈声明和首选库。
* 要遵循或避免的架构模式。
* 安全要求和错误处理方法。
* 文档标准。

对于 Copilot Agent 主机会话中的个人始终在线指令，请使用 `~/.copilot/copilot-instructions.md`。

请按照以下步骤在工作区中创建 `.github/copilot-instructions.md` 文件：

1. 在存储库的根目录中创建一个 `.github/copilot-instructions.md` 文件。如果需要，请先创建一个`.github`目录。
2. 在 Markdown 中添加简洁的、特定于项目的说明。

示例：一般编码指南

```
# Project instructions

## Architecture

* Add HTTP handlers under `src/api/routes`.
* Keep database access in `src/repositories` so handlers remain independently testable.

## Validation

* Run `npm test -- <changed-package>` after changing application code.
* Add or update tests for every behavior change.
```

## 使用`.instructions.md`文件

您可以使用 `*.instructions.md` Markdown 文件创建基于文件的说明。 VS Code 自动附加具有与要更改的文件匹配的 `applyTo` 模式的文件。当`description`与当前任务匹配时，agent还可以按需加载指令文件。

使用 `.instructions.md` 文件：

* 前端和后端代码的不同约定。
* monorepo 中特定于语言的指南。
* 特定模块的特定于框架的模式。
* 测试文件或文档的专门规则。

### 说明文件位置

支持的位置取决于会话类型和所选线束。

|届次及范围|默认文件位置|
| --- | --- |
|Agent 主机工作区，Copilot 格式| `.github/instructions` |
|Agent 主机工作区，Claude 格式| `.claude/rules` |
|Agent 主机用户，Copilot 格式| `~/.copilot/instructions` |
|Agent 主机用户，Claude 格式| `~/.claude/rules` |
|本地 agent 工作区|`.github/instructions` 或 `.claude/rules`|
|本地agent用户|VS Code 配置文件存储|

使用 Agent 自定义编辑器在所选线束支持的位置创建用户指令。要将基于配置文件的说明移至 Agent 主机用户文件夹，请使用 [ 用户自定义迁移 ](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_move-user-agents-and-instructions>)。

注意事项

的


聊天。instructionsFilesLocations

在 VS Code
 中打开 在 VS Code 内部人员中打开 
 设置已弃用，仅由本地 agent 使用。如果您使用此设置配置了其他指令位置，[会将自定义迁移到支持的位置](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_move-customizations-from-configured-locations>)。

VS Code 递归搜索这些文件夹，这使您能够在子目录中组织说明文件。例如，您可以按团队、语言或模块对指令进行分组：

```
.github/instructions/
  frontend/
    react.instructions.md
    accessibility.instructions.md
  backend/
    api-design.instructions.md
  testing/
    unit-tests.instructions.md
```

提示

在 monorepo 中，启用


聊天。useCustomizationsInParentRepositories

在 VS Code
中打开 在 VS Code Insiders
 中打开，以发现来自父存储库根的指令。了解有关 [ 父存储库发现 ](<https://code.visualstudio.com/docs/agent-customization/overview#_use-customizations-in-a-monorepo>) 的更多信息。

### 说明文件格式

对于 `.instructions.md` 文件，YAML frontmatter 控制如何发现和应用指令：

|领域|必填|描述|
| --- | --- | --- |
| `name` |否|显示名称显示在UI中。默认为文件名。|
| `description` |否|描述与该文件相关的任务。将其包含在内以进行按需发现。|
| `applyTo` |否|全局模式，自动将指令应用于相对于工作区根目录的匹配文件。使用 `**` 匹配所有文件。|

正文包含 Markdown 格式的说明。

```
---
name: 'Python testing'
description: 'Use when creating or updating Python unit tests.'
applyTo: '**/*.py'
---
# Python testing

* Use `pytest` fixtures from `tests/conftest.py` instead of creating duplicate setup helpers.
* Name tests `test_<behavior>_<condition>`.
* Run `python -m pytest <test-file>` after changing a test.
```

如果您同时省略 `description` 和 `applyTo`，请在要使用时手动附加该文件。对于 Claude 规则，请使用 `paths` 属性而不是 `applyTo`。

### 创建说明文件

创建说明文件时，选择是将其存储在工作区还是用户范围。工作区指令仅适用于该工作区，而用户指令可跨相应线束的多个工作区使用。

要创建说明文件：

1. 选择应使用说明的 agent 线束。
2. 在聊天视图中，选择 ** 配置聊天 **（齿轮图标），或运行 ** 聊天：从命令面板打开自定义 ** (⇧⌘P (Windows、Linux Ctrl+Shift+P))。
3. 在 Agent 自定义编辑器中，选择 ** 说明 **。
4. 从 **New** 下拉列表中，选择工作区或用户指令。
5. 选择支持的位置并输入文件名。
6. 添加 frontmatter 和 Markdown 指令，然后保存文件。

您还可以从命令面板 (⇧⌘P (Windows、Linux Ctrl+Shift+P)) 运行 **Chat：新指令文件 **。

![显示说明部分以及用于在 Agent 自定义编辑器中创建说明文件的菜单的屏幕截图。](<https://code.visualstudio.com/assets/docs/agent-customization/customization/create-instructions-file.png>)

### 使用 AI 生成说明文件

在 Agent 自定义编辑器中，在 ** 概述 ** 部分中输入描述您要创建的指令的请求。确认生成的文件使用所选线束的格式和位置。

对于本地会话，您还可以在聊天中键入 `/create-instructions` 并描述您要执行的约定或准则。要生成项目范围的指令，请键入 `/init`。

使用之前查看生成的说明。根据存储库验证命令、路径和约定。

## 使用 `AGENTS.md` 文件

`AGENTS.md` 是一种跨agent 的项目指导格式。它由多个线束支持，包括 Copilot 和 OpenAI Codex，以及本地 agent。将主文件放置在存储库根目录中。

在以下情况下使用 `AGENTS.md`：

* 您使用多个兼容的 AI 编码 agents 并希望共享一组指令。
* 您需要适用于单一仓库特定部分的子文件夹级说明。

对于本地agent，配置


聊天。useAgentsMdFile

在 VS Code
中打开 在 VS Code Insiders
 设置中打开，以启用或禁用对 `AGENTS.md` 文件的支持。

### 使用多个 `AGENTS.md` 文件

当不同的文件夹需要不同的指导时，请使用嵌套的 `AGENTS.md` 文件。发现和激活取决于所选的线束。

对于本地 agent，请使用


聊天。useNestedAgentsMdFiles

在 VS Code
中打开 在 VS Code Insiders
 设置中打开，以启用或禁用对嵌套 `AGENTS.md` 文件的支持。默认情况下禁用该设置。

启用后，VS Code 会列出嵌套的 `AGENTS.md` 文件及其文件夹位置，以便本地 agent 可以加载任务的相关说明。对于 Agent 主机会话，请遵循所选线束的工作文件夹和嵌套指令规则。

提示

对于特定于文件夹的说明，您还可以使用具有与文件夹结构匹配的不同 `applyTo` 模式的多个 [`.instructions.md`](<#_use-instructionsmd-files>) 文件。

## 使用 `CLAUDE.md` 文件

对于 Claude Agent 主机会话，请在存储库根目录中使用 `CLAUDE.md` 以获得项目范围的指导。 Claude 线束还支持其他本机位置和格式，例如 `.claude/CLAUDE.md` 和 `.claude/rules`。

当本地agent在这些位置搜索Claude指令时


聊天。useClaudeMdFile

在 VS Code
中打开 在 VS Code 内部人员中打开 
 已启用：

|地点|描述|
| --- | --- |
|工作空间根目录|`CLAUDE.md` 在工作区的根目录中|
|`.claude`文件夹|`.claude/CLAUDE.md` 在您的工作区中|
|用户主页|`~/.claude/CLAUDE.md` 针对所有项目的个人说明|
|本地版本|`CLAUDE.local.md` 仅用于本地指令（不致力于版本控制）|

要启用或禁用对 `CLAUDE.md` 文件的支持，请配置


聊天。useClaudeMdFile

在VS Code
中打开在VS Code内部人员
设置中打开。

注意事项

对于 `.claude/rules` 指令文件，请按照 [Claude 规则格式 ](<https://code.claude.com/docs/en/memory#basic-structure>)，对 glob 模式使用 `paths` 属性而不是 `applyTo`。 `paths` 属性接受 glob 模式数组，省略时默认为 `**`。

## 为您的工作区生成自定义说明

Agent 自定义编辑器可以启动聊天来分析您的存储库并为所选工具创建指令。在保存之前检查结果，因为生成的命令、体系结构详细信息或约定可能不完整。

有关完整的生成和验证工作流程，请参阅[为您的代码库配置AI](<https://code.visualstudio.com/docs/agents/guides/customize-copilot-guide>)。

## 验证您的指示

使用代表性任务测试说明，您的项目指导会影响结果。

1. 选择所需的线束，打开 Agent 自定义编辑器，并确认已列出说明文件。这可以验证发现，但不能验证是否遵循了说明。
2. 使用相同的工具和存储库开始新的聊天。
3. 要求 agent 完成一项具有明确成功标准的小任务。
4. 展开响应中的 **References** 并确认使用了预期的指令。
5. 查看结果和工具活动以检查 agent 是否遵循相关约定和验证步骤。

如果文件丢失或结果不符合说明，请参阅[为什么我的说明文件没有被应用？](<#_why-is-my-instructions-file-not-being-applied>)。

## 其他指令来源

### 跨团队共享自定义指令

要在 GitHub 组织内的多个工作区和存储库之间共享自定义指令，您可以在 GitHub 组织级别定义它们。

支持的 Copilot 会话会自动包含您的帐户可以访问的组织说明。组织指令是对用户和存储库指令的补充。

要启用组织级自定义指令的发现，请设置


github.copilot.chat.organizationInstructions.enabled

在VS Code
中打开在VS Code内部人员
至`true`中打开。

了解如何在 GitHub 文档中[为您的组织添加自定义说明](<https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-organization-instructions>)。

### 跨设备同步本地 agent 指令

VS Code可以使用[设置同步](<https://code.visualstudio.com/docs/configure/settings-sync>)同步存储在您的用户个人资料中的本地agent指令。 Agent 主机文件夹中的用户指令（例如 `~/.copilot/instructions` 和 `~/.claude/rules`）不会通过 Settings Sync 漫游。

要同步配置文件说明，请启用设置同步并运行 **设置同步：从命令面板配置 ** (⇧⌘P (Windows、Linux Ctrl+Shift+P))。从要同步的设置列表中选择**提示和说明**。

### 指定生成内容的说明

注意事项

从 VS Code 1.102 开始，基于设置的代码生成和测试生成指令已弃用。请改用 [ 基于文件的指令 ](<#_types-of-instruction-files>)。

对于代码审查、提交消息和拉取请求描述，您仍然可以使用 VS Code 设置来定义自定义指令。这些设置接受具有 `text` 属性（内联指令）或 `file` 属性（Markdown 文件的路径）的对象数组。

|场景|设置|
| --- | --- |
|检查选定的代码|github.copilot.chat.reviewSelection.instructions 在 VS Code 中打开 在 VS Code 中打开|
|提交消息|github.copilot.chat.commitMessageGeneration.instructions 在 VS Code 中打开 在 VS Code 中打开|
|拉取请求描述|github.copilot.chat.pullRequestDescriptionGeneration.instructions 在 VS Code 中打开 在 VS Code 中打开|

## 解决冲突的指令

适用的指令源是附加的。不要依赖文件顺序或优先规则来解决冲突，因为发现和合并行为可能因线束而异。

保持共享需求在用户、存储库和组织指令之间保持一致。消除重复指导，从源头上解决矛盾。

例如，假设组织指令需要经过批准的 HTTP 客户端，存储库指令标识该客户端的项目包装器，而个人指令则请求不同的库。 API 文件的目标指令可能会添加另一个冲突规则。不要添加新指令或假设一个源会覆盖其他源。

1. 与预期的安全带开始新的聊天，并要求进行与目标指令相匹配的小改动。
2. 使用Agent自定义编辑器和Agent调试日志检查发现了哪些指令源以及它们是否包含错误。
3. 展开响应中的 **References**，以确认请求中包含哪些指令。
4. 将生成的代码、工具活动和验证结果与存储库要求进行比较。
5. 解决源文件中的矛盾。删除或缩小个人规则，并使存储库和目标指令与审查的组织要求保持一致。
6. 在另一个新聊天中重复相同的任务并比较观察到的结果。

有关更多诊断步骤，请参阅 [ 对 VS Code](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/troubleshooting>) 中的 AI 进行故障排除和 [ 检查模型请求 ](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/chat-debug-view#_instructions-or-a-prompt-file-are-not-applied>) 中发送的指令。

## 撰写有效说明的技巧

* 让您的说明简短且独立。每条指令都应该是一个简单的语句。如果您需要提供多条信息，请使用多条指令。
* 包括规则背后的推理。当指令解释 * 为什么 * 存在约定时，AI 在边缘情况下做出更好的决策。例如：“使用 `date-fns` 而不是 `moment.js`，因为 moment.js 已弃用并会增加捆绑包大小。”
* 通过具体的代码示例展示首选和避免的模式。 AI 对示例的响应比对抽象规则的响应更有效。
* 关注非显而易见的规则。跳过标准 linter 或格式化程序已经强制执行的约定。
* 对于特定于任务或语言的说明，请为每个主题使用多个 `*.instructions.md` 文件，并使用 `applyTo` 属性有选择地应用它们。
* 将项目特定的说明存储在您的工作区中，以便与其他团队成员共享并将它们包含在版本控制中。
* 重复使用和参考 [ 提示文件 ](<https://code.visualstudio.com/docs/agent-customization/prompt-files>) 和 [ 自定义 agents](<https://code.visualstudio.com/docs/agent-customization/custom-agents>) 中的说明文件，以保持它们干净和集中，并避免重复说明。

## 常见问题

### 为什么我的说明文件没有被应用？

提示

使用 Agent 自定义编辑器确认所选线束发现该文件。要检查加载详细信息和错误，请运行 **Developer：从命令面板中打开 Agent 调试日志 **，或从省略号中选择 **显示 Agent 调试日志 ** (**...**) 聊天视图中的菜单。详细了解 [ 疑难解答 agent 自定义项 ](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/troubleshooting>)。

如果您的说明文件未得到应用，请检查以下各项：

* 验证是否选择了预期的 agent 线束。
* 验证您的说明文件是否位于该线束和范围的 [ 支持的位置 ](<#_instructions-file-locations>)。
* 对于 `*.instructions.md` 文件，检查 `applyTo` 是否与 agent 创建或修改的文件匹配，或者 `description` 是否清楚标识相关任务。如果两者都不适用，请手动附加文件。
* 对于本地agent，检查相关设置是否已启用。这些包括


聊天。includeApplyingInstructions

在 VS Code
 中打开 在 VS Code Insiders
 中打开以获取基于模式的指令，


聊天。includeReferencedInstructions

在 VS Code
 中打开 在 VS Code 内部人士 
 中打开以获取链接指令，以及


聊天。useAgentsMdFile

在 VS Code
 中打开 在 VS Code 内部人士 
 中打开 `AGENTS.md`。
* 展开聊天响应中的 ** 参考文献 ** 以检查使用了哪些指令。

对于高级诊断，[检查模型请求中发送的指令](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/chat-debug-view#_instructions-or-a-prompt-file-are-not-applied>)。

### 如何找到说明文件？

1. 选择应使用该指令的 agent 线束。
2. 运行 **Chat：从命令面板 (⇧⌘P (Windows、Linux Ctrl+Shift+P)) 打开自定义 **。
3. 选择**指令**，然后选择指令以查看其来源和位置。

如果缺少指令，请运行**开发人员：打开Agent调试日志**以检查发现和加载错误。

## 相关资源

* [创建和管理 agent 自定义项](<https://code.visualstudio.com/docs/agent-customization/overview>)
* [为您的代码库配置AI](<https://code.visualstudio.com/docs/agents/guides/customize-copilot-guide>)
* [agent自定义问题排查](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/troubleshooting>)

10/7/2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Use custom instructions in VS Code

* [View as Markdown](<https://code.visualstudio.com/docs/agent-customization/custom-instructions.md>)
* [Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-instructions%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)
* [Ask in VS Code Insiders](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-instructions%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-instructions%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

---

Custom instructions provide reusable context that helps AI follow your coding practices and project requirements. Instead of repeating guidelines in every chat prompt, store them in Markdown files that you can share with your team or reuse across projects.

This article helps you choose an instruction type and location, configure when instructions apply, and verify that your agent uses them. For a guided setup workflow, see [Configure AI for your codebase](<https://code.visualstudio.com/docs/agents/guides/customize-copilot-guide>).

Instruction support depends on the [agent harness](<https://code.visualstudio.com/docs/agents/concepts/agent-harnesses>) selected for your session. Agent Host sessions use the discovery rules and file formats of the selected harness. The Local agent uses the VS Code instruction settings described in this article.

Tip

Use the [Agent Customizations editor](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>) to discover, create, and manage customizations for the selected agent harness. Run **Chat: Open Customizations** from the Command Palette.

Note

Custom instructions are not taken into account for [inline suggestions](<https://code.visualstudio.com/docs/editing/ai-powered-suggestions>) as you type in the editor.

## Types of instruction files

Select the intended harness before you open the Agent Customizations editor. The editor shows the customizations available to that harness.

### Choose a format

Use an instruction format supported by the selected harness. The Local agent also supports compatibility formats.

| Harness | Recommended project instructions | Targeted instructions |
| --- | --- | --- |
| Copilot | [`.github/copilot-instructions.md`](<#_use-a-githubcopilot-instructionsmd-file>) or [`AGENTS.md`](<#_use-an-agentsmd-file>) | [`.github/instructions/**/*.instructions.md`](<#_use-instructionsmd-files>) |
| Anthropic Claude | [`CLAUDE.md`](<#_use-a-claudemd-file>) | Markdown files in `.claude/rules` |
| OpenAI Codex | [`AGENTS.md`](<#_use-an-agentsmd-file>) | `AGENTS.md` files in subfolders |
| Local | `.github/copilot-instructions.md`, `AGENTS.md`, or `CLAUDE.md` | `.github/instructions/**/*.instructions.md` or Markdown files in `.claude/rules` |

`AGENTS.md` is not specific to Codex. It is a cross-agent format that you can use as the shared project instructions file when your selected harnesses support it. Some harnesses also recognize additional formats.

If your team uses multiple harnesses, use a shared format that they all support when possible. When you need separate files, keep shared requirements consistent and avoid contradictory copies.

### Choose a scope

Store instructions at the narrowest scope that matches how you want to use and share them.

| Scope | Use it for | Storage |
| --- | --- | --- |
| Workspace | Project conventions shared with contributors | A supported folder in the repository or session working folder |
| User | Personal preferences across projects | A harness-specific user folder for Agent Host, or your VS Code profile for the Local agent |
| Organization | Centrally managed requirements for supported Copilot sessions | GitHub organization settings |

User instructions in Agent Host folders, such as `~/.copilot/instructions` and `~/.claude/rules`, do not roam through Settings Sync. Local agent instructions stored in your VS Code profile can roam through Settings Sync.

### Choose activation behavior

Project instructions are automatically included according to the selected harness. Use targeted instructions when guidance applies only to specific files or tasks:

* **File pattern**: VS Code automatically attaches an `.instructions.md` file when its `applyTo` pattern matches a file that the agent creates or modifies.
* **Task relevance**: a descriptive `description` helps the agent decide whether to load an instructions file for the current task.
* **Manual**: attach an instructions file to an individual chat request.

For Claude rules, use the `paths` frontmatter property instead of `applyTo`.

To reference files or URLs in instructions, use Markdown links. Relative file paths resolve from the instructions file. Use `/` path separators to keep instructions portable across operating systems.

## Use a `.github/copilot-instructions.md` file

For Copilot Agent Host sessions, use `.github/copilot-instructions.md` for project-wide guidance. Store the file in the `.github` folder at the repository root.

The Local agent also discovers this workspace file when 


github.copilot.chat.codeGeneration.useInstructionFiles

Open in VS Code
Open in VS Code Insiders
 is enabled.

Use `copilot-instructions.md` for:

* Coding style and naming conventions that apply across the project.
* Technology stack declarations and preferred libraries.
* Architectural patterns to follow or avoid.
* Security requirements and error handling approaches.
* Documentation standards.

For personal, always-on instructions in Copilot Agent Host sessions, use `~/.copilot/copilot-instructions.md`.

Follow these steps to create a `.github/copilot-instructions.md` file in your workspace:

1. Create a `.github/copilot-instructions.md` file at the root of your repository. If needed, create a `.github` directory first.
2. Add concise, project-specific instructions in Markdown.

Example: General coding guidelines

```
# Project instructions

## Architecture

* Add HTTP handlers under `src/api/routes`.
* Keep database access in `src/repositories` so handlers remain independently testable.

## Validation

* Run `npm test -- <changed-package>` after changing application code.
* Add or update tests for every behavior change.
```

## Use `.instructions.md` files

You can create file-based instructions with `*.instructions.md` Markdown files. VS Code automatically attaches files with an `applyTo` pattern that matches the files being changed. The agent can also load an instructions file on demand when its `description` matches the current task.

Use `.instructions.md` files for:

* Different conventions for frontend and backend code.
* Language-specific guidelines in a monorepo.
* Framework-specific patterns for specific modules.
* Specialized rules for test files or documentation.

### Instructions file locations

The supported location depends on the session type and selected harness.

| Session and scope | Default file location |
| --- | --- |
| Agent Host workspace, Copilot format | `.github/instructions` |
| Agent Host workspace, Claude format | `.claude/rules` |
| Agent Host user, Copilot format | `~/.copilot/instructions` |
| Agent Host user, Claude format | `~/.claude/rules` |
| Local agent workspace | `.github/instructions` or `.claude/rules` |
| Local agent user | VS Code profile storage |

Use the Agent Customizations editor to create user instructions in a location supported by the selected harness. To move profile-based instructions to Agent Host user folders, use [user customization migration](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_move-user-agents-and-instructions>).

Note

The 


chat.instructionsFilesLocations

Open in VS Code
Open in VS Code Insiders
 setting is deprecated and only used by the Local agent. If you configured other instruction locations with this setting, [migrate the customizations to supported locations](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_move-customizations-from-configured-locations>).

VS Code searches these folders recursively, which enables you to organize instructions files in subdirectories. For example, you can group instructions by team, language, or module:

```
.github/instructions/
  frontend/
    react.instructions.md
    accessibility.instructions.md
  backend/
    api-design.instructions.md
  testing/
    unit-tests.instructions.md
```

Tip

In a monorepo, enable 


chat.useCustomizationsInParentRepositories

Open in VS Code
Open in VS Code Insiders
 to discover instructions from the parent repository root. Learn more about [parent repository discovery](<https://code.visualstudio.com/docs/agent-customization/overview#_use-customizations-in-a-monorepo>).

### Instructions file format

For `.instructions.md` files, YAML frontmatter controls how the instructions are discovered and applied:

| Field | Required | Description |
| --- | --- | --- |
| `name` | No | Display name shown in the UI. Defaults to the file name. |
| `description` | No | Describes the tasks for which the file is relevant. Include it for on-demand discovery. |
| `applyTo` | No | Glob pattern that automatically applies the instructions to matching files, relative to the workspace root. Use `**` to match all files. |

The body contains the instructions in Markdown format.

```
---
name: 'Python testing'
description: 'Use when creating or updating Python unit tests.'
applyTo: '**/*.py'
---
# Python testing

* Use `pytest` fixtures from `tests/conftest.py` instead of creating duplicate setup helpers.
* Name tests `test_<behavior>_<condition>`.
* Run `python -m pytest <test-file>` after changing a test.
```

If you omit both `description` and `applyTo`, attach the file manually when you want to use it. For Claude rules, use the `paths` property instead of `applyTo`.

### Create an instructions file

When you create an instructions file, choose whether to store it at workspace or user scope. Workspace instructions apply only to that workspace, while user instructions are available across multiple workspaces for the corresponding harness.

To create an instructions file:

1. Select the agent harness that should use the instructions.
2. In the Chat view, select **Configure Chat** (gear icon), or run **Chat: Open Customizations** from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)).
3. In the Agent Customizations editor, select **Instructions**.
4. From the **New** dropdown, select a workspace or user instruction.
5. Select a supported location and enter a file name.
6. Add the frontmatter and Markdown instructions, and then save the file.

You can also run **Chat: New Instructions File** from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)).

![Screenshot showing the Instructions section and the menu for creating an instructions file in the Agent Customizations editor.](<https://code.visualstudio.com/assets/docs/agent-customization/customization/create-instructions-file.png>)

### Generate an instructions file with AI

In the Agent Customizations editor, enter a request in the **Overview** section that describes the instruction you want to create. Confirm that the generated file uses the format and location for the selected harness.

For a Local session, you can also type `/create-instructions` in chat and describe the convention or guideline you want to enforce. To generate project-wide instructions, type `/init`.

Review generated instructions before you use them. Verify commands, paths, and conventions against the repository.

## Use an `AGENTS.md` file

`AGENTS.md` is a cross-agent format for project guidance. It is supported by multiple harnesses, including Copilot and OpenAI Codex, and by the Local agent. Place the primary file at the repository root.

Use `AGENTS.md` when:

* You work with multiple compatible AI coding agents and want to share one set of instructions.
* You want subfolder-level instructions that apply to specific parts of a monorepo.

For the Local agent, configure the 


chat.useAgentsMdFile

Open in VS Code
Open in VS Code Insiders
 setting to enable or disable support for `AGENTS.md` files.

### Use multiple `AGENTS.md` files

Use nested `AGENTS.md` files when different folders need different guidance. Discovery and activation depend on the selected harness.

For the Local agent, use the 


chat.useNestedAgentsMdFiles

Open in VS Code
Open in VS Code Insiders
 setting to enable or disable support for nested `AGENTS.md` files. The setting is disabled by default.

When enabled, VS Code lists nested `AGENTS.md` files with their folder locations so the Local agent can load relevant instructions for the task. For Agent Host sessions, follow the selected harness's working-folder and nested-instruction rules.

Tip

For folder-specific instructions, you can also use multiple [`.instructions.md`](<#_use-instructionsmd-files>) files with different `applyTo` patterns that match the folder structure.

## Use a `CLAUDE.md` file

For Claude Agent Host sessions, use `CLAUDE.md` at the repository root for project-wide guidance. The Claude harness also supports additional native locations and formats, such as `.claude/CLAUDE.md` and `.claude/rules`.

The Local agent searches for Claude instructions in these locations when 


chat.useClaudeMdFile

Open in VS Code
Open in VS Code Insiders
 is enabled:

| Location | Description |
| --- | --- |
| Workspace root | `CLAUDE.md` in the root of your workspace |
| `.claude` folder | `.claude/CLAUDE.md` in your workspace |
| User home | `~/.claude/CLAUDE.md` for personal instructions across all projects |
| Local variant | `CLAUDE.local.md` for local-only instructions (not committed to version control) |

To enable or disable support for `CLAUDE.md` files, configure the 


chat.useClaudeMdFile

Open in VS Code
Open in VS Code Insiders
 setting.

Note

For `.claude/rules` instructions files, use a `paths` property instead of `applyTo` for glob patterns, following the [Claude rules format](<https://code.claude.com/docs/en/memory#basic-structure>). The `paths` property accepts an array of glob patterns and defaults to `**` when omitted.

## Generate custom instructions for your workspace

The Agent Customizations editor can start a chat that analyzes your repository and creates instructions for the selected harness. Review the result before saving it because generated commands, architecture details, or conventions might be incomplete.

For a complete generation and verification workflow, see [Configure AI for your codebase](<https://code.visualstudio.com/docs/agents/guides/customize-copilot-guide>).

## Verify your instructions

Test instructions with a representative task where your project guidance affects the result.

1. Select the intended harness, open the Agent Customizations editor, and confirm that the instructions file is listed. This verifies discovery, but not whether the instructions are followed.
2. Start a new chat with the same harness and repository.
3. Ask the agent to complete a small task with a clear success criterion.
4. Expand **References** in the response and confirm that the expected instructions were used.
5. Review the result and tool activity to check that the agent followed the relevant conventions and validation steps.

If the file is missing or the result does not follow the instructions, see [Why is my instructions file not being applied?](<#_why-is-my-instructions-file-not-being-applied>).

## Other instruction sources

### Share custom instructions across teams

To share custom instructions across multiple workspaces and repositories within your GitHub organization, you can define them at the GitHub organization level.

Supported Copilot sessions automatically include organization instructions that your account can access. Organization instructions are additive to user and repository instructions.

To enable discovery of organization-level custom instructions, set 


github.copilot.chat.organizationInstructions.enabled

Open in VS Code
Open in VS Code Insiders
 to `true`.

Learn how you can [add custom instructions for your organization](<https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-organization-instructions>) in the GitHub documentation.

### Sync Local agent instructions across devices

VS Code can sync Local agent instructions stored in your user profile by using [Settings Sync](<https://code.visualstudio.com/docs/configure/settings-sync>). User instructions in Agent Host folders, such as `~/.copilot/instructions` and `~/.claude/rules`, do not roam through Settings Sync.

To sync profile instructions, enable Settings Sync and run **Settings Sync: Configure** from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)). Select **Prompts and Instructions** from the list of settings to sync.

### Specify instructions for generated content

Note

Settings-based code generation and test generation instructions are deprecated as of VS Code 1.102. Use [file-based instructions](<#_types-of-instruction-files>) instead.

For code review, commit messages, and pull request descriptions, you can still use VS Code settings to define custom instructions. These settings accept an array of objects with either a `text` property (inline instruction) or a `file` property (path to a Markdown file).

| Scenario | Setting |
| --- | --- |
| Review selected code | github.copilot.chat.reviewSelection.instructions  Open in VS Code Open in VS Code Insiders |
| Commit messages | github.copilot.chat.commitMessageGeneration.instructions  Open in VS Code Open in VS Code Insiders |
| Pull request descriptions | github.copilot.chat.pullRequestDescriptionGeneration.instructions  Open in VS Code Open in VS Code Insiders |

## Resolve conflicting instructions

Applicable instruction sources are additive. Do not depend on a file order or precedence rule to resolve conflicts because discovery and merge behavior can differ by harness.

Keep shared requirements consistent across user, repository, and organization instructions. Remove duplicate guidance and resolve contradictions at their source.

For example, suppose organization instructions require an approved HTTP client, repository instructions identify the project's wrapper for that client, and personal instructions request a different library. A targeted instruction for API files might add another conflicting rule. Don't add a new instruction or assume that one source overrides the others.

1. Start a new chat with the intended harness and ask for a small change that matches the targeted instruction.
2. Use the Agent Customizations editor and Agent Debug Logs to check which instruction sources are discovered and whether they contain errors.
3. Expand **References** in the response to confirm which instructions were included for the request.
4. Compare the generated code, tool activity, and validation results with the repository requirement.
5. Resolve the contradiction in the source files. Remove or narrow the personal rule, and align the repository and targeted instructions with the reviewed organization requirement.
6. Repeat the same task in another new chat and compare the observed result.

For more diagnostic steps, see [Troubleshoot AI in VS Code](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/troubleshooting>) and [inspect the instructions sent in the model request](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/chat-debug-view#_instructions-or-a-prompt-file-are-not-applied>).

## Tips for writing effective instructions

* Keep your instructions short and self-contained. Each instruction should be a single, simple statement. If you need to provide multiple pieces of information, use multiple instructions.
* Include the reasoning behind rules. When instructions explain *why* a convention exists, the AI makes better decisions in edge cases. For example: "Use `date-fns` instead of `moment.js` because moment.js is deprecated and increases bundle size."
* Show preferred and avoided patterns with concrete code examples. The AI responds more effectively to examples than to abstract rules.
* Focus on non-obvious rules. Skip conventions that standard linters or formatters already enforce.
* For task or language-specific instructions, use multiple `*.instructions.md` files per topic and apply them selectively by using the `applyTo` property.
* Store project-specific instructions in your workspace to share them with other team members and include them in your version control.
* Reuse and reference instructions files in your [prompt files](<https://code.visualstudio.com/docs/agent-customization/prompt-files>) and [custom agents](<https://code.visualstudio.com/docs/agent-customization/custom-agents>) to keep them clean and focused, and to avoid duplicating instructions.

## Frequently asked questions

### Why is my instructions file not being applied?

Tip

Use the Agent Customizations editor to confirm that the selected harness discovers the file. To inspect loading details and errors, run **Developer: Open Agent Debug Logs** from the Command Palette, or select **Show Agent Debug Logs** from the ellipsis (**...**) menu in the Chat view. Learn more about [troubleshooting agent customizations](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/troubleshooting>).

If your instructions file is not being applied, check the following:

* Verify that the intended agent harness is selected.
* Verify that your instructions file is in a [supported location](<#_instructions-file-locations>) for that harness and scope.
* For `*.instructions.md` files, check that `applyTo` matches a file the agent creates or modifies, or that `description` clearly identifies the relevant task. If neither applies, attach the file manually.
* For the Local agent, check that relevant settings are enabled. These include 


  chat.includeApplyingInstructions

  Open in VS Code
  Open in VS Code Insiders
   for pattern-based instructions, 


  chat.includeReferencedInstructions

  Open in VS Code
  Open in VS Code Insiders
   for linked instructions, and 


  chat.useAgentsMdFile

  Open in VS Code
  Open in VS Code Insiders
   for `AGENTS.md`.
* Expand **References** in the chat response to check which instructions were used.

For advanced diagnostics, [inspect the instructions sent in the model request](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/chat-debug-view#_instructions-or-a-prompt-file-are-not-applied>).

### How do I find an instruction file?

1. Select the agent harness that should use the instruction.
2. Run **Chat: Open Customizations** from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)).
3. Select **Instructions**, and then select the instruction to view its source and location.

If the instruction is missing, run **Developer: Open Agent Debug Logs** to inspect discovery and loading errors.

## Related resources

* [Create and manage agent customizations](<https://code.visualstudio.com/docs/agent-customization/overview>)
* [Configure AI for your codebase](<https://code.visualstudio.com/docs/agents/guides/customize-copilot-guide>)
* [Troubleshoot agent customizations](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/troubleshooting>)

10/7/2026


</details>
