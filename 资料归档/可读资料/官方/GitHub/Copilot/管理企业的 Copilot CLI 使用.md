# 管理企业的 Copilot CLI 使用

说明企业管理员如何管理 CLI 访问、模型、工具策略、审计日志和席位，并排查成员无法使用的问题；属于企业配置资料。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-cli/administer-copilot-cli-for-your-enterprise.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/管理企业的 Copilot CLI 使用.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Administering Copilot CLI for your enterprise
shortTitle: Administer for enterprise
intro: 'Control the use of {% data variables.copilot.copilot_cli_short %} within your enterprise.'
versions:
  feature: copilot
permissions: Enterprise owners and AI managers
contentType: how-tos
allowTitleToDifferFromFilename: true
category:
  - Manage Copilot for a team # Copilot discovery page
  - Administer Copilot CLI # Copilot CLI bespoke landing page
docsTeamMetrics:
  - copilot-cli
  - ai-governance
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data variables.copilot.copilot_cli_short %} 为开发人员的命令行带来了代理功能。启用{% data variables.copilot.copilot_cli_short %}后，开发者可以使用它来要求{% data variables.product.prodname_copilot_short %}在本地处理任务或将工作委托给{% data variables.copilot.copilot_cloud_agent %}。

## 启用或禁用{% data variables.copilot.copilot_cli_short %}

您可以通过配置策略来控制{% data variables.copilot.copilot_cli_short %}的使用。

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. 要管理 **{% data variables.product.prodname_copilot_short %}** 的策略，请在侧栏中单击 {% octicon "copilot" aria-hidden="true" aria-label="copilot" %} **{% data variables.product.prodname_copilot_short %}**。
1. 在“功能和客户端”下，单击 **配置功能和客户端**。
1. 在“客户端”部分中，对于 {% data variables.copilot.copilot_cli_short %}，选择您的首选策略。

> [!NOTE]
> 禁用{% data variables.copilot.copilot_cli_short %}不会禁用{% data variables.copilot.github_copilot_app %}。该应用程序受其自身政策的约束。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/github-copilot-app>)。

## 其他 AI 控件如何影响 {% data variables.copilot.copilot_cli_short %}？

并非所有企业级 AI 控制和策略都适用于 {% data variables.copilot.copilot_cli_short %}。这些是 ** 应用的控件 **：

### {% data variables.copilot.copilot_cli_short %} 启用

您可以在企业或组织级别启用或禁用{% data variables.copilot.copilot_cli_short %}。

### 选型

用户只能访问在企业级别启用的AI模型。当您在企业设置中启用或禁用模型时，这些更改将反映在 {% data variables.copilot.copilot_cli_short %} 中。用户可以使用`/model`命令查看哪些型号可供他们使用。

企业和组织所有者可以提供自定义模型的密钥。用户可以像任何其他模型一样选择这些模型：使用 {% data variables.copilot.copilot_cli_short %} 模型选择器、`--model` 标志或环境变量。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/enable-custom-models>)。

另外，用户还可以在本地提供自己的 LLM 密钥。这不受企业政策控制。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models>)。

### 定制【agents】

企业配置的自定义 agents 可与 {% data variables.copilot.copilot_cli_short %} 一起使用。

### 内容排除

在企业、组织和存储库级别配置的内容排除策略适用于 {% data variables.copilot.copilot_cli_short %}。排除的文件不用作上下文。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/context/content-exclusion>)。

### MCP服务器策略

企业和组织MCP政策适用于{% data variables.copilot.copilot_cli_short %}。您可以配置 MCP 注册表 URL，以便开发人员可以发现批准的服务器，并设置白名单策略来限制哪些 MCP 服务器可以运行。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-mcp-usage/restrict-based-on-registry>)。

### {% data variables.copilot.copilot_cloud_agent %} 启用

必须同时启用 {% data variables.copilot.copilot_cli_short %} 策略和 {% data variables.copilot.copilot_cloud_agent %} 策略，用户才能在 {% data variables.copilot.copilot_cli_short %} 中使用 `/delegate` 命令。

### 审计日志记录

影响 {% data variables.copilot.copilot_cli_short %} 的企业策略更新将作为事件记录在企业审核日志中。

### 座位分配

用户必须拥有分配的 {% data variables.product.prodname_copilot %} 席位才能访问 {% data variables.copilot.copilot_cli_short %}。

### 不适用的控制措施

所有其他控件 ** 不会 ** 影响 {% data variables.copilot.copilot_cli_short %}，特别是：

* **IDE-特定策略**：为特定 IDE 或编辑器扩展配置的策略

## 为什么我的开发人员无法访问 {% data variables.copilot.copilot_cli_short %}？

如果您希望用户有权访问 {% data variables.copilot.copilot_cli_short %} 但他们没有：

1. 确保用户拥有来自企业组织的有效 {% data variables.product.prodname_copilot %} 席位分配。
1. 验证 ** 企业级策略。** 如果您将该策略设置为“随处启用”或“随处禁用”，这将覆盖所有组织级别设置。
1. 如果企业策略设置为“让组织决定”，请检查用户接收其 {% data variables.product.prodname_copilot %} 许可证的组织。 {% data variables.copilot.copilot_cli_short %} 必须在 ** 中至少一个 ** 的组织中启用，并授予其 {% data variables.product.prodname_copilot %} 许可证。

确保所有组织的访问一致的一种方法是在企业级别将策略设置为**在各处启用**。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/cli-best-practices>)


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data variables.copilot.copilot_cli_short %} brings agentic capabilities to developers' command line. When {% data variables.copilot.copilot_cli_short %} is enabled, developers can use it to ask {% data variables.product.prodname_copilot_short %} to work on tasks locally or delegate work to {% data variables.copilot.copilot_cloud_agent %}.

## Enabling or disabling {% data variables.copilot.copilot_cli_short %}

You can control the use of {% data variables.copilot.copilot_cli_short %} by configuring a policy.

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. To manage policies for **{% data variables.product.prodname_copilot_short %}**, in the sidebar, click {% octicon "copilot" aria-hidden="true" aria-label="copilot" %} **{% data variables.product.prodname_copilot_short %}**.
1. Under "Features & clients," click **Configure features & clients**.
1. In the "Clients" section, for {% data variables.copilot.copilot_cli_short %}, select your preferred policy.

> [!NOTE]
> Disabling {% data variables.copilot.copilot_cli_short %} does not disable the {% data variables.copilot.github_copilot_app %}. The app is governed by its own policy. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/github-copilot-app>).

## How do other AI controls affect {% data variables.copilot.copilot_cli_short %}?

Not all enterprise-level AI controls and policies apply to {% data variables.copilot.copilot_cli_short %}. These are the controls that **do apply**:

### {% data variables.copilot.copilot_cli_short %} enablement

You can enable or disable {% data variables.copilot.copilot_cli_short %} at the enterprise or organization level.

### Model selection

Users can only access AI models that are enabled at the enterprise level. When you enable or disable models in your enterprise settings, those changes are reflected in {% data variables.copilot.copilot_cli_short %}. Users can view which models are available to them using the `/model` command.

Enterprise and organization owners can provide keys for custom models. Users can select these like any other model: with the {% data variables.copilot.copilot_cli_short %} model selector, the `--model` flag, or environment variables. See [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/enable-custom-models>).

Separately, users can also provide their own LLM keys locally. This is not controlled by enterprise policies. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models>).

### Custom agents

Enterprise-configured custom agents are available to use with {% data variables.copilot.copilot_cli_short %}.

### Content exclusion

Content exclusion policies configured at the enterprise, organization, and repository levels apply to {% data variables.copilot.copilot_cli_short %}. Excluded files are not used as context. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/context/content-exclusion>).

### MCP server policies

Enterprise and organization MCP policies apply to {% data variables.copilot.copilot_cli_short %}. You can configure an MCP registry URL so developers can discover approved servers, and set an allowlist policy to restrict which MCP servers can run. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-mcp-usage/restrict-based-on-registry>).

### {% data variables.copilot.copilot_cloud_agent %} enablement

Both the {% data variables.copilot.copilot_cli_short %} policy and the {% data variables.copilot.copilot_cloud_agent %} policy must be enabled for users to be able to use the `/delegate` command in {% data variables.copilot.copilot_cli_short %}.

### Audit logging

Updates to enterprise policies that affect {% data variables.copilot.copilot_cli_short %} are recorded as events in the enterprise audit log.

### Seat assignment

Users must have an assigned {% data variables.product.prodname_copilot %} seat to access {% data variables.copilot.copilot_cli_short %}.

### Controls that do not apply

All other controls do **not** affect {% data variables.copilot.copilot_cli_short %}, notably:

* **IDE-specific policies**: Policies configured for specific IDEs or editor extensions

## Why can't my developers access {% data variables.copilot.copilot_cli_short %}?

If you expect a user to have access to {% data variables.copilot.copilot_cli_short %} and they don't:

1. Ensure the user has a valid {% data variables.product.prodname_copilot %} seat assignment from an organization in your enterprise.
1. Verify the **enterprise-level policy.** If you set the policy to "Enabled everywhere" or "Disabled everywhere," this overrides all organization-level settings.
1. If the enterprise policy is set to "Let organizations decide," check the organizations where the user receives their {% data variables.product.prodname_copilot %} license. {% data variables.copilot.copilot_cli_short %} must be enabled in **at least one** of the organization granting them a {% data variables.product.prodname_copilot %} license.

One way to ensure consistent access across all organizations is to set the policy to **Enabled everywhere** at the enterprise level.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/cli-best-practices>)


</details>
