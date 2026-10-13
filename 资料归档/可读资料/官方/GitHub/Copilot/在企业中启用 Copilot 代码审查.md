# 在企业中启用 Copilot 代码审查

说明为成员开放审查、设置自动 PR 审查和定制审查要求，以保持团队代码检查标准。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-15](<https://github.com/github/docs/commit/1413b140620171c5e4494c52e28a736e4b68b36e>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/enable-copilot-code-review.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在企业中启用 Copilot 代码审查.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Enabling GitHub Copilot code review in your enterprise
intro: Apply consistent standards by having {% data variables.product.prodname_copilot_short %} review every pull request.
allowTitleToDifferFromFilename: true
permissions: Enterprise owners
versions:
  feature: copilot
shortTitle: Enable {% data variables.copilot.copilot_code-review_short %}
redirect_from:
  - /copilot/how-tos/administer-copilot/manage-for-enterprise/manage-copilot-code-review
  - /copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/manage-copilot-code-review
contentType: how-tos
category:
  - Manage Copilot for a team
docsTeamMetrics:
  - ai-governance
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data variables.product.prodname_copilot %} 可以审核您企业中的拉取请求。这可以通过检测错误或漏洞并执行一致的编码标准来节省人工审核人员的时间。

## 为您的 {% data variables.product.prodname_copilot_short %} 订阅者启用 {% data variables.copilot.copilot_code-review_short %}

{% data variables.copilot.copilot_code-review_short %} 的策略允许您的许可用户向 {% data variables.product.prodname_copilot_short %} 请求审核并使用 {% data variables.product.prodname_copilot_short %} 生成拉取请求摘要。

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. 向下滚动到“可用 Agents”，然后单击 **{% data variables.copilot.copilot_code-review_short %}**。
1. 在“{% data variables.copilot.copilot_code-review_short %}”旁边，选择一个策略。
1. 然后选择要为您的企业启用哪些 {% data variables.copilot.copilot_code-review_short %} 功能。

{% data reusables.enterprise-accounts.policy-enablement-next-steps %}

## 配置自动代码审查

为了一致地应用标准，您可以将 {% data variables.copilot.copilot_code-review_short %} 配置为在整个企业或特定存储库中打开的所有拉取请求上自动运行。

1. 创建企业级分支规则集。参见[AUTOTITLE](<https://github.com/enterprise-cloud@latest/admin/enforcing-policies/enforcing-policies-for-your-enterprise/enforcing-policies-for-code-governance>)。
1. 定位 {% data variables.copilot.copilot_code-review_short %} 应自动运行的组织和存储库。您可能希望从一小部分存储库开始，然后进行试验以收集反馈。
1. 启用**自动请求{% data variables.copilot.copilot_code-review_short %}**策略。
1. （可选）启用附加设置。通过审核每个推送并审核草稿拉取请求，您将增加审核流程的一致性。然而，你也会给开发者制造更多的噪音。如果您正在进行试点，请考虑从基本设置开始，以便开发人员首先适应新流程。
1. 单击**创建**。

## 定制评论

鼓励组织和存储库管理员为 {% data variables.copilot.copilot_code-review_short %} 创建自定义指令，以便根据您的编码标准和约定定制评论。参见[AUTOTITLE](<https://github.com/copilot/tutorials/customize-code-review>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data variables.product.prodname_copilot %} can review pull requests in your enterprise. This can save time for human reviewers by detecting bugs or vulnerabilities and enforcing consistent coding standards.

## Enabling {% data variables.copilot.copilot_code-review_short %} for your {% data variables.product.prodname_copilot_short %} subscribers

The policy for {% data variables.copilot.copilot_code-review_short %} allows your licensed users to request reviews from {% data variables.product.prodname_copilot_short %} and use {% data variables.product.prodname_copilot_short %} to generate pull request summaries.

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. Scroll down to "Available Agents", then click **{% data variables.copilot.copilot_code-review_short %}**.
1. Next to "{% data variables.copilot.copilot_code-review_short %}", select a policy.
1. Then choose which {% data variables.copilot.copilot_code-review_short %} features to enable for your enterprise.

{% data reusables.enterprise-accounts.policy-enablement-next-steps %}

## Configuring automatic code review

To apply standards consistently, you can configure {% data variables.copilot.copilot_code-review_short %} to run automatically on all pull requests opened across your enterprise or in specific repositories.

1. Create an enterprise-level branch ruleset. See [AUTOTITLE](<https://github.com/enterprise-cloud@latest/admin/enforcing-policies/enforcing-policies-for-your-enterprise/enforcing-policies-for-code-governance>).
1. Target the organizations and repositories where {% data variables.copilot.copilot_code-review_short %} should run automatically. You may want to start with a small selection of repositories and run a trial to collect feedback.
1. Enable the **Automatically request {% data variables.copilot.copilot_code-review_short %}** policy.
1. Optionally, enable the additional settings. By reviewing on every push and reviewing draft pull requests, you will add consistency to the review process. However, you will also create more noise for developers. If you're running a pilot, consider starting with the basic setting to allow developers to get used to the new process first.
1. Click **Create**.

## Customizing reviews

Encourage organization and repository administrators to create custom instructions for {% data variables.copilot.copilot_code-review_short %} so that reviews will be tailored to your coding standards and conventions. See [AUTOTITLE](<https://github.com/copilot/tutorials/customize-code-review>).


</details>
