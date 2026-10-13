# 按 Figma 设计实现界面的技能

讲解节点定位、上下文与截图获取、资源下载、按项目规范实现及视觉对照，帮助检查设计还原。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/openai/skills/commit/0e7823cca07bc2cbf34718a383f9ae92525be6a5>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/figma-implement-design/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/skills/curated/figma-implement-design/SKILL.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: figma-implement-design
description: Translates Figma designs into production-ready application code with 1:1 visual fidelity. Use when implementing UI code from Figma files, when user mentions "implement design", "generate code", "implement component", provides Figma URLs, or asks to build components matching Figma specs. For Figma canvas writes via `use_figma`, use `figma-use`.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 实施设计

## 概述

该技能提供了一个结构化的工作流程，用于将 Figma 设计转换为具有像素完美精度的生产就绪代码。它确保与 Figma MCP 服务器的一致集成、设计令牌的正确使用以及 1:1 与设计的视觉对等。

## 技能界限

- 当可交付成果是用户存储库中的代码时，请使用此技能。
- 如果用户要求 Figma 本身内部的 create/edit/delete 节点，请切换到 [figma-use](<操作 Figma 画布的技能.md>)。
- 如果用户要求通过代码或描述在 Figma 中构建或更新全页屏幕，请切换到 [figma-generate-design](<把应用界面生成到 Figma 的技能.md>)。
- 如果用户仅要求 Code Connect 映射，请切换到 [figma-code-connect-components](<Figma 组件与代码映射技能.md>)。
- 如果用户要求编写可重用的 agent 规则 (`CLAUDE.md`/`AGENTS.md`)，请切换到 [figma-create-design-system-rules](<生成 Figma 设计系统规则的技能.md>)。

## 先决条件

- Figma MCP 服务器必须已连接且可访问
- 用户必须提供 Figma URL，格式为：`https://figma.com/design/:fileKey/:fileName?node-id=1-2`
  - `:fileKey` 是文件密钥
  - `1-2`是节点ID（要实现的具体组件或框架）
- **OR** 使用 `figma-desktop` MCP 时：用户可以直接在 Figma 桌面应用程序中选择节点（不需要 URL）
- 项目应有已建立的设计系统或组件库（首选）

## 所需的工作流程

**按顺序执行以下步骤。请勿跳过步骤。**

### 步骤1：获取节点ID

#### 选项A：从Figma解析URL

当用户提供 Figma URL 时，提取文件密钥和节点 ID 以作为参数传递给 MCP 工具。

**URL 格式：** `https://figma.com/design/:fileKey/:fileName?node-id=1-2`

**摘录：**

- **文件密钥：** `:fileKey`（`/design/`之后的段）
- **节点ID:** `1-2`（`node-id`查询参数的值）

**注意：**使用本地桌面MCP（`figma-desktop`）时，`fileKey`不会作为参数传递给工具调用。服务器自动使用当前打开的文件，因此只需要`nodeId`。

**示例：**

- URL: `https://figma.com/design/kL9xQn2VwM8pYrTb4ZcHjF/DesignSystem?node-id=42-15`
- 文件密钥：`kL9xQn2VwM8pYrTb4ZcHjF`
- 节点ID：`42-15`

#### 选项 B：使用 Figma 桌面应用程序中的当前选择（仅限 figma-desktop MCP）

当使用 `figma-desktop` MCP 并且用户 NOT 提供了 URL 时，工具会自动使用桌面应用程序中打开的 Figma 文件中当前选择的节点。

**注意：** 基于选择的提示仅适用于 `figma-desktop` MCP 服务器。远程服务器需要一个到框架或层的链接来提取上下文。用户必须打开 Figma 桌面应用程序并选择一个节点。

### 步骤 2：获取设计上下文

使用提取的文件密钥和节点 ID 运行 `get_design_context`。

```
get_design_context(fileKey=":fileKey", nodeId="1-2")
```

这提供了结构化数据，包括：

- 布局属性（自动布局、约束、大小调整）
- 排版规范
- 颜色值和设计标记
- 组件结构和变体
- 间距和填充值

**如果响应太大或被截断：**

1. 运行`get_metadata(fileKey=":fileKey", nodeId="1-2")`获取高层节点图
2. 从元数据中识别所需的特定子节点
3. 使用 `get_design_context(fileKey=":fileKey", nodeId=":childNodeId")` 获取各个子节点

### 步骤 3：捕获视觉参考

使用相同的文件密钥和节点 ID 运行 `get_screenshot` 以获取视觉参考。

```
get_screenshot(fileKey=":fileKey", nodeId="1-2")
```

此屏幕截图可作为视觉验证的事实来源。在整个实施过程中保持其可访问性。

### 步骤4：下载所需资源

下载 Figma MCP 服务器返回的任何资源（图像、图标、SVG）。

**IMPORTANT:** 遵循以下资产规则：

- 如果 Figma MCP 服务器返回图像的 `localhost` 源或 SVG，请直接使用该源
- DO NOT 导入或添加新的图标包 - 所有资源均应来自 Figma 有效负载
- DO NOT 如果提供了 `localhost` 源，则使用或创建占位符
- 资产通过 Figma MCP 服务器的内置资产端点提供服务

### 步骤 5：转换为项目约定

将 Figma 输出转换为该项目的框架、样式和约定。

**关键原理：**

- 将 Figma MCP 输出（通常是 React + Tailwind）视为设计和行为的表示，而不是最终的代码样式
- 将 Tailwind 实用程序类替换为项目的首选实用程序或设计系统令牌
- 重用现有组件（按钮、输入、排版、图标包装）而不是重复功能
- 一致地使用项目的颜色系统、版式比例和间距标记
- 尊重现有的路由、状态管理和数据获取模式

### 步骤 6：实现 1：1 视觉奇偶校验

力求与 Figma 设计实现像素完美的视觉效果。

**指南：**

- 优先考虑 Figma 保真度以精确匹配设计
- 避免硬编码值 - 使用 Figma 中可用的设计标记
- 当设计系统令牌和 Figma 规范之间出现冲突时，优先选择设计系统令牌，但尽量调整间距或大小以匹配视觉效果
- 遵循 WCAG 的可访问性要求
- 根据需要添加组件文档

### 步骤 7：针对 Figma 进行验证

在标记完成之前，根据 Figma 屏幕截图验证最终的 UI。

**验证清单：**

- [ ] 布局匹配（间距、对齐方式、大小调整）
- [ ] 版式匹配（字体、大小、粗细、行高）
- [ ] 颜色完全匹配
- [ ] 交互状态按设计工作（悬停、活动、禁用）
- [ ] 响应行为遵循 Figma 约束
- [ ] 资源正确渲染
- [ ] 符合辅助功能标准

## 实施细则

### 组件组织

- 将UI组件放置在项目指定的设计系统目录中
- 遵循项目的组件命名约定
- 除非动态值确实需要，否则避免内联样式

### 设计系统集成

- ALWAYS 尽可能使用项目设计系统中的组件
- 将 Figma 设计代币映射到项目设计代币
- 当存在匹配的组件时，扩展它而不是创建一个新组件
- 记录添加到设计系统中的任何新组件

### 代码质量

- 避免硬编码值 - 提取为常量或设计标记
- 保持组件的可组合性和可重用性
- 为组件道具添加 TypeScript 类型
- 包含导出组件的 JSDoc 注释

## 示例

### 示例1：实现按钮组件

用户说：“实现这个 Figma 按钮组件：https://figma.com/design/kL9xQn2VwM8pYrTb4ZcHjF/DesignSystem?node-id=42-15”

**操作：**

1. 解析 URL 提取 fileKey=`kL9xQn2VwM8pYrTb4ZcHjF` 和 nodeId=`42-15`
2. 运行`get_design_context(fileKey="kL9xQn2VwM8pYrTb4ZcHjF", nodeId="42-15")`
3. 运行 `get_screenshot(fileKey="kL9xQn2VwM8pYrTb4ZcHjF", nodeId="42-15")` 进行视觉参考
4. 从资产端点下载任何按钮图标
5. 检查项目是否已有按钮组件
6. 如果是，则用新的变体扩展它；如果否，则使用项目约定创建新组件
7. 将 Figma 颜色映射到项目设计标记（例如 `primary-500`、`primary-hover`）
8. 根据屏幕截图验证填充、边框半径、版式

**结果：** 按钮组件匹配Figma设计，与项目设计系统集成。

### 示例 2：构建仪表板布局

用户说：“构建此仪表板：https://figma.com/design/pR8mNv5KqXzGwY2JtCfL4D/Dashboard?node-id=10-5”

**操作：**

1. 解析 URL 提取 fileKey=`pR8mNv5KqXzGwY2JtCfL4D` 和 nodeId=`10-5`
2. 运行`get_metadata(fileKey="pR8mNv5KqXzGwY2JtCfL4D", nodeId="10-5")`了解页面结构
3. 识别元数据的主要部分（标题、侧边栏、内容区域、卡片）及其子节点 ID
4. 为每个主要部分运行 `get_design_context(fileKey="pR8mNv5KqXzGwY2JtCfL4D", nodeId=":childNodeId")`
5. 运行 `get_screenshot(fileKey="pR8mNv5KqXzGwY2JtCfL4D", nodeId="10-5")` 获取整个页面
6. 下载所有资源（徽标、图标、图表）
7. 使用项目的布局基元构建布局
8. 尽可能使用现有组件实现每个部分
9. 根据 Figma 约束验证响应行为

**结果：** 完整的仪表板匹配 Figma 设计和响应式布局。

## 最佳实践

### 始终从上下文开始

切勿基于假设来实施。始终先获取 `get_design_context` 和 `get_screenshot`。

### 增量验证

在实施过程中经常验证，而不仅仅是在最后。这可以及早发现问题。

### 文件偏差

如果您必须偏离 Figma 设计（例如，出于可访问性或技术限制），请在代码注释中记录原因。

### 重用胜过娱乐

在创建新组件之前，请务必检查现有组件。代码库的一致性比 Figma 的精确复制更重要。

### 设计系统优先

如有疑问，请选择项目的设计系统模式，而不是 Figma 的字面翻译。

## 常见问题及解决方案

### 问题：Figma 输出被截断

**原因：**设计过于复杂或嵌套层数过多，无法在单个响应中返回。
**解决方案：**使用`get_metadata`获取节点结构，然后使用`get_design_context`单独获取特定节点。

### 问题：设计与实施后不匹配

**原因：**实现的代码与原始Figma设计之间存在视觉差异。
**解决方案：**与步骤3中的屏幕截图并排比较。检查设计上下文数据中的间距、颜色和版式值。

### 问题：资源未加载

**原因：** Figma MCP 服务器的资产端点不可访问或 URL 正在修改。
**解决方案：** 验证 Figma MCP 服务器的资产端点是否可访问。服务器在 `localhost` URL 处提供资产。直接使用这些，无需修改。

### 问题：设计代币值与 Figma 不同

**原因：**项目的设计系统令牌与Figma设计中指定的值不同。
**解决方案：**当项目令牌与Figma值不同时，优先使用项目令牌以保持一致性，但调整spacing/sizing以保持视觉保真度。

## 了解设计实施

Figma 实施工作流程建立了将设计转化为代码的可靠流程：

**对于设计人员：** 相信实现能够以像素完美的精度匹配他们的设计。
**对于开发人员：**一种结构化方法，可以消除猜测并减少来回修改。
**对于团队：** 一致、高质量的实施，保持设计系统的完整性。

通过遵循此工作流程，您可以确保每个 Figma 设计都以同样程度的关注和对细节的关注来实现。

## 其他资源

- [Figma MCP 服务器文档](<https://developers.figma.com/docs/figma-mcp-server/>)
- [Figma MCP 服务器工具和提示](<https://developers.figma.com/docs/figma-mcp-server/tools-and-prompts/>)
- [Figma 变量和设计令牌](<https://help.figma.com/hc/en-us/articles/15339657135383-Guide-to-variables-in-Figma>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Implement Design

## Overview

This skill provides a structured workflow for translating Figma designs into production-ready code with pixel-perfect accuracy. It ensures consistent integration with the Figma MCP server, proper use of design tokens, and 1:1 visual parity with designs.

## Skill Boundaries

- Use this skill when the deliverable is code in the user's repository.
- If the user asks to create/edit/delete nodes inside Figma itself, switch to [figma-use](<操作 Figma 画布的技能.md>).
- If the user asks to build or update a full-page screen in Figma from code or a description, switch to [figma-generate-design](<把应用界面生成到 Figma 的技能.md>).
- If the user asks only for Code Connect mappings, switch to [figma-code-connect-components](<Figma 组件与代码映射技能.md>).
- If the user asks to author reusable agent rules (`CLAUDE.md`/`AGENTS.md`), switch to [figma-create-design-system-rules](<生成 Figma 设计系统规则的技能.md>).

## Prerequisites

- Figma MCP server must be connected and accessible
- User must provide a Figma URL in the format: `https://figma.com/design/:fileKey/:fileName?node-id=1-2`
  - `:fileKey` is the file key
  - `1-2` is the node ID (the specific component or frame to implement)
- **OR** when using `figma-desktop` MCP: User can select a node directly in the Figma desktop app (no URL required)
- Project should have an established design system or component library (preferred)

## Required Workflow

**Follow these steps in order. Do not skip steps.**

### Step 1: Get Node ID

#### Option A: Parse from Figma URL

When the user provides a Figma URL, extract the file key and node ID to pass as arguments to MCP tools.

**URL format:** `https://figma.com/design/:fileKey/:fileName?node-id=1-2`

**Extract:**

- **File key:** `:fileKey` (the segment after `/design/`)
- **Node ID:** `1-2` (the value of the `node-id` query parameter)

**Note:** When using the local desktop MCP (`figma-desktop`), `fileKey` is not passed as a parameter to tool calls. The server automatically uses the currently open file, so only `nodeId` is needed.

**Example:**

- URL: `https://figma.com/design/kL9xQn2VwM8pYrTb4ZcHjF/DesignSystem?node-id=42-15`
- File key: `kL9xQn2VwM8pYrTb4ZcHjF`
- Node ID: `42-15`

#### Option B: Use Current Selection from Figma Desktop App (figma-desktop MCP only)

When using the `figma-desktop` MCP and the user has NOT provided a URL, the tools automatically use the currently selected node from the open Figma file in the desktop app.

**Note:** Selection-based prompting only works with the `figma-desktop` MCP server. The remote server requires a link to a frame or layer to extract context. The user must have the Figma desktop app open with a node selected.

### Step 2: Fetch Design Context

Run `get_design_context` with the extracted file key and node ID.

```
get_design_context(fileKey=":fileKey", nodeId="1-2")
```

This provides the structured data including:

- Layout properties (Auto Layout, constraints, sizing)
- Typography specifications
- Color values and design tokens
- Component structure and variants
- Spacing and padding values

**If the response is too large or truncated:**

1. Run `get_metadata(fileKey=":fileKey", nodeId="1-2")` to get the high-level node map
2. Identify the specific child nodes needed from the metadata
3. Fetch individual child nodes with `get_design_context(fileKey=":fileKey", nodeId=":childNodeId")`

### Step 3: Capture Visual Reference

Run `get_screenshot` with the same file key and node ID for a visual reference.

```
get_screenshot(fileKey=":fileKey", nodeId="1-2")
```

This screenshot serves as the source of truth for visual validation. Keep it accessible throughout implementation.

### Step 4: Download Required Assets

Download any assets (images, icons, SVGs) returned by the Figma MCP server.

**IMPORTANT:** Follow these asset rules:

- If the Figma MCP server returns a `localhost` source for an image or SVG, use that source directly
- DO NOT import or add new icon packages - all assets should come from the Figma payload
- DO NOT use or create placeholders if a `localhost` source is provided
- Assets are served through the Figma MCP server's built-in assets endpoint

### Step 5: Translate to Project Conventions

Translate the Figma output into this project's framework, styles, and conventions.

**Key principles:**

- Treat the Figma MCP output (typically React + Tailwind) as a representation of design and behavior, not as final code style
- Replace Tailwind utility classes with the project's preferred utilities or design system tokens
- Reuse existing components (buttons, inputs, typography, icon wrappers) instead of duplicating functionality
- Use the project's color system, typography scale, and spacing tokens consistently
- Respect existing routing, state management, and data-fetch patterns

### Step 6: Achieve 1:1 Visual Parity

Strive for pixel-perfect visual parity with the Figma design.

**Guidelines:**

- Prioritize Figma fidelity to match designs exactly
- Avoid hardcoded values - use design tokens from Figma where available
- When conflicts arise between design system tokens and Figma specs, prefer design system tokens but adjust spacing or sizes minimally to match visuals
- Follow WCAG requirements for accessibility
- Add component documentation as needed

### Step 7: Validate Against Figma

Before marking complete, validate the final UI against the Figma screenshot.

**Validation checklist:**

- [ ] Layout matches (spacing, alignment, sizing)
- [ ] Typography matches (font, size, weight, line height)
- [ ] Colors match exactly
- [ ] Interactive states work as designed (hover, active, disabled)
- [ ] Responsive behavior follows Figma constraints
- [ ] Assets render correctly
- [ ] Accessibility standards met

## Implementation Rules

### Component Organization

- Place UI components in the project's designated design system directory
- Follow the project's component naming conventions
- Avoid inline styles unless truly necessary for dynamic values

### Design System Integration

- ALWAYS use components from the project's design system when possible
- Map Figma design tokens to project design tokens
- When a matching component exists, extend it rather than creating a new one
- Document any new components added to the design system

### Code Quality

- Avoid hardcoded values - extract to constants or design tokens
- Keep components composable and reusable
- Add TypeScript types for component props
- Include JSDoc comments for exported components

## Examples

### Example 1: Implementing a Button Component

User says: "Implement this Figma button component: https://figma.com/design/kL9xQn2VwM8pYrTb4ZcHjF/DesignSystem?node-id=42-15"

**Actions:**

1. Parse URL to extract fileKey=`kL9xQn2VwM8pYrTb4ZcHjF` and nodeId=`42-15`
2. Run `get_design_context(fileKey="kL9xQn2VwM8pYrTb4ZcHjF", nodeId="42-15")`
3. Run `get_screenshot(fileKey="kL9xQn2VwM8pYrTb4ZcHjF", nodeId="42-15")` for visual reference
4. Download any button icons from the assets endpoint
5. Check if project has existing button component
6. If yes, extend it with new variant; if no, create new component using project conventions
7. Map Figma colors to project design tokens (e.g., `primary-500`, `primary-hover`)
8. Validate against screenshot for padding, border radius, typography

**Result:** Button component matching Figma design, integrated with project design system.

### Example 2: Building a Dashboard Layout

User says: "Build this dashboard: https://figma.com/design/pR8mNv5KqXzGwY2JtCfL4D/Dashboard?node-id=10-5"

**Actions:**

1. Parse URL to extract fileKey=`pR8mNv5KqXzGwY2JtCfL4D` and nodeId=`10-5`
2. Run `get_metadata(fileKey="pR8mNv5KqXzGwY2JtCfL4D", nodeId="10-5")` to understand the page structure
3. Identify main sections from metadata (header, sidebar, content area, cards) and their child node IDs
4. Run `get_design_context(fileKey="pR8mNv5KqXzGwY2JtCfL4D", nodeId=":childNodeId")` for each major section
5. Run `get_screenshot(fileKey="pR8mNv5KqXzGwY2JtCfL4D", nodeId="10-5")` for the full page
6. Download all assets (logos, icons, charts)
7. Build layout using project's layout primitives
8. Implement each section using existing components where possible
9. Validate responsive behavior against Figma constraints

**Result:** Complete dashboard matching Figma design with responsive layout.

## Best Practices

### Always Start with Context

Never implement based on assumptions. Always fetch `get_design_context` and `get_screenshot` first.

### Incremental Validation

Validate frequently during implementation, not just at the end. This catches issues early.

### Document Deviations

If you must deviate from the Figma design (e.g., for accessibility or technical constraints), document why in code comments.

### Reuse Over Recreation

Always check for existing components before creating new ones. Consistency across the codebase is more important than exact Figma replication.

### Design System First

When in doubt, prefer the project's design system patterns over literal Figma translation.

## Common Issues and Solutions

### Issue: Figma output is truncated

**Cause:** The design is too complex or has too many nested layers to return in a single response.
**Solution:** Use `get_metadata` to get the node structure, then fetch specific nodes individually with `get_design_context`.

### Issue: Design doesn't match after implementation

**Cause:** Visual discrepancies between the implemented code and the original Figma design.
**Solution:** Compare side-by-side with the screenshot from Step 3. Check spacing, colors, and typography values in the design context data.

### Issue: Assets not loading

**Cause:** The Figma MCP server's assets endpoint is not accessible or the URLs are being modified.
**Solution:** Verify the Figma MCP server's assets endpoint is accessible. The server serves assets at `localhost` URLs. Use these directly without modification.

### Issue: Design token values differ from Figma

**Cause:** The project's design system tokens have different values than those specified in the Figma design.
**Solution:** When project tokens differ from Figma values, prefer project tokens for consistency but adjust spacing/sizing to maintain visual fidelity.

## Understanding Design Implementation

The Figma implementation workflow establishes a reliable process for translating designs to code:

**For designers:** Confidence that implementations will match their designs with pixel-perfect accuracy.
**For developers:** A structured approach that eliminates guesswork and reduces back-and-forth revisions.
**For teams:** Consistent, high-quality implementations that maintain design system integrity.

By following this workflow, you ensure that every Figma design is implemented with the same level of care and attention to detail.

## Additional Resources

- [Figma MCP Server Documentation](<https://developers.figma.com/docs/figma-mcp-server/>)
- [Figma MCP Server Tools and Prompts](<https://developers.figma.com/docs/figma-mcp-server/tools-and-prompts/>)
- [Figma Variables and Design Tokens](<https://help.figma.com/hc/en-us/articles/15339657135383-Guide-to-variables-in-Figma>)


</details>
