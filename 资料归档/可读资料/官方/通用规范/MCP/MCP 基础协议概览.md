# MCP 基础协议概览

介绍 JSON-RPC 消息、请求响应、错误、通知、认证和 JSON Schema，说明版本相关的无状态行为。

- 来源：通用规范／MCP
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-11](<https://modelcontextprotocol.io/specification/2026-07-28/basic>)；规范版本：[2026-07-28](<https://modelcontextprotocol.io/specification/2026-07-28/basic>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://modelcontextprotocol.io/specification/2026-07-28/basic>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/MCP/MCP 基础协议概览.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://modelcontextprotocol.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 概述

<div id="enable-section-numbers" />

Model Context Protocol 由几个协同工作的关键组件组成：

* **基本协议**：核心JSON-RPC消息类型
* **版本控制和兼容性**：协议版本协商、扩展协商以及与早期协议修订版的互操作性
* **消息模式**：核心协议支持的消息模式，包括请求和响应、多往返请求（MRTR）以及订阅和通知
* **授权**：基于HTTP-的传输的身份验证和授权框架
* **服务器功能**：服务器公开的资源、提示和工具
* **客户端功能**：客户端提供的启发、采样和根目录列表
* **实用程序**：横切关注点，例如日志记录和参数完成

所有实现**MUST**都支持基本协议、版本控制、
和消息模式。其他组件 **MAY** 可以根据 
 应用程序的特定需求来实现。

这些协议层建立了清晰的关注点分离，同时支持客户端和服务器之间丰富的
交互。模块化设计允许实现
准确支持他们所需的功能。

## 留言

MCP 客户端和服务器 **MUST** 之间的所有消息都遵循 
[JSON-RPC 2.0](<https://www.jsonrpc.org/specification>) 规范。该协议定义了
这些类型的消息：

### 要求

[请求](<https://modelcontextprotocol.io/specification/2026-07-28/schema#jsonrpcrequest>)从客户端发送到服务器，以启动操作。

```typescript theme={null}
{
  jsonrpc: "2.0";
  id: string | number;
  method: string;
  params?: {
    [key: string]: unknown;
  };
}
```

* 请求 **MUST** 包含字符串或整数 ID。
* 与基础 JSON-RPC 不同，ID **MUST NOT** 为 `null`。
* 请求 ID **MUST NOT** 与发送者发出的任何其他请求的 ID 匹配，并且
尚未收到回复。

### 回应

响应是为了回复请求而发送的，其中包含操作的结果或错误。

#### 结果回应

[结果响应](<https://modelcontextprotocol.io/specification/2026-07-28/schema#jsonrpcresultresponse>)在操作成功完成时发送。

```typescript theme={null}
{
  jsonrpc: "2.0";
  id: string | number;
  result: {
    resultType: string;
    [key: string]: unknown;
  };
}
```

* 结果响应 **MUST** 包含与其对应的请求相同的 ID。
* 结果响应 **MUST** 包含 `result` 字段。
* `result` **MAY** 遵循任何 JSON 对象结构。
* `result`**MUST**包括`resultType`字段来指示结果的类型。

##### ResultType

结果中的`resultType`字段指示返回结果的类型。 MCP支持多态结果类型，
允许服务器根据请求的结果返回不同的结构。 `resultType`字段是一个字符串，客户端
可以使用它来确定如何解析和处理`result`对象。

* `"complete"`中的`resultType`表示请求成功完成，结果包含最终内容。
* `"input_required"` 的 `resultType` 表示请求不完整，需要更多信息来处理该请求。结果包含一个 [`InputRequiredResult`](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/mrtr#inputrequiredresult>) 对象以及所需的附加信息。
* 扩展 **MAY** 添加额外的 `ResultType` 值。支持的 `ResultType` 值 **MUST** 的集合是根据核心协议中定义的集合创建的，并且包括通过功能通告的支持的扩展的任何附加值。
* 客户端 **MUST** 无法识别的任何值 `resultType` 将被视为无效。
* 为了向后兼容实现早期协议版本（不包括 `resultType`）的服务器，客户端 **MUST** 将不存在的 `resultType` 视为 `"complete"`。

#### 错误响应

[错误响应](<https://modelcontextprotocol.io/specification/2026-07-28/schema#jsonrpcerrorresponse>)在操作失败或遇到错误时发送。

```typescript theme={null}
{
  jsonrpc: "2.0";
  id?: string | number;
  error: {
    code: number;
    message: string;
    data?: unknown;
  }
}
```

* 错误响应 **MUST** 包含与其对应的请求相同的 ID（除非由于格式错误的请求而无法读取 ID 的错误情况）。
* 错误响应 **MUST** 包括带有 `code` 和 `message` 的 `error` 字段。
* 错误代码 **MUST** 为整数。
* 错误响应 **MAY** 包括一个 `data` 成员以及任何类型的附加信息，例如
作为嵌套错误。

#### 错误代码

MCP 使用标准 JSON-RPC 2.0 错误代码（`-32700`、`-32600` 至 `-32603`）
 来处理一般协议故障。

JSON-RPC 2.0 为实现定义的 
 服务器错误保留范围 `-32000` 到 `-32099`。 MCP 将此范围划分如下：

* **`-32000` 至 `-32019` — 遗留。** 此子范围中的代码由
该政策出台前的实施情况。新代码 **MUST NOT** 是 
 分配在此子范围中，并且新实现 **SHOULD NOT** 完全使用此子范围中的代码 
。除了 `-32002`（见下文）之外，接收者 
 **MUST NOT** 假定这些代码具有任何特定含义。
* **`-32020` 至 `-32099` — 为 MCP 规范保留。** 错误代码
  在这个子范围内完全由 MCP 规格和
  记录在 [图式](<https://modelcontextprotocol.io/specification/2026-07-28/schema>)。实施
  **MUST NOT** 发出此子范围中未定义的任何代码
  规格和 **MUST** 仅将定义的代码与指定的代码一起使用
  含义。

MCP 定义以下错误代码：

|代码|名称|
| - | - |
| `-32020` | [`HeaderMismatch`](<https://modelcontextprotocol.io/specification/2026-07-28/schema#headermismatcherror>) |
| `-32021` | [`MissingRequiredClientCapability`](<https://modelcontextprotocol.io/specification/2026-07-28/schema#missingrequiredclientcapabilityerror>) |
| `-32022` | [`UnsupportedProtocolVersion`](<https://modelcontextprotocol.io/specification/2026-07-28/schema#unsupportedprotocolversionerror>) |

早期协议版本定义的代码仍然保留，不会被
重用。此协议版本 **MUST NOT** 的实现发出以下代码：

* `-32002` — 找不到资源（2025-11-25 及更早版本；替换为 `-32602`）。
  客户 [**SHOULD** 仍然
  接受 `-32002`](<https://modelcontextprotocol.io/specification/2026-07-28/server/resources#error-handling>) 来自
  实施早期版本的服务器。
* `-32042` — 需要 URL 诱导（仅限 2025-11-25）。

纯粹本地实现的错误（例如，SDK 内引发的请求
超时）当前未按此
规范分配代码。在 JSON-RPC- 形状的 
 结构中呈现本地错误的实现应确保它们不会被误认为是从 
 对等方收到的错误。该规范的未来版本可能会为保留子范围中的
常见本地错误条件定义标准代码。

用于本规范 **SHOULD** 未定义目的的新错误代码将 
 分配在 JSON-RPC 保留范围之外（`-32768` 至 `-32000`）； 
 整数空间的剩余部分可用于应用程序定义的错误。

### 通知

[通知](<https://modelcontextprotocol.io/specification/2026-07-28/schema#jsonrpcnotification>)作为单向消息从客户端发送到服务器，反之亦然。
接收者**MUSTNOT**发送响应。

```typescript theme={null}
{
  jsonrpc: "2.0";
  method: string;
  params?: {
    [key: string]: unknown;
  };
}
```

* 通知 **MUST NOT** 包括 ID。

### 消息模式

Model Context Protocol (MCP) 支持多种定义客户端和服务器交互方式的 [ 消息模式 ](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns>)：

1. **[请求和响应](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns#request-and-response>)**：客户端向服务器发送请求，服务器返回结果或错误。
2. **[多往返请求 (MRTR)](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns#multi-round-trip-requests>)**：服务器需要额外的客户端输入（采样、启发或根）才能完成请求。
3. **[订阅和通知](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns#subscribe-and-notify>)**：客户端订阅来自服务器的通知流，这些通知在发生时发送。

## 无国籍状态

Model Context Protocol（MCP）是一个**无状态协议**：处理请求所需的所有
信息都包含在请求本身中。
服务器独立处理每个请求；不应从以前的请求推断出任何状态
，即使是同一连接或流上的请求。

具体来说：

* 服务器 **MUST NOT** 依赖于同一连接上的先前请求
建立上下文（例如，功能、协议版本、客户端身份）。
 每个请求在其 [`_meta`](<#_meta>) 字段中提供此元数据。
* 服务器**SHOULD**准备好处理与多个相关的请求
任务、线程或对话。
* 服务器 **SHOULD NOT** 要求客户端重用相同的连接或进程来
进行相关操作。
* 客户端 **SHOULD NOT** 使用单个任务、线程或对话作为
stdio 进程的生命周期边界。
* 需要跨越多个请求的状态（例如，长时间运行的任务，
应用程序级句柄）**MUST** 由客户端在每个请求上传递的显式标识符 
 引用。



**注释：**

这意味着开放连接（例如 STDIO 进程）不是 
 对话或会话：客户端可以在同一 
 传输上交错不相关的请求，并且服务器不得将连接或进程身份视为对话或会话连续性的 
 代理。


像
[`subscriptions/listen`](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/subscriptions>)
这样的长期请求仍然是request/response；响应只是一个开放的通知流。
它们的状态仅限于请求本身，而不是下面的连接。



**说明：**

有关每个请求模型如何映射到 SDK 代码的演练，请参阅 
 [ 架构指南 ](<https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture#example>)。


## 授权

MCP 提供了一个 [授权 ](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization>) 框架，与 HTTP 一起使用。
使用基于 HTTP- 的传输 **SHOULD** 的实现符合此要求规范，
而使用STDIO传输**SHOULDNOT**的实现遵循此规范，
而是从环境中检索凭证。

此外，客户端和服务器 **MAY** 协商自己的自定义身份验证和 
 授权策略。

如需进一步讨论并为 MCP 身份验证机制的发展做出贡献，请加入 
 我们的 
[GitHub 讨论 ](<https://github.com/modelcontextprotocol/specification/discussions>)
 来帮助塑造协议的未来！

## 模式

协议的完整规范定义为
[TypeScript模式](<https://github.com/modelcontextprotocol/specification/blob/main/schema/2026-07-28/schema.ts>)。
这是所有协议消息和结构的真实来源。

还有一个
[JSON架构](<https://github.com/modelcontextprotocol/specification/blob/main/schema/2026-07-28/schema.json>)、
是从TypeScript事实来源自动生成的，与
各种自动化工具一起使用。

## JSON 架构用法

Model Context Protocol 使用 JSON 模式在整个协议中进行验证。本节阐明如何在 MCP 消息中使用 JSON 架构。

### 模式方言

MCP 支持具有以下规则的 JSON Schema：

1. **默认方言**：当架构不包含`$schema`字段时，默认为[JSON架构2020-12](<https://json-schema.org/draft/2020-12/schema>)
2. **显式方言**：架构MAY包含一个`$schema`字段来指定不同的方言
3. **支持的方言**：实现MUST至少支持2020-12和SHOULD文档它们支持哪些其他方言
4. **建议**：实现者是RECOMMENDED使用JSON架构2020-12。

### 用法示例

#### 默认方言（2020-12）：

```json theme={null}
{
  "type": "object",
  "properties": {
    "name": { "type": "string" },
    "age": { "type": "integer", "minimum": 0 }
  },
  "required": ["name"]
}
```

#### 显式方言（草案-07）：

```json theme={null}
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "name": { "type": "string" },
    "age": { "type": "integer", "minimum": 0 }
  },
  "required": ["name"]
}
```

### 实施要求

* 对于没有显式 `$schema` 字段的架构，客户端和服务器 **MUST** 支持 JSON 架构 2020-12
* 客户端和服务器 **MUST** 根据其声明或默认方言验证模式。它们 **MUST** 通过返回指示不支持方言的适当错误来优雅地处理不支持的方言。
* 客户端和服务器 **SHOULD** 记录它们支持哪些模式方言

### 模式验证

* 模式 **MUST** 根据其声明或默认方言有效

### `$ref` 分辨率

JSON 架构 2020-12 允许 `$ref` 指向绝对值 URI。实现 **MUST NOT**
 自动取消引用解析为网络 URI 的 `$ref` 值。

实现**MAY**提供了一种选择加入模式，可以获取非本地`$ref`，但默认情况下
**MUST**被禁用，并且**SHOULD**强制执行主机白名单或在
最小拒绝环回、本地链路和专用网络地址，应用超时和
大小限制，并记录取消引用的URI。

由于未解析的外部 `$ref` **SHOULD** 而无法验证的模式将被拒绝 
，而不是默默地视为许可。

### 作文-关键词资源使用

组合关键字（`anyOf`、`oneOf`、`allOf`、`if`/`then`/`else`）和`$defs`启用
表达模式，但成本高昂验证。实现**SHOULD**应用
合理的界限，例如最大模式深度、子模式总数的上限、
或每次验证时间预算，以防止恶意模式充当针对验证器的拒绝服务
向量。

## 一般字段

### `_meta`

`_meta` property/parameter 由 MCP 使用，允许客户端和服务器 
 将附加元数据附加到其交互中。

MCP 为协议级元数据保留了某些键名称，如下所示；
 实现 **MUST NOT** 对这些键的值进行假设。

**密钥名称格式：**有效的`_meta`密钥名称有两段：可选的**前缀**和**名称**。

**前缀：**

* 如果指定，MUST 是一系列由点 (`.`) 分隔的标签，后跟斜线 (`/`)。
  * 标签MUST以字母开头，以字母或数字结尾；内部字符可以是字母、数字或连字符 (`-`)。
  * 实现 SHOULD 使用反向 DNS 表示法（例如，`com.example/` 而不是 `example.com/`）。
* 第二个标签为 `modelcontextprotocol` 或 `mcp` 的任何前缀都是 ** 保留 ** 以供 MCP 使用。
  * 例如：`io.modelcontextprotocol/`、`dev.mcp/`、`org.modelcontextprotocol.api/`、`com.mcp.tools/`均保留。
  * 然而，`com.example.mcp/` 被保留为 NOT，因为第二个标签是 `example`。

**名称：**

* 除非为空，否则 MUST 以字母数字字符 (`[a-z0-9A-Z]`) 开头和结尾。
* MAY 包含连字符 (`-`)、下划线 (`_`)、点 (`.`) 以及其间的字母数字。

**保留键：**

本规范保留了以下 `_meta` 键：

|钥匙|描述|定义于|
| - | - | - |
| `progressToken` |将请求选择为进度通知|[进度](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/progress>)|
| `io.modelcontextprotocol/protocolVersion` |请求的协议版本|每个请求的协议字段（如下）|
| `io.modelcontextprotocol/clientInfo` |客户端名称和版本|每个请求的协议字段（如下）|
| `io.modelcontextprotocol/clientCapabilities` |与请求相关的客户端功能|每个请求的协议字段（如下）|
| `io.modelcontextprotocol/logLevel` |服务器应为请求发出的最低日志级别|[记录](<https://modelcontextprotocol.io/specification/2026-07-28/server/utilities/logging>)|
| `io.modelcontextprotocol/subscriptionId` |将通知与其原始订阅关联起来|[订阅](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/subscriptions>)|
| `traceparent`, `tracestate`, `baggage` |OpenTelemetry 跟踪上下文传播|OpenTelemetry 跟踪上下文（如下）|

官方 [ 扩展 ](<https://modelcontextprotocol.io/specification/2026-07-28/basic/versioning#extension-negotiation>)
 在 `io.modelcontextprotocol/` 前缀下定义额外的 `_meta` 键，
 第三方扩展使用自己的供应商前缀。
 在这两种情况下，键都在扩展的文档中指定。

**每个请求协议字段：**

客户端请求在`_meta`中携带以下`io.modelcontextprotocol/*`字段；标记为必填的
字段**MUST**包含在每个请求中。服务器使用这些
来识别正在使用的协议版本和功能，而不依赖于任何
先前的连接状态。有关版本协商规则，请参阅
[版本控制和兼容性][生命周期]。

|钥匙|类型|必填|描述|
| - | - | - | - |
| `io.modelcontextprotocol/protocolVersion` | `string` |是的|此请求的协议版本（例如，`"2026-07-28"`）|
| `io.modelcontextprotocol/clientInfo` | `Implementation` |否|客户端名称和版本|
| `io.modelcontextprotocol/clientCapabilities` | `ClientCapabilities` |是的|与此请求相关的客户端功能|
| `io.modelcontextprotocol/logLevel` | `LoggingLevel` |否|服务器应为此请求发出的最低日志级别|

缺少任何必填字段的请求格式错误；服务器 **MUST** 拒绝它，并显示 
JSON-RPC 错误代码 `-32602`（无效参数）。在HTTP上，响应状态**MUST**为
`400 Bad Request`。

客户端 **SHOULD** 在每个请求 
 上都包含 `io.modelcontextprotocol/clientInfo`，除非专门配置为不这样做。

服务器 **MUST NOT** 依赖于客户端未声明的功能。如果
处理请求需要客户端未包含在
`io.modelcontextprotocol/clientCapabilities`中的功能，则服务器**MUST**返回
[`MissingRequiredClientCapabilityError`](<https://modelcontextprotocol.io/specification/2026-07-28/schema#missingrequiredclientcapabilityerror>)
(`-32021`)，其`data.requiredCapabilities`列出了缺少的功能。在
HTTP上，响应状态**MUST**为`400 Bad Request`。

**每个响应协议字段：**

服务器**SHOULD**在
每个结果的`_meta`中包含以下`io.modelcontextprotocol/*`字段，除非专门配置不这样做，
在不依赖任何先前连接状态的情况下识别自己：

|钥匙|类型|必填|描述|
| - | - | - | - |
| `io.modelcontextprotocol/serverInfo` | `Implementation` |否|服务器名称和版本|



**注释：**

`io.modelcontextprotocol/clientInfo`和`io.modelcontextprotocol/serverInfo`
由发送者自行报告，未经协议验证。它们是
，用于显示、记录和调试。实现 **SHOULD NOT**
 使用它们来更改客户端或服务器的行为，并且 **SHOULD NOT**
 依赖它们进行安全决策。


在通过 [`subscriptions/listen`][ 订阅监听 ] 流传递的通知中，
 服务器 **MUST** 将 `io.modelcontextprotocol/subscriptionId` 包含在 `_meta` 中，以便 
 客户端可以将通知与原始订阅请求相关联。

[lifecycle]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/versioning>

[subscriptions-listen]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/subscriptions>

**OpenTelemetry 跟踪上下文：**

作为上述前缀要求的例外，键 `traceparent`、`tracestate` 和 
`baggage` 保留用于 [OpenTelemetry](<https://opentelemetry.io/>) 跟踪上下文传播。
存在时，它们的值 MUST 遵循 [W3C 跟踪分别为上下文](<https://www.w3.org/TR/trace-context/>)
和[W3C行李](<https://www.w3.org/TR/baggage/>)格式。

存在此异常是为了保持与现有实现的兼容性，并且
[OpenTelemetry 语义约定 MCP](<https://opentelemetry.io/docs/specs/semconv/gen-ai/mcp/>).

`_meta` 中跟踪上下文的非规范示例：

```json theme={null}
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "tools/call",
  "params": {
    "name": "get_weather",
    "arguments": {
      "location": "New York"
    },
    "_meta": {
      "traceparent": "00-0af7651916cd43dd8448eb211c80319c-00f067aa0ba902b7-01"
    }
  }
}
```

### `icons`

`icons` 属性为服务器提供了一种标准化方法来公开其资源、工具、提示和实现的可视标识符。图标通过提供视觉上下文并提高可用功能的可发现性来增强用户界面。

图标表示为 `Icon` 对象的数组，其中每个图标包括：

* `src`：指向图标资源的URI（必填）。这可以是：
  * 指向图像文件的 HTTP/HTTPS URL
  * 带有base64编码图像数据的数据URI
* `mimeType`：如果服务器类型缺失或通用，则可选 MIME 类型
* `sizes`：可选的尺寸规格数组（例如，`["48x48"]`、`["any"]` 用于可扩展格式，例如 SVG 或 `["48x48", "96x96"]` 用于多种尺寸）
* `theme`：图标背景的可选主题首选项（`light` 或 `dark`）

**必需的MIME类型支持：**

支持渲染图标**MUST**的客户端至少支持以下MIME类型：

* `image/png` - PNG 图像（安全、通用兼容性）
* `image/jpeg`（和 `image/jpg`）- JPEG 图像（安全、通用兼容性）

支持渲染图标 **SHOULD** 的客户端还支持：

* `image/svg+xml` - SVG 图像（可扩展，但需要采取如下所述的安全预防措施）
* `image/webp` - WebP 图像（现代、高效的格式）

**安全考虑：**

图标元数据 **MUST** 的使用者在处理图标时采取适当的安全预防措施以防止泄露：

* 将图标元数据和图标字节视为不可信输入，并防御网络、隐私和解析风险。
* 确保图标 URI 是 HTTPS 或 `data:` URI。客户端 **MUST** 拒绝使用不安全方案和重定向的图标 URI，例如 `javascript:`、`file:`、`ftp:`、`ws:` 或本地应用程序 URI 方案。
  * 禁止方案更改和重定向到不同来源的主机。
* 能够抵御因超大图像、大尺寸或过多帧（例如 GIF 中）引起的资源耗尽攻击。
  * 消费者 **MAY** 设置图像和内容大小的限制。
* 获取没有凭据的图标。请勿发送 cookie、`Authorization` 标头或客户端凭据。
* 验证图标 URI 是否与服务器来自同一来源。这可以最大限度地降低向第三方暴露数据或跟踪信息的风险。
* 获取和渲染图标时请务必小心，因为有效负载 **MAY** 包含可执行内容（例如，SVG 内嵌 [ JavaScript](<https://www.w3.org/TR/SVG11/script.html>) 或 [ 扩展功能 ](<https://www.w3.org/TR/SVG11/extend.html>)）。
  * 消费者 **MAY** 选择在渲染之前禁止特定文件类型或以其他方式清理图标文件。
* 在渲染之前验证 MIME 类型和文件内容。将 MIME 类型信息视为建议信息。通过魔法字节检测内容类型；拒绝不匹配或未知类型。
  * 维护严格的图像类型许可名单。

**用法：**

图标可以附加到：

* `Implementation`：MCP server/client 实现的视觉标识符
* `Tool`：工具功能的可视化表示
* `Prompt`：与提示模板一起显示的图标
* `Resource`：不同资源类型的可视化指示器

可以提供多个图标来支持不同的显示上下文和分辨率。客户应根据其UI要求选择最合适的图标。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://modelcontextprotocol.io/llms.txt
> Use this file to discover all available pages before exploring further.

# Overview

<div id="enable-section-numbers" />

The Model Context Protocol consists of several key components that work together:

* **Base Protocol**: Core JSON-RPC message types
* **Versioning and Compatibility**: Protocol version negotiation, extension negotiation, and interoperability with earlier protocol revisions
* **Message Patterns**: Messaging patterns supported by the core protocol including request and response, multi round-trip requests (MRTR), and subscribe and notify
* **Authorization**: Authentication and authorization framework for HTTP-based transports
* **Server Features**: Resources, prompts, and tools exposed by servers
* **Client Features**: Elicitation, sampling and root directory lists provided by clients
* **Utilities**: Cross-cutting concerns like logging and argument completion

All implementations **MUST** support the base protocol, versioning,
and the message patterns. Other components **MAY** be implemented based on the specific needs of the
application.

These protocol layers establish clear separation of concerns while enabling rich
interactions between clients and servers. The modular design allows implementations to
support exactly the features they need.

## Messages

All messages between MCP clients and servers **MUST** follow the
[JSON-RPC 2.0](<https://www.jsonrpc.org/specification>) specification. The protocol defines
these types of messages:

### Requests

[Requests](<https://modelcontextprotocol.io/specification/2026-07-28/schema#jsonrpcrequest>) are sent from the client to the server, to initiate an operation.

```typescript theme={null}
{
  jsonrpc: "2.0";
  id: string | number;
  method: string;
  params?: {
    [key: string]: unknown;
  };
}
```

* Requests **MUST** include a string or integer ID.
* Unlike base JSON-RPC, the ID **MUST NOT** be `null`.
* The request ID **MUST NOT** match the ID of any other request the sender has issued and
  not yet received a response for.

### Responses

Responses are sent in reply to requests, containing either the result or error of the operation.

#### Result Responses

[Result responses](<https://modelcontextprotocol.io/specification/2026-07-28/schema#jsonrpcresultresponse>) are sent when the operation completes successfully.

```typescript theme={null}
{
  jsonrpc: "2.0";
  id: string | number;
  result: {
    resultType: string;
    [key: string]: unknown;
  };
}
```

* Result responses **MUST** include the same ID as the request they correspond to.
* Result responses **MUST** include a `result` field.
* The `result` **MAY** follow any JSON object structure.
* The `result` **MUST** include a `resultType` field to indicate the type of the result.

##### ResultType

The `resultType` field in a result indicates the type of the result being returned. MCP supports polymorphic result types,
allowing servers to return different structures based on the outcome of the request. The `resultType` field is a string that clients
can use to determine how to parse and handle the `result` object.

* A `resultType` of `"complete"` indicates the request completed successfully and the result contains the final content.
* A `resultType` of `"input_required"` indicates the request is incomplete and more information is needed to process the request. The result contains an [`InputRequiredResult`](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/mrtr#inputrequiredresult>) object with additional information needed.
* Extensions **MAY** add additional `ResultType` values. The set of supported `ResultType` values **MUST** be created from the set defined in the core protocol and include any additional values of supported extensions that are advertised via capabilities.
* A `resultType` of any value unrecognized by the client **MUST** be considered invalid.
* For backward compatibility with servers implementing earlier protocol versions, which do not include `resultType`, clients **MUST** treat an absent `resultType` as `"complete"`.

#### Error Responses

[Error responses](<https://modelcontextprotocol.io/specification/2026-07-28/schema#jsonrpcerrorresponse>) are sent when the operation fails or encounters an error.

```typescript theme={null}
{
  jsonrpc: "2.0";
  id?: string | number;
  error: {
    code: number;
    message: string;
    data?: unknown;
  }
}
```

* Error responses **MUST** include the same ID as the request they correspond to (except in error cases where the ID could not be read due a malformed request).
* Error responses **MUST** include an `error` field with a `code` and `message`.
* Error codes **MUST** be integers.
* Error responses **MAY** include a `data` member with additional information of any type, such
  as nested errors.

#### Error Codes

MCP uses the standard JSON-RPC 2.0 error codes (`-32700`, `-32600` to `-32603`)
for general protocol failures.

JSON-RPC 2.0 reserves the range `-32000` to `-32099` for implementation-defined
server errors. MCP partitions this range as follows:

* **`-32000` to `-32019` — legacy.** Codes in this sub-range were allocated by
  implementations before this policy was introduced. New codes **MUST NOT** be
  allocated in this sub-range, and new implementations **SHOULD NOT** use codes
  from this sub-range at all. Apart from `-32002` (see below), receivers
  **MUST NOT** assume any specific meaning for these codes.
* **`-32020` to `-32099` — reserved for the MCP specification.** Error codes
  in this sub-range are defined exclusively by the MCP specification and
  recorded in the [schema](<https://modelcontextprotocol.io/specification/2026-07-28/schema>). Implementations
  **MUST NOT** emit any code from this sub-range that is not defined by this
  specification and **MUST** use defined codes only with their specified
  meanings.

MCP defines the following error codes:

| Code | Name |
| - | - |
| `-32020` | [`HeaderMismatch`](<https://modelcontextprotocol.io/specification/2026-07-28/schema#headermismatcherror>) |
| `-32021` | [`MissingRequiredClientCapability`](<https://modelcontextprotocol.io/specification/2026-07-28/schema#missingrequiredclientcapabilityerror>) |
| `-32022` | [`UnsupportedProtocolVersion`](<https://modelcontextprotocol.io/specification/2026-07-28/schema#unsupportedprotocolversionerror>) |

Codes defined by earlier protocol versions remain reserved and will not be
reused. Implementations of this protocol version **MUST NOT** emit these codes:

* `-32002` — resource not found (2025-11-25 and earlier; replaced by `-32602`).
  Clients [**SHOULD** still
  accept `-32002`](<https://modelcontextprotocol.io/specification/2026-07-28/server/resources#error-handling>) from
  servers implementing earlier versions.
* `-32042` — URL elicitation required (2025-11-25 only).

Errors that are purely local to an implementation (for example, a request
timeout raised inside an SDK) are not currently assigned codes by this
specification. Implementations surfacing local errors in JSON-RPC-shaped
structures should ensure they cannot be mistaken for errors received from the
peer. Future versions of the specification may define standard codes for
common local error conditions in the reserved sub-range.

New error codes for purposes not defined by this specification **SHOULD** be
allocated outside the JSON-RPC reserved range (`-32768` to `-32000`); the
remainder of the integer space is available for application-defined errors.

### Notifications

[Notifications](<https://modelcontextprotocol.io/specification/2026-07-28/schema#jsonrpcnotification>) are sent from the client to the server or vice versa, as a one-way message.
The receiver **MUST NOT** send a response.

```typescript theme={null}
{
  jsonrpc: "2.0";
  method: string;
  params?: {
    [key: string]: unknown;
  };
}
```

* Notifications **MUST NOT** include an ID.

### Message Patterns

The Model Context Protocol (MCP) supports several [Message Patterns](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns>) that define how clients and servers interact:

1. **[Request and Response](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns#request-and-response>)**: A client sends a request to the server, and the server responds with a result or error.
2. **[Multi Round-Trip Requests (MRTR)](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns#multi-round-trip-requests>)**: A server requires additional client input (sampling, elicitation, or roots) to complete a request.
3. **[Subscribe and Notify](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns#subscribe-and-notify>)**: A client subscribes to a stream of notifications from the server, which are sent as they occur.

## Statelessness

The Model Context Protocol (MCP) is a **stateless protocol**: all the
information needed to process a request is contained in the request itself.
A server processes each request independently; no state should be inferred
from previous requests, even those on the same connection or stream.

Specifically:

* Servers **MUST NOT** rely on prior requests over the same connection to
  establish context (e.g., capabilities, protocol version, client identity).
  Every request supplies this metadata in its [`_meta`](<#_meta>) field.
* Servers **SHOULD** be prepared to handle requests associated with multiple
  tasks, threads, or conversations.
* Servers **SHOULD NOT** require that a client reuse the same connection or process to
  perform related operations.
* Clients **SHOULD NOT** use an individual task, thread, or conversation as the
  lifetime boundary for the stdio process.
* State that needs to span multiple requests (e.g., long-running tasks,
  application-level handles) **MUST** be referenced by an explicit identifier
  the client passes on each request.



**注释：**

  This implies that an open connection, such as a STDIO process, is not a
  conversation or session: clients may interleave unrelated requests on the same
  transport, and a server must not treat connection or process identity as a
  proxy for conversation or session continuity.


Long-lived requests like
[`subscriptions/listen`](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/subscriptions>)
remain request/response; the response is just an open stream of notifications.
Their state is scoped to the request itself, not to the connection underneath.



**说明：**

  For a walkthrough of how the per-request model maps to SDK code, see the
  [Architecture guide](<https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture#example>).


## Auth

MCP provides an [Authorization](<https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization>) framework for use with HTTP.
Implementations using an HTTP-based transport **SHOULD** conform to this specification,
whereas implementations using STDIO transport **SHOULD NOT** follow this specification,
and instead retrieve credentials from the environment.

Additionally, clients and servers **MAY** negotiate their own custom authentication and
authorization strategies.

For further discussions and contributions to the evolution of MCP's auth mechanisms, join
us in
[GitHub Discussions](<https://github.com/modelcontextprotocol/specification/discussions>)
to help shape the future of the protocol!

## Schema

The full specification of the protocol is defined as a
[TypeScript schema](<https://github.com/modelcontextprotocol/specification/blob/main/schema/2026-07-28/schema.ts>).
This is the source of truth for all protocol messages and structures.

There is also a
[JSON Schema](<https://github.com/modelcontextprotocol/specification/blob/main/schema/2026-07-28/schema.json>),
which is automatically generated from the TypeScript source of truth, for use with
various automated tooling.

## JSON Schema Usage

The Model Context Protocol uses JSON Schema for validation throughout the protocol. This section clarifies how JSON Schema should be used within MCP messages.

### Schema Dialect

MCP supports JSON Schema with the following rules:

1. **Default dialect**: When a schema does not include a `$schema` field, it defaults to [JSON Schema 2020-12](<https://json-schema.org/draft/2020-12/schema>)
2. **Explicit dialect**: Schemas MAY include a `$schema` field to specify a different dialect
3. **Supported dialects**: Implementations MUST support at least 2020-12 and SHOULD document which additional dialects they support
4. **Recommendation**: Implementors are RECOMMENDED to use JSON Schema 2020-12.

### Example Usage

#### Default dialect (2020-12):

```json theme={null}
{
  "type": "object",
  "properties": {
    "name": { "type": "string" },
    "age": { "type": "integer", "minimum": 0 }
  },
  "required": ["name"]
}
```

#### Explicit dialect (draft-07):

```json theme={null}
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "name": { "type": "string" },
    "age": { "type": "integer", "minimum": 0 }
  },
  "required": ["name"]
}
```

### Implementation Requirements

* Clients and servers **MUST** support JSON Schema 2020-12 for schemas without an explicit `$schema` field
* Clients and servers **MUST** validate schemas according to their declared or default dialect. They **MUST** handle unsupported dialects gracefully by returning an appropriate error indicating the dialect is not supported.
* Clients and servers **SHOULD** document which schema dialects they support

### Schema Validation

* Schemas **MUST** be valid according to their declared or default dialect

### `$ref` Resolution

JSON Schema 2020-12 permits `$ref` to point at an absolute URI. Implementations **MUST NOT**
automatically dereference `$ref` values that resolve to a network URI.

Implementations **MAY** offer an opt-in mode that fetches non-local `$ref`s but it
**MUST** be disabled by default and **SHOULD** enforce an allowlist of hosts or at
minimum reject loopback, link-local, and private network addresses, apply timeouts and
size limits, and log dereferenced URIs.

Schemas that fail to validate due to an unresolved external `$ref` **SHOULD** be rejected
rather than silently treated as permissive.

### Composition-Keyword Resource Use

Composition keywords (`anyOf`, `oneOf`, `allOf`, `if`/`then`/`else`) and `$defs` enable
expressive schemas but can be expensive to validate. Implementations **SHOULD** apply
reasonable bounds, such as a maximum schema depth, a cap on the total number of subschemas,
or a per-validation time budget, to prevent a malicious schema from acting as a Denial-of-Service
vector against the validator.

## General fields

### `_meta`

The `_meta` property/parameter is used by MCP to allow clients and servers
to attach additional metadata to their interactions.

Certain key names are reserved by MCP for protocol-level metadata, as specified below;
implementations **MUST NOT** make assumptions about values at these keys.

**Key name format:** valid `_meta` key names have two segments: an optional **prefix**, and a **name**.

**Prefix:**

* If specified, MUST be a series of labels separated by dots (`.`), followed by a slash (`/`).
  * Labels MUST start with a letter and end with a letter or digit; interior characters can be letters, digits, or hyphens (`-`).
  * Implementations SHOULD use reverse DNS notation (e.g., `com.example/` rather than `example.com/`).
* Any prefix where the second label is `modelcontextprotocol` or `mcp` is **reserved** for MCP use.
  * For example: `io.modelcontextprotocol/`, `dev.mcp/`, `org.modelcontextprotocol.api/`, and `com.mcp.tools/` are all reserved.
  * However, `com.example.mcp/` is NOT reserved, as the second label is `example`.

**Name:**

* Unless empty, MUST begin and end with an alphanumeric character (`[a-z0-9A-Z]`).
* MAY contain hyphens (`-`), underscores (`_`), dots (`.`), and alphanumerics in between.

**Reserved keys:**

The following `_meta` keys are reserved by this specification:

| Key | Description | Defined in |
| - | - | - |
| `progressToken` | Opts the request into progress notifications | [Progress](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/progress>) |
| `io.modelcontextprotocol/protocolVersion` | Protocol version for a request | Per-request protocol fields (below) |
| `io.modelcontextprotocol/clientInfo` | Client name and version | Per-request protocol fields (below) |
| `io.modelcontextprotocol/clientCapabilities` | Client capabilities relevant to a request | Per-request protocol fields (below) |
| `io.modelcontextprotocol/logLevel` | Minimum log level the server should emit for a request | [Logging](<https://modelcontextprotocol.io/specification/2026-07-28/server/utilities/logging>) |
| `io.modelcontextprotocol/subscriptionId` | Correlates a notification with its originating subscription | [Subscriptions](<https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/subscriptions>) |
| `traceparent`, `tracestate`, `baggage` | OpenTelemetry trace context propagation | OpenTelemetry trace context (below) |

Official [extensions](<https://modelcontextprotocol.io/specification/2026-07-28/basic/versioning#extension-negotiation>)
define additional `_meta` keys under the `io.modelcontextprotocol/` prefix, and
third-party extensions use their own vendor prefix.
In both cases the keys are specified in the extension's documentation.

**Per-request protocol fields:**

Client requests carry the following `io.modelcontextprotocol/*` fields in `_meta`;
fields marked as required **MUST** be included on every request. Servers use these
to identify the protocol version and capabilities in use without relying on any
prior connection state. See
[Versioning and Compatibility][lifecycle] for version negotiation rules.

| Key | Type | Required | Description |
| - | - | - | - |
| `io.modelcontextprotocol/protocolVersion` | `string` | Yes | Protocol version for this request (e.g., `"2026-07-28"`) |
| `io.modelcontextprotocol/clientInfo` | `Implementation` | No | Client name and version |
| `io.modelcontextprotocol/clientCapabilities` | `ClientCapabilities` | Yes | Client capabilities relevant to this request |
| `io.modelcontextprotocol/logLevel` | `LoggingLevel` | No | Minimum log level the server should emit for this request |

A request missing any required field is malformed; the server **MUST** reject it with
JSON-RPC error code `-32602` (Invalid params). On HTTP, the response status **MUST** be
`400 Bad Request`.

Clients **SHOULD** include `io.modelcontextprotocol/clientInfo` on every request
unless specifically configured not to do so.

A server **MUST NOT** rely on capabilities the client has not declared. If
processing a request requires a capability the client did not include in
`io.modelcontextprotocol/clientCapabilities`, the server **MUST** return a
[`MissingRequiredClientCapabilityError`](<https://modelcontextprotocol.io/specification/2026-07-28/schema#missingrequiredclientcapabilityerror>)
(`-32021`) whose `data.requiredCapabilities` lists the missing capabilities. On
HTTP, the response status **MUST** be `400 Bad Request`.

**Per-response protocol fields:**

Servers **SHOULD** include the following `io.modelcontextprotocol/*` field in
every result's `_meta`, unless specifically configured not to do so, to
identify themselves without relying on any prior connection state:

| Key | Type | Required | Description |
| - | - | - | - |
| `io.modelcontextprotocol/serverInfo` | `Implementation` | No | Server name and version |



**注释：**

  `io.modelcontextprotocol/clientInfo` and `io.modelcontextprotocol/serverInfo`
  are self-reported by the sender and are not verified by the protocol. They are
  intended for display, logging, and debugging. Implementations **SHOULD NOT**
  use them to change the behavior of the client or server, and **SHOULD NOT**
  rely on them for security decisions.


On notifications delivered via a [`subscriptions/listen`][subscriptions-listen] stream,
the server **MUST** include `io.modelcontextprotocol/subscriptionId` in `_meta` so the
client can correlate the notification with the originating subscription request.

[lifecycle]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/versioning>

[subscriptions-listen]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/subscriptions>

**OpenTelemetry trace context:**

As an exception to the prefix requirement above, the keys `traceparent`, `tracestate`, and
`baggage` are reserved for [OpenTelemetry](<https://opentelemetry.io/>) trace context propagation.
When present, their values MUST follow [W3C Trace Context](<https://www.w3.org/TR/trace-context/>)
and [W3C Baggage](<https://www.w3.org/TR/baggage/>) formats respectively.

This exception exists to maintain compatibility with existing implementations and
[OpenTelemetry semantic conventions for MCP](<https://opentelemetry.io/docs/specs/semconv/gen-ai/mcp/>).

Non-normative example of trace context in `_meta`:

```json theme={null}
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "tools/call",
  "params": {
    "name": "get_weather",
    "arguments": {
      "location": "New York"
    },
    "_meta": {
      "traceparent": "00-0af7651916cd43dd8448eb211c80319c-00f067aa0ba902b7-01"
    }
  }
}
```

### `icons`

The `icons` property provides a standardized way for servers to expose visual identifiers for their resources, tools, prompts, and implementations. Icons enhance user interfaces by providing visual context and improving the discoverability of available functionality.

Icons are represented as an array of `Icon` objects, where each icon includes:

* `src`: A URI pointing to the icon resource (required). This can be:
  * An HTTP/HTTPS URL pointing to an image file
  * A data URI with base64-encoded image data
* `mimeType`: Optional MIME type if the server's type is missing or generic
* `sizes`: Optional array of size specifications (e.g., `["48x48"]`, `["any"]` for scalable formats like SVG, or `["48x48", "96x96"]` for multiple sizes)
* `theme`: Optional theme preference (`light` or `dark`) for the icon background

**Required MIME type support:**

Clients that support rendering icons **MUST** support at least the following MIME types:

* `image/png` - PNG images (safe, universal compatibility)
* `image/jpeg` (and `image/jpg`) - JPEG images (safe, universal compatibility)

Clients that support rendering icons **SHOULD** also support:

* `image/svg+xml` - SVG images (scalable but requires security precautions as noted below)
* `image/webp` - WebP images (modern, efficient format)

**Security considerations:**

Consumers of icon metadata **MUST** take appropriate security precautions when handling icons to prevent compromise:

* Treat icon metadata and icon bytes as untrusted inputs and defend against network, privacy, and parsing risks.
* Ensure that the icon URI is either a HTTPS or `data:` URI. Clients **MUST** reject icon URIs that use unsafe schemes and redirects, such as `javascript:`, `file:`, `ftp:`, `ws:`, or local app URI schemes.
  * Disallow scheme changes and redirects to hosts on different origins.
* Be resilient against resource exhaustion attacks stemming from oversized images, large dimensions, or excessive frames (e.g., in GIFs).
  * Consumers **MAY** set limits for image and content size.
* Fetch icons without credentials. Do not send cookies, `Authorization` headers, or client credentials.
* Verify that icon URIs are from the same origin as the server. This minimizes the risk of exposing data or tracking information to third-parties.
* Exercise caution when fetching and rendering icons as the payload **MAY** contain executable content (e.g., SVG with [embedded JavaScript](<https://www.w3.org/TR/SVG11/script.html>) or [extended capabilities](<https://www.w3.org/TR/SVG11/extend.html>)).
  * Consumers **MAY** choose to disallow specific file types or otherwise sanitize icon files before rendering.
* Validate MIME types and file contents before rendering. Treat the MIME type information as advisory. Detect content type via magic bytes; reject on mismatch or unknown types.
  * Maintain a strict allowlist of image types.

**Usage:**

Icons can be attached to:

* `Implementation`: Visual identifier for the MCP server/client implementation
* `Tool`: Visual representation of the tool's functionality
* `Prompt`: Icon to display alongside prompt templates
* `Resource`: Visual indicator for different resource types

Multiple icons can be provided to support different display contexts and resolutions. Clients should select the most appropriate icon based on their UI requirements.


</details>
