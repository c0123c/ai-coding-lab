# 在 IDE 中调用子 Agent

说明将独立子任务交给拥有单独上下文的 Agent，并将结果返回主会话；涵盖启用、调用与支持条件。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方文件提交（截至抓取）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 IDE 中调用子 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: 'Using {% data variables.copilot.subagents_short %} in your IDE'
shortTitle: Use subagents
intro: 'Delegate a self-contained subtask to a separate agent that works in its own context and reports back to your main chat session.'
defaultTool: vscode
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

## 简介

{% data reusables.copilot.subagent-intro %}

{% data variables.copilot.subagents_caps_short %} 可在 {% data variables.product.prodname_vscode %}、JetBrains IDE、Xcode 和 Eclipse 中使用。它们目前在 {% data variables.product.prodname_vs %} 中不可用。启用和调用它们的步骤因编辑器而异，因此请单击上面的选项卡以获取适用于您的 IDE 的说明。

对于 {% data variables.copilot.subagents_short %} 委托的 agent 会话，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)。

<!-- --------------------- -->
<!-- VS Code -->
<!-- --------------------- -->

{% vscode %}

## 启用{% data variables.copilot.subagents_short %}

1. 在{% data variables.copilot.copilot_chat_short %}窗口中，单击工具图标。
1. 启用`runSubagent`工具。

如果您使用自定义提示文件或 {% data variables.copilot.custom_agents_short %}，确保您指定 `runSubagent` 工具在 `tools` frontmatter 属性。参见 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents#configuring-an-agent-profile>), 和 [使用提示文件 VS Code](<https://code.visualstudio.com/docs/copilot/customization/prompt-files>) 在 {% data variables.product.prodname_vscode %} 文档。

## 调用{% data variables.copilot.subagents_short %}

{% data reusables.copilot.using-subagents %}
* **调用#runSubagent工具。**

   ```text
   Evaluate the #file:databaseSchema using #runSubagent and generate an optimized data-migration plan.
   ```

当 {% data variables.copilot.subagent_short %} 完成其任务时，其结果会出现在主聊天会话中，为后续问题或后续步骤做好准备。

## 进一步阅读

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/customization-cheat-sheet>)

{% endvscode %}

<!-- --------------------- -->
<!-- JetBrains -->
<!-- --------------------- -->

{% jetbrains %}

要使用 {% data variables.copilot.subagents_short %}，您 ** 必须在您的环境 ** 中配置 {% data variables.copilot.custom_agents_short %}。请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>)。

## 启用{% data variables.copilot.subagents_short %}

1. 单击菜单栏中的**工具**，然后单击**{% data variables.product.prodname_copilot %}**，然后单击**编辑设置**。
1. 在弹出菜单中，单击**聊天**，然后单击**启用{% data variables.copilot.subagent_caps_short %}**复选框。

## 调用{% data variables.copilot.subagents_short %}

{% data reusables.copilot.using-subagents %}

当 {% data variables.copilot.subagent_short %} 完成其任务时，其结果会出现在主聊天会话中，为后续问题或后续步骤做好准备。

## 进一步阅读

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/customization-cheat-sheet>)

{% endjetbrains %}

<!-- --------------------- -->
<!-- Xcode -->
<!-- --------------------- -->

{% xcode %}

要使用 {% data variables.copilot.subagents_short %}，您 ** 必须在您的环境 ** 中配置 {% data variables.copilot.custom_agents_short %}。请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>)。

## 启用{% data variables.copilot.subagents_short %}

1. 单击菜单栏中的**编辑器**，然后单击**{% data variables.product.prodname_copilot %}**，然后单击**打开GitHub Copilot进行Xcode设置**。
1. 单击聊天面板中的 ** 高级 **，然后在 ** 聊天设置 ** 下单击 ** 启用 {% data variables.copilot.subagents_caps_short %}** 开关。

## 调用{% data variables.copilot.subagents_short %}

{% data reusables.copilot.using-subagents %}

当 {% data variables.copilot.subagent_short %} 完成其任务时，其结果会出现在主聊天会话中，为后续问题或后续步骤做好准备。

## 进一步阅读

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/customization-cheat-sheet>)

{% endxcode %}

<!-- --------------------- -->
<!-- Eclipse -->
<!-- --------------------- -->

{% eclipse %}

要使用 {% data variables.copilot.subagents_short %}，您 ** 必须在您的环境 ** 中配置 {% data variables.copilot.custom_agents_short %}。请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>)。

## 启用{% data variables.copilot.subagents_short %}

1. 单击状态栏中的**{% octicon "copilot" aria-hidden="true" aria-label="copilot" %}**图标。
1. 在弹出菜单中，单击**编辑首选项**。
1. 在**聊天**下，单击**启用子agent**复选框。

## 调用{% data variables.copilot.subagents_short %}

{% data reusables.copilot.using-subagents %}

当 {% data variables.copilot.subagent_short %} 完成其任务时，其结果会出现在主聊天会话中，为后续问题或后续步骤做好准备。

## 进一步阅读

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/customization-cheat-sheet>)

{% endeclipse %}


## 原文对照

<details>
<summary>展开完整原文</summary>

## Introduction

{% data reusables.copilot.subagent-intro %}

{% data variables.copilot.subagents_caps_short %} are available in {% data variables.product.prodname_vscode %}, JetBrains IDEs, Xcode, and Eclipse. They are not currently available in {% data variables.product.prodname_vs %}. The steps to enable and invoke them differ by editor, so click the tabs above for instructions for your IDE.

For the agent session that {% data variables.copilot.subagents_short %} are delegated from, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>).

<!-- --------------------- -->
<!-- VS Code -->
<!-- --------------------- -->

{% vscode %}

## Enabling {% data variables.copilot.subagents_short %}

1. In the {% data variables.copilot.copilot_chat_short %} window, click the tools icon.
1. Enable the `runSubagent` tool.

If you use custom prompt files or {% data variables.copilot.custom_agents_short %}, ensure you specify the `runSubagent` tool in the `tools` frontmatter property. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents#configuring-an-agent-profile>), and [Use prompt files in VS Code](<https://code.visualstudio.com/docs/copilot/customization/prompt-files>) in the {% data variables.product.prodname_vscode %} documentation.

## Invoking {% data variables.copilot.subagents_short %}

{% data reusables.copilot.using-subagents %}
* **Calling the #runSubagent tool.**

   ```text
   Evaluate the #file:databaseSchema using #runSubagent and generate an optimized data-migration plan.
   ```

When the {% data variables.copilot.subagent_short %} completes its task, its results appear back in the main chat session, ready for follow-up questions or next steps.

## Further reading

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/customization-cheat-sheet>)

{% endvscode %}

<!-- --------------------- -->
<!-- JetBrains -->
<!-- --------------------- -->

{% jetbrains %}

To use {% data variables.copilot.subagents_short %}, you **must have {% data variables.copilot.custom_agents_short %} configured in your environment**. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>).

## Enabling {% data variables.copilot.subagents_short %}

1. Click **Tools** in the menu bar, then click **{% data variables.product.prodname_copilot %}**, then **Edit Settings**.
1. In the popup menu, click **Chat**, then click the **Enable {% data variables.copilot.subagent_caps_short %}** checkbox.

## Invoking {% data variables.copilot.subagents_short %}

{% data reusables.copilot.using-subagents %}

When the {% data variables.copilot.subagent_short %} completes its task, its results appear back in the main chat session, ready for follow-up questions or next steps.

## Further reading

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/customization-cheat-sheet>)

{% endjetbrains %}

<!-- --------------------- -->
<!-- Xcode -->
<!-- --------------------- -->

{% xcode %}

To use {% data variables.copilot.subagents_short %}, you **must have {% data variables.copilot.custom_agents_short %} configured in your environment**. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>).

## Enabling {% data variables.copilot.subagents_short %}

1. Click **Editor** in the menu bar, then click **{% data variables.product.prodname_copilot %}** then **Open GitHub Copilot for Xcode Settings**.
1. Click **Advanced** in the chat panel, then under **Chat Settings** click the **Enable {% data variables.copilot.subagents_caps_short %}** toggle.

## Invoking {% data variables.copilot.subagents_short %}

{% data reusables.copilot.using-subagents %}

When the {% data variables.copilot.subagent_short %} completes its task, its results appear back in the main chat session, ready for follow-up questions or next steps.

## Further reading

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/customization-cheat-sheet>)

{% endxcode %}

<!-- --------------------- -->
<!-- Eclipse -->
<!-- --------------------- -->

{% eclipse %}

To use {% data variables.copilot.subagents_short %}, you **must have {% data variables.copilot.custom_agents_short %} configured in your environment**. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>).

## Enabling {% data variables.copilot.subagents_short %}

1. Click the **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %}** icon in the status bar.
1. In the popup menu, click **Edit Preferences**.
1. Under **Chat**, click the **Enable sub-agent** check box.

## Invoking {% data variables.copilot.subagents_short %}

{% data reusables.copilot.using-subagents %}

When the {% data variables.copilot.subagent_short %} completes its task, its results appear back in the main chat session, ready for follow-up questions or next steps.

## Further reading

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/customization-cheat-sheet>)

{% endeclipse %}


</details>
