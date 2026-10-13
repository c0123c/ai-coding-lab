# 在 IDE 中创建和使用自定义 Agent

介绍配置角色档案、工具和提示，并从聊天中调用，按不同 IDE 说明配置方式与支持行为。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方文件提交（截至抓取）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 IDE 中创建和使用自定义 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Using custom agents in your IDE
shortTitle: Use custom agents
intro: You can create specialized agents with tailored expertise for specific development tasks, and invoke them from chat in your IDE.
versions:
  feature: copilot
category:
  - Configure Copilot
  - Author and optimize with Copilot
contentType: how-tos
redirect_from:
  - /copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents-in-your-ide
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data variables.copilot.custom_agents_caps_short %} 允许您针对特定任务定制 {% data variables.product.prodname_copilot_short %} 的专业知识。有关 {% data variables.copilot.custom_agents_short %} 的概念概述，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/cloud-agent/about-custom-agents>)。

{% data reusables.copilot.custom-agents-ide-preview %}

{% vscode %}

## 创建 {% data variables.copilot.copilot_custom_agent_short %} 配置文件

1. 打开{% data variables.product.prodname_vscode %}中的{% data variables.copilot.copilot_chat %}。
1. 从聊天视图底部的 agents 下拉列表中，单击 ** 配置自定义 Agents...**，然后单击 **{% octicon "plus" aria-label="Plus button" %} 创建新 {% data variables.copilot.copilot_custom_agent_short %}**。
1. 选择应创建 {% data variables.copilot.agent_profile %} 的位置：
   * **工作空间**：在工作空间的`.github/agents`文件夹中创建{% data variables.copilot.copilot_custom_agent_short %}配置文件，以便仅在该工作空间中使用它。
   * **用户配置文件**：在当前用户配置文件文件夹中创建 {% data variables.copilot.copilot_custom_agent_short %} 配置文件，以便在所有工作区中使用它。
1. 输入 {% data variables.copilot.copilot_custom_agent_short %} 的文件名。这是显示在 agents 下拉列表中的默认名称。
1. 在新建的`.agent.md`文件中配置{% data variables.copilot.agent_profile %}，包括描述、工具和提示。有关 {% data variables.copilot.agent_profile %} 可以包含的内容的更多信息，请参阅 [配置 {% data variables.copilot.agent_profile %}](<#configuring-an-agent-profile>)。
   * 您可以使用编辑器中的**配置工具...**按钮打开“配置工具”对话框，您可以在其中查看和选择可用工具，包括内置工具和MCP服务器中的工具。单击**OK**将所选工具添加到{% data variables.copilot.agent_profile %}。
   * 要设置 agent 使用哪个 AI 模型，请添加 `model:` 属性并从自动完成下拉列表中选择您的首选模型。

要更新一个 {% data variables.copilot.agent_profile %}，选择 **配置自定义 Agents** 从 agents 下拉菜单，然后单击 agent 从列表中进行修改。欲了解更多信息 {% data variables.copilot.custom_agents_short %} 在 {% data variables.product.prodname_vscode_shortname %}，参见 [{% data variables.copilot.custom_agents_caps_short %} 在 {% data variables.product.prodname_vscode_shortname %}](<https://code.visualstudio.com/docs/copilot/customization/custom-agents>).

{% endvscode %}

<!-- --------------------- -->
<!-- JetBrains -->
<!-- --------------------- -->

{% jetbrains %}

## 创建 {% data variables.copilot.copilot_custom_agent_short %} 配置文件

1. 在JetBrainsIDE中打开{% data variables.copilot.copilot_chat %}窗口。
1. 从聊天视图底部的 agents 下拉列表中，单击 ** 配置 Agents...**，然后在设置窗口中的“聊天 Agents”下，单击 ** 工作区 **。
1. 输入 {% data variables.copilot.copilot_custom_agent_short %} 的文件名。这是显示在 agents 下拉列表中的默认名称。
1. 在`.github/agents`目录下新建的`.agent.md`文件中配置{% data variables.copilot.agent_profile %}，包括描述、工具和提示。有关 {% data variables.copilot.agent_profile %} 可以包含的内容的更多信息，请参阅 [配置 {% data variables.copilot.agent_profile %}](<#configuring-an-agent-profile>)。
   * 您可以使用编辑器中的**配置工具...**按钮打开工具对话框，您可以在其中查看和选择可用工具，包括内置工具和来自MCP服务器的工具。单击**应用**将所选工具添加到{% data variables.copilot.agent_profile %}。
   * 要设置 agent 使用哪个 AI 模型，请添加 `model:` 属性并从自动完成下拉列表中选择您的首选模型。

要更新 {% data variables.copilot.agent_profile %}，请从 agents 下拉列表中选择 **配置自定义 Agents**，然后单击要修改的 agent 旁边的 {% octicon "pencil" aria-label="The pencil icon" %}。

{% endjetbrains %}

<!-- --------------------- -->
<!-- Eclipse -->
<!-- --------------------- -->

{% eclipse %}

## 创建 {% data variables.copilot.copilot_custom_agent_short %} 配置文件

1. 在 Eclipse 中打开 {% data variables.copilot.copilot_chat %} 窗口。
1. 从聊天视图底部的 agents 下拉列表中，单击 **配置 Agents...**，然后单击 **添加...**。
1. 输入 {% data variables.copilot.copilot_custom_agent_short %} 的文件名。这是显示在 agents 下拉列表中的默认名称。
1. 在`.github/agents`目录下新建的`.agent.md`文件中配置{% data variables.copilot.agent_profile %}，包括描述、工具和提示。有关 {% data variables.copilot.agent_profile %} 可以包含的内容的更多信息，请参阅 [配置 {% data variables.copilot.agent_profile %}](<#configuring-an-agent-profile>)。
   * 您可以使用编辑器中的**配置工具...**按钮打开“配置工具”对话框，您可以在其中查看和选择可用工具，包括内置工具和MCP服务器中的工具。单击**应用**将所选工具添加到{% data variables.copilot.agent_profile %}。
   * 要设置 agent 使用哪个 AI 模型，请添加 `model:` 属性并从自动完成下拉列表中选择您的首选模型。

要更新 {% data variables.copilot.agent_profile %}，请从 agents 下拉列表中选择 **配置 Agents...**，然后选择要修改的 agent 并单击**编辑**。

{% endeclipse %}

<!-- --------------------- -->
<!-- XCode -->
<!-- --------------------- -->

{% xcode %}

## 创建 {% data variables.copilot.copilot_custom_agent_short %} 配置文件

1. 在 Xcode 中打开 {% data variables.copilot.copilot_chat %} 窗口。
1. 从聊天视图底部的 agents 下拉列表中，单击 **{% octicon "plus" aria-label="Plus button" %} 创建 agent**。
1. 输入 {% data variables.copilot.copilot_custom_agent_short %} 的文件名。这是显示在 agents 下拉列表中的默认名称。
1. 在`.github/agents`目录下新建的`.agent.md`文件中配置{% data variables.copilot.agent_profile %}，包括描述、工具和提示。有关 {% data variables.copilot.agent_profile %} 可以包含的内容的更多信息，请参阅 [配置 {% data variables.copilot.agent_profile %}](<#configuring-an-agent-profile>)。
   * 您可以使用文件编辑器中的**自定义Agent**按钮打开一个对话框，您可以在其中选择agent使用的AI型号，选择可用工具（包括内置工具和MCP服务器工具），并配置`handoffs`属性在 {% data variables.copilot.custom_agents_short %} 之间转换。单击**应用**将所选选项添加到{% data variables.copilot.agent_profile %}。

要更新 {% data variables.copilot.agent_profile %}，请从 agents 下拉列表中单击要修改的 agent 旁边的铅笔图标。

{% endxcode %}

## 配置{% data variables.copilot.agent_profile %}

{% data reusables.copilot.custom-agents-configuring-profile %}

## 示例 {% data variables.copilot.agent_profiles %}

{% data reusables.copilot.custom-agents-example-profiles %}

## 使用{% data variables.copilot.custom_agents_short %}

{% data reusables.copilot.custom-agents-using %}

### 在您的 IDE 中使用 {% data variables.copilot.custom_agents_short %}

您还可以直接在支持的 IDE 中使用自定义的 {% data variables.copilot.agent_profiles %}。您可以使用聊天窗口中的 agent 下拉菜单在 {% data variables.copilot.custom_agents_short %} 之间切换，从而允许您访问针对不同任务（如规划、代码编辑或研究）的专门配置。

某些属性在 {% data variables.product.prodname_dotcom_the_website %} 和 IDE 环境中的功能可能有所不同。有关支持的属性的更多信息，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/custom-agents-configuration#yaml-frontmatter-properties>)。

欲了解更多信息 {% data variables.copilot.custom_agents_short %} 在 {% data variables.product.prodname_vscode_shortname %} 具体请参见 [{% data variables.copilot.custom_agents_caps_short %} 在 {% data variables.product.prodname_vscode_shortname %}](<https://code.visualstudio.com/docs/copilot/customization/custom-agents>) 在 {% data variables.product.prodname_vscode_shortname %} 文档。

## 后续步骤

{% data reusables.copilot.custom-agents-next-steps %}


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data variables.copilot.custom_agents_caps_short %} allow you to tailor {% data variables.product.prodname_copilot_short %}'s expertise for specific tasks. For a conceptual overview of {% data variables.copilot.custom_agents_short %}, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/cloud-agent/about-custom-agents>).

{% data reusables.copilot.custom-agents-ide-preview %}

{% vscode %}

## Creating a {% data variables.copilot.copilot_custom_agent_short %} profile

1. Open {% data variables.copilot.copilot_chat %} in {% data variables.product.prodname_vscode %}.
1. From the agents dropdown at the bottom of the chat view, click **Configure Custom Agents...**, then click **{% octicon "plus" aria-label="Plus button" %} Create new {% data variables.copilot.copilot_custom_agent_short %}**.
1. Choose the location where the {% data variables.copilot.agent_profile %} should be created:
   * **Workspace**: Create the {% data variables.copilot.copilot_custom_agent_short %} profile in the `.github/agents` folder of your workspace to only use it within that workspace.
   * **User profile**: Create the {% data variables.copilot.copilot_custom_agent_short %} profile in the current user profile folder to use it across all your workspaces.
1. Enter a file name for the {% data variables.copilot.copilot_custom_agent_short %}. This is the default name that appears in the agents dropdown.
1. Configure the {% data variables.copilot.agent_profile %} in the newly created `.agent.md` file, including the description, tools, and prompts. For more information on what the {% data variables.copilot.agent_profile %} can include, see [Configuring an {% data variables.copilot.agent_profile %}](<#configuring-an-agent-profile>).
   * You can use the **Configure Tools...** button within the editor to open the "Configure Tools" dialog, where you can view and select available tools, including built-in tools and tools from MCP servers. Click **OK** to add selected tools to the {% data variables.copilot.agent_profile %}.
   * To set which AI model the agent uses, add a `model:` property and select your preferred model from the autocomplete dropdown.

To update an {% data variables.copilot.agent_profile %}, select **Configure Custom Agents** from the agents dropdown, and then click on an agent from the list to modify it. For more information on {% data variables.copilot.custom_agents_short %} in {% data variables.product.prodname_vscode_shortname %}, see [{% data variables.copilot.custom_agents_caps_short %} in {% data variables.product.prodname_vscode_shortname %}](<https://code.visualstudio.com/docs/copilot/customization/custom-agents>).

{% endvscode %}

<!-- --------------------- -->
<!-- JetBrains -->
<!-- --------------------- -->

{% jetbrains %}

## Creating a {% data variables.copilot.copilot_custom_agent_short %} profile

1. Open the {% data variables.copilot.copilot_chat %} window in your JetBrains IDE.
1. From the agents dropdown at the bottom of the chat view, click **Configure Agents...**, then in the settings window, under "Chat Agents", click **Workspace**.
1. Enter a file name for the {% data variables.copilot.copilot_custom_agent_short %}. This is the default name that appears in the agents dropdown.
1. Configure the {% data variables.copilot.agent_profile %} in the newly created `.agent.md` file in the `.github/agents` directory, including the description, tools, and prompts. For more information on what the {% data variables.copilot.agent_profile %} can include, see [Configuring an {% data variables.copilot.agent_profile %}](<#configuring-an-agent-profile>).
   * You can use the **Configure Tools...** button within the editor to open the tools dialog, where you can view and select available tools, including built-in tools and tools from MCP servers. Click **Apply** to add selected tools to the {% data variables.copilot.agent_profile %}.
   * To set which AI model the agent uses, add a `model:` property and select your preferred model from the autocomplete dropdown.

To update an {% data variables.copilot.agent_profile %}, select **Configure Custom Agents** from the agents dropdown, and then click {% octicon "pencil" aria-label="The pencil icon" %} next to the agent you want to modify.

{% endjetbrains %}

<!-- --------------------- -->
<!-- Eclipse -->
<!-- --------------------- -->

{% eclipse %}

## Creating a {% data variables.copilot.copilot_custom_agent_short %} profile

1. Open the {% data variables.copilot.copilot_chat %} window in Eclipse.
1. From the agents dropdown at the bottom of the chat view, click **Configure Agents...**, then click **Add...**.
1. Enter a file name for the {% data variables.copilot.copilot_custom_agent_short %}. This is the default name that appears in the agents dropdown.
1. Configure the {% data variables.copilot.agent_profile %} in the newly created `.agent.md` file in the `.github/agents` directory, including the description, tools, and prompts. For more information on what the {% data variables.copilot.agent_profile %} can include, see [Configuring an {% data variables.copilot.agent_profile %}](<#configuring-an-agent-profile>).
   * You can use the **Configure Tools...** button within the editor to open the "Configure Tools" dialog, where you can view and select available tools, including built-in tools and tools from MCP servers. Click **Apply** to add selected tools to the {% data variables.copilot.agent_profile %}.
   * To set which AI model the agent uses, add a `model:` property and select your preferred model from the autocomplete dropdown.

To update an {% data variables.copilot.agent_profile %}, select **Configure Agents...** from the agents dropdown, and then select the agent you want to modify and click **Edit**.

{% endeclipse %}

<!-- --------------------- -->
<!-- XCode -->
<!-- --------------------- -->

{% xcode %}

## Creating a {% data variables.copilot.copilot_custom_agent_short %} profile

1. Open the {% data variables.copilot.copilot_chat %} window in Xcode.
1. From the agents dropdown at the bottom of the chat view, click **{% octicon "plus" aria-label="Plus button" %} Create an agent**.
1. Enter a file name for the {% data variables.copilot.copilot_custom_agent_short %}. This is the default name that appears in the agents dropdown.
1. Configure the {% data variables.copilot.agent_profile %} in the newly created `.agent.md` file in the `.github/agents` directory, including the description, tools, and prompts. For more information on what the {% data variables.copilot.agent_profile %} can include, see [Configuring an {% data variables.copilot.agent_profile %}](<#configuring-an-agent-profile>).
   * You can use the **Customize Agent** button within the file editor to open a dialog, where you can select the AI model for the agent to use, select available tools (including built-in and MCP server tools), and configure the `handoffs` property for transitioning between {% data variables.copilot.custom_agents_short %}. Click **Apply** to add selected options to the {% data variables.copilot.agent_profile %}.

To update an {% data variables.copilot.agent_profile %}, from the agents dropdown, click the pencil icon next to the agent you want to modify.

{% endxcode %}

## Configuring an {% data variables.copilot.agent_profile %}

{% data reusables.copilot.custom-agents-configuring-profile %}

## Example {% data variables.copilot.agent_profiles %}

{% data reusables.copilot.custom-agents-example-profiles %}

## Using {% data variables.copilot.custom_agents_short %}

{% data reusables.copilot.custom-agents-using %}

### Using {% data variables.copilot.custom_agents_short %} in your IDE

You can also use your custom {% data variables.copilot.agent_profiles %} directly in supported IDEs. You can switch between {% data variables.copilot.custom_agents_short %} using the agent dropdown in the Chat window, allowing you to access specialized configurations for different tasks like planning, code editing, or research.

Some properties may function differently between the {% data variables.product.prodname_dotcom_the_website %} and IDE environments. For more information on supported properties, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/custom-agents-configuration#yaml-frontmatter-properties>).

For more information on {% data variables.copilot.custom_agents_short %} in {% data variables.product.prodname_vscode_shortname %} specifically, see [{% data variables.copilot.custom_agents_caps_short %} in {% data variables.product.prodname_vscode_shortname %}](<https://code.visualstudio.com/docs/copilot/customization/custom-agents>) in the {% data variables.product.prodname_vscode_shortname %} documentation.

## Next steps

{% data reusables.copilot.custom-agents-next-steps %}


</details>
