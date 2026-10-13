# Figma 组件与代码映射技能

讲解获取组件建议、搜索项目实现、核对匹配和创建 Code Connect 关系，帮助设计系统与代码组件保持对应。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/openai/skills/commit/0e7823cca07bc2cbf34718a383f9ae92525be6a5>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/figma-code-connect-components/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/skills/curated/figma-code-connect-components/SKILL.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: figma-code-connect-components
description: Connects Figma design components to code components using Code Connect mapping tools. Use when user says "code connect", "connect this component to code", "map this component", "link component to code", "create code connect mapping", or wants to establish mappings between Figma designs and code implementations. For canvas writes via `use_figma`, use `figma-use`.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 代码连接组件

## 概述

此技能可帮助您使用 Figma 的代码连接功能将 Figma 设计组件连接到其相应的代码实现。它分析 Figma 设计结构，搜索代码库以查找匹配组件，并建立保持设计代码一致性的映射。

## 技能界限

- 将此技能用于 `get_code_connect_suggestions` + `send_code_connect_mappings` 工作流程。
- 如果任务需要使用插件 API 脚本写入 Figma 画布，请切换到 [figma-use](<操作 Figma 画布的技能.md>)。
- 如果任务是根据代码或描述在 Figma 中构建或更新全页屏幕，请切换到 [figma-generate-design](<把应用界面生成到 Figma 的技能.md>)。
- 如果任务是实现 Figma 的产品代码，请切换到 [figma-implement-design](<按 Figma 设计实现界面的技能.md>)。

## 先决条件

- Figma MCP 服务器必须已连接且可访问
- 用户必须提供带有节点 ID 的Figma URL：`https://figma.com/design/:fileKey/:fileName?node-id=1-2`
  - **IMPORTANT:** Figma URL 必须包含 `node-id` 参数。如果没有它，Code Connect 映射将会失败。
- **OR** 使用 `figma-desktop` MCP 时：用户可以直接在 Figma 桌面应用程序中选择节点（不需要 URL）
- **IMPORTANT:** Figma 组件必须发布到团队库。 Code Connect 仅适用于已发布的组件或组件集。
- **IMPORTANT：** Code Connect 仅适用于组织和企业计划。
- 访问项目代码库以进行组件扫描

## 所需的工作流程

**按顺序执行以下步骤。请勿跳过步骤。**

### 步骤 1：获取代码连接建议

调用 `get_code_connect_suggestions` 以在单个操作中识别所有未映射的组件。该工具自动：

- 从 Figma 场景图中获取组件信息
- 标识选择中已发布的组件
- 检查现有的 Code Connect 映射并过滤掉已连接的组件
- 返回每个未映射组件的组件名称、属性和缩略图

#### 选项 A：使用 `figma-desktop` MCP（未提供 URL）

如果`figma-desktop`MCP服务器已连接并且用户NOT提供了FigmaURL，请立即调用`get_code_connect_suggestions`。不需要 URL 解析 - 桌面 MCP 服务器自动使用打开的 Figma 文件中当前选择的节点。

**注意：** 用户必须打开 Figma 桌面应用程序并选择节点。 `fileKey` 不作为参数传递 — 服务器使用当前打开的文件。

#### 选项 B：提供 Figma URL 时

解析URL，提取`fileKey`和`nodeId`，然后调用`get_code_connect_suggestions`。

**IMPORTANT:** 从 Figma URL 中提取节点 ID 时，转换格式：

- URL 格式使用连字符：`node-id=1-2`
- 工具需要冒号：`nodeId=1:2`

**解析 Figma URL:**

- URL 格式：`https://figma.com/design/:fileKey/:fileName?node-id=1-2`
- 提取文件密钥：`:fileKey`（`/design/`之后的段）
- 从 URL 中提取节点 ID: `1-2`，然后使用工具转换为 `1:2`

```
get_code_connect_suggestions(fileKey=":fileKey", nodeId="1:2")
```

**处理响应：**

- 如果工具返回 **“在此选择中未找到已发布的组件”** → 通知用户并停止。可能需要首先将组件发布到团队库。
- 如果工具返回 **“此选择中的所有组件实例都已通过 Code Connect 连接到代码”** → 通知用户所有内容都已映射。
- 否则，响应包含未映射组件的列表，每个组件具有：
  - 元件名称
  - 节点 ID
  - 组件属性（JSON 以及属性名称和值）
  - 组件的缩略图（用于目视检查）

### 步骤 2：扫描代码库以查找匹配组件

对于 `get_code_connect_suggestions` 返回的每个未映射组件，在代码库中搜索匹配的代码组件。

**要寻找什么：**

- 与 Figma 组件名称匹配或相似的组件名称
- 与 Figma 层次结构一致的组件结构
- 与 Figma 属性相对应的道具（变体、文本、样式）
- 典型组件目录中的文件（`src/components/`、`components/`、`ui/`等）

**搜索策略：**

1. 搜索名称匹配的组件文件
2. 阅读候选文件以检查结构和道具
3. 将代码组件的 props 与步骤 1 中返回的 Figma 组件属性进行比较
4. 检测编程语言（TypeScript、JavaScript）和框架（React、Vue等）
5. 根据结构相似性确定最佳匹配，权衡：
   - 道具名称及其与 Figma 属性的对应关系
   - 与 Figma 默认值匹配的默认值
   - CSS 类或样式对象
   - 澄清意图的描述性评论
6. 如果多个候选者同样优秀，请选择与 prop-interface 最匹配的一个，并在工具调用之前在 1-2 句子注释中记录你的推理

**搜索模式示例：**

- 如果 Figma 组件为“PrimaryButton”，则搜索 `Button.tsx`、`PrimaryButton.tsx`、`Button.jsx`
- 检查常用组件路径：`src/components/`、`app/components/`、`lib/ui/`
- 寻找与 Figma 变体匹配的变体道具，如 `variant`、`size`、`color`

### 步骤 3：向用户呈现匹配

展示您的发现并让用户选择要创建的映射。用户可以接受全部、部分或不接受建议的映射。

**以这种格式呈现匹配：**

```
The following components match the design:
- [ComponentName](path/to/component): DesignComponentName at nodeId [nodeId](figmaUrl?node-id=X-Y)
- [AnotherComponent](path/to/another): AnotherDesign at nodeId [nodeId2](figmaUrl?node-id=X-Y)

Would you like to connect these components? You can accept all, select specific ones, or skip.
```

**如果没有找到精确匹配的组件：**

- 显示 2 最接近的候选者
- 解释差异
- 要求用户确认使用哪个组件或提供正确的路径

**如果用户拒绝所有映射**，请通知他们并停止。不需要进一步的工具调用。

### 步骤 4：创建代码连接映射

用户确认其选择后，仅使用接受的映射调用 `send_code_connect_mappings`。该工具可以在一次调用中批量创建所有映射。

**示例：**

```
send_code_connect_mappings(
  fileKey=":fileKey",
  nodeId="1:2",
  mappings=[
    { nodeId: "1:2", componentName: "Button", source: "src/components/Button.tsx", label: "React" },
    { nodeId: "1:5", componentName: "Card", source: "src/components/Card.tsx", label: "React" }
  ]
)
```

**每个映射的关键参数：**

- `nodeId`：Figma 节点 ID（冒号格式：`1:2`）
- `componentName`：要连接的组件的名称（例如“按钮”、“卡”）
- `source`：代码组件文件的路径（相对于项目根目录）
- `label`：此 Code Connect 映射的框架或语言标签。有效值包括：
  - 网页：“React”、“Web 组件”、“Vue”、“Svelte”、“故事书”、“Javascript”
  - iOS：'Swift UIKit'，'Objective-C UIKit'，'SwiftUI'
  - Android：“撰写”、“Java”、“Kotlin”、“Android XML 布局”
  - 跨平台：“颤动”
  - 文档：“Markdown”

**通话后：**

- 成功时：该工具确认映射已创建
- 出错时：该工具报告哪些特定映射失败以及原因（例如，“组件已映射到代码”、“未找到已发布的组件”、“权限不足”）

**提供处理后的总结**：

```
Code Connect Summary:
- Successfully connected: 3
  - Button (1:2) → src/components/Button.tsx
  - Card (1:5) → src/components/Card.tsx
  - Input (1:8) → src/components/Input.tsx
- Could not connect: 1
  - CustomWidget (1:10) - No matching component found in codebase
```

## 示例

### 示例1：连接按钮组件

用户说：“将此 Figma 按钮连接到我的代码：https://figma.com/design/kL9xQn2VwM8pYrTb4ZcHjF/DesignSystem?node-id=42-15”

**操作：**

1. 解析URL：fileKey=`kL9xQn2VwM8pYrTb4ZcHjF`,nodeId=`42-15`→转换为`42:15`
2. 运行`get_code_connect_suggestions(fileKey="kL9xQn2VwM8pYrTb4ZcHjF", nodeId="42:15")`
3. 响应显示：具有 `variant` (primary/secondary) 和 `size` (sm/md/lg) 属性的按钮组件（未映射），以及缩略图
4. 搜索按钮组件的代码库：查找 `src/components/Button.tsx`
5. 读取`Button.tsx`并确认其具有`variant`和`size`道具
6. 呈现给用户：
   ```
   I found a match:
   - [Button](src/components/Button.tsx): Button at nodeId [42:15](https://figma.com/design/kL9xQn2VwM8pYrTb4ZcHjF/DesignSystem?node-id=42-15)

   Would you like to connect this component?
   ```

7. 用户确认：“是”
8. 检测到它是 TypeScript React 组件
9. 运行`send_code_connect_mappings(fileKey="kL9xQn2VwM8pYrTb4ZcHjF", nodeId="42:15", mappings=[{ nodeId: "42:15", componentName: "Button", source: "src/components/Button.tsx", label: "React" }])`

**结果：** Figma 按钮组件现已连接到代码 Button 组件。

### 示例 2：部分选择的多个组件

用户说：“连接此框架中的组件：https://figma.com/design/pR8mNv5KqXzGwY2JtCfL4D/Components?node-id=10-50”

**操作：**

1. 解析URL：fileKey=`pR8mNv5KqXzGwY2JtCfL4D`,nodeId=`10-50`→转换为`10:50`
2. 运行`get_code_connect_suggestions(fileKey="pR8mNv5KqXzGwY2JtCfL4D", nodeId="10:50")`
3. 响应显示 3 未映射的组件：ProductCard、徽章和 CustomWidget
4. 搜索代码库：
   - ProductCard：找到`src/components/ProductCard.tsx`（道具匹配）
   - 徽章：找到`src/components/Badge.tsx`（道具匹配）
   - CustomWidget：未找到匹配的组件
5. 呈现给用户：
   ```
   The following components match the design:
   - [ProductCard](src/components/ProductCard.tsx): ProductCard at nodeId [10:51](https://figma.com/design/pR8mNv5KqXzGwY2JtCfL4D/Components?node-id=10-51)
   - [Badge](src/components/Badge.tsx): Badge at nodeId [10:52](https://figma.com/design/pR8mNv5KqXzGwY2JtCfL4D/Components?node-id=10-52)

   I couldn't find a match for CustomWidget (10:53).

   Would you like to connect these components? You can accept all, select specific ones, or skip.
   ```

6. 用户：“只需连接ProductCard，暂时跳过徽章”
7. 运行`send_code_connect_mappings(fileKey="pR8mNv5KqXzGwY2JtCfL4D", nodeId="10:50", mappings=[{ nodeId: "10:51", componentName: "ProductCard", source: "src/components/ProductCard.tsx", label: "React" }])`

**结果：** 根据用户的选择，仅连接 ProductCard。

### 示例 3：组件需要创建

用户说：“连接此图标：https://figma.com/design/8yJDMeWDyBz71EnMOSuUiw/Icons?node-id=5-20”

**操作：**

1. 解析URL：fileKey=`8yJDMeWDyBz71EnMOSuUiw`,nodeId=`5-20`→转换为`5:20`
2. 运行`get_code_connect_suggestions(fileKey="8yJDMeWDyBz71EnMOSuUiw", nodeId="5:20")`
3. 响应显示：CheckIcon 组件（未映射）具有颜色和大小属性
4. 搜索代码库 CheckIcon：未找到匹配项
5. 搜索通用图标组件：查找包含其他图标的`src/icons/`目录
6. 向用户报告：“我找不到 CheckIcon 组件，但我在 src/icons/. 找到了一个图标目录，您想：
   - 先新建一个CheckIcon.tsx组件，然后连接
   - 连接到不同的现有图标
   - 如果 CheckIcon 存在于其他地方，请提供该路径”
7. 用户提供路径：“src/icons/CheckIcon.tsx”
8. 从文件中检测语言和框架
9. 运行`send_code_connect_mappings(fileKey="8yJDMeWDyBz71EnMOSuUiw", nodeId="5:20", mappings=[{ nodeId: "5:20", componentName: "CheckIcon", source: "src/icons/CheckIcon.tsx", label: "React" }])`

**结果：** CheckIcon组件已成功连接到 Figma 设计。

## 最佳实践

### 主动组件发现

不要只询问用户文件路径 - 主动搜索他们的代码库以查找匹配的组件。这提供了更好的体验并抓住潜在的绘图机会。

### 精确的结构匹配

将 Figma 组件与代码组件进行比较时，不要只关注名称。检查：

- 道具对齐（变体类型、尺寸选项等）
- 组件层次结构匹配（嵌套元素）
- 该组件具有相同的用途

### 清晰的沟通

当提出创建映射时，请清楚地解释：

- 你发现了什么
- 为什么这是一场很好的比赛
- 映射将做什么
- 道具将如何连接

### 处理歧义

如果多个组件可以匹配，请提供选项而不是猜测。让用户最终决定连接哪个组件。

### 优雅的降级

如果您找不到完全匹配的内容，请提供有用的后续步骤：

- 显示接近的候选人
- 建议组件创建
- 请求用户指导

## 常见问题及解决方案

### 问题：“在此选择中未找到已发布的组件”

**原因：** Figma 组件未发布到团队库。 Code Connect 仅适用于已发布的组件。
**解决方案：** 用户需要将组件发布到 Figma 中的团队库：

1. 在 Figma 中，选择组件或组件集
2. 右键单击并选择“发布到库”或使用团队库发布模式
3. 发布组件
4. 发布后，使用同一节点重试 Code Connect 映射 ID

### 问题：“Code Connect 仅适用于组织和企业计划”

**原因：**用户的 Figma 计划不包括 Code Connect 访问权限。
**解决方案：**用户需要升级到组织或企业计划，或联系其管理员。

### 问题：在代码库中找不到匹配的组件

**原因：**代码库搜索未找到具有匹配名称或结构的组件。
**解决方案：**询问用户该组件是否存在于不同的名称或不同的位置。他们可能需要首先创建组件，或者它可能位于意外的目录中。

### 问题：“未找到已发布的组件”(CODE_CONNECT_ASSET_NOT_FOUND)

**原因：**源文件路径不正确，该位置不存在组件，或者componentName与实际导出不匹配。
**解决方案：**验证源路径是否正确并且相对于项目根目录。检查组件是否已从指定了确切的 componentName 的文件中正确导出。

### 问题：“组件已映射到代码”(CODE_CONNECT_MAPPING_ALREADY_EXISTS)

**原因：**此组件已存在代码连接映射。
**解决方案：**该组件已连接。如果用户想要更新映射，他们可能需要先在 Figma 中删除现有的映射。

### 问题：“没有足够的权限来创建映射”(CODE_CONNECT_INSUFFICIENT_PERMISSIONS)

**原因：**用户对Figma文件或库没有编辑权限。
**解决方案：**用户需要对包含组件的文件进行编辑权限。联系文件所有者或团队管理员。

### 问题：Code Connect 映射失败并出现 URL 错误

**原因：**Figma URL格式不正确或缺少`node-id`参数。
**解决方案：**验证URL是否符合要求的格式： `https://figma.com/design/:fileKey/:fileName?node-id=1-2`。 `node-id` 参数是必需的。还要确保在调用工具时将 `1-2` 转换为 `1:2`。

### 问题：发现多个类似组件

**原因：**代码库包含多个可以与 Figma 组件匹配的组件。
**解决方案：**向用户显示所有候选者及其文件路径，并让他们选择要连接的组件。不同的组件可能用于不同的上下文（例如，`Button.tsx` 与 `LinkButton.tsx`）。

## 了解代码连接

Code Connect 在设计和代码之间建立双向链接：

**对于设计师：**查看哪个代码组件实现了Figma组件
**对于开发人员：**从Figma设计直接导航到实现它们的代码
**对于团队：**维护组件映射的单一事实来源

您创建的映射使这些连接变得明确且可发现，有助于保持设计和代码同步。

## 其他资源

有关代码连接的更多信息：

- [Code Connect 文档](<https://help.figma.com/hc/en-us/articles/23920389749655-Code-Connect>)
- [Figma MCP 服务器工具和提示](<https://developers.figma.com/docs/figma-mcp-server/tools-and-prompts/>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Code Connect Components

## Overview

This skill helps you connect Figma design components to their corresponding code implementations using Figma's Code Connect feature. It analyzes the Figma design structure, searches your codebase for matching components, and establishes mappings that maintain design-code consistency.

## Skill Boundaries

- Use this skill for `get_code_connect_suggestions` + `send_code_connect_mappings` workflows.
- If the task requires writing to the Figma canvas with Plugin API scripts, switch to [figma-use](<操作 Figma 画布的技能.md>).
- If the task is building or updating a full-page screen in Figma from code or a description, switch to [figma-generate-design](<把应用界面生成到 Figma 的技能.md>).
- If the task is implementing product code from Figma, switch to [figma-implement-design](<按 Figma 设计实现界面的技能.md>).

## Prerequisites

- Figma MCP server must be connected and accessible
- User must provide a Figma URL with node ID: `https://figma.com/design/:fileKey/:fileName?node-id=1-2`
  - **IMPORTANT:** The Figma URL must include the `node-id` parameter. Code Connect mapping will fail without it.
- **OR** when using `figma-desktop` MCP: User can select a node directly in the Figma desktop app (no URL required)
- **IMPORTANT:** The Figma component must be published to a team library. Code Connect only works with published components or component sets.
- **IMPORTANT:** Code Connect is only available on Organization and Enterprise plans.
- Access to the project codebase for component scanning

## Required Workflow

**Follow these steps in order. Do not skip steps.**

### Step 1: Get Code Connect Suggestions

Call `get_code_connect_suggestions` to identify all unmapped components in a single operation. This tool automatically:

- Fetches component info from the Figma scenegraph
- Identifies published components in the selection
- Checks existing Code Connect mappings and filters out already-connected components
- Returns component names, properties, and thumbnail images for each unmapped component

#### Option A: Using `figma-desktop` MCP (no URL provided)

If the `figma-desktop` MCP server is connected and the user has NOT provided a Figma URL, immediately call `get_code_connect_suggestions`. No URL parsing is needed — the desktop MCP server automatically uses the currently selected node from the open Figma file.

**Note:** The user must have the Figma desktop app open with a node selected. `fileKey` is not passed as a parameter — the server uses the currently open file.

#### Option B: When a Figma URL is provided

Parse the URL to extract `fileKey` and `nodeId`, then call `get_code_connect_suggestions`.

**IMPORTANT:** When extracting the node ID from a Figma URL, convert the format:

- URL format uses hyphens: `node-id=1-2`
- Tool expects colons: `nodeId=1:2`

**Parse the Figma URL:**

- URL format: `https://figma.com/design/:fileKey/:fileName?node-id=1-2`
- Extract file key: `:fileKey` (segment after `/design/`)
- Extract node ID: `1-2` from URL, then convert to `1:2` for the tool

```
get_code_connect_suggestions(fileKey=":fileKey", nodeId="1:2")
```

**Handle the response:**

- If the tool returns **"No published components found in this selection"** → inform the user and stop. The components may need to be published to a team library first.
- If the tool returns **"All component instances in this selection are already connected to code via Code Connect"** → inform the user that everything is already mapped.
- Otherwise, the response contains a list of unmapped components, each with:
  - Component name
  - Node ID
  - Component properties (JSON with prop names and values)
  - A thumbnail image of the component (for visual inspection)

### Step 2: Scan Codebase for Matching Components

For each unmapped component returned by `get_code_connect_suggestions`, search the codebase for a matching code component.

**What to look for:**

- Component names that match or are similar to the Figma component name
- Component structure that aligns with the Figma hierarchy
- Props that correspond to Figma properties (variants, text, styles)
- Files in typical component directories (`src/components/`, `components/`, `ui/`, etc.)

**Search strategy:**

1. Search for component files with matching names
2. Read candidate files to check structure and props
3. Compare the code component's props with the Figma component properties returned in Step 1
4. Detect the programming language (TypeScript, JavaScript) and framework (React, Vue, etc.)
5. Identify the best match based on structural similarity, weighing:
   - Prop names and their correspondence to Figma properties
   - Default values that match Figma defaults
   - CSS classes or style objects
   - Descriptive comments that clarify intent
6. If multiple candidates are equally good, pick the one with the closest prop-interface match and document your reasoning in a 1-2 sentence comment before your tool call

**Example search patterns:**

- If Figma component is "PrimaryButton", search for `Button.tsx`, `PrimaryButton.tsx`, `Button.jsx`
- Check common component paths: `src/components/`, `app/components/`, `lib/ui/`
- Look for variant props like `variant`, `size`, `color` that match Figma variants

### Step 3: Present Matches to User

Present your findings and let the user choose which mappings to create. The user can accept all, some, or none of the suggested mappings.

**Present matches in this format:**

```
The following components match the design:
- [ComponentName](path/to/component): DesignComponentName at nodeId [nodeId](figmaUrl?node-id=X-Y)
- [AnotherComponent](path/to/another): AnotherDesign at nodeId [nodeId2](figmaUrl?node-id=X-Y)

Would you like to connect these components? You can accept all, select specific ones, or skip.
```

**If no exact match is found for a component:**

- Show the 2 closest candidates
- Explain the differences
- Ask the user to confirm which component to use or provide the correct path

**If the user declines all mappings**, inform them and stop. No further tool calls are needed.

### Step 4: Create Code Connect Mappings

Once the user confirms their selections, call `send_code_connect_mappings` with only the accepted mappings. This tool handles batch creation of all mappings in a single call.

**Example:**

```
send_code_connect_mappings(
  fileKey=":fileKey",
  nodeId="1:2",
  mappings=[
    { nodeId: "1:2", componentName: "Button", source: "src/components/Button.tsx", label: "React" },
    { nodeId: "1:5", componentName: "Card", source: "src/components/Card.tsx", label: "React" }
  ]
)
```

**Key parameters for each mapping:**

- `nodeId`: The Figma node ID (with colon format: `1:2`)
- `componentName`: Name of the component to connect (e.g., "Button", "Card")
- `source`: Path to the code component file (relative to project root)
- `label`: The framework or language label for this Code Connect mapping. Valid values include:
  - Web: 'React', 'Web Components', 'Vue', 'Svelte', 'Storybook', 'Javascript'
  - iOS: 'Swift UIKit', 'Objective-C UIKit', 'SwiftUI'
  - Android: 'Compose', 'Java', 'Kotlin', 'Android XML Layout'
  - Cross-platform: 'Flutter'
  - Docs: 'Markdown'

**After the call:**

- On success: the tool confirms the mappings were created
- On error: the tool reports which specific mappings failed and why (e.g., "Component is already mapped to code", "Published component not found", "Insufficient permissions")

**Provide a summary** after processing:

```
Code Connect Summary:
- Successfully connected: 3
  - Button (1:2) → src/components/Button.tsx
  - Card (1:5) → src/components/Card.tsx
  - Input (1:8) → src/components/Input.tsx
- Could not connect: 1
  - CustomWidget (1:10) - No matching component found in codebase
```

## Examples

### Example 1: Connecting a Button Component

User says: "Connect this Figma button to my code: https://figma.com/design/kL9xQn2VwM8pYrTb4ZcHjF/DesignSystem?node-id=42-15"

**Actions:**

1. Parse URL: fileKey=`kL9xQn2VwM8pYrTb4ZcHjF`, nodeId=`42-15` → convert to `42:15`
2. Run `get_code_connect_suggestions(fileKey="kL9xQn2VwM8pYrTb4ZcHjF", nodeId="42:15")`
3. Response shows: Button component (unmapped) with `variant` (primary/secondary) and `size` (sm/md/lg) properties, plus a thumbnail image
4. Search codebase for Button components: Find `src/components/Button.tsx`
5. Read `Button.tsx` and confirm it has `variant` and `size` props
6. Present to user:
   ```
   I found a match:
   - [Button](src/components/Button.tsx): Button at nodeId [42:15](https://figma.com/design/kL9xQn2VwM8pYrTb4ZcHjF/DesignSystem?node-id=42-15)

   Would you like to connect this component?
   ```

7. User confirms: "Yes"
8. Detect that it's a TypeScript React component
9. Run `send_code_connect_mappings(fileKey="kL9xQn2VwM8pYrTb4ZcHjF", nodeId="42:15", mappings=[{ nodeId: "42:15", componentName: "Button", source: "src/components/Button.tsx", label: "React" }])`

**Result:** Figma button component is now connected to the code Button component.

### Example 2: Multiple Components with Partial Selection

User says: "Connect components in this frame: https://figma.com/design/pR8mNv5KqXzGwY2JtCfL4D/Components?node-id=10-50"

**Actions:**

1. Parse URL: fileKey=`pR8mNv5KqXzGwY2JtCfL4D`, nodeId=`10-50` → convert to `10:50`
2. Run `get_code_connect_suggestions(fileKey="pR8mNv5KqXzGwY2JtCfL4D", nodeId="10:50")`
3. Response shows 3 unmapped components: ProductCard, Badge, and CustomWidget
4. Search codebase:
   - ProductCard: Found `src/components/ProductCard.tsx` (props match)
   - Badge: Found `src/components/Badge.tsx` (props match)
   - CustomWidget: No matching component found
5. Present to user:
   ```
   The following components match the design:
   - [ProductCard](src/components/ProductCard.tsx): ProductCard at nodeId [10:51](https://figma.com/design/pR8mNv5KqXzGwY2JtCfL4D/Components?node-id=10-51)
   - [Badge](src/components/Badge.tsx): Badge at nodeId [10:52](https://figma.com/design/pR8mNv5KqXzGwY2JtCfL4D/Components?node-id=10-52)

   I couldn't find a match for CustomWidget (10:53).

   Would you like to connect these components? You can accept all, select specific ones, or skip.
   ```

6. User: "Just connect ProductCard, skip Badge for now"
7. Run `send_code_connect_mappings(fileKey="pR8mNv5KqXzGwY2JtCfL4D", nodeId="10:50", mappings=[{ nodeId: "10:51", componentName: "ProductCard", source: "src/components/ProductCard.tsx", label: "React" }])`

**Result:** Only ProductCard is connected, per the user's selection.

### Example 3: Component Needs Creation

User says: "Connect this icon: https://figma.com/design/8yJDMeWDyBz71EnMOSuUiw/Icons?node-id=5-20"

**Actions:**

1. Parse URL: fileKey=`8yJDMeWDyBz71EnMOSuUiw`, nodeId=`5-20` → convert to `5:20`
2. Run `get_code_connect_suggestions(fileKey="8yJDMeWDyBz71EnMOSuUiw", nodeId="5:20")`
3. Response shows: CheckIcon component (unmapped) with color and size properties
4. Search codebase for CheckIcon: No matches found
5. Search for generic Icon components: Find `src/icons/` directory with other icons
6. Report to user: "I couldn't find a CheckIcon component, but I found an icons directory at src/icons/. Would you like to:
   - Create a new CheckIcon.tsx component first, then connect it
   - Connect to a different existing icon
   - Provide the path to the CheckIcon if it exists elsewhere"
7. User provides path: "src/icons/CheckIcon.tsx"
8. Detect language and framework from the file
9. Run `send_code_connect_mappings(fileKey="8yJDMeWDyBz71EnMOSuUiw", nodeId="5:20", mappings=[{ nodeId: "5:20", componentName: "CheckIcon", source: "src/icons/CheckIcon.tsx", label: "React" }])`

**Result:** CheckIcon component is successfully connected to the Figma design.

## Best Practices

### Proactive Component Discovery

Don't just ask the user for the file path — actively search their codebase to find matching components. This provides a better experience and catches potential mapping opportunities.

### Accurate Structure Matching

When comparing Figma components to code components, look beyond just names. Check that:

- Props align (variant types, size options, etc.)
- Component hierarchy matches (nested elements)
- The component serves the same purpose

### Clear Communication

When offering to create a mapping, clearly explain:

- What you found
- Why it's a good match
- What the mapping will do
- How props will be connected

### Handle Ambiguity

If multiple components could match, present options rather than guessing. Let the user make the final decision about which component to connect.

### Graceful Degradation

If you can't find an exact match, provide helpful next steps:

- Show close candidates
- Suggest component creation
- Ask for user guidance

## Common Issues and Solutions

### Issue: "No published components found in this selection"

**Cause:** The Figma component is not published to a team library. Code Connect only works with published components.
**Solution:** The user needs to publish the component to a team library in Figma:

1. In Figma, select the component or component set
2. Right-click and choose "Publish to library" or use the Team Library publish modal
3. Publish the component
4. Once published, retry the Code Connect mapping with the same node ID

### Issue: "Code Connect is only available on Organization and Enterprise plans"

**Cause:** The user's Figma plan does not include Code Connect access.
**Solution:** The user needs to upgrade to an Organization or Enterprise plan, or contact their administrator.

### Issue: No matching component found in codebase

**Cause:** The codebase search did not find a component with a matching name or structure.
**Solution:** Ask the user if the component exists under a different name or in a different location. They may need to create the component first, or it might be located in an unexpected directory.

### Issue: "Published component not found" (CODE_CONNECT_ASSET_NOT_FOUND)

**Cause:** The source file path is incorrect, the component doesn't exist at that location, or the componentName doesn't match the actual export.
**Solution:** Verify the source path is correct and relative to the project root. Check that the component is properly exported from the file with the exact componentName specified.

### Issue: "Component is already mapped to code" (CODE_CONNECT_MAPPING_ALREADY_EXISTS)

**Cause:** A Code Connect mapping already exists for this component.
**Solution:** The component is already connected. If the user wants to update the mapping, they may need to remove the existing one first in Figma.

### Issue: "Insufficient permissions to create mapping" (CODE_CONNECT_INSUFFICIENT_PERMISSIONS)

**Cause:** The user does not have edit permissions on the Figma file or library.
**Solution:** The user needs edit access to the file containing the component. Contact the file owner or team admin.

### Issue: Code Connect mapping fails with URL errors

**Cause:** The Figma URL format is incorrect or missing the `node-id` parameter.
**Solution:** Verify the URL follows the required format: `https://figma.com/design/:fileKey/:fileName?node-id=1-2`. The `node-id` parameter is required. Also ensure you convert `1-2` to `1:2` when calling tools.

### Issue: Multiple similar components found

**Cause:** The codebase contains multiple components that could match the Figma component.
**Solution:** Present all candidates to the user with their file paths and let them choose which one to connect. Different components might be used in different contexts (e.g., `Button.tsx` vs `LinkButton.tsx`).

## Understanding Code Connect

Code Connect establishes a bidirectional link between design and code:

**For designers:** See which code component implements a Figma component
**For developers:** Navigate from Figma designs directly to the code that implements them
**For teams:** Maintain a single source of truth for component mappings

The mapping you create helps keep design and code in sync by making these connections explicit and discoverable.

## Additional Resources

For more information about Code Connect:

- [Code Connect Documentation](<https://help.figma.com/hc/en-us/articles/23920389749655-Code-Connect>)
- [Figma MCP Server Tools and Prompts](<https://developers.figma.com/docs/figma-mcp-server/tools-and-prompts/>)


</details>
