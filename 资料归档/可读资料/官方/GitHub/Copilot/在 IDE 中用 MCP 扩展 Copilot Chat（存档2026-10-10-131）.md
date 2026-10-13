# 在 IDE 中用 MCP 扩展 Copilot Chat

说明发现、配置和使用 MCP 服务，复用现有配置并提供外部上下文；按不同 IDE 展示支持方式。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方文件提交（截至抓取）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-in-your-ide/customize-copilot/extend-copilot-with-tools-and-context/extend-copilot-chat-with-mcp.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 IDE 中用 MCP 扩展 Copilot Chat（存档2026-10-10-131）.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Extending GitHub Copilot Chat with Model Context Protocol (MCP) servers
allowTitleToDifferFromFilename: true
shortTitle: Extend Copilot Chat with MCP
intro: Connect MCP servers to {% data variables.copilot.copilot_chat_short %} to share context from other applications.
versions:
  feature: copilot
defaultTool: vscode
redirect_from:
  - /copilot/customizing-copilot/extending-copilot-chat-with-mcp
  - /copilot/customizing-copilot/using-model-context-protocol/extending-copilot-chat-with-mcp
  - /copilot/how-tos/context/model-context-protocol/extending-copilot-chat-with-mcp
  - /copilot/how-tos/context/model-context-protocol/extend-copilot-chat-with-mcp
  - /copilot/how-tos/context/use-mcp/extend-copilot-chat-with-mcp
  - /copilot/how-tos/provide-context/use-mcp/extend-copilot-chat-with-mcp
  - /copilot/how-tos/provide-context/use-mcp-in-your-ide/extend-copilot-chat-with-mcp
  - /copilot/how-tos/copilot-in-your-ide/customize-copilot/use-mcp-in-your-ide/extend-copilot-chat-with-mcp
contentType: how-tos
category:
  - Integrate Copilot with your tools
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 简介

Model Context Protocol (MCP) 是一个开放标准，定义应用程序如何与大型语言模型 (LLM) 共享上下文。有关 MCP 的概述，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/context/mcp>)。

有关来自合作伙伴和社区的 MCP 服务器的精选列表，请参阅 [{% data variables.product.github %} MCP 注册表 ](<https://github.com/mcp>)。

{% data reusables.copilot.mcp.mcp-policy %}

{% vscode %}

## 先决条件

* **访问{% data variables.product.prodname_copilot_short %}**。 {% data reusables.copilot.subscription-prerequisite %}
* **{% data variables.product.prodname_vscode %}版本1.99或更高版本**。有关安装{% data variables.product.prodname_vscode %}的信息，请参阅[{% data variables.product.prodname_vscode %}下载页面](<https://code.visualstudio.com/Download?ref_product=copilot&ref_type=engagement&ref_style=text>)。
* {% data reusables.copilot.mcp-policy-requirement %}

## 在{% data variables.product.prodname_vscode %}中配置MCP服务器

MCP 服务器可以在配置文件中手动配置，或通过 {% data variables.product.github %} MCP 注册表进行配置。 {% data variables.product.github %} MCP 注册表提供了 MCP 服务器的精选列表，您可以轻松将其添加到 {% data variables.product.prodname_vscode %} 实例中。

### 使用 {% data variables.product.github %} MCP 注册表

>[！NOTE]
> {% data variables.product.github %} MCP 注册表位于 {% data variables.release-phases.public_preview %} 中，并且可能会发生变化。

只能通过注册表添加{% data variables.product.github %}MCP注册表中列出的MCP服务器。其他服务器可以手动配置。请参阅[手动配置MCP服务器](<#手动配置mcp服务器>)。

1. 在{% data variables.product.prodname_vscode %}中，单击侧边栏中的扩展程序图标或按<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>X</kbd>打开扩展程序面板(Windows/Linux) / <kbd>命令</kbd>+<kbd>Shift</kbd>+<kbd>X</kbd> (Mac)。
1. 在扩展搜索栏中，键入 `@mcp`，后跟要添加的 MCP 服务器的名称。这将打开 MCP 服务器库并显示匹配的结果。
1. 从搜索结果中选择MCP服务器。在MCP服务器的详细信息页面上，单击**安装**。
1. 出现提示时，确认您信任服务器来启动它。 {% data variables.product.prodname_vscode_shortname %} 发现服务器的工具并使其在聊天中可用。
1. 要验证 MCP 服务器是否配置正确，请按 <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> 打开命令选项板(Windows/Linux) / <kbd>命令</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> (Mac)。
1. 键入并选择 **MCP：列出服务器 **。您应该看到 MCP 服务器列为已配置的服务器。

### 手动配置MCP服务器

要在 {% data variables.product.prodname_vscode %} 中配置 MCP 服务器，您需要设置一个配置脚本来指定要使用的 MCP 服务器的详细信息。您可以为以下任一配置 MCP 服务器：

{% data reusables.copilot.mcp.mcp-configuration-location %}

	>[!NOTE] 我们建议您每台服务器仅使用一个位置。将同一服务器添加到两个位置可能会导致冲突和意外行为。

以下步骤显示如何在 `.vscode/mcp.json` 文件中配置获取 MCP 服务器。 Fetch MCP 服务器是一个简单的 MCP 服务器，提供 Web 内容获取功能。有关 Fetch MCP 服务器的更多信息，请参阅 MCP 服务器存储库中的 [Fetch 目录 ](<https://github.com/modelcontextprotocol/servers/tree/main/src/fetch>)。

您可以使用相同的步骤在您的个人{% data variables.product.prodname_vscode %}设置中配置MCP服务器。有关如何配置其他 MCP 服务器的详细信息，请参阅 [MCP 服务器存储库 ](<https://github.com/modelcontextprotocol/servers/tree/main>)。

有关配置 {% data variables.product.github %} MCP 服务器的信息，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>)。

1. 将以下配置添加到您的 `.vscode/mcp.json` 文件中：

    ```json copy
    {
    "inputs": [
      // The "inputs" section defines the inputs required for the MCP server configuration.
      {
        "type": "promptString"
      }
    ],
    "servers": {
      // The "servers" section defines the MCP servers you want to use.
      "fetch": {
        "command": "uvx",
        "args": ["mcp-server-fetch"]
      }
     }
    }
   ```

1. 保存 `.vscode/mcp.json` 文件。
1. “开始”按钮将出现在您的 `.vscode/mcp.json` 文件中，位于服务器列表的顶部。单击“启动”按钮启动MCP服务器。这将触发输入对话框并发现服务器工具，然后将其存储以供以后的会话使用。

![{% data variables.product.prodname_vscode %} 中的 MCP 服务器配置的屏幕截图。 “开始”按钮以深橙色轮廓显示。 ](<https://raw.githubusercontent.com/assets/images/help/copilot/mcp-start-server-button.png>)

1. 单击{% data variables.product.prodname_vscode %}标题栏中的聊天图标打开{% data variables.copilot.copilot_chat_short %}。
1. 在{% data variables.copilot.copilot_chat_short %}框中，从弹出菜单中选择**Agent**。

![{% data variables.product.prodname_vscode %}中的{% data variables.copilot.copilot_chat_short %}框的屏幕截图。 “Agent”选项以深橙色轮廓显示。](<https://raw.githubusercontent.com/assets/images/help/copilot/copilot-chat-agent-option.png>)

1. 要查看可用的 MCP 服务器列表，请单击聊天框左上角的工具图标。这将打开 MCP 服务器列表，您可以在其中查看 {% data variables.product.prodname_vscode %} 实例中当前可用的所有 MCP 服务器和相关工具。
     * 或者，您可以定义工具集，即可以在聊天中引用的相关工具组。工具集可以更轻松地将相关的 MCP 工具组合在一起并快速启用或禁用它们。有关如何定义和使用工具集的信息，请参阅 [{% data variables.product.prodname_vscode_shortname %} 文档 ](<https://code.visualstudio.com/docs/copilot/agents/agent-tools#_group-tools-with-tool-sets>)。

有关在 {% data variables.product.prodname_vscode %} 中配置 MCP 服务器的更多信息，请参阅 {% data variables.product.prodname_vscode %} 文档中的 [在 {% data variables.product.prodname_vscode %}](<https://aka.ms/vscode-add-mcp>) 中使用 MCP 服务器。

## 在 {% data variables.copilot.copilot_chat_short %} 中使用 MCP 服务器

配置完 MCP 服务器后，您可以在 {% data variables.copilot.copilot_chat_short %} 中使用它们来访问各种工具和服务。在下面的示例中，我们将使用 Fetch MCP 服务器来获取有关网页的详细信息。

{% data reusables.copilot.open-chat-vs-code %}
{% data reusables.copilot.select-agent %}
1. 在具有MCP配置的文件中，检查MCP服务器是否正在运行。如果没有运行，请单击“启动”按钮启动MCP服务器。

![{% data variables.product.prodname_vscode %} 中的 MCP 服务器配置的屏幕截图。 “正在运行”状态以深橙色显示。](<https://raw.githubusercontent.com/assets/images/help/copilot/vsc-mcp-server-running.png>)

1. 要求 {% data variables.copilot.copilot_chat_short %} 获取 URL 的详细信息。例如：

    `Fetch https://github.com/github/docs.`

1. 如果 {% data variables.product.prodname_copilot_short %} 要求您确认是否要继续，请单击 **Continue**。

1. {% data variables.product.prodname_copilot_short %}将获取URL的详细信息并将其显示在聊天框中。

或者，您可以使用 {% data variables.product.prodname_vscode_shortname %} 中的 MCP 提示和资源。

  * MCP 服务器可以定义与其工具交互的预配置提示。您可以使用格式 `/mcp.servername.promptname` 在聊天中使用斜杠命令访问这些提示。
  * MCP 服务器提供资源，这些资源是服务器想要提供的任何类型的数据。例如，{% data variables.product.github %} MCP 服务器提供存储库内容作为资源。要将资源从 MCP 服务器添加到您的聊天上下文，请单击聊天框中的 **添加上下文...**，然后单击 **MCP 资源 **。

有关在 {% data variables.product.prodname_vscode %} 中使用 MCP 服务器的更多信息，请参阅 {% data variables.product.prodname_vscode %} 文档中的 [在 {% data variables.product.prodname_vscode %}](<https://aka.ms/vscode-add-mcp>) 中使用 MCP 服务器。

## 使用现有的 MCP 配置

如果您在 Claude 桌面中已有 MCP 配置，则可以在 {% data variables.product.prodname_vscode %} 中使用该配置来访问相同的 MCP 服务器。为此，请将以下配置添加到 {% data variables.product.prodname_vscode %} 中的 `settings.json` 文件中：

```json copy
"chat.mcp.discovery.enabled": true
```

{% data variables.product.prodname_vscode %}将自动查找您现有的配置并在您的{% data variables.product.prodname_vscode %}实例中使用它。

{% endvscode %}

{% visualstudio %}

## 先决条件

* **访问{% data variables.product.prodname_copilot_short %}**。 {% data reusables.copilot.subscription-prerequisite %}
* **{% data variables.product.prodname_vs %}版本17.14或更高版本**。有关安装 {% data variables.product.prodname_vs %} 的更多信息，请参阅 [{% data variables.product.prodname_vs %} 下载页面 ](<https://visualstudio.microsoft.com/downloads/>)。
* **从{% data variables.product.prodname_vs %}**登录{% data variables.product.company_short %}。
* {% data reusables.copilot.mcp-policy-requirement %}

## 在{% data variables.product.prodname_vs %}中配置MCP服务器

1. 在{% data variables.product.prodname_vs %}菜单栏中，单击**查看**，然后单击**{% data variables.copilot.copilot_chat %}**。
1. 在聊天面板底部，从模式下拉列表中选择 **Agent**。
1. 在 {% data variables.copilot.copilot_chat_short %} 窗口中，单击工具图标，然后单击工具选择器窗口中的加号图标。
1. 在“配置 MCP 服务器”弹出窗口中，填写字段，包括服务器 ID、类型以及特定 MCP 服务器配置所需的任何其他字段。

{% data variables.product.prodname_vs %}支持远程和本地服务器。使用 URL 和凭据定义的远程服务器在外部托管，以便于设置和共享，而使用命令行调用定义的本地服务器在本地计算机上运行并可以访问本地资源。请参阅下面的示例配置，以 {% data variables.product.github %} MCP 服务器为例。

1. 单击**保存**。
1. 如果您使用的是具有 OAuth 身份验证的远程服务器，请在 `mcp.json` 文件中，单击服务器上方 CodeLens 中的 **Auth**，以向服务器进行身份验证。将出现一个弹出窗口或新窗口，允许您使用您的帐户进行身份验证。服务器将只能访问您批准的范围以及您的组织策略允许的范围。
1. 在{% data variables.copilot.copilot_chat_short %}窗口中，单击工具图标。您现在应该可以从您配置的 MCP 服务器中看到其他工具。

### 使用 OAuth 的远程服务器配置示例

  1. 对于“服务器 ID”，输入 `github`。
  1. 对于“类型”，从下拉列表中选择“HTTP/SSE”。
  1. 对于“URL”，输入 `https://api.githubcopilot.com/mcp/`。
  1. 单击**保存**后，`mcp.json`文件中的配置应如下所示：

      ```json copy
          {
            "servers": {
              "github": {
                "url": "https://api.githubcopilot.com/mcp/"
              }
            }
          }
      ```

  1. 在`mcp.json`文件中，单击服务器上方CodeLens中的**Auth**，向服务器进行身份验证。将出现一个弹出窗口，允许您使用您的 {% data variables.product.github %} 帐户进行身份验证。

### 本地服务器配置示例

  1. 对于“服务器 ID”，输入 `github`。
  1. 对于“类型”，从下拉列表中选择“stdio”。
  1. 对于“命令（带有可选参数）”，输入 `docker "run", "-i", "--rm", "-e", "GITHUB_PERSONAL_ACCESS_TOKEN", "ghcr.io/github/github-mcp-server"`
  1. 添加环境变量“GITHUB_PERSONAL_ACCESS_TOKEN”设置为您的{% data variables.product.pat_generic %}。
  1. 单击**保存**后，`mcp.json`文件中的配置应如下所示：

        ```json copy
            {
              "servers": {
                "github": {
                  "type": "stdio",
                  "command": "docker",
                  "args": [
                    "run", "-i", "--rm", "-e", "GITHUB_PERSONAL_ACCESS_TOKEN",
                    "ghcr.io/github/github-mcp-server"
                  ],
                  "env": {
                    "GITHUB_PERSONAL_ACCESS_TOKEN": "YOUR_GITHUB_PAT"
                  }
                }
              }
            }
        ```

有关在 {% data variables.product.prodname_vs %} 中配置 MCP 服务器的更多信息，请参阅 {% data variables.product.prodname_vs %} 文档中的 [在 {% data variables.product.prodname_vs %}（预览版）](<https://learn.microsoft.com/en-us/visualstudio/ide/mcp-servers?view=vs-2022>) 中使用 MCP 服务器。

{% endvisualstudio %}

{% jetbrains %}

## 先决条件

* **访问{% data variables.product.prodname_copilot_short %}**。 {% data reusables.copilot.subscription-prerequisite %}
* **兼容JetBrains IDE**。 {% data variables.product.prodname_copilot %} 与以下 IDE 兼容：

  {% data reusables.copilot.jetbrains-compatible-ides %}
* {% data reusables.copilot.mcp-policy-requirement %}

## 从 MCP 注册表配置 MCP 服务器

1. 在您的JetBrainsIDE中，打开{% data variables.copilot.copilot_chat_short %}。
1. 在{% data variables.copilot.copilot_chat_short %}窗口中，单击{% octicon "mcp" aria-hidden="true" aria-label="mcp" %}MCP图标。
1. 在 MCP 注册表窗口中，从可用服务器列表中找到要添加的 MCP 服务器。
1. 在要添加的每个 MCP 服务器旁边，单击 ** 安装 **。
1. 添加完MCP服务器后，单击**OK**。
1. 在{% data variables.copilot.copilot_chat_short %}窗口中，单击工具图标。您现在应该可以从您安装的 MCP 服务器中看到其他工具。

## 手动配置MCP服务器

{% data reusables.copilot.jetbrains-mcp-setup-steps %}
1. 在 `mcp.json` 文件中，定义您的 MCP 服务器。 JetBrains IDE 支持远程和本地服务器。远程服务器在外部托管，以便于设置和共享，而本地服务器在本地计算机上运行并可以访问本地资源。

您可以使用以下配置作为示例：

### 使用 PAT 的远程服务器配置示例

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-remote %}

### 本地服务器配置示例

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-local %}

{% endjetbrains %}

{% xcode %}

## 先决条件

* **访问{% data variables.product.prodname_copilot_short %}**。 {% data reusables.copilot.subscription-prerequisite %}
* **{% data variables.product.prodname_copilot %} 用于 Xcode 扩展 **。请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension>)。
* {% data reusables.copilot.mcp-policy-requirement %}

## 从 MCP 注册表配置 MCP 服务器

1. 在 Xcode 中，打开 {% data variables.copilot.copilot_chat_short %}。
1. 在【{% data variables.copilot.copilot_chat_short %}】窗口中，点击【{% octicon "gear" aria-label="The Settings gear" %}】图标打开设置。
1. 在设置窗口中，选择**工具**选项卡。
1. 在 **MCP 注册表 URL（可选）** 旁边，单击 ** 浏览 MCP 服务器 **。
1. 在 MCP 注册表窗口中，从可用服务器列表中找到要添加的 MCP 服务器。
1. 在要添加的每个 MCP 服务器旁边，单击 ** 安装 **。
1. 添加完 MCP 服务器后，关闭 **MCP 服务器市场 ** 窗口。
1. 在设置窗口中，在**可用的MCP工具**下，单击**>**图标以展开可用的MCP工具列表。您现在应该可以从您安装的 MCP 服务器中看到其他工具。

## 手动配置MCP服务器

{% data reusables.copilot.xcode-mcp-setup-steps %}
1. 定义您的 MCP 服务器，编辑 `mcp.json`。 Xcode 支持远程和本地服务器。远程服务器在外部托管，以便于设置和共享，而本地服务器在本地计算机上运行并可以访问本地资源。

您可以使用以下配置作为示例：

### 使用 PAT 的远程服务器配置示例

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-remote %}

### 本地服务器配置示例

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-local %}

{% endxcode %}

{% eclipse %}

## 先决条件

{% data reusables.copilot.eclipse-prerequisites %}

## 从 MCP 注册表配置 MCP 服务器

1. 在 Eclipse 中，打开 {% data variables.copilot.copilot_chat_short %}。
1. 在{% data variables.copilot.copilot_chat_short %}窗口中，单击{% octicon "mcp" aria-hidden="true" aria-label="mcp" %}MCP图标。
1. 在 MCP 注册表窗口中，从可用服务器列表中找到要添加的 MCP 服务器。
1. 在要添加的每个 MCP 服务器旁边，单击 ** 安装 **。
1. 添加完MCP服务器后，单击**关闭**。
1. 在{% data variables.copilot.copilot_chat_short %}窗口中，单击工具图标。您现在应该可以从您安装的 MCP 服务器中看到其他工具。

## 手动配置MCP服务器

{% data reusables.copilot.eclipse-mcp-setup-steps %}
1. 在“服务器配置”下，定义您的 MCP 服务器。 Eclipse 支持远程和本地服务器。远程服务器在外部托管，以便于设置和共享，而本地服务器在本地计算机上运行并可以访问本地资源。

您可以使用以下配置作为示例：

### 使用 PAT 的远程服务器配置示例

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-remote %}

### 本地服务器配置示例

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-local %}

{% endeclipse %}

## 创建新的 MCP 服务器

您可以创建一个新的MCP服务器来满足您的特定需求，然后将其与{% data variables.copilot.copilot_chat_short %}集成。例如，您可以创建连接到数据库或 Web 服务的 MCP 服务器，然后在 {% data variables.copilot.copilot_chat_short %} 中使用该服务器对该数据库或 Web 服务执行任务。

有关创建和配置您自己的 MCP 服务器的更多信息，请参阅 [ 官方 MCP 文档 ](<https://modelcontextprotocol.io/quickstart/server>)。

## 进一步阅读

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/tutorials/enhance-agent-mode-with-mcp>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## Introduction

The Model Context Protocol (MCP) is an open standard that defines how applications share context with large language models (LLMs). For an overview of MCP, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/context/mcp>).

For a curated list of MCP servers from partners and the community, see the [{% data variables.product.github %} MCP Registry](<https://github.com/mcp>).

{% data reusables.copilot.mcp.mcp-policy %}

{% vscode %}

## Prerequisites

* **Access to {% data variables.product.prodname_copilot_short %}**. {% data reusables.copilot.subscription-prerequisite %}
* **{% data variables.product.prodname_vscode %} version 1.99 or later**. For information on installing {% data variables.product.prodname_vscode %}, see the [{% data variables.product.prodname_vscode %} download page](<https://code.visualstudio.com/Download?ref_product=copilot&ref_type=engagement&ref_style=text>).
* {% data reusables.copilot.mcp-policy-requirement %}

## Configuring MCP servers in {% data variables.product.prodname_vscode %}

MCP servers can be configured manually in a configuration file, or through the {% data variables.product.github %} MCP Registry. The {% data variables.product.github %} MCP Registry provides a curated list of MCP servers that you can easily add to your {% data variables.product.prodname_vscode %} instance.

### Using the {% data variables.product.github %} MCP Registry

>[!NOTE]
> The {% data variables.product.github %} MCP Registry is in {% data variables.release-phases.public_preview %} and may change.

Only MCP servers listed in the {% data variables.product.github %} MCP Registry can be added through the registry. Other servers can be configured manually. See [Configuring MCP servers manually](<#configuring-mcp-servers-manually>).

1. In {% data variables.product.prodname_vscode %}, open the extensions panel by clicking the extensions icon in the sidebar or pressing <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>X</kbd> (Windows/Linux) / <kbd>Command</kbd>+<kbd>Shift</kbd>+<kbd>X</kbd> (Mac).
1. In the extensions search bar, type `@mcp` followed by the name of the MCP server you want to add. This opens the MCP server gallery and shows matching results.
1. Select the MCP server from the search results. On the MCP server's details page, click **Install**.
1. When prompted, confirm that you trust the server to start it. {% data variables.product.prodname_vscode_shortname %} discovers the server's tools and makes them available in chat.
1. To verify that the MCP server is configured correctly, open the command palette by pressing <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> (Windows/Linux) / <kbd>Command</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> (Mac).
1. Type and select **MCP: List Servers**. You should see the MCP server listed as a configured server.

### Configuring MCP servers manually

To configure MCP servers in {% data variables.product.prodname_vscode %}, you need to set up a configuration script that specifies the details of the MCP servers you want to use. You can configure MCP servers for either:

{% data reusables.copilot.mcp.mcp-configuration-location %}

	>[!NOTE] We recommend you use only one location per server. Adding the same server to both locations may cause conflicts and unexpected behavior.

The steps below show how to configure the Fetch MCP server in your `.vscode/mcp.json` file. The Fetch MCP server is a simple MCP server that provides web content fetching capabilities. For more information on the Fetch MCP server, see [the Fetch directory](<https://github.com/modelcontextprotocol/servers/tree/main/src/fetch>) in the MCP Server repository.

You can use the same steps to configure MCP servers in your personal {% data variables.product.prodname_vscode %} settings. Details on how to configure other MCP servers are available in the [MCP servers repository](<https://github.com/modelcontextprotocol/servers/tree/main>).

For information on configuring the {% data variables.product.github %} MCP server, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>).

1. Add the following configuration to your `.vscode/mcp.json` file:

    ```json copy
    {
    "inputs": [
      // The "inputs" section defines the inputs required for the MCP server configuration.
      {
        "type": "promptString"
      }
    ],
    "servers": {
      // The "servers" section defines the MCP servers you want to use.
      "fetch": {
        "command": "uvx",
        "args": ["mcp-server-fetch"]
      }
     }
    }
   ```

1. Save the `.vscode/mcp.json` file.
1. A "Start" button will appear in your `.vscode/mcp.json` file, at the top of the list of servers. Click the "Start" button to start the MCP servers. This will trigger the input dialog and discover the server tools, which are then stored for later sessions.

    ![Screenshot of MCP server configuration in {% data variables.product.prodname_vscode %}. The "Start" button is outlined in dark orange. ](<https://raw.githubusercontent.com/assets/images/help/copilot/mcp-start-server-button.png>)

1. Open {% data variables.copilot.copilot_chat_short %} by clicking the chat icon in the title bar of {% data variables.product.prodname_vscode %}.
1. In the {% data variables.copilot.copilot_chat_short %} box, select **Agent** from the popup menu.

    ![Screenshot of the {% data variables.copilot.copilot_chat_short %} box in {% data variables.product.prodname_vscode %}. The "Agent" option is outlined in dark orange.](<https://raw.githubusercontent.com/assets/images/help/copilot/copilot-chat-agent-option.png>)

1. To view your list of available MCP servers, click the tools icon in the top left corner of the chat box. This will open the MCP server list, where you can see all the MCP servers and associated tools that are currently available in your {% data variables.product.prodname_vscode %} instance.
     * Optionally, you can define toolsets, groups of related tools that you can reference in chat. Toolsets make it easier to group related MCP tools together and quickly enable or disable them. For information on how to define and use a toolset, see the [{% data variables.product.prodname_vscode_shortname %} docs](<https://code.visualstudio.com/docs/copilot/agents/agent-tools#_group-tools-with-tool-sets>).

For more information on configuring MCP servers in {% data variables.product.prodname_vscode %}, see [Use MCP servers in {% data variables.product.prodname_vscode %}](<https://aka.ms/vscode-add-mcp>) in the {% data variables.product.prodname_vscode %} documentation.

## Using MCP servers in {% data variables.copilot.copilot_chat_short %}

Once you have configured your MCP servers, you can use them in {% data variables.copilot.copilot_chat_short %} to access a wide range of tools and services. In the example below, we will use the Fetch MCP server to fetch details about a web page.

{% data reusables.copilot.open-chat-vs-code %}
{% data reusables.copilot.select-agent %}
1. In the file with the MCP configuration, check that the MCP server is running. If it is not running, click the "Start" button to start the MCP server.

    ![Screenshot of the MCP server configuration in {% data variables.product.prodname_vscode %}. The "Running" status is outlined in dark orange.](<https://raw.githubusercontent.com/assets/images/help/copilot/vsc-mcp-server-running.png>)

1. Ask {% data variables.copilot.copilot_chat_short %} to fetch the details of a URL. For example:

    `Fetch https://github.com/github/docs.`

1. If {% data variables.product.prodname_copilot_short %} asks you to confirm that you want to proceed, click **Continue**.

1. {% data variables.product.prodname_copilot_short %} will fetch the details of the URL and display them in the chat box.

Optionally, you can use MCP prompts and resources in {% data variables.product.prodname_vscode_shortname %}.

  * MCP servers can define preconfigured prompts for interacting with their tools. You can access these prompts in chat with slash commands, using the format `/mcp.servername.promptname`.
  * MCP servers provide resources, which are any kind of data that the server wants to make available. For example, the {% data variables.product.github %} MCP server provides repository content as a resource. To add resources from an MCP server to your chat context, click **Add Context...** in the chat box, then click **MCP Resources**.

For more information on using MCP servers in {% data variables.product.prodname_vscode %}, see [Use MCP servers in {% data variables.product.prodname_vscode %}](<https://aka.ms/vscode-add-mcp>) in the {% data variables.product.prodname_vscode %} documentation.

## Using existing MCP configurations

If you already have an MCP configuration in Claude Desktop, you can use that configuration in {% data variables.product.prodname_vscode %} to access the same MCP servers. To do this, add the following configuration to your `settings.json` file in {% data variables.product.prodname_vscode %}:

```json copy
"chat.mcp.discovery.enabled": true
```

{% data variables.product.prodname_vscode %} will automatically find your existing configuration and use it in your {% data variables.product.prodname_vscode %} instance.

{% endvscode %}

{% visualstudio %}

## Prerequisites

* **Access to {% data variables.product.prodname_copilot_short %}**. {% data reusables.copilot.subscription-prerequisite %}
* **{% data variables.product.prodname_vs %} version 17.14 or later**. For more information on installing {% data variables.product.prodname_vs %}, see the [{% data variables.product.prodname_vs %} downloads page](<https://visualstudio.microsoft.com/downloads/>).
* **Sign in to {% data variables.product.company_short %} from {% data variables.product.prodname_vs %}**.
* {% data reusables.copilot.mcp-policy-requirement %}

## Configuring MCP servers in {% data variables.product.prodname_vs %}

1. In the {% data variables.product.prodname_vs %} menu bar, click **View**, then click **{% data variables.copilot.copilot_chat %}**.
1. At the bottom of the chat panel, select **Agent** from the mode dropdown.
1. In the {% data variables.copilot.copilot_chat_short %} window, click the tools icon, then click the plus icon in the tool picker window.
1. In the "Configure MCP server" pop-up window, fill out the fields, including server ID, type, and any additional fields required for the specific MCP server configuration.

   {% data variables.product.prodname_vs %} supports both remote and local servers. Remote servers, defined with a URL and credentials, are hosted externally for easier setup and sharing, while local servers, defined with command-line invocation, run on your local machine and can access local resources. See example configurations below, using the {% data variables.product.github %} MCP server as an example.

1. Click **Save**.
1. If you are using a remote server with OAuth authentication, in the `mcp.json` file, click **Auth** from the CodeLens above the server to authenticate to the server. A pop-up or new window will appear, allowing you to authenticate with your account. The server will only be able to access the scopes you approve, and that your organization policies allow.
1. In the {% data variables.copilot.copilot_chat_short %} window, click the tools icon. You should now see additional tools from the MCP server that you configured.

### Remote server configuration example with OAuth

  1. For "Server ID", type `github`.
  1. For "Type", select "HTTP/SSE" from the dropdown.
  1. For "URL", type `https://api.githubcopilot.com/mcp/`.
  1. After clicking **Save**, the configuration in the `mcp.json` file should look like this:

      ```json copy
          {
            "servers": {
              "github": {
                "url": "https://api.githubcopilot.com/mcp/"
              }
            }
          }
      ```

  1. In the `mcp.json` file, click **Auth** from the CodeLens above the server to authenticate to the server. A pop-up will come up allowing you to authenticate with your {% data variables.product.github %} account.

### Local server configuration example

  1. For "Server ID", type `github`.
  1. For "Type", select "stdio" from the dropdown.
  1. For "Command (with optional arguments)", type `docker "run", "-i", "--rm", "-e", "GITHUB_PERSONAL_ACCESS_TOKEN", "ghcr.io/github/github-mcp-server"`
  1. Add an environment variable "GITHUB_PERSONAL_ACCESS_TOKEN" set to your {% data variables.product.pat_generic %}.
  1. After clicking **Save**, the configuration in the `mcp.json` file should look like this:

        ```json copy
            {
              "servers": {
                "github": {
                  "type": "stdio",
                  "command": "docker",
                  "args": [
                    "run", "-i", "--rm", "-e", "GITHUB_PERSONAL_ACCESS_TOKEN",
                    "ghcr.io/github/github-mcp-server"
                  ],
                  "env": {
                    "GITHUB_PERSONAL_ACCESS_TOKEN": "YOUR_GITHUB_PAT"
                  }
                }
              }
            }
        ```

For more information on configuring MCP servers in {% data variables.product.prodname_vs %}, see [Use MCP servers in {% data variables.product.prodname_vs %} (Preview)](<https://learn.microsoft.com/en-us/visualstudio/ide/mcp-servers?view=vs-2022>) in the {% data variables.product.prodname_vs %} documentation.

{% endvisualstudio %}

{% jetbrains %}

## Prerequisites

* **Access to {% data variables.product.prodname_copilot_short %}**. {% data reusables.copilot.subscription-prerequisite %}
* **A compatible JetBrains IDE**. {% data variables.product.prodname_copilot %} is compatible with the following IDEs:

  {% data reusables.copilot.jetbrains-compatible-ides %}
* {% data reusables.copilot.mcp-policy-requirement %}

## Configuring MCP servers from your MCP registry

1. In your JetBrains IDE, open {% data variables.copilot.copilot_chat_short %}.
1. In the {% data variables.copilot.copilot_chat_short %} window, click the {% octicon "mcp" aria-hidden="true" aria-label="mcp" %} MCP icon.
1. In the MCP Registry window, find the MCP server(s) you want to add from the list of available servers.
1. Next to each MCP server you want to add, click **Install**.
1. When you are finished adding MCP servers, click **OK**.
1. In the {% data variables.copilot.copilot_chat_short %} window, click the tools icon. You should now see additional tools from the MCP server(s) that you installed.

## Configuring MCP servers manually

{% data reusables.copilot.jetbrains-mcp-setup-steps %}
1. In the `mcp.json` file, define your MCP servers. JetBrains IDEs support both remote and local servers. Remote servers are hosted externally for easier setup and sharing, while local servers run on your local machine and can access local resources.

You can use the following configurations as examples:

### Remote server configuration example with PAT

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-remote %}

### Local server configuration example

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-local %}

{% endjetbrains %}

{% xcode %}

## Prerequisites

* **Access to {% data variables.product.prodname_copilot_short %}**. {% data reusables.copilot.subscription-prerequisite %}
* **{% data variables.product.prodname_copilot %} for Xcode extension**. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension>).
* {% data reusables.copilot.mcp-policy-requirement %}

## Configuring MCP servers from your MCP registry

1. In Xcode, open {% data variables.copilot.copilot_chat_short %}.
1. In the {% data variables.copilot.copilot_chat_short %} window, click the {% octicon "gear" aria-label="The Settings gear" %} icon to open settings.
1. In the settings window, select the **Tools** tab.
1. Next to **MCP Registry URL (Optional)**, click **Browse MCP Servers**.
1. In the MCP Registry window, find the MCP server(s) you want to add from the list of available servers.
1. Next to each MCP server you want to add, click **Install**.
1. When you are finished adding MCP servers, close the **MCP Servers Marketplace** window.
1. In the settings window, under **Available MCP Tools**, click the **>** icon to expand the list of available MCP tools. You should now see additional tools from the MCP server(s) that you installed.

## Configuring MCP servers manually

{% data reusables.copilot.xcode-mcp-setup-steps %}
1. Define your MCP servers, editing `mcp.json`. Xcode supports both remote and local servers. Remote servers are hosted externally for easier setup and sharing, while local servers run on your local machine and can access local resources.

You can use the following configurations as examples:

### Remote server configuration example with PAT

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-remote %}

### Local server configuration example

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-local %}

{% endxcode %}

{% eclipse %}

## Prerequisites

{% data reusables.copilot.eclipse-prerequisites %}

## Configuring MCP servers from your MCP registry

1. In Eclipse, open {% data variables.copilot.copilot_chat_short %}.
1. In the {% data variables.copilot.copilot_chat_short %} window, click the {% octicon "mcp" aria-hidden="true" aria-label="mcp" %} MCP icon.
1. In the MCP Registry window, find the MCP server(s) you want to add from the list of available servers.
1. Next to each MCP server you want to add, click **Install**.
1. When you are finished adding MCP servers, click **Close**.
1. In the {% data variables.copilot.copilot_chat_short %} window, click the tools icon. You should now see additional tools from the MCP server(s) that you installed.

## Configuring MCP servers manually

{% data reusables.copilot.eclipse-mcp-setup-steps %}
1. Under "Server Configurations", define your MCP servers. Eclipse supports both remote and local servers. Remote servers are hosted externally for easier setup and sharing, while local servers run on your local machine and can access local resources.

You can use the following configurations as examples:

### Remote server configuration example with PAT

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-remote %}

### Local server configuration example

  {% data reusables.copilot.mcp-chat-json-snippet-for-other-ides-local %}

{% endeclipse %}

## Creating a new MCP server

You can create a new MCP server to fulfill your specific needs, and then integrate it with {% data variables.copilot.copilot_chat_short %}. For example, you can create an MCP server that connects to a database or a web service, and then use that server in {% data variables.copilot.copilot_chat_short %} to perform tasks on that database or web service.

For more information on creating and configuring your own MCP servers, see [the official MCP documentation](<https://modelcontextprotocol.io/quickstart/server>).

## Further reading

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/tutorials/enhance-agent-mode-with-mcp>)


</details>
