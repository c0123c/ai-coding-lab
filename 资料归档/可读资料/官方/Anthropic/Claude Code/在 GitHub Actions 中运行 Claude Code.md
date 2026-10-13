# 在 GitHub Actions 中运行 Claude Code

介绍安装与配置 GitHub 集成，用评论触发开发任务、把 Issue 转成拉取请求并自动执行工作；涵盖组织设置、权限、触发条件和运行方式。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-08](<https://code.claude.com/docs/en/github-actions>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/github-actions.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/在 GitHub Actions 中运行 Claude Code.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# Claude Code GitHub 操作

> 在 GitHub 操作工作流程中运行 Claude Code，以响应 @claude 提及、自动执行任务并将问题转化为拉取请求

[Claude Code GitHub 操作 ](<https://github.com/anthropics/claude-code-action>) 是一个 GitHub 操作，它在存储库的工作流程中运行 Claude Code。在拉取请求或问题评论中提及 `@claude` 以使 Claude 分析代码、实施更改并推送提交。您还可以提示 Claude Code GitHub 操作在任何 GitHub 事件上自动运行。使用它可以将问题转化为拉取请求、修复评论中的错误或自动执行重复任务。

多种产品共享 Claude Code 名称。本页面涵盖 `claude-code-action` 工作流程集成，您可以使用存储库中的工作流程文件进行配置。相关产品请参见：

* [代码审查](<https://code.claude.com/docs/en/code-review>)：自动审查每个拉取请求，无需编写工作流程
* [Claude Code 在云中](<https://code.claude.com/docs/en/claude-code-on-the-web>)：在云基础架构而不是您的计算机上运行的 Claude Code 会话
* [Claude Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>)：GitHub 操作之外的自定义自动化。 Claude Code GitHub 操作基于 SDK 构建
* [GitHub 企业服务器](<https://code.claude.com/docs/en/github-enterprise-server>)：Claude Code 具有自托管 GitHub

## 设置

您可以通过以下两种方式之一设置 Claude Code GitHub 操作：

* **快速设置**：从Claude Code运行`/install-github-app`。 Claude Code 安装 GitHub 应用程序，添加您的身份验证密钥，并为您准备工作流程拉取请求
* **手动设置**：安装应用程序，添加密钥，然后自行将工作流程文件复制到您的存储库中。当您不在本地运行 Claude Code、命令失败或您想要完全控制工作流程文件时，请使用此路径

对于任一路径，您都需要对存储库的管理员访问权限。

### 快速设置

`/install-github-app` 仅适用于 github.com 存储库。如果您的存储库的 git Remote 位于 gitlab.com 或 bitbucket.org 上，则该命令会打印一条通知并退出，而不是开始安装。要从 GitLab 管道运行 Claude Code，请参阅 [Claude Code GitLab CI/CD](<https://code.claude.com/docs/en/gitlab-ci-cd>)。

在开始之前，请安装 [GitHub CLI](<https://cli.github.com>) 并使用 `gh auth login` 对其进行身份验证。 Claude Code 检查它并在丢失时向您发出警告。

在要连接的存储库中打开`claude`，运行`/install-github-app`，然后按照提示操作。 Claude Code 安装 Claude GitHub 应用程序，然后为工作流程设置身份验证密钥：

* 如果 Claude Code 已经有一个 API 密钥，它会重用该密钥，并提议将存储库现有的 `ANTHROPIC_API_KEY` 保密（如果已设置）
* 否则，请选择使用 Claude 订阅创建长期令牌并粘贴 API 密钥

Claude Code 将凭证保存为存储库机密，对于 API 密钥命名为 `ANTHROPIC_API_KEY`，对于订阅令牌命名为 `CLAUDE_CODE_OAUTH_TOKEN`。

Claude Code 然后推送包含您选择的工作流程文件的分支（已设置为使用该密钥），并在浏览器中打开 GitHub，并准备好创建拉取请求。创建并合并该拉取请求，并且 `@claude` 在存储库中工作。

要中途停止安装，请按 Esc。已进行的步骤完成，并且不会开始后续步骤。结束消息列出了存储库中已发生的情况，例如推送的分支或保存的秘密。

如果您选择审核工作流程，Claude 会发布对拉取请求本身的每条审核，作为它找到的每个问题的内联评论，或者在没有找到任何问题时作为一个摘要评论。 Claude 跳过一些拉取请求，例如草稿。 [审核工作流程示例](<#运行技能>)使用相同的技能并列出它们。在v2.1.229之前，Claude仅将其评论写入工作流运行日志。

要更新早期版本生成的审阅工作流程，请执行以下操作之一：

* 再次运行`/install-github-app`。当存储库已有`claude.yml`时，选择**使用最新版本更新工作流程文件**。 Claude Code 将工作流文件的新副本推送到新分支并打开拉取请求，与首次安装相同。
* 将 `--comment` 参数和 [ 审阅工作流程示例 ](<#运行技能>) 中的 `claude_args` 行添加到您自己签入的文件中，这会保留您对其所做的任何其他编辑。

安装GitHub应用程序后，Claude Code询问是否继续GitHub操作设置。选择**暂时跳过**停止，仅安装GitHub应用程序。稍后再次运行 `/install-github-app` 以完成工作流程和秘密步骤。



**注释：**

  * 当您安装GitHub应用程序时，您授予它多项权限。请参阅[GitHub应用程序权限](<#github-应用程序权限>)了解全套
  * 快速设置适用于 Claude API 和 Claude 订阅。如果您使用 Amazon Bedrock、Google Cloud 的 Agent 平台或 Microsoft Foundry，请参阅 [使用 Claude Code GitHub 与云提供商的操作 ](<https://code.claude.com/docs/en/github-actions-cloud-providers>)



### 手动设置

要配置 Claude Code GitHub 操作而不运行 `/install-github-app`，请安装应用程序，添加机密，然后自行复制工作流程文件：



  

#### 安装ClaudeGitHub应用程序

将 [Claude GitHub 应用程序 ](<https://github.com/apps/claude>) 安装到您的存储库。 Claude Code GitHub 操作依赖于应用程序的三个权限：

    * **Contents**：读写，因此Claude可以修改存储库文件
    * **Issues**：读写，因此Claude可以响应问题
    * **拉取请求**：读写，因此Claude可以创建PR并推送更改

在安装过程中，您还授予其他 Claude 功能使用的权限。请参阅 [GitHub 应用程序权限 ](<#github-应用程序权限>) 了解全套内容。
  


  

#### 添加身份验证秘密

根据您的身份验证方式，将以下机密之一添加到您的存储库。请参阅 GitHub 的 [ 在 GitHub 操作 ](<https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions>) 中使用机密的指南。

    * `ANTHROPIC_API_KEY`：来自 [Claude 控制台 ](<https://platform.claude.com>) 的 Claude API 键
    * `CLAUDE_CODE_OAUTH_TOKEN`：通过您的 Claude 订阅进行身份验证的 OAuth 令牌，适用于 Pro、Max、Team 和 Enterprise 计划。通过在本地运行 `claude setup-token` 生成一个。请参阅[生成长期令牌](<https://code.claude.com/docs/en/authentication#generate-a-long-lived-token>)

在工作流文件中，将密钥传递给匹配的输入：`anthropic_api_key` 表示 API 密钥，或 `claude_code_oauth_token` 表示 OAuth 令牌。
  


  

#### 复制工作流程文件

将 [examples/claude.yml](<https://github.com/anthropics/claude-code-action/blob/main/examples/claude.yml>) 复制到存储库的 `.github/workflows/` 目录中。该文件是一个工作流程，而不仅仅是一个示例。按照承诺，只要有人在问题或拉取请求中提及 `@claude`，Claude 就会做出响应，并使用 `ANTHROPIC_API_KEY` 密钥进行身份验证。如果您添加了 `CLAUDE_CODE_OAUTH_TOKEN`，请将工作流程的 `anthropic_api_key` 行更改为 `claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}`。
  




**提示：**

设置完成后，通过在问题或 PR 评论中标记 `@claude` 来测试 Claude Code GitHub 操作。


### 为组织设置

通过快速设置或手动设置，您可以一次配置一个存储库。要在整个组织内推广 Claude Code GitHub 行动：

* 在组织级别安装 [Claude GitHub 应用程序 ](<https://github.com/apps/claude>) 一次，选择所有存储库或选定列表
* 将身份验证密钥存储为组织级操作密钥，以便每个存储库不需要自己的副本
* 将工作流文件添加到应运行 Claude Code GitHub 操作的每个存储库，或将作业定义为每个存储库调用的 [ 可重用工作流 ](<https://docs.github.com/en/actions/using-workflows/reusing-workflows>)

对于跨存储库共享的机密，请使用 [Claude 控制台 ](<https://platform.claude.com>) 中的 API 密钥进行身份验证，而不是使用 OAuth 令牌，因为 OAuth 令牌与运行 `claude setup-token` 的人员的订阅相关联。

为了避免完全存储长期密钥，请通过工作负载身份联合进行身份验证，其中 Claude Code GitHub 操作通过以下方式交换工作流的 GitHub OpenID 连接 (OIDC) 令牌以获取 Claude API 访问权限Claude 控制台服务帐户。设置这些输入：

* `anthropic_federation_rule_id`：联邦规则ID、`fdrl_...`
* `anthropic_organization_id`：您的Anthropic组织ID
* `anthropic_service_account_id`：服务帐户ID、`svac_...`。可选，因为您在控制台中创建的联合规则已针对服务帐户
* `anthropic_workspace_id`：工作空间ID、`wrkspc_...`。当联合规则针对单个工作区时可选

向工作流授予 `id-token: write` 权限，即使您传递自己的 `github_token`，Claude Code GitHub 操作也需要该权限来进行联合交换。有关控制台端配置，请参阅[Claude Code GitHub Action 的设置指南](<https://github.com/anthropics/claude-code-action/blob/main/docs/setup.md>)。

有关安全审查中的数据处理和保留问题，请参阅[数据使用](<https://code.claude.com/docs/en/data-usage>)和[安全](<https://code.claude.com/docs/en/security>)。

### 卸载

要删除 Claude Code GitHub 操作，请撤消适用于您的安装的每一项设置：

* **工作流程文件**：从`.github/workflows/`中删除使用`anthropics/claude-code-action`的工作流程。如果您使用快速设置，请查找 `claude.yml`；如果您选择了审阅工作流程，则查找 `claude-code-review.yml`。删除工作流程后，Claude Code GitHub 操作不再运行
* **秘密**: 删除 `ANTHROPIC_API_KEY` 或 `CLAUDE_CODE_OAUTH_TOKEN` 来自存储库的秘密，以及来自组织级操作秘密的秘密，如果您 [跨存储库共享它](<#为组织设置>)。如果您删除某个机密，它所持有的凭据仍然有效。退休 API 整个密钥，也删除中的密钥 [Claude 控制台](<https://platform.claude.com>)
* **GitHub 应用程序**：卸载存储库中的 Claude GitHub 应用程序或 GitHub 应用程序下的组织设置，但前提是您不将其用于其他 Claude 功能，例如代码审查或 Web 自动修复

如果您配置了 [ 云提供商 ](<https://code.claude.com/docs/en/github-actions-cloud-providers>)，还需删除提供商机密，例如 `AWS_ROLE_TO_ASSUME`、`GCP_*` 机密或 `AZURE_*` 机密，并卸载自定义 GitHub 应用程序及其`APP_ID` 和 `APP_PRIVATE_KEY` 秘密。

### GitHub 应用程序权限

[Claude GitHub 应用程序 ](<https://github.com/apps/claude>) 由与 GitHub 集成的每个 Claude 功能共享，包括 Claude Code GitHub 操作、[代码查看云会话中拉取请求的 ](<https://code.claude.com/docs/en/code-review>) 和 [ 自动修复 ](<https://code.claude.com/docs/en/claude-code-on-the-web#auto-fix-pull-requests>)。 GitHub 应用程序具有涵盖其所有功能的单个权限集，因此该权限集包含 Claude Code GitHub 操作不使用的一些权限。

当您安装该应用程序时，您将授予以下权限：

|许可|访问|
| - | - |
|行动|读写|
|行政管理|阅读|
|支票|读写|
|内容|读写|
|讨论|读写|
|问题|读写|
|会员|阅读|
|合并队列|阅读|
|元数据|阅读|
|拉取请求|读写|
|存储库挂钩|读写|
|状态|阅读|
|工作流程|读写|

权限集也可以在使用它的功能之前更改。当应用请求以前没有的权限时，GitHub 会提示帐户所有者（组织安装的组织所有者）批准它，并且安装会保留其旧权限，直到他们批准为止。例如，当操作访问权限从读取更改为写入时，应用程序可以重新运行工作流，而不仅仅是查看运行和日志，因此 GitHub 要求所有者批准更改。

当您安装该应用程序时，即表示您接受其完整权限集。 GitHub 不允许您接受子集。如果您的组织仅需要 Claude Code GitHub 操作使用的权限，请按照 [Claude Code GitHub 操作的设置指南 ](<https://github.com/anthropics/claude-code-action/blob/main/docs/setup.md>) 创建一个包含内容、问题和拉取请求的自定义 GitHub 应用程序。自定义应用程序仅涵盖 Claude Code GitHub 操作。代码审查和网络自动修复仍然需要官方应用程序。

有关 Claude Code GitHub 操作如何限制 Claude 使用这些权限执行的操作的详细信息，请参阅 [ 安全文档 ](<https://github.com/anthropics/claude-code-action/blob/main/docs/security.md>)。

## 交互和自动化模式

Claude Code GitHub 操作检测如何从工作流程配置运行：

* **交互模式**：当工作流不提供`prompt`输入时，Claude会在问题或拉取请求评论、拉取请求审阅或新打开问题的正文或标题中等待触发短语（默认情况下为`@claude`），然后响应该请求。进度和结果显示为对触发问题或 PR 的评论。
* **自动化模式**：当工作流程提供`prompt`输入时，Claude无需等待提及即可运行，仅受[检查谁可以触发运行](<#谁可以触发跑动>)的影响。默认情况下，结果显示在工作流运行日志中，而不是注释中。 Claude 可以在提示指向时发布到问题或拉取请求，并且它有一个可以发布的工具，如 [ 代码审查示例 ](<#运行技能>) 中所示。

### 谁可以触发跑动

在这两种模式下，Claude Code GitHub 操作在 Claude 启动之前对触发参与者运行两次检查，并且当任一检查拒绝时运行都会失败：

* **写访问权限**：在发出和拉取请求事件中，触发用户必须具有对存储库的写访问权限。要允许特定用户没有写访问权限，请设置 `allowed_non_write_users` 并传递您自己的 `github_token` 输入。没有用户创建的事件（例如 `schedule` 触发器）会跳过此检查。
* **人类演员**：在每个事件中，Claude Code GitHub操作都会拒绝机器人演员，除非您将其列在`allowed_bots`中，这可以防止机器人在循环中触发Claude。此检查也适用于计划运行，GitHub 归因于存储库用户，通常是最后更改工作流程的 `cron` 计划的用户。如果该用户是机器人，请将其列在 `allowed_bots` 中。

## 示例用例

[示例目录](<https://github.com/anthropics/claude-code-action/tree/main/examples>)包含适用于不同场景的现成工作流程。

此页面上的示例显示 API 密钥身份验证。如果您使用 Claude 订阅进行身份验证，请将任何示例中的 `anthropic_api_key` 行替换为 `claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}`。

### 回复 @claude 提及

此工作流程以交互模式运行 Claude Code GitHub 操作，因此每当有人在问题或 PR 评论中提及 `@claude` 时，Claude 就会做出响应。

```yaml theme={null}
name: Claude Code
on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
jobs:
  claude:
    if: contains(github.event.comment.body, '@claude')
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
      issues: write
      id-token: write
      actions: read
    steps:
      - uses: actions/checkout@v6
        with:
          fetch-depth: 1
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

此工作流程的部分不是样板：

* `id-token: write`：Claude Code GitHub 操作的默认 GitHub 应用程序身份验证所需
* `actions: read`：让Claude读取PR上的CI结果
* `actions/checkout`：为Claude提供要使用的存储库的本地副本
* `if`：阻止跑步者从未提及`@claude`的评论开始。 Claude Code GitHub 操作还会在响应之前检查触发短语本身

工作流程到位后，请在任何问题中提及 `@claude` 或在请求中添加 PR 评论：

```text wrap theme={null}
@claude implement this feature based on the issue description
@claude how should I implement user authentication for this endpoint?
@claude fix the TypeError in the user dashboard component
```

Claude 在同一问题的评论中回复或 PR 并在工作时更新它。

### 运行技能

`prompt` 输入接受 [ 技能 ](<https://code.claude.com/docs/en/skills>) 调用以及纯文本：

* 对于存储库 `.claude/skills/` 目录中的技能，请在 `anthropics/claude-code-action` 步骤之前运行 `actions/checkout`，以便技能文件在运行程序上可用，然后将 `/skill-name` 作为 `prompt` 传递。
* 对于打包在 [ 插件 ](<https://code.claude.com/docs/en/plugins/overview>) 中的技能，请使用 `plugin_marketplaces` 和 `plugins` 输入安装插件，然后将命名空间 `/plugin-name:skill-name` 作为 `prompt` 传递。 `plugins` 输入采用 `plugin-name@marketplace-name`，其中市场名称来自市场自己的清单，而不是其存储库 URL。

以下工作流程会安装 `code-review` 插件，并在拉取请求打开、更新、重新打开或标记为可供审核时运行其技能。它运行与快速设置的审核工作流程相同的插件。当您想要自己控制提示、模型和触发器时，请使用这样的工作流程。对于无需维护工作流程文件的自动审核，请参阅[代码审核](<https://code.claude.com/docs/en/code-review>)。在公共存储库上，GitHub 保留了由分叉拉取请求触发的运行的秘密，因此审核仅对来自同一存储库中的分支的拉取请求运行。

```yaml theme={null}
name: Code Review
on:
  pull_request:
    types: [opened, synchronize, ready_for_review, reopened]
jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: read
      issues: read
      id-token: write
    steps:
      - uses: actions/checkout@v6
        with:
          fetch-depth: 1
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          plugin_marketplaces: "https://github.com/anthropics/claude-code.git"
          plugins: "code-review@claude-code-plugins"
          prompt: "/code-review:code-review --comment ${{ github.repository }}/pull/${{ github.event.pull_request.number }}"
          claude_args: '--allowedTools "mcp__github_inline_comment__create_inline_comment"'
```

此工作流中的两行控制审核的位置：

* **`--comment`**：Claude 发布对拉取请求的评论，作为它找到的每个问题的内嵌评论，或者在没有找到问题时作为一个摘要评论。如果没有它，Claude 不会发布任何内容，并且您会在工作流运行日志中阅读结果。
* **`claude_args`**：即使技能自己的 `allowed-tools` frontmatter 命名相同的工具，也要保留此行，因为 Claude Code GitHub 操作会启动 MCP 服务器，仅当 `claude_args` 中的 `--allowedTools` 时才会发布内联注释给它命名。

Claude 跳过草稿和已关闭的拉取请求、它判断不需要审查的拉取请求（例如自动或琐碎的拉取请求）以及已经有来自 Claude 的评论的拉取请求。

### 按计划运行

通过 `prompt` 输入，Claude Code GitHub 操作在任何 GitHub 事件（包括 cron 计划）上以自动化模式运行。对于纯文本提示，Claude 没有 shell 或 GitHub API 访问权限，除非您授予提示所需的工具，其中 `claude_args` 中的 `--allowedTools` 或 [`permissions.allow` 规则 ](<https://code.claude.com/docs/en/permissions#permission-rule-syntax>) `settings`输入。如果您改为调用技能，Claude 可以使用其 [`allowed-tools` frontmatter](<https://code.claude.com/docs/en/skills#pre-approve-tools-for-a-skill>) 授予的工具。 GitHub 仅从默认分支运行计划工作流，并且在公共存储库中，在 60 天无存储库活动后禁用计划。

此工作流每天在 09:00 UTC 的工作流运行日志中生成报告。其 `claude_args` 行 [ 传递 CLI 参数 ](<#传递-cli-参数>)，用于选择模型并允许两个 GitHub MCP 工具。 Claude 使用这些工具通过 GitHub API 读取提交和问题，因此您可以省略结帐步骤：

```yaml theme={null}
name: Daily Report
on:
  schedule:
    - cron: "0 9 * * *"
jobs:
  report:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      issues: read
      id-token: write
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Generate a summary of yesterday's commits and open issues"
          claude_args: |
            --model claude-opus-5-5
            --allowedTools "mcp__github__list_commits,mcp__github__list_issues"
```

## 最佳实践

### 在CLAUDE.md中定义项目标准

在存储库根目录中创建一个 `CLAUDE.md` 文件来定义代码样式指南、审查标准、项目特定规则和首选模式。 Claude 在创建 PR 和响应请求时遵循这些准则。有关详细信息，请参阅[内存文档](<https://code.claude.com/docs/en/memory>)。

### 保护您的凭据



**注意：**

切勿将 API 密钥或 OAuth 令牌直接提交到您的存储库。始终将它们存储为 GitHub 机密并在工作流程中引用它们，例如 `anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}`。


仅授予工作流所需的权限，并在合并之前查看 Claude 的更改。

有关包括权限和身份验证在内的安全指南，请参阅 [Claude Code 操作安全文档 ](<https://github.com/anthropics/claude-code-action/blob/main/docs/security.md>)。

### 管理成本

每次运行消耗两种资源：

* **GitHub 操作分钟数 **：Claude Code GitHub 操作在 GitHub 托管的运行器上运行，这会消耗您的 GitHub 操作分钟数。有关定价和分钟限制，请参阅 [GitHub 的计费文档 ](<https://docs.github.com/en/billing/managing-billing-for-your-products/managing-billing-for-github-actions/about-billing-for-github-actions>)。
* **API 令牌**：每次交互根据提示和响应的长度、任务复杂性和代码库大小消耗令牌。请参阅 [Claude 的定价页面 ](<https://claude.com/platform/api>) 了解当前代币费率。如果您使用 OAuth 令牌进行身份验证，则使用您的 Claude 订阅而不是 API 计费运行。

您可以通过提供 Claude 更清晰的上下文并限制每次运行可以完成的工作量来降低这两种成本：

* 写入特定的 `@claude` 请求，以便 Claude 需要更少的轮数来完成
* 使用问题模板预先提供上下文
* 保持 `CLAUDE.md` 简洁，因为 Claude 在每次运行时都会读取它
* 在`claude_args`中设置`--max-turns`以限制迭代
* 设置工作流程级别的超时以避免作业失控
* 使用GitHub的并发控制来限制并行运行

有关整个组织的使用情况跟踪，请参阅 [ 分析仪表板 ](<https://code.claude.com/docs/en/analytics>) 和 [ 监控 ](<https://code.claude.com/docs/en/monitoring-usage>)。有关如何衡量和计费使用情况的信息，请参阅[成本](<https://code.claude.com/docs/en/costs>)。

## 使用云提供商

默认情况下，Claude Code GitHub 操作直接使用您的 API 密钥或 OAuth 令牌调用 Claude API。要通过您自己的云帐户路由推理，请为您的提供商设置输入并按照 [使用 Claude Code GitHub 与云提供商的操作 ](<https://code.claude.com/docs/en/github-actions-cloud-providers>)：

* **亚马逊基岩**：`use_bedrock: "true"`
* **Google云的Agent平台**：`use_vertex: "true"`
* **Microsoft 代工厂**：`use_foundry: "true"`

对于这三个提供商，您可以通过 OIDC 身份联合而不是 Claude API 密钥进行身份验证，因此您不会在存储库中存储静态云凭据。

## 故障排除

### Claude 不响应@claude 命令

* 验证 GitHub 应用程序是否安装在存储库上
* 检查是否为存储库启用了工作流程
* 确保您的 API 密钥或 OAuth 令牌已在存储库机密中设置
* 确认评论包含 `@claude` 作为一个完整的单词，而不是 `/claude` 或 `@claude-bot`
* 确认评论用户具有存储库的写入权限。有关例外情况，请参阅[谁可以触发运行](<#谁可以触发跑动>)

### CI 未在 Claude 的提交上运行

* GitHub 不会触发使用默认 `GITHUB_TOKEN` 进行的提交的工作流程。如果您将 `github_token: ${{ secrets.GITHUB_TOKEN }}` 传递给 Claude Code GitHub 操作，请将其删除，以便它作为 Claude GitHub 应用程序进行身份验证，或者传递自定义应用程序令牌
* 检查您的 CI 工作流程的触发器是否包含 Claude 的推送产生的事件，例如 `push` 或 `pull_request`

### 认证错误

* 在调试工作流程之前，通过使用 `claude` 在本地测试 API 密钥或 OAuth 令牌来确认其有效
* 对于 Bedrock、Agent 平台和 Foundry，请参阅云提供商页面的 [ 故障排除部分 ](<https://code.claude.com/docs/en/github-actions-cloud-providers#troubleshooting>)

有关更多解决方案，请参阅 Claude Code GitHub 操作的 [FAQ](<https://github.com/anthropics/claude-code-action/blob/main/docs/faq.md>)。

## 高级配置

### 动作参数

这些是最常用的输入。每个都映射到 `anthropics/claude-code-action` 步骤中的 `with:` 键。

|参数|描述|必填|
| - | - | - |
| `prompt` |Claude 的说明，作为纯文本或 [ 技能 ](<https://code.claude.com/docs/en/skills>) 调用。省略时，Claude 会响应 [ 触发短语 ](<#交互和自动化模式>)|否|
| `claude_args` |CLI 参数传递给 Claude Code|否|
| `anthropic_api_key` |Claude API 键|对于 Claude API，除非您使用 `claude_code_oauth_token` 或 [ 工作负载身份联合 ](<#为组织设置>)。不用于基岩、Agent 平台或铸造厂|
| `claude_code_oauth_token` |用于通过 Claude 订阅进行身份验证的 OAuth 令牌，由 `claude setup-token` 生成|否|
| `github_token` |GitHub 操作的令牌。省略时，Claude Code GitHub 操作将验证为 Claude GitHub 应用程序|否|
| `plugin_marketplaces` |以换行符分隔的插件市场 Git URL 列表|否|
| `plugins` |执行前要安装的以换行符分隔的插件名称列表|否|
| `settings` |Claude Code 设置，作为 JSON 字符串或设置 JSON 文件的路径|否|
| `trigger_phrase` |触发短语Claude响应。默认值：`@claude`|否|
| `use_bedrock` |使用 Amazon Bedrock 代替 Claude API|否|
| `use_vertex` |使用Google云的Agent平台代替ClaudeAPI|否|
| `use_foundry` |使用 Microsoft Foundry 代替 Claude API|否|

有关完整输入列表，请参阅 Claude Code GitHub 操作的 [ 配置参考 ](<https://github.com/anthropics/claude-code-action/blob/main/docs/usage.md#inputs>)。

### 传递 CLI 参数

`claude_args` 参数接受任何 [Claude Code CLI 参数 ](<https://code.claude.com/docs/en/cli-reference>)：

```yaml theme={null}
claude_args: "--max-turns 5 --model claude-sonnet-5 --mcp-config /path/to/config.json"
```

常见论点：

* `--max-turns`：限制通话轮数
* `--model`：要使用的型号，例如`claude-sonnet-5`。如果没有此参数，Claude Code GitHub 操作将使用 Claude Code [ 默认模型 ](<https://code.claude.com/docs/en/model-config>)
* `--mcp-config`：[MCP配置的路径](<https://code.claude.com/docs/en/mcp>)
* `--allowedTools`：以逗号分隔的允许工具列表。 `--allowed-tools` 别名也有效
* `--debug`：启用调试输出

## 从测试版升级

如果您的工作流程仍然引用 `anthropics/claude-code-action@beta`，请将其更新为 v1：

1. 将`uses`行中的`@beta`更改为`@v1`
2. 删除 `mode` 输入，因为 Claude Code GitHub 现在操作 [ 自动检测模式 ](<#交互和自动化模式>)
3. 将 `direct_prompt` 替换为 `prompt`
4. 将CLI选项例如`max_turns`和`model`移动到`claude_args`中。 `custom_instructions`没有同名标志，变成`--append-system-prompt`

有关完整的输入映射和前后示例，请参阅 [ 迁移指南 ](<https://github.com/anthropics/claude-code-action/blob/main/docs/migration-guide.md>)。

## 接下来是什么

* [使用 Claude Code GitHub 与云提供商的操作 ](<https://code.claude.com/docs/en/github-actions-cloud-providers>)：通过 Amazon Bedrock、Google 云的 Agent 平台或 Microsoft Foundry 进行路由推理
* [配置参考](<https://github.com/anthropics/claude-code-action/blob/main/docs/usage.md#inputs>)：动作输入的完整列表
* [示例目录](<https://github.com/anthropics/claude-code-action/tree/main/examples>)：适用于更多场景的即用型工作流程
* [代码审查](<https://code.claude.com/docs/en/code-review>)：自动拉取请求审查，无需维护工作流程文件


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Claude Code GitHub Actions

> Run Claude Code in GitHub Actions workflows to respond to @claude mentions, automate tasks, and turn issues into pull requests

[Claude Code GitHub Actions](<https://github.com/anthropics/claude-code-action>) is a GitHub Action that runs Claude Code inside your repository's workflows. Mention `@claude` in a pull request or issue comment to have Claude analyze code, implement changes, and push commits. You can also give the Claude Code GitHub Action a prompt to run automatically on any GitHub event. Use it to turn issues into pull requests, fix bugs from a comment, or automate recurring tasks.

Several products share the Claude Code name. This page covers the `claude-code-action` workflow integration, which you configure with workflow files in your repository. For the related products, see:

* [Code Review](<https://code.claude.com/docs/en/code-review>): automatic review on every pull request, without writing a workflow
* [Claude Code in the cloud](<https://code.claude.com/docs/en/claude-code-on-the-web>): Claude Code sessions that run on cloud infrastructure instead of your machine
* [Claude Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>): custom automation outside GitHub Actions. The Claude Code GitHub Action is built on the SDK
* [GitHub Enterprise Server](<https://code.claude.com/docs/en/github-enterprise-server>): Claude Code with self-hosted GitHub

## Setup

You can set up the Claude Code GitHub Action in one of two ways:

* **Quick setup**: run `/install-github-app` from Claude Code. Claude Code installs the GitHub App, adds your authentication secret, and prepares the workflow pull request for you
* **Manual setup**: install the app, add the secret, and copy the workflow file into your repository yourself. Use this path when you don't run Claude Code locally, when the command fails, or when you want full control of the workflow files

For either path, you need admin access to the repository.

### Quick setup

`/install-github-app` works only with github.com repositories. If your repository's git remote is on gitlab.com or bitbucket.org, the command prints a notice and exits instead of starting setup. To run Claude Code from GitLab pipelines, see [Claude Code GitLab CI/CD](<https://code.claude.com/docs/en/gitlab-ci-cd>).

Before you start, install the [GitHub CLI](<https://cli.github.com>) and authenticate it with `gh auth login`. Claude Code checks for it and warns you if it's missing.

Open `claude` in the repository you want to connect, run `/install-github-app`, and follow the prompts. Claude Code installs the Claude GitHub App, then sets up an authentication secret for the workflows:

* If Claude Code already has an API key, it reuses that key, and offers to keep the repository's existing `ANTHROPIC_API_KEY` secret if one is already set
* Otherwise, choose between creating a long-lived token with your Claude subscription and pasting in an API key

Claude Code saves the credential as a repository secret, named `ANTHROPIC_API_KEY` for an API key or `CLAUDE_CODE_OAUTH_TOKEN` for a subscription token.

Claude Code then pushes a branch with the workflow files you select, already set to use that secret, and opens GitHub in your browser with a pull request ready to create. Create and merge that pull request, and `@claude` works in the repository.

To stop setup partway through, press Esc. A step already in progress finishes, and no later step starts. The closing message lists what already happened in the repository, such as a pushed branch or a saved secret.

If you select the review workflow, Claude posts each review on the pull request itself, as an inline comment on each issue it finds or as one summary comment when it finds none. Claude skips some pull requests, such as drafts. The [review workflow example](<#run-a-skill>) uses the same skill and lists them. Before v2.1.229, Claude wrote its review only to the workflow run log.

To update a review workflow that an earlier version generated, do one of the following:

* Run `/install-github-app` again. When the repository already has a `claude.yml`, select **Update workflow file with latest version**. Claude Code pushes fresh copies of the workflow files to a new branch and opens the pull request, the same as a first install.
* Add the `--comment` argument and the `claude_args` line from the [review workflow example](<#run-a-skill>) to the checked-in file yourself, which keeps any other edits you made to it.

After installing the GitHub App, Claude Code asks whether to continue with GitHub Actions setup. Choose **Skip for now** to stop with only the GitHub App installed. Run `/install-github-app` again later to finish the workflow and secret steps.



**注释：**

  * When you install the GitHub App, you grant it several permissions. See [GitHub App permissions](<#github-app-permissions>) for the full set
  * Quick setup works with the Claude API and Claude subscriptions. If you use Amazon Bedrock, Google Cloud's Agent Platform, or Microsoft Foundry, see [Use Claude Code GitHub Actions with cloud providers](<https://code.claude.com/docs/en/github-actions-cloud-providers>)



### Manual setup

To configure the Claude Code GitHub Action without running `/install-github-app`, install the app, add a secret, and copy a workflow file yourself:



  

#### Install the Claude GitHub App

    Install the [Claude GitHub App](<https://github.com/apps/claude>) to your repository. The Claude Code GitHub Action relies on three of the app's permissions:

    * **Contents**: read and write, so Claude can modify repository files
    * **Issues**: read and write, so Claude can respond to issues
    * **Pull requests**: read and write, so Claude can create PRs and push changes

    During installation, you also grant permissions that other Claude features use. See [GitHub App permissions](<#github-app-permissions>) for the full set.
  


  

#### Add an authentication secret

    Add one of the following secrets to your repository, depending on how you authenticate. See GitHub's guide to [using secrets in GitHub Actions](<https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions>).

    * `ANTHROPIC_API_KEY`: a Claude API key from the [Claude Console](<https://platform.claude.com>)
    * `CLAUDE_CODE_OAUTH_TOKEN`: an OAuth token that authenticates with your Claude subscription, available on Pro, Max, Team, and Enterprise plans. Generate one by running `claude setup-token` locally. See [Generate a long-lived token](<https://code.claude.com/docs/en/authentication#generate-a-long-lived-token>)

    In workflow files, pass the secret to the matching input: `anthropic_api_key` for an API key, or `claude_code_oauth_token` for an OAuth token.
  


  

#### Copy the workflow file

    Copy [examples/claude.yml](<https://github.com/anthropics/claude-code-action/blob/main/examples/claude.yml>) into your repository's `.github/workflows/` directory. The file is a working workflow, not just an example. As committed, Claude responds whenever someone mentions `@claude` in an issue or pull request, authenticating with the `ANTHROPIC_API_KEY` secret. If you added `CLAUDE_CODE_OAUTH_TOKEN` instead, change the workflow's `anthropic_api_key` line to `claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}`.
  




**提示：**

  After setup, test the Claude Code GitHub Action by tagging `@claude` in an issue or PR comment.


### Set up for an organization

With quick setup or manual setup, you configure one repository at a time. To roll the Claude Code GitHub Action out across an organization:

* Install the [Claude GitHub App](<https://github.com/apps/claude>) once at the organization level, choosing all repositories or a selected list
* Store the authentication secret as an organization-level Actions secret so each repository doesn't need its own copy
* Add the workflow file to each repository that should run the Claude Code GitHub Action, or define the job once as a [reusable workflow](<https://docs.github.com/en/actions/using-workflows/reusing-workflows>) that each repository calls

For a secret shared across repositories, authenticate with an API key from the [Claude Console](<https://platform.claude.com>) rather than an OAuth token, since an OAuth token is tied to the subscription of the person who ran `claude setup-token`.

To avoid storing a long-lived secret entirely, authenticate through workload identity federation, where the Claude Code GitHub Action exchanges the workflow's GitHub OpenID Connect (OIDC) token for Claude API access through a Claude Console service account. Set these inputs:

* `anthropic_federation_rule_id`: the federation rule ID, `fdrl_...`
* `anthropic_organization_id`: your Anthropic organization ID
* `anthropic_service_account_id`: the service account ID, `svac_...`. Optional, since the federation rule you create in the Console already targets a service account
* `anthropic_workspace_id`: the workspace ID, `wrkspc_...`. Optional when the federation rule targets a single workspace

Grant the workflow the `id-token: write` permission, which the Claude Code GitHub Action needs for the federation exchange even when you pass your own `github_token`. See the [Claude Code GitHub Action's setup guide](<https://github.com/anthropics/claude-code-action/blob/main/docs/setup.md>) for the Console-side configuration.

For data handling and retention questions in a security review, see [data usage](<https://code.claude.com/docs/en/data-usage>) and [security](<https://code.claude.com/docs/en/security>).

### Uninstall

To remove the Claude Code GitHub Action, undo each piece of the setup that applies to your installation:

* **Workflow files**: delete the workflows that use `anthropics/claude-code-action` from `.github/workflows/`. If you used quick setup, look for `claude.yml` and, if you selected the review workflow, `claude-code-review.yml`. With the workflows deleted, the Claude Code GitHub Action no longer runs
* **Secrets**: delete the `ANTHROPIC_API_KEY` or `CLAUDE_CODE_OAUTH_TOKEN` secret from the repository, and from organization-level Actions secrets if you [shared it across repositories](<#set-up-for-an-organization>). If you delete a secret, the credential it held stays valid. To retire an API key entirely, also delete the key in the [Claude Console](<https://platform.claude.com>)
* **GitHub App**: uninstall the Claude GitHub App in your repository or organization settings under GitHub Apps, but only if you don't use it for another Claude feature, such as Code Review or web auto-fix

If you configured a [cloud provider](<https://code.claude.com/docs/en/github-actions-cloud-providers>), also delete the provider secrets, such as `AWS_ROLE_TO_ASSUME`, the `GCP_*` secrets, or the `AZURE_*` secrets, and uninstall the custom GitHub App along with its `APP_ID` and `APP_PRIVATE_KEY` secrets.

### GitHub App permissions

The [Claude GitHub App](<https://github.com/apps/claude>) is shared by every Claude feature that integrates with GitHub, including the Claude Code GitHub Action, [Code Review](<https://code.claude.com/docs/en/code-review>), and [auto-fix for pull requests](<https://code.claude.com/docs/en/claude-code-on-the-web#auto-fix-pull-requests>) in cloud sessions. A GitHub App has a single permission set covering all of its features, so the set includes some permissions that the Claude Code GitHub Action doesn't use.

When you install the app, you grant the following permissions:

| Permission | Access |
| - | - |
| Actions | Read and write |
| Administration | Read |
| Checks | Read and write |
| Contents | Read and write |
| Discussions | Read and write |
| Issues | Read and write |
| Members | Read |
| Merge queues | Read |
| Metadata | Read |
| Pull requests | Read and write |
| Repository hooks | Read and write |
| Statuses | Read |
| Workflows | Read and write |

The permission set can also change ahead of the features that use it. When the app requests a permission it didn't have before, GitHub prompts the account owner to approve it, an organization owner for an organization install, and the installation keeps its old permissions until they do. For example, when Actions access changes from read to write, the app can re-run workflows rather than only view runs and logs, so GitHub asks the owner to approve the change.

When you install the app, you accept its full permission set. GitHub doesn't let you accept a subset. If your organization requires only the permissions the Claude Code GitHub Action uses, create a custom GitHub App with Contents, Issues, and Pull requests instead, following the [Claude Code GitHub Action's setup guide](<https://github.com/anthropics/claude-code-action/blob/main/docs/setup.md>). A custom app covers only the Claude Code GitHub Action. Code Review and web auto-fix still require the official app.

For details on how the Claude Code GitHub Action limits what Claude can do with these permissions, see the [security documentation](<https://github.com/anthropics/claude-code-action/blob/main/docs/security.md>).

## Interactive and automation modes

The Claude Code GitHub Action detects how to run from your workflow configuration:

* **Interactive mode**: when the workflow provides no `prompt` input, Claude waits for the trigger phrase, `@claude` by default, in an issue or pull request comment, in a pull request review, or in the body or title of a newly opened issue, then responds to that request. Progress and results appear as a comment on the triggering issue or PR.
* **Automation mode**: when the workflow provides a `prompt` input, Claude runs without waiting for a mention, subject only to the [checks on who can trigger runs](<#who-can-trigger-runs>). By default, results appear in the workflow run log rather than a comment. Claude can post to the issue or pull request when the prompt directs it to and it has a tool that can post, as in the [code-review example](<#run-a-skill>).

### Who can trigger runs

In both modes, the Claude Code GitHub Action runs two checks on the triggering actor before Claude starts, and the run fails when either check rejects it:

* **Write access**: on issue and pull request events, the triggering user must have write access to the repository. To allow specific users without write access, set `allowed_non_write_users` and pass your own `github_token` input. Events that no user authors, such as a `schedule` trigger, skip this check.
* **Human actor**: on every event, the Claude Code GitHub Action rejects a bot actor unless you list it in `allowed_bots`, which keeps bots from triggering Claude in a loop. This check also applies to scheduled runs, which GitHub attributes to a repository user, usually the one who last changed the workflow's `cron` schedule. If that user is a bot, list it in `allowed_bots`.

## Example use cases

The [examples directory](<https://github.com/anthropics/claude-code-action/tree/main/examples>) contains ready-to-use workflows for different scenarios.

The examples on this page show API key authentication. If you authenticate with a Claude subscription, replace the `anthropic_api_key` line in any example with `claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}`.

### Respond to @claude mentions

This workflow runs the Claude Code GitHub Action in interactive mode, so Claude responds whenever someone mentions `@claude` in an issue or PR comment.

```yaml theme={null}
name: Claude Code
on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
jobs:
  claude:
    if: contains(github.event.comment.body, '@claude')
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
      issues: write
      id-token: write
      actions: read
    steps:
      - uses: actions/checkout@v6
        with:
          fetch-depth: 1
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

The parts of this workflow that aren't boilerplate:

* `id-token: write`: required for the Claude Code GitHub Action's default GitHub App authentication
* `actions: read`: lets Claude read CI results on PRs
* `actions/checkout`: gives Claude a local copy of the repository to work in
* `if`: keeps runners from starting on comments that don't mention `@claude`. The Claude Code GitHub Action also checks the trigger phrase itself before responding

Once the workflow is in place, mention `@claude` in any issue or PR comment with a request:

```text wrap theme={null}
@claude implement this feature based on the issue description
@claude how should I implement user authentication for this endpoint?
@claude fix the TypeError in the user dashboard component
```

Claude replies in a comment on the same issue or PR and updates it as it works.

### Run a skill

The `prompt` input accepts a [skill](<https://code.claude.com/docs/en/skills>) invocation as well as plain text:

* For a skill in your repository's `.claude/skills/` directory, run `actions/checkout` before the `anthropics/claude-code-action` step so the skill files are available on the runner, then pass `/skill-name` as the `prompt`.
* For a skill packaged in a [plugin](<https://code.claude.com/docs/en/plugins/overview>), install the plugin with the `plugin_marketplaces` and `plugins` inputs, then pass the namespaced `/plugin-name:skill-name` as the `prompt`. The `plugins` input takes `plugin-name@marketplace-name`, where the marketplace name comes from the marketplace's own manifest rather than its repository URL.

The following workflow installs the `code-review` plugin and runs its skill when a pull request is opened, updated, reopened, or marked ready for review. It runs the same plugin as the review workflow from quick setup. Use a workflow like this when you want to control the prompt, model, and triggers yourself. For automatic reviews without maintaining a workflow file, see [Code Review](<https://code.claude.com/docs/en/code-review>). On public repositories, GitHub withholds secrets from runs triggered by fork pull requests, so the review runs only on pull requests from branches in the same repository.

```yaml theme={null}
name: Code Review
on:
  pull_request:
    types: [opened, synchronize, ready_for_review, reopened]
jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: read
      issues: read
      id-token: write
    steps:
      - uses: actions/checkout@v6
        with:
          fetch-depth: 1
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          plugin_marketplaces: "https://github.com/anthropics/claude-code.git"
          plugins: "code-review@claude-code-plugins"
          prompt: "/code-review:code-review --comment ${{ github.repository }}/pull/${{ github.event.pull_request.number }}"
          claude_args: '--allowedTools "mcp__github_inline_comment__create_inline_comment"'
```

Two lines in this workflow control where the review goes:

* **`--comment`**: Claude posts its review on the pull request, as an inline comment on each issue it finds or as one summary comment when it finds none. Without it, Claude posts nothing, and you read the findings in the workflow run log.
* **`claude_args`**: keep this line even though the skill's own `allowed-tools` frontmatter names the same tool, because the Claude Code GitHub Action starts the MCP server that posts inline comments only when `--allowedTools` in `claude_args` names it.

Claude skips draft and closed pull requests, pull requests it judges not to need a review, such as automated or trivial ones, and pull requests that already have a comment from Claude.

### Run on a schedule

With a `prompt` input, the Claude Code GitHub Action runs in automation mode on any GitHub event, including a cron schedule. For a plain-text prompt, Claude has no shell or GitHub API access until you grant the tools the prompt needs, with `--allowedTools` in `claude_args` or a [`permissions.allow` rule](<https://code.claude.com/docs/en/permissions#permission-rule-syntax>) in the `settings` input. If you invoke a skill instead, Claude can use the tools its [`allowed-tools` frontmatter](<https://code.claude.com/docs/en/skills#pre-approve-tools-for-a-skill>) grants. GitHub runs scheduled workflows only from the default branch and, in public repositories, disables the schedule after 60 days without repository activity.

This workflow generates a report in the workflow run log at 09:00 UTC each day. Its `claude_args` line [passes CLI arguments](<#pass-cli-arguments>) that select the model and allow two GitHub MCP tools. Claude reads commits and issues through the GitHub API with those tools, so you can omit the checkout step:

```yaml theme={null}
name: Daily Report
on:
  schedule:
    - cron: "0 9 * * *"
jobs:
  report:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      issues: read
      id-token: write
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Generate a summary of yesterday's commits and open issues"
          claude_args: |
            --model claude-opus-5-5
            --allowedTools "mcp__github__list_commits,mcp__github__list_issues"
```

## Best practices

### Define project standards in CLAUDE.md

Create a `CLAUDE.md` file in your repository root to define code style guidelines, review criteria, project-specific rules, and preferred patterns. Claude follows these guidelines when creating PRs and responding to requests. See the [memory documentation](<https://code.claude.com/docs/en/memory>) for details.

### Protect your credentials



**注意：**

  Never commit API keys or OAuth tokens directly to your repository. Always store them as GitHub Secrets and reference them in workflows, for example `anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}`.


Grant the workflow only the permissions it needs, and review Claude's changes before merging.

For security guidance including permissions and authentication, see the [Claude Code Action security documentation](<https://github.com/anthropics/claude-code-action/blob/main/docs/security.md>).

### Manage costs

Each run consumes two kinds of resources:

* **GitHub Actions minutes**: the Claude Code GitHub Action runs on GitHub-hosted runners, which consume your GitHub Actions minutes. See [GitHub's billing documentation](<https://docs.github.com/en/billing/managing-billing-for-your-products/managing-billing-for-github-actions/about-billing-for-github-actions>) for pricing and minute limits.
* **API tokens**: each interaction consumes tokens based on the length of prompts and responses, task complexity, and codebase size. See [Claude's pricing page](<https://claude.com/platform/api>) for current token rates. If you authenticate with an OAuth token, runs use your Claude subscription instead of API billing.

You can lower both kinds of cost by giving Claude clearer context and by capping how much work each run can do:

* Write specific `@claude` requests so Claude needs fewer turns to finish
* Use issue templates to provide context up front
* Keep your `CLAUDE.md` concise, since Claude reads it on every run
* Set `--max-turns` in `claude_args` to limit iterations
* Set workflow-level timeouts to avoid runaway jobs
* Use GitHub's concurrency controls to limit parallel runs

For usage tracking across your organization, see the [analytics dashboard](<https://code.claude.com/docs/en/analytics>) and [monitoring](<https://code.claude.com/docs/en/monitoring-usage>). For how usage is measured and billed, see [costs](<https://code.claude.com/docs/en/costs>).

## Use a cloud provider

By default, the Claude Code GitHub Action calls the Claude API directly with your API key or OAuth token. To route inference through your own cloud account instead, set the input for your provider and follow [Use Claude Code GitHub Actions with cloud providers](<https://code.claude.com/docs/en/github-actions-cloud-providers>):

* **Amazon Bedrock**: `use_bedrock: "true"`
* **Google Cloud's Agent Platform**: `use_vertex: "true"`
* **Microsoft Foundry**: `use_foundry: "true"`

With all three providers, you authenticate through OIDC identity federation instead of a Claude API key, so you store no static cloud credentials in your repository.

## Troubleshooting

### Claude not responding to @claude commands

* Verify the GitHub App is installed on the repository
* Check that workflows are enabled for the repository
* Ensure your API key or OAuth token is set in repository secrets
* Confirm the comment contains `@claude` as a complete word, not `/claude` or `@claude-bot`
* Confirm the commenting user has write access to the repository. See [Who can trigger runs](<#who-can-trigger-runs>) for the exceptions

### CI not running on Claude's commits

* GitHub doesn't trigger workflows on commits made with the default `GITHUB_TOKEN`. If you pass `github_token: ${{ secrets.GITHUB_TOKEN }}` to the Claude Code GitHub Action, remove it so it authenticates as the Claude GitHub App, or pass a custom app token instead
* Check that your CI workflow's triggers include the events Claude's pushes produce, such as `push` or `pull_request`

### Authentication errors

* Confirm the API key or OAuth token is valid by testing it locally with `claude` before debugging the workflow
* For Bedrock, Agent Platform, and Foundry, see the cloud provider page's [troubleshooting section](<https://code.claude.com/docs/en/github-actions-cloud-providers#troubleshooting>)

For more solutions, see the Claude Code GitHub Action's [FAQ](<https://github.com/anthropics/claude-code-action/blob/main/docs/faq.md>).

## Advanced configuration

### Action parameters

These are the most commonly used inputs. Each maps to a `with:` key in the `anthropics/claude-code-action` step.

| Parameter | Description | Required |
| - | - | - |
| `prompt` | Instructions for Claude, as plain text or a [skill](<https://code.claude.com/docs/en/skills>) invocation. When omitted, Claude responds to the [trigger phrase](<#interactive-and-automation-modes>) instead | No |
| `claude_args` | CLI arguments passed to Claude Code | No |
| `anthropic_api_key` | Claude API key | For the Claude API, unless you use `claude_code_oauth_token` or [workload identity federation](<#set-up-for-an-organization>). Not used for Bedrock, Agent Platform, or Foundry |
| `claude_code_oauth_token` | OAuth token for authenticating with a Claude subscription, generated with `claude setup-token` | No |
| `github_token` | Token for GitHub operations. When omitted, the Claude Code GitHub Action authenticates as the Claude GitHub App | No |
| `plugin_marketplaces` | Newline-separated list of plugin marketplace Git URLs | No |
| `plugins` | Newline-separated list of plugin names to install before execution | No |
| `settings` | Claude Code settings, as a JSON string or a path to a settings JSON file | No |
| `trigger_phrase` | Trigger phrase Claude responds to. Default: `@claude` | No |
| `use_bedrock` | Use Amazon Bedrock instead of the Claude API | No |
| `use_vertex` | Use Google Cloud's Agent Platform instead of the Claude API | No |
| `use_foundry` | Use Microsoft Foundry instead of the Claude API | No |

For the full input list, see the Claude Code GitHub Action's [configuration reference](<https://github.com/anthropics/claude-code-action/blob/main/docs/usage.md#inputs>).

### Pass CLI arguments

The `claude_args` parameter accepts any [Claude Code CLI argument](<https://code.claude.com/docs/en/cli-reference>):

```yaml theme={null}
claude_args: "--max-turns 5 --model claude-sonnet-5 --mcp-config /path/to/config.json"
```

Common arguments:

* `--max-turns`: limit the number of conversation turns
* `--model`: model to use, for example `claude-sonnet-5`. Without this argument, the Claude Code GitHub Action uses the Claude Code [default model](<https://code.claude.com/docs/en/model-config>)
* `--mcp-config`: path to [MCP configuration](<https://code.claude.com/docs/en/mcp>)
* `--allowedTools`: comma-separated list of allowed tools. The `--allowed-tools` alias also works
* `--debug`: enable debug output

## Upgrade from beta

If your workflows still reference `anthropics/claude-code-action@beta`, update them to v1:

1. Change `@beta` to `@v1` in the `uses` line
2. Remove the `mode` input, since the Claude Code GitHub Action now [detects the mode automatically](<#interactive-and-automation-modes>)
3. Replace `direct_prompt` with `prompt`
4. Move CLI options such as `max_turns` and `model` into `claude_args`. `custom_instructions` has no same-name flag and becomes `--append-system-prompt`

For the full input mapping and before-and-after examples, see the [migration guide](<https://github.com/anthropics/claude-code-action/blob/main/docs/migration-guide.md>).

## What's next

* [Use Claude Code GitHub Actions with cloud providers](<https://code.claude.com/docs/en/github-actions-cloud-providers>): route inference through Amazon Bedrock, Google Cloud's Agent Platform, or Microsoft Foundry
* [Configuration reference](<https://github.com/anthropics/claude-code-action/blob/main/docs/usage.md#inputs>): the full list of action inputs
* [Examples directory](<https://github.com/anthropics/claude-code-action/tree/main/examples>): ready-to-use workflows for more scenarios
* [Code Review](<https://code.claude.com/docs/en/code-review>): automatic pull request review without maintaining a workflow file


</details>
