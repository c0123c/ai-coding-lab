# 从源码安装与构建 Codex

说明系统前提、DotSlash 固定可执行版本、Rust 构建和诊断日志，供运行或调试仓库实现时参考。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 存档版本：`5ef96ab2785f`。
- 文档时间：源码提交（存档版）：[2026-06-14](<https://github.com/openai/codex/commit/740c4f269de8db915dde9d238ec0b8da6339aa56>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/codex/blob/main/docs/install.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/docs/install.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

## 安装和建造

### 系统要求

|要求|详情|
| --------------------------- | --------------------------------------------------------------- |
|操作系统|macOS 12+，Ubuntu 20.04+/Debian 10+，或 Windows 11 **通过 WSL2**|
|Git（可选，推荐）|2.23+ 用于内置 PR 助手|
| RAM                         |4-GB 最小值（8-GB 推荐）|

### DotSlash

GitHub 版本还包含名为 `codex` 的 Codex CLI 的 [DotSlash](<https://dotslash-cli.com/>) 文件。使用 DotSlash 文件可以对源代码控制进行轻量级提交，以确保所有贡献者使用相同版本的可执行文件，无论他们使用什么平台进行开发。

### 从源代码构建

```bash
# Clone the repository and navigate to the root of the Cargo workspace.
git clone https://github.com/openai/codex.git
cd codex/codex-rs

# Install the Rust toolchain, if necessary.
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source "$HOME/.cargo/env"
rustup component add rustfmt
rustup component add clippy
# Install helper tools used by the workspace justfile:
cargo install --locked just
# DotSlash fetches pinned development tools such as buildifier on first use.
cargo install --locked dotslash
# Install nextest for the `just test` helper.
cargo install --locked cargo-nextest

# Build Codex.
cargo build

# Launch the TUI with a sample prompt.
cargo run --bin codex -- "explain this codebase to me"

# After making changes, use the root justfile helpers (they default to codex-rs):
just fmt
just fix -p <crate-you-touched>

# Run the relevant tests (project-specific is fastest), for example:
just test -p codex-tui
# `just test` runs the test suite via nextest:
just test
# Avoid `--all-features` for routine local runs because it increases build
# time and `target/` disk usage by compiling additional feature combinations.
```

## 跟踪/详细日志记录

Codex 是用 Rust 编写的，因此它遵循 `RUST_LOG` 环境变量来配置其日志记录行为。

默认情况下，TUI 在有界本地存储中记录诊断信息。显式设置 `log_dir` 以启用运行的明文 TUI 日志：

```bash
codex -c log_dir=./.codex-log
tail -F ./.codex-log/codex-tui.log
```

非交互模式（`codex exec`）默认为`RUST_LOG=error`，但消息是内联打印的，因此不需要监视单独的文件。

有关配置选项的更多信息，请参阅 [`RUST_LOG`](<https://docs.rs/env_logger/latest/env_logger/#enabling-logging>) 上的 Rust 文档。


## 原文对照

<details>
<summary>展开完整原文</summary>

## Installing & building

### System requirements

| Requirement                 | Details                                                         |
| --------------------------- | --------------------------------------------------------------- |
| Operating systems           | macOS 12+, Ubuntu 20.04+/Debian 10+, or Windows 11 **via WSL2** |
| Git (optional, recommended) | 2.23+ for built-in PR helpers                                   |
| RAM                         | 4-GB minimum (8-GB recommended)                                 |

### DotSlash

The GitHub Release also contains a [DotSlash](<https://dotslash-cli.com/>) file for the Codex CLI named `codex`. Using a DotSlash file makes it possible to make a lightweight commit to source control to ensure all contributors use the same version of an executable, regardless of what platform they use for development.

### Build from source

```bash
# Clone the repository and navigate to the root of the Cargo workspace.
git clone https://github.com/openai/codex.git
cd codex/codex-rs

# Install the Rust toolchain, if necessary.
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source "$HOME/.cargo/env"
rustup component add rustfmt
rustup component add clippy
# Install helper tools used by the workspace justfile:
cargo install --locked just
# DotSlash fetches pinned development tools such as buildifier on first use.
cargo install --locked dotslash
# Install nextest for the `just test` helper.
cargo install --locked cargo-nextest

# Build Codex.
cargo build

# Launch the TUI with a sample prompt.
cargo run --bin codex -- "explain this codebase to me"

# After making changes, use the root justfile helpers (they default to codex-rs):
just fmt
just fix -p <crate-you-touched>

# Run the relevant tests (project-specific is fastest), for example:
just test -p codex-tui
# `just test` runs the test suite via nextest:
just test
# Avoid `--all-features` for routine local runs because it increases build
# time and `target/` disk usage by compiling additional feature combinations.
```

## Tracing / verbose logging

Codex is written in Rust, so it honors the `RUST_LOG` environment variable to configure its logging behavior.

The TUI records diagnostics in bounded local stores by default. Set `log_dir` explicitly to enable a plaintext TUI log for a run:

```bash
codex -c log_dir=./.codex-log
tail -F ./.codex-log/codex-tui.log
```

The non-interactive mode (`codex exec`) defaults to `RUST_LOG=error`, but messages are printed inline, so there is no need to monitor a separate file.

See the Rust documentation on [`RUST_LOG`](<https://docs.rs/env_logger/latest/env_logger/#enabling-logging>) for more information on the configuration options.


</details>
