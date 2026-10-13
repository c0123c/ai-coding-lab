# Agent Skills 格式规范

规定目录、SKILL.md 元数据与正文、可选脚本资源、引用和校验；这是规范定义，具体支持情况需按产品核对。

- 来源：通用规范／Agent Skills
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-08-04](<https://agentskills.io/specification>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://agentskills.io/specification>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/Agent Skills/Agent Skills 格式规范.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://agentskills.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 规格

> Agent Skills的完整格式规范。

## 目录结构

技能是一个至少包含 `SKILL.md` 文件的目录：

```
skill-name/
├── SKILL.md          # Required: metadata + instructions
├── scripts/          # Optional: executable code
├── references/       # Optional: documentation
├── assets/           # Optional: templates, resources
└── ...               # Any additional files or directories
```

## `SKILL.md` 格式

`SKILL.md` 文件必须包含 YAML frontmatter 后跟 Markdown 内容。

### 前题

|领域|必填|约束条件|
| - | - | - |
| `name` |是的|最多 64 个字符。仅限小写字母、数字和连字符。不得以连字符开头或结尾。|
| `description` |是的|最多 1024 个字符。非空。描述该技能的作用以及何时使用它。|
| `license` |否|许可证名称或对捆绑许可证文件的引用。|
| `compatibility` |否|最多 500 个字符。表示环境要求（目标产品、系统包、网络访问等）。|
| `metadata` |否|附加元数据的任意键值映射（从字符串键到字符串值的映射）。|
| `allowed-tools` |否|该技能可以使用的以空格分隔的预先批准的工具串。 （实验性）|



#### 补充说明

**最小示例：**

  ```markdown SKILL.md theme={null}
  ---
  name: skill-name
  description: A description of what this skill does and when to use it.
  ---
  ```

**带有可选字段的示例：**

  ```markdown SKILL.md theme={null}
  ---
  name: pdf-processing
  description: Extract PDF text, fill forms, merge files. Use when handling PDFs.
  license: Apache-2.0
  metadata:
    author: example-org
    version: "1.0"
  ---
  ```



#### `name`字段

必填的 `name` 字段：

* 必须是 1-64 字符
* 只能包含 unicode 小写字母数字字符 (`a-z`、`0-9`) 和连字符 (`-`)
* 不得以连字符开头或结尾 (`-`)
* 不得包含连续的连字符 (`--`)
* 必须与父目录名称匹配



#### 补充说明

**有效示例：**

  ```yaml theme={null}
  name: pdf-processing
  ```

  ```yaml theme={null}
  name: data-analysis
  ```

  ```yaml theme={null}
  name: code-review
  ```

**无效示例：**

  ```yaml theme={null}
  name: PDF-Processing  # uppercase not allowed
  ```

  ```yaml theme={null}
  name: -pdf  # cannot start with hyphen
  ```

  ```yaml theme={null}
  name: pdf--processing  # consecutive hyphens not allowed
  ```



#### `description`字段

必填的 `description` 字段：

* 必须是 1-1024 字符
* 应该描述该技能的用途以及何时使用它
* 应包含有助于 agents 识别相关任务的特定关键字



#### 补充说明

**很好的例子：**

  ```yaml theme={null}
  description: Extracts text and tables from PDF files, fills PDF forms, and merges multiple PDFs. Use when working with PDF documents or when the user mentions PDFs, forms, or document extraction.
  ```

**不好的例子：**

  ```yaml theme={null}
  description: Helps with PDFs.
  ```



#### `license`字段

可选的 `license` 字段：

* 指定应用于技能的许可证
* 我们建议保持简短（许可证名称或捆绑许可证文件的名称）



#### 补充说明

**示例：**

  ```yaml theme={null}
  license: Proprietary. LICENSE.txt has complete terms
  ```



#### `compatibility`字段

可选的 `compatibility` 字段：

* 必须是 1-500 字符（如果提供）
* 仅当您的技能有特定环境要求时才应包含在内
* 可以表明预期的产品、所需的系统包、网络访问需求等。



#### 补充说明

**示例：**

  ```yaml theme={null}
  compatibility: Designed for Claude Code (or similar products)
  ```

  ```yaml theme={null}
  compatibility: Requires git, docker, jq, and access to the internet
  ```

  ```yaml theme={null}
  compatibility: Requires Python 3.14+ and uv
  ```





**注释：**

大多数技能不需要 `compatibility` 字段。


#### `metadata`字段

可选的 `metadata` 字段：

* 从字符串键到字符串值的映射
* 客户端可以使用它来存储 Agent Skills 规范未定义的其他属性
* 我们建议使您的键名合理唯一，以避免意外冲突



#### 补充说明

**示例：**

  ```yaml theme={null}
  metadata:
    author: example-org
    version: "1.0"
  ```



#### `allowed-tools`字段

可选的 `allowed-tools` 字段：

* 预先批准运行的以空格分隔的工具串
* 实验性的。 agent 实现之间对该字段的支持可能有所不同



#### 补充说明

**示例：**

  ```yaml theme={null}
  allowed-tools: Bash(git:*) Bash(jq:*) Read
  ```



### 正文内容

frontmatter 之后的 Markdown 正文包含技能说明。没有格式限制。写下任何有助于 agents 有效执行任务的内容。

推荐栏目：

* 分步说明
* 输入和输出示例
* 常见的边缘情况

请注意，一旦决定激活技能，agent 将加载整个文件。考虑将较长的 `SKILL.md` 内容拆分为引用的文件。

## 可选目录

技能目录可能包含超出所需 `SKILL.md` 的任何文件和目录。以下约定是组织常见内容类型的建议。

### `scripts/`

包含agents可以运行的可执行代码。脚本应该：

* 是独立的或清楚地记录依赖关系
* 包含有用的错误消息
* 优雅地处理边缘情况

支持的语言取决于 agent 实现。常见选项包括 Python、Bash 和 JavaScript。

### `references/`

包含 agents 在需要时可以阅读的附加文档：

* `REFERENCE.md` - 详细技术参考
* `FORMS.md` - 表单模板或结构化数据格式
* 域特定文件（`finance.md`、`legal.md`等）

重点关注各个 [ 参考文件 ](<#文件参考>)。 Agents 按需加载这些文件，因此较小的文件意味着较少使用上下文。

### `assets/`

包含静态资源：

* 模板（文档模板、配置模板）
* 图像（图表、示例）
* 数据文件（查找表、模式）

## 渐进式披露

Agents加载技能*逐步*，仅在任务需要时才拉取更多细节。技能的构建应充分利用这一点：

1. **元数据**（\~100令牌）：`name`和`description`字段在启动时加载所有技能
2. **说明**（推荐使用\<5000代币）：激活技能时会加载完整的`SKILL.md`主体
3. **资源**（根据需要）：仅在需要时加载文件（例如`scripts/`、`references/`或`assets/`中的文件）

将主 `SKILL.md` 保留在 500 行下。将详细的参考材料移至单独的文件中。

## 文件参考

引用技能中的其他文件时，请使用技能根目录中的相对路径：

```markdown SKILL.md theme={null}
See [the reference guide](references/REFERENCE.md) for details.

Run the extraction script:
scripts/extract.py
```

将文件引用保持在 `SKILL.md` 的深一层。避免深度嵌套的引用链。

## 验证

使用 [skills-ref](<https://github.com/agentskills/agentskills/tree/main/skills-ref>) 参考库来验证您的技能：

```bash theme={null}
skills-ref validate ./my-skill
```

这将检查您的 `SKILL.md` frontmatter 是否有效并遵循所有命名约定。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://agentskills.io/llms.txt
> Use this file to discover all available pages before exploring further.

# Specification

> The complete format specification for Agent Skills.

## Directory structure

A skill is a directory containing, at minimum, a `SKILL.md` file:

```
skill-name/
├── SKILL.md          # Required: metadata + instructions
├── scripts/          # Optional: executable code
├── references/       # Optional: documentation
├── assets/           # Optional: templates, resources
└── ...               # Any additional files or directories
```

## `SKILL.md` format

The `SKILL.md` file must contain YAML frontmatter followed by Markdown content.

### Frontmatter

| Field | Required | Constraints |
| - | - | - |
| `name` | Yes | Max 64 characters. Lowercase letters, numbers, and hyphens only. Must not start or end with a hyphen. |
| `description` | Yes | Max 1024 characters. Non-empty. Describes what the skill does and when to use it. |
| `license` | No | License name or reference to a bundled license file. |
| `compatibility` | No | Max 500 characters. Indicates environment requirements (intended product, system packages, network access, etc.). |
| `metadata` | No | Arbitrary key-value mapping for additional metadata (a map from string keys to string values). |
| `allowed-tools` | No | Space-separated string of pre-approved tools the skill may use. (Experimental) |



#### 补充说明

  **Minimal example:**

  ```markdown SKILL.md theme={null}
  ---
  name: skill-name
  description: A description of what this skill does and when to use it.
  ---
  ```

  **Example with optional fields:**

  ```markdown SKILL.md theme={null}
  ---
  name: pdf-processing
  description: Extract PDF text, fill forms, merge files. Use when handling PDFs.
  license: Apache-2.0
  metadata:
    author: example-org
    version: "1.0"
  ---
  ```



#### `name` field

The required `name` field:

* Must be 1-64 characters
* May only contain unicode lowercase alphanumeric characters (`a-z`, `0-9`) and hyphens (`-`)
* Must not start or end with a hyphen (`-`)
* Must not contain consecutive hyphens (`--`)
* Must match the parent directory name



#### 补充说明

  **Valid examples:**

  ```yaml theme={null}
  name: pdf-processing
  ```

  ```yaml theme={null}
  name: data-analysis
  ```

  ```yaml theme={null}
  name: code-review
  ```

  **Invalid examples:**

  ```yaml theme={null}
  name: PDF-Processing  # uppercase not allowed
  ```

  ```yaml theme={null}
  name: -pdf  # cannot start with hyphen
  ```

  ```yaml theme={null}
  name: pdf--processing  # consecutive hyphens not allowed
  ```



#### `description` field

The required `description` field:

* Must be 1-1024 characters
* Should describe both what the skill does and when to use it
* Should include specific keywords that help agents identify relevant tasks



#### 补充说明

  **Good example:**

  ```yaml theme={null}
  description: Extracts text and tables from PDF files, fills PDF forms, and merges multiple PDFs. Use when working with PDF documents or when the user mentions PDFs, forms, or document extraction.
  ```

  **Poor example:**

  ```yaml theme={null}
  description: Helps with PDFs.
  ```



#### `license` field

The optional `license` field:

* Specifies the license applied to the skill
* We recommend keeping it short (either the name of a license or the name of a bundled license file)



#### 补充说明

  **Example:**

  ```yaml theme={null}
  license: Proprietary. LICENSE.txt has complete terms
  ```



#### `compatibility` field

The optional `compatibility` field:

* Must be 1-500 characters if provided
* Should only be included if your skill has specific environment requirements
* Can indicate intended product, required system packages, network access needs, etc.



#### 补充说明

  **Examples:**

  ```yaml theme={null}
  compatibility: Designed for Claude Code (or similar products)
  ```

  ```yaml theme={null}
  compatibility: Requires git, docker, jq, and access to the internet
  ```

  ```yaml theme={null}
  compatibility: Requires Python 3.14+ and uv
  ```





**注释：**

  Most skills do not need the `compatibility` field.


#### `metadata` field

The optional `metadata` field:

* A map from string keys to string values
* Clients can use this to store additional properties not defined by the Agent Skills spec
* We recommend making your key names reasonably unique to avoid accidental conflicts



#### 补充说明

  **Example:**

  ```yaml theme={null}
  metadata:
    author: example-org
    version: "1.0"
  ```



#### `allowed-tools` field

The optional `allowed-tools` field:

* A space-separated string of tools that are pre-approved to run
* Experimental. Support for this field may vary between agent implementations



#### 补充说明

  **Example:**

  ```yaml theme={null}
  allowed-tools: Bash(git:*) Bash(jq:*) Read
  ```



### Body content

The Markdown body after the frontmatter contains the skill instructions. There are no format restrictions. Write whatever helps agents perform the task effectively.

Recommended sections:

* Step-by-step instructions
* Examples of inputs and outputs
* Common edge cases

Note that the agent will load this entire file once it's decided to activate a skill. Consider splitting longer `SKILL.md` content into referenced files.

## Optional directories

A skill directory may contain any files and directories beyond the required `SKILL.md`. The conventions below are recommendations for organizing common types of content.

### `scripts/`

Contains executable code that agents can run. Scripts should:

* Be self-contained or clearly document dependencies
* Include helpful error messages
* Handle edge cases gracefully

Supported languages depend on the agent implementation. Common options include Python, Bash, and JavaScript.

### `references/`

Contains additional documentation that agents can read when needed:

* `REFERENCE.md` - Detailed technical reference
* `FORMS.md` - Form templates or structured data formats
* Domain-specific files (`finance.md`, `legal.md`, etc.)

Keep individual [reference files](<#file-references>) focused. Agents load these on demand, so smaller files mean less use of context.

### `assets/`

Contains static resources:

* Templates (document templates, configuration templates)
* Images (diagrams, examples)
* Data files (lookup tables, schemas)

## Progressive disclosure

Agents load skills *progressively*, pulling in more detail only as a task calls for it. Skills should be structured to take advantage of this:

1. **Metadata** (\~100 tokens): The `name` and `description` fields are loaded at startup for all skills
2. **Instructions** (\< 5000 tokens recommended): The full `SKILL.md` body is loaded when the skill is activated
3. **Resources** (as needed): Files (e.g. those in `scripts/`, `references/`, or `assets/`) are loaded only when required

Keep your main `SKILL.md` under 500 lines. Move detailed reference material to separate files.

## File references

When referencing other files in your skill, use relative paths from the skill root:

```markdown SKILL.md theme={null}
See [the reference guide](references/REFERENCE.md) for details.

Run the extraction script:
scripts/extract.py
```

Keep file references one level deep from `SKILL.md`. Avoid deeply nested reference chains.

## Validation

Use the [skills-ref](<https://github.com/agentskills/agentskills/tree/main/skills-ref>) reference library to validate your skills:

```bash theme={null}
skills-ref validate ./my-skill
```

This checks that your `SKILL.md` frontmatter is valid and follows all naming conventions.


</details>
