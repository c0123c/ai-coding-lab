# 用 Continue 与 Atlassian MCP 处理开发协作

工程教程。展示 Jira 问题查找和更新、Confluence 文档与 Compass 服务目录工作流，包含 OAuth、管理员条件和故障排查；属于开发协作参考，正文标注 Beta 条件。

- 来源：Continue
- 资料类型：有条件工程教程
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/bd36638ee0cf9fb90c314647bab2f1c6897aa6fe>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：依赖 Atlassian 服务、权限与 Beta 功能；与编码正文分开理解。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/atlassian-mcp-continue-cookbook.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/用 Continue 与 Atlassian MCP 处理开发协作.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Jira Issues and Confluence Pages with Atlassian MCP and Continue"
description: "Use Continue and the Atlassian Rovo MCP to search, summarize, and manage Jira issues, Confluence pages, and Compass components with natural language prompts."
sidebarTitle: "Atlassian Workflows with Continue"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### 你将构建什么

Atlassian 工作流程助手，将 Continue 与 Atlassian Rovo MCP 结合使用来：
- 跨项目搜索并总结 Jira 问题
- 查找并消化 Confluence 文档
- 使用自然语言创建和更新 Jira 问题
- 查询 Compass 组件和服务依赖关系
- 通过无头 CLI 运行自动化 Atlassian 工作流程

## 先决条件

开始之前，请确保您拥有：

- Node.js 18+本地安装
- 包含 Jira、Confluence、and/or Compass 的 Atlassian Cloud 站点
- 访问您想要集成的 Atlassian 产品

**注释：**

Atlassian Rovo MCP 服务器目前处于 ** 测试版 **。核心功能已可用，但某些功能仍在开发中。

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

#### 了解 OAuth 流程

Atlassian MCP 使用 **OAuth 2.1** 进行身份验证。首次连接时，您将完成基于浏览器的授权流程，该流程尊重您现有的 Atlassian 权限。

**注意：**

要在无头模式下使用 agents，您需要 [Continue API 密钥 ](<https://continue.dev/settings/api-keys>)。
所有数据访问都尊重您现有的 Jira、Confluence 和 Compass 用户权限。

## 选择您的 Atlassian Agent

#### 🚀 两个专业 Agents

我们创建了两个即用型 agents 来帮助您快速入门 - 它们预先配置了优化的提示、规则和 Atlassian MCP。您不必使用这些 agents，但它们旨在立即为您提供最佳体验。选择符合您需求的 agent，或创建您自己的自定义 agent。

### Agent 快速参考

| Agent |最适合|使用案例|链接|
|-------|----------|-----------|------|
|**吉拉 Agent**|问题管理|搜索问题、创建故事、冲刺计划、状态更新、批量操作|[查看Agent](<https://continue.dev/continuedev/atlassian-continuous-ai-jira-agent>)|
|**汇流 Agent**|文档|搜索页面、总结文档、create/update 页面、管理空间|[查看Agent](<https://continue.dev/continuedev/atlassian-continuous-ai-confluence-agent>)|

**注释：**

**跨产品工作流程**：agents 都可以与 Jira、Confluence 和 Compass 配合使用。根据您的主要关注领域进行选择，或使用 [Atlassian MCP](<https://continue.dev/atlassian/atlassian-mcp>) 创建您自己的 agent。 Atlassian MCP 可以与 Jira、Compass 或 Confluence 配合使用。

#### 📋 Jira Agent（推荐用于问题管理）

#### 使用 Jira Agent

使用 Atlassian Continuous AI - Jira Agent。此 agent 针对以下方面进行了优化：
- 搜索和过滤 Jira 问题
- 创建和更新问题
- Sprint 计划和工作量分析
- 问题转换和状态更新

无需安装 - 您可以立即从命令行开始使用它！

#### 完成OAuth授权

首次使用时，系统会提示您在浏览器中授权 MCP 服务器。这是一项一次性设置，可根据您的 Atlassian 权限授予访问权限。

#### 运行你的第一个命令

从终端的任何位置：
```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Show me my open issues assigned to me"
```
agent 将连接到您的 Atlassian 站点并返回结果。

**说明：**

**专业提示**：为 Jira 创建 shell 别名 agent：
```bash
alias jira-ai='cn --agent continuedev/atlassian-continuous-ai-jira-agent'
```
然后使用：`jira-ai "your prompt"`

#### 📄 Confluence Agent（推荐用于文档）

#### 使用 Confluence Agent

使用 Atlassian Continuous AI - Confluence Agent。此 agent 针对以下方面进行了优化：
- 搜索和总结文档
- 创建和更新 Confluence 页面
- 管理空间和内容
- 文档发现

无需安装 - 您可以立即从命令行开始使用它！

#### 完成OAuth授权

首次使用时，系统会提示您在浏览器中授权 MCP 服务器。这是一项一次性设置，可根据您的 Atlassian 权限授予访问权限。

#### 运行你的第一个命令

从终端的任何位置：
```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Summarize the Q2 planning page"
```
agent 将连接到您的 Atlassian 站点并返回结果。

**说明：**

**专业提示**：为 Confluence 创建 shell 别名 agent：
```bash
alias confluence-ai='cn --agent continuedev/atlassian-continuous-ai-confluence-agent'
```
然后使用：`confluence-ai "your prompt"`

#### 🛠️手动设置

#### 添加 Atlassian MCP

将 Atlassian MCP 添加到您的配置中。最小 YAML 示例：

**代码示例：** `config.yaml`

```yaml
name: My Config
version: 0.0.1
schema: v1

mcpServers:
  - name: Atlassian Rovo MCP
    type: sse
    url: https://mcp.atlassian.com/v1/sse
    connectionTimeout: 30
```

注意事项：
- Atlassian MCP 使用 ** 服务器发送事件 (SSE)** 传输类型
- OAuth 在第一次连接时自动处理
- 配置中不需要 API 密钥或令牌

#### 测试连接

启动 Continue 并询问：
```
List my Jira projects
```
如果这是您第一次，系统会提示您在浏览器中授权。

#### Agent 要求

要将 Atlassian MCP 与 Continue CLI 一起使用，您需要：
    - **Continue CLI 专业计划 ** 以及模型附加组件，OR
    - **您自己的API密钥**配置为环境变量

agent 将自动检测并使用您的配置以及 Atlassian MCP 进行 Jira、Confluence 和 Compass 操作。

---

## Jira 工作流程

**注释：**

**使用 Jira Agent**：以下所有示例均使用针对问题管理进行了优化的 [Jira Agent](<https://continue.dev/continuedev/atlassian-continuous-ai-jira-agent>)。

使用自然语言来探索、分类和管理 Jira 问题。 agent 在底层调用 Atlassian MCP 工具。

**说明：**

**以无头模式运行**：在提示符之前添加 `-p` 标志，并在末尾添加 `--auto` 标志：
```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent -p "your prompt" --auto
```
**重要**：在使用无头模式之前，请先完成浏览器OAuth身份验证。

### 搜索与发现

#### 查找未解决的错误

搜索项目中的错误。

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Find all open bugs in Project Alpha"
```

#### 我分配的问题

获取分配给您的问题列表。

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Show me all issues assigned to me that are in progress"
```

#### 冲刺计划

分析冲刺工作量和优先级。

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "List all issues in the current sprint. Group by assignee and priority. Summarize the workload distribution and flag any overloaded team members."
```

### 创建和更新

#### 根据描述创建故事

将自然语言转化为 Jira 故事。

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Create a story titled 'Redesign onboarding flow' in Project Alpha. Description: We need to simplify the user registration process by reducing steps from 5 to 3. Target completion: Q3."
```

#### 批量创建问题

根据会议记录或规范创建多个问题。

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Create five Jira issues from these meeting notes: 1) Fix login timeout bug 2) Update API documentation 3) Implement dark mode toggle 4) Review security audit findings 5) Optimize database queries"
```

#### 更新问题状态

自然语言的转换问题。

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Move PROJ-123 to In Progress and add a comment: Starting work on this today. ETA: end of week."
```

---

## Confluence 工作流程

**注释：**

**使用 Confluence Agent**：以下所有示例均使用针对文档管理进行了优化的 [Confluence Agent](<https://continue.dev/continuedev/atlassian-continuous-ai-confluence-agent>)。

直接从 Continue 访问、搜索和管理团队的文档。

**说明：**

**以无头模式运行**：在提示符之前添加 `-p` 标志，并在末尾添加 `--auto` 标志：
```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent -p "your prompt" --auto
```
**重要**：在使用无头模式之前，请先完成浏览器OAuth身份验证。

### 搜索与总结

#### 总结文档

获取 Confluence 页面的快速摘要。

```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Summarize the Q2 planning page from the Engineering space"
```

#### 查找文档

在 Confluence 中搜索特定信息。

```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Find all pages about API authentication in our developer docs"
```

#### 列出可用空间

了解您可以访问哪些 Confluence 空间。

```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "What Confluence spaces do I have access to?"
```

### 创建和更新

#### 创建文档页面

生成一个包含结构化内容的新 Confluence 页面。

```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Create a page titled 'Team Goals Q3' in the Engineering space. Include sections for: Objectives, Key Results, and Timeline."
```

#### 更新现有页面

修改现有页面上的内容。

```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Update the 'Onboarding Guide' page to add a new section about our code review process"
```

---

## 指南针工作流程

**注释：**

**使用其中一个 Agent**：罗盘工作流程可与两个 agents 配合使用。使用 Jira agent 进行组件到问题的工作流程，或使用 Confluence agent 进行组件到文档的工作流程。

通过 Compass 集成查询和管理您的服务架构。

**说明：**

**以无头模式运行**：在提示符之前添加 `-p` 标志，并在末尾添加 `--auto` 标志。使用无头模式之前，请先完成浏览器 OAuth 身份验证。

### 服务发现

#### 查询依赖关系

了解服务关系。

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "What services depend on the api-gateway component?"
```

#### 创建服务组件

在 Compass 中注册新服务。

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Create a service component for the current repository. Use the package.json to infer details."
```

#### 批量导入组件

从结构化数据导入多个组件。

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Import Compass components and custom fields from this CSV: [paste CSV data]"
```

或者引用一个文件：
```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Import Compass components from services.csv in the current directory"
```

---

## 组合工作流程

**注释：**

**跨产品工作流程**：这些工作流程跨越多个 Atlassian 产品。根据您的主要关注点选择 agent：
- 当工作流程以问题为中心时，使用 **Jira Agent**
- 当工作流程以文档为中心时，使用 **Confluence Agent**

集成 Jira、Confluence 和 Compass 之间的操作，以实现强大的跨产品工作流程。

**说明：**

**以无头模式运行**：在提示符之前添加 `-p` 标志，并在末尾添加 `--auto` 标志。使用无头模式之前，请先完成浏览器 OAuth 身份验证。

#### 将 Jira 连接到 Confluence

连接跨产品的相关内容。

```bash
# Using Jira Agent for issue-centric workflow
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Link Jira tickets PROJ-123, PROJ-456, and PROJ-789 to the 'Sprint 23 Release Plan' Confluence page"
```

#### 服务文档查找

查找特定组件的文档。

```bash
# Using Confluence Agent for docs-centric workflow
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Fetch the Confluence documentation page linked to the user-authentication Compass component"
```

#### 发行说明生成

根据已完成的工作创建发布文档。

```bash
# Using Jira Agent to pull issue data and create in Confluence
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Generate release notes for all Jira issues completed in Sprint 23. Create a Confluence page in the Release Notes space with: New features (grouped by epic), Bug fixes, Known issues, Deployment instructions"
```

---

## 故障排除

#### OAuth授权问题

- **“您的站点管理员必须授权此应用程序”**：站点管理员需要先完成 OAuth 流程，然后其他用户才能连接
- **无法完成浏览器授权**：确保您登录到正确的 Atlassian Cloud 站点并拥有必要的权限
- **连接超时**：增加MCP配置中的`connectionTimeout`或检查您的网络连接

#### 权限错误

- **“访问被拒绝”或“禁止”**：MCP 尊重您现有的 Atlassian 权限。验证您有权访问您尝试访问的 project/space/component
- **无法创建问题**：确保您在目标 Jira 项目中拥有“创建问题”权限
- **无法编辑 Confluence 页面**：验证您在目标 Confluence 空间中拥有编辑权限

#### 速率限制

- **标准计划**：中等使用阈值
- **Premium/Enterprise 计划**：更高的配额（1,000 requests/hour 加上每用户限制）
- 如果达到速率限制，请减少查询频率或批量操作

#### 测试版限制

- 批量操作可能会受到速率限制的限制
- 如果没有明确设置，自定义 Jira 字段可能无法识别
- Workspace/site 在单个会话中无法进行切换
- 检查 [Atlassian 社区 ](<https://community.atlassian.com/>) 的已知问题

### 管理注意事项

#### 对于 Atlassian 站点管理员

**安装**：Atlassian Rovo MCP 会在首次 OAuth 授权时自动安装（JIT 安装） - 无需安装 Marketplace。

**管理访问**：
- 在 [Admin Hub](<https://admin.atlassian.com>) 中查看连接的应用程序
- 用户可以从其 [ 个人资料设置 ](<https://id.atlassian.com/manage-profile/apps>) 撤销访问权限
- 通过管理中心中的“用户安装的应用程序”控件阻止用户安装的应用程序

**安全**：所有流量均通过HTTPS（TLS 1.2+）加密，访问尊重现有用户权限。

---

## 你建造了什么

完成本指南后，您将拥有一个完整的 **AI- 支持的 Atlassian 工作流程系统 **，该系统：

- ✅ 使用自然语言——复杂的 Atlassian 操作的简单提示
- ✅ 跨越多种产品——在 Jira、Confluence 和 Compass 之间无缝工作
- ✅ 尊重权限 - 通过现有角色强制执行确保基于 OAuth 的访问安全
- ✅ 无头运行 — 集成到 CI/CD 管道和自动化脚本中

#### 连续AI

您的 Atlassian 工作流程现在在 **[ 级别 2 连续 
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI 运行，通过 
 审核和批准在人工监督下处理日常项目管理任务。

## 下一步

1. **探索您的项目** - 尝试 Jira 搜索和分类提示
2. **组织文档** - 使用Confluence工作流程总结和创建页面
3. **映射您的服务** - 查询 Compass 服务依赖关系
4. **自定义提示** - 根据团队的特定需求定制工作流程
5. **监控使用情况** - 跟踪 AI 如何改进您的 Atlassian 工作流程

## 其他资源

#### [阿特拉斯 Rovo MCP](<https://support.atlassian.com/atlassian-rovo-mcp-server/docs/getting-started-with-the-atlassian-remote-mcp-server/>)

Atlassian 官方 MCP 文档

#### [MCP 在 Continue](<https://docs.continue.dev/guides/Continue_Continue MCP 设置、传输与凭据管理.md>) 中

MCP 如何与 Continue agents 配合使用

#### [Continue 文档](<https://docs.continue.dev>)

Continue 文档和指南

#### [Atlassian社区](<https://community.atlassian.com/>)

获取帮助并分享反馈

## 示例用例

#### 发布管理

自动生成发行说明、将问题链接到文档并跟踪 Jira 和 Confluence 中的部署状态。

#### 冲刺计划

分析工作负载分布、识别阻碍因素并自动生成冲刺摘要。

#### 服务所有权

在 Compass 中映射服务依赖关系，在 Confluence 中链接到技术文档，并在 Jira 中跟踪相关问题。

#### 入职自动化

创建标准化 Jira 入职票据、链接到 Confluence 指南并跟踪新员工进度。


## 原文对照

<details>
<summary>展开完整原文</summary>

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### What You'll Build

An Atlassian workflow assistant that uses Continue with the Atlassian Rovo MCP to:
- Search and summarize Jira issues across projects
- Find and digest Confluence documentation
- Create and update Jira issues with natural language
- Query Compass components and service dependencies
- Automate Atlassian workflows with headless CLI runs

## Prerequisites

Before starting, ensure you have:

- Node.js 18+ installed locally
- An Atlassian Cloud site with Jira, Confluence, and/or Compass
- Access to the Atlassian products you want to integrate with

**注释：**

The Atlassian Rovo MCP Server is currently in **Beta**. Core functionality is available, but some features are still under development.

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

#### Understand OAuth Flow

The Atlassian MCP uses **OAuth 2.1** for authentication. The first time you connect, you'll complete a browser-based authorization flow that respects your existing Atlassian permissions.

**注意：**

To use agents in headless mode, you need a [Continue API key](<https://continue.dev/settings/api-keys>).
All data access respects your existing Jira, Confluence, and Compass user permissions.

## Choose Your Atlassian Agent

#### 🚀 Two Specialized Agents

We've created two ready-to-use agents to help you get started fast - they come pre-configured with optimized prompts, rules, and the Atlassian MCP. You don't have to use these agents, but they're designed to give you the best experience right away. Choose the agent that matches your needs, or create your own custom agent.

### Agent Quick Reference

| Agent | Best For | Use Cases | Link |
|-------|----------|-----------|------|
| **Jira Agent** | Issue Management | Search issues, create stories, sprint planning, status updates, bulk operations | [View Agent](<https://continue.dev/continuedev/atlassian-continuous-ai-jira-agent>) |
| **Confluence Agent** | Documentation | Search pages, summarize docs, create/update pages, manage spaces | [View Agent](<https://continue.dev/continuedev/atlassian-continuous-ai-confluence-agent>) |

**注释：**

**Cross-product workflows**: Both agents can work with Jira, Confluence, and Compass. Choose based on your primary focus area, or create your own agent using the [Atlassian MCP](<https://continue.dev/atlassian/atlassian-mcp>). The Atlassian MCP can work with Jira, Compass, or Confluence.

#### 📋 Jira Agent (Recommended for Issue Management)

#### Use the Jira Agent

Use the Atlassian Continuous AI - Jira Agent. This agent is optimized for:
- Searching and filtering Jira issues
- Creating and updating issues
- Sprint planning and workload analysis
- Issue transitions and status updates

No installation needed - you can start using it immediately from the command line!

#### Complete OAuth Authorization

On first use, you'll be prompted to authorize the MCP server in your browser. This is a one-time setup that grants access based on your Atlassian permissions.

#### Run Your First Command

From anywhere in your terminal:
```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Show me my open issues assigned to me"
```
The agent will connect to your Atlassian site and return results.

**说明：**

**Pro tip**: Create a shell alias for the Jira agent:
```bash
alias jira-ai='cn --agent continuedev/atlassian-continuous-ai-jira-agent'
```
Then use: `jira-ai "your prompt"`

#### 📄 Confluence Agent (Recommended for Documentation)

#### Use the Confluence Agent

Use the Atlassian Continuous AI - Confluence Agent. This agent is optimized for:
- Searching and summarizing documentation
- Creating and updating Confluence pages
- Managing spaces and content
- Documentation discovery

No installation needed - you can start using it immediately from the command line!

#### Complete OAuth Authorization

On first use, you'll be prompted to authorize the MCP server in your browser. This is a one-time setup that grants access based on your Atlassian permissions.

#### Run Your First Command

From anywhere in your terminal:
```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Summarize the Q2 planning page"
```
The agent will connect to your Atlassian site and return results.

**说明：**

**Pro tip**: Create a shell alias for the Confluence agent:
```bash
alias confluence-ai='cn --agent continuedev/atlassian-continuous-ai-confluence-agent'
```
Then use: `confluence-ai "your prompt"`

#### 🛠️ Manual Setup

#### Add Atlassian MCP

Add the Atlassian MCP to your configuration. Minimal YAML example:

**代码示例：** `config.yaml`

```yaml
name: My Config
version: 0.0.1
schema: v1

mcpServers:
  - name: Atlassian Rovo MCP
    type: sse
    url: https://mcp.atlassian.com/v1/sse
    connectionTimeout: 30
```

Notes:
- The Atlassian MCP uses **Server-Sent Events (SSE)** transport type
- OAuth is handled automatically on first connection
- No API keys or tokens needed in the config

#### Test the Connection

Launch Continue and ask:
```
List my Jira projects
```
You'll be prompted to authorize in your browser if this is your first time.

#### Agent Requirements

  To use Atlassian MCP with Continue CLI, you need either:
    - **Continue CLI Pro Plan** with the models add-on, OR
    - **Your own API keys** configured as environment variables

  The agent will automatically detect and use your configuration along with the Atlassian MCP for Jira, Confluence, and Compass operations.

---

## Jira Workflows

**注释：**

**Using the Jira Agent**: All examples below use the [Jira Agent](<https://continue.dev/continuedev/atlassian-continuous-ai-jira-agent>) which is optimized for issue management.

Use natural language to explore, triage, and manage Jira issues. The agent calls Atlassian MCP tools under the hood.

**说明：**

**Running in headless mode**: Add `-p` flag before your prompt and `--auto` flag at the end:
```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent -p "your prompt" --auto
```
**Important**: Complete browser OAuth authentication first before using headless mode.

### Search and Discovery

#### Find Open Bugs

Search for bugs across your projects.

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Find all open bugs in Project Alpha"
```

#### My Assigned Issues

Get a list of issues assigned to you.

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Show me all issues assigned to me that are in progress"
```

#### Sprint Planning

Analyze sprint workload and priorities.

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "List all issues in the current sprint. Group by assignee and priority. Summarize the workload distribution and flag any overloaded team members."
```

### Create and Update

#### Create Story from Description

Turn natural language into a Jira story.

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Create a story titled 'Redesign onboarding flow' in Project Alpha. Description: We need to simplify the user registration process by reducing steps from 5 to 3. Target completion: Q3."
```

#### Bulk Issue Creation

Create multiple issues from meeting notes or specs.

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Create five Jira issues from these meeting notes: 1) Fix login timeout bug 2) Update API documentation 3) Implement dark mode toggle 4) Review security audit findings 5) Optimize database queries"
```

#### Update Issue Status

Transition issues with natural language.

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Move PROJ-123 to In Progress and add a comment: Starting work on this today. ETA: end of week."
```

---

## Confluence Workflows

**注释：**

**Using the Confluence Agent**: All examples below use the [Confluence Agent](<https://continue.dev/continuedev/atlassian-continuous-ai-confluence-agent>) which is optimized for documentation management.

Access, search, and manage your team's documentation directly from Continue.

**说明：**

**Running in headless mode**: Add `-p` flag before your prompt and `--auto` flag at the end:
```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent -p "your prompt" --auto
```
**Important**: Complete browser OAuth authentication first before using headless mode.

### Search and Summarize

#### Summarize Documentation

Get a quick summary of a Confluence page.

```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Summarize the Q2 planning page from the Engineering space"
```

#### Find Documentation

Search for specific information across Confluence.

```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Find all pages about API authentication in our developer docs"
```

#### List Available Spaces

Discover what Confluence spaces you have access to.

```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "What Confluence spaces do I have access to?"
```

### Create and Update

#### Create Documentation Page

Generate a new Confluence page with structured content.

```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Create a page titled 'Team Goals Q3' in the Engineering space. Include sections for: Objectives, Key Results, and Timeline."
```

#### Update Existing Page

Modify content on an existing page.

```bash
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Update the 'Onboarding Guide' page to add a new section about our code review process"
```

---

## Compass Workflows

**注释：**

**Using Either Agent**: Compass workflows work with both agents. Use the Jira agent for component-to-issue workflows, or the Confluence agent for component-to-docs workflows.

Query and manage your service architecture with Compass integration.

**说明：**

**Running in headless mode**: Add `-p` flag before your prompt and `--auto` flag at the end. Complete browser OAuth authentication first before using headless mode.

### Service Discovery

#### Query Dependencies

Understand service relationships.

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "What services depend on the api-gateway component?"
```

#### Create Service Component

Register a new service in Compass.

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Create a service component for the current repository. Use the package.json to infer details."
```

#### Bulk Import Components

Import multiple components from structured data.

```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Import Compass components and custom fields from this CSV: [paste CSV data]"
```

Or reference a file:
```bash
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Import Compass components from services.csv in the current directory"
```

---

## Combined Workflows

**注释：**

**Cross-Product Workflows**: These workflows span multiple Atlassian products. Choose the agent based on your primary focus:
- Use **Jira Agent** when the workflow is issue-centric
- Use **Confluence Agent** when the workflow is documentation-centric

Integrate actions across Jira, Confluence, and Compass for powerful cross-product workflows.

**说明：**

**Running in headless mode**: Add `-p` flag before your prompt and `--auto` flag at the end. Complete browser OAuth authentication first before using headless mode.

#### Link Jira to Confluence

Connect related content across products.

```bash
# Using Jira Agent for issue-centric workflow
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Link Jira tickets PROJ-123, PROJ-456, and PROJ-789 to the 'Sprint 23 Release Plan' Confluence page"
```

#### Service Documentation Lookup

Find documentation for a specific component.

```bash
# Using Confluence Agent for docs-centric workflow
cn --agent continuedev/atlassian-continuous-ai-confluence-agent "Fetch the Confluence documentation page linked to the user-authentication Compass component"
```

#### Release Notes Generation

Create release documentation from completed work.

```bash
# Using Jira Agent to pull issue data and create in Confluence
cn --agent continuedev/atlassian-continuous-ai-jira-agent "Generate release notes for all Jira issues completed in Sprint 23. Create a Confluence page in the Release Notes space with: New features (grouped by epic), Bug fixes, Known issues, Deployment instructions"
```

---

## Troubleshooting

#### OAuth Authorization Issues

- **"Your site admin must authorize this app"**: A site admin needs to complete the OAuth flow first before other users can connect
- **Can't complete browser authorization**: Ensure you're logged into the correct Atlassian Cloud site and have necessary permissions
- **Connection timeout**: Increase `connectionTimeout` in your MCP config or check your network connection

#### Permission Errors

- **"Access denied" or "Forbidden"**: The MCP respects your existing Atlassian permissions. Verify you have access to the project/space/component you're trying to access
- **Can't create issues**: Ensure you have "Create issues" permission in the target Jira project
- **Can't edit Confluence pages**: Verify you have edit permissions in the target Confluence space

#### Rate Limiting

- **Standard plan**: Moderate usage thresholds
- **Premium/Enterprise plans**: Higher quotas (1,000 requests/hour plus per-user limits)
- If you hit rate limits, reduce query frequency or batch operations

#### Beta Limitations

- Bulk operations may be constrained by rate limits
- Custom Jira fields may not be recognized without explicit setup
- Workspace/site switching not available within a single session
- Check [Atlassian Community](<https://community.atlassian.com/>) for known issues

### Admin Considerations

#### For Atlassian Site Admins

**Installation**: The Atlassian Rovo MCP is automatically installed on first OAuth authorization (JIT installation) - no Marketplace installation needed.

**Managing Access**:
- View connected apps in [Admin Hub](<https://admin.atlassian.com>)
- Users can revoke access from their [profile settings](<https://id.atlassian.com/manage-profile/apps>)
- Block user-installed apps via "user-installed apps" control in Admin Hub

**Security**: All traffic is encrypted via HTTPS (TLS 1.2+), and access respects existing user permissions.

---

## What You've Built

After completing this guide, you have a complete **AI-powered Atlassian workflow system** that:

- ✅ Uses natural language — Simple prompts for complex Atlassian operations
- ✅ Spans multiple products — Seamlessly works across Jira, Confluence, and Compass
- ✅ Respects permissions — Secure OAuth-based access with existing role enforcement
- ✅ Runs headlessly — Integrate into CI/CD pipelines and automation scripts

#### Continuous AI

Your Atlassian workflows now operate at **[Level 2 Continuous
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI handles routine project management tasks with human oversight through
review and approval.

## Next Steps

1. **Explore your projects** - Try the Jira search and triage prompts
2. **Organize documentation** - Use Confluence workflows to summarize and create pages
3. **Map your services** - Query Compass for service dependencies
4. **Customize prompts** - Tailor workflows to your team's specific needs
5. **Monitor usage** - Track how AI improves your Atlassian workflows

## Additional Resources

#### [Atlassian Rovo MCP](<https://support.atlassian.com/atlassian-rovo-mcp-server/docs/getting-started-with-the-atlassian-remote-mcp-server/>)

Official Atlassian MCP documentation

#### [MCP in Continue](<https://docs.continue.dev/guides/Continue_Continue MCP 设置、传输与凭据管理.md>)

How MCP works with Continue agents

#### [Continue Documentation](<https://docs.continue.dev>)

Continue documentation and guides

#### [Atlassian Community](<https://community.atlassian.com/>)

Get help and share feedback

## Example Use Cases

#### Release Management

Automate release notes generation, link issues to documentation, and track deployment status across Jira and Confluence.

#### Sprint Planning

Analyze workload distribution, identify blockers, and generate sprint summaries automatically.

#### Service Ownership

Map service dependencies in Compass, link to technical documentation in Confluence, and track related issues in Jira.

#### Onboarding Automation

Create standardized Jira onboarding tickets, link to Confluence guides, and track new hire progress.


</details>
