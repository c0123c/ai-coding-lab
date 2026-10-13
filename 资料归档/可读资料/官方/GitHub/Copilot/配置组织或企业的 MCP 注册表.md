# 配置组织或企业的 MCP 注册表

介绍自建注册表与 Azure API Center 两种方式，列出接口、协议版本和跨域要求，供管理可用工具源时参考。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/docs/commit/df9bd944684a61fc90f8cfda11cf031984998e04>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/administer-copilot/manage-mcp-usage/configure-mcp-registry.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/配置组织或企业的 MCP 注册表.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Configure an MCP registry for your organization or enterprise
intro: Create and host a list of MCP servers that your developers can access.
permissions: Enterprise owners and organization owners
product: '{% data variables.copilot.copilot_enterprise_short %} or {% data variables.copilot.copilot_business_short %}'
versions:
  feature: copilot
shortTitle: Configure MCP registry
contentType: how-tos
category:
  - Configure Copilot
  - Manage Copilot for a team
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [!IMPORTANT] {% data reusables.copilot.mcp.registry-alternative %}

## 先决条件

在创建 Model Context Protocol (MCP) 注册表之前，您应该了解 MCP 管理对您公司的功能和优势。参见[AUTOTITLE](<https://github.com/copilot/concepts/enterprise/mcp-management>)。

## 选项 1：自托管 MCP 注册表

MCP 注册表的核心是一组 HTTPS 端点，它们提供有关所包含的 MCP 服务器的详细信息。您可以使用以下任一选项创建注册表：
* 分叉并自行托管开源 MCP 注册表。要开始使用，请参阅 `modelcontextprotocol/registry` 存储库中的 [MCP 注册表快速入门 ](<https://github.com/modelcontextprotocol/registry/tree/main?tab=readme-ov-file#quick-start>)。
* 使用 Docker 在本地运行开源注册表。
* 发布您自己的自定义实现。

> [!NOTE]
> 如果您希望开发人员能够访问本地 MCP 服务器，请将这些服务器与正确的服务器 ID 一起包含在您的注册表中。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/mcp-private-registry-enforcement>)。

要创建 {% data variables.product.prodname_copilot %} 可访问的有效 MCP 注册表，注册表必须满足以下要求：
* [端点和规范要求](<#端点和规格要求>)
* [跨源资源共享要求](<#跨源资源共享要求>)

### 端点和规格要求

有效的注册表必须支持 URL 路由并遵循 v0.1 MCP 注册表规范，包括以下端点：
  * `GET /v0.1/servers`：返回所有包含的MCP服务器的列表
  * `GET /v0.1/servers/{serverName}/versions/latest`：返回特定服务器的最新版本
  * `GET /v0.1/servers/{serverName}/versions/{version}`：返回特定版本服务器的详细信息

有关更多详细信息和 JSON 对请求的响应示例，请参阅 [ 官方 MCP 注册文档 ](<https://registry.modelcontextprotocol.io/docs>)。

#### 支持v0.1规范

虽然 MCP 注册表使用 v0 规范启动，但该版本现在被认为不稳定，不应实施。相反，请根据 v0.1 规范创建注册表，以下表面支持该规范：

|表面|v0.1 支持|
| ---------------------- | ----------------- |
|{% data variables.product.prodname_vscode_shortname %} 内部人士| {% octicon "check" aria-label="Supported" %}  |
| {% data variables.product.prodname_vscode_shortname %}   | {% octicon "check" aria-label="Supported" %} |
| {% data variables.product.prodname_vs %}      | {% octicon "check" aria-label="Supported" %} |
|日食| {% octicon "check" aria-label="Supported" %} |
|JetBrains IDE| {% octicon "check" aria-label="Supported" %} |
|Xcode| {% octicon "check" aria-label="Supported" %} |
| {% data variables.copilot.copilot_cli_short %} | {% octicon "check" aria-label="Supported" %} |

### 跨源资源共享要求

为了确保 {% data variables.product.prodname_copilot_short %} 在获取注册表时能够成功发出跨源请求，注册表或反向代理必须包含跨源资源共享 (CORS) 标头。将以下标头添加到所有 `/v0.1/servers` 端点：

``` http copy
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, OPTIONS
Access-Control-Allow-Headers: Authorization, Content-Type
```

## 选项 2：使用 Azure API 中心作为 MCP 注册表

Azure API 中心提供完全托管的 MCP 注册表，具有自动 CORS 配置、内置治理功能，且无需额外的 Web 服务器设置。

1. 要完成注册表的初始设置，请参阅 Azure 文档中的[注册并发现 API 清单 ](<https://learn.microsoft.com/azure/api-center/register-discover-mcp-server>) 中的远程 MCP 服务器。
1. 如果您希望开发人员能够访问本地 MCP 服务器，请将这些服务器与正确的服务器 ID 一起包含在您的注册表中。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/mcp-private-registry-enforcement>)。
1. 为确保 {% data variables.product.prodname_copilot %} 可以获取您的注册表，请在 API 中心的可见性设置中允许匿名访问。
1. 复制您的 API 中心端点 URL。在下一篇文章中，您将使用此 URL 使您的注册表在整个公司内可用。

### 定价和限制

Azure API 中心提供 ** 免费套餐 **，用于基本 API 编目和发现，包括 MCP 注册表管理。如果您需要比免费套餐更高的限制，您可以升级到标准计划。有关详细限制和定价，请参阅 Azure 文档中的 [Azure API 中心限制 ](<https://learn.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-api-center-limits>)。

## 后续步骤

现在您已经创建了 MCP 注册表，您可以为您的公司设置 MCP 策略。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-mcp-usage/restrict-based-on-registry>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!IMPORTANT] {% data reusables.copilot.mcp.registry-alternative %}

## Prerequisites

Before you create your Model Context Protocol (MCP) registry, you should understand the functionality and benefits of MCP management for your company. See [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/mcp-management>).

## Option 1: Self-hosting an MCP registry

At its core, an MCP registry is a set of HTTPS endpoints that serve details about the included MCP servers. You can create your registry with any of the following options:
* Fork and self-host the open-source MCP Registry. To get started, see the [MCP Registry Quickstart](<https://github.com/modelcontextprotocol/registry/tree/main?tab=readme-ov-file#quick-start>) in the `modelcontextprotocol/registry` repository.
* Run the open-source registry locally using Docker.
* Publish your own custom implementation.

> [!NOTE]
> If you want your developers to have access to local MCP servers, include those servers in your registry with the correct server ID. For more information, see [AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/mcp-private-registry-enforcement>).

To create a valid MCP registry that is reachable by {% data variables.product.prodname_copilot %}, the registry must meet the following requirements:
* [Endpoint and specification requirements](<#endpoint-and-specification-requirements>)
* [Cross-Origin Resource Sharing requirements](<#cross-origin-resource-sharing-requirements>)

### Endpoint and specification requirements

A valid registry must support URL routing and follow the v0.1 MCP registry specification, including the following endpoints:
  * `GET /v0.1/servers`: Returns a list of all included MCP servers
  * `GET /v0.1/servers/{serverName}/versions/latest`: Returns the latest version of a specific server
  * `GET /v0.1/servers/{serverName}/versions/{version}`: Returns the details for a specific version of a server

For more details and example JSON responses to requests, see the [Official MCP Registry documentation](<https://registry.modelcontextprotocol.io/docs>).

#### Support for the v0.1 specification

While the MCP registry launched using the v0 specification, that version is now considered unstable and should not be implemented. Instead, create your registry according to the v0.1 specification, which is supported in the following surfaces:

| Surface       | v0.1 support |
| ---------------------- | ----------------- |
| {% data variables.product.prodname_vscode_shortname %} Insiders | {% octicon "check" aria-label="Supported" %}  |
| {% data variables.product.prodname_vscode_shortname %}   | {% octicon "check" aria-label="Supported" %} |
| {% data variables.product.prodname_vs %}      | {% octicon "check" aria-label="Supported" %} |
| Eclipse | {% octicon "check" aria-label="Supported" %} |
| JetBrains IDEs     | {% octicon "check" aria-label="Supported" %} |
| Xcode    | {% octicon "check" aria-label="Supported" %} |
| {% data variables.copilot.copilot_cli_short %} | {% octicon "check" aria-label="Supported" %} |

### Cross-Origin Resource Sharing requirements

To ensure {% data variables.product.prodname_copilot_short %} can successfully make cross-origin requests when fetching your registry, the registry or reverse proxy must include Cross-Origin Resource Sharing (CORS) headers. Add the following headers to all `/v0.1/servers` endpoints:

``` http copy
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, OPTIONS
Access-Control-Allow-Headers: Authorization, Content-Type
```

## Option 2: Using Azure API Center as an MCP registry

Azure API Center provides a fully managed MCP registry with automatic CORS configuration, built-in governance features, and no additional web server setup.

1. To complete the initial setup for your registry, see [Register and discover remote MCP servers in your API inventory](<https://learn.microsoft.com/azure/api-center/register-discover-mcp-server>) in the Azure documentation.
1. If you want your developers to have access to local MCP servers, include those servers in your registry with the correct server ID. For more information, see [AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/mcp-private-registry-enforcement>).
1. To ensure {% data variables.product.prodname_copilot %} can fetch your registry, in the visibility settings of your API Center, allow anonymous access.
1. Copy your API Center endpoint URL. In the next article, you will use this URL to make your registry available across your company.

### Pricing and limits

Azure API Center offers a **free tier** for basic API cataloging and discovery, including MCP registry management. If you need higher limits than those included with the free tier, you can upgrade to the Standard plan. For detailed limits and pricing, see [Azure API Center limits](<https://learn.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-api-center-limits>) in the Azure documentation.

## Next steps

Now that you have created your MCP registry, you can set MCP policies for your company. See [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-mcp-usage/restrict-based-on-registry>).


</details>
