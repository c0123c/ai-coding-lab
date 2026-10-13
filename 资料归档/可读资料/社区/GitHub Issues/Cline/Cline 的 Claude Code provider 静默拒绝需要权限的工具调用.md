# Cline 的 Claude Code provider 静默拒绝需要权限的工具调用

社区报告权限请求没有审批提示；作者随后通过 CLI 验证未受信任工作区会忽略项目 allow 规则，评论保留这一修正。

- 来源：GitHub Issues／Cline
- 资料类型：社区Issue正文与全部评论
- 文档时间：发表（Issue创建）：2026-09-30；Issue活动更新：2026-10-01；抓取：2026-10-13
- 阅读状态：完整中文译文，由 AI 翻译；包括帖子原文与全部已保存评论。
- 使用边界：社区问题讨论；保留作者后续修正，不据此推断其他版本或所有项目都有同一问题。

[原文发布页](<https://github.com/cline/cline/issues/14699>) · [原始文件](<../../../../原始抓取备份/原文/社区/GitHub Issues/Cline/Cline 的 Claude Code provider 静默拒绝需要权限的工具调用.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

### Cline 使用界面

VSCode 扩展

### Cline 版本

4.1.21（同时安装的 4.1.22 包也以相同方式启动 provider，见下文）

### Beta 版本

- [ ] 我正在使用 Cline 的 Beta 版本

### 发生了什么？

这是 #13146 的后续。该问题在 4.1.12 中修复了工作区**内部**的写入（已在那条 Issue 中确认）。其他所有需要 Claude Code 权限的操作仍然被静默拒绝：模型收到错误，用户从未看到提示，Cline 的界面里也没有可供授权的入口。

在 Claude Code provider 下，以下操作都会被拒绝，整个过程中不会出现审批提示：

- **任何终端命令**，无论多么无害：`python3 -c "print(42)"` → `{"error":"This command requires approval"}`
- **任何 MCP 工具调用**，包括只读调用：`mcp__<server>__research_config` → `{"error":"Claude requested permissions to use mcp__<server>__research_config, but you haven't granted it yet."}`
- **任何工作区外的文件**，无论读取还是写入：`{"error":"Claude requested permissions to read from /Users/me/probe-out/hello.txt, but you haven't granted it yet."}`（`write to` 也一样）

工作区**内部**的读取和编辑无需提示就能执行，符合 #13146 修复的预期。

**用于区分情况的因素是规则放在哪里。** 对同一个会话中的相同规则，行为会随存放它们的 Claude 设置文件而改变：

| 规则 | 放在工作区的 `.claude/settings.json`（项目级） | 放在 `~/.claude/settings.json`（用户级） |
| --- | --- | --- |
| `deny: ["Read(./canary.txt)"]` | **生效**——读取被拒绝：*"File is in a directory that is denied by your permission settings."* | — |
| `allow: ["Bash(python3:*)", "Bash(python3 *)"]` | 被忽略——仍然提示 *"This command requires approval"* | **有效**——命令执行，输出 `42` |
| `allow: ["mcp__<server>__research_config"]` | 未尝试 | **有效**——工具执行并返回数据 |
| `permissions.additionalDirectories: ["/Users/me/probe-out"]` | 被忽略——外部读取和写入仍然被拒绝 | **有效**——外部读取和写入成功 |
| `allow: ["Read(//Users/me/probe-out/**)", "Edit(…)", "Write(…)"]` | 被忽略（也试过 `~/…` 和 `/Users/…` 写法） | **有效**（只使用 `//` 写法） |

`deny` 金丝雀测试是对照组：它表明这些会话**确实**加载了项目设置文件（与 #13146 修复中的 `settingSources: ["user","project"]` 一致），但其中的 allow 规则和 `additionalDirectories` 没有作用。每种用户级机制也都单独测试过（每次运行前删除文件），因此任意一种机制单独使用都能打开该目录。

**为什么始终不出现提示？** Cline 自己的源码已经说明原因，在 `main` 分支的 Claude Code provider 模块（`sdk/packages/llms/src/providers/vendors/community.ts`）中写着：

> Cline 没有把交互式权限提示接入 CLI 会话（没有 canUseTool），因此所有未预先批准的操作都会直接被拒绝。

4.1.21 和 4.1.22 包含相同的启动默认值（`settingSources ["user","project"]`、`permissionMode "acceptEdits"`）。据我观察，VS Code 扩展只向 provider 传入 `cwd`（以及可选的 Claude Code 路径），因此用户也无法从 Cline 设置 `permissionMode`、`additionalDirectories` 或 `permissionPromptToolName`。目前唯一有效的途径是 Claude 的**用户级**设置文件；它也是用户自己使用 Claude Code CLI 时的设置文件，因此为 Cline 授予的权限会同时适用于该用户使用 Claude Code 的所有地方。

**预期行为**，按优先顺序列出；任何一种都能让 provider 在工作区内编辑以外的 Agent 工作中变得可用：

1. **把 `canUseTool` 接到 Cline 的审批界面**，使未预先批准的请求显示通常的批准/拒绝提示（并遵循 Cline 的自动批准设置），而不是静默失败。这对应 #13146 中“采用原生路径并正确接线”的选项；该选项的另外两部分（`cwd`、`settingSources`）已在 4.1.12 中发布。
2. **或者允许用户设置目前固定的 provider 启动选项**——至少包括 `permissionMode`、`additionalDirectories`、`allowedTools`，并可选地提供 `permissionPromptToolName`——入口放在 Cline 的 provider 设置中。启动器已经接受这些选项（`defaultSettings`），扩展只需把它们传过去。这也提供了处理 #13277/#13301 中 plan 模式说明的位置（目前无条件设置 `permissionMode: 'acceptEdits'`）。
3. **至少明确告知这一限制**：当该 provider 下的工具调用因缺少权限而被拒绝时，在界面中说明无法显示提示，规则必须放在 `~/.claude/settings.json`；同时在 provider 文档中说明项目级 allow 规则在此处不被遵循。

相关问题：#13146（工作区内部写入——4.1.12 已修复；`canUseTool` 部分仍未解决）、#13277 / #13301（对 provider 执行的工具进行工具钩子门控——已拒绝；另有 plan 模式说明）、cline/kanban#586（Kanban 中 Claude Agent 出现相同的静默拒绝症状，包括不遵循项目 allow 规则）。

### 复现步骤

1. 创建一个临时工作区和一个工作区外的目录：
   `mkdir -p ~/probe-ws ~/probe-out && echo "outside file" > ~/probe-out/hello.txt && echo "canary" > ~/probe-ws/canary.txt`
   确保 `~/.claude/settings.json` 中没有 `permissions` 区块。
2. 把 `~/probe-ws` 作为单根 VS Code 工作区打开。选择 Claude Code provider（Claude Sonnet 5），使用 Act 模式。创建新任务：

   > 不要使用任何终端命令。1）使用你的文件读取工具读取 /Users/me/probe-out/hello.txt，并引用其内容。2）使用你的文件写入工具创建 /Users/me/probe-out/written.txt，内容为单词 written。逐字引用任何错误。

   **观察结果：** 两项都被拒绝（*"…haven't granted it yet"*），没有显示提示，也没有写入任何内容。
3. 创建新任务：

   > 使用你的终端工具，严格运行以下命令：python3 -c "print(42)"。逐字引用任何输出或错误。

   **观察结果：** *"This command requires approval"*，没有显示提示。
4. 添加 `~/probe-ws/.claude/settings.json`：

```json
   {
     "permissions": {
       "additionalDirectories": ["/Users/me/probe-out"],
       "allow": ["Bash(python3:*)", "Bash(python3 *)", "Read(//Users/me/probe-out/**)", "Edit(//Users/me/probe-out/**)", "Write(//Users/me/probe-out/**)"],
       "deny": ["Read(./canary.txt)"]
     }
   }
```

   重新加载窗口；创建新任务；让它读取 `~/probe-ws/canary.txt`，然后重复步骤 2 和 3。
   **观察结果：** 金丝雀文件读取被 deny 规则拒绝（所以文件确实被加载了）；工作区外的读取和写入以及 `python3` 命令仍然被拒绝。
5. 把同样的 `allow` / `additionalDirectories` 条目移到 `~/.claude/settings.json`（先备份）。重新加载；创建新任务；重复步骤 2 和 3。
   **观察结果：** 外部读取和写入成功（磁盘上出现了该文件），`python3` 输出 `42`。配置项目 MCP server 后，在用户文件中添加一条针对其某个工具的 allow 规则，也会让该工具变得可调用；此前它会以相同方式被拒绝。

### Provider / 模型

Claude Code provider（Claude 订阅，不是 API key）——Claude Sonnet 5

### 诊断信息

- Cline 4.1.21（VS Code 扩展）；同时安装了 4.1.22，并检查过其包
- Claude Code CLI：2.1.263
- 模式：Act
- Cline 自动批准：已启用——对这些拒绝没有作用（拒绝发生在 Claude Code 会话内部）

为完整起见，如果这有助于 #13277 中的信息披露工作：在 4.1.21 中使用此 provider 时，生命周期钩子 `TaskStart`、`UserPromptSubmit` 和 `TaskComplete` **会**触发（工具钩子不会触发，正如那里所记录的）。

### 系统信息

ProductName:	macOS
ProductVersion:	26.6.2 (arm64), Apple Silicon
BuildVersion:		25G83

## 评论 5907911712 — linear-code

- [原评论](<https://github.com/cline/cline/issues/14699#issuecomment-5907911712>)
- 创建：2026-09-30T09:06:08Z
- 最后编辑时间：原页未提供

<!-- linear-linkback -->
<p><a href="https://linear.app/cline-bot/issue/CLINE-3489">CLINE-3489</a></p>

## 评论 5908564974 — Dante-dan

- [原评论](<https://github.com/cline/cline/issues/14699#issuecomment-5908564974>)
- 创建：2026-09-30T09:41:57Z
- 最后编辑时间：原页未提供

我检查了当前 [`community.ts`](<https://github.com/cline/cline/blob/3435f72fcf4cb843bee946b8f9e981683564c9e3/sdk/packages/llms/src/providers/vendors/community.ts>) 中的 Claude Code 启动器：它已经把 `settingSources` 默认设为 `['user', 'project']`，把 `permissionMode` 默认设为 `acceptEdits`，并明确注明没有 `canUseTool` 回调。我没有在该封装中看到 Cline 特有的项目级/用户级权限优先级处理。`sdk/packages/core/src/services/llms/handler-factory.ts` 中的 `buildGatewayProviderOptions()` 会把工作区 `cwd` 转发给此 provider，而 VS Code 的 provider 设置界面提供的是 CLI 路径和推理控制项，没有这些权限选项。因此，你的项目级 deny 对照测试很重要：这并不只是 #13146 中旧的缺少 `settingSources` 问题。

我建议沿着你提出的启动选项方案，先做一轮边界明确的工作：

- 使用 provider 实际采用的 CLI/SDK 版本，隔离测试报告中的项目级与用户级 `allow` / `additionalDirectories` 行为，同时保留项目 deny 金丝雀测试。在改变配置语义前，先确定差异来自 Cline 的启动配置还是底层 CLI。
- 如果修正启动器仍不能解决问题，就通过现有的 `defaultSettings` 路径提供明确的、仅限此 provider 的 `allowedTools` 和 `additionalDirectories`，让 Cline 会话获得这些授权而不改变用户的全局 Claude 设置。保留现有默认值和明确的 deny 规则；不要启用绕过权限，也不要自动授予终端/MCP 访问权限。
- 在文档中说明，这个限定范围的途径只是在启动时提供明确授权，不会添加交互式审批提示。扩展 `sdk/packages/llms/src/providers/gateway.test.ts` 中现有的启动默认值/覆盖测试，并检查对应 CLI 复现，以验证工作区内部编辑、报告中的 Bash/外部目录案例和 deny 行为。

我会把 `canUseTool` 审批桥接以及对 plan 模式/`permissionMode` 行为的更改放在这轮工作的范围之外。#13301 拒绝了永久性的 provider 专用工具钩子桥接；我不会在这里把那种桥接重新引入为绕过方案。我尚未复现所报告的 CLI 行为，因此以上源码检查只能证明启动路径的情况，不能作为项目 allow 规则被忽略的已确认解释。

核心维护者会批准这个范围，还是更倾向于 Issue 中只处理拒绝信息/信息披露的方案？

## 评论 5908809623 — csimonb

- [原评论](<https://github.com/cline/cline/issues/14699#issuecomment-5908809623>)
- 创建：2026-09-30T09:55:36Z
- 最后编辑时间：原页未提供

谢谢——这的确是首先应该问的问题，而且答案很明确：问题并不在 Cline 的启动配置。Claude Code 自身会忽略尚未受信任目录中的项目 `allow` 条目。

在一个新建目录中使用相同的项目设置，直接运行（Claude Code 2.1.263、`claude -p --permission-mode acceptEdits`）时，CLI 输出：

> Ignoring 2 permissions.allow entries from .claude/settings.json: this workspace has not been trusted. Run Claude Code interactively here once and accept the trust dialog, or set projects["<path>"].hasTrustDialogAccepted: true in ~/.claude.json.

随后它拒绝 `python3 -c "print(42)"`，但项目 deny 金丝雀规则仍然生效。在该目录中交互式运行一次 `claude` 并接受信任对话框后，同样的直接运行会输出 `42`（金丝雀读取仍被拒绝）。

通过 Cline 测试（4.1.21、Claude Code provider、Sonnet 5、VS Code 打开该受信任目录并重新加载窗口）：项目 allow 规则会执行 `python3`，项目 `additionalDirectories` + `Read`/`Edit`/`Write` 规则会开放一个工作区外的目录供读取和写入。因此，目录获得信任后，项目级授权就能通过 Cline 生效——而且它们已经限定在那一个目录内，无需修改全局设置。

难以发现的原因是 Cline 从不显示这条 CLI 警告：在 Cline 内部，唯一的症状是简单的 "requires approval" / "haven't granted it yet"。因此，我建议第一轮工作可以比原提议更小：

1. 在 Cline 界面中**显示 CLI 的警告**（至少显示 "Ignoring … not been trusted" 这一行），和/或在 provider 文档中说明项目权限规则只适用于受信任目录。
2. 你提出的通过 `defaultSettings` 提供 provider 范围内的 `allowedTools` / `additionalDirectories`，对不能或不愿信任目录的人仍然有用——但现在它已不是避免修改全局设置的必要条件。

同意把 `canUseTool` 桥接和 plan 模式放在这轮工作范围之外。

## 评论 5915249153 — chenhz01

- [原评论](<https://github.com/cline/cline/issues/14699#issuecomment-5915249153>)
- 创建：2026-09-30T16:19:23Z
- 最后编辑时间：原页未提供

很好的区分分析。看到这种范围分裂后，静默拒绝就说得通了：项目 `.claude/settings.json` 的 allow 规则被忽略，而 `~/.claude/settings.json` 中相同的规则有效——这是权限集合合并方式中的优先级错误。下面这个小审计器会对**两个**范围检查一个命令，并告诉你到底是哪一个授予了权限，可以方便地复现具体哪些规则被丢弃：

```python
# cline_perm_scope_auditor.py
# For #14699: checks a command against BOTH project (.claude/settings.json)
# and user (~/.claude/settings.json) permission scopes to expose the
# project-vs-user allow discrepancy behind the silent refusals.

import json, os

def load(path):
    try:
        with open(path) as f:
            return json.load(f)
    except OSError:
        return {}

def resolve(cmd, project_cfg, user_cfg):
    p_allow = project_cfg.get("permissions", {}).get("allow", [])
    u_allow = user_cfg.get("permissions", {}).get("allow", [])
    p_deny  = project_cfg.get("permissions", {}).get("deny", [])
    if any(cmd in d for d in p_deny):
        return "DENIED (project deny)"
    if any(cmd in a for a in p_allow):
        return "ALLOWED via project allow"
    if any(cmd in a for a in u_allow):
        return "ALLOWED via USER allow (project allow ignored this cmd)"
    return "REQUIRES APPROVAL (no matching allow in either scope)"

# --- point at your real config files (no secrets needed) ---
proj = load(os.path.expanduser("YOUR_PROJECT/.claude/settings.json"))
user = load(os.path.expanduser("~/.claude/settings.json"))
for cmd in ["Bash(python3:*)", "mcp__svc__research_config"]:
    print(cmd, "->", resolve(cmd, proj, user))
```

## 评论 5915641306 — Dante-dan

- [原评论](<https://github.com/cline/cline/issues/14699#issuecomment-5915641306>)
- 创建：2026-09-30T16:44:03Z
- 最后编辑时间：原页未提供

谢谢分享审计器。我会把已确认的行为收窄到 Claude Code 的工作区信任门：此前直接运行 CLI 时，在目录尚未受信任之前会忽略项目 `allow` 条目，获得信任之后就会接受它们。这说明原因不在 Cline 的启动器，但这本身并不能证明存在权限合并的优先级错误。

这段代码是一个有用的说明示例，但子串匹配并不能复现 Claude Code 的权限匹配器，而且它只检查项目级 `deny`。我不会用它来做真正的允许/拒绝决策；直接比较目录受信任前后的 CLI 测试，是更有力的区分方法。

## 评论 5936917240 — samvallad33

- [原评论](<https://github.com/cline/cline/issues/14699#issuecomment-5936917240>)
- 创建：2026-10-01T17:33:29Z
- 最后编辑时间：原页未提供

csimonb 的 CLI 运行揭示了那条被隐藏的警告——"Ignoring 2 permissions.allow entries ... this workspace has not been trusted"——这让我重新理解了这个 bug：拒绝一直都有原因，只是 Cline 从不显示。把那一行显示出来是正确的第一步。我要补充的另一部分是：提供一个在 Cline 审批策略之前触发的钩子（这样连 YOLO/绕过配置也能覆盖），让每个判定都写出一份通过哈希链串联的回执，注明触发的规则和看到的命令。这样，"Requires approval with no prompt" 就会变成 "refused by rule N, here is the receipt"——可以用 grep 查找，而不必猜测设置文件。坦率说，它的局限是确定性的，因此会阻止已知的效果类别；对于新出现的类别，只记录而不阻止。移植版本：https://github.com/samvallad33/vestige/tree/main/operator-lite/ports/cline

（披露：我参与开源项目 Vestige；上面的钩子是我们的移植版本。）


## 原文对照

<details>
<summary>展开完整原文</summary>

# Claude Code provider: any tool call that needs permission is silently refused — no approval prompt, and project-level allow rules are ignored (follow-up to #13146)


<!-- Archive scaffolding: the following dates and comment headings identify original GitHub content. -->


Source: https://github.com/cline/cline/issues/14699

Author: csimonb

Created: 2026-09-30T09:06:04Z

Issue updatedAt: 2026-10-01T17:33:29Z


<!-- Original issue body begins. -->


### Cline Surface

VSCode Extension

### Cline Version

4.1.21 (the 4.1.22 bundle, also installed, launches the provider the same way — see below)

### Beta version

- [ ] I am using a beta version of Cline

### What happened?

Follow-up to #13146, which was fixed in 4.1.12 for writes *inside* the workspace (confirmed there). Everything else that needs Claude Code's permission is still refused, silently: the model gets an error, the user never sees a prompt, and there is nothing in Cline's UI to grant from.

Refused under the Claude Code provider, with no approval prompt at any point:

- **Any terminal command**, however harmless: `python3 -c "print(42)"` → `{"error":"This command requires approval"}`
- **Any MCP tool call**, including read-only ones: `mcp__<server>__research_config` → `{"error":"Claude requested permissions to use mcp__<server>__research_config, but you haven't granted it yet."}`
- **Any file outside the workspace**, read or write: `{"error":"Claude requested permissions to read from /Users/me/probe-out/hello.txt, but you haven't granted it yet."}` (and the same for `write to`)

Reading and editing *inside* the workspace work without a prompt, as #13146's fix intended.

**The discriminator: where the rule lives.** The same rules, for the same session, behave differently depending on which Claude settings file holds them:

| Rule | In the workspace's `.claude/settings.json` (project) | In `~/.claude/settings.json` (user) |
|---|---|---|
| `deny: ["Read(./canary.txt)"]` | **applied** — read refused: *"File is in a directory that is denied by your permission settings."* | — |
| `allow: ["Bash(python3:*)", "Bash(python3 *)"]` | ignored — still *"This command requires approval"* | **works** — command ran, printed `42` |
| `allow: ["mcp__<server>__research_config"]` | not tried | **works** — tool ran and returned data |
| `permissions.additionalDirectories: ["/Users/me/probe-out"]` | ignored — outside read/write still refused | **works** — outside read and write succeeded |
| `allow: ["Read(//Users/me/probe-out/**)", "Edit(…)", "Write(…)"]` | ignored (also tried `~/…` and `/Users/…` notations) | **works** (`//` notation alone) |

The `deny` canary is the control: it shows the project settings file *is* loaded in these sessions (consistent with `settingSources: ["user","project"]` from #13146's fix), yet its allow rules and `additionalDirectories` have no effect. Each user-level mechanism was also tested on its own (with the file deleted before each run), so either one alone opens the folder.

**Why no prompt ever appears** — Cline's own source says it, in the Claude Code provider module (`sdk/packages/llms/src/providers/vendors/community.ts` on `main`):

> Cline has no interactive permission prompt wired into the CLI session (no canUseTool), so anything not pre-approved is denied outright.

The 4.1.21 and 4.1.22 bundles contain the same launch defaults (`settingSources ["user","project"]`, `permissionMode "acceptEdits"`). As far as I can see, the VS Code extension passes the provider only `cwd` (and the optional Claude Code path), so users cannot set `permissionMode`, `additionalDirectories` or `permissionPromptToolName` from Cline either. The only working route today is the *user* Claude settings file — which is also the settings file of the person's own Claude Code CLI, so anything granted for Cline is granted everywhere they use Claude Code.

**Expected behaviour** — in order of preference; any one would make the provider usable for agentic work beyond in-workspace edits:

1. **Wire `canUseTool` to Cline's approval UI**, so a request that isn't pre-approved shows the usual approve/deny prompt (and respects Cline's auto-approve settings) instead of failing silently. This is the "native path, properly wired" option from #13146, whose other two parts (`cwd`, `settingSources`) shipped in 4.1.12.
2. **Or let users set the provider's launch options** that are currently fixed — at least `permissionMode`, `additionalDirectories`, `allowedTools`, and optionally `permissionPromptToolName` — from Cline's provider settings. The launcher already accepts them (`defaultSettings`); the extension only needs to pass them through. This would also give a place to address the plan-mode note from #13277/#13301 (`permissionMode: 'acceptEdits'` set unconditionally).
3. **At minimum, disclose it**: when a tool call is refused for want of permission under this provider, say in the UI that no prompt can be shown and that the rule must live in `~/.claude/settings.json`; and note in the provider docs that project-level allow rules are not honoured there.

Related: #13146 (writes inside the workspace — fixed in 4.1.12; `canUseTool` part still open), #13277 / #13301 (tool-hook gating for provider-executed tools — declined; plan-mode note), cline/kanban#586 (same silent-refusal symptom with the Claude agent in Kanban, including project allow rules not honoured).

### Steps to reproduce

1. Create a throwaway workspace and a folder outside it:
   `mkdir -p ~/probe-ws ~/probe-out && echo "outside file" > ~/probe-out/hello.txt && echo "canary" > ~/probe-ws/canary.txt`
   Make sure `~/.claude/settings.json` has no `permissions` block.
2. Open `~/probe-ws` as a single-root VS Code workspace. Select the Claude Code provider (Claude Sonnet 5), Act mode. New task:
   > Do not use any terminal command. 1) With your file-reading tool, read /Users/me/probe-out/hello.txt and quote it. 2) With your file-writing tool, create /Users/me/probe-out/written.txt containing the word written. Quote any error verbatim.

   **Observed:** both refused (*"…haven't granted it yet"*), no prompt shown, nothing written.
3. New task:
   > Using your terminal tool, run exactly: python3 -c "print(42)". Quote any output or error verbatim.

   **Observed:** *"This command requires approval"*, no prompt shown.
4. Add `~/probe-ws/.claude/settings.json`:
```json
   {
     "permissions": {
       "additionalDirectories": ["/Users/me/probe-out"],
       "allow": ["Bash(python3:*)", "Bash(python3 *)", "Read(//Users/me/probe-out/**)", "Edit(//Users/me/probe-out/**)", "Write(//Users/me/probe-out/**)"],
       "deny": ["Read(./canary.txt)"]
     }
   }
```
   Reload the window; new task; ask it to read `~/probe-ws/canary.txt`, then repeat steps 2 and 3.
   **Observed:** the canary read is refused by the deny rule (so the file is loaded); the outside read/write and the `python3` command are still refused.
5. Move the same `allow` / `additionalDirectories` entries into `~/.claude/settings.json` (back it up first). Reload; new task; repeat steps 2 and 3.
   **Observed:** the outside read and write succeed (the file appears on disk), and `python3` prints `42`. With a project MCP server configured, an allow rule for one of its tools in the user file also makes that tool callable, where before it was refused the same way.

### Provider/Model

Claude Code provider (Claude subscription, not API key) — Claude Sonnet 5

### Diagnostics

- Cline 4.1.21 (VS Code extension); 4.1.22 also installed, bundle checked
- Claude Code CLI: 2.1.263
- Mode: Act
- Cline auto-approve: enabled — no effect on these refusals (they happen inside the Claude Code session)

For completeness, in case it helps with the disclosure work from #13277: under this provider on 4.1.21 the lifecycle hooks `TaskStart`, `UserPromptSubmit` and `TaskComplete` do fire (tool hooks do not, as documented there).

### System Information

ProductName:	macOS
ProductVersion:	26.6.2 (arm64), Apple Silicon
BuildVersion:		25G83


<!-- Original issue body ends. Comments below retain exact publisher body strings. -->


## Comment 5907911712 — linear-code

Source: https://github.com/cline/cline/issues/14699#issuecomment-5907911712

Created: 2026-09-30T09:06:08Z

Last edited: not supplied


<!-- linear-linkback -->
<p><a href="https://linear.app/cline-bot/issue/CLINE-3489">CLINE-3489</a></p>

## Comment 5908564974 — Dante-dan

Source: https://github.com/cline/cline/issues/14699#issuecomment-5908564974

Created: 2026-09-30T09:41:57Z

Last edited: not supplied


I checked the current Claude Code launcher in [`community.ts`](<https://github.com/cline/cline/blob/3435f72fcf4cb843bee946b8f9e981683564c9e3/sdk/packages/llms/src/providers/vendors/community.ts>): it already defaults `settingSources` to `['user', 'project']` and `permissionMode` to `acceptEdits`, and explicitly notes that there is no `canUseTool` callback. I don't see Cline-specific project/user permission precedence handling in that wrapper. `buildGatewayProviderOptions()` in `sdk/packages/core/src/services/llms/handler-factory.ts` forwards the workspace `cwd` for this provider, while the VS Code provider settings UI exposes the CLI path and reasoning controls, rather than these permission options. Your project-level deny control is therefore important: this is not simply the old missing-`settingSources` problem from #13146.

I'd propose a bounded first pass along your launch-options alternative:

- Isolate the reported project-versus-user `allow` / `additionalDirectories` behavior with the CLI/SDK version used by the provider, retaining the project deny canary. Establish whether the discrepancy comes from Cline's launch configuration or the underlying CLI before changing configuration semantics.
- If correcting the launcher does not resolve it, expose explicit, provider-scoped `allowedTools` and `additionalDirectories` through the existing `defaultSettings` path, so a Cline session can receive those grants without changing the user's global Claude settings. Keep the current defaults and explicit deny rules; do not enable bypass permissions or automatically grant terminal/MCP access.
- Document that this scoped route supplies explicit launch-time permissions and does not add interactive approval prompts. Verify in-workspace edits, the reported Bash/outside-directory cases, and deny behavior, by extending the existing launch-default/override tests in `sdk/packages/llms/src/providers/gateway.test.ts` and checking the corresponding CLI reproduction.

I would keep a `canUseTool` approval bridge and changes to plan-mode/`permissionMode` behavior outside this first scope. #13301 rejected a permanent provider-specific tool-hook bridge; I would not reintroduce that bridge as a workaround here. I haven't reproduced the reported CLI behavior yet, so the source inspection above is evidence about the launch path, not a confirmed explanation for project allow rules being ignored.

Would a core maintainer approve this scope, or prefer the refusal/disclosure-only alternative from the issue?


## Comment 5908809623 — csimonb

Source: https://github.com/cline/cline/issues/14699#issuecomment-5908809623

Created: 2026-09-30T09:55:36Z

Last edited: not supplied


Thanks — this was the right first question, and it has a clear answer: it isn't Cline's launch configuration. Claude Code itself ignores project `allow` entries in a folder that hasn't been trusted.

Run directly (Claude Code 2.1.263, `claude -p --permission-mode acceptEdits`) in a fresh folder with the same project settings, the CLI prints:

> Ignoring 2 permissions.allow entries from .claude/settings.json: this workspace has not been trusted. Run Claude Code interactively here once and accept the trust dialog, or set projects["<path>"].hasTrustDialogAccepted: true in ~/.claude.json.

It then refuses `python3 -c "print(42)"`, while the project deny canary still applies. After running `claude` interactively in that folder once and accepting the trust dialog, the same direct run prints `42` (canary still denied).

Through Cline (4.1.21, Claude Code provider, Sonnet 5, VS Code open on that trusted folder, window reloaded): the project allow rule runs `python3`, and project `additionalDirectories` + `Read`/`Edit`/`Write` rules open a folder outside the workspace for reading and writing. So project-level grants work through Cline once the folder is trusted — and they are already scoped to that one folder, without touching global settings.

What made this hard to find is that Cline never shows that CLI warning: inside Cline the only symptom is a bare "requires approval" / "haven't granted it yet". So I'd suggest the first pass could be smaller than proposed:

1. **Surface the CLI's warnings** (at least the "Ignoring … not been trusted" line) in the Cline UI, and/or say in the provider docs that project permission rules apply only in trusted folders.
2. Provider-scoped `allowedTools` / `additionalDirectories` via `defaultSettings`, as you proposed, would still be useful for people who can't or won't trust a folder — but it's no longer needed to avoid touching global settings.

Agree on leaving the `canUseTool` bridge and plan-mode out of this scope.

## Comment 5915249153 — chenhz01

Source: https://github.com/cline/cline/issues/14699#issuecomment-5915249153

Created: 2026-09-30T16:19:23Z

Last edited: not supplied


Nice discriminator analysis. The silent refusals make sense once you see the scope split: project `.claude/settings.json` allow rules are ignored while the same rule in `~/.claude/settings.json` works — a precedence bug in how the permission set is merged. Here's a small auditor that checks a command against BOTH scopes and tells you which one actually grants it, handy for reproducing exactly which rules get dropped:

```python
# cline_perm_scope_auditor.py
# For #14699: checks a command against BOTH project (.claude/settings.json)
# and user (~/.claude/settings.json) permission scopes to expose the
# project-vs-user allow discrepancy behind the silent refusals.

import json, os

def load(path):
    try:
        with open(path) as f:
            return json.load(f)
    except OSError:
        return {}

def resolve(cmd, project_cfg, user_cfg):
    p_allow = project_cfg.get("permissions", {}).get("allow", [])
    u_allow = user_cfg.get("permissions", {}).get("allow", [])
    p_deny  = project_cfg.get("permissions", {}).get("deny", [])
    if any(cmd in d for d in p_deny):
        return "DENIED (project deny)"
    if any(cmd in a for a in p_allow):
        return "ALLOWED via project allow"
    if any(cmd in a for a in u_allow):
        return "ALLOWED via USER allow (project allow ignored this cmd)"
    return "REQUIRES APPROVAL (no matching allow in either scope)"

# --- point at your real config files (no secrets needed) ---
proj = load(os.path.expanduser("YOUR_PROJECT/.claude/settings.json"))
user = load(os.path.expanduser("~/.claude/settings.json"))
for cmd in ["Bash(python3:*)", "mcp__svc__research_config"]:
    print(cmd, "->", resolve(cmd, proj, user))
```

## Comment 5915641306 — Dante-dan

Source: https://github.com/cline/cline/issues/14699#issuecomment-5915641306

Created: 2026-09-30T16:44:03Z

Last edited: not supplied


Thanks for sharing the auditor. I would narrow the confirmed behavior to Claude Code’s workspace trust gate: the earlier direct CLI run ignored project `allow` entries before the folder was trusted, then accepted them after trust. That points away from Cline’s launcher, but does not by itself establish a permission-merge precedence bug.

The snippet is a useful illustration, though substring matching does not reproduce Claude Code’s permission matcher, and it checks project `deny` only. I would not use it to make real allow/deny decisions; the direct CLI before-and-after trust test is the stronger discriminator.

## Comment 5936917240 — samvallad33

Source: https://github.com/cline/cline/issues/14699#issuecomment-5936917240

Created: 2026-10-01T17:33:29Z

Last edited: not supplied



csimonb's CLI run surfacing the buried warning — "Ignoring 2 permissions.allow entries ... this workspace has not been trusted" — reframed this bug for me: the refusal had a reason the whole time, Cline just never showed it. Surfacing that line is the right first pass. The half I'd add: a hook that fires before Cline's approval policies (so even YOLO/bypass configurations are covered), where every verdict writes a hash-chained receipt naming the rule that fired and the command it saw. "Requires approval with no prompt" then becomes "refused by rule N, here is the receipt" — something you can grep instead of guessing at settings files. Honest limit: it's deterministic, so it stops known effect classes; novel ones it logs without blocking. Port: https://github.com/samvallad33/vestige/tree/main/operator-lite/ports/cline

(Disclosure: I work on Vestige, open-source; the hook above is our port.)


</details>
