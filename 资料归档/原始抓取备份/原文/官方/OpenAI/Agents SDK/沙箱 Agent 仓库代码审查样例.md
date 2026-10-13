---
vendor: "OpenAI"
repository: "openai/openai-agents-python"
path: "examples/sandbox/tutorials/repo_code_review/README.md"
source_url: "https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/tutorials/repo_code_review/README.md"
raw_url: "https://raw.githubusercontent.com/openai/openai-agents-python/main/examples/sandbox/tutorials/repo_code_review/README.md"
resolved_url: "https://raw.githubusercontent.com/openai/openai-agents-python/main/examples/sandbox/tutorials/repo_code_review/README.md"
tree_snapshot_sha: "125efa029b4bfd84238bd2c4fd69c3406f802663"
sha256_body: "417d19b25ba1eb8bfc15b560f3ee49623df1021e268ee81afd008209419ebf7c"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0008.jsonl.gz:11"
---

# Repo code review

## Goal

Review a small public git repository, run its tests, leave line-level review comments in the structured output, and write a patch-oriented review artifact.

## Why this is valuable

This demo shows a coding-agent workflow where the sandbox can inspect a real git worktree, run tests, reason over a diff, and produce review artifacts that a developer can act on. The manifest mounts `pypa/sampleproject` at a pinned ref with `GitRepo(...)`. The review contract is intentionally narrow: one finding should target the CI workflow, and one should target the missing type hints in `src/sample/simple.py`.

## Setup

Run the Unix-local example from the repository root:

```bash
uv run python examples/sandbox/tutorials/repo_code_review/main.py
uv run python examples/sandbox/tutorials/repo_code_review/evals.py
```

This demo exits after the scripted review so the generated artifacts and eval contract stay deterministic.

To run the same review in Docker, build the shared tutorial image once and pass
`--docker`:

```bash
docker build -t sandbox-tutorials:latest -f examples/sandbox/tutorials/Dockerfile .
uv run python examples/sandbox/tutorials/repo_code_review/main.py --docker
uv run python examples/sandbox/tutorials/repo_code_review/evals.py
```

## Expected artifacts

- `output/review.md`
- `output/findings.jsonl`
- Optional `output/fix.patch`

## Demo shape

- Inputs: `pypa/sampleproject` at a pinned git ref, mounted into the workspace as `repo/`.
- Runtime primitives: sandbox-local bash, optional file edits, and a typed `RepoReviewResult` final output.
- Workflow: one sandbox reviewer agent is enough here; there is no handoff because the task is a linear inspect -> test -> patch -> summarize loop.
- Scratch space: the reviewer can use `scratchpad/` for notes or draft diffs, then return the final review object for the wrapper to persist.
- Evals: `evals.py` checks that the two findings stay focused on `uv` in the test workflow and type hints in `src/sample/simple.py`, and that the patch only edits `simple.py`.
