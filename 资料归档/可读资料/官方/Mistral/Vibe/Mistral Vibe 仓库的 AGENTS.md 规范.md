# Mistral Vibe 仓库的 AGENTS.md 规范

记录架构、工具链、测试、目录约定及 Python 和 Rust 终端界面开发要求；属于该项目内部规范。

- 来源：Mistral／Vibe
- 资料类型：存档文档
- 存档版本：`4ae5c5955da8`。
- 文档时间：源码提交（存档版）：[2026-10-09](<https://github.com/mistralai/mistral-vibe/commit/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/mistralai/mistral-vibe/blob/main/AGENTS.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Mistral/Vibe/Mistral Vibe 仓库的 AGENTS.md 规范.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# AGENTS.md

AI agents 和为 **Mistral Vibe** 做出贡献的人类的约定 — 由 `uv` 管理的 Python 3.12+ CLI 编码助手。

布局：`vibe/core` 是引擎（agent 循环、工具、LLM 后端、配置）； `vibe/cli` 是旧版 Python（文本）TUI； `vibe/cli-rust` 是将取代它的 Rust (Ratatui) TUI，是 `vibe-app-server` 之上的瘦客户端； `vibe/acp` 桥接到 Agent 客户端协议； `vibe/setup` 运行首次运行向导。测试在 `tests/` 中进行，自动使用装置在 `conftest.py` 中，测试替身在 `tests/stubs/` 中。

## 架构决策

在影响架构的更改之前，请阅读匹配的 ADR。如果更改适合当前代码但与 ADR 方向冲突，请在实施之前将其标记给用户。创建或编辑ADR时，请遵循`write-vibe-adr`技能。

创建或编辑ADR时，请遵循`write-vibe-adr`技巧并保留标准部分。

|变更区域| ADR |
| --- | --- |
|架构原理、模块边界、startup/runtime速度、简单改动|[0001 架构原理](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0001-architecture-principles.md>)|
|核心引擎、文本 CLI、ACP、设置或编程界面|[0002 核心发动机和交付面](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0002-core-engine-and-delivery-surfaces.md>)|
|Agent 循环编排、流式传输、类型化事件、取消或响应能力|[0003 事件驱动 Agent 循环](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0003-event-driven-agent-loop.md>)|
|工具合约、权限、工具输出、UI 元数据或工具适配器|[0004 类型化许可工具](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0004-typed-permissioned-tools.md>)|
|配置模型、分层、默认值、迁移、重新加载或运行时覆盖|[0005 分层配置](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0005-layered-configuration.md>)|
|会话日志记录、恢复、倒回、转录元数据或迁移|[0006 本地会话](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0006-local-sessions.md>)|
|技能、agents、子代理、挂钩、MCP、连接器、自定义工具或发现|[0007 延伸机构](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0007-extension-mechanisms.md>)|
|添加或更改分析仪器、遥测事件或事件属性|[0008 功能仪表](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0008-feature-instrumentation.md>)|
|应用程序服务器所有权、RPC、生命周期、预测、效果、回调、客户端工具或交付适配器|[0009 应用程序服务器边界](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0009-app-server-boundary.md>)|
|小部件中的文本 `Content` 渲染、样式文本、标记解析或主题变量|[0010 文本内容渲染](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0010-textual-content-rendering.md>)|
|应用程序服务器会话后端接口、适配器或运行时所有权|[0011 应用程序服务器会话后端](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0011-unified-harness-backend.md>)|
|忙时运行的斜线命令、旁路命令或仅空闲设置持久性|[0012 忙时斜线命令](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0012-two-phase-slash-command-execution.md>)|
|排队提示 selection/edit 模式、写入时复制消耗的编辑或升级竞赛小部件身份跟踪|[0013 队列选择和编辑模式](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0013-queue-edit-mode.md>)|
|解析后端响应、connector/MCP 引导字段或任何必须容忍自托管版本偏差的 client/backend 线路合同更改|[0014 后端合约兼容性](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0014-backend-contract-compatibility.md>)|
|出站 TLS 连接、证书根或 `enable_system_trust_store`|[0015出站TLS信任策略](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0015-outbound-tls-trust-policy.md>)|
|Rust CLI 交付表面、瘦客户端边界、单向状态或有限资源规则|[0016 Rust CLI 交货面](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0016-rust-cli-delivery-surface.md>)|
|旁白播放、TTS、音频输出设备跟随或`AudioPlayer`/`DeviceFollower`/`NarrationController`类别|[0017 ACP子进程](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0017-narration-playback-in-subprocess.md>)中的旁白播放|

## 终端UI变更

Python TUI 仍然是生产中的默认值，但已被弃用，取而代之的是 Rust TUI。对 Python TUI（`vibe/cli`，加上 `vibe/setup` 中的文本屏幕）的每个用户可见的更改，无论是新功能、行为更改还是错误修复，也必须落在同一 PR 中的 `vibe/cli-rust` 中。

- 将服务器拥有的行为（会话、配置和持久性、工具、网络；请参阅 ADR 0009 中的所有权表）放在应用程序服务器方法或通知后面，以便两个客户端仅呈现它。在每个客户端中保留演示状态（小部件、布局、焦点、选择、编辑）。
- 对于 Rust 侧，遵循 `vibe/cli-rust/AGENTS.md` 并使用 `client-e2e` 场景和黄金覆盖行为（请参阅 `client-e2e/AGENTS.md`）。
- 例外：Rust-仅`vibe/cli-rust/AGENTS.md`允许的更改和分歧；对尚未移植到 Rust 的功能进行更改（在 PR 注释中说明差距，不要移植父功能）；有意仅修复Python（PR注释中的一行理由）。

## 命令

始终遍历 `uv` — 切勿调用裸露的 `python` 或 `pip`。

- `uv run vibe` / `uv run vibe-acp` — 两个入口点。
- `uv run pytest` — 全套（通过 `pytest-xdist` 并行）。
- `uv run pyright` — 严格类型检查。
- `uv run ruff check --fix .` 和 `uv run ruff format .` — 在每次代码更改后运行并报告修改的文件。
- `uv run pre-commit run --all-files` — 完整 lint 通过。使用`uv tool install pre-commit && uv run pre-commit install`安装一次。
- 有用的 uv 基础知识：`uv sync --all-extras`、`uv add <pkg>`、`uv remove <pkg>`。

## 项目布局和模块约定

- `__init__.py` 通过显式 `__all__` 公开 API。
- 私有模块以 `_` 为前缀（例如 `_settings.py`、`_config.py`）。
- Pydantic 配置模型位于 `models.py`；共享配置默认位于 `_defaults.py` 中；有效的配置模式位于`vibe_schema.py`中。
- 抽象接口使用 `_port.py` 后缀（六角形端口）。
- 测试镜像源布局：测试位于镜像其源模块包的目录中。对 `vibe/core` 子包的测试在匹配的 `tests/core/<subpackage>/` 下进行（例如 `vibe/core/utils/retry.py` → `tests/core/utils/test_retry.py`）；对直接位于 `vibe/core/` 中的模块（例如 `loop.py`、`types.py`）的测试在 `tests/core/` 中保持平稳。测试子目录中不需要 `__init__.py` — pytest 在 `--import-mode=importlib` 中运行。 `tests/stubs/` 中的测试替身被命名为 `Fake*`。

## 自我改进

- 当变更请求可以概括为规则时，最好将其添加到相关的 `.agents/skills/` SKILL.md，而不是附加到 AGENTS.md。 AGENTS.md 应该保持小而通用；特定领域的约定属于技能，因此它们仅在需要时加载。当现有技能不适合时创建一项新技能。
- 根据功能更改或添加建议更新 README.md 文件。
- 保持内置 Vibe Skill (`vibe/core/skills/builtins/vibe.py`) 为最新。它记录了 CLI 的功能，例如参数、标志、配置选项和持久性、命令、内置 agents、文件发现逻辑。
- 添加或更改 Vibe 配置字段时，请检查设置管理器的 Vibe 代码托管配置 UI 和 Le Chat Web 的后端 allowlist/schema 是否必须更新。如果不需要更新，请在变更注释中明确说明。


## 原文对照

<details>
<summary>展开完整原文</summary>

# AGENTS.md

Conventions for AI agents and humans contributing to **Mistral Vibe** — a Python 3.12+ CLI coding assistant managed with `uv`.

Layout: `vibe/core` is the engine (agent loop, tools, LLM backends, config); `vibe/cli` is the legacy Python (Textual) TUI; `vibe/cli-rust` is the Rust (Ratatui) TUI that will replace it, a thin client over `vibe-app-server`; `vibe/acp` bridges to the Agent Client Protocol; `vibe/setup` runs first-run wizards. Tests live in `tests/` with autouse fixtures in `conftest.py` and test doubles in `tests/stubs/`.

## Architecture Decisions

Before architecture-affecting changes, read the matching ADR. If a change fits the current code but conflicts with ADR direction, flag it to the user before implementing. When creating or editing an ADR, follow the `write-vibe-adr` skill.

When creating or editing an ADR, follow the `write-vibe-adr` skill and keep the standard sections.

| Change area | ADR |
| --- | --- |
| Architecture principles, module boundaries, startup/runtime speed, simple changes | [0001 Architecture Principles](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0001-architecture-principles.md>) |
| Core engine, Textual CLI, ACP, setup, or programmatic surfaces | [0002 Core Engine And Delivery Surfaces](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0002-core-engine-and-delivery-surfaces.md>) |
| Agent loop orchestration, streaming, typed events, cancellation, or responsiveness | [0003 Event Driven Agent Loop](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0003-event-driven-agent-loop.md>) |
| Tool contracts, permissions, tool output, UI metadata, or tool adapters | [0004 Typed Permissioned Tools](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0004-typed-permissioned-tools.md>) |
| Config models, layering, defaults, migrations, reloads, or runtime overrides | [0005 Layered Configuration](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0005-layered-configuration.md>) |
| Session logging, resume, rewind, transcript metadata, or migrations | [0006 Local Sessions](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0006-local-sessions.md>) |
| Skills, agents, subagents, hooks, MCP, connectors, custom tools, or discovery | [0007 Extension Mechanisms](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0007-extension-mechanisms.md>) |
| Adding or changing analytics instrumentation, telemetry events, or event properties | [0008 Feature Instrumentation](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0008-feature-instrumentation.md>) |
| App-server ownership, RPCs, lifecycle, projections, effects, callbacks, client tools, or delivery adapters | [0009 App Server Boundary](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0009-app-server-boundary.md>) |
| Textual `Content` rendering, styled text, markup parsing, or theme variables in widgets | [0010 Textual Content Rendering](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0010-textual-content-rendering.md>) |
| App-server session backend interfaces, adapters, or runtime ownership | [0011 App Server Session Backends](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0011-unified-harness-backend.md>) |
| Slash commands running while busy, side-channel commands, or idle-only settings persistence | [0012 Slash Commands While Busy](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0012-two-phase-slash-command-execution.md>) |
| Queued-prompt selection/edit mode, copy-on-write consumed edits, or promotion-race widget-identity tracking | [0013 Queue Selection and Edit Mode](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0013-queue-edit-mode.md>) |
| Parsing backend responses, connector/MCP bootstrap fields, or any client/backend wire-contract change that must tolerate self-hosted version skew | [0014 Backend Contract Compatibility](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0014-backend-contract-compatibility.md>) |
| Outbound TLS connections, certificate roots, or `enable_system_trust_store` | [0015 Outbound TLS Trust Policy](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0015-outbound-tls-trust-policy.md>) |
| Rust CLI delivery surface, thin-client boundary, unidirectional state, or bounded-resource rules | [0016 Rust CLI Delivery Surface](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0016-rust-cli-delivery-surface.md>) |
| Narration playback, TTS, audio output device following, or the `AudioPlayer`/`DeviceFollower`/`NarrationController` classes | [0017 Narration Playback in the ACP Subprocess](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/docs/adr/0017-narration-playback-in-subprocess.md>) |

## Terminal UI changes

The Python TUI is still the default in production but is being deprecated in favor of the Rust TUI. Every user-visible change to the Python TUI (`vibe/cli`, plus the Textual screens in `vibe/setup`), whether a new feature, behavior change, or bug fix, must also land in `vibe/cli-rust` in the same PR.

- Put server-owned behavior (session, config and persistence, tools, network; see the ownership table in ADR 0009) behind an app-server method or notification so both clients only render it. Keep presentation state (widgets, layout, focus, selection, editing) in each client.
- For the Rust side, follow `vibe/cli-rust/AGENTS.md` and cover the behavior with a `client-e2e` scenario and golden (see `client-e2e/AGENTS.md`).
- Exceptions: Rust-only changes and divergences allowed by `vibe/cli-rust/AGENTS.md`; changes to a feature not yet ported to Rust (state the gap in the PR notes, do not port the parent feature); intentionally Python-only fixes (one-line justification in the PR notes).

## Commands

Always go through `uv` — never invoke bare `python` or `pip`.

- `uv run vibe` / `uv run vibe-acp` — the two entry points.
- `uv run pytest` — full suite (parallel via `pytest-xdist`).
- `uv run pyright` — strict type check.
- `uv run ruff check --fix .` and `uv run ruff format .` — run both after every code change and report the files modified.
- `uv run pre-commit run --all-files` — full lint pass. Install once with `uv tool install pre-commit && uv run pre-commit install`.
- Useful uv basics: `uv sync --all-extras`, `uv add <pkg>`, `uv remove <pkg>`.

## Project layout & module conventions

- `__init__.py` exposes the public API via an explicit `__all__`.
- Private modules are prefixed with `_` (e.g. `_settings.py`, `_config.py`).
- Pydantic config models live in `models.py`; shared config defaults live in `_defaults.py`; the effective config schema lives in `vibe_schema.py`.
- Abstract interfaces use the `_port.py` suffix (hexagonal-style ports).
- Tests mirror the source layout: a test lives in the directory mirroring its source module's package. Tests for a `vibe/core` subpackage go under the matching `tests/core/<subpackage>/` (e.g. `vibe/core/utils/retry.py` → `tests/core/utils/test_retry.py`); tests for modules that sit directly in `vibe/core/` (e.g. `loop.py`, `types.py`) stay flat in `tests/core/`. No `__init__.py` is needed in test subdirectories — pytest runs in `--import-mode=importlib`. Test doubles in `tests/stubs/` are named `Fake*`.

## Autoimprovement

- When a change request can be generalized as a rule, prefer adding it to the relevant `.agents/skills/` SKILL.md rather than appending to AGENTS.md. AGENTS.md should stay small and universal; domain-specific conventions belong in skills so they load only when needed. Create a new skill when no existing one fits.
- Suggest updates to the README.md file according to feature changes or additions.
- Keep the builtin Vibe Skill (`vibe/core/skills/builtins/vibe.py`) up-to-date. It documents the CLI's features, such as args, flags, config options and persistence, commands, built-in agents, file discovery logic.
- When adding or changing Vibe config fields, check whether Settings Manager's Vibe Code managed-config UI and Le Chat Web's backend allowlist/schema must be updated. If no update is needed, say so explicitly in the change notes.


</details>
