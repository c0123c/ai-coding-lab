# Opus 4.5 提示片段

针对工具过度触发、过度设计、代码探索、前端和推理问题提供调整片段；文档将使用限定为明确需求或问题。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-24](<https://github.com/anthropics/claude-code/commit/a194a9d41b7384879fe88368d67367d7ae1a59e7>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/claude-opus-4-5-migration/skills/claude-opus-4-5-migration/references/prompt-snippets.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Opus 4.5 提示片段.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Opus 的提示片段 4.5

仅当用户明确请求或报告特定问题时才应用这些片段。默认情况下，迁移应该只更新模型字符串。

## 1。工具过度触发

**问题**：旨在减少以前型号上触发不足的提示可能会导致 Opus 4.5 过度触发。

**何时添加**：用户报告工具调用过于频繁或不必要。

**解决方案**：用正常措辞替换攻击性语言。

|之前|之后|
|--------|-------|
| `CRITICAL: You MUST use this tool when...` | `Use this tool when...` |
| `ALWAYS call the search function before...` | `Call the search function before...` |
| `You are REQUIRED to...` | `You should...` |
| `NEVER skip this step` | `Don't skip this step` |

## 2。过度工程预防

**问题**：Opus 4.5可能会创建额外的文件，添加不必要的抽象，或构建未经请求的灵活性。

**何时添加**：用户报告不需要的文件、过度抽象或未请求的功能。

**添加到系统提示符的代码段**：

```
- Avoid over-engineering. Only make changes that are directly requested or clearly necessary. Keep solutions simple and focused.
- Don't add features, refactor code, or make "improvements" beyond what was asked. A bug fix doesn't need surrounding code cleaned up. A simple feature doesn't need extra configurability.
- Don't add error handling, fallbacks, or validation for scenarios that can't happen. Trust internal code and framework guarantees. Only validate at system boundaries (user input, external APIs). Don't use backwards-compatibility shims when you can just change the code.
- Don't create helpers, utilities, or abstractions for one-time operations. Don't design for hypothetical future requirements. The right amount of complexity is the minimum needed for the current task. Reuse existing abstractions where possible and follow the DRY principle.
```

## 3。代码探索

**问题**：Opus 4.5可能会在不阅读代码的情况下提出解决方案或对未读文件做出假设。

**何时添加**：用户报告建议修复的模型而不检查相关代码。

**添加到系统提示符的代码段**：

```
ALWAYS read and understand relevant files before proposing code edits. Do not speculate about code you have not inspected. If the user references a specific file/path, you MUST open and inspect it before explaining or proposing fixes. Be rigorous and persistent in searching code for key facts. Thoroughly review the style, conventions, and abstractions of the codebase before implementing new features or abstractions.
```

## 4。前端设计质量

**问题**：默认前端输出可能看起来很通用（“AI slop”美学）。

**何时添加**：用户请求改进前端设计质量或报告外观通用的输出。

**添加到系统提示符的代码段**：

```xml
<frontend_aesthetics>
You tend to converge toward generic, "on distribution" outputs. In frontend design, this creates what users call the "AI slop" aesthetic. Avoid this: make creative, distinctive frontends that surprise and delight.

Focus on:
- Typography: Choose fonts that are beautiful, unique, and interesting. Avoid generic fonts like Arial and Inter; opt instead for distinctive choices that elevate the frontend's aesthetics.
- Color & Theme: Commit to a cohesive aesthetic. Use CSS variables for consistency. Dominant colors with sharp accents outperform timid, evenly-distributed palettes. Draw from IDE themes and cultural aesthetics for inspiration.
- Motion: Use animations for effects and micro-interactions. Prioritize CSS-only solutions for HTML. Use Motion library for React when available. Focus on high-impact moments: one well-orchestrated page load with staggered reveals (animation-delay) creates more delight than scattered micro-interactions.
- Backgrounds: Create atmosphere and depth rather than defaulting to solid colors. Layer CSS gradients, use geometric patterns, or add contextual effects that match the overall aesthetic.

Avoid generic AI-generated aesthetics:
- Overused font families (Inter, Roboto, Arial, system fonts)
- Clichéd color schemes (particularly purple gradients on white backgrounds)
- Predictable layouts and component patterns
- Cookie-cutter design that lacks context-specific character

Interpret creatively and make unexpected choices that feel genuinely designed for the context. Vary between light and dark themes, different fonts, different aesthetics. You still tend to converge on common choices (Space Grotesk, for example) across generations. Avoid this: it is critical that you think outside the box!
</frontend_aesthetics>
```

## 5。思维敏感度

**问题**：当未启用扩展思维（默认）时，Opus 4.5对“思考”一词及其变体特别敏感。

默认情况下不启用扩展思维。仅当 API 请求包含 `thinking` 参数时才启用：
```json
"thinking": {
    "type": "enabled",
    "budget_tokens": 10000
}
```

**何时应用**：用户报告与“思考”相关的问题，而未启用扩展思考（其请求中没有 `thinking` 参数）。

**解决方案**：用替代词替换“认为”。

|之前|之后|
|--------|-------|
| `think about` | `consider` |
| `think through` | `evaluate` |
| `I think` | `I believe` |
| `think carefully` | `consider carefully` |
| `thinking` | `reasoning` / `considering` |

## 使用指南

1. **深思熟虑地集成** - 不要只是附加片段；将它们编织到现有的提示结构中
2. **使用 XML 标签** - 将添加内容包含在与现有提示结构匹配或补充的描述性标签（例如，`<coding_guidelines>`、`<tool_behavior>`）中
3. **匹配提示样式** - 如果提示简洁，则修剪片段；如果冗长，请保留完整的细节
4. **逻辑放置** - 将编码片段放在其他编码指令附近，将工具指南放在工具定义附近等。
5. **保留现有内容** - 插入片段而不删除功能内容
6. **总结更改** - 迁移后，列出所有模型字符串更新并提示所做的修改


## 原文对照

<details>
<summary>展开完整原文</summary>

# Prompt Snippets for Opus 4.5

Only apply these snippets if the user explicitly requests them or reports a specific issue. By default, the migration should only update model strings.

## 1. Tool Overtriggering

**Problem**: Prompts designed to reduce undertriggering on previous models may cause Opus 4.5 to overtrigger.

**When to add**: User reports tools being called too frequently or unnecessarily.

**Solution**: Replace aggressive language with normal phrasing.

| Before | After |
|--------|-------|
| `CRITICAL: You MUST use this tool when...` | `Use this tool when...` |
| `ALWAYS call the search function before...` | `Call the search function before...` |
| `You are REQUIRED to...` | `You should...` |
| `NEVER skip this step` | `Don't skip this step` |

## 2. Over-Engineering Prevention

**Problem**: Opus 4.5 may create extra files, add unnecessary abstractions, or build unrequested flexibility.

**When to add**: User reports unwanted files, excessive abstraction, or unrequested features.

**Snippet to add to system prompt**:

```
- Avoid over-engineering. Only make changes that are directly requested or clearly necessary. Keep solutions simple and focused.
- Don't add features, refactor code, or make "improvements" beyond what was asked. A bug fix doesn't need surrounding code cleaned up. A simple feature doesn't need extra configurability.
- Don't add error handling, fallbacks, or validation for scenarios that can't happen. Trust internal code and framework guarantees. Only validate at system boundaries (user input, external APIs). Don't use backwards-compatibility shims when you can just change the code.
- Don't create helpers, utilities, or abstractions for one-time operations. Don't design for hypothetical future requirements. The right amount of complexity is the minimum needed for the current task. Reuse existing abstractions where possible and follow the DRY principle.
```

## 3. Code Exploration

**Problem**: Opus 4.5 may propose solutions without reading code or make assumptions about unread files.

**When to add**: User reports the model proposing fixes without inspecting relevant code.

**Snippet to add to system prompt**:

```
ALWAYS read and understand relevant files before proposing code edits. Do not speculate about code you have not inspected. If the user references a specific file/path, you MUST open and inspect it before explaining or proposing fixes. Be rigorous and persistent in searching code for key facts. Thoroughly review the style, conventions, and abstractions of the codebase before implementing new features or abstractions.
```

## 4. Frontend Design Quality

**Problem**: Default frontend outputs may look generic ("AI slop" aesthetic).

**When to add**: User requests improved frontend design quality or reports generic-looking outputs.

**Snippet to add to system prompt**:

```xml
<frontend_aesthetics>
You tend to converge toward generic, "on distribution" outputs. In frontend design, this creates what users call the "AI slop" aesthetic. Avoid this: make creative, distinctive frontends that surprise and delight.

Focus on:
- Typography: Choose fonts that are beautiful, unique, and interesting. Avoid generic fonts like Arial and Inter; opt instead for distinctive choices that elevate the frontend's aesthetics.
- Color & Theme: Commit to a cohesive aesthetic. Use CSS variables for consistency. Dominant colors with sharp accents outperform timid, evenly-distributed palettes. Draw from IDE themes and cultural aesthetics for inspiration.
- Motion: Use animations for effects and micro-interactions. Prioritize CSS-only solutions for HTML. Use Motion library for React when available. Focus on high-impact moments: one well-orchestrated page load with staggered reveals (animation-delay) creates more delight than scattered micro-interactions.
- Backgrounds: Create atmosphere and depth rather than defaulting to solid colors. Layer CSS gradients, use geometric patterns, or add contextual effects that match the overall aesthetic.

Avoid generic AI-generated aesthetics:
- Overused font families (Inter, Roboto, Arial, system fonts)
- Clichéd color schemes (particularly purple gradients on white backgrounds)
- Predictable layouts and component patterns
- Cookie-cutter design that lacks context-specific character

Interpret creatively and make unexpected choices that feel genuinely designed for the context. Vary between light and dark themes, different fonts, different aesthetics. You still tend to converge on common choices (Space Grotesk, for example) across generations. Avoid this: it is critical that you think outside the box!
</frontend_aesthetics>
```

## 5. Thinking Sensitivity

**Problem**: When extended thinking is not enabled (the default), Opus 4.5 is particularly sensitive to the word "think" and its variants.

Extended thinking is not enabled by default. It is only enabled if the API request contains a `thinking` parameter:
```json
"thinking": {
    "type": "enabled",
    "budget_tokens": 10000
}
```

**When to apply**: User reports issues related to "thinking" while extended thinking is not enabled (no `thinking` parameter in their request).

**Solution**: Replace "think" with alternative words.

| Before | After |
|--------|-------|
| `think about` | `consider` |
| `think through` | `evaluate` |
| `I think` | `I believe` |
| `think carefully` | `consider carefully` |
| `thinking` | `reasoning` / `considering` |

## Usage Guidelines

1. **Integrate thoughtfully** - Don't just append snippets; weave them into the existing prompt structure
2. **Use XML tags** - Wrap additions in descriptive tags (e.g., `<coding_guidelines>`, `<tool_behavior>`) that match or complement existing prompt structure
3. **Match prompt style** - If the prompt is concise, trim the snippet; if verbose, keep full detail
4. **Place logically** - Put coding snippets near other coding instructions, tool guidance near tool definitions, etc.
5. **Preserve existing content** - Insert snippets without removing functional content
6. **Summarize changes** - After migration, list all model string updates and prompt modifications made


</details>
