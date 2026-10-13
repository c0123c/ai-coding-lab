# Gemini CLI 自定义命令

介绍保存常用提示、命令位置与优先级、TOML 格式和参数注入，帮助建立可复用的个人或项目快捷操作。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-04-30](<https://geminicli.com/docs/cli/custom-commands/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/custom-commands/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 自定义命令.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 自定义命令

自定义命令可让您保存并重复使用您最喜欢或最常用的
提示作为Gemini CLI中的个人快捷方式。您可以创建
特定于单个项目的命令或在
所有项目中全局可用的命令，从而简化您的工作流程并确保一致性。

## 文件位置和优先级

[标题为“文件位置和优先级”的部分](<#文件位置和优先级>)

Gemini CLI 从两个位置发现命令，并按特定顺序加载：

1. **用户命令（全局）：**位于`~/.gemini/commands/`。这些命令
可用于您正在从事的任何项目。
2. **项目命令（本地）：** 位于
`<your-project-root>/.gemini/commands/`。这些命令特定于 
 当前项目，可以签入版本控制以与 
 您的团队共享。

如果项目目录中的命令与用户
目录中的命令同名，则将始终使用**项目命令。**这允许项目
使用项目特定版本覆盖全局命令。

## 命名和命名空间

[标题为“命名和命名空间”的部分](<#命名和命名空间>)

命令的名称由其相对于 `commands`
 目录的文件路径确定。子目录用于创建命名空间命令，路径
分隔符（`/`或`\`）被转换为冒号（`:`）。

* `~/.gemini/commands/test.toml`处的文件成为命令`/test`。
* `<project>/.gemini/commands/git/commit.toml` 处的文件成为命名空间
命令`/git:commit`。

## TOML 文件格式 (v1)

[标题为“TOML文件格式(v1)”的部分](<#toml-文件格式-v1>)

您的命令定义文件必须以 TOML 格式编写并使用 
`.toml` 文件扩展名。

### 必填字段

[标题为“必填字段”的部分](<#必填字段>)

* `prompt`（字符串）：当Gemini模型
命令被执行。这可以是单行或多行字符串。

### 可选字段

[标题为“可选字段”的部分](<#可选字段>)

* `description`（字符串）：命令内容的简短一行描述
确实如此。此文本将显示在 `/help` 菜单中的命令旁边。
 **如果省略此字段，将从 
 文件名生成通用描述。**

## 处理参数

[标题为“处理参数”的部分](<#处理参数>)

自定义命令支持两种强大的参数处理方法。 CLI
会根据您的命令
`prompt`的内容自动选择正确的方法。

### 1。使用 `{{args}}` 进行上下文感知注入

[标题为“1。上下文感知注入{{args}}”的部分](<#1使用-args-进行上下文感知注入>)

如果您的`prompt`包含特殊占位符`{{args}}`，则CLI将
将该占位符替换为用户在命令名称后键入的文本。

此注入的行为取决于它的使用位置：

**A.原始注入（外部 shell 命令）**

当在提示的主体中使用时，参数的注入与用户键入的
完全相同。

**示例（`git/fix.toml`）：**

```
# Invoked via: /git:fix "Button is misaligned"


description = "Generates a fix for a given issue."


prompt = "Please provide a code fix for the issue described here: {{args}}."
```

型号收到：
`Please provide a code fix for the issue described here: "Button is misaligned".`

**B.在 shell 命令中使用参数（在 `!{...}` 块内）**

当您在 shell 注入块 (`!{...}`) 中使用 `{{args}}` 时，参数 
 在替换之前会自动 **shell 转义 **。这可以让您安全地
将参数传递给 shell 命令，确保生成的命令
语法正确且安全，同时防止命令注入
漏洞。

**示例（`/grep-code.toml`）：**

```
prompt = """


Please summarize the findings for the pattern `{{args}}`.


Search Results:


!{grep -r {{args}} .}


"""
```

当您运行`/grep-code It's complicated`时：

1. CLI 看到`{{args}}` 在`!{...}` 的外部和内部使用。
2. 外部：第一个 `{{args}}` 被原始替换为 `It's complicated`。
3. 内部：第二个 `{{args}}` 被替换为转义版本（对于
例如，在 Linux 上：`"It\'s complicated"`）。
4. 执行的命令是`grep -r "It's complicated" .`。
5. CLI 会提示您在执行之前确认此准确、安全的命令。
6. 最终提示已发送。

### 2。默认参数处理

[标题为“2。默认参数处理”的部分](<#2默认参数处理>)

如果您的 `prompt` 确实 ** 而不是 ** 包含特殊占位符 `{{args}}`，则 
CLI 使用默认行为来处理参数。

如果您为命令提供参数（例如，`/mycommand arg1`），
CLI 会将您键入的完整命令附加到提示符末尾，并用两个换行符分隔 
。这允许模型看到原始指令和
您刚刚提供的具体参数。

如果您**不**提供任何参数（例如，`/mycommand`），则提示
将按原样发送到模型，不附加任何内容。

**示例（`changelog.toml`）：**

此示例演示如何通过为 
 模型定义角色、解释在何处查找用户输入以及指定预期的 
 格式和行为来创建强大的命令。

```
# In: <project>/.gemini/commands/changelog.toml


# Invoked via: /changelog 1.2.0 added "Support for default argument parsing."


description = "Adds a new entry to the project's CHANGELOG.md file."


prompt = """


# Task: Update Changelog


You are an expert maintainer of this software project. A user has invoked a command to add a new entry to the changelog.


**The user's raw command is appended below your instructions.**


Your task is to parse the `<version>`, `<change_type>`, and `<message>` from their input and use the `write_file` tool to correctly update the `CHANGELOG.md` file.


## Expected Format


The command follows this format: `/changelog <version> <type> <message>`


- `<type>` must be one of: "added", "changed", "fixed", "removed".


## Behavior


1. Read the `CHANGELOG.md` file.


2. Find the section for the specified `<version>`.


3. Add the `<message>` under the correct `<type>` heading.


4. If the version or type section doesn't exist, create it.


5. Adhere strictly to the "Keep a Changelog" format.


"""
```

当您运行 `/changelog 1.2.0 added "New feature"` 时，发送到 
 模型的最终文本将是原始提示符，后跟两个换行符和您 
 键入的命令。

### 3。使用 `!{...}` 执行 shell 命令

[标题为“3。使用 !{...}”执行 shell 命令的部分](<#3-executing-shell-commands-with>)

您可以通过直接在内部执行 shell 命令来使命令动态化
你的 `prompt` 并注入它们的输出。这是收集背景信息的理想选择
从您的本地环境，例如读取文件内容或检查状态
吉特。

当自定义命令尝试执行 shell 命令时，Gemini CLI 现在将
 提示您确认后再继续。这是一项安全措施，旨在
确保仅可以运行预期的命令。

**工作原理：**

1. **注入命令：**使用`!{...}`语法。
2. **参数替换：** 如果 `{{args}}` 存在于块内，则它是
自动外壳转义（参见上面的
 [上下文感知注入](<#1使用-args-进行上下文感知注入>)）。
3. **强大的解析：**解析器正确处理复杂的shell命令
包括嵌套大括号，例如 JSON 有效负载。 `!{...}`
 内的内容必须有平衡的大括号（`{` 和 `}`）。如果您需要执行包含不平衡大括号的命令
，请考虑将其包装在外部脚本
文件中，并在`!{...}`块中调用该脚本。
4. **安全检查和确认：** CLI执行安全检查
最终的已解析命令（在参数被转义和替换之后）。将出现一个
对话框，显示要执行的确切命令。
5. **执行并报错：**命令执行完毕。如果命令
如果失败，注入到提示符中的输出将包含错误消息 
 (stderr)，后跟状态行，例如 
 `[Shell command exited with code 1]`。这有助于模型了解故障的 
 上下文。

**示例（`git/commit.toml`）：**

此命令获取暂存的 git diff 并使用它来要求模型写入 
 提交消息。

````
# In: <project>/.gemini/commands/git/commit.toml


# Invoked via: /git:commit


description = "Generates a Git commit message based on staged changes."


# The prompt uses !{...} to execute the command and inject its output.


prompt = """


Please generate a Conventional Commit message based on the following git diff:


```diff


!{git diff --staged}


```


"""
````

当您运行 `/git:commit` 时，CLI 首先执行 `git diff --staged`，然后 
 将 `!{git diff --staged}` 替换为该命令的输出，然后再向模型发送 
 最终完整的提示。

### 4。使用 `@{...}` 注入文件内容

[标题为“4。使用@{...}注入文件内容”的部分](<#4-injecting-file-content-with>)

您可以使用 `@{...}` 语法直接将文件或目录列表的内容嵌入到 
 提示符中。这对于创建
操作特定文件的命令很有用。

**工作原理：**

* **文件注入**：`@{path/to/file.txt}`替换为
  `file.txt`.
* **多模式支持**：如果路径指向支持的图像（例如，
PNG、JPEG）、PDF、音频或视频文件，它将被正确编码并 
 作为多模式输入注入。其他二进制文件被妥善处理并跳过
。
* **目录列表**：遍历`@{path/to/dir}`并且存在每个文件
目录内的所有子目录都会插入到提示符中。如果启用，此
 尊重`.gitignore` 和`.geminiignore`。
* **Workspace-aware**：该命令搜索当前的路径
目录和任何其他工作区目录。如果 
 位于工作空间内，则允许绝对路径。
* **处理顺序**：处理带有`@{...}`的文件内容注入
\* 在 * shell 命令 (`!{...}`) 和参数替换 (`{{args}}`) 之前。
* **解析**：解析器需要`@{...}`（路径）内的内容来
有平衡的大括号（`{` 和 `}`）。

**示例（`review.toml`）：**

此命令注入 * 固定 * 最佳实践文件 
(`docs/best-practices.md`) 的内容，并使用用户的参数为 
 审核提供上下文。

```
# In: <project>/.gemini/commands/review.toml


# Invoked via: /review FileCommandLoader.ts


description = "Reviews the provided context using a best practice guide."


prompt = """


You are an expert code reviewer.


Your task is to review {{args}}.


Use the following best practices when providing your review:


@{docs/best-practices.md}


"""
```

当您运行 `/review FileCommandLoader.ts` 时，`@{docs/best-practices.md}`
 占位符将替换为该文件的内容，并且 `{{args}}` 将替换为 
，然后将最终提示发送到模型。

---

## 示例：“纯函数”重构命令

[标题为“示例：“纯函数”重构命令”的部分](<#示例纯函数重构命令>)

让我们创建一个全局命令，要求模型重构一段代码。

**1。创建文件和目录：**

首先，确保用户命令目录存在，然后创建一个 `refactor`
 子目录用于组织和最终的 TOML 文件。

**macOS/Linux**

终端窗口

```
mkdir -p ~/.gemini/commands/refactor


touch ~/.gemini/commands/refactor/pure.toml
```

**Windows (PowerShell)**

终端窗口

```
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.gemini\commands\refactor"


New-Item -ItemType File -Force -Path "$env:USERPROFILE\.gemini\commands\refactor\pure.toml"
```

**2。将内容添加到文件中：**

在编辑器中打开`~/.gemini/commands/refactor/pure.toml`并添加
以下内容。我们包括可选的 `description` 以获得最佳 
 实践。

~/.gemini/commands/refactor/pure.toml

```
# This command will be invoked via: /refactor:pure


description = "Asks the model to refactor the current context into a pure function."


prompt = """


Please analyze the code I've provided in the current context.


Refactor it into a pure function.


Your response should include:


1. The refactored, pure function code block.


2. A brief explanation of the key changes you made and why they contribute to purity.


"""
```

**3。运行命令：**

就是这样！您现在可以在 CLI 中运行命令。首先，您可以将文件 
 添加到上下文，然后调用您的命令：

```
> @my-messy-function.js


> /refactor:pure
```

然后，Gemini CLI 将执行 TOML 文件中定义的多行提示符。

最后更新：四月 30、2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Custom commands

Custom commands let you save and reuse your favorite or most frequently used
prompts as personal shortcuts within Gemini CLI. You can create commands that
are specific to a single project or commands that are available globally across
all your projects, streamlining your workflow and ensuring consistency.

## File locations and precedence

[Section titled “File locations and precedence”](<#file-locations-and-precedence>)

Gemini CLI discovers commands from two locations, loaded in a specific order:

1. **User commands (global):** Located in `~/.gemini/commands/`. These commands
   are available in any project you are working on.
2. **Project commands (local):** Located in
   `<your-project-root>/.gemini/commands/`. These commands are specific to the
   current project and can be checked into version control to be shared with
   your team.

If a command in the project directory has the same name as a command in the user
directory, the **project command will always be used.** This allows projects to
override global commands with project-specific versions.

## Naming and namespacing

[Section titled “Naming and namespacing”](<#naming-and-namespacing>)

The name of a command is determined by its file path relative to its `commands`
directory. Subdirectories are used to create namespaced commands, with the path
separator (`/` or `\`) being converted to a colon (`:`).

* A file at `~/.gemini/commands/test.toml` becomes the command `/test`.
* A file at `<project>/.gemini/commands/git/commit.toml` becomes the namespaced
  command `/git:commit`.

## TOML file format (v1)

[Section titled “TOML file format (v1)”](<#toml-file-format-v1>)

Your command definition files must be written in the TOML format and use the
`.toml` file extension.

### Required fields

[Section titled “Required fields”](<#required-fields>)

* `prompt` (String): The prompt that will be sent to the Gemini model when the
  command is executed. This can be a single-line or multi-line string.

### Optional fields

[Section titled “Optional fields”](<#optional-fields>)

* `description` (String): A brief, one-line description of what the command
  does. This text will be displayed next to your command in the `/help` menu.
  **If you omit this field, a generic description will be generated from the
  filename.**

## Handling arguments

[Section titled “Handling arguments”](<#handling-arguments>)

Custom commands support two powerful methods for handling arguments. The CLI
automatically chooses the correct method based on the content of your command’s
`prompt`.

### 1. Context-aware injection with `{{args}}`

[Section titled “1. Context-aware injection with {{args}}”](<#1-context-aware-injection-with-args>)

If your `prompt` contains the special placeholder `{{args}}`, the CLI will
replace that placeholder with the text the user typed after the command name.

The behavior of this injection depends on where it is used:

**A. Raw injection (outside shell commands)**

When used in the main body of the prompt, the arguments are injected exactly as
the user typed them.

**Example (`git/fix.toml`):**

```
# Invoked via: /git:fix "Button is misaligned"


description = "Generates a fix for a given issue."


prompt = "Please provide a code fix for the issue described here: {{args}}."
```

The model receives:
`Please provide a code fix for the issue described here: "Button is misaligned".`

**B. Using arguments in shell commands (inside `!{...}` blocks)**

When you use `{{args}}` inside a shell injection block (`!{...}`), the arguments
are automatically **shell-escaped** before replacement. This lets you safely
pass arguments to shell commands, ensuring the resulting command is
syntactically correct and secure while preventing command injection
vulnerabilities.

**Example (`/grep-code.toml`):**

```
prompt = """


Please summarize the findings for the pattern `{{args}}`.


Search Results:


!{grep -r {{args}} .}


"""
```

When you run `/grep-code It's complicated`:

1. The CLI sees `{{args}}` used both outside and inside `!{...}`.
2. Outside: The first `{{args}}` is replaced raw with `It's complicated`.
3. Inside: The second `{{args}}` is replaced with the escaped version (for
   example, on Linux: `"It\'s complicated"`).
4. The command executed is `grep -r "It's complicated" .`.
5. The CLI prompts you to confirm this exact, secure command before execution.
6. The final prompt is sent.

### 2. Default argument handling

[Section titled “2. Default argument handling”](<#2-default-argument-handling>)

If your `prompt` does **not** contain the special placeholder `{{args}}`, the
CLI uses a default behavior for handling arguments.

If you provide arguments to the command (for example, `/mycommand arg1`), the
CLI will append the full command you typed to the end of the prompt, separated
by two newlines. This allows the model to see both the original instructions and
the specific arguments you just provided.

If you do **not** provide any arguments (for example, `/mycommand`), the prompt
is sent to the model exactly as it is, with nothing appended.

**Example (`changelog.toml`):**

This example shows how to create a robust command by defining a role for the
model, explaining where to find the user’s input, and specifying the expected
format and behavior.

```
# In: <project>/.gemini/commands/changelog.toml


# Invoked via: /changelog 1.2.0 added "Support for default argument parsing."


description = "Adds a new entry to the project's CHANGELOG.md file."


prompt = """


# Task: Update Changelog


You are an expert maintainer of this software project. A user has invoked a command to add a new entry to the changelog.


**The user's raw command is appended below your instructions.**


Your task is to parse the `<version>`, `<change_type>`, and `<message>` from their input and use the `write_file` tool to correctly update the `CHANGELOG.md` file.


## Expected Format


The command follows this format: `/changelog <version> <type> <message>`


- `<type>` must be one of: "added", "changed", "fixed", "removed".


## Behavior


1. Read the `CHANGELOG.md` file.


2. Find the section for the specified `<version>`.


3. Add the `<message>` under the correct `<type>` heading.


4. If the version or type section doesn't exist, create it.


5. Adhere strictly to the "Keep a Changelog" format.


"""
```

When you run `/changelog 1.2.0 added "New feature"`, the final text sent to the
model will be the original prompt followed by two newlines and the command you
typed.

### 3. Executing shell commands with `!{...}`

[Section titled “3. Executing shell commands with !{...}”](<#3-executing-shell-commands-with>)

You can make your commands dynamic by executing shell commands directly within
your `prompt` and injecting their output. This is ideal for gathering context
from your local environment, like reading file content or checking the status of
Git.

When a custom command attempts to execute a shell command, Gemini CLI will now
prompt you for confirmation before proceeding. This is a security measure to
ensure that only intended commands can be run.

**How it works:**

1. **Inject commands:** Use the `!{...}` syntax.
2. **Argument substitution:** If `{{args}}` is present inside the block, it is
   automatically shell-escaped (see
   [Context-Aware Injection](<#1-context-aware-injection-with-args>) above).
3. **Robust parsing:** The parser correctly handles complex shell commands that
   include nested braces, such as JSON payloads. The content inside `!{...}`
   must have balanced braces (`{` and `}`). If you need to execute a command
   containing unbalanced braces, consider wrapping it in an external script
   file and calling the script within the `!{...}` block.
4. **Security check and confirmation:** The CLI performs a security check on
   the final, resolved command (after arguments are escaped and substituted). A
   dialog will appear showing the exact command(s) to be executed.
5. **Execution and error reporting:** The command is executed. If the command
   fails, the output injected into the prompt will include the error messages
   (stderr) followed by a status line, for example,
   `[Shell command exited with code 1]`. This helps the model understand the
   context of the failure.

**Example (`git/commit.toml`):**

This command gets the staged git diff and uses it to ask the model to write a
commit message.

````
# In: <project>/.gemini/commands/git/commit.toml


# Invoked via: /git:commit


description = "Generates a Git commit message based on staged changes."


# The prompt uses !{...} to execute the command and inject its output.


prompt = """


Please generate a Conventional Commit message based on the following git diff:


```diff


!{git diff --staged}


```


"""
````

When you run `/git:commit`, the CLI first executes `git diff --staged`, then
replaces `!{git diff --staged}` with the output of that command before sending
the final, complete prompt to the model.

### 4. Injecting file content with `@{...}`

[Section titled “4. Injecting file content with @{...}”](<#4-injecting-file-content-with>)

You can directly embed the content of a file or a directory listing into your
prompt using the `@{...}` syntax. This is useful for creating commands that
operate on specific files.

**How it works:**

* **File injection**: `@{path/to/file.txt}` is replaced by the content of
  `file.txt`.
* **Multimodal support**: If the path points to a supported image (for example,
  PNG, JPEG), PDF, audio, or video file, it will be correctly encoded and
  injected as multimodal input. Other binary files are handled gracefully and
  skipped.
* **Directory listing**: `@{path/to/dir}` is traversed and each file present
  within the directory and all subdirectories is inserted into the prompt. This
  respects `.gitignore` and `.geminiignore` if enabled.
* **Workspace-aware**: The command searches for the path in the current
  directory and any other workspace directories. Absolute paths are allowed if
  they are within the workspace.
* **Processing order**: File content injection with `@{...}` is processed
  *before* shell commands (`!{...}`) and argument substitution (`{{args}}`).
* **Parsing**: The parser requires the content inside `@{...}` (the path) to
  have balanced braces (`{` and `}`).

**Example (`review.toml`):**

This command injects the content of a *fixed* best practices file
(`docs/best-practices.md`) and uses the user’s arguments to provide context for
the review.

```
# In: <project>/.gemini/commands/review.toml


# Invoked via: /review FileCommandLoader.ts


description = "Reviews the provided context using a best practice guide."


prompt = """


You are an expert code reviewer.


Your task is to review {{args}}.


Use the following best practices when providing your review:


@{docs/best-practices.md}


"""
```

When you run `/review FileCommandLoader.ts`, the `@{docs/best-practices.md}`
placeholder is replaced by the content of that file, and `{{args}}` is replaced
by the text you provided, before the final prompt is sent to the model.

---

## Example: A “Pure Function” refactoring command

[Section titled “Example: A “Pure Function” refactoring command”](<#example-a-pure-function-refactoring-command>)

Let’s create a global command that asks the model to refactor a piece of code.

**1. Create the file and directories:**

First, ensure the user commands directory exists, then create a `refactor`
subdirectory for organization and the final TOML file.

**macOS/Linux**

Terminal window

```
mkdir -p ~/.gemini/commands/refactor


touch ~/.gemini/commands/refactor/pure.toml
```

**Windows (PowerShell)**

Terminal window

```
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.gemini\commands\refactor"


New-Item -ItemType File -Force -Path "$env:USERPROFILE\.gemini\commands\refactor\pure.toml"
```

**2. Add the content to the file:**

Open `~/.gemini/commands/refactor/pure.toml` in your editor and add the
following content. We are including the optional `description` for best
practice.

~/.gemini/commands/refactor/pure.toml

```
# This command will be invoked via: /refactor:pure


description = "Asks the model to refactor the current context into a pure function."


prompt = """


Please analyze the code I've provided in the current context.


Refactor it into a pure function.


Your response should include:


1. The refactored, pure function code block.


2. A brief explanation of the key changes you made and why they contribute to purity.


"""
```

**3. Run the command:**

That’s it! You can now run your command in the CLI. First, you might add a file
to the context, and then invoke your command:

```
> @my-messy-function.js


> /refactor:pure
```

Gemini CLI will then execute the multi-line prompt defined in your TOML file.

Last updated: Apr 30, 2026


</details>
