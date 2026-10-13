# Copilot 自定义 Agent 的使用范围（文档源码）

说明自定义角色在网页、IDE、桌面和 CLI 中的使用位置及配置差异；此源码引用共享模板，部分说明需结合模板阅读。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方文件提交（截至抓取）：[2026-09-04](<https://github.com/github/docs/commit/31dde23b61c15aa120f1cd4747d0a777f423b118>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/concepts/agents/cloud-agent/about-custom-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 自定义 Agent 的使用范围（文档源码）.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: About custom agents
shortTitle: Custom agents
intro: '{% data variables.copilot.custom_agents_caps_short %} enhance {% data variables.product.prodname_copilot_short %} with assistance tailored to your needs.'
versions:
  feature: copilot
category:
  - Learn about Copilot
contentType: concepts
redirect_from:
  - /copilot/concepts/agents/coding-agent/about-custom-agents
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data reusables.copilot.copilot-cli.custom-agents-about-intro %}

{% data reusables.copilot.copilot-cli.custom-agents-about-details %}

## 哪里可以使用{% data variables.copilot.custom_agents_short %}

{% data reusables.copilot.custom-agents-ide-preview %}

一旦您创建了 {% data variables.copilot.custom_agents_short %}，它们就可以用于：

* {% data variables.product.prodname_dotcom_the_website %}** 上的 **{% data variables.copilot.copilot_cloud_agent %}：agents 选项卡和面板、问题分配和拉取请求
* IDE 中的 **{% data variables.copilot.copilot_cloud_agent %} **：{% data variables.product.prodname_vscode %}、JetBrains IDE、Eclipse 和 Xcode
* **{% data variables.copilot.github_copilot_app %}**
* **{% data variables.copilot.copilot_cli %}**

您可以直接在 {% data variables.product.prodname_vscode %}、JetBrains IDE、Eclipse 和 Xcode 中使用 {% data variables.copilot.agent_profiles %}。某些属性在环境之间可能会有所不同或被忽略。

有关使用的更多信息 {% data variables.copilot.custom_agents_short %} 在 {% data variables.product.prodname_vscode %}，参见 [{% data variables.copilot.custom_agents_caps_short %} 在 {% data variables.product.prodname_vscode_shortname %}](<https://code.visualstudio.com/docs/copilot/customization/custom-agents>).

## 后续步骤

要创建您自己的 {% data variables.copilot.custom_agents_short %}，请参阅：

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/customization-cheat-sheet>)


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data reusables.copilot.copilot-cli.custom-agents-about-intro %}

{% data reusables.copilot.copilot-cli.custom-agents-about-details %}

## Where you can use {% data variables.copilot.custom_agents_short %}

{% data reusables.copilot.custom-agents-ide-preview %}

Once you create {% data variables.copilot.custom_agents_short %}, they become available to:

* **{% data variables.copilot.copilot_cloud_agent %} on {% data variables.product.prodname_dotcom_the_website %}**: The agents tab and panel, issue assignment, and pull requests
* **{% data variables.copilot.copilot_cloud_agent %} in IDEs**: {% data variables.product.prodname_vscode %}, JetBrains IDEs, Eclipse, and Xcode
* **{% data variables.copilot.github_copilot_app %}**
* **{% data variables.copilot.copilot_cli %}**

You can use {% data variables.copilot.agent_profiles %} directly in {% data variables.product.prodname_vscode %}, JetBrains IDEs, Eclipse, and Xcode. Some properties may function differently or be ignored between environments.

For more information on using {% data variables.copilot.custom_agents_short %} in {% data variables.product.prodname_vscode %}, see [{% data variables.copilot.custom_agents_caps_short %} in {% data variables.product.prodname_vscode_shortname %}](<https://code.visualstudio.com/docs/copilot/customization/custom-agents>).

## Next steps

To create your own {% data variables.copilot.custom_agents_short %}, see:

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/customization-cheat-sheet>)


</details>
