# Codex 技能的 openai.yaml 字段参考

提供产品专用配置完整样例，解释界面、依赖和策略字段及格式限制；属于技能元数据实现参考。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-02-01](<https://github.com/openai/skills/commit/94fa19526eea7676eb5ecde1b9d611d9889aa624>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.system/skill-creator/references/openai_yaml.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Codex 技能的 openai.yaml 字段参考.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# openai.yaml 字段（完整示例+描述）

`agents/openai.yaml` 是一个扩展的、特定于产品的配置，旨在供 machine/harness 读取，而不是 agent。其他特定于产品的配置也可以位于 `agents/` 文件夹中。

## 完整示例

```yaml
interface:
  display_name: "Optional user-facing name"
  short_description: "Optional user-facing description"
  icon_small: "./assets/small-400px.png"
  icon_large: "./assets/large-logo.svg"
  brand_color: "#3B82F6"
  default_prompt: "Optional surrounding prompt to use the skill with"

dependencies:
  tools:
    - type: "mcp"
      value: "github"
      description: "GitHub MCP server"
      transport: "streamable_http"
      url: "https://api.githubcopilot.com/mcp/"
```

## 字段描述和约束

顶层约束：

- 引用所有字符串值。
- 保持键不带引号。
- 对于 `interface.default_prompt`：根据技能生成有用的简短（通常是 1 句子）示例启动提示。它必须明确提及技能为 `$skill-name`（例如，“使用 $skill-name-here 起草一份简明的每周状态更新。”）。

- `interface.display_name`：UI技能列表和筹码中显示的面向人的标题。
- `interface.short_description`：面向人的短UI简介（25–64字符）用于快速扫描。
- `interface.icon_small`：小图标资源的路径（相对于技能目录）。默认为`./assets/`并将图标放置在技能的`assets/`文件夹中。
- `interface.icon_large`：更大徽标资源的路径（相对于技能目录）。默认为`./assets/`并将图标放置在技能的`assets/`文件夹中。
- `interface.brand_color`：用于 UI 强调符号（例如徽章）的十六进制颜色。
- `interface.default_prompt`：调用技能时插入的默认提示片段。
- `dependencies.tools[].type`：依赖类别。目前仅支持`mcp`。
- `dependencies.tools[].value`：工具或依赖项的标识符。
- `dependencies.tools[].description`：人类可读的依赖性解释。
- `dependencies.tools[].transport`：`type`为`mcp`时的连接类型。
- `dependencies.tools[].url`：当`type`为`mcp`时，MCP服务器URL。


## 原文对照

<details>
<summary>展开完整原文</summary>

# openai.yaml fields (full example + descriptions)

`agents/openai.yaml` is an extended, product-specific config intended for the machine/harness to read, not the agent. Other product-specific config can also live in the `agents/` folder.

## Full example

```yaml
interface:
  display_name: "Optional user-facing name"
  short_description: "Optional user-facing description"
  icon_small: "./assets/small-400px.png"
  icon_large: "./assets/large-logo.svg"
  brand_color: "#3B82F6"
  default_prompt: "Optional surrounding prompt to use the skill with"

dependencies:
  tools:
    - type: "mcp"
      value: "github"
      description: "GitHub MCP server"
      transport: "streamable_http"
      url: "https://api.githubcopilot.com/mcp/"
```

## Field descriptions and constraints

Top-level constraints:

- Quote all string values.
- Keep keys unquoted.
- For `interface.default_prompt`: generate a helpful, short (typically 1 sentence) example starting prompt based on the skill. It must explicitly mention the skill as `$skill-name` (e.g., "Use $skill-name-here to draft a concise weekly status update.").

- `interface.display_name`: Human-facing title shown in UI skill lists and chips.
- `interface.short_description`: Human-facing short UI blurb (25–64 chars) for quick scanning.
- `interface.icon_small`: Path to a small icon asset (relative to skill dir). Default to `./assets/` and place icons in the skill's `assets/` folder.
- `interface.icon_large`: Path to a larger logo asset (relative to skill dir). Default to `./assets/` and place icons in the skill's `assets/` folder.
- `interface.brand_color`: Hex color used for UI accents (e.g., badges).
- `interface.default_prompt`: Default prompt snippet inserted when invoking the skill.
- `dependencies.tools[].type`: Dependency category. Only `mcp` is supported for now.
- `dependencies.tools[].value`: Identifier of the tool or dependency.
- `dependencies.tools[].description`: Human-readable explanation of the dependency.
- `dependencies.tools[].transport`: Connection type when `type` is `mcp`.
- `dependencies.tools[].url`: MCP server URL when `type` is `mcp`.


</details>
