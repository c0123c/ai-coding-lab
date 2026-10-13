# VS Code AI 设置参考

集中列出聊天、模型、Agent、工具、权限、沙箱、会话、指令、Skills、插件、调试和测试等设置，便于查询配置项。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/ai-settings.md>)；源码提交（存档版）：[2026-10-07](<https://github.com/microsoft/vscode-docs/commit/eeb6997081a1b674fb894129737e40ffbe7dfc05>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/ai-settings.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code AI 设置参考.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 7b232695-cbbe-4f3f-a625-abc7a5e6496c
DateApproved: 10/7/2026
MetaDescription: Configure AI features and agents in {% data variables.product.prodname_vscode %}, including chat, agent sessions, dictation, and accessibility settings.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# AI设置参考

本文列出了 AI 功能和 {% data variables.product.prodname_vscode %} 中的 agents 的配置设置。有关使用 {% data variables.product.prodname_vscode_shortname %} 中的设置的一般信息，请参阅 [用户和工作区设置 ](<https://github.com/docs/configure/settings.md>)。

该团队正在不断致力于改进 AI 特征在于 {% data variables.product.prodname_vscode_shortname %} 并添加新功能。某些功能仍处于实验阶段。尝试一下并分享您的反馈 [我们的问题](<https://github.com/microsoft/vscode/issues>)。获取有关的更多信息 [功能生命周期在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/configure/settings.md#feature-lifecycle>).

> [！TIP]
> 如果您还没有Copilot订阅，您可以通过注册[{% data variables.copilot.copilot_free_short %}计划](<https://github.com/github-copilot/signup>)来免费使用Copilot，并获得每月的内嵌建议津贴和AI学分。

使用以下链接跳转到特定区域的设置：

* 核心AI功能：[通用](<#常规设置>)、[代码编辑](<#代码编辑设置>)、[聊天](<#聊天设置>)、[agents](<#agent设置>)、 [agent 会话 ](<#agent-会话>) 和 [ 内联聊天 ](<#内嵌聊天设置>)。
* Agent定制：[迁移](<#自定义迁移设置>)、[自定义说明](<#自定义指令设置>)、[提示文件](<#可重复使用的提示文件设置>)、[自定义agents](<#自定义-agents-设置>)、[agent skills](<#agent-skills设置>) 和 [agent 插件 ](<#agent-插件设置>)。
* 其他 AI 功能：[代码审查](<#代码审查设置>)、[源代码控制](<#源代码控制设置>)、[可观察性](<#可观测性设置>)、 [调试](<#调试设置>)、[测试](<#测试设置>)、[笔记本电脑](<#笔记本设置>)、[语音和听写](<#语音和听写设置>)和[辅助功能](<#辅助功能设置>)。

## 常规设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.commandCenter.enabled)`<br/>控制是否在{% data variables.product.prodname_vscode_shortname %}标题栏中显示聊天菜单。| `true` |
|`setting(workbench.settings.showAISearchToggle)`<br/>在设置编辑器中使用AI启用搜索设置。| `true` |
|`setting(workbench.commandPalette.experimental.askChatLocation)` _（实验）_<br/>控制命令面板应询问聊天问题的位置。| `"chatView"` |
|`setting(search.searchView.semanticSearchBehavior)` _（预览）_<br/>配置何时在搜索视图中运行语义搜索：手动（默认）、未找到文本搜索结果时或始终。| `"manual"` |
|`setting(search.searchView.keywordSuggestions)` _（预览）_<br/>控制是否在搜索视图中显示关键字建议。| `false` |
|`setting(chat.disableAIFeatures)`<br/>禁用并隐藏{% data variables.product.prodname_vscode_shortname %}中的内置AI功能，例如聊天和内联建议，并禁用Copilot扩展。| `false` |

## 代码编辑设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(github.copilot.editor.enableCodeActions)`<br/>控制AI命令是否在可用时显示为代码操作。| `true` |
|`setting(github.copilot.renameSuggestions.triggerAutomatically)`<br/>生成符号重命名建议。| `true` |
|`setting(github.copilot.enable)`<br/>启用或禁用指定[语言](<https://github.com/docs/languages/identifiers.md>)的内联建议。| `{ "*": true, "plaintext": false, "markdown": false, "scminput": false }` |
|`setting(github.copilot.nextEditSuggestions.enabled)`<br/>启用[{% data variables.copilot.next edit suggestions %}](<https://github.com/docs/editing/ai-powered-suggestions.md#next-edit-suggestions>)（NES）。| `true` |
|`setting(editor.inlineSuggest.edits.allowCodeShifting)`<br/>配置NES是否能够移动您的代码以显示建议。| `"always"` |
|`setting(editor.inlineSuggest.edits.renderSideBySide)`<br/>配置NES是否可以在可能的情况下并排显示更大的建议，或者NES是否应始终在相关代码下方显示更大的建议。| `"auto"` |
|`setting(github.copilot.nextEditSuggestions.fixes)`<br/>根据诊断（波形曲线）启用{% data variables.copilot.next edit suggestions %}。例如，缺少进口。| `true` |
|`setting(editor.inlineSuggest.edits.showCollapsed)`<br/>仅当您按 `kbstyle(Tab)` 导航至建议或将鼠标悬停在装订线箭头上时，才会在编辑器中显示 NES 代码更改。| `false` |
|`setting(editor.inlineSuggest.fontFamily)`<br/>配置内联完成的字体系列。| `"default"` |
|`setting(editor.inlineSuggest.showToolbar)`<br/>启用或禁用为内联完成而显示的工具栏。| `"onHover"` |
|`setting(editor.inlineSuggest.minShowDelay)`<br/>显示内联建议之前等待的时间（以毫秒为单位）。| `0` |

## 聊天设置

### 聊天体验

|设置及说明|默认|
|------------------------|---------------|
|`setting(github.copilot.chat.localeOverride)`<br/>指定聊天响应的区域设置，例如 `en` 或 `fr`。| `"auto"` |
|`setting(github.copilot.chat.useProjectTemplates)`<br/>使用`/new`时，使用相关GitHub项目作为起始项目。| `true` |
|`setting(github.copilot.chat.terminalChatLocation)`<br/>控制应从终端打开聊天查询的位置。| `"chatView"` |
|`setting(chat.detectParticipant.enabled)`<br/>在{% data variables.copilot.chat_view %}中启用聊天参与者检测。| `true` |
|`setting(chat.stickyScroll.enabled)`<br/>控制滚动时当前提示是否固定到聊天记录的顶部。| `true` |
|`setting(chat.editor.fontFamily)`<br/>聊天代码块中的字体系列。| `"default"` |
|`setting(chat.editor.fontSize)`<br/>聊天代码块中的字体大小（以像素为单位）。| `14` |
|`setting(chat.editor.fontWeight)`<br/>聊天代码块中的字体粗细。| `"default"` |
|`setting(chat.editor.lineHeight)`<br/>聊天代码块中的行高（以像素为单位）。| `0` |
|`setting(chat.editor.wordWrap)`<br/>切换聊天代码块中的换行。| `"off"` |
|`setting(chat.fontFamily)`<br/>聊天中 Markdown 内容的字体系列。| `"default"` |
|`setting(chat.fontSize)`<br/>聊天中 Markdown 内容的字体大小（以像素为单位）。| `13` |
|`setting(chat.verbose)`<br/>在聊天中显示请求和完成时间戳。将鼠标悬停在完成时间戳上可显示经过的响应时间。| `true` |
|`setting(chat.experimental.persistentProgress)` _（实验）_<br/>保持 [ 进度指示器可见，直到 agent 响应完成 ](<https://github.com/docs/chat/chat-overview.md#customize-the-chat-display>)。值为 `off`、`draw`、`drawMonochrome` 和 `drawMonochromeNoIcon`。|`"draw"`（内部人员）<br/>`"off"`（稳定）<br/>实验可以覆盖这些默认值。|
|`setting(chat.experimental.persistentProgressVerbosity)` _（实验）_<br/>在持续进度处于活动状态时控制已完成的工具调用详细信息。 `compact` 用可扩展的摘要替换已完成的组，并且 `verbose` 保持完整的工具调用详细信息可见。| `"compact"` |
|`setting(chat.notifyWindowOnConfirmation)`<br/>配置当聊天会话中需要用户输入时何时显示OS通知：`off`从不显示通知，`windowNotFocused`（默认）仅在{% data variables.product.prodname_vscode_shortname %}窗口未聚焦时显示通知，`always`始终显示通知。| `"windowNotFocused"` |
|`setting(chat.notifyWindowOnResponseReceived)`<br/>配置收到聊天响应时何时显示 OS 通知：`off` 从不显示通知，`windowNotFocused`（默认）仅在 {% data variables.product.prodname_vscode_shortname %} 窗口未聚焦时显示通知，`always` 始终显示通知。| `"windowNotFocused"` |
|`setting(chat.requestQueuing.defaultAction)`<br/>配置请求处理过程中**发送**按钮的默认操作：`queue`将消息添加到队列，`steer`指示当前请求让步。| `"queue"` |
|`setting(chat.math.enabled)` <br/>在聊天中使用 [KaTeX](<https://katex.org>) 启用或禁用数学渲染。| `false` |
|`setting(chat.viewTitle.enabled)` _（预览）_<br/>在聊天标题中显示当前聊天会话的标题。| `true` |
|`setting(chat.emptyState.history.enabled)` _（实验）_<br/>在{% data variables.copilot.chat_view %}的空状态下显示最近的聊天记录。| `false` |
|`setting(imageCarousel.chat.enabled)` _（实验）_<br/>启用图像轮播以浏览聊天响应中的图像。在工具结果或助理消息中选择图像药丸以打开轮播视图。| `false` |

### 聊天编辑和工件

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.artifacts.enabled)` _（实验）_<br/>在聊天中启用或禁用 [ 工件面板 ](<https://github.com/docs/agents/run/artifacts.md>)（预览）。| `false` |
|`setting(chat.artifacts.rules.byMimeType)` _（实验）_<br/>通过 MIME 类型模式从工具结果中提取工件的规则。将 MIME 类型模式（例如 `"image/*"`）映射到组配置。| `{ "image/*": { "groupName": "Screenshots", "onlyShowGroup": true } }` |
|`setting(chat.artifacts.rules.byFilePath)` _（实验）_<br/>通过文件路径 glob 模式从写入文件中提取工件的规则。将 glob 模式（例如 `"**/*plan*.md"`）映射到组配置。| `{ "**/*plan*.md": { "groupName": "Plans" } }` |
|`setting(chat.artifacts.rules.byMemoryFilePath)` _（实验）_<br/>通过内存文件路径 glob 模式从内存工具写入中提取工件的规则。将 glob 模式映射到组配置。| `{ "**/*plan*.md": { "groupName": "Plans" } }` |
|`setting(chat.checkpoints.enabled)` <br/>在聊天中启用或禁用[检查点](<https://github.com/docs/agents/run/review-code-edits.md#edit-requests-and-restore-checkpoints>)。| `true` |
|`setting(chat.checkpoints.showFileChanges)` <br/>在每个聊天请求末尾显示文件更改的摘要。| `false` |
|`setting(chat.editRequests)`<br/>启用或禁用[编辑以前的聊天请求](<https://github.com/docs/agents/run/review-code-edits.md#edit-a-previous-chat-request>)。| `"inline"` |
|`setting(chat.editing.confirmEditRequestRemoval)`<br/>撤消编辑之前请求确认。| `true` |
|`setting(chat.editing.confirmEditRequestRetry)`<br/>在重做上次编辑之前请求确认。| `true` |
|`setting(chat.editing.autoAcceptDelay)`<br/>配置自动接受建议编辑的延迟，使用零禁用自动接受。| `0` |
|`setting(chat.editing.revealNextChangeOnResolve)`<br/>控制编辑器是否在保留或撤消聊天编辑后自动显示下一个更改。| `true` |

### 聊天模型和上下文

|设置及说明|默认|
|------------------------|---------------|
|`setting(github.copilot.chat.scopeSelection)`<br/>如果使用`/explain`并且活动编辑器没有选择，是否提示输入特定符号范围。| `false` |
|`setting(github.copilot.chat.codesearch.enabled)` _(预览)_<br/>在提示符中使用`#codebase`时，agent会自动发现需要编辑的相关文件。| `false` |
|`setting(chat.emptyState.history.enabled)` _（实验）_<br/>在{% data variables.copilot.chat_view %}的空状态下显示最近的聊天记录。| `false` |
|`setting(imageCarousel.chat.enabled)` <br/>启用图像轮播以浏览聊天回复中的图像。在工具结果或助理消息中选择图像药丸以打开轮播视图。| `false` |
|`setting(chat.sendElementsToChat.enabled)`<br/>启用将元素从[集成浏览器](<https://github.com/docs/debugtest/integrated-browser.md>)发送到{% data variables.copilot.chat_view %}作为上下文。| `true` |
|`setting(chat.sendElementsToChat.attachCSS)`<br/>将集成浏览器中的元素添加到聊天上下文时包含 CSS 样式。| `true` |
|`setting(chat.sendElementsToChat.attachImages)`<br/>将集成浏览器中的元素添加到聊天上下文时包含图像。| `true` |
|`setting(workbench.browser.enableChatTools)`<br/>启用[浏览器工具](<https://github.com/docs/debugtest/integrated-browser.md#browser-tools-for-agents>)，让agents与集成浏览器中的页面进行交互。| `true` |
|`setting(chat.useClaudeMdFile)`<br/>启用或禁用使用`CLAUDE.md`文件作为始终开启的自定义指令。| `true` |
|`setting(chat.useNestedAgentsMdFiles)` `feature(nested-agents-md-files)`<br/>启用或禁用使用工作区子文件夹中的 `AGENTS.md` 文件作为聊天请求的上下文。| `false` |
|`setting(github.copilot.chat.customOAIModels)` _（已弃用）_<br/>配置自定义 OpenAI 兼容模型进行聊天。已弃用，改用 [自定义端点 ](<https://github.com/docs/agent-customization/language-models.md#add-a-custom-endpoint-model>) 提供程序，该提供程序支持聊天完成、响应和消息 API。| `[]` |
|`setting(chat.utilityModel)`<br/>覆盖用于内置[实用程序流](<https://github.com/docs/agent-customization/language-models.md#change-the-model-for-utility-tasks>)的语言模型，例如生成标题、摘要和后备响应。| `"Default"` |
|`setting(chat.utilitySmallModel)`<br/>覆盖用于快速、轻量级[实用程序流](<https://github.com/docs/agent-customization/language-models.md#change-the-model-for-utility-tasks>)的语言模型，例如提交消息、拉取请求标题和描述、重命名建议和意图检测。建议使用快速、便宜的型号。| `"Default"` |
| `setting(chat.byokUtilityModelDefault)`<br/>控制内置哪个模型 [效用流量](<https://github.com/docs/agent-customization/language-models.md#configure-the-default-utility-model-for-byok-models>) 当主要 agent 模型是一个 [BYOK](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>) 模型。选择主要 agent 模型, GitHub Copilot 实用新型，或无默认实用新型。主线时没有效果 agent 模型由提供 GitHub Copilot. | `"GitHub Copilot"` |
|`setting(github.copilot.chat.edits.suggestRelatedFilesFromGitHistory)` _（实验）_<br/>在聊天上下文中建议来自 git 历史记录的相关文件。| `true` |

## Agent设置

### Agent行为

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.agent.enabled:true)`<br/>启用或禁用使用agents（需要{% data variables.product.prodname_vscode_shortname %} 1.99或更高版本）。| `true` |
|`setting(chat.agent.maxRequests)`<br/>agent可以发出的最大请求数。| `25` |
|`setting(github.copilot.chat.agent.autoFix)`<br/>自动诊断并修复生成的代码更改中的问题。| `true` |
|`setting(chat.subagents.allowInvocationsFromSubagents)`<br/>允许子代理调用其他子代理，最大嵌套深度为 5。| `false` |
|`setting(chat.subagents.defaultToAuto)` `feature(subagent-auto-model-selection)`<br/>当工具调用和选定的 agent 均未指定模型时，使用 [Auto 作为本地子代理的默认模型 ](<https://github.com/docs/agents/run/subagents.md#use-auto-for-subagents>)。显式模型选择优先。| `false` |
|`setting(chat.subagents.useRichRendering)`<br/>在其自己的编辑器中打开每个子代理，而不是在父聊天中内联显示其完整活动。| `true` |
|`setting(github.copilot.chat.searchSubagent.subagentSemanticSearchEnabled)` _（实验）_<br/>为搜索子代理启用语义搜索工具。这仅影响搜索子代理，不会在其他地方禁用语义搜索。| `true` |
|`setting(chat.agent.thinking.collapsedTools)` _（实验）_<br/>配置工具调用如何与思考内容分组：`off` 将工具调用分开，`withThinking` 仅在存在思考时对它们进行分组，`always` 始终将工具调用分组在可折叠部分中。| `always` |
|`setting(chat.agent.thinkingStyle)` _（实验）_<br/>配置聊天中思考的呈现方式：`collapsed`默认折叠思考，可以将推理与分组工具调用分开，`collapsedPreview`在非思考内容出现后开始展开并折叠，`fixedScrolling`在可扩展的固定高度自动滚动面板中展示思维。| `fixedScrolling` |

### MCP设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.mcp.access)`<br/>管理Model Context Protocol（MCP）服务器可以在{% data variables.product.prodname_vscode_shortname %}中使用。| `true` |
|`setting(chat.mcp.discovery.enabled)`<br/>配置从其他应用程序自动发现MCP服务器配置。| `false` |
|`setting(chat.mcp.serverSampling)`<br/>配置哪些模型暴露给MCP服务器进行采样。| `{}` |
|`setting(chat.mcp.apps.enabled)` `feature(mcp-apps)`<br/>启用或禁用MCP应用程序，它们是MCP服务器提供的丰富的用户界面。| `true` |
|`setting(chat.mcp.apps.enabled)` _（实验）_<br/>启用或禁用 MCP 应用程序，这些应用程序是 MCP 服务器提供的丰富的用户界面。| `true` |
|`setting(chat.mcp.autostart)` _（实验）_<br/>控制哪些MCP服务器{% data variables.product.prodname_vscode_shortname %}在您提交聊天消息时自动启动。此设置不控制 Agent 主机管理的服务器。| `newAndOutdated` |

### Agent 工具

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.tools.compressOutput.enabled)` _（预览）_<br/>在将大型终端输出发送到模型之前对其进行压缩，以减少上下文窗口的使用。折叠未更改的差异块，删除锁定文件差异，并删除安装进度。| `false` |
|`setting(chat.tools.terminal.autoReplyToPrompts)` <br/>自动回复终端提示并使用默认答案。| `false` |
|`setting(chat.tools.terminal.terminalProfile.<platform>)`<br/>配置每个平台上聊天终端命令使用的终端配置文件。| `""` |
|`setting(chat.tools.terminal.outputLocation)` _（实验）_<br/>配置终端命令输出的显示位置：聊天中内联或集成终端中。| `"chat"` |
|`setting(chat.tools.terminal.outputReflow)`<br/>回流[终端输出预览](<https://github.com/docs/agents/run/tools.md#run-terminal-commands>)以适应聊天宽度。关闭此功能以保留终端的列宽并水平滚动。| `true` |
|`setting(chat.tools.terminal.enforceTimeoutFromModel)` _（实验）_<br/>控制是否强制执行agent为终端命令指定的超时值。启用后，agent 在指定的持续时间后停止跟踪命令并返回迄今为止收集的输出。| `true` |
|`setting(chat.sendElementsToChat.enabled)`<br/>启用将元素从[集成浏览器](<https://github.com/docs/debugtest/integrated-browser.md>)发送到{% data variables.copilot.chat_view %}作为上下文。| `true` |
|`setting(chat.sendElementsToChat.attachCSS)`<br/>将集成浏览器中的元素添加到聊天上下文时包含 CSS 样式。| `true` |
|`setting(chat.sendElementsToChat.attachImages)`<br/>将集成浏览器中的元素添加到聊天上下文时包含图像。| `true` |
|`setting(workbench.browser.enableChatTools)`<br/>启用[浏览器工具](<https://github.com/docs/debugtest/integrated-browser.md#browser-tools-for-agents>)，让agents与集成浏览器中的页面进行交互。| `true` |

### 批准和权限

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.tools.riskAssessment.enabled)` _（实验）_<br/>在终端命令确认上显示 AI- 生成的风险徽章，指示命令是否安全、需要谨慎或应仔细审查。| `true` |
| `setting(chat.tools.terminal.autoApprove)` <br/>控制哪些终端命令 [使用时自动批准 agents](<https://github.com/docs/agents/run/approvals.md#automatically-approve-terminal-commands>)。命令可以设置为 `true` （自动批准）或 `false` （需要批准）。可以通过将模式包装在中来使用正则表达式 `/` 字符。 | `{ "rm": false, "rmdir": false, "del": false, "kill": false, "curl": false, "wget": false, "eval": false, "chmod": false, "chown": false, "/^Remove-Item\\b/i": false }` |
|`setting(chat.tools.terminal.enableAutoApprove)` <br/>启用或禁用终端命令的自动批准。| `true` |
|`setting(chat.tools.edits.autoApprove)` <br/>配置哪些文件在应用编辑之前需要批准。使用 glob 模式来匹配工作区中的文件路径。| `{}` |
|`setting(chat.tools.terminal.ignoreDefaultAutoApproveRules)` <br/>忽略终端命令的默认自动批准规则。| `false` |
|`setting(chat.tools.global.autoApprove)`<br/>自动批准所有工具 - 此设置[禁用关键安全保护](<https://github.com/docs/agents/run/security.md>)。| `false` |
|`setting(chat.assistedPermissions.enabled)` `feature(assisted-permissions)`<br/>在支持的 Agent 主机权限选择器中显示 [辅助权限 ](<https://github.com/docs/agents/run/approvals.md#permission-levels>)。 LLM 法官会评估每个工具调用的风险，并在不批准调用时请求您批准。|`true`（内部人员）<br/>`false`（稳定）|
|`setting(chat.permissions.default)` _（实验）_<br/>为新的聊天会话设置默认的 [ 权限级别 ](<https://github.com/docs/agents/run/approvals.md#permission-levels>)。选项：`default`（手动权限）、`autoApprove`（允许全部）、`autopilot`（自动驾驶）。您仍然可以更改每个会话的权限级别。在 Agent 主机上，自动驾驶仪被选择为 agent 模式。如果企业策略禁用自动批准，新会话将使用手动权限。| `"default"` |
|`setting(chat.autopilot.advanced.enabled)` _（实验）_<br/>启用[高级自动驾驶仪](<https://github.com/docs/agents/run/approvals.md#advanced-autopilot-preview>)，其中一个单独的模型会在每次自动驾驶仪转弯后评估您的请求是否完成并指导下一个转弯，而不是依赖于agent发出完成信号。| `false` |
|`setting(chat.tools.urls.autoApprove)` <br/>控制自动批准哪些[URL请求和响应](<https://github.com/docs/agents/run/approvals.md#url-approval>)。| `[]` |
|`setting(chat.agent.thinking.collapsedTools)` _（实验）_<br/>配置工具调用如何与思考内容分组：`off` 将工具调用分开，`withThinking` 仅在存在思考时对它们进行分组，`always` 始终将工具调用分组在可折叠部分中。| `always` |
|`setting(chat.agent.thinkingStyle)` _（实验）_<br/>配置聊天中思考的呈现方式：`collapsed`默认折叠思考，可以将推理与分组工具调用分开，`collapsedPreview`在非思考内容出现后开始展开并折叠，`fixedScrolling`在可扩展的固定高度自动滚动面板中展示思维。| `fixedScrolling` |
|`setting(chat.tools.eligibleForAutoApproval)` _（实验）_<br/>配置哪些工具需要手动批准才能被agents使用。| `[]` |
|`setting(chat.tools.terminal.blockDetectedFileWrites)` `feature(terminal-block-detected-file-writes)`<br/>在工作区之外执行文件写入的终端命令需要用户批准。当会话级命令批准处于活动状态时，可以免除写入 OS 临时文件夹（macOS 和 Linux 上的 `/tmp`，Windows 上的 `%TEMP%`）。| `outsideWorkspace` |
|`setting(github.copilot.chat.additionalReadAccessFolders)`<br/>授予内置 agent 工具对当前工作区之外的其他文件夹的只读访问权限。| `[]` |
|`setting(github.copilot.chat.claudeAgent.allowDangerouslySkipPermissions)` <br/>绕过 Claude agent 的所有权限检查。仅在隔离的沙箱环境中启用此功能。| `false` |

### 沙箱和网络访问

Agent 主机沙箱设置在执行主机上解析。对于连接的远程 Agent 主机，默认值、路径和平台行为指的是该远程主机。

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.agent.sandbox.enabled)`<br/>控制所有平台上的[Agent主机沙箱](<https://github.com/docs/agents/run/agent-sandboxing.md>)。可能的值为 `off` 和 `on`。| `off` |
|`setting(chat.agent.sandbox.network.allowNetwork)`<br/>允许沙盒 Agent 主机进程访问外部网络。| `true` |
|`setting(chat.agent.sandbox.network.allowLocalNetwork)`<br/>允许沙盒Agent主机进程访问本地网络资源。| `false` |
|`setting(chat.agent.sandbox.allowUnsandboxedCommands)`<br/>当沙箱限制阻止时，允许Agent主机请求确认以在沙箱外部运行命令。| `true` |
|`setting(chat.agent.sandbox.fileSystem.userConfiguredPaths)`<br/>使用`readwritePaths`、`readonlyPaths`和`deniedPaths`数组配置其他执行主机路径。拒绝访问优先于只读访问，后者又优先于读写访问。工作目录是自动授予的。| `{ "readwritePaths": [], "readonlyPaths": [], "deniedPaths": [] }` |
|`setting(chat.agent.sandbox.fileSystem.allowDevToolAccess)`<br/>授予开发人员工具对目录、配置和缓存的访问权限。这些位置可以包含凭据，例如包注册表令牌。| `true` |
|`setting(chat.agent.sandbox.mcpServers)`<br/>在启用 Agent 主机沙箱时，将沙箱应用于 Agent 主机启动或管理的 MCP 服务器。| `true` |
|`setting(chat.agent.sandbox.lspServers)`<br/>在启用 Agent 主机沙箱时，将沙箱应用于 Agent 主机启动或管理的语言服务器。| `true` |
|`setting(chat.agent.sandbox.credentials.authenticategit)`<br/>为沙盒Agent主机进程提供Git身份验证。| `true` |
|`setting(chat.agent.sandbox.credentials.authenticategh)`<br/>向沙盒Agent主机进程提供GitHubCLI身份验证。| `true` |
|`setting(chat.agent.networkFilter)`<br/>为agent工具（获取工具、集成浏览器）启用网络域过滤。启用后，网络访问将根据 `setting(chat.agent.allowedNetworkDomains)` 和 `setting(chat.agent.deniedNetworkDomains)` 进行限制。禁用后，不会应用任何过滤。更改此设置后重新启动 {% data variables.product.prodname_vscode_shortname %}，以确保新的集成浏览器会话使用更新的网络策略。| `false` |
|`setting(chat.agent.allowedNetworkDomains)`<br/>启用`setting(chat.agent.networkFilter)`时为获取工具和集成浏览器配置允许的域，以及兼容的终端沙箱实现。终端过滤功能因实现和平台而异。空列表会阻止使用该过滤器的工具的所有域。支持通配符，如 `*.example.com`。更改此设置后重新启动 {% data variables.product.prodname_vscode_shortname %}，以便新的集成浏览器会话使用更新后的策略。| `[]` |
|`setting(chat.agent.deniedNetworkDomains)`<br/>启用 `setting(chat.agent.networkFilter)` 时，为获取工具和集成浏览器以及兼容的终端沙箱实现配置拒绝域。终端过滤功能因实现和平台而异。拒绝的域优先于允许的域。支持通配符，如 `*.example.com`。更改此设置后重新启动 {% data variables.product.prodname_vscode_shortname %}，以便新的集成浏览器会话使用更新后的策略。| `[]` |

#### 旧版本地和自定义终端沙箱设置

以下设置支持旧版本地或自定义终端行为。它们不配置标准 {% data variables.product.prodname_copilot_short %} Agent 主机沙箱。

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.agent.sandbox.allowNetwork)` _（已弃用）_<br/>以前的网络访问设置。其值迁移至 `setting(chat.agent.sandbox.network.allowNetwork)`。| `true` |
|`setting(chat.agent.sandbox.retryWithAllowNetworkRequests)` _（已弃用，仅限本地）_<br/>允许受阻止的本地终端命令在用户确认后以不受限制的网络访问在旧版沙箱内重试。| `true` |
|`setting(chat.agent.sandbox.allowAutoApprove)` _（仅限本地）_<br/>自动批准在旧沙箱内运行的本地终端命令。此设置不适用于 Agent 主机。| `true` |
|`setting(chat.agent.sandbox.fileSystem.linux)` _（已弃用）_<br/>使用 `allowRead`、`allowWrite`、`denyRead` 和 `denyWrite` 配置旧版 Linux 文件系统访问。 Agent 主机忽略此设置。| `{}` |
|`setting(chat.agent.sandbox.fileSystem.mac)` _（已弃用）_<br/>使用 `allowRead`、`allowWrite`、`denyRead` 和 `denyWrite` 配置旧版 macOS 文件系统访问。 Agent 主机忽略此设置。| `{}` |
|`setting(chat.agent.sandbox.fileSystem.windows)` _（已弃用）_<br/>使用 `allowRead`、`allowWrite` 和 `denyRead` 配置旧版 Windows 文件系统访问。 Agent 主机忽略此设置。| `{}` |

### 规划、模型和 agent 提供商

|设置及说明|默认|
|------------------------|---------------|
|`setting(github.copilot.chat.newWorkspaceCreation.enabled)` _（实验）_<br/>启用在聊天中搭建新工作区的工具。| `true` |
|`setting(chat.planWidget.inlineEditor.enabled)` <br/>使用计划控件内的内联编辑器来编辑计划，而不是打开单独的编辑器选项卡。| `true` |
|`setting(chat.planAgent.defaultModel)` <br/>为计划选择默认语言模型 agent。| `"Auto (Vendor Default)"`|
|`setting(github.copilot.chat.implementAgent.model)` _（实验）_<br/>选择规划后实施步骤所使用的语言模型。| `` |
|`setting(github.copilot.chat.planAgent.additionalTools)` _（实验）_<br/>让计划 agent 在研究和规划阶段访问其他工具。| `[]` |
|`setting(github.copilot.chat.claudeAgent.enabled)` <br/>启用或禁用对由 Anthropic 的 Claude Agent SDK 提供支持的 Claude agent 会话的支持。| `true` |
|`setting(github.copilot.chat.agent.thinkingTool:true)` _（实验）_<br/>使用agents时启用思维工具。| `false` |
|`setting(github.copilot.chat.summarizeAgentConversationHistory.enabled)` _（实验）_<br/>当上下文窗口已满时自动总结agent对话历史记录。| `true` |
|`setting(github.copilot.chat.virtualTools.threshold)` _（实验）_<br/>应使用虚拟工具的工具计数。虚拟工具将相似的工具集组合在一起，并使模型能够按需激活它们。使您能够超越 128 工具的聊天请求限制。| `128` |

## Agent 会话

使用 [ 会话列表 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md#sessions-list>) 管理本地和远程 agent 会话、在任务之间切换并跟踪其进度。

### 会议体验

|设置及说明|默认|
|------------------------|---------------|
|`setting(workbench.startupEditor)` <br/>配置 {% data variables.product.prodname_vscode_shortname %} 欢迎页面作为您的 agent 会话入口点。设置为 `agentSessionsWelcomePage` 可显示 [{% data variables.product.prodname_vscode_shortname %} 欢迎页面 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-on-the-vs-code-welcome-page>)，其中包含最近的会话、嵌入式聊天和快速操作。| N/A |
|`setting(chat.viewSessions.enabled)` <br/>显示{% data variables.copilot.chat_view %}中的agent会话列表。| `true` |
|`setting(chat.viewSessions.orientation)` <br/>控制{% data variables.copilot.chat_view %}中会话列表的布局方向。| `"sideBySide"` |
| `setting(chat.agentSessions.showExternal)` <br/>控制哪个 [来自支持的外部应用程序的会话](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) 出现在会话列表中。值为 `none`, `recent` （过去 7 天内最近的两个）， `last24Hours`, `last7Days`, 和 `all`. | `"none"` |
| `setting(chat.agentSessions.sessionStorageCleanupSuggestion.enabled)` _（实验性）_<br/>显示建议 [清理非活动会话工作树](<https://github.com/docs/agents/run/sessions/manage-sessions.md#clean-up-session-worktrees>) 当他们至少使用 5 GiB 或 20 工作树有资格进行清理。 |`false`<br/>实验可以覆盖此默认值。|
|`setting(chat.agentSessions.autoMarkAsDoneMergedSessionsAfterDays)` `feature(automatic-session-cleanup)`<br/>控制具有合并拉取请求的合格会话[自动标记为完成](<https://github.com/docs/agents/run/sessions/manage-sessions.md#automatically-clean-up-merged-sessions>)之前的不活动天数。设置为 `0` 以禁用自动清理。推荐值为`15`。| `0` |
|`setting(chat.agentSessions.autoDeleteMarkedAsDoneMergedSessionsAfterDays)` `feature(automatic-session-cleanup)`<br/>控制符合条件的会话自动标记为完成后的天数，然后将其永久删除。您手动标记为完成的会话不会自动删除。设置为 `0` 以禁用永久删除。推荐值为`15`。| `0` |
|`setting(sessions.showChatTabs)` _（预览）_<br/>使用 `multiple` 将每个聊天显示为选项卡，或者使用 `single` 仅将活动聊天显示为会话视图。| `"multiple"` |
|`setting(sessions.chat.unifiedWorkspacePicker.enabled)` _（实验）_<br/>使用 {% data variables.copilot.agents_window %} 中的统一工作区选择器搜索本地、GitHub 和远程工作区，并在支持时启动没有工作区的会话。|`true`（内部人员）<br/>`false`（稳定）<br/>实验可以覆盖这些默认值。|
|`setting(sessions.chat.experimental.newSessionComposerLayout)` _（实验）_<br/>将工作区、存储库、工作树和线束控件分组到新会话输入上方。需要统一的工作区选择器。| `false` |
|`setting(sessions.chat.experimental.welcomePhrases)` _（实验）_<br/>在 {% data variables.copilot.agents_window %} 中的新会话作曲家上方显示旋转欢迎短语。| `false` |
|`setting(sessions.chat.experimental.welcomeName)` _（实验）_<br/>设置新会话欢迎消息中的名称。留空以使用登录的 GitHub 个人资料中的名字（如果可用）。| `""` |
|`setting(sessions.chat.experimental.welcomeMessages)` _（实验）_<br/>使用 `append` 将自定义欢迎短语添加到默认值，或仅使用 `replace` 的自定义短语。使用 `{name}` 将欢迎名称放置在短语中。| `{"mode": "append", "phrases": []}` |
|`setting(chat.editMode.hidden)` <br/>恢复已弃用的多文件代码编辑模式。| `true` |
|`setting(chat.agentsControl.enabled)` _（实验）_<br/>在命令中心启用[会话状态指示器](<https://github.com/docs/agents/run/sessions/manage-sessions.md#session-status-indicator-experimental>)。显示未读和正在进行的会话徽章。| `true` |
|`setting(chat.agentsControl.clickBehavior)` _（实验）_<br/>配置在 agent 状态指示器中选择聊天图标时的行为。|`"cycle"`（内部人员）<br/>`"default"`（稳定）|
|`setting(sessions.showApplicationBadge)` _（预览）_<br/>在[应用程序图标](<https://github.com/docs/agents/run/sessions/manage-sessions.md#monitor-sessions-from-the-application-icon-preview>)上显示需要关注的未存档会话数。|`true`（内部人员）<br/>`false`（稳定）|
|`setting(chat.agentSessions.preferredDarkBackgroundImageLayout)` _（实验）_<br/>使用深色主题时控制{% data variables.copilot.agents_window %}中的[聊天背景图像布局](<https://github.com/docs/chat/chat-overview.md#customize-the-agents-window-chat-background>)。| `"repeat"` |
|`setting(chat.agentSessions.preferredLightBackgroundImageLayout)` _（实验）_<br/>使用浅色主题时控制{% data variables.copilot.agents_window %}中的[聊天背景图像布局](<https://github.com/docs/chat/chat-overview.md#customize-the-agents-window-chat-background>)。| `"repeat"` |
|`setting(chat.unifiedAgentsBar.enabled)` _（实验）_<br/>用统一的聊天和搜索控件替换命令中心搜索框。| `false` |
|`setting(sessions.layout.singlePaneDetailPanel)` _（实验）_<br/>使用共享选项卡栏将 {% data variables.copilot.agents_window %} 详细信息面板停靠在编辑器内。需要重新加载窗口才能生效。| `false` |
|`setting(sessions.editor.wordWrap)` _（实验）_<br/>控制[{% data variables.copilot.agents_window %}中的代码编辑器如何换行长行](<https://github.com/docs/agents/run/agents-window-configuration.md#configure-word-wrap-for-code-editors>)。值为 `off`、`on` 和 `inherit`。| `"inherit"` |
|`setting(sessions.diffEditor.wordWrap)` _（实验）_<br/>控制[**更改**视图中的差异编辑器如何换行长行](<https://github.com/docs/agents/run/review-code-edits.md#configure-word-wrap-in-diff-editors>)。值为 `off`、`on` 和 `inherit`。| `"inherit"` |
| `setting(chat.experimental.agentsWindowPRComments)` `feature(agents-window-pr-comments)`<br/>显示 **添加 PR 评论** 当你 [审阅时添加范围注释 agent 变化](<https://github.com/docs/agents/run/review-code-edits.md#add-pull-request-review-comments>). | `false` |
|`setting(sessions.useWorktree)` _（内部人士）_<br/>控制在未保存先前的代码隔离选择时是否选择**新工作树**。启动会话后，保存的选择将应用于整个工作区。| `true` |
|`setting(chat.automations.enabled)` `feature(automations)`<br/>在{% data variables.copilot.agents_window %}中显示[自动化](<https://github.com/docs/agents/run/automations.md>)并运行计划的agent任务。禁用后，自动化入口点将被隐藏，并且不会调度计划任务。|`true`（内部人员）<br/>`false`（稳定）|
|`setting(github.copilot.chat.cli.remote.enabled)` <br/>从 github.com 或 GitHub 移动应用程序启用对 Copilot 会话的远程控制支持。| `true` |
|`setting(github.copilot.chat.localIndex.enabled)`<br/>为[会话见解](<https://github.com/docs/agents/run/sessions/session-history.md#query-session-history-with-chronicle>)和`/chronicle`命令启用本地会话跟踪。| `true` |
|`setting(chat.sessionSync.enabled)`<br/>启用[会话同步](<https://github.com/docs/agents/run/sessions/session-history.md>)到GitHub.com。启用后，Copilot 会话数据会同步到您的 GitHub 帐户以进行跨设备访问。还需要启用 `setting(github.copilot.chat.localIndex.enabled)`。| `true` |
|`setting(chat.sessionSync.excludeRepositories)`<br/>要从[会话同步](<https://github.com/docs/agents/run/sessions/session-history.md>)中排除的存储库模式。使用精确的 `owner/repo` 名称或全局模式，例如 `my-org/*`。来自匹配存储库的会话仅存储在本地。| `[]` |

### Agent 主机

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.agentHost.allowSignedOutWhenUsable)` _（实验）_<br/>当至少一种注册会话类型可以使用其自己的提供者凭据运行时，无需 GitHub 身份验证即可打开 {% data variables.copilot.agents_window %}。需要 GitHub 身份验证的提供程序、模型和操作会在需要时提示您登录。| `false` |
|`setting(chat.agentHost.byokModels.enabled)` `feature(agent-host-byok-models)`<br/>连接 [BYOK](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>) 语言模型桥，以便扩展提供的 BYOK 模型可以在 Agent 主机会话中运行。更改将同步到正在运行的 Agent 主机。| `false` |
|`setting(chat.agentHost.claudeAgent.enabled)` _（实验）_<br/>在 [Agent 主机 ](<https://github.com/docs/agents/concepts/agent-host.md>) 进程中注册 Claude 提供程序，以便 Claude 会话在 Agent 上运行主持人。 agent主机进程必须重新启动才能生效。| `true` |
|`setting(chat.agentHost.codexAgent.enabled)` _（实验）_<br/>在 [Agent 主机 ](<https://github.com/docs/agents/concepts/agent-host.md>) 进程中注册 Codex 提供程序。启用无需重启Agent主机即可生效。禁用将在下一次 Agent 主机重新启动后生效。| `false` |
|`setting(chat.agentHost.devContainer.enabled)`<br/>显示**对符合条件的本地文件夹和SSH、隧道或WSL主机上的文件夹使用开发容器**，并[运行Agent在项目的开发中托管会话容器](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>)。| `false` |
|`setting(chat.agentHost.agentOrchestrationLimits)` _（实验）_<br/>控制 Agent 主机会话工具创建的会话和聊天、发送的消息以及递归会话创建的进程范围限制。设置为 `off` 以删除限制而不更改确认或验证要求。| `"on"` |
|`setting(chat.remoteAgentHostsEnabled)` _（实验）_<br/>允许连接到[远程agent主机](<https://github.com/docs/agents/run/remote-agent-sessions.md>)。| `true` |
|`setting(chat.remoteSessions.tools.enabled)` _（实验）_<br/>为agents提供内置工具来发现远程主机、创建和检查远程会话以及在会话之间发送消息。需要远程 agent 主机。关闭此功能不会断开主机连接或停止已在运行的远程会话。| `false` |
|`setting(chat.agentHost.shellTool.initScript.enabled)` `feature(agent-host-shell-initialization)`<br/>在本地 Copilot Agent 主机会话中的每个 SDK shell 命令之前加载 macOS 和 Linux 上的 `~/.bashrc` 或 Windows 上的 PowerShell 配置文件。安装 [Python 环境 ](<https://github.com/docs/python/environments.md#terminal-settings>) 并将 `setting(python-envs.terminal.autoActivationType)` 设置为 `shellStartup` 后，还要激活选定的工作区环境。这不适用于远程会话或 Agent 主机自定义终端工具。| `false` |
|`setting(chat.agents.claude.preferAgentHost)` _（实验）_<br/>运行从 Agent 主机上的 {% data variables.copilot.agents_window %} 打开的 Claude 会话，而不是 GitHub Copilot 聊天扩展。| `true` |
|`setting(chat.editor.codex.preferAgentHost)` _（实验）_<br/>运行从 Agent 主机上的 {% data variables.copilot.chat_view %}（而不是 OpenAI 扩展）打开的 Codex 会话。每个窗口仅出现一个 Codex 实现。需要 `setting(chat.agentHost.codexAgent.enabled)` 并在更改时提示重新启动。| `false` |

### {% data variables.copilot.agent_merge_caps %}

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.agentMerge.enabled)` _（实验）_<br/>启用[{% data variables.copilot.agent_merge %}](<https://github.com/docs/agents/run/agents-window.md#finish-a-pull-request-with-agent-merge>)及其命令。 {% data variables.copilot.agent_merge_caps %} 监视会话的拉取请求，并可以要求 agent 解决选定的阻止程序。| `false` |
|`setting(chat.agentMerge.addressReviews)` _（实验）_<br/>解决来自存储库维护者或 Copilot 拉取请求审阅者的未解决的审阅线程、更改请求的审阅以及新的拉取请求注释。| `true` |
|`setting(chat.agentMerge.fixCI)` _（实验）_<br/>请求 agent 修复失败的必需 CI 检查。| `true` |
|`setting(chat.agentMerge.resolveConflicts)` _（实验）_<br/>要求agent更新后面的分支并解决合并冲突。| `true` |
|`setting(chat.agentMerge.mergePullRequest)` _（实验）_<br/>控制 {% data variables.copilot.agent_merge %} 准备就绪时是否合并或排队拉取请求。 `always` 在任何选定的维护工作后合并，`ifUnchanged` 仅在 agent 修复回合更改拉取请求之前合并，并且 `never` 关闭自动合并。| `"never"` |
|`setting(chat.agentMerge.mergeMethod)` _（实验）_<br/>选择合并方法。 `auto` 按此顺序使用第一个存储库兼容方法：压缩、合并提交或变基。您还可以要求 `squash`、`merge` 或 `rebase`。| `"auto"` |
|`setting(chat.agentMerge.replyAttribution)` _（实验）_<br/>在 {% data variables.copilot.agent_merge %} 发布的评论主题回复中包含自动回复属性。| `true` |

## 内嵌聊天设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(inlineChat.defaultModel)`<br/>配置编辑器内嵌聊天的默认语言模型。您选择的模型在会话期间持续存在，但在 {% data variables.product.prodname_vscode_shortname %} 重新加载后重置为此配置的默认值。| N/A |
|`setting(inlineChat.askInChat)`<br/>当文件属于活动聊天编辑会话时，请使用 {% data variables.copilot.chat_view %} 中的“聊天中提问”，而不是在按 `kb(inlineChat.start)` 时打开常规内联聊天。禁用后，始终打开常规内联聊天。| `true` |
|`setting(inlineChat.renderMode)` _（实验）_<br/>配置内嵌聊天的显示方式。 `hover`：在浮动叠加层中显示内联聊天，`zone`：在编辑器的专用区域中显示内联聊天。| `"hover"` |
|`setting(inlineChat.finishOnType)`<br/>在更改区域之外键入内容时完成编辑器内联聊天会话。| `false` |
|`setting(inlineChat.holdToSpeech)`<br/>按住编辑器内联聊天键盘快捷键 (`kb(inlineChat.start)`) 会自动启用语音识别。| `true` |
|`setting(editor.inlineSuggest.syntaxHighlightingEnabled)`<br/>显示内联建议的语法突出显示。| `true` |
|`setting(inlineChat.affordance)` _（实验）_<br/>当您选择文本以帮助启动内联聊天时显示视觉提示。 `off`：无提示，`gutter`：显示在行号区域，`editor`：显示在cursor位置，有灯泡。| `"off"` |
|`setting(inlineChat.lineEmptyHint)` _（实验）_<br/>在空行上显示编辑器内联聊天的提示。| `false` |
|`setting(inlineChat.lineNaturalLanguageHint)` _（实验）_<br/>当一行主要由单词组成时，触发编辑器内联聊天。| `true` |
|`setting(github.copilot.chat.editor.temporalContext.enabled)` _（实验）_<br/>在编辑器内联聊天的上下文中包含最近查看和编辑的文件。| `false` |

## 代码审查设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(github.copilot.chat.reviewSelection.enabled)` _（预览）_<br/>使用 AI 启用代码审查以进行编辑器文本选择。| `true` |
|`setting(github.copilot.chat.reviewSelection.instructions)` _（预览）_<br/>添加到使用 AI 检查当前编辑器选择的请求中的自定义指令。| `[]` |

## 源代码控制设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(git.addAICoAuthor)`<br/>附加 `Co-authored-by:` Git 预告片以提交 AI- 生成的更改的消息。选项：`off`（无预告片）、`chatAndAgent`（Copilot聊天或agent模式更改的预告片）、`all`（所有AI-生成的代码的预告片，包括内联完成）。请参阅 [AI 合著者归属 ](<https://github.com/docs/sourcecontrol/staging-commits.md#ai-co-author-attribution>)。| `"off"` |

## 自定义迁移设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.customizations.migration.enabled)` _（实验）_<br/>显示选定的 Agent 主机线束无法在其当前位置或格式中使用的自定义的迁移操作、提示、兼容性标志和兼容性详细信息。| `true` |

了解如何[迁移agent自定义项](<https://github.com/docs/agent-customization/migrate-customizations.md>)。

## 自定义指令设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.instructionsFilesLocations)` _（已弃用）_<br/>为本地 agent 配置自定义指令位置。此设置和本地 agent 将在未来版本中删除。使用[自定义迁移](<https://github.com/docs/agent-customization/migrate-customizations.md#move-customizations-from-configured-locations>)将指令移动到支持的位置。| `{ ".github/instructions": true, ".claude/rules": true, "~/.copilot/instructions": true, "~/.claude/rules": true }` |
|`setting(chat.includeApplyingInstructions)`<br/>自动将具有匹配的 `applyTo` 模式的指令文件添加到聊天请求。| `true` |
|`setting(chat.includeReferencedInstructions)`<br/>自动将通过 Markdown 链接引用的说明文件添加到聊天请求。| `false` |
|`setting(github.copilot.chat.codeGeneration.useInstructionFiles)`<br/>自动将 `.github/copilot-instructions.md` 中的自定义指令添加到聊天请求。| `true` |
|`setting(github.copilot.chat.commitMessageGeneration.instructions)` _（实验）_<br/>使用 AI 生成提交消息的自定义指令。| `[]` |
|`setting(github.copilot.chat.pullRequestDescriptionGeneration.instructions)` _（实验）_<br/>使用 AI 生成拉取请求标题和描述的自定义指令。| `[]` |
|`setting(github.copilot.chat.organizationInstructions.enabled)`<br/>启用在 GitHub 组织级别定义的自定义指令的发现。| `true` |
|`setting(chat.useCustomizationsInParentRepositories)`<br/>启用在[父存储库文件夹](<https://github.com/docs/agent-customization/overview.md#use-customizations-in-a-monorepo>)中发现agent自定义项（说明、提示、agents、技能、挂钩）。对于打开子文件夹而不是存储库根目录的 monorepo 设置很有用。| `false` |
| `setting(chat.useHooks)` _（预览）_ <br/>启用 [本地安全带的挂钩](<https://github.com/docs/agent-customization/hooks.md#configure-hooks-for-the-local-harness>)。此设置不配置 Copilot, Claude, 或 Codex 钩子执行。 | `true` |
|`setting(chat.hookFilesLocations)` _（预览）_ <br/>配置其他[本地挂钩文件位置](<https://github.com/docs/agent-customization/hooks.md#local-hook-file-locations>)。指定加载所有 `*.json` 文件的文件夹路径，或指定 `.json` 文件的直接路径。仅支持相对路径和波形符路径。| `{}` |
|`setting(chat.useClaudeHooks)` _（预览）_ <br/>启用本地线束从 Claude 配置文件解析挂钩。本地线束忽略 Claude 匹配器值。| `false` |
|`setting(chat.useAgentsMdFile)` <br/>启用或禁用使用 `AGENTS.md` 文件作为聊天请求的上下文。| `true` |
|`setting(chat.useClaudeMdFile)`<br/>启用或禁用使用`CLAUDE.md`文件作为始终开启的自定义指令。| `true` |
|`setting(chat.useNestedAgentsMdFiles)` `feature(nested-agents-md-files)`<br/>启用或禁用使用工作区子文件夹中的 `AGENTS.md` 文件作为聊天请求的上下文。| `false` |

## 可重复使用的提示文件设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.promptFilesLocations)` _（已弃用）_<br/>配置本地 agent 的提示文件位置。此设置和本地 agent 将在未来版本中删除。使用[提示文件迁移](<https://github.com/docs/agent-customization/migrate-customizations.md#convert-prompt-files-to-skills>)将提示转换为技能。| `{ ".github/prompts": true }` |
|`setting(chat.promptFilesRecommendations)` <br/>打开新的聊天会话时启用或禁用提示文件推荐。提示文件名和布尔值或when子句的键值对列表。| `[]` |

## 自定义 agents 设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.agentFilesLocations)` _（已弃用）_<br/>为本地 agent 配置自定义 agent 位置。此设置和本地 agent 将在未来版本中删除。使用 [ 自定义迁移 ](<https://github.com/docs/agent-customization/migrate-customizations.md#move-customizations-from-configured-locations>) 将 agents 移动到支持的位置。| `{ ".github/agents": true, ".claude/agents": true, "~/.copilot/agents": true }` |
|`setting(github.copilot.chat.cli.customAgents.enabled)` <br/>启用在 Copilot 会话中使用自定义 agents。| `false` |
|`setting(github.copilot.chat.organizationCustomAgents.enabled)` <br/>启用在 GitHub 组织级别定义的自定义 agents 的发现。| `true` |

## Agent skills设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.useAgentSkills)` <br/>在{% data variables.product.prodname_vscode_shortname %}中启用对[agent skills](<https://github.com/docs/agent-customization/agent-skills.md>)的支持。| `true` |
|`setting(chat.agentSkillsLocations)` _（已弃用）_<br/>为本地agent配置agent技能位置。此设置和本地 agent 将在未来版本中删除。使用[自定义迁移](<https://github.com/docs/agent-customization/migrate-customizations.md#move-customizations-from-configured-locations>)将技能移动到支持的位置。| `{ ".agents/skills": true, ".github/skills": true, ".claude/skills": true, "~/.agents/skills": true, "~/.copilot/skills": true, "~/.claude/skills": true }` |
|`setting(github.copilot.chat.skillTool.enabled)` _（实验）_<br/>启用调用[agent skills](<https://github.com/docs/agent-customization/agent-skills.md>)的专用技能工具。需要在单独的子代理上下文中使用 [`context: fork`](<https://github.com/docs/agent-customization/agent-skills.md#run-a-skill-in-a-forked-context-experimental>) 运行技能。| `false` |

## 可观测性设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(github.copilot.chat.otel.enabled)` <br/>为 Copilot 聊天 agent 交互启用 [OpenTelemetry](<https://github.com/docs/agents/guides/monitoring-agents.md>) 发射。| `false` |
|`setting(github.copilot.chat.otel.exporterType)` <br/>OTel 导出商类型：`otlp-http`、`otlp-grpc`、`console` 或 `file`。| `"otlp-http"` |
|`setting(github.copilot.chat.otel.otlpEndpoint)` <br/>OTLP 收集器端点 URL。| `"http://localhost:4318"` |
|`setting(github.copilot.chat.otel.outfile)` <br/>使用 `file` 导出器类型时输出的 JSON- 行的文件路径。| `""` |
|`setting(github.copilot.chat.otel.captureContent)` <br/>捕获 OTel 跨度中的完整提示和响应内容。可能包含敏感信息。| `false` |
|`setting(github.copilot.chat.otel.captureIdentity)` <br/>捕获本地线束会话的 OTel 数据中经过身份验证的帐户名、操作系统用户名和计算机主机名。这与内容捕获无关，并且可能包含敏感信息。| `false` |

## Agent 插件设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(chat.plugins.enabled)`<br/>启用或禁用对 [agent 插件 ](<https://github.com/docs/agent-customization/agent-plugins.md>) 的支持。| `false` |
|`setting(chat.plugins.marketplaces)` _（实验）_<br/>配置其他插件市场 Git 存储库以发现 agent 插件。| `["github/copilot-plugins", "github/awesome-copilot"]` |
|`setting(chat.plugins.enabledPlugins)`<br/>要启用或禁用的插件 ID。当[通过企业策略](<https://github.com/docs/enterprise/manage-ai-settings.md#manage-agent-plugins-and-marketplaces>)集中管理时，`true`强制启用插件，`false`强制禁用它，并且省略的插件仍处于正常用户启用状态。| `{}` |
| `setting(chat.plugins.strictMarketplaces)` _（实验性）_<br/>将插件安装限制在已批准的市场来源列表中。空列表会阻止所有市场。可以是 [通过企业政策集中管理](<https://github.com/docs/enterprise/manage-ai-settings.md#manage-agent-plugins-and-marketplaces>). | `null` |
|`setting(chat.pluginLocations)` _（实验）_<br/>通过将目录路径映射到启用或禁用状态来注册本地克隆或下载的 agent 插件。| `{}` |

## 调试设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(github.copilot.chat.agentDebugLog.fileLogging.enabled)`<br/>在分机主机聊天会话中启用 [agent 调试日志 ](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md>) 的文件日志记录。此设置将 Agent 调试日志面板和 [`/troubleshoot`](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md#attach-debug-events-to-chat>) 斜线命令的调试事件写入磁盘。更改此设置后重新加载窗口。| `false` |
|`setting(chat.agentHost.agentDebugLog.enabled)` _（实验）_<br/>为 Agent 主机会话启用 agent 调试日志记录，并在 Agent 调试日志面板中显示其调试事件。仅捕获启用此设置时运行的会话。| `false` |
|`setting(github.copilot.chat.copilotDebugCommand.enabled)` _（预览）_<br/>启用 `copilot-debug` 终端命令。| `true` |

## 测试设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(github.copilot.chat.generateTests.codeLens)` _（实验）_<br/>显示 **为当前测试覆盖率信息未涵盖的符号生成测试**代码镜头。| `false` |
|`setting(github.copilot.chat.setupTests.enabled)` _（实验）_<br/>在 `/tests` 生成中启用实验 `/setupTests` 意图和提示。| `true` |

## 笔记本设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(notebook.experimental.generate)` _（实验）_<br/>启用 **生成** 操作以使用笔记本内联聊天创建代码单元。| `true` |
|`setting(github.copilot.chat.edits.newNotebook.enabled)` _（实验）_<br/>在编辑模式下启用笔记本工具（已弃用）以创建新的笔记本文件。| `true` |
|`setting(github.copilot.chat.notebook.followCellExecution.enabled)` _（实验）_<br/>显示编辑器中当前正在执行的单元格。| `false` |

## 语音和听写设置

了解如何[使用语音模式和内置听写](<https://github.com/docs/configure/accessibility/voice.md>)。

|设置及说明|默认|
|------------------------|---------------|
|`setting(agents.voice.enabled)` `feature(voice-mode)`<br/>控制语音模式是否可用于与 agent 进行语音对话。| `false` |
|`setting(agents.voice.showButton)` `feature(voice-mode)`<br/>控制**语音模式**按钮是否出现在聊天输入中。当按钮隐藏时，键盘快捷键仍然可用。| `true` |
|`setting(agents.voice.speakResponses)` `feature(voice-mode)`<br/>控制agent是否大声朗读响应。启用 `setting(agents.voice.showTranscript)` 以在语音响应关闭时将响应读取为文本。| `true` |
|`setting(agents.voice.agentProgress)` `feature(voice-mode)`<br/>控制agent在工作时是否说出简短的进度更新。| `true` |
|`setting(agents.voice.voice)` `feature(voice-mode)`<br/>选择大声朗读 agent 响应的语音。| `"birch_neutral"` |
|`setting(agents.voice.language)` `feature(built-in-dictation)`<br/>选择语音识别、听写和口头响应的语言。使用 `auto` 在支持时使用配置的显示语言，否则使用系统或浏览器区域设置。| `"auto"` |
|`setting(agents.voice.showTranscript)` `feature(voice-mode)`<br/>在语音模式处于活动状态时在聊天输入中显示语音模式记录。| `false` |
|`setting(agents.voice.liveTranscript)` `feature(voice-mode)`<br/>在您说话时逐字显示语音。需要 `setting(agents.voice.showTranscript)`。| `false` |
|`setting(agents.voice.handsFree)` `feature(voice-mode)`<br/>在 agent 完成免提通话后再次开始收听。| `true` |
|`setting(agents.voice.turn.silenceMs)` `feature(voice-mode)`<br/>设置语音模式结束并发送回合之前的尾随静音（以毫秒为单位）。设置为 `-1` 可关闭基于静音的回合结束。| `800` |
|`setting(agents.voice.turn.stopPhrases)` `feature(voice-mode)`<br/>列出在话语结束时结束并发送回合的短语。| `["send it"]` |
|`setting(dictation.enabled)` `feature(built-in-dictation)`<br/>控制内置听写是否在聊天、{% data variables.copilot.agents_window %}、编辑器和终端中可用。| `true` |
|`setting(dictation.model)` `feature(built-in-dictation)`<br/>选择听写的语音识别模型。| `"nemotron-3.5-asr-streaming-0.6b"` |
|`setting(dictation.showTranscript)` `feature(built-in-dictation)`<br/>在您讲话时显示临时转录。关闭此设置时仍会插入最终文本。| `true` |
|`setting(dictation.experimental.llmCleanup)` `feature(built-in-dictation)`<br/>使用语言模型来改进最终成绩单中的标点符号、大写、段落、列表和数字格式。| `true` |

## 辅助功能设置

|设置及说明|默认|
|------------------------|---------------|
|`setting(inlineChat.accessibleDiffView)`<br/>内联聊天是否还为其更改呈现可访问的差异查看器。| `"auto"` |
|`setting(accessibility.signals.chatRequestSent)`<br/> 播放信号 - 声音（音频提示） and/or 公告（警报） - 当发出聊天请求时。| `{ "sound": "auto", "announcement": "auto" }` |
|`setting(accessibility.signals.chatResponseReceived)`<br/>收到响应后播放声音/音频提示。| `{ "sound": "auto" }` |
|`setting(accessibility.signals.chatEditModifiedFile)`<br/>当文件被聊天编辑修改时播放声音/音频提示。| `{ "sound": "auto" }` |
|`setting(accessibility.signals.chatUserActionRequired)`<br/>当用户需要在聊天中执行操作时播放声音/音频提示。| `{ "sound": "auto", "announcement": "auto" }` |
|`setting(accessibility.signals.confetti)`<br/>当聊天反馈或将会话标记为已完成时显示五彩纸屑时，会播放欢乐的声音。| `{ "sound": "auto" }` |
|`setting(accessibility.signals.lineHasInlineSuggestion)`<br/>当 cursor 位于具有内嵌建议的行时播放声音/音频提示。| `{ "sound": "auto" }` |
|`setting(accessibility.signals.nextEditSuggestion)`<br/>当下一个编辑建议可用时播放声音/音频提示。| `{ "sound": "auto", "announcement": "auto" }` |
|`setting(accessibility.verboseChatProgressUpdates)`<br/>提供有关聊天活动的详细更新。| `true` |
|`setting(accessibility.verbosity.inlineChat)`<br/>提供有关如何访问内联编辑器聊天辅助功能帮助菜单和警报的信息，以及描述如何在输入焦点时使用该功能的提示。| `true` |
|`setting(accessibility.verbosity.inlineCompletions)`<br/>提供有关如何访问内联建议悬停和无障碍视图的信息。| `true` |
|`setting(accessibility.verbosity.panelChat)`<br/>提供有关如何在聊天输入获得焦点时访问聊天帮助菜单的信息。| `true` |
|`setting(accessibility.verbosity.newSessionWelcome)`<br/>当新会话作曲家出现在{% data variables.copilot.agents_window %}中时，宣布欢迎标题。| `true` |
|`setting(accessibility.voice.keywordActivation)`<br/>控制是否识别关键字短语“Hey Code”来启动语音聊天会话。| `"off"` |
|`setting(accessibility.voice.autoSynthesize)`<br/>控制当使用语音作为输入时是否应自动大声读出文本响应。| `"off"` |
|`setting(accessibility.voice.speechTimeout)`<br/>在指定的静音毫秒数后停止终端听写。设置为 `0` 以继续收听。| `0` |

## 相关资源

* [快速概览 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/reference/ai-features-cheat-sheet.md>) 中的 AI 功能


## 原文对照

<details>
<summary>展开完整原文</summary>

# AI settings reference

This article lists the configuration settings for the AI features and agents in {% data variables.product.prodname_vscode %}. For general information about working with settings in {% data variables.product.prodname_vscode_shortname %}, refer to [User and workspace settings](<https://github.com/docs/configure/settings.md>).

The team is continuously working on improving the AI features in {% data variables.product.prodname_vscode_shortname %} and adding new functionality. Some features are still experimental. Try them out and share your feedback in [our issues](<https://github.com/microsoft/vscode/issues>). Get more info about the [feature lifecycle in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/configure/settings.md#feature-lifecycle>).

> [!TIP]
> If you don't yet have a Copilot subscription, you can use Copilot for free by signing up for the [{% data variables.copilot.copilot_free_short %} plan](<https://github.com/github-copilot/signup>) and get a monthly allowance of inline suggestions and AI credits.

Use the following links to jump to the settings for a specific area:

* Core AI features: [general](<#general-settings>), [code editing](<#code-editing-settings>), [chat](<#chat-settings>), [agents](<#agent-settings>), [agent sessions](<#agent-sessions>), and [inline chat](<#inline-chat-settings>).
* Agent customization: [migration](<#customization-migration-settings>), [custom instructions](<#custom-instructions-settings>), [prompt files](<#reusable-prompt-files-settings>), [custom agents](<#custom-agents-settings>), [agent skills](<#agent-skills-settings>), and [agent plugins](<#agent-plugins-settings>).
* Other AI features: [code review](<#code-review-settings>), [source control](<#source-control-settings>), [observability](<#observability-settings>), [debugging](<#debugging-settings>), [testing](<#testing-settings>), [notebooks](<#notebook-settings>), [voice and dictation](<#voice-and-dictation-settings>), and [accessibility](<#accessibility-settings>).

## General settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.commandCenter.enabled)`<br/>Controls whether to show the Chat menu in the {% data variables.product.prodname_vscode_shortname %} title bar. | `true` |
| `setting(workbench.settings.showAISearchToggle)`<br/>Enable searching settings with AI in the Settings editor. | `true` |
| `setting(workbench.commandPalette.experimental.askChatLocation)` _(Experimental)_<br/>Controls where the Command Palette should ask chat questions. | `"chatView"` |
| `setting(search.searchView.semanticSearchBehavior)` _(Preview)_<br/>Configure when to run semantic search in the Search view: manually (default), when no text search results are found, or always. | `"manual"` |
| `setting(search.searchView.keywordSuggestions)` _(Preview)_<br/>Controls whether to show keyword suggestions in the Search view. | `false` |
| `setting(chat.disableAIFeatures)`<br/>Disable and hide built-in AI features in {% data variables.product.prodname_vscode_shortname %}, such as chat and inline suggestions, and disable the Copilot extensions. | `false` |

## Code editing settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(github.copilot.editor.enableCodeActions)`<br/>Controls if AI commands are shown as Code Actions when available. | `true` |
| `setting(github.copilot.renameSuggestions.triggerAutomatically)`<br/>Generate symbol renaming suggestions. | `true` |
| `setting(github.copilot.enable)`<br/>Enable or disable inline suggestions for specified [languages](<https://github.com/docs/languages/identifiers.md>). | `{ "*": true, "plaintext": false, "markdown": false, "scminput": false }` |
| `setting(github.copilot.nextEditSuggestions.enabled)`<br/>Enables [{% data variables.copilot.next edit suggestions %}](<https://github.com/docs/editing/ai-powered-suggestions.md#next-edit-suggestions>) (NES). | `true` |
| `setting(editor.inlineSuggest.edits.allowCodeShifting)`<br/>Configure if NES is able to shift your code to show a suggestion. | `"always"` |
| `setting(editor.inlineSuggest.edits.renderSideBySide)`<br/>Configure if NES can show larger suggestions side-by-side if possible, or if NES should always show larger suggestions below the relevant code. | `"auto"` |
| `setting(github.copilot.nextEditSuggestions.fixes)`<br/>Enable {% data variables.copilot.next edit suggestions %} based on diagnostics (squiggles). For example, missing imports. | `true` |
| `setting(editor.inlineSuggest.edits.showCollapsed)`<br/>Show NES code changes in the editor only when you press `kbstyle(Tab)` to navigate to the suggestion or hover over the gutter arrow. | `false` |
| `setting(editor.inlineSuggest.fontFamily)`<br/>Configure the font family for inline completions. | `"default"` |
| `setting(editor.inlineSuggest.showToolbar)`<br/>Enable or disable the toolbar that appears for inline completions. | `"onHover"` |
| `setting(editor.inlineSuggest.minShowDelay)`<br/>Time in milliseconds to wait before showing inline suggestions. | `0` |

## Chat settings

### Chat experience

| Setting and Description | Default |
|------------------------|---------------|
| `setting(github.copilot.chat.localeOverride)`<br/>Specify a locale for chat responses, such as `en` or `fr`. | `"auto"` |
| `setting(github.copilot.chat.useProjectTemplates)`<br/>Use relevant GitHub projects as starter projects when using `/new`. | `true` |
| `setting(github.copilot.chat.terminalChatLocation)`<br/>Controls where chat queries from the terminal should be opened. | `"chatView"` |
| `setting(chat.detectParticipant.enabled)`<br/>Enable chat participant detection in the {% data variables.copilot.chat_view %}. | `true` |
| `setting(chat.stickyScroll.enabled)`<br/>Controls whether the current prompt is pinned to the top of the chat transcript while scrolling. | `true` |
| `setting(chat.editor.fontFamily)`<br/>Font family in chat codeblocks. | `"default"` |
| `setting(chat.editor.fontSize)`<br/>Font size in pixels in chat codeblocks. | `14` |
| `setting(chat.editor.fontWeight)`<br/>Font weight in chat codeblocks. | `"default"` |
| `setting(chat.editor.lineHeight)`<br/>Line height in pixels in chat codeblocks. | `0` |
| `setting(chat.editor.wordWrap)`<br/>Toggle line wrapping in chat codeblocks. | `"off"` |
| `setting(chat.fontFamily)`<br/>Font family for Markdown content in chat. | `"default"` |
| `setting(chat.fontSize)`<br/>Font size in pixels for Markdown content in chat. | `13` |
| `setting(chat.verbose)`<br/>Show request and completion timestamps in chat. Hover over a completion timestamp to show elapsed response time. | `true` |
| `setting(chat.experimental.persistentProgress)` _(Experimental)_<br/>Keep a [progress indicator visible until an agent response finishes](<https://github.com/docs/chat/chat-overview.md#customize-the-chat-display>). Values are `off`, `draw`, `drawMonochrome`, and `drawMonochromeNoIcon`. | `"draw"` (Insiders)<br/>`"off"` (Stable)<br/>Experiments can override these defaults. |
| `setting(chat.experimental.persistentProgressVerbosity)` _(Experimental)_<br/>Control completed tool-call details while persistent progress is active. `compact` replaces completed groups with expandable summaries, and `verbose` keeps full tool-call details visible. | `"compact"` |
| `setting(chat.notifyWindowOnConfirmation)`<br/>Configure when to show an OS notification when user input is needed in a chat session: `off` to never show notifications, `windowNotFocused` (default) to show notifications only when the {% data variables.product.prodname_vscode_shortname %} window is not focused, `always` to always show notifications. | `"windowNotFocused"` |
| `setting(chat.notifyWindowOnResponseReceived)`<br/>Configure when to show an OS notification when a chat response is received: `off` to never show notifications, `windowNotFocused` (default) to show notifications only when the {% data variables.product.prodname_vscode_shortname %} window is not focused, `always` to always show notifications. | `"windowNotFocused"` |
| `setting(chat.requestQueuing.defaultAction)`<br/>Configure the default action for the **Send** button while a request is in progress: `queue` adds the message to the queue, `steer` signals the current request to yield. | `"queue"` |
| `setting(chat.math.enabled)` <br/>Enable or disable math rendering with [KaTeX](<https://katex.org>) in chat. | `false` |
| `setting(chat.viewTitle.enabled)` _(Preview)_<br/>Show the title of the current chat session in the chat header. | `true` |
| `setting(chat.emptyState.history.enabled)` _(Experimental)_<br/>Show recent chat history in the empty state of the {% data variables.copilot.chat_view %}. | `false` |
| `setting(imageCarousel.chat.enabled)` _(Experimental)_<br/>Enable the image carousel for browsing images from chat responses. Select image pills in tool results or assistant messages to open a carousel view. | `false` |

### Chat editing and artifacts

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.artifacts.enabled)` _(Experimental)_<br/>Enable or disable the [artifacts panel](<https://github.com/docs/agents/run/artifacts.md>) in the chat (preview). | `false` |
| `setting(chat.artifacts.rules.byMimeType)` _(Experimental)_<br/>Rules for extracting artifacts from tool results by MIME type pattern. Maps MIME type patterns (such as `"image/*"`) to a group configuration. | `{ "image/*": { "groupName": "Screenshots", "onlyShowGroup": true } }` |
| `setting(chat.artifacts.rules.byFilePath)` _(Experimental)_<br/>Rules for extracting artifacts from written files by file path glob pattern. Maps glob patterns (such as `"**/*plan*.md"`) to a group configuration. | `{ "**/*plan*.md": { "groupName": "Plans" } }` |
| `setting(chat.artifacts.rules.byMemoryFilePath)` _(Experimental)_<br/>Rules for extracting artifacts from memory tool writes by memory file path glob pattern. Maps glob patterns to a group configuration. | `{ "**/*plan*.md": { "groupName": "Plans" } }` |
| `setting(chat.checkpoints.enabled)` <br/>Enable or disable [checkpoints](<https://github.com/docs/agents/run/review-code-edits.md#edit-requests-and-restore-checkpoints>) in the chat. | `true` |
| `setting(chat.checkpoints.showFileChanges)` <br/>Show a summary of file changes at the end of each chat request. | `false` |
| `setting(chat.editRequests)`<br/>Enable or disable [editing previous chat requests](<https://github.com/docs/agents/run/review-code-edits.md#edit-a-previous-chat-request>). | `"inline"` |
| `setting(chat.editing.confirmEditRequestRemoval)`<br/>Ask for confirmation before undoing an edit. | `true` |
| `setting(chat.editing.confirmEditRequestRetry)`<br/>Ask for confirmation before performing a redo of the last edit. | `true` |
| `setting(chat.editing.autoAcceptDelay)`<br/>Configure a delay after which suggested edits are automatically accepted, use zero to disable auto-accept. | `0` |
| `setting(chat.editing.revealNextChangeOnResolve)`<br/>Controls whether the editor automatically reveals the next change after keeping or undoing a chat edit. | `true` |

### Chat models and context

| Setting and Description | Default |
|------------------------|---------------|
| `setting(github.copilot.chat.scopeSelection)`<br/>Whether to prompt for a specific symbol scope if you use `/explain` and the active editor has no selection. | `false` |
| `setting(github.copilot.chat.codesearch.enabled)` _(Preview)_<br/>When using `#codebase` in the prompt, the agent automatically discovers relevant files to be edited. | `false` |
| `setting(chat.emptyState.history.enabled)` _(Experimental)_<br/>Show recent chat history in the empty state of the {% data variables.copilot.chat_view %}. | `false` |
| `setting(imageCarousel.chat.enabled)` <br/>Enable the image carousel for browsing images from chat responses. Select image pills in tool results or assistant messages to open a carousel view. | `false` |
| `setting(chat.sendElementsToChat.enabled)`<br/>Enable sending elements from the [integrated browser](<https://github.com/docs/debugtest/integrated-browser.md>) to the {% data variables.copilot.chat_view %} as context. | `true` |
| `setting(chat.sendElementsToChat.attachCSS)`<br/>Include CSS styles when adding elements from the integrated browser to chat context. | `true` |
| `setting(chat.sendElementsToChat.attachImages)`<br/>Include images when adding elements from the integrated browser to chat context. | `true` |
| `setting(workbench.browser.enableChatTools)`<br/>Enable [browser tools](<https://github.com/docs/debugtest/integrated-browser.md#browser-tools-for-agents>) that let agents interact with pages in the integrated browser. | `true` |
| `setting(chat.useClaudeMdFile)`<br/>Enable or disable using `CLAUDE.md` files as always-on custom instructions. | `true` |
| `setting(chat.useNestedAgentsMdFiles)` `feature(nested-agents-md-files)`<br/>Enable or disable using `AGENTS.md` files in subfolders of your workspace as context for chat requests. | `false` |
| `setting(github.copilot.chat.customOAIModels)` _(Deprecated)_<br/>Configure custom OpenAI-compatible models for chat. Deprecated in favor of the [Custom Endpoint](<https://github.com/docs/agent-customization/language-models.md#add-a-custom-endpoint-model>) provider, which supports Chat Completions, Responses, and Messages APIs. | `[]` |
| `setting(chat.utilityModel)`<br/>Override the language model used for built-in [utility flows](<https://github.com/docs/agent-customization/language-models.md#change-the-model-for-utility-tasks>), such as generating titles, summaries, and fallback responses. | `"Default"` |
| `setting(chat.utilitySmallModel)`<br/>Override the language model used for fast, lightweight [utility flows](<https://github.com/docs/agent-customization/language-models.md#change-the-model-for-utility-tasks>), such as commit messages, pull request titles and descriptions, rename suggestions, and intent detection. A fast, inexpensive model is recommended. | `"Default"` |
| `setting(chat.byokUtilityModelDefault)`<br/>Control which model built-in [utility flows](<https://github.com/docs/agent-customization/language-models.md#configure-the-default-utility-model-for-byok-models>) use when the main agent model is a [BYOK](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>) model. Choose the main agent model, GitHub Copilot utility models, or no default utility model. Has no effect when the main agent model is provided by GitHub Copilot. | `"GitHub Copilot"` |
| `setting(github.copilot.chat.edits.suggestRelatedFilesFromGitHistory)` _(Experimental)_<br/>Suggest related files from git history in chat context. | `true` |

## Agent settings

### Agent behavior

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.agent.enabled:true)`<br/>Enable or disable using agents (requires {% data variables.product.prodname_vscode_shortname %} 1.99 or later). | `true` |
| `setting(chat.agent.maxRequests)`<br/>Maximum number of requests that the agent can make. | `25` |
| `setting(github.copilot.chat.agent.autoFix)`<br/>Automatically diagnose and fix issues in the generated code changes. | `true` |
| `setting(chat.subagents.allowInvocationsFromSubagents)`<br/>Enable subagents to invoke other subagents, up to a maximum nesting depth of five. | `false` |
| `setting(chat.subagents.defaultToAuto)` `feature(subagent-auto-model-selection)`<br/>Use [Auto as the default model for local subagents](<https://github.com/docs/agents/run/subagents.md#use-auto-for-subagents>) when neither the tool call nor the selected agent specifies a model. Explicit model selections take precedence. | `false` |
| `setting(chat.subagents.useRichRendering)`<br/>Open each subagent in its own editor instead of showing its full activity inline in the parent chat. | `true` |
| `setting(github.copilot.chat.searchSubagent.subagentSemanticSearchEnabled)` _(Experimental)_<br/>Enable the semantic search tool for the Search subagent. This only affects the Search subagent and does not disable semantic search elsewhere. | `true` |
| `setting(chat.agent.thinking.collapsedTools)` _(Experimental)_<br/>Configure how tool calls are grouped with thinking content: `off` keeps tool calls separate, `withThinking` groups them only when thinking is present, and `always` always groups tool calls in collapsible sections. | `always` |
| `setting(chat.agent.thinkingStyle)` _(Experimental)_<br/>Configure how thinking is rendered in chat: `collapsed` collapses thinking by default and can separate reasoning from grouped tool calls, `collapsedPreview` starts expanded and collapses after non-thinking content appears, and `fixedScrolling` shows thinking in a fixed-height auto-scrolling panel that you can expand. | `fixedScrolling` |

### MCP settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.mcp.access)`<br/>Manage which Model Context Protocol (MCP) servers can be used in {% data variables.product.prodname_vscode_shortname %}. | `true` |
| `setting(chat.mcp.discovery.enabled)`<br/>Configure automatic discovery of MCP server configuration from other applications. | `false` |
| `setting(chat.mcp.serverSampling)`<br/>Configure which models are exposed to MCP servers for sampling. | `{}` |
| `setting(chat.mcp.apps.enabled)` `feature(mcp-apps)`<br/>Enable or disable MCP Apps, which are rich user interfaces provided by MCP servers. | `true` |
| `setting(chat.mcp.apps.enabled)` _(Experimental)_<br/>Enable or disable MCP Apps, which are rich user interfaces provided by MCP servers. | `true` |
| `setting(chat.mcp.autostart)` _(Experimental)_<br/>Control which MCP servers {% data variables.product.prodname_vscode_shortname %} starts automatically when you submit a chat message. This setting doesn't control servers managed by the Agent Host. | `newAndOutdated` |

### Agent tools

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.tools.compressOutput.enabled)` _(Preview)_<br/>Compress large terminal output before sending it to the model to reduce context window usage. Collapses unchanged diff hunks, drops lockfile diffs, and strips install progress. | `false` |
| `setting(chat.tools.terminal.autoReplyToPrompts)` <br/>Automatically reply to terminal prompts with a default answer. | `false` |
| `setting(chat.tools.terminal.terminalProfile.<platform>)`<br/>Configure which terminal profile to use for chat terminal commands on each platform. | `""` |
| `setting(chat.tools.terminal.outputLocation)` _(Experimental)_<br/>Configure where terminal command output appears: inline in chat or in the integrated terminal. | `"chat"` |
| `setting(chat.tools.terminal.outputReflow)`<br/>Reflow [terminal output previews](<https://github.com/docs/agents/run/tools.md#run-terminal-commands>) to fit the chat width. Turn this off to preserve the terminal's column width and scroll horizontally. | `true` |
| `setting(chat.tools.terminal.enforceTimeoutFromModel)` _(Experimental)_<br/>Control whether to enforce the timeout value that the agent specifies for terminal commands. When enabled, the agent stops tracking the command after the specified duration and returns the output collected so far. | `true` |
| `setting(chat.sendElementsToChat.enabled)`<br/>Enable sending elements from the [integrated browser](<https://github.com/docs/debugtest/integrated-browser.md>) to the {% data variables.copilot.chat_view %} as context. | `true` |
| `setting(chat.sendElementsToChat.attachCSS)`<br/>Include CSS styles when adding elements from the integrated browser to chat context. | `true` |
| `setting(chat.sendElementsToChat.attachImages)`<br/>Include images when adding elements from the integrated browser to chat context. | `true` |
| `setting(workbench.browser.enableChatTools)`<br/>Enable [browser tools](<https://github.com/docs/debugtest/integrated-browser.md#browser-tools-for-agents>) that let agents interact with pages in the integrated browser. | `true` |

### Approvals and permissions

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.tools.riskAssessment.enabled)` _(Experimental)_<br/>Show an AI-generated risk badge on terminal command confirmations, indicating whether a command is safe, requires caution, or should be reviewed carefully. | `true` |
| `setting(chat.tools.terminal.autoApprove)` <br/>Control which terminal commands are [auto-approved when using agents](<https://github.com/docs/agents/run/approvals.md#automatically-approve-terminal-commands>). Commands can be set to `true` (auto-approve) or `false` (require approval). Regular expressions can be used by wrapping patterns in `/` characters. | `{ "rm": false, "rmdir": false, "del": false, "kill": false, "curl": false, "wget": false, "eval": false, "chmod": false, "chown": false, "/^Remove-Item\\b/i": false }` |
| `setting(chat.tools.terminal.enableAutoApprove)` <br/>Enable or disable automatic approval of terminal commands. | `true` |
| `setting(chat.tools.edits.autoApprove)` <br/>Configure which files require approval before edits are applied. Uses glob patterns to match file paths in your workspace. | `{}` |
| `setting(chat.tools.terminal.ignoreDefaultAutoApproveRules)` <br/>Ignore the default auto-approve rules for terminal commands. | `false` |
| `setting(chat.tools.global.autoApprove)`<br/>Automatically approve all tools - this setting [disables critical security protections](<https://github.com/docs/agents/run/security.md>). | `false` |
| `setting(chat.assistedPermissions.enabled)` `feature(assisted-permissions)`<br/>Show [Assisted permissions](<https://github.com/docs/agents/run/approvals.md#permission-levels>) in supported Agent Host permission pickers. An LLM judge evaluates the risk of each tool call and asks for your approval when it does not approve a call. | `true` (Insiders)<br/>`false` (Stable) |
| `setting(chat.permissions.default)` _(Experimental)_<br/>Set the default [permission level](<https://github.com/docs/agents/run/approvals.md#permission-levels>) for new chat sessions. Options: `default` (Manual permissions), `autoApprove` (Allow all), `autopilot` (Autopilot). You can still change the permission level per session. On the Agent Host, Autopilot is selected as an agent mode instead. If enterprise policy disables auto-approval, new sessions use Manual permissions. | `"default"` |
| `setting(chat.autopilot.advanced.enabled)` _(Experimental)_<br/>Enable [Advanced Autopilot](<https://github.com/docs/agents/run/approvals.md#advanced-autopilot-preview>), where a separate model evaluates whether your request is complete after each Autopilot turn and guides the next turn, instead of relying on the agent to signal completion. | `false` |
| `setting(chat.tools.urls.autoApprove)` <br/>Control which [URL requests and responses are auto-approved](<https://github.com/docs/agents/run/approvals.md#url-approval>). | `[]` |
| `setting(chat.agent.thinking.collapsedTools)` _(Experimental)_<br/>Configure how tool calls are grouped with thinking content: `off` keeps tool calls separate, `withThinking` groups them only when thinking is present, and `always` always groups tool calls in collapsible sections. | `always` |
| `setting(chat.agent.thinkingStyle)` _(Experimental)_<br/>Configure how thinking is rendered in chat: `collapsed` collapses thinking by default and can separate reasoning from grouped tool calls, `collapsedPreview` starts expanded and collapses after non-thinking content appears, and `fixedScrolling` shows thinking in a fixed-height auto-scrolling panel that you can expand. | `fixedScrolling` |
| `setting(chat.tools.eligibleForAutoApproval)` _(Experimental)_<br/>Configure which tools require manual approval before they can be used by agents. | `[]` |
| `setting(chat.tools.terminal.blockDetectedFileWrites)` `feature(terminal-block-detected-file-writes)`<br/>Require user approval for terminal commands that perform file writes outside the workspace. Writes to the OS temporary folder (`/tmp` on macOS and Linux, `%TEMP%` on Windows) are exempt when session-level command approval is active. | `outsideWorkspace` |
| `setting(github.copilot.chat.additionalReadAccessFolders)`<br/>Grant read-only access to additional folders outside the current workspace for built-in agent tools. | `[]` |
| `setting(github.copilot.chat.claudeAgent.allowDangerouslySkipPermissions)` <br/>Bypass all permission checks for the Claude agent. Only enable this in isolated sandbox environments. | `false` |

### Sandboxing and network access

Agent Host sandbox settings are resolved on the execution host. For a connected remote Agent Host, defaults, paths, and platform behavior refer to that remote host.

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.agent.sandbox.enabled)`<br/>Control [Agent Host sandboxing](<https://github.com/docs/agents/run/agent-sandboxing.md>) on all platforms. Possible values are `off` and `on`. | `off` |
| `setting(chat.agent.sandbox.network.allowNetwork)`<br/>Permit external network access for sandboxed Agent Host processes. | `true` |
| `setting(chat.agent.sandbox.network.allowLocalNetwork)`<br/>Permit sandboxed Agent Host processes to access local network resources. | `false` |
| `setting(chat.agent.sandbox.allowUnsandboxedCommands)`<br/>Allow the Agent Host to request confirmation to run a command outside the sandbox when sandbox restrictions block it. | `true` |
| `setting(chat.agent.sandbox.fileSystem.userConfiguredPaths)`<br/>Configure additional execution-host paths with `readwritePaths`, `readonlyPaths`, and `deniedPaths` arrays. Denied access takes precedence over read-only access, which takes precedence over read-write access. The working directory is granted automatically. | `{ "readwritePaths": [], "readonlyPaths": [], "deniedPaths": [] }` |
| `setting(chat.agent.sandbox.fileSystem.allowDevToolAccess)`<br/>Grant access to directories, configuration, and caches for developer tools. These locations can include credentials such as package registry tokens. | `true` |
| `setting(chat.agent.sandbox.mcpServers)`<br/>Apply sandboxing to MCP servers that the Agent Host launches or manages when Agent Host sandboxing is enabled. | `true` |
| `setting(chat.agent.sandbox.lspServers)`<br/>Apply sandboxing to language servers that the Agent Host launches or manages when Agent Host sandboxing is enabled. | `true` |
| `setting(chat.agent.sandbox.credentials.authenticategit)`<br/>Provide Git authentication to sandboxed Agent Host processes. | `true` |
| `setting(chat.agent.sandbox.credentials.authenticategh)`<br/>Provide GitHub CLI authentication to sandboxed Agent Host processes. | `true` |
| `setting(chat.agent.networkFilter)`<br/>Enable network domain filtering for agent tools (fetch tool, integrated browser). When enabled, network access is restricted according to `setting(chat.agent.allowedNetworkDomains)` and `setting(chat.agent.deniedNetworkDomains)`. When disabled, no filtering is applied. Restart {% data variables.product.prodname_vscode_shortname %} after you change this setting to ensure new integrated browser sessions use the updated network policy. | `false` |
| `setting(chat.agent.allowedNetworkDomains)`<br/>Configure allowed domains for the fetch tool and integrated browser when `setting(chat.agent.networkFilter)` is enabled, and for compatible terminal sandbox implementations. Terminal filtering capabilities vary by implementation and platform. An empty list blocks all domains for tools that use the filter. Supports wildcards like `*.example.com`. Restart {% data variables.product.prodname_vscode_shortname %} after changing this setting so new integrated browser sessions use the updated policy. | `[]` |
| `setting(chat.agent.deniedNetworkDomains)`<br/>Configure denied domains for the fetch tool and integrated browser when `setting(chat.agent.networkFilter)` is enabled, and for compatible terminal sandbox implementations. Terminal filtering capabilities vary by implementation and platform. Denied domains take precedence over allowed domains. Supports wildcards like `*.example.com`. Restart {% data variables.product.prodname_vscode_shortname %} after changing this setting so new integrated browser sessions use the updated policy. | `[]` |

#### Legacy Local and custom terminal sandbox settings

The following settings support legacy Local or custom terminal behavior. They do not configure the standard {% data variables.product.prodname_copilot_short %} Agent Host sandbox.

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.agent.sandbox.allowNetwork)` _(Deprecated)_<br/>Former network access setting. Its value migrates to `setting(chat.agent.sandbox.network.allowNetwork)`. | `true` |
| `setting(chat.agent.sandbox.retryWithAllowNetworkRequests)` _(Deprecated, Local only)_<br/>Allow a blocked Local terminal command to retry inside the legacy sandbox with unrestricted network access after user confirmation. | `true` |
| `setting(chat.agent.sandbox.allowAutoApprove)` _(Local only)_<br/>Automatically approve Local terminal commands that run inside the legacy sandbox. This setting does not apply to Agent Host. | `true` |
| `setting(chat.agent.sandbox.fileSystem.linux)` _(Deprecated)_<br/>Configure legacy Linux file system access with `allowRead`, `allowWrite`, `denyRead`, and `denyWrite`. Agent Host ignores this setting. | `{}` |
| `setting(chat.agent.sandbox.fileSystem.mac)` _(Deprecated)_<br/>Configure legacy macOS file system access with `allowRead`, `allowWrite`, `denyRead`, and `denyWrite`. Agent Host ignores this setting. | `{}` |
| `setting(chat.agent.sandbox.fileSystem.windows)` _(Deprecated)_<br/>Configure legacy Windows file system access with `allowRead`, `allowWrite`, and `denyRead`. Agent Host ignores this setting. | `{}` |

### Planning, models, and agent providers

| Setting and Description | Default |
|------------------------|---------------|
| `setting(github.copilot.chat.newWorkspaceCreation.enabled)` _(Experimental)_<br/>Enable the tool for scaffolding a new workspace in chat. | `true` |
| `setting(chat.planWidget.inlineEditor.enabled)` <br/>Use an inline editor inside the plan control to edit plans, instead of opening a separate editor tab. | `true` |
| `setting(chat.planAgent.defaultModel)` <br/>Select a default language model for the plan agent. | `"Auto (Vendor Default)"`|
| `setting(github.copilot.chat.implementAgent.model)` _(Experimental)_<br/>Select the language model used for the implementation step after planning. | `` |
| `setting(github.copilot.chat.planAgent.additionalTools)` _(Experimental)_<br/>Give the plan agent access to additional tools during research and planning phases. | `[]` |
| `setting(github.copilot.chat.claudeAgent.enabled)` <br/>Enable or disable support for Claude agent sessions powered by Anthropic's Claude Agent SDK. | `true` |
| `setting(github.copilot.chat.agent.thinkingTool:true)` _(Experimental)_<br/>Enable the thinking tool when using agents. | `false` |
| `setting(github.copilot.chat.summarizeAgentConversationHistory.enabled)` _(Experimental)_<br/>Automatically summarize the agent conversation history when the context window is full. | `true` |
| `setting(github.copilot.chat.virtualTools.threshold)` _(Experimental)_<br/>Tool count over which virtual tools should be used. Virtual tools group similar sets of tools together and enable the model to activate them on-demand. Enables you to go beyond the limit of 128 tools for a chat request. | `128` |

## Agent sessions

Use the [session lists](<https://github.com/docs/agents/run/sessions/manage-sessions.md#sessions-list>) to manage local and remote agent sessions, switch between tasks, and track their progress.

### Session experience

| Setting and Description | Default |
|------------------------|---------------|
| `setting(workbench.startupEditor)` <br/>Configure the {% data variables.product.prodname_vscode_shortname %} welcome page to act as your agent sessions entry point. Set to `agentSessionsWelcomePage` to show the [{% data variables.product.prodname_vscode_shortname %} welcome page](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-on-the-vs-code-welcome-page>) with recent sessions, embedded chat, and quick actions. | N/A |
| `setting(chat.viewSessions.enabled)` <br/>Show the agent sessions list in the {% data variables.copilot.chat_view %}. | `true` |
| `setting(chat.viewSessions.orientation)` <br/>Control the layout orientation of the sessions list in the {% data variables.copilot.chat_view %}. | `"sideBySide"` |
| `setting(chat.agentSessions.showExternal)` <br/>Control which [sessions from supported external applications](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) appear in the session lists. Values are `none`, `recent` (the two most recent from the last seven days), `last24Hours`, `last7Days`, and `all`. | `"none"` |
| `setting(chat.agentSessions.sessionStorageCleanupSuggestion.enabled)` _(Experimental)_<br/>Show a suggestion to [clean up inactive session worktrees](<https://github.com/docs/agents/run/sessions/manage-sessions.md#clean-up-session-worktrees>) when they use at least 5 GiB or 20 worktrees are eligible for cleanup. | `false`<br/>Experiments can override this default. |
| `setting(chat.agentSessions.autoMarkAsDoneMergedSessionsAfterDays)` `feature(automatic-session-cleanup)`<br/>Control the number of inactive days before an eligible session with a merged pull request is [automatically marked as done](<https://github.com/docs/agents/run/sessions/manage-sessions.md#automatically-clean-up-merged-sessions>). Set to `0` to disable automatic cleanup. The recommended value is `15`. | `0` |
| `setting(chat.agentSessions.autoDeleteMarkedAsDoneMergedSessionsAfterDays)` `feature(automatic-session-cleanup)`<br/>Control the number of days after an eligible session is automatically marked as done before it is permanently deleted. Sessions that you mark as done manually aren't deleted automatically. Set to `0` to disable permanent deletion. The recommended value is `15`. | `0` |
| `setting(sessions.showChatTabs)` _(Preview)_<br/>Show each chat as a tab with `multiple`, or show only the active chat as the session view with `single`. | `"multiple"` |
| `setting(sessions.chat.unifiedWorkspacePicker.enabled)` _(Experimental)_<br/>Use the unified workspace picker in the {% data variables.copilot.agents_window %} to search local, GitHub, and remote workspaces and, when supported, start a session without a workspace. | `true` (Insiders)<br/>`false` (Stable)<br/>Experiments can override these defaults. |
| `setting(sessions.chat.experimental.newSessionComposerLayout)` _(Experimental)_<br/>Group workspace, repository, worktree, and harness controls above the new-session input. Requires the unified workspace picker. | `false` |
| `setting(sessions.chat.experimental.welcomePhrases)` _(Experimental)_<br/>Show rotating welcome phrases above the new-session composer in the {% data variables.copilot.agents_window %}. | `false` |
| `setting(sessions.chat.experimental.welcomeName)` _(Experimental)_<br/>Set the name in new-session welcome messages. Leave empty to use the first name from the signed-in GitHub profile when available. | `""` |
| `setting(sessions.chat.experimental.welcomeMessages)` _(Experimental)_<br/>Add custom welcome phrases to the defaults with `append`, or use only custom phrases with `replace`. Use `{name}` to position the welcome name in a phrase. | `{"mode": "append", "phrases": []}` |
| `setting(chat.editMode.hidden)` <br/>Restore the deprecated Edit mode for multi-file code edits. | `true` |
| `setting(chat.agentsControl.enabled)` _(Experimental)_<br/>Enable the [session status indicator](<https://github.com/docs/agents/run/sessions/manage-sessions.md#session-status-indicator-experimental>) in the command center. Shows unread and in-progress session badges. | `true` |
| `setting(chat.agentsControl.clickBehavior)` _(Experimental)_<br/>Configure the behavior when selecting the chat icon in the agent status indicator. | `"cycle"` (Insiders)<br/>`"default"` (Stable) |
| `setting(sessions.showApplicationBadge)` _(Preview)_<br/>Show the number of unarchived sessions that need attention on the [application icon](<https://github.com/docs/agents/run/sessions/manage-sessions.md#monitor-sessions-from-the-application-icon-preview>). | `true` (Insiders)<br/>`false` (Stable) |
| `setting(chat.agentSessions.preferredDarkBackgroundImageLayout)` _(Experimental)_<br/>Control the [chat background image layout](<https://github.com/docs/chat/chat-overview.md#customize-the-agents-window-chat-background>) in the {% data variables.copilot.agents_window %} when using a dark color theme. | `"repeat"` |
| `setting(chat.agentSessions.preferredLightBackgroundImageLayout)` _(Experimental)_<br/>Control the [chat background image layout](<https://github.com/docs/chat/chat-overview.md#customize-the-agents-window-chat-background>) in the {% data variables.copilot.agents_window %} when using a light color theme. | `"repeat"` |
| `setting(chat.unifiedAgentsBar.enabled)` _(Experimental)_<br/>Replace the command center search box with a unified chat and search control. | `false` |
| `setting(sessions.layout.singlePaneDetailPanel)` _(Experimental)_<br/>Dock the {% data variables.copilot.agents_window %} detail panel inside the editor with a shared tab bar. Requires a window reload to take effect. | `false` |
| `setting(sessions.editor.wordWrap)` _(Experimental)_<br/>Control [how code editors in the {% data variables.copilot.agents_window %} wrap long lines](<https://github.com/docs/agents/run/agents-window-configuration.md#configure-word-wrap-for-code-editors>). Values are `off`, `on`, and `inherit`. | `"inherit"` |
| `setting(sessions.diffEditor.wordWrap)` _(Experimental)_<br/>Control [how diff editors in the **Changes** view wrap long lines](<https://github.com/docs/agents/run/review-code-edits.md#configure-word-wrap-in-diff-editors>). Values are `off`, `on`, and `inherit`. | `"inherit"` |
| `setting(chat.experimental.agentsWindowPRComments)` `feature(agents-window-pr-comments)`<br/>Show **Add PR Comment** when you [add range comments while reviewing agent changes](<https://github.com/docs/agents/run/review-code-edits.md#add-pull-request-review-comments>). | `false` |
| `setting(sessions.useWorktree)` _(Insiders)_<br/>Control whether **New Worktree** is selected when no previous code-isolation choice has been saved. After you start a session, the saved choice applies across workspaces instead. | `true` |
| `setting(chat.automations.enabled)` `feature(automations)`<br/>Show [Automations](<https://github.com/docs/agents/run/automations.md>) in the {% data variables.copilot.agents_window %} and run scheduled agent tasks. When disabled, automation entry points are hidden and scheduled tasks aren't dispatched. | `true` (Insiders)<br/>`false` (Stable) |
| `setting(github.copilot.chat.cli.remote.enabled)` <br/>Enable remote control support for Copilot sessions from github.com or the GitHub Mobile app. | `true` |
| `setting(github.copilot.chat.localIndex.enabled)`<br/>Enable local session tracking for [session insights](<https://github.com/docs/agents/run/sessions/session-history.md#query-session-history-with-chronicle>) and `/chronicle` commands. | `true` |
| `setting(chat.sessionSync.enabled)`<br/>Enable [session sync](<https://github.com/docs/agents/run/sessions/session-history.md>) to GitHub.com. When enabled, Copilot session data syncs to your GitHub account for cross-device access. Requires `setting(github.copilot.chat.localIndex.enabled)` to also be enabled. | `true` |
| `setting(chat.sessionSync.excludeRepositories)`<br/>Repository patterns to exclude from [session sync](<https://github.com/docs/agents/run/sessions/session-history.md>). Use exact `owner/repo` names or glob patterns like `my-org/*`. Sessions from matching repositories are stored locally only. | `[]` |

### Agent Host

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.agentHost.allowSignedOutWhenUsable)` _(Experimental)_<br/>Open the {% data variables.copilot.agents_window %} without GitHub authentication when at least one registered session type can run with its own provider credentials. Providers, models, and operations that require GitHub authentication prompt you to sign in when needed. | `false` |
| `setting(chat.agentHost.byokModels.enabled)` `feature(agent-host-byok-models)`<br/>Wire up the [BYOK](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>) language model bridge so extension-provided BYOK models can run in Agent Host sessions. Changes are synchronized to the running Agent Host. | `false` |
| `setting(chat.agentHost.claudeAgent.enabled)` _(Experimental)_<br/>Register the Claude provider in the [Agent Host](<https://github.com/docs/agents/concepts/agent-host.md>) process, so Claude sessions run on the Agent Host. The agent host process must be restarted to take effect. | `true` |
| `setting(chat.agentHost.codexAgent.enabled)` _(Experimental)_<br/>Register the Codex provider in the [Agent Host](<https://github.com/docs/agents/concepts/agent-host.md>) process. Enabling takes effect without restarting the Agent Host. Disabling takes effect after the next Agent Host restart. | `false` |
| `setting(chat.agentHost.devContainer.enabled)`<br/>Show **Use Dev Container** for eligible local folders and folders on SSH, Tunnel, or WSL hosts, and [run Agent Host sessions in the project's Dev Container](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>). | `false` |
| `setting(chat.agentHost.agentOrchestrationLimits)` _(Experimental)_<br/>Control process-wide limits on sessions and chats created, messages sent, and recursive session creation by Agent Host session tools. Set to `off` to remove the limits without changing confirmation or validation requirements. | `"on"` |
| `setting(chat.remoteAgentHostsEnabled)` _(Experimental)_<br/>Allow connections to [remote agent hosts](<https://github.com/docs/agents/run/remote-agent-sessions.md>). | `true` |
| `setting(chat.remoteSessions.tools.enabled)` _(Experimental)_<br/>Provide built-in tools for agents to discover remote hosts, create and inspect remote sessions, and send messages between sessions. Requires remote agent hosts. Turning this off doesn't disconnect hosts or stop remote sessions that are already running. | `false` |
| `setting(chat.agentHost.shellTool.initScript.enabled)` `feature(agent-host-shell-initialization)`<br/>Load `~/.bashrc` on macOS and Linux or your PowerShell profiles on Windows before each SDK shell command in local Copilot Agent Host sessions. With [Python Environments](<https://github.com/docs/python/environments.md#terminal-settings>) installed and `setting(python-envs.terminal.autoActivationType)` set to `shellStartup`, also activate the selected workspace environment. This does not apply to remote sessions or the Agent Host custom terminal tool. | `false` |
| `setting(chat.agents.claude.preferAgentHost)` _(Experimental)_<br/>Run Claude sessions opened from the {% data variables.copilot.agents_window %} on the Agent Host instead of the GitHub Copilot Chat extension. | `true` |
| `setting(chat.editor.codex.preferAgentHost)` _(Experimental)_<br/>Run Codex sessions opened from the {% data variables.copilot.chat_view %} on the Agent Host instead of the OpenAI extension. Only one Codex implementation appears per window. Requires `setting(chat.agentHost.codexAgent.enabled)` and prompts for a restart when changed. | `false` |

### {% data variables.copilot.agent_merge_caps %}

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.agentMerge.enabled)` _(Experimental)_<br/>Enable [{% data variables.copilot.agent_merge %}](<https://github.com/docs/agents/run/agents-window.md#finish-a-pull-request-with-agent-merge>) and its commands. {% data variables.copilot.agent_merge_caps %} monitors a session's pull request and can ask the agent to address selected blockers. | `false` |
| `setting(chat.agentMerge.addressReviews)` _(Experimental)_<br/>Address unresolved review threads, changes-requested reviews, and new pull request comments from repository maintainers or the Copilot pull request reviewer. | `true` |
| `setting(chat.agentMerge.fixCI)` _(Experimental)_<br/>Ask the agent to fix failed required CI checks. | `true` |
| `setting(chat.agentMerge.resolveConflicts)` _(Experimental)_<br/>Ask the agent to update branches that are behind and resolve merge conflicts. | `true` |
| `setting(chat.agentMerge.mergePullRequest)` _(Experimental)_<br/>Control whether {% data variables.copilot.agent_merge %} merges or enqueues the pull request when it is ready. `always` merges after any selected maintenance work, `ifUnchanged` merges only until an agent repair turn changes the pull request, and `never` turns off automatic merging. | `"never"` |
| `setting(chat.agentMerge.mergeMethod)` _(Experimental)_<br/>Select the merge method. `auto` uses the first repository-compatible method in this order: squash, merge commit, or rebase. You can also require `squash`, `merge`, or `rebase`. | `"auto"` |
| `setting(chat.agentMerge.replyAttribution)` _(Experimental)_<br/>Include an automated-reply attribution in review-thread replies posted by {% data variables.copilot.agent_merge %}. | `true` |

## Inline chat settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(inlineChat.defaultModel)`<br/>Configure the default language model for editor inline chat. The model you select persists during the session, but resets to this configured default after {% data variables.product.prodname_vscode_shortname %} reloads. | N/A |
| `setting(inlineChat.askInChat)`<br/>When a file belongs to an active chat editing session, use "Ask in Chat" in the {% data variables.copilot.chat_view %} instead of opening regular inline chat when you press `kb(inlineChat.start)`. When disabled, always opens regular inline chat. | `true` |
| `setting(inlineChat.renderMode)` _(Experimental)_<br/>Configure how inline chat is displayed. `hover`: shows inline chat in a floating overlay, `zone`: shows inline chat in a dedicated zone in the editor. | `"hover"` |
| `setting(inlineChat.finishOnType)`<br/>Finish an editor inline chat session when typing outside of changed regions. | `false` |
| `setting(inlineChat.holdToSpeech)`<br/>Holding the editor inline chat keyboard shortcut (`kb(inlineChat.start)`) automatically enables speech recognition. | `true` |
| `setting(editor.inlineSuggest.syntaxHighlightingEnabled)`<br/>Show syntax highlighting for inline suggestions. | `true` |
| `setting(inlineChat.affordance)` _(Experimental)_<br/>Show a visual hint when you select text to help start inline chat. `off`: no hint, `gutter`: shows in line number area, `editor`: shows at cursor position with lightbulb. | `"off"` |
| `setting(inlineChat.lineEmptyHint)` _(Experimental)_<br/>Show a hint for editor inline chat on an empty line. | `false` |
| `setting(inlineChat.lineNaturalLanguageHint)` _(Experimental)_<br/>Trigger editor inline chat as soon as a line mostly consists of words. | `true` |
| `setting(github.copilot.chat.editor.temporalContext.enabled)` _(Experimental)_<br/>Include recently viewed and edited files in the context for editor inline chat. | `false` |

## Code review settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(github.copilot.chat.reviewSelection.enabled)` _(Preview)_<br/>Enable code review with AI for an editor text selection. | `true` |
| `setting(github.copilot.chat.reviewSelection.instructions)` _(Preview)_<br/>Custom instructions that are added to requests for reviewing the current editor selection with AI. | `[]` |

## Source control settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(git.addAICoAuthor)`<br/>Append a `Co-authored-by:` Git trailer to commit messages for AI-generated changes. Options: `off` (no trailer), `chatAndAgent` (trailer for Copilot Chat or agent mode changes), `all` (trailer for all AI-generated code, including inline completions). See [AI co-author attribution](<https://github.com/docs/sourcecontrol/staging-commits.md#ai-co-author-attribution>). | `"off"` |

## Customization migration settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.customizations.migration.enabled)` _(Experimental)_<br/>Show migration actions, hints, compatibility badges, and compatibility details for customizations that the selected Agent Host harness can't use in their current location or format. | `true` |

Learn how to [migrate agent customizations](<https://github.com/docs/agent-customization/migrate-customizations.md>).

## Custom instructions settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.instructionsFilesLocations)` _(Deprecated)_<br/>Configure custom instruction locations for the Local agent. This setting and the Local agent will be removed in a future release. Use [customization migration](<https://github.com/docs/agent-customization/migrate-customizations.md#move-customizations-from-configured-locations>) to move instructions to supported locations. | `{ ".github/instructions": true, ".claude/rules": true, "~/.copilot/instructions": true, "~/.claude/rules": true }` |
| `setting(chat.includeApplyingInstructions)`<br/>Automatically add instruction files with a matching `applyTo` pattern to chat requests. | `true` |
| `setting(chat.includeReferencedInstructions)`<br/>Automatically add instruction files referenced via Markdown links to chat requests. | `false` |
| `setting(github.copilot.chat.codeGeneration.useInstructionFiles)`<br/>Automatically add custom instructions from `.github/copilot-instructions.md` to chat requests. | `true` |
| `setting(github.copilot.chat.commitMessageGeneration.instructions)` _(Experimental)_<br/>Custom instructions for generating commit messages with AI. | `[]` |
| `setting(github.copilot.chat.pullRequestDescriptionGeneration.instructions)` _(Experimental)_<br/>Custom instructions for generating pull request titles and descriptions with AI. | `[]` |
| `setting(github.copilot.chat.organizationInstructions.enabled)`<br/>Enable discovery of custom instructions defined at the GitHub organization level. | `true` |
| `setting(chat.useCustomizationsInParentRepositories)`<br/>Enable discovery of agent customizations (instructions, prompts, agents, skills, hooks) in [parent repository folders](<https://github.com/docs/agent-customization/overview.md#use-customizations-in-a-monorepo>). Useful for monorepo setups where you open a subfolder rather than the repository root. | `false` |
| `setting(chat.useHooks)` _(Preview)_ <br/>Enable [hooks for the Local harness](<https://github.com/docs/agent-customization/hooks.md#configure-hooks-for-the-local-harness>). This setting does not configure Copilot, Claude, or Codex hook execution. | `true` |
| `setting(chat.hookFilesLocations)` _(Preview)_ <br/>Configure additional [Local hook file locations](<https://github.com/docs/agent-customization/hooks.md#local-hook-file-locations>). Specify paths to folders, which load all `*.json` files, or direct paths to `.json` files. Only relative paths and tilde paths are supported. | `{}` |
| `setting(chat.useClaudeHooks)` _(Preview)_ <br/>Enable the Local harness to parse hooks from Claude configuration files. The Local harness ignores Claude matcher values. | `false` |
| `setting(chat.useAgentsMdFile)` <br/>Enable or disable using `AGENTS.md` files as context for chat requests. | `true` |
| `setting(chat.useClaudeMdFile)`<br/>Enable or disable using `CLAUDE.md` files as always-on custom instructions. | `true` |
| `setting(chat.useNestedAgentsMdFiles)` `feature(nested-agents-md-files)`<br/>Enable or disable using `AGENTS.md` files in subfolders of your workspace as context for chat requests. | `false` |

## Reusable prompt files settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.promptFilesLocations)` _(Deprecated)_<br/>Configure prompt file locations for the Local agent. This setting and the Local agent will be removed in a future release. Use [prompt file migration](<https://github.com/docs/agent-customization/migrate-customizations.md#convert-prompt-files-to-skills>) to convert prompts to skills. | `{ ".github/prompts": true }` |
| `setting(chat.promptFilesRecommendations)` <br/>Enable or disable prompt file recommendations when opening a new chat session. List of key-value pairs of prompt file name and boolean or when clause. | `[]` |

## Custom agents settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.agentFilesLocations)` _(Deprecated)_<br/>Configure custom agent locations for the Local agent. This setting and the Local agent will be removed in a future release. Use [customization migration](<https://github.com/docs/agent-customization/migrate-customizations.md#move-customizations-from-configured-locations>) to move agents to supported locations. | `{ ".github/agents": true, ".claude/agents": true, "~/.copilot/agents": true }` |
| `setting(github.copilot.chat.cli.customAgents.enabled)` <br/>Enable using custom agents in Copilot sessions. | `false` |
| `setting(github.copilot.chat.organizationCustomAgents.enabled)` <br/>Enable discovery of custom agents defined at the GitHub organization level. | `true` |

## Agent skills settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.useAgentSkills)` <br/>Enable support for [agent skills](<https://github.com/docs/agent-customization/agent-skills.md>) in {% data variables.product.prodname_vscode_shortname %}. | `true` |
| `setting(chat.agentSkillsLocations)` _(Deprecated)_<br/>Configure agent skill locations for the Local agent. This setting and the Local agent will be removed in a future release. Use [customization migration](<https://github.com/docs/agent-customization/migrate-customizations.md#move-customizations-from-configured-locations>) to move skills to supported locations. | `{ ".agents/skills": true, ".github/skills": true, ".claude/skills": true, "~/.agents/skills": true, "~/.copilot/skills": true, "~/.claude/skills": true }` |
| `setting(github.copilot.chat.skillTool.enabled)` _(Experimental)_<br/>Enable the dedicated skill tool for invoking [agent skills](<https://github.com/docs/agent-customization/agent-skills.md>). Required to run skills with [`context: fork`](<https://github.com/docs/agent-customization/agent-skills.md#run-a-skill-in-a-forked-context-experimental>) in a separate subagent context. | `false` |

## Observability settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(github.copilot.chat.otel.enabled)` <br/>Enable [OpenTelemetry](<https://github.com/docs/agents/guides/monitoring-agents.md>) emission for Copilot Chat agent interactions. | `false` |
| `setting(github.copilot.chat.otel.exporterType)` <br/>OTel exporter type: `otlp-http`, `otlp-grpc`, `console`, or `file`. | `"otlp-http"` |
| `setting(github.copilot.chat.otel.otlpEndpoint)` <br/>OTLP collector endpoint URL. | `"http://localhost:4318"` |
| `setting(github.copilot.chat.otel.outfile)` <br/>File path for JSON-lines output when using the `file` exporter type. | `""` |
| `setting(github.copilot.chat.otel.captureContent)` <br/>Capture full prompt and response content in OTel spans. May include sensitive information. | `false` |
| `setting(github.copilot.chat.otel.captureIdentity)` <br/>Capture the authenticated account name, operating system username, and machine hostname in OTel data for Local harness sessions. This is independent of content capture and may include sensitive information. | `false` |

## Agent plugins settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(chat.plugins.enabled)`<br/>Enable or disable support for [agent plugins](<https://github.com/docs/agent-customization/agent-plugins.md>). | `false` |
| `setting(chat.plugins.marketplaces)` _(Experimental)_<br/>Configure additional plugin marketplace Git repositories for discovering agent plugins. | `["github/copilot-plugins", "github/awesome-copilot"]` |
| `setting(chat.plugins.enabledPlugins)`<br/>Plugin IDs to enable or disable. When [centrally managed through enterprise policy](<https://github.com/docs/enterprise/manage-ai-settings.md#manage-agent-plugins-and-marketplaces>), `true` force-enables a plugin, `false` force-disables it, and omitted plugins remain under normal user enablement. | `{}` |
| `setting(chat.plugins.strictMarketplaces)` _(Experimental)_<br/>Restrict plugin installation to a list of approved marketplace sources. An empty list blocks all marketplaces. Can be [centrally managed through enterprise policy](<https://github.com/docs/enterprise/manage-ai-settings.md#manage-agent-plugins-and-marketplaces>). | `null` |
| `setting(chat.pluginLocations)` _(Experimental)_<br/>Register locally cloned or downloaded agent plugins by mapping directory paths to an enabled or disabled state. | `{}` |

## Debugging settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(github.copilot.chat.agentDebugLog.fileLogging.enabled)`<br/>Enable file logging for [agent debug logs](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md>) in extension host chat sessions. This setting writes debug events to disk for the Agent Debug Logs panel and the [`/troubleshoot`](<https://github.com/docs/agents/agent-troubleshooting/chat-debug-view.md#attach-debug-events-to-chat>) slash command. Reload the window after changing this setting. | `false` |
| `setting(chat.agentHost.agentDebugLog.enabled)` _(Experimental)_<br/>Enable agent debug logging for Agent Host sessions and show their debug events in the Agent Debug Logs panel. Only sessions that run while this setting is enabled are captured. | `false` |
| `setting(github.copilot.chat.copilotDebugCommand.enabled)` _(Preview)_<br/>Enables the `copilot-debug` terminal command. | `true` |

## Testing settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(github.copilot.chat.generateTests.codeLens)` _(Experimental)_<br/>Show **Generate tests** code lens for symbols that are not covered by current test coverage information. | `false` |
| `setting(github.copilot.chat.setupTests.enabled)` _(Experimental)_<br/>Enables the experimental `/setupTests` intent and prompting in `/tests` generation. | `true` |

## Notebook settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(notebook.experimental.generate)` _(Experimental)_<br/>Enable the **Generate** action to create code cells with notebook inline chat. | `true` |
| `setting(github.copilot.chat.edits.newNotebook.enabled)` _(Experimental)_<br/>Enable the notebook tool in Edit mode (deprecated) to create a new notebook file. | `true` |
| `setting(github.copilot.chat.notebook.followCellExecution.enabled)` _(Experimental)_<br/>Show the currently executing cell in the editor. | `false` |

## Voice and dictation settings

Learn how to [use Voice Mode and built-in dictation](<https://github.com/docs/configure/accessibility/voice.md>).

| Setting and Description | Default |
|------------------------|---------------|
| `setting(agents.voice.enabled)` `feature(voice-mode)`<br/>Controls whether Voice Mode is available for spoken conversations with an agent. | `false` |
| `setting(agents.voice.showButton)` `feature(voice-mode)`<br/>Controls whether the **Voice Mode** button appears in the chat input. The keyboard shortcut remains available when the button is hidden. | `true` |
| `setting(agents.voice.speakResponses)` `feature(voice-mode)`<br/>Controls whether the agent reads responses aloud. Enable `setting(agents.voice.showTranscript)` to read responses as text when spoken responses are off. | `true` |
| `setting(agents.voice.agentProgress)` `feature(voice-mode)`<br/>Controls whether the agent speaks brief progress updates while it works. | `true` |
| `setting(agents.voice.voice)` `feature(voice-mode)`<br/>Selects the voice that reads agent responses aloud. | `"birch_neutral"` |
| `setting(agents.voice.language)` `feature(built-in-dictation)`<br/>Selects the language for speech recognition, dictation, and spoken responses. Use `auto` to use the configured display language when supported, or the system or browser locale otherwise. | `"auto"` |
| `setting(agents.voice.showTranscript)` `feature(voice-mode)`<br/>Shows the Voice Mode transcript in the chat input while Voice Mode is active. | `false` |
| `setting(agents.voice.liveTranscript)` `feature(voice-mode)`<br/>Shows speech word by word while you are speaking. Requires `setting(agents.voice.showTranscript)`. | `false` |
| `setting(agents.voice.handsFree)` `feature(voice-mode)`<br/>Starts listening again after the agent finishes speaking for a hands-free conversation. | `true` |
| `setting(agents.voice.turn.silenceMs)` `feature(voice-mode)`<br/>Sets the trailing silence, in milliseconds, before Voice Mode ends and sends a turn. Set to `-1` to turn off silence-based turn ending. | `800` |
| `setting(agents.voice.turn.stopPhrases)` `feature(voice-mode)`<br/>Lists phrases that end and send a turn when spoken at the end of an utterance. | `["send it"]` |
| `setting(dictation.enabled)` `feature(built-in-dictation)`<br/>Controls whether built-in dictation is available in chat, the {% data variables.copilot.agents_window %}, editors, and terminals. | `true` |
| `setting(dictation.model)` `feature(built-in-dictation)`<br/>Selects the speech recognition model for dictation. | `"nemotron-3.5-asr-streaming-0.6b"` |
| `setting(dictation.showTranscript)` `feature(built-in-dictation)`<br/>Shows interim transcription while you speak. Final text is still inserted when this setting is off. | `true` |
| `setting(dictation.experimental.llmCleanup)` `feature(built-in-dictation)`<br/>Uses a language model to improve punctuation, capitalization, paragraphs, lists, and number formatting in the final transcript. | `true` |

## Accessibility settings

| Setting and Description | Default |
|------------------------|---------------|
| `setting(inlineChat.accessibleDiffView)`<br/>Whether the Inline Chat also renders an accessible diff viewer for its changes. | `"auto"` |
| `setting(accessibility.signals.chatRequestSent)`<br/>Plays a signal - sound (audio cue) and/or announcement (alert) - when a chat request is made. | `{ "sound": "auto", "announcement": "auto" }` |
| `setting(accessibility.signals.chatResponseReceived)`<br/>Plays a sound / audio cue when the response has been received. | `{ "sound": "auto" }` |
| `setting(accessibility.signals.chatEditModifiedFile)`<br/>Plays a sound / audio cue when the file has been modified by chat edits. | `{ "sound": "auto" }` |
| `setting(accessibility.signals.chatUserActionRequired)`<br/>Plays a sound / audio cue when the user needs to take an action in chat. | `{ "sound": "auto", "announcement": "auto" }` |
| `setting(accessibility.signals.confetti)`<br/>Plays a joyful sound when chat feedback or marking a session as done displays confetti. | `{ "sound": "auto" }` |
| `setting(accessibility.signals.lineHasInlineSuggestion)`<br/>Plays a sound / audio cue when the cursor is on a line that has an inline suggestion. | `{ "sound": "auto" }` |
| `setting(accessibility.signals.nextEditSuggestion)`<br/>Plays a sound / audio cue when a next edit suggestion is available. | `{ "sound": "auto", "announcement": "auto" }` |
| `setting(accessibility.verboseChatProgressUpdates)`<br/>Provide verbose updates about chat activity. | `true` |
| `setting(accessibility.verbosity.inlineChat)`<br/>Provide information about how to access the inline editor chat accessibility help menu and alert with hints that describe how to use the feature when the input is focused. | `true` |
| `setting(accessibility.verbosity.inlineCompletions)`<br/>Provide information about how to access the inline suggestions hover and Accessible View. | `true` |
| `setting(accessibility.verbosity.panelChat)`<br/>Provide information about how to access the chat help menu when the chat input is focused. | `true` |
| `setting(accessibility.verbosity.newSessionWelcome)`<br/>Announce the welcome heading when a new-session composer appears in the {% data variables.copilot.agents_window %}. | `true` |
| `setting(accessibility.voice.keywordActivation)`<br/>Controls whether the keyword phrase 'Hey Code' is recognized to start a voice chat session. | `"off"` |
| `setting(accessibility.voice.autoSynthesize)`<br/>Controls whether a textual response should automatically be read out aloud when speech was used as input. | `"off"` |
| `setting(accessibility.voice.speechTimeout)`<br/>Stops terminal dictation after the specified number of milliseconds of silence. Set to `0` to keep listening. | `0` |

## Related resources

* [Get a quick overview of the AI features in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/reference/ai-features-cheat-sheet.md>)


</details>
