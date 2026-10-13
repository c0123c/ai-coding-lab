---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "b00b3c8b3829e0e823f4bfea11671bab126db41e5f57c9cea752d11f5f7abe16"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:16"
---

# Self-hosted sandbox examples

Choose how your application starts sandbox compute. Both modes run the agent in
OpenAI's cloud and connect the sandbox through `codex exec-server`.

| Mode | Your application | Sandbox provisioning |
| --- | --- | --- |
| Application-managed | Run the provider's `application_managed/main.py`. | Your application starts and stops compute directly; no webhook handler. |
| [Webhook-managed](webhook_managed.md) | Run the provider's webhook-managed example to send input and follow the turn. | A separately deployed handler starts or reconnects compute when OpenAI sends a webhook. |

Choose one provisioning mode per session. Deleting an API session does not stop
provider compute; each example documents cleanup of both.

## Choose a provider

| Provider | Application-managed | Webhook-managed |
| --- | --- | --- |
| [Blaxel](blaxel/README.md) | [Run](blaxel/application_managed/README.md) | [Deploy](blaxel/webhook_managed/README.md) |
| [Cloudflare](cloudflare/README.md) | [Run](cloudflare/application_managed/README.md) | [Deploy](cloudflare/webhook_managed/README.md) |
| [Daytona](daytona/README.md) | [Run](daytona/application_managed/README.md) | [Deploy](daytona/webhook_managed/README.md) |
| [DigitalOcean](digitalocean/README.md) | [Run](digitalocean/application_managed/README.md) | [Deploy](digitalocean/webhook_managed/README.md) |
| [Docker](docker/README.md) | [Run](docker/application_managed/README.md) | Not included |
| [E2B](e2b/README.md) | [Run](e2b/application_managed/README.md) | [Deploy](e2b/webhook_managed/README.md) |
| [Modal](modal/README.md) | [Run](modal/application_managed/README.md) | [Deploy](modal/webhook_managed/README.md) |
| [AWS Lambda MicroVMs](aws/README.md) | [Run](aws/application_managed/README.md) | [Deploy](aws/webhook_managed/README.md) |
| [OCI](oci/README.md) | [Run](oci/application_managed/README.md) | Not included |
| [Runloop](runloop/README.md) | [Run](runloop/application_managed/README.md) | [Deploy](runloop/webhook_managed/README.md) |
| [Vercel](vercel/README.md) | [Run](vercel/application_managed/README.md) | [Deploy](vercel/webhook_managed/README.md) |

Each provider folder contains its examples and shared sandbox setup. Webhook
examples include their own client; they do not import code from another provider.
Run commands from the Cookbook repository root unless the provider README says otherwise.
