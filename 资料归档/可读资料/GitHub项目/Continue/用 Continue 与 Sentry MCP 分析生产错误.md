# 用 Continue 与 Sentry MCP 分析生产错误

工程教程。配置凭据和工具，以自然语言分析异常、定位原因、生成修复建议和 GitHub Issue，并用 Actions 组织持续监测，说明安全和排查条件。

- 来源：Continue
- 资料类型：工程教程
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/f7f3ab7440341f052642b7d0fab72d40fd8e7ad0>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：AI 根因和修复建议需要结合代码、日志和复现验证。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/sentry-mcp-error-monitoring.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/用 Continue 与 Sentry MCP 分析生产错误.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Automated Error Analysis with Sentry MCP"
description: "Build an AI-powered error monitoring workflow that analyzes Sentry issues, identifies patterns, and creates actionable GitHub issues automatically."
sidebarTitle: "Sentry Automated Error Analysis with MCP"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### 你将构建什么

一个自动错误监控系统，使用 Continue CLI 和 Sentry MCP 来分析生产错误，使用 AI 识别根本原因，并使用建议的修复创建详细的 GitHub 问题。

## 你将学到什么

这本食谱教你：

- 使用 [Sentry MCP](<https://docs.sentry.io/product/sentry-mcp/>) 访问 [ 问题 ](<https://docs.sentry.io/product/issues/>)
- 使用 AI 分析错误模式和堆栈跟踪

- 通过根本原因分析自动创建 GitHub 问题
- 使用 GitHub 操作设置连续错误监控

## 先决条件

开始之前，请确保您拥有：

- GitHub 要在其中创建问题的存储库
- [Sentry 帐户 ](<https://sentry.io>) 具有正在收集错误的活动项目
- Node.js 18+本地安装
- [Continue CLI](<https://docs.continue.dev/guides/cli>) 具有 ** 有效积分 **（使用 API 时需要）
- [GitHub CLI](<https://cli.github.com/>) 已安装（`gh` 命令）

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

#### 设置 Continue CLI 账户 & API 密钥

1. 访问[Continue组织](<https://continue.dev/settings/organizations>)
2. 注册或登录您的Continue帐户
3. 导航至您的组织设置
4. 单击**“API密钥”**，然后单击**“+新API密钥”**
5. 立即复制 API 键（您将不会再看到它！）
6. 登录【CLI】：【`cn login`】

**提示：**

Continue CLI 处理复杂的错误分析和 API 交互 - 您只需要提供正确的提示即可！

## 步骤 1：设置您的凭据

首先，您需要收集您的 Sentry 和 GitHub API 凭据。

#### 配置哨兵 MCP

**提示：**

有关详细配置选项，请参阅 [Sentry MCP 文档 ](<https://docs.sentry.io/product/sentry-mcp/>)

Sentry MCP 支持多种配置方法。对于Continue CLI，建议使用OAuth：

**选项1：OAuth配置（推荐）**

Sentry MCP 首次使用时会提示进行 OAuth 身份验证。只需遵循授权流程即可。

**选项2：STDIO带身份验证令牌的模式**

对于本地开发或自托管Sentry安装，您可以使用STDIO模式：
```bash
npx @sentry/mcp-server@latest --access-token=YOUR_SENTRY_TOKEN --host=sentry.io
```

或者使用环境变量：
```bash
SENTRY_ACCESS_TOKEN=your-token SENTRY_HOST=sentry.io
```

**说明：**

`--host` 参数是必需的，并且应指向您的 Sentry 实例（例如，`sentry.io` 或 `sentry.example.com` 用于自托管）。

#### 哨兵 API 凭证

您需要 **Sentry 用户身份验证令牌 ** 来访问问题和错误数据：

1. Sentry 中的 Go 到 [用户身份验证令牌 ](<https://sentry.io/settings/account/api/auth-tokens/>)
   - 对于自托管 Sentry，请使用：`https://YOUR-SENTRY-DOMAIN/settings/account/api/auth-tokens/`
2. 点击**创建新令牌**
3. 将其命名为“Continue CLI错误分析”
4. **选择这些权限范围**（完整功能所需）：
   - `org:read` - **必填** - 访问组织信息
   - `project:read` - **必需** - 读取项目配置
   - `project:releases` - **必需** - 访问发布信息以进行部署跟踪
   - `event:read` - **必需** - 读取详细的错误事件数据和堆栈跟踪
   - `event:write` - 可选 - 更新错误事件（用于标记为已解决）
   - `member:read` - 推荐 - 读取团队成员信息以进行自动分配
   - `team:read` - 推荐 - 访问团队数据以解决路由问题
5. 立即复制令牌（您将不会再看到它！）
6. 记下您的组织 slug（在您的 Sentry URL 中找到：`https://sentry.io/organizations/YOUR-ORG-SLUG`）
7. 记下您的 Sentry 主机 URL（通常为 `https://sentry.io` 或您的自托管域）

**说明：**

**Sentry MCP 连接**：MCP 服务器通过 OAuth 连接到 `https://mcp.sentry.dev/mcp` 并安全地处理身份验证。对于本地开发，您可以将 STDIO 模式与您的身份验证令牌一起使用。

#### 设置 GitHub CLI 身份验证

GitHub CLI 自动处理身份验证 - 无需手动 PAT：

1. 如果尚未安装，请安装 GitHub CLI
2. 运行`gh auth login`并按照提示操作
3. 选择身份验证方法（浏览器或令牌）
4. 出现提示时授予必要的权限（创建问题时需要 `issues:write` ****）

## Sentry 错误监控工作流程选项

**说明：**

**Sentry MCP 的工作原理**：
- 通过 OAuth 连接到您的 Sentry 组织
- 提供用于访问问题、项目、团队和 DSN 的工具
- 支持托管 (`https://mcp.sentry.dev`) 和自托管 Sentry 实例
- 自动处理身份验证和 API 交互

#### 将哨兵 MCP 添加到 Continue CLI

使用 OAuth 配置 [Sentry MCP](<https://docs.sentry.io/product/sentry-mcp/>)：

MCP服务器在您首次使用时会自动提示进行OAuth认证。

#### 验证哨兵连接

使用以下提示测试您的 Sentry MCP 连接：

```
List my Sentry organizations and projects
```

#### 创建自定义错误分析提示

使用此提示模板与 Continue CLI 来分析 Sentry 错误：

```
Analyze Sentry errors from the past 24 hours:
- Group errors by root cause
- Identify the top 5 most critical issues by frequency and impact
- For each critical issue, provide:
  * Stack trace analysis
  * Affected user count
  * First seen and last seen timestamps
  * Suggested fix based on error context
- Create GitHub issues using gh CLI with:
  * Title format: '🐛 [Sentry] [Error Type]: Brief description'
  * Labels: 'bug', 'sentry', 'production'
  * Priority labels based on severity
  * Full error context and suggested fix in the body
Execute the commands and confirm each issue was created.
```

**说明：**

**为什么GitHub CLI优于GitHub MCP**：虽然GitHub MCP可用，但运行起来可能
令牌昂贵。 `gh` CLI 效率更高，不需要 API 令牌 
（通过 `gh auth login` 验证），并提供更清晰的命令行 
 体验。如果您更喜欢完整的 MCP 集成，GitHub MCP 仍然是一个选项。

---

**注意：**

**所需的存储库标签**：确保您的GitHub存储库具有以下标签：
- `bug`, `sentry`, `production`
- `critical`, `high-priority`, `medium-priority`, `low-priority`
- `needs-investigation`, `has-fix`

在您的存储库中创建缺失的标签：**设置→标签→新标签**

## 步骤2：使用AI分析哨兵错误

使用【Continue】【CLI】进行智能错误分析。在 Continue CLI TUI 中输入以下提示：

**说明：**

要在无头模式下运行下面的任何示例提示，请使用 `cn -p "prompt"`

#### 最近的错误分析

**提示：**
```
Show me Sentry errors from the past 7 days, grouped by error type, with frequency counts
```

#### 严重错误调查

**提示：**
```
Find the most critical Sentry error affecting the most users in production and provide:
- Full stack trace analysis
- Affected user count and browser/OS breakdown
- Timeline of when the error started occurring
- Similar historical issues from Sentry
- Root cause hypothesis based on code context
- Suggested fix with code examples
```

#### 性能问题检测

**提示：**
```
Analyze Sentry performance data to identify:
- Slowest transactions in the past 24 hours
- Database queries with high latency
- API endpoints with degraded performance
- Suggested optimizations for each issue
```

**说明：**

**可用的Sentry MCP工具**：
- **组织**：访问组织级别数据和设置
- **Projects**：查询项目及其配置
- **Issues**：搜索并分析错误问题
- **团队**：管理团队分配
- **DSN**：检索项目 DSN 配置

## 步骤 3：自动创建 GitHub 问题

根据 Sentry 错误创建可操作的 GitHub 问题。在Continue CLI TUI中输入此提示：

**提示：**
```
For each unresolved Sentry error with 'critical' or 'high' severity:
1. Analyze the error using Sentry MCP
2. Create a GitHub issue with gh CLI:
   - Title: '🐛 [Sentry] [Error Type]: Brief description'
   - Body with:
     * Error summary and impact (affected users, frequency)
     * Full stack trace
     * Environment details (browser, OS, release version)
     * Link to Sentry issue
     * Root cause analysis from AI
     * Suggested fix with code snippets
     * Related Sentry issues
   - Labels: 'bug', 'sentry', 'production', and severity label
   - Assignees: Team member based on code ownership
3. Update Sentry issue with GitHub issue link
4. Confirm creation with GitHub issue URL
```

**提示：**

**最佳实践**：将GitHub问题链接回Sentry以实现完全可追溯性。这会在错误监控和问题跟踪之间创建双向连接。

## 步骤 4：使用 GitHub 操作设置连续监控

使用 [Sentry Release GitHub Action](<https://docs.sentry.io/product/releases/setup/release-automation/github-actions/>) 和 Continue CLI 自动进行错误监控，以创建全面的、由 AI- 支持的问题描述：

**提示：**

**为什么将 Sentry 版本与 Continue CLI 结合使用？**
- **发布跟踪**：将错误与特定部署关联
- **AI-强力分析**：Continue CLI生成详细的问题描述和根本原因分析
- **更好的上下文**：将错误链接到提交和拉取请求
- **自动化工作流程**：使用完整堆栈跟踪和建议的修复创建问题

```yaml
name: Sentry Error Monitoring

on:
  push:
    branches:
      - main
  schedule:
    # Run every 6 hours
    - cron: "0 */6 * * *"
  workflow_dispatch: # Allow manual triggers

jobs:
  monitor-errors:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: actions/setup-node@v4
        with:
          node-version: "22"

      # Create Sentry release for better error tracking
      - name: Create Sentry release
        uses: getsentry/action-release@v1
        env:
          SENTRY_AUTH_TOKEN: ${{ secrets.SENTRY_AUTH_TOKEN }}
          SENTRY_ORG: ${{ secrets.SENTRY_ORG }}
          SENTRY_PROJECT: ${{ secrets.SENTRY_PROJECT }}
        with:
          environment: production

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Authenticate GitHub CLI
        run: |
          echo "${{ secrets.GITHUB_TOKEN }}" | gh auth login --with-token
          echo "✅ GitHub CLI authenticated"

      - name: Analyze Sentry Errors and Create Issues
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
          SENTRY_AUTH_TOKEN: ${{ secrets.SENTRY_AUTH_TOKEN }}
          SENTRY_ORG: ${{ secrets.SENTRY_ORG }}
          SENTRY_PROJECT: ${{ secrets.SENTRY_PROJECT }}
        run: |
          echo "🔍 Analyzing Sentry errors..."
          
          # Use Continue CLI to analyze errors and generate comprehensive issue descriptions
          cn -p "Using Sentry MCP, analyze errors from the past 6 hours for project $SENTRY_PROJECT:
                 1. Filter for unresolved errors with high or critical severity
                 2. Group similar errors to avoid duplicates
                 3. For each unique critical error:
                    - Generate a comprehensive issue description including:
                      * Error summary with frequency and user impact metrics
                      * Full stack trace with highlighted problem areas
                      * Environment details (browser, OS, release version)
                      * Link to Sentry issue dashboard
                      * Root cause analysis using AI
                      * Step-by-step reproduction if available
                      * Suggested fix with code examples
                      * Related errors or patterns
                    - Check if a GitHub issue already exists for this error
                    - If not, create a new issue with the generated description
                    - Use gh CLI: gh issue create --title '[Sentry] [Error Type]: Brief description' --body 'AI-generated comprehensive analysis' --label 'bug,sentry,critical,needs-investigation'
                    - Link the GitHub issue back to Sentry
                 4. Generate a summary report with:
                    - Total errors analyzed
                    - New issues created with URLs
                    - Errors skipped (already tracked)
                    - Release correlation if available

                 Sentry Organization: $SENTRY_ORG
                 Project: $SENTRY_PROJECT
                 Only process errors not already tracked in GitHub."

      - name: Post workflow summary
        if: always()
        run: |
          echo "## 📊 Sentry Error Monitoring Summary" >> $GITHUB_STEP_SUMMARY
          echo "✅ Workflow completed at $(date)" >> $GITHUB_STEP_SUMMARY
          echo "" >> $GITHUB_STEP_SUMMARY
          echo "Check the 'Analyze Sentry Errors' step above for:" >> $GITHUB_STEP_SUMMARY
          echo "- Number of errors analyzed" >> $GITHUB_STEP_SUMMARY
          echo "- GitHub issues created" >> $GITHUB_STEP_SUMMARY
          echo "- Errors already tracked" >> $GITHUB_STEP_SUMMARY
```

**注意：**

**必需的GitHub秘密**：
- `CONTINUE_API_KEY`：来自 [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>) 的 Continue API 密钥
- `SENTRY_AUTH_TOKEN`：您的 Sentry 用户身份验证令牌（需要范围：`org:read`、`project:read`、`project:releases`、`event:read`）
- `SENTRY_ORG`：你的哨兵组织子弹
- `SENTRY_PROJECT`：您的 Sentry 项目 slug
- `GITHUB_TOKEN`：由GitHub操作自动提供

将这些添加到：**存储库设置→秘密和变量→操作**

**说明：**

**工作流程最佳实践**：
- 每 6 小时运行一次以快速捕获关键错误
- 创建推送时的 Sentry 版本以跟踪错误与部署的相关性
- 使用 Continue CLI 生成全面的、由 AI- 支持的问题描述
- 使用重复检测以避免同一错误产生多个问题
- 按严重性过滤以关注高影响力问题
- 包括完整的错误上下文和问题中的建议修复
- 使用适当的标签标记问题以进行团队路由
- 链接 GitHub 问题回 Sentry 进行双向跟踪

## 你建造了什么

完成本指南后，您将拥有一个完整的 **Sentry 驱动的错误监控系统 **，该系统：

- **监控生产错误** - 每 6 小时自动获取和分析 Sentry 问题

- **识别关键错误** - 使用AI发现高影响错误

- **创建可操作的任务** - 通过根本原因分析和建议的修复生成GitHub问题

- **自主运行** - 使用GitHub操作连续运行，无需手动干预

- **随您的应用程序扩展** - 自动处理不断增长的错误量和复杂性

#### 连续 AI 错误监控

您的系统现在运行在 **[ 级别 2 连续 AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** - AI 通过 GitHub 问题审查和解决在人工监督下处理常规错误分析。

## 高级错误分析提示

使用这些高级 Continue CLI 提示增强您的工作流程：

#### 发布影响分析

比较最新 [Sentry 版本 ](<https://docs.sentry.io/product/releases/>) 之前和之后的错误率，以识别部署中引入的回归

#### 错误趋势检测

分析过去 30 天的 Sentry 错误趋势，并在新出现的问题变得严重之前识别它们

#### 用户影响评估

确定哪些错误正在影响最独特的用户，并根据用户影响确定修复的优先级

#### 性能相关性

交叉引用带有错误峰值的 Sentry [ 性能问题 ](<https://docs.sentry.io/product/performance/>) 以确定根本原因

## 安全最佳实践

**注意：**

**保护您的API钥匙**：
- 将所有凭据存储为 GitHub 机密，切勿存储在代码中
- 使用ContinueCLI的安全秘密存储
- 将 Sentry 令牌范围限制为所需的最低权限
- 定期轮换 API 钥匙（建议每 90 天）
- 监控令牌使用情况以发现异常活动
- 尽可能使用 OAuth 以提高安全性

## 故障排除

### Sentry MCP 连接问题

如果您遇到连接问题：

1. 验证 OAuth 身份验证是否完成
2. 检查您的 Sentry 组织访问权限
3. 确保 MCP 服务器 URL 正确 (`https://mcp.sentry.dev/mcp`)
4. 对于自托管 Sentry，请验证您的主机 URL 是否配置正确

请参阅 [Sentry MCP GitHub 问题 ](<https://github.com/getsentry/sentry-mcp/issues>) 了解已知问题和解决方案。

### 常见错误分析问题

|问题|解决方案|
|:------|:---------|
|没有返回错误|验证您的 Sentry 项目最近是否收集了错误|
|OAuth 提示未出现|检查 Continue CLI 是否具有正确的 MCP 配置|
|重复的 GitHub 问题|在提示中实施重复检测|
|缺少错误上下文|确保您的 Sentry 令牌具有 `event:read` 范围|

## 下一步

- 设置[Sentry性能监控](<https://docs.sentry.io/product/performance/>)
- 配置 [Sentry 发布跟踪 ](<https://docs.sentry.io/product/releases/>) 以进行部署关联

## 资源

- [Sentry MCP 文档](<https://docs.sentry.io/product/sentry-mcp/>)
- [Sentry API 文档](<https://docs.sentry.io/api/>)
- [Sentry问题指南](<https://docs.sentry.io/product/issues/>)
- [Sentry性能监控](<https://docs.sentry.io/product/performance/>)
- [哨兵发布跟踪](<https://docs.sentry.io/product/releases/>)

- [哨兵 MCP GitHub 存储库 ](<https://github.com/getsentry/sentry-mcp>)
- [GitHub CLI 文档](<https://cli.github.com/>)
- [Continue CLI 指南](<https://docs.continue.dev/guides/cli>)
- [连续 AI 最佳实践](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)


## 原文对照

<details>
<summary>展开完整原文</summary>

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### What You'll Build

An automated error monitoring system that uses Continue CLI with Sentry MCP to analyze production errors, identify root causes with AI, and create detailed GitHub issues with suggested fixes.

## What You'll Learn

This cookbook teaches you to:

- Use [Sentry MCP](<https://docs.sentry.io/product/sentry-mcp/>) to access [issues](<https://docs.sentry.io/product/issues/>)
- Analyze error patterns and stack traces with AI

- Automatically create GitHub issues with root cause analysis
- Set up continuous error monitoring with GitHub Actions

## Prerequisites

Before starting, ensure you have:

- GitHub repository where you want to create issues
- [Sentry account](<https://sentry.io>) with an active project collecting errors
- Node.js 18+ installed locally
- [Continue CLI](<https://docs.continue.dev/guides/cli>) with **active credits** (required for API usage)
- [GitHub CLI](<https://cli.github.com/>) installed (`gh` command)

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

#### Set up Continue CLI Account & API Key

1. Visit [Continue Organizations](<https://continue.dev/settings/organizations>)
2. Sign up or log in to your Continue account
3. Navigate to your organization settings
4. Click **"API Keys"** and then **"+ New API Key"**
5. Copy the API key immediately (you won't see it again!)
6. Login to the CLI: `cn login`

**提示：**

Continue CLI handles complex error analysis and API interactions - you just need to provide the right prompts!

## Step 1: Set Up Your Credentials

First, you'll need to gather your Sentry and GitHub API credentials.

#### Configure Sentry MCP

**提示：**

See [Sentry MCP Documentation](<https://docs.sentry.io/product/sentry-mcp/>) for detailed configuration options

The Sentry MCP supports multiple configuration methods. For Continue CLI, OAuth is recommended:

**Option 1: OAuth Configuration (Recommended)**

The Sentry MCP will prompt for OAuth authentication when first used. Simply follow the authorization flow.

**Option 2: STDIO Mode with Auth Token**

For local development or self-hosted Sentry installations, you can use STDIO mode:
```bash
npx @sentry/mcp-server@latest --access-token=YOUR_SENTRY_TOKEN --host=sentry.io
```

Or use environment variables:
```bash
SENTRY_ACCESS_TOKEN=your-token SENTRY_HOST=sentry.io
```

**说明：**

The `--host` parameter is required and should point to your Sentry instance (e.g., `sentry.io` or `sentry.example.com` for self-hosted).

#### Sentry API Credentials

You'll need a **Sentry User Auth Token** to access issues and error data:

1. Go to [User Auth Tokens](<https://sentry.io/settings/account/api/auth-tokens/>) in Sentry
   - For self-hosted Sentry, use: `https://YOUR-SENTRY-DOMAIN/settings/account/api/auth-tokens/`
2. Click **Create New Token**
3. Name it "Continue CLI Error Analysis"
4. **Select these permission scopes** (required for full functionality):
   - `org:read` - **Required** - Access organization information
   - `project:read` - **Required** - Read project configurations
   - `project:releases` - **Required** - Access release information for deployment tracking
   - `event:read` - **Required** - Read detailed error event data and stack traces
   - `event:write` - Optional - Update error events (for marking as resolved)
   - `member:read` - Recommended - Read team member information for auto-assignment
   - `team:read` - Recommended - Access team data for routing issues
5. Copy the token immediately (you won't see it again!)
6. Note your organization slug (found in your Sentry URL: `https://sentry.io/organizations/YOUR-ORG-SLUG`)
7. Note your Sentry host URL (typically `https://sentry.io` or your self-hosted domain)

**说明：**

**Sentry MCP Connection**: The MCP server connects via OAuth to `https://mcp.sentry.dev/mcp` and handles authentication securely. For local development, you can use STDIO mode with your auth token.

#### Set Up GitHub CLI Authentication

GitHub CLI handles authentication automatically - no manual PAT needed:

1. Install GitHub CLI if not already installed
2. Run `gh auth login` and follow the prompts
3. Choose authentication method (browser or token)
4. Grant necessary permissions when prompted (`issues:write` is **required** for creating issues)

## Sentry Error Monitoring Workflow Options

**说明：**

**How Sentry MCP Works**:
- Connects to your Sentry organization via OAuth
- Provides tools for accessing issues, projects, teams, and DSNs
- Supports both hosted (`https://mcp.sentry.dev`) and self-hosted Sentry instances
- Automatically handles authentication and API interactions

#### Add Sentry MCP to Continue CLI

Configure the [Sentry MCP](<https://docs.sentry.io/product/sentry-mcp/>) using OAuth:

The MCP server will automatically prompt for OAuth authentication when you first use it.

#### Verify Sentry Connection

Test your Sentry MCP connection with this prompt:

```
List my Sentry organizations and projects
```

#### Create Custom Error Analysis Prompts

Use this prompt template with Continue CLI to analyze Sentry errors:

```
Analyze Sentry errors from the past 24 hours:
- Group errors by root cause
- Identify the top 5 most critical issues by frequency and impact
- For each critical issue, provide:
  * Stack trace analysis
  * Affected user count
  * First seen and last seen timestamps
  * Suggested fix based on error context
- Create GitHub issues using gh CLI with:
  * Title format: '🐛 [Sentry] [Error Type]: Brief description'
  * Labels: 'bug', 'sentry', 'production'
  * Priority labels based on severity
  * Full error context and suggested fix in the body
Execute the commands and confirm each issue was created.
```

**说明：**

**Why GitHub CLI over GitHub MCP**: While GitHub MCP is available, it can be
token-expensive to run. The `gh` CLI is more efficient, requires no API tokens
(authenticated via `gh auth login`), and provides a cleaner command-line
experience. GitHub MCP remains an option if you prefer full MCP integration.

---

**注意：**

**Repository Labels Required**: Make sure your GitHub repository has these labels:
- `bug`, `sentry`, `production`
- `critical`, `high-priority`, `medium-priority`, `low-priority`
- `needs-investigation`, `has-fix`

Create missing labels in your repo at: **Settings → Labels → New label**

## Step 2: Analyze Sentry Errors with AI

Use Continue CLI to perform intelligent error analysis. Enter these prompts in the Continue CLI TUI:

**说明：**

To run any of the example prompts below in headless mode, use `cn -p "prompt"`

#### Recent Errors Analysis

**Prompt:**
```
Show me Sentry errors from the past 7 days, grouped by error type, with frequency counts
```

#### Critical Error Investigation

**Prompt:**
```
Find the most critical Sentry error affecting the most users in production and provide:
- Full stack trace analysis
- Affected user count and browser/OS breakdown
- Timeline of when the error started occurring
- Similar historical issues from Sentry
- Root cause hypothesis based on code context
- Suggested fix with code examples
```

#### Performance Issue Detection

**Prompt:**
```
Analyze Sentry performance data to identify:
- Slowest transactions in the past 24 hours
- Database queries with high latency
- API endpoints with degraded performance
- Suggested optimizations for each issue
```

**说明：**

**Available Sentry MCP Tools**:
- **Organizations**: Access org-level data and settings
- **Projects**: Query projects and their configurations
- **Issues**: Search and analyze error issues
- **Teams**: Manage team assignments
- **DSNs**: Retrieve project DSN configurations

## Step 3: Automate GitHub Issue Creation

Create actionable GitHub issues from Sentry errors. Enter this prompt in the Continue CLI TUI:

**Prompt:**
```
For each unresolved Sentry error with 'critical' or 'high' severity:
1. Analyze the error using Sentry MCP
2. Create a GitHub issue with gh CLI:
   - Title: '🐛 [Sentry] [Error Type]: Brief description'
   - Body with:
     * Error summary and impact (affected users, frequency)
     * Full stack trace
     * Environment details (browser, OS, release version)
     * Link to Sentry issue
     * Root cause analysis from AI
     * Suggested fix with code snippets
     * Related Sentry issues
   - Labels: 'bug', 'sentry', 'production', and severity label
   - Assignees: Team member based on code ownership
3. Update Sentry issue with GitHub issue link
4. Confirm creation with GitHub issue URL
```

**提示：**

**Best Practice**: Link GitHub issues back to Sentry for full traceability. This creates a bidirectional connection between your error monitoring and issue tracking.

## Step 4: Set Up Continuous Monitoring with GitHub Actions

Automate error monitoring with the [Sentry Release GitHub Action](<https://docs.sentry.io/product/releases/setup/release-automation/github-actions/>) and Continue CLI to create comprehensive, AI-powered issue descriptions:

**提示：**

**Why Combine Sentry Releases with Continue CLI?**
- **Release Tracking**: Associate errors with specific deployments
- **AI-Powered Analysis**: Continue CLI generates detailed issue descriptions with root cause analysis
- **Better Context**: Link errors to commits and pull requests
- **Automated Workflows**: Create issues with full stack traces and suggested fixes

```yaml
name: Sentry Error Monitoring

on:
  push:
    branches:
      - main
  schedule:
    # Run every 6 hours
    - cron: "0 */6 * * *"
  workflow_dispatch: # Allow manual triggers

jobs:
  monitor-errors:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: actions/setup-node@v4
        with:
          node-version: "22"

      # Create Sentry release for better error tracking
      - name: Create Sentry release
        uses: getsentry/action-release@v1
        env:
          SENTRY_AUTH_TOKEN: ${{ secrets.SENTRY_AUTH_TOKEN }}
          SENTRY_ORG: ${{ secrets.SENTRY_ORG }}
          SENTRY_PROJECT: ${{ secrets.SENTRY_PROJECT }}
        with:
          environment: production

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Authenticate GitHub CLI
        run: |
          echo "${{ secrets.GITHUB_TOKEN }}" | gh auth login --with-token
          echo "✅ GitHub CLI authenticated"

      - name: Analyze Sentry Errors and Create Issues
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
          SENTRY_AUTH_TOKEN: ${{ secrets.SENTRY_AUTH_TOKEN }}
          SENTRY_ORG: ${{ secrets.SENTRY_ORG }}
          SENTRY_PROJECT: ${{ secrets.SENTRY_PROJECT }}
        run: |
          echo "🔍 Analyzing Sentry errors..."
          
          # Use Continue CLI to analyze errors and generate comprehensive issue descriptions
          cn -p "Using Sentry MCP, analyze errors from the past 6 hours for project $SENTRY_PROJECT:
                 1. Filter for unresolved errors with high or critical severity
                 2. Group similar errors to avoid duplicates
                 3. For each unique critical error:
                    - Generate a comprehensive issue description including:
                      * Error summary with frequency and user impact metrics
                      * Full stack trace with highlighted problem areas
                      * Environment details (browser, OS, release version)
                      * Link to Sentry issue dashboard
                      * Root cause analysis using AI
                      * Step-by-step reproduction if available
                      * Suggested fix with code examples
                      * Related errors or patterns
                    - Check if a GitHub issue already exists for this error
                    - If not, create a new issue with the generated description
                    - Use gh CLI: gh issue create --title '[Sentry] [Error Type]: Brief description' --body 'AI-generated comprehensive analysis' --label 'bug,sentry,critical,needs-investigation'
                    - Link the GitHub issue back to Sentry
                 4. Generate a summary report with:
                    - Total errors analyzed
                    - New issues created with URLs
                    - Errors skipped (already tracked)
                    - Release correlation if available

                 Sentry Organization: $SENTRY_ORG
                 Project: $SENTRY_PROJECT
                 Only process errors not already tracked in GitHub."

      - name: Post workflow summary
        if: always()
        run: |
          echo "## 📊 Sentry Error Monitoring Summary" >> $GITHUB_STEP_SUMMARY
          echo "✅ Workflow completed at $(date)" >> $GITHUB_STEP_SUMMARY
          echo "" >> $GITHUB_STEP_SUMMARY
          echo "Check the 'Analyze Sentry Errors' step above for:" >> $GITHUB_STEP_SUMMARY
          echo "- Number of errors analyzed" >> $GITHUB_STEP_SUMMARY
          echo "- GitHub issues created" >> $GITHUB_STEP_SUMMARY
          echo "- Errors already tracked" >> $GITHUB_STEP_SUMMARY
```

**注意：**

**Required GitHub Secrets**:
- `CONTINUE_API_KEY`: Your Continue API key from [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>)
- `SENTRY_AUTH_TOKEN`: Your Sentry User Auth Token (needs scopes: `org:read`, `project:read`, `project:releases`, `event:read`)
- `SENTRY_ORG`: Your Sentry organization slug
- `SENTRY_PROJECT`: Your Sentry project slug
- `GITHUB_TOKEN`: Automatically provided by GitHub Actions

Add these at: **Repository Settings → Secrets and variables → Actions**

**说明：**

**Workflow Best Practices**:
- Run every 6 hours to catch critical errors quickly
- Create Sentry releases on push to track error-to-deployment correlation
- Use Continue CLI to generate comprehensive, AI-powered issue descriptions
- Use duplicate detection to avoid creating multiple issues for the same error
- Filter by severity to focus on high-impact issues
- Include full error context and suggested fixes in issues
- Tag issues with appropriate labels for team routing
- Link GitHub issues back to Sentry for bidirectional tracking

## What You've Built

After completing this guide, you have a complete **Sentry-powered error monitoring system** that:

- **Monitors production errors** - Automatically fetches and analyzes Sentry issues every 6 hours

- **Identifies critical bugs** - Uses AI to spot high-impact errors

- **Creates actionable tasks** - Generates GitHub issues with root cause analysis and suggested fixes

- **Runs autonomously** - Operates continuously without manual intervention using GitHub Actions

- **Scales with your app** - Handles growing error volumes and complexity automatically

#### Continuous AI Error Monitoring

Your system now operates at **[Level 2 Continuous AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** - AI handles routine error analysis with human oversight through GitHub issue review and resolution.

## Advanced Error Analysis Prompts

Enhance your workflow with these advanced Continue CLI prompts:

#### Release Impact Analysis

Compare error rates before and after the latest [Sentry release](<https://docs.sentry.io/product/releases/>) to identify regressions introduced in the deployment

#### Error Trend Detection

Analyze Sentry error trends over the past 30 days and identify emerging issues before they become critical

#### User Impact Assessment

Identify which errors are affecting the most unique users and prioritize fixes based on user impact

#### Performance Correlation

Cross-reference Sentry [performance issues](<https://docs.sentry.io/product/performance/>) with error spikes to identify root causes

## Security Best Practices

**注意：**

**Protect Your API Keys**:
- Store all credentials as GitHub Secrets, never in code
- Use Continue CLI's secure secret storage
- Limit Sentry token scopes to minimum required permissions
- Rotate API keys regularly (every 90 days recommended)
- Monitor token usage for unusual activity
- Use OAuth when possible for better security

## Troubleshooting

### Sentry MCP Connection Issues

If you encounter connection issues:

1. Verify OAuth authentication is complete
2. Check your Sentry organization access
3. Ensure the MCP server URL is correct (`https://mcp.sentry.dev/mcp`)
4. For self-hosted Sentry, verify your host URL is configured correctly

See the [Sentry MCP GitHub Issues](<https://github.com/getsentry/sentry-mcp/issues>) for known issues and solutions.

### Common Error Analysis Issues

| Issue | Solution |
|:------|:---------|
| No errors returned | Verify your Sentry project has collected errors recently |
| OAuth prompt not appearing | Check that Continue CLI has proper MCP configuration |
| Duplicate GitHub issues | Implement duplicate detection in your prompts |
| Missing error context | Ensure your Sentry token has `event:read` scope |

## Next Steps

- Set up [Sentry performance monitoring](<https://docs.sentry.io/product/performance/>)
- Configure [Sentry release tracking](<https://docs.sentry.io/product/releases/>) for deployment correlation

## Resources

- [Sentry MCP Documentation](<https://docs.sentry.io/product/sentry-mcp/>)
- [Sentry API Documentation](<https://docs.sentry.io/api/>)
- [Sentry Issues Guide](<https://docs.sentry.io/product/issues/>)
- [Sentry Performance Monitoring](<https://docs.sentry.io/product/performance/>)
- [Sentry Release Tracking](<https://docs.sentry.io/product/releases/>)

- [Sentry MCP GitHub Repository](<https://github.com/getsentry/sentry-mcp>)
- [GitHub CLI Documentation](<https://cli.github.com/>)
- [Continue CLI Guide](<https://docs.continue.dev/guides/cli>)
- [Continuous AI Best Practices](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)


</details>
