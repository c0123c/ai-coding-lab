# Opus 4.5 迁移操作技能

列出指定旧模型到 Opus 4.5 的模型字符串、API 参数及提示调整步骤；适用于文档指定的历史模型版本。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-24](<https://github.com/anthropics/claude-code/commit/a194a9d41b7384879fe88368d67367d7ae1a59e7>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/claude-opus-4-5-migration/skills/claude-opus-4-5-migration/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Opus 4.5 迁移操作技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: claude-opus-4-5-migration
description: Migrate prompts and code from Claude Sonnet 4.0, Sonnet 4.5, or Opus 4.1 to Opus 4.5. Use when the user wants to update their codebase, prompts, or API calls to use Opus 4.5. Handles model string updates and prompt adjustments for known Opus 4.5 behavioral differences. Does NOT migrate Haiku 4.5.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Opus 4.5 迁移指南

从 Sonnet 4.0、Sonnet 4.5 或 Opus 4.1 一次性迁移到 Opus 4.5。

## 迁移工作流程

1. 在代码库中搜索模型字符串和 API 调用
2. 将模型字符串更新为 Opus 4.5（请参阅下面特定于平台的字符串）
3. 删除不支持的测试标头
4. 将工作量参数集添加到`"high"`（参见`references/effort.md`）
5. 总结所做的所有更改
6. 告诉用户：“如果您在使用 Opus 4.5 时遇到任何问题，请告诉我，我可以帮助调整您的提示。”

## 模型字符串更新

确定代码库使用的平台，然后相应地替换模型字符串。

### 不支持的 Beta 标头

删除 `context-1m-2025-08-07` beta 标头（如果存在）——Opus 4.5 尚不支持它。发表评论指出这一点：

```python
# Note: 1M context beta (context-1m-2025-08-07) not yet supported with Opus 4.5
```

### 目标模型字符串（Opus 4.5）

|平台|Opus 4.5 模型弦|
|----------|----------------------|
| Anthropic API (1P) | `claude-opus-4-5-20251101` |
|AWS 基岩| `anthropic.claude-opus-4-5-20251101-v1:0` |
|Google 顶点 AI| `claude-opus-4-5@20251101` |
|Azure AI 铸造厂| `claude-opus-4-5-20251101` |

### 要替换的源模型字符串

|源模型| Anthropic API (1P) |AWS 基岩|Google 顶点 AI|
|--------------|-------------------|-------------|------------------|
|十四行诗 4.0| `claude-sonnet-4-20250514` | `anthropic.claude-sonnet-4-20250514-v1:0` | `claude-sonnet-4@20250514` |
|十四行诗 4.5| `claude-sonnet-4-5-20250929` | `anthropic.claude-sonnet-4-5-20250929-v1:0` | `claude-sonnet-4-5@20250929` |
|作品 4.1| `claude-opus-4-1-20250422` | `anthropic.claude-opus-4-1-20250422-v1:0` | `claude-opus-4-1@20250422` |

**执行NOT迁移**：任何俳句模型（例如，`claude-haiku-4-5-20251001`）。

## 及时调整

Opus 4.5 与以前的模型存在已知的行为差异。 **仅当用户明确请求或报告特定问题时才应用这些修复。** 默认情况下，仅更新模型字符串。

**集成指南**：添加片段时，不要只是将它们附加到提示中。深思熟虑地整合它们：
- 使用 XML 标签（例如，`<code_guidelines>`、`<tool_usage>`）来组织添加内容
- 匹配现有提示的风格和结构
- 将片段放置在逻辑位置（例如，靠近其他编码指令的编码指南）
- 如果提示已使用 XML 标签，请在适当的现有标签中添加新内容或创建一致的新标签

### 1。工具过度触发

Opus 4.5 对系统提示的响应更加灵敏。在以前的模型上防止触发不足的攻击性语言现在可能会导致过度触发。

**适用于**：用户报告工具调用过于频繁或不必要。

**查找并软化**：
- `CRITICAL:` → 去除或软化
- `You MUST...` → `You should...`
- `ALWAYS do X` → `Do X`
- `NEVER skip...` → `Don't skip...`
- `REQUIRED` → 去除或软化

仅适用于工具触发指令。不要理会强调的其他用途。

### 2。过度工程预防

Opus 4.5 倾向于创建额外的文件、添加不必要的抽象或构建未请求的灵活性。

**适用于**：用户报告不需要的文件、过度抽象或未请求的功能。添加 `references/prompt-snippets.md` 中的片段。

### 3。代码探索

Opus 4.5 在探索代码方面可能过于保守，在不读取文件的情况下提出解决方案。

**应用如果**：用户报告建议修复的模型而不检查相关代码。添加 `references/prompt-snippets.md` 中的片段。

### 4。前端设计

**适用于**：用户请求改进前端设计质量或报告外观通用的输出。

添加 `references/prompt-snippets.md` 中的前端美学片段。

### 5。思维敏感度

当扩展思维未启用（默认）时，Opus 4.5 对“思考”一词及其变体特别敏感。仅当 API 请求包含 `thinking` 参数时，才会启用扩展思维。

**应用如果**：用户报告与“思考”相关的问题，而未启用扩展思考（请求中没有`thinking`参数）。

用“考虑”、“相信”或“评估”等替代词代替“思考”。

## 参考

有关要添加的每个片段的全文，请参阅 `references/prompt-snippets.md`。

请参阅 `references/effort.md` 配置工作量参数（仅当用户请求时）。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Opus 4.5 Migration Guide

One-shot migration from Sonnet 4.0, Sonnet 4.5, or Opus 4.1 to Opus 4.5.

## Migration Workflow

1. Search codebase for model strings and API calls
2. Update model strings to Opus 4.5 (see platform-specific strings below)
3. Remove unsupported beta headers
4. Add effort parameter set to `"high"` (see `references/effort.md`)
5. Summarize all changes made
6. Tell the user: "If you encounter any issues with Opus 4.5, let me know and I can help adjust your prompts."

## Model String Updates

Identify which platform the codebase uses, then replace model strings accordingly.

### Unsupported Beta Headers

Remove the `context-1m-2025-08-07` beta header if present—it is not yet supported with Opus 4.5. Leave a comment noting this:

```python
# Note: 1M context beta (context-1m-2025-08-07) not yet supported with Opus 4.5
```

### Target Model Strings (Opus 4.5)

| Platform | Opus 4.5 Model String |
|----------|----------------------|
| Anthropic API (1P) | `claude-opus-4-5-20251101` |
| AWS Bedrock | `anthropic.claude-opus-4-5-20251101-v1:0` |
| Google Vertex AI | `claude-opus-4-5@20251101` |
| Azure AI Foundry | `claude-opus-4-5-20251101` |

### Source Model Strings to Replace

| Source Model | Anthropic API (1P) | AWS Bedrock | Google Vertex AI |
|--------------|-------------------|-------------|------------------|
| Sonnet 4.0 | `claude-sonnet-4-20250514` | `anthropic.claude-sonnet-4-20250514-v1:0` | `claude-sonnet-4@20250514` |
| Sonnet 4.5 | `claude-sonnet-4-5-20250929` | `anthropic.claude-sonnet-4-5-20250929-v1:0` | `claude-sonnet-4-5@20250929` |
| Opus 4.1 | `claude-opus-4-1-20250422` | `anthropic.claude-opus-4-1-20250422-v1:0` | `claude-opus-4-1@20250422` |

**Do NOT migrate**: Any Haiku models (e.g., `claude-haiku-4-5-20251001`).

## Prompt Adjustments

Opus 4.5 has known behavioral differences from previous models. **Only apply these fixes if the user explicitly requests them or reports a specific issue.** By default, just update model strings.

**Integration guidelines**: When adding snippets, don't just append them to prompts. Integrate them thoughtfully:
- Use XML tags (e.g., `<code_guidelines>`, `<tool_usage>`) to organize additions
- Match the style and structure of the existing prompt
- Place snippets in logical locations (e.g., coding guidelines near other coding instructions)
- If the prompt already uses XML tags, add new content within appropriate existing tags or create consistent new ones

### 1. Tool Overtriggering

Opus 4.5 is more responsive to system prompts. Aggressive language that prevented undertriggering on previous models may now cause overtriggering.

**Apply if**: User reports tools being called too frequently or unnecessarily.

**Find and soften**:
- `CRITICAL:` → remove or soften
- `You MUST...` → `You should...`
- `ALWAYS do X` → `Do X`
- `NEVER skip...` → `Don't skip...`
- `REQUIRED` → remove or soften

Only apply to tool-triggering instructions. Leave other uses of emphasis alone.

### 2. Over-Engineering Prevention

Opus 4.5 tends to create extra files, add unnecessary abstractions, or build unrequested flexibility.

**Apply if**: User reports unwanted files, excessive abstraction, or unrequested features. Add the snippet from `references/prompt-snippets.md`.

### 3. Code Exploration

Opus 4.5 can be overly conservative about exploring code, proposing solutions without reading files.

**Apply if**: User reports the model proposing fixes without inspecting relevant code. Add the snippet from `references/prompt-snippets.md`.

### 4. Frontend Design

**Apply if**: User requests improved frontend design quality or reports generic-looking outputs.

Add the frontend aesthetics snippet from `references/prompt-snippets.md`.

### 5. Thinking Sensitivity

When extended thinking is not enabled (the default), Opus 4.5 is particularly sensitive to the word "think" and its variants. Extended thinking is enabled only if the API request contains a `thinking` parameter.

**Apply if**: User reports issues related to "thinking" while extended thinking is not enabled (no `thinking` parameter in request).

Replace "think" with alternatives like "consider," "believe," or "evaluate."

## Reference

See `references/prompt-snippets.md` for the full text of each snippet to add.

See `references/effort.md` for configuring the effort parameter (only if user requests it).


</details>
