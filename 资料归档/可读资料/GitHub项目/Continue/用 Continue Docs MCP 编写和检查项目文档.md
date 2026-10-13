# 用 Continue Docs MCP 编写和检查项目文档

项目工程教程。围绕 Continue 自身文档贡献，展示新教程编写、旧文更新、规则一致性、本地预览、链接和示例验证及提交流程，可借鉴到自己的文档维护。

- 来源：Continue
- 资料类型：项目工程教程
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/f7f3ab7440341f052642b7d0fab72d40fd8e7ad0>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：原案例针对 Continue 文档仓库，推广到其他项目需调整规则和工具。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/continue-docs-mcp-cookbook.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/用 Continue Docs MCP 编写和检查项目文档.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Contributing to Continue with Model Context Protocol (MCP)"
description: "Use the Continue Docs MCP to write cookbooks, guides, and documentation with AI-powered workflows."
sidebarTitle: "Continue Docs MCP Cookbook"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

#### 你将构建什么

掌握使用 Continue 文档 MCP 贡献文档、创建食谱并保持 Continue 文档的一致性 - 全部通过自然语言提示进行。

## 先决条件

开始之前，请确保您拥有：

- 阅读：[贡献给Continue文档](<https://docs.continue.dev/CONTRIBUTING>)以获取设置说明
- 分叉了 [continuedev/continue](<https://github.com/continuedev/continue>) 存储库
- 已安装 Node.js 20+ 和 Continue CLI

**注意：**

本食谱假设您已阅读 [CONTRIBUTING 指南 ](<https://docs.continue.dev/CONTRIBUTING>) 中的设置。如果还没有，请先从那里开始。

## Continue 文档 MCP 设置

#### 最快的成功之路

使用预构建的 [Docs Assistant - Mintlify agent](<https://continue.dev/continuedev/docs-mintlify>)，其中包括 Continue 文档 MCP，并且可以立即使用。

#### ⚡ 快速入门（推荐）

```bash
# From your Continue docs directory
cn --config continuedev/docs-mintlify
```

此 agent 包括：
- **Continue 文档 MCP** 用于搜索 Continue 文档
- **Mintlify 格式化规则 ** 以正确使用组件
- **针对常见任务的以文档为中心的提示**

#### 🛠️定制Agent

如果您想自定义，请创建您自己的 agent 并添加：
1. [Continue 文档 MCP](<https://continue.dev/continuedev/continue-docs-mcp>)
2. [Mintlify 技术写作规则](<https://continue.dev/mintlify/technical-writing-rule>)

详情请参阅[CONTRIBUTING指南](<https://docs.continue.dev/CONTRIBUTING>)。

---

## Continue 文档 MCP 是什么？

#### Continue 文档 MCP

使用 [Mintlify 的 MCP 生成 ](<https://www.mintlify.com/blog/generate-mcp-servers-for-your-docs>) 构建的 Model Context Protocol 服务器，支持跨 Continue 文档进行语义搜索。 [了解更多→](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue 文档 MCP 检索服务的配置和使用.md>)

MCP可以帮助您：
- **从现有文档中查找示例**
- **与既定模式保持一致性**
- **来源准确信息**有关Continue功能
- **更快地编写更好的文档**

---

## 带提示的文档工作流程

### 🆕 创建一本新的食谱

食谱展示了如何将 Continue CLI 与特定工具或服务结合使用。以下是如何使用 Continue 文档 MCP 创建一个：

#### 研究现有的食谱

```bash
cn --config continuedev/docs-mintlify
```

**提示：**
```
"Show me the structure of existing MCP cookbooks in the Continue docs.
I want to create a cookbook for GitHub MCP."
```

agent 将找到 dlt、Snyk 和 Sanity 食谱等示例。

#### 来源官方文档

**提示：**
```
"Using the Continue Docs MCP, find information about how GitHub MCP works.
Then search the web for the official GitHub MCP documentation and combine
both sources to create a cookbook following the same structure as the
dlt cookbook."
```

agent 将：
- 在 Continue 文档中搜索 MCP 模式
- 获取GitHubMCP官方文档
- 合并两个来源
- 生成格式一致的食谱

#### 添加到导航

**提示：**
```
"Add my new github-mcp-continue-cookbook.mdx to the docs.json navigation
under the Cookbooks section."
```

#### 预览和迭代

```bash
npm run dev
# Visit http://localhost:3000
```

**根据提示进行优化：**
```
"Add more examples to the troubleshooting section"
"Include a CI/CD workflow example using GitHub Actions"
"Add authentication setup using environment variables"
```

**说明：**

**专业提示：** agent 使用 Continue 文档 MCP 自动保持与现有食谱的一致性。

---

### ✏️更新现有文档

#### 查找文档

**提示：**
```
"Where is the MCP tools documentation located? Show me the file path."
```

#### 进行有针对性的更新

**提示：**
```
"Update the MCP tools documentation at docs/customization/mcp-tools.mdx
to include information about a new MCP server. Use the Continue
Docs MCP to find examples of how other MCP servers are documented, then
add a similar section for the new server."
```

---

### 📝 添加新指南

#### 研究类似内容

**提示：**
```
"I want to create a guide for setting up Continue with Amazon Bedrock.
Search the Continue docs for similar model provider setup guides and
show me the common structure they follow."
```

#### 创建指南

**提示：**
```
"Create a new guide at docs/guides/amazon-bedrock-setup.mdx following
the structure you found. Include:
- Prerequisites
- Step-by-step setup with code examples
- Configuration options
- Troubleshooting common issues

Use the Continue Docs MCP to find accurate information about Continue's
model provider configuration."
```

#### 添加导航条目

**提示：**
```
"Add this guide to docs.json under the Model Providers section"
```

---

## 现实世界的食谱示例

### 示例 1：PostgreSQL MCP 食谱

**提示：**
```
"Create a cookbook for using Continue with PostgreSQL MCP. Follow the same
structure as the dlt cookbook, but customize it for database operations.

Include these sections:
1. Quick start with pre-built agent
2. Manual setup with MCP configuration
3. Common database tasks (schema exploration, query writing, migrations)
4. Troubleshooting database connection issues

Use the Continue Docs MCP to find MCP configuration patterns and search the
web for PostgreSQL MCP documentation."
```

### 示例 2：Sentry 错误跟踪手册

**提示：**
```
"Create a cookbook showing how to use Continue to analyze Sentry errors.

The cookbook should demonstrate:
1. Setting up Sentry MCP integration
2. Querying recent errors
3. Analyzing error patterns with AI
4. Generating fixes for common errors
5. CI/CD integration for automated error analysis

Research the Snyk cookbook structure since it's also about error detection,
and adapt it for Sentry."
```

### 示例 3：OpenAPI 文档手册

**提示：**
```
"I want to create a cookbook for using Continue to work with OpenAPI specs.

Show how to:
1. Load OpenAPI specs into Continue's context
2. Generate API client code from specs
3. Create tests based on API endpoints
4. Update documentation when APIs change

Use the Continue Docs MCP to find how context providers work, then combine
that with OpenAPI MCP information."
```

---

## 高级提示模式

#### 多源研究

```bash
"Use the Continue Docs MCP to understand how agents
work in Continue, then search the web for Anthropic's
Computer Use MCP. Create a cookbook showing how to
combine Continue agents with Computer Use for
automated testing."
```

#### 交叉参考文档

```bash
"Find all mentions of MCP servers across Continue
documentation. Then create a comprehensive reference
page that links to all MCP-related guides and
configurations."
```

#### 一致性检查

```bash
"Review all cookbook files in docs/guides/ and check
if they follow the same structure. List any
inconsistencies and suggest updates to make them
uniform."
```

#### 示例提取

```bash
"Extract all code examples showing MCP server
configuration from the Continue docs. Format them
as a single reference page with explanations for
each pattern."
```

---

## 测试您的文档

### 本地预览

```bash
cd docs
npm run dev
# Visit http://localhost:3000
```

### 验证提示

```bash
# Check formatting
"Review this documentation for Mintlify formatting issues and fix any problems"

# Verify links
"Check all links in this file and ensure they point to existing documentation"

# Test code examples
"Verify that all code examples in this cookbook are syntactically correct"
```

---

## 提交您的贡献

**提示：**
```bash
"Create a pull request with my documentation changes and use the repo's
PR template to write the description"
```

agent 将：
- 创建功能分支
- 暂存并提交您的更改
- 推到你的叉子上
- 根据模板生成 PR 描述
- 打开【PR】进行审核

**说明：**

有关手动 PR 提交详细信息，请参阅完整的 [ 贡献指南 ](<https://docs.continue.dev/CONTRIBUTING>)。

---

## 使用 GitHub 操作进行自动文档检查

使用 Continue 文档 MCP agent 将自动文档检查添加到您的 PR 工作流程：

**代码示例：** `.github/workflows/docs-check.yml`

```yaml
name: Documentation Check

on:
  pull_request:
    types: [opened, synchronize]
    paths:
      - 'core/**'
      - 'extensions/**'
      - 'packages/**'
      - 'gui/**'

jobs:
  check-docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install Continue CLI
        run: npm i -g @continuedev/cli

      - name: Analyze Changes for Documentation Needs
        id: analyze
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
        run: |
          echo "🔍 Analyzing code changes for documentation needs..."

          # Get changed files
          git diff origin/${{ github.base_ref }}..HEAD --name-only > changed_files.txt

          # Create detailed diff
          git diff origin/${{ github.base_ref }}..HEAD > changes.diff

          # Use Continue agent to check if docs need updates
          PROMPT="Review these code changes and determine if documentation updates are needed.

          Changed files: $(cat changed_files.txt | tr '\n' ' ')

          Consider:
          - New features or APIs
          - Breaking changes
          - New configuration options
          - Modified CLI commands
          - New MCP integrations

          If docs updates are needed, specify which files in docs/ should be updated.
          If no updates needed, respond with 'NO_DOCS_NEEDED'.
          Be specific and concise."

          cn --config continuedev/docs-mintlify -p "$PROMPT" --auto > analysis.md || {
            echo "⚠️ Analysis failed"
            echo "needs_docs=false" >> $GITHUB_OUTPUT
            exit 0
          }

          if grep -q "NO_DOCS_NEEDED" analysis.md; then
            echo "needs_docs=false" >> $GITHUB_OUTPUT
          else
            echo "needs_docs=true" >> $GITHUB_OUTPUT
          fi

      - name: Post Documentation Recommendation
        if: steps.analyze.outputs.needs_docs == 'true'
        env:
          GH_TOKEN: ${{ github.token }}
        run: |
          echo "📝 Creating PR comment with documentation recommendations..."

          cat > pr-comment.md <<'EOF'
## 📚 Documentation Update Recommended

Based on the code changes in this PR, documentation updates may be needed:

EOF

          cat analysis.md >> pr-comment.md

          cat >> pr-comment.md <<'EOF'

---

### Next Steps

1. Review the suggested documentation areas above
2. Update relevant files in the `/docs` folder
3. Consider updating:
   - API references for interface changes
   - Guides for workflow changes
   - Configuration docs for new settings
   - MCP cookbooks for new integrations

### Resources
- [Contributing to Docs](/CONTRIBUTING)
- [Continue Docs MCP Cookbook](/guides/continue-docs-mcp-cookbook)

*This analysis was generated using the Continue Docs MCP agent.*
EOF

          gh pr comment ${{ github.event.pull_request.number }} --body-file pr-comment.md

      - name: Add Label
        if: steps.analyze.outputs.needs_docs == 'true'
        env:
          GH_TOKEN: ${{ github.token }}
        run: |
          gh pr edit ${{ github.event.pull_request.number }} --add-label "docs-needed"
```

**说明：**

此工作流程使用 Continue 文档 MCP agent 来分析代码更改，并在建议文档更新时自动对 PR 进行评论。

---

## 其他项目的文档 MCP

想要为您自己的项目创建文档 MCP？ Minlify 让一切变得简单：

#### 设置 Mintlify 文档

```bash
npm i -g mintlify
mintlify init
```

#### MCP 自动生成

Mintlify 会自动为您的文档生成 MCP 服务器，从而启用语义搜索。

[了解更多关于 Mintlify MCP 生成 →](<https://www.mintlify.com/blog/generate-mcp-servers-for-your-docs>)

#### 分享您的MCP

分享您的文档 MCP，以便其他人可以将其与 Continue agents 一起使用。

**说明：**

[Continue 文档 MCP](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue 文档 MCP 检索服务的配置和使用.md>) 本身就是这样构建的！

---

## 关键提示备忘单

|任务|提示|
|:-----|:-------|
|**查找结构**|“显示 Continue 文档中现有食谱的结构”|
|**创建食谱**|“按照 dlt 说明书结构为 [ 工具 ] MCP 创建说明书”|
|**更新文档**|“更新 [ 文件 ] 以包含有关 [ 功能 ] 的信息，使用 Continue 文档 MCP 查找示例”|
|**添加导航**|“将 [ 文件 ] 添加到 [ 部分 ] 部分下的 docs.json”|
|**检查一致性**|“检查此文件是否与其他 Continue 文档保持一致”|
|**修复格式**|“检查 Mintlify 格式问题并修复任何问题”|
|**摘录示例**|“在 Continue 文档中查找 [ 主题 ] 的所有示例”|
|**研究特色**|“使用 Continue 文档 MCP 解释 [ 功能 ] 在 Continue 中的工作原理”|

---

## 资源

### Continue 文档
- [贡献指南](<https://docs.continue.dev/CONTRIBUTING>) - 设置和提交流程
- [Continue 文档 MCP 参考](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue 文档 MCP 检索服务的配置和使用.md>) - MCP 服务器详细信息
  - [了解配置](<https://docs.continue.dev/guides/understanding-configs>) - 配置如何工作

### 最小化
- [MCP生成指南](<https://www.mintlify.com/blog/generate-mcp-servers-for-your-docs>)
- [Mintlify 文档](<https://mintlify.com/docs>)

---

## 下一步

#### [开始贡献](<https://docs.continue.dev/CONTRIBUTING>)

设置您的环境

#### [使用文档 Agent](<https://continue.dev/continuedev/docs-mintlify>)

安装预建的 agent

#### [浏览食谱](<https://docs.continue.dev/guides/overview>)

请参阅食谱示例


## 原文对照

<details>
<summary>展开完整原文</summary>

#### What You'll Build

Master using the Continue Docs MCP to contribute documentation, create cookbooks, and maintain consistency across Continue's docs - all through natural language prompts.

## Prerequisites

Before starting, ensure you have:

- Read: [Contributing to Continue Documentation](<https://docs.continue.dev/CONTRIBUTING>) for setup instructions
- Forked the [continuedev/continue](<https://github.com/continuedev/continue>) repository
- Node.js 20+ and Continue CLI installed

**注意：**

This cookbook assumes you've read the setup in the [CONTRIBUTING guide](<https://docs.continue.dev/CONTRIBUTING>). If you haven't, start there first.

## Continue Docs MCP Setup

#### Fastest Path to Success

Use the pre-built [Docs Assistant - Mintlify agent](<https://continue.dev/continuedev/docs-mintlify>) that includes the Continue Docs MCP and is ready to use immediately.

#### ⚡ Quick Start (Recommended)

```bash
# From your Continue docs directory
cn --config continuedev/docs-mintlify
```

This agent includes:
- **Continue Docs MCP** for searching Continue documentation
- **Mintlify formatting rules** for proper component usage
- **Documentation-focused prompts** for common tasks

#### 🛠️ Custom Agent

If you want to customize, create your own agent and add:
1. [Continue Docs MCP](<https://continue.dev/continuedev/continue-docs-mcp>)
2. [Mintlify Technical Writing Rule](<https://continue.dev/mintlify/technical-writing-rule>)

See the [CONTRIBUTING guide](<https://docs.continue.dev/CONTRIBUTING>) for details.

---

## What is the Continue Docs MCP?

#### Continue Docs MCP

A Model Context Protocol server built with [Mintlify's MCP generation](<https://www.mintlify.com/blog/generate-mcp-servers-for-your-docs>) that enables semantic search across Continue documentation. [Learn more →](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue 文档 MCP 检索服务的配置和使用.md>)

The MCP helps you:
- **Find examples** from existing documentation
- **Maintain consistency** with established patterns
- **Source accurate information** about Continue features
- **Write better documentation** faster

---

## Documentation Workflows with Prompts

### 🆕 Creating a New Cookbook

Cookbooks show how to use Continue CLI with specific tools or services. Here's how to create one using the Continue Docs MCP:

#### Research Existing Cookbooks

```bash
cn --config continuedev/docs-mintlify
```

**Prompt:**
```
"Show me the structure of existing MCP cookbooks in the Continue docs.
I want to create a cookbook for GitHub MCP."
```

The agent will find examples like the dlt, Snyk, and Sanity cookbooks.

#### Source Official Documentation

**Prompt:**
```
"Using the Continue Docs MCP, find information about how GitHub MCP works.
Then search the web for the official GitHub MCP documentation and combine
both sources to create a cookbook following the same structure as the
dlt cookbook."
```

The agent will:
- Search Continue docs for MCP patterns
- Fetch GitHub MCP official documentation
- Combine both sources
- Generate a cookbook with consistent formatting

#### Add to Navigation

**Prompt:**
```
"Add my new github-mcp-continue-cookbook.mdx to the docs.json navigation
under the Cookbooks section."
```

#### Preview and Iterate

```bash
npm run dev
# Visit http://localhost:3000
```

**Refine with prompts:**
```
"Add more examples to the troubleshooting section"
"Include a CI/CD workflow example using GitHub Actions"
"Add authentication setup using environment variables"
```

**说明：**

**Pro Tip:** The agent uses the Continue Docs MCP to maintain consistency with existing cookbooks automatically.

---

### ✏️ Updating Existing Documentation

#### Find the Documentation

**Prompt:**
```
"Where is the MCP tools documentation located? Show me the file path."
```

#### Make Targeted Updates

**Prompt:**
```
"Update the MCP tools documentation at docs/customization/mcp-tools.mdx
to include information about a new MCP server. Use the Continue
Docs MCP to find examples of how other MCP servers are documented, then
add a similar section for the new server."
```

---

### 📝 Adding New Guides

#### Research Similar Content

**Prompt:**
```
"I want to create a guide for setting up Continue with Amazon Bedrock.
Search the Continue docs for similar model provider setup guides and
show me the common structure they follow."
```

#### Create the Guide

**Prompt:**
```
"Create a new guide at docs/guides/amazon-bedrock-setup.mdx following
the structure you found. Include:
- Prerequisites
- Step-by-step setup with code examples
- Configuration options
- Troubleshooting common issues

Use the Continue Docs MCP to find accurate information about Continue's
model provider configuration."
```

#### Add Navigation Entry

**Prompt:**
```
"Add this guide to docs.json under the Model Providers section"
```

---

## Real-World Cookbook Examples

### Example 1: PostgreSQL MCP Cookbook

**Prompt:**
```
"Create a cookbook for using Continue with PostgreSQL MCP. Follow the same
structure as the dlt cookbook, but customize it for database operations.

Include these sections:
1. Quick start with pre-built agent
2. Manual setup with MCP configuration
3. Common database tasks (schema exploration, query writing, migrations)
4. Troubleshooting database connection issues

Use the Continue Docs MCP to find MCP configuration patterns and search the
web for PostgreSQL MCP documentation."
```

### Example 2: Sentry Error Tracking Cookbook

**Prompt:**
```
"Create a cookbook showing how to use Continue to analyze Sentry errors.

The cookbook should demonstrate:
1. Setting up Sentry MCP integration
2. Querying recent errors
3. Analyzing error patterns with AI
4. Generating fixes for common errors
5. CI/CD integration for automated error analysis

Research the Snyk cookbook structure since it's also about error detection,
and adapt it for Sentry."
```

### Example 3: OpenAPI Documentation Cookbook

**Prompt:**
```
"I want to create a cookbook for using Continue to work with OpenAPI specs.

Show how to:
1. Load OpenAPI specs into Continue's context
2. Generate API client code from specs
3. Create tests based on API endpoints
4. Update documentation when APIs change

Use the Continue Docs MCP to find how context providers work, then combine
that with OpenAPI MCP information."
```

---

## Advanced Prompt Patterns

#### Multi-Source Research

```bash
"Use the Continue Docs MCP to understand how agents
work in Continue, then search the web for Anthropic's
Computer Use MCP. Create a cookbook showing how to
combine Continue agents with Computer Use for
automated testing."
```

#### Cross-Reference Documentation

```bash
"Find all mentions of MCP servers across Continue
documentation. Then create a comprehensive reference
page that links to all MCP-related guides and
configurations."
```

#### Consistency Checking

```bash
"Review all cookbook files in docs/guides/ and check
if they follow the same structure. List any
inconsistencies and suggest updates to make them
uniform."
```

#### Example Extraction

```bash
"Extract all code examples showing MCP server
configuration from the Continue docs. Format them
as a single reference page with explanations for
each pattern."
```

---

## Testing Your Documentation

### Local Preview

```bash
cd docs
npm run dev
# Visit http://localhost:3000
```

### Validation Prompts

```bash
# Check formatting
"Review this documentation for Mintlify formatting issues and fix any problems"

# Verify links
"Check all links in this file and ensure they point to existing documentation"

# Test code examples
"Verify that all code examples in this cookbook are syntactically correct"
```

---

## Submitting Your Contribution

**Prompt:**
```bash
"Create a pull request with my documentation changes and use the repo's
PR template to write the description"
```

The agent will:
- Create a feature branch
- Stage and commit your changes
- Push to your fork
- Generate a PR description following the template
- Open the PR for review

**说明：**

See the full [Contributing Guide](<https://docs.continue.dev/CONTRIBUTING>) for manual PR submission details.

---

## Automated Documentation Checks with GitHub Actions

Add automated documentation checks to your PR workflow using the Continue Docs MCP agent:

**代码示例：** `.github/workflows/docs-check.yml`

```yaml
name: Documentation Check

on:
  pull_request:
    types: [opened, synchronize]
    paths:
      - 'core/**'
      - 'extensions/**'
      - 'packages/**'
      - 'gui/**'

jobs:
  check-docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install Continue CLI
        run: npm i -g @continuedev/cli

      - name: Analyze Changes for Documentation Needs
        id: analyze
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
        run: |
          echo "🔍 Analyzing code changes for documentation needs..."

          # Get changed files
          git diff origin/${{ github.base_ref }}..HEAD --name-only > changed_files.txt

          # Create detailed diff
          git diff origin/${{ github.base_ref }}..HEAD > changes.diff

          # Use Continue agent to check if docs need updates
          PROMPT="Review these code changes and determine if documentation updates are needed.

          Changed files: $(cat changed_files.txt | tr '\n' ' ')

          Consider:
          - New features or APIs
          - Breaking changes
          - New configuration options
          - Modified CLI commands
          - New MCP integrations

          If docs updates are needed, specify which files in docs/ should be updated.
          If no updates needed, respond with 'NO_DOCS_NEEDED'.
          Be specific and concise."

          cn --config continuedev/docs-mintlify -p "$PROMPT" --auto > analysis.md || {
            echo "⚠️ Analysis failed"
            echo "needs_docs=false" >> $GITHUB_OUTPUT
            exit 0
          }

          if grep -q "NO_DOCS_NEEDED" analysis.md; then
            echo "needs_docs=false" >> $GITHUB_OUTPUT
          else
            echo "needs_docs=true" >> $GITHUB_OUTPUT
          fi

      - name: Post Documentation Recommendation
        if: steps.analyze.outputs.needs_docs == 'true'
        env:
          GH_TOKEN: ${{ github.token }}
        run: |
          echo "📝 Creating PR comment with documentation recommendations..."

          cat > pr-comment.md <<'EOF'
## 📚 Documentation Update Recommended

Based on the code changes in this PR, documentation updates may be needed:

EOF

          cat analysis.md >> pr-comment.md

          cat >> pr-comment.md <<'EOF'

---

### Next Steps

1. Review the suggested documentation areas above
2. Update relevant files in the `/docs` folder
3. Consider updating:
   - API references for interface changes
   - Guides for workflow changes
   - Configuration docs for new settings
   - MCP cookbooks for new integrations

### Resources
- [Contributing to Docs](/CONTRIBUTING)
- [Continue Docs MCP Cookbook](/guides/continue-docs-mcp-cookbook)

*This analysis was generated using the Continue Docs MCP agent.*
EOF

          gh pr comment ${{ github.event.pull_request.number }} --body-file pr-comment.md

      - name: Add Label
        if: steps.analyze.outputs.needs_docs == 'true'
        env:
          GH_TOKEN: ${{ github.token }}
        run: |
          gh pr edit ${{ github.event.pull_request.number }} --add-label "docs-needed"
```

**说明：**

This workflow uses the Continue Docs MCP agent to analyze code changes and automatically comment on PRs when documentation updates are recommended.

---

## Documentation MCP for Other Projects

Want to create documentation MCPs for your own projects? Mintlify makes it easy:

#### Set Up Mintlify Docs

```bash
npm i -g mintlify
mintlify init
```

#### MCP Auto-Generated

Mintlify automatically generates an MCP server for your documentation, enabling semantic search.

[Learn more about Mintlify MCP generation →](<https://www.mintlify.com/blog/generate-mcp-servers-for-your-docs>)

#### Share Your MCP

Share your docs MCP so others can use it with Continue agents.

**说明：**

The [Continue Docs MCP](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue 文档 MCP 检索服务的配置和使用.md>) itself was built this way!

---

## Key Prompts Cheat Sheet

| Task | Prompt |
|:-----|:-------|
| **Find structure** | "Show me the structure of existing cookbooks in Continue docs" |
| **Create cookbook** | "Create a cookbook for [tool] MCP following the dlt cookbook structure" |
| **Update docs** | "Update [file] to include information about [feature], using Continue Docs MCP to find examples" |
| **Add navigation** | "Add [file] to docs.json under the [section] section" |
| **Check consistency** | "Review this file for consistency with other Continue documentation" |
| **Fix formatting** | "Review for Mintlify formatting issues and fix any problems" |
| **Extract examples** | "Find all examples of [topic] in Continue docs" |
| **Research feature** | "Use Continue Docs MCP to explain how [feature] works in Continue" |

---

## Resources

### Continue Documentation
- [Contributing Guide](<https://docs.continue.dev/CONTRIBUTING>) - Setup and submission process
- [Continue Docs MCP Reference](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue 文档 MCP 检索服务的配置和使用.md>) - MCP server details
  - [Understanding Configs](<https://docs.continue.dev/guides/understanding-configs>) - How configs work

### Mintlify
- [MCP Generation Guide](<https://www.mintlify.com/blog/generate-mcp-servers-for-your-docs>)
- [Mintlify Documentation](<https://mintlify.com/docs>)

---

## Next Steps

#### [Start Contributing](<https://docs.continue.dev/CONTRIBUTING>)

Set up your environment

#### [Use Docs Agent](<https://continue.dev/continuedev/docs-mintlify>)

Install pre-built agent

#### [Browse Cookbooks](<https://docs.continue.dev/guides/overview>)

See cookbook examples


</details>
