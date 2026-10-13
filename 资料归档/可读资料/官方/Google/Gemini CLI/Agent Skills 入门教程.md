# Agent Skills 入门教程

通过创建目录、定义技能和补充工具逻辑，展示首个技能的制作、发现验证和调用过程。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-04-30](<https://geminicli.com/docs/cli/tutorials/skills-getting-started/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/tutorials/skills-getting-started/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Agent Skills 入门教程.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 开始使用 Agent Skills

Agent Skills 以专业知识扩展Gemini CLI。在本教程中，
您将学习如何创建您的第一个技能、捆绑自定义逻辑以及在会话期间激活
它。

## 创建你的第一个技能

[标题为“创建你的第一个技能”的部分](<#创建你的第一个技能>)

技能由包含 `SKILL.md` 文件的目录和包含技能使用的参考资料或脚本的 
 子目录定义。
让我们创建一个 **API 审核员 ** 技能，该技能运行一个脚本来帮助您验证 
 是本地还是远程端点响应正确。

### 1。创建目录结构

[标题为“1。创建目录结构”的部分](<#1创建目录结构>)

第一步是为您的技能及其 
 脚本创建必要的文件夹。

**macOS/Linux**

终端窗口

```
mkdir -p .gemini/skills/api-auditor/scripts
```

**Windows (PowerShell)**

终端窗口

```
New-Item -ItemType Directory -Force -Path ".gemini\skills\api-auditor\scripts"
```

### 2。创建定义 (`SKILL.md`)

[标题为“2。创建定义 (SKILL.md)”的部分](<#2创建定义-skillmd>)

`SKILL.md` 文件定义了 agent 的技能用途和说明。
在 `.gemini/skills/api-auditor/SKILL.md` 创建文件。这告诉agent
*何时*使用该技能以及**如何表现。

```
---


name: api-auditor


description:


Expertise in auditing and testing API endpoints. Use when the user asks to


"check", "test", or "audit" a URL or API.


---


# API Auditor Instructions


You act as a QA engineer specialized in API reliability. When this skill is


active, you MUST:


1.  **Audit**: Use the bundled `scripts/audit.js` utility to check the status of


the provided URL.


2.  **Report**: Analyze the output (status codes, latency) and explain any


failures in plain English.


3.  **Secure**: Remind the user if they are testing a sensitive endpoint without


an `https://` protocol.
```

### 3。添加工具逻辑

[标题为“3。添加工具逻辑”的部分](<#3添加工具逻辑>)

技能可以捆绑脚本等资源来执行确定性任务。在 `.gemini/skills/api-auditor/scripts/audit.js` 处创建 
 文件。这是
agent 将运行的代码。

.gemini/skills/api-auditor/scripts/audit.js

```
const url = process.argv[2];


if (!url) {


console.error('Usage: node audit.js <url>');


process.exit(1);


}


console.log(`Auditing ${url}...`);


fetch(url, { method: 'HEAD' })


.then((r) => console.log(`Result: Success (Status ${r.status})`))


.catch((e) => console.error(`Result: Failed (${e.message})`));
```

## 验证发现

[标题为“验证发现”的部分](<#验证发现>)

Gemini CLI 自动发现 `.gemini/skills` 目录中的技能（如 
 以及 `.agents/skills` 别名）。

要检查 Gemini CLI 是否找到了您的新技能，请在交互式会话中使用 `/skills list` 命令 
：

终端窗口

```
/skills list
```

您应该在可用技能列表中看到 `api-auditor`。如果您刚刚添加了
文件，则可以运行`/skills reload`来刷新列表，而无需重新启动
会话。

### 如果你的技能没有出现

[标题为“如果你的技能没有出现”的部分](<#如果你的技能没有出现>)

如果 `/skills list` 没有显示您的技能，请检查以下内容：

1. **该文件夹必须受信任（仅限工作区技能）。** 下的技能
仅当工作区文件夹被 
 标记为可信时才加载 `<workspace>/.gemini/skills/`。运行 `/trust` 并根据需要重新启动会话。 `~/.gemini/skills/`（用户范围）下的技能
不受信任影响。
2. **检查路径布局。** `SKILL.md` 被发现在
技能目录 (`.gemini/skills/SKILL.md`) 或深层目录 
 (`.gemini/skills/<skill-name>/SKILL.md`)。推荐的布局为每个技能使用一个 
 子目录，因此您可以将脚本和其他资源 
 与其一起捆绑。不会发现嵌套超过一个目录深度的文件。
3. **文件名必须完全是`SKILL.md`。**大小写有关
区分大小写的文件系统（Linux 和 macOS，当如此配置时）：
 `skill.md` 或 `Skill.md` 将被忽略。
4. **Frontmatter 必须同时包含 `name:` 和 `description:`，并且必须是
   文件中的第一件事。** A `SKILL.md` 如果任一字段都被静默跳过
   缺失，如果分隔符 (`---` 在他们自己的线路上）不存在，或者如果
   任何文本（ H1 标题、评论，甚至是空行）出现在
   开幕 `---`.
5. **技能名称来自`name:`字段，而不是目录名称。**如果
你的 frontmatter 写的是 `name: foo`，该技能在 
 `/skills list` 中显示为 `foo`，无论其父目录叫什么。名称中的
字符`: \ / < > * ? " |`被替换为`-`。

## 如何使用技能

[标题为“如何使用技能”的部分](<#如何使用技能>)

现在该技能已被发现，您可以通过询问
相关问题来触发其激活。

1. **触发器**：开始一个新会话并询问：“你可以审核<https://google.com>”
2. **激活**：Gemini标识该请求与`api-auditor`匹配
描述并调用`activate_skill`工具。
3. **同意**：您将看到确认提示。输入 **y** 进行批准。
4. **执行**：激活后，Gemini使用`run_shell_command`工具
执行捆绑的脚本：
 `node .gemini/skills/api-auditor/scripts/audit.js https://google.com`

## 专业提示：使用技能创建器

[标题为“专业提示：使用技能创建器”的部分](<#专业提示使用技能创建器>)

如果您不想手动创建文件，可以使用内置的
`skill-creator`技能。只需询问Gemini：

> “创建一项名为‘api-auditor’的新技能，用于测试 URL 是否响应。”

`skill-creator` 将为您处理目录结构和样板文件。

## 管理技能

[标题为“管理技能”的部分](<#管理技能>)

您还可以使用终端中的 `gemini skills` 命令来管理技能：

* **安装**：`gemini skills install <url-or-path>`
* **链接**：`gemini skills link <path>`（对于本地开发有用）
* **卸载**：`gemini skills uninstall <name>`

## 后续步骤

[标题为“后续步骤”的部分](<#后续步骤>)

* [创建Agent Skills](<https://geminicli.com/docs/cli/creating-skills>)：高级详细指南
技能特征和元数据。
* [使用Agent Skills](<https://geminicli.com/docs/cli/using-agent-skills>)：更多方法来发现和
管理您的技能库。
* [技能最佳实践](<https://geminicli.com/docs/cli/skills-best-practices>)：学习如何设计
可靠且有效的专业知识。

最后更新：四月 30、2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Get started with Agent Skills

Agent Skills extend Gemini CLI with specialized expertise. In this tutorial,
you’ll learn how to create your first skill, bundle custom logic, and activate
it during a session.

## Create your first skill

[Section titled “Create your first skill”](<#create-your-first-skill>)

A skill is defined by a directory containing a `SKILL.md` file and
subdirectories containing reference materials or scripts used by the skill.
Let’s create an **API Auditor** skill that runs a script to help you verify if
local or remote endpoints are responding correctly.

### 1. Create the directory structure

[Section titled “1. Create the directory structure”](<#1-create-the-directory-structure>)

The first step is to create the necessary folders for your skill and its
scripts.

**macOS/Linux**

Terminal window

```
mkdir -p .gemini/skills/api-auditor/scripts
```

**Windows (PowerShell)**

Terminal window

```
New-Item -ItemType Directory -Force -Path ".gemini\skills\api-auditor\scripts"
```

### 2. Create the definition (`SKILL.md`)

[Section titled “2. Create the definition (SKILL.md)”](<#2-create-the-definition-skillmd>)

The `SKILL.md` file defines the skill’s purpose and instructions for the agent.
Create a file at `.gemini/skills/api-auditor/SKILL.md`. This tells the agent
*when* to use the skill and *how* to behave.

```
---


name: api-auditor


description:


Expertise in auditing and testing API endpoints. Use when the user asks to


"check", "test", or "audit" a URL or API.


---


# API Auditor Instructions


You act as a QA engineer specialized in API reliability. When this skill is


active, you MUST:


1.  **Audit**: Use the bundled `scripts/audit.js` utility to check the status of


the provided URL.


2.  **Report**: Analyze the output (status codes, latency) and explain any


failures in plain English.


3.  **Secure**: Remind the user if they are testing a sensitive endpoint without


an `https://` protocol.
```

### 3. Add the tool logic

[Section titled “3. Add the tool logic”](<#3-add-the-tool-logic>)

Skills can bundle resources like scripts to perform deterministic tasks. Create
a file at `.gemini/skills/api-auditor/scripts/audit.js`. This is the code the
agent will run.

.gemini/skills/api-auditor/scripts/audit.js

```
const url = process.argv[2];


if (!url) {


console.error('Usage: node audit.js <url>');


process.exit(1);


}


console.log(`Auditing ${url}...`);


fetch(url, { method: 'HEAD' })


.then((r) => console.log(`Result: Success (Status ${r.status})`))


.catch((e) => console.error(`Result: Failed (${e.message})`));
```

## Verify discovery

[Section titled “Verify discovery”](<#verify-discovery>)

Gemini CLI automatically discovers skills in the `.gemini/skills` directory (as
well as the `.agents/skills` alias).

To check if Gemini CLI found your new skill, use the `/skills list` command
within an interactive session:

Terminal window

```
/skills list
```

You should see `api-auditor` in the list of available skills. If you just added
the files, you can run `/skills reload` to refresh the list without restarting
the session.

### If your skill doesn’t appear

[Section titled “If your skill doesn’t appear”](<#if-your-skill-doesnt-appear>)

If `/skills list` doesn’t show your skill, check the following:

1. **The folder must be trusted (workspace skills only).** Skills under
   `<workspace>/.gemini/skills/` are only loaded when the workspace folder is
   marked as trusted. Run `/trust` and restart the session if needed. Skills
   under `~/.gemini/skills/` (user scope) are not affected by trust.
2. **Check the path layout.** `SKILL.md` is discovered either at the root of
   the skills directory (`.gemini/skills/SKILL.md`) or one directory deep
   (`.gemini/skills/<skill-name>/SKILL.md`). The recommended layout uses a
   subdirectory per skill so you can bundle scripts and other resources
   alongside it. Files nested more than one directory deep are not discovered.
3. **The filename must be exactly `SKILL.md`.** Capitalization matters on
   case-sensitive filesystems (Linux, and macOS when configured as such):
   `skill.md` or `Skill.md` will be ignored.
4. **Frontmatter must include both `name:` and `description:`, and must be the
   first thing in the file.** A `SKILL.md` is silently skipped if either field
   is missing, if the delimiters (`---` on their own lines) are absent, or if
   any text (an H1 title, a comment, even a blank line) appears before the
   opening `---`.
5. **The skill name comes from the `name:` field, not the directory name.** If
   your frontmatter says `name: foo`, the skill appears as `foo` in
   `/skills list` regardless of what its parent directory is called. The
   characters `: \ / < > * ? " |` in the name are replaced with `-`.

## How to use the skill

[Section titled “How to use the skill”](<#how-to-use-the-skill>)

Now that the skill is discovered, you can trigger its activation by asking a
relevant question.

1. **Trigger**: Start a new session and ask: “Can you audit <https://google.com>”
2. **Activation**: Gemini identifies that the request matches the `api-auditor`
   description and calls the `activate_skill` tool.
3. **Consent**: You will see a confirmation prompt. Type **y** to approve.
4. **Execution**: Once activated, Gemini uses the `run_shell_command` tool to
   execute your bundled script:
   `node .gemini/skills/api-auditor/scripts/audit.js https://google.com`

## Pro tip: Use the skill-creator

[Section titled “Pro tip: Use the skill-creator”](<#pro-tip-use-the-skill-creator>)

If you don’t want to create the files manually, you can use the built-in
`skill-creator` skill. Simply ask Gemini:

> “Create a new skill called ‘api-auditor’ that tests if URLs are responding.”

The `skill-creator` will handle the directory structure and boilerplate for you.

## Manage skills

[Section titled “Manage skills”](<#manage-skills>)

You can also manage skills using the `gemini skills` command from your terminal:

* **Install**: `gemini skills install <url-or-path>`
* **Link**: `gemini skills link <path>` (useful for local development)
* **Uninstall**: `gemini skills uninstall <name>`

## Next steps

[Section titled “Next steps”](<#next-steps>)

* [Creating Agent Skills](<https://geminicli.com/docs/cli/creating-skills>): Detailed guide on advanced
  skill features and metadata.
* [Using Agent Skills](<https://geminicli.com/docs/cli/using-agent-skills>): More ways to discover and
  manage your skill library.
* [Skill best practices](<https://geminicli.com/docs/cli/skills-best-practices>): Learn how to design
  reliable and effective expertise.

Last updated: Apr 30, 2026


</details>
