# 图像生成技能的备用 CLI 参考

说明脚本生成、编辑和批处理的参数、输出、质量及遮罩设置；属于图像工具的备用调用资料。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-04-15](<https://github.com/openai/skills/commit/e6afb0d74cc75d220df2faf3dd6c635c2dc6a108>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.system/imagegen/references/cli.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/图像生成技能的备用 CLI 参考.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# CLI 参考 (`scripts/image_gen.py`)

此文件仅适用于后备 CLI 模式。仅在用户明确要求使用 `scripts/image_gen.py` 而不是内置 `image_gen` 工具后才阅读。

`generate-batch` 是此后备路径中的 CLI 子命令。这并不是技能的顶级模式。

## 这个 CLI 的作用是什么
- `generate`：根据提示生成新图像
- `edit`：编辑一张或多张现有图像
- `generate-batch`：从 JSONL 文件运行许多生成作业

真正的API呼叫需要**网络访问** + `OPENAI_API_KEY`。 `--dry-run` 没有。

## 快速启动（适用于任何存储库）
设置技能CLI的稳定路径（默认`CODEX_HOME`为`~/.codex`）：

```
export CODEX_HOME="${CODEX_HOME:-$HOME/.codex}"
export IMAGE_GEN="$CODEX_HOME/skills/.system/imagegen/scripts/image_gen.py"
```

使用包管理器将依赖项安装到该环境中。在 uv 管理的环境中，`uv pip install ...` 仍然是首选路径。

## 快速启动

试运行（无 API 调用；无需网络；不需要 `openai` 包）：

```bash
python "$IMAGE_GEN" generate \
  --prompt "Test" \
  --out output/imagegen/test.png \
  --dry-run
```

注意事项：
- 一次性试运行打印 API 有效负载和计算出的输出路径。
- 回购本地决赛应在 `output/imagegen/` 下进行。

生成（需要`OPENAI_API_KEY` + 网络）：

```bash
python "$IMAGE_GEN" generate \
  --prompt "A cozy alpine cabin at dawn" \
  --size 1024x1024 \
  --out output/imagegen/alpine-cabin.png
```

编辑：

```bash
python "$IMAGE_GEN" edit \
  --image input.png \
  --prompt "Replace only the background with a warm sunset" \
  --out output/imagegen/sunset-edit.png
```

## 护栏
- 激活正确的环境后，直接使用捆绑的CLI（`python "$IMAGE_GEN" ...`）。
- 除非用户明确要求自定义包装器，否则 ** 不 ** 创建一次性运行器（例如 `gen_images.py`）。
- **切勿修改** `scripts/image_gen.py`。如果缺少某些内容，请在执行其他操作之前询问用户。

## 默认值
- 型号：【`gpt-image-1.5`】
- CLI 支持的型号系列： GPT 图像型号 (`gpt-image-*`)
- 尺寸: `1024x1024`
- 质量：`auto`
- 输出格式：`png`
- 默认一次性输出路径：`output/imagegen/output.png`
- 背景：未指定，除非设置了 `--background`

## 质量、输入保真度和遮罩（仅限 CLI 后备）
这些是显式的 CLI 控件。它们不是内置的 `image_gen` 工具参数。

- `--quality` 适用于 `generate`、`edit` 和 `generate-batch`：`low|medium|high|auto`
- `--input-fidelity` 是 ** 仅供编辑 ** 并验证为 `low|high`
- `--mask` 是 ** 仅供编辑 **

示例：

```bash
python "$IMAGE_GEN" edit \
  --image input.png \
  --prompt "Change only the background" \
  --quality high \
  --input-fidelity high \
  --out output/imagegen/background-edit.png
```

面膜注意事项：
- 对于多图像编辑，请传递重复的 `--image` 标志。它们的顺序是有意义的，因此请在提示中按索引和角色描述每个图像。
- CLI 接受单个 `--mask`。
- 尽可能使用 PNG 面罩；该脚本将掩码处理视为尽力而为，并且不执行文件之外的完整预检验证 checks/warnings.
- 在编辑提示中，重复不变量 (`change only the background; keep the subject unchanged`) 以减少漂移。

## 输出处理
- 使用 `tmp/imagegen/` 作为临时 JSONL 输入或临时文件。
- 使用 `output/imagegen/` 作为最终输出。
- 如果目标文件已存在，重新运行将失败，除非您传递 `--force`。
- `--out-dir` 将一次性命名更改为 `image_1.<ext>`、`image_2.<ext>` 等。
- 缩小的副本使用默认后缀 `-web`，除非您覆盖它。

## 常见食谱

使用增强字段生成：

```bash
python "$IMAGE_GEN" generate \
  --prompt "A minimal hero image of a ceramic coffee mug" \
  --use-case "product-mockup" \
  --style "clean product photography" \
  --composition "wide product shot with usable negative space for page copy" \
  --constraints "no logos, no text" \
  --out output/imagegen/mug-hero.png
```

生成+还编写一个缩小的副本以实现快速网页加载：

```bash
python "$IMAGE_GEN" generate \
  --prompt "A cozy alpine cabin at dawn" \
  --size 1024x1024 \
  --downscale-max-dim 1024 \
  --out output/imagegen/alpine-cabin.png
```

同时生成多个提示（异步批处理）：

```bash
mkdir -p tmp/imagegen output/imagegen/batch
cat > tmp/imagegen/prompts.jsonl << 'EOF'
{"prompt":"Cavernous hangar interior with a compact shuttle parked near the center","use_case":"stylized-concept","composition":"wide-angle, low-angle","lighting":"volumetric light rays through drifting fog","constraints":"no logos or trademarks; no watermark","size":"1536x1024"}
{"prompt":"Gray wolf in profile in a snowy forest","use_case":"photorealistic-natural","composition":"eye-level","constraints":"no logos or trademarks; no watermark","size":"1024x1024"}
EOF

python "$IMAGE_GEN" generate-batch \
  --input tmp/imagegen/prompts.jsonl \
  --out-dir output/imagegen/batch \
  --concurrency 5

rm -f tmp/imagegen/prompts.jsonl
```

注意事项：
- `generate-batch` 需要 `--out-dir`。
- 生成批次需要 --out-dir。
- 使用`--concurrency`控制并行度（默认`5`）。
- JSONL 支持按作业覆盖（例如 `size`、`quality`、`background`、`output_format`、`output_compression`、`moderation`、`n`、 `model`、`out` 和提示增强字段）。
- `--n` 为单个提示生成多个变体； `generate-batch` 用于许多不同的提示。
- 在批处理模式下，每个作业 `out` 被视为 `--out-dir` 下的文件名。

## CLI 注释
- 支持的尺寸：`1024x1024`、`1536x1024`、`1024x1536` 或 `auto`。
- 透明背景要求 `output_format` 为 `png` 或 `webp`。
- 支持`--prompt-file`、`--output-compression`、`--moderation`、`--max-attempts`、`--fail-fast`、`--force`和`--no-augment`。
- 此 CLI 适用于 GPT 图像型号。不要假设旧的非 GPT 图像模型行为适用于此处。

## 另请参阅
- API回退CLI模式的参数快速参考：`references/image-api.md`
- 两种顶级模式共享的提示示例：`references/sample-prompts.md`
- Network/sandbox 后备 CLI 模式注意事项：`references/codex-network.md`


## 原文对照

<details>
<summary>展开完整原文</summary>

# CLI reference (`scripts/image_gen.py`)

This file is for the fallback CLI mode only. Read it only after the user explicitly asks to use `scripts/image_gen.py` instead of the built-in `image_gen` tool.

`generate-batch` is a CLI subcommand in this fallback path. It is not a top-level mode of the skill.

## What this CLI does
- `generate`: generate a new image from a prompt
- `edit`: edit one or more existing images
- `generate-batch`: run many generation jobs from a JSONL file

Real API calls require **network access** + `OPENAI_API_KEY`. `--dry-run` does not.

## Quick start (works from any repo)
Set a stable path to the skill CLI (default `CODEX_HOME` is `~/.codex`):

```
export CODEX_HOME="${CODEX_HOME:-$HOME/.codex}"
export IMAGE_GEN="$CODEX_HOME/skills/.system/imagegen/scripts/image_gen.py"
```

Install dependencies into that environment with its package manager. In uv-managed environments, `uv pip install ...` remains the preferred path.

## Quick start

Dry-run (no API call; no network required; does not require the `openai` package):

```bash
python "$IMAGE_GEN" generate \
  --prompt "Test" \
  --out output/imagegen/test.png \
  --dry-run
```

Notes:
- One-off dry-runs print the API payload and the computed output path(s).
- Repo-local finals should live under `output/imagegen/`.

Generate (requires `OPENAI_API_KEY` + network):

```bash
python "$IMAGE_GEN" generate \
  --prompt "A cozy alpine cabin at dawn" \
  --size 1024x1024 \
  --out output/imagegen/alpine-cabin.png
```

Edit:

```bash
python "$IMAGE_GEN" edit \
  --image input.png \
  --prompt "Replace only the background with a warm sunset" \
  --out output/imagegen/sunset-edit.png
```

## Guardrails
- Use the bundled CLI directly (`python "$IMAGE_GEN" ...`) after activating the correct environment.
- Do **not** create one-off runners (for example `gen_images.py`) unless the user explicitly asks for a custom wrapper.
- **Never modify** `scripts/image_gen.py`. If something is missing, ask the user before doing anything else.

## Defaults
- Model: `gpt-image-1.5`
- Supported model family for this CLI: GPT Image models (`gpt-image-*`)
- Size: `1024x1024`
- Quality: `auto`
- Output format: `png`
- Default one-off output path: `output/imagegen/output.png`
- Background: unspecified unless `--background` is set

## Quality, input fidelity, and masks (CLI fallback only)
These are explicit CLI controls. They are not built-in `image_gen` tool arguments.

- `--quality` works for `generate`, `edit`, and `generate-batch`: `low|medium|high|auto`
- `--input-fidelity` is **edit-only** and validated as `low|high`
- `--mask` is **edit-only**

Example:

```bash
python "$IMAGE_GEN" edit \
  --image input.png \
  --prompt "Change only the background" \
  --quality high \
  --input-fidelity high \
  --out output/imagegen/background-edit.png
```

Mask notes:
- For multi-image edits, pass repeated `--image` flags. Their order is meaningful, so describe each image by index and role in the prompt.
- The CLI accepts a single `--mask`.
- Use a PNG mask when possible; the script treats mask handling as best-effort and does not perform full preflight validation beyond file checks/warnings.
- In the edit prompt, repeat invariants (`change only the background; keep the subject unchanged`) to reduce drift.

## Output handling
- Use `tmp/imagegen/` for temporary JSONL inputs or scratch files.
- Use `output/imagegen/` for final outputs.
- Reruns fail if a target file already exists unless you pass `--force`.
- `--out-dir` changes one-off naming to `image_1.<ext>`, `image_2.<ext>`, and so on.
- Downscaled copies use the default suffix `-web` unless you override it.

## Common recipes

Generate with augmentation fields:

```bash
python "$IMAGE_GEN" generate \
  --prompt "A minimal hero image of a ceramic coffee mug" \
  --use-case "product-mockup" \
  --style "clean product photography" \
  --composition "wide product shot with usable negative space for page copy" \
  --constraints "no logos, no text" \
  --out output/imagegen/mug-hero.png
```

Generate + also write a downscaled copy for fast web loading:

```bash
python "$IMAGE_GEN" generate \
  --prompt "A cozy alpine cabin at dawn" \
  --size 1024x1024 \
  --downscale-max-dim 1024 \
  --out output/imagegen/alpine-cabin.png
```

Generate multiple prompts concurrently (async batch):

```bash
mkdir -p tmp/imagegen output/imagegen/batch
cat > tmp/imagegen/prompts.jsonl << 'EOF'
{"prompt":"Cavernous hangar interior with a compact shuttle parked near the center","use_case":"stylized-concept","composition":"wide-angle, low-angle","lighting":"volumetric light rays through drifting fog","constraints":"no logos or trademarks; no watermark","size":"1536x1024"}
{"prompt":"Gray wolf in profile in a snowy forest","use_case":"photorealistic-natural","composition":"eye-level","constraints":"no logos or trademarks; no watermark","size":"1024x1024"}
EOF

python "$IMAGE_GEN" generate-batch \
  --input tmp/imagegen/prompts.jsonl \
  --out-dir output/imagegen/batch \
  --concurrency 5

rm -f tmp/imagegen/prompts.jsonl
```

Notes:
- `generate-batch` requires `--out-dir`.
- generate-batch requires --out-dir.
- Use `--concurrency` to control parallelism (default `5`).
- Per-job overrides are supported in JSONL (for example `size`, `quality`, `background`, `output_format`, `output_compression`, `moderation`, `n`, `model`, `out`, and prompt-augmentation fields).
- `--n` generates multiple variants for a single prompt; `generate-batch` is for many different prompts.
- In batch mode, per-job `out` is treated as a filename under `--out-dir`.

## CLI notes
- Supported sizes: `1024x1024`, `1536x1024`, `1024x1536`, or `auto`.
- Transparent backgrounds require `output_format` to be `png` or `webp`.
- `--prompt-file`, `--output-compression`, `--moderation`, `--max-attempts`, `--fail-fast`, `--force`, and `--no-augment` are supported.
- This CLI is intended for GPT Image models. Do not assume older non-GPT image-model behavior applies here.

## See also
- API parameter quick reference for fallback CLI mode: `references/image-api.md`
- Prompt examples shared across both top-level modes: `references/sample-prompts.md`
- Network/sandbox notes for fallback CLI mode: `references/codex-network.md`


</details>
