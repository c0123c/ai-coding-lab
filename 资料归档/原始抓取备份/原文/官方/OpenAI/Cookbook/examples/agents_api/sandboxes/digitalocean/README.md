---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/digitalocean/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/digitalocean/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/digitalocean/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/digitalocean/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "bf85f4136843dc2f6dd8c9f28ee51343725ce85a41c6963419bc26e2ef4a38ab"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:29"
---

# DigitalOcean sandboxes

[DigitalOcean Managed Agents](https://docs.digitalocean.com/products/managed-agents/)
is in public preview. These examples use its Harness Runtime and require PyDo
0.41.0 or later with async support:

- [Application-managed](application_managed/README.md): create a sandbox, run a
  task, download its output, and delete both the sandbox and Agents API session.
- [Webhook-managed](webhook_managed/README.md): run a signed webhook controller
  that provisions sandboxes and resumes them on reconnect.

Both modes use [environment.yaml](environment.yaml). The `codex-agentapi`
template starts the executor, and only the restricted executor key is supplied
to the sandbox.
The webhook controller is separate from sandbox compute and can run locally or
on App Platform.
