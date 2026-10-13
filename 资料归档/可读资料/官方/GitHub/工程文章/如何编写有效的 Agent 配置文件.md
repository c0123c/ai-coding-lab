# 如何编写有效的 Agent 配置文件

总结大量仓库中的配置经验，介绍角色、命令、边界和示例的写法，并提供文档、测试和代码检查等 Agent 模板。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-11-19](<https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/>)；更新：[2025-11-25](<https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/如何编写有效的 Agent 配置文件.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 如何编写出色的 agents.md：来自多个 2,500 存储库的经验教训

我们最近发布了一个新的 [GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post>) 功能：在 `agents.md` 文件中定义的自定义 agents。您现在可以建立一个专家团队，而不是一名普通助理：一个用于技术写作的 `@docs-agent`，一个用于质量保证的 `@test-agent`，以及一个用于安全分析的 `@security-agent`。每个 `agents.md` 文件都充当一个 agent 角色，您可以使用 frontmatter 和自定义指令定义该角色。

`agents.md` 是您定义所有细节的地方：agent 的角色、它应该知道的确切技术堆栈、项目的文件结构、工作流程以及它可以运行的显式命令。您还可以在这里提供代码样式示例，最重要的是，为不该做的事情设置明确的界限。

**挑战？**大多数agent文件都会失败，因为它们太模糊了。 “你是一个有用的编码助手”是行不通的。 “您是一名测试工程师，为 React 组件编写测试，遵循这些示例，并且从不修改源代码”。

我分析了公共存储库中的 2,500 `agents.md` 文件，以了解开发人员如何使用 `agents.md` 文件。分析显示了一个清晰的有效模式：为您的 agent 提供特定的工作或角色、要运行的确切命令、要遵循的明确定义的边界，以及供 agent 遵循的良好输出的清晰示例。

以下是成功人士的不同做法。

## 实践中有效的方法：来自 2,500+ 存储库的经验教训

我对多个 2,500 `agents.md` 文件的分析揭示了失败的文件和有效的文件之间存在明显的区别。成功的agents不仅仅是模糊的帮助者；他们是专家。以下是性能最佳的文件的不同之处：

* **将命令放在前面：**将相关可执行命令放在前面的部分：`npm test`，`npm run build`，`pytest -v`。包括标志和选项，而不仅仅是工具名称。您的 agent 会经常引用这些内容。
* **代码示例胜过解释：** 一个真实的代码片段显示您的风格胜过三段描述它。展示良好的输出是什么样子的。
* **设置明确的边界：**告诉AI它永远不应该接触什么（例如，机密、供应商目录、生产配置或特定文件夹）。 “永远不要泄露秘密”是最常见的有用限制。
* **具体说明你的堆栈：**说“React 18与TypeScript、Vite和Tailwind CSS”而不是“React项目”。包括版本和关键依赖项。
* **涵盖六个核心领域：** 触及这些领域将使您处于顶层：命令、测试、项目结构、代码风格、git 工作流程和边界。

## 出色的 agent.md 文件示例

以下是将存储库中的文档 `agent.md` 角色添加到 `.github/agents/docs-agent.md` 的示例：

```
---
name: docs_agent
description: Expert technical writer for this project
---

You are an expert technical writer for this project.

## Your role
- You are fluent in Markdown and can read TypeScript code
- You write for a developer audience, focusing on clarity and practical examples
- Your task: read code from `src/` and generate or update documentation in `docs/`

## Project knowledge
- **Tech Stack:** React 18, TypeScript, Vite, Tailwind CSS
- **File Structure:**
  - `src/` – Application source code (you READ from here)
  - `docs/` – All documentation (you WRITE to here)
  - `tests/` – Unit, Integration, and Playwright tests

## Commands you can use
Build docs: `npm run docs:build` (checks for broken links)
Lint markdown: `npx markdownlint docs/` (validates your work)

## Documentation practices
Be concise, specific, and value dense
Write so that a new developer to this codebase can understand your writing, don’t assume your audience are experts in the topic/area you are writing about.

## Boundaries
- ✅ **Always do:** Write new files to `docs/`, follow the style examples, run markdownlint
- ⚠️ **Ask first:** Before modifying existing documents in a major way
- 🚫 **Never do:** Modify code in `src/`, edit config files, commit secrets
```

### 为什么这个 [agent.md](<http://agent.md>) 文件运行良好

* **陈述了一个明确的角色：**定义了agent是谁（专家技术作家），它有什么技能（Markdown，TypeScript），以及它做什么（阅读代码，编写文档）。
* **可执行命令：** 提供AI可以运行的工具（`npm run docs:build`和`npx markdownlint docs/`）。命令是第一位的。
* **项目知识：**指定技术堆栈的版本（React 18，TypeScript，Vite，Tailwind CSS）和确切的文件位置。
* **真实示例：** 展示实际代码的良好输出是什么样子。没有抽象的描述。
* **三层边界：**使用“总是做”、“先问”、“从不做”设定明确的规则。防止破坏性错误。

## 如何构建您的第一个 agent

选择一项简单的任务。不要建立一个“总帮手”。选择一些具体的东西，例如：

* 编写函数文档
* 添加单元测试
* 修复 linting 错误

从最少的开始——你只需要三件事：

* **Agent名称**：`test-agent`、`docs-agent`、`lint-agent`
* **描述**：“为TypeScript函数编写单元测试”
* **Persona**：“你是一位编写全面测试的优质软件工程师”

Copilot也可以帮助您生成一个。使用您首选的 IDE，在 `.github/agents/test-agent.md` 打开一个新文件并使用以下提示：

```
Create a test agent for this repository. It should:
- Have the persona of a QA software engineer.
- Write tests for this codebase
- Run tests and analyzes results
- Write to “/tests/” directory only
- Never modify source code or remove failing tests
- Include specific examples of good test structure
```

Copilot 将根据您的代码库生成一个完整的 `agent.md` 文件，其中包含角色、命令和边界。查看它，添加 YAML frontmatter，调整项目的命令，然后就可以使用 `@test-agent`。

## 六个agents值得打造

考虑请求 Copilot 帮助生成以下 agents 的 `agent.md` 文件。我在每个 agents 中都包含了示例，应该对其进行更改以匹配您项目的实际情况。

### @docs-agent

您早期的一位 agents 应该编写文档。它读取您的代码并生成 API 文档、函数参考和教程。向其提供诸如 `npm run docs:build` 和 `markdownlint docs/` 之类的命令，以便它可以验证自己的工作。告诉它写入 `docs/` 并且永远不要触摸 `src/`。

* 作用：将代码注释和函数签名转换为 Markdown 文档
* 命令示例：`npm run docs:build`、`markdownlint docs/`
* 边界示例：写入 `docs/`，切勿修改源代码

### @测试-agent

这个人写测试。将其指向您的测试框架（Jest、PyTest、Playwright）并为其提供运行测试的命令。这里的边界很关键：它可以写入 `tests`，但永远不应该删除测试，因为它失败并且无法由 agent 修复。

* 它的作用：编写单元测试、集成测试和边缘案例覆盖
* 命令示例：`npm test`、`pytest -v`、`cargo test --coverage`
* 边界示例：写入 `tests/`，除非得到用户授权，否则绝不删除失败的测试

### @lint-agent

尽早创建相当安全的 agent。它修复了代码风格和格式，但不应该改变逻辑。给它命令让它自动修复样式问题。这是低风险的，因为 linter 被设计为安全的。

* 它的作用：格式化代码、修复导入顺序、强制命名约定
* 命令示例：`npm run lint --fix`、`prettier --write`
* 边界示例：只修复样式，不改变代码逻辑

### @api-agent

此 agent 构建 API 端点。它需要了解您的框架（Express、FastAPI、Rails）以及路由所在的位置。给它命令来启动开发服务器并测试端点。关键边界：它可以修改 API 路由，但在接触数据库模式之前必须询问。

* 它的作用：创建 REST 端点、GraphQL 解析器、错误处理程序
* 命令示例：`npm run dev`、`curl localhost:3000/api`、`pytest tests/api/`
* 边界示例：修改路线，在架构更改之前询问

### @dev-deploy-agent

处理本地开发环境的构建和部署。保持锁定：仅部署到开发环境并需要明确批准。为其提供构建命令和部署工具，但要使边界非常清晰。

* 它的作用：运行本地或开发构建，创建 Docker 映像
* 命令示例：`npm run test`
* 边界示例：仅部署到开发人员，任何有风险的事情都需要用户批准

## 入门模板

````
---
name: your-agent-name
description: [One-sentence description of what this agent does]
---

You are an expert [technical writer/test engineer/security analyst] for this project.

## Persona
- You specialize in [writing documentation/creating tests/analyzing logs/building APIs]
- You understand [the codebase/test patterns/security risks] and translate that into [clear docs/comprehensive tests/actionable insights]
- Your output: [API documentation/unit tests/security reports] that [developers can understand/catch bugs early/prevent incidents]

## Project knowledge
- **Tech Stack:** [your technologies with versions]
- **File Structure:**
  - `src/` – [what's here]
  - `tests/` – [what's here]

## Tools you can use
- **Build:** `npm run build` (compiles TypeScript, outputs to dist/)
- **Test:** `npm test` (runs Jest, must pass before commits)
- **Lint:** `npm run lint --fix` (auto-fixes ESLint errors)

## Standards

Follow these rules for all code you write:

**Naming conventions:**
- Functions: camelCase (`getUserData`, `calculateTotal`)
- Classes: PascalCase (`UserService`, `DataController`)
- Constants: UPPER_SNAKE_CASE (`API_KEY`, `MAX_RETRIES`)

**Code style example:**
```typescript
// ✅ Good - descriptive names, proper error handling
async function fetchUserById(id: string): Promise<User> {
  if (!id) throw new Error('User ID required');
  
  const response = await api.get(`/users/${id}`);
  return response.data;
}

// ❌ Bad - vague names, no error handling
async function get(x) {
  return await api.get('/users/' + x).data;
}
Boundaries
- ✅ **Always:** Write to `src/` and `tests/`, run tests before commits, follow naming conventions
- ⚠️ **Ask first:** Database schema changes, adding dependencies, modifying CI/CD config
- 🚫 **Never:** Commit secrets or API keys, edit `node_modules/` or `vendor/`
````

## 要点

构建有效的自定义 agent 并不是编写模糊的提示；而是编写一个有效的自定义提示。这是关于提供特定的角色和明确的指示。

我对多个 2,500 [`agents.md`](<http://agents.md>) 文件的分析表明，最好的 agents 具有清晰的角色，最重要的是，具有详细的操作手册。本手册必须包含可执行命令、样式的具体代码示例、明确的边界（例如永远不要接触的文件）以及有关技术堆栈的细节。

创建自己的 [`agents.md`](<http://agents.md>) 时，涵盖六个核心领域：命令、测试、项目结构、代码风格、git 工作流程和边界。从简单开始。测试一下。当您的 agent 出错时添加详细信息。最好的 agent 文件是通过迭代而不是预先规划来增长的。

现在继续构建您自己的自定义 agents，看看他们如何直接提升您的工作流程！

---

## 标签：

* [代理 AI](<https://github.blog/tag/agentic-ai/>)
* [生成 AI](<https://github.blog/tag/generative-ai/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## 撰写者

![马特·奈伊](<https://avatars.githubusercontent.com/u/5862416?v=4&s=200>)

### [马特·奈伊](<https://github.blog/author/mattnigh/>)

[@mattnigh](<https://github.com/mattnigh>)

项目经理总监，我领导 GitHub 的 AI forEveryone 项目。


## 原文对照

<details>
<summary>展开完整原文</summary>

# How to write a great agents.md: Lessons from over 2,500 repositories

We recently released a new [GitHub Copilot](<https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post>) feature: custom agents defined in `agents.md` files. Instead of one general assistant, you can now build a team of specialists: a `@docs-agent` for technical writing, a `@test-agent` for quality assurance, and a `@security-agent` for security analysis. Each `agents.md` file acts as an agent persona, which you define with frontmatter and custom instructions.

`agents.md` is where you define all the specifics: the agent’s persona, the exact tech stack it should know, the project’s file structure, workflows, and the explicit commands it can run. It’s also where you provide code style examples and, most importantly, set clear boundaries of what not to do.

**The challenge?** Most agent files fail because they’re too vague. “You are a helpful coding assistant” doesn’t work. “You are a test engineer who writes tests for React components, follows these examples, and never modifies source code” does.

I analyzed over 2,500 `agents.md` files across public repos to understand how developers were using `agents.md` files. The analysis showed a clear pattern of what works: provide your agent a specific job or persona, exact commands to run, well-defined boundaries to follow, and clear examples of good output for the agent to follow.

Here’s what the successful ones do differently.

## What works in practice: Lessons from 2,500+ repos

My analysis of over 2,500 `agents.md` files revealed a clear divide between the ones that fail and the ones that work. The successful agents aren’t just vague helpers; they are specialists. Here’s what the best-performing files do differently:

* **Put commands early:** Put relevant executable commands in an early section: `npm test`, `npm run build`, `pytest -v`. Include flags and options, not just tool names. Your agent will reference these often.
* **Code examples over explanations:** One real code snippet showing your style beats three paragraphs describing it. Show what good output looks like.
* **Set clear boundaries:** Tell AI what it should never touch (e.g., secrets, vendor directories, production configs, or specific folders). “Never commit secrets” was the most common helpful constraint.
* **Be specific about your stack:** Say “React 18 with TypeScript, Vite, and Tailwind CSS” not “React project.” Include versions and key dependencies.
* **Cover six core areas:** Hitting these areas puts you in the top tier: commands, testing, project structure, code style, git workflow, and boundaries.

## Example of a great agent.md file

Below is an example for adding a documentation `agent.md` persona in your repo to `.github/agents/docs-agent.md`:

```
---
name: docs_agent
description: Expert technical writer for this project
---

You are an expert technical writer for this project.

## Your role
- You are fluent in Markdown and can read TypeScript code
- You write for a developer audience, focusing on clarity and practical examples
- Your task: read code from `src/` and generate or update documentation in `docs/`

## Project knowledge
- **Tech Stack:** React 18, TypeScript, Vite, Tailwind CSS
- **File Structure:**
  - `src/` – Application source code (you READ from here)
  - `docs/` – All documentation (you WRITE to here)
  - `tests/` – Unit, Integration, and Playwright tests

## Commands you can use
Build docs: `npm run docs:build` (checks for broken links)
Lint markdown: `npx markdownlint docs/` (validates your work)

## Documentation practices
Be concise, specific, and value dense
Write so that a new developer to this codebase can understand your writing, don’t assume your audience are experts in the topic/area you are writing about.

## Boundaries
- ✅ **Always do:** Write new files to `docs/`, follow the style examples, run markdownlint
- ⚠️ **Ask first:** Before modifying existing documents in a major way
- 🚫 **Never do:** Modify code in `src/`, edit config files, commit secrets
```

### Why this [agent.md](<http://agent.md>) file works well

* **States a clear role:** Defines who the agent is (expert technical writer), what skills it has (Markdown, TypeScript), and what it does (read code, write docs).
* **Executable commands:** Gives AI tools it can run (`npm run docs:build` and `npx markdownlint docs/`). Commands come first.
* **Project knowledge:** Specifies tech stack with versions (React 18, TypeScript, Vite, Tailwind CSS) and exact file locations.
* **Real examples:** Shows what good output looks like with actual code. No abstract descriptions.
* **Three-tier boundaries:** Set clear rules using always do, ask first, never do. Prevents destructive mistakes.

## How to build your first agent

Pick one simple task. Don’t build a “general helper.” Pick something specific like:

* Writing function documentation
* Adding unit tests
* Fixing linting errors

Start minimal—you only need three things:

* **Agent name**: `test-agent`, `docs-agent`, `lint-agent`
* **Description**: “Writes unit tests for TypeScript functions”
* **Persona**: “You are a quality software engineer who writes comprehensive tests”

Copilot can also help generate one for you. Using your preferred IDE, open a new file at `.github/agents/test-agent.md` and use this prompt:

```
Create a test agent for this repository. It should:
- Have the persona of a QA software engineer.
- Write tests for this codebase
- Run tests and analyzes results
- Write to “/tests/” directory only
- Never modify source code or remove failing tests
- Include specific examples of good test structure
```

Copilot will generate a complete `agent.md` file with persona, commands, and boundaries based on your codebase. Review it, add in YAML frontmatter, adjust the commands for your project, and you’re ready to use `@test-agent`.

## Six agents worth building

Consider asking Copilot to help generate `agent.md` files for the below agents. I’ve included examples with each of the agents, which should be changed to match the reality of your project.

### @docs-agent

One of your early agents should write documentation. It reads your code and generates API docs, function references, and tutorials. Give it commands like `npm run docs:build` and `markdownlint docs/` so it can validate its own work. Tell it to write to `docs/` and never touch `src/`.

* What it does: Turns code comments and function signatures into Markdown documentation
* Example commands: `npm run docs:build`, `markdownlint docs/`
* Example boundaries: Write to `docs/`, never modify source code

### @test-agent

This one writes tests. Point it at your test framework (Jest, PyTest, Playwright) and give it the command to run tests. The boundary here is critical: it can write to `tests` but should never remove a test because it is failing and cannot be fixed by the agent.

* What it does: Writes unit tests, integration tests, and edge case coverage
* Example commands: `npm test`, `pytest -v`, `cargo test --coverage`
* Example boundaries: Write to `tests/`, never remove failing tests unless authorized by user

### @lint-agent

A fairly safe agent to create early on. It fixes code style and formatting but shouldn’t change logic. Give it commands that let it auto-fix style issues. This one’s low-risk because linters are designed to be safe.

* What it does: Formats code, fixes import order, enforces naming conventions
* Example commands: `npm run lint --fix`, `prettier --write`
* Example boundaries: Only fix style, never change code logic

### @api-agent

This agent builds API endpoints. It needs to know your framework (Express, FastAPI, Rails) and where routes live. Give it commands to start the dev server and test endpoints. The key boundary: it can modify API routes but must ask before touching database schemas.

* What it does: Creates REST endpoints, GraphQL resolvers, error handlers
* Example commands: `npm run dev`, `curl localhost:3000/api`, `pytest tests/api/`
* Example boundaries: Modify routes, ask before schema changes

### @dev-deploy-agent

Handles builds and deployments to your local dev environment. Keep it locked down: only deploy to dev environments and require explicit approval. Give it build commands and deployment tools but make the boundaries very clear.

* What it does: Runs local or dev builds, creates Docker images
* Example commands: `npm run test`
* Example boundaries: Only deploy to dev, require user approval for anything with risk

## Starter template

````
---
name: your-agent-name
description: [One-sentence description of what this agent does]
---

You are an expert [technical writer/test engineer/security analyst] for this project.

## Persona
- You specialize in [writing documentation/creating tests/analyzing logs/building APIs]
- You understand [the codebase/test patterns/security risks] and translate that into [clear docs/comprehensive tests/actionable insights]
- Your output: [API documentation/unit tests/security reports] that [developers can understand/catch bugs early/prevent incidents]

## Project knowledge
- **Tech Stack:** [your technologies with versions]
- **File Structure:**
  - `src/` – [what's here]
  - `tests/` – [what's here]

## Tools you can use
- **Build:** `npm run build` (compiles TypeScript, outputs to dist/)
- **Test:** `npm test` (runs Jest, must pass before commits)
- **Lint:** `npm run lint --fix` (auto-fixes ESLint errors)

## Standards

Follow these rules for all code you write:

**Naming conventions:**
- Functions: camelCase (`getUserData`, `calculateTotal`)
- Classes: PascalCase (`UserService`, `DataController`)
- Constants: UPPER_SNAKE_CASE (`API_KEY`, `MAX_RETRIES`)

**Code style example:**
```typescript
// ✅ Good - descriptive names, proper error handling
async function fetchUserById(id: string): Promise<User> {
  if (!id) throw new Error('User ID required');
  
  const response = await api.get(`/users/${id}`);
  return response.data;
}

// ❌ Bad - vague names, no error handling
async function get(x) {
  return await api.get('/users/' + x).data;
}
Boundaries
- ✅ **Always:** Write to `src/` and `tests/`, run tests before commits, follow naming conventions
- ⚠️ **Ask first:** Database schema changes, adding dependencies, modifying CI/CD config
- 🚫 **Never:** Commit secrets or API keys, edit `node_modules/` or `vendor/`
````

## Key takeaways

Building an effective custom agent isn’t about writing a vague prompt; it’s about providing a specific persona and clear instructions.

My analysis of over 2,500 [`agents.md`](<http://agents.md>) files shows that the best agents are given a clear persona and, most importantly, a detailed operating manual. This manual must include executable commands, concrete code examples for styling, explicit boundaries (like files to never touch), and specifics about your tech stack.

When creating your own [`agents.md`](<http://agents.md>) cover the six core areas: Commands, testing, project structure, code style, git workflow, and boundaries. Start simple. Test it. Add detail when your agent makes mistakes. The best agent files grow through iteration, not upfront planning.

Now go forth and build your own custom agents to see how they level up your workflow first-hand!

---

## Tags:

* [agentic AI](<https://github.blog/tag/agentic-ai/>)
* [generative AI](<https://github.blog/tag/generative-ai/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## Written by

![Matt Nigh](<https://avatars.githubusercontent.com/u/5862416?v=4&s=200>)

### [Matt Nigh](<https://github.blog/author/mattnigh/>)

[@mattnigh](<https://github.com/mattnigh>)

Program Manager Director, I lead the AI for Everyone program at GitHub.


</details>
