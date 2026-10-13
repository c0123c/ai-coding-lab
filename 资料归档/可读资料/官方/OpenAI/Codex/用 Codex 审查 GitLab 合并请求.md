# 用 Codex 审查 GitLab 合并请求

说明连接 GitLab、创建或选择审查账户、管理令牌、接收事件并执行审查的流程和前提。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/third-party/gitlab>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/third-party/gitlab.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/用 Codex 审查 GitLab 合并请求.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 Codex 审核 GitLab 合并请求

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

使用 Codex 代码审查来获得 GitLab 合并 
 请求的另一个高信号审查通过。 Codex 审查合并请求差异，遵循存储库 
 指南，并发布针对严重问题的标准 GitLab 代码审查。

GitLab 支持处于测试阶段，适用于所有 ChatGPT 计划。的 Codex
集成运行于 Codex 云。 GitHub-风格的存储库控件
桌面应用程序，例如 **创建拉取请求**，不包含在此测试版中。

## 开始之前

确保您有：

- 已连接的 GitLab 帐户。 GitLab.com 需要
[标准连接流程](<https://help.openai.com/articles/20001486>)；
自我管理或专用GitLab实例需要
[工作区管理模板设置](<https://help.openai.com/articles/20001487>)。
- 如果您希望 Codex 遵循存储库特定的审核，则需要 `AGENTS.md` 文件
指导。

## 设置 Codex 代码审查

### 设置 GitLab 连接和 Codex 审核身份

对于 GitLab.com，一旦您
[连接到 ChatGPT](<https://help.openai.com/articles/20001486>) 中的 GitLab，请连接您在 Codex 中的 GitLab 帐户。
对于自我管理或专用GitLab，每位审阅者应在 
[ 工作空间管理模板 ](<https://help.openai.com/articles/20001487>) 已 
 发布后进行连接。

对于自我管理或专用 GitLab，打开 **Codex 云 ** → ** 设置 ** → 
[** 连接器 **](<https://chatgpt.com/codex/cloud/settings/connectors>)。 
工作区管理员可以让Codex创建服务帐户或保存现有的
服务帐户个人访问令牌。

#### 让Codex创建帐户

在 **Codex 云** → **设置** → **连接器**，选择适合您的应用程序
自我管理或专用 GitLab 主机→选择 **设置服务帐户** →
**创建服务帐户**。完成设置的工作区管理员必须具有
管理员访问权限 GitLab 实例。选择其中之一 **选定的组**
或 **仅限选定的项目**，然后选择位置 Codex 应该经营和创造
帐户。组选项授予开发人员访问每个选定组的权限，
由其项目和子小组继承；项目选项授予开发商
只能访问您选择的单个项目。 Codex 将创建 ChatGPT
Codex 具有个人访问令牌的连接器实例服务帐户
`api` 范围。

#### 使用现有帐户

在 GitLab 中，创建或选择一个服务帐户，并仅在 Codex 应运行的组或项目中授予其开发人员访问权限 
。从 **服务
帐户**页面中，选择帐户 → **管理访问令牌** → **添加新
令牌** 
[创建个人访问令牌](<https://docs.gitlab.com/user/profile/service_accounts/#create-a-personal-access-token-for-a-service-account>)
，其范围为`api`，到期日期至少为30天。返回
Codex，选择**使用现有服务帐户**，粘贴令牌，然后选择
**保存令牌**。令牌在保存时会被加密，并且不会再次显示。

#### 管理服务帐户令牌

工作区管理员可以在 **Codex 云 ** → 
** 设置 ** → ** 连接器 ** 中管理服务帐户。对于 Codex 创建的帐户，管理员可以撤销 
 当前令牌并生成新令牌。对于现有帐户，管理员可以
替换或删除Codex中保存的令牌，并在GitLab中单独撤销（如果
需要）。在
配置有效令牌之前，Codex无法响应GitLab活动。

### 选择 GitLab 活动如何达到 Codex

#### 创建用于编码任务或特定于项目的设置的项目环境

在**Codex云**→**设置**→**环境**中，选择GitLab项目
并创建项目环境您希望 Codex 为该项目编写或执行代码 
 — 例如，编辑文件、提交更改或将更新推送到 
 合并请求分支 — 或者当审查取决于项目特定的机密、
 网络访问或设置命令时。

对于GitLab.com，还需要一个项目环境来启用Codex评论。

创建环境时，打开 **从 GitLab**
 启用 Codex 活动，以安装项目 Webhook，将合并请求、评论和问题 
 事件传递到 Codex。创建项目 Webhook 需要维护者或所有者 
 访问权限、管理员访问权限或可以管理项目 
 webhook 的自定义角色。签名的项目和组 Webhook 需要 GitLab 19.0 或更高版本。在
自管理GitLab19.0上，确认`webhook_signing_token`功能标志已
启用；它默认启用，并在 GitLab 19.1 中删除。

#### 为 GitLab 组中的项目启用 Codex 审核活动

对于自我管理或专用 GitLab，工作区管理员可以打开 **环境**
→ **GitLab 活动** → **管理组** 以启用Codex 跨群组 
 及其子群组的评论。 Codex 将安装一个群组 Webhook，覆盖整个该群组的项目 
。连接的 GitLab 用户必须是组所有者，并且 
 组 Webhook 需要 GitLab Premium 或 Ultimate 以及 GitLab 19.0 或更高版本。

组活动支持代码审查，但不会创建项目环境。
要运行 GitLab 触发的编码任务，例如编辑文件、运行命令、
提交更改或将更新推送到合并请求，请创建项目
环境。

### 配置代码审查策略

在 [Codex 审核设置 ](<https://app.chatgpt.com/settings/code-review?provider=gitlab>) 中配置代码审核策略。
选择您的存储库，然后在 **审核下启用 **自动审核 **代码**.
使用**审核**选择哪些合并请求接收审核，并**触发器**
选择何时运行审核。存储库设置可以覆盖个人默认设置。

## 请求 Codex 审核

1. 在合并请求评论中，提及 `@codex review`。
2. 等待Codex反应(👀)并发表评论。

Codex 发布 GitLab 关于合并请求的讨论和注释，就像 
 队友一样。默认情况下，手动请求的审核可以包括 P0、P1 和 
P2 结果，而自动审核则重点关注 P0 和 P1 结果。

## 启用自动评论

要自动审核合格的合并请求，请在 Codex 设置中启用 **自动 
 审核 **，选择 GitLab 存储库策略，然后选择 
 触发器： ** 开MR 打开**、**每次推送时** 或**智能触发器（实验）**。
Codex 在没有 `@codex review` 注释的情况下运行合并请求事件
与该策略和触发器匹配。

GitLab 活动必须通过项目 webhook 或祖先组 
webhook 启用。对于自我管理或专用 GitLab，配置的服务帐户 
 还必须有权写回项目。 Codex 使用已配置的 
 项目环境（如果存在）。如果祖先组已启用 
 活动，则后代项目将继承该覆盖范围。

## 自定义 Codex 评论内容

Codex 在您的存储库中搜索 `AGENTS.md` 文件并遵循适用的 
 代码审查规则。将 `## Code Review Rules` 部分添加到最接近规则管辖的代码 
 的文件中。当 
 有帮助时，使用 `###` 标题对相关检查进行分组。

例如，实验报告服务可以防止暴露后行为 
 改变比较队列：

```md
## Code Review Rules

### Experiment cohorts

- Do not filter treatment comparisons on post-exposure behavior, including conversion or retention.
  Safe path: build cohorts from assignment or exposure; report conversion as an outcome.
```

将存储库范围的规则放在根 `AGENTS.md` 中，将特定于服务的规则放在 
 嵌套文件中，例如 `services/experiment_reporting/AGENTS.md`。 Codex 应用
涵盖每个更改文件的根和更具体的指南，因此不相关的
更改不必携带特定于服务的上下文。

从两到三个简明规则开始，这些规则通常对审阅者进行编码检查
解释。有用的规则：

- **关注相应的、特定于存储库的行为。**描述
兼容性约束、数据边界或标记的不安全副作用以及
为什么它很重要。
- **说明安全路径或异常。**提供Codex足够的上下文来区分
预期行为的真正问题。
- **保持规则的范围和持久性。**更喜欢结果而不是函数名称
可以更改，并将指导放置在其管辖的代码附近。
- **在 CI 中保留机械检查。** 保留格式、lint 和其他内容
审查规则的确定性检查。

打开代表性合并请求并请求 `@codex review` 进行审核。
根据您看到的发现和反馈完善规则，并缩小或
删除产生噪音的指导。

代码审查规则指南Codex；它们不会取代测试、分支保护或
所需的批准。

对于一次性焦点，请将其添加到您的合并请求评论中：

`@codex review for issues in the database migration`

## 根据审查结果采取行动

修复审核结果需要**配置的项目环境**；组
活动单独支持评论，但无法运行编码任务。如果项目有
环境，请要求Codex通过留下
另一条评论来修复同一合并请求中的问题：

```md
@codex fix the P1 issue
```

Codex 以合并请求作为上下文启动 [ 旧版云聊天 ](<https://learn.chatgpt.com/docs/environments/cloud-environment>)，并且 
 可以在有权限时将修复推送回分支。

## 给予Codex其他任务

其他编码任务也需要 **配置好的项目环境**;组
活动本身就支持评论。如果你提到 `@codex` 在评论中
除了 `review`, Codex 开始一个 [旧版云聊天](<https://learn.chatgpt.com/docs/environments/cloud-environment>) 使用
您的合并请求作为上下文。

```md
@codex fix the CI failures
```

## 解决代码审查问题

如果 Codex 没有反应或发表评论：

- 确认已选择所需的GitLab应用程序；如果您使用特定于项目的
设置后，确认项目具有预期的 Codex 云环境。
- 确认项目或祖先组的活动。在GitLab中，检查
**Webhooks** →
 [**最近事件**](<https://docs.gitlab.com/user/project/integrations/webhooks/>)
 并验证合并请求和注释传送是否成功。
- 对于自我管理或专用 GitLab，请确认项目或组 Webhook 是
已签名，已启用 SSL 验证，并且实例处于 GitLab 19.0 或 
 更新版本。在自我管理的GitLab 19.0上，确认`webhook_signing_token`功能
标志已启用；修复故障后自动禁用的挂钩。
- 对于自我管理或专用 GitLab，请确认现有服务帐户
个人访问令牌处于活动状态并且具有 `api` 范围。如果 Codex 创建了 
 服务帐户，请确认它在 
 [Codex 连接器设置 ](<https://chatgpt.com/codex/cloud/settings/connectors>)
 中正确配置，并且项目或组已启用。
- 对于自我管理或专用 GitLab，请确认工作区服务
帐户（不仅仅是连接的 GitLab 用户）具有对项目 
 或父组的开发者访问权限，因此 Codex 可以发布评论和反应。会员资格由
继承；活动和服务帐户访问是分开的。
- 确认**代码审核**或**自动审核**已启用并且MR匹配
存储库策略和触发器。
- 使用`@codex review`。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Review GitLab merge requests with Codex

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Use Codex code review to get another high-signal review pass on GitLab merge
requests. Codex reviews the merge request diff, follows your repository
guidance, and posts a standard GitLab code review focused on serious issues.

GitLab support is in beta and is available on all ChatGPT plans. The Codex
integration runs in Codex cloud. GitHub-style repository controls in the
desktop app, such as **Create pull request**, aren't included in this beta.

## Before you start

Make sure you have:

- A connected GitLab account. GitLab.com requires the
  [standard connection flow](<https://help.openai.com/articles/20001486>);
  self-managed or Dedicated GitLab instances require
  [workspace-admin template setup](<https://help.openai.com/articles/20001487>).
- An `AGENTS.md` file if you want Codex to follow repository-specific review
  guidance.

## Set up Codex code review

### Set up the GitLab connection and Codex review identity

For GitLab.com, connect your GitLab account in Codex once you've
[connected to GitLab in ChatGPT](<https://help.openai.com/articles/20001486>).
For self-managed or Dedicated GitLab, each reviewer should connect after the
[workspace-admin template](<https://help.openai.com/articles/20001487>) has been
published.

For self-managed or Dedicated GitLab, open **Codex Cloud** → **Settings** →
[**Connectors**](<https://chatgpt.com/codex/cloud/settings/connectors>). A
workspace admin can let Codex create a service account or save an existing
service-account personal access token.

#### Let Codex create the account

In **Codex Cloud** → **Settings** → **Connectors**, select the app for your
self-managed or Dedicated GitLab host → select **Set up service account** →
**Create a service account**. The workspace admin completing setup must have
administrator access to the GitLab instance. Choose either **Selected groups**
or **Selected projects only**, then select where Codex should operate and create
the account. The group option grants Developer access to each chosen group,
inherited by its projects and subgroups; the project option grants Developer
access only to the individual projects you choose. Codex will create the ChatGPT
Codex Connector instance service account with a personal access token with the
`api` scope.

#### Use an existing account

In GitLab, create or choose a service account and grant it Developer access
only in the groups or projects where Codex should operate. From the **Service
accounts** page, select the account → **Manage access tokens** → **Add new
token** to
[create a personal access token](<https://docs.gitlab.com/user/profile/service_accounts/#create-a-personal-access-token-for-a-service-account>)
with the `api` scope and an expiration date at least 30 days away. Back in
Codex, choose **Use an existing service account**, paste the token, and select
**Save token**. The token is encrypted when saved and is never shown again.

#### Manage the service-account token

Workspace admins can manage the service account in **Codex Cloud** →
**Settings** → **Connectors**. For a Codex-created account, admins can revoke
the current token and generate a new one. For an existing account, admins can
replace or remove the saved token in Codex and revoke it separately in GitLab if
needed. Codex cannot respond to GitLab activity until a valid token is
configured.

### Choose how GitLab activity reaches Codex

#### Create a project environment for coding tasks or project-specific setup

In **Codex Cloud** → **Settings** → **Environments**, choose the GitLab project
and create a project environment when you want Codex to write or execute code
for that project—for example, to edit files, commit changes, or push updates to
a merge request branch—or when a review depends on project-specific secrets,
network access, or setup commands.

For GitLab.com, a project environment is also required to enable Codex reviews.

While creating the environment, turn on **Enable Codex activity from GitLab**
to install the project webhook that delivers merge request, comment, and issue
events to Codex. Creating the project webhook requires Maintainer or Owner
access, administrator access, or a custom role that can administer project
webhooks. Signed project and group webhooks require GitLab 19.0 or newer. On
self-managed GitLab 19.0, confirm the `webhook_signing_token` feature flag is
enabled; it is enabled by default and was removed in GitLab 19.1.

#### Enable activity for Codex reviews for projects across a GitLab group

For self-managed or Dedicated GitLab, workspace admins can open **Environments**
→ **GitLab activity** → **Manage groups** to enable Codex reviews across a group
and its subgroups. Codex will install a group webhook covering projects
throughout that group. The connected GitLab user must be a group Owner, and
group webhooks require GitLab Premium or Ultimate and GitLab 19.0 or newer.

Group activity enables code reviews but does not create project environments.
To run GitLab-triggered coding tasks, such as editing files, running commands,
committing changes, or pushing updates to a merge request, create a project
environment.

### Configure code review policies

Configure code review policies in [Codex review settings](<https://app.chatgpt.com/settings/code-review?provider=gitlab>).
Choose your repository, then turn on **Automatic review** under **Review code**.
Use **Review** to choose whose merge requests receive reviews and **Trigger** to
choose when reviews run. Repository settings can override personal defaults.

## Request a Codex review

1. In a merge request comment, mention `@codex review`.
2. Wait for Codex to react (👀) and post a review.

Codex posts GitLab discussions and notes on the merge request, just like a
teammate would. By default, manually requested reviews can include P0, P1, and
P2 findings, while automatic reviews focus on P0 and P1 findings.

## Enable automatic reviews

To review qualifying merge requests automatically, turn on **Automatic
reviews** in Codex settings, choose the GitLab repository policy, and choose a
trigger: **On MR open**, **On every push**, or **Smart Trigger (Experimental)**.
Codex runs without an `@codex review` comment when the merge request event
matches that policy and trigger.

GitLab activity must be enabled through a project webhook or an ancestor group
webhook. For self-managed or Dedicated GitLab, the configured service account
must also have access to write back to the project. Codex uses a configured
project environment when present. If an ancestor group already enables
activity, descendant projects inherit that coverage.

## Customize what Codex reviews

Codex searches your repository for `AGENTS.md` files and follows the applicable
code review rules. Add a `## Code Review Rules` section to the file closest to
the code the rules govern. Use `###` headings to group related checks when
helpful.

For example, an experiment-reporting service can keep post-exposure behavior
from changing a comparison cohort:

```md
## Code Review Rules

### Experiment cohorts

- Do not filter treatment comparisons on post-exposure behavior, including conversion or retention.
  Safe path: build cohorts from assignment or exposure; report conversion as an outcome.
```

Put repository-wide rules in the root `AGENTS.md` and service-specific rules in
a nested file, such as `services/experiment_reporting/AGENTS.md`. Codex applies
the root and more-specific guidance that covers each changed file, so unrelated
changes don't have to carry service-specific context.

Start with two or three concise rules that encode checks reviewers often
explain. Useful rules:

- **Focus on consequential, repository-specific behavior.** Describe the
  compatibility constraint, data boundary, or unsafe side effect to flag and
  why it matters.
- **State the safe path or exception.** Give Codex enough context to distinguish
  a real issue from expected behavior.
- **Keep rules scoped and durable.** Prefer outcomes over function names that
  can change, and place guidance near the code it governs.
- **Leave mechanical checks in CI.** Keep formatting, lint, and other
  deterministic checks out of review rules.

Open a representative merge request and request a review with `@codex review`.
Refine the rules based on the findings and feedback you see, and narrow or
remove guidance that produces noise.

Code review rules guide Codex; they don't replace tests, branch protections, or
required approvals.

For a one-off focus, add it to your merge request comment:

`@codex review for issues in the database migration`

## Act on review findings

Fixing review findings requires a **configured project environment**; group
activity alone supports reviews but cannot run coding tasks. If the project has
an environment, ask Codex to fix an issue in the same merge request by leaving
another comment:

```md
@codex fix the P1 issue
```

Codex starts a [legacy cloud chat](<https://learn.chatgpt.com/docs/environments/cloud-environment>) with the merge request as context and
can push a fix back to the branch when it has permission to do so.

## Give Codex other tasks

Other coding tasks also require a **configured project environment**; group
activity alone supports reviews. If you mention `@codex` in a comment with
anything other than `review`, Codex starts a [legacy cloud chat](<https://learn.chatgpt.com/docs/environments/cloud-environment>) using
your merge request as context.

```md
@codex fix the CI failures
```

## Troubleshoot code review

If Codex doesn't react or post a review:

- Confirm the intended GitLab app has been selected; if you use project-specific
  setup, confirm the project has the intended Codex cloud environment.
- Confirm activity for the project or an ancestor group. In GitLab, check
  **Webhooks** →
  [**Recent events**](<https://docs.gitlab.com/user/project/integrations/webhooks/>)
  and verify merge request and note deliveries succeed.
- For self-managed or Dedicated GitLab, confirm the project or group webhook is
  signed, SSL verification is enabled, and the instance is on GitLab 19.0 or
  newer. On self-managed GitLab 19.0, confirm the `webhook_signing_token` feature
  flag is enabled; repair hooks disabled automatically after failures.
- For self-managed or Dedicated GitLab, confirm an existing service-account
  personal access token is active and has the `api` scope. If Codex created the
  service account, confirm it is correctly configured in
  [Codex connector settings](<https://chatgpt.com/codex/cloud/settings/connectors>)
  and that the project or group is enabled.
- For self-managed or Dedicated GitLab, confirm the workspace service
  account—not just the connected GitLab user—has Developer access to the project
  or a parent group so Codex can post reviews and reactions. Membership is
  inherited; activity and service-account access are separate.
- Confirm **Code review** or **Automatic reviews** is enabled and the MR matches
  the repository policy and trigger.
- Use `@codex review`.


</details>
