# 用云端 Agent 研究、规划并迭代代码

说明在创建拉取请求前深入研究仓库、制定实现计划并在分支上持续修改，配合视觉上下文和用户反馈推进任务。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/research-plan-iterate>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/用云端 Agent 研究、规划并迭代代码.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 Copilot 云 agent 研究、规划和迭代代码更改

对存储库进行深入研究，创建实施计划，并在分支上进行迭代代码更改，然后在准备好时创建拉取请求。

Copilot 云 agent 让您：

* **通过询问 Copilot 问题来研究 ** 存储库。
* **在Copilot进行更改之前计划**并完善方法。
* **迭代**代码更改，检查差异，并**准备好后创建拉取请求**。

会话不会自动创建拉取请求。要立即创建一个，请将其包含在提示中 - 例如，“创建一个拉取请求到...”。

> \[!NOTE] 这些功能仅适用于 GitHub.com 上的 Copilot 云 agent，并且在 Teams 和 Slack 集成的公共预览版中可用。其他云 agent 集成（例如 Azure Boards、JIRA 或 Linear）仅支持直接创建拉取请求。

## 进行深入研究

询问 Copilot 云 agent 有关存储库的问题，以了解其工作原理、找到需要更改的位置或在规划之前确认假设。

1. 从 agents 选项卡、面板、仪表板、Copilot 聊天或从支持的集成中启动任务。请参阅 [ 在 GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/kick-off-a-task>) 上使用 Copilot agents 启动任务。
2. 问一个关于存储库的问题。

例如：`Investigate performance issues in this app and suggest improvements.`

   > \[!TIP] 您还可以打开 Copilot 聊天并附加相关存储库作为上下文。 Copilot 提示您批准深度研究课程。
3. （可选）在 Copilot 正在努力引导会话时发送后续提示。
4. 查看回复并提出后续问题。

## 制定计划

在进行更改之前请Copilot云agent提出计划。

1. 描述您想要的计划。

例如：`Create a plan to implement the most impactful performance improvements for this app.`
2. 检查计划并使用 Copilot 进行迭代，直到它符合您的意图。

## 迭代代码更改

经过研究和规划后，对分支进行更改，检查差异，并决定是否进行更多迭代或创建拉取请求。

1. 描述您想要在提示中进行的更改。

例如，参考之前的计划：`Implement the plan we agreed upon.`
2. 等待 Copilot 完成会话。
3. 单击 **Diff** 查看更改。
4. 或者，打开分支 (`copilot/BRANCH-NAME`) 以检查上下文中的更改。
5. 要请求改进，请继续对话。例如：`Rename this to match our existing conventions.`
6. 会话完成并且您感到满意后，请单击 **创建拉取请求 **。

## 提供视觉背景

Cloud agent 处理图像（例如屏幕截图、草图或设计）作为任务描述的一部分。将图像拖放到问题正文或 Copilot 聊天提示中，然后描述 Copilot 应该如何处理它。

## 进一步阅读

* [GitHub Copilot 在 GitHub.com](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-on-github>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Research, plan, and iterate on code changes with Copilot cloud agent

Perform deep research on a repository, create an implementation plan, and make iterative code changes on a branch, then create a pull request when you're ready.

Copilot cloud agent lets you:

* **Research** a repository by asking Copilot questions.
* **Plan** and refine an approach before Copilot makes changes.
* **Iterate** on code changes, review a diff, and **create a pull request when you're ready**.

Sessions do not create pull requests automatically. To create one immediately, include that in your prompt—for example, "Create a pull request to ...".

> \[!NOTE] These capabilities are only available with Copilot cloud agent on GitHub.com, and in public preview for the Teams and Slack integrations. Other Cloud agent integrations (such as Azure Boards, JIRA, or Linear) only support creating a pull request directly.

## Perform deep research

Ask Copilot cloud agent questions about a repository to understand how it works, find where to make a change, or confirm assumptions before planning.

1. Start a task from the agents tab, panel, dashboard, Copilot Chat, or from within a supported integration. See [Kick off a task with Copilot agents on GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/kick-off-a-task>).
2. Ask a question about the repository.

   For example: `Investigate performance issues in this app and suggest improvements.`

   > \[!TIP] You can also open Copilot Chat and attach the relevant repository as context. Copilot prompts you to approve a deep research session.
3. Optionally, send follow-up prompts while Copilot is working to steer the session.
4. Review the response and ask follow-up questions.

## Create a plan

Ask Copilot cloud agent to propose a plan before making changes.

1. Describe the plan you want.

   For example: `Create a plan to implement the most impactful performance improvements for this app.`
2. Review the plan and iterate with Copilot until it matches your intent.

## Iterate on code changes

After research and planning, make changes on a branch, review the diff, and decide whether to iterate more or create a pull request.

1. Describe the changes you want in your prompt.

   For example, reference the plan from earlier: `Implement the plan we agreed upon.`
2. Wait for Copilot to finish the session.
3. Click **Diff** to review the changes.
4. Optionally, open the branch (`copilot/BRANCH-NAME`) to inspect the changes in context.
5. To request refinements, continue the conversation. For example: `Rename this to match our existing conventions.`
6. When the session is complete and you're satisfied, click **Create pull request**.

## Provide visual context

Cloud agent processes images—such as screenshots, sketches, or designs—as part of your task description. Drag and drop an image into an issue body or a Copilot Chat prompt, then describe what Copilot should do with it.

## Further reading

* [GitHub Copilot on GitHub.com](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-on-github>)


</details>
