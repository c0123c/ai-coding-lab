# OpenCode 实验性资源访问策略

配置参考。说明 experimental.policies 如何按资源和供应商匹配、应用规则顺序，并区分资源使用策略与会话工具权限。

- 来源：OpenCode
- 资料类型：实验配置参考
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-05-30](<https://github.com/anomalyco/opencode/commit/e4d3b81b0e64442bedae267a78c374a6d8b0b7e5>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：原文明确标记实验功能，不能与工具 permission 互相替代。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/policies.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 实验性资源访问策略.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Policies
description: Control which configured resources OpenCode may use.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

策略控制 OpenCode 是否可以对指定资源执行操作。此功能是实验性的，并使用 `opencode.json` 中的 `experimental.policies` 数组进行配置。

策略与 [ 权限 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>) 是分开的。权限控制工具在会话期间可以执行的操作，而策略控制 OpenCode 是否可以使用 LLM 提供程序等资源。

---

## 配置

每个策略声明都有三个字段：

- `effect` - `"allow"` 或 `"deny"`。
- `action` - 正在控制的操作。
- `resource` - 语句适用的资源 ID 或通配符模式。

例如，拒绝使用 `openai` 提供程序：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "experimental": {
    "policies": [
      {
        "effect": "deny",
        "action": "provider.use",
        "resource": "openai"
      }
    ]
  }
}
```

被策略拒绝的提供程序不可用于模型选择或模型使用，即使它具有凭据或以其他方式正确配置也是如此。

---

## 可用的政策

OpenCode 目前支持一项政策行动：

|行动|资源|描述|
| -------------- | ----------------------------- | ------------------------------------- |
| `provider.use` |提供商ID，例如`openai`|允许或拒绝使用 LLM 提供商。|

未来可能会增加更多政策行动。

---

## 配套

`resource`字段支持通配符匹配。使用 `*` 匹配零个或多个字符，使用 `?` 匹配 1 个字符。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "experimental": {
    "policies": [
      {
        "effect": "deny",
        "action": "provider.use",
        "resource": "company-*"
      }
    ]
  }
}
```

这会拒绝 `company-us` 和 `company-eu` 等提供商。

---

## 规则顺序

当多个语句匹配时，最后匹配的语句获胜。首先放置广泛的规则，然后再放置更具体的例外情况。

例如，仅允许 Anthropic：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "experimental": {
    "policies": [
      {
        "effect": "deny",
        "action": "provider.use",
        "resource": "*"
      },
      {
        "effect": "allow",
        "action": "provider.use",
        "resource": "anthropic"
      }
    ]
  }
}
```

如果没有策略与提供程序匹配，则默认情况下允许使用提供程序。

可以在全局配置和项目配置中设置策略。如果两个位置的政策与同一提供商匹配，则您的全局政策优先于项目政策。这可以防止存储库重新启用您全局拒绝的提供程序。

---

## 供应商名单

控制提供者访问时，使用策略而不是旧的 `disabled_providers` 和 `enabled_providers` 设置。

替换 `disabled_providers`：

**代码示例：** `opencode.json`

```json
{
  "experimental": {
    "policies": [
      { "effect": "deny", "action": "provider.use", "resource": "openai" },
      { "effect": "deny", "action": "provider.use", "resource": "google" }
    ]
  }
}
```

要替换 `enabled_providers`，请先拒绝所有提供程序，然后允许选定的提供程序：

**代码示例：** `opencode.json`

```json
{
  "experimental": {
    "policies": [
      { "effect": "deny", "action": "provider.use", "resource": "*" },
      { "effect": "allow", "action": "provider.use", "resource": "anthropic" },
      { "effect": "allow", "action": "provider.use", "resource": "openai" }
    ]
  }
}
```


## 原文对照

<details>
<summary>展开完整原文</summary>

Policies control whether OpenCode may perform an action on a named resource. This feature is experimental and is configured with the `experimental.policies` array in `opencode.json`.

Policies are separate from [permissions](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>). Permissions control what tools can do during a session, while policies control whether OpenCode may use a resource such as an LLM provider.

---

## Configuration

Each policy statement has three fields:

- `effect` - Either `"allow"` or `"deny"`.
- `action` - The operation being controlled.
- `resource` - The resource ID or wildcard pattern the statement applies to.

For example, deny use of the `openai` provider:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "experimental": {
    "policies": [
      {
        "effect": "deny",
        "action": "provider.use",
        "resource": "openai"
      }
    ]
  }
}
```

A provider denied by policy is not available for model selection or model use, even if it has credentials or is otherwise configured correctly.

---

## Available Policies

OpenCode currently supports one policy action:

| Action         | Resource                      | Description                           |
| -------------- | ----------------------------- | ------------------------------------- |
| `provider.use` | Provider ID, such as `openai` | Allow or deny use of an LLM provider. |

More policy actions may be added in the future.

---

## Matching

The `resource` field supports wildcard matching. Use `*` to match zero or more characters and `?` to match one character.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "experimental": {
    "policies": [
      {
        "effect": "deny",
        "action": "provider.use",
        "resource": "company-*"
      }
    ]
  }
}
```

This denies providers such as `company-us` and `company-eu`.

---

## Rule Order

When multiple statements match, the last matching statement wins. Put broad rules first, then more specific exceptions after them.

For example, allow only Anthropic:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "experimental": {
    "policies": [
      {
        "effect": "deny",
        "action": "provider.use",
        "resource": "*"
      },
      {
        "effect": "allow",
        "action": "provider.use",
        "resource": "anthropic"
      }
    ]
  }
}
```

If no policy matches a provider, provider use is allowed by default.

Policies may be set in both your global config and project config. If policies from both locations match the same provider, your global policy takes priority over the project policy. This prevents a repository from re-enabling a provider that you deny globally.

---

## Provider Lists

Use policies instead of the older `disabled_providers` and `enabled_providers` settings when controlling provider access.

To replace `disabled_providers`:

**代码示例：** `opencode.json`

```json
{
  "experimental": {
    "policies": [
      { "effect": "deny", "action": "provider.use", "resource": "openai" },
      { "effect": "deny", "action": "provider.use", "resource": "google" }
    ]
  }
}
```

To replace `enabled_providers`, deny all providers first and allow the selected providers after it:

**代码示例：** `opencode.json`

```json
{
  "experimental": {
    "policies": [
      { "effect": "deny", "action": "provider.use", "resource": "*" },
      { "effect": "allow", "action": "provider.use", "resource": "anthropic" },
      { "effect": "allow", "action": "provider.use", "resource": "openai" }
    ]
  }
}
```


</details>
