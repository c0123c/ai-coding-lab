---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/blaxel/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/blaxel/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/blaxel/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/blaxel/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "9702fda010922e0c9d21ef02ec681486d71e50b556717e1fd95d5ba1d53002ba"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:20"
---

# Blaxel sandboxes

Run Agents API tasks with `codex exec-server` in Blaxel sandboxes:

- [Application-managed](application_managed/README.md): create a sandbox, run a task,
  read its output, and clean up the sandbox and session.
- [Webhook-managed](webhook_managed/README.md): deploy a controller that provisions
  workers when a session needs an environment connection.

Both modes use [sandbox.py](sandbox.py) for executor installation and launch.
Workers receive only the restricted executor key; application credentials stay
in the application or webhook controller.
