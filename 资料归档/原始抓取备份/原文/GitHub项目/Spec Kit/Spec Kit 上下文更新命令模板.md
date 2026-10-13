---
vendor: "GitHub"
repository: "github/spec-kit"
path: "extensions/agent-context/commands/speckit.agent-context.update.md"
source_url: "https://github.com/github/spec-kit/blob/main/extensions/agent-context/commands/speckit.agent-context.update.md"
raw_url: "https://raw.githubusercontent.com/github/spec-kit/main/extensions/agent-context/commands/speckit.agent-context.update.md"
resolved_url: "https://raw.githubusercontent.com/github/spec-kit/main/extensions/agent-context/commands/speckit.agent-context.update.md"
tree_snapshot_sha: "0443760542e7e5269724744a2ac55c4bd386d095"
sha256_body: "215e3df01bafd18e051e31fbb05c5b4986879a3b00127865c62adaec0785ea6d"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0005.jsonl.gz:60"
---

---
description: "Refresh the managed Spec Kit section in coding agent context file(s)"
---

# Update Coding Agent Context

Refresh the managed Spec Kit section inside the active coding agent's context/instruction file (e.g. `CLAUDE.md`, `.github/copilot-instructions.md`, `AGENTS.md`).

## Behavior

The script reads the agent-context extension config at
`.specify/extensions/agent-context/agent-context-config.yml` to discover:

- `context_file` — the path of the coding agent context file to manage.
- `context_files` — optional project-relative paths for multiple coding agent context files. When non-empty, the script updates each listed file and the list takes precedence over `context_file`.
- `context_markers.start` / `.end` — the delimiters surrounding the managed section. Defaults to `<!-- SPECKIT START -->` and `<!-- SPECKIT END -->` when the field is missing.

It then creates, replaces, or appends the managed block so that the section points at the most recent plan path when one can be discovered (any `plan.md` under `specs/`, including nested scoped layouts such as `specs/<scope>/<feature>/plan.md`).

If `context_files` and `context_file` are empty, the command reports nothing to do and exits successfully. Context file paths must stay project-relative; absolute paths, Windows drive paths, backslash separators, and `..` path segments are rejected.

## Execution

- **Bash**: `.specify/extensions/agent-context/scripts/bash/update-agent-context.sh [plan_path]`
- **PowerShell**: `.specify/extensions/agent-context/scripts/powershell/update-agent-context.ps1 [plan_path]`

When `plan_path` is omitted, the script auto-detects the most recently modified `specs/**/plan.md` (searched recursively, so nested scoped layouts are discovered).
