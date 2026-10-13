# Cline 仓库的云端 Agent 指令

规定该项目使用的工具链、CLI 启动、构建、检查、测试及界面验证方式；属于 Cline 项目内部开发规范。

- 来源：Cline
- 资料类型：存档文档
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/cline/cline/commit/4a60215b12a4777de04ced7e96149dba4fa07908>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/cline/cline/blob/main/AGENTS.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline 仓库的云端 Agent 指令.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

这是 **Cline** monorepo。工具链是 **Bun 1.4.2** （包管理器 + 任务运行器），以 **Node >=22** 作为运行时。请勿使用npm/yarn/pnpm.

不要添加 `.changeset/` 文件或其他每个 PR 变更日志条目；这里不使用变更集。发行说明在发行时写入相关的`CHANGELOG.md`中。

## 云Agent使用说明

### Cline CLI
- 从源运行：`bun run cli`（交互式：`bun run cli -i`；一次性：附加提示）。这解析为 `apps/cli` 和 ** 自动生成 `@cline/cline-hub` 守护进程 ** — 您无需单独启动集线器。
- 用【`bun run cli doctor`】检查当地卫生情况； `bun run cli version` 打印版本。
- 实际的 agent 回合需要 **LLM 提供商凭证 **。如果没有凭据，默认的 `cline` 提供程序会快速失败，并出现 `Unauthorized` 错误，并且交互式 TUI 显示提供程序登录屏幕。通过 `cline auth` 或提供者环境变量进行配置（例如 `ANTHROPIC_API_KEY`、`CLINE_API_KEY`、`OPENROUTER_API_KEY`）；参见`apps/cli/README.md`。

### 构建/Lint/测试
- SDK 包（`@cline/shared|llms|agents|core|sdk`）通过编译的 `dist/` 相互解析（它们的 `exports` 仅指向 `dist/`，没有 `development` 源条件）。在运行 CLI 或 SDK 测试之前，您 ** 必须 ** 更改 SDK dependencies/source 后运行 `bun run build:sdk`，否则导入会失败，并缺少 `@cline/*` / 缺失`dist/` 错误。正在运行的进程执行**而不是**热重载SDK源更改 - 重建并重新启动。\
- 已知的 cloud-env 测试工件：`@cline/core` 测试 `src/services/workspace/workspace-manifest.test.ts > readGitWorkspaceState > prefers origin and returns the current branch` 失败，因为云虚拟机配置 git `insteadOf` 规则，将 GitHub 远程重写为 `https://x-access-token:...@github.com/...`。这是一个环境工件，而不是代码错误。
- 某些 `@cline/cli` e2e 断言 (`bun -F @cline/cli test:e2e`) 可能会在精确的工具列表字符串格式上失败；将其视为预先存在的测试漂移，而不是环境问题。

### GUI 显示
- 虚拟 X 显示位于 **`DISPLAY=:1`**（与屏幕截图相同的桌面）。 GUI 应用程序（VS Code，Tauri 桌面窗口）随 `DISPLAY=:1` 一起启动，并可进行屏幕截图 — 无需启动您自己的 `xvfb`。更喜欢在 `tmux` 会话中启动长时间运行的 GUI/dev 进程（请参阅 tmux 指南），以便它们能够生存。

### VS Code 扩展（`apps/vscode`，封装 `claude-dev`）
工具链已预先安装并保存在 VM 中：生成的 gRPC/proto 代码、捆绑的 `ripgrep` 二进制文件 (`apps/vscode/bin/`)、构建的 webview (`webview-ui/build`)、esbuild 捆绑包 (`dist/extension.js`)、 VS Code 本身 (`/usr/bin/code`) 及其测试所需的 GUI 系统库。
- **Codegen先决条件：** `bun run protos`（来自`apps/vscode`）重新生成`src/generated/*`和webview grpc客户端。 `dev`、`build:webview` 和 `check-types` 脚本已经运行它，因此这些命令会拾取原始更改；仅当您在没有完整构建的情况下编辑 `.proto` 文件时才手动运行它。
- **构建：** `bun run build:webview`（webview UI，〜15s）然后`bun esbuild.mjs`（扩展包）。 `bun run package` 进行完整的生产构建。
- **运行它（开发主机）：** `DISPLAY=:1 code --no-sandbox --user-data-dir=/tmp/vscode-userdata --extensionDevelopmentPath=/workspace/apps/vscode <some-folder>`，然后单击活动栏中的Cline图标打开webview。 （此容器中需要 `--no-sandbox`。）
- **测试：** `bun run test:unit`（基于bun，〜984测试，不需要VS Code主机）。 `bun run test:integration`（`@vscode/test-electron`，下载 VS Code 版本，在 GUI 库下运行）和 `bun run test:e2e`（Playwright）使用真正的扩展主机 - 更重，并且它们的 GUI 库已经安装。
- 一次性 deps（已安装，此处列出，以防必须重新创建）：ripgrep via `bun run download-ripgrep`； VS Code 根据 `CONTRIBUTING.md` 测试 GUI 库（`libnss3`、`libatk*`、`libgbm1`、`xvfb` 等）。

### 桌面应用程序（`apps/examples/desktop-app`，包`@cline/code`）
Tauri v2 (Rust) shell + Next.js webview + Bun“sidecar”后端。 Rust 和 Tauri Linux 系统库已预先安装并保留。
- **Headless（无Rust/window）：**分别运行后端和UI - `bun run dev:sidecar`（Bun后端在`127.0.0.1:3126`上，服务`ws://.../transport`）和 `bun run dev:web`（Next.js UI 在 `http://localhost:3125` 上）。
- **本机窗口：** `bun run dev` (`tauri dev`) — 其 `beforeDevCommand` 构建 sidecar 二进制文件并启动 `dev:web` (`:3125`)，然后启动 Rust `main.rs` 生成边车；所以首先释放端口`3125`/`3126`。使用 `DISPLAY=:1` 启动以查看该窗口。 `libEGL: DRI3 error` 警告是良性的（软件渲染）——WebKitGTK 窗口仍然渲染。
- **Rust 版本警告：** 板条箱图需要 Cargo 的 `edition2024` 功能，因此 **Rust ≥1.85** 是必需的（VM 的基础 1.83 失败，并显示“功能”需要 `edition2024`”）。这里的工具链是通过`rustup default stable`（目前是1.97）更新的。首先 `cargo` 构建 downloads/compiles 完整的 Tauri 箱图（几分钟）；后续构建会被缓存。
- **系统库（已安装）：** `libwebkit2gtk-4.1-dev`、`libgtk-3-dev`、`libayatana-appindicator3-dev`、`librsvg2-dev`、`libxdo-dev`、`libssl-dev`、 `build-essential`。
- **Test/typecheck：** `bun run typecheck`，`bun run test:chat-ui`（Vitest）。两者都首先触发`build:ui`。


## 原文对照

<details>
<summary>展开完整原文</summary>

This is the **Cline** monorepo. Toolchain is **Bun 1.4.2** (package manager + task runner) with **Node >=22** as the runtime. Do not use npm/yarn/pnpm.

Don't add `.changeset/` files or other per-PR changelog entries; changesets aren't used here. Release notes are written into the relevant `CHANGELOG.md` at release time.

## Cloud Agent Instructions

### Cline CLI
- Run from source: `bun run cli` (interactive: `bun run cli -i`; one-shot: append a prompt). This resolves to `apps/cli` and **auto-spawns the `@cline/cline-hub` daemon** — you do not start the hub separately.
- Inspect local health with `bun run cli doctor`; `bun run cli version` prints the version.
- An actual agent turn requires an **LLM provider credential**. With no credentials the default `cline` provider fails fast with an `Unauthorized` error and the interactive TUI shows a provider sign-in screen. Configure via `cline auth` or provider env vars (e.g. `ANTHROPIC_API_KEY`, `CLINE_API_KEY`, `OPENROUTER_API_KEY`); see `apps/cli/README.md`.

### Build / Lint / test
- SDK packages (`@cline/shared|llms|agents|core|sdk`) resolve each other through compiled `dist/` (their `exports` point only at `dist/`, with no `development` source condition). You **must** run `bun run build:sdk` after changing SDK dependencies/source before running the CLI or SDK tests, otherwise imports fail with missing `@cline/*` / missing `dist/` errors. Running processes do **not** hot-reload SDK source changes — rebuild and restart.\
- Known cloud-env test artifact: `@cline/core` test `src/services/workspace/workspace-manifest.test.ts > readGitWorkspaceState > prefers origin and returns the current branch` fails because cloud VMs configure git `insteadOf` rules that rewrite GitHub remotes to `https://x-access-token:...@github.com/...`. This is an environment artifact, not a code bug.
- Some `@cline/cli` e2e assertions (`bun -F @cline/cli test:e2e`) may fail on exact tool-listing string formats; treat as pre-existing test drift, not an environment problem.

### GUI display
- A virtual X display is live at **`DISPLAY=:1`** (the same desktop used for screenshots). GUI apps (VS Code, the Tauri desktop window) launched with `DISPLAY=:1` render there and can be screenshotted — no need to start your own `xvfb`. Prefer starting long-running GUI/dev processes in a `tmux` session (see the tmux guidance) so they survive.

### VS Code extension (`apps/vscode`, package `claude-dev`)
Toolchain is pre-installed and persisted in the VM: generated gRPC/proto code, the bundled `ripgrep` binaries (`apps/vscode/bin/`), the built webview (`webview-ui/build`), the esbuild bundle (`dist/extension.js`), VS Code itself (`/usr/bin/code`), and the GUI system libraries its tests need.
- **Codegen prerequisite:** `bun run protos` (from `apps/vscode`) regenerates `src/generated/*` and the webview grpc client. The `dev`, `build:webview`, and `check-types` scripts already run it, so proto changes are picked up by those commands; run it manually only if you edit `.proto` files without a full build.
- **Build:** `bun run build:webview` (webview UI, ~15s) then `bun esbuild.mjs` (extension bundle). `bun run package` does the full production build.
- **Run it (dev host):** `DISPLAY=:1 code --no-sandbox --user-data-dir=/tmp/vscode-userdata --extensionDevelopmentPath=/workspace/apps/vscode <some-folder>`, then click the Cline icon in the Activity Bar to open the webview. (`--no-sandbox` is required in this container.)
- **Test:** `bun run test:unit` (bun-based, ~984 tests, no VS Code host needed). `bun run test:integration` (`@vscode/test-electron`, downloads a VS Code build, runs under the GUI libs) and `bun run test:e2e` (Playwright) exercise a real extension host — heavier, and the GUI libs for them are already installed.
- One-time deps (already installed, listed here in case they must be recreated): ripgrep via `bun run download-ripgrep`; VS Code test GUI libs per `CONTRIBUTING.md` (`libnss3`, `libatk*`, `libgbm1`, `xvfb`, etc.).

### Desktop app (`apps/examples/desktop-app`, package `@cline/code`)
A Tauri v2 (Rust) shell + Next.js webview + a Bun "sidecar" backend. Rust and the Tauri Linux system libs are pre-installed and persisted.
- **Headless (no Rust/window):** run the backend and UI separately — `bun run dev:sidecar` (Bun backend on `127.0.0.1:3126`, serves `ws://.../transport`) and `bun run dev:web` (Next.js UI on `http://localhost:3125`).
- **Native window:** `bun run dev` (`tauri dev`) — its `beforeDevCommand` builds the sidecar binary and starts `dev:web` (`:3125`), then Rust `main.rs` spawns the sidecar; so free ports `3125`/`3126` first. Launch with `DISPLAY=:1` to see the window. A `libEGL: DRI3 error` warning is benign (software rendering) — the WebKitGTK window still renders.
- **Rust version caveat:** the crate graph needs Cargo's `edition2024` feature, so **Rust ≥1.85** is required (the VM's base 1.83 fails with "feature `edition2024` is required"). The toolchain here was updated via `rustup default stable` (currently 1.97). First `cargo` build downloads/compiles the full Tauri crate graph (a few minutes); subsequent builds are cached.
- **System libs (already installed):** `libwebkit2gtk-4.1-dev`, `libgtk-3-dev`, `libayatana-appindicator3-dev`, `librsvg2-dev`, `libxdo-dev`, `libssl-dev`, `build-essential`.
- **Test/typecheck:** `bun run typecheck`, `bun run test:chat-ui` (Vitest). Both trigger `build:ui` first.


</details>
