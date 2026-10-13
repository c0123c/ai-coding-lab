# OpenSpec Store 跨仓库规划指南（Beta）

介绍将规划放在独立仓库的 Store 结构、创建与注册、跨组件仓库需求，以及 references、working context 和 worksets 的协作方式。

- 来源：OpenSpec
- 资料类型：Beta 功能使用指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/stores-beta/user-guide.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/stores-beta/user-guide.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 商店：在自己的仓库中计划

> **测试版。**存储、引用、工作上下文和工作集是
> 新的。命令名称、标志、文件格式和 JSON 输出可能仍会在版本之间改变 
>  形状。下面的每个演练都是针对 
>  当前版本运行的，但升级后请重新阅读本指南。

## 这个解决的问题

OpenSpec 通常位于一个代码存储库中：
 代码旁边的 `openspec/` 文件夹，保存该存储库的规格和更改。

当你的计划大于一个存储库时，这就不再合适了：

- 您的工作跨越多个存储库 - 一项功能涉及 API 服务器，
Web 应用程序和共享库。计划
 位于谁的`openspec/` 文件夹中？
- 您的团队在代码存在之前就进行计划，或者计划永远不会发生的事情
*这个*存储库中的代码。
- 需求由一个团队拥有并由其他团队使用。维基百科
版本漂移，并且您的编码 agent 无论如何都无法读取它。

**商店**就是答案：一个独立的存储库，其整个工作就是规划。
它具有与您已经知道的相同的`openspec/`形状 - 规格和更改 - 
加上一个小身份文件。您可以通过名称 
 在计算机上注册一次，然后每个普通的 OpenSpec 命令都可以从任何地方在其中运行。

## 形状

```
            team-plans  (a store: planning in its own repo)
            ├── .openspec-store/store.yaml     identity: "I am team-plans"
            └── openspec/
                ├── specs/      what is true
                └── changes/    what is in motion
                      ▲
                      │ registered on each machine by name;
                      │ shared by pushing/cloning like any repo
        ┌─────────────┼─────────────┐
        │             │             │
    web-app       api-server     mobile-app
   (code repo)   (code repo)    (code repo)
```

有两条规则使这个简单：

1. **商店只是一个 git 存储库。**你提交、推送、拉取并审查它
你自己。 OpenSpec 绝不会自行克隆、同步或推送任何内容。
2. **声明，而不是机械。**存储库可以*声明*它们如何与
商店（如下所示）。声明会改变 OpenSpec 可以告诉你的内容 - 
 永远不会改变你的命令的作用。

## 五分钟即可到达您的第一家商店

两个命令可以让您从无到有，进行有效的、商店范围内的更改：

```bash
openspec store setup team-plans --path ~/openspec/team-plans
```

```
Store ready: team-plans
Location: /Users/you/openspec/team-plans
OpenSpec root: ready
Registry: registered

Next: run normal OpenSpec commands against this store, for example:
  openspec new change <change-id> --store team-plans
Share this store by committing and pushing it like any Git repo.
```

```bash
openspec new change add-login --store team-plans
```

```
Using OpenSpec root: team-plans (/Users/you/openspec/team-plans)
Created change 'add-login' at /Users/you/openspec/team-plans/openspec/changes/add-login/
Schema: spec-driven
Next: openspec status --change add-login --store team-plans
```

这就是整个模型。从这里开始，生命周期正是您所知道的 - 
`status`、`instructions`、`validate`、`archive` - 每个命令上都有 `--store team-plans`
，并且每个打印的提示都带有您的标志。 
`Using OpenSpec root:` 行始终告诉您命令正在执行的位置。

## 故事：一个团队，一个计划仓库

团队将其规范和更改保留在 `team-plans` 中，而不是在代码存储库中分散 
。

**第一天（无论谁设置）：**

```bash
openspec store setup team-plans --path ~/openspec/team-plans \
  --remote git@github.com:acme/team-plans.git
git -C ~/openspec/team-plans push -u origin main
```

在初始提交中，传递 `--remote` 将克隆 URL 记录在商店自己的身份 
 文件 (`.openspec-store/store.yaml`) 中。每个未来的
克隆诞生时都知道它来自哪里，因此健康检查和错误
消息可以为还没有
的队友打印完整的、可粘贴的修复程序。

**每个队友（每台机器一次）：**

```bash
git clone git@github.com:acme/team-plans.git ~/openspec/team-plans
openspec store register ~/openspec/team-plans
```

从那时起，每个人都按名字在同一个计划存储库中工作：

```bash
openspec status --store team-plans --change add-login
openspec show add-login --store team-plans
```

**共享工作是 git，有意的。**您创建的更改仅存在于
您的结帐中，直到您提交并推送它 - 与代码相同。计划免费获得
分支、拉取请求和审查，因为商店是一个
普通存储库。

**连接团队的代码仓库。**一个规划完全
外部化的代码仓库只需要一行，在`openspec/config.yaml`中：

```yaml
# web-app/openspec/config.yaml
store: team-plans
```

现在，在 `web-app` 内运行的每个 OpenSpec 命令都会作用于 `team-plans`，而 
 根本没有标志：

```bash
cd ~/src/web-app
openspec status --change add-login
```

```
Using OpenSpec root: team-plans (/Users/you/openspec/team-plans)
...
```

指针是后备，而不是覆盖：显式的 `--store` 总是 
 获胜，如果存储库自己增长了真正的计划文件夹，则这些文件夹获胜 
（带有删除过时指针的警告）。

**计算机上的每个存储库都有一个默认值。**如果您工作的许多
代码存储库都计划放入同一存储库，请将其全局设置一次，
而不是将`store:`行添加到每个存储库：

```bash
openspec config set defaultStore team-plans
```

现在，任何在计划根之外运行的命令（并且没有 `--store` 和 
 项目指针）都会解析为 `team-plans`。它位于 
 优先级列表的底部，因此 `--store`、本地根和项目 `store:` 指针 
 仍然获胜。根横幅和 JSON `root` 块使用商店 id 报告 
`source: "global_default"`，因此您始终可以从存储库自己的指针中得知 
 计算机范围的默认值。使用
`openspec config unset defaultStore`清除它。如果 id 未注册，则命令 
error 并告诉您注册它或清除过时的默认值。

## 示例：一个功能，两个组件存储库

假设`add-checkout-promo`同时更改`checkout-api`和
`checkout-web`。团队想要一份共享的产品合同，而每个代码
存储库仍然需要自己的实现任务、分支和审查。

使用两层：

1. 将共享行为保留在`team-plans`中。
2. 在每个组件存储库中保留实施计划并引用商店
作为只读上游上下文。

首先规划商店中的共享合约：

```bash
openspec new change add-checkout-promo --store team-plans
openspec status --change add-checkout-promo --store team-plans
```

提案和规范应描述 
 组件之间边界的行为 - 例如，服务 
 返回的促销字段以及前端如何处理不合格的结账。像任何其他分支和拉取请求一样，在 
 商店存储库中查看此更改。

### 规划的背景是什么？

选择商店会更改 OpenSpec 根；它不会发现或读取
使用该存储的每个代码存储库。存储指令查看存储中的工件
和配置的上下文。仅当这些 
 文件夹也可用于 agent 或编辑器并且 agent 读取它们时，他们才能看到组件代码。

工作集是打开规划存储和两个代码存储库
的便捷方法：

```bash
openspec workset create checkout-promo \
  --member ~/openspec/team-plans \
  --member ~/src/checkout-api \
  --member ~/src/checkout-web \
  --tool code
openspec workset open checkout-promo
```

这使得文件夹在一个 IDE 工作区中可见。它不会将源
上下文复制到商店中，选择受影响的存储库，或授予agent权限
编辑它们。将持久的跨组件事实放入共享规范中；不要
依赖规划者记住它碰巧检查过的来源。

### 每个存储库如何开始实施？

当没有显式的 `--store` 或更接近的 `openspec/` 根适用时，
`store: team-plans` 指针将命令路由到该存储。它不会按调用 `apply` 的目录拆分 
 一个存储任务列表。 OpenSpec
目前不将任务路由到存储库。

当每个组件需要一个独立范围的 apply/review 循环时，给它 
 一个本地 OpenSpec 根并引用中央存储，而不是指向 
：

```yaml
# checkout-api/openspec/config.yaml (and likewise in checkout-web)
schema: spec-driven
references:
  - team-plans
```

共享合同获得批准并在商店的主要 
 规格中可用后，为组件的部分创建一个小的本地更改：

```bash
cd ~/src/checkout-api
openspec new change implement-checkout-promo-api

cd ~/src/checkout-web
openspec new change implement-checkout-promo-ui
```

每个存储库指令中的参考索引提供商店规范的 
 摘要和准确的 `openspec show ... --store team-plans` 获取命令。每个
本地提案都会引用该共享合约，并且其任务仅描述该组件中的工作
。然后在每个repo中分别运行`/opsx:apply`； root
分辨率将工件和实现编辑范围保留在该存储库范围内。
现在可以测试、审查、合并服务和前端更改，并
独立存档。

如果必须在共享存储更改仍处于活动状态时开始实施，则
使用
`openspec show add-checkout-promo --store team-plans`显式获取它；参考索引列表
规范商店规格，而不是活跃的商店更改。保持商店分支和
组件分支在其拉取请求描述中链接，以便审阅者
可以看到每个实现遵循哪个版本的合约。

## 故事：跨越团队界限的需求

平台团队拥有需求。产品团队在他们自己的存储库中使用他们自己的设计来针对它们构建
。参考文献描述了这种
关系，但没有移动任何人的工作。

```
   platform-reqs (store)                 api-server (code repo)
   owned by the platform team            owned by a product team
   ┌──────────────────────────┐          ┌──────────────────────────┐
   │ openspec/specs/          │ ◀────────│ openspec/config.yaml     │
   │   payments/spec.md       │ reads    │   references:            │
   │   auth/spec.md           │          │     - platform-reqs      │
   │                          │          │ openspec/specs/          │
   │ openspec/changes/        │          │   (their own designs)    │
   │   platform work          │          │ openspec/changes/        │
   │                          │          │   (their own work)       │
   │                          │          └──────────────────────────┘
   └──────────────────────────┘
```

**产品团队声明其借鉴内容** 在其回购协议中
`openspec/config.yaml`:

```yaml
references:
  - platform-reqs
```

引用是只读上下文。该存储库保留其自己的 `openspec/` 根；
 工作保留在那里。更改内容：该存储库中的 `openspec instructions` 现在 
 包含引用商店规格的索引 - 每个索引都有一行 
 摘要和确切的获取命令 (`openspec show <spec-id> --type spec
--store platform-reqs`). An agent working in `api-server` 可以找到 
 上游支付要求，引用它们，并将其底层设计写入 
 存储库自己的根中 - 无需任何人粘贴上下文。

参考可以携带其克隆源，因此没有 
 存储的队友可以获得完整的修复，而不是陷入死胡同：

```yaml
references:
  - { id: platform-reqs, remote: "git@github.com:acme/platform-reqs.git" }
```

**当您希望计划和代码一起打开时，请创建一个工作集。**这是
个人且明确的：每个人选择他们在计算机上实际使用的文件夹
。这些本地结帐路径的任何内容均未
提交到共享计划存储库。

```bash
openspec workset create platform \
  --member ~/openspec/platform-reqs \
  --member ~/src/api-server \
  --member ~/src/web-app
```

## 你可以随时问两个问题

**“我的设置正常吗？”** - `openspec doctor` 检查当前根目录和
其引用的存储，只读，每个发现都有可粘贴的修复：

```
Doctor

Root
  Location: /Users/you/src/api-server
  OpenSpec root: ok

References
  - platform-reqs: ok (/Users/you/openspec/platform-reqs)
  - design-system: Referenced store 'design-system' is not registered on this machine.
    Fix: git clone -- git@github.com:acme/design-system.git '/Users/you/openspec/design-system' && openspec store register '/Users/you/openspec/design-system' --id design-system

```

**“我正在使用什么？”** - `openspec context` 从 OpenSpec 声明中组装工作 
 集：根及其引用的存储。

```
Working context for api-server (/Users/you/src/api-server)

OpenSpec root
  api-server  /Users/you/src/api-server

Referenced stores
  platform-reqs  /Users/you/openspec/platform-reqs
    Fetch: openspec show <spec-id> --type spec --store platform-reqs
```

两者都支持 agents 的 `--json`。 `openspec 上下文 --code-workspace
<path>` 另外写入一个包含整个 
 集的 VS Code 工作区文件 - 这是此命令执行的唯一写入操作。

## 工作集：重新打开您一起处理的文件夹

与上述所有内容分开：大多数人在每个会话中都会打开相同的几个文件夹
 \- 计划存储库加上两个或三个代码存储库。
一个**工作集**是一个个人的、命名的视图，可以在您的工具中使用一个
命令重新打开选择。

```
  workset "platform"                 openspec workset open platform
  ├── team-plans   ~/openspec/team-plans         │
  ├── api-server   ~/src/api-server              ▼
  └── web-app      ~/src/web-app       all three open in your tool
```

```bash
openspec workset create platform \
  --member ~/openspec/team-plans --member ~/src/api-server \
  --tool code
openspec workset list
```

```
platform  (opens in VS Code)
  team-plans  /Users/you/openspec/team-plans
  api-server  /Users/you/src/api-server
```

`openspec workset open platform` 然后启动保存的工具：编辑器
(VS Code, Cursor) 为每个成员打开一个窗口并返回。第一个
成员是主要成员。使用 `--tool <id>` 随时覆盖该工具。

工作集故意是*而不是*共享状态。它们存在于您的机器上，
从不承诺，也不对作品做出任何声明——它们只记录
您喜欢一起打开的内容。删除一个永远不会触及成员
文件夹。新工具是配置，而不是代码：通过 
 工作空间文件或每个文件夹附加标志启动的任何内容都可以添加到全局配置 (`openspec config edit`) 中的 `openers`
 键下。

## 命令如何决定在哪里采取行动

每个普通命令都以相同的方式解析其根，按以下顺序：

```
1. --store <id>          you said so explicitly        → that store
2. nearest openspec/     a real planning root here     → this repo
   (walking up from cwd)
3. store: pointer        config.yaml declares a store  → that store
4. defaultStore          global config sets a machine  → that store
                         default
5. none of the above     stores registered on this     → error with a
                         machine?                        selection hint
                         no stores registered?         → the current
                                                          directory
                                                          (classic behavior)
```

`Using OpenSpec root:` 行（以及 `--json` 输出中的 `root` 块）
 告诉您所处的情况。

## 已知的限制

- **Beta 版本。** 此页面上的所有内容可能会在版本之间发生变化 —
名称、标志、文件格式、JSON 键。
- **每台机器每个商店 ID 一次结帐。** 注册第二次结帐
在同一 ID 下失败，首先提示 `store unregister`。
- **从不同步——设计使然。** OpenSpec从不克隆、拉动或推送。
陈旧的结账显示陈旧的规格，直到*你*拉动；引用是
从磁盘上的任何内容实时索引。
- **空计划文件夹可以不存在。**新商店可能没有
Git 
 中的 `openspec/changes/`、`openspec/specs/` 或 `openspec/changes/archive/` 尚未。这是在测试期间被接受的；一旦正常的
命令为它们创建文件，这些文件夹就会出现。
- **指针存储库保留指针。** 一个仅配置的存储库，其
`openspec/config.yaml`声明`store: <id>`被视为外部化的
规划，而不是作为商店结帐来注册。如果您有意将该存储库转换为本地存储根目录，请先删除 `store:` 行
。
- **某些命令保留在原处。** `templates` 和
已弃用的名词形式 (`openspec change show`, ...) 仅作用于当前 
 目录 - 不作用于 `--store`。 `schemas` 遵循规范的根选择 
 优先级并接受 `--store <id>`，同时保持其成功的 JSON 数组 
 形状不变。
- **每台机器状态是每台机器。** 存储注册表和工作集
是本地设置。您的机器布局
 从未致力于共享规划。
- **工作集的两种启动方式。**无法使用
工作区文件或每个文件夹附加标志无法添加为开启程序。
- **Agent JSON 有一个已知的外壳拆分 ** （存储系列密钥是
snake_case，工作流程系列 camelCase）。记录在
[agent合约](<OpenSpec 面向代理的 CLI 输出契约.md>)中；统一它被推迟到
版本发布。

## 事物居住的地方

|什么|哪里|共享？|
|---|---|---|
|一家店的规划|`<store>/openspec/`（规格、变更）|是的——提交并推动它|
|商店的身份| `<store>/.openspec-store/store.yaml` |是的——与商店承诺|
|商店登记处| `<data dir>/openspec/stores/registry.yaml` |否 — 仅此机器|
|工作集| `<data dir>/openspec/worksets/` |否 — 仅此机器|

`<data dir>` 在 macOS 和 Linux 上为 `~/.local/share/openspec`（或在设置时为 
`$XDG_DATA_HOME/openspec`），在 
Windows 上为 `%LOCALAPPDATA%\openspec`。

## 参考

此页面上每个命令的确切标志和 JSON 形状：
[CLI 参考 ](<OpenSpec 命令行参考.md>)（商店、医生、工作环境、个人 
 工作集）和 [agent 合同 ](<OpenSpec 面向代理的 CLI 输出契约.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Stores: Plan in Its Own Repo

> **Beta.** Stores, references, working context, and worksets are
> new. Command names, flags, file formats, and JSON output may still change
> shape between releases. Every walkthrough below was run against the
> current build, but re-read this guide after upgrading.

## The problem this solves

OpenSpec normally lives inside one code repo: an `openspec/` folder next to
your code, holding specs and changes for that repo.

That stops fitting the moment your planning is bigger than one repo:

- Your work spans several repos — one feature touches the API server, the
  web app, and a shared library. Whose `openspec/` folder does the plan
  live in?
- Your team plans before code exists, or plans things that never become
  code in *this* repo.
- Requirements are owned by one team and consumed by others. The wiki
  version drifts, and your coding agent can't read it anyway.

A **store** is the answer: a standalone repo whose whole job is planning.
It has the same `openspec/` shape you already know — specs and changes —
plus a small identity file. You register it on your machine once, by name,
and then every normal OpenSpec command can work in it from anywhere.

## The shape

```
            team-plans  (a store: planning in its own repo)
            ├── .openspec-store/store.yaml     identity: "I am team-plans"
            └── openspec/
                ├── specs/      what is true
                └── changes/    what is in motion
                      ▲
                      │ registered on each machine by name;
                      │ shared by pushing/cloning like any repo
        ┌─────────────┼─────────────┐
        │             │             │
    web-app       api-server     mobile-app
   (code repo)   (code repo)    (code repo)
```

Two rules keep this simple:

1. **A store is just a git repo.** You commit, push, pull, and review it
   yourself. OpenSpec never clones, syncs, or pushes anything on its own.
2. **Declarations, not machinery.** Repos can *declare* how they relate to
   stores (shown below). Declarations change what OpenSpec can tell you —
   never where your commands act.

## Five minutes to your first store

Two commands take you from nothing to a working, store-scoped change:

```bash
openspec store setup team-plans --path ~/openspec/team-plans
```

```
Store ready: team-plans
Location: /Users/you/openspec/team-plans
OpenSpec root: ready
Registry: registered

Next: run normal OpenSpec commands against this store, for example:
  openspec new change <change-id> --store team-plans
Share this store by committing and pushing it like any Git repo.
```

```bash
openspec new change add-login --store team-plans
```

```
Using OpenSpec root: team-plans (/Users/you/openspec/team-plans)
Created change 'add-login' at /Users/you/openspec/team-plans/openspec/changes/add-login/
Schema: spec-driven
Next: openspec status --change add-login --store team-plans
```

That's the whole model. From here the lifecycle is exactly what you know —
`status`, `instructions`, `validate`, `archive` — with `--store team-plans`
on each command, and every printed hint carries the flag for you. The
`Using OpenSpec root:` line always tells you where a command is acting.

## Story: one team, one planning repo

A team keeps its specs and changes in `team-plans` instead of scattering
them across code repos.

**Day one (whoever sets it up):**

```bash
openspec store setup team-plans --path ~/openspec/team-plans \
  --remote git@github.com:acme/team-plans.git
git -C ~/openspec/team-plans push -u origin main
```

Passing `--remote` records the clone URL inside the store's own identity
file (`.openspec-store/store.yaml`), in the initial commit. Every future
clone is born knowing where it came from, so health checks and error
messages can print a complete, pasteable fix for teammates who don't have
it yet.

**Every teammate (once per machine):**

```bash
git clone git@github.com:acme/team-plans.git ~/openspec/team-plans
openspec store register ~/openspec/team-plans
```

From then on, everyone works in the same planning repo by name:

```bash
openspec status --store team-plans --change add-login
openspec show add-login --store team-plans
```

**Sharing work is git, on purpose.** A change you create exists only in
your checkout until you commit and push it — same as code. Plans get
branches, pull requests, and review for free, because a store is an
ordinary repo.

**Connecting the team's code repos.** A code repo whose planning is fully
externalized needs exactly one line, in `openspec/config.yaml`:

```yaml
# web-app/openspec/config.yaml
store: team-plans
```

Now every OpenSpec command run inside `web-app` acts on `team-plans` with
no flags at all:

```bash
cd ~/src/web-app
openspec status --change add-login
```

```
Using OpenSpec root: team-plans (/Users/you/openspec/team-plans)
...
```

The pointer is a fallback, never an override: an explicit `--store` always
wins, and if the repo grows real planning folders of its own, those win
(with a warning to remove the stale pointer).

**One default for every repo on your machine.** If you work across many
code repos that all plan into the same store, set it once, globally,
instead of adding the `store:` line to each repo:

```bash
openspec config set defaultStore team-plans
```

Now any command run outside a planning root — and with no `--store` and no
project pointer — resolves to `team-plans`. It sits at the bottom of the
precedence list, so `--store`, a local root, and a project `store:` pointer
all still win. The root banner and JSON `root` block report
`source: "global_default"` with the store id, so you can always tell a
machine-wide default from a repo's own pointer. Clear it with
`openspec config unset defaultStore`. If the id is not registered, commands
error and tell you to register it or clear the stale default.

## Example: one feature, two component repos

Suppose `add-checkout-promo` changes both `checkout-api` and
`checkout-web`. The team wants one shared product contract, while each code
repo still needs its own implementation tasks, branch, and review.

Use two layers:

1. Keep the shared behavior in `team-plans`.
2. Keep implementation plans in each component repo and reference the store
   as read-only upstream context.

First, plan the shared contract in the store:

```bash
openspec new change add-checkout-promo --store team-plans
openspec status --change add-checkout-promo --store team-plans
```

The proposal and specs should describe the behavior at the boundary between
the components — for example, the promotion fields returned by the service
and how the frontend handles an ineligible checkout. Review this change in
the store repo like any other branch and pull request.

### What context does planning see?

Selecting a store changes the OpenSpec root; it does not discover or read
every code repo that uses that store. Store instructions see the artifacts
and configured context in the store. They see component code only when those
folders are also available to the agent or editor and the agent reads them.

A workset is a convenient way to open the planning store and both code repos
together:

```bash
openspec workset create checkout-promo \
  --member ~/openspec/team-plans \
  --member ~/src/checkout-api \
  --member ~/src/checkout-web \
  --tool code
openspec workset open checkout-promo
```

This makes the folders visible in one IDE workspace. It does not copy source
context into the store, select affected repos, or grant an agent permission
to edit them. Put durable cross-component facts in the shared specs; do not
rely on a planner remembering source it happened to inspect.

### How does implementation start in each repo?

When no explicit `--store` or nearer `openspec/` root applies, a
`store: team-plans` pointer routes commands to that store. It does not split
one store task list by the directory from which `apply` was invoked. OpenSpec
currently does not route tasks to repos.

When each component needs an independently scoped apply/review cycle, give it
a local OpenSpec root and reference the central store instead of pointing at
it:

```yaml
# checkout-api/openspec/config.yaml (and likewise in checkout-web)
schema: spec-driven
references:
  - team-plans
```

After the shared contract is approved and available in the store's main
specs, create a small local change for the component's part:

```bash
cd ~/src/checkout-api
openspec new change implement-checkout-promo-api

cd ~/src/checkout-web
openspec new change implement-checkout-promo-ui
```

The reference index in each repo's instructions supplies the store spec's
summary and exact `openspec show ... --store team-plans` fetch command. Each
local proposal cites that shared contract, and its tasks describe only work
in that component. Then run `/opsx:apply` in each repo separately; root
resolution keeps the artifacts and implementation edits scoped to that repo.
The service and frontend changes can now be tested, reviewed, merged, and
archived independently.

If implementation must begin while the shared store change is still active,
fetch it explicitly with
`openspec show add-checkout-promo --store team-plans`; reference indexes list
canonical store specs, not active store changes. Keep the store branch and
component branches linked in their pull-request descriptions so reviewers
can see which version of the contract each implementation follows.

## Story: requirements that cross team lines

A platform team owns the requirements. Product teams build against them,
in their own repos, with their own designs. A reference describes that
relationship without moving anyone's work.

```
   platform-reqs (store)                 api-server (code repo)
   owned by the platform team            owned by a product team
   ┌──────────────────────────┐          ┌──────────────────────────┐
   │ openspec/specs/          │ ◀────────│ openspec/config.yaml     │
   │   payments/spec.md       │ reads    │   references:            │
   │   auth/spec.md           │          │     - platform-reqs      │
   │                          │          │ openspec/specs/          │
   │ openspec/changes/        │          │   (their own designs)    │
   │   platform work          │          │ openspec/changes/        │
   │                          │          │   (their own work)       │
   │                          │          └──────────────────────────┘
   └──────────────────────────┘
```

**The product team declares what it draws on** in its repo's
`openspec/config.yaml`:

```yaml
references:
  - platform-reqs
```

References are read-only context. The repo keeps its own `openspec/` root;
work stays there. What changes: `openspec instructions` in that repo now
includes an index of the referenced store's specs — each with a one-line
summary and the exact fetch command (`openspec show <spec-id> --type spec
--store platform-reqs`). An agent working in `api-server` can find the
upstream payment requirements, cite them, and write its low-level design in
the repo's own root — without anyone pasting context around.

A reference can carry its clone source, so teammates who don't have the
store yet get a complete fix instead of a dead end:

```yaml
references:
  - { id: platform-reqs, remote: "git@github.com:acme/platform-reqs.git" }
```

**When you want the plan and code open together, make a workset.** This is
personal and explicit: each person chooses the folders they actually work
with on their machine. Nothing about those local checkout paths is
committed to the shared planning repo.

```bash
openspec workset create platform \
  --member ~/openspec/platform-reqs \
  --member ~/src/api-server \
  --member ~/src/web-app
```

## Two questions you can always ask

**"Is my setup healthy?"** — `openspec doctor` checks the current root and
its referenced stores, read-only, with a pasteable fix per finding:

```
Doctor

Root
  Location: /Users/you/src/api-server
  OpenSpec root: ok

References
  - platform-reqs: ok (/Users/you/openspec/platform-reqs)
  - design-system: Referenced store 'design-system' is not registered on this machine.
    Fix: git clone -- git@github.com:acme/design-system.git '/Users/you/openspec/design-system' && openspec store register '/Users/you/openspec/design-system' --id design-system

```

**"What am I working with?"** — `openspec context` assembles the working
set from OpenSpec declarations: the root and the stores it references.

```
Working context for api-server (/Users/you/src/api-server)

OpenSpec root
  api-server  /Users/you/src/api-server

Referenced stores
  platform-reqs  /Users/you/openspec/platform-reqs
    Fetch: openspec show <spec-id> --type spec --store platform-reqs
```

Both support `--json` for agents. `openspec context --code-workspace
<path>` additionally writes a VS Code workspace file containing the whole
set — the only write this command performs.

## Worksets: reopen the folders you work on together

Separate from all of the above: most people open the same few folders
together every session — the planning repo plus two or three code repos.
A **workset** is a personal, named view of exactly that, reopened with one
command in your tool of choice.

```
  workset "platform"                 openspec workset open platform
  ├── team-plans   ~/openspec/team-plans         │
  ├── api-server   ~/src/api-server              ▼
  └── web-app      ~/src/web-app       all three open in your tool
```

```bash
openspec workset create platform \
  --member ~/openspec/team-plans --member ~/src/api-server \
  --tool code
openspec workset list
```

```
platform  (opens in VS Code)
  team-plans  /Users/you/openspec/team-plans
  api-server  /Users/you/src/api-server
```

`openspec workset open platform` then launches the saved tool: editors
(VS Code, Cursor) open one window with every member and return. The first
member is the primary. Override the tool any time with `--tool <id>`.

Worksets are deliberately *not* shared state. They live on your machine,
are never committed, and make no claims about the work — they only record
what you like open together. Removing one never touches the member
folders. New tools are configuration, not code: anything launched via a
workspace file or per-folder attach flags can be added under the `openers`
key in the global config (`openspec config edit`).

## How commands decide where to act

Every normal command resolves its root the same way, in this order:

```
1. --store <id>          you said so explicitly        → that store
2. nearest openspec/     a real planning root here     → this repo
   (walking up from cwd)
3. store: pointer        config.yaml declares a store  → that store
4. defaultStore          global config sets a machine  → that store
                         default
5. none of the above     stores registered on this     → error with a
                         machine?                        selection hint
                         no stores registered?         → the current
                                                          directory
                                                          (classic behavior)
```

The `Using OpenSpec root:` line (and the `root` block in `--json` output)
tells you which case you're in.

## Known limitations

- **Beta shape.** Everything on this page may change between releases —
  names, flags, file formats, JSON keys.
- **One checkout per store id per machine.** Registering a second checkout
  under the same id fails with a hint to `store unregister` first.
- **No sync, ever — by design.** OpenSpec never clones, pulls, or pushes.
  A stale checkout shows stale specs until *you* pull; references are
  indexed live from whatever is on disk.
- **Empty planning folders can be absent.** A new store may not have
  `openspec/changes/`, `openspec/specs/`, or `openspec/changes/archive/` in Git
  yet. That is accepted during the beta; those folders appear once normal
  commands create files for them.
- **Pointer repos stay pointers.** A config-only repo whose
  `openspec/config.yaml` declares `store: <id>` is treated as externalized
  planning, not as a store checkout to register. Remove the `store:` line first
  if you intentionally want to convert that repo into a local store root.
- **Some commands stay where they are.** `templates` and the
  deprecated noun forms (`openspec change show`, ...) act on the current
  directory only — no `--store`. `schemas` follows the canonical root-selection
  precedence and accepts `--store <id>` while keeping its successful JSON array
  shape unchanged.
- **Per-machine state is per-machine.** The store registry and worksets
  are local settings. Nothing about your machine's layout is
  ever committed to shared planning.
- **Two launch styles for worksets.** A tool that can't be launched with a
  workspace file or per-folder attach flags can't be added as an opener.
- **Agent JSON has a known casing split** (store-family keys are
  snake_case, workflow-family camelCase). Documented in the
  [agent contract](<OpenSpec 面向代理的 CLI 输出契约.md>); unifying it is deferred to a
  versioned release.

## Where things live

| What | Where | Shared? |
|---|---|---|
| A store's planning | `<store>/openspec/` (specs, changes) | Yes — commit and push it |
| A store's identity | `<store>/.openspec-store/store.yaml` | Yes — committed with the store |
| The store registry | `<data dir>/openspec/stores/registry.yaml` | No — this machine only |
| Worksets | `<data dir>/openspec/worksets/` | No — this machine only |

`<data dir>` is `~/.local/share/openspec` on macOS and Linux (or
`$XDG_DATA_HOME/openspec` when set), and `%LOCALAPPDATA%\openspec` on
Windows.

## Reference

Exact flags and JSON shapes for every command on this page:
[CLI reference](<OpenSpec 命令行参考.md>) (Stores, Doctor, Working context, Personal
worksets) and the [agent contract](<OpenSpec 面向代理的 CLI 输出契约.md>).


</details>
