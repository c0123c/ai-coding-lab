---
vendor: "GitHub / Microsoft"
title: "use-hooks-dcd0a0477f"
official_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/use-hooks.md"
status: "readable_copy_of_existing_archive"
archive_evidence: "01_资料来源/官方资料/审计记录/扩展厂商正文_2026-10-09/GitHub-Copilot-pages.jsonl.gz"
archive_line: 76
archive_body_sha256: "03088fa4d7607286da91ab971d6124ce54c3945c977f980fabb3a2ca6cfead74"
archive_body_chars: 1345
original_fetched_at: ""
---

# 旧归档可读副本

此页从原有压缩归档按行提取，未重新抓取官网；下方为未经改写的正文。

# 官方正文

---
title: Customize agent workflows with hooks
shortTitle: Use hooks
intro: 'Run automated checks—like linting, formatting, or security scans—at key points during agent execution to enforce quality standards.'
versions:
  feature: copilot
contentType: how-tos
category:
  - Configure Copilot
redirect_from:
  - /copilot/how-tos/use-copilot-agents/coding-agent/use-hooks
  - /copilot/how-tos/use-copilot-agents/cloud-agent/use-hooks
---

{% data reusables.copilot.cloud-agent.hooks-intro %}

## Creating a hook in a repository on {% data variables.product.github %}

1. Create a new `NAME.json` file (where `NAME` describes the purpose of the file) in the `.github/hooks/` folder of your repository.

   > [!IMPORTANT]
   > The hooks configuration file **must be present** on your repository's default branch to be used by {% data variables.copilot.copilot_cloud_agent %}.

{% data reusables.copilot.cloud-agent.create-hooks-instructions %}

## Troubleshooting

{% data reusables.copilot.cloud-agent.troubleshoot-hooks %}

## Further reading

* [AUTOTITLE](/copilot/reference/hooks-reference)
* [AUTOTITLE](/copilot/concepts/copilot-surfaces/copilot-on-github)
* [AUTOTITLE](/copilot/concepts/copilot-surfaces/copilot-cli)
* [AUTOTITLE](/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment)
