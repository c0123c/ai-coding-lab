# Codex Rust 工作区的 Bazel 构建

介绍构建目录、工具链、运行方式、远端配置和 BuildBuddy；属于 Codex 仓库开发构建资料。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 存档版本：`5ef96ab2785f`。
- 文档时间：源码提交（存档版）：[2026-10-08](<https://github.com/openai/codex/commit/3b6ab3471a2d4d994c9fad322fca749564bc05e0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/codex/blob/main/codex-rs/docs/bazel.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex Rust 工作区的 Bazel 构建.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Codex-rs 中的 Bazel

该存储库使用 Bazel 在 `codex-rs` 下构建 Rust 工作区。
Cargo 仍然是包和功能的真实来源，而 Bazel
 提供密封构建、工具链和跨平台工件。

从 6/1/2026 开始，此设置仍处于实验阶段，我们正在稳定它。

## 高层布局

- `../MODULE.bazel` 定义 Bazel 依赖项和 Rust 工具链。
- `rules_rs` 从 `codex-rs/Cargo.toml` 导入第三方 crate，并且
`codex-rs/Cargo.lock` 通过 `crate.from_cargo(...)` 并将它们暴露在 
 `@crates` 下。
- `../defs.bzl`提供`codex_rust_crate`，它包装`rust_library`，
`rust_binary` 和 `rust_test` 因此 Bazel 目标符合 Cargo 约定。
 它提供了一组适用于大多数第一方 crate 的合理默认值，但在某些情况下可能 
 需要调整。
- `codex-rs/*/BUILD.bazel` 中的每个板条箱通常使用 `codex_rust_crate` 和
如果包需要额外的编译时或运行时数据，
 或其他自定义，请进行一些调整。

## 本地运行 Bazel

存储库根 `justfile` 公开了常见的 Bazel 入口点：

```bash
just bazel-test
just bazel-clippy
```

普通本地 `bazel` 和 `just` 调用在本地运行。 BuildBuddy缓存，
构建事件上传、下载和远程执行是可选配置。

## BuildBuddy

Codex 使用 BuildBuddy 进行共享 Bazel 缓存以及远程构建和测试。要使用它
来加速构建和测试，您需要提供API密钥并选择
配置。

### BuildBuddy API 键

如果您是 OpenAI 员工，请登录 https://openai.buildbuddy.io 并使用 Google 登录。

按照 BuildBuddy 的 [ 身份验证指南 ][bb-auth-guide]、
 中所述创建 BuildBuddy API 密钥，然后将其添加到`~/.bazelrc`：

```bazelrc
# Local machine only; this file contains a BuildBuddy credential.
common --remote_header=x-buildbuddy-api-key=<your-buildbuddy-api-key>
```

将凭证保留在工作区之外可以降低意外 
 提交凭证的风险。

如果不同的项目需要不同的API密钥，请将API密钥放入
`%workspace%/user.bazelrc`中。签入的 `.bazelrc` 可以选择导入 
 该文件，并且 `.gitignore` 会排除它。请勿提交或共享包含 
 凭据的文件。

[bb-auth-guide]: <https://www.buildbuddy.io/docs/guide-auth/#managing-keys>

### 选择远程构建配置

OpenAI 员工应默认使用具有远程执行功能的 OpenAI 主机，除非 
 他们有理由选择其他配置。将以下配置
添加到`%workspace%/user.bazelrc`：

```bazelrc
common --config=buildbuddy-openai-rbe
```

OpenAI 不想远程执行的员工可以使用`buildbuddy-openai`。外部用户
应使用`buildbuddy-generic-rbe`或`buildbuddy-generic`。有关这些 
 配置的详细信息，请参阅下文。

### 所有远程配置

GitHub 操作通过 
`.github/scripts/run_bazel_with_buildbuddy.py` 路由 Bazel 构建和输出分辨率命令。 
`.github/scripts/run-bazel-ci.sh` 和 `.github/scripts/rusty_v8_bazel.py`
 等更高级别的帮助程序将远程配置选择委托给该包装器。包装器读取 
GitHub 操作存储库和事件负载，而不是依赖工作流 
 文件来复制租户选择逻辑。它还规范了 GitHub 操作 
 启动选项，以便作业中启动的所有 Bazel 重用同一服务器和 
 内存分析缓存。目标发现和锁定文件帮助程序委托给
相同的包装器，因此它们的调用者不需要选择CI-特定的启动选项。

加载阶段目标发现 `bazel query` 命令在本地运行，因为它们 
 仅枚举标签，不需要远程缓存或执行。

`Cache/BES`主机也用于远程下载。

| Invocation/config                           |需要钥匙| Cache/BES              |构建执行|测试执行程序|
| ------------------------------------------- | ------------ | ---------------------- | ---------- | --------- |
| `bazel ...`                                 |否|无|本地|本地|
| `bazel ... --config=buildbuddy-generic`     |是的| `remote.buildbuddy.io` |本地|本地|
| `bazel ... --config=buildbuddy-generic-rbe` |是的| `remote.buildbuddy.io` |远程|远程|
| `bazel ... --config=buildbuddy-openai`      |是的| `openai.buildbuddy.io` |本地|本地|
| `bazel ... --config=buildbuddy-openai-rbe`  |是的| `openai.buildbuddy.io` |远程|远程|

如果没有 API 密钥，包装器将删除远程 CI 配置并在本地运行 
。使用密钥，工作流可以按如下方式选择主机：

|运行|钥匙|使用 OpenAI BuildBuddy 主机|
| ------------------------------------------------ | --- | --------------------------- |
|推送到`openai/codex`中的`main`|是的|是的|
|`workflow_dispatch` 在 `openai/codex` 中|是的|是的|
|`openai/codex` 中的同一存储库拉取请求|是的|是的|
|将拉取请求分叉到 `openai/codex`|否|不；本地的|
|用钥匙将或 `workflow_dispatch` 推入叉子中|是的|不；通用主机|
|使用密钥在分叉存储库中运行拉取请求|是的|不；通用主机|

CI 配置确定构建和测试是否远程执行：

|CI配置|远程配置|构建执行|测试执行程序|
| ------------------- | ------------- | ----------- | ----------- |
| `ci-linux`          | `*-rbe`       |远程主机|远程主机|
| `ci-v8`             | `*-rbe`       |远程主机|远程主机|
| `ci-macos`          | `*-rbe`       |远程主机|本地|
| `ci-windows-cross`  | `*-rbe`       |远程主机|本地|
| `ci-windows`        |非RBE|本地|本地|
|无钥匙 CI 后备|无|本地|本地|

要使用您的密钥执行通用远程配置：

```bash
BUILDBUDDY_API_KEY=... GITHUB_REPOSITORY=my-fork/codex \
  ./.github/scripts/run_bazel_with_buildbuddy.py \
  build --config=ci-linux //codex-rs/cli:codex
```

包装器仅在 GitHub 操作中选择 OpenAI 主机，以在 `openai/codex` 中进行可信的 
 运行。丢失或格式错误的拉取请求事件 
 有效负载无法关闭通用主机。对于本地 OpenAI 主机访问，请使用上面的 
 `user.bazelrc` 配置。

## 改进设置

当您添加或更改 Rust 依赖项时，请照常更新 Cargo.toml/Cargo.lock。
然后从存储库根目录刷新 Bzlmod 锁定文件：

```bash
just bazel-lock-update
```

这将运行 `bazel mod deps --lockfile_mode=update` 并根据需要更新 `MODULE.bazel.lock`。
提交锁定文件更改以及 Cargo 锁定文件更新。

要在本地验证锁定文件对齐（运行相同的检查 CI），请使用：

```bash
just bazel-lock-check
```

在某些情况下，上游包可能需要补丁或 `../MODULE.bzl`
 中的 `crate.annotation` 才能将其构建在 Bazel 的沙箱中或使其易于交叉编译。如果您发现问题，
请随时 ping zbarsky 或 mbolin。

当您添加新的 crate 或二进制文件时：

1. 像往常一样将其添加到 Cargo 工作区。
2. 创建一个调用 `codex_rust_crate` 的 `BUILD.bazel`（查看附近的板条箱
示例）。
3. 如果依赖项需要特殊处理（compile/runtime 数据、其他二进制文件
对于集成测试、环境变量等），您可能需要将参数调整为 
 `codex_rust_crate` 来配置它。
 一种常见的自定义设置是设置 `test_tags = ["no-sandbox]` 以在未沙盒的情况下运行测试 
。宁愿避免它，但在某些情况下是必要的，例如当
测试本身使用安全带时（沙箱也这样做，并且不能嵌套）。
要限制爆炸半径，请考虑将此类测试隔离到单独的板条箱中。

如果您发现构建问题并且不确定如何应用正确的自定义，请随时 ping zbarsky 或 mbolin。

## 参考文献

- Bazel 概述：https://bazel.build/
- Bzlmod（模块系统）：https://bazel.build/external/overview
- rules_rust: https://github.com/bazelbuild/rules_rust
- rules_rs: https://github.com/bazelbuild/rules_rs


## 原文对照

<details>
<summary>展开完整原文</summary>

# Bazel in codex-rs

This repository uses Bazel to build the Rust workspace under `codex-rs`.
Cargo remains the source of truth for crates and features, while Bazel
provides hermetic builds, toolchains, and cross-platform artifacts.

As of 6/1/2026, this setup is still experimental as we stabilize it.

## High-level layout

- `../MODULE.bazel` defines Bazel dependencies and Rust toolchains.
- `rules_rs` imports third-party crates from `codex-rs/Cargo.toml` and
  `codex-rs/Cargo.lock` via `crate.from_cargo(...)` and exposes them under
  `@crates`.
- `../defs.bzl` provides `codex_rust_crate`, which wraps `rust_library`,
  `rust_binary`, and `rust_test` so Bazel targets line up with Cargo conventions.
  It provides a sane set of defaults that work for most first-party crates, but may
  need tweaks in some cases.
- Each crate in `codex-rs/*/BUILD.bazel` typically uses `codex_rust_crate` and
  makes some adjustments if the crate needs additional compile-time or runtime data,
  or other customizations.

## Running Bazel locally

The repository root `justfile` exposes the common Bazel entry points:

```bash
just bazel-test
just bazel-clippy
```

Ordinary local `bazel` and `just` invocations run locally. BuildBuddy cache,
build event upload, downloads, and remote execution are opt-in configurations.

## BuildBuddy

Codex uses BuildBuddy for a shared Bazel cache and remoted builds and tests. To use it
to speed up your builds and tests you'll need to provide an API key and select a
configuration.

### BuildBuddy API key

If you're an OpenAI employee, log in to https://openai.buildbuddy.io and use Google sign-in.

Create a BuildBuddy API key as described in BuildBuddy's [Authentication Guide][bb-auth-guide],
then add it to `~/.bazelrc`:

```bazelrc
# Local machine only; this file contains a BuildBuddy credential.
common --remote_header=x-buildbuddy-api-key=<your-buildbuddy-api-key>
```

Keeping the credential outside the workspace reduces the risk of accidentally
committing it.

If you need different API keys for different projects, put the API key in
`%workspace%/user.bazelrc` instead. The checked-in `.bazelrc` optionally imports
that file, and `.gitignore` excludes it. Do not commit or share a file containing
the credential.

[bb-auth-guide]: <https://www.buildbuddy.io/docs/guide-auth/#managing-keys>

### Selecting a remote build configuration

OpenAI employees should default to the OpenAI host with remote execution unless
they have a reason to choose another configuration. Add the following configuration
to `%workspace%/user.bazelrc`:

```bazelrc
common --config=buildbuddy-openai-rbe
```

OpenAI employees who don't want remote execution can use `buildbuddy-openai`. External users
should use `buildbuddy-generic-rbe` or `buildbuddy-generic`. See below for details on these
configurations.

### All remote configurations

GitHub Actions routes Bazel build and output-resolution commands through
`.github/scripts/run_bazel_with_buildbuddy.py`. Higher-level helpers such as
`.github/scripts/run-bazel-ci.sh` and `.github/scripts/rusty_v8_bazel.py`
delegate remote configuration selection to that wrapper. The wrapper reads the
GitHub Actions repository and event payload rather than relying on workflow
files to duplicate tenant-selection logic. It also normalizes GitHub Actions
startup options so all Bazel launches in a job reuse the same server and
in-memory analysis cache. Target-discovery and lockfile helpers delegate to the
same wrapper so their callers do not need to select CI-specific startup options.

Loading-phase target-discovery `bazel query` commands run locally because they
only enumerate labels and do not need remote caches or execution.

The `Cache/BES` host is also used for remote downloads.

| Invocation/config                           | Key Required | Cache/BES              | Build exec | Test exec |
| ------------------------------------------- | ------------ | ---------------------- | ---------- | --------- |
| `bazel ...`                                 | No           | None                   | Local      | Local     |
| `bazel ... --config=buildbuddy-generic`     | Yes          | `remote.buildbuddy.io` | Local      | Local     |
| `bazel ... --config=buildbuddy-generic-rbe` | Yes          | `remote.buildbuddy.io` | Remote     | Remote    |
| `bazel ... --config=buildbuddy-openai`      | Yes          | `openai.buildbuddy.io` | Local      | Local     |
| `bazel ... --config=buildbuddy-openai-rbe`  | Yes          | `openai.buildbuddy.io` | Remote     | Remote    |

Without an API key, the wrapper removes remote CI configurations and runs
locally. With a key, workflows choose the host as follows:

| Run                                              | Key | Uses OpenAI BuildBuddy Host |
| ------------------------------------------------ | --- | --------------------------- |
| Push to `main` in `openai/codex`                 | Yes | Yes                         |
| `workflow_dispatch` in `openai/codex`            | Yes | Yes                         |
| Same-repository pull request in `openai/codex`   | Yes | Yes                         |
| Fork pull request into `openai/codex`            | No  | No; local                   |
| Push or `workflow_dispatch` in a fork with a key | Yes | No; generic host            |
| Pull request run in a fork repository with a key | Yes | No; generic host            |

CI configurations determine whether builds and tests execute remotely:

| CI config           | Remote config | Build exec  | Test exec   |
| ------------------- | ------------- | ----------- | ----------- |
| `ci-linux`          | `*-rbe`       | Remote host | Remote host |
| `ci-v8`             | `*-rbe`       | Remote host | Remote host |
| `ci-macos`          | `*-rbe`       | Remote host | Local       |
| `ci-windows-cross`  | `*-rbe`       | Remote host | Local       |
| `ci-windows`        | non-RBE       | Local       | Local       |
| Keyless CI fallback | none          | Local       | Local       |

To exercise the generic remote configuration with your key:

```bash
BUILDBUDDY_API_KEY=... GITHUB_REPOSITORY=my-fork/codex \
  ./.github/scripts/run_bazel_with_buildbuddy.py \
  build --config=ci-linux //codex-rs/cli:codex
```

The wrapper selects the OpenAI host only inside GitHub Actions for a trusted
run in `openai/codex`. A missing or malformed pull request event
payload fails closed to the generic host. For local OpenAI host access, use
the `user.bazelrc` configuration above.

## Evolving the setup

When you add or change Rust dependencies, update the Cargo.toml/Cargo.lock as normal.
Then refresh the Bzlmod lockfile from the repo root:

```bash
just bazel-lock-update
```

This runs `bazel mod deps --lockfile_mode=update` and updates `MODULE.bazel.lock` if needed.
Commit the lockfile changes along with your Cargo lockfile update.

To verify lockfile alignment locally (the same check CI runs), use:

```bash
just bazel-lock-check
```

In some cases, an upstream crate may need a patch or a `crate.annotation` in `../MODULE.bzl`
to have it build in Bazel's sandbox or make it cross-compilation-friendly. If you see issues,
feel free to ping zbarsky or mbolin.

When you add a new crate or binary:

1. Add it to the Cargo workspace as usual.
2. Create a `BUILD.bazel` that calls `codex_rust_crate` (see nearby crates for
   examples).
3. If a dependency needs special handling (compile/runtime data, additional binaries
   for integration tests, env vars, etc) you may need to adjust the parameters to
   `codex_rust_crate` to configure it.
   One common customization is setting `test_tags = ["no-sandbox]` to run the test
   unsandboxed. Prefer to avoid it, but it is necessary in some cases such as when the
   test itself uses Seatbelt (the sandbox does as well, and it cannot be nested).
   To limit the blast radius, consider isolating such tests to a separate crate.

If you see build issue and are not sure how to apply the proper customizations, feel free to ping zbarsky or mbolin.

## References

- Bazel overview: https://bazel.build/
- Bzlmod (module system): https://bazel.build/external/overview
- rules_rust: https://github.com/bazelbuild/rules_rust
- rules_rs: https://github.com/bazelbuild/rules_rs


</details>
