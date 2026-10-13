# Qwen Code 代码审查

介绍 /review 的深度选择、多个审查角色、严重程度、工作树隔离、跨仓库 PR、修复与恢复及结构化报告。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-10-04](<https://github.com/QwenLM/qwen-code/commit/9fe53fbc1ea0a0cb3cf9d85580acf46a63b8cec2>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/code-review.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code 代码审查.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 代码审查

> 使用 `/review` 检查代码更改的正确性、安全性、性能和代码质量。

## 快速入门

```bash
# Review local uncommitted changes
/review

# Review a pull request (by number or URL)
/review 123
/review https://github.com/org/repo/pull/123

# Review and post inline comments on the PR
/review 123 --comment

# Review local changes and apply the findings to your working tree
/review --fix

# Continue a review of the same PR that was interrupted, instead of starting over
/review 123 --resume

# Review a specific file
/review src/utils/auth.ts

# Quick unverified pass (no subagents)
/review --effort low
/review 123 --effort medium
```

如果没有未提交的更改，`/review` 将通知您并停止 — 不会启动 agents。

## 努力程度

`--effort low|medium|high` 用深度换取速度：

|级别|运行什么|调查结果上限|判决|帖子到 PR|
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ----------------------------------- | ---------------- |
| `low`    |3-6 差异上的定向内联角度（按差异大小缩放）加上间隙扫描和地板下重新通过 - 没有子代理，没有 build/test，没有项目规则|10（未经验证）|无|从来没有|
| `medium` |高管道减去其最昂贵的传递：在减小的维度集上的并行查找器扇出，加上build/test和单个验证传递|无上限（已验证）|批准上限为评论|从来没有|
| `high`   |全流程：最多16并行agents→分片验证→迭代逆向审计|无上限（已验证）|批准/请求更改/评论|与 `--comment`|

`/review` 按此顺序解决工作：显式 `--effort`、为此项目显式键入的最后一个级别、运算符 `review.effort` 设置，然后是内置目标默认值（**high** 用于 PR 评论， **中**用于本地和文件审阅）。当记住的级别适用时，`/review` 在工作开始前宣布；输入新的 `--effort` 来替换它。有效的 `--comment` 强制高（发布的评论必须通过验证）——在非 PR 目标上 `--comment` 会被忽略并发出警告，并且 ** 不会 ** 改变努力。 Medium 保留了安全性和测试覆盖率 agents 和 build/test，并放弃了对抗性角色、语言陷阱和 wrapper/proxy 专家 (Agents 1d/1e)、差异专家发现者和反向审计 — 因此只有第二次查看才会出现的微妙 Critical 可能会消失；使用 `--effort high` 进行安全敏感或预发布审查。仅`low`未经验证。工作树隔离适用于同一仓库 PR 评论；跨存储库 PR 以轻量级模式运行（仅差异，无工作树或 build/test）。低通被标记为未经验证，不发出任何判决，并且从不写入增量审查缓存，因此稍后的 `--effort high` 运行永远不会因为“已审查”而被跳过；媒介已验证，但其批准仅限于评论，因为没有任何东西会两次检查第一次通过时错过的内容。每个级别的差异获取机制都是相同的 - PR 评论始终使用独立的工作树和相同的基础分辨率，因此评论永远不会针对错误的基础。仍然存在一个范围差异：增量缓存仅是高的，因此高的重新审查可能仅涵盖新提交（`lastCommitSha..HEAD`），而low/medium始终审查完整的PR差异。

## 它是如何运作的

`/review` 命令运行多级管道：

```
Step 1:  Determine scope + effort level (local diff / PR worktree / file)
         Capture the diff to a file + partition it into chunks
Step 2:  Load project review rules (medium/high)
Step 3C: low effort: 3-6 inline angles + gap sweep + under-floor re-pass
                                                               [0 subagent calls]
Step 3A: high, <=500 src AND <=3200 total: up to 16 agents  [16+ LLM calls]
           |-- Agent 0: Issue Fidelity & Root-Cause Ownership
           |-- Agent 1a: Correctness — line-by-line scan
           |-- Agent 1b: Correctness — removed-behavior audit
           |-- Agent 1c: Correctness — cross-file tracer
           |-- Agent 1d: Correctness — language-pitfall scan
           |-- Agent 1e: Correctness — wrapper/proxy routing
           |     (only when the diff signals a wrapping type)
           |-- Agent 2: Security
           |-- Agent 3a: Reuse & duplication
           |-- Agent 3b: Altitude & abstraction fit
           |-- Agent 3c: Consistency & clarity
           |-- Agent 4: Performance & Efficiency
           |-- Agent 5: Test Coverage
           |-- Agent 6: Undirected Audit (3 personas: 6a/6b/6c)
           |-- Agent 8: Diff-specialized finders (0-2, only when
           |     the diff's domain calls for them)
           '-- Agent 7: Build & Test (runs shell commands)
Step 3B: high, >500 src OR >3200 total: territory x dim.   [N+5..7+3H calls]
           (N chunks, 5-7 whole-diff agents, 3 invariant
            agents per heavy file H)
           |-- 1 chunk agent per ~400 diff lines (all dimensions,
           |     its territory only, returns a coverage receipt)
           |-- 3 invariant agents per heavily-rewritten source
           |     file (whole file; state/timers, counters/
           |      returns/errors, config/early-returns)
           |-- Agent 0: Issue Fidelity      (whole diff)
           |-- Agent 7: Build & Test        (whole repo)
           |-- Agent 1b: Removed-behavior   (whole diff — the
           |     cross-chunk half; chunks keep the local half)
           |-- Agent 1c: Cross-file tracer  (whole diff)
           |-- Agent 8: Specialized finders (whole diff, 0-2)
           '-- Test coverage matrix         (whole diff)
Step 4:  Deduplicate --> Sharded verify (<=8 findings each)
           --> Aggregate                    [ceil(F/8) calls, F=findings]
Step 5:  Iterative reverse audit, fanned out per chunk;
           stop after 2 consecutive dry rounds (cap 10/5/3 by topology)
Step 6:  Present findings + verdict (high; low pass: findings only)
         Canonicalize findings -> .qwen/tmp/...-findings.json
Step 6B: Apply findings + record per-finding outcomes  (--fix only)
         + fix audit: one agent over the applied hunks,
           reports unpinned new assumptions (no verdict)  [1 call]
Step 7:  Submit PR review (inline comments, if requested; high only)
Step 8:  Save report + incremental cache (cache: high only)
Step 9:  Clean up (remove worktree + temp files)
```

步骤3A/3B/4/5是高强度的管道；在 `--effort low|medium` 处，单个内联传递（步骤 3C）取代了它们。

### 评论 Agents

| Agent                             |焦点|
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|Agent 0：问题保真度|链接的问题证据、根本原因所有权以及 PR 是否解决了报告的问题|
|Agent 1a：逐行扫描|遍历每个块及其封闭函数：错误条件、相差一、缺少 `await`、边缘情况、竞争条件|
|Agent 1b：删除行为审核|遍历每个 deleted/replaced 行：命名它强制执行的不变量，并寻找新代码重新建立它的位置 - 包括删除的 ** 导出 **，其替换通常位于另一个文件中并悄悄更改默认值。在 3B 中，它运行整体差异（块 agents 保留本地一半）|
|Agent 1c：跨文件跟踪器|遍历每个更改的符号的调用者（消费者方向）和每个添加的字段的读取站点（生产者方向），以及相同的PR被调用者更改|
|Agent 1d：语言陷阱扫描|携带 diff 语言的经典枪检查表（`==` 强制、假值陷阱、循环变量捕获、可变默认值、nil-map 写入、SQL 连接、DST 算术）并对其进行模式匹配|
|Agent 1e: Wrapper/proxy 路由|对于每种类型，diff 添加或修改包装另一种类型（缓存、代理、装饰器、适配器）：每个方法都通过包装的实例进行路由，并且包装器转发调用者使用的每个方法。仅当 diff 发出包装类型信号时才进行排序|
|Agent 2：安全|注入、XSS、SSRF、身份验证绕过、敏感数据暴露|
|Agent 3a：重用和复制|代码库已经有这个了吗？ Grep 查找行为，命名要调用的现有帮助程序，并标记差异留下的死代码|
|Agent 3b：高度和抽象|修复的深度是否正确——或者是共享基础设施上的创可贴、对上游错误的下游补偿，还是服务于一个调用站点的抽象？|
|Agent 3c：一致性和清晰度|兄弟一致性（平行家族中的一个成员有守卫，但其双胞胎缺乏）、与引用的本地示例的约定偏差、误导性 names/comments、不必要的复杂性|
|Agent 4：性能与效率|N+1 查询、内存泄漏、不必要的重新渲染、包大小|
|Agent 5：测试覆盖率|diff 中未经测试的代码路径、缺少分支覆盖、弱断言|
|Agent 6：无向审计| 3 并行角色（攻击者/凌晨 3 点待命/维护者）——捕获跨维度问题——加上反框架审计（6d， PR 高度努力的目标）：阅读 PR 描述仅用于 EXCLUDE 其提名的主题并在合并后的第二天重播其激励事件            |
|Agent 7：构建和测试|运行构建和测试命令，报告失败|
|Agent 8：Diff 专用取景器|0-2 当差异集中在具有已知故障模式的域（重新连接逻辑、模块加载器、调度程序、编解码器）时，每次审阅时编写的额外查找器|
|`prose-exec`：散文执行|当 diff 触及指令文件（`SKILL.md`、`AGENTS.md`/`CLAUDE.md`/`QWEN.md`、agent 或命令定义、`prompts/` 文件）并且审阅具有树时：EXECUTES一次性副本和文件运行与散文承诺之间的差距|

三个正确性 agents 是 ** 程序 **：每个正确性都是由它如何遍历差异（逐行/删除行/跨文件边缘）来定义的，而不是由错误分类法定义的 - 因此它们的覆盖范围是互补的而不是重叠的。另外两个专用角度（1d/1e）将语言陷阱清单和wrapper/proxy路由从逐行行走中分离出来：清单模式匹配和结构路由期望是不同的注意力模式，并折叠到行走中，它们被其节奏稀释。同样的推理将 ** 代码质量分为三个 ** (3a/3b/3c)：一个 agent 持有六项清单，完成一项 - 在大量重写的文件上测量，一个 agent 持有八项清单，发现 1 5 缺陷和同一型号分为三种方式发现所有 5 - 因此质量检查表在问题真正不同的地方被删除。所有 agents 并行运行（Agent 1 启动 3 程序变体和 2 专用角度，Agent 3 启动 3 检查表切片，以及Agent 6 同时启动 3 角色变体和反框架审核 6d，同一仓库 PR 审核的并行任务总计达到 17 — Agent 1e 仅在 diff 发出包装类型信号时运行 — 加0-2 Agent 8 在 diff 的域需要它们时查找，因此在实践中 16-19，当 diff 触及指令文件时还有一个 (`prose-exec`)； Agent 0 和 6d 被跳过进行本地差异和文件路径审查，运行 14-17；跨存储库轻量级模式也会跳过 Agents 1c 和 7，运行14-17 具有 PR 身份，12-15 没有它）。

每项发现都必须说明 ** 失败场景 ** — 触发它的具体输入、状态或时间以及导致的错误结果（对于质量发现，则为具体成本）。无法命名其场景的发现会从源头删除，验证会通过真实代码重新跟踪所声称的场景，而不是判断发现的散文。

一旦 PR 携带超过 **source** 变化的 500 行 — 或总共超过 3 200 diff 行，超过这 16 个整体 diff 阅读器都被稀释而无法仔细阅读（注意力限制，而不是承诺更少）调用 — 繁重的文件和专门的查找器可能会使 3B 成本更高） — 此维度扇出被 ** 区域 × 维度 ** 扇出所取代：差异被分割成 ~400 行块 — 边界落在块边界上，太大而无法容纳的块仅在顶级声明处分割，而不是在函数内部 — 每个块都有自己的agent 将每个审核维度单独应用于该块。

门故意计算源线而不是差异线。测试代码、散文和锁定文件主导 diff 大小 — 在该存储库的最后一个 40 合并 PR 中，中值 diff 是 41% 测试 — 因此，原始大小的门会将 173 行生产更改划分为区域，只是因为它运送了 489 行新测试，将生产代码留给一名审阅者而不是 15 个镜头（差异读取维度 agents — 十七减去问题保真度和构建和测试）。不管怎样，分块仍然覆盖每一行，包括测试；门决定的是有多少审稿人以及每个审稿人被要求做什么。十五个差异读取镜头全部行走在一个大差异中，将相同的早期大块读取了十五次；每个块一个 agent 意味着差异的每一行都有一个负责任的审阅者。每个块 agent 返回一个 `Covered:` 收据，并且在运行继续之前重新审查没有收据的块 - 因此永远不会在没有人读取的代码上报告“无阻止程序”。

很大程度上重写的 ** 源 ** 文件（300 + 行的现有文件现在是 40% + 新的，或具有 800 + 更改的行）也获得 ** 三个整个文件不变量agents**。测试和生成的文件永远不会合格——检查表询问字段、计时器和错误分类，而重写的测试文件不具备这些。它的错误通常不在任何一个块中，而是在_之间的_新行中——一个在文件顶部附近武装的计时器和一个在下面两千行的拆卸路径。每个 agent 读取整个更改后文件，并遍历固定清单中的两个或三个项目：在每个退出路径上清除的可变字段、在每次关闭时取消的计时器（并且取消不会丢弃捕获的数据）、与删除匹配的映射插入、在每个条目处递增的重试计数器、实际检查的状态返回值、详尽分类的永久与瞬态的错误代码、在每个路径上遵循的配置字段以及跳过所需副作用的早期返回。

该清单有意分为三种方式。将 agent 的所有八项检查交给 2 400 行文件即可正确完成其中一项；三个 agents，每个检查两到三项即可完成所有任务。块 agents 不能替代这一点 - 在 PR #6457 上，他们在指定的区域内保留了所有这些缺陷，并且没有报告任何缺陷。他们缺少的不是台词，而是问题。

结果在 ** 分片批次 ** 中进行验证（每次验证 agent 最多 8 个结果，全部一起启动）。验证者只能通过引用与其相矛盾的代码来拒绝 Critical（或者当 diff 自己的注释将标记的行为记录为故意时）；任何不太确定的东西都会被降级为低置信度，而不是被删除——一个默默拒绝的关键对于每个后续阶段都是不可见的，而降级的关键仍然可以到达人类。该条适用于每个拒绝的形状：它必须可以从代码中构建 - 引用发现误读的行，证明声明的状态不可能从类型、常量或不变量中证明，引用覆盖触发器的 in-diff 保护，或者匹配没有可观察到的效果的纯样式更改 - 或者以其他方式匹配排除标准 - 并且“过于投机”从来不是其中之一。默认情况下，其故障场景命名的状态代码不排除的结果是合理的：并发竞争、罕见但可到达的路径上的 nil/undefined、被视为丢失的假零或空集合、未排除边界上的偏一、重试风暴或部分失败、丢失锚点的正则表达式或白名单。拒绝不会构成四个理由降级而不是下降。验证后，**迭代反向审计**寻找差距，每轮每个块分散一名审计员，每个审计员都有累积的发现列表。循环在**两个连续的干回合**后停止（或在计划的回合上限处 - 如此报告而不是收敛）。该上限遵循 diff 的拓扑：**10** 在一个小 diff 上，其中一轮是单个审核员； **5** 在分块的情况下，每个块有一个审核员；和**3**在一个巨大的差异（≥3000有效行）_当运行有明确的截止日期_（CI的，或捕获时的`--deadline`），因为五〜90分钟轮不适合六小时的CI上限，并且评论在飞行途中杀死了任何内容——在该计划的默认墙下，巨大的差异保留了5的分块上限。从它看到的时钟中捕获并记录价格；读取器只能降低其带内的记录值（在 CI 纪元下捕获的计划将 3 保持在没有该纪元恢复的延续中；如果稍后运行获得 1，则在没有时钟的情况下捕获的值将被切至 3），并且带外的值回落到层。操作员可以通过 `review.reverseAuditRounds` 设置降低每次审核所适用的上限；它永远无法养育一个。一轮干燥的回合并不是收敛的证据，逆向审计结果与其他审计结果一样得到验证。

## 严重程度

|严重性|含义|发表为 PR 评论？|
| ---------------- | ------------------------------------------------------------------- | -------------------------- |
|**严重**|合并前必须修复（错误、安全性、数据丢失、构建失败）|是（仅限高置信度）|
|**建议**|建议改进|是（仅限高置信度）|
|**很高兴拥有**|可选优化|否（仅限终端）|

低可信度的结果出现在终端中单独的“需要人工审核”部分中，并且永远不会作为 PR 评论发布。

## 工作树隔离

当查看 PR 时，`/review` 创建一个临时 git 工作树 (`.qwen/tmp/review-pr-<number>`)，而不是切换当前分支。这意味着：

- 您的工作树、分阶段更改和当前分支**从未触及**
- 依赖项安装在工作树中（`npm ci` 等），因此 build/test 可以工作
- 构建和测试命令独立运行，不会污染本地构建缓存
- 如果出现任何问题，您的环境不会受到影响 - 只需删除工作树即可
- 审核完成后工作树会自动清理
- 如果审阅被中断（Ctrl+C，崩溃），同一 PR 的下一个 `/review` 会在开始新工作之前自动清理过时的工作树。如果中断的会话仍然留下其租约——跳过此的硬终止，或者在稍后的提示期间中断的多重提示审阅——`/review`拒绝并命名要删除的租约文件。干净的停止释放它：完成的审查和早期停止（空差异，自上次审查以来没有新的更改）全部运行`cleanup`，这释放了租约
- 工作树被租给其会话：已在审查中的 PR 的第二个 `/review` 拒绝启动（命名持有者），而不是拆除正在运行的审查的工作树
- 审核报告和缓存保存到主项目目录（而不是工作树）
- **修改**代码来测量某些东西的步骤——测试功效探针的突变体，以及验证者对特定发现的探针——每个步骤都在其旁边的自己的一次性工作树中运行（`…-probe`，`…-scratch-<agent>`），因此一个agent的实验不是阅读共享树的其他人可见。作为后盾，每波中的每个 agent 也会被告知哪些路径（如果有）与启动时正在审查的提交不同，并且仅限于这些路径的失败不是一个发现。在审查结束时，所有这些树都会与工作树一起被清除。

## 交叉仓库 PR 审查

您可以通过传递完整的 URL 来查看其他存储库中的 PR：

```bash
/review https://github.com/other-org/other-repo/pull/456
```

它以 ** 轻量级模式 ** 运行 - 没有工作树，没有 build/test. 审查仅基于差异文本（通过 GitHub API 获取）。 PR 如果您有写入权限，仍然可以发布评论。

|能力|相同的仓库|交叉仓库|
| ----------------------------------------------------------------------------- | --------- | ------------------------------ |
|LLM 审核（Agents 0、1a、1b、1d、1e、2-6 + 验证 + 迭代逆向审核）| ✅        | ✅                             |
|Agent 1c：跨文件跟踪器| ✅        |❌（没有 grep 的本地代码库）|
|Agent 7：构建和测试| ✅        |❌（没有本地代码库）|
|Agent 8：Diff 专用查找器（0-2，当域需要时）| ✅        |✅（只需要差异）|
|PR 内嵌评论| ✅        |✅（如果您有写入权限）|
|增量评论缓存| ✅        | ❌                             |

## PR 内嵌评论

使用`--comment`直接在PR上发布调查结果：

```bash
/review 123 --comment
```

或者，运行 `/review 123` 后，输入 `post comments` 即可发布调查结果，而无需重新运行审核。

**发布内容：**

- 高可信度的批评和建议结果作为特定行的内联注释，每个注释都以 `**[Critical]**` 或 `**[Suggestion]**` 为前缀，因此阻止者与建议区分开来
- 如果修复是单个本地化编辑，则可以一键应用 ` ``` 建议块
- 对于 Approve/Request 更改判决：带有判决的审查摘要
- 对于发布了所有内嵌评论的评论判决：没有单独的摘要（内嵌评论就足够了）
- 每条评论的型号和 CLI 版本归属页脚（例如，_— qwen3-coder via Qwen Code /review (v0.21.2)_）；在您的用户或系统 `settings.json` 中将 `review.attribution` 设置为 `false`（`review.*` 设置会忽略工作区 `.qwen/settings.json`），以便在没有它的情况下发布 - 评论和正文列表也会丢失 `**[Critical]**`/`**[Suggestion]**` 严重性标记，并且该模型从审查的机器分类帐标记中保留，因此在新环境（无审查缓存）中，恢复的增量锚点无法通过相同模型检查，并且重新审查回落到全范围

**仅保留终端的内容：**

- 很高兴有发现
- 低置信度调查结果

**自行创作的 PR：** GitHub 不允许您在自己的拉取请求中提交 `APPROVE` 或 `REQUEST_CHANGES` 评论 - 两者都会失败并显示 HTTP 422。当`/review`检测到PR作者与当前认证用户匹配时，无论判决如何，都会自动将API事件降级为`COMMENT`，因此提交仍然成功。终端仍然显示诚实的判决（“批准”/“请求更改”/“评论”）——只有 GitHub 侧审核事件被无效。实际的调查结果仍然显示为特定行的内联评论，因此实质性反馈没有变化。

**使用先前的 Qwen Code 评论重新审核 PR：** 当 `/review` 在已有 Qwen Code 审核评论的 PR 上运行时，它会在发布新评论之前对它们进行分类。只有 ** 同行重叠 **（作为新发现对同一 `(path, line)` 的现有注释）会提示您确认 - 在这种情况下，您会在同一代码行上看到视觉重复。来自旧提交的评论、回复的评论（视为已解决）以及与任何新发现不重叠的评论都会被默默地跳过，并带有终端日志行，以便您知道过滤了什么。

**CI / APPROVE之前构建状态检查：**如果判决为“批准”，则`/review`在提交之前查询PR的检查运行和提交状态。如果任何检查失败（或所有检查仍待处理），API 事件会自动从 `APPROVE` 降级为 `COMMENT`，审核机构会解释原因。理由：LLM 审查静态读取代码，无法看到运行时测试失败；在 CI 为红色时批准会产生误导。内联调查结果仍保持不变。如果您无论如何都想批准（例如，已知不稳定的 CI 失败），请在验证后手动提交 GitHub 批准。

## 应用研究结果 (`--fix`)

`--fix` 是`--comment` 的反映。 `--comment` 写入 ** 拉取请求 **，因此需要一个； `--fix` 写入 ** 工作树 **，因此它需要一个比审查寿命更长的树：

```bash
/review --fix                 # local uncommitted changes
/review src/auth.ts --fix     # a single file
```

在 **PR 目标上，它会被忽略，并显示警告 ** — PR 审阅在临时工作树中运行，该工作树在审阅结束时被删除，因此其中的“固定”编辑会在几分钟后被丢弃。使用 `--comment` 来发布调查结果。

有效的 `--fix` ** 会降低中等 ** 的工作量，因为它会编辑你的文件，并且 `low` 不会运行任何验证：应用未经验证的发现与发布一个相同的错误，针对的是你的工作树而不是某人的 PR。它不会强制`high` - 媒体的发现得到验证，并且反向审计`high`增加了对_缺失_的发现的搜寻，这并不是决定是否应用一个开启的因素。

审核后，每个发现都会应用 `edit` 工具，然后 ** 占 **，采用以下三种方式之一：

|结果|含义|留在你的盘子里吗？|
| ------------------ | ----------------------------------------------------- | -------------------- |
| `fixed`            |编辑内容在您的树中|否|
| `skipped`          |真实的，未应用的——原因会同时报告|是的|
| `no_change_needed` |发现是错误的，或者代码已经处理了它|否|

当某个发现的修复会改变预期行为、需要在审查的差异之外进行更改或在第二次查看时发现是误报时，就会跳过该发现。

**每项发现都会得到一个结果，这是强制执行的，而不是要求的。**账本会经过`qwen review findings --outcomes`，它拒绝一组不涵盖所有这些结果的集合——一个修复程序应用了九个发现中的六个，并报告六个没有对其中任何一个撒谎，它默默地缩短了列表，你将无法看到掉下来的三个。

**然后由agent审核所应用的修复，而不是通过重新审核您的树。**审核不会在刚刚编辑的代码上重新运行自身（这将是对不同代码的新审核，带有此结论）。相反，它会在第一次编辑之前记录您的工作树（`qwen review fix-delta --snapshot`，通过一次性索引 - 您自己的索引和存储从未被触及），之后将树与该记录进行比较（`fix-delta --since`），并将这些块与每个声称要关闭的 `fixed` 结果一起准确地交给单个 `fix-audit` agent。 agent 从未看到过已审查的差异，也没有提交任何发现。 （这不是关键姿势 PR 重新审查运行的更窄的修复审核形状，如下所述：该轮审查拉取请求，并且此 agent 仅在本地和文件目标上运行 - 在 `--fix` 之后，或在输入 `fix these issues` 之后。）它回答了每个大块的一个问题：这个编辑新假设了什么 - a绑定、密钥、生命周期、共享资源、排序——树中是否有任何内容固定它（变红的测试、类型、值派生的单一来源）？只有那些没有固定的人会回来，每个人都带着可以固定它的东西。它们在终端的 **修复审核 ** 块中报告，并作为受影响的调查结果的结果注释；它们不是发现，不改变结论，留给你采取行动 - 存在这种情况的衡量原因是，多轮审查的后来发现的三分之一是由紧邻其之前的修复引入的，而真正的修复轮交付的两个批评正是这种类型的假设，即预期修复站点的突变探针无法到达（该轮是PR审查，其作者应用了发布的评论 - 此审计不运行的路径；它运行在哪里`--fix` 对本地和文件目标有效）。如果没有 `fixed` 并且没有应用任何内容，则跳过审核并如此说明；如果账本和树不一致——除了落地的大块之外没有`fixed`结果，或者根本没有大块旁边有`fixed`结果——你会被告知，因为这是一个簿记问题而不是审计问题。 `fixed` 发现任何大块都没有接触到任何位置，审计员会将其报告为未经证实而不是拒绝，因为修复程序可以合法地完全进入该发现未命名的文件中。这些帅哥有一个固定的范围，打印在审计旁边：文件 HEAD 跟踪，并且所有其他文件都没有忽略规则隐藏。子模块或嵌套存储库内的编辑、对 gitignored 文件 HEAD 不跟踪的编辑，或对审阅的 `.qwen/tmp` 名称系列（`qwen-review-*`、`review-pr-*`、`file-review-*`）中的任何路径的编辑都不在其中； a hunk 显示 git 存储的文件（二进制文件仅作为 `Binary files … differ`，Git LFS 文件作为其指针）；更改忽略规则的编辑会将它们隐藏的文件作为添加项；并报告快照和差异之间所做的提交 - 提交的编辑仍在 hunks 中，但提交开始跟踪的 gitignored 文件却不是。

## 恢复中断的审核 (`--resume`)

一个中途终止的长审查——连接断开、超时、终端终止——将它所做的一切留在磁盘上：工作树、捕获的差异以及线束自己运行的每个 agent 的记录。 `--resume` 从那里继续而不是重新开始：

```bash
/review 123 --resume
```

它仅适用于 **PR 目标 **（本地审核的差异来自实时工作树，没有稳定的中断状态可以继续），并且只要您不确定：磁盘状态本身的审核规则 - 工作树仍处于获取的提交和干净状态，捕获的差异逐字节未更改，PR 头未移动，则可以安全地通过，简历限制未用完——只要任何内容不再匹配，就会默默地重新开始，告诉您哪张支票被拒绝。后续重用了先前尝试的经过认证的 agent 结果，因此报告会说明恢复了多少个；它是公开的，绝不是覆盖范围的差距。

有两件事需要知道。仅使用内置目标默认值时，继续会保留中断运行的记录 ** 工作量 **。显式`--effort`、项目记忆级别、操作符`review.effort`设置或有效`--comment`提供所需级别；如果它与中断的运行不同，则拒绝恢复并在该级别开始新的运行，因为不同的努力是不同的工作。如果 PR 头在审核停止时移动，则简历会拒绝 (`head-moved`)，并且新运行会审核新提交 - 这就是您想要的，并且它算作本次审核的一次重新启动。

## 作为数据的调查结果

已确认的发现在其他任何内容消耗之前都会被规范化为 `.qwen/tmp/qwen-review-<target>-findings.json` — 终端报告、保存的 Markdown 报告和 PR 评论 JSON 都读取该工件，而不是重新键入列表。每个发现都带有唯一的 `id`（结果和解析的锚点加入）、`severity`、`confidence`、`source`、`summary`、上限为 60 字符的 `shortSummary` 用于列表渲染， `failureScenario`，以及一个或多个 `locations` - 模式聚合的结果使 ** 每次出现一个位置 **，因此每个仍然有自己的内联注释。

**首先，审查会检查它是否正在运行您的代码。**每个`qwen review …`步骤都会运行构建的包，而不是工作树，因此自上次构建以来编辑的审查命令不起作用，并且运行会测量旧的行为。构建记录了它捆绑的评论源的摘要； `parse-args`重新推导它并进行比较，并且`drive`再次检查，因为验证者简报直接发送agents而没有步骤1。如果不匹配，它会在 stderr 上显示该包不是从这些源构建的，以及要重建的内容。当 CLI 解析为捆绑的 `dist/cli.js`（`qwen` 二进制文件或 `node dist/cli.js`）时，检查运行；运行非捆绑输出的启动器，例如 `npm start` 和 `npm run dev`，请跳过它。它无法比较的两种情况会被区别对待：其构建早于记录的签出被告知检查无法运行以及原因，而已安装的软件包（没有任何来源不同）则保持沉默。摘要涵盖审阅命令、注册它们的文件、从目录外部导入的仅审阅租约以及捆绑的审阅技能；它不会跟随它们进入它们导入的共享帮助程序，因此安静运行意味着审查代码与包匹配，而不是与整个树匹配。

**严重，已经失败的基础树被保留，不归档。**当测试命令失败并且可以构建合并基础时，`test-delta`记录哪些失败文件在没有拉取请求的情况下也会失败。规范化会读回该测量值（`qwen review findings --test-delta`，在 `--outcomes` 旁边）：将其自己的文本命名其中一个文件的批评者降级为建议，保留其证据，获得将其降级的测量值和 `heldByMeasurement` 字段，然后宣布降级。已经是红色的测试不是这个拉取请求变成红色的测试 - 如果它现在因 _ 新的 _ 原因而失败，请说出哪个测试，引用双方，然后再次将其归档到“关键”：已包含测量值并无论如何提出的发现将留在您放置的位置。

该命令在写入时进行验证：重复的 id、没有故障情况的发现、空的位置数组或未知的严重性都是错误，而不是无提示地损坏的条目。

图像从哪里来？对于终端渲染声明，`qwen review capture-tui` 在 ** 私有 tmux 服务器 ** 中驱动正在审查的代码（它无法触及您自己的 tmux 会话），将窗格字节捕获为 `.ans`，并通过以下方式渲染 `.png` `freeze` 安装时 — 显式降级（`png` → `ans-only` → 拒绝）并记录它到达哪个梯级，因为验证者必须说明其结论是否基于像素、字节或散文。

## PR 评论中的证据图像

GitHub 的 API 无法将图像附加到审阅评论，因此 `/review` 可以将证据图像（TUI 屏幕截图、渲染输出比较）托管在您指定的存储库中，并通过 URL 嵌入它们：

```bash
export QWEN_REVIEW_ASSETS_REPO=your-org/your-repo   # a repo you can push to
/review 123 --comment
```

将其指向您可以推送到的存储库 - 建议使用专用的映像托管存储库；分叉或临时存储库也可以。避免审查存储库本身：推送到那里的图像分支成为每个克隆获取的可访问对象。图像以内容散列名称降落在 `pr-assets/<pr>-review` 分支上，注释通过 ** 提交固定 ** URL 引用它们 - 即使分支稍后移动，也是不可变的，并且在 GitHub Enterprise 上保持不变。

对于 GitHub 触发的审核（PR-审核工作流程），相同的变量从同名的 ** 存储库变量 ** 连接，并且工作流程仅通过专用外部主机：未设置变量，或设置为正在审核的存储库（在修剪和大小写标准化之后进行比较，因此已填充）或者同一存储库的大小写转换拼写视为自我定位），工作流程传递空值并且发布拒绝 - 没有任何变化，并且审查将其证据保留为散文和本地工件路径。维护人员将存储库的 Actions 变量中的 `QWEN_REVIEW_ASSETS_REPO` 设置为单独的图像主机存储库，可以让评论评论嵌入捕获的 PNG。外部目的地管理其自身的保留；视觉清理工作流程仅清除前 Git 支持的发布者在此存储库中留下的历史 `pr-assets/*` 分支。

发布的门控与发布完全相同：没有指定的存储库意味着没有发布，并且未经授权的运行（无效的`--comment`）会被拒绝，就像`submit`拒绝一样。仅接受图像类型（故意排除 SVG），具有大小上限，并且每个文件的字节必须与其扩展声明的格式匹配 - 拒绝错误标记或无法识别的内容。清单记录了推送的每个文件。如果没有指定，调查结果会将其证据保留为终端中的本地文件路径并保存报告 - 不会中断，评论只是保留文本。

## 后续行动

审核后，上下文感知提示将显示为幽灵文本。按 Tab 接受：

|审核后状态|提示|会发生什么|
| -------------------------------- | ------------------ | --------------------------------------- |
|本地审核，`--fix`未通过| `fix these issues` |LLM 以交互方式修复每个发现|
|PR 审查结果| `post comments`    |帖子 PR 内嵌评论（无需重新审核）|
|PR 审查，零发现| `post comments`    |批准 GitHub (LGTM) 上的 PR|
|本地审核，一切清晰| `commit`           |提交您的更改|

注意：`fix these issues`仅适用于本地评论，出于同样的原因`--fix` - 对于PR评论，工作树在评论后被清理，因此评论后交互式修复是不可能的；请使用 `--comment` 或 `post comments` 来发布调查结果。当 `--fix` 通过时，调查结果已经包含结果，并且没有提供修复提示。

## 项目评审规则

您可以自定义每个项目的审核标准。 `/review` 从这些文件中读取规则（按顺序）：

1. `.qwen/review-rules.md`（Qwen Code本机）
2. `.github/copilot-instructions.md`（首选）或 `copilot-instructions.md`（后备 — 仅加载一个，而不是两者）
3. `AGENTS.md` — `## Code Review` 部分
4. `QWEN.md` — `## Code Review` 部分

规则作为附加条件注入到 LLM 审核 agents (0-6) 中。对于 PR 审核，从 ** 基础分支 ** 读取规则，以防止恶意 PR 注入绕过规则。

## 存储库上下文

存储库可以通过将严格的 JSON 清单提交给 `.qwen/review-context.json` 来向审阅者提供有限的、特定于存储库的指导。在中等或高度努力下，`/review` 在捕获计划后读取清单，并在任何 agent 启动之前附加匹配的指南：

```json
{
  "version": 1,
  "label": "Example repository",
  "rules": [
    {
      "paths": ["packages/*/src/**"],
      "domains": ["runtime"],
      "relatedPaths": ["packages/runtime/src/**"],
      "recommendedTests": ["npm run test:runtime"],
      "requiredConfigurations": ["debug"],
      "requiredAgents": ["test-matrix"],
      "unverifiedDimensions": ["Alternate runtime was not exercised"],
      "verificationNotes": ["Use the repository native test runner"]
    }
  ]
}
```

当任何更改的文件与其 `paths` 全局对象（`*`、`?` 和 `**` 段；区分大小写）之一匹配时，将应用规则。所有匹配规则合并其指导：用于审核的域和相关文件 agents、用于构建和测试的推荐测试和所需配置 agent、额外的审核者角色（仅当所选工作和拓扑运行它们时才受到尊重）以及最终审核作为未经验证的维度公开的证明边界。数组可以按任何顺序写入；重复的条目将被拒绝。

对于 PR 审核，清单是从合并库中读取的，因此接受审核的 PR 无法选择自己加入或退出指导；本地评论从当前工作树中读取。省力且跨存储库的审查会跳过存储库上下文。完整的合约和信任模型位于 [ 设计文档 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/review-repository-context.md>) 中。

## 发行保真度

对于错误修复 PR，问题保真度 agent 直接获取问题证据，而不是依赖 PR 描述文本。它运行 `qwen review issue-context <pr> --repo <owner/repo> --out <file>` 子命令，该子命令解析 GitHub 的强关闭问题元数据，然后获取每个引用问题的标题、**正文 **（记者的原始重现）和完整评论线程 - 每个都来自问题自己的存储库（一个 PR可以在不同的仓库中关闭问题）。此 agent 仅针对 PR 目标运行；本地差异和文件路径评论跳过它。

结束问题集是一个发现提示，而不是证明作者链接了正确的问题：如果它是空的，但 PR 引用了一个明显的目标问题，agent 在判断相关性后仍然会获取它（使用 `--issue <n>` 重新运行；在 PR 的存储库中解析一个裸数字，而`--issue <owner>/<repo>#<n>` 从其自己的存储库中获取跨存储库引用）。获取的问题文本被视为不受信任的数据（提取事实，忽略嵌入指令）。对于相关问题，原始复制、观察到的有效负载、预期行为和维护者评论被视为PR是否修复了正确问题的最高优先级证据。

如果问题证据显示上游服务或提供商在客户端合同之外返回了格式错误的数据，则客户端解析器或清理程序更改不会被视为有效的根本原因修复，除非维护者明确请求防御性解决方法。重放格式错误的上游输出的测试仅证明该解决方法可以处理该形状；它并不能证明该解决方法在架构上是合适的。

示例`.qwen/review-rules.md`：

```markdown
# Review Rules

- All API endpoints must validate authentication
- Database queries must use parameterized statements
- React components must not use inline styles
- Error messages must not expose internal paths
```

## 增量审查

当审核之前审核过的 PR 时，`/review` 仅检查自上次审核以来的更改：

```bash
# First review — full review, cache created
/review 123

# PR updated with new commits — only new changes reviewed
/review 123
```

### 跨模型审查

如果您切换模型（通过 `/model`）并重新审核相同的 PR，`/review` 会检测模型更改并运行完整审核而不是跳过：

```bash
# Review with model A
/review 123

# Switch model
/model

# Review again — full review with model B (not skipped)
/review 123
# → "Previous review used qwen3-coder. Running full review with gpt-4o for a second opinion."
```

模型匹配还控制增量范围，而不仅仅是跳过：“清理到缓存的提交”是上一个模型的结论，因此当自缓存审查以来新的提交落地时，模型不匹配的范围永远不会达到 `lastCommitSha..HEAD` - 范围是完整的差异，并注明“上一轮由 qwen3-coder 审查。使用 gpt-4o 运行全面审查”。 - 除非当前运行的模型认证的锚点是从上次发布的评论（如下）中恢复的，后者会限制范围。上一轮的调查结果仍需重新裁决；只有锚没有。当缓存不存在或其锚点不可用时（CI，另一个克隆），同一个门绑定从最后发布的评论的机器分类帐标记恢复的锚点：仅当现在运行的模型对其进行认证时，它才会限定增量范围 - 由不同模型认证的标记，或不携带模型（在 `review.attribution` 关闭的情况下发布的评论，或来自字段之前的评论），回退到完整差异。未关闭的回合会在没有锚点的情况下干净地发布其标记（它无法证明范围），但是当该回合的工作列表完整保存时，这种损失并不粘性：恢复将锚点从您自己的帐户发布的最近的较早标记向前嫁接，因此单个非干净回合不再强制每个后续回合重新读取完整差异 - 下一轮范围 `anchor..HEAD`，它重新覆盖非干净回合无法覆盖的范围证明。大小上限回合的移植被拒绝（丢弃的发现将落在移植范围之外并默默退出），因此后面的回合不断重新读取完整的差异，直到完整的标记落地。

**关键态势重新审查运行范围较窄（修复审核形状）。**一旦PR的重新审查将回合自适应发帖底限解决为仅限关键（从回合6，或更早时平坦的首次发现趋势锁定 - 请参阅收敛姿势）_和_存在可用的增量锚点，回合停止重新运行1的形状。捕获命令根据上一轮发布的标记预测下限，并在计划中记录 `incremental.posture: "critical"`，然后这一轮：将 Delta 的每个块扇出一个区域 agent（无论其大小如何 — 没有设置 3A 维度，但保持问题保真度 agent：无论 Delta 显示什么，它都会针对问题重新检查头，并且是唯一可以看到的审计员修复提交删除行为（当删除出现在缩小范围的任何一侧时所需的问题）；重新输入每个仍然干净的 _ 交互 _ 文件及其自己的全范围块，与任何其他增量回合完全相同；并缩小反向审计波次而不是限制它们——从波3开始，在一次实质性的干审计（记录证明干审计的每个成员都启动一次）之后，没有包含增量文件的块会离开时间表——轮次1和2一起启动并算作一次启动，因此在屈服或未经认证的合作伙伴或兄弟姐妹旁边的干收据不是一），虽然三角洲地区保留完整的退休规则和任何非三角洲块，但波浪无法证明干燥回落到相同的规则（干燥收据建立在与先前一轮与屈服或未经认证的成员完全相同的结果列表上的干燥收据也是如此，是在未干燥的较早回报返回之前建立的，或者在与未干燥的早期回报不同的会话中运行或在没有会话标记的情况下运行，就像中断审查的恢复一半一样），因此后期波浪（其中修复引发的批评（可衡量地浮现出来）继续在不断缩小的前沿运行。严重程度没有改变——姿势决定了哪些帖子，而不是发现了什么——并且每一次减少都会被披露：在计划中，在该轮的`posture narrowing:`注释中（其中列出了每一块缩小成本，以及在浪潮中保留一个的规则），并在发布的审查正文中以一句话“圆形”披露。该计划的姿势记录本身就是撰写时楼层分辨率的一个分支，因此修复审核轮次的发布栏不能与其运行的形状不一致（即使在撰写时楼层的通常输入不可用，次关键结果也会延迟）。显式的 `--severity-floor suggestion` 会关闭形状和姿势（并且在发布时超越过时的计划记录）；明确的 `--severity-floor critical` 可以从第一次锚定重新审核中启用它。

缓存存储在 `.qwen/review-cache/` 中，并跟踪提交 SHA 和模型 ID。确保此目录位于您的 `.gitignore` 中（更广泛的规则如 `.qwen/*` 也适用）。在 GitHub 上，如果缓存的提交被重新设定基准或强制推送，它将回退到全面审查； Aone 以不同的方式规则缓存的锚点 - 请参阅下面的段落。只有付出很大努力的审阅才会查阅或写入缓存 - `--effort low|medium` 快速通过永远不会被视为“已审阅”。

## 审查报告

对于同一仓库评论，结果将作为 Markdown 文件保存在项目的 `.qwen/reviews/` 目录中（跨仓库轻量级评论跳过报告持久性）：

```
.qwen/reviews/2026-04-06-143022-pr-123.md
.qwen/reviews/2026-04-06-150510-local.md
```

报告包括：时间戳、差异统计、build/test 结果、所有带有验证状态的结果以及判决。章节标题和描述性散文遵循输出语言偏好；技术标识符（SHA、文件路径、门名称、查找 ID）保持逐字记录。

中度和高度努力的评论还保存具有相同主干（例如，`2026-04-06-143022-pr-123.json`）的结构化 JSON 同伴，其中将规范发现和组成的结论作为数据。 Qwen Code 的 Web Shell 将该文档呈现为具有可过滤结果的交互式审阅视图； Markdown 报告保留了人类可读的档案。

管道的确定性部分 - 参数解析 (`qwen review parse-args`) 和 event/body 决策 (`qwen review compose-review`) - 是测试子命令而不是提示文本，因此 `--effort` 语法、`--comment` 强制、判决上限和降级行为由单元测试固定，不能随模型漂移。

**GitHub Enterprise：** 在非 `github.com` 主机上审核 PR URL 会路由该主机上的每个 GitHub 调用 — review 子命令 (`match-remote`, `meta`、`fetch-pr`、`pr-context`、`comment-status`、`issue-context`、`fetch-diff`、`comment-body`、`plan-diff`、`test-plan`、 `presubmit`、`compose-review`、`submit`、`publish-assets`) 接受 `--host` 并将其设置在代码中，因此被遗忘的主机无法默默地将评论重定向到 `github.com`。

**Aone 代码：** 对于起源位于 `gitlab.alibaba-inc.com` 的克隆，从该克隆内部运行 `/review` — 从远程检测到平台并且子命令工作，由 `a1` CLI 支持（至少0.1.90 — 旧版安装在身份验证时被拒绝，并显示升级消息） — 目标号码是全局 MR id。 `fetch-pr` 获取 `refs/merge-requests/<id>/head` 并构建工作树 + diff，因此工作树的 agent 审查不变，并且 `test-plan` 也可以工作 - 它通过同一个阅读器读取 MR 描述。 `pr-context` 也得到支持：它读取 MR 的元数据、讨论线程和之前发布的 qwen 摘要（机器分类帐从中恢复），因此 Aone 运行看到 MR 的现有讨论，就像 GitHub 运行看到 PR 的讨论一样。 `comment-status` 和 `presubmit` 也是 a1 支持的（完全预提交：自我 PR 检测、头部漂移、合并门 CI 和现有评论去重），因此重复 `--comment` 轮去重MR 的现有评论而不是重新发布它们（平台标记为过时的线程 - 修改后其行不再映射 - 保持可重新发布），并且自我 PR 检测也有效。 `publish-assets` 写入被跳过。 `--comment` **通过`a1` CLI发布**评论：每个内联发现一条评论，然后是摘要评论。 Aone 没有本机请求更改状态 - 根据该判决，摘要评论带有阻止标头，并且实际发布的任何内联批评都会通过讨论门阻止合并，而他们的讨论仍未解决（当没有发布内联批评时，标头是建议性的，并且没有任何内容会机械地阻止合并）。发布的评论没有 AI- 评论标志 - `a1` 无法设置 - 因此存储库的专用 `ai_comment` 合并门不会跟踪它们。当运行读取 MR 的上下文时，本机 `a1 repo mr approve` 会触发批准判决（与 GitHub 相同的门；上下文不可用的运行在注释处保持上限）。增量重新审查遵循 AGit-Flow 更新模型：更新 AMENDS 单个 CR 提交到位，孤立上一轮审查的头部 - 因此缓存的锚点被规则为 WITHOUT 祖先（每次更新的锚点后面测试都会失败），并且重新审查范围PR 自己对更新所涉及的文件的差异，而不是退回到全面审查；仅当变基的漂移保留在 CR 的文件内时，也重新基于较新的主版本的更新才会保留该范围 - 接触任何其他文件的漂移会回落到完整审查，并且无论哪种方式，都没有漂移字节进入已发布的范围。请参阅`docs/design/2026-08-15-review-aone-provider.md`。

每次运行都以一个机器可读行 (`Review complete: <target> — <disposition>`) 结束，因此脚本和 CI 包装器可以通过单个 `^Review complete: ` 匹配来检测完成和结果。

## 无头运行 (`qwen review run`)

`/review` 是交互式的。当脚本或 CI 作业需要运行审核并对其结果采取行动时，请使用无头包装器：

```bash
qwen review run [target] [--json] [--fail-on request-changes] [--comment] [--resume] [--quiet]
```

`target` 是 PR 数字、PR URL 或文件路径；省略它以查看本地工作树。该命令以非交互方式运行此构建自己的 CLI（标准输入关闭，因此斜杠命令检测仍然存在），将子进程的进度流式传输到 **stderr**，并将判决打印到 **stdout** — 或者， `--json`，完整结果对象。判决是从`compose-review`写入的工件中读取的（技能将相同的JSON视为判决权威），从未从模型的散文中解析。

退出代码是门应该读取的合同：

|退出|含义|
| ---- | ------------------------------------------------------------------------------------------------- |
| `0`  |审查已完成（无论决定如何）|
| `1`  |它从未得出结论——孩子失败、超时或没有留下任何组成的工件|
| `3`  |它完成了 `REQUEST_CHANGES` ** 和 ** `--fail-on request-changes` 设置（选择加入阻止）|

`3`（不是 `2`）让门区分“审查被阻止”和“工具损坏”——yargs 已经使用 `1` 来处理使用错误——而不解析任何输出。 `--timeout-minutes`（默认 120，位于 1）终止挂起的审阅并退出 `1`，取消命令 (Ctrl+C / SIGTERM) 将终止审阅的进程组，而不是将其孤立。

`--resume` 继续对相同的 PR 进行中断的审查，而不是重新开始 - 当长时间本地运行中途终止时（连接断开、超时、终止终端），重试将重新获取、重新分块并重新启动其工作已在磁盘上的 agents。重试时无条件传递是安全的：磁盘状态本身的 `fetch-pr` 规则（工作树仍然处于获取的 SHA 状态并且干净，差异字节不变，PR 头未移动，恢复上限未使用），并且每当任何内容不再匹配时都会默默地回退到新的审查，因此该标志永远不会失败可以重新开始的运行。当当前调用仅具有内置目标默认值时，延续将保持固定到中断运行的记录工作量。显式`--effort`、项目记忆级别、操作符`review.effort`设置或有效`--comment`提供所需级别；不匹配会拒绝简历并在该级别上运行。 PR 仅目标（本地审查的差异是从实时工作树中捕获的，该树没有稳定的中断状态可以继续）。恢复是**本地便利**：存储库自己的CI审查工作流程确实**而不是**恢复 - 每次重试都会重新运行，因为CI尝试运行无沙箱并且其工作树被删除退出，不留下任何中断状态继续。

时间预算运行还可以导出**软**截止日期，以便审核停止其开放式反向审核循环，同时仍有时间进行验证、撰写和发布：`QWEN_REVIEW_DEADLINE_EPOCH`是运行将被终止的Unix秒时刻，`QWEN_REVIEW_DEADLINE_RESERVE_SECONDS`（默认4800； `0`仅保留轮次估计）是最后一轮验证、`compose-review`和提交必须保留的尾部。当剩余预算不再适合另一轮加上该尾部时，轮构建者拒绝构建它，并且所组成的判决会公开截断的审计（否则批准判决的上限为注释）。如果没有该变量，审核将使用捕获时记录的墙：`qwen review fetch-pr` / `capture-local` / `plan-diff` 上的 `--deadline <minutes>`，或默认情况下 **8 小时 ** 上的小（步骤 3A）差异，**12**在一个大的（3B）上，**16**在一个巨大的上 - 由差异自己的线确定大小，因此关键姿势修复审核回合保持墙其三角洲的大小，即使它按区域扇出并采用分块的圆形上限。该默认值是一个活跃度界限——其大小高于在圆形上限下健康运行所花费的大小——因此它结束了一个已停止收敛的循环，并留下了一个保持运行不变的运行；它按经过的时间（包括空闲时间）收费，因此过夜的会话会花费它，并且由于下一轮的定价是根据上一轮的入场时间来定价的，因此轮之间的几个小时暂停可以停止运行，而时间仍在墙上（新会话中的 `--resume` 会重新启动墙和定价；下面的 ** 审核截止日期 ** 给出了上限）。它在下一轮准入或验证构建时停止循环；它不会中断已经运行的构建。计划记录的墙保留其自身的三分之一（上限为 4800 秒，位于撰写层）作为尾部保留，除非设置了保留变量；无法保持收敛的 `--deadline`（两轮加上保留 - 等于或低于九十分钟，或者当 shell 的保留或 compose-floor 覆盖提高标准时更多；当环境导出格式良好的纪元时，第二条腿被跳过，因为那时标志是惰性的，并且它记录的墙在稍后 env-less `--resume` 运行的任何 shell 中再次定价）被拒绝前面，一旦扇出和验证用完，它上面的墙仍然可以在回合 1 之前停止。当墙不再允许重建时，覆盖检查将另外规定（反向审计轮次，或组合层下的验证者分片），`compose-review` 保留该 `FIX:` 行并打印 `NOTE:` 行，其中包含门的算术；差距仍然在发布的正文中披露。计划墙界定了圆形构建器和验证器；两个读者独自留在环境中 - 生成的审核工作流程的启动限额，以及 `qwen review run` 自己的 `--timeout-minutes` 杀戮，该杀戮不会作为截止日期导出。该计划将墙存储为从尝试开始的持续时间，因此新会话中的 `--resume` 会更新它（同一会话继续尝试及其墙；在恢复运行时将忽略标志本身 - 仅检查其语法和默认规则 - 并且运行会说明计划保留的墙以及剩余的数量，或者它已经用完，以及哪个构建的门现在将拒绝；墙只能在同一会话恢复，或者当分类帐未能记录尝试时，因此最便宜的方法是从新会话恢复，这会更新墙并保留工作树；没有 `--resume` 的新审查也记录了新墙，并且该新捕获上的 `--deadline none` 没有记录）。 `--deadline none` 不记录任何墙，并保留仅由圆帽界定的循环 - 除非环境导出一个纪元，该纪元无论如何都会对运行进行计时。 `/review` 斜杠命令不采用该标志：它会警告并将其与可能为 1 的值（整分钟或 `none` — 因此在删除后立即键入 PR 数字）一起删除，而其后的任何其他值都会像无效的 `--effort` 值一样被处理（ PR URL 成为目标；会报告拼写错误，并且仅作为唯一文件形状的标记保留），并且技能驱动的运行始终使用默认值。巨大层的回合减少（3 回合而不是 5）仅在明确的截止日期（环境或标志的）上起作用，而不是默认的。畸形环境的最后期限落到了计划的墙上；格式错误的计划字段使审查不受限制——外部超时仍然限制运行。

嵌套在该保留区内的是一个较小的**组合楼层**，`QWEN_REVIEW_DEADLINE_COMPOSE_FLOOR_SECONDS`（默认1200；`0`在每个点（包括超过截止日期）完全禁用此门）。保留是一个涵盖“验证上一轮 **plus** 撰写 **plus** 提交”的数字，它适合正常的每次发现重新跟踪，但不适合安全审查，其验证会无限制地重新运行真实的 filesystem/git 工作负载。因此，验证者（而不是回合构建者）被限制在这一层：一旦剩余的楼层或更少，`agent-prompt --role verify`拒绝构建（一条`VERIFY BUDGET:`线，退出**4**），手中的发现保留其未经验证的标签（这限制了判决），并且`compose-review`和提交运行。根据计划自己的规则，地板永远不会超过保留区（计划记录的墙的保留区可以正好位于其上），并且圆形门对保留区顶部的回合进行定价，因此健康运行首先会遇到反向审核门，并且永远不会到达它（地板下方的显式 `QWEN_REVIEW_DEADLINE_RESERVE_SECONDS` - `0` 是记录的逃生舱口 - 是操作员的选择，然后验证者的门可以首先触发）；它是保护区无法限制的一跨度的盖子。

## 跨文件影响分析

专用的跨文件跟踪器 (Agent 1c) 拥有此端到端的遍历。当代码更改修改导出的函数、类或接口时，它会搜索所有调用者并检查兼容性：

- 参数count/type更改
- 返回类型更改
- 删除或重命名公共方法
- 重大 API 更改

它还沿着 ** 生产者方向 **：diff 添加的每个字段、选项或可选参数都被跟踪到其读取站点 - 包括 diff 从未触及的文件。读取未填充任何内容的字段的实时代码路径意味着它默默地门控的功能不执行任何操作，并且在读取站点上被标记为“关键”。

对于较大的差异（>10修改的符号），调用者方向分析优先考虑签名更改的函数；制作人的方向永远不受预算限制，因为不变的签名正是它的重点。

## 审查预算

管道中差异大小具有弹性的部分会根据其进行缩放，并将缩放比例写入差异计划中，因此每个阶段都会读取一个数字，而不是每个阶段自行决定：

|预算字段|其范围|如何扩展|
| ---------------- | ------------------------------------------------ | ------------------------------------------------------------------ |
| `inlineAngles`   |跑了多少个 `low` 角度（步骤 3C）|3，加上每条 60 源线，以现有的 6 角度为上限|
| `candidateFloor` |当 `low` 欠一次确定性召回重新通过时| `min(changed files, 4)`                                            |
| `sweep`          |`low`的间隙扫描是否运行|在 25 源代码行下方关闭|
| `specialistCap`  |Agent 8 天花板|0 低于 80 源代码行，否则 2|
| `verifyShard`    |每次验证的结果 agent|Flat at 8 — 验证者的属性，而不是 diff 的属性|

有两件事它故意不做。它**永远不会缩小一个维度**：审查所欠的agents由花名册决定，花名册读取工作量级别，因此小的差异仍然可以获得其安全性通过和测试覆盖率通过。它读取 **source** 行，而不是 diff 行 - 新测试的 40 行生产变更运输 900 行是一个小变化，并且相同的推理已经控制了区域扇出门。

为什么楼层在原来的位置：在九行错别字修复中，六次内联行走相当于五次空无一物的行走，而扫掠——一个新读者在寻找第一遍未到达的内容——当第一遍到达所有内容时，没有什么可寻找的。当低工作量传递保持在 `candidateFloor` 以下时，需要对最大更改源文件中的每个可覆盖块（回退到任何类型的最大可覆盖文件）和每个可覆盖删除块进行一次确定性的第二次查看。接触不可发现的块的文件将从目标选择中排除，并且传递始终发出一个收据，命名其目标、新候选者计数和任何不可发现的块；当没有文件可覆盖时，它仍然检查可覆盖的已删除块并公开丢失的目标。下限是一个停止信号，而不是配额：干净的差异在重新通过后仍然可以报告没有任何发现。 Agent8的底线是实质性的：“一个域支配差异”是一种判断，对四十行进行判断每次都会找到一个支配域，因为四十行通常都是一件事。

## 审核截止日期

每个评论都会在其计划中记录一个挂钟，除非它是在 `qwen review fetch-pr`、`capture-local` 或 `plan-diff` 上使用 `--deadline none` — `--deadline <minutes>` 捕获的，否则在小差异上为 **8 小时 **， **12** 在一个大的上，**16** 在一个巨大的上 - 由 CI 导出的 `QWEN_REVIEW_DEADLINE_EPOCH` 取代它。在 `high` 的努力下，墙在第一轮结束了它不再承认的反向审核循环，并且在 `high` 和 `medium` 的努力下，一旦只剩下撰写楼层，它就会停止验证者；它永远不会中断已经运行的 agent。以这种方式停止的审查会在公开停止和判决上限的情况下做出裁决，仅在剩余的撰写楼层剩余的情况下验证上一轮的发现，并像任何其他审查一样报告其证明的内容。再次运行 `/review` 不会继续停止的审核：在 `high` 的努力下，PR 审核遵循上述增量审核规则，并且可能在未更改的头上报告任何新内容，并且使用其缓存的本地审核可能会在未更改的工作树上以相同的方式停止；经过 `medium` 的努力，审核再次运行。

墙是经过的时间，包括暂停，暂停会收取两次费用：它会花费墙，虽然它是两轮入场之间的最长差距，但它也是下一轮的价格。实践中重要的暂停发生在第一个审计对之后——一起启动的第 1 轮和 2 轮——以及 3 轮之前。在默认保留下，在捕获完成后立即启动的评论最多可以暂停：

|差异|默认墙|储备|第一个审核对后最长的暂停|
| --------------- | ------------ | ------- | ---------------------------------------- |
|小（步骤 3A）| 8 h          |80 分钟|3 小时 20 分钟|
|大（步骤 3B）| 12 h         |80 分钟|5 小时 20 分钟|
|巨大的| 16 h         |80 分钟|7 小时 20 分钟|

这些是上限，同样的规则适用于默认储备下 4 小时或更长的任何墙：在捕获时发射一对后最多 (墙 - 80 分钟) / 2。暂停时间是从双方的发布开始计算的，因此它包括双方自己的审核和与之一起进行的验证者，并且在双方发布之前花费的审核每分钟都会休息半分钟。随后的每个缺口最多保持在前一次入场时保留在储备之上的一半，并且可以保持在更少的水平，例如一旦之前的缺口长于这一半。优先规则、设置墙和保留的环境变量以及过短的 `--deadline` 的预先拒绝在上面的无头运行中进行了描述。 `/review` 斜线命令不采用 `--deadline`，因此从会话开始的审阅仅通过这些环境变量获得不同的墙，并且它们适用于会话运行的每个审阅：`QWEN_REVIEW_DEADLINE_EPOCH` 是所有人共享的一个绝对时刻，并且像显式 `--deadline` 一样，它从 5 中削减了巨大的差异圆帽至 3。新会话中的 `--resume` 会重新启动隔离墙以及中断的 PR 审核的定价。

## 代币效率

高强度管道限制了每个阶段（分片大小、审计轮次），但总调用量随发现结果（`ceil(F/8)` 验证分片）而变化，并且在 3B 以下，随块计数（每轮每个块的反向审计运行）而变化。典型 3A 曲线：

|舞台|LLM 来电|注释|
| -------------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|查看 agents（步骤 3）| 17 (+0-2)                       |并行运行； Agent 1e 仅当 diff 发出包装类型信号时（16 没有它）；交叉存储库跳过 Agents 1c 和 7（15，而计划带有 PR 身份，13 没有它 - Agent 0 和 6d 一起下降）； local/file 跳过 Agent 0 和反帧审核 6d (15)；当评论有树时，在涉及指令文件的差异上再添加一个（`prose-exec`）|
|分片验证（步骤4）|天花板（F/8）|F=调查结果；每次验证最多 8 agent，一起启动|
|迭代逆向审核（步骤 5）|2-10（3A）；回合 × 块 (3B)|连续两轮干轮停止；上限遵循拓扑 - 10 在一个小的差异上，5 在一个分块的差异上，3 在一个明确的截止日期下的巨大差异上。 3B 每轮每个块扇出一名审计员|
|**总计**| **~20-31 (~16-29)**             |3A 相同仓库：~20-31（典型~20-22）；交叉仓库或local/file：〜16-29（地板是13的无身份交叉仓库名册）；当Agent 1e 未入列时，少一位；当`prose-exec` 欠时，多一位； 3B 通过块进行缩放（参见 DESIGN.md）|

大多数 PR 收敛于该范围的下限；这些上限可以防止病理病例的成本失控。在 `--effort low` 处，审核完全内联运行 — **0 子代理调用 ** — 每个角度遍历一次 diff，而不是总共遍历一次。

## 什么是 NOT 标记的

审查有意排除：

- 未更改代码中预先存在的问题（仅关注差异）
- 格式化程序的样式或格式化将自动规范化，或命名与您的代码库约定相匹配 - 但 linter 或类型检查器将标记 NOT 实质性问题（未使用的变量、无法访问的代码、类型错误），这些问题都在范围内
- 主观的“考虑做 X”建议，但没有真正的问题
- 较小的重构无法修复错误或风险
- 缺少文档，除非逻辑确实令人困惑
- 现有 PR 评论中已讨论的问题（避免重复人类反馈）

## 设计理念

> **沉默胜于喧闹。**每条评论都应该值得读者花时间。

- 如果不确定某事是否存在问题 → 不要报告
- 每个发现都指定了一个具体的失败场景（触发→错误的结果）或一个具体的成本——一个不能的发现在到达你之前就被放弃了
- N 个文件中的相同模式 → 聚合为一个发现
- PR 评论仅具有高可信度（并且仅来自经过高度努力、经过验证的评论）
- 外观 style/formatting 匹配代码库约定被排除


## 原文对照

<details>
<summary>展开完整原文</summary>

# Code Review

> Review code changes for correctness, security, performance, and code quality using `/review`.

## Quick Start

```bash
# Review local uncommitted changes
/review

# Review a pull request (by number or URL)
/review 123
/review https://github.com/org/repo/pull/123

# Review and post inline comments on the PR
/review 123 --comment

# Review local changes and apply the findings to your working tree
/review --fix

# Continue a review of the same PR that was interrupted, instead of starting over
/review 123 --resume

# Review a specific file
/review src/utils/auth.ts

# Quick unverified pass (no subagents)
/review --effort low
/review 123 --effort medium
```

If there are no uncommitted changes, `/review` will let you know and stop — no agents are launched.

## Effort Levels

`--effort low|medium|high` trades depth for speed:

| Level    | What runs                                                                                                                                                   | Findings cap        | Verdict                             | Posts to PR      |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ----------------------------------- | ---------------- |
| `low`    | 3-6 directed inline angles over the diff (scaled by diff size) plus a gap sweep and an under-floor re-pass — no subagents, no build/test, no project rules  | 10 (unverified)     | None                                | Never            |
| `medium` | The high pipeline minus its most expensive passes: the parallel finder fan-out over a reduced dimension set, plus build/test and a single verification pass | Uncapped (verified) | Approve capped at Comment           | Never            |
| `high`   | Full pipeline: up to 16 parallel agents → sharded verification → iterative reverse audit                                                                    | Uncapped (verified) | Approve / Request changes / Comment | With `--comment` |

`/review` resolves effort in this order: an explicit `--effort`, the last level explicitly typed for this project, the operator `review.effort` setting, then the built-in target default (**high** for PR reviews, **medium** for local and file reviews). When a remembered level applies, `/review` announces it before work begins; type a new `--effort` to replace it. An effective `--comment` forces high (posted comments must survive verification) — on a non-PR target `--comment` is ignored with a warning and does **not** change the effort. Medium keeps the security and test-coverage agents and build/test, and drops the adversarial personas, the language-pitfall and wrapper/proxy specialists (Agents 1d/1e), the diff-specialist finders and the reverse audit — so a subtle Critical only the second look would surface can slip; use `--effort high` for security-sensitive or pre-release reviews. Only `low` is unverified. Worktree isolation applies to same-repo PR reviews; cross-repo PRs run in lightweight mode (diff-only, no worktree or build/test). The low pass is labeled unverified, emits no verdict, and never writes the incremental review cache, so a later `--effort high` run is never skipped as "already reviewed"; medium is verified but its Approve is capped at Comment, because nothing looked twice for what the first pass missed. The diff-obtaining mechanics are identical at every level — PR reviews always use the isolated worktree and the same base resolution, so the review is never against the wrong base. One scope difference remains: the incremental cache is high-only, so a high re-review may cover just the new commits (`lastCommitSha..HEAD`) while low/medium always review the full PR diff.

## How It Works

The `/review` command runs a multi-stage pipeline:

```
Step 1:  Determine scope + effort level (local diff / PR worktree / file)
         Capture the diff to a file + partition it into chunks
Step 2:  Load project review rules (medium/high)
Step 3C: low effort: 3-6 inline angles + gap sweep + under-floor re-pass
                                                               [0 subagent calls]
Step 3A: high, <=500 src AND <=3200 total: up to 16 agents  [16+ LLM calls]
           |-- Agent 0: Issue Fidelity & Root-Cause Ownership
           |-- Agent 1a: Correctness — line-by-line scan
           |-- Agent 1b: Correctness — removed-behavior audit
           |-- Agent 1c: Correctness — cross-file tracer
           |-- Agent 1d: Correctness — language-pitfall scan
           |-- Agent 1e: Correctness — wrapper/proxy routing
           |     (only when the diff signals a wrapping type)
           |-- Agent 2: Security
           |-- Agent 3a: Reuse & duplication
           |-- Agent 3b: Altitude & abstraction fit
           |-- Agent 3c: Consistency & clarity
           |-- Agent 4: Performance & Efficiency
           |-- Agent 5: Test Coverage
           |-- Agent 6: Undirected Audit (3 personas: 6a/6b/6c)
           |-- Agent 8: Diff-specialized finders (0-2, only when
           |     the diff's domain calls for them)
           '-- Agent 7: Build & Test (runs shell commands)
Step 3B: high, >500 src OR >3200 total: territory x dim.   [N+5..7+3H calls]
           (N chunks, 5-7 whole-diff agents, 3 invariant
            agents per heavy file H)
           |-- 1 chunk agent per ~400 diff lines (all dimensions,
           |     its territory only, returns a coverage receipt)
           |-- 3 invariant agents per heavily-rewritten source
           |     file (whole file; state/timers, counters/
           |      returns/errors, config/early-returns)
           |-- Agent 0: Issue Fidelity      (whole diff)
           |-- Agent 7: Build & Test        (whole repo)
           |-- Agent 1b: Removed-behavior   (whole diff — the
           |     cross-chunk half; chunks keep the local half)
           |-- Agent 1c: Cross-file tracer  (whole diff)
           |-- Agent 8: Specialized finders (whole diff, 0-2)
           '-- Test coverage matrix         (whole diff)
Step 4:  Deduplicate --> Sharded verify (<=8 findings each)
           --> Aggregate                    [ceil(F/8) calls, F=findings]
Step 5:  Iterative reverse audit, fanned out per chunk;
           stop after 2 consecutive dry rounds (cap 10/5/3 by topology)
Step 6:  Present findings + verdict (high; low pass: findings only)
         Canonicalize findings -> .qwen/tmp/...-findings.json
Step 6B: Apply findings + record per-finding outcomes  (--fix only)
         + fix audit: one agent over the applied hunks,
           reports unpinned new assumptions (no verdict)  [1 call]
Step 7:  Submit PR review (inline comments, if requested; high only)
Step 8:  Save report + incremental cache (cache: high only)
Step 9:  Clean up (remove worktree + temp files)
```

Steps 3A/3B/4/5 are the high-effort pipeline; at `--effort low|medium` a single inline pass (Step 3C) replaces them.

### Review Agents

| Agent                             | Focus                                                                                                                                                                                                                                                                                           |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Agent 0: Issue Fidelity           | Linked issue evidence, root-cause ownership, and whether the PR solves the reported problem                                                                                                                                                                                                     |
| Agent 1a: Line-by-line scan       | Walks every hunk plus its enclosing function: wrong conditions, off-by-one, missing `await`, edge cases, race conditions                                                                                                                                                                        |
| Agent 1b: Removed-behavior audit  | Walks every deleted/replaced line: names the invariant it enforced and hunts for where the new code re-establishes it — including removed **exports**, whose replacement often lives in another file and quietly changed a default. In 3B it runs whole-diff (chunk agents keep the local half) |
| Agent 1c: Cross-file tracer       | Walks every changed symbol's callers (consumer direction) and every added field's read sites (producer direction), plus same-PR callee changes                                                                                                                                                  |
| Agent 1d: Language-pitfall scan   | Carries the classic-footgun checklist for the diff's language (`==` coercion, falsy-value traps, loop-variable capture, mutable defaults, nil-map writes, SQL concatenation, DST arithmetic) and pattern-matches every hunk against it                                                          |
| Agent 1e: Wrapper/proxy routing   | For every type the diff adds or modifies that wraps another (cache, proxy, decorator, adapter): every method routes through the wrapped instance, and the wrapper forwards every method callers use. Rostered only when the diff signals a wrapping type                                        |
| Agent 2: Security                 | Injection, XSS, SSRF, auth bypass, sensitive data exposure                                                                                                                                                                                                                                      |
| Agent 3a: Reuse & duplication     | Does the codebase already have this? Greps for the behavior, names the existing helper to call instead, and flags dead code the diff leaves behind                                                                                                                                              |
| Agent 3b: Altitude & abstraction  | Is the fix at the right depth — or a bandaid on shared infrastructure, a downstream compensation for an upstream bug, or an abstraction serving one call site?                                                                                                                                  |
| Agent 3c: Consistency & clarity   | Sibling consistency (a guard one member of a parallel family has but its twin lacks), convention drift against a cited local example, misleading names/comments, needless complexity                                                                                                            |
| Agent 4: Performance & Efficiency | N+1 queries, memory leaks, unnecessary re-renders, bundle size                                                                                                                                                                                                                                  |
| Agent 5: Test Coverage            | Untested code paths in the diff, missing branch coverage, weak assertions                                                                                                                                                                                                                       |
| Agent 6: Undirected Audit         | 3 parallel personas (attacker / 3am-oncall / maintainer) — catches cross-dimensional issues — plus the counter-frame audit (6d, PR targets at high effort): reads the PR description only to EXCLUDE its nominated topics and replay its motivating incident the day after the merge            |
| Agent 7: Build & Test             | Runs build and test commands, reports failures                                                                                                                                                                                                                                                  |
| Agent 8: Diff-specialized finders | 0-2 extra finders written per-review when the diff concentrates in a domain with known failure modes (reconnect logic, module loaders, schedulers, codecs)                                                                                                                                      |
| `prose-exec`: Prose execution     | When the diff touches an instruction file (a `SKILL.md`, `AGENTS.md`/`CLAUDE.md`/`QWEN.md`, an agent or command definition, a `prompts/` file) and the review has a tree: EXECUTES the changed instructions in a disposable copy and files the gap between the run and what the prose promises  |

The three Correctness agents are **procedural**: each is defined by how it walks the diff (line-by-line / deleted lines / cross-file edges), not by a bug taxonomy — so their coverage is complementary instead of overlapping. Two further dedicated angles (1d/1e) split the language-pitfall checklist and wrapper/proxy routing out of the line-by-line walk: a checklist pattern-match and a structural routing expectation are different attention modes, and folded into the walk they were diluted by its rhythm. The same reasoning splits **code quality into three** (3a/3b/3c): one agent holding a six-item checklist finishes one item — measured on a heavily-rewritten file, one agent holding an eight-item checklist found 1 of 5 defects and the same model split three ways found all 5 — so the quality checklist is cut where the questions genuinely differ. All agents run in parallel (Agent 1 launches 3 procedural variants and 2 dedicated angles, Agent 3 launches 3 checklist slices, and Agent 6 launches 3 persona variants and the counter-frame audit 6d concurrently, totaling up to 17 parallel tasks for same-repo PR reviews — Agent 1e runs only when the diff signals a wrapping type — plus 0-2 Agent 8 finders when the diff's domain calls for them, so 16-19 in practice, and one more (`prose-exec`) when the diff touches an instruction file; Agent 0 and 6d are skipped for local-diff and file-path reviews, which run 14-17; cross-repo lightweight mode also skips Agents 1c and 7, running 14-17 with the PR identity and 12-15 without it).

Every finding must state a **failure scenario** — the concrete input, state, or timing that triggers it and the wrong outcome that results (for quality findings, the concrete cost instead). A finding that cannot name its scenario is dropped at the source, and verification re-traces the claimed scenario through the real code rather than judging the finding's prose.

Once a PR carries more than 500 lines of **source** change — or more than 3 200 diff lines in total, past which the sixteen whole-diff readers are each too diluted to read carefully (an attention bound, not a promise of fewer calls — heavy files and specialized finders can make 3B cost more) — this dimension fan-out is replaced by a **territory × dimension** fan-out: the diff is split into ~400-line chunks — boundaries fall on hunk boundaries, and a hunk too large to fit is split only at a top-level declaration, never inside a function — and each chunk gets its own agent that applies every review dimension to that chunk alone.

The gate deliberately counts source lines rather than diff lines. Test code, prose and lockfiles dominate diff size — across this repo's last 40 merged PRs the median diff is 41% tests — so a gate on raw size would carve a 173-line production change into territories just because it shipped 489 lines of new tests, leaving that production code with one reviewer instead of fifteen lenses (the diff-reading dimension agents — seventeen minus Issue Fidelity and Build & Test). Chunking still covers every line either way, tests included; what the gate decides is how many reviewers there are and what each is asked to do. Fifteen diff-reading lenses all walking one large diff read the same early hunks fifteen times over; one agent per chunk means every line of the diff has exactly one accountable reviewer. Each chunk agent returns a `Covered:` receipt, and a chunk with no receipt is re-reviewed before the run proceeds — so "no blockers" can never be reported over code that nobody read.

A **source** file that is largely rewritten (an existing file of 300+ lines that is now 40%+ new, or has 800+ changed lines) also gets **three whole-file invariant agents**. Test and generated files never qualify — the checklist asks about fields, timers, and error taxonomies, which a rewritten test file does not have. Its bugs are usually not inside any one hunk but _between_ the new lines — a timer armed near the top of the file and a teardown path two thousand lines below. Each agent reads the whole post-change file and walks two or three items of a fixed checklist: mutable fields cleared on every exit path, timers cancelled on every close (and cancellation not discarding captured data), map inserts matched by deletes, retry counters incremented at every entry, status return values actually checked, error codes exhaustively classified permanent vs transient, config fields honoured on every path, and early returns that skip a required side effect.

The checklist is split three ways on purpose. Handing one agent all eight checks over a 2 400-line file gets one of them done properly; three agents with two or three checks each get all of them done. Chunk agents do not substitute for this — on PR #6457 they held every one of these defects inside their assigned territory and reported none. What they lacked was not the lines but the question.

Findings are verified in **sharded batches** (at most 8 findings per verification agent, all launched together). A verifier may reject a Critical only by quoting the code that contradicts it (or when the diff's own comments document the flagged behavior as deliberate); anything less certain is downgraded to low confidence rather than deleted — a silently rejected Critical is invisible to every later stage, while a downgraded one still reaches a human. The bar applies to the shape of every rejection: it must be constructible from the code — quote the line the finding misreads, prove the claimed state impossible from a type, constant, or invariant, cite the in-diff guard that covers the trigger, or match a pure-style change with no observable effect — or otherwise match an exclusion criterion — and "too speculative" is never one of them. A finding whose failure scenario names a state the code does not exclude is plausible by default: a concurrency race, nil/undefined on a rare-but-reachable path, a falsy zero or empty collection treated as missing, an off-by-one on an unexcluded boundary, a retry storm or partial failure, a regex or allowlist that lost an anchor. A rejection that constructs none of the four grounds downgrades instead of dropping. After verification, **iterative reverse audit** hunts for gaps, fanned out one auditor per chunk per round, each with the cumulative finding list. The loop stops after **two consecutive dry rounds** (or at the plan's round cap — reported as such rather than as convergence). That cap follows the diff's topology: **10** on a small diff, where a round is a single auditor; **5** on a chunked one, where it is one auditor per chunk; and **3** on a huge diff (≥ 3000 effective lines) _when the run has an explicit deadline_ (CI's, or `--deadline` at capture), because five ~90-minute rounds do not fit a six-hour CI ceiling and a review killed mid-flight posts nothing — under the plan's default wall a huge diff keeps the chunked cap of 5. Capture prices the tier from the clock it sees and records it; a reader may only lower a recorded value inside its band (a plan captured under CI's epoch keeps 3 in a continuation resumed without that epoch; one captured with no clock is cut to 3 if the run later gains one), and a value outside the band falls back to the tier. An operator can lower whichever cap applies for every review with the `review.reverseAuditRounds` setting; it can never raise one. One dry round is not evidence of convergence, and reverse-audit findings are verified like any other.

## Severity Levels

| Severity         | Meaning                                                             | Posted as PR comment?      |
| ---------------- | ------------------------------------------------------------------- | -------------------------- |
| **Critical**     | Must fix before merging (bugs, security, data loss, build failures) | Yes (high-confidence only) |
| **Suggestion**   | Recommended improvement                                             | Yes (high-confidence only) |
| **Nice to have** | Optional optimization                                               | No (terminal only)         |

Low-confidence findings appear in a separate "Needs Human Review" section in the terminal and are never posted as PR comments.

## Worktree Isolation

When reviewing a PR, `/review` creates a temporary git worktree (`.qwen/tmp/review-pr-<number>`) instead of switching your current branch. This means:

- Your working tree, staged changes, and current branch are **never touched**
- Dependencies are installed in the worktree (`npm ci`, etc.) so build/test work
- Build and test commands run in isolation without polluting your local build cache
- If anything goes wrong, your environment is unaffected — just delete the worktree
- The worktree is automatically cleaned up after the review completes
- If a review is interrupted (Ctrl+C, crash), the next `/review` of the same PR automatically cleans up the stale worktree before starting fresh. If the interrupted session still leaves its lease behind — a hard kill that skips this, or a multi-prompt review interrupted during a later prompt — `/review` refuses and names the lease file to delete. Clean stops release it: a finished review and the early stops (empty diff, no new changes since the last review) all run `cleanup`, which releases the lease
- The worktree is leased to its session: a second `/review` of a PR that is already under review refuses to start (naming the holder) rather than tear down the running review's worktree
- Review reports and cache are saved to the main project directory (not the worktree)
- Steps that **modify** code to measure something — the test-efficacy probe's mutants, and a verifier's probe of a specific finding — each run in their own throwaway worktree beside it (`…-probe`, `…-scratch-<agent>`), so one agent's experiment is not visible to the others reading the shared tree. As a backstop, every agent in each wave is also told which paths (if any) differ from the commit under review at the moment it was launched, and that a failure confined to those paths is not a finding. All of these trees are swept along with the worktree at the end of the review.

## Cross-repo PR Review

You can review PRs from other repositories by passing the full URL:

```bash
/review https://github.com/other-org/other-repo/pull/456
```

This runs in **lightweight mode** — no worktree, no build/test. The review is based on the diff text only (fetched via GitHub API). PR comments can still be posted if you have write access.

| Capability                                                                    | Same-repo | Cross-repo                     |
| ----------------------------------------------------------------------------- | --------- | ------------------------------ |
| LLM review (Agents 0, 1a, 1b, 1d, 1e, 2-6 + verify + iterative reverse audit) | ✅        | ✅                             |
| Agent 1c: Cross-file tracer                                                   | ✅        | ❌ (no local codebase to grep) |
| Agent 7: Build & test                                                         | ✅        | ❌ (no local codebase)         |
| Agent 8: Diff-specialized finders (0-2, when the domain calls for it)         | ✅        | ✅ (needs only the diff)       |
| PR inline comments                                                            | ✅        | ✅ (if you have write access)  |
| Incremental review cache                                                      | ✅        | ❌                             |

## PR Inline Comments

Use `--comment` to post findings directly on the PR:

```bash
/review 123 --comment
```

Or, after running `/review 123`, type `post comments` to publish findings without re-running the review.

**What gets posted:**

- High-confidence Critical and Suggestion findings as inline comments on specific lines, each prefixed with `**[Critical]**` or `**[Suggestion]**` so blockers are distinguishable from recommendations
- Where the fix is a single localized edit, a ` ```suggestion ` block you can apply in one click
- For Approve/Request changes verdicts: a review summary with the verdict
- For Comment verdict with all inline comments posted: no separate summary (inline comments are sufficient)
- Model and CLI version attribution footer on each comment (e.g., _— qwen3-coder via Qwen Code /review (v0.21.2)_); set `review.attribution` to `false` in your user or system `settings.json` (the workspace `.qwen/settings.json` is ignored for `review.*` settings) to post without it — comments and body lists then also lose the `**[Critical]**`/`**[Suggestion]**` severity markers, and the model is withheld from the review's machine-ledger marker, so in fresh environments (no review cache) the recovered incremental anchor fails the same-model check and the re-review falls back to full-range

**What stays terminal-only:**

- Nice to have findings
- Low-confidence findings

**Self-authored PRs:** GitHub does not allow you to submit `APPROVE` or `REQUEST_CHANGES` reviews on your own pull request — both fail with HTTP 422. When `/review` detects that the PR author matches the current authenticated user, it automatically downgrades the API event to `COMMENT` regardless of verdict, so the submission still succeeds. The terminal still shows the honest verdict ("Approve" / "Request changes" / "Comment") — only the GitHub-side review event is neutralized. The actual findings still appear as inline comments on specific lines, so substantive feedback is unchanged.

**Re-reviewing a PR with prior Qwen Code comments:** when `/review` runs on a PR that already has previous Qwen Code review comments, it classifies them before posting new ones. Only **same-line overlap** (an existing comment on the same `(path, line)` as a new finding) prompts you to confirm — that's the case where you'd see a visual duplicate on the same code line. Comments from older commits, replied-to comments (treated as resolved), and comments that simply don't overlap with any new finding are silently skipped, with a terminal log line so you know what was filtered.

**CI / build status check before APPROVE:** if the verdict is "Approve", `/review` queries the PR's check-runs and commit statuses before submitting. If any check has failed (or all checks are still pending), the API event is automatically downgraded from `APPROVE` to `COMMENT`, with the review body explaining why. Rationale: the LLM review reads code statically and cannot see runtime test failures; approving while CI is red would be misleading. The inline findings are still posted unchanged. If you want to approve anyway (e.g., a known-flaky CI failure), submit the GitHub approval manually after verifying.

## Applying the Findings (`--fix`)

`--fix` is `--comment` reflected. `--comment` writes to a **pull request**, so it needs one; `--fix` writes to a **working tree**, so it needs one that outlives the review:

```bash
/review --fix                 # local uncommitted changes
/review src/auth.ts --fix     # a single file
```

On a **PR target it is ignored with a warning** — a PR review runs in an ephemeral worktree that is deleted when the review ends, so "fixed" edits there are discarded minutes later. Use `--comment` to publish the findings instead.

An effective `--fix` **floors the effort at medium**, because it edits your files and `low` runs no verification: applying an unverified finding is the same mistake as posting one, aimed at your working tree rather than someone's PR. It does not force `high` — medium's findings are verified, and the reverse audit `high` adds hunts for findings that are _missing_, which is not what deciding whether to apply one turns on.

After the review, each finding is applied with the `edit` tool and then **accounted for**, one of three ways:

| Outcome            | Meaning                                               | Stays on your plate? |
| ------------------ | ----------------------------------------------------- | -------------------- |
| `fixed`            | The edit is in your tree                              | No                   |
| `skipped`          | Real, not applied — the reason is reported alongside  | Yes                  |
| `no_change_needed` | The finding was wrong, or the code already handled it | No                   |

A finding is skipped when its fix would change intended behavior, would need changes well outside the reviewed diff, or turns out on a second look to be a false positive.

**Every finding gets an outcome, and this is enforced rather than requested.** The ledger goes through `qwen review findings --outcomes`, which refuses a set that does not cover all of them — a fixer that applies six of nine findings and reports six has not lied about any one of them, it has silently shortened the list, and you would have no way to see the three that fell off.

**The applied fix is then audited — by one agent, and not by re-reviewing your tree.** The review does not re-run itself over code it just edited (that would be a new review of different code, wearing this one's verdict). Instead it records your working tree before the first edit (`qwen review fix-delta --snapshot`, through a throwaway index — your own index and stash are never touched), diffs the tree against that record afterwards (`fix-delta --since`), and hands exactly those hunks, together with the `fixed` findings each claims to close, to a single `fix-audit` agent. That agent never sees the reviewed diff and files no findings. (It is not the narrower fix-audit-shaped round a critical-posture PR re-review runs, described below: that round reviews a pull request, and this agent runs only on local and file targets — after `--fix`, or after you type `fix these issues`.) It answers one question per hunk: what does this edit newly assume — a bound, a key, a lifetime, a shared resource, an ordering — and does anything in the tree pin it (a test that goes red, a type, a single source the value derives from)? Only the unpinned ones come back, each with what would pin it. They are reported in a **Fix audit** block in the terminal and as the affected finding's outcome note; they are not findings, change no verdict, and are left for you to act on — the measured reason this exists is that a third of a multi-round review's later findings were introduced by the fix immediately before them, and the two Criticals a real fix round shipped were exactly assumptions of this kind that a mutation probe of the intended fix sites could not reach (that round was a PR review whose author applied the posted comments — the path this audit does not run on; it runs where `--fix` is effective, on local and file targets). If nothing was `fixed` and nothing was applied, the audit is skipped and says so; if the ledger and the tree disagree — no `fixed` outcome beside hunks that landed, or a `fixed` outcome beside no hunk at all — you are told, because that is a bookkeeping problem rather than an audit question. A `fixed` finding none of whose locations any hunk touches is reported by the auditor as unattested rather than refused, since a fix can legitimately land entirely in files the finding does not name. The hunks have a fixed scope, printed beside the audit: the files HEAD tracks, and every other file no ignore rule hides. An edit inside a submodule or a nested repository, to a gitignored file HEAD does not track, or to any path in the review's `.qwen/tmp` name families (`qwen-review-*`, `review-pr-*`, `file-review-*`) is not in them; a hunk shows a file as git stores it (a binary file only as `Binary files … differ`, a Git LFS file as its pointer); an edit that changes the ignore rules brings the files they hid in as additions; and a commit made between the snapshot and the diff is reported — a committed edit is still in the hunks, but a gitignored file the commit started tracking is not.

## Resuming an interrupted review (`--resume`)

A long review that dies part-way — a dropped connection, a timeout, a killed terminal — leaves everything it had done on disk: the worktree, the captured diff, and the harness's own record of every agent that ran. `--resume` continues from there instead of starting over:

```bash
/review 123 --resume
```

It applies to **PR targets only** (a local review's diff comes from a live working tree, which has no stable interrupted state to continue), and it is safe to pass whenever you are unsure: the review rules on the on-disk state itself — the worktree still at the fetched commit and clean, the captured diff unchanged byte for byte, the PR head unmoved, the resume limit unspent — and silently starts fresh whenever anything no longer matches, telling you which check refused. A continuation reuses the earlier attempt's certified agent results, so the report says how many were recovered; it is disclosed, never a coverage gap.

Two things to know. With only the built-in target default, a continuation keeps the interrupted run's recorded **effort**. An explicit `--effort`, a project-remembered level, the operator `review.effort` setting, or an effective `--comment` supplies a required level; if it differs from the interrupted run, resume is refused and a fresh run starts at that level, because different effort is different work. And if the PR head moved while the review was down, the resume refuses (`head-moved`) and the fresh run reviews the new commits — which is what you want, and it counts as this review's one restart.

## Findings as Data

Confirmed findings are canonicalized into `.qwen/tmp/qwen-review-<target>-findings.json` before anything else consumes them — the terminal report, the saved Markdown report, and the PR review JSON all read that one artifact instead of re-typing the list. Each finding carries a unique `id` (what outcomes and resolved anchors join on), `severity`, `confidence`, `source`, `summary`, a `shortSummary` capped at 60 characters for list rendering, `failureScenario`, and one or more `locations` — a pattern-aggregated finding keeps **one location per occurrence**, so each still gets its own inline comment.

**Before anything else, the review checks that it is running your code.** Every `qwen review …` step runs the built bundle, not the working tree, so a review command edited since the last build takes no effect and the run measures the old behaviour. The build records a digest of the review sources it bundled; `parse-args` re-derives it and compares, and `drive` checks again, because the verifier brief sends agents straight there without a step 1. On a mismatch it says on stderr that the bundle was not built from these sources, and what to rebuild. The check runs when the CLI resolves to the bundled `dist/cli.js` (the `qwen` binary, or `node dist/cli.js`); launchers that run unbundled output, such as `npm start` and `npm run dev`, skip it. Two cases it cannot compare are treated differently: a checkout whose build predates the recording is told the check could not run and why, and an installed package — which has no sources to differ from — is left silent. The digest covers the review commands, the file that registers them, the review-only lease they import from outside their directory, and the bundled review skill; it does not follow those into the shared helpers they import, so a quiet run means the review code matches the bundle rather than that the whole tree does.

**A Critical the base tree already failed is held back, not filed.** When a test command failed and the merge base could be built, `test-delta` records which failing files also fail without the pull request. Canonicalization reads that measurement back (`qwen review findings --test-delta`, beside `--outcomes`): a Critical whose own text names one of those files is lowered to a Suggestion, keeps its evidence, gains the measurement that demoted it and a `heldByMeasurement` field, and the demotion is announced. A test that was already red is not a test this pull request turns red — and if it now fails for a _new_ reason, say which test, quote both sides, and file it at Critical again: a finding that already carries the measurement and is raised anyway is left where you put it.

The command validates on write: a duplicate id, a finding with no failure scenario, an empty locations array, or an unknown severity is an error rather than a silently mangled entry.

Where do the images come from? For terminal-rendering claims, `qwen review capture-tui` drives the code under review in a **private tmux server** (it cannot touch your own tmux sessions), captures the pane bytes as `.ans`, and renders a `.png` via `freeze` when installed — degrading explicitly (`png` → `ans-only` → refused) and recording which rung it reached, because a verifier must say whether its verdict stands on pixels, bytes, or prose.

## Evidence Images in PR Comments

GitHub's API cannot attach images to review comments, so `/review` can host evidence images (TUI screenshots, rendered-output comparisons) in a repository you designate and embed them by URL:

```bash
export QWEN_REVIEW_ASSETS_REPO=your-org/your-repo   # a repo you can push to
/review 123 --comment
```

Point it at a repository you can push to — a dedicated image-host repository is the recommended destination; a fork or scratch repo works too. Avoid the repository under review itself: image branches pushed there become reachable objects that every clone fetches. Images land on the `pr-assets/<pr>-review` branch with content-hashed names, and comments reference them by **commit-pinned** URL — immutable even if the branch later moves, and working unchanged on GitHub Enterprise.

For GitHub-triggered reviews (the PR-review workflow), the same variable is wired from a **repository variable** of the same name, and the workflow passes through only a dedicated external host: with the variable unset, or set to the repository under review (compared after trimming and case-normalization, so padded or case-shifted spellings of the same repository count as self-targeting), the workflow passes an empty value and publishing refuses — nothing changes, and the review keeps its evidence as prose and local artifact paths. A maintainer who sets `QWEN_REVIEW_ASSETS_REPO` in the repository's Actions variables to a separate image-host repository enables review comments to embed capture PNGs. An external destination manages its own retention; the visuals cleanup workflow only drains the historical `pr-assets/*` branches that former Git-backed publishers left in this repository.

The publishing is gated exactly like posting: no designated repo means no publish, and an unauthorized run (no effective `--comment`) is refused the same way `submit` refuses. Only image types are accepted (SVG is excluded deliberately), with size caps, and each file's bytes must match the format its extension claims — mislabeled or unrecognized content is refused. A manifest records every file pushed. Without a designation, findings keep their evidence as local file paths in the terminal and saved report — nothing breaks, comments just stay text-only.

## Follow-up Actions

After the review, context-aware tips appear as ghost text. Press Tab to accept:

| State after review               | Tip                | What happens                            |
| -------------------------------- | ------------------ | --------------------------------------- |
| Local review, `--fix` not passed | `fix these issues` | LLM interactively fixes each finding    |
| PR review with findings          | `post comments`    | Posts PR inline comments (no re-review) |
| PR review, zero findings         | `post comments`    | Approves the PR on GitHub (LGTM)        |
| Local review, all clear          | `commit`           | Commits your changes                    |

Note: `fix these issues` is only available for local reviews, for the same reason `--fix` is — for PR reviews the worktree is cleaned up after the review, so post-review interactive fixing is not possible; use `--comment` or `post comments` to publish findings instead. When `--fix` was passed, the findings already carry outcomes and no fix tip is offered.

## Project Review Rules

You can customize review criteria per project. `/review` reads rules from these files (in order):

1. `.qwen/review-rules.md` (Qwen Code native)
2. `.github/copilot-instructions.md` (preferred) or `copilot-instructions.md` (fallback — only one is loaded, not both)
3. `AGENTS.md` — `## Code Review` section
4. `QWEN.md` — `## Code Review` section

Rules are injected into the LLM review agents (0-6) as additional criteria. For PR reviews, rules are read from the **base branch** to prevent a malicious PR from injecting bypass rules.

## Repository Context

Repositories can hand the reviewers bounded, repository-specific guidance by committing a strict JSON manifest to `.qwen/review-context.json`. At medium or high effort, `/review` reads the manifest after capturing the plan and attaches the matching guidance before any agent launches:

```json
{
  "version": 1,
  "label": "Example repository",
  "rules": [
    {
      "paths": ["packages/*/src/**"],
      "domains": ["runtime"],
      "relatedPaths": ["packages/runtime/src/**"],
      "recommendedTests": ["npm run test:runtime"],
      "requiredConfigurations": ["debug"],
      "requiredAgents": ["test-matrix"],
      "unverifiedDimensions": ["Alternate runtime was not exercised"],
      "verificationNotes": ["Use the repository native test runner"]
    }
  ]
}
```

A rule applies when any changed file matches one of its `paths` globs (`*`, `?`, and `**` segments; case-sensitive). All matching rules merge their guidance: domains and related files for the review agents, recommended tests and required configurations for the build-and-test agent, extra reviewer roles (honoured only when the chosen effort and topology run them), and proof boundaries the final review discloses as unverified dimensions. Arrays may be written in any order; duplicate entries are rejected.

For PR reviews the manifest is read from the merge base, so the PR under review cannot opt itself into or out of guidance; local reviews read it from the current worktree. Low-effort and cross-repository reviews skip repository context. The full contract and trust model live in the [design doc](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/review-repository-context.md>).

## Issue Fidelity

For bugfix PRs, the Issue Fidelity agent fetches issue evidence directly instead of relying on PR description text. It runs the `qwen review issue-context <pr> --repo <owner/repo> --out <file>` subcommand, which resolves GitHub's strong closing-issue metadata and then fetches each referenced issue's title, **body** (the reporter's original repro), and full comment thread — each from the issue's own repository (a PR can close an issue in a different repo). This agent runs only for PR targets; local-diff and file-path reviews skip it.

The closing-issue set is a discovery hint rather than proof the author linked the right issue: if it is empty but the PR references an apparent target issue, the agent still fetches it after judging relevance (re-running with `--issue <n>`; a bare number resolves in the PR's repo, while `--issue <owner>/<repo>#<n>` fetches a cross-repo reference from its own repo). Fetched issue text is treated as untrusted data (facts extracted, embedded instructions ignored). For relevant issues, the original reproduction, observed payload, expected behavior, and maintainer comments are treated as the highest-priority evidence for whether the PR fixes the right problem.

If the issue evidence shows an upstream service or provider returned malformed data outside the client contract, client-side parser or sanitizer changes are not treated as a valid root-cause fix unless a maintainer explicitly requested a defensive workaround. A test that replays malformed upstream output proves only that the workaround handles that shape; it does not prove the workaround is architecturally appropriate.

Example `.qwen/review-rules.md`:

```markdown
# Review Rules

- All API endpoints must validate authentication
- Database queries must use parameterized statements
- React components must not use inline styles
- Error messages must not expose internal paths
```

## Incremental Review

When reviewing a PR that was previously reviewed, `/review` only examines changes since the last review:

```bash
# First review — full review, cache created
/review 123

# PR updated with new commits — only new changes reviewed
/review 123
```

### Cross-model review

If you switch models (via `/model`) and re-review the same PR, `/review` detects the model change and runs a full review instead of skipping:

```bash
# Review with model A
/review 123

# Switch model
/model

# Review again — full review with model B (not skipped)
/review 123
# → "Previous review used qwen3-coder. Running full review with gpt-4o for a second opinion."
```

The model match also gates incremental scoping, not just the skip: "clean up to the cached commit" is the previous model's verdict, so when new commits have landed since the cached review, a model mismatch never scopes to `lastCommitSha..HEAD` — the range is the full diff, noting "Previous round was reviewed by qwen3-coder. Running full review with gpt-4o." — unless an anchor certified by the model now running is recovered from the last posted review (below), which scopes the range instead. The previous round's findings still carry over to be re-ruled; only the anchor does not. The same gate binds the anchor recovered from the last posted review's machine-ledger marker when the cache is absent or its anchor is unusable (CI, another clone): it scopes the incremental range only if the model now running certified it — a marker certified by a different model, or carrying no model (a review posted with `review.attribution` off, or one from before the field), falls back to the full diff. A round that did not close cleanly posts its marker without an anchor (it cannot certify a range), but that loss is not sticky when the round's work list survived whole: recovery grafts the anchor forward from the most recent earlier marker your own account posted with one, so a single non-clean round no longer forces every later round to re-read the full diff — the next round scopes `anchor..HEAD`, which re-covers the range the non-clean round could not certify. A size-capped round's graft is refused (dropped findings would fall outside the grafted scope and retire silently), so later rounds keep re-reading the full diff until a complete marker lands.

**Critical-posture re-reviews run a narrower round (fix-audit shape).** Once a PR's re-review resolves the round-adaptive posting floor to critical-only (from round 6, or earlier when the flat first-time-finding trend latches — see the convergence posture) _and_ a usable incremental anchor exists, the round stops re-running round 1's shape. The capture command predicts the floor from the previous round's posted marker and records `incremental.posture: "critical"` in the plan, and the round then: fans out one territory agent per chunk of the delta (whatever its size — no 3A dimension set, but keeping the issue-fidelity agent: it re-checks head against the issue whatever the delta displays, and is the one auditor that can see a fix commit removing behaviour the issue required when the removal appears on neither side of the narrowed range); re-enters each still-clean _interaction_ file with its own full-range hunks, exactly as any other incremental round does; and narrows the reverse-audit waves instead of capping them — from wave 3, a chunk holding no delta file leaves the schedule after one substantive dry audit (a launch every member of which the record certifies dry — rounds 1 and 2 are launched together and count as one launch, so a dry receipt beside a yielding or uncertified partner or sibling is not one), while delta territories keep the full retirement rules and any non-delta chunk the waves could not certify dry falls back to those same rules (so does one whose dry receipt was built on the very same findings list as an earlier round with a yielding or uncertified member, was built before an earlier return that was not dry came back, or ran in a different session from an earlier return that was not dry or beside one no session stamped, as the resumed half of an interrupted review does), so the late waves (where fix-induced Criticals measurably surface) keep running over a shrinking front. Severities are unchanged — the posture governs what posts, never what is found — and every reduction is disclosed: in the plan, in the round's `posture narrowing:` note (which names every chunk the narrowing costs, and the rules that keep one in the wave), and as a one-sentence "Round shape" disclosure in the posted review body. The plan's posture record is itself an arm of the compose-time floor resolution, so a fix-audit round's posting bar cannot disagree with the shape it ran (sub-Critical findings defer even if the floor's usual inputs are unavailable at compose time). An explicit `--severity-floor suggestion` turns the shape off along with the posture (and outranks a stale plan record at posting time); an explicit `--severity-floor critical` enables it from the first anchored re-review.

Cache is stored in `.qwen/review-cache/` and tracks both the commit SHA and model ID. Make sure this directory is in your `.gitignore` (a broader rule like `.qwen/*` also works). On GitHub, if the cached commit was rebased or force-pushed away, it falls back to a full review; Aone rules the cached anchor differently — see its paragraph below. Only high-effort reviews consult or write the cache — a `--effort low|medium` quick pass never counts as "already reviewed".

## Review Reports

For same-repo reviews, results are saved as a Markdown file in your project's `.qwen/reviews/` directory (cross-repo lightweight reviews skip report persistence):

```
.qwen/reviews/2026-04-06-143022-pr-123.md
.qwen/reviews/2026-04-06-150510-local.md
```

Reports include: timestamp, diff stats, build/test results, all findings with verification status, and the verdict. Section headings and descriptive prose follow the output language preference; technical identifiers (SHAs, file paths, gate names, finding ids) stay verbatim.

Medium- and high-effort reviews also save a structured JSON companion with the same stem (for example, `2026-04-06-143022-pr-123.json`) holding the canonical findings and the composed verdict as data. Qwen Code's Web Shell renders that document as an interactive review view with filterable findings; the Markdown report stays the human-readable archive.

The deterministic halves of the pipeline — argument parsing (`qwen review parse-args`) and the event/body decision (`qwen review compose-review`) — are tested subcommands rather than prompt text, so `--effort` grammar, `--comment` forcing, verdict caps, and downgrade behavior are pinned by unit tests and cannot drift with the model.

**GitHub Enterprise:** reviewing a PR URL on a non-`github.com` host routes every GitHub call at that host — the review subcommands (`match-remote`, `meta`, `fetch-pr`, `pr-context`, `comment-status`, `issue-context`, `fetch-diff`, `comment-body`, `plan-diff`, `test-plan`, `presubmit`, `compose-review`, `submit`, `publish-assets`) accept `--host` and set it in code, so a forgotten host cannot silently retarget the review at `github.com`.

**Aone Code:** for a clone whose origin is on `gitlab.alibaba-inc.com`, run `/review` from inside that clone — the platform is detected from the remote and the subcommands work, backed by the `a1` CLI (at least 0.1.90 — an older install is refused at authentication time with an upgrade message) — the target number is the global MR id. `fetch-pr` fetches `refs/merge-requests/<id>/head` and builds the worktree + diff, so the agent review of the worktree is unchanged, and `test-plan` works too — it reads the MR description through the same reader. `pr-context` is backed too: it reads the MR's metadata, discussion threads, and previously posted qwen summaries (the machine ledger recovers from them), so an Aone run sees the MR's existing discussion exactly as a GitHub run sees a PR's. `comment-status` and `presubmit` are a1-backed too (presubmit fully: self-PR detection, head drift, merge-gate CI, and existing-comment dedup), so repeat `--comment` rounds dedup against the MR's existing comments instead of re-posting them (a thread the platform marks outdated — its line no longer maps after an amend — stays re-postable), and self-PR detection works too. The `publish-assets` write is skipped. `--comment` **posts** the review through the `a1` CLI: one comment per inline finding, then the summary comment. Aone has no native request-changes state — on that verdict the summary comment carries a blocking header, and any inline Criticals that were actually posted block the merge through the discussion gate while their discussions stay unresolved (when no inline Critical posted, the header is advisory and nothing mechanically blocks the merge). The posted comments carry no AI-comment flag — `a1` cannot set one — so a repo's dedicated `ai_comment` merge gate does not track them. The native `a1 repo mr approve` fires for an Approve verdict when the run read the MR's context (the same gate as GitHub; a context-unavailable run stays capped at Comment). Incremental re-review follows the AGit-Flow update model: an update AMENDS the single CR commit in place, orphaning the head the previous round reviewed — so the cached anchor is ruled WITHOUT ancestry (the anchor-behind-head test would fail for every update), and the re-review scopes the PR's own diff to the files the update touched instead of falling back to a full review; an update that also rebased onto newer master keeps that scope only while the rebase's drift stays within the CR's files — drift touching any other file falls back to the full review, and no drift byte enters the published scope either way. See `docs/design/2026-08-15-review-aone-provider.md`.

Every run ends with one machine-readable line (`Review complete: <target> — <disposition>`), so scripts and CI wrappers can detect completion and outcome with a single `^Review complete: ` match.

## Headless runs (`qwen review run`)

`/review` is interactive. When a script or CI job needs to run a review and act on its outcome, use the headless wrapper:

```bash
qwen review run [target] [--json] [--fail-on request-changes] [--comment] [--resume] [--quiet]
```

`target` is a PR number, a PR URL, or a file path; omit it to review the local working tree. The command runs this build's own CLI non-interactively (with stdin closed, so slash-command detection survives), streams the child's progress to **stderr**, and prints the verdict to **stdout** — or, with `--json`, the full result object. The verdict is read from the artifact `compose-review` writes (the same JSON the skill treats as the verdict authority), never parsed from the model's prose.

The exit code is the contract a gate should read:

| Exit | Meaning                                                                                           |
| ---- | ------------------------------------------------------------------------------------------------- |
| `0`  | The review completed (whatever it decided)                                                        |
| `1`  | It never reached a verdict — the child failed, timed out, or left no composed artifact            |
| `3`  | It completed with `REQUEST_CHANGES` **and** `--fail-on request-changes` was set (opt-in blocking) |

`3` (not `2`) lets a gate distinguish "the review is blocking" from "the tool broke" — yargs already uses `1` for usage errors — without parsing any output. `--timeout-minutes` (default 120, floored at 1) terminates a hung review and exits `1`, and cancelling the command (Ctrl+C / SIGTERM) terminates the review's process group rather than orphaning it.

`--resume` continues an interrupted review of the same PR instead of starting over — when a long local run dies part-way (a dropped connection, a timeout, a killed terminal), the retry would otherwise re-fetch, re-chunk and re-launch agents whose work is already on disk. It is safe to pass unconditionally on a retry: `fetch-pr` rules on the on-disk state itself (worktree still at the fetched SHA and clean, diff bytes unchanged, PR head unmoved, resume cap unspent) and silently falls back to a fresh review whenever anything no longer matches, so the flag never fails a run that could start over. When the current invocation has only the built-in target default, a continuation stays pinned to the interrupted run's recorded effort. An explicit `--effort`, a project-remembered level, the operator `review.effort` setting, or an effective `--comment` supplies a required level; a mismatch refuses the resume and runs fresh at that level. PR targets only (a local review's diff is captured from a live working tree, which has no stable interrupted state to continue). Resume is a **local convenience**: the repository's own CI review workflow does **not** resume — each retry re-runs fresh, because a CI attempt runs no-sandbox and its worktree is deleted on exit, leaving no interrupted state to continue.

A time-budgeted run can also export a **soft** deadline so the review stops its open-ended reverse-audit loop while there is still time to verify, compose and post: `QWEN_REVIEW_DEADLINE_EPOCH` is the Unix-seconds moment the run will be killed, and `QWEN_REVIEW_DEADLINE_RESERVE_SECONDS` (default 4800; `0` keeps only the round estimate) is the tail that must remain for the last round's verification, `compose-review` and submission. When the remaining budget no longer fits another round plus that tail, the round builder refuses to build it, and the composed verdict discloses the truncated audit (an otherwise-Approve verdict is capped at Comment). Without that variable the review uses the wall its plan recorded at capture: `--deadline <minutes>` on `qwen review fetch-pr` / `capture-local` / `plan-diff`, or by default **8 hours** on a small (Step 3A) diff, **12** on a large (3B) one and **16** on a huge one — sized by the diff's own lines, so a critical-posture fix-audit round keeps the wall its delta's size earns even though it fans out per territory and takes the chunked round cap. That default is a liveness bound — sized above what a healthy run at the round cap spends — so it ends a loop that has stopped converging and leaves a run that keeps working untouched; it is charged in elapsed time, idle included, so a session left overnight spends it, and because the next round is priced from the previous round's admission, a multi-hour pause between rounds can stop a run with time still on the wall (a `--resume` from a new session restarts both the wall and the pricing; **Review Deadline** below gives the ceilings). It stops the loop at the next round admission or verify build; it does not interrupt a build already running. A plan-recorded wall keeps a third of itself (capped at 4800 seconds, floored at the compose floor) as the tail reserve unless the reserve variable is set; a `--deadline` that cannot hold a convergence (two rounds plus that reserve — at or under ninety minutes, or more when the shell's reserve or compose-floor overrides raise the bar; that second leg is skipped while the environment exports a well-formed epoch, since the flag is inert then, and the wall it records is priced again in whatever shell a later env-less `--resume` runs in) is refused up front, and a wall just above it can still stop before round 1 once the fan-out and verification have spent it. When the wall can no longer admit a rebuild the coverage check would otherwise prescribe (a reverse-audit round, or a verifier shard under the compose floor), `compose-review` withholds that `FIX:` line and prints a `NOTE:` line saying so, with the gate's arithmetic; the gap is still disclosed in the posted body. The plan wall bounds the round builder and the verifier; two readers stay on the environment alone — the generated review workflow's launch allowance, and `qwen review run`'s own `--timeout-minutes` kill, which is not exported as a deadline. The plan stores the wall as a duration from the attempt's start, so a `--resume` from a new session renews it (a same-session resume continues the attempt and its wall; the flag itself is ignored on a resumed run — only its grammar and the default rule are checked — and the run says what wall the plan holds and how much of it is left, or that it has run out, and which builds the gates will now refuse; a wall can only run out on a same-session resume, or when the ledger failed to record the attempt, so the cheapest way past it is to resume from a new session, which renews the wall and keeps the worktree; a fresh review without `--resume` records a new wall too, and `--deadline none` on that fresh capture records none). `--deadline none` records no wall and leaves the loop bounded by the round cap alone — unless the environment exports an epoch, which clocks the run regardless. The `/review` slash command does not take the flag: it warns and drops it together with a value that could be one (whole minutes, or `none` — so a PR number typed right after it is dropped too), while any other value after it is disposed of like an invalid `--effort` value (a PR URL becomes the target; a typo is reported, and survives only as a sole file-shaped token), and a skill-driven run always uses the default. The huge tier's round reduction (3 rounds instead of 5) keys on an explicit deadline only — the environment's or the flag's — never on the default. A malformed environment deadline falls through to the plan's wall; a malformed plan field leaves the review ungated — the outer timeout still bounds the run.

Nested inside that reserve is a smaller **compose floor**, `QWEN_REVIEW_DEADLINE_COMPOSE_FLOOR_SECONDS` (default 1200; `0` disables this gate entirely, at every point including past the deadline). The reserve is one number covering "verify the last round **plus** compose **plus** submit", which fits a normal per-finding re-trace but not a security review whose verification re-runs real filesystem/git workloads without bound. So the verifier — not the round builder — is gated on this floor: once the floor or less remains, `agent-prompt --role verify` refuses to build (a `VERIFY BUDGET:` line, exit **4**), the findings in hand keep their unverified tag (which caps the verdict), and `compose-review` and submission run. Under the plan's own rule the floor never exceeds the reserve (a plan-recorded wall's reserve can sit exactly on it), and the round gate prices the round on top of the reserve, so a healthy run hits the reverse-audit gate first and never reaches it (an explicit `QWEN_REVIEW_DEADLINE_RESERVE_SECONDS` below the floor — `0` is the documented escape hatch — is the operator's choice, and then the verifier's gate can fire first); it is the cover for the one span the reserve cannot bound.

## Cross-file Impact Analysis

A dedicated cross-file tracer (Agent 1c) owns this walk end-to-end. When code changes modify exported functions, classes, or interfaces, it searches for all callers and checks compatibility:

- Parameter count/type changes
- Return type changes
- Removed or renamed public methods
- Breaking API changes

It also walks the **producer direction**: every field, option, or optional parameter the diff adds is traced to its read sites — including files the diff never touches. A live code path reading a field that nothing populates means the feature it gates silently does nothing, and that is flagged as Critical at the read site.

For large diffs (>10 modified symbols), the caller-direction analysis prioritizes functions with signature changes; the producer direction is never budget-limited, because an unchanged signature is exactly its point.

## Review Budget

The parts of the pipeline that are elastic in diff size are scaled from it, and the scaling is written into the diff plan so every stage reads one number rather than each deciding for itself:

| Budget field     | What it scopes                                   | How it scales                                                      |
| ---------------- | ------------------------------------------------ | ------------------------------------------------------------------ |
| `inlineAngles`   | How many `low` angles run (Step 3C)              | 3, plus one per 60 source lines, capped at the 6 angles that exist |
| `candidateFloor` | When `low` owes one deterministic recall re-pass | `min(changed files, 4)`                                            |
| `sweep`          | Whether `low`'s gap sweep runs                   | Off below 25 source lines                                          |
| `specialistCap`  | The Agent 8 ceiling                              | 0 below 80 source lines, otherwise 2                               |
| `verifyShard`    | Findings per verification agent                  | Flat at 8 — a property of the verifier, not of the diff            |

Two things it deliberately does not do. It **never scales a dimension away**: which agents a review owes is decided by the roster, which reads the effort level, so a small diff still gets its security pass and its test-coverage pass. And it reads **source** lines, not diff lines — a 40-line production change shipping 900 lines of new tests is a small change, and the same reasoning already governs the territory-fan-out gate.

Why the floors are where they are: on a nine-line typo fix, six inline walks are five walks over nothing, and the sweep — a fresh reader hunting what the first pass did not get to — has nothing to hunt when the first pass got to all of it. When a low-effort pass stays below `candidateFloor`, it takes one deterministic second look at every coverable hunk in the largest changed source file (falling back to the largest coverable file of any kind) and every coverable removed block. Files touching an uncoverable chunk are excluded from the target selection, and the pass always emits a receipt naming its target, new-candidate count, and any uncoverable chunks; when no file is coverable, it still checks the coverable removed blocks and discloses the missing target. The floor is a stopping signal, not a quota: a clean diff can still report no findings after that re-pass. Agent 8's floor is the substantive one: "one domain dominates the diff" is a judgement, and a judgement made about forty lines finds a dominant domain every time, because forty lines are usually all one thing.

## Review Deadline

Every review records a wall clock in its plan unless it was captured with `--deadline none` — `--deadline <minutes>` on `qwen review fetch-pr`, `capture-local` or `plan-diff`, otherwise **8 hours** on a small diff, **12** on a large one and **16** on a huge one — and a `QWEN_REVIEW_DEADLINE_EPOCH` exported by CI replaces it. At `high` effort the wall ends the reverse-audit loop at the first round it can no longer admit, and at `high` and `medium` effort it stops the verifier once no more than the compose floor remains; it never interrupts an agent that is already running. A review stopped this way composes its verdict with the stop disclosed and the verdict capped, verifies the last round's findings only while more than the compose floor remains, and reports what it proved like any other review. Running `/review` again does not continue the stopped audit: at `high` effort a PR review follows the Incremental Review rules above and may report nothing new on an unchanged head, and a local review with its cache may stop the same way on an unchanged working tree; at `medium` effort the review simply runs again.

The wall is elapsed time, pauses included, and a pause is charged twice: it spends the wall, and while it is the longest gap between two round admissions it is also the price of the next round. The pause that matters in practice falls after the first audit pair — rounds 1 and 2, which launch together — and before round 3. Under the default reserve, a review whose pair launched the moment its capture finished can pause for at most:

| Diff            | Default wall | Reserve | Longest pause after the first audit pair |
| --------------- | ------------ | ------- | ---------------------------------------- |
| Small (Step 3A) | 8 h          | 80 min  | 3 h 20 min                               |
| Large (Step 3B) | 12 h         | 80 min  | 5 h 20 min                               |
| Huge            | 16 h         | 80 min  | 7 h 20 min                               |

Those are ceilings, and the same rule covers any wall of 4 hours or more under the default reserve: at most (wall − 80 min) / 2 after a pair launched at capture. The pause is counted from the pair's launch, so it includes the pair's own audits and the verifiers that ride with it, and every minute the review spent before the pair launched takes half a minute off. Each later gap is held to at most half of what was left above the reserve at the previous admission, and can be held to less, for instance once an earlier gap was longer than that half. The precedence rules, the environment variables that set the wall and the reserve, and the up-front refusal of a too-short `--deadline` are described under Headless runs above. The `/review` slash command takes no `--deadline`, so a review started from a session gets a different wall only through those environment variables, and they apply to every review the session runs: `QWEN_REVIEW_DEADLINE_EPOCH` is one absolute moment shared by all of them, and like an explicit `--deadline` it cuts a huge diff's round cap from 5 to 3. A `--resume` from a new session restarts the wall and the pricing for an interrupted PR review.

## Token Efficiency

The high-effort pipeline bounds each stage (shard size, audit rounds), but total calls scale with findings — `ceil(F/8)` verification shards — and, under 3B, with chunk count (reverse audit runs per chunk per round). Typical 3A profile:

| Stage                            | LLM calls                       | Notes                                                                                                                                                                                                                                                                                                                                                                          |
| -------------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Review agents (Step 3)           | 17 (+0-2)                       | Run in parallel; Agent 1e only when the diff signals a wrapping type (16 without it); cross-repo skips Agents 1c and 7 (15 while the plan carries the PR identity, 13 without it — Agent 0 and 6d drop together); local/file skips Agent 0 and the counter-frame audit 6d (15); one more (`prose-exec`) on a diff that touches an instruction file, when the review has a tree |
| Sharded verification (Step 4)    | ceil(F/8)                       | F = findings; at most 8 per verification agent, launched together                                                                                                                                                                                                                                                                                                              |
| Iterative reverse audit (Step 5) | 2-10 (3A); rounds × chunks (3B) | Two consecutive dry rounds to stop; the cap follows the topology — 10 on a small diff, 5 on a chunked one, 3 on a huge one under an explicit deadline. 3B fans out one auditor per chunk per round                                                                                                                                                                             |
| **Total**                        | **~20-31 (~16-29)**             | 3A same-repo: ~20-31 (typical ~20-22); cross-repo or local/file: ~16-29 (the floor is the identity-less cross-repo roster of 13); one fewer when Agent 1e is not rostered, one more when `prose-exec` is owed; 3B scales with chunks (see DESIGN.md)                                                                                                                           |

Most PRs converge to the lower end of the range; the caps prevent runaway cost on pathological cases. At `--effort low` the review runs entirely inline — **0 subagent calls** — walking the diff once per angle instead of once in total.

## What's NOT Flagged

The review intentionally excludes:

- Pre-existing issues in unchanged code (focus on the diff only)
- Style or formatting a formatter would auto-normalize, or naming matching your codebase conventions — but NOT substantive issues a linter or type checker would flag (unused variables, unreachable code, type errors), which are in scope
- Subjective "consider doing X" suggestions without a real problem
- Minor refactoring that doesn't fix a bug or risk
- Missing documentation unless the logic is genuinely confusing
- Issues already discussed in existing PR comments (avoids duplicating human feedback)

## Design Philosophy

> **Silence is better than noise.** Every comment should be worth the reader's time.

- If unsure whether something is a problem → don't report it
- Every finding names a concrete failure scenario (trigger → wrong outcome) or a concrete cost — a finding that can't is dropped before it reaches you
- Same pattern across N files → aggregated into one finding
- PR comments are high-confidence only (and only from high-effort, verified reviews)
- Cosmetic style/formatting matching codebase conventions is excluded


</details>
