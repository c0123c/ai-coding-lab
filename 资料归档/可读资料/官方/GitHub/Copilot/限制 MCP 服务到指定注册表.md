# 限制 MCP 服务到指定注册表

介绍企业与组织如何配置注册表地址和允许策略，控制开发者在 IDE 和 CLI 中能发现及使用的服务。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-08-18](<https://github.com/github/docs/commit/9eaee0af43decd58b41a8ba821d1f1d04499064a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/administer-copilot/manage-mcp-usage/restrict-based-on-registry.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/限制 MCP 服务到指定注册表.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Restrict MCP server access to a custom registry
intro: You can configure an MCP registry URL and access control policy to determine which MCP servers developers can discover and use in supported IDEs and {% data variables.copilot.copilot_cli_short %}.
permissions: Enterprise owners and organization owners
product: '{% data variables.copilot.copilot_enterprise_short %} or {% data variables.copilot.copilot_business_short %}'
versions:
  feature: copilot
shortTitle: Restrict based on registry
redirect_from:
  - /copilot/how-tos/administer-copilot/configure-mcp-server-access
  - /copilot/how-tos/administer-copilot/manage-for-organization/set-extension-permissions
  - /copilot/how-tos/administer-copilot/manage-mcp-usage/configure-mcp-server-access
contentType: how-tos
category:
  - Manage Copilot for a team
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [!IMPORTANT] {% data reusables.copilot.mcp.registry-alternative %}

## 先决条件

在为您的公司完全配置 MCP 服务器访问之前，您需要创建一个 MCP 注册表。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-mcp-usage/configure-mcp-registry>)。

## 为企业配置MCP白名单策略

为了确保统一访问，您可以在企业级别设置和维护 MCP 注册表 URL 和许可名单策略。否则，如果您的团队有不同的需求，您应该为每个组织配置单独的策略。

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
{% data reusables.enterprise-accounts.view-mcp-policies %}
1. 确保 {% data variables.product.prodname_copilot_short %}** 中的 **MCP 服务器设置为 **随处启用 **。
1. 在 **MCP 注册表 URL** 部分中，输入注册表的 URL，然后单击 ** 保存 **。

    {% data reusables.copilot.mcp.azure-api-center-url %}

1. 在 **限制 MCP 访问注册表服务器 ** 部分中，选择下拉菜单，然后单击以下选项之一：
   * **全部允许**：无限制。所有MCP服务器都可以使用。
   * **仅限注册表**：仅注册表中的服务器可以运行。

您选择的策略将立即适用于您企业中的开发人员。

## 为组织配置 MCP 白名单策略

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
1. {% data reusables.user-settings.code-planning-automation %} 单击 **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**，然后单击 ** 策略 **。
1. 在“功能”部分中，确保 {% data variables.product.prodname_copilot_short %}** 中的 **MCP 服务器设置为 **已启用 **。
1. 在 **MCP 注册表 URL（可选）** 字段中，输入注册表的 URL，然后单击 ** 保存 **。

    {% data reusables.copilot.mcp.azure-api-center-url %}

1. 在 **限制 MCP 访问注册表服务器 ** 部分中，选择下拉菜单，然后单击以下选项之一：
   * **全部允许**：无限制。所有MCP服务器都可以使用。
   * **仅限注册表**：仅注册表中的服务器可以运行。

您选择的策略将立即应用于您组织中的开发人员。

## 后续步骤

有关 MCP 白名单实施和限制的详细信息，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/mcp-private-registry-enforcement>)。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/reference/supported-surfaces-for-policies>)


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!IMPORTANT] {% data reusables.copilot.mcp.registry-alternative %}

## Prerequisites

Before you can fully configure MCP server access for your company, you need to create an MCP registry. See [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-mcp-usage/configure-mcp-registry>).

## Configuring the MCP allowlist policy for an enterprise

To ensure uniform access, you can set and maintain your MCP registry URL and allowlist policy at the enterprise level. Otherwise, if your teams have different needs, you should configure separate policies for each organization.

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
{% data reusables.enterprise-accounts.view-mcp-policies %}
1. Ensure **MCP servers in {% data variables.product.prodname_copilot_short %}** is set to **Enabled everywhere**.
1. In the **MCP Registry URL** section, enter the URL of your registry, then click **Save**.

    {% data reusables.copilot.mcp.azure-api-center-url %}

1. In the **Restrict MCP access to registry servers** section, select the dropdown menu, then click one of the following options:
   * **Allow all**: No restrictions. All MCP servers can be used.
   * **Registry only**: Only servers from the registry may run.

    Your chosen policy will immediately apply to developers in your enterprise.

## Configuring the MCP allowlist policy for an organization

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
1. {% data reusables.user-settings.code-planning-automation %} click **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**, then click **Policies**.
1. In the "Features" section, ensure **MCP servers in {% data variables.product.prodname_copilot_short %}** is set to **Enabled**.
1. In the **MCP Registry URL (optional)** field, enter the URL of your registry, then click **Save**.

    {% data reusables.copilot.mcp.azure-api-center-url %}

1. In the **Restrict MCP access to registry servers** section, select the dropdown menu, then click one of the following options:
   * **Allow all**: No restrictions. All MCP servers can be used.
   * **Registry only**: Only servers from the registry may run.

    Your chosen policy will immediately apply to developers in your organization.

## Next steps

For detailed information on MCP allowlist enforcement and limitations, see [AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/mcp-private-registry-enforcement>).

## Further reading

* [AUTOTITLE](<https://github.com/copilot/reference/supported-surfaces-for-policies>)


</details>
