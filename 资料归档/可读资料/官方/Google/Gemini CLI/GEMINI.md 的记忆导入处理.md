# GEMINI.md 的记忆导入处理

讲解文件引用、相对与绝对路径、嵌套导入、循环检测、访问限制和异常处理，用于拆分大型项目上下文。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-04-10](<https://github.com/google-gemini/gemini-cli/commit/e09410b6e5b7c56222c6583e8f028a35d623c764>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/reference/memport.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/reference/memport.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 内存导入处理器

内存导入处理器是一项功能，可让您通过使用 `@file.md` 语法从其他文件导入内容来模块化 GEMINI.md
 文件。

## 概述

此功能使您能够将大型 GEMINI.md 文件分解为更小、更
可管理的组件，这些组件可以在不同的上下文中重复使用。导入
处理器支持相对和绝对路径，具有内置安全
功能，可防止循环导入并确保文件访问安全。

## 语法

使用 `@` 符号，后跟要导入的文件的路径：

```markdown
# Main GEMINI.md file

This is the main content.

@./components/instructions.md

More content here.

@./shared/configuration.md
```

## 支持的路径格式

### 相对路径

- `@./file.md` - 从同一目录导入
- `@../file.md` - 从父目录导入
- `@./components/file.md` - 从子目录导入

### 绝对路径

- `@/absolute/path/to/file.md` - 使用绝对路径导入

## 示例

### 基本导入

```markdown
# My GEMINI.md

Welcome to my project!

@./get-started.md

## Features

@./features/overview.md
```

### 嵌套导入

导入的文件本身可以包含导入，从而创建嵌套结构：

```markdown
# main.md

@./header.md @./content.md @./footer.md
```

```markdown
# header.md

# Project Header

@./shared/title.md
```

## 安全特性

### 循环进口检测

处理器自动检测并防止循环导入：

```markdown
# file-a.md

@./file-b.md
```

```markdown
# file-b.md

@./file-a.md <!-- This will be detected and prevented -->
```

### 文件访问安全

`validateImportPath` 功能确保仅允许从 
 指定目录进行导入，从而防止访问允许的 
 范围之外的敏感文件。

### 最大导入深度

为了防止无限递归，有一个可配置的最大导入深度
（默认值：5级别）。

## 错误处理

### 丢失文件

如果引用的文件不存在，导入将正常失败，并在输出中显示 
 错误注释。

### 文件访问错误

权限问题或其他文件系统错误可以通过
适当的错误消息得到妥善处理。

## 代码区域检测

导入处理器使用 `marked` 库来检测代码块和内联 
 代码跨度，确保正确忽略这些区域内的 `@` 导入。
这提供了对嵌套代码块和复杂 Markdown
结构的稳健处理。

## 导入树结构

处理器返回一个导入树，显示导入文件的层次结构，
类似于Claude的`/memory`功能。这可以通过显示读取的文件及其导入
关系来帮助用户调试
其GEMINI.md 文件的问题。

树结构示例：

```
Memory Files
 L project: GEMINI.md
            L a.md
              L b.md
                L c.md
              L d.md
                L e.md
                  L f.md
            L included.md
```

该树保留文件导入的顺序，并显示完整的 
 导入链以供调试之用。

## 与 Claude Code 的 `/memory` (`claude.md`) 方法的比较

Claude Code 的 `/memory` 功能（如 `claude.md` 中所示）通过连接所有包含的文件来生成平面、线性的 
 文档，始终使用清晰的注释和路径名称标记文件边界 
。它没有显式地呈现导入
层次结构，但LLM接收所有文件内容和路径，这足以
在需要时重建层次结构。

> [！NOTE] 导入树主要是为了在开发过程中清晰起见，并且 
>  与 LLM 消耗的相关性有限。

## API参考

### `processImports(content, basePath, debugMode?, importState?)`

处理GEMINI.md内容中的导入语句。

**参数：**

- `content`（字符串）：要处理导入的内容
- `basePath`（字符串）：当前文件所在的目录路径
- `debugMode`（布尔值，可选）：是否启用调试日志记录（默认值：
假）
- `importState`（ImportState，可选）：循环导入的状态跟踪
预防

**返回：** Promise&lt;ProcessImportsResult&gt; - 包含已处理的
内容和导入树的对象

### `ProcessImportsResult`

```typescript
interface ProcessImportsResult {
  content: string; // The processed content with imports resolved
  importTree: MemoryFile; // Tree structure showing the import hierarchy
}
```

### `MemoryFile`

```typescript
interface MemoryFile {
  path: string; // The file path
  imports?: MemoryFile[]; // Direct imports, in the order they were imported
}
```

### `validateImportPath(importPath, basePath, allowedDirectories)`

验证导入路径以确保它们安全且位于允许的目录内。

**参数：**

- `importPath`（字符串）：要验证的导入路径
- `basePath`（字符串）：解析相对路径的基目录
- `allowedDirectories`（字符串[]）：允许的目录路径数组

**返回：** boolean - 导入路径是否有效

### `findProjectRoot(startDir)`

通过搜索找到项目根目录 `.git` 目录向上
给定的起始目录。实施为 **异步** 使用非阻塞的函数
文件系统 API 以避免阻塞 Node.js 事件循环。

**参数：**

- `startDir`（字符串）：开始搜索的目录

**返回：** Promise&lt;字符串&gt; - 项目根目录（如果没有找到`.git`，则为启动
目录）

## 最佳实践

1. **对导入的组件使用描述性文件名**
2. **保持浅导入** - 避免深度嵌套的导入链
3. **记录您的结构** - 维护导入文件的清晰层次结构
4. **测试您的导入** - 确保所有引用的文件存在并且可访问
5. **尽可能使用相对路径**以获得更好的可移植性

## 故障排除

### 常见问题

1. **导入不起作用**：检查文件是否存在且路径是否正确
2. **循环导入警告**：检查循环的导入结构
参考文献
3. **权限错误**：确保文件可读且在允许范围内
目录
4. **路径解析问题**：如果不是相对路径，则使用绝对路径
正确解决

### 调试模式

启用调试模式以查看导入过程的详细日志记录：

```typescript
const result = await processImports(content, basePath, true);
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Memory Import Processor

The Memory Import Processor is a feature that lets you modularize your GEMINI.md
files by importing content from other files using the `@file.md` syntax.

## Overview

This feature enables you to break down large GEMINI.md files into smaller, more
manageable components that can be reused across different contexts. The import
processor supports both relative and absolute paths, with built-in safety
features to prevent circular imports and ensure file access security.

## Syntax

Use the `@` symbol followed by the path to the file you want to import:

```markdown
# Main GEMINI.md file

This is the main content.

@./components/instructions.md

More content here.

@./shared/configuration.md
```

## Supported path formats

### Relative paths

- `@./file.md` - Import from the same directory
- `@../file.md` - Import from parent directory
- `@./components/file.md` - Import from subdirectory

### Absolute paths

- `@/absolute/path/to/file.md` - Import using absolute path

## Examples

### Basic import

```markdown
# My GEMINI.md

Welcome to my project!

@./get-started.md

## Features

@./features/overview.md
```

### Nested imports

The imported files can themselves contain imports, creating a nested structure:

```markdown
# main.md

@./header.md @./content.md @./footer.md
```

```markdown
# header.md

# Project Header

@./shared/title.md
```

## Safety features

### Circular import detection

The processor automatically detects and prevents circular imports:

```markdown
# file-a.md

@./file-b.md
```

```markdown
# file-b.md

@./file-a.md <!-- This will be detected and prevented -->
```

### File access security

The `validateImportPath` function ensures that imports are only allowed from
specified directories, preventing access to sensitive files outside the allowed
scope.

### Maximum import depth

To prevent infinite recursion, there's a configurable maximum import depth
(default: 5 levels).

## Error handling

### Missing files

If a referenced file doesn't exist, the import will fail gracefully with an
error comment in the output.

### File access errors

Permission issues or other file system errors are handled gracefully with
appropriate error messages.

## Code region detection

The import processor uses the `marked` library to detect code blocks and inline
code spans, ensuring that `@` imports inside these regions are properly ignored.
This provides robust handling of nested code blocks and complex Markdown
structures.

## Import tree structure

The processor returns an import tree that shows the hierarchy of imported files,
similar to Claude's `/memory` feature. This helps users debug problems with
their GEMINI.md files by showing which files were read and their import
relationships.

Example tree structure:

```
Memory Files
 L project: GEMINI.md
            L a.md
              L b.md
                L c.md
              L d.md
                L e.md
                  L f.md
            L included.md
```

The tree preserves the order that files were imported and shows the complete
import chain for debugging purposes.

## Comparison to Claude Code's `/memory` (`claude.md`) approach

Claude Code's `/memory` feature (as seen in `claude.md`) produces a flat, linear
document by concatenating all included files, always marking file boundaries
with clear comments and path names. It does not explicitly present the import
hierarchy, but the LLM receives all file contents and paths, which is sufficient
for reconstructing the hierarchy if needed.

> [!NOTE] The import tree is mainly for clarity during development and has
> limited relevance to LLM consumption.

## API reference

### `processImports(content, basePath, debugMode?, importState?)`

Processes import statements in GEMINI.md content.

**Parameters:**

- `content` (string): The content to process for imports
- `basePath` (string): The directory path where the current file is located
- `debugMode` (boolean, optional): Whether to enable debug logging (default:
  false)
- `importState` (ImportState, optional): State tracking for circular import
  prevention

**Returns:** Promise&lt;ProcessImportsResult&gt; - Object containing processed
content and import tree

### `ProcessImportsResult`

```typescript
interface ProcessImportsResult {
  content: string; // The processed content with imports resolved
  importTree: MemoryFile; // Tree structure showing the import hierarchy
}
```

### `MemoryFile`

```typescript
interface MemoryFile {
  path: string; // The file path
  imports?: MemoryFile[]; // Direct imports, in the order they were imported
}
```

### `validateImportPath(importPath, basePath, allowedDirectories)`

Validates import paths to ensure they are safe and within allowed directories.

**Parameters:**

- `importPath` (string): The import path to validate
- `basePath` (string): The base directory for resolving relative paths
- `allowedDirectories` (string[]): Array of allowed directory paths

**Returns:** boolean - Whether the import path is valid

### `findProjectRoot(startDir)`

Finds the project root by searching for a `.git` directory upwards from the
given start directory. Implemented as an **async** function using non-blocking
file system APIs to avoid blocking the Node.js event loop.

**Parameters:**

- `startDir` (string): The directory to start searching from

**Returns:** Promise&lt;string&gt; - The project root directory (or the start
directory if no `.git` is found)

## Best Practices

1. **Use descriptive file names** for imported components
2. **Keep imports shallow** - avoid deeply nested import chains
3. **Document your structure** - maintain a clear hierarchy of imported files
4. **Test your imports** - ensure all referenced files exist and are accessible
5. **Use relative paths** when possible for better portability

## Troubleshooting

### Common issues

1. **Import not working**: Check that the file exists and the path is correct
2. **Circular import warnings**: Review your import structure for circular
   references
3. **Permission errors**: Ensure the files are readable and within allowed
   directories
4. **Path resolution issues**: Use absolute paths if relative paths aren't
   resolving correctly

### Debug mode

Enable debug mode to see detailed logging of the import process:

```typescript
const result = await processImports(content, basePath, true);
```


</details>
