---
vendor: "OpenAI"
repository: "openai/openai-cookbook"
path: "examples/agents_api/sandboxes/modal/webhook_managed/README.md"
source_url: "https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/modal/webhook_managed/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/modal/webhook_managed/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-cookbook/main/examples/agents_api/sandboxes/modal/webhook_managed/README.md"
tree_snapshot_sha: "5bd6cb40b6b84b69b2032d810610aa85064102df"
sha256_body: "ad3e1f4b59fc8098c0ca5254cf5bc8e56c1ad889eb9c4d9b3adc02b5836470ae"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:38"
---

# Modal webhook-managed sandbox

The endpoint verifies OpenAI signatures and queues work for a Modal function.
One provisioning worker processes requests in order and reuses named sandboxes.

## Deploy

From the Cookbook repository root:

```bash
uvx modal setup
```

Create the agent using the [shared setup](../../webhook_managed.md#set-up-once)
with this provider's `webhook_managed/client.py`, then create
these secrets in Modal's dashboard:

| Secret name | Variables |
| --- | --- |
| `agents-api-webhook-modal-controller` | `OPENAI_API_KEY`, `OPENAI_AGENT_ID` — agent ID from the shared setup |
| `agents-api-webhook-modal-executor` | `CODEX_API_KEY` — the restricted executor key |
| `agents-api-webhook-modal-signing` | `OPENAI_WEBHOOK_SECRET` |

Use `pending-webhook-registration` for the signing value only while obtaining the
endpoint URL. The handler explicitly rejects this placeholder. Never put real
credentials in source files.

Keep [modal_executor.py](../modal_executor.py) beside the mode directories; the
controller image includes this module for remote imports.

```bash
uv run examples/agents_api/sandboxes/modal/webhook_managed/handler.py
```

Register the printed URL with OpenAI, store the generated signing secret, and run
the deployment command again. Then follow [run and reconnect](../../webhook_managed.md#run-and-reconnect):

```bash
uv run examples/agents_api/sandboxes/modal/webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
uv run examples/agents_api/sandboxes/modal/webhook_managed/client.py --session-id "$SESSION_ID" --input "Run another shell command."
```

When rotating keys, edit the existing secrets and redeploy. With the CLI, use
`uvx modal secret create --force` to replace values; the Python API's
`allow_existing=True` does not update an existing secret.

## Stop or clean up

The sandbox name is the API session ID. Terminate it in the Modal dashboard, or
run this with Modal credentials available:

```python
import asyncio
import os
import modal


async def stop():
    try:
        sandbox = await modal.Sandbox.from_name.aio(
            "agents-api-webhook-modal", os.environ["SESSION_ID"]
        )
    except modal.exception.NotFoundError:
        return
    await sandbox.terminate.aio()


asyncio.run(stop())
```

For final cleanup, also delete the API session. Remove the OpenAI webhook before
stopping the deployed Modal app. Replacement sandboxes start with a fresh filesystem;
the example does not attach a persistent volume.
