---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/modal/application_managed/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/modal/application_managed/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/modal/application_managed/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/modal/application_managed/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "3b23eccfd140a756564561b87f4a562394dcd2f85f4c822767e74948d1e2986d"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:37"
---

# Application-managed Modal sandbox

The application builds an image remotely, starts a Modal sandbox, connects the
executor, runs the agent against `sample_report.txt`, and cleans up both resources.

## Run

Set `OPENAI_API_KEY` and a separate restricted `OPENAI_EXECUTOR_API_KEY`. Use keys
with the same owner, organization, and project. From the Cookbook repository root:

```bash
uv tool run --from 'modal[api-proxy-support]>=1.3.4,<2' modal setup
uv run examples/agents_api/sandboxes/modal/application_managed/main.py
```

The Modal SDK uses the active profile in `~/.modal.toml`; `MODAL_TOKEN_ID` and
`MODAL_TOKEN_SECRET` override it when set. The sandbox has a 15-minute maximum
lifetime and no inbound port: `codex exec-server` connects outbound to OpenAI.

For a Modal-hosted handler that provisions compute from OpenAI webhooks, use the
[webhook-managed Modal example](../webhook_managed/README.md).
