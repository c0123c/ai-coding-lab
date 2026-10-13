# 把 Copilot 审查纳入拉取请求生命周期

介绍从草稿、迭代到合并的审查流程，涵盖早期审查、再审查、质量调优、项目指令和技能及 MCP 扩展。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-09-09](<https://github.com/github/docs/commit/df9bd944684a61fc90f8cfda11cf031984998e04>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/tutorials/use-copilot-code-review-across-the-pull-request-lifecycle>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/把 Copilot 审查纳入拉取请求生命周期.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 在拉取请求生命周期中使用 GitHub Copilot 代码审查

为从草稿拉取请求到合并的 Copilot 代码审查构建可重复的工作流程。

## 简介

Copilot 当您将代码审查用作拉取请求工作流程的一部分时，它是最有效的。本教程展示了如何在手动和自动审核之间进行选择，如何在草稿拉取请求中尽早请求审核，以及决定何时在合并之前触发重新审核。

您还将了解何时使用自定义、技能、MCP 服务器和运行器选项来根据您的需求定制 Copilot 的评论。

## 完成拉取请求生命周期

选择审核模型并为草稿和活动拉取请求设置早期反馈。

### 为您的团队选择正确的审核模型

Copilot 代码审查可以在您自己触发时手动运行，也可以在拉取请求生命周期中的一个或多个点自动运行。手动审核使您能够准确控制 Copilot 代码审核发挥作用的时间。自动审核可确保一致的覆盖范围，并且您可以选择它们是在打开时、草稿时还是每次新推送时触发。

|审查模型|最适合|为什么|
| --------------------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------- |
|手动审核请求|高上下文变化或选择性使用|只有当你想要另一个视角时才可以触发Copilot。|
|自动审查开放的拉取请求|想要广泛、一致的覆盖范围的团队|每个拉取请求都会获得基线反馈，而不依赖于个人习惯。|
|自动审查草稿拉取请求|想要更早反馈循环的团队|作者可以在人工审核开始之前解决问题。|
|自动审核新推送|具有多个修订的快速移动拉取请求|Copilot 随着拉取请求的发展重新评估实质性更新。|

有关设置步骤，请参阅[使用GitHub Copilot代码审查](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)和[通过GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>)配置代码审查。

### 尽早对拉取请求草稿使用 Copilot 代码审查

通过 Copilot 代码审查来审查草稿拉取请求可以让您在向团队请求审查之前对代码进行早期、可靠的检查。

一个有用的草稿工作流程是：

1. 打开拉取请求作为草稿。
2. 请求 ** 审阅者 ** 下的 Copilot 代码审阅进行审阅，或启用草稿拉取请求的自动审阅。
3. 首先解决高可信度反馈，例如正确性、安全性和明确的可维护性问题。
4. 推送更新并确认主要评论已得到解决。
5. 将拉取请求标记为可供人工审核。

这种方法可以帮助您的团队将更多的人工审查时间花在设计权衡和产品影响上，而不是本来可以更早发现的明显问题。

## 调整评论质量

通过决定何时请求重新审核以及定制、外部环境和跑步者在何处增加价值来提高审核质量。

### 合并前使用重新审核

发生重大变更后，请求重新审查，以检查在解决早期反馈的同时是否引入了新的风险。

当您出现以下情况时，请求重新审核：

* 跨服务或包边界更新多个文件。
* 改变安全敏感或数据敏感的行为。
* 应用大量建议的更改并希望获得最终通过。

对于经常更新活动拉取请求的团队来说，启用对新推送的自动审查可以减少手动重新审查请求，同时保持反馈最新。

### 将定制与您的工作流程相匹配

当每种文件类型支持工作流程中的审核决策点时，自定义最为有用：

* 使用 `.github/copilot-instructions.md` 来获得适用于大多数拉取请求的存储库范围的审查期望。
* 当代码库的不同部分具有不同的标准时，使用 `.github/instructions/**/*.instructions.md` 获取 agent 特定和路径特定的指导。
* 使用 `AGENTS.md` 提供可提高相关性的存储库上下文，例如有意模式、架构边界或审查优先级。

让这些文件重点关注审阅者在拉取请求期间需要决定的内容。避免将它们变成完整的工程手册。

有关详细的自定义机制和示例，请参阅[使用自定义指令解锁Copilot代码审查的强大功能](<https://docs.github.com/en/copilot/tutorials/customize-code-review>)。

### 使用 agent skills 和 MCP 服务器实现专门的审核目标

当您的团队需要根据组织特定上下文进行审核时，Agent skills 和 MCP 服务器最有价值。

当您想要可重复使用的审阅例程时，请使用 agent skills，例如：

* 将标准清单应用于迁移拉取请求。
* 在单一存储库中运行特定于语言或框架的审查。
* 优先考虑某些风险领域，例如授权或计费逻辑。

当评论应该引用拉取请求本身之外的上下文时，请使用 MCP 服务器，例如：

* 将拉取请求更改链接到问题、事件或服务所有权数据。
* 检查内部系统的预期部署或操作环境。
* 根据外部文档来源验证实施细节。

有关设置详细信息，请参阅[为GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills>)添加agent skills和[为存储库配置MCP服务器](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)。

### 决定是否需要自托管运行器

在许多情况下，标准的 GitHub 托管运行程序足以进行 Copilot 代码审查，并且无需设置；它们是默认为您配置的。

当您需要时，请考虑自托管或更大的运行器：

* 为较重的审核工作负载提供更好的性能。
* 您的组织所需的对内部系统的网络访问。

需要运行程序，因为 Copilot 代码审查使用 GitHub 操作进行代理操作，例如更深入的上下文收集和工具调用。如果 GitHub 托管的运行器不可用并且没有正确配置受支持的自托管选项，评论将回退到更有限的模式。

有关规划和设置的详细信息，请参阅 [为 GitHub Copilot 代码审查配置运行器 ](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-runners>)。

## 按场景推荐的工作流程

|团队场景|推荐的工作流程|
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|频繁拉取请求的小团队|对所有拉取请求启用自动审查，审查草稿以获取早期反馈，并在发生重大更改时在合并之前使用手动重新审查。|
|具有混合堆栈的大型单一存储库|启用自动审核并审核新推送，为每个主要区域添加特定于路径的说明，并使用重复审核模式的技能。|
|安全敏感或受监管的代码库|使用草稿拉取请求审查以及合并前所需的重新审查，将存储库范围的安全期望保留在 `.github/copilot-instructions.md` 中，并使用 MCP 服务器来存储 GitHub 外部的事件或合规性上下文。|
|具有内部平台依赖性的团队|从自动审核开始，然后仅在需要内部系统访问以获得有用的审核反馈时添加自托管运行器和 MCP 上下文。|

## 后续步骤

* 有关更广泛的概念，包括审核工作和代理功能，请参阅[关于GitHub Copilot代码审核](<https://docs.github.com/en/copilot/concepts/agents/code-review>)。
* 要调整特定存储库的自动审核质量，请参阅 [使用 Copilot](<https://docs.github.com/en/copilot/tutorials/optimize-code-reviews>) 构建优化的审核流程。
* 有关排除的文件类型和其他限制，请参阅 [从 GitHub Copilot 代码审查中排除的文件 ](<https://docs.github.com/en/copilot/reference/review-excluded-files>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Use GitHub Copilot code review across the pull request lifecycle

Build a repeatable workflow for Copilot code review from draft pull request to merge.

## Introduction

Copilot code review is most effective when you use it as part of a pull request workflow. This tutorial shows how to choose between manual and automatic reviews, request reviews early on draft pull requests, and decide when to trigger re-review before merge.

You will also learn when to use customization, skills, MCP servers, and runner options to tailor Copilot's reviews to your needs.

## Work through the pull request lifecycle

Choose a review model and set up early feedback for draft and active pull requests.

### Choose the right review model for your team

Copilot code review can run manually, when you trigger it yourself, or automatically, at one or more points in a pull request's lifecycle. Manual reviews give you control over exactly when Copilot code review weighs in. Automatic reviews ensure consistent coverage, and you can choose whether they trigger on open, on draft, or on every new push.

| Review model                            | Best for                                          | Why                                                                             |
| --------------------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------- |
| Manual review request                   | High-context changes or selective use             | You can trigger Copilot only when you want another perspective.                 |
| Automatic review on open pull requests  | Teams that want broad, consistent coverage        | Every pull request gets baseline feedback without relying on individual habits. |
| Automatic review on draft pull requests | Teams that want earlier feedback loops            | Authors can fix issues before human review starts.                              |
| Automatic review on new pushes          | Fast-moving pull requests with multiple revisions | Copilot reevaluates substantial updates as the pull request evolves.            |

For setup steps, see [Using GitHub Copilot code review](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>) and [Configuring code review by GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>).

### Use Copilot code review early on draft pull requests

Reviewing draft pull requests with Copilot code review gives you an early, dependable check on your code before you request a review from your team.

A useful draft workflow is:

1. Open a pull request as draft.
2. Request a review from Copilot code review under **Reviewers**, or enable automatic review for draft pull requests.
3. Address high-confidence feedback first, such as correctness, security, and clear maintainability issues.
4. Push updates and confirm that major comments have been resolved.
5. Mark the pull request as ready for human review.

This approach helps your team spend more human review time on design tradeoffs and product impact, instead of obvious issues that could have been caught earlier.

## Tune review quality

Improve review quality by deciding when to request re-review and where customization, external context, and runners add value.

### Use re-review before merge

After substantial changes, request re-review to check whether new risks were introduced while addressing earlier feedback.

Request re-review when you:

* Update multiple files across service or package boundaries.
* Change security-sensitive or data-sensitive behavior.
* Apply a large batch of suggested changes and want a final pass.

For teams with frequent updates to active pull requests, enabling automatic reviews on new pushes can reduce manual re-review requests while keeping feedback current.

### Match customization to your workflow

Customization is most useful when each file type supports a review decision point in your workflow:

* Use `.github/copilot-instructions.md` for repository-wide review expectations that should apply to most pull requests.
* Use `.github/instructions/**/*.instructions.md` for agent-specific and path-specific guidance when different parts of the codebase have different standards.
* Use `AGENTS.md` to provide repository context that improves relevance, such as intentional patterns, architecture boundaries, or review priorities.

Keep these files focused on what reviewers need to decide during pull requests. Avoid turning them into full engineering handbooks.

For detailed customization mechanics and examples, see [Using custom instructions to unlock the power of Copilot code review](<https://docs.github.com/en/copilot/tutorials/customize-code-review>).

### Use agent skills and MCP servers for specialized review goals

Agent skills and MCP servers are most valuable when your team needs reviews that depend on organization-specific context.

Use agent skills when you want reusable review routines, for example:

* Applying a standard checklist to migration pull requests.
* Running language- or framework-specific review passes in a monorepo.
* Prioritizing certain risk areas, such as authorization or billing logic.

Use MCP servers when reviews should reference context outside the pull request itself, for example:

* Linking pull request changes to issue, incident, or service ownership data.
* Checking expected rollout or operational context from internal systems.
* Validating implementation details against external documentation sources.

For setup details, see [Adding agent skills for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills>) and [Configure MCP servers for your repository](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>).

### Decide whether you need self-hosted runners

In many cases, standard GitHub-hosted runners are sufficient for Copilot code review and require no setup; they are configured for you by default.

Consider self-hosted or larger runners when you need:

* Better performance for heavier review workloads.
* Network access to internal systems required by your organization.

Runners are required because Copilot code review uses GitHub Actions for agentic operations, such as deeper context gathering and tool-calling. If GitHub-hosted runners are unavailable and no supported self-hosted option is configured correctly, reviews will fall back to a more limited mode.

For planning and setup details, see [Configuring runners for GitHub Copilot code review](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-runners>).

## Recommended workflows by scenario

| Team scenario                            | Recommended workflow                                                                                                                                                                                                                   |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Small team with frequent pull requests   | Enable automatic reviews for all pull requests, review drafts for early feedback, and use manual re-review before merge when changes are substantial.                                                                                  |
| Large monorepo with mixed stacks         | Enable automatic reviews and review new pushes, add path-specific instructions for each major area, and use skills for repeated review patterns.                                                                                       |
| Security-sensitive or regulated codebase | Use draft pull request reviews plus required re-review before merge, keep repository-wide security expectations in `.github/copilot-instructions.md`, and use MCP servers for incident or compliance context stored outside of GitHub. |
| Team with internal platform dependencies | Start with automatic review, then add self-hosted runners and MCP context only when internal-system access is required for useful review feedback.                                                                                     |

## Next steps

* For broader concepts, including review effort and agentic capabilities, see [About GitHub Copilot code review](<https://docs.github.com/en/copilot/concepts/agents/code-review>).
* To tune automated review quality for specific repositories, see [Build an optimized review process with Copilot](<https://docs.github.com/en/copilot/tutorials/optimize-code-reviews>).
* For excluded file types and other limits, see [Files excluded from GitHub Copilot code review](<https://docs.github.com/en/copilot/reference/review-excluded-files>).


</details>
