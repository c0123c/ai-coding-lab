---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/digitalocean/application_managed/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/digitalocean/application_managed/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/digitalocean/application_managed/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/digitalocean/application_managed/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "0b1080145b67cbfa4ff7d0389b54919e3a8b9ffd6c5dc23e33026796a7e3fdfd"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:30"
---

# Application-managed DigitalOcean sandbox

The application creates an Agents API session and starts a DigitalOcean sandbox
using the `codex-agentapi` template. The template runs `codex exec-server`;
OpenAI runs the agent. The application downloads the generated `plan.md`,
destroys the sandbox, and deletes the session.

## Run

You need a sandbox-enabled DigitalOcean account with access to the
`codex-agentapi` template. [DigitalOcean Managed Agents](https://docs.digitalocean.com/products/managed-agents/)
is in public preview. Use [PyDo 0.41.0 or later](https://github.com/digitalocean/pydo/releases)
with async support (`pydo[aio]`).
The DigitalOcean CLI is not required for this flow.

Set `DIGITALOCEAN_TOKEN`, `OPENAI_API_KEY`, and a separate restricted
`OPENAI_EXECUTOR_API_KEY`. The OpenAI keys must have the same owner,
organization, and project. Only the executor key is passed to DigitalOcean.

From the Cookbook repository root:

```bash
uv run examples/agents_api/sandboxes/digitalocean/application_managed/main.py
```

Dependencies are declared inline. The script reads the shared [environment.yaml](../environment.yaml)
manifest and supplies the environment ID and executor key at runtime. Do not
log or save the resolved manifest: it contains the executor key.

The script allows ten minutes for setup and execution, then attempts both
cleanup operations. If creation times out before returning an ID, check
DigitalOcean for `agents-api-<last 12 characters of the printed session ID>`
before retrying.

For follow-up turns, keep both resources until the application is finished.
Do not attach a provisioning webhook handler to these sessions.

## References

- [DigitalOcean sandbox setup example](https://github.com/digitalocean/pydo/tree/main/examples/agents/doc_python_sdk)
- [DigitalOcean Python SDK](https://github.com/digitalocean/pydo)
- [Webhook-managed example](../webhook_managed/README.md)
