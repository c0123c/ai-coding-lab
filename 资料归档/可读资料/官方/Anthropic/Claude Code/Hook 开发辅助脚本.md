# Hook 开发辅助脚本

说明配置校验、模拟事件测试和代码检查脚本的用途、运行流程及常见错误，支持发布前验证 Hook。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/387dc35db708150c229d7ed2d34342042e417fa0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/plugin-dev/skills/hook-development/scripts/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Hook 开发辅助脚本.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Hook 开发实用程序脚本

这些脚本有助于在部署之前验证、测试和检查钩子实现。

## validate-hook-schema.sh

验证 `hooks.json` 配置文件的结构和常见问题是否正确。

**用法：**
```bash
./validate-hook-schema.sh path/to/hooks.json
```

**检查：**
- 有效的 JSON 语法
- 存在必填字段
- 有效的挂钩事件名称
- 正确的吊钩类型 (command/prompt)
- 有效范围内的超时值
- 硬编码路径检测
- 提示钩子事件兼容性

**示例：**
```bash
cd my-plugin
./validate-hook-schema.sh hooks/hooks.json
```

## test-hook.sh

在部署到 Claude Code 之前使用示例输入测试各个挂钩脚本。

**用法：**
```bash
./test-hook.sh [options] <hook-script> <test-input.json>
```

**选项：**
- `-v, --verbose` - 显示详细的执行信息
- `-t, --timeout N` - 设置超时（以秒为单位）（默认值：60）
- `--create-sample <event-type>` - 生成示例测试输入

**示例：**
```bash
# Create sample test input
./test-hook.sh --create-sample PreToolUse > test-input.json

# Test a hook script
./test-hook.sh my-hook.sh test-input.json

# Test with verbose output and custom timeout
./test-hook.sh -v -t 30 my-hook.sh test-input.json
```

**特点：**
- 设置适当的环境变量（CLAUDE_PROJECT_DIR，CLAUDE_PLUGIN_ROOT）
- 测量执行时间
- 验证输出 JSON
- 显示退出代码及其含义
- 捕获环境文件输出

## hook-linter.sh

检查挂钩脚本是否存在常见问题和最佳实践违规情况。

**用法：**
```bash
./hook-linter.sh <hook-script.sh> [hook-script2.sh ...]
```

**检查：**
- 舍邦存在
- `set -euo pipefail`用法
- 标准输入输入读取
- 正确的错误处理
- 变量引用（防止注入）
- 退出代码使用
- 硬编码路径
- 长时间运行的代码检测
- 错误输出到 stderr
- 输入验证

**示例：**
```bash
# Lint single script
./hook-linter.sh ../examples/validate-write.sh

# Lint multiple scripts
./hook-linter.sh ../examples/*.sh
```

## 典型工作流程

1. **编写你的钩子脚本**
   ```bash
   vim my-plugin/scripts/my-hook.sh
   ```

2. **检查脚本**
   ```bash
   ./hook-linter.sh my-plugin/scripts/my-hook.sh
   ```

3. **创建测试输入**
   ```bash
   ./test-hook.sh --create-sample PreToolUse > test-input.json
   # Edit test-input.json as needed
   ```

4. **测试挂钩**
   ```bash
   ./test-hook.sh -v my-plugin/scripts/my-hook.sh test-input.json
   ```

5. **添加到hooks.json**
   ```bash
   # Edit my-plugin/hooks/hooks.json
   ```

6. **验证配置**
   ```bash
   ./validate-hook-schema.sh my-plugin/hooks/hooks.json
   ```

7. **在Claude Code**中测试
   ```bash
   claude --debug
   ```

## 温馨提示

- 在部署之前始终测试挂钩以避免破坏用户工作流程
- 使用详细模式 (`-v`) 调试钩子行为
- 检查 linter 输出的安全性和最佳实践问题
- 进行任何更改后验证 hooks.json
- 为各种场景（安全操作、危险操作、边缘情况）创建不同的测试输入

## 常见问题

### 钩子不执行

检查：
- 脚本有 shebang (`#!/bin/bash`)
- 脚本可执行（`chmod +x`）
- hooks.json 中的路径正确（使用 `${CLAUDE_PLUGIN_ROOT}`）

### 挂钩超时

- 减少 hooks.json 中的超时
- 优化钩子脚本性能
- 删除长时间运行的操作

### Hook 无声地失败

- 检查退出代码（应为 0 或 2）
- 确保错误转到 stderr (`>&2`)
- 验证JSON输出结构

### 注入漏洞

- 始终引用变量：`"$variable"`
- 使用`set -euo pipefail`
- 验证所有输入字段
- 运行 linter 来捕获问题


## 原文对照

<details>
<summary>展开完整原文</summary>

# Hook Development Utility Scripts

These scripts help validate, test, and lint hook implementations before deployment.

## validate-hook-schema.sh

Validates `hooks.json` configuration files for correct structure and common issues.

**Usage:**
```bash
./validate-hook-schema.sh path/to/hooks.json
```

**Checks:**
- Valid JSON syntax
- Required fields present
- Valid hook event names
- Proper hook types (command/prompt)
- Timeout values in valid ranges
- Hardcoded path detection
- Prompt hook event compatibility

**Example:**
```bash
cd my-plugin
./validate-hook-schema.sh hooks/hooks.json
```

## test-hook.sh

Tests individual hook scripts with sample input before deploying to Claude Code.

**Usage:**
```bash
./test-hook.sh [options] <hook-script> <test-input.json>
```

**Options:**
- `-v, --verbose` - Show detailed execution information
- `-t, --timeout N` - Set timeout in seconds (default: 60)
- `--create-sample <event-type>` - Generate sample test input

**Example:**
```bash
# Create sample test input
./test-hook.sh --create-sample PreToolUse > test-input.json

# Test a hook script
./test-hook.sh my-hook.sh test-input.json

# Test with verbose output and custom timeout
./test-hook.sh -v -t 30 my-hook.sh test-input.json
```

**Features:**
- Sets up proper environment variables (CLAUDE_PROJECT_DIR, CLAUDE_PLUGIN_ROOT)
- Measures execution time
- Validates output JSON
- Shows exit codes and their meanings
- Captures environment file output

## hook-linter.sh

Checks hook scripts for common issues and best practices violations.

**Usage:**
```bash
./hook-linter.sh <hook-script.sh> [hook-script2.sh ...]
```

**Checks:**
- Shebang presence
- `set -euo pipefail` usage
- Stdin input reading
- Proper error handling
- Variable quoting (injection prevention)
- Exit code usage
- Hardcoded paths
- Long-running code detection
- Error output to stderr
- Input validation

**Example:**
```bash
# Lint single script
./hook-linter.sh ../examples/validate-write.sh

# Lint multiple scripts
./hook-linter.sh ../examples/*.sh
```

## Typical Workflow

1. **Write your hook script**
   ```bash
   vim my-plugin/scripts/my-hook.sh
   ```

2. **Lint the script**
   ```bash
   ./hook-linter.sh my-plugin/scripts/my-hook.sh
   ```

3. **Create test input**
   ```bash
   ./test-hook.sh --create-sample PreToolUse > test-input.json
   # Edit test-input.json as needed
   ```

4. **Test the hook**
   ```bash
   ./test-hook.sh -v my-plugin/scripts/my-hook.sh test-input.json
   ```

5. **Add to hooks.json**
   ```bash
   # Edit my-plugin/hooks/hooks.json
   ```

6. **Validate configuration**
   ```bash
   ./validate-hook-schema.sh my-plugin/hooks/hooks.json
   ```

7. **Test in Claude Code**
   ```bash
   claude --debug
   ```

## Tips

- Always test hooks before deploying to avoid breaking user workflows
- Use verbose mode (`-v`) to debug hook behavior
- Check the linter output for security and best practice issues
- Validate hooks.json after any changes
- Create different test inputs for various scenarios (safe operations, dangerous operations, edge cases)

## Common Issues

### Hook doesn't execute

Check:
- Script has shebang (`#!/bin/bash`)
- Script is executable (`chmod +x`)
- Path in hooks.json is correct (use `${CLAUDE_PLUGIN_ROOT}`)

### Hook times out

- Reduce timeout in hooks.json
- Optimize hook script performance
- Remove long-running operations

### Hook fails silently

- Check exit codes (should be 0 or 2)
- Ensure errors go to stderr (`>&2`)
- Validate JSON output structure

### Injection vulnerabilities

- Always quote variables: `"$variable"`
- Use `set -euo pipefail`
- Validate all input fields
- Run the linter to catch issues


</details>
