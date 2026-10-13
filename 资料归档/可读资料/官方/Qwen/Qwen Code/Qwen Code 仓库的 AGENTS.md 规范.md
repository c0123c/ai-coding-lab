# Qwen Code 仓库的 AGENTS.md 规范

规定开发原则、维护者边界、构建测试、代码风格、网页界面和 GitHub 操作；属于该仓库内部约定。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-20](<https://github.com/QwenLM/qwen-code/commit/64dd058c9c109eeb5f7122c430f84bdec860d186>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/AGENTS.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code 仓库的 AGENTS.md 规范.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# AGENTS.md

## 工作原理

### 简单第一

**解决问题的最少代码。没什么推测的。**
**（这是我们最关心的原则。）**

- 没有超出要求的功能。
- 没有一次性代码的抽象。
- 没有不被要求的“灵活性”或“可配置性”。
- 对于不可能的情况没有错误处理。
- 如果你写了200行，它可能是50，重写它。

问问自己：“高级工程师会说这过于复杂吗？”如果是，
简化。

### 核心基础设施仅限维护者（分类门、两层规则）

核心模块 - `packages/core/src/**`、`packages/*/src/auth/**`、
`packages/*/src/providers/**`、`packages/*/src/models/**`、
`packages/*/src/config/**`、`packages/*/src/tools/**`、
`packages/*/src/services/**`、跨封装更改 - 是建筑
骨干。接触它们的外部 PR 面临两层门（维护者编写的 
PR 除外）：

1. **大范围`refactor`变更（500+核心生产逻辑线，
不包括测试和 generated/schema 文件）→ 硬块。**
 完全跳过评估 - 上述维护者豁免是唯一的 
 例外。大规模核心重构必须由维护者启动。
 计算行数时，排除匹配 `*.test.ts`、`*.test.tsx`、
、`*.spec.ts`、`*.spec.tsx`、`__tests__/**`、 `*.schema.ts`、`*.schema.json`、
、`*.generated.ts` 和 `**/generated/**` — 仅生产逻辑计数。
、`feat` 类型和其他非 `refactor` PR 为NOT 尺寸硬块；他们
 升级到维护人员以提高认识。非阻塞咨询
也适用于1000+生产逻辑线。广度本身并不意味着大小——
低风险扫描触及10+
文件，但每更改一两行，就会升级为维护者以提高
意识，并在2级别下进行判断100%-置信度栏，而不是
在文件计数上自动拒绝。
2. **小范围变化→门可以评估，但必须100%有信心。**
任何疑问 → 升级给维护人员。 “方向看起来是正确的”
不是信心。门必须命名每个下游消费者；如果
 不能，则升级。

**如有疑问，请升级。错误地升级比错误地
批准更好。**

## 常用命令

### 建筑

```bash
corepack pnpm install --frozen-lockfile  # Install all dependencies (the tree CI tests)
npm run build      # Build all packages (TypeScript compilation + asset copying)
npm run build:all  # Build everything including sandbox container
npm run bundle     # Bundle dist/ into a single dist/cli.js via esbuild
                   # (requires build first)
```

### 发展

```bash
npm run dev        # Run CLI from TypeScript source
```

通过 `tsx` 和 `DEV=true` 运行 CLI。对 `packages/core` 或 
`packages/cli` 的更改会立即反映，无需重建。浏览器使用重用
其构建的运行时，就像其他编译的工作区依赖项一样。编辑
浏览器使用后，运行`npm run build --workspace=@qwen-code/browser-use`来重建
并为开发者暂存其运行时。普通`npm install`自动准备。

### 单元测试

测试必须从特定的包目录运行，而不是项目
根目录。

**新克隆或新工作树：** `packages/cli`单元测试通过其构建导入工作区
包（`@qwen-code/acp-bridge`，`@qwen-code/web-templates`，
`packages/channels/*`，...） `dist/` 输出，
`packages/core` 测试导入包自己的条目 
(`@qwen-code/qwen-code-core`)，它也解析为 `dist/`。普通的 
`corepack pnpm install --frozen-lockfile` 已经通过 
`prepare` 脚本构建它们，但是共享主结帐的 
`node_modules` （或深度清理的副本）的工作树没有它们。如果缺少任何
先决条件，vitest `globalSetup`守卫会停止运行并命名
修复；从存储库根目录构建一次：

```bash
npm run build
```

**安装依赖项：** CI，使用固定在 `packageManager` 中的 
pnpm 版本发布和每个工作流程安装（`corepack pnpm install
--frozen-lockfile`); scripts still run through `npm run`。 Git
worktree 可以使用 `node scripts/setup-worktree.js`，它从共享存储运行相同的安装 
（≈ 99 MiB 在写时复制文件系统上，例如 APFS， btrfs,
 和 XFS with reflink; ≈ 1.2 GiB on ext4) 并跳过 `prepare` 构建，因此在包测试之前运行 
`npm run build`。 
清单，运行`corepack pnpm install`（或`corepack pnpm add`），并提交
`pnpm-lock.yaml`；每个CI安装都与`--frozen-lockfile`一起运行，因此不再与清单匹配的
锁定文件将导致构建失败。

**运行单独的测试文件**（始终首选）：

```bash
cd packages/core && npx vitest run src/path/to/file.test.ts
cd packages/cli && npx vitest run src/path/to/file.test.ts
```

**更新快照：**

```bash
cd packages/cli && npx vitest run src/path/to/file.test.ts --update
```

**避免：**

- `npm run test -- --filter=...` — NOT 过滤；运行整个套件
- 来自项目根目录的 `npx vitest` — 由于特定于包的 vitest 而失败
配置
- 除非必要，否则运行整个测试套件（例如，最终 PR 验证）

**测试陷阱：**

- 在 CLI 测试中，对 `vi.mock()` 使用的模拟使用 `vi.hoisted()` —
模拟工厂在模块加载时运行，在测试执行之前。

### 集成测试

首先构建捆绑包：`npm run build && npm run bundle`

使用专用的 npm 脚本从项目根运行：

```bash
npm run test:integration:cli:sandbox:none
npm run test:integration:interactive:sandbox:none
```

或者组合成一个命令：

```bash
cd integration-tests && \
  cross-env QWEN_SANDBOX=false npx vitest run cli interactive
```

**问题：** 在交互式测试中，始终在发送 -
ANSI 输出流之间异步调用 `session.idle()`。

### 检测和格式化

```bash
npm run lint       # ESLint check
npm run lint:fix   # Auto-fix lint issues
npm run format     # Prettier formatting
npm run typecheck  # TypeScript type checking
npm run preflight  # Full check: clean → install → format → lint → build
                   # → typecheck → test
```

## 代码约定

- **模块系统**：整个ESM（所有包中的`"type": "module"`）
- **TypeScript**：严格模式，`noImplicitAny`、`strictNullChecks`、
  `noUnusedLocals`, `verbatimModuleSyntax`
- **格式**：更漂亮 - 单引号、分号、尾随逗号、
2-空格缩进，80-字符宽度
- **Linting**：无`any`类型，一致类型导入，无相对导入
包之间
- **cli**中的核心导入：`packages/cli/src`中的生产代码从定义它们的模块（`@qwen-code/qwen-code-core/utils/debugLogger.js`）导入核心值，而不是包根，它评估到达文件的每个测试中的所有核心。仅类型进口可以豁免。早于该规则的文件已列入 `eslint.legacy-core-barrel-imports.mjs` 允许名单；当您将其文件移出根目录时，会删除一个条目，而不要添加一个条目。
- **测试**：与源并置（`file.test.ts`在`file.ts`旁边），
维测试框架
- **文件命名**：`PascalCase.tsx`表示React组件，`kebab-case.ts`表示
`packages/core` 和 `packages/cli` 中的 `.ts` 文件（由 ESLint 强制执行）。现有的 camelCase 文件已列入 `eslint.legacy-filenames.mjs` 允许名单；在接触它们时机会性地重命名，更新同一提交中的所有导入（注意：重命名会丢失 `git blame` 历史记录）。
- **注释**：默认为无。仅当_为什么_不明显时添加；不要删除现有的作为清理。
- **提交**：常规提交（例如，`feat(cli): Add --json flag`）
- **Node.js**：开发和生产都需要`>=22`（墨水7+React19.2要求）

### Web Shell UI 开发

- 更喜欢共享原语
`packages/web-shell/client/components/ui` 开发 Web Shell 时 UI。不要
复制现有的原语或重写稳定的CSS模块，仅为了
一致性。
- 如果缺少所需的原语，请运行
`packages/web-shell` 中的 `npx shadcn@latest add <component>`，然后查看 
 生成的差异。不要让 CLI 覆盖现有的全局 CSS、
 语义标记、CSS 范围或门户根集成。将生成的 
 组件保留在内部，除非明确需要公共包 API。
- Web Shell 支持 React 18 和 React 19。经常生成shadcn组件
假定 React 19 引用语义，因此接受或接收引用的包装器 - 
 包括基数 `asChild`、`Slot`、`Presence` 和门户子级 - 必须使用 
 `React.forwardRef` 并将引用传递给底层 DOM 或 Radix 原语。
 为任何引用敏感组件路径添加回归测试。
- 使用无前缀的 Tailwind 类和 shadcn 语义颜色标记，例如
`background`、`primary` 和 `muted`。包构建范围生成 CSS
 到 Web Shell 根和门户根，并为全局动画和 CSS
 属性注册添加前缀；更改必须保持与主机页面
样式的隔离。
- 具有门户的组件，例如对话框、弹出窗口、下拉菜单和
工具提示，必须使用 `useWebShellPortalRoot()` 作为 Radix 门户容器，因此 
 主题、范围 CSS 和 z 索引变量继续适用。保留 
 现有 `data-web-shell-*` 属性和公共 `--web-shell-*` CSS
 变量。完整约定请参见 `packages/web-shell/README.md`。

## 开发指南

### 一般工作流程

1. **重要工作的设计文档** — 在 `docs/design/` 中写入一个，如果
   更改涉及多个文件或涉及设计决策。跳过小号
   错误修复。提供英语 `<name>.md` 和一个中国人
   `<name>.zh-CN.md` 版本在同一目录中，遵循
   [设计文件要求](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/README.md>)。添加倒数
   语言链接并保持两个版本的完整和同步
   更改，包括更新现有设计时。
2. **行为改变的测试计划** — 编写一个 E2E 测试计划
`.qwen/e2e-tests/` 当更改影响用户可观察的行为时。首先针对全局 `qwen` CLI 进行试运行 
 以确认基线。
3. **在声明完成之前构建、类型检查和测试**：
`npm run build && npm run typecheck`，加上您
更改的文件的单元测试。
4. **声明完成之前进行自我审核** - 阅读您所关注的完整差异
以开放式方式运送，包括新的未跟踪文件，而不是寻找
任何特定的东西。然后验证每个更改，以及您
依赖的每个绿色测试作为证据，假设它是错误的（通过的测试可以断言
错误的事情）。在连续两次干净传递后停止 - 干净传递是
有关该传递的证据，而不是代码。修复重新运行步骤 3，重置 
 干净传递计数，并进一步传递更新后的差异 - 永远不会在发现某些内容的传递中退出 
。如果五遍没有带来收敛，
 如此说而不是宣布完成。缩放到差异：一次干净、仔细的
传递足以进行微不足道的更改。

### 功能开发

使用 `/feat-dev` 技能完成整个工作流程：调查、设计、测试计划、
 试运行、实施、验证、自我审核、代码审查和迭代。

### 错误修正

使用 `/bugfix` 技能进行重现优先工作流程：重现、修复、
验证、测试、自我审核和代码审查。

## 代码审查

`/review` 的项目特定规则。该技能逐字加载本节（通过
其`## Code Review`标题）并将其交给每个评论agent，因此将其保留为
_此_代码库的评论者必须检查的事情 - 而不是一般建议。

- **在报告之前根据确切的已审核提交验证结果。**
阅读您要引用的内容。引用 
 正在审查的提交中不存在的代码的批评比没有发现更糟糕 - 它阻止了作者 
 任何事情。不要报告您仅从符号名称或 
 diff 片段推断出的缺陷。
- **A `C=0` / APPROVE 是一项索赔，而非违约。** 在提交之前，请先
PR 上已经存在每个未解决的 Critical，并根据 
 所代表的代码检查它： _ 仍然代表 _ / _ 由此差异修复 _ / _ 不能告诉_。 GitHub 线程 
 可以读取 `isResolved: false, isOutdated: false` 以查找稍后提交 
 在相邻行上修复的错误 - 该标志跟踪锚定行，而不是修复。
- **对于每个添加的字段、选项或可选参数，grep 其读取站点**，
  包括差异之外。一个 `foo?: boolean` 被声明和读取但从未
  任何调用者设置的都是死开关（`options.foo ?? true` 总是采取
  默认）。确定读取位置的严重性；永远不要解释一个无人居住的领域
  出于作者的意图，你无法观察到。
- **按所有权对每个添加或更改的守护进程路由进行分类。** 名称是否
是进程全局、旧版主、选定运行时、实时会话所有者或 
 持久工作空间范围，并验证每个下游使用者都匹配该 
 范围。
- **验证工作区范围的路由是否保留在解析的运行时内。**检查
环境、网桥、服务、文件系统、信任边界和故障路径。
 每个未知、不可信、不明确、引导、耗尽或删除的状态 
 必须遵循其声明的故障语义，并且绝不能回退到 
 主运行时。
- **判断时需符合房屋风格。**仅限ESM；没有`any`；无相对进口
  包之间； `kebab-case.ts` 为了 `.ts` 在 `packages/core` 和 `packages/cli`,
  `PascalCase.tsx` 为了 React 组件；测试搭配为 `file.test.ts`.
  评论默认为无 - 标记 a _失踪_ 仅评论 _为什么_ 是
  真正不明显，并且永远不要因为删除不存在的评论而指责差异
  更长时间适用。
- **缺少对行为改变的测试是建议，而不是批评**，除非
未经测试的路径本身就是缺陷。
- **检查两种语言的设计文档。**新的或更新的设计
必须包括具有匹配结构的链接英文和中文版本、
决策、约束和验收标准。翻译差距本身就是
一个建议，而不是批评。

## GitHub 操作

使用 `gh` CLI 进行所有与 GitHub 相关的操作 - 问题、拉取请求、
 评论、CI 检查、发布和 API 调用。优先使用 `gh issue view`、
`gh pr view`、`gh pr checks`、`gh run view`、`gh api` 等，而不是 Web 获取 
 或手动 REST 调用。

## 测试、调试和错误修复

- **错误再现和验证**：产生`test-engineer` agent。它
读取代码和文档以了解错误，然后通过E2E测试
（或测试脚本回退）重现它。它还处理修复后验证。它不能
编辑源代码——只能观察和报告。
- **硬错误**：在需要调试时使用`structured-debugging`技能
不仅仅是快速浏览 - 特别是当第一次尝试修复没有
工作或者该行为似乎不可能时。
- **E2E测试**：`e2e-testing`技能涵盖无头模式、交互
(tmux) 模式、MCP 服务器测试和 API 流量检查。 
 `test-engineer` agent 在内部调用此技能 - 您通常不需要
 直接使用它。

## 提交 PR

创建PR时，请按照`.github/pull_request_template.md`中的模板进行操作。
提交PR后，如果
适用，请与E2E测试报告一起发表单独的评论。

- **PR说明**：解释散文的动机和变化。避免
引用文件名或函数名。
- **审阅者测试计划**（模板部分）：描述审阅者的行为
应该验证以及期望什么，而不是脚本化的测试命令。使用**如何
验证**进行重现步骤；当
适用时，Before/After为TUI证据。
- **换行**：不要以固定的列宽对PR主体进行硬换行。
GitHub 将单个换行符呈现为 `<br>`，因此包装的描述将 
 显示为窄列。将每个段落或列表项写成一长行。
- **不要让审核轮次膨胀PR。**每个接受的更改都会扩大范围
diff 并往往会触发另一轮，因此 PR 可能会远远超出其 
 的原始意图。一旦 PR 经过大致 **5 轮审核 **，仅修复 
 关键修复 — 正确性、安全性、数据丢失、回归 — 并且 
 将剩余建议推迟到后续问题或 PR。在 PR 线程中记录每个延迟 
，这样就不会默默地丢弃任何内容。

## 项目目录

设计文档和实施计划在`docs/`下提交，因此它们在版本控制中
进行跟踪：

|目录|目的|
| -------------- | -------------------------------- |
| `docs/design/` |计划功能的设计文档|
| `docs/plans/`  |实施计划|

其他工作工件位于 `.qwen/` 下（git-ignored）：

|目录|目的|
| ----------------------- | ------------------------------------ |
| `.qwen/e2e-tests/`      |E2E测试计划和结果|
| `.qwen/issues/`         |在 GitHub 备案前发出草案|
| `.qwen/pr-drafts/`      |PR 提交前草稿|
| `.qwen/pr-reviews/`     |PR复习笔记|
| `.qwen/investigations/` |结构化调试日志|
| `.qwen/scripts/`        |实用脚本|


## 原文对照

<details>
<summary>展开完整原文</summary>

# AGENTS.md

## Working Principles

### Simplicity First

**Minimum code that solves the problem. Nothing speculative.**
**(This is the principle we care about most.)**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes,
simplify.

### Core Infrastructure Is Maintainer-Only (triage gate, two-tier rule)

Core modules — `packages/core/src/**`, `packages/*/src/auth/**`,
`packages/*/src/providers/**`, `packages/*/src/models/**`,
`packages/*/src/config/**`, `packages/*/src/tools/**`,
`packages/*/src/services/**`, cross-package changes — are the architectural
backbone. External PRs touching them face a two-tier gate (maintainer-authored
PRs are exempt):

1. **Large-scope `refactor` changes (500+ production logic lines in core,
   excluding test and generated/schema files) → hard block.**
   Skip evaluation entirely — the maintainer exemption above is the sole
   exception. Large-scale core refactors must be maintainer-initiated.
   When counting lines, exclude files matching `*.test.ts`, `*.test.tsx`,
   `*.spec.ts`, `*.spec.tsx`, `__tests__/**`, `*.schema.ts`, `*.schema.json`,
   `*.generated.ts`, and `**/generated/**` — only production logic counts.
   `feat`-type and other non-`refactor` PRs are NOT hard-blocked on size; they
   escalate to the maintainer for awareness instead. A non-blocking advisory
   also applies at 1000+ production logic lines. Breadth alone is not size — a
   low-risk sweep that touches 10+
   files but changes a line or two each is escalated to a maintainer for
   awareness and otherwise judged under Tier 2's 100%-confidence bar, not
   auto-rejected on file count.
2. **Small-scope changes → gate may evaluate, but must be 100% confident.**
   Any doubt at all → escalate to maintainer. "The direction looks correct"
   is not confidence. The gate must name every downstream consumer; if it
   cannot, escalate.

**When in doubt, escalate. Better to wrongly escalate than to wrongly
approve.**

## Common Commands

### Building

```bash
corepack pnpm install --frozen-lockfile  # Install all dependencies (the tree CI tests)
npm run build      # Build all packages (TypeScript compilation + asset copying)
npm run build:all  # Build everything including sandbox container
npm run bundle     # Bundle dist/ into a single dist/cli.js via esbuild
                   # (requires build first)
```

### Development

```bash
npm run dev        # Run CLI from TypeScript source
```

Runs the CLI via `tsx` with `DEV=true`. Changes to `packages/core` or
`packages/cli` are reflected immediately without rebuilding. Browser Use reuses
its built runtime, like other compiled workspace dependencies. After editing
Browser Use, run `npm run build --workspace=@qwen-code/browser-use` to rebuild
and stage its runtime for dev. Normal `npm install` prepares it automatically.

### Unit Testing

Tests must be run from within the specific package directory, not the project
root.

**Fresh clone or new worktree:** `packages/cli` unit tests import workspace
packages (`@qwen-code/acp-bridge`, `@qwen-code/web-templates`,
`packages/channels/*`, ...) through their built `dist/` output, and
`packages/core` tests import the package's own entry
(`@qwen-code/qwen-code-core`), which also resolves into `dist/`. A plain
`corepack pnpm install --frozen-lockfile` already builds them via the
`prepare` script, but a worktree that shares the main checkout's
`node_modules` (or a deep-cleaned copy) does not have them. If any
prerequisite is missing, a vitest `globalSetup` guard stops the run and names
the fix; build once from the repository root:

```bash
npm run build
```

**Installing dependencies:** CI, release and every workflow install with the
pnpm version pinned in `packageManager` (`corepack pnpm install
--frozen-lockfile`); scripts still run through `npm run`. An additional Git
worktree can use `node scripts/setup-worktree.js`, which runs the same install
from a shared store (≈ 99 MiB on copy-on-write filesystems such as APFS, btrfs,
and XFS with reflink; ≈ 1.2 GiB on ext4) and skips the `prepare` build, so run
`npm run build` before package tests. When dependencies change, edit the
manifest, run `corepack pnpm install` (or `corepack pnpm add`), and commit
`pnpm-lock.yaml`; every CI install runs with `--frozen-lockfile`, so a
lockfile that no longer matches the manifests fails the build.

**Run individual test files** (always preferred):

```bash
cd packages/core && npx vitest run src/path/to/file.test.ts
cd packages/cli && npx vitest run src/path/to/file.test.ts
```

**Update snapshots:**

```bash
cd packages/cli && npx vitest run src/path/to/file.test.ts --update
```

**Avoid:**

- `npm run test -- --filter=...` — does NOT filter; runs the entire suite
- `npx vitest` from the project root — fails due to package-specific vitest
  configs
- Running the whole test suite unless necessary (e.g., final PR verification)

**Test gotchas:**

- In CLI tests, use `vi.hoisted()` for mocks consumed by `vi.mock()` — the
  mock factory runs at module load time, before test execution.

### Integration Testing

Build the bundle first: `npm run build && npm run bundle`

Run from the project root using the dedicated npm scripts:

```bash
npm run test:integration:cli:sandbox:none
npm run test:integration:interactive:sandbox:none
```

Or combined in one command:

```bash
cd integration-tests && \
  cross-env QWEN_SANDBOX=false npx vitest run cli interactive
```

**Gotcha:** In interactive tests, always call `session.idle()` between sends —
ANSI output streams asynchronously.

### Linting & Formatting

```bash
npm run lint       # ESLint check
npm run lint:fix   # Auto-fix lint issues
npm run format     # Prettier formatting
npm run typecheck  # TypeScript type checking
npm run preflight  # Full check: clean → install → format → lint → build
                   # → typecheck → test
```

## Code Conventions

- **Module system**: ESM throughout (`"type": "module"` in all packages)
- **TypeScript**: Strict mode with `noImplicitAny`, `strictNullChecks`,
  `noUnusedLocals`, `verbatimModuleSyntax`
- **Formatting**: Prettier — single quotes, semicolons, trailing commas,
  2-space indent, 80-char width
- **Linting**: No `any` types, consistent type imports, no relative imports
  between packages
- **Core imports in cli**: production code in `packages/cli/src` imports core values from the module that defines them (`@qwen-code/qwen-code-core/utils/debugLogger.js`), not the package root, which evaluates all of core in every test that reaches the file. Type-only imports are exempt. Files that predate the rule are allowlisted in `eslint.legacy-core-barrel-imports.mjs`; drop an entry when you move its file off the root, never add one.
- **Tests**: Collocated with source (`file.test.ts` next to `file.ts`),
  vitest framework
- **File naming**: `PascalCase.tsx` for React components, `kebab-case.ts` for
  `.ts` files in `packages/core` and `packages/cli` (enforced by ESLint). Existing camelCase files are allowlisted in `eslint.legacy-filenames.mjs`; rename opportunistically when touching them, updating all imports in the same commit (note: renames lose `git blame` history).
- **Comments**: Default to none. Add only when _why_ is non-obvious; don't delete existing ones as cleanup.
- **Commits**: Conventional Commits (e.g., `feat(cli): Add --json flag`)
- **Node.js**: Development and production both require `>=22` (Ink 7 + React 19.2 requirement)

### Web Shell UI development

- Prefer the shared primitives in
  `packages/web-shell/client/components/ui` when developing Web Shell UI. Do
  not duplicate an existing primitive or rewrite stable CSS Modules solely for
  consistency.
- If a required primitive is missing, run
  `npx shadcn@latest add <component>` from `packages/web-shell`, then review the
  generated diff. Do not let the CLI overwrite the existing global CSS,
  semantic tokens, CSS scoping, or portal-root integration. Keep generated
  components internal unless a public package API is explicitly required.
- Web Shell supports React 18 and React 19. Generated shadcn components often
  assume React 19 ref semantics, so wrappers that accept or receive refs —
  including Radix `asChild`, `Slot`, `Presence`, and portal children — must use
  `React.forwardRef` and pass the ref to the underlying DOM or Radix primitive.
  Add a regression test for any ref-sensitive component path.
- Use unprefixed Tailwind classes and shadcn semantic color tokens such as
  `background`, `primary`, and `muted`. The package build scopes generated CSS
  to the Web Shell root and portal root and prefixes global animations and CSS
  property registrations; changes must preserve that isolation from host-page
  styles.
- Components with portals, such as dialogs, popovers, dropdown menus, and
  tooltips, must use `useWebShellPortalRoot()` as the Radix portal container so
  themes, scoped CSS, and z-index variables continue to apply. Preserve
  existing `data-web-shell-*` attributes and public `--web-shell-*` CSS
  variables. See `packages/web-shell/README.md` for the full conventions.

## Development Guidelines

### General workflow

1. **Design doc for non-trivial work** — write one in `docs/design/` if the
   change touches multiple files or involves design decisions. Skip for small
   bugfixes. Provide both an English `<name>.md` and a Chinese
   `<name>.zh-CN.md` version in the same directory, following the
   [design documentation requirements](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/README.md>). Add reciprocal
   language links and keep both versions complete and synchronized in the same
   change, including when updating an existing design.
2. **Test plan for behavioral changes** — write an E2E test plan in
   `.qwen/e2e-tests/` when the change affects user-observable behavior. Dry-run
   against the global `qwen` CLI first to confirm the baseline.
3. **Build, typecheck, and test before declaring done**:
   `npm run build && npm run typecheck`, plus unit tests for the files you
   changed.
4. **Self-audit before declaring done** — read the full diff you are about
   to ship, including new untracked files, in open-ended passes, not hunting
   for anything specific. Then verify each change, and each green test you
   rely on as evidence, presuming it wrong (a passing test can assert the
   wrong thing). Stop after two consecutive clean passes — a clean pass is
   evidence about that pass, not the code. A fix re-runs step 3, resets the
   clean-pass count, and gets a further pass over the updated diff — never
   exit on a pass that found something. If five passes bring no convergence,
   say so instead of declaring done. Scale to the diff: one clean, careful
   pass suffices for a trivial change.

### Feature development

Use the `/feat-dev` skill for the full workflow: investigate, design, test plan,
dry-run, implement, verify, self-audit, code review, and iterate.

### Bugfix

Use the `/bugfix` skill for the reproduce-first workflow: reproduce, fix,
verify, test, self-audit, and code review.

## Code Review

Project-specific rules for `/review`. The skill loads this section verbatim (by
its `## Code Review` heading) and hands it to every review agent, so keep it to
things a reviewer of _this_ codebase must check — not general advice.

- **Verify a finding against the exact reviewed commit before reporting it.**
  Read the lines you are about to cite. A Critical that quotes code not present at
  the commit under review is worse than no finding — it blocks the author over
  nothing. Do not report a defect you have only inferred from a symbol name or a
  diff fragment.
- **A `C=0` / APPROVE is a claim, not a default.** Before submitting one, take
  each unresolved Critical already on the PR and check it against the code as it
  stands: _still stands_ / _fixed by this diff_ / _cannot tell_. A GitHub thread
  can read `isResolved: false, isOutdated: false` for a bug that a later commit
  fixed on an adjacent line — the flag tracks the anchored line, not the fix.
- **For every added field, option, or optional parameter, grep its read sites**,
  including outside the diff. A `foo?: boolean` that is declared and read but never
  set by any caller is a dead switch (`options.foo ?? true` always takes the
  default). Decide severity at the read site; never explain an unpopulated field
  with author intent you cannot observe.
- **Classify every added or changed daemon route by ownership.** Name whether it
  is process-global, legacy-primary, selected-runtime, live-session-owner, or
  persisted-workspace scoped, and verify every downstream consumer matches that
  scope.
- **Verify workspace-scoped routes stay inside the resolved runtime.** Check the
  environment, bridge, service, filesystem, trust boundary, and failure paths.
  Each unknown, untrusted, ambiguous, bootstrapping, draining, or removed state
  must follow its declared failure semantics and must never fall back to the
  primary runtime.
- **Match the house style when judging.** ESM only; no `any`; no relative imports
  between packages; `kebab-case.ts` for `.ts` in `packages/core` and `packages/cli`,
  `PascalCase.tsx` for React components; tests collocated as `file.test.ts`.
  Comments default to none — flag a _missing_ comment only where the _why_ is
  genuinely non-obvious, and never fault a diff for deleting a comment that no
  longer applies.
- **A missing test for changed behavior is a Suggestion, not a Critical**, unless
  the untested path is itself the defect.
- **Check design documentation in both languages.** New or updated designs
  must include linked English and Chinese versions with matching structure,
  decisions, constraints, and acceptance criteria. A translation gap alone is
  a Suggestion, not a Critical.

## GitHub Operations

Use the `gh` CLI for all GitHub-related operations — issues, pull requests,
comments, CI checks, releases, and API calls. Prefer `gh issue view`,
`gh pr view`, `gh pr checks`, `gh run view`, `gh api`, etc. over web fetches
or manual REST calls.

## Testing, Debugging, and Bug Fixes

- **Bug reproduction & verification**: spawn the `test-engineer` agent. It
  reads code and docs to understand the bug, then reproduces it via E2E testing
  (or a test-script fallback). It also handles post-fix verification. It cannot
  edit source code — only observe and report.
- **Hard bugs**: use the `structured-debugging` skill when debugging requires
  more than a quick glance — especially when the first attempt at a fix didn't
  work or the behavior seems impossible.
- **E2E testing**: the `e2e-testing` skill covers headless mode, interactive
  (tmux) mode, MCP server testing, and API traffic inspection. The
  `test-engineer` agent invokes this skill internally — you typically don't
  need to use it directly.

## Submitting PRs

When creating a PR, follow the template at `.github/pull_request_template.md`.
After the PR is submitted, post a separate comment with the E2E test report if
applicable.

- **PR description**: explain the motivation and changes in prose. Avoid
  referencing file names or function names.
- **Reviewer Test Plan** (template section): describe behaviors a reviewer
  should verify and what to expect, not scripted test commands. Use **How to
  verify** for reproduction steps; Before/After for TUI evidence when
  applicable.
- **Line wrapping**: do not hard-wrap the PR body at a fixed column width.
  GitHub renders single newlines as `<br>`, so a wrapped description displays
  as a narrow column. Write each paragraph or list item as one long line.
- **Don't let review rounds balloon the PR.** Every accepted change widens the
  diff and tends to trigger another round, so a PR can drift far past its
  original intent. Once a PR has been through roughly **5 review rounds**, land
  only Critical fixes — correctness, security, data loss, regressions — and
  defer remaining Suggestions to a follow-up issue or PR. Record each deferral
  in the PR thread so nothing is silently dropped.

## Project Directories

Design docs and implementation plans are committed under `docs/` so they are
tracked in version control:

| Directory      | Purpose                          |
| -------------- | -------------------------------- |
| `docs/design/` | Design docs for planned features |
| `docs/plans/`  | Implementation plans             |

Other working artifacts live under `.qwen/` (git-ignored):

| Directory               | Purpose                              |
| ----------------------- | ------------------------------------ |
| `.qwen/e2e-tests/`      | E2E test plans and results           |
| `.qwen/issues/`         | Issue drafts before filing on GitHub |
| `.qwen/pr-drafts/`      | PR drafts before submitting          |
| `.qwen/pr-reviews/`     | PR review notes                      |
| `.qwen/investigations/` | Structured debugging journals        |
| `.qwen/scripts/`        | Utility scripts                      |


</details>
