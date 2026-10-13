# MCP 协议修订变更说明

列出相对前一版本的主要、次要、弃用和结构变化，帮助核对升级后的行为；这是版本差异资料。

- 来源：通用规范／MCP
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-11](<https://modelcontextprotocol.io/specification/2026-07-28/changelog>)；规范版本：[2026-07-28](<https://modelcontextprotocol.io/specification/2026-07-28/changelog>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://modelcontextprotocol.io/specification/2026-07-28/changelog>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/MCP/MCP 协议修订变更说明.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://modelcontextprotocol.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 主要变化

<div id="enable-section-numbers" />

本文档列出了自 
 上一版本 [2025-11-25](<https://modelcontextprotocol.io/specification/2025-11-25>) 以来对 Model Context Protocol (MCP) 规范所做的更改。

## 主要变化

1. 从 Streamable HTTP 传输中删除协议级会话和 `Mcp-Session-Id` 标头。列表端点（`tools/list`、`resources/list`、`prompts/list`）不再因每个连接而异。需要交叉调用状态的服务器使用显式的、服务器创建的句柄作为普通工具参数传递（[SEP-2567](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2567>)）。

2. 使 MCP 无状态：删除 `initialize`/`notifications/initialized` 握手。现在，每个请求都在 `_meta`（`io.modelcontextprotocol/protocolVersion`、`io.modelcontextprotocol/clientCapabilities`）中携带其协议版本和客户端功能。客户端 SHOULD 在每个请求 (`io.modelcontextprotocol/clientInfo`) 上标识自己，服务器 SHOULD 在每个结果的 `_meta` (`io.modelcontextprotocol/serverInfo`) 中标识自己。版本不匹配返回 `UnsupportedProtocolVersionError` ([SEP-2575](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2575>))。

3. 添加 `server/discover`：服务器 MUST 实现此 RPC 来通告其支持的协议版本、功能和身份。客户端 MAY 在任何其他前期版本选择请求之前调用它，或者将其用作 STDIO ([SEP-2575](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2575>)) 上的向后兼容性探测器。

4. 将 HTTP GET 端点和 `resources/subscribe`/`resources/unsubscribe` 替换为 `subscriptions/listen`：用于选择加入服务器到客户端更改通知的单个长期 POST- 响应流。客户选择特定类型（`toolsListChanged`、`promptsListChanged`、`resourcesListChanged`、`resourceSubscriptions`）；服务器确认并用 `io.modelcontextprotocol/subscriptionId` 标记通知。请求范围的通知（例如 `notifications/progress` 和 `notifications/message`）继续在与其相关的请求的响应流上流动，而不是在 `subscriptions/listen` 流 ([SEP-2575](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2575>)) 上流动。

5. 删除`ping`、`logging/setLevel`和`notifications/roots/list_changed`。现在通过 `_meta` 中的 `io.modelcontextprotocol/logLevel` 设置每个请求的日志级别；服务器 MUST NOT 对于不包含此字段 ([SEP-2575](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2575>)) 的请求发出 `notifications/message`。

6. 将实验任务从核心协议中移出并移至官方扩展中（`io.modelcontextprotocol/tasks`）。重新设计的扩展将阻塞 `tasks/result` 方法替换为通过 `tasks/get` 轮询和用于客户端到服务器输入的新 `tasks/update`，删除 `tasks/list`，并允许服务器主动返回任务句柄，而无需按请求选择加入 ([SEP-2663](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2663>))。

7. 引入了多往返请求 (MRTR) 模式，它取代了以前发送服务器发起的请求的方法，例如 `roots/list`、`sampling/createMessage` 或 `elicitation/create`。服务器返回一个 `InputRequiredResult` (`resultType: "input_required"`)，其 `inputRequests` 字段携带处理请求所需的附加信息的请求。客户端在重试原始请求时以 `inputResponses` 进行响应，提供所请求的信息。 （[SEP-2322](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2322>)）。

8. 现在，所有结果都带有必需的 `resultType` 字段：对于普通结果为 `"complete"`，对于 [多次往返请求 ](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/mrtr>) 临时结果为 `"input_required"`。客户端 **MUST** 将来自省略该字段的早期协议服务器的结果视为 `"complete"` ([SEP-2322](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2322>))。

9. 从 Streamable HTTP 传输中删除 SSE 流可恢复性和消息重新传递（`Last-Event-ID` 标头和 SSE 事件 ID）。损坏的响应流会丢失正在进行的请求；客户端 **MUST** 使用新请求 ID ([SEP-2575](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2575>)) 将其重新发出为新请求。

## 小改动

1. 将 `extensions` 字段添加到 `ClientCapabilities` 和 `ServerCapabilities` 以支持核心协议之外的可选 [ 扩展 ](<https://modelcontextprotocol.io/docs/extensions/overview>)。
2. 记录 `_meta` 键（`traceparent`、`tracestate`、`baggage`）（[SEP-414](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/414>)）的 OpenTelemetry 跟踪上下文传播约定。
3. 服务器 **SHOULD** 以确定的顺序从 `tools/list` 返回工具，以启用客户端缓存并提高 LLM 提示缓存命中率。
4. 在可流 HTTP POST 请求上需要标准 MCP 请求标头（`Mcp-Method`、`Mcp-Name`），并通过 `x-mcp-header` ([SEP-2243](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2243>)) 添加对工具参数中自定义标头的支持。
5. 在 `tools/list`、`prompts/list`、`resources/list`、`resources/read` 和 `resources/templates/list` 通过新的 `CacheableResult` 接口返回的结果中需要 `ttlMs` 和 `cacheScope` 字段。 `ttlMs` 是新鲜度提示（以毫秒为单位），允许客户端缓存响应并减少轮询； `cacheScope`（`"public"`或`"private"`）控制共享中介是否可以缓存响应。这两个字段都补充了现有的 `listChanged` 通知 ([SEP-2549](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2549>))。
6. 将未找到资源错误代码从 `-32002` 更改为 `-32602`（无效参数）以与 JSON-RPC 规范保持一致。
7. 授权服务器 **SHOULD** 在授权响应中包含 `iss` 参数
   [RFC 9207](<https://datatracker.ietf.org/doc/html/rfc9207>), 和 MCP 客户 **MUST** 验证一个
   现在 `iss` 在兑换授权码之前针对已记录的发行人
   ([SEP-2468](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2468>)).
8. 要求 MCP 客户端在动态客户端期间指定适当的 `application_type`
注册以避免 OpenID 连接重定向 URI 冲突 
 ([SEP-837](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/837>))。
9. 澄清客户端凭据绑定到颁发它们的授权服务器：
客户端**MUST**密钥通过颁发者标识符保存凭证，**MUSTNOT**在不同的授权服务器上重用它们
，并且**MUST**在授权服务器
更改（[SEP-2352](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2352>)）时重新注册。
10. 放宽 `inputSchema` 和 `outputSchema` 以允许任何 JSON 架构 2020-12 关键字，并且
`structuredContent` 允许任何 JSON 值。添加 `$ref` 分辨率要求和 
 组合关键字资源边界 
 ([SEP-2106](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2106>))。
11. 删除`notifications/elicitation/complete`通知和
`elicitationId` URL 模式引出请求字段，均在 
 `2025-11-25` 中介绍。在 
 [ 多往返请求 ](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/mrtr>) 模式下，
 客户端通过重试原始 
 请求来了解带外交互的结果，因此服务器发起的完成信号 - 以及使用的标识符到
将其关联起来——不再符合协议。需要跨重试关联 
 引出的服务器将其自己的标识符编码在 `requestState` 中。
12. 定义[错误代码分配策略](<https://modelcontextprotocol.io/specification/2026-07-28/basic/index#error-codes>)
对 JSON-RPC 服务器错误范围进行分区：`-32000` 到 `-32019` 仍然由 
 实现定义（现有的 SDK 用法不受继承），`-32020` 到 `-32099` 
 为 MCP 规范保留。相应地重新编号本草案中引入的错误代码
 — `HeaderMismatch` `-32001` → `-32020`、`MissingRequiredClientCapability`
 `-32003` → `-32021`、`UnsupportedProtocolVersion` `-32004` → `-32022` — 并将 
 `HeaderMismatchError` 添加到模式中，该模式以前仅存在于传输散文中。

## 已弃用

此处列出的功能仍然是规范的一部分，但计划根据 [ 功能生命周期和弃用策略 ](<https://modelcontextprotocol.io/community/feature-lifecycle>) 删除。新的实现不应采用它们。 [已弃用功能注册表](<https://modelcontextprotocol.io/specification/2026-07-28/deprecated>) 跟踪当前处于“已弃用”状态的每个功能。

1. 弃用 Roots、Sampling 和 Logging 功能
([SEP-2577](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2577>))。
 这些功能在弃用窗口期间仍保持完整功能，但新的 
 实现不应添加对它们的支持。建议迁移：通过工具参数、资源URI或服务器
配置而不是根传递
目录或文件；直接与 LLM 提供商 API 
 集成，而不是采样；登录到 `stderr` (stdio) 或使用 OpenTelemetry 而不是 
 日志记录。

2. 重新分类 HTTP+SSE 传输（自协议版本起已弃用）
`2025-03-26`) 在功能生命周期策略下已弃用 
 ([SEP-2596](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2596>))。
 迁移到 [Streamable HTTP](<https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/streamable-http>)。

3. 对 `includeContext` 值 `"thisServer"` 和 `"allServers"` 重新分类
（自协议版本 `2025-11-25` 起软弃用） as Deprecated
 ([SEP-2596](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2596>)).
 省略该字段或使用 `"none"`；这些值将在采样功能本身
之前被删除。

4. 弃用 OAuth 2.0 动态客户端注册协议
（[RFC7591](<https://datatracker.ietf.org/doc/html/rfc7591>)）作为客户端注册
机制有利于
 [客户端ID元数据文档](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization/client-registration#client-id-metadata-documents>)
（[PR #2858](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2858>)).
 它仍然可向后兼容 
 不支持客户端 ID 元数据文档的授权服务器。

## 其他架构更改

1. `schema.json` 现在可以正确反映 minimum/maximum/default 的 Typescript 定义是 `number`，而不仅仅是 `integers`。这是由于使用 `--defaultNumberType integer` ([PR#2710](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2710>)) 运行生成器引起的。

## 治理和流程更新

1. 采用规范
[功能生命周期和弃用策略](<https://modelcontextprotocol.io/community/feature-lifecycle>)
定义“活动”、“已弃用”和“已删除”功能状态、最短
十二个月的弃用窗口以及
 [已弃用功能的注册表](<https://modelcontextprotocol.io/specification/2026-07-28/deprecated>)
 （[SEP-2596](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2596>)）。

## 流程变更

1. 形式化 PR-基于 SEP 包含 Markdown 文件的工作流程 `seps/` 目录, PR-通过派生编号、赞助商责任和状态管理 PR 标签（[SEP-1850](<https://github.com/modelcontextprotocol/specification/pull/1850>)).

## 完整变更日志

有关自上次协议修订以来所做的所有更改的完整列表，
[请参阅GitHub](<https://github.com/modelcontextprotocol/specification/compare/2025-11-25...2026-07-28>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://modelcontextprotocol.io/llms.txt
> Use this file to discover all available pages before exploring further.

# Key Changes

<div id="enable-section-numbers" />

This document lists changes made to the Model Context Protocol (MCP) specification since
the previous revision, [2025-11-25](<https://modelcontextprotocol.io/specification/2025-11-25>).

## Major changes

1. Remove protocol-level sessions and the `Mcp-Session-Id` header from the Streamable HTTP transport. List endpoints (`tools/list`, `resources/list`, `prompts/list`) no longer vary per-connection. Servers that need cross-call state use explicit, server-minted handles passed as ordinary tool arguments ([SEP-2567](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2567>)).

2. Make MCP stateless: remove the `initialize`/`notifications/initialized` handshake. Every request now carries its protocol version and client capabilities in `_meta` (`io.modelcontextprotocol/protocolVersion`, `io.modelcontextprotocol/clientCapabilities`). Clients SHOULD identify themselves on each request (`io.modelcontextprotocol/clientInfo`), and servers SHOULD identify themselves in each result's `_meta` (`io.modelcontextprotocol/serverInfo`). Version mismatches return `UnsupportedProtocolVersionError` ([SEP-2575](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2575>)).

3. Add `server/discover`: servers MUST implement this RPC to advertise their supported protocol versions, capabilities, and identity. Clients MAY call it before any other request for up-front version selection, or use it as a backward-compatibility probe on STDIO ([SEP-2575](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2575>)).

4. Replace the HTTP GET endpoint and `resources/subscribe`/`resources/unsubscribe` with `subscriptions/listen`: a single long-lived POST-response stream for opted-in server-to-client change notifications. Clients opt in to specific types (`toolsListChanged`, `promptsListChanged`, `resourcesListChanged`, `resourceSubscriptions`); the server acknowledges and tags notifications with `io.modelcontextprotocol/subscriptionId`. Request-scoped notifications such as `notifications/progress` and `notifications/message` continue to flow on the response stream of the request they relate to, not the `subscriptions/listen` stream ([SEP-2575](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2575>)).

5. Remove `ping`, `logging/setLevel`, and `notifications/roots/list_changed`. Log level is now set per-request via `io.modelcontextprotocol/logLevel` in `_meta`; servers MUST NOT emit `notifications/message` for requests that did not include this field ([SEP-2575](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2575>)).

6. Move experimental tasks out of the core protocol and into an official extension (`io.modelcontextprotocol/tasks`). The redesigned extension replaces the blocking `tasks/result` method with polling via `tasks/get` and a new `tasks/update` for client-to-server input, removes `tasks/list`, and allows servers to return task handles unsolicited without per-request opt-in ([SEP-2663](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2663>)).

7. Multi Round-Trip Requests (MRTR) pattern introduced which replaces the previous approach of sending server-initiated requests, such as `roots/list`, `sampling/createMessage`, or `elicitation/create`. Servers return an `InputRequiredResult` (`resultType: "input_required"`) whose `inputRequests` field carries the requests for the additional information needed to process the request. Clients respond with `inputResponses` on a retry of the original request providing the requested information. ([SEP-2322](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2322>)).

8. All results now carry a required `resultType` field: `"complete"` for ordinary results and `"input_required"` for [multi round-trip request](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/mrtr>) interim results. Clients **MUST** treat results from earlier-protocol servers that omit the field as `"complete"` ([SEP-2322](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2322>)).

9. Remove SSE stream resumability and message redelivery (the `Last-Event-ID` header and SSE event IDs) from the Streamable HTTP transport. A broken response stream loses the in-flight request; clients **MUST** re-issue it as a new request with a new request ID ([SEP-2575](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2575>)).

## Minor changes

1. Add `extensions` field to `ClientCapabilities` and `ServerCapabilities` to support optional [extensions](<https://modelcontextprotocol.io/docs/extensions/overview>) beyond the core protocol.
2. Document OpenTelemetry trace context propagation conventions for `_meta` keys (`traceparent`, `tracestate`, `baggage`) ([SEP-414](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/414>)).
3. Servers **SHOULD** return tools from `tools/list` in a deterministic order to enable client-side caching and improve LLM prompt cache hit rates.
4. Require standard MCP request headers (`Mcp-Method`, `Mcp-Name`) on Streamable HTTP POST requests, and add support for custom headers from tool parameters via `x-mcp-header` ([SEP-2243](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2243>)).
5. Require `ttlMs` and `cacheScope` fields on results returned by `tools/list`, `prompts/list`, `resources/list`, `resources/read`, and `resources/templates/list` via a new `CacheableResult` interface. `ttlMs` is a freshness hint (in milliseconds) allowing clients to cache responses and reduce polling; `cacheScope` (`"public"` or `"private"`) controls whether shared intermediaries may cache the response. Both fields complement existing `listChanged` notifications ([SEP-2549](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2549>)).
6. Change resource not found error code from `-32002` to `-32602` (Invalid Params) to align with JSON-RPC specification.
7. Authorization servers **SHOULD** include the `iss` parameter in authorization responses per
   [RFC 9207](<https://datatracker.ietf.org/doc/html/rfc9207>), and MCP clients **MUST** validate a
   present `iss` against the recorded issuer before redeeming the authorization code
   ([SEP-2468](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2468>)).
8. Require MCP clients to specify an appropriate `application_type` during Dynamic Client
   Registration to avoid OpenID Connect redirect URI conflicts
   ([SEP-837](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/837>)).
9. Clarify that client credentials are bound to the authorization server that issued them:
   clients **MUST** key persisted credentials by the issuer identifier, **MUST NOT** reuse them
   with a different authorization server, and **MUST** re-register when the authorization server
   changes ([SEP-2352](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2352>)).
10. Loosen `inputSchema` and `outputSchema` to allow any JSON Schema 2020-12 keywords, and
    `structuredContent` to allow any JSON value. Add `$ref` resolution requirements and
    composition-keyword resource bounds
    ([SEP-2106](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2106>)).
11. Remove the `notifications/elicitation/complete` notification and the
    `elicitationId` field of URL mode elicitation requests, both introduced in
    `2025-11-25`. Under the
    [Multi Round-Trip Requests](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/mrtr>) pattern, the
    client learns the outcome of an out-of-band interaction by retrying the original
    request, so a server-initiated completion signal — and the identifier used to
    correlate it — no longer fit the protocol. Servers needing to correlate an
    elicitation across retries encode their own identifier in `requestState`.
12. Define an [error code allocation policy](<https://modelcontextprotocol.io/specification/2026-07-28/basic/index#error-codes>)
    partitioning the JSON-RPC server-error range: `-32000` to `-32019` remains
    implementation-defined (existing SDK usage is grandfathered), `-32020` to `-32099` is
    reserved for the MCP specification. Renumber the error codes introduced in this draft
    accordingly — `HeaderMismatch` `-32001` → `-32020`, `MissingRequiredClientCapability`
    `-32003` → `-32021`, `UnsupportedProtocolVersion` `-32004` → `-32022` — and add
    `HeaderMismatchError` to the schema, which previously existed only in transport prose.

## Deprecated

Features listed here remain part of the specification but are scheduled for removal under the [feature lifecycle and deprecation policy](<https://modelcontextprotocol.io/community/feature-lifecycle>). New implementations should not adopt them. The [deprecated features registry](<https://modelcontextprotocol.io/specification/2026-07-28/deprecated>) tracks every feature currently in the Deprecated state.

1. Deprecate the Roots, Sampling, and Logging features
   ([SEP-2577](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2577>)).
   These features remain fully functional during the deprecation window but new
   implementations should not add support for them. Suggested migrations: pass
   directories or files via tool parameters, resource URIs, or server
   configuration instead of Roots; integrate directly with LLM provider APIs
   instead of Sampling; log to `stderr` (stdio) or use OpenTelemetry instead of
   Logging.

2. Reclassify the HTTP+SSE transport (deprecated since protocol version
   `2025-03-26`) as Deprecated under the feature lifecycle policy
   ([SEP-2596](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2596>)).
   Migrate to [Streamable HTTP](<https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/streamable-http>).

3. Reclassify the `includeContext` values `"thisServer"` and `"allServers"`
   (soft-deprecated since protocol version `2025-11-25`) as Deprecated
   ([SEP-2596](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2596>)).
   Omit the field or use `"none"`; these values will be removed no later than
   the Sampling feature itself.

4. Deprecate the OAuth 2.0 Dynamic Client Registration Protocol
   ([RFC7591](<https://datatracker.ietf.org/doc/html/rfc7591>)) as a client registration
   mechanism in favor of
   [Client ID Metadata Documents](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization/client-registration#client-id-metadata-documents>)
   ([PR #2858](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2858>)).
   It remains available for backwards compatibility with authorization servers that do
   not support Client ID Metadata Documents.

## Other schema changes

1. `schema.json` now correctly reflects that the Typescript definition of minimum/maximum/default are `number`'s and not just `integers`. This was caused by running the generator using `--defaultNumberType integer` ([PR#2710](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2710>)).

## Governance and process updates

1. Adopt a specification
   [feature lifecycle and deprecation policy](<https://modelcontextprotocol.io/community/feature-lifecycle>)
   defining the Active, Deprecated, and Removed feature states, a minimum
   twelve-month deprecation window, and a
   [registry of deprecated features](<https://modelcontextprotocol.io/specification/2026-07-28/deprecated>)
   ([SEP-2596](<https://github.com/modelcontextprotocol/modelcontextprotocol/pull/2596>)).

## Process changes

1. Formalize PR-based SEP workflow with markdown files in `seps/` directory, PR-derived numbering, sponsor responsibilities, and status management via PR labels ([SEP-1850](<https://github.com/modelcontextprotocol/specification/pull/1850>)).

## Full changelog

For a complete list of all changes that have been made since the last protocol revision,
[see GitHub](<https://github.com/modelcontextprotocol/specification/compare/2025-11-25...2026-07-28>).


</details>
