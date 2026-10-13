# 管理 Agent 会话

说明实时查看任务进度和日志、追溯提交、发送后续指令，以及停止、归档、分享和查询会话的方法。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/管理 Agent 会话.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 管理 agent 会话

实时监控 agent 的进度，通过后续提示进行引导，以及停止或存档会话。

使用 GitHub 上的 agents 面板来监控和管理存储库中的 agent 会话。

## 监控 agent 活动

从 agents 面板（可从 GitHub 上的任何页面访问）或 [agents 页面 ](<https://github.com/copilot/agents?ref_product=copilot\&ref_type=engagement\&ref_style=text>) 跟踪会话。您启动的会话或其他用户提示 Copilot 进行的会话将显示在您的会话列表中。

单击会话可打开会话日志和概述，您可以在其中监控 agent 的进度、令牌使用情况和会话时长。

## 查看会话日志

会话日志显示 Copilot 的内部推理以及它用于了解您的存储库、进行更改和验证其工作的工具。 Copilot 拥有自己的临时开发环境，因此它可以在推送之前运行自动化测试和 linter 来验证更改。

## 跟踪提交到会话日志

来自 Copilot 云 agent 的提交由 Copilot 撰写，启动任务的人被列为共同作者。每条提交消息都包含一个指向会话日志的链接，因此您可以跟踪在代码审查或审核期间进行更改的原因。提交已签名并在 GitHub 上显示为“已验证”。

## 引导 agent 会话

如果 Copilot 方向错误，或者您意识到原来的提示需要调整，您可以在不停止会话的情况下重定向它。

1. 打开[agents页面](<https://github.com/copilot/agents?ref_product=copilot\&ref_type=engagement\&ref_style=text>)并选择会话。

2. 在会话日志下方的提示框中，键入您的后续内容。例如：

   ```text
   Use our existing ErrorHandler utility class instead of writing custom try-catch blocks for each endpoint.
   ```

3. 按<kbd>输入</kbd>。 Copilot 在完成当前工具调用后实现您的输入。

每个转向消息消耗AI个积分。转向不适用于第三方编码 agents。

## 停止会话

如果不再需要完成该任务，或者您想以新提示重新开始，请在会话日志查看器中单击 ** 停止会话 **。停止会话会结束 GitHub 操作运行并保留所有已推送的提交。

## 存档会议

存档已停止的会话以将其从会话列表中删除。

1. 打开已停止的会话。
2. 单击 **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-kebab-horizontal" aria-label="More actions" role="img"><path d="M8 9a1.5 1.5 0 1 0 0-3 1.5 1.5 0 0 0 0 3ZM1.5 9a1.5 1.5 0 1 0 0-3 1.5 1.5 0 0 0 0 3Zm13 0a1.5 1.5 0 1 0 0-3 1.5 1.5 0 0 0 0 3Z"></path></svg>**，然后单击 **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-inbox" aria-label="inbox" role="img"><path d="M2.8 2.06A1.75 1.75 0 0 1 4.41 1h7.18c.7 0 1.333.417 1.61 1.06l2.74 6.395c.04.093.06.194.06.295v4.5A1.75 1.75 0 0 1 14.25 15H1.75A1.75 1.75 0 0 1 0 13.25v-4.5c0-.101.02-.202.06-.295Zm1.61.44a.25.25 0 0 0-.23.152L1.887 8H4.75a.75.75 0 0 1 .6.3L6.625 10h2.75l1.275-1.7a.75.75 0 0 1 .6-.3h2.863L11.82 2.652a.25.25 0 0 0-.23-.152Zm10.09 7h-2.875l-1.275 1.7a.75.75 0 0 1-.6.3h-3.5a.75.75 0 0 1-.6-.3L4.375 9.5H1.5v3.75c0 .138.112.25.25.25h12.5a.25.25 0 0 0 .25-.25Z"></path></svg> 存档会话 **。
3. 在对话框中，单击**是，存档**。

Copilot云agent会话可以存档，但不能删除。只能删除本地会话（来自 GitHub Copilot、CLI、VS Code、JetBrains 或 GitHub Copilot 应用程序）。有关删除会话的更多信息，请参阅[关于GitHub Copilot会话数据](<https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/session-data#deleting-session-data>)。

## 共享会话

默认情况下，Copilot 云 agent 会话是 ** 共享 **。它们出现在存储库的“Agents”选项卡上的“所有会话”视图中，任何有权访问存储库的人都可以看到。

本地会话（来自 GitHub Copilot、CLI、VS Code、JetBrains 或 GitHub Copilot 应用程序）默认为 ** 未共享 **。您可以选择从存储库的“Agents”选项卡共享本地会话，为存储库协作者提供仅查看访问权限。要共享或取消共享本地会话，请使用会话菜单中的共享控件。

收件人可以查看会话的提示、响应和文件更改，但无法引导或修改会话。共享本地会话显示在“所有会话”视图中，但不会为其他用户的会话查询编制索引（它们不会出现在其他用户的 `/chronicle` 结果中）。

有关会话同步的更多信息，请参阅[关于GitHub Copilot会话数据](<https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/session-data#session-syncing>)。

## 查询过去的会话

您可以通过在 VS Code、JetBrains、GitHub Copilot 应用程序或 GitHub.com 中的 Copilot 聊天中询问自然语言问题来搜索会话历史记录。 Copilot 从 Copilot CLI、Copilot 云 agent 和 Copilot 代码审查中搜索同步会话以提供答案。

您只能查询您启动的会话。默认情况下，您的会话数据是私有的，但您可以选择共享单个会话以进行仅查看访问。请参阅本文前面的[共享会话](<#共享会话>)。

### 先决条件

您的会话必须同步到您的 GitHub 帐户。默认情况下，会话数据会自动同步。如果您选择不同步（通过在 CLI 设置 JSON 中设置 `"remoteExport": false`），或者您的组织尚未启用“在云中存储本地会话”策略，则会话查询不可用。有关更多信息，请参阅[关于GitHub Copilot会话数据](<https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/session-data#session-syncing>)。

### 示例查询

```text
What agent tasks did I run last week?
```

```text
Find the session where I refactored authentication.
```

```text
Have I worked on anything related to the payments API?
```

```text
Show me sessions where the agent struggled or I had to correct it multiple times.
```

Copilot 可以搜索同步会话中的提示、响应、文件更改和其他上下文。

有关 CLI- 特定会话查询和 `/chronicle` 命令，请参阅 [ 使用 GitHub Copilot CLI 会话数据 ](<https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle>)。有关如何存储和同步会话数据的概念性信息，请参阅[关于GitHub Copilot会话数据](<https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/session-data>)。

## Continue GitHub Copilot 聊天中的会话工作

您可以在 Copilot 聊天和 GitHub 上的 Copilot 云 agent 会话之间传递上下文。

当您从 Copilot 聊天启动会话时，它会合并您的对话上下文，并且在会话运行时，Copilot 聊天会反映其状态，以便您可以提出后续问题。

当会话完成时，或者当您查看创建的拉取请求 Copilot 时，您可以询问更改的内容、验证的内容以及原因，Copilot 将通过在会话日志上绘制来回答。您还可以直接从 Copilot 聊天开始另一个会话。

有关更多信息，请参阅 GitHub.com 上的 [GitHub Copilot](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-on-github>) 和 [使用 GitHub Copilot 探索拉取请求](<https://docs.github.com/en/copilot/tutorials/explore-pull-requests>)。

## 进一步阅读

* [使用GitHub Copilot处理任务的最佳实践](<https://docs.github.com/en/copilot/tutorials/cloud-agent/get-the-best-results>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Managing agent sessions

Monitor an agent's progress in real time, steer it with follow-up prompts, and stop or archive sessions.

Use the agents panel on GitHub to monitor and manage agent sessions across your repositories.

## Monitor agent activity

Track sessions from the agents panel (available from any page on GitHub) or from the [agents page](<https://github.com/copilot/agents?ref_product=copilot\&ref_type=engagement\&ref_style=text>). Sessions that you started, or that another user prompted Copilot to work on, appear in your sessions list.

Click a session to open the session log and overview, where you can monitor the agent's progress, token usage, and session length.

## Review session logs

Session logs show Copilot's internal reasoning and the tools it used to understand your repository, make changes, and validate its work. Copilot has its own ephemeral development environment, so it can run automated tests and linters to validate changes before pushing.

## Trace commits to session logs

Commits from Copilot cloud agent are authored by Copilot, with the person who started the task listed as co-author. Each commit message includes a link to the session logs, so you can trace why a change was made during code review or an audit. Commits are signed and appear as "Verified" on GitHub.

## Steer an agent session

If Copilot is heading in the wrong direction, or you realize your original prompt needs adjusting, you can redirect it without stopping the session.

1. Open the [agents page](<https://github.com/copilot/agents?ref_product=copilot\&ref_type=engagement\&ref_style=text>) and select the session.

2. In the prompt box below the session log, type your follow-up. For example:

   ```text
   Use our existing ErrorHandler utility class instead of writing custom try-catch blocks for each endpoint.
   ```

3. Press <kbd>Enter</kbd>. Copilot implements your input after it finishes its current tool call.

Each steering message consumes AI credits. Steering is not available for third-party coding agents.

## Stop a session

If the task no longer needs to be done, or you want to start over with a new prompt, click **Stop session** in the session log viewer. Stopping a session ends the GitHub Actions run and preserves any commits already pushed.

## Archive sessions

Archive stopped sessions to remove them from your sessions list.

1. Open the stopped session.
2. Click **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-kebab-horizontal" aria-label="More actions" role="img"><path d="M8 9a1.5 1.5 0 1 0 0-3 1.5 1.5 0 0 0 0 3ZM1.5 9a1.5 1.5 0 1 0 0-3 1.5 1.5 0 0 0 0 3Zm13 0a1.5 1.5 0 1 0 0-3 1.5 1.5 0 0 0 0 3Z"></path></svg>**, then click **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-inbox" aria-label="inbox" role="img"><path d="M2.8 2.06A1.75 1.75 0 0 1 4.41 1h7.18c.7 0 1.333.417 1.61 1.06l2.74 6.395c.04.093.06.194.06.295v4.5A1.75 1.75 0 0 1 14.25 15H1.75A1.75 1.75 0 0 1 0 13.25v-4.5c0-.101.02-.202.06-.295Zm1.61.44a.25.25 0 0 0-.23.152L1.887 8H4.75a.75.75 0 0 1 .6.3L6.625 10h2.75l1.275-1.7a.75.75 0 0 1 .6-.3h2.863L11.82 2.652a.25.25 0 0 0-.23-.152Zm10.09 7h-2.875l-1.275 1.7a.75.75 0 0 1-.6.3h-3.5a.75.75 0 0 1-.6-.3L4.375 9.5H1.5v3.75c0 .138.112.25.25.25h12.5a.25.25 0 0 0 .25-.25Z"></path></svg> Archive session**.
3. In the dialog, click **Yes, archive**.

Copilot cloud agent sessions can be archived but not deleted. Only local sessions (from GitHub Copilot CLI, VS Code, JetBrains, or the GitHub Copilot app) can be deleted. For more information about deleting sessions, see [About GitHub Copilot session data](<https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/session-data#deleting-session-data>).

## Share a session

Copilot cloud agent sessions are **shared** by default. They appear in the "All sessions" view on the "Agents" tab of your repository, visible to anyone with access to the repository.

Local sessions (from GitHub Copilot CLI, VS Code, JetBrains, or the GitHub Copilot app) are **unshared** by default. You can choose to share a local session from the "Agents" tab of your repository, giving view-only access to repository collaborators. To share or unshare a local session, use the sharing controls in the session's menu.

Recipients can view the session's prompts, responses, and file changes, but cannot steer or modify the session. Shared local sessions appear in the "All sessions" view but are not indexed for other users' session queries (they will not appear in another user's `/chronicle` results).

For more information about session syncing, see [About GitHub Copilot session data](<https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/session-data#session-syncing>).

## Query past sessions

You can search your session history by asking natural language questions in Copilot Chat in VS Code, JetBrains, the GitHub Copilot app, or GitHub.com. Copilot searches across your synced sessions from Copilot CLI, Copilot cloud agent, and Copilot code review to provide answers.

You can only query sessions that you started. Your session data is private by default, but you can choose to share individual sessions for view-only access. See [Share a session](<#share-a-session>) earlier in this article.

### Prerequisites

Your sessions must be synced to your GitHub account. By default, session data is synced automatically. If you have opted out of syncing (by setting `"remoteExport": false` in your CLI settings JSON), or if your organization has not enabled the "Store local sessions in the Cloud" policy, session querying is not available. For more information, see [About GitHub Copilot session data](<https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/session-data#session-syncing>).

### Example queries

```text
What agent tasks did I run last week?
```

```text
Find the session where I refactored authentication.
```

```text
Have I worked on anything related to the payments API?
```

```text
Show me sessions where the agent struggled or I had to correct it multiple times.
```

Copilot can search across your prompts, responses, file changes, and other context from your synced sessions.

For CLI-specific session querying and `/chronicle` commands, see [Using GitHub Copilot CLI session data](<https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle>). For conceptual information about how session data is stored and synced, see [About GitHub Copilot session data](<https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/session-data>).

## Continue a session's work in GitHub Copilot Chat

You can pass context between Copilot Chat and your Copilot cloud agent sessions on GitHub.

When you start a session from Copilot Chat, it incorporates the context of your conversation, and while the session runs, Copilot Chat reflects its status so you can ask follow-up questions.

When a session is complete, or when you are viewing a pull request Copilot created, you can ask what changed, what was validated, and why, and Copilot will answer by drawing on the session logs. You can also start another session directly from Copilot Chat.

For more information, see [GitHub Copilot on GitHub.com](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-on-github>) and [Using GitHub Copilot to explore pull requests](<https://docs.github.com/en/copilot/tutorials/explore-pull-requests>).

## Further reading

* [Best practices for using GitHub Copilot to work on tasks](<https://docs.github.com/en/copilot/tutorials/cloud-agent/get-the-best-results>)


</details>
