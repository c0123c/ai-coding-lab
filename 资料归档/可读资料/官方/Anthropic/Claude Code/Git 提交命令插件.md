# Git 提交命令插件

介绍提交、推送并创建拉取请求、清理失效分支等命令及安装方法，帮助组织重复 Git 操作。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-10-17](<https://github.com/anthropics/claude-code/commit/cabd74fa9e2613b4f3fe19900862fe273c79f443>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/commit-commands/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Git 提交命令插件.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 提交命令插件

使用用于提交、推送和创建拉取请求的简单命令简化您的 git 工作流程。

## 概述

Commit Commands 插件可自动执行常见的 git 操作，减少上下文切换和手动命令执行。使用单个斜杠命令来处理整个工作流程，而不是运行多个 git 命令。

## 命令

### `/commit`

使用基于暂存和未暂存更改自动生成的提交消息创建 git 提交。

**它的作用：**
1. 分析当前 git 状态
2. 审查已分阶段和未分阶段的更改
3. 检查最近的提交消息以匹配您的存储库的风格
4. 起草适当的提交消息
5. 阶段相关文件
6. 创建提交

**用法：**
```bash
/commit
```

**示例工作流程：**
```bash
# Make some changes to your code
# Then simply run:
/commit

# Claude will:
# - Review your changes
# - Stage the files
# - Create a commit with an appropriate message
# - Show you the commit status
```

**特点：**
- 自动起草与您的存储库风格相匹配的提交消息
- 遵循传统的提交实践
- 避免提交带有机密的文件（.env、credentials.json）
- 在提交消息中包含 Claude Code 归属

### `/commit-push-pr`

一步完成提交、推送和创建拉取请求的工作流命令。

**它的作用：**
1. 创建一个新分支（如果当前在主分支上）
2. 使用适当的消息暂存并提交更改
3. 将分支推送到原点
4. 使用 `gh pr create` 创建拉取请求
5. 提供 PR URL

**用法：**
```bash
/commit-push-pr
```

**示例工作流程：**
```bash
# Make your changes
# Then run:
/commit-push-pr

# Claude will:
# - Create a feature branch (if needed)
# - Commit your changes
# - Push to remote
# - Open a PR with summary and test plan
# - Give you the PR URL to review
```

**特点：**
- 分析分支中的所有提交（不仅仅是最新的）
- 使用以下内容创建全面的 PR 描述：
  - 变更摘要（1-3 要点）
  - 测试计划清单
  - Claude Code 归属
- 自动处理分支创建
- 使用 GitHub CLI (`gh`) 创建 PR

**要求：**
- GitHub CLI (`gh`) 必须安装并经过身份验证
- 存储库必须有一个名为 `origin` 的远程

### `/clean_gone`

清理已从远程存储库中删除的本地分支。

**它的作用：**
1. 列出所有本地分支机构以识别 [ 消失 ] 状态
2. 识别并删除与 [ 消失的 ] 分支关联的工作树
3. 删除所有标记为 [gone] 的分支
4. 提供有关已删除分支的反馈

**用法：**
```bash
/clean_gone
```

**示例工作流程：**
```bash
# After PRs are merged and remote branches are deleted
/clean_gone

# Claude will:
# - Find all branches marked as [gone]
# - Remove any associated worktrees
# - Delete the stale local branches
# - Report what was cleaned up
```

**特点：**
- 处理常规分支和工作树分支
- 在删除分支之前安全删除工作树
- 显示有关已删除内容的清晰反馈
- 报告是否不需要清理

**何时使用：**
- 合并并删除远程分支后
- 当您的本地分支列表中堆满了陈旧的分支时
- 在定期存储库维护期间

## 安装

该插件包含在 Claude Code 存储库中。使用 Claude Code 时，这些命令自动可用。

## 最佳实践

### 使用`/commit`
- 在提交之前检查分阶段的更改
- 让 Claude 分析您的更改并匹配您的存储库的提交风格
- 信任自动消息，但验证其准确性
- 用于开发期间的常规提交

### 使用`/commit-push-pr`
- 当您准备好创建 PR 时使用
- 确保所有更改均已完成并经过测试
- Claude 将分析 PR 描述的完整分支历史记录
- 查看 PR 描述并根据需要进行编辑
- 当您想要最小化上下文切换时使用

### 使用`/clean_gone`
- 定期运行以保持分支列表干净
- 在合并多个 PR 后特别有用
- 安全运行 - 仅删除已远程删除的分支
- 帮助维护整洁的本地存储库

## 工作流程整合

### 快速提交工作流程：
```bash
# Write code
/commit
# Continue development
```

### 功能分支工作流程：
```bash
# Develop feature across multiple commits
/commit  # First commit
# More changes
/commit  # Second commit
# Ready to create PR
/commit-push-pr
```

### 维护工作流程：
```bash
# After several PRs are merged
/clean_gone
# Clean workspace ready for next feature
```

## 要求

- 必须安装并配置 Git
- 对于 `/commit-push-pr`：必须安装并验证 GitHub CLI (`gh`)
- 存储库必须是带有远程的git存储库

## 故障排除

### `/commit` 创建空提交

**问题**：没有要提交的更改

**解决方案**：
- 确保您有未暂存或暂存的更改
- 运行 `git status` 以验证更改是否存在

### `/commit-push-pr` 无法创建 PR

**问题**：`gh pr create`命令失败

**解决方案**：
- 安装GitHubCLI：`brew install gh`(macOS)或参见[GitHubCLI安装](<https://cli.github.com/>)
- 验证：`gh auth login`
- 确保存储库具有 GitHub 远程

### `/clean_gone` 找不到分支

**问题**：没有分支标记为[消失]

**解决方案**：
- 运行`git fetch --prune`更新远程跟踪
- 必须从远程删除分支才能显示为 [ 已消失 ]

## 温馨提示

- **与其他工具结合使用**：开发时使用`/commit`，准备好后使用`/commit-push-pr`
- **让Claude起草消息**：提交消息分析从您的存储库风格中学习
- **定期清理**：每周运行`/clean_gone`以维护干净的分支列表
- **推送前审核**：推送前始终审核提交消息和更改

## 作者

Anthropic (support@anthropic.com)

## 版本

1.0.0


## 原文对照

<details>
<summary>展开完整原文</summary>

# Commit Commands Plugin

Streamline your git workflow with simple commands for committing, pushing, and creating pull requests.

## Overview

The Commit Commands Plugin automates common git operations, reducing context switching and manual command execution. Instead of running multiple git commands, use a single slash command to handle your entire workflow.

## Commands

### `/commit`

Creates a git commit with an automatically generated commit message based on staged and unstaged changes.

**What it does:**
1. Analyzes current git status
2. Reviews both staged and unstaged changes
3. Examines recent commit messages to match your repository's style
4. Drafts an appropriate commit message
5. Stages relevant files
6. Creates the commit

**Usage:**
```bash
/commit
```

**Example workflow:**
```bash
# Make some changes to your code
# Then simply run:
/commit

# Claude will:
# - Review your changes
# - Stage the files
# - Create a commit with an appropriate message
# - Show you the commit status
```

**Features:**
- Automatically drafts commit messages that match your repo's style
- Follows conventional commit practices
- Avoids committing files with secrets (.env, credentials.json)
- Includes Claude Code attribution in commit message

### `/commit-push-pr`

Complete workflow command that commits, pushes, and creates a pull request in one step.

**What it does:**
1. Creates a new branch (if currently on main)
2. Stages and commits changes with an appropriate message
3. Pushes the branch to origin
4. Creates a pull request using `gh pr create`
5. Provides the PR URL

**Usage:**
```bash
/commit-push-pr
```

**Example workflow:**
```bash
# Make your changes
# Then run:
/commit-push-pr

# Claude will:
# - Create a feature branch (if needed)
# - Commit your changes
# - Push to remote
# - Open a PR with summary and test plan
# - Give you the PR URL to review
```

**Features:**
- Analyzes all commits in the branch (not just the latest)
- Creates comprehensive PR descriptions with:
  - Summary of changes (1-3 bullet points)
  - Test plan checklist
  - Claude Code attribution
- Handles branch creation automatically
- Uses GitHub CLI (`gh`) for PR creation

**Requirements:**
- GitHub CLI (`gh`) must be installed and authenticated
- Repository must have a remote named `origin`

### `/clean_gone`

Cleans up local branches that have been deleted from the remote repository.

**What it does:**
1. Lists all local branches to identify [gone] status
2. Identifies and removes worktrees associated with [gone] branches
3. Deletes all branches marked as [gone]
4. Provides feedback on removed branches

**Usage:**
```bash
/clean_gone
```

**Example workflow:**
```bash
# After PRs are merged and remote branches are deleted
/clean_gone

# Claude will:
# - Find all branches marked as [gone]
# - Remove any associated worktrees
# - Delete the stale local branches
# - Report what was cleaned up
```

**Features:**
- Handles both regular branches and worktree branches
- Safely removes worktrees before deleting branches
- Shows clear feedback about what was removed
- Reports if no cleanup was needed

**When to use:**
- After merging and deleting remote branches
- When your local branch list is cluttered with stale branches
- During regular repository maintenance

## Installation

This plugin is included in the Claude Code repository. The commands are automatically available when using Claude Code.

## Best Practices

### Using `/commit`
- Review the staged changes before committing
- Let Claude analyze your changes and match your repo's commit style
- Trust the automated message, but verify it's accurate
- Use for routine commits during development

### Using `/commit-push-pr`
- Use when you're ready to create a PR
- Ensure all your changes are complete and tested
- Claude will analyze the full branch history for the PR description
- Review the PR description and edit if needed
- Use when you want to minimize context switching

### Using `/clean_gone`
- Run periodically to keep your branch list clean
- Especially useful after merging multiple PRs
- Safe to run - only removes branches already deleted remotely
- Helps maintain a tidy local repository

## Workflow Integration

### Quick commit workflow:
```bash
# Write code
/commit
# Continue development
```

### Feature branch workflow:
```bash
# Develop feature across multiple commits
/commit  # First commit
# More changes
/commit  # Second commit
# Ready to create PR
/commit-push-pr
```

### Maintenance workflow:
```bash
# After several PRs are merged
/clean_gone
# Clean workspace ready for next feature
```

## Requirements

- Git must be installed and configured
- For `/commit-push-pr`: GitHub CLI (`gh`) must be installed and authenticated
- Repository must be a git repository with a remote

## Troubleshooting

### `/commit` creates empty commit

**Issue**: No changes to commit

**Solution**:
- Ensure you have unstaged or staged changes
- Run `git status` to verify changes exist

### `/commit-push-pr` fails to create PR

**Issue**: `gh pr create` command fails

**Solution**:
- Install GitHub CLI: `brew install gh` (macOS) or see [GitHub CLI installation](<https://cli.github.com/>)
- Authenticate: `gh auth login`
- Ensure repository has a GitHub remote

### `/clean_gone` doesn't find branches

**Issue**: No branches marked as [gone]

**Solution**:
- Run `git fetch --prune` to update remote tracking
- Branches must be deleted from the remote to show as [gone]

## Tips

- **Combine with other tools**: Use `/commit` during development, then `/commit-push-pr` when ready
- **Let Claude draft messages**: The commit message analysis learns from your repo's style
- **Regular cleanup**: Run `/clean_gone` weekly to maintain a clean branch list
- **Review before pushing**: Always review the commit message and changes before pushing

## Author

Anthropic (support@anthropic.com)

## Version

1.0.0


</details>
