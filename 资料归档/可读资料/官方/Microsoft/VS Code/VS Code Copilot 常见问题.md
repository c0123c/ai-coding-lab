# VS Code Copilot 常见问题

解答账号、额度、补全、Agent、模型、仓库规模、会话延续和网络问题，提供排查与反馈入口。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/agent-troubleshooting/faq.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/agent-troubleshooting/faq.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code Copilot 常见问题.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: e02ded07-6e5a-4f94-b618-434a2c3e8f09
DateApproved: 10/7/2026
MetaDescription: Find answers about GitHub Copilot access, usage, chat, agents, inline suggestions, and troubleshooting in {% data variables.product.prodname_vscode %}.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# GitHub Copilot常见问题解答

在 {% data variables.product.prodname_vscode %} 中查找有关 GitHub Copilot 访问、使用、聊天、agents、内联建议和故障排除的答案。

## GitHub Copilot 订阅

### 我可以在不登录的情况下使用{% data variables.product.prodname_vscode_shortname %}吗？

是的。您不需要帐户即可使用 {% data variables.product.prodname_vscode_shortname %} 中的核心编辑器功能。

要使用 AI 功能，请选择以下访问方法之一：

* 使用包含您的 GitHub Copilot 套餐的 GitHub 帐户登录。这提供了计划中包含的型号、功能和使用限额。
* 配置 [ 自带密钥模型 ](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>) 以使用支持的聊天功能，无需 GitHub 登录。

某些功能（包括内联建议、语义搜索和嵌入）需要 GitHub Copilot 服务，并且无法通过自带密钥模型使用。

如果您不想使用AI，则无需登录即可继续使用{% data variables.product.prodname_vscode_shortname %}。若要隐藏内置AI功能，请参见[从{% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/setup/copilot.md#remove-ai-features-from-vs-code>)中删除AI功能。

### 我如何获得 Copilot 订阅？

有多种方法可以访问 GitHub Copilot：

|用户类型|描述|
|--------------------------------|-------------|
|个人|<ul><li>设置 {% data variables.copilot.copilot_free %}，免费探索基本功能，并每月获得内嵌建议和 AI 积分。</li><li>注册付费 GitHub Copilot 计划，以获得更大的灵活性并获得高级功能。</li><li>查看[为自己设置GitHub Copilot](<https://docs.github.com/en/copilot/setting-up-github-copilot/setting-up-github-copilot-for-yourself>)的所有选项。 </li></ul>|
|Organization/Enterprise 会员|<ul><li>如果您是订阅了 GitHub Copilot 的组织或企业的成员，您可以通过转至 <https://github.com/settings/copilot> 并在“从组织获取 Copilot”下请求访问来请求访问 Copilot。</li><li>参见[为您的组织设置 GitHub Copilot ](<https://docs.github.com/en/copilot/setting-up-github-copilot/setting-up-github-copilot-for-your-organization>) 为您的组织启用 Copilot。</li></ul>|

### 使用GitHub账户登录有什么好处？

使用有权访问GitHub Copilot的GitHub帐户登录具有以下好处：

* [更高的每月AI学分津贴](<https://docs.github.com/en/copilot/get-started/plans#comparing-copilot-plans>)
* [在聊天中访问高级语言模型](<https://docs.github.com/en/copilot/reference/ai-models/supported-models#supported-ai-models-per-copilot-plan>)超越自动模型选择
* [自带模型密钥](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>)（BYOK）访问更多模型
* [远程存储库索引和语义代码搜索](<https://github.com/docs/agents/reference/workspace-context.md#semantic-index-sources>)
* [Copilot 代码审查](<https://docs.github.com/en/copilot/concepts/agents/code-review>)
* [Copilot 内容排除](<https://docs.github.com/en/copilot/how-tos/configure-content-exclusion/exclude-content-from-copilot>)
* [将任务委托给{% data variables.copilot.copilot_cloud_agent %}](<https://github.com/docs/agents/run/agent-harnesses.md#cloud>)进行远程执行。

根据您的 Copilot 计划，您可能具有不同级别的访问权限和限制。有关更多信息，请参阅[GitHub Copilot计划](<https://docs.github.com/en/copilot/get-started/plans>)。

### 如何监控我的 Copilot 使用情况？

您可以通过 {% data variables.product.prodname_vscode_shortname %} 状态栏在 Copilot 状态仪表板中查看当前 Copilot 使用情况。仪表板显示以下信息：

* **内嵌建议**：您当月使用的内嵌建议配额百分比。付费计划对内嵌建议有无限配额。
* **AI 积分**：您当月已使用的每月AI 积分津贴的百分比。

请访问 GitHub Copilot 文档，了解有关 [ 监控使用情况和权利 ](<https://docs.github.com/en/copilot/managing-copilot/monitoring-usage-and-entitlements/monitoring-your-copilot-usage-and-entitlements>) 的更多信息。有关减少信用消耗的提示，请参阅[优化AI信用使用](<https://github.com/docs/agents/guides/optimize-usage.md>)。

### 我达到了内嵌建议或 AI 积分限制

您的内嵌建议津贴和 AI 积分津贴每月都会重置。如果您仅达到 AI 积分限制，您仍然可以使用内联建议。同样，如果您已达到内嵌建议的限制，您仍然可以使用聊天。

对于 {% data variables.copilot.copilot_free_short %} 上的用户，要获得更多内嵌建议和 AI 积分，您可以直接从 {% data variables.product.prodname_vscode_shortname %} 注册 [ 付费套餐 ](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>)。或者，您可以等到下个月继续免费使用Copilot。

如果您使用的是付费计划并用完您的 AI 积分，您可以设置额外使用的预算并继续工作，或者等到下一个每月周期您的津贴重置时。详细了解 [如果您超出 GitHub Copilot 文档中包含的 AI 积分 ](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals#what-happens-if-i-exceed-my-included-ai-credits>) 会发生什么情况。

有关减少信用消耗的提示，请参阅[优化AI信用使用](<https://github.com/docs/agents/guides/optimize-usage.md>)。

### 在 {% data variables.product.prodname_vscode_shortname %} 中未检测到我的 Copilot 订阅

要在聊天中使用您的 Copilot 计划，您必须使用有权访问该计划的 GitHub 帐户登录 {% data variables.product.prodname_vscode %}。

* 如果您的 Copilot 订阅与另一个 GitHub 帐户关联，请退出您的 GitHub 帐户并使用另一个帐户登录。使用活动栏中的 **帐户** 菜单注销您当前的 GitHub 帐户。请参阅 [使用不同的 GitHub 帐户与 Copilot](<https://github.com/docs/setup/copilot.md#use-a-different-github-account-with-copilot>) 了解更多信息。

* 验证您的 Copilot 订阅在 [GitHub Copilot 设置 ](<https://github.com/settings/copilot>) 中仍然有效。

* 如果您对 GHE.com 上的托管用户帐户使用 Copilot 套餐，则需要在登录前更新一些设置。请参阅[通过 GHE.com 上的帐户使用 GitHub Copilot](<https://docs.github.com/en/copilot/managing-copilot/configure-personal-settings/using-github-copilot-with-an-account-on-ghecom>)。

### 如何切换Copilot账户？

如果您的 Copilot 订阅与另一个 GitHub 帐户关联，请在 {% data variables.product.prodname_vscode_shortname %} 中注销您的 GitHub 帐户，然后使用另一个帐户登录。

请参阅[使用不同的GitHub帐户与Copilot](<https://github.com/docs/setup/copilot.md#use-a-different-github-account-with-copilot>)了解更多信息。

## 一般 Copilot 问题

### 如何从{% data variables.product.prodname_vscode_shortname %}中删除Copilot？

您可以使用 `setting(chat.disableAIFeatures)` 设置禁用 {% data variables.product.prodname_vscode_shortname %} 中的内置 AI 功能，类似于配置 {% data variables.product.prodname_vscode_shortname %} 中其他功能的方式。这会禁用和隐藏 {% data variables.product.prodname_vscode_shortname %} 中的聊天或内联建议等功能，并禁用 Copilot 扩展。您可以在工作区或用户级别配置设置。

或者，使用标题栏中聊天菜单中的 **了解如何隐藏 AI 功能 ** 操作来访问设置。

> [！NOTE]
> 如果您之前禁用了内置的AI功能，则在更新到新版本的{% data variables.product.prodname_vscode_shortname %}时，您的选择将受到尊重。

### Copilot 的网络和防火墙配置

* 配置您的防火墙或代理以允许 GitHub Copilot 所需的域、端口和协议。请参阅 [ 对 GitHub Copilot](<https://docs.github.com/en/copilot/troubleshooting-github-copilot/troubleshooting-firewall-settings-for-github-copilot>) 的防火墙设置进行故障排除。

* 如果您使用企业 VPN 或 HTTP 代理，请检查 Copilot 是否可以在没有连接或证书错误的情况下到达 GitHub。请参阅 [ 排查 GitHub Copilot](<https://docs.github.com/en/copilot/troubleshooting-github-copilot/troubleshooting-network-errors-for-github-copilot>) 的网络错误。

要识别失败的连接，[收集网络诊断信息](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#collect-network-diagnostics>)。

### 我的请求有速率限制

此错误意味着该服务暂时限制请求。即使您的每月 AI 积分限额未用完，也可能会出现利率限制。等待几分钟并重试该请求。

请参阅 [GitHub Copilot](<https://docs.github.com/en/copilot/troubleshooting-github-copilot/rate-limits-for-github-copilot>) 的速率限制，了解有关速率限制以及速率受限时该怎么做的更多信息。

### 是否有 Copilot 扩展的预发布版本？

是的，您可以切换到 Copilot 扩展的预发布（夜间）版本，以尝试最新的功能和修复。在扩展视图中，右键单击或选择齿轮图标以显示上下文菜单，然后选择 **切换到预发布版本**：

![带有切换到预发布版本选项的扩展视图上下文菜单](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/faq/switch-to-pre-release.png>)

您可以通过扩展详细信息中的“预发布”徽章来判断您是否正在运行预发布版本：

![GitHub Copilot 扩展的预发布版本](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/faq/copilot-ext-pre-release.png>)

## 内嵌建议

### 如何启用或禁用内联建议？

您可以使用 {% data variables.product.prodname_vscode_shortname %} 状态栏中的 Copilot 状态仪表板中的复选框来启用或禁用 {% data variables.product.prodname_vscode_shortname %} 中的内联建议。您可以全局或针对活动编辑器的文件类型启用或禁用内联建议。

![显示 {% data variables.product.prodname_vscode_shortname %} 状态栏的屏幕截图，突出显示指示 Copilot 处于活动状态的 Copilot 图标。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/faq/copilot-disable-completions.png>)

或者，使用 `setting(github.copilot.enable)` 和 `setting(github.copilot.nextEditSuggestions.enabled)` 设置分别启用或禁用内联建议和 {% data variables.copilot.next edit suggestions %}。您可以在工作区或用户级别配置这些设置。

### 内联建议在编辑器中不起作用

* 验证 [GitHub Copilot 未全局或针对该语言关闭 ](<#如何启用或禁用内联建议>)。
* 验证您的 [GitHub Copilot 订阅是否处于活动状态并已检测到 ](<#my-copilot-subscription-is-not-detected-in-vs-code>)。
* 验证您的 [ 网络设置 ](<#copilot-的网络和防火墙配置>) 是否允许连接到 GitHub Copilot。
* 验证您是否未达到 [{% data variables.copilot.copilot_free_short %} 计划 ](<https://docs.github.com/copilot/managing-copilot/managing-copilot-as-an-individual-subscriber/about-github-copilot-free>) 当月的内嵌建议限制。

如果这些检查通过，[查看GitHub Copilot日志](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#view-logs-for-github-copilot>)。

## 聊天和agents

### 聊天功能不适合我

检查以下要求：

* 运行**代码：检查更新**以更新{% data variables.product.prodname_vscode %}。
* 如果您使用 Copilot 计划，请验证 {% data variables.product.prodname_vscode_shortname %} 是否使用具有有效计划的 GitHub 帐户登录。
* 如果您使用自带密钥模型，请验证模型提供程序配置。某些聊天功能仍然需要 GitHub Copilot 服务。
* 检查 Copilot 状态仪表板是否已耗尽 AI 积分限额。如果请求报告临时速率限制，请等待几分钟并重试。
* 检查您的[网络和防火墙配置](<#copilot-的网络和防火墙配置>)。

如果这些检查通过，请按照 [ 基于症状的故障排除步骤 ](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#choose-a-troubleshooting-path>) 进行操作。

### Agents 在聊天中不可用

验证 agents 是否已通过 `setting(chat.agent.enabled)` 设置打开。您的组织可以使用策略来管理此设置。如果该设置不可用或无法管理，请联系您的管理员。

### agents在{% data variables.product.prodname_vscode_shortname %}中可以做什么？

Agents 自主处理完整的编码任务。他们计划多步骤实施，跨多个文件执行协调更改，运行终端命令，调用工具，并在遇到错误时进行自我纠正。使用 agents 用于功能实现、架构级重构、框架迁移、调试和测试生成。了解更多关于 [使用 agents](<https://github.com/docs/agents/overview.md>).

### Copilot 是否适用于大型代码库和 monorepos？

是的。 {% data variables.product.prodname_vscode_shortname %} 使用语义搜索、语言智能 (LSP) 和 GitHub 的代码搜索自动索引您的工作区，以提供对整个存储库的深入理解。对于大型存储库，[远程索引](<https://github.com/docs/agents/reference/workspace-context.md#semantic-index-sources>)使用GitHub的索引在相关存储库中获得快速、全面的结果。使用 [ 多根工作区 ](<https://github.com/docs/editing/workspaces/multi-root-workspaces.md>) 来确定 monorepos 中的上下文范围，并使用 [ 自定义指令 ](<https://github.com/docs/agent-customization/custom-instructions.md>) 来描述项目的架构。请参阅 [大型代码库的最佳实践 ](<https://github.com/docs/agents/best-practices.md#work-with-large-codebases>)。

### 我的组织可以控制 AI 功能和 agent 访问权限吗？

是的。组织管理员可以通过 [企业 AI 设置 ](<https://github.com/docs/enterprise/manage-ai-settings.md>) 和 [ 策略 ](<https://github.com/docs/enterprise/policies.md>) 管理 Copilot，包括启用或禁用 agents、控制模型访问、配置内容排除以及加强信任边界。请参阅 [GitHub Copilot 信任中心 ](<https://resources.github.com/copilot-trust-center/>) 了解合规性详细信息。

### agents 有使用限制吗？

Agents 消耗您的 Copilot 计划中的 AI 积分。付费计划包括每月 AI 积分津贴，并且您可以根据需要设置额外使用的预算。您可以跨本地、后台和云环境并行运行多个 agent 会话。免费计划的用户每月可获得 AI 积分津贴。详细了解 [ 基于使用情况的计费 ](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>) 和 [GitHub Copilot 计划 ](<https://docs.github.com/en/copilot/get-started/plans>)。

### 并非所有模型都在语言模型选择器中可用

您可以在语言模型选择器中选择可用的模型。了解如何[自定义语言模型选择器](<https://github.com/docs/agent-customization/language-models.md#customize-the-model-picker>)。

组织可以限制对某些模型的访问。如果您认为模型应该可用，请联系您的组织管理员。

### 如何防止{% data variables.copilot.chat_view %}自动打开？

默认情况下，{% data variables.copilot.chat_view %} 在辅助侧栏中打开。当您关闭工作区的 {% data variables.copilot.chat_view %} 时，{% data variables.product.prodname_vscode_shortname %} 会记住此设置，并且在您下次打开该工作区时不会自动打开 {% data variables.copilot.chat_view %}。

您可以直接从 {% data variables.copilot.chat_view %} 更改默认可见性：

1. 打开{% data variables.copilot.chat_view %}（`kb(workbench.action.chat.open)`）。
1. 选择{% data variables.copilot.chat_view %}右上角的`...`图标。
1. 选择**默认显示视图**以启用或禁用自动打开{% data variables.copilot.chat_view %}。

您还可以使用 `setting(workbench.secondarySideBar.defaultVisibility)` 设置控制辅助侧栏的默认可见性。将其设置为`hidden`以防止{% data variables.copilot.chat_view %}自动打开。

## 跨 {% data variables.product.prodname_copilot_short %} 经验开展工作

### 我可以在 {% data variables.product.prodname_vscode_shortname %} 中继续 {% data variables.copilot.copilot_cli_short %} 或 {% data variables.copilot.github_copilot_app_short %} 会话吗？

是，在会话列表过滤器中使用 **外部** > **全部** 来显示 {% data variables.copilot.chat_view %} 中的 [ 支持的来自其他应用程序的本地会话 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) 或{% data variables.copilot.agents_window %}。打开会话并发送消息以继续会话。然后，Agent 主机采用该会话，并且 **外部** 过滤器不再影响其可见性。

### 为什么我看不到已经开始的 {% data variables.product.prodname_copilot_short %} 会话？

来自其他应用程序的会话将被隐藏，直到您将 [**外部 ** 过滤器 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) 从 ** 无 ** 更改。 {% data variables.product.prodname_copilot_short %} 发现过去 7 天内更新的与存储库相关的会话。 **最近** 显示两个最近更新的外部会话，而**全部** 显示所有发现的会话，而不是每个过去的会话。

### 我可以在终端中继续 {% data variables.product.prodname_vscode_shortname %} {% data variables.product.prodname_copilot_short %} 会话吗？

是的，打开 {% data variables.product.prodname_copilot_short %} 会话的上下文菜单，然后选择 ** 在终端中恢复 ** 以使用 {% data variables.copilot.copilot_cli %} 继续它。了解[在集成终端](<https://github.com/docs/agents/run/agent-harnesses.md#use-copilot-cli-from-the-terminal>)中使用{% data variables.copilot.copilot_cli_short %}。

### {% data variables.copilot.chat_view %}和{% data variables.copilot.agents_window %}之间切换是否会启动新会话？

不会，在任一界面中打开相同的受支持的 Agent 主机会话会保留其对话、工具、工作区和工作树。 [切换表面](<https://github.com/docs/agents/concepts/sessions.md#sessions-across-surfaces>)不会创建、分叉或移交会话。

### 获取会话与同步其历史记录相同吗？

不需要，[本地会话发现](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>)可让您继续从其他应用程序支持的会话，而[历史记录同步](<https://github.com/docs/agents/run/sessions/session-history.md>)可通过您的{% data variables.product.prodname_github %}帐户提供记录的会话数据。同步受您的设置和组织策略的约束，并且其本身并不提供对正在运行的 agent 的控制。如需从其他设备进行实时监控或转向，请使用[远程agent访问](<https://github.com/docs/agents/run/remote-agent-sessions.md>)或[{% data variables.product.prodname_copilot_short %}远程控制](<https://github.com/docs/agents/run/agent-harnesses.md#remote-control-copilot-sessions>)。

## 故障排除和反馈

### 我如何提供有关 Copilot 的反馈？

使用与内联建议或下一个编辑建议关联的反馈操作。对于聊天和 agent 问题，请使用 {% data variables.product.prodname_vscode_shortname %} 问题报告器。

请参阅[报告未解决的问题](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#report-an-unresolved-issue>)，了解报告步骤、证据清单以及共享诊断数据的注意事项。请遵循 [Copilot 问题报告指南 ](<https://github.com/microsoft/vscode/wiki/Copilot-Issues>) 使报告具有可操作性。

## 其他资源

* [GitHub Copilot 信任中心](<https://resources.github.com/copilot-trust-center/>)
* [{% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/run/security.md>)中AI的安全注意事项
* GitHub 文档中的 [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# GitHub Copilot frequently asked questions

Find answers about GitHub Copilot access, usage, chat, agents, inline suggestions, and troubleshooting in {% data variables.product.prodname_vscode %}.

## GitHub Copilot subscription

### Can I use {% data variables.product.prodname_vscode_shortname %} without signing in?

Yes. You don't need an account to use the core editor features in {% data variables.product.prodname_vscode_shortname %}.

To use AI features, choose one of these access methods:

* Sign in with the GitHub account that has your GitHub Copilot plan. This provides the models, features, and usage allowances included with the plan.
* Configure a [bring-your-own-key model](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>) for supported chat features without GitHub sign-in.

Some features, including inline suggestions, semantic search, and embeddings, require the GitHub Copilot service and aren't available through bring-your-own-key models.

If you don't want to use AI, you can continue using {% data variables.product.prodname_vscode_shortname %} without signing in. To hide the built-in AI features, see [Remove AI features from {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/setup/copilot.md#remove-ai-features-from-vs-code>).

### How can I get a Copilot subscription?

There are different ways to get access to GitHub Copilot:

| Type of User                   | Description |
|--------------------------------|-------------|
| Individual                     | <ul><li>Set up {% data variables.copilot.copilot_free %} to explore basic functionality at no cost with a monthly allowance of inline suggestions and AI credits.</li><li>Sign up for a paid GitHub Copilot plan for more flexibility and access to premium features.</li><li>See [Setting up GitHub Copilot for yourself](<https://docs.github.com/en/copilot/setting-up-github-copilot/setting-up-github-copilot-for-yourself>) for all options. </li></ul> |
| Organization/Enterprise member | <ul><li>If you are a member of an organization or enterprise that has a subscription to GitHub Copilot, you can request access to Copilot by going to <https://github.com/settings/copilot> and requesting access under "Get Copilot from an organization."</li><li>See [Setting up GitHub Copilot for your organization](<https://docs.github.com/en/copilot/setting-up-github-copilot/setting-up-github-copilot-for-your-organization>) to enable Copilot for your organization.</li></ul> |

### What's the advantage of signing in with a GitHub account?

Signing in with a GitHub account that has access to GitHub Copilot has the following benefits:

* [Higher monthly AI credits allowance](<https://docs.github.com/en/copilot/get-started/plans#comparing-copilot-plans>)
* [Access to premium language models in chat](<https://docs.github.com/en/copilot/reference/ai-models/supported-models#supported-ai-models-per-copilot-plan>) beyond auto model selection
* [Bring your own model keys](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>) (BYOK) to access more models
* [Remote repository indexing and semantic code search](<https://github.com/docs/agents/reference/workspace-context.md#semantic-index-sources>)
* [Copilot code review](<https://docs.github.com/en/copilot/concepts/agents/code-review>)
* [Copilot content exclusions](<https://docs.github.com/en/copilot/how-tos/configure-content-exclusion/exclude-content-from-copilot>)
* [Delegate tasks to the {% data variables.copilot.copilot_cloud_agent %}](<https://github.com/docs/agents/run/agent-harnesses.md#cloud>) for remote execution.

Depending on your Copilot plan, you might have different levels of access and limits. See [GitHub Copilot plans](<https://docs.github.com/en/copilot/get-started/plans>) for more information.

### How can I monitor my Copilot usage?

You can view the current Copilot usage in the Copilot status dashboard, available through the {% data variables.product.prodname_vscode_shortname %} Status Bar. The dashboard shows the following information:

* **Inline suggestions**: The percentage of inline suggestions quota you have used in the current month. Paid plans have an unlimited quota for inline suggestions.
* **AI credits**: The percentage of your monthly AI credits allowance you have used in the current month.

Visit the GitHub Copilot documentation for more information about [monitoring usage and entitlements](<https://docs.github.com/en/copilot/managing-copilot/monitoring-usage-and-entitlements/monitoring-your-copilot-usage-and-entitlements>). For tips on reducing credit consumption, see [optimize AI credit usage](<https://github.com/docs/agents/guides/optimize-usage.md>).

### I reached my inline suggestions or AI credits limit

Your inline suggestions allowance and AI credits allowance reset every month. If you've only reached the AI credits limit, you can still use inline suggestions. Similarly, if you've reached the limit for inline suggestions, you can still use chat.

For users on {% data variables.copilot.copilot_free_short %}, to access more inline suggestions and AI credits, you can sign up for a [paid plan](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>) directly from {% data variables.product.prodname_vscode_shortname %}. Alternatively, you can wait until the next month to continue using Copilot for free.

If you're on a paid plan and exhaust your AI credits, you can set a budget for additional usage and keep working, or wait until the next monthly cycle when your allowance resets. Learn more about [what happens if you exceed your included AI credits](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals#what-happens-if-i-exceed-my-included-ai-credits>) in the GitHub Copilot documentation.

For tips on reducing credit consumption, see [optimize AI credit usage](<https://github.com/docs/agents/guides/optimize-usage.md>).

### My Copilot subscription is not detected in {% data variables.product.prodname_vscode_shortname %}

To use your Copilot plan in chat, you must be signed into {% data variables.product.prodname_vscode %} with the GitHub account that has access to the plan.

* If your Copilot subscription is associated with another GitHub account, sign out of your GitHub account and sign in with another account. Use the **Accounts** menu in the Activity Bar for signing out of your current GitHub account. See [Use a different GitHub account with Copilot](<https://github.com/docs/setup/copilot.md#use-a-different-github-account-with-copilot>) for more information.

* Verify that your Copilot subscription is still active in [GitHub Copilot settings](<https://github.com/settings/copilot>).

* If you're using a Copilot plan for a managed user account on GHE.com, you need to update some settings before you sign in. See [Using GitHub Copilot with an account on GHE.com](<https://docs.github.com/en/copilot/managing-copilot/configure-personal-settings/using-github-copilot-with-an-account-on-ghecom>).

### How can I switch accounts for Copilot?

If your Copilot subscription is associated with another GitHub account, sign out of your GitHub account in {% data variables.product.prodname_vscode_shortname %}, and sign in with another account.

See [Use a different GitHub account with Copilot](<https://github.com/docs/setup/copilot.md#use-a-different-github-account-with-copilot>) for more information.

## General Copilot questions

### How can I remove Copilot from {% data variables.product.prodname_vscode_shortname %}?

You can disable the built-in AI features in {% data variables.product.prodname_vscode_shortname %} with the `setting(chat.disableAIFeatures)` setting, similar to how you configure other features in {% data variables.product.prodname_vscode_shortname %}. This disables and hides features like chat or inline suggestions in {% data variables.product.prodname_vscode_shortname %} and disables the Copilot extensions. You can configure the setting at the workspace or user level.

Alternatively, use the **Learn How to Hide AI Features** action from the Chat menu in the title bar to access the setting.

> [!NOTE]
> If you have previously disabled the built-in AI features, your choice is respected upon updating to a new version of {% data variables.product.prodname_vscode_shortname %}.

### Network and firewall configuration for Copilot

* Configure your firewall or proxy to permit the domains, ports, and protocols required by GitHub Copilot. See [Troubleshoot firewall settings for GitHub Copilot](<https://docs.github.com/en/copilot/troubleshooting-github-copilot/troubleshooting-firewall-settings-for-github-copilot>).

* If you use a corporate VPN or HTTP proxy, check whether Copilot can reach GitHub without connection or certificate errors. See [Troubleshoot network errors for GitHub Copilot](<https://docs.github.com/en/copilot/troubleshooting-github-copilot/troubleshooting-network-errors-for-github-copilot>).

To identify a failing connection, [collect network diagnostics](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#collect-network-diagnostics>).

### My request is rate-limited

This error means that the service is temporarily limiting requests. A rate limit can occur even when your monthly AI credits allowance is not exhausted. Wait a few minutes and retry the request.

See [Rate limits for GitHub Copilot](<https://docs.github.com/en/copilot/troubleshooting-github-copilot/rate-limits-for-github-copilot>) for more information about rate limits and what to do if you are rate limited.

### Are there pre-release builds of the Copilot extensions?

Yes, you can switch to the pre-release (nightly) version of a Copilot extension to try the latest features and fixes. From the Extensions view, right-click or select the gear icon to bring up the context menu, and then select **Switch to Pre-Release Version**:

![Extensions view context menu with Switch to Pre-Release Version option](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/faq/switch-to-pre-release.png>)

You can tell if you're running a pre-release version by the "Pre-release" badge in the extension details:

![Pre-release version of the GitHub Copilot extension](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/faq/copilot-ext-pre-release.png>)

## Inline suggestions

### How do I enable or disable inline suggestions?

You can enable or disable inline suggestions in {% data variables.product.prodname_vscode_shortname %} by using the checkboxes in the Copilot status dashboard from the {% data variables.product.prodname_vscode_shortname %} Status Bar. You can enable or disable inline suggestions globally or for the file type of the active editor.

![Screenshot showing the {% data variables.product.prodname_vscode_shortname %} status bar, highlighting the Copilot icon that indicates Copilot is active.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/faq/copilot-disable-completions.png>)

Alternatively, use the `setting(github.copilot.enable)` and `setting(github.copilot.nextEditSuggestions.enabled)` settings to enable or disable inline suggestions and {% data variables.copilot.next edit suggestions %}, respectively. You can configure these settings at the workspace or user level.

### Inline suggestions are not working in the editor

* Verify that [GitHub Copilot is not turned off](<#how-do-i-enable-or-disable-inline-suggestions>) globally or for this language.
* Verify that your [GitHub Copilot subscription is active and detected](<#my-copilot-subscription-is-not-detected-in-vs-code>).
* Verify that your [network settings](<#network-and-firewall-configuration-for-copilot>) permit connectivity to GitHub Copilot.
* Verify that you have not reached the inline suggestions limit for the month with the [{% data variables.copilot.copilot_free_short %} plan](<https://docs.github.com/copilot/managing-copilot/managing-copilot-as-an-individual-subscriber/about-github-copilot-free>).

If these checks pass, [view the GitHub Copilot logs](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#view-logs-for-github-copilot>).

## Chat and agents

### Chat features aren't working for me

Check the following requirements:

* Run **Code: Check for Updates** to update {% data variables.product.prodname_vscode %}.
* If you use a Copilot plan, verify that {% data variables.product.prodname_vscode_shortname %} is signed in with the GitHub account that has the active plan.
* If you use a bring-your-own-key model, verify the model provider configuration. Some chat features still require the GitHub Copilot service.
* Check the Copilot status dashboard for an exhausted AI credits allowance. If the request reports a temporary rate limit, wait a few minutes and retry it.
* Check your [network and firewall configuration](<#network-and-firewall-configuration-for-copilot>).

If these checks pass, follow the [symptom-based troubleshooting steps](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#choose-a-troubleshooting-path>).

### Agents are not available in chat

Verify that agents are turned on with the `setting(chat.agent.enabled)` setting. Your organization can manage this setting with a policy. If the setting is unavailable or managed, contact your administrator.

### What can agents do in {% data variables.product.prodname_vscode_shortname %}?

Agents handle complete coding tasks autonomously. They plan multi-step implementations, execute coordinated changes across multiple files, run terminal commands, invoke tools, and self-correct when they encounter errors. Use agents for feature implementation, architecture-level refactoring, framework migrations, debugging, and test generation. Learn more about [using agents](<https://github.com/docs/agents/overview.md>).

### Does Copilot work with large codebases and monorepos?

Yes. {% data variables.product.prodname_vscode_shortname %} automatically indexes your workspace using semantic search, language intelligence (LSP), and GitHub's code search to provide deep understanding across your repository. For large repositories, [remote indexing](<https://github.com/docs/agents/reference/workspace-context.md#semantic-index-sources>) uses GitHub's index for fast, comprehensive results across related repositories. Use [multi-root workspaces](<https://github.com/docs/editing/workspaces/multi-root-workspaces.md>) to scope context in monorepos, and [custom instructions](<https://github.com/docs/agent-customization/custom-instructions.md>) to describe your project's architecture. See [best practices for large codebases](<https://github.com/docs/agents/best-practices.md#work-with-large-codebases>).

### Can my organization control AI features and agent access?

Yes. Organization administrators can manage Copilot through [enterprise AI settings](<https://github.com/docs/enterprise/manage-ai-settings.md>) and [policies](<https://github.com/docs/enterprise/policies.md>), including enabling or disabling agents, controlling model access, configuring content exclusions, and enforcing trust boundaries. See the [GitHub Copilot Trust Center](<https://resources.github.com/copilot-trust-center/>) for compliance details.

### Are agents usage-limited?

Agents consume AI credits from your Copilot plan. Paid plans include a monthly AI credits allowance, and you can set a budget for additional usage if needed. You can run multiple agent sessions in parallel across local, background, and cloud environments. Users on the free plan have a monthly AI credits allowance. Learn more about [usage-based billing](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>) and [GitHub Copilot plans](<https://docs.github.com/en/copilot/get-started/plans>).

### Not all models are available in the language model picker

You can choose which models are available in the language model picker. Learn how to [customize the language model picker](<https://github.com/docs/agent-customization/language-models.md#customize-the-model-picker>).

Organizations can restrict access to certain models. If you believe a model should be available, contact your organization administrator.

### How do I prevent the {% data variables.copilot.chat_view %} from opening automatically?

By default, the {% data variables.copilot.chat_view %} opens in the Secondary Side Bar. When you close the {% data variables.copilot.chat_view %} for a workspace, {% data variables.product.prodname_vscode_shortname %} remembers this setting and does not open the {% data variables.copilot.chat_view %} automatically the next time you open that workspace.

You can change the default visibility directly from the {% data variables.copilot.chat_view %}:

1. Open the {% data variables.copilot.chat_view %} (`kb(workbench.action.chat.open)`).
1. Select the `...` icon in the top-right corner of the {% data variables.copilot.chat_view %}.
1. Select **Show View by Default** to enable or disable the automatic opening of the {% data variables.copilot.chat_view %}.

You can also control the default visibility of the Secondary Side Bar with the `setting(workbench.secondarySideBar.defaultVisibility)` setting. Set it to `hidden` to prevent the {% data variables.copilot.chat_view %} from opening automatically.

## Working across {% data variables.product.prodname_copilot_short %} experiences

### Can I continue a {% data variables.copilot.copilot_cli_short %} or {% data variables.copilot.github_copilot_app_short %} session in {% data variables.product.prodname_vscode_shortname %}?

Yes, use **External** > **All** in the sessions list filter to show [supported local sessions from other applications](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) in the {% data variables.copilot.chat_view %} or {% data variables.copilot.agents_window %}. Open a session and send a message to continue it. The Agent Host then adopts the session, and the **External** filter no longer affects its visibility.

### Why can't I see a {% data variables.product.prodname_copilot_short %} session I already started?

Sessions from other applications are hidden until you change the [**External** filter](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) from **None**. {% data variables.product.prodname_copilot_short %} discovers repository-associated sessions updated within the last seven days. **Recent** shows the two most recently updated external sessions, whereas **All** shows all discovered sessions, not every past session.

### Can I continue a {% data variables.product.prodname_vscode_shortname %} {% data variables.product.prodname_copilot_short %} session in the terminal?

Yes, open a {% data variables.product.prodname_copilot_short %} session's context menu and select **Resume in Terminal** to continue it with {% data variables.copilot.copilot_cli %}. Learn about [using {% data variables.copilot.copilot_cli_short %} in the integrated terminal](<https://github.com/docs/agents/run/agent-harnesses.md#use-copilot-cli-from-the-terminal>).

### Does switching between the {% data variables.copilot.chat_view %} and {% data variables.copilot.agents_window %} start a new session?

No, opening the same supported Agent Host session in either interface keeps its conversation, harness, workspace, and worktree. [Switching surfaces](<https://github.com/docs/agents/concepts/sessions.md#sessions-across-surfaces>) does not create, fork, or hand off a session.

### Is picking up a session the same as syncing its history?

No, [local session discovery](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) lets you continue supported sessions from another application, while [history sync](<https://github.com/docs/agents/run/sessions/session-history.md>) makes recorded session data available through your {% data variables.product.prodname_github %} account. Sync is subject to your settings and organization policies and does not by itself provide control over a running agent. For live monitoring or steering from another device, use [remote agent access](<https://github.com/docs/agents/run/remote-agent-sessions.md>) or [{% data variables.product.prodname_copilot_short %} remote control](<https://github.com/docs/agents/run/agent-harnesses.md#remote-control-copilot-sessions>).

## Troubleshooting and feedback

### How can I provide feedback on Copilot?

Use the feedback action associated with an inline suggestion or next edit suggestion. For chat and agent issues, use the {% data variables.product.prodname_vscode_shortname %} Issue Reporter.

See [Report an unresolved issue](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md#report-an-unresolved-issue>) for the reporting steps, evidence checklist, and precautions for sharing diagnostic data. Follow the [Copilot issue-reporting guidelines](<https://github.com/microsoft/vscode/wiki/Copilot-Issues>) to make the report actionable.

## Additional resources

* [GitHub Copilot Trust Center](<https://resources.github.com/copilot-trust-center/>)
* [Security considerations for AI in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/run/security.md>)
* [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>) in the GitHub documentation


</details>
