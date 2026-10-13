# 管理 Copilot 云端沙箱访问

说明组织和企业如何通过策略开放或关闭云端沙箱，供部署与权限管理时参考。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/github/docs/commit/03b432e6d532db63f40312771e3ba8212d1995c0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/cloud-and-local-sandboxes/enabling-or-disabling-cloud-sandboxes-for-your-organization.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/管理 Copilot 云端沙箱访问.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Enabling or disabling cloud sandboxes for your organization or enterprise
shortTitle: Enable or disable cloud sandboxes
allowTitleToDifferFromFilename: true
intro: 'You can control whether members of your organization or enterprise can use cloud sandboxes by managing the cloud sandbox access policy in your organization or enterprise settings.'
permissions: Enterprise owners and organization owners
redirect_from:
  - /copilot/how-tos/cloud-and-local-sandboxes/enabling-or-disabling-cloud-and-local-sandboxes-for-your-organization
versions:
  feature: copilot
contentType: how-tos
category:
  - Manage cloud and local sandboxes for your organization
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [!NOTE]
> {% data reusables.cli.public-preview-cloud-sandbox %}

## 关于启用和禁用云沙箱

组织所有者可以通过配置云沙箱访问策略来控制组织成员是否有权访问云沙箱。企业所有者可以控制企业中所有组织的云沙箱访问权限。默认情况下，云沙箱访问处于禁用状态。

有关云沙箱的更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>)。

## 为您的组织启用或禁用云沙箱

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
1. {% data reusables.user-settings.code-planning-automation %} 单击**沙箱**。
1. 在“云沙盒访问”下，选择您的首选设置：
   * **已禁用**：组织成员无法访问云沙箱。
   * **启用**：该组织的成员可以使用云沙箱。
1. 单击**保存**。

## 为您的企业启用或禁用云沙箱

企业所有者可以通过企业设置的**策略**选项卡控制企业中所有组织的云沙箱访问。默认情况下，企业中的所有组织都禁用云沙箱访问。

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.policies-tab %}
1. 单击**沙箱**。
1. 在“云沙盒访问”下，选择您的首选设置：
   * **已禁用**：此企业中的任何组织都无法访问云沙箱。
   * **让组织启用（选择加入）**：组织所有者可以选择为其成员启用云沙箱访问权限。新组织默认为禁用。
   * **默认为所有组织启用（选择退出）**：为所有组织启用云沙箱访问。如果需要，组织所有者仍然可以禁用其组织的访问权限。
1. 单击**保存**。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>)
* [AUTOTITLE](<https://github.com/billing/concepts/product-billing/cloud-and-local-sandboxes>)
* [AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings#sandbox>)


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE]
> {% data reusables.cli.public-preview-cloud-sandbox %}

## About enabling and disabling cloud sandboxes

Organization owners can control whether members of their organization have access to cloud sandboxes by configuring the cloud sandbox access policy. Enterprise owners can control cloud sandbox access for all organizations in the enterprise. By default, cloud sandbox access is disabled.

For more information about cloud sandboxes, see [AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>).

## Enabling or disabling cloud sandboxes for your organization

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
1. {% data reusables.user-settings.code-planning-automation %} click **Sandboxes**.
1. Under "Cloud sandbox access", select your preferred setting:
   * **Disabled**: Cloud sandbox access is not available for organization members.
   * **Enabled**: Members of this organization can use cloud sandboxes.
1. Click **Save**.

## Enabling or disabling cloud sandboxes for your enterprise

Enterprise owners can control cloud sandbox access for all organizations in the enterprise from the **Policies** tab of the enterprise settings. By default, cloud sandbox access is disabled for all organizations in the enterprise.

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.policies-tab %}
1. Click **Sandboxes**.
1. Under "Cloud sandbox access", select your preferred setting:
   * **Disabled**: Cloud sandbox access is not available for any organizations in this enterprise.
   * **Let organizations enable (opt-in)**: Organization owners can choose to enable cloud sandbox access for their members. New organizations default to disabled.
   * **Enabled by default for all organizations (opt-out)**: Cloud sandbox access is enabled for all organizations. Organization owners can still disable access for their organization if needed.
1. Click **Save**.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>)
* [AUTOTITLE](<https://github.com/billing/concepts/product-billing/cloud-and-local-sandboxes>)
* [AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings#sandbox>)


</details>
