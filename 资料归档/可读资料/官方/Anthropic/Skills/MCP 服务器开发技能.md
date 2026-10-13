# MCP 服务器开发技能

按调研、接口规划、工具实现、代码审查和评测的流程开发 MCP 服务器，包含 Python 与 TypeScript 的实现参考。

- 来源：Anthropic／Skills
- 资料类型：存档文档
- 存档版本：`dbd4588f9e10`。
- 文档时间：源码提交（存档版）：[2025-12-01](<https://github.com/anthropics/skills/commit/ef740771ac901e03fbca3ce4e1c453a96010f30a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/skills/blob/main/skills/mcp-builder/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Skills/MCP 服务器开发技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: mcp-builder
description: Guide for creating high-quality MCP (Model Context Protocol) servers that enable LLMs to interact with external services through well-designed tools. Use when building MCP servers to integrate external APIs or services, whether in Python (FastMCP) or Node/TypeScript (MCP SDK).
license: Complete terms in LICENSE.txt
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# MCP 服务器开发指南

## 概述

创建 MCP (Model Context Protocol) 服务器，使 LLM 能够通过精心设计的工具与外部服务交互。 MCP 服务器的质量是通过它使法学硕士完成实际任务的能力来衡量的。

---

# 工艺流程

## 🚀 高级工作流程

创建高质量的 MCP 服务器涉及四个主要阶段：

### 阶段1：深入研究与规划

#### 1.1了解现代MCP设计

**API 覆盖范围与工作流程工具：**
使用专门的工作流程工具平衡全面的 API 端点覆盖范围。工作流工具可以更方便地完成特定任务，而全面的覆盖范围则使agents可以灵活地组合操作。性能因客户端而异，一些客户端受益于结合基本工具的代码执行，而另一些客户端则通过更高级别的工作流程更好地工作。当不确定时，优先考虑全面的 API 覆盖范围。

**工具命名和可发现性：**
清晰、描述性的工具名称有助于agents快速找到合适的工具。使用一致的前缀（例如，`github_create_issue`、`github_list_repos`）和面向操作的命名。

**上下文管理：**
Agents受益于简洁的工具描述和filter/paginate结果的能力。设计可返回重点相关数据的工具。一些客户端支持代码执行，这可以帮助agents高效地过滤和处理数据。

**可操作的错误消息：**
错误消息应指导agents找到包含具体建议和后续步骤的解决方案。

#### 1.2 研究 MCP 协议文档

**浏览MCP规范：**

从站点地图开始查找相关页面：`https://modelcontextprotocol.io/sitemap.xml`

然后获取带有 `.md` 后缀的 Markdown 格式的特定页面（例如，`https://modelcontextprotocol.io/specification/draft.md`）。

要查看的关键页面：
- 规范概述和架构
- 传输机制（可流传输 HTTP、stdio）
- 工具、资源和提示定义

#### 1.3学习框架文档

**推荐堆栈：**
- **语言**：TypeScript（在许多执行环境中高质量的SDK支持和良好的兼容性，例如MCPB。加上AI模型擅长生成TypeScript代码，受益于其广泛的用途、静态类型和良好的 linting 工具）
- **传输**：用于远程服务器的可流式传输HTTP，使用无状态JSON（与有状态会话和流式响应相比，更易于扩展和维护）。用于本地服务器的 stdio。

**加载框架文档：**

- **MCP 最佳实践**：[📋 查看最佳实践](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/mcp_best_practices.md>) - 核心指南

**对于TypeScript（推荐）：**
- **TypeScript SDK**：使用WebFetch加载`https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md`
- [⚡ TypeScript 指南](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/node_mcp_server.md>) - TypeScript 模式和示例

**对于Python：**
- **Python SDK**：使用WebFetch加载`https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md`
- [🐍 Python 指南](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/python_mcp_server.md>) - Python 模式和示例

#### 1.4 规划您的实施

**了解 API：**
查看服务的 API 文档，以确定关键端点、身份验证要求和数据模型。根据需要使用网络搜索和 WebFetch。

**工具选择：**
优先考虑全面的API覆盖范围。列出要实现的端点，从最常见的操作开始。

---

### 阶段 2：实施

#### 2.1 设置项目结构

请参阅项目设置的特定语言指南：
- [⚡ TypeScript 指南](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/node_mcp_server.md>) - 项目结构，package.json，tsconfig.json
- [🐍 Python 指南](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/python_mcp_server.md>) - 模块组织、依赖关系

#### 2.2 实施核心基础设施

创建共享实用程序：
- API 具有身份验证的客户端
- 错误处理助手
- 响应格式（JSON/Markdown）
- 分页支持

#### 2.3 实施工具

对于每个工具：

**输入架构：**
- 使用 Zod (TypeScript) 或 Pydantic (Python)
- 包括约束和清晰的描述
- 在字段描述中添加示例

**输出架构：**
- 尽可能为结构化数据定义 `outputSchema`
- 在工具响应中使用 `structuredContent`（TypeScript SDK 功能）
- 帮助客户理解和处理工具输出

**工具说明：**
- 功能简明总结
- 参数说明
- 返回类型架构

**实施：**
- Async/await 用于 I/O 操作
- 通过可操作的消息进行正确的错误处理
- 在适用的情况下支持分页
- 使用现代 SDK 时返回文本内容和结构化数据

**注释：**
- `readOnlyHint`: true/false
- `destructiveHint`: true/false
- `idempotentHint`: true/false
- `openWorldHint`: true/false

---

### 阶段 3：审查和测试

#### 3.1 代码质量

评论：
- 不重复代码（DRY原则）
- 一致的错误处理
- 全类型覆盖
- 清晰的工具说明

#### 3.2 构建和测试

**TypeScript:**
- 运行`npm run build`验证编译
- 使用 MCP 检查员进行测试：`npx @modelcontextprotocol/inspector`

**Python:**
- 验证语法：`python -m py_compile your_server.py`
- 使用 MCP 检查员进行测试

请参阅特定语言的指南，了解详细的测试方法和质量检查表。

---

### 阶段 4：创建评估

实施 MCP 服务器后，创建全面的评估以测试其有效性。

**加载[✅评估指南](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/evaluation.md>)以获取完整的评估指南。**

#### 4.1了解评估目的

使用评估来测试法学硕士是否可以有效地使用您的 MCP 服务器来回答现实、复杂的问题。

#### 4.2 创建 10 评估问题

要进行有效的评估，请遵循评估指南中概述的流程：

1. **工具检查**：列出可用工具并了解其功能
2. **内容探索**：使用READ-ONLY操作探索可用数据
3. **问题生成**：创建10复杂、现实的问题
4. **答案验证**：自己解决每个问题以验证答案

#### 4.3评估要求

确保每个问题是：
- **独立**：不依赖于其他问题
- **只读**：仅需要非破坏性操作
- **复杂**：需要多次工具调用和深度探索
- **现实**：基于人类会关心的真实用例
- **可验证**：可以通过字符串比较来验证的单一、清晰的答案
- **稳定**：答案不会随时间改变

#### 4.4 输出格式

创建具有以下结构的 XML 文件：

```xml
<evaluation>
  <qa_pair>
    <question>Find discussions about AI model launches with animal codenames. One model needed a specific safety designation that uses the format ASL-X. What number X was being determined for the model named after a spotted wild cat?</question>
    <answer>3</answer>
  </qa_pair>
<!-- More qa_pairs... -->
</evaluation>
```

---

# 参考文件

## 📚 文档库

在开发过程中根据需要加载这些资源：

### 核心MCP文档（首先加载）
- **MCP 协议**：从 `https://modelcontextprotocol.io/sitemap.xml` 处的站点地图开始，然后获取带有 `.md` 后缀的特定页面
- [📋 MCP 最佳实践](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/mcp_best_practices.md>) - 通用 MCP 指南，包括：
  - 服务器和工具命名约定
  - 响应格式指南（JSON 与 Markdown）
  - 分页最佳实践
  - 传输选择（流式传输 HTTP 与 stdio）
  - 安全和错误处理标准

### SDK 文档（相位 1/2 期间的负载）
- **Python SDK**：从 `https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md` 获取
- **TypeScript SDK**：从 `https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md` 获取

### 特定于语言的实施指南（阶段加载 2）
- [🐍 Python 实施指南](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/python_mcp_server.md>) - 完整的 Python/FastMCP 指南：
  - 服务器初始化模式
  - Pydantic 模型示例
  - 使用 `@mcp.tool` 进行工具注册
  - 完整的工作示例
  - 质量检查表

- [⚡ TypeScript 实施指南](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/node_mcp_server.md>) - 完整的 TypeScript 指南：
  - 项目结构
  - Zod 模式模式
  - 使用 `server.registerTool` 进行工具注册
  - 完整的工作示例
  - 质量检查表

### 评估指南（相位期间负载4）
- [✅ 评估指南](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/evaluation.md>) - 完整的评估创建指南：
  - 问题创建指南
  - 答案验证策略
  - XML格式规范
  - 示例问题和答案
  - 使用提供的脚本运行评估


## 原文对照

<details>
<summary>展开完整原文</summary>

# MCP Server Development Guide

## Overview

Create MCP (Model Context Protocol) servers that enable LLMs to interact with external services through well-designed tools. The quality of an MCP server is measured by how well it enables LLMs to accomplish real-world tasks.

---

# Process

## 🚀 High-Level Workflow

Creating a high-quality MCP server involves four main phases:

### Phase 1: Deep Research and Planning

#### 1.1 Understand Modern MCP Design

**API Coverage vs. Workflow Tools:**
Balance comprehensive API endpoint coverage with specialized workflow tools. Workflow tools can be more convenient for specific tasks, while comprehensive coverage gives agents flexibility to compose operations. Performance varies by client—some clients benefit from code execution that combines basic tools, while others work better with higher-level workflows. When uncertain, prioritize comprehensive API coverage.

**Tool Naming and Discoverability:**
Clear, descriptive tool names help agents find the right tools quickly. Use consistent prefixes (e.g., `github_create_issue`, `github_list_repos`) and action-oriented naming.

**Context Management:**
Agents benefit from concise tool descriptions and the ability to filter/paginate results. Design tools that return focused, relevant data. Some clients support code execution which can help agents filter and process data efficiently.

**Actionable Error Messages:**
Error messages should guide agents toward solutions with specific suggestions and next steps.

#### 1.2 Study MCP Protocol Documentation

**Navigate the MCP specification:**

Start with the sitemap to find relevant pages: `https://modelcontextprotocol.io/sitemap.xml`

Then fetch specific pages with `.md` suffix for markdown format (e.g., `https://modelcontextprotocol.io/specification/draft.md`).

Key pages to review:
- Specification overview and architecture
- Transport mechanisms (streamable HTTP, stdio)
- Tool, resource, and prompt definitions

#### 1.3 Study Framework Documentation

**Recommended stack:**
- **Language**: TypeScript (high-quality SDK support and good compatibility in many execution environments e.g. MCPB. Plus AI models are good at generating TypeScript code, benefiting from its broad usage, static typing and good linting tools)
- **Transport**: Streamable HTTP for remote servers, using stateless JSON (simpler to scale and maintain, as opposed to stateful sessions and streaming responses). stdio for local servers.

**Load framework documentation:**

- **MCP Best Practices**: [📋 View Best Practices](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/mcp_best_practices.md>) - Core guidelines

**For TypeScript (recommended):**
- **TypeScript SDK**: Use WebFetch to load `https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md`
- [⚡ TypeScript Guide](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/node_mcp_server.md>) - TypeScript patterns and examples

**For Python:**
- **Python SDK**: Use WebFetch to load `https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md`
- [🐍 Python Guide](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/python_mcp_server.md>) - Python patterns and examples

#### 1.4 Plan Your Implementation

**Understand the API:**
Review the service's API documentation to identify key endpoints, authentication requirements, and data models. Use web search and WebFetch as needed.

**Tool Selection:**
Prioritize comprehensive API coverage. List endpoints to implement, starting with the most common operations.

---

### Phase 2: Implementation

#### 2.1 Set Up Project Structure

See language-specific guides for project setup:
- [⚡ TypeScript Guide](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/node_mcp_server.md>) - Project structure, package.json, tsconfig.json
- [🐍 Python Guide](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/python_mcp_server.md>) - Module organization, dependencies

#### 2.2 Implement Core Infrastructure

Create shared utilities:
- API client with authentication
- Error handling helpers
- Response formatting (JSON/Markdown)
- Pagination support

#### 2.3 Implement Tools

For each tool:

**Input Schema:**
- Use Zod (TypeScript) or Pydantic (Python)
- Include constraints and clear descriptions
- Add examples in field descriptions

**Output Schema:**
- Define `outputSchema` where possible for structured data
- Use `structuredContent` in tool responses (TypeScript SDK feature)
- Helps clients understand and process tool outputs

**Tool Description:**
- Concise summary of functionality
- Parameter descriptions
- Return type schema

**Implementation:**
- Async/await for I/O operations
- Proper error handling with actionable messages
- Support pagination where applicable
- Return both text content and structured data when using modern SDKs

**Annotations:**
- `readOnlyHint`: true/false
- `destructiveHint`: true/false
- `idempotentHint`: true/false
- `openWorldHint`: true/false

---

### Phase 3: Review and Test

#### 3.1 Code Quality

Review for:
- No duplicated code (DRY principle)
- Consistent error handling
- Full type coverage
- Clear tool descriptions

#### 3.2 Build and Test

**TypeScript:**
- Run `npm run build` to verify compilation
- Test with MCP Inspector: `npx @modelcontextprotocol/inspector`

**Python:**
- Verify syntax: `python -m py_compile your_server.py`
- Test with MCP Inspector

See language-specific guides for detailed testing approaches and quality checklists.

---

### Phase 4: Create Evaluations

After implementing your MCP server, create comprehensive evaluations to test its effectiveness.

**Load [✅ Evaluation Guide](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/evaluation.md>) for complete evaluation guidelines.**

#### 4.1 Understand Evaluation Purpose

Use evaluations to test whether LLMs can effectively use your MCP server to answer realistic, complex questions.

#### 4.2 Create 10 Evaluation Questions

To create effective evaluations, follow the process outlined in the evaluation guide:

1. **Tool Inspection**: List available tools and understand their capabilities
2. **Content Exploration**: Use READ-ONLY operations to explore available data
3. **Question Generation**: Create 10 complex, realistic questions
4. **Answer Verification**: Solve each question yourself to verify answers

#### 4.3 Evaluation Requirements

Ensure each question is:
- **Independent**: Not dependent on other questions
- **Read-only**: Only non-destructive operations required
- **Complex**: Requiring multiple tool calls and deep exploration
- **Realistic**: Based on real use cases humans would care about
- **Verifiable**: Single, clear answer that can be verified by string comparison
- **Stable**: Answer won't change over time

#### 4.4 Output Format

Create an XML file with this structure:

```xml
<evaluation>
  <qa_pair>
    <question>Find discussions about AI model launches with animal codenames. One model needed a specific safety designation that uses the format ASL-X. What number X was being determined for the model named after a spotted wild cat?</question>
    <answer>3</answer>
  </qa_pair>
<!-- More qa_pairs... -->
</evaluation>
```

---

# Reference Files

## 📚 Documentation Library

Load these resources as needed during development:

### Core MCP Documentation (Load First)
- **MCP Protocol**: Start with sitemap at `https://modelcontextprotocol.io/sitemap.xml`, then fetch specific pages with `.md` suffix
- [📋 MCP Best Practices](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/mcp_best_practices.md>) - Universal MCP guidelines including:
  - Server and tool naming conventions
  - Response format guidelines (JSON vs Markdown)
  - Pagination best practices
  - Transport selection (streamable HTTP vs stdio)
  - Security and error handling standards

### SDK Documentation (Load During Phase 1/2)
- **Python SDK**: Fetch from `https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md`
- **TypeScript SDK**: Fetch from `https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md`

### Language-Specific Implementation Guides (Load During Phase 2)
- [🐍 Python Implementation Guide](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/python_mcp_server.md>) - Complete Python/FastMCP guide with:
  - Server initialization patterns
  - Pydantic model examples
  - Tool registration with `@mcp.tool`
  - Complete working examples
  - Quality checklist

- [⚡ TypeScript Implementation Guide](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/node_mcp_server.md>) - Complete TypeScript guide with:
  - Project structure
  - Zod schema patterns
  - Tool registration with `server.registerTool`
  - Complete working examples
  - Quality checklist

### Evaluation Guide (Load During Phase 4)
- [✅ Evaluation Guide](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/mcp-builder/reference/evaluation.md>) - Complete evaluation creation guide with:
  - Question creation guidelines
  - Answer verification strategies
  - XML format specifications
  - Example questions and answers
  - Running an evaluation with the provided scripts


</details>
