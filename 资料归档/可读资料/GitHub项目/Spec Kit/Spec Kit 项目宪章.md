# Spec Kit 项目宪章

列出该仓库的代码质量、测试、CLI 一致性、离线性能、依赖和安全文件操作原则；属于项目治理规范。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-06-19](<https://github.com/github/spec-kit/commit/487af97864901462874f18f1c7f8d8adec0b7ddd>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/.specify/memory/constitution.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 项目宪章.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

<!--
SYNC IMPACT REPORT
==================
版本变更：(template/unratified) → 1.0.0
碰撞理由：初步批准具体宪法Brownfield
 规范套件/specify-cli 代码库，源自对 
 源树、测试套件、CI 管道和项目约定的详尽多遍分析（AGENTS.md、
 CONTRIBUTING.md、 DEVELOPMENT.md）。 MAJOR 基线，因为它建立了以前不存在的绑定
 治理。

定义的原则：
 I. 代码质量和架构纪律
 II。  测试支持的变更 (NON-NEGOTIABLE)
 III。 CLI 和用户体验一致性
 IV。  离线优先性能和资源规则
 V. 最小依赖性和安全、幂等文件操作

添加的部分：
  - 安全性和跨平台限制
  - 开发工作流程和质量门
  - 治理

模板已审核对齐：
 ✅ .specify/templates/plan-template.md — 通用“宪法检查”门（行 39）
 仍然有效；现在，在计划时，门已具体填充了原则 I-V。
 ✅ .specify/templates/spec-template.md — 没有宪法特定的标记；无需更改。
 ✅ .specify/templates/tasks-template.md — 任务类别 (setup/foundational/story/polish)
 已适应此处强制执行的测试 + 性能 + UX 任务；无需更改。
 ✅ .github/agents/speckit.*.agent.md — 命令指导与 agent 无关；无需更改。

后续 TODO：无。 RATIFICATION_DATE 设置为下面的首次采用日期。
-->

# 规格套件构成

Spec Kit（`specify-cli` 包及其捆绑资产）是一个本地、离线功能的 
 开发人员 CLI，它引导和操作 AI 编码 
agents 的规范驱动开发工作流程。这些原则源自代码库已经强制执行的模式。它们
绑定所有更改 - 包括`specify bundle`子命令和任何未来的
命令组、集成、扩展、预设或工作流程。

## 核心原则

### I. 代码质量和架构纪律

代码库遵循严格的、注册表驱动的分层架构，所有更改 MUST
 都会保留它。

- **将 CLI 表面与可导入逻辑分开。** Typer 中存在面向用户的命令
子应用程序（例如 `commands/`、`*/_commands.py`）；业务逻辑存在于简单的、可导入的
模块中，没有`@app.command()`装饰器。新功能 MUST 使编排逻辑 
 可以独立于 Typer 进行测试。
- **使用已建立的扩展模式。**新建agents/integrationsMUST子类之一
标准基类（`MarkdownIntegration`、`TomlIntegration`、`YamlIntegration`、
 `SkillsIntegration`）并声明所需的类属性（`key`、`config`、
 `registrar_config` 和 `context_file`（如适用）。仅当没有基类适合且偏差MUST合理时才允许直接扩展`IntegrationBase`
。
- **尊重单一事实来源。**内置程序通过相关注册表连接
（例如 `INTEGRATION_REGISTRY` 通过 `_register_builtins()`），导入和注册 
 按字母顺序保存。重复的键 MUST 会大声失败，而不是默默地覆盖。
- **命名和键入不是可选的。**私有modules/functions是`_`前缀和MUST
NOT 跨包边界导入。每个新模块都以
 `from __future__ import annotations`开头，并使用现代类型语法（`dict[str, Any]`，
 `str | None`）；旧版 `Dict`/`List`/`Optional` 表单被拒绝。
- **包目录使用下划线；键保持其规范（通常用连字符连接）形式**
（例如包 `kiro_cli/`、`key = "kiro-cli"`）。对于 CLI- 支持的集成，`key` MUST
 与可执行文件名称匹配，因此 `shutil.which(key)` 可以解析。

**基本原理：**注册表加基类架构允许数十个集成、
扩展和工作流以最小的耦合共存。这里的漂移使维护
成本成倍增加，并打破了“添加一个子类，注册一次，发布测试”的合同。

### II。测试支持的变更 (NON-NEGOTIABLE)

每个行为改变MUST都伴随着自动化测试，并且该套件是一个硬门。

- **测试门合并。** CI 在 ubuntu + windows × Python 3.11 矩阵上运行 `pytest`，
3.12和3.13。更改 MUST 传递到该矩阵的每个单元格。
- **奇偶校验不变量 MUST 保持。** 每个集成 MUST 出现在注册表中，都有一个
`CommandRegistrar` 配置条目（在需要时），并提供专用的 
 `tests/integrations/test_integration_<key>.py`（密钥中的连字符成为 
 文件名中的下划线）。这些是通过参数化测试（例如`test_registry.py`）强制执行的，并且MUST NOT
被削弱。
- **遵循 pytest 约定。** 测试 modules/classes/functions 使用 `test_*` / `Test*`
命名项目配置，在 `--strict-markers` 下运行，并使用 
、`tmp_path`、`monkeypatch` 和自动使用身份验证隔离装置隔离状态。特定于平台的测试
 MUST受到保护（例如`@requires_bash`）而不是失败。
- **安全和幂等测试是强制性类别。**路径遍历拒绝，
现有的
套件涵盖了清单哈希integrity/symlink安全性和无覆盖幂等性；涉及文件写入、路径处理或设置脚本的更改MUST扩展（绝不
减少）该覆盖范围。
- **网络被模拟。**没有测试可以进行真正的出站网络呼叫； HTTP MUST 被截断
因此该套件是确定性的并且可以离线运行。

**理由：**受支持的agents和offline/air-gapped保证的广度只能
通过详尽的参数化测试来维持。奇偶校验和安全套件可以阻​​止 
 单个新集成使整个矩阵回归。

### III。 CLI 和用户体验一致性

CLI 呈现出一个相干表面；每个指挥组 MUST 都感觉和其他组一样。

- **重用共享动词词汇。**面向消费者的群体使用已建立的动词 —
`list`、`add`/`install`、`remove`、`search`、`info`、`update`，加上`enable`/`disable`和
 `set-priority`（如果相关）。新动词 MUST NOT 在现有动词适合时被发明，并且 
 任何真正的新动词 MUST 都被证明是合理的。
- **镜像目录堆栈模型。**目录支持的组MUST公开
`<group> catalog list|add|remove`，使用按优先级排序的源堆栈（较低的数字 
 = 较高的优先级）加上每个源的安装策略（`install-allowed` 与 `discovery-only`）、
 来支持它，并在不存在项目配置时回退到内置默认堆栈。
- **以标准方式注册子应用程序。**命令组是`typer.Typer(...)`实例
通过 `app.add_typer(child, name="...")` 连接，最好通过 `__init__.py` 中导入的模块化 
 `register(app)` 函数连接。嵌套 MUST 保持在 ~2–3 级别内。
- **输出一致且对机器友好。**人类输出使用共享的Rich
约定（例如，`[green]✓[/green]` 成功、`[red]Error:[/red]` + 
 失败时非零退出、消息中的可操作补救措施）。在提供 `--json` 标志的情况下，有效的 JSON
 将转到 stdout，所有其他日志记录将重定向到 stderr。
- **交互是安全且幂等的。**破坏性行为显示之前会发生什么变化
确认； “已安装/已存在”结果成功（退出0）而不是
错误。面向用户的命令组 MUST 记录在 `docs/reference/` 下。

**基本原理：**可预测性是产品。用户学习一组动词、一个目录模型 
 和一种输出语法，然后将它们应用到每个组 - 包括 `specify bundle`。

### IV。离线优先的性能和资源规则

规格套件是本地的 CLI；响应能力、离线可操作性和优雅降级是
性能契约。

- **`specify init` 和核心脚手架 MUST 使用捆绑 `core_pack` 完全离线工作 **
资产。资产解析 MUST 更喜欢捆绑资产，然后在 
 到达网络之前进行源检查。
- **网络使用是惰性的、有限的且可降级的。**网络调用仅发生在显式的情况下
用户命令，MUST 设置超时，MUST 缓存目录结果（1 小时 TTL）并在失败时回退到 
 陈旧缓存，并且 MUST 表面 offline/rate-limit 条件明确消息
而不会崩溃。
- **保持启动成本低廉。**避免在导入时间中添加重量级工作。新可选
子系统 SHOULD 更喜欢延迟加载而不是无条件急切导入，以便不相关的 
 命令（包括 `--help`）保持快速。
- **文件系统写入量最小且幂等。** 安装 MUST 轨道文件 (SHA-256
清单），避免破坏用户修改的内容，仅卸载散列仍然
匹配的文件，并且永远不要遵循项目根目录之外的符号链接。

**基本原理：**开发人员在气隙、企业和片状网络
环境中运行此工具。离线优先行为和幂等、哈希跟踪文件操作
使其能够安全、快速地重复运行。

### V. 最小依赖性和安全、幂等文件操作

该项目有意保护其依赖面及其磁盘占用空间。

- **默认情况下零新的运行时依赖项。**运行时依赖项设置是故意的
小并固定到最低主要版本。添加依赖项需要维护者
协议，并证明现有的deps（typer、click、rich、pyyaml、packaging、
 platformdirs、pathspec、json5、readchar）无法满足需求。新子系统 SHOULD 重用 
 现有的原始机器，而不是重新实现或重新运输它。
- **所有路径都经过验证。**从user/manifest/catalog派生的任何项目相对路径
输入 MUST 仅限于项目根（`Path.relative_to` 检查）并拒绝遍历 
 有效负载；符号链接转义 MUST 被拒绝。
- **错误是显式的和连锁的。**预先验证输入，通过可操作的上下文提出
（冒犯field/value加上提示），并使用`raise ... from exc`来保留原因。 I/O
 可以合法地失败 MUST 优雅地降级而不是发出原始回溯。
- **版本控制遵循SemVer。**用户可见和打包的行为更改遵循
MAJOR.MINOR.PATCH 语义；向后不兼容的更改 MUST 被调用并证明其合理性。

**基本原理：**精益、固定的依赖关系集和强化的幂等文件处理是
保持该工具在企业和气隙环境中值得信赖并且维护成本低廉的原因。

## 安全性和跨平台限制

- **需要跨平台奇偶校验。**代码MUST在Linux、macOS和Windows上运行
Python 3.11–3.13。 Windows 细节（UTF-8 流重新配置、bash 相关测试 
 自动跳过）MUST 受到尊重；不要仅引入POSIX-假设，而没有受保护的
后备。
- **安全工具是一个大门。** CodeQL 以及项目的安全测试套件
（路径遍历，manifest/symlink 强化）MUST 保持绿色。网络访问 MUST 默认 
 在测试中关闭，并在运行时选择加入、超时限制和凭据隔离。
- **强制执行格式。** `.editorconfig` 规则（LF 结尾、最后换行符、无尾随
空格、4-空格 Python / 2-空格 YAML-JSON-Markdown)、`ruff check src/` 和 
 `markdownlint-cli2` MUST 传递。

## 开发工作流程和质量门

- **分支命名**遵循`<type>/<number>-<short-slug>`（或`<type>/<short-slug>`，没有
问题），其中 `<type>` ∈ {feat, fix, docs, community, chore}。
- **PR 重点关注 ** 和 MUST：通过 `ruff`、`pytest`（完整矩阵）、markdown lint 和 CodeQL；
add/extend 测试新行为；当行为发生变化时更新面向用户的文档（`README.md`、`docs/`、
 `spec-driven.md`）；并披露所使用的任何AI帮助。
- **影响斜线命令的更改** MUST通过编码agent和
结果报告在 PR 中，根据 CONTRIBUTING.md。
- **大型或跨领域更改**（新模板、参数、命令组）MUST达成一致
在实施之前与维护者一起。

## 治理

当它们发生冲突时，本宪法取代临时公约；它所编纂的现有代码库
模式仍然是权威参考。

- **权威。**原则 I-V 是绑定门。的 `## Constitution Check` 部分
计划模板 MUST 根据这些原则进行评估，并且 `/speckit.analyze` 将 
 与 MUST 的冲突视为 CRITICAL。违规行为可以通过更改规范、计划或
任务来解决，而不是通过淡化原则。
- **修正案。**对此文档的更改需要PR并提供理由、维护者批准，
以及根据以下政策进行版本更新。任何修改 MUST 都会传播到同一更改中的依赖模板 
 和命令指南，记录在该文件 
 顶部的同步影响报告中。
- **版本控制策略（SemVer 用于治理）。** MAJOR = 向后不兼容的治理或
原理removal/redefinition； MINOR = 新的 principle/section 或实质性扩展的 
 指导； PATCH = 澄清和非语义细化。
- **合规审查。**每次PR并审查MUST验证是否符合这些原则。
增加的复杂性或任何偏差 MUST 在 -PR 中进行合理化（对于计划，在计划的 
 复杂性跟踪部分中）。不合理的违规行为会阻止合并。

**版本**：1.0.0|**已批准**：2026-06-19 | **最后修改**：2026-06-19


## 原文对照

<details>
<summary>展开完整原文</summary>

<!--
SYNC IMPACT REPORT
==================
Version change: (template/unratified) → 1.0.0
Bump rationale: Initial ratification of a concrete constitution for the brownfield
  Spec Kit / specify-cli codebase, derived from an exhaustive multi-pass analysis of
  the source tree, test suite, CI pipelines, and project conventions (AGENTS.md,
  CONTRIBUTING.md, DEVELOPMENT.md). MAJOR baseline because it establishes binding
  governance where none previously existed.

Principles defined:
  I.   Code Quality & Architectural Discipline
  II.  Test-Backed Change (NON-NEGOTIABLE)
  III. CLI & User-Experience Consistency
  IV.  Offline-First Performance & Resource Discipline
  V.   Minimal Dependencies & Safe, Idempotent File Operations

Added sections:
  - Security & Cross-Platform Constraints
  - Development Workflow & Quality Gates
  - Governance

Templates reviewed for alignment:
  ✅ .specify/templates/plan-template.md — generic "Constitution Check" gate (line 39)
       remains valid; gates are now concretely populated by Principles I–V at plan time.
  ✅ .specify/templates/spec-template.md — no constitution-specific tokens; no change needed.
  ✅ .specify/templates/tasks-template.md — task categories (setup/foundational/story/polish)
       already accommodate testing + performance + UX tasks mandated here; no change needed.
  ✅ .github/agents/speckit.*.agent.md — command guidance is agent-agnostic; no change needed.

Follow-up TODOs: none. RATIFICATION_DATE set to first adoption date below.
-->

# Spec Kit Constitution

Spec Kit (the `specify-cli` package and its bundled assets) is a local, offline-capable
developer CLI that bootstraps and operates Spec-Driven Development workflows for AI coding
agents. These principles are derived from the patterns the codebase already enforces. They
are binding on all changes — including the `specify bundle` subcommand and any future
command group, integration, extension, preset, or workflow.

## Core Principles

### I. Code Quality & Architectural Discipline

The codebase follows a strict, registry-driven, layered architecture, and all changes MUST
preserve it.

- **Separate the CLI surface from importable logic.** User-facing commands live in Typer
  sub-apps (e.g. `commands/`, `*/_commands.py`); business logic lives in plain, importable
  modules with no `@app.command()` decorators. New features MUST keep orchestration logic
  testable independently of Typer.
- **Use the established extension pattern.** New agents/integrations MUST subclass one of the
  standard base classes (`MarkdownIntegration`, `TomlIntegration`, `YamlIntegration`,
  `SkillsIntegration`) and declare the required class attributes (`key`, `config`,
  `registrar_config`, and `context_file` where applicable). Extending `IntegrationBase`
  directly is permitted only when no base class fits, and the deviation MUST be justified.
- **Honor the single source of truth.** Built-ins are wired through the relevant registry
  (e.g. `INTEGRATION_REGISTRY` via `_register_builtins()`), with imports and registrations
  kept in alphabetical order. Duplicate keys MUST fail loudly rather than silently override.
- **Naming and typing are not optional.** Private modules/functions are `_`-prefixed and MUST
  NOT be imported across package boundaries. Every new module begins with
  `from __future__ import annotations` and uses modern type syntax (`dict[str, Any]`,
  `str | None`); legacy `Dict`/`List`/`Optional` forms are rejected.
- **Package directories use underscores; keys keep their canonical (often hyphenated) form**
  (e.g. package `kiro_cli/`, `key = "kiro-cli"`). For CLI-backed integrations the `key` MUST
  match the executable name so `shutil.which(key)` resolves.

**Rationale:** A registry-plus-base-class architecture is what lets dozens of integrations,
extensions, and workflows coexist with minimal coupling. Drift here multiplies maintenance
cost and breaks the "add one subclass, register once, ship a test" contract.

### II. Test-Backed Change (NON-NEGOTIABLE)

Every behavioral change MUST be accompanied by automated tests, and the suite is a hard gate.

- **Tests gate merges.** CI runs `pytest` across a matrix of ubuntu + windows × Python 3.11,
  3.12, and 3.13. Changes MUST pass on every cell of that matrix.
- **Parity invariants MUST hold.** Every integration MUST be present in the registry, have a
  `CommandRegistrar` config entry where required, and ship a dedicated
  `tests/integrations/test_integration_<key>.py` (hyphens in the key become underscores in the
  filename). These are enforced by parametrized tests (e.g. `test_registry.py`) and MUST NOT
  be weakened.
- **Follow pytest conventions.** Test modules/classes/functions use the `test_*` / `Test*`
  naming the project configures, run under `--strict-markers`, and isolate state with
  `tmp_path`, `monkeypatch`, and the autouse auth-isolation fixture. Platform-specific tests
  MUST be guarded (e.g. `@requires_bash`) rather than left to fail.
- **Security and idempotency tests are mandatory categories.** Path-traversal rejection,
  manifest hash integrity/symlink safety, and no-overwrite idempotency are covered by existing
  suites; changes touching file writes, path handling, or setup scripts MUST extend (never
  reduce) that coverage.
- **Network is mocked.** No test may make a real outbound network call; HTTP MUST be stubbed
  so the suite is deterministic and offline-runnable.

**Rationale:** The breadth of supported agents and the offline/air-gapped guarantees can only
be sustained by exhaustive, parametrized tests. The parity and security suites are what stop a
single new integration from regressing the whole matrix.

### III. CLI & User-Experience Consistency

The CLI presents one coherent surface; every command group MUST feel like the others.

- **Reuse the shared verb vocabulary.** Consumer-facing groups use the established verbs —
  `list`, `add`/`install`, `remove`, `search`, `info`, `update`, plus `enable`/`disable` and
  `set-priority` where relevant. New verbs MUST NOT be invented when an existing one fits, and
  any genuinely new verb MUST be justified.
- **Mirror the catalog-stack model.** Catalog-backed groups MUST expose
  `<group> catalog list|add|remove`, back it with a priority-ordered source stack (lower number
  = higher precedence) plus per-source install policy (`install-allowed` vs `discovery-only`),
  and fall back to a built-in default stack when no project config is present.
- **Register sub-apps the standard way.** Command groups are `typer.Typer(...)` instances
  attached via `app.add_typer(child, name="...")`, preferably through a modular
  `register(app)` function imported in `__init__.py`. Nesting MUST stay within ~2–3 levels.
- **Output is consistent and machine-friendly.** Human output uses the shared Rich
  conventions (e.g. `[green]✓[/green]` success, `[red]Error:[/red]` + non-zero exit on
  failure, actionable remediation in messages). Where a `--json` flag is offered, valid JSON
  goes to stdout and all other logging is redirected to stderr.
- **Interactions are safe and idempotent.** Destructive actions show what will change before
  confirming; "already installed / already present" outcomes succeed (exit 0) rather than
  error. User-facing command groups MUST be documented under `docs/reference/`.

**Rationale:** Predictability is the product. Users learn one set of verbs, one catalog model,
and one output grammar, then apply them to every group — including `specify bundle`.

### IV. Offline-First Performance & Resource Discipline

Spec Kit is a local CLI; responsiveness, offline operability, and graceful degradation are the
performance contract.

- **`specify init` and core scaffolding MUST work fully offline** using bundled `core_pack`
  assets. Asset resolution MUST prefer bundled assets, then a source checkout, before ever
  reaching the network.
- **Network use is lazy, bounded, and degradable.** Network calls happen only on explicit
  user commands, MUST set timeouts, MUST cache catalog results (1-hour TTL) and fall back to
  stale cache on failure, and MUST surface offline/rate-limit conditions as clear messages
  without crashing.
- **Keep startup cheap.** Avoid adding heavyweight work to import time. New optional
  subsystems SHOULD prefer lazy loading over unconditional eager imports so that unrelated
  commands (including `--help`) stay fast.
- **Filesystem writes are minimal and idempotent.** Installs MUST track files (SHA-256
  manifests), avoid clobbering user-modified content, only uninstall files whose hash still
  matches, and never follow symlinks out of the project root.

**Rationale:** Developers run this tool in air-gapped, enterprise, and flaky-network
environments. Offline-first behavior and idempotent, hash-tracked file operations are what
make it safe and fast to run repeatedly.

### V. Minimal Dependencies & Safe, Idempotent File Operations

The project guards its dependency surface and its on-disk footprint deliberately.

- **Zero new runtime dependencies by default.** The runtime dependency set is intentionally
  small and pinned to a minimum major version. Adding a dependency requires maintainer
  agreement and a justification that existing deps (typer, click, rich, pyyaml, packaging,
  platformdirs, pathspec, json5, readchar) cannot serve the need. New subsystems SHOULD reuse
  existing primitive machinery in-process rather than re-implementing or re-shipping it.
- **All paths are validated.** Any project-relative path derived from user/manifest/catalog
  input MUST be confined to the project root (`Path.relative_to` checks) and reject traversal
  payloads; symlink escapes MUST be refused.
- **Errors are explicit and chained.** Validate inputs up front, raise with actionable context
  (offending field/value plus a hint), and use `raise ... from exc` to preserve causes. I/O
  that can legitimately fail MUST degrade gracefully rather than emit a raw traceback.
- **Versioning follows SemVer.** User-visible and packaged behavior changes follow
  MAJOR.MINOR.PATCH semantics; backward-incompatible changes MUST be called out and justified.

**Rationale:** A lean, pinned dependency set and hardened, idempotent file handling are what
keep the tool trustworthy in enterprise and air-gapped contexts and cheap to maintain.

## Security & Cross-Platform Constraints

- **Cross-platform parity is required.** Code MUST run on Linux, macOS, and Windows and on
  Python 3.11–3.13. Windows specifics (UTF-8 stream reconfiguration, bash-dependent tests
  auto-skipping) MUST be respected; do not introduce POSIX-only assumptions without a guarded
  fallback.
- **Security tooling is a gate.** CodeQL and the project's security test suites
  (path-traversal, manifest/symlink hardening) MUST remain green. Network access MUST default
  to off in tests and be opt-in, timeout-bounded, and credential-isolated at runtime.
- **Formatting is enforced.** `.editorconfig` rules (LF endings, final newline, no trailing
  whitespace, 4-space Python / 2-space YAML-JSON-Markdown), `ruff check src/`, and
  `markdownlint-cli2` MUST pass.

## Development Workflow & Quality Gates

- **Branch naming** follows `<type>/<number>-<short-slug>` (or `<type>/<short-slug>` with no
  issue), with `<type>` ∈ {feat, fix, docs, community, chore}.
- **PRs are focused** and MUST: pass `ruff`, `pytest` (full matrix), markdown lint, and CodeQL;
  add/extend tests for new behavior; update user-facing docs (`README.md`, `docs/`,
  `spec-driven.md`) when behavior changes; and disclose any AI assistance used.
- **Slash-command-affecting changes** MUST be manually exercised through a coding agent and the
  results reported in the PR, per CONTRIBUTING.md.
- **Large or cross-cutting changes** (new templates, arguments, command groups) MUST be agreed
  with maintainers before implementation.

## Governance

This constitution supersedes ad-hoc convention where they conflict; the existing codebase
patterns it codifies remain authoritative references.

- **Authority.** Principles I–V are binding gates. The `## Constitution Check` section of the
  plan template MUST be evaluated against these principles, and `/speckit.analyze` treats
  conflicts with a MUST as CRITICAL. Violations are resolved by changing the spec, plan, or
  tasks — not by diluting a principle.
- **Amendments.** Changes to this document require a PR with rationale, maintainer approval,
  and a version bump per the policy below. Any amendment MUST propagate to dependent templates
  and command guidance in the same change, recorded in the Sync Impact Report at the top of
  this file.
- **Versioning policy (SemVer for governance).** MAJOR = backward-incompatible governance or
  principle removal/redefinition; MINOR = a new principle/section or materially expanded
  guidance; PATCH = clarifications and non-semantic refinements.
- **Compliance review.** Every PR and review MUST verify compliance with these principles.
  Added complexity or any deviation MUST be justified in-PR (and, for plans, in the plan's
  Complexity Tracking section). Unjustified violations block merge.

**Version**: 1.0.0 | **Ratified**: 2026-06-19 | **Last Amended**: 2026-06-19


</details>
