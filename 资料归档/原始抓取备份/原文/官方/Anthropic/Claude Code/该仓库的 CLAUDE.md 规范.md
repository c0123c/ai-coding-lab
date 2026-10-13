---
vendor: "Anthropic"
repository: "anthropics/claude-code"
path: "CLAUDE.md"
source_url: "https://github.com/anthropics/claude-code/blob/main/CLAUDE.md"
raw_url: "https://raw.githubusercontent.com/anthropics/claude-code/main/CLAUDE.md"
resolved_url: "https://raw.githubusercontent.com/anthropics/claude-code/main/CLAUDE.md"
tree_snapshot_sha: "2301018b1f61073c501a8e7a4813ef48c239163b"
sha256_body: "0fafa0a267c1a7320c4d9ccb4acaa27bdb7062e34cb23569f32a7a472ed0c7ac"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0002.jsonl.gz:17"
---

# CLAUDE.md

Guidance for Claude Code and other coding agents working in this repository.

## Security hardening for GitHub Actions

Workflow jobs in this repository that call Claude run with three protections.
Keep them when you add or edit a workflow.

1. **Egress-firewall runner.** The job has `runs-on: ubuntu-24.04-firewall`,
   a GitHub-hosted runner that filters the job's outbound network traffic. Do
   not move a job that calls Claude to another runner.
2. **Network allow list.** `.github/egress-firewall.yaml` lists the hosts
   those jobs may reach, besides any that GitHub's firewall allows by default.
   Keep `mode: enforce`, which is what makes the firewall block the rest. Follow
   that file's header when you add a host.
3. **Auto permission mode.** Every step that runs the Claude Code action
   (`uses: anthropics/claude-code-action`, or a local action with a
   `claude_args` input) passes `--permission-mode auto` in `claude_args`. A tool
   call that needs permission and that the allowed tools do not cover then runs
   only if Claude Code's safety review passes it. Allow only the tools the job
   needs, and keep any `--disallowedTools` list a step has. Use `claude-opus-4-6`
   or a newer model: on an older one Claude Code falls back to its default
   permission mode.

`claude.yml` answers `@claude` mentions. The Claude Code action sets
`--permission-mode acceptEdits` for those, and the `--permission-mode auto` in
the workflow's `claude_args`, which comes after it, replaces it.

`.github/workflows/workflow-hardening.yml` fails when a job that runs the Claude
Code action or mentions `ANTHROPIC_FEDERATION_RULE_ID` breaks protection 1 or 3,
or when the allow list is missing, empty, not `mode: enforce`, or names a host
with `*`. It cannot see a job that calls Claude another way, so check new
workflows by hand too. If a job cannot meet protection 1 or 3, add it with the
reason to the matching exemption table in
`.github/scripts/check_workflow_hardening.py`. A job in `EXEMPT_FROM_AUTO_MODE`
must set no permission mode at all. Do not skip or weaken the check.

Keep each workflow's `permissions:` block minimal, and never print tokens or
environment variables in workflow logs.
