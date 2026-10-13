---
vendor: "OpenAI"
repository: "openai/skills"
path: "skills/.system/skill-creator/references/openai_yaml.md"
source_url: "https://github.com/openai/skills/blob/main/skills/.system/skill-creator/references/openai_yaml.md"
raw_url: "https://raw.githubusercontent.com/openai/skills/main/skills/.system/skill-creator/references/openai_yaml.md"
resolved_url: "https://raw.githubusercontent.com/openai/skills/main/skills/.system/skill-creator/references/openai_yaml.md"
tree_snapshot_sha: "49f948faa9258a0c61caceaf225e179651397431"
sha256_body: "9fb5928b7f4a746fdb39df503ebbd7a659d5e64b47f85a60626aa1a03fd76b17"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0009.jsonl.gz:19"
---

# openai.yaml fields (full example + descriptions)

`agents/openai.yaml` is an extended, product-specific config intended for the machine/harness to read, not the agent. Other product-specific config can also live in the `agents/` folder.

## Full example

```yaml
interface:
  display_name: "Optional user-facing name"
  short_description: "Optional user-facing description"
  icon_small: "./assets/small-400px.png"
  icon_large: "./assets/large-logo.svg"
  brand_color: "#3B82F6"
  default_prompt: "Optional surrounding prompt to use the skill with"

dependencies:
  tools:
    - type: "mcp"
      value: "github"
      description: "GitHub MCP server"
      transport: "streamable_http"
      url: "https://api.githubcopilot.com/mcp/"
```

## Field descriptions and constraints

Top-level constraints:

- Quote all string values.
- Keep keys unquoted.
- For `interface.default_prompt`: generate a helpful, short (typically 1 sentence) example starting prompt based on the skill. It must explicitly mention the skill as `$skill-name` (e.g., "Use $skill-name-here to draft a concise weekly status update.").

- `interface.display_name`: Human-facing title shown in UI skill lists and chips.
- `interface.short_description`: Human-facing short UI blurb (25–64 chars) for quick scanning.
- `interface.icon_small`: Path to a small icon asset (relative to skill dir). Default to `./assets/` and place icons in the skill's `assets/` folder.
- `interface.icon_large`: Path to a larger logo asset (relative to skill dir). Default to `./assets/` and place icons in the skill's `assets/` folder.
- `interface.brand_color`: Hex color used for UI accents (e.g., badges).
- `interface.default_prompt`: Default prompt snippet inserted when invoking the skill.
- `dependencies.tools[].type`: Dependency category. Only `mcp` is supported for now.
- `dependencies.tools[].value`: Identifier of the tool or dependency.
- `dependencies.tools[].description`: Human-readable explanation of the dependency.
- `dependencies.tools[].transport`: Connection type when `type` is `mcp`.
- `dependencies.tools[].url`: MCP server URL when `type` is `mcp`.
