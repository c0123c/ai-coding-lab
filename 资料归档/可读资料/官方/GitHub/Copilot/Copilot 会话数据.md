# Copilot 会话数据

说明本地和 GitHub 会话保存哪些数据、存放位置、同步策略、访问权限、共享和保留方式，用于理解开发会话的数据边界。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：源码提交（存档版）：[2026-09-23](<https://github.com/github/docs/commit/fc2c908a7831570a31dd61732aa3ba4645559b1a>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/concepts/security-governance-and-network-settings/session-data.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 会话数据.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: About GitHub Copilot session data
shortTitle: Session data
allowTitleToDifferFromFilename: true
intro: 'Understand what session data is, where it is stored, who can access it, and how it is managed.'
versions:
  feature: copilot
contentType: concepts
category:
  - Learn about Copilot # Copilot discovery page
docsTeamMetrics:
  - copilot-cli
redirect_from:
  - /copilot/concepts/agents/copilot-cli/chronicle
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

会话是与{% data variables.product.prodname_copilot_short %}交互的一段时间，例如IDE中的对话或agent执行的工作。 **会话数据**是记录有关该交互的信息。这可以包括提示和响应、使用的工具以及对文件所做的更改。

您的**会话历史记录**是您可以查询的会话集合。

会话数据可帮助您了解并返回使用 {% data variables.product.prodname_copilot_short %} 执行的工作。您可以使用会话数据来：

* **查询您的会话历史记录**：询问有关您之前会话中的工作的自然语言问题。
* **恢复会话**：从之前会话中中断的位置继续。
* **查看或分享**会话。
* **生成见解**，例如站立报告、工作流程提示和成本分析。

可用功能取决于会话运行的表面。

## 了解会话位置和访问

会话运行的位置、数据的存储位置、是否同步到您的 {% data variables.product.github %} 帐户以及谁可以访问它是单独考虑的因素。

本地运行的会话可以将数据存储在您的计算机和您的 {% data variables.product.github %} 帐户中。同步会话不会与其他人共享。

### 本地运行的会话

{% data variables.copilot.copilot_cli_short %}和{% data variables.copilot.github_copilot_app %}在`~/.copilot/session-state/`下存储每个会话的完整记录。它们还将数据的子集存储在本地 SQLite 数据库中，称为会话存储。会话存储支持会话历史记录查询和 `/chronicle` 命令。

在 IDE 中启动的会话的会话存储是 IDE- 特定的。有关存储哪些会话数据以及存储位置的信息，请参阅 IDE 的文档。

### 会话在 {% data variables.product.github %} 上运行

{% data variables.copilot.copilot_cloud_agent %} 会话在 {% data variables.product.github %} 托管的临时环境中运行。会话结束时环境被破坏，但会话日志在 {% data variables.product.prodname_dotcom_the_website %} 上仍然可用。这些会话默认情况下是共享的，并且对有权访问存储库的人员可见。

## 会话同步

默认情况下，使用 {% data variables.copilot.copilot_cli_short %} 或 {% data variables.copilot.github_copilot_app %} 创建的本地运行会话会同步到您的 {% data variables.product.github %} 帐户。

您可以控制{% data variables.copilot.copilot_cli_short %}和{% data variables.copilot.github_copilot_app %}的同步。  参见[AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/copilot-cli-reference/cli-config-dir-reference>)中的`remote`和`remoteExport`。

### 管理会话同步的策略

对于 {% data variables.copilot.copilot_enterprise_short %} 和 {% data variables.copilot.copilot_business_short %} 用户，适用的“**在云中存储本地会话**”策略必须至少设置为“从云查看”才能同步会话数据。如果禁用或未配置策略，则会话仅存储在本地。

## 隐私和共享

默认情况下，本地会话不共享。您可以与有权访问存储库的人员共享单独的本地会话。收件人具有仅查看访问权限，并且共享会话不包含在收件人自己的会话历史记录的查询中。请参阅[共享会话](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle#sharing-a-session>)。

同步的会话数据与您的个人帐户绑定，默认情况下只有您可以访问。管理员可以控制同步是否可用，但启用同步并不能让他们访问您的会话数据。

默认情况下，{% data variables.copilot.copilot_cloud_agent %} 会话是共享的。它们出现在存储库的“Agents”选项卡上的“所有会话”视图中，任何有权访问存储库的人都可以看到。

当您查询之前的交互或使用 `/chronicle` 时，
{% data variables.product.prodname_copilot_short %} 可能会向 AI 模型发送相关会话数据，例如提示、上下文和响应。

## 会话数据的访问和保留

可用于管理会话数据的控件取决于会话数据的存储和访问位置。

### 本地存储的会话

可以使用以下控件：

* **共享**：以链接、要点或文件的形式创建会话的可共享副本。
* **删除**：从本地存储中永久删除会话。
* **存档**：将会话移出活动列表而不删除它。

|表面|分享|删除|存档|
| --- | --- | --- | --- |
| {% data variables.copilot.copilot_cli_short %} | {% octicon "check" aria-label="Supported" %} | {% octicon "check" aria-label="Supported" %} | {% octicon "x" aria-label="Not supported" %} |
| {% data variables.copilot.github_copilot_app %} | {% octicon "check" aria-label="Supported" %} | {% octicon "check" aria-label="Supported" %} | {% octicon "check" aria-label="Supported" %} |

对于 {% data variables.copilot.copilot_cli_short %}，删除已同步到您的帐户的本地会话会提示您选择是否也删除已同步的副本。对于 {% data variables.copilot.github_copilot_app %}，删除本地会话也会立即删除其同步副本。

### 会话存储在 {% data variables.product.prodname_dotcom_the_website %} 上

可以使用以下控件：

* **共享**：使会话对有权访问存储库的人员可见。
* **删除**：从{% data variables.product.prodname_dotcom_the_website %}中永久删除同步的会话记录。
* **存档**：将会话移出{% data variables.product.prodname_dotcom_the_website %}上的活动列表，而不删除它。

|会话源|分享|删除|存档|
| --- | --- | --- | --- |
|已同步 {% data variables.copilot.copilot_cli_short %} 会话|{% octicon "check" aria-label="Supported" %}（默认不共享）| {% octicon "check" aria-label="Supported" %} | {% octicon "check" aria-label="Supported" %} |
|已同步 {% data variables.copilot.github_copilot_app %} 会话|{% octicon "check" aria-label="Supported" %}（默认不共享）| {% octicon "check" aria-label="Supported" %} | {% octicon "check" aria-label="Supported" %} |
|{% data variables.copilot.copilot_cloud_agent %} 会话|{% octicon "check" aria-label="Supported" %}（默认共享）| {% octicon "x" aria-label="Not supported" %} | {% octicon "check" aria-label="Supported" %} |

有关更多信息，请参阅：

* **{% data variables.copilot.copilot_cli_short %}**: [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle>)
* **{% data variables.copilot.github_copilot_app %}**: [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/github-copilot-app/agent-sessions>)
* **{% data variables.copilot.copilot_cloud_agent %}**: [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)

有关 IDE 中 {% data variables.product.prodname_copilot_short %} 可用的会话数据控件的信息，请参阅该 IDE 的文档。

## {% data variables.copilot.copilot_cli_short %} 的会话管理

特别是对于 {% data variables.copilot.copilot_cli_short %}，您可以使用斜杠命令或通过手动与位于 `~/.copilot/session-state/` 的会话存储交互来管理本地存储的会话数据。

### 删除会话数据

您可以从您的计算机以及 {% data variables.product.prodname_dotcom_the_website %} 上的同步会话历史记录中删除本地会话数据。删除会话会将其从本地会话列表、会话历史记录索引和同步会话列表中删除。

使用`/session`命令删除CLI会话或从`~/.copilot/session-state/`手动删除会话数据。请参阅[删除会话](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle#deleting-sessions>)。

### 重新索引会话存储

如果本地会话文件被移动、从备份恢复或不再出现在会话存储中，`/chronicle reindex` 将从 `~/.copilot/session-state/` 下的文件重建会话存储。请参阅[重新索引会话存储](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle#reindexing-the-session-store>)。

## 进一步阅读

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/github-copilot-app/agent-sessions>)


## 原文对照

<details>
<summary>展开完整原文</summary>

A session is a period of interaction with {% data variables.product.prodname_copilot_short %}, such as a conversation in an IDE or work performed by an agent. **Session data** is the information recorded about that interaction. This can include prompts and responses, tools used, and changes made to files.

Your **session history** is the collection of sessions that you can query.

Session data helps you understand and return to work performed with {% data variables.product.prodname_copilot_short %}. You can use session data to:

* **Query your session history**: Ask natural-language questions about work from your previous sessions.
* **Resume sessions**: Pick up where you left off in any previous session.
* **Review or share** a session.
* **Generate insights** such as standup reports, workflow tips, and cost analysis.

The available capabilities depend on the surface where the session is running.

## Understanding session location and access

Where a session runs, where its data is stored, whether it is synced to your {% data variables.product.github %} account, and who can access it are separate considerations.

A locally run session can have data stored both on your machine and in your {% data variables.product.github %} account. Syncing a session does not share it with other people.

### Locally run sessions

{% data variables.copilot.copilot_cli_short %} and the {% data variables.copilot.github_copilot_app %} store the complete record of each session under `~/.copilot/session-state/`. They also store a subset of the data in a local SQLite database, referred to as the session store. The session store supports session-history queries and the `/chronicle` command.

Session storage for sessions started in an IDE is IDE-specific. For information about what session data is stored and where, see the documentation for your IDE.

### Sessions run on {% data variables.product.github %}

{% data variables.copilot.copilot_cloud_agent %} sessions run in an ephemeral environment hosted by {% data variables.product.github %}. The environment is destroyed when the session ends, but the session log remains available on {% data variables.product.prodname_dotcom_the_website %}. These sessions are shared by default and visible to people with access to the repository.

## Session syncing

By default, locally-run sessions created with {% data variables.copilot.copilot_cli_short %} or the {% data variables.copilot.github_copilot_app %} are synced to your {% data variables.product.github %} account.

You can control syncing for {% data variables.copilot.copilot_cli_short %} and the {% data variables.copilot.github_copilot_app %}.  See `remote` and `remoteExport` in [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/copilot-cli-reference/cli-config-dir-reference>).

### Policy governing session syncing

For {% data variables.copilot.copilot_enterprise_short %} and {% data variables.copilot.copilot_business_short %} users, the applicable "**Store local sessions in the Cloud**" policy must be set to at least "View from cloud" for session data to be synced. If the policy is disabled or unconfigured, sessions are stored locally only.

## Privacy and sharing

Local sessions are unshared by default. You can share an individual local session with people who have access to the repository. Recipients have view-only access, and shared sessions are not included in queries of the recipient's own session history. See [Sharing a session](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle#sharing-a-session>).

Synced session data is tied to your personal account and is accessible only to you by default. Administrators can control whether syncing is available, but enabling syncing does not give them access to your session data.

{% data variables.copilot.copilot_cloud_agent %} sessions are shared by default. They appear in the "All sessions" view on the "Agents" tab of your repository, visible to anyone with access to the repository.

When you query previous interactions or use `/chronicle`,
{% data variables.product.prodname_copilot_short %} may send relevant session data, such as prompts, context, and responses, to the AI model.

## Access and retention of session data

The controls available for managing session data depend on where the session data is stored and accessed.

### Locally stored sessions

The following controls are available:

* **Share**: Create a shareable copy of the session as a link, gist, or file.
* **Delete**: Permanently remove the session from local storage.
* **Archive**: Move the session out of your active list without deleting it.

| Surface | Share | Delete | Archive |
| --- | --- | --- | --- |
| {% data variables.copilot.copilot_cli_short %} | {% octicon "check" aria-label="Supported" %} | {% octicon "check" aria-label="Supported" %} | {% octicon "x" aria-label="Not supported" %} |
| {% data variables.copilot.github_copilot_app %} | {% octicon "check" aria-label="Supported" %} | {% octicon "check" aria-label="Supported" %} | {% octicon "check" aria-label="Supported" %} |

For {% data variables.copilot.copilot_cli_short %}, deleting a local session that has been synced to your account prompts you to choose whether to also delete the synced copy. For the {% data variables.copilot.github_copilot_app %}, deleting a local session also deletes its synced copy immediately.

### Sessions stored on {% data variables.product.prodname_dotcom_the_website %}

The following controls are available:

* **Share**: Make the session visible to people with access to the repository.
* **Delete**: Permanently remove the synced session record from {% data variables.product.prodname_dotcom_the_website %}.
* **Archive**: Move the session out of the active list on {% data variables.product.prodname_dotcom_the_website %} without deleting it.

| Session source | Share | Delete | Archive |
| --- | --- | --- | --- |
| Synced {% data variables.copilot.copilot_cli_short %} session | {% octicon "check" aria-label="Supported" %} (Unshared by default) | {% octicon "check" aria-label="Supported" %} | {% octicon "check" aria-label="Supported" %} |
| Synced {% data variables.copilot.github_copilot_app %} session | {% octicon "check" aria-label="Supported" %} (Unshared by default) | {% octicon "check" aria-label="Supported" %} | {% octicon "check" aria-label="Supported" %} |
| {% data variables.copilot.copilot_cloud_agent %} session | {% octicon "check" aria-label="Supported" %} (Shared by default) | {% octicon "x" aria-label="Not supported" %} | {% octicon "check" aria-label="Supported" %} |

For more information, see:

* **{% data variables.copilot.copilot_cli_short %}**: [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle>)
* **{% data variables.copilot.github_copilot_app %}**: [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/github-copilot-app/agent-sessions>)
* **{% data variables.copilot.copilot_cloud_agent %}**: [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)

For information about the session data controls available for {% data variables.product.prodname_copilot_short %} in an IDE, see the documentation for that IDE.

## Session management for {% data variables.copilot.copilot_cli_short %}

For {% data variables.copilot.copilot_cli_short %} specifically, you can manage locally stored session data using slash commands or by manually interacting with the session store located at `~/.copilot/session-state/`.

### Deleting session data

You can delete local session data from your machine and from your synced session history on {% data variables.product.prodname_dotcom_the_website %}. Deleting a session removes it from your local session list, your session-history index, and your synced session list.

Use the `/session` commands to delete CLI sessions or delete session data manually from `~/.copilot/session-state/`. See [Deleting sessions](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle#deleting-sessions>).

### Re-indexing the session store

If local session files are moved, restored from a backup, or no longer represented in the session store, `/chronicle reindex` rebuilds the session store from the files under `~/.copilot/session-state/`. See [Reindexing the session store](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle#reindexing-the-session-store>).

## Further reading

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/github-copilot-app/agent-sessions>)


</details>
