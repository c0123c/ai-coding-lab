---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/vercel/application_managed/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/vercel/application_managed/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/vercel/application_managed/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/vercel/application_managed/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "e73235afb11ce08e86f78e5bf5b3d15590a366fefd00390a1b1d9997ceacff11"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:42"
---

# Application-managed Vercel sandbox

The application creates an Agents API session and a Vercel sandbox, starts
`codex exec-server`, and asks the agent to turn `brief.txt` into `plan.md`.
It prints the plan, destroys the sandbox, and deletes the session.

## Run

Use the [provider prerequisites](../README.md) and a dedicated Vercel project.
Set `VERCEL_TOKEN`, `VERCEL_TEAM_ID`, `VERCEL_PROJECT_ID`, `OPENAI_API_KEY`, and
a separate restricted `OPENAI_EXECUTOR_API_KEY`. The OpenAI keys must have the
same owner, organization, and project. Only the executor key enters the sandbox.

From the Cookbook repository root:

```bash
uv run examples/agents_api/sandboxes/vercel/application_managed/main.py
```

Dependencies are declared inline. The script allows six minutes for setup and
execution and attempts both cleanup operations on failure. It creates a
non-persistent sandbox with a ten-minute execution limit. If creation times out,
check Vercel for `agents-api-<last 12 characters of the printed session ID>`
before retrying.

For follow-up turns, keep both resources until the application is finished.
Do not attach a provisioning webhook handler to these sessions.

## References

- [Vercel Sandbox documentation](https://vercel.com/docs/sandbox)
- [Vercel Sandbox SDK reference](https://vercel.com/docs/sandbox/sdk-reference)
- [Webhook-managed example](../webhook_managed/README.md)
- [Webhook setup and lifecycle](../../webhook_managed.md#set-up-once)
