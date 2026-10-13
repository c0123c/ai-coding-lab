# 创建 Copilot 自动化任务

介绍按时间或事件触发云端 Agent，涵盖任务创建、仓库选择、测试运行与后续管理，支持重复性的仓库工作。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-06-02](<https://github.com/github/docs/commit/e0679b208251b643fdf7cbcc1c6189caa16016c4>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/create-automations.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/创建 Copilot 自动化任务.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 Copilot 云 agent 创建自动化

创建和管理自动化以按计划或响应事件运行 Copilot 云 agent。

## 简介

通过自动化，您可以将 Copilot 云 agent 设置为按计划或响应存储库中的事件自动运行。自动化可以在配置它们的存储库中采取操作，例如打开拉取请求或标记问题。

您可以从 GitHub 上存储库的 **Agents** 选项卡创建和管理自动化。您还可以从 GitHub Copilot 应用程序中的 **自动化 ** 选项卡创建和管理自动化。

有关自动化的概述，包括触发器、工具、可见性和安全性，请参阅[关于Copilot自动化](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations>)。

## 先决条件

要使自动化在存储库中可用，必须满足以下所有条件：

* 存储库必须是 ** 私有或内部 **。公共存储库中不提供自动化。
* 必须为存储库启用 Copilot 云 agent。如果您拥有 Copilot Business 或 Copilot Enterprise，管理员必须启用 Copilot 云 agent 策略。请参阅[管理对GitHub Copilot云的访问agent](<https://docs.github.com/en/copilot/concepts/enterprise/cloud-agent-access>)。
* 组织必须允许 Copilot 云 agent 和存储库中的自动化（默认情况下均启用）。请参阅 [将 GitHub Copilot 云 agent 添加到您的组织 ](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent>)。

GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Max、GitHub Copilot Business 和 GitHub Copilot Enterprise 计划提供自动化功能。

## 创建自动化

1. 在 GitHub 上，导航到存储库的主页。

2. 在您的存储库名称下，单击 **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-agent" aria-label="agent" role="img"><path d="M14.5 8.9v-.052A2.956 2.956 0 0 0 11.542 5.9a.815.815 0 0 1-.751-.501l-.145-.348A3.496 3.496 0 0 0 7.421 2.9h-.206a3.754 3.754 0 0 0-3.736 4.118l.011.121a.822.822 0 0 1-.619.879A1.81 1.81 0 0 0 1.5 9.773v.14c0 1.097.89 1.987 1.987 1.987H4.5a.75.75 0 0 1 0 1.5H3.487A3.487 3.487 0 0 1 0 9.913v-.14C0 8.449.785 7.274 1.963 6.75A5.253 5.253 0 0 1 7.215 1.4h.206a4.992 4.992 0 0 1 4.586 3.024A4.455 4.455 0 0 1 16 8.848V8.9a.75.75 0 0 1-1.5 0Z"></path><path d="m8.38 7.67 2.25 2.25a.749.749 0 0 1 0 1.061L8.38 13.23a.749.749 0 1 1-1.06-1.06l1.719-1.72L7.32 8.731A.75.75 0 0 1 8.38 7.67ZM15 13.45h-3a.75.75 0 0 1 0-1.5h3a.75.75 0 0 1 0 1.5Z"></path></svg> Agents**。

3. 在侧边栏中，单击 **自动化**。

4. 单击**新建**。

5. 输入自动化的 ** 名称 **。

6. 选择一个或多个 ** 触发器 ** 来确定自动化何时运行：

   * **按计划**：选择重复间隔，每小时、每天或每周。
   * **创建问题时**：每次在存储库中打开问题时都会运行自动化。
   * **打开拉取请求时**：每次在存储库中打开拉取请求时都会运行自动化。
   * **同步拉取请求时**：每次将新提交推送到存储库中的拉取请求时，都会运行自动化。

您可以选择为问题和拉取请求触发器配置过滤器：

   * 对于当**创建问题**时，添加搜索查询过滤器。
   * 对于当 ** 打开拉取请求时 ** 以及当 ** 同步拉取请求时 **，添加搜索查询过滤器和拉取请求中更改的文件的过滤器。

7. 在 ** 提示 ** 字段中，描述您希望 Copilot 每次自动化运行时执行的任务。

例如，`Label this issue as a bug, an enhancement, or other, based on its content.`

   > \[!WARNING]
   > 由自动化启动的 Copilot 云 agent 会话对于有权访问存储库的其他人可见。不要在提示中包含秘密或其他敏感信息。要授予 Copilot 对敏感值的访问权限，请使用存储库机密。请参阅[为Copilot云agent](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables>)配置机密和变量。

8. 或者，选择您想要 Copilot 使用的 ** 型号 **。请参阅[更改GitHub Copilot云agent](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model>)的AI模型。

9. 选择自动化运行时可以使用的 ** 工具 ** Copilot，例如推送更改、更新问题标签或创建拉取请求。

仅选择任务所需的工具。您选择的工具控制 Copilot 可以在您的存储库中执行哪些操作。或者，您可以使用**建议工具**按钮要求Copilot根据您的提示建议工具。请参阅 [关于 Copilot 自动化 ](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations#tools-and-actions>)。

10. 单击 **创建自动化 ** 保存自动化。

## 测试自动化

您可以立即运行自动化，而无需等待其触发器触发，以检查其行为是否符合您的预期。

1. 从 **Automations** 窗格中打开要测试的自动化。
2. 单击**立即运行**按钮。
Copilot 启动 Copilot 云 agent 会话，并使用您选择的工具运行自动化提示。您可以打开会话以跟踪其进度并查看其所做的任何更改。

## 管理您的自动化

您的自动化对您来说是私有的，但从您的自动化启动的会话将对具有存储库读取权限的每个人都可见。

从存储库中的 **Automations** 窗格中，您可以：

* 查看存储库的自动化以及它们已启动的会话。
* 编辑自动化以更改其名称、提示、触发器、工具或模型。
* 禁用自动化以停止其运行，或稍后再次启用。
* 删除您不再需要的自动化。

要查看每个存储库中的所有自动化并导航以管理每个自动化，请使用用户级别的自动化视图。

## 进一步阅读

* [关于Copilot自动化](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations>)
* [正在启动 GitHub Copilot 会话](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions>)
* [GitHub Copilot云agent](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent/risks-and-mitigations>)的风险和缓解措施


## 原文对照

<details>
<summary>展开完整原文</summary>

# Creating automations with Copilot cloud agent

Create and manage automations to run Copilot cloud agent on a schedule or in response to events.

## Introduction

With automations, you can set up Copilot cloud agent to run automatically, either on a schedule or in response to an event in a repository. Automations can take action within the repository where they are configured, such as opening a pull request or labeling an issue.

You can create and manage automations from the **Agents** tab of a repository on GitHub. You can also create and manage automations from the **Automations** tab in the GitHub Copilot app.

For an overview of automations, including triggers, tools, visibility, and security, see [About Copilot automations](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations>).

## Prerequisites

For automations to be available in a repository, all of the following must be true:

* The repository must be **private or internal**. Automations are not available in public repositories.
* Copilot cloud agent must be enabled for the repository. If you have Copilot Business or Copilot Enterprise, an administrator must enable the Copilot cloud agent policy. See [Managing access to GitHub Copilot cloud agent](<https://docs.github.com/en/copilot/concepts/enterprise/cloud-agent-access>).
* The organization must allow both Copilot cloud agent and automations in the repository (both are enabled by default). See [Adding GitHub Copilot cloud agent to your organization](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent>).

Automations are available with the GitHub Copilot Pro, GitHub Copilot Pro+, GitHub Copilot Max, GitHub Copilot Business, and GitHub Copilot Enterprise plans.

## Creating an automation

1. On GitHub, navigate to the main page of the repository.

2. Under your repository name, click **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-agent" aria-label="agent" role="img"><path d="M14.5 8.9v-.052A2.956 2.956 0 0 0 11.542 5.9a.815.815 0 0 1-.751-.501l-.145-.348A3.496 3.496 0 0 0 7.421 2.9h-.206a3.754 3.754 0 0 0-3.736 4.118l.011.121a.822.822 0 0 1-.619.879A1.81 1.81 0 0 0 1.5 9.773v.14c0 1.097.89 1.987 1.987 1.987H4.5a.75.75 0 0 1 0 1.5H3.487A3.487 3.487 0 0 1 0 9.913v-.14C0 8.449.785 7.274 1.963 6.75A5.253 5.253 0 0 1 7.215 1.4h.206a4.992 4.992 0 0 1 4.586 3.024A4.455 4.455 0 0 1 16 8.848V8.9a.75.75 0 0 1-1.5 0Z"></path><path d="m8.38 7.67 2.25 2.25a.749.749 0 0 1 0 1.061L8.38 13.23a.749.749 0 1 1-1.06-1.06l1.719-1.72L7.32 8.731A.75.75 0 0 1 8.38 7.67ZM15 13.45h-3a.75.75 0 0 1 0-1.5h3a.75.75 0 0 1 0 1.5Z"></path></svg> Agents**.

3. In the sidebar, click **Automations**.

4. Click **Create new**.

5. Enter a **name** for the automation.

6. Select one or more **triggers** that determine when the automation runs:

   * **On a schedule**: choose a recurring interval, either hourly, daily, or weekly.
   * **When an issue is created**: the automation runs each time an issue is opened in the repository.
   * **When a pull request is opened**: the automation runs each time a pull request is opened in the repository.
   * **When a pull request is synchronized**: the automation runs each time new commits are pushed to a pull request in the repository.

   You can optionally configure filters for issue and pull request triggers:

   * For when **an issue is created**, add a search query filter.
   * For when **a pull request is opened** and when **a pull request is synchronized**, add a search query filter and a filter for files changed in the pull request.

7. In the **prompt** field, describe the task you want Copilot to perform each time the automation runs.

   For example, `Label this issue as a bug, an enhancement, or other, based on its content.`

   > \[!WARNING]
   > The Copilot cloud agent sessions started by an automation are visible to others with access to the repository. Don't include secrets or other sensitive information in your prompt. To give Copilot access to sensitive values, use repository secrets. See [Configure secrets and variables for Copilot cloud agent](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables>).

8. Optionally, select the **model** you want Copilot to use. See [Changing the AI model for GitHub Copilot cloud agent](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model>).

9. Select the **tools** Copilot can use when the automation runs, such as pushing changes, updating issue labels, or creating a pull request.

   Select only the tools the task requires. The tools you select control what actions Copilot can take in your repository. Optionally, you can use the **Suggest tools** button to ask Copilot to suggest tools based on your prompt. See [About Copilot automations](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations#tools-and-actions>).

10. Save the automation by clicking **Create automation**.

## Testing an automation

You can run an automation immediately, without waiting for its trigger to fire, to check that it behaves as you expect.

1. Open the automation you want to test from the **Automations** pane.
2. Click the **Run now** button.
   Copilot starts a Copilot cloud agent session and runs the automation's prompt with the tools you selected. You can open the session to follow its progress and review any changes it makes.

## Managing your Automations

Your automations are private to you, but the sessions started from your automations will be visible to everyone with read access to the repository.

From the **Automations** pane in a repository, you can:

* View your automations for the repository and the sessions they have started.
* Edit an automation to change its name, prompt, triggers, tools, or model.
* Disable an automation to stop it running, or enable it again later.
* Delete an automation you no longer need.

To see all of your automations across every repository, and navigate to manage each one, use the Automations view at the user level.

## Further reading

* [About Copilot automations](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations>)
* [Starting GitHub Copilot sessions](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions>)
* [Risks and mitigations for GitHub Copilot cloud agent](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent/risks-and-mitigations>)


</details>
