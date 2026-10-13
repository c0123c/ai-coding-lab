# 建立 AI 原生工程团队

按规划、设计、实现、验证和交付讨论编码 Agent 的作用与工程师责任，提供团队引入方式、检查清单和协作建议。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/guides/build-ai-native-engineering-team>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/guides/build-ai-native-engineering-team>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/建立 AI 原生工程团队.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 建立 AI- 本地工程团队 | ChatGPT 学习

# 建立 AI- 本地工程团队

编码 agents 如何加快软件开发生命周期

## 简介

AI 模型正在迅速扩大它们可以执行的任务范围，这对工程具有重大影响。 Frontier 系统现在支持多小时推理：截至 8 月 2025、METR 发现领先模型可以完成 2 小时和 17 分钟的连续工作，并且大约有 50% 的信心产生正确答案。

这种能力正在迅速提高，任务长度大约每七个月就会增加一倍。仅在几年前，模型可以管理大约 30 秒的推理 - 对于小型代码建议来说足够了。如今，随着模型维持更长的推理链，整个软件开发生命周期可能都在 AI 的协助范围内，从而使编码 agents 能够有效地为规划、设计、开发、测试、代码审查和部署做出贡献。

在本指南中，我们将分享真实的示例，概述 AI agents 如何为软件开发生命周期做出贡献，并提供有关工程领导者今天可以做什么来开始构建 AI- 本地团队和流程的实用指导。

## AI 编码：从自动完成到 Agents

AI 编码工具的进步远远超出了其作为自动完成助手的起源。早期的工具处理快速任务，例如建议下一行代码或填写函数模板。随着模型推理能力的增强，开发者开始通过IDE中的聊天界面与agents进行交互，进行结对编程和代码探索。

今天的编码 agents 可以生成整个文件、搭建新项目并将设计转换为代码。他们可以通过调试或重构等多步骤问题进行推理，agent执行现在也从个人开发人员的机器转移到基于云的多agent环境。这正在改变开发人员的工作方式，使他们能够花更少的时间使用 IDE 内的 agent 生成代码，而将更多时间用于委派整个工作流程。

功能 实现的功能 跨系统的统一上下文 单个模型可以读取代码、配置和遥测，从而在以前需要单独工具的各层之间提供一致的推理。结构化工具执行模型现在可以直接调用编译器、测试运行器和扫描器，生成可验证的结果而不是静态建议。持久的项目记忆长上下文窗口和压缩等技术允许模型遵循从建议到部署的功能，记住以前的设计选择和约束。评估循环 可以根据基准（单元测试、延迟目标或风格指南）自动测试模型输出，因此改进基于可衡量的质量。

在OpenAI，我们亲眼目睹了这一点。开发周期加快，曾经需要数周时间完成的工作现在仅需几天即可交付。团队可以更轻松地跨领域移动，更快地加入不熟悉的项目，并在整个组织中以更大的敏捷性和自主性运作。许多日常且耗时的任务，从记录新代码和进行相关测试、维护依赖关系和清理功能标志，现在都完全委托给了 Codex。

然而，工程的某些方面保持不变。代码的真正所有权（尤其是对于新问题或模糊问题）仍然取决于工程师，并且某些挑战超出了当前模型的能力。但通过像 Codex 这样的编码 agents，工程师现在可以将更多时间花在复杂和新颖的挑战上，专注于设计、架构和系统级推理，而不是调试或死记硬背的实现。

在以下部分中，我们将详细介绍 SDLC 的每个阶段如何随着编码 agents 的变化而变化，并概述您的团队可以采取的具体步骤来开始作为 AI- 本地工程组织进行运营。

## 1。计划

组织中的团队通常依赖工程师来确定某个功能是否可行、构建需要多长时间以及将涉及哪些系统或团队。虽然任何人都可以起草规范，但形成准确的计划通常需要深入的代码库意识和多轮工程迭代，以发现需求、澄清边缘情况并与技术上的实际情况保持一致。

### 编码 agents 如何提供帮助

AI 编码 agents 为团队在规划和范围界定过程中提供即时的、代码感知的见解。例如，团队可以构建将编码 agents 连接到问题跟踪系统的工作流程，以读取功能规范，将其与代码库交叉引用，然后标记歧义，将工作分解为子组件，或估计难度。

编码 agents 还可以立即跟踪代码路径，以显示某个功能涉及哪些服务——以前需要数小时或数天手动挖掘大型代码库的工作。

### 工程师会做什么

团队在核心功能工作上花费了更多时间，因为 agents 揭示了以前需要召开会议来进行产品调整和范围界定的背景。预先确定关键的实施细节、依赖关系和边缘情况，从而可以通过更少的会议更快地做出决策。

委托审核自己的 AI agents 可以首先进行可行性和架构分析。他们阅读规范，将其映射到代码库，识别依赖关系，并找出需要澄清的歧义或边缘情况。团队审查 agent 的调查结果，以验证准确性、评估完整性并确保估算反映真实的技术限制。故事点分配、工作量调整和识别非明显风险仍然需要人类判断。战略决策——例如优先级、长期方向、排序和权衡——仍然以人为主导。团队可以向 agent 询问选项或后续步骤，但规划和产品方向的最终责任仍由组织承担。

### 入门清单

- 识别需要功能和源代码之间的一致性的常见流程。公共区域包括功能范围和票证创建。

- 首先实施基本工作流程，例如标记和删除重复问题或功能请求。

- 考虑更高级的工作流程，例如根据初始功能描述向工单添加子任务。或者当票证到达特定阶段时启动 agent 运行，以补充更多详细信息的描述。

## 2。设计

设计阶段常常因基础设置工作而减慢。团队花费大量时间连接样板、集成设计系统以及完善 UI 组件或流程。模型和实现之间的不一致可能会导致返工和较长的反馈周期，而探索替代方案或适应不断变化的需求的带宽有限，会延迟设计验证。

### 编码 agents 如何提供帮助

AI 编码工具通过搭建样板代码、构建项目结构以及立即实施设计标记或风格指南，极大地加速了原型设计。工程师可以用自然语言描述所需的功能或 UI 布局，并接收符合团队约定的原型代码或组件存根。

他们可以将设计直接转换为代码，提出可访问性改进建议，甚至分析代码库以了解用户流程或边缘情况。这使得在数小时而不是数天内迭代多个原型成为可能，并尽早以高保真度进行原型设计，为团队提供更清晰的决策基础，并使客户能够在此过程中更快地进行测试。

### 工程师会做什么

通过 agents 处理日常设置和翻译任务，团队可以将注意力转移到更高杠杆的工作上。工程师专注于完善核心逻辑、建立可扩展的架构模式并确保组件满足质量和可靠性标准。设计师可以花更多时间评估用户流程并探索替代概念。协作工作从实施开销转向改善底层产品体验。

委托审查自己 Agents 通过搭建项目、生成样板代码、将模型转换为组件以及应用设计令牌或样式指南来处理初始实施工作。该团队审查 agent 的输出，以确保组件遵循设计惯例、满足质量和可访问性标准，并与现有系统正确集成。该团队拥有总体设计系统、UX 模式、架构决策和用户体验的最终方向。

### 入门清单

- 使用同时接受文本和图像输入的多模态编码 agent

- 通过 MCP 与编码 agents 集成设计工具

- 使用 MCP 以编程方式公开组件库，并将它们与您的编码模型集成

- 构建映射设计 → 组件 → 组件实现的工作流程

- 利用类型化语言（例如 Typescript）为 agent 定义有效的 props 和子组件

## 3。构建

构建阶段是团队感受到最大摩擦的阶段，也是编码 agents 产生最明显影响的阶段。工程师花费大量时间将规范转换为代码结构，将服务连接在一起，在代码库中复制模式，并填充样板文件，即使是很小的功能也需要数小时的忙碌工作。

随着系统的发展，这种摩擦会加剧。大型单一存储库积累了模式、惯例和历史怪癖，这些都减慢了贡献者的速度。工程师可以花费与实现功能本身一样多的时间来重新发现做某事的“正确方法”。规范、代码搜索、构建错误、测试失败和依赖管理之间不断的上下文切换会增加认知负担，而长时间运行的任务期间的中断会破坏流程并进一步延迟交付。

### 编码 agents 如何提供帮助

在 IDE 和 CLI 中运行的编码 agents 通过处理更大的多步骤实施任务来加速构建阶段。他们不是只生成下一个函数或文件，而是可以在一次协调运行中生成端到端的完整功能 - 数据模型、API、UI 组件、测试和文档。通过对整个代码库的持续推理，他们可以处理曾经需要工程师手动跟踪代码路径的决策。

对于长时间运行的任务，agents 可以：

- 根据书面规范起草整个功能实现。

- 在数十个文件中搜索和修改代码，同时保持一致性。

- 生成符合约定的样板：错误处理、遥测、安全包装器或样式模式。

- 修复出现的构建错误，而不是暂停以进行人工干预。

- 将测试与实现一起编写为单个工作流程的一部分。

- 生成遵循内部指南并包含 PR 消息的 diff-ready 变更集。

实际上，这将大部分机械“构建工作”从工程师转移到了 agents。 agent成为第一遍实施者；工程师成为审稿人、编辑和指导来源。

### 工程师会做什么

当agents能够可靠地执行多步构建任务时，工程师将注意力转移到更高阶的工作上：

- 在实施之前澄清产品行为、边缘情况和规格。

- 检查 AI- 生成的代码的架构含义，而不是执行死记硬背。

- 细化需要深度领域推理的业务逻辑和性能关键路径。

- 设计指导 agent 生成的代码的模式、护栏和约定。

- 与产品经理和设计人员合作，迭代功能意图，而不是样板文件。

工程师不是将功能规范“翻译”成代码，而是专注于正确性、连贯性、可维护性和长期质量，这些领域的人文环境仍然最重要。

代表审核自己的 Agents 起草明确指定功能的第一个实现阶段 — 脚手架、CRUD 逻辑、接线、重构和测试。随着长期运行推理的改进，这越来越多地涵盖完整的端到端构建，而不是孤立的片段。工程师评估设计选择、性能、安全性、迁移风险和域对齐，同时纠正 agent 可能遗漏的细微问题。他们塑造和完善AI-生成的代码，而不是执行机械工作。工程师保留需要深入系统直觉的工作所有权：新的抽象、横切架构变更、模糊的产品需求以及长期可维护性权衡。随着 agents 承担更长的任务，工程从逐行实施转向迭代监督。

示例：

云从科技的工程师、PM、设计师和操作员每天都使用 Codex 将规范转换为工作代码，无论他们需要脚本、新的欺诈规则还是在几分钟内交付的完整微服务。它消除了构建阶段的繁忙工作，并赋予每位员工以惊人的速度实施想法的能力。

### 入门清单

- 从明确指定的任务开始

- 让 agent 通过 MCP 使用规划工具，或者编写提交到代码库的 PLAN.md 文件

- 检查 agent 尝试执行的命令是否成功

- 迭代 AGENTS.md 文件，解锁代理循环，例如运行测试和 linter 以接收反馈

## 4。测试

开发人员通常很难确保足够的测试覆盖率，因为编写和维护全面的测试需要时间、需要上下文切换以及对边缘情况的深入理解。团队经常面临快速行动和编写彻底测试之间的权衡。当最后期限临近时，测试覆盖率往往是首先受到影响的。

即使编写了测试，随着代码的发展保持更新也会带来持续的摩擦。测试可能会变得脆弱，由于不明原因而失败，并且随着底层产品的变化，可能需要进行重大重构。高质量的测试让团队更加自信地更快地交付。

### 编码 agents 如何提供帮助

AI 编码工具可以帮助开发人员通过多种强大的方式编写更好的测试。首先，他们可以根据阅读需求文档和功能代码的逻辑来建议测试用例。模型在建议开发人员很容易忽视的边缘情况和故障模式方面出奇地出色，尤其是当他们深入关注该功能并需要第二意见时。

此外，模型可以帮助测试随着代码的发展而更新，减少重构的摩擦并避免过时的测试变得不稳定。通过处理测试编写的基本实现细节和呈现边缘情况，编码 agents 加速了开发测试的过程。

### 工程师会做什么

使用 AI 工具编写测试并不能消除开发人员考虑测试的需要。事实上，随着 agents 消除了生成代码的障碍，测试作为应用程序功能的真实来源发挥着越来越重要的作用。由于 agents 可以运行测试套件并根据输出进行迭代，因此定义高质量测试通常是允许 agent 构建功能的第一步。

相反，开发人员更关注于查看测试覆盖率中的高级模式，构建并挑战模型对测试用例的识别。加快测试编写速度可以让开发人员更快地发布功能，并实现更雄心勃勃的功能。

委托审查自己的工程师将根据功能规范委托生成测试用例的初始阶段。他们还将使用该模型来完成生成测试的第一步。让模型在与功能实现不同的会话中生成测试会很有帮助。工程师仍然必须彻底审查模型生成的测试，以确保模型没有走捷径或实施存根测试。工程师还确保测试可以通过他们的 agents 运行； agent 具有适当的运行权限，并且 agent 具有对其可以运行的不同测试套件的上下文感知。工程师负责根据功能规范和用户体验期望调整测试覆盖范围。对抗性思维、绘制边缘案例的创造力以及对测试意图的关注仍然是关键技能。

### 入门清单

- 指导模型将测试作为单独的步骤来实现，并在进行功能实现之前验证新测试是否失败。

- 在 AGENTS.md 文件中设置测试覆盖率指南

- 给出agent可以调用的代码覆盖率工具的具体示例，以了解测试覆盖率

## 5。评论

平均而言，开发人员每周花费 2–5 小时进行代码审查。团队经常面临一个选择：是投入大量时间进行深入审查，还是对看似很小的更改进行快速“足够好”的检查。当这种优先级被取消时，错误就会溜进生产中，给用户带来问题并造成大量返工。

### 编码 agents 如何提供帮助

编码 agents 允许扩展代码审查流程，以便每个 PR 都能获得一致的关注基线。与传统的静态分析工具（依赖于模式匹配和基于规则的检查）不同，AI 审阅者实际上可以执行部分​​代码、解释运行时行为以及跨文件和服务跟踪逻辑。然而，为了有效，模型必须经过专门训练以识别 P0 和 P1- 级别的错误，并进行调整以提供简洁、高信号的反馈；过于冗长的响应就像嘈杂的 lint 警告一样容易被忽略。

### 工程师会做什么

在 OpenAI，我们发现 AI 代码审查让工程师更有信心，他们不会将重大错误带入生产。通常，代码审查会发现贡献者可以在引入另一位工程师之前纠正的问题。代码审查不一定会使拉取请求过程更快，特别是当它发现有意义的错误时 - 但它确实可以防止缺陷和中断。

### 委托、审查、自己

即使进行了 AI 代码审查，工程师仍然有责任确保代码已准备好交付。实际上，这意味着阅读并理解变化的影响。工程师将初始代码审查委托给 agent，但拥有最终审查和合并流程。

委托审核自己的工程师将初始编码审核委托给 agents。在拉取请求被标记为可供团队成员审核之前，这种情况可能会发生多次。工程师仍然审查拉取请求，但更注重架构一致性；是否正在实现可组合模式，是否使用了正确的约定，功能是否符合要求。工程师最终拥有部署到生产环境的代码；他们必须确保其可靠运行并满足预期要求。

示例：

Sansan 使用 Codex 审查竞争条件和数据库关系，这是人类经常忽视的问题。 Codex 还能够捕获不正确的硬编码，甚至预测未来的可扩展性问题。

### 入门清单

- 策划由工程师进行的黄金标准 PR 示例，包括代码更改和留下的评论。将其保存为评估集以测量不同的工具。

- 选择具有经过专门培训的代码审查模型的产品。我们发现广义模型经常挑剔并且提供较低的信噪比。

- 定义您的团队将如何衡量评论是否高质量。我们建议跟踪 PR 评论反应，作为标记好评和差评的低摩擦方式。

- 从小规模开始，但一旦您对审核结果充满信心，就可以快速推出。

## 6。文件

大多数工程团队都知道他们的文档落后了，但发现追赶的成本很高。关键知识通常由个人掌握，而不是在可搜索的知识库中捕获，并且现有文档很快就会过时，因为更新它们会使工程师远离产品工作。即使团队进行文档冲刺，结果通常也是一次性的工作，随着系统的发展，这种工作就会消失。

### 编码 agents 如何提供帮助

编码 agents 非常有能力根据阅读代码库来总结功能。他们不仅可以编写部分代码库的工作原理，还可以使用 mermaid 等语法生成系统图。当开发人员使用 agents 构建功能时，他们还可以通过提示模型来更新文档。借助 AGENTS.md，每个提示中都可以自动包含根据需要更新文档的说明，以提高一致性。

由于编码 agents 可以通过 SDK 以编程方式运行，因此它们也可以合并到发布工作流程中。例如，我们可以要求编码 agent 审查版本中包含的提交并总结关键更改。结果是文档成为交付管道的内置部分：生成速度更快，更容易保持最新状态，并且不再依赖于“抽出时间”的人。

### 工程师会做什么

工程师从手工编写每份文档转向塑造和监督系统。他们决定如何组织文档，添加决策背后的重要“原因”，为 agents 制定明确的标准和模板以供遵循，并审查关键或面向客户的部分。他们的工作是确保文档结构化、准确并融入交付流程，而不是自己完成所有打字工作。

委托审核自己将低风险、重复性工作完全移交给Codex，例如文件和模块的首轮摘要、输入和输出的基本描述、依赖项列表以及拉取请求更改的简短摘要。在发布任何内容之前，工程师会审查和编辑由 Codex 起草的重要文档，例如核心服务概述、公共 API 和 SDK 文档、运行手册和架构页面。工程师仍然负责 agent 遵循的整体文档策略和结构、标准和模板，以及涉及法律、法规或品牌风险的所有面向外部或安全关键的文档。

### 入门清单

- 通过提示编码来试验文档生成 agent

- 将文档指南合并到您的 AGENTS.md 中

- 确定可以自动生成文档的工作流程（例如发布周期）

- 检查生成内容的质量、正确性和重点

## 7。部署和维护

了解应用程序日志记录对于软件可靠性至关重要。在发生事件期间，软件工程师将参考日志记录工具、代码部署和基础设施更改来确定根本原因。令人惊讶的是，这个过程通常是手动的，并且需要开发人员在不同的系统之间来回切换，在事件等高压情况下会花费关键的时间。

### 编码 agents 如何提供帮助

使用 AI 编码工具，除了代码库的上下文之外，您还可以通过 MCP 服务器提供对日志记录工具的访问。这允许开发人员拥有一个单一的工作流程，他们可以在其中提示模型查看特定端点的错误，然后模型可以使用该上下文来遍历代码库并查找相关的错误或性能问题。由于编码 agents 也可以使用命令行工具，因此他们可以查看 git 历史记录来识别可能导致在日志跟踪中捕获问题的特定更改。

### 工程师会做什么

通过自动化日志分析和事件分类的繁琐工作，AI 使工程师能够专注于更高级别的故障排除和系统改进。工程师可以专注于验证 AI- 生成的根本原因、设计弹性修复和制定预防措施，而不是手动关联日志、提交和基础设施更改。这种转变减少了花在被动救火上的时间，使团队能够在主动可靠性工程和架构改进上投入更多精力。

委托审查自己的许多操作任务可以委托给agents——解析日志、显示异常指标、识别可疑代码更改，甚至提出修补程序。工程师审查和完善 AI- 生成的诊断，确认准确性并批准修复步骤。他们确保修复符合可靠性、安全性和合规性标准。关键决策由工程师负责，特别是对于新事件、敏感的生产变化或模型置信度较低的情况。人类仍然负责判断和最终签署。

示例：

维珍航空使用 Codex 来加强团队部署和维护系统的方式。 Codex VS Code 扩展为工程师提供了一个单一位置来调查日志、跨代码和数据跟踪问题以及通过 Azure DevOps MCP 和 Databricks 托管 MCP 查看更改。通过在 IDE 内统一此操作上下文，Codex 可以加快根本原因发现、减少手动分类，并帮助团队专注于验证修复和提高系统可靠性。

### 入门清单

- 将 AI 工具连接到日志记录和部署系统：将 Codex CLI 或类似工具与您的 MCP 服务器和日志聚合器集成。

- 定义访问范围和权限：确保 agents 可以访问相关日志、代码存储库和部署历史记录，同时维护安全最佳实践。

- 配置提示模板：为常见操作查询创建可重复使用的提示，例如“调查端点 X 的错误”或“分析部署后的日志峰值”。

- 测试工作流程：运行模拟事件场景，以确保 AI 呈现正确的上下文、准确跟踪代码并提出可行的诊断。

- 迭代和改进：从真实事件中收集反馈，调整提示策略，并随着系统和流程的发展扩展 agent 功能。

## 结论

编码 agents 正在通过承担传统上拖慢工程团队速度的机械、多步骤工作来改变软件开发生命周期。凭借持续的推理、统一的代码库上下文以及执行真实工具的能力，这些 agents 现在可以处理从范围界定和原型设计到实施、测试、审查甚至操作分类的任务。工程师牢牢地控制着架构、产品意图和质量，但编码 agents 越来越多地充当 SDLC 每个阶段的首要实施者和持续合作者。

这种转变不需要彻底的改革；随着编码 agents 变得更加强大和可靠，小型、有针对性的工作流程会快速复合。从范围明确的任务开始、投资护栏并迭代扩展 agent 职责的团队在速度、一致性和开发人员关注度方面看到了有意义的收益。

如果您正在探索编码 agents 如何加速您的组织或准备首次部署，请联系 OpenAI。我们随时帮助您将编码 agents 转化为真正的杠杆作用 - 设计跨规划、设计、构建、测试、审查和运营的端到端工作流程，并帮助您的团队采用生产就绪模式，使 AI- 原生工程成为现实。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Building an AI-Native Engineering Team | ChatGPT Learn

# Building an AI-Native Engineering Team

How coding agents speed up the software development lifecycle

## Introduction

AI models are rapidly expanding the range of tasks they can perform, with significant implications for engineering. Frontier systems now sustain multi-hour reasoning: as of August 2025, METR found that leading models could complete 2 hours and 17 minutes of continuous work with roughly 50% confidence of producing a correct answer.

This capability is improving quickly, with task length doubling about every seven months. Only a few years ago, models could manage about 30 seconds of reasoning – enough for small code suggestions. Today, as models sustain longer chains of reasoning, the entire software development lifecycle is potentially in scope for AI assistance, enabling coding agents to contribute effectively to planning, design, development, testing, code reviews, and deployment.

In this guide, we’ll share real examples that outline how AI agents are contributing to the software development lifecycle with practical guidance on what engineering leaders can do today to start building AI-native teams and processes.

## AI Coding: From Autocomplete to Agents

AI coding tools have progressed far beyond their origins as autocomplete assistants. Early tools handled quick tasks such as suggesting the next line of code or filling in function templates. As models gained stronger reasoning abilities, developers began interacting with agents through chat interfaces in IDEs for pair programming and code exploration.

Today’s coding agents can generate entire files, scaffold new projects, and translate designs into code. They can reason through multi-step problems such as debugging or refactoring, with agent execution also now shifting from an individual developer’s machine to cloud-based, multi-agent environments. This is changing how developers work, allowing them to spend less time generating code with the agent inside the IDE and more time delegating entire workflows.

Capability What It Enables Unified context across systems A single model can read code, configuration, and telemetry, providing consistent reasoning across layers that previously required separate tooling. Structured tool execution Models can now call compilers, test runners, and scanners directly, producing verifiable results rather than static suggestions. Persistent project memory Long context windows and techniques like compaction allow models to follow a feature from proposal to deployment, remembering previous design choices and constraints. Evaluation loops Model outputs can be tested automatically against benchmarks—unit tests, latency targets, or style guides—so improvements are grounded in measurable quality.

At OpenAI, we have witnessed this firsthand. Development cycles have accelerated, with work that once required weeks now being delivered in days. Teams move more easily across domains, onboard faster to unfamiliar projects, and operate with greater agility and autonomy across the organization. Many routine and time-consuming tasks, from documenting new code and surfacing relevant tests, maintaining dependencies and cleaning up feature flags are now delegated to Codex entirely.

However, some aspects of engineering remain unchanged. True ownership of code—especially for new or ambiguous problems—still rests with engineers, and certain challenges exceed the capabilities of current models. But with coding agents like Codex, engineers can now spend more time on complex and novel challenges, focusing on design, architecture, and system-level reasoning rather than debugging or rote implementation.

In the following sections, we break down how each phase of the SDLC changes with coding agents — and outline the concrete steps your team can take to start operating as an AI-native engineering org.

## 1. Plan

Teams across an organization often depend on engineers to determine whether a feature is feasible, how long it will take to build, and which systems or teams will be involved. While anyone can draft a specification, forming an accurate plan typically requires deep codebase awareness and multiple rounds of iteration with engineering to uncover requirements, clarify edge cases, and align on what is technically realistic.

### How coding agents help

AI coding agents give teams immediate, code-aware insights during planning and scoping. For example, teams may build workflows that connect coding agents to their issue-tracking systems to read a feature specification, cross-reference it against the codebase, and then flag ambiguities, break the work into subcomponents, or estimate difficulty.

Coding agents can also instantly trace code paths to show which services are involved in a feature — work that previously required hours or days of manual digging through a large codebase.

### What engineers do instead

Teams spend more time on core feature work because agents surface the context that previously required meetings for product alignment and scoping. Key implementation details, dependencies, and edge cases are identified up front, enabling faster decisions with fewer meetings.

Delegate Review Own AI agents can take the first pass at feasibility and architectural analysis. They read a specification, map it to the codebase, identify dependencies, and surface ambiguities or edge cases that need clarification. Teams review the agent’s findings to validate accuracy, assess completeness, and ensure estimates reflect real technical constraints. Story point assignment, effort sizing, and identifying non-obvious risks still require human judgment. Strategic decisions — such as prioritization, long-term direction, sequencing, and tradeoffs — remain human-led. Teams may ask the agent for options or next steps, but final responsibility for planning and product direction stays with the organization.

### Getting started checklist

- Identify common processes that require alignment between features and source code. Common areas include feature scoping and ticket creation.

- Begin by implementing basic workflows, for example tagging and deduplicating issues or feature requests.

- Consider more advanced workflows, like adding sub-tasks to a ticket based on an initial feature description. Or kick off an agent run when a ticket reaches a specific stage to supplement the description with more details.

## 2. Design

The design phase is often slowed by foundational setup work. Teams spend significant time wiring up boilerplate, integrating design systems, and refining UI components or flows. Misalignment between mockups and implementation can create rework and long feedback cycles, and limited bandwidth to explore alternatives or adapt to changing requirements delays design validation.

### How coding agents help

AI coding tools dramatically accelerate prototyping by scaffolding boilerplate code, building project structures, and instantly implementing design tokens or style guides. Engineers can describe desired features or UI layouts in natural language and receive prototype code or component stubs that match the team’s conventions.

They can convert designs directly into code, suggest accessibility improvements, and even analyze the codebase for user flows or edge cases. This makes it possible to iterate on multiple prototypes in hours instead of days, and to prototype in high fidelity early, giving teams a clearer basis for decision-making and enabling customer testing far sooner in the process.

### What engineers do instead

With routine setup and translation tasks handled by agents, teams can redirect their attention to higher-leverage work. Engineers focus on refining core logic, establishing scalable architectural patterns, and ensuring components meet quality and reliability standards. Designers can spend more time evaluating user flows and exploring alternative concepts. The collaborative effort shifts from implementation overhead to improving the underlying product experience.

Delegate Review Own Agents handle the initial implementation work by scaffolding projects, generating boilerplate code, translating mockups into components, and applying design tokens or style guides. The team reviews the agent’s output to ensure components follow design conventions, meet quality and accessibility standards, and integrate correctly with existing systems. The team owns the overarching design system, UX patterns, architectural decisions, and the final direction of the user experience.

### Getting started checklist

- Use a multi-modal coding agent that accepts both text and image input

- Integrate design tools via MCP with coding agents

- Programmatically expose component libraries with MCP, and integrate them with your coding model

- Build workflows that map designs → components → implementation of components

- Utilize typed languages (e.g. Typescript) to define valid props and subcomponents for the agent

## 3. Build

The build phase is where teams feel the most friction, and where coding agents have the clearest impact. Engineers spend substantial time translating specs into code structures, wiring services together, duplicating patterns across the codebase, and filling in boilerplate, with even small features requiring hours of busy-work.

As systems grow, this friction compounds. Large monorepos accumulate patterns, conventions, and historical quirks that slow contributors down. Engineers can spend as much time rediscovering the “right way” to do something as implementing the feature itself. Constant context switching between specs, code search, build errors, test failures, and dependency management adds cognitive load — and interruptions during long-running tasks break flow and delay delivery further.

### How coding agents help

Coding agents running in the IDE and CLI accelerate the build phase by handling larger, multi-step implementation tasks. Rather than producing just the next function or file, they can produce full features end-to-end — data models, APIs, UI components, tests, and documentation — in a single coordinated run. With sustained reasoning across the entire codebase, they handle decisions that once required engineers to manually trace code paths.

With long-running tasks, agents can:

- Draft entire feature implementations based on a written spec.

- Search and modify code across dozens of files while maintaining consistency.

- Generate boilerplate that matches conventions: error handling, telemetry, security wrappers, or style patterns.

- Fix build errors as they appear rather than pausing for human intervention.

- Write tests alongside implementation as part of a single workflow.

- Produce diff-ready changesets that follow internal guidelines and include PR messages.

In practice, this shifts much of the mechanical “build work” from engineers to agents. The agent becomes the first-pass implementer; the engineer becomes the reviewer, editor, and source of direction.

### What engineers do instead

When agents can reliably execute multi-step build tasks, engineers shift their attention to higher-order work:

- Clarifying product behavior, edge cases, and specs before implementation.

- Reviewing architectural implications of AI-generated code instead of performing rote wiring.

- Refining business logic and performance-critical paths that require deep domain reasoning.

- Designing patterns, guardrails, and conventions that guide agent-generated code.

- Collaborating with PMs and design to iterate on feature intent, not boilerplate.

Instead of “translating” a feature spec into code, engineers concentrate on correctness, coherence, maintainability, and long-term quality, areas where human context still matters most.

Delegate Review Own Agents draft the first implementation pass for well-specified features — scaffolding, CRUD logic, wiring, refactors, and tests. As long-running reasoning improves, this increasingly covers full end-to-end builds rather than isolated snippets. Engineers assess design choices, performance, security, migration risk, and domain alignment while correcting subtle issues the agent may miss. They shape and refine AI-generated code rather than performing the mechanical work. Engineers retain ownership of work requiring deep system intuition: new abstractions, cross-cutting architectural changes, ambiguous product requirements, and long-term maintainability trade-offs. As agents take on longer tasks, engineering shifts from line-by-line implementation to iterative oversight.

Example:

Engineers, PMs, designers, and operators at Cloudwalk use Codex daily to turn specs into working code whether they need a script, a new fraud rule, or a full microservice delivered in minutes. It removes the busy work from the build phase and gives every employee the power to implement ideas at remarkable speed.

### Getting started checklist

- Start with well specified tasks

- Have the agent use a planning tool via MCP, or by writing a PLAN.md file that is committed to the codebase

- Check that the commands the agent attempts to execute are succeeding

- Iterate on an AGENTS.md file that unlocks agentic loops like running tests and linters to receive feedback

## 4. Test

Developers often struggle to ensure adequate test coverage because writing and maintaining comprehensive tests takes time, requires context switching, and deep understanding of edge cases. Teams frequently face trade-offs between moving fast and writing thorough tests. When deadlines loom, test coverage is often the first thing to suffer.

Even when tests are written, keeping them updated as code evolves introduces ongoing friction. Tests can become brittle, fail for unclear reasons, and can require their own major refactors as the underlying product changes. High quality tests let teams ship faster with more confidence.

### How coding agents help

AI coding tools can help developers author better tests in several powerful ways. First, they can suggest test cases based on reading a requirements document and the logic of the feature code. Models can be surprisingly good at suggesting edge cases and failure modes that may be easy for a developer to overlook, especially when they have been deeply focused on the feature and need a second opinion.

In addition, models can help tests up to date as code evolves, reducing the friction of refactoring and avoiding stale tests that become flaky. By handling the basic implementation details of test writing and surfacing edge cases, coding agents accelerate the process of developing tests.

### What engineers do instead

Writing tests with AI tools doesn’t remove the need for developers to think about testing. In fact, as agents remove barriers to generating code, tests serve a more and more important function as a source of truth for application functionality. Since agents can run the test suite and iterate based on the output, defining high quality tests is often the first step to allowing an agent to build a feature.

Instead, developers focus more on seeing the high level patterns in test coverage, building on and challenging the model’s identification of test cases. Making test writing faster allows developers to ship features more quickly and also take on more ambitious features.

Delegate Review Own Engineers will delegate the initial pass at generating test cases based on feature specifications. They’ll also use the model to take a first pass at generating tests. It can be helpful to have the model generate tests in a separate session from the feature implementation. Engineers must still thoroughly review model-generated tests to ensure that the model did not take shortcuts or implement stubbed tests. Engineers also ensure that tests are runnable by their agents; that the agent has the appropriate permissions to run, and that the agent has context awareness of the different test suites it can run. Engineers own aligning test coverage with feature specifications and user experience expectations. Adversarial thinking, creativity in mapping edge cases, and focus on intent of the tests remain critical skills.

### Getting started checklist

- Guide the model to implement tests as a separate step, and validate that new tests fail before moving to feature implementation.

- Set guidelines for test coverage in your AGENTS.md file

- Give the agent specific examples of code coverage tools it can call to understand test coverage

## 5. Review

On average, developers spend 2–5 hours per week conducting code reviews. Teams often face a choice between investing significant time in a deep review or doing a quick “good enough” pass for changes that seem small. When this prioritization is off, bugs slip into production, causing issues for users and creating substantial rework.

### How coding agents help

Coding agents allow the code review process to scale so every PR receives a consistent baseline of attention. Unlike traditional static analysis tools (which rely on pattern matching and rule-based checks) AI reviewers can actually execute parts of the code, interpret runtime behavior, and trace logic across files and services. To be effective, however, models must be trained specifically to identify P0 and P1-level bugs, and tuned to provide concise, high-signal feedback; overly verbose responses are ignored just as easily as noisy lint warnings.

### What engineers do instead

At OpenAI, we find that AI code review gives engineers more confidence that they are not shipping major bugs into production. Frequently, code review will catch issues that the contributor can correct before pulling in another engineer. Code review doesn’t necessarily make the pull request process faster, especially if it finds meaningful bugs – but it does prevent defects and outages.

### Delegate vs review vs own

Even with AI code review, engineers are still responsible for ensuring that the code is ready to ship. Practically, this means reading and understanding the implications of the change. Engineers delegate the initial code review to an agent, but own the final review and merge process.

Delegate Review Own Engineers delegate the initial coding review to agents. This may happen multiple times before the pull request is marked as ready for review by a teammate. Engineers still review pull requests, but with more of an emphasis on architectural alignment; are composable patterns being implemented, are the correct conventions being used, does the functionality match requirements. Engineers ultimately own the code that is deployed to production; they must ensure it functions reliably and fulfills the intended requirements.

Example:

Sansan uses Codex review for race conditions and database relations, which are issues humans often overlook. Codex has also been able to catch improper hard-coding and even anticipates future scalability concerns.

### Getting started checklist

- Curate examples of gold-standard PRs that have been conducted by engineers including both the code changes and comments left. Save this as an evaluation set to measure different tools.

- Select a product that has a model specifically trained on code review. We’ve found that generalized models often nitpick and provide a low signal to noise ratio.

- Define how your team will measure whether reviews are high quality. We recommend tracking PR comment reactions as a low-friction way to mark good and bad reviews.

- Start small but rollout quickly once you gain confidence in the results of reviews.

## 6. Document

Most engineering teams know their documentation is behind, but find catching up costly. Critical knowledge is often held by individuals rather than captured in searchable knowledge bases, and existing docs quickly go stale because updating them pulls engineers away from product work. And even when teams run documentation sprints, the result is usually a one-off effort that decays as soon as the system evolves.

### How coding agents help

Coding agents are highly capable of summarizing functionality based on reading codebases. Not only can they write about how parts of the codebase work, but they can also generate system diagrams in syntaxes like mermaid. As developers build features with agents, they can also update documentation simply by prompting the model. With AGENTS.md, instructions to update documentation as needed can be automatically included with every prompt for more consistency.

Since coding agents can be run programmatically through SDKs, they can also be incorporated into release workflows. For example, we can ask a coding agent to review commits being included in the release and summarize key changes. The result is that documentation becomes a built-in part of the delivery pipeline: faster to produce, easier to keep current, and no longer dependent on someone “finding the time.”

### What engineers do instead

Engineers move from writing every doc by hand to shaping and supervising the system. They decide how docs are organized, add the important “why” behind decisions, set clear standards and templates for agents to follow, and review the critical or customer-facing pieces. Their job becomes making sure documentation is structured, accurate, and wired into the delivery process rather than doing all the typing themselves.

Delegate Review Own Fully hand off low-risk, repetitive work to Codex like first-pass summaries of files and modules, basic descriptions of inputs and outputs, dependency lists, and short summaries of pull-request changes. Engineers review and edit important docs drafted by Codex like overviews of core services, public API and SDK docs, runbooks, and architecture pages, before anything is published. Engineers remain responsible for overall documentation strategy and structure, standards and templates the agent follows, and all external-facing or safety-critical documentation involving legal, regulatory, or brand risk.

### Getting started checklist

- Experiment with documentation generation by prompting the coding agent

- Incorporate documentation guidelines into your AGENTS.md

- Identify workflows (e.g. release cycles) where documentation can be automatically generated

- Review generated content for quality, correctness, and focus

## 7. Deploy and Maintain

Understanding application logging is critical to software reliability. During an incident, software engineers will reference logging tools, code deploys, and infrastructure changes to identify a root cause. This process is often surprisingly manual and requires developers to tab back and forth between different systems, costing critical minutes in high pressure situations like incidents.

### How coding agents help

With AI coding tools, you can provide access to your logging tools via MCP servers in addition to the context of your codebase. This allows developers to have a single workflow where they can prompt the model to look at errors for a specific endpoint, and then the model can use that context to traverse the codebase and find relevant bugs or performance issues. Since coding agents can also use command line tools, they can look at the git history to identify specific changes that might result in issues captured in log traces.

### What engineers do instead

By automating the tedious aspects of log analysis and incident triage, AI enables engineers to concentrate on higher-level troubleshooting and system improvement. Rather than manually correlating logs, commits, and infrastructure changes, engineers can focus on validating AI-generated root causes, designing resilient fixes, and developing preventative measures.This shift reduces time spent on reactive firefighting, allowing teams to invest more energy in proactive reliability engineering and architectural improvements.

Delegate Review Own Many operational tasks can be delegated to agents — parsing logs, surfacing anomalous metrics, identifying suspect code changes, and even proposing hotfixes. Engineers vet and refine AI-generated diagnostics, confirm accuracy, and approve remediation steps. They ensure fixes meet reliability, security, and compliance standards. Critical decisions stay with engineers, especially for novel incidents, sensitive production changes, or situations where model confidence is low. Humans remain responsible for judgment and final sign-off.

Example:

Virgin Atlantic uses Codex to strengthen how teams deploy and maintain their systems. The Codex VS Code Extension gives engineers a single place to investigate logs, trace issues across code and data, and review changes through Azure DevOps MCP and Databricks Managed MCPs. By unifying this operational context inside the IDE, Codex speeds up root cause discovery, reduces manual triage, and helps teams focus on validating fixes and improving system reliability.

### Getting started checklist

- Connect AI tools to logging and deployment systems: Integrate Codex CLI or similar with your MCP servers and log aggregators.

- Define access scopes and permissions: Ensure agents can access relevant logs, code repositories, and deployment histories, while maintaining security best practices.

- Configure prompt templates: Create reusable prompts for common operational queries, such as “Investigate errors for endpoint X” or “Analyze log spikes post-deploy.”

- Test the workflow: Run simulated incident scenarios to ensure the AI surfaces correct context, traces code accurately, and proposes actionable diagnostics.

- Iterate and improve: Collect feedback from real incidents, tune prompt strategies, and expand agent capabilities as your systems and processes evolve.

## Conclusion

Coding agents are transforming the software development lifecycle by taking on the mechanical, multi-step work that has traditionally slowed engineering teams down. With sustained reasoning, unified codebase context, and the ability to execute real tools, these agents now handle tasks ranging from scoping and prototyping to implementation, testing, review, and even operational triage. Engineers stay firmly in control of architecture, product intent, and quality — but coding agents increasingly serve as the first-pass implementer and continuous collaborator across every phase of the SDLC.

This shift doesn’t require a radical overhaul; small, targeted workflows compound quickly as coding agents become more capable and reliable. Teams that start with well-scoped tasks, invest in guardrails, and iteratively expand agent responsibility see meaningful gains in speed, consistency, and developer focus.

If you’re exploring how coding agents can accelerate your organization or preparing for your first deployment, reach out to OpenAI. We’re here to help you turn coding agents into real leverage—designing end-to-end workflows across planning, design, build, test, review, and operations, and helping your team adopt production-ready patterns that make AI-native engineering a reality.


</details>
