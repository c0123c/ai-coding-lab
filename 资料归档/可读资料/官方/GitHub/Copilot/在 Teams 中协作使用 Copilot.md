# 在 Teams 中协作使用 Copilot

介绍安装集成、在会话线程中启动和迭代开发任务、创建 Issue、设置默认仓库，以及协作权限和沙箱。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-integrations/integrate-cloud-agent-with-teams.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 Teams 中协作使用 Copilot.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Integrating Copilot cloud agent with Teams
shortTitle: Integrate cloud agent with Teams
allowTitleToDifferFromFilename: true
intro: 'You can use the {% data variables.product.github %} integration in Teams to provide context and open pull requests all from within your Teams channels.'
product: '{% data reusables.copilot.plans.permission-paid-plans-cfi %}'
versions:
  feature: copilot
contentType: how-tos
category:
  - Integrate Copilot with your tools
redirect_from:
  - /copilot/how-tos/use-copilot-agents/cloud-agent/integrate-cloud-agent-with-teams
  - /copilot/how-tos/use-copilot-agents/coding-agent/integrate-coding-agent-with-teams
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [!NOTE]
> * 此功能当前位于 {% data variables.release-phases.public_preview %} 中，可能会发生变化。
> * {% data variables.product.prodname_copilot %} 使用 AI。检查是否有错误。请参阅[AUTOTITLE](<https://github.com/copilot/responsible-use/agents>)。

Microsoft Teams 中的 {% data variables.product.github %} 集成允许您在 Teams 对话中与 {% data variables.copilot.copilot_cloud_agent %} 进行交互。在 Teams 中，您可以使用对话上下文启动 {% data variables.copilot.copilot_cloud_agent_short %} 会话来调查、规划、编写代码以及创建问题和拉取请求。您团队的协作决策与您的代码保持联系，从而弥合了讨论地点和实施地点之间的差距。

有关其他 {% data variables.product.prodname_copilot_short %} 集成的信息，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/tools/about-copilot-integrations>)。

## 安全考虑

在 Teams 中@提及 {% data variables.product.github %} 之前，请考虑 {% data variables.copilot.copilot_cloud_agent %} 将捕获整个线程作为您的请求的上下文，根据讨论理解和实施解决方案。此上下文存储在 agent 生成的工件中。如果您想限制上下文，可以向 Teams 的 {% data variables.product.github %} 应用发送直接消息。

## 了解协作会话、权限和沙箱

{% data variables.product.prodname_copilot_short %} 使用的身份取决于您是在直接消息中还是在共享上下文中与其交互。

* 当您在私信中使用 {% data variables.product.prodname_copilot_short %} 时，它可以为您执行操作，例如创建拉取请求或问题，以及回答问题。它使用您链接的 {% data variables.product.github %} 个人帐户的权限来执行这些操作。

* 当您在共享上下文（例如组线程或通道）中使用 {% data variables.product.prodname_copilot_short %} 时，{% data variables.product.prodname_copilot_short %} 在其应用程序身份而不是您的个人帐户下创建工件，例如拉取请求。

   > [!NOTE]
   > {% data reusables.copilot.cloud-agent.unattributed-additional-approval-note %}

只有对存储库具有 ** 写入 ** 访问权限的用户才能触发 {% data variables.product.prodname_copilot_short %} 进行更改，但任何对话参与者都可以提供输入。工作区的来宾成员和存储库的外部协作者无法在 Teams 中启动或引导 {% data variables.product.prodname_copilot_short %} 的会话。

{% data variables.product.prodname_copilot_short %}使用对话中的所有消息来通知工作。整个线程成为工件的决策上下文。

当您要求 {% data variables.product.prodname_copilot_short %} 执行任务时，它将显示有关会话的详细信息，例如工作存储库、问题或拉取请求链接、使用的模型以及任务状态或摘要。

### 安全的云沙箱

当 {% data variables.copilot.copilot_cloud_agent %} 开始处理 Teams 中的任务时，{% data variables.product.prodname_copilot_short %} 会继续在 ** 安全云沙箱 ** 中异步工作，并在准备就绪时发布结果。

您可以继续从 Teams 进行指导，或者继续在 {% data variables.product.github %}、终端或您首选的代码编辑器中处理 agent 生成的工件。

## 先决条件

* 您必须拥有一个 {% data variables.product.github %} 帐户，可以通过付费 {% data variables.product.prodname_copilot_short %} 计划访问 {% data variables.product.prodname_copilot_short %}。
* 您必须有一个团队帐户。
{% data reusables.copilot.cloud-agent.cloud-sandboxes-prerequisite-teams %}

## 在 Teams 中安装 {% data variables.product.github %} 应用程序

{% data variables.product.github %}应用程序只需在团队中安装一次。安装应用程序后，团队的任何成员都可以将其 {% data variables.product.github %} 帐户连接到该应用程序并开始使用它。

1. 在 Web 浏览器中打开 [{% data variables.product.github %} 集成安装链接 ](<https://teams.microsoft.com/l/app/836ecc9e-6dca-4696-a2e9-15e252cd3f31>) 以启动 Teams 和安装对话框。
1. 单击 ** 添加 ** 将应用程序添加到您的团队。
1. 按照屏幕上的提示对应用程序进行身份验证和授权。

## 将{% data variables.product.github %}应用程序连接到您的{% data variables.product.github %}帐户

首次在 Teams 中使用 {% data variables.product.github %} 应用程序时，需要将其连接到您的 {% data variables.product.github %} 帐户。然后，如果出现提示，请设置默认存储库。

默认存储库提供了 {% data variables.product.prodname_copilot_short %} 在响应提示时使用的上下文，并且它也是打开 {% data variables.copilot.copilot_cloud_agent %} 会话创建的问题和拉取请求的位置，除非您在提示中指定存储库。

开始使用：

1. 在 Teams 中，通过键入 `@{% data variables.product.github %}` 在消息中@提及该应用。
1. 按照提示连接您的 {% data variables.product.github %} 帐户，如果出现提示，可以选择设置默认存储库。
1. 要查看您还可以执行哪些操作，请在线程中输入 `@{% data variables.product.github %} help` @提及该应用程序。

## 从您的团队对话开始 {% data variables.product.prodname_copilot %} 会话

通过键入 `@{% data variables.product.github %}`，然后输入您的任务，@在任何 Teams 聊天中提及该应用程序。您可以为您拥有 `write` 访问权限的任何存储库召唤 agent。 agent 通过计划更改的摘要以及指向其创建的工件的链接进行响应。

例如，要要求 agent 在存储库中的特定分支上创建拉取请求，您可以键入：

```text
@{% data variables.product.github %} Create a pull request to...YOUR_PROMPT repo=OWNER/REPO_NAME branch=BRANCH_NAME
```

`repo` 参数告诉 {% data variables.product.prodname_copilot_short %} 要使用哪个存储库，`branch` 参数指定要用作拉取请求的基础分支的现有分支。

## 迭代线程中的工作

要完善拉取请求，请在与您请求的更改相同的线程中@提及 `@{% data variables.product.github %}`。 {% data variables.product.prodname_copilot_short %} 合并了自上次@提及以来的所有消息以迭代工作，保持讨论和实现的联系。

## 使用 {% data variables.product.prodname_copilot_short %} 创建问题

您可以要求 {% data variables.product.prodname_copilot_short %} 直接从 Teams 创建 {% data variables.product.github %} 问题，将对话转化为可操作的任务。只需用自然语言描述您需要什么，{% data variables.product.prodname_copilot_short %} 就会为您创建问题。

您可以通过子父关系创建一个问题或同时创建多个问题。

当您@提及该应用程序时，它会使用完整的线程历史记录作为其创建的问题的上下文。为了保持上下文的焦点，请考虑启动一个新线程或发送直接消息。

## 在 Teams 中自定义 {% data variables.copilot.copilot_cloud_agent %}

您可以使用 `settings` 参数自定义 {% data variables.copilot.copilot_cloud_agent %} 在您的频道和线程中的工作方式。例如，您可以为频道设置默认存储库。

1. 要查看和更改您的频道设置，请通过键入以下内容在消息中提及该应用：

   ```text
   @{% data variables.product.github %} settings
   ```

1. 然后按照提示进行更改。

### 为频道设置默认存储库

您可以为每个私人或公共频道设置默认存储库。您无法使用 {% data variables.product.prodname_copilot_short %} 设置直接消息的默认存储库。

如果频道没有默认存储库，{% data variables.product.prodname_copilot_short %} 会将您在该频道的第一个会话中使用的存储库设置为该频道的默认存储库。

当您不指定存储库或分支时，{% data variables.product.prodname_copilot_short %} 使用通道的默认存储库和该存储库的默认分支。

1. 在频道中，输入`@{% data variables.product.github %} settings`并发送消息。
1. 按照提示选择频道的默认存储库。

## 反馈和支持

您可以在我们的 [ 论坛 ](<https://github.com/orgs/community/discussions/205453>) 中查看和分享反馈。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/cloud-agent-access>)


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE]
> * This feature is currently in {% data variables.release-phases.public_preview %} and subject to change.
> * {% data variables.product.prodname_copilot %} uses AI. Check for mistakes. See [AUTOTITLE](<https://github.com/copilot/responsible-use/agents>).

The {% data variables.product.github %} integration in Microsoft Teams allows you to interact with {% data variables.copilot.copilot_cloud_agent %} all from within your Teams conversations. Within Teams you can initiate {% data variables.copilot.copilot_cloud_agent_short %} sessions to investigate, plan, write code, and create issues and pull requests, using the context of your conversation. Your team's collaborative decisions stay connected to your code, bridging the gap between where discussions happen and where implementation lives.

For information about additional {% data variables.product.prodname_copilot_short %} integrations, see [AUTOTITLE](<https://github.com/copilot/concepts/tools/about-copilot-integrations>).

## Security considerations

Before you @mention {% data variables.product.github %} in Teams, consider that {% data variables.copilot.copilot_cloud_agent %} will capture the entire thread as context for your request, understanding and implementing solutions based on the discussion. This context is stored in the artifacts the agent generates. If you want to limit the context, you can send a direct message to the {% data variables.product.github %} app for Teams instead.

## Understanding collaborative sessions, permissions, and sandboxes

The identity {% data variables.product.prodname_copilot_short %} uses depends on whether you interact with it in a direct message or a shared context.

* When you use {% data variables.product.prodname_copilot_short %} in a direct message, it can take actions for you, such as creating pull requests or issues, as well as answer questions. It uses the permissions of your linked {% data variables.product.github %} personal account to take these actions.

* When you use {% data variables.product.prodname_copilot_short %} in a shared context, such as a group thread or channel, {% data variables.product.prodname_copilot_short %} creates artifacts, such as pull requests, under its app identity rather than your personal account.

   > [!NOTE]
   > {% data reusables.copilot.cloud-agent.unattributed-additional-approval-note %}

Only users with **write** access to a repository can trigger {% data variables.product.prodname_copilot_short %} to make changes, but any conversation participant can provide input. Guest members of a workspace, and outside collaborators to repositories are not able to start or steer a session with {% data variables.product.prodname_copilot_short %} in Teams.

{% data variables.product.prodname_copilot_short %} uses all messages in the conversation to inform the work. The entire thread becomes the decision-making context for the artifact.

When you ask {% data variables.product.prodname_copilot_short %} to perform a task, it will display details about the session, such as the working repository, issue or pull request link, model used, and a task status or summary.

### Secure cloud sandboxes

When {% data variables.copilot.copilot_cloud_agent %} starts work on a task from Teams, {% data variables.product.prodname_copilot_short %} continues working asynchronously in a **secure cloud sandbox**, and posts the result when it's ready.

You can keep steering from Teams, or continue the work on the agent-generated artifacts in {% data variables.product.github %}, the terminal, or your preferred code editor.

## Prerequisites

* You must have a {% data variables.product.github %} account with access to {% data variables.product.prodname_copilot_short %} through a paid {% data variables.product.prodname_copilot_short %} plan.
* You must have a Teams account.
{% data reusables.copilot.cloud-agent.cloud-sandboxes-prerequisite-teams %}

## Installing the {% data variables.product.github %} app in Teams

The {% data variables.product.github %} app only needs to be installed once in a team. After the app is installed, any member of the team can connect their {% data variables.product.github %} account to the app and start using it.

1. Open the [{% data variables.product.github %} integration installation link](<https://teams.microsoft.com/l/app/836ecc9e-6dca-4696-a2e9-15e252cd3f31>) in your web browser to launch Teams and the installation dialog.
1. Click **Add** to add the app to your team.
1. Follow the prompts on screen to authenticate and authorize the app.

## Connecting the {% data variables.product.github %} app to your {% data variables.product.github %} account

The first time you use the {% data variables.product.github %} app in Teams, you need to connect it to your {% data variables.product.github %} account. Then if prompted, set a default repository.

The default repository provides the context that {% data variables.product.prodname_copilot_short %} uses when responding to prompts, and it's also where issues and pull requests created by {% data variables.copilot.copilot_cloud_agent %} sessions will be opened unless you specify a repository in your prompt.

To get started:

1. In Teams, @mention the app in a message by typing `@{% data variables.product.github %}`.
1. Follow the prompts to connect your {% data variables.product.github %} account, and if prompted, optionally set a default repository.
1. To see what else you can do, in the thread, @mention the app by typing `@{% data variables.product.github %} help`.

## Starting a {% data variables.product.prodname_copilot %} session from your team conversation

@Mention the app in any Teams chat by typing `@{% data variables.product.github %}` followed by your task. You can summon the agent for any repository where you have `write` access. The agent responds with a summary of planned changes and a link to the artifacts it creates.

For example, to ask the agent to create a pull request on a particular branch in a repository, you can type:

```text
@{% data variables.product.github %} Create a pull request to...YOUR_PROMPT repo=OWNER/REPO_NAME branch=BRANCH_NAME
```

The `repo` parameter tells {% data variables.product.prodname_copilot_short %} which repository to use, and the `branch` parameter specifies an existing branch to use as the base branch for a pull request.

## Iterating on work in the thread

To refine the pull request, @mention `@{% data variables.product.github %}` in the same thread with your requested changes. {% data variables.product.prodname_copilot_short %} incorporates all messages since the previous @mention to iterate on the work, keeping the discussion and implementation connected.

## Creating issues with {% data variables.product.prodname_copilot_short %}

You can ask {% data variables.product.prodname_copilot_short %} to create {% data variables.product.github %} issues directly from Teams, turning conversations into actionable tasks. Just describe what you need in natural language, and {% data variables.product.prodname_copilot_short %} creates the issue for you.

You can create a single issue or multiple issues at once with child-parent relationships.

When you @mention the app, it uses the full thread history as context for the issues it creates. To keep the context focused, consider starting a new thread or sending a direct message.

## Customizing {% data variables.copilot.copilot_cloud_agent %} in Teams

You can customize how {% data variables.copilot.copilot_cloud_agent %} works in your channels and threads using the `settings` parameter. For example, you can set a default repository for a channel.

1. To see and change your channel settings, mention the app in a message by typing:

   ```text
   @{% data variables.product.github %} settings
   ```

1. Then follow the prompts to make your changes.

### Setting a default repository for a channel

You can set a default repository for each private or public channel. You cannot set a default repository for direct messages with {% data variables.product.prodname_copilot_short %}.

If a channel does not have a default repository, {% data variables.product.prodname_copilot_short %} sets the repository you use in your first session in that channel as the channel's default repository.

When you do not specify a repository or branch, {% data variables.product.prodname_copilot_short %} uses the channel's default repository and that repository's default branch.

1. In the channel, type `@{% data variables.product.github %} settings` and send the message.
1. Follow the prompts to select a default repository for the channel.

## Feedback and support

You can view and share feedback in our [discussion forum](<https://github.com/orgs/community/discussions/205453>).

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/cloud-agent-access>)


</details>
