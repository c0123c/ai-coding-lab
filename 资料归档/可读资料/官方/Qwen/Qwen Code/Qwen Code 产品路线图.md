# Qwen Code 产品路线图

按已完成、待开发及待讨论功能记录规划；属于归档时的计划，不能据此认定功能已实现。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/QwenLM/qwen-code/commit/eb383fd3bbd4b55f1c5ba8d7da7048193f018230>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/developers/roadmap.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code 产品路线图.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Qwen Code RoadMap

> **目标**：追赶Claude Code的产品功能，不断完善细节，提升用户体验。

|类别|相1|相2|
| ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|用户体验|✅ 终端UI<br>✅ 支持OpenAI协议<br>✅ 设置<br>✅ OAuth<br>✅ 缓存控制<br>✅ 内存<br>✅ 压缩<br>✅主题|更好的UI<br>OnBoarding<br>LogView<br>✅会话<br>权限<br>🔄跨平台兼容性<br>✅编码计划<br>✅Anthropic提供商<br>✅多模式输入<br>✅统一Web Shell|
|编码工作流程|✅ 斜线命令<br>✅ MCP<br>✅ PlanMode<br>✅ TodoWrite<br>✅ SubAgent<br>✅ 多模型<br>✅ 聊天管理<br>✅ 工具 (WebFetch,重击，TextSearch，FileReadFile，EditFile）|🔄 钩子<br>✅ 技能<br>✅ 无头模式<br>✅ 工具（WebSearch）<br>✅ LSP 支持<br>✅ 并发运行|
|构建开放能力|✅ 自定义命令|✅ QwenCode SDK<br>✅ 扩展系统|
|整合社区生态系统|                                                                                                                                                                                    |✅ VSCode 插件<br>✅ ACP/Zed<br>✅ GHA|
|行政能力|✅ 统计数据<br>✅ 反馈|费用<br>仪表板<br>✅ 用户反馈对话框|

> 更多详情，请参阅下面的列表。

## 特点

#### 已完成的功能

|特点|版本|描述|类别|阶段|
| ----------------------- | --------- | ------------------------------------------------------- | ------------------------------- | ----- |
|**编码计划**| `V0.10.0` |阿里云编码计划认证和模型|用户体验| 2     |
|统一Web Shell| `V0.9.0`  |VS Code 和 `qwen serve` 的共享浏览器 UI|用户体验| 2     |
|导出聊天记录| `V0.8.0`  |将会话导出到 Markdown/HTML/JSON/JSONL|用户体验| 2     |
|扩展系统| `V0.8.0`  |使用斜杠命令进行完整的扩展管理|构建开放能力| 2     |
|LSP 支持| `V0.7.0`  |实验性 LSP 服务 (`--experimental-lsp`)|编码工作流程| 2     |
|Anthropic 提供商| `V0.7.0`  |Anthropic API 提供商支持|用户体验| 2     |
|用户反馈对话框| `V0.7.0`  |具有疲劳机制的应用内反馈收集|行政能力| 2     |
|并发运行者| `V0.6.0`  |通过 Git 集成批量执行 CLI|编码工作流程| 2     |
|多模态输入| `V0.6.0`  |图片、PDF、音频、视频输入支持|用户体验| 2     |
|技能| `V0.6.0`  |可扩展自定义【AI】技能（实验）|编码工作流程| 2     |
|GitHub 行动| `V0.5.0`  |qwen-代码操作和自动化|整合社区生态系统| 1     |
|VSCode 插件| `V0.5.0`  |VSCode 扩展插件|整合社区生态系统| 1     |
| QwenCode SDK            | `V0.4.0`  |打开【SDK】进行第三方集成|构建开放能力| 1     |
|会议| `V0.4.0`  |增强的会话管理|用户体验| 1     |
| i18n                    | `V0.3.0`  |国际化和多语言支持|用户体验| 1     |
|无头模式| `V0.3.0`  |无头模式（非交互）|编码工作流程| 1     |
| ACP/Zed                 | `V0.2.0`  |ACP 与 Zed 编辑器集成|整合社区生态系统| 1     |
|终端UI| `V0.1.0+` |交互式终端用户界面|用户体验| 1     |
|设置| `V0.1.0+` |配置管理系统|用户体验| 1     |
|主题| `V0.1.0+` |多主题支持|用户体验| 1     |
|支持【OpenAI】协议| `V0.1.0+` |支持OpenAIAPI协议|用户体验| 1     |
|聊天管理| `V0.1.0+` |会话管理（保存、恢复、浏览）|编码工作流程| 1     |
| MCP                     | `V0.1.0+` |Model Context Protocol 集成|编码工作流程| 1     |
|多型号| `V0.1.0+` |多型号支持及切换|编码工作流程| 1     |
|斜线命令| `V0.1.0+` |斜线命令系统|编码工作流程| 1     |
|工具：重击| `V0.1.0+` |Shell命令执行工具（带is_background参数）|编码工作流程| 1     |
|工具：FileRead/EditFile| `V0.1.0+` |文件read/write和编辑工具|编码工作流程| 1     |
|自定义命令| `V0.1.0+` |自定义命令加载|构建开放能力| 1     |
|反馈意见| `V0.1.0+` |反馈机制（/bug命令）|行政能力| 1     |
|统计数据| `V0.1.0+` |使用统计和配额显示|行政能力| 1     |
|内存| `V0.0.9+` |项目级和全局内存管理|用户体验| 1     |
|缓存控制| `V0.0.9+` |提示缓存控制（Anthropic、DashScope）|用户体验| 1     |
| PlanMode                | `V0.0.14` |任务规划模式|编码工作流程| 1     |
|压缩| `V0.0.11` |聊天压缩机制|用户体验| 1     |
| SubAgent                | `V0.0.11` |专用子agent系统|编码工作流程| 1     |
| TodoWrite               | `V0.0.10` |任务管理和进度跟踪|编码工作流程| 1     |
|工具：TextSearch| `V0.0.8+` |文本搜索工具（grep，支持.qwenignore）|编码工作流程| 1     |
|工具：WebFetch| `V0.0.7+` |网页内容获取工具|编码工作流程| 1     |
|工具：WebSearch| `V0.0.7+` |内置网络搜索（DashScope 响应 API）|编码工作流程| 1     |
|开放认证| `V0.0.5+` |OAuth登录认证（Qwen OAuth）|用户体验| 1     |

#### 需要开发的功能

|特点|优先级|状态|描述|类别|
| ---------------------------- | -------- | ----------- | --------------------------------- | --------------------------- |
|更好UI| P1       |计划中|优化终端【UI】交互|用户体验|
| OnBoarding                   | P1       |计划中|新用户入职流程|用户体验|
|许可| P1       |计划中|权限系统优化|用户体验|
|跨平台兼容性| P1       |进行中|Windows/Linux/macOS 兼容性|用户体验|
| LogView                      | P2       |计划中|日志查看和调试功能|用户体验|
|挂钩| P2       |进行中|延长钩系统|编码工作流程|
|成本| P2       |计划中|成本跟踪和分析|行政能力|
|仪表板| P2       |计划中|管理仪表板|行政能力|

#### 值得讨论的特色

|特点|状态|描述|
| ---------------- | -------- | ----------------------------------------------------- |
|主页聚焦|研究|项目发现和快速启动|
|竞技模式|研究|竞技模式|
|脉冲|研究|用户活动脉冲分析（OpenAI脉冲参考）|
|代码维基|研究|项目代码库 wiki/documentation 系统|


## 原文对照

<details>
<summary>展开完整原文</summary>

# Qwen Code RoadMap

> **Objective**: Catch up with Claude Code's product functionality, continuously refine details, and enhance user experience.

| Category                        | Phase 1                                                                                                                                                                            | Phase 2                                                                                                                                                                                     |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| User Experience                 | ✅ Terminal UI<br>✅ Support OpenAI Protocol<br>✅ Settings<br>✅ OAuth<br>✅ Cache Control<br>✅ Memory<br>✅ Compress<br>✅ Theme                                                | Better UI<br>OnBoarding<br>LogView<br>✅ Session<br>Permission<br>🔄 Cross-platform Compatibility<br>✅ Coding Plan<br>✅ Anthropic Provider<br>✅ Multimodal Input<br>✅ Unified Web Shell |
| Coding Workflow                 | ✅ Slash Commands<br>✅ MCP<br>✅ PlanMode<br>✅ TodoWrite<br>✅ SubAgent<br>✅ Multi Model<br>✅ Chat Management<br>✅ Tools (WebFetch, Bash, TextSearch, FileReadFile, EditFile) | 🔄 Hooks<br>✅ Skill<br>✅ Headless Mode<br>✅ Tools (WebSearch)<br>✅ LSP Support<br>✅ Concurrent Runner                                                                                  |
| Building Open Capabilities      | ✅ Custom Commands                                                                                                                                                                 | ✅ QwenCode SDK<br>✅ Extension System                                                                                                                                                      |
| Integrating Community Ecosystem |                                                                                                                                                                                    | ✅ VSCode Plugin<br>✅ ACP/Zed<br>✅ GHA                                                                                                                                                    |
| Administrative Capabilities     | ✅ Stats<br>✅ Feedback                                                                                                                                                            | Costs<br>Dashboard<br>✅ User Feedback Dialog                                                                                                                                               |

> For more details, please see the list below.

## Features

#### Completed Features

| Feature                 | Version   | Description                                             | Category                        | Phase |
| ----------------------- | --------- | ------------------------------------------------------- | ------------------------------- | ----- |
| **Coding Plan**         | `V0.10.0` | Alibaba Cloud Coding Plan authentication & models       | User Experience                 | 2     |
| Unified Web Shell       | `V0.9.0`  | Shared browser UI for VS Code and `qwen serve`          | User Experience                 | 2     |
| Export Chat             | `V0.8.0`  | Export sessions to Markdown/HTML/JSON/JSONL             | User Experience                 | 2     |
| Extension System        | `V0.8.0`  | Full extension management with slash commands           | Building Open Capabilities      | 2     |
| LSP Support             | `V0.7.0`  | Experimental LSP service (`--experimental-lsp`)         | Coding Workflow                 | 2     |
| Anthropic Provider      | `V0.7.0`  | Anthropic API provider support                          | User Experience                 | 2     |
| User Feedback Dialog    | `V0.7.0`  | In-app feedback collection with fatigue mechanism       | Administrative Capabilities     | 2     |
| Concurrent Runner       | `V0.6.0`  | Batch CLI execution with Git integration                | Coding Workflow                 | 2     |
| Multimodal Input        | `V0.6.0`  | Image, PDF, audio, video input support                  | User Experience                 | 2     |
| Skill                   | `V0.6.0`  | Extensible custom AI skills (experimental)              | Coding Workflow                 | 2     |
| GitHub Actions          | `V0.5.0`  | qwen-code-action and automation                         | Integrating Community Ecosystem | 1     |
| VSCode Plugin           | `V0.5.0`  | VSCode extension plugin                                 | Integrating Community Ecosystem | 1     |
| QwenCode SDK            | `V0.4.0`  | Open SDK for third-party integration                    | Building Open Capabilities      | 1     |
| Session                 | `V0.4.0`  | Enhanced session management                             | User Experience                 | 1     |
| i18n                    | `V0.3.0`  | Internationalization and multilingual support           | User Experience                 | 1     |
| Headless Mode           | `V0.3.0`  | Headless mode (non-interactive)                         | Coding Workflow                 | 1     |
| ACP/Zed                 | `V0.2.0`  | ACP and Zed editor integration                          | Integrating Community Ecosystem | 1     |
| Terminal UI             | `V0.1.0+` | Interactive terminal user interface                     | User Experience                 | 1     |
| Settings                | `V0.1.0+` | Configuration management system                         | User Experience                 | 1     |
| Theme                   | `V0.1.0+` | Multi-theme support                                     | User Experience                 | 1     |
| Support OpenAI Protocol | `V0.1.0+` | Support for OpenAI API protocol                         | User Experience                 | 1     |
| Chat Management         | `V0.1.0+` | Session management (save, restore, browse)              | Coding Workflow                 | 1     |
| MCP                     | `V0.1.0+` | Model Context Protocol integration                      | Coding Workflow                 | 1     |
| Multi Model             | `V0.1.0+` | Multi-model support and switching                       | Coding Workflow                 | 1     |
| Slash Commands          | `V0.1.0+` | Slash command system                                    | Coding Workflow                 | 1     |
| Tool: Bash              | `V0.1.0+` | Shell command execution tool (with is_background param) | Coding Workflow                 | 1     |
| Tool: FileRead/EditFile | `V0.1.0+` | File read/write and edit tools                          | Coding Workflow                 | 1     |
| Custom Commands         | `V0.1.0+` | Custom command loading                                  | Building Open Capabilities      | 1     |
| Feedback                | `V0.1.0+` | Feedback mechanism (/bug command)                       | Administrative Capabilities     | 1     |
| Stats                   | `V0.1.0+` | Usage statistics and quota display                      | Administrative Capabilities     | 1     |
| Memory                  | `V0.0.9+` | Project-level and global memory management              | User Experience                 | 1     |
| Cache Control           | `V0.0.9+` | Prompt caching control (Anthropic, DashScope)           | User Experience                 | 1     |
| PlanMode                | `V0.0.14` | Task planning mode                                      | Coding Workflow                 | 1     |
| Compress                | `V0.0.11` | Chat compression mechanism                              | User Experience                 | 1     |
| SubAgent                | `V0.0.11` | Dedicated sub-agent system                              | Coding Workflow                 | 1     |
| TodoWrite               | `V0.0.10` | Task management and progress tracking                   | Coding Workflow                 | 1     |
| Tool: TextSearch        | `V0.0.8+` | Text search tool (grep, supports .qwenignore)           | Coding Workflow                 | 1     |
| Tool: WebFetch          | `V0.0.7+` | Web content fetching tool                               | Coding Workflow                 | 1     |
| Tool: WebSearch         | `V0.0.7+` | Built-in web search (DashScope Responses API)           | Coding Workflow                 | 1     |
| OAuth                   | `V0.0.5+` | OAuth login authentication (Qwen OAuth)                 | User Experience                 | 1     |

#### Features to Develop

| Feature                      | Priority | Status      | Description                       | Category                    |
| ---------------------------- | -------- | ----------- | --------------------------------- | --------------------------- |
| Better UI                    | P1       | Planned     | Optimized terminal UI interaction | User Experience             |
| OnBoarding                   | P1       | Planned     | New user onboarding flow          | User Experience             |
| Permission                   | P1       | Planned     | Permission system optimization    | User Experience             |
| Cross-platform Compatibility | P1       | In Progress | Windows/Linux/macOS compatibility | User Experience             |
| LogView                      | P2       | Planned     | Log viewing and debugging feature | User Experience             |
| Hooks                        | P2       | In Progress | Extension hooks system            | Coding Workflow             |
| Costs                        | P2       | Planned     | Cost tracking and analysis        | Administrative Capabilities |
| Dashboard                    | P2       | Planned     | Management dashboard              | Administrative Capabilities |

#### Distinctive Features to Discuss

| Feature          | Status   | Description                                           |
| ---------------- | -------- | ----------------------------------------------------- |
| Home Spotlight   | Research | Project discovery and quick launch                    |
| Competitive Mode | Research | Competitive mode                                      |
| Pulse            | Research | User activity pulse analysis (OpenAI Pulse reference) |
| Code Wiki        | Research | Project codebase wiki/documentation system            |


</details>
