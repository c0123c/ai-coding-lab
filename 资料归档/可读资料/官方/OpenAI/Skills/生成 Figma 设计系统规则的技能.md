# 生成 Figma 设计系统规则的技能

对照代码库整理组件、样式、资源和 MCP 约定，保存到项目规则文件并验证，帮助约束设计稿转代码。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/openai/skills/commit/0e7823cca07bc2cbf34718a383f9ae92525be6a5>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/figma-create-design-system-rules/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/skills/curated/figma-create-design-system-rules/SKILL.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: figma-create-design-system-rules
description: Generates custom design system rules for the user's codebase. Use when user says "create design system rules", "generate rules for my project", "set up design rules", "customize design system guidelines", or wants to establish project-specific conventions for Figma-to-code workflows. Requires Figma MCP server connection.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 创建设计系统规则

## 概述

此技能可帮助您生成适合项目特定需求的定制设计系统规则。这些规则指导 AI 编码 agents 在实现 Figma 设计时生成一致、高质量的代码，确保自动遵循团队的约定、组件模式和架构决策。

### 支持的规则文件

| Agent |规则文件|
|-------|-----------|
| Claude Code | `CLAUDE.md` |
| Codex CLI | `AGENTS.md` |
| Cursor | `.cursor/rules/figma-design-system.mdc` |

## 什么是设计系统规则？

设计系统规则是项目级指令，对代码库的“不成文知识”进行编码 - 经验丰富的开发人员知道并将传递给新团队成员的专业知识：

- 使用哪些布局基元和组件
- 组件文件应位于何处
- 组件应如何命名和构造
- 什么不应该被硬编码
- 如何处理设计标记和样式
- 项目特定的架构模式

一旦定义，这些规则将大大减少重复提示，并确保所有 Figma 实施任务的输出一致。

## 先决条件

- Figma MCP 服务器必须已连接且可访问
- 访问项目代码库进行分析
- 了解团队的组件约定（或愿意建立它们）

## 何时使用此技能

在以下情况下使用此技能：

- 开始一个将使用 Figma 设计的新项目
- 将 AI 编码 agent 添加到具有既定模式的现有项目
- 在整个团队中标准化 Figma 到代码的工作流程
- 更新或完善现有的设计系统约定
- 用户明确请求：“创建设计系统规则”、“设置Figma指南”、“为我的项目定制规则”

## 所需的工作流程

**按顺序执行以下步骤。请勿跳过步骤。**

### 步骤 1：运行创建设计系统规则工具

Call the Figma MCP server's `create_design_system_rules` tool to get the foundational prompt and template.

**参数：**

- `clientLanguages`：项目中使用的以逗号分隔的语言列表（例如，“typescript，javascript”，“python”，“javascript”）
- `clientFrameworks`：正在使用的框架（例如，“react”、“vue”、“svelte”、“Angular”、“unknown”）

This tool returns guidance and a template for creating design system rules.

按照工具响应中提供的模板格式构建设计系统规则。

### 步骤 2：分析代码库

在最终确定规则之前，分析项目以了解现有模式：

**组件组织：**

- Where are UI components located? (e.g., `src/components/`, `app/ui/`, `lib/components/`)
- Is there a dedicated design system directory?
- How are components organized? (by feature, by type, flat structure)

**造型手法：**

- What CSS framework or approach is used? （Tailwind、CSS 模块、样式组件等）
- Where are design tokens defined? （CSS 变量、主题文件、配置文件）
- 是否有现有的颜色、排版或间距标记？

**Component Patterns:**

- What naming conventions are used? (PascalCase, kebab-case, prefixes)
- How are component props typically structured?
- Are there common composition patterns?

**Architecture Decisions:**

- How is state management handled?
- What routing system is used?
- Are there specific import patterns or path aliases?

### Step 3: Generate Project-Specific Rules

Based on your codebase analysis, create a comprehensive set of rules.包括：

#### 一般组件规则

```markdown
- IMPORTANT: Always use components from `[YOUR_PATH]` when possible
- Place new UI components in `[COMPONENT_DIRECTORY]`
- Follow `[NAMING_CONVENTION]` for component names
- Components must export as `[EXPORT_PATTERN]`
```

#### 造型规则

```markdown
- Use `[CSS_FRAMEWORK/APPROACH]` for styling
- Design tokens are defined in `[TOKEN_LOCATION]`
- IMPORTANT: Never hardcode colors - always use tokens from `[TOKEN_FILE]`
- Spacing values must use the `[SPACING_SYSTEM]` scale
- Typography follows the scale defined in `[TYPOGRAPHY_LOCATION]`
```

#### Figma MCP 积分规则

```markdown
## Figma MCP Integration Rules

These rules define how to translate Figma inputs into code for this project and must be followed for every Figma-driven change.

### Required Flow (do not skip)

1. Run get_design_context first to fetch the structured representation for the exact node(s)
2. If the response is too large or truncated, run get_metadata to get the high-level node map, then re-fetch only the required node(s) with get_design_context
3. Run get_screenshot for a visual reference of the node variant being implemented
4. Only after you have both get_design_context and get_screenshot, download any assets needed and start implementation
5. Translate the output (usually React + Tailwind) into this project's conventions, styles, and framework
6. Validate against Figma for 1:1 look and behavior before marking complete

### Implementation Rules

- Treat the Figma MCP output (React + Tailwind) as a representation of design and behavior, not as final code style
- Replace Tailwind utility classes with `[YOUR_STYLING_APPROACH]` when applicable
- Reuse existing components from `[COMPONENT_PATH]` instead of duplicating functionality
- Use the project's color system, typography scale, and spacing tokens consistently
- Respect existing routing, state management, and data-fetch patterns
- Strive for 1:1 visual parity with the Figma design
- Validate the final UI against the Figma screenshot for both look and behavior
```

#### 资产处理规则

```markdown
## Asset Handling

- The Figma MCP server provides an assets endpoint which can serve image and SVG assets
- IMPORTANT: If the Figma MCP server returns a localhost source for an image or SVG, use that source directly
- IMPORTANT: DO NOT import/add new icon packages - all assets should be in the Figma payload
- IMPORTANT: DO NOT use or create placeholders if a localhost source is provided
- Store downloaded assets in `[ASSET_DIRECTORY]`
```

#### 项目特定约定

```markdown
## Project-Specific Conventions

- [Add any unique architectural patterns]
- [Add any special import requirements]
- [Add any testing requirements]
- [Add any accessibility standards]
- [Add any performance considerations]
```

### Step 4: Save Rules to the Appropriate Rule File

Detect which AI coding agent the user is working with and save the generated rules to the corresponding file:

| Agent |规则文件|注释|
|-------|-----------|-------|
| Claude Code |`CLAUDE.md` 在项目根目录中|降价格式。 Can also use `.claude/rules/figma-design-system.md` for modular organization.|
| Codex CLI |`AGENTS.md` 在项目根目录中|降价格式。如果文件已存在，则附加为新部分。 32 KiB 组合尺寸限制。|
| Cursor | `.cursor/rules/figma-design-system.mdc` |Markdown with YAML frontmatter (`description`, `globs`, `alwaysApply`).|

If unsure which agent the user is working with, check for existing rule files in the project or ask the user.

For Cursor, wrap the rules with YAML frontmatter:

```markdown
---
description: Rules for implementing Figma designs using the Figma MCP server. Covers component organization, styling conventions, design tokens, asset handling, and the required Figma-to-code workflow.
globs: "src/components/**"
alwaysApply: false
---

[Generated rules here]
```

自定义 `globs` 模式以匹配 Figma 派生代码将驻留在项目中的目录（例如，`"src/**/*.tsx"` 或 `["src/components/**", "src/pages/**"]`）。

After saving, the rules will be automatically loaded by the agent and applied to all Figma implementation tasks.

### 步骤 5：验证和迭代

创建规则后：

1. Test with a simple Figma component implementation
2. Verify the agent follows the rules correctly
3. Refine any rules that aren't working as expected
4. 与团队成员分享以获得反馈
5. 随着项目的发展更新规则

## 规则类别和示例

### 基本规则（始终包括）

**组件发现：**

```markdown
- UI components are located in `src/components/ui/`
- Feature components are in `src/components/features/`
- Layout primitives are in `src/components/layout/`
```

**设计令牌用法：**

```markdown
- Colors are defined as CSS variables in `src/styles/tokens.css`
- Never hardcode hex colors - use `var(--color-*)` tokens
- Spacing uses the 4px base scale: `--space-1` (4px), `--space-2` (8px), etc.
```

**造型手法：**

```markdown
- Use Tailwind utility classes for styling
- Custom styles go in component-level CSS modules
- Theme customization is in `tailwind.config.js`
```

### 推荐规则（非常有价值）

**Component Patterns:**

```markdown
- All components must accept a `className` prop for composition
- Variant props should use union types: `variant: 'primary' | 'secondary'`
- Icon components should accept `size` and `color` props
```

**导入约定：**

```markdown
- Use path aliases: `@/components`, `@/styles`, `@/utils`
- Group imports: React, third-party, internal, types
- No relative imports beyond parent directory
```

**代码质量：**

```markdown
- Add JSDoc comments for exported components
- Include PropTypes or TypeScript types for all props
- Extract magic numbers to named constants
```

### 可选规则（特定于项目）

**辅助功能：**

```markdown
- All interactive elements must have aria-labels
- Color contrast must meet WCAG AA standards
- Keyboard navigation required for all interactions
```

**性能：**

```markdown
- Lazy load images with the `Image` component from `@/components/Image`
- Use React.memo for components that receive complex props
- Icons should be SVG components, not icon fonts
```

**测试：**

```markdown
- Include unit tests for new components in `__tests__/` directory
- Use Testing Library queries (getByRole, getByLabelText)
- Test all interactive states and variants
```

## 示例

### Example 1: React + Tailwind Project

User says: "Create design system rules for my React project"

**操作：**

1. 运行`create_design_system_rules(clientLanguages="typescript,javascript", clientFrameworks="react")`
2. 分析代码库结构
3. 生成规则：

```markdown
# Figma MCP Integration Rules

## Component Organization

- UI components are in `src/components/ui/`
- Page components are in `src/app/`
- Use Tailwind for styling

## Figma Implementation Flow

1. Run get_design_context for the node
2. Run get_screenshot for visual reference
3. Map Figma colors to Tailwind colors defined in `tailwind.config.js`
4. Reuse components from `src/components/ui/` when possible
5. Validate against screenshot before completing

## Styling Rules

- IMPORTANT: Use Tailwind utility classes, not inline styles
- Colors are defined in `tailwind.config.js` theme.colors
- Spacing uses Tailwind's default scale
- Custom components go in `src/components/ui/`

## Asset Rules

- IMPORTANT: Use localhost sources from Figma MCP server directly
- Store static assets in `public/assets/`
- DO NOT install new icon libraries
```

4. 保存到用户的 agent 的相应规则文件中
5. 使用简单的按钮实现进行测试

**结果：** agent 现在在实施 Figma 设计时始终遵循项目的惯例。

### 示例 2：Vue + 自定义 CSS 项目

用户说：“为我的 Vue 应用程序设置 Figma 规则”

**操作：**

1. 运行`create_design_system_rules(clientLanguages="typescript,javascript", clientFrameworks="vue")`
2. 发现该项目使用 CSS 模块和自定义设计令牌系统
3. 生成规则：

```markdown
# Figma MCP Integration Rules

## Component Organization

- Components are in `src/components/`
- Composables are in `src/composables/`
- Vue SFC structure: <script setup>, <template>, <style scoped>

## Design Tokens

- IMPORTANT: All colors defined in `src/styles/tokens.css` as CSS variables
- Use `var(--color-primary)`, `var(--color-secondary)`, etc.
- Spacing: `var(--space-xs)` through `var(--space-xl)`
- Typography: `var(--text-sm)` through `var(--text-2xl)`

## Figma Implementation Flow

1. Run get_design_context and get_screenshot
2. Translate React output to Vue 3 Composition API
3. Map Figma colors to CSS variables in `src/styles/tokens.css`
4. Use CSS Modules for component styles
5. Check for existing components in `src/components/` before creating new ones

## Styling Rules

- Use CSS Modules (`.module.css` files)
- IMPORTANT: Reference design tokens, never hardcode values
- Scoped styles with CSS modules
```

4. 保存到用户的 agent 的相应规则文件中
5. 使用卡组件进行验证

**结果：** agent 现在使用项目的设计令牌系统正确地将 Figma 设计转换为 Vue 组件。

### 示例3：设计系统团队

用户说：“为我们的设计系统库生成规则”

**操作：**

1. 运行`create_design_system_rules(clientLanguages="typescript", clientFrameworks="react")`
2. 使用设计系统包分析 monorepo 结构
3. 生成综合规则：

```markdown
# Design System Rules

## Repository Structure

- Design system components: `packages/design-system/src/components/`
- Documentation: `packages/docs/`
- Design tokens: `packages/tokens/src/`

## Component Development

- IMPORTANT: All components must be in `packages/design-system/src/components/`
- Component file structure: `ComponentName/index.tsx`, `ComponentName.stories.tsx`, `ComponentName.test.tsx`
- Export all components from `packages/design-system/src/index.ts`

## Design Tokens

- Colors: `packages/tokens/src/colors.ts`
- Typography: `packages/tokens/src/typography.ts`
- Spacing: `packages/tokens/src/spacing.ts`
- IMPORTANT: Never hardcode values - import from tokens package

## Documentation Requirements

- Add Storybook story for every component
- Include JSDoc with @example
- Document all props with descriptions
- Add accessibility notes

## Figma Integration

1. Get design context and screenshot from Figma
2. Map Figma tokens to design system tokens
3. Create or extend component in design system package
4. Add Storybook stories showing all variants
5. Validate against Figma screenshot
6. Update documentation
```

4. 保存到适当的规则文件并与团队共享
5. 添加到团队文档

**结果：** 整个团队在将 Figma 中的组件添加到设计系统时遵循一致的模式。

## 最佳实践

### 从简单开始，迭代

不要试图预先掌握每条规则。从最重要的约定开始，并在遇到不一致时添加规则。

### 具体一点

而不是：“使用设计系统”
写入：“始终使用 `src/components/ui/Button.tsx` 中的 Button 组件和变体道具（'主要'|'次要'|'幽灵'）”

### 让规则具有可操作性

每条规则都应该告诉 agent 到底要做什么，而不仅仅是要避免什么。

好：“颜色在 `src/theme/colors.ts` 中定义 - 导入并使用这些常量”
坏：“不要硬编码颜色”

### 使用 IMPORTANT 作为关键规则

“IMPORTANT:”不得违反前缀规则，以确保 agent 优先考虑它们。

```markdown
- IMPORTANT: Never expose API keys in client-side code
- IMPORTANT: Always sanitize user input before rendering
```

### 记录原因

当规则看起来任意时，请解释原因：

```markdown
- Place all data-fetching in server components (reduces client bundle size and improves performance)
- Use absolute imports with `@/` alias (makes refactoring easier and prevents broken relative paths)
```

## 常见问题及解决方案

### 问题：agent 不遵守规则

**原因：**规则可能过于模糊或agent未正确加载。
**解决方案：**

- 使规则更加具体和可操作
- 验证规则是否保存在正确的配置文件中
- 重新启动您的agent或IDE以重新加载规则
- 在关键规则中添加“IMPORTANT：”前缀

### 问题：规则相互冲突

**原因：**规则相互矛盾或重叠。
**解决方案：**

- 检查所有冲突规则
- 建立清晰的优先级层次结构
- 删除冗余规则
- 将相关规则合并为单一、清晰的陈述

### 问题：规则过多会增加延迟

**原因：**过多的规则会增加上下文大小和处理时间。
**解决方案：**

- 重点关注解决80%一致性问题的规则20%
- 删除很少适用的过于具体的规则
- 结合相关规则
- 使用渐进式披露（基本规则优先，链接文件中的高级规则）

### 问题：随着项目的发展，规则变得过时

**原因：**代码库更改，但规则不变。
**解决方案：**

- 安排定期规则审查（每月或每季度）
- 当架构决策发生变化时更新规则
- 版本控制您的规则文件
- 记录提交消息中的规则更改

## 了解设计系统规则

设计系统规则改变了 AI 编码 agents 如何与您的 Figma 设计配合使用：

**规则前：**

- agent 对组件结构做出假设
- 跨实现的样式方法不一致
- 与设计标记不匹配的硬编码值
- 在随机位置创建的组件
- 项目约定的重复解释

**规则后：**

- agent 自动遵循您的约定
- 一致的组件结构和样式
- 从一开始就正确使用设计令牌
- 组件组织正确
- 零重复提示

在创建良好规则上投入的时间在每个 Figma 实施任务中都会得到指数级的回报。

## 其他资源

- [Figma MCP 服务器文档](<https://developers.figma.com/docs/figma-mcp-server/>)
- [Figma 变量和设计令牌](<https://help.figma.com/hc/en-us/articles/15339657135383-Guide-to-variables-in-Figma>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Create Design System Rules

## Overview

This skill helps you generate custom design system rules tailored to your project's specific needs. These rules guide AI coding agents to produce consistent, high-quality code when implementing Figma designs, ensuring that your team's conventions, component patterns, and architectural decisions are followed automatically.

### Supported Rule Files

| Agent | Rule File |
|-------|-----------|
| Claude Code | `CLAUDE.md` |
| Codex CLI | `AGENTS.md` |
| Cursor | `.cursor/rules/figma-design-system.mdc` |

## What Are Design System Rules?

Design system rules are project-level instructions that encode the "unwritten knowledge" of your codebase - the kind of expertise that experienced developers know and would pass on to new team members:

- Which layout primitives and components to use
- Where component files should be located
- How components should be named and structured
- What should never be hardcoded
- How to handle design tokens and styling
- Project-specific architectural patterns

Once defined, these rules dramatically reduce repetitive prompting and ensure consistent output across all Figma implementation tasks.

## Prerequisites

- Figma MCP server must be connected and accessible
- Access to the project codebase for analysis
- Understanding of your team's component conventions (or willingness to establish them)

## When to Use This Skill

Use this skill when:

- Starting a new project that will use Figma designs
- Onboarding an AI coding agent to an existing project with established patterns
- Standardizing Figma-to-code workflows across your team
- Updating or refining existing design system conventions
- Users explicitly request: "create design system rules", "set up Figma guidelines", "customize rules for my project"

## Required Workflow

**Follow these steps in order. Do not skip steps.**

### Step 1: Run the Create Design System Rules Tool

Call the Figma MCP server's `create_design_system_rules` tool to get the foundational prompt and template.

**Parameters:**

- `clientLanguages`: Comma-separated list of languages used in the project (e.g., "typescript,javascript", "python", "javascript")
- `clientFrameworks`: Framework being used (e.g., "react", "vue", "svelte", "angular", "unknown")

This tool returns guidance and a template for creating design system rules.

Structure your design system rules following the template format provided in the tool's response.

### Step 2: Analyze the Codebase

Before finalizing rules, analyze the project to understand existing patterns:

**Component Organization:**

- Where are UI components located? (e.g., `src/components/`, `app/ui/`, `lib/components/`)
- Is there a dedicated design system directory?
- How are components organized? (by feature, by type, flat structure)

**Styling Approach:**

- What CSS framework or approach is used? (Tailwind, CSS Modules, styled-components, etc.)
- Where are design tokens defined? (CSS variables, theme files, config files)
- Are there existing color, typography, or spacing tokens?

**Component Patterns:**

- What naming conventions are used? (PascalCase, kebab-case, prefixes)
- How are component props typically structured?
- Are there common composition patterns?

**Architecture Decisions:**

- How is state management handled?
- What routing system is used?
- Are there specific import patterns or path aliases?

### Step 3: Generate Project-Specific Rules

Based on your codebase analysis, create a comprehensive set of rules. Include:

#### General Component Rules

```markdown
- IMPORTANT: Always use components from `[YOUR_PATH]` when possible
- Place new UI components in `[COMPONENT_DIRECTORY]`
- Follow `[NAMING_CONVENTION]` for component names
- Components must export as `[EXPORT_PATTERN]`
```

#### Styling Rules

```markdown
- Use `[CSS_FRAMEWORK/APPROACH]` for styling
- Design tokens are defined in `[TOKEN_LOCATION]`
- IMPORTANT: Never hardcode colors - always use tokens from `[TOKEN_FILE]`
- Spacing values must use the `[SPACING_SYSTEM]` scale
- Typography follows the scale defined in `[TYPOGRAPHY_LOCATION]`
```

#### Figma MCP Integration Rules

```markdown
## Figma MCP Integration Rules

These rules define how to translate Figma inputs into code for this project and must be followed for every Figma-driven change.

### Required Flow (do not skip)

1. Run get_design_context first to fetch the structured representation for the exact node(s)
2. If the response is too large or truncated, run get_metadata to get the high-level node map, then re-fetch only the required node(s) with get_design_context
3. Run get_screenshot for a visual reference of the node variant being implemented
4. Only after you have both get_design_context and get_screenshot, download any assets needed and start implementation
5. Translate the output (usually React + Tailwind) into this project's conventions, styles, and framework
6. Validate against Figma for 1:1 look and behavior before marking complete

### Implementation Rules

- Treat the Figma MCP output (React + Tailwind) as a representation of design and behavior, not as final code style
- Replace Tailwind utility classes with `[YOUR_STYLING_APPROACH]` when applicable
- Reuse existing components from `[COMPONENT_PATH]` instead of duplicating functionality
- Use the project's color system, typography scale, and spacing tokens consistently
- Respect existing routing, state management, and data-fetch patterns
- Strive for 1:1 visual parity with the Figma design
- Validate the final UI against the Figma screenshot for both look and behavior
```

#### Asset Handling Rules

```markdown
## Asset Handling

- The Figma MCP server provides an assets endpoint which can serve image and SVG assets
- IMPORTANT: If the Figma MCP server returns a localhost source for an image or SVG, use that source directly
- IMPORTANT: DO NOT import/add new icon packages - all assets should be in the Figma payload
- IMPORTANT: DO NOT use or create placeholders if a localhost source is provided
- Store downloaded assets in `[ASSET_DIRECTORY]`
```

#### Project-Specific Conventions

```markdown
## Project-Specific Conventions

- [Add any unique architectural patterns]
- [Add any special import requirements]
- [Add any testing requirements]
- [Add any accessibility standards]
- [Add any performance considerations]
```

### Step 4: Save Rules to the Appropriate Rule File

Detect which AI coding agent the user is working with and save the generated rules to the corresponding file:

| Agent | Rule File | Notes |
|-------|-----------|-------|
| Claude Code | `CLAUDE.md` in project root | Markdown format. Can also use `.claude/rules/figma-design-system.md` for modular organization. |
| Codex CLI | `AGENTS.md` in project root | Markdown format. Append as a new section if file already exists. 32 KiB combined size limit. |
| Cursor | `.cursor/rules/figma-design-system.mdc` | Markdown with YAML frontmatter (`description`, `globs`, `alwaysApply`). |

If unsure which agent the user is working with, check for existing rule files in the project or ask the user.

For Cursor, wrap the rules with YAML frontmatter:

```markdown
---
description: Rules for implementing Figma designs using the Figma MCP server. Covers component organization, styling conventions, design tokens, asset handling, and the required Figma-to-code workflow.
globs: "src/components/**"
alwaysApply: false
---

[Generated rules here]
```

Customize the `globs` pattern to match the directories where Figma-derived code will live in the project (e.g., `"src/**/*.tsx"` or `["src/components/**", "src/pages/**"]`).

After saving, the rules will be automatically loaded by the agent and applied to all Figma implementation tasks.

### Step 5: Validate and Iterate

After creating rules:

1. Test with a simple Figma component implementation
2. Verify the agent follows the rules correctly
3. Refine any rules that aren't working as expected
4. Share with team members for feedback
5. Update rules as the project evolves

## Rule Categories and Examples

### Essential Rules (Always Include)

**Component Discovery:**

```markdown
- UI components are located in `src/components/ui/`
- Feature components are in `src/components/features/`
- Layout primitives are in `src/components/layout/`
```

**Design Token Usage:**

```markdown
- Colors are defined as CSS variables in `src/styles/tokens.css`
- Never hardcode hex colors - use `var(--color-*)` tokens
- Spacing uses the 4px base scale: `--space-1` (4px), `--space-2` (8px), etc.
```

**Styling Approach:**

```markdown
- Use Tailwind utility classes for styling
- Custom styles go in component-level CSS modules
- Theme customization is in `tailwind.config.js`
```

### Recommended Rules (Highly Valuable)

**Component Patterns:**

```markdown
- All components must accept a `className` prop for composition
- Variant props should use union types: `variant: 'primary' | 'secondary'`
- Icon components should accept `size` and `color` props
```

**Import Conventions:**

```markdown
- Use path aliases: `@/components`, `@/styles`, `@/utils`
- Group imports: React, third-party, internal, types
- No relative imports beyond parent directory
```

**Code Quality:**

```markdown
- Add JSDoc comments for exported components
- Include PropTypes or TypeScript types for all props
- Extract magic numbers to named constants
```

### Optional Rules (Project-Specific)

**Accessibility:**

```markdown
- All interactive elements must have aria-labels
- Color contrast must meet WCAG AA standards
- Keyboard navigation required for all interactions
```

**Performance:**

```markdown
- Lazy load images with the `Image` component from `@/components/Image`
- Use React.memo for components that receive complex props
- Icons should be SVG components, not icon fonts
```

**Testing:**

```markdown
- Include unit tests for new components in `__tests__/` directory
- Use Testing Library queries (getByRole, getByLabelText)
- Test all interactive states and variants
```

## Examples

### Example 1: React + Tailwind Project

User says: "Create design system rules for my React project"

**Actions:**

1. Run `create_design_system_rules(clientLanguages="typescript,javascript", clientFrameworks="react")`
2. Analyze codebase structure
3. Generate rules:

```markdown
# Figma MCP Integration Rules

## Component Organization

- UI components are in `src/components/ui/`
- Page components are in `src/app/`
- Use Tailwind for styling

## Figma Implementation Flow

1. Run get_design_context for the node
2. Run get_screenshot for visual reference
3. Map Figma colors to Tailwind colors defined in `tailwind.config.js`
4. Reuse components from `src/components/ui/` when possible
5. Validate against screenshot before completing

## Styling Rules

- IMPORTANT: Use Tailwind utility classes, not inline styles
- Colors are defined in `tailwind.config.js` theme.colors
- Spacing uses Tailwind's default scale
- Custom components go in `src/components/ui/`

## Asset Rules

- IMPORTANT: Use localhost sources from Figma MCP server directly
- Store static assets in `public/assets/`
- DO NOT install new icon libraries
```

4. Save to the appropriate rule file for the user's agent
5. Test with a simple button implementation

**Result:** The agent now consistently follows the project's conventions when implementing Figma designs.

### Example 2: Vue + Custom CSS Project

User says: "Set up Figma rules for my Vue app"

**Actions:**

1. Run `create_design_system_rules(clientLanguages="typescript,javascript", clientFrameworks="vue")`
2. Discover that project uses CSS modules and a custom design token system
3. Generate rules:

```markdown
# Figma MCP Integration Rules

## Component Organization

- Components are in `src/components/`
- Composables are in `src/composables/`
- Vue SFC structure: <script setup>, <template>, <style scoped>

## Design Tokens

- IMPORTANT: All colors defined in `src/styles/tokens.css` as CSS variables
- Use `var(--color-primary)`, `var(--color-secondary)`, etc.
- Spacing: `var(--space-xs)` through `var(--space-xl)`
- Typography: `var(--text-sm)` through `var(--text-2xl)`

## Figma Implementation Flow

1. Run get_design_context and get_screenshot
2. Translate React output to Vue 3 Composition API
3. Map Figma colors to CSS variables in `src/styles/tokens.css`
4. Use CSS Modules for component styles
5. Check for existing components in `src/components/` before creating new ones

## Styling Rules

- Use CSS Modules (`.module.css` files)
- IMPORTANT: Reference design tokens, never hardcode values
- Scoped styles with CSS modules
```

4. Save to the appropriate rule file for the user's agent
5. Validate with a card component

**Result:** The agent now correctly translates Figma designs to Vue components using the project's design token system.

### Example 3: Design System Team

User says: "Generate rules for our design system library"

**Actions:**

1. Run `create_design_system_rules(clientLanguages="typescript", clientFrameworks="react")`
2. Analyze the monorepo structure with design system package
3. Generate comprehensive rules:

```markdown
# Design System Rules

## Repository Structure

- Design system components: `packages/design-system/src/components/`
- Documentation: `packages/docs/`
- Design tokens: `packages/tokens/src/`

## Component Development

- IMPORTANT: All components must be in `packages/design-system/src/components/`
- Component file structure: `ComponentName/index.tsx`, `ComponentName.stories.tsx`, `ComponentName.test.tsx`
- Export all components from `packages/design-system/src/index.ts`

## Design Tokens

- Colors: `packages/tokens/src/colors.ts`
- Typography: `packages/tokens/src/typography.ts`
- Spacing: `packages/tokens/src/spacing.ts`
- IMPORTANT: Never hardcode values - import from tokens package

## Documentation Requirements

- Add Storybook story for every component
- Include JSDoc with @example
- Document all props with descriptions
- Add accessibility notes

## Figma Integration

1. Get design context and screenshot from Figma
2. Map Figma tokens to design system tokens
3. Create or extend component in design system package
4. Add Storybook stories showing all variants
5. Validate against Figma screenshot
6. Update documentation
```

4. Save to the appropriate rule file and share with team
5. Add to team documentation

**Result:** Entire team follows consistent patterns when adding components from Figma to the design system.

## Best Practices

### Start Simple, Iterate

Don't try to capture every rule upfront. Start with the most important conventions and add rules as you encounter inconsistencies.

### Be Specific

Instead of: "Use the design system"
Write: "Always use Button components from `src/components/ui/Button.tsx` with variant prop ('primary' | 'secondary' | 'ghost')"

### Make Rules Actionable

Each rule should tell the agent exactly what to do, not just what to avoid.

Good: "Colors are defined in `src/theme/colors.ts` - import and use these constants"
Bad: "Don't hardcode colors"

### Use IMPORTANT for Critical Rules

Prefix rules that must never be violated with "IMPORTANT:" to ensure the agent prioritizes them.

```markdown
- IMPORTANT: Never expose API keys in client-side code
- IMPORTANT: Always sanitize user input before rendering
```

### Document the Why

When rules seem arbitrary, explain the reasoning:

```markdown
- Place all data-fetching in server components (reduces client bundle size and improves performance)
- Use absolute imports with `@/` alias (makes refactoring easier and prevents broken relative paths)
```

## Common Issues and Solutions

### Issue: The agent isn't following the rules

**Cause:** Rules may be too vague or not properly loaded by the agent.
**Solution:**

- Make rules more specific and actionable
- Verify rules are saved in the correct configuration file
- Restart your agent or IDE to reload rules
- Add "IMPORTANT:" prefix to critical rules

### Issue: Rules conflict with each other

**Cause:** Contradictory or overlapping rules.
**Solution:**

- Review all rules for conflicts
- Establish a clear priority hierarchy
- Remove redundant rules
- Consolidate related rules into single, clear statements

### Issue: Too many rules increase latency

**Cause:** Excessive rules increase context size and processing time.
**Solution:**

- Focus on the 20% of rules that solve 80% of consistency issues
- Remove overly specific rules that rarely apply
- Combine related rules
- Use progressive disclosure (basic rules first, advanced rules in linked files)

### Issue: Rules become outdated as project evolves

**Cause:** Codebase changes but rules don't.
**Solution:**

- Schedule periodic rule reviews (monthly or quarterly)
- Update rules when architectural decisions change
- Version control your rule files
- Document rule changes in commit messages

## Understanding Design System Rules

Design system rules transform how AI coding agents work with your Figma designs:

**Before rules:**

- The agent makes assumptions about component structure
- Inconsistent styling approaches across implementations
- Hardcoded values that don't match design tokens
- Components created in random locations
- Repetitive explanations of project conventions

**After rules:**

- The agent automatically follows your conventions
- Consistent component structure and styling
- Proper use of design tokens from the start
- Components organized correctly
- Zero repetitive prompting

The time invested in creating good rules pays off exponentially across every Figma implementation task.

## Additional Resources

- [Figma MCP Server Documentation](<https://developers.figma.com/docs/figma-mcp-server/>)
- [Figma Variables and Design Tokens](<https://help.figma.com/hc/en-us/articles/15339657135383-Guide-to-variables-in-Figma>)


</details>
