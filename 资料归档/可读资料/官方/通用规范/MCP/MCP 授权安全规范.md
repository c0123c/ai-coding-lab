# MCP 授权安全规范

列出令牌绑定、防盗、通信、重定向、授权服务器、信任和最小权限要求，供客户端及服务端实现参考。

- 来源：通用规范／MCP
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-11](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization/security-considerations>)；规范版本：[2026-07-28](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization/security-considerations>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization/security-considerations>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/MCP/MCP 授权安全规范.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://modelcontextprotocol.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 授权安全注意事项

<div id="enable-section-numbers" />

本文档概述了实施者 **MUST** 在 
 构建 MCP 客户端和服务器时考虑的安全要求。

此外，实现者 **MUST** 遵循 
[OAuth 2.1 部分 7 中概述的 OAuth 2.1 安全最佳实践。 “安全考虑”](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#name-security-considerations>)。

## 令牌受众绑定和验证

[RFC 8707](<https://www.rfc-editor.org/rfc/rfc8707.html>) 资源指示器通过将令牌与其预期目标绑定来提供关键的安全优势
观众 **当授权服务器支持该功能时**。为了实现当前和未来的采用：

* MCP 客户端 **MUST** 在授权和令牌请求中包含 `resource` 参数，如 [资源参数实现 ](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization#resource-parameter-implementation>) 部分中指定
* MCP 服务器 **MUST** 验证提供给他们的令牌是否是专门为其使用而颁发的

[安全最佳实践文档](<https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices#token-passthrough>)
概述了为什么令牌受众验证至关重要以及为什么明确禁止令牌传递。

## 代币盗窃

获取客户端存储的令牌或服务器上缓存或记录的令牌的攻击者可以使用对资源服务器来说合法的 
 请求访问受保护的资源。

客户端和服务器 **MUST** 实施安全令牌存储并遵循 OAuth 最佳实践，
，如 [OAuth 2.1，第 7.1](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-7.1>) 节中所述。

授权服务器 **SHOULD** 发出短期访问令牌，以减少泄漏令牌的影响。
对于公共客户端，授权服务器 **MUST** 轮换刷新令牌，如 [OAuth 2.1 部分 4.3.1“令牌端点”中所述。扩展名“](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-4.3.1>)。

## 通讯安全

实现 **MUST** 遵循 [OAuth 2.1 部分 1.5“通信安全”](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-1.5>)。

具体来说：

1. 所有授权服务器端点 **MUST** 均通过 HTTPS 提供服务。
2. 所有重定向 URI **MUST** 可以是 `localhost` 或使用 HTTPS。

## 授权码保护

获得授权响应中包含的授权代码的访问权限的攻击者可以尝试将授权代码兑换为访问令牌或以其他方式使用授权代码。
（在[OAuth 2.1部分7.5](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-7.5>)中进一步描述）

为了缓解这个问题，MCP客户端**MUST**根据[OAuth 2.1部分7.5.2](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-7.5.2>)和**MUST**实现PKCE，在继续之前验证PKCE支持授权。
PKCE 通过要求客户端创建秘密验证者挑战对来帮助防止授权代码拦截和注入攻击，确保只有原始请求者才能用授权代码交换令牌。

MCP 客户端 **MUST** 在技术上可行时使用 `S256` 代码质询方法，按照 [OAuth 2.1 部分 4.1.1](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-4.1.1>) 的要求。

由于 OAuth 2.1 和 PKCE 规范没有定义客户端发现 PKCE 支持的机制，因此 MCP 客户端 **MUST** 依赖授权服务器元数据来验证此功能：

* **OAuth 2.0授权服务器元数据**：如果`code_challenge_methods_supported`不存在，授权服务器不支持PKCE和MCP客户端**MUST**拒绝继续。

* **OpenID Connect Discovery 1.0**：虽然 [OpenID 提供商元数据 ](<https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata>) 未定义 `code_challenge_methods_supported`，但此字段通常由 OpenID 提供商包含。 MCP 客户端 **MUST** 验证提供者元数据响应中是否存在 `code_challenge_methods_supported`。如果该字段不存在，MCP客户端**MUST**将拒绝继续。

提供 OpenID Connect Discovery 1.0 **MUST** 的授权服务器在其元数据中包含 `code_challenge_methods_supported`，以确保 MCP 兼容性。

## 混合攻击

控制 MCP 客户端与之交互的授权服务器之一的攻击者可能会尝试让客户端向其发送由不同的、诚实的授权服务器颁发的授权代码或令牌（混合攻击，如 [RFC9207 部分 1](<https://datatracker.ietf.org/doc/html/rfc9207#section-1>) 中所述）。 [授权响应验证](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization#authorization-response-validation>)指定所需的缓解措施。

## 打开重定向

攻击者可能会制作恶意重定向 URI 以将用户引导至网络钓鱼站点。

MCP 客户端 **MUST** 已向授权服务器注册了重定向 URI。

授权服务器 **MUST** 根据预先注册的值验证准确的重定向 URI，以防止重定向攻击。

MCP 客户端 **SHOULD** 使用并验证授权代码流 
 中的状态参数，并丢弃任何不包含原始状态或与原始状态不匹配的结果。

授权服务器 **MUST** 采取预防措施，防止将用户 agents 重定向到不受信任的 URI，遵循 [OAuth 2.1 部分 7.12.2](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-7.12.2>) 中提出的建议

仅当授权服务器 **SHOULD** 信任重定向 URI 时，才会自动重定向用户 agent。如果URI不可信，授权服务器MAY会通知用户并依赖用户做出正确的决定。

## 客户端ID元数据文档安全

实施时 [客户 ID 元数据文档](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization/client-registration#client-id-metadata-documents>)、授权服务器 **MUST** 考虑安全影响
详述于 [OAuth客户端 ID 元数据文档，部分 6](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-client-id-metadata-document-00#name-security-considerations>).
主要考虑因素包括：

### 授权服务器滥用保护

授权服务器获取元数据文档 **SHOULD** 考虑
[服务器端请求伪造 (SSRF)](<https://developer.mozilla.org/docs/Web/Security/Attacks/SSRF>) 风险，如 [OAuth 客户端 ID 元数据文档：服务器端请求伪造中所述(SSRF) 攻击](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-client-id-metadata-document-00#name-server-side-request-forgery>)。

### 本地主机重定向 URI 风险

客户端ID元数据文档本身无法防止`localhost`URL冒充。

授权服务器：

* **SHOULD** 显示仅 `localhost` 重定向 URI 的附加警告
* **MAY** 需要额外的证明机制以增强安全性
* **MUST**授权期间清晰显示重定向URI主机名

### 信托政策

授权服务器**MAY**实施基于域的信任策略来接受客户端ID元数据文档，如客户端ID元数据文档的[部分6.4](<https://www.ietf.org/archive/id/draft-ietf-oauth-client-id-metadata-document-00.html#section-6.4>)和[部分6.8](<https://www.ietf.org/archive/id/draft-ietf-oauth-client-id-metadata-document-00.html#section-6.8>)中所述规格。

## 困惑的副问题

攻击者可以利用MCP服务器作为第三方API的中介，导致[混淆代理漏洞](<https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices#confused-deputy-problem>)。
通过使用窃取的授权代码，他们可以在未经用户同意的情况下获取访问令牌。

MCP 使用静态客户端 ID **MUST** 的代理服务器在转发到第三方授权服务器（可能需要额外同意）之前，为每个 
[ 动态注册客户端 ](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization/client-registration#dynamic-client-registration>)
 获取用户同意。

## 访问令牌权限限制

如果服务器接受为其他资源颁发的令牌，攻击者可以获得未经授权的访问或以其他方式危害 MCP 服务器。

MCP 服务器 **MUST** 在处理请求之前验证访问令牌，确保访问令牌是专门为 MCP 服务器颁发的，并采取所有必要的步骤确保不会将数据返回给未经授权的各方。

MCP 服务器 **MUST** 遵循 [OAuth 2.1 - 5.2](<https://www.ietf.org/archive/id/draft-ietf-oauth-v2-1-13.html#section-5.2>) 部分中的指南来验证入站令牌。

MCP 服务器 **MUST** 仅接受专门为其自身设计的令牌，**MUST** 拒绝不将其包含在受众声明中的令牌或以其他方式验证它们是否是令牌的预期接收者。有关详细信息，请参阅[安全最佳实践令牌传递部分](<https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices#token-passthrough>)。

如果 MCP 服务器向上游 API 发出请求，它可能充当它们的 OAuth 客户端。上游使用的访问令牌 API 是一个单独的令牌，由上游授权服务器颁发。 MCP 服务器 **MUST NOT** 传递从 MCP 客户端收到的令牌。

MCP 客户端 **MUST** 实现并使用 [RFC 8707 - OAuth 资源指示符 2.0](<https://www.rfc-editor.org/rfc/rfc8707.html>)
 中定义的 `resource` 参数来显式指定正在请求令牌的目标资源。此要求与 
[RFC 9728 部分 7.4](<https://datatracker.ietf.org/doc/html/rfc9728#section-7.4>) 中的建议一致。这可确保访问令牌绑定到其预期资源，并且 
 不会在不同的服务中被滥用。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://modelcontextprotocol.io/llms.txt
> Use this file to discover all available pages before exploring further.

# Authorization Security Considerations

<div id="enable-section-numbers" />

This document outlines security requirements that implementers **MUST** consider when
building MCP clients and servers.

Additionally, implementors **MUST** follow OAuth 2.1 security best practices as outlined in
[OAuth 2.1 Section 7. "Security Considerations"](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#name-security-considerations>).

## Token Audience Binding and Validation

[RFC 8707](<https://www.rfc-editor.org/rfc/rfc8707.html>) Resource Indicators provide critical security benefits by binding tokens to their intended
audiences **when the Authorization Server supports the capability**. To enable current and future adoption:

* MCP clients **MUST** include the `resource` parameter in authorization and token requests as specified in the [Resource Parameter Implementation](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization#resource-parameter-implementation>) section
* MCP servers **MUST** validate that tokens presented to them were specifically issued for their use

The [Security Best Practices document](<https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices#token-passthrough>)
outlines why token audience validation is crucial and why token passthrough is explicitly forbidden.

## Token Theft

Attackers who obtain tokens stored by the client, or tokens cached or logged on the server can access protected resources with
requests that appear legitimate to resource servers.

Clients and servers **MUST** implement secure token storage and follow OAuth best practices,
as outlined in [OAuth 2.1, Section 7.1](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-7.1>).

Authorization servers **SHOULD** issue short-lived access tokens to reduce the impact of leaked tokens.
For public clients, authorization servers **MUST** rotate refresh tokens as described in [OAuth 2.1 Section 4.3.1 "Token Endpoint Extension"](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-4.3.1>).

## Communication Security

Implementations **MUST** follow [OAuth 2.1 Section 1.5 "Communication Security"](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-1.5>).

Specifically:

1. All authorization server endpoints **MUST** be served over HTTPS.
2. All redirect URIs **MUST** be either `localhost` or use HTTPS.

## Authorization Code Protection

An attacker who has gained access to an authorization code contained in an authorization response can try to redeem the authorization code for an access token or otherwise make use of the authorization code.
(Further described in [OAuth 2.1 Section 7.5](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-7.5>))

To mitigate this, MCP clients **MUST** implement PKCE according to [OAuth 2.1 Section 7.5.2](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-7.5.2>) and **MUST** verify PKCE support before proceeding with authorization.
PKCE helps prevent authorization code interception and injection attacks by requiring clients to create a secret verifier-challenge pair, ensuring that only the original requestor can exchange an authorization code for tokens.

MCP clients **MUST** use the `S256` code challenge method when technically capable, as required by [OAuth 2.1 Section 4.1.1](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-4.1.1>).

Since OAuth 2.1 and PKCE specifications do not define a mechanism for clients to discover PKCE support, MCP clients **MUST** rely on authorization server metadata to verify this capability:

* **OAuth 2.0 Authorization Server Metadata**: If `code_challenge_methods_supported` is absent, the authorization server does not support PKCE and MCP clients **MUST** refuse to proceed.

* **OpenID Connect Discovery 1.0**: While the [OpenID Provider Metadata](<https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata>) does not define `code_challenge_methods_supported`, this field is commonly included by OpenID providers. MCP clients **MUST** verify the presence of `code_challenge_methods_supported` in the provider metadata response. If the field is absent, MCP clients **MUST** refuse to proceed.

Authorization servers providing OpenID Connect Discovery 1.0 **MUST** include `code_challenge_methods_supported` in their metadata to ensure MCP compatibility.

## Mix-Up Attacks

An attacker that controls one of the authorization servers an MCP client interacts with may attempt to have the client send it an authorization code or token issued by a different, honest authorization server (a mix-up attack, described in [RFC9207 Section 1](<https://datatracker.ietf.org/doc/html/rfc9207#section-1>)). [Authorization Response Validation](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization#authorization-response-validation>) specifies the required mitigation.

## Open Redirection

An attacker may craft malicious redirect URIs to direct users to phishing sites.

MCP clients **MUST** have redirect URIs registered with the authorization server.

Authorization servers **MUST** validate exact redirect URIs against pre-registered values to prevent redirection attacks.

MCP clients **SHOULD** use and verify state parameters in the authorization code flow
and discard any results that do not include or have a mismatch with the original state.

Authorization servers **MUST** take precautions to prevent redirecting user agents to untrusted URI's, following suggestions laid out in [OAuth 2.1 Section 7.12.2](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-13#section-7.12.2>)

Authorization servers **SHOULD** only automatically redirect the user agent if it trusts the redirection URI. If the URI is not trusted, the authorization server MAY inform the user and rely on the user to make the correct decision.

## Client ID Metadata Document Security

When implementing [Client ID Metadata Documents](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization/client-registration#client-id-metadata-documents>), authorization servers **MUST** consider the security implications
detailed in [OAuth Client ID Metadata Document, Section 6](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-client-id-metadata-document-00#name-security-considerations>).
Key considerations include:

### Authorization Server Abuse Protection

Authorization servers fetching metadata documents **SHOULD** consider
[Server-Side Request Forgery (SSRF)](<https://developer.mozilla.org/docs/Web/Security/Attacks/SSRF>) risks, as described in [OAuth Client ID Metadata Document: Server Side Request Forgery (SSRF) Attacks](<https://datatracker.ietf.org/doc/html/draft-ietf-oauth-client-id-metadata-document-00#name-server-side-request-forgery>).

### Localhost Redirect URI Risks

Client ID Metadata Documents cannot prevent `localhost` URL impersonation by themselves.

Authorization servers:

* **SHOULD** display additional warnings for `localhost`-only redirect URIs
* **MAY** require additional attestation mechanisms for enhanced security
* **MUST** clearly display the redirect URI hostname during authorization

### Trust Policies

Authorization servers **MAY** implement domain-based trust policies for accepting Client ID Metadata Documents, as described in [Section 6.4](<https://www.ietf.org/archive/id/draft-ietf-oauth-client-id-metadata-document-00.html#section-6.4>) and [Section 6.8](<https://www.ietf.org/archive/id/draft-ietf-oauth-client-id-metadata-document-00.html#section-6.8>) of the Client ID Metadata Document specification.

## Confused Deputy Problem

Attackers can exploit MCP servers acting as intermediaries to third-party APIs, leading to [confused deputy vulnerabilities](<https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices#confused-deputy-problem>).
By using stolen authorization codes, they can obtain access tokens without user consent.

MCP proxy servers using static client IDs **MUST** obtain user consent for each
[dynamically registered client](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization/client-registration#dynamic-client-registration>)
before forwarding to third-party authorization servers (which may require additional consent).

## Access Token Privilege Restriction

An attacker can gain unauthorized access or otherwise compromise an MCP server if the server accepts tokens issued for other resources.

MCP servers **MUST** validate access tokens before processing the request, ensuring the access token is issued specifically for the MCP server, and take all necessary steps to ensure no data is returned to unauthorized parties.

A MCP server **MUST** follow the guidelines in [OAuth 2.1 - Section 5.2](<https://www.ietf.org/archive/id/draft-ietf-oauth-v2-1-13.html#section-5.2>) to validate inbound tokens.

MCP servers **MUST** only accept tokens specifically intended for themselves and **MUST** reject tokens that do not include them in the audience claim or otherwise verify that they are the intended recipient of the token. See the [Security Best Practices Token Passthrough section](<https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices#token-passthrough>) for details.

If the MCP server makes requests to upstream APIs, it may act as an OAuth client to them. The access token used at the upstream API is a separate token, issued by the upstream authorization server. The MCP server **MUST NOT** pass through the token it received from the MCP client.

MCP clients **MUST** implement and use the `resource` parameter as defined in [RFC 8707 - Resource Indicators for OAuth 2.0](<https://www.rfc-editor.org/rfc/rfc8707.html>)
to explicitly specify the target resource for which the token is being requested. This requirement aligns with the recommendation in
[RFC 9728 Section 7.4](<https://datatracker.ietf.org/doc/html/rfc9728#section-7.4>). This ensures that access tokens are bound to their intended resources and
cannot be misused across different services.


</details>
