---
vendor: "GitHub"
repository: "github/docs"
path: "content/copilot/tutorials/optimize-chat-usage.md"
source_url: "https://github.com/github/docs/blob/main/content/copilot/tutorials/optimize-chat-usage.md"
raw_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/tutorials/optimize-chat-usage.md"
resolved_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/tutorials/optimize-chat-usage.md"
tree_snapshot_sha: "be38ec5d78e24172587e61b3c6ff40ace1865c71"
sha256_body: "126374b0376fc5c4c45a63dbd12b108d2d28cc36f74d78b11a464570e74be6bb"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0005.jsonl.gz:44"
---

---
title: Optimize GitHub Copilot Chat context to reduce AI credit usage
shortTitle: Optimize Chat usage
intro: Keep {% data variables.copilot.copilot_chat_short %} responses focused and reduce {% data variables.product.prodname_ai_credits_short %} usage by knowing when to continue a conversation and when to start a new one.
versions:
  feature: copilot
contentType: tutorials
category:
  - Author and optimize with Copilot
redirect_from:
  - /copilot/how-tos/chat-with-copilot/manage-long-conversations
  - /copilot/tutorials/manage-long-conversations
---

## Continue a conversation when the context is still relevant

Continue the same conversation when your next prompt depends on earlier messages. For example:

* You're refining code that {% data variables.product.prodname_copilot_short %} already generated.
* You're debugging the same error across multiple prompts.
* You're iterating on one design, test plan, or implementation approach.

Keeping related prompts together helps {% data variables.product.prodname_copilot_short %} keep the right context and reduces repetition.

## Start a new conversation when you switch tasks

Start a new conversation when your next prompt is about a different problem. For example:

* You finished one feature and are starting another.
* You're moving from coding work to documentation or release work.
* The existing thread contains context that no longer applies.

Starting fresh helps {% data variables.product.prodname_copilot_short %} focus on your new goal.

## Understand how conversation length affects {% data variables.product.prodname_ai_credits_short %} usage

Each prompt in {% data variables.copilot.copilot_chat_short %} is processed with your new message and relevant context, such as conversation history, selected files, and tool results.

Longer threads can require more context to be processed for each new prompt. More processed context can increase token usage, which can increase {% data variables.product.prodname_ai_credits_short %} usage.


## Further reading

* [AUTOTITLE](/copilot/tutorials/optimize-ai-usage)
* [AUTOTITLE](/copilot/get-started/best-practices)
