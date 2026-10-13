# Qwen Code 仓库自动化与分流流程

介绍 Issue 分流、PR 的持续集成及版本发布自动化，帮助贡献者理解仓库机器人和审查流程。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-08-03](<https://github.com/QwenLM/qwen-code/commit/72bd3dccc29510eeb20ff45f553e926bf79c2e3c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/developers/development/issue-and-pr-automation.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code 仓库自动化与分流流程.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 自动化和分类流程

本文档详细概述了我们用于管理和分类问题以及拉取请求的自动化流程。我们的目标是提供及时反馈并确保有效地审查和整合贡献。了解这种自动化将帮助您作为贡献者了解期望什么以及如何最好地与我们的存储库机器人交互。

## 指导原则：问题和拉取请求

首先也是最重要的，几乎每个 Pull 请求 (PR) 都应该链接到相应的问题。该问题描述了“什么”和“为什么”（错误或功能），而 PR 是“如何”（实现）。这种分离有助于我们跟踪工作、确定功能的优先级并保持清晰的历史背景。我们的自动化是围绕这一原则构建的。

---

## 详细的自动化工作流程

以下是我们存储库中运行的特定自动化工作流程的细分。

### 1。当您打开问题时：`Qwen Triage`

这是您创建问题时将与之交互的第一个机器人。它的工作是执行初步分析并应用正确的标签。

- **工作流程文件**：`.github/workflows/qwen-triage.yml`
- **运行时**：在创建、编辑或重新打开问题后立即，或者在维护人员手动请求分类时。
- **它的作用**：
  - 它使用 Qwen 模型根据一组详细的指南来分析问题的标题和正文。
  - **应用一个 `area/*` 标签**：将问题分类到项目的功能区域中（例如，`area/ux`、`area/models`、`area/platform`）。
  - **应用一个 `kind/*` 标签 **：标识问题类型（例如，`kind/bug`、`kind/enhancement`、`kind/question`）。
  - **应用一个 `priority/*` 标签 **：根据描述的影响分配从 P0（严重）到 P3（低）的优先级。
  - **可能适用 `status/need-information`**：如果问题缺乏关键细节（例如日志或复制步骤），则会被标记以获取更多信息。
  - **可能适用`status/need-retesting`**：如果问题引用了超过六个版本的CLI版本，它将被标记为在当前版本上重新测试。
- **你应该做什么**：
  - 尽可能完整地填写问题模板。您提供的详细信息越多，分类就越准确。
  - 如果添加了 `status/need-information` 标签，请在评论中提供所需的详细信息。
  - 维护者可以评论 `@qwen-code /triage` 再次运行分类。

### 2。当您打开拉取请求时：`Continuous Integration (CI)`

此工作流程可确保所有更改在合并之前均符合我们的质量标准。

- **工作流程文件**：`.github/workflows/ci.yml`
- **运行时**：每次推送拉取请求时。
- **它的作用**：
  - **Lint**：检查您的代码是否符合我们项目的格式和样式规则。
  - **测试**：在macOS、Windows和Linux以及多个Node.js版本上运行我们的全套自动化测试。这是CI过程中最耗时的部分。
  - **发布覆盖评论**：成功通过所有测试后，机器人将在您的PR上发布评论。此评论总结了测试覆盖您的更改的情况。
- **你应该做什么**：
  - 确保所有 CI 检查通过。当一切成功时，绿色复选标记 ✅ 将出现在您的提交旁边。
  - 如果检查失败（红色“X”❌），请单击失败检查旁边的“详细信息”链接以查看日志、识别问题并推送修复。

### 3。发布自动化

此工作流程处理打包和发布 Qwen Code 新版本的过程。

- **工作流程文件**：`.github/workflows/release.yml`
- **运行时**：按每日计划“每晚”发布，手动发布官方patch/minor。
- **它的作用**：
  - 自动构建项目，更改版本号，并将包发布到 npm。
  - 在 GitHub 上创建相应的版本并生成发行说明。
- **你应该做什么**：
  - 作为贡献者，您无需为此过程执行任何操作。您可以放心，一旦您的 PR 合并到 `main` 分支中，您的更改将包含在下一个夜间版本中。

我们希望这个详细的概述对您有所帮助。如果您对我们的自动化或流程有任何疑问，请随时提问！


## 原文对照

<details>
<summary>展开完整原文</summary>

# Automation and Triage Processes

This document provides a detailed overview of the automated processes we use to manage and triage issues and pull requests. Our goal is to provide prompt feedback and ensure that contributions are reviewed and integrated efficiently. Understanding this automation will help you as a contributor know what to expect and how to best interact with our repository bots.

## Guiding Principle: Issues and Pull Requests

First and foremost, almost every Pull Request (PR) should be linked to a corresponding Issue. The issue describes the "what" and the "why" (the bug or feature), while the PR is the "how" (the implementation). This separation helps us track work, prioritize features, and maintain clear historical context. Our automation is built around this principle.

---

## Detailed Automation Workflows

Here is a breakdown of the specific automation workflows that run in our repository.

### 1. When you open an Issue: `Qwen Triage`

This is the first bot you will interact with when you create an issue. Its job is to perform an initial analysis and apply the correct labels.

- **Workflow File**: `.github/workflows/qwen-triage.yml`
- **When it runs**: Immediately after an issue is created, edited, or reopened, or when a maintainer manually requests triage.
- **What it does**:
  - It uses a Qwen model to analyze the issue's title and body against a detailed set of guidelines.
  - **Applies one `area/*` label**: Categorizes the issue into a functional area of the project (e.g., `area/ux`, `area/models`, `area/platform`).
  - **Applies one `kind/*` label**: Identifies the type of issue (e.g., `kind/bug`, `kind/enhancement`, `kind/question`).
  - **Applies one `priority/*` label**: Assigns a priority from P0 (critical) to P3 (low) based on the described impact.
  - **May apply `status/need-information`**: If the issue lacks critical details (like logs or reproduction steps), it will be flagged for more information.
  - **May apply `status/need-retesting`**: If the issue references a CLI version that is more than six versions old, it will be flagged for retesting on a current version.
- **What you should do**:
  - Fill out the issue template as completely as possible. The more detail you provide, the more accurate the triage will be.
  - If the `status/need-information` label is added, please provide the requested details in a comment.
  - Maintainers can comment `@qwen-code /triage` to run triage again.

### 2. When you open a Pull Request: `Continuous Integration (CI)`

This workflow ensures that all changes meet our quality standards before they can be merged.

- **Workflow File**: `.github/workflows/ci.yml`
- **When it runs**: On every push to a pull request.
- **What it does**:
  - **Lint**: Checks that your code adheres to our project's formatting and style rules.
  - **Test**: Runs our full suite of automated tests across macOS, Windows, and Linux, and on multiple Node.js versions. This is the most time-consuming part of the CI process.
  - **Post Coverage Comment**: After all tests have successfully passed, a bot will post a comment on your PR. This comment provides a summary of how well your changes are covered by tests.
- **What you should do**:
  - Ensure all CI checks pass. A green checkmark ✅ will appear next to your commit when everything is successful.
  - If a check fails (a red "X" ❌), click the "Details" link next to the failed check to view the logs, identify the problem, and push a fix.

### 3. Release Automation

This workflow handles the process of packaging and publishing new versions of Qwen Code.

- **Workflow File**: `.github/workflows/release.yml`
- **When it runs**: On a daily schedule for "nightly" releases, and manually for official patch/minor releases.
- **What it does**:
  - Automatically builds the project, bumps the version numbers, and publishes the packages to npm.
  - Creates a corresponding release on GitHub with generated release notes.
- **What you should do**:
  - As a contributor, you don't need to do anything for this process. You can be confident that once your PR is merged into the `main` branch, your changes will be included in the very next nightly release.

We hope this detailed overview is helpful. If you have any questions about our automation or processes, please don't hesitate to ask!


</details>
