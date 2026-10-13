# OpenCode 快捷键与终端输入设置

操作参考。解释 Leader 键、按键配置、禁用绑定、桌面提示框、Shift+Enter 以及 Windows Terminal 的设置，对应日常使用与输入排查。

- 来源：OpenCode
- 资料类型：操作参考
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-06-23](<https://github.com/anomalyco/opencode/commit/3cdd431794f540a87864361f5f8656b38d050834>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：快捷键取决于操作系统、终端和所存版本。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/keybinds.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 快捷键与终端输入设置.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Keybinds
description: Customize your keybinds.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 有一个按键绑定列表，您可以通过 `tui.json` 自定义。

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "leader_timeout": 2000,
  "keybinds": {
    "leader": "ctrl+x",
    "app_exit": "ctrl+c,ctrl+d,<leader>q",
    "app_debug": "none",
    "app_console": "none",
    "app_heap_snapshot": "none",
    "app_toggle_animations": "none",
    "app_toggle_file_context": "none",
    "app_toggle_diffwrap": "none",
    "app_toggle_paste_summary": "none",
    "app_toggle_session_directory_filter": "none",
    "command_list": "ctrl+p",
    "help_show": "none",
    "docs_open": "none",

    "editor_open": "<leader>e",
    "theme_list": "<leader>t",
    "theme_switch_mode": "none",
    "theme_mode_lock": "none",
    "sidebar_toggle": "<leader>b",
    "scrollbar_toggle": "none",
    "status_view": "<leader>s",

    "session_export": "<leader>x",
    "session_copy": "none",
    "session_move": "none",
    "session_new": "<leader>n",
    "session_list": "<leader>l",
    "session_timeline": "<leader>g",
    "session_fork": "none",
    "session_rename": "ctrl+r",
    "session_delete": "ctrl+d",
    "session_share": "none",
    "session_unshare": "none",
    "session_interrupt": "escape",
    "session_compact": "<leader>c",
    "session_toggle_timestamps": "none",
    "session_toggle_generic_tool_output": "none",
    "session_child_first": "<leader>down",
    "session_child_cycle": "right",
    "session_child_cycle_reverse": "left",
    "session_parent": "up",

    "stash_delete": "ctrl+d",
    "model_provider_list": "ctrl+a",
    "model_favorite_toggle": "ctrl+f",
    "model_list": "<leader>m",
    "model_cycle_recent": "f2",
    "model_cycle_recent_reverse": "shift+f2",
    "model_cycle_favorite": "none",
    "model_cycle_favorite_reverse": "none",
    "mcp_list": "none",
    "provider_connect": "none",
    "console_org_switch": "none",
    "agent_list": "<leader>a",
    "agent_cycle": "tab",
    "agent_cycle_reverse": "shift+tab",
    "variant_cycle": "ctrl+t",
    "variant_list": "none",

    "messages_page_up": "pageup,ctrl+alt+b",
    "messages_page_down": "pagedown,ctrl+alt+f",
    "messages_line_up": "ctrl+alt+y",
    "messages_line_down": "ctrl+alt+e",
    "messages_half_page_up": "ctrl+alt+u",
    "messages_half_page_down": "ctrl+alt+d",
    "messages_first": "ctrl+g,home",
    "messages_last": "ctrl+alt+g,end",
    "messages_next": "none",
    "messages_previous": "none",
    "messages_last_user": "none",
    "messages_copy": "<leader>y",
    "messages_undo": "<leader>u",
    "messages_redo": "<leader>r",
    "messages_toggle_conceal": "<leader>h",
    "tool_details": "none",
    "display_thinking": "none",

    "prompt_submit": "none",
    "prompt_editor_context_clear": "none",
    "prompt_skills": "none",
    "prompt_stash": "none",
    "prompt_stash_pop": "none",
    "prompt_stash_list": "none",
    "workspace_set": "none",

    "input_clear": "ctrl+c",
    "input_paste": {
      "key": "ctrl+v",
      "preventDefault": false
    },
    "input_submit": "return",
    "input_newline": "shift+return,ctrl+return,alt+return,ctrl+j",
    "input_move_left": "left,ctrl+b",
    "input_move_right": "right,ctrl+f",
    "input_move_up": "up",
    "input_move_down": "down",
    "input_select_left": "shift+left",
    "input_select_right": "shift+right",
    "input_select_up": "shift+up",
    "input_select_down": "shift+down",
    "input_line_home": "ctrl+a",
    "input_line_end": "ctrl+e",
    "input_select_line_home": "ctrl+shift+a",
    "input_select_line_end": "ctrl+shift+e",
    "input_visual_line_home": "alt+a",
    "input_visual_line_end": "alt+e",
    "input_select_visual_line_home": "alt+shift+a",
    "input_select_visual_line_end": "alt+shift+e",
    "input_buffer_home": "home",
    "input_buffer_end": "end",
    "input_select_buffer_home": "shift+home",
    "input_select_buffer_end": "shift+end",
    "input_delete_line": "ctrl+shift+d",
    "input_delete_to_line_end": "ctrl+k",
    "input_delete_to_line_start": "ctrl+u",
    "input_backspace": "backspace,shift+backspace",
    "input_delete": "ctrl+d,delete,shift+delete",
    "input_undo": "ctrl+-,super+z",
    "input_redo": "ctrl+.,super+shift+z",
    "input_word_forward": "alt+f,alt+right,ctrl+right",
    "input_word_backward": "alt+b,alt+left,ctrl+left",
    "input_select_word_forward": "alt+shift+f,alt+shift+right",
    "input_select_word_backward": "alt+shift+b,alt+shift+left",
    "input_delete_word_forward": "alt+d,alt+delete,ctrl+delete",
    "input_delete_word_backward": "ctrl+w,ctrl+backspace,alt+backspace",
    "input_select_all": "super+a",
    "history_previous": "up",
    "history_next": "down",

    "dialog.select.prev": "up,ctrl+p",
    "dialog.select.next": "down,ctrl+n",
    "dialog.select.page_up": "pageup",
    "dialog.select.page_down": "pagedown",
    "dialog.select.home": "home",
    "dialog.select.end": "end",
    "dialog.select.submit": "return",
    "dialog.prompt.submit": "return",
    "dialog.mcp.toggle": "space",
    "prompt.autocomplete.prev": "up,ctrl+p",
    "prompt.autocomplete.next": "down,ctrl+n",
    "prompt.autocomplete.hide": "escape",
    "prompt.autocomplete.select": "return",
    "prompt.autocomplete.complete": "tab",
    "permission.prompt.fullscreen": "ctrl+f",
    "plugins.toggle": "space",
    "dialog.plugins.install": "shift+i",

    "terminal_suspend": "ctrl+z",
    "terminal_title_toggle": "none",
    "tips_toggle": "<leader>h",
    "plugin_manager": "none",
    "plugin_install": "none",

    "which_key_toggle": "ctrl+alt+k",
    "which_key_layout_toggle": "ctrl+alt+shift+k",
    "which_key_pending_toggle": "ctrl+alt+shift+p",
    "which_key_group_previous": "ctrl+alt+left,ctrl+alt+[",
    "which_key_group_next": "ctrl+alt+right,ctrl+alt+]",
    "which_key_scroll_up": "ctrl+alt+up,ctrl+alt+p",
    "which_key_scroll_down": "ctrl+alt+down,ctrl+alt+n",
    "which_key_page_up": "ctrl+alt+pageup",
    "which_key_page_down": "ctrl+alt+pagedown",
    "which_key_home": "ctrl+alt+home",
    "which_key_end": "ctrl+alt+end"
  }
}
```

**注释：**

在 Windows 上，`input_undo` 和 `terminal_suspend` 的默认值不同：

- 未显式配置时，`input_undo` 默认为 `ctrl+z,ctrl+-,super+z`。添加 `ctrl+z` 绑定是因为 Windows 终端不支持 POSIX 挂起。
- `terminal_suspend` 被迫 `none`，因为本机 Windows 终端不支持 POSIX 挂起。

---

## 领袖钥匙

OpenCode 对许多按键绑定使用 `leader` 键。这可以避免终端中的冲突。

默认情况下，`ctrl+x` 是主键，许多操作需要您先按主键，然后再按快捷键。例如，要开始新会话，请先按 `ctrl+x`，然后按 `n`。

您不需要为键绑定使用主键，但我们建议您这样做。

默认情况下，某些导航键绑定有意不使用前导键。对于子代理会话，默认值为 `session_child_first` = `<leader>down`、`session_child_cycle` = `right`、`session_child_cycle_reverse` = `left` 和 `session_parent` = `up`。

`leader_timeout` 控制 OpenCode 等待前导键后的下一个键的时间。默认为 `2000` 毫秒。

---

## 绑定值

字符串可以包含一个快捷方式或多个以逗号分隔的快捷方式。您还可以将数组用于多个快捷方式。

对于高级情况，请使用带有 `key`、`event`、`preventDefault` 或 `fallthrough` 的对象。

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "keybinds": {
    "messages_copy": ["<leader>y", "ctrl+shift+c"],
    "input_paste": {
      "key": "ctrl+v",
      "preventDefault": false
    }
  }
}
```

---

## 禁用按键绑定

您可以通过将键添加到 `tui.json` 并值为 `"none"` 或 `false` 来禁用键绑定。

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "keybinds": {
    "session_compact": "none"
  }
}
```

---

## 桌面提示快捷方式

OpenCode桌面应用程序提示输入支持常见的Readline/Emacs-style编辑文本的快捷方式。这些是内置的，目前无法通过 `opencode.json` 配置。

|快捷方式|行动|
| -------- | ---------------------------------------- |
| `ctrl+a` |移动到当前行的开头|
| `ctrl+e` |移至当前行末尾|
| `ctrl+b` |cursor 后移一个字符|
| `ctrl+f` |cursor 向前移动一个字符|
| `alt+b`  |cursor 后移一个字|
| `alt+f`  |cursor 向前移动一个字|
| `ctrl+d` |删除cursor下的字符|
| `ctrl+k` |杀死到行尾|
| `ctrl+u` |杀死到行首|
| `ctrl+w` |删除前一个单词|
| `alt+d`  |杀掉下一个字|
| `ctrl+t` |转置字符|
| `ctrl+g` |取消弹出窗口/中止运行响应|

---

## Shift+Enter

默认情况下，某些终端不发送带有 Enter 的修饰键。您可能需要配置终端以发送 `Shift+Enter` 作为转义序列。

### Windows 终端

打开您的 `settings.json`：

```
%LOCALAPPDATA%\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\LocalState\settings.json
```

将其添加到根级 `actions` 数组：

```json
"actions": [
  {
    "command": {
      "action": "sendInput",
      "input": "\u001b[13;2u"
    },
    "id": "User.sendInput.ShiftEnterCustom"
  }
]
```

将其添加到根级 `keybindings` 数组：

```json
"keybindings": [
  {
    "keys": "shift+enter",
    "id": "User.sendInput.ShiftEnterCustom"
  }
]
```

保存文件并重新启动 Windows 终端或打开新选项卡。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode has a list of keybinds that you can customize through `tui.json`.

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "leader_timeout": 2000,
  "keybinds": {
    "leader": "ctrl+x",
    "app_exit": "ctrl+c,ctrl+d,<leader>q",
    "app_debug": "none",
    "app_console": "none",
    "app_heap_snapshot": "none",
    "app_toggle_animations": "none",
    "app_toggle_file_context": "none",
    "app_toggle_diffwrap": "none",
    "app_toggle_paste_summary": "none",
    "app_toggle_session_directory_filter": "none",
    "command_list": "ctrl+p",
    "help_show": "none",
    "docs_open": "none",

    "editor_open": "<leader>e",
    "theme_list": "<leader>t",
    "theme_switch_mode": "none",
    "theme_mode_lock": "none",
    "sidebar_toggle": "<leader>b",
    "scrollbar_toggle": "none",
    "status_view": "<leader>s",

    "session_export": "<leader>x",
    "session_copy": "none",
    "session_move": "none",
    "session_new": "<leader>n",
    "session_list": "<leader>l",
    "session_timeline": "<leader>g",
    "session_fork": "none",
    "session_rename": "ctrl+r",
    "session_delete": "ctrl+d",
    "session_share": "none",
    "session_unshare": "none",
    "session_interrupt": "escape",
    "session_compact": "<leader>c",
    "session_toggle_timestamps": "none",
    "session_toggle_generic_tool_output": "none",
    "session_child_first": "<leader>down",
    "session_child_cycle": "right",
    "session_child_cycle_reverse": "left",
    "session_parent": "up",

    "stash_delete": "ctrl+d",
    "model_provider_list": "ctrl+a",
    "model_favorite_toggle": "ctrl+f",
    "model_list": "<leader>m",
    "model_cycle_recent": "f2",
    "model_cycle_recent_reverse": "shift+f2",
    "model_cycle_favorite": "none",
    "model_cycle_favorite_reverse": "none",
    "mcp_list": "none",
    "provider_connect": "none",
    "console_org_switch": "none",
    "agent_list": "<leader>a",
    "agent_cycle": "tab",
    "agent_cycle_reverse": "shift+tab",
    "variant_cycle": "ctrl+t",
    "variant_list": "none",

    "messages_page_up": "pageup,ctrl+alt+b",
    "messages_page_down": "pagedown,ctrl+alt+f",
    "messages_line_up": "ctrl+alt+y",
    "messages_line_down": "ctrl+alt+e",
    "messages_half_page_up": "ctrl+alt+u",
    "messages_half_page_down": "ctrl+alt+d",
    "messages_first": "ctrl+g,home",
    "messages_last": "ctrl+alt+g,end",
    "messages_next": "none",
    "messages_previous": "none",
    "messages_last_user": "none",
    "messages_copy": "<leader>y",
    "messages_undo": "<leader>u",
    "messages_redo": "<leader>r",
    "messages_toggle_conceal": "<leader>h",
    "tool_details": "none",
    "display_thinking": "none",

    "prompt_submit": "none",
    "prompt_editor_context_clear": "none",
    "prompt_skills": "none",
    "prompt_stash": "none",
    "prompt_stash_pop": "none",
    "prompt_stash_list": "none",
    "workspace_set": "none",

    "input_clear": "ctrl+c",
    "input_paste": {
      "key": "ctrl+v",
      "preventDefault": false
    },
    "input_submit": "return",
    "input_newline": "shift+return,ctrl+return,alt+return,ctrl+j",
    "input_move_left": "left,ctrl+b",
    "input_move_right": "right,ctrl+f",
    "input_move_up": "up",
    "input_move_down": "down",
    "input_select_left": "shift+left",
    "input_select_right": "shift+right",
    "input_select_up": "shift+up",
    "input_select_down": "shift+down",
    "input_line_home": "ctrl+a",
    "input_line_end": "ctrl+e",
    "input_select_line_home": "ctrl+shift+a",
    "input_select_line_end": "ctrl+shift+e",
    "input_visual_line_home": "alt+a",
    "input_visual_line_end": "alt+e",
    "input_select_visual_line_home": "alt+shift+a",
    "input_select_visual_line_end": "alt+shift+e",
    "input_buffer_home": "home",
    "input_buffer_end": "end",
    "input_select_buffer_home": "shift+home",
    "input_select_buffer_end": "shift+end",
    "input_delete_line": "ctrl+shift+d",
    "input_delete_to_line_end": "ctrl+k",
    "input_delete_to_line_start": "ctrl+u",
    "input_backspace": "backspace,shift+backspace",
    "input_delete": "ctrl+d,delete,shift+delete",
    "input_undo": "ctrl+-,super+z",
    "input_redo": "ctrl+.,super+shift+z",
    "input_word_forward": "alt+f,alt+right,ctrl+right",
    "input_word_backward": "alt+b,alt+left,ctrl+left",
    "input_select_word_forward": "alt+shift+f,alt+shift+right",
    "input_select_word_backward": "alt+shift+b,alt+shift+left",
    "input_delete_word_forward": "alt+d,alt+delete,ctrl+delete",
    "input_delete_word_backward": "ctrl+w,ctrl+backspace,alt+backspace",
    "input_select_all": "super+a",
    "history_previous": "up",
    "history_next": "down",

    "dialog.select.prev": "up,ctrl+p",
    "dialog.select.next": "down,ctrl+n",
    "dialog.select.page_up": "pageup",
    "dialog.select.page_down": "pagedown",
    "dialog.select.home": "home",
    "dialog.select.end": "end",
    "dialog.select.submit": "return",
    "dialog.prompt.submit": "return",
    "dialog.mcp.toggle": "space",
    "prompt.autocomplete.prev": "up,ctrl+p",
    "prompt.autocomplete.next": "down,ctrl+n",
    "prompt.autocomplete.hide": "escape",
    "prompt.autocomplete.select": "return",
    "prompt.autocomplete.complete": "tab",
    "permission.prompt.fullscreen": "ctrl+f",
    "plugins.toggle": "space",
    "dialog.plugins.install": "shift+i",

    "terminal_suspend": "ctrl+z",
    "terminal_title_toggle": "none",
    "tips_toggle": "<leader>h",
    "plugin_manager": "none",
    "plugin_install": "none",

    "which_key_toggle": "ctrl+alt+k",
    "which_key_layout_toggle": "ctrl+alt+shift+k",
    "which_key_pending_toggle": "ctrl+alt+shift+p",
    "which_key_group_previous": "ctrl+alt+left,ctrl+alt+[",
    "which_key_group_next": "ctrl+alt+right,ctrl+alt+]",
    "which_key_scroll_up": "ctrl+alt+up,ctrl+alt+p",
    "which_key_scroll_down": "ctrl+alt+down,ctrl+alt+n",
    "which_key_page_up": "ctrl+alt+pageup",
    "which_key_page_down": "ctrl+alt+pagedown",
    "which_key_home": "ctrl+alt+home",
    "which_key_end": "ctrl+alt+end"
  }
}
```

**注释：**

On Windows, the defaults for `input_undo` and `terminal_suspend` are different:

- `input_undo` defaults to `ctrl+z,ctrl+-,super+z` when it is not explicitly configured. The `ctrl+z` binding is added because Windows terminals do not support POSIX suspend.
- `terminal_suspend` is forced to `none` because native Windows terminals do not support POSIX suspend.

---

## Leader Key

OpenCode uses a `leader` key for many keybinds. This avoids conflicts in your terminal.

By default, `ctrl+x` is the leader key and many actions require you to first press the leader key and then the shortcut. For example, to start a new session you first press `ctrl+x` and then press `n`.

You don't need to use a leader key for your keybinds but we recommend doing so.

Some navigation keybinds intentionally do not use the leader key by default. For subagent sessions, the defaults are `session_child_first` = `<leader>down`, `session_child_cycle` = `right`, `session_child_cycle_reverse` = `left`, and `session_parent` = `up`.

`leader_timeout` controls how long OpenCode waits for the next key after the leader key. It defaults to `2000` milliseconds.

---

## Binding Values

A string can contain one shortcut or multiple comma-separated shortcuts. You can also use an array for multiple shortcuts.

For advanced cases, use an object with `key`, `event`, `preventDefault`, or `fallthrough`.

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "keybinds": {
    "messages_copy": ["<leader>y", "ctrl+shift+c"],
    "input_paste": {
      "key": "ctrl+v",
      "preventDefault": false
    }
  }
}
```

---

## Disable Keybind

You can disable a keybind by adding the key to `tui.json` with a value of `"none"` or `false`.

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "keybinds": {
    "session_compact": "none"
  }
}
```

---

## Desktop Prompt Shortcuts

The OpenCode desktop app prompt input supports common Readline/Emacs-style shortcuts for editing text. These are built-in and currently not configurable via `opencode.json`.

| Shortcut | Action                                   |
| -------- | ---------------------------------------- |
| `ctrl+a` | Move to start of current line            |
| `ctrl+e` | Move to end of current line              |
| `ctrl+b` | Move cursor back one character           |
| `ctrl+f` | Move cursor forward one character        |
| `alt+b`  | Move cursor back one word                |
| `alt+f`  | Move cursor forward one word             |
| `ctrl+d` | Delete character under cursor            |
| `ctrl+k` | Kill to end of line                      |
| `ctrl+u` | Kill to start of line                    |
| `ctrl+w` | Kill previous word                       |
| `alt+d`  | Kill next word                           |
| `ctrl+t` | Transpose characters                     |
| `ctrl+g` | Cancel popovers / abort running response |

---

## Shift+Enter

Some terminals don't send modifier keys with Enter by default. You may need to configure your terminal to send `Shift+Enter` as an escape sequence.

### Windows Terminal

Open your `settings.json` at:

```
%LOCALAPPDATA%\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\LocalState\settings.json
```

Add this to the root-level `actions` array:

```json
"actions": [
  {
    "command": {
      "action": "sendInput",
      "input": "\u001b[13;2u"
    },
    "id": "User.sendInput.ShiftEnterCustom"
  }
]
```

Add this to the root-level `keybindings` array:

```json
"keybindings": [
  {
    "keys": "shift+enter",
    "id": "User.sendInput.ShiftEnterCustom"
  }
]
```

Save the file and restart Windows Terminal or open a new tab.


</details>
