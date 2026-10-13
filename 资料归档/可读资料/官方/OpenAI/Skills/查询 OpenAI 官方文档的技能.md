# 查询 OpenAI 官方文档的技能

规定产品资料、Codex 手册和官网查询的来源优先级、检索步骤及引用要求，帮助核实能力和版本信息。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-06-17](<https://github.com/openai/skills/commit/972cb867affac58fda9afa76bb1a19b399a278cf>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.system/openai-docs/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/查询 OpenAI 官方文档的技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: "openai-docs"
description: "Use when the user asks how to build with OpenAI products or APIs, asks about Codex itself or choosing Codex surfaces, needs up-to-date official documentation with citations, help choosing the latest model for a use case, or model upgrade and prompt-upgrade guidance; use OpenAI docs MCP tools for non-Codex docs questions, use the Codex manual helper first for broad Codex self-knowledge, and restrict fallback browsing to official OpenAI domains."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# OpenAI 文档

使用developers.openai.com MCP 服务器从OpenAI 开发人员文档中提供权威的最新指导。 “文档MCP”表示`mcp__openaiDeveloperDocs__search_openai_docs`和`mcp__openaiDeveloperDocs__fetch_openai_doc`；对于 API 参考、架构、参数或必填字段问题，也可在可用时使用 `mcp__openaiDeveloperDocs__get_openapi_spec`。当这些工具不可用或无用时，官方域网络搜索是后备措施。广泛的 Codex 问题在文档 MCP 之前使用手动帮助程序。该技能还拥有模型选择、API模型迁移以及提示升级指导。

## API 按键设置

对于构建、运行、配置、调试或实现 API- 支持的应用程序、脚本、CLI、生成器或工具的请求，请首先使用 `openai-platform-api-key`（如果可用）。解决该凭证门之后，请根据需要返回此处获取当前文档。

直接将此技能用于仅文档问题、引文、model/API 指南、概念解释和示例，不需要构建或运行 API- 支持的工件。

## 工作流程配置

### 来源优先级

- 对于Codex自知之明，请使用下面的Codex源路由；它拥有何时使用手动帮助程序、文档 MCP 或有限的不确定性。
- 对于非 Codex OpenAI 文档问题，请使用 `mcp__openaiDeveloperDocs__search_openai_docs` 查找最相关的文档页面。
- 对于非 Codex OpenAI 文档问题，请在回答之前使用 `mcp__openaiDeveloperDocs__fetch_openai_doc` 获取相关页面。如果搜索有噪音，请运行更窄的文档 MCP 搜索；当已知或找到任何可信的官方 OpenAI 文档 URL 时，请在依赖网络搜索内容之前尝试通过文档 MCP 获取 URL。
- 对于 API 参考、架构、参数或必填字段问题，请在可用时使用 `mcp__openaiDeveloperDocs__get_openapi_spec` 来验证相关指南或参考页面旁边的 API 形状。
- 仅当您需要浏览或发现非Codex页面且没有明确查询时才使用`mcp__openaiDeveloperDocs__list_openai_docs`。
- 对于模型选择、“最新模型”或默认模型问题，请首先获取 `https://developers.openai.com/api/docs/guides/latest-model.md`。如果不可用，请加载 `references/latest-model.md`。
- 对于模型升级或提示升级，仅当目标为latest/current/default或其他未指定时运行`node scripts/resolve-latest-model-info.js`；否则保留明确请求的目标。
- 保留明确的目标请求：如果用户命名一个目标模型，如“迁移到 GPT-5.4”，则保留该请求的目标，即使 `latest-model.md` 命名一个较新的模型。仅将更新的指南作为可选内容提及。
- 如果需要当前远程指导，请直接获取返回的迁移和提示指导 URL。如果直接获取失败，则使用MCP/search后备；如果这也失败，请使用捆绑后备参考并公开后备。

## OpenAI 产品快照

1. 应用程序 SDK：通过提供 Web 组件 UI 和向 ChatGPT 公开应用程序工具的 MCP 服务器来构建 ChatGPT 应用程序。
2. 响应 API：专为代理工作流程中的状态、多模式、工具使用交互而设计的统一端点。
3. 聊天完成 API：从包含对话的消息列表生成模型响应。
4. Codex：OpenAI的软件开发编码agent，可以编写、理解、审查和调试代码。
5. gpt-oss：在 Apache 2.0 许可证下发布的开放权重 OpenAI 推理模型（gpt-oss-120b 和 gpt-oss-20b）。
6. 实时 API：构建低延迟、多模式体验，包括自然语音对话。
7. Agents SDK：用于构建代理应用程序的工具包，其中模型可以使用工具和上下文、移交给其他 agents、流式传输部分结果并保留完整跟踪。

## Codex 自知之明

使用此路径来解决有关 Codex 本身的问题：配置、扩展、操作、故障排除、本地状态、产品表面或 Codex 行为应存在的位置。代码库仅仅提及插件、技能、挂钩、MCP 服务器、浏览器或自动化是不够的。对于通用软件任务，直接回答软件任务；如果被问及 Codex 自我知识是否适用，请简要回答该元问题并继续请求的工件。

### 来源路线

Codex 手册是广泛 Codex 综合的第一个来源。将手册和文档 MCP 视为不同的通道，不可互换的官方文档源。对于已发布用户 Codex 产品答案，源路由是完整的：手册、文档 MCP（当此路由需要时）、官方 OpenAI Web 后备以及当问题涉及该功能时当前会话中出现的可调用功能。 Developers.openai.com 之外的知识库不属于公共产品答案的这条路线。

对于广泛的 Codex 行为、设置、自定义、技能、插件、MCP、挂钩、`AGENTS.md`、自动化、表面、本地状态或系统映射问题：

1. 当同一线程手册和大纲路径仍然新鲜时，重复使用它。
2. 否则，首先在正常的可写会话中运行技能本地帮助程序。仅当会话显式只读、shell 执行不可用或可见策略显示不允许的临时缓存时，才跳过它而不尝试。
3. 默认情况下，帮助程序按以下顺序选择第一个可用的临时缓存目录：`$TMPDIR/openai-docs-cache`、`%TEMP%\openai-docs-cache`、`%TMP%\openai-docs-cache`、`/private/tmp/openai-docs-cache`，然后是`/tmp/openai-docs-cache`。对于此临时缓存，仅工作区写入访问是不够的。
4. 除非您需要覆盖缓存目录，否则直接运行帮助程序。当本机 `fetch` 不可用或存在代理环境变量时，助手会回退到 `curl`，因此不需要特定于 shell 的代理前缀。将`<skill-dir>`解析为该技能的实际目录；在复制的本地 eval workdirs 中，这通常是 `.codex/skills/openai-docs`：

```bash
node <skill-dir>/scripts/fetch-codex-manual.mjs
```

如果需要覆盖缓存目录，请传递`--cache-dir <cache-dir>`。在 Windows 上，助手会自动检查 `%TEMP%` 和 `%TMP%`；在PowerShell中，`$env:TEMP\\openai-docs-cache`是典型的显式覆盖。

将助手可用性视为由显式 read-only/no-shell 策略或实际命令结果建立的。猜测的沙箱或猜测的帮助程序失败不足以切换到文档 MCP 或网络查找；在实际的帮助命令失败后，继续下面最窄的官方下一个来源。

助手验证新鲜度，写入 `codex-manual.md`，并发出 `codex-manual.outline.md`。大纲将源页面和标题映射到行范围；使用它选择相关手册部分，然后阅读或搜索目标手册部分以获取 Codex 产品信息。使用技能目录找到并运行助手；帮助程序成功后，使用返回的手册和大纲路径作为Codex产品事实和术语覆盖率检查的搜索范围。

重复使用同线程手册和大纲路径来解决后续 Codex 问题。当手册是在大约一天前获取的、路径不可用、路径来自另一个线程或不确定的出处、或者可能的当前信息丢失并且可能过时时，请首先刷新。

对于有关手册现在是否足够新以供依赖的问题，请在允许临时缓存时运行帮助程序，并根据其返回的状态、手册路径和大纲路径得出答案。

如果手册解决了 Codex 声明，请从中做出回答并停止扩展该声明的来源；如果文档查找只是一种依赖项，则继续用户更广泛的任务。手动源页面和已知锚点足以为手动覆盖的材料提供引用支持。

如果由于会话是只读的、没有 shell 执行或没有允许的临时缓存而跳过助手，则下一个来源是文档 MCP：在任何 Web 回退之前调用 `mcp__openaiDeveloperDocs__search_openai_docs`，然后调用 `mcp__openaiDeveloperDocs__fetch_openai_doc` 以获得相关命中。

如果用户命名了新手册中未使用的 Codex 术语或模式，请在手册中搜索明显相邻的概念，然后回答没有记录确切的术语，并使用最接近的记录术语。如果提示询问该术语如何映射到 Codex 行为，请解析相邻手册部分的映射。如果手动通过后确切的术语仍然重要或可能是最新的，请在有限的不确定性之前使用一份狭窄的文档 MCP search/fetch；否则，该术语或映射声明的源查找已完成。

仅当手册不可用、帮助程序失败、不允许临时缓存、其他材料声明丢失或可能过时，或者用户明确需要特定于页面的引用时，才使用最窄的官方下一个来源。首选特定文档 MCP 搜索，如果返回明确相关的页面，则进行一次提取；对于未解决的 Codex 功能名称、首字母缩略词、计划术语或确切的错误文本，此文档 MCP 步骤是 Web 搜索之前的下一个来源。在手册加上任何允许的文档 MCP 间隙填充后，将剩余间隙解决为有界不确定性。仅在文档 MCP 路径不可用或无帮助后才使用官方域 Web 后备。如果该主张仍未成立，则在不确定性有限的情况下停止。如果官方 docs/manual 与当前会话中已出现的可调用功能发生冲突，请说明冲突并首选针对该环境进行验证的当前会话行为。

对于未记录或看起来私人的模型块、产品模式标签、权利标签、帐户访问路径或推出名称，请从当前公共文档和有限的不确定性中得到答案。这些标签并不是离开公共源路线的理由。

对于支持式诊断，更喜欢手册中的逐层答案，而不是特定于提供商的 Web 查找：installed/enabled 插件、捆绑应用程序或连接器授权、MCP 设置、workspace/admin 策略、重新启动或新线程期望，然后如果仍未解决，则提供支持或反馈。

如果源路线仍然无法建立声明，请将有限不确定性或路线返回给支持、管理员或产品反馈，而不是扩大调查范围。

对于未解决的产品术语，请从手册以及允许的官方下一个来源中进行回答。如果这些来源没有建立该术语，请以这些来源的有限不确定性来回答。

### 表面图

当 Codex 名词或持久指令表面重叠时，推荐与范围匹配的最小表面：

- 提示或线程上下文 -> 一次性任务约束。
- `AGENTS.md` -> 持久的回购约定、命令、验证步骤和审查期望；更紧密的嵌套文件适用于其子树下。
- 项目 `.codex/config.toml` -> 可信存储库 Codex 设置，例如沙箱、MCP、挂钩、模型或推理默认值。
- 全局配置或全局指导 -> 跨存储库的个人默认值。
- 技能 -> 带参考或脚本的可重用任务工作流程。
- 插件 -> 可安装的捆绑包，包含技能以及命令、工具、MCP 配置、挂钩、资产、应用程序或市场元数据。
- MCP 服务器或应用程序连接器 -> 实时外部 data/actions 或授权私有 app/workspace 数据。使用私人 Google 文档、日历、Slack、GitHub、Notion 和类似数据的连接器，而不是 Web 搜索或模型内存。
- 自动化->定期检查、提醒、监控或后续工作；当现有线程的连续性很重要时，请使用线程心跳。
- 挂钩 -> 围绕工具调用、命令或文件编辑的生命周期强制执行。

拆分混合范围的请求而不是强制一个答案。示例：“始终执行 X，但仅限于此 PR”默认为当前运行的 prompt/thread 上下文；仅当需要持续存在时才使用 `AGENTS.md` 或项目配置，仅用于机械执行的挂钩，仅用于计划或后续工作的自动化。

需要时使用此快速产品图：CLI 是终端优先的本地存储库工作； IDE扩展名是编辑附加编码； Codexapp是桌面规划、审阅、互动工作； cloud/web主持parallel/offloaded工作；浏览器Use/in-app浏览器是Codex控制的网页测试； Chrome 扩展程序使用用户的 Chrome 个人资料；计算机使用控制桌面应用程序和 OS UI。将 `config.toml` 默认值、`requirements.toml` 约束和 managed/admin 策略分开。

### 边界和输出

- API 密钥身份验证并不意味着 ChatGPT、云任务或连接器访问。对于 plugin/app/auth 故障，请在回答之前检查捆绑包可用性、插件 installed/enabled 状态、connector/app 授权、MCP 设置、restart/refresh 期望、工作区策略和每个表面的可用性。
- 沙箱或网络拒绝需要在有明确理由的情况下进行范围升级。破坏性命令、工作区外写入或广泛的访问权限更改需要明确批准。
- 记忆可以提供用户偏好或上下文，但明确的提示指令获胜，并且记忆不是当前外部事实的来源。
- 对于肯定的表面选择答案，请使用以下形状：建议、原因、要避免的内容以及使用的 manual/source 证据。
- 当实际需要特定页面的 Codex 引用时，这些锚点通常适合：`concepts/customization#agents-guidance` 用于 `AGENTS.md`，`concepts/customization#skills` 用于技能，`plugins/build#plugin-structure` 用于插件，`concepts/customization#mcp` 用于 MCP， `config-advanced#hooks` 用于钩子，`app/automations#thread-automations` 用于线程自动化，`config-reference#configtoml` 用于配置。

## 如果MCP服务器丢失

如果 MCP 工具失败或没有 OpenAI 文档资源可用：

1. 自己运行安装命令：`codex mcp add openaiDeveloperDocs --url https://developers.openai.com/mcp`
2. 如果由于 permissions/sandboxing 导致失败，请立即使用升级的权限重试相同的命令，并包含 1 句子理由以供批准。
3. 仅当升级尝试失败时才要求用户运行安装命令。
4. 要求用户重新启动Codex。
5. 重新启动后重新运行文档 search/fetch。

## 工作流程

1. 澄清请求是否是一般文档查找、模型选择、模型字符串升级、提示升级指导或更广泛的 API/provider 迁移。
2. 对于 Codex 自知识请求，请遵循上面的 Codex 自知识源过程。
3. 对于模型选择或升级请求，当用户请求 latest/current/default 指导时，优先选择当前的远程文档而不是捆绑的参考。
   - 获取`https://developers.openai.com/api/docs/guides/latest-model.md`。
   - 查找最新型号 ID 和明确的迁移或提示指导链接。
   - 优先选择来自最新模型页面的显式链接，而不是派生的 URL。
   - 对于显式命名模型请求，保留请求的模型目标。仅将更新的远程指导作为可选内容提及。
   - 对于动态 latest/current/default 升级，请运行 `node scripts/resolve-latest-model-info.js`，然后在可能的情况下直接获取两个返回的指南 URL。
   - 如果直接获取指南失败，请使用开发人员文档 MCP 工具或官方 OpenAI 域搜索来查找相同的指南内容。
   - 如果远程文档不可用，请使用捆绑的后备参考并说明已使用后备指南。
4. 对于模型升级，请缩小更改范围：仅在安全时更新活动的 OpenAI API 模型默认值和直接相关提示。
5. 保留历史文档、示例、评估基线、固定装置、提供商比较、提供商注册表、定价表、别名默认值、低成本后备路径和不明确的旧模型使用不变，除非用户明确要求升级它们。
6. 将 SDK、工具、IDE、插件、shell、身份验证和提供者环境迁移保留在模型和提示升级之外，除非用户明确要求它们。
7. 如果升级需要API-表面更改、架构重新布线、工具处理程序更改或超出字面模型字符串替换和提示编辑的实现工作，请将其报告为已阻止或需要确认。
8. 对于一般文档查找，请从 2-6 基本术语的紧凑、类似标题的搜索查询开始。不要将完整的用户问题变成关键字列表。获取所需的最佳页面和确切部分，并用简洁的引文进行回答。

## 参考图

只阅读您需要的内容：

- `https://developers.openai.com/api/docs/guides/latest-model.md` -> 当前模型选择和“best/latest/current 模型”问题。
- `scripts/fetch-codex-manual.mjs` -> 当前 Codex 手动获取、验证、本地临时缓存和大纲生成。
- `https://developers.openai.com/codex/codex-manual.md` -> 当前的Codex自我知识综合，包括设置、定制、技能、插件、MCP、挂钩、`AGENTS.md`、自动化和表面行为；通常通过辅助路径访问它，并且当临时缓存可用时读取目标文件。
- `references/latest-model.md` -> 模型选择和“best/latest/current 模型”问题的捆绑后备。
- `references/upgrade-guide.md` -> 模型升级和升级规划请求的捆绑后备。
- `references/prompting-guide.md` -> 提示重写和提示行为升级的捆绑后备。

## 质量规则

- 将 OpenAI 文档视为事实来源；避免猜测。
- 对于Codex自我认知，请遵循上面的来源路线，而不是依赖于记住的行为。
- 保持迁移变化范围较小并保留行为。
- 尽可能选择仅提示升级。
- 避免发明定价、可用性、参数、API 更改或重大更改。
- 保持报价简短并在政策限制内；更喜欢用引文解释。
- 如果多个页面存在差异，请指出差异并引用两者。
- 如果官方文档和经过验证的可调用当前会话行为不一致，请在进行广泛声明或编辑之前说明冲突。
- 如果文档不能满足用户的需求，请说明并提供后续步骤。

## 工装注意事项

- 在网络搜索 OpenAI 相关 Markdown 文档之前，请使用 MCP 文档工具。 Codex 手动流程是例外：遵循 Codex 自知源程序进行广泛的 Codex 综合。
- 如果安装了 MCP 服务器但未返回任何有意义的结果，则使用 Web 搜索作为后备。
- 当回到网络搜索时，仅限于官方 OpenAI 域（developers.openai.com、platform.openai.com）并引用来源。


## 原文对照

<details>
<summary>展开完整原文</summary>

# OpenAI Docs

Provide authoritative, current guidance from OpenAI developer docs using the developers.openai.com MCP server. "Docs MCP" means `mcp__openaiDeveloperDocs__search_openai_docs` and `mcp__openaiDeveloperDocs__fetch_openai_doc`; for API reference, schema, parameter, or required-field questions, also use `mcp__openaiDeveloperDocs__get_openapi_spec` when available. Official-domain web search is fallback after those tools are unavailable or unhelpful. Broad Codex questions use the manual helper before Docs MCP. This skill also owns model selection, API model migration, and prompt-upgrade guidance.

## API Key Setup

For requests to build, run, configure, debug, or implement an API-backed app, script, CLI, generator, or tool, use `openai-platform-api-key` first when available. After that credential gate is resolved, return here for current docs as needed.

Use this skill directly for docs-only questions, citations, model/API guidance, conceptual explanations, and examples that do not require building or running an API-backed artifact.

## Workflow Configuration

### Source Priority

- For Codex self-knowledge, use the Codex source route below; it owns when to use the manual helper, Docs MCP, or bounded uncertainty.
- For non-Codex OpenAI docs questions, use `mcp__openaiDeveloperDocs__search_openai_docs` to find the most relevant doc pages.
- For non-Codex OpenAI docs questions, fetch the relevant page with `mcp__openaiDeveloperDocs__fetch_openai_doc` before answering. If search is noisy, run a narrower Docs MCP search; when any plausible official OpenAI docs URL is known or found, try fetching that URL through Docs MCP before relying on web-search content.
- For API reference, schema, parameter, or required-field questions, use `mcp__openaiDeveloperDocs__get_openapi_spec` when available to verify the API shape alongside the relevant guide or reference page.
- Use `mcp__openaiDeveloperDocs__list_openai_docs` only when you need to browse or discover non-Codex pages without a clear query.
- For model-selection, "latest model", or default-model questions, fetch `https://developers.openai.com/api/docs/guides/latest-model.md` first. If that is unavailable, load `references/latest-model.md`.
- For model upgrades or prompt upgrades, run `node scripts/resolve-latest-model-info.js` only when the target is latest/current/default or otherwise unspecified; otherwise preserve the explicitly requested target.
- Preserve explicit target requests: if the user names a target model like "migrate to GPT-5.4", keep that requested target even if `latest-model.md` names a newer model. Mention newer guidance only as optional.
- If current remote guidance is needed, fetch both the returned migration and prompting guide URLs directly. If direct fetch fails, use MCP/search fallback; if that also fails, use bundled fallback references and disclose the fallback.

## OpenAI product snapshots

1. Apps SDK: Build ChatGPT apps by providing a web component UI and an MCP server that exposes your app's tools to ChatGPT.
2. Responses API: A unified endpoint designed for stateful, multimodal, tool-using interactions in agentic workflows.
3. Chat Completions API: Generate a model response from a list of messages comprising a conversation.
4. Codex: OpenAI's coding agent for software development that can write, understand, review, and debug code.
5. gpt-oss: Open-weight OpenAI reasoning models (gpt-oss-120b and gpt-oss-20b) released under the Apache 2.0 license.
6. Realtime API: Build low-latency, multimodal experiences including natural speech-to-speech conversations.
7. Agents SDK: A toolkit for building agentic apps where a model can use tools and context, hand off to other agents, stream partial results, and keep a full trace.

## Codex self-knowledge

Use this path for questions about Codex itself: configuring, extending, operating, troubleshooting, local state, product surfaces, or where Codex behavior should live. A codebase merely mentioning a plugin, skill, hook, MCP server, browser, or automation is not enough. For generic software tasks, answer the software task directly; if asked whether Codex self-knowledge applies, answer that meta question briefly and continue the requested artifact.

### Source Route

The Codex manual is the first source for broad Codex synthesis. Treat the manual and Docs MCP as different lanes, not interchangeable official-doc sources. For published-user Codex product answers, the source route is complete: the manual, Docs MCP when this route calls for it, official OpenAI web fallback, and callable capabilities surfaced in the current session when the question is about that capability. Knowledge bases outside developers.openai.com are outside this route for public product answers.

For broad Codex behavior, setup, customization, skills, plugins, MCP, hooks, `AGENTS.md`, automations, surfaces, local state, or system-map questions:

1. Reuse a same-thread manual and outline path when it is still fresh.
2. Otherwise run the skill-local helper first in normal writable sessions. Skip it without trying only when the session is explicitly read-only, shell execution is unavailable, or visible policy shows no allowed temp cache.
3. By default, the helper chooses the first usable temp cache dir in this order: `$TMPDIR/openai-docs-cache`, `%TEMP%\openai-docs-cache`, `%TMP%\openai-docs-cache`, `/private/tmp/openai-docs-cache`, then `/tmp/openai-docs-cache`. Workspace-only write access is not enough for this temp cache.
4. Run the helper directly unless you need to override the cache dir. The helper falls back to `curl` when native `fetch` is unavailable or when proxy env vars are present, so no shell-specific proxy prefix is required. Resolve `<skill-dir>` to this skill's actual directory; in copied local eval workdirs this is usually `.codex/skills/openai-docs`:

```bash
node <skill-dir>/scripts/fetch-codex-manual.mjs
```

If you need to override the cache dir, pass `--cache-dir <cache-dir>`. On Windows, the helper checks `%TEMP%` and `%TMP%` automatically; in PowerShell, `$env:TEMP\\openai-docs-cache` is a typical explicit override.

Treat helper availability as established by explicit read-only/no-shell policy or an actual command result. A guessed sandbox or guessed helper failure is not enough to switch to Docs MCP or web lookup; after an actual helper command failure, continue to the narrowest official next source below.

The helper verifies freshness, writes `codex-manual.md`, and emits `codex-manual.outline.md`. The outline maps source pages and headings to line ranges; use it to choose the relevant manual section, then read or search targeted manual sections for Codex product facts. Use the skill directory to locate and run the helper; after the helper succeeds, use the returned manual and outline paths as the search scope for Codex product facts and term coverage checks.

Reuse the same-thread manual and outline paths for follow-up Codex questions. Refresh first when the manual was fetched more than about a day ago, the path is unusable, the path came from another thread or uncertain provenance, or likely-current information is missing and staleness is plausible.

For questions about whether the manual is current enough to rely on now, run the helper when temp caching is allowed and base the answer on its returned status, manual path, and outline path.

If the manual resolves a Codex claim, answer from it and stop expanding sources for that claim; continue the user's broader task if the docs lookup was only one dependency. Manual source pages and known anchors are enough citation support for manual-covered material.

If the helper is skipped because the session is read-only, has no shell execution, or has no allowed temp cache, the next source is Docs MCP: call `mcp__openaiDeveloperDocs__search_openai_docs`, then `mcp__openaiDeveloperDocs__fetch_openai_doc` for a relevant hit before any web fallback.

If a user names a Codex term or mode that a fresh manual does not use, search the manual for obvious adjacent concepts, then answer that the exact term is not documented and use the closest documented terminology. If the prompt asks how that term maps to Codex behavior, resolve the mapping from adjacent manual sections. If the exact term remains material or likely current after that manual pass, use one narrow Docs MCP search/fetch before bounded uncertainty; otherwise, the source lookup for that terminology or mapping claim is complete.

Use the narrowest official next source only when the manual is unavailable, the helper fails, temp caching is not allowed, another material claim is missing or likely stale, or the user explicitly needs a page-specific citation. Prefer one specific Docs MCP search and, if it returns a clearly relevant page, one fetch; for unresolved Codex capability names, acronyms, scheduling terms, or exact error text, this Docs MCP step is the next source before web search. After the manual plus any permitted Docs MCP gap-fill, resolve remaining gaps as bounded uncertainty. Use official-domain web fallback only after that Docs MCP path is unavailable or unhelpful. If the claim is still not established, stop with bounded uncertainty. If official docs/manual conflict with a callable capability already surfaced in the current session, state the conflict and prefer verified current-session behavior for that environment.

For undocumented or private-looking model slugs, product mode labels, entitlement labels, account access paths, or rollout names, answer from current public docs and bounded uncertainty. Those labels are not a reason to leave the public source route.

For support-style diagnostics, prefer a layer-by-layer answer from the manual over provider-specific web lookups: installed/enabled plugin, bundled app or connector authorization, MCP setup, workspace/admin policy, restart or new-thread expectations, then support or feedback if still unresolved.

If the source route still does not establish a claim, return bounded uncertainty or route to support, an admin, or product feedback instead of widening the investigation.

For unresolved product terminology, answer from the manual plus the allowed official next source. If those sources do not establish the term, answer with bounded uncertainty from those sources.

### Surface Map

When Codex nouns or durable-instruction surfaces overlap, recommend the smallest surface that matches the scope:

- Prompt or thread context -> one-off task constraints.
- `AGENTS.md` -> durable repo conventions, commands, verification steps, and review expectations; closer nested files apply under their subtree.
- Project `.codex/config.toml` -> trusted-repo Codex settings such as sandbox, MCP, hooks, model, or reasoning defaults.
- Global config or global guidance -> personal defaults across repos.
- Skill -> reusable task workflow with references or scripts.
- Plugin -> installable bundle with skills plus commands, tools, MCP config, hooks, assets, apps, or marketplace metadata.
- MCP server or app connector -> live external data/actions or authorized private app/workspace data. Use connectors for private Google Docs, Calendar, Slack, GitHub, Notion, and similar data instead of web search or model memory.
- Automation -> scheduled checks, reminders, monitors, or follow-up work; use a thread heartbeat when continuity in an existing thread matters.
- Hook -> lifecycle enforcement around tool calls, commands, or file edits.

Split mixed-scope requests instead of forcing one answer. Example: "always do X, but only for this PR" defaults to prompt/thread context for the current run; use `AGENTS.md` or project config only if it should persist, hooks only for mechanical enforcement, and automations only for scheduled or follow-up work.

Use this quick product map when needed: CLI is terminal-first local repo work; IDE extension is editor-attached coding; Codex app is desktop planning, review, and interactive work; cloud/web is hosted parallel/offloaded work; Browser Use/in-app browser is Codex-controlled web testing; Chrome extension uses the user's Chrome profile; Computer Use controls desktop apps and OS UI. Keep `config.toml` defaults, `requirements.toml` constraints, and managed/admin policy separate.

### Boundaries And Output

- API key auth does not imply ChatGPT, cloud task, or connector access. For plugin/app/auth failures, check bundle availability, plugin installed/enabled state, connector/app authorization, MCP setup, restart/refresh expectations, workspace policy, and per-surface availability before answering.
- Sandbox or network denials need scoped escalation with a clear justification. Destructive commands, writes outside the workspace, or broad access changes require explicit approval.
- Memory can provide user preference or context, but explicit prompt instructions win and memory is not a source for current external facts.
- For affirmative surface-selection answers, use this shape: recommendation, why, what to avoid, and the manual/source evidence used.
- When page-specific Codex citations are actually needed, these anchors often fit: `concepts/customization#agents-guidance` for `AGENTS.md`, `concepts/customization#skills` for skills, `plugins/build#plugin-structure` for plugins, `concepts/customization#mcp` for MCP, `config-advanced#hooks` for hooks, `app/automations#thread-automations` for thread automations, and `config-reference#configtoml` for config.

## If MCP server is missing

If MCP tools fail or no OpenAI docs resources are available:

1. Run the install command yourself: `codex mcp add openaiDeveloperDocs --url https://developers.openai.com/mcp`
2. If it fails due to permissions/sandboxing, immediately retry the same command with escalated permissions and include a 1-sentence justification for approval.
3. Ask the user to run the install command only if the escalated attempt fails.
4. Ask the user to restart Codex.
5. Re-run the doc search/fetch after restart.

## Workflow

1. Clarify whether the request is general docs lookup, model selection, a model-string upgrade, prompt-upgrade guidance, or broader API/provider migration.
2. For Codex self-knowledge requests, follow the Codex self-knowledge source procedure above.
3. For model-selection or upgrade requests, prefer current remote docs over bundled references when the user asks for latest/current/default guidance.
   - Fetch `https://developers.openai.com/api/docs/guides/latest-model.md`.
   - Find the latest model ID and explicit migration or prompt-guidance links.
   - Prefer explicit links from the latest-model page over derived URLs.
   - For explicit named-model requests, preserve the requested model target. Mention newer remote guidance only as optional.
   - For dynamic latest/current/default upgrades, run `node scripts/resolve-latest-model-info.js`, then fetch both returned guide URLs directly when possible.
   - If direct guide fetch fails, use the developer-docs MCP tools or official OpenAI-domain search to find the same guide content.
   - If remote docs are unavailable, use bundled fallback references and say that fallback guidance was used.
4. For model upgrades, keep changes narrow: update active OpenAI API model defaults and directly related prompts only when safe.
5. Leave historical docs, examples, eval baselines, fixtures, provider comparisons, provider registries, pricing tables, alias defaults, low-cost fallback paths, and ambiguous older model usage unchanged unless the user explicitly asks to upgrade them.
6. Keep SDK, tooling, IDE, plugin, shell, auth, and provider-environment migrations out of a model-and-prompt upgrade unless the user explicitly asks for them.
7. If an upgrade needs API-surface changes, schema rewiring, tool-handler changes, or implementation work beyond a literal model-string replacement and prompt edits, report it as blocked or confirmation-needed.
8. For general docs lookup, start with a compact, title-like search query of 2-6 essential terms. Do not turn the full user question into a keyword list. Fetch the best page and exact section needed, and answer with concise citations.

## Reference map

Read only what you need:

- `https://developers.openai.com/api/docs/guides/latest-model.md` -> current model-selection and "best/latest/current model" questions.
- `scripts/fetch-codex-manual.mjs` -> current Codex manual fetch, verification, local temp cache, and outline generation.
- `https://developers.openai.com/codex/codex-manual.md` -> current Codex self-knowledge synthesis, including setup, customization, skills, plugins, MCP, hooks, `AGENTS.md`, automations, and surface behavior; normally access it through the helper path and targeted file reads when temp caching is available.
- `references/latest-model.md` -> bundled fallback for model-selection and "best/latest/current model" questions.
- `references/upgrade-guide.md` -> bundled fallback for model upgrade and upgrade-planning requests.
- `references/prompting-guide.md` -> bundled fallback for prompt rewrites and prompt-behavior upgrades.

## Quality rules

- Treat OpenAI docs as the source of truth; avoid speculation.
- For Codex self-knowledge, follow the source route above instead of relying on remembered behavior.
- Keep migration changes narrow and behavior-preserving.
- Prefer prompt-only upgrades when possible.
- Avoid inventing pricing, availability, parameters, API changes, or breaking changes.
- Keep quotes short and within policy limits; prefer paraphrase with citations.
- If multiple pages differ, call out the difference and cite both.
- If official docs and verified callable current-session behavior disagree, state the conflict before making broad claims or edits.
- If docs do not cover the user’s need, say so and offer next steps.

## Tooling notes

- Use MCP doc tools before web search for OpenAI-related markdown docs. The Codex manual flow is the exception: follow the Codex self-knowledge source procedure for broad Codex synthesis.
- If the MCP server is installed but returns no meaningful results, then use web search as a fallback.
- When falling back to web search, restrict to official OpenAI domains (developers.openai.com, platform.openai.com) and cite sources.


</details>
