# 云端 Agent 与 MCP

介绍默认 MCP 服务、仓库接入和自定义 Agent 配置，并给出工具选择与使用建议。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方文件提交（截至抓取）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/云端 Agent 与 MCP.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Model Context Protocol (MCP) and GitHub Copilot cloud agent
shortTitle: MCP and {% data variables.copilot.copilot_cloud_agent_short %}
allowTitleToDifferFromFilename: true
intro: Find out about using the Model Context Protocol (MCP) with {% data variables.copilot.copilot_cloud_agent %}.
versions:
  feature: copilot
contentType: concepts
redirect_from:
  - /copilot/concepts/agents/coding-agent/mcp-and-coding-agent
  - /copilot/concepts/coding-agent/mcp-and-coding-agent
category: 
  - Integrate Copilot with your tools
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 概述

{% data reusables.copilot.cloud-agent.mcp-brief-intro %}

agent可以使用本地和远程MCP服务器提供的工具。某些 MCP 服务器默认配置为提供最佳的入门体验。

有关MCP的更多信息，请参阅[官方MCP文档](<https://modelcontextprotocol.io/introduction>)。有关当前可用的一些 MCP 服务器的信息，请参阅 [ MCP 服务器存储库 ](<https://github.com/modelcontextprotocol/servers/tree/main>)。

{% data reusables.copilot.mcp.repo-mcp-limitations %}

## 默认 MCP 服务器

为 {% data variables.copilot.copilot_cloud_agent %} 自动配置以下 MCP 服务器：

* **{% data variables.product.github %}**：{% data variables.product.github %} MCP 服务器允许 {% data variables.product.prodname_copilot_short %} 访问 {% data variables.product.github %} 数据，例如问题和拉取请求。要了解更多信息，请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>)。
  * 默认情况下，{% data variables.product.github %} MCP 服务器使用特殊范围的令牌连接到 {% data variables.product.github %}，该令牌仅对当前存储库具有只读访问权限。您可以对其进行自定义以使用具有更广泛访问权限的不同令牌。更多详情请参见[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers#customizing-the-built-in-github-mcp-server>)。

* **Playwright**：[Playwright MCP服务器](<https://github.com/microsoft/playwright-mcp>)为{% data variables.product.prodname_copilot_short %}提供网页访问权限，包括阅读、交互和截屏的能力。
  * 默认情况下，Playwright MCP 服务器只能访问 {% data variables.product.prodname_copilot_short %} 自己环境中托管的 Web 资源，可在 `localhost` 或 `127.0.0.1` 上访问。

## 在存储库中设置 MCP 服务器

存储库管理员可以配置 MCP 服务器以在该存储库中使用。这是通过 {% data variables.product.github %} 上的存储库设置中的 JSON- 格式的配置来完成的。

> [!NOTE]
> {% data variables.product.github %} 上的存储库 MCP 配置适用于 {% data variables.copilot.copilot_cloud_agent %} 和 {% data variables.copilot.copilot_code-review_short %}。在存储库 MCP 设置中配置的 MCP 服务器可用于 agents。

一旦将MCP服务器配置为在存储库中使用，配置中指定的工具将在每个分配的任务期间可供{% data variables.copilot.copilot_cloud_agent %}使用。

{% data variables.product.prodname_copilot_short %}将自主使用可用的工具，并且在使用前不会征求批准。

有关如何在存储库中设置 MCP 服务器的详细信息，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)。

## MCP {% data variables.copilot.custom_agents_short %} 的服务器

您还可以为{% data variables.copilot.custom_agents_short %}配置MCP服务器。

{% data variables.copilot.custom_agents_short %} 中配置的 MCP 服务器仅适用于特定的 agent，并遵循与其他 MCP 配置相同的处理顺序，其中 {% data variables.copilot.copilot_custom_agent_short %} MCP 设置在默认服务器之后但在存储库级别配置之前处理。

有关为 {% data variables.copilot.custom_agents_short %} 配置 MCP 服务器的更多信息，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/custom-agents-configuration#mcp-server-configuration-details>)。

## 最佳实践

* 启用第三方 MCP 服务器的使用可能会影响 agent 的性能和输出的质量。彻底检查第三方 MCP 服务器并确保其满足您组织的要求。

* 默认情况下，{% data variables.copilot.copilot_cloud_agent %}无权写入MCP服务器工具。然而，一些MCP服务器确实包含此类工具。请务必查看您要使用的 MCP 服务器中可用的工具。仅使用必要的工具更新 MCP 配置中的 `tools` 字段。

* 在保存配置之前，请仔细检查已配置的 MCP 服务器，以确保配置了正确的服务器以供使用。


## 原文对照

<details>
<summary>展开完整原文</summary>

## Overview

{% data reusables.copilot.cloud-agent.mcp-brief-intro %}

The agent can use tools provided by local and remote MCP servers. Some MCP servers are configured by default to provide the best experience for getting started.

For more information on MCP, see [the official MCP documentation](<https://modelcontextprotocol.io/introduction>). For information on some of the currently available MCP servers, see [the MCP servers repository](<https://github.com/modelcontextprotocol/servers/tree/main>).

{% data reusables.copilot.mcp.repo-mcp-limitations %}

## Default MCP servers

The following MCP servers are configured automatically for {% data variables.copilot.copilot_cloud_agent %}:

* **{% data variables.product.github %}**: The {% data variables.product.github %} MCP server gives {% data variables.product.prodname_copilot_short %} access to {% data variables.product.github %} data like issues and pull requests. To learn more, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>).
  * By default, the {% data variables.product.github %} MCP server connects to {% data variables.product.github %} using a specially scoped token that only has read-only access to the current repository. You can customize it to use a different token with broader access. For more details, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers#customizing-the-built-in-github-mcp-server>).

* **Playwright**: The [Playwright MCP server](<https://github.com/microsoft/playwright-mcp>) gives {% data variables.product.prodname_copilot_short %} access to web pages, including the ability to read, interact and take screenshots.
  * By default, the Playwright MCP server is only able to access web resources hosted within {% data variables.product.prodname_copilot_short %}'s own environment, accessible on `localhost` or `127.0.0.1`.

## Setting up MCP servers in a repository

Repository administrators can configure MCP servers for use within that repository. This is done via a JSON-formatted configuration in repository settings on {% data variables.product.github %}.

> [!NOTE]
> Repository MCP configuration on {% data variables.product.github %} applies to both {% data variables.copilot.copilot_cloud_agent %} and {% data variables.copilot.copilot_code-review_short %}. MCP servers configured in repository MCP settings are available to both agents.

Once MCP servers are configured for use within a repository, the tools specified in the configuration will be available to {% data variables.copilot.copilot_cloud_agent %} during each assigned task.

{% data variables.product.prodname_copilot_short %} will use available tools autonomously, and will not ask for approval before use.

For details of how to set up MCP servers in a repository, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>).

## MCP servers for {% data variables.copilot.custom_agents_short %}

You can also configure MCP servers for {% data variables.copilot.custom_agents_short %}.

MCP servers configured in {% data variables.copilot.custom_agents_short %} are available only to that specific agent and follow the same processing order as other MCP configurations, with {% data variables.copilot.copilot_custom_agent_short %} MCP settings processed after default servers but before repository-level configurations.

For more information on configuring MCP servers for {% data variables.copilot.custom_agents_short %}, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/custom-agents-configuration#mcp-server-configuration-details>).

## Best practices

* Enabling third-party MCP servers for use may impact the performance of the agent and the quality of the outputs. Review the third-party MCP server thoroughly and ensure that it meets your organization’s requirements.

* By default, {% data variables.copilot.copilot_cloud_agent %} does not have access to write MCP server tools. However, some MCP servers do contain such tools. Be sure to review the tools available in the MCP server you want to use. Update the `tools` field in the MCP configuration with only the necessary tooling.

* Carefully review the configured MCP servers prior to saving the configuration to ensure the correct servers are configured for use.


</details>
