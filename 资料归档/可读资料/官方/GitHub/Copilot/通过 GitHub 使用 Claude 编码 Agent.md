# 通过 GitHub 使用 Claude 编码 Agent

介绍 GitHub 集成的 Anthropic Claude 编码 Agent 与支持模型；这是 GitHub 产品内的合作方接入说明。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-25](<https://github.com/github/docs/commit/a3acef5a1e89964266aa14de432b6db92d017c1f>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/agents/anthropic-claude.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/通过 GitHub 使用 Claude 编码 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Anthropic Claude
shortTitle: Anthropic Claude
intro: 'Use the {% data variables.product.prodname_anthropic_claude %} coding agent powered by {% data variables.product.prodname_copilot_short %}.'
product: '{% data reusables.gated-features.anthropic-claude %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: concepts
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [！NOTE] {% data variables.product.prodname_anthropic_claude %}编码agent当前位于{% data variables.release-phases.public_preview %}中。

## 简介

{% data variables.product.prodname_anthropic_claude %} 编码 agent 使用 Claude Agent SDK，并且可以由您现有的 {% data variables.product.prodname_copilot_short %} 订阅提供支持。有关 {% data variables.product.prodname_anthropic_claude %} 的信息，请参阅 [{% data variables.product.prodname_anthropic_claude %} 文档 ](<https://platform.claude.com/docs/en/agent-sdk/overview>)。

## {% data variables.product.prodname_anthropic_claude %}编码agent

在将任务分配给编码 agent 的 {% data variables.product.prodname_anthropic_claude %} 之前，必须先启用它。请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/about-third-party-coding-agents#making-coding-agents-available>)。

要了解有关使用第三方编码 agents 的更多信息，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/agents/about-third-party-coding-agents>)。

### 支持机型

当使用{% data variables.product.prodname_anthropic_claude %}编码agent启动任务时，您可以选择agent使用的AI模型。有以下型号可供选择：

* 汽车
{% data reusables.copilot.anthropic-claude-agent-models %}

如果您选择**自动**，它将选择上面列出的可用型号之一。 **{% data variables.product.prodname_anthropic_claude %}编码agent中的**自动**不**杠杆{% data variables.copilot.copilot_auto_model_selection %}。


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE] The {% data variables.product.prodname_anthropic_claude %} coding agent is currently in {% data variables.release-phases.public_preview %}.

## Introduction

The {% data variables.product.prodname_anthropic_claude %} coding agent uses the Claude Agent SDK and can be powered by your existing {% data variables.product.prodname_copilot_short %} subscription. For information about {% data variables.product.prodname_anthropic_claude %}, see the [{% data variables.product.prodname_anthropic_claude %} documentation](<https://platform.claude.com/docs/en/agent-sdk/overview>).

## {% data variables.product.prodname_anthropic_claude %} coding agent

Before you can assign tasks to {% data variables.product.prodname_anthropic_claude %} coding agent, it must be enabled. See [AUTOTITLE](<https://github.com/copilot/concepts/agents/about-third-party-coding-agents#making-coding-agents-available>).

To learn more about using third-party coding agents, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/about-third-party-coding-agents>).

### Supported models

When starting a task with the {% data variables.product.prodname_anthropic_claude %} coding agent, you can select the AI model used by the agent. The following models are available:

* Auto
{% data reusables.copilot.anthropic-claude-agent-models %}

If you select **Auto**, it will choose one of the available models listed above. **Auto** in {% data variables.product.prodname_anthropic_claude %} coding agent does **not** leverage {% data variables.copilot.copilot_auto_model_selection %}.


</details>
