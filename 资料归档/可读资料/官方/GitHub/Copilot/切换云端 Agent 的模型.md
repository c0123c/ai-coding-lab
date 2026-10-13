# 切换云端 Agent 的模型

介绍启动任务时选择支持模型与推理级别的方式，以及不同入口的条件和限制，帮助理解任务执行的模型配置。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/切换云端 Agent 的模型.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Changing the AI model for GitHub Copilot cloud agent
shortTitle: Changing the AI model
allowTitleToDifferFromFilename: true
intro: 'In supported entrypoints, when starting a task with {% data variables.copilot.copilot_cloud_agent %}, you can select the model used, and, for supported models, the reasoning level.'
product: '{% data reusables.gated-features.copilot-cloud-agent %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: how-tos
category: 
  - Author and optimize with Copilot
redirect_from:
  - /copilot/how-tos/use-copilot-agents/coding-agent/changing-the-ai-model
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [!NOTE]
> 有关 {% data variables.copilot.copilot_cloud_agent %} 的概述，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/copilot-surfaces/copilot-on-github>)。

## 简介

在支持的入口点中，您可以选择{% data variables.copilot.copilot_cloud_agent %}使用的模型。

您可能会发现不同的模型表现更好，或者提供更有用的响应，具体取决于您给出的任务类型 {% data variables.product.prodname_copilot_short %}。

> [!NOTE]
> 型号选择为 {% data variables.copilot.copilot_cloud_agent %} 仅当将问题分配给 {% data variables.product.prodname_copilot_short %} 上 {% data variables.product.prodname_dotcom_the_website %}，当提到 `@copilot` 在拉取请求评论中 {% data variables.product.prodname_dotcom_the_website %}，或者从 agents 选项卡， agents 面板， {% data variables.product.prodname_mobile %} 或 Raycast 启动器。如果模型选择器不可用，将自动使用“自动”。看 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions>).

## 支持机型

目前可以使用以下选项：

* 汽车
{% data reusables.copilot.copilot-cloud-agent-non-auto-models %}

{% data reusables.copilot.auto-model-option %}

## 推理水平

某些模型支持可配置的推理级别，该级别控制模型在响应之前花费多少时间和精力进行推理。较高的推理水平可以提高对复杂任务的响应质量，但可能需要更长时间并使用更多{% data variables.product.prodname_ai_credits_short %}。

如果您选择支持可配置推理的模型，则会出现第二个下拉菜单，以便您可以选择想要 {% data variables.copilot.copilot_cloud_agent %} 用于该任务的推理级别。有关哪些模型支持可配置推理的更多信息，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/ai-models/supported-models#models-with-extended-capabilities>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE]
> For an overview of {% data variables.copilot.copilot_cloud_agent %}, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/copilot-surfaces/copilot-on-github>).

## Introduction

In supported entrypoints, you can select the model used by {% data variables.copilot.copilot_cloud_agent %}.

You may find that different models perform better, or provide more useful responses, depending on the type of tasks you give {% data variables.product.prodname_copilot_short %}.

> [!NOTE]
> Model selection for {% data variables.copilot.copilot_cloud_agent %} is only supported when assigning an issue to {% data variables.product.prodname_copilot_short %} on {% data variables.product.prodname_dotcom_the_website %}, when mentioning `@copilot` in a pull request comment on {% data variables.product.prodname_dotcom_the_website %}, or when starting a task from the agents tab, agents panel, {% data variables.product.prodname_mobile %} or the Raycast launcher. Where a model picker is not available, Auto will be used automatically. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions>).

## Supported models

The following options are currently available:

* Auto 
{% data reusables.copilot.copilot-cloud-agent-non-auto-models %}

{% data reusables.copilot.auto-model-option %}

## Reasoning level

Some models support configurable reasoning levels, which control how much time and effort the model spends reasoning before it responds. Higher reasoning levels can improve the quality of responses to complex tasks, but may take longer and use more {% data variables.product.prodname_ai_credits_short %}.

If you select a model that supports configurable reasoning, a second dropdown menu appears so you can choose the reasoning level you want {% data variables.copilot.copilot_cloud_agent %} to use for that task. For more information about which models support configurable reasoning, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/ai-models/supported-models#models-with-extended-capabilities>).


</details>
