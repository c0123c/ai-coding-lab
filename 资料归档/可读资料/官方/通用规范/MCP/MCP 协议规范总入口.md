# MCP 协议规范总入口

概述基础协议、工具资源、扩展和信任原则，提供各能力的规范入口；具体要求按归档版本阅读。

- 来源：通用规范／MCP
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-11](<https://modelcontextprotocol.io/specification/2026-07-28>)；规范版本：[2026-07-28](<https://modelcontextprotocol.io/specification/2026-07-28.md>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://modelcontextprotocol.io/specification/2026-07-28.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/MCP/MCP 协议规范总入口.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://modelcontextprotocol.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 规格

<div id="enable-section-numbers" />

[Model Context Protocol](<https://modelcontextprotocol.io>) (MCP) 是一种开放协议，
 支持 LLM 应用程序与外部数据源和 
 工具之间的无缝集成。无论您是构建 AI- 驱动的 IDE、增强聊天界面，还是创建 
 自定义 AI 工作流程，MCP 都提供了一种标准化方法，将 LLM 与他们所需的上下文 
 连接起来。

该规范定义了权威的协议要求，基于
TypeScript 模式在
[schema.ts](<https://github.com/modelcontextprotocol/specification/blob/main/schema/2026-07-28/schema.ts>).

有关实施指南和示例，请访问
[modelcontextprotocol.io](<https://modelcontextprotocol.io>)。

关键字“MUST”、“MUSTNOT”、“REQUIRED”、“SHALL”、“SHALLNOT”、“SHOULD”、本文档中的“SHOULD
NOT”、“RECOMMENDED”、“NOT RECOMMENDED”、“MAY”和“OPTIONAL”将被解释为
，如[BCP 14](<https://datatracker.ietf.org/doc/html/bcp14>)
\[[RFC2119](<https://datatracker.ietf.org/doc/html/rfc2119>)]
\[[RFC8174](<https://datatracker.ietf.org/doc/html/rfc8174>)] 当且仅当它们 
 以全部大写形式出现时，如下所示。

## 概述

MCP 为应用程序提供了一种标准化方法：

* 与语言模型共享上下文信息
* 向 AI 系统公开工具和功能
* 构建可组合的集成和工作流程

该协议使用 [JSON-RPC](<https://www.jsonrpc.org/>) 2.0 消息在以下之间建立 
 通信：

* **主机**：发起连接的LLM应用程序
* **客户端**：主机应用程序内的连接器
* **服务器**：提供上下文和功能的服务

MCP 从 
[ 语言服务器协议 ](<https://microsoft.github.io/language-server-protocol/>) 中获得一些灵感，该协议 
 标准化了如何在整个 
 开发工具生态系统中添加对编程语言的支持。以类似的方式，MCP标准化了如何将附加上下文
和工具集成到AI应用程序的生态系统中。

## 关键细节

### 基础协议

* [JSON-RPC](<https://www.jsonrpc.org/>)消息格式
* 无状态、独立的请求
* 每个请求的能力协商

### 特点

服务器向客户端提供以下任何功能：

* **资源**：上下文和数据，供用户或AI模型使用
* **提示**：用户的模板化消息和工作流程
* **工具**：AI模型执行的函数

客户端可以向服务器提供以下功能：

* **Elicitation**：服务器发起的用户附加信息请求

### 附加实用程序

* 配置
* 进度追踪
* 取消
* 错误报告

### 扩展

除了核心协议之外，MCP 定义了可选的 [ 扩展 ](<https://modelcontextprotocol.io/extensions/overview>)
，用于添加模块化、专用或实验功能。扩展
始终是选择加入的，并且需要客户端和服务器的显式支持，在初始化期间协商
。值得注意的扩展包括：

* **[任务](<https://modelcontextprotocol.io/extensions/tasks/overview>)**：长时间运行的异步执行
操作，具有轮询、飞行中输入和耐用手柄
* **[比MCP](<https://modelcontextprotocol.io/community/working-groups/skills-over-mcp>)**的技能：丰富、结构化
通过 MCP 发现和使用的 agent 工作流程说明
* **[MCP 应用程序](<https://modelcontextprotocol.io/extensions/apps/overview>)**：交互式UI 元素（图表、表单、
视频播放器）在对话中内联呈现

## 安全与信任与安全

Model Context Protocol 通过任意数据访问 
 和代码执行路径实现强大的功能。这种能力带来了重要的安全和信任
考虑因素，所有实施者都必须仔细解决。

### 关键原则

1. **用户同意和控制**
   * 用户必须明确同意并理解所有数据访问和操作
   * 用户必须保留对共享哪些数据以及采取哪些操作的控制权
   * 实施者应提供清晰的 UI 来审核和授权活动

2. **数据隐私**
   * 主机在向服务器公开用户数据之前必须获得明确的用户同意
   * 未经用户同意，主机不得将资源数据传输到其他地方
   * 应通过适当的访问控制来保护用户数据

3. **工具安全**
   * 工具代表任意代码执行，必须以适当的方式对待
小心。
     * 特别是，工具行为的描述（例如注释）应该
除非从受信任的服务器获取，否则被视为不受信任。
   * 主机在调用任何工具之前必须获得明确的用户同意
   * 用户在授权使用之前应了解每个工具的用途

### 实施指南

虽然MCP本身无法在协议级别强制执行这些安全原则，但
实现了**SHOULD**：

1. 在其应用程序中构建强大的同意和授权流程
2. 提供清晰的安全影响文档
3. 实施适当的访问控制和数据保护
4. 在集成中遵循安全最佳实践
5. 在功能设计中考虑隐私影响

## 了解更多

探索每个协议组件的详细规范：



  

#### [架构](<https://modelcontextprotocol.io/specification/2026-07-28/architecture>)


  

#### [基础协议](<https://modelcontextprotocol.io/specification/2026-07-28/basic>)


  

#### [服务器功能](<https://modelcontextprotocol.io/specification/2026-07-28/server>)


  

#### [客户端功能](<https://modelcontextprotocol.io/specification/2026-07-28/client>)


  

#### [贡献](<https://modelcontextprotocol.io/community/contributing>)


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://modelcontextprotocol.io/llms.txt
> Use this file to discover all available pages before exploring further.

# Specification

<div id="enable-section-numbers" />

[Model Context Protocol](<https://modelcontextprotocol.io>) (MCP) is an open protocol that
enables seamless integration between LLM applications and external data sources and
tools. Whether you're building an AI-powered IDE, enhancing a chat interface, or creating
custom AI workflows, MCP provides a standardized way to connect LLMs with the context
they need.

This specification defines the authoritative protocol requirements, based on the
TypeScript schema in
[schema.ts](<https://github.com/modelcontextprotocol/specification/blob/main/schema/2026-07-28/schema.ts>).

For implementation guides and examples, visit
[modelcontextprotocol.io](<https://modelcontextprotocol.io>).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD
NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [BCP 14](<https://datatracker.ietf.org/doc/html/bcp14>)
\[[RFC2119](<https://datatracker.ietf.org/doc/html/rfc2119>)]
\[[RFC8174](<https://datatracker.ietf.org/doc/html/rfc8174>)] when, and only when, they
appear in all capitals, as shown here.

## Overview

MCP provides a standardized way for applications to:

* Share contextual information with language models
* Expose tools and capabilities to AI systems
* Build composable integrations and workflows

The protocol uses [JSON-RPC](<https://www.jsonrpc.org/>) 2.0 messages to establish
communication between:

* **Hosts**: LLM applications that initiate connections
* **Clients**: Connectors within the host application
* **Servers**: Services that provide context and capabilities

MCP takes some inspiration from the
[Language Server Protocol](<https://microsoft.github.io/language-server-protocol/>), which
standardizes how to add support for programming languages across a whole ecosystem of
development tools. In a similar way, MCP standardizes how to integrate additional context
and tools into the ecosystem of AI applications.

## Key Details

### Base Protocol

* [JSON-RPC](<https://www.jsonrpc.org/>) message format
* Stateless, self-contained requests
* Per-request capability negotiation

### Features

Servers offer any of the following features to clients:

* **Resources**: Context and data, for the user or the AI model to use
* **Prompts**: Templated messages and workflows for users
* **Tools**: Functions for the AI model to execute

Clients may offer the following features to servers:

* **Elicitation**: Server-initiated requests for additional information from users

### Additional Utilities

* Configuration
* Progress tracking
* Cancellation
* Error reporting

### Extensions

Beyond the core protocol, MCP defines optional [extensions](<https://modelcontextprotocol.io/extensions/overview>)
that add modular, specialized, or experimental functionality. Extensions
are always opt-in and require explicit support from both client and server, negotiated
during initialization. Notable extensions include:

* **[Tasks](<https://modelcontextprotocol.io/extensions/tasks/overview>)**: Asynchronous execution of long-running
  operations, with polling, mid-flight input, and durable handles
* **[Skills over MCP](<https://modelcontextprotocol.io/community/working-groups/skills-over-mcp>)**: Rich, structured
  instructions for agent workflows, discovered and consumed through MCP
* **[MCP Apps](<https://modelcontextprotocol.io/extensions/apps/overview>)**: Interactive UI elements (charts, forms,
  video players) rendered inline within conversations

## Security and Trust & Safety

The Model Context Protocol enables powerful capabilities through arbitrary data access
and code execution paths. With this power comes important security and trust
considerations that all implementors must carefully address.

### Key Principles

1. **User Consent and Control**
   * Users must explicitly consent to and understand all data access and operations
   * Users must retain control over what data is shared and what actions are taken
   * Implementors should provide clear UIs for reviewing and authorizing activities

2. **Data Privacy**
   * Hosts must obtain explicit user consent before exposing user data to servers
   * Hosts must not transmit resource data elsewhere without user consent
   * User data should be protected with appropriate access controls

3. **Tool Safety**
   * Tools represent arbitrary code execution and must be treated with appropriate
     caution.
     * In particular, descriptions of tool behavior such as annotations should be
       considered untrusted, unless obtained from a trusted server.
   * Hosts must obtain explicit user consent before invoking any tool
   * Users should understand what each tool does before authorizing its use

### Implementation Guidelines

While MCP itself cannot enforce these security principles at the protocol level,
implementors **SHOULD**:

1. Build robust consent and authorization flows into their applications
2. Provide clear documentation of security implications
3. Implement appropriate access controls and data protections
4. Follow security best practices in their integrations
5. Consider privacy implications in their feature designs

## Learn More

Explore the detailed specification for each protocol component:



  

#### [Architecture](<https://modelcontextprotocol.io/specification/2026-07-28/architecture>)


  

#### [Base Protocol](<https://modelcontextprotocol.io/specification/2026-07-28/basic>)


  

#### [Server Features](<https://modelcontextprotocol.io/specification/2026-07-28/server>)


  

#### [Client Features](<https://modelcontextprotocol.io/specification/2026-07-28/client>)


  

#### [Contributing](<https://modelcontextprotocol.io/community/contributing>)


</details>
