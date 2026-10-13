# AI Agent 的上下文工程

解释上下文容量与注意力的限制，介绍系统指令、工具和示例的组织方式，以及按需检索、上下文压缩、结构化笔记和子 Agent 在长任务中的用途。

- 来源：Anthropic／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-09-29](<https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents>)；更新：[2026-01-06](<https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/工程文章/AI Agent 的上下文工程.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

有效的情境工程 AI agents \ AnthropicSkip 到主要 contentSkip 到页脚
研究
政策
承诺
学习
NewsTry ClaudeEngineering 在 Anthropic
有效的情境工程 AI agents
九月出版 29, 2025
上下文是关键但有限的资源 AI agents。在这篇文章中，我们探讨了有效策划和管理为其提供动力的环境的策略。

在即时工程成为应用领域关注的焦点AI几年后，一个新术语开始受到关注：上下文工程。使用语言模型进行构建不再是为提示找到正确的单词和短语，而是更多地回答更广泛的问题：“什么样的上下文配置最有可能生成我们模型所需的行为？”

上下文是指从大语言模型 (LLM) 采样时包含的标记集。当前的工程问题是根据法学硕士的固有限制优化这些代币的效用，以便始终如一地实现预期结果。有效地争论法学硕士通常需要结合上下文进行思考——换句话说：考虑LLM在任何给定时间可用的整体状态以及该状态可能产生哪些潜在行为。

在这篇文章中，我们将探索情境工程的新兴艺术，并提供一个用于构建可操纵、有效的精细思维模型 agents。

上下文工程与提示工程
在Anthropic，我们将上下文工程视为提示工程的自然进展。即时工程是指编写和组织 LLM 指令以获得最佳结果的方法（有关概述和有用的即时工程策略，请参阅我们的文档）。上下文工程是指在 LLM 推理期间策划和维护最佳标记（信息）集的一组策略，包括提示之外可能出现的所有其他信息。
在法学硕士工程的早期，提示是 AI 工程工作的最大组成部分，因为日常聊天交互之外的大多数用例都需要针对提示进行优化一次性分类或文本生成任务。顾名思义，提示工程的首要关注点是如何编写有效的提示，特别是系统提示。然而，随着我们朝着设计能力更强的 agents 方向发展，在多轮推理和更长的时间范围内运行，我们需要管理整个上下文状态的策略（系统指令、工具、Model Context Protocol (MCP)、外部数据、消息历史记录等）。
循环运行的 agent 会生成越来越多的数据可能与下一轮推理相关，并且这些信息必须循环完善。上下文工程是一门艺术和科学，它管理从不断发展的可能信息宇宙中进入有限上下文窗口的内容。与编写提示的离散任务相比，上下文工程是迭代的，每次我们决定将什么传递给模型时，都会发生管理阶段。

为什么上下文工程对于培养有能力的人很重要 agents
尽管法学硕士管理越来越多的数据的速度和能力很高，但我们观察到，法学硕士和人类一样，在某个时刻会失去注意力或感到困惑。对大海捞针式基准测试的研究揭示了上下文腐烂的概念：随着上下文窗口中标记数量的增加，模型从上下文中准确回忆信息的能力会下降。
虽然某些模型比其他模型表现出更温和的退化，但这一特征在所有模型中都出现。因此，背景必须被视为边际收益递减的有限资源。就像人类的工作记忆容量有限一样，法学硕士在解析大量上下文时也有一个“注意力预算”。每推出一个新代币都会消耗一定数量的预算，从而增加了仔细管理可用代币的需要 LLM.
这种注意力的稀缺源于法学硕士的架构限制。 LLM 基于变压器架构，该架构使每个令牌都能参与整个上下文中的每个其他令牌。这会产生 n 个标记的 n² 成对关系。
随着上下文长度的增加，模型捕获这些成对关系的能力就会减弱，从而在上下文大小和注意力焦点之间产生自然的张力。此外，模型根据训练数据分布发展其注意力模式，其中较短的序列通常比较长的序列更常见。这意味着模型对上下文范围依赖关系的经验较少，并且专用参数也较少。
位置编码插值等技术允许模型通过使模型适应最初训练的较小上下文来处理更长的序列，尽管令牌位置理解会有所退化。这些因素造成了性能梯度而不是悬崖：模型在较长上下文中仍然具有很强的能力，但与较短上下文中的性能相比，信息检索和远程推理的精度可能会降低。
这些现实意味着深思熟虑的环境工程对于构建有能力的环境至关重要 agents.
有效情境的剖析
鉴于法学硕士受到有限注意力预算的限制，良好的上下文工程意味着找到尽可能最小的高信号标记集，从而最大限度地提高某些期望结果的可能性。实施这一实践说起来容易做起来难，但在下一节中，我们概述了这一指导原则在不同背景下的实践中的含义。
系统提示应极其清晰，并使用简单、直接的语言，以适当的高度向受众提出想法。 agent。正确的高度是两种常见故障模式之间的金发姑娘区。在一种极端情况下，我们看到工程师在提示中硬编码复杂、脆弱的逻辑，以引发精确的代理行为。随着时间的推移，这种方法会造成脆弱性并增加维护复杂性。在另一个极端，工程师有时会提供模糊的、高级的指导，而无法给出 LLM 期望输出的具体信号或错误地假设共享上下文。最佳高度达到了平衡：足够具体以有效地指导行为，但又足够灵活，可以为模型提供强大的启发式指导行为。在这一范围的一端，我们看到脆弱的 if-else 硬编码提示，而在另一端，我们看到过于笼统或错误地假设共享上下文的提示。
我们建议将提示组织成不同的部分（例如 <background_information>, <instructions>, ## 工具指南, ## 输出描述等）并使用诸如 XML 标记或 Markdown 标题来描述这些部分，尽管随着模型变得更加强大，提示的确切格式可能变得不那么重要。

无论您决定如何构建系统提示，您都应该努力获得完整概述您的预期行为的最少信息集。 （请注意，最小并不一定意味着短；您仍然需要预先向 agent 提供足够的信息，以确保其遵循所需的行为。）最好首先使用可用的最佳模型测试最小提示，以了解其在任务中的执行情况，然后添加明确的说明和示例，以根据初始测试期间发现的故障模式来提高性能。
工具允许 agents 与他们的环境，并在工作时引入新的、额外的背景。由于工具定义了 agents 与其 information/action 空间之间的契约，因此工具通过返回代币高效的信息和鼓励高效的 agent 行为来提高效率非常重要。
在为 AI agents 编写工具时 – AI agents，我们讨论了构建法学硕士可以很好理解并且功能重叠最小的工具。与精心设计的代码库的功能类似，工具应该是独立的，对错误具有鲁棒性，并且对其预期用途非常清晰。输入参数同样应该是描述性的、明确的，并发挥模型的固有优势。
我们看到的最常见的故障模式之一是臃肿的工具集，它们涵盖了太多的功能或导致关于使用哪个工具的决策点不明确。如果人类工程师不能明确地说出在给定情况下应该使用哪种工具，那么 AI agent 就不能指望做得更好。正如我们稍后将讨论的，为 agent 策划一套最小可行的工具还可以在长时间交互中实现更可靠的上下文维护和修剪。
提供示例（也称为“少样本提示”）是众所周知的最佳实践，我们将继续强烈建议。然而，团队经常会在提示中填入一系列边缘情况，试图阐明 LLM 针对特定任务应遵循的每条可能的规则。我们不推荐这样做。相反，我们建议努力策划一组多样化的规范示例，以有效地描绘 agent 的预期行为。对于 LLM，示例是价值一千个单词的“图片”。
我们对上下文不同组成部分（系统提示、工具、示例、消息历史记录等）的总体指导是深思熟虑，让您的上下文内容丰富但又紧凑。现在让我们深入研究运行时动态检索上下文。
上下文检索和代理搜索
在构建有效的AIagents中，我们强调了基于LLM-的工作流程和agents之间的差异。自从我们写那篇文章以来，我们倾向于对 agents 进行简单的定义：法学硕士在循环中自主使用工具。
与我们的客户一起工作，我们已经看到该领域正在向这个简单的范式收敛。随着底层模型变得更加强大，agents 的自主水平可以扩展：更智能的模型允许 agents 独立导航细微的问题空间并从错误中恢复。
我们现在看到工程师对 agents 设计环境的思考方式发生了转变。如今，许多 AI- 原生应用程序采用某种形式的基于嵌入的预推理时间检索来显示重要上下文，供 agent 进行推理。随着该领域转向更代理的方法，我们越来越多地看到团队通过“及时”上下文策略来增强这些检索系统。
使用“及时”方法构建的agents不是预先预处理所有相关数据，而是维护轻量级标识符（文件路径、存储的查询、Web 链接等），并使用这些引用在运行时使用工具将数据动态加载到上下文中。 Anthropic 的代理编码解决方案 Claude Code 使用这种方法对大型数据库执行复杂的数据分析。该模型可以编写有针对性的查询、存储结果，并利用 head 和 tail 等 Bash 命令来分析大量数据，而无需将完整的数据对象加载到上下文中。这种方法反映了人类的认知：我们通常不会记住整个信息库，而是引入外部组织和索引系统（例如文件系统、收件箱和书签）来按需检索相关信息。
除了存储效率之外，这些引用的元数据还提供了一种有效细化行为的机制，无论是明确提供的还是直观的。对于在文件系统中操作的 agent，测试文件夹中名为 test_utils.py 的文件的存在意味着与位于 src/core_logic/ 中的同名文件不同的用途。文件夹层次结构、命名约定和时间戳都提供了重要信号，帮助人类和 agents 了解如何以及何时使用
让agents自主导航和检索数据还可以实现渐进式披露，换句话说，允许agents通过探索逐步发现相关上下文。每次交互都会产生上下文，为下一个决策提供信息：文件大小表明复杂性；命名约定暗示目的；时间戳可以作为相关性的代理。 Agents 可以逐层组合理解，仅保留工作记忆中必要的内容，并利用笔记策略来增强持久性。这个自我管理的上下文窗口使agent专注于相关子集，而不是淹没在详尽但可能不相关的信息中。
当然，有一个权衡：运行时探索比检索预先计算的数据慢。不仅如此，还需要固执己见和深思熟虑的工程来确保 LLM 拥有正确的工具和启发式方法来有效地驾驭其信息环境。如果没有适当的指导，agent 可能会通过滥用工具、追逐死胡同或无法识别关键信息来浪费上下文。
 在某些设置中，最有效的 agents 可能会采用混合策略，预先检索一些数据以提高速度，并酌情进行进一步的自主探索。 “正确”自主级别的决策边界取决于任务。 Claude Code 是一个采用这种混合模型的 agent：CLAUDE.md 文件被天真地预先放入上下文中，而像 glob 和 grep 这样的原语允许它导航其环境并及时检索文件，有效地绕过陈旧索引和复杂语法树的问题。
混合策略可能更适合适用于动态内容较少的环境，例如法律或财务工作。随着模型能力的提高，代理设计将趋向于让智能模型智能地行动，逐渐减少人工管理。鉴于该领域的快速进展，“做最简单、有效的事情”可能仍然是我们对在 Claude 之上构建 agents 的团队的最佳建议。
长期任务的上下文工程
长期任务需要 agents 保持连贯性、上下文和在令牌计数超过 LLM 上下文窗口的操作序列上的目标导向行为。对于持续工作数十分钟到多个小时的任务，例如大型代码库迁移或综合研究项目，agents需要专门的技术来解决上下文窗口大小限制。
等待更大的上下文窗口似乎是一个明显的策略。但在可预见的未来，所有大小的上下文窗口都可能会受到上下文污染和信息相关性问题的影响——至少对于需要最强 agent 性能的情况来说是这样。为了使 agents 能够在较长的时间范围内有效工作，我们开发了一些直接解决这些上下文污染约束的技术：压缩、结构化笔记和多 agent 架构。
压缩
压缩是在接近上下文窗口限制的情况下进行对话、总结其内容并使用摘要重新启动新的上下文窗口的做法。压缩通常是上下文工程中的第一个杠杆，以推动更好的长期一致性。其核心是，压缩以高保真方式提取上下文窗口的内容，使 agent 能够以最小的性能下降继续运行。 
 例如，在 Claude Code 中，我们通过将消息历史记录传递给模型来总结和压缩最关键的细节来实现这一点。该模型保留了架构决策、未解决的错误和实现细节，同时丢弃了冗余的工具输出或消息。然后，agent 可以继续使用此压缩上下文以及五个最近访问的文件。用户可以获得连续性，而不必担心上下文窗口限制。
压缩的艺术在于选择保留什么与丢弃什么，因为过度激进的压缩可能会导致微妙但关键的上下文的丢失，其重要性只有在以后才会变得明显。对于实施压实系统的工程师，我们建议仔细调整复杂 agent 迹线的提示。首先最大限度地提高召回率，以确保您的压缩提示从跟踪中捕获每条相关信息，然后通过迭代消除多余内容来提高精度。
简单的多余内容的一个示例是清除工具调用和结果 - 一旦在消息历史记录中调用了某个工具，为什么 agent 需要再次查看原始结果？最安全、最轻量的压缩形式之一是工具结果清除，最近作为 Claude 开发者平台上的一项功能推出。
结构化笔记记录
结构化笔记记录或代理记忆是一种技术，agent 定期将笔记写入上下文窗口之外的内存中。这些注释稍后会被拉回到上下文窗口中。
此策略以最小的开销提供持久内存。就像 Claude Code 创建待办事项列表，或自定义 agent 维护 NOTES.md 文件一样，这种简单的模式允许 agent 跟踪复杂任务的进度，维护关键上下文和依赖关系，否则这些上下文和依赖关系可能会在数十个工具调用中丢失。
Claude 玩 Pokémon 演示了内存如何转换agent非编码领域的能力。 agent 在数千个游戏步骤中保持精确的计数 - 跟踪目标，例如“在我在路线 1 中训练我的神奇宝贝的最后 1,234 步中，皮卡丘已获得 8 等级，以实现 10 的目标。”在没有任何关于记忆结构的提示的情况下，它会开发探索区域的地图，记住它已解锁的关键成就，并维护战斗策略的战略笔记，帮助它了解哪些攻击最适合不同的对手。
上下文重置后，agent读取自己的笔记并继续数小时的训练序列或地下城探索。这种跨汇总步骤的一致性实现了长期策略，而这在单独将所有信息保留在 LLM 的上下文窗口中时是不可能实现的。
作为我们 Sonnet 4.5 发布的一部分，我们在 Claude 开发者平台上发布了一个公开测试版的记忆工具，使您可以更轻松地通过基于文件的系统。这使得agents能够随着时间的推移建立知识库，跨会话维护项目状态，并参考以前的工作，而无需将所有内容保留在上下文中。
子agent架构
子agent架构提供了另一种绕过上下文限制的方法。专门的子agents可以使用干净的上下文窗口来处理重点任务，而不是一个agent尝试在整个项目中维护状态。主要 agent 与高级计划协调，而子代理则执行深入的技术工作或使用工具查找相关信息。每个子代理可能会使用数万个或更多的令牌进行广泛的探索，但仅返回其工作的浓缩、提炼摘要（通常为 1,000-2,000 令牌）。
这种方法实现了明确的关注点分离 - 详细的搜索上下文在子 agents 中保持隔离，而主导搜索上下文agent侧重于结果的综合和分析。这种模式在我们如何构建多 agent 研究系统中进行了讨论，在复杂的研究任务上表现出比单 agent 系统有显着的改进。
这些方法之间的选择取决于任务特征。例如：
压缩为需要大量来回的任务维持会话流；
记笔记擅长具有明确里程碑的迭代开发；
多agent架构处理复杂的研究和分析，其中并行探索带来红利。
即使模型不断改进，在扩展交互中保持一致性的挑战仍然是构建更有效的核心agents。
结论
上下文工程代表了我们如何使用法学硕士进行构建的根本转变。随着模型变得更加强大，面临的挑战不仅仅是精心设计完美的提示，而是在每个步骤中精心策划哪些信息进入模型有限的注意力预算。无论您是为长期任务实施压缩、设计令牌高效工具，还是让 agents 能够及时探索其环境，指导原则都是一样的：找到最小的高信号令牌集，最大限度地提高您期望结果的可能性。
我们概述的技术将随着模型的改进而不断发展。我们已经看到，更智能的模型需要更少的规范工程，从而使 agents 能够以更多的自主权运行。但即使随着能力的扩展，将上下文视为宝贵的、有限的资源仍然是构建可靠、有效的 agents 的核心。
立即开始在 Claude 开发者平台中进行上下文工程，并通过我们的内存和上下文管理手册获取有用的提示和最佳实践。
致谢
书面由 Anthropic 的应用 AI 团队：Prithvi Rajasekaran、Ethan Dixon、Carly Ryan 和 Jeremy Hadfield 编写，团队成员 Rafi Ayub、Hannah Moran、Cal Rueb 和 Connor Jennings 做出贡献。特别感谢 Molly Vorwerck、Stuart Ritchie 和 Maggie Vo 的支持。

获取开发者通讯
产品更新、操作方法、社区焦点等等。每月发送到您的收件箱。
如果您想接收我们的每月开发者通讯，请提供您的电子邮件地址。您可以随时取消订阅。
产品展示
Claude
Claude Code
Claude Code 企业
Claude 协同办公
@Claude
Claude 设计
Claude 科学
Claude 安全性
Claude 在 Chrome 中
Claude 为了 Microsoft 365
技能
下载应用程序
定价
登录到 Claude
型号
神话
寓言
作品
十四行诗
俳句
解决方案
AI agents
代码现代化
编码
商业
客户支持
网络安全
企业
财务顾问
金融服务
政府
医疗保健
高等教育
K-12 教师
法律
生命科学
非营利组织
销售
小型企业
Claude 平台
概述
开发者文档
定价
生态系统
市场
区域合规性
Claude 上 AWS
Google 云
Microsoft 铸造厂
控制台登录
资源
博客
Claude 合作伙伴网络
社区
连接器
课程
客户案例
开发者博客
工程于 Anthropic
活动
插件
技术支持： Claude
服务合作伙伴
教程
使用案例
节目
校园
前沿学院
科学家
初创公司
帮助和安全
可用性
状态
支持中心
公司简介
Anthropic
职业机会
领导力
政策
经济期货
研究
新闻动态
Claude的宪法
Claude 军团
继续思考
政策 AI 指数
负责任的扩展政策
安全性和合规性
透明度
条款和政策
隐私政策
消费者健康数据隐私政策
负责任的披露政策
服务条款：商业
服务条款：消费者
服务条款： US K-12
数据处理协议： US K-12
使用政策© 2026 Anthropic PBC


## 原文对照

<details>
<summary>展开完整原文</summary>

Effective context engineering for AI agents \ AnthropicSkip to main contentSkip to footer
Research
Policy
Commitments
Learn
NewsTry ClaudeEngineering at Anthropic
Effective context engineering for AI agents
Published Sep 29, 2025
Context is a critical but finite resource for AI agents. In this post, we explore strategies for effectively curating and managing the context that powers them.

After a few years of prompt engineering being the focus of attention in applied AI, a new term has come to prominence: context engineering. Building with language models is becoming less about finding the right words and phrases for your prompts, and more about answering the broader question of “what configuration of context is most likely to generate our model’s desired behavior?"

Context refers to the set of tokens included when sampling from a large-language model (LLM). The engineering problem at hand is optimizing the utility of those tokens against the inherent constraints of LLMs in order to consistently achieve a desired outcome. Effectively wrangling LLMs often requires thinking in context — in other words: considering the holistic state available to the LLM at any given time and what potential behaviors that state might yield.

In this post, we’ll explore the emerging art of context engineering and offer a refined mental model for building steerable, effective agents.

Context engineering vs. prompt engineering
At Anthropic, we view context engineering as the natural progression of prompt engineering. Prompt engineering refers to methods for writing and organizing LLM instructions for optimal outcomes (see our docs for an overview and useful prompt engineering strategies). Context engineering refers to the set of strategies for curating and maintaining the optimal set of tokens (information) during LLM inference, including all the other information that may land there outside of the prompts.
In the early days of engineering with LLMs, prompting was the biggest component of AI engineering work, as the majority of use cases outside of everyday chat interactions required prompts optimized for one-shot classification or text generation tasks. As the term implies, the primary focus of prompt engineering is how to write effective prompts, particularly system prompts. However, as we move towards engineering more capable agents that operate over multiple turns of inference and longer time horizons, we need strategies for managing the entire context state (system instructions, tools, Model Context Protocol (MCP), external data, message history, etc).
An agent running in a loop generates more and more data that could be relevant for the next turn of inference, and this information must be cyclically refined. Context engineering is the art and science of curating what will go into the limited context window from that constantly evolving universe of possible information.In contrast to the discrete task of writing a prompt, context engineering is iterative and the curation phase happens each time we decide what to pass to the model.

Why context engineering is important to building capable agents
Despite their speed and ability to manage larger and larger volumes of data, we’ve observed that LLMs, like humans, lose focus or experience confusion at a certain point. Studies on needle-in-a-haystack style benchmarking have uncovered the concept of context rot: as the number of tokens in the context window increases, the model’s ability to accurately recall information from that context decreases.
While some models exhibit more gentle degradation than others, this characteristic emerges across all models. Context, therefore, must be treated as a finite resource with diminishing marginal returns. Like humans, who have limited working memory capacity, LLMs have an “attention budget” that they draw on when parsing large volumes of context. Every new token introduced depletes this budget by some amount, increasing the need to carefully curate the tokens available to the LLM.
This attention scarcity stems from architectural constraints of LLMs. LLMs are based on the transformer architecture, which enables every token to attend to every other token across the entire context. This results in n² pairwise relationships for n tokens.
As its context length increases, a model's ability to capture these pairwise relationships gets stretched thin, creating a natural tension between context size and attention focus. Additionally, models develop their attention patterns from training data distributions where shorter sequences are typically more common than longer ones. This means models have less experience with, and fewer specialized parameters for, context-wide dependencies.
Techniques like position encoding interpolation allow models to handle longer sequences by adapting them to the originally trained smaller context, though with some degradation in token position understanding. These factors create a performance gradient rather than a hard cliff: models remain highly capable at longer contexts but may show reduced precision for information retrieval and long-range reasoning compared to their performance on shorter contexts.
These realities mean that thoughtful context engineering is essential for building capable agents.
The anatomy of effective context
Given that LLMs are constrained by a finite attention budget, good context engineering means finding the smallest possible set of high-signal tokens that maximize the likelihood of some desired outcome. Implementing this practice is much easier said than done, but in the following section, we outline what this guiding principle means in practice across the different components of context.
System prompts should be extremely clear and use simple, direct language that presents ideas at the right altitude for the agent. The right altitude is the Goldilocks zone between two common failure modes. At one extreme, we see engineers hardcoding complex, brittle logic in their prompts to elicit exact agentic behavior. This approach creates fragility and increases maintenance complexity over time. At the other extreme, engineers sometimes provide vague, high-level guidance that fails to give the LLM concrete signals for desired outputs or falsely assumes shared context. The optimal altitude strikes a balance: specific enough to guide behavior effectively, yet flexible enough to provide the model with strong heuristics to guide behavior.At one end of the spectrum, we see brittle if-else hardcoded prompts, and at the other end we see prompts that are overly general or falsely assume shared context.
We recommend organizing prompts into distinct sections (like <background_information>, <instructions>, ## Tool guidance, ## Output description, etc) and using techniques like XML tagging or Markdown headers to delineate these sections, although the exact formatting of prompts is likely becoming less important as models become more capable.

Regardless of how you decide to structure your system prompt, you should be striving for the minimal set of information that fully outlines your expected behavior. (Note that minimal does not necessarily mean short; you still need to give the agent sufficient information up front to ensure it adheres to the desired behavior.) It’s best to start by testing a minimal prompt with the best model available to see how it performs on your task, and then add clear instructions and examples to improve performance based on failure modes found during initial testing.
Tools allow agents to operate with their environment and pull in new, additional context as they work. Because tools define the contract between agents and their information/action space, it’s extremely important that tools promote efficiency, both by returning information that is token efficient and by encouraging efficient agent behaviors.
In Writing tools for AI agents – with AI agents, we discussed building tools that are well understood by LLMs and have minimal overlap in functionality. Similar to the functions of a well-designed codebase, tools should be self-contained, robust to error, and extremely clear with respect to their intended use. Input parameters should similarly be descriptive, unambiguous, and play to the inherent strengths of the model.
One of the most common failure modes we see is bloated tool sets that cover too much functionality or lead to ambiguous decision points about which tool to use. If a human engineer can’t definitively say which tool should be used in a given situation, an AI agent can’t be expected to do better. As we’ll discuss later, curating a minimal viable set of tools for the agent can also lead to more reliable maintenance and pruning of context over long interactions.
Providing examples, otherwise known as few-shot prompting, is a well known best practice that we continue to strongly advise. However, teams will often stuff a laundry list of edge cases into a prompt in an attempt to articulate every possible rule the LLM should follow for a particular task. We do not recommend this. Instead, we recommend working to curate a set of diverse, canonical examples that effectively portray the expected behavior of the agent. For an LLM, examples are the “pictures” worth a thousand words.
Our overall guidance across the different components of context (system prompts, tools, examples, message history, etc) is to be thoughtful and keep your context informative, yet tight. Now let's dive into dynamically retrieving context at runtime.
Context retrieval and agentic search
In Building effective AI agents, we highlighted the differences between LLM-based workflows and agents. Since we wrote that post, we’ve gravitated towards a simple definition for agents: LLMs autonomously using tools in a loop.
Working alongside our customers, we’ve seen the field converging on this simple paradigm. As the underlying models become more capable, the level of autonomy of agents can scale: smarter models allow agents to independently navigate nuanced problem spaces and recover from errors.
We’re now seeing a shift in how engineers think about designing context for agents. Today, many AI-native applications employ some form of embedding-based pre-inference time retrieval to surface important context for the agent to reason over. As the field transitions to more agentic approaches, we increasingly see teams augmenting these retrieval systems with “just in time” context strategies.
Rather than pre-processing all relevant data up front, agents built with the “just in time” approach maintain lightweight identifiers (file paths, stored queries, web links, etc.) and use these references to dynamically load data into context at runtime using tools. Anthropic’s agentic coding solution Claude Code uses this approach to perform complex data analysis over large databases. The model can write targeted queries, store results, and leverage Bash commands like head and tail to analyze large volumes of data without ever loading the full data objects into context. This approach mirrors human cognition: we generally don’t memorize entire corpuses of information, but rather introduce external organization and indexing systems like file systems, inboxes, and bookmarks to retrieve relevant information on demand.
Beyond storage efficiency, the metadata of these references provides a mechanism to efficiently refine behavior, whether explicitly provided or intuitive. To an agent operating in a file system, the presence of a file named test_utils.py in a tests folder implies a different purpose than a file with the same name located in src/core_logic/ Folder hierarchies, naming conventions, and timestamps all provide important signals that help both humans and agents understand how and when to utilize information.
Letting agents navigate and retrieve data autonomously also enables progressive disclosure—in other words, allows agents to incrementally discover relevant context through exploration. Each interaction yields context that informs the next decision: file sizes suggest complexity; naming conventions hint at purpose; timestamps can be a proxy for relevance. Agents can assemble understanding layer by layer, maintaining only what's necessary in working memory and leveraging note-taking strategies for additional persistence. This self-managed context window keeps the agent focused on relevant subsets rather than drowning in exhaustive but potentially irrelevant information.
Of course, there's a trade-off: runtime exploration is slower than retrieving pre-computed data. Not only that, but opinionated and thoughtful engineering is required to ensure that an LLM has the right tools and heuristics for effectively navigating its information landscape. Without proper guidance, an agent can waste context by misusing tools, chasing dead-ends, or failing to identify key information.
In certain settings, the most effective agents might employ a hybrid strategy, retrieving some data up front for speed, and pursuing further autonomous exploration at its discretion. The decision boundary for the ‘right’ level of autonomy depends on the task. Claude Code is an agent that employs this hybrid model: CLAUDE.md files are naively dropped into context up front, while primitives like glob and grep allow it to navigate its environment and retrieve files just-in-time, effectively bypassing the issues of stale indexing and complex syntax trees.
The hybrid strategy might be better suited for contexts with less dynamic content, such as legal or finance work. As model capabilities improve, agentic design will trend towards letting intelligent models act intelligently, with progressively less human curation. Given the rapid pace of progress in the field, "do the simplest thing that works" will likely remain our best advice for teams building agents on top of Claude.
Context engineering for long-horizon tasks
Long-horizon tasks require agents to maintain coherence, context, and goal-directed behavior over sequences of actions where the token count exceeds the LLM’s context window. For tasks that span tens of minutes to multiple hours of continuous work, like large codebase migrations or comprehensive research projects, agents require specialized techniques to work around the context window size limitation.
Waiting for larger context windows might seem like an obvious tactic. But it's likely that for the foreseeable future, context windows of all sizes will be subject to context pollution and information relevance concerns—at least for situations where the strongest agent performance is desired. To enable agents to work effectively across extended time horizons, we've developed a few techniques that address these context pollution constraints directly: compaction, structured note-taking, and multi-agent architectures.
Compaction
Compaction is the practice of taking a conversation nearing the context window limit, summarizing its contents, and reinitiating a new context window with the summary. Compaction typically serves as the first lever in context engineering to drive better long-term coherence. At its core, compaction distills the contents of a context window in a high-fidelity manner, enabling the agent to continue with minimal performance degradation.
In Claude Code, for example, we implement this by passing the message history to the model to summarize and compress the most critical details. The model preserves architectural decisions, unresolved bugs, and implementation details while discarding redundant tool outputs or messages. The agent can then continue with this compressed context plus the five most recently accessed files. Users get continuity without worrying about context window limitations.
The art of compaction lies in the selection of what to keep versus what to discard, as overly aggressive compaction can result in the loss of subtle but critical context whose importance only becomes apparent later. For engineers implementing compaction systems, we recommend carefully tuning your prompt on  complex agent traces. Start by maximizing recall to ensure your compaction prompt captures every relevant piece of information from the trace, then iterate to improve precision by eliminating superfluous content.
An example of low-hanging superfluous content is clearing tool calls and results – once a tool has been called deep in the message history, why would the agent need to see the raw result again? One of the safest lightest touch forms of compaction is tool result clearing, most recently launched as a feature on the Claude Developer Platform.
Structured note-taking
Structured note-taking, or agentic memory, is a technique where the agent regularly writes notes persisted to memory outside of the context window. These notes get pulled back into the context window at later times.
This strategy provides persistent memory with minimal overhead. Like Claude Code creating a to-do list, or your custom agent maintaining a NOTES.md file, this simple pattern allows the agent to track progress across complex tasks, maintaining critical context and dependencies that would otherwise be lost across dozens of tool calls.
Claude playing Pokémon demonstrates how memory transforms agent capabilities in non-coding domains. The agent maintains precise tallies across thousands of game steps—tracking objectives like "for the last 1,234 steps I've been training my Pokémon in Route 1, Pikachu has gained 8 levels toward the target of 10." Without any prompting about memory structure, it develops maps of explored regions, remembers which key achievements it has unlocked, and maintains strategic notes of combat strategies that help it learn which attacks work best against different opponents.
After context resets, the agent reads its own notes and continues multi-hour training sequences or dungeon explorations. This coherence across summarization steps enables long-horizon strategies that would be impossible when keeping all the information in the LLM’s context window alone.
As part of our Sonnet 4.5 launch, we released a memory tool in public beta on the Claude Developer Platform that makes it easier to store and consult information outside the context window through a file-based system. This allows agents to build up knowledge bases over time, maintain project state across sessions, and reference previous work without keeping everything in context.
Sub-agent architectures
Sub-agent architectures provide another way around context limitations. Rather than one agent attempting to maintain state across an entire project, specialized sub-agents can handle focused tasks with clean context windows. The main agent coordinates with a high-level plan while subagents perform deep technical work or use tools to find relevant information. Each subagent might explore extensively, using tens of thousands of tokens or more, but returns only a condensed, distilled summary of its work (often 1,000-2,000 tokens).
This approach achieves a clear separation of concerns—the detailed search context remains isolated within sub-agents, while the lead agent focuses on synthesizing and analyzing the results. This pattern, discussed in How we built our multi-agent research system, showed a substantial improvement over single-agent systems on complex research tasks.
The choice between these approaches depends on task characteristics. For example:
Compaction maintains conversational flow for tasks requiring extensive back-and-forth;
Note-taking excels for iterative development with clear milestones;
Multi-agent architectures handle complex research and analysis where parallel exploration pays dividends.
Even as models continue to improve, the challenge of maintaining coherence across extended interactions will remain central to building more effective agents.
Conclusion
Context engineering represents a fundamental shift in how we build with LLMs. As models become more capable, the challenge isn't just crafting the perfect prompt—it's thoughtfully curating what information enters the model's limited attention budget at each step. Whether you're implementing compaction for long-horizon tasks, designing token-efficient tools, or enabling agents to explore their environment just-in-time, the guiding principle remains the same: find the smallest set of high-signal tokens that maximize the likelihood of your desired outcome.
The techniques we've outlined will continue evolving as models improve. We're already seeing that smarter models require less prescriptive engineering, allowing agents to operate with more autonomy. But even as capabilities scale, treating context as a precious, finite resource will remain central to building reliable, effective agents.
Get started with context engineering in the Claude Developer Platform today, and access helpful tips and best practices via our memory and context management cookbook.
Acknowledgements
Written by Anthropic's Applied AI team: Prithvi Rajasekaran, Ethan Dixon, Carly Ryan, and Jeremy Hadfield, with contributions from team members Rafi Ayub, Hannah Moran, Cal Rueb, and Connor Jennings. Special thanks to Molly Vorwerck, Stuart Ritchie, and Maggie Vo for their support.

Get the developer newsletter
Product updates, how-tos, community spotlights, and more. Delivered monthly to your inbox.
Please provide your email address if you'd like to receive our monthly developer newsletter. You can unsubscribe at any time.
Products
Claude
Claude Code
Claude Code Enterprise
Claude Cowork
@Claude
Claude Design
Claude Science
Claude Security
Claude in Chrome
Claude for Microsoft 365
Skills
Download app
Pricing
Log in to Claude
Models
Mythos
Fable
Opus
Sonnet
Haiku
Solutions
AI agents
Code modernization
Coding
Commerce
Customer support
Cybersecurity
Enterprise
Financial advisors
Financial services
Government
Healthcare
Higher education
K-12 teachers
Legal
Life sciences
Nonprofits
Sales
Small business
Claude Platform
Overview
Developer docs
Pricing
Ecosystem
Marketplace
Regional compliance
Claude on AWS
Google Cloud
Microsoft Foundry
Console login
Resources
Blog
Claude partner network
Community
Connectors
Courses
Customer stories
Developer blog
Engineering at Anthropic
Events
Plugins
Powered by Claude
Service partners
Tutorials
Use cases
Programs
Campus
Frontier Academy
Scientists
Startups
Help and security
Availability
Status
Support center
Company
Anthropic
Careers
Leadership
Policy
Economic Futures
Research
News
Claude’s Constitution
Claude Corps
Keep thinking
Policy on the AI Exponential
Responsible Scaling Policy
Security and compliance
Transparency
Terms and policies
Privacy policy
Consumer health data privacy policy
Responsible disclosure policy
Terms of service: Commercial
Terms of service: Consumer
Terms of Service: US K-12
Data Processing Agreement: US K-12
Usage policy© 2026 Anthropic PBC


</details>
