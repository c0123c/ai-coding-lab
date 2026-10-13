# Figma MCP 集成技能

通过设计上下文、截图、变量和资源实现界面，规定读取与验证顺序、代码约定及资源处理方式。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-02-01](<https://github.com/openai/skills/commit/dad3072ab3236e7315749a20a0abe48b53f831e2>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/figma/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Figma MCP 集成技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: figma
description: Use the Figma MCP server to fetch design context, screenshots, variables, and assets from Figma, and to translate Figma nodes into production code. Trigger when a task involves Figma URLs, node IDs, design-to-code implementation, or Figma MCP setup and troubleshooting.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Figma MCP

使用 Figma MCP 服务器进行 Figma 驱动的实施。有关设置和调试详细信息（环境变量、配置、验证），请参阅 `references/figma-mcp-config.md`。

## Figma MCP 积分规则
这些规则定义了如何将 Figma 输入转换为该项目的代码，并且每个 Figma 驱动的更改都必须遵循这些规则。

### 所需流程（请勿跳过）
1. 首先运行 get_design_context 以获取确切节点的结构化表示。
2. 如果响应太大或被截断，请运行 get_metadata 以获取高级节点映射，然后使用 get_design_context 仅重新获取所需的节点。
3. 运行 get_screenshot 以获得正在实现的节点变体的视觉参考。
4. 仅当您同时拥有 get_design_context 和 get_screenshot 后，才下载所需的任何资产并开始实施。
5. 将输出（通常是 React + Tailwind）转换为该项目的约定、样式和框架。尽可能重用项目的颜色标记、组件和版式。
6. 在标记完成之前，根据 Figma 验证 1:1 的外观和行为。

### 实施细则
- 将 Figma MCP 输出（React + Tailwind）视为设计和行为的表示，而不是最终的代码样式。
- 如果适用，将 Tailwind 实用程序类替换为项目的首选 utilities/design-system 令牌。
- 重用现有组件（例如按钮、输入、排版、图标包装）而不是重复功能。
- 一致地使用项目的颜色系统、版式比例和间距标记。
- 尊重存储库中已采用的现有路由、状态管理和数据获取模式。
- 努力实现 1:1 与 Figma 设计的视觉对等。当出现冲突时，更喜欢设计系统标记并最小化调整间距或大小以匹配视觉效果。
- 根据 Figma 屏幕截图验证最终的 UI 的外观和行为。

### 资产处理
- Figma MCP 服务器提供了一个资产端点，可以为图像和 SVG 资产提供服务。
- IMPORTANT：如果 Figma MCP 服务器返回图像或 SVG 的本地主机源，请直接使用该图像或 SVG 源。
- IMPORTANT：DO NOT import/add 新图标包，所有资源应位于 Figma 负载中。
- IMPORTANT：如果提供了本地主机源，则NOT使用或创建占位符。

### 基于链接的提示
- 服务器是基于链接的：复制 Figma frame/layer 链接，并在寻求实施帮助时将 URL 提供给 MCP 客户端。
- 客户端无法浏览URL，但从链接中提取节点ID；始终确保链接指向您想要的确切 node/variant。

## 参考文献
- `references/figma-mcp-config.md` — 设置、验证、故障排除和基于链接的使用提醒。
- `references/figma-tools-and-prompts.md` — 用于选择 frameworks/components 和获取元数据的工具目录和提示模式。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Figma MCP

Use the Figma MCP server for Figma-driven implementation. For setup and debugging details (env vars, config, verification), see `references/figma-mcp-config.md`.

## Figma MCP Integration Rules
These rules define how to translate Figma inputs into code for this project and must be followed for every Figma-driven change.

### Required flow (do not skip)
1. Run get_design_context first to fetch the structured representation for the exact node(s).
2. If the response is too large or truncated, run get_metadata to get the high-level node map and then re-fetch only the required node(s) with get_design_context.
3. Run get_screenshot for a visual reference of the node variant being implemented.
4. Only after you have both get_design_context and get_screenshot, download any assets needed and start implementation.
5. Translate the output (usually React + Tailwind) into this project's conventions, styles and framework. Reuse the project's color tokens, components, and typography wherever possible.
6. Validate against Figma for 1:1 look and behavior before marking complete.

### Implementation rules
- Treat the Figma MCP output (React + Tailwind) as a representation of design and behavior, not as final code style.
- Replace Tailwind utility classes with the project's preferred utilities/design-system tokens when applicable.
- Reuse existing components (e.g., buttons, inputs, typography, icon wrappers) instead of duplicating functionality.
- Use the project's color system, typography scale, and spacing tokens consistently.
- Respect existing routing, state management, and data-fetch patterns already adopted in the repo.
- Strive for 1:1 visual parity with the Figma design. When conflicts arise, prefer design-system tokens and adjust spacing or sizes minimally to match visuals.
- Validate the final UI against the Figma screenshot for both look and behavior.

### Asset handling
- The Figma MCP Server provides an assets endpoint which can serve image and SVG assets.
- IMPORTANT: If the Figma MCP Server returns a localhost source for an image or an SVG, use that image or SVG source directly.
- IMPORTANT: DO NOT import/add new icon packages, all the assets should be in the Figma payload.
- IMPORTANT: do NOT use or create placeholders if a localhost source is provided.

### Link-based prompting
- The server is link-based: copy the Figma frame/layer link and give that URL to the MCP client when asking for implementation help.
- The client cannot browse the URL but extracts the node ID from the link; always ensure the link points to the exact node/variant you want.

## References
- `references/figma-mcp-config.md` — setup, verification, troubleshooting, and link-based usage reminders.
- `references/figma-tools-and-prompts.md` — tool catalog and prompt patterns for selecting frameworks/components and fetching metadata.


</details>
