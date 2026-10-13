# OpenTUI 终端界面开发技能

整理核心 API、React 和 Solid 实现中的组件、布局、键盘输入、动画及测试参考；用于构建终端交互界面。

- 来源：Cline
- 资料类型：存档文档
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-06-04](<https://github.com/cline/cline/commit/4d15d161099de118d31fa153e325dd57255b54b6>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/cline/cline/blob/main/.agents/skills/opentui/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/OpenTUI 终端界面开发技能.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: opentui
description: Comprehensive OpenTUI skill for building terminal user interfaces. Covers the core imperative API, React reconciler, and Solid reconciler. Use for any TUI development task including components, layout, keyboard handling, animations, and testing.
metadata:
   references: core, react, solid
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# OpenTUI 平台技能

使用 OpenTUI 构建终端用户界面的综合技能。使用下面的决策树找到正确的框架和组件，然后加载详细的参考。

## 关键规则

**所有OpenTUI代码请遵循以下规则：**

1. **将`create-tui`用于新项目。**请参阅框架`REFERENCE.md`快速入门。
2. **`create-tui` 选项必须位于参数之前。** `bunx create-tui -t react my-app` 有效，`bunx create-tui my-app -t react` 有效 NOT。
3. **切勿直接调用`process.exit()`。**使用`renderer.destroy()`（参见`core/gotchas.md`）。
4. **文本样式需要React/Solid.**中的嵌套标签使用修饰符元素，而不是道具（请参阅`components/text-display.md`）。

## 如何使用此技能

### 参考文件结构

框架引用遵循 5 文件模式。横切概念是单文件指南。

`./references/<framework>/` 中的每个框架包含：

|文件|目的|何时阅读|
|------|---------|--------------|
| `REFERENCE.md` |概述、何时使用、快速入门|**始终先阅读**|
| `api.md` |运行时 API、组件、挂钩|编写代码|
| `configuration.md` |设置、tsconfig、捆绑|配置项目|
| `patterns.md` |常见模式、最佳实践|实施指导|
| `gotchas.md` |陷阱、限制、调试|故障排除|

`./references/<concept>/` 中的横切概念以 `REFERENCE.md` 作为切入点。

### 阅读顺序

1. 从您选择的框架的 `REFERENCE.md` 开始
2. 然后阅读与您的任务相关的其他文件：
   - 建筑组件 -> `api.md` + `components/<category>.md`
   - 设置项目 -> `configuration.md`
   - Layout/positioning -> `layout/REFERENCE.md`
   - Keyboard/input 处理 -> `keyboard/REFERENCE.md`
   - 动画 -> `animation/REFERENCE.md`
   - 故障排除 -> `gotchas.md` + `testing/REFERENCE.md`

### 示例路径

```
./references/react/REFERENCE.md           # Start here for React
./references/react/api.md              # React components and hooks
./references/solid/configuration.md    # Solid project setup
./references/components/inputs.md      # Input, Textarea, Select docs
./references/core/gotchas.md           # Core debugging tips
```

### 运行时注释

OpenTUI 在 Bun 上运行并使用 Zig 进行本机构建。请阅读 `./references/core/gotchas.md` 了解运行时要求和构建指南。

## 快速决策树

### “我应该使用哪个框架？”

```
Which framework?
├─ I want full control, maximum performance, no framework overhead
│  └─ core/ (imperative API)
├─ I know React, want familiar component patterns
│  └─ react/ (React reconciler)
├─ I want fine-grained reactivity, optimal re-renders
│  └─ solid/ (Solid reconciler)
└─ I'm building a library/framework on top of OpenTUI
   └─ core/ (imperative API)
```

### “我需要显示内容”

```
Display content?
├─ Plain or styled text -> components/text-display.md
├─ Container with borders/background -> components/containers.md
├─ Scrollable content area -> components/containers.md (scrollbox)
├─ ASCII art banner/title -> components/text-display.md (ascii-font)
├─ Data table with borders/wrapping -> components/code-diff.md (TextTable)
├─ Code with syntax highlighting -> components/code-diff.md
├─ Diff viewer (unified/split) -> components/code-diff.md
├─ Line numbers with diagnostics -> components/code-diff.md
└─ Markdown content (streaming) -> components/code-diff.md (markdown)
```

### “我需要用户输入”

```
User input?
├─ Single-line text field -> components/inputs.md (input)
├─ Multi-line text editor -> components/inputs.md (textarea)
├─ Select from a list (vertical) -> components/inputs.md (select)
├─ Tab-based selection (horizontal) -> components/inputs.md (tab-select)
└─ Custom keyboard shortcuts -> keyboard/REFERENCE.md
```

### “我需要layout/positioning”

```
Layout?
├─ Flexbox-style layouts (row, column, wrap) -> layout/REFERENCE.md
├─ Absolute positioning -> layout/patterns.md
├─ Responsive to terminal size -> layout/patterns.md
├─ Centering content -> layout/patterns.md
└─ Complex nested layouts -> layout/patterns.md
```

### “我需要动画”

```
Animations?
├─ Timeline-based animations -> animation/REFERENCE.md
├─ Easing functions -> animation/REFERENCE.md
├─ Property transitions -> animation/REFERENCE.md
└─ Looping animations -> animation/REFERENCE.md
```

### “我需要处理输入”

```
Input handling?
├─ Keyboard events (keypress, release) -> keyboard/REFERENCE.md
├─ Focus management -> keyboard/REFERENCE.md
├─ Paste events -> keyboard/REFERENCE.md
├─ Mouse events -> components/containers.md
├─ Text selection & copy-on-select -> keyboard/REFERENCE.md (selection)
└─ Clipboard (OSC 52) -> keyboard/REFERENCE.md (clipboard)
```

### “我需要测试我的 TUI”

```
Testing?
├─ Snapshot testing -> testing/REFERENCE.md
├─ Interaction testing -> testing/REFERENCE.md
├─ Test renderer setup -> testing/REFERENCE.md
└─ Debugging tests -> testing/REFERENCE.md
```

### “我需要 debug/troubleshoot”

```
Troubleshooting?
├─ Runtime errors, crashes -> <framework>/gotchas.md
├─ Layout issues -> layout/REFERENCE.md + layout/patterns.md
├─ Input/focus issues -> keyboard/REFERENCE.md
└─ Repro + regression tests -> testing/REFERENCE.md
```

### 故障排除索引

- 终端清理，崩溃 -> `core/gotchas.md`
- 文本样式不适用 -> `components/text-display.md`
- 输入 focus/shortcuts -> `keyboard/REFERENCE.md`
- 布局未对齐 -> `layout/REFERENCE.md`
- 片状快照 -> `testing/REFERENCE.md`

有关组件命名差异和文本修饰符的信息，请参阅 `components/REFERENCE.md`。

## 产品索引

### 框架
|框架|条目文件|描述|
|-----------|------------|-------------|
|核心| `./references/core/REFERENCE.md` |命令式 API，所有原语|
| React | `./references/react/REFERENCE.md` |React 声明性协调器 TUI|
|固体| `./references/solid/REFERENCE.md` |SolidJS 声明性协调器 TUI|

### 跨领域概念
|概念|条目文件|描述|
|---------|------------|-------------|
|布局| `./references/layout/REFERENCE.md` |Yoga/Flexbox布局系统|
|组件| `./references/components/REFERENCE.md` |按类别分类的组件参考|
|键盘| `./references/keyboard/REFERENCE.md` |键盘输入处理|
|动画| `./references/animation/REFERENCE.md` |基于时间轴的动画|
|测试| `./references/testing/REFERENCE.md` |测试渲染器和快照|

### 组件类别
|类别|条目文件|组件|
|----------|------------|------------|
|文字与显示| `./references/components/text-display.md` |文本、ascii 字体、样式文本|
|集装箱| `./references/components/containers.md` |框、滚动框、边框|
|输入| `./references/components/inputs.md` |输入、文本区域、选择、选项卡选择|
|代码与差异| `./references/components/code-diff.md` |代码、行号、差异、降价、文本表|

## 资源

**存储库**：https://github.com/anomalyco/opentui
**核心文档**：https://github.com/anomalyco/opentui/tree/main/packages/core/docs
**示例**：https://github.com/anomalyco/opentui/tree/main/packages/core/src/examples
**很棒的列表**： https://github.com/msmps/awesome-opentui


## 原文对照

<details>
<summary>展开完整原文</summary>

# OpenTUI Platform Skill

Consolidated skill for building terminal user interfaces with OpenTUI. Use decision trees below to find the right framework and components, then load detailed references.

## Critical Rules

**Follow these rules in all OpenTUI code:**

1. **Use `create-tui` for new projects.** See framework `REFERENCE.md` quick starts.
2. **`create-tui` options must come before arguments.** `bunx create-tui -t react my-app` works, `bunx create-tui my-app -t react` does NOT.
3. **Never call `process.exit()` directly.** Use `renderer.destroy()` (see `core/gotchas.md`).
4. **Text styling requires nested tags in React/Solid.** Use modifier elements, not props (see `components/text-display.md`).

## How to Use This Skill

### Reference File Structure

Framework references follow a 5-file pattern. Cross-cutting concepts are single-file guides.

Each framework in `./references/<framework>/` contains:

| File | Purpose | When to Read |
|------|---------|--------------|
| `REFERENCE.md` | Overview, when to use, quick start | **Always read first** |
| `api.md` | Runtime API, components, hooks | Writing code |
| `configuration.md` | Setup, tsconfig, bundling | Configuring a project |
| `patterns.md` | Common patterns, best practices | Implementation guidance |
| `gotchas.md` | Pitfalls, limitations, debugging | Troubleshooting |

Cross-cutting concepts in `./references/<concept>/` have `REFERENCE.md` as the entry point.

### Reading Order

1. Start with `REFERENCE.md` for your chosen framework
2. Then read additional files relevant to your task:
   - Building components -> `api.md` + `components/<category>.md`
   - Setting up project -> `configuration.md`
   - Layout/positioning -> `layout/REFERENCE.md`
   - Keyboard/input handling -> `keyboard/REFERENCE.md`
   - Animations -> `animation/REFERENCE.md`
   - Troubleshooting -> `gotchas.md` + `testing/REFERENCE.md`

### Example Paths

```
./references/react/REFERENCE.md           # Start here for React
./references/react/api.md              # React components and hooks
./references/solid/configuration.md    # Solid project setup
./references/components/inputs.md      # Input, Textarea, Select docs
./references/core/gotchas.md           # Core debugging tips
```

### Runtime Notes

OpenTUI runs on Bun and uses Zig for native builds. Read `./references/core/gotchas.md` for runtime requirements and build guidance.

## Quick Decision Trees

### "Which framework should I use?"

```
Which framework?
├─ I want full control, maximum performance, no framework overhead
│  └─ core/ (imperative API)
├─ I know React, want familiar component patterns
│  └─ react/ (React reconciler)
├─ I want fine-grained reactivity, optimal re-renders
│  └─ solid/ (Solid reconciler)
└─ I'm building a library/framework on top of OpenTUI
   └─ core/ (imperative API)
```

### "I need to display content"

```
Display content?
├─ Plain or styled text -> components/text-display.md
├─ Container with borders/background -> components/containers.md
├─ Scrollable content area -> components/containers.md (scrollbox)
├─ ASCII art banner/title -> components/text-display.md (ascii-font)
├─ Data table with borders/wrapping -> components/code-diff.md (TextTable)
├─ Code with syntax highlighting -> components/code-diff.md
├─ Diff viewer (unified/split) -> components/code-diff.md
├─ Line numbers with diagnostics -> components/code-diff.md
└─ Markdown content (streaming) -> components/code-diff.md (markdown)
```

### "I need user input"

```
User input?
├─ Single-line text field -> components/inputs.md (input)
├─ Multi-line text editor -> components/inputs.md (textarea)
├─ Select from a list (vertical) -> components/inputs.md (select)
├─ Tab-based selection (horizontal) -> components/inputs.md (tab-select)
└─ Custom keyboard shortcuts -> keyboard/REFERENCE.md
```

### "I need layout/positioning"

```
Layout?
├─ Flexbox-style layouts (row, column, wrap) -> layout/REFERENCE.md
├─ Absolute positioning -> layout/patterns.md
├─ Responsive to terminal size -> layout/patterns.md
├─ Centering content -> layout/patterns.md
└─ Complex nested layouts -> layout/patterns.md
```

### "I need animations"

```
Animations?
├─ Timeline-based animations -> animation/REFERENCE.md
├─ Easing functions -> animation/REFERENCE.md
├─ Property transitions -> animation/REFERENCE.md
└─ Looping animations -> animation/REFERENCE.md
```

### "I need to handle input"

```
Input handling?
├─ Keyboard events (keypress, release) -> keyboard/REFERENCE.md
├─ Focus management -> keyboard/REFERENCE.md
├─ Paste events -> keyboard/REFERENCE.md
├─ Mouse events -> components/containers.md
├─ Text selection & copy-on-select -> keyboard/REFERENCE.md (selection)
└─ Clipboard (OSC 52) -> keyboard/REFERENCE.md (clipboard)
```

### "I need to test my TUI"

```
Testing?
├─ Snapshot testing -> testing/REFERENCE.md
├─ Interaction testing -> testing/REFERENCE.md
├─ Test renderer setup -> testing/REFERENCE.md
└─ Debugging tests -> testing/REFERENCE.md
```

### "I need to debug/troubleshoot"

```
Troubleshooting?
├─ Runtime errors, crashes -> <framework>/gotchas.md
├─ Layout issues -> layout/REFERENCE.md + layout/patterns.md
├─ Input/focus issues -> keyboard/REFERENCE.md
└─ Repro + regression tests -> testing/REFERENCE.md
```

### Troubleshooting Index

- Terminal cleanup, crashes -> `core/gotchas.md`
- Text styling not applying -> `components/text-display.md`
- Input focus/shortcuts -> `keyboard/REFERENCE.md`
- Layout misalignment -> `layout/REFERENCE.md`
- Flaky snapshots -> `testing/REFERENCE.md`

For component naming differences and text modifiers, see `components/REFERENCE.md`.

## Product Index

### Frameworks
| Framework | Entry File | Description |
|-----------|------------|-------------|
| Core | `./references/core/REFERENCE.md` | Imperative API, all primitives |
| React | `./references/react/REFERENCE.md` | React reconciler for declarative TUI |
| Solid | `./references/solid/REFERENCE.md` | SolidJS reconciler for declarative TUI |

### Cross-Cutting Concepts
| Concept | Entry File | Description |
|---------|------------|-------------|
| Layout | `./references/layout/REFERENCE.md` | Yoga/Flexbox layout system |
| Components | `./references/components/REFERENCE.md` | Component reference by category |
| Keyboard | `./references/keyboard/REFERENCE.md` | Keyboard input handling |
| Animation | `./references/animation/REFERENCE.md` | Timeline-based animations |
| Testing | `./references/testing/REFERENCE.md` | Test renderer and snapshots |

### Component Categories
| Category | Entry File | Components |
|----------|------------|------------|
| Text & Display | `./references/components/text-display.md` | text, ascii-font, styled text |
| Containers | `./references/components/containers.md` | box, scrollbox, borders |
| Inputs | `./references/components/inputs.md` | input, textarea, select, tab-select |
| Code & Diff | `./references/components/code-diff.md` | code, line-number, diff, markdown, text-table |

## Resources

**Repository**: https://github.com/anomalyco/opentui
**Core Docs**: https://github.com/anomalyco/opentui/tree/main/packages/core/docs
**Examples**: https://github.com/anomalyco/opentui/tree/main/packages/core/src/examples
**Awesome List**: https://github.com/msmps/awesome-opentui


</details>
