# 远程控制 Copilot CLI 会话

说明从网页或移动端查看和引导本地 CLI 会话的前提、操作、重连与访问控制，适用于离开电脑后继续管理任务。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-08-18](<https://github.com/github/docs/commit/9eaee0af43decd58b41a8ba821d1f1d04499064a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/agents/copilot-cli/about-remote-control.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/远程控制 Copilot CLI 会话.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: About remote control of {% data variables.copilot.copilot_cli %} sessions
shortTitle: About remote control
intro: 'Remote control lets you monitor and steer a {% data variables.copilot.copilot_cli_short %} session from {% data variables.product.prodname_dotcom_the_website %} or {% data variables.product.prodname_mobile %}, even after you''ve stepped away from your machine.'
versions:
  feature: copilot
redirect_from:
  - /copilot/concepts/agents/copilot-cli/about-remote-access
contentType: concepts
category:
  - Learn about Copilot # Copilot discovery page
  - Learn about Copilot CLI # Copilot CLI bespoke page
docsTeamMetrics:
  - copilot-cli
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

本文解释了有关 {% data variables.copilot.copilot_cli_short %} 会话远程控制的概念。有关如何启用远程控制的说明，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/steer-remotely>)。

## 当远程控制有帮助时

默认情况下，{% data variables.copilot.copilot_cli %} 会话只能从本地计算机进行控制。但是，您可以启用会话的远程控制。当您想要查看进度或响应提示和权限请求而不必留在运行会话的计算机上时，远程控制非常有用。例如：

* **您离开工作站**：继续通过手机或其他设备与{% data variables.product.prodname_copilot_short %}交互，而无需返回正在运行会话的计算机。
* **长时间运行的任务需要您的输入**：批准权限请求并回答出现的问题，以便在您离开时任务不会被阻止。
* **您想要快速状态检查**：在您处理其他事情时从{% data variables.product.prodname_mobile %}查看会话进度。

## 先决条件

远程控制需要：

* **策略启用**：如果您的{% data variables.product.prodname_copilot_short %}席位来自组织，则企业或组织所有者必须将“在云端存储本地会话”策略设置为“查看和控制”（默认情况下未配置）。请参阅本文后面的[管理远程控制](<#管理远程控制>)。
* **计算机必须在线**：CLI 会话必须在具有 Internet 连接的计算机的终端中主动运行。如果计算机进入睡眠状态或失去连接，则在计算机重新联机之前，远程控制将不可用。请参阅本文后面的[重新连接](<#重连>)。
* **交互式会话**：远程访问仅适用于交互式会话。当您通过 `--prompt` 命令行选项以编程方式使用 CLI 时（例如，当您在脚本中使用 CLI 时），它不可用。

远程控制不需要工作目录包含托管在 {% data variables.product.prodname_dotcom_the_website %} 上的 Git 存储库。对于 {% data variables.product.prodname_copilot_short %} 席位来自企业或组织的用户，由适用的企业或组织策略（而不是存储库托管）决定远程控制是否可用。请参阅本文后面的[管理远程控制](<#管理远程控制>)。

## 远程访问会话

当您对{% data variables.copilot.copilot_cli_short %}会话启用远程控制时，您可以转到{% data variables.product.prodname_dotcom_the_website %}或{% data variables.product.prodname_mobile %}并在最近的agent会话列表中找到该会话。远程界面实时更新，使您能够监控会话的持续输出，并在提示和权限请求出现时对其进行响应。

本地终端和远程接口同时处于活动状态。您可以在任一界面中输入命令。 {% data variables.copilot.copilot_cli_short %} 使用它收到的对任何提示或权限请求的第一个响应。

您的会话继续在本地计算机上运行。远程接口提供了一种与会话交互的方式，但 CLI 本身（及其运行的所有工具、shell 命令和文件操作）仍保留在您启动会话的计算机上。

## 您可以远程做什么

当从 {% data variables.product.prodname_dotcom_the_website %} 或 {% data variables.product.prodname_mobile %} 远程连接到会话时，您可以：

* **响应权限请求**：批准或拒绝工具、文件路径和URL权限请求。
* **回答问题**：当{% data variables.product.prodname_copilot_short %}要求您提供更多信息或做出决定时回答。
* **批准或拒绝计划**：当{% data variables.product.prodname_copilot_short %}处于计划模式时响应计划批准提示。
* **提交新提示**：输入问题或说明，就像在终端中一样。
* **切换模式**：更改会话模式 - 例如，在交互模式和计划模式之间切换。
* **结束当前操作**：取消agent当前的工作。

{% data reusables.cli.remote-access-slash-commands %}

## 重连

如果您的本地计算机和 {% data variables.product.prodname_dotcom %} 之间的连接暂时丢失（例如，由于网络中断），您可以在连接恢复后立即继续远程使用会话。

您可以使用 `/keep-alive` 斜杠命令来防止机器进入睡眠状态。请参阅[防止计算机进入睡眠状态](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/steer-remotely#preventing-your-machine-from-going-to-sleep>)。

{% data reusables.cli.remote-access-reconnection %}

## 安全和隐私

远程控制仅适用于使用启动 CLI 会话的同一帐户登录 {% data variables.product.prodname_dotcom %} 的人员。其他人都无法远程查看您的会话或与之交互。

当远程控制启用时：

* 会话事件（对话消息、工具执行事件和权限请求）从您的本地计算机发送到 {% data variables.product.prodname_dotcom %}。
* 远程命令由 {% data variables.copilot.copilot_cli_short %} 从 {% data variables.product.prodname_dotcom %} 轮询并注入到本地会话中。
* CLI 继续在本地运行。所有 shell 命令、文件操作和工具执行都发生在您的计算机上。除了 CLI agent 在会话中可以执行的操作之外，远程控制不会授予对您的计算机的直接访问权限。

## 管理远程控制

企业和组织所有者控制用户是否可以使用“在云中存储本地会话”策略启用远程控制。

* **组织级策略**（默认未配置）：组织所有者可以将此策略设置为“从云端查看”（仅同步）或“查看和控制”（同步加远程控制）。如果禁用或取消配置该策略，组织用户将无法使用会话同步或远程控制。
* **企业级策略**：企业所有者可以在所有组织中强制执行设置，或者选择“让组织决定”让每个组织选择自己的级别。如果企业强制执行“查看和控制”，则其下的所有组织都会收到该设置。

为了使远程控制可用，适用的策略（企业强制或组织级别）必须设置为“查看和控制”。

企业所有者可以使用 `remoteControl` 企业托管设置进一步限制远程控制，该设置适用于“在云中存储本地会话”策略。此设置适用于每个设备，并控制是否可以远程控制该设备 ** 上托管的会话 **：它可以要求控制客户端已获得特定组织的 SSO- 授权，或者完全禁用该设备上托管的会话的远程控制。它不会影响同一用户远程控制其他设备上托管的会话的能力。请参阅[AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings>)。

欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>)和[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/administer-copilot-cli-for-your-enterprise>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

This article explains the concepts around remote control of {% data variables.copilot.copilot_cli_short %} sessions. For instructions on how to enable remote control, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/steer-remotely>).

## When remote control helps

By default, {% data variables.copilot.copilot_cli %} sessions are only steerable from your local machine. However, you can enable remote control of the session. Remote control is useful when you want to view progress or respond to prompts and permission requests, without having to remain at the machine where the session is running. For example:

* **You step away from your workstation**: Keep interacting with {% data variables.product.prodname_copilot_short %} from your phone or another device, without returning to the machine where the session is running.
* **A long-running task needs your input**: Approve permission requests and answer questions as they come up, so the task isn't blocked while you're away.
* **You want a quick status check**: Glance at session progress from {% data variables.product.prodname_mobile %} while you work on something else.

## Prerequisites

Remote control requires:

* **Policy enablement**: If your {% data variables.product.prodname_copilot_short %} seat comes from an organization, an enterprise or organization owner must set the "Store local sessions in the Cloud" policy to "View and control" (unconfigured by default). See [Administering remote control](<#administering-remote-control>) later in this article.
* **The machine must be online**: The CLI session must be actively running in a terminal on a machine with an internet connection. If the machine goes to sleep or loses its connection, remote control is unavailable until the machine is back online. See [Reconnection](<#reconnection>) later in this article.
* **An interactive session**: Remote access is only available for interactive sessions. It is not available when you use the CLI programmatically with the `--prompt` command-line option, for example when you use the CLI in a script.

Remote control does not require the working directory to contain a Git repository hosted on {% data variables.product.prodname_dotcom_the_website %}. For users whose {% data variables.product.prodname_copilot_short %} seat comes from an enterprise or organization, the applicable enterprise or organization policy, not repository hosting, determines whether remote control is available. See [Administering remote control](<#administering-remote-control>) later in this article.

## Accessing a session remotely

When you enable remote control for a {% data variables.copilot.copilot_cli_short %} session, you can go to {% data variables.product.prodname_dotcom_the_website %} or {% data variables.product.prodname_mobile %} and find the session in the list of your recent agent sessions. The remote interface is updated in real time, allowing you to monitor ongoing output from the session and respond to prompts and permission requests as they come in.

Both the local terminal and the remote interface are active at the same time. You can enter commands in either interface. {% data variables.copilot.copilot_cli_short %} uses the first response it receives to any prompt or permission request.

Your session continues to run on your local machine. The remote interface provides a way to interact with the session, but the CLI itself (and all the tools, shell commands, and file operations it runs) remains on the machine where you started the session.

## What you can do remotely

When connected to a session remotely from {% data variables.product.prodname_dotcom_the_website %} or {% data variables.product.prodname_mobile %}, you can:

* **Respond to permission requests**: Approve or deny tool, file path, and URL permission requests.
* **Respond to questions**: Answer when {% data variables.product.prodname_copilot_short %} asks you to supply more information or make a decision.
* **Approve or reject plans**: Respond to plan approval prompts when {% data variables.product.prodname_copilot_short %} is in plan mode.
* **Submit new prompts**: Enter questions or instructions, just as you would in the terminal.
* **Switch modes**: Change the session mode—for example, between interactive and plan mode.
* **End the current operation**: Cancel the agent's current work.

{% data reusables.cli.remote-access-slash-commands %}

## Reconnection

If the connection between your local machine and {% data variables.product.prodname_dotcom %} is temporarily lost—for example, due to a network interruption—you can continue using the session remotely as soon as the connection is restored.

You can use the `/keep-alive` slash command to prevent your machine from going to sleep. See [Preventing your machine from going to sleep](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/steer-remotely#preventing-your-machine-from-going-to-sleep>).

{% data reusables.cli.remote-access-reconnection %}

## Security and privacy

Remote control is only available to the person signed in to {% data variables.product.prodname_dotcom %} with the same account that started the CLI session. No one else can view or interact with your sessions remotely.

When remote control is enabled:

* Session events (conversation messages, tool execution events, and permission requests) are sent from your local machine to {% data variables.product.prodname_dotcom %}.
* Remote commands are polled by {% data variables.copilot.copilot_cli_short %} from {% data variables.product.prodname_dotcom %} and injected into your local session.
* The CLI continues to run locally. All shell commands, file operations, and tool executions happen on your machine. Remote control does not grant direct access to your machine beyond what the CLI agent can do within the session.

## Administering remote control

Enterprise and organization owners control whether users can enable remote control using the "Store local sessions in the Cloud" policy.

* **Organization-level policy** (unconfigured by default): Organization owners can set this policy to "View from cloud" (syncing only) or "View and control" (syncing plus remote control). If the policy is disabled or unconfigured, neither session syncing nor remote control is available for the organization's users.
* **Enterprise-level policy**: Enterprise owners can enforce a setting across all organizations, or select "Let organizations decide" to let each organization choose its own level. If the enterprise enforces "View and control," all organizations under it receive that setting.

For remote control to be available, the applicable policy (enterprise-enforced or organization-level) must be set to "View and control."

Enterprise owners can further restrict remote control using the `remoteControl` enterprise managed setting, which applies on top of the "Store local sessions in the Cloud" policy. This setting is applied per device and controls whether a session **hosted on that device** can be remotely controlled: it can require that the controlling client is SSO-authorized for specific organizations, or disable remote control of sessions hosted on that device entirely. It doesn't affect the same user's ability to remotely control sessions hosted on other devices. See [AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings>).

For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>) and [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/administer-copilot-cli-for-your-enterprise>).


</details>
