# 用 Continue 与 Sanity MCP 开发内容管理流程

有条件工程教程。展示内容 Schema、GROQ 查询、迁移、本地化、文档生成、发布和 GitHub Actions；用于 CMS 应用开发与维护参考。

- 来源：Continue
- 资料类型：有条件工程教程
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/bd36638ee0cf9fb90c314647bab2f1c6897aa6fe>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：依赖 Sanity 项目、数据集及权限；业务操作样例不能直接当通用开发规范。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/sanity-mcp-continue-cookbook.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/用 Continue 与 Sanity MCP 开发内容管理流程.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Content Management with Sanity MCP and Continue"
description: "Set up an AI-powered content management workflow that helps you manage schemas, run GROQ queries, handle documentation, and perform migrations using natural language commands."
sidebarTitle: "Sanity CMS with Continue"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### 你将构建什么

由 AI- 提供支持的内容管理系统工作流程，使用 Continue 的 AI agent 和 Sanity
MCP 来管理模式、执行 GROQ 查询、处理迁移和维护文档 - 所有这些都通过简单的自然语言提示进行

## 先决条件

开始之前，请确保您拥有：

- Node.js 20+本地安装
- 一个 [Sanity 帐户 ](<https://www.sanity.io/>) 和项目（免费层作品）
- 对内容管理系统的基本了解

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

#### 安装理智 CLI

```bash
npm install -g @sanity/cli
```

**注意：**

要在无头模式下使用 agents，您需要 [Continue API 密钥 ](<https://continue.dev/settings/api-keys>) 和正确的环境变量配置。

## 开始保持理智

#### 刚接触理智？从这里开始

刚接触理智？按照 [Sanity 入门指南 ](<https://www.sanity.io/docs/getting-started>) 设置您的第一个项目。

**快速设置：**
```bash
npm create sanity@latest
cd your-project-name
npm run dev
```

**关键资源：**
- [Sanity Studio](<https://www.sanity.io/docs/sanity-studio>) - 内容编辑界面
- [架构类型](<https://www.sanity.io/docs/schema-types>) - 定义内容结构
- [GROQ](<https://www.sanity.io/docs/groq>) - 用于获取内容的查询语言
- [Content Lake](<https://www.sanity.io/docs/datastore>) - 实时数据存储

## Sanity MCP 工作流程选项

#### 最快的成功之路

跳过手动设置并使用我们预构建的 Sanity Assistant agent，其中包括 
 Sanity MCP 和优化的内容管理工作流程，以获得更一致的结果。您可以根据您的特定需求对其进行自定义。

确保满足上述 **先决条件 ** 后，您有两条途径可以开始：

#### ⚡ 快速入门（推荐）

#### 加载预建的Agent

运行：

```bash
cn --agent continuedev/sanity-assistant-agent
```

此 agent 包括：
- **[Sanity MCP](<https://continue.dev/sanity/sanity-mcp>)** 已预先配置并可供使用
- **内容管理规则**最佳实践
- **架构优化**指南

#### 您的第一次 MCP 对话

从这些适合初学者的提示开始探索您的内容：

```bash
# Content exploration
"Show me all blog posts published in the last month"

# Schema understanding
"Show me all the document types in my Sanity schema and explain their relationships"

# Content management
"Create a new product page for our upcoming feature"
```

就是这样！ agent 自动处理一切。有关更多入门提示和示例，请参阅 [ 官方 Sanity MCP 博客文章 ](<https://www.sanity.io/blog/introducing-sanity-model-context-protocol-server>)。

**说明：**

**为什么使用 Agent？** 预构建的 Sanity Assistant agent 提供一致的内容管理工作流程并自动处理 MCP 配置，从而更轻松地开始使用 AI- 驱动的 CMS 操作。得益于 [Sanity MCP](<https://continue.dev/sanity/sanity-mcp>) 集成和预先测试的提示，结果更加一致，调试也更加容易。

#### 🛠️手动设置

#### 将理智 MCP 添加到 Continue CLI

将 Sanity MCP 添加到您的配置中：

**提示：**

**安装方法：**
1. **快速CLI安装**：`cn --mcp sanity/sanity-mcp`
2. **手动配置**：将MCP添加到您的agent配置中

安装后，Sanity MCP 工具即可供您的 Continue agent 使用以获取所有提示。

**说明：**

**身份验证选项：**
- **交互模式**：通过浏览器进行OAuth身份验证（7天后过期）
- **Headless/CI 模式**：使用环境变量（SANITY_API_TOKEN、SANITY_PROJECT_ID 等）

#### 您的第一次 MCP 对话

从这些适合初学者的提示开始：
```bash
cn
# Try these starter prompts:
# "Show me all blog posts published in the last month"
# "Show me all the document types in my Sanity schema and explain their relationships"
# "Create a new product page for our upcoming feature"
```

有关更多示例，请参阅 [Sanity MCP 博客文章 ](<https://www.sanity.io/blog/introducing-sanity-model-context-protocol-server>)。

#### Agent 要求

要使用预构建的 Sanity Assistant agent，您需要：
    - **Continue CLI 专业计划 ** 以及模型附加组件，OR
    - **您自己的API密钥**配置为环境变量

agent 将自动检测并使用您的配置以及预配置的 Sanity MCP 进行内容操作。请注意，首次使用时需要进行 OAuth 身份验证。

---

## 理智 MCP 能力

#### 了解能力

**Sanity MCP** 提供全面的内容管理工具：
- 执行[GROQ查询](<https://www.sanity.io/docs/groq>)以获取和分析内容
- 探索和修改 [文档架构](<https://www.sanity.io/docs/schema-types>)
- 管理[内容发布](<https://www.sanity.io/docs/release-schedules>)和版本
- 处理环境之间的内容 [ 迁移 ](<https://www.sanity.io/docs/migrating-data>)
- 自动生成文档
- 执行批量内容操作
- 管理 [ 本地化 ](<https://www.sanity.io/docs/localization>) 和翻译

MCP 与您现有的 Sanity 工作区无缝集成，为开发和内容运营提供 AI- 强大的帮助。

---

## 您的第一次 MCP 对话

一切设置完毕后，您就可以开始第一次 AI- 支持的内容对话！尝试这些适合初学者的入门提示：

#### 内容发现

```
"Show me all blog posts published in the last month"
```

#### 内容创作

```
"Create a new product page for our upcoming feature"
```

#### 内容更新

```
"Update our pricing information across all service pages"
```

#### 内容安排

```
"Schedule the Easter marketing campaign content release for next Tuesday"
```

**说明：**

**MCP新手？** 这些提示展示了自然语言内容管理的强大功能。有关更多示例和详细说明，请查看 [ 官方 Sanity MCP 博文 ](<https://www.sanity.io/blog/introducing-sanity-model-context-protocol-server>)。

## 内容管理秘诀

现在，您可以使用自然语言提示来管理您的 Sanity 内容和架构。 Continue agent 自动调用适当的 Sanity MCP 工具。

**说明：**

**在哪里运行这些工作流程：**
- **IDE 扩展 **：在 VS Code、JetBrains 或其他支持的 IDE 中使用 Continue
- **终端（TUI模式）**：运行`cn`进入交互模式，然后输入提示
- **CLI（无头模式）**：使用 `cn -p "your prompt"` 进行无头命令

**首先在计划模式下测试**：在运行可能会导致
更改的操作之前，请在计划模式下测试提示（请参阅[计划模式
指南](<https://github.com/guides/plan-mode-guide>)；按**Shift+Tab**切换TUI/IDE中的模式）。这个
向您展示了agent在不执行它的情况下会做什么。

要在无头模式下运行下面的任何示例提示，请使用 `cn -p "prompt"`

**说明：**

**关于--auto标志**：`--auto`标志使工具能够连续运行，无需手动确认。这对于无头模式至关重要，在无头模式中，agent 需要自动执行多个工具来完成模式探索、GROQ 执行和内容迁移等任务。

### 模式管理

#### 探索文档类型

查看并了解您的内容架构结构。

**提示：**
```
Show me all document types in my Sanity schema with their fields,
validation rules, and relationships to other types.
```

### GROQ 查询

#### 执行 GROQ 查询

运行 GROQ 查询来获取和分析内容。

**提示：**
```
Run a GROQ query to fetch all articles published in the last 30 days,
including their titles, authors, categories, and view counts.
```

### 内容运营

#### 批量内容更新

对您的内容执行批量操作。

**提示：**
```
Find all blog posts with the category "News" and update their
status to "archived" if they are older than 6 months.
```

### 本地化

#### 添加本地化支持

设置和管理内容翻译。

**提示：**
```
Add localization support to my article document type for
Spanish and French languages with appropriate field configurations.
```

### 内容迁移

#### 迁移内容结构

在不同架构或环境之间迁移内容。

**提示：**
```
Help me migrate my blog posts from the old schema structure
to the new one, mapping the deprecated fields to the new format.
```

### 文档生成

#### 生成架构文档

自动为您的内容模型生成文档。

**提示：**
```
Generate comprehensive documentation for my Sanity schema including
all document types, their purposes, field descriptions, and usage examples.
```

### 内容发布

#### 管理内容发布

使用 Sanity 的内容发布功能。

**提示：**
```
List all active releases in my dataset and show me the content
changes scheduled for each release.
```

### 绩效分析

#### 分析查询性能

优化 GROQ 查询以获得更好的性能。

**提示：**
```
Analyze the performance of my most frequent GROQ queries and
suggest optimizations to improve response times.
```

## 通过 GitHub 操作进行持续内容管理

此示例演示了一个 **连续 AI 工作流程 **，其中内容验证和架构检查使用 Sanity agent 配置在无头模式下在 CI/CD 管道中自动运行。考虑自定义此 agent 以添加您组织的特定内容治理规则。

### 添加 GitHub 秘密

导航到 ** 存储库设置 → 秘密和变量 → 操作 ** 并添加：

- `CONTINUE_API_KEY`：来自 [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>) 的 Continue API 密钥
- `SANITY_PROJECT_ID`：您的 Sanity 项目 ID
- `SANITY_DATASET`：您的 Sanity 数据集名称（通常为“生产”）
- `SANITY_API_TOKEN`：您的理智 API 令牌，具有适当的权限
- `MCP_USER_ROLE`：您的 MCP 用户角色（通常为“管理员”或“编辑者”）

**说明：**

该工作流程使用 [Sanity Assistant Agent](<https://continue.dev/continuedev/sanity-assistant-agent>)，通过 [Sanity MCP 配置 ](<https://continue.dev/sanity/sanity-mcp-config>) 进行环境变量身份验证。这使得无需 OAuth 浏览器身份验证即可实现无头模式操作。

### 创建工作流程文件

此工作流程使用 [ 无头模式 ](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue CLI 非交互运行与流水线调用.md>) 中的 Continue CLI 自动验证拉取请求中的 Sanity 架构和内容。它检查架构完整性，验证内容关系，并将摘要报告作为 PR 评论发布。

在您的存储库中创建 `.github/workflows/sanity-content-validation.yml`：

```yaml
name: Sanity Content Validation with MCP

on:
  pull_request:
    branches: [main]
  workflow_dispatch:

jobs:
  validate-content:
    runs-on: ubuntu-latest
    env:
      CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
      SANITY_PROJECT_ID: ${{ secrets.SANITY_PROJECT_ID }}
      SANITY_DATASET: ${{ secrets.SANITY_DATASET }}
      SANITY_API_TOKEN: ${{ secrets.SANITY_API_TOKEN }}
      MCP_USER_ROLE: ${{ secrets.MCP_USER_ROLE }}

    steps:
      - uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "18"

      - name: Install Sanity CLI
        run: |
          npm install -g @sanity/cli
          echo "✅ Sanity CLI installed"

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Validate Schema Structure
        run: |
          echo "🔍 Validating schema structure..."
          cn --agent continuedev/sanity-assistant-agent \
             -p "Analyze the Sanity schema for any structural issues,
                 missing required fields, or broken references between document types." \
             --auto

      - name: Check Content Integrity
        run: |
          echo "📊 Checking content integrity..."
          cn --agent continuedev/sanity-assistant-agent \
             -p "Run GROQ queries to identify any orphaned documents,
                 broken references, or missing required fields in the content." \
             --auto

      - name: Generate Schema Documentation
        run: |
          echo "📝 Generating schema documentation..."
          cn --agent continuedev/sanity-assistant-agent \
             -p "Generate a markdown summary of all schema changes
                 in this PR and their potential impact on existing content." \
             --auto > schema-changes.md

      - name: Comment Report on PR
        if: always() && github.event_name == 'pull_request'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          REPORT=$(          cn --agent continuedev/sanity-assistant-agent \
             -p "Generate a concise summary (200 words or less) of:
                 - Schema validation results
                 - Content integrity checks
                 - Any breaking changes detected
                 - Recommended actions before merging" \
             --auto)

          gh pr comment ${{ github.event.pull_request.number }} --body "$REPORT"
```

**说明：**

环境变量使 MCP 能够在没有 OAuth 浏览器提示的情况下进行身份验证。 [Sanity MCP Config](<https://continue.dev/sanity/sanity-mcp-config>) 文档提供了所有必需变量的详细设置说明。

## 内容管理最佳实践

使用 Continue 的规则系统实施自动内容质量检查。请参阅 [规则深入探讨 ](<https://docs.continue.dev/customize/deep-dives/rules>) 了解创作技巧。

#### 模式验证

```bash
"Before deploying schema changes, validate that all required fields
are present and that no breaking changes affect existing content."
```

#### 内容一致性

```bash
"When creating new content types, ensure they follow the existing
naming conventions and field patterns established in the schema."
```

#### 查询优化

```bash
"Review GROQ queries for performance issues and suggest indexes
or query restructuring to improve response times."
```

#### 本地化标准

```bash
"Ensure all user-facing content fields have proper localization
support configured for the required languages."
```

## 故障排除

### 身份验证问题

```bash
"Check if I'm properly authenticated with Sanity.
If not, help me set up OAuth or API token authentication."
```

### 未找到架构

```bash
"Verify that the Sanity project is properly configured
and that the schema files are accessible in the current directory."
```

### GROQ 查询错误

```bash
"Debug this GROQ query and explain why it's failing,
then provide a corrected version that achieves the intended result."
```

### 迁移冲突

**检查：**

**验证步骤：**
- Sanity MCP 已安装并配置
- 项目已通过 Sanity 验证
- 架构文件存在且有效
- 数据集权限配置正确

## 你建造了什么

完成本指南后，您将拥有一个完整的 **AI- 支持的内容管理系统 **，该系统：

- ✅ 使用自然语言 — 简单的提示而不是复杂的 CMS 命令
- ✅ 自动管理模式 - AI 处理模式演变和迁移
- ✅ 连续运行 - CI/CD 管道中的自动验证
- ✅ 确保质量 - 内容检查可防止损坏的引用和无效数据

#### 连续AI

您的内容管理工作流程现在在 **[ 级别 2 连续 
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI 运行，通过审核和批准变更，在人工监督下 
 处理日常内容操作和架构管理。

## 下一步

1. **探索您的架构** - 在当前项目上尝试架构探索提示
2. **运行 GROQ 查询** - 使用自然语言查询您的内容
3. **设置 CI 验证** - 将 GitHub 操作工作流程添加到您的存储库
4. **创建文档** - 为您的内容模型生成全面的文档
5. **优化性能** - 分析和提高查询性能

## 其他资源

#### [Sanity 文档](<https://www.sanity.io/docs>)

完整的 Sanity 平台文档

#### [Continue 文档](<https://docs.continue.dev>)

探索 Continue 文档和指南

#### [GROQ 文档](<https://www.sanity.io/docs/groq>)

学习 GROQ 查询语言

#### [理智 MCP 博客文章 ](<https://www.sanity.io/blog/introducing-sanity-model-context-protocol-server>)

官方 Sanity MCP 介绍并举例

#### [理智 MCP 设置 ](<https://www.sanity.io/docs/compute-and-ai/mcp-server>)

官方 Sanity MCP 文档


## 原文对照

<details>
<summary>展开完整原文</summary>

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### What You'll Build

An AI-powered content management system workflow that uses Continue's AI agent with Sanity
MCP to manage schemas, execute GROQ queries, handle migrations, and maintain documentation - all through simple natural language prompts

## Prerequisites

Before starting, ensure you have:

- Node.js 20+ installed locally
- A [Sanity account](<https://www.sanity.io/>) and project (free tier works)
- Basic understanding of content management systems

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

#### Install Sanity CLI

```bash
npm install -g @sanity/cli
```

**注意：**

To use agents in headless mode, you need a [Continue API key](<https://continue.dev/settings/api-keys>) and proper environment variable configuration.

## Getting Started with Sanity

#### New to Sanity? Start Here

New to Sanity? Follow the [Sanity Getting Started Guide](<https://www.sanity.io/docs/getting-started>) to set up your first project.

**Quick setup:**
```bash
npm create sanity@latest
cd your-project-name
npm run dev
```

**Key resources:**
- [Sanity Studio](<https://www.sanity.io/docs/sanity-studio>) - Content editing interface
- [Schema Types](<https://www.sanity.io/docs/schema-types>) - Define content structure
- [GROQ](<https://www.sanity.io/docs/groq>) - Query language for fetching content
- [Content Lake](<https://www.sanity.io/docs/datastore>) - Real-time data store

## Sanity MCP Workflow Options

#### Fastest Path to Success

Skip the manual setup and use our pre-built Sanity Assistant agent that includes
    the Sanity MCP and optimized content management workflows for more consistent results. You can customize it for your specific needs.

After ensuring you meet the **Prerequisites** above, you have two paths to get started:

#### ⚡ Quick Start (Recommended)

#### Load the Pre-Built Agent

Run:

```bash
cn --agent continuedev/sanity-assistant-agent
```

This agent includes:
- **[Sanity MCP](<https://continue.dev/sanity/sanity-mcp>)** pre-configured and ready to use
- **Content management rules** for best practices
- **Schema optimization** guidelines

#### Your First MCP Conversation

Start with these beginner-friendly prompts to explore your content:

```bash
# Content exploration
"Show me all blog posts published in the last month"

# Schema understanding
"Show me all the document types in my Sanity schema and explain their relationships"

# Content management
"Create a new product page for our upcoming feature"
```

That's it! The agent handles everything automatically. For more starter prompts and examples, see the [official Sanity MCP blog post](<https://www.sanity.io/blog/introducing-sanity-model-context-protocol-server>).

**说明：**

**Why Use the Agent?** The pre-built Sanity Assistant agent provides consistent content management workflows and handles MCP configuration automatically, making it easier to get started with AI-powered CMS operations. Results are more consistent and debugging is easier thanks to the [Sanity MCP](<https://continue.dev/sanity/sanity-mcp>) integration and pre-tested prompts.

#### 🛠️ Manual Setup

#### Add Sanity MCP to Continue CLI

Add the Sanity MCP to your configuration:

**提示：**

**Installation methods:**
1. **Quick CLI install**: `cn --mcp sanity/sanity-mcp`
2. **Manual configuration**: Add the MCP to your agent configuration

Once installed, Sanity MCP tools become available to your Continue agent for all prompts.

**说明：**

**Authentication Options:**
- **Interactive mode**: OAuth authentication via browser (expires after 7 days)
- **Headless/CI mode**: Uses environment variables (SANITY_API_TOKEN, SANITY_PROJECT_ID, etc.)

#### Your First MCP Conversation

Start with these beginner-friendly prompts:
```bash
cn
# Try these starter prompts:
# "Show me all blog posts published in the last month"
# "Show me all the document types in my Sanity schema and explain their relationships"
# "Create a new product page for our upcoming feature"
```

For more examples, see the [Sanity MCP blog post](<https://www.sanity.io/blog/introducing-sanity-model-context-protocol-server>).

#### Agent Requirements

  To use the pre-built Sanity Assistant agent, you need either:
    - **Continue CLI Pro Plan** with the models add-on, OR
    - **Your own API keys** configured as environment variables

  The agent will automatically detect and use your configuration along with the pre-configured Sanity MCP for content operations. Note that OAuth authentication will be required on first use.

---

## Sanity MCP Capabilities

#### Understanding the Capabilities

**Sanity MCP** provides comprehensive tools for content management:
- Execute [GROQ queries](<https://www.sanity.io/docs/groq>) to fetch and analyze content
- Explore and modify [document schemas](<https://www.sanity.io/docs/schema-types>)
- Manage [content releases](<https://www.sanity.io/docs/release-schedules>) and versions
- Handle content [migrations](<https://www.sanity.io/docs/migrating-data>) between environments
- Automate documentation generation
- Perform bulk content operations
- Manage [localization](<https://www.sanity.io/docs/localization>) and translations

The MCP integrates seamlessly with your existing Sanity workspace, providing AI-powered assistance for both development and content operations.

---

## Your First MCP Conversation

With everything set up, you're ready for your first AI-powered content conversation! Try these beginner-friendly starter prompts:

#### Content Discovery

```
"Show me all blog posts published in the last month"
```

#### Content Creation

```
"Create a new product page for our upcoming feature"
```

#### Content Updates

```
"Update our pricing information across all service pages"
```

#### Content Scheduling

```
"Schedule the Easter marketing campaign content release for next Tuesday"
```

**说明：**

**New to MCP?** These prompts demonstrate the power of natural language content management. For more examples and detailed explanations, check out the [official Sanity MCP blog post](<https://www.sanity.io/blog/introducing-sanity-model-context-protocol-server>).

## Content Management Recipes

Now you can use natural language prompts to manage your Sanity content and schemas. The Continue agent automatically calls the appropriate Sanity MCP tools.

**说明：**

**Where to run these workflows:**
- **IDE Extensions**: Use Continue in VS Code, JetBrains, or other supported IDEs
- **Terminal (TUI mode)**: Run `cn` to enter interactive mode, then type your prompts
- **CLI (headless mode)**: Use `cn -p "your prompt"` for headless commands

**Test in Plan Mode First**: Before running operations that might make
changes, test your prompts in plan mode (see the [Plan Mode
Guide](<https://github.com/guides/plan-mode-guide>); press **Shift+Tab** to switch modes in TUI/IDE). This
shows you what the agent will do without executing it.

To run any of the example prompts below in headless mode, use `cn -p "prompt"`

**说明：**

**About the --auto flag**: The `--auto` flag enables tools to run continuously without manual confirmation. This is essential for headless mode where the agent needs to execute multiple tools automatically to complete tasks like schema exploration, GROQ execution, and content migration.

### Schema Management

#### Explore Document Types

Review and understand your content schema structure.

**Prompt:**
```
Show me all document types in my Sanity schema with their fields,
validation rules, and relationships to other types.
```

### GROQ Queries

#### Execute GROQ Queries

Run GROQ queries to fetch and analyze content.

**Prompt:**
```
Run a GROQ query to fetch all articles published in the last 30 days,
including their titles, authors, categories, and view counts.
```

### Content Operations

#### Bulk Content Updates

Perform bulk operations on your content.

**Prompt:**
```
Find all blog posts with the category "News" and update their
status to "archived" if they are older than 6 months.
```

### Localization

#### Add Localization Support

Set up and manage content translations.

**Prompt:**
```
Add localization support to my article document type for
Spanish and French languages with appropriate field configurations.
```

### Content Migration

#### Migrate Content Structure

Migrate content between different schemas or environments.

**Prompt:**
```
Help me migrate my blog posts from the old schema structure
to the new one, mapping the deprecated fields to the new format.
```

### Documentation Generation

#### Generate Schema Documentation

Automatically generate documentation for your content model.

**Prompt:**
```
Generate comprehensive documentation for my Sanity schema including
all document types, their purposes, field descriptions, and usage examples.
```

### Content Releases

#### Manage Content Releases

Work with Sanity's content release feature.

**Prompt:**
```
List all active releases in my dataset and show me the content
changes scheduled for each release.
```

### Performance Analysis

#### Analyze Query Performance

Optimize GROQ queries for better performance.

**Prompt:**
```
Analyze the performance of my most frequent GROQ queries and
suggest optimizations to improve response times.
```

## Continuous Content Management with GitHub Actions

  This example demonstrates a **Continuous AI workflow** where content validation and schema checks run automatically in your CI/CD pipeline in headless mode using the Sanity agent config. Consider customizing this agent to add your organization's specific content governance rules.

### Add GitHub Secrets

Navigate to **Repository Settings → Secrets and variables → Actions** and add:

- `CONTINUE_API_KEY`: Your Continue API key from [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>)
- `SANITY_PROJECT_ID`: Your Sanity project ID
- `SANITY_DATASET`: Your Sanity dataset name (usually "production")
- `SANITY_API_TOKEN`: Your Sanity API token with appropriate permissions
- `MCP_USER_ROLE`: Your MCP user role (typically "admin" or "editor")

**说明：**

The workflow uses the [Sanity Assistant Agent](<https://continue.dev/continuedev/sanity-assistant-agent>) with environment variable authentication via [Sanity MCP Config](<https://continue.dev/sanity/sanity-mcp-config>). This enables headless mode operation without OAuth browser authentication.

### Create Workflow File

This workflow automatically validates your Sanity schemas and content on pull requests using the Continue CLI in [headless mode](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue CLI 非交互运行与流水线调用.md>). It checks schema integrity, validates content relationships, and posts a summary report as a PR comment.

Create `.github/workflows/sanity-content-validation.yml` in your repository:

```yaml
name: Sanity Content Validation with MCP

on:
  pull_request:
    branches: [main]
  workflow_dispatch:

jobs:
  validate-content:
    runs-on: ubuntu-latest
    env:
      CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
      SANITY_PROJECT_ID: ${{ secrets.SANITY_PROJECT_ID }}
      SANITY_DATASET: ${{ secrets.SANITY_DATASET }}
      SANITY_API_TOKEN: ${{ secrets.SANITY_API_TOKEN }}
      MCP_USER_ROLE: ${{ secrets.MCP_USER_ROLE }}

    steps:
      - uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "18"

      - name: Install Sanity CLI
        run: |
          npm install -g @sanity/cli
          echo "✅ Sanity CLI installed"

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Validate Schema Structure
        run: |
          echo "🔍 Validating schema structure..."
          cn --agent continuedev/sanity-assistant-agent \
             -p "Analyze the Sanity schema for any structural issues,
                 missing required fields, or broken references between document types." \
             --auto

      - name: Check Content Integrity
        run: |
          echo "📊 Checking content integrity..."
          cn --agent continuedev/sanity-assistant-agent \
             -p "Run GROQ queries to identify any orphaned documents,
                 broken references, or missing required fields in the content." \
             --auto

      - name: Generate Schema Documentation
        run: |
          echo "📝 Generating schema documentation..."
          cn --agent continuedev/sanity-assistant-agent \
             -p "Generate a markdown summary of all schema changes
                 in this PR and their potential impact on existing content." \
             --auto > schema-changes.md

      - name: Comment Report on PR
        if: always() && github.event_name == 'pull_request'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          REPORT=$(          cn --agent continuedev/sanity-assistant-agent \
             -p "Generate a concise summary (200 words or less) of:
                 - Schema validation results
                 - Content integrity checks
                 - Any breaking changes detected
                 - Recommended actions before merging" \
             --auto)

          gh pr comment ${{ github.event.pull_request.number }} --body "$REPORT"
```

**说明：**

Environment variables enable the MCP to authenticate without OAuth browser prompts. The [Sanity MCP Config](<https://continue.dev/sanity/sanity-mcp-config>) documentation provides detailed setup instructions for all required variables.

## Content Management Best Practices

Implement automated content quality checks using Continue's rule system. See the [Rules deep dive](<https://docs.continue.dev/customize/deep-dives/rules>) for authoring tips.

#### Schema Validation

```bash
"Before deploying schema changes, validate that all required fields
are present and that no breaking changes affect existing content."
```

#### Content Consistency

```bash
"When creating new content types, ensure they follow the existing
naming conventions and field patterns established in the schema."
```

#### Query Optimization

```bash
"Review GROQ queries for performance issues and suggest indexes
or query restructuring to improve response times."
```

#### Localization Standards

```bash
"Ensure all user-facing content fields have proper localization
support configured for the required languages."
```

## Troubleshooting

### Authentication Issues

```bash
"Check if I'm properly authenticated with Sanity.
If not, help me set up OAuth or API token authentication."
```

### Schema Not Found

```bash
"Verify that the Sanity project is properly configured
and that the schema files are accessible in the current directory."
```

### GROQ Query Errors

```bash
"Debug this GROQ query and explain why it's failing,
then provide a corrected version that achieves the intended result."
```

### Migration Conflicts

**检查：**

**Verification Steps:**
- Sanity MCP is installed and configured
- Project is authenticated with Sanity
- Schema files are present and valid
- Dataset permissions are correctly configured

## What You've Built

After completing this guide, you have a complete **AI-powered content management system** that:

- ✅ Uses natural language — Simple prompts instead of complex CMS commands
- ✅ Manages schemas automatically — AI handles schema evolution and migrations
- ✅ Runs continuously — Automated validation in CI/CD pipelines
- ✅ Ensures quality — Content checks prevent broken references and invalid data

#### Continuous AI

Your content management workflow now operates at **[Level 2 Continuous
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI handles routine content operations and schema management with human oversight
through review and approval of changes.

## Next Steps

1. **Explore your schema** - Try the schema exploration prompt on your current project
2. **Run GROQ queries** - Use natural language to query your content
3. **Set up CI validation** - Add the GitHub Actions workflow to your repo
4. **Create documentation** - Generate comprehensive docs for your content model
5. **Optimize performance** - Analyze and improve query performance

## Additional Resources

#### [Sanity Documentation](<https://www.sanity.io/docs>)

Complete Sanity platform documentation

#### [Continue Documentation](<https://docs.continue.dev>)

Explore Continue documentation and guides

#### [GROQ Documentation](<https://www.sanity.io/docs/groq>)

Learn GROQ query language

#### [Sanity MCP Blog Post](<https://www.sanity.io/blog/introducing-sanity-model-context-protocol-server>)

Official Sanity MCP introduction with examples

#### [Sanity MCP Setup](<https://www.sanity.io/docs/compute-and-ai/mcp-server>)

Official Sanity MCP documentation


</details>
