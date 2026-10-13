# Continue CLI 的 AI 代码检查

说明安装与登录、发现检查 Agent、按分支对比运行、输出格式和自动修复，用于在本地变更或 CI 中执行 AI 检查。

- 来源：Continue
- 资料类型：存档文档
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-02-02](<https://github.com/continuedev/continue/commit/46b04cad4e8ca08a1bbff06dd38ee640828cc93b>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/continuedev/continue/blob/main/skills/cn-check/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/Continue CLI 的 AI 代码检查.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: cn-check
description: Install and run the Continue CLI (`cn`) to execute AI agent checks on local code changes. Use when asked to "run checks", "lint with AI", "review my changes with cn", or set up Continue CI locally.
license: Apache-2.0
metadata:
  author: continuedev
  version: "1.0.0"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# cn check — 本地 AI Agent 检查

Run AI-powered code checks locally against your working tree changes using the Continue CLI.每项检查都是一个 agent（在 markdown 中定义），用于审查您的差异、识别问题，并可选择建议修复作为补丁。

## 何时使用

- 用户要求对其代码更改运行 AI 检查
- 用户想要在项目中设置`cn check`
- 用户需要创建自定义支票 agents
- User wants to apply AI-suggested fixes locally
- 用户询问基于 Continue CI 或 agent 的代码审查

## 安装

### 先决条件

- Node.js 18+
- 具有未提交或分支更改的 git 存储库

### 安装CLI

```bash
npm install -g @continuedev/cli
```

### 身份验证（集线器检查必需，仅本地可选）

```bash
cn login
```

This opens a browser for authentication.登录后，集线器配置的检查将自动可用。

## 用途

### 基本：运行所有发现的检查

```bash
cn check
```

这会自动检测来自三个来源的检查（按优先级顺序）：

1. Hub API — 检查 continue.dev 上为您的存储库配置的信息
2. 本地 agents — `.continue/agents/*.md` 中的 markdown 文件

### 明确指定 agents

```bash
# Run a single local agent
cn check --agent .continue/agents/security-review.md

# Run a Hub-published agent
cn check --agent myorg/code-style

# Run multiple agents
cn check --agent .continue/agents/security.md --agent .continue/agents/docs.md
```

### 与特定的基础分支进行比较

```bash
cn check --base develop
```

默认：自动检测`main`或`master`。

### 输出格式

```bash
# JSON output (for CI pipelines or scripting)
cn check --format json

# Unified patch output (pipe to git apply)
cn check --patch | git apply

# Stop on first failure
cn check --fail-fast
```

### 自动修复模式

```bash
cn check --fix
```

运行所有检查，然后将任何建议的补丁直接应用到工作树。冲突的补丁会被报告但会被跳过。

## 创建支票 Agent

在 `.continue/agents/<name>.md` 创建一个 markdown 文件：

```markdown
# Security Review

You are a security reviewer. Examine the code changes for:

- SQL injection vulnerabilities
- XSS risks in user-facing output
- Hardcoded secrets or credentials
- Insecure use of eval() or similar

If you find issues, edit the files to fix them. If everything looks good, say so and exit.
```

agent 接收：

- 与基础分支的完整差异
- 已更改文件的列表
- 访问临时工作树中的 read/edit 文件

agent 所做的任何编辑都会被捕获为补丁，并报告为“失败”并附有建议的更改。

## 它是如何运作的

1. **Diff** — 计算 `git diff <base>...HEAD` 以查找已更改的文件
2. **Resolve** — 发现要运行的检查（集线器、本地或 `--agent` 标志）
3. **Worktree** — 每次检查创建一个临时 git 工作树以进行隔离
4. **Run** — 每次检查分叉一个工作进程； agent 具有完整的工具访问权限
5. **Capture** - agent 完成后，在工作树中捕获 `git diff` 作为补丁
6. **报告** - 渲染结果：通过（无更改）、失败（生成补丁）或错误

默认情况下，检查并行运行。使用 `--fail-fast` 进行顺序执行，在第一次失败时停止。

## 输出

### 交互终端（TTY）

实时更新表显示检查进度：

```
cn check  -  3 checks against main  -  5 changed files

Check               Status         Time
--------------------------------------------
Security Review     * Running       12s
Code Style          Pass            8s
Documentation       Pending         -
```

完成后，将打印一份完整的报告，其中包含 pass/fail 状态、agent 输出和建议的补丁。

### JSON 输出 (`--format json`)

```json
{
  "checks": [
    {
      "agent": ".continue/agents/security.md",
      "name": "security",
      "status": "pass",
      "patch": "",
      "output": "No security issues found.",
      "duration": 8.2
    }
  ],
  "summary": {
    "total": 1,
    "passed": 1,
    "failed": 0,
    "errored": 0
  }
}
```

## CLI 参考

```
cn check [options]

Options:
  --base <branch>     Base branch for diff (default: auto-detect)
  --format <format>   Output format: text or json (default: text)
  --fix               Apply suggested fixes to working tree
  --patch             Output unified patch (pipe to git apply)
  --fail-fast         Stop after first failing check
  --agent <agent>     Agent to run (hub slug or local path, repeatable)
  --config <path>     Path to config file
  --org <slug>        Organization slug
  --verbose           Enable debug logging
```

## 故障排除

|问题|解决方案|
| ---------------------------- | ------------------------------------------------------------------------------------- |
|“未检测到任何变化”|确保您有未提交的更改或指定 `--base`|
|“未找到支票”|创建 `.continue/agents/*.md` 文件或运行 `cn login` 进行集线器检查|
|检查超时（5 分钟）|减少 diff 大小或拆分为焦点 agents|
|“工作人员退出，代码为 1”|使用 `--verbose` 运行以查看工作线程 stderr|
|补丁与 `--fix` 冲突|手动应用补丁：`cn check --patch > changes.patch && git apply changes.patch`|


## 原文对照

<details>
<summary>展开完整原文</summary>

# cn check — Local AI Agent Checks

Run AI-powered code checks locally against your working tree changes using the Continue CLI. Each check is an agent (defined in markdown) that reviews your diff, identifies issues, and optionally suggests fixes as a patch.

## When to Use

- User asks to run AI checks on their code changes
- User wants to set up `cn check` in a project
- User needs to create custom check agents
- User wants to apply AI-suggested fixes locally
- User asks about Continue CI or agent-based code review

## Installation

### Prerequisites

- Node.js 18+
- A git repository with uncommitted or branched changes

### Install the CLI

```bash
npm install -g @continuedev/cli
```

### Authenticate (required for Hub checks, optional for local-only)

```bash
cn login
```

This opens a browser for authentication. After login, Hub-configured checks are available automatically.

## Usage

### Basic: Run all discovered checks

```bash
cn check
```

This auto-detects checks from three sources (in priority order):

1. Hub API — checks configured for your repo on continue.dev
2. Local agents — markdown files in `.continue/agents/*.md`

### Specify agents explicitly

```bash
# Run a single local agent
cn check --agent .continue/agents/security-review.md

# Run a Hub-published agent
cn check --agent myorg/code-style

# Run multiple agents
cn check --agent .continue/agents/security.md --agent .continue/agents/docs.md
```

### Compare against a specific base branch

```bash
cn check --base develop
```

Default: auto-detects `main` or `master`.

### Output formats

```bash
# JSON output (for CI pipelines or scripting)
cn check --format json

# Unified patch output (pipe to git apply)
cn check --patch | git apply

# Stop on first failure
cn check --fail-fast
```

### Auto-fix mode

```bash
cn check --fix
```

Runs all checks, then applies any suggested patches directly to the working tree. Patches that conflict are reported but skipped.

## Creating a Check Agent

Create a markdown file at `.continue/agents/<name>.md`:

```markdown
# Security Review

You are a security reviewer. Examine the code changes for:

- SQL injection vulnerabilities
- XSS risks in user-facing output
- Hardcoded secrets or credentials
- Insecure use of eval() or similar

If you find issues, edit the files to fix them. If everything looks good, say so and exit.
```

The agent receives:

- The full diff against the base branch
- A list of changed files
- Access to read/edit files in a temporary worktree

Any edits the agent makes are captured as a patch and reported as a "fail" with suggested changes.

## How It Works

1. **Diff** — Computes `git diff <base>...HEAD` to find changed files
2. **Resolve** — Discovers which checks to run (Hub, local, or `--agent` flags)
3. **Worktree** — Creates a temporary git worktree per check for isolation
4. **Run** — Forks a worker process per check; the agent runs with full tool access
5. **Capture** — After the agent finishes, captures `git diff` in the worktree as a patch
6. **Report** — Renders results: pass (no changes), fail (patch produced), or error

Checks run in parallel by default. Use `--fail-fast` for sequential execution that stops on first failure.

## Output

### Interactive terminal (TTY)

A live-updating table shows check progress:

```
cn check  -  3 checks against main  -  5 changed files

Check               Status         Time
--------------------------------------------
Security Review     * Running       12s
Code Style          Pass            8s
Documentation       Pending         -
```

When complete, a full report prints with pass/fail status, agent output, and suggested patches.

### JSON output (`--format json`)

```json
{
  "checks": [
    {
      "agent": ".continue/agents/security.md",
      "name": "security",
      "status": "pass",
      "patch": "",
      "output": "No security issues found.",
      "duration": 8.2
    }
  ],
  "summary": {
    "total": 1,
    "passed": 1,
    "failed": 0,
    "errored": 0
  }
}
```

## CLI Reference

```
cn check [options]

Options:
  --base <branch>     Base branch for diff (default: auto-detect)
  --format <format>   Output format: text or json (default: text)
  --fix               Apply suggested fixes to working tree
  --patch             Output unified patch (pipe to git apply)
  --fail-fast         Stop after first failing check
  --agent <agent>     Agent to run (hub slug or local path, repeatable)
  --config <path>     Path to config file
  --org <slug>        Organization slug
  --verbose           Enable debug logging
```

## Troubleshooting

| Problem                      | Solution                                                                              |
| ---------------------------- | ------------------------------------------------------------------------------------- |
| "No changes detected"        | Make sure you have uncommitted changes or specify `--base`                            |
| "No checks found"            | Create `.continue/agents/*.md` files or run `cn login` for Hub checks                 |
| Check times out (5 min)      | Reduce diff size or split into focused agents                                         |
| "Worker exited with code 1"  | Run with `--verbose` to see worker stderr                                             |
| Patch conflicts with `--fix` | Apply patches manually: `cn check --patch > changes.patch && git apply changes.patch` |


</details>
