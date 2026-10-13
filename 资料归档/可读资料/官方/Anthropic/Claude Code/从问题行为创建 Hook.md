# 从问题行为创建 Hook

展示分析对话或明确约束、向用户确认发现、生成规则并测试的过程，帮助将重复问题转成检查规则。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/59372c0921b0170e81f9c63777962d02347411d5>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/hookify/commands/hookify.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/从问题行为创建 Hook.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: Create hooks to prevent unwanted behaviors from conversation analysis or explicit instructions
argument-hint: Optional specific behavior to address
allowed-tools: ["Read", "Write", "AskUserQuestion", "Task", "Grep", "TodoWrite", "Skill"]
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Hookify - 根据不需要的行为创建 Hooks

**FIRST：使用 Skill 工具加载 hookify:writing-rules 技能 ** 以了解规则文件格式和语法。

通过分析对话或明确的用户指令，创建挂钩规则以防止出现问题的行为。

## 你的任务

您将帮助用户创建 hookify 规则以防止不良行为。请按照下列步骤操作：

### 步骤 1：收集行为信息

**如果提供$ARGUMENTS：**
- 用户已给出具体说明：`$ARGUMENTS`
- 仍然分析最近的对话（最后的 10-15 用户消息）以获取更多上下文
- 寻找发生的行为的例子

**如果$ARGUMENTS为空：**
- 启动对话分析器 agent 以查找有问题的行为
- Agent 将扫描用户提示以获取沮丧信号
- Agent 将返回结构化结果

**分析对话：**
使用任务工具启动对话分析器agent：
```
{
  "subagent_type": "general-purpose",
  "description": "Analyze conversation for unwanted behaviors",
  "prompt": "You are analyzing a Claude Code conversation to find behaviors the user wants to prevent.

Read user messages in the current conversation and identify:
1. Explicit requests to avoid something (\"don't do X\", \"stop doing Y\")
2. Corrections or reversions (user fixing Claude's actions)
3. Frustrated reactions (\"why did you do X?\", \"I didn't ask for that\")
4. Repeated issues (same problem multiple times)

For each issue found, extract:
- What tool was used (Bash, Edit, Write, etc.)
- Specific pattern or command
- Why it was problematic
- User's stated reason

Return findings as a structured list with:
- category: Type of issue
- tool: Which tool was involved
- pattern: Regex or literal pattern to match
- context: What happened
- severity: high/medium/low

Focus on the most recent issues (last 20-30 messages). Don't go back further unless explicitly asked."
}
```

### 步骤 2：向用户展示调查结果

收集行为后（从参数或 agent），使用 AskUserQuestion 向用户呈现：

**问题1：要hook哪些行为？**
- 标题：“创建规则”
- multiSelect：正确
- 选项：列出每个检测到的行为（最大 4）
  - 标签：简短描述（例如“Block rm -rf”）
  - 描述：为什么会出现问题

**问题2：对于每个选定的行为，询问操作：**
- “这应该阻止操作还是只是发出警告？”
- 选项：
  - “只是警告”（操作：警告 - 显示消息但允许）
  - “阻止操作”（动作：阻止 - 阻止执行）

**问题3：询问示例模式：**
- “什么模式应该触发这个规则？”
- 显示检测到的模式
- 允许用户完善或添加更多

### 步骤3：生成规则文件

对于每个已确认的行为，创建一个 `.claude/hookify.{rule-name}.local.md` 文件：

**规则命名约定：**
- 使用烤肉串盒
- 具有描述性：`block-dangerous-rm`、`warn-console-log`、`require-tests-before-stop`
- 以动作动词开头：阻止、警告、阻止、要求

**文件格式：**
```markdown
---
name: {rule-name}
enabled: true
event: {bash|file|stop|prompt|all}
pattern: {regex pattern}
action: {warn|block}
---

{Message to show Claude when rule triggers}
```

**动作值：**
- `warn`：显示消息但允许操作（默认）
- `block`：阻止操作或停止会话

**对于更复杂的规则（多个条件）：**
```markdown
---
name: {rule-name}
enabled: true
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.env$
  - field: new_text
    operator: contains
    pattern: API_KEY
---

{Warning message}
```

### 步骤4：创建文件并确认

**IMPORTANT**：规则文件必须创建在当前工作目录的`.claude/`文件夹，NOT插件目录中。

使用当前工作目录（Claude Code 的启动位置）作为基本路径。

1. 检查当前工作目录中是否存在`.claude/`目录
   - 如果没有，请先使用以下命令创建它：`mkdir -p .claude`

2. 使用写入工具创建每个`.claude/hookify.{name}.local.md`文件
   - 使用当前工作目录的相对路径：`.claude/hookify.{name}.local.md`
   - 该路径应解析为项目的 .claude 目录，而不是插件的

3. 向用户显示创建的内容：
   ```
   Created 3 hookify rules:
   - .claude/hookify.dangerous-rm.local.md
   - .claude/hookify.console-log.local.md
   - .claude/hookify.sensitive-files.local.md

   These rules will trigger on:
   - dangerous-rm: Bash commands matching "rm -rf"
   - console-log: Edits adding console.log statements
   - sensitive-files: Edits to .env or credentials files
   ```

4. 通过列出文件来验证文件是否创建在正确的位置

5. 通知用户：**“规则立即生效 - 无需重新启动！”**

hookify 挂钩已加载，并将在下次使用工具时读取您的新规则。

## 事件类型参考

- **bash**：匹配 Bash 工具命令
- **文件**：匹配编辑、写入、MultiEdit工具
- **stop**：当agent想要停止时匹配（用于完成检查）
- **prompt**：用户提交提示时匹配
- **all**：匹配所有事件

## 模式写作技巧

**重击模式：**
- 匹配危险命令：`rm\s+-rf|chmod\s+777|dd\s+if=`
- 匹配具体工具：`npm\s+install\s+|pip\s+install`

**文件模式：**
- 匹配代码模式：`console\.log\(|eval\(|innerHTML\s*=`
- 匹配文件路径：`\.env$|\.git/|node_modules/`

**停止模式：**
- 检查是否有遗漏的步骤：（检查成绩单或完成标准）

## 示例工作流程

**用户说**：“/hookify在没有先询问我的情况下不要使用 rm -rf”

**您的回复**：
1. 分析：用户想要阻止 rm -rf 命令
2. 问：“我应该阻止这个命令还是只是警告你？”
3. 用户选择：“只是警告”
4. 创建`.claude/hookify.dangerous-rm.local.md`：
   ```markdown
   ---
   name: warn-dangerous-rm
   enabled: true
   event: bash
   pattern: rm\s+-rf
   ---

   ⚠️ **Dangerous rm command detected**

   You requested to be warned before using rm -rf.
   Please verify the path is correct.
   ```
5. 确认：“已创建 hookify 规则。它立即生效 - 尝试触发它！”

## 重要提示

- **无需重启**：规则在下次使用工具时立即生效
- **文件位置**：在项目的`.claude/`目录（当前工作目录）中创建文件，NOT插件的.claude/
- **正则表达式语法**：使用Python正则表达式语法（原始字符串，不需要在YAML中转义）
- **操作类型**：规则可以`warn`（默认）或`block`操作
- **测试**：创建规则后立即测试规则

## 故障排除

**如果规则文件创建失败：**
1. 使用 pwd 检查当前工作目录
2. 确保 `.claude/` 目录存在（如果需要，使用 mkdir 创建）
3. 如果需要，请使用绝对路径：`{cwd}/.claude/hookify.{name}.local.md`
4. 验证文件是使用 Glob 或 ls 创建的

**如果规则创建后未触发：**
1. 验证文件位于项目 `.claude/` 中，而不是插件 `.claude/` 中
2. 使用读取工具检查文件以确保模式正确
3. 测试模式：`python3 -c "import re; print(re.search(r'pattern', 'test text'))"`
4. 验证 frontmatter 中的 `enabled: true`
5. 请记住：规则立即生效，无需重新启动

**如果封锁似乎太严格：**
1. 将规则文件中的`action: block`更改为`action: warn`
2. 或者将模式调整得更具体
3. 更改将在下次使用工具时生效

使用 TodoWrite 跟踪您的步骤进度。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Hookify - Create Hooks from Unwanted Behaviors

**FIRST: Load the hookify:writing-rules skill** using the Skill tool to understand rule file format and syntax.

Create hook rules to prevent problematic behaviors by analyzing the conversation or from explicit user instructions.

## Your Task

You will help the user create hookify rules to prevent unwanted behaviors. Follow these steps:

### Step 1: Gather Behavior Information

**If $ARGUMENTS is provided:**
- User has given specific instructions: `$ARGUMENTS`
- Still analyze recent conversation (last 10-15 user messages) for additional context
- Look for examples of the behavior happening

**If $ARGUMENTS is empty:**
- Launch the conversation-analyzer agent to find problematic behaviors
- Agent will scan user prompts for frustration signals
- Agent will return structured findings

**To analyze conversation:**
Use the Task tool to launch conversation-analyzer agent:
```
{
  "subagent_type": "general-purpose",
  "description": "Analyze conversation for unwanted behaviors",
  "prompt": "You are analyzing a Claude Code conversation to find behaviors the user wants to prevent.

Read user messages in the current conversation and identify:
1. Explicit requests to avoid something (\"don't do X\", \"stop doing Y\")
2. Corrections or reversions (user fixing Claude's actions)
3. Frustrated reactions (\"why did you do X?\", \"I didn't ask for that\")
4. Repeated issues (same problem multiple times)

For each issue found, extract:
- What tool was used (Bash, Edit, Write, etc.)
- Specific pattern or command
- Why it was problematic
- User's stated reason

Return findings as a structured list with:
- category: Type of issue
- tool: Which tool was involved
- pattern: Regex or literal pattern to match
- context: What happened
- severity: high/medium/low

Focus on the most recent issues (last 20-30 messages). Don't go back further unless explicitly asked."
}
```

### Step 2: Present Findings to User

After gathering behaviors (from arguments or agent), present to user using AskUserQuestion:

**Question 1: Which behaviors to hookify?**
- Header: "Create Rules"
- multiSelect: true
- Options: List each detected behavior (max 4)
  - Label: Short description (e.g., "Block rm -rf")
  - Description: Why it's problematic

**Question 2: For each selected behavior, ask about action:**
- "Should this block the operation or just warn?"
- Options:
  - "Just warn" (action: warn - shows message but allows)
  - "Block operation" (action: block - prevents execution)

**Question 3: Ask for example patterns:**
- "What patterns should trigger this rule?"
- Show detected patterns
- Allow user to refine or add more

### Step 3: Generate Rule Files

For each confirmed behavior, create a `.claude/hookify.{rule-name}.local.md` file:

**Rule naming convention:**
- Use kebab-case
- Be descriptive: `block-dangerous-rm`, `warn-console-log`, `require-tests-before-stop`
- Start with action verb: block, warn, prevent, require

**File format:**
```markdown
---
name: {rule-name}
enabled: true
event: {bash|file|stop|prompt|all}
pattern: {regex pattern}
action: {warn|block}
---

{Message to show Claude when rule triggers}
```

**Action values:**
- `warn`: Show message but allow operation (default)
- `block`: Prevent operation or stop session

**For more complex rules (multiple conditions):**
```markdown
---
name: {rule-name}
enabled: true
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.env$
  - field: new_text
    operator: contains
    pattern: API_KEY
---

{Warning message}
```

### Step 4: Create Files and Confirm

**IMPORTANT**: Rule files must be created in the current working directory's `.claude/` folder, NOT the plugin directory.

Use the current working directory (where Claude Code was started) as the base path.

1. Check if `.claude/` directory exists in current working directory
   - If not, create it first with: `mkdir -p .claude`

2. Use Write tool to create each `.claude/hookify.{name}.local.md` file
   - Use relative path from current working directory: `.claude/hookify.{name}.local.md`
   - The path should resolve to the project's .claude directory, not the plugin's

3. Show user what was created:
   ```
   Created 3 hookify rules:
   - .claude/hookify.dangerous-rm.local.md
   - .claude/hookify.console-log.local.md
   - .claude/hookify.sensitive-files.local.md

   These rules will trigger on:
   - dangerous-rm: Bash commands matching "rm -rf"
   - console-log: Edits adding console.log statements
   - sensitive-files: Edits to .env or credentials files
   ```

4. Verify files were created in the correct location by listing them

5. Inform user: **"Rules are active immediately - no restart needed!"**

   The hookify hooks are already loaded and will read your new rules on the next tool use.

## Event Types Reference

- **bash**: Matches Bash tool commands
- **file**: Matches Edit, Write, MultiEdit tools
- **stop**: Matches when agent wants to stop (use for completion checks)
- **prompt**: Matches when user submits prompts
- **all**: Matches all events

## Pattern Writing Tips

**Bash patterns:**
- Match dangerous commands: `rm\s+-rf|chmod\s+777|dd\s+if=`
- Match specific tools: `npm\s+install\s+|pip\s+install`

**File patterns:**
- Match code patterns: `console\.log\(|eval\(|innerHTML\s*=`
- Match file paths: `\.env$|\.git/|node_modules/`

**Stop patterns:**
- Check for missing steps: (check transcript or completion criteria)

## Example Workflow

**User says**: "/hookify Don't use rm -rf without asking me first"

**Your response**:
1. Analyze: User wants to prevent rm -rf commands
2. Ask: "Should I block this command or just warn you?"
3. User selects: "Just warn"
4. Create `.claude/hookify.dangerous-rm.local.md`:
   ```markdown
   ---
   name: warn-dangerous-rm
   enabled: true
   event: bash
   pattern: rm\s+-rf
   ---

   ⚠️ **Dangerous rm command detected**

   You requested to be warned before using rm -rf.
   Please verify the path is correct.
   ```
5. Confirm: "Created hookify rule. It's active immediately - try triggering it!"

## Important Notes

- **No restart needed**: Rules take effect immediately on the next tool use
- **File location**: Create files in project's `.claude/` directory (current working directory), NOT the plugin's .claude/
- **Regex syntax**: Use Python regex syntax (raw strings, no need to escape in YAML)
- **Action types**: Rules can `warn` (default) or `block` operations
- **Testing**: Test rules immediately after creating them

## Troubleshooting

**If rule file creation fails:**
1. Check current working directory with pwd
2. Ensure `.claude/` directory exists (create with mkdir if needed)
3. Use absolute path if needed: `{cwd}/.claude/hookify.{name}.local.md`
4. Verify file was created with Glob or ls

**If rule doesn't trigger after creation:**
1. Verify file is in project `.claude/` not plugin `.claude/`
2. Check file with Read tool to ensure pattern is correct
3. Test pattern with: `python3 -c "import re; print(re.search(r'pattern', 'test text'))"`
4. Verify `enabled: true` in frontmatter
5. Remember: Rules work immediately, no restart needed

**If blocking seems too strict:**
1. Change `action: block` to `action: warn` in the rule file
2. Or adjust the pattern to be more specific
3. Changes take effect on next tool use

Use TodoWrite to track your progress through the steps.


</details>
