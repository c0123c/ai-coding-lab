# VS Code Agent 编程实践教程

用个人作品网页串联生成、预览、修改、审查和提交，并展示从 Agent 窗口转入编辑器继续同一会话。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/agents-tutorial.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/agents-tutorial.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code Agent 编程实践教程.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 72ad9b70-5227-4032-81d7-6aec00a1e8f8
DateApproved: 10/7/2026
MetaDescription: Build an app with AI agents in {% data variables.product.prodname_vscode_shortname %} and learn editor, browser, and source control workflows.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 教程：{% data variables.product.prodname_vscode_shortname %} 中的代理编码

在本教程中，您将在{% data variables.product.prodname_vscode %}中使用AI agents构建个人投资组合页面。您用自然语言描述您想要的内容，然后 agent 创建和编辑文件。然后您查看并测试结果。该应用程序使用 HTML、CSS 和 JavaScript，因此您无需安装任何运行时或构建工具。

您从 **{% data variables.copilot.agents_window %}** 开始创建应用程序，然后在 **{% data variables.copilot.chat_view %}** 中继续相同的会话，以与您的代码一起完善它。在此过程中，您将学习如何打开项目文件夹、在集成浏览器中预览应用程序以及使用 Git 检查和提交更改。

{% action-card title="学习 {% data variables.product.prodname_vscode_shortname %} 编辑器功能" display="inline" %}
熟悉 {% data variables.product.prodname_vscode_shortname %} 用户界面、编辑功能和关键生产力工具。

* [启动{% data variables.product.prodname_vscode_shortname %}编辑教程](<https://github.com/docs/editing/getting-started/editor-tutorial.md>)

{% /action-card %}

## 先决条件

* [下载并安装{% data variables.product.prodname_vscode %}](<https://github.com/download>)。

* [启用{% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/getstarted/overview.md#enable-ai-features>)中的AI功能

如果您使用 GitHub Copilot，请详细了解 [AI 积分和模型成本 ](<https://github.com/docs/agents/concepts/language-models.md#ai-credits-and-model-costs>) 以及 [ 达到限制时会发生什么情况 ](<https://github.com/docs/agents/agent-troubleshooting/faq.md#i-reached-my-inline-suggestions-or-ai-credits-limit>)。

* [安装Git](<https://git-scm.com/>)

## 1。创建项目文件夹

Agents 在文件夹上下文中工作，在 {% data variables.product.prodname_vscode_shortname %} 中也称为 * 工作空间 *。首先为您的项目创建一个文件夹。首先为教程应用程序创建一个空文件夹，并启用 Git 版本控制来跟踪您的更改。

1. 在您的计算机上，创建一个名为 `myportfolio` 的新文件夹。

    ```bash
    mkdir myportfolio
    ```

1. 将文件夹置于 Git 版本控制之下以跟踪更改。打开终端并运行以下命令：

    ```bash
    cd myportfolio
    git init
    ```

## 2。使用 {% data variables.copilot.agents_window %} 构建功能

{% action-card title="Explore the {% data variables.copilot.agents_window %}" display="sidebar" %}
使用 {% data variables.copilot.agents_window %} 从 {% data variables.product.prodname_vscode_shortname %} 中的一个位置运行和监控项目中的 agent 会话。

* [了解{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>)

{% /action-card %}

{% data variables.copilot.agents_window %} 是 {% data variables.product.prodname_vscode_shortname %} 中的一个专用窗口，针对在所有项目中使用 agents 进行了优化，而无需为每个项目打开单独的 {% data variables.product.prodname_vscode_shortname %} 窗口。

在此部分中，您将打开 {% data variables.copilot.agents_window %} 中的文件夹并任务 agent 来构建您的投资组合页面。

### 打开{% data variables.copilot.agents_window %}

1. 在{% data variables.product.prodname_vscode_shortname %}中，选择{% data variables.product.prodname_vscode_shortname %}标题栏中的**在Agents**中打开按钮。

您还可以从 {% data variables.product.prodname_vscode_shortname %} 欢迎页面打开 {% data variables.copilot.agents_window %}，或运行 **聊天：从命令面板 (`kb(workbench.action.showCommands)`) 打开 {% data variables.copilot.agents_window %}** 命令。

![{% data variables.product.prodname_vscode_shortname %}标题栏中的“在Agents中打开”按钮的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/open-in-agents-button.png>)

1. 如果系统提示您登录，请使用有权访问 {% data variables.product.prodname_copilot %} 的 GitHub 帐户。本教程使用 **Copilot** agent 线束。要将您自己的提供商凭据用于受支持的工作流程，请查看 [agent 线束身份验证选项 ](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>)。

### 开始 agent 会话

1. 选择左侧边栏顶部的**新建**以启动新的agent会话。

![在重新设计的新会话输入中启动新的 agent 会话的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/agent-session-new.png>)

侧边栏显示活动的 agent 会话列表，按工作区分组。您可以使用会话列表在会话及其工作区上下文之间切换。

    > [!TIP]
    > If you want to ask a general question, you can also start a chat session that is not tied to a specific workspace.

1. 由于{% data variables.copilot.agents_window %}可以跨不同项目运行，因此从下拉列表中选择您之前创建的`myportfolio`文件夹。该文件夹成为会话的主要执行工作区。

如果{% data variables.product.prodname_vscode_shortname %}询问您是否信任该文件夹，请选择**信任**。

    > [!IMPORTANT]
    > When you download code from the internet, review the code before trusting it to make sure it's safe to run. Get more info about [Workspace Trust](/docs/editing/workspaces/workspace-trust.md).

1. 为会话配置以下设置。保留任何其他选项的默认值。

    |控制|价值|简短描述|
    |---------|-------|-------------------|
    |**会话目标**| **Copilot** |使用 {% data variables.product.prodname_copilot_short %} agent 线束通过计算机上的 {% data variables.copilot.copilot_sdk %} 运行会话。|
    | **Agent** | **Agent** |使用工具来规划、编辑文件、运行命令并验证结果。|
    |**语言模型**|**自动**|根据任务复杂性和可用性自动选择模型。|
    |**权限**|**手动权限**|请求您批准运行工具或访问资源。 agent可以在您的项目文件夹中进行文件编辑。|

![屏幕截图显示重新设计的新会话输入中的 Copilot 会话目标、Agent 和手动权限。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/agent-session-select-harness-role.png>)

1. 在聊天输入中输入以下提示并按`kbstyle(Enter)`：

    ```prompt
    Create a personal portfolio page with HTML, CSS, and JavaScript in separate files. Include a header with my name and a short bio, a section for projects with cards, and a contact section. Use modern styling and add some sample content.
    ```

1. agent 分析您的请求，规划工作，然后开始创建和编辑文件。如果遇到错误，它会自行更正或要求澄清和批准。

![agent 在 {% data variables.copilot.agents_window %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/agent-generating-files.png>) 中生成投资组合页面文件的屏幕截图

**Copilot** agent 线束应用编辑并将其直接保存到项目文件夹中。在提交更改之前查看生成的差异。

    > [!TIP]
    > If the agent heads in the wrong direction, [steer or stop the request](/docs/chat/chat-overview.md#send-messages-while-a-request-is-running). Stopping doesn't undo completed actions. To undo workspace file changes from a request, [restore a checkpoint](/docs/agents/run/review-code-edits.md#restore-a-checkpoint).

### 预览并迭代设计

{% data variables.copilot.agents_window %} 非常适合将任务交给 agent 然后验证结果的工作流程，而不是具体的代码更改。通过集成浏览器，您无需离开{% data variables.product.prodname_vscode_shortname %}就可以预览agent的作品。

要在集成浏览器中预览生成的投资组合：

1. 选择左侧边栏中的**文件**选项卡，右键单击`index.html`文件并选择**在集成浏览器中打开**。

**文件**选项卡显示工作区中的所有文件，类似于编辑器中的**资源管理器**视图。

![{% data variables.copilot.agents_window %} 中“文件”选项卡的屏幕截图，显示了组合文件和“在集成浏览器中打开”选项。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/open-in-integrated-browser.png>)

1. 集成浏览器在 {% data variables.copilot.agents_window %} 中的新选项卡中打开，您可以像在普通浏览器中一样与页面交互。

![在 {% data variables.copilot.agents_window %} 中的集成浏览器中打开的投资组合页面的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/portfolio-integrated-browser.png>)

1. 让我们对页面进行设计更改。在集成浏览器中，选择**将元素添加到聊天**按钮进入选择模式。

![集成浏览器工具栏的屏幕截图，突出显示“将元素添加到聊天”按钮。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/add-element-to-chat-button.png>)

1. 将鼠标悬停在页面上并选择要更改的元素，例如选择主标题。

agent 将所选元素作为上下文添加到提示中，包括其 HTML、CSS 和屏幕截图。

1. 在聊天输入中，输入描述您想要的更改的提示，然后按 `kbstyle(Enter)`。例如：

    ```prompt
    Use a gradient color for the text and use cursive.
    ```

1. agent 将更改应用于您选择的元素。刷新集成浏览器中的页面即可查看更新。

### 检查并提交更改

在提交 agent 的工作之前，请检查 agent 应用的代码更改。 **更改**面板显示agent在其会话期间创建或修改的每个文件的差异。要查看并提交文件更改：

1. 选择 ** 更改 ** 面板以查看 agent 添加或修改的文件的差异。每个项目还显示更改统计信息和 add/delete/update 指示器。

![{% data variables.copilot.agents_window %} 中“更改”面板的屏幕截图，显示 agent 更改的文件列表。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/changes-panel.png>)

要通过键盘和屏幕阅读器访问差异，请使用[可访问差异查看器](<https://github.com/docs/configure/accessibility/accessibility.md#diff-editor-accessibility>)。

1. 打开 `index.html` 文件中的差异并选择一个文本块以打开内联反馈流。输入您的反馈，然后选择**提交**。

![差异视图中内嵌反馈流程的屏幕截图，显示了选择用于反馈的文本块。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/inline-feedback.png>)

请注意，您的反馈已添加到聊天对话中，agent 会对其进行处理并将更改应用到文件。您可以继续提供有关差异视图中其他更改的反馈。

1. 从更改下拉列表中，选择 **未提交的更改** 以查看尚未提交到 Git 存储库的更改。

使用更改下拉菜单在分支更改、未提交的更改、会话中的所有更改以及上一次 agent 轮中所做的更改之间切换。

![“更改”面板中“未提交的更改”视图的屏幕截图，显示具有未提交更改的文件列表。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/uncommitted-changes.png>)

1. 现在，在 **Changes** 面板中选择 **Commit Changes**，将 agent 的更改保存到您的 Git 存储库。

{% data variables.product.prodname_vscode_shortname %} 根据 agent 的提示及其所做的更改自动创建提交消息。

提交更改后，分支更改和未提交的更改现在为空，因为没有挂起的更改。更改统计信息也会从会话列表中的会话条目中清除。

## 3。 Continue 在编辑器中使用 agents

{% action-card title="Explore the {% data variables.copilot.chat_view %}" display="sidebar" %}
您可以将 {% data variables.copilot.chat_view %} 与编辑器一起使用，让 agents 协助您在活动工作区中完成编码任务。

* [了解{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>)

{% /action-card %}

对于某些更改，您可能更喜欢代码优先的方法，其中您的重点是编写代码，并且 Copilot 在此过程中为您提供帮助。例如，您可能想要添加主题切换器并随时微调样式。对于此方法，请在 {% data variables.copilot.chat_view %} 中继续相同的 Copilot 会话。

### 打开您工作区的编辑器

1. 在{% data variables.copilot.agents_window %}中，选择标题栏中的**在编辑器中打开**按钮以在编辑器中打开活动工作区。

![{% data variables.copilot.agents_window %} 标题栏中“在编辑器中打开”按钮的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/open-in-editor-button.png>)

这将在您的工作区中打开一个新的 {% data variables.product.prodname_vscode_shortname %} 窗口。 {% data variables.copilot.chat_view %} 在右侧边栏中仍然打开，因此您可以在编辑器中工作时与 agents 进行交互。

1. 请注意，左侧边栏显示 **Explorer** 视图，其中显示工作区中的文件。选择一个文件以在主区域的编辑器选项卡中将其打开。

![编辑器的屏幕截图，显示包含组合文件的资源管理器视图以及包含活动 agent 会话的 {% data variables.copilot.chat_view %}。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/explorer-and-chat-view.png>)

右侧边栏中的 {% data variables.copilot.chat_view %} 显示您之前在 {% data variables.copilot.agents_window %} 中创建的正在进行的 agent 会话。

### Continue 来自 {% data variables.copilot.chat_view %} 的会话

{% data variables.copilot.chat_view %} 位于辅助侧栏中，在编辑器选项卡旁边。当您在 {% data variables.copilot.agents_window %} 和编辑器之间移动时，相同的 Copilot 会话保持活动状态。

1. 在聊天输入中输入以下提示并按`kbstyle(Enter)`：

    ```prompt
    Add an accessible theme switcher button that toggles between light and dark color themes. Persist the selected theme across page reloads, update the button label to describe the theme it applies, and keep the layout responsive on narrow screens.
    ```

{% data variables.product.prodname_copilot_short %} 将更改直接应用并保存到您的项目文件中。

1. 打开 ** 源代码管理 ** 视图以查看 Copilot 更改的文件。选择一个文件来检查其差异。

您还可以在{% data variables.copilot.chat_view %}中选择更改的文件来打开其差异。

1. 选择 `index.html` 文件，然后选择标题栏中的 ** 在集成浏览器中打开 **（球形）按钮。

1. 在集成浏览器中，验证更改：

    * 选择主题切换器并验证页面颜色和按钮标签是否发生变化。
    * 将浏览器调整为较窄的宽度，并验证内容是否仍然可读并且项目卡是否适应可用空间。
    * 使用`kbstyle(Tab)`聚焦主题切换器，然后使用`kbstyle(Enter)`或`kbstyle(Space)`切换主题。

1. 如果检查失败，请向 {% data variables.copilot.chat_view %} 中的 Copilot 描述您观察到的情况。例如：

    ```prompt
    The selected theme resets after I refresh the page. Persist the theme selection and verify that it is restored when the page loads.
    ```

1. 查看 **源代码控制** 视图中的最终更改并将其提交到您的 Git 存储库。

恭喜！您通过使用 agent 优先和代码优先方法构建了一个包含 Copilot 的投资组合页面。您在 {% data variables.copilot.agents_window %} 和 {% data variables.copilot.chat_view %} 上继续相同的会话，并使用集成浏览器预览和验证结果。

## 后续步骤

{% action-card title="Use agents in your own project" display="sidebar" %}
将相同的提示、审核和验证工作流程应用于现有项目中的有界任务。

* [将工作流程应用到您的项目](<https://github.com/docs/agents/best-practices.md#apply-the-workflow-to-your-project>)

{% /action-card %}

要深入了解 {% data variables.product.prodname_vscode %} 中的代理编码，请获取有关如何执行以下操作的更多信息：

* [在不更改文件的情况下探索陌生的代码库](<https://github.com/docs/agents/guides/explore-a-codebase.md>)

* [查看建议的安全基线](<https://github.com/docs/agents/run/security.md#recommended-security-baseline>)

* [了解agents如何在{% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/concepts/agents.md>)中工作

* [查找下一个任务的指南](<https://github.com/docs/agents/guides/overview.md#work-on-a-project>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Tutorial: Agentic coding in {% data variables.product.prodname_vscode_shortname %}

In this tutorial, you build a personal portfolio page with AI agents in {% data variables.product.prodname_vscode %}. You describe what you want in natural language, and an agent creates and edits files. You then review and test the result. The app uses HTML, CSS, and JavaScript, so you don't need to install any runtimes or build tools.

You start in the **{% data variables.copilot.agents_window %}** to create the app, then continue the same session in the **{% data variables.copilot.chat_view %}** to refine it alongside your code. Along the way, you learn to open a project folder, preview your app in the integrated browser, and review and commit changes with Git.

{% action-card title="Learn {% data variables.product.prodname_vscode_shortname %} editor features" display="inline" %}
Get familiar with the {% data variables.product.prodname_vscode_shortname %} user interface, editing features, and key productivity tools.

* [Start the {% data variables.product.prodname_vscode_shortname %} editing tutorial](<https://github.com/docs/editing/getting-started/editor-tutorial.md>)

{% /action-card %}

## Prerequisites

* [Download and install {% data variables.product.prodname_vscode %}](<https://github.com/download>).

* [Enable AI features in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/getstarted/overview.md#enable-ai-features>)

    If you use GitHub Copilot, learn more about [AI credits and model costs](<https://github.com/docs/agents/concepts/language-models.md#ai-credits-and-model-costs>) and [what happens when you reach a limit](<https://github.com/docs/agents/agent-troubleshooting/faq.md#i-reached-my-inline-suggestions-or-ai-credits-limit>).

* [Install Git](<https://git-scm.com/>)

## 1. Create a project folder

Agents work in the context of a folder, also referred to as a *workspace* in {% data variables.product.prodname_vscode_shortname %}. You start by creating a folder for your project. Start by creating an empty folder for the tutorial app and enabling Git version control to track your changes.

1. On your computer, create a new folder named `myportfolio`.

    ```bash
    mkdir myportfolio
    ```

1. Put the folder under Git version control to track changes. Open a terminal and run the following commands:

    ```bash
    cd myportfolio
    git init
    ```

## 2. Build features with the {% data variables.copilot.agents_window %}

{% action-card title="Explore the {% data variables.copilot.agents_window %}" display="sidebar" %}
Use the {% data variables.copilot.agents_window %} to run and monitor agent sessions across your projects from a single place in {% data variables.product.prodname_vscode_shortname %}.

* [Learn about the {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>)

{% /action-card %}

The {% data variables.copilot.agents_window %} is a dedicated window in {% data variables.product.prodname_vscode_shortname %} that is optimized for working with agents across all your projects without needing to open a separate {% data variables.product.prodname_vscode_shortname %} window for each one.

In this part, you open your folder in the {% data variables.copilot.agents_window %} and task an agent to build your portfolio page.

### Open the {% data variables.copilot.agents_window %}

1. In {% data variables.product.prodname_vscode_shortname %}, select the **Open in Agents** button in the {% data variables.product.prodname_vscode_shortname %} title bar.

    You can also open the {% data variables.copilot.agents_window %} from the {% data variables.product.prodname_vscode_shortname %} welcome page, or run the **Chat: Open {% data variables.copilot.agents_window %}** command from the Command Palette (`kb(workbench.action.showCommands)`).

    ![Screenshot of the Open in Agents button in the {% data variables.product.prodname_vscode_shortname %} title bar.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/open-in-agents-button.png>)

1. If you're prompted to sign in, use the GitHub account that has access to {% data variables.product.prodname_copilot %}. This tutorial uses the **Copilot** agent harness. To use your own provider credentials for supported workflows, review the [agent harness authentication options](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>).

### Start an agent session

1. Select **New** at the top of the left sidebar to start a new agent session.

    ![Screenshot of starting a new agent session in the redesigned new-session input.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-quickstart/agent-session-new.png>)

    The sidebar shows your list of active agent sessions, grouped by workspace. You can use the sessions list to switch between sessions and their workspace context.

    > [!TIP]
    > If you want to ask a general question, you can also start a chat session that is not tied to a specific workspace.

1. Because the {% data variables.copilot.agents_window %} can operate across different projects, select the `myportfolio` folder you created earlier from the dropdown. This folder becomes the primary execution workspace for the session.

    If {% data variables.product.prodname_vscode_shortname %} asks whether you trust the folder, select **Trust**.

    > [!IMPORTANT]
    > When you download code from the internet, review the code before trusting it to make sure it's safe to run. Get more info about [Workspace Trust](/docs/editing/workspaces/workspace-trust.md).

1. Configure the following settings for the session. Keep the default values for any other options.

    | Control | Value | Short description |
    |---------|-------|-------------------|
    | **Session Target** | **Copilot** | Uses the {% data variables.product.prodname_copilot_short %} agent harness to run the session with the {% data variables.copilot.copilot_sdk %} on your machine. |
    | **Agent** | **Agent** | Uses tools to plan, edit files, run commands, and validate the result. |
    | **Language model** | **Auto** | Automatically selects a model based on task complexity and availability. |
    | **Permissions** | **Manual permissions** | Requests your approval for running tools or accessing resources. The agent can make file edits in your project folder. |

    ![Screenshot showing the Copilot Session Target, Agent, and Manual permissions in the redesigned new-session input.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/agent-session-select-harness-role.png>)

1. Enter the following prompt in the chat input and press `kbstyle(Enter)`:

    ```prompt
    Create a personal portfolio page with HTML, CSS, and JavaScript in separate files. Include a header with my name and a short bio, a section for projects with cards, and a contact section. Use modern styling and add some sample content.
    ```

1. The agent analyzes your request, plans the work, and then starts creating and editing files. If it encounters errors, it self-corrects or asks for clarification and approval.

    ![Screenshot of the agent generating the portfolio page files in the {% data variables.copilot.agents_window %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/agent-generating-files.png>)

    The **Copilot** agent harness applies and saves edits directly to your project folder. Review the resulting diffs before you commit the changes.

    > [!TIP]
    > If the agent heads in the wrong direction, [steer or stop the request](/docs/chat/chat-overview.md#send-messages-while-a-request-is-running). Stopping doesn't undo completed actions. To undo workspace file changes from a request, [restore a checkpoint](/docs/agents/run/review-code-edits.md#restore-a-checkpoint).

### Preview and iterate on the design

The {% data variables.copilot.agents_window %} is great for workflows where you hand off tasks to the agent and then validate the outcome, rather than the specific code changes. With the integrated browser, you can preview the agent's work without having to leave {% data variables.product.prodname_vscode_shortname %}.

To preview the generated portfolio in the integrated browser:

1. Select the **Files** tab in the left sidebar, right-click the `index.html` file and select **Open in Integrated Browser**.

    The **Files** tab shows all files in the workspace, similar to the **Explorer** view in the editor.

    ![Screenshot of the Files tab in the {% data variables.copilot.agents_window %}, showing the portfolio files and the Open in Integrated Browser option.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/open-in-integrated-browser.png>)

1. The integrated browser opens in a new tab in the {% data variables.copilot.agents_window %}, and you can interact with the page as you would in a normal browser.

    ![Screenshot of the portfolio page open in the integrated browser in the {% data variables.copilot.agents_window %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/portfolio-integrated-browser.png>)

1. Let's make a design change to the page. In the integrated browser, select the **Add Element to Chat** button to enter selection mode.

    ![Screenshot of the integrated browser toolbar, highlighting the Add Element to Chat button.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/add-element-to-chat-button.png>)

1. Hover over the page and select an element you want to change, for example select the main title.

    The agent adds the selected element to your prompt as context, including its HTML, CSS, and a screenshot.

1. In the chat input, enter a prompt that describes the change you want, and press `kbstyle(Enter)`. For example:

    ```prompt
    Use a gradient color for the text and use cursive.
    ```

1. The agent applies the change to the element you selected. Refresh the page in the integrated browser to see the updates.

### Review and commit the changes

Before you commit the agent's work, review the code changes that the agent applied. The **Changes** panel shows diffs for every file the agent created or modified during its session. To review and commit the file changes:

1. Select the **Changes** panel to see the diffs of the files the agent added or modified. Each item also shows change stats and an add/delete/update indicator.

    ![Screenshot of the Changes panel in the {% data variables.copilot.agents_window %}, showing the list of files changed by the agent.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/changes-panel.png>)

    For keyboard and screen reader access to the diffs, use the [Accessible Diff Viewer](<https://github.com/docs/configure/accessibility/accessibility.md#diff-editor-accessibility>).

1. Open the diff from the `index.html` file and select a block of text to open the inline feedback flow. Enter your feedback and then select **Submit**.

    ![Screenshot of the inline feedback flow in the diff view, showing a block of text selected for feedback.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/inline-feedback.png>)

    Notice that your feedback is added to the chat conversation, and the agent processes it and applies the change to the file. You can continue to provide feedback on other changes in the diff view.

1. From the changes dropdown, select **Uncommitted Changes** to see the changes that have not yet been committed to your Git repository.

    Use the changes dropdown to switch between branch changes, uncommitted changes, all changes from the session, and changes made during the last agent turn.

    ![Screenshot of the Uncommitted Changes view in the Changes panel, showing the list of files with uncommitted changes.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/uncommitted-changes.png>)

1. Now select **Commit Changes** in the **Changes** panel to save the agent's changes to your Git repository.

    {% data variables.product.prodname_vscode_shortname %} automatically creates a commit message based on the agent's prompt and the changes it made.

    After committing the changes, the branch changes and uncommitted changes are now empty because there are no pending changes. The change stats are also cleared from the session entry in the session list.

## 3. Continue working with agents in the editor

{% action-card title="Explore the {% data variables.copilot.chat_view %}" display="sidebar" %}
You can use the {% data variables.copilot.chat_view %} alongside your editor to let agents assist you with coding tasks in your active workspace.

* [Learn about the {% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>)

{% /action-card %}

For some changes, you might prefer a code-first approach, where your focus is on writing code and Copilot assists you in the process. For example, you might want to add a theme switcher and fine-tune the styles as you go. For this approach, continue the same Copilot session in the {% data variables.copilot.chat_view %}.

### Open the editor for your workspace

1. In the {% data variables.copilot.agents_window %}, select the **Open in Editor** button in the title bar to open the active workspace in the editor.

    ![Screenshot of the Open in Editor button in the {% data variables.copilot.agents_window %} title bar.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/open-in-editor-button.png>)

    This opens a new {% data variables.product.prodname_vscode_shortname %} window with your workspace. The {% data variables.copilot.chat_view %} is still open in the right sidebar, so you can interact with agents while you work in the editor.

1. Notice that the left sidebar shows the **Explorer** view, which displays the files in your workspace. Select a file to open it in an editor tab in the main area.

    ![Screenshot of the editor showing the Explorer view with the portfolio files and the {% data variables.copilot.chat_view %} with the active agent session.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/getting-started/explorer-and-chat-view.png>)

    The {% data variables.copilot.chat_view %} in the right sidebar shows the ongoing agent session you created previously in the {% data variables.copilot.agents_window %}.

### Continue the session from the {% data variables.copilot.chat_view %}

The {% data variables.copilot.chat_view %} is located in the Secondary Side Bar, alongside your editor tabs. The same Copilot session remains active when you move between the {% data variables.copilot.agents_window %} and the editor.

1. Enter the following prompt in the chat input and press `kbstyle(Enter)`:

    ```prompt
    Add an accessible theme switcher button that toggles between light and dark color themes. Persist the selected theme across page reloads, update the button label to describe the theme it applies, and keep the layout responsive on narrow screens.
    ```

    {% data variables.product.prodname_copilot_short %} applies and saves the changes directly to your project files.

1. Open the **Source Control** view to review the files that Copilot changed. Select a file to inspect its diff.

    You can also select a changed file in the {% data variables.copilot.chat_view %} to open its diff.

1. Select the `index.html` file and select the **Open in Integrated Browser** (globe) button in the title bar.

1. In the integrated browser, validate the changes:

    * Select the theme switcher and verify that the page colors and button label change.
    * Resize the browser to a narrow width and verify that the content remains readable and the project cards adapt to the available space.
    * Use `kbstyle(Tab)` to focus the theme switcher, and then use `kbstyle(Enter)` or `kbstyle(Space)` to toggle the theme.

1. If a check fails, describe what you observed to Copilot in the {% data variables.copilot.chat_view %}. For example:

    ```prompt
    The selected theme resets after I refresh the page. Persist the theme selection and verify that it is restored when the page loads.
    ```

1. Review the final changes in the **Source Control** view and commit them to your Git repository.

Congratulations! You built a portfolio page with Copilot by using both an agent-first and code-first approach. You continued the same session across the {% data variables.copilot.agents_window %} and the {% data variables.copilot.chat_view %}, and used the integrated browser to preview and validate the result.

## Next steps

{% action-card title="Use agents in your own project" display="sidebar" %}
Apply the same prompt, review, and validation workflow to a bounded task in an existing project.

* [Apply the workflow to your project](<https://github.com/docs/agents/best-practices.md#apply-the-workflow-to-your-project>)

{% /action-card %}

To go deeper with agentic coding in {% data variables.product.prodname_vscode %}, get more info about how to:

* [Explore an unfamiliar codebase without changing files](<https://github.com/docs/agents/guides/explore-a-codebase.md>)

* [Review the recommended security baseline](<https://github.com/docs/agents/run/security.md#recommended-security-baseline>)

* [Learn how agents work in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/concepts/agents.md>)

* [Find a guide for your next task](<https://github.com/docs/agents/guides/overview.md#work-on-a-project>)


</details>
