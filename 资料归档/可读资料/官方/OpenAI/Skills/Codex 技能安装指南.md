# Codex 技能安装指南

说明从精选目录或 GitHub 仓库安装技能的脚本、路径、选项和结果反馈，包含私有仓库接入情况。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-06-24](<https://github.com/openai/skills/commit/49f948faa9258a0c61caceaf225e179651397431>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.system/skill-installer/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Codex 技能安装指南.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: skill-installer
description: Install Codex skills into $CODEX_HOME/skills from a curated list or a GitHub repo path. Use when a user asks to list installable skills, install a curated skill, or install a skill from another repo (including private repos).
metadata:
  short-description: Install curated skills from openai/skills or other repos
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 技能安装员

帮助安装技能。默认情况下，这些位置来自 https://github.com/openai/skills/tree/main/skills/.curated,，但用户也可以提供其他位置。

根据任务使用帮助程序脚本：
- 当用户询问可用的技能或用户使用此技能但未指定要执行的操作时，列出技能。默认列表是`.curated`，但是当他们询问实验技能时，你可以通过`--path skills/.experimental`。
- 当用户提供技能名称时，从精选列表中安装。
- 当用户提供 GitHub repo/path（包括私人存储库）时，从另一个存储库安装。

使用帮助程序脚本安装技能。

## 通讯

列出技能时，根据用户请求的上下文，输出大致如下。如果他们询问实验技能，请列出 `.experimental` 而不是 `.curated`，并相应地标记来源：
"""
来自 {repo} 的技能：
1. 技能-1
2. 技能-2（已安装）
3. ...
您想要安装哪些？
"""

安装技能后，告诉用户该技能将在下一轮可用。

## 脚本

所有这些脚本都使用网络，因此在沙箱中运行时，请在运行时请求升级。

- `scripts/list-skills.py`（打印带有已安装注释的技能列表）
- `scripts/list-skills.py --format json`
- 示例（实验列表）：`scripts/list-skills.py --path skills/.experimental`
- `scripts/install-skill-from-github.py --repo <owner>/<repo> --path <path/to/skill> [<path/to/skill> ...]`
- `scripts/install-skill-from-github.py --url https://github.com/<owner>/<repo>/tree/<ref>/<path>`
- 示例（实验技能）：`scripts/install-skill-from-github.py --repo openai/skills --path skills/.experimental/<skill-name>`

## 行为和选项

- 默认为直接下载公共 GitHub 存储库。
- 如果下载失败并出现 auth/permission 错误，则返回到 git稀疏结帐。
- 如果目标技能目录已存在，则中止。
- 安装到 `$CODEX_HOME/skills/<skill-name>`（默认为 `~/.codex/skills`）。
- 多个 `--path` 值在一次运行中安装多个技能，每个技能均从路径基本名称命名，除非提供 `--name`。
- 选项：`--ref <ref>`（默认为`main`）、`--dest <path>`、`--method auto|download|git`。

## 注释

- 精选列表通过 GitHub API 从 `https://github.com/openai/skills/tree/main/skills/.curated` 获取。如果不可用，请解释错误并退出。
- 私人 GitHub 存储库可以通过现有的 git 凭据或可选的 `GITHUB_TOKEN`/`GH_TOKEN` 进行下载来访问。
- Git 回退首先尝试 HTTPS，然后尝试 SSH。
- https://github.com/openai/skills/tree/main/skills/.system的技能是预装的，所以不需要帮助用户安装。如果他们问起，只需解释一下即可。如果他们坚持，您可以下载并覆盖。
- 安装的注释来自`$CODEX_HOME/skills`。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Skill Installer

Helps install skills. By default these are from https://github.com/openai/skills/tree/main/skills/.curated, but users can also provide other locations.

Use the helper scripts based on the task:
- List skills when the user asks what is available, or if the user uses this skill without specifying what to do. Default listing is `.curated`, but you can pass `--path skills/.experimental` when they ask about experimental skills.
- Install from the curated list when the user provides a skill name.
- Install from another repo when the user provides a GitHub repo/path (including private repos).

Install skills with the helper scripts.

## Communication

When listing skills, output approximately as follows, depending on the context of the user's request. If they ask about experimental skills, list from `.experimental` instead of `.curated` and label the source accordingly:
"""
Skills from {repo}:
1. skill-1
2. skill-2 (already installed)
3. ...
Which ones would you like installed?
"""

After installing a skill, tell the user it will be available on their next turn.

## Scripts

All of these scripts use network, so when running in the sandbox, request escalation when running them.

- `scripts/list-skills.py` (prints skills list with installed annotations)
- `scripts/list-skills.py --format json`
- Example (experimental list): `scripts/list-skills.py --path skills/.experimental`
- `scripts/install-skill-from-github.py --repo <owner>/<repo> --path <path/to/skill> [<path/to/skill> ...]`
- `scripts/install-skill-from-github.py --url https://github.com/<owner>/<repo>/tree/<ref>/<path>`
- Example (experimental skill): `scripts/install-skill-from-github.py --repo openai/skills --path skills/.experimental/<skill-name>`

## Behavior and Options

- Defaults to direct download for public GitHub repos.
- If download fails with auth/permission errors, falls back to git sparse checkout.
- Aborts if the destination skill directory already exists.
- Installs into `$CODEX_HOME/skills/<skill-name>` (defaults to `~/.codex/skills`).
- Multiple `--path` values install multiple skills in one run, each named from the path basename unless `--name` is supplied.
- Options: `--ref <ref>` (default `main`), `--dest <path>`, `--method auto|download|git`.

## Notes

- Curated listing is fetched from `https://github.com/openai/skills/tree/main/skills/.curated` via the GitHub API. If it is unavailable, explain the error and exit.
- Private GitHub repos can be accessed via existing git credentials or optional `GITHUB_TOKEN`/`GH_TOKEN` for download.
- Git fallback tries HTTPS first, then SSH.
- The skills at https://github.com/openai/skills/tree/main/skills/.system are preinstalled, so no need to help users install those. If they ask, just explain this. If they insist, you can download and overwrite.
- Installed annotations come from `$CODEX_HOME/skills`.


</details>
