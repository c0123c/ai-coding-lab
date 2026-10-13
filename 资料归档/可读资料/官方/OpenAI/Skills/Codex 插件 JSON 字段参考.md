# Codex 插件 JSON 字段参考

解释插件标识、版本、接口、路径及市场条目格式，提供样例和生成规则，供检查插件配置使用。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-04-18](<https://github.com/openai/skills/commit/e940b8a86138adf03972802b990a1dfc57fcbf09>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.system/plugin-creator/references/plugin-json-spec.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Codex 插件 JSON 字段参考.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 插件 JSON 示例规范

```json
{
  "name": "plugin-name",
  "version": "1.2.0",
  "description": "Brief plugin description",
  "author": {
    "name": "Author Name",
    "email": "author@example.com",
    "url": "https://github.com/author"
  },
  "homepage": "https://docs.example.com/plugin",
  "repository": "https://github.com/author/plugin",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"],
  "skills": "./skills/",
  "hooks": "./hooks.json",
  "mcpServers": "./.mcp.json",
  "apps": "./.app.json",
  "interface": {
    "displayName": "Plugin Display Name",
    "shortDescription": "Short description for subtitle",
    "longDescription": "Long description for details page",
    "developerName": "OpenAI",
    "category": "Productivity",
    "capabilities": ["Interactive", "Write"],
    "websiteURL": "https://openai.com/",
    "privacyPolicyURL": "https://openai.com/policies/row-privacy-policy/",
    "termsOfServiceURL": "https://openai.com/policies/row-terms-of-use/",
    "defaultPrompt": [
      "Summarize my inbox and draft replies for me.",
      "Find open bugs and turn them into Linear tickets.",
      "Review today's meetings and flag scheduling gaps."
    ],
    "brandColor": "#3B82F6",
    "composerIcon": "./assets/icon.png",
    "logo": "./assets/logo.png",
    "screenshots": [
      "./assets/screenshot1.png",
      "./assets/screenshot2.png",
      "./assets/screenshot3.png"
    ]
  }
}
```

## 现场指导

### 顶级字段

- `name` (`string`)：插件标识符（短横线大小写，无空格）。如果提供 `plugin.json` 并用作清单名称和组件命名空间，则为必需。
- `version` (`string`)：插件语义版本。
- `description` (`string`)：简短的目的摘要。
- `author` (`object`)：发布者身份。
  - `name` (`string`)：作者或团队名称。
  - `email` (`string`)：联系电子邮件。
  - `url` (`string`)：Author/team 主页或个人资料 URL。
- `homepage` (`string`)：插件使用文档 URL。
- `repository` (`string`)：源代码URL。
- `license` (`string`)：许可证标识符（例如 `MIT`、`Apache-2.0`）。
- `keywords`（`string`的`array`）：Search/discovery标签。
- `skills` (`string`)：技能的相对路径 directories/files.
- `hooks` (`string`)：挂钩配置路径。
- `mcpServers` (`string`): MCP 配置路径。
- `apps` (`string`)：插件集成的应用程序清单路径。
- `interface` (`object`)：Interface/UX 用于插件演示的元数据块。

### `interface` 字段

- `displayName` (`string`)：为插件显示的面向用户的标题。
- `shortDescription` (`string`)：紧凑视图中使用的简短字幕。
- `longDescription` (`string`)：详细信息屏幕上使用的较长说明。
- `developerName` (`string`)：人类可读的发布者名称。
- `category` (`string`)：插件类别桶。
- `capabilities`（`string`的`array`）：来自实现的能力列表。
- `websiteURL` (`string`)：插件的公共网站。
- `privacyPolicyURL` (`string`)：隐私政策 URL。
- `termsOfServiceURL` (`string`)：服务条款 URL。
- `defaultPrompt`（`string` 的 `array`）：composer/UX 上下文中显示的入门提示。
  - 最多包含 3 个字符串。第一个 3 之后的条目将被忽略，并且不会被包含在内。
  - 每个字符串的上限为 128 个字符。较长的条目将被截断。
  - 最好在 50 字符周围有简短的入门提示，这样它们就可以在 UI 中很好地扫描。
- `brandColor` (`string`)：插件卡的主题颜色。
- `composerIcon` (`string`)：图标资源的路径。
- `logo` (`string`)：徽标资源的路径。
- `screenshots`（`string`的`array`）：屏幕截图资源路径列表。
  - 屏幕截图条目必须是 PNG 文件名并存储在 `./assets/` 下。
  - 保持文件路径相对于插件根目录。

### 路径约定和默认值

- 路径值应该是相对的，并以 `./` 开头。
- 在默认组件发现的基础上补充了`skills`、`hooks`和`mcpServers`；它们不会取代默认值。
- 自定义路径值必须遵循插件根约定和 naming/namespacing 规则。
- 这个仓库的脚手架写的是`.codex-plugin/plugin.json`；将其视为该技能生成的明显位置。

# 市场 JSON 示例规格

`marketplace.json` 取决于插件应该存在的位置：

- 回购插件：`<repo-root>/.agents/plugins/marketplace.json`
- 本地插件：`~/.agents/plugins/marketplace.json`

```json
{
  "name": "openai-curated",
  "interface": {
    "displayName": "ChatGPT Official"
  },
  "plugins": [
    {
      "name": "linear",
      "source": {
        "source": "local",
        "path": "./plugins/linear"
      },
      "policy": {
        "installation": "AVAILABLE",
        "authentication": "ON_INSTALL"
      },
      "category": "Productivity"
    }
  ]
}
```

## 市场现场指南

### 顶级字段

- `name` (`string`)：市场标识符或目录名称。
- `interface`（`object`，可选）：市场演示元数据。
- `plugins` (`array`)：有序插件条目。此顺序决定了 Codex 如何呈现插件。

### `interface` 字段

- `displayName`（`string`，可选）：面向用户的市场标题。

### 插件输入字段

- `name` (`string`)：插件标识符。匹配插件文件夹名称和 `plugin.json` `name`。
- `source` (`object`)：插件源描述符。
  - `source` (`string`)：对此存储库工作流程使用 `local`。
  - `path` (`string`)：基于市场根的相对插件路径。
    - 回购插件：`./plugins/<plugin-name>`
    - `~/.agents/plugins/marketplace.json`中的本地插件：`./plugins/<plugin-name>`
  - 相同的相对路径约定用于基于回购的市场和基于本地的市场。
    - 示例：对于 `~/.agents/plugins/marketplace.json`，`./plugins/<plugin-name>` 解析为 `~/plugins/<plugin-name>`。
- `policy` (`object`)：市场政策块。始终包含它。
  - `installation` (`string`)：可用性政策。
    - 允许的值：`NOT_AVAILABLE`、`AVAILABLE`、`INSTALLED_BY_DEFAULT`
    - 新条目的默认值：`AVAILABLE`
  - `authentication` (`string`)：认证计时策略。
    - 允许值：`ON_INSTALL`、`ON_USE`
    - 新条目的默认值：`ON_INSTALL`
  - `products`（`string` 的 `array`，可选）：此插件条目的产品覆盖。除非明确要求产品门控，否则请忽略它。
- `category` (`string`)：显示类别桶。始终包含它。

### 市场生成规则

- `displayName` 属于顶级 `interface` 对象，而不是单个插件条目。
- 从头开始创建新的市场文件时，将 `interface.displayName` 与顶级 `name` 一起播种。
- 在每个生成或更新的插件条目中始终包含 `policy.installation`、`policy.authentication` 和 `category`。
- 将 `policy.products` 视为覆盖并忽略它，除非明确要求。
- 附加新条目，除非用户明确请求重新排序。
- 仅当有意覆盖时才替换同一插件的现有条目。
- 选择与插件目标匹配的市场位置：
  - 回购插件：`<repo-root>/.agents/plugins/marketplace.json`
  - 本地插件：`~/.agents/plugins/marketplace.json`


## 原文对照

<details>
<summary>展开完整原文</summary>

# Plugin JSON sample spec

```json
{
  "name": "plugin-name",
  "version": "1.2.0",
  "description": "Brief plugin description",
  "author": {
    "name": "Author Name",
    "email": "author@example.com",
    "url": "https://github.com/author"
  },
  "homepage": "https://docs.example.com/plugin",
  "repository": "https://github.com/author/plugin",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"],
  "skills": "./skills/",
  "hooks": "./hooks.json",
  "mcpServers": "./.mcp.json",
  "apps": "./.app.json",
  "interface": {
    "displayName": "Plugin Display Name",
    "shortDescription": "Short description for subtitle",
    "longDescription": "Long description for details page",
    "developerName": "OpenAI",
    "category": "Productivity",
    "capabilities": ["Interactive", "Write"],
    "websiteURL": "https://openai.com/",
    "privacyPolicyURL": "https://openai.com/policies/row-privacy-policy/",
    "termsOfServiceURL": "https://openai.com/policies/row-terms-of-use/",
    "defaultPrompt": [
      "Summarize my inbox and draft replies for me.",
      "Find open bugs and turn them into Linear tickets.",
      "Review today's meetings and flag scheduling gaps."
    ],
    "brandColor": "#3B82F6",
    "composerIcon": "./assets/icon.png",
    "logo": "./assets/logo.png",
    "screenshots": [
      "./assets/screenshot1.png",
      "./assets/screenshot2.png",
      "./assets/screenshot3.png"
    ]
  }
}
```

## Field guide

### Top-level fields

- `name` (`string`): Plugin identifier (kebab-case, no spaces). Required if `plugin.json` is provided and used as manifest name and component namespace.
- `version` (`string`): Plugin semantic version.
- `description` (`string`): Short purpose summary.
- `author` (`object`): Publisher identity.
  - `name` (`string`): Author or team name.
  - `email` (`string`): Contact email.
  - `url` (`string`): Author/team homepage or profile URL.
- `homepage` (`string`): Documentation URL for plugin usage.
- `repository` (`string`): Source code URL.
- `license` (`string`): License identifier (for example `MIT`, `Apache-2.0`).
- `keywords` (`array` of `string`): Search/discovery tags.
- `skills` (`string`): Relative path to skill directories/files.
- `hooks` (`string`): Hook config path.
- `mcpServers` (`string`): MCP config path.
- `apps` (`string`): App manifest path for plugin integrations.
- `interface` (`object`): Interface/UX metadata block for plugin presentation.

### `interface` fields

- `displayName` (`string`): User-facing title shown for the plugin.
- `shortDescription` (`string`): Brief subtitle used in compact views.
- `longDescription` (`string`): Longer description used on details screens.
- `developerName` (`string`): Human-readable publisher name.
- `category` (`string`): Plugin category bucket.
- `capabilities` (`array` of `string`): Capability list from implementation.
- `websiteURL` (`string`): Public website for the plugin.
- `privacyPolicyURL` (`string`): Privacy policy URL.
- `termsOfServiceURL` (`string`): Terms of service URL.
- `defaultPrompt` (`array` of `string`): Starter prompts shown in composer/UX context.
  - Include at most 3 strings. Entries after the first 3 are ignored and will not be included.
  - Each string is capped at 128 characters. Longer entries are truncated.
  - Prefer short starter prompts around 50 characters so they scan well in the UI.
- `brandColor` (`string`): Theme color for the plugin card.
- `composerIcon` (`string`): Path to icon asset.
- `logo` (`string`): Path to logo asset.
- `screenshots` (`array` of `string`): List of screenshot asset paths.
  - Screenshot entries must be PNG filenames and stored under `./assets/`.
  - Keep file paths relative to plugin root.

### Path conventions and defaults

- Path values should be relative and begin with `./`.
- `skills`, `hooks`, and `mcpServers` are supplemented on top of default component discovery; they do not replace defaults.
- Custom path values must follow the plugin root convention and naming/namespacing rules.
- This repo’s scaffold writes `.codex-plugin/plugin.json`; treat that as the manifest location this skill generates.

# Marketplace JSON sample spec

`marketplace.json` depends on where the plugin should live:

- Repo plugin: `<repo-root>/.agents/plugins/marketplace.json`
- Local plugin: `~/.agents/plugins/marketplace.json`

```json
{
  "name": "openai-curated",
  "interface": {
    "displayName": "ChatGPT Official"
  },
  "plugins": [
    {
      "name": "linear",
      "source": {
        "source": "local",
        "path": "./plugins/linear"
      },
      "policy": {
        "installation": "AVAILABLE",
        "authentication": "ON_INSTALL"
      },
      "category": "Productivity"
    }
  ]
}
```

## Marketplace field guide

### Top-level fields

- `name` (`string`): Marketplace identifier or catalog name.
- `interface` (`object`, optional): Marketplace presentation metadata.
- `plugins` (`array`): Ordered plugin entries. This order determines how Codex renders plugins.

### `interface` fields

- `displayName` (`string`, optional): User-facing marketplace title.

### Plugin entry fields

- `name` (`string`): Plugin identifier. Match the plugin folder name and `plugin.json` `name`.
- `source` (`object`): Plugin source descriptor.
  - `source` (`string`): Use `local` for this repo workflow.
  - `path` (`string`): Relative plugin path based on the marketplace root.
    - Repo plugin: `./plugins/<plugin-name>`
    - Local plugin in `~/.agents/plugins/marketplace.json`: `./plugins/<plugin-name>`
  - The same relative path convention is used for both repo-rooted and home-rooted marketplaces.
    - Example: with `~/.agents/plugins/marketplace.json`, `./plugins/<plugin-name>` resolves to `~/plugins/<plugin-name>`.
- `policy` (`object`): Marketplace policy block. Always include it.
  - `installation` (`string`): Availability policy.
    - Allowed values: `NOT_AVAILABLE`, `AVAILABLE`, `INSTALLED_BY_DEFAULT`
    - Default for new entries: `AVAILABLE`
  - `authentication` (`string`): Authentication timing policy.
    - Allowed values: `ON_INSTALL`, `ON_USE`
    - Default for new entries: `ON_INSTALL`
  - `products` (`array` of `string`, optional): Product override for this plugin entry. Omit it unless product gating is explicitly requested.
- `category` (`string`): Display category bucket. Always include it.

### Marketplace generation rules

- `displayName` belongs under the top-level `interface` object, not individual plugin entries.
- When creating a new marketplace file from scratch, seed `interface.displayName` alongside top-level `name`.
- Always include `policy.installation`, `policy.authentication`, and `category` on every generated or updated plugin entry.
- Treat `policy.products` as an override and omit it unless explicitly requested.
- Append new entries unless the user explicitly requests reordering.
- Replace an existing entry for the same plugin only when overwrite is intentional.
- Choose marketplace location to match the plugin destination:
  - Repo plugin: `<repo-root>/.agents/plugins/marketplace.json`
  - Local plugin: `~/.agents/plugins/marketplace.json`


</details>
