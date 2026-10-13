# OpenSpec Skill 目录与安装说明

介绍通过 skills.sh 安装 OpenSpec 工作流 Skill 的方式和相关目录，属于技能分发说明。

- 来源：OpenSpec
- 资料类型：Skill 目录与安装说明
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/skills/README.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/skills/README.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# OpenSpec skills.sh 的技能

将 OpenSpec 工作流程技能安装到任何 [skills.sh](<https://skills.sh>) 兼容的 agent 中：

```bash
npx skills add Fission-AI/OpenSpec
```

这里的每个 `openspec-*/SKILL.md` 都是将相同的技能 `openspec init` 写入到 
 项目中。这些技能驱动 `openspec` CLI，因此对于完整设置（CLI +
`openspec/` 项目脚手架 + 斜杠命令）运行：

```bash
npx openspec@latest init
```

> 这些文件是从技能模板生成的 - 不要手动编辑。更改模板后运行
> `pnpm build && pnpm generate:skills`；如果它们漂移，
> `skillssh-parity.test.ts`就会失败。


## 原文对照

<details>
<summary>展开完整原文</summary>

# OpenSpec skills for skills.sh

Install the OpenSpec workflow skills into any [skills.sh](<https://skills.sh>)-compatible agent:

```bash
npx skills add Fission-AI/OpenSpec
```

Each `openspec-*/SKILL.md` here is the same skill `openspec init` writes into a
project. The skills drive the `openspec` CLI, so for the full setup (CLI +
`openspec/` project scaffolding + slash commands) run:

```bash
npx openspec@latest init
```

> These files are generated from the skill templates — do not edit by hand. Run
> `pnpm build && pnpm generate:skills` after changing a template;
> `skillssh-parity.test.ts` fails if they drift.


</details>
