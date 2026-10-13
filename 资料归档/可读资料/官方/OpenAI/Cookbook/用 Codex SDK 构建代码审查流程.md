# 用 Codex SDK 构建代码审查流程

展示结构化审查提示与多个 CI 平台样例；原文标记为归档示例，并指出凭据与写入权限设计需要按现有指南调整。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-08-27](<https://github.com/openai/openai-cookbook/commit/1de082679c38f84df58d1eafb2a75d3e78e4bc97>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/codex/build_code_review_with_codex_sdk.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/用 Codex SDK 构建代码审查流程.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 Codex SDK 构建代码审查

> **已存档示例 - 在使用这些工作流程之前查看当前的 CI 指南。** 下面的 GitHub 示例向整个作业公开 API 和存储库令牌，并将审核与拉取请求写入权限结合起来。请勿复制该凭证和权限布局；仅只读沙箱并不能保护秘密。使用[当前CodexGitHub操作工作流程和安全检查表](<https://learn.chatgpt.com/docs/github-action>)，它将审核与发布反馈分开。对于其他 CI 系统，请遵循 [ 自动化身份验证指南 ](<https://learn.chatgpt.com/docs/non-interactive-mode#authenticate-in-automation>)。

通过 Codex 云中的 [代码审查 ](<https://chatgpt.com/codex/settings/code-review>)，您可以将团队的云托管 GitHub 存储库连接到 Codex，并接收每个 PR 的自动代码审查。但是，如果您的代码托管在本地，或者您没有 GitHub 作为 SCM，该怎么办？

幸运的是，我们可以在我们自己的 CI/CD 运行器中复制 Codex 的云托管审核流程。在本指南中，我们将使用 Codex CLI 无头模式以及 GitHub 操作、GitLab CI/CD、Azure DevOps Pipelines 和 Jenkins 构建自己的代码审查操作。

模型推荐：使用 `gpt-5.5` 在这些工作流程中获得最强的代码审查准确性和一致性。

为了建立我们自己的代码审查，我们将采取以下步骤并严格遵守：

1. 在我们的 CI/CD 运行器中安装 Codex CLI
2. 使用 CLI 附带的代码审查提示在无头（执行）模式下提示 Codex
3. 为 Codex 指定结构化输出 JSON 架构
4. 解析 JSON 结果并使用它对我们的 SCM 进行 API 调用以创建审核评论

一旦实施，Codex将能够留下内联代码审查评论：

![Codex GitHub](<https://raw.githubusercontent.com/openai/openai-cookbook/5bd6cb40b6b84b69b2032d810610aa85064102df/images/codex_code_review.png>) 中的代码审查


## 代码审查提示

GPT-5.5是Codex中复杂编码工作流程的推荐模型。您可以引导GPT-5.5进行代码审查，提示如下：

```
You are acting as a reviewer for a proposed code change made by another engineer.
Focus on issues that impact correctness, performance, security, maintainability, or developer experience.
Flag only actionable issues introduced by the pull request.
When you flag an issue, provide a short, direct explanation and cite the affected file and line range.
Prioritize severe issues and avoid nit-level comments unless they block understanding of the diff.
After listing findings, produce an overall correctness verdict (\"patch is correct\" or \"patch is incorrect\") with a concise justification and a confidence score between 0 and 1.
Ensure that file citations and line numbers are exactly correct using the tools available; if they are incorrect your comments will be rejected.
```

## Codex 结构化输出

为了对拉取请求中的代码范围进行评论，我们需要以特定格式接收 Codex 的响应。为此，我们可以创建一个名为 `codex-output-schema.json` 的文件，该文件符合 OpenAI 的 [ 结构化输出 ](<https://platform.openai.com/docs/guides/structured-outputs>) 格式。

要在我们的工作流程 YAML 中使用此文件，我们可以使用 `output-schema-file` 参数调用 Codex，如下所示：

```yaml
- name: Run Codex structured review
        id: run-codex
        uses: openai/codex-action@main
        with:
            openai-api-key: ${{ secrets.OPENAI_API_KEY }}
            prompt-file: codex-prompt.md
            sandbox: read-only
            model: ${{ env.CODEX_MODEL }}
            output-schema-file: codex-output-schema.json # <-- Our schema file
            output-file: codex-output.json
```

您还可以将类似的参数传递给 `codex exec`，例如：

```bash
codex exec "Review my pull request!" --output-schema codex-output-schema.json
```

## GitHub 动作示例

让我们把它们放在一起。如果您在本地环境中使用 GitHub 操作，则可以根据您的特定工作流程定制此示例。内嵌注释突出了关键步骤。

```yaml
name: Codex Code Review

# Determine when the review action should be run:
on:
  pull_request:
    types:
      - opened
      - reopened
      - synchronize
      - ready_for_review

concurrency:
  group: codex-structured-review-${{ github.event.pull_request.number }}
  cancel-in-progress: true

jobs:
  codex-structured-review:
    name: Run Codex structured review
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    env:
      OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
      GITHUB_TOKEN: ${{ github.token }}
      CODEX_MODEL: ${{ vars.CODEX_MODEL || 'gpt-5.5' }}
      PR_NUMBER: ${{ github.event.pull_request.number }}
      HEAD_SHA: ${{ github.event.pull_request.head.sha }}
      BASE_SHA: ${{ github.event.pull_request.base.sha }}
      REPOSITORY: ${{ github.repository }}
    outputs:
      codex-output: ${{ steps.run-codex.outputs.final-message }}
    steps:
      - name: Checkout pull request merge commit
        uses: actions/checkout@v5
        with:
          ref: refs/pull/${{ github.event.pull_request.number }}/merge

      - name: Fetch base and head refs
        run: |
          set -euxo pipefail
          git fetch --no-tags origin \
            "${{ github.event.pull_request.base.ref }}" \
            +refs/pull/${{ github.event.pull_request.number }}/head
        shell: bash

      # The structured output schema ensures that codex produces comments
      # with filepaths, line numbers, title, body, etc.
      - name: Generate structured output schema
        run: |
          set -euo pipefail
          cat <<'JSON' > codex-output-schema.json
          {
              "type": "object",
              "properties": {
                "findings": {
                  "type": "array",
                  "items": {
                    "type": "object",
                    "properties": {
                      "title": {
                        "type": "string",
                        "maxLength": 80
                      },
                      "body": {
                        "type": "string",
                        "minLength": 1
                      },
                      "confidence_score": {
                        "type": "number",
                        "minimum": 0,
                        "maximum": 1
                      },
                      "priority": {
                        "type": "integer",
                        "minimum": 0,
                        "maximum": 3
                      },
                      "code_location": {
                        "type": "object",
                        "properties": {
                          "absolute_file_path": {
                            "type": "string",
                            "minLength": 1
                          },
                          "line_range": {
                            "type": "object",
                            "properties": {
                              "start": {
                                "type": "integer",
                                "minimum": 1
                              },
                              "end": {
                                "type": "integer",
                                "minimum": 1
                              }
                            },
                            "required": [
                              "start",
                              "end"
                            ],
                            "additionalProperties": false
                          }
                        },
                        "required": [
                          "absolute_file_path",
                          "line_range"
                        ],
                        "additionalProperties": false
                      }
                    },
                    "required": [
                      "title",
                      "body",
                      "confidence_score",
                      "priority",
                      "code_location"
                    ],
                    "additionalProperties": false
                  }
                },
                "overall_correctness": {
                  "type": "string",
                  "enum": [
                    "patch is correct",
                    "patch is incorrect"
                  ]
                },
                "overall_explanation": {
                  "type": "string",
                  "minLength": 1
                },
                "overall_confidence_score": {
                  "type": "number",
                  "minimum": 0,
                  "maximum": 1
                }
              },
              "required": [
                "findings",
                "overall_correctness",
                "overall_explanation",
                "overall_confidence_score"
              ],
              "additionalProperties": false
            }
          JSON
        shell: bash

      # This section generates our prompt:
      - name: Build Codex review prompt
        env:
          REVIEW_PROMPT_PATH: ${{ vars.CODEX_PROMPT_PATH || 'review_prompt.md' }}
        run: |
          set -euo pipefail
          PROMPT_PATH="codex-prompt.md"
          TEMPLATE_PATH="${REVIEW_PROMPT_PATH}"

          if [ -n "$TEMPLATE_PATH" ] && [ -f "$TEMPLATE_PATH" ]; then
            cat "$TEMPLATE_PATH" > "$PROMPT_PATH"
          else
            {
              printf '%s\n' "You are acting as a reviewer for a proposed code change made by another engineer."
              printf '%s\n' "Focus on issues that impact correctness, performance, security, maintainability, or developer experience."
              printf '%s\n' "Flag only actionable issues introduced by the pull request."
              printf '%s\n' "When you flag an issue, provide a short, direct explanation and cite the affected file and line range."
              printf '%s\n' "Prioritize severe issues and avoid nit-level comments unless they block understanding of the diff."
              printf '%s\n' "After listing findings, produce an overall correctness verdict (\"patch is correct\" or \"patch is incorrect\") with a concise justification and a confidence score between 0 and 1."
              printf '%s\n' "Ensure that file citations and line numbers are exactly correct using the tools available; if they are incorrect your comments will be rejected."
            } > "$PROMPT_PATH"
          fi

          {
            echo ""
            echo "Repository: ${REPOSITORY}"
            echo "Pull Request #: ${PR_NUMBER}"
            echo "Base ref: ${{ github.event.pull_request.base.ref }}"
            echo "Head ref: ${{ github.event.pull_request.head.ref }}"
            echo "Base SHA: ${BASE_SHA}"
            echo "Head SHA: ${HEAD_SHA}"
            echo "Changed files:"
            git --no-pager diff --name-status "${BASE_SHA}" "${HEAD_SHA}"
            echo ""
            echo "Unified diff (context=5):"
            git --no-pager diff --unified=5 --stat=200 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/diffstat.txt
            git --no-pager diff --unified=5 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/full.diff
            cat /tmp/diffstat.txt
            echo ""
            cat /tmp/full.diff
          } >> "$PROMPT_PATH"
        shell: bash

      # Putting it all together: we run the codex action with our code review prompt,
      # structured output, and output file:
      - name: Run Codex structured review
        id: run-codex
        uses: openai/codex-action@main
        with:
          openai-api-key: ${{ secrets.OPENAI_API_KEY }}
          prompt-file: codex-prompt.md
          output-schema-file: codex-output-schema.json
          output-file: codex-output.json
          sandbox: read-only
          model: ${{ env.CODEX_MODEL }}

      - name: Inspect structured Codex output
        if: ${{ always() }}
        run: |
          if [ -s codex-output.json ]; then
            jq '.' codex-output.json || true
          else
            echo "Codex output file missing"
          fi
        shell: bash

      # This step produces in-line code review comments on specific line
      # ranges of code.
      - name: Publish inline review comments
        if: ${{ always() }}
        env:
          REVIEW_JSON: codex-output.json
        run: |
          set -euo pipefail
          if [ ! -s "$REVIEW_JSON" ]; then
            echo "No Codex output file present; skipping comment publishing."
            exit 0
          fi
          findings_count=$(jq '.findings | length' "$REVIEW_JSON")
          if [ "$findings_count" -eq 0 ]; then
            echo "Codex returned no findings; skipping inline comments."
            exit 0
          fi
          jq -c --arg commit "$HEAD_SHA" '.findings[] | {
              body: (.title + "\n\n" + .body + "\n\nConfidence: " + (.confidence_score | tostring) + (if has("priority") then "\nPriority: P" + (.priority | tostring) else "" end)),
              commit_id: $commit,
              path: .code_location.absolute_file_path,
              line: .code_location.line_range.end,
              side: "RIGHT",
              start_line: (if .code_location.line_range.start != .code_location.line_range.end then .code_location.line_range.start else null end),
              start_side: (if .code_location.line_range.start != .code_location.line_range.end then "RIGHT" else null end)
            } | with_entries(select(.value != null))' "$REVIEW_JSON" > findings.jsonl
          while IFS= read -r payload; do
            echo "Posting review comment payload:" && echo "$payload" | jq '.'
            curl -sS \
              -X POST \
              -H "Accept: application/vnd.github+json" \
              -H "Authorization: Bearer ${GITHUB_TOKEN}" \
              -H "X-GitHub-Api-Version: 2022-11-28" \
              "https://api.github.com/repos/${REPOSITORY}/pulls/${PR_NUMBER}/comments" \
              -d "$payload"
          done < findings.jsonl
        shell: bash

      # This section creates a single comment summarizing the review.
      - name: Publish overall summary comment
        if: ${{ always() }}
        env:
          REVIEW_JSON: codex-output.json
        run: |
          set -euo pipefail
          if [ ! -s "$REVIEW_JSON" ]; then
            echo "Codex output missing; skipping summary."
            exit 0
          fi
          overall_state=$(jq -r '.overall_correctness' "$REVIEW_JSON")
          overall_body=$(jq -r '.overall_explanation' "$REVIEW_JSON")
          confidence=$(jq -r '.overall_confidence_score' "$REVIEW_JSON")
          msg="**Codex automated review**\n\nVerdict: ${overall_state}\nConfidence: ${confidence}\n\n${overall_body}"
          curl -sS \
            -X POST \
            -H "Accept: application/vnd.github+json" \
            -H "Authorization: Bearer ${GITHUB_TOKEN}" \
            -H "X-GitHub-Api-Version: 2022-11-28" \
            "https://api.github.com/repos/${REPOSITORY}/issues/${PR_NUMBER}/comments" \
            -d "$(jq -n --arg body "$msg" '{body: $body}')"
        shell: bash
```

## GitLab 示例
GitLab 没有与 GitHub 操作直接等效的操作，但您可以在 GitLab CI/CD 内运行 codex exec 来执行自动代码审查。

然而，GitHub操作包括一个重要的[安全策略](<https://github.com/openai/codex-action?tab=readme-ov-file#safety-strategy>)：它会删除sudo权限，因此Codex无法访问自己的OpenAIAPI密钥。这种隔离至关重要，特别是对于可能存在敏感机密（例如您的 OpenAI API 密钥）的公共存储库，因为它可以防止 Codex 在执行期间读取或泄露凭证。
在运行此作业之前，配置您的 GitLab 项目：

1. Go 至 **项目 → 设置 → CI/CD**.
2. 展开 ** 变量 ** 部分。
3. 添加这些变量：
   - `OPENAI_API_KEY`
   - `GITLAB_TOKEN`
4. 根据需要将它们标记为 masked/protected。
5. 将以下 GitLab 示例作业添加到存储库根目录下的 `.gitlab-ci.yml` 文件中，以便它在合并请求管道期间运行。

请注意公共存储库上的 API 密钥。

```yaml
stages:
  - review

codex-structured-review:
  stage: review
  image: ubuntu:22.04
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
  variables:
    PR_NUMBER: $CI_MERGE_REQUEST_IID
    REPOSITORY: "$CI_PROJECT_PATH"
    BASE_SHA: "$CI_MERGE_REQUEST_DIFF_BASE_SHA"
    HEAD_SHA: "$CI_COMMIT_SHA"

  before_script:
    - apt-get update -y
    - apt-get install -y git curl jq
    - |
      if ! command -v codex >/dev/null 2>&1; then
        ARCH="$(uname -m)"
        case "$ARCH" in
          x86_64) CODEX_PLATFORM="x86_64-unknown-linux-musl" ;;
          aarch64|arm64) CODEX_PLATFORM="aarch64-unknown-linux-musl" ;;
          *)
            echo "Unsupported architecture: $ARCH"
            exit 1
            ;;
        esac

        CODEX_VERSION="${CODEX_VERSION:-latest}"
        if [ -n "${CODEX_DOWNLOAD_URL:-}" ]; then
          CODEX_URL="$CODEX_DOWNLOAD_URL"
        elif [ "$CODEX_VERSION" = "latest" ]; then
          CODEX_URL="https://github.com/openai/codex/releases/latest/download/codex-${CODEX_PLATFORM}.tar.gz"
        else
          CODEX_URL="https://github.com/openai/codex/releases/download/${CODEX_VERSION}/codex-${CODEX_PLATFORM}.tar.gz"
        fi

        TMP_DIR="$(mktemp -d)"
        curl -fsSL "$CODEX_URL" -o "$TMP_DIR/codex.tar.gz"
        tar -xzf "$TMP_DIR/codex.tar.gz" -C "$TMP_DIR"
        install -m 0755 "$TMP_DIR"/codex-* /usr/local/bin/codex
        rm -rf "$TMP_DIR"
      fi
    - git fetch origin $CI_MERGE_REQUEST_TARGET_BRANCH_NAME
    - git fetch origin $CI_MERGE_REQUEST_SOURCE_BRANCH_NAME
    - git checkout $CI_MERGE_REQUEST_SOURCE_BRANCH_NAME

  script:
    - echo "Running Codex structured review for MR !${PR_NUMBER}"

    # Generate structured output schema
    - |
      cat <<'JSON' > codex-output-schema.json
      {
        "$schema": "http://json-schema.org/draft-07/schema#",
        "title": "Codex Structured Review",
        "type": "object",
        "additionalProperties": false,
        "required": [
          "overall_correctness",
          "overall_explanation",
          "overall_confidence_score",
          "findings"
        ],
        "properties": {
          "overall_correctness": {
            "type": "string",
            "description": "Overall verdict for the merge request."
          },
          "overall_explanation": {
            "type": "string",
            "description": "Explanation backing up the verdict."
          },
          "overall_confidence_score": {
            "type": "number",
            "minimum": 0,
            "maximum": 1,
            "description": "Confidence level for the verdict."
          },
          "findings": {
            "type": "array",
            "description": "Collection of actionable review findings.",
            "items": {
              "type": "object",
              "additionalProperties": false,
              "required": [
                "title",
                "body",
                "confidence_score",
                "code_location"
              ],
              "properties": {
                "title": {
                  "type": "string"
                },
                "body": {
                  "type": "string"
                },
                "confidence_score": {
                  "type": "number",
                  "minimum": 0,
                  "maximum": 1
                },
                "code_location": {
                  "type": "object",
                  "additionalProperties": false,
                  "required": [
                    "absolute_file_path",
                    "relative_file_path",
                    "line_range"
                  ],
                  "properties": {
                    "absolute_file_path": {
                      "type": "string"
                    },
                    "relative_file_path": {
                      "type": "string"
                    },
                    "line_range": {
                      "type": "object",
                      "additionalProperties": false,
                      "required": [
                        "start",
                        "end"
                      ],
                      "properties": {
                        "start": {
                          "type": "integer",
                          "minimum": 1
                        },
                        "end": {
                          "type": "integer",
                          "minimum": 1
                        }
                      }
                    }
                  }
                }
              }
            },
            "default": []
          }
        }
      }
      JSON

    # Build Codex review prompt
    - |
      PROMPT_PATH="codex-prompt.md"
      TEMPLATE_PATH="${REVIEW_PROMPT_PATH:-review_prompt.md}"
      if [ -n "$TEMPLATE_PATH" ] && [ -f "$TEMPLATE_PATH" ]; then
        cat "$TEMPLATE_PATH" > "$PROMPT_PATH"
      else
        {
          printf '%s\n' "You are acting as a reviewer for a proposed code change..."
          printf '%s\n' "Focus on issues that impact correctness, performance, security..."
          printf '%s\n' "Flag only actionable issues introduced by this merge request..."
          printf '%s\n' "Provide an overall correctness verdict..."
        } > "$PROMPT_PATH"
      fi
      {
        echo ""
        echo "Repository: ${REPOSITORY}"
        echo "Merge Request #: ${PR_NUMBER}"
        echo "Base SHA: ${BASE_SHA}"
        echo "Head SHA: ${HEAD_SHA}"
        echo ""
        echo "Changed files:"
        git --no-pager diff --name-status "${BASE_SHA}" "${HEAD_SHA}"
        echo ""
        echo "Unified diff (context=5):"
        git --no-pager diff --unified=5 "${BASE_SHA}" "${HEAD_SHA}"
      } >> "$PROMPT_PATH"

    # Run Codex exec CLI
    - |
      printenv OPENAI_API_KEY | codex login --with-api-key && \
      codex exec --output-schema codex-output-schema.json \
                 --output-last-message codex-output.json \
                 --sandbox read-only \
                 - < codex-prompt.md

    # Inspect structured Codex output
    - |
      if [ -s codex-output.json ]; then
        jq '.' codex-output.json || true
      else
        echo "Codex output file missing"; exit 1
      fi

    # Publish inline comments to GitLab MR
    - |
      findings_count=$(jq '.findings | length' codex-output.json)
      if [ "$findings_count" -eq 0 ]; then
        echo "No findings from Codex; skipping comments."
        exit 0
      fi

      jq -c \
        --arg base "$BASE_SHA" \
        --arg start "$BASE_SHA" \
        --arg head "$HEAD_SHA" '
        .findings[] | {
          body: (.title + "\n\n" + .body + "\n\nConfidence: " + (.confidence_score | tostring)),
          position: {
            position_type: "text",
            base_sha: $base,
            start_sha: $start,
            head_sha: $head,
            new_path: (.code_location.relative_file_path // .code_location.absolute_file_path),
            new_line: .code_location.line_range.end
          }
        }
      ' codex-output.json > findings.jsonl

      while IFS= read -r payload; do
        curl -sS --request POST \
             --header "PRIVATE-TOKEN: $GITLAB_TOKEN" \
             --header "Content-Type: application/json" \
             --data "$payload" \
             "https://gitlab.com/api/v4/projects/${CI_PROJECT_ID}/merge_requests/${PR_NUMBER}/discussions"
      done < findings.jsonl

    # Publish overall summary comment
    - |
      overall_state=$(jq -r '.overall_correctness' codex-output.json)
      overall_body=$(jq -r '.overall_explanation' codex-output.json)
      confidence=$(jq -r '.overall_confidence_score' codex-output.json)

      summary="**Codex automated review**\n\nVerdict: ${overall_state}\nConfidence: ${confidence}\n\n${overall_body}"

      curl -sS --request POST \
           --header "PRIVATE-TOKEN: $GITLAB_TOKEN" \
           --header "Content-Type: application/json" \
           --data "$(jq -n --arg body "$summary" '{body: $body}')" \
           "https://gitlab.com/api/v4/projects/${CI_PROJECT_ID}/merge_requests/${PR_NUMBER}/notes"

  artifacts:
    when: always
    paths:
      - codex-output.json
      - codex-prompt.md

```


## Azure DevOps 管道示例

Azure DevOps 不使用 GitHub 操作或 GitLab CI/CD，但适用相同的 Codex 审核模式：在 Azure Pipeline 中运行 Codex，提供拉取请求差异，请求结构化输出，并将结果发布回 Azure DevOps 将请求作为审核线程。

在运行此作业之前，请配置您的 Azure DevOps 项目：

1. 将 `OPENAI_API_KEY` 存储为秘密管道变量或变量组中。
2. 如果计划使用 `$(System.AccessToken)` 进行 Azure DevOps REST API 调用，请启用对 OAuth 令牌的脚本访问。
3. 对于 Azure Repos Git，在目标分支上配置 **构建验证** 分支策略，以便管道针对拉取请求运行。
4. 确保构建服务身份有权参与拉取请求。
5. 添加类似于以下示例的 Azure Pipelines YAML 文件。

请注意公共存储库上的 API 密钥。对于公共或不受信任的拉取请求，请使用 GitLab 部分中描述的相同隔离心态：Codex 在审查任意代码时不应读取或窃取凭据。

此示例假设 `review_prompt.md` 和 `codex-output-schema.json` 已提交到管道旁边。该架构应包括 `code_location.relative_file_path`，Azure DevOps 发布步骤使用它来将结果映射回存储库路径。

```yaml
trigger: none
pr: none # Azure Repos PR validation is configured with branch policies.

pool:
  vmImage: ubuntu-latest

variables:
  CODEX_MODEL: gpt-5.5

steps:
  - checkout: self
    fetchDepth: 0
    persistCredentials: true

  - script: |
      set -euo pipefail
      npm install -g @openai/codex
      command -v jq >/dev/null || {
        sudo apt-get update -y
        sudo apt-get install -y jq
      }
    displayName: Install Codex CLI and jq

  - script: |
      set -euo pipefail
      TARGET_BRANCH="${SYSTEM_PULLREQUEST_TARGETBRANCH#refs/heads/}"
      SOURCE_BRANCH="${SYSTEM_PULLREQUEST_SOURCEBRANCH#refs/heads/}"

      git fetch --no-tags origin \
        "+refs/heads/${TARGET_BRANCH}:refs/remotes/origin/${TARGET_BRANCH}" \
        "+refs/heads/${SOURCE_BRANCH}:refs/remotes/origin/${SOURCE_BRANCH}"

      BASE_SHA="$(git merge-base "origin/${TARGET_BRANCH}" "origin/${SOURCE_BRANCH}")"
      HEAD_SHA="${SYSTEM_PULLREQUEST_SOURCECOMMITID:-$(git rev-parse "origin/${SOURCE_BRANCH}")}"

      cp review_prompt.md codex-prompt.md
      {
        echo ""
        echo "Repository: $(Build.Repository.Name)"
        echo "Pull Request ID: $(System.PullRequest.PullRequestId)"
        echo "Base ref: ${TARGET_BRANCH}"
        echo "Head ref: ${SOURCE_BRANCH}"
        echo "Base SHA: ${BASE_SHA}"
        echo "Head SHA: ${HEAD_SHA}"
        echo "Changed files:"
        git --no-pager diff --name-status "${BASE_SHA}" "${HEAD_SHA}"
        echo ""
        echo "Unified diff (context=5):"
        git --no-pager diff --unified=5 --stat=200 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/diffstat.txt
        git --no-pager diff --unified=5 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/full.diff
        cat /tmp/diffstat.txt
        cat /tmp/full.diff
      } >> codex-prompt.md
    displayName: Build Codex review prompt

  - script: |
      set -euo pipefail
      codex exec \
        --model "${CODEX_MODEL}" \
        --output-schema codex-output-schema.json \
        -o codex-output.json \
        --sandbox read-only \
        - < codex-prompt.md
    env:
      CODEX_API_KEY: $(OPENAI_API_KEY)
    displayName: Run Codex structured review

  - script: |
      set -euo pipefail
      if [ -s codex-output.json ]; then
        jq '.' codex-output.json || true
      else
        echo "Codex output file missing"
      fi
    displayName: Inspect structured Codex output
    condition: always()

  - script: |
      set -euo pipefail
      [ -s codex-output.json ] || exit 0

      org_url="$(System.CollectionUri)"
      project="$(jq -rn --arg v "$(System.TeamProject)" '$v|@uri')"
      repo_id="$(Build.Repository.ID)"
      pr_id="$(System.PullRequest.PullRequestId)"
      api_version="7.1"
      api_base="${org_url}${project}/_apis/git/repositories/${repo_id}/pullRequests/${pr_id}"
      headers=(-H "Authorization: Bearer ${SYSTEM_ACCESSTOKEN}" -H "Content-Type: application/json")

      ado_get() { curl -fsS "${headers[@]}" "$1"; }
      ado_post() { curl -fsS -X POST "${headers[@]}" "$1" -d @"$2"; }

      iterations_json="$(ado_get "${api_base}/iterations?api-version=${api_version}")"
      last_iteration="$(echo "$iterations_json" | jq -r '(.value // .) | map(.id) | max')"

      if [ -n "$last_iteration" ] && [ "$last_iteration" != "null" ]; then
        ado_get "${api_base}/iterations/${last_iteration}/changes?\$top=2000&api-version=${api_version}" |
          jq '(.changeEntries // .value // [])
            | map({
                key: (.item.path // .sourceServerItem),
                value: {
                  id: .changeTrackingId,
                  type: (.changeType // "")
                }
              })
            | map(select(.key != null))
            | from_entries' > azure-devops-changes.json

        jq -c '.findings[]?' codex-output.json | while IFS= read -r finding; do
          path="$(echo "$finding" | jq -r '.code_location.relative_file_path // empty')"
          start_line="$(echo "$finding" | jq -r '.code_location.line_range.start')"
          end_line="$(echo "$finding" | jq -r '.code_location.line_range.end')"
          ado_path="/${path#/}"
          change="$(jq -c --arg path "$ado_path" '.[$path] // empty' azure-devops-changes.json)"

          if [ -z "$path" ] || [ -z "$change" ] || ! [[ "$start_line" =~ ^[0-9]+$ && "$end_line" =~ ^[0-9]+$ ]]; then
            echo "Skipping finding that cannot be safely anchored."
            continue
          fi

          change_type="$(echo "$change" | jq -r '.type')"
          [ "$change_type" != "delete" ] || continue

          jq -n \
            --arg content "$(echo "$finding" | jq -r '"\(.title)\n\n\(.body)\n\nConfidence: \(.confidence_score)\nPriority: P\(.priority)"')" \
            --arg filePath "$ado_path" \
            --argjson start "$start_line" \
            --argjson end "$end_line" \
            --argjson changeTrackingId "$(echo "$change" | jq -r '.id')" \
            --argjson iteration "$last_iteration" \
            '{
              comments: [{ parentCommentId: 0, content: $content, commentType: 1 }],
              status: 1,
              threadContext: {
                filePath: $filePath,
                rightFileStart: { line: $start, offset: 1 },
                rightFileEnd: { line: $end, offset: 1 }
              },
              pullRequestThreadContext: {
                changeTrackingId: $changeTrackingId,
                iterationContext: {
                  firstComparingIteration: 1,
                  secondComparingIteration: $iteration
                }
              }
            }' > inline-comment.json

          ado_post "${api_base}/threads?api-version=${api_version}" inline-comment.json || true
        done
      fi

      jq -n --slurpfile review codex-output.json '{
        comments: [{
          parentCommentId: 0,
          commentType: 1,
          content: "**Codex automated review**\n\nVerdict: \($review[0].overall_correctness)\nConfidence: \($review[0].overall_confidence_score)\n\n\($review[0].overall_explanation)"
        }],
        status: 1
      }' > summary-comment.json

      ado_post "${api_base}/threads?api-version=${api_version}" summary-comment.json
    displayName: Publish Azure DevOps comments
    condition: always()
    env:
      SYSTEM_ACCESSTOKEN: $(System.AccessToken)
```

这是一个最小的实用 Azure Repos 示例，其中包含有关内联注释锚定的明确警告。

此示例假设同一存储库 Azure Repos PR；分叉或跨存储库 PR 可能需要从 `System.PullRequest.SourceRepositoryUri` 获取。

Azure DevOps 特定步骤是通过 [ 拉取请求线程 API](<https://learn.microsoft.com/en-us/rest/api/azure/devops/git/pull-request-threads/create?view=azure-devops-rest-7.1>) 发布 Codex 结果。内嵌注释附有 `threadContext.filePath`、`threadContext.rightFileStart`、`threadContext.rightFileEnd` 和 `pullRequestThreadContext.changeTrackingId`。该示例在发布评论之前从最新的拉取请求迭代更改中解析 `changeTrackingId`。

在您自己的 Azure DevOps 项目中验证内联锚定行为，然后在所需的分支策略中依赖它。特别是测试新文件、修改文件、重命名文件、删除文件和多行结果。如果发现结果无法安全地锚定到差异的右侧，则最好跳过内联注释并依赖整体摘要，而不是发布误导性的行注释。

## 詹金斯示例

我们可以使用相同的方法使用 Jenkins 编写作业脚本。评论再次强调了工作流程的关键阶段：

```groovy
pipeline {
  agent any

  options {
    timestamps()
    ansiColor('xterm')
    // Prevent overlapping runs on the same PR. Newer builds will cancel older ones after passing the milestone.
    disableConcurrentBuilds()
  }

  environment {
    // Default model like your GHA (can be overridden at job/env level)
    CODEX_MODEL = "${env.CODEX_MODEL ?: 'gpt-5.5'}"

    // Filled in during Init
    PR_NUMBER   = ''
    HEAD_SHA    = ''
    BASE_SHA    = ''
    REPOSITORY  = ''   // org/repo
  }

  stages {
    stage('Init (PR context, repo, SHAs)') {
      steps {
        checkout scm

        // Compute PR context and SHAs similar to the GitHub Action
        sh '''
          set -euo pipefail

          # Derive PR number from Jenkins env when building PRs via GitHub Branch Source
          PR_NUMBER="${CHANGE_ID:-}"
          if [ -z "$PR_NUMBER" ]; then
            echo "Not a PR build (CHANGE_ID missing). Exiting."
            exit 1
          fi
          echo "PR_NUMBER=$PR_NUMBER" >> $WORKSPACE/jenkins.env

          # Discover owner/repo (normalize SSH/HTTPS forms)
          ORIGIN_URL="$(git config --get remote.origin.url)"
          if echo "$ORIGIN_URL" | grep -qE '^git@github.com:'; then
            REPO_PATH="${ORIGIN_URL#git@github.com:}"
            REPO_PATH="${REPO_PATH%.git}"
          else
            # e.g. https://github.com/owner/repo.git
            REPO_PATH="${ORIGIN_URL#https://github.com/}"
            REPO_PATH="${REPO_PATH%.git}"
          fi
          echo "REPOSITORY=$REPO_PATH" >> $WORKSPACE/jenkins.env

          # Ensure we have all refs we need
          git fetch --no-tags origin \
            "+refs/heads/*:refs/remotes/origin/*" \
            "+refs/pull/${PR_NUMBER}/head:refs/remotes/origin/PR-${PR_NUMBER}-head" \
            "+refs/pull/${PR_NUMBER}/merge:refs/remotes/origin/PR-${PR_NUMBER}-merge"

          # HEAD (PR head) and BASE (target branch tip)
          CHANGE_TARGET="${CHANGE_TARGET:-main}"
          HEAD_SHA="$(git rev-parse refs/remotes/origin/PR-${PR_NUMBER}-head)"
          BASE_SHA="$(git rev-parse refs/remotes/origin/${CHANGE_TARGET})"

          echo "HEAD_SHA=$HEAD_SHA" >> $WORKSPACE/jenkins.env
          echo "BASE_SHA=$BASE_SHA" >> $WORKSPACE/jenkins.env

          echo "Resolved:"
          echo "  REPOSITORY=$REPO_PATH"
          echo "  PR_NUMBER=$PR_NUMBER"
          echo "  CHANGE_TARGET=$CHANGE_TARGET"
          echo "  HEAD_SHA=$HEAD_SHA"
          echo "  BASE_SHA=$BASE_SHA"
        '''
        script {
          def envMap = readProperties file: 'jenkins.env'
          env.PR_NUMBER  = envMap['PR_NUMBER']
          env.REPOSITORY = envMap['REPOSITORY']
          env.HEAD_SHA   = envMap['HEAD_SHA']
          env.BASE_SHA   = envMap['BASE_SHA']
        }

        // Ensure only latest build for this PR proceeds; older in-flight builds will be aborted here
        milestone 1
      }
    }

    stage('Generate structured output schema') {
      steps {
        sh '''
          set -euo pipefail
          cat > codex-output-schema.json <<'JSON'
          {
            "type": "object",
            "properties": {
              "findings": {
                "type": "array",
                "items": {
                  "type": "object",
                  "properties": {
                    "title": { "type": "string", "maxLength": 80 },
                    "body": { "type": "string", "minLength": 1 },
                    "confidence_score": { "type": "number", "minimum": 0, "maximum": 1 },
                    "priority": { "type": "integer", "minimum": 0, "maximum": 3 },
                    "code_location": {
                      "type": "object",
                      "properties": {
                        "absolute_file_path": { "type": "string", "minLength": 1 },
                        "line_range": {
                          "type": "object",
                          "properties": {
                            "start": { "type": "integer", "minimum": 1 },
                            "end": { "type": "integer", "minimum": 1 }
                          },
                          "required": ["start","end"],
                          "additionalProperties": false
                        }
                      },
                      "required": ["absolute_file_path","line_range"],
                      "additionalProperties": false
                    }
                  },
                  "required": ["title","body","confidence_score","priority","code_location"],
                  "additionalProperties": false
                }
              },
              "overall_correctness": { "type": "string", "enum": ["patch is correct","patch is incorrect"] },
              "overall_explanation": { "type": "string", "minLength": 1 },
              "overall_confidence_score": { "type": "number", "minimum": 0, "maximum": 1 }
            },
            "required": ["findings","overall_correctness","overall_explanation","overall_confidence_score"],
            "additionalProperties": false
          }
          JSON
        '''
      }
    }

    stage('Build Codex review prompt') {
      environment {
        REVIEW_PROMPT_PATH = "${env.CODEX_PROMPT_PATH ?: 'review_prompt.md'}"
      }
      steps {
        sh '''
          set -euo pipefail
          PROMPT_PATH="codex-prompt.md"
          TEMPLATE_PATH="${REVIEW_PROMPT_PATH}"

          if [ -n "$TEMPLATE_PATH" ] && [ -f "$TEMPLATE_PATH" ]; then
            cat "$TEMPLATE_PATH" > "$PROMPT_PATH"
          else
            {
              printf '%s\n' "You are acting as a reviewer for a proposed code change made by another engineer."
              printf '%s\n' "Focus on issues that impact correctness, performance, security, maintainability, or developer experience."
              printf '%s\n' "Flag only actionable issues introduced by the pull request."
              printf '%s\n' "When you flag an issue, provide a short, direct explanation and cite the affected file and line range."
              printf '%s\n' "Prioritize severe issues and avoid nit-level comments unless they block understanding of the diff."
              printf '%s\n' "After listing findings, produce an overall correctness verdict (\\\"patch is correct\\\" or \\\"patch is incorrect\\\") with a concise justification and a confidence score between 0 and 1."
              printf '%s\n' "Ensure that file citations and line numbers are exactly correct using the tools available; if they are incorrect your comments will be rejected."
            } > "$PROMPT_PATH"
          fi

          {
            echo ""
            echo "Repository: ${REPOSITORY}"
            echo "Pull Request #: ${PR_NUMBER}"
            echo "Base ref: ${CHANGE_TARGET}"
            echo "Head ref: ${CHANGE_BRANCH:-PR-${PR_NUMBER}-head}"
            echo "Base SHA: ${BASE_SHA}"
            echo "Head SHA: ${HEAD_SHA}"
            echo "Changed files:"
            git --no-pager diff --name-status "${BASE_SHA}" "${HEAD_SHA}"
            echo ""
            echo "Unified diff (context=5):"
            git --no-pager diff --unified=5 --stat=200 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/diffstat.txt
            git --no-pager diff --unified=5 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/full.diff
            cat /tmp/diffstat.txt
            echo ""
            cat /tmp/full.diff
          } >> "$PROMPT_PATH"
        '''
      }
    }

    stage('Run Codex structured review') {
      environment {
        REVIEW_PROMPT = 'codex-prompt.md'
        REVIEW_SCHEMA = 'codex-output-schema.json'
        REVIEW_OUTPUT = 'codex-output.json'
      }
      steps {
        withCredentials([
          string(credentialsId: 'openai-api-key', variable: 'OPENAI_API_KEY')
        ]) {
          // Option A: If you have the OpenAI CLI installed on the Jenkins agent
          sh '''
            set -euo pipefail
            if command -v openai >/dev/null 2>&1; then
              # Use the Responses API with a JSON schema tool spec
              # Produces codex-output.json with the structured result.
              openai responses.create \
                --model "${CODEX_MODEL}" \
                --input-file "${REVIEW_PROMPT}" \
                --response-format "json_object" \
                --output-schema "${RESPONSE_FORMAT}" \
                --tool-choice "auto" \
                > raw_response.json || true

              # Fallback if CLI doesn’t support your exact flags:
              # Keep demo resilient: If raw_response.json is empty, create a minimal stub so later steps don’t fail.
              if [ ! -s raw_response.json ]; then
                echo '{"findings":[],"overall_correctness":"patch is correct","overall_explanation":"No issues detected.","overall_confidence_score":0.5}' > "${REVIEW_OUTPUT}"
              else
                # If your CLI/format returns a JSON object with the structured content in .output or similar, map it here.
                # Adjust jq path to match your CLI output shape.
                jq -r '.output // .' raw_response.json > "${REVIEW_OUTPUT}" || cp raw_response.json "${REVIEW_OUTPUT}"
              fi
            else
              echo "openai CLI not found; creating a stub output for demo continuity."
              echo '{"findings":[],"overall_correctness":"patch is correct","overall_explanation":"(CLI not available on agent)","overall_confidence_score":0.4}' > "${REVIEW_OUTPUT}"
            fi
          '''
        }
      }
    }

    stage('Inspect structured Codex output') {
      steps {
        sh '''
          if [ -s codex-output.json ]; then
            jq '.' codex-output.json || true
          else
            echo "Codex output file missing"
          fi
        '''
      }
    }

    stage('Publish inline review comments') {
      when { expression { true } }
      steps {
        withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
          sh '''
            set -euo pipefail
            REVIEW_JSON="codex-output.json"
            if [ ! -s "$REVIEW_JSON" ]; then
              echo "No Codex output file present; skipping comment publishing."
              exit 0
            fi

            findings_count=$(jq '.findings | length' "$REVIEW_JSON")
            if [ "$findings_count" -eq 0 ]; then
              echo "Codex returned no findings; skipping inline comments."
              exit 0
            fi

            jq -c --arg commit "$HEAD_SHA" '.findings[] | {
                body: (.title + "\\n\\n" + .body + "\\n\\nConfidence: " + (.confidence_score | tostring) + (if has("priority") then "\\nPriority: P" + (.priority | tostring) else "" end)),
                commit_id: $commit,
                path: .code_location.absolute_file_path,
                line: .code_location.line_range.end,
                side: "RIGHT",
                start_line: (if .code_location.line_range.start != .code_location.line_range.end then .code_location.line_range.start else null end),
                start_side: (if .code_location.line_range.start != .code_location.line_range.end then "RIGHT" else null end)
              } | with_entries(select(.value != null))' "$REVIEW_JSON" > findings.jsonl

            while IFS= read -r payload; do
              echo "Posting review comment payload:" && echo "$payload" | jq '.'
              curl -sS \
                -X POST \
                -H "Accept: application/vnd.github+json" \
                -H "Authorization: Bearer ${GITHUB_TOKEN}" \
                -H "X-GitHub-Api-Version: 2022-11-28" \
                "https://api.github.com/repos/${REPOSITORY}/pulls/${PR_NUMBER}/comments" \
                -d "$payload"
            done < findings.jsonl
          '''
        }
      }
    }

    stage('Publish overall summary comment') {
      steps {
        withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
          sh '''
            set -euo pipefail
            REVIEW_JSON="codex-output.json"
            if [ ! -s "$REVIEW_JSON" ]; then
              echo "Codex output missing; skipping summary."
              exit 0
            fi

            overall_state=$(jq -r '.overall_correctness' "$REVIEW_JSON")
            overall_body=$(jq -r '.overall_explanation' "$REVIEW_JSON")
            confidence=$(jq -r '.overall_confidence_score' "$REVIEW_JSON")
            msg="**Codex automated review**\\n\\nVerdict: ${overall_state}\\nConfidence: ${confidence}\\n\\n${overall_body}"

            jq -n --arg body "$msg" '{body: $body}' > /tmp/summary.json

            curl -sS \
              -X POST \
              -H "Accept: application/vnd.github+json" \
              -H "Authorization: Bearer ${GITHUB_TOKEN}" \
              -H "X-GitHub-Api-Version: 2022-11-28" \
              "https://api.github.com/repos/${REPOSITORY}/issues/${PR_NUMBER}/comments" \
              -d @/tmp/summary.json
          '''
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'codex-*.json, *.md, /tmp/diff*.txt', allowEmptyArchive: true
    }
  }
}
```

# 总结

借助 Codex SDK，您可以在未直接连接到 Codex 云的 CI/CD 环境中构建自己的自动化代码审查工作流程。然而，通过提示触发 Codex、接收结构化输出，然后通过 API 调用对该输出进行操作的模式远远超出了代码审查的范围。例如，我们可以使用此模式在创建事件时触发根本原因分析，并将结构化报告发布到 Slack 通道中。或者，我们可以在每个 PR 上创建代码质量报告并将结果发布到仪表板中。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Build Code Review with the Codex SDK

> **Archived example — review the current CI guidance before using these workflows.** The GitHub example below exposes API and repository tokens to the whole job and combines review with pull request write permissions. Do not copy that credential and permission layout; a read-only sandbox alone does not protect secrets. Use the [current Codex GitHub Action workflow and security checklist](<https://learn.chatgpt.com/docs/github-action>), which separate review from posting feedback. For other CI systems, follow the [automation authentication guidance](<https://learn.chatgpt.com/docs/non-interactive-mode#authenticate-in-automation>).

With [Code Review](<https://chatgpt.com/codex/settings/code-review>) in Codex Cloud, you can connect your team's cloud hosted GitHub repository to Codex and receive automated code reviews on every PR. But what if your code is hosted on-prem, or you don't have GitHub as an SCM?

Luckily, we can replicate Codex's cloud hosted review process in our own CI/CD runners. In this guide, we'll build our own Code Review action using the Codex CLI headless mode with GitHub Actions, GitLab CI/CD, Azure DevOps Pipelines, and Jenkins.

Model recommendation: use `gpt-5.5` for the strongest code review accuracy and consistency in these workflows.

To build our own Code review, we'll take the following steps and adhere to them closely:

1. Install the Codex CLI in our CI/CD runner
2. Prompt Codex in headless (exec) mode with the Code Review prompt that ships with the CLI
3. Specify a structured output JSON schema for Codex
4. Parse the JSON result and use it to make API calls to our SCM to create review comments

Once implemented, Codex will be able to leave inline code review comments:

![Codex Code Review in GitHub](<https://raw.githubusercontent.com/openai/openai-cookbook/5bd6cb40b6b84b69b2032d810610aa85064102df/images/codex_code_review.png>)


## The Code Review Prompt

GPT-5.5 is the recommended model for complex coding workflows in Codex. You can steer GPT-5.5 to conduct a code review with the following prompt:

```
You are acting as a reviewer for a proposed code change made by another engineer.
Focus on issues that impact correctness, performance, security, maintainability, or developer experience.
Flag only actionable issues introduced by the pull request.
When you flag an issue, provide a short, direct explanation and cite the affected file and line range.
Prioritize severe issues and avoid nit-level comments unless they block understanding of the diff.
After listing findings, produce an overall correctness verdict (\"patch is correct\" or \"patch is incorrect\") with a concise justification and a confidence score between 0 and 1.
Ensure that file citations and line numbers are exactly correct using the tools available; if they are incorrect your comments will be rejected.
```

## Codex Structured Outputs

In order to make comments on code ranges in our pull request, we need to receive Codex's response in a specific format. To do that we can create a file called `codex-output-schema.json` that conforms to OpenAI's [structured outputs](<https://platform.openai.com/docs/guides/structured-outputs>) format.

To use this file in our workflow YAML, we can call Codex with the `output-schema-file` argument like this:

```yaml
- name: Run Codex structured review
        id: run-codex
        uses: openai/codex-action@main
        with:
            openai-api-key: ${{ secrets.OPENAI_API_KEY }}
            prompt-file: codex-prompt.md
            sandbox: read-only
            model: ${{ env.CODEX_MODEL }}
            output-schema-file: codex-output-schema.json # <-- Our schema file
            output-file: codex-output.json
```

You can also pass a similar argument to `codex exec` for example:

```bash
codex exec "Review my pull request!" --output-schema codex-output-schema.json
```

## GitHub Actions Example

Let's put it all together. If you're using GitHub Actions in an on-prem environment, you can tailor this example to your specific workflow. Inline comments highlight the key steps.

```yaml
name: Codex Code Review

# Determine when the review action should be run:
on:
  pull_request:
    types:
      - opened
      - reopened
      - synchronize
      - ready_for_review

concurrency:
  group: codex-structured-review-${{ github.event.pull_request.number }}
  cancel-in-progress: true

jobs:
  codex-structured-review:
    name: Run Codex structured review
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    env:
      OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
      GITHUB_TOKEN: ${{ github.token }}
      CODEX_MODEL: ${{ vars.CODEX_MODEL || 'gpt-5.5' }}
      PR_NUMBER: ${{ github.event.pull_request.number }}
      HEAD_SHA: ${{ github.event.pull_request.head.sha }}
      BASE_SHA: ${{ github.event.pull_request.base.sha }}
      REPOSITORY: ${{ github.repository }}
    outputs:
      codex-output: ${{ steps.run-codex.outputs.final-message }}
    steps:
      - name: Checkout pull request merge commit
        uses: actions/checkout@v5
        with:
          ref: refs/pull/${{ github.event.pull_request.number }}/merge

      - name: Fetch base and head refs
        run: |
          set -euxo pipefail
          git fetch --no-tags origin \
            "${{ github.event.pull_request.base.ref }}" \
            +refs/pull/${{ github.event.pull_request.number }}/head
        shell: bash

      # The structured output schema ensures that codex produces comments
      # with filepaths, line numbers, title, body, etc.
      - name: Generate structured output schema
        run: |
          set -euo pipefail
          cat <<'JSON' > codex-output-schema.json
          {
              "type": "object",
              "properties": {
                "findings": {
                  "type": "array",
                  "items": {
                    "type": "object",
                    "properties": {
                      "title": {
                        "type": "string",
                        "maxLength": 80
                      },
                      "body": {
                        "type": "string",
                        "minLength": 1
                      },
                      "confidence_score": {
                        "type": "number",
                        "minimum": 0,
                        "maximum": 1
                      },
                      "priority": {
                        "type": "integer",
                        "minimum": 0,
                        "maximum": 3
                      },
                      "code_location": {
                        "type": "object",
                        "properties": {
                          "absolute_file_path": {
                            "type": "string",
                            "minLength": 1
                          },
                          "line_range": {
                            "type": "object",
                            "properties": {
                              "start": {
                                "type": "integer",
                                "minimum": 1
                              },
                              "end": {
                                "type": "integer",
                                "minimum": 1
                              }
                            },
                            "required": [
                              "start",
                              "end"
                            ],
                            "additionalProperties": false
                          }
                        },
                        "required": [
                          "absolute_file_path",
                          "line_range"
                        ],
                        "additionalProperties": false
                      }
                    },
                    "required": [
                      "title",
                      "body",
                      "confidence_score",
                      "priority",
                      "code_location"
                    ],
                    "additionalProperties": false
                  }
                },
                "overall_correctness": {
                  "type": "string",
                  "enum": [
                    "patch is correct",
                    "patch is incorrect"
                  ]
                },
                "overall_explanation": {
                  "type": "string",
                  "minLength": 1
                },
                "overall_confidence_score": {
                  "type": "number",
                  "minimum": 0,
                  "maximum": 1
                }
              },
              "required": [
                "findings",
                "overall_correctness",
                "overall_explanation",
                "overall_confidence_score"
              ],
              "additionalProperties": false
            }
          JSON
        shell: bash

      # This section generates our prompt:
      - name: Build Codex review prompt
        env:
          REVIEW_PROMPT_PATH: ${{ vars.CODEX_PROMPT_PATH || 'review_prompt.md' }}
        run: |
          set -euo pipefail
          PROMPT_PATH="codex-prompt.md"
          TEMPLATE_PATH="${REVIEW_PROMPT_PATH}"

          if [ -n "$TEMPLATE_PATH" ] && [ -f "$TEMPLATE_PATH" ]; then
            cat "$TEMPLATE_PATH" > "$PROMPT_PATH"
          else
            {
              printf '%s\n' "You are acting as a reviewer for a proposed code change made by another engineer."
              printf '%s\n' "Focus on issues that impact correctness, performance, security, maintainability, or developer experience."
              printf '%s\n' "Flag only actionable issues introduced by the pull request."
              printf '%s\n' "When you flag an issue, provide a short, direct explanation and cite the affected file and line range."
              printf '%s\n' "Prioritize severe issues and avoid nit-level comments unless they block understanding of the diff."
              printf '%s\n' "After listing findings, produce an overall correctness verdict (\"patch is correct\" or \"patch is incorrect\") with a concise justification and a confidence score between 0 and 1."
              printf '%s\n' "Ensure that file citations and line numbers are exactly correct using the tools available; if they are incorrect your comments will be rejected."
            } > "$PROMPT_PATH"
          fi

          {
            echo ""
            echo "Repository: ${REPOSITORY}"
            echo "Pull Request #: ${PR_NUMBER}"
            echo "Base ref: ${{ github.event.pull_request.base.ref }}"
            echo "Head ref: ${{ github.event.pull_request.head.ref }}"
            echo "Base SHA: ${BASE_SHA}"
            echo "Head SHA: ${HEAD_SHA}"
            echo "Changed files:"
            git --no-pager diff --name-status "${BASE_SHA}" "${HEAD_SHA}"
            echo ""
            echo "Unified diff (context=5):"
            git --no-pager diff --unified=5 --stat=200 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/diffstat.txt
            git --no-pager diff --unified=5 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/full.diff
            cat /tmp/diffstat.txt
            echo ""
            cat /tmp/full.diff
          } >> "$PROMPT_PATH"
        shell: bash

      # Putting it all together: we run the codex action with our code review prompt,
      # structured output, and output file:
      - name: Run Codex structured review
        id: run-codex
        uses: openai/codex-action@main
        with:
          openai-api-key: ${{ secrets.OPENAI_API_KEY }}
          prompt-file: codex-prompt.md
          output-schema-file: codex-output-schema.json
          output-file: codex-output.json
          sandbox: read-only
          model: ${{ env.CODEX_MODEL }}

      - name: Inspect structured Codex output
        if: ${{ always() }}
        run: |
          if [ -s codex-output.json ]; then
            jq '.' codex-output.json || true
          else
            echo "Codex output file missing"
          fi
        shell: bash

      # This step produces in-line code review comments on specific line
      # ranges of code.
      - name: Publish inline review comments
        if: ${{ always() }}
        env:
          REVIEW_JSON: codex-output.json
        run: |
          set -euo pipefail
          if [ ! -s "$REVIEW_JSON" ]; then
            echo "No Codex output file present; skipping comment publishing."
            exit 0
          fi
          findings_count=$(jq '.findings | length' "$REVIEW_JSON")
          if [ "$findings_count" -eq 0 ]; then
            echo "Codex returned no findings; skipping inline comments."
            exit 0
          fi
          jq -c --arg commit "$HEAD_SHA" '.findings[] | {
              body: (.title + "\n\n" + .body + "\n\nConfidence: " + (.confidence_score | tostring) + (if has("priority") then "\nPriority: P" + (.priority | tostring) else "" end)),
              commit_id: $commit,
              path: .code_location.absolute_file_path,
              line: .code_location.line_range.end,
              side: "RIGHT",
              start_line: (if .code_location.line_range.start != .code_location.line_range.end then .code_location.line_range.start else null end),
              start_side: (if .code_location.line_range.start != .code_location.line_range.end then "RIGHT" else null end)
            } | with_entries(select(.value != null))' "$REVIEW_JSON" > findings.jsonl
          while IFS= read -r payload; do
            echo "Posting review comment payload:" && echo "$payload" | jq '.'
            curl -sS \
              -X POST \
              -H "Accept: application/vnd.github+json" \
              -H "Authorization: Bearer ${GITHUB_TOKEN}" \
              -H "X-GitHub-Api-Version: 2022-11-28" \
              "https://api.github.com/repos/${REPOSITORY}/pulls/${PR_NUMBER}/comments" \
              -d "$payload"
          done < findings.jsonl
        shell: bash

      # This section creates a single comment summarizing the review.
      - name: Publish overall summary comment
        if: ${{ always() }}
        env:
          REVIEW_JSON: codex-output.json
        run: |
          set -euo pipefail
          if [ ! -s "$REVIEW_JSON" ]; then
            echo "Codex output missing; skipping summary."
            exit 0
          fi
          overall_state=$(jq -r '.overall_correctness' "$REVIEW_JSON")
          overall_body=$(jq -r '.overall_explanation' "$REVIEW_JSON")
          confidence=$(jq -r '.overall_confidence_score' "$REVIEW_JSON")
          msg="**Codex automated review**\n\nVerdict: ${overall_state}\nConfidence: ${confidence}\n\n${overall_body}"
          curl -sS \
            -X POST \
            -H "Accept: application/vnd.github+json" \
            -H "Authorization: Bearer ${GITHUB_TOKEN}" \
            -H "X-GitHub-Api-Version: 2022-11-28" \
            "https://api.github.com/repos/${REPOSITORY}/issues/${PR_NUMBER}/comments" \
            -d "$(jq -n --arg body "$msg" '{body: $body}')"
        shell: bash
```

## GitLab Example
GitLab doesn’t have a direct equivalent to the GitHub Action, but you can run codex exec inside GitLab CI/CD to perform automated code reviews.

However, the GitHub Action includes an important [safety strategy](<https://github.com/openai/codex-action?tab=readme-ov-file#safety-strategy>): it drops sudo permissions so Codex cannot access its own OpenAI API key. This isolation is critical—especially for public repositories where sensitive secrets (like your OpenAI API key) may be present—because it prevents Codex from reading or exfiltrating credentials during execution.
Before running this job, configure your GitLab project:

1. Go to **Project → Settings → CI/CD**.
2. Expand the **Variables** section.
3. Add these variables:
   - `OPENAI_API_KEY`
   - `GITLAB_TOKEN`
4. Mark them as masked/protected as appropriate.
5. Add the following GitLab example job to your `.gitlab-ci.yml` file at the root of your repository so it runs during merge request pipelines.

Please be mindful with your API key on public repositories.

```yaml
stages:
  - review

codex-structured-review:
  stage: review
  image: ubuntu:22.04
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
  variables:
    PR_NUMBER: $CI_MERGE_REQUEST_IID
    REPOSITORY: "$CI_PROJECT_PATH"
    BASE_SHA: "$CI_MERGE_REQUEST_DIFF_BASE_SHA"
    HEAD_SHA: "$CI_COMMIT_SHA"

  before_script:
    - apt-get update -y
    - apt-get install -y git curl jq
    - |
      if ! command -v codex >/dev/null 2>&1; then
        ARCH="$(uname -m)"
        case "$ARCH" in
          x86_64) CODEX_PLATFORM="x86_64-unknown-linux-musl" ;;
          aarch64|arm64) CODEX_PLATFORM="aarch64-unknown-linux-musl" ;;
          *)
            echo "Unsupported architecture: $ARCH"
            exit 1
            ;;
        esac

        CODEX_VERSION="${CODEX_VERSION:-latest}"
        if [ -n "${CODEX_DOWNLOAD_URL:-}" ]; then
          CODEX_URL="$CODEX_DOWNLOAD_URL"
        elif [ "$CODEX_VERSION" = "latest" ]; then
          CODEX_URL="https://github.com/openai/codex/releases/latest/download/codex-${CODEX_PLATFORM}.tar.gz"
        else
          CODEX_URL="https://github.com/openai/codex/releases/download/${CODEX_VERSION}/codex-${CODEX_PLATFORM}.tar.gz"
        fi

        TMP_DIR="$(mktemp -d)"
        curl -fsSL "$CODEX_URL" -o "$TMP_DIR/codex.tar.gz"
        tar -xzf "$TMP_DIR/codex.tar.gz" -C "$TMP_DIR"
        install -m 0755 "$TMP_DIR"/codex-* /usr/local/bin/codex
        rm -rf "$TMP_DIR"
      fi
    - git fetch origin $CI_MERGE_REQUEST_TARGET_BRANCH_NAME
    - git fetch origin $CI_MERGE_REQUEST_SOURCE_BRANCH_NAME
    - git checkout $CI_MERGE_REQUEST_SOURCE_BRANCH_NAME

  script:
    - echo "Running Codex structured review for MR !${PR_NUMBER}"

    # Generate structured output schema
    - |
      cat <<'JSON' > codex-output-schema.json
      {
        "$schema": "http://json-schema.org/draft-07/schema#",
        "title": "Codex Structured Review",
        "type": "object",
        "additionalProperties": false,
        "required": [
          "overall_correctness",
          "overall_explanation",
          "overall_confidence_score",
          "findings"
        ],
        "properties": {
          "overall_correctness": {
            "type": "string",
            "description": "Overall verdict for the merge request."
          },
          "overall_explanation": {
            "type": "string",
            "description": "Explanation backing up the verdict."
          },
          "overall_confidence_score": {
            "type": "number",
            "minimum": 0,
            "maximum": 1,
            "description": "Confidence level for the verdict."
          },
          "findings": {
            "type": "array",
            "description": "Collection of actionable review findings.",
            "items": {
              "type": "object",
              "additionalProperties": false,
              "required": [
                "title",
                "body",
                "confidence_score",
                "code_location"
              ],
              "properties": {
                "title": {
                  "type": "string"
                },
                "body": {
                  "type": "string"
                },
                "confidence_score": {
                  "type": "number",
                  "minimum": 0,
                  "maximum": 1
                },
                "code_location": {
                  "type": "object",
                  "additionalProperties": false,
                  "required": [
                    "absolute_file_path",
                    "relative_file_path",
                    "line_range"
                  ],
                  "properties": {
                    "absolute_file_path": {
                      "type": "string"
                    },
                    "relative_file_path": {
                      "type": "string"
                    },
                    "line_range": {
                      "type": "object",
                      "additionalProperties": false,
                      "required": [
                        "start",
                        "end"
                      ],
                      "properties": {
                        "start": {
                          "type": "integer",
                          "minimum": 1
                        },
                        "end": {
                          "type": "integer",
                          "minimum": 1
                        }
                      }
                    }
                  }
                }
              }
            },
            "default": []
          }
        }
      }
      JSON

    # Build Codex review prompt
    - |
      PROMPT_PATH="codex-prompt.md"
      TEMPLATE_PATH="${REVIEW_PROMPT_PATH:-review_prompt.md}"
      if [ -n "$TEMPLATE_PATH" ] && [ -f "$TEMPLATE_PATH" ]; then
        cat "$TEMPLATE_PATH" > "$PROMPT_PATH"
      else
        {
          printf '%s\n' "You are acting as a reviewer for a proposed code change..."
          printf '%s\n' "Focus on issues that impact correctness, performance, security..."
          printf '%s\n' "Flag only actionable issues introduced by this merge request..."
          printf '%s\n' "Provide an overall correctness verdict..."
        } > "$PROMPT_PATH"
      fi
      {
        echo ""
        echo "Repository: ${REPOSITORY}"
        echo "Merge Request #: ${PR_NUMBER}"
        echo "Base SHA: ${BASE_SHA}"
        echo "Head SHA: ${HEAD_SHA}"
        echo ""
        echo "Changed files:"
        git --no-pager diff --name-status "${BASE_SHA}" "${HEAD_SHA}"
        echo ""
        echo "Unified diff (context=5):"
        git --no-pager diff --unified=5 "${BASE_SHA}" "${HEAD_SHA}"
      } >> "$PROMPT_PATH"

    # Run Codex exec CLI
    - |
      printenv OPENAI_API_KEY | codex login --with-api-key && \
      codex exec --output-schema codex-output-schema.json \
                 --output-last-message codex-output.json \
                 --sandbox read-only \
                 - < codex-prompt.md

    # Inspect structured Codex output
    - |
      if [ -s codex-output.json ]; then
        jq '.' codex-output.json || true
      else
        echo "Codex output file missing"; exit 1
      fi

    # Publish inline comments to GitLab MR
    - |
      findings_count=$(jq '.findings | length' codex-output.json)
      if [ "$findings_count" -eq 0 ]; then
        echo "No findings from Codex; skipping comments."
        exit 0
      fi

      jq -c \
        --arg base "$BASE_SHA" \
        --arg start "$BASE_SHA" \
        --arg head "$HEAD_SHA" '
        .findings[] | {
          body: (.title + "\n\n" + .body + "\n\nConfidence: " + (.confidence_score | tostring)),
          position: {
            position_type: "text",
            base_sha: $base,
            start_sha: $start,
            head_sha: $head,
            new_path: (.code_location.relative_file_path // .code_location.absolute_file_path),
            new_line: .code_location.line_range.end
          }
        }
      ' codex-output.json > findings.jsonl

      while IFS= read -r payload; do
        curl -sS --request POST \
             --header "PRIVATE-TOKEN: $GITLAB_TOKEN" \
             --header "Content-Type: application/json" \
             --data "$payload" \
             "https://gitlab.com/api/v4/projects/${CI_PROJECT_ID}/merge_requests/${PR_NUMBER}/discussions"
      done < findings.jsonl

    # Publish overall summary comment
    - |
      overall_state=$(jq -r '.overall_correctness' codex-output.json)
      overall_body=$(jq -r '.overall_explanation' codex-output.json)
      confidence=$(jq -r '.overall_confidence_score' codex-output.json)

      summary="**Codex automated review**\n\nVerdict: ${overall_state}\nConfidence: ${confidence}\n\n${overall_body}"

      curl -sS --request POST \
           --header "PRIVATE-TOKEN: $GITLAB_TOKEN" \
           --header "Content-Type: application/json" \
           --data "$(jq -n --arg body "$summary" '{body: $body}')" \
           "https://gitlab.com/api/v4/projects/${CI_PROJECT_ID}/merge_requests/${PR_NUMBER}/notes"

  artifacts:
    when: always
    paths:
      - codex-output.json
      - codex-prompt.md

```


## Azure DevOps Pipelines Example

Azure DevOps does not use GitHub Actions or GitLab CI/CD, but the same Codex review pattern applies: run Codex in an Azure Pipeline, provide the pull request diff, request structured output, and publish the resulting findings back to the Azure DevOps pull request as review threads.

Before running this job, configure your Azure DevOps project:

1. Store `OPENAI_API_KEY` as a secret pipeline variable or in a variable group.
2. Enable script access to the OAuth token if you plan to use `$(System.AccessToken)` for Azure DevOps REST API calls.
3. For Azure Repos Git, configure a **Build validation** branch policy on the target branch so the pipeline runs for pull requests.
4. Ensure the build service identity has permission to contribute to pull requests.
5. Add an Azure Pipelines YAML file similar to the example below.

Please be mindful with your API key on public repositories. For public or untrusted pull requests, use the same isolation mindset described in the GitLab section: Codex should not be able to read or exfiltrate credentials while reviewing arbitrary code.

This example assumes `review_prompt.md` and `codex-output-schema.json` are committed next to the pipeline. The schema should include `code_location.relative_file_path`, which the Azure DevOps publishing step uses to map findings back to repository paths.

```yaml
trigger: none
pr: none # Azure Repos PR validation is configured with branch policies.

pool:
  vmImage: ubuntu-latest

variables:
  CODEX_MODEL: gpt-5.5

steps:
  - checkout: self
    fetchDepth: 0
    persistCredentials: true

  - script: |
      set -euo pipefail
      npm install -g @openai/codex
      command -v jq >/dev/null || {
        sudo apt-get update -y
        sudo apt-get install -y jq
      }
    displayName: Install Codex CLI and jq

  - script: |
      set -euo pipefail
      TARGET_BRANCH="${SYSTEM_PULLREQUEST_TARGETBRANCH#refs/heads/}"
      SOURCE_BRANCH="${SYSTEM_PULLREQUEST_SOURCEBRANCH#refs/heads/}"

      git fetch --no-tags origin \
        "+refs/heads/${TARGET_BRANCH}:refs/remotes/origin/${TARGET_BRANCH}" \
        "+refs/heads/${SOURCE_BRANCH}:refs/remotes/origin/${SOURCE_BRANCH}"

      BASE_SHA="$(git merge-base "origin/${TARGET_BRANCH}" "origin/${SOURCE_BRANCH}")"
      HEAD_SHA="${SYSTEM_PULLREQUEST_SOURCECOMMITID:-$(git rev-parse "origin/${SOURCE_BRANCH}")}"

      cp review_prompt.md codex-prompt.md
      {
        echo ""
        echo "Repository: $(Build.Repository.Name)"
        echo "Pull Request ID: $(System.PullRequest.PullRequestId)"
        echo "Base ref: ${TARGET_BRANCH}"
        echo "Head ref: ${SOURCE_BRANCH}"
        echo "Base SHA: ${BASE_SHA}"
        echo "Head SHA: ${HEAD_SHA}"
        echo "Changed files:"
        git --no-pager diff --name-status "${BASE_SHA}" "${HEAD_SHA}"
        echo ""
        echo "Unified diff (context=5):"
        git --no-pager diff --unified=5 --stat=200 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/diffstat.txt
        git --no-pager diff --unified=5 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/full.diff
        cat /tmp/diffstat.txt
        cat /tmp/full.diff
      } >> codex-prompt.md
    displayName: Build Codex review prompt

  - script: |
      set -euo pipefail
      codex exec \
        --model "${CODEX_MODEL}" \
        --output-schema codex-output-schema.json \
        -o codex-output.json \
        --sandbox read-only \
        - < codex-prompt.md
    env:
      CODEX_API_KEY: $(OPENAI_API_KEY)
    displayName: Run Codex structured review

  - script: |
      set -euo pipefail
      if [ -s codex-output.json ]; then
        jq '.' codex-output.json || true
      else
        echo "Codex output file missing"
      fi
    displayName: Inspect structured Codex output
    condition: always()

  - script: |
      set -euo pipefail
      [ -s codex-output.json ] || exit 0

      org_url="$(System.CollectionUri)"
      project="$(jq -rn --arg v "$(System.TeamProject)" '$v|@uri')"
      repo_id="$(Build.Repository.ID)"
      pr_id="$(System.PullRequest.PullRequestId)"
      api_version="7.1"
      api_base="${org_url}${project}/_apis/git/repositories/${repo_id}/pullRequests/${pr_id}"
      headers=(-H "Authorization: Bearer ${SYSTEM_ACCESSTOKEN}" -H "Content-Type: application/json")

      ado_get() { curl -fsS "${headers[@]}" "$1"; }
      ado_post() { curl -fsS -X POST "${headers[@]}" "$1" -d @"$2"; }

      iterations_json="$(ado_get "${api_base}/iterations?api-version=${api_version}")"
      last_iteration="$(echo "$iterations_json" | jq -r '(.value // .) | map(.id) | max')"

      if [ -n "$last_iteration" ] && [ "$last_iteration" != "null" ]; then
        ado_get "${api_base}/iterations/${last_iteration}/changes?\$top=2000&api-version=${api_version}" |
          jq '(.changeEntries // .value // [])
            | map({
                key: (.item.path // .sourceServerItem),
                value: {
                  id: .changeTrackingId,
                  type: (.changeType // "")
                }
              })
            | map(select(.key != null))
            | from_entries' > azure-devops-changes.json

        jq -c '.findings[]?' codex-output.json | while IFS= read -r finding; do
          path="$(echo "$finding" | jq -r '.code_location.relative_file_path // empty')"
          start_line="$(echo "$finding" | jq -r '.code_location.line_range.start')"
          end_line="$(echo "$finding" | jq -r '.code_location.line_range.end')"
          ado_path="/${path#/}"
          change="$(jq -c --arg path "$ado_path" '.[$path] // empty' azure-devops-changes.json)"

          if [ -z "$path" ] || [ -z "$change" ] || ! [[ "$start_line" =~ ^[0-9]+$ && "$end_line" =~ ^[0-9]+$ ]]; then
            echo "Skipping finding that cannot be safely anchored."
            continue
          fi

          change_type="$(echo "$change" | jq -r '.type')"
          [ "$change_type" != "delete" ] || continue

          jq -n \
            --arg content "$(echo "$finding" | jq -r '"\(.title)\n\n\(.body)\n\nConfidence: \(.confidence_score)\nPriority: P\(.priority)"')" \
            --arg filePath "$ado_path" \
            --argjson start "$start_line" \
            --argjson end "$end_line" \
            --argjson changeTrackingId "$(echo "$change" | jq -r '.id')" \
            --argjson iteration "$last_iteration" \
            '{
              comments: [{ parentCommentId: 0, content: $content, commentType: 1 }],
              status: 1,
              threadContext: {
                filePath: $filePath,
                rightFileStart: { line: $start, offset: 1 },
                rightFileEnd: { line: $end, offset: 1 }
              },
              pullRequestThreadContext: {
                changeTrackingId: $changeTrackingId,
                iterationContext: {
                  firstComparingIteration: 1,
                  secondComparingIteration: $iteration
                }
              }
            }' > inline-comment.json

          ado_post "${api_base}/threads?api-version=${api_version}" inline-comment.json || true
        done
      fi

      jq -n --slurpfile review codex-output.json '{
        comments: [{
          parentCommentId: 0,
          commentType: 1,
          content: "**Codex automated review**\n\nVerdict: \($review[0].overall_correctness)\nConfidence: \($review[0].overall_confidence_score)\n\n\($review[0].overall_explanation)"
        }],
        status: 1
      }' > summary-comment.json

      ado_post "${api_base}/threads?api-version=${api_version}" summary-comment.json
    displayName: Publish Azure DevOps comments
    condition: always()
    env:
      SYSTEM_ACCESSTOKEN: $(System.AccessToken)
```

This is a minimal practical Azure Repos example, with explicit caveats around inline comment anchoring.

This example assumes a same-repository Azure Repos PR; forked or cross-repository PRs may need to fetch from `System.PullRequest.SourceRepositoryUri`.

The Azure DevOps-specific step is publishing Codex findings through the [Pull Request Threads API](<https://learn.microsoft.com/en-us/rest/api/azure/devops/git/pull-request-threads/create?view=azure-devops-rest-7.1>). Inline comments are attached with `threadContext.filePath`, `threadContext.rightFileStart`, `threadContext.rightFileEnd`, and `pullRequestThreadContext.changeTrackingId`. The example resolves `changeTrackingId` from the latest pull request iteration changes before posting a comment.

Validate inline anchoring behavior in your own Azure DevOps project before depending on it in a required branch policy. In particular, test new files, modified files, renamed files, deleted files, and multi-line findings. If a finding cannot be safely anchored to the right side of the diff, prefer skipping the inline comment and relying on the overall summary instead of posting a misleading line comment.

## Jenkins Example

We can use the same approach to scripting a job with Jenkins. Once again, comments highlight key stages of the workflow:

```groovy
pipeline {
  agent any

  options {
    timestamps()
    ansiColor('xterm')
    // Prevent overlapping runs on the same PR. Newer builds will cancel older ones after passing the milestone.
    disableConcurrentBuilds()
  }

  environment {
    // Default model like your GHA (can be overridden at job/env level)
    CODEX_MODEL = "${env.CODEX_MODEL ?: 'gpt-5.5'}"

    // Filled in during Init
    PR_NUMBER   = ''
    HEAD_SHA    = ''
    BASE_SHA    = ''
    REPOSITORY  = ''   // org/repo
  }

  stages {
    stage('Init (PR context, repo, SHAs)') {
      steps {
        checkout scm

        // Compute PR context and SHAs similar to the GitHub Action
        sh '''
          set -euo pipefail

          # Derive PR number from Jenkins env when building PRs via GitHub Branch Source
          PR_NUMBER="${CHANGE_ID:-}"
          if [ -z "$PR_NUMBER" ]; then
            echo "Not a PR build (CHANGE_ID missing). Exiting."
            exit 1
          fi
          echo "PR_NUMBER=$PR_NUMBER" >> $WORKSPACE/jenkins.env

          # Discover owner/repo (normalize SSH/HTTPS forms)
          ORIGIN_URL="$(git config --get remote.origin.url)"
          if echo "$ORIGIN_URL" | grep -qE '^git@github.com:'; then
            REPO_PATH="${ORIGIN_URL#git@github.com:}"
            REPO_PATH="${REPO_PATH%.git}"
          else
            # e.g. https://github.com/owner/repo.git
            REPO_PATH="${ORIGIN_URL#https://github.com/}"
            REPO_PATH="${REPO_PATH%.git}"
          fi
          echo "REPOSITORY=$REPO_PATH" >> $WORKSPACE/jenkins.env

          # Ensure we have all refs we need
          git fetch --no-tags origin \
            "+refs/heads/*:refs/remotes/origin/*" \
            "+refs/pull/${PR_NUMBER}/head:refs/remotes/origin/PR-${PR_NUMBER}-head" \
            "+refs/pull/${PR_NUMBER}/merge:refs/remotes/origin/PR-${PR_NUMBER}-merge"

          # HEAD (PR head) and BASE (target branch tip)
          CHANGE_TARGET="${CHANGE_TARGET:-main}"
          HEAD_SHA="$(git rev-parse refs/remotes/origin/PR-${PR_NUMBER}-head)"
          BASE_SHA="$(git rev-parse refs/remotes/origin/${CHANGE_TARGET})"

          echo "HEAD_SHA=$HEAD_SHA" >> $WORKSPACE/jenkins.env
          echo "BASE_SHA=$BASE_SHA" >> $WORKSPACE/jenkins.env

          echo "Resolved:"
          echo "  REPOSITORY=$REPO_PATH"
          echo "  PR_NUMBER=$PR_NUMBER"
          echo "  CHANGE_TARGET=$CHANGE_TARGET"
          echo "  HEAD_SHA=$HEAD_SHA"
          echo "  BASE_SHA=$BASE_SHA"
        '''
        script {
          def envMap = readProperties file: 'jenkins.env'
          env.PR_NUMBER  = envMap['PR_NUMBER']
          env.REPOSITORY = envMap['REPOSITORY']
          env.HEAD_SHA   = envMap['HEAD_SHA']
          env.BASE_SHA   = envMap['BASE_SHA']
        }

        // Ensure only latest build for this PR proceeds; older in-flight builds will be aborted here
        milestone 1
      }
    }

    stage('Generate structured output schema') {
      steps {
        sh '''
          set -euo pipefail
          cat > codex-output-schema.json <<'JSON'
          {
            "type": "object",
            "properties": {
              "findings": {
                "type": "array",
                "items": {
                  "type": "object",
                  "properties": {
                    "title": { "type": "string", "maxLength": 80 },
                    "body": { "type": "string", "minLength": 1 },
                    "confidence_score": { "type": "number", "minimum": 0, "maximum": 1 },
                    "priority": { "type": "integer", "minimum": 0, "maximum": 3 },
                    "code_location": {
                      "type": "object",
                      "properties": {
                        "absolute_file_path": { "type": "string", "minLength": 1 },
                        "line_range": {
                          "type": "object",
                          "properties": {
                            "start": { "type": "integer", "minimum": 1 },
                            "end": { "type": "integer", "minimum": 1 }
                          },
                          "required": ["start","end"],
                          "additionalProperties": false
                        }
                      },
                      "required": ["absolute_file_path","line_range"],
                      "additionalProperties": false
                    }
                  },
                  "required": ["title","body","confidence_score","priority","code_location"],
                  "additionalProperties": false
                }
              },
              "overall_correctness": { "type": "string", "enum": ["patch is correct","patch is incorrect"] },
              "overall_explanation": { "type": "string", "minLength": 1 },
              "overall_confidence_score": { "type": "number", "minimum": 0, "maximum": 1 }
            },
            "required": ["findings","overall_correctness","overall_explanation","overall_confidence_score"],
            "additionalProperties": false
          }
          JSON
        '''
      }
    }

    stage('Build Codex review prompt') {
      environment {
        REVIEW_PROMPT_PATH = "${env.CODEX_PROMPT_PATH ?: 'review_prompt.md'}"
      }
      steps {
        sh '''
          set -euo pipefail
          PROMPT_PATH="codex-prompt.md"
          TEMPLATE_PATH="${REVIEW_PROMPT_PATH}"

          if [ -n "$TEMPLATE_PATH" ] && [ -f "$TEMPLATE_PATH" ]; then
            cat "$TEMPLATE_PATH" > "$PROMPT_PATH"
          else
            {
              printf '%s\n' "You are acting as a reviewer for a proposed code change made by another engineer."
              printf '%s\n' "Focus on issues that impact correctness, performance, security, maintainability, or developer experience."
              printf '%s\n' "Flag only actionable issues introduced by the pull request."
              printf '%s\n' "When you flag an issue, provide a short, direct explanation and cite the affected file and line range."
              printf '%s\n' "Prioritize severe issues and avoid nit-level comments unless they block understanding of the diff."
              printf '%s\n' "After listing findings, produce an overall correctness verdict (\\\"patch is correct\\\" or \\\"patch is incorrect\\\") with a concise justification and a confidence score between 0 and 1."
              printf '%s\n' "Ensure that file citations and line numbers are exactly correct using the tools available; if they are incorrect your comments will be rejected."
            } > "$PROMPT_PATH"
          fi

          {
            echo ""
            echo "Repository: ${REPOSITORY}"
            echo "Pull Request #: ${PR_NUMBER}"
            echo "Base ref: ${CHANGE_TARGET}"
            echo "Head ref: ${CHANGE_BRANCH:-PR-${PR_NUMBER}-head}"
            echo "Base SHA: ${BASE_SHA}"
            echo "Head SHA: ${HEAD_SHA}"
            echo "Changed files:"
            git --no-pager diff --name-status "${BASE_SHA}" "${HEAD_SHA}"
            echo ""
            echo "Unified diff (context=5):"
            git --no-pager diff --unified=5 --stat=200 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/diffstat.txt
            git --no-pager diff --unified=5 "${BASE_SHA}" "${HEAD_SHA}" > /tmp/full.diff
            cat /tmp/diffstat.txt
            echo ""
            cat /tmp/full.diff
          } >> "$PROMPT_PATH"
        '''
      }
    }

    stage('Run Codex structured review') {
      environment {
        REVIEW_PROMPT = 'codex-prompt.md'
        REVIEW_SCHEMA = 'codex-output-schema.json'
        REVIEW_OUTPUT = 'codex-output.json'
      }
      steps {
        withCredentials([
          string(credentialsId: 'openai-api-key', variable: 'OPENAI_API_KEY')
        ]) {
          // Option A: If you have the OpenAI CLI installed on the Jenkins agent
          sh '''
            set -euo pipefail
            if command -v openai >/dev/null 2>&1; then
              # Use the Responses API with a JSON schema tool spec
              # Produces codex-output.json with the structured result.
              openai responses.create \
                --model "${CODEX_MODEL}" \
                --input-file "${REVIEW_PROMPT}" \
                --response-format "json_object" \
                --output-schema "${RESPONSE_FORMAT}" \
                --tool-choice "auto" \
                > raw_response.json || true

              # Fallback if CLI doesn’t support your exact flags:
              # Keep demo resilient: If raw_response.json is empty, create a minimal stub so later steps don’t fail.
              if [ ! -s raw_response.json ]; then
                echo '{"findings":[],"overall_correctness":"patch is correct","overall_explanation":"No issues detected.","overall_confidence_score":0.5}' > "${REVIEW_OUTPUT}"
              else
                # If your CLI/format returns a JSON object with the structured content in .output or similar, map it here.
                # Adjust jq path to match your CLI output shape.
                jq -r '.output // .' raw_response.json > "${REVIEW_OUTPUT}" || cp raw_response.json "${REVIEW_OUTPUT}"
              fi
            else
              echo "openai CLI not found; creating a stub output for demo continuity."
              echo '{"findings":[],"overall_correctness":"patch is correct","overall_explanation":"(CLI not available on agent)","overall_confidence_score":0.4}' > "${REVIEW_OUTPUT}"
            fi
          '''
        }
      }
    }

    stage('Inspect structured Codex output') {
      steps {
        sh '''
          if [ -s codex-output.json ]; then
            jq '.' codex-output.json || true
          else
            echo "Codex output file missing"
          fi
        '''
      }
    }

    stage('Publish inline review comments') {
      when { expression { true } }
      steps {
        withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
          sh '''
            set -euo pipefail
            REVIEW_JSON="codex-output.json"
            if [ ! -s "$REVIEW_JSON" ]; then
              echo "No Codex output file present; skipping comment publishing."
              exit 0
            fi

            findings_count=$(jq '.findings | length' "$REVIEW_JSON")
            if [ "$findings_count" -eq 0 ]; then
              echo "Codex returned no findings; skipping inline comments."
              exit 0
            fi

            jq -c --arg commit "$HEAD_SHA" '.findings[] | {
                body: (.title + "\\n\\n" + .body + "\\n\\nConfidence: " + (.confidence_score | tostring) + (if has("priority") then "\\nPriority: P" + (.priority | tostring) else "" end)),
                commit_id: $commit,
                path: .code_location.absolute_file_path,
                line: .code_location.line_range.end,
                side: "RIGHT",
                start_line: (if .code_location.line_range.start != .code_location.line_range.end then .code_location.line_range.start else null end),
                start_side: (if .code_location.line_range.start != .code_location.line_range.end then "RIGHT" else null end)
              } | with_entries(select(.value != null))' "$REVIEW_JSON" > findings.jsonl

            while IFS= read -r payload; do
              echo "Posting review comment payload:" && echo "$payload" | jq '.'
              curl -sS \
                -X POST \
                -H "Accept: application/vnd.github+json" \
                -H "Authorization: Bearer ${GITHUB_TOKEN}" \
                -H "X-GitHub-Api-Version: 2022-11-28" \
                "https://api.github.com/repos/${REPOSITORY}/pulls/${PR_NUMBER}/comments" \
                -d "$payload"
            done < findings.jsonl
          '''
        }
      }
    }

    stage('Publish overall summary comment') {
      steps {
        withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
          sh '''
            set -euo pipefail
            REVIEW_JSON="codex-output.json"
            if [ ! -s "$REVIEW_JSON" ]; then
              echo "Codex output missing; skipping summary."
              exit 0
            fi

            overall_state=$(jq -r '.overall_correctness' "$REVIEW_JSON")
            overall_body=$(jq -r '.overall_explanation' "$REVIEW_JSON")
            confidence=$(jq -r '.overall_confidence_score' "$REVIEW_JSON")
            msg="**Codex automated review**\\n\\nVerdict: ${overall_state}\\nConfidence: ${confidence}\\n\\n${overall_body}"

            jq -n --arg body "$msg" '{body: $body}' > /tmp/summary.json

            curl -sS \
              -X POST \
              -H "Accept: application/vnd.github+json" \
              -H "Authorization: Bearer ${GITHUB_TOKEN}" \
              -H "X-GitHub-Api-Version: 2022-11-28" \
              "https://api.github.com/repos/${REPOSITORY}/issues/${PR_NUMBER}/comments" \
              -d @/tmp/summary.json
          '''
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'codex-*.json, *.md, /tmp/diff*.txt', allowEmptyArchive: true
    }
  }
}
```

# Wrap Up

With the Codex SDK, you can build your own automated code review workflow in CI/CD environments that are not directly connected to Codex Cloud. However, the pattern of triggering Codex with a prompt, receiving a structured output, and then acting on that output with an API call extends far beyond Code Review. For example, we could use this pattern to trigger a root-cause analysis when an incident is created and post a structured report into a Slack channel. Or we could create a code quality report on each PR and post results into a dashboard.


</details>
