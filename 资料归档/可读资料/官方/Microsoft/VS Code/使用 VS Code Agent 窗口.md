# 使用 VS Code Agent 窗口

介绍跨项目分派、监控和审阅任务、隔离会话、测试验证、提交及 PR 操作，适合集中管理异步开发。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/agents-window.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/agents-window.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/使用 VS Code Agent 窗口.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: b3e7a1d4-5f2c-4e9a-8b6d-1c0f3a2e5d47
DateApproved: 10/7/2026
MetaDescription: Run parallel agent sessions, review changes, and finish pull requests in the {% data variables.copilot.agents_window %}.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 使用{% data variables.copilot.agents_window %}

当您跨多个项目委派任务时，可以使用 {% data variables.copilot.agents_window %} 在一个位置跟踪进度并查看结果，而不是在项目窗口之间切换。这个专用的 {% data variables.product.prodname_vscode %} 窗口将重点放在分配工作和审查结果上。

如果您的任务需要在一个项目中频繁编辑、调试或测试，请改用编辑器旁边的 [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>)。本文介绍如何在 {% data variables.copilot.agents_window %} 中开始、监控、查看和完成会话。对于两个界面共享的对话控件，请参阅[在{% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>)中使用聊天。

<!-- <video src="../images/agents-window/agents-demo-20260510.mp4" title="Video showing the {% data variables.copilot.agents_window %} experience in {% data variables.product.prodname_vscode_shortname %} Insiders." controls></video> -->

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Get started with agents">
按照实践教程使用AI agents在{% data variables.product.prodname_vscode_shortname %}中构建应用程序。

* [开始agentic编码教程](<https://github.com/docs/agents/agents-tutorial.md>)

</div>

## 先决条件

* {% data variables.product.prodname_vscode %}已安装。 [下载{% data variables.product.prodname_vscode_shortname %}](<https://github.com/download>)。
* 以下身份验证选项之一：
  * 访问 GitHub Copilot。按照[在{% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/setup/copilot.md>)中设置GitHub Copilot中的步骤登录并激活您的订阅。
  * 用于实验性注销体验的 [Claude API 密钥或其他受支持的自带密钥 (BYOK) 配置 ](<https://github.com/docs/agents/run/agent-harnesses.md#use-claude-without-github-sign-in-experimental>)。
  * [现有的ChatGPT登录Codex](<https://github.com/docs/agents/run/agent-harnesses.md#use-codex-without-github-sign-in-experimental>)以获得实验性注销体验。
  * 为 Agent 主机会话配置的 [BYOK 模型 ](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>)。

## 打开{% data variables.copilot.agents_window %}

{% data variables.copilot.agents_window %} 作为专用的 {% data variables.product.prodname_vscode_shortname %} 窗口与主编辑器窗口一起打开。要打开{% data variables.copilot.agents_window %}，请使用以下方法之一：

* 选择{% data variables.product.prodname_vscode_shortname %}标题栏中的**在Agents**中打开按钮

* 运行 ** 聊天：从命令面板 (`kb(workbench.action.showCommands)`) 打开 {% data variables.copilot.agents_window %}** 命令

* 选择 ** 尝试 {% data variables.product.prodname_vscode_shortname %} 欢迎页面中的新 {% data variables.copilot.agents_window %}** 链接

* 从命令行运行`code --agents`。

* 在 Windows 上，右键单击 {% data variables.product.prodname_vscode_shortname %} 任务栏图标，然后从任务跳转列表中选择 **Agents Window**。

* 在浏览器中打开<https://insiders.vscode.dev/agents>，即可从任何设备使用{% data variables.copilot.agents_window %}。有关设置说明，请参阅 [ 远程 agent 会话 ](<https://github.com/docs/agents/run/remote-agent-sessions.md#use-the-agents-window-in-the-browser>)。

默认情况下，{% data variables.copilot.agents_window %} 需要 GitHub 身份验证才能访问您的 Copilot 订阅和会话。如果您已在 {% data variables.product.prodname_vscode_shortname %} 中登录 GitHub，则当 {% data variables.copilot.agents_window %} 打开时您也将登录。

> [！NOTE]
> 您可以通过在标题栏中右键单击**在Agents**中打开按钮并选择**隐藏“在Agents中打开”**来隐藏它。您仍然可以随时从命令面板或命令行打开 {% data variables.copilot.agents_window %}。

### 无需登录 GitHub 即可打开（实验性）

在桌面上，当至少有以下选项之一可用时，您可以在不登录 GitHub 的情况下打开 {% data variables.copilot.agents_window %}：

* Claude 配置有 API 键或其他受支持的 Claude BYOK 选项。
* Codex 登录到 ChatGPT。
* {% data variables.product.prodname_vscode_shortname %} 中配置的可见 BYOK 模型。启用 `setting(chat.agentHost.byokModels.enabled)` 以使 BYOK 模型可用于 Agent 主机会话。

在打开窗口之前启用 `setting(chat.agentHost.allowSignedOutWhenUsable)`。默认情况下此设置处于关闭状态，但可以通过实验启用它。

要使用ChatGPT订阅，请启用`setting(chat.agentHost.codexAgent.enabled)`，打开{% data variables.copilot.agents_window %}中的帐户菜单，然后选择**登录ChatGPT**。登录后，您可以注销 GitHub 并继续使用 ChatGPT 支持的 Codex 模型。

当您退出 GitHub 时，模型选择器仅显示来自具有可用凭据或密钥的提供商的模型。从帐户菜单登录 GitHub 以添加 Copilot 支持的模型。如果多个提供者提供具有相同名称的模型，则模型选择器会识别提供者。

当 {% data variables.product.prodname_vscode_shortname %} 发现现有的 Claude 配置时，会出现一条通知，指示 Claude 无需登录 GitHub 即可使用。使用 **X** 关闭通知以将其隐藏在当前窗口中。选择**不再显示**以在同一台计算机上的未来窗口中隐藏它。

如果 {% data variables.product.prodname_vscode_shortname %} 找不到可以使用自己的凭据运行的提供程序，{% data variables.copilot.agents_window %} 将显示现有的 GitHub 登录体验。需要 GitHub 身份验证的提供程序、模型和操作在您选择时会提示您登录。基于浏览器的 {% data variables.copilot.agents_window %} 始终需要 GitHub 登录。

## {% data variables.copilot.agents_window %}接口概述

{% data variables.copilot.agents_window %}有以下主要区域：

1. **会话列表**：跨工作区查看和管理会话。默认情况下，会话按工作区分组。选择一个会话以[使其处于活动状态](<#了解活动会话>)。

1. **自定义面板**：访问工作流程和首选项的agent自定义，并在启用时打开[自动化](<#安排重复任务>)。

1. **聊天区域**：查看活跃的agent聊天对话并与之互动

1. **更改面板**：查看活动会话的更改

1. **文件面板**：浏览与活动会话关联的工作区

![{% data variables.copilot.agents_window %} 界面的屏幕截图，显示会话列表、自定义面板、聊天区域、更改面板和文件面板。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-ui-annotated.png>)

默认情况下，**更改**和**文件**视图显示在单独的侧面板中。文件和差异在聊天旁边的编辑器或模式窗口中打开。

## 了解活动会话

{% data variables.copilot.agents_window %} 在您的工作区中拾取您的 agent 会话。 **活动会话**是当前具有焦点的会话。它的对话和项目上下文决定了您所看到的内容以及您的操作适用于哪个工作区。

在会话列表中选择一个会话将其打开并使其处于活动状态。当您[并排打开多个会话](<#open-multiple-sessions-side-by-side>)时，选择会话视图中的任意位置以使该会话处于活动状态。活动会话在会话列表中突出显示。

当活动会话更改时，窗口的以下部分会更新：

|面积|显示活动会话的内容|
|------|--------------------------------------|
|**聊天**|对话历史记录和提示输入。|
|**文件**|会话工作区文件夹或工作树中的文件。此视图包括所有工作区文件，而不仅仅是 agent 更改的文件。|
|**变更**|会话的更改和 Git 操作。使用下拉菜单选择分支更改、未提交的更改、所有更改或上一个 agent 回合的更改。|
|**终端和任务**|在会话的工作区文件夹或工作树中运行的命令和任务。|
|**浏览器**|属于会话的浏览器选项卡和页面状态。|

[快速聊天](<#开始快速聊天>) 不与工作区关联。当快速聊天处于活动状态时，不会显示工作区特定的 **Files** 和 **Changes** 视图。

## 开始 agent 会话

为本地文件夹、GitHub 存储库或 [ 远程工作区 ](<https://github.com/docs/agents/run/remote-agent-sessions.md>) 启动会话。在发送第一个提示之前，您可以附加其他项目、问题和拉取请求作为上下文。对于不属于项目的工作，请启动[快速聊天](<#开始快速聊天>)。

要在 {% data variables.copilot.agents_window %} 中启动新的 agent 会话：

1. 选择侧边栏顶部的**新建**或按`kb(workbench.action.chat.newChat)`。要直接在特定工作区中启动，请将鼠标悬停在会话列表中的工作区上，然后选择 **+**（新会话）。

1. 选择**文件夹**或**存储库**以选择会话的主执行工作区。您选择的第一个文件夹或存储库决定了 agent 运行和更改文件的位置。

要通过 SSH 或开发隧道进行连接，请选择 ** 远程设置 **。如果工作区不受信任，请先查看 [工作区信任 ](<https://github.com/docs/editing/workspaces/workspace-trust.md>) 提示，然后再继续。

1. （可选）为请求附加更多上下文：

    * 选择 **Folder** 或 **Repository** 以附加更多项目作为上下文，而无需添加工作区根目录。
    * 选择**添加上下文**，然后选择**问题...**或**拉取请求...**。如果有多个 GitHub 存储库可用，请在选择问题或拉取请求之前选择一个存储库。
    * 将 GitHub 问题或拉取请求 URL 直接粘贴到提示中。 URL 保留在提示中，并且 {% data variables.product.prodname_vscode_shortname %} 会自动将该项目添加为上下文附件。

![新会话输入的屏幕截图，突出显示文件夹名称和创建 PR 控件。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/new-session-input.png>)

1. 选择可用的 agent 工具，并可选择配置 agent、语言模型、权限级别和隔离模式。对于本地 Git 存储库中的文件夹隔离，您还可以在会话开始之前选择现有的本地分支进行签出。可用选项取决于工作空间。了解如何[选择线束和代码隔离](<https://github.com/docs/agents/run/agent-harnesses.md>)。

1. 输入描述您要完成的任务的提示，然后按 `kbstyle(Enter)` 将其提交到 agent。

    > [!TIP]
    > To start a session in the background without leaving the current session, press `kbstyle(Alt+Enter)` or hold `kbstyle(Alt)` and select **Send**. The new session appears in the sessions list after you send the prompt.

会话列表显示会话的状态以及会话运行时的更改统计信息。该会话也可在主 {% data variables.product.prodname_vscode_shortname %} 窗口中使用。了解有关 [ 管理会话 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md>) 的更多信息。

### 在开发容器中运行会话

`feature(agent-host-dev-containers)`

在开发容器中运行 Agent 主机会话，以便 agent 可以使用项目的工具和依赖项进行构建和测试。使用本地文件夹，或者从 {% data variables.product.prodname_vscode_shortname %} 1.139 开始，使用 SSH、隧道或 WSL 主机上的文件夹。

此选项仅在桌面 {% data variables.copilot.agents_window %} 中可用。启用`setting(chat.agentHost.devContainer.enabled)`。

> [！NOTE]
> 开发容器会话正在逐步推出。如果该设置尚未为您启用，您可以手动启用它。

在开始之前，请确保：

* [Docker 已在包含项目文件夹的计算机上安装并运行 ](<https://github.com/docs/devcontainers/containers.md#installation>)，并且 Docker CLI 在该计算机的 `PATH` 上可用。对于远程文件夹，Docker 必须在远程主机上运行。
* 项目文件夹包含位于 `.devcontainer/devcontainer.json` 或 `.devcontainer.json` 的 [Dev 容器配置 ](<https://github.com/docs/devcontainers/create-dev-container.md>)。
* 对于远程文件夹，其 SSH、隧道或 WSL 连接在 {% data variables.copilot.agents_window %} 中配置，并且源主机通告 Dev Container 支持。了解 [ 连接到远程主机 ](<https://github.com/docs/agents/run/remote-agent-sessions.md>)。

要在开发容器中运行会话：

1. 选择侧边栏顶部的**新建**。

1. 在工作区选择器中，展开符合条件的本地文件夹或已配置的 SSH、隧道或 WSL 主机上的文件夹的菜单，然后选择 **使用开发容器 **。

工作区标签获得 **- Dev Container** 后缀。要在开始会话之前切换回来，请再次展开文件夹菜单，然后为本地文件夹选择 **使用本地 **，或为远程文件夹选择 **使用远程主机 **。

1. 选择可用的 agent 线束，配置会话，然后输入提示。

开发容器会话直接在容器工作区中工作，不能与 **New Worktree** 组合。如果容器无法启动，请在输出视图中查看工作区特定的 **Dev Container** 通道以了解设置和连接详细信息。

**使用开发容器**选项不适用于不受支持的主机或源嵌套在另一个远程环境中的文件夹。

当容器中没有会话正在积极工作、等待输入或持有未发送的草稿时，{% data variables.product.prodname_vscode_shortname %} 将在五分钟后停止容器。批准提示算作等待输入并保持容器运行。继续会话会重新启动容器并重新连接它，而不会丢失对话历史记录。

使用`setting(chat.agentHost.devContainer.idleTimeout)`更改不活动时间。默认值为 `300` 秒。将值设置为 `0` 以关闭自动怠速关闭。将会话标记为已完成或删除它们仍然可以删除容器。

### 从拉取请求启动会话

对于本地 GitHub 支持的工作区，从现有拉取请求启动会话以询问有关建议更改的问题或继续处理拉取请求。该会话包括拉取请求详细信息、更改和评论作为上下文。它使用[隔离的Git工作树](<https://github.com/docs/agents/run/agent-harnesses.md#choose-code-isolation>)来跟踪拉取请求分支。

要从拉取请求启动会话：

1. 在会话列表中，按工作区对会话进行分组。

1. 将鼠标悬停在拉取请求的工作区上，展开 **+**（新会话）操作，然后选择 **来自拉取请求的新会话**。

![屏幕截图显示 {% data variables.copilot.agents_window %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-new-session-from-pull-request.png>) 中的拉取请求操作和拉取请求选择器的新会话

1. 从列表中选择拉取请求。

新会话将打开并显示拉取请求标题。使用聊天询问有关拉取请求的问题或输入提示以进行更多更改。 **Changes** 视图显示拉取请求更改。

1. 检查并验证更改，然后在标题栏中选择 **提交更改** 和 **同步更改**。

**同步更改**更新GitHub上的拉取请求分支，因此现有拉取请求包含您的提交。

> [!NOTE]
> 不支持来自分叉的拉取请求，并且不会出现在拉取请求选择器中。

### 从会话中删除拉取请求

当拉取请求不再与会话相关时，从会话中删除其工件：

* 如果会话有一个拉取请求，请右键单击聊天输入上方的拉取请求药丸，然后选择 **从会话中删除拉取请求工件 **。
* 如果会话有多个拉取请求，请选择拉取请求丸以打开下拉列表，然后为要删除的拉取请求选择 **从会话 ** 中删除拉取请求工件。

删除拉取请求工件只会取消该工件与当前会话的关联。它不会关闭 GitHub 上的拉取请求。如果会话与同一拉取请求有单独的关联，例如当您从该拉取请求启动会话时，该关联将保持不变。

## 开始快速聊天

快速聊天是不限于工作区的轻量级聊天。使用快速聊天来提出问题或启动不属于特定项目的任务。快速聊天显示在会话列表顶部的 **聊天** 部分，与工作区范围的会话分开。

要在 {% data variables.copilot.agents_window %} 中开始新的快速聊天：

1. 在 **Chats** 部分标题 (`kb(sessionsView.newQuickChat)`) 上选择 **+** 或从命令面板运行 **新建快速聊天 ** （`kb(workbench.action.showCommands)`）。

![显示 {% data variables.copilot.agents_window %} 中的快速聊天组的屏幕截图，其中突出显示了用于开始新的快速聊天的 + 按钮。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-quick-chat.png>)

1. 从下拉列表中选择 agent 线束。

1. 在输入框中输入提示，提交至【agent】。 agent在聊天区回复。

要使用语音而不是打字，请从聊天输入启动 [语音模式](<https://github.com/docs/configure/accessibility/voice.md#use-voice-mode>)。语音模式适用于 {% data variables.copilot.agents_window %} 中的活动聊天或 agent 会话。 `feature(voice-mode)`

默认情况下，**Chats** 组在会话列表中保持可见，即使它为空也是如此。要隐藏空的默认组，请将 `setting(sessions.list.showEmptyDefaultGroups)` 设置为 `false`。

### Continue 在工作区中快速聊天

如果快速聊天变得特定于项目，请附加本地工作区并继续相同的对话。会话保留其标题、对话历史记录和当前请求。工作区设置完成后，agent 会自动继续您的请求并访问项目文件。

> [！NOTE]
> 此选项当前可用于在 Agent 主机上使用 Copilot 安全带或 Codex 的快速聊天。对于 Codex，使用交互模式。目标必须是本地文件夹。 [工作树隔离](<https://github.com/docs/agents/run/agent-harnesses.md#choose-code-isolation>)需要一个至少有一次提交的本地Git存储库。

要在工作区中继续快速聊天：

1. 要求 agent 在特定的本地工作区中继续执行任务。

1. 出现提示时，确认文件夹并选择 agent 是否应直接在文件夹中进行更改或使用独立的 Git 工作树。

1. 审核并批准 **设置工作空间 ** 工具确认。

如果该文件夹不受信任，请先查看 [ 工作区信任 ](<https://github.com/docs/editing/workspaces/workspace-trust.md>) 提示，然后再继续。

1. 等待工作区设置完成。快速聊天成为工作区会话，并从 **Chats** 组移动到会话列表中选定的工作区。 agent 然后继续原始请求。

## 查看并完成 agent 会话

当 agent 完成任务时，使会话处于活动状态以检查其工作区并访问其验证和 Git 操作。

### 检查工作区文件和更改

选择 ** 文件 ** 浏览活动会话的工作区文件夹或工作树。选择 ** 更改 ** 以查看分支更改、未提交的更改、所有更改或自上一回合 agent 以来的更改。打开更改的文件以检查其差异或为 agent 留下基于范围的反馈。

![显示 {% data variables.copilot.agents_window %} 中“更改”面板的屏幕截图，其中“文件”和“更改”视图可见。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-changes.png>)

有关反馈、修订、检查点和集成更改的完整说明，请参阅[查看AI-生成的代码编辑](<https://github.com/docs/agents/run/review-code-edits.md>)。

### 验证更改

使用 [ 集成浏览器 ](<https://github.com/docs/debugtest/integrated-browser.md>) 验证活动会话中的 Web 应用程序。在桌面上，在活动会话中打开常规 `.html` 文件默认会在集成浏览器中打开它。 HTML 差异继续在差异编辑器中打开，以便您可以查看更改。您还可以从聊天或终端中选择 `localhost` 链接，右键单击 ** 文件 ** 中的文件，然后选择 ** 在集成浏览器中打开 **，或运行 ** 打开集成浏览器 **命令面板（`kb(workbench.action.showCommands)`）。浏览器选项卡和页面状态属于您打开它们的会话。了解 agents 如何 [ 使用浏览器工具 ](<https://github.com/docs/agents/run/browser-tools.md>) 来检查网页并与之交互。

要运行工作区任务，请选择 **任务** > **添加任务**，然后提供其名称、命令、运行选项和保存位置。从 ** 任务 ** 下拉列表中运行配置的任务。要在活动会话的文件夹或工作树中运行临时命令，请选择标题栏中的 ** 打开终端 **。

### 提交更改

如果活动会话有未提交的更改，请在 ** 更改 ** 视图中选择 ** 提交更改 **。 {% data variables.product.prodname_vscode_shortname %} 根据更改生成提交消息并提交所有当前更改。根据会话类型，您可能还具有 ** 提交和同步更改 ** 操作。

### 创建拉取请求

对于没有拉取请求的 Agent 主机会话，请使用 **创建 PR** 表单来查看拉取请求详细信息并选择创建后会发生的情况：

1. 打开 **更改** 视图，然后选择 **创建 PR**。

表单打开，同时 {% data variables.product.prodname_vscode_shortname %} 生成标题和描述。您可以编辑这些字段，而无需等待生成完成。

1. 查看存储库、源分支、基础分支、标题和描述。

1. 要将拉取请求保留在草稿中直至准备好进行审核，请选择 **创建为草稿 **。

1. 在**创建后**下，选择以下互斥选项之一：

    * **手动合并**：准备好后自行合并拉取请求。
    * **自动合并**：当所需的检查和批准通过时，让GitHub合并拉取请求。
        * **合并方法**：选择**Squash**、**合并提交**或**Rebase**。
    * **{% data variables.copilot.agent_merge_caps %}**：让{% data variables.copilot.agent_merge %}监视拉取请求并要求agent解决阻止者问题。
        * **拦截器**：选择**地址审核**、**修复CI故障**或**解决冲突和分支背后**。
        * **合并拉取请求**：选择**关闭**、**如果未更改**或**就绪时**。 **Off** 使拉取请求保持打开状态，**If Unchanged** 仅当 {% data variables.copilot.agent_merge %} 不进行任何更改时才合并它，**When Ready** 在经过必要的检查和批准后合并它通过。

当选择**创建为草稿**时，GitHub自动合并不可用。它也不会修复失败的检查或解决审核反馈。

1. 选择**创建PR**。

在创建拉取请求之前，将提交任何未提交的更改并推送分支。

该表单会记住草稿设置、合并选项、{% data variables.copilot.agent_merge %} 选项和您的上一个操作（**创建 PR** 或 **发送创建 PR 消息**）。它不记得标题或描述。

要让 agent 创建拉取请求，请打开 **创建 PR** 表单中的 **拉取请求操作 ** 菜单，然后选择 **发送创建 PR消息**。

此操作将标题、说明、草稿状态和**手动合并**或**自动合并**选项发送到会话聊天，包括选择**自动合并**时的合并方法。它不会直接创建拉取请求。 {% data variables.copilot.agent_merge_caps %} 选项不包含在消息中。

### 使用 {% data variables.copilot.agent_merge %} 完成拉取请求

`feature(agent-merge)`

{% data variables.copilot.agent_merge_caps %} 是一项实验性功能，用于监视与 agent 会话关联的拉取请求，并要求 agent 解决阻止程序，直到拉取请求准备好合并为止。根据您的配置方式，{% data variables.copilot.agent_merge %} 可以：

* 解决未解决的审核线程、更改请求的审核以及来自存储库维护者或 Copilot 拉取请求审核者的新评论。
* 修复失败的所需 CI 检查。
* 更新其基础分支后面的分支并解决合并冲突。
* 选定的维护工作完成后，合并拉取请求或将其添加到合并队列中。

首先，启用`setting(chat.agentMerge.enabled)`。

要为现有拉取请求启用 {% data variables.copilot.agent_merge %}：

1. 打开与拉取请求关联的会话。要创建一个，请使用[创建PR表单](<#创建拉取请求>)或按照[从拉取请求启动会话](<#从拉取请求启动会话>)中的步骤操作。

1. 在标题栏中选择**{% data variables.copilot.agent_merge_caps %}**，然后选择**启用{% data variables.copilot.agent_merge %}**。

1. 从 **{% data variables.copilot.agent_merge_caps %}** 菜单中，配置 agent 应解决哪些拦截器以及是否在准备就绪时合并拉取请求。

您还可以从命令面板 (`kb(workbench.action.showCommands)`) 运行 **为活动会话 ** 配置 {% data variables.copilot.agent_merge %}。有关选项的完整列表，请参阅 [{% data variables.copilot.agent_merge %} 设置 ](<https://github.com/docs/agents/reference/ai-settings.md#agent-sessions>)。

<!-- TODO: Add a screenshot of the {% data variables.copilot.agent_merge_caps %} menu in the Agents window title bar. -->

虽然拉取请求是草稿，但可以从 **Changes** 视图中获取 **Mark Ready**。当 {% data variables.copilot.agent_merge %} 解决阻止者问题时，从操作菜单中选择 ** 标记就绪 ** 以使拉取请求准备好接受审核。当所需的检查和可操作的审核反馈明确时，**标记就绪**成为主要操作。

> [!CAUTION]
> {% data variables.copilot.agent_merge_caps %} 开始 agent 转动、更改和同步拉取请求分支，并消耗模型请求。启用它会将会话更改为 [自动驾驶仪](<https://github.com/docs/agents/run/approvals.md#how-autopilot-works>) 与 [辅助权限](<https://github.com/docs/agents/run/approvals.md#permission-levels>)。回顾 {% data variables.copilot.agent_merge %} 启用自动合并之前的选项。

{% data variables.copilot.agent_merge_caps %} 在所需的检查挂起时等待，并在合并或将其添加到合并队列之前立即检查拉取请求是否已准备好。如果会话开始跟踪不同的分支或拉取请求，{% data variables.copilot.agent_merge %} 将关闭并要求您再次启用它。

要查看最近 {% data variables.copilot.agent_merge %} 修复周期中的更改，请打开 ** 更改 ** 视图，然后从更改集下拉列表中选择 **{% data variables.copilot.agent_merge_caps %} 更改 **。此变更集将最新完成的 {% data variables.copilot.agent_merge %} 修复回合与之前完成的用户回合进行比较。在您收到最新消息后，它会保持为空，直到 {% data variables.copilot.agent_merge %} 完成另一轮修复。

要停止监视拉取请求，请在标题栏中选择 **{% data variables.copilot.agent_merge_caps %}**，然后选择 ** 禁用 {% data variables.copilot.agent_merge %}**。

## 处理多个会话

会话列表显示所有工作区中的会话。您可以按工作空间或时间对会话进行分组、创建自定义组、固定会话以及通过拖放重新排列项目。了解如何[组织和管理会话](<https://github.com/docs/agents/run/sessions/manage-sessions.md#sessions-list>)。

### 将会话排列在网格中

在二维网格中安排并行会话以监控进度并比较结果。要在网格中打开另一个会话：

* 要在开始新会话时保持活动会话可见，请按住 `kbstyle(Alt)`（macOS 上的 `kbstyle(Option)`）并选择 **New**。
* 右键单击会话列表中的会话，然后选择**打开到侧面**。
* 将会话从会话列表拖到视图区域中。
* 按住 `kbstyle(Alt)` 并在会话列表中选择一个会话。

将一个会话拖动到另一个会话的顶部、底部、左侧或右侧边缘以创建水平或垂直分割。放置预览显示会话出现的位置。

<video src="../images/agents-window/sessions-grid.mp4" title="Video showing agent sessions arranged in a grid in the {% data variables.copilot.agents_window %}." autoplay loop controls muted></video>

一次只有一个会话窗格处于活动状态。选择一个窗格使其处于活动状态并定向 **文件**、**更改**、**终端**、 **任务**，以及该会话的浏览器操作。在列表中选择另一个会话将替换取消固定的活动视图。

从会话标题或其溢出菜单中打开 **会话布局** 子菜单。从那里，您可以选择**在平衡网格中排列会话**，将会话集中在一个方向上，移动活动会话，或者增加或减少其宽度或高度。默认方向对焦快捷键为 `kb(sessions.focusSessionLeft)`、`kb(sessions.focusSessionRight)`、`kb(sessions.focusSessionAbove)` 和 `kb(sessions.focusSessionBelow)`。

使用会话标头中的 **Maximize** 来展开活动会话，并使用 **Restore** 返回到网格。这些操作独立于 **会话布局** 子菜单。

当会话具有焦点时按 `kb(sessions.closeAllSessions)` 可关闭所有可见窗格并返回到新会话视图。此操作不会删除或存档会话。

{% data variables.copilot.agents_window %} 在重新加载后恢复对话、草稿、窗格几何形状、活动会话和最大化状态。在较窄或手机宽度下，仅显示活动会话。当您加宽窗口时，网格会返回。

### 在一个会话中处理多个聊天

在受支持的 Agent 主持会话中，使用聊天选项卡和分组来保持多个对话可见。水平或垂直排列交互式对等聊天、[侧面聊天](<https://github.com/docs/agents/run/sessions/manage-sessions.md#ask-side-questions>)和[只读子代理聊天](<https://github.com/docs/agents/run/subagents.md#agents-window>)。

主聊天和交互式同伴聊天也可以在编辑器的 [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md#switch-chats-within-a-session>) 中找到，您可以从 ** 会话 ** 视图而不是 {% data variables.copilot.agents_window %} 聊天选项卡中选择它们。

有关聊天共享的内容、如何创建聊天以及每个界面中的控件，请参阅[在会话中运行多个聊天](<https://github.com/docs/agents/run/sessions/manage-sessions.md#run-multiple-chats-in-a-session>)。

## 安排重复任务

`feature(automations)`

自动化根据保存的提示和计划运行重复的 agent 任务。启用`setting(chat.automations.enabled)`，然后在边栏中选择**自动化**即可开始。您可以按需运行任务，也可以将其安排为每小时、每天或每周运行。

了解如何[创建自动化并查看其结果](<https://github.com/docs/agents/run/automations.md>)。

## 配置{% data variables.copilot.agents_window %}

您可以个性化聊天、调整窗口布局、配置设置和扩展名，以及选择 Markdown 文件的打开方式。请参阅 [ 配置 {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window-configuration.md>) 以了解所有特定于窗口的选项。

## 局限性

* Agent 主机 Codex 会话可以在 {% data variables.copilot.agents_window %} 和主 {% data variables.product.prodname_vscode_shortname %} 窗口中运行。 OpenAI 扩展中的本地线束和 Codex 会话仅在主 {% data variables.product.prodname_vscode_shortname %} 窗口中运行。

* Copilot 仅 GitHub 支持的存储库支持云会话。对于非GitHub项目，您仍然可以在{% data variables.copilot.agents_window %}中使用Copilot。

* agents 下拉列表当前没有计划 agent。您可以在 Copilot 或 Claude agent 会话中使用 `/plan` 命令。在 Copilot 会话中，当您要求计划 agent 创建计划时，它也会自动调用。

* Copilot 和 Claude 会话当前支持在单个会话中运行多个聊天。

* {% data variables.copilot.agents_window %} 尚不支持多根会话。您可以要求 agent 在单个会话中跨项目工作。

## 后续步骤

* [在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>) 中使用聊天 - 发送和引导请求、添加上下文以及导航对话。
* [管理 agent 会话](<https://github.com/docs/agents/run/sessions/manage-sessions.md>) - 组织、分叉、存档和导出会话。
* [查看 AI-生成的代码编辑](<https://github.com/docs/agents/run/review-code-edits.md>) - 检查、修改和集成 agent 更改。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Use the {% data variables.copilot.agents_window %}

When you delegate tasks across several projects, use the {% data variables.copilot.agents_window %} to track progress and review results in one place instead of switching between project windows. This dedicated {% data variables.product.prodname_vscode %} window keeps the focus on assigning work and reviewing outcomes.

If your task needs frequent editing, debugging, or testing in one project, use the [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>) beside your editor instead. This article shows how to start, monitor, review, and finish sessions in the {% data variables.copilot.agents_window %}. For conversation controls shared by both interfaces, see [Use chat in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>).

<!-- <video src="../images/agents-window/agents-demo-20260510.mp4" title="Video showing the {% data variables.copilot.agents_window %} experience in {% data variables.product.prodname_vscode_shortname %} Insiders." controls></video> -->

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Get started with agents">
Follow a hands-on tutorial to build an app with AI agents in {% data variables.product.prodname_vscode_shortname %}.

* [Start agentic coding tutorial](<https://github.com/docs/agents/agents-tutorial.md>)

</div>

## Prerequisites

* {% data variables.product.prodname_vscode %} installed. [Download {% data variables.product.prodname_vscode_shortname %}](<https://github.com/download>).
* One of the following authentication options:
  * Access to GitHub Copilot. Follow the steps in [Set up GitHub Copilot in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/setup/copilot.md>) to sign in and activate your subscription.
  * A [Claude API key or another supported bring-your-own-key (BYOK) configuration](<https://github.com/docs/agents/run/agent-harnesses.md#use-claude-without-github-sign-in-experimental>) for the experimental signed-out experience.
  * An [existing ChatGPT sign-in for Codex](<https://github.com/docs/agents/run/agent-harnesses.md#use-codex-without-github-sign-in-experimental>) for the experimental signed-out experience.
  * A [BYOK model](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>) configured for Agent Host sessions.

## Open the {% data variables.copilot.agents_window %}

The {% data variables.copilot.agents_window %} opens as a dedicated {% data variables.product.prodname_vscode_shortname %} window alongside your main editor window. To open the {% data variables.copilot.agents_window %}, use one of the following methods:

* Select the **Open in Agents** button in the {% data variables.product.prodname_vscode_shortname %} title bar

* Run the **Chat: Open {% data variables.copilot.agents_window %}** command from the Command Palette (`kb(workbench.action.showCommands)`)

* Select **Try out the new {% data variables.copilot.agents_window %}** link from the {% data variables.product.prodname_vscode_shortname %} welcome page

* Run `code --agents` from the command line.

* On Windows, right-click the {% data variables.product.prodname_vscode_shortname %} taskbar icon and select **Agents Window** from the Tasks jump list.

* Open <https://insiders.vscode.dev/agents> in a browser to use the {% data variables.copilot.agents_window %} from any device. See [remote agent sessions](<https://github.com/docs/agents/run/remote-agent-sessions.md#use-the-agents-window-in-the-browser>) for setup instructions.

By default, the {% data variables.copilot.agents_window %} requires GitHub authentication to access your Copilot subscription and sessions. If you're already signed in to GitHub in {% data variables.product.prodname_vscode_shortname %}, you'll also be signed in when the {% data variables.copilot.agents_window %} opens.

> [!NOTE]
> You can hide the **Open in Agents** button by right-clicking it in the title bar and selecting **Hide 'Open in Agents'**. You can still open the {% data variables.copilot.agents_window %} at any time from the Command Palette or command line.

### Open without GitHub sign-in (Experimental)

On desktop, you can open the {% data variables.copilot.agents_window %} without signing in to GitHub when at least one of these options is available:

* Claude configured with an API key or another supported Claude BYOK option.
* Codex signed in to ChatGPT.
* A visible BYOK model configured in {% data variables.product.prodname_vscode_shortname %}. Enable `setting(chat.agentHost.byokModels.enabled)` to make BYOK models available to Agent Host sessions.

Enable `setting(chat.agentHost.allowSignedOutWhenUsable)` before you open the window. This setting is off by default, but it might be enabled by an experiment.

To use a ChatGPT subscription, enable `setting(chat.agentHost.codexAgent.enabled)`, open the account menu in the {% data variables.copilot.agents_window %}, and select **Sign in to ChatGPT**. After you sign in, you can sign out of GitHub and continue to use ChatGPT-backed Codex models.

While you're signed out of GitHub, the model picker only shows models from providers with available credentials or keys. Sign in to GitHub from the account menu to add Copilot-backed models. If multiple providers offer a model with the same name, the model picker identifies the provider.

When {% data variables.product.prodname_vscode_shortname %} discovers an existing Claude configuration, a notification indicates that Claude is available without GitHub sign-in. Dismiss the notification with **X** to hide it for the current window. Select **Don't Show Again** to hide it for future windows on the same machine.

If {% data variables.product.prodname_vscode_shortname %} doesn't find a provider that can run with its own credentials, the {% data variables.copilot.agents_window %} shows the existing GitHub sign-in experience. Providers, models, and operations that require GitHub authentication prompt you to sign in when you select them. The browser-based {% data variables.copilot.agents_window %} always requires GitHub sign-in.

## {% data variables.copilot.agents_window %} interface overview

The {% data variables.copilot.agents_window %} has the following main areas:

1. **Sessions list**: view and manage sessions across workspaces. By default, sessions are grouped by workspace. Select a session to [make it active](<#understand-the-active-session>).

1. **Customizations panel**: access agent customizations for your workflow and preferences, and open [Automations](<#schedule-recurring-tasks>) when enabled.

1. **Chat area**: view and interact with the active agent chat conversation

1. **Changes panel**: review changes for the active session

1. **Files panel**: browse the workspace associated with the active session

![Screenshot of the {% data variables.copilot.agents_window %} interface, showing the sessions list, customizations panel, chat area, changes panel, and files panel.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-ui-annotated.png>)

By default, the **Changes** and **Files** views appear in a separate side panel. Files and diffs open in an editor beside the chat or in a modal window.

## Understand the active session

The {% data variables.copilot.agents_window %} picks up your agent sessions across your workspaces. The **active session** is the session that currently has focus. Its conversation and project context determine what you see and which workspace your actions apply to.

Select a session in the sessions list to open it and make it active. When you [open multiple sessions side by side](<#open-multiple-sessions-side-by-side>), select anywhere in a session view to make that session active. The active session is highlighted in the sessions list.

The following parts of the window update when the active session changes:

| Area | What it shows for the active session |
|------|--------------------------------------|
| **Chat** | The conversation history and prompt input. |
| **Files** | The files in the session's workspace folder or worktree. This view includes all workspace files, not only files changed by the agent. |
| **Changes** | The changes and Git actions for the session. Use the dropdown to choose the branch changes, uncommitted changes, all changes, or changes from the last agent turn. |
| **Terminal and Tasks** | Commands and tasks that run in the session's workspace folder or worktree. |
| **Browser** | Browser tabs and page state that belong to the session. |

[Quick chats](<#start-a-quick-chat>) aren't associated with a workspace. When a quick chat is active, the workspace-specific **Files** and **Changes** views aren't shown.

## Start an agent session

Start a session for a local folder, GitHub repository, or [remote workspace](<https://github.com/docs/agents/run/remote-agent-sessions.md>). You can attach other projects, issues, and pull requests as context before you send the first prompt. For work that doesn't belong to a project, start a [quick chat](<#start-a-quick-chat>).

To start a new agent session in the {% data variables.copilot.agents_window %}:

1. Select **New** at the top of the sidebar or press `kb(workbench.action.chat.newChat)`. To start directly in a specific workspace, hover over the workspace in the sessions list and select **+** (New Session).

1. Select **Folder** or **Repository** to choose the primary execution workspace for the session. The first folder or repository you select determines where the agent runs and changes files.

    To connect through SSH or a dev tunnel, select **Remote Setup**. If the workspace isn't trusted, review the [Workspace Trust](<https://github.com/docs/editing/workspaces/workspace-trust.md>) prompt before you continue.

1. Optionally, attach more context to the request:

    * Select **Folder** or **Repository** to attach more projects as context without adding workspace roots.
    * Select **Add Context**, and then select **Issue...** or **Pull Request...**. If multiple GitHub repositories are available, select a repository before you choose the issue or pull request.
    * Paste a GitHub issue or pull request URL directly into the prompt. The URL remains in the prompt, and {% data variables.product.prodname_vscode_shortname %} automatically adds the item as a context attachment.

    ![Screenshot of the new-session input highlighting the folder name and the Create PR control.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/new-session-input.png>)

1. Choose an available agent harness, and optionally configure the agent, language model, permission level, and isolation mode. For folder isolation in a local Git repository, you can also select an existing local branch to check out before the session starts. The available options depend on the workspace. Learn how to [choose a harness and code isolation](<https://github.com/docs/agents/run/agent-harnesses.md>).

1. Type a prompt that describes what you want to accomplish, and press `kbstyle(Enter)` to submit it to the agent.

    > [!TIP]
    > To start a session in the background without leaving the current session, press `kbstyle(Alt+Enter)` or hold `kbstyle(Alt)` and select **Send**. The new session appears in the sessions list after you send the prompt.

The sessions list shows the session's status and change statistics while it works. The session is also available in the main {% data variables.product.prodname_vscode_shortname %} window. Learn more about [managing sessions](<https://github.com/docs/agents/run/sessions/manage-sessions.md>).

### Run a session in a Dev Container

`feature(agent-host-dev-containers)`

Run an Agent Host session in a Dev Container so the agent can build and test with your project's tools and dependencies. Use a local folder or, starting in {% data variables.product.prodname_vscode_shortname %} 1.139, a folder on an SSH, Tunnel, or WSL host.

This option is available only in the desktop {% data variables.copilot.agents_window %}. Enable `setting(chat.agentHost.devContainer.enabled)`.

> [!NOTE]
> Dev Container sessions are rolling out gradually. If the setting isn't enabled for you yet, you can enable it manually.

Before you start, make sure that:

* [Docker is installed and running](<https://github.com/docs/devcontainers/containers.md#installation>) on the machine that contains the project folder, and the Docker CLI is available on that machine's `PATH`. For a remote folder, Docker must run on the remote host.
* The project folder contains a [Dev Container configuration](<https://github.com/docs/devcontainers/create-dev-container.md>) at `.devcontainer/devcontainer.json` or `.devcontainer.json`.
* For a remote folder, its SSH, Tunnel, or WSL connection is configured in the {% data variables.copilot.agents_window %}, and the source host advertises Dev Container support. Learn about [connecting to remote hosts](<https://github.com/docs/agents/run/remote-agent-sessions.md>).

To run a session in a Dev Container:

1. Select **New** at the top of the sidebar.

1. In the workspace picker, expand the menu for an eligible local folder or a folder on a configured SSH, Tunnel, or WSL host, and select **Use Dev Container**.

    The workspace label gains the **- Dev Container** suffix. To switch back before you start the session, expand the folder menu again and select **Use Local** for a local folder or **Use Remote Host** for a remote folder.

1. Choose an available agent harness, configure the session, and enter your prompt.

Dev Container sessions work directly in the container workspace and can't be combined with **New Worktree**. If the container fails to start, review the workspace-specific **Dev Container** channel in the Output view for setup and connection details.

The **Use Dev Container** option isn't available for unsupported hosts or for folders whose source is nested inside another remote environment.

When no session in the container is actively working, waiting for input, or holding an unsent draft, {% data variables.product.prodname_vscode_shortname %} stops the container after five minutes. Approval prompts count as waiting for input and keep the container running. Continuing a session restarts the container and reconnects it without losing the conversation history.

Use `setting(chat.agentHost.devContainer.idleTimeout)` to change the inactivity period. The default is `300` seconds. Set the value to `0` to turn off automatic idle shutdown. Marking sessions as done or deleting them can still remove the container.

### Start a session from a pull request

For a local GitHub-backed workspace, start a session from an existing pull request to ask questions about the proposed changes or continue working on the pull request. The session includes the pull request details, changes, and comments as context. It uses an [isolated Git worktree](<https://github.com/docs/agents/run/agent-harnesses.md#choose-code-isolation>) that tracks the pull request branch.

To start a session from a pull request:

1. In the sessions list, group sessions by workspace.

1. Hover over the workspace for the pull request, expand the **+** (New Session) action, and select **New Session from Pull Request**.

    ![Screenshot showing the New Session from Pull Request action and pull request picker in the {% data variables.copilot.agents_window %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-new-session-from-pull-request.png>)

1. Select a pull request from the list.

    The new session opens with the pull request title. Use the chat to ask questions about the pull request or enter a prompt to make more changes. The **Changes** view shows the pull request changes.

1. Review and validate the changes, then select **Commit Changes** and **Sync Changes** in the title bar.

    **Sync Changes** updates the pull request branch on GitHub, so the existing pull request includes your commits.

> [!NOTE]
> Pull requests from forks are not supported and don't appear in the pull request picker.

### Remove a pull request from a session

When a pull request is no longer relevant to a session, remove its artifact from the session:

* If the session has one pull request, right-click the pull request pill above the chat input, and then select **Remove Pull Request Artifact from Session**.
* If the session has multiple pull requests, select the pull requests pill to open the dropdown, and then select **Remove Pull Request Artifact from Session** for the pull request you want to remove.

Removing a pull request artifact only disassociates the artifact from the current session. It doesn't close the pull request on GitHub. If the session has a separate association with the same pull request, such as when you start a session from that pull request, that association remains unchanged.

## Start a quick chat

Quick chats are lightweight chats that aren't scoped to a workspace. Use a quick chat to ask a question or start a task that doesn't belong to a specific project. Quick chats appear in the **Chats** section at the top of the sessions list, separate from your workspace-scoped sessions.

To start a new quick chat in the {% data variables.copilot.agents_window %}:

1. Select **+** on the **Chats** section header (`kb(sessionsView.newQuickChat)`) or run **New Quick Chat** from the Command Palette (`kb(workbench.action.showCommands)`).

    ![Screenshot showing the quick chats group in the {% data variables.copilot.agents_window %}, with + button to start a new quick chat highlighted.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-quick-chat.png>)

1. Choose the agent harness from the dropdown.

1. Enter a prompt in the input box to submit it to the agent. The agent responds in the chat area.

To use speech instead of typing, start [Voice Mode](<https://github.com/docs/configure/accessibility/voice.md#use-voice-mode>) from the chat input. Voice Mode works with the active chat or agent session in the {% data variables.copilot.agents_window %}. `feature(voice-mode)`

By default, the **Chats** group stays visible in the sessions list even when it's empty. To hide empty default groups, set `setting(sessions.list.showEmptyDefaultGroups)` to `false`.

### Continue a quick chat in a workspace

If a quick chat becomes project-specific, attach a local workspace and continue the same conversation. The session retains its title, conversation history, and current request. After workspace setup finishes, the agent automatically continues your request with access to the project files.

> [!NOTE]
> This option is currently available for quick chats that use the Copilot harness or Codex on the Agent Host. For Codex, use Interactive mode. The target must be a local folder. [Worktree isolation](<https://github.com/docs/agents/run/agent-harnesses.md#choose-code-isolation>) requires a local Git repository with at least one commit.

To continue a quick chat in a workspace:

1. Ask the agent to continue the task in a specific local workspace.

1. When prompted, confirm the folder and choose whether the agent should make changes directly in the folder or use an isolated Git worktree.

1. Review and approve the **Set Workspace** tool confirmation.

    If the folder isn't trusted, review the [Workspace Trust](<https://github.com/docs/editing/workspaces/workspace-trust.md>) prompt before you proceed.

1. Wait for workspace setup to finish. The quick chat becomes a workspace session and moves from the **Chats** group to the selected workspace in the sessions list. The agent then continues the original request.

## Review and finish an agent session

When an agent finishes a task, make the session active to inspect its workspace and access its validation and Git actions.

### Inspect workspace files and changes

Select **Files** to browse the active session's workspace folder or worktree. Select **Changes** to review branch changes, uncommitted changes, all changes, or changes from the last agent turn. Open a changed file to inspect its diff or leave range-based feedback for the agent.

![Screenshot showing the Changes panel in the {% data variables.copilot.agents_window %}, with the Files and Changes views visible.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-changes.png>)

For complete instructions about feedback, revisions, checkpoints, and integrating changes, see [Review AI-generated code edits](<https://github.com/docs/agents/run/review-code-edits.md>).

### Validate changes

Use the [integrated browser](<https://github.com/docs/debugtest/integrated-browser.md>) to validate web applications in the active session. On desktop, opening a regular `.html` file in the active session opens it in the integrated browser by default. HTML diffs continue to open in the diff editor so you can review changes. You can also select a `localhost` link from the chat or terminal, right-click a file in **Files** and select **Open in Integrated Browser**, or run **Open Integrated Browser** from the Command Palette (`kb(workbench.action.showCommands)`). Browser tabs and page state belong to the session where you open them. Learn how agents can [use browser tools](<https://github.com/docs/agents/run/browser-tools.md>) to inspect and interact with a web page.

To run a workspace task, select **Tasks** > **Add Task**, and then provide its name, command, run options, and save location. Run configured tasks from the **Tasks** dropdown. To run an ad hoc command in the active session's folder or worktree, select **Open Terminal** in the title bar.

### Commit changes

If the active session has uncommitted changes, select **Commit Changes** in the **Changes** view. {% data variables.product.prodname_vscode_shortname %} generates a commit message based on the changes and commits all current changes. Depending on the session type, you might also have a **Commit and Sync Changes** action.

### Create a pull request

For an Agent Host session without a pull request, use the **Create PR** form to review the pull request details and choose what happens after creation:

1. Open the **Changes** view, and then select **Create PR**.

    The form opens while {% data variables.product.prodname_vscode_shortname %} generates a title and description. You can edit these fields without waiting for generation to finish.

1. Review the repository, source branch, base branch, title, and description.

1. To keep the pull request in draft until it is ready for review, select **Create as Draft**.

1. Under **After creation**, choose one of these mutually exclusive options:

    * **Merge Manually**: merge the pull request yourself when it is ready.
    * **Auto-Merge**: let GitHub merge the pull request when required checks and approvals pass.
        * **Merge method**: select **Squash**, **Merge Commit**, or **Rebase**.
    * **{% data variables.copilot.agent_merge_caps %}**: have {% data variables.copilot.agent_merge %} monitor the pull request and ask the agent to address blockers.
        * **Blockers**: select **Address Reviews**, **Fix CI Failures**, or **Resolve Conflicts and Behind Branches**.
        * **Merge Pull Request**: select **Off**, **If Unchanged**, or **When Ready**. **Off** leaves the pull request open, **If Unchanged** merges it only if {% data variables.copilot.agent_merge %} makes no changes, and **When Ready** merges it after required checks and approvals pass.

    GitHub auto-merge is unavailable when **Create as Draft** is selected. It also does not fix failed checks or address review feedback.

1. Select **Create PR**.

    Any uncommitted changes are committed and the branch is pushed before the pull request is created.

The form remembers the draft setting, merge options, {% data variables.copilot.agent_merge %} options, and your last action (**Create PR** or **Send Create PR Message**). It does not remember titles or descriptions.

To have the agent create the pull request instead, open the **Pull Request Actions** menu in the **Create PR** form and select **Send Create PR Message**.

This action sends the title, description, draft status, and the **Merge Manually** or **Auto-Merge** choice to the session chat, including the merge method when you select **Auto-Merge**. It does not create the pull request directly. {% data variables.copilot.agent_merge_caps %} options are not included in the message.

### Finish a pull request with {% data variables.copilot.agent_merge %}

`feature(agent-merge)`

{% data variables.copilot.agent_merge_caps %} is an experimental feature that monitors the pull request associated with an agent session and asks the agent to address blockers until the pull request is ready to merge. Depending on how you configure it, {% data variables.copilot.agent_merge %} can:

* Address unresolved review threads, changes-requested reviews, and new comments from repository maintainers or the Copilot pull request reviewer.
* Fix failed required CI checks.
* Update a branch that is behind its base branch and resolve merge conflicts.
* Merge the pull request or add it to the merge queue after the selected maintenance work is complete.

First, enable `setting(chat.agentMerge.enabled)`.

To enable {% data variables.copilot.agent_merge %} for an existing pull request:

1. Open a session that is associated with a pull request. To create one, use the [Create PR form](<#create-a-pull-request>) or follow the steps in [Start a session from a pull request](<#start-a-session-from-a-pull-request>).

1. Select **{% data variables.copilot.agent_merge_caps %}** in the title bar, and then select **Enable {% data variables.copilot.agent_merge %}**.

1. From the **{% data variables.copilot.agent_merge_caps %}** menu, configure which blockers the agent should address and whether to merge the pull request when it is ready.

    You can also run **Configure {% data variables.copilot.agent_merge %} for Active Session** from the Command Palette (`kb(workbench.action.showCommands)`). For a complete list of options, see the [{% data variables.copilot.agent_merge %} settings](<https://github.com/docs/agents/reference/ai-settings.md#agent-sessions>).

<!-- TODO: Add a screenshot of the {% data variables.copilot.agent_merge_caps %} menu in the Agents window title bar. -->

While the pull request is a draft, **Mark Ready** is available from the **Changes** view. While {% data variables.copilot.agent_merge %} addresses blockers, select **Mark Ready** from the action menu to make the pull request ready for review. When required checks and actionable review feedback are clear, **Mark Ready** becomes the primary action.

> [!CAUTION]
> {% data variables.copilot.agent_merge_caps %} starts agent turns, changes and syncs the pull request branch, and consumes model requests. Enabling it changes the session to [Autopilot](<https://github.com/docs/agents/run/approvals.md#how-autopilot-works>) with [Assisted permissions](<https://github.com/docs/agents/run/approvals.md#permission-levels>). Review the {% data variables.copilot.agent_merge %} options before you enable automatic merging.

{% data variables.copilot.agent_merge_caps %} waits while required checks are pending and checks that the pull request is ready immediately before it merges or adds it to the merge queue. If the session starts tracking a different branch or pull request, {% data variables.copilot.agent_merge %} turns off and requires you to enable it again.

To review the changes from the most recent {% data variables.copilot.agent_merge %} repair cycle, open the **Changes** view and select **{% data variables.copilot.agent_merge_caps %} Changes** from the changeset dropdown. This changeset compares the latest completed {% data variables.copilot.agent_merge %} repair turn with the preceding completed user turn. It remains empty after your latest message until {% data variables.copilot.agent_merge %} completes another repair turn.

To stop monitoring the pull request, select **{% data variables.copilot.agent_merge_caps %}** in the title bar, and then select **Disable {% data variables.copilot.agent_merge %}**.

## Work with multiple sessions

The sessions list shows sessions across all your workspaces. You can group sessions by workspace or time, create custom groups, pin sessions, and rearrange items with drag and drop. Learn how to [organize and manage sessions](<https://github.com/docs/agents/run/sessions/manage-sessions.md#sessions-list>).

### Arrange sessions in a grid

Arrange parallel sessions in a two-dimensional grid to monitor progress and compare results. To open another session in the grid:

* To keep the active session visible while you start a new session, hold `kbstyle(Alt)` (`kbstyle(Option)` on macOS) and select **New**.
* Right-click a session in the sessions list and select **Open to the Side**.
* Drag a session from the sessions list into the view area.
* Hold `kbstyle(Alt)` and select a session in the sessions list.

Drag a session to the top, bottom, left, or right edge of another session to create a horizontal or vertical split. A placement preview shows where the session appears.

<video src="../images/agents-window/sessions-grid.mp4" title="Video showing agent sessions arranged in a grid in the {% data variables.copilot.agents_window %}." autoplay loop controls muted></video>

Only one session pane is active at a time. Select a pane to make it active and direct the **Files**, **Changes**, **Terminal**, **Tasks**, and browser actions to that session. Selecting another session in the list replaces an unpinned active view.

Open the **Session Layout** submenu from a session header or its overflow menu. From there, you can select **Arrange Sessions in a Balanced Grid**, focus a session in a direction, move the active session, or increase or decrease its width or height. The default directional focus shortcuts are `kb(sessions.focusSessionLeft)`, `kb(sessions.focusSessionRight)`, `kb(sessions.focusSessionAbove)`, and `kb(sessions.focusSessionBelow)`.

Use **Maximize** in the session header to expand the active session, and use **Restore** to return to the grid. These actions are separate from the **Session Layout** submenu.

Press `kb(sessions.closeAllSessions)` while a session has focus to close all visible panes and return to the new-session view. This action does not delete or archive the sessions.

The {% data variables.copilot.agents_window %} restores conversations, drafts, pane geometry, the active session, and the maximized state after a reload. At narrow or phone width, only the active session is shown. The grid returns when you widen the window.

### Work with multiple chats in a session

In supported Agent Host sessions, use chat tabs and split groups to keep several conversations visible. Arrange interactive peer chats, [side chats](<https://github.com/docs/agents/run/sessions/manage-sessions.md#ask-side-questions>), and [read-only subagent chats](<https://github.com/docs/agents/run/subagents.md#agents-window>) horizontally or vertically.

The main chat and interactive peer chats are also available in the editor's [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md#switch-chats-within-a-session>), where you select them from the **Sessions** view rather than the {% data variables.copilot.agents_window %} chat tabs.

For what chats share, how to create them, and the controls in each interface, see [Run multiple chats in a session](<https://github.com/docs/agents/run/sessions/manage-sessions.md#run-multiple-chats-in-a-session>).

## Schedule recurring tasks

`feature(automations)`

Automations run recurring agent tasks from a saved prompt and schedule. Enable `setting(chat.automations.enabled)`, and then select **Automations** in the sidebar to get started. You can run a task on demand or schedule it to run hourly, daily, or weekly.

Learn how to [create an automation and review its results](<https://github.com/docs/agents/run/automations.md>).

## Configure the {% data variables.copilot.agents_window %}

You can personalize chat, adjust the window layout, configure settings and extensions, and choose how Markdown files open. See [Configure the {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window-configuration.md>) for all window-specific options.

## Limitations

* Agent Host Codex sessions can run in both the {% data variables.copilot.agents_window %} and the main {% data variables.product.prodname_vscode_shortname %} window. The Local harness and Codex sessions from the OpenAI extension run only in the main {% data variables.product.prodname_vscode_shortname %} window.

* Copilot Cloud sessions are only supported for GitHub-backed repositories. For non-GitHub projects, you can still use Copilot in the {% data variables.copilot.agents_window %}.

* The agents dropdown currently doesn't have the plan agent. You can use the `/plan` command in a Copilot or Claude agent session. In Copilot sessions, the plan agent is also automatically invoked when you ask it to create a plan.

* Running multiple chats in a single session is currently supported for Copilot and Claude sessions.

* Multi-root sessions are not yet supported in the {% data variables.copilot.agents_window %}. You can ask the agent to work across projects in a single session.

## Next steps

* [Use chat in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>) - send and steer requests, add context, and navigate conversations.
* [Manage agent sessions](<https://github.com/docs/agents/run/sessions/manage-sessions.md>) - organize, fork, archive, and export sessions.
* [Review AI-generated code edits](<https://github.com/docs/agents/run/review-code-edits.md>) - inspect, revise, and integrate agent changes.


</details>
