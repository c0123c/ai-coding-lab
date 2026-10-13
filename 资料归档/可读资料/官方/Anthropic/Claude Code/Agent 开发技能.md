# Agent 开发技能

介绍角色文件、描述、模型、工具和系统提示的配置格式，以及触发示例；属于官方插件中的开发技能实现。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/387dc35db708150c229d7ed2d34342042e417fa0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/plugin-dev/skills/agent-development/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Agent 开发技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: Agent Development
description: This skill should be used when the user asks to "create an agent", "add an agent", "write a subagent", "agent frontmatter", "when to use description", "agent examples", "agent tools", "agent colors", "autonomous agent", or needs guidance on agent structure, system prompts, triggering conditions, or agent development best practices for Claude Code plugins.
version: 0.1.0
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Agent Claude Code 插件开发

## 概述

Agents 是自主子流程，可以独立处理复杂的多步骤任务。了解agent结构、触发条件和系统提示设计可以创建强大的自主能力。

**关键概念：**
- Agents 是 FOR 自主工作，命令是 FOR 用户发起的操作
- 带有 YAML frontmatter 的 Markdown 文件格式
- 通过示例描述字段触发
- 系统提示定义 agent 行为
- 型号及颜色定制

## Agent 文件结构

### 完整格式

```markdown
---
name: agent-identifier
description: Use this agent when [triggering conditions]. Examples:

<example>
Context: [Situation description]
user: "[User request]"
assistant: "[How assistant should respond and use this agent]"
<commentary>
[Why this agent should be triggered]
</commentary>
</example>

<example>
[Additional example...]
</example>

model: inherit
color: blue
tools: ["Read", "Write", "Grep"]
---

You are [agent role description]...

**Your Core Responsibilities:**
1. [Responsibility 1]
2. [Responsibility 2]

**Analysis Process:**
[Step-by-step workflow]

**Output Format:**
[What to return]
```

## 前沿领域

### 姓名（必填）

Agent 用于命名空间和调用的标识符。

**格式：**小写、数字、连字符
**长度：** 3-50字符
**模式：**必须以字母数字开头和结尾

**很好的例子：**
- `code-reviewer`
- `test-generator`
- `api-docs-writer`
- `security-analyzer`

**不良示例：**
- `helper`（太通用）
- `-agent-`（starts/ends 带连字符）
- `my_agent`（不允许使用下划线）
- `ag`（太短，< 3 字符）

### 描述（必填）

定义何时 Claude 应触发此 agent。 **这是最关键的字段。**

**必须包括：**
1. 触发条件（“当...时使用此agent”）
2. 显示用法的多个 `<example>` 块
3. 每个示例中的上下文、用户请求和助理响应
4. `<commentary>`解释为什么agent触发

**格式：**
```
Use this agent when [conditions]. Examples:

<example>
Context: [Scenario description]
user: "[What user says]"
assistant: "[How Claude should respond]"
<commentary>
[Why this agent is appropriate]
</commentary>
</example>

[More examples...]
```

**最佳实践：**
- 包括2-4具体例子
- 显示主动和被动触发
- 涵盖相同意图的不同措辞
- 在评论中解释推理
- 具体说明何时NOT使用agent

### 型号（必填）

agent应该使用哪种型号。

**选项：**
- `inherit` - 使用与父级相同的型号（推荐）
- `sonnet` - Claude 十四行诗（平衡）
- `opus` - Claude Opus（最有能力，最昂贵）
- `haiku` - Claude俳句（快速，便宜）

**建议：** 使用`inherit`，除非agent需要特定模型功能。

### 颜色（必填）

UI 中 agent 的视觉标识符。

**选项：**、`blue`、`cyan`、`green`、`yellow`、`magenta`、`red`

**指南：**
- 在同一插件中为不同的 agents 选择不同的颜色
- 对相似的 agent 类型使用一致的颜色
- Blue/cyan：分析、回顾
- 绿色：以成功为导向的任务
- 黄色：警告，验证
- 红色：关键、安全
- 洋红色：创意、世代

### 工具（可选）

将 agent 限制为特定工具。

**格式：** 工具名称数组

```yaml
tools: ["Read", "Write", "Grep", "Bash"]
```

**默认：**如果省略，agent可以访问所有工具

**最佳实践：**将工具限制在所需的最低限度（最小权限原则）

**常用工具组：**
- 只读分析：`["Read", "Grep", "Glob"]`
- 代码生成：`["Read", "Write", "Grep"]`
- 测试：`["Read", "Bash", "Grep"]`
- 完全访问：省略字段或使用 `["*"]`

## 系统提示设计

Markdown 正文成为 agent 的系统提示符。以第二人称书写，直接寻址agent。

### 结构

**标准模板：**
```markdown
You are [role] specializing in [domain].

**Your Core Responsibilities:**
1. [Primary responsibility]
2. [Secondary responsibility]
3. [Additional responsibilities...]

**Analysis Process:**
1. [Step one]
2. [Step two]
3. [Step three]
[...]

**Quality Standards:**
- [Standard 1]
- [Standard 2]

**Output Format:**
Provide results in this format:
- [What to include]
- [How to structure]

**Edge Cases:**
Handle these situations:
- [Edge case 1]: [How to handle]
- [Edge case 2]: [How to handle]
```

### 最佳实践

✅ **DO:**
- 用第二人称写作（“你是……”、“你将……”）
- 明确职责
- 提供分步流程
- 定义输出格式
- 包括质量标准
- 解决边缘情况
- 保留在 10,000 个字符之下

❌ **DON'T:**
- 以第一人称写作（“我是……”、“我会……”）
- 模糊或笼统
- 省略流程步骤
- 保留输出格式未定义
- 跳过质量指导
- 忽略错误情况

## 创建 Agents

### 方法1：AI-辅助生成

使用此提示模式（摘自Claude Code）：

```
Create an agent configuration based on this request: "[YOUR DESCRIPTION]"

Requirements:
1. Extract core intent and responsibilities
2. Design expert persona for the domain
3. Create comprehensive system prompt with:
   - Clear behavioral boundaries
   - Specific methodologies
   - Edge case handling
   - Output format
4. Create identifier (lowercase, hyphens, 3-50 chars)
5. Write description with triggering conditions
6. Include 2-3 <example> blocks showing when to use

Return JSON with:
{
  "identifier": "agent-name",
  "whenToUse": "Use this agent when... Examples: <example>...</example>",
  "systemPrompt": "You are..."
}
```

然后用frontmatter转换为agent文件格式。

完整模板请参见 `examples/agent-creation-prompt.md`。

### 方法2：手动创建

1. 选择 agent 标识符（3-50 字符、小写字母、连字符）
2. 写出描述并举例
3. 选择型号（通常为`inherit`）
4. 选择用于视觉识别的颜色
5. 定义工具（如果限制访问）
6. 按照上面的结构编写系统提示符
7. 另存为`agents/agent-name.md`

## 验证规则

### 标识符验证

```
✅ Valid: code-reviewer, test-gen, api-analyzer-v2
❌ Invalid: ag (too short), -start (starts with hyphen), my_agent (underscore)
```

**规则：**
- 3-50 字符
- 仅小写字母、数字、连字符
- 必须以字母数字开头和结尾
- 没有下划线、空格或特殊字符

### 描述验证

**长度：** 10-5,000个字符
**必须包含：**触发条件及示例
**最佳：** 200-1,000 字符以及 2-4 示例

### 系统提示验证

**长度：** 20-10,000 个字符
**最佳：** 500-3,000字符
**结构：**明确职责、流程、输出格式

## Agent 组织

### 插件Agents目录

```
plugin-name/
└── agents/
    ├── analyzer.md
    ├── reviewer.md
    └── generator.md
```

`agents/` 中的所有 `.md` 文件都会自动发现。

### 命名空间

Agents 自动命名：
- 单个插件：`agent-name`
- 包含子目录：`plugin:subdir:agent-name`

## 测试Agents

### 测试触发

创建测试场景以正确验证 agent 触发器：

1. 写agent具体触发例子
2. 使用与测试中的示例类似的措辞
3. 检查Claude加载agent
4. 验证 agent 提供预期功能

### 测试系统提示

确保系统提示完成：

1. 给出agent典型任务
2. 检查是否遵循流程步骤
3. 验证输出格式是否正确
4. 测试提示中提到的边缘情况
5. 确认符合质量标准

## 快速参考

### 最小 Agent

```markdown
---
name: simple-agent
description: Use this agent when... Examples: <example>...</example>
model: inherit
color: blue
---

You are an agent that [does X].

Process:
1. [Step 1]
2. [Step 2]

Output: [What to provide]
```

### Frontmatter 字段摘要

|领域|必填|格式|示例|
|-------|----------|--------|---------|
|姓名|是的|小写连字符|代码审查者|
|描述|是的|文字+例子|使用时... <example>...|
|模型|是的| inherit/sonnet/opus/haiku |继承|
|颜色|是的|颜色名称|蓝色|
|工具|否|工具名称数组|[“读取”，“Grep”]|

### 最佳实践

**DO:**
- ✅ 描述中包含2-4具体示例
- ✅ 写出具体的触发条件
- ✅ 除非特殊需要，否则使用 `inherit` 型号
- ✅ 选择合适的工具（最低权限）
- ✅ 编写清晰、结构化的系统提示
- ✅ 彻底测试agent触发

**DON'T:**
- ❌ 使用通用描述而不使用示例
- ❌ 省略触发条件
- ❌ 给所有agents相同的颜色
- ❌ 授予不必要的工具访问权限
- ❌ 写出模糊的系统提示
- ❌ 跳过测试

## 其他资源

### 参考文件

如需详细指导，请参阅：

- **`references/system-prompt-design.md`** - 完整的系统提示模式
- **`references/triggering-examples.md`** - 示例格式和最佳实践
- **`references/agent-creation-system-prompt.md`** - 来自 Claude Code 的确切提示

### 示例文件

`examples/`中的工作示例：

- **`agent-creation-prompt.md`** - AI-辅助agent生成模板
- **`complete-agent-examples.md`** - 不同用例的完整 agent 示例

### 实用脚本

`scripts/`中的开发工具：

- **`validate-agent.sh`** - 验证 agent 文件结构
- **`test-agent-trigger.sh`** - 测试agent是否正确触发

## 实施工作流程

要为插件创建 agent：

1. 定义agent目的和触发条件
2. 选择创建方式（AI-辅助或手动）
3. 创建`agents/agent-name.md`文件
4. 编写包含所有必填字段的 frontmatter
5. 按照最佳实践编写系统提示符
6. 描述中包含2-4触发示例
7. 使用 `scripts/validate-agent.sh` 进行验证
8. 使用真实场景测试触发
9. 插件 README 中的文档 agent

注重明确的触发条件和全面的系统提示自主操作。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Agent Development for Claude Code Plugins

## Overview

Agents are autonomous subprocesses that handle complex, multi-step tasks independently. Understanding agent structure, triggering conditions, and system prompt design enables creating powerful autonomous capabilities.

**Key concepts:**
- Agents are FOR autonomous work, commands are FOR user-initiated actions
- Markdown file format with YAML frontmatter
- Triggering via description field with examples
- System prompt defines agent behavior
- Model and color customization

## Agent File Structure

### Complete Format

```markdown
---
name: agent-identifier
description: Use this agent when [triggering conditions]. Examples:

<example>
Context: [Situation description]
user: "[User request]"
assistant: "[How assistant should respond and use this agent]"
<commentary>
[Why this agent should be triggered]
</commentary>
</example>

<example>
[Additional example...]
</example>

model: inherit
color: blue
tools: ["Read", "Write", "Grep"]
---

You are [agent role description]...

**Your Core Responsibilities:**
1. [Responsibility 1]
2. [Responsibility 2]

**Analysis Process:**
[Step-by-step workflow]

**Output Format:**
[What to return]
```

## Frontmatter Fields

### name (required)

Agent identifier used for namespacing and invocation.

**Format:** lowercase, numbers, hyphens only
**Length:** 3-50 characters
**Pattern:** Must start and end with alphanumeric

**Good examples:**
- `code-reviewer`
- `test-generator`
- `api-docs-writer`
- `security-analyzer`

**Bad examples:**
- `helper` (too generic)
- `-agent-` (starts/ends with hyphen)
- `my_agent` (underscores not allowed)
- `ag` (too short, < 3 chars)

### description (required)

Defines when Claude should trigger this agent. **This is the most critical field.**

**Must include:**
1. Triggering conditions ("Use this agent when...")
2. Multiple `<example>` blocks showing usage
3. Context, user request, and assistant response in each example
4. `<commentary>` explaining why agent triggers

**Format:**
```
Use this agent when [conditions]. Examples:

<example>
Context: [Scenario description]
user: "[What user says]"
assistant: "[How Claude should respond]"
<commentary>
[Why this agent is appropriate]
</commentary>
</example>

[More examples...]
```

**Best practices:**
- Include 2-4 concrete examples
- Show proactive and reactive triggering
- Cover different phrasings of same intent
- Explain reasoning in commentary
- Be specific about when NOT to use the agent

### model (required)

Which model the agent should use.

**Options:**
- `inherit` - Use same model as parent (recommended)
- `sonnet` - Claude Sonnet (balanced)
- `opus` - Claude Opus (most capable, expensive)
- `haiku` - Claude Haiku (fast, cheap)

**Recommendation:** Use `inherit` unless agent needs specific model capabilities.

### color (required)

Visual identifier for agent in UI.

**Options:** `blue`, `cyan`, `green`, `yellow`, `magenta`, `red`

**Guidelines:**
- Choose distinct colors for different agents in same plugin
- Use consistent colors for similar agent types
- Blue/cyan: Analysis, review
- Green: Success-oriented tasks
- Yellow: Caution, validation
- Red: Critical, security
- Magenta: Creative, generation

### tools (optional)

Restrict agent to specific tools.

**Format:** Array of tool names

```yaml
tools: ["Read", "Write", "Grep", "Bash"]
```

**Default:** If omitted, agent has access to all tools

**Best practice:** Limit tools to minimum needed (principle of least privilege)

**Common tool sets:**
- Read-only analysis: `["Read", "Grep", "Glob"]`
- Code generation: `["Read", "Write", "Grep"]`
- Testing: `["Read", "Bash", "Grep"]`
- Full access: Omit field or use `["*"]`

## System Prompt Design

The markdown body becomes the agent's system prompt. Write in second person, addressing the agent directly.

### Structure

**Standard template:**
```markdown
You are [role] specializing in [domain].

**Your Core Responsibilities:**
1. [Primary responsibility]
2. [Secondary responsibility]
3. [Additional responsibilities...]

**Analysis Process:**
1. [Step one]
2. [Step two]
3. [Step three]
[...]

**Quality Standards:**
- [Standard 1]
- [Standard 2]

**Output Format:**
Provide results in this format:
- [What to include]
- [How to structure]

**Edge Cases:**
Handle these situations:
- [Edge case 1]: [How to handle]
- [Edge case 2]: [How to handle]
```

### Best Practices

✅ **DO:**
- Write in second person ("You are...", "You will...")
- Be specific about responsibilities
- Provide step-by-step process
- Define output format
- Include quality standards
- Address edge cases
- Keep under 10,000 characters

❌ **DON'T:**
- Write in first person ("I am...", "I will...")
- Be vague or generic
- Omit process steps
- Leave output format undefined
- Skip quality guidance
- Ignore error cases

## Creating Agents

### Method 1: AI-Assisted Generation

Use this prompt pattern (extracted from Claude Code):

```
Create an agent configuration based on this request: "[YOUR DESCRIPTION]"

Requirements:
1. Extract core intent and responsibilities
2. Design expert persona for the domain
3. Create comprehensive system prompt with:
   - Clear behavioral boundaries
   - Specific methodologies
   - Edge case handling
   - Output format
4. Create identifier (lowercase, hyphens, 3-50 chars)
5. Write description with triggering conditions
6. Include 2-3 <example> blocks showing when to use

Return JSON with:
{
  "identifier": "agent-name",
  "whenToUse": "Use this agent when... Examples: <example>...</example>",
  "systemPrompt": "You are..."
}
```

Then convert to agent file format with frontmatter.

See `examples/agent-creation-prompt.md` for complete template.

### Method 2: Manual Creation

1. Choose agent identifier (3-50 chars, lowercase, hyphens)
2. Write description with examples
3. Select model (usually `inherit`)
4. Choose color for visual identification
5. Define tools (if restricting access)
6. Write system prompt with structure above
7. Save as `agents/agent-name.md`

## Validation Rules

### Identifier Validation

```
✅ Valid: code-reviewer, test-gen, api-analyzer-v2
❌ Invalid: ag (too short), -start (starts with hyphen), my_agent (underscore)
```

**Rules:**
- 3-50 characters
- Lowercase letters, numbers, hyphens only
- Must start and end with alphanumeric
- No underscores, spaces, or special characters

### Description Validation

**Length:** 10-5,000 characters
**Must include:** Triggering conditions and examples
**Best:** 200-1,000 characters with 2-4 examples

### System Prompt Validation

**Length:** 20-10,000 characters
**Best:** 500-3,000 characters
**Structure:** Clear responsibilities, process, output format

## Agent Organization

### Plugin Agents Directory

```
plugin-name/
└── agents/
    ├── analyzer.md
    ├── reviewer.md
    └── generator.md
```

All `.md` files in `agents/` are auto-discovered.

### Namespacing

Agents are namespaced automatically:
- Single plugin: `agent-name`
- With subdirectories: `plugin:subdir:agent-name`

## Testing Agents

### Test Triggering

Create test scenarios to verify agent triggers correctly:

1. Write agent with specific triggering examples
2. Use similar phrasing to examples in test
3. Check Claude loads the agent
4. Verify agent provides expected functionality

### Test System Prompt

Ensure system prompt is complete:

1. Give agent typical task
2. Check it follows process steps
3. Verify output format is correct
4. Test edge cases mentioned in prompt
5. Confirm quality standards are met

## Quick Reference

### Minimal Agent

```markdown
---
name: simple-agent
description: Use this agent when... Examples: <example>...</example>
model: inherit
color: blue
---

You are an agent that [does X].

Process:
1. [Step 1]
2. [Step 2]

Output: [What to provide]
```

### Frontmatter Fields Summary

| Field | Required | Format | Example |
|-------|----------|--------|---------|
| name | Yes | lowercase-hyphens | code-reviewer |
| description | Yes | Text + examples | Use when... <example>... |
| model | Yes | inherit/sonnet/opus/haiku | inherit |
| color | Yes | Color name | blue |
| tools | No | Array of tool names | ["Read", "Grep"] |

### Best Practices

**DO:**
- ✅ Include 2-4 concrete examples in description
- ✅ Write specific triggering conditions
- ✅ Use `inherit` for model unless specific need
- ✅ Choose appropriate tools (least privilege)
- ✅ Write clear, structured system prompts
- ✅ Test agent triggering thoroughly

**DON'T:**
- ❌ Use generic descriptions without examples
- ❌ Omit triggering conditions
- ❌ Give all agents same color
- ❌ Grant unnecessary tool access
- ❌ Write vague system prompts
- ❌ Skip testing

## Additional Resources

### Reference Files

For detailed guidance, consult:

- **`references/system-prompt-design.md`** - Complete system prompt patterns
- **`references/triggering-examples.md`** - Example formats and best practices
- **`references/agent-creation-system-prompt.md`** - The exact prompt from Claude Code

### Example Files

Working examples in `examples/`:

- **`agent-creation-prompt.md`** - AI-assisted agent generation template
- **`complete-agent-examples.md`** - Full agent examples for different use cases

### Utility Scripts

Development tools in `scripts/`:

- **`validate-agent.sh`** - Validate agent file structure
- **`test-agent-trigger.sh`** - Test if agent triggers correctly

## Implementation Workflow

To create an agent for a plugin:

1. Define agent purpose and triggering conditions
2. Choose creation method (AI-assisted or manual)
3. Create `agents/agent-name.md` file
4. Write frontmatter with all required fields
5. Write system prompt following best practices
6. Include 2-4 triggering examples in description
7. Validate with `scripts/validate-agent.sh`
8. Test triggering with real scenarios
9. Document agent in plugin README

Focus on clear triggering conditions and comprehensive system prompts for autonomous operation.


</details>
