# 在 Claude Code 会话之间发送消息

介绍查找和联系其他本地、远端或云端会话的方法，说明消息交付、闲置通知、可达范围与使用限制。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-10](<https://code.claude.com/docs/en/cross-session-messaging>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/cross-session-messaging.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/在 Claude Code 会话之间发送消息.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 向您的其他 Claude Code 会话发送消息

> 让 Claude 列出您在此计算机上的其他 Claude Code 会话并向其发送消息，并在其他计算机或云中访问您的会话。



**注释：**

跨会话消息传递需要 Claude Code v2.1.224 或更高版本的 macOS 和 Linux，包括 WSL 2 内的 Linux。在本机 Windows 上，它需要 Claude Code v2.1.234 或更高版本。当会话满足要求时，消息传递将开启，无需启用任何内容。请参阅 [ 可用性 ](<#可用性>) 了解提供商要求以及如何确认会话具有它。


跨会话消息传递让 Claude 将消息从您的一个 Claude Code 会话传递到另一个会话。当一个会话中的更改破坏了另一个会话的基础时，Claude 可以在您注意到之前警告该会话。当一个会话解决了另一个会话被阻止的问题时，Claude 可以发送答案。

消息是一个人 Claude 写入另一个人的一段文本，而不是发送者的对话历史记录或文件。要移动整个对话或其上下文，请改为[恢复会话](<https://code.claude.com/docs/en/sessions#resume-a-session>)。

## 何时使用跨会话消息传递

当其中一个会话有其他会话需要完成任务时，请使用消息传递。 Claude 可以在需要时自行发送一条消息，例如在进行影响另一个会话正在执行的工作的更改后，或者您可以要求它发送一条消息。常见案例：

* **移交调查结果**：当一个会话发现重大更改或做出决定时，Claude会为受影响区域的会话进行总结，而不是在那里重新解释。
* **协调并行工作树**：当会话在单独的[工作树](<https://code.claude.com/docs/en/worktrees>)中使用同一存储库时，Claude可以告诉其他会话什么内容落地。
* **从长时间运行的工作中获取状态**：将迁移或测试运行报告返回到您正在观看的会话，或者从那里自己询问。如果该会话位于本计算机上，Claude 还可以 [ 在下次空闲或退出时 ](<#当另一个会话空闲时收到通知>) 请求它发出通知。
* **跨机器消息**：在另一台机器或云中到达您的一个会话。

## 向另一个会话发送消息

当您的一个会话了解另一个会话需要的内容（例如发现、状态或决定）时，Claude 会将其传递，而不是在终端之间复制粘贴。 Claude 使用 `ListAgents` 发现目标并使用 `SendMessage` 发送，因此您永远不会自己调用任何一个工具。 Claude 可以在没有询问的情况下决定发送消息，也可以提示发送消息。

要提示自己，请告诉 Claude 您希望其他会话知道或做什么。此示例是您键入的提示，而不是 Claude 发送的消息：

```text wrap theme={null}
Ask the session running in my other terminal whether the migration finished
```

Claude 本身写入实际消息，因此您的提示可以将内容留给 Claude。此提示要求提供摘要，但不指定其措辞，并且 Claude 发送的内容各不相同：

```text wrap theme={null}
Explain what we just did to the session working on the payments API
```

要自己命名目标，请在提示中提及会话：键入 `@`，后跟会话名称的第一个字母，然后从输入内容中选择会话，就像 [@-提及子代理 ](<https://code.claude.com/docs/en/sub-agents#invoke-subagents-explicitly>) 一样。需要 Claude Code v2.1.232 或更高版本。 Claude Code 插入提及，例如 `@api-worker`，并告诉 Claude 它命名的是哪个会话，因此 Claude 可以向该会话发送消息，而无需先列出您的会话。此提示通过提及来命名目标：

```text wrap theme={null}
Let @api-worker know the schema migration finished
```

预先输入内容会列出您在此计算机上的其他实时会话。有两种情况需要的不仅仅是名字的第一个字母：

* **本机之外的会话**：仅在 Claude 列出或向本机之外的会话发送消息后，云或远程控制会话才会出现在预输入中，因此请要求 Claude 首先列出它们。
* **包含空格或除字母、数字、连字符和下划线之外的其他字符的名称**：用双引号键入，例如`@"release notes"`。当您从预输入中选择会话时，Claude Code 会为您插入引号。

您也可以在不使用选择器的情况下键入提及内容。当多个实时会话回答所提到的名称时，Claude 会在发送前询问您指的是哪一个。

有关消息 Claude 到达时写入的内容（包括其中一个示例），请参阅 [ 消息是什么样子 ](<#what-a-message-looks-like>)。

### 消息传递

接收 Claude 在活动转动期间读取工具调用之间的消息，因此运行中的工具永远不会中断。当接收会话空闲时，Claude Code使用该消息开始新一轮。

来自另一个会话的消息以纯文本形式到达。如果它使用 `@` 提及文件或 [MCP 资源 ](<https://code.claude.com/docs/en/mcp#use-mcp-resources>)，则 Claude 会看到所提及的内容，而 Claude Code 不会附加任何内容，无论消息开始新一轮还是在一轮中到达。 Claude 仍然可以使用自己的工具在接收计算机上打开提到的路径，但须遵守该会话的权限。

Claude Code在以下情况下拒绝留言：

* 该消息[超过大小上限](<#局限性>)。 Claude Code 在发送会话中、在离开之前拒绝它。
* 此计算机上的会话快速爆发已达到 [ 该会话收件箱接受的容量 ](<#局限性>)。 Claude Code 拒绝向该会话发送更多消息。
* 本机之外的会话被[列为无法接收跨会话消息](<#其他机器上的消息会话>)。 Claude Code 在消息离开本机之前拒绝发送会话中的消息。
* 此计算机上的回复目标未通过安全检查，例如符号链接目标。 [拒绝发送跨会话消息](<https://code.claude.com/docs/en/errors#refusing-to-send-a-cross-session-message>)列出了这些检查。

接收会话根据其自己的 [ 入站控制 ](<#控制入站消息>) 检查每个到达的消息，检查以以下三个结果之一结束：

* **已交付**：Claude Code将消息传递给接收方Claude。
* **已保留**：Claude Code 将消息置于未送达状态。仅当您批准或稍后的模式或设置更改允许时，保留的消息才会到达 Claude。
* **拒绝**：Claude Code丢弃消息而不传递它。

发送后，消息将像您键入的提示一样计入 [ 使用量 ](<https://code.claude.com/docs/en/costs>)，并且接收方 Claude 可以以相同的方式回复发送方，但 [ 单向跨机器情况 ](<#其他机器上的消息会话>) 除外。

权限边界保留在每个会话中。 Claude 被指示永远不要要求另一个会话执行在其自己的会话中被拒绝或阻止的操作，或者其自己的权限设置将阻止的操作，而是将该工作路由回给您。在接收端，[接收会话自己的权限提示和规则仍然适用](<#会话如何处理传入消息>)消息要求的任何内容。

### 当另一个会话空闲时收到通知

Claude 可以要求该计算机上的一个会话在该会话下次空闲或退出时发回一条通知。这里空闲意味着会话完成了一个回合，没有任何队列。当您在另一个会话中等待较长的任务并希望听到它何时完成而不是检查时，请使用它。在两个会话中都需要 Claude Code v2.1.236 或更高版本。

#### 索取通知

告诉Claude你在等什么。此提示要求迁移会话发出通知：

```text wrap theme={null}
Tell me when the migration session finishes what it's working on
```

Claude 使用 `SendMessage` 工具的 `notify_when_idle` 输入进行订阅，要么附加到它正在发送的消息上，要么单独附加。就其本身而言，Claude Code 无需在观看的会话中开始轮次或花费代币即可订阅，并且如果该会话已空闲，则立即发送通知。附加到消息后，Claude Code先传递消息，后发送通知。

#### 每个会话显示什么

监视的会话显示一行，表示另一个进程要求在会话下一次空闲时被告知。询问会话将通知显示为命名所观看会话的行。该行可以包括该会话的回合结束的时间以及该回合的单行状态。如果询问会话空闲，Claude Code将开始新一轮的通知。

#### 限制

如果在 12 小时内没有收到通知，Claude Code 将放弃订阅并告知 Claude，因此它不会继续等待。

双方的 [ 入站控制 ](<#控制入站消息>) 适用于消息等通知：

* **`refuse` 两边**：什么也没有到达。观看的会话会丢弃请求，而不记录或应答该请求，因此订阅会在 12 小时后无人应答而过期，并且 `refuse` 的询问会话永远不会订阅。
* **`hold` 两侧**：通知到达时数量较少。观看的会话会忽略一行状态，而询问会话会在您的记录中显示通知，但不会将其发送到 Claude。

只有您主要对话中的 Claude 可以订阅，并且只能订阅您在本机上的会话。当 Claude 请求任何其他目标（例如队友、子代理或本机之外的会话）发出通知时，Claude Code 会拒绝整个呼叫，包括附加的任何消息。

### 查看 Claude 可以到达哪些会话

Claude 会自行查找消息的目标，因此您在要求其发送之前无需运行任何操作。要亲自查看 Claude 可以访问哪些会话，请运行 `/list-agents` 命令。第一行（如果存在）是该会话自己的名称，其他会话使用该名称向其发送消息。下面的行是 Claude 可以到达的会话：

* **子代理**：agents在当前会话内运行。
* **队友**：本场比赛自己的[agent队伍](<https://code.claude.com/docs/en/agent-teams>)队友。
* **您的其他本地会话**：在同一台计算机上运行的Claude Code会话，包括[后台会话](<https://code.claude.com/docs/en/agent-view>)。仅当绑定 [ 收件箱套接字 ](<#the-sessions-inbox-socket>) 时，才会出现会话。
* **您的[云会话](<https://code.claude.com/docs/en/claude-code-on-the-web>)**：此会话连接到[远程控制](<https://code.claude.com/docs/en/remote-control>)时显示。
* **您在其他计算机上的远程控制会话**：此会话连接到[远程控制](<https://code.claude.com/docs/en/remote-control>)时显示，并标记为`Remote Control`。 Claude Code 显示 `offline` 作为远程控制连接已断开的会话的状态。

当此会话连接到 [ 远程控制 ](<https://code.claude.com/docs/en/remote-control>) 时，Claude Code 会从 `/list-agents` 输出中保留本地会话的一些详细信息，而不更改 Claude 本身在查找会话消息时看到的内容：

* **工作目录**：它省略了每个本地会话的工作目录。
* **会话名称**：它省略了无法归属于某个人的任何会话名称，因此没有名称的行显示为`(unnamed session)`。
* **第一行**：它会省略带有此会话自己名称的行，除非您在此终端上键入该名称，并使用`--name`或`/rename`和名称，因为您启动或上次恢复了会话。

当输出列出任何内容时，它都会以一条注释结尾，说明详细信息已被保留。在会话自己的键盘上运行 `/rename`，后跟未使用的名称将为该会话提供一个出现在输出中的名称。

会话响应您使用 [`/rename`](<https://code.claude.com/docs/en/commands>) 命令或 [`--name`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 标志设置的名称。如果您未设置，Claude Code 会自行命名会话。对于交互式会话，该名称显示在运行会话的 [ 列表 ](<https://code.claude.com/docs/en/sessions#name-your-sessions>) 中。

当您使用该计算机上的另一个实时会话已使用的名称重命名会话或启动或恢复交互式会话时，Claude Code 会将该名称保留为已使用该名称的会话，并且 [ 会将您的名称重命名为变体 ](<https://code.claude.com/docs/en/sessions#name-your-sessions>)。会话仍然可以共享名称，例如，当其中一个会话运行早期版本的 Claude Code 或共享名称是生成的 Claude Code 时。除非此会话连接到远程控制，否则 Claude Code 在 `/list-agents` 输出中显示每个本地会话的工作目录，因此当同名会话在不同目录中运行时，您可以区分它们。 Claude 以两种方式之一处理消息，具体取决于有多少实时会话响应该名称：

* **一个会话应答名称**：Claude Code仅根据名称传递消息。
* **多个会话共享名称，或者Claude Code无法检查会话运行的所有位置**：Claude向其列表的每一行添加一个短标识符，并在地址中使用标识符。

### 其他机器上的消息会话

消息如何传输以及是否通过 Anthropic 服务器取决于目标会话的运行位置：

|其他会话运行的位置|消息如何传播|
| :- | :- |
|在这台机器上|通过 macOS 和 Linux 上的每会话套接字，或本机 Windows 上的每会话命名管道，从不通过 Anthropic 服务器|
|在你的另一台机器上|通过 Anthropic 服务器，通过该计算机的 [ 远程控制 ](<https://code.claude.com/docs/en/remote-control>) 连接到达|
|在[云端](<https://code.claude.com/docs/en/claude-code-on-the-web>)|通过Anthropic服务器，直接到云会话|

与另一台计算机上的会话开始对话需要 Claude Code v2.1.225 或更高版本以及 [ 出现在列表 ](<#查看-claude-可以到达哪些会话>) 中的目标。

[ 列表 ](<#查看-claude-可以到达哪些会话>) 中的会话行可以显示一个条件，该条件会更改 Claude 向该会话发送消息时发生的情况：

* **`offline`**：该会话的远程控制连接已断开。该消息会通过，但仅在该会话的计算机重新连接后才到达。
* **`can't receive cross-session messages (off in that session)`**：消息传递 [ 在该会话中不可用 ](<#可用性>)，或其 [`crossSessionInbound`](<https://code.claude.com/docs/en/settings-reference#crosssessioninbound>) 值为 `refuse`。 Claude Code 在消息离开本机之前拒绝向该会话发送消息。 Claude 的 `SendMessage` 调用下的结果开始于 `Not sent` 并指出原因。一旦在该会话中修复了原因，稍后的列表将不再显示此情况，并且 Claude 可以向该会话发送消息。

容器内的会话和主机上的会话无法相互访问。同一容器内的两个会话仍然可以互相发送消息，包括在 [ 自托管运行器 ](<https://code.claude.com/docs/en/self-hosted-environments>) 上。 WSL 2 内部的会话和同一台计算机上的本机 Windows 会话也无法相互访问。

如果当 Claude 发送到本机之外的会话时此会话未连接到远程控制，则消息仍会通过，但没有 [ 回复地址 ](<#what-a-message-looks-like>)，因此接收方 Claude 无法回复它。

要在任何消息超出本机范围之前需要您的批准，请设置 [`isolatePeerMachines`](<#需要批准跨机器消息>)。

## 会话如何处理传入消息

当会话 A 向会话 B 发送消息时，Claude Code 告诉 B 的 Claude 该消息来自另一个会话，而不是您，并限制该消息可以执行的操作：

* **它无法批准任何内容**：来自其他会话的消息永远不会被视为您的同意，因此它无法代表您回答待处理的权限提示。
* **无法更改配置**：Claude Code指示接收方Claude永远不要更改权限设置、`CLAUDE.md`或其他配置，因为另一个会话请求。
* **命令不运行**：消息文本中的命令（例如 `/compact`）以纯文本形式到达。 Claude Code从不执行它。
* **权限提示仍然会触发**：如果对消息进行操作需要接收会话没有的权限，您会看到与任何其他工作相同的提示。

<h3 id="what-a-message-looks-like">
 消息是什么样的
</h3>

当消息到达时，Claude Code 在对话中将其显示为暗淡的单行预览，并且预览行随后保留在对话中。预览中包含发件人姓名和消息第一行，较长时用 `…` 进行剪切，例如 `› Message from @api-worker: Schema migration finished (ctrl+o to expand)`。

其中任何一个都会显示全文：

* 按`Ctrl+O`打开[转录查看器](<https://code.claude.com/docs/en/interactive-mode#transcript-viewer>)并阅读发件人会话名称下的全文。
* 在 [ 全屏渲染 ](<https://code.claude.com/docs/en/fullscreen#use-the-mouse>) 中，单击保留部分消息的预览行以将其展开到位。
* 在以 [`--verbose`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 开始的会话中，Claude Code 显示全文而不是预览。

预览仅缩短您所看到的内容。无论您是否展开它，Claude 都会读取完整的消息。

Claude接收带有发送者姓名和回复地址的消息，但[单向跨机消息](<#其他机器上的消息会话>)除外，该消息不携带回复地址。

此示例是一个 Claude 写给另一个的消息，当您展开它时，其全文如下：

```text wrap theme={null}
Schema migration finished
The new column is tenant_id, and rebasing on main is safe now.
```

### 控制入站消息

设置 [`crossSessionInbound`](<https://code.claude.com/docs/en/settings-reference#crosssessioninbound>) 以选择会话对来自其他会话的消息执行的操作：

|价值|行为|
| :- | :- |
| `accept` |Claude Code 将每条消息传递至 Claude|
| `hold` |Claude Code 显示每条消息的通知，但不发送。如果 `accept` 稍后适用，则根据 [ 优先规则 ](<https://code.claude.com/docs/en/settings-reference#crosssessioninbound>)，Claude Code 释放保留的消息|
| `refuse` |Claude Code 丢弃每条消息而不传递它|

除了编辑设置文件之外，您还可以选择 `/config` 行 **来自其他会话的消息 ** 中的值。 Claude Code 将您选择的值写入用户设置。该行需要 Claude Code v2.1.232 或更高版本，并且在托管设置或 `--settings` 标志设置键时不会出现，因为此时用户设置值将不适用。 Claude Code 拒绝此键的 `/config crossSessionInbound=value` 简写。

要查看应用哪个值，请遵循 [ 设置参考 ](<https://code.claude.com/docs/en/settings-reference#crosssessioninbound>) 中的 `crossSessionInbound` 优先规则。

当没有值适用时，Claude Code 从两个会话的权限模式中决定每条消息。它将 [ 绕过权限提示 ](<https://code.claude.com/docs/en/permission-modes#skip-all-checks-with-bypasspermissions-mode>) 的会话分组为一类，并将所有其他会话分组为另一类。在具有可用绕过权限的交互式终端会话中，计划模式算作绕过，并且 [auto](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>)、`acceptEdits` 和 `dontAsk` 算作提示：

* **接收会话提示权限**：Claude Code传递每条消息。仅当发送会话将自身标识为绕过权限提示时，它才会保留一个供您批准。
* **接收会话绕过权限提示**：Claude Code保留每条消息以供您批准。仅当发送会话将自身标识为旁路时，它才会传送一个。

当默认在交互式终端会话中保留消息时，Claude Code 会在那里打开一个批准对话框。该对话框显示发件人和预览：

* **批准**将该消息传递给Claude。
* **拒绝**，或关闭对话框，将其删除。
* 当对话框在 [`dialogExpiry`](<https://code.claude.com/docs/en/settings-reference#dialogexpiry>) 截止日期之后仍未得到答复时，Claude Code 将关闭它并删除消息。截止时间默认为五分钟。
* 虽然没有终端连接到 [ 后台会话 ](<https://code.claude.com/docs/en/agent-view>)，但 Claude Code 在截止日期后仍使对话框保持打开状态。附加后，如果对话框在整个截止期限内仍未得到答复，Claude Code 会将其关闭并删除消息。
* 如果在保留消息时此会话的权限模式类别发生更改，Claude Code 会重新应用入站规则，传递它们现在接受的消息，并显示通知。

VS Code 扩展或桌面应用程序中的会话无法显示该对话框。 Claude Code 将保留的消息保留在那里，直到相同的截止日期，如 [ 非交互式会话 ](<#非交互式会话>) 所描述的。

Claude Code 最多保存 100 条消息，超过该限度就会丢弃最旧的消息。

### 非交互式会话

Claude Code 为 [`claude -p`](<https://code.claude.com/docs/en/headless>) 会话绑定收件箱套接字，就像交互式会话一样，因此长时间运行的 `-p` 工作线程可以接收消息并出现在列表中。当您以[裸模式](<https://code.claude.com/docs/en/headless#start-faster-with-bare-mode>)启动会话时，Claude Code不会绑定套接字，因此该会话无法接收消息并且不会出现在agent列表中。

`-p` 会话无法显示批准对话框。当 [ 入站默认 ](<#控制入站消息>) 保留一条消息时，Claude Code 将其保留到对话框使用的相同 [`dialogExpiry`](<https://code.claude.com/docs/en/settings-reference#dialogexpiry>) 截止时间，默认为五分钟：

* **截止日期之前**：如果模式或设置更改允许消息，Claude Code 会传送该消息。
* **已过截止日期**：Claude Code 丢弃该邮件并向其可以到达的发件人报告该邮件已过期。

将 `dialogExpiry` 设置为 `"never"` 以保留默认保留的消息，直到会话结束。由显式 `hold` 设置保存的消息不会过期； Claude Code 仅当`accept` 稍后适用时才传送。

要让 `-p` 工作线程在无人值守的情况下获取消息，请在其 `--settings` 值中将 `crossSessionInbound` 设置为 `accept` 来启动它。用户设置中的 `accept` 也有效，但适用于您运行的每个会话。

<h3 id="the-sessions-inbox-socket">
 会话的收件箱套接字
</h3>

当您期望的会话不在 agent 列表中、当您希望将脚本或挂钩发布到会话中或者当沙盒命令无法到达套接字时，请阅读本节。

Claude Code 为每个会话绑定一个收件箱套接字，并启用跨会话消息传递，计算机上的其他会话将在其中传递消息。该套接字是 macOS 和 Linux 上的 Unix 域套接字，包括 WSL 2 中的 Linux，以及本机 Windows 上的命名管道。对于哪些会话类型绑定一种，请参阅[非交互式会话](<#非交互式会话>)。

您可以在两个地方找到套接字的路径：

* `/status` 将其显示在 `Peer address` 行中。该路径以 `uds:` 为前缀。
* Claude Code 将其导出到 [hooks](<https://code.claude.com/docs/en/hooks>) 和 Bash 命令作为 [`CLAUDE_CODE_MESSAGING_SOCKET`](<https://code.claude.com/docs/en/env-vars#variables>) 环境变量：
  * 在以消息传递方式启动的会话中，Claude Code 在任何挂钩运行之前导出变量，包括 `SessionStart`。

在 macOS 和 Linux 上，Claude Code 将套接字限制为您的操作系统用户。在本机 Windows 上，它要求每个连接首先使用只有操作系统用户才能读取的密钥进行身份验证。无论哪种方式，在共享计算机上，其他用户的会话都无法传递给它。

在 macOS 和 Linux 上，Claude Code 还拒绝在它无法接受的目录中创建套接字，例如另一用户拥有的目录，并使用私有的每用户目录 `/tmp/cc-socks-<uid>` 来代替。当它无法接受任何目录时，会话将在没有收件箱的情况下运行：Claude Code 显示通知，`/status` 显示 `unavailable` 及其 `Peer address` 行中的原因，[`--debug`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 日志记录完全拒绝。

除了套接字的路径之外，Claude Code 将每个会话令牌导出为 [`CLAUDE_CODE_MESSAGING_TOKEN`](<https://code.claude.com/docs/en/env-vars#variables>)。发布到其自己的会话套接字的脚本可以发送 `{"type":"auth","token":"<token>"}` 作为其连接的第一行，其中 `<token>` 是 `CLAUDE_CODE_MESSAGING_TOKEN` 的值。 Claude Code是否需要线路取决于平台：

* **macOS 和 Linux，包括 WSL 2**：该行是可选的。 Claude Code 接受有或没有它的连接。
* **本机Windows**：该行是必需的。 Claude Code 关闭第一行不是有效授权行的所有连接，并且不会从该连接传递任何内容。

仅当您要发布的消息准备就绪时才打开连接。 Claude Code 会关闭在 30 秒内未发送完整行的连接，因此首先捕获慢速命令的输出，然后打开连接来发送它。

<span id="own-child-messages" />Claude Code 与任何其他对等消息一样，通过相同的 [ 入站控制 ](<#控制入站消息>) 运行到达套接字的消息，但有一个例外和一个先决条件：

* **自己的子消息**：当没有`crossSessionInbound`值适用时，Claude Code传递一条消息，它验证来自会话自己的子进程，例如回发到其自己会话套接字的挂钩或Bash命令。
  * 在 Linux 上，包括 WSL 2 内部，Claude Code 可以通过进程证据进行验证，即使对于已经退出的子进程也是如此。在 macOS 上，它只能在发布进程仍在运行时验证这种方式，并且在 Claude Code 作为进程 ID 1 运行的容器中，它根本没有进程证据。在本机 Windows 上也没有。
  * 在发布进程退出后的 macOS 上以及 Claude Code 作为进程 ID 1 运行的容器中，该进程证据丢失，并且 Claude Code 而是验证在打开其身份验证行中发送会话导出的 [`CLAUDE_CODE_MESSAGING_TOKEN`](<https://code.claude.com/docs/en/env-vars#variables>) 的子进程连接。在本机 Windows 上，该令牌是 Claude Code 验证自己的子消息的唯一方式。
  * 当 Claude Code 无法验证这两种方式时，它会像处理任何其他断言无权限类的消息一样对待该消息，因此绕过权限提示的会话会保留该消息以供您批准。
* **沙盒会话**：控制 Bash 命令是否可以从内部到达套接字 [沙箱](<https://code.claude.com/docs/en/sandboxing>) 使用沙箱的 Unix 套接字设置， [`sandbox.network.allowAllUnixSockets` 和 `sandbox.network.allowUnixSockets`](<https://code.claude.com/docs/en/settings-reference#sandbox-settings>).

## 限制跨会话消息传递

除了每条消息的默认设置之外，您还可以通过两种方式缩小消息范围。在任何消息离开计算机之前都需要您的批准，或者关闭会话或组织的消息传递。

### 需要批准跨机器消息

将 [`isolatePeerMachines`](<https://code.claude.com/docs/en/settings-reference#isolatepeermachines>) 设置为 `true`，以在任何 `SendMessage` 到达本机之外的会话之前需要您的明确批准：

```json theme={null}
{
  "isolatePeerMachines": true
}
```

通过此设置，Claude Code 在 Claude 发送给本机之外的会话的消息离开之前会请求您的批准，即使在 `bypassPermissions` 模式下也是如此，该模式会跳过普通的权限提示。任何设置范围的 `true` 都适用，因此签入的项目文件可以打开但不能关闭该要求。 Claude Code 不会在同一台计算机上的会话之间提示消息。

### 关闭跨会话消息传递

接收和发送是单独的控件，因此请关闭您需要的任何方向，或同时关闭这两个方向。对到达的消息使用 `crossSessionInbound`，对此处 Claude 可以发送或列出的内容使用权限规则：

* **停止接收**：将`crossSessionInbound`设置为`refuse`，并且Claude Code丢弃入站对等消息而不传递它们。从项目或本地设置，`refuse` 适用于所有其他源，并且从您的用户设置，它适用，除非托管设置或 `--settings` 标志设置了值。
* **停止发送并列出**：添加[权限拒绝规则](<https://code.claude.com/docs/en/permissions#tool-specific-permission-rules>)命名为`SendMessage`和`ListAgents`。两者都采用没有说明符的裸工具名称。

管理员可以在 [ 托管设置 ](<https://code.claude.com/docs/en/managed-settings>) 中关闭组织的两侧，将拒绝规则与 `refuse` 结合起来：

```json theme={null}
{
  "permissions": {
    "deny": ["SendMessage", "ListAgents"]
  },
  "crossSessionInbound": "refuse"
}
```

完成此操作后，Claude Code 仍然绑定每个会话的收件箱套接字，但会丢弃到达它的每条消息，而不向 Claude 传递任何内容。拒绝 `SendMessage` 还会删除发送给子代理和 agent 团队成员的消息，因为同一个工具为两者提供服务。拒绝会话在其自己的 `/status` 或同一计算机上的其他会话的列表中没有显示可见的更改，因此要确认它，请检查应用于该会话的设置文件而不是其状态。

## 可用性

跨会话消息传递需要 macOS、Linux 上的 Claude Code v2.1.224 或更高版本，以及本机 Windows 上的 WSL 2 和 v2.1.234 或更高版本。可用性以及哪些会话 Claude 可以发送消息，还取决于您的操作系统、提供商和配置：

* **操作系统**：可在macOS、Windows和Linux上使用，包括WSL 2内的Linux。

* **本机上的会话**：在每个提供商上可用，包括 Amazon Bedrock、AWS 上的 Claude 平台、Google 云的 Agent 平台和 Microsoft Foundry，以及在以下会话中在关闭 [ 功能标志获取 ](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>) 的情况下运行。在这些提供商上，并且在标记获取关闭的情况下，同机消息传递需要 Claude Code v2.1.248 或更高版本。 Claude Code 通过计算机上的 [ 每会话套接字 ](<#the-sessions-inbox-socket>) 传递这些消息，而不是通过 Anthropic 服务器。

要阻止会话接收它们，请将 [`crossSessionInbound`](<#关闭跨会话消息传递>) 设置为 `refuse`。

* **本机之外的会话**：Claude从连接到远程控制的会话中找到您的[云会话](<https://code.claude.com/docs/en/claude-code-on-the-web>)和其他计算机上的会话，该会话需要claude.ai登录作为此会话的主动身份验证和其他会话[远程控制要求](<https://code.claude.com/docs/en/remote-control#requirements>)。 Claude 无法使用 API 密钥或在 Amazon Bedrock、AWS 上的 Claude 平台、Google Cloud 的 Agent 平台和 Microsoft Foundry 上找到这些会话。

要检查会话，请键入 `/list-agents`，也可用作 `/peers`。结果将不具有该功能的会话与包含较窄内容阻止消息的会话区分开来，例如缺少 `SendMessage` 工具或拒绝发送：

* **`/list-agents` 无法识别**：会话没有跨会话消息传递。从版本要求的 `claude --version` 开始，完成上述要求。
* **`/list-agents` 有效，但发送未到达 **：消息传递已开启，并且适用范围更窄的内容：
  * **拒绝规则**：[权限拒绝规则](<#关闭跨会话消息传递>)删除了`SendMessage`和`ListAgents`工具。
  * **入站控制**：[接收会话的入站控制](<#控制入站消息>)可以保留或删除您发送的内容。
  * **云会话丢失**：仅当该会话连接到[远程控制](<https://code.claude.com/docs/en/remote-control>)时才会出现云会话。
  * **其他机器会话丢失**：仅当您的另一台机器与[远程控制](<https://code.claude.com/docs/en/remote-control>)一起运行并且该会话也已连接时，才会出现该会话。
  * **其他计算机会话 `offline`**：发往列为 `offline` 的会话的消息已通过，但 [ 仅在该会话的计算机重新连接 ](<#其他机器上的消息会话>) 后才到达。
  * ** 云或其他计算机会话 `can't receive cross-session messages`**：发往具有此条件 [ 的会话的消息不会离开本计算机 ](<#其他机器上的消息会话>)，并且 `SendMessage` 下的结果开始 `Not sent`。
  * **较旧的云或其他计算机会话丢失**：Claude Code首先读取最新的会话列表，并在有限数量的页面后停止，因此Claude无法按名称向超出它们的会话发送消息。

在具有消息传递的会话中，`/status` 还会显示 `Peer address` 行，其中包含会话自己的收件箱地址，或 `unavailable` 以及 Claude Code [ 无法设置收件箱 ](<#the-sessions-inbox-socket>) 的原因。

## 局限性

这里的限制是消息传递通道本身的属性，并且适用于该功能运行的任何地方。有关平台和提供商的差距，请参阅 [ 可用性 ](<#可用性>)。

* **仅纯文本**：Claude仅跨会话发送纯文本。结构化 [agent 团队 ](<https://code.claude.com/docs/en/agent-teams>) 协议消息保留在团队内。
* **同一机器消息大小有上限**：一旦序列化形式传递大约一百万个字符，Claude Code就会拒绝向该机器上的会话发送消息。拒绝[命名了确切的尺寸](<https://code.claude.com/docs/en/errors#message-too-large-for-cross-session-delivery>)。没有任何内容到达接收会话。
* **发件人拒绝快速突发到一个会话**：一旦快速突发到本计算机上的会话的消息达到该会话收件箱接受的内容，Claude Code将拒绝在发送会话中进一步发送。 [ 拒绝将突发命名为 ](<https://code.claude.com/docs/en/errors#too-many-messages-to-this-session-just-now>) 并告诉 Claude 将其余的批处理为一条消息或等待。
* **消息循环受到限制**：在接收会话中，Claude Code对每个发送者的重复消息进行速率限制，丢弃在短窗口内到达的相同重复消息，并将最多50个接受的消息排队供Claude读取。因此，两个会话之间的消息循环会自行停止。

## 相关资源

* [子代理](<https://code.claude.com/docs/en/sub-agents#resume-subagents>)和[agent团队](<https://code.claude.com/docs/en/agent-teams#messages-between-agents>)：单个会话或团队内的消息传递
* [后台agents](<https://code.claude.com/docs/en/agent-view>)：调度和监视您可能发送消息的并行会话
* [远程控制](<https://code.claude.com/docs/en/remote-control>)：连接此会话以访问其他计算机上的会话
* [频道](<https://code.claude.com/docs/en/channels>)：将外部事件（例如CI结果或聊天消息）推送到会话中
* [设置](<https://code.claude.com/docs/en/settings-reference#all-settings>)：`crossSessionInbound`、`isolatePeerMachines`和`dialogExpiry`
* [权限模式](<https://code.claude.com/docs/en/permission-modes>)：入站默认的两类背后的模式
* [工具参考](<https://code.claude.com/docs/en/tools-reference>)：工具表中的`ListAgents`和`SendMessage`行
* [并行运行agents](<https://code.claude.com/docs/en/agents>)：比较Claude Code运行多个agents的方式


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Message your other Claude Code sessions

> Let Claude list and message your other Claude Code sessions on this machine, and reach your sessions on other machines or in the cloud.



**注释：**

  Cross-session messaging requires Claude Code v2.1.224 or later on macOS and Linux, including Linux inside WSL 2. On native Windows, it requires Claude Code v2.1.234 or later. When a session meets the requirements, messaging is on with nothing to enable. See [Availability](<#availability>) for provider requirements and how to confirm a session has it.


Cross-session messaging lets Claude deliver a message from one of your Claude Code sessions to another. When a change in one session breaks what another is building on, Claude can warn that session before you notice. When one session settles a question another is blocked on, Claude can send the answer across.

A message is a piece of text one Claude writes to another, never the sender's conversation history or files. To move a whole conversation or its context, [resume the session](<https://code.claude.com/docs/en/sessions#resume-a-session>) instead.

## When to use cross-session messaging

Use messaging when one of your sessions has something another session needs mid-task. Claude can send a message on its own when it sees the need, for example after making a change that affects work another session is doing, or you can ask it to send one. The common cases:

* **Hand over a finding**: when one session discovers a breaking change or makes a decision, Claude summarizes it for the session working on the affected area, instead of you re-explaining it there.
* **Coordinate parallel worktrees**: when sessions work the same repository in separate [worktrees](<https://code.claude.com/docs/en/worktrees>), Claude can tell the other sessions what landed.
* **Get status from long-running work**: have a migration or test run report back to the session you're watching, or ask it yourself from there. If that session is on this machine, Claude can also [ask it for one notice when it next goes idle or exits](<#get-a-notice-when-another-session-goes-idle>).
* **Message across machines**: reach one of your sessions on another machine or in the cloud.

## Message another session

When one of your sessions learns something another session needs, such as a finding, a status, or a decision, Claude passes it along instead of you copy-pasting between terminals. Claude discovers the target with `ListAgents` and sends with `SendMessage`, so you never call either tool yourself. Claude can decide to send a message without being asked, and you can also prompt for one.

To prompt one yourself, tell Claude what you want the other session to know or do. This example is a prompt you type, not a message Claude sends:

```text wrap theme={null}
Ask the session running in my other terminal whether the migration finished
```

Claude writes the actual message itself, so your prompt can leave the content to Claude. This prompt asks for a summary without dictating its wording, and what Claude sends varies:

```text wrap theme={null}
Explain what we just did to the session working on the payments API
```

To name the target yourself, mention the session in your prompt: type `@` followed by the first letters of the session's name and pick the session from the typeahead, the same way you [@-mention a subagent](<https://code.claude.com/docs/en/sub-agents#invoke-subagents-explicitly>). Requires Claude Code v2.1.232 or later. Claude Code inserts the mention, such as `@api-worker`, and tells Claude which session it names, so Claude can message that session without listing your sessions first. This prompt names the target with a mention:

```text wrap theme={null}
Let @api-worker know the schema migration finished
```

The typeahead lists your other live sessions on this machine. Two cases need more than the first letters of a name:

* **A session beyond this machine**: a cloud or Remote Control session appears in the typeahead only after Claude has listed or messaged your sessions beyond this machine, so ask Claude to list them first.
* **A name with a space or other characters outside letters, digits, hyphens, and underscores**: type it in double quotes, such as `@"release notes"`. When you pick the session from the typeahead, Claude Code inserts the quotes for you.

You can also type the mention without the picker. When more than one live session answers to the mentioned name, Claude asks you which one you mean before sending.

For what the message Claude writes looks like when it arrives, including an example of one, see [what a message looks like](<#what-a-message-looks-like>).

### Message delivery

The receiving Claude reads the message between tool calls during an active turn, so a running tool is never interrupted. When the receiving session is idle, Claude Code starts a new turn with the message.

A message from another session arrives as plain text. If it mentions a file or an [MCP resource](<https://code.claude.com/docs/en/mcp#use-mcp-resources>) with `@`, Claude sees the mention as written and Claude Code attaches nothing, whether the message starts a new turn or arrives during one. Claude can still open a mentioned path on the receiving machine with its own tools, subject to that session's permissions.

Claude Code refuses a message in the following cases:

* The message is [over the size cap](<#limitations>). Claude Code refuses it in the sending session, before it leaves.
* A rapid burst to a session on this machine has reached [what that session's inbox accepts](<#limitations>). Claude Code refuses further messages to that session.
* A session beyond this machine is [listed as unable to receive cross-session messages](<#message-sessions-on-other-machines>). Claude Code refuses the message in the sending session, before it leaves this machine.
* The reply target on this machine fails a safety check, such as a symlinked target. [Refusing to send a cross-session message](<https://code.claude.com/docs/en/errors#refusing-to-send-a-cross-session-message>) lists these checks.

The receiving session checks each arriving message against its own [inbound controls](<#control-inbound-messages>), and the check ends in one of three outcomes:

* **Delivered**: Claude Code passes the message to the receiving Claude.
* **Held**: Claude Code sets the message aside undelivered. A held message reaches Claude only when you approve it or a later mode or settings change allows it.
* **Refused**: Claude Code drops the message without delivering it.

Once delivered, the message counts toward [usage](<https://code.claude.com/docs/en/costs>) like a prompt you type, and the receiving Claude can reply to the sender the same way, except in the [one-way cross-machine case](<#message-sessions-on-other-machines>).

Permission boundaries stay per-session. Claude is instructed never to ask another session for an action that was denied or blocked in its own session, or that its own permission settings would block, and to route that work back to you instead. On the receiving side, the [receiving session's own permission prompts and rules still apply](<#how-a-session-treats-an-incoming-message>) to anything the message asks for.

### Get a notice when another session goes idle

Claude can ask one of your sessions on this machine to send back one notice when that session next goes idle or exits. Idle here means the session finished a turn with nothing queued. Use it when you're waiting on a long task in another session and want to hear when it's done instead of checking. Requires Claude Code v2.1.236 or later in both sessions.

#### Ask for a notice

Tell Claude what you're waiting on. This prompt asks for a notice from the migration session:

```text wrap theme={null}
Tell me when the migration session finishes what it's working on
```

Claude subscribes with the `SendMessage` tool's `notify_when_idle` input, either attached to a message it's sending anyway or on its own. On its own, Claude Code subscribes without starting a turn or spending tokens in the watched session, and sends the notice right away if that session is already idle. Attached to a message, Claude Code delivers the message first and sends the notice later.

#### What each session shows

The watched session shows a line saying another process asked to be told when the session is next idle. The asking session shows the notice as a line naming the watched session. The line can include the time that session's turn finished and a one-line status from that turn. If the asking session is idle, Claude Code starts a new turn with the notice.

#### Limits

If no notice arrives within 12 hours, Claude Code drops the subscription and tells Claude, so it doesn't keep waiting.

Each side's [inbound controls](<#control-inbound-messages>) apply to a notice like a message:

* **`refuse` on either side**: nothing arrives. The watched session drops the request without recording or answering it, so the subscription expires unanswered after 12 hours, and an asking session with `refuse` never subscribes.
* **`hold` on either side**: the notice arrives with less. The watched session leaves the one-line status out, and the asking session shows the notice in your transcript without delivering it to Claude.

Only the Claude in your main conversation can subscribe, and only to your sessions on this machine. When Claude asks for a notice from any other target, such as a teammate, a subagent, or a session beyond this machine, Claude Code refuses the whole call, including any message attached to it.

### See which sessions Claude can reach

Claude finds a message's target on its own, so you don't need to run anything before asking it to send. To see for yourself which sessions Claude can reach, run the `/list-agents` command. The first line, when present, is this session's own name, the one your other sessions use to message it. The rows below it are the sessions Claude can reach:

* **Subagents**: agents running inside the current session.
* **Teammates**: this session's own [agent team](<https://code.claude.com/docs/en/agent-teams>) teammates.
* **Your other local sessions**: Claude Code sessions running on the same machine, including [background sessions](<https://code.claude.com/docs/en/agent-view>). A session appears only when it binds an [inbox socket](<#the-sessions-inbox-socket>).
* **Your [cloud sessions](<https://code.claude.com/docs/en/claude-code-on-the-web>)**: shown while this session is connected to [Remote Control](<https://code.claude.com/docs/en/remote-control>).
* **Your Remote Control sessions on other machines**: shown while this session is connected to [Remote Control](<https://code.claude.com/docs/en/remote-control>), and labeled `Remote Control`. Claude Code shows `offline` as the status of a session whose Remote Control connection has dropped.

While this session is connected to [Remote Control](<https://code.claude.com/docs/en/remote-control>), Claude Code withholds some details of your local sessions from the `/list-agents` output, without changing what Claude itself sees when it looks for a session to message:

* **Working directories**: it leaves out each local session's working directory.
* **Session names**: it leaves out any session name it can't attribute to a person, so a row left with no name reads `(unnamed session)`.
* **The first line**: it leaves out the line with this session's own name unless you typed that name at this terminal, with `--name` or with `/rename` and the name, since you launched or last resumed the session.

When the output lists anything, it ends with a note saying details were withheld. Running `/rename` followed by an unused name at a session's own keyboard gives that session a name that appears in the output.

A session answers to the name you set with the [`/rename`](<https://code.claude.com/docs/en/commands>) command or the [`--name`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) flag. When you don't set one, Claude Code names the session itself. For an interactive session, that is the name shown in [listings of running sessions](<https://code.claude.com/docs/en/sessions#name-your-sessions>).

When you rename a session, or start or resume an interactive one, with a name another live session on this machine already uses, Claude Code leaves the name with the session that already has it and [renames yours to a variant](<https://code.claude.com/docs/en/sessions#name-your-sessions>). Sessions can still share a name, for example when one of them runs an earlier version of Claude Code or the shared name is one Claude Code generated. Unless this session is connected to Remote Control, Claude Code shows each local session's working directory in the `/list-agents` output, so you can tell same-named sessions apart when they run in different directories. Claude addresses the message in one of two ways, depending on how many live sessions answer to the name:

* **One session answers to the name**: Claude Code delivers the message on the name alone.
* **Several sessions share the name, or Claude Code couldn't check everywhere your sessions run**: Claude adds a short identifier to each row of its listing and uses the identifier in the address.

### Message sessions on other machines

How a message travels, and whether it passes through Anthropic servers, depends on where the target session runs:

| Where the other session runs | How the message travels |
| :- | :- |
| On this machine | Over a per-session socket on macOS and Linux, or a per-session named pipe on native Windows, never through Anthropic servers |
| On another of your machines | Through Anthropic servers, arriving over that machine's [Remote Control](<https://code.claude.com/docs/en/remote-control>) connection |
| In the [cloud](<https://code.claude.com/docs/en/claude-code-on-the-web>) | Through Anthropic servers, straight to the cloud session |

Starting a conversation with a session on another of your machines requires Claude Code v2.1.225 or later and a target that [appears in the listing](<#see-which-sessions-claude-can-reach>).

A session's row in [the listing](<#see-which-sessions-claude-can-reach>) can show a condition that changes what happens when Claude messages that session:

* **`offline`**: that session's Remote Control connection has dropped. The message goes through, but arrives only after that session's machine reconnects.
* **`can't receive cross-session messages (off in that session)`**: messaging [isn't available](<#availability>) in that session, or its [`crossSessionInbound`](<https://code.claude.com/docs/en/settings-reference#crosssessioninbound>) value is `refuse`. Claude Code refuses a message to that session before the message leaves this machine. The result under Claude's `SendMessage` call begins `Not sent` and names the reason. Once the cause is fixed in that session, a later listing no longer shows this condition, and Claude can message that session.

A session inside a container and a session on the host can't reach each other. Two sessions inside the same container can still message each other, including on a [self-hosted runner](<https://code.claude.com/docs/en/self-hosted-environments>). A session inside WSL 2 and a native Windows session on the same computer can't reach each other either.

If this session isn't connected to Remote Control when Claude sends to a session beyond this machine, the message still goes through, but without a [reply address](<#what-a-message-looks-like>), so the receiving Claude can't answer it.

To require your approval before any message goes beyond this machine, set [`isolatePeerMachines`](<#require-approval-for-cross-machine-messages>).

## How a session treats an incoming message

When session A messages session B, Claude Code tells B's Claude that the message came from another session, not from you, and limits what the message can do:

* **It can't approve anything**: a message from another session never counts as your consent, so it can't answer a pending permission prompt on your behalf.
* **It can't change configuration**: Claude Code instructs the receiving Claude never to change permission settings, `CLAUDE.md`, or other configuration because another session asked.
* **Commands don't run**: a command in the message's text, such as `/compact`, arrives as plain text. Claude Code never executes it.
* **Permission prompts still fire**: if acting on the message requires a permission the receiving session doesn't have, you see the same prompt you'd see for any other work.

<h3 id="what-a-message-looks-like">
  What a message looks like
</h3>

When a message arrives, Claude Code shows it in the conversation as a dim one-line preview, and the preview line stays in the conversation afterward. The preview carries the sender's name and the first line of the message, cut with `…` when it's long, such as `› Message from @api-worker: Schema migration finished (ctrl+o to expand)`.

Any of these shows you the full text:

* Press `Ctrl+O` to open the [transcript viewer](<https://code.claude.com/docs/en/interactive-mode#transcript-viewer>) and read the full text under the sender's session name.
* In [fullscreen rendering](<https://code.claude.com/docs/en/fullscreen#use-the-mouse>), click a preview line that leaves part of the message out to expand it in place.
* In a session started with [`--verbose`](<https://code.claude.com/docs/en/cli-reference#cli-flags>), Claude Code shows the full text instead of the preview.

The preview shortens only what you see. Whether or not you expand it, Claude reads the full message.

Claude receives the message with the sender's name and a reply address, except for a [one-way cross-machine message](<#message-sessions-on-other-machines>), which carries no reply address.

This example is a message one Claude wrote to another, as its full text reads when you expand it:

```text wrap theme={null}
Schema migration finished
The new column is tenant_id, and rebasing on main is safe now.
```

### Control inbound messages

Set [`crossSessionInbound`](<https://code.claude.com/docs/en/settings-reference#crosssessioninbound>) to choose what a session does with messages arriving from your other sessions:

| Value | Behavior |
| :- | :- |
| `accept` | Claude Code delivers each message to Claude |
| `hold` | Claude Code shows a notice for each message and doesn't deliver it. If an `accept` later applies, per the [precedence rules](<https://code.claude.com/docs/en/settings-reference#crosssessioninbound>), Claude Code releases the held messages |
| `refuse` | Claude Code drops each message without delivering it |

Beyond editing a settings file, you can select the value in the `/config` row **Messages from your other sessions**. Claude Code writes the value you select to your user settings. The row requires Claude Code v2.1.232 or later and doesn't appear while managed settings or the `--settings` flag sets the key, since a user-settings value wouldn't apply then. Claude Code rejects the `/config crossSessionInbound=value` shorthand for this key.

To see which value applies, follow the `crossSessionInbound` precedence rules in the [settings reference](<https://code.claude.com/docs/en/settings-reference#crosssessioninbound>).

When no value applies, Claude Code decides per message from the two sessions' permission modes. It groups sessions that [bypass permission prompts](<https://code.claude.com/docs/en/permission-modes#skip-all-checks-with-bypasspermissions-mode>) into one class, and every other session into the other. Plan mode counts as bypassing in interactive terminal sessions with bypass permissions available, and [auto](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>), `acceptEdits`, and `dontAsk` count as prompting:

* **The receiving session prompts for permissions**: Claude Code delivers each message. It holds one for your approval only when the sending session identifies itself as bypassing permission prompts.
* **The receiving session bypasses permission prompts**: Claude Code holds each message for your approval. It delivers one only when the sending session identifies itself as also bypassing.

When the default holds a message in an interactive terminal session, Claude Code opens an approval dialog there. The dialog shows the sender and a preview:

* **Approve** delivers that one message to Claude.
* **Deny**, or dismissing the dialog, drops it.
* When the dialog stays unanswered past the [`dialogExpiry`](<https://code.claude.com/docs/en/settings-reference#dialogexpiry>) deadline, Claude Code closes it and drops the message. The deadline defaults to five minutes.
* While no terminal is attached to a [background session](<https://code.claude.com/docs/en/agent-view>), Claude Code leaves the dialog open past the deadline. After you attach, if the dialog stays unanswered for a full deadline period, Claude Code closes it and drops the message.
* If this session's permission-mode class changes while messages are held, Claude Code re-applies the inbound rules, delivers the messages they now accept, and shows a notice.

A session in the VS Code extension or the Desktop app can't show the dialog. Claude Code keeps a held message there until the same deadline, as [Non-interactive sessions](<#non-interactive-sessions>) describes.

Claude Code holds at most 100 messages, and past that drops the oldest.

### Non-interactive sessions

Claude Code binds an inbox socket for a [`claude -p`](<https://code.claude.com/docs/en/headless>) session like an interactive one, so a long-running `-p` worker can receive messages and appears in the listing. When you start a session in [bare mode](<https://code.claude.com/docs/en/headless#start-faster-with-bare-mode>), Claude Code doesn't bind the socket, so that session can't receive messages and doesn't appear in the agent list.

A `-p` session can't show the approval dialog. When the [inbound default](<#control-inbound-messages>) holds a message there, Claude Code keeps it for the same [`dialogExpiry`](<https://code.claude.com/docs/en/settings-reference#dialogexpiry>) deadline the dialog uses, five minutes by default:

* **Before the deadline**: if a mode or settings change allows the message, Claude Code delivers it.
* **Past the deadline**: Claude Code drops the message and reports it as expired to a sender it can reach.

Set `dialogExpiry` to `"never"` to keep default-held messages until the session ends. A message held by an explicit `hold` setting doesn't expire; Claude Code delivers it only when an `accept` later applies.

To let a `-p` worker take messages unattended, start it with `crossSessionInbound` set to `accept` in its `--settings` value. An `accept` in your user settings also works but applies to every session you run.

<h3 id="the-sessions-inbox-socket">
  The session's inbox socket
</h3>

Read this section when a session you expect isn't in the agent list, when you want a script or hook to post into a session, or when a sandboxed command can't reach the socket.

Claude Code binds an inbox socket for each session with cross-session messaging enabled, where other sessions on the machine deliver messages. The socket is a Unix domain socket on macOS and Linux, including Linux inside WSL 2, and a named pipe on native Windows. For which session kinds bind one, see [Non-interactive sessions](<#non-interactive-sessions>).

You can find the socket's path in two places:

* `/status` shows it in the `Peer address` row. The path is prefixed with `uds:`.
* Claude Code exports it to [hooks](<https://code.claude.com/docs/en/hooks>) and Bash commands as the [`CLAUDE_CODE_MESSAGING_SOCKET`](<https://code.claude.com/docs/en/env-vars#variables>) environment variable:
  * In a session that starts with messaging on, Claude Code exports the variable before any hook runs, including `SessionStart`.

On macOS and Linux, Claude Code restricts the socket to your operating-system user. On native Windows, it instead requires each connection to authenticate first with a key that only your operating-system user can read. Either way, on a shared machine another user's sessions can't deliver to it.

On macOS and Linux, Claude Code also refuses to create the socket in a directory it can't accept, for example one that another user owns, and uses a private per-user directory, `/tmp/cc-socks-<uid>`, instead. When it can't accept any directory, the session runs without an inbox: Claude Code shows a notice, `/status` shows `unavailable` and the reason in its `Peer address` row, and the [`--debug`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) log records the full refusal.

Alongside the socket's path, Claude Code exports a per-session token as [`CLAUDE_CODE_MESSAGING_TOKEN`](<https://code.claude.com/docs/en/env-vars#variables>). A script posting to its own session's socket can send `{"type":"auth","token":"<token>"}` as the first line of its connection, where `<token>` is the value of `CLAUDE_CODE_MESSAGING_TOKEN`. Whether Claude Code requires the line depends on the platform:

* **macOS and Linux, including WSL 2**: the line is optional. Claude Code accepts a connection with or without it.
* **Native Windows**: the line is required. Claude Code closes any connection whose first line isn't a valid auth line and delivers nothing from that connection.

Open the connection only when the message you're posting is ready. Claude Code closes a connection that hasn't sent a complete line within 30 seconds, so capture a slow command's output first and then open the connection to send it.

<span id="own-child-messages" />Claude Code runs messages arriving on the socket through the same [inbound controls](<#control-inbound-messages>) as any other peer message, with one exception and one prerequisite:

* **Own-child messages**: when no `crossSessionInbound` value applies, Claude Code delivers a message it verifies came from the session's own child processes, such as a hook or Bash command posting back to its own session's socket.
  * On Linux, including inside WSL 2, Claude Code can verify by process evidence even for a child that has already exited. On macOS it can verify that way only while the posting process is still running, and in a container where Claude Code runs as process ID 1 it has no process evidence at all. On native Windows it also has none.
  * On macOS after the posting process has exited and in containers where Claude Code runs as process ID 1, that process evidence is missing, and Claude Code instead verifies a child that sent the session's exported [`CLAUDE_CODE_MESSAGING_TOKEN`](<https://code.claude.com/docs/en/env-vars#variables>) in the auth line that opened its connection. On native Windows, that token is the only way Claude Code verifies an own-child message.
  * When Claude Code can verify neither way, it treats the message like any other that asserts no permission class, so a session that bypasses permission prompts holds it for your approval.
* **Sandboxed sessions**: control whether a Bash command can reach the socket from inside the [sandbox](<https://code.claude.com/docs/en/sandboxing>) with the sandbox's Unix-socket settings, [`sandbox.network.allowAllUnixSockets` and `sandbox.network.allowUnixSockets`](<https://code.claude.com/docs/en/settings-reference#sandbox-settings>).

## Restrict cross-session messaging

Beyond the per-message defaults, you can narrow messaging in two ways. Require your approval before any message leaves the machine, or turn messaging off for a session or an organization.

### Require approval for cross-machine messages

Set [`isolatePeerMachines`](<https://code.claude.com/docs/en/settings-reference#isolatepeermachines>) to `true` to require your explicit approval before any `SendMessage` reaches a session beyond this machine:

```json theme={null}
{
  "isolatePeerMachines": true
}
```

With this set, Claude Code asks for your approval before Claude's message to a session beyond this machine leaves, even in `bypassPermissions` mode, which skips ordinary permission prompts. A `true` from any settings scope applies, so a checked-in project file can turn the requirement on but not off. Claude Code doesn't prompt for messages between sessions on the same machine.

### Turn off cross-session messaging

Receiving and sending are separate controls, so turn off whichever direction you need, or both. Use `crossSessionInbound` for messages that arrive, and permission rules for what Claude here can send or list:

* **Stop receiving**: set `crossSessionInbound` to `refuse`, and Claude Code drops inbound peer messages without delivering them. From project or local settings, `refuse` applies over every other source, and from your user settings it applies unless managed settings or the `--settings` flag set a value.
* **Stop sending and listing**: add [permission deny rules](<https://code.claude.com/docs/en/permissions#tool-specific-permission-rules>) naming `SendMessage` and `ListAgents`. Both take the bare tool name with no specifier.

Administrators can turn both sides off for an organization in [managed settings](<https://code.claude.com/docs/en/managed-settings>), combining the deny rules with the `refuse`:

```json theme={null}
{
  "permissions": {
    "deny": ["SendMessage", "ListAgents"]
  },
  "crossSessionInbound": "refuse"
}
```

With this in place, Claude Code still binds each session's inbox socket, but drops every message that arrives on it without delivering anything to Claude. Denying `SendMessage` also removes messaging to subagents and agent-team teammates, since the same tool serves both. A refusing session shows no visible change, in its own `/status` or in the listings of other sessions on the same machine, so to confirm it, check the settings files that apply to that session rather than its status.

## Availability

Cross-session messaging requires Claude Code v2.1.224 or later on macOS, Linux, and WSL 2, and v2.1.234 or later on native Windows. Availability, and which sessions Claude can message, also depend on your operating system, provider, and configuration:

* **Operating system**: available on macOS, Windows, and Linux, including Linux inside WSL 2.

* **Sessions on this machine**: available on every provider, including Amazon Bedrock, Claude Platform on AWS, Google Cloud's Agent Platform, and Microsoft Foundry, and in sessions that run with [feature-flag fetching](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>) off. On those providers, and with flag fetching off, same-machine messaging requires Claude Code v2.1.248 or later. Claude Code delivers these messages over a [per-session socket on your machine](<#the-sessions-inbox-socket>), never through Anthropic servers.

  To stop a session from receiving them, set [`crossSessionInbound`](<#turn-off-cross-session-messaging>) to `refuse`.

* **Sessions beyond this machine**: Claude finds your [cloud sessions](<https://code.claude.com/docs/en/claude-code-on-the-web>) and your sessions on other machines from a session that is connected to Remote Control, which needs a claude.ai sign-in as this session's active authentication and the other [Remote Control requirements](<https://code.claude.com/docs/en/remote-control#requirements>). Claude can't find those sessions with an API key or on Amazon Bedrock, Claude Platform on AWS, Google Cloud's Agent Platform, and Microsoft Foundry.

To check a session, type `/list-agents`, also available as `/peers`. The result separates a session that doesn't have the feature from a session where something narrower blocked a message, such as a missing `SendMessage` tool or a refused send:

* **`/list-agents` isn't recognized**: the session doesn't have cross-session messaging. Work through the requirements above, starting with `claude --version` for the version requirement.
* **`/list-agents` works but a send didn't arrive**: messaging is on, and something narrower applies:
  * **Deny rules**: a [permission deny rule](<#turn-off-cross-session-messaging>) removes the `SendMessage` and `ListAgents` tools.
  * **Inbound controls**: the [receiving session's inbound controls](<#control-inbound-messages>) can hold or drop what you send it.
  * **Cloud session missing**: a cloud session appears only while this session is connected to [Remote Control](<https://code.claude.com/docs/en/remote-control>).
  * **Other-machine session missing**: a session on another of your machines appears only when it runs with [Remote Control](<https://code.claude.com/docs/en/remote-control>) and this session is connected as well.
  * **Other-machine session `offline`**: a message to a session listed as `offline` goes through, but [arrives only after that session's machine reconnects](<#message-sessions-on-other-machines>).
  * **Cloud or other-machine session `can't receive cross-session messages`**: a message to a session listed with this condition [doesn't leave this machine](<#message-sessions-on-other-machines>), and the result under `SendMessage` begins `Not sent`.
  * **Older cloud or other-machine session missing**: Claude Code reads those session lists newest first and stops after a bounded number of pages, so Claude can't message a session that fell past them by name.

In a session with messaging, `/status` also shows a `Peer address` row with the session's own inbox address, or `unavailable` and the reason when Claude Code [couldn't set up an inbox](<#the-sessions-inbox-socket>).

## Limitations

The limits here are properties of the messaging channel itself and apply wherever the feature runs. For platform and provider gaps, see [Availability](<#availability>) instead.

* **Plain text only**: Claude sends only plain text across sessions. Structured [agent team](<https://code.claude.com/docs/en/agent-teams>) protocol messages stay within a team.
* **Same-machine message size is capped**: Claude Code refuses a message to a session on this machine once its serialized form passes about a million characters. The refusal [names the exact sizes](<https://code.claude.com/docs/en/errors#message-too-large-for-cross-session-delivery>). Nothing reaches the receiving session.
* **Rapid bursts to one session are refused at the sender**: once a rapid burst of messages to a session on this machine reaches what that session's inbox accepts, Claude Code refuses further sends in the sending session. The [refusal names the burst](<https://code.claude.com/docs/en/errors#too-many-messages-to-this-session-just-now>) and tells Claude to batch the rest into one message or wait.
* **Message loops are throttled**: in the receiving session, Claude Code rate-limits repeated messages per sender, drops identical repeats arriving within a short window, and queues at most 50 accepted messages for Claude to read. A message loop between two sessions therefore stops on its own.

## Related resources

* [Subagents](<https://code.claude.com/docs/en/sub-agents#resume-subagents>) and [agent teams](<https://code.claude.com/docs/en/agent-teams#messages-between-agents>): messaging within a single session or team
* [Background agents](<https://code.claude.com/docs/en/agent-view>): dispatch and monitor the parallel sessions you might message
* [Remote Control](<https://code.claude.com/docs/en/remote-control>): connect this session to reach your sessions on other machines
* [Channels](<https://code.claude.com/docs/en/channels>): push external events, such as CI results or chat messages, into a session
* [Settings](<https://code.claude.com/docs/en/settings-reference#all-settings>): `crossSessionInbound`, `isolatePeerMachines`, and `dialogExpiry`
* [Permission modes](<https://code.claude.com/docs/en/permission-modes>): the modes behind the inbound default's two classes
* [Tools reference](<https://code.claude.com/docs/en/tools-reference>): the `ListAgents` and `SendMessage` rows in the tools table
* [Run agents in parallel](<https://code.claude.com/docs/en/agents>): compare the ways Claude Code runs multiple agents


</details>
