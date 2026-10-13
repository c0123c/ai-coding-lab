---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/daytona/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/daytona/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/daytona/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/daytona/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "dfee589fd7a45f7f72ce3121fefee27fb7cb88663c2bea2b2d1c709a24d0ebc3"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:26"
---

# Daytona sandboxes

Run Agents API tasks with `codex exec-server` in Daytona sandboxes:

- [Application-managed](application_managed/README.md): create a sandbox, run a task,
  read its output, and clean up the sandbox and session.
- [Webhook-managed](webhook_managed/README.md): deploy a controller that provisions
  workers and restarts stopped workers when a session needs a connection.

Both modes use [sandbox.py](sandbox.py) for executor installation and launch.
Workers receive only the restricted executor key; application credentials stay
in the application or webhook controller.
