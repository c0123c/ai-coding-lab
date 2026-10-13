# Continue MCP 功能概览与后续入口

功能概览和导航。简述 MCP 对外部系统、工具和数据源的连接用途，给出设置、示例等后续阅读入口；不把这份短概览当作完整配置教程。

- 来源：Continue
- 资料类型：概览导航
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-25](<https://github.com/continuedev/continue/commit/f14f6ef7b09a930c2fe61320ecc8cb24fc64b6e2>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：应结合 MCP 设置和具体服务指南阅读。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/mcp-tools.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/Continue MCP 功能概览与后续入口.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "MCP servers"
description: "Learn how to use Model Context Protocol (MCP) blocks in Continue to integrate external tools, connect databases, and extend your development environment."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

Model Context Protocol (MCP) 服务器允许 Continue 通过运行 MCP 服务器来连接到外部工具、系统和数据库。

这些服务器可以：

- **启用与外部工具和系统的集成**
- **创建可扩展接口**以实现自定义功能
- **支持与您的开发环境的复杂交互**
- **允许合作伙伴**贡献专门的功能
- **连接到数据库**以在开发过程中了解架构和数据模型

![MCP 服务器概述](<https://raw.githubusercontent.com/continuedev/continue/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/images/customize/images/mcp-blocks-overview-c9a104f9b586779c156f9cf34da197c2.png>)

## 了解有关 MCP 服务器的更多信息

在 [MCP 深入研究 ](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/Continue_Continue MCP 设置、传输与凭据管理.md>) 中了解更多信息，浏览 [ 示例 MCP 服务器配置 ](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/Continue_Continue 常见 MCP 服务配置示例.md>)，并查看 YAML 参考中的 [`mcpServers`](<https://docs.continue.dev/reference#mcpservers>) 以了解更多详细信息。


## 原文对照

<details>
<summary>展开完整原文</summary>

Model Context Protocol (MCP) servers let Continue connect to external tools, systems, and databases by running MCP servers.

These servers make it possible to:

- **Enable integration** with external tools and systems
- **Create extensible interfaces** for custom capabilities
- **Support complex interactions** with your development environment
- **Allow partners** to contribute specialized functionality
- **Connect to databases** to understand schema and data models during development

![MCP servers overview](<https://raw.githubusercontent.com/continuedev/continue/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/images/customize/images/mcp-blocks-overview-c9a104f9b586779c156f9cf34da197c2.png>)

## Learn More About MCP servers

Learn more in the [MCP deep dive](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/Continue_Continue MCP 设置、传输与凭据管理.md>), browse [example MCP server configurations](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/Continue_Continue 常见 MCP 服务配置示例.md>), and view [`mcpServers`](<https://docs.continue.dev/reference#mcpservers>) in the YAML Reference for more details.


</details>
