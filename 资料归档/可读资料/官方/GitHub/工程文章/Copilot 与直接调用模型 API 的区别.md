# Copilot 与直接调用模型 API 的区别

比较模型访问与开发工作流、权限和运行框架的关系，说明自带密钥与使用成本的考虑；价格信息以归档当时为准。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-07-22](<https://github.blog/ai-and-ml/github-copilot/copilot-vs-raw-api-access-what-are-you-actually-paying-for/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/copilot-vs-raw-api-access-what-are-you-actually-paying-for/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/Copilot 与直接调用模型 API 的区别.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Copilot 与原始 API 访问：您实际支付的费用是什么？

我不断看到这个问题：“当我可以通过 API 调用相同的模型时，为什么我要支付 GitHub Copilot 费用？”

这是一个公平的问题。答案取决于您需要拥有什么工作。

您是否正在使用自己的提示、检索、路由、日志、安全模型和计费控制来构建产品功能？或者您是否正在尝试从 GitHub 问题获取已审核的拉取请求，并且已连接编辑器、存储库、终端和组织策略？

成本是这个等式的一部分。 Copilot 计划包括每月分配 GitHub AI 积分。计量使用量是根据输入、输出和缓存令牌按照所选模型列出的速率计算得出的。

原始 API 访问和 Copilot 寻址该系统的不同层。正确的选择取决于您需要拥有的工作。

## Copilot 是围绕模型的开发工具

现在执行一个常见的维护任务：开发人员从 GitHub 问题开始，检查存储库，更改受影响的文件，在终端中运行测试套件，然后打开拉取请求以供审核。模型调用是该工作流程中的一个步骤。周围系统需要问题、差异、存储库指令、允许的命令和组织的策略。

GitHub Copilot 将这些界面连接到编辑器、存储库、拉取请求、问题、终端和组织控件中。这就是该计划与模型访问一起涵盖的内容。计费更改使拆分更容易看到：代码完成和下一步编辑建议仍然包含在付费计划中，而 AI 积分适用于资源密集型聊天和代理工作。

因此，每个任务的成本不仅仅取决于列出的令牌率。上下文选择、工具使用、重试以及从问题到审核拉取请求的路径都会影响花费的代币数量以及工作是否完成。

相同的计费模式为买家提供了可见性。组织计划在整个组织内“池”AI 积分，管理员可以在计费仪表板中设置预算并跟踪使用情况。采用率保持可衡量，而不是分散在各个 API 密钥和未跟踪的脚本中。

## 原始 API 访问权限适用于您拥有的系统

当您构建产品功能、内部 agent 平台、评估工具或自动化管道时，直接 API 访问是正确的基础。您可以控制提示、检索、路由、重试、日志、安全模型和计费。

考虑一个内部 agent，它读取标记的问题、检索公司文档、在单独的系统中创建变更请求，并写入完整的审核记录。该工作流需要自己的数据边界、事件触发器和批准点。 API 为团队提供了将这些需求构建到产品中的原语。

工程工作是真实的。生产系统需要决定要检索哪些存储库文件、如何保留指令、何时重试失败的工具调用、在哪里存储跟踪以及 agent 可以使用哪些凭据。这些是开发人员做出的系统设计决策。模型端点并不适合您。

Agent SDK 位于这些层之间。处理编排、工具使用、会话和流媒体，需要进行一些权衡：有些与单个提供商的 API 相关，而另一些则跨提供商工作。 GitHub 运送此层。 Copilot SDK 公开了与 Copilot CLI 相同的 agent 运行时，因此您可以嵌入一个经过基准测试、经过生产测试的线束，而不是构建一个。使用您的 Copilot 订阅或您自己的提供商密钥运行它。

## BYOK保持工作流程并更改账单

目前正在公开预览中的“自带 Copilot 密钥”可让开发人员在 Copilot 聊天、Copilot、CLI 和 VS Code 中提供受支持的提供程序模型。支持的提供程序包括 Anthropic、AWS Bedrock、Google AI Studio、Microsoft Foundry、OpenAI、OpenAI 兼容提供程序和 xAI。

BYOK 模型通过相同的线束运行，并且 GitHub 构建和维护相同的集成。您的提供商接管代币账单。 GitHub 仍在开发工具。

无论哪种方式，模型访问都是一项政策决定。 Copilot 支持多个 20 模型，企业和组织管理员可以选择为其团队启用哪些模型，无论是 GitHub 托管还是通过 BYOK 连接。

当开发人员在正常工作流程中使用 Copilot 时，拥有现有提供商合同或承诺云支出的团队可以保持这种商业关系。 Copilot CLI 还支持本地和外部 BYOK 模型，包括 OpenAI 兼容端点、Azure OpenAI、Anthropic 和本地 Ollama 模型。

检查 [ 的当前文档，使用您自己的 API 密钥和 GitHub Copilot（企业）](<https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/use-your-own-api-keys>) 和 [，在 Copilot CLI](<https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models>) 中使用您自己的 LLM 型号在做出购买或架构决策之前，因为 BYOK 仍处于公共预览阶段。

## 选择您需要的图层

当您构建需要自定义行为、集成和控制的系统时，请选择原始 API 访问权限。当工作是在工具和存储库中进行软件开发时，选择 GitHub Copilot，团队已经在其中编写、审查、保护和交付代码。

交付软件是围绕代码进行的工作：问题、拉取请求、审查、检查、操作和安全性。 GitHub 是团队进行这项工作的地方。 Copilot 帮助他们更快地完成任务。

查看每个 [Copilot 计划包含哪些内容以及 AI 积分如何运作 ](<https://github.com/features/copilot/plans>)。

---

## 标签：

* [AI 制作人员](<https://github.blog/tag/ai-credits/>)
* [API](<https://github.blog/tag/api/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## 撰写者

![安德里亚·格里菲斯](<https://github.blog/wp-content/uploads/2025/08/Andrea-Griffiths_avatar_1755783168-200x200.jpeg>)

### [安德里亚·格里菲斯](<https://github.blog/author/andreagriffiths11/>)

[@AndreaGriffiths11](<https://github.com/andreagriffiths11>)

Andrea 是 GitHub 的高级开发人员倡导者，在开发人员工具方面拥有十多年的经验。她将技术深度与让先进技术更容易获得的使命结合起来。从陆军服役和施工管理转向软件开发后，她以独特的视角将复杂的工程概念与实际实施联系起来。她与威尔士伴侣、两个儿子和两只狗住在佛罗里达州，在那里她继续通过 GitHub 的全球计划推动创新并支持开源。在线查找她@acolombiadev。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Copilot vs. raw API access: What are you actually paying for?

I keep seeing this question: “Why would I pay for GitHub Copilot when I can call the same models through an API?”

It’s a fair question. The answer depends on what work you need to own.

Are you building a product feature with your own prompts, retrieval, routing, logs, security model, and billing controls? Or are you trying to get from a GitHub Issue to a reviewed pull request with the editor, repository, terminal, and organization policies already connected?

Cost is part of that equation. Copilot plans include a monthly allocation of GitHub AI Credits. Metered usage is calculated from input, output, and cached tokens at the listed rate for the selected model.

Raw API access and Copilot address different layers of that system. The right choice follows the work you need to own.

## Copilot is development tooling around the model

Now take a common maintenance task: a developer starts from a GitHub Issue, inspects the repository, changes the affected files, runs the test suite in the terminal, and opens a pull request for review. The model call is one step in that workflow. The surrounding system needs the issue, the diff, repository instructions, permitted commands, and the organization’s policies.

GitHub Copilot connects those surfaces across the editor, repository, pull request, issue, terminal, and organization controls. That is what the plan covers alongside model access. The billing change makes the split easier to see: code completions and Next Edit Suggestions remain included in paid plans, while AI Credits apply to more resource-intensive chat and agentic work.

Cost per task therefore depends on more than the listed token rate. Context selection, tool use, retries, and the path from an issue to a reviewed pull request all affect the number of tokens spent and whether the work finishes.

The same billing model gives buyers visibility. Organization plans “pool” AI Credits across the organization, and admins can set budgets and track usage in the billing dashboard. Adoption stays measurable instead of scattering across individual API keys and untracked scripts.

## Raw API access is for systems you own

Direct API access is the right foundation when you are building a product feature, an internal agent platform, an evaluation harness, or an automation pipeline. You control the prompts, retrieval, routing, retries, logs, security model, and billing.

Consider an internal agent that reads a tagged issue, retrieves company documentation, creates a change request in a separate system, and writes a complete audit record. That workflow needs its own data boundaries, event triggers, and approval points. An API gives the team the primitives to build those requirements into the product.

The engineering work is real. A production system needs to decide which repository files to retrieve, how to preserve instructions, when to retry a failed tool call, where to store traces, and which credentials an agent can use. Those are system design decisions made by developers. A model endpoint does not make them for you.

Agent SDKs sit between these layers. Handling orchestration, tool use, sessions and streaming, with some tradeoffs: some are tied to a single provider’s API while others work across providers. GitHub ships this layer. The Copilot SDK exposes the same agent’s runtime that powers the Copilot CLI, so you can embed a benchmarked, production tested harness instead of building one. Run it with your Copilot subscription or your own provider key.

## BYOK keeps the workflow and changes the bill

Bring Your Own Key for Copilot, currently in public preview, lets developers make supported provider models available in Copilot Chat, Copilot CLI, and VS Code. Supported providers include Anthropic, AWS Bedrock, Google AI Studio, Microsoft Foundry, OpenAI, OpenAI-compatible providers, and xAI.

BYOK models run through the same harness and the same integrations GitHub builds and maintains. Your provider takes over the token bill. GitHub still develops the tooling.

Model access is a policy decision either way. Copilot supports more than 20 models, and enterprise and organization admins choose which ones are enabled for their teams, whether GitHub-hosted or connected through BYOK.

A team with an existing provider contract or committed cloud spend can keep that commercial relationship while developers use Copilot in their normal workflow. Copilot CLI also supports local and external BYOK models, including OpenAI-compatible endpoints, Azure OpenAI, Anthropic, and local Ollama models.

Check the current documentation on [using your own API keys with GitHub Copilot (enterprise)](<https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/use-your-own-api-keys>) and [using your own LLM models in Copilot CLI](<https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models>) before making purchasing or architecture decisions because BYOK is still in public preview.

## Choose the layer you need

Choose raw API access when you are building a system that requires custom behavior, integrations, and controls. Choose GitHub Copilot when the work is software development inside the tools and repositories where a team already writes, reviews, secures, and ships code.

Shipping software is the work around the code: issues, pull requests, reviews, checks, actions, and security. GitHub is where teams do that work. Copilot helps them move through it faster.

See what each [Copilot plan includes and how AI Credits work](<https://github.com/features/copilot/plans>).

---

## Tags:

* [AI Credits](<https://github.blog/tag/ai-credits/>)
* [API](<https://github.blog/tag/api/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## Written by

![Andrea Griffiths](<https://github.blog/wp-content/uploads/2025/08/Andrea-Griffiths_avatar_1755783168-200x200.jpeg>)

### [Andrea Griffiths](<https://github.blog/author/andreagriffiths11/>)

[@AndreaGriffiths11](<https://github.com/andreagriffiths11>)

Andrea is a Senior Developer Advocate at GitHub with over a decade of experience in developer tools. She combines technical depth with a mission to make advanced technologies more accessible. After transitioning from Army service and construction management to software development, she brings a unique perspective to bridging complex engineering concepts with practical implementation. She lives in Florida with her Welsh partner, two sons, and two dogs, where she continues to drive innovation and support open source through GitHub's global initiatives. Find her online @acolombiadev.


</details>
