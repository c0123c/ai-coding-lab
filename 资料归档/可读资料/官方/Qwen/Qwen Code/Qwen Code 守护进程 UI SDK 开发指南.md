# Qwen Code 守护进程 UI SDK 开发指南

说明客户端分层、事件、聊天渲染、工具预览、状态、取消和子 Agent 展示，帮助开发网页聊天或终端客户端。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-08-19](<https://github.com/QwenLM/qwen-code/commit/b5577b7d116864339703fa60dbb0df46892d51aa>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/developers/daemon-ui/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code 守护进程 UI SDK 开发指南.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 守护进程 UI SDK — 开发人员指南

`@qwen-code/sdk/daemon` 子路径为守护进程 
 客户端提供共享 UI 原语。目前的采用目标是网络聊天和网络终端；本机本地 
TUI、通道和 IDE 集成保持其现有的默认路径，同时 
 守护进程 UI 合约稳定。本指南涵盖了 
PR #4353 引入的 API 表面（PR #4328 共享 UI 转录层的统一后续版本）。

## 三层模型

```
Daemon SSE wire (NDJSON envelopes)
   │
   ▼
normalizeDaemonEvent(envelope) → DaemonUiEvent[]
   │
   ▼
reduceDaemonTranscriptEvents(state, events) → DaemonTranscriptState
   │                                            { blocks, currentToolCallId,
   │                                              approvalMode, toolProgress, ... }
   ▼
daemonBlockToMarkdown(block) / ToHtml / ToPlainText  ← your renderer plugs here
```

- **Normalizer**：采用原始守护进程 SSE 信封，返回输入的 UI 事件
- **Reducer**：将事件累积到转录状态机中
- **渲染助手**：将状态块项目到可渲染字符串

## 快速启动

```ts
import {
  DaemonSessionClient,
  createDaemonTranscriptStore,
  normalizeDaemonEvent,
  daemonBlockToMarkdown,
  selectCurrentTool,
  selectApprovalMode,
} from '@qwen-code/sdk/daemon';

const session = await DaemonSessionClient.createOrAttach(client, {
  workspaceCwd,
});
const store = createDaemonTranscriptStore();

for await (const envelope of session.events({ signal })) {
  const events = normalizeDaemonEvent(envelope, {
    clientId: session.clientId,
    suppressOwnUserEcho: true,
  });
  store.dispatch(events);
}

// Read state from any subscriber
store.subscribe(() => {
  const state = store.getSnapshot();
  const currentTool = selectCurrentTool(state);
  const mode = selectApprovalMode(state);
  const markdown = state.blocks.map(daemonBlockToMarkdown).join('\n\n');
  myRenderer.render({ markdown, currentTool, mode });
});
```

## 事件分类（28+类型）

`DaemonUiEvent` 是所有 UI- 面临事件的可区分联合：

### 聊天流事件

|活动|当|
| ---------------------------- | ----------------------------------------------------- |
| `user.text.delta`            |用户消息块从守护进程到达|
| `assistant.text.delta`       |助理流式传输块|
| `assistant.done`             |提示完成（来自sendPrompt解析）|
| `thought.text.delta`         |Agent 推理块|
| `tool.update`                |工具调用生命周期（运行/完成/取消）|
| `shell.output`               |Shell 工具 stdout/stderr 块|
| `permission.request`         |工具需要用户授权|
| `permission.resolved`        |许可决定已到达|
| `model.changed`              |会话模型已切换|
| `status` / `debug` / `error` |状态/调试/错误块|

### 会话元事件（PR-A）

|活动|当|
| ------------------------------- | ------------------------------------------------ |
| `session.metadata.changed`      |会话标题/显示名称已更新|
| `session.approval_mode.changed` |模式切换（计划/默认/yolo/自动编辑）|
| `session.available_commands`    |刷新斜线命令列表|

### 工作区事件（PR-A、Wave 3-4）

|活动|当|
| -------------------------------------- | ------------------------------------- |
| `workspace.memory.changed`             |QWEN.md/内存文件修改|
| `workspace.agent.changed`              |子agent创建/更新/删除|
| `workspace.tool.toggled`               |内置工具启用/禁用|
| `workspace.initialized`                |`qwen init`已完成|
| `workspace.mcp.budget_warning`         |MCP 儿童计数接近上限|
| `workspace.mcp.child_refused`          |MCP 服务器因预算而被拒绝|
| `workspace.mcp.server_restarted`       |手动MCP重启成功|
| `workspace.mcp.server_restart_refused` |手动重启被阻止|

### 身份验证设备流事件（PR-A、Wave 4 OAuth）

`auth.device_flow.{started,throttled,authorized,failed,cancelled}`

每个都带有守护进程的`deviceFlowId`。失败的事件带有一个封闭枚举
`errorKind`（封闭枚举 - 请参阅从`@qwen-code/sdk/daemon`导出的`KNOWN_DEVICE_FLOW_ERROR_KINDS`以获取规范列表，当前：`expired_token` / `access_denied` / `invalid_grant` / `upstream_error` / `persist_failed` / `not_found_or_evicted`）。

## 渲染合同（PR-D）

三个投影助手，一个预览助手。所有对 `block.kind`
 或 `preview.kind` 的歧视：

```ts
daemonBlockToMarkdown(block, { sanitizeUrls?, maxFieldLength?, locale? })
daemonBlockToHtml(block, { sanitizer?, ...renderOpts })
daemonBlockToPlainText(block, renderOpts)
daemonToolPreviewToMarkdown(preview, renderOpts)
```

### Cookbook：将成绩单呈现为 Markdown

```ts
const markdown = state.blocks
  .map((b) => daemonBlockToMarkdown(b, { sanitizeUrls: true }))
  .join('\n\n');
```

### 食谱：渲染为已清理的 HTML 的 SSR

```ts
import DOMPurify from 'dompurify';
import MarkdownIt from 'markdown-it';
const md = new MarkdownIt();

const html = state.blocks
  .map((b) => {
    // Two-stage pipeline: markdown → HTML → DOMPurify
    const rawHtml = md.render(daemonBlockToMarkdown(b));
    return DOMPurify.sanitize(rawHtml);
  })
  .join('\n');
```

或者使用内置保守的 HTML 渲染器（没有 markdown 解析，只是 
HTML 转义）：

```ts
const html = state.blocks
  .map((b) => daemonBlockToHtml(b, { sanitizer: DOMPurify.sanitize }))
  .join('\n');
```

### Cookbook：复制粘贴纯文本

```ts
const plain = state.blocks.map(daemonBlockToPlainText).join('\n');
navigator.clipboard.writeText(plain);
```

## 工具预览分类（13 种类）

|种类|表面|
| --------------------- | ------------------------------------------------- |
| `ask_user_question`   |带选项的多项选择题|
| `command`             |Bash 风格的命令 + cwd|
| `file_diff`           |使用 oldText/newText 或补丁编辑文件|
| `file_read`           |路径+可选行范围|
| `web_fetch`           |URL + HTTP 方法|
| `mcp_invocation`      |MCP 服务器+工具+参数摘要|
| `code_block`          |语言标记的代码片段|
| `search`              |查询+结果计数+热门结果|
| `tabular`             |列 + 行（上限为 50，已标记截断）|
| `image_generation`    |提示+可选缩略图URL|
| `subagent_delegation` |Agent 姓名+任务|
| `key_value`           |通用 label/value 行|
| `generic`             |后备总结|

每个都有一个 `daemonToolPreviewToMarkdown` 投影。自定义渲染器可以
在`preview.kind`上调度，以实现丰富的每种类型显示（带有
语法突出显示的文件差异、MCP服务器徽章、图像缩略图等）。

## 状态选择器 (PR-E)

```ts
selectCurrentTool(state); // → DaemonToolTranscriptBlock | undefined
selectApprovalMode(state); // → 'plan' | 'default' | 'auto-edit' | 'yolo' | undefined
selectToolProgress(state, toolCallId); // → { ratio?, step? } | undefined
selectPendingPermissionBlocks(state); // → ReadonlyArray<DaemonPermissionTranscriptBlock>
selectTranscriptBlocks(state); // → ReadonlyArray<DaemonTranscriptBlock>
selectTranscriptBlocksOrderedByEventId(state); // sorted by daemon-monotonic id

// PR-K — sub-agent nesting
selectSubagentChildBlocks(state, parentToolCallId); // direct children only
isSubagentChildBlock(block); // type guard: was this tool invoked inside a sub-agent?
```

`currentToolCallId`由减速机自动维护：

- 当刀具进入飞行状态时设置（`running` / `in_progress` / `pending` / `confirming`）
- 当工具进入终端状态时清零（`completed`/`failed`/`cancelled`/等）
- 未知状态保持不变（前向兼容）

## 取消传播（PR-E）

当`assistant.done.reason === 'cancelled'`时，减速机行走每个
飞行中刀块，并强制将其状态设置为`'cancelled'`。当父提示被取消时，守护进程 
 不保证每个运行中的 
 工具都有一个终端 `tool_call_update` — 这种传播会阻止 UI
 旋转器永远旋转。

子 agent 子级与其父级一起取消，因为 
 取消会迭代 `toolBlockByCallId`、
 中的每个飞行中工具块，而不仅仅是当前指针。

## 子agent嵌套(PR-K)

当主 agent 委托给子 agent（`Task` 工具或 
 等效工具）时，守护进程会在 
 上标记 `parentToolCallId` 和 `subagentType` **子**工具通过`tool_call._meta`调用。减速器读取 
 和：

- 将 `parentToolCallId` + `subagentType` 镜像到
  `DaemonToolTranscriptBlock`
- 解决 `parentBlockId` （父母的成绩单块 `id`）当
父块已经处于状态；否则，当父块稍后出现时，将其保留为 `undefined` 和 
 回填

无序到达（子项在父项之前）的处理是透明的。父级被 `maxBlocks` 修剪的 
 子级保留 `parentToolCallId`
 用于选择器查询，但 `parentBlockId` 为空（悬空 id 
 将不再通过 `blockIndexById` 解析）。

```ts
import {
  selectSubagentChildBlocks,
  isSubagentChildBlock,
} from '@qwen-code/sdk/daemon';

// Render a parent tool block, then walk children:
function renderToolBlock(state, block) {
  if (block.kind !== 'tool') return renderOther(block);
  const children = selectSubagentChildBlocks(state, block.toolCallId);
  return (
    <ToolBlock block={block}>
      {children.length > 0 && (
        <Indent>
          {children.map((c) => renderToolBlock(state, c))}
        </Indent>
      )}
    </ToolBlock>
  );
}

// Or filter top-level vs. nested at render time:
const topLevel = state.blocks.filter((b) => !isSubagentChildBlock(b));
```

`selectSubagentChildBlocks` 仅返回 **direct** 子项。递归地遍历
以渲染嵌套子agents（
子agent内的子agent）。守护进程不会发出循环，但通过 
`parentBlockId` 向上行走的渲染器仍应防御性地检测它们（例如，深度上限或 
 访问集）。

自引用 (`parentToolCallId === toolCallId`) 在到达减速器之前被 
 标准化器删除。

## 时间语义 (PR-B)

```ts
interface DaemonTranscriptBlockBase {
  eventId?: number; // PRIMARY sort key — daemon-monotonic
  serverTimestamp?: number; // PREFERRED display — daemon-authoritative
  clientReceivedAt: number; // FALLBACK — local clock
  createdAt: number; // @deprecated alias for clientReceivedAt
}
```

**显示长会话时始终按 `eventId`**（使用 `selectTranscriptBlocksOrderedByEventId`）
 排序。守护进程单调 cursor 在重新连接后重放的 SSE 中保留 
；客户端时钟不是。

**始终格式化显示来自 `serverTimestamp`** 的时间戳（
回退到 `clientReceivedAt`）。仅当多个客户端都从守护程序时钟读取时，查看同一会话 
 的多个客户端才会看到相同的“5 分钟前”。

```ts
import { formatBlockTimestamp } from '@qwen-code/sdk/daemon';

const label = formatBlockTimestamp(block, {
  locale: 'zh-CN',
  timeZone: 'Asia/Shanghai',
  timeStyle: 'short',
});
```

## 适配器一致性 (PR-G)

验证您的适配器将 SDK 的参考语料库投影为语义上 
 等效的输出：

```ts
import { runAdapterConformanceSuite } from '@qwen-code/sdk/daemon';

it('my adapter conforms to daemon UI corpus', () => {
  const result = runAdapterConformanceSuite({
    reduce: (events) => myReducer(events),
    renderToText: (state) => myRenderer(state),
  });
  expect(result.failed).toEqual([]);
});
```

夹具语料库（`DAEMON_UI_CONFORMANCE_FIXTURES`）涵盖聊天、工具
生命周期、文件编辑、MCP、权限、MCP预算警告、取消、
格式错误的有效负载编辑、OAuth、命令更新和子agent
嵌套。 （计数可在运行时导出——读取
`DAEMON_UI_CONFORMANCE_FIXTURES.length`。）

**格式无关** — 您的适配器可以渲染为 ANSI / HTML / markdown /
JSX;该框架仅通过 `expectedContains` 和 
`expectedAbsent` 检查语义内容。

## 错误分类（PR-A）

`DaemonUiErrorEvent.errorKind` 是从 
 守护进程的类型错误分类法传播的封闭枚举（当守护进程对其进行标记时）：

```ts
import type { DaemonErrorKind } from '@qwen-code/sdk/daemon';
// 'missing_binary' | 'blocked_egress' | 'auth_env_error' | 'init_timeout' | 'restore_timeout'
// | 'protocol_error' | 'missing_file' | 'parse_error' | 'budget_exhausted'
```

渲染器应该在 `errorKind` 上分支以获得可操作的功能：

```ts
function errorAffordance(errorKind?: DaemonErrorKind): React.ReactNode {
  switch (errorKind) {
    case 'auth_env_error': return <button>Re-authenticate</button>;
    case 'missing_file':   return <button>Choose file</button>;
    case 'blocked_egress': return <span>Network blocked — check proxy</span>;
    default:               return null;
  }
}
```

## 工具出处调度（PR-A）

`DaemonUiToolUpdateEvent.provenance` 是一个封闭枚举（`builtin` / `mcp` /
`subagent` / `unknown`）。当`mcp`时为`serverId?: string`。将其用于
图标调度和徽章：

```ts
function toolIcon(event: DaemonUiToolUpdateEvent): React.ReactNode {
  switch (event.provenance) {
    case 'mcp':      return <McpIcon server={event.serverId} />;
    case 'subagent': return <SubagentIcon />;
    case 'builtin':  return <BuiltinIcon name={event.toolName} />;
    default:         return <GenericIcon />;
  }
}
```

SDK 有一个 `mcp__<server>__<tool>` 命名启发式回退 - 即使 
 当守护进程没有明确标记出处时，MCP 工具也是可检测到的。

## 调试原因分类

`DaemonUiStatusEvent.debugReason` 是一个封闭枚举，当规范化器投影 `debug` 事件而不是类型化事件时，它会标记 
：

```ts
import type { DaemonUiDebugReason } from '@qwen-code/sdk/daemon';
// 'unrecognized_event' | 'unrecognized_session_update' | 'malformed_payload'
```

规范列表导出为 `DAEMON_UI_DEBUG_REASONS`。原因名称
是通配符命名的类别：`unrecognized_*`表示守护程序发送了
帧，此SDK版本没有案例 - 前向兼容噪音，开发人员
诊断而不是对话内容。 `malformed_*` 表示帧 
 SDK _ 确实 _ 知道带有不可用的有效负载 - 一个真正的缺陷 
 信号。

**路由因类别而异。** `unrecognized_*`诊断被路由
到有界的`unrecognizedDiagnostics`侧通道，并且永远不会进入
`blocks[]`（因此它们无法最终确定流assistant/thought块或
消耗`maxBlocks`预算）。使用
`selectUnrecognizedDiagnostics`阅读它们；上限是 `UNRECOGNIZED_DIAGNOSTICS_LIMIT`
，路由子集是 `DAEMON_UI_UNRECOGNIZED_DIAGNOSTIC_REASONS`。
`malformed_*` 诊断 — 在此分割之前遗留块仍然存在 —
 保留在转录本中作为 `DaemonStatusTranscriptBlock`，因此块级 
`debugReason` 处理现在仅适用于这些块。

渲染器过滤块应在 `debugReason` 上分支，而不是在调试 
 文本上分支 - 文本前缀是诊断性措辞，如有更改，恕不另行通知：

```ts
function hideDebugBlock(reason?: DaemonUiDebugReason): boolean {
  // Hide forward-compat noise by category so reasons a newer SDK adds are
  // covered automatically. Defect signals and client-dispatched debug
  // events (which carry no reason) keep rendering.
  return reason?.startsWith('unrecognized_') ?? false;
}
```

`status` 事件从不携带 `debugReason`，客户端本身调度的调试事件 
 也不携带（例如 Web Shell 的模型切换摘要）——
 两者都必须保持渲染。

## 前向兼容原则

守护进程 UI SDK 中的每一层都遵循 ** 前向兼容原则 **:
 未知值会 NOT 抛出；它们会优雅地降解。

- 未知的守护进程事件类型 → `debug` 事件，具有原始类型名称，
标有 `unrecognized_*` `debugReason` 并路由到有界的 
 `unrecognizedDiagnostics` 侧通道（见上文）
- 未知工具状态 → `currentToolCallId` 保持不变（未清除）
- 未知错误类型 → `errorKind` 未定义（渲染器回退到文本）
- 缺少 serverTimestamp → 回退到 `clientReceivedAt`
- 无法识别的预览形状 → `generic` 类型为 `summary`

这意味着 **SDK 可以在守护进程发射 ** 之前发送。 PR-A 的工具 
 来源启发式、PR-B 的三位置时间戳提取以及 
PR-E 的未知状态保存都是“守护进程 
 发送时就绪；未发送时安全”的示例。

## 交叉引用

- [PR #4328](<https://github.com/QwenLM/qwen-code/pull/4328>) — 基础 PR 与共享 UI 转录层
- [PR#4353](<https://github.com/QwenLM/qwen-code/pull/4353>)——这个PR（统一完整性后续）
- [问题#3803](<https://github.com/QwenLM/qwen-code/issues/3803>) — 守护进程模式提案
- [问题#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) — 模式 B v0.16 实施跟踪器


## 原文对照

<details>
<summary>展开完整原文</summary>

# Daemon UI SDK — Developer Guide

The `@qwen-code/sdk/daemon` subpath ships shared UI primitives for daemon
clients. The current adoption target is web chat and web terminal; native local
TUI, channel, and IDE integrations keep their existing default paths while the
daemon UI contract stabilizes. This guide covers the API surface introduced by
PR #4353 (the unified follow-up to PR #4328's shared UI transcript layer).

## Three-layer model

```
Daemon SSE wire (NDJSON envelopes)
   │
   ▼
normalizeDaemonEvent(envelope) → DaemonUiEvent[]
   │
   ▼
reduceDaemonTranscriptEvents(state, events) → DaemonTranscriptState
   │                                            { blocks, currentToolCallId,
   │                                              approvalMode, toolProgress, ... }
   ▼
daemonBlockToMarkdown(block) / ToHtml / ToPlainText  ← your renderer plugs here
```

- **Normalizer**: takes raw daemon SSE envelopes, returns typed UI events
- **Reducer**: accumulates events into a transcript state machine
- **Render helpers**: project state blocks to renderable strings

## Quick start

```ts
import {
  DaemonSessionClient,
  createDaemonTranscriptStore,
  normalizeDaemonEvent,
  daemonBlockToMarkdown,
  selectCurrentTool,
  selectApprovalMode,
} from '@qwen-code/sdk/daemon';

const session = await DaemonSessionClient.createOrAttach(client, {
  workspaceCwd,
});
const store = createDaemonTranscriptStore();

for await (const envelope of session.events({ signal })) {
  const events = normalizeDaemonEvent(envelope, {
    clientId: session.clientId,
    suppressOwnUserEcho: true,
  });
  store.dispatch(events);
}

// Read state from any subscriber
store.subscribe(() => {
  const state = store.getSnapshot();
  const currentTool = selectCurrentTool(state);
  const mode = selectApprovalMode(state);
  const markdown = state.blocks.map(daemonBlockToMarkdown).join('\n\n');
  myRenderer.render({ markdown, currentTool, mode });
});
```

## Event taxonomy (28+ types)

`DaemonUiEvent` is a discriminated union of all UI-facing events:

### Chat-stream events

| Event                        | When                                                  |
| ---------------------------- | ----------------------------------------------------- |
| `user.text.delta`            | User message chunk arrives from daemon                |
| `assistant.text.delta`       | Assistant streaming chunk                             |
| `assistant.done`             | Prompt completion (from sendPrompt resolve)           |
| `thought.text.delta`         | Agent reasoning chunk                                 |
| `tool.update`                | Tool call lifecycle (running / completed / cancelled) |
| `shell.output`               | Shell tool stdout/stderr chunk                        |
| `permission.request`         | Tool needs user authorization                         |
| `permission.resolved`        | Permission decision arrived                           |
| `model.changed`              | Session model switched                                |
| `status` / `debug` / `error` | Status / debug / error blocks                         |

### Session-meta events (PR-A)

| Event                           | When                                             |
| ------------------------------- | ------------------------------------------------ |
| `session.metadata.changed`      | Session title / display name updated             |
| `session.approval_mode.changed` | Mode toggled (plan / default / yolo / auto-edit) |
| `session.available_commands`    | Slash command list refreshed                     |

### Workspace events (PR-A, Wave 3-4)

| Event                                  | When                                  |
| -------------------------------------- | ------------------------------------- |
| `workspace.memory.changed`             | QWEN.md / memory file modified        |
| `workspace.agent.changed`              | Sub-agent created / updated / deleted |
| `workspace.tool.toggled`               | Builtin tool enabled / disabled       |
| `workspace.initialized`                | `qwen init` completed                 |
| `workspace.mcp.budget_warning`         | MCP child count approaching cap       |
| `workspace.mcp.child_refused`          | MCP server refused due to budget      |
| `workspace.mcp.server_restarted`       | Manual MCP restart succeeded          |
| `workspace.mcp.server_restart_refused` | Manual restart blocked                |

### Auth device-flow events (PR-A, Wave 4 OAuth)

`auth.device_flow.{started,throttled,authorized,failed,cancelled}`

Each carries the daemon's `deviceFlowId`. Failed events carry a closed-enum
`errorKind` (closed enum — see `KNOWN_DEVICE_FLOW_ERROR_KINDS` exported from `@qwen-code/sdk/daemon` for the canonical list, currently: `expired_token` / `access_denied` / `invalid_grant` / `upstream_error` / `persist_failed` / `not_found_or_evicted`).

## Render contract (PR-D)

Three projection helpers, one preview helper. All discriminate on `block.kind`
or `preview.kind`:

```ts
daemonBlockToMarkdown(block, { sanitizeUrls?, maxFieldLength?, locale? })
daemonBlockToHtml(block, { sanitizer?, ...renderOpts })
daemonBlockToPlainText(block, renderOpts)
daemonToolPreviewToMarkdown(preview, renderOpts)
```

### Cookbook: render a transcript to markdown

```ts
const markdown = state.blocks
  .map((b) => daemonBlockToMarkdown(b, { sanitizeUrls: true }))
  .join('\n\n');
```

### Cookbook: render to sanitized HTML for SSR

```ts
import DOMPurify from 'dompurify';
import MarkdownIt from 'markdown-it';
const md = new MarkdownIt();

const html = state.blocks
  .map((b) => {
    // Two-stage pipeline: markdown → HTML → DOMPurify
    const rawHtml = md.render(daemonBlockToMarkdown(b));
    return DOMPurify.sanitize(rawHtml);
  })
  .join('\n');
```

Or use the built-in conservative HTML renderer (no markdown parsing, just
HTML escape):

```ts
const html = state.blocks
  .map((b) => daemonBlockToHtml(b, { sanitizer: DOMPurify.sanitize }))
  .join('\n');
```

### Cookbook: copy-paste plain text

```ts
const plain = state.blocks.map(daemonBlockToPlainText).join('\n');
navigator.clipboard.writeText(plain);
```

## Tool preview taxonomy (13 kinds)

| Kind                  | Surface                                           |
| --------------------- | ------------------------------------------------- |
| `ask_user_question`   | Multi-choice question with options                |
| `command`             | Bash-style command + cwd                          |
| `file_diff`           | File edit with oldText/newText or patch           |
| `file_read`           | Path + optional line range                        |
| `web_fetch`           | URL + HTTP method                                 |
| `mcp_invocation`      | MCP server + tool + args summary                  |
| `code_block`          | Language-tagged code snippet                      |
| `search`              | Query + result count + top results                |
| `tabular`             | Columns + rows (capped at 50, truncation flagged) |
| `image_generation`    | Prompt + optional thumbnail URL                   |
| `subagent_delegation` | Agent name + task                                 |
| `key_value`           | Generic label/value rows                          |
| `generic`             | Fallback summary                                  |

Each has a `daemonToolPreviewToMarkdown` projection. Custom renderers can
dispatch on `preview.kind` for rich per-type display (file diff with
syntax highlighting, MCP server badge, image thumbnail, etc.).

## State selectors (PR-E)

```ts
selectCurrentTool(state); // → DaemonToolTranscriptBlock | undefined
selectApprovalMode(state); // → 'plan' | 'default' | 'auto-edit' | 'yolo' | undefined
selectToolProgress(state, toolCallId); // → { ratio?, step? } | undefined
selectPendingPermissionBlocks(state); // → ReadonlyArray<DaemonPermissionTranscriptBlock>
selectTranscriptBlocks(state); // → ReadonlyArray<DaemonTranscriptBlock>
selectTranscriptBlocksOrderedByEventId(state); // sorted by daemon-monotonic id

// PR-K — sub-agent nesting
selectSubagentChildBlocks(state, parentToolCallId); // direct children only
isSubagentChildBlock(block); // type guard: was this tool invoked inside a sub-agent?
```

`currentToolCallId` is automatically maintained by the reducer:

- Set when a tool enters in-flight status (`running` / `in_progress` / `pending` / `confirming`)
- Cleared when tool enters terminal status (`completed` / `failed` / `cancelled` / etc.)
- Unknown statuses leave it untouched (forward-compat)

## Cancellation propagation (PR-E)

When `assistant.done.reason === 'cancelled'`, the reducer walks every
in-flight tool block and force-sets its status to `'cancelled'`. Daemon
does not guarantee a terminal `tool_call_update` for every in-flight
tool when the parent prompt is cancelled — this propagation prevents UI
spinners from spinning forever.

Sub-agent children are cancelled together with their parent because
cancellation iterates every in-flight tool block in `toolBlockByCallId`,
not just the current pointer.

## Sub-agent nesting (PR-K)

When the main agent delegates to a sub-agent (the `Task` tool, or
equivalent), the daemon stamps `parentToolCallId` and `subagentType` on
the **child** tool calls via `tool_call._meta`. The reducer reads both
and:

- Mirrors `parentToolCallId` + `subagentType` onto
  `DaemonToolTranscriptBlock`
- Resolves `parentBlockId` (the parent's transcript block `id`) when the
  parent block is already in state; otherwise leaves it `undefined` and
  back-fills when the parent block later appears

Out-of-order arrival (child before parent) is handled transparently. A
child whose parent gets trimmed by `maxBlocks` keeps `parentToolCallId`
for selector queries, but `parentBlockId` is nulled (the dangling id
would no longer resolve via `blockIndexById`).

```ts
import {
  selectSubagentChildBlocks,
  isSubagentChildBlock,
} from '@qwen-code/sdk/daemon';

// Render a parent tool block, then walk children:
function renderToolBlock(state, block) {
  if (block.kind !== 'tool') return renderOther(block);
  const children = selectSubagentChildBlocks(state, block.toolCallId);
  return (
    <ToolBlock block={block}>
      {children.length > 0 && (
        <Indent>
          {children.map((c) => renderToolBlock(state, c))}
        </Indent>
      )}
    </ToolBlock>
  );
}

// Or filter top-level vs. nested at render time:
const topLevel = state.blocks.filter((b) => !isSubagentChildBlock(b));
```

`selectSubagentChildBlocks` returns **direct** children only. Walk
recursively to render nested sub-agents (a sub-agent inside a
sub-agent). Daemon does not emit cycles, but renderers walking up via
`parentBlockId` should still detect them defensively (e.g., depth cap or
visited set).

Self-references (`parentToolCallId === toolCallId`) are dropped by the
normalizer before reaching the reducer.

## Time semantics (PR-B)

```ts
interface DaemonTranscriptBlockBase {
  eventId?: number; // PRIMARY sort key — daemon-monotonic
  serverTimestamp?: number; // PREFERRED display — daemon-authoritative
  clientReceivedAt: number; // FALLBACK — local clock
  createdAt: number; // @deprecated alias for clientReceivedAt
}
```

**Always sort by `eventId`** (use `selectTranscriptBlocksOrderedByEventId`)
when displaying long sessions. The daemon-monotonic cursor is preserved
across SSE replay-after-reconnect; client clocks are not.

**Always format display timestamps from `serverTimestamp`** (with
fallback to `clientReceivedAt`). Multiple clients viewing the same session
see the same "5 minutes ago" only when both read from the daemon clock.

```ts
import { formatBlockTimestamp } from '@qwen-code/sdk/daemon';

const label = formatBlockTimestamp(block, {
  locale: 'zh-CN',
  timeZone: 'Asia/Shanghai',
  timeStyle: 'short',
});
```

## Adapter conformance (PR-G)

Validate your adapter projects the SDK's reference corpus to semantically
equivalent output:

```ts
import { runAdapterConformanceSuite } from '@qwen-code/sdk/daemon';

it('my adapter conforms to daemon UI corpus', () => {
  const result = runAdapterConformanceSuite({
    reduce: (events) => myReducer(events),
    renderToText: (state) => myRenderer(state),
  });
  expect(result.failed).toEqual([]);
});
```

The fixture corpus (`DAEMON_UI_CONFORMANCE_FIXTURES`) covers chat, tool
lifecycle, file edits, MCP, permissions, MCP budget warning, cancellation,
malformed payload redaction, OAuth, command updates, and sub-agent
nesting. (Count is derivable at runtime — read
`DAEMON_UI_CONFORMANCE_FIXTURES.length`.)

**Format-agnostic** — your adapter can render to ANSI / HTML / markdown /
JSX; the framework only checks semantic content via `expectedContains` and
`expectedAbsent`.

## Error categorization (PR-A)

`DaemonUiErrorEvent.errorKind` is a closed-enum propagated from the
daemon's typed-error taxonomy (when the daemon stamps it):

```ts
import type { DaemonErrorKind } from '@qwen-code/sdk/daemon';
// 'missing_binary' | 'blocked_egress' | 'auth_env_error' | 'init_timeout' | 'restore_timeout'
// | 'protocol_error' | 'missing_file' | 'parse_error' | 'budget_exhausted'
```

Renderers should branch on `errorKind` for actionable affordances:

```ts
function errorAffordance(errorKind?: DaemonErrorKind): React.ReactNode {
  switch (errorKind) {
    case 'auth_env_error': return <button>Re-authenticate</button>;
    case 'missing_file':   return <button>Choose file</button>;
    case 'blocked_egress': return <span>Network blocked — check proxy</span>;
    default:               return null;
  }
}
```

## Tool provenance dispatch (PR-A)

`DaemonUiToolUpdateEvent.provenance` is a closed-enum (`builtin` / `mcp` /
`subagent` / `unknown`). With `serverId?: string` when `mcp`. Use it for
icon dispatch and badging:

```ts
function toolIcon(event: DaemonUiToolUpdateEvent): React.ReactNode {
  switch (event.provenance) {
    case 'mcp':      return <McpIcon server={event.serverId} />;
    case 'subagent': return <SubagentIcon />;
    case 'builtin':  return <BuiltinIcon name={event.toolName} />;
    default:         return <GenericIcon />;
  }
}
```

The SDK has a `mcp__<server>__<tool>` naming heuristic fallback — even
when daemon doesn't explicitly stamp provenance, MCP tools are detectable.

## Debug reason categorization

`DaemonUiStatusEvent.debugReason` is a closed-enum the normalizer stamps
when it projects a `debug` event instead of a typed event:

```ts
import type { DaemonUiDebugReason } from '@qwen-code/sdk/daemon';
// 'unrecognized_event' | 'unrecognized_session_update' | 'malformed_payload'
```

The canonical list is exported as `DAEMON_UI_DEBUG_REASONS`. Reason names
are wildcard-named categories: `unrecognized_*` means the daemon sent a
frame this SDK version has no case for — forward-compat noise, developer
diagnostics rather than conversation content. `malformed_*` means a frame
the SDK _does_ know arrived with an unusable payload — a real defect
signal.

**Routing differs by category.** `unrecognized_*` diagnostics are routed
to the bounded `unrecognizedDiagnostics` sidechannel and never enter
`blocks[]` (so they cannot finalize a streaming assistant/thought block or
consume the `maxBlocks` budget). Read them with
`selectUnrecognizedDiagnostics`; the cap is `UNRECOGNIZED_DIAGNOSTICS_LIMIT`
and the routed subset is `DAEMON_UI_UNRECOGNIZED_DIAGNOSTIC_REASONS`.
`malformed_*` diagnostics — and legacy blocks persisted before this split —
stay in the transcript as `DaemonStatusTranscriptBlock`s, so block-level
`debugReason` handling now applies to those only.

Renderers filtering blocks should branch on `debugReason`, not the debug
text — the text prefix is diagnostic wording and changes without notice:

```ts
function hideDebugBlock(reason?: DaemonUiDebugReason): boolean {
  // Hide forward-compat noise by category so reasons a newer SDK adds are
  // covered automatically. Defect signals and client-dispatched debug
  // events (which carry no reason) keep rendering.
  return reason?.startsWith('unrecognized_') ?? false;
}
```

`status` events never carry a `debugReason`, and neither do debug events
dispatched by clients themselves (e.g. Web Shell's model-switch summary) —
both must keep rendering.

## Forward-compat principles

Every layer in the daemon UI SDK follows the **forward-compat principle**:
unknown values do NOT throw; they degrade gracefully.

- Unknown daemon event types → `debug` event with the raw type name,
  stamped with an `unrecognized_*` `debugReason` and routed to the bounded
  `unrecognizedDiagnostics` sidechannel (see above)
- Unknown tool status → `currentToolCallId` left untouched (no clear)
- Unknown error kind → `errorKind` undefined (renderer falls back to text)
- Missing serverTimestamp → falls back to `clientReceivedAt`
- Unrecognized preview shape → `generic` kind with `summary`

This means **SDK can ship ahead of daemon emission**. PR-A's tool
provenance heuristic, PR-B's three-location timestamp extraction, and
PR-E's unknown-status preservation are all examples of "ready when daemon
sends; safe when it doesn't."

## Cross-references

- [PR #4328](<https://github.com/QwenLM/qwen-code/pull/4328>) — base PR with the shared UI transcript layer
- [PR #4353](<https://github.com/QwenLM/qwen-code/pull/4353>) — this PR (unified completeness follow-up)
- [Issue #3803](<https://github.com/QwenLM/qwen-code/issues/3803>) — daemon mode proposal
- [Issue #4175](<https://github.com/QwenLM/qwen-code/issues/4175>) — Mode B v0.16 implementation tracker


</details>
