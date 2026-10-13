# 命令 YAML 元数据参考

逐项说明 description、工具、模型、参数提示等字段及格式，帮助核对自定义命令的配置。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/387dc35db708150c229d7ed2d34342042e417fa0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/plugin-dev/skills/command-development/references/frontmatter-reference.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/命令 YAML 元数据参考.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 命令前沿参考

斜杠命令中 YAML frontmatter 字段的完整参考。

## 前沿概述

YAML frontmatter 是命令文件开头的可选元数据：

```markdown
---
description: Brief description
allowed-tools: Read, Write
model: sonnet
argument-hint: [arg1] [arg2]
---

Command prompt content here...
```

所有字段都是可选的。命令无需任何前置内容即可工作。

## 现场规格

### 描述

**类型：** 字符串
**必填：** 否
**默认：** 命令提示符第一行
**最大长度：** ~60推荐用于`/help`显示的字符

**用途：** 描述该命令的作用，如 `/help` 输出所示

**示例：**
```yaml
description: Review code for security issues
```
```yaml
description: Deploy to staging environment
```
```yaml
description: Generate API documentation
```

**最佳实践：**
- 保留在 60 字符下以便清晰显示
- 从动词开始（审查、部署、生成）
- 具体说明命令的作用
- 避免多余的“命令”或“斜线命令”

**好：**
- ✅“审查 PR 代码质量和安全性”
- ✅“将应用程序部署到指定环境”
- ✅“生成全面的 API 文档”

**不良：**
- ❌“此命令审查 PR”（不必要的“此命令”）
- ❌“评论”（太模糊）
- ❌“审查拉取请求的代码质量、安全问题和最佳实践的命令”（太长）

### 允许的工具

**类型：** 字符串或字符串数组
**必需：** 否
**默认：** 从对话权限继承

**用途：**限制或指定可以使用哪些工具命令

**格式：**

**单个工具：**
```yaml
allowed-tools: Read
```

**多个工具（逗号分隔）：**
```yaml
allowed-tools: Read, Write, Edit
```

**多个工具（数组）：**
```yaml
allowed-tools:
  - Read
  - Write
  - Bash(git:*)
```

**工具图案：**

**专用工具：**
```yaml
allowed-tools: Read, Grep, Edit
```

**带有命令过滤器的 Bash：**
```yaml
allowed-tools: Bash(git:*)           # Only git commands
allowed-tools: Bash(npm:*)           # Only npm commands
allowed-tools: Bash(docker:*)        # Only docker commands
```

**所有工具（不推荐）：**
```yaml
allowed-tools: "*"
```

**何时使用：**

1. **安全：** 限制命令进行安全操作
   ```yaml
   allowed-tools: Read, Grep  # Read-only command
   ```

2. **清晰度：**文档所需工具
   ```yaml
   allowed-tools: Bash(git:*), Read
   ```

3. **Bash 执行：** 启用 bash 命令输出
   ```yaml
   allowed-tools: Bash(git status:*), Bash(git diff:*)
   ```

**最佳实践：**
- 尽可能限制
- 对 Bash 使用命令过滤器（例如，`git:*` 而不是 `*`）
- 仅在与对话权限不同时指定
- 记录为什么需要特定工具

### 模型

**类型：** 字符串
**必需：** 否
**默认：** 从会话继承
**值：** `sonnet`、`opus`、`haiku`

**用途：**指定哪个Claude模型执行命令

**示例：**
```yaml
model: haiku    # Fast, efficient for simple tasks
```
```yaml
model: sonnet   # Balanced performance (default)
```
```yaml
model: opus     # Maximum capability for complex tasks
```

**何时使用：**

**使用`haiku`：**
- 简单、公式化的命令
- 需要快速执行
- 低复杂度任务
- 频繁调用

```yaml
---
description: Format code file
model: haiku
---
```

**使用`sonnet`：**
- 标准命令（默认）
- 平衡speed/quality
- 最常见的用例

```yaml
---
description: Review code changes
model: sonnet
---
```

**使用`opus`：**
- 复杂分析
- 架构决策
- 深入理解代码
- 关键任务

```yaml
---
description: Analyze system architecture
model: opus
---
```

**最佳实践：**
- 除非有特殊需要，否则省略
- 尽可能使用 `haiku` 来提高速度
- 为真正复杂的任务保留 `opus`
- 使用不同的模型进行测试以找到适当的平衡

### 论证提示

**类型：** 字符串
**必需：** 否
**默认：** 无

**用途：**记录用户的预期参数和自动完成

**格式：**
```yaml
argument-hint: [arg1] [arg2] [optional-arg]
```

**示例：**

**单个参数：**
```yaml
argument-hint: [pr-number]
```

**多个必需参数：**
```yaml
argument-hint: [environment] [version]
```

**可选参数：**
```yaml
argument-hint: [file-path] [options]
```

**描述性名称：**
```yaml
argument-hint: [source-branch] [target-branch] [commit-message]
```

**最佳实践：**
- 对每个参数使用方括号 `[]`
- 使用描述性名称（不是 `arg1`、`arg2`）
- 在描述中注明可选与必需
- 将顺序与命令中的位置参数匹配
- 保持简洁但清晰

**按模式排列的示例：**

**简单命令：**
```yaml
---
description: Fix issue by number
argument-hint: [issue-number]
---

Fix issue #$1...
```

**多参数：**
```yaml
---
description: Deploy to environment
argument-hint: [app-name] [environment] [version]
---

Deploy $1 to $2 using version $3...
```

**有选项：**
```yaml
---
description: Run tests with options
argument-hint: [test-pattern] [options]
---

Run tests matching $1 with options: $2
```

### 禁用模型调用

**类型：** 布尔值
**必需：** 否
**默认值：** false

**用途：**防止SlashCommand工具以编程方式调用命令

**示例：**
```yaml
disable-model-invocation: true
```

**何时使用：**

1. **仅手动命令：**需要用户判断的命令
   ```yaml
   ---
   description: Approve deployment to production
   disable-model-invocation: true
   ---
   ```

2. **破坏性操作：**具有不可逆效果的命令
   ```yaml
   ---
   description: Delete all test data
   disable-model-invocation: true
   ---
   ```

3. **交互式工作流程：**需要用户输入的命令
   ```yaml
   ---
   description: Walk through setup wizard
   disable-model-invocation: true
   ---
   ```

**默认行为（假）：**
- SlashCommand 工具可用的命令
- Claude 可以通过编程方式调用
- 仍然可以手动调用

**为真时：**
- 命令只能由用户键入 `/command` 调用
- 不适用于 SlashCommand 工具
- 敏感操作更安全

**最佳实践：**
- 谨慎使用（限制Claude的自主权）
- 在命令注释中记录原因
- 如果始终手动，请考虑命令是否应该存在

## 完整的例子

### 最小命令

无需前置内容：

```markdown
Review this code for common issues and suggest improvements.
```

### 简单命令

只是描述：

```markdown
---
description: Review code for issues
---

Review this code for common issues and suggest improvements.
```

### 标准命令

描述和工具：

```markdown
---
description: Review Git changes
allowed-tools: Bash(git:*), Read
---

Current changes: !`git diff --name-only`

Review each changed file for:
- Code quality
- Potential bugs
- Best practices
```

### 复杂命令

所有公共字段：

```markdown
---
description: Deploy application to environment
argument-hint: [app-name] [environment] [version]
allowed-tools: Bash(kubectl:*), Bash(helm:*), Read
model: sonnet
---

Deploy $1 to $2 environment using version $3

Pre-deployment checks:
- Verify $2 configuration
- Check cluster status: !`kubectl cluster-info`
- Validate version $3 exists

Proceed with deployment following deployment runbook.
```

### 仅手动命令

限制调用：

```markdown
---
description: Approve production deployment
argument-hint: [deployment-id]
disable-model-invocation: true
allowed-tools: Bash(gh:*)
---

<!--
MANUAL APPROVAL REQUIRED
This command requires human judgment and cannot be automated.
-->

Review deployment $1 for production approval:

Deployment details: !`gh api /deployments/$1`

Verify:
- All tests passed
- Security scan clean
- Stakeholder approval
- Rollback plan ready

Type "APPROVED" to confirm deployment.
```

## 验证

### 常见错误

**无效的 YAML 语法：**
```yaml
---
description: Missing quote
allowed-tools: Read, Write
model: sonnet
---  # ❌ Missing closing quote above
```

**修复：**验证YAML语法

**工具规格不正确：**
```yaml
allowed-tools: Bash  # ❌ Missing command filter
```

**修复：**使用`Bash(git:*)`格式

**无效型号名称：**
```yaml
model: gpt4  # ❌ Not a valid Claude model
```

**修复：**使用`sonnet`、`opus`或`haiku`

### 验证清单

提交命令之前：
- [ ] YAML 语法有效（无错误）
- [ ] 60 字符下的说明
- [ ] allowed-tools 使用正确的格式
- [ ] 型号如果指定则为有效值
- [ ] 参数提示匹配位置参数
- [ ] 适当使用禁用模型调用

## 最佳实践总结

1. **最小启动：**仅在需要时添加 frontmatter
2. **文档参数：** 始终对参数使用参数提示
3. **限制工具：**使用最有效的限制性允许工具
4. **选择正确的型号：**使用俳句提高速度，使用作品提高复杂性
5. **仅手动谨慎：**仅在必要时使用禁用模型调用
6. **清晰的描述：**使命令在`/help`中可发现
7. **彻底测试：**验证 frontmatter 按预期工作


## 原文对照

<details>
<summary>展开完整原文</summary>

# Command Frontmatter Reference

Complete reference for YAML frontmatter fields in slash commands.

## Frontmatter Overview

YAML frontmatter is optional metadata at the start of command files:

```markdown
---
description: Brief description
allowed-tools: Read, Write
model: sonnet
argument-hint: [arg1] [arg2]
---

Command prompt content here...
```

All fields are optional. Commands work without any frontmatter.

## Field Specifications

### description

**Type:** String
**Required:** No
**Default:** First line of command prompt
**Max Length:** ~60 characters recommended for `/help` display

**Purpose:** Describes what the command does, shown in `/help` output

**Examples:**
```yaml
description: Review code for security issues
```
```yaml
description: Deploy to staging environment
```
```yaml
description: Generate API documentation
```

**Best practices:**
- Keep under 60 characters for clean display
- Start with verb (Review, Deploy, Generate)
- Be specific about what command does
- Avoid redundant "command" or "slash command"

**Good:**
- ✅ "Review PR for code quality and security"
- ✅ "Deploy application to specified environment"
- ✅ "Generate comprehensive API documentation"

**Bad:**
- ❌ "This command reviews PRs" (unnecessary "This command")
- ❌ "Review" (too vague)
- ❌ "A command that reviews pull requests for code quality, security issues, and best practices" (too long)

### allowed-tools

**Type:** String or Array of strings
**Required:** No
**Default:** Inherits from conversation permissions

**Purpose:** Restrict or specify which tools command can use

**Formats:**

**Single tool:**
```yaml
allowed-tools: Read
```

**Multiple tools (comma-separated):**
```yaml
allowed-tools: Read, Write, Edit
```

**Multiple tools (array):**
```yaml
allowed-tools:
  - Read
  - Write
  - Bash(git:*)
```

**Tool Patterns:**

**Specific tools:**
```yaml
allowed-tools: Read, Grep, Edit
```

**Bash with command filter:**
```yaml
allowed-tools: Bash(git:*)           # Only git commands
allowed-tools: Bash(npm:*)           # Only npm commands
allowed-tools: Bash(docker:*)        # Only docker commands
```

**All tools (not recommended):**
```yaml
allowed-tools: "*"
```

**When to use:**

1. **Security:** Restrict command to safe operations
   ```yaml
   allowed-tools: Read, Grep  # Read-only command
   ```

2. **Clarity:** Document required tools
   ```yaml
   allowed-tools: Bash(git:*), Read
   ```

3. **Bash execution:** Enable bash command output
   ```yaml
   allowed-tools: Bash(git status:*), Bash(git diff:*)
   ```

**Best practices:**
- Be as restrictive as possible
- Use command filters for Bash (e.g., `git:*` not `*`)
- Only specify when different from conversation permissions
- Document why specific tools are needed

### model

**Type:** String
**Required:** No
**Default:** Inherits from conversation
**Values:** `sonnet`, `opus`, `haiku`

**Purpose:** Specify which Claude model executes the command

**Examples:**
```yaml
model: haiku    # Fast, efficient for simple tasks
```
```yaml
model: sonnet   # Balanced performance (default)
```
```yaml
model: opus     # Maximum capability for complex tasks
```

**When to use:**

**Use `haiku` for:**
- Simple, formulaic commands
- Fast execution needed
- Low complexity tasks
- Frequent invocations

```yaml
---
description: Format code file
model: haiku
---
```

**Use `sonnet` for:**
- Standard commands (default)
- Balanced speed/quality
- Most common use cases

```yaml
---
description: Review code changes
model: sonnet
---
```

**Use `opus` for:**
- Complex analysis
- Architectural decisions
- Deep code understanding
- Critical tasks

```yaml
---
description: Analyze system architecture
model: opus
---
```

**Best practices:**
- Omit unless specific need
- Use `haiku` for speed when possible
- Reserve `opus` for genuinely complex tasks
- Test with different models to find right balance

### argument-hint

**Type:** String
**Required:** No
**Default:** None

**Purpose:** Document expected arguments for users and autocomplete

**Format:**
```yaml
argument-hint: [arg1] [arg2] [optional-arg]
```

**Examples:**

**Single argument:**
```yaml
argument-hint: [pr-number]
```

**Multiple required arguments:**
```yaml
argument-hint: [environment] [version]
```

**Optional arguments:**
```yaml
argument-hint: [file-path] [options]
```

**Descriptive names:**
```yaml
argument-hint: [source-branch] [target-branch] [commit-message]
```

**Best practices:**
- Use square brackets `[]` for each argument
- Use descriptive names (not `arg1`, `arg2`)
- Indicate optional vs required in description
- Match order to positional arguments in command
- Keep concise but clear

**Examples by pattern:**

**Simple command:**
```yaml
---
description: Fix issue by number
argument-hint: [issue-number]
---

Fix issue #$1...
```

**Multi-argument:**
```yaml
---
description: Deploy to environment
argument-hint: [app-name] [environment] [version]
---

Deploy $1 to $2 using version $3...
```

**With options:**
```yaml
---
description: Run tests with options
argument-hint: [test-pattern] [options]
---

Run tests matching $1 with options: $2
```

### disable-model-invocation

**Type:** Boolean
**Required:** No
**Default:** false

**Purpose:** Prevent SlashCommand tool from programmatically invoking command

**Examples:**
```yaml
disable-model-invocation: true
```

**When to use:**

1. **Manual-only commands:** Commands requiring user judgment
   ```yaml
   ---
   description: Approve deployment to production
   disable-model-invocation: true
   ---
   ```

2. **Destructive operations:** Commands with irreversible effects
   ```yaml
   ---
   description: Delete all test data
   disable-model-invocation: true
   ---
   ```

3. **Interactive workflows:** Commands needing user input
   ```yaml
   ---
   description: Walk through setup wizard
   disable-model-invocation: true
   ---
   ```

**Default behavior (false):**
- Command available to SlashCommand tool
- Claude can invoke programmatically
- Still available for manual invocation

**When true:**
- Command only invokable by user typing `/command`
- Not available to SlashCommand tool
- Safer for sensitive operations

**Best practices:**
- Use sparingly (limits Claude's autonomy)
- Document why in command comments
- Consider if command should exist if always manual

## Complete Examples

### Minimal Command

No frontmatter needed:

```markdown
Review this code for common issues and suggest improvements.
```

### Simple Command

Just description:

```markdown
---
description: Review code for issues
---

Review this code for common issues and suggest improvements.
```

### Standard Command

Description and tools:

```markdown
---
description: Review Git changes
allowed-tools: Bash(git:*), Read
---

Current changes: !`git diff --name-only`

Review each changed file for:
- Code quality
- Potential bugs
- Best practices
```

### Complex Command

All common fields:

```markdown
---
description: Deploy application to environment
argument-hint: [app-name] [environment] [version]
allowed-tools: Bash(kubectl:*), Bash(helm:*), Read
model: sonnet
---

Deploy $1 to $2 environment using version $3

Pre-deployment checks:
- Verify $2 configuration
- Check cluster status: !`kubectl cluster-info`
- Validate version $3 exists

Proceed with deployment following deployment runbook.
```

### Manual-Only Command

Restricted invocation:

```markdown
---
description: Approve production deployment
argument-hint: [deployment-id]
disable-model-invocation: true
allowed-tools: Bash(gh:*)
---

<!--
MANUAL APPROVAL REQUIRED
This command requires human judgment and cannot be automated.
-->

Review deployment $1 for production approval:

Deployment details: !`gh api /deployments/$1`

Verify:
- All tests passed
- Security scan clean
- Stakeholder approval
- Rollback plan ready

Type "APPROVED" to confirm deployment.
```

## Validation

### Common Errors

**Invalid YAML syntax:**
```yaml
---
description: Missing quote
allowed-tools: Read, Write
model: sonnet
---  # ❌ Missing closing quote above
```

**Fix:** Validate YAML syntax

**Incorrect tool specification:**
```yaml
allowed-tools: Bash  # ❌ Missing command filter
```

**Fix:** Use `Bash(git:*)` format

**Invalid model name:**
```yaml
model: gpt4  # ❌ Not a valid Claude model
```

**Fix:** Use `sonnet`, `opus`, or `haiku`

### Validation Checklist

Before committing command:
- [ ] YAML syntax valid (no errors)
- [ ] Description under 60 characters
- [ ] allowed-tools uses proper format
- [ ] model is valid value if specified
- [ ] argument-hint matches positional arguments
- [ ] disable-model-invocation used appropriately

## Best Practices Summary

1. **Start minimal:** Add frontmatter only when needed
2. **Document arguments:** Always use argument-hint with arguments
3. **Restrict tools:** Use most restrictive allowed-tools that works
4. **Choose right model:** Use haiku for speed, opus for complexity
5. **Manual-only sparingly:** Only use disable-model-invocation when necessary
6. **Clear descriptions:** Make commands discoverable in `/help`
7. **Test thoroughly:** Verify frontmatter works as expected


</details>
