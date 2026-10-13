# 深入使用 Copilot 的 Agent 能力

涵盖系统设计、任务拆分、模块化开发、迁移发布、重构与扩展，说明如何在架构约束下使用 Agent 推进工程工作。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-02-02](<https://github.blog/ai-and-ml/github-copilot/how-to-maximize-github-copilots-agentic-capabilities/>)；更新：[2026-04-21](<https://github.blog/ai-and-ml/github-copilot/how-to-maximize-github-copilots-agentic-capabilities/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/how-to-maximize-github-copilots-agentic-capabilities/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/深入使用 Copilot 的 Agent 能力.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 如何最大化GitHub Copilot的代理能力

现代工程工作很少集中在一个文件中。真实的系统是经过多年逐步分层的决策而演变的——有些是好的，有些是偶然的。单个功能请求（“向注释添加标签”、“重构验证层”、“在我们的 API 上支持新消费者”）通常会涉及控制器、域模型、存储库、迁移、测试、文档和部署策略。

在这些情况下，Copilot 的代理能力不会取代你的判断——它们会放大你的判断。如果使用得当，Copilot 会成为系统设计、重构、现代化和多文件协调方面的合作伙伴。

本指南重点关注工程师每天使用的架构感知、多步骤工作流程，但编写的目的是为了让想要了解的早期职业工程师可以理解 *如何* 高级工程师的想法——以及如何 Copilot 可以加速自己的成长。

它借鉴了四个 [GitHub 技能练习 ](<https://learn.github.com/skills>)（链接如下），并构建了一个完整的真实场景：使用标记子系统扩展小型模块化 Notes 服务、重构验证层、设计安全迁移以及现代化测试。

---

## 开始之前

如果您具备以下条件，您将能够从本指南中获得最大收益：

* GitHub Copilot 启用 agent 模式
* 对服务层架构有一定的了解（Node、Python、Go——语言并不重要
* 将 GitHub 技能练习模板复制到您的用户名或组织（使用绿色的“复制练习”按钮）
* 愿意让 Copilot 提出解决方案——以及检查和挑战这些解决方案的判断力

如果您处于职业生涯的早期，请不要担心。每个部分都解释了**为什么**这些模式很重要以及如何安全地练习它们。

---

## 使用Copilot进行系统设计和分解（不仅仅是脚手架）

高级工程师很少从编写代码开始。他们首先确定边界：领域逻辑、数据访问、接口以及模块应如何交互。

Copilot agent 模式可以通过揭示结构问题和提出架构来提供帮助。

**提示：**

```
Analyze this service and propose a modular decomposition with domain, infrastructure, and interface layers.

Identify anti-patterns, coupling issues, and potential failure points.
```

您通常会得到：

* 建议的模块边界
* 跨层耦合问题
* Async/transaction 陷阱
* 职责重复或紧密交织
* 可测试性和可观察性影响

这将 Copilot 从自动完成工具转变为设计审阅器。

您可以通过要求它比较架构来进一步推进：

```
Compare hexagonal architecture vs. layered architecture for this codebase.

Recommend one based on the constraints here. Include tradeoffs.
```

想亲自尝试一下吗？使用这些建议作为起点。

## 使用代理工作流程构建模块化服务

一旦定义了边界，Copilot 就可以协调跨模块的更改。

**提示符**：

```
Implement the domain, controller, and repository layers as distinct modules.

Use dependency inversion to reduce coupling.

Document assumptions and contracts for each module.
```

Copilot 通常会生成：

* 领域模型接口
* 存储库抽象
* 控制器逻辑调用域服务
* 描述每个模块的简短 Markdown 摘要

对于职业生涯早期的工程师来说，这可以让他们接触到真正的工程模式。对于高级工程师来说，它提供了杠杆作用并减少了样板开销。

## 具有架构意识的功能工作（例如：标记子系统）

添加标记子系统是一个看似简单的请求，但具有有意义的架构含义。

即使是这个单一功能也会迫使整个系统做出决策：

* **数据建模：**嵌入式标签与规范化表与多对多关系
* **搜索行为：**标签如何影响索引、过滤和相关性
* **API 合约：** 标签是一级资源还是实现细节
* **验证边界：** 强制执行约束和不变量的地方
* **迁移和推出：**附加与重大更改和回滚策略

在接触代码之前，请 Copilot 绘制影响图。

**提示符**：

```
Propose the architectural changes required to add a tagging subsystem.

Identify migration needs, cross-cutting concerns, caching or indexing implications, and potential regressions.
```

Copilot 可能会识别：

* 标签-注释关系（一对多或多对多）
* 迁移策略
* 对搜索逻辑的影响
* 所需的测试更新
* 验证逻辑的变化
* 对外部 API 消费者的影响

这是Copilot可以帮助初级开发人员采用的员工级镜头。

**然后实现：**

```
Implement the tagging domain model, schema changes, repository updates, and controller logic.

Update tests and documentation. Show each change as a diff.
```

示例输出（简化）

**迁移示例：**

```
ALTER TABLE notes ADD COLUMN tags TEXT DEFAULT '[]';
```

**域模型示例：**

```
export interface Tag {
  id: string;
  label: string;
}

export interface Note {
  id: string;
  title: string;
  body: string;
  tags: Tag[];
}
```

**控制器更新（部分）：**

```
await noteService.addTag(noteId, { label: req.body.label });
```

这就是 agent 模式的闪光点：以一致的意图协调多个文件。

## 架构迁移和安全部署策略

在高级水平上，最难的部分不是写SQL。它正在设计一个更改：

* 向后兼容
* 可逆
* 负载下安全
* 对依赖系统透明

请Copilot对此进行推理：

**提示：**

```
Generate an additive, backward-compatible schema migration to support the tagging subsystem.

Describe the rollback plan, compatibility window, and expected impact to existing clients.
```

这迫使Copilot考虑：

* 打破常规的添加剂领域
* 可选字段与必填字段
* 是否需要双读或双写策略
* 安全回滚程序
* API 版本控制影响

如果您处于职业生涯早期，这将为您提供有关如何设计安全迁移的课程。如果您更有经验，这将为您提供用于多步骤模式演化的可重复工作流程。

## 使用代理工作流程进行高级重构

让我们执行真正的跨模块重构：将控制器中的验证提取到域服务中。

**提示：**

```
Create a step-by-step refactor plan to extract validation logic into a domain service.

Identify affected modules and required test updates.
```

Copilot 可能会输出类似以下内容：

1. 引入域名`validationService`
2. 将验证逻辑从控制器移至服务
3. 更新控制器以使用新服务
4. 更新验证假设泄漏的存储库逻辑
5. 更新域测试
6. 更新集成测试

### 以增量步骤执行

**提示：**

```
Execute steps 1–3 only. Stop before controller rewrites.

Provide detailed diffs and call out risky areas.
```

这是一个低爆炸半径重构，直接在 IDE 中建模。

## 现代化测试策略

不要要求 Copilot“编写测试”，而是要求它评估整个套件。

**提示：**

```
Analyze the current test suite and identify systemic gaps.

Recommend a modernization plan including contract, integration, and domain-layer tests.
```

**然后实施合约测试：**

```
describe("NotesRepository contract", () => {
  test("create + fetch returns a fully hydrated note object", async () => {
    const note = await notesRepo.create({ title: "Test", body: "…" });
    const fetched = await notesRepo.get(note.id);

    expect(fetched).toMatchObject({ title: "Test" });
    expect(fetched.id).toBeDefined();
  });
});
```

这将测试提升为架构问题。

## 完整的端到端工作流程

将所有内容放在一起，这是您可以使用 Copilot 运行的真实序列：

1. **要求Copilot分析现有架构：**识别危险、模块化机会
2. **定义模块边界：**域、存储库、控制器层
3. **添加标记子系统：** 架构评估、实施、测试、文档更新
4. **创建向后兼容的迁移：**附加架构到回滚计划
5. **执行有针对性的重构：**验证层提取
6. **现代化测试：**合同+集成+域测试

该工作流程在架构上是现实的，并且是 Copilot 如何成为系统级协作者的模型。

## agent 模式是 * 不是 * 的用途

需要澄清的是，agent 模式并不适合：

* 无需人工审核即可更改域不变量
* 重新设计跨服务所有权边界
* 取代机构知识驱动的逻辑
* 对数百个文件进行大规模重写
* 调试深度运行时问题

Copilot应该*支持*你的决策，而不是取代它。

## 下一步去哪里

这就是 GitHub 技能的用武之地 - 不是作为“初学者内容”，而是作为一组指导性的、独立的实验室来强化上述模式。

即使是高级工程师也会受益：这些练习是结构化的，因此您可以可靠地重新创建复杂的工作流程并在受控环境中测试 Copilot 的行为。

[探索GitHub技能>](<https://learn.github.com/skills>)

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub 技能](<https://github.blog/tag/github-skills/>)

## 撰写者

![阿里 LiVigni](<https://avatars.githubusercontent.com/u/4140343?v=4&s=200>)

### [阿里 LiVigni](<https://github.blog/author/arilivigni/>)

[@arilivigni](<https://github.com/arilivigni>)

高级服务交付工程师，GitHub


## 原文对照

<details>
<summary>展开完整原文</summary>

# How to maximize GitHub Copilot’s agentic capabilities

Modern engineering work rarely lives in a single file. Real systems evolve across years of incrementally layered decisions—some good, some accidental. A single feature request (“Add tagging to notes,” “Refactor the validation layer,” “Support a new consumer on our API”) often touches controllers, domain models, repositories, migrations, tests, documentation, and deployment strategy.

Copilot’s agentic capabilities don’t replace your judgment in these situations—they amplify it. When used well, Copilot becomes a partner in system design, refactoring, modernization, and multi-file coordination.

This guide focuses on architecture-aware, multi-step workflows used every day by staff engineers, but written to be accessible for earlier-career engineers who want to understand *how* senior engineers think—and how Copilot can accelerate their own growth.

It draws on four [GitHub Skills exercises](<https://learn.github.com/skills>) (linked below), and builds toward a complete, real-world scenario: extending a small modular Notes Service with a tagging subsystem, refactoring a validation layer, designing a safe migration, and modernizing tests.

---

## Before you start

You’ll get the most out of this guide if you have:

* GitHub Copilot with agent mode enabled
* Some familiarity with service-layer architectures (Node, Python, Go—language doesn’t matter
* Copy a GitHub Skills exercise template to your handle or organization (use the green “Copy Exercise” button)
* A willingness to let Copilot propose solutions—and the judgment to inspect and challenge them

If you’re earlier in your career, don’t worry. Each section explains **why** these patterns matter and how to practice them safely.

---

## Using Copilot for system design and decomposition (not just scaffolding)

Senior engineers rarely begin by writing code. They begin by identifying boundaries: domain logic, data access, interfaces, and how modules should interact.

Copilot agent mode can help by revealing structural issues and proposing architectures.

**Prompt:**

```
Analyze this service and propose a modular decomposition with domain, infrastructure, and interface layers.

Identify anti-patterns, coupling issues, and potential failure points.
```

You’ll typically get back:

* Proposed module boundaries
* Cross-layer coupling concerns
* Async/transaction pitfalls
* Duplication or tight weaving of responsibilities
* Testability and observability implications

This transforms Copilot from an autocomplete tool into a design reviewer.

You can push further by asking it to compare architectures:

```
Compare hexagonal architecture vs. layered architecture for this codebase.

Recommend one based on the constraints here. Include tradeoffs.
```

Want to try it yourself? Use these proposals as starting points.

## Building a modular service using agentic workflows

Once boundaries are defined, Copilot can coordinate changes across modules.

**Prompt**:

```
Implement the domain, controller, and repository layers as distinct modules.

Use dependency inversion to reduce coupling.

Document assumptions and contracts for each module.
```

Copilot will typically generate:

* Domain model interfaces
* Repository abstractions
* Controller logic calling domain services
* A short Markdown summary describing each module

For earlier-career engineers, this provides exposure to real engineering patterns. For senior engineers, it provides leverage and reduces boilerplate overhead.

## Feature work with architectural awareness (example: tagging subsystem)

Adding a tagging subsystem is a deceptively simple request with meaningful architectural implications.

Even this single feature forces decisions across the system:

* **Data modeling:** embedded tags vs. normalized tables vs. many-to-many relationships
* **Search behavior:** how tags affect indexing, filtering, and relevance
* **API contracts:** whether tags are first-class resources or an implementation detail
* **Validation boundaries:** where constraints and invariants are enforced
* **Migration and rollout:** additive vs. breaking changes and rollback strategy

Before touching code, ask Copilot to map the impact.

**Prompt**:

```
Propose the architectural changes required to add a tagging subsystem.

Identify migration needs, cross-cutting concerns, caching or indexing implications, and potential regressions.
```

Copilot may identify:

* Tag–note relationships (one-to-many or many-to-many)
* Migration strategy
* Impact to search logic
* Required test updates
* Changes in validation logic
* Implications on external API consumers

This is the staff-level lens that Copilot can help junior developers adopt.

**Then implement it:**

```
Implement the tagging domain model, schema changes, repository updates, and controller logic.

Update tests and documentation. Show each change as a diff.
```

Example output (simplified)

**Migration example:**

```
ALTER TABLE notes ADD COLUMN tags TEXT DEFAULT '[]';
```

**Domain model example:**

```
export interface Tag {
  id: string;
  label: string;
}

export interface Note {
  id: string;
  title: string;
  body: string;
  tags: Tag[];
}
```

**Controller update (partial):**

```
await noteService.addTag(noteId, { label: req.body.label });
```

This is where agent mode shines: coordinating multiple files with consistent intent.

## Schema migrations and safe rollout strategies

At senior levels, the hardest part isn’t writing SQL. It’s designing a change that is:

* Backward compatible
* Reversible
* Safe under load
* Transparent to dependent systems

Ask Copilot to reason about this:

**Prompt:**

```
Generate an additive, backward-compatible schema migration to support the tagging subsystem.

Describe the rollback plan, compatibility window, and expected impact to existing clients.
```

This forces Copilot to consider:

* Mon-breaking additive fields
* Optional fields vs. required fields
* Whether a dual-read or dual-write strategy is needed
* Safe rollback procedures
* API versioning implications

If you’re earlier in your career, this offers lessons on how safe migrations are designed. And if you’re more experienced, this gives you a repeatable workflow for multi-step schema evolution.

## Advanced refactoring with agentic workflows

Let’s perform a real cross-module refactor: extracting validation out of controllers into a domain service.

**Prompt:**

```
Create a step-by-step refactor plan to extract validation logic into a domain service.

Identify affected modules and required test updates.
```

Copilot may output something like:

1. Introduce domain `validationService`
2. Move validation logic from controller to service
3. Update controllers to use new service
4. Update repository logic where validation assumptions leak
5. Update domain tests
6. Update integration tests

### Execute in incremental steps

**Prompt:**

```
Execute steps 1–3 only. Stop before controller rewrites.

Provide detailed diffs and call out risky areas.
```

This is a low-blast-radius refactor, modeled directly in the IDE.

## Modernizing test strategy

Instead of asking Copilot “write tests,” ask it to assess the entire suite.

**Prompt:**

```
Analyze the current test suite and identify systemic gaps.

Recommend a modernization plan including contract, integration, and domain-layer tests.
```

**Then implement contract tests:**

```
describe("NotesRepository contract", () => {
  test("create + fetch returns a fully hydrated note object", async () => {
    const note = await notesRepo.create({ title: "Test", body: "…" });
    const fetched = await notesRepo.get(note.id);

    expect(fetched).toMatchObject({ title: "Test" });
    expect(fetched.id).toBeDefined();
  });
});
```

This elevates testing into an architectural concern.

## A complete end-to-end workflow

Bringing it all together, here’s a real sequence you might run with Copilot:

1. **Ask Copilot to analyze the existing architecture:** identify hazards, modularization opportunities
2. **Define module boundaries:** domain, repository, controller layers
3. **Add tagging subsystem:** architectural assessment to implementation to tests to doc updates
4. **Create a backward-compatible migration:** additive schema to rollback plan
5. **Perform a targeted refactor:** validation layer extraction
6. **Modernize tests:** contract + integration + domain tests

This workflow is architecturally realistic—and a model for how Copilot becomes a system-level collaborator.

## What agent mode is *not* for

It’s important to clarify that agent mode is not ideal for:

* Altering domain invariants without human review
* Redesigning cross-service ownership boundaries
* Replacing logic driven by institutional knowledge
* Large sweeping rewrites across hundreds of files
* Debugging deep runtime issues

Copilot should *support* your decision-making, not replace it.

## Where to go next

Here’s where GitHub Skills comes in—not as “beginner content,” but as a set of guided, self-contained labs that reinforce the patterns above.

Even senior engineers will benefit: These exercises are structured so you can reliably recreate complex workflows and test Copilot’s behavior in controlled environments.

[Explore GitHub Skills >](<https://learn.github.com/skills>)

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Skills](<https://github.blog/tag/github-skills/>)

## Written by

![Ari LiVigni](<https://avatars.githubusercontent.com/u/4140343?v=4&s=200>)

### [Ari LiVigni](<https://github.blog/author/arilivigni/>)

[@arilivigni](<https://github.com/arilivigni>)

Senior Service Delivery Engineer, GitHub


</details>
