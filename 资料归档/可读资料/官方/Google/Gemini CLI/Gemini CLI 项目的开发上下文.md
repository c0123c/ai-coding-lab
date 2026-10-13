# Gemini CLI 项目的开发上下文

记录项目构建、运行、测试和开发约定，供参与该仓库开发时使用；属于项目内部指令。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-04-08](<https://github.com/google-gemini/gemini-cli/commit/c7b920717fad72229c788aa8efc646afac7a2965>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/GEMINI.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 项目的开发上下文.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Gemini CLI 项目背景

Gemini CLI 是一个开源的 AI agent，它将 Gemini 的功能直接带入终端 
。它被设计为面向开发人员的终端优先、可扩展且
强大的工具。

## 项目概况

- **用途：**为Gemini型号提供无缝终端接口，
通过 MCP
 (Model Context Protocol) 支持代码理解、生成、自动化和集成。
- **主要技术：**
  - **运行时：** Node.js（>=20.0.0，推荐~20.19.0进行开发）
  - **语言：** TypeScript
  - **UI框架：** React（使用[墨水](<https://github.com/vadimdemedes/ink>)
用于 CLI 渲染）
  - **测试：** Vitest
  - **捆绑：** esbuild
  - **Linting/Formatting:** ESLint，更漂亮
- **架构：**使用npm工作区的Monorepo结构。
  - `packages/cli`：面向用户的终端UI，输入处理和显示
渲染。
  - `packages/core`：后端逻辑，GeminiAPI编排、提示
构建和工具执行。
  - `packages/a2a-server`：实验性Agent到Agent服务器。
  - `packages/sdk`：用于嵌入 Gemini CLI 功能的编程 SDK。
  - `packages/devtools`：集成开发者工具（Network/Console检查器）。
  - `packages/test-utils`：共享测试实用程序和测试装备。
  - `packages/vscode-ide-companion`：VS Code分机与CLI配对。

## 构建和运行

- **安装依赖项：** `npm install`
- **全部构建：** `npm run build:all`（构建包、沙箱和 VS Code
同伴）
- **构建包：** `npm run build`
- **开发中运行：** `npm run start`
- **在调试模式下运行：** `npm run debug`（启用Node.js检查器）
- **捆绑项目：** `npm run bundle`
- **清洁工件：** `npm run clean`

## 测试和质量

- **测试命令：**
  - **单位（全部）：** `npm run test`
  - **积分（E2E）：** `npm run test:e2e`
  - > **NOTE**：仅当**您是时，请在本地运行内存和性能测试**
    > implementing changes related to those test areas. Otherwise skip these
    > tests locally and rely on CI to run them on nightly builds.
  - **内存（每晚）：** `npm run test:memory`（运行内存回归测试
对照基线。从 `preflight` 中排除，每晚运行。）
  - **性能（每晚）：** `npm run test:perf`（运行 CPU 性能
针对基线的回归测试。从 `preflight` 中排除，每晚运行。）
  - **工作空间特定：** `npm test -w <pkg> -- <path>`（注意：`<path>` 必须
相对于工作空间根目录，例如，
 `-w @google/gemini-cli-core -- src/routing/modelRouterService.test.ts`)
- **完整验证：** `npm run preflight`（最重的检查；干净运行，安装，
构建、lint、类型检查和测试。在提交 PR 之前推荐。由于
其运行时间较长，因此只能在代码实现任务的最后运行它。
如果失败，请使用更快的、有针对性的命令（例如，`npm run test`、
、`npm run lint`或特定于工作区的测试）来迭代修复在
重新运行`preflight`之前。对于简单的非代码更改（例如文档或 
 提示更新），请在任务结束时跳过 `preflight` 并等待 PR
 验证。）
- **单独支票：** `npm run lint` / `npm run format` / `npm run typecheck`

## 发展惯例

- **贡献：**遵循`CONTRIBUTING.md`中概述的流程。需要
签署 Google CLA。
- **拉取请求：**保持 PR 较小、重点突出并链接到现有问题。
即使使用
`gh`CLI时，始终激活PR生成的`pr-creator`技能。
- **提交消息：**遵循
[常规提交](<https://www.conventionalcommits.org/>)标准。
- **导入：** 使用特定导入并避免受限制的相对导入
包之间（由 ESLint 强制执行）。
- **许可证标头：** 对于所有新源代码文件（`.ts`、`.tsx`、`.js`），
包含 Apache-2.0 许可证标头和当前年份。 （例如，
 `Copyright 2026 Google LLC`）。这是由 ESLint 强制执行的。

## 测试约定

- **环境变量：**测试依赖于环境的代码时
变量，在 `beforeEach` 中使用 `vi.stubEnv('NAME', 'value')`，在 `afterEach` 中使用 
 `vi.unstubAllEnvs()`。避免直接将`process.env`修改为
，这会导致测试泄漏并且可靠性较低。要“取消设置”变量，请使用 
 空字符串 `vi.stubEnv('NAME', '')`。

## 文档

- 始终使用 `docs-writer` 当你被要求写作、编辑或
查看任何文档。
- 文档位于 `docs/` 目录中。
- 当代码更改呈现现有文档时建议文档更新
过时或不完整。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Gemini CLI Project Context

Gemini CLI is an open-source AI agent that brings the power of Gemini directly
into the terminal. It is designed to be a terminal-first, extensible, and
powerful tool for developers.

## Project Overview

- **Purpose:** Provide a seamless terminal interface for Gemini models,
  supporting code understanding, generation, automation, and integration via MCP
  (Model Context Protocol).
- **Main Technologies:**
  - **Runtime:** Node.js (>=20.0.0, recommended ~20.19.0 for development)
  - **Language:** TypeScript
  - **UI Framework:** React (using [Ink](<https://github.com/vadimdemedes/ink>)
    for CLI rendering)
  - **Testing:** Vitest
  - **Bundling:** esbuild
  - **Linting/Formatting:** ESLint, Prettier
- **Architecture:** Monorepo structure using npm workspaces.
  - `packages/cli`: User-facing terminal UI, input processing, and display
    rendering.
  - `packages/core`: Backend logic, Gemini API orchestration, prompt
    construction, and tool execution.
  - `packages/a2a-server`: Experimental Agent-to-Agent server.
  - `packages/sdk`: Programmatic SDK for embedding Gemini CLI capabilities.
  - `packages/devtools`: Integrated developer tools (Network/Console inspector).
  - `packages/test-utils`: Shared test utilities and test rig.
  - `packages/vscode-ide-companion`: VS Code extension pairing with the CLI.

## Building and Running

- **Install Dependencies:** `npm install`
- **Build All:** `npm run build:all` (Builds packages, sandbox, and VS Code
  companion)
- **Build Packages:** `npm run build`
- **Run in Development:** `npm run start`
- **Run in Debug Mode:** `npm run debug` (Enables Node.js inspector)
- **Bundle Project:** `npm run bundle`
- **Clean Artifacts:** `npm run clean`

## Testing and Quality

- **Test Commands:**
  - **Unit (All):** `npm run test`
  - **Integration (E2E):** `npm run test:e2e`
  - > **NOTE**: Please run the memory and perf tests locally **only if** you are
    > implementing changes related to those test areas. Otherwise skip these
    > tests locally and rely on CI to run them on nightly builds.
  - **Memory (Nightly):** `npm run test:memory` (Runs memory regression tests
    against baselines. Excluded from `preflight`, run nightly.)
  - **Performance (Nightly):** `npm run test:perf` (Runs CPU performance
    regression tests against baselines. Excluded from `preflight`, run nightly.)
  - **Workspace-Specific:** `npm test -w <pkg> -- <path>` (Note: `<path>` must
    be relative to the workspace root, e.g.,
    `-w @google/gemini-cli-core -- src/routing/modelRouterService.test.ts`)
- **Full Validation:** `npm run preflight` (Heaviest check; runs clean, install,
  build, lint, type check, and tests. Recommended before submitting PRs. Due to
  its long runtime, only run this at the very end of a code implementation task.
  If it fails, use faster, targeted commands (e.g., `npm run test`,
  `npm run lint`, or workspace-specific tests) to iterate on fixes before
  re-running `preflight`. For simple, non-code changes like documentation or
  prompting updates, skip `preflight` at the end of the task and wait for PR
  validation.)
- **Individual Checks:** `npm run lint` / `npm run format` / `npm run typecheck`

## Development Conventions

- **Contributions:** Follow the process outlined in `CONTRIBUTING.md`. Requires
  signing the Google CLA.
- **Pull Requests:** Keep PRs small, focused, and linked to an existing issue.
  Always activate the `pr-creator` skill for PR generation, even when using the
  `gh` CLI.
- **Commit Messages:** Follow the
  [Conventional Commits](<https://www.conventionalcommits.org/>) standard.
- **Imports:** Use specific imports and avoid restricted relative imports
  between packages (enforced by ESLint).
- **License Headers:** For all new source code files (`.ts`, `.tsx`, `.js`),
  include the Apache-2.0 license header with the current year. (e.g.,
  `Copyright 2026 Google LLC`). This is enforced by ESLint.

## Testing Conventions

- **Environment Variables:** When testing code that depends on environment
  variables, use `vi.stubEnv('NAME', 'value')` in `beforeEach` and
  `vi.unstubAllEnvs()` in `afterEach`. Avoid modifying `process.env` directly as
  it can lead to test leakage and is less reliable. To "unset" a variable, use
  an empty string `vi.stubEnv('NAME', '')`.

## Documentation

- Always use the `docs-writer` skill when you are asked to write, edit, or
  review any documentation.
- Documentation is located in the `docs/` directory.
- Suggest documentation updates when code changes render existing documentation
  obsolete or incomplete.


</details>
