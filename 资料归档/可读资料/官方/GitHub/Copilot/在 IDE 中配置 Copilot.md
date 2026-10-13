# 在 IDE 中配置 Copilot

按支持的编辑器说明启用、停用、快捷键、语言与建议设置，帮助调整代码补全和交互体验。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-in-your-ide/set-up-copilot/configure-in-ide.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 IDE 中配置 Copilot.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Configuring GitHub Copilot in your environment
shortTitle: Configure in IDE
intro: You can enable, configure, or disable {% data variables.product.prodname_copilot %} in a supported IDE.
redirect_from:
  - /copilot/configuring-github-copilot/configuring-github-copilot-in-visual-studio
  - /copilot/configuring-github-copilot/configuring-github-copilot-in-visual-studio-code
  - /copilot/configuring-github-copilot/configuring-github-copilot-in-a-jetbrains-ide
  - /copilot/configuring-github-copilot/configuring-github-copilot-in-neovim
  - /copilot/configuring-github-copilot/configuring-github-copilot-in-your-environment
  - /copilot/managing-copilot/configure-personal-settings/configuring-github-copilot-in-your-environment
  - /copilot/how-tos/personal-settings/configuring-github-copilot-in-your-environment
  - /copilot/how-tos/personal-settings/configure-in-your-environment
  - /copilot/how-tos/configure-personal-settings/configure-in-your-environment
  - /copilot/how-tos/configure-personal-settings/configure-in-ide
versions:
  feature: copilot
contentType: how-tos
category:
  - Configure Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% jetbrains %}

## 关于 JetBrains IDE 中的 {% data variables.product.prodname_copilot %}

如果您使用 JetBrains IDE，{% data variables.product.prodname_copilot %} 可以帮助您完成各种任务，包括生成代码建议、解释编辑器中的代码如何工作以及建议代码修复。安装后，您可以启用或禁用{% data variables.product.prodname_copilot %}，并且可以在IDE或{% data variables.product.github %}上配置高级设置。本文介绍如何在 IntelliJ IDE 中配置 {% data variables.product.prodname_copilot %}，但其他 JetBrains IDE 的用户界面可能会有所不同。

## 先决条件

要在JetBrains IDE中配置{% data variables.product.prodname_copilot %}，您必须安装{% data variables.product.prodname_copilot %}插件。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=jetbrains>)。

## 启用或禁用{% data variables.product.prodname_copilot %}

您可以从 JetBrains IDE 中启用或禁用 {% data variables.product.prodname_copilot %}。 JetBrains窗口底部面板中的{% data variables.product.prodname_copilot %}状态图标指示{% data variables.product.prodname_copilot %}是否启用或禁用。启用后，该图标会突出显示。禁用后，该图标呈灰色。

1. 要启用或禁用{% data variables.product.prodname_copilot %}，请单击JetBrains窗口右侧底部面板中的状态图标。

![JetBrains IDE 中底部面板的屏幕截图。 {% data variables.product.prodname_copilot %} 状态图标以深橙色轮廓显示。](<https://github.com/assets/images/help/copilot/status-icon-jetbrains.png>)

1. 如果您要禁用 {% data variables.product.prodname_copilot %}，系统会询问您是否要全局禁用它，或者针对当前正在编辑的文件的语言禁用它。要全局禁用，请单击**禁用完成**。或者，单击特定于语言的按钮以禁用指定语言的 {% data variables.product.prodname_copilot %}。

![全局禁用 {% data variables.product.prodname_copilot %} 或 JetBrains IDE 中当前语言的菜单屏幕截图。](<https://github.com/assets/images/help/copilot/disable-copilot-global-or-language-jetbrains.png>)

## 重新绑定键盘快捷键

使用 {% data variables.product.prodname_copilot %} 时，您可以在 JetBrains IDE 中使用默认键盘快捷键进行内联建议。有关默认键盘快捷键的列表，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/keyboard-shortcuts>)。

或者，您可以将每个特定命令的快捷方式重新绑定到您的首选键盘快捷方式。有关在 JetBrains IDE 中重新绑定键盘快捷键的更多信息，请参阅 JetBrains 文档。例如，您可以查看[IntelliJ IDEA](<https://www.jetbrains.com/help/idea/mastering-keyboard-shortcuts.html#choose-keymap>)文档。

## 配置{% data variables.product.prodname_copilot %}的高级设置

您可以在 JetBrains IDE 中管理 {% data variables.product.prodname_copilot %} 的高级设置，例如 IDE 如何显示内联建议，以及您要为 {% data variables.product.prodname_copilot %} 启用或禁用哪些语言。

{% data reusables.copilot.jetbrains-settings %}
{% data reusables.copilot.jetbrains-tools %}，然后查看 **常规** 和 **完成** 设置。
1. 根据您的个人喜好编辑设置。
   * 要调整代码建议的行为和外观，以及是否自动检查更新，请选择或取消选择相应的复选框。
   * 如果您选择接收自动更新，则可以选择是接收稳定但频率较低的更新，还是接收可能不太稳定的夜间更新。单击 ** 更新通道 ** 下拉列表，然后选择 ** 稳定 ** 进行稳定更新，或选择 **夜间 ** 进行夜间更新。

## 配置{% data variables.product.prodname_copilot %}的语言设置

您可以在 IDE 中或通过编辑 `github-copilot.xml` 文件来指定要激活或停用 {% data variables.product.prodname_copilot %} 的语言。如果您更改IDE中的语言设置，您可以单独选择和取消选择您想要激活或停用的语言。

如果您更改 `github-copilot.xml` 文件中的语言设置，则可以指定单独的语言，也可以使用通配符激活或停用所有语言的 {% data variables.product.prodname_copilot %}。您还可以指定例外，这将覆盖指定语言的通配符设置。例如，您可以对除 Python 和 YAML 之外的所有语言停用 {% data variables.product.prodname_copilot %}。默认情况下，当您安装 {% data variables.product.prodname_copilot %} 扩展时，将为所有语言激活 {% data variables.product.prodname_copilot %}。

### 在IDE中配置语言设置

{% data reusables.copilot.jetbrains-settings %}
{% data reusables.copilot.jetbrains-tools %}，然后单击 ** 完成情况 **。
1. 在“语言”下，选择或取消选择您想要激活或停用 {% data variables.product.prodname_copilot %} 的语言的复选框。
1. 单击**应用**，然后单击**OK**。
1. 重新启动您的JetBrains IDE以使更改生效。

### 编辑您的 `github-copilot.xml` 文件

要在 `github-copilot.xml` 文件中配置语言设置，您必须编辑 `languageAllowList`。添加到 `languageAllowList` 的每一行都必须包含输入键和值。输入键是语言名称，或 (`*`) 表示通配符。该值为 `true` 或 `false`。如果值为`true`，则为指定语言激活{% data variables.product.prodname_copilot %}。如果值为 `false`，则对指定语言禁用 {% data variables.product.prodname_copilot %}。

该文件位于以下目录中：

* **macOS:** `~/Library/Application Support/JetBrains/<product><version>/options/github-copilot.xml`
* **Windows：** `%APPDATA%\JetBrains\<product><version>\options\github-copilot.xml`
* **Linux：** `~/.config/JetBrains/<product><version>/options/github-copilot.xml`

例如，如果您在 macOS 上使用 IntelliJ IDEA 2021.1，则该文件位于 `~/Library/Application Support/JetBrains/IdeaIC2021.1/options/github-copilot.xml`。

在 IDE 设置中更改默认语言配置之前，可能不会生成 `github-copilot.xml` 文件。如果找不到该文件，您应该尝试修改IDE中的默认语言设置。有关详细信息，请参阅[在IDE](<#在ide中配置语言设置>)中配置语言设置。

或者，您可以手动创建该文件并将其保存在上面列出的适合您的操作系统的位置。有关更多信息，请参阅[语言配置示例](<#语言配置示例>)。

1. 在文本编辑器中打开 `github-copilot.xml` 文件。
1. 在 `<map>` 标签之间，添加您想要激活或停用 {% data variables.product.prodname_copilot %} 的语言的一行或多行。例如，要停用所有语言的 {% data variables.product.prodname_copilot %}：

    ```xml copy
    <entry key="*" value="false" />
    ```

1. 将更改保存到 `github-copilot.xml` 文件。
1. 重新启动您的JetBrains IDE以使更改生效。

### 语言配置示例

`github-copilot.xml`文件的默认配置为所有语言启用{% data variables.product.prodname_copilot %}，如下：

```xml copy
<application>
  <component name="github-copilot">
    <languageAllowList>
      <map>
        <entry key="*" value="true" />
      </map>
    </languageAllowList>
  </component>
</application>
```

要对所有语言停用 {% data variables.product.prodname_copilot %}，请将通配符 (`*`) 值更改为 `false`：

```xml copy
<application>
  <component name="github-copilot">
    <languageAllowList>
      <map>
        <entry key="*" value="false" />
      </map>
    </languageAllowList>
  </component>
</application>
```

要单独指定语言，请为要激活或停用 {% data variables.product.prodname_copilot %} 的每种语言添加一个条目。特定语言设置将覆盖通配符。例如，要为 Python 和 YAML 激活 {% data variables.product.prodname_copilot %}，并为所有其他语言停用 {% data variables.product.prodname_copilot %}，请添加以下条目：

```xml copy
<application>
  <component name="github-copilot">
    <languageAllowList>
      <map>
        <entry key="*" value="false" />
        <entry key="Python" value="true" />
        <entry key="YAML" value="true" />
      </map>
    </languageAllowList>
  </component>
</application>
```

您还可以在IDE的设置中添加配置以使`languageAllowList`只读。这将阻止您更改 IDE 中的语言设置。例如：

```xml copy
<application>
  <component name="github-copilot">
    <option name="languageAllowListReadOnly" value="true" />
    <languageAllowList>
      <map>
        <entry key="*" value="true" />
      </map>
    </languageAllowList>
  </component>
</application>
```

{% data reusables.copilot.dotcom-settings %}

## 进一步阅读

* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot/#faq>)

{% endjetbrains %}

{% visualstudio %}

## 关于{% data variables.product.prodname_vs %}中的{% data variables.product.prodname_copilot %}

如果您使用 {% data variables.product.prodname_vs %}，{% data variables.product.prodname_copilot %} 可以帮助您完成各种任务，包括生成代码建议、解释编辑器中的代码如何工作以及建议代码修复。安装后，您可以启用或禁用{% data variables.product.prodname_copilot %}，并且可以在{% data variables.product.prodname_vs %}或{% data variables.product.github %}上配置高级设置。

## 先决条件

要在{% data variables.product.prodname_vs %}中配置{% data variables.product.prodname_copilot %}，您必须安装{% data variables.product.prodname_copilot %}插件。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=visualstudio>)。

## 重新绑定键盘快捷键

使用 {% data variables.product.prodname_copilot %} 时，您可以使用 {% data variables.product.prodname_vs %} 中内联建议的默认键盘快捷键。有关默认键盘快捷键的列表，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/keyboard-shortcuts>)。

如果您在使用 {% data variables.product.prodname_copilot %} 时不想使用 {% data variables.product.prodname_vs %} 中的默认键盘快捷键，您可以使用每个特定命令的首选键盘快捷键在键盘编辑器中重新绑定快捷键。

1. 在{% data variables.product.prodname_vs %}菜单栏的**工具**下，单击**选项**。

![{% data variables.product.prodname_vs %}菜单栏的屏幕截图。 “工具”菜单展开，“选项”项以橙色轮廓突出显示。](<https://github.com/assets/images/help/copilot/vs-toolbar-options.png>)

1. 在“选项”对话框中的**环境**下，单击**键盘**。
1. 在“显示包含以下内容的命令”下，搜索要重新绑定的命令。

![“显示包含的命令”搜索栏的屏幕截图。在搜索字段中输入字符串“tools.next”。](<https://github.com/assets/images/help/copilot/vs-show-commands-containing.png>)

1. 在“按快捷键”下，键入要分配给命令的快捷键，然后单击“**分配**”。

![用于输入新键盘快捷键分配的字段的屏幕截图。](<https://github.com/assets/images/help/copilot/vs-rebind-shortcut.png>)

{% data reusables.copilot.enabling-or-disabling-vs %}

## 为{% data variables.product.prodname_copilot %}配置ReSharper

如果您使用 ReSharper，则当您将 ReSharper 配置为使用 {% data variables.product.prodname_copilot %} 的本机 IntelliSense 时，{% data variables.product.prodname_copilot %} 可能效果最佳。有关 ReSharper 的更多信息，请参阅 [ReSharper 文档 ](<https://www.jetbrains.com/resharper/documentation/documentation.html>)

1. 在 {% data variables.product.prodname_vs %} 菜单栏中的 ** 扩展 ** 下，单击 **ReSharper**，然后单击 ** 选项 **。
1. 在“选项”对话框中的**环境**下，单击**IntelliSense**，然后单击**常规**。
1. 在“常规”下选择**{% data variables.product.prodname_vs %}**，然后单击**保存**。

{% data reusables.copilot.dotcom-settings %}

## 启用{% data variables.copilot.next_edit_suggestions %}

要在{% data variables.product.prodname_vs %}中使用{% data variables.copilot.next_edit_suggestions %}，您需要先启用该功能。

1. 在{% data variables.product.prodname_vs %}菜单栏的**工具**下，单击**选项**。
1. 在“选项”对话框中的 **{% data variables.product.github %}** 下，单击 **{% data variables.product.prodname_copilot_short %}**，然后单击 **{% data variables.product.prodname_copilot_short %} 完成项 **。
1. 勾选**启用{% data variables.copilot.next_edit_suggestions %}**。

## 进一步阅读

* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot/#faq>)

{% endvisualstudio %}

{% vscode %}

## 关于{% data variables.product.prodname_vscode %}中的{% data variables.product.prodname_copilot %}

如果您使用 {% data variables.product.prodname_vscode %}，{% data variables.product.prodname_copilot %} 可以帮助您完成各种任务，包括生成代码建议、解释编辑器中的代码如何工作以及根据您的指示提出编辑建议。您可以启用或禁用{% data variables.product.prodname_copilot %}，并在{% data variables.product.prodname_vscode %}或{% data variables.product.github %}上配置高级设置。

您可以在[{% data variables.product.prodname_vscode_shortname %}文档](<https://code.visualstudio.com/docs/copilot/overview#_use-cases-for-github-copilot-in-vs-code>)中了解有关场景和设置的更多信息。

## 重新绑定键盘快捷键

使用 {% data variables.product.prodname_copilot %} 时，您可以使用 {% data variables.product.prodname_vscode_shortname %} 中内联建议的默认键盘快捷键。在键盘快捷键编辑器中按命令名称搜索键盘快捷键。有关默认键盘快捷键的列表，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/keyboard-shortcuts>)。

或者，您可以在键盘快捷键编辑器中重新绑定每个命令的快捷键。有关更多信息，请参阅有关编辑快捷方式的 [{% data variables.product.prodname_vscode %} 文档 ](<https://code.visualstudio.com/Docs/editor/keybindings>)。

{% data reusables.copilot.enabling-or-disabling-in-vsc %}

## 启用或禁用内联建议

您可以选择在 {% data variables.product.prodname_vscode %} 中启用或禁用 {% data variables.product.prodname_copilot %} 的内嵌建议。

{% data reusables.copilot.vscode-settings %}
1. 在设置选项卡的左侧面板中，单击**扩展**，然后选择**{% data variables.product.prodname_copilot_short %}**。
1. 在“内联建议：启用”下，选择或取消选择复选框以启用或禁用内联建议。

## 启用{% data variables.copilot.next_edit_suggestions %}

您可以通过{% data variables.product.prodname_vscode_shortname %}设置`github.copilot.nextEditSuggestions.enabled`启用{% data variables.copilot.next_edit_suggestions %}。有关更详细的说明，请参阅 {% data variables.product.prodname_vscode_shortname %} 文档中的 [启用编辑建议 ](<https://code.visualstudio.com/docs/copilot/ai-powered-suggestions#_enabling-edit-suggestions>)。

{% data reusables.copilot.editor-preview-settings %}

## 针对特定语言启用或禁用 {% data variables.product.prodname_copilot %}

您可以指定要启用或禁用 {% data variables.product.prodname_copilot %} 的语言。

1. 在 {% data variables.product.prodname_vscode %} 中，单击 ** 扩展 ** 选项卡，然后导航到 **Copilot** 部分。有关详细信息，请参阅[启用或禁用内联建议](<#启用或禁用内联建议>)。
1. 在“对指定语言启用或禁用 {% data variables.product.prodname_copilot_short %}”下，单击 ** 在 settings.json** 中编辑。
1. 在 _settings.json_ 文件中，添加或删除要启用或禁用 {% data variables.product.prodname_copilot %} 的语言。例如，要在 {% data variables.product.prodname_copilot %} 中启用 Python，请将 `"python": true` 添加到列表中，确保除最后一个列表项之外的所有后面都有一个尾随逗号。

   {% data reusables.copilot.vs-code-settings-json-ex %}

## 撤销{% data variables.product.prodname_copilot %}授权

{% data variables.product.prodname_vscode %}保留通过特定{% data variables.product.prodname_dotcom %}帐户使用{% data variables.product.prodname_copilot %}的授权。如果您想防止您的{% data variables.product.prodname_dotcom %}账户在您无法再访问的设备上被用于{% data variables.product.prodname_copilot %}，您可以撤销授权，然后重新执行授权流程。您之前使用的设备将不会获得新的授权。

{% data reusables.user-settings.access_settings %}
{% data reusables.user-settings.access_applications %}
{% data reusables.user-settings.access_authorized_oauth_apps %}
1. 单击{% data variables.product.prodname_vscode_shortname %}**的**{% data variables.product.prodname_dotcom %}旁边的**...**，然后单击**撤销**。
{% data reusables.user-settings.access_authorized_github_apps %}
1. 如果列出了 **{% data variables.product.prodname_copilot %}** 扩展，请单击 ** 撤销 **。

撤销授权后，{% data variables.product.prodname_vscode %}将能够在当前会话中继续使用{% data variables.product.prodname_copilot %}，最长持续时间为30分钟。此后，您将需要重新授权{% data variables.product.prodname_copilot %}才能再次在{% data variables.product.prodname_vscode %}中使用。

## 重新授权{% data variables.product.prodname_copilot %}

撤销授权后，如果您想继续使用【{% data variables.product.prodname_copilot %}】，则需要完成重新授权流程。

1. 在{% data variables.product.prodname_vscode %}的左下角，单击**帐户**图标，将鼠标悬停在您的用户名上，然后单击**退出**。

![{% data variables.product.prodname_vscode %}中的菜单截图。 “注销”选项以深橙色轮廓显示。](<https://github.com/assets/images/help/copilot/vsc-sign-out.png>)

1. 在“{% data variables.product.prodname_vscode %}”弹出窗口中，单击**注销**。

1. 在{% data variables.product.prodname_vscode %}左下角，点击**账户**图标，将鼠标悬停在您的用户名上，然后点击**使用{% data variables.product.prodname_dotcom %}登录以使用{% data variables.product.prodname_copilot %}**。

![{% data variables.product.prodname_vscode %}中帐户菜单的屏幕截图。 “使用 {% data variables.product.prodname_dotcom %} 登录以使用 {% data variables.product.prodname_copilot %} (1)”选项以深橙色轮廓显示。](<https://github.com/assets/images/help/copilot/vsc-sign-in.png>)

1. 在您的浏览器中，{% data variables.product.prodname_dotcom %}将请求{% data variables.product.prodname_copilot %}必要的权限。要批准这些权限，请单击 **Continue**。
1. 在“打开{% data variables.product.prodname_vscode %}？”中弹出窗口，点击【**】打开【{% data variables.product.prodname_vscode %}**】。

{% data reusables.copilot.dotcom-settings %}

## 进一步阅读

* [{% data variables.product.prodname_copilot %} 在 VS Code](<https://code.visualstudio.com/docs/copilot/overview>) 中
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot/#faq>)

{% endvscode %}

{% vimneovim %}

## 在Vim/Neovim中配置{% data variables.product.prodname_copilot %}

有关在 Vim/Neovim 中配置 {% data variables.product.prodname_copilot %} 的指南，请通过运行以下命令调用 Vim/Neovim 中的 {% data variables.product.prodname_copilot %} 文档：

：帮助copilot

## 重新绑定键盘快捷键

使用{% data variables.product.prodname_copilot %}时，您可以重新绑定Vim/Neovim中的键盘快捷键，以便为每个特定命令使用您喜欢的键盘快捷键。有关更多信息，请参阅 Neovim 文档中的 [Map](<https://neovim.io/doc/user/map.html>) 文章。

{% data reusables.copilot.dotcom-settings %}

## 进一步阅读

* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot/#faq>)

{% endvimneovim %}

{% xcode %}

## 关于Xcode中的{% data variables.product.prodname_copilot %}

如果您使用 Xcode，{% data variables.product.prodname_copilot %} 可以帮助您完成各种任务，包括生成代码建议、解释编辑器中的代码如何工作以及建议代码修复。安装后，您可以启用或禁用{% data variables.product.prodname_copilot %}，并且可以在Xcode或{% data variables.product.github %}上配置高级设置。

## 先决条件

要为 Xcode 配置 {% data variables.product.prodname_copilot %}，您必须安装 {% data variables.product.prodname_copilot %} 扩展。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=xcode>)。

## 重新绑定键盘快捷键

使用 {% data variables.product.prodname_copilot %} 时，您可以在 Xcode 中使用默认键盘快捷键进行内联建议。有关默认键盘快捷键的列表，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/keyboard-shortcuts>)。

如果您不想使用 {% data variables.product.prodname_copilot %} 的默认键盘快捷键，您可以在按键绑定编辑器中重新绑定快捷键并使用您喜欢的键盘快捷键。

如果您想使用 <kbd>Tab</kbd> 之外的其他内容来接受建议的第一行，则需要在 Xcode 应用程序的 {% data variables.product.prodname_copilot %} 的高级设置中禁用“接受 Tab 建议”选项。此外，我们目前仅支持 <kbd>Option</kbd> 键来执行“查看完整建议”操作。

1. 在 Xcode 菜单中，单击 **Xcode**，然后单击 **设置**。
1. 单击**按键绑定**并搜索“{% data variables.product.prodname_copilot_short %}”以找到您要重新绑定的命令。

## 启用或禁用{% data variables.product.prodname_copilot %}

您可以从应用程序内启用或禁用 {% data variables.product.prodname_copilot %} 扩展。

1. 打开 Xcode 应用程序的 {% data variables.product.prodname_copilot %}。
1. 在应用程序窗口顶部，单击**高级**。
1. 在“建议设置”部分中，使用“键入时请求建议”开关来启用或禁用该扩展程序。

## 自动更新 Xcode {% data variables.product.prodname_copilot %}

您可以配置 {% data variables.product.prodname_copilot %} 扩展来自动检查更新。

1. 打开 Xcode 应用程序的 {% data variables.product.prodname_copilot %}。
1. 选择**自动检查更新**。

更新扩展后，必须重新启动 Xcode 才能使更改生效。

## 禁用 {% data variables.copilot.next_edit_suggestions %}

{% data variables.copilot.next_edit_suggestions_caps %} 默认启用。要禁用{% data variables.copilot.next_edit_suggestions %}，请转到{% data variables.product.prodname_copilot %}中的“高级”选项卡进行Xcode扩展设置。您还可以选择禁用“使用 Tab 接受建议”选项。

{% data reusables.copilot.editor-preview-settings %}

{% data reusables.copilot.dotcom-settings %}

{% endxcode %}

{% eclipse %}

## 关于 Eclipse 中的 {% data variables.product.prodname_copilot %}

如果您使用 Eclipse，{% data variables.product.prodname_copilot %} 可以在您使用 IDE 时提供代码建议。您还可以使用 {% data variables.copilot.copilot_chat_short %} 面板与 {% data variables.product.prodname_copilot_short %} 一起作为您的 AI 配对编程器。

在Eclipse中安装{% data variables.product.prodname_copilot %}后，您可以启用或禁用它，并且可以在IDE中配置高级设置。

## 先决条件

要在 Eclipse 中配置 {% data variables.product.prodname_copilot %}，您必须安装 {% data variables.product.prodname_copilot %} 扩展。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=eclipse>)。

## 重新绑定键盘快捷键

如果您不想使用 {% data variables.product.prodname_copilot %} 的默认键盘快捷键，您可以在按键绑定编辑器中重新绑定快捷键并使用您喜欢的键盘快捷键。有关默认键盘快捷键的列表，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/keyboard-shortcuts>)。

1. 在【IDE】中，点击【**{% octicon "copilot" aria-hidden="true" aria-label="copilot" %}】【{% data variables.product.prodname_copilot_short %}**】打开菜单。
1. 单击**编辑键盘快捷键...**重新绑定快捷键。

## 设置和配置

对于高级设置，您可以设置自动完成行为、配置代理并分配 {% data variables.product.prodname_enterprise %} 身份验证端点。

## 启用{% data variables.copilot.next_edit_suggestions %}

您可以在 Eclipse 中的 {% data variables.product.prodname_copilot %} 扩展设置中的“Completions”下启用 {% data variables.copilot.next_edit_suggestions %}。

{% data reusables.copilot.editor-preview-settings %}

{% data reusables.copilot.dotcom-settings %}

{% endeclipse %}


## 原文对照

<details>
<summary>展开完整原文</summary>

{% jetbrains %}

## About {% data variables.product.prodname_copilot %} in JetBrains IDEs

If you use a JetBrains IDE, {% data variables.product.prodname_copilot %} can help you with a variety of tasks, including generating code suggestions, explaining how the code in your editor works, and suggesting code fixes. After installation, you can enable or disable {% data variables.product.prodname_copilot %}, and you can configure advanced settings within your IDE or on {% data variables.product.github %}. This article describes how to configure {% data variables.product.prodname_copilot %} in the IntelliJ IDE, but the user interfaces of other JetBrains IDEs may differ.

## Prerequisites

To configure {% data variables.product.prodname_copilot %} in a JetBrains IDE, you must install the {% data variables.product.prodname_copilot %} plugin. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=jetbrains>).

## Enabling or disabling {% data variables.product.prodname_copilot %}

You can enable or disable {% data variables.product.prodname_copilot %} from within your JetBrains IDE. The {% data variables.product.prodname_copilot %} status icon in the bottom panel of the JetBrains window indicates whether {% data variables.product.prodname_copilot %} is enabled or disabled. When enabled, the icon is highlighted. When disabled, the icon is grayed out.

1. To enable or disable {% data variables.product.prodname_copilot %}, click the status icon in the bottom panel on the right of the JetBrains window.

    ![Screenshot of the bottom panel in a JetBrains IDE. The {% data variables.product.prodname_copilot %} status icon is outlined in dark orange.](<https://github.com/assets/images/help/copilot/status-icon-jetbrains.png>)

1. If you are disabling {% data variables.product.prodname_copilot %}, you will be asked whether you want to disable it globally, or for the language of the file you are currently editing. To disable globally, click **Disable Completions**. Alternatively, click the language-specific button to disable {% data variables.product.prodname_copilot %} for the specified language.

    ![Screenshot of the menu to disable {% data variables.product.prodname_copilot %} globally or for the current language in a JetBrains IDE.](<https://github.com/assets/images/help/copilot/disable-copilot-global-or-language-jetbrains.png>)

## Rebinding keyboard shortcuts

You can use the default keyboard shortcuts for inline suggestions in your JetBrains IDE when using {% data variables.product.prodname_copilot %}. For a list of default keyboard shortcuts, see [AUTOTITLE](<https://github.com/copilot/reference/keyboard-shortcuts>).

Alternatively, you can rebind the shortcuts to your preferred keyboard shortcuts for each specific command. For more information on rebinding keyboard shortcuts in your JetBrains IDE, see the JetBrains documentation. For example, you can view the [IntelliJ IDEA](<https://www.jetbrains.com/help/idea/mastering-keyboard-shortcuts.html#choose-keymap>) documentation.

## Configuring advanced settings for {% data variables.product.prodname_copilot %}

You can manage advanced settings for {% data variables.product.prodname_copilot %} in your JetBrains IDE, such as how your IDE displays inline suggestions, and which languages you want to enable or disable for {% data variables.product.prodname_copilot %}.

{% data reusables.copilot.jetbrains-settings %}
{% data reusables.copilot.jetbrains-tools %}, and review the **General** and **Completions** settings.
1. Edit the settings according to your personal preferences.
   * To adjust the behavior and appearance of code suggestions, and whether to automatically check for updates, select or deselect the corresponding checkboxes.
   * If you have selected to receive automatic updates, you can choose whether to receive stable, but less frequent updates, or nightly updates, which may be less stable. Click the **Update channel** dropdown and select **Stable** for stable updates, or **Nightly** for nightly updates.

## Configuring language settings for {% data variables.product.prodname_copilot %}

You can specify which languages you want to activate or deactivate {% data variables.product.prodname_copilot %} for either in the IDE or by editing your `github-copilot.xml` file. If you make changes to language settings in your IDE, you can individually select and deselect the languages you want to activate or deactivate.

If you make changes to the language settings in your `github-copilot.xml` file, you can specify individual languages, or you can use a wildcard to activate or deactivate {% data variables.product.prodname_copilot %} for all languages. You can also specify exceptions, which will override the wild card setting for the specified languages. For example, you can deactivate {% data variables.product.prodname_copilot %} for all languages, except for Python and YAML. By default, when you install the {% data variables.product.prodname_copilot %} extension, {% data variables.product.prodname_copilot %} is activated for all languages.

### Configuring language settings in the IDE

{% data reusables.copilot.jetbrains-settings %}
{% data reusables.copilot.jetbrains-tools %}, then click **Completions**.
1. Under "Languages," select or deselect the checkboxes for the languages you want to activate or deactivate {% data variables.product.prodname_copilot %} for.
1. Click **Apply**, and then click **OK**.
1. Restart your JetBrains IDE for the changes to take effect.

### Editing your `github-copilot.xml` file

To configure language settings in the `github-copilot.xml` file, you must edit the `languageAllowList`. Every line you add to the `languageAllowList` must contain an entry key and a value. The entry key is the name of the language, or (`*`) for a wildcard. The value is either `true` or `false`. If the value is `true`, {% data variables.product.prodname_copilot %} is activated for the specified language. If the value is `false`, {% data variables.product.prodname_copilot %} is deactivated for the specified language.

The file is located in the following directory:

* **macOS:** `~/Library/Application Support/JetBrains/<product><version>/options/github-copilot.xml`
* **Windows:** `%APPDATA%\JetBrains\<product><version>\options\github-copilot.xml`
* **Linux:** `~/.config/JetBrains/<product><version>/options/github-copilot.xml`

For example, if you are using IntelliJ IDEA 2021.1 on macOS, the file is located at `~/Library/Application Support/JetBrains/IdeaIC2021.1/options/github-copilot.xml`.

The `github-copilot.xml` file might not be generated until you make a change to your default language configuration in the IDE's settings. If you cannot locate the file, you should try modifying the default language settings in the IDE. For more information, see [Configuring language settings in the IDE](<#configuring-language-settings-in-the-ide>).

Alternatively, you can create the file manually and save it in the location for your operating system listed above. For more information, see [Example language configurations](<#example-language-configurations>).

1. Open the `github-copilot.xml` file in a text editor.
1. Between the `<map>` tags, add the line or lines for the languages you want to activate or deactivate {% data variables.product.prodname_copilot %} for. For example, to deactivate {% data variables.product.prodname_copilot %} for all languages:

    ```xml copy
    <entry key="*" value="false" />
    ```

1. Save the changes to the `github-copilot.xml` file.
1. Restart your JetBrains IDE for the changes to take effect.

### Example language configurations

The default configuration of the `github-copilot.xml` file, which enables {% data variables.product.prodname_copilot %} for all languages is as follows:

```xml copy
<application>
  <component name="github-copilot">
    <languageAllowList>
      <map>
        <entry key="*" value="true" />
      </map>
    </languageAllowList>
  </component>
</application>
```

To deactivate {% data variables.product.prodname_copilot %} for all languages, the wildcard (`*`) value is changed to `false`:

```xml copy
<application>
  <component name="github-copilot">
    <languageAllowList>
      <map>
        <entry key="*" value="false" />
      </map>
    </languageAllowList>
  </component>
</application>
```

To specify languages individually, add an entry for each language you want to activate or deactivate {% data variables.product.prodname_copilot %} for. Specific language settings will override the wildcard. For example, to activate {% data variables.product.prodname_copilot %} for Python and YAML, and deactivate {% data variables.product.prodname_copilot %} for all other languages, add the following entries:

```xml copy
<application>
  <component name="github-copilot">
    <languageAllowList>
      <map>
        <entry key="*" value="false" />
        <entry key="Python" value="true" />
        <entry key="YAML" value="true" />
      </map>
    </languageAllowList>
  </component>
</application>
```

You can also add a configuration to make the `languageAllowList` readonly in the IDE's settings. This will prevent you from changing the language settings in the IDE. For example:

```xml copy
<application>
  <component name="github-copilot">
    <option name="languageAllowListReadOnly" value="true" />
    <languageAllowList>
      <map>
        <entry key="*" value="true" />
      </map>
    </languageAllowList>
  </component>
</application>
```

{% data reusables.copilot.dotcom-settings %}

## Further reading

* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot/#faq>)

{% endjetbrains %}

{% visualstudio %}

## About {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vs %}

If you use {% data variables.product.prodname_vs %}, {% data variables.product.prodname_copilot %} can help you with a variety of tasks, including generating code suggestions, explaining how the code in your editor works, and suggesting code fixes. After installation, you can enable or disable {% data variables.product.prodname_copilot %}, and you can configure advanced settings within {% data variables.product.prodname_vs %} or on {% data variables.product.github %}.

## Prerequisites

To configure {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vs %}, you must install the {% data variables.product.prodname_copilot %} plugin. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=visualstudio>).

## Rebinding keyboard shortcuts

You can use the default keyboard shortcuts for inline suggestions in {% data variables.product.prodname_vs %} when using {% data variables.product.prodname_copilot %}. For a list of default keyboard shortcuts, see [AUTOTITLE](<https://github.com/copilot/reference/keyboard-shortcuts>).

If you don't want to use the default keyboard shortcuts in {% data variables.product.prodname_vs %} when using {% data variables.product.prodname_copilot %}, you can rebind the shortcuts in the Keyboard editor using your preferred keyboard shortcuts for each specific command.

1. In the {% data variables.product.prodname_vs %} menu bar, under **Tools**, click **Options**.

   ![Screenshot of the {% data variables.product.prodname_vs %} menu bar. The "Tools" menu is expanded, and the "Options" item is highlighted with an orange outline.](<https://github.com/assets/images/help/copilot/vs-toolbar-options.png>)

1. In the "Options" dialog, under **Environment**, click **Keyboard**.
1. Under "Show commands containing:", search for the command you want to rebind.

   ![Screenshot of the "Show commands containing" search bar. The string "tools.next" is entered in the search field.](<https://github.com/assets/images/help/copilot/vs-show-commands-containing.png>)

1. Under "Press shortcut keys," type the shortcut you want to assign to the command, then click **Assign**.

   ![Screenshot of the fields for entering a new keyboard shortcut assignment.](<https://github.com/assets/images/help/copilot/vs-rebind-shortcut.png>)

{% data reusables.copilot.enabling-or-disabling-vs %}

## Configuring ReSharper for {% data variables.product.prodname_copilot %}

If you use ReSharper, {% data variables.product.prodname_copilot %} may work best when you configure ReSharper to use {% data variables.product.prodname_copilot %}'s native IntelliSense. For more information about ReSharper, see the [ReSharper documentation](<https://www.jetbrains.com/resharper/documentation/documentation.html>)

1. In the {% data variables.product.prodname_vs %} menu bar, under **Extensions**, click **ReSharper**, then click **Options**.
1. In the "Options" dialog, under **Environment**, click **IntelliSense** and then click **General**.
1. Under "General" select **{% data variables.product.prodname_vs %}** and then click **Save**.

{% data reusables.copilot.dotcom-settings %}

## Enabling {% data variables.copilot.next_edit_suggestions %}

To use {% data variables.copilot.next_edit_suggestions %} in {% data variables.product.prodname_vs %}, you need to enable the feature first.

1. In the {% data variables.product.prodname_vs %} menu bar, under **Tools**, click **Options**.
1. In the "Options" dialog, under **{% data variables.product.github %}**, click **{% data variables.product.prodname_copilot_short %}** and then click **{% data variables.product.prodname_copilot_short %} Completions**.
1. Check **Enable {% data variables.copilot.next_edit_suggestions %}**.

## Further reading

* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot/#faq>)

{% endvisualstudio %}

{% vscode %}

## About {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vscode %}

If you use {% data variables.product.prodname_vscode %}, {% data variables.product.prodname_copilot %} can help you with a variety of tasks, including generating code suggestions, explaining how the code in your editor works, and suggesting edits based on your instructions. You can enable or disable {% data variables.product.prodname_copilot %}, and configure advanced settings within {% data variables.product.prodname_vscode %} or on {% data variables.product.github %}.

You can learn more about scenarios and setup in the [{% data variables.product.prodname_vscode_shortname %} documentation](<https://code.visualstudio.com/docs/copilot/overview#_use-cases-for-github-copilot-in-vs-code>).

## Rebinding keyboard shortcuts

You can use the default keyboard shortcuts for inline suggestions in {% data variables.product.prodname_vscode_shortname %} when using {% data variables.product.prodname_copilot %}. Search keyboard shortcuts by command name in the Keyboard Shortcuts editor. For a list of default keyboard shortcuts, see [AUTOTITLE](<https://github.com/copilot/reference/keyboard-shortcuts>).

Alternatively, you can rebind the shortcut for each command in the Keyboard Shortcuts editor. For more information, see the [{% data variables.product.prodname_vscode %} documentation on editing shortcuts](<https://code.visualstudio.com/Docs/editor/keybindings>).

{% data reusables.copilot.enabling-or-disabling-in-vsc %}

## Enabling or disabling inline suggestions

You can choose to enable or disable inline suggestions for {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vscode %}.

{% data reusables.copilot.vscode-settings %}
1. In the left-side panel of the settings tab, click **Extensions** and then select **{% data variables.product.prodname_copilot_short %}**.
1. Under "Inline Suggest:Enable," select or deselect the checkbox to enable or disable inline suggestions.

## Enabling {% data variables.copilot.next_edit_suggestions %}

You can enable {% data variables.copilot.next_edit_suggestions %} via the {% data variables.product.prodname_vscode_shortname %} setting `github.copilot.nextEditSuggestions.enabled`. For more detailed instructions, see [Enabling edit suggestions](<https://code.visualstudio.com/docs/copilot/ai-powered-suggestions#_enabling-edit-suggestions>) in the {% data variables.product.prodname_vscode_shortname %} documentation.

{% data reusables.copilot.editor-preview-settings %}

## Enabling or disabling {% data variables.product.prodname_copilot %} for specific languages

You can specify which languages you want to enable or disable {% data variables.product.prodname_copilot %} for.

1. From the {% data variables.product.prodname_vscode %}, click the **Extensions** tab, then navigate to the **Copilot** section. For more information, see [Enabling or disabling inline suggestions](<#enabling-or-disabling-inline-suggestions>).
1. Under "Enable or disable {% data variables.product.prodname_copilot_short %} for specified languages," click **Edit in settings.json**.
1. In the _settings.json_ file, add or remove the languages you want to enable or disable {% data variables.product.prodname_copilot %} for. For example, to enable Python in {% data variables.product.prodname_copilot %}, add `"python": true` to the list, ensuring there is a trailing comma after all but the last list item.

   {% data reusables.copilot.vs-code-settings-json-ex %}

## Revoking {% data variables.product.prodname_copilot %} authorization

{% data variables.product.prodname_vscode %} retains authorization to use {% data variables.product.prodname_copilot %} through a particular {% data variables.product.prodname_dotcom %} account. If you want to prevent your {% data variables.product.prodname_dotcom %} account being used for {% data variables.product.prodname_copilot %} on a device you no longer have access to, you can revoke authorization and then go through the authorization process again. The device you previously used will not have the new authorization.

{% data reusables.user-settings.access_settings %}
{% data reusables.user-settings.access_applications %}
{% data reusables.user-settings.access_authorized_oauth_apps %}
1. Click the **...** next to **{% data variables.product.prodname_dotcom %} for {% data variables.product.prodname_vscode_shortname %}** and click **Revoke**.
{% data reusables.user-settings.access_authorized_github_apps %}
1. If the **{% data variables.product.prodname_copilot %}** extension is listed, click **Revoke**.

After revoking authorization, {% data variables.product.prodname_vscode %} will be able to continue using {% data variables.product.prodname_copilot %} in a current session for a maximum of 30 minutes. After that time, you will need to reauthorize {% data variables.product.prodname_copilot %} for use in {% data variables.product.prodname_vscode %} again.

## Re-authorizing {% data variables.product.prodname_copilot %}

After you have revoked authorization, if you want to continue using {% data variables.product.prodname_copilot %}, you will need to complete the reauthorization process.

1. In the bottom left corner of {% data variables.product.prodname_vscode %}, click the **Accounts** icon, hover over your username, and click **Sign out**.

   ![Screenshot of the menu in {% data variables.product.prodname_vscode %}. The "Sign out" option is outlined in dark orange.](<https://github.com/assets/images/help/copilot/vsc-sign-out.png>)

1. In the "{% data variables.product.prodname_vscode %}" pop-up, click **Sign Out**.

1. In the bottom left corner of {% data variables.product.prodname_vscode %}, click the **Accounts** icon, hover over your username, and click **Sign in with {% data variables.product.prodname_dotcom %} to use {% data variables.product.prodname_copilot %}**.

   ![Screenshot of the accounts menu in {% data variables.product.prodname_vscode %}. The "Sign in with {% data variables.product.prodname_dotcom %} to use {% data variables.product.prodname_copilot %} (1)" option is outlined in dark orange.](<https://github.com/assets/images/help/copilot/vsc-sign-in.png>)

1. In your browser, {% data variables.product.prodname_dotcom %} will request the necessary permissions for {% data variables.product.prodname_copilot %}. To approve these permissions, click **Continue**.
1. In the "Open {% data variables.product.prodname_vscode %}?" pop-up, click **Open {% data variables.product.prodname_vscode %}**.

{% data reusables.copilot.dotcom-settings %}

## Further reading

* [{% data variables.product.prodname_copilot %} in VS Code](<https://code.visualstudio.com/docs/copilot/overview>)
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot/#faq>)

{% endvscode %}

{% vimneovim %}

## Configuring {% data variables.product.prodname_copilot %} in Vim/Neovim

For guidance on configuring {% data variables.product.prodname_copilot %} in Vim/Neovim, invoke the {% data variables.product.prodname_copilot %} documentation in Vim/Neovim by running the following command:

    :help copilot

## Rebinding keyboard shortcuts

You can rebind the keyboard shortcuts in Vim/Neovim when using {% data variables.product.prodname_copilot %} to use your preferred keyboard shortcuts for each specific command. For more information, see the [Map](<https://neovim.io/doc/user/map.html>) article in the Neovim documentation.

{% data reusables.copilot.dotcom-settings %}

## Further reading

* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot/#faq>)

{% endvimneovim %}

{% xcode %}

## About {% data variables.product.prodname_copilot %} in Xcode

If you use Xcode, {% data variables.product.prodname_copilot %} can help you with a variety of tasks, including generating code suggestions, explaining how the code in your editor works, and suggesting code fixes. After installation, you can enable or disable {% data variables.product.prodname_copilot %}, and you can configure advanced settings within Xcode or on {% data variables.product.github %}.

## Prerequisites

To configure {% data variables.product.prodname_copilot %} for Xcode, you must install the {% data variables.product.prodname_copilot %} extension. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=xcode>).

## Rebinding keyboard shortcuts

You can use the default keyboard shortcuts for inline suggestions in Xcode when using {% data variables.product.prodname_copilot %}. For a list of default keyboard shortcuts, see [AUTOTITLE](<https://github.com/copilot/reference/keyboard-shortcuts>).

If you don't want to use the default keyboard shortcuts for {% data variables.product.prodname_copilot %}, you can rebind the shortcuts in the Key Bindings editor and use your preferred keyboard shortcuts.

If you want to use something besides <kbd>Tab</kbd> to accept the first line of a suggestion, you need to disable the "Accept suggestions with Tab" option in the advanced settings in the {% data variables.product.prodname_copilot %} for Xcode application. Additionally, we currently only support the <kbd>Option</kbd> key for the "View full suggestion" action.

1. In the Xcode menu, click **Xcode** then **Settings**.
1. Click **Key Bindings** and search for "{% data variables.product.prodname_copilot_short %}" to find the commands you want to rebind.

## Enabling or disabling {% data variables.product.prodname_copilot %}

You can enable or disable the {% data variables.product.prodname_copilot %} extension from within the application.

1. Open the {% data variables.product.prodname_copilot %} for Xcode application.
1. At the top of the application window, click **Advanced**.
1. In the "Suggestion Settings" section, use the "Request suggestions while typing" toggle to enable or disable the extension.

## Automatically updating {% data variables.product.prodname_copilot %} for Xcode

You can configure the {% data variables.product.prodname_copilot %} extension to automatically check for updates.

1. Open the {% data variables.product.prodname_copilot %} for Xcode application.
1. Select **Automatically check for updates**.

After updating the extension, Xcode must be restarted for the changes to take effect.

## Disabling {% data variables.copilot.next_edit_suggestions %}

{% data variables.copilot.next_edit_suggestions_caps %} are enabled by default. To disable {% data variables.copilot.next_edit_suggestions %}, go to the "Advanced" tab in the {% data variables.product.prodname_copilot %} for Xcode extension settings. You can also choose to disable the option to "Accept suggestions with Tab".

{% data reusables.copilot.editor-preview-settings %}

{% data reusables.copilot.dotcom-settings %}

{% endxcode %}

{% eclipse %}

## About {% data variables.product.prodname_copilot %} in Eclipse

If you use Eclipse, {% data variables.product.prodname_copilot %} can provide code suggestions as you work in the IDE. You can also use the {% data variables.copilot.copilot_chat_short %} panel to work with {% data variables.product.prodname_copilot_short %} as your AI pair programmer.

After you install {% data variables.product.prodname_copilot %} in Eclipse, you can enable or disable it, and you can configure advanced settings within the IDE.

## Prerequisites

To configure {% data variables.product.prodname_copilot %} in Eclipse, you must install the {% data variables.product.prodname_copilot %} extension. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=eclipse>).

## Rebinding keyboard shortcuts

If you don't want to use the default keyboard shortcuts for {% data variables.product.prodname_copilot %}, you can rebind the shortcuts in the Key Bindings editor and use your preferred keyboard shortcuts. For a list of default keyboard shortcuts, see [AUTOTITLE](<https://github.com/copilot/reference/keyboard-shortcuts>).

1. In the IDE, click **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}** to open the menu.
1. Click **Edit Keyboard Shortcuts...** to rebind the shortcuts.

## Settings and configurations

For advanced settings, you can set auto-completion behavior, configure proxy, and assign a {% data variables.product.prodname_enterprise %} authentication endpoint.

## Enabling {% data variables.copilot.next_edit_suggestions %}

You can enable {% data variables.copilot.next_edit_suggestions %} under "Completions" in the {% data variables.product.prodname_copilot %} extension settings in Eclipse.

{% data reusables.copilot.editor-preview-settings %}

{% data reusables.copilot.dotcom-settings %}

{% endeclipse %}


</details>
