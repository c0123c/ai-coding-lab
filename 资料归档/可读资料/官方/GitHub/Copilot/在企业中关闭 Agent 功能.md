# 在企业中关闭 Agent 功能

说明管理员如何关闭云端 Agent 或代码审查等功能及其适用范围；属于企业治理配置资料。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：源码提交（存档版）：[2026-05-21](<https://github.com/github/docs/commit/029b10203c9e7e0d0a91749e37ddefb2f9252b04>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/block-agentic-features.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在企业中关闭 Agent 功能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Blocking agentic features in your enterprise
intro: Disable features for all users and repositories.
permissions: Enterprise owners and AI managers
versions:
  feature: copilot
shortTitle: Block agentic features
contentType: how-tos
category:
  - Manage Copilot for a team
allowTitleToDifferFromFilename: true
redirect_from:
  - /copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/block-copilot-cloud-agent
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

大多数 {% data variables.product.prodname_copilot_short %} 策略仅影响从您的企业或组织接收 {% data variables.product.prodname_copilot_short %} 许可证的用户。但是，有专门的策略可以完全禁用存储库中的功能，包括从个人计划或其他企业访问 {% data variables.product.prodname_copilot_short %} 的用户。

## 封锁 {% data variables.copilot.copilot_cloud_agent %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
{% data reusables.enterprise-accounts.cca-policies %}
1. 在 ** 旁边，在 ENTERPRISE-NAME** 拥有的所有存储库中阻止 {% data variables.copilot.copilot_cloud_agent %}，单击切换按钮。

## 封锁 {% data variables.copilot.copilot_code-review_short %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. 在左侧边栏中，单击{% octicon "agent" aria-hidden="true" aria-label="agent" %} **Agents**。
1. 在“可用Agents”下，单击**{% data variables.copilot.copilot_code-review_short %}**。
1. 在所有企业存储库 ** 中的 ** 阻止 {% data variables.copilot.copilot_code-review_short %} 旁边，单击切换开关。


## 原文对照

<details>
<summary>展开完整原文</summary>

Most {% data variables.product.prodname_copilot_short %} policies only affect users who receive a {% data variables.product.prodname_copilot_short %} license from your enterprise or organizations. However, there are dedicated policies to completely disable features in your repositories, including for users who get access to {% data variables.product.prodname_copilot_short %} from a personal plan or another enterprise.

## Blocking {% data variables.copilot.copilot_cloud_agent %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
{% data reusables.enterprise-accounts.cca-policies %}
1. Next to **Block {% data variables.copilot.copilot_cloud_agent %} in all repositories owned by ENTERPRISE-NAME**, click the toggle.

## Blocking {% data variables.copilot.copilot_code-review_short %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. In the left sidebar, click {% octicon "agent" aria-hidden="true" aria-label="agent" %} **Agents**.
1. Under "Available Agents", click **{% data variables.copilot.copilot_code-review_short %}**.
1. Next to **Block {% data variables.copilot.copilot_code-review_short %} in all enterprise repositories**, click the toggle.


</details>
