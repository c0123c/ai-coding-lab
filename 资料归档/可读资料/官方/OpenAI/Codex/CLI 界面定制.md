# CLI 界面定制

介绍终端主题、语法高亮、Shell 补全和提示编辑器设置，帮助调整日常命令行使用体验。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/cli-customization>)）；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/cli-customization.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/CLI 界面定制.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# CLI定制

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

Codex CLI 提供了特定于终端的选项，用于说明交互式会话 
 的外观以及输入命令和提示的方式。

## 语法突出显示和主题

终端 UI (TUI) 语法高亮显示受隔离的 Markdown 代码块和文件 
 差异。运行`/theme`打开主题选择器，预览主题，并将
选择保存到`$CODEX_HOME/config.toml`中的`tui.theme`。

要添加自定义主题，请将 `.tmTheme` 文件放置在 `$CODEX_HOME/themes` 中，然后 
 从主题选择器中选择它。

## 壳牌完井

为 Bash、Z shell、Fish 或 PowerShell 生成完成脚本：

```bash
codex completion zsh
```

从 shell 配置加载脚本。对于 Z shell，添加：

```bash
eval "$(codex completion zsh)"
```

如果 Z shell 报告 `command not found: compdef`，请在加载 Codex 补全之前初始化其补全系统 
：

```bash
autoload -Uz compinit && compinit
eval "$(codex completion zsh)"
```

重新启动 shell，键入 `codex`，然后按 <kbd>Tab</kbd> 以验证完成。

## 提示编辑器

对于较长的提示，请在编辑器中按<kbd>Ctrl</kbd>+<kbd>G</kbd>打开
由`VISUAL`配置的编辑器，或在`VISUAL`时按`EDITOR`未设置。保存
并关闭编辑器以在发送之前将文本返回给作曲家。

有关交互式键盘控件以及完整的命令和选项列表，请参阅
[命令](<https://learn.chatgpt.com/docs/developer-commands?surface=cli#cli-interactive-shortcuts>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# CLI customization

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

The Codex CLI provides terminal-specific options for how interactive sessions
look and how you enter commands and prompts.

## Syntax highlighting and themes

The terminal UI (TUI) syntax-highlights fenced Markdown code blocks and file
diffs. Run `/theme` to open the theme picker, preview themes, and save your
selection to `tui.theme` in `$CODEX_HOME/config.toml`.

To add a custom theme, place a `.tmTheme` file in `$CODEX_HOME/themes`, then
select it from the theme picker.

## Shell completions

Generate a completion script for Bash, the Z shell, Fish, or PowerShell:

```bash
codex completion zsh
```

Load the script from your shell configuration. For the Z shell, add:

```bash
eval "$(codex completion zsh)"
```

If the Z shell reports `command not found: compdef`, initialize its completion system
before loading the Codex completions:

```bash
autoload -Uz compinit && compinit
eval "$(codex completion zsh)"
```

Restart the shell, type `codex`, and press <kbd>Tab</kbd> to verify completion.

## Prompt editor

For longer prompts, press <kbd>Ctrl</kbd>+<kbd>G</kbd> in the composer to open
the editor configured by `VISUAL`, or `EDITOR` when `VISUAL` isn't set. Save
and close the editor to return the text to the composer before sending it.

For interactive keyboard controls and the full command and option list, see
[Commands](<https://learn.chatgpt.com/docs/developer-commands?surface=cli#cli-interactive-shortcuts>).


</details>
