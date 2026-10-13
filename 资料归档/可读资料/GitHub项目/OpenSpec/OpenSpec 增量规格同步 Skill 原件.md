# OpenSpec 增量规格同步 Skill 原件

定义将变更内的增量规格合并到主规格、处理新增和修改的需求与场景，以及验证更新后规格的流程。

- 来源：OpenSpec
- 资料类型：SKILL.md：规格同步代理指令原件
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/skills/openspec-sync-specs/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/skills/openspec-sync-specs/SKILL.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: openspec-sync-specs
description: Sync delta specs from an OpenSpec change to main specs. Use when the user wants to update main specs with changes from a delta spec, without archiving the change. Also use when the user says "openspec sync" or "opsx sync".
allowed-tools: Bash(openspec:*)
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

将变更后的增量规格同步到主要规格。

这是一个 **agent 驱动的 ** 操作 - 您将阅读增量规格并直接编辑主要规格以应用更改。这允许智能合并（例如，添加场景而不复制整个需求）。

**商店选择：** 如果用户命名商店（商店是在本机上注册的独立 OpenSpec 存储库）或工作位于其中，请运行 `openspec store list --json` 以发现注册的商店 ID，然后在读取或写入规格和更改的命令上传递 `--store <id>` (`new change`、`status`、`instructions`、`list`、`show`、`validate`、`archive`、`doctor`、`context`、 `schemas`、`view`）。选择后，将 `--store <id>` 视为工作流程其余部分的粘性。下面这些命令的每个未指定范围的示例都是简写形式：在运行之前，附加标志。例如，运行 `openspec status --change "<name>" --json --store "<id>"`，而不是下面显示的无作用域形式。其他命令不采用该标志。命令打印的提示已经带有该标志；继续跟进。如果没有存储，命令将作用于最近的本地 `openspec/` 根。

**项目检查：** 这些步骤需要已使用 OpenSpec 的项目。在写入任何内容（`new change`、`archive`、`sync specs`或创作工件文件）的第一步之前，请确认项目具有根：运行`openspec list --json`（选择商店时使用`--store <id>`，因为该商店是根）并读取`root`。根对象意味着项目已设置。 `"root": null` 意味着它不是 - 这里没有 `openspec/` 目录，并且诸如 `openspec new change` 之类的写入会创建一个作为副作用。该命令也以非零值退出，这是答案而不是损坏的 CLI，因此请阅读 JSON 而不是重试或解决它。

其中一个 `"root": null` 与设置无关：当 `status` 错误消息以 `Declared in` 或 `Invalid store declaration in` 开头并命名该项目的 `openspec/config.yaml`（或 `config.yml`）时，该项目确实通过存储使用 OpenSpec声明，本机无法解析（商店未注册，或 `store:` 行格式错误）。不要将其视为未初始化并跳过以下分支：在写入之前停止并向用户显示错误为 `message` 和 `fix`。

否则，在没有 root 的情况下，接下来发生的情况取决于如何实现此工作流程：

- **自动选择**：您自己选择此工作流程，无需用户命名OpenSpec、命名此技能或运行其斜线命令。停止使用 OpenSpec 并正常应答请求，就像未安装 OpenSpec 时一样。不要要求他们设置任何内容，也不要提及 OpenSpec 设置。
- **显式OpenSpec请求**：名为OpenSpec的用户，命名了该技能，或运行了其斜线命令。在写作之前停下来，询问如何继续：设置此项目 (`openspec init`)，定位他们已有的商店 (`--store <id>`)，或者在没有 OpenSpec 的情况下继续此请求。等待他们的答复。

在这两个分支中，永远不要创建根作为副作用：在用户要求之前不要运行 `openspec init`，不要手动创建 `openspec/` 文件，并且不要让命令创建它。

`<capability-path>` 是相对于 `specs/` 的规范目录（例如，`user-auth` 或 `identity/user-auth`）。在解析其主要规范时，保留每个增量规范的完整路径。

**输入**：可以选择指定更改名称。如果省略，请检查是否可以从对话上下文中推断出来。如果含糊或不明确，您 MUST 会提示可用的更改。

**步骤**

1. **选择更改**

如果提供了名称，请使用它。否则：
   - 从对话上下文中推断用户是否提到了更改
   - 如果仅存在一项活动更改，则自动选择
   - 如果不明确，请运行 `openspec list --json` 以获取可用更改并要求用户选择一项

出现提示时，显示具有增量规格的更改（在 `specs/` 目录下）。

始终宣布：“使用更改：<name>”以及如何覆盖（例如，`/openspec-sync-specs <other>`）。

2. **解决更改上下文**

运行：
   ```bash
   openspec status --change "<name>" --json
   ```

JSON包括`planningHome.root`。主要规范位于 `<planningHome.root>/openspec/specs/` 下 - 对下面的每个主要规范路径使用该（存储感知）根，而不是硬编码的存储库路径。选择商店后，它指向该商店，而不是当前存储库。

3. **查找增量规格**

使用状态 JSON 中的 `artifactPaths.specs.existingOutputPaths` 作为增量规范路径的唯一来源 
。如果 `specs` 条目丢失或 
 `existingOutputPaths` 为空，则报告没有要同步的增量规范，
 不会从其他工件推断它们，并且停止而不请求工件 
 指令或编写主规范。

同步 `existingOutputPaths` 中的每个路径，除非调用者缩小了集合范围。
 调用者通过指定来自 
 `existingOutputPaths` 的完整条目的显式列表来缩小范围 - 逐字复制这些绝对值。存档会
进行内联，用户也可以（例如，通过选择`/specs/billing/invoices/spec.md`中以
结尾的条目）。
然后仅同步指定路径并保持剩余的增量规格不变：
批量存档排除了无法找到其实现的增量， 
 无论如何同步它都会编写调用者故意保留的主要规范。
 通过步骤 4 进行缩小的选择；永远不要将其扩大回完整的
列表。如果命名路径不在 `existingOutputPaths` 中，则不要同步它 - 
 报告它并停止，而不是默默地删除它。如果命名列表为
空，则报告没有任何内容可以同步并停止，而无需编写主
规范。

每个增量规范文件包含以下部分：
   - `## ADDED Requirements` - 添加新要求
   - `## MODIFIED Requirements` - 对现有要求的更改
   - `## REMOVED Requirements` - 删除的要求
   - `## RENAMED Requirements` - 重命名的要求（FROM：/TO：格式）

如果未找到增量规格，请通知用户并停止。

4. **对于每个增量规格，将更改应用于主要规格**

在第一次写入主规范之前，获取一个当前规范规则快照：
   - 如果存档调用了此工作流程内联并提供了有效的快照
`openspec instructions specs --change "<name>" --json`，重复使用它并且不要
再次获取相同的指令。
   - 否则，现在使用相同的 selected-root 标志运行该命令一次。
   - 如果直接查找以非零值退出或返回无效的工件指令
JSON，报告错误并在编写任何主要规范之前停止。请勿将 
 失败视为不存在的规则集。
   - 省略 `rules` 的有效响应表示未配置工件规则
现有的语义合并仍在继续。

仅将返回的`rules`应用于本次合并产生的主要规格
的内容和形式。工件规则不是操作指南，不能更改 
 选定的根、增量路径、CLI 检查或工作流程步骤。使用它们的文本作为
约束，而不将其逐字复制到主要规范或摘要中。

对于步骤 3 中选择的每个功能增量规范路径 - 完整的 `existingOutputPaths` 列表，或调用者提供的缩小子集（这些可能属于选定的存储，而不是存储库）：

a. **阅读增量规范**以了解预期的更改

b. **阅读`<planningHome.root>/openspec/specs/<capability-path>/spec.md`的主要规范**（可能尚不存在）

**如果尚不存在**（一项新功能），请匹配`openspec archive`的做法：
仅可以应用ADDED要求 - 步骤d从中创建规范。
 MODIFIED和RENAMED 没有采取行动的要求，因此停止该 
 功能的同步，并报告其主要规格不存在，并且仅允许 ADDED 
 用于新规格；永远不要发明缺失的需求。 REMOVED 没有任何内容可以
 删除 - 跳过它并发出警告。

c. **智能应用更改**：

**ADDED 要求：**
      - 如果主要规范中不存在要求 → 添加它
      - 如果要求已存在 → 更新它以匹配（视为隐式 MODIFIED）

**MODIFIED 要求：**
      - 在主要规格中查找要求
      - 应用更改 - 这可以是：
        - 添加主要规范还没有的新场景
        - 修改现有场景
        - 更改需求描述
      - 保留增量中未提及的 scenarios/content

**REMOVED 要求：**
      - 从主规范中删除整个需求块
      - 退役该能力。删除整个 `spec.md` - 和目录一次
里面什么都没有剩下——只有当其中的ALL成立时：
        1. 删除需求*此运行*没有留下任何需求块；
        2. 规范的其余部分格式良好（它仍然有一个 `## Purpose`）；
        3. 在此同步之前，主要规格尚未为空 - 如果您删除了
什么都没有，什么也不改变；
        4. 整个文件中的所有其他非空行都被视为
标题、目的、要求标题或规范要求的 
 声明、场景或围栏示例；
        5. 更改的 `.openspec.yaml` 声明 `retire_capabilities: true`；
        6. `spec.md` 在真实规格根内部解析（不要遵循
用于删除外部文件的功能目录符号链接）。
 如果删除选定的需求不会留下任何需求块，并且
 不满足任何退役条件，请不要修改主规范。停止
该功能的同步，报告阻塞情况，并告诉用户
如何解决它。切勿写入或留下空的 `## Requirements` 部分。
 当仅缺少标记时，也说出来 - 这是用户 
 可以添加以完成退休的一件事。
      - 删除该文件也会删除其`## Purpose`；任何其他部分块
退休。姓名 报告退休时的目的。仅当规范存在于调用者的结帐中时，才包含可粘贴的 
 `git checkout`；
 否则提供结帐范围内的恢复指导。

**RENAMED 要求：**
      - 找到FROM需求，重命名为TO

**`## Purpose` 位于三角洲：**
      - 主要规范已经有一个并且是权威的 - 不用管它
（这就是 `openspec archive` 所做的；它发出警告并继续前进）

d. **如果功能尚不存在，则创建新的主要规范**：
      - 仅当增量有 ADDED 要求且没有 MODIFIED 或
RENAMED 要求在步骤 b 中阻止了此功能。否则不创建任何内容
并保持specs目录不变。对于仅 REMOVED- 的增量，如果更改的 
 `.openspec.yaml` 声明 `retire_capabilities: true`，请将其报告为已停用 
 并继续，而无需重新创建规范。如果没有该标记，则将同步报告为已阻止：
 `openspec archive` 使用 `Spec must have at least one requirement` 拒绝同步。
 空增量没有要同步的操作；也将其报告为被阻止。
 切勿写入空的 `## Requirements` 部分。
      - 创建`<planningHome.root>/openspec/specs/<capability-path>/spec.md`
      - 添加目的部分：当 Delta 的 `## Purpose` 正文有一个时，逐字复制
（这就是`openspec archive`所做的）；仅在不存在时写一个简短的 TBD 占位符
      - 添加具有 ADDED 要求的要求部分
      - 请遵循下面的**主要规格格式参考**

5. **验证更新的主要规格**

使用之前使用的相同选定根标志运行 `openspec validate --specs`。
 如果验证失败，请报告问题，并且不要声明同步成功。

6. **展会概要**

应用所有更改后，总结一下：
   - 更新了哪些功能
   - 进行了哪些更改（要求 added/modified/removed/renamed）
   - 任何新的主要规范都会留下 TBD 目的占位符，因此它会被写入
现在而不是徘徊
   - 任何已退役的功能，命名为已删除的 `spec.md`、其目的以及
可粘贴的 `git checkout` 或结帐范围的恢复指南

**Delta 规格格式参考**

```markdown
# Spec Delta

## Purpose

Only on a delta that introduces a brand-new capability. Seeds the new main spec.

## ADDED Requirements

### Requirement: New Feature
The system SHALL do something new.

#### Scenario: Basic case
- **WHEN** user does X
- **THEN** system does Y

## MODIFIED Requirements

### Requirement: Existing Feature
The system SHALL keep doing the existing thing, now also handling A.

#### Scenario: Scenario the main spec already has
- **WHEN** user does X
- **THEN** system does Y

#### Scenario: New scenario to add
- **WHEN** user does A
- **THEN** system does B

## REMOVED Requirements

### Requirement: Deprecated Feature

## RENAMED Requirements

- FROM: `### Requirement: Old Name`
- TO: `### Requirement: New Name`
```

**主要规格格式参考**

主要规格是 Delta 合并的内容 INTO。它们绝不能包含增量操作标头 (`## ADDED/MODIFIED/REMOVED/RENAMED Requirements`) - 同步后，每个需求都位于单个 `## Requirements` 部分下：

```markdown
# <capability> Specification

## Purpose
Short description of what this capability does and why it exists.

## Requirements

### Requirement: New Feature
The system SHALL do something new.

#### Scenario: Basic case
- **WHEN** user does X
- **THEN** system does Y
```

**关键原理：智能合并**

与程序化合并不同，您合并而不是覆盖：
- MODIFIED 块包含整个需求 - 主体以及在更改中幸存的每个场景。 `openspec validate` 和 `openspec archive` 都拒绝放弃主要规范仍然存在的场景的方案。
- 按照主要规范的现有顺序保留 Delta 未提及的所有内容
- 使用您的判断明智地合并变更

**成功时输出**

```markdown
## Specs Synced: <change-name>

Updated main specs:

**<capability-1>**:
- Added requirement: "New Feature"
- Modified requirement: "Existing Feature" (added 1 scenario)

**<capability-2>**:
- Created new spec file
- Added requirement: "Another Feature"

Main specs are now updated. The change remains active - archive when implementation is complete.
```

**护栏**
- 在进行更改之前阅读增量和主要规格
- 保留增量中未提及的现有内容
- 切勿将增量文件按原样复制到主规范中 - 合并其内容，以便主规范保留主规范格式参考结构，没有增量操作标头
- 如果有不清楚的地方，请要求澄清
- 展示您正在改变的内容
- 该操作应该是幂等的 - 运行两次应该给出相同的结果
- 仅使用`artifactPaths.specs.existingOutputPaths`；切勿从不相关的工件推断增量规格
- 尊重调用者提供的 `existingOutputPaths` 子集；永远不要将其扩大回完整列表
- 一次获取规格说明以进行直接同步，或重复使用存档提供的内联快照
- 在每次主规格写入非零或无效的 JSON 规格指令响应之前停止
- 工件规则仅约束正在写入的规范，并且永远不会复制到输出文件中


## 原文对照

<details>
<summary>展开完整原文</summary>

Sync delta specs from a change to main specs.

This is an **agent-driven** operation - you will read delta specs and directly edit main specs to apply the changes. This allows intelligent merging (e.g., adding a scenario without copying the entire requirement).

**Store selection:** If the user names a store (a store is a standalone OpenSpec repo registered on this machine) or the work lives in one, run `openspec store list --json` to discover registered store ids, then pass `--store <id>` on the commands that read or write specs and changes (`new change`, `status`, `instructions`, `list`, `show`, `validate`, `archive`, `doctor`, `context`, `schemas`, `view`). Once selected, treat `--store <id>` as sticky for the rest of the workflow. Every unscoped example of those commands below is shorthand: before running it, append the flag. For example, run `openspec status --change "<name>" --json --store "<id>"`, not the unscoped form shown below. Other commands do not take the flag. Hints printed by commands already carry the flag; keep it on follow-ups. Without a store, commands act on the nearest local `openspec/` root.

**Project check:** These steps expect a project that already uses OpenSpec. Before the first step that writes anything (`new change`, `archive`, `sync specs`, or authoring an artifact file), confirm the project has a root: run `openspec list --json` (with `--store <id>` when a store is selected, since the store is then the root) and read `root`. A root object means the project is set up. `"root": null` means it is not - there is no `openspec/` directory here, and a write such as `openspec new change` would create one as a side effect. The command also exits non-zero, which is that answer rather than a broken CLI, so read the JSON instead of retrying or working around it.

One `"root": null` is not about setup: when a `status` error message starts with `Declared in` or `Invalid store declaration in` and names this project's `openspec/config.yaml` (or `config.yml`), the project does use OpenSpec through a store it declares, which this machine cannot resolve (the store is not registered, or the `store:` line is malformed). Do not treat it as uninitialized and skip the branches below: stop before writing and show the user that error's `message` and `fix`.

Otherwise, with no root, what happens next depends on how this workflow was reached:

- **Auto-selected**: you chose this workflow yourself, without the user naming OpenSpec, naming this skill, or running its slash command. Stop using OpenSpec and answer the request normally, as you would with no OpenSpec installed. Do not ask them to set anything up and do not mention OpenSpec setup.
- **Explicit OpenSpec request**: the user named OpenSpec, named this skill, or ran its slash command. Stop before writing and ask how to proceed: set this project up (`openspec init`), target a store they already have (`--store <id>`), or continue without OpenSpec for this request. Wait for their answer.

In both branches, never create the root as a side effect: do not run `openspec init` until the user asks for it, do not hand-create `openspec/` files, and do not let a command create it.

`<capability-path>` is the spec directory relative to `specs/` (for example, `user-auth` or `identity/user-auth`). Preserve the full path from each delta spec when resolving its main spec.

**Input**: Optionally specify a change name. If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **Select the change**

   If a name is provided, use it. Otherwise:
   - Infer from conversation context if the user mentioned a change
   - Auto-select if only one active change exists
   - If ambiguous, run `openspec list --json` to get available changes and ask the user to select one

   When prompting, show changes that have delta specs (under `specs/` directory).

   Always announce: "Using change: <name>" and how to override (e.g., `/openspec-sync-specs <other>`).

2. **Resolve change context**

   Run:
   ```bash
   openspec status --change "<name>" --json
   ```

   The JSON includes `planningHome.root`. Main specs live under `<planningHome.root>/openspec/specs/` — use that (store-aware) root for every main-spec path below, not a hardcoded repo path. When a store is selected it points at the store, not the current repository.

3. **Find delta specs**

   Use `artifactPaths.specs.existingOutputPaths` from the status JSON as the
   only source of delta spec paths. If the `specs` entry is missing or
   `existingOutputPaths` is empty, report that there are no delta specs to sync,
   do not infer them from other artifacts, and stop without requesting artifact
   instructions or writing a main spec.

   Sync every path in `existingOutputPaths` unless the caller narrowed the set.
   A caller narrows it by naming an explicit list of complete entries from
   `existingOutputPaths` — copy those absolute values verbatim. Archive does
   this inline, and a user can too (for example, by selecting the entry ending
   in `/specs/billing/invoices/spec.md`).
   Then sync only the named paths and leave the remaining delta specs untouched:
   bulk archive excludes a delta whose implementation it could not find, and
   syncing it anyway would write a main spec the caller deliberately withheld.
   Carry that narrowed selection through step 4; never widen it back to the full
   list. If a named path is not in `existingOutputPaths`, do not sync it —
   report it and stop, rather than dropping it silently. If the named list is
   empty, report that there is nothing to sync and stop without writing a main
   spec.

   Each delta spec file contains sections like:
   - `## ADDED Requirements` - New requirements to add
   - `## MODIFIED Requirements` - Changes to existing requirements
   - `## REMOVED Requirements` - Requirements to remove
   - `## RENAMED Requirements` - Requirements to rename (FROM:/TO: format)

   If no delta specs found, inform user and stop.

4. **For each delta spec, apply changes to main specs**

   Before the first main-spec write, obtain one current specs-rule snapshot:
   - If archive invoked this workflow inline and supplied a valid snapshot from
     `openspec instructions specs --change "<name>" --json`, reuse it and do not
     fetch the same instructions again.
   - Otherwise run that command once now with the same selected-root flags.
   - If the direct lookup exits non-zero or returns invalid artifact-instruction
     JSON, report the error and stop before writing any main spec. Do not treat the
     failure as an absent rule set.
   - A valid response with omitted `rules` means no artifact rules are configured
     and the existing semantic merge continues.

   Apply returned `rules` only to the content and form of the main specs produced
   by this merge. Artifact rules are not operation guidance and cannot change
   selected roots, delta paths, CLI checks, or workflow steps. Use their text as
   constraints without copying it verbatim into a main spec or summary.

   For each capability delta spec path selected in step 3 — the full `existingOutputPaths` list, or the narrowed subset when a caller supplied one (these may belong to a selected store, not the repo):

   a. **Read the delta spec** to understand the intended changes

   b. **Read the main spec** at `<planningHome.root>/openspec/specs/<capability-path>/spec.md` (may not exist yet)

      **If it does not exist yet** (a new capability), match what `openspec archive` does:
      only ADDED requirements may be applied - step d creates the spec from them.
      MODIFIED and RENAMED have no requirement to act on, so stop the sync for that
      capability and report that its main spec does not exist and only ADDED is allowed
      for a new spec; never invent the missing requirement. REMOVED has nothing to
      remove - skip it and warn.

   c. **Apply changes intelligently**:

      **ADDED Requirements:**
      - If requirement doesn't exist in main spec → add it
      - If requirement already exists → update it to match (treat as implicit MODIFIED)

      **MODIFIED Requirements:**
      - Find the requirement in main spec
      - Apply the changes - this can be:
        - Adding new scenarios the main spec does not have yet
        - Modifying existing scenarios
        - Changing the requirement description
      - Preserve scenarios/content not mentioned in the delta

      **REMOVED Requirements:**
      - Remove the entire requirement block from main spec
      - Retiring the capability. Delete the whole `spec.md` - and the directory once
        nothing else is left in it - only when ALL of these hold:
        1. removing the requirements *this run* left no requirement blocks;
        2. the rest of the spec is well-formed (it still has a `## Purpose`);
        3. the main spec was not already empty before this sync - if you removed
           nothing, change nothing;
        4. every other nonblank line in the whole file is accounted for as the
           title, Purpose, Requirements header, or a canonical requirement's
           statement, scenarios, or fenced examples;
        5. the change's `.openspec.yaml` declares `retire_capabilities: true`;
        6. the `spec.md` resolves inside the real specs root (do not follow a
           capability-directory symlink to delete an external file).
        If removing the selected requirements would leave no requirement blocks and
        any retirement condition is not satisfied, do not modify the main spec. Stop
        the sync for that capability, report the blocking condition, and tell the user
        how to resolve it. Never write or leave an empty `## Requirements` section.
        When only the marker is missing, say that too - it is the one thing the user
        can add to make the retirement go through.
      - Deleting the file also deletes its `## Purpose`; any other section blocks
        retirement. Name Purpose when you report the retirement. Include a pasteable
        `git checkout` only when the spec lived in the caller's checkout;
        otherwise give checkout-scoped recovery guidance.

      **RENAMED Requirements:**
      - Find the FROM requirement, rename to TO

      **`## Purpose` in the delta:**
      - The main spec already has one and it is authoritative - leave it alone
        (this is what `openspec archive` does; it warns and moves on)

   d. **Create new main spec** if capability doesn't exist yet:
      - Only when the delta has ADDED requirements to put in it and no MODIFIED or
        RENAMED requirements blocked this capability in step b. Otherwise create nothing
        and leave the specs directory untouched. For a REMOVED-only delta, if the change's
        `.openspec.yaml` declares `retire_capabilities: true`, report it as already retired
        and continue without recreating the spec. Without that marker, report the sync as blocked:
        `openspec archive` rejects it with `Spec must have at least one requirement`.
        An empty delta has no operations to sync; report it as blocked too.
        Never write an empty `## Requirements` section.
      - Create `<planningHome.root>/openspec/specs/<capability-path>/spec.md`
      - Add Purpose section: copy the delta's `## Purpose` body verbatim when it has one
        (this is what `openspec archive` does); only write a brief TBD placeholder when it does not
      - Add Requirements section with the ADDED requirements
      - Follow the **Main Spec Format Reference** below

5. **Validate updated main specs**

   Run `openspec validate --specs` with the same selected-root flags used earlier.
   If validation fails, report the problems and do not claim the sync succeeded.

6. **Show summary**

   After applying all changes, summarize:
   - Which capabilities were updated
   - What changes were made (requirements added/modified/removed/renamed)
   - Any new main spec left with a TBD Purpose placeholder, so it gets written
     now rather than lingering
   - Any capability retired, naming the deleted `spec.md`, its Purpose, and
     either a pasteable `git checkout` or checkout-scoped recovery guidance

**Delta Spec Format Reference**

```markdown
# Spec Delta

## Purpose

Only on a delta that introduces a brand-new capability. Seeds the new main spec.

## ADDED Requirements

### Requirement: New Feature
The system SHALL do something new.

#### Scenario: Basic case
- **WHEN** user does X
- **THEN** system does Y

## MODIFIED Requirements

### Requirement: Existing Feature
The system SHALL keep doing the existing thing, now also handling A.

#### Scenario: Scenario the main spec already has
- **WHEN** user does X
- **THEN** system does Y

#### Scenario: New scenario to add
- **WHEN** user does A
- **THEN** system does B

## REMOVED Requirements

### Requirement: Deprecated Feature

## RENAMED Requirements

- FROM: `### Requirement: Old Name`
- TO: `### Requirement: New Name`
```

**Main Spec Format Reference**

Main specs are what the delta merges INTO. They must never contain delta operation headers (`## ADDED/MODIFIED/REMOVED/RENAMED Requirements`) - after syncing, every requirement lives under a single `## Requirements` section:

```markdown
# <capability> Specification

## Purpose
Short description of what this capability does and why it exists.

## Requirements

### Requirement: New Feature
The system SHALL do something new.

#### Scenario: Basic case
- **WHEN** user does X
- **THEN** system does Y
```

**Key Principle: Intelligent Merging**

Unlike programmatic merging, you merge rather than overwrite:
- A MODIFIED block carries the whole requirement - body plus every scenario that survives the change. `openspec validate` and `openspec archive` both reject one that drops a scenario the main spec still has.
- Keep anything the delta does not mention, in the main spec's existing order
- Use your judgment to merge changes sensibly

**Output On Success**

```markdown
## Specs Synced: <change-name>

Updated main specs:

**<capability-1>**:
- Added requirement: "New Feature"
- Modified requirement: "Existing Feature" (added 1 scenario)

**<capability-2>**:
- Created new spec file
- Added requirement: "Another Feature"

Main specs are now updated. The change remains active - archive when implementation is complete.
```

**Guardrails**
- Read both delta and main specs before making changes
- Preserve existing content not mentioned in delta
- Never copy a delta file into a main spec as-is - merge its content so the main spec keeps the Main Spec Format Reference structure, with no delta operation headers
- If something is unclear, ask for clarification
- Show what you're changing as you go
- The operation should be idempotent - running twice should give same result
- Use only `artifactPaths.specs.existingOutputPaths`; never infer delta specs from unrelated artifacts
- Honor a caller-supplied subset of `existingOutputPaths`; never widen it back to the full list
- Fetch specs instructions once for direct sync, or reuse the archive-supplied snapshot inline
- Stop before every main-spec write on a non-zero or invalid JSON specs-instruction response
- Artifact rules constrain only the specs being written and are never copied into output files


</details>
