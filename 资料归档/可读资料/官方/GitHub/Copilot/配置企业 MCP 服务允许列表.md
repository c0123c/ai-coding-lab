# 配置企业 MCP 服务允许列表

讲解允许和拒绝规则及判断顺序，用于限制成员可接入的 MCP 服务；属于企业工具权限管理资料。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-15](<https://github.com/github/docs/commit/1413b140620171c5e4494c52e28a736e4b68b36e>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/administer-copilot/manage-mcp-usage/configure-enterprise-allowlist.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/配置企业 MCP 服务允许列表.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Configuring an MCP server allowlist for your enterprise
intro: Define which MCP servers your users can and cannot use without the need for a private registry.
permissions: Enterprise owners
product: '{% data variables.copilot.copilot_enterprise_short %} or {% data variables.copilot.copilot_business_short %}'
versions:
  feature: copilot
shortTitle: Configure enterprise allowlist
contentType: how-tos
category:
  - Configure Copilot
  - Manage Copilot for a team
docsTeamMetrics:
  - ai-governance
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 关于许可名单

您可以定义允许列表和拒绝列表来控制企业中的哪些 MCP 服务器用户可以在 {% data variables.product.prodname_copilot_short %} 客户端中运行。这些列表在您企业的 `{% data variables.copilot.managed_setting_file %}` 中定义，您可以将其存储在 {% data variables.product.github %} 上。

欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/enterprise/mcp-management>)。

## 先决条件

* 要运行任何 MCP 服务器，必须为您的企业或应允许 MCP 服务器的组织启用 {% data variables.product.prodname_copilot_short %}** 策略中的 **MCP 服务器。
* 如果您当前将 MCP 服务器限制为自定义注册表，我们建议您关闭此限制，以避免与新的允许列表发生冲突并维护单一事实来源。将 **限制 MCP 访问注册表服务器 ** 策略设置为 **允许所有 **，并可选择清除 **MCP 注册表 URL** 的值。

您可以在 {% data variables.product.prodname_copilot_short %} 策略的 {% octicon "mcp" aria-hidden="true" aria-label="MCP" %} **MCP** 部分找到这些设置。参见[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-enterprise-policies>)。

## 定义允许列表或拒绝列表

1. 为您的企业创建一个 `{% data variables.copilot.managed_setting_file %}` 文件。大多数企业将此文件存储在 `.github-private` 存储库中。您还可以使用移动设备管理将其直接安装在用户的计算机上。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/use-managed-settings/deploy-managed-settings#deploying-server-managed-settings>)。
1. 编辑该文件以定义 MCP 服务器的允许列表和拒绝列表。您可以按名称、服务器 URL 或特定命令进行匹配。有关语法详细信息，请参阅“企业托管设置”中的 [allowedMcpServers](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings#allowedmcpservers>) 和 [deniedMcpServers](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings#deniedmcpservers>)。

以下示例允许与三个允许列表条目中的任何一个条目匹配的服务器。配置为访问根文件系统的文件系统服务器始终被阻止，即使它也与允许列表条目匹配。

   ```json copy
   {
     "allowedMcpServers": [
       { "serverUrl": "https://api.githubcopilot.com/*" },
       { "serverCommand": ["npx", "@playwright/mcp@latest"] },
       { "serverCommand": ["cmd", "/c", "uvx", "markitdown-mcp"] }
     ],
     "deniedMcpServers": [
       {
         "serverCommand": [
           "npx",
           "-y",
           "@modelcontextprotocol/server-filesystem",
           "/"
         ]
       }
     ]
   }
   ```

## 评审规则

{% data variables.product.prodname_copilot_short %} 客户端按以下顺序评估 MCP 服务器：

1. 始终允许内置默认服务器，例如内置 {% data variables.product.github %} MCP 服务器。
1. 如果服务器与 `deniedMcpServers` 中的任何条目匹配，则阻止服务器。
1. 如果存在 `allowedMcpServers`，则在与条目不匹配时阻止服务器。
1. 如果服务器的 URL 或命令包含未解析的变量，例如 `${VARIABLE}` 或 `$VARIABLE`，则阻止服务器，因为客户端无法验证服务器。

如果客户端从多个 `{% data variables.copilot.managed_setting_file %}` 部署方法接收设置，则所有设置都适用。来自任何来源的拒绝规则都会阻止服务器，并且服务器必须与定义该规则的每一层的允许列表条目相匹配。

如果允许列表或拒绝列表格式不正确（例如，JSON 无效），客户端会将该策略视为空的 `allowedMcpServers` 列表。这会阻止除内置默认服务器之外的所有服务器。

如果客户端由于检索或设备发现错误而无法确定策略层，它将保留之前强制执行的策略。有效的政策可以变得更加严格，但不能减少限制。


## 原文对照

<details>
<summary>展开完整原文</summary>

## About allowlists

You can define an allowlist and denylist to control which MCP servers users in your enterprise can run in {% data variables.product.prodname_copilot_short %} clients. These lists are defined in your enterprise's `{% data variables.copilot.managed_setting_file %}`, which you can store on {% data variables.product.github %}.

For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/mcp-management>).

## Prerequisites

* For any MCP servers to run, the **MCP servers in {% data variables.product.prodname_copilot_short %}** policy must be enabled for your enterprise or for organizations where MCP servers should be allowed.
* If you currently restrict MCP servers to a custom registry, we recommend turning off this restriction to avoid conflicts with your new allowlist and maintain a single source of truth. Set the **Restrict MCP access to registry servers** policy to **Allow all**, and optionally clear the value for **MCP Registry URL**.

You can find these settings in the {% octicon "mcp" aria-hidden="true" aria-label="MCP" %} **MCP** section of your {% data variables.product.prodname_copilot_short %} policies. See [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-enterprise-policies>).

## Defining an allowlist or denylist

1. Create a `{% data variables.copilot.managed_setting_file %}` file for your enterprise. Most enterprises store this file in a `.github-private` repository. You can also install it directly on users' machines using mobile device management. See [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/use-managed-settings/deploy-managed-settings#deploying-server-managed-settings>).
1. Edit the file to define an allowlist and denylist for MCP servers. You can match by name, server URL, or specific commands. For syntax details, see [allowedMcpServers](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings#allowedmcpservers>), and [deniedMcpServers](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings#deniedmcpservers>) in "Enterprise managed settings."

    The following example allows servers that match any of the three allowlist entries. The filesystem server configured to access the root filesystem is always blocked, even if it also matches an allowlist entry.

   ```json copy
   {
     "allowedMcpServers": [
       { "serverUrl": "https://api.githubcopilot.com/*" },
       { "serverCommand": ["npx", "@playwright/mcp@latest"] },
       { "serverCommand": ["cmd", "/c", "uvx", "markitdown-mcp"] }
     ],
     "deniedMcpServers": [
       {
         "serverCommand": [
           "npx",
           "-y",
           "@modelcontextprotocol/server-filesystem",
           "/"
         ]
       }
     ]
   }
   ```

## Evaluation rules

{% data variables.product.prodname_copilot_short %} clients evaluate MCP servers in this order:

1. Always allow built-in default servers, such as the built-in {% data variables.product.github %} MCP server.
1. Block the server if it matches any entry in `deniedMcpServers`.
1. If `allowedMcpServers` is present, block the server if it does not match an entry.
1. Block the server if its URL or command contains an unresolved variable, such as `${VARIABLE}` or `$VARIABLE`, because the client cannot verify the server.

If a client receives settings from multiple `{% data variables.copilot.managed_setting_file %}` deployment methods, all the settings apply. A deny rule from any source blocks the server, and a server must match an allowlist entry at every layer that defines one.

If an allowlist or denylist is malformed (for example, has invalid JSON), the client treats the policy as an empty `allowedMcpServers` list. This blocks all servers except built-in default servers.

If the client cannot determine a policy layer because of a retrieval or device-discovery error, it retains the previously enforced policy. The effective policy can become more restrictive, but not less restrictive.


</details>
