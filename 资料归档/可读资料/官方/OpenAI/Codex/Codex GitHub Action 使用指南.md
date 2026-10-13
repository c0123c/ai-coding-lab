# Codex GitHub Action 使用指南

介绍在 CI 中运行非交互任务、配置权限和捕获输出，提供工作流示例、准备条件与故障排查。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/github-action>)）；本地抓取：2026-10-09
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/github-action.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex GitHub Action 使用指南.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Codex GitHub 行动

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

使用 Codex GitHub 操作 (`openai/codex-action@v1`) 在 CI/CD 作业中运行 Codex、应用补丁或从 GitHub 操作工作流程发布评论。
该操作安装Codex CLI，当您提供 API 密钥时启动响应 API 代理，并在您指定的权限下运行 `codex exec`。

当您想要执行以下操作时，请采取行动：

- 自动执行 Codex 对拉取请求或发布的反馈，而无需亲自管理 CLI。
- 作为 CI 管道的一部分，Codex 驱动的质量检查的门发生变化。
- 从工作流程文件运行可重复的 Codex 任务（代码审查、发布准备、迁移）。

有关 CI 示例，请参阅 [非交互模式 ](<https://learn.chatgpt.com/docs/non-interactive-mode>) 并探索 [openai/codex-action 存储库 ](<https://github.com/openai/codex-action>) 中的源代码。

## 先决条件

- 将您的 OpenAI 密钥存储为 GitHub 密钥（例如 `OPENAI_API_KEY`）并在工作流程中引用它。
- 在 Linux 或 macOS 运行器上运行作业。对于Windows，设置`safety-strategy: unsafe`。
- 在调用操作之前检查您的代码，以便 Codex 可以读取存储库内容。
- 决定您要运行哪个提示。您可以通过 `prompt` 提供内联文本，或使用 `prompt-file` 指向存储库中提交的文件。

## 工作流程示例

下面的示例工作流程审查新的拉取请求，捕获 Codex 的响应，并将其发布回 PR。

```yaml
name: Codex pull request review
on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  codex:
    runs-on: ubuntu-latest
    permissions:
      contents: read
    outputs:
      final_message: ${{ steps.run_codex.outputs.final-message }}
    steps:
      - uses: actions/checkout@v5
        with:
          ref: refs/pull/${{ github.event.pull_request.number }}/merge
          fetch-depth: 0
          persist-credentials: false

      - name: Run Codex
        id: run_codex
        uses: openai/codex-action@v1
        with:
          openai-api-key: ${{ secrets.OPENAI_API_KEY }}
          prompt-file: .github/codex/prompts/review.md
          output-file: codex-output.md

  post_feedback:
    runs-on: ubuntu-latest
    needs: codex
    if: needs.codex.outputs.final_message != ''
    permissions:
      issues: write
      pull-requests: write
    steps:
      - name: Post Codex feedback
        uses: actions/github-script@v7
        with:
          github-token: ${{ github.token }}
          script: |
            await github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.payload.pull_request.number,
              body: process.env.CODEX_FINAL_MESSAGE,
            });
        env:
          CODEX_FINAL_MESSAGE: ${{ needs.codex.outputs.final_message }}
```

将 `.github/codex/prompts/review.md` 替换为您自己的提示文件，或使用 `prompt` 输入作为内联文本。该示例还将最终的 Codex 消息写入 `codex-output.md` 以供以后检查或工件上传。

## 配置`codex exec`

通过设置映射到 `codex exec` 选项的操作输入来微调 Codex 的运行方式：

- `prompt` 或 `prompt-file`（选择一项）：内联指令或 Markdown 的存储库路径或任务文本。考虑将提示存储在 `.github/codex/prompts/` 中。
- `codex-args`：额外的CLI标志。提供 JSON 数组（例如 `["--ephemeral"]`）或 shell 字符串（`--profile ci`）来配置会话、配置文件或 MCP 设置。
- `model`和`effort`：选择您想要的Codex agent配置；默认值留空。
- `sandbox`：将沙箱模式（`workspace-write`、`read-only`、`danger-full-access`）与运行期间所需的权限Codex相匹配。
- `output-file`：将最终的 Codex 消息保存到磁盘，以便后续步骤可以上传或比较它。
- `codex-version`：固定特定的 CLI 版本。留空以使用最新发布的版本。
- `codex-home`：如果您想跨步骤重复使用配置文件或 MCP 设置，请指向共享的 Codex 主目录。

## 管理权限

Codex 对 GitHub 托管的运行器具有广泛的访问权限，除非您对其进行限制。使用这些输入来控制曝光：

- `safety-strategy`（默认为`drop-sudo`）在运行Codex之前删除`sudo`。这对于工作来说是不可逆转的，并且可以保护内存中的秘密。在 Windows 上，您必须设置 `safety-strategy: unsafe`。
- `unprivileged-user` 将 `safety-strategy: unprivileged-user` 与 `codex-user` 配对，将 Codex 作为特定帐户运行。确保用户可以读取和写入存储库签出（有关所有权修复，请参阅 [`unprivileged-user` 示例 ](<https://github.com/openai/codex-action/blob/main/examples/unprivileged-user.yml>)）。
- `read-only` 阻止 Codex 更改文件或使用网络，但它仍然以提升的权限运行。不要仅依靠 `read-only` 来保护秘密。
- `sandbox` 限制 Codex 本身内的文件系统和网络访问。选择仍能让任务完成的最窄选项。
- `allow-users` 和 `allow-bots` 限制谁可以触发工作流。默认情况下，只有具有写入权限的用户才能运行该操作；明确列出额外的受信任帐户或将该字段留空以实现默认行为。

## 捕获输出

该操作通过 `final-message` 输出发出最后一条 Codex 消息。将其映射到作业输出（如上所示）或在后续步骤中直接处理它。如果您希望从跑步者那里收集完整的成绩单，请将 `output-file` 与上传的工件功能结合起来。当您需要结构化数据时，请将 `--output-schema` 传递到 `codex-args` 以强制执行 JSON 形状。

## 安全检查表

- 限制谁可以启动工作流程。优先选择受信任的事件或明确的批准，而不是允许每个人针对您的存储库运行 Codex。
- 清理来自拉取请求、提交消息或问题正文的提示输入，以避免提示注入。在将 HTML 评论或隐藏文本提供给 Codex 之前，请先查看它。
- 通过将 `safety-strategy` 保留在 `drop-sudo` 上或将 Codex 移至非特权用户来保护您的 `OPENAI_API_KEY`。切勿在多租户运行器上将操作保留在 `unsafe` 模式下。
- 将 Codex 作为作业的最后一步运行，以便后续步骤不会继承任何意外的状态更改。
- 如果您怀疑代理日志或操作输出暴露了秘密材料，请立即轮换密钥。

## 故障排除

- **您设置了提示和提示文件**：删除重复的输入，以便您只提供一个源。
- **responses-api-proxy 未写入服务器信息**：确认 API 密钥存在且有效；仅当您提供 `openai-api-key` 时，代理才会启动。
- **预期 `sudo` 删除，但 `sudo` 成功**：确保没有恢复先前的步骤 `sudo` 并且运行程序 OS 是 Linux 或 macOS。使用新作业重新运行。
- ** `drop-sudo`** 之后出现权限错误：在操作运行之前授予写入访问权限（例如使用 `chmod -R g+rwX "$GITHUB_WORKSPACE"` 或使用非特权用户模式）。
- **未经授权的触发器被阻止**：如果您需要允许默认写入协作者之外的服务帐户，请调整`allow-users`或`allow-bots`输入。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Codex GitHub Action

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Use the Codex GitHub Action (`openai/codex-action@v1`) to run Codex in CI/CD jobs, apply patches, or post reviews from a GitHub Actions workflow.
The action installs the Codex CLI, starts the Responses API proxy when you provide an API key, and runs `codex exec` under the permissions you specify.

Reach for the action when you want to:

- Automate Codex feedback on pull requests or releases without managing the CLI yourself.
- Gate changes on Codex-driven quality checks as part of your CI pipeline.
- Run repeatable Codex tasks (code review, release prep, migrations) from a workflow file.

For a CI example, see [Non-interactive mode](<https://learn.chatgpt.com/docs/non-interactive-mode>) and explore the source in the [openai/codex-action repository](<https://github.com/openai/codex-action>).

## Prerequisites

- Store your OpenAI key as a GitHub secret (for example `OPENAI_API_KEY`) and reference it in the workflow.
- Run the job on a Linux or macOS runner. For Windows, set `safety-strategy: unsafe`.
- Check out your code before invoking the action so Codex can read the repository contents.
- Decide which prompts you want to run. You can provide inline text via `prompt` or point to a file committed in the repo with `prompt-file`.

## Example workflow

The sample workflow below reviews new pull requests, captures Codex's response, and posts it back on the PR.

```yaml
name: Codex pull request review
on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  codex:
    runs-on: ubuntu-latest
    permissions:
      contents: read
    outputs:
      final_message: ${{ steps.run_codex.outputs.final-message }}
    steps:
      - uses: actions/checkout@v5
        with:
          ref: refs/pull/${{ github.event.pull_request.number }}/merge
          fetch-depth: 0
          persist-credentials: false

      - name: Run Codex
        id: run_codex
        uses: openai/codex-action@v1
        with:
          openai-api-key: ${{ secrets.OPENAI_API_KEY }}
          prompt-file: .github/codex/prompts/review.md
          output-file: codex-output.md

  post_feedback:
    runs-on: ubuntu-latest
    needs: codex
    if: needs.codex.outputs.final_message != ''
    permissions:
      issues: write
      pull-requests: write
    steps:
      - name: Post Codex feedback
        uses: actions/github-script@v7
        with:
          github-token: ${{ github.token }}
          script: |
            await github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.payload.pull_request.number,
              body: process.env.CODEX_FINAL_MESSAGE,
            });
        env:
          CODEX_FINAL_MESSAGE: ${{ needs.codex.outputs.final_message }}
```

Replace `.github/codex/prompts/review.md` with your own prompt file or use the `prompt` input for inline text. The example also writes the final Codex message to `codex-output.md` for later inspection or artifact upload.

## Configure `codex exec`

Fine-tune how Codex runs by setting the action inputs that map to `codex exec` options:

- `prompt` or `prompt-file` (choose one): Inline instructions or a repository path to Markdown or text with your task. Consider storing prompts in `.github/codex/prompts/`.
- `codex-args`: Extra CLI flags. Provide a JSON array (for example `["--ephemeral"]`) or a shell string (`--profile ci`) to configure sessions, profiles, or MCP settings.
- `model` and `effort`: Pick the Codex agent configuration you want; leave empty for defaults.
- `sandbox`: Match the sandbox mode (`workspace-write`, `read-only`, `danger-full-access`) to the permissions Codex needs during the run.
- `output-file`: Save the final Codex message to disk so later steps can upload or diff it.
- `codex-version`: Pin a specific CLI release. Leave blank to use the latest published version.
- `codex-home`: Point to a shared Codex home directory if you want to reuse configuration files or MCP setups across steps.

## Manage privileges

Codex has broad access on GitHub-hosted runners unless you restrict it. Use these inputs to control exposure:

- `safety-strategy` (default `drop-sudo`) removes `sudo` before running Codex. This is irreversible for the job and protects secrets in memory. On Windows you must set `safety-strategy: unsafe`.
- `unprivileged-user` pairs `safety-strategy: unprivileged-user` with `codex-user` to run Codex as a specific account. Ensure the user can read and write the repository checkout (see the [`unprivileged-user` example](<https://github.com/openai/codex-action/blob/main/examples/unprivileged-user.yml>) for an ownership fix).
- `read-only` keeps Codex from changing files or using the network, but it still runs with elevated privileges. Don't rely on `read-only` alone to protect secrets.
- `sandbox` limits filesystem and network access within Codex itself. Choose the narrowest option that still lets the task complete.
- `allow-users` and `allow-bots` restrict who can trigger the workflow. By default only users with write access can run the action; list extra trusted accounts explicitly or leave the field empty for the default behavior.

## Capture outputs

The action emits the last Codex message through the `final-message` output. Map it to a job output (as shown above) or handle it directly in later steps. Combine `output-file` with the uploaded artifacts feature if you prefer to collect the full transcript from the runner. When you need structured data, pass `--output-schema` through `codex-args` to enforce a JSON shape.

## Security checklist

- Limit who can start the workflow. Prefer trusted events or explicit approvals instead of allowing everyone to run Codex against your repository.
- Sanitize prompt inputs from pull requests, commit messages, or issue bodies to avoid prompt injection. Review HTML comments or hidden text before feeding it to Codex.
- Protect your `OPENAI_API_KEY` by keeping `safety-strategy` on `drop-sudo` or moving Codex to an unprivileged user. Never leave the action in `unsafe` mode on multi-tenant runners.
- Run Codex as the last step in a job so later steps don't inherit any unexpected state changes.
- Rotate keys immediately if you suspect the proxy logs or action output exposed secret material.

## Troubleshooting

- **You set both prompt and prompt-file**: Remove the duplicate input so you provide exactly one source.
- **responses-api-proxy didn't write server info**: Confirm the API key is present and valid; the proxy starts only when you provide `openai-api-key`.
- **Expected `sudo` removal, but `sudo` succeeded**: Ensure no earlier step restored `sudo` and that the runner OS is Linux or macOS. Re-run with a fresh job.
- **Permission errors after `drop-sudo`**: Grant write access before the action runs (for example with `chmod -R g+rwX "$GITHUB_WORKSPACE"` or by using the unprivileged-user pattern).
- **Unauthorized trigger blocked**: Adjust `allow-users` or `allow-bots` inputs if you need to permit service accounts beyond the default write collaborators.


</details>
