# Codex npm 包发布说明

介绍生成 CLI、代理及 SDK 发布包的暂存工具与原生构件准备；属于仓库发布维护资料。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 存档版本：`5ef96ab2785f`。
- 文档时间：源码提交（存档版）：[2026-09-02](<https://github.com/openai/codex/commit/ddf8a67ab09cd76b8adc0969f11ee1271179aba7>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/codex/blob/main/codex-cli/scripts/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex npm 包发布说明.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# npm 发布

使用存储库根目录中的暂存帮助程序生成用于发布的 npm tarball。对于 
 示例，要为版本 `0.6.0` 暂存 CLI、响应代理和 SDK 包：

```bash
./scripts/stage_npm_packages.py \
  --release-version 0.6.0 \
  --package codex \
  --package codex-responses-api-proxy \
  --package codex-sdk
```

这将下载所需的本机包存档工件，为 
 每个包水合 `vendor/`，并将 tarball 写入 `dist/npm/`。

当提供 `--package codex` 时，登台助手会构建轻量级 
`@openai/codex` 元包以及所有平台本机 `@openai/codex` 变体 
，这些变体稍后在特定于平台的 dist 标签下发布。

直接 `build_npm_package.py` 调用对于特定于包的 
 调试仍然有用，但本机包期望 `--vendor-src` 指向预水化的 
`vendor/` 树。发布包装应使用`scripts/stage_npm_packages.py`


## 原文对照

<details>
<summary>展开完整原文</summary>

# npm releases

Use the staging helper in the repo root to generate npm tarballs for a release. For
example, to stage the CLI, responses proxy, and SDK packages for version `0.6.0`:

```bash
./scripts/stage_npm_packages.py \
  --release-version 0.6.0 \
  --package codex \
  --package codex-responses-api-proxy \
  --package codex-sdk
```

This downloads the required native package archive artifacts, hydrates `vendor/` for
each package, and writes tarballs to `dist/npm/`.

When `--package codex` is provided, the staging helper builds the lightweight
`@openai/codex` meta package plus all platform-native `@openai/codex` variants
that are later published under platform-specific dist-tags.

Direct `build_npm_package.py` invocations are still useful for package-specific
debugging, but native packages expect `--vendor-src` to point at a prehydrated
`vendor/` tree. Release packaging should use `scripts/stage_npm_packages.py`


</details>
