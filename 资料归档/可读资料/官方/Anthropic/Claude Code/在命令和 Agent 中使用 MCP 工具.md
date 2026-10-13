# 在命令和 Agent 中使用 MCP 工具

说明工具命名、发现、权限与配置方法，并以调用示例展示插件命令怎样使用外部工具。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/387dc35db708150c229d7ed2d34342042e417fa0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/plugin-dev/skills/mcp-integration/references/tool-usage.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/在命令和 Agent 中使用 MCP 工具.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 在命令和 Agents 中使用 MCP 工具

在 Claude Code 插件命令和 agents 中有效使用 MCP 工具的完整指南。

## 概述

配置 MCP 服务器后，其工具就可以使用前缀 `mcp__plugin_<plugin-name>_<server-name>__<tool-name>`。在命令和 agents 中使用这些工具就像内置 Claude Code 工具一样。

## 工具命名约定

### 格式

```
mcp__plugin_<plugin-name>_<server-name>__<tool-name>
```

### 示例

**带有 asana 服务器的 Asana 插件：**
- `mcp__plugin_asana_asana__asana_create_task`
- `mcp__plugin_asana_asana__asana_search_tasks`
- `mcp__plugin_asana_asana__asana_get_project`

**带有数据库服务器的自定义插件：**
- `mcp__plugin_myplug_database__query`
- `mcp__plugin_myplug_database__execute`
- `mcp__plugin_myplug_database__list_tables`

### 发现工具名称

**使用`/mcp`命令：**
```bash
/mcp
```

这表明：
- 所有可用的MCP服务器
- 每个服务器提供的工具
- 工具架构和描述
- 配置中使用的完整工具名称

## 在命令中使用工具

### 预允许工具

在命令 frontmatter 中指定 MCP 工具：

```markdown
---
description: Create a new Asana task
allowed-tools: [
  "mcp__plugin_asana_asana__asana_create_task"
]
---

# Create Task Command

To create a task:
1. Gather task details from user
2. Use mcp__plugin_asana_asana__asana_create_task with the details
3. Confirm creation to user
```

### 多种工具

```markdown
---
allowed-tools: [
  "mcp__plugin_asana_asana__asana_create_task",
  "mcp__plugin_asana_asana__asana_search_tasks",
  "mcp__plugin_asana_asana__asana_get_project"
]
---
```

### 通配符（谨慎使用）

```markdown
---
allowed-tools: ["mcp__plugin_asana_asana__*"]
---
```

**警告：** 仅当命令确实需要从服务器访问所有工具时才使用通配符。

### 命令说明中的工具使用

**命令示例：**
```markdown
---
description: Search and create Asana tasks
allowed-tools: [
  "mcp__plugin_asana_asana__asana_search_tasks",
  "mcp__plugin_asana_asana__asana_create_task"
]
---

# Asana Task Management

## Searching Tasks

To search for tasks:
1. Use mcp__plugin_asana_asana__asana_search_tasks
2. Provide search filters (assignee, project, etc.)
3. Display results to user

## Creating Tasks

To create a task:
1. Gather task details:
   - Title (required)
   - Description
   - Project
   - Assignee
   - Due date
2. Use mcp__plugin_asana_asana__asana_create_task
3. Show confirmation with task link
```

## 使用Agents中的工具

### Agent 配置

Agents可以自主使用MCP工具，无需预先允许：

```markdown
---
name: asana-status-updater
description: This agent should be used when the user asks to "update Asana status", "generate project report", or "sync Asana tasks"
model: inherit
color: blue
---

## Role

Autonomous agent for generating Asana project status reports.

## Process

1. **Query tasks**: Use mcp__plugin_asana_asana__asana_search_tasks to get all tasks
2. **Analyze progress**: Calculate completion rates and identify blockers
3. **Generate report**: Create formatted status update
4. **Update Asana**: Use mcp__plugin_asana_asana__asana_create_comment to post report

## Available Tools

The agent has access to all Asana MCP tools without pre-approval.
```

### Agent 工具访问

Agents 具有比命令更广泛的工具访问权限：
- 可以使用任何工具Claude确定有必要
- 不需要预先允许的列表
- 应记录他们通常使用哪些工具

## 工具调用模式

### 模式1：简单工具调用

带验证的单个工具调用：

```markdown
Steps:
1. Validate user provided required fields
2. Call mcp__plugin_api_server__create_item with validated data
3. Check for errors
4. Display confirmation
```

### 模式 2：顺序工具

链接多个工具调用：

```markdown
Steps:
1. Search for existing items: mcp__plugin_api_server__search
2. If not found, create new: mcp__plugin_api_server__create
3. Add metadata: mcp__plugin_api_server__update_metadata
4. Return final item ID
```

### 模式3：批量操作

使用同一工具进行多次调用：

```markdown
Steps:
1. Get list of items to process
2. For each item:
   - Call mcp__plugin_api_server__update_item
   - Track success/failure
3. Report results summary
```

### 模式 4：错误处理

优雅的错误处理：

```markdown
Steps:
1. Try to call mcp__plugin_api_server__get_data
2. If error (rate limit, network, etc.):
   - Wait and retry (max 3 attempts)
   - If still failing, inform user
   - Suggest checking configuration
3. On success, process data
```

## 工具参数

### 了解工具模式

每个 MCP 工具都有一个定义其参数的模式。使用`/mcp`查看。

**示例架构：**
```json
{
  "name": "asana_create_task",
  "description": "Create a new Asana task",
  "inputSchema": {
    "type": "object",
    "properties": {
      "name": {
        "type": "string",
        "description": "Task title"
      },
      "notes": {
        "type": "string",
        "description": "Task description"
      },
      "workspace": {
        "type": "string",
        "description": "Workspace GID"
      }
    },
    "required": ["name", "workspace"]
  }
}
```

### 带参数调用工具

Claude 根据模式自动构建工具调用：

```typescript
// Claude generates this internally
{
  toolName: "mcp__plugin_asana_asana__asana_create_task",
  input: {
    name: "Review PR #123",
    notes: "Code review for new feature",
    workspace: "12345",
    assignee: "67890",
    due_on: "2025-01-15"
  }
}
```

### 参数验证

**命令中，调用前先验证：**

```markdown
Steps:
1. Check required parameters:
   - Title is not empty
   - Workspace ID is provided
   - Due date is valid format (YYYY-MM-DD)
2. If validation fails, ask user to provide missing data
3. If validation passes, call MCP tool
4. Handle tool errors gracefully
```

## 响应处理

### 成功回应

```markdown
Steps:
1. Call MCP tool
2. On success:
   - Extract relevant data from response
   - Format for user display
   - Provide confirmation message
   - Include relevant links or IDs
```

### 错误响应

```markdown
Steps:
1. Call MCP tool
2. On error:
   - Check error type (auth, rate limit, validation, etc.)
   - Provide helpful error message
   - Suggest remediation steps
   - Don't expose internal error details to user
```

### 部分成功

```markdown
Steps:
1. Batch operation with multiple MCP calls
2. Track successes and failures separately
3. Report summary:
   - "Successfully processed 8 of 10 items"
   - "Failed items: [item1, item2] due to [reason]"
   - Suggest retry or manual intervention
```

## 性能优化

### 批量请求

**好：带过滤器的单个查询**
```markdown
Steps:
1. Call mcp__plugin_api_server__search with filters:
   - project_id: "123"
   - status: "active"
   - limit: 100
2. Process all results
```

**避免：许多单独的查询**
```markdown
Steps:
1. For each item ID:
   - Call mcp__plugin_api_server__get_item
   - Process item
```

### 缓存结果

```markdown
Steps:
1. Call expensive MCP operation: mcp__plugin_api_server__analyze
2. Store results in variable for reuse
3. Use cached results for subsequent operations
4. Only re-fetch if data changes
```

### 并行工具调用

当工具不相互依赖时，并行调用：

```markdown
Steps:
1. Make parallel calls (Claude handles this automatically):
   - mcp__plugin_api_server__get_project
   - mcp__plugin_api_server__get_users
   - mcp__plugin_api_server__get_tags
2. Wait for all to complete
3. Combine results
```

## 集成最佳实践

### 用户体验

**提供反馈：**
```markdown
Steps:
1. Inform user: "Searching Asana tasks..."
2. Call mcp__plugin_asana_asana__asana_search_tasks
3. Show progress: "Found 15 tasks, analyzing..."
4. Present results
```

**处理长操作：**
```markdown
Steps:
1. Warn user: "This may take a minute..."
2. Break into smaller steps with updates
3. Show incremental progress
4. Final summary when complete
```

### 错误信息

**好的错误消息：**
```
❌ "Could not create task. Please check:
   1. You're logged into Asana
   2. You have access to workspace 'Engineering'
   3. The project 'Q1 Goals' exists"
```

**糟糕的错误消息：**
```
❌ "Error: MCP tool returned 403"
```

### 文档

**命令中的文档MCP工具用法：**
```markdown
## MCP Tools Used

This command uses the following Asana MCP tools:
- **asana_search_tasks**: Search for tasks matching criteria
- **asana_create_task**: Create new task with details
- **asana_update_task**: Update existing task properties

Ensure you're authenticated to Asana before running this command.
```

## 测试工具使用

### 本地测试

1. **在`.mcp.json`中配置MCP服务器**
2. **在`.claude-plugin/`中本地安装插件**
3. **使用 `/mcp` 验证可用工具 **
4. **使用工具的测试命令**
5. **检查调试输出**：`claude --debug`

### 测试场景

**测试成功调用：**
```markdown
Steps:
1. Create test data in external service
2. Run command that queries this data
3. Verify correct results returned
```

**测试错误案例：**
```markdown
Steps:
1. Test with missing authentication
2. Test with invalid parameters
3. Test with non-existent resources
4. Verify graceful error handling
```

**测试边缘情况：**
```markdown
Steps:
1. Test with empty results
2. Test with maximum results
3. Test with special characters
4. Test with concurrent access
```

## 常见模式

### 模式：CRUD 操作

```markdown
---
allowed-tools: [
  "mcp__plugin_api_server__create_item",
  "mcp__plugin_api_server__read_item",
  "mcp__plugin_api_server__update_item",
  "mcp__plugin_api_server__delete_item"
]
---

# Item Management

## Create
Use create_item with required fields...

## Read
Use read_item with item ID...

## Update
Use update_item with item ID and changes...

## Delete
Use delete_item with item ID (ask for confirmation first)...
```

### 模式：搜索和处理

```markdown
Steps:
1. **Search**: mcp__plugin_api_server__search with filters
2. **Filter**: Apply additional local filtering if needed
3. **Transform**: Process each result
4. **Present**: Format and display to user
```

### 模式：多步骤工作流程

```markdown
Steps:
1. **Setup**: Gather all required information
2. **Validate**: Check data completeness
3. **Execute**: Chain of MCP tool calls:
   - Create parent resource
   - Create child resources
   - Link resources together
   - Add metadata
4. **Verify**: Confirm all steps succeeded
5. **Report**: Provide summary to user
```

## 故障排除

### 工具不可用

**检查：**
- MCP服务器配置正确
- 服务器已连接（检查`/mcp`）
- 工具名称完全匹配（区分大小写）
- 配置更改后重新启动Claude Code

### 工具调用失败

**检查：**
- 认证有效
- 参数匹配工具架构
- 提供所需参数
- 检查`claude --debug`日志

### 性能问题

**检查：**
- 批量查询而不是单独调用
- 适当时缓存结果
- 不进行不必要的工具调用
- 尽可能并行调用

## 结论

有效使用 MCP 工具需要：
1. **通过 `/mcp` 了解工具架构 **
2. **在命令中适当地预先允许工具**
3. **优雅地处理错误**
4. **通过批处理和缓存优化性能**
5. **提供良好的UX**，并提供反馈和明确的错误
6. **部署前彻底测试**

请遵循这些模式，在您的插件命令和 agents 中实现强大的 MCP 工具集成。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Using MCP Tools in Commands and Agents

Complete guide to using MCP tools effectively in Claude Code plugin commands and agents.

## Overview

Once an MCP server is configured, its tools become available with the prefix `mcp__plugin_<plugin-name>_<server-name>__<tool-name>`. Use these tools in commands and agents just like built-in Claude Code tools.

## Tool Naming Convention

### Format

```
mcp__plugin_<plugin-name>_<server-name>__<tool-name>
```

### Examples

**Asana plugin with asana server:**
- `mcp__plugin_asana_asana__asana_create_task`
- `mcp__plugin_asana_asana__asana_search_tasks`
- `mcp__plugin_asana_asana__asana_get_project`

**Custom plugin with database server:**
- `mcp__plugin_myplug_database__query`
- `mcp__plugin_myplug_database__execute`
- `mcp__plugin_myplug_database__list_tables`

### Discovering Tool Names

**Use `/mcp` command:**
```bash
/mcp
```

This shows:
- All available MCP servers
- Tools provided by each server
- Tool schemas and descriptions
- Full tool names for use in configuration

## Using Tools in Commands

### Pre-Allowing Tools

Specify MCP tools in command frontmatter:

```markdown
---
description: Create a new Asana task
allowed-tools: [
  "mcp__plugin_asana_asana__asana_create_task"
]
---

# Create Task Command

To create a task:
1. Gather task details from user
2. Use mcp__plugin_asana_asana__asana_create_task with the details
3. Confirm creation to user
```

### Multiple Tools

```markdown
---
allowed-tools: [
  "mcp__plugin_asana_asana__asana_create_task",
  "mcp__plugin_asana_asana__asana_search_tasks",
  "mcp__plugin_asana_asana__asana_get_project"
]
---
```

### Wildcard (Use Sparingly)

```markdown
---
allowed-tools: ["mcp__plugin_asana_asana__*"]
---
```

**Caution:** Only use wildcards if the command truly needs access to all tools from a server.

### Tool Usage in Command Instructions

**Example command:**
```markdown
---
description: Search and create Asana tasks
allowed-tools: [
  "mcp__plugin_asana_asana__asana_search_tasks",
  "mcp__plugin_asana_asana__asana_create_task"
]
---

# Asana Task Management

## Searching Tasks

To search for tasks:
1. Use mcp__plugin_asana_asana__asana_search_tasks
2. Provide search filters (assignee, project, etc.)
3. Display results to user

## Creating Tasks

To create a task:
1. Gather task details:
   - Title (required)
   - Description
   - Project
   - Assignee
   - Due date
2. Use mcp__plugin_asana_asana__asana_create_task
3. Show confirmation with task link
```

## Using Tools in Agents

### Agent Configuration

Agents can use MCP tools autonomously without pre-allowing them:

```markdown
---
name: asana-status-updater
description: This agent should be used when the user asks to "update Asana status", "generate project report", or "sync Asana tasks"
model: inherit
color: blue
---

## Role

Autonomous agent for generating Asana project status reports.

## Process

1. **Query tasks**: Use mcp__plugin_asana_asana__asana_search_tasks to get all tasks
2. **Analyze progress**: Calculate completion rates and identify blockers
3. **Generate report**: Create formatted status update
4. **Update Asana**: Use mcp__plugin_asana_asana__asana_create_comment to post report

## Available Tools

The agent has access to all Asana MCP tools without pre-approval.
```

### Agent Tool Access

Agents have broader tool access than commands:
- Can use any tool Claude determines is necessary
- Don't need pre-allowed lists
- Should document which tools they typically use

## Tool Call Patterns

### Pattern 1: Simple Tool Call

Single tool call with validation:

```markdown
Steps:
1. Validate user provided required fields
2. Call mcp__plugin_api_server__create_item with validated data
3. Check for errors
4. Display confirmation
```

### Pattern 2: Sequential Tools

Chain multiple tool calls:

```markdown
Steps:
1. Search for existing items: mcp__plugin_api_server__search
2. If not found, create new: mcp__plugin_api_server__create
3. Add metadata: mcp__plugin_api_server__update_metadata
4. Return final item ID
```

### Pattern 3: Batch Operations

Multiple calls with same tool:

```markdown
Steps:
1. Get list of items to process
2. For each item:
   - Call mcp__plugin_api_server__update_item
   - Track success/failure
3. Report results summary
```

### Pattern 4: Error Handling

Graceful error handling:

```markdown
Steps:
1. Try to call mcp__plugin_api_server__get_data
2. If error (rate limit, network, etc.):
   - Wait and retry (max 3 attempts)
   - If still failing, inform user
   - Suggest checking configuration
3. On success, process data
```

## Tool Parameters

### Understanding Tool Schemas

Each MCP tool has a schema defining its parameters. View with `/mcp`.

**Example schema:**
```json
{
  "name": "asana_create_task",
  "description": "Create a new Asana task",
  "inputSchema": {
    "type": "object",
    "properties": {
      "name": {
        "type": "string",
        "description": "Task title"
      },
      "notes": {
        "type": "string",
        "description": "Task description"
      },
      "workspace": {
        "type": "string",
        "description": "Workspace GID"
      }
    },
    "required": ["name", "workspace"]
  }
}
```

### Calling Tools with Parameters

Claude automatically structures tool calls based on schema:

```typescript
// Claude generates this internally
{
  toolName: "mcp__plugin_asana_asana__asana_create_task",
  input: {
    name: "Review PR #123",
    notes: "Code review for new feature",
    workspace: "12345",
    assignee: "67890",
    due_on: "2025-01-15"
  }
}
```

### Parameter Validation

**In commands, validate before calling:**

```markdown
Steps:
1. Check required parameters:
   - Title is not empty
   - Workspace ID is provided
   - Due date is valid format (YYYY-MM-DD)
2. If validation fails, ask user to provide missing data
3. If validation passes, call MCP tool
4. Handle tool errors gracefully
```

## Response Handling

### Success Responses

```markdown
Steps:
1. Call MCP tool
2. On success:
   - Extract relevant data from response
   - Format for user display
   - Provide confirmation message
   - Include relevant links or IDs
```

### Error Responses

```markdown
Steps:
1. Call MCP tool
2. On error:
   - Check error type (auth, rate limit, validation, etc.)
   - Provide helpful error message
   - Suggest remediation steps
   - Don't expose internal error details to user
```

### Partial Success

```markdown
Steps:
1. Batch operation with multiple MCP calls
2. Track successes and failures separately
3. Report summary:
   - "Successfully processed 8 of 10 items"
   - "Failed items: [item1, item2] due to [reason]"
   - Suggest retry or manual intervention
```

## Performance Optimization

### Batching Requests

**Good: Single query with filters**
```markdown
Steps:
1. Call mcp__plugin_api_server__search with filters:
   - project_id: "123"
   - status: "active"
   - limit: 100
2. Process all results
```

**Avoid: Many individual queries**
```markdown
Steps:
1. For each item ID:
   - Call mcp__plugin_api_server__get_item
   - Process item
```

### Caching Results

```markdown
Steps:
1. Call expensive MCP operation: mcp__plugin_api_server__analyze
2. Store results in variable for reuse
3. Use cached results for subsequent operations
4. Only re-fetch if data changes
```

### Parallel Tool Calls

When tools don't depend on each other, call in parallel:

```markdown
Steps:
1. Make parallel calls (Claude handles this automatically):
   - mcp__plugin_api_server__get_project
   - mcp__plugin_api_server__get_users
   - mcp__plugin_api_server__get_tags
2. Wait for all to complete
3. Combine results
```

## Integration Best Practices

### User Experience

**Provide feedback:**
```markdown
Steps:
1. Inform user: "Searching Asana tasks..."
2. Call mcp__plugin_asana_asana__asana_search_tasks
3. Show progress: "Found 15 tasks, analyzing..."
4. Present results
```

**Handle long operations:**
```markdown
Steps:
1. Warn user: "This may take a minute..."
2. Break into smaller steps with updates
3. Show incremental progress
4. Final summary when complete
```

### Error Messages

**Good error messages:**
```
❌ "Could not create task. Please check:
   1. You're logged into Asana
   2. You have access to workspace 'Engineering'
   3. The project 'Q1 Goals' exists"
```

**Poor error messages:**
```
❌ "Error: MCP tool returned 403"
```

### Documentation

**Document MCP tool usage in command:**
```markdown
## MCP Tools Used

This command uses the following Asana MCP tools:
- **asana_search_tasks**: Search for tasks matching criteria
- **asana_create_task**: Create new task with details
- **asana_update_task**: Update existing task properties

Ensure you're authenticated to Asana before running this command.
```

## Testing Tool Usage

### Local Testing

1. **Configure MCP server** in `.mcp.json`
2. **Install plugin locally** in `.claude-plugin/`
3. **Verify tools available** with `/mcp`
4. **Test command** that uses tools
5. **Check debug output**: `claude --debug`

### Test Scenarios

**Test successful calls:**
```markdown
Steps:
1. Create test data in external service
2. Run command that queries this data
3. Verify correct results returned
```

**Test error cases:**
```markdown
Steps:
1. Test with missing authentication
2. Test with invalid parameters
3. Test with non-existent resources
4. Verify graceful error handling
```

**Test edge cases:**
```markdown
Steps:
1. Test with empty results
2. Test with maximum results
3. Test with special characters
4. Test with concurrent access
```

## Common Patterns

### Pattern: CRUD Operations

```markdown
---
allowed-tools: [
  "mcp__plugin_api_server__create_item",
  "mcp__plugin_api_server__read_item",
  "mcp__plugin_api_server__update_item",
  "mcp__plugin_api_server__delete_item"
]
---

# Item Management

## Create
Use create_item with required fields...

## Read
Use read_item with item ID...

## Update
Use update_item with item ID and changes...

## Delete
Use delete_item with item ID (ask for confirmation first)...
```

### Pattern: Search and Process

```markdown
Steps:
1. **Search**: mcp__plugin_api_server__search with filters
2. **Filter**: Apply additional local filtering if needed
3. **Transform**: Process each result
4. **Present**: Format and display to user
```

### Pattern: Multi-Step Workflow

```markdown
Steps:
1. **Setup**: Gather all required information
2. **Validate**: Check data completeness
3. **Execute**: Chain of MCP tool calls:
   - Create parent resource
   - Create child resources
   - Link resources together
   - Add metadata
4. **Verify**: Confirm all steps succeeded
5. **Report**: Provide summary to user
```

## Troubleshooting

### Tools Not Available

**Check:**
- MCP server configured correctly
- Server connected (check `/mcp`)
- Tool names match exactly (case-sensitive)
- Restart Claude Code after config changes

### Tool Calls Failing

**Check:**
- Authentication is valid
- Parameters match tool schema
- Required parameters provided
- Check `claude --debug` logs

### Performance Issues

**Check:**
- Batching queries instead of individual calls
- Caching results when appropriate
- Not making unnecessary tool calls
- Parallel calls when possible

## Conclusion

Effective MCP tool usage requires:
1. **Understanding tool schemas** via `/mcp`
2. **Pre-allowing tools** in commands appropriately
3. **Handling errors gracefully**
4. **Optimizing performance** with batching and caching
5. **Providing good UX** with feedback and clear errors
6. **Testing thoroughly** before deployment

Follow these patterns for robust MCP tool integration in your plugin commands and agents.


</details>
