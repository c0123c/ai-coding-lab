# 在 VS Code 会话中使用产物面板

说明截图、计划和文档等资源的展示、来源、提取规则与移除方式，帮助在聊天旁查看开发成果。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/artifacts.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/artifacts.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/在 VS Code 会话中使用产物面板.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: a4e7b2c1-3d5f-4a8e-b9c6-1e2d3f4a5b6c
DateApproved: 10/7/2026
FeatureStatus: agent-artifacts
MetaDescription: View and manage artifacts, references, screenshots, plans, and other agent session resources in {% data variables.product.prodname_vscode %}.
MetaSocialImage: ../../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 在 agent 会话中使用工件

{% data variables.product.prodname_vscode %} 中的工件面板显示了重要的资源，例如屏幕截图、计划和文档以及聊天对话。工件可以同时来自多个源，并显示在可折叠的树控件中，并按来源分组。

> [！NOTE]
> 此页面上的功能适用于[{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>)和[{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>)。

> [!NOTE]
> 要启用工件面板，请将 `setting(chat.artifacts.enabled)` 设置为 `true`。

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Try artifacts in action">
启动聊天提示，要求agent将屏幕截图保存为工件。

* [打开于{% data variables.product.prodname_vscode_shortname %}](<vscode://GitHub.Copilot-Chat/chat?agent=agent%26prompt=Take%20a%20screenshot%20of%20my%20app%20and%20set%20it%20as%20an%20artifact.>)

</div>

![{% data variables.copilot.chat_view %} 中的工件面板的屏幕截图，显示链接、图像和文档等示例工件。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-artifacts/chat-artifacts-list.png>)

## 工件和参考

Agent 会话可以在聊天输入旁边显示工件和参考。工件代表会议所处理的显着结果或资源，而参考则提供会议仅参考的支持信息。

对于 GitHub 问题和拉取请求，当会话处理某个项目时，该项目就是一个工件，即使该项目在会话开始之前就已存在。会话仅用于提供信息的问题或拉取请求是参考。

## 工件来源

工件可以出现在来自不同来源的面板中，这些来源按来源合并和分组：

* **基于规则的提取**：{% data variables.product.prodname_vscode_shortname %}根据可配置的MIME类型模式、文件路径全局或内存文件路径自动从对话内容中提取工件。例如，内置规则显示屏幕截图和计划文档。有关详细信息，请参阅[配置工件提取规则](<#配置工件提取规则>)。
* **Agent-设置工件**：agent使用`#artifacts`工具显式设置工件。每次更新都会替换之前的 agent 工件列表。
* **子代理工件**：当agent将工作委托给子代理时，每个子代理可以设置自己的工件，显示在标有子代理名称的单独组中。

## 配置工件提取规则

基于规则的提取使用三个设置，每个设置将模式映射到组配置。组配置有一个 `groupName`（显示标签）和一个可选的 `onlyShowGroup` 标志（当 `true` 时，仅显示组标题而不显示单个项目）。

|设置|描述|默认|
|---------|-------------|---------|
| `setting(chat.artifacts.rules.byMimeType)` |按 MIME 类型模式匹配工具结果，例如 `"image/*"`。| `{ "image/*": { "groupName": "Screenshots", "onlyShowGroup": true } }` |
| `setting(chat.artifacts.rules.byFilePath)` |按 glob 模式匹配工作区文件，例如 `"**/*plan*.md"`。| `{ "**/*plan*.md": { "groupName": "Plans" } }` |
| `setting(chat.artifacts.rules.byMemoryFilePath)` |匹配内存工具按 glob 模式写入，例如 `"**/*plan*.md"`。| `{ "**/*plan*.md": { "groupName": "Plans" } }` |

例如，要添加将 agent 写入的 HTML 文件显示为工件的规则，请将以下内容添加到您的设置中：

```json
"chat.artifacts.rules.byFilePath": {
    "**/*.html": { "groupName": "Web Pages" }
}
```

要完全禁用基于规则的提取，请将所有三个规则设置设置为 `{}`。

## 使用工件

当工件可用时，工件面板会出现在对话旁边。工件根据其来源分组。

* 选择一个工件以打开相应的资源。例如，选择图像工件会显示预览，而选择文档工件会在编辑器中打开文件。
* 每次 agent 更新时，agent 中的工件都会替换以前的 agent 列表。
* 每个子代理的工件都显示在单独的组中。您可以独立清除特定子代理的工件。
* 随着对话的进展，基于规则的工件会自动更新。如果更改规则设置，工件面板会反映更新的规则。

## 删除工件或参考

要删除单个录制项目，请将鼠标悬停在聊天输入旁边的项目上或将其聚焦，然后选择其删除操作。如果一个组包含多个项目，请先选择该组以打开列表。当您重新加载或重新打开会话时，删除仍然存在。

删除项目只会从当前会话中删除其记录。它不会删除文件或网站、恢复文件更改或修改问题或拉取请求。

并非所有显示的项目都是可移除的。仅当会话提供程序支持删除并且录制项目具有稳定的 ID 时，删除操作才可用。 {% data variables.product.prodname_vscode_shortname %} 独立发现的问题或拉取请求关联可能在您删除记录的项目后仍然可见或重新出现。此操作无法删除派生文件更改和独立发现的关联。

## 要求 agent 创建工件

您可以通过在提示中描述您想要的内容来要求 agent 将某些内容存储为工件。例如：

* “截取我的应用程序的屏幕截图并将其设置为神器”
* “写一个项目计划并将其设置为工件”
* “将这些链接保存为工件”

然后，agent 使用 `#artifacts` 工具将资源添加到工件面板。您不需要直接引用该工具。只需描述您想要保留的内容，agent 就会决定何时将其作为工件显示。

agent还可以使用`#artifactRules`工具覆盖当前会话的提取规则。这将取代会话期间任何基于设置的规则。

## 相关资源

* [聊天概述](<https://github.com/docs/chat/chat-overview.md>)
* [Agent 工具](<https://github.com/docs/agents/run/tools.md>)
* [AI设置参考](<https://github.com/docs/agents/reference/ai-settings.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Use artifacts in agent sessions

The artifacts panel in {% data variables.product.prodname_vscode %} surfaces important resources, such as screenshots, plans, and documents, alongside the chat conversation. Artifacts can come from multiple sources simultaneously and are displayed in a collapsible tree control, grouped by origin.

> [!NOTE]
> The features on this page work in both the [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>) and the [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>).

> [!NOTE]
> To enable the artifacts panel, set `setting(chat.artifacts.enabled)` to `true`.

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Try artifacts in action">
Launch a chat prompt that asks the agent to save a screenshot as an artifact.

* [Open in {% data variables.product.prodname_vscode_shortname %}](<vscode://GitHub.Copilot-Chat/chat?agent=agent%26prompt=Take%20a%20screenshot%20of%20my%20app%20and%20set%20it%20as%20an%20artifact.>)

</div>

![Screenshot of the artifacts panel in the {% data variables.copilot.chat_view %}, showing example artifacts such as links, images, and documents.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-artifacts/chat-artifacts-list.png>)

## Artifacts and references

Agent sessions can surface both artifacts and references next to the chat input. Artifacts represent notable results or resources the session works on, while references provide supporting information that the session only consults.

For GitHub issues and pull requests, an item is an artifact when the session works on it, even if the item existed before the session started. An issue or pull request that the session only uses for information is a reference.

## Artifact sources

Artifacts can appear in the panel from different sources, which are merged and grouped by origin:

* **Rules-based extraction**: {% data variables.product.prodname_vscode_shortname %} automatically extracts artifacts from conversation content based on configurable MIME type patterns, file path globs, or memory file paths. For example, the built-in rules surface screenshots and plan documents. See [Configure artifact extraction rules](<#configure-artifact-extraction-rules>) for details.
* **Agent-set artifacts**: the agent explicitly sets artifacts by using the `#artifacts` tool. Each update replaces the previous agent artifact list.
* **Subagent artifacts**: when the agent delegates work to subagents, each subagent can set its own artifacts, shown in separate groups labeled with the subagent's name.

## Configure artifact extraction rules

Rules-based extraction uses three settings, each mapping a pattern to a group configuration. The group configuration has a `groupName` (display label) and an optional `onlyShowGroup` flag (when `true`, only the group header is shown instead of individual items).

| Setting | Description | Default |
|---------|-------------|---------|
| `setting(chat.artifacts.rules.byMimeType)` | Match tool results by MIME type pattern, such as `"image/*"`. | `{ "image/*": { "groupName": "Screenshots", "onlyShowGroup": true } }` |
| `setting(chat.artifacts.rules.byFilePath)` | Match workspace files by glob pattern, such as `"**/*plan*.md"`. | `{ "**/*plan*.md": { "groupName": "Plans" } }` |
| `setting(chat.artifacts.rules.byMemoryFilePath)` | Match memory tool writes by glob pattern, such as `"**/*plan*.md"`. | `{ "**/*plan*.md": { "groupName": "Plans" } }` |

For example, to add a rule that surfaces HTML files written by the agent as artifacts, add the following to your settings:

```json
"chat.artifacts.rules.byFilePath": {
    "**/*.html": { "groupName": "Web Pages" }
}
```

To disable rules-based extraction entirely, set all three rule settings to `{}`.

## Use artifacts

When artifacts are available, the artifacts panel appears alongside the conversation. Artifacts are organized in groups based on their source.

* Select an artifact to open the corresponding resource. For example, selecting an image artifact shows a preview, while selecting a document artifact opens the file in the editor.
* Artifacts from the agent replace the previous agent list each time the agent updates them.
* Each subagent's artifacts are shown in a separate group. You can clear a specific subagent's artifacts independently.
* Rules-based artifacts update automatically as the conversation progresses. If you change the rule settings, the artifacts panel reflects the updated rules.

## Remove an artifact or reference

To remove an individual recorded item, hover over or focus the item next to the chat input, and then select its remove action. If a group contains multiple items, first select the group to open the list. The removal persists when you reload or reopen the session.

Removing an item deletes only its record from the current session. It doesn't delete a file or website, revert file changes, or modify an issue or pull request.

Not every displayed item is removable. The remove action is available only when the session provider supports removal and the recorded item has a stable ID. An issue or pull request association that {% data variables.product.prodname_vscode_shortname %} discovers independently might remain visible or reappear after you remove a recorded item. Derived file changes and independently discovered associations can't be removed with this action.

## Ask the agent to create artifacts

You can ask the agent to store something as an artifact by describing what you want in your prompt. For example:

* "Take a screenshot of my app and set it as an artifact"
* "Write a project plan and set it as an artifact"
* "Save these links as artifacts"

The agent then uses the `#artifacts` tool to add the resource to the artifacts panel. You don't need to reference the tool directly. Just describe what you want to keep, and the agent determines when to surface it as an artifact.

The agent can also use the `#artifactRules` tool to override the extraction rules for the current session. This replaces any settings-based rules for the duration of the session.

## Related resources

* [Chat overview](<https://github.com/docs/chat/chat-overview.md>)
* [Agent tools](<https://github.com/docs/agents/run/tools.md>)
* [AI settings reference](<https://github.com/docs/agents/reference/ai-settings.md>)


</details>
