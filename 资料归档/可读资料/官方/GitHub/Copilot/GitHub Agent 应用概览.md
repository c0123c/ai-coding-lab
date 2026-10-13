# GitHub Agent 应用概览

说明如何在 GitHub 工作流中接入合作方 Agent，涵盖可用位置、安装启用、运行方式和计费；属于产品集成说明。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/agents/agent-apps.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/GitHub Agent 应用概览.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: About agent apps
shortTitle: Agent apps
allowTitleToDifferFromFilename: true
intro: '{% data variables.copilot.agent_apps_caps %} let you use partner-built agents directly in your workflows on {% data variables.product.github %}, powered by your {% data variables.product.prodname_copilot_short %} subscription.'
product: '{% data reusables.copilot.plans.permission-paid-plans-no-purchase-link %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=button&utm_source=docs-about-agent-apps&utm_medium=docs&utm_campaign=msbuild-2026" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: concepts
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [!NOTE] {% data reusables.copilot.agent-apps-preview %}

## 简介

{% data reusables.copilot.agent-apps-intro %} {% data variables.copilot.agent_apps_caps %} 由 {% data variables.copilot.copilot_cloud_agent %} 供电。要了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)。

例如，{% data variables.copilot.agent_app %} 可以分析您的产品分析，扫描您的应用程序是否存在安全漏洞，或向拉取请求添加功能标记，然后连接回合作伙伴的系统以完成任务。

您可以在[{% data variables.product.prodname_marketplace %}](<https://github.com/marketplace?type=apps&category=agent-apps>)上查看可用的应用程序。

## 哪里可以使用{% data variables.copilot.agent_apps %}

{% data reusables.copilot.agent-apps-entrypoints %}

您可以像使用 {% data variables.copilot.copilot_cloud_agent %} 和其他第三方编码 agents 一样使用这些入口点。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/use-agent-apps>)。

## {% data variables.copilot.agent_apps %} 如何运作

{% data variables.copilot.agent_app %} 是合作伙伴配置为充当 agent 的 {% data variables.product.prodname_github_app %}。每个 {% data variables.copilot.agent_app %} 都可以使用自己的提示、模型、工具和 Model Context Protocol (MCP) 服务器定义自定义 agent。要了解有关自定义 agents 和 MCP 的更多信息，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/about-custom-agents>) 和 [AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent>)。

{% data variables.copilot.agent_apps_caps %}可以通过MCP服务器连接到合作伙伴自己的系统。这些 MCP 服务器使用 {% data variables.product.github %} 发出的 JWT 断言来授权 {% data variables.copilot.agent_app %}，因此合作伙伴可以安全地识别您的帐户及其 {% data variables.copilot.agent_app %}，而无需您管理其他凭据。

首次使用 {% data variables.copilot.agent_app %} 时，系统会要求您在 agent 运行之前通过 OAuth 流程授权应用程序。这使得 {% data variables.copilot.agent_app %} 可以代表您行事并访问合作伙伴的功能。有关授权应用程序的更多信息，请参阅[AUTOTITLE](<https://github.com/apps/using-github-apps/authorizing-github-apps>)。

## 安装并启用{% data variables.copilot.agent_apps %}

要使用 {% data variables.copilot.agent_app %}，您的帐户或组织中必须安装 {% data variables.product.prodname_github_app %}，并且必须为该应用启用 agent 功能。如果应用程序安装在企业拥有的组织中，则还必须在企业 {% data variables.product.prodname_copilot_short %} 设置中启用“{% data variables.copilot.agent_apps_caps %}”{% data variables.product.prodname_copilot_short %} 策略。

* 当您安装 {% data variables.copilot.agent_app %} 时，{% data variables.product.github %} 会突出显示该应用程序包含 agent 功能，并询问您是否要启用它们。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/apps/using-github-apps/about-using-github-apps>)。
* 如果应用程序安装在企业拥有的组织中，则管理员还必须启用“{% data variables.copilot.agent_apps %}”{% data variables.product.prodname_copilot_short %} 策略，然后才能使用 agent 功能。有关详细信息，请参阅“后续步骤”部分。

{% data reusables.copilot.model-access-agent-apps %} 参见[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-availability-of-default-models>)。

## 计费

{% data reusables.copilot.agent-apps-billing %}

## 后续步骤

* 要了解如何使用 {% data variables.copilot.agent_app %}，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/use-agent-apps>)。
* 要安装 {% data variables.copilot.agent_app %} 并选择使用其 agent 功能，请参阅 [AUTOTITLE](<https://github.com/apps/using-github-apps/about-using-github-apps>)。
* 要在企业拥有的组织中启用 {% data variables.copilot.agent_apps %}，管理员必须在企业级别启用“{% data variables.copilot.agent_apps %}”策略，然后您才能使用它。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/enable-copilot-cloud-agent#enabling-agent-apps-and-third-party-agents>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE] {% data reusables.copilot.agent-apps-preview %}

## Introduction

{% data reusables.copilot.agent-apps-intro %} {% data variables.copilot.agent_apps_caps %} are powered by {% data variables.copilot.copilot_cloud_agent %}. To learn more, see [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>).

For example, an {% data variables.copilot.agent_app %} could analyze your product analytics, scan your application for security vulnerabilities, or add feature flags to a pull request, then connect back to the partner's systems to complete the task.

You can view available apps on [{% data variables.product.prodname_marketplace %}](<https://github.com/marketplace?type=apps&category=agent-apps>).

## Where you can use {% data variables.copilot.agent_apps %}

{% data reusables.copilot.agent-apps-entrypoints %}

You can use these entry points in the same way as you use {% data variables.copilot.copilot_cloud_agent %} and other third-party coding agents. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/use-agent-apps>).

## How {% data variables.copilot.agent_apps %} work

An {% data variables.copilot.agent_app %} is a {% data variables.product.prodname_github_app %} that a partner has configured to act as an agent. Each {% data variables.copilot.agent_app %} can define a custom agent with its own prompt, model, tools, and Model Context Protocol (MCP) servers. To learn more about custom agents and MCP, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/about-custom-agents>) and [AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent>).

{% data variables.copilot.agent_apps_caps %} can connect to the partner's own systems through MCP servers. These MCP servers authorize the {% data variables.copilot.agent_app %} using a JWT assertion issued by {% data variables.product.github %}, so the partner can securely identify your account and their {% data variables.copilot.agent_app %} without you managing additional credentials.

The first time you use an {% data variables.copilot.agent_app %}, you are asked to authorize the app through an OAuth flow before the agent runs. This lets the {% data variables.copilot.agent_app %} act on your behalf and access the partner's functionality. For more information about authorizing apps, see [AUTOTITLE](<https://github.com/apps/using-github-apps/authorizing-github-apps>).

## Installing and enabling {% data variables.copilot.agent_apps %}

To use an {% data variables.copilot.agent_app %}, the {% data variables.product.prodname_github_app %} must be installed on your account or organization, and agent features must be enabled for the app. If the app is installed in an organization owned by an enterprise, the "{% data variables.copilot.agent_apps_caps %}" {% data variables.product.prodname_copilot_short %} policy must also be enabled in your enterprise {% data variables.product.prodname_copilot_short %} settings.

* When you install an {% data variables.copilot.agent_app %}, {% data variables.product.github %} highlights that the app includes agent features and asks you if you want to enable them. For more information, see [AUTOTITLE](<https://github.com/apps/using-github-apps/about-using-github-apps>).
* If the app is installed in an organization owned by an enterprise, an administrator must also enable the "{% data variables.copilot.agent_apps %}" {% data variables.product.prodname_copilot_short %} policy before the agent features become available. For more information, see the "Next steps" section.

{% data reusables.copilot.model-access-agent-apps %} See [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-availability-of-default-models>).

## Billing

{% data reusables.copilot.agent-apps-billing %}

## Next steps

* To learn about using an {% data variables.copilot.agent_app %}, see [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/use-agent-apps>).
* To install an {% data variables.copilot.agent_app %} and opt in to its agent features, see [AUTOTITLE](<https://github.com/apps/using-github-apps/about-using-github-apps>).
* To enable using {% data variables.copilot.agent_apps %} in an organization owned by an enterprise, an administrator must enable the "{% data variables.copilot.agent_apps %}" policy at the enterprise level before you can use it. See [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/enable-copilot-cloud-agent#enabling-agent-apps-and-third-party-agents>).


</details>
