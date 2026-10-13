# Claude Code 代码审查命令模板

提供插件审查 PR 的提示流程，包含仓库指令读取、多角色检查、置信度筛选和反馈；属于插件命令实现。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2026-03-12](<https://github.com/anthropics/claude-code/commit/db8834ba1d72e9a26fba30ac85f3bc4316bb0689>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/code-review/commands/code-review.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Claude Code 代码审查命令模板.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
allowed-tools: Bash(gh issue view:*), Bash(gh search:*), Bash(gh issue list:*), Bash(gh pr comment:*), Bash(gh pr diff:*), Bash(gh pr view:*), Bash(gh pr list:*), mcp__github_inline_comment__create_inline_comment
description: Code review a pull request
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

为给定的拉取请求提供代码审查。

**Agent 假设（适用于所有 agents 和子代理）：**
- 所有工具均可用且不会出现错误。不要测试工具或进行探索性调用。确保启动的每个子代理都清楚这一点。
- 仅在需要完成任务时才调用工具。每个工具调用都应该有明确的目的。

为此，请严格执行以下步骤：

1. 启动俳句 agent 检查以下任一情况是否为真：
   - 拉取请求已关闭
   - 拉取请求是一个草稿
   - 拉取请求不需要代码审查（例如自动PR，明显正确的琐碎更改）
   - Claude 已经对此 PR 发表评论（检查 `gh pr view <PR> --comments` 克劳德留下的评论）

如果任何条件为真，请停止并且不要继续。

注意：仍然查看 Claude 生成的 PR。

2. 启动俳句 agent 以返回所有相关 CLAUDE.md 文件的文件路径列表（不是其内容），包括：
   - 根 CLAUDE.md 文件（如果存在）
   - 包含拉取请求修改的文件的目录中的任何 CLAUDE.md 文件

3. 启动十四行诗 agent 以查看拉取请求并返回更改摘要

4. 并行启动 4 agents 以独立审查更改。每个 agent 应返回问题列表，其中每个问题都包含描述和标记原因（例如“CLAUDE.md 遵守”、“bug”）。 agents 应执行以下操作：

Agents 1 + 2：CLAUDE.md 合规性十四行诗 agents
 并行审核 CLAUDE.md 合规性变更。注意：在评估文件的 CLAUDE.md 合规性时，您应该仅考虑与该文件或父文件共享文件路径的 CLAUDE.md 文件。

Agent 3：Opus bug agent（与 agent 4 并行的子代理）
 扫描明显的 bug。仅关注差异本身，而不阅读额外的上下文。仅标记重大错误；忽略挑剔和可能的误报。不要标记那些如果不查看 git diff 之外的上下文就无法验证的问题。

Agent 4：Opus bug agent（与 agent 3 并行的子代理）
 查找引入的代码中存在的问题。这可能是安全问题、不正确的逻辑等。仅查找属于已更改代码的问题。

**CRITICAL：我们只想要HIGH SIGNAL问题。**标记问题，其中：
   - 代码将无法编译或解析（语法错误、类型错误、缺少导入、未解析的引用）
   - 无论输入如何，代码肯定会产生错误的结果（明显的逻辑错误）
   - 清晰、明确的 CLAUDE.md 违规行为，您可以在其中引用所违反的确切规则

执行 NOT 标志：
   - 代码风格或质量问题
   - 取决于特定输入或状态的潜在问题
   - 主观建议或改进

如果您不确定某个问题是否真实，请不要标记它。误报会削弱信任并浪费审阅者的时间。

除上述内容外，还应告知每个子代理 PR 标题和说明。这将有助于提供有关作者意图的背景。

5. 对于 agents 3 和 4 在上一步中发现的每个问题，启动并行子代理来验证问题。这些子代理应该获得 PR 标题和描述以及问题的描述。 agent 的工作是审查该问题，以验证所述问题确实是一个具有高可信度的问题。例如，如果标记了诸如“变量未定义”之类的问题，则子代理的工作将是验证代码中的情况是否确实如此。另一个例子是 CLAUDE.md 问题。 agent 应验证所违反的 CLAUDE.md 规则是否在此文件的范围内并且确实被违反。使用 Opus 子代理来解决错误和逻辑问题，使用十四行诗 agents 来解决 CLAUDE.md 违规问题。

6. 过滤掉步骤 5 中未验证的任何问题。此步骤将为我们提供高信号问题列表以供我们审查。

7. 将审核结果摘要输出到终端：
   - 如果发现问题，请列出每个问题并附上简短说明。
   - 如果未发现问题，请说明：“未发现问题。已检查错误和 CLAUDE.md 合规性。”

   如果 `--comment` 论点是 NOT 前提是，停在这里。请勿发布任何 GitHub 评论。

如果提供了 `--comment` 参数 IS 并且发现了 NO 问题，请使用 `gh pr comment` 发布摘要评论并停止。

如果提供了 `--comment` 参数 IS 并且发现问题，请继续执行步骤 8。

8. 创建您计划留下的所有评论的列表。这只是为了确保您对这些评论感到满意。不要将此列表张贴在任何地方。

9. 使用 `mcp__github_inline_comment__create_inline_comment` 和 `confirmed: true` 发布每个问题的内联评论。对于每条评论：
   - 提供问题的简要描述
   - 对于小型、独立的修复，请包含可提交的建议块
   - 对于较大的修复（6+行、结构更改或跨越多个位置的更改），请描述问题和建议的修复，而不使用建议块
   - 切勿发布可提交的建议 UNLESS 提交建议可以完全解决问题。如果需要采取后续步骤，请勿留下可提交的建议。

**IMPORTANT：仅针对每个独特问题发布 ONE 评论。请勿发表重复评论。**

在评估步骤 4 和 5 中的问题时使用此列表（这些是误报，请执行 NOT 标记）：

- 先前存在的问题
- 看似错误但实际上是正确的东西
- 高级工程师不会指出的迂腐的挑剔
- linter 将捕获的问题（不要运行 linter 来验证）
- 除非 CLAUDE.md 中明确要求，否则一般代码质量问题（例如，缺乏测试覆盖率、一般安全问题）
- CLAUDE.md 中提到的问题，但在代码中明确静音（例如，通过 lint 忽略注释）

注意事项：

- 使用 gh CLI 与 GitHub 交互（例如，获取拉取请求、创建评论）。不要使用网络获取。
- 在开始之前创建一个待办事项列表。
- 您必须在内联注释中引用并链接每个问题（例如，如果引用 CLAUDE.md，请包含指向它的链接）。
- 如果没有发现问题并且提供了 `--comment` 参数，请使用以下格式发表评论：

---

## 代码审查

没有发现问题。检查了错误和 CLAUDE.md 合规性。

---

- 链接到内联注释中的代码时，请严格遵循以下格式，否则 Markdown 预览将无法正确呈现：https://github.com/anthropics/claude-code/blob/c21d3c10bc8e898b7ac1a2d745bdc9bc4e423afe/package.json#L10-L15
  - 需要完整的 git sha
  - 您必须提供完整的 sha。像 `https://github.com/owner/repo/blob/$(git rev-parse HEAD)/foo/bar` 这样的命令将不起作用，因为你的评论将直接在 Markdown 中呈现。
  - 存储库名称必须与您正在审查代码的存储库相匹配
  - # 文件名后的符号
  - 行范围格式为 L[start]-L[end]
  - 前后至少提供 1 行上下文，以您要评论的行为中心（例如，如果您评论行 5-6，则应链接到 `L4-7`）


## 原文对照

<details>
<summary>展开完整原文</summary>

Provide a code review for the given pull request.

**Agent assumptions (applies to all agents and subagents):**
- All tools are functional and will work without error. Do not test tools or make exploratory calls. Make sure this is clear to every subagent that is launched.
- Only call a tool if it is required to complete the task. Every tool call should have a clear purpose.

To do this, follow these steps precisely:

1. Launch a haiku agent to check if any of the following are true:
   - The pull request is closed
   - The pull request is a draft
   - The pull request does not need code review (e.g. automated PR, trivial change that is obviously correct)
   - Claude has already commented on this PR (check `gh pr view <PR> --comments` for comments left by claude)

   If any condition is true, stop and do not proceed.

Note: Still review Claude generated PR's.

2. Launch a haiku agent to return a list of file paths (not their contents) for all relevant CLAUDE.md files including:
   - The root CLAUDE.md file, if it exists
   - Any CLAUDE.md files in directories containing files modified by the pull request

3. Launch a sonnet agent to view the pull request and return a summary of the changes

4. Launch 4 agents in parallel to independently review the changes. Each agent should return the list of issues, where each issue includes a description and the reason it was flagged (e.g. "CLAUDE.md adherence", "bug"). The agents should do the following:

   Agents 1 + 2: CLAUDE.md compliance sonnet agents
   Audit changes for CLAUDE.md compliance in parallel. Note: When evaluating CLAUDE.md compliance for a file, you should only consider CLAUDE.md files that share a file path with the file or parents.

   Agent 3: Opus bug agent (parallel subagent with agent 4)
   Scan for obvious bugs. Focus only on the diff itself without reading extra context. Flag only significant bugs; ignore nitpicks and likely false positives. Do not flag issues that you cannot validate without looking at context outside of the git diff.

   Agent 4: Opus bug agent (parallel subagent with agent 3)
   Look for problems that exist in the introduced code. This could be security issues, incorrect logic, etc. Only look for issues that fall within the changed code.

   **CRITICAL: We only want HIGH SIGNAL issues.** Flag issues where:
   - The code will fail to compile or parse (syntax errors, type errors, missing imports, unresolved references)
   - The code will definitely produce wrong results regardless of inputs (clear logic errors)
   - Clear, unambiguous CLAUDE.md violations where you can quote the exact rule being broken

   Do NOT flag:
   - Code style or quality concerns
   - Potential issues that depend on specific inputs or state
   - Subjective suggestions or improvements

   If you are not certain an issue is real, do not flag it. False positives erode trust and waste reviewer time.

   In addition to the above, each subagent should be told the PR title and description. This will help provide context regarding the author's intent.

5. For each issue found in the previous step by agents 3 and 4, launch parallel subagents to validate the issue. These subagents should get the PR title and description along with a description of the issue. The agent's job is to review the issue to validate that the stated issue is truly an issue with high confidence. For example, if an issue such as "variable is not defined" was flagged, the subagent's job would be to validate that is actually true in the code. Another example would be CLAUDE.md issues. The agent should validate that the CLAUDE.md rule that was violated is scoped for this file and is actually violated. Use Opus subagents for bugs and logic issues, and sonnet agents for CLAUDE.md violations.

6. Filter out any issues that were not validated in step 5. This step will give us our list of high signal issues for our review.

7. Output a summary of the review findings to the terminal:
   - If issues were found, list each issue with a brief description.
   - If no issues were found, state: "No issues found. Checked for bugs and CLAUDE.md compliance."

   If `--comment` argument was NOT provided, stop here. Do not post any GitHub comments.

   If `--comment` argument IS provided and NO issues were found, post a summary comment using `gh pr comment` and stop.

   If `--comment` argument IS provided and issues were found, continue to step 8.

8. Create a list of all comments that you plan on leaving. This is only for you to make sure you are comfortable with the comments. Do not post this list anywhere.

9. Post inline comments for each issue using `mcp__github_inline_comment__create_inline_comment` with `confirmed: true`. For each comment:
   - Provide a brief description of the issue
   - For small, self-contained fixes, include a committable suggestion block
   - For larger fixes (6+ lines, structural changes, or changes spanning multiple locations), describe the issue and suggested fix without a suggestion block
   - Never post a committable suggestion UNLESS committing the suggestion fixes the issue entirely. If follow up steps are required, do not leave a committable suggestion.

   **IMPORTANT: Only post ONE comment per unique issue. Do not post duplicate comments.**

Use this list when evaluating issues in Steps 4 and 5 (these are false positives, do NOT flag):

- Pre-existing issues
- Something that appears to be a bug but is actually correct
- Pedantic nitpicks that a senior engineer would not flag
- Issues that a linter will catch (do not run the linter to verify)
- General code quality concerns (e.g., lack of test coverage, general security issues) unless explicitly required in CLAUDE.md
- Issues mentioned in CLAUDE.md but explicitly silenced in the code (e.g., via a lint ignore comment)

Notes:

- Use gh CLI to interact with GitHub (e.g., fetch pull requests, create comments). Do not use web fetch.
- Create a todo list before starting.
- You must cite and link each issue in inline comments (e.g., if referring to a CLAUDE.md, include a link to it).
- If no issues are found and `--comment` argument is provided, post a comment with the following format:

---

## Code review

No issues found. Checked for bugs and CLAUDE.md compliance.

---

- When linking to code in inline comments, follow the following format precisely, otherwise the Markdown preview won't render correctly: https://github.com/anthropics/claude-code/blob/c21d3c10bc8e898b7ac1a2d745bdc9bc4e423afe/package.json#L10-L15
  - Requires full git sha
  - You must provide the full sha. Commands like `https://github.com/owner/repo/blob/$(git rev-parse HEAD)/foo/bar` will not work, since your comment will be directly rendered in Markdown.
  - Repo name must match the repo you're code reviewing
  - # sign after the file name
  - Line range format is L[start]-L[end]
  - Provide at least 1 line of context before and after, centered on the line you are commenting about (eg. if you are commenting about lines 5-6, you should link to `L4-7`)


</details>
