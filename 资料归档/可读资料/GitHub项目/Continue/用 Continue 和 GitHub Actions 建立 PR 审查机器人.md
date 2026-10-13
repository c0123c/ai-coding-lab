# 用 Continue 和 GitHub Actions 建立 PR 审查机器人

工程教程。将 Continue CLI 放入 Actions Runner，结合团队规则生成审查意见，说明交互命令、日志、测试准则、配置和故障排查；输出需按项目验收。

- 来源：Continue
- 资料类型：工程教程
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/f7f3ab7440341f052642b7d0fab72d40fd8e7ad0>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：代码仍会送往所选模型服务，不能把本地 Runner 理解成完全离线。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/github-pr-review-bot.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/用 Continue 和 GitHub Actions 建立 PR 审查机器人.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Code Review Bot with Continue and GitHub Actions"
description: "Set up automated, context-aware pull request reviews using Continue CLI in GitHub Actions - privacy-first with custom rules"
sidebarTitle: "Pull Request Review Bot"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

#### 你将构建什么

一个自动拉取请求审查系统：
- 当拉取请求打开或更新时自动审查代码
- 应用您团队的自定义规则和标准
- 在您的 GitHub 操作运行程序中运行（代码直接发送到您配置的 LLM）
- 将可操作的反馈作为拉取请求评论发布
- 响应交互式评审请求

## 为什么采用这种方法？

#### 隐私第一

所有日志和处理都发生在您的运行程序中：Continue CLI 在 GitHub 操作 → 代码中运行到您的 LLM 提供程序（OpenAI、Anthropic 等）。没有托管的 Continue 服务读取您的代码。

#### 可定制

在 `.continue/rules/` 中定义团队特定的规则，自动应用于每个拉取请求。

#### 情境意识

利用 Continue 的 AI agent 进行智能、上下文感知的审核，并完全控制您的配置。

## 先决条件

开始之前，请确保您拥有：

- 带有拉取请求的 GitHub 存储库
- 一个Continue帐户
  - 阅读：[了解配置](<https://docs.continue.dev/guides/understanding-configs>)
- 来自 [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>) 的 Continue API 键
- Continue 配置用于代码审查的助手（或使用我们推荐的默认值）

**说明：**

**想要自定义评论机器人吗？**

您可以在 [continue.dev/continuedev/review-bot](<https://continue.dev/continuedev/review-bot>) 重新混合默认审核机器人配置，以使用自定义提示、规则和行为创建您自己的个性化版本。

## 快速设置（10 分钟）

#### 配置存储库机密和变量

导航到您的存储库设置：**设置→秘密和变量→操作**

**所需的秘密：**
- `CONTINUE_API_KEY` - 来自 [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>) 的 Continue API 密钥

**可选（以获得更好的权限）：**
- **变量**选项卡：`APP_ID` - GitHub应用程序ID（用于增强的API速率限制）
- **秘密**选项卡：`APP_PRIVATE_KEY` - GitHub应用程序私钥

#### 设置 GitHub 应用程序（可选但推荐）

为了获得更好的速率限制和权限，请创建一个 GitHub 应用程序：

1. Go 设置 → 开发者设置 → GitHub 应用程序 → 新 GitHub 应用程序
2. 设置权限：
    - **内容**：阅读
    - **拉取请求**：写入
    - **问题**：写入
3. 生成私钥
4. 在您的存储库上安装应用程序
5. 添加 `APP_ID` 作为存储库 ** 变量 **（变量选项卡）
6. 添加 `APP_PRIVATE_KEY` 作为存储库 **secret**（“机密”选项卡）

如果没有 GitHub 应用程序，操作将使用默认的 `GITHUB_TOKEN`。

#### 添加工作流程文件

使用提供的配置在 `.github/workflows/code-review.yml` 创建 GitHub 操作工作流程文件。

```yaml
name: Continue Code Review

on:
  pull_request:
    types: [opened, synchronize, ready_for_review]
  issue_comment:
    types: [created]

permissions:
  contents: read
  pull-requests: write
  issues: write

jobs:
  review:
    runs-on: ubuntu-latest
    # Only run on PRs or when @review-bot is mentioned
    if: |
      github.event_name == 'pull_request' ||
      (github.event_name == 'issue_comment' && contains(github.event.comment.body, '@review-bot'))
    
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history for better context

      # Optional: Use GitHub App token for better rate limits
      - name: Generate App Token
        id: app-token
        if: vars.APP_ID != ''
        uses: actions/create-github-app-token@v1
        with:
          app-id: ${{ vars.APP_ID }}
          private-key: ${{ secrets.APP_PRIVATE_KEY }}

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install Continue CLI
        run: npm i -g @continuedev/cli

      - name: Get Pull Request Details
        id: pr
        env:
          GH_TOKEN: ${{ steps.app-token.outputs.token || github.token }}
        run: |
          # Get pull request number and details
          if [ "${{ github.event_name }}" = "pull_request" ]; then
            PR_NUMBER=${{ github.event.pull_request.number }}
          else
            PR_NUMBER=$(jq -r .issue.number "$GITHUB_EVENT_PATH")
          fi
          
          echo "pr_number=$PR_NUMBER" >> $GITHUB_OUTPUT
          
          # Get pull request diff
          gh pr diff $PR_NUMBER > pr.diff
          
          # Get changed files
          gh pr view $PR_NUMBER --json files -q '.files[].path' > changed_files.txt

      - name: Run Continue Review
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
          GH_TOKEN: ${{ steps.app-token.outputs.token || github.token }}
        run: |
          # Check if custom rules exist
          if [ -d ".continue/rules" ]; then
            echo "📋 Found custom rules in .continue/rules/"
            RULES_CONTEXT="Apply the custom rules found in .continue/rules/ directory."
          else
            echo "ℹ️  No custom rules found. Using general best practices."
            RULES_CONTEXT="Review for general best practices, security issues, and code quality."
          fi
          
          # Build review prompt
          PROMPT="Review this pull request with the following context:

          ## Changed Files
          $(cat changed_files.txt)

          ## Diff
          \`\`\`diff
          $(cat pr.diff)
          \`\`\`

          ## Instructions
          $RULES_CONTEXT

          Provide:
          1. A brief summary of changes
          2. Key findings (potential issues, security concerns, suggestions)
          3. Positive observations (good practices, improvements)
          4. Specific actionable recommendations

          Format as markdown suitable for a GitHub pull request comment."

          # Run Continue CLI in headless mode
          cn --config continuedev/review-bot \
             -p "$PROMPT" \
             --auto > review_output.md

      - name: Post Review Comment
        env:
          GH_TOKEN: ${{ steps.app-token.outputs.token || github.token }}
        run: |
          # Add header
          cat > review_comment.md <<'EOF'
          ## 🤖 AI Code Review
          
          EOF
          
          # Add review content
          cat review_output.md >> review_comment.md
          
          # Add footer
          cat >> review_comment.md <<'EOF'
          
          ---
          *Powered by [Continue](https://continue.dev) • Need a focused review? Comment `@review-bot check for [specific concern]`*
          EOF
          
          # Check for existing review comment
          EXISTING_COMMENT=$(gh pr view ${{ steps.pr.outputs.pr_number }} \
            --json comments -q '.comments[] | select(.body | contains("🤖 AI Code Review")) | .id' | head -1)
          
          if [ -n "$EXISTING_COMMENT" ]; then
            echo "Updating existing comment..."
            gh api --method PATCH \
              "repos/${{ github.repository }}/issues/comments/$EXISTING_COMMENT" \
              -f body="$(cat review_comment.md)"
          else
            echo "Creating new comment..."
            gh pr comment ${{ steps.pr.outputs.pr_number }} \
              --body-file review_comment.md
          fi
```

#### 创建自定义规则（可选）

在 `.continue/rules/` 中定义您团队的标准：

#### 安全规则

创建`.continue/rules/security.md`：

```markdown
---
globs: "**/*.{ts,tsx,js,jsx,py}"
description: "Security Review Standards"
alwaysApply: true
---

# Security Checklist

- No hardcoded credentials, API keys, or secrets
- All user inputs are validated and sanitized
- SQL queries use parameterization (no string concatenation)
- Authentication and authorization checks are in place
- Sensitive data is properly encrypted
- Error messages don't leak sensitive information
```

#### TypeScript 标准

创建`.continue/rules/typescript.md`：

```markdown
  ---
  globs: "**/*.{ts,tsx}"
  description: "TypeScript Best Practices"
  ---

  # TypeScript Standards

  - Use strict type checking (avoid `any` types)
  - Prefer interfaces for object shapes
  - Use proper error handling with typed errors
  - Document complex types with JSDoc comments
  - Export types for public APIs
  - Use const assertions where appropriate
```

#### 检测标准

创建`.continue/rules/testing.md`：

```markdown
---
globs: "**/*.{test,spec}.{ts,tsx,js,jsx}"
description: "Testing Requirements"
---

# Testing Guidelines

- Write tests for new features and bug fixes
- Test both happy paths and error conditions
- Use descriptive test names that explain intent
- Keep tests focused and isolated (no shared state)
- Mock external dependencies
- Aim for meaningful assertions, not coverage metrics
```

#### Python 标准

创建`.continue/rules/python.md`：

```markdown
---
globs: "**/*.py"
description: "Python Code Standards"
---

# Python Best Practices

- Follow PEP 8 style guidelines
- Use type hints for function signatures
- Write docstrings for public functions/classes
- Use context managers for resource management
- Prefer list comprehensions for simple transformations
- Handle exceptions explicitly, avoid bare except
```

## 它是如何运作的

工作流程遵循以下步骤：

1. **拉取请求 Created/Updated** - 拉取请求已打开或同步
2. **工作流程已触发** - GitHub 操作工作流程自动启动
3. **加载自定义规则** - 从`.continue/rules/`读取您团队的规则
4. **获取拉取请求差异** - 获取差异和已更改文件的列表
5. **Continue CLI 分析代码** - AI agent 根据您的规则检查代码
6. **发布或更新评论评论** - 创建或更新单个 PR 评论并提供反馈

## 交互式命令

对任何拉取请求发表评论以触发重点审查：

```
@review-bot check for security issues
@review-bot review the TypeScript types
@review-bot explain the architecture changes
@review-bot focus on error handling
```

工作流程将根据您的请求进行有针对性的审核。

## 高级配置

#### 使用您自己的 Continue 配置

默认情况下，工作流程使用针对代码审查优化的 `continuedev/review-bot` 配置。将 `continuedev/review-bot` 替换为您自己的配置：

```yaml
- name: Run Continue Review
  env:
    CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
    CONTINUE_ORG: your-org-name      # Add your org
    CONTINUE_CONFIG: username/config-name  # Add your config
  run: |
    cn --config $CONTINUE_ORG/$CONTINUE_CONFIG \
      -p "$PROMPT" \
      --auto > review_output.md
```

将 `CONTINUE_ORG` 和 `CONTINUE_CONFIG` 存储为存储库变量以便于更新。

#### 按文件类型过滤

仅审查拉取请求中的特定文件类型：

```yaml
on:
  pull_request:
    types: [opened, synchronize, ready_for_review]
    paths:
      - '**.ts'
      - '**.tsx'
      - '**.py'
      - '**.go'
```

#### 查看大小限制

跳过审查成本高昂的大型拉取请求：

```yaml
- name: Check PR Size
  id: size-check
  env:
    GH_TOKEN: ${{ steps.app-token.outputs.token || github.token }}
  run: |
    ADDITIONS=$(gh pr view ${{ steps.pr.outputs.pr_number }} --json additions -q .additions)
    DELETIONS=$(gh pr view ${{ steps.pr.outputs.pr_number }} --json deletions -q .deletions)
    TOTAL=$((ADDITIONS + DELETIONS))
    
    if [ $TOTAL -gt 1000 ]; then
      echo "skip=true" >> $GITHUB_OUTPUT
      echo "⚠️  Pull request too large for automatic review ($TOTAL lines changed)"
    else
      echo "skip=false" >> $GITHUB_OUTPUT
    fi

- name: Run Continue Review
  if: steps.size-check.outputs.skip != 'true'
  # ... rest of review step
```

## 故障排除

#### Continue CLI 未找到

- 工作流程自动安装 CLI，但确保 Node.js 20+ 可用
- 检查“安装 Continue CLI”步骤日志中是否有错误

#### 认证失败

- 验证您的 `CONTINUE_API_KEY` 是否有效
- 检查 GitHub 令牌是否具有所需的权限

#### 未生成评论

- 验证您的 Continue 配置是否可访问
- 检查工作流程运行中的 Continue CLI 日志
- 尝试在本地运行：`cn -p "Test prompt" --auto`

#### 评论未发表

- 确保设置了 `pull-requests: write` 权限
- 验证 GitHub 令牌具有对拉取请求发表评论的范围
- 检查存储库是否需要签名提交

## 示例输出

典型的评论评论如下：

> ## 🤖 AI 代码审查
>
> ### 总结
> 此拉取请求引入了带有 JWT 令牌和密码哈希的新用户身份验证系统。该实施遵循安全最佳实践，并提出一些小建议。
>
> ### 主要发现
>
> **安全** ✅
> - 使用 bcrypt 正确实现密码哈希
> - JWT 代币包含适当的到期日
> - 所有端点均存在输入验证
>
> **代码质量** 💡
> - 考虑向登录端点添加速率限制
> - `secretKey` 应从环境变量加载，而不是硬编码
> - 添加针对令牌过期边缘情况的单元测试
>
> ### 积极的观察
> - 与中间件模式的关注点良好分离
> - 清除身份验证失败的错误消息
> - 始终正确使用 async/await
>
> ### 建议
> 1. 将 `secretKey` 移至环境变量（参见 `.continue/rules/security.md`）
> 2. 添加限速中间件，防止暴力攻击
> 3. 考虑为身份验证流程添加集成测试
> 4. 记录 JWT 有效负载结构
>
> \---
> *由 [Continue](<https://continue.dev>) 提供支持 • 需要重点审查？评论`@review-bot check for security`*

## 你建造了什么

完成此设置后，您将拥有一个由 **AI- 支持的代码审查系统 **，该系统：

- ✅ **自动运行** - 审查每个拉取请求，无需手动干预
- ✅ **隐私优先** - CLI 在您的 GitHub 操作运行器中运行，代码直接发送到您配置的 LLM
- ✅ **可定制** - 自动应用特定于团队的规则
- ✅ **互动** - 回复重点审核请求
- ✅ **连续** - 随着拉取请求的变化更新评论

#### 连续AI

您的拉取请求工作流程现在在 **[ 级别 2 连续 
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI 运行，通过审核和 
 批准在人工监督下处理例行代码审核。

## 下一步

1. **测试一下** - 创建测试拉取请求并观看评论出现
2. **优化规则** - 添加更多特定于您的代码库的自定义规则
3. **自定义提示** - 调整审阅提示以符合您团队的风格
4. **添加指标** - 跟踪一段时间内的审核有效性
5. **创建团队配置** - 设置共享的 Continue 配置以获得一致的评论

## 灵感与资源

#### [CodeBunny](<https://github.com/bdougie/codebunny>)

最初的灵感 - 隐私第一 AI 代码审查

#### [Continue CLI 指南](<https://docs.continue.dev/guides/cli>)

了解有关 Continue CLI 功能的更多信息

#### [规则文档](<https://docs.continue.dev/customize/deep-dives/rules>)

深入研究自定义规则


## 原文对照

<details>
<summary>展开完整原文</summary>

#### What You'll Build

An automated pull request review system that:
- Reviews code automatically when pull requests open or update
- Applies your team's custom rules and standards
- Runs in your GitHub Actions runner (code is sent directly to your configured LLM)
- Posts actionable feedback as pull request comments
- Responds to interactive review requests

## Why This Approach?

#### Privacy-First

All logs and processing happen in your runner: Continue CLI runs in GitHub Actions → code to your LLM provider (OpenAI, Anthropic, etc.). No hosted Continue service reads your code.

#### Customizable

Define team-specific rules in `.continue/rules/` that automatically apply to every pull request.

#### Context Awareness

Leverage Continue's AI agent for intelligent, context-aware reviews with full control over your configuration.

## Prerequisites

Before starting, ensure you have:

- A GitHub repository with pull requests
- A Continue account
  - Read: [Understanding Configs](<https://docs.continue.dev/guides/understanding-configs>)
- A Continue API key from [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>)
- Continue assistant configured for code reviews (or use our recommended default)

**说明：**

**Want to customize the review bot?**

You can remix the default review bot configuration at [continue.dev/continuedev/review-bot](<https://continue.dev/continuedev/review-bot>) to create your own personalized version with custom prompts, rules, and behaviors.

## Quick Setup (10 Minutes)

#### Configure Repository Secrets and Variables

Navigate to your repository settings: **Settings → Secrets and variables → Actions**

**Required Secrets:**
- `CONTINUE_API_KEY` - Your Continue API key from [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>)

**Optional (for better permissions):**
- **Variables** tab: `APP_ID` - GitHub App ID (for enhanced API rate limits)
- **Secrets** tab: `APP_PRIVATE_KEY` - GitHub App private key

#### Setting up a GitHub App (Optional but Recommended)

For better rate limits and permissions, create a GitHub App:

1. Go to Settings → Developer settings → GitHub Apps → New GitHub App
2. Set permissions:
    - **Contents**: Read
    - **Pull Requests**: Write
    - **Issues**: Write
3. Generate a private key
4. Install the app on your repository
5. Add `APP_ID` as a repository **variable** (Variables tab)
6. Add `APP_PRIVATE_KEY` as a repository **secret** (Secrets tab)

Without a GitHub App, the action will use the default `GITHUB_TOKEN`.

#### Add Workflow File

Create a GitHub Actions workflow file at `.github/workflows/code-review.yml` with the provided configuration.

```yaml
name: Continue Code Review

on:
  pull_request:
    types: [opened, synchronize, ready_for_review]
  issue_comment:
    types: [created]

permissions:
  contents: read
  pull-requests: write
  issues: write

jobs:
  review:
    runs-on: ubuntu-latest
    # Only run on PRs or when @review-bot is mentioned
    if: |
      github.event_name == 'pull_request' ||
      (github.event_name == 'issue_comment' && contains(github.event.comment.body, '@review-bot'))
    
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history for better context

      # Optional: Use GitHub App token for better rate limits
      - name: Generate App Token
        id: app-token
        if: vars.APP_ID != ''
        uses: actions/create-github-app-token@v1
        with:
          app-id: ${{ vars.APP_ID }}
          private-key: ${{ secrets.APP_PRIVATE_KEY }}

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install Continue CLI
        run: npm i -g @continuedev/cli

      - name: Get Pull Request Details
        id: pr
        env:
          GH_TOKEN: ${{ steps.app-token.outputs.token || github.token }}
        run: |
          # Get pull request number and details
          if [ "${{ github.event_name }}" = "pull_request" ]; then
            PR_NUMBER=${{ github.event.pull_request.number }}
          else
            PR_NUMBER=$(jq -r .issue.number "$GITHUB_EVENT_PATH")
          fi
          
          echo "pr_number=$PR_NUMBER" >> $GITHUB_OUTPUT
          
          # Get pull request diff
          gh pr diff $PR_NUMBER > pr.diff
          
          # Get changed files
          gh pr view $PR_NUMBER --json files -q '.files[].path' > changed_files.txt

      - name: Run Continue Review
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
          GH_TOKEN: ${{ steps.app-token.outputs.token || github.token }}
        run: |
          # Check if custom rules exist
          if [ -d ".continue/rules" ]; then
            echo "📋 Found custom rules in .continue/rules/"
            RULES_CONTEXT="Apply the custom rules found in .continue/rules/ directory."
          else
            echo "ℹ️  No custom rules found. Using general best practices."
            RULES_CONTEXT="Review for general best practices, security issues, and code quality."
          fi
          
          # Build review prompt
          PROMPT="Review this pull request with the following context:

          ## Changed Files
          $(cat changed_files.txt)

          ## Diff
          \`\`\`diff
          $(cat pr.diff)
          \`\`\`

          ## Instructions
          $RULES_CONTEXT

          Provide:
          1. A brief summary of changes
          2. Key findings (potential issues, security concerns, suggestions)
          3. Positive observations (good practices, improvements)
          4. Specific actionable recommendations

          Format as markdown suitable for a GitHub pull request comment."

          # Run Continue CLI in headless mode
          cn --config continuedev/review-bot \
             -p "$PROMPT" \
             --auto > review_output.md

      - name: Post Review Comment
        env:
          GH_TOKEN: ${{ steps.app-token.outputs.token || github.token }}
        run: |
          # Add header
          cat > review_comment.md <<'EOF'
          ## 🤖 AI Code Review
          
          EOF
          
          # Add review content
          cat review_output.md >> review_comment.md
          
          # Add footer
          cat >> review_comment.md <<'EOF'
          
          ---
          *Powered by [Continue](https://continue.dev) • Need a focused review? Comment `@review-bot check for [specific concern]`*
          EOF
          
          # Check for existing review comment
          EXISTING_COMMENT=$(gh pr view ${{ steps.pr.outputs.pr_number }} \
            --json comments -q '.comments[] | select(.body | contains("🤖 AI Code Review")) | .id' | head -1)
          
          if [ -n "$EXISTING_COMMENT" ]; then
            echo "Updating existing comment..."
            gh api --method PATCH \
              "repos/${{ github.repository }}/issues/comments/$EXISTING_COMMENT" \
              -f body="$(cat review_comment.md)"
          else
            echo "Creating new comment..."
            gh pr comment ${{ steps.pr.outputs.pr_number }} \
              --body-file review_comment.md
          fi
```

#### Create Custom Rules (Optional)

Define your team's standards in `.continue/rules/`:

#### Security Rule

Create `.continue/rules/security.md`:

```markdown
---
globs: "**/*.{ts,tsx,js,jsx,py}"
description: "Security Review Standards"
alwaysApply: true
---

# Security Checklist

- No hardcoded credentials, API keys, or secrets
- All user inputs are validated and sanitized
- SQL queries use parameterization (no string concatenation)
- Authentication and authorization checks are in place
- Sensitive data is properly encrypted
- Error messages don't leak sensitive information
```

#### TypeScript Standards

Create `.continue/rules/typescript.md`:

```markdown
  ---
  globs: "**/*.{ts,tsx}"
  description: "TypeScript Best Practices"
  ---

  # TypeScript Standards

  - Use strict type checking (avoid `any` types)
  - Prefer interfaces for object shapes
  - Use proper error handling with typed errors
  - Document complex types with JSDoc comments
  - Export types for public APIs
  - Use const assertions where appropriate
```

#### Testing Standards

Create `.continue/rules/testing.md`:

```markdown
---
globs: "**/*.{test,spec}.{ts,tsx,js,jsx}"
description: "Testing Requirements"
---

# Testing Guidelines

- Write tests for new features and bug fixes
- Test both happy paths and error conditions
- Use descriptive test names that explain intent
- Keep tests focused and isolated (no shared state)
- Mock external dependencies
- Aim for meaningful assertions, not coverage metrics
```

#### Python Standards

Create `.continue/rules/python.md`:

```markdown
---
globs: "**/*.py"
description: "Python Code Standards"
---

# Python Best Practices

- Follow PEP 8 style guidelines
- Use type hints for function signatures
- Write docstrings for public functions/classes
- Use context managers for resource management
- Prefer list comprehensions for simple transformations
- Handle exceptions explicitly, avoid bare except
```

## How It Works

The workflow follows these steps:

1. **Pull Request Created/Updated** - A pull request is opened or synchronized
2. **Workflow Triggered** - GitHub Actions workflow starts automatically
3. **Load Custom Rules** - Reads your team's rules from `.continue/rules/`
4. **Get Pull Request Diff** - Fetches the diff and list of changed files
5. **Continue CLI Analyzes Code** - AI agent reviews the code with your rules
6. **Post or Update Review Comment** - Creates or updates a single PR comment with feedback

## Interactive Commands

Comment on any pull request to trigger focused reviews:

```
@review-bot check for security issues
@review-bot review the TypeScript types
@review-bot explain the architecture changes
@review-bot focus on error handling
```

The workflow will respond with a targeted review based on your request.

## Advanced Configuration

#### Use Your Own Continue Config

By default, the workflow uses the `continuedev/review-bot` config optimized for code reviews. Replace `continuedev/review-bot` with your own config:

```yaml
- name: Run Continue Review
  env:
    CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
    CONTINUE_ORG: your-org-name      # Add your org
    CONTINUE_CONFIG: username/config-name  # Add your config
  run: |
    cn --config $CONTINUE_ORG/$CONTINUE_CONFIG \
      -p "$PROMPT" \
      --auto > review_output.md
```

Store `CONTINUE_ORG` and `CONTINUE_CONFIG` as repository variables for easy updates.

#### Filter by File Types

Only review specific file types in pull requests:

```yaml
on:
  pull_request:
    types: [opened, synchronize, ready_for_review]
    paths:
      - '**.ts'
      - '**.tsx'
      - '**.py'
      - '**.go'
```

#### Review Size Limits

Skip large pull requests that would be expensive to review:

```yaml
- name: Check PR Size
  id: size-check
  env:
    GH_TOKEN: ${{ steps.app-token.outputs.token || github.token }}
  run: |
    ADDITIONS=$(gh pr view ${{ steps.pr.outputs.pr_number }} --json additions -q .additions)
    DELETIONS=$(gh pr view ${{ steps.pr.outputs.pr_number }} --json deletions -q .deletions)
    TOTAL=$((ADDITIONS + DELETIONS))
    
    if [ $TOTAL -gt 1000 ]; then
      echo "skip=true" >> $GITHUB_OUTPUT
      echo "⚠️  Pull request too large for automatic review ($TOTAL lines changed)"
    else
      echo "skip=false" >> $GITHUB_OUTPUT
    fi

- name: Run Continue Review
  if: steps.size-check.outputs.skip != 'true'
  # ... rest of review step
```

## Troubleshooting

#### Continue CLI not found

- The workflow installs the CLI automatically, but ensure Node.js 20+ is available
- Check the "Install Continue CLI" step logs for errors

#### Authentication failed

- Verify your `CONTINUE_API_KEY` is valid
- Check that GitHub token has required permissions

#### No review generated

- Verify your Continue config is accessible
- Check Continue CLI logs in the workflow run
- Try running locally: `cn -p "Test prompt" --auto`

#### Review comment not posted

- Ensure `pull-requests: write` permission is set
- Verify GitHub token has scope to comment on pull requests
- Check if the repository requires signed commits

## Example Output

Here's what a typical review comment looks like:

> ## 🤖 AI Code Review
>
> ### Summary
> This pull request introduces a new user authentication system with JWT tokens and password hashing. The implementation follows security best practices with a few minor suggestions.
>
> ### Key Findings
>
> **Security** ✅
> - Password hashing properly implemented with bcrypt
> - JWT tokens include appropriate expiry
> - Input validation present for all endpoints
>
> **Code Quality** 💡
> - Consider adding rate limiting to login endpoint
> - The `secretKey` should be loaded from environment variables, not hardcoded
> - Add unit tests for token expiration edge cases
>
> ### Positive Observations
> - Good separation of concerns with middleware pattern
> - Clear error messages for authentication failures
> - Proper async/await usage throughout
>
> ### Recommendations
> 1. Move `secretKey` to environment variables (see `.continue/rules/security.md`)
> 2. Add rate limiting middleware to prevent brute force attacks
> 3. Consider adding integration tests for the auth flow
> 4. Document the JWT payload structure
>
> ---
> *Powered by [Continue](<https://continue.dev>) • Need a focused review? Comment `@review-bot check for security`*

## What You've Built

After completing this setup, you have an **AI-powered code review system** that:

- ✅ **Runs automatically** - Reviews every pull request without manual intervention
- ✅ **Privacy-first** - CLI runs in your GitHub Actions runner, code sent directly to your configured LLM
- ✅ **Customizable** - Team-specific rules apply automatically
- ✅ **Interactive** - Responds to focused review requests
- ✅ **Continuous** - Updates reviews as pull requests change

#### Continuous AI

Your pull request workflow now operates at **[Level 2 Continuous
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI handles routine code review with human oversight through review and
approval.

## Next Steps

1. **Test it out** - Create a test pull request and watch the review appear
2. **Refine rules** - Add more custom rules specific to your codebase
3. **Customize prompts** - Adjust the review prompt to match your team's style
4. **Add metrics** - Track review effectiveness over time
5. **Create team config** - Set up a shared Continue config for consistent reviews

## Inspiration & Resources

#### [CodeBunny](<https://github.com/bdougie/codebunny>)

Original inspiration - Privacy-first AI code reviews

#### [Continue CLI Guide](<https://docs.continue.dev/guides/cli>)

Learn more about Continue CLI capabilities

#### [Rules Documentation](<https://docs.continue.dev/customize/deep-dives/rules>)

Deep dive into custom rules


</details>
