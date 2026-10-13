# 同步与查询 VS Code 会话历史

介绍同步状态、排除仓库、分享、删除和企业策略，以及通过 Chronicle 查询过去的编码活动。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/sessions/session-history.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/sessions/session-history.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/同步与查询 VS Code 会话历史.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: b4e8c9f3-6d5a-4b2e-c7a4-8f9e1b3d2c5a
DateApproved: 10/7/2026
MetaDescription: Sync, share, and query Copilot session history in {% data variables.product.prodname_vscode %} with GitHub sync, privacy controls, Chronicle reports, and natural-language search.
MetaSocialImage: ../../../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 同步和查询会话历史记录

{% data variables.product.prodname_vscode_shortname %} 维护您的 Copilot 会话的可搜索历史记录。默认情况下，它将本地 agent 会话同步到您的 GitHub 帐户，以便您可以跨设备访问它们、共享选定的会话以及从 {% data variables.product.prodname_vscode_shortname %}、{% data variables.copilot.copilot_cli_short %}、{% data variables.copilot.copilot_cloud_agent_short %}、代码审查和 GitHub Copilot 桌面应用程序查询工作。

除非您明确共享，否则同步的会话是私有的。

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Try it">
启动聊天提示，将您最近的编码会话总结为站立报告。

* [打开于{% data variables.product.prodname_vscode_shortname %}](<vscode://GitHub.Copilot-Chat/chat?agent=agent%26prompt=%2Fchronicle%3Astandup>)

</div>

## 选择退出会话同步

要仅将会话数据保留在本地，请将 `setting(chat.sessionSync.enabled)` 设置为 `false`。当您选择退出时，会话数据将保留在您的计算机上，并且您只能在本地查询。

## 从同步中排除存储库

使用 `setting(chat.sessionSync.excludeRepositories)` 阻止特定存储库中的会话同步到云。该设置接受精确的 `owner/repo` 名称或全局模式：

```json
"chat.sessionSync.excludeRepositories": [
    "my-org/private-repo",
    "my-org/secret-*"
]
```

来自匹配存储库的会话仅存储在本地。

## 企业政策

对于 {% data variables.copilot.copilot_business_short %} 和 {% data variables.copilot.copilot_enterprise_short %} 用户，有两个策略控制会话同步：

* **GitHub.com 企业策略**（“在云中存储本地会话”）：企业和组织所有者在 GitHub.com 上配置此策略，以为其用户启用或禁用云同步。
* **{% data variables.product.prodname_vscode_shortname %} 组策略 ** (`CopilotSessionSync`)：禁用时，`setting(chat.sessionSync.enabled)` 设置强制为 `false`，并且会话仅保留在本地。

> [!IMPORTANT]
> 启用该策略不会授予管理员访问您的会话数据的权限。同步会话与您的个人帐户绑定，默认情况下只有您可以访问。

当被策略禁用时，会话同步状态显示 ** 被策略禁用 **，并且用户无法覆盖该设置。

## 共享会话

默认情况下不共享会话。在 GitHub.com 上，您可以与有权访问存储库的任何人共享同步会话，以进行仅查看访问：

1. 打开 GitHub.com 上的 **Agents** 选项卡。
1. 选择一个会话并从`...`菜单中打开**共享设置**。
1. 启用共享以向存储库协作者提供仅查看访问权限。

收件人可以查看会话的提示、响应和文件更改，但无法引导或修改会话。共享会话不会为其他用户的会话查询建立索引。

## 会话同步状态

会话同步状态显示在{% data variables.copilot.chat_view %}中的Copilot状态栏中。它显示了云同步的当前状态：

|状态|描述|
|-------|-------------|
|**未启用**|会话同步已关闭。数据保留在该设备的本地。|
|**已启用**|会话正在同步到您的 GitHub 帐户。|
|**N 个会话已同步**|显示已上传的会话数。选择查看 GitHub.com 上的会话。|
|**正在同步 N 个会话**|上传正在进行中。|
|**被策略禁用**|您的组织的策略阻止会话同步。|
|**同步错误**|上次同步期间出现问题。稍后再试。|

## 隐私和数据控制

* 默认情况下，会话对您来说是私有的。同步会话与您的个人 GitHub 帐户绑定，并且只有您可以访问，除非您明确共享它们。
* 在数据离开您的计算机之前，令牌、API 密钥和凭据等机密会被自动删除。
* 您可以随时通过将 `setting(chat.sessionSync.enabled)` 设置为 `false` 来选择退出。现有的同步会话将保留在 GitHub.com 上，直到您将其删除。

## 删除同步会话

要删除同步的会话数据，请从命令面板运行 **删除会话同步数据** 命令 (`github.copilot.sessionSync.deleteSessions`)。该命令显示一个选择器，您可以在其中选择要删除的会话。选择会话后，您可以选择删除范围：

* **从本地和云端删除**：从您的计算机和 GitHub.com 中删除会话数据。此操作无法撤消。
* **仅从云中删除**：从GitHub.com删除会话数据，但保持本地数据完整。

您还可以从 GitHub.com 上的 **Agents** 选项卡隐藏或删除单个同步会话。隐藏会话会将其从会话索引中删除，以便它不再出现在查询结果中。

## 使用 Chronicle 查询会话历史记录

使用 Chronicle 命令或自然语言问题来搜索过去的会话、生成站立报告并获取个性化的使用提示。

### 编年史命令

在聊天输入中输入以下命令：

|命令|描述|
|---------|-------------|
| `/chronicle:standup` |将最近的编码会议总结为站立报告，按分支和存储库分组。默认情况下涵盖最后 24 小时。|
| `/chronicle:tips` |分析最近的会话历史记录并提出更有效地使用 Copilot 的方法。|
| `/chronicle:cost-tips` |确定减少代币使用和 Copilot 成本的机会。|
| `/chronicle:search <query>` |按关键字、文件路径、拉取请求或问题参考搜索会话。|
| `/chronicle:reindex` |重建本地会话索引并将会话数据同步到您的帐户。|

### 询问有关会话历史记录的问题

提出自由形式的问题，例如“我昨天编辑了哪些文件？”或“我是否做过与付款相关的任何事情API？” Copilot 使用语义理解来查找相关会话。当您想要直接内容搜索时，请使用 `/chronicle:search`。

### 跟踪什么

本地会话存储记录：

* **会话元数据**：存储库、分支、工作目录、时间戳和agent或参与者。
* **对话转向**：用户消息和助理响应。
* **触及的文件**：工具调用的文件路径。
* **外部引用**：拉取请求编号、问题编号和提交 SHA。

数据存储在本地 SQLite 数据库中。在同步数据之前，会过滤令牌、API 密钥、密码和连接字符串等机密。

### 重新索引会话存储

如果会话丢失或数据库损坏，请重建索引：

```prompt
/chronicle:reindex
```

您还可以从命令面板运行 **Reindex Sessions** (`github.copilot.chronicle.reindex`)。

从备份恢复会话文件、意外崩溃、手动删除会话目录或选择重新进行会话同步后重新索引。

## 设置参考

|设置|默认|描述|
|---------|---------|-------------|
| `setting(github.copilot.chat.localIndex.enabled)` | `true` |启用本地会话跟踪（同步的先决条件）|
| `setting(chat.sessionSync.enabled)` | `true` |将会话同步到您的 GitHub 帐户|
| `setting(chat.sessionSync.excludeRepositories)` | `[]` |要从同步中排除的存储库模式|

## 相关内容

* [管理 agent 会话](<https://github.com/docs/agents/run/sessions/manage-sessions.md>) - 创建和组织 agent 会话
* [安全](<https://github.com/docs/agents/run/security.md>) - Copilot 安全和隐私
* [使用缓存资源管理器诊断提示缓存](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>) - 查找会话浪费令牌的位置


## 原文对照

<details>
<summary>展开完整原文</summary>

# Sync and query session history

{% data variables.product.prodname_vscode_shortname %} maintains a searchable history of your Copilot sessions. By default, it syncs local agent sessions to your GitHub account so you can access them across devices, share selected sessions, and query work from {% data variables.product.prodname_vscode_shortname %}, {% data variables.copilot.copilot_cli_short %}, {% data variables.copilot.copilot_cloud_agent_short %}, code review, and the GitHub Copilot Desktop app.

Synced sessions are private unless you explicitly share them.

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Try it">
Launch a chat prompt that summarizes your recent coding sessions into a standup report.

* [Open in {% data variables.product.prodname_vscode_shortname %}](<vscode://GitHub.Copilot-Chat/chat?agent=agent%26prompt=%2Fchronicle%3Astandup>)

</div>

## Opt out of session sync

To keep session data local only, set `setting(chat.sessionSync.enabled)` to `false`. When you opt out, session data stays on your machine and you can only query it locally.

## Exclude repositories from sync

Use `setting(chat.sessionSync.excludeRepositories)` to prevent sessions in specific repositories from syncing to the cloud. The setting accepts exact `owner/repo` names or glob patterns:

```json
"chat.sessionSync.excludeRepositories": [
    "my-org/private-repo",
    "my-org/secret-*"
]
```

Sessions from matching repositories are stored locally only.

## Enterprise policy

For {% data variables.copilot.copilot_business_short %} and {% data variables.copilot.copilot_enterprise_short %} users, two policies control session sync:

* **GitHub.com enterprise policy** ("Store local sessions in the Cloud"): enterprise and organization owners configure this on GitHub.com to enable or disable cloud sync for their users.
* **{% data variables.product.prodname_vscode_shortname %} group policy** (`CopilotSessionSync`): when disabled, the `setting(chat.sessionSync.enabled)` setting is forced to `false` and sessions stay local only.

> [!IMPORTANT]
> Enabling the policy does not give administrators access to your session data. Synced sessions are tied to your personal account and are accessible only to you by default.

When disabled by policy, the session sync status shows **Disabled by policy** and users cannot override the setting.

## Share a session

Sessions are not shared by default. On GitHub.com, you can share a synced session for view-only access to anyone who has access to the repository:

1. Open the **Agents** tab on GitHub.com.
1. Select a session and open **Sharing settings** from the `...` menu.
1. Enable sharing to give repository collaborators view-only access.

Recipients can view the session's prompts, responses, and file changes, but cannot steer or modify the session. Shared sessions are not indexed for other users' session queries.

## Session sync status

The session sync status appears in the Copilot status bar in the {% data variables.copilot.chat_view %}. It shows the current state of cloud sync:

| State | Description |
|-------|-------------|
| **Not enabled** | Session sync is off. Data stays local to this device. |
| **Enabled** | Sessions are syncing to your GitHub account. |
| **N sessions synced** | Shows how many sessions have been uploaded. Select to view sessions on GitHub.com. |
| **Syncing N sessions** | Upload is in progress. |
| **Disabled by policy** | Your organization's policy prevents session sync. |
| **Sync error** | Something went wrong during the last sync. Try again later. |

## Privacy and data control

* Sessions are private to you by default. Synced sessions are tied to your personal GitHub account and are accessible only to you unless you explicitly share them.
* Secrets such as tokens, API keys, and credentials are automatically stripped before data leaves your machine.
* You can opt out at any time by setting `setting(chat.sessionSync.enabled)` to `false`. Existing synced sessions remain on GitHub.com until you delete them.

## Delete synced sessions

To delete synced session data, run the **Delete Session Sync Data** command (`github.copilot.sessionSync.deleteSessions`) from the Command Palette. The command shows a picker where you select which sessions to remove. After selecting sessions, you choose the deletion scope:

* **Delete from local and cloud**: removes session data from your machine and from GitHub.com. This action cannot be undone.
* **Delete from cloud only**: removes session data from GitHub.com but keeps local data intact.

You can also hide or delete individual synced sessions from the **Agents** tab on GitHub.com. Hiding a session removes it from your session index so it no longer appears in query results.

## Query session history with Chronicle

Use Chronicle commands or natural-language questions to search past sessions, generate standup reports, and get personalized usage tips.

### Chronicle commands

Enter these commands in the chat input:

| Command | Description |
|---------|-------------|
| `/chronicle:standup` | Summarize recent coding sessions into a standup report, grouped by branch and repository. Covers the last 24 hours by default. |
| `/chronicle:tips` | Analyze recent session history and suggest ways to use Copilot more effectively. |
| `/chronicle:cost-tips` | Identify opportunities to reduce token usage and Copilot cost. |
| `/chronicle:search <query>` | Search sessions by keyword, file path, or pull request or issue reference. |
| `/chronicle:reindex` | Rebuild the local session index and sync session data to your account. |

### Ask questions about session history

Ask free-form questions such as "What files did I edit yesterday?" or "Have I worked on anything related to the payments API?" Copilot uses semantic understanding to find relevant sessions. Use `/chronicle:search` when you want a direct content search.

### What gets tracked

The local session store records:

* **Session metadata**: repository, branch, working directory, timestamps, and the agent or participant.
* **Conversation turns**: user messages and assistant responses.
* **Files touched**: file paths from tool calls.
* **External references**: pull request numbers, issue numbers, and commit SHAs.

Data is stored in a local SQLite database. Secrets such as tokens, API keys, passwords, and connection strings are filtered before data is synced.

### Reindex the session store

If sessions are missing or the database becomes corrupted, rebuild the index:

```prompt
/chronicle:reindex
```

You can also run **Reindex Sessions** (`github.copilot.chronicle.reindex`) from the Command Palette.

Reindex after restoring session files from a backup, an unexpected crash, manually deleting session directories, or opting back into session sync.

## Settings reference

| Setting | Default | Description |
|---------|---------|-------------|
| `setting(github.copilot.chat.localIndex.enabled)` | `true` | Enable local session tracking (prerequisite for sync) |
| `setting(chat.sessionSync.enabled)` | `true` | Sync sessions to your GitHub account |
| `setting(chat.sessionSync.excludeRepositories)` | `[]` | Repository patterns to exclude from sync |

## Related content

* [Manage agent sessions](<https://github.com/docs/agents/run/sessions/manage-sessions.md>) - Create and organize agent sessions
* [Security](<https://github.com/docs/agents/run/security.md>) - Copilot security and privacy
* [Diagnose prompt caching with the Cache Explorer](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>) - Find where sessions waste tokens


</details>
