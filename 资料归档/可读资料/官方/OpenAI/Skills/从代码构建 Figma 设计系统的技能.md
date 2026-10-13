# 从代码构建 Figma 设计系统的技能

按阶段整理变量、主题、基础样式和组件库，说明复用判断、进度管理和检查，帮助对齐代码与设计资源。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/openai/skills/commit/0e7823cca07bc2cbf34718a383f9ae92525be6a5>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/figma-generate-library/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/从代码构建 Figma 设计系统的技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: figma-generate-library
description: "Build or update a professional-grade design system in Figma from a codebase. Use when the user wants to create variables/tokens, build component libraries, set up theming (light/dark modes), document foundations, or reconcile gaps between code and Figma. This skill teaches WHAT to build and in WHAT ORDER — it complements the `figma-use` skill which teaches HOW to call the Plugin API. Both skills should be loaded together."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 设计系统构建器 — Figma MCP 技能

在 Figma 中构建与代码匹配的专业级设计系统。该技能可以跨 20–100+ `use_figma` 调用编排多阶段工作流程，强制执行现实世界设计系统的质量模式（Material 3、Polaris、Figma UI3、Simple DS）。

**先决条件**：每次`use_figma`调用时也会加载`figma-use`技能MUST。它提供插件API语法规则（返回模式、页面重置、ID返回、字体加载、颜色范围）。该技能提供设计系统领域知识和工作流程编排。

**作为此技能的一部分，调用 `use_figma` 时始终传递 `skillNames: "figma-generate-library"`。** 这是一个日志记录参数 — 它不会影响执行。

---

## 1。最重要的一条规则

**这是NEVER一次性任务。**构建设计系统需要跨多个阶段的20–100+`use_figma`调用，并且在它们之间有强制性的用户检查点。任何在一次调用 WILL 中创建所有内容的尝试都会产生损坏、不完整或无法恢复的结果。将每个操作分解为最小的有用单元，验证，获取反馈，然后继续。

---

## 2。强制性工作流程

每个设计系统的构建都遵循这个阶段顺序。跳过或重新排序阶段会导致结构性故障，且修复成本高昂。

```
Phase 0: DISCOVERY (always first — no use_figma writes yet)
  0a. Analyze codebase → extract tokens, components, naming conventions
  0b. Inspect Figma file → pages, variables, components, styles, existing conventions
  0c. Search subscribed libraries → use search_design_system for reusable assets
  0d. Lock v1 scope → agree on exact token set + component list before any creation
  0e. Map code → Figma → resolve conflicts (code and Figma disagree = ask user)
  ✋ USER CHECKPOINT: present full plan, await explicit approval

Phase 1: FOUNDATIONS (tokens first — always before components)
  1a. Create variable collections and modes
  1b. Create primitive variables (raw values, 1 mode)
  1c. Create semantic variables (aliased to primitives, mode-aware)
  1d. Set scopes on ALL variables
  1e. Set code syntax on ALL variables
  1f. Create effect styles (shadows) and text styles (typography)
  → Exit criteria: every token from the agreed plan exists, all scopes set, all code syntax set
  ✋ USER CHECKPOINT: show variable summary, await approval

Phase 2: FILE STRUCTURE (before components)
  2a. Create page skeleton: Cover → Getting Started → Foundations → --- → Components → --- → Utilities
  2b. Create foundations documentation pages (color swatches, type specimens, spacing bars)
  → Exit criteria: all planned pages exist, foundations docs are navigable
  ✋ USER CHECKPOINT: show page list + screenshot, await approval

Phase 3: COMPONENTS (one at a time — never batch)
  For EACH component (in dependency order: atoms before molecules):
    3a. Create dedicated page
    3b. Build base component with auto-layout + full variable bindings
    3c. Create all variant combinations (combineAsVariants + grid layout)
    3d. Add component properties (TEXT, BOOLEAN, INSTANCE_SWAP)
    3e. Link properties to child nodes
    3f. Add page documentation (title, description, usage notes)
    3g. Validate: get_metadata (structure) + get_screenshot (visual)
    3h. Optional: lightweight Code Connect mapping while context is fresh
    → Exit criteria: variant count correct, all bindings verified, screenshot looks right
    ✋ USER CHECKPOINT per component: show screenshot, await approval before next component

Phase 4: INTEGRATION + QA (final pass)
  4a. Finalize all Code Connect mappings
  4b. Accessibility audit (contrast, min touch targets, focus visibility)
  4c. Naming audit (no duplicates, no unnamed nodes, consistent casing)
  4d. Unresolved bindings audit (no hardcoded fills/strokes remaining)
  4e. Final review screenshots of every page
  ✋ USER CHECKPOINT: complete sign-off
```

---

## 3。关键规则

**插件API基础知识**（来自use_figma技能 - 也在这里强制执行）：
- 使用 `return` 发回数据（自动序列化）。将 NOT 包裹在 IIFE 中或调用 closePlugin。
- 每个返回值中返回ALL created/mutated节点ID
- 页面上下文重置每次调用 - 始终在开始时 `await figma.setCurrentPageAsync(page)`
- `figma.notify()` 抛出 — 切勿使用它
- 颜色范围为 0–1，而不是 0–255
- 在写入任何文本之前加载字体 MUST：`await figma.loadFontAsync({family, style})`

**设计系统规则**：
1. **变量 BEFORE 组件** — 组件绑定到变量。没有令牌=没有组件。
2. **创建前检查** — 运行只读`use_figma`以发现现有约定。匹配他们。
3. **每个组件一页** *（默认）* - 例外：紧密相关的系列（例如，输入+助手）可能共享一个具有清晰部分分隔的页面。
4. **将视觉属性绑定到变量** *（默认）* — 填充、描边、填充、半径、间隙。例外：故意固定几何形状（图标像素网格大小、静态分隔线）。
5. **每个变量的作用域** — NEVER保留为`ALL_SCOPES`。背景：`FRAME_FILL, SHAPE_FILL`。文本：`TEXT_FILL`。边框：`STROKE_COLOR`。间距：`GAP`。半径：`CORNER_RADIUS`。基元：`[]`（隐藏）。
6. **每个变量的代码语法** - WEB语法MUST使用`var()`包装器：`var(--color-bg-primary)`，而不是`--color-bg-primary`。使用代码库中的实际 CSS 变量名称。 ANDROID/iOS NOT 使用包装器。
7. **原语的别名语义** — `{ type: 'VARIABLE_ALIAS', id: primitiveVar.id }`。切勿在语义层中重复原始值。
8. **combineAsVariants** 之后的位置变体 — 它们堆叠在 (0,0)。手动网格布局+调整大小。
9. **INSTANCE_SWAP 图标 ** — 切勿为每个图标创建变体。 Cap 变体矩阵：如果 Size × Style × State > 30 组合，则拆分为子组件。
10. **确定性命名** — 使用一致、唯一的节点名称进行幂等清理和可恢复性。通过返回值和状态分类账跟踪创建的节点 ID。
11. **无破坏性清理** — 清理脚本通过名称约定或返回的 ID 来识别节点，而不是通过猜测。
12. **在继续之前进行验证** - 切勿在未经验证的工作上进行构建。每次创建后 `get_metadata`，每个组件后 `get_screenshot`。
13. **NEVER 并行 `use_figma` 调用 ** — Figma 状态突变必须严格按顺序进行。即使您的工具支持并行调用，也切勿同时运行两个 use_figma 调用。
14. **永远不要幻觉节点 ID** — 始终从先前调用返回的状态分类帐中读取 ID。切勿从记忆中重建或猜测 ID。
15. **使用帮助程序脚本** — 将 `scripts/` 中的脚本嵌入到您的 use_figma 调用中。不要从头开始编写 200 行内联脚本。
16. **显式阶段批准** - 在每个检查点，显式命名下一阶段。如果您询问阶段 1，“看起来不错”并不表示批准进入阶段 3。

---

## 4。状态管理（长工作流程所需）

> **`getPluginData()` / `setPluginData()` 是 `use_figma` 中支持的 NOT。** 使用 `getSharedPluginData()` / `setSharedPluginData()` 代替（这些 ARE 支持），或使用基于名称的查找和状态分类账（返回的 ID）。

|实体类型|幂等性密钥|如何检查存在|
|-------------|----------------|----------------------|
|场景节点（页面、框架、组件）|`setSharedPluginData('dsb', 'key', value)` 或唯一名称|`node.getSharedPluginData('dsb', 'key')` 或 `page.findOne(n => n.name === 'Button')`|
|变量|集合中的名称| `(await figma.variables.getLocalVariablesAsync()).find(v => v.name === name && v.variableCollectionId === collId)` |
|风格|名称| `getLocalTextStyles().find(s => s.name === name)` |

创建后立即标记每个创建的**场景节点**：
```javascript
node.setSharedPluginData('dsb', 'run_id', RUN_ID);        // identifies this build run
node.setSharedPluginData('dsb', 'phase', 'phase3');        // which phase created it
node.setSharedPluginData('dsb', 'key', 'component/button');// unique logical key
```

**状态持久性**：NOT是否仅依赖于状态分类帐的对话上下文。将其写入磁盘：
```
/tmp/dsb-state-{RUN_ID}.json
```
每回合开始时重新读取该文件。在较长的工作流程中，对话上下文将被截断——文件是事实的来源。

维护状态分类账跟踪：
```json
{
  "runId": "ds-build-2024-001",
  "phase": "phase3",
  "step": "component-button",
  "entities": {
    "collections": { "primitives": "id:...", "color": "id:..." },
    "variables": { "color/bg/primary": "id:...", "spacing/sm": "id:..." },
    "pages": { "Cover": "id:...", "Button": "id:..." },
    "components": { "Button": "id:..." }
  },
  "pendingValidations": ["Button:screenshot"],
  "completedSteps": ["phase0", "phase1", "phase2", "component-avatar"]
}
```

**每次创建之前进行幂等性检查**：按名称+状态分类帐查询ID。如果存在，则跳过或更新——切勿重复。

**恢复协议**：在会话开始时或上下文截断后，运行只读`use_figma`以按名称扫描所有页面、组件、变量和样式以重建`{key → id}`映射。然后从磁盘重新读取状态文件（如果可用）。

**继续提示**（在新聊天中恢复时将其提供给用户）：
> “我正在继续设计系统构建。运行ID：{RUN_ID}。加载figma-generate-library技能并从最后完成的步骤恢复。”

---

## 5。 search_design_system — 重用决策矩阵

在阶段 0 中搜索 FIRST，然后在每个组件创建之前再次搜索。

```
search_design_system({ query, fileKey, includeComponents: true, includeVariables: true, includeStyles: true })
```

**如果**所有这些都为真，则重用：
- 组件属性 API 满足您的需求（相同的变体轴，兼容的类型）
- 令牌绑定模型兼容（使用相同或可别名的变量）
- 命名约定与目标文件匹配
- 组件是可编辑的（未锁定在您不拥有的远程库中）

**如果**其中任何一个则重建：
- API 不兼容（不同的属性名称、错误的变体模型）
- 令牌模型不兼容（硬编码值、不同的变量模式）
- 所有权问题（无法修改库）

**如果**视觉匹配但API不兼容则换行：
- 将库组件作为新包装组件内的嵌套实例导入
- 露出包装纸上干净的API

**三路优先级**：本地已有→订阅库导入→新建。

---

## 6。用户检查点

强制性的。设计决策需要人的判断。

|之后|所需工件|询问|
|-------|-------------------|-----|
|发现+范围锁定|代币列表、组件列表、差距分析|“这是我的计划。在我创造任何东西之前先批准一下？”|
|基础|变量摘要（N 个集合、M 个变量、K 个模式）、样式列表|“所有令牌均已创建。在文件结构之前检查一下吗？”|
|文件结构|页面列表+截图|“页面已设置。在组件之前检查？”|
|各组件|组件页面的get_screenshot|“这是具有 N 个变体的 [ 组件 ]。对吗？”|
|每个冲突（代码≠ Figma）|显示两个版本|“代码说 X，Figma 有 Y。哪一个获胜？”|
|最终QA|每页截图+审核报告|“完成。结束？”|

**如果用户拒绝**：在继续之前修复。永远不要在被拒绝的工作的基础上继续创作。

---

## 7。命名约定

匹配现有文件约定。如果重新开始：

**变量**（斜杠分隔）：
```
color/bg/primary     color/text/secondary    color/border/default
spacing/xs  spacing/sm  spacing/md  spacing/lg  spacing/xl  spacing/2xl
radius/none  radius/sm  radius/md  radius/lg  radius/full
typography/body/font-size    typography/heading/line-height
```

**基元**：`blue/50` → `blue/900`、`gray/50` → `gray/900`

**组件名称**：`Button`、`Input`、`Card`、`Avatar`、`Badge`、`Checkbox`、`Toggle`

**变体名称**：`Property=Value, Property=Value` — 例如，`Size=Medium, Style=Primary, State=Default`

**页面分隔符**：`---`（最常见）或`——— COMPONENTS ———`

> 完整命名参考：[naming-conventions.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/naming-conventions.md>)

---

## 8。代币架构

|复杂性|图案|
|-----------|---------|
|< 50 代币|单集，2 模式 (Light/Dark)|
|50–200 代币|**标准**：基元（1模式）+颜色语义（Light/Dark）+间距（1模式）+版式（1模式）|
|200+ 代币|**高级**：多种语义集合，4–8模式（Light/Dark×对比度×品牌）。请参阅 [token-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/token-creation.md>) 中的 M3 模式|

标准模式（推荐起点）：
```
Collection: "Primitives"    modes: ["Value"]
  blue/500 = #3B82F6, gray/900 = #111827, ...

Collection: "Color"         modes: ["Light", "Dark"]
  color/bg/primary → Light: alias Primitives/white, Dark: alias Primitives/gray-900
  color/text/primary → Light: alias Primitives/gray-900, Dark: alias Primitives/white

Collection: "Spacing"       modes: ["Value"]
  spacing/xs = 4, spacing/sm = 8, spacing/md = 16, ...
```

---

## 9。每相反模式

**阶段0反模式：**
- ❌ 在用户锁定范围之前开始创建任何内容
- ❌ 忽略现有的文件约定并强加新的约定
- ❌ 在规划组件创建之前跳过 `search_design_system`

**阶段1反模式：**
- ❌ 在任何变量上使用 `ALL_SCOPES`
- ❌ 在语义层复制原始值而不是别名
- ❌ 不设置代码语法（破坏开发模式和往返）
- ❌ 在就代币分类达成一致之前创建组件代币

**阶段2反模式：**
- ❌ 跳过封面页或基础文档
- ❌ 将多个不相关的组件放在一页上

**阶段3反模式：**
- ❌ 在基础存在之前创建组件
- ❌ 对组件中的任何 fill/stroke/spacing/radius 值进行硬编码
- ❌ 为每个图标创建一个变体（使用 INSTANCE_SWAP 代替）
- ❌ 不在 combineAsVariants 之后定位变体（它们都堆叠在 0,0 处）
- ❌ 构建变体矩阵 > 30 不进行分裂（变体爆炸）
- ❌ 导入远程组件然后立即将其分离

**一般反模式：**
- ❌ 在不首先了解错误的情况下重试失败的脚本
- ❌ 使用名称前缀匹配进行清理（删除用户拥有的节点）
- ❌ 基于上一步未经验证的工作
- ❌ 跳过用户检查点以“节省时间”
- ❌ 并行化 use_figma 调用（始终按顺序）
- ❌ Guessing/hallucinating 内存中的节点 ID（始终从状态分类账中读取）
- ❌ 编写大量内联脚本而不是使用提供的帮助脚本
- ❌ 开始阶段 3 因为用户在没有完成阶段 0-2 的情况下说“构建按钮”

---

## 10。参考文档

按需加载——每个参考文献在其阶段都是权威的：

需要时使用文件阅读工具来阅读这些文档。不要从文件名中假设其内容。

|医生|阶段|必需/可选|加载时|
|-----|-------|---------------------|-----------|
| [discovery-phase.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/discovery-phase.md>) | 0 |**必填**|开始任何构建 - 代码库分析 + Figma 检查|
| [token-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/token-creation.md>) | 1 |**必填**|创建变量、集合、模式、样式|
| [documentation-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/documentation-creation.md>) | 2 |必填|创建封面、基础文档、样本|
| [component-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/component-creation.md>) | 3 |**必填**|创建任何组件或变体|
| [code-connect-setup.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/code-connect-setup.md>) | 3–4 |必填|设置 Code Connect 或可变代码语法|
| [naming-conventions.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/naming-conventions.md>) |任意|可选|命名任何东西——变量、页面、变体、样式|
| [error-recovery.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/error-recovery.md>) |任意|**错误时需要**|脚本失败、多步工作流恢复、废弃工作流状态清理|

---

## 11。脚本

可重复使用的插件 API 辅助函数。嵌入 `use_figma` 调用中：

|脚本|目的|
|--------|---------|
|[inspectFileStructure.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/inspectFileStructure.js>)|发现所有页面、组件、变量、样式；退回全部库存|
|[createVariableCollection.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/createVariableCollection.js>)|创建具有模式的命名集合；返回 `{collectionId, modeIds}`|
|[createSemanticTokens.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/createSemanticTokens.js>)|从标记映射创建别名语义变量|
|[createComponentWithVariants.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/createComponentWithVariants.js>)|从变体矩阵构建组件集；处理网格布局|
|[bindVariablesToComponent.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/bindVariablesToComponent.js>)|将设计令牌绑定到所有组件视觉属性|
|[createDocumentationPage.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/createDocumentationPage.js>)|创建一个包含标题+描述+部分结构的页面|
|[validateCreation.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/validateCreation.js>)|验证创建的节点是否符合预期的计数、名称、结构|
|[cleanupOrphans.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/cleanupOrphans.js>)|通过名称约定或状态账本 ID 删除孤立节点|
|[rehydrateState.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/rehydrateState.js>)|按名称扫描文件中的所有页面、组件、变量；返回完整的 `{key → nodeId}` 地图以进行状态重建|


## 原文对照

<details>
<summary>展开完整原文</summary>

# Design System Builder — Figma MCP Skill

Build professional-grade design systems in Figma that match code. This skill orchestrates multi-phase workflows across 20–100+ `use_figma` calls, enforcing quality patterns from real-world design systems (Material 3, Polaris, Figma UI3, Simple DS).

**Prerequisites**: The `figma-use` skill MUST also be loaded for every `use_figma` call. It provides Plugin API syntax rules (return pattern, page reset, ID return, font loading, color range). This skill provides design system domain knowledge and workflow orchestration.

**Always pass `skillNames: "figma-generate-library"` when calling `use_figma` as part of this skill.** This is a logging parameter — it does not affect execution.

---

## 1. The One Rule That Matters Most

**This is NEVER a one-shot task.** Building a design system requires 20–100+ `use_figma` calls across multiple phases, with mandatory user checkpoints between them. Any attempt to create everything in one call WILL produce broken, incomplete, or unrecoverable results. Break every operation to the smallest useful unit, validate, get feedback, proceed.

---

## 2. Mandatory Workflow

Every design system build follows this phase order. Skipping or reordering phases causes structural failures that are expensive to undo.

```
Phase 0: DISCOVERY (always first — no use_figma writes yet)
  0a. Analyze codebase → extract tokens, components, naming conventions
  0b. Inspect Figma file → pages, variables, components, styles, existing conventions
  0c. Search subscribed libraries → use search_design_system for reusable assets
  0d. Lock v1 scope → agree on exact token set + component list before any creation
  0e. Map code → Figma → resolve conflicts (code and Figma disagree = ask user)
  ✋ USER CHECKPOINT: present full plan, await explicit approval

Phase 1: FOUNDATIONS (tokens first — always before components)
  1a. Create variable collections and modes
  1b. Create primitive variables (raw values, 1 mode)
  1c. Create semantic variables (aliased to primitives, mode-aware)
  1d. Set scopes on ALL variables
  1e. Set code syntax on ALL variables
  1f. Create effect styles (shadows) and text styles (typography)
  → Exit criteria: every token from the agreed plan exists, all scopes set, all code syntax set
  ✋ USER CHECKPOINT: show variable summary, await approval

Phase 2: FILE STRUCTURE (before components)
  2a. Create page skeleton: Cover → Getting Started → Foundations → --- → Components → --- → Utilities
  2b. Create foundations documentation pages (color swatches, type specimens, spacing bars)
  → Exit criteria: all planned pages exist, foundations docs are navigable
  ✋ USER CHECKPOINT: show page list + screenshot, await approval

Phase 3: COMPONENTS (one at a time — never batch)
  For EACH component (in dependency order: atoms before molecules):
    3a. Create dedicated page
    3b. Build base component with auto-layout + full variable bindings
    3c. Create all variant combinations (combineAsVariants + grid layout)
    3d. Add component properties (TEXT, BOOLEAN, INSTANCE_SWAP)
    3e. Link properties to child nodes
    3f. Add page documentation (title, description, usage notes)
    3g. Validate: get_metadata (structure) + get_screenshot (visual)
    3h. Optional: lightweight Code Connect mapping while context is fresh
    → Exit criteria: variant count correct, all bindings verified, screenshot looks right
    ✋ USER CHECKPOINT per component: show screenshot, await approval before next component

Phase 4: INTEGRATION + QA (final pass)
  4a. Finalize all Code Connect mappings
  4b. Accessibility audit (contrast, min touch targets, focus visibility)
  4c. Naming audit (no duplicates, no unnamed nodes, consistent casing)
  4d. Unresolved bindings audit (no hardcoded fills/strokes remaining)
  4e. Final review screenshots of every page
  ✋ USER CHECKPOINT: complete sign-off
```

---

## 3. Critical Rules

**Plugin API basics** (from use_figma skill — enforced here too):
- Use `return` to send data back (auto-serialized). Do NOT wrap in IIFE or call closePlugin.
- Return ALL created/mutated node IDs in every return value
- Page context resets each call — always `await figma.setCurrentPageAsync(page)` at start
- `figma.notify()` throws — never use it
- Colors are 0–1 range, not 0–255
- Font MUST be loaded before any text write: `await figma.loadFontAsync({family, style})`

**Design system rules**:
1. **Variables BEFORE components** — components bind to variables. No token = no component.
2. **Inspect before creating** — run read-only `use_figma` to discover existing conventions. Match them.
3. **One page per component** *(default)* — exception: tightly related families (e.g., Input + helpers) may share a page with clear section separation.
4. **Bind visual properties to variables** *(default)* — fills, strokes, padding, radius, gap. Exceptions: intentionally fixed geometry (icon pixel-grid sizes, static dividers).
5. **Scopes on every variable** — NEVER leave as `ALL_SCOPES`. Background: `FRAME_FILL, SHAPE_FILL`. Text: `TEXT_FILL`. Border: `STROKE_COLOR`. Spacing: `GAP`. Radii: `CORNER_RADIUS`. Primitives: `[]` (hidden).
6. **Code syntax on every variable** — WEB syntax MUST use the `var()` wrapper: `var(--color-bg-primary)`, not `--color-bg-primary`. Use the actual CSS variable name from the codebase. ANDROID/iOS do NOT use a wrapper.
7. **Alias semantics to primitives** — `{ type: 'VARIABLE_ALIAS', id: primitiveVar.id }`. Never duplicate raw values in semantic layer.
8. **Position variants after combineAsVariants** — they stack at (0,0). Manually grid-layout + resize.
9. **INSTANCE_SWAP for icons** — never create a variant per icon. Cap variant matrices: if Size × Style × State > 30 combinations, split into sub-component.
10. **Deterministic naming** — use consistent, unique node names for idempotent cleanup and resumability. Track created node IDs via return values and the state ledger.
11. **No destructive cleanup** — cleanup scripts identify nodes by name convention or returned IDs, not by guessing.
12. **Validate before proceeding** — never build on unvalidated work. `get_metadata` after every create, `get_screenshot` after each component.
13. **NEVER parallelize `use_figma` calls** — Figma state mutations must be strictly sequential. Even if your tool supports parallel calls, never run two use_figma calls simultaneously.
14. **Never hallucinate Node IDs** — always read IDs from the state ledger returned by previous calls. Never reconstruct or guess an ID from memory.
15. **Use the helper scripts** — embed scripts from `scripts/` into your use_figma calls. Don't write 200-line inline scripts from scratch.
16. **Explicit phase approval** — at each checkpoint, name the next phase explicitly. "looks good" is not approval to proceed to Phase 3 if you asked about Phase 1.

---

## 4. State Management (Required for Long Workflows)

> **`getPluginData()` / `setPluginData()` are NOT supported in `use_figma`.** Use `getSharedPluginData()` / `setSharedPluginData()` instead (these ARE supported), or use name-based lookups and the state ledger (returned IDs).

| Entity type | Idempotency key | How to check existence |
|-------------|----------------|----------------------|
| Scene nodes (pages, frames, components) | `setSharedPluginData('dsb', 'key', value)` or unique name | `node.getSharedPluginData('dsb', 'key')` or `page.findOne(n => n.name === 'Button')` |
| Variables | Name within collection | `(await figma.variables.getLocalVariablesAsync()).find(v => v.name === name && v.variableCollectionId === collId)` |
| Styles | Name | `getLocalTextStyles().find(s => s.name === name)` |

Tag every created **scene node** immediately after creation:
```javascript
node.setSharedPluginData('dsb', 'run_id', RUN_ID);        // identifies this build run
node.setSharedPluginData('dsb', 'phase', 'phase3');        // which phase created it
node.setSharedPluginData('dsb', 'key', 'component/button');// unique logical key
```

**State persistence**: Do NOT rely solely on conversation context for the state ledger. Write it to disk:
```
/tmp/dsb-state-{RUN_ID}.json
```
Re-read this file at the start of every turn. In long workflows, conversation context will be truncated — the file is the source of truth.

Maintain a state ledger tracking:
```json
{
  "runId": "ds-build-2024-001",
  "phase": "phase3",
  "step": "component-button",
  "entities": {
    "collections": { "primitives": "id:...", "color": "id:..." },
    "variables": { "color/bg/primary": "id:...", "spacing/sm": "id:..." },
    "pages": { "Cover": "id:...", "Button": "id:..." },
    "components": { "Button": "id:..." }
  },
  "pendingValidations": ["Button:screenshot"],
  "completedSteps": ["phase0", "phase1", "phase2", "component-avatar"]
}
```

**Idempotency check** before every create: query by name + state ledger ID. If exists, skip or update — never duplicate.

**Resume protocol**: at session start or after context truncation, run a read-only `use_figma` to scan all pages, components, variables, and styles by name to reconstruct the `{key → id}` map. Then re-read the state file from disk if available.

**Continuation prompt** (give this to the user when resuming in a new chat):
> "I'm continuing a design system build. Run ID: {RUN_ID}. Load the figma-generate-library skill and resume from the last completed step."

---

## 5. search_design_system — Reuse Decision Matrix

Search FIRST in Phase 0, then again immediately before each component creation.

```
search_design_system({ query, fileKey, includeComponents: true, includeVariables: true, includeStyles: true })
```

**Reuse if** all of these are true:
- Component property API matches your needs (same variant axes, compatible types)
- Token binding model is compatible (uses same or aliasable variables)
- Naming conventions match the target file
- Component is editable (not locked in a remote library you don't own)

**Rebuild if** any of these:
- API incompatibility (different property names, wrong variant model)
- Token model incompatible (hardcoded values, different variable schema)
- Ownership issue (can't modify the library)

**Wrap if** visual match but API incompatible:
- Import the library component as a nested instance inside a new wrapper component
- Expose a clean API on the wrapper

**Three-way priority**: local existing → subscribed library import → create new.

---

## 6. User Checkpoints

Mandatory. Design decisions require human judgment.

| After | Required artifacts | Ask |
|-------|-------------------|-----|
| Discovery + scope lock | Token list, component list, gap analysis | "Here's my plan. Approve before I create anything?" |
| Foundations | Variable summary (N collections, M vars, K modes), style list | "All tokens created. Review before file structure?" |
| File structure | Page list + screenshot | "Pages set up. Review before components?" |
| Each component | get_screenshot of component page | "Here's [Component] with N variants. Correct?" |
| Each conflict (code ≠ Figma) | Show both versions | "Code says X, Figma has Y. Which wins?" |
| Final QA | Per-page screenshots + audit report | "Complete. Sign off?" |

**If user rejects**: fix before moving on. Never build on rejected work.

---

## 7. Naming Conventions

Match existing file conventions. If starting fresh:

**Variables** (slash-separated):
```
color/bg/primary     color/text/secondary    color/border/default
spacing/xs  spacing/sm  spacing/md  spacing/lg  spacing/xl  spacing/2xl
radius/none  radius/sm  radius/md  radius/lg  radius/full
typography/body/font-size    typography/heading/line-height
```

**Primitives**: `blue/50` → `blue/900`, `gray/50` → `gray/900`

**Component names**: `Button`, `Input`, `Card`, `Avatar`, `Badge`, `Checkbox`, `Toggle`

**Variant names**: `Property=Value, Property=Value` — e.g., `Size=Medium, Style=Primary, State=Default`

**Page separators**: `---` (most common) or `——— COMPONENTS ———`

> Full naming reference: [naming-conventions.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/naming-conventions.md>)

---

## 8. Token Architecture

| Complexity | Pattern |
|-----------|---------|
| < 50 tokens | Single collection, 2 modes (Light/Dark) |
| 50–200 tokens | **Standard**: Primitives (1 mode) + Color semantic (Light/Dark) + Spacing (1 mode) + Typography (1 mode) |
| 200+ tokens | **Advanced**: Multiple semantic collections, 4–8 modes (Light/Dark × Contrast × Brand). See M3 pattern in [token-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/token-creation.md>) |

Standard pattern (recommended starting point):
```
Collection: "Primitives"    modes: ["Value"]
  blue/500 = #3B82F6, gray/900 = #111827, ...

Collection: "Color"         modes: ["Light", "Dark"]
  color/bg/primary → Light: alias Primitives/white, Dark: alias Primitives/gray-900
  color/text/primary → Light: alias Primitives/gray-900, Dark: alias Primitives/white

Collection: "Spacing"       modes: ["Value"]
  spacing/xs = 4, spacing/sm = 8, spacing/md = 16, ...
```

---

## 9. Per-Phase Anti-Patterns

**Phase 0 anti-patterns:**
- ❌ Starting to create anything before scope is locked with user
- ❌ Ignoring existing file conventions and imposing new ones
- ❌ Skipping `search_design_system` before planning component creation

**Phase 1 anti-patterns:**
- ❌ Using `ALL_SCOPES` on any variable
- ❌ Duplicating raw values in semantic layer instead of aliasing
- ❌ Not setting code syntax (breaks Dev Mode and round-tripping)
- ❌ Creating component tokens before agreeing on token taxonomy

**Phase 2 anti-patterns:**
- ❌ Skipping the cover page or foundations docs
- ❌ Putting multiple unrelated components on one page

**Phase 3 anti-patterns:**
- ❌ Creating components before foundations exist
- ❌ Hardcoding any fill/stroke/spacing/radius value in a component
- ❌ Creating a variant per icon (use INSTANCE_SWAP instead)
- ❌ Not positioning variants after combineAsVariants (they all stack at 0,0)
- ❌ Building variant matrix > 30 without splitting (variant explosion)
- ❌ Importing remote components then immediately detaching them

**General anti-patterns:**
- ❌ Retrying a failed script without understanding the error first
- ❌ Using name-prefix matching for cleanup (deletes user-owned nodes)
- ❌ Building on unvalidated work from the previous step
- ❌ Skipping user checkpoints to "save time"
- ❌ Parallelizing use_figma calls (always sequential)
- ❌ Guessing/hallucinating node IDs from memory (always read from state ledger)
- ❌ Writing massive inline scripts instead of using the provided helper scripts
- ❌ Starting Phase 3 because the user said "build the button" without completing Phases 0-2

---

## 10. Reference Docs

Load on demand — each reference is authoritative for its phase:

Use your file reading tool to read these docs when needed. Do not assume their contents from the filename.

| Doc | Phase | Required / Optional | Load when |
|-----|-------|---------------------|-----------|
| [discovery-phase.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/discovery-phase.md>) | 0 | **Required** | Starting any build — codebase analysis + Figma inspection |
| [token-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/token-creation.md>) | 1 | **Required** | Creating variables, collections, modes, styles |
| [documentation-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/documentation-creation.md>) | 2 | Required | Creating cover page, foundations docs, swatches |
| [component-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/component-creation.md>) | 3 | **Required** | Creating any component or variant |
| [code-connect-setup.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/code-connect-setup.md>) | 3–4 | Required | Setting up Code Connect or variable code syntax |
| [naming-conventions.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/naming-conventions.md>) | Any | Optional | Naming anything — variables, pages, variants, styles |
| [error-recovery.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/references/error-recovery.md>) | Any | **Required on error** | Script fails, multi-step workflow recovery, cleanup of abandoned workflow state |

---

## 11. Scripts

Reusable Plugin API helper functions. Embed in `use_figma` calls:

| Script | Purpose |
|--------|---------|
| [inspectFileStructure.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/inspectFileStructure.js>) | Discover all pages, components, variables, styles; returns full inventory |
| [createVariableCollection.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/createVariableCollection.js>) | Create a named collection with modes; returns `{collectionId, modeIds}` |
| [createSemanticTokens.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/createSemanticTokens.js>) | Create aliased semantic variables from a token map |
| [createComponentWithVariants.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/createComponentWithVariants.js>) | Build a component set from a variant matrix; handles grid layout |
| [bindVariablesToComponent.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/bindVariablesToComponent.js>) | Bind design tokens to all component visual properties |
| [createDocumentationPage.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/createDocumentationPage.js>) | Create a page with title + description + section structure |
| [validateCreation.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/validateCreation.js>) | Verify created nodes match expected counts, names, structure |
| [cleanupOrphans.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/cleanupOrphans.js>) | Remove orphaned nodes by name convention or state ledger IDs |
| [rehydrateState.js](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-generate-library/scripts/rehydrateState.js>) | Scan file for all pages, components, variables by name; returns full `{key → nodeId}` map for state reconstruction |


</details>
