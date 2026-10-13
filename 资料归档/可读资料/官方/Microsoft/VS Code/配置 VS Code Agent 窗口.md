# 配置 VS Code Agent 窗口

说明共享账号与模型配置、窗口布局、Markdown 阅读、扩展和聊天界面设置，帮助调整任务管理工作区。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/agents-window-configuration.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/agents-window-configuration.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/配置 VS Code Agent 窗口.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 8b38ebbb-831f-43ba-8285-93846b7cb135
DateApproved: 10/7/2026
MetaDescription: Configure AI providers, accounts, layout, settings, extensions, and editors in the {% data variables.copilot.agents_window %}.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 配置{% data variables.copilot.agents_window %}

{% data variables.copilot.agents_window %} 使用 {% data variables.product.prodname_vscode_shortname %} 中配置的 AI 提供商和帐户。它与主 {% data variables.product.prodname_vscode_shortname %} 窗口共享设置和默认配置文件。本文介绍共享帐户选项以及特定于窗口的布局、编辑器、设置和扩展配置。

有关启动和使用会话的说明，请参阅[使用{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>)。

## 管理 AI 提供商和帐户

{% data variables.copilot.agents_window %} 没有单独的提供程序配置。它使用 {% data variables.product.prodname_vscode_shortname %} 中提供的帐户和模型凭据：

* **GitHub Copilot**：选择右上角的账户图标，然后登录GitHub。要切换帐户，请注销，然后使用不同的 GitHub 帐户进行身份验证。
* **Claude**：对 Claude 使用 Claude API 密钥或其他受支持的自带密钥 (BYOK) 选项。
* **Codex**：选择帐户图标，然后选择**登录ChatGPT**。
* **自带密钥 (BYOK)**：在语言模型编辑器中添加模型并启用 `setting(chat.agentHost.byokModels.enabled)` 以使其可用于 Agent 主机会话。了解如何[配置BYOK模型](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>)。

有关完整的身份验证、计费和功能信息，请参阅[配置线束或云目标](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>)。

Claude、ChatGPT 支持的 Codex 和 BYOK 模型可以在桌面 {% data variables.copilot.agents_window %} 中运行，无需 GitHub 登录。此注销体验是实验性的，需要 `setting(chat.agentHost.allowSignedOutWhenUsable)`。需要 GitHub 身份验证的功能会在需要时提示您登录。基于浏览器的 {% data variables.copilot.agents_window %} 始终需要 GitHub 登录。

## 配置 {% data variables.copilot.agents_window %} 的设置

{% data variables.copilot.agents_window %} 共享您的所有 {% data variables.product.prodname_vscode_shortname %} 设置。要在 {% data variables.copilot.agents_window %} 和编辑器窗口中使用不同的行为，请覆盖 {% data variables.copilot.agents_window %} 的各个设置，而不影响主 {% data variables.product.prodname_vscode_shortname %} 设置。

要仅覆盖 {% data variables.copilot.agents_window %} 的设置，请编辑您的设置文件并确定 {% data variables.copilot.agents_window %} 部分下的值范围。从 {% data variables.copilot.agents_window %} 打开设置编辑器 (`kb(workbench.action.openSettings)`) 以查看设置适用的范围。

![显示在 {% data variables.copilot.agents_window %} 中打开的设置编辑器的屏幕截图，其中突出显示了不同的设置范围。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-settings.png>)

### 为代码编辑器配置自动换行

`feature(agents-window-word-wrap)`

控制 {% data variables.copilot.agents_window %} 中的代码编辑器如何使用 `setting(sessions.editor.wordWrap)` 换行。此设置具有以下值：

* `inherit`（默认）：遵循`setting(editor.wordWrap)`设置。
* `on`：以编辑器视口宽度换行。
* `off`：切勿换行。

要从代码编辑器更改此设置，请选择 **更多操作 ** (**...**) > **自动换行**。要临时覆盖当前文件的自动换行，请使用 `kb(editor.action.toggleWordWrap)`。此键盘快捷键不会更改 `setting(sessions.editor.wordWrap)`。

此设置不会影响 **Changes** 视图中的差异编辑器。要在这些编辑器中配置自动换行，请参阅[在 diff 编辑器中配置自动换行](<https://github.com/docs/agents/run/review-code-edits.md#configure-word-wrap-in-diff-editors>)。

## 配置新会话体验

### 个性化欢迎标题（实验）

启用 `setting(sessions.chat.experimental.welcomePhrases)` 以在 {% data variables.copilot.agents_window %} 中的新会话编辑器上方显示旋转欢迎短语。

要个性化标题，请选择铅笔图标 (**自定义欢迎消息...**)，然后选择以下选项之一：

* **名称**：设置欢迎短语中使用的名称。清除名称以使用您的 GitHub 个人资料中的名字（如果可用），或者当您的个人资料不包含该名称时省略该名称。您也可以使用 `setting(sessions.chat.experimental.welcomeName)` 设置名称。该名称在设备之间同步。
* **短语**：打开`setting(sessions.chat.experimental.welcomeMessages)`添加自定义短语或替换默认短语。

以下示例将两个自定义短语添加到默认短语中：

```json
"sessions.chat.experimental.welcomeMessages": {
    "mode": "append",
    "phrases": ["Ready, {name}?", "Let's ship it"]
}
```

将 `mode` 设置为 `append` 以使用您的短语和默认短语，或将其设置为 `replace` 以仅使用您的短语。在短语中的任意位置使用 `{name}` 插入配置的或 GitHub 配置文件名称。如果没有可用的名称，{% data variables.product.prodname_vscode_shortname %} 将删除占位符及其相邻的分隔符。

要隐藏标题，请右键单击标题并选择**隐藏欢迎消息**。此操作会关闭 `setting(sessions.chat.experimental.welcomePhrases)`。重新启用设置以再次显示标题。

当屏幕阅读器优化模式处于活动状态时，当作曲家出现时，标题会被宣布一次。关闭`setting(accessibility.verbosity.newSessionWelcome)`以忽略公告。

### 对作曲家控件进行分组（实验）

启用 `setting(sessions.chat.experimental.newSessionComposerLayout)` 将工作区、分支、工作树和线束控件分组到新会话聊天输入上方。此布局需要 `setting(sessions.chat.unifiedWorkspacePicker.enabled)`。

启用统一工作区选取器后，使用 `kb(sessions.focusNewSessionWorkspacePicker)` 聚焦工作区选取器，或使用 `kb(sessions.focusNewSessionHarnessPicker)` 聚焦线束选取器。这些命令不需要实验性的 Composer 布局。要分配键绑定，请使用选取器上下文菜单中的 **配置键绑定 ** 操作。

## 调整窗口布局

### 使用单窗格编辑器面板（实验）

实验性的单窗格布局用一个停靠窗格取代了单独的编辑器和侧面板。共享选项卡栏跨越编辑器和 **更改** 或 **文件** 详细视图。文件和差异在聊天旁边的停靠编辑器中打开，而不是在模式窗口中打开。

要使用单窗格布局，请启用 `setting(sessions.layout.singlePaneDetailPanel)` 并重新加载窗口。 {% data variables.copilot.agents_window %} 启动时读取该设置。

<!-- TODO: Add a screenshot of the single-pane editor panel showing the shared tab bar, editor, and detail panel. -->

在共享标签栏中，选择**新标签**（`+`）打开**更改**、**文件**、 **浏览器**，或**搜索**。使用**隐藏编辑器**、**切换详细信息**和**最大化编辑器区域**或**恢复编辑器区域**来调整布局。 **切换详细信息**仅适用于支持详细信息面板的选项卡。

当您切换会话或重新加载窗口时，每个会话都会恢复其侧窗格宽度、打开的编辑器、活动编辑器和每个文件的折叠状态。

### 自动折叠会话侧边栏（实验）

当您启用 `setting(sessions.layout.autoCollapseSessionsSidebar)` 时，当编辑器区域和侧面板都打开时，{% data variables.copilot.agents_window %} 会隐藏狭窄窗口中的会话侧边栏。当有空间时，侧边栏会再次出现。 {% data variables.copilot.agents_window %} 保留您手动关闭的侧边栏，并在多个会话并排打开时暂停自动折叠。

## 查看和编辑 Markdown 文件

{% data variables.copilot.agents_window %} 支持渲染的 Markdown 预览和 `.md` 文件的实验性 Markdown 编辑器。默认打开哪个编辑器取决于`setting(workbench.editor.markdownDefaultEditorInAgentsWindow)`。

* 启用后，`.md` 文件将使用 **Markdown 编辑器（实验）** 打开。
* 禁用后，`.md` 文件将使用 **Markdown 预览 ** 打开。

在 **Markdown 编辑器（实验）** 中，在 ** 编辑 ** 和 ** 锁定 ** 模式之间切换，同时保留渲染的 Markdown 上下文。在**编辑**模式下，直接编辑内容。在 **锁定** 模式下，文档保持呈现且只读状态。

当您编辑 Markdown 文件时，编辑器会在页边空白处显示 Git 更改标记。绿色表示新增内容，蓝色表示修改内容，红色表示删除内容。这些标记反映了当前的 Git 更改，并在您撤消或恢复相应更改时消失。

## 在 {% data variables.copilot.agents_window %} 中使用 {% data variables.product.prodname_vscode_shortname %} 扩展

{% data variables.copilot.agents_window %}可以运行{% data variables.product.prodname_vscode_shortname %}扩展。仅提供静态内容（例如主题、语法、语言和键绑定）的扩展会自动激活。

对于其他扩展，请通过 ID 和 `setting(extensions.supportAgentsWindow)` 设置选择它们：

```json
"extensions.supportAgentsWindow": {
    "myextension.id": true
}
```

该扩展必须安装在您的默认 {% data variables.product.prodname_vscode_shortname %} 配置文件中。扩展支持仍在不断发展。如果扩展程序未按预期运行，请[提交问题](<https://github.com/microsoft/vscode/issues>)。

## 个性化聊天

添加装饰性聊天背景，使用交互式 VS Code 宠物，或调整聊天内容的显示方式。了解如何[个性化聊天](<https://github.com/docs/chat/chat-overview.md#personalize-chat>)。

## 后续步骤

* [选择 agent 工具 ](<https://github.com/docs/agents/run/agent-harnesses.md>) - 比较提供程序、执行环境、权限和隔离。
* [使用 {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) - 跨工作区启动、监控、检查和完成 agent 会话。
* [AI设置参考](<https://github.com/docs/agents/reference/ai-settings.md>) - 查看agents和聊天的设置。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Configure the {% data variables.copilot.agents_window %}

The {% data variables.copilot.agents_window %} uses the AI providers and accounts configured in {% data variables.product.prodname_vscode_shortname %}. It shares settings and the default profile with the main {% data variables.product.prodname_vscode_shortname %} window. This article describes the shared account options and the window-specific layout, editor, setting, and extension configuration.

For instructions about starting and working with sessions, see [Use the {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>).

## Manage AI providers and accounts

The {% data variables.copilot.agents_window %} doesn't have separate provider configuration. It uses the accounts and model credentials available in {% data variables.product.prodname_vscode_shortname %}:

* **GitHub Copilot**: select the account icon in the top-right corner, and then sign in to GitHub. To switch accounts, sign out and then authenticate with a different GitHub account.
* **Claude**: use a Claude API key or another supported bring-your-own-key (BYOK) option for Claude.
* **Codex**: select the account icon, and then select **Sign in to ChatGPT**.
* **Bring your own key (BYOK)**: add a model in the Language Models editor and enable `setting(chat.agentHost.byokModels.enabled)` to make it available to Agent Host sessions. Learn how to [configure BYOK models](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>).

For complete authentication, billing, and capability information, see [Configure a harness or Cloud target](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>).

Claude, ChatGPT-backed Codex, and BYOK models can run in the desktop {% data variables.copilot.agents_window %} without GitHub sign-in. This signed-out experience is experimental and requires `setting(chat.agentHost.allowSignedOutWhenUsable)`. Features that require GitHub authentication prompt you to sign in when needed. The browser-based {% data variables.copilot.agents_window %} always requires GitHub sign-in.

## Configure settings for the {% data variables.copilot.agents_window %}

The {% data variables.copilot.agents_window %} shares all of your {% data variables.product.prodname_vscode_shortname %} settings. To use different behavior in the {% data variables.copilot.agents_window %} and the editor window, override individual settings for the {% data variables.copilot.agents_window %} without affecting the main {% data variables.product.prodname_vscode_shortname %} setup.

To override a setting for the {% data variables.copilot.agents_window %} only, edit your settings file and scope the value under the {% data variables.copilot.agents_window %} section. Open the Settings editor (`kb(workbench.action.openSettings)`) from the {% data variables.copilot.agents_window %} to see which scope a setting applies to.

![Screenshot showing the Settings editor open in the {% data variables.copilot.agents_window %}, with the different scopes for settings highlighted.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-settings.png>)

### Configure word wrap for code editors

`feature(agents-window-word-wrap)`

Control how code editors in the {% data variables.copilot.agents_window %} wrap long lines with `setting(sessions.editor.wordWrap)`. This setting has the following values:

* `inherit` (default): Follow the `setting(editor.wordWrap)` setting.
* `on`: Wrap lines at the editor viewport width.
* `off`: Never wrap lines.

To change this setting from a code editor, select **More Actions** (**...**) > **Word Wrap**. To temporarily override word wrapping for the current file, use `kb(editor.action.toggleWordWrap)`. This keyboard shortcut doesn't change `setting(sessions.editor.wordWrap)`.

This setting doesn't affect diff editors in the **Changes** view. To configure word wrapping in those editors, see [Configure word wrap in diff editors](<https://github.com/docs/agents/run/review-code-edits.md#configure-word-wrap-in-diff-editors>).

## Configure the new-session experience

### Personalize the welcome heading (Experimental)

Enable `setting(sessions.chat.experimental.welcomePhrases)` to show a rotating welcome phrase above the new-session composer in the {% data variables.copilot.agents_window %}.

To personalize the heading, select the pencil icon (**Customize Welcome Message...**), and then choose one of the following options:

* **Name**: Set the name used in welcome phrases. Clear the name to use the first name from your GitHub profile when available, or omit the name when your profile doesn't include one. You can also set the name with `setting(sessions.chat.experimental.welcomeName)`. The name syncs across devices.
* **Phrases**: Open `setting(sessions.chat.experimental.welcomeMessages)` to add custom phrases or replace the default phrases.

The following example adds two custom phrases to the default phrases:

```json
"sessions.chat.experimental.welcomeMessages": {
    "mode": "append",
    "phrases": ["Ready, {name}?", "Let's ship it"]
}
```

Set `mode` to `append` to use your phrases and the default phrases, or set it to `replace` to use only your phrases. Use `{name}` anywhere in a phrase to insert the configured or GitHub profile name. If no name is available, {% data variables.product.prodname_vscode_shortname %} removes the placeholder and its adjoining separator.

To hide the heading, right-click it and select **Hide Welcome Message**. This action turns off `setting(sessions.chat.experimental.welcomePhrases)`. Re-enable the setting to show the heading again.

When screen reader optimized mode is active, the heading is announced once when the composer appears. Turn off `setting(accessibility.verbosity.newSessionWelcome)` to omit the announcement.

### Group the composer controls (Experimental)

Enable `setting(sessions.chat.experimental.newSessionComposerLayout)` to group the workspace, branch, worktree, and harness controls above the new-session chat input. This layout requires `setting(sessions.chat.unifiedWorkspacePicker.enabled)`.

With the unified workspace picker enabled, use `kb(sessions.focusNewSessionWorkspacePicker)` to focus the workspace picker or `kb(sessions.focusNewSessionHarnessPicker)` to focus the harness picker. These commands don't require the experimental composer layout. To assign a keybinding, use the **Configure Keybinding** action in the picker's context menu.

## Adjust the window layout

### Use the single-pane editor panel (Experimental)

The experimental single-pane layout replaces the separate editor and side panel with one docked pane. A shared tab bar spans the editor and the **Changes** or **Files** detail view. Files and diffs open in the docked editor next to the chat instead of in a modal window.

To use the single-pane layout, enable `setting(sessions.layout.singlePaneDetailPanel)` and reload the window. The setting is read when the {% data variables.copilot.agents_window %} starts.

<!-- TODO: Add a screenshot of the single-pane editor panel showing the shared tab bar, editor, and detail panel. -->

In the shared tab bar, select **New Tab** (`+`) to open **Changes**, **Files**, **Browser**, or **Search**. Use **Hide Editor**, **Toggle Details**, and **Maximize Editor Area** or **Restore Editor Area** to adjust the layout. **Toggle Details** is available only for tabs that support a detail panel.

Each session restores its side-pane width, open editors, active editor, and per-file collapsed state when you switch sessions or reload the window.

### Automatically collapse the sessions sidebar (Experimental)

When you enable `setting(sessions.layout.autoCollapseSessionsSidebar)`, the {% data variables.copilot.agents_window %} hides the sessions sidebar on narrow windows when both the editor area and side panel are open. The sidebar appears again when there is room. The {% data variables.copilot.agents_window %} preserves a sidebar that you closed manually and suspends auto-collapse while multiple sessions are open side by side.

## View and edit Markdown files

The {% data variables.copilot.agents_window %} supports rendered Markdown preview and an experimental Markdown editor for `.md` files. Which editor opens by default depends on `setting(workbench.editor.markdownDefaultEditorInAgentsWindow)`.

* When enabled, `.md` files open with **Markdown Editor (Experimental)**.
* When disabled, `.md` files open with **Markdown Preview**.

In **Markdown Editor (Experimental)**, switch between **Editing** and **Locked** modes while keeping the rendered Markdown context. In **Editing** mode, edit content directly. In **Locked** mode, the document remains rendered and read-only.

When you edit a Markdown file, the editor shows Git change markers in the margin. Green indicates added content, blue indicates modified content, and red indicates deleted content. The markers reflect the current Git changes and disappear when you undo or revert the corresponding changes.

## Use {% data variables.product.prodname_vscode_shortname %} extensions in the {% data variables.copilot.agents_window %}

The {% data variables.copilot.agents_window %} can run {% data variables.product.prodname_vscode_shortname %} extensions. Extensions that contribute only static content, such as themes, grammars, languages, and keybindings, activate automatically.

For other extensions, opt them in by ID with the `setting(extensions.supportAgentsWindow)` setting:

```json
"extensions.supportAgentsWindow": {
    "myextension.id": true
}
```

The extension must be installed in your default {% data variables.product.prodname_vscode_shortname %} profile. Extension support is still evolving. If an extension doesn't behave as expected, [file an issue](<https://github.com/microsoft/vscode/issues>).

## Personalize chat

Add a decorative chat background, use the interactive VS Code pet, or adjust how chat content appears. Learn how to [personalize chat](<https://github.com/docs/chat/chat-overview.md#personalize-chat>).

## Next steps

* [Choose an agent harness](<https://github.com/docs/agents/run/agent-harnesses.md>) - compare providers, execution environments, permissions, and isolation.
* [Use the {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) - start, monitor, review, and finish agent sessions across workspaces.
* [AI settings reference](<https://github.com/docs/agents/reference/ai-settings.md>) - review settings for agents and chat.


</details>
