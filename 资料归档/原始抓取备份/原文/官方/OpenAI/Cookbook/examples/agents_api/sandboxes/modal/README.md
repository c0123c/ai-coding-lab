---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/modal/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/modal/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/modal/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/modal/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "69230849c9edd486a8080f4c31441ea753ffc495677ef00a5639adaae0a1363a"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:36"
---

# Modal sandboxes

Choose who provisions and cleans up the sandbox:

- [Application-managed](application_managed/README.md): run a report task with a
  sample file, then terminate the sandbox and delete the Agents API session.
- [Webhook-managed](webhook_managed/README.md): deploy a signed webhook receiver
  and reconnect sessions through named Modal sandboxes.

Both modes use [modal_executor.py](modal_executor.py) for the base image and
executor command. The application adds its report tools and sample file on top;
the webhook controller packages the shared Python source in its deployment.
Only the restricted executor key reaches the sandbox.
