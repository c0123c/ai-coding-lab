# VS Code Agent 编程教程

从创建项目和启动会话到构建功能、浏览器预览、迭代、审查与提交，展示编辑器内的开发流程。

- 来源：Microsoft／VS Code
- 资料类型：存档文档
- 文档时间：官方审核：[2026-10-07](<https://code.visualstudio.com/docs/agents/agents-tutorial>)；官方文件提交（截至抓取）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.visualstudio.com/docs/agents/agents-tutorial>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code Agent 编程教程.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 教程：VS Code 中的代理编码

* [以 Markdown 形式查看](<https://code.visualstudio.com/docs/agents/agents-tutorial.md>)
* [在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fagents-tutorial%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问
* [在VS Code内部人士中提问](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fagents-tutorial%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fagents-tutorial%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问

---

在本教程中，您将在Visual Studio Code中使用AI agents构建个人投资组合页面。您用自然语言描述您想要的内容，然后 agent 创建和编辑文件。然后您查看并测试结果。该应用程序使用 HTML、CSS 和 JavaScript，因此您无需安装任何运行时或构建工具。

您从 **Agents 窗口 ** 开始创建应用程序，然后在 ** 聊天视图 ** 中继续相同的会话，以与代码一起完善它。在此过程中，您将学习如何打开项目文件夹、在集成浏览器中预览应用程序以及使用 Git 检查和提交更改。

了解VS Code编辑器功能

熟悉 VS Code 用户界面、编辑功能和关键生产力工具。

[启动VS Code编辑教程](<https://code.visualstudio.com/docs/editing/getting-started/editor-tutorial>)

## 先决条件

* [下载并安装Visual Studio Code](<https://code.visualstudio.com/download>)。
* [启用VS Code](<https://code.visualstudio.com/docs/getstarted/overview#_enable-ai-features>)中的AI功能

如果您使用 GitHub Copilot，请详细了解 [AI 积分和模型成本 ](<https://code.visualstudio.com/docs/agents/concepts/language-models#_ai-credits-and-model-costs>) 以及 [ 达到限制时会发生什么情况 ](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/faq#_i-reached-my-inline-suggestions-or-ai-credits-limit>)。
* [安装Git](<https://git-scm.com/>)

## 1。创建项目文件夹

Agents 在文件夹上下文中工作，在 VS Code 中也称为 * 工作空间 *。首先为您的项目创建一个文件夹。首先为教程应用程序创建一个空文件夹，并启用 Git 版本控制来跟踪您的更改。

1. 在您的计算机上，创建一个名为 `myportfolio` 的新文件夹。

   ```
   mkdir myportfolio
   ```
2. 将文件夹置于 Git 版本控制之下以跟踪更改。打开终端并运行以下命令：

   ```
   cd myportfolio
   git init
   ```

## 2。使用 Agents 窗口构建功能

Agents 窗口是 VS Code 中的一个专用窗口，它针对在所有项目中使用 agents 进行了优化，而无需为每个项目打开单独的 VS Code 窗口。

在此部分中，您在 Agents 窗口中打开文件夹并任务 agent 来构建您的投资组合页面。

### 打开Agents窗口

1. 在VS Code中，选择VS Code标题栏中的**在Agents**中打开按钮。

您还可以从 VS Code 欢迎页面打开 Agents 窗口，或运行 **Chat：从命令面板打开 Agents 窗口 ** 命令 (⇧⌘P (Windows、Linux Ctrl+Shift+P))。

![VS Code标题栏中的“在Agents中打开”按钮的屏幕截图。](<https://code.visualstudio.com/assets/docs/agents/getting-started/open-in-agents-button.png>)
2. 如果系统提示您登录，请使用有权访问 GitHub Copilot 的 GitHub 帐户。本教程使用 **Copilot** agent 线束。要将您自己的提供商凭据用于受支持的工作流程，请查看 [agent 线束身份验证选项 ](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_configure-a-harness-or-cloud-target>)。

### 开始 agent 会话

1. 选择左侧边栏顶部的**新建**以启动新的agent会话。

![在重新设计的新会话输入中启动新的 agent 会话的屏幕截图。](<https://code.visualstudio.com/assets/docs/agents/agents-quickstart/agent-session-new.png>)

侧边栏显示活动的 agent 会话列表，按工作区分组。您可以使用会话列表在会话及其工作区上下文之间切换。

提示

如果您想询问一般性问题，您还可以启动不与特定工作区绑定的聊天会话。
2. 由于Agents窗口可以跨不同项目操作，因此从下拉列表中选择您之前创建的`myportfolio`文件夹。该文件夹成为会话的主要执行工作区。

如果VS Code询问您是否信任该文件夹，请选择**信任**。

重要

当您从 Internet 下载代码时，请在信任代码之前检查代码，以确保其可以安全运行。获取有关 [Workspace Trust](<https://code.visualstudio.com/docs/editing/workspaces/workspace-trust>) 的更多信息。
3. 为会话配置以下设置。保留任何其他选项的默认值。

   |控制|价值|简短描述|
   | --- | --- | --- |
   |**会话目标**| **Copilot** |使用 Copilot agent 线束与计算机上的 GitHub Copilot SDK 一起运行会话。|
   | **Agent** | **Agent** |使用工具来规划、编辑文件、运行命令并验证结果。|
   |**语言模型**|**自动**|根据任务复杂性和可用性自动选择模型。|
   |**权限**|**手动权限**|请求您批准运行工具或访问资源。 agent可以在您的项目文件夹中进行文件编辑。|

![屏幕截图显示重新设计的新会话输入中的 Copilot 会话目标、Agent 和手动权限。](<https://code.visualstudio.com/assets/docs/agents/getting-started/agent-session-select-harness-role.png>)
4. 在聊天输入中输入以下提示并按 Enter：

   ```
   Create a personal portfolio page with HTML, CSS, and JavaScript in separate files. Include a header with my name and a short bio, a section for projects with cards, and a contact section. Use modern styling and add some sample content.
   ```
5. agent 分析您的请求，规划工作，然后开始创建和编辑文件。如果遇到错误，它会自行更正或要求澄清和批准。

![agent在Agents窗口中生成投资组合页面文件的屏幕截图。](<https://code.visualstudio.com/assets/docs/agents/getting-started/agent-generating-files.png>)

**Copilot** agent 线束应用编辑并将其直接保存到项目文件夹中。在提交更改之前查看生成的差异。

提示

如果agent方向错误，[转向或停止请求](<https://code.visualstudio.com/docs/chat/chat-overview#_send-messages-while-a-request-is-running>)。停止不会撤消已完成的操作。要撤消请求中的工作区文件更改，[恢复检查点](<https://code.visualstudio.com/docs/agents/run/review-code-edits#_restore-a-checkpoint>)。

### 预览并迭代设计

Agents 窗口非常适合将任务交给 agent 的工作流程，然后验证结果，而不是具体的代码更改。通过集成浏览器，您无需离开VS Code就可以预览agent的作品。

要在集成浏览器中预览生成的投资组合：

1. 选择左侧边栏中的**文件**选项卡，右键单击`index.html`文件并选择**在集成浏览器中打开**。

**文件**选项卡显示工作区中的所有文件，类似于编辑器中的**资源管理器**视图。

![Agents 窗口中“文件”选项卡的屏幕截图，显示了组合文件和“在集成浏览器中打开”选项。](<https://code.visualstudio.com/assets/docs/agents/getting-started/open-in-integrated-browser.png>)
2. 集成浏览器会在 Agents 窗口中的新选项卡中打开，您可以像在普通浏览器中一样与页面交互。

![在 Agents 窗口的集成浏览器中打开的投资组合页面的屏幕截图。](<https://code.visualstudio.com/assets/docs/agents/getting-started/portfolio-integrated-browser.png>)
3. 让我们对页面进行设计更改。在集成浏览器中，选择**将元素添加到聊天**按钮进入选择模式。

![集成浏览器工具栏的屏幕截图，突出显示“将元素添加到聊天”按钮。](<https://code.visualstudio.com/assets/docs/agents/getting-started/add-element-to-chat-button.png>)
4. 将鼠标悬停在页面上并选择要更改的元素，例如选择主标题。

agent 将所选元素作为上下文添加到提示中，包括其 HTML、CSS 和屏幕截图。
5. 在聊天输入中，输入描述您想要的更改的提示，然后按 Enter。例如：

   ```
   Use a gradient color for the text and use cursive.
   ```
6. agent 将更改应用于您选择的元素。刷新集成浏览器中的页面即可查看更新。

### 检查并提交更改

在提交 agent 的工作之前，请检查 agent 应用的代码更改。 **更改**面板显示agent在其会话期间创建或修改的每个文件的差异。要查看并提交文件更改：

1. 选择 ** 更改 ** 面板以查看 agent 添加或修改的文件的差异。每个项目还显示更改统计信息和 add/delete/update 指示器。

![Agents 窗口中“更改”面板的屏幕截图，显示 agent 更改的文件列表。](<https://code.visualstudio.com/assets/docs/agents/getting-started/changes-panel.png>)

要通过键盘和屏幕阅读器访问差异，请使用[可访问差异查看器](<https://code.visualstudio.com/docs/configure/accessibility/accessibility#_diff-editor-accessibility>)。
2. 打开 `index.html` 文件中的差异并选择一个文本块以打开内联反馈流。输入您的反馈，然后选择**提交**。

![差异视图中内嵌反馈流程的屏幕截图，显示了选择用于反馈的文本块。](<https://code.visualstudio.com/assets/docs/agents/getting-started/inline-feedback.png>)

请注意，您的反馈已添加到聊天对话中，agent 会对其进行处理并将更改应用到文件。您可以继续提供有关差异视图中其他更改的反馈。
3. 从更改下拉列表中，选择 **未提交的更改** 以查看尚未提交到 Git 存储库的更改。

使用更改下拉菜单在分支更改、未提交的更改、会话中的所有更改以及上一次 agent 轮中所做的更改之间切换。

![“更改”面板中“未提交的更改”视图的屏幕截图，显示具有未提交更改的文件列表。](<https://code.visualstudio.com/assets/docs/agents/getting-started/uncommitted-changes.png>)
4. 现在，在 **Changes** 面板中选择 **Commit Changes**，将 agent 的更改保存到您的 Git 存储库。

VS Code 根据 agent 的提示及其所做的更改自动创建提交消息。

提交更改后，分支更改和未提交的更改现在为空，因为没有挂起的更改。更改统计信息也会从会话列表中的会话条目中清除。

## 3。 Continue 在编辑器中使用 agents

对于某些更改，您可能更喜欢代码优先的方法，其中您的重点是编写代码，并且 Copilot 在此过程中为您提供帮助。例如，您可能想要添加主题切换器并随时微调样式。对于此方法，请在聊天视图中继续相同的 Copilot 会话。

### 打开您工作区的编辑器

1. 在Agents窗口中，选择标题栏中的**在编辑器中打开**按钮以在编辑器中打开活动工作区。

![Agents 窗口标题栏中的“在编辑器中打开”按钮的屏幕截图。](<https://code.visualstudio.com/assets/docs/agents/getting-started/open-in-editor-button.png>)

这将在您的工作区中打开一个新的 VS Code 窗口。右侧边栏中的聊天视图仍然打开，因此您可以在编辑器中工作时与 agents 进行交互。
2. 请注意，左侧边栏显示 **Explorer** 视图，其中显示工作区中的文件。选择一个文件以在主区域的编辑器选项卡中将其打开。

![编辑器的屏幕截图，显示包含项目组合文件的资源管理器视图和包含活动 agent 会话的聊天视图。](<https://code.visualstudio.com/assets/docs/agents/getting-started/explorer-and-chat-view.png>)

右侧边栏中的聊天视图显示您之前在 Agents 窗口中创建的正在进行的 agent 会话。

### Continue 聊天视图中的会话

聊天视图位于辅助侧栏中，位于编辑器选项卡旁边。当您在 Agents 窗口和编辑器之间移动时，相同的 Copilot 会话保持活动状态。

1. 在聊天输入中输入以下提示并按 Enter：

   ```
   Add an accessible theme switcher button that toggles between light and dark color themes. Persist the selected theme across page reloads, update the button label to describe the theme it applies, and keep the layout responsive on narrow screens.
   ```

Copilot 将更改直接应用并保存到您的项目文件中。
2. 打开 ** 源代码管理 ** 视图以查看 Copilot 更改的文件。选择一个文件来检查其差异。

您还可以在聊天视图中选择已更改的文件以打开其差异。
3. 选择 `index.html` 文件，然后选择标题栏中的 ** 在集成浏览器中打开 **（球形）按钮。
4. 在集成浏览器中，验证更改：

   * 选择主题切换器并验证页面颜色和按钮标签是否发生变化。
   * 将浏览器调整为较窄的宽度，并验证内容是否仍然可读并且项目卡是否适应可用空间。
   * 使用 Tab 聚焦主题切换器，然后使用 Enter 或 Space 切换主题。
5. 如果检查失败，请在聊天视图中向 Copilot 描述您观察到的情况。例如：

   ```
   The selected theme resets after I refresh the page. Persist the theme selection and verify that it is restored when the page loads.
   ```
6. 查看 **源代码控制** 视图中的最终更改并将其提交到您的 Git 存储库。

恭喜！您通过使用 agent 优先和代码优先方法构建了一个包含 Copilot 的投资组合页面。您在 Agents 窗口和聊天视图中继续相同的会话，并使用集成浏览器预览和验证结果。

## 后续步骤

要深入了解 Visual Studio Code 中的代理编码，请获取有关如何执行以下操作的更多信息：

* [在不更改文件的情况下探索陌生的代码库](<https://code.visualstudio.com/docs/agents/guides/explore-a-codebase>)
* [查看建议的安全基线](<https://code.visualstudio.com/docs/agents/run/security#_recommended-security-baseline>)
* [了解agents如何在VS Code](<https://code.visualstudio.com/docs/agents/concepts/agents>)中工作
* [查找下一个任务的指南](<https://code.visualstudio.com/docs/agents/guides/overview#_work-on-a-project>)

10/7/2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Tutorial: Agentic coding in VS Code

* [View as Markdown](<https://code.visualstudio.com/docs/agents/agents-tutorial.md>)
* [Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fagents-tutorial%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)
* [Ask in VS Code Insiders](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fagents-tutorial%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagents%2Fagents-tutorial%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

---

In this tutorial, you build a personal portfolio page with AI agents in Visual Studio Code. You describe what you want in natural language, and an agent creates and edits files. You then review and test the result. The app uses HTML, CSS, and JavaScript, so you don't need to install any runtimes or build tools.

You start in the **Agents window** to create the app, then continue the same session in the **Chat view** to refine it alongside your code. Along the way, you learn to open a project folder, preview your app in the integrated browser, and review and commit changes with Git.

Learn VS Code editor features

Get familiar with the VS Code user interface, editing features, and key productivity tools.

[Start the VS Code editing tutorial](<https://code.visualstudio.com/docs/editing/getting-started/editor-tutorial>)

## Prerequisites

* [Download and install Visual Studio Code](<https://code.visualstudio.com/download>).
* [Enable AI features in VS Code](<https://code.visualstudio.com/docs/getstarted/overview#_enable-ai-features>)

  If you use GitHub Copilot, learn more about [AI credits and model costs](<https://code.visualstudio.com/docs/agents/concepts/language-models#_ai-credits-and-model-costs>) and [what happens when you reach a limit](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/faq#_i-reached-my-inline-suggestions-or-ai-credits-limit>).
* [Install Git](<https://git-scm.com/>)

## 1. Create a project folder

Agents work in the context of a folder, also referred to as a *workspace* in VS Code. You start by creating a folder for your project. Start by creating an empty folder for the tutorial app and enabling Git version control to track your changes.

1. On your computer, create a new folder named `myportfolio`.

   ```
   mkdir myportfolio
   ```
2. Put the folder under Git version control to track changes. Open a terminal and run the following commands:

   ```
   cd myportfolio
   git init
   ```

## 2. Build features with the Agents window

The Agents window is a dedicated window in VS Code that is optimized for working with agents across all your projects without needing to open a separate VS Code window for each one.

In this part, you open your folder in the Agents window and task an agent to build your portfolio page.

### Open the Agents window

1. In VS Code, select the **Open in Agents** button in the VS Code title bar.

   You can also open the Agents window from the VS Code welcome page, or run the **Chat: Open Agents window** command from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)).

   ![Screenshot of the Open in Agents button in the VS Code title bar.](<https://code.visualstudio.com/assets/docs/agents/getting-started/open-in-agents-button.png>)
2. If you're prompted to sign in, use the GitHub account that has access to GitHub Copilot. This tutorial uses the **Copilot** agent harness. To use your own provider credentials for supported workflows, review the [agent harness authentication options](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_configure-a-harness-or-cloud-target>).

### Start an agent session

1. Select **New** at the top of the left sidebar to start a new agent session.

   ![Screenshot of starting a new agent session in the redesigned new-session input.](<https://code.visualstudio.com/assets/docs/agents/agents-quickstart/agent-session-new.png>)

   The sidebar shows your list of active agent sessions, grouped by workspace. You can use the sessions list to switch between sessions and their workspace context.

   Tip

   If you want to ask a general question, you can also start a chat session that is not tied to a specific workspace.
2. Because the Agents window can operate across different projects, select the `myportfolio` folder you created earlier from the dropdown. This folder becomes the primary execution workspace for the session.

   If VS Code asks whether you trust the folder, select **Trust**.

   Important

   When you download code from the internet, review the code before trusting it to make sure it's safe to run. Get more info about [Workspace Trust](<https://code.visualstudio.com/docs/editing/workspaces/workspace-trust>).
3. Configure the following settings for the session. Keep the default values for any other options.

   | Control | Value | Short description |
   | --- | --- | --- |
   | **Session Target** | **Copilot** | Uses the Copilot agent harness to run the session with the GitHub Copilot SDK on your machine. |
   | **Agent** | **Agent** | Uses tools to plan, edit files, run commands, and validate the result. |
   | **Language model** | **Auto** | Automatically selects a model based on task complexity and availability. |
   | **Permissions** | **Manual permissions** | Requests your approval for running tools or accessing resources. The agent can make file edits in your project folder. |

   ![Screenshot showing the Copilot Session Target, Agent, and Manual permissions in the redesigned new-session input.](<https://code.visualstudio.com/assets/docs/agents/getting-started/agent-session-select-harness-role.png>)
4. Enter the following prompt in the chat input and press Enter:

   ```
   Create a personal portfolio page with HTML, CSS, and JavaScript in separate files. Include a header with my name and a short bio, a section for projects with cards, and a contact section. Use modern styling and add some sample content.
   ```
5. The agent analyzes your request, plans the work, and then starts creating and editing files. If it encounters errors, it self-corrects or asks for clarification and approval.

   ![Screenshot of the agent generating the portfolio page files in the Agents window.](<https://code.visualstudio.com/assets/docs/agents/getting-started/agent-generating-files.png>)

   The **Copilot** agent harness applies and saves edits directly to your project folder. Review the resulting diffs before you commit the changes.

   Tip

   If the agent heads in the wrong direction, [steer or stop the request](<https://code.visualstudio.com/docs/chat/chat-overview#_send-messages-while-a-request-is-running>). Stopping doesn't undo completed actions. To undo workspace file changes from a request, [restore a checkpoint](<https://code.visualstudio.com/docs/agents/run/review-code-edits#_restore-a-checkpoint>).

### Preview and iterate on the design

The Agents window is great for workflows where you hand off tasks to the agent and then validate the outcome, rather than the specific code changes. With the integrated browser, you can preview the agent's work without having to leave VS Code.

To preview the generated portfolio in the integrated browser:

1. Select the **Files** tab in the left sidebar, right-click the `index.html` file and select **Open in Integrated Browser**.

   The **Files** tab shows all files in the workspace, similar to the **Explorer** view in the editor.

   ![Screenshot of the Files tab in the Agents window, showing the portfolio files and the Open in Integrated Browser option.](<https://code.visualstudio.com/assets/docs/agents/getting-started/open-in-integrated-browser.png>)
2. The integrated browser opens in a new tab in the Agents window, and you can interact with the page as you would in a normal browser.

   ![Screenshot of the portfolio page open in the integrated browser in the Agents window.](<https://code.visualstudio.com/assets/docs/agents/getting-started/portfolio-integrated-browser.png>)
3. Let's make a design change to the page. In the integrated browser, select the **Add Element to Chat** button to enter selection mode.

   ![Screenshot of the integrated browser toolbar, highlighting the Add Element to Chat button.](<https://code.visualstudio.com/assets/docs/agents/getting-started/add-element-to-chat-button.png>)
4. Hover over the page and select an element you want to change, for example select the main title.

   The agent adds the selected element to your prompt as context, including its HTML, CSS, and a screenshot.
5. In the chat input, enter a prompt that describes the change you want, and press Enter. For example:

   ```
   Use a gradient color for the text and use cursive.
   ```
6. The agent applies the change to the element you selected. Refresh the page in the integrated browser to see the updates.

### Review and commit the changes

Before you commit the agent's work, review the code changes that the agent applied. The **Changes** panel shows diffs for every file the agent created or modified during its session. To review and commit the file changes:

1. Select the **Changes** panel to see the diffs of the files the agent added or modified. Each item also shows change stats and an add/delete/update indicator.

   ![Screenshot of the Changes panel in the Agents window, showing the list of files changed by the agent.](<https://code.visualstudio.com/assets/docs/agents/getting-started/changes-panel.png>)

   For keyboard and screen reader access to the diffs, use the [Accessible Diff Viewer](<https://code.visualstudio.com/docs/configure/accessibility/accessibility#_diff-editor-accessibility>).
2. Open the diff from the `index.html` file and select a block of text to open the inline feedback flow. Enter your feedback and then select **Submit**.

   ![Screenshot of the inline feedback flow in the diff view, showing a block of text selected for feedback.](<https://code.visualstudio.com/assets/docs/agents/getting-started/inline-feedback.png>)

   Notice that your feedback is added to the chat conversation, and the agent processes it and applies the change to the file. You can continue to provide feedback on other changes in the diff view.
3. From the changes dropdown, select **Uncommitted Changes** to see the changes that have not yet been committed to your Git repository.

   Use the changes dropdown to switch between branch changes, uncommitted changes, all changes from the session, and changes made during the last agent turn.

   ![Screenshot of the Uncommitted Changes view in the Changes panel, showing the list of files with uncommitted changes.](<https://code.visualstudio.com/assets/docs/agents/getting-started/uncommitted-changes.png>)
4. Now select **Commit Changes** in the **Changes** panel to save the agent's changes to your Git repository.

   VS Code automatically creates a commit message based on the agent's prompt and the changes it made.

   After committing the changes, the branch changes and uncommitted changes are now empty because there are no pending changes. The change stats are also cleared from the session entry in the session list.

## 3. Continue working with agents in the editor

For some changes, you might prefer a code-first approach, where your focus is on writing code and Copilot assists you in the process. For example, you might want to add a theme switcher and fine-tune the styles as you go. For this approach, continue the same Copilot session in the Chat view.

### Open the editor for your workspace

1. In the Agents window, select the **Open in Editor** button in the title bar to open the active workspace in the editor.

   ![Screenshot of the Open in Editor button in the Agents window title bar.](<https://code.visualstudio.com/assets/docs/agents/getting-started/open-in-editor-button.png>)

   This opens a new VS Code window with your workspace. The Chat view is still open in the right sidebar, so you can interact with agents while you work in the editor.
2. Notice that the left sidebar shows the **Explorer** view, which displays the files in your workspace. Select a file to open it in an editor tab in the main area.

   ![Screenshot of the editor showing the Explorer view with the portfolio files and the Chat view with the active agent session.](<https://code.visualstudio.com/assets/docs/agents/getting-started/explorer-and-chat-view.png>)

   The Chat view in the right sidebar shows the ongoing agent session you created previously in the Agents window.

### Continue the session from the Chat view

The Chat view is located in the Secondary Side Bar, alongside your editor tabs. The same Copilot session remains active when you move between the Agents window and the editor.

1. Enter the following prompt in the chat input and press Enter:

   ```
   Add an accessible theme switcher button that toggles between light and dark color themes. Persist the selected theme across page reloads, update the button label to describe the theme it applies, and keep the layout responsive on narrow screens.
   ```

   Copilot applies and saves the changes directly to your project files.
2. Open the **Source Control** view to review the files that Copilot changed. Select a file to inspect its diff.

   You can also select a changed file in the Chat view to open its diff.
3. Select the `index.html` file and select the **Open in Integrated Browser** (globe) button in the title bar.
4. In the integrated browser, validate the changes:

   * Select the theme switcher and verify that the page colors and button label change.
   * Resize the browser to a narrow width and verify that the content remains readable and the project cards adapt to the available space.
   * Use Tab to focus the theme switcher, and then use Enter or Space to toggle the theme.
5. If a check fails, describe what you observed to Copilot in the Chat view. For example:

   ```
   The selected theme resets after I refresh the page. Persist the theme selection and verify that it is restored when the page loads.
   ```
6. Review the final changes in the **Source Control** view and commit them to your Git repository.

Congratulations! You built a portfolio page with Copilot by using both an agent-first and code-first approach. You continued the same session across the Agents window and the Chat view, and used the integrated browser to preview and validate the result.

## Next steps

To go deeper with agentic coding in Visual Studio Code, get more info about how to:

* [Explore an unfamiliar codebase without changing files](<https://code.visualstudio.com/docs/agents/guides/explore-a-codebase>)
* [Review the recommended security baseline](<https://code.visualstudio.com/docs/agents/run/security#_recommended-security-baseline>)
* [Learn how agents work in VS Code](<https://code.visualstudio.com/docs/agents/concepts/agents>)
* [Find a guide for your next task](<https://code.visualstudio.com/docs/agents/guides/overview#_work-on-a-project>)

10/7/2026


</details>
