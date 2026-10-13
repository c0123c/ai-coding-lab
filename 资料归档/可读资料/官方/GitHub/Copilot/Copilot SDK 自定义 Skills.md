# Copilot SDK 自定义 Skills

介绍技能目录和 SKILL.md 格式、加载与停用配置、和 Agent 或 MCP 的组合，以及技能冲突排查。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/github/docs/commit/d00eabd5ae6575005437fbc848705f0d0415d8d4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-sdk/features/skills.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot SDK 自定义 Skills.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Custom skills
shortTitle: Skills
intro: >-
  Skills are reusable prompt modules that extend Copilot's capabilities. Load
  skills from directories to give Copilot specialized abilities for specific
  domains or workflows.
versions:
  fpt: '*'
  ghec: '*'
redirect_from:
  - /copilot/how-tos/copilot-sdk/use-copilot-sdk/custom-skills
contentType: how-tos
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## 概述

技能是一个包含 `SKILL.md` 文件的命名目录 - 一个为 Copilot 提供说明的 Markdown 文档。加载后，技能的内容将被注入到会话上下文中。

技能使您能够：
* 将领域专业知识打包到可重用模块中
* 跨项目共享专业行为
* 组织复杂的 agent 配置
* Enable/disable 每个会话的功能

## 装货技巧

创建会话时指定包含技能的目录：

{% codetabs %}
{% codetab typescript %}

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
const session = await client.createSession({
    model: "gpt-5.4",
    skillDirectories: [
        "./skills/code-review",
        "./skills/documentation",
    ],
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});

// Copilot now has access to skills in those directories
await session.sendAndWait({ prompt: "Review this code for security issues" });
```

{% endcodetab %}
{% codetab python %}

```python
from copilot import CopilotClient, PermissionDecisionApproveOnce

async def main():
    client = CopilotClient()
    await client.start()

    session = await client.create_session(
        on_permission_request=lambda req, inv: PermissionDecisionApproveOnce(),
        model="gpt-5.4",
        skill_directories=[
            "./skills/code-review",
            "./skills/documentation",
        ],
    )

    # Copilot now has access to skills in those directories
    await session.send_and_wait("Review this code for security issues")

    await client.stop()
```

{% endcodetab %}
{% codetab go %}

```golang
package main

import (
    "context"
    "log"
    copilot "github.com/github/copilot-sdk/go"
    "github.com/github/copilot-sdk/go/rpc"
)

func main() {
    ctx := context.Background()
    client := copilot.NewClient(nil)
    if err := client.Start(ctx); err != nil {
        log.Fatal(err)
    }
    defer client.Stop()

    session, err := client.CreateSession(ctx, &copilot.SessionConfig{
        Model: "gpt-5.4",
        SkillDirectories: []string{
            "./skills/code-review",
            "./skills/documentation",
        },
        OnPermissionRequest: func(req copilot.PermissionRequest, inv copilot.PermissionInvocation) (rpc.PermissionDecision, error) {
            return &rpc.PermissionDecisionApproveOnce{}, nil
        },
    })
    if err != nil {
        log.Fatal(err)
    }

    // Copilot now has access to skills in those directories
    _, err = session.SendAndWait(ctx, copilot.MessageOptions{
        Prompt: "Review this code for security issues",
    })
    if err != nil {
        log.Fatal(err)
    }
}
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
using GitHub.Copilot;
using GitHub.Copilot.Rpc;

await using var client = new CopilotClient();
await using var session = await client.CreateSessionAsync(new SessionConfig
{
    Model = "gpt-5.4",
    SkillDirectories = new List<string>
    {
        "./skills/code-review",
        "./skills/documentation",
    },
    OnPermissionRequest = (req, inv) =>
        Task.FromResult(PermissionDecision.ApproveOnce()),
});

// Copilot now has access to skills in those directories
await session.SendAndWaitAsync(new MessageOptions
{
    Prompt = "Review this code for security issues"
});
```

{% endcodetab %}
{% codetab java %}

```java
import com.github.copilot.CopilotClient;
import com.github.copilot.rpc.*;
import java.util.List;

try (var client = new CopilotClient()) {
    client.start().get();

    var session = client.createSession(
        new SessionConfig()
            .setModel("gpt-5.4")
            .setSkillDirectories(List.of(
                "./skills/code-review",
                "./skills/documentation"
            ))
            .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
    ).get();

    // Copilot now has access to skills in those directories
    session.sendAndWait(new MessageOptions()
        .setPrompt("Review this code for security issues")
    ).get();
}
```

{% endcodetab %}
{% endcodetabs %}

## 禁用技能

禁用特定技能，同时保持其他技能活跃：

{% codetabs %}
{% codetab typescript %}

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills"],
    disabledSkills: ["experimental-feature", "deprecated-tool"],
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot.session import PermissionHandler

session = await client.create_session(
    on_permission_request=PermissionHandler.approve_all,
    skill_directories=["./skills"],
    disabled_skills=["experimental-feature", "deprecated-tool"],
)
```

{% endcodetab %}
{% codetab go %}

```golang
session, _ := client.CreateSession(context.Background(), &copilot.SessionConfig{
    SkillDirectories: []string{"./skills"},
    DisabledSkills:   []string{"experimental-feature", "deprecated-tool"},
})
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
var session = await client.CreateSessionAsync(new SessionConfig
{
    SkillDirectories = new List<string> { "./skills" },
    DisabledSkills = new List<string> { "experimental-feature", "deprecated-tool" },
});
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```java
import com.github.copilot.rpc.*;
import java.util.List;

var session = client.createSession(
    new SessionConfig()
        .setSkillDirectories(List.of("./skills"))
        .setDisabledSkills(List.of("experimental-feature", "deprecated-tool"))
        .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
).get();
```

{% endcodetab %}
{% endcodetabs %}

## 技能目录结构

每个技能都是一个包含 `SKILL.md` 文件的命名子目录：

```text
skills/
├── code-review/
│   └── SKILL.md
└── documentation/
    └── SKILL.md
```

`skillDirectories` 选项指向父目录（例如，`./skills`）。 CLI 发现直接子目录中的所有 `SKILL.md` 文件。

### SKILL.md 格式

`SKILL.md` 文件是一个带有可选 YAML frontmatter 的 Markdown 文档：

```markdown
---
name: code-review
description: Specialized code review capabilities
---

# Code Review Guidelines

When reviewing code, always check for:

1. **Security vulnerabilities** - SQL injection, XSS, etc.
2. **Performance issues** - N+1 queries, memory leaks
3. **Code style** - Consistent formatting, naming conventions
4. **Test coverage** - Are critical paths tested?

Provide specific line-number references and suggested fixes.
```

frontmatter 字段：
* **`name`**：技能的标识符（与`disabledSkills`一起使用以选择性地禁用它）。如果省略，则使用目录名称。
* **`description`**：该技能作用的简短描述。

Markdown 正文包含加载技能时注入会话上下文的指令。

## 配置选项

### SessionConfig 技能领域

|语言|领域|类型|描述|
|----------|-------|------|-------------|
| Node.js | `skillDirectories` | `string[]` |从中加载技能的目录|
| Node.js | `disabledSkills` | `string[]` |禁用技能|
| Python | `skill_directories` | `list[str]` |从中加载技能的目录|
| Python | `disabled_skills` | `list[str]` |禁用技能|
| Go | `SkillDirectories` | `[]string` |从中加载技能的目录|
| Go | `DisabledSkills` | `[]string` |禁用技能|
| .NET | `SkillDirectories` | `List<string>` |从中加载技能的目录|
| .NET | `DisabledSkills` | `List<string>` |禁用技能|

### 内置技能和`mode: "empty"`

运行时附带一组捆绑的 ** 内置 ** 技能，这些技能默认符合 
 条件。当您在 `mode: "empty"`（
[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/setup/multi-tenancy>) 的建议基准）中运行客户端时，SDK 会排除每个 
 运行时捆绑的内置技能：它会在 
 创建后和恢复后选项上发送一个空的 `includedBuiltinSkills` 列表补丁，旁边是空的 
`installedPlugins` 列表。

此排除是默认的，而不是永久的限制。允许选择
运行时捆绑技能，设置 `includedBuiltinSkills` （或特定于语言的
大小写）到他们的名字。您还可以选择加入您的 **拥有** 自定义技能下
`mode: "empty"`—启用技能并通过自己的技能 `skillDirectories`——还有那些
保持完全可用，包括与内置技能共享名称的自定义技能。
下 `mode: "copilot-cli"` 除非您设置该选项，否则该字段将被省略。

## 最佳实践

1. **按域组织** - 将相关技能分组在一起（例如，`skills/security/`、`skills/testing/`）

1. **使用 frontmatter** - 为了清晰起见，在 YAML frontmatter 中包含 `name` 和 `description`

1. **文档依赖项** - 记下技能所需的任何工具或 MCP 服务器

1. **单独测试技能** - 在组合技能之前验证技能是否有效

1. **使用相对路径** - 保持技能跨环境可移植

## 与其他功能相结合

### 技能+自定义【agents】

agent 的 `skills` 字段中列出的技能是 ** 热切预加载的 ** - 它们的完整内容在启动时注入到 agent 的上下文中，因此 agent 可以立即访问技能指令，而无需调用技能工具。技能名称是从会话级别 `skillDirectories` 解析的。

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills/security"],
    customAgents: [{
        name: "security-auditor",
        description: "Security-focused code reviewer",
        prompt: "Focus on OWASP Top 10 vulnerabilities",
        skills: ["security-scan", "dependency-check"],
    }],
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

> [!NOTE]
> 技能是选择加入的——当省略`skills`时，不会注入任何技能内容。子agents不继承父级的技能；您必须根据 agent 明确列出它们。

### 技能+MCP服务器

技能可以补充MCP服务器功能：

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills/database"],
    mcpServers: {
        postgres: {
            type: "local",
            command: "npx",
            args: ["-y", "@modelcontextprotocol/server-postgres"],
            tools: ["*"],
        },
    },
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

## 故障排除

### 技能未加载

1. **检查路径是否存在** - 验证技能目录路径是否正确并且包含带有`SKILL.md`文件的子目录
1. **检查权限** - 确保SDK可以读取该目录
1. **检查 SKILL.md 格式 ** - 验证 markdown 格式正确并且任何 YAML frontmatter 使用有效的语法
1. **启用调试日志记录** - 设置`logLevel: "debug"`以查看技能加载日志

### 技能冲突

如果多种技能提供了相互冲突的指令：
* 使用 `disabledSkills` 排除冲突的技能
* 重新组织技能目录以避免重叠

## 另请参阅

* [AUTOTITLE](<https://github.com/copilot/get-started/sdk-quickstart#create-custom-agents>) - 定义专门的 AI 角色
* [AUTOTITLE](<https://github.com/copilot/get-started/sdk-quickstart#step-4-add-a-custom-tool>) - 构建您自己的工具
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/mcp>) - 连接外部工具提供商


## 原文对照

<details>
<summary>展开完整原文</summary>

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## Overview

A skill is a named directory containing a `SKILL.md` file—a markdown document that provides instructions to Copilot. When loaded, the skill's content is injected into the session context.

Skills allow you to:
* Package domain expertise into reusable modules
* Share specialized behaviors across projects
* Organize complex agent configurations
* Enable/disable capabilities per session

## Loading skills

Specify directories containing skills when creating a session:

{% codetabs %}
{% codetab typescript %}

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
const session = await client.createSession({
    model: "gpt-5.4",
    skillDirectories: [
        "./skills/code-review",
        "./skills/documentation",
    ],
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});

// Copilot now has access to skills in those directories
await session.sendAndWait({ prompt: "Review this code for security issues" });
```

{% endcodetab %}
{% codetab python %}

```python
from copilot import CopilotClient, PermissionDecisionApproveOnce

async def main():
    client = CopilotClient()
    await client.start()

    session = await client.create_session(
        on_permission_request=lambda req, inv: PermissionDecisionApproveOnce(),
        model="gpt-5.4",
        skill_directories=[
            "./skills/code-review",
            "./skills/documentation",
        ],
    )

    # Copilot now has access to skills in those directories
    await session.send_and_wait("Review this code for security issues")

    await client.stop()
```

{% endcodetab %}
{% codetab go %}

```golang
package main

import (
    "context"
    "log"
    copilot "github.com/github/copilot-sdk/go"
    "github.com/github/copilot-sdk/go/rpc"
)

func main() {
    ctx := context.Background()
    client := copilot.NewClient(nil)
    if err := client.Start(ctx); err != nil {
        log.Fatal(err)
    }
    defer client.Stop()

    session, err := client.CreateSession(ctx, &copilot.SessionConfig{
        Model: "gpt-5.4",
        SkillDirectories: []string{
            "./skills/code-review",
            "./skills/documentation",
        },
        OnPermissionRequest: func(req copilot.PermissionRequest, inv copilot.PermissionInvocation) (rpc.PermissionDecision, error) {
            return &rpc.PermissionDecisionApproveOnce{}, nil
        },
    })
    if err != nil {
        log.Fatal(err)
    }

    // Copilot now has access to skills in those directories
    _, err = session.SendAndWait(ctx, copilot.MessageOptions{
        Prompt: "Review this code for security issues",
    })
    if err != nil {
        log.Fatal(err)
    }
}
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
using GitHub.Copilot;
using GitHub.Copilot.Rpc;

await using var client = new CopilotClient();
await using var session = await client.CreateSessionAsync(new SessionConfig
{
    Model = "gpt-5.4",
    SkillDirectories = new List<string>
    {
        "./skills/code-review",
        "./skills/documentation",
    },
    OnPermissionRequest = (req, inv) =>
        Task.FromResult(PermissionDecision.ApproveOnce()),
});

// Copilot now has access to skills in those directories
await session.SendAndWaitAsync(new MessageOptions
{
    Prompt = "Review this code for security issues"
});
```

{% endcodetab %}
{% codetab java %}

```java
import com.github.copilot.CopilotClient;
import com.github.copilot.rpc.*;
import java.util.List;

try (var client = new CopilotClient()) {
    client.start().get();

    var session = client.createSession(
        new SessionConfig()
            .setModel("gpt-5.4")
            .setSkillDirectories(List.of(
                "./skills/code-review",
                "./skills/documentation"
            ))
            .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
    ).get();

    // Copilot now has access to skills in those directories
    session.sendAndWait(new MessageOptions()
        .setPrompt("Review this code for security issues")
    ).get();
}
```

{% endcodetab %}
{% endcodetabs %}

## Disabling skills

Disable specific skills while keeping others active:

{% codetabs %}
{% codetab typescript %}

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills"],
    disabledSkills: ["experimental-feature", "deprecated-tool"],
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot.session import PermissionHandler

session = await client.create_session(
    on_permission_request=PermissionHandler.approve_all,
    skill_directories=["./skills"],
    disabled_skills=["experimental-feature", "deprecated-tool"],
)
```

{% endcodetab %}
{% codetab go %}

```golang
session, _ := client.CreateSession(context.Background(), &copilot.SessionConfig{
    SkillDirectories: []string{"./skills"},
    DisabledSkills:   []string{"experimental-feature", "deprecated-tool"},
})
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
var session = await client.CreateSessionAsync(new SessionConfig
{
    SkillDirectories = new List<string> { "./skills" },
    DisabledSkills = new List<string> { "experimental-feature", "deprecated-tool" },
});
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```java
import com.github.copilot.rpc.*;
import java.util.List;

var session = client.createSession(
    new SessionConfig()
        .setSkillDirectories(List.of("./skills"))
        .setDisabledSkills(List.of("experimental-feature", "deprecated-tool"))
        .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
).get();
```

{% endcodetab %}
{% endcodetabs %}

## Skill directory structure

Each skill is a named subdirectory containing a `SKILL.md` file:

```text
skills/
├── code-review/
│   └── SKILL.md
└── documentation/
    └── SKILL.md
```

The `skillDirectories` option points to the parent directory (e.g., `./skills`). The CLI discovers all `SKILL.md` files in immediate subdirectories.

### SKILL.md format

A `SKILL.md` file is a markdown document with optional YAML frontmatter:

```markdown
---
name: code-review
description: Specialized code review capabilities
---

# Code Review Guidelines

When reviewing code, always check for:

1. **Security vulnerabilities** - SQL injection, XSS, etc.
2. **Performance issues** - N+1 queries, memory leaks
3. **Code style** - Consistent formatting, naming conventions
4. **Test coverage** - Are critical paths tested?

Provide specific line-number references and suggested fixes.
```

The frontmatter fields:
* **`name`**: The skill's identifier (used with `disabledSkills` to selectively disable it). If omitted, the directory name is used.
* **`description`**: A short description of what the skill does.

The markdown body contains the instructions that are injected into the session context when the skill is loaded.

## Configuration options

### SessionConfig skill fields

| Language | Field | Type | Description |
|----------|-------|------|-------------|
| Node.js | `skillDirectories` | `string[]` | Directories to load skills from |
| Node.js | `disabledSkills` | `string[]` | Skills to disable |
| Python | `skill_directories` | `list[str]` | Directories to load skills from |
| Python | `disabled_skills` | `list[str]` | Skills to disable |
| Go | `SkillDirectories` | `[]string` | Directories to load skills from |
| Go | `DisabledSkills` | `[]string` | Skills to disable |
| .NET | `SkillDirectories` | `List<string>` | Directories to load skills from |
| .NET | `DisabledSkills` | `List<string>` | Skills to disable |

### Built-in skills and `mode: "empty"`

The runtime ships with a set of bundled **built-in** skills that are eligible by
default. When you run the client in `mode: "empty"` (the recommended baseline for
[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/setup/multi-tenancy>)), the SDK excludes every
runtime-bundled built-in skill: it sends an empty `includedBuiltinSkills` list on
the post-create and post-resume options patch, alongside the empty
`installedPlugins` list.

This exclusion is the default, not a permanent restriction. To allow selected
runtime-bundled skills, set `includedBuiltinSkills` (or the language-specific
casing) to their names. You can also opt into your **own** custom skills under
`mode: "empty"`—enable skills and pass your own `skillDirectories`—and those
remain fully usable, including a custom skill that shares a name with a built-in.
Under `mode: "copilot-cli"` the field is omitted unless you set the option.

## Best practices

1. **Organize by domain** - Group related skills together (e.g., `skills/security/`, `skills/testing/`)

1. **Use frontmatter** - Include `name` and `description` in YAML frontmatter for clarity

1. **Document dependencies** - Note any tools or MCP servers a skill requires

1. **Test skills in isolation** - Verify skills work before combining them

1. **Use relative paths** - Keep skills portable across environments

## Combining with other features

### Skills + custom agents

Skills listed in an agent's `skills` field are **eagerly preloaded**—their full content is injected into the agent's context at startup, so the agent has access to the skill instructions immediately without needing to invoke a skill tool. Skill names are resolved from the session-level `skillDirectories`.

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills/security"],
    customAgents: [{
        name: "security-auditor",
        description: "Security-focused code reviewer",
        prompt: "Focus on OWASP Top 10 vulnerabilities",
        skills: ["security-scan", "dependency-check"],
    }],
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

> [!NOTE]
> Skills are opt-in—when `skills` is omitted, no skill content is injected. Sub-agents do not inherit skills from the parent; you must list them explicitly per agent.

### Skills + MCP servers

Skills can complement MCP server capabilities:

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills/database"],
    mcpServers: {
        postgres: {
            type: "local",
            command: "npx",
            args: ["-y", "@modelcontextprotocol/server-postgres"],
            tools: ["*"],
        },
    },
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

## Troubleshooting

### Skills not loading

1. **Check path exists** - Verify the skill directory path is correct and contains subdirectories with `SKILL.md` files
1. **Check permissions** - Ensure the SDK can read the directory
1. **Check SKILL.md format** - Verify the markdown is well-formed and any YAML frontmatter uses valid syntax
1. **Enable debug logging** - Set `logLevel: "debug"` to see skill loading logs

### Skill conflicts

If multiple skills provide conflicting instructions:
* Use `disabledSkills` to exclude conflicting skills
* Reorganize skill directories to avoid overlaps

## See also

* [AUTOTITLE](<https://github.com/copilot/get-started/sdk-quickstart#create-custom-agents>) - Define specialized AI personas
* [AUTOTITLE](<https://github.com/copilot/get-started/sdk-quickstart#step-4-add-a-custom-tool>) - Build your own tools
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/mcp>) - Connect external tool providers


</details>
