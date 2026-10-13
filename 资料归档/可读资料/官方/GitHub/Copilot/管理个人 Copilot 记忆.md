# 管理个人 Copilot 记忆

介绍查看、删除和启停保存的开发偏好与仓库事实，并说明默认计费主体等相关设置。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：源码提交（存档版）：[2026-06-02](<https://github.com/github/docs/commit/e0679b208251b643fdf7cbcc1c6189caa16016c4>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/use-copilot-agents/copilot-memory/manage-for-yourself.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/管理个人 Copilot 记忆.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Managing Copilot Memory for your personal account
shortTitle: Manage for yourself
intro: Review and manage the coding conventions, preferences, and other details that {% data variables.product.prodname_copilot_short %} has stored from your interactions.
product: 'Individual users on a paid {% data variables.product.prodname_copilot_short %} plan.<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=engagement&ref_style=button&utm_source=docs-web-copilot-memory-how-to-signup&utm_medium=docs&utm_campaign=dec25postuniverse" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: how-tos
category:
  - Author and optimize with Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data reusables.copilot.memory-how-to-intro %}

如果满足以下条件，则默认为您的帐户启用 {% data variables.copilot.copilot_memory %}：

* 您使用的是付费个人 {% data variables.product.prodname_copilot_short %} 计划。
* 您的计划由允许您使用 {% data variables.copilot.copilot_memory %} 的企业或组织管理。

无论哪种情况，您都可以随时在个人 {% data variables.product.prodname_copilot_short %} 设置中禁用或重新启用 {% data variables.copilot.copilot_memory %}。

## 禁用或启用 {% data variables.copilot.copilot_memory %}

启用后，{% data variables.copilot.copilot_memory %} 将在您使用受支持的 {% data variables.product.prodname_copilot_short %} 功能的任何存储库中使用。存储库级别的事实和用户级别的首选项都将被保存。

{% data reusables.user-settings.copilot-settings %}
1. 在“功能”下，向下滚动到 **{% data variables.copilot.copilot_memory %}** 的设置。
1. 单击下拉按钮并选择**启用**或**禁用**。

## 选择默认计费实体

如果您通过多个企业或组织获得 {% data variables.product.prodname_copilot_short %} 访问权限，则您 ** 必须 ** 选择默认计费实体，以便使用 {% data variables.copilot.copilot_memory %} 生成用户级别首选项。计费实体确定哪个帐户“拥有”（可以管理和删除）为您的帐户生成的首选项。在您的[帐户设置](<https://github.com/settings/copilot/features?ref_product=copilot&ref_type=engagement&ref_style=text>)中执行此操作。

## 查看和删除存储库级事实

作为正在使用 {% data variables.copilot.copilot_memory %} 的存储库的所有者，您可以查看当前存储的存储库级事实。如果您认为任何内容不适当、具有误导性或不正确，您可以将其删除。

{% data reusables.copilot.manage-repo-memories %}

## 查看和删除您的用户级别首选项

{% data variables.copilot.copilot_memory %} 存储您个人使用 {% data variables.product.prodname_copilot_short %} 的偏好设置。

{% data reusables.user-settings.copilot-settings %}
1. 在侧边栏中的 **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}** 下，单击 ** 内存 **。
1. 您将看到用户级别首选项列表，您可以根据需要将其删除。

## 进一步阅读

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/use-copilot-agents/copilot-memory/manage-as-administrator>)


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data reusables.copilot.memory-how-to-intro %}

{% data variables.copilot.copilot_memory %} is enabled for your account by default if:

* You are on a paid, individual {% data variables.product.prodname_copilot_short %} plan.
* Your plan is managed by an enterprise or organization that has allowed you to use {% data variables.copilot.copilot_memory %}.

In either case, you can disable or re-enable {% data variables.copilot.copilot_memory %} at any time in your personal {% data variables.product.prodname_copilot_short %} settings.

## Disabling or enabling {% data variables.copilot.copilot_memory %}

When enabled, {% data variables.copilot.copilot_memory %} will be used in any repository in which you use a supported {% data variables.product.prodname_copilot_short %} feature. Both repository-level facts and user-level preferences will be saved.

{% data reusables.user-settings.copilot-settings %}
1. Under "Features", scroll down to the setting for **{% data variables.copilot.copilot_memory %}**.
1. Click the dropdown button and select **Enabled** or **Disabled**.

## Selecting a default billing entity

If you receive {% data variables.product.prodname_copilot_short %} access through multiple enterprises or organizations, you **must** select a default billing entity in order to generate user-level preferences with {% data variables.copilot.copilot_memory %}. The billing entity determines which account "owns" (can manage and delete) preferences that are generated for your account. Do this in your [account settings](<https://github.com/settings/copilot/features?ref_product=copilot&ref_type=engagement&ref_style=text>).

## Viewing and deleting repository-level facts

As an owner of a repository in which {% data variables.copilot.copilot_memory %} is in use, you can review the currently stored repository-level facts. If you think any are inappropriate, misleading, or incorrect, you can delete them.

{% data reusables.copilot.manage-repo-memories %}

## Viewing and deleting your user-level preferences

{% data variables.copilot.copilot_memory %} stores preferences for your personal use of {% data variables.product.prodname_copilot_short %}.

{% data reusables.user-settings.copilot-settings %}
1. In the sidebar, under **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**, click **Memory**.
1. You will see a list of your user-level preferences, which you can delete as needed.

## Further reading

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/use-copilot-agents/copilot-memory/manage-as-administrator>)


</details>
