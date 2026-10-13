# Mistral Vibe Rust 终端界面规范

规定快捷键、导航、滚动、搜索、选择、颜色和验证要求；属于该仓库界面开发的技能指令。

- 来源：Mistral／Vibe
- 资料类型：存档文档
- 存档版本：`4ae5c5955da8`。
- 文档时间：源码提交（存档版）：[2026-10-09](<https://github.com/mistralai/mistral-vibe/commit/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/mistralai/mistral-vibe/blob/main/.agents/skills/vibe-rust-tui/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Mistral/Vibe/Mistral Vibe Rust 终端界面规范.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: vibe-rust-tui
description: Ratatui UI conventions for the Mistral Vibe Rust CLI (vibe/cli-rust). Use when adding or changing any screen, list, picker, footer hint, keyboard shortcut, list navigation or scrolling, back/close behavior, search field, selection highlight, color, or onboarding/dialog copy in the Rust TUI.
metadata:
  display-name: Vibe Rust TUI
  short-description: Hint, keyboard, navigation, search, list-cursor and color conventions for the Rust TUI
  default-prompt: Use $vibe-rust-tui to follow the Rust TUI hint, keyboard, navigation, search, list-cursor and color conventions.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 氛围 Rust TUI

UI 约定为 `vibe/cli-rust/`。每个屏幕都遵循它们，因此 TUI 读起来就像一个产品。流程规则（从 Python、场景、黄金移植）位于 `migrate-cli-to-rust` 中；本地代码规则位于 `vibe/cli-rust/AGENTS.md` 中。

## 快捷方式提示

- 仅通过 `src/hints.rs` 命名键和操作：`key::*`、`action::*` 和共享对（`hints::NAVIGATE`、`SELECT`、`SEARCH`、`CANCEL`、`CLOSE`、 `BACK`）。切勿在调用站点将键或操作写为字符串文字。单字母键（`"d"`、`"r"`）就可以，只要其操作来自`action::*`。当缺少键或动词时，将其添加到 `src/hints.rs`。
- 提示是未填充的 `(key, action)` 对 (`hints::Hint`)。渲染器拥有所有间距；切勿将空格烘焙到标签中。
- 措辞：动作是小写动词（`navigate`、`select`、`close`）；键保留其规范大小写（`Enter`、`Esc`、`Ctrl+C`、`Backspace`）。组合键使用`/`（`Space/Enter`、`Esc/Ctrl+C`）。
- 每当 j/k 工作时，垂直导航为 `key::NAV` (`↑↓/jk`)；仅当焦点文本字段拥有字母时才使用 `key::ARROWS` (`↑↓`)。水平导航为`key::LEFT_RIGHT` (`←→`)。
- 页脚为 `key action  key action`，以 Esc 提示结束。内联状态行（加载行、旁白）是一个例外，请阅读 `key to action`。
- 通过 `ui/hint_line.rs`（`draw`、`draw_clipped`、`line`、`spans`、`styled`）渲染每个页脚：它们将共享的 key/action 样式和剪辑应用到框。切勿将提示文本与硬编码的 x 偏移量或自定义键样式一起放置。用 `hints::width` 将提示居中。
- 入门和对话框遵循相同的页脚形式（`Enter continue`、`Esc back`），而不是诸如“按 Esc 返回”之类的句子。

## 键盘导航

- 当没有文本字段聚焦时，每个可导航列表都接受 `j`/`k` 和 `↑`/`↓`。仅匹配普通键（无 Ctrl/Alt/Super），因此 `Ctrl+J`/`Ctrl+K` 保留其编辑含义。
- 显示屏幕实际处理的导航键：仅在连接 j/k 的位置写入 `↑↓/jk`。
- 边缘：`↑↓`/`jk`（以及对话框选项中的`←→`）在两端包裹； PageUp/PageDown/Home/End 夹具。当某些行（节标题、注释、空白）无法取cursor时，移动到`list_nav::wrap`或`list_nav::wrap_selectable`；切勿手掷 `saturating_sub`/`min(len - 1)` 进行箭头移动。唯一不换行的列表是附加到作曲家的列表：向下超过最新排队的提示将离开队列模式，子代理列表顶部的向上将焦点返回给作曲家。
- 返回：Esc 返回一级（字段 → 列表、详细信息 → 列表、验证流程 → 其 `/mcp` 列表）并仅在顶层关闭。退格键用于编辑文本，但绝不是导航键。嵌套视图的页脚以 `hints::BACK` (`Esc back`) 结尾；顶级视图的页脚以 `hints::CLOSE` 或 `hints::CANCEL` 结尾。
- 数字：当且仅当行显示其数字 (`1. Trust folder`) 时，行的数字快捷方式才存在。按它与移动到该行并按 Enter 键相同。用`list_nav::digit`解析它；从不接受未编号行上的数字，并且从不对不接受数字的行进行编号。

## 滚动

- 滚动列表通过`ui/list_scroll.rs``follow`保持突出显示的选项，传递哪些行是选项；永远不要写自己的`scroll = selected` 和解。它将视口移动得最少，并带动选项的不可选择的邻居：其上方的节标题、第一个选项的列表前导行、最后一个选项的尾随行。因此，从底部绕回顶部始终显示列表的最顶部。
- 鼠标滚轮滚动设置列表的 `free_scroll` 并跳过 `follow` 直到下一个键盘移动；它仅限制偏移量。

## 可搜索列表

- 对任何可过滤列表使用 `search_field::Search`、`search_field::handle_key` 和 `search_field::paste`，并使用 `search_field::hints` 构建其页脚。
- 模型：列表打开，列表聚焦； `/`聚焦领域；实时输入过滤器（字母，包括 j/k，转到该字段）； `↑↓`、PageUp/PageDown和Enter在字段聚焦时继续在列表上动作； Esc 首先离开字段（过滤器保留），然后清除过滤器，然后关闭屏幕。
- 不要将其他入口点添加到搜索中（Tab、Left、打开时键入搜索）。不要让 Enter 仅“应用”过滤器：它作用于突出显示的项目。
- 列表页脚包括`hints::SEARCH`。
- 搜索行位于标题正下方，后跟一个空白行，内容为 `/  placeholder`（`Search plugins`、`Search settings`）。它始终在列表视图中可见并在详细视图中隐藏。仅使用`ui/search_field.rs` `draw_row`绘制它，它拥有`/`和字段的缩进；切勿将搜索字段放在列表底部或页脚中。项目选择器是一个例外：其标记为 `Search projects:` 的字段共享其创建视图的形式 `Field`。

## 选择

- 聚焦的行是列表 - cursor 栏：`ui/list_cursor.rs` `style()` 横跨整个行宽度（块 - cursor 颜色，粗体）。没有用于聚焦的字形光标（`›`、`▸`、`>`）。
- 仅通过 `list_cursor` 绘制条形：`paint(f, area)` 填充行区域，`styles(highlighted)` / `styles_on(highlighted, bg)` 给出行的 `(text, dim)` 样式，`marker_style` 为其上的当前值标记设置样式。切勿在 `ui/theme/` 和 `ui/list_cursor.rs` 之外读取 `theme::block_cursor_bg`/`block_cursor_fg`（单元测试强制执行）。文本字段脱字符不是列表光标：使用 `theme::block_caret`（类似 TextArea 的字段）或 `theme::fixed::input_caret`（类似 `Input` 的紧凑字段）。
- 水平选项（对话框按钮）将每个选项渲染为一个` label `芯片；所选芯片为`list_cursor::style()`。切换选择时将标签保持在同一列。
- 当前值（活动模型、主题、关卡、图层或会话）在 `list_cursor::current_color()` 中用 `list_cursor::marker(true)` (`› `) 标记，位于两列槽中，在其他行上保持空白 (`list_cursor::BLANK`)。
- `(String, Style)` 行渲染器为 `list_cursor::is_bar` 所在的行绘制全宽条，并保留跨度自己的背景而不是覆盖它。

## 颜色

- 每个颜色值都存在于`src/ui/theme/`中：主题调色板中与主题相关的颜色（`theme::foreground()`，`theme::muted_style()`，...），`ui/theme/fixed.rs`中的固定品牌和终端颜色（`ORANGE`，`LOADING_GRADIENT`，载入颜色， `sgr::*`转义码，Monokai/Alabaster通过`fixed::named`命名的调色板），语法颜色在`ui/theme/syntax.rs`中。固定颜色（例如品牌橙色）很好，但它一旦定义就可以在其他地方导入。
- 在 `src/ui/theme/` 之外，切勿写入 `Color::Rgb(..)`、`Color::Indexed(..)`、命名的 `Color::*` 变体或原始 SGR 颜色代码。当屏幕需要新颜色时，首先添加命名常量或主题访问器。 `tests/units/color_literals.rs` 在主题模块之外的任何文字上失败（仅 `update_prompt/ansi.rs`，它将任何 `Color` 转换为 SGR，并且测试代码除外）。

## 验证

- 对提示、按键、列表行为（换行、后退、数字、滚动、搜索）或选择样式的任何更改都是用户可见的：更新受影响的 `client-e2e` 场景（`screen_contains`、按键序列、鼠标坐标）并按照 `migrate-cli-to-rust` 重新生成其黄金。新行为有其自己的场景（例如 `mcp/mcp_wrap_scroll`）。
- 共享助手携带自己的单元测试（`tests/units/list_nav.rs`、`list_scroll.rs`、`search_field.rs`）；当助手发生变化时扩展它们，而不是逐屏测试规则。
- `tests/units/color_literals.rs`守卫颜色和列表-cursor规则。不要添加豁免以使其通过：将颜色移至 `src/ui/theme/` 或将条形图穿过 `list_cursor`。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Vibe Rust TUI

UI conventions for `vibe/cli-rust/`. Every screen follows them so the TUI reads as one product. Process rules (porting from Python, scenarios, goldens) live in `migrate-cli-to-rust`; local code rules live in `vibe/cli-rust/AGENTS.md`.

## Shortcut hints

- Name keys and actions only through `src/hints.rs`: `key::*`, `action::*` and the shared pairs (`hints::NAVIGATE`, `SELECT`, `SEARCH`, `CANCEL`, `CLOSE`, `BACK`). Never write a key or action as a string literal at a call site. A single letter key (`"d"`, `"r"`) is fine as long as its action comes from `action::*`. When a key or verb is missing, add it to `src/hints.rs`.
- A hint is an unpadded `(key, action)` pair (`hints::Hint`). The renderer owns all spacing; never bake spaces into a label.
- Wording: actions are lowercase verbs (`navigate`, `select`, `close`); keys keep their canonical casing (`Enter`, `Esc`, `Ctrl+C`, `Backspace`). Combined keys use `/` (`Space/Enter`, `Esc/Ctrl+C`).
- Vertical navigation is `key::NAV` (`↑↓/jk`) whenever j/k work; use `key::ARROWS` (`↑↓`) only where a focused text field owns the letters. Horizontal navigation is `key::LEFT_RIGHT` (`←→`).
- Footers read `key action  key action`, ending with the Esc hint. Inline status lines (loading line, narrator) are the one exception and read `key to action`.
- Render every footer through `ui/hint_line.rs` (`draw`, `draw_clipped`, `line`, `spans`, `styled`): they apply the shared key/action styles and clip to the box. Never place hint text with hardcoded x offsets or custom key styles. Center a hint with `hints::width`.
- Onboarding and dialogs follow the same footer form (`Enter continue`, `Esc back`), not sentences like "Press Esc to go back".

## Keyboard navigation

- Every navigable list accepts `j`/`k` alongside `↑`/`↓` when no text field is focused. Match only plain keys (no Ctrl/Alt/Super) so `Ctrl+J`/`Ctrl+K` keep their editing meaning.
- Show the navigation keys a screen actually handles: write `↑↓/jk` only where j/k are wired.
- Edges: `↑↓`/`jk` (and `←→` across dialog options) wrap at both ends; PageUp/PageDown/Home/End clamp. Move through `list_nav::wrap`, or `list_nav::wrap_selectable` when some rows (section headers, notes, blanks) cannot take the cursor; never hand-roll `saturating_sub`/`min(len - 1)` for arrow moves. The only lists that do not wrap are the ones attached to the composer: Down past the newest queued prompt leaves queue mode, and Up at the top of the subagent list returns focus to the composer.
- Back: Esc goes back exactly one level (field → list, detail → list, auth flow → its `/mcp` list) and closes only at the top level. Backspace edits text and is never a navigation key. A nested view's footer ends with `hints::BACK` (`Esc back`); a top-level view's footer ends with `hints::CLOSE` or `hints::CANCEL`.
- Digits: a row's digit shortcut exists if and only if the row displays its number (`1. Trust folder`). Pressing it is the same as moving to the row and pressing Enter. Parse it with `list_nav::digit`; never accept digits on unnumbered rows, and never number rows that do not accept their digit.

## Scrolling

- A scrolling list keeps the highlighted option in view through `ui/list_scroll.rs` `follow`, passing which lines are options; never write its own `scroll = selected` reconciliation. It moves the viewport the least and brings the option's non-selectable neighbours along: its section header above it, the list's leading lines for the first option, the trailing lines for the last one. Wrapping from the bottom back to the top therefore always shows the very top of the list.
- Mouse-wheel scrolling sets the list's `free_scroll` and skips `follow` until the next keyboard move; it only clamps the offset.

## Searchable lists

- Use `search_field::Search`, `search_field::handle_key` and `search_field::paste` for any filterable list, and build its footer with `search_field::hints`.
- The model: lists open with the list focused; `/` focuses the field; typing filters live (letters, including j/k, go to the field); `↑↓`, PageUp/PageDown and Enter keep acting on the list while the field is focused; Esc first leaves the field (the filter stays), then clears the filter, then closes the screen.
- Do not add other entry points into search (Tab, Left, type-to-search on open). Do not make Enter only "apply" the filter: it acts on the highlighted item.
- The list footer includes `hints::SEARCH`.
- The search row sits directly under the title, followed by one blank row, and reads `/  placeholder` (`Search plugins`, `Search settings`). It is always visible in the list view and hidden in detail views. Draw it only with `ui/search_field.rs` `draw_row`, which owns the `/` and the field's indent; never put a search field at the bottom of a list or in the footer. The project picker is the one exception: its labelled `Search projects:` field shares the form `Field` of its create view.

## Selection

- The focused row is the list-cursor bar: `ui/list_cursor.rs` `style()` across the full row width (block-cursor colors, bold). No glyph cursors (`›`, `▸`, `>`) for focus.
- Draw the bar only through `list_cursor`: `paint(f, area)` fills a row area, `styles(highlighted)` / `styles_on(highlighted, bg)` give the `(text, dim)` styles of a row, `marker_style` styles the current-value marker on it. Never read `theme::block_cursor_bg`/`block_cursor_fg` outside `ui/theme/` and `ui/list_cursor.rs` (a unit test enforces it). Text-field carets are not list cursors: use `theme::block_caret` (TextArea-like fields) or `theme::fixed::input_caret` (compact `Input`-like fields).
- Horizontal options (dialog buttons) render each option as a ` label ` chip; the selected chip takes `list_cursor::style()`. Keep labels at the same columns when switching selection.
- The current value (the active model, theme, level, layer or session) is marked with `list_cursor::marker(true)` (`› `) in `list_cursor::current_color()`, in a two-column slot that stays blank (`list_cursor::BLANK`) on other rows.
- `(String, Style)` row renderers paint the full-width bar for rows where `list_cursor::is_bar` holds, and keep a span's own background instead of overwriting it.

## Colors

- Every color value lives in `src/ui/theme/`: theme-dependent colors in the theme palettes (`theme::foreground()`, `theme::muted_style()`, ...), fixed brand and terminal colors in `ui/theme/fixed.rs` (`ORANGE`, `LOADING_GRADIENT`, onboarding colors, `sgr::*` escape codes, the Monokai/Alabaster named palette via `fixed::named`), syntax colors in `ui/theme/syntax.rs`. A fixed color such as the brand orange is fine, but it is defined once there and imported everywhere else.
- Outside `src/ui/theme/`, never write `Color::Rgb(..)`, `Color::Indexed(..)`, a named `Color::*` variant or a raw SGR color code. When a screen needs a new color, add a named constant or theme accessor first. `tests/units/color_literals.rs` fails on any literal outside the theme module (only `update_prompt/ansi.rs`, which converts any `Color` to SGR, and test code are exempt).

## Verification

- Any change to a hint, a key, a list behavior (wrap, back, digits, scroll, search) or a selection style is user-visible: update the affected `client-e2e` scenarios (`screen_contains`, key sequences, mouse coordinates) and regenerate their goldens following `migrate-cli-to-rust`. A new behavior gets its own scenario (for example `mcp/mcp_wrap_scroll`).
- Shared helpers carry their own unit tests (`tests/units/list_nav.rs`, `list_scroll.rs`, `search_field.rs`); extend them when a helper changes instead of testing the rule screen by screen.
- `tests/units/color_literals.rs` guards the color and list-cursor rules. Do not add exemptions to make it pass: move the color into `src/ui/theme/` or route the bar through `list_cursor`.


</details>
