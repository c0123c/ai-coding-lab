# Codex 插件创建技能

说明生成插件目录、必需清单、可选组件和市场记录，提供验证步骤；属于插件脚手架实现资料。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-04-18](<https://github.com/openai/skills/commit/e940b8a86138adf03972802b990a1dfc57fcbf09>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.system/plugin-creator/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Codex 插件创建技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: plugin-creator
description: Create and scaffold plugin directories for Codex with a required `.codex-plugin/plugin.json`, optional plugin folders/files, and baseline placeholders you can edit before publishing or testing. Use when Codex needs to create a new local plugin, add optional plugin structure, or generate or update repo-root `.agents/plugins/marketplace.json` entries for plugin ordering and availability metadata.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 插件创建者

## 快速入门

1. 运行脚手架脚本：

```bash
  # Plugin names are normalized to lower-case hyphen-case and must be <= 64 chars.
  # The generated folder and plugin.json name are always the same.
# Run from repo root (or replace .agents/... with the absolute path to this SKILL).
# By default creates in <repo_root>/plugins/<plugin-name>.
python3 .agents/skills/plugin-creator/scripts/create_basic_plugin.py <plugin-name>
```

2. 打开 `<plugin-path>/.codex-plugin/plugin.json` 并替换 `[TODO: ...]` 占位符。

3. 当插件应出现在 Codex UI 排序中时，生成或更新存储库市场条目：

```bash
# marketplace.json always lives at <repo-root>/.agents/plugins/marketplace.json
python3 .agents/skills/plugin-creator/scripts/create_basic_plugin.py my-plugin --with-marketplace
```

对于本地本地插件，将 `<home>` 视为根并使用：

```bash
python3 .agents/skills/plugin-creator/scripts/create_basic_plugin.py my-plugin \
  --path ~/plugins \
  --marketplace-path ~/.agents/plugins/marketplace.json \
  --with-marketplace
```

4. Generate/adjust 根据需要可选的配套文件夹：

```bash
python3 .agents/skills/plugin-creator/scripts/create_basic_plugin.py my-plugin --path <parent-plugin-directory> \
  --with-skills --with-hooks --with-scripts --with-assets --with-mcp --with-apps --with-marketplace
```

`<parent-plugin-directory>` 是将创建插件文件夹 `<plugin-name>` 的目录（例如 `~/code/plugins`）。

## 这项技能创造了什么

- 如果用户没有明确插件位置，请在生成市场条目之前询问他们是否需要存储库本地插件或家庭本地插件。
- 在 `/<parent-plugin-directory>/<plugin-name>/` 创建插件根目录。
- 始终创建 `/<parent-plugin-directory>/<plugin-name>/.codex-plugin/plugin.json`。
- 使用完整的架构形状、占位符值和完整的 `interface` 部分填充清单。
- 设置 `--with-marketplace` 时创建或更新 `<repo-root>/.agents/plugins/marketplace.json`。
  - 如果市场文件尚不存在，请在添加第一个插件条目之前播种顶级 `name` 加上 `interface.displayName` 占位符。
- `<plugin-name>` 使用技能创建者命名规则进行规范化：
  - `My Plugin` → `my-plugin`
  - `My--Plugin` → `my-plugin`
  - 下划线、空格和标点符号转换为 `-`
  - 结果是小写连字符分隔，连续连字符折叠
- 支持可选创建：
  - `skills/`
  - `hooks/`
  - `scripts/`
  - `assets/`
  - `.mcp.json`
  - `.app.json`

## 市场工作流程

- `marketplace.json` 始终居住在 `<repo-root>/.agents/plugins/marketplace.json`。
- 对于本地本地插件，请使用与 `<home>` 作为根的相同约定：
`~/.agents/plugins/marketplace.json` 加 `./plugins/<plugin-name>`。
- 市场根元数据支持顶级 `name` 和可选 `interface.displayName`。
- 将 `plugins[]` 中的插件顺序视为 Codex 中的渲染顺序。附加新条目，除非用户明确要求对列表重新排序。
- `displayName` 属于市场 `interface` 对象，而不是单个 `plugins[]` 条目。
- 每个生成的市场条目必须包含以下所有内容：
  - `policy.installation`
  - `policy.authentication`
  - `category`
- 默认新条目为：
  - `policy.installation: "AVAILABLE"`
  - `policy.authentication: "ON_INSTALL"`
- 仅当用户明确指定另一个允许的值时才覆盖默认值。
- 允许的 `policy.installation` 值：
  - `NOT_AVAILABLE`
  - `AVAILABLE`
  - `INSTALLED_BY_DEFAULT`
- 允许的 `policy.authentication` 值：
  - `ON_INSTALL`
  - `ON_USE`
- 将 `policy.products` 视为覆盖。除非用户明确请求产品门控，否则请忽略它。
- 生成的插件条目形状为：

```json
{
  "name": "plugin-name",
  "source": {
    "source": "local",
    "path": "./plugins/plugin-name"
  },
  "policy": {
    "installation": "AVAILABLE",
    "authentication": "ON_INSTALL"
  },
  "category": "Productivity"
}
```

- 仅当有意替换相同插件名称的现有市场条目时才使用 `--force`。
- 如果 `<repo-root>/.agents/plugins/marketplace.json` 尚不存在，请使用顶级 `"name"`、包含 `"displayName"` 的 `"interface"` 对象和 `plugins` 数组创建它，然后添加新条目。

- 对于全新的市场文件，根对象应如下所示：

```json
{
  "name": "[TODO: marketplace-name]",
  "interface": {
    "displayName": "[TODO: Marketplace Display Name]"
  },
  "plugins": [
    {
      "name": "plugin-name",
      "source": {
        "source": "local",
        "path": "./plugins/plugin-name"
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

## 所需行为

- 外部文件夹名称和 `plugin.json` `"name"` 始终是相同的规范化插件名称。
- 请勿拆除所需结构；保持 `.codex-plugin/plugin.json` 存在。
- 将显式值保留为占位符，直到人工或后续步骤明确填充它们为止。
- 如果在现有插件路径内创建文件，仅当有意覆盖时才使用 `--force`。
- 保留任何现有市场 `interface.displayName`。
- 生成市场条目时，请始终写入 `policy.installation`、`policy.authentication` 和 `category`，即使它们的值为默认值。
- 仅当用户明确要求覆盖时才添加 `policy.products`。
- 将相对于仓库根的市场 `source.path` 保持为 `./plugins/<plugin-name>`。

## 参考准确的规格样本

对于插件清单和市场条目的确切规范示例 JSON，请使用：

- `references/plugin-json-spec.md`

## 验证

编辑`SKILL.md`后，运行：

```bash
python3 <path-to-skill-creator>/scripts/quick_validate.py .agents/skills/plugin-creator
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Plugin Creator

## Quick Start

1. Run the scaffold script:

```bash
  # Plugin names are normalized to lower-case hyphen-case and must be <= 64 chars.
  # The generated folder and plugin.json name are always the same.
# Run from repo root (or replace .agents/... with the absolute path to this SKILL).
# By default creates in <repo_root>/plugins/<plugin-name>.
python3 .agents/skills/plugin-creator/scripts/create_basic_plugin.py <plugin-name>
```

2. Open `<plugin-path>/.codex-plugin/plugin.json` and replace `[TODO: ...]` placeholders.

3. Generate or update the repo marketplace entry when the plugin should appear in Codex UI ordering:

```bash
# marketplace.json always lives at <repo-root>/.agents/plugins/marketplace.json
python3 .agents/skills/plugin-creator/scripts/create_basic_plugin.py my-plugin --with-marketplace
```

For a home-local plugin, treat `<home>` as the root and use:

```bash
python3 .agents/skills/plugin-creator/scripts/create_basic_plugin.py my-plugin \
  --path ~/plugins \
  --marketplace-path ~/.agents/plugins/marketplace.json \
  --with-marketplace
```

4. Generate/adjust optional companion folders as needed:

```bash
python3 .agents/skills/plugin-creator/scripts/create_basic_plugin.py my-plugin --path <parent-plugin-directory> \
  --with-skills --with-hooks --with-scripts --with-assets --with-mcp --with-apps --with-marketplace
```

`<parent-plugin-directory>` is the directory where the plugin folder `<plugin-name>` will be created (for example `~/code/plugins`).

## What this skill creates

- If the user has not made the plugin location explicit, ask whether they want a repo-local plugin or a home-local plugin before generating marketplace entries.
- Creates plugin root at `/<parent-plugin-directory>/<plugin-name>/`.
- Always creates `/<parent-plugin-directory>/<plugin-name>/.codex-plugin/plugin.json`.
- Fills the manifest with the full schema shape, placeholder values, and the complete `interface` section.
- Creates or updates `<repo-root>/.agents/plugins/marketplace.json` when `--with-marketplace` is set.
  - If the marketplace file does not exist yet, seed top-level `name` plus `interface.displayName` placeholders before adding the first plugin entry.
- `<plugin-name>` is normalized using skill-creator naming rules:
  - `My Plugin` → `my-plugin`
  - `My--Plugin` → `my-plugin`
  - underscores, spaces, and punctuation are converted to `-`
  - result is lower-case hyphen-delimited with consecutive hyphens collapsed
- Supports optional creation of:
  - `skills/`
  - `hooks/`
  - `scripts/`
  - `assets/`
  - `.mcp.json`
  - `.app.json`

## Marketplace workflow

- `marketplace.json` always lives at `<repo-root>/.agents/plugins/marketplace.json`.
- For a home-local plugin, use the same convention with `<home>` as the root:
  `~/.agents/plugins/marketplace.json` plus `./plugins/<plugin-name>`.
- Marketplace root metadata supports top-level `name` plus optional `interface.displayName`.
- Treat plugin order in `plugins[]` as render order in Codex. Append new entries unless a user explicitly asks to reorder the list.
- `displayName` belongs inside the marketplace `interface` object, not individual `plugins[]` entries.
- Each generated marketplace entry must include all of:
  - `policy.installation`
  - `policy.authentication`
  - `category`
- Default new entries to:
  - `policy.installation: "AVAILABLE"`
  - `policy.authentication: "ON_INSTALL"`
- Override defaults only when the user explicitly specifies another allowed value.
- Allowed `policy.installation` values:
  - `NOT_AVAILABLE`
  - `AVAILABLE`
  - `INSTALLED_BY_DEFAULT`
- Allowed `policy.authentication` values:
  - `ON_INSTALL`
  - `ON_USE`
- Treat `policy.products` as an override. Omit it unless the user explicitly requests product gating.
- The generated plugin entry shape is:

```json
{
  "name": "plugin-name",
  "source": {
    "source": "local",
    "path": "./plugins/plugin-name"
  },
  "policy": {
    "installation": "AVAILABLE",
    "authentication": "ON_INSTALL"
  },
  "category": "Productivity"
}
```

- Use `--force` only when intentionally replacing an existing marketplace entry for the same plugin name.
- If `<repo-root>/.agents/plugins/marketplace.json` does not exist yet, create it with top-level `"name"`, an `"interface"` object containing `"displayName"`, and a `plugins` array, then add the new entry.

- For a brand-new marketplace file, the root object should look like:

```json
{
  "name": "[TODO: marketplace-name]",
  "interface": {
    "displayName": "[TODO: Marketplace Display Name]"
  },
  "plugins": [
    {
      "name": "plugin-name",
      "source": {
        "source": "local",
        "path": "./plugins/plugin-name"
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

## Required behavior

- Outer folder name and `plugin.json` `"name"` are always the same normalized plugin name.
- Do not remove required structure; keep `.codex-plugin/plugin.json` present.
- Keep manifest values as placeholders until a human or follow-up step explicitly fills them.
- If creating files inside an existing plugin path, use `--force` only when overwrite is intentional.
- Preserve any existing marketplace `interface.displayName`.
- When generating marketplace entries, always write `policy.installation`, `policy.authentication`, and `category` even if their values are defaults.
- Add `policy.products` only when the user explicitly asks for that override.
- Keep marketplace `source.path` relative to repo root as `./plugins/<plugin-name>`.

## Reference to exact spec sample

For the exact canonical sample JSON for both plugin manifests and marketplace entries, use:

- `references/plugin-json-spec.md`

## Validation

After editing `SKILL.md`, run:

```bash
python3 <path-to-skill-creator>/scripts/quick_validate.py .agents/skills/plugin-creator
```


</details>
