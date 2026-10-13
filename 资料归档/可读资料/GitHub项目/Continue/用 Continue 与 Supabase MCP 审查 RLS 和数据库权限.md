# 用 Continue 与 Supabase MCP 审查 RLS 和数据库权限

工程教程。展示凭据设置、RLS 漏洞查找、策略修复、Schema 分析、迁移生成和自动监测，对应数据库应用的安全检查与维护。

- 来源：Continue
- 资料类型：工程教程
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/f7f3ab7440341f052642b7d0fab72d40fd8e7ad0>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：需要 Supabase 项目和合适权限，迁移及安全修复须验证。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/supabase-mcp-database-workflow.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/用 Continue 与 Supabase MCP 审查 RLS 和数据库权限.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Supabase RLS Security Audit and Fixes with MCP"
description: "Learn how to audit Row Level Security in your Supabase database, identify vulnerabilities, and automatically generate fixes using Continue CLI and Supabase MCP."
sidebarTitle: "Supabase RLS Security Audit"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### 你将构建什么

安全审核工作流程，使用 Continue CLI 和 Supabase MCP 来识别 RLS 漏洞、生成安全策略、修复权限问题并确保您的数据库遵循安全最佳实践。

## 你将学到什么

这本食谱教你：

- 使用[Supabase MCP](<https://supabase.com/docs/guides/getting-started/mcp>)审计数据库安全
- 识别未启用行级安全性 (RLS) 的表
- 查找并修复过于宽松或缺失的 RLS 策略
- 按照最佳实践生成安全的 RLS 迁移
- 使用 GitHub 操作自动执行安全审核

## 先决条件

开始之前，请确保您拥有：

- [Supabase 帐户](<https://supabase.com>) 具有活动项目
- Node.js 18+本地安装
- [Continue CLI](<https://docs.continue.dev/guides/cli>) 具有 ** 有效积分 **（使用 API 时需要）
- 对SQL和数据库概念有基本的了解

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

Continue CLI 可以分析您的数据库模式并生成复杂的 SQL 查询 - 您只需用简单的语言描述您想要的内容！

## 步骤 1：设置您的凭据

首先，您需要设置对 Supabase 项目的访问权限。

#### 配置 Supabase MCP

**注意：**

**安全第一**：使用MCP时遵循[Supabase的安全最佳实践](<https://supabase.com/docs/guides/getting-started/mcp#security-risks>)：
- 切勿直接连接到生产数据库
- 使用开发或暂存环境
- 尽可能启用只读模式
- 范围MCP访问特定项目

Supabase MCP 支持 OAuth 身份验证以实现安全访问：

**OAuth配置（推荐）**

Supabase MCP 首次使用时会提示进行 OAuth 身份验证。只需遵循授权流程即可。

**远程 MCP URL**
```
https://mcp.supabase.com/mcp
```

**说明：**

MCP 服务器可以限定为特定项目以获得更好的安全性。在设置过程中配置此项。

#### Supabase 项目设置

要有效使用 Supabase MCP，请确保您的项目具有：

1. **开发环境** - 创建单独的开发项目：
   - Go 至 [Supabase 仪表板 ](<https://supabase.com/dashboard>)
   - 为development/testing创建一个新项目
   - 复制项目URL和匿名密钥

2. **数据库架构** - 设置一些初始表进行测试：
```sql
-- Example schema for testing
CREATE TABLE users (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE posts (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  title TEXT NOT NULL,
  content TEXT,
  published BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

3. **行级安全性 (RLS)** - 启用 RLS 以确保安全：
```sql
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
```

**提示：**

使用 [Supabase 分支 ](<https://supabase.com/docs/guides/deployment/branching>) 创建隔离的开发分支以进行安全测试。

## Supabase 数据库工作流程选项

#### 最快的成功之路

跳过手动设置并使用我们预构建的 Supabase Continuous AI agent，其中包括 
 优化的提示、规则和用于智能数据库管理的 Supabase MCP。

**说明：**

**Supabase MCP 的工作原理**：
- 通过 OAuth 连接到您的 Supabase 项目
- 提供用于数据库查询、模式检查和迁移的工具
- 支持只读模式，操作更安全
- 可以将范围限制在特定项目的安全性上

#### ⚡ 快速入门（推荐）

**完美适用于：**通过AI-支持的查询优化和模式洞察进行即时数据库分析

#### 添加预建的 Agent

运行：

```bash
cn --agent continuedev/supabase-agent
```

此 agent 包括：
- **优化数据库分析和查询生成的提示**
- **针对 SQL 最佳实践和安全性的内置规则 **
- **[Supabase MCP](<https://supabase.com/docs/guides/getting-started/mcp>)** 用于安全数据库访问
- **通过 OAuth 流程自动身份验证**

#### 运行数据库分析

导航到您的项目目录并在 Continue CLI TUI 中输入此提示：

```
Analyze my Supabase database schema and suggest performance optimizations
```

就是这样！ agent 自动处理一切。

**说明：**

**为什么使用Agent？**通过预先测试的提示和内置的SQL优化规则获得一致的结果。

#### 🛠️手动设置

#### 将 Supabase MCP 添加到 Continue CLI

使用 OAuth 配置 [Supabase MCP](<https://supabase.com/docs/guides/getting-started/mcp>)：

MCP服务器在您首次使用时会自动提示进行OAuth认证。

#### 验证 Supabase 连接

使用此提示测试您的 Supabase MCP 连接：

```
List all tables in my Supabase database
```

#### 创建自定义数据库分析提示

使用此提示模板与 Continue CLI 来分析您的数据库：

```
Analyze my Supabase database:
- List all tables with row counts
- Identify tables without indexes
- Find potential N+1 query patterns
- Suggest missing foreign key constraints
- Recommend index optimizations based on table structure
- Generate SQL migrations for suggested improvements
- Check for security issues (missing RLS policies)
```

#### Agent 要求

要使用预构建的 agent，您需要：
- **Continue CLI 专业计划 ** 以及模型附加组件，OR
- **您自己的API密钥**配置为环境变量

agent将自动检测并使用您的配置。对于 Supabase MCP：
- **Supabase 帐户** 至少有一个项目
- **OAuth身份验证**（自动处理）
- 开发或暂存环境（非生产环境）

---

**注意：**

**安全最佳实践**：
- **切勿将 MCP 与生产数据库一起使用 ** - 始终使用开发或登台环境
- **分析数据时启用只读模式**以防止意外修改
- **范围到特定项目**限制访问
- **使用分支数据库**测试架构更改

## 步骤 2：使用 AI 分析您的数据库

使用【Continue】【CLI】进行数据库智能分析。在 Continue CLI TUI 中输入以下提示：

#### 模式分析

**提示：**
```
Analyze my Supabase database schema and provide:
- Complete table structure with data types
- Relationships between tables
- Missing indexes that could improve performance
- Unused or redundant columns
- Suggestions for normalization improvements
```

#### RLS安全审计

**提示：**
```
Perform a comprehensive RLS (Row Level Security) audit on my Supabase database:

For each table:
- Check if RLS is enabled
- List all existing RLS policies
- Identify tables without any RLS policies (security risk)
- Find overly permissive policies (e.g., allowing all operations)
- Suggest missing policies based on common patterns
- Generate SQL to fix identified security issues

Prioritize findings by risk level:
1. Critical: Tables with sensitive data but no RLS
2. High: Overly permissive policies
3. Medium: Missing common policies (e.g., users can only see own data)
4. Low: Policy optimization opportunities
```

#### 修复 RLS 政策

**提示：**
```
Based on the RLS audit, generate SQL migrations to fix all security issues:

1. Enable RLS on all tables that don't have it:
   - Include ALTER TABLE statements
   - Add comment explaining why RLS is needed

2. Create secure default policies for common patterns:
   - Users can only read/update their own records
   - Admins have full access (with role checking)
   - Public read-only access where appropriate
   - Proper INSERT policies for new records

3. Fix overly permissive policies:
   - Replace 'true' conditions with proper checks
   - Add user_id or role-based restrictions
   - Include USING clauses for read operations
   - Include WITH CHECK clauses for write operations

4. For each policy, include:
   - Clear naming convention (e.g., 'users_select_own')
   - Comments explaining the security model
   - Rollback statements

Generate the complete migration file with all fixes.
```

**说明：**

**可用的 Supabase MCP 功能 **：
- **数据库查询**：执行SQL查询并分析结果
- **架构检查**：查看表结构、关系和约束
- **性能分析**：识别慢速查询和优化机会
- **安全审查**：检查RLS策略和访问控制
- **迁移生成**：为架构更改创建SQL迁移

## 步骤 3：生成数据库迁移

根据 AI 建议创建并应用数据库迁移。在Continue CLI TUI中输入此提示：

**示例：完成RLS安全修复**

```
I need to secure my Supabase database. Please:

1. First, audit all tables for RLS security issues
2. Generate a complete migration to fix all issues found
3. Create a security report I can share with my team

Here's what I need fixed:
- Enable RLS on all tables
- Create policies so users can only access their own data
- Ensure admins (role = 'admin') have full access
- Add policies for service accounts (role = 'service')
- Include audit logging for sensitive operations

For the users table specifically:
- Users can read their own profile
- Users can update their own profile (except role field)
- Only admins can view all users
- Only admins can delete users
- New users can insert their own record during signup

Generate the complete migration with:
- All SQL statements
- Clear policy names and comments
- Rollback statements
- A summary of what each policy does
```

**预期输出：**
AI将生成一个完整的SQL迁移文件：
- 在所有表上启用 RLS
- 创建安全、记录齐全的政策
- 遵循 Supabase 安全最佳实践
- 包括回滚功能
- 为您的团队提供安全摘要

**提示：**

**最佳实践**：在应用AI-生成的迁移之前始终检查它们。首先使用 Supabase 的分支功能在开发分支中进行测试。

## 步骤 4：设置自动数据库监控

使用 Continue CLI 和 GitHub 操作自动执行数据库运行状况检查：

```yaml
name: Database Health Monitor

on:
  schedule:
    # Run daily at 2 AM UTC
    - cron: "0 2 * * *"
  workflow_dispatch: # Allow manual triggers

jobs:
  monitor-database:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: "22"

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Analyze Database Security
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
        run: |
          echo "🔍 Performing security audit..."
          
          # Use Continue CLI to audit RLS and generate fixes
          cn -p "Using Supabase MCP, perform a comprehensive RLS security audit:
                 1. Check all tables for RLS enablement
                 2. Identify tables with missing or weak RLS policies
                 3. Find overly permissive policies (e.g., 'true' conditions)
                 4. Check for common security anti-patterns:
                    - Missing user_id checks
                    - No role-based access control
                    - Unrestricted DELETE operations
                    - Missing WITH CHECK clauses
                 5. Generate fixes for all security issues found
                 6. Create a security report with:
                    - Critical vulnerabilities (tables without RLS)
                    - High-risk policies that need immediate fixes
                    - SQL migrations to fix all issues
                    - Best practice recommendations
                    - Compliance checklist (GDPR, SOC2, etc.)
                 
                 If critical security issues are found:
                 - Generate the complete fix migration
                 - Create a GitHub issue with severity labels
                 - Tag security team members for review"

      - name: Save Health Report
        run: |
          echo "## 📊 Database Health Report" >> $GITHUB_STEP_SUMMARY
          echo "Generated at $(date)" >> $GITHUB_STEP_SUMMARY
          echo "" >> $GITHUB_STEP_SUMMARY
          echo "Check the 'Analyze Database Security' step for the full report" >> $GITHUB_STEP_SUMMARY

      - name: Upload Health Report
        uses: actions/upload-artifact@v4
        with:
          name: database-health-report
          path: |
            *.md
            *.sql
```

**注意：**

**必需的GitHub秘密**：
- `CONTINUE_API_KEY`：来自 [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>) 的 Continue API 密钥

将其添加到：**存储库设置→秘密和变量→操作**

## 你建造了什么

完成本指南后，您将拥有一个完整的 **AI- 支持的数据库管理系统 **，该系统：

- **分析架构设计** - 确定优化机会和最佳实践
- **优化查询** - 建议索引和查询重写以获得更好的性能
- **生成迁移** - 创建SQL代码以进行架构改进
- **监控运行状况** - 运行性能和安全问题的自动检查
- **提供见解** - 根据数据模式提供可行的建议

#### 连续AI数据库管理

您的系统现在在 **[ 级别 2 连续 AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** - AI 运行，在人工监督下处理常规数据库分析和优化，以进行迁移审批。

## 高级数据库提示

使用这些高级 Continue CLI 提示增强您的工作流程：

#### 实时表现

实时监控查询性能并获取超过阈值时间的慢查询警报

#### 数据质量检查

自动验证数据完整性、查找重复项并确保相关表之间的一致性

#### 访问模式分析

分析 API 日志以了解数据访问模式并相应地优化索引

#### 成本优化

检查数据库使用情况并提出在保持性能的同时降低成本的方法

## 安全最佳实践

**注意：**

**数据库安全指南**：
- 始终将 development/staging 环境与 MCP 一起使用
- 为分析任务启用只读模式
- 切勿将生产凭据存储在代码中
- 执行前查看所有AI-生成的SQL
- 使用 RLS 策略强制执行访问控制
- 定期审核数据库权限

## 故障排除

### Supabase MCP 连接问题

如果您遇到连接问题：

1. 验证 OAuth 身份验证是否完成
2. 检查您的 Supabase 项目是否可访问
3. 确保您没有连接到生产数据库
4. 验证 MCP URL 是否正确：`https://mcp.supabase.com/mcp`

### 常见的数据库分析问题

|问题|解决方案|
|:------|:---------|
|没有找到桌子|验证您的数据库是否已创建表|
|权限被拒绝|检查 OAuth 范围和项目权限|
|慢查询分析|确保您的数据库已启用查询日志|
|迁移失败|首先在分支数据库中测试迁移|

## 下一步

- 设置 [Supabase Edge Functions](<https://supabase.com/docs/guides/functions>) 以实现自动化工作流程
- 配置 [Supabase Realtime](<https://supabase.com/docs/guides/realtime>) 进行实时数据监控
- 实施 [数据库 Webhooks](<https://supabase.com/docs/guides/database/webhooks>) 以实现事件驱动的自动化

## 资源

- [Supabase MCP 文档](<https://supabase.com/docs/guides/getting-started/mcp>)
- [Supabase数据库指南](<https://supabase.com/docs/guides/database>)
- [Supabase 安全最佳实践](<https://supabase.com/docs/guides/database/security>)
- [Model Context Protocol 文档](<https://modelcontextprotocol.io/introduction>)
- [Continue CLI 指南](<https://docs.continue.dev/guides/cli>)
- [连续 AI 最佳实践](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)


## 原文对照

<details>
<summary>展开完整原文</summary>

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### What You'll Build

A security audit workflow that uses Continue CLI with Supabase MCP to identify RLS vulnerabilities, generate secure policies, fix permission issues, and ensure your database follows security best practices.

## What You'll Learn

This cookbook teaches you to:

- Use [Supabase MCP](<https://supabase.com/docs/guides/getting-started/mcp>) to audit database security
- Identify tables without Row Level Security (RLS) enabled
- Find and fix overly permissive or missing RLS policies
- Generate secure RLS migrations following best practices
- Automate security audits with GitHub Actions

## Prerequisites

Before starting, ensure you have:

- [Supabase account](<https://supabase.com>) with an active project
- Node.js 18+ installed locally
- [Continue CLI](<https://docs.continue.dev/guides/cli>) with **active credits** (required for API usage)
- Basic understanding of SQL and database concepts

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

Continue CLI can analyze your database schema and generate complex SQL queries - you just need to describe what you want in plain language!

## Step 1: Set Up Your Credentials

First, you'll need to set up access to your Supabase project.

#### Configure Supabase MCP

**注意：**

**Security First**: Follow [Supabase's security best practices](<https://supabase.com/docs/guides/getting-started/mcp#security-risks>) when using MCP:
- Never connect to production databases directly
- Use development or staging environments
- Enable read-only mode when possible
- Scope MCP access to specific projects

The Supabase MCP supports OAuth authentication for secure access:

**OAuth Configuration (Recommended)**

The Supabase MCP will prompt for OAuth authentication when first used. Simply follow the authorization flow.

**Remote MCP URL**
```
https://mcp.supabase.com/mcp
```

**说明：**

The MCP server can be scoped to a specific project for better security. Configure this during setup.

#### Supabase Project Setup

To use Supabase MCP effectively, ensure your project has:

1. **Development Environment** - Create a separate development project:
   - Go to [Supabase Dashboard](<https://supabase.com/dashboard>)
   - Create a new project for development/testing
   - Copy the project URL and anon key

2. **Database Schema** - Set up some initial tables for testing:
```sql
-- Example schema for testing
CREATE TABLE users (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE posts (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  title TEXT NOT NULL,
  content TEXT,
  published BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

3. **Row Level Security (RLS)** - Enable RLS for security:
```sql
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
```

**提示：**

Use [Supabase Branching](<https://supabase.com/docs/guides/deployment/branching>) to create isolated development branches for safe testing.

## Supabase Database Workflow Options

#### Fastest Path to Success

Skip the manual setup and use our pre-built Supabase Continuous AI agent that includes
optimized prompts, rules, and the Supabase MCP for intelligent database management.

**说明：**

**How Supabase MCP Works**:
- Connects to your Supabase project via OAuth
- Provides tools for database queries, schema inspection, and migrations
- Supports read-only mode for safer operations
- Can be scoped to specific projects for security

#### ⚡ Quick Start (Recommended)

**Perfect for:** Immediate database analysis with AI-powered query optimization and schema insights

#### Add the Pre-Built Agent

Run:

```bash
cn --agent continuedev/supabase-agent
```

This agent includes:
- **Optimized prompts** for database analysis and query generation
- **Built-in rules** for SQL best practices and security
- **[Supabase MCP](<https://supabase.com/docs/guides/getting-started/mcp>)** for secure database access
- **Automatic authentication** via OAuth flow

#### Run Database Analysis

Navigate to your project directory and enter this prompt in the Continue CLI TUI:

```
Analyze my Supabase database schema and suggest performance optimizations
```

That's it! The agent handles everything automatically.

**说明：**

**Why Use the Agent?** Get consistent results with pre-tested prompts and built-in SQL optimization rules.

#### 🛠️ Manual Setup

#### Add Supabase MCP to Continue CLI

Configure the [Supabase MCP](<https://supabase.com/docs/guides/getting-started/mcp>) using OAuth:

The MCP server will automatically prompt for OAuth authentication when you first use it.

#### Verify Supabase Connection

Test your Supabase MCP connection with this prompt:

```
List all tables in my Supabase database
```

#### Create Custom Database Analysis Prompts

Use this prompt template with Continue CLI to analyze your database:

```
Analyze my Supabase database:
- List all tables with row counts
- Identify tables without indexes
- Find potential N+1 query patterns
- Suggest missing foreign key constraints
- Recommend index optimizations based on table structure
- Generate SQL migrations for suggested improvements
- Check for security issues (missing RLS policies)
```

#### Agent Requirements

To use the pre-built agent, you need either:
- **Continue CLI Pro Plan** with the models add-on, OR
- **Your own API keys** configured as environment variables

The agent will automatically detect and use your configuration. For Supabase MCP:
- **Supabase account** with at least one project
- **OAuth authentication** (handled automatically)
- Development or staging environment (not production)

---

**注意：**

**Security Best Practices**: 
- **Never use MCP with production databases** - Always use development or staging environments
- **Enable read-only mode** when analyzing data to prevent accidental modifications
- **Scope to specific projects** to limit access
- **Use branch databases** for testing schema changes

## Step 2: Analyze Your Database with AI

Use Continue CLI to perform intelligent database analysis. Enter these prompts in the Continue CLI TUI:

#### Schema Analysis

**Prompt:**
```
Analyze my Supabase database schema and provide:
- Complete table structure with data types
- Relationships between tables
- Missing indexes that could improve performance
- Unused or redundant columns
- Suggestions for normalization improvements
```

#### RLS Security Audit

**Prompt:**
```
Perform a comprehensive RLS (Row Level Security) audit on my Supabase database:

For each table:
- Check if RLS is enabled
- List all existing RLS policies
- Identify tables without any RLS policies (security risk)
- Find overly permissive policies (e.g., allowing all operations)
- Suggest missing policies based on common patterns
- Generate SQL to fix identified security issues

Prioritize findings by risk level:
1. Critical: Tables with sensitive data but no RLS
2. High: Overly permissive policies
3. Medium: Missing common policies (e.g., users can only see own data)
4. Low: Policy optimization opportunities
```

#### Fix RLS Policies

**Prompt:**
```
Based on the RLS audit, generate SQL migrations to fix all security issues:

1. Enable RLS on all tables that don't have it:
   - Include ALTER TABLE statements
   - Add comment explaining why RLS is needed

2. Create secure default policies for common patterns:
   - Users can only read/update their own records
   - Admins have full access (with role checking)
   - Public read-only access where appropriate
   - Proper INSERT policies for new records

3. Fix overly permissive policies:
   - Replace 'true' conditions with proper checks
   - Add user_id or role-based restrictions
   - Include USING clauses for read operations
   - Include WITH CHECK clauses for write operations

4. For each policy, include:
   - Clear naming convention (e.g., 'users_select_own')
   - Comments explaining the security model
   - Rollback statements

Generate the complete migration file with all fixes.
```

**说明：**

**Available Supabase MCP Capabilities**:
- **Database Queries**: Execute SQL queries and analyze results
- **Schema Inspection**: View table structures, relationships, and constraints
- **Performance Analysis**: Identify slow queries and optimization opportunities
- **Security Review**: Check RLS policies and access controls
- **Migration Generation**: Create SQL migrations for schema changes

## Step 3: Generate Database Migrations

Create and apply database migrations based on AI recommendations. Enter this prompt in the Continue CLI TUI:

**Example: Complete RLS Security Fix**

```
I need to secure my Supabase database. Please:

1. First, audit all tables for RLS security issues
2. Generate a complete migration to fix all issues found
3. Create a security report I can share with my team

Here's what I need fixed:
- Enable RLS on all tables
- Create policies so users can only access their own data
- Ensure admins (role = 'admin') have full access
- Add policies for service accounts (role = 'service')
- Include audit logging for sensitive operations

For the users table specifically:
- Users can read their own profile
- Users can update their own profile (except role field)
- Only admins can view all users
- Only admins can delete users
- New users can insert their own record during signup

Generate the complete migration with:
- All SQL statements
- Clear policy names and comments
- Rollback statements
- A summary of what each policy does
```

**Expected Output:**
The AI will generate a complete SQL migration file that:
- Enables RLS on all tables
- Creates secure, well-documented policies
- Follows Supabase security best practices
- Includes rollback capabilities
- Provides a security summary for your team

**提示：**

**Best Practice**: Always review AI-generated migrations before applying them. Test in a development branch first using Supabase's branching feature.

## Step 4: Set Up Automated Database Monitoring

Automate database health checks with Continue CLI and GitHub Actions:

```yaml
name: Database Health Monitor

on:
  schedule:
    # Run daily at 2 AM UTC
    - cron: "0 2 * * *"
  workflow_dispatch: # Allow manual triggers

jobs:
  monitor-database:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: "22"

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Analyze Database Security
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
        run: |
          echo "🔍 Performing security audit..."
          
          # Use Continue CLI to audit RLS and generate fixes
          cn -p "Using Supabase MCP, perform a comprehensive RLS security audit:
                 1. Check all tables for RLS enablement
                 2. Identify tables with missing or weak RLS policies
                 3. Find overly permissive policies (e.g., 'true' conditions)
                 4. Check for common security anti-patterns:
                    - Missing user_id checks
                    - No role-based access control
                    - Unrestricted DELETE operations
                    - Missing WITH CHECK clauses
                 5. Generate fixes for all security issues found
                 6. Create a security report with:
                    - Critical vulnerabilities (tables without RLS)
                    - High-risk policies that need immediate fixes
                    - SQL migrations to fix all issues
                    - Best practice recommendations
                    - Compliance checklist (GDPR, SOC2, etc.)
                 
                 If critical security issues are found:
                 - Generate the complete fix migration
                 - Create a GitHub issue with severity labels
                 - Tag security team members for review"

      - name: Save Health Report
        run: |
          echo "## 📊 Database Health Report" >> $GITHUB_STEP_SUMMARY
          echo "Generated at $(date)" >> $GITHUB_STEP_SUMMARY
          echo "" >> $GITHUB_STEP_SUMMARY
          echo "Check the 'Analyze Database Security' step for the full report" >> $GITHUB_STEP_SUMMARY

      - name: Upload Health Report
        uses: actions/upload-artifact@v4
        with:
          name: database-health-report
          path: |
            *.md
            *.sql
```

**注意：**

**Required GitHub Secrets**:
- `CONTINUE_API_KEY`: Your Continue API key from [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>)

Add this at: **Repository Settings → Secrets and variables → Actions**

## What You've Built

After completing this guide, you have a complete **AI-powered database management system** that:

- **Analyzes schema design** - Identifies optimization opportunities and best practices
- **Optimizes queries** - Suggests indexes and query rewrites for better performance
- **Generates migrations** - Creates SQL code for schema improvements
- **Monitors health** - Runs automated checks for performance and security issues
- **Provides insights** - Delivers actionable recommendations based on data patterns

#### Continuous AI Database Management

Your system now operates at **[Level 2 Continuous AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** - AI handles routine database analysis and optimization with human oversight for migration approval.

## Advanced Database Prompts

Enhance your workflow with these advanced Continue CLI prompts:

#### Real-time Performance

Monitor query performance in real-time and get alerts for slow queries exceeding threshold times

#### Data Quality Checks

Automatically validate data integrity, find duplicates, and ensure consistency across related tables

#### Access Pattern Analysis

Analyze API logs to understand data access patterns and optimize indexes accordingly

#### Cost Optimization

Review database usage and suggest ways to reduce costs while maintaining performance

## Security Best Practices

**注意：**

**Database Security Guidelines**:
- Always use development/staging environments with MCP
- Enable read-only mode for analysis tasks
- Never store production credentials in code
- Review all AI-generated SQL before execution
- Use RLS policies to enforce access control
- Regularly audit database permissions

## Troubleshooting

### Supabase MCP Connection Issues

If you encounter connection issues:

1. Verify OAuth authentication is complete
2. Check your Supabase project is accessible
3. Ensure you're not connecting to a production database
4. Verify the MCP URL is correct: `https://mcp.supabase.com/mcp`

### Common Database Analysis Issues

| Issue | Solution |
|:------|:---------|
| No tables found | Verify your database has tables created |
| Permission denied | Check OAuth scopes and project permissions |
| Slow query analysis | Ensure your database has query logs enabled |
| Migration failures | Test migrations in a branch database first |

## Next Steps

- Set up [Supabase Edge Functions](<https://supabase.com/docs/guides/functions>) for automated workflows
- Configure [Supabase Realtime](<https://supabase.com/docs/guides/realtime>) for live data monitoring
- Implement [Database Webhooks](<https://supabase.com/docs/guides/database/webhooks>) for event-driven automation

## Resources

- [Supabase MCP Documentation](<https://supabase.com/docs/guides/getting-started/mcp>)
- [Supabase Database Guide](<https://supabase.com/docs/guides/database>)
- [Supabase Security Best Practices](<https://supabase.com/docs/guides/database/security>)
- [Model Context Protocol Docs](<https://modelcontextprotocol.io/introduction>)
- [Continue CLI Guide](<https://docs.continue.dev/guides/cli>)
- [Continuous AI Best Practices](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)


</details>
