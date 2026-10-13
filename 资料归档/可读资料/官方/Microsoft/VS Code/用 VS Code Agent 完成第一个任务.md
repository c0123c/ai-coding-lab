# 用 VS Code Agent 完成第一个任务

以小型网页应用展示描述需求、生成代码、浏览器检查、人工验证和撤销修改的完整入门过程。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/quickstart.md>)；源码提交（存档版）：[2026-10-08](<https://github.com/microsoft/vscode-docs/commit/a88484276b0f7e053f9145da14de6a98f32b61c5>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/quickstart.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/用 VS Code Agent 完成第一个任务.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 9f3c7e21-6b48-4d5a-a097-2e1c8f64b3d9
DateApproved: 10/7/2026
MetaDescription: Build and validate a web app with an AI agent in {% data variables.product.prodname_vscode_shortname %}, then review and recover changes.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 快速入门：使用 agent 完成您的第一个任务

在本快速入门中，您将使用 {% data variables.product.prodname_vscode %} 中的 AI agent 根据自然语言提示构建小型 Web 应用。然后，您检查生成的代码，让 agent 使用浏览器工具验证应用程序，并自行验证结果。

{% action-card title="Build a complete app with agents" display="sidebar" %}
按照实践教程使用{% data variables.product.prodname_vscode_shortname %}中的agents构建和完善应用程序。

* [开始agents教程](<https://github.com/docs/agents/agents-tutorial.md>)

{% /action-card %}

## 先决条件

* [下载并安装{% data variables.product.prodname_vscode %}](<https://github.com/download>)。

* [在{% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/setup/copilot.md>)中设置{% data variables.product.prodname_copilot %}。

从 [{% data variables.product.prodname_copilot_short %} 安全带 ](<https://github.com/docs/agents/run/agent-harnesses.md#use-the-copilot-harness>) 开始完成本快速入门。它将模型连接到构建和测试应用程序的工具。

> [！NOTE]
> 本快速入门中的请求使用您的 Copilot 计划中的 AI 积分。 {% data variables.copilot.copilot_free_short %} 包括每月津贴。从状态栏打开 Copilot 状态仪表板以监控您每月的使用情况。详细了解 [AI 积分和模型成本 ](<https://github.com/docs/agents/concepts/language-models.md#ai-credits-and-model-costs>) 和 [ 达到限制时会发生什么情况 ](<https://github.com/docs/agents/agent-troubleshooting/faq.md#i-reached-my-inline-suggestions-or-ai-credits-limit>)。

## 1。创建项目文件夹

首先为快速入门应用程序创建一个空文件夹。在下一部分中，您将在 {% data variables.product.prodname_vscode_shortname %} 中打开此文件夹，并要求 agent 构建应用程序。

在终端中运行以下命令来创建文件夹：

```bash
mkdir agent-quickstart
```

## 2。构建应用程序

{% data variables.product.prodname_vscode_shortname %} 允许您以不同的方式使用 agents：

* **{% data variables.copilot.chat_view %}**：在编辑器中将 agent 与代码一起使用，重点关注当前项目。
* **{% data variables.copilot.agents_window %}**：将（高级）任务分配给agents并在项目之间切换，而无需重新加载窗口。

选择最适合您的方法，然后按照下面相应选项卡中的步骤进行操作。

{% tabs id="agent-surface" %}
{% 标签标签="{% data variables.copilot.chat_view %}" %}

1. 在{% data variables.product.prodname_vscode_shortname %}中，从菜单中选择**文件**>**打开文件夹**，然后打开`agent-quickstart`文件夹。

如果{% data variables.product.prodname_vscode_shortname %}询问您是否信任该文件夹，请从通知中选择**管理**，然后选择**信任**。

![信任{% data variables.product.prodname_vscode_shortname %}中的文件夹的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/editor-trust-folder.png>)

1. 用`kb(workbench.action.chat.open)`打开{% data variables.copilot.chat_view %}，然后选择`+`开始新的聊天。

![在Copilot {% data variables.copilot.chat_view %}中打开新聊天的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/editor-new-chat.png>)

如果您已经进行了一次活跃的聊天，则开始新的聊天不会关闭上一个聊天。之前的聊天仍然可以通过会话列表访问。

1. 为聊天会话配置以下设置。保留任何其他选项的默认值。

    |控制|价值|简短描述|
    |---------|-------|-------------------|
    |**会话目标**| **Copilot** |为会话选择 {% data variables.product.prodname_copilot_short %} 工具及其工具和工作流程。|
    | **Agent** | **Agent** |使用工具来规划、编辑文件、运行命令并验证结果。|
    |**语言模型**|**自动**|根据任务复杂性和可用性自动选择模型。|
    |**权限**|**手动权限**|请求您批准运行工具或访问资源。 agent可以在您的项目文件夹中进行文件编辑。|

![选择 Copilot agent 线束和具有手动权限的 Agent 角色的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/agent-session-editor-select-harness-role-2.png>)

1. 输入以下提示并按`kbstyle(Enter)`：

    ```prompt
    Create a task list web app in a single index.html file with embedded CSS and JavaScript. Let me add, complete, and delete tasks. Save the tasks in local storage so they persist after a page reload. Use no external libraries.
    ```

1. 遵循 agent 的进度并在接受每个批准请求之前对其进行审查。

agent 创建 `index.html` 文件并保存更新。对于您的批准设置未涵盖的操作，将显示批准提示。

    > [!TIP]
    > At any time you can stop the agent by selecting the **Stop** button in the chat input or steer it in another direction by sending a new prompt.

1. 如果agent要求在集成浏览器中打开`index.html`文件，请选择**在此会话中允许**。您可以直接在{% data variables.product.prodname_vscode_shortname %}中的集成浏览器中与应用程序交互。

![在集成浏览器中与应用程序交互的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/editor-integrated-browser-interaction.png>)

{% /tab %}
{% 标签标签="{% data variables.copilot.agents_window %}" %}

{% data variables.copilot.agents_window %}是一个专用窗口，用于跨不同项目与agents交互。

1. 要打开{% data variables.copilot.agents_window %}，请选择{% data variables.product.prodname_vscode_shortname %}标题栏中的**在Agents**中打开或运行**聊天：从命令面板（`kb(workbench.action.showCommands)`）打开{% data variables.copilot.agents_window %}**。

![在{% data variables.product.prodname_vscode_shortname %}中打开{% data variables.copilot.agents_window %}的截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/open-agents-window.png>)

1. 选择左侧边栏顶部的**新建**以启动新的agent会话。

![在重新设计的新会话输入中启动新的 agent 会话的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/agent-session-new.png>)

1. 由于{% data variables.copilot.agents_window %}可以跨不同项目运行，因此从下拉列表中选择您之前创建的`agent-quickstart`文件夹。

如果{% data variables.product.prodname_vscode_shortname %}询问您是否信任该文件夹，请选择**信任**。

1. 为会话配置以下设置。保留任何其他选项的默认值。

    |控制|价值|简短描述|
    |---------|-------|-------------------|
    |**会话目标**| **Copilot** |为会话选择 {% data variables.product.prodname_copilot_short %} 工具及其工具和工作流程。|
    | **Agent** | **Agent** |使用工具来规划、编辑文件、运行命令并验证结果。|
    |**语言模型**|**自动**|根据任务复杂性和可用性自动选择模型。|
    |**权限**|**手动权限**|请求您批准运行工具或访问资源。 agent可以在您的项目文件夹中进行文件编辑。|

![在重新设计的新会话输入中选择 Copilot agent 工具、Agent 角色和手动权限的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/agent-session-select-harness-role.png>)

1. 输入以下提示并按`kbstyle(Enter)`：

    ```prompt
    Create a task list web app in a single index.html file with embedded CSS and JavaScript. Let me add, complete, and delete tasks. Save the tasks in local storage so they persist after a page reload. Use no external libraries.
    ```

1. 遵循 agent 的进度并在接受每个批准请求之前对其进行审查。

agent 创建 `index.html` 文件并保存更新。对于您的批准设置未涵盖的操作，将显示批准提示。

    > [!TIP]
    > At any time you can stop the agent by selecting the **Stop** button in the chat input or steer it in another direction by sending a new prompt.

1. 如果agent要求在集成浏览器中打开`index.html`文件，请选择**在此会话中允许**。您可以直接在{% data variables.product.prodname_vscode_shortname %}中的集成浏览器中与应用程序交互。

![在集成浏览器中与应用程序交互的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/agent-integrated-browser-interaction.png>)

{% /tab %}
{% /tabs %}

## 3。审查并验证结果

It's important to review the generated code and outcome carefully.您可以通过在集成浏览器中运行应用程序并观察其行为，让 agent 为您验证关键场景和边缘情况。

agent 可能已经启动了集成浏览器，以验证应用程序是否正确运行，作为前面步骤中创建任务列表 Web 应用程序的一部分。

在以下步骤中，您将要求 agent 验证任务列表 Web 应用程序的基本功能。

{% tabs id="agent-surface" %}
{% 标签标签="{% data variables.copilot.chat_view %}" %}

1. The {% data variables.copilot.chat_view %} indicates it changed one file and shows diff stats.选择它以查看生成的代码及其差异。

1. 输入以下提示，让 agent 在集成浏览器中打开应用程序并验证其功能。

    ```prompt
    Open index.html in the integrated browser and validate the app.
    Add a task, mark it complete, and delete it. Then add another task,
    reload the page, and verify that the task persists. If any step fails,
    fix the issue and repeat the complete flow.
    ```

1. 请注意 agent 如何与集成浏览器交互并自动验证不同的用户场景。

![验证应用程序的集成浏览器的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/editor-integrated-browser-validation.png>)

{% /tab %}
{% 标签标签="{% data variables.copilot.agents_window %}" %}

1. 打开右侧边栏（`kb(workbench.view.agentSessions.changesContainer)`）中的**更改**面板，然后选择`index.html`以查看生成的代码。

如果您对结果的特定部分不满意，请在差异视图中选择它并输入反馈以将其发送到 agent。

![在“更改”面板中查看生成的代码的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/review-changes-panel.png>)

1. 输入以下提示，让 agent 在集成浏览器中打开应用程序并验证其功能。

    ```prompt
    Open index.html in the integrated browser and validate the app.
    Add a task, mark it complete, and delete it. Then add another task,
    reload the page, and verify that the task persists. If any step fails,
    fix the issue and repeat the complete flow.
    ```

1. 请注意 agent 如何与集成浏览器交互并自动验证不同的用户场景。

![验证应用程序的集成浏览器的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/integrated-browser-validation.png>)

{% /tab %}
{% /tabs %}

要通过键盘和屏幕阅读器访问生成的差异，请使用[可访问的差异查看器](<https://github.com/docs/configure/accessibility/accessibility.md#diff-editor-accessibility>)。

## 4。自己验证一下结果

agent的验证报告可以帮助您发现问题，但它不能取代您自己的评论。在集成浏览器中：

1. 添加任务并将其标记为完成。
1. 重新加载页面并确认任务及其已完成状态仍然存在。
1. 删除任务并确认它在再次重新加载后不会返回。

如果检查失败，请描述您在聊天中观察到的情况，并要求 agent 修复并重新测试。在提交更改之前查看生成的差异。

您已经完成了第一个任务 agent。 agent 解释了您的目标，创建了代码，在浏览器中运行了应用程序，并修复了发现的问题。您查看了代码和结果。

## 停止、修改或撤消工作

如果您对 agent 的行为或结果不满意，您有多种选择：

* **停止或重定向当前请求**：请求正在运行时，使用**发送**下拉列表选择**通过消息转向**或**停止和发送**。停止不会撤消已经完成的操作。

* **修改结果**：发送后续提示。在差异中，您可以选择代码并在界面提供该操作时提供有针对性的反馈。

* **撤消请求中的文件更改**：将鼠标悬停在较早的请求上，然后选择**恢复检查点**。这将恢复受影响的工作区文件和聊天历史记录。它不会反转终端命令、网络请求、部署或对外部服务的更改。了解有关 [查看和恢复 agent 更改 ](<https://github.com/docs/agents/run/review-code-edits.md>) 的更多信息。

如果后续操作未能解决问题，请使用 [让 agent 回到正轨 ](<https://github.com/docs/agents/guides/get-agent-back-on-track.md>) 选择您的下一个恢复操作。

## 如果您的屏幕看起来有所不同

* 如果 **Copilot** 目标或 **Agent** 角色不可用，请验证您的登录并查看 [agent 线束设置要求 ](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>)。您组织的策略可能会限制特定的 agents、模型或工具。

* 如果您达到 AI 积分限额，请查看 [ 剩余可用金额以及配额何时重置 ](<https://github.com/docs/agents/agent-troubleshooting/faq.md#i-reached-my-inline-suggestions-or-ai-credits-limit>)。

<a name="optional-continue-in-the-other-surface"></a>

## Continue 在另一个界面或应用程序中

{% data variables.copilot.agents_window %} 和 {% data variables.copilot.chat_view %} 共享您的 {% data variables.product.prodname_copilot_short %} 会话，因此您可以在它们之间切换而不会丢失对话。

* 从{% data variables.copilot.agents_window %}中，选择标题栏中的**在编辑器中打开**。 {% data variables.product.prodname_vscode_shortname %} 在编辑器窗口中打开项目，会话在 {% data variables.copilot.chat_view %} 中可用。

* From the {% data variables.copilot.chat_view %}, select **Open in Agents** in the title bar. The {% data variables.copilot.agents_window %} opens with the same session selected.

如果您在 {% data variables.copilot.copilot_cli %} 或 {% data variables.copilot.github_copilot_app %} 中开始工作，则可以 [ 从 {% data variables.product.prodname_vscode_shortname %} 中的这些应用程序 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) 打开并继续支持的本地会话。要在 CLI 中继续 {% data variables.product.prodname_copilot_short %} 会话，请右键单击该会话并选择 [**在终端中恢复 **](<https://github.com/docs/agents/run/agent-harnesses.md#use-copilot-cli-from-the-terminal>)。

## 清理资源

When you no longer need the app, run these steps to clean up your local resources:

* 关闭{% data variables.product.prodname_vscode_shortname %}中的`agent-quickstart`文件夹。
* 从计算机中删除 `agent-quickstart` 文件夹。

## 后续步骤

* [将工作流应用于您自己项目中的有界任务](<https://github.com/docs/agents/best-practices.md#apply-the-workflow-to-your-project>)。
* [在不更改文件的情况下探索代码库](<https://github.com/docs/agents/guides/explore-a-codebase.md>)。
* [Build a complete app and learn the editor, browser, and source control workflows](<https://github.com/docs/agents/agents-tutorial.md>).
* [Review the recommended security baseline](<https://github.com/docs/agents/run/security.md#recommended-security-baseline>).


## 原文对照

<details>
<summary>展开完整原文</summary>

# Quickstart: Complete your first task with an agent

In this quickstart, you use an AI agent in {% data variables.product.prodname_vscode %} to build a small web app from a natural-language prompt. You then review the generated code, let the agent validate the app with browser tools, and verify the result yourself.

{% action-card title="Build a complete app with agents" display="sidebar" %}
Follow a hands-on tutorial to build and refine an app with agents in {% data variables.product.prodname_vscode_shortname %}.

* [Start agents tutorial](<https://github.com/docs/agents/agents-tutorial.md>)

{% /action-card %}

## Prerequisites

* [Download and install {% data variables.product.prodname_vscode %}](<https://github.com/download>).

* [Set up {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/setup/copilot.md>).

    Start with the [{% data variables.product.prodname_copilot_short %} harness](<https://github.com/docs/agents/run/agent-harnesses.md#use-the-copilot-harness>) to complete this quickstart. It connects the model to the tools that build and test your app.

> [!NOTE]
> Requests in this quickstart use AI credits from your Copilot plan. {% data variables.copilot.copilot_free_short %} includes a monthly allowance. Open the Copilot status dashboard from the Status Bar to monitor your monthly usage. Learn more about [AI credits and model costs](<https://github.com/docs/agents/concepts/language-models.md#ai-credits-and-model-costs>) and [what happens when you reach a limit](<https://github.com/docs/agents/agent-troubleshooting/faq.md#i-reached-my-inline-suggestions-or-ai-credits-limit>).

## 1. Create a project folder

Start by creating an empty folder for the quickstart app. In the next section, you open this folder in {% data variables.product.prodname_vscode_shortname %} and ask an agent to build the app.

Run the following command in your terminal to create the folder:

```bash
mkdir agent-quickstart
```

## 2. Build the app

{% data variables.product.prodname_vscode_shortname %} lets you work with agents in different ways:

* **{% data variables.copilot.chat_view %}**: work with an agent alongside your code in the editor, focused on the current project.
* **{% data variables.copilot.agents_window %}**: assign (high-level) tasks to agents and switch between projects without reloading the window.

Choose the approach that works best for you and follow the steps in the corresponding tab below.

{% tabs id="agent-surface" %}
{% tab label="{% data variables.copilot.chat_view %}" %}

1. In {% data variables.product.prodname_vscode_shortname %}, select **File** > **Open Folder** from the menu, and then open the `agent-quickstart` folder.

    If {% data variables.product.prodname_vscode_shortname %} asks whether you trust the folder, select **Manage** from the notification, and then select **Trust**.

    ![Screenshot of trusting the folder in {% data variables.product.prodname_vscode_shortname %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/editor-trust-folder.png>)

1. Open the {% data variables.copilot.chat_view %} with `kb(workbench.action.chat.open)`, and then select `+` to start a new chat.

    ![Screenshot of opening a new chat in the Copilot {% data variables.copilot.chat_view %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/editor-new-chat.png>)

    If you already had an active chat, starting a new chat does not close the previous one. The previous chat remains accessible via the sessions list.

1. Configure the following settings for the chat session. Keep the default values for any other options.

    | Control | Value | Short description |
    |---------|-------|-------------------|
    | **Session Target** | **Copilot** | Selects the {% data variables.product.prodname_copilot_short %} harness and its tools and workflow for the session. |
    | **Agent** | **Agent** | Uses tools to plan, edit files, run commands, and validate the result. |
    | **Language model** | **Auto** | Automatically selects a model based on task complexity and availability. |
    | **Permissions** | **Manual permissions** | Requests your approval for running tools or accessing resources. The agent can make file edits in your project folder. |

    ![Screenshot of selecting the Copilot agent harness and the Agent role with Manual permissions.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/agent-session-editor-select-harness-role-2.png>)

1. Enter the following prompt and press `kbstyle(Enter)`:

    ```prompt
    Create a task list web app in a single index.html file with embedded CSS and JavaScript. Let me add, complete, and delete tasks. Save the tasks in local storage so they persist after a page reload. Use no external libraries.
    ```

1. Follow the agent's progress and review each approval request before you accept it.

    The agent creates the `index.html` file and saves updates as it progresses. Approval prompts appear for actions that aren't covered by your approval settings.

    > [!TIP]
    > At any time you can stop the agent by selecting the **Stop** button in the chat input or steer it in another direction by sending a new prompt.

1. If the agent asks to open the `index.html` file in the integrated browser, select **Allow in this Session**. You can interact with the app directly in the integrated browser in {% data variables.product.prodname_vscode_shortname %}.

    ![Screenshot of interacting with the app in the integrated browser.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/editor-integrated-browser-interaction.png>)

{% /tab %}
{% tab label="{% data variables.copilot.agents_window %}" %}

The {% data variables.copilot.agents_window %} is a dedicated window for interacting with agents across different projects.

1. To open the {% data variables.copilot.agents_window %}, select **Open in Agents** in the title bar of {% data variables.product.prodname_vscode_shortname %} or run **Chat: Open {% data variables.copilot.agents_window %}** from the Command Palette (`kb(workbench.action.showCommands)`).

    ![Screenshot of opening the {% data variables.copilot.agents_window %} in {% data variables.product.prodname_vscode_shortname %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/open-agents-window.png>)

1. Select **New** at the top of the left sidebar to start a new agent session.

    ![Screenshot of starting a new agent session in the redesigned new-session input.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/agent-session-new.png>)

1. Because the {% data variables.copilot.agents_window %} can operate across different projects, select the `agent-quickstart` folder you created earlier from the dropdown.

    If {% data variables.product.prodname_vscode_shortname %} asks whether you trust the folder, select **Trust**.

1. Configure the following settings for the session. Keep the default values for any other options.

    | Control | Value | Short description |
    |---------|-------|-------------------|
    | **Session Target** | **Copilot** | Selects the {% data variables.product.prodname_copilot_short %} harness and its tools and workflow for the session. |
    | **Agent** | **Agent** | Uses tools to plan, edit files, run commands, and validate the result. |
    | **Language model** | **Auto** | Automatically selects a model based on task complexity and availability. |
    | **Permissions** | **Manual permissions** | Requests your approval for running tools or accessing resources. The agent can make file edits in your project folder. |

    ![Screenshot of selecting the Copilot agent harness, Agent role, and Manual permissions in the redesigned new-session input.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/agent-session-select-harness-role.png>)

1. Enter the following prompt and press `kbstyle(Enter)`:

    ```prompt
    Create a task list web app in a single index.html file with embedded CSS and JavaScript. Let me add, complete, and delete tasks. Save the tasks in local storage so they persist after a page reload. Use no external libraries.
    ```

1. Follow the agent's progress and review each approval request before you accept it.

    The agent creates the `index.html` file and saves updates as it progresses. Approval prompts appear for actions that aren't covered by your approval settings.

    > [!TIP]
    > At any time you can stop the agent by selecting the **Stop** button in the chat input or steer it in another direction by sending a new prompt.

1. If the agent asks to open the `index.html` file in the integrated browser, select **Allow in this Session**. You can interact with the app directly in the integrated browser in {% data variables.product.prodname_vscode_shortname %}.

    ![Screenshot of interacting with the app in the integrated browser.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/agent-integrated-browser-interaction.png>)

{% /tab %}
{% /tabs %}

## 3. Review and validate the result

It's important to review the generated code and outcome carefully. You can let the agent validate key scenarios and edge cases for you by running the app in the integrated browser and observing its behavior.

The agent might already have launched the integrated browser to validate that the app runs correctly as part of creating the task list web app in the previous steps.

In the following steps you'll ask the agent to validate the basic functionality of the task list web app.

{% tabs id="agent-surface" %}
{% tab label="{% data variables.copilot.chat_view %}" %}

1. The {% data variables.copilot.chat_view %} indicates it changed one file and shows diff stats. Select it to review the generated code and its diff.

1. Enter the following prompt to have the agent open the app in the integrated browser and validate its functionality.

    ```prompt
    Open index.html in the integrated browser and validate the app.
    Add a task, mark it complete, and delete it. Then add another task,
    reload the page, and verify that the task persists. If any step fails,
    fix the issue and repeat the complete flow.
    ```

1. Notice how the agent interacts with the integrated browser and autonomously validates different user scenarios.

    ![Screenshot of the integrated browser validating the app.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/editor-integrated-browser-validation.png>)

{% /tab %}
{% tab label="{% data variables.copilot.agents_window %}" %}

1. Open the **Changes** panel in the right sidebar (`kb(workbench.view.agentSessions.changesContainer)`) and select `index.html` to review the generated code.

    If you're not happy with a specific part of the result, select it in the diff view and enter feedback to send it to the agent.

    ![Screenshot of reviewing the generated code in the Changes panel.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/review-changes-panel.png>)

1. Enter the following prompt to have the agent open the app in the integrated browser and validate its functionality.

    ```prompt
    Open index.html in the integrated browser and validate the app.
    Add a task, mark it complete, and delete it. Then add another task,
    reload the page, and verify that the task persists. If any step fails,
    fix the issue and repeat the complete flow.
    ```

1. Notice how the agent interacts with the integrated browser and autonomously validates different user scenarios.

    ![Screenshot of the integrated browser validating the app.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/integrated-browser-validation.png>)

{% /tab %}
{% /tabs %}

For keyboard and screen reader access to the generated diffs, use the [Accessible Diff Viewer](<https://github.com/docs/configure/accessibility/accessibility.md#diff-editor-accessibility>).

## 4. Verify the result yourself

The agent's validation report helps you find problems, but it doesn't replace your own review. In the integrated browser:

1. Add a task and mark it complete.
1. Reload the page and confirm that the task and its completed state persist.
1. Delete the task and confirm that it doesn't return after another reload.

If a check fails, describe what you observed in chat and ask the agent to fix and retest it. Review the resulting diff before you commit the changes.

You have completed your first task with an agent. The agent interpreted your goal, created the code, exercised the app in the browser, and fixed issues it found. You reviewed both the code and the result.

## Stop, revise, or undo the work

If you're not satisfied with the agent's actions or the results, you have several options:

* **Stop or redirect the current request**: while a request is running, use the **Send** dropdown to choose **Steer with Message** or **Stop and Send**. Stopping doesn't undo actions that already completed.

* **Revise the result**: send a follow-up prompt. In a diff, you can select code and provide focused feedback when the interface offers that action.

* **Undo file changes from a request**: hover over an earlier request and select **Restore Checkpoint**. This restores affected workspace files and chat history. It doesn't reverse terminal commands, network requests, deployments, or changes to external services. Learn more about [reviewing and reverting agent changes](<https://github.com/docs/agents/run/review-code-edits.md>).

If a follow-up doesn't resolve the problem, use [Get an agent back on track](<https://github.com/docs/agents/guides/get-agent-back-on-track.md>) to choose your next recovery action.

## If your screen looks different

* If the **Copilot** target or **Agent** role isn't available, verify your sign-in and review the [agent harness setup requirements](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>). Your organization's policies might restrict specific agents, models, or tools.

* If you reach an AI credits limit, review [what remains available and when allowances reset](<https://github.com/docs/agents/agent-troubleshooting/faq.md#i-reached-my-inline-suggestions-or-ai-credits-limit>).

<a name="optional-continue-in-the-other-surface"></a>

## Continue in another interface or application

The {% data variables.copilot.agents_window %} and {% data variables.copilot.chat_view %} share your {% data variables.product.prodname_copilot_short %} session, so you can switch between them without losing the conversation.

* From the {% data variables.copilot.agents_window %}, select **Open in Editor** in the title bar. {% data variables.product.prodname_vscode_shortname %} opens the project in an editor window with the session available in the {% data variables.copilot.chat_view %}.

* From the {% data variables.copilot.chat_view %}, select **Open in Agents** in the title bar. The {% data variables.copilot.agents_window %} opens with the same session selected.

If you start work in {% data variables.copilot.copilot_cli %} or the {% data variables.copilot.github_copilot_app %}, you can [open and continue supported local sessions from those applications](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>) in {% data variables.product.prodname_vscode_shortname %}. To continue a {% data variables.product.prodname_copilot_short %} session in the CLI, right-click the session and select [**Resume in Terminal**](<https://github.com/docs/agents/run/agent-harnesses.md#use-copilot-cli-from-the-terminal>).

## Clean up resources

When you no longer need the app, run these steps to clean up your local resources:

* Close the `agent-quickstart` folder in {% data variables.product.prodname_vscode_shortname %}.
* Delete the `agent-quickstart` folder from your computer.

## Next steps

* [Apply the workflow to a bounded task in your own project](<https://github.com/docs/agents/best-practices.md#apply-the-workflow-to-your-project>).
* [Explore a codebase without changing files](<https://github.com/docs/agents/guides/explore-a-codebase.md>).
* [Build a complete app and learn the editor, browser, and source control workflows](<https://github.com/docs/agents/agents-tutorial.md>).
* [Review the recommended security baseline](<https://github.com/docs/agents/run/security.md#recommended-security-baseline>).


</details>
