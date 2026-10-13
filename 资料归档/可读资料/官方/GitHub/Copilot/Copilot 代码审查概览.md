# Copilot 代码审查概览

介绍审查的运行方式、支持界面、权限与结果处理，说明 Agent 能力和运行器需求，并交代使用条件、用量及功能限制。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-10-08](<https://github.com/github/docs/commit/62ea212d62a528026a8d6c2d64e4e9e36f9186a6>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/concepts/agents/code-review>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 代码审查概览.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 关于GitHub Copilot代码审查

Copilot 审核您的拉取请求、识别问题并提出修复建议，您只需点击几下即可应用。

## 简介

Copilot 代码审查审查以任何语言编写的代码，并提供反馈。它从多个角度审查您的代码以识别问题并提出修复建议。您只需点击几下即可应用建议的更改。

本文概述了 Copilot 代码审查。要了解如何向 Copilot 请求代码审查，请参阅 [使用 GitHub Copilot 代码审查 ](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)。

## 可用性

Copilot 代码审查支持：

* GitHub.com
* GitHub CLI
* GitHub 手机
* VS Code
* 视觉工作室
* Xcode
* JetBrains IDE
* Azure DevOps（公共预览版）

> \[!NOTE]
> 如果您从组织收到 Copilot，则您的组织必须在 Copilot 策略设置中启用 **Copilot 代码审核 ** 选项。这适用于 GitHub.com 或 GitHub Mobile 中的评论。请参阅 [管理组织中 GitHub Copilot 的策略和功能 ](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>)。

## Copilot 代码审查，无需 Copilot 许可证

没有 Copilot 许可证 ** 的组织成员 ** 可以在 GitHub.com 上使用 Copilot 代码审查。企业管理员或组织所有者必须启用它。此功能可供使用 **Copilot 商业版 ** 和 **Copilot 企业版 ** 计划的组织使用。

### 为没有许可证的用户启用代码审查

要允许没有 Copilot 许可证的组织成员使用 Copilot 代码审查，您必须启用两项策略：

1. **AI 积分付费使用**。首先启用此策略。它允许企业或组织因 Copilot 代码审查 AI 积分使用而产生费用。
2. **允许没有Copilot许可的会员在GitHub.com**中使用Copilot代码审查。此子策略为没有许可证的用户启用 Copilot 代码审查。

第二条政策具有以下特点：

* 默认情况下它是禁用的。
* 一旦在企业级别设置此策略，它就会在组织级别变得**可见，但不可编辑**。
* 该政策**限制性最强**。 Copilot 代码审查仅适用于您已明确启用该策略的组织下的存储库。

### 没有许可证的用户如何运作

启用这两个策略后，没有 Copilot 许可证的用户可以请求 Copilot 代码审查对其组织存储库中的拉取请求进行审查。

在启用自动代码审查的存储库中，Copilot 自动审查所有拉取请求。无论作者是否拥有 Copilot 许可证，都会发生这种情况。有关如何配置自动代码审查的更多信息，请参阅[配置代码审查GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>)。

Copilot IDE 中不支持没有许可证的用户进行代码审查。

## Copilot 代码审查的代理功能

Copilot 代码审查利用代理功能来扩展其功能。

* **完整项目背景收集**。这提供了更具体、更准确和上下文相关的代码审查。此功能分析您的整个存储库，以更好地了解代码更改的上下文。
* **能够将建议传递给Copilot云agent**。这会自动针对您的分支创建新的拉取请求，并应用建议的修复。将建议传递给 Copilot 云 agent 处于公共预览版，可能会发生变化。

对于包含 Copilot 代码审查的所有计划，这些功能都会自动启用。有关在精简分析级别和平衡分析级别之间进行选择的信息，请参阅本文后面的[审核工作级别](<#审查努力程度>)。

如果 GitHub 操作不可用，或者 Copilot 代码审核使用的操作工作流程失败，仍会生成审核。但是，它们不会包括代理功能提供的附加功能。

### 在代码审查中使用 GitHub 操作运行程序来实现代理功能

Copilot 代码审查使用 GitHub 操作来运行代理功能，包括完整的项目上下文收集以及将建议传递给 Copilot 云 agent。默认情况下，Copilot 代码审查使用标准 GitHub 托管的运行器。您还可以升级到更大的 GitHub 托管运行器以获得更好的性能，或使用自托管运行器。

> \[！NOTE]
> 使用较大的 GitHub 托管的运行程序按更高的每分钟费率计费。自托管运行器不消耗 GitHub 操作分钟数。

您无需在组织或企业中启用 GitHub 操作即可在代码审查中使用代理功能。

如果您的组织已禁用 GitHub 托管的运行器，则代理功能将不可用。在这种情况下，代码审查将退回到更有限的审查。在这种情况下，组织可以使用自托管运行器。

有关配置运行器的更多信息，请参阅 [为 GitHub Copilot 代码审查配置运行器 ](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-runners>)。

您可以查看与 Copilot 代码审查运行关联的 GitHub 操作分钟数。有关更多信息，请参阅 [GitHub 代码审查操作分钟 ](<https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing#pricing-and-usage-cost-considerations-for-copilot-code-review>)。

### 预计消耗量

一次审核通常会消耗估计 $0.05 USD 至 $1 USD 的 AI 积分（“Lite”），以及 $0.25 USD通过“平衡”努力获得价值 5 USD 的 AI 积分。

消耗通常会随着拉取请求大小和存储库自定义指令的增加而增加，并且范围可能会随着模型的发展而变化。这些估计值不包括 GitHub 操作分钟数。

存储库和组织管理员可以设置自动代码审查的默认审查工作级别。配置步骤请参见[配置代码审查GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review#customizing-copilot-code-review-1>)。

## 代码审查使用和计费

每次 Copilot 审核拉取请求或审核 IDE 中的代码时，交互都会消耗 AI 积分。金额取决于所使用的模型和处理的代币数量。

代码审查有两个成本组成部分：用于模型交互（审查本身）的 AI 学分，以及用于代理功能（上下文收集和工具使用）的 GitHub 操作分钟数。有关 GitHub 操作用法的更多信息，请参阅 [在代码审查中使用 GitHub 操作运行程序实现代理功能 ](<#在代码审查中使用-github-操作运行程序来实现代理功能>)。

默认情况下，当存储库自动请求代码审查时，AI 积分消耗归因于拉取请求作者。如果其他用户手动请求评论，则消费将归因于该用户。

对于 Copilot 云 agent 创作的拉取请求，使用首先归因于与更改相关的人类共同作者。如果无法向共同作者计费，则直接向组织收取使用费用。

对于其他机器人编写的拉取请求，或者当机器人请求审核时，使用量将直接向组织计费。这些拉取请求有资格接受代理审查。

对于与已支付 Copilot 许可证的组织成员关联的请求，默认情况下会对成员的权利进行计费。企业或组织所有者可以选择向组织计费。对组织进行计费需要 AI 积分付费使用，但预算是可选的。此选择适用于手动请求和自动代码审查，并且仅更改计费。它本身不授予对 Copilot 代码审查的访问权限。

### 使用外部 Copilot 许可证请求审核

外部 Copilot 许可证不是由拥有存储库的组织或企业提供的许可证，例如个人许可证或来自其他组织的许可证。默认情况下，人们可以使用外部许可证来请求 Copilot 进行审核。

为了防止这种情况，组织所有者和存储库管理员可以为组织或单个存储库启用**仅允许授权用户触发Copilot代码审查**设置。当启用该设置时：

* Copilot 不会作为审阅者提供给未经授权的人员，并且这些人发出的 API 审阅请求不会启动审阅。
* 在个人存储库中，只有存储库所有者或直接协作者可以请求审阅。
* 未经授权的人员不会从个人设置中进行自动审核。由存储库或组织规则集配置的自动审核仍然运行。

配置步骤请参见[通过GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review#customizing-copilot-code-review>)配置代码审查。

### 没有 Copilot 许可证或包含 Copilot 代码审查的计划的用户

无法访问 Copilot 代码审查的用户没有每月 AI 积分的津贴。这包括没有 Copilot 许可证的用户和使用 Copilot 免费计划的用户，该计划不包括 Copilot 代码审查。

当为这些用户启用 Copilot 代码审查时，他们消耗的任何 AI 积分将作为额外使用费直接向组织或企业计费。这适用于手动请求的审查和自动代码审查。

AI 这些用户消耗的积分不属于任何单个用户的预算。它们在计费报告中显示为附加使用情况。

### 达到预算后会发生什么

对于 Copilot 商业版和 Copilot 企业版，代码审查访问权限由适用于所选计费来源的预算控制。如果向用户收取使用费用，则当用户达到预算时，代码审查将被阻止。如果使用量按组织计费，则当适用的组织预算、成本中心预算或企业支出限制用尽时，代码审查将被阻止。代码审查与其他 AI 消耗积分的功能一起被阻止。请参阅[基于使用情况的计费预算](<https://docs.github.com/en/copilot/concepts/billing-and-usage/organizations-and-enterprises/budgets#what-happens-when-a-user-is-blocked>)。

## 型号用途

Copilot 代码审查是一款专门构建的产品，它使用精心调整的模型、提示和系统行为组合，在各种代码库中提供一致、高质量的反馈。不支持模型切换，因为更改模型可能会影响可靠性、用户体验和评论质量。

> \[!NOTE]
> Copilot 代码审查可能会使用您组织的“模型”设置页面上未启用的模型。 “模型”设置页面仅控制Copilot聊天。
>
> 由于Copilot代码审查已普遍可用，因此所有模型的使用都将遵守普遍可用的条款。请参阅 [管理组织中 GitHub Copilot 的策略和功能 ](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>)。

## 自动拉取请求审查

默认情况下，如果您将 Copilot 分配给拉取请求，则仅审核该拉取请求。但是，您可以配置自动审阅。

* **用户**可以配置Copilot以自动审查他们创建的拉取请求。此功能适用于 Copilot Pro、Copilot Pro+ 和 Copilot Max 套餐，并具有 Copilot Business 或 Copilot Enterprise 许可证。它不适用于托管用户帐户。
* **存储库所有者**可以将Copilot配置为自动审查存储库中由有权访问Copilot的人员创建的所有拉取请求。
* **组织所有者** 可以将 Copilot 配置为自动审核组织中由 Copilot 用户创建拉取请求的部分或全部存储库中的所有拉取请求。

Copilot 根据两个单独的配置评估拉取请求：用户的设置和任何适用的规则集。这些配置没有层次结构；两者都不继承或覆盖对方。当至少启用一项配置时，会自动审核拉取请求。如果在拉取请求中启用了两种配置，Copilot 仍然只发布一篇评论。

如果您的组织已启用 Copilot 无需进行代码审查 Copilot 许可证，自动审查也适用于没有许可证的组织成员创建的拉取请求。这适用于启用自动审查的策略所涵盖的存储库。有关更多信息，请参阅 [Copilot 无需进行代码审查 Copilot 许可证](<#copilot-代码审查无需-copilot-许可证>).

### 触发自动拉取请求审查

自动代码审查的触发器取决于配置设置。

* 基本设置：
  * 当您创建拉取请求作为“打开”拉取请求时。
  * 第一次将“草稿”拉取请求切换为“打开”。
* 查看新推送：
  * 每次将新的提交推送到拉取请求时。
* 审核拉取请求草稿：
  * 拉取请求在您将其切换为“打开”之前，会在它们仍为草稿时自动进行审核。

当拉取请求符合自动审查资格时，如果作者的设置或适用的规则集打开了**审查新推送**和**审查草稿拉取请求**，则应用它们。您无法使用自己的设置来关闭规则集已打开的推送或草稿审核。

有关完整说明，请参阅 [通过 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>) 配置代码审查。

> \[！NOTE]
> 除非Copilot已配置为审核每个拉取请求的推送，否则它只会审核一次拉取请求。如果您在自动审核拉取请求后对其进行更改，并且希望 Copilot 重新审核它，您可以手动请求。单击 ** 审稿人 ** 菜单中 Copilot 姓名旁边的 <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-sync" aria-label="Re-request review" role="img"><path d="M1.705 8.005a.75.75 0 0 1 .834.656 5.5 5.5 0 0 0 9.592 2.97l-1.204-1.204a.25.25 0 0 1 .177-.427h3.646a.25.25 0 0 1 .25.25v3.646a.25.25 0 0 1-.427.177l-1.38-1.38A7.002 7.002 0 0 1 1.05 8.84a.75.75 0 0 1 .656-.834ZM8 2.5a5.487 5.487 0 0 0-4.131 1.869l1.204 1.204A.25.25 0 0 1 4.896 6H1.25A.25.25 0 0 1 1 5.75V2.104a.25.25 0 0 1 .427-.177l1.38 1.38A7.002 7.002 0 0 1 14.95 7.16a.75.75 0 0 1-1.49.178A5.5 5.5 0 0 0 8 2.5Z"></path></svg> 按钮。

## 审查努力程度

Copilot 代码审查支持多个 Copilot 审查工作级别，因此您可以选择与代码的关键性相匹配的彻底性级别。

* **Lite**：标准审查。针对错误、安全漏洞和风格不一致等常见问题提供快速、有针对性的反馈。
* **平衡**：将拉取请求路由到更高推理模型，以便对复杂逻辑、安全敏感代码和跨服务更改进行更长时间的分析（默认）。与精简评论相比，平衡评论使用更多的 AI 积分，并且可能消耗更多的 GitHub 操作分钟数。

对于安全敏感的代码、多服务拉取请求或具有严格质量标准的存储库，请使用平衡。使用 Lite 进行例行更改，其中快速反馈比详尽分析更重要。

在拉取请求中请求审阅时，您可以在 **审阅者 ** 部分下选择 Copilot 审阅工作，其中 Copilot 显示为审阅者。您还可以在 Copilot 代码审核设置中设置默认的 Copilot 审核工作。组织所有者可以为其组织中的自动代码审查设置默认的 Copilot 审查工作。存储库管理员可以覆盖特定存储库的组织默认值。

当 Copilot 确定要使用哪种审核工作时，它会按顺序检查以下选项并使用第一个适用的选项：

1. 请求审核时选择的 Copilot 审核工作
2. 之前在此拉取请求中使用的 Copilot 审核工作
3. 请求者的 Copilot 审核工作。对于新的拉取请求，请求者就是作者。当某人将草稿标记为可供审阅时，该人就是请求者。
4. 为存储库设置的 Copilot 审核工作
5. 为组织设置的 Copilot 审核工作，或存储库所有者对用户拥有的存储库的 Copilot 审核工作
6. GitHub的内置默认值是平衡的。

Copilot 代码审查审查拉取请求后，拉取请求概述注释会显示每次审查运行所使用的工作量级别。

配置步骤请参见[通过GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>)配置代码审查。

## Copilot 批准

> \[！NOTE]
> Copilot 批准处于公开预览阶段，可能会发生变化。

每个 Copilot 代码审查都在概述注释中包含批准评估，指示 Copilot 是否已确定拉取请求在审查后准备批准。默认情况下，Copilot 的评论不计入拉取请求所需的批准。

当在存储库、组织和企业设置中启用 Copilot 审批时，Copilot 可以提交满足存储库所需审批规则的审批审核，就像团队成员的审批一样。如果在 Copilot 批准后推送新提交，则批准将被驳回，您可以重新请求审核。

## MCP 服务器和 agent skills 用于代码审查

Copilot 代码审查可以使用与审查相关的存储库级 agent skills 和 MCP 服务器。

当您的存储库或拉取请求给出明确的信号时，Copilot 代码审查更有可能使用技能和 MCP 上下文，包括以审查为中心的技能目录名称、引用 MCP 上下文的自定义指令，以及包含引用已配置的 MCP 服务器的标识符（例如问题密钥或事件 ID）的拉取请求描述。

### Agent skills

如果您的存储库包含 agent skills，Copilot 代码审查可以在审查拉取请求时自动使用相关技能，从而将 Copilot 扩展到其内置分析之外。

查看拉取请求时，Copilot 从头分支（包含更改的分支）而不是基础分支读取存储库自定义指令、agent 指令和 agent skills。例如，当将 `my-feature-branch` 合并到 `main` 时，Copilot 使用 `my-feature-branch` 中的指令和技能，因此您可以在同一个 Pull 请求中测试对它们的更改，而无需先合并它们。

有关设置详情，请参阅[为GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills>)添加agent skills。

### MCP 服务器

Copilot 代码审查可以使用 MCP 服务器将上下文直接从您团队使用的第三方平台和内部系统拉入审查，包括问题跟踪、文档、服务目录和事件工具。

默认情况下启用 GitHub MCP 服务器和 Playwright MCP 服务器。

您可以在存储库设置中配置 MCP 服务器。 GitHub 上的存储库 MCP 配置适用于 Copilot 云 agent 和 Copilot 代码审查。您对存储库 MCP 设置所做的更改会影响这两个功能。有关设置详细信息，请参阅[为您的存储库配置MCP服务器](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)。

在存储库设置中，默认情况下启用**允许Copilot在审查拉取请求时使用MCP工具**。如果您希望 MCP 服务器仅可用于 Copilot 云 agent，而不可用于 Copilot 代码审查，请禁用此设置。有关分步说明，请参阅 [为存储库配置 MCP 服务器 ](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers#disabling-mcp-tools-for-code-review>)。

## 排除的文件

某些文件类型被排除在 Copilot 代码审查之外：

* 依赖管理文件，例如package.json和Gemfile.lock
* 日志文件
* SVG 文件

如果您在拉取请求中包含这些文件类型，Copilot 代码审查将不会审查该文件。

有关详细信息，请参阅 [从 GitHub Copilot 代码审查中排除的文件 ](<https://docs.github.com/en/copilot/reference/review-excluded-files>)。

## 增强 Copilot 对存储库的了解

Copilot 对您存储库中的代码、您使用的工具以及您的编码标准和实践了解得越多，其评论就会变得越准确和有用。您可以通过两种方式增强 Copilot 对您的存储库的了解。

### 定制说明

这些是简短的自然语言语句，您可以将其编写并作为一个或多个文件存储在存储库中。如果您是 GitHub 上组织的所有者，您还可以在组织的设置中定义自定义说明。有关更多信息，请参阅 [关于自定义 GitHub Copilot 响应 ](<https://docs.github.com/en/copilot/concepts/prompting/response-customization?tool=webui#about-repository-custom-instructions>)。

### 在自定义指令、AGENTS.md 和技能之间进行选择

Copilot 代码审查可以利用多个定制源，每个源都有不同的目的。使用 `.github/copilot-instructions.md` 指定特定于 Copilot 的存储库范围规则，使用 `.github/instructions/` 下路径特定的 `*.instructions.md` 文件指定仅适用于某些文件或目录的规则，使用 `AGENTS.md` 指定要在 AI 工具之间共享的常规规则，以及agents，并使用 Copilot 按需运行的特定于任务的工作流程的技能。下表总结了何时使用每种规则以及如何提供规则。

|使用| `copilot-instructions.md`                                  |路径特定 `*.instructions.md`| `AGENTS.md`                                                    |技能|
| ---------- | ---------------------------------------------------------- | ----------------------------------------------------------------------------------- | -------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
|最适合|Copilot 的存储库范围内始终有效的规则|针对特定路径、文件类型或目录的始终有效规则|在 AI agents 之间共享始终开启的规则|特定于任务的审核工作流程|
|存储在| `.github/copilot-instructions.md`                          | `.github/instructions/**/*.instructions.md`                                         |存储库根（`AGENTS.md`）| `.github/skills/...`                                                                     |
|示例|编码标准、架构默认、测试期望|`content/**` 编写规则、`src/**` 编码约定、特定语言指南|应该适用于 Copilot 之外的共享存储库约定|评论、发布、迁移、分析|
|激活|自动|当更改的文件与指令范围匹配时自动|自动（从存储库根读取）|相关时自动（例如以复习为重点的技能，如 `code-review`），或按需|
|适用范围|存储库范围和 Copilot 特定|存储库子路径和 Copilot 特定的|交叉工具/agent-不可知|每个任务调用|
|规则|“Copilot，始终了解此存储库”|“Copilot，在这些路径中工作时始终要知道这一点”|“任何 agent，始终知道这一点”|“需要的时候做这个”|

有关更多信息，请参阅[为GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills>)添加agent skills和[为GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)添加存储库自定义指令。

### Copilot 内存（公开预览版）

如果您有 Copilot Pro、Copilot Pro+ 或 Copilot Max 套餐，则可以启用 Copilot 内存。这允许 Copilot 存储它了解到的有关存储库的有用详细信息。 Copilot 然后可以在审查该存储库中的拉取请求时使用此信息。有关详细信息，请参阅 [关于 GitHub Copilot 内存 ](<https://docs.github.com/en/copilot/concepts/agents/copilot-memory>)。

## 在整个存储库中获取详细的代码质量反馈

GitHub Copilot 代码审查审查拉取请求中的更改并提出修复建议。要在代码的可靠性和可维护性、拉取请求和默认分支上添加系统反馈，请启用 GitHub 代码质量。

GitHub 代码质量通过添加以下内容来补充 Copilot 代码审查：

* **基于规则的 CodeQL 支持的对拉取请求和默认分支的分析 **。
* **拉取请求的测试覆盖率指标**，以便您可以了解更改是否维持或减少了覆盖率。
* **一键式、Copilot驱动的修复**，包括将修复委托给Copilot云agent。
* **带有规则集的可选合并门**，因此可以阻止具有未解决的基于规则的结果（或错过覆盖阈值）的拉取请求合并。

有关更多信息，请参阅[GitHub代码质量](<https://docs.github.com/en/code-security/concepts/code-quality/code-quality>)。

## 验证 Copilot 代码审查

Copilot 不保证发现拉取请求中的所有问题或问题。有时会犯错误。请务必仔细验证 Copilot 的反馈。通过人工审核补充 Copilot 的反馈。

欲了解更多信息，请参阅[申请卡：GitHub Copilot Agents](<https://docs.github.com/en/copilot/responsible-use/agents>)。

## 后续步骤

* [使用GitHub Copilot代码审查](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)
* [通过GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>)配置代码审查


## 原文对照

<details>
<summary>展开完整原文</summary>

# About GitHub Copilot code review

Copilot reviews your pull requests, identifies issues, and suggests fixes you can apply in a couple of clicks.

## Introduction

Copilot code review reviews code written in any language, and provides feedback. It reviews your code from multiple angles to identify issues and suggest fixes. You can apply suggested changes with a couple of clicks.

This article provides an overview of Copilot code review. To learn how to request a code review from Copilot, see [Using GitHub Copilot code review](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>).

## Availability

Copilot code review is supported in:

* GitHub.com
* GitHub CLI
* GitHub Mobile
* VS Code
* Visual Studio
* Xcode
* JetBrains IDEs
* Azure DevOps (public preview)

> \[!NOTE]
> If you receive Copilot from an organization, your organization must enable the **Copilot code review** option in the Copilot policy settings. This applies to reviews on GitHub.com or in GitHub Mobile. See [Managing policies and features for GitHub Copilot in your organization](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>).

## Copilot code review without a Copilot license

Organization members **without a Copilot license** can use Copilot code review on GitHub.com. An enterprise administrator or organization owner must enable it. This capability is available to organizations on **Copilot Business** and **Copilot Enterprise** plans.

### Enabling code review for users without a license

To allow organization members without a Copilot license to use Copilot code review, you must enable two policies:

1. **AI credits paid usage**. Enable this policy first. It allows the enterprise or organization to incur charges for Copilot code review AI credits usage.
2. **Allow members without a Copilot license to use Copilot code review in GitHub.com**. This sub-policy enables Copilot code review for users without a license.

The second policy has these characteristics:

* It is disabled by default.
* Once this policy is set at the enterprise level, it becomes **visible, but not editable** at the organization level.
* The policy is **most restrictive**. Copilot code review is only available in repositories under an organization where you have explicitly enabled the policy.

### How it works for users without a license

When both policies are enabled, users without a Copilot license can request a review from Copilot code review on their pull requests in the organization's repositories.

In repositories where automatic code review is enabled, Copilot automatically reviews all pull requests. This happens regardless of whether the author has a Copilot license. For more information about how to configure automatic code review, see [Configuring code review by GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>).

Copilot code review for users without a license is not available in IDEs.

## Agentic capabilities for Copilot code review

Copilot code review utilizes agentic capabilities to extend its functionality.

* **Full project context gathering**. This provides more specific, accurate, and contextually aware code reviews. This capability analyzes your entire repository to better understand the context of code changes.
* **The ability to pass suggestions to Copilot cloud agent**. This automates creating a new pull request against your branch with the suggested fixes applied. Passing suggestions to Copilot cloud agent is in public preview and subject to change.

These capabilities are enabled automatically for all plans that include Copilot code review. See [Review effort level](<#review-effort-level>) later in this article for information about choosing between Lite and Balanced analysis levels.

If GitHub Actions is unavailable or if Actions workflows used by Copilot code review fail, reviews will still be generated. However, they will not include the additional features provided by the agentic capabilities.

### Usage of GitHub Actions runners for agentic capabilities in code review

Copilot code review uses GitHub Actions to run the agentic capabilities, including full project context gathering and passing suggestions to Copilot cloud agent. By default, Copilot code review uses standard GitHub-hosted runners. You can also upgrade to larger GitHub-hosted runners for better performance, or use self-hosted runners.

> \[!NOTE]
> Usage of larger GitHub-hosted runners is billed at a higher per-minute rate. Self-hosted runners do not consume GitHub Actions minutes.

You do not need to have GitHub Actions enabled in your organization or enterprise to use the agentic capabilities in code review.

If your organization has disabled GitHub-hosted runners, the agentic capabilities will not be available. In this case, code reviews will fall back to a more limited review. Organizations in this situation can use self-hosted runners.

For more information on configuring runners, see [Configuring runners for GitHub Copilot code review](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-runners>).

You can view the GitHub Actions minutes associated with Copilot code review runs. For more information, see [GitHub Actions minutes for code review](<https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing#pricing-and-usage-cost-considerations-for-copilot-code-review>).

### Estimated consumption

A review typically consumes an estimated $0.05 USD to $1 USD worth of AI credits with "Lite" effort, and $0.25 USD to $5 USD worth of AI credits with "Balanced" effort.

Consumption generally increases with pull request size and repository custom instructions, and the ranges may change as models evolve. These estimates do not include GitHub Actions minutes.

Repository and organization administrators can set the default review effort level for automatic code reviews. For configuration steps, see [Configuring code review by GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review#customizing-copilot-code-review-1>).

## Code review usage and billing

Each time Copilot reviews a pull request or reviews code in your IDE, the interaction consumes AI credits. The amount depends on the model used and the number of tokens processed.

Code reviews have two cost components: AI credits for the model interaction (the review itself), and GitHub Actions minutes for the agentic capabilities (context gathering and tool use). For more information on GitHub Actions usage, see [Usage of GitHub Actions runners for agentic capabilities in code review](<#usage-of-github-actions-runners-for-agentic-capabilities-in-code-review>).

By default, when a repository automatically requests a code review, AI credits consumption is attributed to the pull request author. If another user manually requests a review, consumption is attributed to that user instead.

For pull requests authored by Copilot cloud agent, usage is attributed first to the human co-author associated with the change. If the co-author cannot be billed, usage is billed directly to the organization.

For pull requests authored by other bots, or when a bot requests the review, usage is billed directly to the organization. These pull requests are eligible for agentic review.

For requests associated with organization members who have paid Copilot licenses, the member's entitlement is billed by default. An enterprise or organization owner can instead choose to bill the organization. Billing the organization requires AI credits paid usage, but a budget is optional. This choice applies to both manually requested and automatic code reviews, and changes billing only. On its own, it does not grant access to Copilot code review.

### Reviews requested with an external Copilot license

An external Copilot license is one that is not provided by the organization or enterprise that owns the repository, such as a personal license or a license from another organization. By default, people can use an external license to request a review from Copilot.

To prevent this, organization owners and repository administrators can enable the **Only allow Copilot code review to be triggered by authorized users** setting for an organization or a single repository. When the setting is enabled:

* Copilot is not offered as a reviewer to people who are not authorized, and API review requests from those people do not start a review.
* In personal repositories, only the repository owner or a direct collaborator can request a review.
* Automatic reviews from personal settings do not run for people who are not authorized. Automatic reviews configured by repository or organization rulesets still run.

For configuration steps, see [Configuring code review by GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review#customizing-copilot-code-review>).

### Users without a Copilot license or plan that includes Copilot code review

Users without access to Copilot code review do not have a monthly allowance of AI credits for it. This includes users who have no Copilot license and users on the Copilot Free plan, which does not include Copilot code review.

When Copilot code review is enabled for these users, any AI credits they consume are billed directly to the organization or enterprise as paid additional usage. This applies to both manually requested reviews and automatic code reviews.

AI credits consumed by these users are not attributed to any individual user's budget. They appear as additional usage in billing reports.

### What happens when a budget is reached

For Copilot Business and Copilot Enterprise, code review access is governed by the budgets that apply to the selected billing source. If usage is billed to a user, code reviews are blocked when the user reaches their budget. If usage is billed to an organization, code reviews are blocked when the applicable organization budget, cost center budget, or enterprise spending limit is exhausted. Code reviews are blocked along with other AI credits-consuming features. See [Budgets for usage-based billing](<https://docs.github.com/en/copilot/concepts/billing-and-usage/organizations-and-enterprises/budgets#what-happens-when-a-user-is-blocked>).

## Model usage

Copilot code review is a purpose-built product that uses a carefully tuned mix of models, prompts, and system behaviors to deliver consistent, high-quality feedback across a wide range of codebases. Model switching is not supported, as changing the model is likely to compromise reliability, user experience, and the quality of review comments.

> \[!NOTE]
> Copilot code review may use models that are not enabled on your organization's "Models" settings page. The "Models" settings page only controls Copilot Chat.
>
> Since Copilot code review is generally available, all model usage will be subject to the generally available terms. See [Managing policies and features for GitHub Copilot in your organization](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>).

## Automatic pull request reviews

By default, Copilot only reviews a pull request if you assign it to the pull request. However, you can configure automatic reviews.

* **Users** can configure Copilot to automatically review the pull requests they create. This is available on the Copilot Pro, Copilot Pro+, and Copilot Max plans, and with a Copilot Business or Copilot Enterprise license. It is not available for managed user accounts.
* **Repository owners** can configure Copilot to automatically review all pull requests in the repository that are created by people with access to Copilot.
* **Organization owners** can configure Copilot to automatically review all pull requests in some or all of the repositories in the organization where the pull request is created by a Copilot user.

Copilot evaluates pull requests against two separate configurations: a user's settings and any applicable ruleset. These configurations are not hierarchical; neither inherits from or overrides the other. A pull request is reviewed automatically when at least one configuration is enabled. If both configurations are enabled on a pull request, Copilot still posts only one review.

If your organization has enabled Copilot code review without a Copilot license, automatic reviews also apply to pull requests created by organization members without a license. This applies to repositories covered by a policy where automatic reviews are enabled. For more information, see [Copilot code review without a Copilot license](<#copilot-code-review-without-a-copilot-license>).

### Triggering an automatic pull request review

The triggers for automatic code review depend on the configuration settings.

* Basic setting:
  * When you create a pull request as an "Open" pull request.
  * The first time you switch a "Draft" pull request to "Open".
* Review new pushes:
  * Every time you push a new commit to the pull request.
* Review draft pull requests:
  * Pull requests are automatically reviewed while they are still drafts, before you switch them to "Open".

When a pull request qualifies for automatic review, **Review new pushes** and **Review draft pull requests** apply if either the author's settings or an applicable ruleset turns them on. You cannot use your own settings to turn off push or draft reviews that a ruleset has turned on.

For full instructions, see [Configuring code review by GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>).

> \[!NOTE]
> Unless Copilot has been configured to review each push to a pull request, it will only review a pull request once. If you make changes to the pull request after it has been automatically reviewed and you want Copilot to re-review it, you can request this manually. Click the <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-sync" aria-label="Re-request review" role="img"><path d="M1.705 8.005a.75.75 0 0 1 .834.656 5.5 5.5 0 0 0 9.592 2.97l-1.204-1.204a.25.25 0 0 1 .177-.427h3.646a.25.25 0 0 1 .25.25v3.646a.25.25 0 0 1-.427.177l-1.38-1.38A7.002 7.002 0 0 1 1.05 8.84a.75.75 0 0 1 .656-.834ZM8 2.5a5.487 5.487 0 0 0-4.131 1.869l1.204 1.204A.25.25 0 0 1 4.896 6H1.25A.25.25 0 0 1 1 5.75V2.104a.25.25 0 0 1 .427-.177l1.38 1.38A7.002 7.002 0 0 1 14.95 7.16a.75.75 0 0 1-1.49.178A5.5 5.5 0 0 0 8 2.5Z"></path></svg> button next to Copilot's name in the **Reviewers** menu.

## Review effort level

Copilot code review supports multiple Copilot review effort levels, so you can choose the level of thoroughness that matches the criticality of your code.

* **Lite**: Standard review. Provides fast, targeted feedback on common issues such as bugs, security vulnerabilities, and style inconsistencies.
* **Balanced**: Routes pull requests to a higher-reasoning model for longer analysis of complex logic, security-sensitive code, and cross-service changes (default). Balanced reviews use more AI credits, and may consume marginally more GitHub Actions minutes, than Lite reviews.

Use Balanced for security-sensitive code, multi-service pull requests, or repositories with strict quality standards. Use Lite for routine changes where fast feedback is more important than exhaustive analysis.

You can select Copilot review effort when requesting a review in the pull request, under the **Reviewers** section where Copilot appears as a reviewer. You can also set a default Copilot review effort in your Copilot code review settings. Organization owners can set a default Copilot review effort for automatic code reviews in their organization. Repository administrators can override the organization default for a specific repository.

When Copilot determines which review effort to use, it checks the following options in order and uses the first one that applies:

1. A Copilot review effort chosen when the review is requested
2. A Copilot review effort previously used on this pull request
3. The requestor's Copilot review effort. For a new pull request, the requestor is the author. When someone marks a draft ready for review, that person is the requestor.
4. A Copilot review effort set for the repository
5. A Copilot review effort set for the organization, or the repository owner's Copilot review effort on a user-owned repository
6. GitHub's built-in default, which is Balanced.

After Copilot code review reviews a pull request, the pull request overview comment shows the effort level used for each review run.

For configuration steps, see [Configuring code review by GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>).

## Copilot approvals

> \[!NOTE]
> Copilot approvals are in public preview and subject to change.

Every Copilot code review includes an approval assessment in the overview comment, indicating whether Copilot has determined the pull request ready to approve after reviewing it. By default, Copilot's reviews do not count toward required approvals for the pull request.

When Copilot approvals are enabled in repository, organization, and enterprise settings, Copilot can submit an approving review that satisfies your repository's required-approval rule the same way a teammate's approval would. If new commits are pushed after Copilot approves, the approval is dismissed, and you can re-request a review.

## MCP servers and agent skills for code review

Copilot code review can use repository-level agent skills and MCP servers when they are relevant to the review.

Copilot code review is more likely to use skills and MCP context when your repository or pull request gives clear signals, including review-focused skill directory names, custom instructions that reference MCP context, and pull request descriptions that include identifiers referencing configured MCP servers such as issue keys or incident IDs.

### Agent skills

If your repository includes agent skills, Copilot code review can automatically use relevant skills when reviewing a pull request, extending Copilot beyond its built-in analysis.

When reviewing a pull request, Copilot reads repository custom instructions, agent instructions, and agent skills from the head branch (the branch with your changes), not the base branch. For example, when merging `my-feature-branch` into `main`, Copilot uses the instructions and skills in `my-feature-branch`, so you can test changes to them in the same pull request without merging them first.

For setup details, see [Adding agent skills for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills>).

### MCP servers

Copilot code review can use MCP servers to pull context directly into the review from the third-party platforms and internal systems your team uses, including issue tracking, documentation, service catalogs, and incident tooling.

The GitHub MCP server and Playwright MCP server are enabled by default.

You can configure MCP servers in your repository settings. Repository MCP configuration on GitHub applies to both Copilot cloud agent and Copilot code review. Changes you make to repository MCP settings affect both features. For setup details, see [Configure MCP servers for your repository](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>).

In repository settings, **Allow Copilot to use MCP tools when reviewing pull requests** is enabled by default. Disable this setting if you want MCP servers available only for Copilot cloud agent, and not for Copilot code review. For step-by-step instructions, see [Configure MCP servers for your repository](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers#disabling-mcp-tools-for-code-review>).

## Excluded files

Some file types are excluded from Copilot code review:

* Dependency management files, such as package.json and Gemfile.lock
* Log files
* SVG files

If you include these file types in a pull request, Copilot code review will not review the file.

For more information, see [Files excluded from GitHub Copilot code review](<https://docs.github.com/en/copilot/reference/review-excluded-files>).

## Enhancing Copilot's knowledge of a repository

The more Copilot knows about the code in your repository, the tools you use, and your coding standards and practices, the more accurate and useful its reviews will become. You can enhance Copilot's knowledge of your repositories in two ways.

### Custom instructions

These are short, natural-language statements that you write and store as one or more files in a repository. If you are the owner of an organization on GitHub, you can also define custom instructions in the settings for your organization. For more information, see [About customizing GitHub Copilot responses](<https://docs.github.com/en/copilot/concepts/prompting/response-customization?tool=webui#about-repository-custom-instructions>).

### Choosing between custom instructions, AGENTS.md, and skills

Copilot code review can draw on several sources of customization, and each serves a different purpose. Use `.github/copilot-instructions.md` for repository-wide rules specific to Copilot, use path-specific `*.instructions.md` files under `.github/instructions/` for rules that apply only to certain files or directories, use `AGENTS.md` for standing rules you want to share across AI tools and agents, and use skills for task-specific workflows that Copilot runs on demand. The following table summarizes when to use each and how to provide rules.

| Use        | `copilot-instructions.md`                                  | Path-specific `*.instructions.md`                                                   | `AGENTS.md`                                                    | Skills                                                                                   |
| ---------- | ---------------------------------------------------------- | ----------------------------------------------------------------------------------- | -------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Best for   | Repository-wide, always-on rules for Copilot               | Always-on rules for specific paths, file types, or directories                      | Always-on rules shared across AI agents                        | Task-specific review workflows                                                           |
| Stored in  | `.github/copilot-instructions.md`                          | `.github/instructions/**/*.instructions.md`                                         | Repository root (`AGENTS.md`)                                  | `.github/skills/...`                                                                     |
| Examples   | Coding standards, architecture defaults, test expectations | `content/**` writing rules, `src/**` coding conventions, language-specific guidance | Shared repository conventions that should apply beyond Copilot | Reviews, releases, migrations, analysis                                                  |
| Activation | Automatic                                                  | Automatic when the changed files match the instruction scope                        | Automatic (read from repository root)                          | Automatic when relevant (e.g. review-focused skills such as `code-review`), or on demand |
| Scope      | Repository-wide and Copilot-specific                       | Repository sub-paths and Copilot-specific                                           | Cross-tool / agent-agnostic                                    | Invoked per task                                                                         |
| Rule       | "Copilot, always know this for this repository"            | "Copilot, always know this when working in these paths"                             | "Any agent, always know this"                                  | "Do this when needed"                                                                    |

For more information, see [Adding agent skills for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills>) and [Adding repository custom instructions for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

### Copilot Memory (public preview)

If you have a Copilot Pro, Copilot Pro+, or Copilot Max plan, you can enable Copilot Memory. This allows Copilot to store useful details it has learned about a repository. Copilot can then use this information when it reviews pull requests in that repository. For more information, see [About GitHub Copilot Memory](<https://docs.github.com/en/copilot/concepts/agents/copilot-memory>).

## Getting detailed code quality feedback across your repository

GitHub Copilot code review reviews the changes in a pull request and suggests fixes. To add systematic feedback on the reliability and maintainability of your code, on pull requests and across your default branch, enable GitHub Code Quality.

GitHub Code Quality complements Copilot code review by adding:

* **Rules-based  CodeQL-powered analysis** on pull requests and on your default branch.
* **Test-coverage metrics** on pull requests, so you can see whether a change maintains or reduces coverage.
* **One-click, Copilot-powered fixes**, including delegating remediation to Copilot cloud agent.
* **Optional merge gating** with rulesets, so pull requests with unresolved rules-based findings (or that miss a coverage threshold) can be blocked from merging.

For more information, see [GitHub Code Quality](<https://docs.github.com/en/code-security/concepts/code-quality/code-quality>).

## Validating Copilot code reviews

Copilot is not guaranteed to spot all problems or issues in a pull request. Sometimes it will make mistakes. Always validate Copilot's feedback carefully. Supplement Copilot's feedback with a human review.

For more information, see [Application card: GitHub Copilot Agents](<https://docs.github.com/en/copilot/responsible-use/agents>).

## Next steps

* [Using GitHub Copilot code review](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)
* [Configuring code review by GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>)


</details>
