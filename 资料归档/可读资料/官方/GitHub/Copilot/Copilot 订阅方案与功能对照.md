# Copilot 订阅方案与功能对照

按个人、组织和企业方案比较额度、补全、模型、Agent 与定制功能；属于所收录版本的产品方案资料。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/ddc34e6c76d8836ec12ed3edfaffb1c4be41829e>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/get-started/plans.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 订阅方案与功能对照.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Plans for GitHub Copilot
intro: 'Discover the plans available for {% data variables.product.prodname_copilot_short %}.'
versions:
  feature: copilot
shortTitle: Plans
redirect_from:
  - /copilot/about-github-copilot/subscription-plans-for-github-copilot
  - /copilot/about-github-copilot/plans-for-github-copilot
  - /copilot/get-started/plans-for-github-copilot
  - /copilot/concepts/billing/individual-plans
contentType: get-started
category:
  - Learn about Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## {% data variables.product.prodname_copilot %} 计划

{% data variables.product.company_short %}为{% data variables.product.prodname_copilot_short %}提供多种方案。根据您的需求以及您是作为个人还是作为组织或企业的一部分使用 {% data variables.product.prodname_copilot_short %}，在它们之间进行选择。

**{% data variables.copilot.copilot_free_short %}**：[开始使用{% data variables.copilot.copilot_free_short %}](<https://github.com/copilot?ref_product=copilot&ref_type=engagement&ref_style=text&ref_plan=free>)。

* 该计划包括对一系列 {% data variables.product.prodname_copilot_short %} 功能的有限访问，让您可以免费尝试 AI- 提供的编码帮助。

**{% data variables.copilot.copilot_student_short %}**：[访问{% data variables.copilot.copilot_student_short %}](<https://github.com/copilot/how-tos/copilot-on-github/set-up-copilot/enable-copilot/set-up-for-students>)。

* 可供经过验证的学生使用。免费使用{% data variables.product.prodname_copilot_short %}的功能。

**{% data variables.copilot.copilot_pro %}**：[订阅{% data variables.copilot.copilot_pro_short %}](<https://github.com/github-copilot/signup?ref_product=copilot&ref_type=purchase&ref_style=text&ref_plan=pro>)。

* 专为想要更灵活地选择型号和每月津贴 {% data variables.product.prodname_ai_credits_short %} 的个人而设计。

**{% data variables.copilot.copilot_pro_plus %}**：[订阅{% data variables.copilot.copilot_pro_plus_short %}](<https://github.com/github-copilot/signup?ref_product=copilot&ref_type=purchase&ref_style=text&ref_plan=pro-plus>)。

* 非常适合想要访问最先进功能的 AI 高级用户。此付费计划包括 {% data variables.copilot.copilot_pro %} 中的所有内容以及更高的每月津贴 {% data variables.product.prodname_ai_credits_short %}。

**{% data variables.copilot.copilot_max %}**：[升级到{% data variables.copilot.copilot_max_short %}](<https://github.com/settings/billing/licensing?ref_product=copilot&ref_type=purchase&ref_style=text&ref_plan=max>)。

* 非常适合想要获得最多 AI 可用积分的持续、高容量 AI 高级用户。此付费计划包括 {% data variables.copilot.copilot_pro_plus_short %} 中的所有内容，加上我们最高的个人每月津贴 {% data variables.product.prodname_ai_credits_short %}。

**{% data variables.copilot.copilot_for_business %}**：[订阅{% data variables.copilot.copilot_business_short %}](<https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=purchase&ref_style=button&ref_plan=business>)。

* 该计划专为组织和企业而设计，为组织成员提供集中管理和 {% data variables.product.prodname_copilot_short %} 策略控制。

**{% data variables.copilot.copilot_enterprise %}**：[订阅{% data variables.copilot.copilot_enterprise_short %}](<https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=purchase&ref_style=button&ref_plan=enterprise>)。

* 专为使用{% data variables.product.prodname_ghe_cloud %}的企业而设计。该计划包括 {% data variables.copilot.copilot_business_short %} 的所有功能，提供更大的每月 {% data variables.product.prodname_ai_credits_short %} 池，以及额外的企业级功能。

> [！NOTE]
> {% data variables.product.prodname_copilot_short %} 目前不适用于 {% data variables.product.prodname_ghe_server %}。

## {% data variables.product.prodname_copilot_short %} 计划概览

下表概述了计划之间的差异。所有计划包括{% data variables.copilot.copilot_cli_short %}和{% data variables.copilot.github_copilot_app_short %}。

{% rowheaders %}

|计划|定价| {% data variables.product.prodname_ai_credits %} | Agents                                                                     |型号|
| ----------------------------------------------------- | ----------------------------------------------------------------------------------- | --------------- | -------------------------------------------------------------------------- | --------------------------------- |
| {% data variables.copilot.copilot_free_short %}       |免费|津贴 {% data variables.product.prodname_ai_credits %}|有限公司|仅限自动车型选择|
| {% data variables.copilot.copilot_student_short %}    |免费|津贴 {% data variables.product.prodname_ai_credits %}|{% octicon "check" aria-label="Included" %}<br>不包括第三方 agents|仅限自动车型选择|
| {% data variables.copilot.copilot_pro_short %}        |{% data variables.copilot.cfi_price_per_month %} 每月<br>（部分用户免费）|底座：{% data variables.copilot.ai_credits_per_user_pro %}| {% octicon "check" aria-label="Included" %}                                |精选型号|
| {% data variables.copilot.copilot_pro_plus_short %}   |每月 {% data variables.copilot.cpp_price_per_month %}。|底座：{% data variables.copilot.ai_credits_per_user_pro_plus %}| {% octicon "check" aria-label="Included" %}                                |获得高级型号|
| {% data variables.copilot.copilot_max_short %}        |每月 {% data variables.copilot.cm_price_per_month %}|底座：{% data variables.copilot.ai_credits_per_user_max %}| {% octicon "check" aria-label="Included" %}                                |优先获得高级型号|
| {% data variables.copilot.copilot_business_short %}   |{% data variables.copilot.cfb_price_per_month %} 每月每个授予的席位|每个用户每月总计：{% data variables.copilot.ai_credits_per_user_business %}| {% octicon "check" aria-label="Included" %}                                |获得高级型号|
| {% data variables.copilot.copilot_enterprise_short %} |{% data variables.copilot.ce_price_per_month %} 每月每个授予的席位|每个用户每月总计：{% data variables.copilot.ai_credits_per_user_enterprise %}| {% octicon "check" aria-label="Included" %}                                |优先获得高级型号|

{% endrowheaders %}

每个计划都附带 {% data variables.product.prodname_ai_credits %} 的津贴。有关更多信息，包括 {% data variables.product.prodname_ai_credits %} 的工作原理，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/individuals/billing>) 和 [AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/organizations-and-enterprises/billing>)。

有关每个计划中独特可用内容的更多详细信息，请参阅以下部分：
* [个人计划](<#个人计划>)
* [组织机构及企业规划](<#组织和企业计划>)

## 个人计划

可用的个人计划有：
* 免费套餐包括{% data variables.copilot.copilot_free_short %}和{% data variables.copilot.copilot_student_short %}。
* 付费套餐包括{% data variables.copilot.copilot_pro_short %}、{% data variables.copilot.copilot_pro_plus_short %}和{% data variables.copilot.copilot_max_short %}。

通过这些计划，您将可以使用以下特性和功能。

> [!NOTE] 
> * {% data variables.copilot.copilot_free_short %} 计划仅适用于无法通过组织或企业访问 {% data variables.product.prodname_copilot_short %} 的个人开发者。
> * 经过验证的教师和流行开源项目的维护者可能有资格免费访问 {% data variables.copilot.copilot_pro_short %}。

### {% data variables.product.prodname_ai_credits %} 计划津贴

下表显示了每个付费计划所包含的内容。

{% data reusables.copilot.plans.ai-credits-by-plan %}

{% data variables.copilot.copilot_free_short %} 和 {% data variables.copilot.copilot_student_short %} 的容差均为 {% data variables.product.prodname_ai_credits_short %}。

有关 {% data variables.product.prodname_ai_credits %} 工作原理的更多信息，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/individuals/billing>)

### 内嵌建议和 {% data variables.copilot.copilot_chat_short %}

内联建议是实时代码建议，包含 IDE 和 {% data variables.copilot.next_edit_suggestions_caps %} 中的模型。
* {% data variables.copilot.copilot_free_short %} 每月仅限 2000 次完成。

**{% data variables.copilot.copilot_chat_short %}** 可用功能包括：
* IDE 中的 {% data variables.copilot.copilot_chat_short %}
* 内嵌聊天
* 斜杠命令
* {% data variables.copilot.copilot_mobile_short %}
* {% data variables.copilot.copilot_chat_dotcom_short %}
* {% data variables.copilot.copilot_chat_short %} 在 {% data variables.product.prodname_windows_terminal %} 中
* {% data variables.copilot.copilot_chat_short %} IDE 技能。[^1]（在 {% data variables.copilot.copilot_free_short %} 中不可用）。

### 型号

在 {% data variables.copilot.copilot_free_short %} 和 {% data variables.copilot.copilot_student_short %} 计划中，只能通过 {% data variables.copilot.copilot_auto_model_selection_short %} 访问模型。

{% rowheaders %}

|可用型号| {% data variables.copilot.copilot_pro_short %}  | {% data variables.copilot.copilot_pro_plus_short %} | {% data variables.copilot.copilot_max_short %} |
|---------------------------------------------------------|-------------------------------------------------|-----------------------------------------------------|------------------------------------------------|
| {% for model in tables.copilot.model-supported-plans %} |
|{{ model.name }}{% if model.name == 'GPT-5.4 nano' %}[^gpt54nano]{% endif %}{% if model.name == 'Claude Fable 5' or model.name == 'Claude Fable 5.1' %}[^克劳德寓言-5]{% endif %}| {% if model.pro == true %}{% octicon "check" aria-label="Included" %}{% else %}{% octicon "x" aria-label="Not included" %}{% endif %} | {% if model.pro_plus == true %}{% octicon "check" aria-label="Included" %}{% else %}{% octicon "x" aria-label="Not included" %}{% endif %} | {% if model.max == true %}{% octicon "check" aria-label="Included" %}{% else %}{% octicon "x" aria-label="Not included" %}{% endif %} |
| {% endfor %}                                            |

{% endrowheaders %}

### Agents

{% rowheaders %}

| Agents                                                                  | {% data variables.copilot.copilot_free_short %}                                   | {% data variables.copilot.copilot_student_short %} | {% data variables.copilot.copilot_pro_short %} | {% data variables.copilot.copilot_pro_plus_short %} | {% data variables.copilot.copilot_max_short %} |
| ----------------------------------------------------------------------- | --------------------------------------------------------------------------------- | -------------------------------------------------- | ---------------------------------------------- | --------------------------------------------------- | ---------------------------------------------- |
| {% data variables.copilot.copilot_cloud_agent %}                        | {% octicon "x" aria-label="Not included" %}                                       | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
|Agent模式| {% octicon "check" aria-label="Included" %}                                       | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
|{% data variables.product.prodname_copilot_short %} 代码审查|仅在{% data variables.product.prodname_vscode_shortname %}中“查看选择”| {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| Model Context Protocol (MCP)                                            | {% octicon "check" aria-label="Included" %}                                       | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
|第三方 Agents ({% data variables.release-phases.public_preview %})| {% octicon "x" aria-label="Not included" %}                                       | {% octicon "x" aria-label="Not included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |

{% endrowheaders %}

### 定制化

{% rowheaders %}

|定制化| {% data variables.copilot.copilot_free_short %} | {% data variables.copilot.copilot_student_short %} | {% data variables.copilot.copilot_pro_short %} | {% data variables.copilot.copilot_pro_plus_short %} | {% data variables.copilot.copilot_max_short %} |
| -------------------------------------------------------------------------------- | ----------------------------------------------- | -------------------------------------------------- | ---------------------------------------------- | --------------------------------------------------- | ---------------------------------------------- |
|存储库和个人定制说明| {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
|组织定制说明| {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "x" aria-label="Not included" %}         | {% octicon "x" aria-label="Not included" %}    |
|提示文件| {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| Model Context Protocol (MCP)                                                     | {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
|阻止与公共代码匹配的建议| {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
|从 {% data variables.product.prodname_copilot_short %} 中排除指定文件| {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "x" aria-label="Not included" %}         | {% octicon "x" aria-label="Not included" %}    |
|全组织范围的政策管理| {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "x" aria-label="Not included" %}         | {% octicon "x" aria-label="Not included" %}    |

{% endrowheaders %}

### 其他特点

{% rowheaders %}

|                                                                                                  | {% data variables.copilot.copilot_free_short %} | {% data variables.copilot.copilot_student_short %} | {% data variables.copilot.copilot_pro_short %} | {% data variables.copilot.copilot_pro_plus_short %} | {% data variables.copilot.copilot_max_short %} |
| ------------------------------------------------------------------------------------------------ | ----------------------------------------------- | -------------------------------------------------- | ---------------------------------------------- | --------------------------------------------------- | ---------------------------------------------- |
| {% data variables.copilot.copilot_for_prs %}                                                     | {% octicon "x" aria-label="Not included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
|审核日志| {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
|内容排除| {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "x" aria-label="Not included" %}         | {% octicon "x" aria-label="Not included" %}    |
| {% data variables.copilot.copilot_cli_short %}                                                   | {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| {% data variables.copilot.github_copilot_app_short %}                                                   | {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| {% data variables.product.prodname_spark %} ({% data variables.release-phases.public_preview %}) | {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "x" aria-label="Not included" %}    |

{% endrowheaders %}

## 组织和企业计划

可供组织和企业使用的 {% data variables.product.prodname_copilot_short %} 计划包括：
* {% data variables.copilot.copilot_business_short %}
* {% data variables.copilot.copilot_enterprise_short %}

通过这些计划，您将可以使用以下特性和功能。

> [!NOTE] 通过 {% data variables.product.prodname_ghe_cloud %}，企业所有者可以为企业中的每个组织选择计划。有关选择计划的指南，请参阅 [AUTOTITLE](<https://github.com/copilot/tutorials/roll-out-at-scale/assign-licenses/choose-enterprise-plan>)。

### {% data variables.product.prodname_ai_credits %} 计划津贴

|计划|每月每个授予席位的价格|{% data variables.product.prodname_ai_credits %} 每个用户每月| 
| --- | --- | --- | 
| {% data variables.copilot.copilot_business_short %} | {% data variables.copilot.cfb_price_per_month %}  | {% data variables.copilot.ai_credits_per_user_business %} | 
| {% data variables.copilot.copilot_enterprise_short %}| {% data variables.copilot.ce_price_per_month %}  | {% data variables.copilot.ai_credits_per_user_enterprise %} | 

{% data variables.product.prodname_copilot_short %} 使用量是在基于使用量的计费下在 {% data variables.product.prodname_ai_credits_short %} 中测量的。每个许可证将 {% data variables.product.prodname_ai_credits_short %} 贡献给共享企业池，池外的使用按 {% data variables.product.prodname_ai_credits_value %} 按 {% data variables.product.prodname_ai_credit_singular %} 收费。代码完成和 {% data variables.copilot.next_edit_suggestions %} 不在 {% data variables.product.prodname_ai_credits_short %} 中计费，并且对所有付费计划保持无限。

有关 {% data variables.product.prodname_ai_credits_short %} 如何工作的完整说明，包括池化、额外使用以及积分用完时会发生什么，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/organizations-and-enterprises/billing>)。

### 内嵌建议和 {% data variables.copilot.copilot_chat_short %}

**内联建议**：所有计划均提供内联建议功能：

* 包含模型的实时代码建议
* {% data variables.copilot.next_edit_suggestions_caps %}

**{% data variables.copilot.copilot_chat_short %}** 功能在所有计划中均可用：

* IDE 中的 {% data variables.copilot.copilot_chat_short %}
* 内嵌聊天
* 斜杠命令
* {% data variables.copilot.copilot_mobile_short %}
* {% data variables.copilot.copilot_chat_dotcom_short %}
* {% data variables.copilot.copilot_chat_short %} 在 {% data variables.product.prodname_windows_terminal %} 中
* {% data variables.copilot.copilot_chat_short %} IDE 技能[^1]

### 型号

{% rowheaders %}

|可用型号| {% data variables.copilot.copilot_business_short %} | {% data variables.copilot.copilot_enterprise_short %} |
|---------------------------------------------------------|-----------------------------------------------------|-------------------------------------------------------|
| {% for model in tables.copilot.model-supported-plans %} |
|{{ model.name }}{% if model.name == 'GPT-5.4 nano' %}[^gpt54nano]{% endif %}{% if model.name == 'Claude Fable 5' or model.name == 'Claude Fable 5.1' %}[^克劳德寓言-5]{% endif %}| {% if model.business == true %}{% octicon "check" aria-label="Included" %}{% else %}{% octicon "x" aria-label="Not included" %}{% endif %} | {% if model.enterprise == true %}{% octicon "check" aria-label="Included" %}{% else %}{% octicon "x" aria-label="Not included" %}{% endif %} |
| {% endfor %}                                            |

{% endrowheaders %}

[^gpt54nano]: <https://github.com/github/docs/blob/be38ec5d78e24172587e61b3c6ff40ace1865c71/content/copilot/get-started/GPT-5.4> nano is currently only available in the Codex {% data variables.product.prodname_vscode %} extension ({% data variables.copilot.copilot_pro_plus_short %} only) and is not available in {% data variables.copilot.copilot_chat_short %}.

### Agents

{% rowheaders %}

| Agents                                                                  | {% data variables.copilot.copilot_business_short %} | {% data variables.copilot.copilot_enterprise_short %} |
| ----------------------------------------------------------------------- | --------------------------------------------------- | ----------------------------------------------------- |
| {% data variables.copilot.copilot_cloud_agent %}                        | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
|Agent模式| {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
|{% data variables.product.prodname_copilot_short %} 代码审查| {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Model Context Protocol (MCP)                                            | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
|第三方 Agents ({% data variables.release-phases.public_preview %})| {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |

{% endrowheaders %}

### 定制化

{% rowheaders %}

|定制化| {% data variables.copilot.copilot_business_short %} | {% data variables.copilot.copilot_enterprise_short %} |
| -------------------------------------------------------------------------------- | --------------------------------------------------- | ----------------------------------------------------- |
|存储库和个人定制说明| {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
|组织定制说明| {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
|提示文件| {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Model Context Protocol (MCP)                                                     | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
|阻止与公共代码匹配的建议| {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
|从 {% data variables.product.prodname_copilot_short %} 中排除指定文件| {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
|全组织范围的政策管理| {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |

{% endrowheaders %}

### 其他特点

{% rowheaders %}

|                                                                                                  | {% data variables.copilot.copilot_business_short %} | {% data variables.copilot.copilot_enterprise_short %} |
| ------------------------------------------------------------------------------------------------ | --------------------------------------------------- | ----------------------------------------------------- |
| {% data variables.copilot.copilot_for_prs %}                                                     | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
|审核日志| {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
|内容排除| {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| {% data variables.copilot.copilot_cli_short %}                                                   | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| {% data variables.copilot.github_copilot_app_short %}                                                  | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| {% data variables.product.prodname_spark %} ({% data variables.release-phases.public_preview %}) | {% octicon "x" aria-label="Not included" %}         | {% octicon "check" aria-label="Included" %}           |

{% endrowheaders %}

## 进一步阅读

* 要比较跨 IDE 的功能支持，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/copilot-feature-matrix>)。
* 要比较跨计划、功能和 IDE 支持的模型，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/ai-models/supported-models>)。

[^1]: <https://github.com/github/docs/blob/be38ec5d78e24172587e61b3c6ff40ace1865c71/content/copilot/get-started/{%> data variables.copilot.copilot_chat_short %} skills in IDEs is available in {% data variables.product.prodname_vscode %} and {% data variables.product.prodname_vs %}.
[^claude-fable-5]: <https://github.com/github/docs/blob/be38ec5d78e24172587e61b3c6ff40ace1865c71/content/copilot/get-started/When> {% data variables.copilot.copilot_claude_fable_5 %} or {% data variables.copilot.copilot_claude_fable_51 %} is used, Anthropic retains data, including prompts and outputs, by default to operate safety classifiers that detect harmful use. Customers can request to use {% data variables.copilot.copilot_claude_fable_5 %} or {% data variables.copilot.copilot_claude_fable_51 %} with zero data retention (ZDR) through the end of 2026 under a time-bound exemption while Anthropic rolls out Enterprise Frontier Safeguards (EFS). After that point, continued use of these models would require EFS, which will enable eligible customers to keep their data under their own control while also enabling automated safety monitoring. For an enterprise that has been approved and configured for this type of access, {% data variables.copilot.copilot_claude_fable_5 %} and {% data variables.copilot.copilot_claude_fable_51 %} requests will use the ZDR endpoint through the end of 2026 when the models are enabled. To learn whether your enterprise is eligible and request access, contact your {% data variables.product.github %} account team. Approval for access does not automatically enable {% data variables.copilot.copilot_claude_fable_5 %} or {% data variables.copilot.copilot_claude_fable_51 %}. An enterprise or organization administrator must still enable each model before users can access it. Other Claude models, except for {% data variables.copilot.copilot_claude_fable_5 %} and {% data variables.copilot.copilot_claude_fable_51 %}, continue to operate under ZDR. Customers who enable a Fable model with ZDR agree to use this model only for internal operations, including to develop and evaluate products for their own customers. They may not make the model endpoints or outputs available externally. To enable these models, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/set-up-copilot/configure-access-to-ai-models>).


## 原文对照

<details>
<summary>展开完整原文</summary>

## {% data variables.product.prodname_copilot %} plans

{% data variables.product.company_short %} offers a variety of plans for {% data variables.product.prodname_copilot_short %}. Choose between them depending on your needs and whether you're using {% data variables.product.prodname_copilot_short %} as an individual or as part of an organization or enterprise.

**{% data variables.copilot.copilot_free_short %}**: [Start using {% data variables.copilot.copilot_free_short %}](<https://github.com/copilot?ref_product=copilot&ref_type=engagement&ref_style=text&ref_plan=free>).

* This plan includes limited access to a selection of {% data variables.product.prodname_copilot_short %} features allowing you to try AI-powered coding assistance at no cost.

**{% data variables.copilot.copilot_student_short %}**: [Get access to {% data variables.copilot.copilot_student_short %}](<https://github.com/copilot/how-tos/copilot-on-github/set-up-copilot/enable-copilot/set-up-for-students>).

* Available to verified students. Get access to {% data variables.product.prodname_copilot_short %}'s features for free. 

**{% data variables.copilot.copilot_pro %}**: [Subscribe to {% data variables.copilot.copilot_pro_short %}](<https://github.com/github-copilot/signup?ref_product=copilot&ref_type=purchase&ref_style=text&ref_plan=pro>).

* Designed for individuals who want more flexibility with access to a selection of models and a monthly allowance of {% data variables.product.prodname_ai_credits_short %}. 

**{% data variables.copilot.copilot_pro_plus %}**: [Subscribe to {% data variables.copilot.copilot_pro_plus_short %}](<https://github.com/github-copilot/signup?ref_product=copilot&ref_type=purchase&ref_style=text&ref_plan=pro-plus>).

* Ideal for AI power users who want access to the most advanced capabilities. This paid plan includes everything in {% data variables.copilot.copilot_pro %} and a higher monthly allowance of {% data variables.product.prodname_ai_credits_short %}. 

**{% data variables.copilot.copilot_max %}**: [Upgrade to {% data variables.copilot.copilot_max_short %}](<https://github.com/settings/billing/licensing?ref_product=copilot&ref_type=purchase&ref_style=text&ref_plan=max>).

* Ideal for sustained, high-volume AI power users who want access to the most AI credits available to them. This paid plan includes everything in {% data variables.copilot.copilot_pro_plus_short %}, plus our highest individual monthly allowance of {% data variables.product.prodname_ai_credits_short %}. 

**{% data variables.copilot.copilot_for_business %}**: [Subscribe to {% data variables.copilot.copilot_business_short %}](<https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=purchase&ref_style=button&ref_plan=business>).

* Made for organizations and enterprises, this plan offers centralized management and {% data variables.product.prodname_copilot_short %} policy control for organization members.

**{% data variables.copilot.copilot_enterprise %}**: [Subscribe to {% data variables.copilot.copilot_enterprise_short %}](<https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=purchase&ref_style=button&ref_plan=enterprise>).

* Designed for enterprises using {% data variables.product.prodname_ghe_cloud %}. This plan includes all the features of {% data variables.copilot.copilot_business_short %}, offers a larger monthly pool of {% data variables.product.prodname_ai_credits_short %}, plus additional enterprise-grade capabilities. 

> [!NOTE]
> {% data variables.product.prodname_copilot_short %} is not currently available for {% data variables.product.prodname_ghe_server %}.

## {% data variables.product.prodname_copilot_short %} plans overview

The table below provides an overview of differences between plans. All plans include {% data variables.copilot.copilot_cli_short %} and {% data variables.copilot.github_copilot_app_short %}. 

{% rowheaders %}

| Plan                                                  | Pricing                                                                             | {% data variables.product.prodname_ai_credits %} | Agents                                                                     | Models                            |
| ----------------------------------------------------- | ----------------------------------------------------------------------------------- | --------------- | -------------------------------------------------------------------------- | --------------------------------- |
| {% data variables.copilot.copilot_free_short %}       | Free                                                                                | An allowance of {% data variables.product.prodname_ai_credits %}                | Limited                                                                    | Auto model selection only         |
| {% data variables.copilot.copilot_student_short %}    | Free                                                                                | An allowance of {% data variables.product.prodname_ai_credits %}                | {% octicon "check" aria-label="Included" %}<br>Excludes third-party agents | Auto model selection only         |
| {% data variables.copilot.copilot_pro_short %}        | {% data variables.copilot.cfi_price_per_month %} per month<br>(free for some users) | Base: {% data variables.copilot.ai_credits_per_user_pro %}                | {% octicon "check" aria-label="Included" %}                                | A selection of models             |
| {% data variables.copilot.copilot_pro_plus_short %}   | {% data variables.copilot.cpp_price_per_month %} per month.                         | Base: {% data variables.copilot.ai_credits_per_user_pro_plus %}                | {% octicon "check" aria-label="Included" %}                                | Access to premium models          |
| {% data variables.copilot.copilot_max_short %}        | {% data variables.copilot.cm_price_per_month %} per month                           | Base: {% data variables.copilot.ai_credits_per_user_max %}                | {% octicon "check" aria-label="Included" %}                                | Priority access to premium models |
| {% data variables.copilot.copilot_business_short %}   | {% data variables.copilot.cfb_price_per_month %} per granted seat per month         | Total per user per month: {% data variables.copilot.ai_credits_per_user_business %}                | {% octicon "check" aria-label="Included" %}                                | Access to premium models          |
| {% data variables.copilot.copilot_enterprise_short %} | {% data variables.copilot.ce_price_per_month %} per granted seat per month          | Total per user per month: {% data variables.copilot.ai_credits_per_user_enterprise %}                | {% octicon "check" aria-label="Included" %}                                | Priority access to premium models |

{% endrowheaders %}

Each plan comes with an allowance of {% data variables.product.prodname_ai_credits %}. For more information, including how {% data variables.product.prodname_ai_credits %} work, see [AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/individuals/billing>) and [AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/organizations-and-enterprises/billing>).

For more detail on what's uniquely available in each plan, see the following sections: 
* [Individual plans](<#individual-plans>)
* [Organization and enterprise plans](<#organization-and-enterprise-plans>)

## Individual plans

The individual plans available are: 
* Free plans including {% data variables.copilot.copilot_free_short %} and {% data variables.copilot.copilot_student_short %}.
* Paid plans including {% data variables.copilot.copilot_pro_short %}, {% data variables.copilot.copilot_pro_plus_short %}, and {% data variables.copilot.copilot_max_short %}.

With these plans you'll receive access to the following features and capabilities. 

> [!NOTE] 
> * {% data variables.copilot.copilot_free_short %} plans are only available to individual developers who don't have access to {% data variables.product.prodname_copilot_short %} through an organization or enterprise.
> * Verified teachers, and maintainers of popular open source projects may be eligible for free access to {% data variables.copilot.copilot_pro_short %}. 

### {% data variables.product.prodname_ai_credits %} allowance by plan

The following table shows what's included with each paid plan.

{% data reusables.copilot.plans.ai-credits-by-plan %}

{% data variables.copilot.copilot_free_short %} and {% data variables.copilot.copilot_student_short %} both have an allowance of {% data variables.product.prodname_ai_credits_short %}.

For more information on how {% data variables.product.prodname_ai_credits %} work, see [AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/individuals/billing>)

### Inline suggestions and {% data variables.copilot.copilot_chat_short %}

Inline suggestions are real-time code suggestions with included models in IDEs and {% data variables.copilot.next_edit_suggestions_caps %}. 
* Limited to 2000 completions per month on {% data variables.copilot.copilot_free_short %}. 

**{% data variables.copilot.copilot_chat_short %}** features available include:
* {% data variables.copilot.copilot_chat_short %} in IDEs
* Inline chat
* Slash commands
* {% data variables.copilot.copilot_mobile_short %}
* {% data variables.copilot.copilot_chat_dotcom_short %}
* {% data variables.copilot.copilot_chat_short %} in {% data variables.product.prodname_windows_terminal %}
* {% data variables.copilot.copilot_chat_short %} skills in IDEs.[^1] (Not available in {% data variables.copilot.copilot_free_short %}).

### Models

On {% data variables.copilot.copilot_free_short %} and {% data variables.copilot.copilot_student_short %} plans, access to models is available through {% data variables.copilot.copilot_auto_model_selection_short %} only.

{% rowheaders %}

| Available models                               | {% data variables.copilot.copilot_pro_short %}  | {% data variables.copilot.copilot_pro_plus_short %} | {% data variables.copilot.copilot_max_short %} |
|---------------------------------------------------------|-------------------------------------------------|-----------------------------------------------------|------------------------------------------------|
| {% for model in tables.copilot.model-supported-plans %} |
| {{ model.name }}{% if model.name == 'GPT-5.4 nano' %}[^gpt54nano]{% endif %}{% if model.name == 'Claude Fable 5' or model.name == 'Claude Fable 5.1' %}[^claude-fable-5]{% endif %} | {% if model.pro == true %}{% octicon "check" aria-label="Included" %}{% else %}{% octicon "x" aria-label="Not included" %}{% endif %} | {% if model.pro_plus == true %}{% octicon "check" aria-label="Included" %}{% else %}{% octicon "x" aria-label="Not included" %}{% endif %} | {% if model.max == true %}{% octicon "check" aria-label="Included" %}{% else %}{% octicon "x" aria-label="Not included" %}{% endif %} |
| {% endfor %}                                            |

{% endrowheaders %}

### Agents

{% rowheaders %}

| Agents                                                                  | {% data variables.copilot.copilot_free_short %}                                   | {% data variables.copilot.copilot_student_short %} | {% data variables.copilot.copilot_pro_short %} | {% data variables.copilot.copilot_pro_plus_short %} | {% data variables.copilot.copilot_max_short %} |
| ----------------------------------------------------------------------- | --------------------------------------------------------------------------------- | -------------------------------------------------- | ---------------------------------------------- | --------------------------------------------------- | ---------------------------------------------- |
| {% data variables.copilot.copilot_cloud_agent %}                        | {% octicon "x" aria-label="Not included" %}                                       | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| Agent mode                                                              | {% octicon "check" aria-label="Included" %}                                       | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| {% data variables.product.prodname_copilot_short %} code review         | Only "Review selection" in {% data variables.product.prodname_vscode_shortname %} | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| Model Context Protocol (MCP)                                            | {% octicon "check" aria-label="Included" %}                                       | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| Third-party Agents ({% data variables.release-phases.public_preview %}) | {% octicon "x" aria-label="Not included" %}                                       | {% octicon "x" aria-label="Not included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |

{% endrowheaders %}

### Customization

{% rowheaders %}

| Customization                                                                    | {% data variables.copilot.copilot_free_short %} | {% data variables.copilot.copilot_student_short %} | {% data variables.copilot.copilot_pro_short %} | {% data variables.copilot.copilot_pro_plus_short %} | {% data variables.copilot.copilot_max_short %} |
| -------------------------------------------------------------------------------- | ----------------------------------------------- | -------------------------------------------------- | ---------------------------------------------- | --------------------------------------------------- | ---------------------------------------------- |
| Repository and personal custom instructions                                      | {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| Organization custom instructions                                                 | {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "x" aria-label="Not included" %}         | {% octicon "x" aria-label="Not included" %}    |
| Prompt files                                                                     | {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| Model Context Protocol (MCP)                                                     | {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| Block suggestions matching public code                                           | {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| Exclude specified files from {% data variables.product.prodname_copilot_short %} | {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "x" aria-label="Not included" %}         | {% octicon "x" aria-label="Not included" %}    |
| Organization-wide policy management                                              | {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "x" aria-label="Not included" %}         | {% octicon "x" aria-label="Not included" %}    |

{% endrowheaders %}

### Other features

{% rowheaders %}

|                                                                                                  | {% data variables.copilot.copilot_free_short %} | {% data variables.copilot.copilot_student_short %} | {% data variables.copilot.copilot_pro_short %} | {% data variables.copilot.copilot_pro_plus_short %} | {% data variables.copilot.copilot_max_short %} |
| ------------------------------------------------------------------------------------------------ | ----------------------------------------------- | -------------------------------------------------- | ---------------------------------------------- | --------------------------------------------------- | ---------------------------------------------- |
| {% data variables.copilot.copilot_for_prs %}                                                     | {% octicon "x" aria-label="Not included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| Audit logs                                                                                       | {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| Content exclusion                                                                                | {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "x" aria-label="Not included" %}         | {% octicon "x" aria-label="Not included" %}    |
| {% data variables.copilot.copilot_cli_short %}                                                   | {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| {% data variables.copilot.github_copilot_app_short %}                                                   | {% octicon "check" aria-label="Included" %}     | {% octicon "check" aria-label="Included" %}        | {% octicon "check" aria-label="Included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}    |
| {% data variables.product.prodname_spark %} ({% data variables.release-phases.public_preview %}) | {% octicon "x" aria-label="Not included" %}     | {% octicon "x" aria-label="Not included" %}        | {% octicon "x" aria-label="Not included" %}    | {% octicon "check" aria-label="Included" %}         | {% octicon "x" aria-label="Not included" %}    |

{% endrowheaders %}

## Organization and enterprise plans

The {% data variables.product.prodname_copilot_short %} plans available for organizations and enterprises are: 
* {% data variables.copilot.copilot_business_short %}
* {% data variables.copilot.copilot_enterprise_short %}

With these plans you'll receive access to the following features and capabilities. 

> [!NOTE] With {% data variables.product.prodname_ghe_cloud %}, an enterprise owner chooses the plan for each organization in the enterprise. For guidance on choosing a plan, see [AUTOTITLE](<https://github.com/copilot/tutorials/roll-out-at-scale/assign-licenses/choose-enterprise-plan>).

### {% data variables.product.prodname_ai_credits %} allowance by plan

| Plan | Price per granted seat per month | {% data variables.product.prodname_ai_credits %} per user per month | 
| --- | --- | --- | 
| {% data variables.copilot.copilot_business_short %} | {% data variables.copilot.cfb_price_per_month %}  | {% data variables.copilot.ai_credits_per_user_business %} | 
| {% data variables.copilot.copilot_enterprise_short %}| {% data variables.copilot.ce_price_per_month %}  | {% data variables.copilot.ai_credits_per_user_enterprise %} | 

{% data variables.product.prodname_copilot_short %} usage is measured in {% data variables.product.prodname_ai_credits_short %} under usage-based billing. Each license contributes {% data variables.product.prodname_ai_credits_short %} to a shared enterprise pool, and usage beyond the pool is charged at {% data variables.product.prodname_ai_credits_value %} per {% data variables.product.prodname_ai_credit_singular %}. Code completions and {% data variables.copilot.next_edit_suggestions %} are not billed in {% data variables.product.prodname_ai_credits_short %} and remain unlimited for all paid plans.

For a full explanation of how {% data variables.product.prodname_ai_credits_short %} work, including pooling, additional usage, and what happens when credits run out, see [AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/organizations-and-enterprises/billing>).

### Inline suggestions and {% data variables.copilot.copilot_chat_short %}

**Inline suggestions**: Inline suggestion features are available in all plans:

* Real-time code suggestions with included models
* {% data variables.copilot.next_edit_suggestions_caps %}

**{% data variables.copilot.copilot_chat_short %}** features are available in all plans: 

* {% data variables.copilot.copilot_chat_short %} in IDEs
* Inline chat
* Slash commands
* {% data variables.copilot.copilot_mobile_short %}
* {% data variables.copilot.copilot_chat_dotcom_short %}
* {% data variables.copilot.copilot_chat_short %} in {% data variables.product.prodname_windows_terminal %}
* {% data variables.copilot.copilot_chat_short %} skills in IDEs[^1]

### Models

{% rowheaders %}

| Available models                               | {% data variables.copilot.copilot_business_short %} | {% data variables.copilot.copilot_enterprise_short %} |
|---------------------------------------------------------|-----------------------------------------------------|-------------------------------------------------------|
| {% for model in tables.copilot.model-supported-plans %} |
| {{ model.name }}{% if model.name == 'GPT-5.4 nano' %}[^gpt54nano]{% endif %}{% if model.name == 'Claude Fable 5' or model.name == 'Claude Fable 5.1' %}[^claude-fable-5]{% endif %} | {% if model.business == true %}{% octicon "check" aria-label="Included" %}{% else %}{% octicon "x" aria-label="Not included" %}{% endif %} | {% if model.enterprise == true %}{% octicon "check" aria-label="Included" %}{% else %}{% octicon "x" aria-label="Not included" %}{% endif %} |
| {% endfor %}                                            |

{% endrowheaders %}

[^gpt54nano]: <https://github.com/github/docs/blob/be38ec5d78e24172587e61b3c6ff40ace1865c71/content/copilot/get-started/GPT-5.4> nano is currently only available in the Codex {% data variables.product.prodname_vscode %} extension ({% data variables.copilot.copilot_pro_plus_short %} only) and is not available in {% data variables.copilot.copilot_chat_short %}.

### Agents

{% rowheaders %}

| Agents                                                                  | {% data variables.copilot.copilot_business_short %} | {% data variables.copilot.copilot_enterprise_short %} |
| ----------------------------------------------------------------------- | --------------------------------------------------- | ----------------------------------------------------- |
| {% data variables.copilot.copilot_cloud_agent %}                        | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Agent mode                                                              | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| {% data variables.product.prodname_copilot_short %} code review         | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Model Context Protocol (MCP)                                            | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Third-party Agents ({% data variables.release-phases.public_preview %}) | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |

{% endrowheaders %}

### Customization

{% rowheaders %}

| Customization                                                                    | {% data variables.copilot.copilot_business_short %} | {% data variables.copilot.copilot_enterprise_short %} |
| -------------------------------------------------------------------------------- | --------------------------------------------------- | ----------------------------------------------------- |
| Repository and personal custom instructions                                      | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Organization custom instructions                                                 | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Prompt files                                                                     | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Model Context Protocol (MCP)                                                     | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Block suggestions matching public code                                           | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Exclude specified files from {% data variables.product.prodname_copilot_short %} | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Organization-wide policy management                                              | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |

{% endrowheaders %}

### Other features

{% rowheaders %}

|                                                                                                  | {% data variables.copilot.copilot_business_short %} | {% data variables.copilot.copilot_enterprise_short %} |
| ------------------------------------------------------------------------------------------------ | --------------------------------------------------- | ----------------------------------------------------- |
| {% data variables.copilot.copilot_for_prs %}                                                     | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Audit logs                                                                                       | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| Content exclusion                                                                                | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| {% data variables.copilot.copilot_cli_short %}                                                   | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| {% data variables.copilot.github_copilot_app_short %}                                                  | {% octicon "check" aria-label="Included" %}         | {% octicon "check" aria-label="Included" %}           |
| {% data variables.product.prodname_spark %} ({% data variables.release-phases.public_preview %}) | {% octicon "x" aria-label="Not included" %}         | {% octicon "check" aria-label="Included" %}           |

{% endrowheaders %}

## Further reading

* To compare feature support across IDEs, see [AUTOTITLE](<https://github.com/copilot/reference/copilot-feature-matrix>).
* To compare models supported across plans, features, and IDEs, see [AUTOTITLE](<https://github.com/copilot/reference/ai-models/supported-models>).

[^1]: <https://github.com/github/docs/blob/be38ec5d78e24172587e61b3c6ff40ace1865c71/content/copilot/get-started/{%> data variables.copilot.copilot_chat_short %} skills in IDEs is available in {% data variables.product.prodname_vscode %} and {% data variables.product.prodname_vs %}.
[^claude-fable-5]: <https://github.com/github/docs/blob/be38ec5d78e24172587e61b3c6ff40ace1865c71/content/copilot/get-started/When> {% data variables.copilot.copilot_claude_fable_5 %} or {% data variables.copilot.copilot_claude_fable_51 %} is used, Anthropic retains data, including prompts and outputs, by default to operate safety classifiers that detect harmful use. Customers can request to use {% data variables.copilot.copilot_claude_fable_5 %} or {% data variables.copilot.copilot_claude_fable_51 %} with zero data retention (ZDR) through the end of 2026 under a time-bound exemption while Anthropic rolls out Enterprise Frontier Safeguards (EFS). After that point, continued use of these models would require EFS, which will enable eligible customers to keep their data under their own control while also enabling automated safety monitoring. For an enterprise that has been approved and configured for this type of access, {% data variables.copilot.copilot_claude_fable_5 %} and {% data variables.copilot.copilot_claude_fable_51 %} requests will use the ZDR endpoint through the end of 2026 when the models are enabled. To learn whether your enterprise is eligible and request access, contact your {% data variables.product.github %} account team. Approval for access does not automatically enable {% data variables.copilot.copilot_claude_fable_5 %} or {% data variables.copilot.copilot_claude_fable_51 %}. An enterprise or organization administrator must still enable each model before users can access it. Other Claude models, except for {% data variables.copilot.copilot_claude_fable_5 %} and {% data variables.copilot.copilot_claude_fable_51 %}, continue to operate under ZDR. Customers who enable a Fable model with ZDR agree to use this model only for internal operations, including to develop and evaluate products for their own customers. They may not make the model endpoints or outputs available externally. To enable these models, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/set-up-copilot/configure-access-to-ai-models>).


</details>
