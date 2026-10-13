# 在云端运行 Claude Code

说明通过网页、手机、桌面或终端启动云会话，移动任务和修复拉取请求；涵盖 GitHub 认证、环境配置及本地与云端交接。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-09](<https://code.claude.com/docs/en/claude-code-on-the-web>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/claude-code-on-the-web.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/在云端运行 Claude Code.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 在云端使用Claude Code

> Run Claude Code sessions in the cloud from your browser, phone, Desktop app, or terminal, move them with `--cloud` and `--teleport`, and auto-fix pull requests.



**注释：**

云会话适用于 Pro、Max 和 Team 计划，并且适用于拥有高级席位或 Chat + Claude Code 席位的企业用户。


云会话是在云基础架构而不是您的计算机上运行的 Claude Code 会话。 By default it runs on infrastructure Anthropic manages, or on your organization's [self-hosted environment](<https://code.claude.com/docs/en/self-hosted-environments>) when routed there.关闭笔记本电脑后，会话将继续运行，您可以从任何设备检查或引导它。 It counts toward your plan's usage limits alongside the rest of your Claude and Claude Code usage, and there's no separate charge for the cloud VM.

To let cloud sessions clone your code from GitHub and push branches, connect GitHub with one of the [GitHub connection methods](<#github-身份验证选项>).如果您的存储库位于 GitLab、Bitbucket 或其他主机上，请参阅 [ 平台限制 ](<#局限性>) 了解有效方法。

您可以从以下任意界面启动云会话：

* **浏览器**：[claude.ai/code](<https://claude.ai/code>)，在网络上也称为Claude Code
* **手机**: 的 **代码** 选项卡中的 [Claude 应用程序](<https://code.claude.com/docs/en/mobile>)
* **Desktop app**: select **Cloud** instead of **Local** when you [start a session](<https://code.claude.com/docs/en/desktop#run-long-running-tasks-in-the-cloud>)
* **终端**：[`claude --cloud`](<#from-terminal-to-cloud>)
* **例程**：[计划和触发的运行](<https://code.claude.com/docs/en/routines>)每次都作为云会话运行

Once you're set up, use this page to move work between your terminal and the cloud, manage and share sessions, turn on auto-fix for pull requests, and troubleshoot.



**注释：**

这些案例在其他页面中有介绍：

  * **Starting your first cloud session**: [Get started with cloud sessions](<https://code.claude.com/docs/en/web-quickstart>) connects GitHub and walks through a task in the browser
  * **Many cloud sessions for one body of work**: a [project](<https://code.claude.com/docs/en/claude-projects>) has Claude start and keep track of them for you
  * **Steering a local session from another device**: sessions in your terminal, IDE, or the Desktop app with **Local** selected run on your machine, and [Remote Control](<https://code.claude.com/docs/en/remote-control>) lets you通过您的手机或浏览器联系他们



## 云环境

Every cloud session runs in a [cloud environment](<https://code.claude.com/docs/en/cloud-environments>), the saved configuration that controls network access, environment variables, and setup scripts.

* **Your first environment**: if you don't have one yet, onboarding sets up a **Default** environment with [**Trusted** network access](<https://code.claude.com/docs/en/cloud-environments#access-levels>), either by creating为您提供或要求您创建它。请参阅 [默认环境 ](<https://code.claude.com/docs/en/cloud-environments#the-default-environment>)，了解您的计划中会发生哪些情况
* **Which environment a session uses**: see [The Default environment](<https://code.claude.com/docs/en/cloud-environments#the-default-environment>) for how sessions choose an environment when you have more than one
* **更改启动时可以访问或运行的会话**：请参阅[配置云环境](<https://code.claude.com/docs/en/cloud-environments>)
* **无任何配置安装的内容**：参见[安装的工具](<https://code.claude.com/docs/en/cloud-environments#installed-tools>)

## GitHub 身份验证选项

云会话需要访问您的 GitHub 存储库才能克隆代码并推送分支。您可以通过两种方式授予访问权限：

|方法|您如何连接|存储库会话可以达到|最适合|
| :- | :- | :- | :- |
|**GitHub 应用程序**|在 [ 网页登录 ](<https://code.claude.com/docs/en/web-quickstart>) 期间授权 Claude GitHub 应用程序|安装了 Claude GitHub 应用程序的任何公共存储库和私有存储库|浏览器入门；想要 [ 自动修复 ](<#自动修复拉取请求>) 的团队|
| **`/web-setup`** |在终端中运行 `/web-setup` 将本地 `gh` CLI 令牌发送到您的 Claude 帐户|您的 `gh` 令牌可以访问的任何存储库，无论是否安装 Claude GitHub 应用程序|已经使用`gh`的个人开发者|

这些功能取决于存储库上安装的 Claude GitHub 应用程序：

* **自动修复**：在存储库上安装Claude GitHub应用程序还会为其中的拉取请求启用[自动修复](<#自动修复拉取请求>)
* **项目**：[项目](<https://code.claude.com/docs/en/claude-projects>)中的线程需要在它们克隆的每个存储库上安装Claude GitHub应用程序，无论您使用哪种方法连接。请参见[设置GitHub访问权限](<https://code.claude.com/docs/en/claude-projects#set-up-github-access>)

在 Anthropic 托管环境中，您的 GitHub 凭据在 Anthropic 的服务器上保持加密状态，并且永远不会进入会话的 VM。来自 VM 的 GitHub 操作通过 [GitHub 代理 ](<https://code.claude.com/docs/en/cloud-environments#github-proxy>)，该代理将凭证附加到服务器端。

请参阅 [从终端连接 ](<https://code.claude.com/docs/en/web-quickstart#connect-from-your-terminal>) 了解 `/web-setup` 演练，包括 `/web-setup` 存储的内容以及如何删除它。



**注释：**

启用了 [ 零数据保留 ](<https://code.claude.com/docs/en/zero-data-retention>) 或应用了 [HIPAA 配置 ](<https://code.claude.com/docs/en/hipaa-setup>) 的组织无法使用 `/web-setup` 或其他云会话功能。


### 团队和企业快速设置

快速设置是一种组织设置，可删除成员 GitHub 和环境设置中的步骤。在团队和企业计划中，它默认处于关闭状态。

以下是启用后会员会发生的变化：

* **`/web-setup`**：会员可以将GitHub与`/web-setup`连接。当设置关闭时，该命令被隐藏
* **GitHub 应用程序提示**：浏览器入门会跳过 Claude GitHub 应用程序安装提示
* **第一个环境**：浏览器入门为成员创建[**默认**环境](<https://code.claude.com/docs/en/cloud-environments#the-default-environment>)，而不是显示环境表单

安 [业主](<https://code.claude.com/docs/en/server-managed-settings#access-control>) 将其打开 **快速设置** 切换于 [**组织设置 > Claude Code**](<https://claude.ai/admin-settings/claude-code>).

## 在终端和云端之间移动任务

这些工作流程需要 [Claude Code CLI](<https://code.claude.com/docs/en/quickstart>) 登录到同一 claude.ai 帐户。您可以从终端启动新的云会话，或将云会话拉入终端以在本地继续。即使您关闭笔记本电脑，云会话仍然存在，并且您可以从任何地方（包括 Claude 移动应用程序）监控它们。



**注释：**

从 CLI 开始，会话切换是单向的：您可以使用 `--teleport` 将云会话拉入终端，但无法将现有终端会话推送到云。带有任务描述的 `--cloud` 标志为您当前的存储库创建一个新的云会话；使用 `-p` 和会话 ID 或 claude.ai/code URL，它会 [ 将消息排队到现有会话 ](<https://code.claude.com/docs/en/claude-code-on-the-web#send-follow-ups-from-the-cli>) 中。 [桌面应用程序](<https://code.claude.com/docs/en/desktop#continue-in-another-surface>)可以将其代码选项卡中的本地会话从其**在**菜单中打开发送到云。


<span id="from-terminal-to-cloud" />

### 从您的终端启动云会话

使用 `--cloud` 标志从命令行启动云会话：

```bash theme={null}
claude --cloud "Fix the authentication bug in src/auth/login.ts"
```

这将在 claude.ai 上创建一个新的云会话。云 VM 会在当前分支克隆当前目录的 GitHub 远程目录，而不是本地签出，因此如果您有本地提交，请先推送。对于 Claude Code 上传本地存储库而不是克隆的情况，请参阅 [发送不带 GitHub](<#发送不带-github-的本地存储库>) 的本地存储库。

`--cloud` 一次使用一个存储库。该任务在云中运行，而您继续在本地工作。旧的 `--remote` 拼写仍然可以用作 `--cloud` 的已弃用别名。

当云容器启动时，CLI 会显示设置步骤的实时清单，例如克隆存储库并运行 [ 设置脚本 ](<https://code.claude.com/docs/en/cloud-environments#setup-scripts>)。它将您在配置期间键入的消息排队，并在会话准备就绪后发送它们。



**注释：**

`--cloud` 创建云会话。 `--remote-control` 不相关：它允许您从 claude.ai 或 Claude 应用程序监控和引导本地 CLI 会话。参见[远程控制](<https://code.claude.com/docs/en/remote-control>)。


在 claude.ai 或 Claude 移动应用程序上打开会话以检查进度或直接互动。从那里您可以像在任何其他对话中一样引导 Claude、提供反馈或回答问题。

如果 Claude 提出问题并且会话处于空闲状态，您回来时仍然可以回答，直到 [ 环境到期 ](<#环境已过期>)，并且会话从您的回答继续。

#### 云任务提示

**在本地规划，在云端执行**：对于复杂的任务，在计划模式下启动Claude以协作处理该方法，然后将工作发送到云端：

```bash theme={null}
claude --permission-mode plan
```

在计划模式下，Claude 读取文件、运行命令进行探索并提出计划，而无需编辑源代码。一旦您满意，请将计划保存到存储库、提交并推送，以便云 VM 可以克隆它。然后启动云会话进行自主执行：

```bash theme={null}
claude --cloud "Execute the migration plan in docs/migration-plan.md"
```

**并行运行任务**：每个`--cloud`命令都会创建自己独立运行的云会话。您可以启动多个任务，它们将在单独的会话中同时运行：

```bash theme={null}
claude --cloud "Fix the flaky test in auth.spec.ts"
claude --cloud "Update the API documentation"
claude --cloud "Refactor the logger to use structured output"
```

当会话完成时，您可以创建一个 PR 来自 claude.ai/code 或 [传送](<#from-cloud-to-terminal>) 将会话发送到您的终端以继续工作。

#### 发送不带 GitHub 的本地存储库

当您从没有 git Remote 的存储库或未安装 Claude GitHub 应用程序的 github.com 存储库运行 `claude --cloud` 时，Claude Code 会捆绑您的本地存储库并将其直接上传到云会话。即使您将 GitHub 与 `/web-setup` 连接，这也适用。

对于完整克隆，该捆绑包包含所有分支的存储库历史记录，以及对跟踪文件的未提交更改。

敏感文件中未提交的更改会发生什么情况取决于您的平台：

* **macOS、Linux 和 WSL**：Claude Code 将对名为凭证或密钥等名称的文件留下未提交的更改，不上传。这包括 `.env` 文件、Terraform `*.tfvars` 文件以及关键文件，例如 `id_rsa` 和 `*.pem`。它还遗漏了对 git 过滤器（例如 Git LFS）管理的文件的未提交更改。 `Left on this machine:` 通知会命名遗漏的文件，并且会话以每个文件的已提交版本开始，如果没有提交，则不包含该文件。
* **本机Windows**：对跟踪文件上传的未提交更改，无论文件的名称如何。在启动之前，在云会话中隐藏或恢复您不需要的编辑。

要上传包，即使 Claude Code 会从远程克隆，请设置 `CCR_FORCE_BUNDLE=1`：

```bash theme={null}
CCR_FORCE_BUNDLE=1 claude --cloud "Run the test suite and fix any failures"
```

捆绑存储库必须满足以下限制：

* 该目录必须是至少有一次提交的 git 存储库
* 捆绑的存储库必须位于 100 MB 下。较大的存储库会回退到仅捆绑当前分支，然后到工作树的单个压缩快照，如果快照仍然太大，则会失败
* 不包括未跟踪的文件；对您希望云会话查看的文件运行 `git add`
* 在 macOS、Linux 和 WSL 上，Claude Code 无法遵循影响适用于文件的属性规则的 git 设置（例如包含的配置文件中设置的 `core.attributesFile`）时会拒绝上传。 [拒绝消息](<https://code.claude.com/docs/en/errors#the-repository-upload-cant-follow-a-git-setting>)命名设置和修复
* 仅当您的 [GitHub 连接 ](<#github-身份验证选项>) 具有对该存储库的推送访问权限时，从捆绑包创建的会话才能推送回 GitHub 远程

在 macOS、Linux 和 WSL 上，上传还需要 git 2.31 或更高版本及其支持的结帐布局，而在本机 Windows Claude Code 上上传无需任何检查。当结帐不满足这些要求时，Claude Code 不会启动会话。它打印包含 `Not uploading this working tree:` 的错误，指出原因并说明要更改的内容。以下是常见原因：

* **较旧的 git**：安装的 git 早于 2.31。更新 git，然后重试。
* **上传不支持的签出布局**：您在子模块内开始，在使用`git clone --separate-git-dir`、`--shared`或`--reference`制作的克隆中、在设置了`core.worktree`的签出中或在将其引用保存在的存储库中开始可重写格式。从使用普通 `git clone` 制作的克隆的主要检查开始。
* **具有稀疏签出的链接工作树**：`git sparse-checkout`将设置写入工作树自己的`config.worktree`文件，上传不接受该文件，因此不会上传具有这些设置的工作树，也不会上传使用Claude Code创建的工作树[`worktree.sparsePaths`](<https://code.claude.com/docs/en/settings-reference#worktree-sparsepaths>)。相反，从存储库的主要签出开始。
* **Git 配置保存在工作树中**：您的 git 配置包含位于结账内部的文件，例如指向存储库的 `include.path` 条目。将该文件移到工作树之外或删除包含文件，然后重试。

在 macOS、Linux 和 WSL 上，使用 `git clone --filter` 制作的部分克隆会作为其工作树的快照上传，没有历史记录，只要克隆在本地保存每个跟踪文件即可。

对于 `claude --cloud`，如果存储库位于 GitHub 上，您可以避免上传及其要求：推送您的分支，在存储库上安装 Claude GitHub 应用程序，然后再次启动会话，以便它从 GitHub 克隆。

### 从 CLI 发送后续信息

云会话运行后，无论它在何处执行，请从您使用 `claude auth login` 登录的任何计算机上的 `claude` CLI 向其发送后续消息。 CLI 使用您的 Anthropic 帐户凭据进行身份验证，并且不发送本地会话状态，因此不需要从启动会话的计算机运行该命令。

该命令发布一条消息并退出：

```bash theme={null}
claude -p "your message" --cloud <session-id>
```

CLI 将消息排队到会话中并退出而不等待回复。使用它来引导长时间运行的会话，在当前步骤尚未完成时对下一步进行排队，或者从 [CI 脚本 ](<https://code.claude.com/docs/en/self-hosted-environments-testing#run-the-test-loop>) 发送后续操作。您还可以通过标准输入传输消息，而不是将其作为参数传递：`echo "your message" | claude -p --cloud <session-id>`。

对于 `<session-id>`，传递裸露的 ID，例如 `session_...` 或 `cse_...`，或会话的 `claude.ai/code/<id>` URL，带或不带方案或查询字符串。在 claude.ai/code. 的会话列表中找到 ID



**注释：**

`--cloud`需要Anthropic帐户。当为 Amazon Bedrock、Google 云的 Agent 平台或其他第三方提供商配置 Claude Code 时，它不可用。仅通过 `ANTHROPIC_BASE_URL` 配置的 [LLM 网关 ](<https://code.claude.com/docs/en/llm-gateway>) 不算作此检查的第三方提供商，但您仍然需要使用 `claude auth login` 登录。还必须启用您组织的 `allow_remote_sessions` 策略。所有者可以在 claude.ai/admin-settings/claude-code. 的 Claude Code 管理设置中将其打开


#### 输出

成功后，该命令将打印会话 ID 以及用于查看会话的链接：

```
Sent to cloud session.
Session ID: session_01DiUkqY2kzbUbDmW1w96rfi
View: https://claude.ai/code/session_01DiUkqY2kzbUbDmW1w96rfi?from=cli&m=0
```

传递 `--output-format json` 以获得机器可读的结果：成功时传递 `{ok, session_id, url}`，发送失败时传递 `{ok: false, session_id, error}`，例如会话丢失或存档时。配置错误（例如不受支持的提供程序或禁用的组织策略）会打印到 stderr，但不带 JSON。 `--output-format stream-json` 不支持 `--cloud <session-id>`。

如果发送失败，请参阅[发送到云会话时出错](<#发送到云会话时出错>)。

<span id="from-cloud-to-terminal" />

### Continue 终端中的云会话

使用以下任意一项将云会话拉入您的终端：

* **使用`--teleport`**：从命令行运行`claude --teleport`以获取交互式会话选择器，或运行`claude --teleport <session-id>`以直接恢复特定会话。如果您有未提交的更改，系统会提示您先隐藏它们。
* **使用`/teleport`**：在现有的CLI会话中，运行`/teleport`或`/tp`打开相同的会话选择器，而无需重新启动Claude Code。
* **从`/tasks`**：运行`/tasks`查看您的后台会话，然后按`t`传送到其中。
* **从 claude.ai/code**：从会话菜单中选择 **在 > 终端中打开** 以复制可粘贴到终端中的命令。
* **从云会话内部**：输入`/teleport`，Claude Code回复该会话的确切`claude --teleport <session-id>`命令，准备从存储库签出运行。在会话环境中需要 Claude Code v2.1.223 或更高版本。

当您传送会话时，Claude 会验证您是否位于正确的存储库中，从云会话中获取并签出分支，并将完整的对话历史记录加载到您的终端中。终端获得自己的会话副本：那里的新工作保留在本地，不会出现在 claude.ai 或 Claude 移动应用程序的云会话中。要在传送后继续通过手机进行转向，请在本地会话中启动 [`/remote-control`](<https://code.claude.com/docs/en/remote-control>)。

`--teleport` 与 `--resume` 不同。 `--resume` 从本机的本地历史记录中重新打开对话，并且不列出云会话； `--teleport` 拉取云会话及其分支。

#### 传送要求

Teleport 在恢复会话之前会检查这些要求。如果不满足任何要求，您将看到错误或提示解决问题。

|要求|详情|
| - | - |
|清理 git 状态|您的工作目录必须没有未提交的更改。如果需要，传送会提示您隐藏更改。|
|正确的存储库|您必须从同一存储库的签出运行 `--teleport`，而不是分支。如果您从不同存储库的签出运行它，Claude Code 会显示一个错误，该错误同时命名会话的存储库和您的签出存储库。如果 Claude Code 无法将您的遥控器解析为主机名，例如 SSH 主机别名（如 `git@work:owner/repo.git`），它会要求您确认，并在遥控器的所有者和存储库名称与会话的存储库匹配时接受签出。|
|有分行|云会话中的分支必须已推送到远程。 Teleport 会自动获取并检查它。|
|同一账户|您必须通过云会话中使用的同一 claude.ai 帐户进行身份验证。|

当 teleport 获取会话的分支时，获取永远不会等待终端中的输入。如果 git 或 ssh 要求输入密码、密钥密码或确认新的 SSH 主机，则提取会失败，并且仅当您的本地克隆已有分支时，签出才有效。对于两种SSH情况，将密钥加载到`ssh-agent`中并先手动运行一次`git fetch`以记录主机。

#### `--teleport` 不可用

Teleport 需要 claude.ai 订阅身份验证。找到与您匹配的案例：

* **您已通过 API 密钥进行身份验证**：运行 `/login` 以使用您的 claude.ai 帐户登录
* **错误命名您的提供商**：第三方提供商无法提供云会话。参见[错误表](<#发送到云会话时出错>)
* **您已通过 claude.ai 登录**：您的组织可能已禁用云会话

## 使用会话

会话显示在侧栏中的 claude.ai/code. 中，您可以在此处查看更改、与团队成员共享、存档已完成的工作或永久删除会话。

### 云会话中的权限模式

您选择一个云会话 [权限模式](<https://code.claude.com/docs/en/permission-modes>) 从 [模式下拉菜单](<https://code.claude.com/docs/en/permission-modes#switch-permission-modes>)，无论是在创建任务时还是在会话运行时。

Claude Code 以执行以下任一操作时所处的权限模式恢复会话：

* 重新打开 Anthropic 托管的 [ 环境已过期 ](<#环境已过期>) 的会话
* 向自托管运行器 [ 在空闲时释放的会话发送消息 ](<https://code.claude.com/docs/en/self-hosted-environments-reference#runner-cli-flags>)

### 审查变更

每个会话都会显示一个差异指示器，其中添加和删除了行，例如 `+42 -18`。选择它可以打开差异视图，在特定行上留下内联注释，并将它们与您的下一条消息一起发送到 Claude。

默认情况下，差异视图将会话的更改与其基本分支进行比较。要与存储库中的任何其他分支进行比较，请选择 **与 ** 比较并选择一个。

Claude Code 根据原始 git blob 内容计算这些差异，因此存储库中配置的差异驱动程序和 `textconv` 过滤器不适用。

这些步骤已在其他地方介绍：

* **完整演练，包括PR创建**：请参阅[审查和迭代](<https://code.claude.com/docs/en/web-quickstart#review-and-iterate>)
* **让Claude监视PR的CI故障并自动审查评论**：请参阅[自动修复拉取请求](<#自动修复拉取请求>)

### 管理上下文

云会话支持生成文本输出的[内置命令](<https://code.claude.com/docs/en/commands>)。仅在终端界面中运行的命令（例如 `/plugin` 或 `/resume`）不可用。在终端中打开选取器或面板的命令在云会话中的行为有所不同：

* **`/model`、`/effort`、`/color` 和 `/rename`**：将值作为参数传递，例如 `/model sonnet`，而不是打开终端选择器或滑块。参数形式需要会话环境中的 Claude Code v2.1.205 或更高版本，并遵循每个命令的 [ 可用性说明 ](<https://code.claude.com/docs/en/commands#all-commands>)。
* **`/fast`**：切换 [快速模式](<https://code.claude.com/docs/en/fast-mode#use-fast-mode-in-cloud-sessions>) 对于快速模式时的会话 [您的帐户上可用](<https://code.claude.com/docs/en/fast-mode#requirements>)。需要 Claude Code v2.1.271 或稍后在会话环境中。
* **`/config`**：在浏览器中的 claude.ai/code 中，打开设置的 Claude Code 部分而不是设置值，并且命令后面的文本（包括 `key=value`）将被忽略。要更改云会话的设置，请在环境中设置 [ 环境变量 ](<https://code.claude.com/docs/en/cloud-environments#set-environment-variables>)，或者在具有一个存储库的会话中，将密钥提交到该存储库的 `.claude/settings.json`。 [云会话中的设置](<https://code.claude.com/docs/en/settings#settings-in-cloud-sessions>)列出每个会话读取的内容。

特别是对于上下文管理：

|命令|在云会话中工作|注释|
| :- | :- | :- |
| `/compact` |是的|总结对话以释放上下文。接受可选的焦点指令，例如 `/compact keep the test output`|
| `/context` |是的|显示上下文窗口中当前的内容|
| `/clear` |否|从侧边栏开始新会话|

当上下文窗口接近容量时，自动压缩会自动运行。云会话自行设置 [`CLAUDE_AUTOCOMPACT_PCT_OVERRIDE`](<https://code.claude.com/docs/en/env-vars>)，因此压缩会在 [ 自动压缩窗口 ](<https://code.claude.com/docs/en/model-config#set-the-auto-compact-window>) 的中途触发，而不是在窗口填满时触发。该值会覆盖您在 [ 环境变量 ](<https://code.claude.com/docs/en/cloud-environments#set-environment-variables>) 中添加的值，因此在触发压缩时添加该变量不会发生变化。

要更改自动压缩窗口，请在环境变量中设置 [`CLAUDE_CODE_AUTO_COMPACT_WINDOW`](<https://code.claude.com/docs/en/env-vars>)，或者在未设置该变量的会话中使用令牌计数运行 [`/autocompact`](<https://code.claude.com/docs/en/commands#all-commands>)。

[子代理](<https://code.claude.com/docs/en/sub-agents>)的工作方式与本地相同。 Claude 可以使用 Agent 工具生成它们，将研究或并行工作转移到单独的上下文窗口中，从而使主要对话变得轻松。系统会自动选取存储库 `.claude/agents/` 中定义的子代理。

[Agent 团队 ](<https://code.claude.com/docs/en/agent-teams>) 默认处于关闭状态，但可以通过将 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 添加到 [ 环境变量 ](<https://code.claude.com/docs/en/cloud-environments#set-environment-variables>) 来启用。

### 取回排队的消息

如果您在 Claude 工作时发送消息，消息将排队直到 Claude 读取它。要取回排队的消息，请单击其上的 ✕。文本返回到消息框，以便您可以编辑它或发送其他内容。

如果 Claude 已阅读该消息，则该消息将保留在对话中。

### 分享会议

要共享会话，请根据以下帐户类型切换其可见性。之后，按原样共享会话链接。收件人打开链接时会看到最新状态，但他们的视图不会实时更新。

#### 从企业或团队帐户共享

企业和团队帐户的共享工作方式如下：

* **可见性选项**：**私人**和**团队**。团队可见性使会议对 claude.ai 组织的其他成员可见
* **存储库访问**：默认启用验证，基于与接收者帐户连接的GitHub帐户
* **您的姓名**：您帐户的显示名称对所有具有访问权限的收件人可见
* **Slack 会话**：Slack 中的 [Claude ](<https://code.claude.com/docs/en/slack>) 会话会自动与团队可见性共享

#### 从 Max 或 Pro 帐户共享

Max 和 Pro 帐户的共享工作方式如下：

* **可见性选项**：**私人**和**公共**。公共可见性使登录到 claude.ai 的任何用户都可以看到该会话
* **存储库访问**：默认情况下未启用验证
* **敏感内容**：在分享之前检查您的会话。会话可能包含来自私有 GitHub 存储库的代码和凭据

要要求收件人具有存储库访问权限，或在共享会话中隐藏您的姓名，请转至 [** 设置 > Claude Code > 共享设置 **](<https://claude.ai/settings/claude-code>)。

### 存档会议

您可以存档会话以保持会话列表井井有条。存档会话在默认会话列表中隐藏，但可以通过筛选存档会话来查看。

要存档会话，请将鼠标悬停在侧栏中的会话上，然后选择存档图标。

### 删除会话

删除会话将永久删除该会话及其数据。此操作无法撤消。您可以通过两种方式删除会话：

* **从侧边栏**：筛选已存档的会话，然后将鼠标悬停在要删除的会话上并选择删除图标
* **从会话菜单**：打开会话，选择会话标题旁边的下拉列表，然后选择**删除**

在删除会话之前，系统会要求您确认。

## 自动修复拉取请求

Claude 可以观看拉取请求并自动响应 CI 失败并审核评论。 Claude 订阅 PR 上的 GitHub 活动，当检查失败或审阅者留下评论时，Claude 会进行调查并在明确的情况下推送修复。



**注释：**

自动修复需要在您的存储库上安装 Claude GitHub 应用程序。如果您尚未安装，请从 [GitHub 应用程序页面 ](<https://github.com/apps/claude>) 安装。


有几种方法可以打开自动修复，具体取决于 PR 的来源以及您使用的设备：

* **在云会话中创建的 PR **：打开 claude.ai/code 处的会话，打开 CI 状态栏，然后选择 **自动修复 **
* **从您的终端**：在PR的分支上运行[`/autofix-pr`](<https://code.claude.com/docs/en/commands>)。 Claude Code 检测到打开的 PR 与 `gh`，生成云会话，并一步打开自动修复
* **从移动应用程序**：告诉Claude自动修复PR，例如“观看此PR并修复任何CI故障或查看评论”
* **任何现有的 PR**：将 PR URL 粘贴到会话中并告诉 Claude 自动修复它

自动修复是针对每个 PR 的切换。要停止监控，请在 claude.ai/code 的会话中打开 CI 状态栏，然后清除 ** 自动修复 ** 开关，或告诉 Claude 停止观看 PR。

### Claude 如何响应 PR 活动

当自动修复处于活动状态时，Claude 会收到 PR 的 GitHub 事件，包括新的审阅意见和 CI 检查失败。对于每个事件，Claude 都会调查并决定如何进行：

* **清除修复**：如果Claude对修复有信心并且与之前的说明不冲突，Claude会进行更改，推送它，并解释会话中所做的事情
* **不明确的请求**：如果审阅者的评论可以通过多种方式解释或涉及具有架构意义的内容，Claude会在采取行动之前询问您
* **重复或无操作事件**：如果事件重复或不需要更改，Claude 在会话中记录它并继续

当基础分支前进并产生合并冲突时，GitHub 不会发出 webhook，因此自动修复无法自行对冲突做出反应。要解决冲突，请打开会话并要求 Claude 重新设置基准。

作为解决问题的一部分，Claude 可能会回复 GitHub 上的评论评论线程。这些回复是使用您的 GitHub 帐户发布的，因此它们显示在您的用户名下，但每个回复都被标记为来自 Claude Code，因此审阅者知道它是由 agent 撰写的，而不是由您直接撰写的。



**注意：**

如果您的存储库使用评论触发的自动化，例如 Atlantis、Terraform Cloud 或在 `issue_comment` 事件上运行的自定义 GitHub 操作，请注意 Claude 可以代表您回复，这可以触发这些工作流程。在启用自动修复之前检查存储库的自动化，并考虑对 PR 注释可以部署基础设施或运行特权操作的存储库禁用自动修复。


## 安全与隔离

每个云会话都通过多个层与您的计算机和其他会话分开：

* **隔离的虚拟机**：每个会话都在隔离的、由Anthropic管理的VM中运行。您的组织路由到 [ 自托管环境 ](<https://code.claude.com/docs/en/self-hosted-environments>) 的会话改为在您自己的基础设施上运行，其中隔离是您的部署的责任
* <span id="default-allowed-domains" />**网络访问控制**：在Anthropic托管环境中，网络访问默认受到限制，可以禁用。请参阅 [网络访问 ](<https://code.claude.com/docs/en/cloud-environments#network-access>) 了解访问级别、[默认允许的域 ](<https://code.claude.com/docs/en/cloud-environments#default-allowed-domains>) 以及不通过白名单的流量。在自托管环境中，您可以在自己的网络边界限制会话出站。在禁用网络访问的情况下运行时，Claude Code 仍然可以与 Anthropic API 通信，这可能允许数据退出 VM。
* **凭据保护**：在Anthropic托管环境中，git凭据和签名密钥保留在沙箱之外，并且代理使用范围凭据代表会话进行身份验证。在自托管环境中，您的部署提供 git 凭据；参见[配置git](<https://code.claude.com/docs/en/self-hosted-environments-deploy#configure-git>)
* **网络机密**：在Pro和Max计划的Anthropic托管环境中，您[添加到云环境](<https://code.claude.com/docs/en/cloud-environments#add-network-secrets>)的密钥以同样的方式保留在沙箱之外，在离开会话后附加到匹配的请求。自托管环境没有网络机密，团队和企业计划还没有网络机密
* **安全分析**：在创建 PR 之前在会话的隔离环境中分析和修改代码

## 故障排除

对于对话中出现的运行时 API 错误，例如 `API Error: 500`、`529 Overloaded`、`429` 或 `Prompt is too long`，请参阅 [错误参考 ](<https://code.claude.com/docs/en/errors>)。这些错误及其修复与 CLI 和桌面应用程序共享。以下各节介绍了特定于云会话的问题。

### 会话创建失败

如果新会话无法以 `Session creation failed` 启动或在配置时停止，Claude Code 无法为会话分配 VM。

* 检查 [status.claude.com](<https://status.claude.com>) 是否有云会话事件
* 一分钟后重试，因为容量是按需配置的
* 通过以下操作确认您的GitHub连接可以到达存储库[连接后没有出现存储库GitHub](<https://code.claude.com/docs/en/web-quickstart#no-repositories-appear-after-connecting-github>)

### 无法获取组织 UUID

`claude --cloud` 和 `claude --teleport` 需要使用 claude.ai 帐户登录。如果您使用 API 密钥进行身份验证，或者您存储的帐户详细信息已过时，您会看到以下内容之一：

* `Unable to get organization UUID`
* ``Cloud sessions need a claude.ai sign-in. Run `claude auth login` (or /login in a local session), then try again.``
* 当您在没有会话 ID 的情况下运行 `claude --teleport` 时，会话选择器中的 `Error loading Claude Code sessions`

在 shell 中运行 [`claude auth login`](<https://code.claude.com/docs/en/cli-reference#cli-commands>) 以使用您的 claude.ai 帐户登录，然后重试该命令。在正在运行的会话中，`/login` 执行相同的操作。如果错误指出的是您的提供商，请参阅[错误表](<#发送到云会话时出错>)：第三方提供商无法提供云会话。

从 v2.1.274 到 v2.1.289，登录消息为 `Claude Code cloud sessions require authentication with a Claude.ai account. API key authentication is not sufficient. Please run /login to authenticate, or check your authentication status with /status.`

### 远程控制会话已过期或访问被拒绝

`--teleport` 通过云会话使用的相同远程控制会话基础设施进行连接，因此身份验证和会话过期错误会通过远程控制措辞出现。您可能会看到`Remote Control session expired`或`Access denied`。连接令牌是短暂的，并且仅限于您的帐户。

* 在本地运行 `/login` 以刷新您的凭据，然后重新连接
* 确认您登录到拥有该会话的同一帐户
* 如果您看到 `Remote Control may not be available for this organization`，则所有者尚未为您的组织启用云会话

### 发送到云会话时出错

这些错误来自于运行 `claude` 和 [`--cloud <session-id>`](<#从-cli-发送后续信息>)，无论是否带有 `-p`。 CLI 在错误前加上 `Error: ` 前缀。失败的传递被包装为 `failed to send message to cloud session <id>: <reason>`。

|留言|这意味着什么|
| - | - |
| `Cloud sessions aren't available with <provider>. They run on Anthropic's infrastructure and require an Anthropic account.` |Claude Code 是为第三方提供商配置的。该消息使用您的配置使用的标签来命名提供程序，例如 `Amazon Bedrock` 或 `Google Vertex AI`。删除该提供商的配置，例如通过取消设置 `CLAUDE_CODE_USE_BEDROCK`，然后使用 Anthropic 帐户 (`claude auth login`) 登录。|
| `Cloud sessions are disabled by your organization's policy. Contact your organization admin to enable them.` |`allow_remote_sessions` 组织策略已关闭。|
| `Couldn't verify your organization's policy for cloud sessions. Check your network connection and try again.` |Claude Code 无法获取您组织的策略，因此它拒绝发送，而不是假设允许云会话。检查您的网络连接并重试。|
| `Attaching to an existing cloud session is not enabled for your account.` |您在没有 `-p` 的情况下运行了 `--cloud <session-id>`。使用`claude -p "your message" --cloud <session-id>`发送消息。|
| `Session not found: <id>` |ID 或 URL 与您可以访问的会话不匹配。将其与会话的 claude.ai/code URL 进行检查。|
| `cloud session <id> is archived and cannot accept new messages` |会议已存档。开始一个新的会话。|

### 环境已过期

云会话在一段时间不活动后停止，并且会话的 VM 被回收。会话在等待您批准 [MCP 连接器 ](<https://code.claude.com/docs/en/cloud-environments#network-access>) 工具调用或登录 MCP 服务器时被视为不活动，并且在等待期间可能会过期。

从 [claude.ai/code](<https://claude.ai/code>) 重新打开会话以配置新的 VM：

* **已恢复**：您的对话历史记录
* **未恢复**：回收VM时仍在运行的后台工作，例如子代理和shell命令，以及[自进度`/loop`](<https://code.claude.com/docs/en/scheduled-tasks#let-claude-choose-the-interval>)的挂起唤醒。要重新启动循环，请再次运行 `/loop`。

## 局限性

在依赖云会话进行工作流之前，请考虑以下限制：

* **速率限制**：云会话与您帐户内的所有其他 Claude 和 Claude Code 使用共享速率限制。并行运行多个任务会按比例消耗更多的速率限制。
* **时间限制**：命令Claude运行和SessionStart挂钩具有可以更改的默认超时，并且仅当安装脚本在大约五分钟内完成时才会缓存。参见[时间限制](<https://code.claude.com/docs/en/cloud-environments#time-limits>)
* **存储库身份验证**：只有当您通过同一帐户的身份验证时，才能将云会话拉入终端
* **平台限制**：存储库克隆和拉取请求创建需要GitHub。团队和企业计划支持自托管 [GitHub 企业服务器 ](<https://code.claude.com/docs/en/github-enterprise-server>) 实例。您可以通过设置 `CCR_FORCE_BUNDLE=1` 将 GitLab、Bitbucket 或其他非 GitHub 存储库作为 [ 本地捆绑包 ](<#发送不带-github-的本地存储库>) 发送到云会话，但会话无法将结果推送回该远程
* **组织 IP 白名单 **：云会话从 Anthropic 管理的基础设施（而不是您的网络）调用 Anthropic API，而 [ 自托管中的会话环境](<https://code.claude.com/docs/en/self-hosted-environments>) 从您自己的网络调用它。如果您的组织启用了 [IP 白名单 ](<https://support.claude.com/en/articles/13200993-restrict-access-to-claude-with-ip-allowlisting>)，则每个 Anthropic 托管的云会话都会失败并出现身份验证错误。这同样适用于在 Anthropic 托管环境中运行的 [ 代码审查 ](<https://code.claude.com/docs/en/code-review>) 和 [ 例程 ](<https://code.claude.com/docs/en/routines>)；路由到自承载环境的例程从您自己的网络调用 API。请联系 [Anthropic 支持 ](<https://support.claude.com/>)，将 Anthropic 托管的服务从您组织的 IP 许可名单中排除。

## 相关资源

* [云环境](<https://code.claude.com/docs/en/cloud-environments>)：为云会话配置网络访问、环境变量和设置脚本
* [项目](<https://code.claude.com/docs/en/claude-projects>)：Claude协调存储库上的并行云会话并返回报告的一次对话
* [Ultrareview](<https://code.claude.com/docs/en/ultrareview>)：在云沙箱中运行深度多agent代码审查
* [例程](<https://code.claude.com/docs/en/routines>)：通过API调用或响应GitHub事件按计划自动执行工作
* [挂钩配置](<https://code.claude.com/docs/en/hooks>)：在会话生命周期事件中运行脚本
* [所有设置](<https://code.claude.com/docs/en/settings-reference>)：所有配置选项
* [安全性](<https://code.claude.com/docs/en/security>)：隔离保证和数据处理
* [数据使用情况](<https://code.claude.com/docs/en/data-usage>)：Anthropic从云会话中保留的内容
* [Claude标签](<https://claude.com/docs/claude-tag/overview>)：Slack中组织管理的@Claude，在同一云基础设施上运行


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Use Claude Code in the cloud

> Run Claude Code sessions in the cloud from your browser, phone, Desktop app, or terminal, move them with `--cloud` and `--teleport`, and auto-fix pull requests.



**注释：**

  Cloud sessions are available on Pro, Max, and Team plans, and for Enterprise users with premium seats or Chat + Claude Code seats.


A cloud session is a Claude Code session that runs on cloud infrastructure instead of on your machine. By default it runs on infrastructure Anthropic manages, or on your organization's [self-hosted environment](<https://code.claude.com/docs/en/self-hosted-environments>) when routed there. The session keeps running after you close your laptop, and you can check on it or steer it from any device. It counts toward your plan's usage limits alongside the rest of your Claude and Claude Code usage, and there's no separate charge for the cloud VM.

To let cloud sessions clone your code from GitHub and push branches, connect GitHub with one of the [GitHub connection methods](<#github-authentication-options>). If your repository is on GitLab, Bitbucket, or another host, see [Platform restrictions](<#limitations>) for what works.

You can start a cloud session from any of these surfaces:

* **Browser**: [claude.ai/code](<https://claude.ai/code>), also called Claude Code on the web
* **Mobile**: the **Code** tab in the [Claude app](<https://code.claude.com/docs/en/mobile>)
* **Desktop app**: select **Cloud** instead of **Local** when you [start a session](<https://code.claude.com/docs/en/desktop#run-long-running-tasks-in-the-cloud>)
* **Terminal**: [`claude --cloud`](<#from-terminal-to-cloud>)
* **Routines**: [scheduled and triggered runs](<https://code.claude.com/docs/en/routines>) each run as a cloud session

Once you're set up, use this page to move work between your terminal and the cloud, manage and share sessions, turn on auto-fix for pull requests, and troubleshoot.



**注释：**

  These cases are covered on other pages:

  * **Starting your first cloud session**: [Get started with cloud sessions](<https://code.claude.com/docs/en/web-quickstart>) connects GitHub and walks through a task in the browser
  * **Many cloud sessions for one body of work**: a [project](<https://code.claude.com/docs/en/claude-projects>) has Claude start and keep track of them for you
  * **Steering a local session from another device**: sessions in your terminal, IDE, or the Desktop app with **Local** selected run on your machine, and [Remote Control](<https://code.claude.com/docs/en/remote-control>) lets you reach them from your phone or browser



## Cloud environments

Every cloud session runs in a [cloud environment](<https://code.claude.com/docs/en/cloud-environments>), the saved configuration that controls network access, environment variables, and setup scripts.

* **Your first environment**: if you don't have one yet, onboarding sets up a **Default** environment with [**Trusted** network access](<https://code.claude.com/docs/en/cloud-environments#access-levels>), either by creating it for you or by asking you to create it. See [The Default environment](<https://code.claude.com/docs/en/cloud-environments#the-default-environment>) for which of those happens on your plan
* **Which environment a session uses**: see [The Default environment](<https://code.claude.com/docs/en/cloud-environments#the-default-environment>) for how sessions choose an environment when you have more than one
* **Change what sessions can reach or run at startup**: see [Configure cloud environments](<https://code.claude.com/docs/en/cloud-environments>)
* **What's installed without any configuration**: see [Installed tools](<https://code.claude.com/docs/en/cloud-environments#installed-tools>)

## GitHub authentication options

Cloud sessions need access to your GitHub repositories to clone code and push branches. You can grant access in two ways:

| Method | How you connect | Repositories sessions can reach | Best for |
| :- | :- | :- | :- |
| **GitHub App** | Authorize the Claude GitHub App during [web onboarding](<https://code.claude.com/docs/en/web-quickstart>) | Any public repository, and private repositories that the Claude GitHub App is installed on | Browser onboarding; teams that want [Auto-fix](<#auto-fix-pull-requests>) |
| **`/web-setup`** | Run `/web-setup` in your terminal to send your local `gh` CLI token to your Claude account | Any repository your `gh` token can access, whether or not the Claude GitHub App is installed | Individual developers who already use `gh` |

These features depend on the Claude GitHub App being installed on the repository:

* **Auto-fix**: installing the Claude GitHub App on a repository also enables [Auto-fix](<#auto-fix-pull-requests>) for pull requests in it
* **Projects**: threads in a [project](<https://code.claude.com/docs/en/claude-projects>) need the Claude GitHub App installed on each repository they clone, whichever method you connected with. See [Set up GitHub access](<https://code.claude.com/docs/en/claude-projects#set-up-github-access>)

In Anthropic-hosted environments, your GitHub credentials stay encrypted on Anthropic's servers and never enter a session's VM. GitHub operations from the VM go through the [GitHub proxy](<https://code.claude.com/docs/en/cloud-environments#github-proxy>), which attaches the credential on the server side.

See [Connect from your terminal](<https://code.claude.com/docs/en/web-quickstart#connect-from-your-terminal>) for the `/web-setup` walkthrough, including what `/web-setup` stores and how to remove it.



**注释：**

  Organizations with [Zero Data Retention](<https://code.claude.com/docs/en/zero-data-retention>) enabled, or with the [HIPAA configuration](<https://code.claude.com/docs/en/hipaa-setup>) applied, can't use `/web-setup` or other cloud session features.


### Quick setup for Team and Enterprise

Quick setup is an organization setting that removes steps from members' GitHub and environment setup. On Team and Enterprise plans it's off by default.

Here's what changes for members when it's on:

* **`/web-setup`**: members can connect GitHub with `/web-setup`. While the setting is off, the command is hidden
* **GitHub App prompt**: browser onboarding skips the Claude GitHub App install prompt
* **First environment**: browser onboarding creates the [**Default** environment](<https://code.claude.com/docs/en/cloud-environments#the-default-environment>) for members instead of showing the environment form

An [Owner](<https://code.claude.com/docs/en/server-managed-settings#access-control>) turns it on with the **Quick setup** toggle at [**Organization settings > Claude Code**](<https://claude.ai/admin-settings/claude-code>).

## Move tasks between terminal and cloud

These workflows require the [Claude Code CLI](<https://code.claude.com/docs/en/quickstart>) signed in to the same claude.ai account. You can start new cloud sessions from your terminal, or pull cloud sessions into your terminal to continue locally. Cloud sessions persist even if you close your laptop, and you can monitor them from anywhere including the Claude mobile app.



**注释：**

  From the CLI, session handoff is one-way: you can pull cloud sessions into your terminal with `--teleport`, but you can't push an existing terminal session to the cloud. The `--cloud` flag with a task description creates a new cloud session for your current repository; with `-p` and a session ID or claude.ai/code URL it instead [queues a message into that existing session](<https://code.claude.com/docs/en/claude-code-on-the-web#send-follow-ups-from-the-cli>). The [Desktop app](<https://code.claude.com/docs/en/desktop#continue-in-another-surface>) can send a local session in its Code tab to the cloud from its **Open in** menu.


<span id="from-terminal-to-cloud" />

### Start a cloud session from your terminal

Start a cloud session from the command line with the `--cloud` flag:

```bash theme={null}
claude --cloud "Fix the authentication bug in src/auth/login.ts"
```

This creates a new cloud session on claude.ai. The cloud VM clones your current directory's GitHub remote at your current branch, not your local checkout, so push first if you have local commits. See [Send local repositories without GitHub](<#send-local-repositories-without-github>) for the cases where Claude Code uploads your local repository instead of cloning.

`--cloud` works with a single repository at a time. The task runs in the cloud while you continue working locally. The older `--remote` spelling still works as a deprecated alias for `--cloud`.

While the cloud container starts, the CLI shows a live checklist of setup steps, such as cloning the repository and running your [setup script](<https://code.claude.com/docs/en/cloud-environments#setup-scripts>). It queues messages you type during provisioning and sends them once the session is ready.



**注释：**

  `--cloud` creates cloud sessions. `--remote-control` is unrelated: it lets you monitor and steer a local CLI session from claude.ai or the Claude app. See [Remote Control](<https://code.claude.com/docs/en/remote-control>).


Open the session on claude.ai or the Claude mobile app to check progress or interact directly. From there you can steer Claude, provide feedback, or answer questions as in any other conversation.

If Claude asks a question and the session sits idle, you can still answer when you come back, up to [environment expiry](<#environment-expired>), and the session continues from your answer.

#### Tips for cloud tasks

**Plan locally, execute in the cloud**: for complex tasks, start Claude in plan mode to collaborate on the approach, then send work to the cloud:

```bash theme={null}
claude --permission-mode plan
```

In plan mode, Claude reads files, runs commands to explore, and proposes a plan without editing source code. Once you're satisfied, save the plan to the repo, commit, and push so the cloud VM can clone it. Then start a cloud session for autonomous execution:

```bash theme={null}
claude --cloud "Execute the migration plan in docs/migration-plan.md"
```

**Run tasks in parallel**: each `--cloud` command creates its own cloud session that runs independently. You can start multiple tasks and they'll all run simultaneously in separate sessions:

```bash theme={null}
claude --cloud "Fix the flaky test in auth.spec.ts"
claude --cloud "Update the API documentation"
claude --cloud "Refactor the logger to use structured output"
```

When a session completes, you can create a PR from claude.ai/code or [teleport](<#from-cloud-to-terminal>) the session to your terminal to continue working.

#### Send local repositories without GitHub

When you run `claude --cloud` from a repository that has no git remote, or from a github.com repository that the Claude GitHub App isn't installed on, Claude Code bundles your local repository and uploads it directly to the cloud session. This applies even if you connected GitHub with `/web-setup`.

For a full clone, the bundle includes your repository history across all branches, plus uncommitted changes to tracked files.

What happens to uncommitted changes in sensitive files depends on your platform:

* **macOS, Linux, and WSL**: Claude Code leaves uncommitted changes to files named like credentials or keys out of the upload. This includes `.env` files, Terraform `*.tfvars` files, and key files such as `id_rsa` and `*.pem`. It also leaves out uncommitted changes to files that a git filter such as Git LFS manages. A `Left on this machine:` notice names the files left out, and the session starts with the committed version of each, or without the file if none is committed.
* **Native Windows**: uncommitted changes to tracked files upload as they are, whatever the file's name. Stash or revert an edit you don't want in the cloud session before you start it.

To upload a bundle even when Claude Code would otherwise clone from the remote, set `CCR_FORCE_BUNDLE=1`:

```bash theme={null}
CCR_FORCE_BUNDLE=1 claude --cloud "Run the test suite and fix any failures"
```

Bundled repositories must meet these limits:

* The directory must be a git repository with at least one commit
* The bundled repository must be under 100 MB. Larger repositories fall back to bundling only the current branch, then to a single squashed snapshot of the working tree, and fail if the snapshot is still too large
* Untracked files are not included; run `git add` on files you want the cloud session to see
* On macOS, Linux, and WSL, Claude Code refuses the upload when it can't follow a git setting that affects which attribute rules apply to your files, such as `core.attributesFile` set in an included config file. The [refusal message](<https://code.claude.com/docs/en/errors#the-repository-upload-cant-follow-a-git-setting>) names the setting and the fix
* Sessions created from a bundle can push back to a GitHub remote only when your [GitHub connection](<#github-authentication-options>) has push access to that repository

On macOS, Linux, and WSL, the upload also needs git 2.31 or later and a checkout layout it supports, while on native Windows Claude Code uploads without either check. When a checkout doesn't meet those requirements, Claude Code doesn't start the session. It prints an error that contains `Not uploading this working tree:`, names the cause, and says what to change. These are the common causes:

* **Older git**: the installed git is older than 2.31. Update git, then retry.
* **A checkout layout the upload doesn't support**: you started inside a submodule, in a clone made with `git clone --separate-git-dir`, `--shared`, or `--reference`, in a checkout with `core.worktree` set, or in a repository that keeps its refs in the reftable format. Start from the main checkout of a clone made with a plain `git clone` instead.
* **A linked worktree with a sparse checkout**: `git sparse-checkout` writes settings to the worktree's own `config.worktree` file, which the upload doesn't accept, so a worktree that has those settings isn't uploaded, and neither is one Claude Code created with [`worktree.sparsePaths`](<https://code.claude.com/docs/en/settings-reference#worktree-sparsepaths>). Start from the repository's main checkout instead.
* **Git configuration kept inside the working tree**: your git configuration includes a file that sits inside the checkout, for example an `include.path` entry that points into the repository. Move that file outside the working tree or remove the include, then retry.

On macOS, Linux, and WSL, a partial clone made with `git clone --filter` uploads as a snapshot of its working tree without history, as long as the clone holds every tracked file locally.

For `claude --cloud`, if the repository is on GitHub, you can avoid the upload and its requirements: push your branch, install the Claude GitHub App on the repository, and start the session again so that it clones from GitHub.

### Send follow-ups from the CLI

Once a cloud session is running, wherever it executes, send it a follow-up message from the `claude` CLI on any machine where you're logged in with `claude auth login`. The CLI authenticates with your Anthropic account credentials and sends no local session state, so the command doesn't need to run from the machine that started the session.

The command posts one message and exits:

```bash theme={null}
claude -p "your message" --cloud <session-id>
```

The CLI queues the message into the session and exits without waiting for a reply. Use it to steer a long-running session, queue the next step while the current one is still finishing, or send follow-ups from a [CI script](<https://code.claude.com/docs/en/self-hosted-environments-testing#run-the-test-loop>). You can also pipe the message on stdin instead of passing it as an argument: `echo "your message" | claude -p --cloud <session-id>`.

For `<session-id>`, pass the bare ID, such as `session_...` or `cse_...`, or the session's `claude.ai/code/<id>` URL, with or without the scheme or query string. Find the ID in your session list at claude.ai/code.



**注释：**

  `--cloud` requires an Anthropic account. It's not available when Claude Code is configured for Amazon Bedrock, Google Cloud's Agent Platform, or another third-party provider. An [LLM gateway](<https://code.claude.com/docs/en/llm-gateway>) configured only through `ANTHROPIC_BASE_URL` doesn't count as a third-party provider for this check, but you still need to sign in with `claude auth login`. Your organization's `allow_remote_sessions` policy must also be enabled. An Owner can turn it on in the Claude Code admin settings at claude.ai/admin-settings/claude-code.


#### Output

On success, the command prints the session ID and a link to view the session:

```
Sent to cloud session.
Session ID: session_01DiUkqY2kzbUbDmW1w96rfi
View: https://claude.ai/code/session_01DiUkqY2kzbUbDmW1w96rfi?from=cli&m=0
```

Pass `--output-format json` for a machine-readable result: `{ok, session_id, url}` on success, or `{ok: false, session_id, error}` when the send fails, for example when the session is missing or archived. Configuration errors, such as an unsupported provider or a disabled organization policy, print to stderr without JSON. `--output-format stream-json` isn't supported with `--cloud <session-id>`.

If the send fails, see [Errors when sending to a cloud session](<#errors-when-sending-to-a-cloud-session>).

<span id="from-cloud-to-terminal" />

### Continue a cloud session in your terminal

Pull a cloud session into your terminal using any of these:

* **Using `--teleport`**: from the command line, run `claude --teleport` for an interactive session picker, or `claude --teleport <session-id>` to resume a specific session directly. If you have uncommitted changes, you'll be prompted to stash them first.
* **Using `/teleport`**: inside an existing CLI session, run `/teleport` or `/tp` to open the same session picker without restarting Claude Code.
* **From `/tasks`**: run `/tasks` to see your background sessions, then press `t` to teleport into one.
* **From claude.ai/code**: select **Open in > Terminal** from the session menu to copy a command you can paste into your terminal.
* **From inside the cloud session**: type `/teleport` and Claude Code replies with the exact `claude --teleport <session-id>` command for that session, ready to run from a checkout of the repository. Requires Claude Code v2.1.223 or later in the session's environment.

When you teleport a session, Claude verifies you're in the correct repository, fetches and checks out the branch from the cloud session, and loads the full conversation history into your terminal. The terminal gets its own copy of the session: new work there stays local and doesn't appear in the cloud session on claude.ai or the Claude mobile app. To keep steering from your phone after teleporting, start [`/remote-control`](<https://code.claude.com/docs/en/remote-control>) in the local session.

`--teleport` is distinct from `--resume`. `--resume` reopens a conversation from this machine's local history and doesn't list cloud sessions; `--teleport` pulls a cloud session and its branch.

#### Teleport requirements

Teleport checks these requirements before resuming a session. If any requirement isn't met, you'll see an error or be prompted to resolve the issue.

| Requirement | Details |
| - | - |
| Clean git state | Your working directory must have no uncommitted changes. Teleport prompts you to stash changes if needed. |
| Correct repository | You must run `--teleport` from a checkout of the same repository, not a fork. If you run it from a checkout of a different repository, Claude Code shows an error that names both the session's repository and your checkout's. If Claude Code can't parse your remote into a hostname, for example an SSH host alias like `git@work:owner/repo.git`, it asks you to confirm, and accepts the checkout when the remote's owner and repository name match the session's repository. |
| Branch available | The branch from the cloud session must have been pushed to the remote. Teleport automatically fetches and checks it out. |
| Same account | You must be authenticated to the same claude.ai account used in the cloud session. |

When teleport fetches the session's branch, the fetch never waits for input in your terminal. If git or ssh would ask for a password, a key passphrase, or confirmation of a new SSH host, the fetch fails, and the checkout then works only if your local clone already has the branch. For the two SSH cases, load your key into `ssh-agent` and run `git fetch` once by hand first to record the host.

#### `--teleport` is unavailable

Teleport requires claude.ai subscription authentication. Find the case that matches yours:

* **You're authenticated via API key**: run `/login` to sign in with your claude.ai account instead
* **The error names your provider**: cloud sessions aren't available through third-party providers. See the [error table](<#errors-when-sending-to-a-cloud-session>)
* **You're already signed in via claude.ai**: your organization may have disabled cloud sessions

## Work with sessions

Sessions appear in the sidebar at claude.ai/code. From there you can review changes, share with teammates, archive finished work, or delete sessions permanently.

### Permission modes in cloud sessions

You pick a cloud session's [permission mode](<https://code.claude.com/docs/en/permission-modes>) from the [mode dropdown](<https://code.claude.com/docs/en/permission-modes#switch-permission-modes>), both when you create the task and while the session runs.

Claude Code resumes a session in the permission mode it was in when you do either of these:

* Reopen a session whose Anthropic-hosted [environment expired](<#environment-expired>)
* Send a message to a session that a self-hosted runner [released while it was idle](<https://code.claude.com/docs/en/self-hosted-environments-reference#runner-cli-flags>)

### Review changes

Each session shows a diff indicator with lines added and removed, like `+42 -18`. Select it to open the diff view, leave inline comments on specific lines, and send them to Claude with your next message.

The diff view compares the session's changes against its base branch by default. To compare against any other branch in the repository, select **Compare against** and pick one.

Claude Code computes these diffs from raw git blob content, so diff drivers and `textconv` filters configured in the repository don't apply.

These steps are covered elsewhere:

* **The full walkthrough, including PR creation**: see [Review and iterate](<https://code.claude.com/docs/en/web-quickstart#review-and-iterate>)
* **Having Claude monitor the PR for CI failures and review comments automatically**: see [Auto-fix pull requests](<#auto-fix-pull-requests>)

### Manage context

Cloud sessions support [built-in commands](<https://code.claude.com/docs/en/commands>) that produce text output. Commands that only run in the terminal interface, such as `/plugin` or `/resume`, aren't available. Commands that open a picker or panel in the terminal behave differently in cloud sessions:

* **`/model`, `/effort`, `/color`, and `/rename`**: pass the value as an argument, for example `/model sonnet`, instead of opening the terminal picker or slider. The argument forms require Claude Code v2.1.205 or later in the session's environment and follow each command's [availability notes](<https://code.claude.com/docs/en/commands#all-commands>).
* **`/fast`**: toggles [fast mode](<https://code.claude.com/docs/en/fast-mode#use-fast-mode-in-cloud-sessions>) for the session when fast mode is [available on your account](<https://code.claude.com/docs/en/fast-mode#requirements>). Requires Claude Code v2.1.271 or later in the session's environment.
* **`/config`**: in your browser at claude.ai/code, opens the Claude Code section of your settings instead of setting a value, and text after the command, including `key=value`, is ignored. To change a setting for a cloud session, set an [environment variable](<https://code.claude.com/docs/en/cloud-environments#set-environment-variables>) on the environment, or in a session with one repository, commit the key to that repository's `.claude/settings.json`. [Settings in cloud sessions](<https://code.claude.com/docs/en/settings#settings-in-cloud-sessions>) lists what each session reads.

For context management specifically:

| Command | Works in cloud sessions | Notes |
| :- | :- | :- |
| `/compact` | Yes | Summarizes the conversation to free up context. Accepts optional focus instructions like `/compact keep the test output` |
| `/context` | Yes | Shows what's currently in the context window |
| `/clear` | No | Start a new session from the sidebar instead |

Auto-compaction runs automatically when the context window approaches capacity. Cloud sessions set [`CLAUDE_AUTOCOMPACT_PCT_OVERRIDE`](<https://code.claude.com/docs/en/env-vars>) themselves, so compaction triggers partway through the [auto-compact window](<https://code.claude.com/docs/en/model-config#set-the-auto-compact-window>) rather than when the window fills. That value overrides one you add in your [environment variables](<https://code.claude.com/docs/en/cloud-environments#set-environment-variables>), so adding the variable there doesn't change when compaction triggers.

To change the auto-compact window instead, set [`CLAUDE_CODE_AUTO_COMPACT_WINDOW`](<https://code.claude.com/docs/en/env-vars>) in your environment variables, or run [`/autocompact`](<https://code.claude.com/docs/en/commands#all-commands>) with a token count in a session where the variable isn't set.

[Subagents](<https://code.claude.com/docs/en/sub-agents>) work the same way they do locally. Claude can spawn them with the Agent tool to offload research or parallel work into a separate context window, keeping the main conversation lighter. Subagents defined in your repo's `.claude/agents/` are picked up automatically.

[Agent teams](<https://code.claude.com/docs/en/agent-teams>) are off by default but can be enabled by adding `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` to your [environment variables](<https://code.claude.com/docs/en/cloud-environments#set-environment-variables>).

### Take back a queued message

If you send a message while Claude is working, the message queues until Claude reads it. To take a queued message back, click the ✕ on it. The text returns to the message box so you can edit it or send something else.

If Claude has already read the message, it stays in the conversation.

### Share sessions

To share a session, toggle its visibility according to the account types below. After that, share the session link as-is. Recipients see the latest state when they open the link, but their view doesn't update in real time.

#### Share from an Enterprise or Team account

Sharing works as follows for Enterprise and Team accounts:

* **Visibility options**: **Private** and **Team**. Team visibility makes the session visible to other members of your claude.ai organization
* **Repository access**: verification is enabled by default, based on the GitHub account connected to the recipient's account
* **Your name**: your account's display name is visible to all recipients with access
* **Slack sessions**: [Claude in Slack](<https://code.claude.com/docs/en/slack>) sessions are automatically shared with Team visibility

#### Share from a Max or Pro account

Sharing works as follows for Max and Pro accounts:

* **Visibility options**: **Private** and **Public**. Public visibility makes the session visible to any user logged into claude.ai
* **Repository access**: verification isn't enabled by default
* **Sensitive content**: check your session before sharing. Sessions may contain code and credentials from private GitHub repositories

To require recipients to have repository access, or to hide your name from shared sessions, go to [**Settings > Claude Code > Sharing settings**](<https://claude.ai/settings/claude-code>).

### Archive sessions

You can archive sessions to keep your session list organized. Archived sessions are hidden from the default session list but can be viewed by filtering for archived sessions.

To archive a session, hover over the session in the sidebar and select the archive icon.

### Delete sessions

Deleting a session permanently removes the session and its data. This action can't be undone. You can delete a session in two ways:

* **From the sidebar**: filter for archived sessions, then hover over the session you want to delete and select the delete icon
* **From the session menu**: open a session, select the dropdown next to the session title, and select **Delete**

You will be asked to confirm before a session is deleted.

## Auto-fix pull requests

Claude can watch a pull request and automatically respond to CI failures and review comments. Claude subscribes to GitHub activity on the PR, and when a check fails or a reviewer leaves a comment, Claude investigates and pushes a fix if one is clear.



**注释：**

  Auto-fix requires the Claude GitHub App to be installed on your repository. If you haven't already, install it from the [GitHub App page](<https://github.com/apps/claude>).


There are a few ways to turn on auto-fix depending on where the PR came from and what device you're using:

* **PRs created in a cloud session**: open the session at claude.ai/code, open the CI status bar, and select **Auto-fix**
* **From your terminal**: run [`/autofix-pr`](<https://code.claude.com/docs/en/commands>) while on the PR's branch. Claude Code detects the open PR with `gh`, spawns a cloud session, and turns on auto-fix in one step
* **From the mobile app**: tell Claude to auto-fix the PR, for example "watch this PR and fix any CI failures or review comments"
* **Any existing PR**: paste the PR URL into a session and tell Claude to auto-fix it

Auto-fix is a per-PR toggle. To stop monitoring, open the CI status bar in the session at claude.ai/code and clear the **Auto-fix** toggle, or tell Claude to stop watching the PR.

### How Claude responds to PR activity

When auto-fix is active, Claude receives GitHub events for the PR including new review comments and CI check failures. For each event, Claude investigates and decides how to proceed:

* **Clear fixes**: if Claude is confident in a fix and it doesn't conflict with earlier instructions, Claude makes the change, pushes it, and explains what was done in the session
* **Ambiguous requests**: if a reviewer's comment could be interpreted multiple ways or involves something architecturally significant, Claude asks you before acting
* **Duplicate or no-action events**: if an event is a duplicate or requires no change, Claude notes it in the session and moves on

GitHub does not emit a webhook when the base branch advances and creates a merge conflict, so auto-fix can't react to conflicts on its own. To resolve a conflict, open the session and ask Claude to rebase.

Claude may reply to review comment threads on GitHub as part of resolving them. These replies are posted using your GitHub account, so they appear under your username, but each reply is labeled as coming from Claude Code so reviewers know it was written by the agent and not by you directly.



**注意：**

  If your repository uses comment-triggered automation such as Atlantis, Terraform Cloud, or custom GitHub Actions that run on `issue_comment` events, be aware that Claude can reply on your behalf, which can trigger those workflows. Review your repository's automation before enabling auto-fix, and consider disabling auto-fix for repositories where a PR comment can deploy infrastructure or run privileged operations.


## Security and isolation

Each cloud session is separated from your machine and from other sessions through several layers:

* **Isolated virtual machines**: each session runs in an isolated, Anthropic-managed VM. Sessions your organization routes to a [self-hosted environment](<https://code.claude.com/docs/en/self-hosted-environments>) run on your own infrastructure instead, where isolation is your deployment's responsibility
* <span id="default-allowed-domains" />**Network access controls**: in Anthropic-hosted environments, network access is limited by default and can be disabled. See [Network access](<https://code.claude.com/docs/en/cloud-environments#network-access>) for the access levels, the [default allowed domains](<https://code.claude.com/docs/en/cloud-environments#default-allowed-domains>), and the traffic that doesn't go through the allowlist. In a self-hosted environment, you restrict session egress at your own network boundary. When running with network access disabled, Claude Code can still communicate with the Anthropic API, which may allow data to exit the VM.
* **Credential protection**: in Anthropic-hosted environments, git credentials and signing keys stay outside the sandbox, and a proxy authenticates on the session's behalf with scoped credentials. In a self-hosted environment, your deployment supplies git credentials; see [Configure git](<https://code.claude.com/docs/en/self-hosted-environments-deploy#configure-git>)
* **Network secrets**: in Anthropic-hosted environments on Pro and Max plans, keys you [add to a cloud environment](<https://code.claude.com/docs/en/cloud-environments#add-network-secrets>) stay outside the sandbox the same way, attached to matching requests after they leave the session. A self-hosted environment doesn't have network secrets, and Team and Enterprise plans don't have them yet
* **Secure analysis**: code is analyzed and modified within the session's isolated environment before creating PRs

## Troubleshooting

For runtime API errors that appear in the conversation such as `API Error: 500`, `529 Overloaded`, `429`, or `Prompt is too long`, see the [Error reference](<https://code.claude.com/docs/en/errors>). Those errors and their fixes are shared with the CLI and Desktop app. The sections below cover issues specific to cloud sessions.

### Session creation failed

If a new session fails to start with `Session creation failed` or stalls at provisioning, Claude Code could not allocate a VM for the session.

* Check [status.claude.com](<https://status.claude.com>) for cloud session incidents
* Retry after a minute, as capacity is provisioned on demand
* Confirm your GitHub connection can reach the repository by following [No repositories appear after connecting GitHub](<https://code.claude.com/docs/en/web-quickstart#no-repositories-appear-after-connecting-github>)

### Unable to get organization UUID

`claude --cloud` and `claude --teleport` require sign-in with a claude.ai account. If you authenticate with an API key, or your stored account details are stale, you see one of these:

* `Unable to get organization UUID`
* ``Cloud sessions need a claude.ai sign-in. Run `claude auth login` (or /login in a local session), then try again.``
* `Error loading Claude Code sessions` in the session picker, when you run `claude --teleport` without a session ID

Run [`claude auth login`](<https://code.claude.com/docs/en/cli-reference#cli-commands>) in your shell to sign in with your claude.ai account, then retry the command. Inside a running session, `/login` does the same. If the error names your provider instead, see the [error table](<#errors-when-sending-to-a-cloud-session>): cloud sessions aren't available through third-party providers.

From v2.1.274 through v2.1.289, the sign-in message read `Claude Code cloud sessions require authentication with a Claude.ai account. API key authentication is not sufficient. Please run /login to authenticate, or check your authentication status with /status.`

### Remote Control session expired or access denied

`--teleport` connects through the same Remote Control session infrastructure that cloud sessions use, so authentication and session-expiry errors surface with Remote Control wording. You may see `Remote Control session expired` or `Access denied`. The connection token is short-lived and scoped to your account.

* Run `/login` locally to refresh your credentials, then reconnect
* Confirm you are signed in to the same account that owns the session
* If you see `Remote Control may not be available for this organization`, an Owner has not enabled cloud sessions for your organization

### Errors when sending to a cloud session

These errors come from running `claude` with [`--cloud <session-id>`](<#send-follow-ups-from-the-cli>), with or without `-p`. The CLI prefixes errors with `Error: `. A failed delivery is wrapped as `failed to send message to cloud session <id>: <reason>`.

| Message | What it means |
| - | - |
| `Cloud sessions aren't available with <provider>. They run on Anthropic's infrastructure and require an Anthropic account.` | Claude Code is configured for a third-party provider. The message names the provider with the label your configuration uses, such as `Amazon Bedrock` or `Google Vertex AI`. Remove that provider's configuration, for example by unsetting `CLAUDE_CODE_USE_BEDROCK`, and sign in with an Anthropic account (`claude auth login`). |
| `Cloud sessions are disabled by your organization's policy. Contact your organization admin to enable them.` | The `allow_remote_sessions` organization policy is off. |
| `Couldn't verify your organization's policy for cloud sessions. Check your network connection and try again.` | Claude Code couldn't fetch your organization's policy, so it refuses the send rather than assume cloud sessions are allowed. Check your network connection and retry. |
| `Attaching to an existing cloud session is not enabled for your account.` | You ran `--cloud <session-id>` without `-p`. Send the message with `claude -p "your message" --cloud <session-id>`. |
| `Session not found: <id>` | The ID or URL doesn't match a session you can access. Check it against the session's claude.ai/code URL. |
| `cloud session <id> is archived and cannot accept new messages` | The session has been archived. Start a new session instead. |

### Environment expired

Cloud sessions stop after a period of inactivity and the session's VM is reclaimed. A session counts as inactive while it waits for you to approve an [MCP connector](<https://code.claude.com/docs/en/cloud-environments#network-access>) tool call or to sign in to an MCP server, and it can expire during that wait.

Reopen the session from [claude.ai/code](<https://claude.ai/code>) to provision a fresh VM:

* **Restored**: your conversation history
* **Not restored**: background work that was still running when the VM was reclaimed, such as subagents and shell commands, and the pending wakeup of a [self-paced `/loop`](<https://code.claude.com/docs/en/scheduled-tasks#let-claude-choose-the-interval>). To restart the loop, run `/loop` again.

## Limitations

Before relying on cloud sessions for a workflow, account for these constraints:

* **Rate limits**: cloud sessions share rate limits with all other Claude and Claude Code usage within your account. Running multiple tasks in parallel consumes more rate limits proportionately.
* **Time limits**: commands Claude runs and SessionStart hooks have default timeouts you can change, and a setup script is cached only when it finishes in roughly five minutes. See [Time limits](<https://code.claude.com/docs/en/cloud-environments#time-limits>)
* **Repository authentication**: you can only pull a cloud session into your terminal when you are authenticated to the same account
* **Platform restrictions**: repository cloning and pull request creation require GitHub. Self-hosted [GitHub Enterprise Server](<https://code.claude.com/docs/en/github-enterprise-server>) instances are supported for Team and Enterprise plans. You can send a GitLab, Bitbucket, or other non-GitHub repository to a cloud session as a [local bundle](<#send-local-repositories-without-github>) by setting `CCR_FORCE_BUNDLE=1`, but the session can't push results back to that remote
* **Organization IP allowlist**: cloud sessions call the Anthropic API from Anthropic-managed infrastructure, not your network, while sessions in a [self-hosted environment](<https://code.claude.com/docs/en/self-hosted-environments>) call it from your own network. If your organization has [IP allowlisting](<https://support.claude.com/en/articles/13200993-restrict-access-to-claude-with-ip-allowlisting>) enabled, every Anthropic-hosted cloud session fails with an authentication error. The same applies to [Code Review](<https://code.claude.com/docs/en/code-review>) and to [routines](<https://code.claude.com/docs/en/routines>) that run on Anthropic-hosted environments; a routine routed to a self-hosted environment calls the API from your own network. Contact [Anthropic support](<https://support.claude.com/>) to exempt Anthropic-hosted services from your organization's IP allowlist.

## Related resources

* [Cloud environments](<https://code.claude.com/docs/en/cloud-environments>): configure network access, environment variables, and setup scripts for cloud sessions
* [Projects](<https://code.claude.com/docs/en/claude-projects>): one conversation where Claude coordinates parallel cloud sessions on your repositories and reports back
* [Ultrareview](<https://code.claude.com/docs/en/ultrareview>): run a deep multi-agent code review in a cloud sandbox
* [Routines](<https://code.claude.com/docs/en/routines>): automate work on a schedule, via API call, or in response to GitHub events
* [Hooks configuration](<https://code.claude.com/docs/en/hooks>): run scripts at session lifecycle events
* [All settings](<https://code.claude.com/docs/en/settings-reference>): all configuration options
* [Security](<https://code.claude.com/docs/en/security>): isolation guarantees and data handling
* [Data usage](<https://code.claude.com/docs/en/data-usage>): what Anthropic retains from cloud sessions
* [Claude Tag](<https://claude.com/docs/claude-tag/overview>): an organization-managed @Claude in Slack that runs on the same cloud infrastructure


</details>
