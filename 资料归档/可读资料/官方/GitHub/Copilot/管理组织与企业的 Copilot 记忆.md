# 管理组织与企业的 Copilot 记忆

说明启用与管理共享记忆的方法，包括仓库事实、用户偏好和导出等操作；属于管理员控制记忆使用的资料。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：源码提交（存档版）：[2026-08-24](<https://github.com/github/docs/commit/534cc76e811aaacae4cb26010cc16501c879a33f>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/use-copilot-agents/copilot-memory/manage-as-administrator.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/管理组织与企业的 Copilot 记忆.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Managing Copilot Memory for an organization or enterprise
shortTitle: Manage as administrator
intro: Manage {% data variables.copilot.copilot_memory %} settings and stored memories for an organization or enterprise.
product: 'Enterprises and organizations with a {% data variables.copilot.copilot_enterprise_short %} or {% data variables.copilot.copilot_business_short %} plan.'
permissions: Enterprise, organization, or repository administrators.
versions:
  feature: copilot
contentType: how-tos
category:
  - Manage Copilot for a team
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data reusables.copilot.memory-how-to-intro %}

## 启用{% data variables.copilot.copilot_memory %}

对于企业和组织管理的 {% data variables.product.prodname_copilot_short %} 订阅，{% data variables.copilot.copilot_memory %} 默认处于关闭状态，必须在企业或组织设置中启用。

一旦启用{% data variables.copilot.copilot_memory %}，该功能默认为用户开启。 {% data variables.copilot.copilot_memory %} 将用于用户使用受支持的 {% data variables.product.prodname_copilot_short %} 功能的任何存储库，并且它将保存存储库级别的事实和用户级别的首选项。个人用户可以在其帐户设置中选择退出 {% data variables.copilot.copilot_memory %}。

> [!NOTE]
> 如果一个用户被多个组织分配了 {% data variables.product.prodname_copilot_short %} 订阅，则适用最严格的设置 — 即，除非所有这些组织都启用了此功能，否则不会使用 {% data variables.copilot.copilot_memory %}。

### 为企业启用{% data variables.copilot.copilot_memory %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. 在侧边栏中，单击 **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**。
1. 在“功能和客户端”下，单击 **配置功能和客户端**。
1. 在“功能”下，向下滚动到 **{% data variables.copilot.copilot_memory %}** 设置，然后从下拉列表中选择一个策略。

### 为组织启用 {% data variables.copilot.copilot_memory %}

组织所有者可以为具有 {% data variables.product.prodname_copilot_short %} 许可证的组织的所有成员启用或禁用 {% data variables.copilot.copilot_memory %}。

如果组织属于企业，则组织所有者启用或禁用 {% data variables.copilot.copilot_memory %} 的能力可以由企业级策略控制。

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
1. {% data reusables.user-settings.code-planning-automation %} 单击 **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**，然后单击 ** 策略 **。
1. 在“功能”下，向下滚动到 **{% data variables.copilot.copilot_memory %}** 的设置。
1. 单击下拉按钮并选择**启用**。

{% data variables.copilot.copilot_memory %} 已为拥有 {% data variables.product.prodname_copilot_short %} 许可证的组织中的所有成员启用。

## 管理存储库级事实

作为正在使用 {% data variables.copilot.copilot_memory %} 的存储库的管理员，您可以查看当前存储的存储库级事实。如果您认为任何内容不适当、具有误导性或不正确，您可以将其删除。

{% data reusables.copilot.manage-repo-memories %}

## 管理组织的用户级别首选项

作为管理员，您可以导出或删除您的组织作为活动计费实体生成的所有用户级别首选项。有关所有权模型的更多信息，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/copilot-memory#user-level-preferences>)。

### 导出用户级别首选项

您可以以 JSONL 格式导出用户级别首选项。您可以同时为所有人或单个用户执行此操作。

{% data reusables.organizations.navigate-to-org %}
{% data reusables.organizations.org_settings %}
1. {% data reusables.user-settings.code-planning-automation %}单击**{% octicon "copilot" aria-hidden="true" aria-label="copilot" %}{% data variables.product.prodname_copilot_short %}**，然后单击**访问**。
1. 从这里，您可以导出每个人或单个用户的首选项。

   * 对于个人用户：在用户名旁边，单击 **{% octicon "kebab-horizontal" aria-label="Open seat options" %}**，然后单击 ** 导出 Copilot 内存 **。
   * 对于所有人：单击**获取使用情况报告**下拉列表，然后单击**下载用户记忆**。

{% data reusables.copilot.memory-export %}

### 删除用户级别首选项

您可以批量删除或删除单个用户的用户级别首选项。

{% data reusables.organizations.navigate-to-org %}
{% data reusables.organizations.org_settings %}
1. {% data reusables.user-settings.code-planning-automation %}单击**{% octicon "copilot" aria-hidden="true" aria-label="copilot" %}{% data variables.product.prodname_copilot_short %}**，然后单击**访问**。
1. 从这里，您可以删除每个人或单个用户的首选项。

   * 对于个人用户：在用户名旁边，单击**{% octicon "kebab-horizontal" aria-label="Open seat options" %}**，然后单击**删除Copilot内存**。
   * 对于所有人：单击**获取使用情况报告**下拉列表，然后单击**删除用户记忆**。

## 管理企业的用户级别首选项

作为管理员，您可以导出或删除您的企业作为活动计费实体生成的所有用户级别首选项。有关所有权模型的更多信息，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/copilot-memory#user-level-preferences>)。

### 导出用户级别首选项

您可以以 JSONL 格式导出用户级别首选项。您可以同时为所有人或单个用户执行此操作。

{% data reusables.enterprise-accounts.copilot-licensing %}
1. 从这里，您可以导出每个人或单个用户的首选项。

   * 对于个人用户：在用户名旁边，单击 **{% octicon "kebab-horizontal" aria-label="Open seat options" %}**，然后单击 ** 导出 Copilot 内存 **。
   * 对于所有人：单击**获取使用情况报告**下拉列表，然后单击**下载用户记忆**。

{% data reusables.copilot.memory-export %}

### 删除用户级别首选项

您可以批量删除或删除单个用户的用户级别首选项。

{% data reusables.enterprise-accounts.copilot-licensing %}
1. 从这里，您可以删除每个人或单个用户的首选项。

   * 对于个人用户：在用户名旁边，单击**{% octicon "kebab-horizontal" aria-label="Open seat options" %}**，然后单击**删除Copilot内存**。
   * 对于所有人：单击**获取使用情况报告**下拉列表，然后单击**删除用户记忆**。

## 审核{% data variables.copilot.copilot_memory %}

当管理员导出或删除内存以及用户选择退出 {% data variables.copilot.copilot_memory %} 时，事件会出现在您的组织或企业的审核日志中。参见[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-enterprise/review-audit-logs>)。

## 进一步阅读

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/use-copilot-agents/copilot-memory/manage-for-yourself>)


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data reusables.copilot.memory-how-to-intro %}

## Enabling {% data variables.copilot.copilot_memory %}

For enterprise- and organization-managed {% data variables.product.prodname_copilot_short %} subscriptions, {% data variables.copilot.copilot_memory %} is off by default and must be enabled in the enterprise or organization settings.

Once {% data variables.copilot.copilot_memory %} is enabled, the feature is on for users by default. {% data variables.copilot.copilot_memory %} will be used in any repository in which users use supported {% data variables.product.prodname_copilot_short %} features, and it will save both repository-level facts and user-level preferences. Individual users can opt out of {% data variables.copilot.copilot_memory %} in their account settings.

> [!NOTE]
> If a user is assigned a {% data variables.product.prodname_copilot_short %} subscription by more than one organization, the most restrictive setting applies—that is, {% data variables.copilot.copilot_memory %} will not be used unless all of those organizations have enabled this feature.

### Enabling {% data variables.copilot.copilot_memory %} for an enterprise

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. In the sidebar, click **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**.
1. Under "Features & clients," click **Configure features & clients**.
1. Under "Features", scroll down to the **{% data variables.copilot.copilot_memory %}** setting and select a policy from the dropdown.

### Enabling {% data variables.copilot.copilot_memory %} for an organization

Organization owners can enable or disable {% data variables.copilot.copilot_memory %} for all members of the organization with a {% data variables.product.prodname_copilot_short %} license.

If the organization belongs to an enterprise, the ability for organization owners to enable or disable {% data variables.copilot.copilot_memory %} may be controlled by the enterprise-level policy.

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
1. {% data reusables.user-settings.code-planning-automation %} click **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**, then click **Policies**.
1. Under "Features", scroll down to the setting for **{% data variables.copilot.copilot_memory %}**.
1. Click the dropdown button and select **Enabled**.

   {% data variables.copilot.copilot_memory %} is enabled for all members of the organization who have a {% data variables.product.prodname_copilot_short %} license.

## Managing repository-level facts

As an administrator of a repository in which {% data variables.copilot.copilot_memory %} is in use, you can review the currently stored repository-level facts. If you think any are inappropriate, misleading, or incorrect, you can delete them.

{% data reusables.copilot.manage-repo-memories %}

## Managing user-level preferences for an organization

As an administrator, you can export or delete all user-level preferences that were generated with your organization as the active billing entity. For more information on the ownership model, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/copilot-memory#user-level-preferences>).

### Exporting user-level preferences

You can export user-level preferences in JSONL format. You can do this for everyone at once or for individual users.

{% data reusables.organizations.navigate-to-org %}
{% data reusables.organizations.org_settings %}
1. {% data reusables.user-settings.code-planning-automation %} click **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**, then click **Access**.
1. From here, you can export preferences for everyone or for an individual user.

   * For an individual user: Next to the user's name, click **{% octicon "kebab-horizontal" aria-label="Open seat options" %}**, then click **Export Copilot memories**.
   * For everyone: Click the **Get usage report** dropdown, then click **Download user memories**.

{% data reusables.copilot.memory-export %}

### Deleting user-level preferences

You can delete user-level preferences in bulk or for individual users.

{% data reusables.organizations.navigate-to-org %}
{% data reusables.organizations.org_settings %}
1. {% data reusables.user-settings.code-planning-automation %} click **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**, then click **Access**.
1. From here, you can delete preferences for everyone or for an individual user.

   * For an individual user: Next to the user's name, click **{% octicon "kebab-horizontal" aria-label="Open seat options" %}**, then click **Delete Copilot memories**.
   * For everyone: Click the **Get usage report** dropdown, then click **Delete user memories**.

## Managing user-level preferences for an enterprise

As an administrator, you can export or delete all user-level preferences that were generated with your enterprise as the active billing entity. For more information on the ownership model, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/copilot-memory#user-level-preferences>).

### Exporting user-level preferences

You can export user-level preferences in JSONL format. You can do this for everyone at once or for individual users.

{% data reusables.enterprise-accounts.copilot-licensing %}
1. From here, you can export preferences for everyone or for an individual user.

   * For an individual user: Next to the user's name, click **{% octicon "kebab-horizontal" aria-label="Open seat options" %}**, then click **Export Copilot memories**.
   * For everyone: Click the **Get usage report** dropdown, then click **Download user memories**.

{% data reusables.copilot.memory-export %}

### Deleting user-level preferences

You can delete user-level preferences in bulk or for individual users.

{% data reusables.enterprise-accounts.copilot-licensing %}
1. From here, you can delete preferences for everyone or for an individual user.

   * For an individual user: Next to the user's name, click **{% octicon "kebab-horizontal" aria-label="Open seat options" %}**, then click **Delete Copilot memories**.
   * For everyone: Click the **Get usage report** dropdown, then click **Delete user memories**.

## Auditing {% data variables.copilot.copilot_memory %}

Events appear in your organization or enterprise's audit log when an administrator exports or deletes memories, and when a user opts out of {% data variables.copilot.copilot_memory %}. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-enterprise/review-audit-logs>).

## Further reading

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/use-copilot-agents/copilot-memory/manage-for-yourself>)


</details>
