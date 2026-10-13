---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/e2b/application_managed/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/e2b/application_managed/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/e2b/application_managed/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/e2b/application_managed/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "fd42bec53aec7094f9d58d345ac693c1c4867b80ab0aabedeb4c9d1e4d3b6441"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:34"
---

# Application-managed E2B sandbox

The application creates an Agents API session and an E2B sandbox, starts
`codex exec-server`, and asks the agent to turn `brief.txt` into `plan.md`.
It prints the plan, kills the sandbox, and deletes the session.

## Run

Set `E2B_API_KEY`, `OPENAI_API_KEY`, and a separate restricted
`OPENAI_EXECUTOR_API_KEY`. The OpenAI keys must have the same owner,
organization, and project. Only the executor key enters the sandbox.

From the Cookbook repository root:

```bash
uv run examples/agents_api/sandboxes/e2b/application_managed/main.py
```

Dependencies are declared inline. The script allows six minutes for setup and
execution and attempts both cleanup operations on failure. The sandbox has a
ten-minute timeout. If creation times out before returning an ID, check E2B for
the `agents-session-id` metadata matching the printed session ID before retrying.

For follow-up turns, keep both resources until the application is finished.
Do not attach a provisioning webhook handler to these sessions.

## References

- [E2B documentation](https://e2b.dev/docs)
- [E2B SDK reference](https://e2b.dev/docs/sdk-reference)
- [Webhook-managed example](../webhook_managed/README.md)
