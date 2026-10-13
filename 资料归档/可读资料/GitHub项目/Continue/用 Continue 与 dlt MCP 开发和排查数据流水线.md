# 用 Continue 与 dlt MCP 开发和排查数据流水线

有条件工程教程。展示流水线执行检查、Schema 管理、数据探索、加载故障定位、创建和演进，以及 GitHub Actions 自动运行；适用于数据工程开发。

- 来源：Continue
- 资料类型：有条件工程教程
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/bd36638ee0cf9fb90c314647bab2f1c6897aa6fe>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：需要 dlt 或 dlt+ 及实际数据目标，案例不等同于通用编码流程。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/dlt-mcp-continue-cookbook.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/用 Continue 与 dlt MCP 开发和排查数据流水线.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Building Data Pipelines with dlt MCP and Continue"
description: "Set up an AI-powered data engineering workflow that helps you develop, debug, and inspect dlt data pipelines using natural language commands."
sidebarTitle: "dlt Data Pipelines with Continue"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### 你将构建什么

一个由 AI- 驱动的数据管道开发系统，使用 Continue 的 AI agent 和 dlt
MCP 来检查管道执行、检索模式、分析数据集和调试加载错误 - 所有这些都通过简单的自然语言提示进行

## 先决条件

开始之前，请确保您拥有：

- Python 3.8+本地安装
- dlt 管道项目（或在本指南中创建一个）
- 对数据管道的基本了解

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

#### 安装dlt

```bash
pip install dlt
```

**注意：**

要在无头模式下使用agents，您需要[Continue API密钥](<https://continue.dev/settings/api-keys>)。

## dlt MCP 工作流程选项

#### 🚀 最快的成功之路

跳过手动设置并使用我们预构建的 [dlt Agent](<https://continue.dev/continuedev/dlt-agent>)，其中包括 
 dlt MCP 和优化的数据管道工作流程，以获得更一致的结果。您可以根据您的特定需求对其进行自定义。

确保满足上述 **先决条件 ** 后，您有两条途径可以开始：

#### ⚡ 快速入门（推荐）

#### 加载预建的Agent

导航到您的管道项目目录并运行：
```bash
cn --agent continuedev/dlt-agent
```

此 agent 包括：
- **dlt MCP** 已预先配置并可供使用
- **针对数据工程最佳实践的以管道为中心的规则**

#### 运行您的第一次管道检查

从全面的管道检查开始：
```bash
# TUI mode
Inspect the execution of my dlt pipeline and summarize the load info, including timing and file sizes.
```

就是这样！ agent 自动处理一切。

**说明：**

**为什么使用 Agent？** 预构建的 [dlt Agent](<https://continue.dev/continuedev/dlt-agent>) 提供一致的管道开发工作流程并自动处理 MCP 配置，从而更轻松地开始使用 AI- 支持的数据工程。您可以稍后自定义此 agent 以适合您团队的特定工作流程。

#### 🛠️手动设置

#### 将 dlt MCP 添加到 Continue CLI

将 dlt MCP 添加到您的配置中：

**提示：**

**安装方法：**
1. **快速CLI安装**：`cn --mcp dlthub/dlt-mcp`
2. **手动配置**：将MCP添加到`mcpServers`部分下的`~/.continue/config.json`

安装后，dlt MCP 工具即可用于您的 Continue agent 的所有提示。

**说明：**

MCP 将与当前目录中现有的 dlt 管道一起使用。

#### 运行您的第一次管道检查

从全面的管道检查开始：
```bash
# TUI mode
cn
# Then type: Inspect the execution of my dlt pipeline and summarize the load info, including timing and file sizes.
```

#### Agent 要求

要使用预构建的 [dlt Agent](<https://continue.dev/continuedev/dlt-agent>)，您需要：
    - **Continue CLI 专业计划 ** 以及模型附加组件，OR
    - **您自己的API密钥**配置为环境变量

agent 将自动检测并使用您的配置以及预配置的 dlt MCP 进行管道操作。

---

## dlt MCP 与 dlt+ MCP

#### 了解差异

**dlt MCP** 专注于本地管道开发和检查。它提供的工具可以：
- 检查管道执行和负载信息
- 从本地管道检索架构元数据
- 从目标数据库查询数据集记录
- 分析加载错误、时间和文件大小

**[dlt+ MCP](<https://continue.dev/dlthub/dlt-plus-mcp>)** 通过基于云的生产部署功能扩展了这些功能：
- 连接到 dlt+ 项目并管理部署
- 监控跨多个环境运行的管道
- 访问集中式日志记录和可观察性
- 与团队成员合作开发管道

对于本地开发和入门，**[dlt MCP](<https://continue.dev/dlthub/dlt-mcp>)** 是正确的选择。当您需要生产部署功能和团队协作时，请考虑 **[dlt+ MCP](<https://continue.dev/dlthub/dlt-plus-mcp>)**。

---

## 管道开发秘诀

现在您可以使用自然语言提示来开发和调试您的 dlt 管道。 Continue agent 自动调用适当的 dlt MCP 工具。

**说明：**

**在哪里运行这些工作流程：**
- **IDE 扩展 **：在 VS Code、JetBrains 或其他支持的 IDE 中使用 Continue
- **终端（TUI模式）**：运行`cn`进入交互模式，然后输入提示
- **CLI（无头模式）**：使用 `cn -p "your prompt"` 进行无头命令

**首先在计划模式下进行测试**：在运行可能会进行
更改的管道操作之前，请在计划模式下测试提示（请参阅[计划模式
指南](<https://github.com/guides/plan-mode-guide>)；按**Shift+Tab**切换TUI/IDE中的模式）。这个
向您展示了agent在不执行它的情况下会做什么。

要在无头模式下运行下面的任何示例提示，请使用 `cn -p "prompt"`

**说明：**

**关于--auto标志**：`--auto`标志使工具能够连续运行，无需手动确认。这对于无头模式至关重要，其中 agent 需要自动执行多个工具来完成管道检查、模式检索和错误分析等任务。

### 管道检测

#### 检查管道执行情况

查看管道执行详细信息，包括加载时间和文件大小。

**提示：**
```
Inspect my dlt pipeline execution and provide a summary of the load info.
Show me the timing breakdown and file sizes for each table.
```

### 模式管理

#### 检索架构元数据

获取管道表的详细架构信息。

**提示：**
```
Show me the schema for my users table including all columns,
data types, and any constraints.
```

### 数据探索

#### 查询数据集记录

从目标数据库检索并分析记录。

**提示：**
```
Get the last 10 records from my orders table and show me
the distribution of order statuses.
```

### 错误调试

#### 分析负载错误

调查并了解管道负载错误。

**提示：**
```
Check for any load errors in my last pipeline run. If there are errors,
explain what went wrong and suggest fixes.
```

### 管道创建

#### 建设新管道

从 API 或数据源创建新的 dlt 管道。

**提示：**
```
Help me create a new dlt pipeline that loads data from the
JSONPlaceholder API users endpoint into DuckDB.
```

### 模式演化

#### 处理架构更改

检查和管理管道中的架构演变。

**提示：**
```
Check if my pipeline schema has evolved since the last run.
Show me what columns were added or modified.
```

## 具有 GitHub 操作的连续数据管道

此示例演示了一个 **连续 AI 工作流程 **，其中数据管道验证使用 [dlt Assistant agent](<https://continue.dev/dlthub/dlt-assistant>) 在无头模式下在 CI/CD 管道中自动运行。考虑自定义此 agent 以添加您组织的特定验证规则。

### 添加 GitHub 秘密

导航到 ** 存储库设置 → 秘密和变量 → 操作 ** 并添加：

- `CONTINUE_API_KEY`：来自 [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>) 的 Continue API 密钥
- 您的目的地所需的任何数据库凭据

**说明：**

该工作流程使用预构建的 [dlt Agent](<https://continue.dev/continuedev/dlt-agent>) 和 `--agent continuedev/dlt-agent`。此 agent 预先配置了 dlt MCP 和针对管道操作的优化规则。您可以根据您的特定管道要求自定义验证规则和提示。

### 创建工作流程文件

此工作流程使用 [ 无头模式 ](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue CLI 非交互运行与流水线调用.md>) 中的 Continue CLI 自动验证拉取请求上的 dlt 数据管道。它检查管道模式，检查错误，并以 PR 评论的形式发布摘要报告。工作流程也可以通过`workflow_dispatch`手动触发。

在您的存储库中创建 `.github/workflows/dlt-pipeline-validation.yml`：

```yaml
name: Data Pipeline Validation with dlt MCP

on:
  pull_request:
    branches: [main]
  workflow_dispatch:

jobs:
  validate-pipeline:
    runs-on: ubuntu-latest
    env:
      CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "18"

      - name: Install dlt
        run: |
          pip install dlt
          echo "✅ dlt installed"

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Validate Pipeline Schema
        run: |
          echo "🔍 Validating pipeline schema..."
          cn --agent continuedev/dlt-agent \
             -p "Inspect the pipeline schema and verify all required tables
                 and columns are present. Flag any missing or unexpected changes." \
             --auto

      - name: Check Pipeline Health
        run: |
          echo "📊 Checking pipeline health..."
          cn --agent continuedev/dlt-agent \
             -p "Analyze the last pipeline run for errors or warnings.
                 Report any issues that need attention." \
             --auto

      - name: Comment Pipeline Report on PR
        if: always() && github.event_name == 'pull_request'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          REPORT=$(cn --agent continuedev/dlt-agent \
             -p "Generate a concise summary (200 words or less) of:
                 - Pipeline schemas and row counts
                 - Any load errors or warnings
                 - Performance metrics (timing, file sizes)
                 - Recommended improvements" \
             --auto)

          gh pr comment ${{ github.event.pull_request.number }} --body "$REPORT"
```

**说明：**

dlt MCP 适用于您的本地管道状态。确保您的 CI 环境 
 有权访问必要的管道配置和凭据。

## 管道开发最佳实践

使用 Continue 的规则系统实施自动化管道质量检查。请参阅 [规则深入探讨 ](<https://docs.continue.dev/customize/deep-dives/rules>) 了解创作技巧。

#### 模式验证

```bash
"Before committing pipeline changes, verify the schema
matches expectations and flag any unexpected modifications."
```

#### 错误处理

```bash
"When load errors occur, analyze the error details and
suggest specific code fixes to handle the data issues."
```

#### 性能监控

```bash
"Track pipeline execution times and file sizes. Alert if
performance degrades significantly from baseline."
```

#### 数据质量

```bash
"After each pipeline run, validate row counts and check for
null values in critical columns."
```

## 故障排除

### 未找到管道

```bash
"Check if there's a dlt pipeline in the current directory.
If not, help me initialize a new pipeline."
```

### 目的地连接问题

```bash
"Verify the destination connection and credentials for my pipeline.
Test the connection and report any issues."
```

### 模式推理问题

**检查：**

**验证步骤：**
- dlt MCP 已安装并配置
- 管道目录可访问
- 目标数据库凭据已配置
- 管道至少运行过一次

## 你建造了什么

完成本指南后，您将拥有一个完整的 **AI- 支持的数据管道开发系统 **，该系统：

✅ 使用自然语言 — 简单的提示而不是复杂的管道命令
 ✅ 自动调试 — AI 分析错误并建议修复
 ✅ 连续运行 — CI/CD 管道中的自动验证
 ✅ 确保质量 — 管道检查可防止传输不良数据

#### 连续AI

您的数据管道工作流程现在在 **[ 级别 2 连续 
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI 运行，通过审核和批准变更，在人工监督下 
 处理常规管道检查和调试。

## 下一步

1. **检查您的第一个管道** - 在当前项目上尝试管道检查提示
2. **调试加载错误** - 使用错误分析提示修复任何问题
3. **设置 CI 管道** - 将 GitHub 操作工作流程添加到您的存储库
4. **创建新管道** - 使用AI搭建新数据源
5. **监控性能** - 随着时间的推移跟踪管道执行指标

## 其他资源

#### [dlt 文档](<https://dlthub.com/docs>)

完整的 dlt 平台文档

#### [Continue 文档](<https://docs.continue.dev>)

探索 Continue 文档和指南

#### [dlt 博客：MCP 深入探讨 ](<https://dlthub.com/blog/deep-dive-assistants-mcp-continue>)

了解 AI、agents、MCP 和 Continue 集成

#### [MCP 概念](<https://dlthub.com/blog/deep-dive-assistants-mcp-continue>)

深入探讨 dlt MCP 集成


## 原文对照

<details>
<summary>展开完整原文</summary>

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### What You'll Build

An AI-powered data pipeline development system that uses Continue's AI agent with dlt
MCP to inspect pipeline execution, retrieve schemas, analyze datasets, and debug load errors - all through simple natural language prompts

## Prerequisites

Before starting, ensure you have:

- Python 3.8+ installed locally
- A dlt pipeline project (or create one during this guide)
- Basic understanding of data pipelines

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

#### Install dlt

```bash
pip install dlt
```

**注意：**

To use agents in headless mode, you need a [Continue API key](<https://continue.dev/settings/api-keys>).

## dlt MCP Workflow Options

#### 🚀 Fastest Path to Success

Skip the manual setup and use our pre-built [dlt Agent](<https://continue.dev/continuedev/dlt-agent>) that includes
    the dlt MCP and optimized data pipeline workflows for more consistent results. You can customize it for your specific needs.

After ensuring you meet the **Prerequisites** above, you have two paths to get started:

#### ⚡ Quick Start (Recommended)

#### Load the Pre-Built Agent

Navigate to your pipeline project directory and run:
```bash
cn --agent continuedev/dlt-agent
```

This agent includes:
- **dlt MCP** pre-configured and ready to use
- **Pipeline-focused rules** for data engineering best practices

#### Run Your First Pipeline Inspection

Start with a comprehensive pipeline check:
```bash
# TUI mode
Inspect the execution of my dlt pipeline and summarize the load info, including timing and file sizes.
```

That's it! The agent handles everything automatically.

**说明：**

**Why Use the Agent?** The pre-built [dlt Agent](<https://continue.dev/continuedev/dlt-agent>) provides consistent pipeline development workflows and handles MCP configuration automatically, making it easier to get started with AI-powered data engineering. You can customize this agent later to fit your team's specific workflow.

#### 🛠️ Manual Setup

#### Add dlt MCP to Continue CLI

Add the dlt MCP to your configuration:

**提示：**

**Installation methods:**
1. **Quick CLI install**: `cn --mcp dlthub/dlt-mcp`
2. **Manual configuration**: Add the MCP to your `~/.continue/config.json` under the `mcpServers` section

Once installed, dlt MCP tools become available to your Continue agent for all prompts.

**说明：**

The MCP will work with your existing dlt pipelines in your current directory.

#### Run Your First Pipeline Inspection

Start with a comprehensive pipeline check:
```bash
# TUI mode
cn
# Then type: Inspect the execution of my dlt pipeline and summarize the load info, including timing and file sizes.
```

#### Agent Requirements

  To use the pre-built [dlt Agent](<https://continue.dev/continuedev/dlt-agent>), you need either:
    - **Continue CLI Pro Plan** with the models add-on, OR
    - **Your own API keys** configured as environment variables

  The agent will automatically detect and use your configuration along with the pre-configured dlt MCP for pipeline operations.

---

## dlt MCP vs dlt+ MCP

#### Understanding the Difference

**dlt MCP** is focused on local pipeline development and inspection. It provides tools to:
- Inspect pipeline execution and load information
- Retrieve schema metadata from your local pipelines
- Query dataset records from destination databases
- Analyze load errors, timings, and file sizes

**[dlt+ MCP](<https://continue.dev/dlthub/dlt-plus-mcp>)** extends these capabilities with cloud-based features for production deployments:
- Connect to dlt+ Projects and manage deployments
- Monitor pipeline runs across multiple environments
- Access centralized logging and observability
- Collaborate with team members on pipeline development

For local development and getting started, **[dlt MCP](<https://continue.dev/dlthub/dlt-mcp>)** is the right choice. Consider **[dlt+ MCP](<https://continue.dev/dlthub/dlt-plus-mcp>)** when you need production deployment features and team collaboration.

---

## Pipeline Development Recipes

Now you can use natural language prompts to develop and debug your dlt pipelines. The Continue agent automatically calls the appropriate dlt MCP tools.

**说明：**

**Where to run these workflows:**
- **IDE Extensions**: Use Continue in VS Code, JetBrains, or other supported IDEs
- **Terminal (TUI mode)**: Run `cn` to enter interactive mode, then type your prompts
- **CLI (headless mode)**: Use `cn -p "your prompt"` for headless commands

**Test in Plan Mode First**: Before running pipeline operations that might make
changes, test your prompts in plan mode (see the [Plan Mode
Guide](<https://github.com/guides/plan-mode-guide>); press **Shift+Tab** to switch modes in TUI/IDE). This
shows you what the agent will do without executing it.

To run any of the example prompts below in headless mode, use `cn -p "prompt"`

**说明：**

**About the --auto flag**: The `--auto` flag enables tools to run continuously without manual confirmation. This is essential for headless mode where the agent needs to execute multiple tools automatically to complete tasks like pipeline inspection, schema retrieval, and error analysis.

### Pipeline Inspection

#### Inspect Pipeline Execution

Review pipeline execution details including load timing and file sizes.

**Prompt:**
```
Inspect my dlt pipeline execution and provide a summary of the load info.
Show me the timing breakdown and file sizes for each table.
```

### Schema Management

#### Retrieve Schema Metadata

Get detailed schema information for your pipeline's tables.

**Prompt:**
```
Show me the schema for my users table including all columns,
data types, and any constraints.
```

### Data Exploration

#### Query Dataset Records

Retrieve and analyze records from your destination database.

**Prompt:**
```
Get the last 10 records from my orders table and show me
the distribution of order statuses.
```

### Error Debugging

#### Analyze Load Errors

Investigate and understand pipeline load errors.

**Prompt:**
```
Check for any load errors in my last pipeline run. If there are errors,
explain what went wrong and suggest fixes.
```

### Pipeline Creation

#### Build New Pipeline

Create a new dlt pipeline from an API or data source.

**Prompt:**
```
Help me create a new dlt pipeline that loads data from the
JSONPlaceholder API users endpoint into DuckDB.
```

### Schema Evolution

#### Handle Schema Changes

Review and manage schema evolution in your pipelines.

**Prompt:**
```
Check if my pipeline schema has evolved since the last run.
Show me what columns were added or modified.
```

## Continuous Data Pipelines with GitHub Actions

  This example demonstrates a **Continuous AI workflow** where data pipeline validation runs automatically in your CI/CD pipeline in headless mode using the [dlt Assistant agent](<https://continue.dev/dlthub/dlt-assistant>). Consider customizing this agent to add your organization's specific validation rules.

### Add GitHub Secrets

Navigate to **Repository Settings → Secrets and variables → Actions** and add:

- `CONTINUE_API_KEY`: Your Continue API key from [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>)
- Any required database credentials for your destination

**说明：**

    The workflow uses the pre-built [dlt Agent](<https://continue.dev/continuedev/dlt-agent>) with `--agent continuedev/dlt-agent`. This agent comes pre-configured with the dlt MCP and optimized rules for pipeline operations. You can customize the validation rules and prompts for your specific pipeline requirements.

### Create Workflow File

This workflow automatically validates your dlt data pipelines on pull requests using the Continue CLI in [headless mode](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue CLI 非交互运行与流水线调用.md>). It inspects pipeline schemas, checks for errors, and posts a summary report as a PR comment. The workflow can also be triggered manually via `workflow_dispatch`.

Create `.github/workflows/dlt-pipeline-validation.yml` in your repository:

```yaml
name: Data Pipeline Validation with dlt MCP

on:
  pull_request:
    branches: [main]
  workflow_dispatch:

jobs:
  validate-pipeline:
    runs-on: ubuntu-latest
    env:
      CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "18"

      - name: Install dlt
        run: |
          pip install dlt
          echo "✅ dlt installed"

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Validate Pipeline Schema
        run: |
          echo "🔍 Validating pipeline schema..."
          cn --agent continuedev/dlt-agent \
             -p "Inspect the pipeline schema and verify all required tables
                 and columns are present. Flag any missing or unexpected changes." \
             --auto

      - name: Check Pipeline Health
        run: |
          echo "📊 Checking pipeline health..."
          cn --agent continuedev/dlt-agent \
             -p "Analyze the last pipeline run for errors or warnings.
                 Report any issues that need attention." \
             --auto

      - name: Comment Pipeline Report on PR
        if: always() && github.event_name == 'pull_request'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          REPORT=$(cn --agent continuedev/dlt-agent \
             -p "Generate a concise summary (200 words or less) of:
                 - Pipeline schemas and row counts
                 - Any load errors or warnings
                 - Performance metrics (timing, file sizes)
                 - Recommended improvements" \
             --auto)

          gh pr comment ${{ github.event.pull_request.number }} --body "$REPORT"
```

**说明：**

The dlt MCP works with your local pipeline state. Make sure your CI environment
has access to the necessary pipeline configuration and credentials.

## Pipeline Development Best Practices

Implement automated pipeline quality checks using Continue's rule system. See the [Rules deep dive](<https://docs.continue.dev/customize/deep-dives/rules>) for authoring tips.

#### Schema Validation

```bash
"Before committing pipeline changes, verify the schema
matches expectations and flag any unexpected modifications."
```

#### Error Handling

```bash
"When load errors occur, analyze the error details and
suggest specific code fixes to handle the data issues."
```

#### Performance Monitoring

```bash
"Track pipeline execution times and file sizes. Alert if
performance degrades significantly from baseline."
```

#### Data Quality

```bash
"After each pipeline run, validate row counts and check for
null values in critical columns."
```

## Troubleshooting

### Pipeline Not Found

```bash
"Check if there's a dlt pipeline in the current directory.
If not, help me initialize a new pipeline."
```

### Destination Connection Issues

```bash
"Verify the destination connection and credentials for my pipeline.
Test the connection and report any issues."
```

### Schema Inference Problems

**检查：**

**Verification Steps:**
- dlt MCP is installed and configured
- Pipeline directory is accessible
- Destination database credentials are configured
- Pipeline has been run at least once

## What You've Built

After completing this guide, you have a complete **AI-powered data pipeline development system** that:

✅ Uses natural language — Simple prompts instead of complex pipeline commands
✅ Debugs automatically — AI analyzes errors and suggests fixes
✅ Runs continuously — Automated validation in CI/CD pipelines
✅ Ensures quality — Pipeline checks prevent bad data from shipping

#### Continuous AI

Your data pipeline workflow now operates at **[Level 2 Continuous
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI handles routine pipeline inspection and debugging with human oversight
through review and approval of changes.

## Next Steps

1. **Inspect your first pipeline** - Try the pipeline inspection prompt on your current project
2. **Debug load errors** - Use the error analysis prompt to fix any issues
3. **Set up CI pipeline** - Add the GitHub Actions workflow to your repo
4. **Create new pipelines** - Use AI to scaffold new data sources
5. **Monitor performance** - Track pipeline execution metrics over time

## Additional Resources

#### [dlt Documentation](<https://dlthub.com/docs>)

Complete dlt platform documentation

#### [Continue Documentation](<https://docs.continue.dev>)

Explore Continue documentation and guides

#### [dlt Blog: MCP Deep Dive](<https://dlthub.com/blog/deep-dive-assistants-mcp-continue>)

Learn about AI agents, MCP, and Continue integration

#### [MCP Concepts](<https://dlthub.com/blog/deep-dive-assistants-mcp-continue>)

Deep dive into dlt MCP integration


</details>
