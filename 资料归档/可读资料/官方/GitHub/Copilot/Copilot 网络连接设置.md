# Copilot 网络连接设置

介绍 HTTP 代理、Kerberos 和自定义证书的配置，帮助处理企业网络下的连接与认证问题。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/concepts/security-governance-and-network-settings/network-settings.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 网络连接设置.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Network settings for GitHub Copilot
shortTitle: Network settings
intro: 'You can connect to {% data variables.product.prodname_copilot %} through an HTTP proxy and use custom certificates.'
versions:
  fpt: '*'
  ghec: '*'
redirect_from:
  - /copilot/concepts/network-settings-for-github-copilot
  - /copilot/concepts/network-settings
contentType: concepts
category: 
  - Manage Copilot for a team
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data variables.product.prodname_copilot %} 的网络设置允许您通过 HTTP 代理连接到 {% data variables.product.prodname_dotcom %} 并使用自定义证书。

## {% data variables.product.prodname_copilot_short %} 的代理设置

默认情况下，{% data variables.product.prodname_copilot_short %} 通过安全的 HTTPS 连接直接从您的环境连接到 {% data variables.product.prodname_dotcom %} 的服务器。您不一定需要配置任何其他网络设置即可使用 {% data variables.product.prodname_copilot_short %}。

某些网络使用 HTTP 代理服务器来拦截互联网流量，然后再将其发送到预期位置。公司经常使用 HTTP 代理来检测可疑流量或限制进入其网络的内容。如果您在公司网络上工作，则可能需要配置 {% data variables.product.prodname_copilot_short %} 以通过 HTTP 代理进行连接。

> [!NOTE] 您的代理服务器或防火墙的管理员还需要配置网络设置，以使 {% data variables.product.prodname_copilot_short %} 按预期工作。参见[AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/copilot-allowlist-reference>)。

{% data variables.product.prodname_copilot_short %} 支持基本的 HTTP 代理设置。如果您需要对代理进行身份验证，{% data variables.product.prodname_copilot_short %}支持基本身份验证或使用 Kerberos 进行身份验证。如果代理URL启动`https://`，则当前不支持该代理。

如果您没有直接在编辑器中配置代理，{% data variables.product.prodname_copilot_short %} 会检查是否在以下任何环境变量中设置了代理 URL（按优先级从最高到最低的顺序列出）。

* `HTTPS_PROXY`
* `https_proxy`
* `HTTP_PROXY`
* `http_proxy`

> [!NOTE] 您可以使用这些变量中的任何一个来存储标准 HTTP 代理的 URL。在标准用法中，这些变量的 `http` 和 `https` 部分指的是发出的请求的类型，而不是代理本身的 URL。 {% data variables.product.prodname_copilot_short %}不遵循此约定，而是使用存储在具有最高优先级的变量中的URL作为HTTP和HTTPS请求的代理。

### 使用 Kerberos 进行身份验证

Kerberos 是一种身份验证协议，允许用户和服务相互证明自己的身份。当用户成功进行身份验证时，身份验证服务会向用户授予一张票证，使他们能够在一段时间内访问服务。网络管理员可能更喜欢 Kerberos 而不是基本身份验证，因为它更安全并且不需要发送未加密的凭据。

{% data variables.product.prodname_copilot %} 支持使用 Kerberos 对代理进行身份验证。要使用 Kerberos，您必须在计算机上安装适合您的操作系统的 krb5 库以及代理服务的有效票证（使用 `kinit` 命令手动创建或由其他应用程序创建）。您可以使用`klist`命令检查您是否有代理服务的票证。

Kerberos 使用服务主体名称 (SPN) 来唯一标识服务实例。默认情况下，SPN 源自代理 URL。例如，如果代理URL是`http://proxy.example.com:3128`，则SPN是`HTTP/proxy.example.com`。

如果默认的 SPN 对于您的代理不正确，您可以覆盖 {% data variables.product.prodname_vscode_shortname %} 和 JetBrains IDE 中的 SPN。您当前无法覆盖 {% data variables.product.prodname_vs %} 中的默认 SPN。但是，您可以使用环境变量 `AGENT_KERBEROS_SERVICE_PRINCIPAL` 覆盖 {% data variables.product.prodname_vscode_shortname %} 和 JetBrains IDE 的 SPN。

## 定制证书

{% data variables.product.prodname_copilot_short %} 可以读取用户计算机上安装的自定义 SSL 证书。这允许代理服务器被识别为 {% data variables.product.prodname_copilot_short %} 安全连接的预期接收者，因此可以检查网络流量。如果没有自定义证书，HTTP 代理可用于监视、路由和终止 {% data variables.product.prodname_copilot_short %} 的连接，但不能检查流量的内容。

{% data variables.product.prodname_copilot_short %} 从操作系统的信任存储中读取证书。它还从标准 Node.js 环境变量 `NODE_EXTRA_CA_CERTS` 指定的文件中读取额外的证书。有关更多信息，请参阅 [Node.js 文档 ](<https://nodejs.org/docs/latest-v18.x/api/cli.html#node_extra_ca_certsfile>)。

无论用户计算机上是否直接配置代理，{% data variables.product.prodname_copilot_short %} 都可以读取证书。这允许 {% data variables.product.prodname_copilot_short %} 支持透明代理或 Zscaler 等设置。

## 后续步骤

要了解如何在编辑器中配置网络设置，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/configure-network-settings>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

The network settings for {% data variables.product.prodname_copilot %} allow you to connect to {% data variables.product.prodname_dotcom %} through an HTTP proxy and use custom certificates.

## Proxy settings for {% data variables.product.prodname_copilot_short %}

By default, {% data variables.product.prodname_copilot_short %} connects to {% data variables.product.prodname_dotcom %}'s server directly from your environment via a secure HTTPS connection. You don't necessarily need to configure any additional network settings to use {% data variables.product.prodname_copilot_short %}.

Some networks use an HTTP proxy server to intercept Internet traffic before sending it to its intended location. Companies often use an HTTP proxy to detect suspicious traffic or restrict the content entering their networks. If you're working on a corporate network, you may need to configure {% data variables.product.prodname_copilot_short %} to connect via an HTTP proxy.

> [!NOTE] The administrator of your proxy server or firewall also needs to configure network settings for {% data variables.product.prodname_copilot_short %} to work as expected. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/copilot-allowlist-reference>).

{% data variables.product.prodname_copilot_short %} supports basic HTTP proxy setups. If you need to authenticate to a proxy, {% data variables.product.prodname_copilot_short %} supports basic authentication or authentication with Kerberos. If the proxy URL starts `https://`, the proxy is not currently supported.

If you don't configure a proxy directly in your editor, {% data variables.product.prodname_copilot_short %} checks if a proxy URL is set in any of the following environment variables, listed from highest to lowest priority.

* `HTTPS_PROXY`
* `https_proxy`
* `HTTP_PROXY`
* `http_proxy`

> [!NOTE] You can use any of these variables to store the URL of a standard HTTP proxy. In standard usage, the `http` and `https` portions of these variables refer to the type of request being made, not the URL of the proxy itself. {% data variables.product.prodname_copilot_short %} does not follow this convention and uses the URL stored in the variable with the highest priority as the proxy for both HTTP and HTTPS requests.

### Authentication with Kerberos

Kerberos is an authentication protocol that allows users and services to prove their identity to each other. When a user successfully authenticates, an authentication service grants the user a ticket that gives them access to a service for a period of time. Network administrators may prefer Kerberos to basic authentication because it is more secure and doesn't require sending unencrypted credentials.

{% data variables.product.prodname_copilot %} supports authentication to a proxy with Kerberos. To use Kerberos, you must have the appropriate krb5 library for your operating system installed on your machine and an active ticket for the proxy service (either created manually with the `kinit` command or by another application). You can use the `klist` command to check if you have a ticket for the proxy service.

Kerberos uses a service principal name (SPN) to uniquely identify a service instance. By default, the SPN is derived from the proxy URL. For example, if the proxy URL is `http://proxy.example.com:3128`, the SPN is `HTTP/proxy.example.com`.

If the default SPN isn't correct for your proxy, you can override the SPN in {% data variables.product.prodname_vscode_shortname %} and in JetBrains IDEs. You cannot currently override the default SPN in {% data variables.product.prodname_vs %}. However, you can use the environment variable `AGENT_KERBEROS_SERVICE_PRINCIPAL` to override the SPN for {% data variables.product.prodname_vscode_shortname %} and JetBrains IDEs.

## Custom certificates

{% data variables.product.prodname_copilot_short %} can read custom SSL certificates installed on a user's machine. This allows a proxy server to be identified as the intended recipient of {% data variables.product.prodname_copilot_short %}'s secure connection, so network traffic can be inspected. Without a custom certificate, an HTTP proxy can be used to monitor, route, and terminate {% data variables.product.prodname_copilot_short %}'s connection, but not to inspect the contents of the traffic.

{% data variables.product.prodname_copilot_short %} reads certificates from the operating system's trust store. It also reads extra certificates from the file specified by the standard Node.js environment variable `NODE_EXTRA_CA_CERTS`. For more information, see the [Node.js documentation](<https://nodejs.org/docs/latest-v18.x/api/cli.html#node_extra_ca_certsfile>).

{% data variables.product.prodname_copilot_short %} can read certificates regardless of whether a proxy is configured directly on a user's machine. This allows {% data variables.product.prodname_copilot_short %} to support setups such as transparent proxies or Zscaler.

## Next steps

To learn how to configure network settings in your editor, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/configure-network-settings>).


</details>
