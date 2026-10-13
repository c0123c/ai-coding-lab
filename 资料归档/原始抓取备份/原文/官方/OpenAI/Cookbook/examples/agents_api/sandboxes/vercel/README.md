---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/vercel/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/vercel/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/vercel/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/vercel/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "cae53c354600939603ceb522002c820150e1ecf924e3bd0aaf5b82cdcd973b8c"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:41"
---

# Vercel sandboxes

Choose who starts and reconnects the executor:

- [Application-managed](application_managed/README.md): a Python application
  creates a non-persistent sandbox, reads the output, and cleans up both resources.
- [Webhook-managed](webhook_managed/README.md): TypeScript functions verify
  webhooks and use Vercel Queues to provision named sandboxes.

The modes use different provider SDKs and keep their implementations separate.
Both run `codex exec-server` with a restricted `OPENAI_EXECUTOR_API_KEY`; the
application/session-read key stays outside the sandbox. The two OpenAI keys must
have the same owner, organization, and project.

Use a dedicated Vercel project with Sandbox access. The webhook mode also needs
Queues access. Test deployments should use a separate project, not overwrite an
existing application. Each mode's README covers credentials, execution limits,
and cleanup.
