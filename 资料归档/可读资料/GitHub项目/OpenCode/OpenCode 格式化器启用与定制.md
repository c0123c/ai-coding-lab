# OpenCode 格式化器启用与定制

产品指南。说明写入和编辑后的格式化机制、支持语言、配置、禁用及自定义命令；存档正文说明默认关闭，需要显式启用。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-05-03](<https://github.com/anomalyco/opencode/commit/d10fb88b66181ab710b768a23317c0b972bcd9c5>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：需要对应格式化工具和项目配置，不能推断保存后默认已格式化。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/formatters.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 格式化器启用与定制.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Formatters
description: OpenCode uses language specific formatters.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 可以在使用特定于语言的格式化程序编写或编辑文件后对其进行格式化。默认情况下禁用格式化程序；在 OpenCode 运行它们之前，在您的配置中启用它们。

---

## 内置

OpenCode 附带了多个适用于流行语言和框架的内置格式化程序。下面是格式化程序、支持的文件扩展名以及所需的命令或配置选项的列表。

|格式化程序|扩展|要求|
| -------------------- | -------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
|空气| .R                                                                                                       |`air`命令可用|
|生物群系|.js、.jsx、.ts、.tsx、.html、.css、.md、.json、.yaml 和 [更多](<https://biomejs.dev/>)|`biome.json(c)` 配置文件|
|货运公司|.rs|`cargo fmt`命令可用|
|clang 格式|.c、.cpp、.h、.hpp、.ino 和 [更多](<https://clang.llvm.org/docs/ClangFormat.html>)|`.clang-format` 配置文件|
|克利夫马特|.clj、.cljs、.cljc、.edn|`cljfmt`命令可用|
|飞镖|.dart|`dart`命令可用|
|dfmt| .d                                                                                                       |`dfmt`命令可用|
|微光|.闪光|`gleam`命令可用|
|戈夫姆特|.go|`gofmt`命令可用|
|html美化器|.erb, .html.erb|`htmlbeautifier`命令可用|
|克特林特|.kt, .kts|`ktlint`命令可用|
|混合|.ex、.exs、.eex、.heex、.leex、.neex、.sface|`mix`命令可用|
|尼克斯夫MT|.nix|`nixfmt`命令可用|
|ocaml格式|.ml、.mli|`ocamlformat` 可用命令和 `.ocamlformat` 配置文件|
|奥尔莫卢|.hs|`ormolu`命令可用|
|oxfmt（实验）|.js、.jsx、.ts、.tsx|`oxfmt` `package.json` 中的依赖关系和 [ 实验环境变量标志 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode CLI 命令、参数与环境变量.md#experimental>)|
|品脱|.php|`laravel/pint` `composer.json` 中的依赖关系|
|更漂亮|.js、.jsx、.ts、.tsx、.html、.css、.md、.json、.yaml 和 [更多](<https://prettier.io/docs/en/index.html>)|`prettier` `package.json` 中的依赖关系|
|鲁博科普|.rb、.rake、.gemspec、.ru|`rubocop`命令可用|
|领子|.py、.pyi|`ruff` 命令可通过配置使用|
|铁锈|.rs|`rustfmt`命令可用|
|转移|.sh、.bash|`shfmt`命令可用|
|标准RB|.rb、.rake、.gemspec、.ru|`standardrb`命令可用|
|地形|.tf、.tfvars|`terraform`命令可用|
| uv                   |.py、.pyi|`uv`命令可用|
|之字形|.zig、.zon|`zig`命令可用|

启用格式化程序后，如果您的项目在 `package.json` 中有 `prettier`，则 OpenCode 将使用 `prettier` 来匹配文件。

---

## 它是如何运作的

当 OpenCode 写入或编辑文件并且启用格式化程序时，它：

1. 根据所有启用的格式化程序检查文件扩展名。
2. 对文件运行适当的格式化程序命令。
3. 应用格式更改。

对于启用的格式化程序，此过程在后台发生。

---

## 配置

您可以通过 OpenCode 配置中的 `formatter` 部分启用和自定义格式化程序。

要启用所有内置格式化程序，请将 `formatter` 设置为 `true`。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": true
}
```

在配置覆盖或自定义格式化程序时，使用对象来保持内置功能启用。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": {}
}
```

每个格式化程序配置支持以下内容：

|财产|类型|描述|
| ------------- | -------- | ------------------------------------------------------------------------------------------ |
| `disabled`    |布尔值|将其设置为 `true` 以禁用格式化程序|
| `command`     |字符串[]|运行格式化命令。自定义格式化程序所需；对于内置程序是可选的。|
| `environment` |对象|运行格式化程序时要设置的环境变量|
| `extensions`  |字符串[]|此格式化程序应处理的文件扩展名|

让我们看一些例子。

---

### 禁用格式化程序

如果省略 `formatter`，则禁用所有格式化程序。要在另一个配置启用所有格式化程序后禁用它们，请将 `formatter` 设置为 `false`：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": false
}
```

要禁用 ** 特定的 ** 格式化程序，请将 `disabled` 设置为 `true`：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": {
    "prettier": {
      "disabled": true
    }
  }
}
```

---

### 自定义格式化程序

您可以使用 `environment` 或 `extensions` 等选项配置内置格式化程序。要添加自定义格式化程序，请指定 `command` 和 `extensions`：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": {
    "prettier": {
      "command": ["npx", "prettier", "--write", "$FILE"],
      "environment": {
        "NODE_ENV": "development"
      },
      "extensions": [".js", ".ts", ".jsx", ".tsx"]
    },
    "custom-markdown-formatter": {
      "command": ["deno", "fmt", "$FILE"],
      "extensions": [".md"]
    }
  }
}
```

命令中的 **`$FILE` 占位符 ** 将替换为正在格式化的文件的路径。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode can format files after they are written or edited using language-specific formatters. Formatters are disabled by default; enable them in your config before OpenCode will run them.

---

## Built-in

OpenCode comes with several built-in formatters for popular languages and frameworks. Below is a list of the formatters, supported file extensions, and commands or config options it needs.

| Formatter            | Extensions                                                                                               | Requirements                                                                                          |
| -------------------- | -------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| air                  | .R                                                                                                       | `air` command available                                                                               |
| biome                | .js, .jsx, .ts, .tsx, .html, .css, .md, .json, .yaml, and [more](<https://biomejs.dev/>)                   | `biome.json(c)` config file                                                                           |
| cargofmt             | .rs                                                                                                      | `cargo fmt` command available                                                                         |
| clang-format         | .c, .cpp, .h, .hpp, .ino, and [more](<https://clang.llvm.org/docs/ClangFormat.html>)                       | `.clang-format` config file                                                                           |
| cljfmt               | .clj, .cljs, .cljc, .edn                                                                                 | `cljfmt` command available                                                                            |
| dart                 | .dart                                                                                                    | `dart` command available                                                                              |
| dfmt                 | .d                                                                                                       | `dfmt` command available                                                                              |
| gleam                | .gleam                                                                                                   | `gleam` command available                                                                             |
| gofmt                | .go                                                                                                      | `gofmt` command available                                                                             |
| htmlbeautifier       | .erb, .html.erb                                                                                          | `htmlbeautifier` command available                                                                    |
| ktlint               | .kt, .kts                                                                                                | `ktlint` command available                                                                            |
| mix                  | .ex, .exs, .eex, .heex, .leex, .neex, .sface                                                             | `mix` command available                                                                               |
| nixfmt               | .nix                                                                                                     | `nixfmt` command available                                                                            |
| ocamlformat          | .ml, .mli                                                                                                | `ocamlformat` command available and `.ocamlformat` config file                                        |
| ormolu               | .hs                                                                                                      | `ormolu` command available                                                                            |
| oxfmt (Experimental) | .js, .jsx, .ts, .tsx                                                                                     | `oxfmt` dependency in `package.json` and an [experimental env variable flag](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode CLI 命令、参数与环境变量.md#experimental>) |
| pint                 | .php                                                                                                     | `laravel/pint` dependency in `composer.json`                                                          |
| prettier             | .js, .jsx, .ts, .tsx, .html, .css, .md, .json, .yaml, and [more](<https://prettier.io/docs/en/index.html>) | `prettier` dependency in `package.json`                                                               |
| rubocop              | .rb, .rake, .gemspec, .ru                                                                                | `rubocop` command available                                                                           |
| ruff                 | .py, .pyi                                                                                                | `ruff` command available with config                                                                  |
| rustfmt              | .rs                                                                                                      | `rustfmt` command available                                                                           |
| shfmt                | .sh, .bash                                                                                               | `shfmt` command available                                                                             |
| standardrb           | .rb, .rake, .gemspec, .ru                                                                                | `standardrb` command available                                                                        |
| terraform            | .tf, .tfvars                                                                                             | `terraform` command available                                                                         |
| uv                   | .py, .pyi                                                                                                | `uv` command available                                                                                |
| zig                  | .zig, .zon                                                                                               | `zig` command available                                                                               |

When formatters are enabled, OpenCode will use `prettier` for matching files if your project has `prettier` in `package.json`.

---

## How it works

When OpenCode writes or edits a file and formatters are enabled, it:

1. Checks the file extension against all enabled formatters.
2. Runs the appropriate formatter command on the file.
3. Applies the formatting changes.

This process happens in the background for enabled formatters.

---

## Configure

You can enable and customize formatters through the `formatter` section in your OpenCode config.

To enable all built-in formatters, set `formatter` to `true`.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": true
}
```

Use an object to keep built-ins enabled while configuring overrides or custom formatters.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": {}
}
```

Each formatter configuration supports the following:

| Property      | Type     | Description                                                                                |
| ------------- | -------- | ------------------------------------------------------------------------------------------ |
| `disabled`    | boolean  | Set this to `true` to disable the formatter                                                |
| `command`     | string[] | The command to run for formatting. Required for custom formatters; optional for built-ins. |
| `environment` | object   | Environment variables to set when running the formatter                                    |
| `extensions`  | string[] | File extensions this formatter should handle                                               |

Let's look at some examples.

---

### Disabling formatters

If `formatter` is omitted, all formatters are disabled. To disable all formatters after another config enabled them, set `formatter` to `false`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": false
}
```

To disable a **specific** formatter, set `disabled` to `true`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": {
    "prettier": {
      "disabled": true
    }
  }
}
```

---

### Custom formatters

You can configure built-in formatters with options like `environment` or `extensions`. To add a custom formatter, specify a `command` and `extensions`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": {
    "prettier": {
      "command": ["npx", "prettier", "--write", "$FILE"],
      "environment": {
        "NODE_ENV": "development"
      },
      "extensions": [".js", ".ts", ".jsx", ".tsx"]
    },
    "custom-markdown-formatter": {
      "command": ["deno", "fmt", "$FILE"],
      "extensions": [".md"]
    }
  }
}
```

The **`$FILE` placeholder** in the command will be replaced with the path to the file being formatted.


</details>
