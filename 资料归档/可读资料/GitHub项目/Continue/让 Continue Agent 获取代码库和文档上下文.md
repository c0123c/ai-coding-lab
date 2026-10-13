# 让 Continue Agent 获取代码库和文档上下文

产品指南。说明内置代码探索、项目规则、外部仓库 CLI、文档链接及 MCP/RAG，介绍从旧 @Codebase、@Docs 上下文方式迁移到 Agent 工具。

- 来源：Continue
- 资料类型：产品指南
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-01-30](<https://github.com/continuedev/continue/commit/03e5fc3b92d7593b74527dbe4341ccac4e88d52c>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：公共与内部资料的访问条件不同，迁移依据以所用版本为准。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/codebase-documentation-awareness.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/让 Continue Agent 获取代码库和文档上下文.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: How to Make Agent mode Aware of Codebases and Documentation
description: Learn how to give your Agent mode access to codebases and documentation for more context-aware assistance
keywords: [agent, codebase, documentation, MCP, context, RAG, tools]
sidebarTitle: Codebase and Documentation Awareness
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

Agent 模式在了解项目上下文时效果最佳。本指南向您展示如何授予 agent 模式对代码库和文档的访问权限，使其更加有用和准确。

## 让 Agent 模式了解您的开放代码库

当agent模式了解您当前的代码库时，它可以提供更多相关的建议和答案。

### 让 Agent 模式使用工具探索代码库

Agent 模式可以使用内置工具来导航和理解您的代码：

1. **文件探索工具**：agent可以读取文件、搜索模式并了解项目结构
2. **代码搜索**：使用搜索查找相关代码片段
3. **Git 集成**：访问提交历史记录并了解代码演变

### 创建规则来帮助 Agent 了解您的代码库

规则指导 agent 模式的行为和理解。将 markdown 文件放置在项目的 `.continue/rules` 中以提供上下文：

```markdown
# Project Architecture

This is a React application with:

- Components in `/src/components`
- API routes in `/src/api`
- State management using Redux in `/src/store`

## Coding Standards

- Use TypeScript for all new files
- Follow the existing naming conventions
- Write tests for all new features
```

**提示：**

将规则文件放置在项目层次结构的不同级别，以在 
 触发时确定范围

了解有关 [ 规则配置 ](<https://docs.continue.dev/customize/deep-dives/rules>) 的更多信息。

## 使 Agent 模式了解其他代码库

有时您需要 agent 模式来理解当前项目之外的代码。

### 公共代码库

对于开源项目和公共存储库，您有多种选择：

#### 带有超链接的规则

创建指向外部代码库的规则：

```markdown
# External Dependencies

Our authentication system is based on:

- [Auth.js documentation](https://authjs.dev/)
- [Example implementation](https://github.com/nextauthjs/next-auth-example)

When implementing auth features, reference these patterns.
```

#### GitHub 和 GitLab CLI

为 agent 模式启用 `gh` 或 `glab` CLI 访问权限以与存储库交互。

添加规则来指导CLI使用：

```markdown
# Repository Access

You can use the `gh` CLI to:

- Search for issues: `gh issue list --repo owner/repo`
- View pull requests: `gh pr list --repo owner/repo`
- Clone repositories: `gh repo clone owner/repo`
```

#### DeepWiki MCP

[DeepWiki MCP](<https://continue.dev/deepwiki/deepwiki-mcp>) 让 agent 模式探索任何公共 GitHub 存储库。

配置完成后，agent 模式可以探索以下存储库：

- “探索 React 存储库结构”
- “了解如何在 NextAuth.js 中实现身份验证”

### 内部代码库

对于私有和内部存储库，您需要额外的设置：

#### 自定义 MCP 服务器

创建一个可以访问您的内部存储库的 MCP 服务器。

#### 自定义代码 RAG

为了通过非常大的内部代码库实现更快的检索并降低成本，请考虑实施 [ 自定义代码 RAG](<https://docs.continue.dev/guides/custom-code-rag>) 系统。这是一种高级方法，需要更多设置，但可以大规模提供性能优势。

## 让Agent模式了解相关文档

文档为 agent 模式提供了理解 API、框架和最佳实践的重要上下文。

### 公共文档

#### 带有文档链接的规则

引导agent模式相关文档：

```markdown
# Documentation Resources

For framework-specific questions, refer to:

- React: https://react.dev/reference/react
- Next.js: https://nextjs.org/docs
- Tailwind CSS: https://tailwindcss.com/docs

Always cite documentation when explaining concepts.
```

#### 上下文7 MCP

[Context7 MCP](<https://continue.dev/upstash/context7-mcp>) 启用 agent 模式从公共文档中搜索和检索信息：

Agent 模式可以回答如下问题：

- “如何使用 React 挂钩？”
- “Tailwind CSS 动画的语法是什么？”

### 内部文档

对于私人文档和 wiki：

#### 带有内部链接的规则

创建引用内部资源的规则：

```markdown
# Internal Documentation

Our team documentation is available at:

- API Documentation: https://internal.docs/api
- Architecture Guide: https://internal.docs/architecture
- Deployment Process: https://internal.docs/deployment

Always follow our internal standards when suggesting code.
```

#### 自定义 MCP 文档服务器

创建一个用于访问您的内部文档的 MCP 服务器。

## 从已弃用的上下文提供程序迁移

如果您之前使用过 `@Codebase` 或 `@Docs` 上下文提供程序，请按照以下步骤迁移到新方法：

### 从@Codebase迁移

`@Codebase` 上下文提供程序已被弃用。相反：

1. **使用内置工具**：Agent模式现在可以使用文件探索和搜索工具来了解您的代码库
2. **添加规则**：创建`.continue/rules`文件以提供有关项目结构的上下文
3. **使用 MCP 服务器 **：对于外部代码库，使用 DeepWiki MCP 或自定义 MCP 服务器

### 从@Docs迁移

`@Docs` 上下文提供程序已被弃用。相反：

1. **使用 Context7 MCP**：对于公共文档，Context7 MCP 提供类似的功能
2. **在规则中添加文档链接**：创建引用文档 URL 的规则
3. **使用自定义 MCP 服务器**：对于内部文档，创建一个可以访问您的文档的 MCP 服务器

新方法提供了与 Continue 的 Agent 模式功能的更好集成以及更智能的上下文选择。

## 下一步

- 了解有关 [MCP 服务器的更多信息](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue 文档 MCP 检索服务的配置和使用.md>)
- 探索[规则配置](<https://docs.continue.dev/customize/deep-dives/rules>)
  - 设置具有特定知识领域的[其他自定义配置](<https://docs.continue.dev/guides/understanding-configs>)


## 原文对照

<details>
<summary>展开完整原文</summary>

Agent mode works best when it understands the context of your project. This guide shows you how to give agent mode access to codebases and documentation, making it more helpful and accurate.

## Make Agent Mode Aware of Your Open Codebase

When agent mode understands your current codebase, it can provide more relevant suggestions and answers.

### Let Agent Mode Explore the Codebase Using Tools

Agent mode can use built-in tools to navigate and understand your code:

1. **File exploration tools**: The agent can read files, search for patterns, and understand project structure
2. **Code search**: Use search to find relevant code snippets
3. **Git integration**: Access commit history and understand code evolution

### Create Rules to Help the Agent Understand Your Codebase

Rules guide agent mode's behavior and understanding. Place markdown files in `.continue/rules` in your project to provide context:

```markdown
# Project Architecture

This is a React application with:

- Components in `/src/components`
- API routes in `/src/api`
- State management using Redux in `/src/store`

## Coding Standards

- Use TypeScript for all new files
- Follow the existing naming conventions
- Write tests for all new features
```

**提示：**

Place rules files at different levels of your project hierarchy to scope when
they trigger

Learn more about [rules configuration](<https://docs.continue.dev/customize/deep-dives/rules>).

## Make Agent Mode Aware of Other Codebases

Sometimes you need agent mode to understand code beyond your current project.

### Public Codebases

For open-source projects and public repositories, you have several options:

#### Rules with Hyperlinks

Create rules that point to external codebases:

```markdown
# External Dependencies

Our authentication system is based on:

- [Auth.js documentation](https://authjs.dev/)
- [Example implementation](https://github.com/nextauthjs/next-auth-example)

When implementing auth features, reference these patterns.
```

#### GitHub and GitLab CLIs

Enable `gh` or `glab` CLI access for agent mode to interact with repositories.

Add rules to guide CLI usage:

```markdown
# Repository Access

You can use the `gh` CLI to:

- Search for issues: `gh issue list --repo owner/repo`
- View pull requests: `gh pr list --repo owner/repo`
- Clone repositories: `gh repo clone owner/repo`
```

#### DeepWiki MCP

[DeepWiki MCP](<https://continue.dev/deepwiki/deepwiki-mcp>) lets agent mode explore any public GitHub repository.

Once configured, agent mode can explore repositories like:

- "Explore the React repository structure"
- "Find how authentication is implemented in NextAuth.js"

### Internal Codebases

For private and internal repositories, you need additional setup:

#### Custom MCP Servers

Create an MCP server that has access to your internal repositories.

#### Custom Code RAG

For faster retrieval and lower costs with very large internal codebases, consider implementing a [custom code RAG](<https://docs.continue.dev/guides/custom-code-rag>) system. This is an advanced approach that requires more setup but can provide performance benefits at scale.

## Make Agent Mode Aware of Relevant Documentation

Documentation provides crucial context for agent mode to understand APIs, frameworks, and best practices.

### Public Documentation

#### Rules with Documentation Links

Guide agent mode to relevant documentation:

```markdown
# Documentation Resources

For framework-specific questions, refer to:

- React: https://react.dev/reference/react
- Next.js: https://nextjs.org/docs
- Tailwind CSS: https://tailwindcss.com/docs

Always cite documentation when explaining concepts.
```

#### Context7 MCP

[Context7 MCP](<https://continue.dev/upstash/context7-mcp>) enables agent mode to search and retrieve information from public documentation:

Agent mode can then answer questions like:

- "How do I use React hooks?"
- "What's the syntax for Tailwind CSS animations?"

### Internal Documentation

For private documentation and wikis:

#### Rules with Internal Links

Create rules that reference internal resources:

```markdown
# Internal Documentation

Our team documentation is available at:

- API Documentation: https://internal.docs/api
- Architecture Guide: https://internal.docs/architecture
- Deployment Process: https://internal.docs/deployment

Always follow our internal standards when suggesting code.
```

#### Custom MCP Servers for Docs

Create an MCP server that accesses your internal documentation.

## Migrating from Deprecated Context Providers

If you were previously using the `@Codebase` or `@Docs` context providers, here's how to migrate to the new approach:

### Migrating from @Codebase

The `@Codebase` context provider has been deprecated. Instead:

1. **Use built-in tools**: Agent mode can now use file exploration and search tools to understand your codebase
2. **Add rules**: Create `.continue/rules` files to provide context about your project structure
3. **Use MCP servers**: For external codebases, use DeepWiki MCP or custom MCP servers

### Migrating from @Docs

The `@Docs` context provider has been deprecated. Instead:

1. **Use Context7 MCP**: For public documentation, Context7 MCP provides similar functionality
2. **Add documentation links in rules**: Create rules that reference documentation URLs
3. **Use custom MCP servers**: For internal documentation, create an MCP server with access to your docs

The new approach provides better integration with Continue's Agent mode features and more intelligent context selection.

## Next Steps

- Learn more about [MCP servers](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/Continue_Continue 文档 MCP 检索服务的配置和使用.md>)
- Explore [rules configuration](<https://docs.continue.dev/customize/deep-dives/rules>)
  - Set up [other custom configurations](<https://docs.continue.dev/guides/understanding-configs>) with specific knowledge domains


</details>
