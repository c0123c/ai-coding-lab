# OpenSpec 多语言工件配置

说明初始化和配置文件中的语言设置，并给出葡萄牙语、西班牙语、简体中文、日语等语言的配置示例。

- 来源：OpenSpec
- 资料类型：多语言配置指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/multi-language.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/multi-language.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 多语言指南

配置 OpenSpec 以生成英语以外的语言的工件。

## 快速设置

对于新项目，在初始化期间设置语言：

```bash
openspec init --language "Portuguese (pt-BR)"
```

这会将语言指令写入`openspec/config.yaml`。如果项目
已有配置，请直接编辑其`context`字段，以便保留现有项目
指导。

您还可以手动配置相同的行为：

添加语言指令到您的`openspec/config.yaml`：

```yaml
schema: spec-driven

context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.
  Keep OpenSpec structural headings and SHALL/MUST keywords in English.

  # Your other project context below...
  Tech stack: TypeScript, React, Node.js
```

就是这样。所有生成的工件现在都将是葡萄牙语。

OpenSpec 的文档结构和规范性 `SHALL`/`MUST` 关键字保留为 
 英语，因为验证依赖于它们。周围的要求和
场景散文可以使用您选择的语言。

## 语言示例

### 葡萄牙语（巴西）

```yaml
context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.
```

### 西班牙语

```yaml
context: |
  Idioma: Español
  Todos los artefactos deben escribirse en español.
```

### 中文（简体）

```yaml
context: |
  语言：中文（简体）
  所有产出物必须用简体中文撰写。
```

### 日语

```yaml
context: |
  言語：日本語
  すべての成果物は日本語で作成してください。
```

### 法语

```yaml
context: |
  Langue : Français
  Tous les artefacts doivent être rédigés en français.
```

### 德语

```yaml
context: |
  Sprache: Deutsch
  Alle Artefakte müssen auf Deutsch verfasst werden.
```

## 温馨提示

### 处理技术术语

决定如何处理技术术语：

```yaml
context: |
  Language: Japanese
  Write in Japanese, but:
  - Keep technical terms like "API", "REST", "GraphQL" in English
  - Code examples and file paths remain in English
```

### 与其他上下文结合

语言设置与您的其他项目上下文一起工作：

```yaml
schema: spec-driven

context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.

  Tech stack: TypeScript, React 18, Node.js 20
  Database: PostgreSQL with Prisma ORM
```

## 验证

要验证您的语言配置是否正常工作：

```bash
# Check the instructions - should show your language context
openspec instructions proposal --change my-change

# Output will include your language context
```

## 相关文档

- [定制指南](<OpenSpec 项目配置与工作流定制.md>) - 项目配置选项
- [工作流程指南](<OpenSpec 快速与扩展工作流.md>) - 完整的工作流程文档


## 原文对照

<details>
<summary>展开完整原文</summary>

# Multi-Language Guide

Configure OpenSpec to generate artifacts in languages other than English.

## Quick Setup

For a new project, set the language during initialization:

```bash
openspec init --language "Portuguese (pt-BR)"
```

This writes the language instruction to `openspec/config.yaml`. If the project
already has a config, edit its `context` field directly so existing project
guidance is preserved.

You can also configure the same behavior manually:

Add a language instruction to your `openspec/config.yaml`:

```yaml
schema: spec-driven

context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.
  Keep OpenSpec structural headings and SHALL/MUST keywords in English.

  # Your other project context below...
  Tech stack: TypeScript, React, Node.js
```

That's it. All generated artifacts will now be in Portuguese.

OpenSpec's document structure and normative `SHALL`/`MUST` keywords remain in
English because validation relies on them. The surrounding requirement and
scenario prose can use your selected language.

## Language Examples

### Portuguese (Brazil)

```yaml
context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.
```

### Spanish

```yaml
context: |
  Idioma: Español
  Todos los artefactos deben escribirse en español.
```

### Chinese (Simplified)

```yaml
context: |
  语言：中文（简体）
  所有产出物必须用简体中文撰写。
```

### Japanese

```yaml
context: |
  言語：日本語
  すべての成果物は日本語で作成してください。
```

### French

```yaml
context: |
  Langue : Français
  Tous les artefacts doivent être rédigés en français.
```

### German

```yaml
context: |
  Sprache: Deutsch
  Alle Artefakte müssen auf Deutsch verfasst werden.
```

## Tips

### Handle Technical Terms

Decide how to handle technical terminology:

```yaml
context: |
  Language: Japanese
  Write in Japanese, but:
  - Keep technical terms like "API", "REST", "GraphQL" in English
  - Code examples and file paths remain in English
```

### Combine with Other Context

Language settings work alongside your other project context:

```yaml
schema: spec-driven

context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.

  Tech stack: TypeScript, React 18, Node.js 20
  Database: PostgreSQL with Prisma ORM
```

## Verification

To verify your language config is working:

```bash
# Check the instructions - should show your language context
openspec instructions proposal --change my-change

# Output will include your language context
```

## Related Documentation

- [Customization Guide](<OpenSpec 项目配置与工作流定制.md>) - Project configuration options
- [Workflows Guide](<OpenSpec 快速与扩展工作流.md>) - Full workflow documentation


</details>
