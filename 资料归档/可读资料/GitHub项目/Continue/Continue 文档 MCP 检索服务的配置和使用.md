# Continue 文档 MCP 检索服务的配置和使用

产品指南。说明在项目中配置 Continue Docs MCP、启用 Agent 模式、检索模型和上下文设置，以及服务加载和搜索无结果的排查。

- 来源：Continue
- 资料类型：产品指南
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-01-15](<https://github.com/continuedev/continue/commit/4762623549d4e44e7c8c8aa34edb0cd2ab7d12e4>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：针对 Continue 文档搜索服务，不能推断任意资料库都可直接检索。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/reference/continue-mcp.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/Continue 文档 MCP 检索服务的配置和使用.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Continue Documentation MCP Server
description: Set up an MCP server to search Continue documentation
keywords: [mcp, documentation, search, mintlify, reference]
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

Continue 文档 MCP 服务器允许您直接在 agent 对话中搜索和检索 Continue 文档中的信息。

## 设置

### 配置Continue

1. 在工作区的顶层创建一个名为 `.continue/mcpServers` 的文件夹
2. 将名为 `continue-docs-mcp.yaml` 的文件添加到此文件夹
3. 写入以下内容并保存：

**代码示例：** `.continue/mcpServers/continue-docs-mcp.yaml`

```yaml
name: Continue Documentation MCP
version: 0.0.1
schema: v1
mcpServers:
  - uses: continuedev/continue-docs-mcp
```

### 启用Agent模式

MCP服务器仅在agent模式下工作。测试前请务必将Continue切换至agent模式。

## 使用示例

配置完成后，您可以使用MCP服务器搜索Continue文档：

### 型号配置帮助

```
How do I add Claude 4 Sonnet as a model from Bedrock in Continue?
```

### 上下文提供者

```
What context providers are available in Continue?
```

### 定制化

```
How do I add custom rules to my configuration in Continue?
```

## 故障排除

### MCP 服务器未加载

1. **检查配置**：确保您的YAML配置使用正确的`uses`字段和`continuedev/continue-docs-mcp`
2. **检查agent模式**：MCP服务器仅在agent模式下工作
3. **重新启动Continue**：尝试重新启动Continue扩展

### 没有搜索结果

1. **验证连接**：MCP服务器需要访问互联网才能搜索文档
2. **检查查询格式**：尝试重新表述您的搜索查询
3. **使用已知主题进行测试**：搜索记录良好的功能，例如“模型配置”

## 相关文档

- [MCP 概述](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/reference/Continue_Continue MCP 设置、传输与凭据管理.md>)
- [Agent 模式](<https://docs.continue.dev/ide-extensions/agent/quick-start>)
- [配置](<https://docs.continue.dev/customize/overview>)


## 原文对照

<details>
<summary>展开完整原文</summary>

The Continue Documentation MCP Server allows you to search and retrieve information from the Continue documentation directly within your agent conversations.

## Set Up

### Configure Continue

1. Create a folder called `.continue/mcpServers` at the top level of your workspace
2. Add a file called `continue-docs-mcp.yaml` to this folder
3. Write the following contents and save:

**代码示例：** `.continue/mcpServers/continue-docs-mcp.yaml`

```yaml
name: Continue Documentation MCP
version: 0.0.1
schema: v1
mcpServers:
  - uses: continuedev/continue-docs-mcp
```

### Enable Agent Mode

MCP servers only work in agent mode. Make sure to switch to agent mode in Continue before testing.

## Usage Examples

Once configured, you can use the MCP server to search Continue documentation:

### Model Configuration Help

```
How do I add Claude 4 Sonnet as a model from Bedrock in Continue?
```

### Context Providers

```
What context providers are available in Continue?
```

### Customization

```
How do I add custom rules to my configuration in Continue?
```

## Troubleshooting

### MCP Server Not Loading

1. **Check configuration**: Ensure your YAML configuration uses the correct `uses` field with `continuedev/continue-docs-mcp`
2. **Check agent mode**: MCP servers only work in agent mode
3. **Restart Continue**: Try restarting the Continue extension

### No Search Results

1. **Verify connection**: The MCP server needs internet access to search the documentation
2. **Check query format**: Try rephrasing your search query
3. **Test with known topics**: Search for well-documented features like "model configuration"

## Related Documentation

- [MCP Overview](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/reference/Continue_Continue MCP 设置、传输与凭据管理.md>)
- [Agent Mode](<https://docs.continue.dev/ide-extensions/agent/quick-start>)
- [Configuration](<https://docs.continue.dev/customize/overview>)


</details>
