# 选择 VS Code 的 Agent 执行框架

比较 Copilot、Claude、Codex 和云端目标的运行方式、认证、代码隔离与审批，以及会话交接和角色选择。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/agent-harnesses.md>)；源码提交（存档版）：[2026-10-08](<https://github.com/microsoft/vscode-docs/commit/a88484276b0f7e053f9145da14de6a98f32b61c5>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/agent-harnesses.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/选择 VS Code 的 Agent 执行框架.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 5b1e6f94-2c73-4a80-9d15-7f3c8e2a6b41
DateApproved: 10/7/2026
MetaDescription: Choose an agent harness in {% data variables.product.prodname_vscode %} and configure sessions, permissions, and code isolation.
MetaSocialImage: ../../images/shared/github-copilot-social.png
Keywords:
- copilot
- ai
- agents
- agent harness
- copilot harness
- session target
- claude
- codex
- "{% data variables.copilot.copilot_cloud_agent_short %}"
- worktree
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 选择并使用 agent 安全带

从 [{% data variables.product.prodname_copilot_short %} 安全带 ](<#use-the-copilot-harness>) 开始进行日常编码和 agent 任务。它在您的本地环境中运行，并且可以独立于编辑器窗口继续运行。当您需要特定工具或工作流程时，选择另一个工具，或者将独立更改委托给返回拉取请求的云 agent。

agent 工具可协调工具调用、上下文和代码更改。 {% data variables.product.prodname_vscode %} 支持 {% data variables.product.prodname_copilot %}、{% data variables.product.prodname_anthropic_claude %} 和 {% data variables.product.prodname_openai_codex %} 线束，以及可用云 agents 的云目标。

使用 **会话目标** 控件选择可用的线束或云目标。每个工具仍然不同，因此工具、模型、权限、自定义和变更审核工作流程可能有所不同。

有关工具、语言模型、agent角色和执行环境之间的关系，请参阅[Agent工具](<https://github.com/docs/agents/concepts/agent-harnesses.md>)。

## 了解会话控制

聊天输入中的控件配置会话的单独部分。对于工作区中的第一个编码任务，请使用以下开始选项：

|控制|它决定什么|开始于|
|---------|--------------------|------------|
|**会话目标**|哪个线束运行会话以及其工具在哪里运行|**Copilot** 用于一般编码任务|
| **Agent** |适用哪些说明、工具和行为|**Agent**用于实施或**计划**首先审查方法。当选定的目标提供问题时，使用 ** 询问 ** 问题。|
|**语言模型**|agent 的推理方式、响应速度以及消耗 AI 积分的方式|**自动**（如果可用）|
|**权限**|哪些操作需要您确认|**手动权限**|
|**代码隔离**|更改是否进入当前文件夹或单独的 Git 工作树|**文件夹**，用于快速入门指南或当前未提交的文件|

当您希望将更改与活动工作区分开并且任务可以从提交的 Git 状态开始时，请使用 **New Worktree**。 Worktree 会话使用 ** 允许所有 **，因此当您需要手动批准提示时，请选择 ** 文件夹 **。工作树隔离代码更改，但不是安全边界。

您可以选择的模型取决于线束以及您[访问模型](<https://github.com/docs/agent-customization/language-models.md>)的方式，例如通过{% data variables.product.prodname_copilot %}订阅、其他支持的帐户或您自己的API密钥。更改型号或访问源不会切换线束。

## 选择会话目标

要更改推理、速度或模型成本，您可能首先需要在当前线束可用时[选择不同的模型](<https://github.com/docs/agent-customization/language-models.md#change-the-model-for-chat>)。更改模型不会切换线束或将项目自定义转换为其他格式。

要确定其他线束是否更适合您的工作流程，请使用以下指南：

* 选择 **Copilot** 进行日常编码和 agent 任务，无论您留在编辑器中还是在其他地方继续。 [agents 快速入门](<https://github.com/docs/agents/quickstart.md>) 使用此选项。
* 如果您已使用该提供商的 agent 工作流程，并且在使用 {% data variables.product.prodname_vscode_shortname %} 时需要其支持的项目配置和权限选项，请选择 **Claude** 或 **Codex**。切换前检查[Claude设置和功能](<#claude-preview>)或[Codex设置和功能](<#codex>)。
* 选择 **Cloud** 来执行范围明确的任务，该任务可以针对 GitHub 存储库独立运行并返回拉取请求。
* 选择 ** 本地 ** 以在当前工作区中使用 {% data variables.product.prodname_vscode_shortname %} 分机主机工作流程。

{% data variables.product.prodname_vscode_shortname %} 提供跨目标的通用聊天和会话管理界面。可用的控件和工作流程取决于所选的目标。您的选择主要影响 agent 的运行位置、它可以使用哪些工具和模型以及它如何应用代码更改。

|会话目标|工具运行的地方|代码访问|选择它是为了|
|----------------|-----------------|-------------|---------------|
| **Copilot** |在您计算机上、远程主机上或开发容器中的 Agent 主机中|当前文件夹、独立的 Git 工作树或 Dev Container 工作区|日常编码，可以选择跨受支持的接口继续工作|
| **Claude** |在你的机器上|当前文件夹或隔离的 Git 工作树|使用熟悉的 Claude agent 工作流程及其权限模式，同时查看 {% data variables.product.prodname_vscode_shortname %} 中的更改|
| **Codex** |在你的机器上|当前文件夹或隔离的 Git 工作树|使用熟悉的 Codex 工作流程执行 {% data variables.product.prodname_vscode_shortname %} 中的交互式或后台编码任务|
|**云**|在提供商的远程基础设施上|GitHub 存储库和拉取请求|独立任务不需要本地编辑器上下文并受益于团队审核|
|**本地**|在当前工作区的 {% data variables.product.prodname_vscode_shortname %} 扩展主机中|当前工作空间|当前工作区中的 {% data variables.product.prodname_vscode_shortname %} 分机-主机工作流程|

**Local** 是其中一个线束的名称，并不意味着它是本地运行 agents 的唯一方法。 Copilot、Claude 和 Codex 也在您的计算机上运行。 **云**是在提供商管理的基础设施上运行的执行目标，而不是在您的计算机或连接的主机上运行的线束的后台模式。

特定于线束的定制，包括 [ 挂钩 ](<https://github.com/docs/agent-customization/hooks.md#choose-the-hook-implementation-for-your-session>)，遵循所选线束。在 Agent 主机中运行多个线束不会为它们提供共享的挂钩架构。

Dev Container 执行仅在桌面中可用 {% data variables.copilot.agents_window %}。使用工作区选择器启动 Agent 在本地项目的开发容器中或在 SSH、隧道或 WSL 主机。这将选择执行环境。使用 **会话目标** 单独控制以选择线束。 Dev Container 会话直接在容器工作区中工作，不支持 **新工作树**。了解要求以及如何做 [运行一个 agent 开发容器中的会话](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>).

<a name="use-the-copilot-harness"></a>

## 使用 {% data variables.product.prodname_copilot_short %} 安全带

使用 {% data variables.product.prodname_copilot_short %} 工具提出问题、计划更改、编辑代码和运行测试。该线束基于 [{% data variables.copilot.copilot_sdk %}](<https://github.com/github/copilot-sdk>) 构建，有助于在 {% data variables.product.prodname_vscode_shortname %}、{% data variables.copilot.copilot_cli %} 和 {% data variables.copilot.github_copilot_app %} 上提供更一致的线束行为和核心功能。您无需单独安装SDK。

* **保持工作跨接口进行**：[Agent主机](<https://github.com/docs/agents/concepts/agent-host.md>)在专用进程中运行线束，与扩展主机和显示会话的窗口分开。繁忙的扩展不会阻止 agent 进程，并且任务可以在您关闭其项目文件夹后继续。在{% data variables.copilot.chat_view %}或{% data variables.copilot.agents_window %}中打开相同的实时会话。 {% data variables.product.prodname_vscode_shortname %} 还可以 [ 发现并继续在 {% data variables.copilot.copilot_cli %} 和 {% data variables.copilot.github_copilot_app %}](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) 中创建的受支持会话。要在终端中继续 {% data variables.product.prodname_vscode_shortname %} {% data variables.product.prodname_copilot_short %} 会话，请使用 [**在终端中恢复 **](<#use-copilot-cli-from-the-terminal>)。
* **在代码所在的位置运行**：在您的计算机、连接的主机或开发容器中运行 Agent 主机。文件编辑和命令在包含主机的环境中运行。
* **重用项目指南**：通过[自定义指令](<https://github.com/docs/agent-customization/custom-instructions.md>)共享编码约定，并通过[Agent Skills](<https://github.com/docs/agent-customization/agent-skills.md>)共享重复工作流程。例如，使用相同的存储库技能在 {% data variables.product.prodname_vscode_shortname %} 和 {% data variables.copilot.copilot_cli_short %} 中运行项目的测试工作流程。
* **重用支持的挂钩（预览版）**：{% data variables.product.prodname_copilot_short %}会话使用[与{% data variables.copilot.copilot_cli_short %}相同的SDK挂钩实现](<https://github.com/docs/agent-customization/hooks.md#choose-the-hook-implementation-for-your-session>)。在重用挂钩之前检查支持的事件和工具负载。

对于由桌面 {% data variables.product.prodname_vscode_shortname %} 管理的本地会话，请保持应用程序运行。关闭项目文件夹与退出应用程序不同。客户端工具还需要提供它们保持连接的窗口。

工具、模型、权限和自定义可能因体验而异。跨产品使用 {% data variables.copilot.copilot_sdk %} 并不意味着所有会话或个人设置在它们之间同步。

有关详细信息，请参阅[工具可用性](<https://github.com/docs/agents/run/tools.md#manage-tool-availability-for-copilot>)、[查看更改](<https://github.com/docs/agents/run/review-code-edits.md>)和[{% data variables.product.prodname_copilot_short %}设置和限制](<#copilot>)。

## 开始会话

当您在{% data variables.copilot.chat_view %}或{% data variables.copilot.agents_window %}中启动会话时，您可以选择会话目标。当您更改正在进行的会话的目标时，{% data variables.product.prodname_vscode_shortname %} 会将其视为 [ 切换 ](<#移交会话>)，并将对话历史记录和上下文传送到新目标。

**会话目标**控件仅列出当前窗口中可用的目标。如果您的首选线束未列出，请在 [配置 agent 线束 ](<#configure-an-agent-harness>) 中查看其先决条件。

要开始会话：

1. 打开{% data variables.copilot.chat_view %}（`kb(workbench.action.chat.open)`）或{% data variables.copilot.agents_window %}。

1. 在{% data variables.copilot.chat_view %}中选择**新建聊天**(`+`)，或在{% data variables.copilot.agents_window %}中选择**新建**。

1. 如果您使用 {% data variables.copilot.agents_window %}，请选择会话的文件夹或 GitHub 存储库。

1. 打开 **会话目标** 控件并选择可用的线束或云目标。

![{% data variables.copilot.agents_window %}中会话目标控件的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agent-harnesses/agents-window-session-target.png>)

1. 为所选目标配置任何可用的 agent 角色、语言模型、权限级别和代码隔离选项。

1. 输入提示并提交。

1. 在 agent 工作期间审核批准请求。任务完成后，[查看其更改和验证结果](<https://github.com/docs/agents/run/review-code-edits.md>)。

对于指导性的第一个任务，请完成 [agents 快速入门 ](<https://github.com/docs/agents/quickstart.md>)。了解有关 [创建和管理会话 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md>) 的更多信息。

## 选择代码隔离

> [！NOTE]
> 代码隔离选项（工作树或文件夹）仅在{% data variables.copilot.agents_window %}中可用。

AI agents 可以将代码更改应用到您的工作区。为了隔离代码更改并避免与活动工作区发生冲突，您可以选择在新的 [Git 工作树 ](<https://github.com/docs/sourcecontrol/branches-worktrees.md#understanding-worktrees>) 中运行会话。当您选择本地线束时，更改始终会应用到活动工作区中。

代码隔离控制 agent 应用文件更改的位置。权限级别控制哪些操作需要您的批准。工作树隔离可将更改保留在活动工作区之外，但不会限制 agent 可用的命令或网络访问。

|选择|选择它是为了|注意事项|
|--------|---------------|----------------|
|**新工作树**|不应修改活动工作空间的并行任务|从提交的 Git 状态开始，并要求您集成结果|
|**文件夹**|应使用当前文件和未提交更改的小型交互式任务|Agent 编辑内容会立即显示在您的活动工作区中|

对于使用单独的工作树执行两个独立编码任务的演练，请遵循 [委派两个任务而不混合它们的更改](<https://github.com/docs/agents/guides/delegate-two-tasks.md>)。

当您[在{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md#start-an-agent-session>)中启动会话时，选择**新建工作树**并选择基础分支来隔离会话。基础分支提供新工作树的初始内容，并且不会更改活动工作区中的分支。

如果未选择 **New Worktree**，则 agent 直接作用于工作区中的代码。对于至少有一个分支的本地 Git 存储库，您可以在 agent 启动之前选择一个现有的本地分支进行签出。签出分支会更改活动工作区中的分支，并且需要干净的工作树。在选择另一个分支之前提交或隐藏您的更改。以后的会话不会记住分支选择。

您在 {% data variables.copilot.chat_view %} 中启动的会话始终使用当前工作区。

在 {% data variables.product.prodname_vscode_shortname %} Insiders 中，`setting(sessions.useWorktree)` 控制在创建第一个工作区会话时是否选择 **新工作树 **。启动会话后，{% data variables.product.prodname_vscode_shortname %} 会记住您在工作区之间的隔离选择并使用它而不是此设置。

![{% data variables.copilot.agents_window %} 中的“新建工作树”复选框和基本分支控件的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agent-harnesses/agents-window-new-worktree.png>)

工作树隔离需要一个至少有一次提交的 Git 存储库。新的工作树包含来自所选基础分支的已提交文件。它不会自动包含主工作树中未提交的跟踪更改或未跟踪文件。提交 agent 需要的更改，或者当任务取决于您当前未提交的状态时使用文件夹隔离。

默认情况下，Git 忽略的文件（例如 `.env` 文件和已安装的依赖项）也不存在。使用`setting(git.worktreeIncludeFiles)`将忽略的文件[复制到新工作树](<https://github.com/docs/sourcecontrol/branches-worktrees.md#include-files-when-creating-a-worktree>)中。对于大型被忽略的文件夹，`setting(git.worktreeSymlinkFolders)`（实验）可以[符号链接文件夹而不是](<https://github.com/docs/sourcecontrol/branches-worktrees.md#symlink-ignored-folders-when-creating-a-worktree>)。 agent 通过符号链接进行的更改会影响原始结账中的文件夹。

工作树会话使用**允许所有**，因为它们的代码更改与您的活动工作区分开。文件夹会话提供所选线束支持的 [ 权限级别 ](<https://github.com/docs/agents/run/approvals.md#permission-levels>)。对于操作系统级文件系统和网络限制，请配置[agent沙箱](<https://github.com/docs/agents/run/agent-sandboxing.md>)。

<a name="configure-an-agent-harness"></a>

## 配置线束或云目标

展开目标以查看其设置和功能。

<a name="copilot"></a>



<summary>Copilot</summary>

有关 {% data variables.copilot.copilot_sdk %} 和支持的工作流程的摘要，请参阅 [使用 {% data variables.product.prodname_copilot_short %} 线束 ](<#use-the-copilot-harness>)。

### 设置和身份验证

Copilot 会话使用相同的 GitHub 身份验证上下文作为聊天 {% data variables.product.prodname_vscode_shortname %}。如果您使用 GitHub 企业帐号为 Copilot，会话使用该帐户。对于托管用户帐户 GHE.com，完成设置 [使用 GitHub Copilot 有一个帐户 GHE.com](<https://docs.github.com/en/copilot/managing-copilot/configure-personal-settings/using-github-copilot-with-an-account-on-ghecom>).

### 首选 Copilot 进行新的编辑聊天会话

启用 `setting(chat.editor.preferCopilotHarness)` _（实验）_ 以在新的编辑器聊天会话中首选使用 {% data variables.copilot.copilot_sdk_short %} 工具。它不会迁移现有会话或更改显式或记住的 Claude 和 Codex 选择。

企业管理员可以使用 `ChatEditorPreferCopilotHarness` 设备策略（从版本 1.134 开始提供）强制执行首选项。 Agent 主机上的 Copilot 会话使用共享 SDK 挂钩实现并加载 Copilot 策略挂钩。本地会话不加载 SDK 策略挂钩。请参阅[在线束之间迁移挂钩](<https://github.com/docs/agent-customization/hooks.md#migrate-hooks-between-harnesses>)和[企业挂钩配置](<https://github.com/docs/enterprise/manage-ai-settings.md#use-the-sdk-harness-for-policy-hooks>)。

### 权限和批准

可用的[权限级别](<https://github.com/docs/agents/run/approvals.md#permission-levels>)取决于隔离模式：

* **工作树**：权限级别为**允许全部**且不可更改。
* **文件夹**：从权限选择器中选择**手动权限**或**允许所有**。要同时使用 **辅助权限 ** `feature(assisted-permissions)`，请打开 `setting(chat.assistedPermissions.enabled)`。

由于 Copilot 会话在 Agent 主机上运行，因此 **Autopilot** 是 [agent 模式 ](<https://github.com/docs/agents/run/approvals.md#how-autopilot-works>)，而不是权限级别。

### 提供商特定的功能

* **Shell 初始化** `feature(agent-host-shell-initialization)`：使agent shell 命令与您的开发环境保持一致。在使用 SDK 内置 shell 工具的本地 Copilot 会话中，启用 `setting(chat.agentHost.shellTool.initScript.enabled)` 在每个命令之前加载 macOS 和 Linux 上的 `~/.bashrc` 或 Windows 上的 PowerShell 配置文件。安装 [Python 环境 ](<https://github.com/docs/python/environments.md#terminal-settings>) 并将 `setting(python-envs.terminal.autoActivationType)` 设置为 `shellStartup` 后，所选工作区环境也会被激活。这不适用于远程会话或 Agent 主机自定义终端工具。

* **斜杠命令**：在聊天输入中输入`/`以查看Copilot会话中可用的斜杠命令。例如，使用 `/compact` 减少对话上下文，使用 `/yolo` 和 `/autoApprove` 控制 [ 自动工具批准 ](<https://github.com/docs/agents/run/approvals.md#allow-all-tools-globally>) 或 [`/plugin`](<https://github.com/docs/agent-customization/agent-plugins.md#manage-plugins-with-slash-commands>) 管理插件和市场。

#### 通过橡皮鸭获得第二意见

`feature(rubber-duck)`

Rubber Duck 是一个内置的只读评论家，它为 Copilot 的计划、代码和测试提供第二意见。它使用补充模型来查找实质性问题，例如逻辑错误、设计缺陷、安全漏洞和缺少测试覆盖率。 Rubber Duck 将其反馈分为阻塞问题、非阻塞问题和建议。 Copilot 总结了批评并决定如何对其采取行动，但 Rubber Duck 不会编辑文件或运行更改环境的命令。

对于不平凡的工作，Copilot可能会在关键点自动咨询橡皮鸭，例如：

* 制定计划之后，实施之前。
* 在复杂的实施过程中。
* 写完测试后。
* 经过多次失败或意想不到的结果。

对于较小的任务，Copilot通常会跳过此审查。如需随时请求审核，请用自然语言询问 Copilot：

```prompt
Get a second opinion on the changes you made so far.
```

您也可以在聊天输入中输入`/rubber-duck <question>`。例如，输入`/rubber-duck What edge cases are missing?`。

> [！NOTE]
> 大黄鸭仅在主会话使用Claude或GPT模型且有合适的补充模型可用时才可用。额外的模型通道会增加延迟和模型使用。

在 GitHub 文档中了解有关 [Rubber Duck agent](<https://docs.github.com/en/copilot/concepts/agents/copilot-cli/rubber-duck>) 的更多信息。

<a name="remote-control-copilot-sessions"></a>

* **远程控制**：输入`"/remote on"`以监控和引导来自GitHub.com或GitHub移动应用程序的正在运行的Copilot会话。会话历史记录、工具活动、状态、批准和问题保持同步。远程控制需要 GitHub 身份验证和映射到 GitHub 存储库的工作区。输入`"/remote off"`停止共享会话。

<a name="run-deep-research-with-the-research-agent"></a>

* **研究 agent** _（预览）_：在 {% data variables.product.prodname_vscode_shortname %} Insiders 中，输入 `/research <topic>` 以生成详细的 Markdown 报告，其中引用了您的代码库、相关 GitHub 存储库，和网络。对于纳入实施计划的研究，请使用 [ 计划 agent](<https://github.com/docs/agents/run/planning.md>)。对于将结果返回到当前对话的重点研究，请使用 [ 子代理 ](<https://github.com/docs/agents/run/subagents.md>)。

<a name="use-copilot-cli-from-the-terminal"></a>

* **终端集成**：打开**{% data variables.copilot.copilot_cli %}**终端配置文件，从命令面板（`kb(workbench.action.showCommands)`）运行**聊天：新建{% data variables.copilot.copilot_cli_short %}会话**，或输入`copilot`在集成终端中。 {% data variables.product.prodname_vscode_shortname %} 将会话添加到会话列表中。要从终端继续现有的 Copilot 会话，请右键单击它并选择 **在终端中恢复 **。

### 局限性

Copilot 会话无法访问每个 {% data variables.product.prodname_vscode_shortname %} 内置或扩展提供的工具。仅当 {% data variables.product.prodname_vscode_shortname %} 连接到会话时，启用的客户端工具才可供 agent 使用，并且您 [ 管理哪些工具可供 Copilot](<https://github.com/docs/agents/run/tools.md#manage-tool-availability-for-copilot>) 使用。在重用现有服务器配置之前，请检查 [MCP 配置兼容性 ](<https://github.com/docs/agent-customization/mcp-servers.md#configure-the-mcpjson-file>)。




<a name="claude-preview"></a>
<a name="third-party-agents"></a>



<summary>Claude</summary>

Claude 会话使用 Anthropic 的 Claude Agent SDK，并且可以在您的工作区中自主运行。 {% data variables.product.prodname_vscode_shortname %} 通过其 SDK 集成了该工具，同时在 {% data variables.product.prodname_vscode_shortname %} 中保留会话管理、聊天和代码审查。

### 设置和身份验证

默认情况下启用 Claude 支持。使用 `setting(github.copilot.chat.claudeAgent.enabled)` 打开或关闭它。

Claude支持两种身份验证和计费选项：

* **GitHub Copilot订阅**：登录GitHub以使用Copilot路由模型。使用量通过您的 Copilot 订阅进行计费。
* **自带密钥 (BYOK)**：使用 Claude API 密钥或其他受支持的 Claude BYOK 选项。使用量由您配置的提供商计费。

当两个选项均可用时，模型选择器按 **Anthropic** 和 **Copilot** 对模型进行分组。您选择的模型决定了下一回合的提供商和计费方式。您可以在现有 Claude 会话中在 BYOK- 支持的模型和 Copilot 路由模型之间切换。

<a name="use-claude-without-github-sign-in"></a>
<a name="use-claude-without-github-sign-in-experimental"></a>

要在不登录 GitHub _（实验）_ 的情况下使用 Claude，请配置 Claude API 密钥或其他受支持的 Claude BYOK 选项。对于 Anthropic API 密钥，请在您的环境中或 `~/.claude/settings.json` 中的 `env` 对象中设置 `ANTHROPIC_API_KEY`。了解有关 [Claude Code 身份验证 ](<https://code.claude.com/docs/en/authentication>) 的更多信息。

启用`setting(chat.agentHost.allowSignedOutWhenUsable)`以在退出GitHub时打开{% data variables.copilot.agents_window %}。在您登录之前，模型选择器仅显示 Claude BYOK 配置中的模型。登录 GitHub 后，Copilot 路由的模型也可用。

### 权限和批准

Claude支持以下权限模式：

* **自动编辑**：应用更改而不请求批准。
* **请求批准**：在应用更改之前询问。
* **计划**：概述实施前的方法。

> [！CAUTION]
> `setting(github.copilot.chat.claudeAgent.allowDangerouslySkipPermissions)` 设置绕过所有权限检查。仅在无法访问互联网的隔离沙箱环境中使用它。

### 提供商特定的功能

在聊天输入中输入 `/` 可查看用于管理 Claude-原生 agents、挂钩、内存文件和代码审查的命令。了解有关 [Claude 子代理 ](<https://code.claude.com/docs/en/sub-agents>) 和 [Claude 挂钩 ](<https://code.claude.com/docs/en/hooks>) 的更多信息。




<a name="codex"></a>



<summary>Codex</summary>

Codex 线束使用 OpenAI Codex 执行交互式和后台编码任务。它通过 OpenAI Codex 扩展运行，或者实验性地在 Agent 主机上运行。 {% data variables.product.prodname_vscode_shortname %} 为这两种集成提供会话管理、聊天和代码审查。

### 设置和身份验证

默认情况下不列出Codex。在选择其中一个选项之前，先完成该选项。你不需要两者：

* **在{% data variables.copilot.chat_view %}**中使用OpenAICodex扩展：安装并启用[OpenAICodex扩展](<https://marketplace.visualstudio.com/items?itemName=openai.chatgpt>)。
* **在Agent主机**_上使用Codex（实验）_：启用`setting(chat.agentHost.codexAgent.enabled)`。这使得 Codex 在 {% data variables.copilot.agents_window %} 中可用。要使用{% data variables.copilot.chat_view %}中的Agent主机Codex，还需启用`setting(chat.editor.codex.preferAgentHost)`并在出现提示时重新启动{% data variables.product.prodname_vscode_shortname %}。

每个窗口中仅出现一个 Codex 实现。当您更喜欢 {% data variables.copilot.chat_view %} 中的 Agent 主机 Codex 时，它会替换该窗口中 OpenAI 扩展中的 Codex 目标。

在Agent主机上，Codex支持两种身份验证和订阅选项：

* **GitHub Copilot订阅**：登录GitHub以使用Copilot支持的模型。此选项需要 {% data variables.copilot.copilot_pro_plus_short %}。
* 【**ChatGPT】订阅【**】：打开账户菜单，选择【**】登录【ChatGPT**】。一个免费的 ChatGPT 帐户就足够了。

当两个帐户都登录时，模型选择器按 **Copilot** 和 **ChatGPT** 对模型进行分组。您的选择决定了使用哪个订阅，并且 {% data variables.product.prodname_vscode_shortname %} 会将该提供程序与会话一起保存。

<a name="use-codex-without-github-sign-in"></a>
<a name="use-codex-without-github-sign-in-experimental"></a>

要在不登录 GitHub _（实验）_ 的情况下使用 Codex，请登录 ChatGPT 并启用 `setting(chat.agentHost.allowSignedOutWhenUsable)`。然后，桌面 {% data variables.copilot.agents_window %} 在注销时显示 ChatGPT 支持的模型。支持 Copilot 的型号会提示您登录 GitHub，而基于浏览器的 {% data variables.copilot.agents_window %} 仍需要 GitHub 登录。

### 权限和批准

在 Agent 主机上，Codex 提供以下批准预设：

* **默认权限**：读取和编辑工作区文件以及运行例行本地命令。 Codex 在使用互联网或访问工作区之外的资源之前询问。
* **自动审阅**：使用与**默认权限**相同的工作区访问权限，但将批准请求发送给自动审阅者而不是提示您。
* **完全访问权限**：在工作区之外编辑文件并无需询问即可使用互联网。

> [!CAUTION]
> **完全访问权限** 提供Codex 无限制的磁盘和网络访问权限。仅当您打算授予 agent 对您的计算机的完全访问权限时才使用它。




<a name="cloud"></a>



<summary>云目标</summary>

云目标在远程基础设施上运行可用的提供程序工具，并与 GitHub 存储库配合使用。 agent 在分支上实现任务并打开拉取请求以供审核。选择云来执行范围广泛的任务，这些任务无需访问本地编辑器上下文、终端输出或扩展提供的工具即可运行。

{% data variables.product.prodname_vscode_shortname %} 支持：

* **{% data variables.copilot.copilot_cloud_agent %}** 用于实现功能、处理审核反馈和创建拉取请求。
* **Claude 和 Codex {% data variables.copilot.copilot_cloud_agent_short %}s** 用于特定于提供商的功能。第三方 {% data variables.copilot.copilot_cloud_agent_short %} 目前处于预览状态。

要在云中使用 Claude 或 Codex，请在 Copilot 帐户设置中启用支持。请参阅[管理第三方{% data variables.copilot.copilot_cloud_agent_short %}的策略](<https://docs.github.com/en/copilot/how-tos/manage-your-account/manage-policies#enabling-or-disabling-third-party-coding-agents-in-your-repositories>)。您不需要提供商的 {% data variables.product.prodname_vscode_shortname %} 扩展来进行云会话。

### 启动云会话

1. 打开{% data variables.copilot.chat_view %}并选择**新聊天**。

1. 从**会话目标**控件中选择**云**。

1. 选择云提供商以及自定义 agent 和型号（如果可用）。

1. 输入提示并提交。

会话远程运行并显示在会话列表中。您通过将问题或拉取请求分配给 GitHub.com 上的 {% data variables.copilot.copilot_cloud_agent_short %} 创建的会话也会出现在 {% data variables.product.prodname_vscode_shortname %} 中。

您还可以在 [ 在 {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md#start-an-agent-session>) 中启动会话时选择 GitHub 存储库，或 [ 将现有会话 ](<#移交会话>) 移交给云目标。在 Copilot 会话中，输入 `/delegate` 以继续在云中执行任务。

云会话使用云服务配置的工具、MCP 服务器和模型。他们无法访问 {% data variables.product.prodname_vscode_shortname %} 内置工具或本地开发上下文。




<a name="local"></a>



<summary>本地</summary>

本地线束在 {% data variables.product.prodname_vscode_shortname %} [ 扩展主机 ](<https://github.com/docs/agents/concepts/agent-host.md#behavior-on-the-extension-host>) 中以交互方式运行，并直接在活动工作区中工作。它可以使用{% data variables.product.prodname_vscode_shortname %}内置工具、扩展提供的工具、MCP服务器以及{% data variables.product.prodname_vscode_shortname %}中配置的模型，包括[自带关键模型](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>)。

### 选择内置agent角色

本地会话提供以下内置 agent 角色：

* **询问**：提出问题并提供指导，而无需更改代码。
* **Agent**：自主计划和执行复杂的编码任务、编辑文件、运行命令并对结果进行迭代。
* **计划**：研究任务并在代码更改之前创建结构化实施计划。了解有关 [ 在本地会话中进行规划 ](<https://github.com/docs/agents/run/planning.md#plan-in-a-local-session>) 的更多信息。

您可以在会话期间通过 agent 选择器切换角色。




## 移交会话

切换继续使用不同的 agent 配置进行正在进行的工作，并携带对话历史记录和上下文。切换可以更改工具、执行环境或 agent 角色。当另一种配置更适合任务的下一部分时，请使用切换。

例如，继续与 Claude 或 Codex 进行 Copilot 会话以使用特定于提供商的功能，将范围明确的任务发送到拉取请求工作流的云目标，或者从计划 agent 移至实施 agent。

您只能从本地会话发起切换。本地和远程 Agent 主机会话不显示 ** 会话目标 ** 下拉列表，但它们仍然可用作本地会话的切换目标。

要将会话移交给另一个工具或执行环境：

1. 打开会话。

1. 在聊天输入中，打开 **会话目标** 下拉列表。

1. 选择应继续工作的目标，例如 Copilot、Claude、Codex 或云。

{% data variables.product.prodname_vscode_shortname %} 将对话历史记录和上下文携带到所选目标。由于每个工具、执行环境或 agent 角色提供不同的功能，工具、权限和模型可能会发生变化。

要在本地会话中实施内置 **计划 ** agent 中的计划，请选择 **开始实施 **。这会切换到当前对话中的**Agent**并提交实施请求。要在另一个受支持的会话中继续，请打开 **开始实施 ** 旁边的下拉列表，然后在 ** 目标中选择可用的 **Continue。有关本地和 {% data variables.product.prodname_copilot_short %} 会话中的不同控制，请参阅 [ 使用 agents](<https://github.com/docs/agents/run/planning.md>) 进行规划。

> [!TIP]
> 在{% data variables.copilot.copilot_cli_short %}中，输入`/delegate`以继续使用{% data variables.copilot.copilot_cloud_agent_short %}进行工作。

### 切换与相关操作的比较

|行动|它的作用|
|---|---|
|**切换**|使用不同的工具、执行环境或 agent 角色继续工作，并携带对话历史记录和上下文。|
|**分叉会话**|从对话中的某个点创建独立会话。了解有关 [ 分叉会话 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md#fork-a-chat-session>) 的更多信息。|
|**开关表面**|在 [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>) 或 [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) 中打开同一会话，而不更改其线束或上下文。|

有关切换工作原理的背景信息，请参阅[会话和切换](<https://github.com/docs/agents/concepts/sessions.md#hand-off-a-session>)。

## 相关资源

* [Agent 安全带概念](<https://github.com/docs/agents/concepts/agent-harnesses.md>)
* [管理 agent 会话](<https://github.com/docs/agents/run/sessions/manage-sessions.md>)
* [批准和权限](<https://github.com/docs/agents/run/approvals.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Choose and use an agent harness

Start with the [{% data variables.product.prodname_copilot_short %} harness](<#use-the-copilot-harness>) for day-to-day coding and agent tasks. It runs in your local environment and can continue independently of an editor window. Choose another harness when you need its specific tools or workflows, or delegate an independent change to a cloud agent that returns a pull request.

An agent harness coordinates tool calls, context, and code changes. {% data variables.product.prodname_vscode %} supports the {% data variables.product.prodname_copilot %}, {% data variables.product.prodname_anthropic_claude %}, and {% data variables.product.prodname_openai_codex %} harnesses, plus a Cloud target for available cloud agents.

Use the **Session Target** control to choose an available harness or the Cloud target. Each harness remains distinct, so tools, models, permissions, customizations, and change-review workflows can differ.

For the relationship between harnesses, language models, agent roles, and execution environments, see [Agent harnesses](<https://github.com/docs/agents/concepts/agent-harnesses.md>).

## Understand the session controls

The controls in the chat input configure separate parts of the session. For your first coding task in a workspace, use these starting choices:

| Control | What it determines | Start with |
|---------|--------------------|------------|
| **Session Target** | Which harness runs the session and where its tools operate | **Copilot** for a general coding task |
| **Agent** | Which instructions, tools, and behavior apply | **Agent** for implementation or **Plan** to review an approach first. Use **Ask** for questions when the selected target provides it. |
| **Language model** | How the agent reasons, how quickly it responds, and how it consumes AI credits | **Auto** when it is available |
| **Permissions** | Which actions require your confirmation | **Manual permissions** |
| **Code isolation** | Whether changes go into the current folder or a separate Git worktree | **Folder** for the guided quickstart or current uncommitted files |

Use **New Worktree** when you want changes separate from your active workspace and the task can start from committed Git state. Worktree sessions use **Allow all**, so choose **Folder** when you want manual approval prompts. A worktree isolates code changes but isn't a security boundary.

The models you can select depend on the harness and how you [access models](<https://github.com/docs/agent-customization/language-models.md>), such as through a {% data variables.product.prodname_copilot %} subscription, another supported account, or your own API key. Changing the model or access source doesn't switch harnesses.

## Choose a session target

To change reasoning, speed, or model cost, you may first want to [choose a different model](<https://github.com/docs/agent-customization/language-models.md#change-the-model-for-chat>) within the current harness when one is available. Changing the model does not switch harnesses or convert your project customizations to another format.

To decide whether another harness better fits your workflow, use these guidelines:

* Choose **Copilot** for day-to-day coding and agent tasks, whether you stay in your editor or continue elsewhere. The [agents quickstart](<https://github.com/docs/agents/quickstart.md>) uses this option.
* Choose **Claude** or **Codex** when you already use that provider's agent workflow and want its supported project configuration and permission options while working in {% data variables.product.prodname_vscode_shortname %}. Check [Claude setup and capabilities](<#claude-preview>) or [Codex setup and capabilities](<#codex>) before switching.
* Choose **Cloud** for a well-scoped task that can run independently against a GitHub repository and return a pull request.
* Choose **Local** to use the {% data variables.product.prodname_vscode_shortname %} extension-host workflow in your current workspace.

{% data variables.product.prodname_vscode_shortname %} provides common chat and session-management surfaces across targets. Available controls and workflows depend on the selected target. Your choice primarily affects where the agent runs, which tools and models it can use, and how it applies code changes.

| Session target | Where tools run | Code access | Choose it for |
|----------------|-----------------|-------------|---------------|
| **Copilot** | In the Agent Host on your machine, on a remote host, or in a Dev Container | Current folder, an isolated Git worktree, or a Dev Container workspace | Day-to-day coding, with the option to continue work across supported interfaces |
| **Claude** | On your machine | Current folder or an isolated Git worktree | Use a familiar Claude agent workflow and its permission modes while reviewing changes in {% data variables.product.prodname_vscode_shortname %} |
| **Codex** | On your machine | Current folder or an isolated Git worktree | Use a familiar Codex workflow for interactive or background coding tasks in {% data variables.product.prodname_vscode_shortname %} |
| **Cloud** | On a provider's remote infrastructure | A GitHub repository and pull request | Independent tasks that don't need local editor context and benefit from team review |
| **Local** | In the {% data variables.product.prodname_vscode_shortname %} extension host for the current workspace | Current workspace | The {% data variables.product.prodname_vscode_shortname %} extension-host workflow in the current workspace |

**Local** is the name of one of the harnesses and doesn't mean it is the only way to run agents locally. Copilot, Claude, and Codex also run on your machine. **Cloud** is an execution target that runs on provider-managed infrastructure, not a background mode for a harness running on your machine or a connected host.

Harness-specific customizations, including [hooks](<https://github.com/docs/agent-customization/hooks.md#choose-the-hook-implementation-for-your-session>), follow the selected harness. Running multiple harnesses in Agent Host does not give them a shared hook schema.

Dev Container execution is available only in the desktop {% data variables.copilot.agents_window %}. Use the workspace picker to start an Agent Host session in a local project's Dev Container or one on an SSH, Tunnel, or WSL host. This selects the execution environment. Use the **Session Target** control separately to choose the harness. Dev Container sessions work directly in the container workspace and don't support **New Worktree**. Learn about requirements and how to [run an agent session in a Dev Container](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>).

<a name="use-the-copilot-harness"></a>

## Work with the {% data variables.product.prodname_copilot_short %} harness

Use the {% data variables.product.prodname_copilot_short %} harness to ask questions, plan changes, edit code, and run tests. The harness is built on the [{% data variables.copilot.copilot_sdk %}](<https://github.com/github/copilot-sdk>), which helps provide more consistent harness behavior and core capabilities across {% data variables.product.prodname_vscode_shortname %}, {% data variables.copilot.copilot_cli %}, and the {% data variables.copilot.github_copilot_app %}. You don't need to install the SDK separately.

* **Keep work going across interfaces**: the [Agent Host](<https://github.com/docs/agents/concepts/agent-host.md>) runs the harness in a dedicated process, separate from the extension host and the window that displays the session. Busy extensions don't block the agent process, and a task can continue after you close its project folder. Open the same live session in the {% data variables.copilot.chat_view %} or {% data variables.copilot.agents_window %}. {% data variables.product.prodname_vscode_shortname %} can also [discover and continue supported sessions created in {% data variables.copilot.copilot_cli %} and the {% data variables.copilot.github_copilot_app %}](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>). To continue a {% data variables.product.prodname_vscode_shortname %} {% data variables.product.prodname_copilot_short %} session in the terminal, use [**Resume in Terminal**](<#use-copilot-cli-from-the-terminal>).
* **Run where your code lives**: run the Agent Host on your machine, on a connected host, or in a Dev Container. File edits and commands run in the environment that contains the host.
* **Reuse project guidance**: share coding conventions through [custom instructions](<https://github.com/docs/agent-customization/custom-instructions.md>) and recurring workflows through [Agent Skills](<https://github.com/docs/agent-customization/agent-skills.md>). For example, use the same repository skill to run your project's test workflow in {% data variables.product.prodname_vscode_shortname %} and {% data variables.copilot.copilot_cli_short %}.
* **Reuse supported hooks (Preview)**: {% data variables.product.prodname_copilot_short %} sessions use the [same SDK hook implementation](<https://github.com/docs/agent-customization/hooks.md#choose-the-hook-implementation-for-your-session>) as {% data variables.copilot.copilot_cli_short %}. Check the supported events and tool payloads before reusing a hook.

For local sessions managed by desktop {% data variables.product.prodname_vscode_shortname %}, keep the application running. Closing a project folder is different from quitting the application. Client-side tools also require the window that provides them to stay connected.

Tools, models, permissions, and customizations can differ between experiences. Using the {% data variables.copilot.copilot_sdk %} across products does not mean that all sessions or personal settings synchronize between them.

For more information, see [tool availability](<https://github.com/docs/agents/run/tools.md#manage-tool-availability-for-copilot>), [reviewing changes](<https://github.com/docs/agents/run/review-code-edits.md>), and [{% data variables.product.prodname_copilot_short %} setup and limitations](<#copilot>).

## Start a session

You can select a session target when you start a session in the {% data variables.copilot.chat_view %} or the {% data variables.copilot.agents_window %}. When you change the target for an ongoing session, {% data variables.product.prodname_vscode_shortname %} considers this a [handoff](<#hand-off-a-session>) and carries the conversation history and context to the new target.

The **Session Target** control only lists targets that are available in the current window. If your preferred harness is not listed, review its prerequisites in [Configure an agent harness](<#configure-an-agent-harness>).

To start a session:

1. Open the {% data variables.copilot.chat_view %} (`kb(workbench.action.chat.open)`) or the {% data variables.copilot.agents_window %}.

1. Select **New Chat** (`+`) in the {% data variables.copilot.chat_view %}, or select **New** in the {% data variables.copilot.agents_window %}.

1. If you're using the {% data variables.copilot.agents_window %}, select the folder or GitHub repository for the session.

1. Open the **Session Target** control and select an available harness or the Cloud target.

    ![Screenshot of the Session Target control in the {% data variables.copilot.agents_window %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agent-harnesses/agents-window-session-target.png>)

1. Configure any available agent role, language model, permission level, and code-isolation options for the selected target.

1. Enter a prompt and submit it.

1. Review approval requests while the agent works. When the task is complete, [review its changes and validation results](<https://github.com/docs/agents/run/review-code-edits.md>).

For a guided first task, complete the [agents quickstart](<https://github.com/docs/agents/quickstart.md>). Learn more about [creating and managing sessions](<https://github.com/docs/agents/run/sessions/manage-sessions.md>).

## Choose code isolation

> [!NOTE]
> The code isolation option (worktree or folder) is only available in {% data variables.copilot.agents_window %}.

AI agents can apply code changes to your workspace. To isolate code changes and avoid conflicts with your active workspace, you can choose to run sessions in a new [Git worktree](<https://github.com/docs/sourcecontrol/branches-worktrees.md#understanding-worktrees>). When you choose the Local harness, changes are always applied in the active workspace.

Code isolation controls where the agent applies file changes. The permission level controls which actions require your approval. Worktree isolation keeps changes out of your active workspace, but it does not restrict the commands or network access available to the agent.

| Choice | Choose it for | Considerations |
|--------|---------------|----------------|
| **New Worktree** | Parallel tasks that should not modify your active workspace | Starts from committed Git state and requires you to integrate the result |
| **Folder** | Small, interactive tasks that should use your current files and uncommitted changes | Agent edits appear immediately in your active workspace |

For a walkthrough that uses separate worktrees for two independent coding tasks, follow [Delegate two tasks without mixing their changes](<https://github.com/docs/agents/guides/delegate-two-tasks.md>).

When you [start a session in the {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md#start-an-agent-session>), select **New Worktree** and choose the base branch to isolate the session. The base branch provides the initial contents of the new worktree and does not change the branch in your active workspace.

If you leave **New Worktree** unselected, the agent works directly on the code in the workspace. For a local Git repository with at least one branch, you can select an existing local branch to check out before the agent starts. Checking out a branch changes the branch in your active workspace and requires a clean working tree. Commit or stash your changes before you select another branch. The branch selection is not remembered for later sessions.

Sessions that you start in the {% data variables.copilot.chat_view %} always use the current workspace.

In {% data variables.product.prodname_vscode_shortname %} Insiders, `setting(sessions.useWorktree)` controls whether **New Worktree** is selected when you create your first workspace session. After you start a session, {% data variables.product.prodname_vscode_shortname %} remembers your isolation choice across workspaces and uses it instead of this setting.

![Screenshot of the New Worktree checkbox and base branch control in the {% data variables.copilot.agents_window %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agent-harnesses/agents-window-new-worktree.png>)

Worktree isolation requires a Git repository with at least one commit. A new worktree contains the committed files from the selected base branch. It does not automatically contain uncommitted tracked changes or untracked files from your primary worktree. Commit changes that the agent needs, or use folder isolation when the task depends on your current uncommitted state.

Git-ignored files, such as `.env` files and installed dependencies, are also absent by default. Use `setting(git.worktreeIncludeFiles)` to [copy ignored files into new worktrees](<https://github.com/docs/sourcecontrol/branches-worktrees.md#include-files-when-creating-a-worktree>). For large ignored folders, `setting(git.worktreeSymlinkFolders)` (Experimental) can [symlink a folder instead](<https://github.com/docs/sourcecontrol/branches-worktrees.md#symlink-ignored-folders-when-creating-a-worktree>). Changes an agent makes through the symlink affect the folder in the original checkout.

Worktree sessions use **Allow all** because their code changes are separate from your active workspace. Folder sessions offer the [permission levels](<https://github.com/docs/agents/run/approvals.md#permission-levels>) supported by the selected harness. For operating system-level file system and network restrictions, configure [agent sandboxing](<https://github.com/docs/agents/run/agent-sandboxing.md>).

<a name="configure-an-agent-harness"></a>

## Configure a harness or Cloud target

Expand a target to review its setup and capabilities.

<a name="copilot"></a>



<summary>Copilot</summary>

For a summary of the {% data variables.copilot.copilot_sdk %} and supported workflows, see [Work with the {% data variables.product.prodname_copilot_short %} harness](<#use-the-copilot-harness>).

### Setup and authentication

Copilot sessions use the same GitHub authentication context as chat in {% data variables.product.prodname_vscode_shortname %}. If you use a GitHub Enterprise account for Copilot, the session uses that account. For managed user accounts on GHE.com, complete the setup in [Using GitHub Copilot with an account on GHE.com](<https://docs.github.com/en/copilot/managing-copilot/configure-personal-settings/using-github-copilot-with-an-account-on-ghecom>).

### Prefer Copilot for new editor-chat sessions

Enable `setting(chat.editor.preferCopilotHarness)` _(Experimental)_ to prefer the {% data variables.copilot.copilot_sdk_short %} harness for new editor-chat sessions. It does not migrate existing sessions or change explicit or remembered Claude and Codex selections.

Enterprise admins can enforce the preference with the `ChatEditorPreferCopilotHarness` device policy, available from version 1.134. Copilot sessions on Agent Host use the shared SDK hooks implementation and load Copilot Policy Hooks. Local sessions do not load SDK Policy Hooks. See [migrate hooks between harnesses](<https://github.com/docs/agent-customization/hooks.md#migrate-hooks-between-harnesses>) and [enterprise hook configuration](<https://github.com/docs/enterprise/manage-ai-settings.md#use-the-sdk-harness-for-policy-hooks>).

### Permissions and approvals

The available [permission levels](<https://github.com/docs/agents/run/approvals.md#permission-levels>) depend on the isolation mode:

* **Worktree**: the permission level is **Allow all** and can't be changed.
* **Folder**: select **Manual permissions** or **Allow all** from the permissions picker. To also use **Assisted permissions** `feature(assisted-permissions)`, turn on `setting(chat.assistedPermissions.enabled)`.

Because Copilot sessions run on the Agent Host, **Autopilot** is an [agent mode](<https://github.com/docs/agents/run/approvals.md#how-autopilot-works>) rather than a permission level.

### Provider-specific capabilities

* **Shell initialization** `feature(agent-host-shell-initialization)`: keep agent shell commands aligned with your development environment. In local Copilot sessions that use the SDK built-in shell tool, enable `setting(chat.agentHost.shellTool.initScript.enabled)` to load `~/.bashrc` on macOS and Linux or your PowerShell profiles on Windows before each command. With [Python Environments](<https://github.com/docs/python/environments.md#terminal-settings>) installed and `setting(python-envs.terminal.autoActivationType)` set to `shellStartup`, the selected workspace environment is also activated. This does not apply to remote sessions or the Agent Host custom terminal tool.

* **Slash commands**: enter `/` in the chat input to view the slash commands available in a Copilot session. For example, use `/compact` to reduce conversation context, `/yolo` and `/autoApprove` to control [automatic tool approval](<https://github.com/docs/agents/run/approvals.md#allow-all-tools-globally>), or [`/plugin`](<https://github.com/docs/agent-customization/agent-plugins.md#manage-plugins-with-slash-commands>) to manage plugins and marketplaces.

#### Get a second opinion with Rubber Duck

`feature(rubber-duck)`

Rubber Duck is a built-in, read-only critic that gives Copilot a second opinion on its plans, code, and tests. It uses a complementary model to look for substantive issues, such as logic errors, design flaws, security vulnerabilities, and missing test coverage. Rubber Duck groups its feedback into blocking issues, non-blocking issues, and suggestions. Copilot summarizes the critique and decides how to act on it, but Rubber Duck doesn't edit files or run commands that change your environment.

For non-trivial work, Copilot might consult Rubber Duck automatically at key points, such as:

* After creating a plan, before implementation.
* During a complex implementation.
* After writing tests.
* After repeated failures or unexpected results.

For smaller tasks, Copilot typically skips this review. To request a review at any time, ask Copilot in natural language:

```prompt
Get a second opinion on the changes you made so far.
```

You can also enter `/rubber-duck <question>` in the chat input. For example, enter `/rubber-duck What edge cases are missing?`.

> [!NOTE]
> Rubber Duck is available only when the main session uses a Claude or GPT model and a suitable complementary model is available. The additional model pass adds latency and model usage.

Learn more about the [Rubber Duck agent](<https://docs.github.com/en/copilot/concepts/agents/copilot-cli/rubber-duck>) in the GitHub documentation.

<a name="remote-control-copilot-sessions"></a>

* **Remote control**: enter `"/remote on"` to monitor and steer a running Copilot session from GitHub.com or the GitHub Mobile app. Session history, tool activity, status, approvals, and questions stay synchronized. Remote control requires GitHub authentication and a workspace that maps to a GitHub repository. Enter `"/remote off"` to stop sharing the session.

<a name="run-deep-research-with-the-research-agent"></a>

* **Research agent** _(Preview)_: in {% data variables.product.prodname_vscode_shortname %} Insiders, enter `/research <topic>` to produce a detailed Markdown report with citations from your codebase, relevant GitHub repositories, and the web. For research that feeds into an implementation plan, use the [Plan agent](<https://github.com/docs/agents/run/planning.md>). For focused research that returns results to the current conversation, use [subagents](<https://github.com/docs/agents/run/subagents.md>).

<a name="use-copilot-cli-from-the-terminal"></a>

* **Terminal integration**: open the **{% data variables.copilot.copilot_cli %}** terminal profile, run **Chat: New {% data variables.copilot.copilot_cli_short %} Session** from the Command Palette (`kb(workbench.action.showCommands)`), or enter `copilot` in an integrated terminal. {% data variables.product.prodname_vscode_shortname %} adds the session to the sessions list. To continue an existing Copilot session from the terminal, right-click it and select **Resume in Terminal**.

### Limitations

Copilot sessions don't have access to every {% data variables.product.prodname_vscode_shortname %} built-in or extension-provided tool. Enabled client-side tools are available to the agent only while {% data variables.product.prodname_vscode_shortname %} is connected to the session, and you [manage which tools are available to Copilot](<https://github.com/docs/agents/run/tools.md#manage-tool-availability-for-copilot>). Check [MCP configuration compatibility](<https://github.com/docs/agent-customization/mcp-servers.md#configure-the-mcpjson-file>) before reusing an existing server configuration.




<a name="claude-preview"></a>
<a name="third-party-agents"></a>



<summary>Claude</summary>

Claude sessions use Anthropic's Claude Agent SDK and can run autonomously on your workspace. {% data variables.product.prodname_vscode_shortname %} integrates the harness through its SDK while keeping session management, chat, and code review in {% data variables.product.prodname_vscode_shortname %}.

### Setup and authentication

Claude support is enabled by default. Turn it on or off with `setting(github.copilot.chat.claudeAgent.enabled)`.

Claude supports two authentication and billing options:

* **GitHub Copilot subscription**: sign in to GitHub to use Copilot-routed models. Usage is billed through your Copilot subscription.
* **Bring your own key (BYOK)**: use a Claude API key or another supported Claude BYOK option. Usage is billed by your configured provider.

When both options are available, the model picker groups models by **Anthropic** and **Copilot**. The model you select determines the provider and billing method for the next turn. You can switch between BYOK-backed and Copilot-routed models in an existing Claude session.

<a name="use-claude-without-github-sign-in"></a>
<a name="use-claude-without-github-sign-in-experimental"></a>

To use Claude without signing in to GitHub _(Experimental)_, configure a Claude API key or another supported Claude BYOK option. For an Anthropic API key, set `ANTHROPIC_API_KEY` in your environment or in the `env` object in `~/.claude/settings.json`. Learn more about [Claude Code authentication](<https://code.claude.com/docs/en/authentication>).

Enable `setting(chat.agentHost.allowSignedOutWhenUsable)` to open the {% data variables.copilot.agents_window %} while signed out of GitHub. The model picker only shows models from your Claude BYOK configuration until you sign in. After you sign in to GitHub, Copilot-routed models are also available.

### Permissions and approvals

Claude supports these permission modes:

* **Edit automatically**: apply changes without asking for approval.
* **Request approval**: ask before applying changes.
* **Plan**: outline the approach before implementation.

> [!CAUTION]
> The `setting(github.copilot.chat.claudeAgent.allowDangerouslySkipPermissions)` setting bypasses all permission checks. Use it only in an isolated sandbox environment without internet access.

### Provider-specific capabilities

Enter `/` in the chat input to view commands for managing Claude-native agents, hooks, memory files, and code review. Learn more about [Claude subagents](<https://code.claude.com/docs/en/sub-agents>) and [Claude hooks](<https://code.claude.com/docs/en/hooks>).




<a name="codex"></a>



<summary>Codex</summary>

The Codex harness uses OpenAI Codex for interactive and background coding tasks. It runs through the OpenAI Codex extension or, experimentally, on the Agent Host. {% data variables.product.prodname_vscode_shortname %} provides session management, chat, and code review for both integrations.

### Setup and authentication

Codex is not listed by default. Complete one of these options before you select it. You don't need both:

* **Use the OpenAI Codex extension in the {% data variables.copilot.chat_view %}**: install and enable the [OpenAI Codex extension](<https://marketplace.visualstudio.com/items?itemName=openai.chatgpt>).
* **Use Codex on Agent Host** _(Experimental)_: enable `setting(chat.agentHost.codexAgent.enabled)`. This makes Codex available in the {% data variables.copilot.agents_window %}. To use Agent Host Codex in the {% data variables.copilot.chat_view %}, also enable `setting(chat.editor.codex.preferAgentHost)` and restart {% data variables.product.prodname_vscode_shortname %} when prompted.

Only one Codex implementation appears in each window. When you prefer Agent Host Codex in the {% data variables.copilot.chat_view %}, it replaces the Codex target from the OpenAI extension in that window.

On the Agent Host, Codex supports two authentication and subscription options:

* **GitHub Copilot subscription**: sign in to GitHub to use Copilot-backed models. This option requires {% data variables.copilot.copilot_pro_plus_short %}.
* **ChatGPT subscription**: open the account menu and select **Sign in to ChatGPT**. A free ChatGPT account is sufficient.

When both accounts are signed in, the model picker groups models by **Copilot** and **ChatGPT**. Your selection determines which subscription is used, and {% data variables.product.prodname_vscode_shortname %} saves that provider with the session.

<a name="use-codex-without-github-sign-in"></a>
<a name="use-codex-without-github-sign-in-experimental"></a>

To use Codex without signing in to GitHub _(Experimental)_, sign in to ChatGPT and enable `setting(chat.agentHost.allowSignedOutWhenUsable)`. The desktop {% data variables.copilot.agents_window %} then shows ChatGPT-backed models while signed out. Copilot-backed models prompt you to sign in to GitHub, and the browser-based {% data variables.copilot.agents_window %} still requires GitHub sign-in.

### Permissions and approvals

On the Agent Host, Codex provides these approval presets:

* **Default Permissions**: read and edit workspace files and run routine local commands. Codex asks before using the internet or accessing resources outside the workspace.
* **Auto-Review**: use the same workspace access as **Default Permissions**, but send approval requests to an automatic reviewer instead of prompting you.
* **Full Access**: edit files outside the workspace and use the internet without asking.

> [!CAUTION]
> **Full Access** gives Codex unrestricted disk and network access. Use it only when you intend to give the agent full access to your machine.




<a name="cloud"></a>



<summary>Cloud target</summary>

The Cloud target runs an available provider harness on remote infrastructure and works with a GitHub repository. The agent implements the task on a branch and opens a pull request for review. Choose Cloud for well-scoped tasks that can run without access to your local editor context, terminal output, or extension-provided tools.

{% data variables.product.prodname_vscode_shortname %} supports:

* **{% data variables.copilot.copilot_cloud_agent %}** for implementing features, addressing review feedback, and creating pull requests.
* **Claude and Codex {% data variables.copilot.copilot_cloud_agent_short %}s** for provider-specific capabilities. Third-party {% data variables.copilot.copilot_cloud_agent_short %}s are currently in preview.

To use Claude or Codex in the cloud, turn on support in your Copilot account settings. See [Managing policies for third-party {% data variables.copilot.copilot_cloud_agent_short %}s](<https://docs.github.com/en/copilot/how-tos/manage-your-account/manage-policies#enabling-or-disabling-third-party-coding-agents-in-your-repositories>). You don't need the provider's {% data variables.product.prodname_vscode_shortname %} extension for a cloud session.

### Start a cloud session

1. Open the {% data variables.copilot.chat_view %} and select **New Chat**.

1. Select **Cloud** from the **Session Target** control.

1. Choose the cloud provider and, when available, a custom agent and model.

1. Enter a prompt and submit it.

The session runs remotely and appears in the sessions list. Sessions that you create by assigning an issue or pull request to a {% data variables.copilot.copilot_cloud_agent_short %} on GitHub.com also appear in {% data variables.product.prodname_vscode_shortname %}.

You can also select a GitHub repository when you [start a session in the {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md#start-an-agent-session>), or [hand off an existing session](<#hand-off-a-session>) to the Cloud target. In a Copilot session, enter `/delegate` to continue the task in the cloud.

Cloud sessions use the tools, MCP servers, and models configured by the cloud service. They can't access {% data variables.product.prodname_vscode_shortname %} built-in tools or local development context.




<a name="local"></a>



<summary>Local</summary>

The Local harness runs interactively in the {% data variables.product.prodname_vscode_shortname %} [extension host](<https://github.com/docs/agents/concepts/agent-host.md#behavior-on-the-extension-host>) and works directly in your active workspace. It can use {% data variables.product.prodname_vscode_shortname %} built-in tools, extension-provided tools, MCP servers, and the models configured in {% data variables.product.prodname_vscode_shortname %}, including [bring your own key models](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>).

### Choose a built-in agent role

Local sessions provide these built-in agent roles:

* **Ask**: asks questions and provides guidance without making changes to the code.
* **Agent**: autonomously plans and performs complex coding tasks, edits files, runs commands, and iterates on results.
* **Plan**: researches a task and creates a structured implementation plan before code changes. Learn more about [planning in a Local session](<https://github.com/docs/agents/run/planning.md#plan-in-a-local-session>).

You can switch roles during a session from the agent picker.




## Hand off a session

Handoff continues ongoing work with a different agent configuration and carries the conversation history and context with it. A handoff can change the harness, execution environment, or agent role. Use handoff when another configuration is a better fit for the next part of the task.

For example, continue a Copilot session with Claude or Codex to use provider-specific capabilities, send a well-scoped task to the Cloud target for a pull request workflow, or move from the Plan agent to an implementation agent.

You can initiate a handoff only from a Local session. Local and remote Agent Host sessions don't show the **Session Target** dropdown, but they remain available as handoff destinations from a Local session.

To hand off a session to another harness or execution environment:

1. Open the session.

1. In the chat input, open the **Session Target** dropdown.

1. Select the target that should continue the work, such as Copilot, Claude, Codex, or Cloud.

{% data variables.product.prodname_vscode_shortname %} carries the conversation history and context to the selected target. The tools, permissions, and models might change because each harness, execution environment, or agent role provides different capabilities.

To implement a plan from the built-in **Plan** agent in a Local session, select **Start Implementation**. This switches to **Agent** in the current conversation and submits the implementation request. To continue in another supported session, open the dropdown next to **Start Implementation** and select an available **Continue in** destination. For the different controls in Local and {% data variables.product.prodname_copilot_short %} sessions, see [planning with agents](<https://github.com/docs/agents/run/planning.md>).

> [!TIP]
> In {% data variables.copilot.copilot_cli_short %}, enter `/delegate` to continue the work with a {% data variables.copilot.copilot_cloud_agent_short %}.

### Handoff compared to related actions

| Action | What it does |
|---|---|
| **Hand off** | Continues the work with a different harness, execution environment, or agent role and carries the conversation history and context with it. |
| **Fork a session** | Creates an independent session from a point in the conversation. Learn more about [forking sessions](<https://github.com/docs/agents/run/sessions/manage-sessions.md#fork-a-chat-session>). |
| **Switch surfaces** | Opens the same session in the [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>) or [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) without changing its harness or context. |

For background on how handoff works, see [Sessions and handoff](<https://github.com/docs/agents/concepts/sessions.md#hand-off-a-session>).

## Related resources

* [Agent harness concepts](<https://github.com/docs/agents/concepts/agent-harnesses.md>)
* [Manage agent sessions](<https://github.com/docs/agents/run/sessions/manage-sessions.md>)
* [Approvals and permissions](<https://github.com/docs/agents/run/approvals.md>)


</details>
