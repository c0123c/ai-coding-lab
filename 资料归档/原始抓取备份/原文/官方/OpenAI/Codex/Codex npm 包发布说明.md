---
vendor: "OpenAI"
repository: "openai/codex"
path: "codex-cli/scripts/README.md"
source_url: "https://github.com/openai/codex/blob/main/codex-cli/scripts/README.md"
raw_url: "https://raw.githubusercontent.com/openai/codex/main/codex-cli/scripts/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/codex/main/codex-cli/scripts/README.md"
tree_snapshot_sha: "5ef96ab2785f3ecddf279639eff7b1b42c906fa4"
sha256_body: "a95dbb1c8a458e9fef6b746d7ab5457aac4e4eef70467993582d66c53da534c4"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0007.jsonl.gz:51"
---

# npm releases

Use the staging helper in the repo root to generate npm tarballs for a release. For
example, to stage the CLI, responses proxy, and SDK packages for version `0.6.0`:

```bash
./scripts/stage_npm_packages.py \
  --release-version 0.6.0 \
  --package codex \
  --package codex-responses-api-proxy \
  --package codex-sdk
```

This downloads the required native package archive artifacts, hydrates `vendor/` for
each package, and writes tarballs to `dist/npm/`.

When `--package codex` is provided, the staging helper builds the lightweight
`@openai/codex` meta package plus all platform-native `@openai/codex` variants
that are later published under platform-specific dist-tags.

Direct `build_npm_package.py` invocations are still useful for package-specific
debugging, but native packages expect `--vendor-src` to point at a prehydrated
`vendor/` tree. Release packaging should use `scripts/stage_npm_packages.py`
