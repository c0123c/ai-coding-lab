# 该仓库的 CLAUDE.md 规范

记录为此仓库贡献代码时的操作约定，重点包含 GitHub Actions 的安全限制；属于项目内部规范样例。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2026-09-29](<https://github.com/anthropics/claude-code/commit/7dbaea237fca9aed7baf250390ec4d49a57a2211>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/CLAUDE.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/该仓库的 CLAUDE.md 规范.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# CLAUDE.md

在此存储库中工作的 Claude Code 和其他编码 agents 的指南。

## GitHub 操作的安全强化

此存储库中调用 Claude 的工作流作业以三种保护方式运行。
在添加或编辑工作流时保留它们。

1. **出口防火墙运行程序。** 该作业有 `runs-on: ubuntu-24.04-firewall`，
GitHub 托管的运行器，用于过滤作业的出站网络流量。不要
将调用Claude的作业移动到另一个运行器。
2. **网络允许列表。** `.github/egress-firewall.yaml` 列出主机
除了 GitHub 的防火墙默认允许的任何作业之外，这些作业可能会到达。
 保留 `mode: enforce`，这就是防火墙阻止其余作业的原因。添加主机时遵循 
 该文件的标头。
3. **自动权限模式。**运行Claude Code操作的每个步骤
（`uses: anthropics/claude-code-action`，或具有 
 `claude_args` 输入的本地操作）在 `claude_args` 中传递 `--permission-mode auto`。仅当 Claude Code 的安全审查通过时，需要许可且允许的工具未涵盖的工具 
 调用才会运行 
。仅允许作业 
 需要的工具，并保留步骤拥有的任何 `--disallowedTools` 列表。使用 `claude-opus-4-6`
 或较新的型号：在较旧的 Claude Code 上，回退到其默认的 
 权限模式。

`claude.yml` 答案 `@claude` 提到。的 Claude Code 动作组
`--permission-mode acceptEdits` 对于那些，以及 `--permission-mode auto` 在
工作流程的 `claude_args`，它紧随其后，取代它。

当运行 Claude
Code 操作或提及 `ANTHROPIC_FEDERATION_RULE_ID` 的作业破坏保护 1 或 3、
 时，或者当允许列表缺失、为空而不是 `mode: enforce` 或指定一个名称时，`.github/workflows/workflow-hardening.yml` 将失败主机
与`*`。它无法看到以另一种方式调用 Claude 的作业，因此也手动检查新的 
 工作流程。如果作业无法满足保护1或3，请将其与
原因一起添加到
`.github/scripts/check_workflow_hardening.py`中的匹配豁免表中。 `EXEMPT_FROM_AUTO_MODE`
 中的作业必须完全设置无权限模式。不要跳过或削弱检查。

保持每个工作流的 `permissions:` 块最小化，并且永远不要在工作流日志中打印令牌或 
 环境变量。


## 原文对照

<details>
<summary>展开完整原文</summary>

# CLAUDE.md

Guidance for Claude Code and other coding agents working in this repository.

## Security hardening for GitHub Actions

Workflow jobs in this repository that call Claude run with three protections.
Keep them when you add or edit a workflow.

1. **Egress-firewall runner.** The job has `runs-on: ubuntu-24.04-firewall`,
   a GitHub-hosted runner that filters the job's outbound network traffic. Do
   not move a job that calls Claude to another runner.
2. **Network allow list.** `.github/egress-firewall.yaml` lists the hosts
   those jobs may reach, besides any that GitHub's firewall allows by default.
   Keep `mode: enforce`, which is what makes the firewall block the rest. Follow
   that file's header when you add a host.
3. **Auto permission mode.** Every step that runs the Claude Code action
   (`uses: anthropics/claude-code-action`, or a local action with a
   `claude_args` input) passes `--permission-mode auto` in `claude_args`. A tool
   call that needs permission and that the allowed tools do not cover then runs
   only if Claude Code's safety review passes it. Allow only the tools the job
   needs, and keep any `--disallowedTools` list a step has. Use `claude-opus-4-6`
   or a newer model: on an older one Claude Code falls back to its default
   permission mode.

`claude.yml` answers `@claude` mentions. The Claude Code action sets
`--permission-mode acceptEdits` for those, and the `--permission-mode auto` in
the workflow's `claude_args`, which comes after it, replaces it.

`.github/workflows/workflow-hardening.yml` fails when a job that runs the Claude
Code action or mentions `ANTHROPIC_FEDERATION_RULE_ID` breaks protection 1 or 3,
or when the allow list is missing, empty, not `mode: enforce`, or names a host
with `*`. It cannot see a job that calls Claude another way, so check new
workflows by hand too. If a job cannot meet protection 1 or 3, add it with the
reason to the matching exemption table in
`.github/scripts/check_workflow_hardening.py`. A job in `EXEMPT_FROM_AUTO_MODE`
must set no permission mode at all. Do not skip or weaken the check.

Keep each workflow's `permissions:` block minimal, and never print tokens or
environment variables in workflow logs.


</details>
