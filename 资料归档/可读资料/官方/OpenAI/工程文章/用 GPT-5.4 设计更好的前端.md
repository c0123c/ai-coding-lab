# 用 GPT-5.4 设计更好的前端

介绍设计原则、图片理解、工具使用、功能实现、电脑操作和验证，配合提示与技能示例改善网页质量。

- 来源：OpenAI／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-03-20](<https://developers.openai.com/blog/designing-delightful-frontends-with-gpt-5-4>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://developers.openai.com/blog/designing-delightful-frontends-with-gpt-5-4>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/工程文章/用 GPT-5.4 设计更好的前端.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 GPT-5.4 设计令人愉悦的前端

> 完整的文档索引，请参见[llms.txt](<https://developers.openai.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

GPT-5.4 是一个比其前辈更好的 Web 开发人员 - 生成更具视觉吸引力和雄心勃勃的前端。值得注意的是，我们训练了 GPT-5.4，重点是改进 UI 功能和图像使用。在正确的指导下，该模型可以生成可投入生产的前端​​，其中包含微妙的接触、精心设计的交互和美丽的图像。

网页设计可以产生大面积的结果。伟大的设计平衡了克制与发明——借鉴经受时间考验的模式，同时引入新的东西。 GPT-5.4 已经学习了广泛的设计方法，并了解构建网站的许多不同方式。

当提示未指定时，模型通常会回退到训练数据中的高频模式。其中一些是经过验证的惯例，但许多只是我们想要避免的过度表现的习惯。结果通常是合理且实用的，但它可能会倾向于通用结构、弱视觉层次以及与我们头脑中的想象不符的设计选择。

本指南解释了指导 GPT-5.4 实现您设想的设计的实用技术。

## 模型改进

虽然 GPT-5.4 在 [ 轴范围 ](<https://openai.com/index/introducing-gpt-5-4/>) 上有所改进，但对于前端工作，我们重点关注三个实际收益：

- 在整个设计过程中增强图像理解
- 功能更完整的应用程序和网站
- 更好地使用工具来检查、测试和验证自己的工作

### 图像理解和工具使用

GPT-5.4 经过训练，可以原生使用图像搜索和图像生成工具，使其能够将视觉推理直接纳入其设计过程。为了获得最佳结果，请指示模型在选择最终资产之前首先生成情绪板或多个视觉选项。

您可以通过明确描述图像应捕获的属性（例如风格、调色板、构图或情绪）来引导模型获得强大的视觉参考。您还应该包含提示说明，指导模型重用以前生成的图像、调用图像生成工具来创建新的视觉效果，或在需要时引用特定的外部图像。

```
Default to using any uploaded/pre-generated images. Otherwise use the image generation tool to create visually stunning image artifacts. Do not reference or link to web images unless the user explicitly asks for them.
```

### 功能改进

该模型经过训练可以开发更完整、功能更健全的应用程序。期望该模型在长期任务中更加可靠。您以前认为不可能的游戏和复杂的用户体验在一两回合内就成为现实。

### 计算机使用与验证

GPT-5.4 是我们第一个针对计算机使用进行训练的主线模型。它可以本地导航界面，并与 Playwright 等工具结合使用，可以迭代地检查其工作、验证行为并完善实现，从而实现更长、更自主的开发工作流程。

观看我们的 [ 发布视频 ](<https://openai.com/index/introducing-gpt-5-4/?video=1170427106%20>) 以了解这些功能的实际应用。

Playwright对于前端开发尤其有价值。它允许模型检查渲染的页面、测试多个视口、导航应用程序流以及检测状态或导航问题。提供剧作家工具或技能可以显着提高 GPT-5.4 生成精美、功能完整的界面的可能性。通过改进图像理解，它还为模型提供了一种直观验证其工作并检查其是否与参考 UI（如果提供）匹配的方法。

## 实用技巧快速入门

如果您仅采用本文档中的一些做法，请从以下开始：

1. 首先选择低推理水平。
2. 预先定义您的设计系统和约束（即排版、调色板、布局）。
3. 提供视觉参考或情绪板（即附加屏幕截图）以为模型提供视觉护栏。
4. 预先定义叙述或内容策略以指导模型的内容创建。

这是开始的提示。

```
## Frontend tasks

When doing frontend design tasks, avoid generic, overbuilt layouts.

**Use these hard rules:**
- One composition: The first viewport must read as one composition, not a dashboard (unless it's a dashboard).
- Brand first: On branded pages, the brand or product name must be a hero-level signal, not just nav text or an eyebrow. No headline should overpower the brand.
- Brand test: If the first viewport could belong to another brand after removing the nav, the branding is too weak.
- Typography: Use expressive, purposeful fonts and avoid default stacks (Inter, Roboto, Arial, system).
- Background: Don't rely on flat, single-color backgrounds; use gradients, images, or subtle patterns to build atmosphere.
- Full-bleed hero only: On landing pages and promotional surfaces, the hero image should be a dominant edge-to-edge visual plane or background by default. Do not use inset hero images, side-panel hero images, rounded media cards, tiled collages, or floating image blocks unless the existing design system clearly requires it.
- Hero budget: The first viewport should usually contain only the brand, one headline, one short supporting sentence, one CTA group, and one dominant image. Do not place stats, schedules, event listings, address blocks, promos, "this week" callouts, metadata rows, or secondary marketing content in the first viewport.
- No hero overlays: Do not place detached labels, floating badges, promo stickers, info chips, or callout boxes on top of hero media.
- Cards: Default: no cards. Never use cards in the hero. Cards are allowed only when they are the container for a user interaction. If removing a border, shadow, background, or radius does not hurt interaction or understanding, it should not be a card.
- One job per section: Each section should have one purpose, one headline, and usually one short supporting sentence.
- Real visual anchor: Imagery should show the product, place, atmosphere, or context. Decorative gradients and abstract backgrounds do not count as the main visual idea.
- Reduce clutter: Avoid pill clusters, stat strips, icon rows, boxed promos, schedule snippets, and multiple competing text blocks.
- Use motion to create presence and hierarchy, not noise. Ship at least 2-3 intentional motions for visually led work.
- Color & Look: Choose a clear visual direction; define CSS variables; avoid purple-on-white defaults. No purple bias or dark mode bias.
- Ensure the page loads properly on both desktop and mobile.
- For React code, prefer modern patterns including useEffectEvent, startTransition, and useDeferredValue when appropriate if used by the team. Do not add useMemo/useCallback by default unless already used; follow the repo's React Compiler guidance.

Exception: If working within an existing website or design system, preserve the established patterns, structure, and visual language.
```

## 更好的设计技术

### 从设计原则开始

定义约束，例如一个 H1 标题、不超过六个部分、最多两种字体、一种强调色和折叠上方的一种主要 CTA。

### 提供视觉参考

参考屏幕截图或情绪板可帮助模型推断布局节奏、排版比例、间距系统和图像处理。下面是 GPT-5.4 生成自己的情绪板供用户查看的示例。



![用于引导 GPT-5.4 走向有凝聚力的视觉方向的示例情绪板](<https://cdn.openai.com/devhub/blog/codex_moodboard.png>)




  

_
 以Codex中的GPT-5.4创建的情绪板，灵感来自NYC咖啡文化
和Y2K美学
_
  




### 以叙述方式构建页面

典型的营销页面结构：

1. 英雄——确立身份和承诺
2. 支持图像——显示背景或环境
3. 产品详细信息 - 解释产品
4. 社会证明——建立可信度
5. 最终 CTA — 将兴趣转化为行动

### 指导设计系统的遵守

鼓励模型在构建初期建立清晰的设计系统。定义核心设计标记，例如 `background`、`surface`、`primary text`、`muted text` 和 `accent`，以及排版角色，例如 `display`、`headline`、 `body`和`caption`。此结构有助于模型在整个应用程序中生成一致的、可扩展的 UI 模式。

对于大多数 Web 项目，从熟悉的堆栈（例如 **React 和 Tailwind **）开始效果很好。 GPT-5.4 在使用这些工具时表现尤其出色，可以更轻松地快速迭代并达到完美的结果。

运动和分层 UI 元素可能会带来复杂性，尤其是当固定或浮动组件与主要内容交互时。当使用动画、覆盖层或装饰层时，包含鼓励安全布局行为的指导会有所帮助。例如：

```
Keep fixed or floating UI elements from overlapping text, buttons, or other key content across screen sizes. Place them in safe areas, behind primary content where appropriate, and maintain sufficient spacing.
```

### 回拨推理

对于更简单的网站，更多的推理并不总是更好。在实践中，**中低推理水平通常会带来更强的前端结果**，帮助模型保持快速、专注，并且不易过度思考，同时仍然为更雄心勃勃的设计留出推理空间。

### 将设计融入真实内容

为模型提供真实的副本、产品上下文或明确的项目目标是改进前端结果的最简单方法之一。该上下文有助于它选择正确的网站结构，形成更清晰的部分级叙述，并编写更可信的消息传递，而不是退回到通用占位符模式。

## 将所有这些与前端技能结合在一起

为了帮助人们在一般前端任务中充分利用 GPT-5.4，我们还准备了一个专用的 [`frontend-skill`](<https://github.com/openai/skills/tree/main/skills/.curated/frontend-skill>)，您可以在下面找到。它为模型在结构、品味和交互模式方面提供了更强的指导，帮助其生成更精美、更有意向、更令人愉悦的开箱即用设计。



```
---
name: frontend-skill
description: Use when the task asks for a visually strong landing page, website, app, prototype, demo, or game UI. This skill enforces restrained composition, image-led hierarchy, cohesive content structure, and tasteful motion while avoiding generic cards, weak branding, and UI clutter.
---

# Frontend skill

Use this skill when the quality of the work depends on art direction, hierarchy, restraint, imagery, and motion rather than component count.

Goal: ship interfaces that feel deliberate, premium, and current. Default toward award-level composition: one big idea, strong imagery, sparse copy, rigorous spacing, and a small number of memorable motions.

## Working Model

Before building, write three things:

- visual thesis: one sentence describing mood, material, and energy
- content plan: hero, support, detail, final CTA
- interaction thesis: 2-3 motion ideas that change the feel of the page

Each section gets one job, one dominant visual idea, and one primary takeaway or action.

## Beautiful Defaults

- Start with composition, not components.
- Prefer a full-bleed hero or full-canvas visual anchor.
- Make the brand or product name the loudest text.
- Keep copy short enough to scan in seconds.
- Use whitespace, alignment, scale, cropping, and contrast before adding chrome.
- Limit the system: two typefaces max, one accent color by default.
- Default to cardless layouts. Use sections, columns, dividers, lists, and media blocks instead.
- Treat the first viewport as a poster, not a document.

## Landing Pages

Default sequence:

1. Hero: brand or product, promise, CTA, and one dominant visual
2. Support: one concrete feature, offer, or proof point
3. Detail: atmosphere, workflow, product depth, or story
4. Final CTA: convert, start, visit, or contact

Hero rules:

- One composition only.
- Full-bleed image or dominant visual plane.
- Canonical full-bleed rule: on branded landing pages, the hero itself must run edge-to-edge with no inherited page gutters, framed container, or shared max-width; constrain only the inner text/action column.
- Brand first, headline second, body third, CTA fourth.
- No hero cards, stat strips, logo clouds, pill soup, or floating dashboards by default.
- Keep headlines to roughly 2-3 lines on desktop and readable in one glance on mobile.
- Keep the text column narrow and anchored to a calm area of the image.
- All text over imagery must maintain strong contrast and clear tap targets.

If the first viewport still works after removing the image, the image is too weak. If the brand disappears after hiding the nav, the hierarchy is too weak.

Viewport budget:

- If the first screen includes a sticky/fixed header, that header counts against the hero. The combined header + hero content must fit within the initial viewport at common desktop and mobile sizes.
- When using `100vh`/`100svh` heroes, subtract persistent UI chrome (`calc(100svh - header-height)`) or overlay the header instead of stacking it in normal flow.

## Apps

Default to Linear-style restraint:

- calm surface hierarchy
- strong typography and spacing
- few colors
- dense but readable information
- minimal chrome
- cards only when the card is the interaction

For app UI, organize around:

- primary workspace
- navigation
- secondary context or inspector
- one clear accent for action or state

Avoid:

- dashboard-card mosaics
- thick borders on every region
- decorative gradients behind routine product UI
- multiple competing accent colors
- ornamental icons that do not improve scanning

If a panel can become plain layout without losing meaning, remove the card treatment.

## Imagery

Imagery must do narrative work.

- Use at least one strong, real-looking image for brands, venues, editorial pages, and lifestyle products.
- Prefer in-situ photography over abstract gradients or fake 3D objects.
- Choose or crop images with a stable tonal area for text.
- Do not use images with embedded signage, logos, or typographic clutter fighting the UI.
- Do not generate images with built-in UI frames, splits, cards, or panels.
- If multiple moments are needed, use multiple images, not one collage.

The first viewport needs a real visual anchor. Decorative texture is not enough.

## Copy

- Write in product language, not design commentary.
- Let the headline carry the meaning.
- Supporting copy should usually be one short sentence.
- Cut repetition between sections.
- do not include prompt language or design commentary into the UI
- Give every section one responsibility: explain, prove, deepen, or convert.

If deleting 30 percent of the copy improves the page, keep deleting.

## Utility Copy For Product UI

When the work is a dashboard, app surface, admin tool, or operational workspace, default to utility copy over marketing copy.

- Prioritize orientation, status, and action over promise, mood, or brand voice.
- Start with the working surface itself: KPIs, charts, filters, tables, status, or task context. Do not introduce a hero section unless the user explicitly asks for one.
- Section headings should say what the area is or what the user can do there.
- Good: "Selected KPIs", "Plan status", "Search metrics", "Top segments", "Last sync".
- Avoid aspirational hero lines, metaphors, campaign-style language, and executive-summary banners on product surfaces unless specifically requested.
- Supporting text should explain scope, behavior, freshness, or decision value in one sentence.
- If a sentence could appear in a homepage hero or ad, rewrite it until it sounds like product UI.
- If a section does not help someone operate, monitor, or decide, remove it.
- Litmus check: if an operator scans only headings, labels, and numbers, can they understand the page immediately?

## Motion

Use motion to create presence and hierarchy, not noise.

Ship at least 2-3 intentional motions for visually led work:

- one entrance sequence in the hero
- one scroll-linked, sticky, or depth effect
- one hover, reveal, or layout transition that sharpens affordance

Prefer Framer Motion when available for:

- section reveals
- shared layout transitions
- scroll-linked opacity, translate, or scale shifts
- sticky storytelling
- carousels that advance narrative, not just fill space
- menus, drawers, and modal presence effects

Motion rules:

- noticeable in a quick recording
- smooth on mobile
- fast and restrained
- consistent across the page
- removed if ornamental only

## Hard Rules

- No cards by default.
- No hero cards by default.
- No boxed or center-column hero when the brief calls for full bleed.
- No more than one dominant idea per section.
- No section should need many tiny UI devices to explain itself.
- No headline should overpower the brand on branded pages.
- No filler copy.
- No split-screen hero unless text sits on a calm, unified side.
- No more than two typefaces without a clear reason.
- No more than one accent color unless the product already has a strong system.

## Reject These Failures

- Generic SaaS card grid as the first impression
- Beautiful image with weak brand presence
- Strong headline with no clear action
- Busy imagery behind text
- Sections that repeat the same mood statement
- Carousel with no narrative purpose
- App UI made of stacked cards instead of layout

## Litmus Checks

- Is the brand or product unmistakable in the first screen?
- Is there one strong visual anchor?
- Can the page be understood by scanning headlines only?
- Does each section have one job?
- Are cards actually necessary?
- Does motion improve hierarchy or atmosphere?
- Would the design still feel premium if all decorative shadows were removed?

```



通过在 Codex 应用程序中运行以下命令来安装 `frontend-skill`：

```
$skill-installer frontend-skill
```

以下是在前端设计技能的帮助下生成的一些示例网站。

### 登陆页面

### 游戏

### 仪表板

## 要点

GPT-5.4 当提示提供清晰的设计约束、视觉参考、结构化叙述和定义的设计系统时，可以生成高质量的前端界面。

我们希望这些技术可以帮助您构建更加独特、设计精良的应用程序。

如果您想共享完全使用 GPT-5.4 和编码 agent（例如 [Codex](<http://developers.openai.com/codex>)）生成的项目，请提交您的应用程序以在我们的 [ 画廊 ](<http://developers.openai.com/showcase>) 中展示。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Designing delightful frontends with GPT-5.4

> For the complete documentation index, see [llms.txt](<https://developers.openai.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

GPT-5.4 is a better web developer than its predecessors—generating more visually appealing and ambitious frontends. Notably, we trained GPT-5.4 with a focus on improved UI capabilities and use of images. With the right guidance, the model can produce production-ready frontends incorporating subtle touches, well-crafted interactions, and beautiful imagery.

Web design can produce a large surface area of outcomes. Great design balances restraint with invention—drawing from patterns that have stood the test of time while introducing something new. GPT-5.4 has learned this wide spectrum of design approaches and understands many different ways a website can be built.

When prompts are underspecified, models often fall back to high-frequency patterns from the training data. Some of these are proven conventions, but many are simply overrepresented habits we want to avoid. The result is usually plausible and functional, but it can drift toward generic structure, weak visual hierarchy, and design choices that fall short of what we visualize in our heads.

This guide explains practical techniques for steering GPT-5.4 toward crafting the designs you envision.

## Model Improvements

While GPT-5.4 improves across a [range of axes](<https://openai.com/index/introducing-gpt-5-4/>), for front-end work we focused on three practical gains:

- stronger image understanding throughout the design process
- more functionally complete apps and websites
- better use of tools to inspect, test, and verify its own work

### Image understanding and tool use

GPT-5.4 was trained to use image search and image generation tools natively, allowing it to incorporate visual reasoning directly into its design process. For best results, instruct the model to first generate a mood board or several visual options before selecting the final assets.

You can guide the model toward strong visual references by explicitly describing the attributes the images should capture (e.g., style, color palette, composition, or mood). You should also include prompt instructions that guide the model to reuse previously generated images, call the image generation tool to create new visuals, or reference specific external images when required.

```
Default to using any uploaded/pre-generated images. Otherwise use the image generation tool to create visually stunning image artifacts. Do not reference or link to web images unless the user explicitly asks for them.
```

### Functionality improvements

The model was trained to develop more complete and functionally sound apps. Expect the model to be more reliable over long-horizon tasks. Games and complex user experiences you previously thought were impossible are a reality in one or two turns.

### Computer Use and Verification

GPT-5.4 is our first mainline model trained for computer use. It can natively navigate interfaces, and combined with tools such as Playwright, it can iteratively inspect its work, validate behavior, and refine implementations—enabling longer, more autonomous development workflows.

Watch our [launch video](<https://openai.com/index/introducing-gpt-5-4/?video=1170427106%20>) to see these capabilities in action.

Playwright is particularly valuable for front-end development. It allows the model to inspect rendered pages, test multiple viewports, navigate application flows, and detect issues with state or navigation. Providing a Playwright tool or skill significantly improves the likelihood that GPT-5.4 produces polished, functionally complete interfaces. With improved image understanding, it also provides a way for the model to verify its work visually and check that it matches the reference UI if provided.

## Practical tips quickstart

If you adopt only a few practices from this document, start with these:

1. Select low reasoning level to begin with.
2. Define your design system and constraints upfront (i.e., typography, color palette, layout).
3. Provide visual references or a mood board (i.e., attach a screenshot) to provide visual guardrails for the model.
4. Define a narrative or content strategy upfront to guide the model's content creation.

Here’s a prompt to get started.

```
## Frontend tasks

When doing frontend design tasks, avoid generic, overbuilt layouts.

**Use these hard rules:**
- One composition: The first viewport must read as one composition, not a dashboard (unless it's a dashboard).
- Brand first: On branded pages, the brand or product name must be a hero-level signal, not just nav text or an eyebrow. No headline should overpower the brand.
- Brand test: If the first viewport could belong to another brand after removing the nav, the branding is too weak.
- Typography: Use expressive, purposeful fonts and avoid default stacks (Inter, Roboto, Arial, system).
- Background: Don't rely on flat, single-color backgrounds; use gradients, images, or subtle patterns to build atmosphere.
- Full-bleed hero only: On landing pages and promotional surfaces, the hero image should be a dominant edge-to-edge visual plane or background by default. Do not use inset hero images, side-panel hero images, rounded media cards, tiled collages, or floating image blocks unless the existing design system clearly requires it.
- Hero budget: The first viewport should usually contain only the brand, one headline, one short supporting sentence, one CTA group, and one dominant image. Do not place stats, schedules, event listings, address blocks, promos, "this week" callouts, metadata rows, or secondary marketing content in the first viewport.
- No hero overlays: Do not place detached labels, floating badges, promo stickers, info chips, or callout boxes on top of hero media.
- Cards: Default: no cards. Never use cards in the hero. Cards are allowed only when they are the container for a user interaction. If removing a border, shadow, background, or radius does not hurt interaction or understanding, it should not be a card.
- One job per section: Each section should have one purpose, one headline, and usually one short supporting sentence.
- Real visual anchor: Imagery should show the product, place, atmosphere, or context. Decorative gradients and abstract backgrounds do not count as the main visual idea.
- Reduce clutter: Avoid pill clusters, stat strips, icon rows, boxed promos, schedule snippets, and multiple competing text blocks.
- Use motion to create presence and hierarchy, not noise. Ship at least 2-3 intentional motions for visually led work.
- Color & Look: Choose a clear visual direction; define CSS variables; avoid purple-on-white defaults. No purple bias or dark mode bias.
- Ensure the page loads properly on both desktop and mobile.
- For React code, prefer modern patterns including useEffectEvent, startTransition, and useDeferredValue when appropriate if used by the team. Do not add useMemo/useCallback by default unless already used; follow the repo's React Compiler guidance.

Exception: If working within an existing website or design system, preserve the established patterns, structure, and visual language.
```

## Techniques for better designs

### Start with design principles

Define constraints such as one H1 headline, no more than six sections, two typefaces maximum, one accent color, and one primary CTA above the fold.

### Provide visual references

Reference screenshots or mood boards help the model infer layout rhythm, typography scale, spacing systems, and imagery treatment. Below is an example of GPT-5.4 generating its own mood board for the user to review.



![Example mood board used to guide GPT-5.4 toward a cohesive visual direction](<https://cdn.openai.com/devhub/blog/codex_moodboard.png>)




  

    _
      Mood board created with GPT-5.4 in Codex inspired by NYC coffee culture
      and Y2K aesthetics
    _
  




### Structure the page as a narrative

Typical marketing page structure:

1. Hero — establish identity and promise
2. Supporting imagery — show context or environment
3. Product detail — explain the offering
4. Social proof — establish credibility
5. Final CTA — convert interest into action

### Instruct design system adherence

Encourage the model to establish a clear design system early in the build. Define core design tokens such as `background`, `surface`, `primary text`, `muted text`, and `accent`, along with typography roles like `display`, `headline`, `body`, and `caption`. This structure helps the model produce consistent, scalable UI patterns across the application.

For most web projects, starting with a familiar stack such as **React and Tailwind** works well. GPT-5.4 performs particularly strongly with these tools, making it easier to iterate quickly and reach polished results.

Motion and layered UI elements can introduce complexity, especially when fixed or floating components interact with primary content. When working with animations, overlays, or decorative layers, it helps to include guidance that encourages safe layout behavior. For example:

```
Keep fixed or floating UI elements from overlapping text, buttons, or other key content across screen sizes. Place them in safe areas, behind primary content where appropriate, and maintain sufficient spacing.
```

### Dial back the reasoning

For simpler websites, more reasoning is not always better. In practice, **low and medium reasoning levels often lead to stronger front-end results**, helping the model stay fast, focused, and less prone to overthinking, while still leaving headroom to turn reasoning up for more ambitious designs.

### Ground the design in real content

Providing the model with real copy, product context, or a clear project goal is one of the simplest ways to improve front-end results. That context helps it choose the right site structure, shape clearer section-level narratives, and write more believable messaging instead of falling back to generic placeholder patterns.

## Bringing it all together with the Frontend Skill

To help people get the most out of GPT-5.4 on general front-end tasks, we’ve also prepared a dedicated [`frontend-skill`](<https://github.com/openai/skills/tree/main/skills/.curated/frontend-skill>) you can find below. It gives the model stronger guidance on structure, taste, and interaction patterns, helping it produce more polished, intentional, and delightful designs out of the box.



```
---
name: frontend-skill
description: Use when the task asks for a visually strong landing page, website, app, prototype, demo, or game UI. This skill enforces restrained composition, image-led hierarchy, cohesive content structure, and tasteful motion while avoiding generic cards, weak branding, and UI clutter.
---

# Frontend skill

Use this skill when the quality of the work depends on art direction, hierarchy, restraint, imagery, and motion rather than component count.

Goal: ship interfaces that feel deliberate, premium, and current. Default toward award-level composition: one big idea, strong imagery, sparse copy, rigorous spacing, and a small number of memorable motions.

## Working Model

Before building, write three things:

- visual thesis: one sentence describing mood, material, and energy
- content plan: hero, support, detail, final CTA
- interaction thesis: 2-3 motion ideas that change the feel of the page

Each section gets one job, one dominant visual idea, and one primary takeaway or action.

## Beautiful Defaults

- Start with composition, not components.
- Prefer a full-bleed hero or full-canvas visual anchor.
- Make the brand or product name the loudest text.
- Keep copy short enough to scan in seconds.
- Use whitespace, alignment, scale, cropping, and contrast before adding chrome.
- Limit the system: two typefaces max, one accent color by default.
- Default to cardless layouts. Use sections, columns, dividers, lists, and media blocks instead.
- Treat the first viewport as a poster, not a document.

## Landing Pages

Default sequence:

1. Hero: brand or product, promise, CTA, and one dominant visual
2. Support: one concrete feature, offer, or proof point
3. Detail: atmosphere, workflow, product depth, or story
4. Final CTA: convert, start, visit, or contact

Hero rules:

- One composition only.
- Full-bleed image or dominant visual plane.
- Canonical full-bleed rule: on branded landing pages, the hero itself must run edge-to-edge with no inherited page gutters, framed container, or shared max-width; constrain only the inner text/action column.
- Brand first, headline second, body third, CTA fourth.
- No hero cards, stat strips, logo clouds, pill soup, or floating dashboards by default.
- Keep headlines to roughly 2-3 lines on desktop and readable in one glance on mobile.
- Keep the text column narrow and anchored to a calm area of the image.
- All text over imagery must maintain strong contrast and clear tap targets.

If the first viewport still works after removing the image, the image is too weak. If the brand disappears after hiding the nav, the hierarchy is too weak.

Viewport budget:

- If the first screen includes a sticky/fixed header, that header counts against the hero. The combined header + hero content must fit within the initial viewport at common desktop and mobile sizes.
- When using `100vh`/`100svh` heroes, subtract persistent UI chrome (`calc(100svh - header-height)`) or overlay the header instead of stacking it in normal flow.

## Apps

Default to Linear-style restraint:

- calm surface hierarchy
- strong typography and spacing
- few colors
- dense but readable information
- minimal chrome
- cards only when the card is the interaction

For app UI, organize around:

- primary workspace
- navigation
- secondary context or inspector
- one clear accent for action or state

Avoid:

- dashboard-card mosaics
- thick borders on every region
- decorative gradients behind routine product UI
- multiple competing accent colors
- ornamental icons that do not improve scanning

If a panel can become plain layout without losing meaning, remove the card treatment.

## Imagery

Imagery must do narrative work.

- Use at least one strong, real-looking image for brands, venues, editorial pages, and lifestyle products.
- Prefer in-situ photography over abstract gradients or fake 3D objects.
- Choose or crop images with a stable tonal area for text.
- Do not use images with embedded signage, logos, or typographic clutter fighting the UI.
- Do not generate images with built-in UI frames, splits, cards, or panels.
- If multiple moments are needed, use multiple images, not one collage.

The first viewport needs a real visual anchor. Decorative texture is not enough.

## Copy

- Write in product language, not design commentary.
- Let the headline carry the meaning.
- Supporting copy should usually be one short sentence.
- Cut repetition between sections.
- do not include prompt language or design commentary into the UI
- Give every section one responsibility: explain, prove, deepen, or convert.

If deleting 30 percent of the copy improves the page, keep deleting.

## Utility Copy For Product UI

When the work is a dashboard, app surface, admin tool, or operational workspace, default to utility copy over marketing copy.

- Prioritize orientation, status, and action over promise, mood, or brand voice.
- Start with the working surface itself: KPIs, charts, filters, tables, status, or task context. Do not introduce a hero section unless the user explicitly asks for one.
- Section headings should say what the area is or what the user can do there.
- Good: "Selected KPIs", "Plan status", "Search metrics", "Top segments", "Last sync".
- Avoid aspirational hero lines, metaphors, campaign-style language, and executive-summary banners on product surfaces unless specifically requested.
- Supporting text should explain scope, behavior, freshness, or decision value in one sentence.
- If a sentence could appear in a homepage hero or ad, rewrite it until it sounds like product UI.
- If a section does not help someone operate, monitor, or decide, remove it.
- Litmus check: if an operator scans only headings, labels, and numbers, can they understand the page immediately?

## Motion

Use motion to create presence and hierarchy, not noise.

Ship at least 2-3 intentional motions for visually led work:

- one entrance sequence in the hero
- one scroll-linked, sticky, or depth effect
- one hover, reveal, or layout transition that sharpens affordance

Prefer Framer Motion when available for:

- section reveals
- shared layout transitions
- scroll-linked opacity, translate, or scale shifts
- sticky storytelling
- carousels that advance narrative, not just fill space
- menus, drawers, and modal presence effects

Motion rules:

- noticeable in a quick recording
- smooth on mobile
- fast and restrained
- consistent across the page
- removed if ornamental only

## Hard Rules

- No cards by default.
- No hero cards by default.
- No boxed or center-column hero when the brief calls for full bleed.
- No more than one dominant idea per section.
- No section should need many tiny UI devices to explain itself.
- No headline should overpower the brand on branded pages.
- No filler copy.
- No split-screen hero unless text sits on a calm, unified side.
- No more than two typefaces without a clear reason.
- No more than one accent color unless the product already has a strong system.

## Reject These Failures

- Generic SaaS card grid as the first impression
- Beautiful image with weak brand presence
- Strong headline with no clear action
- Busy imagery behind text
- Sections that repeat the same mood statement
- Carousel with no narrative purpose
- App UI made of stacked cards instead of layout

## Litmus Checks

- Is the brand or product unmistakable in the first screen?
- Is there one strong visual anchor?
- Can the page be understood by scanning headlines only?
- Does each section have one job?
- Are cards actually necessary?
- Does motion improve hierarchy or atmosphere?
- Would the design still feel premium if all decorative shadows were removed?

```



Install the `frontend-skill` by running the following command inside the Codex app:

```
$skill-installer frontend-skill
```

Here are a few example websites generated with the help of the Frontend Design skill.

### Landing Pages

### Games

### Dashboards

## Key Takeaway

GPT-5.4 can generate high-quality front-end interfaces when prompts provide clear design constraints, visual references, structured narratives, and defined design systems.

We hope these techniques help you build more distinctive, well-designed apps.

If you want to share a project you’ve entirely generated with GPT-5.4 and a coding agent such as [Codex](<http://developers.openai.com/codex>), submit your app to be showcased in our [gallery](<http://developers.openai.com/showcase>).


</details>
