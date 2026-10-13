# MCP 标准输入输出传输规范

定义子进程通信中的消息收发、元数据、取消、关闭、异常终止和兼容方式。

- 来源：通用规范／MCP
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-11](<https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/stdio>)；规范版本：[2026-07-28](<https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/stdio>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/stdio>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/MCP/MCP 标准输入输出传输规范.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://modelcontextprotocol.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 标准输入输出

<div id="enable-section-numbers" />

在 **stdio** 传输中，客户端将 MCP 服务器作为子进程启动。
两端通过子进程的标准流进行通信：

* 服务器从`stdin`读取JSON-RPC消息并将JSON-RPC消息写入
  `stdout`.
* 每条消息都是一个 JSON-RPC 请求、通知或响应。
* 消息由换行符分隔，并且 **MUST NOT** 包含嵌入的换行符。
* 服务器**MAY**将UTF-8字符串写入`stderr`以用于任何日志记录目的
包括信息、调试和错误消息。
* 客户端**MAY**捕获、转发或忽略服务器的`stderr`输出并
**SHOULD NOT** 假定 `stderr` 输出指示错误情况。
* 服务器 **MUST NOT** 向其 `stdout` 写入不是有效 MCP 的任何内容
消息。
* 客户端 **MUST NOT** 向服务器的 `stdin` 写入任何不是
有效的 MCP 消息。

标准流是规范通道，但除了进程生命周期之外，此绑定
中的任何内容都不依赖于它们。有线格式（通过可靠的双向 
 字节流每行一条 
 换行符分隔的 JSON-RPC 消息）在 Unix 域套接字、TCP 连接或 
 任何类似通道上保持不变。
[自定义基于此类流 **SHOULD** 构建的传输 ](<https://modelcontextprotocol.io/specification/2026-07-28/basic/transports#custom-transports>)
 在 
 此页面上重用此框架和消息规则；只有特定于子流程的方面（启动、`stderr`、通过关闭流关闭
、流程重新启动）需要特定于通道的等效项。

## 发送消息

客户端通过将 JSON-RPC * 请求 * 和 * 通知 *
 写入服务器的 `stdin` 来发送消息，每行一条消息。客户端 **MUST NOT** 写入 
JSON-RPC * 响应 *。

## 接收消息

客户端从`stdout`读取服务器消息，每行一条消息。所有
消息共享这个单一通道；没有每个请求的流。

服务器写入三种消息：

1. * 对客户端请求的响应 *，由 JSON-RPC `id` 关联。
2. *与正在进行的请求相关的通知*，例如
`notifications/progress` 和 `notifications/message`。
3. *为活动发送的通知*
[`subscriptions/listen`][订阅-监听]请求。客户端**MUST**
使用`_meta`中的`io.modelcontextprotocol/subscriptionId`字段
将这些关联起来；请参阅
 [`SubscriptionsListenRequest`][订阅监听请求]。

服务器**MUSTNOT**写JSON-RPC*请求*到`stdout`。
承载服务器到客户端的交互在
[`InputRequiredResult`][mrtr-input-required]回复；请参阅
[多往返请求][mrtr]。

[mrtr]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/mrtr>

[mrtr-input-required]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/mrtr#inputrequiredresult>

[subscriptions-listen]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/subscriptions>

[subscriptions-listen-request]: <https://modelcontextprotocol.io/specification/2026-07-28/schema#subscriptionslistenrequest>

## 请求元数据

stdio 传输的所有请求元数据都内联在 
JSON-RPC 消息正文中。协议版本、每个请求的功能和
可选客户端身份位于
[`_meta.io.modelcontextprotocol/*`][元字段]中；
方法名称和参数位于JSON-RPC放置的位置。没有
标头层。

[meta-fields]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/index#meta>

## 取消

要取消正在进行的请求，客户端 **MUST** 发送引用请求的 ID 的 
`notifications/cancelled` 通知。由于 
stdio 是单个共享双向通道，因此没有要关闭的每个请求流 
。一旦
实际并且**MUSTNOT**发送任何进一步的消息，服务器**SHOULD**就会停止处理已取消的请求。完整规则请参见
[取消][取消]。

[cancellation]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/cancellation>

## 关机

客户端**SHOULD**通过以下方式启动关闭：

1. 关闭子进程（服务器）的输入流。
2. 等待服务器退出。
3. 如果服务器在合理时间内没有退出，则强制终止
使用适合操作系统的机制的进程。

在 POSIX 系统上，强制终止通常从 
[`SIGTERM`][sigterm]
 升级到 `SIGKILL`。在 Windows 上，POSIX 信号不可用，客户端可以
使用 [`TerminateProcess`][terminateprocess]
 或 [作业对象][作业对象]。

服务器 **SHOULD** 当标准输入关闭或读取时立即退出
返回文件结尾。这是主要的正常关闭信号，也是唯一的
便携式的，因此遵守它可以减少强制终止的需要。

服务器 **MAY** 通过关闭其到 
 客户端的输出流并退出来启动关闭。

## 意外终止

如果服务器进程意外退出，客户端**SHOULD**会重新启动它。
由于协议是无状态的，任何正在进行的请求都会丢失，
客户端可以针对新进程重试它们。重新启动后，活动的
[`subscriptions/listen`][订阅监听]流也必须
重新建立。

[sigterm]: <https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/signal.h.html>

[terminateprocess]: <https://learn.microsoft.com/windows/win32/api/processthreadsapi/nf-processthreadsapi-terminateprocess>

[job-objects]: <https://learn.microsoft.com/windows/win32/procthread/job-objects>

## 向后兼容性

支持现代（按请求元数据）MCP 版本和 
 旧版本的客户端，在发送任何其他版本之前需要使用 
[`server/discover`][ 服务器发现 ] 进行 `initialize` 握手 **SHOULD** 探测请求，
在`_meta`中设置其首选现代版本。该探测器有三种
可能的结果：

* 服务器返回`DiscoverResult`：服务器是现代的。选择一个
来自 `supportedVersions` 的相互支持版本并继续。
* 服务器返回可识别的现代 JSON-RPC 错误，例如
[`UnsupportedProtocolVersionError`][不支持的版本]：服务器是
现代但不支持请求的版本。使用其公布的 `supported` 列表中的版本 
 之一。 **不**回落到`initialize`。
* 服务器返回任何其他错误，或者在一段时间内没有响应
合理的超时：服务器是遗留的。回到 `initialize`
 握手。

后备 **MUST NOT** 被锁定为一个特定的错误代码：旧服务器 
 响应未知的前 `initialize` 请求，并出现实现定义的 
 错误（通常为 `-32601` 或 `-32602`）或者根本没有。

仅支持现代版本的客户端不需要探测，但
探测仍然是**RECOMMENDED**：某些旧服务器不会验证
请求在`initialize`之后到达，并且会处理时代模糊的方法
（例如`tools/call`）在遗留语义下。相反，探测会产生
确定性失败。

请参阅[版本控制：向后兼容性][生命周期兼容性]，了解时代模型
和实现者的兼容性矩阵。

[server-discover]: <https://modelcontextprotocol.io/specification/2026-07-28/schema#discoverrequest>

[unsupported-version]: <https://modelcontextprotocol.io/specification/2026-07-28/schema#unsupportedprotocolversionerror>

[lifecycle-compat]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/versioning#backward-compatibility-with-initialization-based-versions>


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://modelcontextprotocol.io/llms.txt
> Use this file to discover all available pages before exploring further.

# stdio

<div id="enable-section-numbers" />

In the **stdio** transport, the client launches the MCP server as a subprocess.
The two ends communicate over the subprocess's standard streams:

* The server reads JSON-RPC messages from `stdin` and writes JSON-RPC messages to
  `stdout`.
* Each message is a single JSON-RPC request, notification, or response.
* Messages are delimited by newlines, and **MUST NOT** contain embedded newlines.
* The server **MAY** write UTF-8 strings to `stderr` for any logging purposes
  including informational, debug, and error messages.
* The client **MAY** capture, forward, or ignore the server's `stderr` output and
  **SHOULD NOT** assume `stderr` output indicates error conditions.
* The server **MUST NOT** write anything to its `stdout` that is not a valid MCP
  message.
* The client **MUST NOT** write anything to the server's `stdin` that is not a
  valid MCP message.

Standard streams are the canonical channel, but nothing in this binding
depends on them except the process lifecycle. The wire format (one
newline-delimited JSON-RPC message per line over a reliable bidirectional
byte stream) works unchanged over Unix domain sockets, TCP connections, or
any similar channel.
[Custom transports](<https://modelcontextprotocol.io/specification/2026-07-28/basic/transports#custom-transports>)
built on such streams **SHOULD** reuse this framing and the message rules on
this page; only the subprocess-specific aspects (launch, `stderr`, shutdown
by closing the stream, process restart) need channel-specific equivalents.

## Sending Messages

The client sends messages by writing JSON-RPC *requests* and *notifications*
to the server's `stdin`, one message per line. The client **MUST NOT** write
JSON-RPC *responses*.

## Receiving Messages

The client reads server messages from `stdout`, one message per line. All
messages share this single channel; there are no per-request streams.

The server writes three kinds of messages:

1. *Responses* to client requests, correlated by JSON-RPC `id`.
2. *Notifications* that relate to an in-flight request, such as
   `notifications/progress` and `notifications/message`.
3. *Notifications* delivered for an active
   [`subscriptions/listen`][subscriptions-listen] request. Clients **MUST**
   correlate these using the `io.modelcontextprotocol/subscriptionId` field
   in `_meta`; see
   [`SubscriptionsListenRequest`][subscriptions-listen-request].

The server **MUST NOT** write JSON-RPC *requests* to `stdout`.
Server-to-client interactions are carried in
[`InputRequiredResult`][mrtr-input-required] replies; see
[Multi Round-Trip Requests][mrtr].

[mrtr]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/mrtr>

[mrtr-input-required]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/mrtr#inputrequiredresult>

[subscriptions-listen]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/subscriptions>

[subscriptions-listen-request]: <https://modelcontextprotocol.io/specification/2026-07-28/schema#subscriptionslistenrequest>

## Request Metadata

All request metadata for the stdio transport is carried inline in the
JSON-RPC message body. The protocol version, per-request capabilities, and
optional client identity live in
[`_meta.io.modelcontextprotocol/*`][meta-fields];
the method name and arguments live where JSON-RPC puts them. There is no
header layer.

[meta-fields]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/index#meta>

## Cancellation

To cancel an in-flight request, the client **MUST** send a
`notifications/cancelled` notification referencing the request's ID. Because
stdio is a single shared bidirectional channel, there is no per-request stream
to close. Servers **SHOULD** stop work on a cancelled request as soon as
practical and **MUST NOT** send any further messages for it. See
[Cancellation][cancellation] for the full rules.

[cancellation]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/cancellation>

## Shutdown

The client **SHOULD** initiate shutdown by:

1. Closing the input stream to the child process (the server).
2. Waiting for the server to exit.
3. If the server does not exit within a reasonable time, forcibly terminating
   the process using the mechanism appropriate for the operating system.

On POSIX systems, forced termination typically escalates from
[`SIGTERM`][sigterm]
to `SIGKILL`. On Windows, where POSIX signals are not available, clients can
use [`TerminateProcess`][terminateprocess]
or [Job Objects][job-objects].

Servers **SHOULD** exit promptly when their standard input is closed or reads
return end-of-file. This is the primary graceful-shutdown signal and the only
portable one, so honoring it reduces the need for forced termination.

The server **MAY** initiate shutdown by closing its output stream to the
client and exiting.

## Unexpected Termination

If the server process exits unexpectedly, the client **SHOULD** restart it.
Because the protocol is stateless, any in-flight requests are simply lost and
the client can retry them against the fresh process. Active
[`subscriptions/listen`][subscriptions-listen] streams must also be
re-established after restart.

[sigterm]: <https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/signal.h.html>

[terminateprocess]: <https://learn.microsoft.com/windows/win32/api/processthreadsapi/nf-processthreadsapi-terminateprocess>

[job-objects]: <https://learn.microsoft.com/windows/win32/procthread/job-objects>

## Backward Compatibility

A client that supports both modern (per-request-metadata) MCP versions and a
legacy version that requires an `initialize` handshake **SHOULD** probe with
[`server/discover`][server-discover] before sending any other request,
setting its preferred modern version in `_meta`. The probe has three
possible outcomes:

* The server returns a `DiscoverResult`: the server is modern. Select a
  mutually supported version from `supportedVersions` and continue.
* The server returns a recognized modern JSON-RPC error such as
  [`UnsupportedProtocolVersionError`][unsupported-version]: the server is
  modern but does not support the requested version. Use one of the versions
  in its advertised `supported` list. Do **not** fall back to `initialize`.
* The server returns any other error, or does not respond within a
  reasonable timeout: the server is legacy. Fall back to the `initialize`
  handshake.

The fallback **MUST NOT** be keyed to one specific error code: legacy servers
respond to unknown pre-`initialize` requests with implementation-defined
errors (commonly `-32601` or `-32602`) or not at all.

A client that only supports modern versions does not need to probe, but
probing is still **RECOMMENDED**: some legacy servers do not validate that a
request arrives after `initialize` and would process an era-ambiguous method
(such as `tools/call`) under legacy semantics. Probing yields a
deterministic failure instead.

See [Versioning: Backward Compatibility][lifecycle-compat] for the era model
and a compatibility matrix for implementors.

[server-discover]: <https://modelcontextprotocol.io/specification/2026-07-28/schema#discoverrequest>

[unsupported-version]: <https://modelcontextprotocol.io/specification/2026-07-28/schema#unsupportedprotocolversionerror>

[lifecycle-compat]: <https://modelcontextprotocol.io/specification/2026-07-28/basic/versioning#backward-compatibility-with-initialization-based-versions>


</details>
