# OpenCode 会话分享、取消与数据边界

产品指南。说明手动、自动或禁用分享、取消链接、数据保留和企业选项；帮助判断开发对话分享范围，正文指出持链接者可访问共享内容。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-02-25](<https://github.com/anomalyco/opencode/commit/444178e079fb41ba2149a1cdfdd3040593715d70>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：属于开发协作边界参考，公开内容范围需要用户判断。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/share.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 会话分享、取消与数据边界.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Share
description: Share your OpenCode conversations.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 的共享功能允许您创建指向 OpenCode 对话的公共链接，以便您可以与队友协作或从其他人那里获得帮助。

**注释：**

任何知道链接的人都可以公开访问共享对话。

---

## 它是如何运作的

当您分享对话时，OpenCode：

1. 为您的会话创建一个唯一的公共 URL
2. 将您的对话历史记录同步到我们的服务器
3. 通过可共享链接访问对话 - `opncd.ai/s/<share-id>`

---

## 分享

OpenCode 支持三种共享模式来控制对话的共享方式：

---

### 手动（默认）

默认情况下，OpenCode使用手动共享模式。会话不会自动共享，但您可以使用 `/share` 命令手动共享它们：

```
/share
```

这将生成一个唯一的 URL，并将其复制到您的剪贴板。

要在 [ 配置文件 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>) 中显式设置手动模式：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "share": "manual"
}
```

---

### 自动分享

您可以通过在 [ 配置文件 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>) 中将 `share` 选项设置为 `"auto"` 来为所有新对话启用自动共享：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "share": "auto"
}
```

启用自动共享后，每个新对话都会自动共享并生成链接。

---

### 残疾人

您可以通过在 [ 配置文件 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>) 中将 `share` 选项设置为 `"disabled"` 来完全禁用共享：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "share": "disabled"
}
```

要在整个团队中针对给定项目强制执行此操作，请将其添加到项目中的 `opencode.json` 并签入 Git。

---

## 取消共享

要停止共享对话并将其从公共访问中删除：

```
/unshare
```

这将删除共享链接并删除与对话相关的数据。

---

## 隐私

分享对话时需要记住一些事项。

---

### 数据保留

共享对话仍然可以访问，直到您明确取消共享。此
包括：

- 完整的对话历史记录
- 所有消息和回复
- 会话元数据

---

### 建议

- 仅共享不包含敏感信息的对话。
- 分享之前查看对话内容。
- 协作完成后取消共享对话。
- 避免与专有代码或机密数据共享对话。
- 对于敏感项目，完全禁用共享。

---

## 对于企业

对于企业部署，共享功能可以是：

- **完全禁用**以确保安全合规性
- **仅限通过SSO验证的用户**
- **在您自己的基础设施上自行托管**

[详细了解](<https://opencode.ai/docs/enterprise>)有关在您的组织中使用opencode的信息。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode's share feature allows you to create public links to your OpenCode conversations, so you can collaborate with teammates or get help from others.

**注释：**

Shared conversations are publicly accessible to anyone with the link.

---

## How it works

When you share a conversation, OpenCode:

1. Creates a unique public URL for your session
2. Syncs your conversation history to our servers
3. Makes the conversation accessible via the shareable link — `opncd.ai/s/<share-id>`

---

## Sharing

OpenCode supports three sharing modes that control how conversations are shared:

---

### Manual (default)

By default, OpenCode uses manual sharing mode. Sessions are not shared automatically, but you can manually share them using the `/share` command:

```
/share
```

This will generate a unique URL that'll be copied to your clipboard.

To explicitly set manual mode in your [config file](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>):

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "share": "manual"
}
```

---

### Auto-share

You can enable automatic sharing for all new conversations by setting the `share` option to `"auto"` in your [config file](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>):

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "share": "auto"
}
```

With auto-share enabled, every new conversation will automatically be shared and a link will be generated.

---

### Disabled

You can disable sharing entirely by setting the `share` option to `"disabled"` in your [config file](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>):

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "share": "disabled"
}
```

To enforce this across your team for a given project, add it to the `opencode.json` in your project and check into Git.

---

## Un-sharing

To stop sharing a conversation and remove it from public access:

```
/unshare
```

This will remove the share link and delete the data related to the conversation.

---

## Privacy

There are a few things to keep in mind when sharing a conversation.

---

### Data retention

Shared conversations remain accessible until you explicitly unshare them. This
includes:

- Full conversation history
- All messages and responses
- Session metadata

---

### Recommendations

- Only share conversations that don't contain sensitive information.
- Review conversation content before sharing.
- Unshare conversations when collaboration is complete.
- Avoid sharing conversations with proprietary code or confidential data.
- For sensitive projects, disable sharing entirely.

---

## For enterprises

For enterprise deployments, the share feature can be:

- **Disabled** entirely for security compliance
- **Restricted** to users authenticated through SSO only
- **Self-hosted** on your own infrastructure

[Learn more](<https://opencode.ai/docs/enterprise>) about using opencode in your organization.


</details>
