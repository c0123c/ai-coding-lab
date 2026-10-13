# OpenCode HTTP 服务、认证与 API

服务接口参考。说明无界面服务、端口、CORS、认证、客户端连接和 OpenAPI，列出会话、消息、文件、工具、MCP、Agent、事件等接口。

- 来源：OpenCode
- 资料类型：服务接口参考
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-02-03](<https://github.com/anomalyco/opencode/commit/95d0d476e306e0767e569466e39152390e7024d1>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：实验接口和服务暴露范围按所用版本与部署环境判断。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/server.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode HTTP 服务、认证与 API.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Server
description: Interact with opencode server over HTTP.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

`opencode serve` 命令运行无头 HTTP 服务器，该服务器公开 OpenAPI 端点供 opencode 客户端使用。

---

### 用途

```bash
opencode serve [--port <number>] [--hostname <string>] [--cors <origin>]
```

#### 选项

|旗帜|描述|默认|
| --------------- | ----------------------------------- | ---------------- |
| `--port`        |监听端口| `4096`           |
| `--hostname`    |要监听的主机名| `127.0.0.1`      |
| `--mdns`        |启用 mDNS 发现| `false`          |
| `--mdns-domain` |mDNS服务的自定义域名| `opencode.local` |
| `--cors`        |允许的其他浏览器来源| `[]`             |

`--cors`可以多次传递：

```bash
opencode serve --cors http://localhost:5173 --cors https://app.example.com
```

---

### 认证

设置`OPENCODE_SERVER_PASSWORD`以使用HTTP基本身份验证保护服务器。用户名默认为`opencode`，或者设置`OPENCODE_SERVER_USERNAME`来覆盖它。这适用于 `opencode serve` 和 `opencode web`。

```bash
OPENCODE_SERVER_PASSWORD=your-password opencode serve
```

---

### 它是如何运作的

当您运行 `opencode` 时，它会启动 TUI 和服务器。其中 TUI 是与服务器通信的 
 客户端。服务器公开 OpenAPI 3.1 规范 
 端点。该端点还用于生成 [SDK](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode JavaScript 与 TypeScript SDK.md>)。

**提示：**

使用 opencode 服务器以编程方式与 opencode 交互。

此架构让 opencode 支持多个客户端，并允许您以编程方式与 opencode 交互。

您可以运行`opencode serve`来启动独立服务器。如果您正在运行
opencode TUI，`opencode serve`将启动一个新服务器。

---

#### 连接到现有服务器

当您启动 TUI 时，它会随机分配端口和主机名。您可以改为传入 `--hostname` 和 `--port` [ 标志 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode CLI 命令、参数与环境变量.md>)。然后使用它连接到其服务器。

[`/tui`](<#tui>)端点可用于通过服务器驱动TUI。例如，您可以预填充或运行提示。此设置由 OpenCode [IDE](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 编辑器集成与安装排查.md>) 插件使用。

---

## 规格

服务器发布了 OpenAPI 3.1 规范，可以在以下位置查看：

```
http://<hostname>:<port>/doc
```

例如，`http://localhost:4096/doc`。使用规范生成客户端或检查请求和响应类型。或者在 Swagger 浏览器中查看它。

---

## API

opencode服务器公开以下API。

---

### 全球

|方法|路径|描述|回应|
| ------ | ---------------- | ------------------------------ | ------------------------------------ |
| `GET`  | `/global/health` |获取服务器健康状况和版本| `{ healthy: true, version: string }` |
| `GET`  | `/global/event`  |获取全局事件（SSE 流）|事件流|

---

### 项目

|方法|路径|描述|回应|
| ------ | ------------------ | ----------------------- | --------------------------------------------- |
| `GET`  | `/project`         |列出所有项目| [`Project[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |
| `GET`  | `/project/current` |获取当前项目| [`Project`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)   |

---

### 路径 & VCS

|方法|路径|描述|回应|
| ------ | ------- | ------------------------------------ | ------------------------------------------- |
| `GET`  | `/path` |获取当前路径| [`Path`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)    |
| `GET`  | `/vcs`  |获取当前项目的 VCS 信息| [`VcsInfo`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

### 实例

|方法|路径|描述|回应|
| ------ | ------------------- | ---------------------------- | --------- |
| `POST` | `/instance/dispose` |处置当前实例| `boolean` |

---

### 配置

|方法|路径|描述|回应|
| ------- | ------------------- | --------------------------------- | ---------------------------------------------------------------------------------------- |
| `GET`   | `/config`           |获取配置信息| [`Config`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                               |
| `PATCH` | `/config`           |更新配置| [`Config`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                               |
| `GET`   | `/config/providers` |列出提供者和默认模型|`{ providers: `[提供商[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, default: { [key: string]: string } }`|

---

### 提供者

|方法|路径|描述|回应|
| ------ | -------------------------------- | ------------------------------------ | ----------------------------------------------------------------------------------- |
| `GET`  | `/provider`                      |列出所有提供商|`{ all: `[提供商[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, default: {...}, connected: string[] }`|
| `GET`  | `/provider/auth`                 |获取提供商身份验证方法| `{ [providerID: string]: `[ProviderAuthMethod[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)` }`           |
| `POST` | `/provider/{id}/oauth/authorize` |使用 OAuth 授权提供商| [`ProviderAuthAuthorization`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                       |
| `POST` | `/provider/{id}/oauth/callback`  |处理提供商的 OAuth 回调| `boolean`                                                                           |

---

### 会议

|方法|路径|描述|注释|
| -------- | ---------------------------------------- | ------------------------------------- | ---------------------------------------------------------------------------------- |
| `GET`    | `/session`                               |列出所有会话|返回[`Session[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `POST`   | `/session`                               |创建一个新会话|主体：`{ parentID?, title? }`，返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `GET`    | `/session/status`                        |获取所有会话的会话状态|返回`{ [sessionID: string]: `[SessionStatus](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)` }`|
| `GET`    | `/session/:id`                           |获取会话详细信息|返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `DELETE` | `/session/:id`                           |删除会话及其所有数据|返回`boolean`|
| `PATCH`  | `/session/:id`                           |更新会话属性|主体：`{ title? }`，返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `GET`    | `/session/:id/children`                  |获取会话的子会话|返回[`Session[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `GET`    | `/session/:id/todo`                      |获取会话的待办事项列表|返回[`Todo[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `POST`   | `/session/:id/init`                      |分析应用并创建 `AGENTS.md`|主体：`{ messageID, providerID, modelID }`，返回`boolean`|
| `POST`   | `/session/:id/fork`                      |在消息中分叉现有会话|主体：`{ messageID? }`，返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `POST`   | `/session/:id/abort`                     |中止正在运行的会话|返回`boolean`|
| `POST`   | `/session/:id/share`                     |共享会话|返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `DELETE` | `/session/:id/share`                     |取消共享会话|返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `GET`    | `/session/:id/diff`                      |获取本次会话的差异|查询：`messageID?`，返回[`FileDiff[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `POST`   | `/session/:id/summarize`                 |总结会议|主体：`{ providerID, modelID }`，返回`boolean`|
| `POST`   | `/session/:id/revert`                    |回复消息|主体：`{ messageID, partID? }`，返回`boolean`|
| `POST`   | `/session/:id/unrevert`                  |恢复所有已恢复的消息|返回`boolean`|
| `POST`   | `/session/:id/permissions/:permissionID` |响应权限请求|主体：`{ response, remember? }`，返回`boolean`|

---

### 留言

|方法|路径|描述|注释|
| ------ | --------------------------------- | --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`  | `/session/:id/message`            |列出会话中的消息|查询：`limit?`，返回`{ info: `[消息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[部分[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}[]`|
| `POST` | `/session/:id/message`            |发送消息并等待回复|主体：`{ messageID?, model?, agent?, noReply?, system?, tools?, parts }`，返回`{ info: `[消息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[部分[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}`|
| `GET`  | `/session/:id/message/:messageID` |获取消息详情|返回`{ info: `[消息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[部分[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}`|
| `POST` | `/session/:id/prompt_async`       |异步发送消息（无需等待）|body：与`/session/:id/message`相同，返回`204 No Content`|
| `POST` | `/session/:id/command`            |执行斜杠命令|主体：`{ messageID?, agent?, model?, command, arguments }`，返回`{ info: `[消息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[部分[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}`|
| `POST` | `/session/:id/shell`              |运行外壳命令|主体：`{ agent, model?, command }`，返回`{ info: `[消息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[部分[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}`|

---

### 命令

|方法|路径|描述|回应|
| ------ | ---------- | ----------------- | --------------------------------------------- |
| `GET`  | `/command` |列出所有命令| [`Command[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

### 文件

|方法|路径|描述|回应|
| ------ | ------------------------ | ---------------------------------- | ------------------------------------------------------------------------------------------- |
| `GET`  | `/find?pattern=<pat>`    |在文件中搜索文本|具有 `path`、`lines`、`line_number`、`absolute_offset`、`submatches` 的匹配对象数组|
| `GET`  | `/find/file?query=<q>`   |按名称查找文件和目录|`string[]`（路径）|
| `GET`  | `/find/symbol?query=<q>` |查找工作区符号| [`Symbol[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                |
| `GET`  | `/file?path=<path>`      |列出文件和目录| [`FileNode[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                              |
| `GET`  | `/file/content?path=<p>` |读取文件| [`FileContent`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                             |
| `GET`  | `/file/status`           |获取跟踪文件的状态| [`File[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                  |

#### `/find/file`查询参数

- `query`（必需）- 搜索字符串（模糊匹配）
- `type`（可选）- 将结果限制为 `"file"` 或 `"directory"`
- `directory`（可选）- 覆盖搜索的项目根目录
- `limit`（可选）— 最大结果 (1–200)
- `dirs`（可选）- 旧标志（`"false"` 仅返回文件）

---

### 工具（实验）

|方法|路径|描述|回应|
| ------ | ------------------------------------------- | ---------------------------------------- | -------------------------------------------- |
| `GET`  | `/experimental/tool/ids`                    |列出所有工具 ID| [`ToolIDs`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)  |
| `GET`  | `/experimental/tool?provider=<p>&model=<m>` |列出具有模型 JSON 架构的工具| [`ToolList`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

### LSP、格式化程序和 MCP

|方法|路径|描述|回应|
| ------ | ------------ | -------------------------- | -------------------------------------------------------- |
| `GET`  | `/lsp`       |获取LSP服务器状态| [`LSPStatus[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)          |
| `GET`  | `/formatter` |获取格式化程序状态| [`FormatterStatus[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)    |
| `GET`  | `/mcp`       |获取MCP服务器状态|`{ [name: string]: `[MCP状态](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)` }`|
| `POST` | `/mcp`       |动态添加MCP服务器|body：`{ name, config }`，返回MCP状态对象|

---

### Agents

|方法|路径|描述|回应|
| ------ | -------- | ------------------------- | ------------------------------------------- |
| `GET`  | `/agent` |列出所有可用的 agents| [`Agent[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

### 记录

|方法|路径|描述|回应|
| ------ | ------ | ------------------------------------------------------------ | --------- |
| `POST` | `/log` |写入日志条目。机身：`{ service, level, message, extra? }`| `boolean` |

---

### TUI

|方法|路径|描述|回应|
| ------ | ----------------------- | ------------------------------------------- | ---------------------- |
| `POST` | `/tui/append-prompt`    |将文本附加到提示| `boolean`              |
| `POST` | `/tui/open-help`        |打开帮助对话框| `boolean`              |
| `POST` | `/tui/open-sessions`    |打开会话选择器| `boolean`              |
| `POST` | `/tui/open-themes`      |打开主题选择器| `boolean`              |
| `POST` | `/tui/open-models`      |打开模型选择器| `boolean`              |
| `POST` | `/tui/submit-prompt`    |提交当前提示| `boolean`              |
| `POST` | `/tui/clear-prompt`     |清除提示| `boolean`              |
| `POST` | `/tui/execute-command`  |执行命令（`{ command }`）| `boolean`              |
| `POST` | `/tui/show-toast`       |显示吐司 (`{ title?, message, variant }`)| `boolean`              |
| `GET`  | `/tui/control/next`     |等待下一个控制请求|控制请求对象|
| `POST` | `/tui/control/response` |响应控制请求（`{ body }`）| `boolean`              |

---

### 授权

|方法|路径|描述|回应|
| ------ | ----------- | --------------------------------------------------------------- | --------- |
| `PUT`  | `/auth/:id` |设置身份验证凭据。正文必须与提供者架构匹配| `boolean` |

---

### 活动

|方法|路径|描述|回应|
| ------ | -------- | ----------------------------------------------------------------------------- | ------------------------- |
| `GET`  | `/event` |服务器发送的事件流。第一个事件是 `server.connected`，然后是总线事件|服务器发送的事件流|

---

### 文档

|方法|路径|描述|回应|
| ------ | ------ | ------------------------- | --------------------------- |
| `GET`  | `/doc` |OpenAPI 3.1 规格|HTML 页面，包含 OpenAPI 规格|


## 原文对照

<details>
<summary>展开完整原文</summary>

The `opencode serve` command runs a headless HTTP server that exposes an OpenAPI endpoint that an opencode client can use.

---

### Usage

```bash
opencode serve [--port <number>] [--hostname <string>] [--cors <origin>]
```

#### Options

| Flag            | Description                         | Default          |
| --------------- | ----------------------------------- | ---------------- |
| `--port`        | Port to listen on                   | `4096`           |
| `--hostname`    | Hostname to listen on               | `127.0.0.1`      |
| `--mdns`        | Enable mDNS discovery               | `false`          |
| `--mdns-domain` | Custom domain name for mDNS service | `opencode.local` |
| `--cors`        | Additional browser origins to allow | `[]`             |

`--cors` can be passed multiple times:

```bash
opencode serve --cors http://localhost:5173 --cors https://app.example.com
```

---

### Authentication

Set `OPENCODE_SERVER_PASSWORD` to protect the server with HTTP basic auth. The username defaults to `opencode`, or set `OPENCODE_SERVER_USERNAME` to override it. This applies to both `opencode serve` and `opencode web`.

```bash
OPENCODE_SERVER_PASSWORD=your-password opencode serve
```

---

### How it works

When you run `opencode` it starts a TUI and a server. Where the TUI is the
client that talks to the server. The server exposes an OpenAPI 3.1 spec
endpoint. This endpoint is also used to generate an [SDK](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode JavaScript 与 TypeScript SDK.md>).

**提示：**

Use the opencode server to interact with opencode programmatically.

This architecture lets opencode support multiple clients and allows you to interact with opencode programmatically.

You can run `opencode serve` to start a standalone server. If you have the
opencode TUI running, `opencode serve` will start a new server.

---

#### Connect to an existing server

When you start the TUI it randomly assigns a port and hostname. You can instead pass in the `--hostname` and `--port` [flags](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode CLI 命令、参数与环境变量.md>). Then use this to connect to its server.

The [`/tui`](<#tui>) endpoint can be used to drive the TUI through the server. For example, you can prefill or run a prompt. This setup is used by the OpenCode [IDE](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 编辑器集成与安装排查.md>) plugins.

---

## Spec

The server publishes an OpenAPI 3.1 spec that can be viewed at:

```
http://<hostname>:<port>/doc
```

For example, `http://localhost:4096/doc`. Use the spec to generate clients or inspect request and response types. Or view it in a Swagger explorer.

---

## APIs

The opencode server exposes the following APIs.

---

### Global

| Method | Path             | Description                    | Response                             |
| ------ | ---------------- | ------------------------------ | ------------------------------------ |
| `GET`  | `/global/health` | Get server health and version  | `{ healthy: true, version: string }` |
| `GET`  | `/global/event`  | Get global events (SSE stream) | Event stream                         |

---

### Project

| Method | Path               | Description             | Response                                      |
| ------ | ------------------ | ----------------------- | --------------------------------------------- |
| `GET`  | `/project`         | List all projects       | [`Project[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |
| `GET`  | `/project/current` | Get the current project | [`Project`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)   |

---

### Path & VCS

| Method | Path    | Description                          | Response                                    |
| ------ | ------- | ------------------------------------ | ------------------------------------------- |
| `GET`  | `/path` | Get the current path                 | [`Path`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)    |
| `GET`  | `/vcs`  | Get VCS info for the current project | [`VcsInfo`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

### Instance

| Method | Path                | Description                  | Response  |
| ------ | ------------------- | ---------------------------- | --------- |
| `POST` | `/instance/dispose` | Dispose the current instance | `boolean` |

---

### Config

| Method  | Path                | Description                       | Response                                                                                 |
| ------- | ------------------- | --------------------------------- | ---------------------------------------------------------------------------------------- |
| `GET`   | `/config`           | Get config info                   | [`Config`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                               |
| `PATCH` | `/config`           | Update config                     | [`Config`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                               |
| `GET`   | `/config/providers` | List providers and default models | `{ providers: `[Provider[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, default: { [key: string]: string } }` |

---

### Provider

| Method | Path                             | Description                          | Response                                                                            |
| ------ | -------------------------------- | ------------------------------------ | ----------------------------------------------------------------------------------- |
| `GET`  | `/provider`                      | List all providers                   | `{ all: `[Provider[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, default: {...}, connected: string[] }` |
| `GET`  | `/provider/auth`                 | Get provider authentication methods  | `{ [providerID: string]: `[ProviderAuthMethod[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)` }`           |
| `POST` | `/provider/{id}/oauth/authorize` | Authorize a provider using OAuth     | [`ProviderAuthAuthorization`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                       |
| `POST` | `/provider/{id}/oauth/callback`  | Handle OAuth callback for a provider | `boolean`                                                                           |

---

### Sessions

| Method   | Path                                     | Description                           | Notes                                                                              |
| -------- | ---------------------------------------- | ------------------------------------- | ---------------------------------------------------------------------------------- |
| `GET`    | `/session`                               | List all sessions                     | Returns [`Session[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                              |
| `POST`   | `/session`                               | Create a new session                  | body: `{ parentID?, title? }`, returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |
| `GET`    | `/session/status`                        | Get session status for all sessions   | Returns `{ [sessionID: string]: `[SessionStatus](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)` }`          |
| `GET`    | `/session/:id`                           | Get session details                   | Returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                |
| `DELETE` | `/session/:id`                           | Delete a session and all its data     | Returns `boolean`                                                                  |
| `PATCH`  | `/session/:id`                           | Update session properties             | body: `{ title? }`, returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)            |
| `GET`    | `/session/:id/children`                  | Get a session's child sessions        | Returns [`Session[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                              |
| `GET`    | `/session/:id/todo`                      | Get the todo list for a session       | Returns [`Todo[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                 |
| `POST`   | `/session/:id/init`                      | Analyze app and create `AGENTS.md`    | body: `{ messageID, providerID, modelID }`, returns `boolean`                      |
| `POST`   | `/session/:id/fork`                      | Fork an existing session at a message | body: `{ messageID? }`, returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)        |
| `POST`   | `/session/:id/abort`                     | Abort a running session               | Returns `boolean`                                                                  |
| `POST`   | `/session/:id/share`                     | Share a session                       | Returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                |
| `DELETE` | `/session/:id/share`                     | Unshare a session                     | Returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                |
| `GET`    | `/session/:id/diff`                      | Get the diff for this session         | query: `messageID?`, returns [`FileDiff[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)        |
| `POST`   | `/session/:id/summarize`                 | Summarize the session                 | body: `{ providerID, modelID }`, returns `boolean`                                 |
| `POST`   | `/session/:id/revert`                    | Revert a message                      | body: `{ messageID, partID? }`, returns `boolean`                                  |
| `POST`   | `/session/:id/unrevert`                  | Restore all reverted messages         | Returns `boolean`                                                                  |
| `POST`   | `/session/:id/permissions/:permissionID` | Respond to a permission request       | body: `{ response, remember? }`, returns `boolean`                                 |

---

### Messages

| Method | Path                              | Description                             | Notes                                                                                                                                                                 |
| ------ | --------------------------------- | --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`  | `/session/:id/message`            | List messages in a session              | query: `limit?`, returns `{ info: `[Message](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[Part[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}[]`                                                        |
| `POST` | `/session/:id/message`            | Send a message and wait for response    | body: `{ messageID?, model?, agent?, noReply?, system?, tools?, parts }`, returns `{ info: `[Message](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[Part[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}` |
| `GET`  | `/session/:id/message/:messageID` | Get message details                     | Returns `{ info: `[Message](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[Part[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}`                                                                           |
| `POST` | `/session/:id/prompt_async`       | Send a message asynchronously (no wait) | body: same as `/session/:id/message`, returns `204 No Content`                                                                                                        |
| `POST` | `/session/:id/command`            | Execute a slash command                 | body: `{ messageID?, agent?, model?, command, arguments }`, returns `{ info: `[Message](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[Part[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}`               |
| `POST` | `/session/:id/shell`              | Run a shell command                     | body: `{ agent, model?, command }`, returns `{ info: `[Message](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[Part[]](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}`                                       |

---

### Commands

| Method | Path       | Description       | Response                                      |
| ------ | ---------- | ----------------- | --------------------------------------------- |
| `GET`  | `/command` | List all commands | [`Command[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

### Files

| Method | Path                     | Description                        | Response                                                                                    |
| ------ | ------------------------ | ---------------------------------- | ------------------------------------------------------------------------------------------- |
| `GET`  | `/find?pattern=<pat>`    | Search for text in files           | Array of match objects with `path`, `lines`, `line_number`, `absolute_offset`, `submatches` |
| `GET`  | `/find/file?query=<q>`   | Find files and directories by name | `string[]` (paths)                                                                          |
| `GET`  | `/find/symbol?query=<q>` | Find workspace symbols             | [`Symbol[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                |
| `GET`  | `/file?path=<path>`      | List files and directories         | [`FileNode[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                              |
| `GET`  | `/file/content?path=<p>` | Read a file                        | [`FileContent`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                             |
| `GET`  | `/file/status`           | Get status for tracked files       | [`File[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                  |

#### `/find/file` query parameters

- `query` (required) — search string (fuzzy match)
- `type` (optional) — limit results to `"file"` or `"directory"`
- `directory` (optional) — override the project root for the search
- `limit` (optional) — max results (1–200)
- `dirs` (optional) — legacy flag (`"false"` returns only files)

---

### Tools (Experimental)

| Method | Path                                        | Description                              | Response                                     |
| ------ | ------------------------------------------- | ---------------------------------------- | -------------------------------------------- |
| `GET`  | `/experimental/tool/ids`                    | List all tool IDs                        | [`ToolIDs`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)  |
| `GET`  | `/experimental/tool?provider=<p>&model=<m>` | List tools with JSON schemas for a model | [`ToolList`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

### LSP, Formatters & MCP

| Method | Path         | Description                | Response                                                 |
| ------ | ------------ | -------------------------- | -------------------------------------------------------- |
| `GET`  | `/lsp`       | Get LSP server status      | [`LSPStatus[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)          |
| `GET`  | `/formatter` | Get formatter status       | [`FormatterStatus[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)    |
| `GET`  | `/mcp`       | Get MCP server status      | `{ [name: string]: `[MCPStatus](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)` }` |
| `POST` | `/mcp`       | Add MCP server dynamically | body: `{ name, config }`, returns MCP status object      |

---

### Agents

| Method | Path     | Description               | Response                                    |
| ------ | -------- | ------------------------- | ------------------------------------------- |
| `GET`  | `/agent` | List all available agents | [`Agent[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

### Logging

| Method | Path   | Description                                                  | Response  |
| ------ | ------ | ------------------------------------------------------------ | --------- |
| `POST` | `/log` | Write log entry. Body: `{ service, level, message, extra? }` | `boolean` |

---

### TUI

| Method | Path                    | Description                                 | Response               |
| ------ | ----------------------- | ------------------------------------------- | ---------------------- |
| `POST` | `/tui/append-prompt`    | Append text to the prompt                   | `boolean`              |
| `POST` | `/tui/open-help`        | Open the help dialog                        | `boolean`              |
| `POST` | `/tui/open-sessions`    | Open the session selector                   | `boolean`              |
| `POST` | `/tui/open-themes`      | Open the theme selector                     | `boolean`              |
| `POST` | `/tui/open-models`      | Open the model selector                     | `boolean`              |
| `POST` | `/tui/submit-prompt`    | Submit the current prompt                   | `boolean`              |
| `POST` | `/tui/clear-prompt`     | Clear the prompt                            | `boolean`              |
| `POST` | `/tui/execute-command`  | Execute a command (`{ command }`)           | `boolean`              |
| `POST` | `/tui/show-toast`       | Show toast (`{ title?, message, variant }`) | `boolean`              |
| `GET`  | `/tui/control/next`     | Wait for the next control request           | Control request object |
| `POST` | `/tui/control/response` | Respond to a control request (`{ body }`)   | `boolean`              |

---

### Auth

| Method | Path        | Description                                                     | Response  |
| ------ | ----------- | --------------------------------------------------------------- | --------- |
| `PUT`  | `/auth/:id` | Set authentication credentials. Body must match provider schema | `boolean` |

---

### Events

| Method | Path     | Description                                                                   | Response                  |
| ------ | -------- | ----------------------------------------------------------------------------- | ------------------------- |
| `GET`  | `/event` | Server-sent events stream. First event is `server.connected`, then bus events | Server-sent events stream |

---

### Docs

| Method | Path   | Description               | Response                    |
| ------ | ------ | ------------------------- | --------------------------- |
| `GET`  | `/doc` | OpenAPI 3.1 specification | HTML page with OpenAPI spec |


</details>
