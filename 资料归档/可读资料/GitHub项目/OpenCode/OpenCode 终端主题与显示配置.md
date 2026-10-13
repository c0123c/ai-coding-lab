# OpenCode 终端主题与显示配置

界面参考。介绍 Truecolor 要求、内置和系统主题、自定义主题文件、配置层次和颜色定义；主要用于界面使用与显示排查。

- 来源：OpenCode
- 资料类型：界面参考
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-02-25](<https://github.com/anomalyco/opencode/commit/9d29d692c6d93322f5894cca4232d80106e7c81a>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：与开发方法关系较弱，按实际显示需求查阅。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/themes.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 终端主题与显示配置.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Themes
description: Select a built-in theme or define your own.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

使用 OpenCode，您可以从多个内置主题之一中进行选择，使用适合您的终端主题的主题，或定义您自己的自定义主题。

默认情况下，OpenCode使用我们自己的`opencode`主题。

---

## 终端要求

为了使主题能够正确显示完整的调色板，您的终端必须支持 **truecolor**（24 位颜色）。大多数现代终端默认支持此功能，但您可能需要启用它：

- **检查支持**：运行`echo $COLORTERM` - 它应该输出`truecolor`或`24bit`
- **启用 truecolor**：在 shell 配置文件中设置环境变量 `COLORTERM=truecolor`
- **终端兼容性**：确保您的终端模拟器支持24位颜色（大多数现代终端，如iTerm2、Alacritty、Kitty、Windows Terminal和最新版本的GNOME Terminal）

如果没有真彩色支持，主题的颜色精度可能会降低，或者会回退到最接近的 256 颜色近似值。

---

## 内置主题

OpenCode 带有几个内置主题。

|名称|描述|
| ---------------------- | ---------------------------------------------------------------------------- |
| `system`               |适应您终端的背景颜色|
| `tokyonight`           |基于 [Tokyonight](<https://github.com/folke/tokyonight.nvim>) 主题|
| `everforest`           |基于[Everforest](<https://github.com/sainnhe/everforest>)主题|
| `ayu`                  |基于[Ayu](<https://github.com/ayu-theme>)黑暗主题|
| `catppuccin`           |基于 [Catppuccin](<https://github.com/catppuccin>) 主题|
| `catppuccin-macchiato` |基于 [Catppuccin](<https://github.com/catppuccin>) 主题|
| `gruvbox`              |基于 [Gruvbox](<https://github.com/morhetz/gruvbox>) 主题|
| `kanagawa`             |基于[神奈川](<https://github.com/rebelot/kanagawa.nvim>)主题|
| `nord`                 |基于 [Nord](<https://github.com/nordtheme/nord>) 主题|
| `matrix`               |黑客风格黑底绿主题|
| `one-dark`             |基于[Atom One](<https://github.com/Th3Whit3Wolf/one-nvim>)黑暗主题|

此外，我们还在不断添加新主题。

---

## 系统主题

`system` 主题旨在自动适应您终端的配色方案。与使用固定颜色的传统主题不同，_系统_主题：

- **生成灰度**：根据终端的背景颜色创建自定义灰度，确保最佳对比度。
- **使用ANSI颜色**：利用标准ANSI颜色（0-15）进行语法突出显示和UI元素，尊重终端的调色板。
- **保留终端默认值**：使用`none`作为文本和背景颜色以保持终端的本机外观。

系统主题适合以下用户：

- 希望 OpenCode 与他们的终端外观相匹配
- 使用自定义终端配色方案
- 希望所有终端应用程序具有一致的外观

---

## 使用主题

您可以通过使用 `/theme` 命令调出主题选择来选择主题。或者您可以在`tui.json`中指定。

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "theme": "tokyonight"
}
```

---

## 自定义主题

OpenCode支持灵活的基于JSON-的主题系统，允许用户轻松创建和自定义主题。

---

### 层次结构

主题按以下顺序从多个目录加载，其中后面的目录覆盖前面的目录：

1. **内置主题** - 这些主题嵌入在二进制文件中
2. **用户配置目录** - 在`~/.config/opencode/themes/*.json`或`$XDG_CONFIG_HOME/opencode/themes/*.json`中定义
3. **项目根目录** - 定义在`<project-root>/.opencode/themes/*.json`中
4. **当前工作目录** - 在`./.opencode/themes/*.json`中定义

如果多个目录包含同名主题，则将使用优先级较高的目录中的主题。

---

### 创建主题

要创建自定义主题，请在主题目录之一中创建 JSON 文件。

对于用户范围的主题：

```bash
mkdir -p ~/.config/opencode/themes
vim ~/.config/opencode/themes/my-theme.json
```

以及针对特定项目的主题。

```bash
mkdir -p .opencode/themes
vim .opencode/themes/my-theme.json
```

---

### JSON 格式

主题使用灵活的 JSON 格式，支持：

- **十六进制颜色**：`"#ffffff"`
- **ANSI 颜色**: `3` (0-255)
- **颜色参考**：`"primary"`或自定义
- **Dark/light 变体**：`{"dark": "#000", "light": "#fff"}`
- **无颜色**：`"none"` - 使用终端的默认颜色或透明

---

### 颜色定义

`defs` 部分是可选的，它允许您定义可在主题中引用的可重用颜色。

---

### 终端默认值

特殊值`"none"`可用于任何颜色以继承终端的默认颜色。这对于创建与终端配色方案无缝融合的主题特别有用：

- `"text": "none"` - 使用终端的默认前景色
- `"background": "none"` - 使用终端的默认背景颜色

---

### 示例

以下是自定义主题的示例：

**代码示例：** `my-theme.json`

```json
{
  "$schema": "https://opencode.ai/theme.json",
  "defs": {
    "nord0": "#2E3440",
    "nord1": "#3B4252",
    "nord2": "#434C5E",
    "nord3": "#4C566A",
    "nord4": "#D8DEE9",
    "nord5": "#E5E9F0",
    "nord6": "#ECEFF4",
    "nord7": "#8FBCBB",
    "nord8": "#88C0D0",
    "nord9": "#81A1C1",
    "nord10": "#5E81AC",
    "nord11": "#BF616A",
    "nord12": "#D08770",
    "nord13": "#EBCB8B",
    "nord14": "#A3BE8C",
    "nord15": "#B48EAD"
  },
  "theme": {
    "primary": {
      "dark": "nord8",
      "light": "nord10"
    },
    "secondary": {
      "dark": "nord9",
      "light": "nord9"
    },
    "accent": {
      "dark": "nord7",
      "light": "nord7"
    },
    "error": {
      "dark": "nord11",
      "light": "nord11"
    },
    "warning": {
      "dark": "nord12",
      "light": "nord12"
    },
    "success": {
      "dark": "nord14",
      "light": "nord14"
    },
    "info": {
      "dark": "nord8",
      "light": "nord10"
    },
    "text": {
      "dark": "nord4",
      "light": "nord0"
    },
    "textMuted": {
      "dark": "nord3",
      "light": "nord1"
    },
    "background": {
      "dark": "nord0",
      "light": "nord6"
    },
    "backgroundPanel": {
      "dark": "nord1",
      "light": "nord5"
    },
    "backgroundElement": {
      "dark": "nord1",
      "light": "nord4"
    },
    "border": {
      "dark": "nord2",
      "light": "nord3"
    },
    "borderActive": {
      "dark": "nord3",
      "light": "nord2"
    },
    "borderSubtle": {
      "dark": "nord2",
      "light": "nord3"
    },
    "diffAdded": {
      "dark": "nord14",
      "light": "nord14"
    },
    "diffRemoved": {
      "dark": "nord11",
      "light": "nord11"
    },
    "diffContext": {
      "dark": "nord3",
      "light": "nord3"
    },
    "diffHunkHeader": {
      "dark": "nord3",
      "light": "nord3"
    },
    "diffHighlightAdded": {
      "dark": "nord14",
      "light": "nord14"
    },
    "diffHighlightRemoved": {
      "dark": "nord11",
      "light": "nord11"
    },
    "diffAddedBg": {
      "dark": "#3B4252",
      "light": "#E5E9F0"
    },
    "diffRemovedBg": {
      "dark": "#3B4252",
      "light": "#E5E9F0"
    },
    "diffContextBg": {
      "dark": "nord1",
      "light": "nord5"
    },
    "diffLineNumber": {
      "dark": "nord2",
      "light": "nord4"
    },
    "diffAddedLineNumberBg": {
      "dark": "#3B4252",
      "light": "#E5E9F0"
    },
    "diffRemovedLineNumberBg": {
      "dark": "#3B4252",
      "light": "#E5E9F0"
    },
    "markdownText": {
      "dark": "nord4",
      "light": "nord0"
    },
    "markdownHeading": {
      "dark": "nord8",
      "light": "nord10"
    },
    "markdownLink": {
      "dark": "nord9",
      "light": "nord9"
    },
    "markdownLinkText": {
      "dark": "nord7",
      "light": "nord7"
    },
    "markdownCode": {
      "dark": "nord14",
      "light": "nord14"
    },
    "markdownBlockQuote": {
      "dark": "nord3",
      "light": "nord3"
    },
    "markdownEmph": {
      "dark": "nord12",
      "light": "nord12"
    },
    "markdownStrong": {
      "dark": "nord13",
      "light": "nord13"
    },
    "markdownHorizontalRule": {
      "dark": "nord3",
      "light": "nord3"
    },
    "markdownListItem": {
      "dark": "nord8",
      "light": "nord10"
    },
    "markdownListEnumeration": {
      "dark": "nord7",
      "light": "nord7"
    },
    "markdownImage": {
      "dark": "nord9",
      "light": "nord9"
    },
    "markdownImageText": {
      "dark": "nord7",
      "light": "nord7"
    },
    "markdownCodeBlock": {
      "dark": "nord4",
      "light": "nord0"
    },
    "syntaxComment": {
      "dark": "nord3",
      "light": "nord3"
    },
    "syntaxKeyword": {
      "dark": "nord9",
      "light": "nord9"
    },
    "syntaxFunction": {
      "dark": "nord8",
      "light": "nord8"
    },
    "syntaxVariable": {
      "dark": "nord7",
      "light": "nord7"
    },
    "syntaxString": {
      "dark": "nord14",
      "light": "nord14"
    },
    "syntaxNumber": {
      "dark": "nord15",
      "light": "nord15"
    },
    "syntaxType": {
      "dark": "nord7",
      "light": "nord7"
    },
    "syntaxOperator": {
      "dark": "nord9",
      "light": "nord9"
    },
    "syntaxPunctuation": {
      "dark": "nord4",
      "light": "nord0"
    }
  }
}
```


## 原文对照

<details>
<summary>展开完整原文</summary>

With OpenCode you can select from one of several built-in themes, use a theme that adapts to your terminal theme, or define your own custom theme.

By default, OpenCode uses our own `opencode` theme.

---

## Terminal requirements

For themes to display correctly with their full color palette, your terminal must support **truecolor** (24-bit color). Most modern terminals support this by default, but you may need to enable it:

- **Check support**: Run `echo $COLORTERM` - it should output `truecolor` or `24bit`
- **Enable truecolor**: Set the environment variable `COLORTERM=truecolor` in your shell profile
- **Terminal compatibility**: Ensure your terminal emulator supports 24-bit color (most modern terminals like iTerm2, Alacritty, Kitty, Windows Terminal, and recent versions of GNOME Terminal do)

Without truecolor support, themes may appear with reduced color accuracy or fall back to the nearest 256-color approximation.

---

## Built-in themes

OpenCode comes with several built-in themes.

| Name                   | Description                                                                  |
| ---------------------- | ---------------------------------------------------------------------------- |
| `system`               | Adapts to your terminal’s background color                                   |
| `tokyonight`           | Based on the [Tokyonight](<https://github.com/folke/tokyonight.nvim>) theme    |
| `everforest`           | Based on the [Everforest](<https://github.com/sainnhe/everforest>) theme       |
| `ayu`                  | Based on the [Ayu](<https://github.com/ayu-theme>) dark theme                  |
| `catppuccin`           | Based on the [Catppuccin](<https://github.com/catppuccin>) theme               |
| `catppuccin-macchiato` | Based on the [Catppuccin](<https://github.com/catppuccin>) theme               |
| `gruvbox`              | Based on the [Gruvbox](<https://github.com/morhetz/gruvbox>) theme             |
| `kanagawa`             | Based on the [Kanagawa](<https://github.com/rebelot/kanagawa.nvim>) theme      |
| `nord`                 | Based on the [Nord](<https://github.com/nordtheme/nord>) theme                 |
| `matrix`               | Hacker-style green on black theme                                            |
| `one-dark`             | Based on the [Atom One](<https://github.com/Th3Whit3Wolf/one-nvim>) Dark theme |

And more, we are constantly adding new themes.

---

## System theme

The `system` theme is designed to automatically adapt to your terminal's color scheme. Unlike traditional themes that use fixed colors, the _system_ theme:

- **Generates gray scale**: Creates a custom gray scale based on your terminal's background color, ensuring optimal contrast.
- **Uses ANSI colors**: Leverages standard ANSI colors (0-15) for syntax highlighting and UI elements, which respect your terminal's color palette.
- **Preserves terminal defaults**: Uses `none` for text and background colors to maintain your terminal's native appearance.

The system theme is for users who:

- Want OpenCode to match their terminal's appearance
- Use custom terminal color schemes
- Prefer a consistent look across all terminal applications

---

## Using a theme

You can select a theme by bringing up the theme select with the `/theme` command. Or you can specify it in `tui.json`.

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "theme": "tokyonight"
}
```

---

## Custom themes

OpenCode supports a flexible JSON-based theme system that allows users to create and customize themes easily.

---

### Hierarchy

Themes are loaded from multiple directories in the following order where later directories override earlier ones:

1. **Built-in themes** - These are embedded in the binary
2. **User config directory** - Defined in `~/.config/opencode/themes/*.json` or `$XDG_CONFIG_HOME/opencode/themes/*.json`
3. **Project root directory** - Defined in the `<project-root>/.opencode/themes/*.json`
4. **Current working directory** - Defined in `./.opencode/themes/*.json`

If multiple directories contain a theme with the same name, the theme from the directory with higher priority will be used.

---

### Creating a theme

To create a custom theme, create a JSON file in one of the theme directories.

For user-wide themes:

```bash
mkdir -p ~/.config/opencode/themes
vim ~/.config/opencode/themes/my-theme.json
```

And for project-specific themes.

```bash
mkdir -p .opencode/themes
vim .opencode/themes/my-theme.json
```

---

### JSON format

Themes use a flexible JSON format with support for:

- **Hex colors**: `"#ffffff"`
- **ANSI colors**: `3` (0-255)
- **Color references**: `"primary"` or custom definitions
- **Dark/light variants**: `{"dark": "#000", "light": "#fff"}`
- **No color**: `"none"` - Uses the terminal's default color or transparent

---

### Color definitions

The `defs` section is optional and it allows you to define reusable colors that can be referenced in the theme.

---

### Terminal defaults

The special value `"none"` can be used for any color to inherit the terminal's default color. This is particularly useful for creating themes that blend seamlessly with your terminal's color scheme:

- `"text": "none"` - Uses terminal's default foreground color
- `"background": "none"` - Uses terminal's default background color

---

### Example

Here's an example of a custom theme:

**代码示例：** `my-theme.json`

```json
{
  "$schema": "https://opencode.ai/theme.json",
  "defs": {
    "nord0": "#2E3440",
    "nord1": "#3B4252",
    "nord2": "#434C5E",
    "nord3": "#4C566A",
    "nord4": "#D8DEE9",
    "nord5": "#E5E9F0",
    "nord6": "#ECEFF4",
    "nord7": "#8FBCBB",
    "nord8": "#88C0D0",
    "nord9": "#81A1C1",
    "nord10": "#5E81AC",
    "nord11": "#BF616A",
    "nord12": "#D08770",
    "nord13": "#EBCB8B",
    "nord14": "#A3BE8C",
    "nord15": "#B48EAD"
  },
  "theme": {
    "primary": {
      "dark": "nord8",
      "light": "nord10"
    },
    "secondary": {
      "dark": "nord9",
      "light": "nord9"
    },
    "accent": {
      "dark": "nord7",
      "light": "nord7"
    },
    "error": {
      "dark": "nord11",
      "light": "nord11"
    },
    "warning": {
      "dark": "nord12",
      "light": "nord12"
    },
    "success": {
      "dark": "nord14",
      "light": "nord14"
    },
    "info": {
      "dark": "nord8",
      "light": "nord10"
    },
    "text": {
      "dark": "nord4",
      "light": "nord0"
    },
    "textMuted": {
      "dark": "nord3",
      "light": "nord1"
    },
    "background": {
      "dark": "nord0",
      "light": "nord6"
    },
    "backgroundPanel": {
      "dark": "nord1",
      "light": "nord5"
    },
    "backgroundElement": {
      "dark": "nord1",
      "light": "nord4"
    },
    "border": {
      "dark": "nord2",
      "light": "nord3"
    },
    "borderActive": {
      "dark": "nord3",
      "light": "nord2"
    },
    "borderSubtle": {
      "dark": "nord2",
      "light": "nord3"
    },
    "diffAdded": {
      "dark": "nord14",
      "light": "nord14"
    },
    "diffRemoved": {
      "dark": "nord11",
      "light": "nord11"
    },
    "diffContext": {
      "dark": "nord3",
      "light": "nord3"
    },
    "diffHunkHeader": {
      "dark": "nord3",
      "light": "nord3"
    },
    "diffHighlightAdded": {
      "dark": "nord14",
      "light": "nord14"
    },
    "diffHighlightRemoved": {
      "dark": "nord11",
      "light": "nord11"
    },
    "diffAddedBg": {
      "dark": "#3B4252",
      "light": "#E5E9F0"
    },
    "diffRemovedBg": {
      "dark": "#3B4252",
      "light": "#E5E9F0"
    },
    "diffContextBg": {
      "dark": "nord1",
      "light": "nord5"
    },
    "diffLineNumber": {
      "dark": "nord2",
      "light": "nord4"
    },
    "diffAddedLineNumberBg": {
      "dark": "#3B4252",
      "light": "#E5E9F0"
    },
    "diffRemovedLineNumberBg": {
      "dark": "#3B4252",
      "light": "#E5E9F0"
    },
    "markdownText": {
      "dark": "nord4",
      "light": "nord0"
    },
    "markdownHeading": {
      "dark": "nord8",
      "light": "nord10"
    },
    "markdownLink": {
      "dark": "nord9",
      "light": "nord9"
    },
    "markdownLinkText": {
      "dark": "nord7",
      "light": "nord7"
    },
    "markdownCode": {
      "dark": "nord14",
      "light": "nord14"
    },
    "markdownBlockQuote": {
      "dark": "nord3",
      "light": "nord3"
    },
    "markdownEmph": {
      "dark": "nord12",
      "light": "nord12"
    },
    "markdownStrong": {
      "dark": "nord13",
      "light": "nord13"
    },
    "markdownHorizontalRule": {
      "dark": "nord3",
      "light": "nord3"
    },
    "markdownListItem": {
      "dark": "nord8",
      "light": "nord10"
    },
    "markdownListEnumeration": {
      "dark": "nord7",
      "light": "nord7"
    },
    "markdownImage": {
      "dark": "nord9",
      "light": "nord9"
    },
    "markdownImageText": {
      "dark": "nord7",
      "light": "nord7"
    },
    "markdownCodeBlock": {
      "dark": "nord4",
      "light": "nord0"
    },
    "syntaxComment": {
      "dark": "nord3",
      "light": "nord3"
    },
    "syntaxKeyword": {
      "dark": "nord9",
      "light": "nord9"
    },
    "syntaxFunction": {
      "dark": "nord8",
      "light": "nord8"
    },
    "syntaxVariable": {
      "dark": "nord7",
      "light": "nord7"
    },
    "syntaxString": {
      "dark": "nord14",
      "light": "nord14"
    },
    "syntaxNumber": {
      "dark": "nord15",
      "light": "nord15"
    },
    "syntaxType": {
      "dark": "nord7",
      "light": "nord7"
    },
    "syntaxOperator": {
      "dark": "nord9",
      "light": "nord9"
    },
    "syntaxPunctuation": {
      "dark": "nord4",
      "light": "nord0"
    }
  }
}
```


</details>
