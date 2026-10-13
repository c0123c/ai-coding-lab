# 创建和管理 Agent 自动化

介绍保存提示与会话配置、从模板创建任务、执行和审查结果、设置周期并导入导出的流程。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方审核：[2026-10-07](<https://raw.githubusercontent.com/microsoft/vscode-docs/main/docs/agents/run/automations.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/microsoft/vscode-docs/main/docs/agents/run/automations.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/创建和管理 Agent 自动化.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 76a5b461-bcbd-474f-9638-d4533c51a4e3
DateApproved: 10/7/2026
FeatureStatus: automations
MetaDescription: Create, share, schedule, and manage agent automations in the {% data variables.copilot.agents_window %}, including reusable templates.
MetaSocialImage: ../images/agents-window/agents-window-ui-annotated.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 创建和管理 agent 自动化

自动化根据保存的提示、会话配置和计划运行 agent 任务。使用它们进行工作，例如总结存储库更改或对问题进行分类，而无需手动启动每个会话。按需或按定期计划运行自动化。您还可以从模板开始或将自动化共享为可重用文件。

本文介绍如何创建、共享、运行、计划和管理自动化。

## 先决条件

* 使用可用的 agent 设置并打开 [{% data variables.copilot.agents_window %}](<https://raw.githubusercontent.com/docs/agents/run/agents-window.md#prerequisites>)。
* 在设置编辑器中启用 `setting(chat.automations.enabled)`。

自动化正在逐步推广。该设置在稳定版中默认关闭，在 Insiders 中默认打开。如果 **自动化 ** 在侧边栏中不可见，请检查该设置是否已启用。

## 创建自动化

1. 在 {% data variables.copilot.agents_window %} 侧栏中，选择 ** 自动化 **。

1. 选择**创建自动化**或[从模板开始](<#从模板开始>)以填写启动提示和时间表。

1. 输入描述性的 ** 名称 ** 和 ** 提示 **。指定任务、其范围以及您期望的输出。例如：

    ```prompt
    Summarize commits on the current branch from the last 24 hours.
    Group the summary by feature, fix, and maintenance work.
    Include commit references and flag changes that might need documentation.
    Do not modify files.
    ```

如果您从模板开始，请在保存之前查看并调整其提示。

1. 选择 agent 应该工作的工作区，或者为不需要项目文件的任务选择 **无工作区 **。可用的 agents 取决于此选择。

1. 选择可用的 agent 并查看 **会话配置 **，包括模型和权限选项。对于 Git 工作区，当 agent 支持隔离时，您可以选择 ** 新建工作树 ** 和基础分支。了解如何[选择agent和代码隔离](<https://raw.githubusercontent.com/docs/agents/run/agent-harnesses.md>)。

1. 将**计划**设置为**手动**以在重复执行任务之前检查任务，然后选择**创建**。

![显示带有提示、工作区、会话配置和计划控件的自动化创建表单的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs//docs/agents/images/automations/automation-creation-form.png>)

> [！CAUTION]
> 自动化可以根据其agent的权限读取文件、运行命令并进行更改。在安排无人值守工作之前​​，请检查 [ 权限级别 ](<https://raw.githubusercontent.com/docs/agents/run/approvals.md#permission-levels>)。保存自动化不会绕过组织策略或保证将来的运行不需要您的批准。

## 从模板开始

自动化模板提供了您可以查看和调整的起始名称、提示和时间表：

* **内置模板**包含{% data variables.product.prodname_vscode_shortname %}附带的模板。
* **来自插件的模板**包含来自已启用的[Agent插件](<https://raw.githubusercontent.com/docs/agent-customization/agent-plugins.md#automations-in-plugins>)的模板。每个模板都会显示一个 **Plugin** 徽章及其源插件。

当您没有保存的自动化时，模板部分开始展开；当您保存自动化时，模板部分会折叠。 **插件模板**上的蓝色标记表示已启用的插件贡献了新模板。展开该部分以清除标记。

选择一个模板以在**新建自动化**对话框中将其打开。查看提示和计划，然后选择工作区、agent、模型、权限和隔离。默认情况下，**启用**复选框处于清除状态。仅在检查完整配置后才选择它，然后选择**创建**。

禁用插件将从视图中删除其模板。您已从这些模板创建的自动化及其运行历史记录仍然可用。

## 运行并审查自动化

在自动化卡上选择**立即运行**。自动化启动 agent 会话，其运行显示在 ** 历史记录 ** 下。

在 ** 历史记录 ** 中选择一个运行以打开其会话。在依赖任务的输出之前检查响应和任何更改。如果会议需要批准，请在那里查看请求。如果运行失败，请在重试之前检查其错误和会话。

每次运行都使用选定的 agent 和模型，因此在选择计划时请考虑重复使用情况。

## 设置重复时间表

检查第一次运行后，选择自动化的名称进行编辑。选择**计划**，保持选中**已启用**，然后选择**保存**。

|时间表|当它运行时|
|---|---|
|**说明书**|仅当您选择**立即运行**时。|
|**每小时**|每个小时。|
|**每日**|每天在选定的 ** 时间 **。|
|**每周**|在选定的 ** 时间 ** 选定的 ** 星期几 **。|

每日和每周的时间表使用您当地的时区。

请记住这些执行要求：

* 保持运行自动化的机器处于唤醒状态并且其 agent 可用。 Agent 主机支持的计划需要正在运行的 Agent 主机进程。其他计划需要运行的 {% data variables.product.prodname_vscode_shortname %} 窗口。不要假设退出应用程序后本地自动化会继续。
* 中断后，错过时间表可能会触发补跑。不要依赖重播每一个错过的事件。
* 自动化一次运行一个会话。另一个计划的事件不会启动同一自动化的并行运行。

## 分享一个自动化

将自动化导出为 `.automation.md` 文件，以便在另一台计算机上重复使用它或与其他人共享。该文件是可读的 Markdown，带有 YAML frontmatter，并且仅包含可移植信息：

* 名称和提示。
* 手动、每小时、每天或每周计划。
* 文件格式版本和可移植标识符。

该文件不包含工作区、提供程序、模型、权限、启用状态或运行历史记录。导入文件的人在本地选择这些选项。

### 导出自动化

1. 在自动化卡上，选择 **更多操作** > **导出**。

1. 选择保存建议的 `.automation.md` 文件的位置。

您可以在编辑器中打开导出的文件，以在共享之前查看其提示和时间表。

### 导入自动化

1. 在自动化视图中选择 ** 导入自动化 **，然后选择 `.automation.md` 文件。您还可以将一个 `.automation.md` 文件拖到视图上的任意位置。

1. 在 **新建自动化** 对话框中，查看导入的名称、提示和计划。

1. 选择自动化的工作区、agent、模型、权限和隔离。

1. 默认情况下，**已启用**复选框处于清除状态。仅在检查完整配置后才选择它，然后选择**创建**。

{% data variables.product.prodname_vscode_shortname %} 拒绝不受支持的文件，而不是默默地更改其计划或导入执行权限。

## 管理自动化

* **编辑**：选择自动化的名称，更新其提示、目标、会话配置或计划，然后选择**保存**。
* **启用或禁用将来运行**：选择**更多操作** > **启用**或**禁用**。禁用自动化不会停止正在进行的运行。
* **复制**：选择**更多操作** > **复制**，查看**新自动化**中的副本对话框，然后选择**创建**。
* **停止活动运行**：在**历史记录**中为正在运行的会话选择**停止**。
* **导出**：选择**更多操作** > **导出**。有关更多信息，请参阅[共享自动化](<#分享一个自动化>)。
* **删除**：选择**更多操作** > **删除**并确认。这将永久删除自动化及其运行历史记录。

## 后续步骤

* [管理 agent 会话](<https://raw.githubusercontent.com/docs/agents/run/sessions/manage-sessions.md>)
* [发现和管理 Agent 插件](<https://raw.githubusercontent.com/docs/agent-customization/agent-plugins.md>)
* [查看 AI-生成的代码编辑](<https://raw.githubusercontent.com/docs/agents/run/review-code-edits.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Create and manage agent automations

Automations run agent tasks from a saved prompt, session configuration, and schedule. Use them for work such as summarizing repository changes or triaging issues without starting each session manually. Run an automation on demand or on a recurring schedule. You can also start from a template or share an automation as a reusable file.

This article explains how to create, share, run, schedule, and manage automations.

## Prerequisites

* Set up and open the [{% data variables.copilot.agents_window %}](<https://raw.githubusercontent.com/docs/agents/run/agents-window.md#prerequisites>) with an available agent.
* Enable `setting(chat.automations.enabled)` in the Settings editor.

Automations are rolling out gradually. The setting is off by default on Stable and on by default in Insiders. If **Automations** isn't visible in the sidebar, check that the setting is enabled.

## Create an automation

1. In the {% data variables.copilot.agents_window %} sidebar, select **Automations**.

1. Select **Create Automation**, or [start from a template](<#start-from-a-template>) to fill in a starting prompt and schedule.

1. Enter a descriptive **Name** and a **Prompt**. Specify the task, its scope, and the output you expect. For example:

    ```prompt
    Summarize commits on the current branch from the last 24 hours.
    Group the summary by feature, fix, and maintenance work.
    Include commit references and flag changes that might need documentation.
    Do not modify files.
    ```

    If you start with a template, review and adapt its prompt before saving.

1. Choose the workspace where the agent should work, or **No workspace** for a task that doesn't need project files. The available agents depend on this choice.

1. Choose an available agent and review **Session configuration**, including the model and permission options. For a Git workspace, you can select **New Worktree** and a base branch when the agent supports isolation. Learn how to [choose an agent and code isolation](<https://raw.githubusercontent.com/docs/agents/run/agent-harnesses.md>).

1. Set **Schedule** to **Manual** to check the task before making it recurring, and then select **Create**.

![Screenshot showing the automation creation form with the prompt, workspace, session configuration, and schedule controls.](<https://raw.githubusercontent.com/microsoft/vscode-docs//docs/agents/images/automations/automation-creation-form.png>)

> [!CAUTION]
> An automation can read files, run commands, and make changes according to its agent's permissions. Review the [permission level](<https://raw.githubusercontent.com/docs/agents/run/approvals.md#permission-levels>) before scheduling unattended work. Saving an automation doesn't bypass organization policies or guarantee that future runs won't need your approval.

## Start from a template

Automation templates provide a starting name, prompt, and schedule that you can review and adapt:

* **Built-in Templates** contains templates included with {% data variables.product.prodname_vscode_shortname %}.
* **Templates from Plugins** contains templates from enabled [Agent Plugins](<https://raw.githubusercontent.com/docs/agent-customization/agent-plugins.md#automations-in-plugins>). Each template shows a **Plugin** badge and its source plugin.

The template sections start expanded when you have no saved automations and collapsed when you have saved automations. A blue marker on **Templates from Plugins** indicates that an enabled plugin contributed a new template. Expand the section to clear the marker.

Select a template to open it in the **New Automation** dialog. Review the prompt and schedule, and then choose the workspace, agent, model, permissions, and isolation. The **Enabled** checkbox is cleared by default. Select it only after you review the complete configuration, and then select **Create**.

Disabling a plugin removes its templates from the view. Automations that you already created from those templates and their run history remain available.

## Run and review an automation

Select **Run now** on the automation card. The automation starts an agent session, and its run appears under **History**.

Select a run in **History** to open its session. Review the response and any changes before relying on the task's output. If the session needs approval, review the request there. If a run fails, inspect its error and session before trying again.

Each run uses the selected agent and model, so account for recurring usage when choosing a schedule.

## Set a recurring schedule

After checking the first run, select the automation's name to edit it. Choose a **Schedule**, keep **Enabled** selected, and select **Save**.

| Schedule | When it runs |
|---|---|
| **Manual** | Only when you select **Run now**. |
| **Hourly** | Every hour. |
| **Daily** | At the selected **Time** each day. |
| **Weekly** | At the selected **Time** on the selected **Day of week**. |

Daily and weekly schedules use your local time zone.

Keep these execution requirements in mind:

* Keep the machine that runs the automation awake and its agent available. Agent Host-backed schedules require a running Agent Host process. Other schedules require a running {% data variables.product.prodname_vscode_shortname %} window. Don't assume that local automations continue after you quit the application.
* After an interruption, a missed schedule can trigger a catch-up run. Don't rely on every missed occurrence being replayed.
* An automation runs one session at a time. Another scheduled occurrence doesn't start a parallel run of the same automation.

## Share an automation

Export an automation as a `.automation.md` file to reuse it on another machine or share it with someone else. The file is readable Markdown with YAML frontmatter and contains only portable information:

* Name and prompt.
* Manual, hourly, daily, or weekly schedule.
* File format version and portable identifier.

The file doesn't include the workspace, provider, model, permissions, enabled state, or run history. The person who imports the file chooses these options locally.

### Export an automation

1. On the automation card, select **More Actions** > **Export**.

1. Choose where to save the proposed `.automation.md` file.

You can open the exported file in an editor to review its prompt and schedule before you share it.

### Import an automation

1. Select **Import Automation** in the Automations view, and then choose a `.automation.md` file. You can also drag one `.automation.md` file anywhere over the view.

1. In the **New Automation** dialog, review the imported name, prompt, and schedule.

1. Choose the workspace, agent, model, permissions, and isolation for the automation.

1. The **Enabled** checkbox is cleared by default. Select it only after you review the complete configuration, and then select **Create**.

{% data variables.product.prodname_vscode_shortname %} rejects unsupported files instead of silently changing their schedule or importing execution permissions.

## Manage an automation

* **Edit**: select the automation's name, update its prompt, target, session configuration, or schedule, and select **Save**.
* **Enable or disable future runs**: select **More Actions** > **Enable** or **Disable**. Disabling an automation doesn't stop a run already in progress.
* **Duplicate**: select **More Actions** > **Duplicate**, review the copy in the **New Automation** dialog, and select **Create**.
* **Stop an active run**: select **Stop** for the running session in **History**.
* **Export**: select **More Actions** > **Export**. For more information, see [Share an automation](<#share-an-automation>).
* **Delete**: select **More Actions** > **Delete** and confirm. This permanently deletes the automation and its run history.

## Next steps

* [Manage agent sessions](<https://raw.githubusercontent.com/docs/agents/run/sessions/manage-sessions.md>)
* [Discover and manage Agent Plugins](<https://raw.githubusercontent.com/docs/agent-customization/agent-plugins.md>)
* [Review AI-generated code edits](<https://raw.githubusercontent.com/docs/agents/run/review-code-edits.md>)


</details>
