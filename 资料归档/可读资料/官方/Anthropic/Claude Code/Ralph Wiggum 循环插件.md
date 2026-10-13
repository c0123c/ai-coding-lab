# Ralph Wiggum 循环插件

介绍反复读取结果并继续开发的循环机制、启动和停止命令及结束条件，用于理解持续执行的插件实现。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-16](<https://github.com/anthropics/claude-code/commit/68f90e05dd919b626c43351624fd0dc10c70b341>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/ralph-wiggum/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Ralph Wiggum 循环插件.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 拉尔夫·维格姆插件

在 Claude Code 中实现 Ralph Wiggum 技术，用于迭代、自引用 AI 开发循环。

## 拉尔夫是什么？

Ralph 是一种基于连续 AI agent 循环的开发方法。正如 Geoffrey Huntley 所描述的：**“Ralph 是一个 Bash 循环”** - 一个简单的 `while true`，它重复地向 AI agent 提供一个提示文件，允许它迭代地改进其工作直到完成。

该技术以《辛普森一家》中的拉尔夫·威格姆 (Ralph Wiggum) 命名，体现了尽管遇到挫折仍坚持迭代的哲学。

### 核心理念

该插件使用 **Stop 钩子 ** 来拦截 Claude 的退出尝试来实现 Ralph：

```bash
# You run ONCE:
/ralph-loop "Your task description" --completion-promise "DONE"

# Then Claude Code automatically:
# 1. Works on the task
# 2. Tries to exit
# 3. Stop hook blocks exit
# 4. Stop hook feeds the SAME prompt back
# 5. Repeat until completion
```

循环发生在当前会话 ** 内 ** - 您不需要外部 bash 循环。 `hooks/stop-hook.sh` 中的 Stop 挂钩通过阻止正常会话退出来创建自引用反馈循环。

这将创建一个 ** 自参考反馈循环 **，其中：
- 提示在迭代之间永远不会改变
- Claude 之前的工作仍保留在文件中
- 每次迭代都会看到修改的文件和 git 历史记录
- Claude 通过读取文件中自己过去的工作来自主改进

## 快速入门

```bash
/ralph-loop "Build a REST API for todos. Requirements: CRUD operations, input validation, tests. Output <promise>COMPLETE</promise> when done." --completion-promise "COMPLETE" --max-iterations 50
```

Claude 将：
- 迭代实现 API
- 运行测试并查看失败情况
- 根据测试输出修复错误
- 迭代直至满足所有要求
- 完成后输出完成承诺

## 命令

### /ralph-loop

在当前会话中启动 Ralph 循环。

**用法：**
```bash
/ralph-loop "<prompt>" --max-iterations <n> --completion-promise "<text>"
```

**选项：**
- `--max-iterations <n>` - N次迭代后停止（默认：无限制）
- `--completion-promise <text>` - 表示完成的短语

### /cancel-ralph

取消活动的拉尔夫循环。

**用法：**
```bash
/cancel-ralph
```

## 即时写作最佳实践

### 1。明确的完成标准

❌ 坏：“构建一个待办事项 API 并使其变得更好。”

✅ 好：
```markdown
Build a REST API for todos.

When complete:
- All CRUD endpoints working
- Input validation in place
- Tests passing (coverage > 80%)
- README with API docs
- Output: <promise>COMPLETE</promise>
```

### 2。增量目标

❌坏：“创建一个完整的电子商务平台。”

✅ 好：
```markdown
Phase 1: User authentication (JWT, tests)
Phase 2: Product catalog (list/search, tests)
Phase 3: Shopping cart (add/remove, tests)

Output <promise>COMPLETE</promise> when all phases done.
```

### 3。自我修正

❌ 不好：“为功能 X 编写代码。”

✅ 好：
```markdown
Implement feature X following TDD:
1. Write failing tests
2. Implement feature
3. Run tests
4. If any fail, debug and fix
5. Refactor if needed
6. Repeat until all green
7. Output: <promise>COMPLETE</promise>
```

### 4。逃生舱口

始终使用 `--max-iterations` 作为安全网，以防止不可能的任务出现无限循环：

```bash
# Recommended: Always set a reasonable iteration limit
/ralph-loop "Try to implement feature X" --max-iterations 20

# In your prompt, include what to do if stuck:
# "After 15 iterations, if not complete:
#  - Document what's blocking progress
#  - List what was attempted
#  - Suggest alternative approaches"
```

**注意**：`--completion-promise`使用精确的字符串匹配，因此不能将其用于多个完成条件（例如“SUCCESS”与“BLOCKED”）。始终依赖 `--max-iterations` 作为您的主要安全机制。

## 理念

拉尔夫体现了几个关键原则：

### 1。迭代>完善
不要在第一次尝试时就追求完美。让循环完善工作。

### 2。失败就是数据
“确定性的坏”意味着故障是可预测的且信息丰富。使用它们来调整提示。

### 3。操作员技能很重要
成功取决于写出好的提示，而不仅仅是拥有好的模型。

### 4。坚持就是胜利
不断尝试直到成功。循环自动处理重试逻辑。

## 何时使用拉尔夫

**适用于：**
- 明确的任务和明确的成功标准
- 需要迭代和细化的任务（例如，让测试通过）
- 您可以离开的绿地项目
- 自动验证任务（测试、linter）

**不适合：**
- 需要人类判断或设计决策的任务
- 一键操作
- 成功标准不明确的任务
- 生产调试（改为使用目标调试）

## 真实世界的结果

- 在 Y Combinator 黑客马拉松测试中连夜成功生成 6 存储库
- 一份 5 万美元的合同以 API 成本 297 完成
- 使用这种方法在 3 个月内创建了整个编程语言（“诅咒”）

## 了解更多

- 原始技术：https://ghuntley.com/ralph/
- 拉尔夫协调器：https://github.com/mikeyobrien/ralph-orchestrator

## 寻求帮助

运行Claude Code中的`/help`，查看详细命令参考和示例。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Ralph Wiggum Plugin

Implementation of the Ralph Wiggum technique for iterative, self-referential AI development loops in Claude Code.

## What is Ralph?

Ralph is a development methodology based on continuous AI agent loops. As Geoffrey Huntley describes it: **"Ralph is a Bash loop"** - a simple `while true` that repeatedly feeds an AI agent a prompt file, allowing it to iteratively improve its work until completion.

The technique is named after Ralph Wiggum from The Simpsons, embodying the philosophy of persistent iteration despite setbacks.

### Core Concept

This plugin implements Ralph using a **Stop hook** that intercepts Claude's exit attempts:

```bash
# You run ONCE:
/ralph-loop "Your task description" --completion-promise "DONE"

# Then Claude Code automatically:
# 1. Works on the task
# 2. Tries to exit
# 3. Stop hook blocks exit
# 4. Stop hook feeds the SAME prompt back
# 5. Repeat until completion
```

The loop happens **inside your current session** - you don't need external bash loops. The Stop hook in `hooks/stop-hook.sh` creates the self-referential feedback loop by blocking normal session exit.

This creates a **self-referential feedback loop** where:
- The prompt never changes between iterations
- Claude's previous work persists in files
- Each iteration sees modified files and git history
- Claude autonomously improves by reading its own past work in files

## Quick Start

```bash
/ralph-loop "Build a REST API for todos. Requirements: CRUD operations, input validation, tests. Output <promise>COMPLETE</promise> when done." --completion-promise "COMPLETE" --max-iterations 50
```

Claude will:
- Implement the API iteratively
- Run tests and see failures
- Fix bugs based on test output
- Iterate until all requirements met
- Output the completion promise when done

## Commands

### /ralph-loop

Start a Ralph loop in your current session.

**Usage:**
```bash
/ralph-loop "<prompt>" --max-iterations <n> --completion-promise "<text>"
```

**Options:**
- `--max-iterations <n>` - Stop after N iterations (default: unlimited)
- `--completion-promise <text>` - Phrase that signals completion

### /cancel-ralph

Cancel the active Ralph loop.

**Usage:**
```bash
/cancel-ralph
```

## Prompt Writing Best Practices

### 1. Clear Completion Criteria

❌ Bad: "Build a todo API and make it good."

✅ Good:
```markdown
Build a REST API for todos.

When complete:
- All CRUD endpoints working
- Input validation in place
- Tests passing (coverage > 80%)
- README with API docs
- Output: <promise>COMPLETE</promise>
```

### 2. Incremental Goals

❌ Bad: "Create a complete e-commerce platform."

✅ Good:
```markdown
Phase 1: User authentication (JWT, tests)
Phase 2: Product catalog (list/search, tests)
Phase 3: Shopping cart (add/remove, tests)

Output <promise>COMPLETE</promise> when all phases done.
```

### 3. Self-Correction

❌ Bad: "Write code for feature X."

✅ Good:
```markdown
Implement feature X following TDD:
1. Write failing tests
2. Implement feature
3. Run tests
4. If any fail, debug and fix
5. Refactor if needed
6. Repeat until all green
7. Output: <promise>COMPLETE</promise>
```

### 4. Escape Hatches

Always use `--max-iterations` as a safety net to prevent infinite loops on impossible tasks:

```bash
# Recommended: Always set a reasonable iteration limit
/ralph-loop "Try to implement feature X" --max-iterations 20

# In your prompt, include what to do if stuck:
# "After 15 iterations, if not complete:
#  - Document what's blocking progress
#  - List what was attempted
#  - Suggest alternative approaches"
```

**Note**: The `--completion-promise` uses exact string matching, so you cannot use it for multiple completion conditions (like "SUCCESS" vs "BLOCKED"). Always rely on `--max-iterations` as your primary safety mechanism.

## Philosophy

Ralph embodies several key principles:

### 1. Iteration > Perfection
Don't aim for perfect on first try. Let the loop refine the work.

### 2. Failures Are Data
"Deterministically bad" means failures are predictable and informative. Use them to tune prompts.

### 3. Operator Skill Matters
Success depends on writing good prompts, not just having a good model.

### 4. Persistence Wins
Keep trying until success. The loop handles retry logic automatically.

## When to Use Ralph

**Good for:**
- Well-defined tasks with clear success criteria
- Tasks requiring iteration and refinement (e.g., getting tests to pass)
- Greenfield projects where you can walk away
- Tasks with automatic verification (tests, linters)

**Not good for:**
- Tasks requiring human judgment or design decisions
- One-shot operations
- Tasks with unclear success criteria
- Production debugging (use targeted debugging instead)

## Real-World Results

- Successfully generated 6 repositories overnight in Y Combinator hackathon testing
- One $50k contract completed for $297 in API costs
- Created entire programming language ("cursed") over 3 months using this approach

## Learn More

- Original technique: https://ghuntley.com/ralph/
- Ralph Orchestrator: https://github.com/mikeyobrien/ralph-orchestrator

## For Help

Run `/help` in Claude Code for detailed command reference and examples.


</details>
