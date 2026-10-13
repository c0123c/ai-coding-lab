---
vendor: "GitHub"
repository: "github/docs"
path: "content/copilot/concepts/security-governance-and-network-settings/content-exclusion.md"
source_url: "https://github.com/github/docs/blob/main/content/copilot/concepts/security-governance-and-network-settings/content-exclusion.md"
raw_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/concepts/security-governance-and-network-settings/content-exclusion.md"
resolved_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/concepts/security-governance-and-network-settings/content-exclusion.md"
tree_snapshot_sha: "be38ec5d78e24172587e61b3c6ff40ace1865c71"
sha256_body: "511bd9d37d96fa3eccb5e4c894f99d11399c593dd6ca56a5452aecf5bd0b6c26"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0004.jsonl.gz:42"
---

---
title: Content exclusion for GitHub Copilot
shortTitle: Content exclusion
intro: You can prevent {% data variables.product.prodname_copilot_short %} from accessing certain files.
product: '{% data reusables.gated-features.copilot-business-and-enterprise %}'
versions:
  feature: copilot
redirect_from:
  - /copilot/concepts/content-exclusion-for-github-copilot
  - /copilot/concepts/content-exclusion
  - /copilot/concepts/context/content-exclusion
contentType: concepts
category:
  - Manage Copilot for a team
---

## About content exclusion for {% data variables.product.prodname_copilot_short %}

You can use content exclusion to configure {% data variables.product.prodname_copilot_short %} to ignore certain files. When you exclude content from {% data variables.product.prodname_copilot_short %}:

* Inline suggestions will not be available in the affected files.
* The content in affected files will not inform inline suggestions in other files.
* The content in affected files will not inform {% data variables.product.prodname_copilot_short %}'s responses.
* Affected files will not be reviewed in a {% data variables.product.prodname_copilot_short %} code review.

### Who can configure content exclusion

Repository administrators, organization owners, and enterprise owners can configure content exclusion.

{% data reusables.copilot.content-exclusions-scope %}

### Availability of content exclusion

The {% data variables.copilot.copilot_business_short %} and {% data variables.copilot.copilot_enterprise_short %} plans provide the following level of support for content exclusion.

| Tool                                            | Inline suggestions support                    | Chat and agent support                         |
|-------------------------------------------------|:---------------------------------------------:|:----------------------------------------------:|
| {% data variables.product.prodname_vs %}        | {% octicon "check" aria-label="Supported" %}  | {% octicon "check" aria-label="Supported" %}   |
| {% data variables.product.prodname_vscode %}    | {% octicon "check" aria-label="Supported" %}  | Chat: {% octicon "check" aria-label="Supported" %}<br>Edit: {% octicon "x" aria-label="Not supported" %}<br>Agent: {% octicon "x" aria-label="Not supported" %} |
| JetBrains IDEs                                  | {% octicon "check" aria-label="Supported" %}  | {% octicon "check" aria-label="Supported" %}   |
| Vim/Neovim                                      | {% octicon "check" aria-label="Supported" %}  | Not applicable                                 |
| Xcode                                           | {% octicon "check" aria-label="Supported" %}  | {% octicon "x" aria-label="Not supported" %}   |
| Eclipse                                         | {% octicon "check" aria-label="Supported" %}  | {% octicon "x" aria-label="Not supported" %}   |
| Azure Data Studio                               | {% octicon "x" aria-label="Not supported" %}  | Not applicable                                 |
| The {% data variables.product.github %} website | Not applicable                                | {% octicon "check" aria-label="Supported" %}   |
| {% data variables.product.prodname_mobile %}    | Not applicable                                | {% octicon "check" aria-label="Supported" %}   |
| {% data variables.copilot.github_copilot_app %} | Not applicable                                | {% octicon "check" aria-label="Supported" %}   |
| {% data variables.copilot.copilot_cli %}        | Not applicable                                | {% octicon "check" aria-label="Supported" %}   |

Content exclusions also apply to {% data variables.copilot.copilot_code-review_short %} on the {% data variables.product.github %} website.

> [!NOTE]
> * Content exclusion is in {% data variables.release-phases.public_preview %} on the {% data variables.product.github %} website and in {% data variables.product.prodname_mobile %} and is subject to change.
> * Content exclusion is currently not supported in Edit and Agent modes of {% data variables.copilot.copilot_chat_short %} in {% data variables.product.prodname_vscode %} and other editors.

### Limitations of content exclusion

{% data reusables.copilot.content-exclusion-limitations %}

Currently, content exclusions do not apply to symbolic links (symlinks) and repositories located on remote filesystems.

### Data sent to {% data variables.product.prodname_dotcom %}

After you configure content exclusion, the client (for example, the {% data variables.product.prodname_copilot_short %} extension for {% data variables.product.prodname_vscode_shortname %}) sends the current repository URL to the {% data variables.product.prodname_dotcom %} server so that the server can return the correct policy to the client. These URLs are not logged anywhere.

## Further reading

* [AUTOTITLE](/copilot/how-tos/configure-content-exclusion/exclude-content-from-copilot)
* [AUTOTITLE](/copilot/how-tos/configure-content-exclusion/review-changes)
