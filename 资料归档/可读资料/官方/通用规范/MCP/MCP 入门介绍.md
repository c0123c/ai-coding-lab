# MCP 入门介绍

解释 AI 应用如何通过统一接口连接数据、工具和工作流，介绍用途、生态及客户端和服务端开发入口。

- 来源：通用规范／MCP
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-11](<https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/MCP/MCP 入门介绍.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://modelcontextprotocol.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# Model Context Protocol（MCP）是什么？

MCP (Model Context Protocol) 是用于将 AI 应用程序连接到外部系统的开源标准。

使用 MCP、Claude 或 ChatGPT 等 AI 应用程序可以连接到数据源（例如本地文件、数据库）、工具（例如搜索引擎、计算器）和工作流程（例如专门提示），使它们能够访问关键信息并执行任务。

将 MCP 想象为 AI 应用程序的 USB-C 端口。正如USB-C提供了一种连接电子设备的标准化方法，MCP提供了一种将AI应用程序连接到外部系统的标准化方法。



  

![原文图片](<https://mintcdn.com/mcp/bEUxYpZqie0DsluH/images/mcp-simple-diagram.png?fit=max&auto=format&n=bEUxYpZqie0DsluH&q=85&s=35268aa0ad50b8c385913810e7604550>)


## MCP可以启用什么？

* Agents可以访问您的Google日历和Notion，充当更加个性化的AI助手。
* Claude Code 可以使用 Figma 设计生成整个 Web 应用程序。
* 企业聊天机器人可以连接到组织中的多个数据库，使用户能够使用聊天来分析数据。
* AI 模型可以在 Blender 上创建 3D 设计并使用 3D 打印机打印出来。

## 为什么 MCP 很重要？

根据您在生态系统中的位置，MCP 可以带来一系列好处。

* **开发人员**：MCP在构建或集成AI应用程序或agent时减少了开发时间和复杂性。
* **AI 应用程序或 agents**：MCP 使他们能够访问数据源、工具和应用程序的生态系统，从而增强他们的功能并改善最终用户体验。
* **最终用户**：MCP导致功能更强大的AI应用程序或agents可以访问用户数据并在必要时代表用户采取操作。

## 广泛的生态系统支持

MCP 是一种跨广泛的客户端和服务器支持的开放协议。 AI 助手如 [Claude](<https://claude.com/docs/connectors/building>) 和 [ChatGPT](<https://developers.openai.com/api/docs/mcp/>)，开发工具如 [Visual Studio Code](<https://code.visualstudio.com/docs/copilot/chat/mcp-servers>)、[Cursor](<https://cursor.com/docs/context/mcp>)、[MCPJam](<https://docs.mcpjam.com/getting-started>) 以及许多其他工具都支持 MCP —使其易于构建一次并随处集成。

## 开始建造



  

#### [构建服务器](<https://modelcontextprotocol.io/docs/2026-07-28/develop/build-server>)

创建 MCP 服务器来公开您的数据和工具
  


  

#### [建立客户](<https://modelcontextprotocol.io/docs/2026-07-28/develop/build-client>)

开发连接到 MCP 服务器的应用程序
  


  

#### [构建MCP应用程序](<https://modelcontextprotocol.io/extensions/apps/overview>)

构建在 AI 客户端内运行的交互式应用程序
  


## 了解更多



  

#### [架构](<https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture>)

了解MCP的核心概念和架构
  


  

#### [安全](<https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices>)

了解 MCP 的安全注意事项和最佳实践
  


## 社区



  

#### [贡献](<https://modelcontextprotocol.io/community/contributing>)

了解如何参与并为 MCP 做出贡献
  


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://modelcontextprotocol.io/llms.txt
> Use this file to discover all available pages before exploring further.

# What is the Model Context Protocol (MCP)?

MCP (Model Context Protocol) is an open-source standard for connecting AI applications to external systems.

Using MCP, AI applications like Claude or ChatGPT can connect to data sources (e.g. local files, databases), tools (e.g. search engines, calculators) and workflows (e.g. specialized prompts)—enabling them to access key information and perform tasks.

Think of MCP like a USB-C port for AI applications. Just as USB-C provides a standardized way to connect electronic devices, MCP provides a standardized way to connect AI applications to external systems.



  

![原文图片](<https://mintcdn.com/mcp/bEUxYpZqie0DsluH/images/mcp-simple-diagram.png?fit=max&auto=format&n=bEUxYpZqie0DsluH&q=85&s=35268aa0ad50b8c385913810e7604550>)


## What can MCP enable?

* Agents can access your Google Calendar and Notion, acting as a more personalized AI assistant.
* Claude Code can generate an entire web app using a Figma design.
* Enterprise chatbots can connect to multiple databases across an organization, empowering users to analyze data using chat.
* AI models can create 3D designs on Blender and print them out using a 3D printer.

## Why does MCP matter?

Depending on where you sit in the ecosystem, MCP can have a range of benefits.

* **Developers**: MCP reduces development time and complexity when building, or integrating with, an AI application or agent.
* **AI applications or agents**: MCP gives them access to an ecosystem of data sources, tools and apps, which enhances their capabilities and improves the end-user experience.
* **End-users**: MCP results in more capable AI applications or agents that can access user data and take actions on the user's behalf when necessary.

## Broad ecosystem support

MCP is an open protocol supported across a wide range of clients and servers. AI assistants like [Claude](<https://claude.com/docs/connectors/building>) and [ChatGPT](<https://developers.openai.com/api/docs/mcp/>), development tools like [Visual Studio Code](<https://code.visualstudio.com/docs/copilot/chat/mcp-servers>), [Cursor](<https://cursor.com/docs/context/mcp>), [MCPJam](<https://docs.mcpjam.com/getting-started>), and many others all support MCP — making it easy to build once and integrate everywhere.

## Start Building



  

#### [Build servers](<https://modelcontextprotocol.io/docs/2026-07-28/develop/build-server>)

    Create MCP servers to expose your data and tools
  


  

#### [Build clients](<https://modelcontextprotocol.io/docs/2026-07-28/develop/build-client>)

    Develop applications that connect to MCP servers
  


  

#### [Build MCP Apps](<https://modelcontextprotocol.io/extensions/apps/overview>)

    Build interactive apps that run inside AI clients
  


## Learn more



  

#### [Architecture](<https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture>)

    Learn the core concepts and architecture of MCP
  


  

#### [Security](<https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices>)

    Understand the security considerations and best practices for MCP
  


## Community



  

#### [Contributing](<https://modelcontextprotocol.io/community/contributing>)

    Learn how to get involved and contribute to MCP
  


</details>
