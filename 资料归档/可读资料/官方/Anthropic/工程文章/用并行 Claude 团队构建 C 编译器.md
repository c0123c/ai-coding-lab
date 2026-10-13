# 用并行 Claude 团队构建 C 编译器

复盘多个 Agent 共同编写编译器的实验，讨论长任务循环、高质量测试、共享代码协调、角色分工与持续验证，并交代成本和能力边界。

- 来源：Anthropic／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-02-05](<https://www.anthropic.com/engineering/building-c-compiler>)；更新：[2026-02-05](<https://www.anthropic.com/engineering/building-c-compiler>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://www.anthropic.com/engineering/building-c-compiler>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/工程文章/用并行 Claude 团队构建 C 编译器.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

*由我们保障团队的研究员 Nicholas Carlini 撰写。*

我一直在尝试一种监督语言模型的新方法，我们称之为“agent团队”。

通过 agent 团队，多个 Claude 实例在共享代码库上并行工作，无需主动人工干预。这种方法极大地扩展了 LLM agents 可实现的范围。

为了对其进行压力测试，我给 16 agents 分配了一个任务，从头开始编写一个基于 Rust 的 C 编译器，能够编译 Linux 内核。经过近 2,000 Claude Code 会话和 $20,000 的 API 成本，agent 团队制作了一个 100,000 行编译器，可以在 x86 上构建 Linux 6.9， ARM和RISC-V。

[编译器本身就是一个有趣的工件](<https://github.com/anthropics/claudes-c-compiler>)，但我在这里重点介绍我在为长期运行的自治agent团队设计工具方面所学到的知识：如何编写测试以使agents在没有人工监督的情况下保持在正轨上，如何构建工作以便多个agents可以并行取得进展，以及在哪里方法达到了上限。

## 启用长时间运行的 Claudes

现有的 agent 脚手架（例如 Claude Code）需要操作员在线并可以共同工作。如果您要求解决一个漫长而复杂的问题，模型可能会解决其中的一部分，但最终它会停止并等待继续输入 - 问题、状态更新或澄清请求。

为了获得持续、自主的进步，我制作了一个安全带，将 Claude 固定在一个简单的循环中（如果您见过 Ralph-loop，这应该看起来很熟悉）。当它完成一项任务时，它会立即开始执行下一项任务。 *（在容器中运行，而不是在您的实际机器中）。*

```
#!/bin/bash

while true; do
    COMMIT=$(git rev-parse --short=6 HEAD)
    LOGFILE="agent_logs/agent_${COMMIT}.log"

    claude --dangerously-skip-permissions \
           -p "$(cat AGENT_PROMPT.md)" \
           --model claude-opus-X-Y &> "$LOGFILE"
done
```

在 agent 提示中，我告诉 Claude 要解决什么问题，并要求它通过将其分解为小块来解决问题，跟踪它正在做什么，弄清楚下一步要做什么，并有效地继续下去，直到完美为止。 （在最后一点上，Claude别无选择。循环永远运行——尽管在一个例子中，我确实偶然看到了Claude`pkill -9 bash`，从而杀死了自己并结束了循环。哎呀！）。

## 并行运行 Claude

并行运行多个实例可以解决单个 agent 线束的两个弱点：

* 一个 Claude Code 会话一次只能执行一件事。特别是随着项目范围的扩大，并行调试多个问题的效率要高得多。
* 运行多个 Claude agents 可以实现专业化。虽然一些 agents 的任务是解决手头的实际问题，但可以调用其他专门的 agents 来（例如）维护文档、关注代码质量或解决专门的子任务。

我的并行 Claude 实现很简单。创建一个新的裸 git 存储库，并且对于每个 agent，都会启动一个 Docker 容器，并将存储库安装到 `/upstream`。每个 agent 将本地副本克隆到 `/workspace`，完成后，从其自己的本地容器推送到上游。

为了防止两个 agents 尝试同时解决同一问题，该线束使用了一种简单的同步算法：

1. Claude 通过将文本文件写入当前 \_tasks/ 来“锁定”任务（例如，一个 agent 可能锁定当前 \_tasks/parse\_if\_statement.txt，而另一个锁定当前\_任务/代码生成\_函数\_definition.txt）。如果两个 agents 尝试申请相同的任务，git 的同步会强制第二个 agent 选择不同的任务。
2. Claude 处理该任务，然后从上游拉取，合并来自其他 agents 的更改，推送其更改，并删除锁。合并冲突很频繁，但 Claude 足够聪明，能够解决这个问题。
3. 无限的 agent 生成循环在新容器中生成一个新的 Claude Code 会话，并且循环重复。

这是一个非常早期的研究原型。我还没有实现 agents 之间的任何其他通信方法，也没有强制执行任何管理高级目标的流程。我不使用编排agent。

相反，我将其留给每个 Claude agent 来决定如何采取行动。在大多数情况下，Claude 会发现“下一个最明显”的问题。当遇到错误时，Claude 通常会维护失败方法和剩余任务的运行文档。在项目的[git存储库](<https://github.com/anthropics/claudes-c-compiler>)中，您可以阅读历史记录并观察它对各种任务的锁定。

## 与 Claude agent 团队一起编程的经验教训

脚手架在循环中运行 Claude，但该循环仅在 Claude 能够告诉如何取得进展时才有用。我的大部分精力都花在围绕 Claude 设计环境上——测试、环境、反馈——这样它就可以在没有我的情况下自行定位。我发现这些方法在编排多个 Claude 实例时最有帮助。

### 编写极其高质量的测试

Claude 将自主工作来解决我提出的任何问题。因此任务验证器近乎完美非常重要，否则Claude将解决错误的问题。改进测试工具需要找到高质量的编译器测试套件，为开源软件包编写验证器和构建脚本，并观察 Claude 所犯的错误，然后在识别这些故障模式时设计新的测试。

例如，在项目接近尾声时，Claude 开始在每次实现新功能时频繁破坏现有功能。为了解决这个问题，我构建了一个持续集成管道并实施了更严格的执行，允许 Claude 更好地测试其工作，以便新的提交不会破坏现有代码。

### 设身处地为Claude着想

我必须不断提醒自己，我是为 Claude 而不是为我自己编写此测试工具，这意味着重新思考我关于测试应如何传达结果的许多假设。

例如，每个 agent 都会被放入一个没有上下文的新容器中，并且将花费大量时间进行自我定位，尤其是在大型项目中。在我们进行测试之前，为了帮助 Claude 自行解决，我添加了维护大量自述文件和进度文件的说明，这些文件应该根据当前状态经常更新。

我还牢记这样一个事实：语言模型具有固有的局限性，在这种情况下，需要围绕这些局限性进行设计。这些包括：

* **上下文窗口污染：**测试工具不应打印数千个无用的字节。最多，它应该打印几行输出并将所有重要信息记录到文件中，以便 Claude 在需要时可以找到它。日志文件应该很容易自动处理：如果有错误，Claude应该写ERROR并将原因放在同一行，这样grep就能找到它。它有助于预先计算聚合汇总统计数据，因此 Claude 不必重新计算它们。
* **时间盲目性：** Claude 无法分辨时间，并且独自一人，会很乐意花几个小时运行测试而不是取得进展。该工具很少打印增量进度（以避免污染上下文），并包含运行 1% 或 10% 随机样本的默认 `--fast` 选项。该子样本对于每个 agent 是确定性的，但在虚拟机之间是随机的，因此 Claude 仍然覆盖所有文件，但每个 agent 都可以完美识别回归。

### 让并行变得简单

当有许多不同的失败测试时，并行化是微不足道的：每个 agent 选择一个不同的失败测试来处理。在测试套件达到 99% 通过率后，每个 agent 都致力于编译一个不同的小型开源项目（例如，SQlite、Redis、libjpeg、MQuickJS、Lua）。

但是当agents开始编译Linux内核时，他们陷入了困境。与包含数百个独立测试的测试套件不同，编译 Linux 内核是一项艰巨的任务。每个 agent 都会遇到相同的错误，修复该错误，然后覆盖彼此的更改。让16 agents运行并没有帮助，因为每个人都在解决相同的任务。

修复方法是使用 [GCC](<https://gcc.gnu.org/>) 作为在线已知良好的编译器预言机进行比较。我编写了一个新的测试工具，使用 GCC 随机编译大部分内核，并使用 Claude 的 C 编译器仅编译剩余文件。如果内核工作，那么问题不在 Claude 的文件子集中。如果它损坏了，那么它可以通过使用 GCC 重新编译其中一些文件来进一步完善。这让每个agent并行工作，修复不同文件中的不同错误，直到Claude的编译器最终可以编译所有文件。 （此操作成功后，仍然需要应用增量调试技术来查找一起失败但独立工作的文件对。）

### 多个 agent 角色

并行性还可以实现专业化。 LLM-编写的代码经常重新实现现有功能，因此我给一个agent分配了合并它发现的任何重复代码的任务。我让另一个负责提高编译器本身的性能，第三个负责输出高效的编译代码。我要求另一位agent从Rust开发人员的角度批评项目的设计，并对项目进行结构性更改以提高整体代码质量，另一位则负责文档工作。

## 对 agent 团队的极限进行压力测试

该项目被设计为能力基准。我有兴趣对法学硕士今天*勉强*实现的极限进行压力测试，以帮助我们为未来模型可靠实现的目标做好准备。

我一直使用 C 编译器项目作为整个 Claude 4 模型系列的基准。正如我在之前的项目中所做的那样，我首先起草了我想要的东西：一个从头开始优化的编译器，没有依赖项，GCC-兼容，能够编译 Linux 内核，并设计为支持多个后端。虽然我指定了设计的某些方面（例如，它应该有一个 SSA IR 以启用多个优化过程），但我没有详细说明如何做到这一点。

以前的 Opus 4 模型几乎无法生成函数式编译器。 Opus 4.5 是第一个跨过门槛的人，它可以生成可以通过大型测试套件的功能编译器，但它仍然无法编译任何真正的大型项目。我对 Opus 4.6 的目标是再次测试极限。

### 评价

在为期两周的近 2,000 Claude Code 会话中，Opus 4.6 消耗了 2 十亿个输入代币并生成了 140 万个输出代币，总成本略低于 20,000 美元。与最昂贵的 Claude Max 计划相比，这是一个极其昂贵的项目。但这个总数只是我自己制作这个所需成本的一小部分——更不用说整个团队了。

这是一个洁净室实施（Claude 在其开发过程中的任何时候都没有互联网访问）；它仅依赖于Rust标准库。 100,000 行编译器可以在 x86、ARM 和 RISC-V 上构建可启动的 Linux 6.9。它还可以编译 QEMU、FFmpeg、SQlite、postgres、redis，并且在大多数编译器测试套件（包括 [GCC 折磨测试套件 ](<https://gcc.gnu.org/onlinedocs/gccint/Torture-Tests.html>)）上具有 99% 通过率。它还通过了开发人员的终极试金石：它可以编译并运行 Doom。

然而，编译器并非没有限制。这些包括：

* 它缺少从实模式启动 Linux 所必需的 16 位 x86 编译器。为此，它调用 GCC（x86\_32 和 x86\_64 编译器是它自己的）。
* 它没有自己的汇编器和链接器；这些是 Claude 开始自动化的最后部分，但仍然存在一些问题。该演示视频是使用 GCC 汇编器和链接器制作的。
* 编译器成功构建了许多项目，但不是全部。它还不是真正编译器的直接替代品。
* 生成的代码效率不是很高。即使启用了所有优化，它输出的代码效率也低于禁用所有优化 * 的 GCC。*
* Rust 代码质量是合理的，但与专家 Rust 程序员可能产生的质量相去甚远。

由此产生的编译器几乎达到了 Opus 能力的极限。我尝试（努力！）解决上述几个限制，但没有完全成功。新功能和错误修复经常会破坏现有功能。

作为一个特别具有挑战性的示例，Opus 无法实现启动进入 16 位实模式所需的 16 位 x86 代码生成器。虽然编译器可以通过 66/67 操作码前缀输出正确的 16 位 x86，但生成的编译输出超过 60kb，远远超过 Linux 强制执行的 32k 代码限制。相反，Claude只是在这里作弊，并在此阶段调用GCC（这仅适用于x86。对于ARM或RISC-V，Claude的编译器可以完全自行编译。）

编译器的[源代码可用](<https://github.com/anthropics/claudes-c-compiler>)。下载它，通读代码，然后在您最喜欢的 C 项目中尝试它。我一直发现，理解语言模型功能的最佳方法就是将它们推向极限，然后研究它们在哪里开始崩溃。在接下来的几天里，如果您想跟随 Claude 不断尝试解决这些限制，我将继续让 Claude 推动新的更改。

## 展望未来

每一代语言模型都开辟了新的使用方式。早期模型对于 IDE 中的制表符补全很有用。不久之后，模型就可以从其文档字符串中完成函数体。 Claude Code的推出使agents成为主流，并使开发者能够与Claude配对编程。但这些产品的运行前提是用户定义一个任务，LLM 运行几秒钟或几分钟并返回答案，然后用户提供后续操作。

Agent 团队展示了自主实施整个复杂项目的可能性。作为这些工具的用户，这使我们能够更加雄心勃勃地实现我们的目标。

我们还处于早期阶段，完全自主的开发会带来真正的风险。当开发人员在开发过程中与 Claude 坐在一起时，他们可以确保一致的质量并实时捕获错误。对于自治系统，很容易看到测试通过并假设工作已经完成，但这种情况很少发生。我曾经从事渗透测试工作，利用大公司生产的产品中的漏洞，程序员部署他们从未亲自验证过的软件的想法是一个真正令人担忧的问题。

所以，这个实验虽然让我兴奋，但也让我感到不安。构建这个编译器是我最近最有趣的事情之一，但我没想到这会在2026这么早的时候实现。语言模型和我们用来与之交互的支架的快速进步为编写大量新代码打开了大门。我预计积极的应用将超过消极的，但我们正在进入一个新世界，这将需要新的策略来安全航行。

### 致谢

特别感谢 Josef Bacik、Edwin Chen、Bernardo Meurer Costa、Jake Eaton、Dan Kelley、Felix Klock、Jannet Park、Steve Weis 以及 Anthropic 的许多其他人的帮助和贡献。


## 原文对照

<details>
<summary>展开完整原文</summary>

*Written by Nicholas Carlini, a researcher on our Safeguards team.*

I've been experimenting with a new approach to supervising language models that we’re calling "agent teams."

With agent teams, multiple Claude instances work in parallel on a shared codebase without active human intervention. This approach dramatically expands the scope of what's achievable with LLM agents.

To stress test it, I tasked 16 agents with writing a Rust-based C compiler, from scratch, capable of compiling the Linux kernel. Over nearly 2,000 Claude Code sessions and $20,000 in API costs, the agent team produced a 100,000-line compiler that can build Linux 6.9 on x86, ARM, and RISC-V.

[The compiler is an interesting artifact](<https://github.com/anthropics/claudes-c-compiler>) on its own, but I focus here on what I learned about designing harnesses for long-running autonomous agent teams: how to write tests that keep agents on track without human oversight, how to structure work so multiple agents can make progress in parallel, and where this approach hits its ceiling.

## Enabling long-running Claudes

Existing agent scaffolds like Claude Code require an operator to be online and available to work jointly. If you ask for a solution to a long and complex problem, the model may solve part of it, but eventually it will stop and wait for continued input—a question, a status update, or a request for clarification.

To elicit sustained, autonomous progress, I built a harness that sticks Claude in a simple loop (if you’ve seen Ralph-loop, this should look familiar). When it finishes one task, it immediately picks up the next. *(Run this in a container, not your actual machine).*

```
#!/bin/bash

while true; do
    COMMIT=$(git rev-parse --short=6 HEAD)
    LOGFILE="agent_logs/agent_${COMMIT}.log"

    claude --dangerously-skip-permissions \
           -p "$(cat AGENT_PROMPT.md)" \
           --model claude-opus-X-Y &> "$LOGFILE"
done
```

In the agent prompt, I tell Claude what problem to solve and ask it to approach the problem by breaking it into small pieces, tracking what it’s working on, figuring out what to work on next, and to effectively keep going until it’s perfect. (On this last point, Claude has no choice. The loop runs forever—although in one instance, I did see Claude `pkill -9 bash` on accident, thus killing itself and ending the loop. Whoops!).

## Running Claude in parallel

Running multiple instances in parallel can address two weaknesses of a single-agent harness:

* One Claude Code session can only do one thing at a time. Especially as the scope of a project expands, debugging multiple issues in parallel is far more efficient.
* Running multiple Claude agents allows for specialization. While a few agents are tasked to solve the actual problem at hand, other specialized agents can be invoked to (for example) maintain documentation, keep an eye on code quality, or solve specialized sub-tasks.

My implementation of parallel Claude is bare-bones. A new bare git repo is created, and for each agent, a Docker container is spun up with the repo mounted to `/upstream`. Each agent clones a local copy to `/workspace`, and when it's done, pushes from its own local container to upstream.

To prevent two agents from trying to solve the same problem at the same time, the harness uses a simple synchronization algorithm:

1. Claude takes a "lock" on a task by writing a text file to current\_tasks/ (e.g., one agent might lock current\_tasks/parse\_if\_statement.txt, while another locks current\_tasks/codegen\_function\_definition.txt). If two agents try to claim the same task, git's synchronization forces the second agent to pick a different one.
2. Claude works on the task, then pulls from upstream, merges changes from other agents, pushes its changes, and removes the lock. Merge conflicts are frequent, but Claude is smart enough to figure that out.
3. The infinite agent-generation-loop spawns a new Claude Code session in a fresh container, and the cycle repeats.

This is a very early research prototype. I haven’t yet implemented any other method for communication between agents, nor do I enforce any process for managing high-level goals. I don’t use an orchestration agent.

Instead, I leave it up to each Claude agent to decide how to act. In most cases, Claude picks up the “next most obvious” problem. When stuck on a bug, Claude will often maintain a running doc of failed approaches and remaining tasks. In the [git repository](<https://github.com/anthropics/claudes-c-compiler>) of the project, you can read through the history and watch it take out locks on various tasks.

## Lessons from programming with Claude agent teams

The scaffolding runs Claude in a loop, but that loop is only useful if Claude can tell how to make progress. Most of my effort went into designing the environment around Claude—the tests, the environment, the feedback—so that it could orient itself without me. These are the approaches I’ve found most helpful when orchestrating multiple Claude instances.

### Write extremely high-quality tests

Claude will work autonomously to solve whatever problem I give it. So it’s important that the task verifier is nearly perfect, otherwise Claude will solve the wrong problem. Improving the testing harness required finding high-quality compiler test suites, writing verifiers and build scripts for open-source software packages, and watching for mistakes Claude was making, then designing new tests as I identified those failure modes.

For example, near the end of the project, Claude started to frequently break existing functionality each time it implemented a new feature. To address this, I built a continuous integration pipeline and implemented stricter enforcement that allowed Claude to better test its work so that new commits can’t break existing code.

### Put yourself in Claude’s shoes

I had to constantly remind myself that I was writing this test harness for Claude and not for myself, which meant rethinking many of my assumptions about how tests should communicate results.

For example, each agent is dropped into a fresh container with no context and will spend significant time orienting itself, especially on large projects. Before we even reach the tests, to help Claude help itself, I included instructions to maintain extensive READMEs and progress files that should be updated frequently with the current status.

I also kept in mind the fact that language models have inherent limitations, which, in this case, needed to be designed around. These include:

* **Context window pollution:** The test harness should not print thousands of useless bytes. At most, it should print a few lines of output and log all important information to a file so Claude can find it when needed. Logfiles should be easy to process automatically: if there are errors, Claude should write ERROR and put the reason on the same line so grep will find it. It helps to pre-compute aggregate summary statistics so Claude doesn't have to recompute them.
* **Time blindness:** Claude can't tell time and, left alone, will happily spend hours running tests instead of making progress. The harness prints incremental progress infrequently (to avoid polluting context) and includes a default `--fast` option that runs a 1% or 10% random sample. This subsample is deterministic per-agent but random across VMs, so Claude still covers all files but each agent can perfectly identify regressions.

### Make parallelism easy

When there are many distinct failing tests, parallelization is trivial: each agent picks a different failing test to work on. After the test suite reached a 99% pass rate, each agent worked on getting a different small open-source project (e.g., SQlite, Redis, libjpeg, MQuickJS, Lua) to compile.

But when agents started to compile the Linux kernel, they got stuck. Unlike a test suite with hundreds of independent tests, compiling the Linux kernel is one giant task. Every agent would hit the same bug, fix that bug, and then overwrite each other's changes. Having 16 agents running didn't help because each was stuck solving the same task.

The fix was to use [GCC](<https://gcc.gnu.org/>) as an online known-good compiler oracle to compare against. I wrote a new test harness that randomly compiled most of the kernel using GCC, and only the remaining files with Claude's C Compiler. If the kernel worked, then the problem wasn’t in Claude’s subset of the files. If it broke, then it could further refine by re-compiling some of these files with GCC. This let each agent work in parallel, fixing different bugs in different files, until Claude's compiler could eventually compile all files. (After this worked, it was still necessary to apply delta debugging techniques to find pairs of files that failed together but worked independently.)

### Multiple agent roles

Parallelism also enables specialization. LLM-written code frequently re-implements existing functionality, so I tasked one agent with coalescing any duplicate code it found. I put another in charge of improving the performance of the compiler itself, and a third I made responsible for outputting efficient compiled code. I asked another agent to critique the design of the project from the perspective of a Rust developer, and make structural changes to the project to improve the overall code quality, and another to work on documentation.

## Stress testing the limits of agent teams

This project was designed as a capability benchmark. I am interested in stress-testing the limits of what LLMs can just *barely* achieve today in order to help us prepare for what models will reliably achieve in the future.

I’ve been using the C Compiler project as a benchmark across the entire Claude 4 model series. As I did with prior projects, I started by drafting what I wanted: a from-scratch optimizing compiler with no dependencies, GCC-compatible, able to compile the Linux kernel, and designed to support multiple backends. While I specified some aspects of the design (e.g., that it should have an SSA IR to enable multiple optimization passes) I did not go into any detail on how to do so.

Previous Opus 4 models were barely capable of producing a functional compiler. Opus 4.5 was the first to cross a threshold that allowed it to produce a functional compiler which could pass large test suites, but it was still incapable of compiling any real large projects. My goal with Opus 4.6 was to again test the limits.

### Evaluation

Over nearly 2,000 Claude Code sessions across two weeks, Opus 4.6 consumed 2 billion input tokens and generated 140 million output tokens, a total cost just under $20,000. Compared to even the most expensive Claude Max plans, this was an extremely expensive project. But that total is a fraction of what it would cost me to produce this myself—let alone an entire team.

This was a clean-room implementation (Claude did not have internet access at any point during its development); it depends only on the Rust standard library. The 100,000-line compiler can build a bootable Linux 6.9 on x86, ARM, and RISC-V. It can also compile QEMU, FFmpeg, SQlite, postgres, redis, and has a 99% pass rate on most compiler test suites including the [GCC torture test suite](<https://gcc.gnu.org/onlinedocs/gccint/Torture-Tests.html>). It also passes the developer's ultimate litmus test: it can compile and run Doom.

The compiler, however, is not without limitations. These include:

* It lacks the 16-bit x86 compiler that is necessary to boot Linux out of real mode. For this, it calls out to GCC (the x86\_32 and x86\_64 compilers are its own).
* It does not have its own assembler and linker; these are the very last bits that Claude started automating and are still somewhat buggy. The demo video was produced with a GCC assembler and linker.
* The compiler successfully builds many projects, but not all. It's not yet a drop-in replacement for a real compiler.
* The generated code is not very efficient. Even with all optimizations enabled, it outputs less efficient code than GCC with all optimizations *disabled.*
* The Rust code quality is reasonable, but is nowhere near the quality of what an expert Rust programmer might produce.

The resulting compiler has nearly reached the limits of Opus’s abilities. I tried (hard!) to fix several of the above limitations but wasn’t fully successful. New features and bugfixes frequently broke existing functionality.

As one particularly challenging example, Opus was unable to implement a 16-bit x86 code generator needed to boot into 16-bit real mode. While the compiler can output correct 16-bit x86 via the 66/67 opcode prefixes, the resulting compiled output is over 60kb, far exceeding the 32k code limit enforced by Linux. Instead, Claude simply cheats here and calls out to GCC for this phase (This is only the case for x86. For ARM or RISC-V, Claude’s compiler can compile completely by itself.)

The [source code for the compiler is available](<https://github.com/anthropics/claudes-c-compiler>). Download it, read through the code, and try it on your favorite C projects. I’ve consistently found the best way to understand what language models can do is to push them to their limits, and then study where they start to break down. Over the coming days, I’ll continue having Claude push new changes if you want to follow along with Claude’s continued attempts at addressing these limitations.

## Looking forward

Each generation of language models opens up new ways of working with them. Early models were useful for tab-completion in IDEs. Before long, models could complete a function body from its docstring. The launch of Claude Code brought agents into the mainstream and enabled developers to pair-program with Claude. But each of these products operates under the assumption that a user defines a task, an LLM runs for a few seconds or minutes and returns an answer, and then the user provides a follow-up.

Agent teams show the possibility of implementing entire, complex projects autonomously. This allows us, as users of these tools, to become more ambitious with our goals.

We are still early, and fully autonomous development comes with real risks. When a human sits with Claude during development, they can ensure consistent quality and catch errors in real time. For autonomous systems, it is easy to see tests pass and assume the job is done, when this is rarely the case. I used to work in penetration testing, exploiting vulnerabilities in products produced by large companies, and the thought of programmers deploying software they’ve never personally verified is a real concern.

So, while this experiment excites me, it also leaves me feeling uneasy. Building this compiler has been some of the most fun I’ve had recently, but I did not expect this to be anywhere near possible so early in 2026. The rapid progress in both language models and the scaffolds we use to interact with them opens the door to writing an enormous amount of new code. I expect the positive applications to outweigh the negative, but we’re entering a new world which will require new strategies to navigate safely.

### Acknowledgements

Special thanks to Josef Bacik, Edwin Chen, Bernardo Meurer Costa, Jake Eaton, Dan Kelley, Felix Klock, Jannet Park, Steve Weis, and many other people across Anthropic for their assistance and contributions.


</details>
