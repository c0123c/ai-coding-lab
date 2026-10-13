# Codex 沙箱完整性检查约定

规定检查范围、职责、适用后端和近似处理；正文说明发现仅用于遥测，属于内部实现审查规范。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 存档版本：`5ef96ab2785f`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/openai/codex/commit/c2eb1f42a0914bf7a204fccb54797c17badacd5a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/codex/blob/main/codex-rs/exec-server/src/sandbox_integrity/AGENTS.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex 沙箱完整性检查约定.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 沙箱完整性：范围和审查合同

这些检查目前仅发出遥测数据。调查结果和准备错误不得
更改沙箱构造、权限、批准或命令执行。
支持的后端包括 Seatbelt、bubblewrap 和 MXC；旧版 Windows 超出了范围。

## 职责

- 后端提供完整的依赖项清单并解释请求的
原始文件系统策略：解析命令临时目录和 MXC 卷、
 并重用后端的拒绝 glob 扩展（其中强制扩展 glob）。
- 运行程序准备依赖事实并针对每个依赖调用每个检查器。
检查器保持独立于库存和遥测。
 阻塞工作在阻塞池上运行，调用者超时为五秒。
 超时使用现有检查指标（`dependency=preparation`、`outcome=error`、
 `error_kind=timeout`）。逾期结果将被丢弃；取消阻塞任务
或其扫描器子进程被推迟。
- 遥测从策略、依赖事实和检查器中派生有界标签
结果。切勿发出路径、环境值或错误文本。
 缺少指标客户端会禁用发出，而不会检查本身。
 使用进程全局指标客户端；程序中单独注入的客户端
嵌入Codex超出了此阶段的范围。

## 刻意的近似

遵守基本的 read/write/deny 规则。请勿重建本机强制执行或
审核OS ACL。额外的后端保护可能会产生误报；
额外的写入授权（例如安全带刮擦访问）可能会产生误报
阴性。后端 glob/symlink 细微差别和可写硬链接别名未
完全建模。单独的全局扫描可能与强制执行的快照不同。
指标描述了观察到的检查器结果，而不是准确性或错过的违规行为。

MXC 扫描仪发现当前使用 `which`，而强制执行则使用 
`Command`。它们的搜索顺序和环境差异可能会选择不同的
可执行文件或导致仅一次扫描使用内部walker。这是此阶段可接受的
遥测限制；推迟更改生产扫描仪选择。

这些是此阶段可接受的限制，而不是重复后端
实现的请求。根据此范围进行审查。显式策略
处理、执行者上下文或报告中的回归仍然可操作；这不是误报或漏报的全面
豁免。

## 资格

在准备或
遥测之前跳过`SandboxOverride::EscalatedSandboxWithRestrictions`：这些批准的升级仅保留拒绝读取限制。
仍检查带有拒绝的普通配置的`:root = write`策略。
不要从策略形状推断批准。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Sandbox integrity: scope and review contract

These checks currently emit telemetry only. Findings and preparation errors must
not change sandbox construction, permissions, approvals, or command execution.
Supported backends are Seatbelt, bubblewrap, and MXC; legacy Windows is out of scope.

## Responsibilities

- Backends supply complete dependency inventories and interpret the request's
  original filesystem policy: resolve command temp directories and MXC volumes,
  and reuse the backend's deny-glob expansion where enforcement expands globs.
- The runner prepares dependency facts and invokes each checker per dependency.
  Checkers remain independent of inventories and telemetry.
  Blocking work runs on the blocking pool with a five-second caller timeout.
  Timeouts use the existing check metric (`dependency=preparation`, `outcome=error`,
  `error_kind=timeout`). Late results are discarded; cancelling the blocking task
  or its scanner subprocess is deferred.
- Telemetry derives bounded tags from the policy, dependency facts, and checker
  results. Never emit paths, environment values, or error text.
  A missing metrics client disables emission, never the checks themselves.
  Use the process-global metrics client; separately injected clients in programs
  embedding Codex are outside this phase's scope.

## Deliberate approximation

Honor the basic read/write/deny rules. Do not reconstruct native enforcement or
audit OS ACLs. Additional backend protections can produce false positives;
additional write grants (such as Seatbelt scratch access) can produce false
negatives. Backend glob/symlink nuances and writable hard-link aliases are not
fully modeled. The separate glob scan can differ from enforcement's snapshot.
Metrics describe observed checker outcomes, not accuracy or missed violations.

MXC scanner discovery currently uses `which`, while enforcement uses
`Command`. Their search-order and environment differences can select different
executables or cause only one scan to use the internal walker. This is an accepted
telemetry limitation for this phase; changing production scanner selection is deferred.

These are accepted limitations of this phase, not requests to duplicate backend
implementations. Review against this scope. Regressions in explicit policy
handling, executor context, or reporting remain actionable; this is not a blanket
exemption for false positives or false negatives.

## Eligibility

Skip `SandboxOverride::EscalatedSandboxWithRestrictions` before preparation or
telemetry: these approved escalations retain only deny-read restrictions.
An ordinary configured `:root = write` policy with denials is still checked.
Do not infer approval from policy shape.


</details>
