# Continue 可复用提示、斜杠命令与自动任务

产品指南。说明将 Markdown 设为可调用提示，结合代码上下文、工具和规则，在 CLI 交互或非交互模式重复运行任务；包含数据库函数开发样例。

- 来源：Continue
- 资料类型：产品指南
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/bd36638ee0cf9fb90c314647bab2f1c6897aa6fe>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：提示模板是操作材料，实际代码改动仍需验证。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/prompts.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/Continue 可复用提示、斜杠命令与自动任务.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "How to Create and Manage Prompts in Continue"
description: "Prompts are used to kick off tasks for Agent mode, Plan mode, and Chat mode"
keywords: [prompts, context, slash command]
sidebarTitle: "Prompts"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

提示作为用户消息包含在内，作为重复性 and/or 复杂任务的说明特别有用。

## 斜线命令

通过将 `invokable` 设置为 `true`，您可以使 Markdown 文件成为提示，当您在聊天、计划和 Agent 模式中键入 `/` 时，该提示将可用。

**代码示例：** `explain-invokable.md`

```md
---
name: Explain invokable
description: Explains what happens when you set invokable to true
invokable: true
---

Explain that when `invokable` is set to `true`, a slash command becomes available in the IDE extensions and CLI
```

这些斜杠命令可以与其他指令（包括突出显示的代码）结合使用，以提供额外的上下文。

## 示例：`Create Supabase functions`提示

以下是生成遵循最佳实践的高质量 PostgreSQL 函数的提示：

**代码示例：** `supabase-create-functions.md`

```md
---
name: Create Supabase functions
description: Guidelines for writing Supabase database functions
invokable: true
---

# Database: Create functions

You're a Supabase Postgres expert in writing database functions. Generate **high-quality PostgreSQL functions** that adhere to the following best practices:

## General Guidelines

1. **Default to `SECURITY INVOKER`:**

   - Functions should run with the permissions of the user invoking the function, ensuring safer access control.
   - Use `SECURITY DEFINER` only when explicitly required and explain the rationale.

2. **Set the `search_path` Configuration Parameter:**

   - Always set `search_path` to an empty string (`set search_path = '';`).
   - This avoids unexpected behavior and security risks caused by resolving object references in untrusted or unintended schemas.
   - Use fully qualified names (e.g., `schema_name.table_name`) for all database objects referenced within the function.

3. **Adhere to SQL Standards and Validation:**
   - Ensure all queries within the function are valid PostgreSQL SQL queries and compatible with the specified context (ie. Supabase).

...
```

**说明：**

您可以阅读其余部分 `Create Supabase functions` 提示 [这里](<http://continue.dev/supabase/create-functions>)

如果您使用本地 `config.yaml`，您可以将其添加到您的配置中，如下所示：

**代码示例：** `config.yaml`

```md
...

prompts:
  - uses: supabase/create-functions

...
```

要使用此提示，您可以打开聊天/Agent/编辑，键入`/`，选择提示，然后键入您要添加的任何其他说明。

## 使用带有 `cn (TUI mode)` 的提示

您可以运行此命令来启动 [cn](<https://docs.continue.dev/guides/cli>)，并显示 [Create Supabase 函数](<http://continue.dev/supabase/create-functions>) 提示符。

```
cn --prompt supabase/create-functions "I need a function that checks for the health status"
```
或者，您可以启动 [cn](<https://docs.continue.dev/guides/cli>)，然后输入 `/` 自己手动调用提示符。

## 使用提示启动带有 `cn (Headless mode)` 的连续 AI 工作流程

您可以通过添加 `-p` 标志，使用带有 [cn](<https://docs.continue.dev/guides/cli>) 的提示来启动连续 AI 工作流程。

例如，假设您正在构建 SaaS 应用程序，并且必须为接受用户输入的每个新功能重复创建自定义 Supabase 验证函数。

这些功能要求您解释业务需求，实现复杂的跨表逻辑（例如检查用户权限、层限制和基于时间的限制），并对边缘情况进行判断。

每个函数都是独一无二的，无法对其进行模板化或脚本化。在这里，启动连续的 AI 工作流程对您很有帮助。

当您有新功能时，您可以运行以下命令：

```
 cn -p --prompt supabase/create-functions "I need a function for the new feature on my current branch similar to my existing database functions"
```

**说明：**

你可以看到整个 `Create Supabase functions` 提示 [这里](<http://continue.dev/supabase/create-functions>)

当您运行此工作流程时，[cn](<https://docs.continue.dev/guides/cli>)将检查您当前的分支，探索新的和现有的代码，然后为您起草一个函数。

然后，您将能够在合并新功能之前检查实施情况并对其进行改进。


## 原文对照

<details>
<summary>展开完整原文</summary>

Prompts are included as user messages and are especially useful as instructions for repetitive and/or complex tasks.

## Slash Commands

By setting `invokable` to `true`, you make the markdown file a prompt, which will be available when you type `/` in Chat, Plan, and Agent mode.

**代码示例：** `explain-invokable.md`

```md
---
name: Explain invokable
description: Explains what happens when you set invokable to true
invokable: true
---

Explain that when `invokable` is set to `true`, a slash command becomes available in the IDE extensions and CLI
```

These slash commands can be combined with other instructions, including highlighted code, to provide additional context.

## Example: `Create Supabase functions` prompt

Here is a prompt that generates high-quality PostgreSQL functions that adhere to best practices:

**代码示例：** `supabase-create-functions.md`

```md
---
name: Create Supabase functions
description: Guidelines for writing Supabase database functions
invokable: true
---

# Database: Create functions

You're a Supabase Postgres expert in writing database functions. Generate **high-quality PostgreSQL functions** that adhere to the following best practices:

## General Guidelines

1. **Default to `SECURITY INVOKER`:**

   - Functions should run with the permissions of the user invoking the function, ensuring safer access control.
   - Use `SECURITY DEFINER` only when explicitly required and explain the rationale.

2. **Set the `search_path` Configuration Parameter:**

   - Always set `search_path` to an empty string (`set search_path = '';`).
   - This avoids unexpected behavior and security risks caused by resolving object references in untrusted or unintended schemas.
   - Use fully qualified names (e.g., `schema_name.table_name`) for all database objects referenced within the function.

3. **Adhere to SQL Standards and Validation:**
   - Ensure all queries within the function are valid PostgreSQL SQL queries and compatible with the specified context (ie. Supabase).

...
```

**说明：**

You can read the rest of the `Create Supabase functions` prompt [here](<http://continue.dev/supabase/create-functions>)

If you are using a local `config.yaml`, you can add it to your config like this:

**代码示例：** `config.yaml`

```md
...

prompts:
  - uses: supabase/create-functions

...
```

To use this prompt, you can open Chat / Agent / Edit, type `/`, select the prompt, and type out any additional instructions you'd like to add.

## Using a prompt with `cn (TUI mode)`

You can run this command to start [cn](<https://docs.continue.dev/guides/cli>) with the [Create Supabase functions](<http://continue.dev/supabase/create-functions>) prompt.

```
cn --prompt supabase/create-functions "I need a function that checks for the health status"
```
Alternatively, you can start [cn](<https://docs.continue.dev/guides/cli>) and then type `/` to manually invoke the prompt yourself.

## Using a prompt to kick off a Continuous AI workflow with `cn (Headless mode)`

You can kick off Continuous AI workflows using a prompt with [cn](<https://docs.continue.dev/guides/cli>) by adding the `-p` flag.

For example, say you are building a SaaS application and must repeatedly create custom Supabase validation functions for each new feature that accepts user input. 

These functions require you to interpret business requirements, implement complex cross-table logic (like checking user permissions, tier limits, and time-based restrictions), and make judgment calls about edge cases. 

Each function is unique enough that it can't be templated or scripted. This is where kicking off a Continuous AI workflow to get you started can be quite helpful.

Here is a command that you could run whenever you have a new feature:

```
 cn -p --prompt supabase/create-functions "I need a function for the new feature on my current branch similar to my existing database functions"
```

**说明：**

You can see the entire `Create Supabase functions` prompt [here](<http://continue.dev/supabase/create-functions>)

When you run this workflow, [cn](<https://docs.continue.dev/guides/cli>) will checkout your current branch, explore the new and existing code, and then draft a function for you.

You will then be able to review the implementation and improve it before you merge the new feature.


</details>
