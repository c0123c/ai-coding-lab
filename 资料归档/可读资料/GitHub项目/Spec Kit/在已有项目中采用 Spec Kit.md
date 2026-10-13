# 在已有项目中采用 Spec Kit

介绍保留可审查基线、补充项目约束、选择有限变更、结合仓库制定计划，以及处理规格长期维护的方式。

- 来源：Spec Kit
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-09-15](<https://github.com/github/spec-kit/commit/3affdb2c53b9ac87981cf46a21582526820b8ba4>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.github.com/spec-kit/guides/existing-projects.html>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/在已有项目中采用 Spec Kit.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 在现有项目中采用规格套件

在使用
规格套件之前，您无需根据规格重新创建现有系统。就地初始化存储库，捕获重要的规则，
并使用工作流程进行下一个有界更改。

## 1。从可审查的基线开始

在初始化之前，提交或隐藏现有工作并为 
 采用创建一个分支。这使得每个生成的文件在正常的代码审查中可见。

选择 [集成密钥](<https://github.github.com/spec-kit/reference/integrations.html>) 用于编码 agent
你用。然后从存储库根运行命令：

```
specify init --here --force --integration <key>
```

`--here` 目标当前目录。 `--force` 允许在 
 非空目录中进行初始化，并且可能会替换冲突的托管路径中的文件，因此仅在创建可审查的基线后才使用 
。它不会删除您的应用程序的其余部分
。

在继续之前查看生成的差异。初始化会添加共享的
`.specify/`项目文件以及
所选集成所需的命令或技能文件。它不会重写您的应用程序或推断现有行为的
规范。

##### 注意事项

Git 初始化和功能分支是可选的，由 
**git** 扩展管理。如果您想要 
 工作流程，请添加 `specify extension add git`。

## 2。捕获项目护栏

其余步骤说明采用 **SDD** 作为有界特征。如果您的
第一个任务是修复，您可以在初始化后按照
[错误修复快速入门](<https://github.github.com/spec-kit/guides/bugfix.html>)进行操作；要评估提案，
使用[想法评估快速入门](<https://github.github.com/spec-kit/guides/assessment.html>)。这两个过程都不​​需要
首先完成下面的SDD步骤。

使用 
 存储库已经适用的原则或团队已明确同意采用的原则运行 `/speckit.constitution`：

```
/speckit.constitution Preserve public API compatibility. Follow the existing
service boundaries. Every database migration must include a rollback plan.
Run the repository's established unit and integration test suites.
```

使用存储库的 README、架构决策、贡献指南和 
CI 配置作为证据。不要仅仅为了填充
宪法模板而发明标准。宪法管辖后来的规划和分析，
因此不切实际的规则会产生噪音而不是有用的约束。

## 3。选择有界的第一个变化

从可以独立审查的功能或现代化切片开始
。不要将“记录整个现有系统”作为您的第一个 
 功能，除非该清单本身就是预期的可交付成果。

描述所请求的结果和必须
保持完整的兼容性边界：

```
/speckit.specify Add CSV export to the existing orders page. Preserve current
filters and authorization behavior. Export only the rows visible to the signed-in
user, and do not change the existing JSON API response.
```

代码库仍然是实现上下文。新的 `spec.md` 定义了您打算进行的 
 更改，而不是每个现有 
 行为的追溯规范。

## 4。针对存储库进行计划

Continue通过正常工作流程：

1. 运行 `/speckit.clarify` 来解决不确定的行为和兼容性
要求。
2. 运行 `/speckit.plan` 并验证所提出的设计是否重用了现有的
架构、依赖关系和测试约定。
3. 运行`/speckit.tasks`，然后运行`/speckit.analyze`以检查之前的一致性
实施。
4. 运行 `/speckit.implement` 并一起审查代码和工件更改。
5. 运行 `/speckit.converge` 查找剩余间隙。如果添加了任务，则重复
实施和收敛，直到功能完成。

有关命令详细信息和可选质量门，请参阅
[快速入门指南](<https://github.github.com/spec-kit/quickstart.html>)和
[代理SDD参考](<https://github.github.com/spec-kit/reference/agentic-sdd.html>)。

## 5。决定规格如何老化

第一次更改后，就团队如何维护已完成的功能 
 工件达成一致：

* 将每个功能目录保留为不可变的历史记录。
* 维护 `spec.md` 作为活合约并重新生成下游工件。
* 允许从代码、任务或计划中回流发现，然后协调
完整的工件集。

[规格持久模型](<https://github.github.com/spec-kit/concepts/spec-persistence.html>)页面比较
这些选择。 [不断发展的规格指南](<https://github.github.com/spec-kit/guides/evolving-specs.html>)为每个型号提供了
维护循环。

## 现有项目示例

[ 社区演练 ](<https://github.github.com/spec-kit/community/walkthroughs.html>) 包括跨 .NET、Java 和 Go/React 代码库的棕地 
 示例。 
架构发现和棕地引导的社区扩展列在
[扩展目录](<https://github.github.com/spec-kit/community/extensions.html>)中。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Adopting Spec Kit in an Existing Project

You do not need to recreate an existing system from specifications before using
Spec Kit. Initialize the repository in place, capture the rules that matter,
and use the workflow for the next bounded change.

## 1. Start from a Reviewable Baseline

Before initialization, commit or stash existing work and create a branch for the
adoption. This makes every generated file visible in a normal code review.

Choose the [integration key](<https://github.github.com/spec-kit/reference/integrations.html>) for the coding agent
you use. Then run the command from the repository root:

```
specify init --here --force --integration <key>
```

`--here` targets the current directory. `--force` allows initialization in a
non-empty directory and may replace files at conflicting managed paths, so use
it only after creating a reviewable baseline. It does not delete the rest of
your application.

Review the resulting diff before continuing. Initialization adds the shared
`.specify/` project files and the command or skill files required by your
selected integration. It does not rewrite your application or infer
specifications for existing behavior.

##### Note

Git initialization and feature branches are optional and are managed by the
**git** extension. Add it with `specify extension add git` if you want that
workflow.

## 2. Capture Project Guardrails

The remaining steps illustrate adopting **SDD** for a bounded feature. If your
first task is a repair, you can instead follow the
[bug-fixing quickstart](<https://github.github.com/spec-kit/guides/bugfix.html>) after initialization; to evaluate a proposal,
use the [idea assessment quickstart](<https://github.github.com/spec-kit/guides/assessment.html>). Neither process requires
completing the SDD steps below first.

Run `/speckit.constitution` with principles that are already true for the
repository or that the team has explicitly agreed to adopt:

```
/speckit.constitution Preserve public API compatibility. Follow the existing
service boundaries. Every database migration must include a rollback plan.
Run the repository's established unit and integration test suites.
```

Use the repository's README, architecture decisions, contribution guide, and
CI configuration as evidence. Do not invent standards merely to fill the
constitution template. The constitution governs later planning and analysis,
so unrealistic rules create noise instead of useful constraints.

## 3. Choose a Bounded First Change

Start with a feature or modernization slice that can be reviewed
independently. Do not make "document the entire existing system" your first
feature unless that inventory is itself the intended deliverable.

Describe both the requested outcome and the compatibility boundaries that must
remain intact:

```
/speckit.specify Add CSV export to the existing orders page. Preserve current
filters and authorization behavior. Export only the rows visible to the signed-in
user, and do not change the existing JSON API response.
```

The codebase remains implementation context. The new `spec.md` defines the
change you intend to make, not a retroactive specification of every existing
behavior.

## 4. Plan Against the Repository

Continue through the normal workflow:

1. Run `/speckit.clarify` to resolve uncertain behavior and compatibility
   requirements.
2. Run `/speckit.plan` and verify that the proposed design reuses the existing
   architecture, dependencies, and test conventions.
3. Run `/speckit.tasks`, then `/speckit.analyze` to check consistency before
   implementation.
4. Run `/speckit.implement` and review code and artifact changes together.
5. Run `/speckit.converge` to find remaining gaps. If it adds tasks, repeat
   implementation and convergence until the feature is complete.

For command details and optional quality gates, see the
[Quick Start Guide](<https://github.github.com/spec-kit/quickstart.html>) and
[Agentic SDD reference](<https://github.github.com/spec-kit/reference/agentic-sdd.html>).

## 5. Decide How Specs Will Age

After the first change, agree on how the team will maintain completed feature
artifacts:

* Keep each feature directory as an immutable historical record.
* Maintain `spec.md` as a living contract and regenerate downstream artifacts.
* Allow discoveries to flow back from code, tasks, or plans, then reconcile the
  full artifact set.

The [Spec Persistence Models](<https://github.github.com/spec-kit/concepts/spec-persistence.html>) page compares
these choices. The [Evolving Specs guide](<https://github.github.com/spec-kit/guides/evolving-specs.html>) provides the
maintenance loop for each model.

## Existing-Project Examples

The [community walkthroughs](<https://github.github.com/spec-kit/community/walkthroughs.html>) include brownfield
examples across .NET, Java, and Go/React codebases. Community extensions for
architecture discovery and brownfield bootstrapping are listed in the
[extension catalog](<https://github.github.com/spec-kit/community/extensions.html>).


</details>
