# 在 Slack 中协作使用 Copilot

介绍连接账号、提供开发上下文、启动云端任务和创建 Issue，并说明协作会话、权限及沙箱边界。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-integrations/integrate-cloud-agent-with-slack.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 Slack 中协作使用 Copilot.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Integrating Copilot cloud agent with Slack
shortTitle: Integrate cloud agent with Slack
allowTitleToDifferFromFilename: true
intro: 'Provide context to the {% data variables.product.prodname_copilot_short %} cloud agent and open pull requests, all from within your Slack workspace.'
product: '{% data reusables.copilot.plans.permission-paid-plans-cfi %}'
versions:
  feature: copilot
contentType: how-tos
category:
  - Integrate Copilot with your tools
redirect_from:
  - /copilot/how-tos/use-copilot-agents/cloud-agent/integrate-cloud-agent-with-slack
  - /copilot/how-tos/use-copilot-agents/coding-agent/integrate-coding-agent-with-slack
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [!NOTE]
> * 此功能位于 {% data variables.release-phases.public_preview %} 中，可能会发生变化。
> * {% data variables.product.prodname_copilot %} 使用 AI。检查是否有错误。请参阅[AUTOTITLE](<https://github.com/copilot/responsible-use/agents>)。

## 简介

Slack 中的 {% data variables.copilot.copilot_cloud_agent %} 集成允许您从 Slack 工作区与 {% data variables.copilot.copilot_cloud_agent %} 进行交互，并且包含在 Slack 的 {% data variables.product.github %} 应用程序中。在 Slack 消息、话题或直接消息中，您可以使用对话上下文启动 {% data variables.copilot.copilot_cloud_agent_short %} 会话来调查、计划、编写代码以及创建问题和拉取请求。您团队的协作决策与您的代码保持联系，从而弥合了讨论地点和实施地点之间的差距。

有关其他 {% data variables.product.prodname_copilot_short %} 集成的信息，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/tools/about-copilot-integrations>)。

## 安全考虑

在 Slack 中@提及 {% data variables.product.github %} 之前，请考虑 {% data variables.copilot.copilot_cloud_agent %} 将捕获整个线程作为您的请求的上下文，根据讨论理解和实施解决方案。此上下文存储在 agent 生成的工件中。如果您想限制上下文，您可以向 Slack 的 {% data variables.product.github %} 应用程序发送直接消息。

## 了解协作会话、权限、代码通道和沙箱

{% data variables.product.prodname_copilot_short %} 使用的身份取决于您是在直接消息中还是在共享上下文中与其交互。

* 当您在私信中使用 {% data variables.product.prodname_copilot_short %} 时，它可以为您执行操作，例如创建拉取请求或问题，以及回答问题。它使用您链接的 {% data variables.product.github %} 个人帐户的权限来执行这些操作。

* 当您在共享上下文（例如组线程或通道）中使用 {% data variables.product.prodname_copilot_short %} 时，{% data variables.product.prodname_copilot_short %} 在其应用程序身份而不是您的个人帐户下创建工件，例如拉取请求。

   > [!NOTE]
   > {% data reusables.copilot.cloud-agent.unattributed-additional-approval-note %}

只有对存储库具有 ** 写入 ** 访问权限的用户才能触发 {% data variables.product.prodname_copilot_short %} 进行更改，但任何对话参与者都可以提供输入。工作区的来宾成员和存储库的外部协作者无法在 Slack 中启动或引导 {% data variables.product.prodname_copilot_short %} 会话。

{% data variables.product.prodname_copilot_short %}使用对话中的所有消息来通知工作。整个线程成为工件的决策上下文。

### 松弛代码

当你要求{% data variables.product.prodname_copilot_short %}执行任务时，{% data variables.copilot.copilot_cloud_agent %}将创建一个专用的代码通道，称为**Slack Code**。您和您的队友（可选）可以在此处与 {% data variables.product.prodname_copilot_short %} 协作完成任务。建立代码通道后，仅通过该通道引导会话。

{% data variables.product.prodname_copilot_short %} 管理代码通道并显示有关会话的详细信息，例如工作存储库、分支、问题或拉取请求链接、状态和正在使用的模型。代码通道一次用于一个会话：每个任务一个通道。会话结束后，系统会询问您是否要存档频道。存档后，频道及其历史记录仍然可见和可搜索，并且您可以根据需要重新打开频道。

### 安全的云沙箱

当 {% data variables.copilot.copilot_cloud_agent %} 开始处理 Slack 中的任务时，{% data variables.product.prodname_copilot_short %} 会继续在 ** 安全云沙箱 ** 中异步工作，并在准备就绪时发布结果。您可以继续从 Slack 进行操作，或者继续在 {% data variables.product.github %}、终端或您首选的代码编辑器中处理 agent 生成的工件。

## 先决条件

* 您必须拥有一个 {% data variables.product.github %} 帐户，可以通过付费 {% data variables.product.prodname_copilot_short %} 计划访问 {% data variables.product.prodname_copilot_short %}。
* 您必须拥有 Slack 帐户并且是工作区的成员。
* 您必须安装 Slack 的 {% data variables.product.github %} 集成。参见[AUTOTITLE](<https://github.com/integrations/how-tos/slack/integrate-github-with-slack>)。
{% data reusables.copilot.cloud-agent.cloud-sandboxes-prerequisite-slack %}

## 将{% data variables.product.github %}应用程序连接到您的{% data variables.product.github %}帐户

第一次在 Slack 中使用 {% data variables.product.github %} 集成时，应用程序会提示您将其连接到您的 {% data variables.product.github %} 帐户。然后，如果出现提示，请设置默认存储库。

默认存储库提供了 {% data variables.product.prodname_copilot_short %} 在响应提示时使用的上下文，并且它也是打开 {% data variables.copilot.copilot_cloud_agent %} 会话创建的问题和拉取请求的位置，除非您在提示中指定存储库。

开始使用：

1. 在 Slack 中，使用 {% data variables.product.github %} 应用打开直接消息，或通过输入 `@{% data variables.product.github %}` 在线程中@提及 {% data variables.product.github %} 应用。
1. 按照提示连接您的 {% data variables.product.github %} 帐户，如果出现提示，可以选择设置默认存储库。
1. 要查看您还可以执行哪些操作，请在线程中输入 `@{% data variables.product.github %} help` @提及该应用程序。

## 在 Slack 中使用 {% data variables.product.github %} 应用程序

您可以发送 {% data variables.product.github %} 应用程序直接消息或在线程中@提及它。机器人将响应您的消息并根据您的请求执行任务。

您必须对默认存储库或提示中指定的存储库具有写入权限，才能触发 {% data variables.copilot.copilot_cloud_agent %} 工作。如果您没有相关存储库的写入权限，您仍然可以通过在 Slack 线程中提供输入来帮助指导 {% data variables.product.prodname_copilot_short %}，当 {% data variables.copilot.copilot_cloud_agent %} 在拉取请求中进行更改时，该输入将用作上下文。

用户可以在拥有 `write` 访问权限的任何存储库上调用 {% data variables.copilot.copilot_cloud_agent %}。对于企业拥有的存储库，管理员必须安装和配置 [Slack {% data variables.product.github %} 应用程序 ](<https://github.com/marketplace/slack-github?ref_product=copilot&ref_type=engagement&ref_style=text&ref_plan=enterprise>) 并指定 Slack 应用程序可以访问哪些存储库。有关配置 {% data variables.product.prodname_github_apps %} 的更多信息，请参阅 [AUTOTITLE](<https://github.com/apps/using-github-apps/installing-a-github-app-from-github-marketplace-for-your-organizations>)。

1. 在 Slack 中，使用 {% data variables.product.github %} 应用程序打开直接消息，或通过输入 `@{% data variables.product.github %}` 在线程中@提及该应用程序。
1. 输入您的提示，然后发送。作为请求的一部分，您可以使用自然语言描述存储库和分支。例如：

    `@{% data variables.product.github %} Add "Hello World" to the README in octo-org/octo-repo on the develop branch`

1. {% data variables.copilot.copilot_cloud_agent %} 将启动 {% data variables.copilot.copilot_cloud_agent_short %} 会话，并且在 {% data variables.copilot.copilot_cloud_agent_short %} 完成后，响应它计划进行的更改的摘要以及它在指定存储库中创建的工件的链接。

### 使用 {% data variables.product.prodname_copilot_short %} 创建问题

您可以要求 {% data variables.product.prodname_copilot_short %} 直接从 Slack 创建 {% data variables.product.github %} 问题，将对话转化为可操作的任务。只需用自然语言描述您需要什么，{% data variables.product.prodname_copilot_short %} 就会为您创建问题。

您可以通过子父关系创建一个问题或同时创建多个问题。

当您@提及该应用程序时，它会使用完整的线程历史记录作为其创建的问题的上下文。为了保持上下文的焦点，请考虑启动一个新线程或发送直接消息。

1. 在 Slack 中，要求 {% data variables.product.prodname_copilot_short %} 创建一个或多个问题，并指定目标存储库。

要创建单个问题：

   ```text
   @{% data variables.product.github %} In octo-org/octo-repo, create a feature request to add fuzzy matching to search.
   ```

要一次创建多个问题：

   ```text
   @{% data variables.product.github %} In octo-org/octo-repo, open separate issues for adding fuzzy matching to search, paginating the results list, and caching search queries.
   ```

造成子女与父母关系的问题：

   ```text
   @{% data variables.product.github %} In octo-org/octo-repo, create an epic to redesign search, with child issues for fuzzy matching, pagination, and query caching.
   ```

   > [!NOTE] 您只能使用 {% data variables.product.prodname_copilot_short %} 在您已拥有创建问题权限的存储库中创建问题。此功能不会更改您的访问权限或绕过存储库权限。

1. {% data variables.product.prodname_copilot_short %} 创建问题并回复每个问题的链接。每个问题都包含标题和描述，并且根据您的提示 {% data variables.product.prodname_copilot_short %} 还可以添加元数据，例如标签、受让人和问题类型。

### 为频道设置默认存储库

您可以为每个私人或公共频道设置默认存储库。您无法使用 {% data variables.product.prodname_copilot_short %} 设置直接消息的默认存储库。

如果频道没有默认存储库，{% data variables.product.prodname_copilot_short %} 会将您在该频道的第一个会话中使用的存储库设置为该频道的默认存储库。

1. 在频道中，输入`@{% data variables.product.github %} settings`并发送消息。
1. 选择要用作默认存储库的存储库，然后保存更改。

当您不指定存储库或分支时，{% data variables.product.prodname_copilot_short %} 使用通道的默认存储库和该存储库的默认分支。

> [!NOTE] 默认存储库在整个通道中共享，因此任何更改都适用于在该通道中使用 {% data variables.product.prodname_copilot_short %} 的每个人。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>) - 了解有关 {% data variables.copilot.copilot_cloud_agent %} 及其如何为您提供支持的更多信息。


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE]
> * This feature is in {% data variables.release-phases.public_preview %} and subject to change.
> * {% data variables.product.prodname_copilot %} uses AI. Check for mistakes. See [AUTOTITLE](<https://github.com/copilot/responsible-use/agents>).

## Introduction

The {% data variables.copilot.copilot_cloud_agent %} integration in Slack allows you to interact with {% data variables.copilot.copilot_cloud_agent %} from your Slack workspace and is included in the {% data variables.product.github %} app for Slack. Within a Slack message, thread, or direct message, you can initiate {% data variables.copilot.copilot_cloud_agent_short %} sessions to investigate, plan, write code, and create issues and pull requests, using the context of your conversation. Your team's collaborative decisions stay connected to your code, bridging the gap between where discussions happen and where implementation lives.

For information about additional {% data variables.product.prodname_copilot_short %} integrations, see [AUTOTITLE](<https://github.com/copilot/concepts/tools/about-copilot-integrations>).

## Security considerations

Before you @mention {% data variables.product.github %} in Slack, consider that {% data variables.copilot.copilot_cloud_agent %} will capture the entire thread as context for your request, understanding and implementing solutions based on the discussion. This context is stored in the artifacts the agent generates. If you want to limit the context, you can send a direct message to the {% data variables.product.github %} app for Slack instead.

## Understanding collaborative sessions, permissions, code channels and sandboxes

The identity {% data variables.product.prodname_copilot_short %} uses depends on whether you interact with it in a direct message or a shared context.

* When you use {% data variables.product.prodname_copilot_short %} in a direct message, it can take actions for you, such as creating pull requests or issues, as well as answer questions. It uses the permissions of your linked {% data variables.product.github %} personal account to take these actions.

* When you use {% data variables.product.prodname_copilot_short %} in a shared context, such as a group thread or channel, {% data variables.product.prodname_copilot_short %} creates artifacts, such as pull requests, under its app identity rather than your personal account.

   > [!NOTE]
   > {% data reusables.copilot.cloud-agent.unattributed-additional-approval-note %}

Only users with **write** access to a repository can trigger {% data variables.product.prodname_copilot_short %} to make changes, but any conversation participant can provide input. Guest members of a workspace, and outside collaborators to repositories are not able to start or steer a session with {% data variables.product.prodname_copilot_short %} in Slack.

{% data variables.product.prodname_copilot_short %} uses all messages in the conversation to inform the work. The entire thread becomes the decision-making context for the artifact.

### Slack Code

When you ask {% data variables.product.prodname_copilot_short %} to perform a task, {% data variables.copilot.copilot_cloud_agent %} will create a dedicated code channel, called **Slack Code**. This is where you, and optionally your teammates, can collaborate with {% data variables.product.prodname_copilot_short %} on a task. Once a code channel is established, steer the session exclusively through that channel.

{% data variables.product.prodname_copilot_short %} manages the code channel and displays details about the session, such as the working repository, branch, issue or pull request link, status, and model in use. Code channels are intended for one session at a time: one channel per task. When the session is finished, you are asked whether you want to archive the channel. After archiving, the channel and its history remain viewable and searchable, and you can reopen the channel if needed.

### Secure cloud sandboxes

When {% data variables.copilot.copilot_cloud_agent %} starts work on a task from Slack, {% data variables.product.prodname_copilot_short %} continues working asynchronously in a **secure cloud sandbox**, and posts the result when it's ready. You can keep steering from Slack or continue the work on the agent-generated artifacts in {% data variables.product.github %}, the terminal, or your preferred code editor.

## Prerequisites

* You must have a {% data variables.product.github %} account with access to {% data variables.product.prodname_copilot_short %} through a paid {% data variables.product.prodname_copilot_short %} plan.
* You must have a Slack account and be a member of a workspace.
* You must have the {% data variables.product.github %} integration for Slack installed. See [AUTOTITLE](<https://github.com/integrations/how-tos/slack/integrate-github-with-slack>).
{% data reusables.copilot.cloud-agent.cloud-sandboxes-prerequisite-slack %}

## Connecting the {% data variables.product.github %} app to your {% data variables.product.github %} account

The first time you use the {% data variables.product.github %} integration in Slack, the app will prompt you to connect it to your {% data variables.product.github %} account. Then if prompted, set a default repository.

The default repository provides the context that {% data variables.product.prodname_copilot_short %} uses when responding to prompts, and it's also where issues and pull requests created by {% data variables.copilot.copilot_cloud_agent %} sessions will be opened unless you specify a repository in your prompt.

To get started:

1. In Slack, open a direct message with the {% data variables.product.github %} app or @mention the {% data variables.product.github %} app in a thread by typing `@{% data variables.product.github %}`.
1. Follow the prompts to connect your {% data variables.product.github %} account, and if prompted, optionally set a default repository.
1. To see what else you can do, in the thread, @mention the app by typing `@{% data variables.product.github %} help`.

## Using the {% data variables.product.github %} app in Slack

You can send the {% data variables.product.github %} app direct messages or @mention it in a thread. The bot will respond to your messages and perform tasks based on your requests.

You must have write access to the default repository, or the repository specified in your prompt, in order to trigger {% data variables.copilot.copilot_cloud_agent %} to work. If you do not have write access to the relevant repository, you can still help guide {% data variables.product.prodname_copilot_short %} by providing input in the Slack thread, which will be used as context when {% data variables.copilot.copilot_cloud_agent %} makes changes in the pull request.

Users can invoke {% data variables.copilot.copilot_cloud_agent %} on any repository where they have `write` access. For enterprise-owned repositories, administrators must install and configure the [Slack {% data variables.product.github %} app](<https://github.com/marketplace/slack-github?ref_product=copilot&ref_type=engagement&ref_style=text&ref_plan=enterprise>) and specify which repositories the Slack app can access. For more information about configuring {% data variables.product.prodname_github_apps %}, see [AUTOTITLE](<https://github.com/apps/using-github-apps/installing-a-github-app-from-github-marketplace-for-your-organizations>).

1. In Slack, open a direct message with the {% data variables.product.github %} app or @mention the app in a thread by typing `@{% data variables.product.github %}`.
1. Type your prompt, then send it. You can describe the repository and branch in natural language as part of your request. For example:

    `@{% data variables.product.github %} Add "Hello World" to the README in octo-org/octo-repo on the develop branch`

1. {% data variables.copilot.copilot_cloud_agent %} will initiate a {% data variables.copilot.copilot_cloud_agent_short %} session and, once the {% data variables.copilot.copilot_cloud_agent_short %} has finished, respond with a summary of the changes it plans to make and a link to the artifacts it has created in the specified repository.

### Creating issues with {% data variables.product.prodname_copilot_short %}

You can ask {% data variables.product.prodname_copilot_short %} to create {% data variables.product.github %} issues directly from Slack, turning conversations into actionable tasks. Just describe what you need in natural language, and {% data variables.product.prodname_copilot_short %} creates the issue for you.

You can create a single issue or multiple issues at once with child-parent relationships.

When you @mention the app, it uses the full thread history as context for the issues it creates. To keep the context focused, consider starting a new thread or sending a direct message.

1. In Slack, ask {% data variables.product.prodname_copilot_short %} to create one or more issues, specifying the target repository.

   To create a single issue:

   ```text
   @{% data variables.product.github %} In octo-org/octo-repo, create a feature request to add fuzzy matching to search.
   ```

   To create multiple issues at once:

   ```text
   @{% data variables.product.github %} In octo-org/octo-repo, open separate issues for adding fuzzy matching to search, paginating the results list, and caching search queries.
   ```

   To create issues with child-parent relationships:

   ```text
   @{% data variables.product.github %} In octo-org/octo-repo, create an epic to redesign search, with child issues for fuzzy matching, pagination, and query caching.
   ```

   > [!NOTE] You can only use {% data variables.product.prodname_copilot_short %} to create issues in repositories where you already have permission to create issues. This feature doesn't change your access or bypass repository permissions.

1. {% data variables.product.prodname_copilot_short %} creates the issues and replies with a link to each one. Each issue includes a title and description, and based on your prompt {% data variables.product.prodname_copilot_short %} can also add metadata such as labels, assignees, and issue type.

### Setting a default repository for a channel

You can set a default repository for each private or public channel. You cannot set a default repository for direct messages with {% data variables.product.prodname_copilot_short %}.

If a channel does not have a default repository, {% data variables.product.prodname_copilot_short %} sets the repository you use in your first session in that channel as the channel's default repository.

1. In the channel, type `@{% data variables.product.github %} settings` and send the message.
1. Select the repository you want to use as the default, then save your changes.

When you do not specify a repository or branch, {% data variables.product.prodname_copilot_short %} uses the channel's default repository and that repository's default branch.

> [!NOTE] The default repository is shared across the channel, so any change applies to everyone using {% data variables.product.prodname_copilot_short %} in that channel.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>) - Learn more about {% data variables.copilot.copilot_cloud_agent %} and how it can support you.


</details>
