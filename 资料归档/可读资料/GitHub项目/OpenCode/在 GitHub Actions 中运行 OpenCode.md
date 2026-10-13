# 在 GitHub Actions 中运行 OpenCode

工程指南。通过 Issue、PR 评论、计划任务等事件触发 OpenCode，展示安装、权限、Secrets、模型和自定义提示配置，对应仓库自动开发与维护。

- 来源：OpenCode
- 资料类型：工程指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-08-24](<https://github.com/anomalyco/opencode/commit/18b4cb6819d7de0b37927fef60d03927e678c9dd>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：执行位于 Actions Runner，外部模型及仓库写权限需按配置理解。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/github.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/在 GitHub Actions 中运行 OpenCode.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: GitHub
description: Use OpenCode in GitHub issues and pull-requests.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 与您的 GitHub 工作流程集成。在评论中提及 `/opencode` 或 `/oc`，OpenCode 将在您的 GitHub 操作运行器中执行任务。

---

## 特点

- **分类问题**：请OpenCode调查问题并向您解释。
- **修复并实施**：要求OpenCode修复问题或实施功能。它将在新分支中工作并提交包含所有更改的 PR。
- **安全**：OpenCode在你的GitHub的跑步者内部运行。

---

## 安装

在 GitHub 存储库中的项目中运行以下命令：

```bash
opencode github install
```

这将引导您完成安装 GitHub 应用程序、创建工作流程和设置机密。

---

### 手动设置

或者您可以手动设置。

1. **安装GitHub应用程序**

前往 [**github.com/apps/opencode-agent**](<https://github.com/apps/opencode-agent>)。确保它已安装在目标存储库上。

2. **添加工作流程**

将以下工作流程文件添加到存储库中的 `.github/workflows/opencode.yml`。确保在 `env` 中设置适当的 `model` 和所需的 API 键。

**代码示例：** `.github/workflows/opencode.yml`

```yml
name: opencode

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]

jobs:
  opencode:
    if: |
      contains(github.event.comment.body, '/oc') ||
      contains(github.event.comment.body, '/opencode')
    runs-on: ubuntu-latest
    permissions:
      id-token: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v6
        with:
          fetch-depth: 1
          persist-credentials: false

      - name: Run OpenCode
        uses: anomalyco/opencode/github@latest
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        with:
          model: anthropic/claude-sonnet-4-20250514
          # share: true
```

3. **将 API 密钥存储在机密中 **

在您的组织或项目的**设置**中，展开左侧的**秘密和变量**，然后选择**操作**。并添加所需的API键。

---

## 配置

- `model`：与OpenCode一起使用的型号。格式为`provider/model`。这是**必需的**。
- `agent`：要使用的agent。必须是主要 agent。如果未找到，则从配置回退到 `default_agent` 或 `"build"`。
- `share`：是否共享OpenCode会话。对于公共存储库，默认为 **true**。
- `prompt`：可选的自定义提示以覆盖默认行为。使用它来自定义 OpenCode 处理请求的方式。
- `mentions`：以逗号分隔的触发短语列表，不区分大小写。默认为 `/opencode,/oc`。
- `variant`：特定于提供者的推理工作的模型变体，例如 `high`、`max` 或 `minimal`。
- `oidc_base_url`：OIDC代币交换API的基础URL。仅在运行自定义 GitHub 应用程序安装时需要。默认为 `https://api.opencode.ai`。
- `use_github_token`：设置为 `true` 以使用调用者提供的 `GITHUB_TOKEN`，而不是将 OIDC 令牌交换为 OpenCode 应用程序安装令牌。默认为 `false`。

使用此模式运行无需安装OpenCodeGitHub应用程序。通过 `env` 传递令牌并授予您的工作流程所需的权限：

```yaml
permissions:
  contents: write
  pull-requests: write
  issues: write

steps:
  - uses: anomalyco/opencode/github@latest
    env:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    with:
      model: anthropic/claude-sonnet-4-20250514
      use_github_token: true
```

此模式下不需要 `id-token: write`，因为会跳过 OIDC 交换。要使用 [ 个人访问令牌 ](<https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens>) 或其他 GitHub 应用程序令牌，请将其存储为机密，并将该机密作为 `GITHUB_TOKEN` 传递。

---

## 支持的活动

OpenCode可由以下GitHub事件触发：

|事件类型|触发者|详情|
| ----------------------------- | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `issue_comment`               |对问题发表评论或 PR|在评论中提及 `/opencode` 或 `/oc`。 OpenCode 读取上下文并可以创建分支、打开 PR 或回复。|
| `pull_request_review_comment` |对 PR 中的特定代码行进行评论|在检查代码时提及 `/opencode` 或 `/oc`。 OpenCode 接收文件路径、行号和差异上下文。|
| `issues`                      |已打开或已编辑的问题|创建或修改问题时自动触发OpenCode。需要输入 `prompt`。|
| `pull_request`                |PR 已打开或已更新|PR 打开、同步或重新打开时自动触发 OpenCode。对于自动评论很有用。|
| `schedule`                    |基于 Cron 的计划|按计划运行 OpenCode。需要输入 `prompt`。输出进入日志和 PR（没有可评论的问题）。|
| `workflow_dispatch`           |从 GitHub UI 手动触发|通过“操作”选项卡按需触发 OpenCode。需要输入 `prompt`。输出进入日志和 PR。|

### 时间表示例

按计划运行 OpenCode 以执行自动化任务：

**代码示例：** `.github/workflows/opencode-scheduled.yml`

```yaml
name: Scheduled OpenCode Task

on:
  schedule:
    - cron: "0 9 * * 1" # Every Monday at 9am UTC

jobs:
  opencode:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: write
      pull-requests: write
      issues: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v6
        with:
          persist-credentials: false

      - name: Run OpenCode
        uses: anomalyco/opencode/github@latest
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        with:
          model: anthropic/claude-sonnet-4-20250514
          prompt: |
            Review the codebase for any TODO comments and create a summary.
            If you find issues worth addressing, open an issue to track them.
```

对于计划的事件，`prompt` 输入是 ** 必需的 **，因为没有注释可从中提取指令。计划工作流在没有用户上下文的情况下运行以进行权限检查，因此如果您希望 OpenCode 创建分支或 PR，工作流必须授予 `contents: write` 和 `pull-requests: write`。

---

### 拉取请求示例

打开或更新 PR 时自动审核：

**代码示例：** `.github/workflows/opencode-review.yml`

```yaml
name: opencode-review

on:
  pull_request:
    types: [opened, synchronize, reopened, ready_for_review]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
      pull-requests: read
      issues: read
    steps:
      - uses: actions/checkout@v6
        with:
          persist-credentials: false
      - uses: anomalyco/opencode/github@latest
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          model: anthropic/claude-sonnet-4-20250514
          use_github_token: true
          prompt: |
            Review this pull request:
            - Check for code quality issues
            - Look for potential bugs
            - Suggest improvements
```

对于 `pull_request` 事件，如果未提供 `prompt`，则 OpenCode 默认审核拉取请求。

---

### 问题分类示例

自动分类新问题。此示例过滤到早于 30 天的帐户以减少垃圾邮件：

**代码示例：** `.github/workflows/opencode-triage.yml`

```yaml
name: Issue Triage

on:
  issues:
    types: [opened]

jobs:
  triage:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: write
      pull-requests: write
      issues: write
    steps:
      - name: Check account age
        id: check
        uses: actions/github-script@v7
        with:
          script: |
            const user = await github.rest.users.getByUsername({
              username: context.payload.issue.user.login
            });
            const created = new Date(user.data.created_at);
            const days = (Date.now() - created) / (1000 * 60 * 60 * 24);
            return days >= 30;
          result-encoding: string

      - uses: actions/checkout@v6
        if: steps.check.outputs.result == 'true'
        with:
          persist-credentials: false

      - uses: anomalyco/opencode/github@latest
        if: steps.check.outputs.result == 'true'
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        with:
          model: anthropic/claude-sonnet-4-20250514
          prompt: |
            Review this issue. If there's a clear fix or relevant docs:
            - Provide documentation links
            - Add error handling guidance for code examples
            Otherwise, do not comment.
```

对于 `issues` 事件，`prompt` 输入是 ** 必需的 **，因为没有注释可从中提取指令。

---

## 自定义提示

覆盖默认提示，为您的工作流程自定义 OpenCode 的行为。

**代码示例：** `.github/workflows/opencode.yml`

```yaml
- uses: anomalyco/opencode/github@latest
  with:
    model: anthropic/claude-sonnet-4-5
    prompt: |
      Review this pull request:
      - Check for code quality issues
      - Look for potential bugs
      - Suggest improvements
```

这对于执行与您的项目相关的特定审查标准、编码标准或重点领域非常有用。

---

## 示例

以下是如何在 GitHub 中使用 OpenCode 的一些示例。

- **解释一个问题**

在 GitHub 问题中添加此评论。

```
/opencode explain this issue
```

OpenCode 将阅读整个帖子，包括所有评论，并回复并提供清晰的解释。

- **修复问题**

在 GitHub 问题中，说：

```
/opencode fix this
```

OpenCode 将创建一个新分支，实施更改，并打开包含更改的 PR。

- **查看 PR 并进行更改**

在 GitHub PR 上留下以下评论。

```
Delete the attachment from S3 when the note is removed /oc
```

OpenCode 将实施请求的更改并将其提交到相同的 PR。

- **查看具体代码行**

直接在 PR 的“文件”选项卡中的代码行上留下评论。 OpenCode 自动检测文件、行号和差异上下文以提供精确的响应。

```
[Comment on specific lines in Files tab]
/oc add error handling here
```

当评论特定行时，OpenCode 收到：
  - 正在审查的确切文件
  - 具体代码行
  - 周围的 diff 上下文
  - 行号信息

这允许更有针对性的请求，而无需手动指定文件路径或行号。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode integrates with your GitHub workflow. Mention `/opencode` or `/oc` in your comment, and OpenCode will execute tasks within your GitHub Actions runner.

---

## Features

- **Triage issues**: Ask OpenCode to look into an issue and explain it to you.
- **Fix and implement**: Ask OpenCode to fix an issue or implement a feature. And it will work in a new branch and submits a PR with all the changes.
- **Secure**: OpenCode runs inside your GitHub's runners.

---

## Installation

Run the following command in a project that is in a GitHub repo:

```bash
opencode github install
```

This will walk you through installing the GitHub app, creating the workflow, and setting up secrets.

---

### Manual Setup

Or you can set it up manually.

1. **Install the GitHub app**

   Head over to [**github.com/apps/opencode-agent**](<https://github.com/apps/opencode-agent>). Make sure it's installed on the target repository.

2. **Add the workflow**

   Add the following workflow file to `.github/workflows/opencode.yml` in your repo. Make sure to set the appropriate `model` and required API keys in `env`.

**代码示例：** `.github/workflows/opencode.yml`

```yml
name: opencode

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]

jobs:
  opencode:
    if: |
      contains(github.event.comment.body, '/oc') ||
      contains(github.event.comment.body, '/opencode')
    runs-on: ubuntu-latest
    permissions:
      id-token: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v6
        with:
          fetch-depth: 1
          persist-credentials: false

      - name: Run OpenCode
        uses: anomalyco/opencode/github@latest
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        with:
          model: anthropic/claude-sonnet-4-20250514
          # share: true
```

3. **Store the API keys in secrets**

   In your organization or project **settings**, expand **Secrets and variables** on the left and select **Actions**. And add the required API keys.

---

## Configuration

- `model`: The model to use with OpenCode. Takes the format of `provider/model`. This is **required**.
- `agent`: The agent to use. Must be a primary agent. Falls back to `default_agent` from config or `"build"` if not found.
- `share`: Whether to share the OpenCode session. Defaults to **true** for public repositories.
- `prompt`: Optional custom prompt to override the default behavior. Use this to customize how OpenCode processes requests.
- `mentions`: Comma-separated list of trigger phrases, case-insensitive. Defaults to `/opencode,/oc`.
- `variant`: Model variant for provider-specific reasoning effort, for example `high`, `max`, or `minimal`.
- `oidc_base_url`: Base URL for the OIDC token exchange API. Only needed when running a custom GitHub App install. Defaults to `https://api.opencode.ai`.
- `use_github_token`: Set to `true` to use a caller-provided `GITHUB_TOKEN` instead of exchanging an OIDC token for an OpenCode App installation token. Defaults to `false`.

  Use this mode to run without installing the OpenCode GitHub App. Pass the token through `env` and grant the permissions required by your workflow:

```yaml
permissions:
  contents: write
  pull-requests: write
  issues: write

steps:
  - uses: anomalyco/opencode/github@latest
    env:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    with:
      model: anthropic/claude-sonnet-4-20250514
      use_github_token: true
```

  `id-token: write` is not required in this mode because OIDC exchange is skipped. To use a [personal access token](<https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens>) or another GitHub App token, store it as a secret and pass that secret as `GITHUB_TOKEN` instead.

---

## Supported Events

OpenCode can be triggered by the following GitHub events:

| Event Type                    | Triggered By                           | Details                                                                                                           |
| ----------------------------- | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `issue_comment`               | Comment on an issue or PR              | Mention `/opencode` or `/oc` in your comment. OpenCode reads context and can create branches, open PRs, or reply. |
| `pull_request_review_comment` | Comment on specific code lines in a PR | Mention `/opencode` or `/oc` while reviewing code. OpenCode receives file path, line numbers, and diff context.   |
| `issues`                      | Issue opened or edited                 | Automatically trigger OpenCode when issues are created or modified. Requires `prompt` input.                      |
| `pull_request`                | PR opened or updated                   | Automatically trigger OpenCode when PRs are opened, synchronized, or reopened. Useful for automated reviews.      |
| `schedule`                    | Cron-based schedule                    | Run OpenCode on a schedule. Requires `prompt` input. Output goes to logs and PRs (no issue to comment on).        |
| `workflow_dispatch`           | Manual trigger from GitHub UI          | Trigger OpenCode on demand via Actions tab. Requires `prompt` input. Output goes to logs and PRs.                 |

### Schedule Example

Run OpenCode on a schedule to perform automated tasks:

**代码示例：** `.github/workflows/opencode-scheduled.yml`

```yaml
name: Scheduled OpenCode Task

on:
  schedule:
    - cron: "0 9 * * 1" # Every Monday at 9am UTC

jobs:
  opencode:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: write
      pull-requests: write
      issues: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v6
        with:
          persist-credentials: false

      - name: Run OpenCode
        uses: anomalyco/opencode/github@latest
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        with:
          model: anthropic/claude-sonnet-4-20250514
          prompt: |
            Review the codebase for any TODO comments and create a summary.
            If you find issues worth addressing, open an issue to track them.
```

For scheduled events, the `prompt` input is **required** since there's no comment to extract instructions from. Scheduled workflows run without a user context to permission-check, so the workflow must grant `contents: write` and `pull-requests: write` if you expect OpenCode to create branches or PRs.

---

### Pull Request Example

Automatically review PRs when they are opened or updated:

**代码示例：** `.github/workflows/opencode-review.yml`

```yaml
name: opencode-review

on:
  pull_request:
    types: [opened, synchronize, reopened, ready_for_review]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
      pull-requests: read
      issues: read
    steps:
      - uses: actions/checkout@v6
        with:
          persist-credentials: false
      - uses: anomalyco/opencode/github@latest
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          model: anthropic/claude-sonnet-4-20250514
          use_github_token: true
          prompt: |
            Review this pull request:
            - Check for code quality issues
            - Look for potential bugs
            - Suggest improvements
```

For `pull_request` events, if no `prompt` is provided, OpenCode defaults to reviewing the pull request.

---

### Issues Triage Example

Automatically triage new issues. This example filters to accounts older than 30 days to reduce spam:

**代码示例：** `.github/workflows/opencode-triage.yml`

```yaml
name: Issue Triage

on:
  issues:
    types: [opened]

jobs:
  triage:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: write
      pull-requests: write
      issues: write
    steps:
      - name: Check account age
        id: check
        uses: actions/github-script@v7
        with:
          script: |
            const user = await github.rest.users.getByUsername({
              username: context.payload.issue.user.login
            });
            const created = new Date(user.data.created_at);
            const days = (Date.now() - created) / (1000 * 60 * 60 * 24);
            return days >= 30;
          result-encoding: string

      - uses: actions/checkout@v6
        if: steps.check.outputs.result == 'true'
        with:
          persist-credentials: false

      - uses: anomalyco/opencode/github@latest
        if: steps.check.outputs.result == 'true'
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        with:
          model: anthropic/claude-sonnet-4-20250514
          prompt: |
            Review this issue. If there's a clear fix or relevant docs:
            - Provide documentation links
            - Add error handling guidance for code examples
            Otherwise, do not comment.
```

For `issues` events, the `prompt` input is **required** since there's no comment to extract instructions from.

---

## Custom prompts

Override the default prompt to customize OpenCode's behavior for your workflow.

**代码示例：** `.github/workflows/opencode.yml`

```yaml
- uses: anomalyco/opencode/github@latest
  with:
    model: anthropic/claude-sonnet-4-5
    prompt: |
      Review this pull request:
      - Check for code quality issues
      - Look for potential bugs
      - Suggest improvements
```

This is useful for enforcing specific review criteria, coding standards, or focus areas relevant to your project.

---

## Examples

Here are some examples of how you can use OpenCode in GitHub.

- **Explain an issue**

  Add this comment in a GitHub issue.

```
/opencode explain this issue
```

  OpenCode will read the entire thread, including all comments, and reply with a clear explanation.

- **Fix an issue**

  In a GitHub issue, say:

```
/opencode fix this
```

  And OpenCode will create a new branch, implement the changes, and open a PR with the changes.

- **Review PRs and make changes**

  Leave the following comment on a GitHub PR.

```
Delete the attachment from S3 when the note is removed /oc
```

  OpenCode will implement the requested change and commit it to the same PR.

- **Review specific code lines**

  Leave a comment directly on code lines in the PR's "Files" tab. OpenCode automatically detects the file, line numbers, and diff context to provide precise responses.

```
[Comment on specific lines in Files tab]
/oc add error handling here
```

  When commenting on specific lines, OpenCode receives:
  - The exact file being reviewed
  - The specific lines of code
  - The surrounding diff context
  - Line number information

  This allows for more targeted requests without needing to specify file paths or line numbers manually.


</details>
