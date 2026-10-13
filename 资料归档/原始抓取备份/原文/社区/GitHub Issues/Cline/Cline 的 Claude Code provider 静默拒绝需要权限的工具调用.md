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


I checked the current Claude Code launcher in [`community.ts`](https://github.com/cline/cline/blob/3435f72fcf4cb843bee946b8f9e981683564c9e3/sdk/packages/llms/src/providers/vendors/community.ts): it already defaults `settingSources` to `['user', 'project']` and `permissionMode` to `acceptEdits`, and explicitly notes that there is no `canUseTool` callback. I don't see Cline-specific project/user permission precedence handling in that wrapper. `buildGatewayProviderOptions()` in `sdk/packages/core/src/services/llms/handler-factory.ts` forwards the workspace `cwd` for this provider, while the VS Code provider settings UI exposes the CLI path and reasoning controls, rather than these permission options. Your project-level deny control is therefore important: this is not simply the old missing-`settingSources` problem from #13146.

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

