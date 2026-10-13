---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/docker/application_managed/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/docker/application_managed/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/docker/application_managed/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/docker/application_managed/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "78b2af39478d385da6bdacd53fb383a555a4906c5c8d8f0b7b57aad7c7c260ca"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:32"
---

# Application-managed Docker sandbox

The application builds a local image, starts the executor, runs the agent against
`sample_report.txt`, and cleans up the container and API session.

## Run

Start Docker and set `OPENAI_API_KEY` and a separate restricted
`OPENAI_EXECUTOR_API_KEY`. Use keys with the same owner, organization, and project.
From the Cookbook repository root:

```bash
uv run examples/agents_api/sandboxes/docker/application_managed/main.py
```

The [Dockerfile](Dockerfile) installs Codex and copies the sample into `/workspace`.
This is a local development example, not a hardened multi-tenant sandbox.

To manage cloud compute through OpenAI webhooks, use the
[webhook-managed examples](../../webhook_managed.md).
