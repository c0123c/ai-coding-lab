# Cline 拉取请求创建技能

说明检查 GitHub CLI 与登录状态、分析分支和提交、准备 PR 模板、推送及创建拉取请求的流程；遵循该项目的贡献约定。

- 来源：Cline
- 资料类型：存档文档
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-02-24](<https://github.com/cline/cline/commit/091cf945e41966aaadef4b4435f41e15ec413d6d>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/cline/cline/blob/main/.agents/skills/create-pull-request/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline 拉取请求创建技能.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: create-pull-request
description: Create a GitHub pull request following project conventions. Use when the user asks to create a PR, submit changes for review, or open a pull request. Handles commit analysis, branch management, PR template usage, and PR creation using the gh CLI tool.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 创建拉取请求

此技能将指导您创建遵循项目约定和最佳实践的结构良好的 GitHub 拉取请求。

## 先决条件检查

在继续之前，请验证以下内容：

### 1。检查是否安装了`gh`CLI

```bash
gh --version
```

如果未安装，请通知用户：
> GitHub CLI (`gh`) 是必需的，但未安装。请安装它：
> - macOS: `brew install gh`
> - 其他：https://cli.github.com/

### 2。检查是否通过GitHub进行身份验证

```bash
gh auth status
```

如果未通过认证，引导用户运行`gh auth login`。

### 3。验证干净的工作目录

```bash
git status
```

如果有未提交的更改，询问用户是否：
- 将它们作为此 PR 的一部分提交
- 暂时把它们藏起来
- 丢弃它们（小心）

## 收集背景信息

### 1。识别当前分支

```bash
git branch --show-current
```

确保您不在 `main` 或 `master` 上。如果是这样，请要求用户创建或切换到功能分支。

### 2。找到基础分支

```bash
git remote show origin | grep "HEAD branch"
```

这通常是 `main` 或 `master`。

### 3。分析与此相关的最近提交 PR

```bash
git log origin/main..HEAD --oneline --no-decorate
```

查看这些提交以了解：
- 正在引入哪些变化
- PR的范围（单个feature/fix或多个更改）
- 提交是否应该被压缩或重组

### 4。查看差异

```bash
git diff origin/main..HEAD --stat
```

这显示了哪些文件发生了更改，并有助于识别更改的类型。

## 信息收集

在创建PR之前，您需要以下信息。检查是否可以从以下位置推断：
- 提交消息
- 分支名称（例如，`fix/issue-123`、`feature/new-login`）
- 更改的文件及其内容

如果缺少任何关键信息，请使用`ask_followup_question`询问用户：

### 所需信息

1. **相关问题号**：在提交消息中查找类似`#123`、`fixes #123`或`closes #123`的模式
2. **描述**：这解决了什么问题？为什么要做这些改变？
3. **变更类型**：错误修复、新功能、重大变更、重构、修饰、文档或工作流程
4. **测试程序**：这是如何测试的？什么可以打破？

### 澄清问题示例

如果未找到问题编号：
> 我在提交消息或分支名称中找不到相关的问题编号。此 PR 解决什么 GitHub 问题？ （输入问题编号，例如，“123”或“N/A”用于小修复）

## Git 最佳实践

在创建 PR 之前，请考虑以下最佳实践：

### 保证卫生

1. **原子提交**：每次提交应代表一个逻辑更改
2. **清除提交消息**：尽可能遵循常规提交格式
3. **无合并提交**：更喜欢变基而不是合并以保持历史记录干净

### 分行管理

1. **基于最新的主版本**（如果需要）：
   ```bash
   git fetch origin
   git rebase origin/main
   ```

2. **如果适当的话压缩**：如果有许多小的“WIP”提交，请考虑交互式变基：
   ```bash
   git rebase -i origin/main
   ```
仅当提交显得混乱并且用户对变基感到满意时才建议这样做。

### 推送变更

确保所有提交均已推送：
```bash
git push origin HEAD
```

如果分支被重新定位，您可能需要：
```bash
git push origin HEAD --force-with-lease
```

## 创建拉取请求

**IMPORTANT**：阅读并使用`.github/pull_request_template.md`处的PR模板。 PR正文格式必须**严格匹配**模板结构。不要偏离模板格式。

填写模板时：
- 将 `#XXXX` 替换为实际问题编号，如果不存在问题，则保留为 `#XXXX`（用于小修复）
- 使用从提交和上下文中收集的相关信息填写所有部分
- 标记适当的“更改类型”复选框
- 填写适用的“飞行前检查表”项目

### 使用 gh CLI 创建 PR

**对 PR 正文 ** 使用临时文件以避免 shell 转义问题、换行问题和其他命令行问题：

1. 将 PR 正文写入临时文件：
   ```
   /tmp/pr-body.md
   ```

2. 使用以下文件创建 PR：
   ```bash
   gh pr create --title "PR_TITLE" --body-file /tmp/pr-body.md --base main
   ```

3. 清理临时文件：
   ```bash
   rm /tmp/pr-body.md
   ```

对于 PR 草案：
```bash
gh pr create --title "PR_TITLE" --body-file /tmp/pr-body.md --base main --draft
```

**为什么使用文件？**直接通过`--body`传递带有换行符、特殊字符和复选框的复杂降价很容易出错。 `--body-file` 标志可靠地处理所有内容。

## 创作后

创建PR后：

1. **显示PR URL**以便用户可以查看
2. **关于 CI 检查的提醒 **：测试和 linting 将自动运行
3. **建议后续步骤**：
   - 如果需要，添加审阅者：`gh pr edit --add-reviewer USERNAME`
   - 如果需要，添加标签：`gh pr edit --add-label "bug"`

## 错误处理

### 常见问题

1. **main 之前没有提交**：分支没有要提交的更改
   - 询问用户是否打算在不同的分支上工作

2. **分支未推送**：远程没有分支
   - 先推送分支：`git push -u origin HEAD`

3. **PR 已存在**：此分支的 PR 已存在
   - 显示现有的PR：`gh pr view`
   - 询问他们是否想要更新

4. **合并冲突**：分支与基冲突
   - 指导用户解决冲突或重新设定基础

## 摘要清单

在最终确定之前，请确保：
- [ ] `gh` CLI 已安装并经过身份验证
- [ ] 工作目录是干净的
- [ ] 所有提交均已推送
- [ ] 分支与基础分支是最新的
- [ ] 已识别相关问题编号，或使用占位符
- [ ] PR 描述完全遵循模板
- [ ] 选择适当的更改类型
- [ ] 已解决飞行前检查清单项目


## 原文对照

<details>
<summary>展开完整原文</summary>

# Create Pull Request

This skill guides you through creating a well-structured GitHub pull request that follows project conventions and best practices.

## Prerequisites Check

Before proceeding, verify the following:

### 1. Check if `gh` CLI is installed

```bash
gh --version
```

If not installed, inform the user:
> The GitHub CLI (`gh`) is required but not installed. Please install it:
> - macOS: `brew install gh`
> - Other: https://cli.github.com/

### 2. Check if authenticated with GitHub

```bash
gh auth status
```

If not authenticated, guide the user to run `gh auth login`.

### 3. Verify clean working directory

```bash
git status
```

If there are uncommitted changes, ask the user whether to:
- Commit them as part of this PR
- Stash them temporarily
- Discard them (with caution)

## Gather Context

### 1. Identify the current branch

```bash
git branch --show-current
```

Ensure you're not on `main` or `master`. If so, ask the user to create or switch to a feature branch.

### 2. Find the base branch

```bash
git remote show origin | grep "HEAD branch"
```

This is typically `main` or `master`.

### 3. Analyze recent commits relevant to this PR

```bash
git log origin/main..HEAD --oneline --no-decorate
```

Review these commits to understand:
- What changes are being introduced
- The scope of the PR (single feature/fix or multiple changes)
- Whether commits should be squashed or reorganized

### 4. Review the diff

```bash
git diff origin/main..HEAD --stat
```

This shows which files changed and helps identify the type of change.

## Information Gathering

Before creating the PR, you need the following information. Check if it can be inferred from:
- Commit messages
- Branch name (e.g., `fix/issue-123`, `feature/new-login`)
- Changed files and their content

If any critical information is missing, use `ask_followup_question` to ask the user:

### Required Information

1. **Related Issue Number**: Look for patterns like `#123`, `fixes #123`, or `closes #123` in commit messages
2. **Description**: What problem does this solve? Why were these changes made?
3. **Type of Change**: Bug fix, new feature, breaking change, refactor, cosmetic, documentation, or workflow
4. **Test Procedure**: How was this tested? What could break?

### Example clarifying question

If the issue number is not found:
> I couldn't find a related issue number in the commit messages or branch name. What GitHub issue does this PR address? (Enter the issue number, e.g., "123" or "N/A" for small fixes)

## Git Best Practices

Before creating the PR, consider these best practices:

### Commit Hygiene

1. **Atomic commits**: Each commit should represent a single logical change
2. **Clear commit messages**: Follow conventional commit format when possible
3. **No merge commits**: Prefer rebasing over merging to keep history clean

### Branch Management

1. **Rebase on latest main** (if needed):
   ```bash
   git fetch origin
   git rebase origin/main
   ```

2. **Squash if appropriate**: If there are many small "WIP" commits, consider interactive rebase:
   ```bash
   git rebase -i origin/main
   ```
   Only suggest this if commits appear messy and the user is comfortable with rebasing.

### Push Changes

Ensure all commits are pushed:
```bash
git push origin HEAD
```

If the branch was rebased, you may need:
```bash
git push origin HEAD --force-with-lease
```

## Create the Pull Request

**IMPORTANT**: Read and use the PR template at `.github/pull_request_template.md`. The PR body format must **strictly match** the template structure. Do not deviate from the template format.

When filling out the template:
- Replace `#XXXX` with the actual issue number, or keep as `#XXXX` if no issue exists (for small fixes)
- Fill in all sections with relevant information gathered from commits and context
- Mark the appropriate "Type of Change" checkbox(es)
- Complete the "Pre-flight Checklist" items that apply

### Create PR with gh CLI

**Use a temporary file for the PR body** to avoid shell escaping issues, newline problems, and other command-line flakiness:

1. Write the PR body to a temporary file:
   ```
   /tmp/pr-body.md
   ```

2. Create the PR using the file:
   ```bash
   gh pr create --title "PR_TITLE" --body-file /tmp/pr-body.md --base main
   ```

3. Clean up the temporary file:
   ```bash
   rm /tmp/pr-body.md
   ```

For draft PRs:
```bash
gh pr create --title "PR_TITLE" --body-file /tmp/pr-body.md --base main --draft
```

**Why use a file?** Passing complex markdown with newlines, special characters, and checkboxes directly via `--body` is error-prone. The `--body-file` flag handles all content reliably.

## Post-Creation

After creating the PR:

1. **Display the PR URL** so the user can review it
2. **Remind about CI checks**: Tests and linting will run automatically
3. **Suggest next steps**:
   - Add reviewers if needed: `gh pr edit --add-reviewer USERNAME`
   - Add labels if needed: `gh pr edit --add-label "bug"`

## Error Handling

### Common Issues

1. **No commits ahead of main**: The branch has no changes to submit
   - Ask if the user meant to work on a different branch

2. **Branch not pushed**: Remote doesn't have the branch
   - Push the branch first: `git push -u origin HEAD`

3. **PR already exists**: A PR for this branch already exists
   - Show the existing PR: `gh pr view`
   - Ask if they want to update it instead

4. **Merge conflicts**: Branch conflicts with base
   - Guide user through resolving conflicts or rebasing

## Summary Checklist

Before finalizing, ensure:
- [ ] `gh` CLI is installed and authenticated
- [ ] Working directory is clean
- [ ] All commits are pushed
- [ ] Branch is up-to-date with base branch
- [ ] Related issue number is identified, or placeholder is used
- [ ] PR description follows the template exactly
- [ ] Appropriate type of change is selected
- [ ] Pre-flight checklist items are addressed


</details>
