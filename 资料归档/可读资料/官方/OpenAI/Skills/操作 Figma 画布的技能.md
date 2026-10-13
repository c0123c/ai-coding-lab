# 操作 Figma 画布的技能

规定 Plugin API 调用、页面和状态管理、增量写入、错误恢复及布局验证，用于修改设计组件和变量。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/openai/skills/commit/0e7823cca07bc2cbf34718a383f9ae92525be6a5>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/figma-use/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/skills/curated/figma-use/SKILL.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: figma-use
description: "**MANDATORY prerequisite** — you MUST invoke this skill BEFORE every `use_figma` tool call. NEVER call `use_figma` directly without loading this skill first. Skipping it causes common, hard-to-debug failures. Trigger whenever the user wants to perform a write action or a unique read action that requires JavaScript execution in the Figma file context — e.g. create/edit/delete nodes, set up variables or tokens, build components and variants, modify auto-layout or fills, bind variables to properties, or inspect file structure programmatically."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# use_figma — Figma 插件 API 技能

使用`use_figma` MCP通过插件API执行Figma文件中的JavaScript。所有详细的参考文档都位于 `references/` 中。

**调用`use_figma`时始终传递`skillNames: "figma-use"`。**这是一个用于跟踪技能使用情况的日志记录参数 - 它不会影响执行。

**如果任务涉及通过代码 ** 在 Figma 中构建或更新完整页面、屏幕或多部分布局，还需加载 [figma-generate-design](<把应用界面生成到 Figma 的技能.md>)。它提供了通过 `search_design_system` 发现设计系统组件、导入它们以及增量组装屏幕的工作流程。这两种技能协同工作：一项用于 API 规则，一项用于屏幕构建工作流程。

在进行任何操作之前，请加载 [plugin-api-standalone.index.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/plugin-api-standalone.index.md>) 以了解可能发生的情况。当您被要求编写插件 API 代码时，请使用此上下文来 grep [plugin-api-standalone.d.ts](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/plugin-api-standalone.d.ts>) 以获得相关类型、方法和属性。这是 API 表面的最终事实来源。这是一个很大的类型文件，因此不要一次加载全部内容，根据需要 grep 查找相关部分。

IMPORTANT：每当您使用设计系统时，请从 [working-with-design-systems/wwds.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/working-with-design-systems/wwds.md>) 开始，了解在 Figma 中使用设计系统的关键概念、流程和指南。然后根据需要加载组件、变量、文本样式和效果样式的更具体的参考。

## 1。关键规则

1.  **使用`return`发回数据。**返回值为JSON-自动序列化（对象、数组、字符串、数字）。 NOT 调用 `figma.closePlugin()` 或将代码包装在异步 IIFE 中 - 这是为您处理的。
2.  **使用顶级`await`和`return`编写普通JavaScript。**代码自动包装在异步上下文中。将 NOT 包裹在 `(async () => { ... })()` 中。
3.  `figma.notify()` **抛出“未实现”** - 永远不要使用它
3a. `getPluginData()` / `setPluginData()` 是 ** 不支持 `use_figma` 中的 ** — 不要使用它们。请改用 `getSharedPluginData()` / `setSharedPluginData()`（支持这些 ARE），或通过返回节点 ID 并将其传递给后续调用来跟踪节点 ID。
4.  `console.log()` 是 NOT 返回 — 使用 `return` 进行输出
5.  **以小步递增的方式工作。**将大型操作分解为多个`use_figma`调用。每个步骤后进行验证。这是避免错误的最重要的做法。
6.  颜色为 **0–1 范围 **（不是 0–255）： `{r: 1, g: 0, b: 0}` = 红色
7.  Fills/strokes 是 ** 只读数组 ** — 克隆、修改、重新分配
8.  在任何文本操作之前加载字体 **MUST**：`await figma.loadFontAsync({family, style})`
9.  **页面增量加载** - 使用`await figma.setCurrentPageAsync(page)`切换页面并加载其内容（请参阅下面的页面规则）
10. `setBoundVariableForPaint` 返回 **NEW** 油漆 — 必须捕获并重新分配
11. `createVariable` 接受集合 ** 对象或 ID 字符串 **（首选对象）
12. **`layoutSizingHorizontal/Vertical = 'FILL'` MUST 设置为 AFTER `parent.appendChild(child)`** — 在附加抛出之前设置。这同样适用于非自动布局节点上的 `'HUG'`。
13. **将新的顶级节点放置在远离 (0,0) 的位置。** 直接附加到页面的节点默认为 (0,0)。扫描`figma.currentPage.children`找到一个清晰的位置（例如，最右边节点的右侧）。这仅适用于页面级节点 - 嵌套在其他框架或自动布局容器内的节点由其父节点定位。请参阅 [ 陷阱 ](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/gotchas.md>)。
14. **关于`use_figma`错误，STOP。 NOT立即重试。**失败的脚本是**原子** - 如果脚本出错，则根本不会执行它，并且不会对文件进行任何更改。仔细阅读错误消息，修复脚本，然后重试。请参阅[错误恢复](<#6错误恢复和自我纠正>)。
15. **MUST `return` ALL created/mutated 节点 ID。** 每当脚本在画布上创建新节点或改变现有节点时，收集每个受影响的节点 ID 并将它们返回到结构化对象中（例如`return { createdNodeIds: [...], mutatedNodeIds: [...] }`）。这对于后续调用来引用、验证或清理这些节点至关重要。
16. **创建变量时始终显式设置`variable.scopes`。**默认的`ALL_SCOPES`会污染每个属性选择器 - 几乎从来不是你想要的。使用特定范围，例如 `["FRAME_FILL", "SHAPE_FILL"]` 表示背景，`["TEXT_FILL"]` 表示文本颜色，`["GAP"]` 表示间距等。有关完整列表，请参阅 [variable-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/variable-patterns.md>)。
17. **`await` 每个 Promise。** 永远不要让 Promise 处于等待状态 - 未等待的异步调用（例如，没有 `await` 的 `figma.loadFontAsync(...)`，或者没有 `await` 的 `figma.setCurrentPageAsync(page)`）将“即发即忘”，导致无提示故障或比赛条件。该脚本可能会在异步操作完成之前返回，从而导致数据丢失或更改未应用。

> 有关每个规则的详细WRONG/CORRECT示例，请参阅[陷阱和常见错误](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/gotchas.md>)。

## 2。页面规则（重要）

**页面上下文在 `use_figma` 调用之间重置 ** — `figma.currentPage` 每次从第一页开始。

### 切换页面

使用`await figma.setCurrentPageAsync(page)`切换页面并加载其内容。同步设置器 `figma.currentPage = page` ** 在 `use_figma` 运行时抛出错误 **。

```js
// Switch to a specific page (loads its content)
const targetPage = figma.root.children.find((p) => p.name === "My Page");
await figma.setCurrentPageAsync(targetPage);
// targetPage.children is now populated

// Iterate over all pages
for (const page of figma.root.children) {
  await figma.setCurrentPageAsync(page);
  // page.children is now loaded — read or modify them here
}
```

### 跨脚本运行

在每次 `use_figma` 调用开始时，`figma.currentPage` 重置为 **第一页 **。如果您的工作流程跨越多个调用并以非默认页面为目标，请在每次调用开始时调用 `await figma.setCurrentPageAsync(page)`。

您可以多次调用 `use_figma` 以增量方式构建文件状态，或在编写另一个脚本之前检索信息。例如，编写一个脚本来获取有关现有节点的元数据，`return` 该数据，然后在后续脚本中使用它来修改这些节点。

## 3。 `return` 是您的输出通道

agent 看到 **ONLY** 的值为您 `return`。其他一切都是看不见的。

- **返回ID（CRITICAL）**：创建或改变画布节点的每个脚本**MUST**返回所有受影响的节点ID - 例如`return { createdNodeIds: [...], mutatedNodeIds: [...] }`。这是硬性要求，不是可选的。
- **进度报告**：`return { createdNodeIds: [...], count: 5, errors: [] }`
- **错误信息**：抛出的错误会被自动捕获并返回——只需让它们传播或显式地`throw`。
- `console.log()` 输出是 ** 从未将 ** 返回到 agent
- 始终返回可操作的数据（ID、计数、状态），以便后续调用可以引用创建的对象

## 4。编辑器模式

`use_figma` 在 ** 设计模式 ** 下工作（editorType `"figma"`，默认）。 FigJam (`"figjam"`) 有一组不同的可用节点类型 - 大多数设计节点都被阻止在那里。

在设计模式中可用：矩形、框架、组件、文本、椭圆、星形、线、矢量、多边形、BooleanOperation、切片、页面、剖面、TextPath。

**在设计模式下屏蔽了**：粘滞、连接器、ShapeWithText、CodeBlock、幻灯片、SlideRow、网页。

## 5。增量工作流程（如何避免错误）

错误的最常见原因是尝试在单个 `use_figma` 调用中执行过多操作。 **分小步进行，并在每一步之后进行验证。**

### 图案

1. **首先检查。**在创建任何内容之前，运行只读`use_figma`以发现文件中已存在的内容 - 页面、组件、变量、命名约定。匹配那里的东西。
2. **每次调用只做一件事。**在一次调用中创建变量，在下一次调用中创建组件，在另一次调用中编写布局。不要尝试在一个脚本中构建整个屏幕。
3. **每次调用返回ID。**总是`return`创建节点ID、变量ID、集合ID作为对象（例如`return { createdNodeIds: [...] }`）。您将需要这些作为后续调用的输入。
4. **在每个步骤后进行验证。** 使用 `get_metadata` 验证结构（计数、名称、层次结构、位置）。在重大里程碑之后使用 `get_screenshot` 来捕获视觉问题。
5. **在继续之前先修复。**如果验证发现问题，请在继续下一步之前修复它。不要在破碎的基础上建造。

### 复杂任务的建议步骤顺序

```
Step 1: Inspect file — discover existing pages, components, variables, conventions
Step 2: Create tokens/variables (if needed)
       → validate with get_metadata
Step 3: Create individual components
       → validate with get_metadata + get_screenshot
Step 4: Compose layouts from component instances
       → validate with get_screenshot
Step 5: Final verification
```

### 每一步要验证什么

|之后...|检查`get_metadata`|检查`get_screenshot`|
|---|---|---|
|创建变量|集合计数、变量计数、模式名称| — |
|创建组件|子计数、变体名称、属性定义|变体可见，不折叠，网格可读|
|绑定变量|节点属性反映绑定|Colors/tokens 正确解析|
|组合布局|实例节点有 mainComponent，层次结构正确|无 cropped/clipped 文本，无重叠元素，间距正确|

## 6。错误恢复和自我纠正

**`use_figma` 是原子性的 — 失败的脚本不会执行。** 如果脚本出错，则不会对文件进行任何更改。文件保持与调用之前相同的状态。这意味着不存在部分节点，失败的脚本中不存在孤立元素，并且修复后重试是安全的。

### 当`use_figma`返回错误时

1. **STOP.** 不要立即修复代码并重试。
2. **仔细阅读错误消息。**准确理解出了什么问题——错误的API用法、缺少字体、无效的属性值等。
3. **如果错误不清楚**，请调用`get_metadata`或`get_screenshot`了解当前文件状态。
4. **根据错误信息修复脚本**。
5. **重试**更正的脚本。

### 常见的自我纠正模式

|错误信息|可能的原因|如何修复|
|---|---|---|
| `"not implemented"` |二手 `figma.notify()`|删除它 - 使用 `return` 进行输出|
| `"node must be an auto-layout frame..."` |在附加到自动布局父级之前设置 `FILL`/`HUG`|将 `appendChild` 移到 `layoutSizingX = 'FILL'` 之前|
| `"Setting figma.currentPage is not supported"` |使用同步页面设置器|使用`await figma.setCurrentPageAsync(page)`|
|财产价值超出范围|颜色通道 > 1（使用 0–255 而不是 0–1）|除以 255|
| `"Cannot read properties of null"` |节点不存在（错误ID，错误页面）|检查页面上下文，验证ID|
|脚本挂起/无响应|无限循环或未解决的承诺|检查是否有`while(true)`或缺失`await`；确保代码终止|
| `"The node with id X does not exist"` |父实例被子实例 `detachInstance()` 隐式分离，更改 ID|通过从稳定（非实例）父框架遍历来重新发现节点|

### 当脚本成功但结果看起来错误时

1. 调用 `get_metadata` 检查结构正确性（层次结构、计数、位置）。
2. 致电`get_screenshot`检查视觉正确性。仔细查看 cropped/clipped 文本（切断内容的行高）和重叠元素 - 这些很常见且容易错过。
3. 识别差异 - 是结构性的（错误的层次结构、缺少节点）还是视觉上的差异（错误的颜色、损坏的布局、剪裁的内容）？
4. 编写一个有针对性的修复脚本，仅修改损坏的部分 - 不要重新创建所有内容。

> 有关完整的验证工作流程，请参阅[验证和错误恢复](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/validation-and-recovery.md>)。

## 7。飞行前检查表

在提交 ANY `use_figma` 调用之前，请验证：

- [ ] 代码使用 `return` 发回数据 (NOT `figma.closePlugin()`)
- [ ] 代码是 NOT 包装在异步 IIFE 中（自动为您包装）
- [ ] `return` 值包括具有可操作信息（ID、计数）的结构化数据
- [ ] NO 在任何地方使用 `figma.notify()`
- [ ] NO 使用 `console.log()` 作为输出（使用 `return` 代替）
- [ ] 所有颜色均使用 0–1 范围（不是 0–255）
- [ ] Fills/strokes 被重新分配为新数组（未就地突变）
- [ ] 页面切换使用 `await figma.setCurrentPageAsync(page)` （同步设置器抛出）
- [ ] `layoutSizingVertical/Horizontal = 'FILL'` 设置为 AFTER `parent.appendChild(child)`
- [ ] `loadFontAsync()` 称为 BEFORE 任何文本属性更改
- [ ] `lineHeight`/`letterSpacing` 使用 `{unit, value}` 格式（不是裸数字）
- [ ] `resize()` 称为 BEFORE 设置大小模式（调整大小会将其重置为 FIXED）
- [ ] 对于多步骤工作流程：先前调用的 ID 作为字符串文字（而不是变量）传递
- [ ] 新的顶级节点远离 (0,0) 以避免与现有内容重叠
- [ ] ALL created/mutated 节点 ID 被收集并包含在 `return` 值中
- [ ] 每个异步调用（`loadFontAsync`、`setCurrentPageAsync`、`importComponentByKeyAsync` 等）都是 `await` ed — 没有即发即忘的承诺

## 8。在创建之前发现约定

**在创建任何内容之前始终检查 Figma 文件。** 不同的文件使用不同的命名约定、变量结构和组件模式。您的代码应该与已有的代码相匹配，而不是强加新的约定。

当对任何约定（命名、范围、结构）有疑问时，请首先检查 Figma 文件，然后检查用户的代码库。只有当两者都不存在时才回退到常见模式。

### 快速检查脚本

**列出所有页面和顶级节点：**
```js
const pages = figma.root.children.map(p => `${p.name} id=${p.id} children=${p.children.length}`);
return pages.join('\n');
```

**列出所有页面中的现有组件：**
```js
const results = [];
for (const page of figma.root.children) {
  await figma.setCurrentPageAsync(page);
  page.findAll(n => {
    if (n.type === 'COMPONENT' || n.type === 'COMPONENT_SET')
      results.push(`[${page.name}] ${n.name} (${n.type}) id=${n.id}`);
    return false;
  });
}
return results.join('\n');
```

**列出现有变量集合及其约定：**
```js
const collections = await figma.variables.getLocalVariableCollectionsAsync();
const results = collections.map(c => ({
  name: c.name, id: c.id,
  varCount: c.variableIds.length,
  modes: c.modes.map(m => m.name)
}));
return results;
```

## 9。参考文档

根据您的任务涉及的内容根据需要加载这些：

|医生|何时加载|它涵盖什么|
|-----|-------------|----------------|
| [gotchas.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/gotchas.md>) |在任何 `use_figma` 之前|WRONG/CORRECT 代码示例中每个已知的陷阱|
| [common-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/common-patterns.md>) |需要工作代码示例|脚本支架：形状、文本、自动布局、变量、组件、多步骤工作流程|
| [plugin-api-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/plugin-api-patterns.md>) |Creating/editing 节点|填充、描边、自动布局、效果、分组、克隆、样式|
| [api-reference.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/api-reference.md>) |需要精确的 API 表面|节点创建、变量 API、核心属性、哪些有效、哪些无效|
| [validation-and-recovery.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/validation-and-recovery.md>) |多步写入或错误恢复|`get_metadata` 与 `get_screenshot` 工作流程，强制错误恢复步骤|
| [component-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/component-patterns.md>) |创建 components/variants|combineAsVariants，组件属性，INSTANCE_SWAP，变体布局，发现现有组件，元数据遍历|
| [variable-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/variable-patterns.md>) |Creating/binding 变量|集合、模式、范围、别名、绑定模式、发现现有变量|
| [text-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/text-style-patterns.md>) |Creating/applying 文本样式|类型渐变、字体探测、列出样式、将样式应用于节点|
| [effect-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/effect-style-patterns.md>) |Creating/applying 效果样式|阴影、列出样式、将样式应用于节点|
| [plugin-api-standalone.index.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/plugin-api-standalone.index.md>) |需要了解完整的API表面|插件中所有类型、方法和属性的索引 API|
| [plugin-api-standalone.d.ts](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/plugin-api-standalone.d.ts>) |需要精确的类型签名|完整的打字文件 - grep 特定符号，不要一次加载全部|

## 10。片段示例

您将在此处看到整个文档中的片段。这些片段包含可以重新调整用途的有用插件 API 代码。按原样使用它们，或者作为您的入门代码。如果有一些关键概念最好记录为通用片段，请将它们调出并写入磁盘，以便将来可以重复使用。


## 原文对照

<details>
<summary>展开完整原文</summary>

# use_figma — Figma Plugin API Skill

Use `use_figma` MCP to execute JavaScript in Figma files via the Plugin API. All detailed reference docs live in `references/`.

**Always pass `skillNames: "figma-use"` when calling `use_figma`.** This is a logging parameter used to track skill usage — it does not affect execution.

**If the task involves building or updating a full page, screen, or multi-section layout in Figma from code**, also load [figma-generate-design](<把应用界面生成到 Figma 的技能.md>). It provides the workflow for discovering design system components via `search_design_system`, importing them, and assembling screens incrementally. Both skills work together: this one for the API rules, that one for the screen-building workflow.

Before anything, load [plugin-api-standalone.index.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/plugin-api-standalone.index.md>) to understand what is possible. When you are asked to write plugin API code, use this context to grep [plugin-api-standalone.d.ts](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/plugin-api-standalone.d.ts>) for relevant types, methods, and properties. This is the definitive source of truth for the API surface. It is a large typings file, so do not load it all at once, grep for relevant sections as needed.

IMPORTANT: Whenever you work with design systems, start with [working-with-design-systems/wwds.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/working-with-design-systems/wwds.md>) to understand the key concepts, processes, and guidelines for working with design systems in Figma. Then load the more specific references for components, variables, text styles, and effect styles as needed.

## 1. Critical Rules

1.  **Use `return` to send data back.** The return value is JSON-serialized automatically (objects, arrays, strings, numbers). Do NOT call `figma.closePlugin()` or wrap code in an async IIFE — this is handled for you.
2.  **Write plain JavaScript with top-level `await` and `return`.** Code is automatically wrapped in an async context. Do NOT wrap in `(async () => { ... })()`.
3.  `figma.notify()` **throws "not implemented"** — never use it
3a. `getPluginData()` / `setPluginData()` are **not supported** in `use_figma` — do not use them. Use `getSharedPluginData()` / `setSharedPluginData()` instead (these ARE supported), or track node IDs by returning them and passing them to subsequent calls.
4.  `console.log()` is NOT returned — use `return` for output
5.  **Work incrementally in small steps.** Break large operations into multiple `use_figma` calls. Validate after each step. This is the single most important practice for avoiding bugs.
6.  Colors are **0–1 range** (not 0–255): `{r: 1, g: 0, b: 0}` = red
7.  Fills/strokes are **read-only arrays** — clone, modify, reassign
8.  Font **MUST** be loaded before any text operation: `await figma.loadFontAsync({family, style})`
9.  **Pages load incrementally** — use `await figma.setCurrentPageAsync(page)` to switch pages and load their content (see Page Rules below)
10. `setBoundVariableForPaint` returns a **NEW** paint — must capture and reassign
11. `createVariable` accepts collection **object or ID string** (object preferred)
12. **`layoutSizingHorizontal/Vertical = 'FILL'` MUST be set AFTER `parent.appendChild(child)`** — setting before append throws. Same applies to `'HUG'` on non-auto-layout nodes.
13. **Position new top-level nodes away from (0,0).** Nodes appended directly to the page default to (0,0). Scan `figma.currentPage.children` to find a clear position (e.g., to the right of the rightmost node). This only applies to page-level nodes — nodes nested inside other frames or auto-layout containers are positioned by their parent. See [Gotchas](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/gotchas.md>).
14. **On `use_figma` error, STOP. Do NOT immediately retry.** Failed scripts are **atomic** — if a script errors, it is not executed at all and no changes are made to the file. Read the error message carefully, fix the script, then retry. See [Error Recovery](<#6-error-recovery--self-correction>).
15. **MUST `return` ALL created/mutated node IDs.** Whenever a script creates new nodes or mutates existing ones on the canvas, collect every affected node ID and return them in a structured object (e.g. `return { createdNodeIds: [...], mutatedNodeIds: [...] }`). This is essential for subsequent calls to reference, validate, or clean up those nodes.
16. **Always set `variable.scopes` explicitly when creating variables.** The default `ALL_SCOPES` pollutes every property picker — almost never what you want. Use specific scopes like `["FRAME_FILL", "SHAPE_FILL"]` for backgrounds, `["TEXT_FILL"]` for text colors, `["GAP"]` for spacing, etc. See [variable-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/variable-patterns.md>) for the full list.
17. **`await` every Promise.** Never leave a Promise unawaited — unawaited async calls (e.g. `figma.loadFontAsync(...)` without `await`, or `figma.setCurrentPageAsync(page)` without `await`) will fire-and-forget, causing silent failures or race conditions. The script may return before the async operation completes, leading to missing data or half-applied changes.

> For detailed WRONG/CORRECT examples of each rule, see [Gotchas & Common Mistakes](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/gotchas.md>).

## 2. Page Rules (Critical)

**Page context resets between `use_figma` calls** — `figma.currentPage` starts on the first page each time.

### Switching pages

Use `await figma.setCurrentPageAsync(page)` to switch pages and load their content. The sync setter `figma.currentPage = page` **throws an error** in `use_figma` runtimes.

```js
// Switch to a specific page (loads its content)
const targetPage = figma.root.children.find((p) => p.name === "My Page");
await figma.setCurrentPageAsync(targetPage);
// targetPage.children is now populated

// Iterate over all pages
for (const page of figma.root.children) {
  await figma.setCurrentPageAsync(page);
  // page.children is now loaded — read or modify them here
}
```

### Across script runs

`figma.currentPage` resets to the **first page** at the start of each `use_figma` call. If your workflow spans multiple calls and targets a non-default page, call `await figma.setCurrentPageAsync(page)` at the start of each invocation.

You can call `use_figma` multiple times to incrementally build on the file state, or to retrieve information before writing another script. For example, write a script to get metadata about existing nodes, `return` that data, then use it in a subsequent script to modify those nodes.

## 3. `return` Is Your Output Channel

The agent sees **ONLY** the value you `return`. Everything else is invisible.

- **Returning IDs (CRITICAL)**: Every script that creates or mutates canvas nodes **MUST** return all affected node IDs — e.g. `return { createdNodeIds: [...], mutatedNodeIds: [...] }`. This is a hard requirement, not optional.
- **Progress reporting**: `return { createdNodeIds: [...], count: 5, errors: [] }`
- **Error info**: Thrown errors are automatically captured and returned — just let them propagate or `throw` explicitly.
- `console.log()` output is **never** returned to the agent
- Always return actionable data (IDs, counts, status) so subsequent calls can reference created objects

## 4. Editor Mode

`use_figma` works in **design mode** (editorType `"figma"`, the default). FigJam (`"figjam"`) has a different set of available node types — most design nodes are blocked there.

Available in design mode: Rectangle, Frame, Component, Text, Ellipse, Star, Line, Vector, Polygon, BooleanOperation, Slice, Page, Section, TextPath.

**Blocked** in design mode: Sticky, Connector, ShapeWithText, CodeBlock, Slide, SlideRow, Webpage.

## 5. Incremental Workflow (How to Avoid Bugs)

The most common cause of bugs is trying to do too much in a single `use_figma` call. **Work in small steps and validate after each one.**

### The pattern

1. **Inspect first.** Before creating anything, run a read-only `use_figma` to discover what already exists in the file — pages, components, variables, naming conventions. Match what's there.
2. **Do one thing per call.** Create variables in one call, create components in the next, compose layouts in another. Don't try to build an entire screen in one script.
3. **Return IDs from every call.** Always `return` created node IDs, variable IDs, collection IDs as objects (e.g. `return { createdNodeIds: [...] }`). You'll need these as inputs to subsequent calls.
4. **Validate after each step.** Use `get_metadata` to verify structure (counts, names, hierarchy, positions). Use `get_screenshot` after major milestones to catch visual issues.
5. **Fix before moving on.** If validation reveals a problem, fix it before proceeding to the next step. Don't build on a broken foundation.

### Suggested step order for complex tasks

```
Step 1: Inspect file — discover existing pages, components, variables, conventions
Step 2: Create tokens/variables (if needed)
       → validate with get_metadata
Step 3: Create individual components
       → validate with get_metadata + get_screenshot
Step 4: Compose layouts from component instances
       → validate with get_screenshot
Step 5: Final verification
```

### What to validate at each step

| After... | Check with `get_metadata` | Check with `get_screenshot` |
|---|---|---|
| Creating variables | Collection count, variable count, mode names | — |
| Creating components | Child count, variant names, property definitions | Variants visible, not collapsed, grid readable |
| Binding variables | Node properties reflect bindings | Colors/tokens resolved correctly |
| Composing layouts | Instance nodes have mainComponent, hierarchy correct | No cropped/clipped text, no overlapping elements, correct spacing |

## 6. Error Recovery & Self-Correction

**`use_figma` is atomic — failed scripts do not execute.** If a script errors, no changes are made to the file. The file remains in the same state as before the call. This means there are no partial nodes, no orphaned elements from the failed script, and retrying after a fix is safe.

### When `use_figma` returns an error

1. **STOP.** Do not immediately fix the code and retry.
2. **Read the error message carefully.** Understand exactly what went wrong — wrong API usage, missing font, invalid property value, etc.
3. **If the error is unclear**, call `get_metadata` or `get_screenshot` to understand the current file state.
4. **Fix the script** based on the error message.
5. **Retry** the corrected script.

### Common self-correction patterns

| Error message | Likely cause | How to fix |
|---|---|---|
| `"not implemented"` | Used `figma.notify()` | Remove it — use `return` for output |
| `"node must be an auto-layout frame..."` | Set `FILL`/`HUG` before appending to auto-layout parent | Move `appendChild` before `layoutSizingX = 'FILL'` |
| `"Setting figma.currentPage is not supported"` | Used sync page setter | Use `await figma.setCurrentPageAsync(page)` |
| Property value out of range | Color channel > 1 (used 0–255 instead of 0–1) | Divide by 255 |
| `"Cannot read properties of null"` | Node doesn't exist (wrong ID, wrong page) | Check page context, verify ID |
| Script hangs / no response | Infinite loop or unresolved promise | Check for `while(true)` or missing `await`; ensure code terminates |
| `"The node with id X does not exist"` | Parent instance was implicitly detached by a child `detachInstance()`, changing IDs | Re-discover nodes by traversal from a stable (non-instance) parent frame |

### When the script succeeds but the result looks wrong

1. Call `get_metadata` to check structural correctness (hierarchy, counts, positions).
2. Call `get_screenshot` to check visual correctness. Look closely for cropped/clipped text (line heights cutting off content) and overlapping elements — these are common and easy to miss.
3. Identify the discrepancy — is it structural (wrong hierarchy, missing nodes) or visual (wrong colors, broken layout, clipped content)?
4. Write a targeted fix script that modifies only the broken parts — don't recreate everything.

> For the full validation workflow, see [Validation & Error Recovery](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/validation-and-recovery.md>).

## 7. Pre-Flight Checklist

Before submitting ANY `use_figma` call, verify:

- [ ] Code uses `return` to send data back (NOT `figma.closePlugin()`)
- [ ] Code is NOT wrapped in an async IIFE (auto-wrapped for you)
- [ ] `return` value includes structured data with actionable info (IDs, counts)
- [ ] NO usage of `figma.notify()` anywhere
- [ ] NO usage of `console.log()` as output (use `return` instead)
- [ ] All colors use 0–1 range (not 0–255)
- [ ] Fills/strokes are reassigned as new arrays (not mutated in place)
- [ ] Page switches use `await figma.setCurrentPageAsync(page)` (sync setter throws)
- [ ] `layoutSizingVertical/Horizontal = 'FILL'` is set AFTER `parent.appendChild(child)`
- [ ] `loadFontAsync()` called BEFORE any text property changes
- [ ] `lineHeight`/`letterSpacing` use `{unit, value}` format (not bare numbers)
- [ ] `resize()` is called BEFORE setting sizing modes (resize resets them to FIXED)
- [ ] For multi-step workflows: IDs from previous calls are passed as string literals (not variables)
- [ ] New top-level nodes are positioned away from (0,0) to avoid overlapping existing content
- [ ] ALL created/mutated node IDs are collected and included in the `return` value
- [ ] Every async call (`loadFontAsync`, `setCurrentPageAsync`, `importComponentByKeyAsync`, etc.) is `await`ed — no fire-and-forget Promises

## 8. Discover Conventions Before Creating

**Always inspect the Figma file before creating anything.** Different files use different naming conventions, variable structures, and component patterns. Your code should match what's already there, not impose new conventions.

When in doubt about any convention (naming, scoping, structure), check the Figma file first, then the user's codebase. Only fall back to common patterns when neither exists.

### Quick inspection scripts

**List all pages and top-level nodes:**
```js
const pages = figma.root.children.map(p => `${p.name} id=${p.id} children=${p.children.length}`);
return pages.join('\n');
```

**List existing components across all pages:**
```js
const results = [];
for (const page of figma.root.children) {
  await figma.setCurrentPageAsync(page);
  page.findAll(n => {
    if (n.type === 'COMPONENT' || n.type === 'COMPONENT_SET')
      results.push(`[${page.name}] ${n.name} (${n.type}) id=${n.id}`);
    return false;
  });
}
return results.join('\n');
```

**List existing variable collections and their conventions:**
```js
const collections = await figma.variables.getLocalVariableCollectionsAsync();
const results = collections.map(c => ({
  name: c.name, id: c.id,
  varCount: c.variableIds.length,
  modes: c.modes.map(m => m.name)
}));
return results;
```

## 9. Reference Docs

Load these as needed based on what your task involves:

| Doc | When to load | What it covers |
|-----|-------------|----------------|
| [gotchas.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/gotchas.md>) | Before any `use_figma` | Every known pitfall with WRONG/CORRECT code examples |
| [common-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/common-patterns.md>) | Need working code examples | Script scaffolds: shapes, text, auto-layout, variables, components, multi-step workflows |
| [plugin-api-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/plugin-api-patterns.md>) | Creating/editing nodes | Fills, strokes, Auto Layout, effects, grouping, cloning, styles |
| [api-reference.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/api-reference.md>) | Need exact API surface | Node creation, variables API, core properties, what works and what doesn't |
| [validation-and-recovery.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/validation-and-recovery.md>) | Multi-step writes or error recovery | `get_metadata` vs `get_screenshot` workflow, mandatory error recovery steps |
| [component-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/component-patterns.md>) | Creating components/variants | combineAsVariants, component properties, INSTANCE_SWAP, variant layout, discovering existing components, metadata traversal |
| [variable-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/variable-patterns.md>) | Creating/binding variables | Collections, modes, scopes, aliasing, binding patterns, discovering existing variables |
| [text-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/text-style-patterns.md>) | Creating/applying text styles | Type ramps, font probing, listing styles, applying styles to nodes |
| [effect-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/effect-style-patterns.md>) | Creating/applying effect styles | Drop shadows, listing styles, applying styles to nodes |
| [plugin-api-standalone.index.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/plugin-api-standalone.index.md>) | Need to understand the full API surface | Index of all types, methods, and properties in the Plugin API |
| [plugin-api-standalone.d.ts](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/plugin-api-standalone.d.ts>) | Need exact type signatures | Full typings file — grep for specific symbols, don't load all at once |

## 10. Snippet examples

You will see snippets throughout documentation here. These snippets contain useful plugin API code that can be repurposed. Use them as is, or as starter code as you go. If there are key concepts that are best documented as generic snippets, call them out and write to disk so you can reuse in the future.


</details>
