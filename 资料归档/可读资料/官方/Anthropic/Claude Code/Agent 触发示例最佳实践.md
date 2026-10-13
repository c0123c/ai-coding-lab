# Agent 触发示例最佳实践

讲解角色描述中的场景、用户请求和预期响应怎样组成触发示例，用于提高专门角色被正确调用的概率。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/387dc35db708150c229d7ed2d34342042e417fa0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/plugin-dev/skills/agent-development/references/triggering-examples.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Agent 触发示例最佳实践.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Agent 触发示例：最佳实践

在 agent 描述中编写有效 `<example>` 块以实现可靠触发的完整指南。

## 块格式示例

触发示例的标准格式：

```markdown
<example>
Context: [Describe the situation - what led to this interaction]
user: "[Exact user message or request]"
assistant: "[How Claude should respond before triggering]"
<commentary>
[Explanation of why this agent should be triggered in this scenario]
</commentary>
assistant: "[How Claude triggers the agent - usually 'I'll use the [agent-name] agent...']"
</example>
```

## 一个好例子的剖析

### 背景

**用途：**设置场景-用户消息之前发生了什么

**良好的上下文：**
```
Context: User just implemented a new authentication feature
Context: User has created a PR and wants it reviewed
Context: User is debugging a test failure
Context: After writing several functions without documentation
```

**不良上下文：**
```
Context: User needs help (too vague)
Context: Normal usage (not specific)
```

### 用户留言

**用途：**显示应触发agent的确切短语

**良好的用户留言：**
```
user: "I've added the OAuth flow, can you check it?"
user: "Review PR #123"
user: "Why is this test failing?"
user: "Add docs for these functions"
```

**改变措辞：**
包含具有相同意图的不同措辞的多个示例：
```
Example 1: user: "Review my code"
Example 2: user: "Can you check this implementation?"
Example 3: user: "Look over my changes"
```

### 助理响应（触发前）

**目的：** 在启动 agent 之前展示 Claude 所说的内容

**好的回复：**
```
assistant: "I'll analyze your OAuth implementation."
assistant: "Let me review that PR for you."
assistant: "I'll investigate the test failure."
```

**主动示例：**
```
assistant: "Great! Now let me review the code quality."
<commentary>
Code was just written, proactively trigger review agent.
</commentary>
```

### 评论

**目的：**解释原因 - WHY这个agent应该触发

**好评论：**
```
<commentary>
User explicitly requested code review, trigger the code-reviewer agent.
</commentary>

<commentary>
After code implementation, proactively use review agent to check quality.
</commentary>

<commentary>
PR analysis request matches pr-analyzer agent's expertise.
</commentary>
```

**包括决策逻辑：**
```
<commentary>
User wrote tests (Test tool used). The test-analyzer agent should check
test quality and coverage before continuing.
</commentary>
```

### 辅助响应（触发）

**用途：**展示Claude如何调用agent

**标准图案：**
```
assistant: "I'll use the [agent-name] agent to [what it will do]."
```

**示例：**
```
assistant: "I'll use the code-reviewer agent to analyze the changes."
assistant: "Let me use the test-generator agent to create comprehensive tests."
assistant: "I'll use the security-analyzer agent to check for vulnerabilities."
```

## 示例类型

### 类型 1：显式请求

用户直接询问agent的作用：

```markdown
<example>
Context: User explicitly requests security analysis
user: "Check my code for security vulnerabilities"
assistant: "I'll use the security-analyzer agent to perform a thorough security review."
<commentary>
Direct security analysis request triggers the security-analyzer agent.
</commentary>
</example>
```

### 类型2：主动触发

Agent在没有明确请求的情况下进行相关工作后触发：

```markdown
<example>
Context: User just implemented database queries
user: "I've added the user search functionality"
assistant: "Great! Let me review it for security."
<commentary>
Database code written. Proactively trigger security-analyzer to check
for SQL injection and other database security issues.
</commentary>
assistant: "I'll use the security-analyzer agent to check the database code."
</example>
```

### 类型3：隐式请求

用户暗示需要而不直接说明：

```markdown
<example>
Context: User mentions code is hard to understand
user: "This code is confusing and hard to follow"
assistant: "I can help improve the code clarity."
<commentary>
User indicates complexity issues. Trigger code-simplifier agent to refactor
for better readability.
</commentary>
assistant: "I'll use the code-simplifier agent to suggest improvements."
</example>
```

### 类型4：工具使用模式

Agent 根据之前的工具使用情况触发：

```markdown
<example>
Context: User made multiple edits to test files
user: "I've updated all the tests"
assistant: "Great! Let me verify test quality."
<commentary>
Multiple Edit tools used on test files. Proactively trigger test-quality-analyzer
to ensure tests follow best practices.
</commentary>
assistant: "I'll use the test-quality-analyzer agent to review the tests."
</example>
```

## 多例策略

### 涵盖不同的短语

```markdown
<example>
user: "Review my code"
[...]
</example>

<example>
user: "Can you check my implementation?"
[...]
</example>

<example>
user: "Look over these changes"
[...]
</example>
```

### 涵盖主动式和被动式

```markdown
<example>
Context: User explicitly requests review
user: "Review my code for issues"
[...]
</example>

<example>
Context: After user writes code
user: "I've implemented the feature"
assistant: "Great! Now let me review it."
<commentary>
Code written, proactively review.
</commentary>
[...]
</example>
```

### 覆盖边缘情况

```markdown
<example>
Context: Typical usage
user: "Check my PR"
[...]
</example>

<example>
Context: Large PR that needs thorough analysis
user: "This is a big PR, can you do a deep review?"
[...]
<commentary>
Large PR mentioned, use thorough analysis mode.
</commentary>
</example>
```

## 常见错误

### ❌ 缺少上下文

```markdown
<example>
user: "Review my code"
assistant: "I'll use the code-reviewer agent."
</example>
```

**为什么不好：**没有关于导致此请求的原因的上下文。

### ✅ 有上下文

```markdown
<example>
Context: User just finished implementing authentication
user: "Review my code"
assistant: "I'll use the code-reviewer agent to analyze the auth implementation."
<commentary>
Auth code written, review for security and best practices.
</commentary>
</example>
```

### ❌无评论

```markdown
<example>
Context: User requests review
user: "Check my changes"
assistant: "I'll use the reviewer agent."
</example>
```

**为什么不好：**不解释WHY agent触发器。

### ✅ 有评论

```markdown
<example>
Context: User requests review after making changes
user: "Check my changes"
assistant: "I'll use the code-reviewer agent to analyze the modifications."
<commentary>
Code review request triggers agent to check quality, security, and adherence to standards.
</commentary>
</example>
```

### ❌【Agent】直接回复

```markdown
<example>
user: "Review my code"
assistant: "I found the following issues: [lists issues]"
</example>
```

**为什么不好：**显示agent的输出，不触发。

### ✅ 显示触发

```markdown
<example>
user: "Review my code"
assistant: "I'll use the code-reviewer agent to perform the review."
<commentary>
Review request triggers the agent.
</commentary>
</example>
```

## 有多少例子？

### 最小值：2 示例

至少涵盖：
1. 明确要求
2. 一种变化或主动触发

### 推荐：3-4 示例

封面：
1. 明确请求（直接询问）
2. 隐式请求（用户暗示需要）
3. 主动触发（相关工作后）
4. 边缘情况或特定场景

### 最大值：6 示例

超过 6 会使描述过长。专注于最重要的场景。

## 模板库

### 代码审查 Agent

```markdown
<example>
Context: User just implemented a new feature
user: "I've added the user registration feature"
assistant: "Great! Let me review the code quality."
<commentary>
Code written, proactively trigger code-reviewer agent.
</commentary>
assistant: "I'll use the code-reviewer agent to analyze the implementation."
</example>

<example>
Context: User explicitly requests review
user: "Can you review my changes for issues?"
assistant: "I'll use the code-reviewer agent to perform a thorough review."
<commentary>
Explicit review request triggers the agent.
</commentary>
</example>
```

### 测试生成 Agent

```markdown
<example>
Context: User implemented functions without tests
user: "I've added the data processing functions"
assistant: "Great! Let me generate tests for these."
<commentary>
New code without tests, proactively trigger test-generator agent.
</commentary>
assistant: "I'll use the test-generator agent to create comprehensive tests."
</example>

<example>
Context: User explicitly asks for tests
user: "Generate tests for my code"
assistant: "I'll use the test-generator agent to create a complete test suite."
<commentary>
Direct test generation request triggers the agent.
</commentary>
</example>
```

### 文档 Agent

```markdown
<example>
Context: User added new API endpoints
user: "I've implemented the REST API endpoints"
assistant: "I'll document these endpoints."
<commentary>
New API code, proactively generate documentation.
</commentary>
assistant: "I'll use the api-docs-writer agent to create comprehensive documentation."
</example>

<example>
Context: User requests documentation
user: "Write docs for this API"
assistant: "I'll use the api-docs-writer agent to generate clear documentation."
<commentary>
Documentation request triggers the agent.
</commentary>
</example>
```

### 验证Agent

```markdown
<example>
Context: User ready to commit changes
user: "I'm ready to commit this code"
assistant: "Let me validate it first."
<commentary>
Before commit, proactively validate with validation-agent.
</commentary>
assistant: "I'll use the code-validator agent to check for issues."
</example>

<example>
Context: User asks for validation
user: "Validate my implementation"
assistant: "I'll use the code-validator agent to verify correctness."
<commentary>
Explicit validation request triggers the agent.
</commentary>
</example>
```

## 调试触发问题

### Agent 未触发

**检查：**
1. 示例包括用户消息中的相关关键字
2. 上下文与实际使用场景相符
3. 注释清楚地解释了触发逻辑
4. Assistant 在示例中展示了 Agent 工具的使用

**修复：**
添加更多涵盖不同短语的示例。

### Agent 触发过于频繁

**检查：**
1. 示例过于宽泛或笼统
2. 触发条件与其他条件重叠agents
3. 注释不区分何时使用 NOT

**修复：**
使示例更具体，添加反面示例。

### Agent 在错误场景中触发

**检查：**
1. 示例与实际用途不符
2. 评论暗示触发不当

**修复：**
修改示例以仅显示正确的触发场景。

## 最佳实践总结

✅ **DO:**
- 包括2-4具体的、具体的例子
- 显示显式触发和主动触发
- 为每个示例提供清晰的上下文
- 在评论中解释推理
- 改变用户消息措辞
- 使用Agent工具显示Claude

❌ **DON'T:**
- 使用通用、模糊的例子
- 省略上下文或评论
- 仅显示一种触发类型
- 跳过 agent 调用步骤
- 让示例过于相似
- 忘记解释为什么agent触发

## 结论

精心设计的示例对于可靠的 agent 触发至关重要。投入时间创建多样化的具体示例，以清楚地展示何时以及为何应使用 agent。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Agent Triggering Examples: Best Practices

Complete guide to writing effective `<example>` blocks in agent descriptions for reliable triggering.

## Example Block Format

The standard format for triggering examples:

```markdown
<example>
Context: [Describe the situation - what led to this interaction]
user: "[Exact user message or request]"
assistant: "[How Claude should respond before triggering]"
<commentary>
[Explanation of why this agent should be triggered in this scenario]
</commentary>
assistant: "[How Claude triggers the agent - usually 'I'll use the [agent-name] agent...']"
</example>
```

## Anatomy of a Good Example

### Context

**Purpose:** Set the scene - what happened before the user's message

**Good contexts:**
```
Context: User just implemented a new authentication feature
Context: User has created a PR and wants it reviewed
Context: User is debugging a test failure
Context: After writing several functions without documentation
```

**Bad contexts:**
```
Context: User needs help (too vague)
Context: Normal usage (not specific)
```

### User Message

**Purpose:** Show the exact phrasing that should trigger the agent

**Good user messages:**
```
user: "I've added the OAuth flow, can you check it?"
user: "Review PR #123"
user: "Why is this test failing?"
user: "Add docs for these functions"
```

**Vary the phrasing:**
Include multiple examples with different phrasings for the same intent:
```
Example 1: user: "Review my code"
Example 2: user: "Can you check this implementation?"
Example 3: user: "Look over my changes"
```

### Assistant Response (Before Triggering)

**Purpose:** Show what Claude says before launching the agent

**Good responses:**
```
assistant: "I'll analyze your OAuth implementation."
assistant: "Let me review that PR for you."
assistant: "I'll investigate the test failure."
```

**Proactive example:**
```
assistant: "Great! Now let me review the code quality."
<commentary>
Code was just written, proactively trigger review agent.
</commentary>
```

### Commentary

**Purpose:** Explain the reasoning - WHY this agent should trigger

**Good commentary:**
```
<commentary>
User explicitly requested code review, trigger the code-reviewer agent.
</commentary>

<commentary>
After code implementation, proactively use review agent to check quality.
</commentary>

<commentary>
PR analysis request matches pr-analyzer agent's expertise.
</commentary>
```

**Include decision logic:**
```
<commentary>
User wrote tests (Test tool used). The test-analyzer agent should check
test quality and coverage before continuing.
</commentary>
```

### Assistant Response (Triggering)

**Purpose:** Show how Claude invokes the agent

**Standard pattern:**
```
assistant: "I'll use the [agent-name] agent to [what it will do]."
```

**Examples:**
```
assistant: "I'll use the code-reviewer agent to analyze the changes."
assistant: "Let me use the test-generator agent to create comprehensive tests."
assistant: "I'll use the security-analyzer agent to check for vulnerabilities."
```

## Example Types

### Type 1: Explicit Request

User directly asks for what the agent does:

```markdown
<example>
Context: User explicitly requests security analysis
user: "Check my code for security vulnerabilities"
assistant: "I'll use the security-analyzer agent to perform a thorough security review."
<commentary>
Direct security analysis request triggers the security-analyzer agent.
</commentary>
</example>
```

### Type 2: Proactive Triggering

Agent triggers after relevant work without explicit request:

```markdown
<example>
Context: User just implemented database queries
user: "I've added the user search functionality"
assistant: "Great! Let me review it for security."
<commentary>
Database code written. Proactively trigger security-analyzer to check
for SQL injection and other database security issues.
</commentary>
assistant: "I'll use the security-analyzer agent to check the database code."
</example>
```

### Type 3: Implicit Request

User implies need without stating it directly:

```markdown
<example>
Context: User mentions code is hard to understand
user: "This code is confusing and hard to follow"
assistant: "I can help improve the code clarity."
<commentary>
User indicates complexity issues. Trigger code-simplifier agent to refactor
for better readability.
</commentary>
assistant: "I'll use the code-simplifier agent to suggest improvements."
</example>
```

### Type 4: Tool Usage Pattern

Agent triggers based on prior tool usage:

```markdown
<example>
Context: User made multiple edits to test files
user: "I've updated all the tests"
assistant: "Great! Let me verify test quality."
<commentary>
Multiple Edit tools used on test files. Proactively trigger test-quality-analyzer
to ensure tests follow best practices.
</commentary>
assistant: "I'll use the test-quality-analyzer agent to review the tests."
</example>
```

## Multiple Examples Strategy

### Cover Different Phrasings

```markdown
<example>
user: "Review my code"
[...]
</example>

<example>
user: "Can you check my implementation?"
[...]
</example>

<example>
user: "Look over these changes"
[...]
</example>
```

### Cover Proactive and Reactive

```markdown
<example>
Context: User explicitly requests review
user: "Review my code for issues"
[...]
</example>

<example>
Context: After user writes code
user: "I've implemented the feature"
assistant: "Great! Now let me review it."
<commentary>
Code written, proactively review.
</commentary>
[...]
</example>
```

### Cover Edge Cases

```markdown
<example>
Context: Typical usage
user: "Check my PR"
[...]
</example>

<example>
Context: Large PR that needs thorough analysis
user: "This is a big PR, can you do a deep review?"
[...]
<commentary>
Large PR mentioned, use thorough analysis mode.
</commentary>
</example>
```

## Common Mistakes

### ❌ Missing Context

```markdown
<example>
user: "Review my code"
assistant: "I'll use the code-reviewer agent."
</example>
```

**Why bad:** No context about what led to this request.

### ✅ With Context

```markdown
<example>
Context: User just finished implementing authentication
user: "Review my code"
assistant: "I'll use the code-reviewer agent to analyze the auth implementation."
<commentary>
Auth code written, review for security and best practices.
</commentary>
</example>
```

### ❌ No Commentary

```markdown
<example>
Context: User requests review
user: "Check my changes"
assistant: "I'll use the reviewer agent."
</example>
```

**Why bad:** Doesn't explain WHY agent triggers.

### ✅ With Commentary

```markdown
<example>
Context: User requests review after making changes
user: "Check my changes"
assistant: "I'll use the code-reviewer agent to analyze the modifications."
<commentary>
Code review request triggers agent to check quality, security, and adherence to standards.
</commentary>
</example>
```

### ❌ Agent Responds Directly

```markdown
<example>
user: "Review my code"
assistant: "I found the following issues: [lists issues]"
</example>
```

**Why bad:** Shows agent's output, not triggering.

### ✅ Shows Triggering

```markdown
<example>
user: "Review my code"
assistant: "I'll use the code-reviewer agent to perform the review."
<commentary>
Review request triggers the agent.
</commentary>
</example>
```

## How Many Examples?

### Minimum: 2 Examples

Cover at least:
1. Explicit request
2. One variation or proactive trigger

### Recommended: 3-4 Examples

Cover:
1. Explicit request (direct ask)
2. Implicit request (user implies need)
3. Proactive trigger (after relevant work)
4. Edge case or specific scenario

### Maximum: 6 Examples

More than 6 makes description too long. Focus on most important scenarios.

## Template Library

### Code Review Agent

```markdown
<example>
Context: User just implemented a new feature
user: "I've added the user registration feature"
assistant: "Great! Let me review the code quality."
<commentary>
Code written, proactively trigger code-reviewer agent.
</commentary>
assistant: "I'll use the code-reviewer agent to analyze the implementation."
</example>

<example>
Context: User explicitly requests review
user: "Can you review my changes for issues?"
assistant: "I'll use the code-reviewer agent to perform a thorough review."
<commentary>
Explicit review request triggers the agent.
</commentary>
</example>
```

### Test Generation Agent

```markdown
<example>
Context: User implemented functions without tests
user: "I've added the data processing functions"
assistant: "Great! Let me generate tests for these."
<commentary>
New code without tests, proactively trigger test-generator agent.
</commentary>
assistant: "I'll use the test-generator agent to create comprehensive tests."
</example>

<example>
Context: User explicitly asks for tests
user: "Generate tests for my code"
assistant: "I'll use the test-generator agent to create a complete test suite."
<commentary>
Direct test generation request triggers the agent.
</commentary>
</example>
```

### Documentation Agent

```markdown
<example>
Context: User added new API endpoints
user: "I've implemented the REST API endpoints"
assistant: "I'll document these endpoints."
<commentary>
New API code, proactively generate documentation.
</commentary>
assistant: "I'll use the api-docs-writer agent to create comprehensive documentation."
</example>

<example>
Context: User requests documentation
user: "Write docs for this API"
assistant: "I'll use the api-docs-writer agent to generate clear documentation."
<commentary>
Documentation request triggers the agent.
</commentary>
</example>
```

### Validation Agent

```markdown
<example>
Context: User ready to commit changes
user: "I'm ready to commit this code"
assistant: "Let me validate it first."
<commentary>
Before commit, proactively validate with validation-agent.
</commentary>
assistant: "I'll use the code-validator agent to check for issues."
</example>

<example>
Context: User asks for validation
user: "Validate my implementation"
assistant: "I'll use the code-validator agent to verify correctness."
<commentary>
Explicit validation request triggers the agent.
</commentary>
</example>
```

## Debugging Triggering Issues

### Agent Not Triggering

**Check:**
1. Examples include relevant keywords from user message
2. Context matches actual usage scenarios
3. Commentary explains triggering logic clearly
4. Assistant shows use of Agent tool in examples

**Fix:**
Add more examples covering different phrasings.

### Agent Triggers Too Often

**Check:**
1. Examples are too broad or generic
2. Triggering conditions overlap with other agents
3. Commentary doesn't distinguish when NOT to use

**Fix:**
Make examples more specific, add negative examples.

### Agent Triggers in Wrong Scenarios

**Check:**
1. Examples don't match actual intended use
2. Commentary suggests inappropriate triggering

**Fix:**
Revise examples to show only correct triggering scenarios.

## Best Practices Summary

✅ **DO:**
- Include 2-4 concrete, specific examples
- Show both explicit and proactive triggering
- Provide clear context for each example
- Explain reasoning in commentary
- Vary user message phrasing
- Show Claude using Agent tool

❌ **DON'T:**
- Use generic, vague examples
- Omit context or commentary
- Show only one type of triggering
- Skip the agent invocation step
- Make examples too similar
- Forget to explain why agent triggers

## Conclusion

Well-crafted examples are crucial for reliable agent triggering. Invest time in creating diverse, specific examples that clearly demonstrate when and why the agent should be used.


</details>
