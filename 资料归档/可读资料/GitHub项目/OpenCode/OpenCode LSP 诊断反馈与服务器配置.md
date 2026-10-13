# OpenCode LSP 诊断反馈与服务器配置

产品指南。说明语言服务器支持、检测和启动机制、环境变量、初始化选项、关闭及自定义服务；存档正文说明 LSP 默认关闭。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-05-26](<https://github.com/anomalyco/opencode/commit/fdfd0afed7fddbe852ea53b5a75ce1ea8ad725a2>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：需要实际语言服务器与项目环境，诊断反馈不代替测试。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/lsp.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode LSP 诊断反馈与服务器配置.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: LSP Servers
description: OpenCode integrates with your LSP servers.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 可以与语言服务器协议 (LSP) 服务器集成，以使用诊断作为 agent 的反馈。

---

## 内置

OpenCode 附带多个适用于流行语言的内置 LSP 服务器：

|LSP 服务器|扩展|要求|
| ------------------ | ------------------------------------------------------------------- | ------------------------------------------------------------ |
|阿斯特罗|.astro|自动安装 Astro 项目|
|巴什|.sh、.bash、.zsh、.ksh|自动安装 bash-语言-服务器|
|铿锵|.c、.cpp、.cc、.cxx、.c++、.h、.hpp、.hh、.hxx、.h++|自动安装 C/C++ 项目|
|夏普|.cs、.csx|`.NET SDK`已安装|
|Clojure-lsp|.clj、.cljs、.cljc、.edn|`clojure-lsp`命令可用|
|飞镖|.dart|`dart`命令可用|
|德诺|.ts、.tsx、.js、.jsx、.mjs|`deno` 命令可用（自动检测 deno.json/deno.jsonc）|
|elixir-ls|.ex、.exs|`elixir`命令可用|
|埃斯林特|.ts、.tsx、.js、.jsx、.mjs、.cjs、.mts、.cts、.vue|`eslint` 项目中的依赖项|
|锐利|.fs、.fsi、.fsx、.fsscript|`.NET SDK`已安装|
|微光|.闪光|`gleam`命令可用|
|戈普尔斯|.go|`go`命令可用|
|高斯|.hs、.lhs|`haskell-language-server-wrapper`命令可用|
|jdtls|.java|`Java SDK (version 21+)`已安装|
|朱利亚斯|.jl|已安装 `julia` 和 `LanguageServer.jl`|
|kotlin-ls|.kt, .kts|自动安装 Kotlin 项目|
|lua-ls|.lua|自动安装 Lua 项目|
|尼克斯德|.nix|`nixd`命令可用|
|ocaml-lsp|.ml、.mli|`ocamllsp`命令可用|
|奥克斯林特|.ts、.tsx、.js、.jsx、.mjs、.cjs、.mts、.cts、.vue、.astro、.svelte|`oxlint` 项目中的依赖项|
|php intelephense|.php|自动安装 PHP 项目|
|棱镜|.prisma|`prisma`命令可用|
|皮赖特|.py、.pyi|`pyright` 依赖项已安装|
|剃须刀|.razor、.cshtml|安装了 `.NET SDK` 和 VS Code C# 扩展|
|ruby-lsp（rubocop）|.rb、.rake、.gemspec、.ru|`ruby` 和 `gem` 命令可用|
|生锈|.rs|`rust-analyzer`命令可用|
|源码包-lsp|.swift、.objc、.objcpp|已安装 `swift`（macOS 上的 `xcode`）|
|苗条的|.svelte|自动安装 Svelte 项目|
|地形|.tf、.tfvars|从 GitHub 版本自动安装|
|微雾|.typ、.typc|从 GitHub 版本自动安装|
|打字稿|.ts、.tsx、.js、.jsx、.mjs、.cjs、.mts、.cts|`typescript` 项目中的依赖项|
|视图|.vue|自动安装 Vue 项目|
|yaml-ls|.yaml、.yml|自动安装 Red Hat yaml-language-server|
|兹勒斯|.zig、.zon|`zig`命令可用|

LSP 默认禁用。启用后，服务器将在检测到上述文件扩展名之一并且满足要求时启动。

**注释：**

您可以通过将 `OPENCODE_DISABLE_LSP_DOWNLOAD` 环境变量设置为 `true` 来禁用自动 LSP 服务器下载。

---

## 它是如何运作的

当启用 LSP 且 opencode 打开文件时，它：

1. 根据所有已启用的 LSP 服务器检查文件扩展名。
2. 如果尚未运行，则启动相应的 LSP 服务器。

---

## 最佳实践

LSP 可以通过从语言服务器提供诊断来帮助 agent 查找并修复问题。这在某些项目中很有用，但并不总是积极的。

语言服务器可能会不同步、使用大量内存、因版本或项目而异，并减慢 agent 工作流程。在许多项目中，最好让 agent 直接运行 lint、类型检查或其他诊断 CLI 工具，这样错误就会反馈到 agent 循环中，而无需进行这些权衡。将这些命令记录在指令文件（例如 `AGENTS.md` 或技能）中，以便 agent 知道要运行什么。当您的项目受益于额外的语言服务器反馈时，启用 LSP。

---

## 配置

您可以通过 opencode 配置中的 `lsp` 部分启用和自定义 LSP 服务器。

要启用所有内置LSP服务器，请将`lsp`设置为`true`。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": true
}
```

在配置覆盖或自定义服务器时，使用对象来保持内置功能启用。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {}
}
```

每个配置的 LSP 服务器条目支持以下内容：

服务器条目需要 `command`，除非它们仅禁用服务器。

|财产|类型|描述|
| ---------------- | -------- | ------------------------------------------------- |
| `disabled`       |布尔值|将其设置为 `true` 以禁用 LSP 服务器|
| `command`        |字符串[]|启动LSP服务器的命令|
| `extensions`     |字符串[]|此 LSP 服务器应处理的文件扩展名|
| `env`            |对象|启动服务器时设置的环境变量|
| `initialization` |对象|发送到LSP服务器的初始化选项|

让我们看一些例子。

---

### 环境变量

启动LSP服务器时，使用`env`属性设置环境变量：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "rust": {
      "command": ["rust-analyzer"],
      "env": {
        "RUST_LOG": "debug"
      }
    }
  }
}
```

---

### 初始化选项

使用 `initialization` 属性将初始化选项传递到 LSP 服务器。这些是在 LSP `initialize` 请求期间发送的服务器特定设置：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "custom-lsp": {
      "command": ["custom-lsp-server", "--stdio"],
      "extensions": [".custom"],
      "initialization": {
        "preferences": {
          "importModuleSpecifierPreference": "relative"
        }
      }
    }
  }
}
```

**注释：**

初始化选项因 LSP 服务器而异。检查 LSP 服务器的文档以获取可用选项。

---

### 禁用 LSP 服务器

如果省略 `lsp`，则禁用所有 LSP 服务器。要在另一个配置启用所有 LSP 服务器后禁用它们，请将 `lsp` 设置为 `false`：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": false
}
```

要禁用 ** 特定的 ** LSP 服务器，请将 `disabled` 设置为 `true`：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "typescript": {
      "disabled": true
    }
  }
}
```

---

### 自定义 LSP 服务器

您可以通过指定命令和文件扩展名来添加自定义 LSP 服务器：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "custom-lsp": {
      "command": ["custom-lsp-server", "--stdio"],
      "extensions": [".custom"]
    }
  }
}
```

---

## 附加信息

### PHP 特勒芬塞

PHP Intelephense 通过许可证密钥提供高级功能。您可以通过将（仅）密钥放入位于以下位置的文本文件中来提供许可证密钥：

- 在macOS/Linux上：`$HOME/intelephense/license.txt`
- 在 Windows 上：`%USERPROFILE%/intelephense/license.txt`

该文件应仅包含许可证密钥，不包含其他内容。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode can integrate with Language Server Protocol (LSP) servers to use diagnostics as feedback for the agent.

---

## Built-in

OpenCode comes with several built-in LSP servers for popular languages:

| LSP Server         | Extensions                                                          | Requirements                                                 |
| ------------------ | ------------------------------------------------------------------- | ------------------------------------------------------------ |
| astro              | .astro                                                              | Auto-installs for Astro projects                             |
| bash               | .sh, .bash, .zsh, .ksh                                              | Auto-installs bash-language-server                           |
| clangd             | .c, .cpp, .cc, .cxx, .c++, .h, .hpp, .hh, .hxx, .h++                | Auto-installs for C/C++ projects                             |
| csharp             | .cs, .csx                                                           | `.NET SDK` installed                                         |
| clojure-lsp        | .clj, .cljs, .cljc, .edn                                            | `clojure-lsp` command available                              |
| dart               | .dart                                                               | `dart` command available                                     |
| deno               | .ts, .tsx, .js, .jsx, .mjs                                          | `deno` command available (auto-detects deno.json/deno.jsonc) |
| elixir-ls          | .ex, .exs                                                           | `elixir` command available                                   |
| eslint             | .ts, .tsx, .js, .jsx, .mjs, .cjs, .mts, .cts, .vue                  | `eslint` dependency in project                               |
| fsharp             | .fs, .fsi, .fsx, .fsscript                                          | `.NET SDK` installed                                         |
| gleam              | .gleam                                                              | `gleam` command available                                    |
| gopls              | .go                                                                 | `go` command available                                       |
| hls                | .hs, .lhs                                                           | `haskell-language-server-wrapper` command available          |
| jdtls              | .java                                                               | `Java SDK (version 21+)` installed                           |
| julials            | .jl                                                                 | `julia` and `LanguageServer.jl` installed                    |
| kotlin-ls          | .kt, .kts                                                           | Auto-installs for Kotlin projects                            |
| lua-ls             | .lua                                                                | Auto-installs for Lua projects                               |
| nixd               | .nix                                                                | `nixd` command available                                     |
| ocaml-lsp          | .ml, .mli                                                           | `ocamllsp` command available                                 |
| oxlint             | .ts, .tsx, .js, .jsx, .mjs, .cjs, .mts, .cts, .vue, .astro, .svelte | `oxlint` dependency in project                               |
| php intelephense   | .php                                                                | Auto-installs for PHP projects                               |
| prisma             | .prisma                                                             | `prisma` command available                                   |
| pyright            | .py, .pyi                                                           | `pyright` dependency installed                               |
| razor              | .razor, .cshtml                                                     | `.NET SDK` and VS Code C# extension installed                |
| ruby-lsp (rubocop) | .rb, .rake, .gemspec, .ru                                           | `ruby` and `gem` commands available                          |
| rust               | .rs                                                                 | `rust-analyzer` command available                            |
| sourcekit-lsp      | .swift, .objc, .objcpp                                              | `swift` installed (`xcode` on macOS)                         |
| svelte             | .svelte                                                             | Auto-installs for Svelte projects                            |
| terraform          | .tf, .tfvars                                                        | Auto-installs from GitHub releases                           |
| tinymist           | .typ, .typc                                                         | Auto-installs from GitHub releases                           |
| typescript         | .ts, .tsx, .js, .jsx, .mjs, .cjs, .mts, .cts                        | `typescript` dependency in project                           |
| vue                | .vue                                                                | Auto-installs for Vue projects                               |
| yaml-ls            | .yaml, .yml                                                         | Auto-installs Red Hat yaml-language-server                   |
| zls                | .zig, .zon                                                          | `zig` command available                                      |

LSP is disabled by default. When enabled, servers start when one of the above file extensions is detected and the requirements are met.

**注释：**

You can disable automatic LSP server downloads by setting the `OPENCODE_DISABLE_LSP_DOWNLOAD` environment variable to `true`.

---

## How It Works

When LSP is enabled and opencode opens a file, it:

1. Checks the file extension against all enabled LSP servers.
2. Starts the appropriate LSP server if not already running.

---

## Best Practices

LSP can help the agent find and fix issues by providing diagnostics from language servers. This is useful in some projects, but it is not always a net positive.

Language servers can get out of sync, use significant memory, vary by version or project, and slow down agent workflows. In many projects it is better to have the agent run lint, typecheck, or other diagnostic CLI tools directly, so errors are fed back into the agent loop without those tradeoffs. Document those commands in instruction files such as `AGENTS.md` or skills so the agent knows what to run. Enable LSP when your project benefits from additional language-server feedback.

---

## Configure

You can enable and customize LSP servers through the `lsp` section in your opencode config.

To enable all built-in LSP servers, set `lsp` to `true`.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": true
}
```

Use an object to keep built-ins enabled while configuring overrides or custom servers.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {}
}
```

Each configured LSP server entry supports the following:

Server entries need `command` unless they only disable a server.

| Property         | Type     | Description                                       |
| ---------------- | -------- | ------------------------------------------------- |
| `disabled`       | boolean  | Set this to `true` to disable the LSP server      |
| `command`        | string[] | The command to start the LSP server               |
| `extensions`     | string[] | File extensions this LSP server should handle     |
| `env`            | object   | Environment variables to set when starting server |
| `initialization` | object   | Initialization options to send to the LSP server  |

Let's look at some examples.

---

### Environment variables

Use the `env` property to set environment variables when starting the LSP server:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "rust": {
      "command": ["rust-analyzer"],
      "env": {
        "RUST_LOG": "debug"
      }
    }
  }
}
```

---

### Initialization options

Use the `initialization` property to pass initialization options to the LSP server. These are server-specific settings sent during the LSP `initialize` request:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "custom-lsp": {
      "command": ["custom-lsp-server", "--stdio"],
      "extensions": [".custom"],
      "initialization": {
        "preferences": {
          "importModuleSpecifierPreference": "relative"
        }
      }
    }
  }
}
```

**注释：**

Initialization options vary by LSP server. Check your LSP server's documentation for available options.

---

### Disabling LSP servers

If `lsp` is omitted, all LSP servers are disabled. To disable all LSP servers after another config enabled them, set `lsp` to `false`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": false
}
```

To disable a **specific** LSP server, set `disabled` to `true`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "typescript": {
      "disabled": true
    }
  }
}
```

---

### Custom LSP servers

You can add custom LSP servers by specifying the command and file extensions:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "custom-lsp": {
      "command": ["custom-lsp-server", "--stdio"],
      "extensions": [".custom"]
    }
  }
}
```

---

## Additional Information

### PHP Intelephense

PHP Intelephense offers premium features through a license key. You can provide a license key by placing (only) the key in a text file at:

- On macOS/Linux: `$HOME/intelephense/license.txt`
- On Windows: `%USERPROFILE%/intelephense/license.txt`

The file should contain only the license key with no additional content.


</details>
