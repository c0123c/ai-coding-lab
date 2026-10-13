# 定制 GitHub Copilot 应用

汇总全局和仓库指令、Skills、MCP、自定义 Agent、插件及画布扩展的配置入口，供个人与团队调整工作方式。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/docs/commit/df9bd944684a61fc90f8cfda11cf031984998e04>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/github-copilot-app/customize-github-copilot-app.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/定制 GitHub Copilot 应用.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Customizing the GitHub Copilot app
shortTitle: Customize the GitHub Copilot app
intro: 'Customize the {% data variables.copilot.github_copilot_app %} so it works the way you and your team do.'
allowTitleToDifferFromFilename: true
product: '{% data reusables.gated-features.github-app %}<br><a href="https://github.com/features/ai/github-app" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Download {% data variables.copilot.github_copilot_app %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: how-tos
redirect_from:
  - /copilot/how-tos/github-app/customize-github-app
category:
  - Author and optimize with Copilot
  - Configure Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

根据您的工作流程定制 {% data variables.copilot.github_copilot_app %}，以便您的 agents 遵循您的惯例，使用您喜欢的工具，并将正确的专业知识应用于每项任务。

应用程序侧边栏中的 ** 自定义 ** 选项卡允许您在一处发现和管理 MCP 服务器、插件、技能和画布。从此选项卡中，您可以：

* 如果您尚不知道需要哪种自定义，请从探索特色自定义作为起点。
* 按定制类型浏览。
* 通过查看趋势选项或按类别浏览来查找 MCP 服务器。
* 查看 **已安装** 视图中已可供您使用的自定义项。

## 设置全局和存储库指令

您可以添加全局适用或仅适用于特定存储库的说明。

### 设置全局指令

全局说明适用于所有项目的每个会话。

1. 打开应用程序设置。
1. 单击 ** 会话 **。
1. 在“说明”下，编辑“应用程序说明”。

### 设置特定于存储库的指令

特定于存储库的说明适用于所选存储库的每个会话。

1. 打开应用程序设置。
1. 在“项目”下，单击存储库。
1. 编辑“说明”字段。

## 添加agent skills

Agent skills 是 {% data variables.product.prodname_copilot_short %} 可以在相关时加载的指令、脚本和资源的文件夹，以提高其在专门任务中的性能。为您的存储库或 {% data variables.product.prodname_copilot_short %} CLI 配置的任何技能都会在 {% data variables.copilot.github_copilot_app %} 中自动可用。要添加或管理技能，请点击应用侧边栏中的**自定义**，然后点击**技能**。

有关agent skills的更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/about-agent-skills>)。

{% data variables.product.github %}提供的内置技能参考，请参见[AUTOTITLE](<https://github.com/copilot/reference/github-copilot-app-reference/built-in-skills>)。

## 配置MCP服务器

MCP服务器将agent连接到外部工具和数据源。为您的存储库或 {% data variables.product.prodname_copilot_short %} CLI 配置的任何 MCP 服务器将自动在 {% data variables.copilot.github_copilot_app %} 中可用。

要发现并安装 MCP 服务器：

1. 单击应用侧边栏中的**自定义**。
1. 单击**MCP**。
1. 探索特色或趋势服务器、按类别浏览或添加自定义服务器。
1. 选择一个服务器并按照提示进行安装。

要查看或管理已安装的MCP服务器，请单击**已安装**。

有关MCP的更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers>)。


## 使用自定义agents

{% data variables.copilot.custom_agents_caps_short %} 是 {% data variables.copilot.copilot_cloud_agent %} 的专门版本，您可以根据特定任务和工作流程进行定制。

在会话之前或会话期间，使用提示框中的 agent 选择器下拉菜单选择 {% data variables.copilot.copilot_custom_agent_short %}。

或者，在提示框中键入 `/agent` 以选择并调用 {% data variables.copilot.copilot_custom_agent_short %}。

欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/about-custom-agents>)。

## 添加插件

插件是可安装的软件包，可添加一组预配置的功能，例如技能、挂钩、{% data variables.copilot.custom_agents_short %}、MCP 服务器和画布扩展，从而扩展 {% data variables.copilot.github_copilot_app %} 的功能。

**插件**视图显示应用程序中配置的市场中的插件。您可以按市场过滤列表或添加自定义市场。

要浏览并安装插件：

1. 单击应用侧边栏中的**自定义**，然后单击**插件**。
1. （可选）使用市场下拉列表来过滤可用的插件。
1. 找到您要使用的插件，然后点击**安装**。

添加自定义市场：

1. 在 ** 插件 ** 视图中，单击市场下拉列表旁边的 {% octicon "gear" aria-label="The marketplace settings icon" %} 图标。
1. 按照提示添加托管市场的 {% data variables.product.github %} 存储库或 Git URL。

欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/about-plugins>)。

## 使用画布扩展

使用画布扩展为团队或个人工作流程构建共享的、agent 驱动的工件和界面。您可以在**自定义**中的**Canvas**下找到画布，或者在会话中使用`/create-canvas`来创建自己的画布。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/working-with-canvas-extensions>)。

## 组织与企业管理

企业和组织所有者可以设置策略来管理如何跨表面使用 {% data variables.product.prodname_copilot_short %}。 {% data variables.copilot.github_copilot_app %}支持的主要政策参见[AUTOTITLE](<https://github.com/copilot/reference/supported-surfaces-for-policies>)。

企业还可以定义一个`{% data variables.copilot.managed_setting_file %}`文件来控制用户可以在支持的{% data variables.product.prodname_copilot_short %}客户端中执行哪些操作，例如用户可以安装哪些插件以及是否允许“YOLO-style”命令。参见[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/use-managed-settings/get-started>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

Tailor the {% data variables.copilot.github_copilot_app %} to your workflows so your agents follow your conventions, use your preferred tools, and apply the right expertise to every task.

The **Customize** tab in the app sidebar allows you to discover and manage MCP servers, plugins, skills, and canvases in one place. From this tab, you can:

* Explore featured customizations as a starting point if you do not already know which customization you need.
* Browse by customization type.
* Find MCP servers by viewing trending options or browsing by category.
* Review customizations already available for you to use in the **Installed** view.

## Setting global and repository instructions

You can add instructions that apply globally or only to a specific repository.

### Setting global instructions

Global instructions apply to every session across all projects.

1. Open the app settings.
1. Click **Sessions**.
1. Under "Instructions," edit "App instructions."

### Setting repository-specific instructions

Repository-specific instructions apply to every session for the selected repository.

1. Open the app settings.
1. Under "Projects," click the repository.
1. Edit the "Instructions" field.

## Adding agent skills

Agent skills are folders of instructions, scripts, and resources that {% data variables.product.prodname_copilot_short %} can load when relevant to improve its performance in specialized tasks. Any skills configured for your repositories or {% data variables.product.prodname_copilot_short %} CLI are automatically available in the {% data variables.copilot.github_copilot_app %}. To add or manage skills, click **Customize** in the app sidebar, then click **Skills**.

For more information about agent skills, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/about-agent-skills>).

For a {% data variables.product.github %}-provided built-in skills reference, see [AUTOTITLE](<https://github.com/copilot/reference/github-copilot-app-reference/built-in-skills>).

## Configuring MCP servers

MCP servers connect the agent to external tools and data sources. Any MCP servers configured for your repositories or {% data variables.product.prodname_copilot_short %} CLI are automatically available in the {% data variables.copilot.github_copilot_app %}.

To discover and install an MCP server:

1. Click **Customize** in the app sidebar.
1. Click **MCP**.
1. Explore featured or trending servers, browse by category, or add a custom server.
1. Select a server and follow the prompts to install it.

To view or manage MCP servers that are already installed, click **Installed**.

For more information about MCP, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers>).


## Using custom agents

{% data variables.copilot.custom_agents_caps_short %} are specialized versions of {% data variables.copilot.copilot_cloud_agent %} that you can tailor to specific tasks and workflows.

Use the agent picker dropdown in the prompt box to select a {% data variables.copilot.copilot_custom_agent_short %} before or during a session.

Alternatively, type `/agent` in the prompt box to choose and invoke a {% data variables.copilot.copilot_custom_agent_short %}.

For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/about-custom-agents>).

## Adding plugins

Plugins are installable packages that add a preconfigured set of capabilities, such as skills, hooks, {% data variables.copilot.custom_agents_short %}, MCP servers, and canvas extensions, extending the functionality of the {% data variables.copilot.github_copilot_app %}.

The **Plugins** view shows plugins from the marketplaces configured in the app. You can filter the list by marketplace or add a custom marketplace.

To browse and install a plugin:

1. Click **Customize** in the app sidebar, then click **Plugins**.
1. Optionally, use the marketplace dropdown to filter the available plugins.
1. Find the plugin you want to use, then click **Install**.

To add a custom marketplace:

1. In the **Plugins** view, click the {% octicon "gear" aria-label="The marketplace settings icon" %} icon next to the marketplace dropdown.
1. Follow the prompts to add the {% data variables.product.github %} repository or Git URL that hosts the marketplace.

For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/about-plugins>).

## Working with canvas extensions

Use canvas extensions to build shared, agent-driven artifacts and interfaces for team or personal workflows. You can find canvases under **Canvas** in **Customize**, or use `/create-canvas` in a session to create your own. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/working-with-canvas-extensions>).

## Organization and enterprise management

Enterprise and organization owners can set policies to govern how {% data variables.product.prodname_copilot_short %} is used across surfaces. For the major policies supported by the {% data variables.copilot.github_copilot_app %}, see [AUTOTITLE](<https://github.com/copilot/reference/supported-surfaces-for-policies>).

Enterprises can also define a `{% data variables.copilot.managed_setting_file %}` file to control which actions users can take in supported {% data variables.product.prodname_copilot_short %} clients, such as which plugins users can install and whether "YOLO-style" commands are permitted. See [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/use-managed-settings/get-started>).


</details>
