---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/daytona/application_managed/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/daytona/application_managed/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/daytona/application_managed/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/daytona/application_managed/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "574dfe15aeac0e2ba79e15d2ed1aa74078fa2948e62937aeb3ad34db44698f6a"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:27"
---

# Application-managed Daytona sandbox

The application creates an Agents API session and a Daytona sandbox, starts
`codex exec-server`, and asks the agent to turn `brief.txt` into `plan.md`.
It downloads the plan, deletes the sandbox, and deletes the session.

## Run

Set `DAYTONA_API_KEY`, `OPENAI_API_KEY`, and a separate restricted
`OPENAI_EXECUTOR_API_KEY`. The OpenAI keys must have the same owner,
organization, and project. Only the executor key enters the sandbox.

From the Cookbook repository root:

```bash
uv run examples/agents_api/sandboxes/daytona/application_managed/main.py
```

Dependencies are declared inline. The script allows six minutes for setup and
execution and attempts both cleanup operations on failure. The sandbox has a
ten-minute TTL. If creation times out before returning a sandbox, check Daytona
for `agents-api-<last 12 characters of the printed session ID>` before retrying.

For follow-up turns, keep both resources until the application is finished.
Do not attach a provisioning webhook handler to these sessions.

## References

- [Daytona documentation](https://www.daytona.io/docs/)
- [Daytona Python SDK](https://www.daytona.io/docs/en/python-sdk/)
- [Webhook-managed example](../webhook_managed/README.md)
