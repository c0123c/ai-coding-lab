# 配置 Copilot 网络防火墙

说明云端 Agent 与代码审查可以访问哪些域名，涵盖组织和仓库白名单、推荐配置、开关与网络限制。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-15](<https://github.com/github/docs/commit/d8ef2a051ec0c973b3fb485be23f8b9398e46345>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/配置 Copilot 网络防火墙（存档be38ec5d-497）.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Customizing or disabling the firewall for GitHub Copilot
shortTitle: Customize the firewall
allowTitleToDifferFromFilename: true
intro: 'Learn how to control the domains and URLs that {% data variables.copilot.copilot_cloud_agent %} and {% data variables.copilot.copilot_code-review_short %} can access.'
versions:
  feature: copilot
redirect_from:
  - /copilot/how-tos/use-copilot-agents/coding-agent/customize-the-agent-firewall
  - /copilot/customizing-copilot/customizing-or-disabling-the-firewall-for-copilot-coding-agent
  - /copilot/how-tos/agents/copilot-coding-agent/customizing-or-disabling-the-firewall-for-copilot-coding-agent
  - /copilot/how-tos/agents/copilot-coding-agent/customize-the-agent-firewall
  - /copilot/how-tos/agents/coding-agent/customize-the-agent-firewall
  - /copilot/how-tos/use-copilot-agents/cloud-agent/customize-the-agent-firewall
  - /copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
contentType: how-tos
category:
  - Configure Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [！NOTE]
> 防火墙配置通过“Internet 访问”设置选项卡进行管理，该选项卡涵盖 {% data variables.copilot.copilot_cloud_agent %} 和 {% data variables.copilot.copilot_code-review_short %}。以前保存为操作变量的配置将保留在该页面上。

## 概述

默认情况下，{% data variables.product.prodname_copilot_short %}对互联网的访问受到防火墙的限制。

> [!NOTE]
> {% data variables.copilot.copilot_code-review_short %} 还支持组织和存储库级别的防火墙配置，位于下面描述的“Internet 访问”选项卡的其自己的部分下。这允许您独立于 {% data variables.copilot.copilot_cloud_agent %} 为 {% data variables.copilot.copilot_code-review_short %} 配置防火墙规则。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review#customizing-copilot-code-reviews-environment>)。

限制互联网访问有助于管理数据泄露风险。 {% data variables.product.prodname_copilot_short %} 的意外行为或恶意指令可能会导致代码或其他敏感信息泄露到远程位置。

防火墙始终允许访问 {% data variables.product.prodname_copilot_short %} 用于与 {% data variables.product.github %} 交互的多个主机。默认情况下，还会启用推荐的白名单，以允许 agent 下载依赖项。

如果 {% data variables.product.prodname_copilot_short %} 尝试发出被防火墙阻止的请求，则会将警告添加到拉取请求正文（对于新拉取请求）或注释（对于现有拉取请求）。该警告显示被阻止的地址和尝试发出请求的命令。

![来自 {% data variables.product.prodname_copilot_short %} 的有关被防火墙阻止的警告的屏幕截图。](<https://github.com/assets/images/help/copilot/cloud-agent/firewall-warning.png>)

## 局限性

agent 防火墙具有影响其安全覆盖范围的重要限制。

* **仅适用于由agent**启动的进程：防火墙仅适用于由agent通过其Bash工具启动的进程。它并不直接应用于 Model Context Protocol (MCP) 服务器进程或在配置的 {% data variables.product.prodname_copilot_short %} 设置步骤中启动的进程。当 MCP 服务器正在使用时，其他 agent 进程仍然可以发出网络请求，并且这些请求受到防火墙的约束。
* **仅适用于 {% data variables.product.prodname_actions %} 设备内 **：防火墙仅在 {% data variables.product.prodname_actions %} 设备环境内运行。它不适用于在此环境之外运行的进程。
* **绕过潜力**：复杂的攻击可能会绕过防火墙，从而可能允许未经授权的网络访问和数据泄露。

这些限制意味着防火墙为常见场景提供保护，但不应被视为全面的安全解决方案。

## 了解推荐的防火墙白名单

默认情况下启用的推荐白名单允许访问：

* 常见操作系统软件包存储库（例如 Debian、Ubuntu、Red Hat）。
* 常见容器注册表（例如 Docker Hub、Azure 容器注册表、AWS 弹性容器注册表）。
* 流行编程语言（C#、Dart、Go、Haskell、Java、JavaScript、Perl、PHP、Python、Ruby、Rust、斯威夫特）。
* 通用证书颁发机构（以允许验证 SSL 证书）。
* 用于为 Playwright MCP 服务器下载 Web 浏览器的主机。

有关建议允许列表中包含的主机的完整列表，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/copilot-allowlist-reference#copilot-cloud-agent-recommended-allowlist>)。

## 在组织级别配置防火墙

组织所有者可以在组织级别配置 {% data variables.copilot.copilot_cloud_agent %} 和 {% data variables.copilot.copilot_code-review_short %} 的所有防火墙设置。要访问防火墙设置：

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.copilot.internet-access-settings %}

“Internet 访问”页面有单独的 {% data variables.copilot.copilot_cloud_agent %} 和 {% data variables.copilot.copilot_code-review_short %} 部分，因此您可以为每个部分单独配置以下设置。

### 启用或禁用防火墙

> [！WARNING]
> 禁用防火墙将允许{% data variables.product.prodname_copilot_short %}连接到任何主机，从而增加代码或其他敏感信息泄露的风险。

1. 在“Internet 访问”下，将**启用防火墙**设置为**启用**、**禁用**或**让存储库决定**（默认）。

### 启用或禁用推荐的白名单

1. 在“Internet 访问”下，将 ** 推荐允许列表 ** 设置为 ** 已启用 **、** 已禁用 ** 或 ** 让存储库决定**（默认）。

### 控制存储库是否可以添加自定义白名单规则

默认情况下，存储库管理员可以将自己的条目添加到防火墙白名单中。组织所有者可以禁用此功能以防止存储库添加自定义规则。

1. 在“Internet 访问”下，将 **允许存储库自定义规则 ** 设置为 **启用**（默认）或**禁用**。

### 管理组织自定义许可名单

添加到组织自定义允许列表的项目适用于组织中的所有存储库。这些项目无法在存储库级别删除。组织级和存储库级规则相结合。

1. 在“Internet 访问”下，单击 **组织自定义白名单 **。
{% data reusables.copilot.cloud-agent.custom-allowlist-add-entries %}

## 在存储库级别配置防火墙

存储库管理员可以在存储库级别配置防火墙设置，包括启用或禁用防火墙、启用或禁用推荐的白名单以及管理自定义白名单。根据组织级别的配置，其中一些设置可能会被锁定。存储库设置页面还具有单独的 {% data variables.copilot.copilot_cloud_agent %} 和 {% data variables.copilot.copilot_code-review_short %} 部分，因此您可以为每个部分单独配置这些设置。

要访问防火墙设置：

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-settings %}
1. {% data reusables.user-settings.code-planning-automation %}点击**{% data variables.product.prodname_copilot_short %}**，然后点击**上网**。

### 启用或禁用防火墙

> [!NOTE]
> 如果组织级别 **启用防火墙 ** 设置设置为 **让存储库决定 **，则只能在存储库级别更改此设置。如果组织级别设置为 **Enabled** 或 **Disabled**，则无法更改各个存储库的此设置。

1. 打开或关闭**启用防火墙**设置。

### 启用或禁用推荐的白名单

> [!NOTE]
> 如果组织级别 **推荐允许列表 ** 设置设置为 **让存储库决定 **，则只能在存储库级别更改此设置。如果组织级别设置为 **Enabled** 或 **Disabled**，则无法更改各个存储库的此设置。

1. 打开或关闭 ** 推荐白名单 ** 设置。

### 管理自定义白名单

> [!NOTE]
> 如果组织级别 **允许存储库自定义规则 ** 设置设置为 **已启用 **，则只能在存储库级别添加自定义白名单规则。有关详细信息，请参阅 [控制存储库是否可以添加自定义白名单规则 ](<#控制存储库是否可以添加自定义白名单规则>)。

1. 单击**自定义白名单**。
{% data reusables.copilot.cloud-agent.custom-allowlist-add-entries %}

## 进一步阅读

* [AUTOTITLE](<https://github.com/actions/how-tos/write-workflows/choose-what-workflows-do/use-variables#creating-configuration-variables-for-a-repository>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE]
> Firewall configuration is managed from the "Internet access" settings tab, which covers both {% data variables.copilot.copilot_cloud_agent %} and {% data variables.copilot.copilot_code-review_short %}. Previous configurations saved as Actions variables will be maintained on that page.

## Overview

By default, {% data variables.product.prodname_copilot_short %}'s access to the internet is limited by a firewall.

> [!NOTE]
> {% data variables.copilot.copilot_code-review_short %} also supports firewall configuration, at both the organization and repository level, under its own section of the "Internet access" tab described below. This allows you to configure firewall rules for {% data variables.copilot.copilot_code-review_short %} independently of {% data variables.copilot.copilot_cloud_agent %}. See [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review#customizing-copilot-code-reviews-environment>).

Limiting internet access helps manage data exfiltration risks. Unexpected behavior from {% data variables.product.prodname_copilot_short %}, or malicious instructions, could lead to code or other sensitive information being leaked to remote locations.

The firewall always allows access to a number of hosts that {% data variables.product.prodname_copilot_short %} uses to interact with {% data variables.product.github %}. By default, a recommended allowlist is also enabled to allow the agent to download dependencies.

If {% data variables.product.prodname_copilot_short %} tries to make a request which is blocked by the firewall, a warning is added to the pull request body (for new pull requests) or to a comment (for existing pull requests). The warning shows the blocked address and the command that tried to make the request.

![Screenshot of a warning from {% data variables.product.prodname_copilot_short %} about being blocked by the firewall.](<https://github.com/assets/images/help/copilot/cloud-agent/firewall-warning.png>)

## Limitations

The agent firewall has important limitations that affect its security coverage.

* **Only applies to processes started by the agent**: The firewall only applies to processes started by the agent via its Bash tool. It does not apply directly to Model Context Protocol (MCP) server processes or processes started in configured {% data variables.product.prodname_copilot_short %} setup steps. Other agent processes can still make network requests while an MCP server is in use, and these requests are subject to the firewall.
* **Only applies within the {% data variables.product.prodname_actions %} appliance**: The firewall only operates within the {% data variables.product.prodname_actions %} appliance environment. It does not apply to processes running outside of this environment.
* **Bypass potential**: Sophisticated attacks may bypass the firewall, potentially allowing unauthorized network access and data exfiltration.

These limitations mean that the firewall provides protection for common scenarios, but should not be considered a comprehensive security solution.

## Understanding the recommended firewall allowlist

The recommended allowlist, enabled by default, allows access to:

* Common operating system package repositories (for example, Debian, Ubuntu, Red Hat).
* Common container registries (for example, Docker Hub, Azure Container Registry, AWS Elastic Container Registry).
* Packages registries used by popular programming languages (C#, Dart, Go, Haskell, Java, JavaScript, Perl, PHP, Python, Ruby, Rust, Swift).
* Common certificate authorities (to allow SSL certificates to be validated).
* Hosts used to download web browsers for the Playwright MCP server.

For the complete list of hosts included in the recommended allowlist, see [AUTOTITLE](<https://github.com/copilot/reference/copilot-allowlist-reference#copilot-cloud-agent-recommended-allowlist>).

## Configuring the firewall at the organization level

Organization owners can configure all firewall settings at the organization level, for both {% data variables.copilot.copilot_cloud_agent %} and {% data variables.copilot.copilot_code-review_short %}. To access the firewall settings:

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.copilot.internet-access-settings %}

The "Internet access" page has separate sections for {% data variables.copilot.copilot_cloud_agent %} and {% data variables.copilot.copilot_code-review_short %}, so you can configure the following settings independently for each.

### Enabling or disabling the firewall

> [!WARNING]
> Disabling the firewall will allow {% data variables.product.prodname_copilot_short %} to connect to any host, increasing risks of exfiltration of code or other sensitive information.

1. Under "Internet access", set the **Enable firewall** setting to **Enabled**, **Disabled**, or **Let repositories decide** (default).

### Enabling or disabling the recommended allowlist

1. Under "Internet access", set the **Recommended allowlist** setting to **Enabled**, **Disabled**, or **Let repositories decide** (default).

### Controlling whether repositories can add custom allowlist rules

By default, repository administrators can add their own entries to the firewall allowlist. Organization owners can disable this to prevent repositories from adding custom rules.

1. Under "Internet access", set the **Allow repository custom rules** setting to **Enabled** (default) or **Disabled**.

### Managing the organization custom allowlist

Items added to the organization custom allowlist apply to all repositories in the organization. These items cannot be deleted at the repository level. Organization-level and repository-level rules are combined.

1. Under "Internet access", click **Organization custom allowlist**.
{% data reusables.copilot.cloud-agent.custom-allowlist-add-entries %}

## Configuring the firewall at the repository level

Repository administrators can configure firewall settings at the repository level, including enabling or disabling the firewall, enabling or disabling the recommended allowlist, and managing a custom allowlist. Depending on the organization-level configuration, some of these settings may be locked. The repository settings page also has separate sections for {% data variables.copilot.copilot_cloud_agent %} and {% data variables.copilot.copilot_code-review_short %}, so you can configure these settings independently for each.

To access the firewall settings:

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-settings %}
1. {% data reusables.user-settings.code-planning-automation %} click **{% data variables.product.prodname_copilot_short %}** then **Internet access**.

### Enabling or disabling the firewall

> [!NOTE]
> You can only change this setting at the repository level if the organization-level **Enable firewall** setting is set to **Let repositories decide**. If the organization-level setting is **Enabled** or **Disabled**, you can't change this setting for individual repositories.

1. Toggle the **Enable firewall** setting on or off.

### Enabling or disabling the recommended allowlist

> [!NOTE]
> You can only change this setting at the repository level if the organization-level **Recommended allowlist** setting is set to **Let repositories decide**. If the organization-level setting is **Enabled** or **Disabled**, you can't change this setting for individual repositories.

1. Toggle the **Recommended allowlist** setting on or off.

### Managing the custom allowlist

> [!NOTE]
> You can only add custom allowlist rules at the repository level if the organization-level **Allow repository custom rules** setting is set to **Enabled**. For more information, see [Controlling whether repositories can add custom allowlist rules](<#controlling-whether-repositories-can-add-custom-allowlist-rules>).

1. Click **Custom allowlist**.
{% data reusables.copilot.cloud-agent.custom-allowlist-add-entries %}

## Further reading

* [AUTOTITLE](<https://github.com/actions/how-tos/write-workflows/choose-what-workflows-do/use-variables#creating-configuration-variables-for-a-repository>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)


</details>
