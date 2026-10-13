# 把应用界面生成到 Figma 的技能

先理解页面与设计系统，再建立框架、复用组件、填充区段并核对完整界面，适用于代码与设计稿同步。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/openai/skills/commit/0e7823cca07bc2cbf34718a383f9ae92525be6a5>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/figma-generate-design/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/skills/curated/figma-generate-design/SKILL.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: figma-generate-design
description: "Use this skill alongside figma-use when the task involves translating an application page, view, or multi-section layout into Figma. Triggers: 'write to Figma', 'create in Figma from code', 'push page to Figma', 'take this app/page and build it in Figma', 'create a screen', 'build a landing page in Figma', 'update the Figma screen to match code'. This is the preferred workflow skill whenever the user wants to build or update a full page, screen, or view in Figma from code or a description. Discovers design system components, variables, and styles via search_design_system, imports them, and assembles screens incrementally section-by-section using design system tokens instead of hardcoded values."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 从设计系统构建/更新屏幕

通过**重用已发布的设计系统**（组件、变量和样式），使用此技能在 Figma 中创建或更新全页屏幕，而不是使用硬编码值绘制基元。关键见解：Figma 文件可能有一个已发布的设计系统，其中包含与代码库的 UI 组件和标记相对应的组件、color/spacing 变量和 text/effect 样式。查找并使用它们，而不是用十六进制颜色绘制方框。

**MANDATORY**：您MUST还在任何`use_figma`调用之前加载[figma-use](<操作 Figma 画布的技能.md>)。该技能包含适用于您编写的每个脚本的关键规则（颜色范围、字体加载等）。

**作为此技能的一部分，调用 `use_figma` 时始终传递 `skillNames: "figma-generate-design"`。** 这是一个日志记录参数 — 它不会影响执行。

## 技能界限

- 当交付物是由设计系统组件实例组成的 **Figma 屏幕 **（新的或更新的）时，请使用此技能。
- 如果用户想要从Figma设计**生成**代码，请切换到[figma-implement-design](<按 Figma 设计实现界面的技能.md>)。
- 如果用户想要创建**新的可重用组件或变体**，请直接使用[figma-use](<操作 Figma 画布的技能.md>)。
- 如果用户想要编写 **Code Connect 映射 **，请切换到 [figma-code-connect-components](<Figma 组件与代码映射技能.md>)。

## 先决条件

- Figma MCP 服务器必须已连接
- 目标 Figma 文件必须具有带有组件的已发布设计系统（或访问团队库）
- 用户应提供：
  - Figma 文件 URL / 使用的文件密钥
  - 或者关于要定位的文件的上下文（agent 可以发现页面）
- 源代码或屏幕说明到build/update

## 使用 generate_figma_design 的并行工作流程（仅限 Web 应用程序）

当从**网络应用程序**构建可以在浏览器中渲染的屏幕时，最好的结果来自并行运行这两种方法：

1. **并联：**
   - 使用此技能的工作流程开始构建屏幕（use_figma + 设计系统组件）
   - 运行 `generate_figma_design` 以捕获正在运行的 Web 应用程序的像素完美屏幕截图
2. **两者完成后：**更新use_figma输出以匹配`generate_figma_design`捕获的像素完美布局。捕获提供了精确的间距、尺寸和视觉处理，而您的 use_figma 输出具有链接到设计系统的正确组件实例。
3. **一旦确认看起来不错：**删除`generate_figma_design`输出 - 它仅用作视觉参考。

这结合了两者的优点：`generate_figma_design` 提供像素完美的布局精度，而 use_figma 提供保持链接和可更新的正确设计系统组件实例。

**此工作流程仅适用于网络应用程序**，其中`generate_figma_design`可以捕获正在运行的页面。对于非 Web 应用程序（iOS、Android 等）或更新现有屏幕时，请使用下面的标准工作流程。

## 所需的工作流程

**按顺序执行以下步骤。请勿跳过步骤。**

### 步骤1：了解屏幕

在接触 Figma 之前，请先了解您正在构建的内容：

1. 如果从代码构建，请阅读相关源文件以了解页面结构、部分以及使用的组件。
2. 识别屏幕的主要部分（例如，页眉、英雄、内容面板、定价网格、FAQ 手风琴、页脚）。
3. 对于每个部分，列出所涉及的 UI 组件（按钮、输入、卡片、导航丸、手风琴等）。

### 步骤 2：发现设计系统 - 组件、变量和样式

您需要设计系统中的三样东西：**组件**（按钮、卡片等）、**变量**（颜色、间距、半径）和**样式**（文本样式、效果样式，例如阴影）。当设计系统令牌存在时，不要对十六进制颜色或像素值进行硬编码。

#### 2a：发现组件

**首选：首先检查现有屏幕。**如果目标文件已包含使用相同设计系统的屏幕，请跳过`search_design_system`并直接检查现有实例。遍历现有框架实例的单个 `use_figma` 调用将为您提供准确、权威的组件映射：

```js
const frame = figma.currentPage.findOne(n => n.name === "Existing Screen");
const uniqueSets = new Map();
frame.findAll(n => n.type === "INSTANCE").forEach(inst => {
  const mc = inst.mainComponent;
  const cs = mc?.parent?.type === "COMPONENT_SET" ? mc.parent : null;
  const key = cs ? cs.key : mc?.key;
  const name = cs ? cs.name : mc?.name;
  if (key && !uniqueSets.has(key)) {
    uniqueSets.set(key, { name, key, isSet: !!cs, sampleVariant: mc.name });
  }
});
return [...uniqueSets.values()];
```

仅当文件没有可供参考的现有屏幕时才回退到 `search_design_system`。使用它时，**广泛搜索** - 尝试多个术语和同义词（例如“按钮”、“输入”、“导航”、“卡片”、“手风琴”、“页眉”、“页脚”、“标签”、“头像”、“切换”、“图标”等）。使用 `includeComponents: true` 重点关注组件。

**在地图中包含组件属性** - 您需要知道每个组件为文本覆盖公开哪些TEXT属性。创建一个临时实例，读取其 `componentProperties`（以及嵌套实例的那些），然后删除临时实例。

带有属性信息的示例组件图：

```
Component Map:
- Button → key: "abc123", type: COMPONENT_SET
  Properties: { "Label#2:0": TEXT, "Has Icon#4:64": BOOLEAN }
- PricingCard → key: "ghi789", type: COMPONENT_SET
  Properties: { "Device": VARIANT, "Variant": VARIANT }
  Nested "Text Heading" has: { "Text#2104:5": TEXT }
  Nested "Button" has: { "Label#2:0": TEXT }
```

#### 2b：发现变量（颜色、间距、半径）

**首先检查现有屏幕**（与组件相同）。或者将 `search_design_system` 与 `includeVariables: true` 一起使用。

> **WARNING：两种不同的变量发现方法——不要混淆它们。**
>
> - `use_figma` 和 `figma.variables.getLocalVariableCollectionsAsync()` — 仅返回 ** 当前文件 ** 中定义的局部变量。如果返回空，则**而不是**意味着不存在变量。 Remote/published 库变量对此 API 不可见。
> - `search_design_system` 和 `includeVariables: true` — 搜索 ** 所有链接库 **，包括远程库和已发布库。这是发现设计系统变量的正确工具。
>
> **永远不要仅根据`getLocalVariableCollectionsAsync()`返回空来得出“不存在变量”的结论。**在决定创建自己的库变量之前，始终运行`search_design_system`和`includeVariables: true`来检查库变量。

**查询策略：** `search_design_system`与**变量名称**匹配（例如，“Gray/gray-9”，“core/gray/100”， “space/400”），而不是类别。并行运行多个简短的简单查询，而不是一个复合查询：

- **原色：**“灰色”、“红色”、“蓝色”、“绿色”、“白色”、“品牌”
- **语义颜色：**“背景”、“前景”、“边框”、“表面”、“文本”
- **Spacing/sizing：**“空间”，“半径”，“间隙”，“填充”

如果初始搜索返回空，请尝试较短的片段或不同的命名约定 - 库差异很大（“灰色”与“灰色”、“间距”与“空间”、“color/bg”与“背景”）。

检查现有屏幕的绑定变量以获得最权威的结果：

```js
const frame = figma.currentPage.findOne(n => n.name === "Existing Screen");
const varMap = new Map();
frame.findAll(() => true).forEach(node => {
  const bv = node.boundVariables;
  if (!bv) return;
  for (const [prop, binding] of Object.entries(bv)) {
    const bindings = Array.isArray(binding) ? binding : [binding];
    for (const b of bindings) {
      if (b?.id && !varMap.has(b.id)) {
        const v = await figma.variables.getVariableByIdAsync(b.id);
        if (v) varMap.set(b.id, { name: v.name, id: v.id, key: v.key, type: v.resolvedType, remote: v.remote });
      }
    }
  }
});
return [...varMap.values()];
```

对于库变量（remote = true），使用 `figma.variables.importVariableByKeyAsync(key)` 键导入它们。对于局部变量，直接使用`figma.variables.getVariableByIdAsync(id)`。

有关绑定模式，请参阅 [variable-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/variable-patterns.md>)。

#### 2c：发现样式（文本样式、效果样式）

使用 `search_design_system` 和 `includeStyles: true` 以及“标题”、“正文”、“阴影”、“标高”等术语搜索样式。或者检查现有屏幕使用的内容：

```js
const frame = figma.currentPage.findOne(n => n.name === "Existing Screen");
const styles = { text: new Map(), effect: new Map() };
frame.findAll(() => true).forEach(node => {
  if ('textStyleId' in node && node.textStyleId) {
    const s = figma.getStyleById(node.textStyleId);
    if (s) styles.text.set(s.id, { name: s.name, id: s.id, key: s.key });
  }
  if ('effectStyleId' in node && node.effectStyleId) {
    const s = figma.getStyleById(node.effectStyleId);
    if (s) styles.effect.set(s.id, { name: s.name, id: s.id, key: s.key });
  }
});
return {
  textStyles: [...styles.text.values()],
  effectStyles: [...styles.effect.values()]
};
```

使用 `figma.importStyleByKeyAsync(key)` 导入库样式，然后使用 `node.textStyleId = style.id` 或 `node.effectStyleId = style.id` 应用。

详情请参见[text-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/text-style-patterns.md>)和[effect-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/effect-style-patterns.md>)。

### 步骤 3：首先创建页面包装框架

**执行NOT将节构建为顶级页面子级，并稍后重新设置它们的父级** - 使用`appendChild()`跨`use_figma`调用移动节点会静默失败并产生孤立帧。相反，首先创建包装器，然后直接在其中构建每个部分。

在其自己的 `use_figma` 调用中创建页面包装器。将其放置在远离现有内容的位置并返回其 ID：

```js
// Find clear space
let maxX = 0;
for (const child of figma.currentPage.children) {
  maxX = Math.max(maxX, child.x + child.width);
}

const wrapper = figma.createFrame();
wrapper.name = "Homepage";
wrapper.layoutMode = "VERTICAL";
wrapper.primaryAxisAlignItems = "CENTER";
wrapper.counterAxisAlignItems = "CENTER";
wrapper.resize(1440, 100);
wrapper.layoutSizingHorizontal = "FIXED";
wrapper.layoutSizingVertical = "HUG";
wrapper.x = maxX + 200;
wrapper.y = 0;

return { success: true, wrapperId: wrapper.id };
```

### 步骤 4：构建包装器内的每个部分

**这是最重要的一步。** 一次构建一个部分，每个部分都在其自己的 `use_figma` 调用中。在每个脚本开始时，通过 ID 获取包装器并将新内容直接附加到其中。

```js
const createdNodeIds = [];
const wrapper = await figma.getNodeByIdAsync("WRAPPER_ID_FROM_STEP_3");

// Import design system components by key
const buttonSet = await figma.importComponentSetByKeyAsync("BUTTON_SET_KEY");
const primaryButton = buttonSet.children.find(c =>
  c.type === "COMPONENT" && c.name.includes("variant=primary")
) || buttonSet.defaultVariant;

// Import design system variables for colors and spacing
const bgColorVar = await figma.variables.importVariableByKeyAsync("BG_COLOR_VAR_KEY");
const spacingVar = await figma.variables.importVariableByKeyAsync("SPACING_VAR_KEY");

// Build section frame with variable bindings (not hardcoded values)
const section = figma.createFrame();
section.name = "Header";
section.layoutMode = "HORIZONTAL";
section.setBoundVariable("paddingLeft", spacingVar);
section.setBoundVariable("paddingRight", spacingVar);
const bgPaint = figma.variables.setBoundVariableForPaint(
  { type: 'SOLID', color: { r: 0, g: 0, b: 0 } }, 'color', bgColorVar
);
section.fills = [bgPaint];

// Import and apply text/effect styles
const shadowStyle = await figma.importStyleByKeyAsync("SHADOW_STYLE_KEY");
section.effectStyleId = shadowStyle.id;

// Create component instances inside the section
const btnInstance = primaryButton.createInstance();
section.appendChild(btnInstance);
createdNodeIds.push(btnInstance.id);

// Append section to wrapper
wrapper.appendChild(section);
section.layoutSizingHorizontal = "FILL"; // AFTER appending

createdNodeIds.push(section.id);
return { success: true, createdNodeIds };
```

在每个部分之后，请先使用 `get_screenshot` 进行验证，然后再继续。仔细查看 cropped/clipped 文本（截断内容的行高）和重叠元素 - 这些是最常见的问题，很容易一眼忽略。

#### 使用 setProperties() 覆盖实例文本

组件实例附带占位符文本（“标题”、“标题”、“按钮”）。使用您在步骤 2 中发现的组件属性键，用 `setProperties()` 覆盖它们 - 这比直接 `node.characters` 操作更可靠。完整模式请参见 [component-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/component-patterns.md#overriding-text-in-a-component-instance>)。

对于公开其自己的 TEXT 属性的嵌套实例，请在嵌套实例上调用 `setProperties()`：

```js
const nestedHeading = cardInstance.findOne(n => n.type === "INSTANCE" && n.name === "Text Heading");
if (nestedHeading) {
  nestedHeading.setProperties({ "Text#2104:5": "Actual heading from source code" });
}
```

对于由任何组件属性管理的 NOT 文本，仅回退到直接 `node.characters`。

#### 仔细阅读源代码默认值

将代码组件转换为 Figma 实例时，请检查源代码中组件的默认 prop 值，而不仅仅是显式传递的值。例如，`<Button size="small">Register</Button>` 没有变体属性 - 检查组件定义以找到 `variant = "primary"` 作为默认值。选择错误的变体（例如，中性而不是主要）会产生视觉上不正确的结果，很容易被忽略。

#### 手动构建什么与从设计系统导入

|手动构建|从设计系统导入|
|----------------|--------------------------|
|页面包装框架|**组件**：按钮、卡、输入、导航等。|
|型材集装箱框架|**变量**：颜色（填充、描边）、间距（填充、间隙）、半径|
|布局网格（行、列）|**文字样式**：标题、正文、说明文字等。|
| |**效果样式**：阴影、模糊等。|

**当设计系统变量存在时，切勿硬编码十六进制颜色或像素间距**。使用 `setBoundVariable` 作为 spacing/radii，使用 `setBoundVariableForPaint` 作为颜色。使用 `node.textStyleId` 应用文本样式，使用 `node.effectStyleId` 应用效果样式。

### 步骤5：验证全屏

编写完所有部分后，在整个页面框架上调用 `get_screenshot` 并与源进行比较。修复目标 `use_figma` 调用的任何问题 - 不要重建整个屏幕。

**对各个部分进行屏幕截图，而不仅仅是整个页面。** 降低分辨率的全页屏幕截图隐藏了文本截断、错误的颜色和尚未覆盖的占位符文本。按节点 ID 截取每个部分的屏幕截图以捕获：
- **Cropped/clipped 文本 ** — 行高或框架大小，截断下降部分、上升部分或整行
- **重叠内容** - 由于尺寸不正确或缺少自动布局，元素彼此堆叠
- 占位符文本仍然显示（“标题”、“标题”、“按钮”）
- 由于布局大小错误而截断的内容
- 错误的组件变体（例如，中性按钮与主按钮）

### 步骤 6：更新现有屏幕

当更新而不是从头开始创建时：

1. 使用`get_metadata`检查现有的屏幕结构。
2. 确定哪些部分需要更新，哪些部分可以保留。
3. 对于需要更改的每个部分：
   - 通过ID或名称找到现有节点
   - 如果设计系统组件发生更改，则交换组件实例
   - 根据需要更新文本内容、变体属性或布局
   - 删除已弃用的部分
   - 添加新部分
4. 每次修改后用`get_screenshot`进行验证。

```js
// Example: Swap a button variant in an existing screen
const existingButton = await figma.getNodeByIdAsync("EXISTING_BUTTON_INSTANCE_ID");
if (existingButton && existingButton.type === "INSTANCE") {
  // Import the updated component
  const buttonSet = await figma.importComponentSetByKeyAsync("BUTTON_SET_KEY");
  const newVariant = buttonSet.children.find(c =>
    c.name.includes("variant=primary") && c.name.includes("size=lg")
  ) || buttonSet.defaultVariant;
  existingButton.swapComponent(newVariant);
}
return { success: true, mutatedNodeIds: [existingButton.id] };
```

## 参考文档

有关详细的 API 模式和陷阱，请根据需要从 [figma-use](<操作 Figma 画布的技能.md>) 参考文献中加载：

- [component-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/component-patterns.md>) — 按键导入、查找变体、setProperties、文本覆盖、使用实例
- [variable-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/variable-patterns.md>) — creating/binding 变量、导入库变量、作用域、别名、发现现有变量
- [text-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/text-style-patterns.md>) — creating/applying 文本样式、导入库文本样式、类型渐变
- [effect-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/effect-style-patterns.md>) — creating/applying 效果样式（阴影），导入库效果样式
- [gotchas.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/gotchas.md>) — 布局陷阱（HUG/FILL 交互、counterAxisAlignItems、尺寸顺序）、paint/color 问题、页面上下文重置

## 错误恢复

按照 [figma-use](<操作 Figma 画布的技能.md#6错误恢复和自我纠正>) 中的错误恢复过程进行操作：

1. **STOP** 出错 — 不要立即重试。
2. **仔细阅读错误消息**以了解出了什么问题。
3. 如果错误不清楚，请调用`get_metadata`或`get_screenshot`检查当前文件状态。
4. **根据错误信息修复脚本**。
5. **重试**更正的脚本 - 这是安全的，因为失败的脚本是原子的（如果脚本错误，则不会创建任何内容）。

由于此技能是增量工作的（每次调用一个部分），因此错误自然会限制在单个部分。成功调用之前的部分保持不变。

## 最佳实践

- **在构建之前始终进行搜索。**设计系统可能具有您需要的组件、变量或样式。手动构造和硬编码值应该是例外，而不是规则。
- **广泛搜索。** 尝试同义词和部分术语。 “NavigationPill”可能会在“pill”、“nav”、“tab”或“chip”下找到。对于变量，搜索“颜色”、“间距”、“半径”等。
- ** 优先选择设计系统标记而不是硬编码值。** 对颜色、间距和半径使用变量绑定。使用文本样式进行排版。使用阴影效果样式。这使屏幕与设计系统保持链接。
- **优先选择组件实例而不是手动构建。**实例与源组件保持链接，并在设计系统发展时自动更新。
- **逐节工作。**每次`use_figma`调用切勿构建多个主要部分。
- **从每次调用返回节点ID。**您将需要它们来组成部分并进行错误恢复。
- **在每个部分之后进行视觉验证。** 使用 `get_screenshot` 及早发现问题。
- **匹配现有约定。**如果文件已有屏幕，请匹配其命名、大小和布局模式。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Build / Update Screens from Design System

Use this skill to create or update full-page screens in Figma by **reusing the published design system** — components, variables, and styles — rather than drawing primitives with hardcoded values. The key insight: the Figma file likely has a published design system with components, color/spacing variables, and text/effect styles that correspond to the codebase's UI components and tokens. Find and use those instead of drawing boxes with hex colors.

**MANDATORY**: You MUST also load [figma-use](<操作 Figma 画布的技能.md>) before any `use_figma` call. That skill contains critical rules (color ranges, font loading, etc.) that apply to every script you write.

**Always pass `skillNames: "figma-generate-design"` when calling `use_figma` as part of this skill.** This is a logging parameter — it does not affect execution.

## Skill Boundaries

- Use this skill when the deliverable is a **Figma screen** (new or updated) composed of design system component instances.
- If the user wants to generate **code from a Figma design**, switch to [figma-implement-design](<按 Figma 设计实现界面的技能.md>).
- If the user wants to create **new reusable components or variants**, use [figma-use](<操作 Figma 画布的技能.md>) directly.
- If the user wants to write **Code Connect mappings**, switch to [figma-code-connect-components](<Figma 组件与代码映射技能.md>).

## Prerequisites

- Figma MCP server must be connected
- The target Figma file must have a published design system with components (or access to a team library)
- User should provide either:
  - A Figma file URL / file key to work in
  - Or context about which file to target (the agent can discover pages)
- Source code or description of the screen to build/update

## Parallel Workflow with generate_figma_design (Web Apps Only)

When building a screen from a **web app** that can be rendered in a browser, the best results come from running both approaches in parallel:

1. **In parallel:**
   - Start building the screen using this skill's workflow (use_figma + design system components)
   - Run `generate_figma_design` to capture a pixel-perfect screenshot of the running web app
2. **Once both complete:** Update the use_figma output to match the pixel-perfect layout from the `generate_figma_design` capture. The capture provides the exact spacing, sizing, and visual treatment to aim for, while your use_figma output has proper component instances linked to the design system.
3. **Once confirmed looking good:** Delete the `generate_figma_design` output — it was only used as a visual reference.

This combines the best of both: `generate_figma_design` gives pixel-perfect layout accuracy, while use_figma gives proper design system component instances that stay linked and updatable.

**This workflow only applies to web apps** where `generate_figma_design` can capture the running page. For non-web apps (iOS, Android, etc.) or when updating existing screens, use the standard workflow below.

## Required Workflow

**Follow these steps in order. Do not skip steps.**

### Step 1: Understand the Screen

Before touching Figma, understand what you're building:

1. If building from code, read the relevant source files to understand the page structure, sections, and which components are used.
2. Identify the major sections of the screen (e.g., Header, Hero, Content Panels, Pricing Grid, FAQ Accordion, Footer).
3. For each section, list the UI components involved (buttons, inputs, cards, navigation pills, accordions, etc.).

### Step 2: Discover Design System — Components, Variables, and Styles

You need three things from the design system: **components** (buttons, cards, etc.), **variables** (colors, spacing, radii), and **styles** (text styles, effect styles like shadows). Don't hardcode hex colors or pixel values when design system tokens exist.

#### 2a: Discover components

**Preferred: inspect existing screens first.** If the target file already contains screens using the same design system, skip `search_design_system` and inspect existing instances directly. A single `use_figma` call that walks an existing frame's instances gives you an exact, authoritative component map:

```js
const frame = figma.currentPage.findOne(n => n.name === "Existing Screen");
const uniqueSets = new Map();
frame.findAll(n => n.type === "INSTANCE").forEach(inst => {
  const mc = inst.mainComponent;
  const cs = mc?.parent?.type === "COMPONENT_SET" ? mc.parent : null;
  const key = cs ? cs.key : mc?.key;
  const name = cs ? cs.name : mc?.name;
  if (key && !uniqueSets.has(key)) {
    uniqueSets.set(key, { name, key, isSet: !!cs, sampleVariant: mc.name });
  }
});
return [...uniqueSets.values()];
```

Only fall back to `search_design_system` when the file has no existing screens to reference. When using it, **search broadly** — try multiple terms and synonyms (e.g., "button", "input", "nav", "card", "accordion", "header", "footer", "tag", "avatar", "toggle", "icon", etc.). Use `includeComponents: true` to focus on components.

**Include component properties** in your map — you need to know which TEXT properties each component exposes for text overrides. Create a temporary instance, read its `componentProperties` (and those of nested instances), then remove the temp instance.

Example component map with property info:

```
Component Map:
- Button → key: "abc123", type: COMPONENT_SET
  Properties: { "Label#2:0": TEXT, "Has Icon#4:64": BOOLEAN }
- PricingCard → key: "ghi789", type: COMPONENT_SET
  Properties: { "Device": VARIANT, "Variant": VARIANT }
  Nested "Text Heading" has: { "Text#2104:5": TEXT }
  Nested "Button" has: { "Label#2:0": TEXT }
```

#### 2b: Discover variables (colors, spacing, radii)

**Inspect existing screens first** (same as components). Or use `search_design_system` with `includeVariables: true`.

> **WARNING: Two different variable discovery methods — do not confuse them.**
>
> - `use_figma` with `figma.variables.getLocalVariableCollectionsAsync()` — returns **only local variables defined in the current file**. If this returns empty, it does **not** mean no variables exist. Remote/published library variables are invisible to this API.
> - `search_design_system` with `includeVariables: true` — searches across **all linked libraries**, including remote and published ones. This is the correct tool for discovering design system variables.
>
> **Never conclude "no variables exist" based solely on `getLocalVariableCollectionsAsync()` returning empty.** Always also run `search_design_system` with `includeVariables: true` to check for library variables before deciding to create your own.

**Query strategy:** `search_design_system` matches against **variable names** (e.g., "Gray/gray-9", "core/gray/100", "space/400"), not categories. Run multiple short, simple queries in parallel rather than one compound query:

- **Primitive colors:** "gray", "red", "blue", "green", "white", "brand"
- **Semantic colors:** "background", "foreground", "border", "surface", "text"
- **Spacing/sizing:** "space", "radius", "gap", "padding"

If initial searches return empty, try shorter fragments or different naming conventions — libraries vary widely ("grey" vs "gray", "spacing" vs "space", "color/bg" vs "background").

Inspect an existing screen's bound variables for the most authoritative results:

```js
const frame = figma.currentPage.findOne(n => n.name === "Existing Screen");
const varMap = new Map();
frame.findAll(() => true).forEach(node => {
  const bv = node.boundVariables;
  if (!bv) return;
  for (const [prop, binding] of Object.entries(bv)) {
    const bindings = Array.isArray(binding) ? binding : [binding];
    for (const b of bindings) {
      if (b?.id && !varMap.has(b.id)) {
        const v = await figma.variables.getVariableByIdAsync(b.id);
        if (v) varMap.set(b.id, { name: v.name, id: v.id, key: v.key, type: v.resolvedType, remote: v.remote });
      }
    }
  }
});
return [...varMap.values()];
```

For library variables (remote = true), import them by key with `figma.variables.importVariableByKeyAsync(key)`. For local variables, use `figma.variables.getVariableByIdAsync(id)` directly.

See [variable-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/variable-patterns.md>) for binding patterns.

#### 2c: Discover styles (text styles, effect styles)

Search for styles using `search_design_system` with `includeStyles: true` and terms like "heading", "body", "shadow", "elevation". Or inspect what an existing screen uses:

```js
const frame = figma.currentPage.findOne(n => n.name === "Existing Screen");
const styles = { text: new Map(), effect: new Map() };
frame.findAll(() => true).forEach(node => {
  if ('textStyleId' in node && node.textStyleId) {
    const s = figma.getStyleById(node.textStyleId);
    if (s) styles.text.set(s.id, { name: s.name, id: s.id, key: s.key });
  }
  if ('effectStyleId' in node && node.effectStyleId) {
    const s = figma.getStyleById(node.effectStyleId);
    if (s) styles.effect.set(s.id, { name: s.name, id: s.id, key: s.key });
  }
});
return {
  textStyles: [...styles.text.values()],
  effectStyles: [...styles.effect.values()]
};
```

Import library styles with `figma.importStyleByKeyAsync(key)`, then apply with `node.textStyleId = style.id` or `node.effectStyleId = style.id`.

See [text-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/text-style-patterns.md>) and [effect-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/effect-style-patterns.md>) for details.

### Step 3: Create the Page Wrapper Frame First

**Do NOT build sections as top-level page children and reparent them later** — moving nodes across `use_figma` calls with `appendChild()` silently fails and produces orphaned frames. Instead, create the wrapper first, then build each section directly inside it.

Create the page wrapper in its own `use_figma` call. Position it away from existing content and return its ID:

```js
// Find clear space
let maxX = 0;
for (const child of figma.currentPage.children) {
  maxX = Math.max(maxX, child.x + child.width);
}

const wrapper = figma.createFrame();
wrapper.name = "Homepage";
wrapper.layoutMode = "VERTICAL";
wrapper.primaryAxisAlignItems = "CENTER";
wrapper.counterAxisAlignItems = "CENTER";
wrapper.resize(1440, 100);
wrapper.layoutSizingHorizontal = "FIXED";
wrapper.layoutSizingVertical = "HUG";
wrapper.x = maxX + 200;
wrapper.y = 0;

return { success: true, wrapperId: wrapper.id };
```

### Step 4: Build Each Section Inside the Wrapper

**This is the most important step.** Build one section at a time, each in its own `use_figma` call. At the start of each script, fetch the wrapper by ID and append new content directly to it.

```js
const createdNodeIds = [];
const wrapper = await figma.getNodeByIdAsync("WRAPPER_ID_FROM_STEP_3");

// Import design system components by key
const buttonSet = await figma.importComponentSetByKeyAsync("BUTTON_SET_KEY");
const primaryButton = buttonSet.children.find(c =>
  c.type === "COMPONENT" && c.name.includes("variant=primary")
) || buttonSet.defaultVariant;

// Import design system variables for colors and spacing
const bgColorVar = await figma.variables.importVariableByKeyAsync("BG_COLOR_VAR_KEY");
const spacingVar = await figma.variables.importVariableByKeyAsync("SPACING_VAR_KEY");

// Build section frame with variable bindings (not hardcoded values)
const section = figma.createFrame();
section.name = "Header";
section.layoutMode = "HORIZONTAL";
section.setBoundVariable("paddingLeft", spacingVar);
section.setBoundVariable("paddingRight", spacingVar);
const bgPaint = figma.variables.setBoundVariableForPaint(
  { type: 'SOLID', color: { r: 0, g: 0, b: 0 } }, 'color', bgColorVar
);
section.fills = [bgPaint];

// Import and apply text/effect styles
const shadowStyle = await figma.importStyleByKeyAsync("SHADOW_STYLE_KEY");
section.effectStyleId = shadowStyle.id;

// Create component instances inside the section
const btnInstance = primaryButton.createInstance();
section.appendChild(btnInstance);
createdNodeIds.push(btnInstance.id);

// Append section to wrapper
wrapper.appendChild(section);
section.layoutSizingHorizontal = "FILL"; // AFTER appending

createdNodeIds.push(section.id);
return { success: true, createdNodeIds };
```

After each section, validate with `get_screenshot` before moving on. Look closely for cropped/clipped text (line heights cutting off content) and overlapping elements — these are the most common issues and easy to miss at a glance.

#### Override instance text with setProperties()

Component instances ship with placeholder text ("Title", "Heading", "Button"). Use the component property keys you discovered in Step 2 to override them with `setProperties()` — this is more reliable than direct `node.characters` manipulation. See [component-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/component-patterns.md#overriding-text-in-a-component-instance>) for the full pattern.

For nested instances that expose their own TEXT properties, call `setProperties()` on the nested instance:

```js
const nestedHeading = cardInstance.findOne(n => n.type === "INSTANCE" && n.name === "Text Heading");
if (nestedHeading) {
  nestedHeading.setProperties({ "Text#2104:5": "Actual heading from source code" });
}
```

Only fall back to direct `node.characters` for text that is NOT managed by any component property.

#### Read source code defaults carefully

When translating code components to Figma instances, check the component's default prop values in the source code, not just what's explicitly passed. For example, `<Button size="small">Register</Button>` with no variant prop — check the component definition to find `variant = "primary"` as the default. Selecting the wrong variant (e.g., Neutral instead of Primary) produces a visually incorrect result that's easy to miss.

#### What to build manually vs. import from design system

| Build manually | Import from design system |
|----------------|--------------------------|
| Page wrapper frame | **Components**: buttons, cards, inputs, nav, etc. |
| Section container frames | **Variables**: colors (fills, strokes), spacing (padding, gap), radii |
| Layout grids (rows, columns) | **Text styles**: heading, body, caption, etc. |
| | **Effect styles**: shadows, blurs, etc. |

**Never hardcode hex colors or pixel spacing** when a design system variable exists. Use `setBoundVariable` for spacing/radii and `setBoundVariableForPaint` for colors. Apply text styles with `node.textStyleId` and effect styles with `node.effectStyleId`.

### Step 5: Validate the Full Screen

After composing all sections, call `get_screenshot` on the full page frame and compare against the source. Fix any issues with targeted `use_figma` calls — don't rebuild the entire screen.

**Screenshot individual sections, not just the full page.** A full-page screenshot at reduced resolution hides text truncation, wrong colors, and placeholder text that hasn't been overridden. Take a screenshot of each section by node ID to catch:
- **Cropped/clipped text** — line heights or frame sizing cutting off descenders, ascenders, or entire lines
- **Overlapping content** — elements stacking on top of each other due to incorrect sizing or missing auto-layout
- Placeholder text still showing ("Title", "Heading", "Button")
- Truncated content from layout sizing bugs
- Wrong component variants (e.g., Neutral vs Primary button)

### Step 6: Updating an Existing Screen

When updating rather than creating from scratch:

1. Use `get_metadata` to inspect the existing screen structure.
2. Identify which sections need updating and which can stay.
3. For each section that needs changes:
   - Locate the existing nodes by ID or name
   - Swap component instances if the design system component changed
   - Update text content, variant properties, or layout as needed
   - Remove deprecated sections
   - Add new sections
4. Validate with `get_screenshot` after each modification.

```js
// Example: Swap a button variant in an existing screen
const existingButton = await figma.getNodeByIdAsync("EXISTING_BUTTON_INSTANCE_ID");
if (existingButton && existingButton.type === "INSTANCE") {
  // Import the updated component
  const buttonSet = await figma.importComponentSetByKeyAsync("BUTTON_SET_KEY");
  const newVariant = buttonSet.children.find(c =>
    c.name.includes("variant=primary") && c.name.includes("size=lg")
  ) || buttonSet.defaultVariant;
  existingButton.swapComponent(newVariant);
}
return { success: true, mutatedNodeIds: [existingButton.id] };
```

## Reference Docs

For detailed API patterns and gotchas, load these from the [figma-use](<操作 Figma 画布的技能.md>) references as needed:

- [component-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/component-patterns.md>) — importing by key, finding variants, setProperties, text overrides, working with instances
- [variable-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/variable-patterns.md>) — creating/binding variables, importing library variables, scopes, aliasing, discovering existing variables
- [text-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/text-style-patterns.md>) — creating/applying text styles, importing library text styles, type ramps
- [effect-style-patterns.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/effect-style-patterns.md>) — creating/applying effect styles (shadows), importing library effect styles
- [gotchas.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/figma-use/references/gotchas.md>) — layout pitfalls (HUG/FILL interactions, counterAxisAlignItems, sizing order), paint/color issues, page context resets

## Error Recovery

Follow the error recovery process from [figma-use](<操作 Figma 画布的技能.md#6-error-recovery--self-correction>):

1. **STOP** on error — do not retry immediately.
2. **Read the error message carefully** to understand what went wrong.
3. If the error is unclear, call `get_metadata` or `get_screenshot` to inspect the current file state.
4. **Fix the script** based on the error message.
5. **Retry** the corrected script — this is safe because failed scripts are atomic (nothing is created if a script errors).

Because this skill works incrementally (one section per call), errors are naturally scoped to a single section. Previous sections from successful calls remain intact.

## Best Practices

- **Always search before building.** The design system likely has the component, variable, or style you need. Manual construction and hardcoded values should be the exception, not the rule.
- **Search broadly.** Try synonyms and partial terms. A "NavigationPill" might be found under "pill", "nav", "tab", or "chip". For variables, search "color", "spacing", "radius", etc.
- **Prefer design system tokens over hardcoded values.** Use variable bindings for colors, spacing, and radii. Use text styles for typography. Use effect styles for shadows. This keeps the screen linked to the design system.
- **Prefer component instances over manual builds.** Instances stay linked to the source component and update automatically when the design system evolves.
- **Work section by section.** Never build more than one major section per `use_figma` call.
- **Return node IDs from every call.** You'll need them to compose sections and for error recovery.
- **Validate visually after each section.** Use `get_screenshot` to catch issues early.
- **Match existing conventions.** If the file already has screens, match their naming, sizing, and layout patterns.


</details>
