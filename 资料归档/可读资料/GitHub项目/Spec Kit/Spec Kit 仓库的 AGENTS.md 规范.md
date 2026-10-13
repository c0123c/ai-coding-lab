# Spec Kit 仓库的 AGENTS.md 规范

规定为该仓库修改 CLI、MCP、Agent 集成和工作流时的测试与贡献要求；属于项目内部规范。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-10-06](<https://github.com/github/spec-kit/commit/4e1b814e61e078dd6b0c5f90e3755aaa62b4a14f>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/AGENTS.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 仓库的 AGENTS.md 规范.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# AGENTS.md

## 关于规格套件和指定

**GitHub 规范套件** 是一个用于实施规范驱动开发 (SDD) 的综合工具包 - 一种强调在实施之前创建清晰规范的方法。该工具包包括模板、脚本和工作流程，可指导开发团队通过结构化方法构建软件。

**指定 CLI** 是使用 Spec Kit 框架引导项目的命令行界面。它设置必要的目录结构、模板和 AI agent 集成以支持规范驱动开发工作流程。

该工具包支持多个 AI 编码助手，允许团队使用他们喜欢的工具，同时保持一致的项目结构和开发实践。

## 添加或更新 CLI 命令

在添加、更新或重新组织指定CLI命令之前，请阅读
[共享命令应用架构](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/shared.md>)和
[指定CLI命令架构](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/cli.md>)。它们定义共享
操作边界、命令模块命名、私有阶段、嵌套命令
组、注册所有权和镜像测试。

## 添加或更新 MCP 命令

在添加或更改指定命令的MCP工具之前，请阅读
[共享命令应用架构](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/shared.md>)和
[指定MCP命令架构](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/mcp.md>)。他们定义了共享的
操作边界、类型化合约、显式清单、副作用元数据、
本地stdio协议边界和镜像测试。

## 添加或更新 Agent 集成

在添加或更改 AI agent 集成之前，请阅读 
[Agent 集成设计 ](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/integration.md>)。它涵盖
交付路线、输出格式、注册和install/uninstall所有权。

## 添加或更新工作流程步骤

在添加或更改工作流程步骤类型之前，请阅读
[工作流程步骤设计](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/workflow-step.md>)。它涵盖注册、
验证、执行、恢复和安装的步骤包。

## 测试可执行行为

在更改在没有 
 和 LLM 的情况下运行或控制执行的代码或配置之前，请阅读
[测试确定性行为](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/CONTRIBUTING.md#testing-deterministic-behavior>)。
行为变化需要积极和消极的覆盖；错误修复需要
回归前后的证据。

## 分支机构和 Agent 贡献

创建分支时，请遵循 [ 分支命名 ](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/CONTRIBUTING.md#branch-naming>)。
在创作提交、打开 PR 或发布审阅评论之前，请阅读 
[Agent 创作的 Git 并审阅活动 ](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/CONTRIBUTING.md#agent-authored-git-and-review-activity>)。
Agent 创作的提交和AI-生成的 PR 和评论均需要其
自己披露；单独的 PR- 正文披露并不涵盖后续活动。

## 其他贡献指导

对于上面未涵盖的贡献或存储库工作流程问题，或者当
适用的指南不清楚时，请在采取行动之前阅读[CONTRIBUTING.md](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/CONTRIBUTING.md>)
。

## 常见陷阱

- **针对错误的环境运行测试：**运行其中的套件
worktree自己的virtualenv（`uv sync --extra test`然后
`.venv/bin/python -m pytest`）。裸露的 `uv run pytest` 可以从另一个工作树获取 
 可编辑安装，但无法导入新的子包。


## 原文对照

<details>
<summary>展开完整原文</summary>

# AGENTS.md

## About Spec Kit and Specify

**GitHub Spec Kit** is a comprehensive toolkit for implementing Spec-Driven Development (SDD) - a methodology that emphasizes creating clear specifications before implementation. The toolkit includes templates, scripts, and workflows that guide development teams through a structured approach to building software.

**Specify CLI** is the command-line interface that bootstraps projects with the Spec Kit framework. It sets up the necessary directory structures, templates, and AI agent integrations to support the Spec-Driven Development workflow.

The toolkit supports multiple AI coding assistants, allowing teams to use their preferred tools while maintaining consistent project structure and development practices.

## Adding or Updating CLI Commands

Before adding, updating, or reorganizing Specify CLI commands, read
[Shared Command Application Architecture](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/shared.md>) and
[Specify CLI Command Architecture](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/cli.md>). They define the shared
operation boundary, command-module naming, private phases, nested command
groups, registration ownership, and mirrored tests.

## Adding or Updating MCP Commands

Before adding or changing MCP tools for Specify commands, read
[Shared Command Application Architecture](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/shared.md>) and
[Specify MCP Command Architecture](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/mcp.md>). They define the shared
operation boundary, typed contracts, explicit inventory, side-effect metadata,
the local stdio protocol boundary, and mirrored tests.

## Adding or Updating Agent Integrations

Before adding or changing AI agent integrations, read
[Agent Integration Design](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/integration.md>). It covers
delivery routes, output formats, registration, and install/uninstall ownership.

## Adding or Updating Workflow Steps

Before adding or changing workflow step types, read
[Workflow Step Design](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/design/workflow-step.md>). It covers registration,
validation, execution, resume, and installed step packages.

## Testing Executable Behavior

Before changing code or configuration that runs or controls execution without
an LLM, read
[Testing deterministic behavior](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/CONTRIBUTING.md#testing-deterministic-behavior>).
Behavioral changes need positive and negative coverage; bug fixes need
before-and-after regression evidence.

## Branches and Agent Contributions

When creating a branch, follow [Branch naming](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/CONTRIBUTING.md#branch-naming>).
Before authoring commits, opening PRs, or posting review comments, read
[Agent-authored Git and review activity](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/CONTRIBUTING.md#agent-authored-git-and-review-activity>).
Agent-authored commits and AI-generated PRs and comments each require their
own disclosure; a PR-body disclosure alone does not cover later activity.

## Other Contribution Guidance

For contribution or repository-workflow questions not covered above, or when
the applicable guidance is unclear, read [CONTRIBUTING.md](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/CONTRIBUTING.md>)
before acting.

## Common Pitfalls

- **Running tests against the wrong environment:** Run the suite inside this
  worktree's own virtualenv (`uv sync --extra test` then
  `.venv/bin/python -m pytest`). A bare `uv run pytest` can pick up an
  editable install from another worktree and fail to import new subpackages.


</details>
