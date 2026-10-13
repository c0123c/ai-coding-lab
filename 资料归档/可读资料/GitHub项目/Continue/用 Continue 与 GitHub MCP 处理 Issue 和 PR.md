# 用 Continue 与 GitHub MCP 处理 Issue 和 PR

工程教程。介绍问题分流、查找和评论、合并 PR 摘要、发布说明和自动化工作流，给出配置、令牌条件与排查；对应仓库协作和维护。

- 来源：Continue
- 资料类型：工程教程
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/bd36638ee0cf9fb90c314647bab2f1c6897aa6fe>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：令牌与写入权限按目标仓库配置。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/github-mcp-continue-cookbook.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/用 Continue 与 GitHub MCP 处理 Issue 和 PR.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "GitHub Issues and PRs with GitHub MCP and Continue"
description: "Use Continue and the GitHub MCP to list, summarize, and act on open issues and recently merged pull requests with natural language prompts."
sidebarTitle: "GitHub Issues with Continue"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### 你将构建什么

GitHub 工作流程助手，将 Continue 与 GitHub MCP 结合使用来：
- 列出、过滤和总结未解决的问题
- 回顾并总结最近合并的 PR
- 使用 AI- 生成的摘要或清单发表评论
- 通过无头 CLI 运行自动执行日常 GitHub 维护

## 先决条件

开始之前，请确保您拥有：

- Node.js 22+本地安装
- 一个 GitHub 帐户和一个可使用的存储库
- 具有适当范围的 GitHub 令牌：
  - 只读：`repo:read`
  - 对 issues/PRs 进行评论： `public_repo` 用于公共仓库或 `repo` 用于私人仓库

对于所有选项，首先：

#### 安装ContinueCLI

#### macOS/Linux

```bash
curl -fsSL https://raw.githubusercontent.com/continuedev/continue/main/extensions/cli/scripts/install.sh | bash
```

#### 窗户

```powershell
irm https://raw.githubusercontent.com/continuedev/continue/main/extensions/cli/scripts/install.ps1 | iex
```

#### npm（跨平台）

如果您已经有 Node.js 20+：
```bash
npm i -g @continuedev/cli
```

#### 配置GitHub令牌

将 `GITHUB_TOKEN` 设置为环境变量：
```bash
export GITHUB_TOKEN="your_token_here"
```

**注意：**

要在无头模式下使用 agents，您需要 [Continue API 密钥 ](<https://continue.dev/settings/api-keys>)。
对于写入操作（例如，发布评论），您的令牌必须包含相关的 GitHub 范围。

## GitHub MCP 工作流程选项

#### 最快的成功之路

使用 GitHub MCP 和 Continue CLI 进行快速设置。

确保满足上述 **先决条件 ** 后，您有两条途径可以开始：

#### ⚡ 快速入门（推荐）

#### 加载预建的Agent

运行：
```bash
cn --agent continuedev/github-project-manager-agent
```

确保设置了 `GITHUB_TOKEN` 环境变量。

#### 启动Agent

从您的存储库根目录：
```bash
cn --agent continuedev/github-project-manager-agent
```
现在尝试：“列出我标记为错误的未解决问题并总结优先级。”

**说明：**

您还可以将 MCP 附加到一次性会话：`cn --mcp anthropic/github-mcp`。

#### 🛠️手动设置

#### 添加GitHubMCP

将 GitHub MCP 添加到您的配置中。最小 YAML 示例：

**代码示例：** `config.yaml`

```yaml
name: My Config
version: 0.0.1
schema: v1

mcpServers:
  - name: GitHub MCP
    command: npx
    args:
      - "-y"
      - "@modelcontextprotocol/server-github"
    env:
      GITHUB_TOKEN: ${env:GITHUB_TOKEN}
    connectionTimeout: 30
```

注意事项：
- 通过环境变量提供`GITHUB_TOKEN`。

#### 测试连接

启动 Continue 并询问：
```
List the 5 most recently updated open issues in this repository.
```

#### Agent 要求

要将 GitHub MCP 与 Continue CLI 一起使用，您需要：
    - **Continue CLI 专业计划 ** 以及模型附加组件，OR
    - **您自己的API密钥**配置为环境变量

agent 将自动检测并使用您的配置以及 GitHub MCP 进行问题和 PR 操作。

---

## 问题工作流程

使用自然语言来探索、分类并针对悬而未决的问题采取行动。 agent 在底层调用 GitHub MCP 工具。

**说明：**

**在哪里运行这些工作流程：**
- **IDE 扩展 **：在 VS Code、JetBrains 或其他支持的 IDE 中使用 Continue
- **终端（TUI模式）**：运行`cn`进入交互模式，然后输入提示
- **CLI（无头模式）**：使用`cn -p "your prompt" --auto`进行自动化

要在无头模式下运行下面的任何示例提示，请使用 `cn -p "prompt"`

### 分类和总结

#### 总结未解决的问题

获取当前未决问题的优先概述。

**提示：**
```
List the 20 most recently updated open issues in this repo.
Cluster by label and severity. Summarize top priorities.
```

#### Owner/Label 过滤器

按标签或受让人缩小范围。

**提示：**
```
Show open issues labeled bug or security, assigned to @me.
Summarize blockers and suggest next steps.
```

### 采取行动

#### 对问题发表评论

发布 AI- 生成的状态更新或清单。

**提示：**
```
Find the issue with the most engagement (comments, reactions) in the last 30 days.
Come up with a triage plan and draft a comment with:
- current hypothesis
- next 2 steps
- owner and ETA
Then post the comment.
```

**注释：**

需要具有评论权限的令牌（`public_repo` 或 `repo`）。除非使用`--auto`，否则agent将在发布前确认。

#### 查找陈旧问题

找出需要注意的问题。

**提示：**
```
Find open issues with no activity in the last 30 days.
Suggest action (close, needs-repro, or prioritize) and draft comments.
```

---

## 合并的 PR 工作流程

分析最近合并的 PR，了解变更意识、发行说明和质量信号。

### 最近合并概述

#### 合并 PR 摘要

总结一段时间内合并的 PR。

**提示：**
```
List PRs merged in the last 7 days.
Group by area (label or path). Summarize impact and notable changes.
```

### 发行说明

#### 生成发行说明

将合并的 PR 转化为清晰的发行说明。

**提示：**
```
Generate release notes for PRs merged since tag v1.2.0.
Use keep-a-changelog sections and include PR numbers/authors.
```

### 深潜

#### 总结一个PR

获取最近合并的 PR 的人类可读摘要以及活跃的讨论。

**提示：**
```
Find the last merged PR with a significant amount of comments.
Summarize the conversation, including goal, key changes,
risk areas, and any follow-ups.
```

---

## 使用 GitHub 操作实现自动化

按计划或在 PR 中运行无头命令以使团队随时了解情况。

### 添加 GitHub 秘密

存储库设置 → 秘密和变量 → 操作：

- `CONTINUE_API_KEY`：来自[continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>)
- `GITHUB_TOKEN`：具有读取issues/PRs和发表评论权限的令牌

### 示例工作流程

创建`.github/workflows/github-mcp-reports.yml`：

```yaml
name: GitHub MCP Reports

on:
  schedule:
    - cron: "0 13 * * 1"  # Mondays 13:00 UTC
  workflow_dispatch:
  pull_request:
    types: [opened, synchronize]

jobs:
  report:
    runs-on: ubuntu-latest
    env:
      CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "18"
      - name: Install Continue CLI
        run: npm i -g @continuedev/cli

      - name: Weekly Issue Triage Summary
        if: github.event_name == 'schedule' || github.event_name == 'workflow_dispatch'
        run: |
          cn --agent continuedev/github-project-manager-agent \
             -p "List open issues updated in the last 14 days. Group by label and priority. Propose top 5 actions and include issue links." \
             --auto > issue_summary.txt

      - name: Post PR Context Comment
        if: github.event_name == 'pull_request'
        env:
          PR_NUMBER: ${{ github.event.pull_request.number }}
        run: |
          REPORT=$(        cn --agent continuedev/github-project-manager-agent \
            -p "Summarize the context for PR #${PR_NUMBER}: related open issues, risk areas, and test suggestions. Keep under 200 words." \
            --auto)
          gh pr comment ${PR_NUMBER} --body "$REPORT"
```

**说明：**

在整个自动化过程中一致使用 `continuedev/github-project-manager-agent` 作为推荐的 agent 配置。

---

## 故障排除

- **令牌缺失或无效：** 确保已设置 `GITHUB_TOKEN` 并具有所需的范围。尝试一个最小的测试：“在此存储库中列出 3 未解决的问题”。
- **评论权限错误：** 您的令牌必须包含 `public_repo`（公共）或 `repo`（私人）才能撰写评论。
- **速率限制：**降低频率或过滤查询；在 CI 中运行时，请考虑使用与默认操作令牌不同的 PAT。
- **MCP 连接超时：** 如果使用自定义 YAML，请增加 `connectionTimeout` 或验证配置中的 `command`/`args`。

## 你建造了什么

完成本指南后，您将拥有一个完整的 **AI- 支持的 GitHub 工作流程系统 **，该系统：

- ✅ 使用自然语言——复杂的GitHub操作的简单提示
- ✅ 自动进行问题分类 — AI 分析和总结未解决的问题
- ✅ 生成发行说明 - 自动 PR 摘要和变更日志
- ✅ 连续运行 — 通过 GitHub 操作自动生成报告

#### 连续AI

您的 GitHub 工作流程现在在 **[ 级别 2 连续 
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI 运行，通过审核和批准处理常规问题分类和 PR 在人工监督下的摘要 
。

## 下一步

1. **探索您的问题** - 尝试存储库上的问题分类提示
2. **生成发行说明** - 使用合并的 PR 提示创建变更日志
3. **设置自动化** - 将 GitHub 操作工作流程添加到您的存储库
4. **自定义提示** - 根据团队的工作流程定制提示
5. **监控进度** - 随着时间的推移跟踪问题解决指标

## 其他资源

#### [GitHub MCP 服务器](<https://github.com/modelcontextprotocol/servers/tree/main/src/github>)

Anthropic GitHub MCP 服务器

#### [MCP 在 Continue](<https://docs.continue.dev/guides/Continue_Continue MCP 设置、传输与凭据管理.md>) 中

MCP 如何与 Continue agents 配合使用

#### [GitHub 项目经理 Agent](<https://docs.continue.dev/guides/Continue_用 Continue 与 GitHub MCP 处理 Issue 和 PR.md>)

为 GitHub 工作流程预配置 agent

#### [GitHub MCP 服务器](<https://github.com/github/github-mcp-server/blob/main/README.md>)

官方GitHubMCP服务器README


## 原文对照

<details>
<summary>展开完整原文</summary>

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### What You'll Build

A GitHub workflow assistant that uses Continue with the GitHub MCP to:
- List, filter, and summarize open issues
- Review and summarize recently merged PRs
- Post comments with AI-generated summaries or checklists
- Automate routine GitHub maintenance with headless CLI runs

## Prerequisites

Before starting, ensure you have:

- Node.js 22+ installed locally
- A GitHub account and a repository to work with
- A GitHub token with the appropriate scopes:
  - For read-only: `repo:read`
  - To comment on issues/PRs: `public_repo` for public repos or `repo` for private repos

For all options, first:

#### Install Continue CLI

#### macOS / Linux

```bash
curl -fsSL https://raw.githubusercontent.com/continuedev/continue/main/extensions/cli/scripts/install.sh | bash
```

#### Windows

```powershell
irm https://raw.githubusercontent.com/continuedev/continue/main/extensions/cli/scripts/install.ps1 | iex
```

#### npm (cross-platform)

If you already have Node.js 20+:
```bash
npm i -g @continuedev/cli
```

#### Configure GitHub Token

Set your `GITHUB_TOKEN` as an environment variable:
```bash
export GITHUB_TOKEN="your_token_here"
```

**注意：**

To use agents in headless mode, you need a [Continue API key](<https://continue.dev/settings/api-keys>).
For write actions (e.g., posting comments), your token must include the relevant GitHub scopes.

## GitHub MCP Workflow Options

#### Fastest Path to Success

Use the GitHub MCP with Continue CLI for quick setup.

After ensuring you meet the **Prerequisites** above, you have two paths to get started:

#### ⚡ Quick Start (Recommended)

#### Load the Pre-Built Agent

Run:
```bash
cn --agent continuedev/github-project-manager-agent
```

Make sure your `GITHUB_TOKEN` environment variable is set.

#### Launch the Agent

From your repo root:
```bash
cn --agent continuedev/github-project-manager-agent
```
Now try: "List my open issues labeled bug and summarize priorities."

**说明：**

You can also attach an MCP to a one-off session: `cn --mcp anthropic/github-mcp`.

#### 🛠️ Manual Setup

#### Add GitHub MCP

Add the GitHub MCP to your configuration. Minimal YAML example:

**代码示例：** `config.yaml`

```yaml
name: My Config
version: 0.0.1
schema: v1

mcpServers:
  - name: GitHub MCP
    command: npx
    args:
      - "-y"
      - "@modelcontextprotocol/server-github"
    env:
      GITHUB_TOKEN: ${env:GITHUB_TOKEN}
    connectionTimeout: 30
```

Notes:
- Provide `GITHUB_TOKEN` via environment variables.

#### Test the Connection

Launch Continue and ask:
```
List the 5 most recently updated open issues in this repository.
```

#### Agent Requirements

  To use GitHub MCP with Continue CLI, you need either:
    - **Continue CLI Pro Plan** with the models add-on, OR
    - **Your own API keys** configured as environment variables

  The agent will automatically detect and use your configuration along with the GitHub MCP for issue and PR operations.

---

## Issue Workflows

Use natural language to explore, triage, and act on open issues. The agent calls GitHub MCP tools under the hood.

**说明：**

**Where to run these workflows:**
- **IDE Extensions**: Use Continue in VS Code, JetBrains, or other supported IDEs
- **Terminal (TUI mode)**: Run `cn` to enter interactive mode, then type your prompts
- **CLI (headless mode)**: Use `cn -p "your prompt" --auto` for automation

To run any of the example prompts below in headless mode, use `cn -p "prompt"`

### Triage and Summaries

#### Summarize Open Issues

Get a prioritized overview of current open issues.

**Prompt:**
```
List the 20 most recently updated open issues in this repo.
Cluster by label and severity. Summarize top priorities.
```

#### Owner/Label Filters

Narrow by label or assignee.

**Prompt:**
```
Show open issues labeled bug or security, assigned to @me.
Summarize blockers and suggest next steps.
```

### Taking Action

#### Comment on an Issue

Post an AI-generated status update or checklist.

**Prompt:**
```
Find the issue with the most engagement (comments, reactions) in the last 30 days.
Come up with a triage plan and draft a comment with:
- current hypothesis
- next 2 steps
- owner and ETA
Then post the comment.
```

**注释：**

Requires a token with permission to comment (`public_repo` or `repo`). The agent will confirm before posting unless `--auto` is used.

#### Find Stale Issues

Identify issues that need attention.

**Prompt:**
```
Find open issues with no activity in the last 30 days.
Suggest action (close, needs-repro, or prioritize) and draft comments.
```

---

## Merged PR Workflows

Analyze recently merged PRs for change awareness, release notes, and quality signals.

### Recently Merged Overview

#### Merged PR Digest

Summarize merged PRs over a time window.

**Prompt:**
```
List PRs merged in the last 7 days.
Group by area (label or path). Summarize impact and notable changes.
```

### Release Notes

#### Generate Release Notes

Turn merged PRs into crisp release notes.

**Prompt:**
```
Generate release notes for PRs merged since tag v1.2.0.
Use keep-a-changelog sections and include PR numbers/authors.
```

### Deep Dives

#### Summarize a PR

Get a human-readable summary of a recently merged PR with active discussion.

**Prompt:**
```
Find the last merged PR with a significant amount of comments.
Summarize the conversation, including goal, key changes,
risk areas, and any follow-ups.
```

---

## Automate with GitHub Actions

Run headless commands on a schedule or in PRs to keep teams informed.

### Add GitHub Secrets

Repository Settings → Secrets and variables → Actions:

- `CONTINUE_API_KEY`: From [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>)
- `GITHUB_TOKEN`: A token with permissions to read issues/PRs and post comments

### Example Workflow

Create `.github/workflows/github-mcp-reports.yml`:

```yaml
name: GitHub MCP Reports

on:
  schedule:
    - cron: "0 13 * * 1"  # Mondays 13:00 UTC
  workflow_dispatch:
  pull_request:
    types: [opened, synchronize]

jobs:
  report:
    runs-on: ubuntu-latest
    env:
      CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "18"
      - name: Install Continue CLI
        run: npm i -g @continuedev/cli

      - name: Weekly Issue Triage Summary
        if: github.event_name == 'schedule' || github.event_name == 'workflow_dispatch'
        run: |
          cn --agent continuedev/github-project-manager-agent \
             -p "List open issues updated in the last 14 days. Group by label and priority. Propose top 5 actions and include issue links." \
             --auto > issue_summary.txt

      - name: Post PR Context Comment
        if: github.event_name == 'pull_request'
        env:
          PR_NUMBER: ${{ github.event.pull_request.number }}
        run: |
          REPORT=$(        cn --agent continuedev/github-project-manager-agent \
            -p "Summarize the context for PR #${PR_NUMBER}: related open issues, risk areas, and test suggestions. Keep under 200 words." \
            --auto)
          gh pr comment ${PR_NUMBER} --body "$REPORT"
```

**说明：**

Use `continuedev/github-project-manager-agent` consistently throughout your automation as the recommended agent configuration.

---

## Troubleshooting

- **Missing or invalid token:** Ensure `GITHUB_TOKEN` is set and has the required scopes. Try a minimal test: “List 3 open issues in this repo”.
- **Permissions errors on comment:** Your token must include `public_repo` (public) or `repo` (private) to write comments.
- **Rate limiting:** Reduce frequency or filter queries; consider using a PAT distinct from the default Actions token when running in CI.
- **MCP connection timeout:** If using custom YAML, increase `connectionTimeout` or verify the `command`/`args` in your configuration.

## What You've Built

After completing this guide, you have a complete **AI-powered GitHub workflow system** that:

- ✅ Uses natural language — Simple prompts for complex GitHub operations
- ✅ Automates issue triage — AI analyzes and summarizes open issues
- ✅ Generates release notes — Automatic PR digests and changelogs
- ✅ Runs continuously — Automated reports via GitHub Actions

#### Continuous AI

Your GitHub workflow now operates at **[Level 2 Continuous
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI handles routine issue triage and PR summaries with human oversight
through review and approval.

## Next Steps

1. **Explore your issues** - Try the issue triage prompts on your repository
2. **Generate release notes** - Use the merged PR prompts to create changelogs
3. **Set up automation** - Add the GitHub Actions workflow to your repo
4. **Customize prompts** - Tailor the prompts to your team's workflow
5. **Monitor progress** - Track issue resolution metrics over time

## Additional Resources

#### [GitHub MCP Server](<https://github.com/modelcontextprotocol/servers/tree/main/src/github>)

Anthropic GitHub MCP server

#### [MCP in Continue](<https://docs.continue.dev/guides/Continue_Continue MCP 设置、传输与凭据管理.md>)

How MCP works with Continue agents

#### [GitHub Project Manager Agent](<https://docs.continue.dev/guides/Continue_用 Continue 与 GitHub MCP 处理 Issue 和 PR.md>)

Pre-configured agent for GitHub workflows

#### [GitHub MCP Server](<https://github.com/github/github-mcp-server/blob/main/README.md>)

Official GitHub MCP server README


</details>
