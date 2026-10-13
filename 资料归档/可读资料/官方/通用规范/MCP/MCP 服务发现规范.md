# MCP 服务发现规范

规定 server/discover 请求、返回的版本、能力和身份，以及何时调用和数据类型；属于协议接口定义。

- 来源：通用规范／MCP
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-11](<https://modelcontextprotocol.io/specification/2026-07-28/server/discover>)；规范版本：[2026-07-28](<https://modelcontextprotocol.io/specification/2026-07-28/server/discover>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://modelcontextprotocol.io/specification/2026-07-28/server/discover>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/MCP/MCP 服务发现规范.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://modelcontextprotocol.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 发现

<div id="enable-section-numbers" />

`server/discover` 允许客户端在发送任何其他请求之前查询服务器支持的协议版本、
 功能和身份。服务器**MUST**
实现它。

## 请求

该请求不携带超出标准`_meta`的主体参数：

```json theme={null}
{
  "jsonrpc": "2.0",
  "id": "discover-1",
  "method": "server/discover",
  "params": {
    "_meta": {
      "io.modelcontextprotocol/protocolVersion": "2026-07-28",
      "io.modelcontextprotocol/clientInfo": {
        "name": "ExampleClient",
        "version": "1.0.0"
      },
      "io.modelcontextprotocol/clientCapabilities": {}
    }
  }
}
```

## 回应

服务器回复其支持的协议版本、功能和
身份。此操作支持[缓存](<https://modelcontextprotocol.io/specification/2026-07-28/server/utilities/caching>)。

```json theme={null}
{
  "jsonrpc": "2.0",
  "id": "discover-1",
  "result": {
    "resultType": "complete",
    "supportedVersions": ["2026-07-28"],
    "capabilities": {
      "tools": {},
      "resources": {}
    },
    "_meta": {
      "io.modelcontextprotocol/serverInfo": {
        "name": "ExampleServer",
        "version": "1.0.0"
      }
    },
    "instructions": "This server provides weather and resource utilities.",
    "ttlMs": 3600000,
    "cacheScope": "public"
  }
}
```

## 何时致电

调用 `server/discover` 对于客户端来说是可选的 — 如果服务器不支持请求的版本，客户端可以内联调用任何 
RPC 并处理 
[`UnsupportedProtocolVersionError`](<https://modelcontextprotocol.io/specification/2026-07-28/schema#unsupportedprotocolversionerror>)
。然而，`server/discover`
在两种情况下很有用：

* **呈现服务器信息。**而客户端不需要调用
`server/discover` 使用服务器，这是在单个请求中检索 
 服务器的身份、功能和支持的版本的便捷方法。
 例如，客户端可以从 
 单个 `server/discover` 响应中呈现服务器支持的功能，而不是使用单独的 
 `tools/list`、`prompts/list` 和 `resources/list` 请求。
* **stdio 向后兼容性探针。** 在 stdio 上，没有每个请求
HTTP 驱动回退的状态代码。支持现代
（每个请求`_meta`）和传统（`initialize`握手）服务器**SHOULD**
的客户端首先发送`server/discover`；有关后备规则，请参阅
 [stdio：向后兼容性](<https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/stdio#backward-compatibility>)
。

有关完整的版本选择流程，请参阅[协议版本协商](<https://modelcontextprotocol.io/specification/2026-07-28/basic/versioning#protocol-version-negotiation>)
。对于为
未知方法返回的HTTP-特定状态代码，请参阅传输中的[协议版本标头](<https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/streamable-http#protocol-version-header>)
部分。

## 数据类型

### DiscoverResult

发现结果包括：

* `supportedVersions`：服务器支持的协议版本。客户应该
为后续请求选择其中之一。
* `capabilities`：服务器支持的功能（工具、资源、提示、
等）
* `_meta['io.modelcontextprotocol/serverInfo']`：服务器的名称和版本
软件。服务器 **SHOULD** 包含此字段。
* `instructions`：针对法学硕士如何使用的可选自然语言指导
该服务器有效



**注释：**

`serverInfo`由服务器自报告，未经
协议验证。它用于显示、记录和调试。客户端 **SHOULD
 NOT** 使用它来改变自己的行为，并且 **SHOULD NOT** 依赖它来做出 
 安全决策。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://modelcontextprotocol.io/llms.txt
> Use this file to discover all available pages before exploring further.

# Discovery

<div id="enable-section-numbers" />

`server/discover` lets a client query a server's supported protocol versions,
capabilities, and identity before sending any other requests. Servers **MUST**
implement it.

## Request

The request carries no body parameters beyond the standard `_meta`:

```json theme={null}
{
  "jsonrpc": "2.0",
  "id": "discover-1",
  "method": "server/discover",
  "params": {
    "_meta": {
      "io.modelcontextprotocol/protocolVersion": "2026-07-28",
      "io.modelcontextprotocol/clientInfo": {
        "name": "ExampleClient",
        "version": "1.0.0"
      },
      "io.modelcontextprotocol/clientCapabilities": {}
    }
  }
}
```

## Response

The server replies with its supported protocol versions, capabilities, and
identity. This operation supports [caching](<https://modelcontextprotocol.io/specification/2026-07-28/server/utilities/caching>).

```json theme={null}
{
  "jsonrpc": "2.0",
  "id": "discover-1",
  "result": {
    "resultType": "complete",
    "supportedVersions": ["2026-07-28"],
    "capabilities": {
      "tools": {},
      "resources": {}
    },
    "_meta": {
      "io.modelcontextprotocol/serverInfo": {
        "name": "ExampleServer",
        "version": "1.0.0"
      }
    },
    "instructions": "This server provides weather and resource utilities.",
    "ttlMs": 3600000,
    "cacheScope": "public"
  }
}
```

## When to Call

Calling `server/discover` is optional for clients — a client may invoke any
RPC inline and handle
[`UnsupportedProtocolVersionError`](<https://modelcontextprotocol.io/specification/2026-07-28/schema#unsupportedprotocolversionerror>)
if the server does not support the requested version. However, `server/discover`
is useful in two scenarios:

* **Presenting server information.** While a client doesn't need to call
  `server/discover` to use the server, it's a convenient way to retrieve the
  server's identity, capabilities, and supported versions in a single request.
  For example, a client can present the capabilities a server supports from a
  single `server/discover` response instead of probing with separate
  `tools/list`, `prompts/list`, and `resources/list` requests.
* **stdio backward-compatibility probe.** On stdio, there is no per-request
  HTTP status code to drive fallback. A client that supports both modern
  (per-request `_meta`) and legacy (`initialize` handshake) servers **SHOULD**
  send `server/discover` first; see
  [stdio: Backward Compatibility](<https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/stdio#backward-compatibility>)
  for the fallback rules.

See [Protocol Version Negotiation](<https://modelcontextprotocol.io/specification/2026-07-28/basic/versioning#protocol-version-negotiation>)
for the full version-selection flow. For HTTP-specific status codes returned for
unknown methods, see the [Protocol Version Header](<https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/streamable-http#protocol-version-header>)
section in Transports.

## Data Types

### DiscoverResult

A discovery result includes:

* `supportedVersions`: Protocol versions the server supports. The client should
  choose one of these for subsequent requests.
* `capabilities`: Capabilities the server supports (tools, resources, prompts,
  etc.)
* `_meta['io.modelcontextprotocol/serverInfo']`: Name and version of the server
  software. Servers **SHOULD** include this field.
* `instructions`: Optional natural-language guidance for LLMs on how to use
  this server effectively



**注释：**

  `serverInfo` is self-reported by the server and is not verified by the
  protocol. It is intended for display, logging, and debugging. Clients **SHOULD
  NOT** use it to change their behavior, and **SHOULD NOT** rely on it for
  security decisions.


</details>
