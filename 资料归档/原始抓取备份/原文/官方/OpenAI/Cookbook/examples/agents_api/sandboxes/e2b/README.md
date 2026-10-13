---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/e2b/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/e2b/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/e2b/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/e2b/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "9f82ef2da70140962d9ff8c80b724a163b0f75050f55f0791e145324948e00cc"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:33"
---

# E2B sandboxes

Run Agents API tasks with `codex exec-server` in E2B sandboxes:

- [Application-managed](application_managed/README.md): create a sandbox, run a task,
  read its output, and clean up the sandbox and session.
- [Webhook-managed](webhook_managed/README.md): deploy a controller that provisions
  workers and resumes paused workers when a session needs a connection.

Both modes use [sandbox.py](sandbox.py) for executor installation and launch.
Workers receive only the restricted executor key; application credentials stay
in the application or webhook controller.
