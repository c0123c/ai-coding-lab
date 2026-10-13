# 配置组织级云端 Agent 运行器

介绍选择默认运行器和限制仓库自定义运行器的管理设置，用于控制云端 Agent 的执行资源和组织级运行策略。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：源码提交（存档版）：[2026-09-03](<https://github.com/github/docs/commit/6820b60091a0121ed813dae4408aa33934abd272>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/administer-copilot/manage-for-organization/configure-runner-for-coding-agent.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/配置组织级云端 Agent 运行器.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Configuring runners for GitHub Copilot cloud agent in your organization
shortTitle: Configure agent runners
allowTitleToDifferFromFilename: true
intro: 'Configure the {% data variables.product.prodname_actions %} runners used by {% data variables.copilot.copilot_cloud_agent %} and control whether repositories can customize the runner type.'
permissions: Organization owners
product: '{% data reusables.gated-features.copilot-cloud-agent %}<br><a href="https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=trial&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span>{% octicon "link-external" height:16 %}</a>'
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

## 关于组织级运行器控制

默认情况下，{% data variables.copilot.copilot_cloud_agent %} 在标准 {% data variables.product.prodname_dotcom %} 托管的 {% data variables.product.prodname_actions %} 运行器 (`ubuntu-latest`) 上运行。作为组织所有者，您可以更改组织中所有存储库的默认运行程序类型，并选择是否允许各个存储库覆盖此默认值。

如果您的组织需要所有 {% data variables.copilot.copilot_cloud_agent %} 会话在特定运行器上运行，这非常有用 - 例如，使用更大的运行器以获得更好的性能，或使用可以访问内部资源的自托管运行器。

您可以配置：

* **跑步者类型**：在标准 {% data variables.product.prodname_dotcom %} 托管的跑步者或来自特定跑步者组的标记跑步者之间进行选择。
* **允许存储库自定义运行程序类型**：控制存储库是否可以使用`.github/workflows/copilot-setup-steps.yml`中定义的{% data variables.product.prodname_copilot_short %}设置步骤工作流覆盖组织默认值。

## 配置默认运行器类型

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.copilot.cloud-agent-settings %}
1. 单击“跑步者类型”旁边的铅笔图标 ({% octicon "pencil" aria-label="Edit" %})。
1. 选择整个组织中 {% data variables.copilot.copilot_cloud_agent %} 默认使用的运行器类型。
   * **标准{% data variables.product.prodname_dotcom %}跑步者**：{% data variables.copilot.copilot_cloud_agent %}将使用`ubuntu-latest`。
   * **带标签的跑步者**：{% data variables.copilot.copilot_cloud_agent %}将使用与您指定的组名称and/or标签匹配的跑步者。在 **流道组名称 ** and/or **流道标签 ** 字段中输入值。
1. 单击**保存跑步者选择**。

## 防止存储库自定义运行器类型

默认情况下，存储库可以使用位于 `.github/workflows/copilot-setup-steps.yml` 的 {% data variables.product.prodname_copilot_short %} 设置步骤工作流程覆盖组织级运行器配置。如果您想在所有存储库中强制执行一致的运行程序类型，您可以禁用此选项。

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.copilot.cloud-agent-settings %}
1. 在“允许存储库自定义运行程序类型”下，切换设置以启用或禁用存储库级别自定义。
   * 启用后，存储库可以通过设置 `copilot-setup-steps.yml` 的 `copilot-setup-steps` 作业中的 `runs-on` 字段来覆盖默认运行器。参见[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)。
   * 禁用后，组织中的所有存储库都将使用组织级别的运行程序类型。
1. 单击**保存**。

## 进一步阅读

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## About organization-level runner controls

By default, {% data variables.copilot.copilot_cloud_agent %} runs on a standard {% data variables.product.prodname_dotcom %}-hosted {% data variables.product.prodname_actions %} runner (`ubuntu-latest`). As an organization owner, you can change the default runner type for all repositories in your organization, and choose whether individual repositories are allowed to override this default.

This is useful if your organization requires all {% data variables.copilot.copilot_cloud_agent %} sessions to run on specific runners—for example, to use larger runners for better performance, or to use self-hosted runners that have access to internal resources.

You can configure:

* **Runner type**: Choose between a standard {% data variables.product.prodname_dotcom %}-hosted runner or a labeled runner from a specific runner group.
* **Allow repositories to customize the runner type**: Control whether repositories can override the organization default using a {% data variables.product.prodname_copilot_short %} setup steps workflow defined at `.github/workflows/copilot-setup-steps.yml`.

## Configuring the default runner type

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.copilot.cloud-agent-settings %}
1. Next to "Runner type," click the pencil icon ({% octicon "pencil" aria-label="Edit" %}).
1. Select the runner type to use by default for {% data variables.copilot.copilot_cloud_agent %} across your organization.
   * **Standard {% data variables.product.prodname_dotcom %} runner**: {% data variables.copilot.copilot_cloud_agent %} will use `ubuntu-latest`.
   * **Labeled runner**: {% data variables.copilot.copilot_cloud_agent %} will use a runner matching the group name and/or label you specify. Enter values in the **Runner group name** and/or **Runner label** fields.
1. Click **Save runner selection**.

## Preventing repositories from customizing the runner type

By default, repositories can override the organization-level runner configuration using a {% data variables.product.prodname_copilot_short %} setup steps workflow located at `.github/workflows/copilot-setup-steps.yml`. If you want to enforce a consistent runner type across all repositories, you can disable this option.

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.copilot.cloud-agent-settings %}
1. Under "Allow repositories to customize the runner type," toggle the setting to enable or disable repository-level customization.
   * When enabled, repositories can override the default runner by setting the `runs-on` field in the `copilot-setup-steps` job of `copilot-setup-steps.yml`. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>).
   * When disabled, all repositories in your organization will use the organization-level runner type.
1. Click **Save**.

## Further reading

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent>)


</details>
