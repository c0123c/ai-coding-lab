# 在开发环境中安装 Copilot

按支持的 IDE 介绍安装、版本兼容、登录及权限设置，帮助完成插件接入；不同编辑器的步骤分开说明。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在开发环境中安装 Copilot.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Installing the GitHub Copilot extension in your environment
shortTitle: Install Copilot extension
intro: To use {% data variables.product.prodname_copilot_short %} in your preferred coding environment, follow the steps for your chosen IDE.
versions:
  feature: copilot
defaultTool: vscode
redirect_from:
  - /copilot/configuring-github-copilot/installing-the-github-copilot-extension-in-your-environment
  - /copilot/managing-copilot/configure-personal-settings/installing-the-github-copilot-extension-in-your-environment
  - /copilot/how-tos/personal-settings/installing-the-github-copilot-extension-in-your-environment
  - /copilot/how-tos/set-up/installing-the-github-copilot-extension-in-your-environment
  - /copilot/how-tos/set-up/install-copilot-extension
contentType: how-tos
category:
  - Configure Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 先决条件

要在 IDE 中使用 {% data variables.product.prodname_copilot_short %}，您需要通过 {% data variables.copilot.copilot_free_short %} 进行有限访问或付费 {% data variables.product.prodname_copilot_short %} 计划才能获得完全访问权限。有关如何访问并选择正确计划的更多信息，请参阅 [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>)。

{% azure_data_studio %}

## 关于 Azure Data Studio 中的 {% data variables.product.prodname_copilot %} 扩展

通过在 Azure Data Studio 中安装 {% data variables.product.prodname_copilot %} 扩展，您可以在键入时接收来自 {% data variables.product.prodname_copilot_short %} 的编码建议。

要查看其他流行编码环境的说明，请使用页面顶部的工具切换器。

## 在 Azure Data Studio 中安装 {% data variables.product.prodname_copilot %} 扩展

1. 确保您有权访问 {% data variables.product.prodname_copilot %}。有关信息，请参阅 [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>)。

1. 确保你有 Azure Data Studio 的兼容版本。若要在 Azure Data Studio 中使用 {% data variables.product.prodname_copilot %}，必须安装 Azure Data Studio 版本 1.44.0 或更高版本。请参阅 Azure Data Studio 文档中的 [Azure Data Studio 下载页面 ](<https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio>)。

1. 在 Azure Data Studio 中安装 {% data variables.product.prodname_copilot %} 扩展。请参阅Microsoft文档中的[安装{% data variables.product.prodname_copilot %}扩展](<https://learn.microsoft.com/en-us/azure-data-studio/extensions/github-copilot-extension-overview#install-the-github-copilot-extension>)。

1. 如果 Azure Data Studio 中的弹出窗口提示您登录以使用 {% data variables.product.prodname_copilot %}，请单击 **登录到 {% data variables.product.prodname_dotcom %}** 并按照屏幕上的说明进行操作。

   * 如果您之前已在 {% data variables.product.prodname_dotcom %} 上为您的帐户授权了 Azure Data Studio，则 {% data variables.product.prodname_copilot %} 将自动获得授权。
   * 如果没有收到授权提示，可以通过单击 Azure Data Studio 窗口底部面板中的响铃图标来查看通知。

1. 如果您按照授权步骤操作，则在浏览器中，{% data variables.product.prodname_dotcom %} 将请求 {% data variables.product.prodname_copilot %} 的必要权限。要批准这些权限，请单击 **授权 Azure Data Studio**。

{% endazure_data_studio %}

{% jetbrains %}

## 关于 JetBrains IDE 中的 {% data variables.product.prodname_copilot %} 扩展

在 JetBrains IDE 中安装 {% data variables.product.prodname_copilot %} 扩展允许您在 IDE 中与 {% data variables.product.prodname_copilot_short %} 聊天，并在您键入时接收来自 {% data variables.product.prodname_copilot_short %} 的编码建议。

要查看其他流行编码环境的说明，请使用页面顶部的工具切换器。

### 版本兼容性

有关 JetBrains IDE 中 {% data variables.product.prodname_copilot %} 扩展的版本兼容性的信息，请参阅 JetBrains Marketplace 中的 [{% data variables.product.prodname_copilot %} 版本 ](<https://plugins.jetbrains.com/plugin/17718-github-copilot/versions>)。

### 关于 JetBrains IDE 中 {% data variables.product.prodname_copilot %} 插件的许可证

{% data variables.product.prodname_dotcom %}, Inc. 是 JetBrains 插件的许可方。此插件的最终用户许可协议是 [{% data variables.product.prodname_dotcom %} 附加产品和功能条款 ](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)，并且此插件的使用受这些条款的约束。 JetBrains 不承担与插件或此类协议相关的责任或义务。使用该插件即表示您同意上述条款。

## 在您的 JetBrains IDE 中安装 {% data variables.product.prodname_copilot %} 插件

以下过程将指导您完成在 IntelliJ IDEA 中安装 {% data variables.product.prodname_copilot %} 插件。在另一个受支持的 IDE 中安装插件的步骤可能有所不同。

1. 确保您有权访问 {% data variables.product.prodname_copilot %}。有关信息，请参阅 [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>)。

1. 确保您拥有与 {% data variables.product.prodname_copilot %} 兼容的 JetBrains IDE。 {% data variables.product.prodname_copilot %} 与以下 IDE 兼容：

   {% data reusables.copilot.jetbrains-compatible-ides %}

1. 在您的JetBrains IDE中，打开**插件**设置，在**Marketplace**中搜索{% data variables.product.prodname_copilot %}插件，然后单击**安装**。有关更多信息，请参阅 JetBrains 市场中的 [{% data variables.product.prodname_copilot %} 插件 ](<https://plugins.jetbrains.com/plugin/17718-github-copilot?ref_product=copilot&ref_type=engagement&ref_style=text>)。

1. {% data variables.product.prodname_copilot %}安装完成后，点击**重新启动IDE**。

1. JetBrains IDE 重新启动后，单击 ** 工具 ** 菜单。点击【**{% data variables.product.prodname_copilot %}**】，然后点击【**】登录【{% data variables.product.prodname_dotcom %}**】。

   > [！NOTE] {% data reusables.copilot.sign-in-ghecom %} 请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/authenticate-to-ghecom?tool=jetbrains#authenticating-from-jetbrains-ides>)。

![展开的“工具”菜单和“{% data variables.product.prodname_copilot %}”子菜单的屏幕截图。 “登录 {% data variables.product.prodname_dotcom %}”选项以蓝色突出显示。](<https://github.com/assets/images/help/copilot/jetbrains-tools-menu.png>)

1. 在“登录{% data variables.product.prodname_dotcom %}”对话框中，要复制设备代码并打开设备激活窗口，请单击**复制并打开**。

![“登录 {% data variables.product.prodname_dotcom %}”对话框的屏幕截图。设备代码显示在标有“复制并打开”的按钮上方。](<https://github.com/assets/images/help/copilot/device-code-copy-and-open.png>)

1. 您的浏览器中将打开设备激活窗口。如果提示登录 {% data variables.product.prodname_dotcom %}，请使用您的 {% data variables.product.github %} 帐户登录，或者使用 Google** 单击 **Continue，或使用 Apple** 单击 **Continue。

1. 粘贴设备代码，然后单击**Continue**。

1. {% data variables.product.prodname_dotcom %}将请求{% data variables.product.prodname_copilot %}必要的权限。要批准这些权限，请单击**授权{% data variables.product.prodname_copilot %}插件**。

1. 权限获得批准后，您的JetBrainsIDE将显示确认信息。要开始使用 {% data variables.product.prodname_copilot %}，请单击 **OK**。

## 访问{% data variables.product.prodname_jetbrains_ides %}中的{% data variables.product.prodname_copilot %}的其他方式

该插件提供了完整的{% data variables.product.prodname_copilot_short %}体验，但您也可以在不安装插件的情况下通过JetBrainsAI助手或{% data variables.copilot.copilot_cli_short %}访问{% data variables.product.prodname_copilot_short %}。有关每个入口点提供的功能的比较，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-in-ides>)。

{% endjetbrains %}

{% vimneovim %}

## 关于Vim/Neovim中的{% data variables.product.prodname_copilot %}扩展

在 Vim/Neovim 中安装 {% data variables.product.prodname_copilot %} 扩展允许您在键入时接收来自 {% data variables.product.prodname_copilot_short %} 的编码建议。

要查看其他流行编码环境的说明，请使用页面顶部的工具切换器。

## 在 Vim/Neovim 中安装 {% data variables.product.prodname_copilot %} 扩展

{% data variables.product.prodname_dotcom %}建议您使用Vim/Neovim的内置插件管理器安装{% data variables.product.prodname_copilot %}插件。或者，您可以使用您选择的插件管理器来安装 `github/copilot.vim`。有关更多信息，请参阅 [copilot.vim 存储库 ](<https://github.com/github/copilot.vim>)。

1. 确保您有权访问 {% data variables.product.prodname_copilot %}。有关信息，请参阅 [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>)。

1. 确保您安装了 Vim/Neovim 的兼容版本。要在 Vim/Neovim 中使用 {% data variables.product.prodname_copilot %}，您必须拥有 Vim 版本 9.0.0185 / Neovim 版本 0.6 或更高版本以及 Node.js 版本 18 或更高版本。请参阅 [Vim](<https://vimhelp.org/>) / [Neovim 文档 ](<https://neovim.io/doc/>) 和 [Node.js 网站 ](<https://nodejs.org/en/>)。

1. 使用内置插件管理器安装{% data variables.product.prodname_copilot %}：

   * 对于 macOS 或 Linux** 上的 **Neovim，在终端中运行以下命令。

     ```shell copy
     git clone https://github.com/github/copilot.vim \
     ~/.config/nvim/pack/github/start/copilot.vim
     ```

   * 对于 Windows 上的 **Neovim **，请在 Git Bash 中运行以下命令：

     ```shell copy
     git clone https://github.com/github/copilot.vim.git ^
     %USERPROFILE%/AppData/Local/nvim/pack/github/start/copilot.vim
     ```

   * 对于 macOS 或 Linux** 上的 **Vim，在终端中运行以下命令。

     ```shell copy
     git clone https://github.com/github/copilot.vim \
     ~/.vim/pack/github/start/copilot.vim
     ```

   * 对于 Windows 上的 **Vim **，请在 Git Bash 中运行以下命令：

     ```shell copy
     git clone https://github.com/github/copilot.vim.git ^
     %USERPROFILE%/vimfiles/pack/github/start/copilot.vim
     ```

{% data reusables.copilot.config-enable-copilot-in-vimneovim %}

{% endvimneovim %}

{% visualstudio %}

## 关于{% data variables.product.prodname_vs %}中的{% data variables.product.prodname_copilot %}扩展

在 {% data variables.product.prodname_vs %} 中安装 {% data variables.product.prodname_copilot %} 扩展允许您在键入时接收来自 {% data variables.product.prodname_copilot_short %} 的编码建议。

要查看其他流行编码环境的说明，请使用页面顶部的工具切换器。

### 版本兼容性

从{% data variables.product.prodname_vs %}2022版本17.10开始，默认包含统一的{% data variables.product.prodname_copilot_short %}和{% data variables.copilot.copilot_chat %}扩展作为内置组件。有关更多信息，请参阅[在Microsoft文档中的{% data variables.product.prodname_vs %}](<https://learn.microsoft.com/en-us/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot&ref_type=engagement&ref_style=text>)中安装{% data variables.product.prodname_copilot %}。

以下说明适用于 Windows 版 {% data variables.product.prodname_vs %} 的 2022 17.8 和 2022 17.9 版本。

## 在 {% data variables.product.prodname_vs %} 中安装 {% data variables.product.prodname_copilot %} 扩展

1. 确保您有权访问 {% data variables.product.prodname_copilot %}。有关信息，请参阅 [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>)。

1. 确保您安装了 {% data variables.product.prodname_vs %} 的兼容版本。 {% data reusables.copilot.visual-studio-version %}

1. 在{% data variables.product.prodname_vs %}中安装{% data variables.product.prodname_copilot %}扩展。请参阅[在Microsoft文档中的{% data variables.product.prodname_vs %}](<https://learn.microsoft.com/en-us/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot&ref_type=engagement&ref_style=text>)中安装{% data variables.product.prodname_copilot %}。

1. 安装{% data variables.product.prodname_copilot %}扩展后，要启用{% data variables.product.prodname_copilot %}，请确保您已将{% data variables.product.prodname_dotcom %}帐户添加到{% data variables.product.prodname_vs %}。有关更多信息，请参阅 Microsoft 文档中的 [将您的 {% data variables.product.prodname_dotcom %} 帐户添加到您的 {% data variables.product.prodname_vs %} 钥匙串 ](<https://learn.microsoft.com/en-us/visualstudio/ide/work-with-github-accounts?ref_product=copilot&ref_type=engagement&ref_style=text>)。

   > [！NOTE] {% data reusables.copilot.sign-in-ghecom %} 请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/authenticate-to-ghecom?tool=visualstudio#authenticating-from-visual-studio>)。

{% endvisualstudio %}

{% vscode %}

## 关于{% data variables.product.prodname_vscode %}中的{% data variables.product.prodname_copilot %}

{% data variables.product.prodname_vscode %} 中的 {% data variables.product.prodname_copilot %} 允许您在键入时接收来自 {% data variables.product.prodname_copilot_short %} 的编码建议。您还可以自动访问 {% data variables.copilot.copilot_chat %}，从而可以与 {% data variables.product.prodname_copilot_short %} 聊天。

要查看其他流行编码环境的说明，请使用页面顶部的工具切换器。

## 在{% data variables.product.prodname_vscode %}中设置{% data variables.product.prodname_copilot %}

当您第一次在{% data variables.product.prodname_vscode %}中设置{% data variables.product.prodname_copilot %}时，会自动安装所需的扩展。您无需手动下载或安装它们。

详细说明请参见 [设置 {% data variables.product.prodname_copilot %} 在 {% data variables.product.prodname_vscode %}](<https://code.visualstudio.com/docs/copilot/setup?ref_product=copilot&ref_type=engagement&ref_style=text#_set-up-copilot-in-vs-code>) 在 {% data variables.product.prodname_vscode %} 文档。

> [！NOTE] {% data reusables.copilot.sign-in-ghecom %} 请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/authenticate-to-ghecom?tool=vscode#authenticating-from-vs-code>)。

{% endvscode %}

{% xcode %}

## 关于 Xcode 的 {% data variables.product.prodname_copilot %} 扩展

安装 Xcode 的 {% data variables.product.prodname_copilot %} 扩展允许您在键入时接收来自 {% data variables.product.prodname_copilot_short %} 的编码建议。

要查看其他流行编码环境的说明，请使用页面顶部的工具切换器。

### 版本兼容性

要在 Xcode 中使用 {% data variables.product.prodname_copilot %}，您必须安装 Xcode 版本 8.0 或更高版本以及 macOS Monterey (12.0) 或更高版本。请参阅 Apple 开发者网站上的 [Xcode](<https://developer.apple.com/xcode/>)。

## 安装 Xcode 的 {% data variables.product.prodname_copilot %} 扩展

1. 确保您有权访问 {% data variables.product.prodname_copilot %}。有关信息，请参阅 [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>)。
1. 确保您安装了兼容版本的 Xcode。要在 Xcode 中使用 {% data variables.product.prodname_copilot %}，您必须拥有 Xcode 版本 8.0 或更高版本以及 macOS Monterey (12.0) 或更高版本。请参阅 Apple 开发者网站上的 [Xcode](<https://developer.apple.com/xcode/>)。
1. 从 [`github/CopilotForXcode` 存储库 ](<https://github.com/github/CopilotForXcode/releases/latest/download/GitHubCopilotForXcode.dmg?ref_product=copilot&ref_type=engagement&ref_style=text>) 下载最新版本的 {% data variables.product.prodname_copilot %} for Xcode 扩展并安装。将添加一个背景项，以便应用程序能够在 Xcode 启动时自行启动。
1. 从应用程序文件夹中打开 Xcode** 应用程序的 **{% data variables.product.prodname_copilot %}，然后按照屏幕上的说明逐步设置扩展程序。

## 授予所需的权限

需要两个权限才能使用该扩展：“Accessibility”和“Xcode Source Editor Extension”。首次启动扩展程序时，系统会提示您启用“辅助功能”权限。

需要手动启用“Xcode Source Editor Extension”权限。

1. 打开 Xcode 应用程序的 {% data variables.product.prodname_copilot %}。
1. 点击**扩展权限**。
1. 启用{% data variables.product.prodname_copilot %}并单击**完成**。

授予所需权限后，重新启动 Xcode。您将在“编辑器”菜单中看到一个名为“{% data variables.product.prodname_copilot %}”的新项目。

## 登录 {% data variables.product.prodname_copilot %}

在您可以使用 Xcode 的 {% data variables.product.prodname_copilot %} 扩展程序之前，您需要授权该扩展程序访问您的 {% data variables.product.github %} 帐户。

> [！NOTE] {% data reusables.copilot.sign-in-ghecom %} 请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/authenticate-to-ghecom?tool=xcode#authenticating-from-xcode>)。

1. 打开 Xcode 应用程序的 {% data variables.product.prodname_copilot %}。
1. 点击【**】登录【{% data variables.product.github %}**】，按照提示授权扩展。

{% endxcode %}

{% eclipse %}

## 关于 Eclipse 中的 {% data variables.product.prodname_copilot %}

在 Eclipse 中安装 {% data variables.product.prodname_copilot %} 允许您在键入时接收来自 {% data variables.product.prodname_copilot_short %} 的编码建议。您还可以自动访问 {% data variables.copilot.copilot_chat %}，从而可以与 {% data variables.product.prodname_copilot_short %} 聊天。

要查看其他流行编码环境的说明，请使用页面顶部的工具切换器。

### 版本兼容性

要使用 {% data variables.product.prodname_copilot %} 扩展，您必须拥有 Eclipse 版本 2024-03 或更高版本。请参阅 [Eclipse 下载页面 ](<https://www.eclipse.org/downloads/packages/>)。

## 在 Eclipse 中安装 {% data variables.product.prodname_copilot %}

1. 从 [Eclipse Marketplace](<https://aka.ms/copiloteclipse?ref_product=copilot&ref_type=engagement&ref_style=text>) 或直接通过 [Eclipse 更新站点 ](<https://azuredownloads-g3ahgwb5b8bkbxhd.b01.azurefd.net/github-copilot/?ref_product=copilot&ref_type=engagement&ref_style=text>) 下载并安装最新版本的 {% data variables.product.prodname_copilot %}。有关更多信息，请参阅 Eclipse 文档中的[安装新软件](<https://help.eclipse.org/latest/topic/org.eclipse.platform.doc.user/tasks/tasks-124.htm>)。

1. 安装扩展后，重新启动 Eclipse 以应用更改。

1. 在 Eclipse 工作台右下角，单击 **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**，然后单击 ** 登录到 {% data variables.product.prodname_dotcom %}**。

1. 在“登录{% data variables.product.prodname_dotcom %}”对话框中，要复制设备代码并打开设备激活窗口，请单击**复制代码并打开**。

1. 您的浏览器中将打开设备激活窗口。粘贴设备代码，然后单击**Continue**。

1. {% data variables.product.prodname_dotcom %}将请求{% data variables.product.prodname_copilot %}必要的权限。要批准这些权限，请单击**授权{% data variables.product.prodname_copilot %}插件**。

1. 权限获得批准后，Eclipse 将显示确认信息。要开始使用 {% data variables.product.prodname_copilot %}，请单击 **OK**。

{% endeclipse %}

## 后续步骤

* **开始使用 {% data variables.product.prodname_copilot_short %}** - 了解如何在您喜欢的编码环境中使用 {% data variables.product.prodname_copilot_short %}。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/get-ide-code-suggestions>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

## Prerequisite

To use {% data variables.product.prodname_copilot_short %} in your IDE, you need either limited access through {% data variables.copilot.copilot_free_short %} or a paid {% data variables.product.prodname_copilot_short %} plan for full access. For more information about how to get access and choose the right plan, see [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>).

{% azure_data_studio %}

## About the {% data variables.product.prodname_copilot %} extension in Azure Data Studio

Installing the {% data variables.product.prodname_copilot %} extension in Azure Data Studio allows you to receive coding suggestions from {% data variables.product.prodname_copilot_short %} as you type.

To see instructions for other popular coding environments, use the tool switcher at the top of the page.

## Installing the {% data variables.product.prodname_copilot %} extension in Azure Data Studio

1. Make sure you have access to {% data variables.product.prodname_copilot %}. For information, see [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>).

1. Make sure you have a compatible version of Azure Data Studio. To use {% data variables.product.prodname_copilot %} in Azure Data Studio, you must have Azure Data Studio version 1.44.0 or later installed. See the [Azure Data Studio download page](<https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio>) in the Azure Data Studio documentation.

1. Install the {% data variables.product.prodname_copilot %} extension in Azure Data Studio. See [Install the {% data variables.product.prodname_copilot %} extension](<https://learn.microsoft.com/en-us/azure-data-studio/extensions/github-copilot-extension-overview#install-the-github-copilot-extension>) in the Microsoft documentation.

1. If a popup window in Azure Data Studio prompts you to sign in to use {% data variables.product.prodname_copilot %}, click **Sign in to {% data variables.product.prodname_dotcom %}** and follow the instructions on screen.

   * If you have previously authorized Azure Data Studio for your account on {% data variables.product.prodname_dotcom %}, {% data variables.product.prodname_copilot %} will be automatically authorized.
   * If you don't get the prompt to authorize, you can view notifications by clicking the bell icon in the bottom panel of the Azure Data Studio window.

1. If you are following the authorization steps, in your browser, {% data variables.product.prodname_dotcom %} will request the necessary permissions for {% data variables.product.prodname_copilot %}. To approve these permissions, click **Authorize Azure Data Studio**.

{% endazure_data_studio %}

{% jetbrains %}

## About the {% data variables.product.prodname_copilot %} extension in JetBrains IDEs

Installing the {% data variables.product.prodname_copilot %} extension in JetBrains IDEs allows you to chat with {% data variables.product.prodname_copilot_short %} in your IDE and receive coding suggestions from {% data variables.product.prodname_copilot_short %} as you type.

To see instructions for other popular coding environments, use the tool switcher at the top of the page.

### Version compatibility

For information about version compatibility of the {% data variables.product.prodname_copilot %} extension in JetBrains IDEs, see [{% data variables.product.prodname_copilot %} Versions](<https://plugins.jetbrains.com/plugin/17718-github-copilot/versions>) in the JetBrains Marketplace.

### About the license for the {% data variables.product.prodname_copilot %} plugin in JetBrains IDEs

{% data variables.product.prodname_dotcom %}, Inc. is the licensor of the JetBrains plugin. The end user license agreement for this plugin is the [{% data variables.product.prodname_dotcom %} Terms for Additional Products and Features](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>) and use of this plugin is subject to those terms. JetBrains has no responsibility or liability in connection with the plugin or such agreement. By using the plugin, you agree to the foregoing terms.

## Installing the {% data variables.product.prodname_copilot %} plugin in your JetBrains IDE

The following procedure will guide you through installation of the {% data variables.product.prodname_copilot %} plugin in IntelliJ IDEA. Steps to install the plugin in another supported IDE may differ.

1. Make sure you have access to {% data variables.product.prodname_copilot %}. For information, see [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>).

1. Make sure you have a JetBrains IDE that is compatible with {% data variables.product.prodname_copilot %}. {% data variables.product.prodname_copilot %} is compatible with the following IDEs:

   {% data reusables.copilot.jetbrains-compatible-ides %}

1. In your JetBrains IDE, open the **Plugins** settings, search the **Marketplace** for the {% data variables.product.prodname_copilot %} plugin, then click **Install**. For more information, see [{% data variables.product.prodname_copilot %} plugin](<https://plugins.jetbrains.com/plugin/17718-github-copilot?ref_product=copilot&ref_type=engagement&ref_style=text>) in the JetBrains Marketplace.

1. After {% data variables.product.prodname_copilot %} is installed, click **Restart IDE**.

1. After your JetBrains IDE has restarted, click the **Tools** menu. Click **{% data variables.product.prodname_copilot %}**, then click **Login to {% data variables.product.prodname_dotcom %}**.

   > [!NOTE] {% data reusables.copilot.sign-in-ghecom %} See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/authenticate-to-ghecom?tool=jetbrains#authenticating-from-jetbrains-ides>).

   ![Screenshot of the expanded "Tools" menu and "{% data variables.product.prodname_copilot %}" sub-menu. The "Login to {% data variables.product.prodname_dotcom %}" option is highlighted in blue.](<https://github.com/assets/images/help/copilot/jetbrains-tools-menu.png>)

1. In the "Sign in to {% data variables.product.prodname_dotcom %}" dialog box, to copy the device code and open the device activation window, click **Copy and Open**.

   ![Screenshot of the "Sign in to {% data variables.product.prodname_dotcom %}" dialog. A device code is displayed above a button labeled "Copy and Open".](<https://github.com/assets/images/help/copilot/device-code-copy-and-open.png>)

1. A device activation window will open in your browser. If prompted to sign in to {% data variables.product.prodname_dotcom %}, sign in with your {% data variables.product.github %} account, or click **Continue with Google** or **Continue with Apple**.

1. Paste the device code, then click **Continue**.

1. {% data variables.product.prodname_dotcom %} will request the necessary permissions for {% data variables.product.prodname_copilot %}. To approve these permissions, click **Authorize {% data variables.product.prodname_copilot %} Plugin**.

1. After the permissions have been approved, your JetBrains IDE will show a confirmation. To begin using {% data variables.product.prodname_copilot %}, click **OK**.

## Other ways to access {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_jetbrains_ides %}

The plugin provides the full {% data variables.product.prodname_copilot_short %} experience, but you can also access {% data variables.product.prodname_copilot_short %} through JetBrains AI Assistant or {% data variables.copilot.copilot_cli_short %} without installing the plugin. For a comparison of what each entry point offers, see [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-in-ides>).

{% endjetbrains %}

{% vimneovim %}

## About the {% data variables.product.prodname_copilot %} extension in Vim/Neovim

Installing the {% data variables.product.prodname_copilot %} extension in Vim/Neovim allows you to receive coding suggestions from {% data variables.product.prodname_copilot_short %} as you type.

To see instructions for other popular coding environments, use the tool switcher at the top of the page.

## Installing the {% data variables.product.prodname_copilot %} extension in Vim/Neovim

{% data variables.product.prodname_dotcom %} recommends that you install the {% data variables.product.prodname_copilot %} plugin with Vim/Neovim's built-in plugin manager. Alternatively, you can use a plugin manager of your choice to install `github/copilot.vim`. For more information, see the [copilot.vim repository](<https://github.com/github/copilot.vim>).

1. Make sure you have access to {% data variables.product.prodname_copilot %}. For information, see [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>).

1. Make sure you have a compatible version of Vim/Neovim installed. To use {% data variables.product.prodname_copilot %} in Vim/Neovim you must have Vim version 9.0.0185 / Neovim version 0.6 or above and Node.js version 18 or above. See the [Vim](<https://vimhelp.org/>) / [Neovim documentation](<https://neovim.io/doc/>) and the [Node.js website](<https://nodejs.org/en/>).

1. Install {% data variables.product.prodname_copilot %} using the built-in plugin manager:

   * For **Neovim on macOS or Linux**, run the following command in the terminal.

     ```shell copy
     git clone https://github.com/github/copilot.vim \
     ~/.config/nvim/pack/github/start/copilot.vim
     ```

   * For **Neovim on Windows**, run the following command in Git Bash:

     ```shell copy
     git clone https://github.com/github/copilot.vim.git ^
     %USERPROFILE%/AppData/Local/nvim/pack/github/start/copilot.vim
     ```

   * For **Vim on macOS or Linux**, run the following command in the terminal.

     ```shell copy
     git clone https://github.com/github/copilot.vim \
     ~/.vim/pack/github/start/copilot.vim
     ```

   * For **Vim on Windows**, run the following command in Git Bash:

     ```shell copy
     git clone https://github.com/github/copilot.vim.git ^
     %USERPROFILE%/vimfiles/pack/github/start/copilot.vim
     ```

{% data reusables.copilot.config-enable-copilot-in-vimneovim %}

{% endvimneovim %}

{% visualstudio %}

## About the {% data variables.product.prodname_copilot %} extension in {% data variables.product.prodname_vs %}

Installing the {% data variables.product.prodname_copilot %} extension in {% data variables.product.prodname_vs %} allows you to receive coding suggestions from {% data variables.product.prodname_copilot_short %} as you type.

To see instructions for other popular coding environments, use the tool switcher at the top of the page.

### Version compatibility

Starting from {% data variables.product.prodname_vs %} 2022 Version 17.10, the unified {% data variables.product.prodname_copilot_short %} and {% data variables.copilot.copilot_chat %} extension is included by default as a built-in component. For more information, see [Install {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vs %}](<https://learn.microsoft.com/en-us/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot&ref_type=engagement&ref_style=text>) in the Microsoft documentation.

The following instructions are for versions 2022 17.8 and 2022 17.9 of {% data variables.product.prodname_vs %} for Windows.

## Installing the {% data variables.product.prodname_copilot %} extension in {% data variables.product.prodname_vs %}

1. Make sure you have access to {% data variables.product.prodname_copilot %}. For information, see [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>).

1. Make sure you have a compatible version of {% data variables.product.prodname_vs %} installed. {% data reusables.copilot.visual-studio-version %}

1. Install the {% data variables.product.prodname_copilot %} extension in {% data variables.product.prodname_vs %}. See [Install {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vs %}](<https://learn.microsoft.com/en-us/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot&ref_type=engagement&ref_style=text>) in the Microsoft documentation.

1. After installing the {% data variables.product.prodname_copilot %} extension, to enable {% data variables.product.prodname_copilot %}, ensure you have added your {% data variables.product.prodname_dotcom %} account to {% data variables.product.prodname_vs %}. For more information, see [Add your {% data variables.product.prodname_dotcom %} accounts to your {% data variables.product.prodname_vs %} keychain](<https://learn.microsoft.com/en-us/visualstudio/ide/work-with-github-accounts?ref_product=copilot&ref_type=engagement&ref_style=text>) in the Microsoft documentation.

   > [!NOTE] {% data reusables.copilot.sign-in-ghecom %} See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/authenticate-to-ghecom?tool=visualstudio#authenticating-from-visual-studio>).

{% endvisualstudio %}

{% vscode %}

## About {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vscode %}

{% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vscode %} allows you to receive coding suggestions from {% data variables.product.prodname_copilot_short %} as you type. You also automatically get access to {% data variables.copilot.copilot_chat %}, which allows you to chat with {% data variables.product.prodname_copilot_short %}.

To see instructions for other popular coding environments, use the tool switcher at the top of the page.

## Setting up {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vscode %}

When you set up {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vscode %} for the first time, the required extensions are installed automatically. You don't need to download or install them manually.

For detailed instructions, see [Set up {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vscode %}](<https://code.visualstudio.com/docs/copilot/setup?ref_product=copilot&ref_type=engagement&ref_style=text#_set-up-copilot-in-vs-code>) in the {% data variables.product.prodname_vscode %} documentation.

> [!NOTE] {% data reusables.copilot.sign-in-ghecom %} See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/authenticate-to-ghecom?tool=vscode#authenticating-from-vs-code>).

{% endvscode %}

{% xcode %}

## About the {% data variables.product.prodname_copilot %} extension for Xcode

Installing the {% data variables.product.prodname_copilot %} extension for Xcode allows you to receive coding suggestions from {% data variables.product.prodname_copilot_short %} as you type.

To see instructions for other popular coding environments, use the tool switcher at the top of the page.

### Version compatibility

To use {% data variables.product.prodname_copilot %} for Xcode you must have Xcode version 8.0 or above and macOS Monterey (12.0) or above installed. See [Xcode](<https://developer.apple.com/xcode/>) on the Apple Developer site.

## Installing the {% data variables.product.prodname_copilot %} extension for Xcode

1. Make sure you have access to {% data variables.product.prodname_copilot %}. For information, see [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>).
1. Make sure you have a compatible version of Xcode installed. To use {% data variables.product.prodname_copilot %} for Xcode you must have Xcode version 8.0 or above and macOS Monterey (12.0) or above. See [Xcode](<https://developer.apple.com/xcode/>) on the Apple Developer site.
1. Download the latest version of the {% data variables.product.prodname_copilot %} for Xcode extension from the [`github/CopilotForXcode` repository](<https://github.com/github/CopilotForXcode/releases/latest/download/GitHubCopilotForXcode.dmg?ref_product=copilot&ref_type=engagement&ref_style=text>) and install it. A background item will be added for the application to be able to start itself when Xcode starts.
1. Open the **{% data variables.product.prodname_copilot %} for Xcode** application from the Applications folder and step through the on-screen instructions for setting up the extension.

## Granting required permissions

Two permissions are required to be able to use the extension: "Accessibility" and "Xcode Source Editor Extension". You will be prompted to enable the "Accessibility" permission when you first start the extension.

The "Xcode Source Editor Extension" permission needs to be enabled manually.

1. Open the {% data variables.product.prodname_copilot %} for Xcode application.
1. Click **Extension Permission**.
1. Enable {% data variables.product.prodname_copilot %} and click **Done**.

After granting the required permissions, restart Xcode. You will see a new item in the "Editor" menu called "{% data variables.product.prodname_copilot %}".

## Signing in to {% data variables.product.prodname_copilot %}

Before you can use the {% data variables.product.prodname_copilot %} extension for Xcode, you need to authorize the extension to access your {% data variables.product.github %} account.

> [!NOTE] {% data reusables.copilot.sign-in-ghecom %} See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/authenticate-to-ghecom?tool=xcode#authenticating-from-xcode>).

1. Open the {% data variables.product.prodname_copilot %} for Xcode application.
1. Click **Login to {% data variables.product.github %}** and follow the prompts to authorize the extension.

{% endxcode %}

{% eclipse %}

## About {% data variables.product.prodname_copilot %} in Eclipse

Installing {% data variables.product.prodname_copilot %} in Eclipse allows you to receive coding suggestions from {% data variables.product.prodname_copilot_short %} as you type. You also automatically get access to {% data variables.copilot.copilot_chat %}, which allows you to chat with {% data variables.product.prodname_copilot_short %}.

To see instructions for other popular coding environments, use the tool switcher at the top of the page.

### Version compatibility

To use the {% data variables.product.prodname_copilot %} extension, you must have Eclipse version 2024-03 or above. See the [Eclipse download page](<https://www.eclipse.org/downloads/packages/>).

## Installing {% data variables.product.prodname_copilot %} in Eclipse

1. Download and install the latest version of {% data variables.product.prodname_copilot %} from the [Eclipse Marketplace](<https://aka.ms/copiloteclipse?ref_product=copilot&ref_type=engagement&ref_style=text>) or directly via the [Eclipse Update Site](<https://azuredownloads-g3ahgwb5b8bkbxhd.b01.azurefd.net/github-copilot/?ref_product=copilot&ref_type=engagement&ref_style=text>). For more information, see [Installing New Software](<https://help.eclipse.org/latest/topic/org.eclipse.platform.doc.user/tasks/tasks-124.htm>) in the Eclipse documentation.

1. After the extension is installed, restart Eclipse to apply the changes.

1. In the bottom right corner of the Eclipse workbench, click **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**, then click **Sign In to {% data variables.product.prodname_dotcom %}**.

1. In the "Sign In to {% data variables.product.prodname_dotcom %}" dialog box, to copy the device code and open the device activation window, click **Copy Code and Open**.

1. A device activation window will open in your browser. Paste the device code, then click **Continue**.

1. {% data variables.product.prodname_dotcom %} will request the necessary permissions for {% data variables.product.prodname_copilot %}. To approve these permissions, click **Authorize {% data variables.product.prodname_copilot %} Plugin**.

1. After the permissions have been approved, Eclipse will show a confirmation. To begin using {% data variables.product.prodname_copilot %}, click **OK**.

{% endeclipse %}

## Next steps

* **Get started with {% data variables.product.prodname_copilot_short %}** - Learn how to use {% data variables.product.prodname_copilot_short %} in your preferred coding environment. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/get-ide-code-suggestions>).


</details>
