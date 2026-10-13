# Qwen Code 消息渠道插件开发指南

解释平台适配器、消息结构、附件、扩展清单及可选接口，帮助把编码 Agent 接入消息平台。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/QwenLM/qwen-code/commit/cfb173ec4669b36fb5459bd721b90a388bd0542e>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/developers/channel-plugins.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/developers/channel-plugins.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 频道插件开发人员指南

通道插件将 Qwen Code 连接到消息平台。它被打包为 [ 扩展 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/extension/introduction>) 并在启动时加载。有关安装和配置插件的面向用户的文档，请参阅 [插件](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/channels/plugins>)。

## 它如何组合在一起

您的插件位于平台适配器层。您处理特定于平台的问题（连接、接收消息、发送响应）。 `ChannelBase` 处理其他一切（访问控制、会话路由、提示排队、斜线命令、崩溃恢复）。

```
Your Plugin  →  builds Envelope  →  handleInbound()
ChannelBase  →  gates → commands → routing → ChannelAgentBridge.prompt()
ChannelBase  →  calls your sendMessage() with the agent's response
```

`ChannelAgentBridge` 是面向适配器的桥接合约。当前独立的 `qwen channel start` 路径提供了 `AcpBridge`，但插件代码应将构造函数参数键入为 `ChannelAgentBridge`，以便同一适配器稍后可以在其他桥接实现后面运行。

现有 TypeScript 插件的迁移注意事项：如果您的适配器构造函数或工厂显式将 `bridge` 键入为 `AcpBridge`，请将该注释更改为 `ChannelAgentBridge` 并继续仅使用该合约公开的方法。 JavaScript插件在运行时不受影响，独立的`qwen channel start`仍然通过当前的`AcpBridge`实现。

## 运行时模式

相同的插件适配器可以由任一通道运行时托管：

- `qwen channel start [name]` 是独立的 ACP- 支持的服务。它仍然使用 `AcpBridge` 并仍然是在守护进程之外运行通道的稳定命令。
- `qwen serve --channel <name>` 和可重复的 `--channel` 标志启动实验性守护进程管理的通道工作人员。命名通道按所属工作区分组，每个所属运行时有一名工作人员。 `--channel all` 有意仅启动主工作区的已配置通道。 Workers 属于 `qwen serve`，通过 SDK 连接到该守护进程，并向适配器传递由 `DaemonChannelBridge` 支持的 `ChannelAgentBridge` 外观。

守护程序管理的通道继承守护程序的生命周期和状态报告。它们是故意在进程外的，因此适配器或平台 SDK 故障不会使守护程序崩溃。每个命名通道必须准确解析为一个已注册、受信任的工作区；它的工作线程接收该运行时的规范 cwd 和环境覆盖。当注册多个工作区时，没有 cwd 的 user/system 通道不明确，而工作区本地设置文件中的通道默认属于该工作区。 `--channel all` 仅保留为主，不能与命名选择组合。

## 插件对象

您的扩展入口点导出符合 `ChannelPlugin` 的 `plugin`：

```typescript
import type { ChannelPlugin } from '@qwen-code/channel-base';
import { MyChannel } from './MyChannel.js';

export const plugin: ChannelPlugin = {
  channelType: 'my-platform', // Unique ID, used in settings.json "type" field
  displayName: 'My Platform', // Shown in CLI output
  requiredConfigFields: ['apiKey'], // Validated at startup (beyond standard ChannelConfig)
  createChannel: (name, config, bridge, options) =>
    new MyChannel(name, config, bridge, options),
};
```

## 通道适配器

扩展`ChannelBase`并实现三个方法：

```typescript
import { ChannelBase } from '@qwen-code/channel-base';
import type {
  ChannelBaseOptions,
  ChannelAgentBridge,
  ChannelConfig,
  Envelope,
  SessionTarget,
} from '@qwen-code/channel-base';

export class MyChannel extends ChannelBase {
  constructor(
    name: string,
    config: ChannelConfig,
    bridge: ChannelAgentBridge,
    options?: ChannelBaseOptions,
  ) {
    super(name, config, bridge, options);
  }

  async connect(): Promise<void> {
    // Connect to your platform, register message handlers
    // When a message arrives:
    const envelope: Envelope = {
      channelName: this.name,
      senderId: '...', // Stable, unique platform user ID
      senderName: '...', // Display name
      chatId: '...', // Chat/conversation ID (distinct for DMs vs groups)
      text: '...', // Message text (strip @mentions)
      isGroup: false, // Accurate — used by GroupGate
      isMentioned: false, // Accurate — used by GroupGate
      isReplyToBot: false, // Accurate — used by GroupGate
    };
    this.handleInbound(envelope);
  }

  async sendMessage(chatId: string, text: string): Promise<void> {
    // Format markdown → platform format, chunk if needed, deliver
  }

  disconnect(): void {
    // Clean up connections
  }
}
```

大多数适配器应保持不变地通过 `options`。如果适配器创建自己的 `SessionRouter` 并将该路由器传递给 `super()`，请在 `ChannelBaseOptions` 中设置 `registerBridgeEvents: true`，以便 `ChannelBase` 仍直接接收 `toolCall` 和 `sessionDied` 事件。对于通道网关提供的路由器，请将其保留为未设置。

如果您的适配器公开 shell 命令或 BTW 附带问题行为，请在启用之前检查相应的 `bridge.shellCommand` / `bridge.btw` 方法是否存在。守护进程管理的工作线程会忽略这些可选方法，除非守护进程通告匹配的 `session_shell_command` / `session_btw` 功能。

## 信封

您根据平台数据构建的标准化消息对象。布尔标志驱动门逻辑，因此它们必须准确。

|领域|类型|必填|注释|
| ---------------- | ------------ | -------- | -------------------------------------------------------------------------- |
| `channelName`    |字符串|是的|使用`this.name`|
| `senderId`       |字符串|是的|跨消息必须稳定（用于会话路由+访问控制）|
| `senderName`     |字符串|是的|显示名称|
| `chatId`         |字符串|是的|必须将 DM 与群组区分开来|
| `chatName`       |字符串|否|Group/conversation 平台提供的名称|
| `text`           |字符串|是的|剥离机器人@提及|
| `threadId`       |字符串|否|对于 `sessionScope: "thread"`|
| `messageId`      |字符串|否|平台消息 ID — 对于响应关联有用|
| `isGroup`        |布尔值|是的|GroupGate靠这个|
| `isMentioned`    |布尔值|是的|GroupGate靠这个|
| `isReplyToBot`   |布尔值|是的|GroupGate靠这个|
| `referencedText` |字符串|否|引用的消息 - 作为上下文前置|
| `imageBase64`    |字符串|否|Base64 编码图像（旧版 — 更喜欢 `attachments`）|
| `imageMimeType`  |字符串|否|例如，`image/jpeg`（旧版 — 更喜欢 `attachments`）|
| `attachments`    |附件[]|否|结构化媒体附件（见下文）|

### 附件

将 `attachments` 数组用于图像、文件、音频和视频。 `handleInbound()` 自动解析它们：具有 base64 `data` 的图像作为视觉输入发送到模型，具有 `filePath` 的文件将其路径附加到提示中，以便 agent 可以读取它们。

```typescript
interface Attachment {
  type: 'image' | 'file' | 'audio' | 'video';
  data?: string; // base64-encoded data (images, small files)
  filePath?: string; // absolute path to local file (large files saved to disk)
  mimeType: string; // e.g. 'application/pdf', 'image/jpeg'
  fileName?: string; // original file name from the platform
}
```

示例 - 在适配器中处理文件上传：

```typescript
import { writeFileSync, mkdirSync, existsSync } from 'node:fs';
import { join } from 'node:path';
import { tmpdir } from 'node:os';

const buf = await downloadFromPlatform(fileId);
const dir = join(tmpdir(), 'channel-files');
if (!existsSync(dir)) mkdirSync(dir, { recursive: true });
const filePath = join(dir, fileName);
writeFileSync(filePath, buf);

envelope.attachments = [
  {
    type: 'file',
    filePath,
    mimeType: 'application/pdf',
    fileName,
  },
];
```

旧的 `imageBase64`/`imageMimeType` 字段仍可用于向后兼容，但 `attachments` 是新代码的首选。

## 扩展清单

您的 `qwen-extension.json` 声明通道类型。该密钥必须与插件对象中的 `channelType` 匹配：

```json
{
  "name": "my-channel-extension",
  "version": "1.0.0",
  "channels": {
    "my-platform": {
      "entry": "dist/index.js",
      "displayName": "My Platform Channel"
    }
  }
}
```

## 可选扩展点

**自定义斜杠命令** - 在构造函数中注册：

```typescript
this.registerCommand('mycommand', async (envelope, args) => {
  await this.sendMessage(envelope.chatId, 'Response');
  return true; // handled, don't forward to agent
});
```

**工作指示器** — 覆盖`onPromptStart()`和`onPromptEnd()`以显示特定于平台的键入指示器。这些钩子仅在提示实际开始处理时触发 - 不适用于缓冲消息（收集模式）或 gated/blocked 消息：

```typescript
protected override onPromptStart(chatId: string, sessionId: string, messageId?: string): void {
  this.platformClient.sendTyping(chatId); // your platform API
}

protected override onPromptEnd(chatId: string, sessionId: string, messageId?: string): void {
  this.platformClient.stopTyping(chatId);
}
```

**工具调用挂钩** — 覆盖`onToolCall()`以显示agent活动（例如，“正在运行shell命令...”）。

**Streaming hooks** — 覆盖 `onResponseChunk(chatId, chunk, sessionId, segment)` 以实现每个块的渐进式显示（例如，就地编辑消息）。覆盖 `onResponseComplete(chatId, fullText, sessionId, segment)` 以自定义最终交付。在守护程序管理的命名任务模式下，`segment.sourceLabel` 是该段的不可变传递元数据。在每个独立可见的消息或卡片上渲染一次，包括单独可见的最终响应，但不要将其添加到原始缓冲区或模型文本中。从 `onOutputSegmentEnd()` 中清除适配器拥有的段状态。

**命名任务归因** - `sendThreadMessage(chatId, threadId, text, sourceLabel)` 接收一次性和主动交付边界的相同可选纯文本标签。默认实现处理普通消息。覆盖传递、分割消息、发出卡片或提供回退发送的适配器必须在每个独立可见边界处重复标签，仅转义目标标记方言的标签，并将其呈现的大小包含在平台限制中。在呈现前针对原始响应运行无回复检查、媒体标记投影、审核散列、转录持久性和重试正文捕获；如果为了重新启动安全重试而保留传递，则单独保留捕获的标签。

互动`ChannelUserInputRequestContext`还带有`sourceLabel`。卡、终端替换和普通回退必须保留它，而不会削弱现有的请求、会话、运行、所有者和目标检查。

**主动传送** — 当适配器可以在没有活动入站请求的情况下发送时，覆盖 `supportsProactiveSend()` 以返回 `true`。 `ChannelBase` 使用此功能来实现持久通道循环、Webhook 任务、后台 agent 结果和守护进程传递。默认目标策略拒绝线程目标；仅针对您的平台可以安全交付的目标形状覆盖受保护的目标检查：

```typescript
override supportsProactiveSend(): boolean {
  return true;
}

protected override supportsProactiveTarget(target: SessionTarget): boolean {
  return target.threadId === undefined;
}

protected override async pushProactive(
  target: SessionTarget,
  text: string,
  sourceLabel?: string,
): Promise<void> {
  await this.sendThreadMessage(
    target.chatId,
    target.threadId,
    text,
    sourceLabel,
  );
}
```

当通用守护程序传递接受不同的目标形状时，请使用 `supportsProactiveDeliveryTarget()`；当 Webhook 传递与循环和后台结果不同时，请使用 `supportsProactiveWebhookTarget()`。拒绝不受支持的目标，而不是退回到另一个对话。

**Media** — 用 images/files. 填充 `envelope.attachments` 请参阅上面的 [ 附件 ](<#附件>)。

## 参考实现

- **插件示例** (`packages/channels/plugin-example/`) — 基于 WebSocket 的最小适配器，良好的起点
- **Telegram** (`packages/channels/telegram/`) — 功能齐全：图像、文件、格式、打字指示器
- **DingTalk** (`packages/channels/dingtalk/`) — 基于流的富文本处理


## 原文对照

<details>
<summary>展开完整原文</summary>

# Channel Plugin Developer Guide

A channel plugin connects Qwen Code to a messaging platform. It's packaged as an [extension](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/extension/introduction>) and loaded at startup. For user-facing docs on installing and configuring plugins, see [Plugins](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/channels/plugins>).

## How It Fits Together

Your plugin sits in the Platform Adapter layer. You handle platform-specific concerns (connecting, receiving messages, sending responses). `ChannelBase` handles everything else (access control, session routing, prompt queuing, slash commands, crash recovery).

```
Your Plugin  →  builds Envelope  →  handleInbound()
ChannelBase  →  gates → commands → routing → ChannelAgentBridge.prompt()
ChannelBase  →  calls your sendMessage() with the agent's response
```

`ChannelAgentBridge` is the adapter-facing bridge contract. The current standalone `qwen channel start` path provides an `AcpBridge`, but plugin code should type constructor parameters as `ChannelAgentBridge` so the same adapter can run behind other bridge implementations later.

Migration note for existing TypeScript plugins: if your adapter constructor or factory explicitly types `bridge` as `AcpBridge`, change that annotation to `ChannelAgentBridge` and keep using only the methods exposed by that contract. JavaScript plugins are unaffected at runtime, and standalone `qwen channel start` still passes the current `AcpBridge` implementation.

## Runtime Modes

The same plugin adapter can be hosted by either channel runtime:

- `qwen channel start [name]` is the standalone ACP-backed service. It still uses `AcpBridge` and remains the stable command for running channels outside a daemon.
- `qwen serve --channel <name>` and repeatable `--channel` flags start experimental daemon-managed channel workers. Named channels are grouped by owning workspace, with one worker per owning runtime. `--channel all` intentionally starts only the primary workspace's configured channels. Workers are owned by `qwen serve`, connect to that daemon through the SDK, and pass adapters a `ChannelAgentBridge` facade backed by `DaemonChannelBridge`.

Daemon-managed channels inherit the daemon's lifecycle and status reporting. They are intentionally out-of-process so adapter or platform SDK failures do not crash the daemon. Every named channel must resolve to exactly one registered, trusted workspace; its worker receives that runtime's canonical cwd and environment overlay. A user/system channel with no cwd is ambiguous when several workspaces are registered, while a channel in a workspace-local settings file belongs to that workspace by default. `--channel all` remains primary-only and cannot be combined with named selections.

## The Plugin Object

Your extension entry point exports a `plugin` conforming to `ChannelPlugin`:

```typescript
import type { ChannelPlugin } from '@qwen-code/channel-base';
import { MyChannel } from './MyChannel.js';

export const plugin: ChannelPlugin = {
  channelType: 'my-platform', // Unique ID, used in settings.json "type" field
  displayName: 'My Platform', // Shown in CLI output
  requiredConfigFields: ['apiKey'], // Validated at startup (beyond standard ChannelConfig)
  createChannel: (name, config, bridge, options) =>
    new MyChannel(name, config, bridge, options),
};
```

## The Channel Adapter

Extend `ChannelBase` and implement three methods:

```typescript
import { ChannelBase } from '@qwen-code/channel-base';
import type {
  ChannelBaseOptions,
  ChannelAgentBridge,
  ChannelConfig,
  Envelope,
  SessionTarget,
} from '@qwen-code/channel-base';

export class MyChannel extends ChannelBase {
  constructor(
    name: string,
    config: ChannelConfig,
    bridge: ChannelAgentBridge,
    options?: ChannelBaseOptions,
  ) {
    super(name, config, bridge, options);
  }

  async connect(): Promise<void> {
    // Connect to your platform, register message handlers
    // When a message arrives:
    const envelope: Envelope = {
      channelName: this.name,
      senderId: '...', // Stable, unique platform user ID
      senderName: '...', // Display name
      chatId: '...', // Chat/conversation ID (distinct for DMs vs groups)
      text: '...', // Message text (strip @mentions)
      isGroup: false, // Accurate — used by GroupGate
      isMentioned: false, // Accurate — used by GroupGate
      isReplyToBot: false, // Accurate — used by GroupGate
    };
    this.handleInbound(envelope);
  }

  async sendMessage(chatId: string, text: string): Promise<void> {
    // Format markdown → platform format, chunk if needed, deliver
  }

  disconnect(): void {
    // Clean up connections
  }
}
```

Most adapters should pass `options` through unchanged. If an adapter creates its own `SessionRouter` and passes that router to `super()`, set `registerBridgeEvents: true` in `ChannelBaseOptions` so `ChannelBase` still receives `toolCall` and `sessionDied` events directly. Leave it unset for routers supplied by the channel gateway.

If your adapter exposes shell-command or BTW side-question behavior, check that the corresponding `bridge.shellCommand` / `bridge.btw` method exists before enabling it. Daemon-managed workers omit those optional methods unless the daemon advertises the matching `session_shell_command` / `session_btw` capability.

## The Envelope

The normalized message object you build from platform data. The boolean flags drive gate logic, so they must be accurate.

| Field            | Type         | Required | Notes                                                                      |
| ---------------- | ------------ | -------- | -------------------------------------------------------------------------- |
| `channelName`    | string       | Yes      | Use `this.name`                                                            |
| `senderId`       | string       | Yes      | Must be stable across messages (used for session routing + access control) |
| `senderName`     | string       | Yes      | Display name                                                               |
| `chatId`         | string       | Yes      | Must distinguish DMs from groups                                           |
| `chatName`       | string       | No       | Group/conversation name when supplied by the platform                      |
| `text`           | string       | Yes      | Strip bot @mentions                                                        |
| `threadId`       | string       | No       | For `sessionScope: "thread"`                                               |
| `messageId`      | string       | No       | Platform message ID — useful for response correlation                      |
| `isGroup`        | boolean      | Yes      | GroupGate relies on this                                                   |
| `isMentioned`    | boolean      | Yes      | GroupGate relies on this                                                   |
| `isReplyToBot`   | boolean      | Yes      | GroupGate relies on this                                                   |
| `referencedText` | string       | No       | Quoted message — prepended as context                                      |
| `imageBase64`    | string       | No       | Base64-encoded image (legacy — prefer `attachments`)                       |
| `imageMimeType`  | string       | No       | e.g., `image/jpeg` (legacy — prefer `attachments`)                         |
| `attachments`    | Attachment[] | No       | Structured media attachments (see below)                                   |

### Attachments

Use the `attachments` array for images, files, audio, and video. `handleInbound()` resolves them automatically: images with base64 `data` are sent to the model as vision input, files with a `filePath` get their path appended to the prompt so the agent can read them.

```typescript
interface Attachment {
  type: 'image' | 'file' | 'audio' | 'video';
  data?: string; // base64-encoded data (images, small files)
  filePath?: string; // absolute path to local file (large files saved to disk)
  mimeType: string; // e.g. 'application/pdf', 'image/jpeg'
  fileName?: string; // original file name from the platform
}
```

Example — handling a file upload in your adapter:

```typescript
import { writeFileSync, mkdirSync, existsSync } from 'node:fs';
import { join } from 'node:path';
import { tmpdir } from 'node:os';

const buf = await downloadFromPlatform(fileId);
const dir = join(tmpdir(), 'channel-files');
if (!existsSync(dir)) mkdirSync(dir, { recursive: true });
const filePath = join(dir, fileName);
writeFileSync(filePath, buf);

envelope.attachments = [
  {
    type: 'file',
    filePath,
    mimeType: 'application/pdf',
    fileName,
  },
];
```

The legacy `imageBase64`/`imageMimeType` fields still work for backwards compatibility but `attachments` is preferred for new code.

## Extension Manifest

Your `qwen-extension.json` declares the channel type. The key must match `channelType` in your plugin object:

```json
{
  "name": "my-channel-extension",
  "version": "1.0.0",
  "channels": {
    "my-platform": {
      "entry": "dist/index.js",
      "displayName": "My Platform Channel"
    }
  }
}
```

## Optional Extension Points

**Custom slash commands** — register in your constructor:

```typescript
this.registerCommand('mycommand', async (envelope, args) => {
  await this.sendMessage(envelope.chatId, 'Response');
  return true; // handled, don't forward to agent
});
```

**Working indicators** — override `onPromptStart()` and `onPromptEnd()` to show platform-specific typing indicators. These hooks fire only when a prompt actually begins processing — not for buffered messages (collect mode) or gated/blocked messages:

```typescript
protected override onPromptStart(chatId: string, sessionId: string, messageId?: string): void {
  this.platformClient.sendTyping(chatId); // your platform API
}

protected override onPromptEnd(chatId: string, sessionId: string, messageId?: string): void {
  this.platformClient.stopTyping(chatId);
}
```

**Tool call hooks** — override `onToolCall()` to display agent activity (e.g., "Running shell command...").

**Streaming hooks** — override `onResponseChunk(chatId, chunk, sessionId, segment)` for per-chunk progressive display (e.g., editing a message in-place). Override `onResponseComplete(chatId, fullText, sessionId, segment)` to customize final delivery. In daemon-managed named-task mode, `segment.sourceLabel` is immutable delivery metadata for that segment. Render it once on each independently visible message or card, including a separately visible final response, but do not add it to raw buffers or model text. Clear adapter-owned segment state from `onOutputSegmentEnd()`.

**Named-task attribution** — `sendThreadMessage(chatId, threadId, text, sourceLabel)` receives the same optional plain-text label for one-shot and proactive delivery boundaries. The default implementation handles plain messages. Adapters that override delivery, split messages, emit cards, or provide fallback sends must repeat the label at every independently visible boundary, escape only the label for the target markup dialect, and include its rendered size in platform limits. Run no-reply checks, media-marker projection, audit hashing, transcript persistence, and retry-body capture against the raw response before presentation; if delivery is persisted for restart-safe retry, persist the captured label separately.

Interactive `ChannelUserInputRequestContext` also carries `sourceLabel`. Cards, terminal replacements, and plain fallbacks must retain it without weakening the existing request, session, run, owner, and target checks.

**Proactive delivery** — override `supportsProactiveSend()` to return `true` when the adapter can send without an active inbound request. `ChannelBase` uses this capability for persistent channel loops, webhook tasks, background-agent results, and daemon delivery. The default target policy rejects threaded targets; override the protected target checks only for target shapes your platform can deliver safely:

```typescript
override supportsProactiveSend(): boolean {
  return true;
}

protected override supportsProactiveTarget(target: SessionTarget): boolean {
  return target.threadId === undefined;
}

protected override async pushProactive(
  target: SessionTarget,
  text: string,
  sourceLabel?: string,
): Promise<void> {
  await this.sendThreadMessage(
    target.chatId,
    target.threadId,
    text,
    sourceLabel,
  );
}
```

Use `supportsProactiveDeliveryTarget()` when generic daemon delivery accepts a different target shape, and `supportsProactiveWebhookTarget()` when webhook delivery differs from loops and background results. Keep unsupported targets rejected rather than falling back to another conversation.

**Media** — populate `envelope.attachments` with images/files. See [Attachments](<#attachments>) above.

## Reference Implementations

- **Plugin example** (`packages/channels/plugin-example/`) — minimal WebSocket-based adapter, good starting point
- **Telegram** (`packages/channels/telegram/`) — full-featured: images, files, formatting, typing indicators
- **DingTalk** (`packages/channels/dingtalk/`) — stream-based with rich text handling


</details>
