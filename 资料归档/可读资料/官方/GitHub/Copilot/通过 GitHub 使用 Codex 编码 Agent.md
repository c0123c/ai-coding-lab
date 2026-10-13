# 通过 GitHub 使用 Codex 编码 Agent

介绍 GitHub 中的 OpenAI Codex Agent、VS Code 扩展与模型可用性；属于 GitHub 集成说明。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-25](<https://github.com/github/docs/commit/a3acef5a1e89964266aa14de432b6db92d017c1f>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/agents/openai-codex.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/通过 GitHub 使用 Codex 编码 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: OpenAI Codex
shortTitle: OpenAI Codex
allowTitleToDifferFromFilename: true
intro: 'Use the {% data variables.product.prodname_openai_codex %} coding agent and Visual Studio Code extension powered by {% data variables.product.prodname_copilot_short %}.'
product: '{% data reusables.gated-features.openai-codex %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_plan=cfi&ref_type=purchase&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: concepts
category:
  - Learn about Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [！NOTE] {% data variables.product.prodname_openai_codex %} 集成当前位于 {% data variables.release-phases.public_preview %} 中。

## 简介

{% data variables.product.prodname_openai_codex %} 编码 agent 和 {% data variables.product.prodname_vscode_shortname %} {% data variables.product.prodname_openai_codex %} 集成使用 Codex SDK，并且可以由您现有的 {% data variables.product.prodname_copilot_short %} 订阅提供支持。有关 {% data variables.product.prodname_openai_codex %} 工作原理的更多信息，请参阅 [{% data variables.product.prodname_openai_codex %} 文档 ](<https://developers.openai.com/codex>)。

## {% data variables.product.prodname_openai_codex %}编码agent

在将任务分配给编码 agent 的 {% data variables.product.prodname_openai_codex %} 之前，必须先启用它。请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/about-third-party-coding-agents#making-coding-agents-available>)。

要了解有关在 {% data variables.product.github %} 上使用第三方 agents 的更多信息，请参阅 [AUTOTITLE](<https://github.com/free-pro-team@latest/copilot/concepts/agents/about-third-party-coding-agents>)。

### 支持机型

当使用{% data variables.product.prodname_openai_codex %}编码agent启动任务时，您可以选择agent使用的AI模型。有以下型号可供选择：

* 汽车
{% data reusables.copilot.openai-codex-agent-models %}

如果您选择**自动**，它将选择上面列出的可用型号之一。 **{% data variables.product.prodname_openai_codex %}编码agent中的**自动**不**杠杆{% data variables.copilot.copilot_auto_model_selection %}。

## {% data variables.product.prodname_vscode_shortname %} 扩展名

> [!NOTE] {% data variables.product.prodname_openai_codex %} {% data variables.product.prodname_vscode_shortname %} 分机中的“使用 {% data variables.product.prodname_copilot_short %} 登录”选项仅适用于 {% data variables.copilot.copilot_pro_plus %} 和 {% data variables.copilot.copilot_max_short %} 用户。

启动扩展程序时使用“使用 {% data variables.product.prodname_copilot_short %} 登录”。 {% data variables.copilot.copilot_pro_plus_short %} 和 {% data variables.copilot.copilot_max_short %} 用户可以在 {% data variables.product.prodname_vscode_shortname %} 内部人员的 [Agent 会话视图 ](<https://code.visualstudio.com/docs/copilot/chat/chat-sessions#_agent-sessions-view>) 中看到此集成以及其正在运行的任务的进度。所有使用均受 {% data variables.product.github %} 费率限制和计费的约束。请参阅[AUTOTITLE](<https://github.com/copilot/reference/copilot-billing/request-based-billing-legacy/copilot-requests#premium-features>)。

### 型号可用性

可用模型的子集可能仅在 {% data variables.product.prodname_openai_codex %} 扩展中可用。模型可用性和可见性不受 {% data variables.product.prodname_copilot_short %} 模型配置策略的约束。


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE] {% data variables.product.prodname_openai_codex %} integration is currently in {% data variables.release-phases.public_preview %}.

## Introduction

The {% data variables.product.prodname_openai_codex %} coding agent and the {% data variables.product.prodname_vscode_shortname %} {% data variables.product.prodname_openai_codex %} integration use the Codex SDK and can be powered by your existing {% data variables.product.prodname_copilot_short %} subscription. For more information about how {% data variables.product.prodname_openai_codex %} works, see the [{% data variables.product.prodname_openai_codex %} documentation](<https://developers.openai.com/codex>).

## {% data variables.product.prodname_openai_codex %} coding agent

Before you can assign tasks to {% data variables.product.prodname_openai_codex %} coding agent, it must be enabled. See [AUTOTITLE](<https://github.com/copilot/concepts/agents/about-third-party-coding-agents#making-coding-agents-available>).

To learn more about using third-party agents on {% data variables.product.github %}, see [AUTOTITLE](<https://github.com/free-pro-team@latest/copilot/concepts/agents/about-third-party-coding-agents>).

### Supported models

When starting a task with the {% data variables.product.prodname_openai_codex %} coding agent, you can select the AI model used by the agent. The following models are available:

* Auto
{% data reusables.copilot.openai-codex-agent-models %}

If you select **Auto**, it will choose one of the available models listed above. **Auto** in {% data variables.product.prodname_openai_codex %} coding agent does **not** leverage {% data variables.copilot.copilot_auto_model_selection %}.

## {% data variables.product.prodname_vscode_shortname %} extension

> [!NOTE] The "Sign in with {% data variables.product.prodname_copilot_short %}" option in the {% data variables.product.prodname_openai_codex %} {% data variables.product.prodname_vscode_shortname %} extension is only available to {% data variables.copilot.copilot_pro_plus %} and {% data variables.copilot.copilot_max_short %} subscribers.

Use "Sign in with {% data variables.product.prodname_copilot_short %}" when launching the extension. {% data variables.copilot.copilot_pro_plus_short %} and {% data variables.copilot.copilot_max_short %} users can see this integration in the [Agent Sessions view](<https://code.visualstudio.com/docs/copilot/chat/chat-sessions#_agent-sessions-view>) in {% data variables.product.prodname_vscode_shortname %} Insiders along with progress on their running tasks. All usage is subject to {% data variables.product.github %} rate limits and billing. See [AUTOTITLE](<https://github.com/copilot/reference/copilot-billing/request-based-billing-legacy/copilot-requests#premium-features>).

### Model availability

A subset of available models may only be available in the {% data variables.product.prodname_openai_codex %} extension. Model availability and visibility is not governed by {% data variables.product.prodname_copilot_short %} model configuration policies.


</details>
