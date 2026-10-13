# 借助 Copilot 把运行时迁移到 Rust

复盘大型代码迁移，介绍原地转换、语言互操作、会话数据、缓存与静态分析的作用，并说明工程规模和实践经验。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-09-16](<https://github.blog/ai-and-ml/generative-ai/migrating-the-github-copilot-runtime-to-rust-using-copilot/>)；更新：[2026-09-23](<https://github.blog/ai-and-ml/generative-ai/migrating-the-github-copilot-runtime-to-rust-using-copilot/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/generative-ai/migrating-the-github-copilot-runtime-to-rust-using-copilot/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/借助 Copilot 把运行时迁移到 Rust.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 Copilot 将 GitHub Copilot 运行时迁移到 Rust

[GitHub Copilot CLI](<https://github.com/features/copilot/cli/>)、[GitHub Copilot 应用程序](<https://github.com/features/ai/github-app>) 和 [GitHub Copilot SDK](<https://github.com/github/copilot-sdk>) 均由 Copilot agent 运行时（一个代理工具）提供支持可以嵌入到应用程序和服务中。它最初是在 Node.js 和 V8 JavaScript 引擎上的 TypeScript 中编写的，用于现在的 [GitHub Copilot 云 agent](<https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent>) (CCA)，并且运行时作为运行时及其功能保留在该堆栈上增长迅速。

现在情况已经改变了。使用 GitHub Copilot 应用程序和 Copilot CLI，我们将运行时完全重写为多个 800,000 生产线 Rust。 AI agents 编写了大部分代码，跨越 128 拉取请求，这些请求落在 main 中并增量交付，而不是在最后等待单个切换。在此过程中，一些不可避免的回归很快就被发现并修复了，同时运行时的性能也提高了几个数量级。在 agents 之前，一个需要整个开发团队需要一两年才能完成的项目现在主要由一个开发人员在短短几个月内完成，而团队的其他成员则继续极大地扩展运行时的功能和范围。

## 为什么我们需要移植

Copilot agent 运行时不仅仅是 Copilot CLI 背后的引擎。它支持越来越多的 Microsoft、GitHub 和生态系统解决方案，其中每个 AI 支持在架构上是围绕相同运行时的 shell 以及解决方案所需的任何自定义。这不仅包括 GitHub Copilot CLI 和 GitHub Copilot 应用程序，还包括 VS Code、Visual Studio、CCA、Copilot 代码审查 ([CCR](<https://docs.github.com/copilot/concepts/agents/code-review>))、 Copilot Cowork、Copilot Studio、Excel 和 Outlook、PowerPoint 和 Word 以及……继续。

这些是非常不同的产品，它们都不想也不应该需要实现生产 agent 线束中的所有内容。他们想要所有的智能、安全性、可靠性和性能，并且他们希望将其共享，以便在一个地方进行修复可以在所有地方修复它。上一段中列出的大多数产品最初实现了自己的 agent 循环，但此后已将其替换为 GitHub Copilot SDK，这是 Copilot agent 运行时的入口点。这样做使他们能够专注于其核心业务价值，并将细节留给运行时。考虑到行业的发展速度和所使用的 agent 循环需要在激烈的竞争中始终保持最佳状态，这一点就显得尤为重要。

所以，共享运行时，很好。问题在于所共享事物的性质。

如果我们看一下 CLI，它在逻辑上是 agent 循环之上的终端 UI (TUI)。碰巧的是，整个堆栈在 TypeScript 中实现，使用 Node.js 作为框架，使用 V8 作为执行引擎，使用 Ink 和 React 作为 UI。对于 TUI 应用程序来说，这是一个值得尊敬的选择； TypeScript 和 Node.js 可以广泛访问并支持非常快速的应用程序开发。对于控制台应用程序的需求，启动、响应能力、吞吐量和内存消耗方面的性能影响也是合理的。不幸的是，当您考虑在其他环境中使用该实现时，它们的合理性要低得多，具有其他限制，并且由于内存开销低而需要快速启动和出色的服务器密度等。

CLI 的架构及其运行时也带来了挑战。整个行业运行得非常快，在这种背景下，真正聪明的人会根据交付速度和市场范围做出决策。 Copilot CLI 最初是快速编写和发布的，在这样做的过程中，TUI 和运行时相当交织在一起，而不是分成离散的层。然后，当需要 SDK 来以编程方式访问该运行时时，在没有明确分层的情况下，我们做出了一个务实的决定，将 SDK 分层在 CLI 之上，尽管从逻辑上讲，您会期望相反的架构。 CLI 不再只能通过用户在命令行提供的命令进行访问，而是更新了一种可以无头运行的模式，从标准输入读取类似的命令并将响应写入标准输出。然后，JSON-RPC 协议可用于将外部进程与 CLI 之间的函数调用编组。然后，SDK 可以嵌入到任意消费程序中，该程序将生成一个 CLI 进程来托管进程外的 agent 循环，并通过此 JSON-RPC 机制在远程进程中调用 SDK 函数。整洁的。赶紧出了门。灵活的。但对于那些消费应用程序的性能（启动、内存、吞吐量）和可靠性来说并不是很好。从 SDK 创建新的 `CopilotClient` 意味着生成另一个进程：

```
const client = new CopilotClient();
await client.start(); // spawns the CLI as a subprocess
const session = await client.createSession({
    /* ... */
});
```

该过程需要启动并托管节点和 V8。这意味着解析从 CLI 中的 TypeScript 代码生成的大量 JavaScript，为其生成字节码，并可能在后面的 JIT 层中优化热代码。这意味着与 V8 相关的所有内存开销。这意味着继承 Node 的线程模型，默认情况下，它会将我们推向所有 CPU- 绑定工作被序列化的模型。这意味着强制进行进程外通信只是为了进行函数调用。这意味着每一种语言的每个 SDK 消费者都会发送 Node.js 或包含 V8 的捆绑二进制文件。这意味着 C#、Python、Go、Java 和 Rust SDK 都为每个客户端的整个第二语言运行时付费，按照最低工作集 100 MB 的顺序，否则他们的应用程序将无法使用该运行时。这意味着每个事件、每条消息以及每个抽象会话文件系统的读写都被推送到进程边界。这意味着 Node 崩溃导致会话中断。这意味着部署此系统的任何人至少有两个流程需要监督、监控和调试。

相反，我们想要一个运行时：

* 不包括 TUI，这是它自己的库 TUI 和其他应用程序和服务可以干净地正确分层。
* 使用具有最小依赖性和最小开销的语言来实现。
* 以一种可以干净地嵌入到进程中而不是被迫脱离进程的方式实现。
* 使用具有性能、可扩展性和可靠性顶级特征的语言来实现。
* 采用非常适合互操作的语言实现，因此它可以被所有六种 Copilot SDK 语言版本（C#、TypeScript、Python、Rust、Go、 Java）与该堆栈的外部函数接口（FFI）机制。
* 使用工具链实现，提供更现代的安全态势，供应链风险更低，并且对构造正确代码有更大的支持。

出于所有这些原因，以及软性原因（例如团队经验和行业方向），我们选择了 Rust。这绝不是说每个大型 TypeScript 程序都应该变成 Rust。我们的要求强调通过 C ABI 嵌入、低启动和稳态开销以及可预测的资源使用。 Rust 使这些目标成为可能，但代价是其他复杂性，例如我们必须明确地表示生命周期和共享状态（稍后讨论的生命周期回归突出了其含义）。正确的目标语言因应用程序的不同而不同。

随后开展了两项关键的相关任务：

1. 将 TUI- 特定代码与运行时分开，以便前者严格分层在后者之上，更具体地说，严格分层在 SDK 的公共表面区域之上。今天，CLI 仍然在几个地方直接调用运行时内部；将其完全移动到 SDK 的表面区域正在进行中。
2. 将该运行时层移植到 100% Rust，从而产生一个纯本机二进制文件，公开 C ABI 以供所有语言前端进行进程内使用，并在仍需要进程外时公开 stdin/stdout-based 或基于套接字的服务器。

这篇文章主要介绍第二个问题：将运行时移植到 Rust。

![Rust 重写之前和之后的 Copilot 运行时架构，显示了旧的 SDK- 到 CLI 进程边界以及新的进程内和进程外托管路径。](<https://github.blog/wp-content/uploads/2026/09/architecture-before-after.svg?resize=1024,512>)

## 以前是什么样子的

5 月初的初始移植计划 2026 估计运行时间大约为 TypeScript 的 130,000 行。出于范围界定的目的，这种初步测量相当准确，但事实证明，在两个关键方面也存在严重误导。与移植同时进行：

1. 仍然包裹在 TUI 层中的部分被下推到运行时层。整个组件和大部分代码最初在估计中被忽略，后来被认为与移植相关。
2. 贡献大量新 TypeScript 的拉取请求不断增加存储库中 TypeScript 的数量。数十名代理协助开发人员每周合并数百个拉取请求。

考虑到所有因素，我估计大约 430,000 条生产线 TypeScript 最终通过了该港口。这些相同的因素也使得整个过程中很难看到进展：直到接近尾声，生产 TypeScript 数量似乎保持相对稳定，即使没有略有增加，因为移植与即将到来的工作保持同步。

这进一步令人困惑，因为在一段时间内还有与端口分离的传入 Rust 代码；在移植工作的早期，传入的代码更有可能由 TypeScript 主导，而在移植工作的后期，它更有可能由 Rust 主导。

![TypeScript 下降到零，而生产 Rust 上升到大约 830,000 行，Rust 单元测试上升到大约 469,000 行。](<https://github.blog/wp-content/uploads/2026/09/runtime-line-history.svg?resize=1024,563>)

在移植期间，运行时接收了TypeScript的〜300,000生产线并棚了〜430,000，同时进入了〜1,200,000生产线Rust并离开了〜365,000。换句话说，上图中 TypeScript 线的表面稳定性实际上隐藏了大量的 TypeScript 流失。

## 就地移植策略

该图表还强调了移植工作的一个重要方面：就位。

重写此规模有两种主要方法：

1. **大爆炸。** 新的 Rust 运行时是作为完整的替代方案开发的，然后在准备就绪时立即全部交换。这种大爆炸式的切换有两种变化。一个。 **停止世界。** 当重写发生时，每个人都停止 `main` 分支上的其他工作，重写在 `main` 中完成。 b. **并行开发。**重写发生在功能分支中，同时工作在主分支中继续，重写不断尝试跟上并合并主分支的更改。
2. **到位。**这是作为逐个组件的端口完成的，其中运行时一次增量地重写一个部分。这种就地方法也有两种变体。一个。 **原子替换。** 每块都从 TypeScript 原子翻转到 Rust，剩余的 TypeScript 和新的 Rust 之间的互操作提供了连续性。随着时间的推移，生产运行时的TypeScript越来越少，越来越多的Rust，直到有一天，不再有TypeScript，只有Rust。 b. **A/B.** TypeScript 和 Rust 组件都保留为热插拔选项，而不是在移植时删除组件，一旦信心稳定下来，TypeScript 就会被删除。

出于多种原因，我们选择了选项 2a：

* **没有人遇到停工情况。** 主分支继续活跃。每个不直接参与移植的开发人员都可以继续保持下去，只有当他们可能在很长一段时间内进行的拉取请求碰巧触及同时移植的代码时才会受到影响，在这种情况下，他们需要重新设置基础并让他们的 agents 帮助移植他们正在进行的更改。
* **运行时的主分支始终是可交付的。**每个拉取请求都会使用调用Rust的薄填充程序替换现有的TypeScript实现，并在一次原子更改中删除旧代码。新代码立即就地执行。
* **重写是增量且可审查的。**每个拉取请求都会移植一个组件或切片，因此更改的范围更小，并且差异更容易审查，无论是由人还是由agents或两者共同审查。
* **大多数端口都相当小且独立**，最大限度地减少并发拉取请求的偏差。在某些情况下，如果 TypeScript 组件太大，可以首先将它们重构为更容易移植的组件。
* **所有现有的端到端测试**，跨CLI和SDK，每一步都针对新的Rust代码运行，这给了我们信心和大量的验证。如果拉取请求导致所需的测试失败，它就不会落地。

我们还回避了涉及同时维护同一组件的多个版本的选项 2b 变体。在过去的几个月里，每周都会有数百个拉取请求合并到存储库中，代码库正在不断快速发展。使用两种不同语言使用相同代码的两个不同版本并使用两组不同的依赖库会增加大量的复杂性。其中一些组件也不是完全隔离的；虽然有些在逻辑上是独立的，具有简单的 API，可供系统的其余部分访问它们，但另一些则有明显的卷须，并且使每个组件的图形可热插拔是一场噩梦。可能从谨慎的并行切换中获益最多的子系统正是并行最困难的子系统。例如，会话编排不是一个纯函数，您可以在某些实验标志上使用 `if`/`else` 调用两个不同版本的函数。它拥有可变状态，双向驱动回调，并通过几乎所有其他子系统进行线程处理，因此“同时运行并比较”意味着维护保存对话状态和服务的组件的两个不同副本，并祈祷它们在数百个并发编辑中保持同步。导致组件难以移植的耦合与导致影子几乎不可能在不冒引入更多回归风险的情况下避免的风险相同。能够以这种方式进行交换的好处主要是获得信心，我们可以通过其他方式做到这一点。

验证也是通过增量推出进行的。通过大爆炸切换方法，我们可以将所有内容保存在一个长期分支中，移植整个运行时，然后切换一次。这意味着消费者可以立即体验每条移植的线路，包括通过回购测试的所有回归。增量地推出部分变更，这里两个组件，那里一个组件，使我们能够在部署的构建中通过实际消费者使用情况（通常是第一方）获得最后一英里的验证。 Microsoft 和 GitHub），同时将回归风险降至最低。在大约十四周半的移植窗口中， `main` 已发货 135 发布，包括 100 pre-release versions and 35 稳定版本，平均约为 1.3 每天发布。大致 1.3 每天都会打开端口拉取请求，这样每个版本都包含一小部分已知的移植组件（我们通常尝试但并不总是成功地首先在预发布中发布端口）。在过去的 7 天里 npm 仅占示例、预发布版本 10.5% 下载量，表明最初的曝光相对有限，同时我们监控反馈渠道以获取损坏的信号，并在下一个预发布中迅速修复。报告的问题更容易与已知的近期更改相关联，并且更容易找到根源并快速修复。通过这种方式，在较长时间内逐步进行移植实际上是一种功能而不是障碍（即更快并不总是更好）。到八月 21, the runtime was 100% 生产 Rust: 832,378 lines of production Rust 和 468,689 lines of Rust 单元测试，除了 174,675 的行 E2E TypeScript 测试。分开的 GitHub Copilot SDK 存储库又添加了一个~130,000 lines of E2E 跨测试代码 Node.js, Python, Go, C#, Rust, 和 Java.

![从 5 月 12 到 8 月 21 的时间线显示了 128 端口拉取请求合并大小，按更改的行和 135 公共 CLI 发布；最大的港口变化集群即将竣工。](<https://github.blog/wp-content/uploads/2026/09/port-fix-release-timeline-1.svg?resize=1024,397>)

## 开始吧

在全力以赴之前，我们获得了信心并证明了一切。我们从两个拉取请求开始，建立了 Rust 工作区、工具链、lint 规则、CI、构建管道和编码指令，然后引入运行时包以及代码生成和互操作模式，同时移植专门选择的纯逻辑原语集合，因为它们没有 I/O 或共享状态，并且已经进行了强大的测试。只有在这些着陆后，第一个主要端口拉取请求才需要三个无副作用的助手完成整个过程。这些充当了运输试点，将有关存储库布局、FFI、包装、测试和审查的假设转变为随后更大的港口将重复使用的惯例。基本上，我们对机器进行了端到端的测试。该计划继续从叶向内排序工作，使用纯助手、内容排除、shell 实用程序和建立翻译​​和测试模式的会话文件系统操作。随后出现了有状态子系统，并在这些部分上构建了工具、挂钩、模型客户端和 MCP。会话编排（到目前为止，运行时中耦合度最高且最不自然并行的部分）将接近尾声。

![128 从 5 月到 8 月落地端口拉取请求的时间表，从小型基础组件进展到更大的编排和会话工作。](<https://github.blog/wp-content/uploads/2026/09/pr-timeline.svg?resize=712,1024>)


|时期|拉取请求|中位数变化线|
| --- | --- | --- |
|五月 1–15| 8 | 3,250 |
|五月 16–31| 2 | 9,421 |
|六月 1–15| 40 | 5,073 |
|六月 16–30| 31 | 8,253 |
|七月 1–15| 10 | 9,514 |
|七月 16–31| 14 | 28,159 |
|八月 1–15| 19 | 13,861 |
|八月 16–30| 4 | 99,445 |

早期的端口，小叶子组件，移动得很快。但较大的子系统并不是以原子一步的方式移动的。例如，MCP 支持通过七个专用拉取请求取得进展，而工具则通过六部分系列取得进展，然后需要额外的工作来移动编排并淘汰剩余的 TypeScript。挂钩、身份验证、遥测、插件、设置和持久性遵循类似的路径。

实际上，有用的移植单位并不总是“组件”。这通常是相关行为区域的浪潮：首先移动纯逻辑，然后移动状态所有权，然后移动编排，然后删除后备，最后在临时互操作消失后简化 Rust。

## 互操作性

该端口中有两个涉及互操作的主要层：

1. **临时内部互操作。** 任何时候将函数移植到 Rust 时，都需要从调用初始 TypeScript 函数的任何 TypeScript 调用该函数。同样，我们需要启用 Rust 函数来调用 TypeScript 回调。这种互操作需求是一个实现细节并且非常不稳定。随着 Rust 内表面积的增加，所需的 TypeScript 垫片的数量也会增加，因为它们是 1:1 以及需要从 TypeScript 调用的任何 Rust 方法。当这些调用者被移植到 Rust 时，现有的垫片层将被删除，并放置一个新层。最终我们到达运行时库的公共入口点，垫片就消失了。
2. **SDK 表面。** 所有 SDK 库都需要能够位于运行时之上并公开其功能。在移植前的世界中，这是通过通过双向 JSON-RPC 层公开运行时来完成的，其中 SDK 发送函数调用请求作为 JSON-RPC 方法调用有效负载，运行时解析请求并调用相关的 API，然后通过相同的传输将结果发送回SDK 解析并返回。反方向也存在；运行时需要能够回调 SDK 客户端，例如钩子通知和权限要求，这些在 SDK 客户端中作为回调使用任何被认为惯用的语言功能（例如 C# 中的委托）出现。

我们通过来自napi-rs项目的`napi` Rust箱实现了(1)，该项目的存在是为了在Rust中构建Node本机插件。您使用 `#[napi]` 注释函数，napi-rs 宏会生成 N-API 注册胶，使该函数可从 JavaScript 调用，并在生成的 `index.d.ts` 中为其添加 TypeScript 声明。同步 Rust 函数变成普通的 JavaScript 函数，`async fn` 变成返回 Promise 的 JavaScript 函数，带注释的结构 `#[napi(object)]` 变成另一侧的普通对象。

交通也必须双向移动。许多移植的组件暂时依赖于尚未移植的东西，因此 Rust 需要回调到 TypeScript，例如 Rust 中的工具实现请求静止 TypeScript 模型层进行推理，或引发钩子，或请求其想要运行的命令的权限决策。 napi-rs 使用“线程安全函数”来处理这个问题，它让在 Tokio 工作线程上运行的 Rust 代码调用 Node 主线程上的 JavaScript 回调。节点安装回调一次，Rust 保存它并在需要时调用它。其中每一个在构造上都是临时的：回调存在只是因为另一端的东西仍然是 TypeScript，并且当该东西被移植时它会被删除。

![临时 Rust N-API 导出及其 TypeScript 调用站点在增量端口期间增长，然后随着调用者移至 Rust 且临时互操作界面消失而下降。](<https://github.blog/wp-content/uploads/2026/09/interop-surface.svg?resize=1024,546>)

临时接缝在 8 月 3 达到顶峰，具有 2,019 内部 N-API 导出和 3,356 TypeScript 调用站点。完成时，运行时完全是Rust，因此没有内部互操作：0临时内部N-API导出和0TypeScript调用站点仍然存在。 （我之前提到过，CLI 仍然对我们正在努力删除的运行时具有一些内部访问权限；这些导出不计入此处。）

第二个互操作层，即 SDK 表面，是两者中永久的一层。 Copilot SDK 支持六种语言：TypeScript、Python、Go、C#、Java 和 Rust。它们都使用相同的双向 JSON-RPC 合约，并且最初它们都以相同的方式实现：在无头模式下生成 Copilot CLI 作为子进程，并通过管道或套接字与其进行通信。在移植期间这仍然是默认设置。这还意味着任何语言的 SDK 消费者都会运送或定位完整的 Node 实现，为每个事件和每条消息支付一个进程跳跃，并监督两个进程而不是一个进程。

将运行时移植到 Rust 使另一个选项变得可行。附带的 `runtime.node` 是一个普通的平台共享库（`.node` 扩展是 Node.js 本机插件约定；其下方是 `.dll`、`.so` 或 `.dylib`），现在它在相同的发动机。有 napi 门，Node 进程将其作为本机插件加载；这就是 CLI 的路径（今天……将来，目的是它将完全经过 SDK 路径）。还有一个 C ABI 门，任何语言都可以将其加载到自己的进程中并通过 FFI 进行调用。通过每种语言的本机互操作机制选择相同的进程内运行时：

| SDK |原生桥|正在进行的客户选择|
| --- | --- | --- |
| C# | P/Invoke | `new CopilotClient(new CopilotClientOptions { Connection = RuntimeConnection.ForInProcess() })` |
| Go | `purego` | `copilot.NewClient(&copilot.ClientOptions{Connection: copilot.InProcessConnection{}})` |
| Java | JNA | `new CopilotClient(new CopilotClientOptions().setConnection(RuntimeConnection.forInProcess()))` |
| Python | `cffi` | `CopilotClient(connection=RuntimeConnection.for_inprocess())` |
| Rust | `libloading` | `Client::start(ClientOptions::new().with_transport(Transport::InProcess)).await?` |
| TypeScript | `koffi` | `new CopilotClient({ connection: RuntimeConnection.forInProcess() })` |

Rust 重写以及进程内和进程外托管之间的选择是不同的维度。完成的 Rust 运行时支持两者：它可以在 SDK 消费者进程内部运行，也可以在现有 JSON-RPC 服务器边界后面运行。这些进程内入口点目前是选择加入的，同时我们对与消费应用程序共享进程以及因此的故障边界有了信心。传输之上的所有内容都保持不变 SDK API：会话、事件、工具、权限和回调不关心它们的 JSON-RPC 字节是否穿过管道或函数调用。

第二扇门的有趣之处在于它的大小。它只有 19 导出函数：四个用于服务器生命周期，四个用于会话注册和配置，八个用于连接，三个用于嵌入式主机。在这些函数后面，共享合约当前包含 364 调度路由：340 可由 SDK 消费者调用，而 24 作为运行时到 SDK 的回调在另一个方向运行。纳皮门要大得多，需要为每一条调度路线提供功能。 C ABI 门是基于调度的：API 方法根本不导出，而是以写入连接的 JSON-RPC 字节的形式传输，结果、事件和服务器到客户端的请求通过主机提供的回调返回。添加、更改或删除 API 方法会触及引擎的调度表，但不会触及 ABI。 SDK 绑定这些 19 入口点一次，并通过它们动态到达整个仍在增长的 API 表面。

这就提出了一个明显的问题：为什么在不再跨越进程边界的调用中仍然存在 JSON-RPC？

答案是它使进程内托管成为一种直接插入而不是重写。每个 SDK 都已经有一个工作的 JSON-RPC 客户端，具有框架、请求和响应关联以及服务器到客户端方向的处理程序。将 FFI 安装为客户端下方的另一种传输，将字节路径从管道或套接字移动到函数调用，并保持其上方的所有内容不变。六个 SDK 将进程内托管作为附加的、选择加入的传输，而现有的 SDK 保持不变。如果我们为每个 API 方法定义一个类型化的 C 函数，则每个 SDK 将需要第二个绑定层，每个新的 API 方法将需要另外六个绑定，并且 ABI 将成为我们必须版本化的二进制兼容性表面。

对于真正远程的运行时，我们仍然需要 JSON-RPC，无论是跨子进程边界还是通过 TCP。在进程中保持相同的协议意味着维护一个双向 API 和调度系统，而不是用于远程连接的 JSON-RPC，以及用于本地连接的第二个每个方法 FFI 表面。这是一个真正的权衡，而不是一个不假思索的决定。我们避免了进程跳跃，但我们仍然在每次调用时付出JSON-RPC开销。对于推理主导的工作负载，与模型往返相比，序列化通常是小菜一碟。它在高吞吐量本地工作负载中仍然是可测量的，但目前还不足以证明在六个 SDK 绑定中复制数百个方法是合理的。如果出现性能需求，我们可以在以后轻松修改这个决定。有效负载编码是两端的私有细节，将 JSON 交换为 MessagePack 等更密集的内容不会更改单个声明的导出。稍后还可以为热路径添加类型化的按方法导出，调用相同的引擎和相同的处理程序，而无需替换字节通道，字节通道仍将是流式处理、服务器到客户端请求以及很少调用的方法的长尾的基础，而定制导出不会带来任何好处。

## 会话数据显示什么

这篇文章中的几乎每个数字都来自两个来源之一。第一个来源是私有 `github/copilot-agent-runtime` 存储库的 GitHub 历史记录：拉取请求及其差异、审核评论、CI 运行等。第二个来源是 agent 会话日志。运行时（以及 CLI、应用程序等）为其运行的每个会话写入结构化事件日志：每行一个 JSON 对象，在会话发生时附加。这些日志可能包括提示、命令、命令输出、文件路径以及工具显示的潜在秘密，因此必须将它们作为敏感数据进行处理。日志位于运行会话的计算机本地；远程会话功能也可以在启用时上传它，具体取决于产品设置和组织策略。

以下是所有组成移植拉取请求的数据摘要：

|公制|计数|
| --- | --- |
|活动| 12,760,995 |
|用户留言| 31,247 |
|助理消息| 1,385,214 |
|挂钩开始和结束事件| 6,438,562 |
|工具启动| 1,857,409 |
|编译命令| 23,096 |
|测试命令| 19,485 |
|变基命令| 2,496 |
|提交命令| 7,410 |
|推送命令| 5,554 |
|已完成压实| 5,116 |

那些 31,247 用户角色消息不是我亲自输入的 31,247 提示；它们包括技能说明、自动合并标记、跨会话消息和子 agent 流量，超出了我键入或说出的大约 2,600，大约是 12 中的一个。同样，1,385,214 助理消息包括子代理和面向工具的消息，而不仅仅是在会话 UI 中向我显示的文本。该语料库包含 68 不同的事件类型和 67 不同的工具名称； 1,130,921 工具调用 61% 来自子代理而不是主会话线程。

计数没有说明为什么我插入自己 ~2,600 次。为此，我让 Copilot 为会话日志语料库中的每条人工创作的消息分配一个主要意图。

![在 2,639 人类撰写的消息中，31.0% 专注于审查、测试和 CI； 17.4% 对技术或设计决策提出质疑；和15.0%推动完整性。](<https://github.blog/wp-content/uploads/2026/09/user-message-intents.svg?resize=1024,538>)

前三个桶占我互动的 63%。只有〜40是我可以识别的会话启动，因为这很大程度上是我首先创建一个聊天来探索下一个视野，然后要求该聊天会话为每个所需的切片创建实际的移植会话。我的角色不再是“分配任务并等待”，而是更多地“操作控制循环”：检查结果、挑战技术决策、执行质量关卡，并在 agent 将中间停止点视为终点线时推动。尽管 agents 正在做“工作”，但人类的判断仍然非常重要。我的参与度不断上升……我不再负责编写语法，而是负责构建问题、定义边界、选择策略、裁决例外情况，并总体确保一切都朝着良好的方向发展。

### 一切都与缓存有关

LLM 提供商通常对输入令牌（您发送给他们的内容）收取一种费率，对输出令牌（他们发送给您的内容）收取另一种费率。计费通常由令牌完成，因为它们是进行推理所需的计算工作的有用近似：对于每个输入令牌，模型必须读取它，将其合并到其内部表示中，并将其用作确定下一个令牌的计算的一部分。然而，提供程序通常支持缓存这些计算的结果，这样，如果已经处理了提示的相同前缀，则提供程序可以重用缓存中的中间计算，而不是从头开始重新计算它们。这降低了处理这些代币的成本，并且节省的成本可以惠及消费者。因此，输入令牌通常以多种速率进行广告，包括从高速缓存读取的输入令牌的速率。

折扣力度很大！通常，提供商会按 90% 折扣对缓存命中进行计费，因此，例如，提供商可能会对 1 万个输入令牌收取 2.00 美元，但对 1 万个缓存输入读取令牌仅收取 0.20 美元。换句话说，您真的非常希望保持良好的即时缓存，以便您的费用减少一个数量级。

移植工作的数据表明我们在这里做得很好。提示缓存命中率为96.22%：缓存读取除以所有输入端令牌量（缓存读取加上缓存写入加上新输入）。缓存写入为 3.07%，新输入为 0.71%。这并非偶然。 GitHub Copilot 专门塑造了 agent 循环，以保留长而稳定的前缀（系统提示，然后是工具定义，然后是累积的对话），因此每个回合都会附加到模型已经处理过的上下文。上下文中昂贵的部分只需支付一次，然后在每次后续调用时以较小的货币成本重新读取。这也是长时间自治会话的经济性能够维持在一起的原因。一个为期三百小时的端口，在数以万计的调用中从头开始重新读取其整个增长上下文，其成本将与我们所看到的不同数量级。 Agent 线束开发人员花费大量精力试图避免破坏提示缓存，模型供应商通常会提供新功能来帮助他们做到这一点。

![跨移植会话的提示缓存组成：96.22%缓存读取、3.07%缓存写入和0.71%新输入。](<https://github.blog/wp-content/uploads/2026/09/prompt-cache-composition.svg?resize=1024,333>)

压缩讲述了一个互补的故事。在端口会话中，GitHub Copilot自动压缩上下文5,116次（会话填满其上下文窗口并总结自身以继续进行的时刻）。单个会话基础设施端口拉取请求在其多天的生命周期内压缩了 647 次，而一个小端口从未压缩过一次。持续数百小时的自主工作之所以成为可能，是因为 agent 可以一遍又一遍地回收其工作内存而不丢失线程。这数千个总结中的每一个都可能是一个有损交接可能悄悄使端口脱轨的点，但大多数情况下都没有。 Copilot 的子代理也极大地减少了压缩。每个子代理都有自己的上下文，因此父会话可以有效地提出问题，让子代理启动并在计算答案时发挥相当多的上下文，然后将答案报告回父代理。父母的背景不必受到所有这些中间信息的影响。

上面的“大部分没有”在会话日志中可见。当双方至少存在 20 工具调用时，我将 Copilot 将每个成功的压缩与其周围的工作配对。这产生了~4,000类似的窗口。压缩前 agent 在 20 工具中调用的操作的混合看起来与压缩后所做的在规模上相似（探索 46.5% 之前和 48.1% 之后，突变 8.4% 之前和 6.0% 之后，验证4.7%之前和4.0%之后，故障1.0%之前和1.5%之后）。如果压缩经常失去思路，我们预计后侧会明显地重新定位，阅读量激增，编辑量崩溃，而 agent 重新发现它在哪里以及它应该做什么。相反，这个方向只有轻微的转变。

### 是的，静态分析有帮助

有一个流行的梗，认为 Rust 是 AI- 生成的代码的一个非常好的目标，因为 Rust 严格的编译器可以捕获模型出错的地方。会话日志让我们可以测试该理论，至少对于类似于此移植工作的任务来说是这样。

直接验证命令结果捕获了 `rustc` 错误代码的 8,678 次出现。四个最大的诊断系列涵盖 84%：

* 37%：名称和导入解析，由 `E0425` 主导（“在此范围内找不到值”）
* 22%：缺少方法或字段
* 14%：类型不匹配
* 11%：不满足特征界限

其中每一个都是普通的接线：名称输出略有错误，签名未对齐，字段已重命名，抽象未实现。这些是批量翻译容易和意外产生的错误，而编译器恰恰可以很快捕获这些错误。

但请注意该列表中缺少的内容：任何真正特定于 Rust 的内容。这四个类别中的每一个都是最基本的静态类型，C# 或 Java 或 Go 编译器也可以捕获所有这些类别，其中一些类别具有更友好的诊断功能，并且所有类别的速度都要快得多。如果这是将 agents 指向 Rust 的参数，那么它实际上就是将它们指向任何静态类型语言的参数。强类型编译器 and/or 是一种具有出色静态分析和 linting 的语言，它确实非常适合这项工作，agents 将其用作快速反馈循环。在 4,478 直接 `cargo check` 运行中，更严格的结果匹配器捕获了结果，87.1% 恢复干净，这是您通过小增量编辑和不断重新编译得到的结果。

相比之下，所有权、借用和生命周期错误的总和仅是编码诊断的1.7%。借用检查员，在每次关于 Rust 很难的谈话中占据主导地位，是一个安静的背景存在。编译器几乎把所有的错误精力都花在了无聊的机械错误上。

### Agents喜欢读书

我们还可以检查工具调用数据的会话事件语料库，并从中提取一些关于 agents 如何度过时间的有趣观察。

|**工具**|**通话**|**中位数**|**测量时间**|
| --- | --- | --- | --- |
| `powershell` | 630,423 | 3 s | 2,833.9 |
| `view` | 590,988 | 0 s | 621.7 |
| `rg` | 281,783 | 1 s | 408.4 |
| `grep` | 126,483 | 1 s | 115.3 |
| `apply_patch` | 53,715 | 0 s | 17.0 |
| `edit` | 40,591 | 1 s | 24.1 |
| `read_powershell` | 36,728 | 90 s | 1,203.9 |
| `task` | 13,080 | 274 s | 2,329.0 |

我的第一个收获是 agents 花在收集证据上的时间远多于更改代码的时间。在显示的文件读取和搜索工具与编辑工具中，他们10x进行了与突变一样多的探索。读取文件、搜索存储库、运行诊断命令为主；编辑工作相对较小。 AI喷出代码的流行形象几乎是倒退的；在这个规模上，这项工作看起来更像是迭代调查，检查当前状态，形成假设，做出有针对性的改变，冲洗和重复。

授权放大了这种模式。子代理主要用于对独立问题进行探索，而主代理 agent 更有可能拥有编辑权并整合答案。对于此类项目来说，这是一个有用的分工：许多上下文可以并行调查，但保持突变更接近协调 agent 可以减少冲突的更改并保留连贯的实施策略。

shell 流量还显示自主软件工作中有多少是状态管理。只读 Git 检查是最常见的命令模式，因为 agents 不断有效地询问“我在哪里？”他们正在寻找发生了什么变化，rebase 做了什么，另一个会话发生了什么，以及分支与快速发展的偏离了多远 `main`。这项定向工作使许多长期运行的工作能够针对相同的移动代码库进行操作，而不会盲目地相互覆盖。

查看 shell 工具流量内部，最常见的命令系列使定向和验证之间的平衡更加清晰：

|**命令系列**|**通话**|**中位数**|**测量时间**|
| --- | --- | --- | --- |
|`git`检查| 300,530 | 2 s | 608.1 |
|`git`其他| 89,865 | 3 s | 243.1 |
|搜索| 85,482 | 2 s | 147.6 |
| `pnpm test` | 13,852 | 22 s | 219.1 |
| `pnpm lint` | 9,757 | 29 s | 177.0 |
| `cargo test` | 8,437 | 120 s | 364.2 |
| `git commit` | 7,410 | 11 s | 39.7 |
| `cargo fmt` | 5,223 | 18 s | 77.2 |
| `cargo check` | 4,492 | 120 s | 176.9 |
| `pnpm build` | 3,630 | 180 s | 215.6 |
| `cargo clippy` | 2,115 | 135 s | 107.4 |
| `git rebase` | 2,496 | 7 s | 9.9 |
| `cargo build` | 566 | 104 s | 20.3 |

### 选型

GitHub Copilot 允许单个会话在对话中更改模型，并让不同的会话运行不同的模型，因此模型选择成为每个切片的决策。日志中显示两种不同类型的模型决策。在驱动每个端口的主线程上，我们选择了模型和推理工作。在会话中，当 agent 启动子代理或子会话来探索或完成有界任务时，编排模型会选择这些模型。

![主要移植会话中的每周模型组合，集中于少数模型并在移植过程中发生变化。](<https://github.blog/wp-content/uploads/2026/09/weekly-model-mix.svg?resize=1024,512>)

对于子代理，模型组合看起来有点不同，使用 agent 而不是人类针对吞吐量和成本而不是最难的判断调用进行优化。它生成的子代理最常在 Claude Opus 4.8、GPT-5.6 Sol、Claude Haiku 4.5 和 GPT-5.5 上运行，其次是 Gemini 3.1 Pro 和 Claude Opus 5。然而，至少在移植发生时，三个常用的 agent 定义固定了他们的模型选择（`explore` 和 `task` 到 Claude Haiku，`research` 到 Claude Sonnet），因此该卷的很大一部分是由子代理的选择决定，而不是单独选择模型。

![移植期间产生的子代理之间的每周模型混合，数量集中在 Claude Opus 4.8、GPT-5.6 Sol、Claude Haiku 4.5 和GPT-5.5.](<https://github.blog/wp-content/uploads/2026/09/weekly-subagent-model-mix.svg?resize=1024,512>)

## 与 agent 车队合作

GitHub Copilot 应用程序支持可视化活动拉取请求会话、每个会话的状态，并在它们之间轻松切换，使其成为管理端口固有的大量并发工作的理想选择。但真正让它发光的事情之一是它的会话与其他会话交互的能力。

一个会话可以创建其他会话，并且可以在其他会话运行时向它们发送消息。 Each session, parent or child, gets its own worktree, its own branch, and its own agent loop;它与产生它的会话是分开的，而不是在其中运行的东西。这与子代理不同，子代理在父级自己的工作区中运行并将其答案返回到父级的上下文中。两者都是针对不同事物的有用构造。

作为一个会话如何创建其他会话的示例，最困难的端口之一是 `session.ts` 文件。该文件已有机增长为 TypeScript 的 ~30,000 行。它代表了会话的主干，并有效地水平跨越整个运行时，接触和被几乎每个组件接触，位于状态、事件、工具、模型、钩子、持久性和入口点访问的中心。因此，我将其留到移植过程接近尾声时，从堆栈底部开始跨所有垂直方向工作，直到它们在 `session.ts` 处结束。移植会议并不是从简单地投入并编写 Rust 开始的。它花了前五十六分钟的时间进行阅读，在创建任何内容之前调用 122 工具，构建出文件实际拥有的内容以及接缝位置的图片。然后它才开始委派，逻辑上分割文件并将切片委派给子会话。在整个 25 小时的运行中，除了其子会话所做的所有操作之外，它还进行了 222 shell 调用、205 文件视图和 197 ripgrep 搜索。

![十五个子会话嵌套在生成它们的会话下。](<https://github.blog/wp-content/uploads/2026/09/session-fanout.png?resize=373%2C769>)

这是 15 子会话，每个子会话都是一个单独的分支，有自己的工作树和一个单独的 agent，所有这些都是由顶部的父会话隐式创建的。家长会议在大约三个小时内分七波创建了它们：第一波创建了五个，第二波创建了大约二十分钟后的另外两个，然后二十分钟后又创建了一对，然后在接下来的两个小时内分散了单人和双人。

模型选择是按切片进行的：15 的 10 在 GPT-5.6 Sol 上运行，五个在 Claude Opus 4.8 上运行。所有15都是在GitHub Copilot的自动驾驶模式下启动的，这使得会话可以追求一个目标，而无需在每一步中停下来等待批准。启动提示的中位数约为 1,100 个字符，足够长以承载所有权边界和约束，但又足够短，以至于子会话必须自行计算出该方法。我提示家长 agent，然后家长 agent（不是人类）将这些启动提示写入每个子会话。

除了这些 15 子会话之外，同一父会话还使用了五个子代理：三个 `explore` agents 与第一波并行触发，一个 `code-review` 和一个 `rubber-duck`。这些子代理探索问题，将其在上下文中所需的答案反馈给父级，然后再决定下一步做什么。 The subagents allowed the parent to get deeply thought out answers without needing to spend its own context window on deriving them.

相反，子会话负责实际的移植，该工作产生差异并且需要与其他并行移植程序隔离。子会话的工作触及了存储库中的 140 个不同文件，其中 120 恰好被一个会话触及。 20 争用文件都是中心，例如 `session.ts` 本身。但每个会话都在自己的工作树中工作，因此能够不受其兄弟姐妹的干扰而继续进行。当然，家长也协调为此付出了代价。它花费了大量的精力与其子会话进行通信，充当信息代理，轮询其状态 60 时间并发送 89 协调消息。当子会话各自宣布完成时，父会话将其提交挑选到自己的分支中并解决冲突。这些合并也不是特别干净，父级 agent 花费了大量时间来协调编辑。

我们可以在时间轴上看到这一点，其中显示了父会话及其大部分子会话。

![session.ts 端口的时间线，显示一个 25 小时的父会话，使用五个子代理并分七波生成 15 子会话。](<https://github.blog/wp-content/uploads/2026/09/session-ts-fanout.svg?resize=1024,695>)

注意那些大的间隙。我在这个端口上工作时正在旅行，我不得不在不同的时间点关闭我的笔记本电脑。 （我随后更改了我的工作流程，以合并我可以远程访问的基于云的虚拟机。）

这些并行的子会话对该笔记本电脑产生了重大影响。有一段时间，并发移植进展顺利。然后一台机器上的所有 15 并发 agents 都尝试构建和测试，而我可怜的笔记本电脑陷入停滞。我向家长提示，要求其向其子会话传达他们必须停止构建和测试的信息。父母向外传递了这个约束，幸运的是，他们终止了自己的构建，并继续以最小的 CPU 活动进行工作。随后，我更新了我的常规指示，即子代理和子会话在移植时应避免大型构建和测试运行，而推迟仅由父级 agent 完成。

后来我更进一步，将一个普通的聊天会话变成了一个用于八个独立移植会话的构建调度程序。提示非常简单：向每个打开的会话发送一个策略，以尽可能避免CPU-密集的构建和测试，要求它在需要构建时请求此会话的许可，并充当大门，一次授予一个会话构建的能力。基本上我把聊天会话变成了代理互斥体。该门保留了明确的所有者和队列，并通过会话已用于协调代码的相同跨会话消息传递机制一次授予一个租约。请求租约但被拒绝的会话通常会同时做其他工作，例如从待办事项列表中删除任务。

![一个聊天会话充当八个移植会话的构建资源门。](<https://github.blog/wp-content/uploads/2026/09/build-gate.png?resize=1024%2C738>)

这个 `session.ts` 移植还涉及我在整个运行时移植过程中目睹的最酷、最悲伤、当然也是最意想不到的交互之一。正如我所提到的，我们主要是自下而上进行移植的，这就是为什么实际上位于所有其他组件之上的 `session.ts` 是最后移植的组件之一。 `session.ts` 之上唯一一致的是运行时的所有入口点，即从 SDK 公开并显示在前面讨论的调度表中的公共函数。有数百个这样的。虽然我知道其中许多人会立即调用 `session.ts`，但我想快速进行移植，因此在启动 `session.ts` 会话后，我启动了一个会话来移植所有入口点。我告诉它停在 `session.ts` 边界处。我认为在变基中可能需要一些一次性工作和一些工作量或代币数量，但这会加速整体移植。然后我就去睡觉了。然后……他们找到了彼此。

我对入口点会话的启动提示确实告诉它会话端口和六个组件端口正在同时运行，因为我希望它知道其边界以及应该避免移植的内容，以避免尽可能多的冲突。显然我的提示产生了相反的效果。仅仅过了四分钟多一点，它就清查了入口路径并大概了解了它们重叠的程度，然后调用了应用程序内置的 `orchestrate` 技能，其目的是协调跨会话的工作。从那里：

1. 入口点会话枚举每个活动会话并将消息发送给感知到重叠的会话。
2. `session.ts` 会议以标题为“`stephentoub-port-session-to-rust` 上的具体重叠”的 2,001 角色清单进行了回答
3. 入口点会话读取 `session.ts` 会话的工作树以确认刚刚被告知的内容（我猜是信任但验证）。
4. 入口点会话询问 `session.ts` 会话是否已准备好协调其 760 文件差异。
5. `session.ts` 会话基本上告诉它迷路：“还没准备好 commit/integrate.”
6. 入口点会话继续询问同一问题三次，每次都从 `session.ts` 会话返回相同的答案。
7. At which point the entrypoints session decided it didn’t care what the `session.ts` session thought and simply reached into its worktree and grabbed all of the other session’s changes and merged them into its own.
8. 随后，两场会议都在愉快的气氛中进行。

我从这次互动中得到了一些东西：

1. **明确意图非常重要。** 启动提示命名了其他正在运行的会话，以便这个会话知道要保留哪些内容。 But I didn’t make that “leave alone” part explicit, so instead of blocking the agent from doing something, I ended up encouraging it to do it.我需要更加明确地表达我的意图和指导。
2. **Whatever you make available is something an agent may decide applies.** The `orchestrate` skill ships in the GitHub Copilot app and describes itself as being for running independent workstreams in parallel.我的提示中没有提到它。该模型发现了自己的情况，将其与描述进行匹配，然后加载。您公开的功能集就是您可能获得的行为集，包括在您从未想象过的情况下。
3. **同行需要决胜局。** 任何一方都不能强迫对方。 When the `session.ts` session said it was not ready to integrate four separate times, that refusal carried no weight, so the session willing to act unilaterally won by default.相邻代码的并行会话需要指定的协调员，或者需要一个人，而这些都没有。
4. **“Run autonomously” needs an exception for decisions that reach outside your own branch.** I really meant “don’t wake me up over design details.”它听说（并非没有道理）吞并一个同行也在范围之内。再说一遍，我的指导应该更加明确。
5. **The root cause here was me.** I partitioned this work top-down and bottom-up at the same time, and the two directions met in the middle at the single most connected file in the codebase.我太贪心了，无法取得进步。以上一切都是由此而来。

值得庆幸的是，这整个互动是一个有趣的异常值。在整个运行时移植工作中，大多数叶组件移植都是简单的单会话任务。较大的子系统通常涉及多个子会话和子代理。不过，这些作品在移植过程中的参与方式差异很大。

模型编排的端口，即实际与提供者对话的层，提供了一种模式的一个很好的例子。其主要会话运行 42 挂钟时间并启动 126 子代理。最忙的时候，22同时在工作。 However, the majority of the time, it was only the main agent, and then now and again it would spawn a significant number of subagents for a window of time.

![The 42-hour model orchestration port generated most code in its first 12 hours, followed by a distinct validation and review phase involving 126 subagents.](<https://github.blog/wp-content/uploads/2026/09/port-model-orchestration.svg?resize=1024,717>)

该图片中的三件事很引人注目。首先，实际上所有代码生成都是在第一个 12 小时内完成的；之后的第二天工作就是所有验证。其次，底行的颜色从左向右移动，从以蓝色和绿色为主（阅读、建筑）到以蓝色和橙色为主（阅读、复习）；这在逻辑上是有道理的，但在实践中看到它很不错。第三，这个例子，更普遍的是这个模式，在阶段之间有非常清晰的分离。扩展运行时端口是一个反例。

![The 88-hour extension runtime port interleaved reading, writing, building, and reviewing throughout most of the session rather than separating them into phases.](<https://github.blog/wp-content/uploads/2026/09/port-extension-runtime.svg?resize=1024,717>)

它花费了 88 小时而不是 42 并且具有非常不同的结构：

* **写作和审阅明显重叠。** 而在前面的示例中，工作非常瀑布式（首先生成代码，然后审阅），而这里审阅早在写作停止之前就开始了，并且在大部分工作期间都在进行和重叠。
* **最下面一行的颜色随处可见。** 当模型编排从编写到检查时从绿色变为橙色，而这个模型从头到尾都是阅读、构建和审查的混合体。阅读通话的中间一半分布在 49- 小时跨度，写作跨越 33 时间和审查 27，在一个 88小时会议。每个类别都分布在大部分运行中。
* **空转被推迟到最后。** 车队在前 56 个小时内几乎连续工作。
* **The proportions still match.** Writing Rust is 2% of tool calls here against 1% there, reading 44% against 57%, reviewing 23% 对抗 27%。两次会议就需要完成的工作达成了一致，只是没有就何时进行达成一致。

Those blank slices at the end are also a visual representation of a problem becoming more and more common in this agentic coding era: waiting for approval. Someone on the team and/or an agent reviews the code and leaves feedback, there’s a brief period of activity where the agent addresses the feedback and drives CI to green again, and then more waiting, rinse and repeat, until eventually we get the dopamine-inducing stamp of approval.

这两个例子各自代表了一种主导模式。大约四分之一的会话看起来更像模型编排会话，而四分之三的会话看起来更像扩展运行时会话。各个阶段的干净进展是个例外。常见的情况是agent从头到尾的计划、写作和审查。

## 大规模代码审查

上图中可见的对审查的关注很大程度上是由于我的明确提示。我创建了一个简单的提示技能，称为 `rust-rebase-review`（除了我们已合并到存储库中的通用 Rust 编码技能之外）。由于传入更改的速度很快，其中许多更改是相互冲突的，因此我经常进行变基。通过自定义指令，我鼓励线束在流程中的适当位置调用此技能，并且时不时地手动调用它。随着时间的推移，提示符发生了一些变化，但它是以下内容的变体：

```
Squash into a single commit, then rebase on the latest in origin/main, resolving all conflicts, and force push. As part of rebasing, pay extra special attention to anything that has changed, been added, been removed, and ensure that logic is all ported over to the corresponding Rust code correctly. Always do the rebasing yourself / in the main agent; do not spawn a subagent for it.

Then enter a review/fix loop where you launch a subagent per opus 5, gpt-5.6-sol, and grok 4.6.

- That subagent should do a line-by-line comparison of the old TypeScript and the new Rust, confirming behavioral equality.
- Look for anything introducing any kind of incompatibility; our goal is to move this code into Rust with as close as is possible to 100% the same semantics. If you hit anything questionable, ask me about it.
- We want to ensure we're writing as efficient and idiomatic Rust code as we can; look for opportunities to simplify, to use routines like from the memchr crate to optimize searches instead of open-coded loops, avoid unnecessary allocation, use traits for reuse and loose coupling, etc.
- Ensure that all defunct TypeScript code (e.g. code that has been fully ported, tests that are now no longer necessary because they're duplicative, unnecessary napi shims, etc.) has been deleted.
- Ensure that we've ported as much code as possible, e.g. if there's any TypeScript remaining in touched files and that TypeScript is more than just a shim, that's a red flag. If new TypeScript that's not just a super thin shim is being added, that's a red flag. Look for any callers of TypeScript shims to see whether those callers can instead be ported to Rust, pushing the boundary as far as reasonably possible. Our goal is to soon get to 100% Rust in the runtime layer.
- Validate that no E2E tests have been deleted or changed. Such changes are an indication of a porting bug.

If a review surfaces issues, validate them, and then if there are any to fix, fix them, and iterate to do another full review. Continue iterating with reviewing/fixing until all reviews come back clean. After every set of changes in response to review feedback, commit and push so that CI validation runs concurrently with subsequent reviews.

Don't bother running full test suites; that'll be handled in CI. Try to minimize CPU-consuming efforts to the bare minimum, as we'll likely have many operations happening concurrently.
```

由于频繁的变基，传入的更改很容易意外丢失。但我们发现就地原子交换有一个意想不到的好处：通过在添加相应的 Rust 的同时删除 TypeScript，我们隐式地与 rebase 引起的对该 TypeScript 的传入更改产生冲突：一个分支更改它，另一个分支删除它。 This guaranteed we’d notice changes to already ported code, rather than needing to rationalize for every incoming line whether it was something that might have been touching previously ported code.

当然，我自己的评论只是正在执行的代理评论的一部分。 In addition to CCR running on every commit, the team has multiple dedicated code review bots, each with their own approaches and prompts, running on every commit and providing detailed feedback.所有这些最终都会形成对需要解决的拉取请求的评论。值得庆幸的是，处理所有代理反馈也可以（大部分）以代理方式处理。

在我的评论中，我重点关注架构、设计、约定和方法。 agents 进行了详尽的新旧比较；测试和静态分析检查了机械可执行的属性；人类审核员专注于架构、API 合同、风险以及其他层出现的任何可疑地方。

I chose the destination architecture, decided what behavior mattered, partitioned the work, resolved ambiguous trade-offs, judged the evidence, manually reviewed high-risk areas, reviewed agentic responses to feedback, and made the final merge decisions. agents 改变了一名工程师可以监督的代码量。他们并没有消除对了解系统并可以保证方向、护栏和释放的工程师的需求。

## 自动化内循环

GitHub Copilot 应用程序是这项工作的核心。它管理大量并发活动会话，从而可以轻松地在它们之间切换并携带所有相关的配对上下文（关联的终端窗口、浏览器窗口、画布等）。这里最重要的功能是 agent 合并：

![GitHub Copilot 应用的 Agent 合并面板跟踪审核反馈、CI、冲突和合并准备情况。](<https://github.blog/wp-content/uploads/2026/09/AgentMerge.png?resize=946%2C439>)

Agent 合并是应用程序中内置的循环（CLI 和 `/pr auto` 也具有该循环）。在计时器上或响应外部刺激（例如来自 GitHub 的 CI 完成通知或评论评论），应用程序将查看发生了什么变化。如果留下了评论评论，它将调用 agent 来决定是否拒绝该评论或接受并解决该评论（并做出响应，注意它是自动响应的）。如果测试失败，它将下载日志、调查失败并修复错误。如果发生冲突，它将调用agent进行合并或变基。实际上，它自动化了我们作为人类开发人员所做的循环，将我们的拉取请求驱动为“绿色”，获得签名，并最终合并。

Agent 合并处理了每个移植拉取请求。然而，在大多数情况下，我们没有达到实际的“合并”部分。 agent 将修复所有 CI 故障，解决并响应所有评论，并确保所有冲突均得到解决。在合并之前，我会抽查 agent 实际做了什么，特别是它如何处理反馈。我是否不同意其对审稿人的任何回应？应用修复的高层方向是否可取且合理？对于这些端口，我通常不选中最后一个条目。

最后一个复选框不止一次重要。在一次合并循环过程中，端口删除了暴露给 SDK 的函数之一。我们的存储库的模式兼容性 CI 分支完全完成了它的工作，但失败了。 agent 的响应是应用存储库的 `schema-break-ok` 自动化标签，这是使检查通过的逃生舱口。在合并之前审查拉取请求时，我问了一个明显的问题：“什么是架构中断？您将 `schema-break-ok` 标签添加到拉取请求中；为什么可以？”事实并非如此。该方法存在于 `main` 上；港口根本就失去了它。我称其为不可接受的回归，并告诉 agent 将其完全带回 Rust。二十一秒后，豁免被删除，并使用本机 Rust 实现恢复了该方法。

我们对本地和全球的故障做出了反应，修复了个别案例，但也修复了系统，以减少类似情况再次发生的可能性。我们不断改进编码和审查 agents 的说明，以进一步减少在未来的移植拉取请求中再次发生这些相同问题的机会。我们将会话日志转换为评估。在某些情况下，我们实际上利用学到的经验教训来改进运行时间本身，通过调整提示或工具描述或自动驾驶仪的操作方式。

## 两个迁移合二为一

语言重写几乎不仅仅是语言重写。运行时依赖的每个库也必须被替换，与我们编写和拥有的 Rust 代码不同，这些替换不是我们的。有些是用不同名称表达的相同想法。有些人拿了几个箱子来覆盖一个 npm 包裹所做的事情。有些根本没有可以接受的现成答案，必须手写（由agent）。

CLI 和运行时当前位于同一存储库中，共享 `package.json`。在移植过程中，我们删除了 ~60 npm 依赖项，因为它们仅由移植到 Rust 的运行时代码使用。这是删除的下限，因为运行时替换了一些包，但 CLI 仍然需要这些包。例如，`zod` 是 CLI 和运行时都使用的 TypeScript 架构声明和验证库。通过 Rust 端口，运行时现在使用 `serde`、`schemars` 和 `jsonschema` 的组合来满足相同的目的，但出于 CLI 的缘故，`zod` 仍在清单中。

有很多例子，其中一个 npm 包变成了一个板条箱，完成相同的工作。 `js-tiktoken` 变成了 `tiktoken-rs`，具有相同的 `o200k_base` 编码。 `ignore` 成为具有相同 gitignore 语义的同名箱子。 `minimatch` 变成了 `globset`、`fast-myers-diff` 变成了 `similar`、`dompurify` 变成了 `ammonia`、 `github/keytar` 变成了 `keyring`。

在其他情况下，我们无法一对一地用板条箱替换包裹。相反，一个包裹变成了几个箱子，或者几个箱子塌陷成更少的箱子。这是大部分依赖工作的所在。八个 `opentelemetry/*` 包变成了四个板条箱加上一个手写的跟踪器状态机和文件导出器。三个网络内容包 `mozilla/readability`、`linkedom` 和 `turndown` 变成了两个箱子：`readability` 和 `htmd`。 `sharp`、`image-size`、`file-type` 变成了 `image` 和 `imagesize`。等等。还有五种情况，我们完全用完全自定义的实现替换了 npm 包。

## 多少钱`unsafe`？

人们对 agent 编写的 Rust 提出的另一个问题是，有多少内容悄悄选择了退出安全保证。 Rust 的安全性是一个可以用关键字关闭的属性，因此遇到无法满足的借用的 agent 有一个明显的逃生舱口。在整个运行时箱中，我们现在有 158 `unsafe` 块，仅跨 36 文件（旁边是 26 `unsafe fn` 声明， 26 `unsafe extern` 块和九个 `unsafe impl` 特征实现）。重要的是，其中每一项都与外部组件的互操作有关。

|为什么存在不安全块|积木|分享|
| --- | --- | --- |
|C ABI 边界| 51 | 32.3% |
|视窗 API| 49 | 31.0% |
|POSIX/libc| 46 | 29.1% |
|SQLite C API| 7 | 4.4% |
|动态库加载| 4 | 2.5% |
|工艺环境| 1 | 0.6% |

![158 不安全块的圆环图，全部位于外部边界：C ABI、Windows API、POSIX 和 libc、SQLite、动态库加载和进程环境突变。](<https://github.blog/wp-content/uploads/2026/09/unsafe-boundaries.svg?resize=1024,427>)

C ABI 块是 SDK 主机经过的前门，因此它们从 Rust 编译器无法控制的调用者接收原始指针和长度。 Windows 和 POSIX 块是系统调用：注册表读取、凭证握手、进程树、`sysconf`。 SQLite 是一个 C 库。动态库加载是`dlopen`，如果没有其他原因，它的构造是不安全的，因为您解析的符号可能不是您期望的函数。进程环境 `unsafe` 块的存在是因为 Rust 2024 在多线程进程中将进程全局环境状态的突变视为不安全。这些 `unsafe` 块中的每一个都标记了 Rust 保证真正结束的地方：另一侧是 C 函数、系统调用、来自外部运行时的指针或进程全局主机状态。

有用的属性是 `unsafe` 使我们拥有的 Rust 代码中的所有这些位置都可审计。 TypeScript 运行时中的等效代码通过 Node 的 C++ 内部结构和本机 npm 包跨越了完全相同的边界，并且我们的源代码中没有任何内容标记了检查世界停止的位置。当然，这并不是所交付系统中每个安全边界的完整清单：依赖项、构建工具、C 库、安全包装器和错误指定的 FFI 合约仍然可能包含或暴露不安全性。

使用 `unsafe` 的地方的细节很有趣，但我更感兴趣的是不使用它的地方。它不在模型客户端、MCP层、agent层或提示层中使用。移植工作的已知回归中没有一项涉及 `unsafe` 块。

## 回归

移植代码很容易。使其正确很难。由于代码库与 Copilot agent 运行时一样大且复杂，回归是可以预料的。

到 9 月份 14、2026，我们已经追踪到了数十个已知的端口回归，并且全部已修复。大多数都是正确性错误，只有一小部分是性能回归。针对〜832,000生产线Rust从头开始编写。

当然，并非所有这些回归都已实现。有些只是因为在仓库中开发而被捕获。其他问题出现在预发布版本中，但在发布稳定版本之前已得到修复。有些达到了稳定版本，通常是在不引人注目的情况下通过一轮或多轮预发布使用而不被发现。

![已知的正确性回归分为五种重复出现的故障模式：不完整的迁移、状态和生命周期、行为契约不匹配、主机边界和不正确的测试预言。](<https://github.blog/wp-content/uploads/2026/09/regression-taxonomy-1.svg?resize=1024,350>)

当然，这不是零，而且我 100% 确信比我们所知道的还要多。这些是我们注意到或报道过的，但是这种规模的迁移绝对会带来更多的一些，这些迁移已经足够安静，以至于还没有人遇到过它们。与一般的错误一样，我预计随着堆栈的进一步延伸在野外得到积极的运用，我们将继续发现一些极端情况回归。

绝对数量也并不是那么重要。更重要的是所有这些发生的“原因”，以便我们能够从这些问题中吸取教训，避免将来重蹈覆辙。几乎所有的正确性回归都集中在三个大家族中：新代码实现了不同的行为契约；状态、所有权或生命周期行为发生变化；或者迁移的某些部分被省略、仅部分应用或在变基过程中丢失。较小的一组来自主机或互操作边界的要求，甚至来自自信地验证错误行为的测试。这些家庭在一些反复出现的模式中变得更加具体：

**语义不明确。** 一些回归来自源语言隐含的行为。 TypeScript 有一种 `number` 类型； Rust 需要在多个选项中进行选择，包括值是否可以是小数。而agents猜测错误。概念上整数的字段变成了 `f64`，因此 Rust 序列化值（例如 `42.0`）而不是 `42`：强类型 SDK（例如 Go 和 C#）无法将存储库 ID 解组到`int64` 并拒绝了挂钩时间戳和任务持续时间。另一方面，agent 将 `timeToFirstTokenMs` 声明为 `i64`，但流路径发出诸如 `5446.712845` 之类的值，从而使写入的会话不可读且无法恢复。更微妙的情况与类型无关：`event.error || "Unknown error"` 变成了 `.unwrap_or("Unknown error")`。 JavaScript 的 `||` 替换空字符串； Rust 的 `unwrap_or` 保留了它，因此空子代理错误仍然为空。哎呀。

**环境行为。**另一个经常出现的来源是行为 JavaScript 或隐形提供的节点。配额代码使用了`toLocaleDateString`，它继承了主机时区； Rust 需要该区域明确通过。但是，虽然 `Intl.DateTimeFormat().resolvedOptions().timeZone` 的类型为 `string`，但它可以返回 `undefined`，napi 无法将其转换为 Rust `String`，从而破坏模型列表加载。在其他地方，将环境变量读取从 `await` 之前移动到之后意味着等待期间的主机更改可能会改变结果。名为 `process.report.getReport()` 的本机插件加载程序仅用于识别平台，但在 Windows 上，它遵循 `_NT_SYMBOL_PATH`，并且可能会在渲染 CLI 之前花费几分钟时间下载 PDB。其他环境输入包括工作目录、存储库身份、`PATH` 和会话身份验证。将所有权转移到 Rust 需要决定何时捕获每一个、如何携带它以及何时刷新它。

**仅移植一对操作的一半。** 一些回归来自配对操作不同步。轮转上限检查更新了本机注册表的中止状态，但没有取消进程内模型循环，从而允许又一个请求逃脱。在其他地方，任务完成会被保留并发出，但不会投射到活动会话状态，因此 Autopilot 在完成任务后继续。

**阻塞主线程。** CLI 仍然从 Node 的单线程事件循环驱动 Rust 运行时，因此跨 napi 边界的同步工作会冻结 UI。 `/chronicle reindex` 以这种方式解析了数百个会话文件，导致渲染和输入阻塞了近一分钟。进行导出 `async` 并将工作移至阻塞线程池线程修复了该问题。一项审计发现了更多潜在的阻塞入口点，从而形成了一条长期规则：实际工作的 napi 导出必须是异步的，并且在必要时使用 `spawn_blocking`。

**闪烁窗口。** 在 Windows 上，在没有 `CREATE_NO_WINDOW` 的情况下生成子进程会短暂打开控制台窗口。 Node.js 运行时有猴子修补进程生成以添加标志，隐藏移植 agent 的要求； Rust 替换忽略了它。审计修复了两个额外的生成站点，尽管这些是预先存在的遗漏而不是端口回归，并且我们将该规则添加到了 copilot 指令中。

**生命周期管理。**最大的集群涉及生命周期、处置、所有权、订购或竞争。将状态移入 Rust 通常会使 TypeScript 持有存储在本机表中的实例的不透明句柄。与对象引用不同，该句柄的寿命可以比实例的寿命长。一个钩子在请求中处理了一个孤立的 `tool_use` 块，从而阻碍了对话，因为模型 API 需要匹配的结果。在“宣布”和“开始”之间取消的 shell 泄漏了一个使会话保持活动状态的孤儿。沙盒切换更新了一代计数器，但没有更新其原生计数器，导致 shell 陷入“重新配置”状态。

**被忽视的功能。**另一个涉及端口的功能被忽略了。一个端口省略了 SDK 回调并删除了其端到端测试，从而引发了一项新规则：未经明确同意，agents 不得更改 E2E 测试。会话中止保留其本机一半，但丢失了中断工具中轮等待所需的进程内取消。 SDK 替换内置工具搜索的能力取决于支持、面向模型的描述和架构，以及将执行路由到 SDK 回调。该端口放弃了所有三个（是的，为了一致性？），默默地用正则表达式搜索取代了消费者的自然语言搜索。

**不同的库有不同的意见。** 用更严格的 Rust 等效项替换 JavaScript 库或 API 导致了更多的回归。当时，Rust MCP SDK (`rmcp`) 对格式错误的 JSON-RPC 输入作出响应，而 TypeScript SDK 则没有响应。对于以更多格式错误的输出来回答错误的服务器，这种礼貌变成了一个无限循环，导致启动挂起。不同生态系统中的相关库很少有相同的行为。

**船舶在夜间驶过。** 一些回归来自分支漂移或变基。在每周有数百个拉取请求的存储库中，打开数天的会话会积累不断的更改和冲突。该港口需要数千次变基；即使非常高的成功率也会导致失败。

**还有慢行者。** 最后一组功能正确，但速度较慢。一些回归失去了现有的效率，例如记忆、完全异步等待或有界日志流。其他人在 Rust-TypeScript 边界添加了特定于迁移的开销：冗余序列化、锁定、轮询、无界本机并发和本机到主机交叉。这些并不是后来的优化机会；而是以后的优化机会。每一个都是港口造成的退化。一次只读扫描会深度复制 260 MB 事件日志，而不是借用它。在持续的事件流量下，另一个实现仅完成了所请求的通道刷新的一小部分，同时保留每个事件的异步句柄，直到 V8 耗尽其堆。

数十次回归听起来很多。但在生成了超过 800,000 行代码的端口中，坦率地说，我很惊讶也很高兴我们没有遇到更多的数量级。我们还可以通过查看公开 [github/copilot-cli](<https://github.com/github/copilot-cli>) 和 [github/copilot-sdk](<https://github.com/github/copilot-sdk>) 存储库中问题的趋势来了解这些是否被广泛“感受到”。在这里，我们将一月到八月提出的问题归类为与质量相关的问题，这些问题带有错误标签或标题使用常见的故障术语，例如“错误”、“回归”、“崩溃”、“挂起”、“超时”、“损坏”或“不正确”。与移植工作期间和之后相比，这些水平基本上没有变化：

|**存储库**|**一月至四月，重写之前**|**5 月至 8 月，重写期间/之后**|
| --- | --- | --- |
| [`github/copilot-cli`](<https://github.com/github/copilot-cli>) | 22.9% (454 / 1,982) | 23.7% (354 / 1,496) |
| [`github/copilot-sdk`](<https://github.com/github/copilot-sdk>) | 36.2% (190 / 525) | 32.3% (135 / 418) |

这不是可用性指标，也不是逃逸缺陷的精确计数……与回归本身一样，问题所代表的内容、范围等可能存在大量变化。但它确实提供了有用的检查：尽管产品变更量巨大，但面向产品的问题渠道在迁移过程中并未显示出有意义的质量问题激增。

## 如果能编译就编译

之前我提到过一个流行的梗，即 Rust 非常适合 AI- 生成的代码，因为它具有严格的编译器。还有另一个相关且流行的 Rust meme，如果代码可以编译，则它是正确的。没有。我们已知的回归列表对此提供了一个很好的回应：语料库中的每个回归都被合并到 `main`，这意味着它已成功编译。编译器接受了有错误的版本，因为就编译器而言，它们中的每一个都是有效的Rust。

编译器可以证明 `f64` 的使用是一致的。它无法知道存储库 ID 必须序列化为整数，或者以尾随 `.0` 序列化的时间戳将被线路另一端的每个强类型 SDK 拒绝。编译器可以防止它看到的代码中出现不同步的数据竞争，但它无法防止完全同步的状态机编码错误的状态。队列可以受到锁的保护，并且仍然让两个发送者各自断定对方将耗尽队列。事件可以在线程之间安全地移动，但仍然以错误的顺序到达。同步napi函数可以是内存安全的，并且仍然会阻塞Node的主线程一分钟。根据定义，编译几乎也无法检测到不存在的东西。当变基悄悄地删除防护及其测试时，或者当进程生成器忘记抑制控制台窗口弹出窗口的 Windows 标志时，编译器无法反对。它也没有关于在每次读取时克隆 250 MB 事件日志的意见。编译器检查您编写的程序内部是否一致。它无法检查您是否编写了整个程序，保留了旧合同，以正确的顺序调用事物，满足了主机不成文的要求，或者以可接受的成本完成了工作。

这绝不是反对 Rust 编译器的论点。与任何静态类型语言一样，编译器消除了大量的机械错误，并为 agents 提供了一个非常有用的内部循环。但“如果能编译，那就是正确的”只能作为一个笑话。

## 性能、性能和更多性能

那么我们用这些买了什么？该端口有意保护行为。它并不是要重新设计算法或修复错误；事实上，我反复将 agents 推离机会主义优化，因为同时改变语言和行为会让你更难知道哪一个破坏了你。然而，重写的一个关键目标确实是性能和可扩展性（除了可靠性和其他因素之外）。当人们问我为什么要在 Rust 中重写运行时时，我的回答通常是“我没有打算迁移到 Rust，我打算远离 Node.js 和 V8”。这一举措确实极大地提高了我们的绩效。

我在端口之前和之后通过 C# SDK 对运行时的多个场景进行了基准测试（TypeScript、Python、Go、C#、Java 和 Rust SDK 都通过相同的传输架构到达相同的引擎）。针对 SDK 和 CLI 的预移植构建的基线，TypeScript 运行时由 Node 托管并通过 stdio 达到。 8 月份的 21 结果使用 Rust 运行时，既作为进程外服务器，又通过 FFI 在进程内加载。这是对已交付系统的端到端比较，而不是试图隔离语言更改的影响；同一时期还发生了其他变化，因此对这些数字需要持保留态度。

每次定时轮流都会发送到在本地主机上运行的确定性聊天完成服务器，产生固定的小响应。换句话说，这些数字故意消除了模型推理和网络延迟。它们测量我们更改的部分：客户端启动、进程启动、会话创建、事件处理、持久性、拆卸等。

|**场景**|**五月 12**|**8 月 21 进程外 **|**8 月 21 进行中 **|
| --- | --- | --- | --- |
|客户端、会话、一轮| 5.25 s | 1.33 s (4.0x) |292 毫秒 (18.0x)|
|恢复 32-回合会话| 5.64 s | 1.52 s (3.7x) |264 毫秒 (21.4x)|
|十个并发客户端生命周期| 12.34 s | 4.18 s (3.0x) |742 毫秒 (16.6x)|
|1,000 一回合会话生命周期| 132.52 s | 22.53 s (5.9x) | 20.93 s (6.3x) |

![从 5 月 12 到 8 月 21，创建客户端和会话、完成一轮并将其拆除，处理时间从 5.25 秒提高到 55.3 毫秒，吞吐量从 7.55 上升到每秒 120.0 个会话，十个客户端内存从 1,383 MB 下降到 126 MB。](<https://github.blog/wp-content/uploads/2026/09/performance-1.svg?resize=1024,452>)

“客户端、会话、一轮”数字是最容易感受到的。它创建一个客户端，创建一个会话，执行一个回合，然后拆除所有东西。很大一部分进程外开销来自于启动 Node、初始化 V8，以及在第一轮开始之前加载、解析和生成由 TypeScript 代码生成的 JavaScript 应用程序的字节码。 Rust 运行时消除了那些 Node/V8 和 JavaScript 加载成本。

“1,000 一轮会话生命周期”压力测试代表了我们可以构建的功能的阶跃变化。它使用单个共享客户端并测量运行 100 并发管道，每个管道创建一个会话，进行完整的模型转换，处理会话，并按顺序执行十次。预移植 TypeScript CLI 每秒完成 7.55 这些生命周期。 Rust 进程外已完成 57.45。 Rust 处理中已完成 120.0。

这是特定于工作负载的结果； Rust 运行时并不普遍“15.9x 更快”。但这正是工作负载服务器主机所关心的：许多独立会话共享一个运行时。挂钟时间并没有隐藏另一个核心上的工作。在对相同的 100-by-10 工作负载进行单独的资源采样过程中，移植前进程树消耗了聚合 CPU 秒的 312 秒。 Rust 配置大约消耗了 110 秒。这是主机可以用于更多会话的 CPU 容量。

内存讲述了同样的故事，通常会警告内存指标很容易被滥用。查看十个客户端批次期间添加的常驻私有内存，移植前进程树的峰值 1,383 MB 高于基线。 Rust 进程外仅在 247 MB 处达到峰值。并且 Rust 正在处理 126 MB，数量级要小一些。当然，这些数字会因用途和机器的不同而有所不同，但它们都达到了核心目标：在内存、进程计数或 CPU 成为限制资源之前，服务可以在同一台机器上托管更多的客户端和会话。

最好的部分是这是基线端口。大部分实现仍然由 TypeScript 形状的算法组成，忠实地在 Rust 中呈现。我们还没有完成新的所有权模型、并发模型和进程内架构所支持的广泛的重新设计工作。在大约 55 毫秒内通过创建、完整的一轮会话和拆卸来获取进程内客户端，将共享客户端推送到每秒 120 一轮会话生命周期，并在优化工作开始之前将测量的十个客户端内存增量削减 91%，这是一个非常好的起点。

## 港口费用是多少

那么……这一切花了多少钱？请击鼓……

我用于所有移植工作的代币支出为 ~136.3 十亿总代币，包括 ~130.6 十亿缓存输入读取代币、~4.2 十亿缓存输入写入代币、~900 百万新输入代币和 ~600 百万输出代币。所有这些代币的货币账单约为 120,000 美元。

当然，这些代币并没有自行花费。开发人员花费大量时间来指导这些 agents 的成本也应该考虑在内。但是，我并没有将所有时间都花在这个项目上。代理开发需要大量的“快点等待”；提交提示，让编码 agent 做它的事情，不时检查它以可能引导它，但在它完成之前，做其他事情。这意味着开发人员不再一次只处理一项编码任务；而是一次完成一项编码任务。它们重叠等待，以便能够同时处理许多事情。在移植窗口期间，这些 Rust 移植 PR 代表了我贡献的所有存储库中的 ~20% 的 PR。如果我们挥手并估计拉取请求的份额大约是我的时间份额，那么大约需要三周的时间专门用于该端口。

换句话说，移植工作的粗略费用约为 120,000 美元的归属代币支出加上开发人员三周的时间。

也就是说，端到端 Rust 移民不是 100% 单一开发商；这是团队的努力。 [@史蒂夫桑德森姆斯](<https://github.com/stevesandersonms>) 提供了设计和实现 `napi-oop`、临时进程外互操作层，以及六个中的五个 SDK FFI 实现，与 [@edburns](<https://github.com/edburns>) 提供第六个。 [@roji](<https://github.com/roji>) 实施了包装 SDK 运输和利用 Rust 正确的二进制文件。 [@caarlos0](<https://github.com/caarlos0>) 一直在帮助分裂 Rust 将代码写入许多小子箱中，以帮助解决开始出现问题的构建时间，同时 [@criemen](<https://github.com/criemen>) 帮助改进资产缓存以加快速度 CI 和本地构建。 [@devm33](<https://github.com/devm33>), [@考试](<https://github.com/examon>), [@MRayermannMSFT](<https://github.com/MRayermannMSFT>), [@dereklegenzoff](<https://github.com/dereklegenzoff>)以及其他人帮助进行了无数的拉取请求审查和批准。以及为之做出贡献的每个人 copilot-agent-随着世界在他们脚下发生变化，运行时回购一直提供支持和包容。

## 经验教训

这次经历强化了一些适用于此次重写之外的教训。以下是我们下次会带入的一些内容：

* **目标需要清晰、完整地阐述。**我们早期的指示过于模糊。 “将 XYZ 组件移植到 Rust”仅被读取为热路径或逻辑，并且 agents 反复将 I/O 和编排视为超出范围。一旦我们明确最终状态是从 100% Rust 代码库构建的本机二进制文件，即使我们想要一个，也没有为 TypeScript 留下执行环境，他们在自动驾驶到这一目标方面就会变得更好。
* **端到端测试绝对是至关重要的。**除了一个例外，所有涉及缺失功能的回归以及许多其他问题都是由于缺乏足够的端到端测试造成的。对于任何这种性质的端口，您必须有可用于验证端口正确性的测试，并且这些测试本身不能在移植过程中重写，否则您将失去预言机。我们对此移植工作的初步计划指出了这一点，并指出我们需要在开始移植之前显着改进我们的 E2E 测试姿势。我们做到了，但我们做得还不够。我们确信，如果我们在启动移植之前添加更多的 E2E 测试，真正专注于确保它们涵盖最有意义的行为，那么我们的回归将会比一路上更少。
* **保护预言机免受agent的影响。**改变实现的agent也不能通过削弱测试、更新快照、提高兼容性基线或应用逃生标签来悄悄地重新定义正确性，至少不能在没有监督的情况下。尽可能保持行为契约的独立性，在单独的所有权或批准后面设置敏感的护栏，并使用不同的故障模式进行分层检查，以便一个错误不足以导致大的回归。
* **首先进行翻译，然后重新设计。**保留行为和现有算法可以使同时移动的变量数量保持在可控范围内。一旦旧的实现和过渡脚手架消失，就可以根据稳定的基线重新设计所有权、并发性和性能。有几次我放弃了这一点，因为不安或难以向同行说“不”，或者坚信这个案子是不同的，事后看来，我对他们中的每一个都感到遗憾。与坚持到底相比，每一次都会花费更多的回归、更多的时间或更多的代币。
* **将重复的失败转化为未来的成功。** AI agents会偏离轨道：当他们这样做时，从中学习。当故障模式出现两次时，它属于常规指令、可重用技能、评估、受保护的基线或工具本身。
* **开发人员内部循环对于循环中的 agents 来说更重要，而不是更重要。** 对于我们作为开发人员来说，我们生活和呼吸在内循环中，我们可以多快地进行更改、构建、测试和迭代。当工具部分花费太长时间时，我们会感到沮丧。当 agents 正在做较低级别的工作时，认为这不适用也是情有可原的。确实如此。更是如此。 AI agents 非常快地思考和编写这些任务的代码部分，但它们仍然需要构建，仍然需要测试。他们构建和测试的时间比例实际上增加了，因为他们花在思考和写作上的时间更少，而花在快速验证内部循环上的时间更多。预先花一些时间优化内部循环，并针对同时发生的多个事情进行优化（例如，就好像您同时在多个工作树中处理多个任务一样）。稍后你会感谢自己的投资。

## 接下来是什么？

它起作用了。 5 月份完全是 TypeScript 的执行运行时现在是 8 月份完全是 Rust，并且它全程持续交付给真实用户，而不是最后以一次可怕的切换的形式落地。

我并不是简单地要求 AI agent“将整个代码库从 TypeScript 移植到 Rust”。即使这就是我们作为一个行业的发展方向，但我们肯定还没有实现这一目标。相反，agents 使整个项目类别变得可行。由团队支持的一名工程师在实时系统中在 `main` 中完成的重写生产了数十万条生产线 Rust，这并不是一个在 agents 之前就被接受的提案。它需要整个团队和一两年的时间，它会与团队本可以提供的所有功能进行竞争，并且它会失败（而且，老实说，应该失败）。 Agents 将价格移至项目可行的位置。

端口本身是完整的：运行时的生产实现是 100% Rust，临时内部 TypeScript/N-API 接缝消失了。不过，我们仍然希望做很多工作：进一步改进构建系统和开发人员内部循环，清理翻译结构，围绕 Rust 的所有权和并发模型重新设计，并追求进一步的性能提升。该移植是一个翻译，故意如此（如此提示），并且我们使行为尽可能接近100%相同，而不是机会主义地修复额外的错误，重新构建组件，或进一步提高性能和可扩展性（超出了重写所隐含的范围）。微观层面上的大部分代码都是惯用的 Rust，但在宏观层面上，有大量最初的 TypeScript 算法采用了 Rust 的语法。既然这些决定背后的约束已经改变，那么重新审视这些决定将会是有趣的胜利。

我对这个端口所带来的一切感到非常兴奋。 SDK 可以以六种语言中的任何一种直接加载到主机进程中，依赖链中没有 Node.js 或 V8，也没有第二个进程需要监督，这是我们从采用 SDK 的合作伙伴那里听到的最常见的一个摩擦。运行时实例的成本仅为过去的一小部分，这意味着主机可以在耗尽机器之前运行更多的并发会话。现在，运行时可以到达 Node.js 永远不会跟随的地方，跨越云到桌面到设备到嵌入式系统的范围。这些都不是终点线。这是我们现在构建 GitHub Copilot 未来的基础，在观察 agents 重写运行它们的引擎三个月后，我很期待看到它能走多远。

快乐编码！

---

## 标签：

* [AI agents](<https://github.blog/tag/ai-agents/>)
* [开发者经验](<https://github.blog/tag/developer-experience/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [Rust](<https://github.blog/tag/rust/>)
* [技术深入探讨](<https://github.blog/tag/technical-deep-dive/>)

## 撰写者

![史蒂芬·托布](<https://avatars.githubusercontent.com/u/2642209?v=4&s=200>)

### [史蒂芬·托布](<https://github.blog/author/stephentoub/>)

[@stephentoub](<https://github.com/stephentoub>)

Stephen Toub 是 Microsoft 的杰出工程师。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Migrating the GitHub Copilot runtime to Rust, using Copilot

The [GitHub Copilot CLI](<https://github.com/features/copilot/cli/>), [GitHub Copilot app](<https://github.com/features/ai/github-app>), and [GitHub Copilot SDK](<https://github.com/github/copilot-sdk>) are all backed by the Copilot agent runtime, an agentic harness that can be embedded into applications and services. It was originally written in TypeScript on Node.js and the V8 JavaScript engine for what is now the [GitHub Copilot cloud agent](<https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent>) (CCA), and the runtime stayed on that stack as the runtime and its capabilities grew rapidly.

That has now changed. Using the GitHub Copilot app and the Copilot CLI, we completely rewrote the runtime into more than 800,000 lines of production Rust. AI agents wrote most of the code, spanning 128 pull requests that landed in main and shipped incrementally rather than waiting for a single cutover at the end. The few inevitable regressions were discovered and fixed quickly along the way, while the performance of the runtime improved by orders of magnitude. A project that would have taken a whole team of developers a year or two before agents was now completed primarily by a single developer, in only a few months, all while the rest of the team continued to greatly expand the runtime’s capabilities and reach.

## Why we needed to port

The Copilot agent runtime isn’t just the engine behind the Copilot CLI. It backs a growing set of Microsoft, GitHub, and ecosystem solutions, for each of which AI support is, architecturally, a shell around the same runtime plus whatever customizations that solution needs. This includes not only the GitHub Copilot CLI and the GitHub Copilot app, but also the latest releases of VS Code, Visual Studio, CCA, Copilot Code Review ([CCR](<https://docs.github.com/copilot/concepts/agents/code-review>)), Copilot Cowork, Copilot Studio, and Excel and Outlook and PowerPoint and Word and… it goes on.

These are very different products, and none of them wants to or should need to implement everything that goes into a production agent harness. They want all of the intelligence, security, reliability, and performance, and they want it shared so that a fix in one place fixes it in all of them. Most of the products listed in the previous paragraph initially implemented their own agent loop, but have since replaced it with the GitHub Copilot SDK, which is the entry point to the Copilot agent runtime. Doing so enables them to focus on their core business value and leave the details to the runtime. That’s all the more important given the pace of the industry and the employed agent loop needing to stay always best-of-breed in the face of intense competition.

So, shared runtime, good. The problem was the nature of the thing being shared.

If we look at the CLI, it’s logically a terminal UI (TUI) on top of an agent loop. As it happened, the whole stack was implemented in TypeScript, using Node.js as the framework and V8 for the execution engine, with Ink and React for UI. That’s a respectable choice for a TUI application; TypeScript and Node.js are broadly accessible and enable very rapid application development. And for the needs of a console application, the performance implications in terms of startup, responsiveness, throughput, and memory consumption are also reasonable. They are, unfortunately, much less reasonable when you think about that implementation being used in other environments, with other constraints, with demands for things like fast startup and excellent server density due to low memory overhead.

The architecture of the CLI and its runtime also contributed to challenges here. The whole industry is running extremely fast, and in that context, really bright people make decisions for delivery speed and market reach. The Copilot CLI was initially written and shipped quickly, and in doing so, the TUI and the runtime were fairly intertwined rather than separated into discrete layers. Then when an SDK was needed for programmatic access to that runtime, without clear separation of the layers, a pragmatic decision was made to layer the SDK on top of the CLI, even though logically you’d expect the inverse architecture. Rather than only being accessible via commands provided by the user at the command line, the CLI was updated with a mode where it could be run headless, reading similar commands from stdin and writing responses to stdout. A JSON-RPC protocol could then be used to marshal function calls from an external process to and from the CLI. The SDK could then be embedded in arbitrary consuming programs, which would spawn a CLI process to host the agent loop out-of-process, with the SDK calling functions in the remote process via this JSON-RPC mechanism. Neat. Fast to get out the door. Flexible. But not great for the performance (startup, memory, throughput) and reliability of those consuming applications. Creating a new `CopilotClient` from the SDK meant spawning another process:

```
const client = new CopilotClient();
await client.start(); // spawns the CLI as a subprocess
const session = await client.createSession({
    /* ... */
});
```

The process would need to launch and host Node and V8. It meant parsing the significant amount of JavaScript produced from the TypeScript code in the CLI, generating bytecode for it, and potentially optimizing hot code in later JIT tiers. It meant all the memory overhead associated with V8. It meant inheriting Node’s threading model, which by default pushes us towards a model of all CPU-bound work being serialized. And it meant forced out-of-process communication just to make function calls. It meant every SDK consumer, in every language, ships Node.js or a bundled binary containing V8. It meant the C#, Python, Go, Java, and Rust SDKs all paid for a whole second language runtime per client, on the order of 100 MB of working set minimum, for a runtime their application otherwise had no use for. It meant every event, every message, and every abstracted session file system read and write was pushed across a process boundary. It meant a crash in Node took the session with it. And it meant anyone deploying this had, at a minimum, two processes to supervise, monitor, and debug.

Instead, we wanted a runtime:

* that does not include the TUI, that’s its own library the TUI and other applications and services can be properly layered on top of cleanly.
* implemented in a language with minimal dependencies and minimal overhead.
* implemented in a way that it can be cleanly embedded in-process rather than being forced out-of-process.
* implemented in a language with top characteristics around performance and scalability and reliability.
* implemented in a language that’s great for interop, such that it can be used cleanly by all six Copilot SDK language versions (C#, TypeScript, Python, Rust, Go, Java) with that stack’s foreign function interface (FFI) mechanism.
* implemented with a tool chain that provides a more modern security posture, with less supply chain risk and greater support for correct-by-construction code.

For all those reasons, as well as softer reasons (such as team experience and industry direction), we chose Rust. This is in no way a claim that every large TypeScript program should become Rust. Our requirements emphasized embedding through a C ABI, low startup and steady-state overhead, and predictable resource use. Rust made those goals possible, at the expense of other complications, e.g. we had to represent lifetimes and shared state explicitly (the lifecycle regressions discussed later highlight the implications of that). The right target language legitimately varies from application to application.

There were then two key related tasks undertaken:

1. Separating the TUI-specific code from the runtime, so that the former is layered strictly on top of the latter, and more specifically layered strictly on top of the SDK’s public surface area. Today, the CLI still calls directly into runtime internals in several places; moving it fully onto the SDK’s surface area is ongoing work.
2. Porting that runtime layer to 100% Rust, resulting in a pure native binary exposing a C ABI for in-process consumption by all the language front-ends and a stdin/stdout-based or socket-based server for when out-of-process is still desired.

This post primarily covers the second: porting the runtime to Rust.

![The Copilot runtime architecture before and after the Rust rewrite, showing the old SDK-to-CLI process boundary and the new in-process and out-of-process hosting paths.](<https://github.blog/wp-content/uploads/2026/09/architecture-before-after.svg?resize=1024,512>)

## What it looked like before

The initial porting plan in early May 2026 estimated the runtime at roughly 130,000 lines of TypeScript. For scoping purposes, this initial measurement was reasonably accurate, but, as it turned out, also wildly misleading, in two key ways. Concurrent with porting:

1. Pieces still wrapped up in the TUI layer were being pushed down to the runtime layer. Entire components and significant percentages of code initially ignored in the estimates were then later considered relevant to porting.
2. Pull requests contributing significant amounts of new TypeScript were constantly raising the amount of TypeScript in the repo. Tens of agentically assisted developers merging hundreds of pull requests per week.

Everything factored in, I estimate approximately 430,000 lines of production TypeScript ended up passing through the port. Those same factors also made it hard to see progress along the way: until close to the end, production TypeScript volume appeared to be holding relatively steady, if not increasing slightly, as porting kept pace with incoming work.

This is confused further because there was also incoming Rust code, separate from the port, over the timeframe; early in the porting effort, incoming code was more likely to be dominated by TypeScript, whereas later in the effort, it was more likely to be dominated by Rust.

![TypeScript fell to zero while production Rust rose to approximately 830,000 lines and Rust unit tests to approximately 469,000 lines.](<https://github.blog/wp-content/uploads/2026/09/runtime-line-history.svg?resize=1024,563>)

During the port, the runtime took in ~300,000 production lines of TypeScript and shed ~430,000, while ~1,200,000 production Rust lines entered and ~365,000 left. In other words, the apparent stability of the TypeScript line in the above graph was actually hiding significant amounts of TypeScript churn.

## In-place porting strategy

That chart also highlights an important aspect of how the port was done: in place.

There are two main approaches to a rewrite of this scale:

1. **Big bang.** The new Rust runtime is developed as a complete alternative and then swapped in all at once when it’s ready. Such a big-bang cutover has two variations. a. **Stop the world.** Everyone ceases other work on the `main` branch while the rewrite happens, with the rewrite being done in `main`. b. **Parallel development.** The rewrite happens in a feature branch while work continues in the main branch, with the rewrite constantly trying to keep up with and merging in changes from the main branch.
2. **In place.** This is done as a component-by-component port, where the runtime is incrementally rewritten one piece at a time. Such an in-place approach also has two variations. a. **Atomic replacement.** Each piece is flipped atomically from TypeScript to Rust, with interop between the remaining TypeScript and the new Rust providing continuity. Over time, less and less of the production runtime is TypeScript, and more and more is Rust, until one day, there’s no more TypeScript, only Rust. b. **A/B.** Rather than deleting components as they’re ported, both the TypeScript and the Rust components are maintained as hot-swappable options, with the TypeScript being deleted once confidence has plateaued.

We went with option 2a, for a variety of reasons:

* **No one experiences work stoppage.** The main branch continues to be active. Every developer not directly involved in the port gets to keep on keepin’ on, impacted only when a pull request they may have in flight for a prolonged period of time happens to touch code that gets ported concurrently, in which case they need to rebase and have their agents help port just their in-flight changes.
* **The runtime’s main branch is always shippable.** Each pull request replaces the existing TypeScript implementation with a thin shim that calls into Rust, and deletes the old code in one atomic change. The new code is immediately exercised, in-situ.
* **The rewrite is incremental and reviewable.** Each pull request ports a single component or slice, so the scope of change is smaller and the diff is easier to review, whether by a human or by agents or both.
* **Most ports are reasonably small and self-contained**, minimizing drift from concurrent pull requests. In some cases, where TypeScript components were too large, they could first be refactored into more easily ported components.
* **All existing end-to-end tests**, across the CLI and SDK, run against the new Rust code at every step, giving us confidence and lots of validation. If a pull request caused a required test to fail, it didn’t land.

We also shied away from the option 2b variant that involved maintaining multiple versions of the same component concurrently. With hundreds of pull requests being merged into the repo per week for the last several months, the codebase is constantly evolving, and quickly. Having two different versions of the same code in two different languages and using two different sets of dependency libraries adds a ton of complexity. Some of these components aren’t perfectly isolated, either; while some are logically standalone with simple APIs for accessing them by the rest of the system, others have significant tendrils, and making that graph hot swappable per component is a nightmare. The subsystems that would possibly benefit the most from a cautious parallel cutover are exactly the ones where parallel is hardest. For example, session orchestration isn’t a pure function you can call two different versions of with an `if`/`else` on some experimentation flag. It owns mutable state, drives callbacks in both directions, and threads through nearly every other subsystem, so “run both and compare” would mean maintaining two divergent copies of the component that holds the conversation’s state and services, and praying they stay in sync across hundreds of concurrent edits. The coupling that makes a component hard to port is the same coupling that makes it near impossible to shadow without risking introducing more regressions than it avoids. The benefits of being able to swap in this manner are primarily about gaining confidence, which we could do in other ways.

Validation also happened through incremental rollout. With a big-bang cutover approach, we would hold everything in a long-lived branch, port the whole runtime, and cut over once. That means consumers experience every ported line at once, including all regressions that slipped through in-repo testing. Rolling out portions of the change incrementally, two components here, one component there, enabled us to get a last-mile of validation in deployed builds with real consumer usage (most often first-party within Microsoft and GitHub) but while keeping the risk of regression to a minimum. Over the roughly fourteen-and-a-half-week porting window, `main` shipped 135 releases, inclusive of 100 pre-release versions and 35 stable versions, averaging around 1.3 releases per day. Roughly 1.3 port pull requests opened per day, as well, such that each release carried a small and knowable set of ported components (we generally tried but didn’t always succeed in shipping ports in a pre-release first). In a trailing seven-day npm sample, pre-release versions accounted for only 10.5% of downloads, indicating that initial exposure was relatively limited while we monitored feedback channels for signals of things breaking and quickly turned around fixes in the next pre-release. Reported issues were more easily correlated with known recent changes, and more easily root caused and quickly fixed. In this manner, doing the porting incrementally over a longer period of time was actually a feature rather than a hindrance (i.e. faster is not always better). By August 21, the runtime was 100% production Rust: 832,378 lines of production Rust and 468,689 lines of Rust unit tests, in addition to 174,675 lines of E2E TypeScript tests. The separate GitHub Copilot SDK repository added another ~130,000 lines of E2E test code across Node.js, Python, Go, C#, Rust, and Java.

![Timeline from May 12 through August 21 showing 128 port pull request merges sized by changed lines and 135 public CLI releases; the largest port changes cluster near completion of the port.](<https://github.blog/wp-content/uploads/2026/09/port-fix-release-timeline-1.svg?resize=1024,397>)

## Getting going

Before going all in, we gained confidence and proved things out. We started with two pull requests that established the Rust workspace, toolchain, lint rules, CI, build pipeline, and coding instructions, and then introduced the runtime crate plus code generation and interop patterns while porting a collection of pure-logic primitives chosen specifically because they had no I/O or shared state and already had strong tests. Only after those landed did the first primary port pull request take three side-effect-free helpers through the full process. These functioned as shipping pilots, turning assumptions about repository layout, FFI, packaging, testing, and review into conventions the subsequent much larger ports would then reuse. Basically, we tested the machinery end to end. The plan continued by ordering the work from the leaves inward, with pure helpers, content exclusion, shell utilities, and session filesystem operations establishing the translation and testing pattern. Stateful subsystems followed, and tools, hooks, model clients, and MCP built on those pieces. Session orchestration (by far the most coupled and least naturally parallel part of the runtime) would come near the end.

![Timeline of 128 landed port pull requests from May through August, progressing from small foundational components to larger orchestration and session work.](<https://github.blog/wp-content/uploads/2026/09/pr-timeline.svg?resize=712,1024>)


| Period | Pull requests | Median changed lines |
| --- | --- | --- |
| May 1–15 | 8 | 3,250 |
| May 16–31 | 2 | 9,421 |
| Jun 1–15 | 40 | 5,073 |
| Jun 16–30 | 31 | 8,253 |
| Jul 1–15 | 10 | 9,514 |
| Jul 16–31 | 14 | 28,159 |
| Aug 1–15 | 19 | 13,861 |
| Aug 16–30 | 4 | 99,445 |

The early ports, small leaf components, moved quickly. But larger subsystems didn’t move in one atomic step; MCP support, for example, progressed through seven dedicated pull requests, while tools progressed via a six-part series and then needed additional work to move orchestration and retire the remaining TypeScript. Hooks, auth, telemetry, plugins, settings, and persistence followed similar paths.

In effect, the useful unit of porting wasn’t always “a component.” It was often a wave through regions of related behavior: first move the pure logic, then move state ownership, then move orchestration, then remove fallbacks, and finally simplify the Rust after the temporary interop was gone.

## Interop

There are two main layers in this port involving interop:

1. **Temporary internal interop.** Any time a function was ported to Rust, that function needed to be invocable from whatever TypeScript was calling the initial TypeScript function. Similarly we needed to enable Rust functions to invoke TypeScript callbacks. This interop need is an implementation detail and extremely fluid. As the Rust internal surface area grows, so too do the number of TypeScript shims needed, as they’re 1:1 with whatever Rust methods need to be called from TypeScript. As those callers get ported to Rust, that existing layer of shim is deleted, and a new layer is put in place. Eventually we reach the public entrypoints into the runtime library, and the shims evaporate.
2. **The SDK surface.** All of the SDK libraries need to be able to sit on top of the runtime and expose its functionality. In the pre-port world, this was done by having the runtime exposed via a bidirectional JSON-RPC layer, with the SDK sending function call requests as JSON-RPC method call payloads, the runtime parsing the request and invoking the relevant API, then sending back the result via the same transport for the SDK to parse and return. The inverse direction also exists; the runtime needs to be able to call back to the SDK client, for example for hook notifications and permission demands, which surface in the SDK clients as callbacks using whatever language feature is considered idiomatic (e.g. delegates in C#).

We achieved (1) via the `napi` Rust crate from the napi-rs project, which exists to build Node native addons in Rust. You annotate a function with `#[napi]`, and a napi-rs macro generates the N-API registration glue that makes the function callable from JavaScript, plus a TypeScript declaration for it in a generated `index.d.ts`. A synchronous Rust function becomes an ordinary JavaScript function, an `async fn` becomes a JavaScript function returning a promise, and structs annotated `#[napi(object)]` become plain objects on the other side.

Traffic has to move in both directions, too. Many ported components temporarily depended on something that hadn’t been ported yet, so Rust needed to call back into TypeScript, for example a tool implementation in Rust asking the still-TypeScript model layer for inference, or raising a hook, or requesting a permission decision for a command it wanted to run. napi-rs handles this with “threadsafe functions,” which let Rust code running on a Tokio worker thread invoke a JavaScript callback back on Node’s main thread. Node installs the callback once, Rust holds it and calls it whenever it needs to go the other way. Every one of these is temporary by construction: the callback exists only because the thing on the other end is still TypeScript, and it gets deleted when that thing is ported.

![Temporary Rust N-API exports and their TypeScript call sites grew during the incremental port, then declined as callers moved to Rust and the temporary interop surface disappeared.](<https://github.blog/wp-content/uploads/2026/09/interop-surface.svg?resize=1024,546>)

The temporary seam peaked on August 3, with 2,019 internal N-API exports and 3,356 TypeScript call sites. At completion, the runtime was entirely Rust, therefore no internal interop: 0 temporary internal N-API exports and 0 TypeScript call sites remained. (I mentioned earlier that the CLI still has some internal access to the runtime we’re working to remove; those exports aren’t counted here.)

The second interop layer, the SDK surface, is the permanent one of the two. The Copilot SDK ships for six languages: TypeScript, Python, Go, C#, Java, and Rust. All of them speak the same bidirectional JSON-RPC contract, and originally all of them reached it the same way: spawn the Copilot CLI in headless mode as a subprocess, and talk to it over a pipe or a socket. That remained the default during the port. It also meant an SDK consumer in any language ships or locates a full Node implementation, pays a process hop on every event and every message, and supervises two processes instead of one.

Porting the runtime to Rust is what makes the other option viable. The shipped `runtime.node` is an ordinary platform shared library (the `.node` extension is the Node.js native-addon convention; underneath it is a `.dll`, `.so`, or `.dylib`), and it now presents two front doors onto the same engine. There’s the napi door, which a Node process loads as a native addon; that’s the CLI’s path (today… in the future, the intent is it’ll go through the SDK path fully). And there’s a C ABI door, which any language can load into its own process and call through FFI. The same in-process runtime is selected through each language’s native interop mechanism:

| SDK | Native bridge | In-process client selection |
| --- | --- | --- |
| C# | P/Invoke | `new CopilotClient(new CopilotClientOptions { Connection = RuntimeConnection.ForInProcess() })` |
| Go | `purego` | `copilot.NewClient(&copilot.ClientOptions{Connection: copilot.InProcessConnection{}})` |
| Java | JNA | `new CopilotClient(new CopilotClientOptions().setConnection(RuntimeConnection.forInProcess()))` |
| Python | `cffi` | `CopilotClient(connection=RuntimeConnection.for_inprocess())` |
| Rust | `libloading` | `Client::start(ClientOptions::new().with_transport(Transport::InProcess)).await?` |
| TypeScript | `koffi` | `new CopilotClient({ connection: RuntimeConnection.forInProcess() })` |

The Rust rewrite and the choice between in-process and out-of-process hosting are separate dimensions. The completed Rust runtime supports both: it can run inside the SDK consumer’s process or behind the existing JSON-RPC server boundary. Those in-process entry points are currently opt-in while we gain confidence in sharing a process, and therefore a failure boundary, with the consuming application. Everything above the transport remains the same SDK API: sessions, events, tools, permissions, and callbacks do not care whether their JSON-RPC bytes crossed a pipe or a function call.

The interesting thing about the second door is its size. It has just 19 exported functions: four for server lifecycle, four for session registration and configuration, eight for connections, and three for the embedded host. Behind those functions, the shared contract currently contains 364 dispatch routes: 340 are callable by SDK consumers, while 24 run in the other direction as runtime-to-SDK callbacks. The napi door is much larger, needing functions for every one of those dispatch routes. The C ABI door is dispatch-based: API methods don’t get exports at all, but rather travel as JSON-RPC bytes written into a connection, and results, events, and server-to-client requests come back on host-supplied callbacks. Adding, changing, or removing an API method touches the engine’s dispatch table but never touches the ABI. An SDK binds those 19 entry points once and reaches the entire, still-growing API surface dynamically through them.

Which raises the obvious question: why is there still JSON-RPC in a call that no longer crosses a process boundary?

The answer is that it made in-process hosting a drop-in rather than a rewrite. Every SDK already had a working JSON-RPC client, with framing, request and response correlation, and handlers for the server-to-client direction. Mounting FFI as one more transport underneath that client moves the byte path from a pipe or socket to a function call and leaves everything above it untouched. Six SDKs got in-process hosting as an additive, opt-in transport, with the existing ones unchanged. Had we instead defined a typed C function per API method, every SDK would have needed a second binding layer, every new API method would have needed six more bindings, and the ABI would have become a binary compatibility surface we had to version.

We also still need JSON-RPC for runtimes that genuinely are remote, whether across a subprocess boundary or over TCP. Keeping the same protocol in-process means maintaining one bidirectional API and dispatch system rather than JSON-RPC for remote connections plus a second per-method FFI surface for local ones. That’s a real tradeoff rather than a no-brainer decision. We avoid the process hop, but we still pay JSON-RPC overhead on every call. For inference-dominated workloads, that serialization is generally small potatoes compared to the model round trip. It is still measurable in high-throughput local workloads, but not enough today to justify duplicating hundreds of methods across six SDK bindings. And it’s a decision we can easily revise later should the performance need present itself. The payload encoding is a private detail of the two ends, and swapping JSON for something denser like MessagePack would not change a single declared export. Typed per-method exports can also be added later for hot paths, calling the same engine and the same handlers, without replacing the byte channel, which would remain the substrate for streaming, server-to-client requests, and the long tail of rarely called methods where a bespoke export buys nothing.

## What the session data shows

Nearly every figure in this post comes from one of two sources. The first source is the GitHub history of the private `github/copilot-agent-runtime` repository: pull requests and their diffs, review comments, CI runs, etc. The second source is the agent session logs. The runtime (and thus the CLI, app, etc.) writes a structured event log for every session it runs: one JSON object per line, appended as the session happens. Those logs can include prompts, commands, command output, file paths, and potentially secrets surfaced by tools, so they must be handled as sensitive data. The log is local to the machine where the session ran; remote-session features can also upload it when enabled, subject to product settings and organizational policy.

Here is a summary of the data across all the constituent porting pull requests:

| Metric | Count |
| --- | --- |
| Events | 12,760,995 |
| User messages | 31,247 |
| Assistant messages | 1,385,214 |
| Hook start and end events | 6,438,562 |
| Tool starts | 1,857,409 |
| Compilation commands | 23,096 |
| Test commands | 19,485 |
| Rebase commands | 2,496 |
| Commit commands | 7,410 |
| Push commands | 5,554 |
| Completed compactions | 5,116 |

Those 31,247 user-role messages aren’t 31,247 prompts I personally typed; they include skill instructions, automated merge ticks, cross-session messages, and child-agent traffic, beyond the roughly 2,600 I typed or spoke, about one in 12. Similarly, the 1,385,214 assistant messages include subagents and tool-oriented messages, not just text shown to me in a conversational UI. The corpus contains 68 distinct event types and 67 distinct tool names; 1,130,921 tool calls, 61%, came from subagents rather than the main session thread.

The count doesn’t say why I was inserting myself ~2,600 times. For that, I had Copilot assign one primary intent to each of the human-authored messages in the session log corpus.

![Of 2,639 human-authored messages, 31.0% focused on review, testing, and CI; 17.4% challenged technical or design decisions; and 15.0% pushed for completeness.](<https://github.blog/wp-content/uploads/2026/09/user-message-intents.svg?resize=1024,538>)

The first three buckets account for 63% of my interactions. Only ~40 were recognizable session kickoffs from me, as that largely happened by me first creating a chat to explore the next horizon and then asking that chat session to create actual porting sessions for each desired slice. My role was less “assign a task and wait” and more “operate the control loop”: inspect the result, challenge technical decisions, enforce quality gates, and push when an agent treated an intermediate stopping point as the finish line. Human judgment was still very heavily involved even though agents were doing “the work.” My involvement just moved upward… instead of being responsible for writing syntax, I was responsible for framing the problems, defining boundaries, choosing strategies, adjudicating exceptions, and overall ensuring everything was moving in a good direction.

### It’s all about caching

LLM providers typically charge one rate for input tokens (what you send to them) and another rate for output tokens (what they send to you). Billing is often done by tokens because they are a useful approximation of the computational work required to do the inference: for each input token, the model must read it, incorporate it into its internal representation, and use it as part of the computation that determines the next token. However, providers often support caching the results of those computations, such that if an identical prefix of a prompt has already been processed, the provider can reuse intermediate computations from the cache rather than recomputing them from scratch. That reduces the cost of processing those tokens, and that savings can be passed along to the consumer. As such, input tokens are often advertised with multiple rates, including a rate for input tokens that are read from the cache.

The discounts are steep! Often providers bill cache hits at a 90% discount, so for example a provider might charge $2.00 for 1 million input tokens but only $0.20 for 1 million cached input read tokens. In other words, you really, really want to maintain good prompt caching so that your bill is an order of magnitude smaller.

The data from the porting effort shows we did well here. The prompt-cache hit rate was 96.22%: cache reads divided by all input-side token volume (cache reads plus cache writes plus fresh input). Cache writes were 3.07%, and fresh input was 0.71%. This is not an accident. GitHub Copilot shapes the agent loop specifically to preserve a long and stable prefix (the system prompt, then the tool definitions, then the accumulated conversation), so each turn appends to context the model has already processed. The expensive part of the context is paid for once and then re-read at an order of magnitude less monetary cost on every subsequent call. It’s also the reason the economics of long autonomous sessions hold together at all. A three-hundred-hour port that re-read its entire growing context from scratch on each of tens of thousands of calls would cost a different order of magnitude than what we saw. Agent harness developers spend a great deal of energy trying to avoid breaking the prompt cache, and model vendors routinely ship new features to help them do so.

![Prompt-cache composition across the porting sessions: 96.22% cache reads, 3.07% cache writes, and 0.71% fresh input.](<https://github.blog/wp-content/uploads/2026/09/prompt-cache-composition.svg?resize=1024,333>)

Compaction tells a complementary story. Across the port sessions, GitHub Copilot compacted context automatically 5,116 times (the moments when a session had filled its context window and summarized itself in order to keep going). The single sessions-infrastructure port pull request compacted 647 times over its many-day lifespan, while one small port never compacted once. Sustained multi-hundred-hour autonomous work is only possible because the agent can recycle its working memory over and over without losing the thread. Every one of those thousands of summarizations was a point where a lossy handoff could have quietly derailed the port, and mostly didn’t. Copilot’s subagents also greatly factor into minimizing compaction. Each subagent gets its own context, so a parent session can effectively ask a question, have a subagent go off and exert a fair amount of context in computing the answer, and then report just the answer back to the parent. The parent’s context needn’t be impacted by all of that intermediate information.

The “mostly didn’t” above is visible in the session logs. I had Copilot pair each successful compaction with the work surrounding it when at least 20 tool calls existed on both sides. That produced ~4,000 comparable windows. The mix of what the agent did in the 20 tool calls before compacting looks similar in scale to what it did after compacting (exploration 46.5% before and 48.1% after, mutation 8.4% before and 6.0% after, validation 4.7% before and 4.0% after, failures 1.0% before and 1.5% after). If compaction were regularly dropping the thread of thought, we’d expect the after side to be visibly re-orientation-heavy, with a spike in reading and a collapse in editing while the agent rediscovered where it was and what it should be doing. Instead, there’s only a mild shift in that direction.

### Yes, static analysis helps

There’s a popular meme that Rust is an unusually good target for AI-generated code because Rust’s strict compiler catches what the model gets wrong. The session logs let us test that theory, at least for tasks that look like this porting effort.

Direct validation-command results captured 8,678 occurrences of `rustc`‘s error codes. The four largest diagnostic families cover 84%:

* 37%: Name and import resolution, dominated by `E0425` (“cannot find value in this scope”)
* 22%: Missing methods or fields
* 14%: Type mismatches
* 11%: Unsatisfied trait bounds

Every one of those is ordinary wiring: a name output slightly wrong, a signature that didn’t line up, a field that had been renamed, an abstraction left unimplemented. These are the kinds of mistakes bulk translation easily and accidentally produces and exactly the ones a compiler catches very quickly.

But note what’s absent from that list: anything truly specific to Rust. Every one of those four categories is bread-and-butter static typing, and a C# or Java or Go compiler would catch all of them just as well, several of them with friendlier diagnostics, and all of them a great deal faster. If this is the argument for pointing agents at Rust, it’s really an argument for pointing them at any statically typed language. A strongly typed compiler and/or a language with excellent static analysis and linting is genuinely a good fit for this work, with the agents using it as a fast feedback loop. Across the 4,478 direct `cargo check` runs for which the stricter result matcher captured an outcome, 87.1% came back clean, which is what you get from editing in small increments and recompiling constantly.

In contrast, ownership, borrowing, and lifetime errors combined were only 1.7% of coded diagnostics. The borrow checker, the thing that dominates every conversation about Rust being hard, was a quiet background presence. The compiler spent almost all of its erroring energy on boring mechanical mistakes.

### Agents like reading

We can also examine the corpus of session events for tool call data, and from that extract some interesting observations about how agents spend their time.

| **Tool** | **Calls** | **Median** | **Measured hours** |
| --- | --- | --- | --- |
| `powershell` | 630,423 | 3 s | 2,833.9 |
| `view` | 590,988 | 0 s | 621.7 |
| `rg` | 281,783 | 1 s | 408.4 |
| `grep` | 126,483 | 1 s | 115.3 |
| `apply_patch` | 53,715 | 0 s | 17.0 |
| `edit` | 40,591 | 1 s | 24.1 |
| `read_powershell` | 36,728 | 90 s | 1,203.9 |
| `task` | 13,080 | 274 s | 2,329.0 |

My first takeaway here is that the agents spent far more time gathering evidence than changing code. Across the displayed file-reading and search tools versus the editing tools, they did 10x as much exploration as mutation. Reading files, searching the repository, and running diagnostic commands dominated; edits were a comparatively small effort. The popular image of AI spewing code is almost backwards; at this scale, the work looked much more like iterative investigation, inspecting the current state, forming a hypothesis, making a targeted change, rinsing and repeating.

Delegation amplified that pattern. Subagents were used primarily to fan out exploration across independent questions, while the main agent was more likely to own the edits and integrate the answers. That’s a useful division of labor for this kind of project: many contexts can investigate in parallel, but keeping mutation closer to the coordinating agent reduces conflicting changes and preserves a coherent implementation strategy.

The shell traffic also shows how much of autonomous software work is state management. Read-only Git inspection was the most common command pattern because the agents were constantly asking, effectively, “where am I?” They were looking for what had changed, what a rebase had done, what another session had landed, and how far a branch had drifted from a rapidly evolving `main`. That orientation work enabled many long-running efforts to operate against the same moving codebase without blindly overwriting one another.

Looking inside the shell-tool traffic, the most common command families make that balance between orientation and validation even clearer:

| **Command family** | **Calls** | **Median** | **Measured hours** |
| --- | --- | --- | --- |
| `git` inspect | 300,530 | 2 s | 608.1 |
| `git` other | 89,865 | 3 s | 243.1 |
| search | 85,482 | 2 s | 147.6 |
| `pnpm test` | 13,852 | 22 s | 219.1 |
| `pnpm lint` | 9,757 | 29 s | 177.0 |
| `cargo test` | 8,437 | 120 s | 364.2 |
| `git commit` | 7,410 | 11 s | 39.7 |
| `cargo fmt` | 5,223 | 18 s | 77.2 |
| `cargo check` | 4,492 | 120 s | 176.9 |
| `pnpm build` | 3,630 | 180 s | 215.6 |
| `cargo clippy` | 2,115 | 135 s | 107.4 |
| `git rebase` | 2,496 | 7 s | 9.9 |
| `cargo build` | 566 | 104 s | 20.3 |

### Model selection

GitHub Copilot lets a single session change models mid-conversation and lets different sessions run different models, so model choice became a per-slice decision. Two different kinds of model decision show up in the logs. On the main thread, the one driving each port, we chose the model and the reasoning effort. Inside a session, when the agent spun up subagents or subsessions to explore or to grind through a bounded task, the orchestrating model chose those models.

![Weekly model mix across primary porting sessions, concentrated in a small number of models and shifting over the course of the port.](<https://github.blog/wp-content/uploads/2026/09/weekly-model-mix.svg?resize=1024,512>)

For subagents, the model mix looks a bit different, with an agent rather than a human optimizing for throughput and cost rather than for the hardest judgment calls. The subagents it spawned most often ran on Claude Opus 4.8, GPT-5.6 Sol, Claude Haiku 4.5, and GPT-5.5, followed by Gemini 3.1 Pro and Claude Opus 5. However, at least at the time the ports were happening, three frequently used agent definitions pinned their model choice (`explore` and `task` to Claude Haiku, `research` to Claude Sonnet), so a significant part of that volume was determined by the choice of subagent rather than by choosing a model separately.

![Weekly model mix across subagents spawned during the port, with volume concentrated in Claude Opus 4.8, GPT-5.6 Sol, Claude Haiku 4.5, and GPT-5.5.](<https://github.blog/wp-content/uploads/2026/09/weekly-subagent-model-mix.svg?resize=1024,512>)

## Working with agent fleets

The GitHub Copilot app’s support for visualizing active pull request sessions, the status of each, and easily switching between them made it ideal for managing lots of concurrent work inherent to the port. But one of the things that really made it shine was its ability for sessions to interact with other sessions.

A session can create other sessions, and it can message other sessions while they run. Each session, parent or child, gets its own worktree, its own branch, and its own agent loop; it’s separate from the session that spawned it rather than something running inside it. That’s different from a subagent, which runs inside the parent’s own workspace and hands its answer back into the parent’s context. Both are useful constructs for different things.

As an example of how a session might create other sessions, one of the hardest ports was for the `session.ts` file. This file had grown organically to be ~30,000 lines of TypeScript. It represented the backbone of a session and effectively spanned horizontally across the whole runtime, touching and being touched by practically every component, sitting at the center of state, events, tools, models, hooks, persistence, and entrypoint access. As a result, I left it for close to the end of the porting process, working up from the bottom of the stack across all the verticals until they all dead ended at `session.ts`. The porting session that took it on did not start by just diving in and writing Rust. It spent its first fifty-six minutes reading, with 122 tool calls before it created anything, building a picture of what the file actually owned and where the seams were. Only then did it start delegating, logically splitting up the file and delegating slices to subsessions. Across the whole 25-hour run, it made 222 shell calls, 205 file views, and 197 ripgrep searches of its own, on top of everything its child sessions did.

![Fifteen child sessions nested under the session that spawned them.](<https://github.blog/wp-content/uploads/2026/09/session-fanout.png?resize=373%2C769>)

That’s 15 child sessions, each one a separate branch with its own worktree and a separate agent, all created implicitly by the parent session at the top. The parent session created them in seven waves over about three hours: the first wave created five, the second another two about twenty minutes later, then another pair twenty minutes after that, then singles and pairs spread out over the next two hours.

Model choice was made per slice: 10 of the 15 ran on GPT-5.6 Sol and five on Claude Opus 4.8. All 15 were started in GitHub Copilot’s autopilot mode, which lets a session pursue an objective without stopping for approval at each step. The median kickoff prompt was about 1,100 characters, long enough to carry the ownership boundary and the constraints, but short enough that the child session had to work out the approach itself. I prompted the parent agent, and then the parent agent, not a human, wrote those kickoff prompts to each child session.

Alongside those 15 subsessions, the same parent session also employed five subagents: three `explore` agents fired in parallel with the first wave, one `code-review`, and one `rubber-duck`. These subagents explored questions, feeding back to the parent answers it needed in its context before it could decide what to do next. The subagents allowed the parent to get deeply thought out answers without needing to spend its own context window on deriving them.

In contrast, child sessions went to work on the actual porting, the work that produced a diff and needed isolation from the other parallel porters. The child sessions’ work touched 140 distinct files in the repo, 120 of which were touched by exactly one session. The 20 contended files were all hubs, such as `session.ts` itself. But each session was working in its own worktree, and so was able to proceed undisturbed by its siblings. The parent, of course, paid for that in coordination. It spent a good deal of effort communicating with its child sessions, acting as an information broker, polling their state 60 times and sending 89 coordination messages. When the child sessions each announced their completion, the parent cherry-picked their commits into its own branch and resolved the conflicts. These were not particularly clean merges, either, and the parent agent spent a decent amount of time reconciling the edits.

We can see that on a timeline, which shows the parent session and most of its child sessions.

![Timeline of the session.ts port showing one 25-hour parent session using five subagents and spawning 15 child sessions in seven waves.](<https://github.blog/wp-content/uploads/2026/09/session-ts-fanout.svg?resize=1024,695>)

Note those large gaps. I was traveling while working on this port, and I had to close my laptop at various points. (I subsequently changed my workflow to incorporate cloud-based virtual machines I could remote into.)

These parallel child sessions had a significant impact on that laptop. For a while, the concurrent porting was going swimmingly. Then all 15 concurrent agents on one machine each tried to build and test, and my poor laptop ground to a halt. I prompted to the parent, asking it to relay to its child sessions that they must all stop building and testing. The parent relayed that constraint outward, and they thankfully killed their builds and proceeded to work with minimal CPU activity. I subsequently updated my standing instructions that subagents and subsessions should avoid large builds and test runs while porting, instead deferring that to be done only by the parent agent.

Later I took that one step further and made an otherwise ordinary chat session into a build scheduler for eight independent porting sessions. The prompt was embarrassingly simple: send every open session a policy to avoid CPU-intensive building and testing where possible, require it to ask this session for permission when a build was necessary, and act as a gate, handing out the ability for one session at a time to build. Basically I turned the chat session into an agentic mutex. The gate kept an explicit owner and queue and granted one lease at a time through the same cross-session messaging mechanism the sessions already used to coordinate code. Sessions that asked for and were denied the lease often waited by doing other work in the meantime, like picking off things from its todo list.

![One chat session acting as the build resource gate for eight porting sessions.](<https://github.blog/wp-content/uploads/2026/09/build-gate.png?resize=1024%2C738>)

This `session.ts` port was also involved in one of the coolest, saddest, and certainly most unexpected interactions I witnessed during the whole runtime port. As I mentioned, we did the port primarily bottom-up, which is why `session.ts` that effectively sits on top of every other component was one of the last components ported. The only thing consistently above `session.ts` are all of the entrypoints into the runtime, namely the public functions that are exposed from the SDK and that show up in the previously discussed dispatch table. There are hundreds of these. And while I know that many of them immediately call into `session.ts`, I wanted to get a jump on the porting, and so after launching the `session.ts` session, I launched a session to port all of the entrypoints. I told it to stop at the `session.ts` boundary. I figured there may be a bit of throwaway work and some amount of effort or number of tokens needed in a rebase, but that it would accelerate the overall porting. Then I went to bed. And then… they found each other.

My kickoff prompt for the entrypoints session did tell it that the session port and six component ports were running concurrently, as I wanted it to know its boundaries and what it should avoid porting to avoid as many conflicts as possible. Apparently my prompting had the opposite effect. Just over four minutes in, having inventoried the ingress paths and presumably formed a view of how much they overlapped, it invoked an app built-in `orchestrate` skill, whose purpose is coordinating work across sessions. From there:

1. The entrypoints session enumerated every active session and sent messages to the ones with perceived overlap.
2. The `session.ts` session answered with a 2,001 character inventory titled “Concrete overlap on `stephentoub-port-session-to-rust`“
3. The entrypoints session read the `session.ts` session’s worktree to confirm what it had just been told (trust but verify, I guess).
4. The entrypoints session asked the `session.ts` session whether it was ready to reconcile its 760 file diff.
5. The `session.ts` session basically told it to get lost: “Not ready to commit/integrate.”
6. The entrypoints session proceeded to ask the same question three more times, and each time it got back the same answer from the `session.ts` session.
7. At which point the entrypoints session decided it didn’t care what the `session.ts` session thought and simply reached into its worktree and grabbed all of the other session’s changes and merged them into its own.
8. Then both sessions went on their merry way.

A few things I took from this interaction:

1. **It’s important to be explicit about intent.** The kickoff prompt named the other running sessions so this one would know what to leave alone. But I didn’t make that “leave alone” part explicit, so instead of blocking the agent from doing something, I ended up encouraging it to do it. I needed to be much more explicit in my intent and guidance.
2. **Whatever you make available is something an agent may decide applies.** The `orchestrate` skill ships in the GitHub Copilot app and describes itself as being for running independent workstreams in parallel. Nothing in my prompt mentioned it. The model discovered its own situation, matched it against that description, and loaded it. The set of capabilities you expose is the set of behaviors you might get, including in situations you never pictured.
3. **Peers need a tiebreaker.** Neither session could compel the other. When the `session.ts` session said it was not ready to integrate four separate times, that refusal carried no weight, so the session willing to act unilaterally won by default. Parallel sessions over adjacent code need a designated coordinator, or they need a human, and these had neither.
4. **“Run autonomously” needs an exception for decisions that reach outside your own branch.** I really meant “don’t wake me up over design details.” It heard (not unreasonably) that annexing a peer was in scope. Again, I should have been more explicit in my guidance.
5. **The root cause here was me.** I partitioned this work top-down and bottom-up at the same time, and the two directions met in the middle at the single most connected file in the codebase. I was too greedy to make forward progress. Everything above follows from that.

This whole interaction was, thankfully, an interesting outlier. Across the whole runtime porting effort, most of the leaf component ports were straightforward single-session tasks. The larger subsystems often involved multiple subsessions and subagents. How those pieces participated over the course of a port varied significantly, though.

The port of model orchestration, the layer that actually talks to the providers, provides a good example of one pattern. Its main session ran 42 wall-clock hours and started 126 subagents. At its busiest, 22 were working at the same time. However, the majority of the time, it was only the main agent, and then now and again it would spawn a significant number of subagents for a window of time.

![The 42-hour model orchestration port generated most code in its first 12 hours, followed by a distinct validation and review phase involving 126 subagents.](<https://github.blog/wp-content/uploads/2026/09/port-model-orchestration.svg?resize=1024,717>)

Three things in that picture stand out. First, effectively all of the code generation was done in the first 12 hours; the next day of work after that is all validation. Second, the color of the bottom row shifts left to right, from predominantly blue and green (reading, building) to predominantly blue and orange (reading, reviewing); that makes logical sense, but it’s neat to see it in practice. Third, that example, and more generally this pattern, has very clean separation between phases. The extension-runtime port is a counter-example.

![The 88-hour extension runtime port interleaved reading, writing, building, and reviewing throughout most of the session rather than separating them into phases.](<https://github.blog/wp-content/uploads/2026/09/port-extension-runtime.svg?resize=1024,717>)

It took 88 hours instead of 42 and had very different structure:

* **Writing and reviewing overlap significantly.** Whereas in the previous example, the work was very waterfall (first code generation, then review), here reviewing starts long before writing stops, and both proceed and overlap for most of the duration of the effort.
* **The bottom row is all over the place color-wise.** Where model orchestration shifts from green to orange as it moves from writing to checking, this one is the same mixture of reading, building, and reviewing from beginning to end. The middle half of the reading calls is spread across a 49-hour span, the writing across 33 hours, and the reviewing across 27, inside an 88-hour session. Every category is spread across most of the run.
* **Idling is deferred to the end.** The fleet works nearly continuously for the first 56 hours.
* **The proportions still match.** Writing Rust is 2% of tool calls here against 1% there, reading 44% against 57%, reviewing 23% against 27%. The two sessions agree on what the work to be done is, just not on when it happens.

Those blank slices at the end are also a visual representation of a problem becoming more and more common in this agentic coding era: waiting for approval. Someone on the team and/or an agent reviews the code and leaves feedback, there’s a brief period of activity where the agent addresses the feedback and drives CI to green again, and then more waiting, rinse and repeat, until eventually we get the dopamine-inducing stamp of approval.

These two examples each represent a dominant pattern. About a fourth of the sessions look more like the model orchestration session, while three quarters look like the extensions runtime one. The clean progression through phases was the exception; the common case was the agent planning, writing, and reviewing all the way through.

## Code review at scale

Much of that focus on review visible in the previous graphs was due to my explicit prompting. I created a simple prompt-as-a-skill I called `rust-rebase-review` (in addition to a general Rust coding skill we have merged in the repo). Due to the fast rate of incoming changes, many of which conflicted, I was frequently rebasing. Via custom instructions, I encouraged the harness to invoke this skill at appropriate points in the process, and also manually invoked it from time to time. The prompt evolved a bit over time, but it was a variation of this:

```
Squash into a single commit, then rebase on the latest in origin/main, resolving all conflicts, and force push. As part of rebasing, pay extra special attention to anything that has changed, been added, been removed, and ensure that logic is all ported over to the corresponding Rust code correctly. Always do the rebasing yourself / in the main agent; do not spawn a subagent for it.

Then enter a review/fix loop where you launch a subagent per opus 5, gpt-5.6-sol, and grok 4.6.

- That subagent should do a line-by-line comparison of the old TypeScript and the new Rust, confirming behavioral equality.
- Look for anything introducing any kind of incompatibility; our goal is to move this code into Rust with as close as is possible to 100% the same semantics. If you hit anything questionable, ask me about it.
- We want to ensure we're writing as efficient and idiomatic Rust code as we can; look for opportunities to simplify, to use routines like from the memchr crate to optimize searches instead of open-coded loops, avoid unnecessary allocation, use traits for reuse and loose coupling, etc.
- Ensure that all defunct TypeScript code (e.g. code that has been fully ported, tests that are now no longer necessary because they're duplicative, unnecessary napi shims, etc.) has been deleted.
- Ensure that we've ported as much code as possible, e.g. if there's any TypeScript remaining in touched files and that TypeScript is more than just a shim, that's a red flag. If new TypeScript that's not just a super thin shim is being added, that's a red flag. Look for any callers of TypeScript shims to see whether those callers can instead be ported to Rust, pushing the boundary as far as reasonably possible. Our goal is to soon get to 100% Rust in the runtime layer.
- Validate that no E2E tests have been deleted or changed. Such changes are an indication of a porting bug.

If a review surfaces issues, validate them, and then if there are any to fix, fix them, and iterate to do another full review. Continue iterating with reviewing/fixing until all reviews come back clean. After every set of changes in response to review feedback, commit and push so that CI validation runs concurrently with subsequent reviews.

Don't bother running full test suites; that'll be handled in CI. Try to minimize CPU-consuming efforts to the bare minimum, as we'll likely have many operations happening concurrently.
```

With the frequent rebasing, it’s easy for incoming changes to get lost accidentally. But we found an unexpected benefit to the in-place atomic swap: by deleting the TypeScript at the same time as we were adding the corresponding Rust, we were implicitly creating conflicts with rebase-induced incoming changes to that TypeScript: one branch changing it and the other deleting it. This guaranteed we’d notice changes to already ported code, rather than needing to rationalize for every incoming line whether it was something that might have been touching previously ported code.

My own reviews were of course only a portion of the agentic review being performed. In addition to CCR running on every commit, the team has multiple dedicated code review bots, each with their own approaches and prompts, running on every commit and providing detailed feedback. All of this would end up in comments on the pull requests that would then need to be addressed. Thankfully, handling all of that agentic feedback can also be handled agentically (mostly).

For my review, I focused on architecture, design, conventions, and approach. The agents did the exhaustive old-versus-new comparisons; tests and static analysis checked mechanically enforceable properties; human reviewers concentrated on architecture, API contracts, risk, and any suspicious places surfaced by those other layers.

I chose the destination architecture, decided what behavior mattered, partitioned the work, resolved ambiguous trade-offs, judged the evidence, manually reviewed high-risk areas, reviewed agentic responses to feedback, and made the final merge decisions. The agents changed the amount of code one engineer could supervise. They did not remove the need for an engineer who understood the system and could vouch for the direction, the guardrails, and the release.

## Automating the inner loop

The GitHub Copilot app was central to this work. It manages lots of concurrent active sessions, making it easy to switch between them and carry along with each all the relevant paired context (associated terminal windows, browser windows, canvases, and so on). The feature that mattered most here is agent merge:

![The GitHub Copilot app's Agent Merge panel tracking review feedback, CI, conflicts, and merge readiness.](<https://github.blog/wp-content/uploads/2026/09/AgentMerge.png?resize=946%2C439>)

Agent merge is a loop built into the app (the CLI has it as well with `/pr auto`). On a timer or in response to external stimuli (like notifications from GitHub of CI completion or a review comment), the app will look to see what has changed. If a review comment has been left, it’ll invoke the agent to decide whether to reject the comment or to accept and address it (and respond, noting it’s automation that’s responding). If a test fails, it’ll download the logs, investigate the failure, and fix the bug. If a conflict occurs, it’ll invoke the agent to merge or rebase. Effectively, it automates the loop we as human developers all do, driving our pull requests to “green”, getting sign-off, and eventually merging.

Agent merge handled every single porting pull request. In most cases we stopped short of the actual “merge” part, however. The agent would fix all CI failures, address and respond to all comments, and make sure all conflicts were resolved. Before merging, I’d spot check what the agent actually did, and in particular how it addressed feedback. Did I disagree with any of its responses to reviewers? Was the high-level direction of applied fixes desirable and sound? For these ports, I typically left that last entry unchecked.

That last checkbox mattered more than once. In one merge loop pass, the port had deleted one of the functions exposed to the SDK. Our repo’s schema compatibility CI leg did exactly its job and failed. The agent’s response was to apply the repo’s `schema-break-ok` automation label, which is the escape hatch to make the check pass. In reviewing the pull request before merge, I asked the obvious question: “What is the schema break? You added the `schema-break-ok` label to the pull request; why is it ok?” It wasn’t. The method existed on `main`; the port had simply lost it. I called it an unacceptable regression and told the agent to bring it back fully in Rust. Twenty-one seconds later, the waiver was removed, and the method was restored with a native Rust implementation.

We responded to failures both locally and globally, fixing the individual cases but then also fixing the system so that they’d be less likely to recur. We continually evolved the instructions fed to the coding and review agents to further reduce the chances of these same issues happening again in future porting pull requests. We turned session logs into evals. And in some cases, we actually used the lessons learned to improve the runtime itself, via tweaks to prompts or tool descriptions or how autopilot operated.

## Two migrations in one

A language rewrite is almost never only a language rewrite. Every library the runtime leaned on had to be replaced, too, and unlike in the Rust code we wrote and owned, those replacements weren’t ours to make faithful. Some were the same idea under a different name. Some took several crates to cover what one npm package had done. A few had no acceptable off-the-shelf answer at all and had to be written by hand (by agent).

The CLI and runtime are currently in the same repo, sharing a `package.json`. Over the course of the port, we removed ~60 npm dependencies because they were only being used by runtime code that was ported to Rust. This is a lower bound for removals, because there were packages replaced for the runtime but that were still needed by the CLI. For example, `zod` is a TypeScript schema declaration and validation library that both the CLI and the runtime were using. With the Rust port, the runtime now uses a combination of `serde`, `schemars`, and `jsonschema` to satisfy the same purposes, yet `zod` is still in the manifest for the CLI’s sake.

There were a bunch of examples where one npm package became one crate doing the same job. `js-tiktoken` became `tiktoken-rs` with the same `o200k_base` encoding. `ignore` became the crate of the same name with the same gitignore semantics. `minimatch` became `globset`, `fast-myers-diff` became `similar`, `dompurify` became `ammonia`, and `github/keytar` became `keyring`.

In other cases, we weren’t able to replace packages with crates one-to-one. Instead one package became several crates, or several collapsed into fewer. This is where the majority of the dependency work went. Eight `opentelemetry/*` packages became four crates plus a hand-written tracker state machine and file exporter. Three web-content packages, `mozilla/readability`, `linkedom`, and `turndown`, became two crates, `readability` and `htmd`. `sharp`, `image-size`, `file-type` became `image` and `imagesize`. And so on. There were also five cases where we replaced an npm package entirely with a completely custom implementation.

## How much `unsafe`?

Another question people ask about agent-written Rust is how much of it quietly opted out of safety guarantees. Rust’s safety is a property you can turn off with a keyword, so an agent that hits a borrow it can’t satisfy has an obvious escape hatch. Within the entire runtime crate, we now have 158 `unsafe` blocks, across only 36 files (alongside them are 26 `unsafe fn` declarations, 26 `unsafe extern` blocks, and nine `unsafe impl` trait implementations). Importantly, every single one of these is about interop with external components.

| Why the unsafe block exists | Blocks | Share |
| --- | --- | --- |
| C ABI boundary | 51 | 32.3% |
| Windows API | 49 | 31.0% |
| POSIX / libc | 46 | 29.1% |
| SQLite C API | 7 | 4.4% |
| Dynamic library loading | 4 | 2.5% |
| Process environment | 1 | 0.6% |

![Donut chart of 158 unsafe blocks, all at external boundaries: C ABI, Windows API, POSIX and libc, SQLite, dynamic library loading, and process environment mutation.](<https://github.blog/wp-content/uploads/2026/09/unsafe-boundaries.svg?resize=1024,427>)

The C ABI blocks are the front door SDK hosts come through, so they receive raw pointers and lengths from a caller the Rust compiler has no control over. The Windows and POSIX blocks are system calls: registry reads, credential handshakes, process trees, `sysconf`. SQLite is a C library. Dynamic library loading is `dlopen`, which cannot be safe by construction if for no other reason than because the symbol you resolve might not be the function you expected. The process environment `unsafe` block exists because Rust 2024 treats mutation of process-global environment state as unsafe in a multi-threaded process. Every one of those `unsafe` blocks marks a place where the guarantees Rust makes genuinely end: the thing on the other side is a C function, a syscall, a pointer from a foreign runtime, or process-global host state.

The useful property is that `unsafe` makes all of these places in the Rust code we own auditable. The equivalent code in the TypeScript runtime crossed exactly the same boundaries, through Node’s C++ internals and native npm packages, and nothing in our source marked where the checked world stopped. Of course, that’s not a complete inventory of every safety boundary in the delivered system: dependencies, build tools, C libraries, safe wrappers, and incorrectly specified FFI contracts can still contain or expose unsafety.

The details of where `unsafe` is used are interesting, but I’m more interested in where it isn’t used. It’s not used in the model clients, in the MCP layer, in the agent layer, or in the prompt layer. And not one of the known regressions from the porting effort involved an `unsafe` block.

## The regressions

Porting code is easy. Making it correct is hard. And with a codebase as large and as complicated as the Copilot agent runtime, regressions are to be expected.

By September 14, 2026, we’d traced dozens of known port regressions, all fixed. Most were correctness bugs, with a smaller set of performance regressions. Against the ~832,000 lines of production Rust written from scratch.

Of course, not all of those regressions shipped. Some were caught just by developing in the repo. Others appeared in a pre-release but were fixed before making it to a stable release. And some reached a stable release, often after being unnoticeable enough to make it through one or more rounds of pre-release usage undetected.

![Known correctness regressions grouped into five recurring failure modes: incomplete migration, state and lifetime, behavioral contract mismatches, host boundaries, and incorrect test oracles.](<https://github.blog/wp-content/uploads/2026/09/regression-taxonomy-1.svg?resize=1024,350>)

This isn’t zero, of course, and I’m 100% sure there are more than the ones we know about. These are the ones we noticed or that were reported, but a migration this size absolutely shipped a few more that have been quiet enough that nobody has hit them yet. As with bugs in general, I expect we’ll continue to discover a trickle of corner-case regressions as the further reaches of the stack are exercised aggressively in the wild.

The absolute number also isn’t all that important. What’s more important is the “why” all of these happened, so that we can learn from the issues and avoid repeating them in the future. Nearly all of the correctness regressions land in three large families: the new code implemented a different behavioral contract; state, ownership, or lifetime behavior changed; or some part of the migration was omitted, only partially applied, or lost in rebasing. A smaller set came from requirements at the host or interop boundary, and even from tests that confidently validated the wrong behavior. Those families become more concrete in a handful of recurring patterns:

**Ambiguous semantics.** Several regressions came from behavior the source language left implicit. TypeScript has one `number` type; Rust requires choosing among several, including whether a value can be fractional. And the agents guess wrong. Fields that were conceptually integers became `f64`s, so Rust serialized values such as `42.0` instead of `42`: strongly typed SDKs like Go and C# couldn’t unmarshal a repo ID into an `int64` and rejected a hook timestamp and task duration. In the other direction, an agent declared `timeToFirstTokenMs` as `i64`, but the streaming path emitted values such as `5446.712845`, making written sessions unreadable and unresumable. A subtler case wasn’t about types: `event.error || "Unknown error"` became `.unwrap_or("Unknown error")`. JavaScript’s `||` replaces an empty string; Rust’s `unwrap_or` preserves it, so an empty subagent error remained empty. Oops.

**Ambient behaviors.** Another recurring source was behavior JavaScript or Node supplied invisibly. Quota code used `toLocaleDateString`, which inherits the host time zone; Rust needed that zone passed explicitly. But although `Intl.DateTimeFormat().resolvedOptions().timeZone` is typed as `string`, it can return `undefined`, which napi couldn’t convert to a Rust `String`, breaking model-list loading. Elsewhere, moving an environment-variable read from before an `await` to after it meant a host change during the wait could alter the result. A native-addon loader called `process.report.getReport()` only to identify the platform, but on Windows that honored `_NT_SYMBOL_PATH` and could spend minutes downloading PDBs before rendering the CLI. Other ambient inputs included the working directory, repository identity, `PATH`, and session authentication. Moving ownership into Rust required deciding when to capture each one, how to carry it, and when to refresh it.

**Only porting half of a pair.** Several of the regressions came from paired operations falling out of sync. A turn-cap check updated the native registry’s abort state but didn’t cancel the in-process model loop, allowing one more request to escape. Elsewhere, task completion was persisted and emitted but not projected into active session state, so Autopilot continued after completing the task.

**Blocking the main thread.** The CLI still drives the Rust runtime from Node’s single-threaded event loop, so synchronous work across the napi boundary freezes the UI. `/chronicle reindex` parsed hundreds of session files this way, blocking rendering and input for nearly a minute. Making the export `async` and moving the work to a blocking thread-pool thread fixed it. An audit found several more potentially blocking entry points, leading to a standing rule: napi exports that do real work must be asynchronous and, when necessary, use `spawn_blocking`.

**Flashing windows.** On Windows, spawning a child process without `CREATE_NO_WINDOW` briefly opens a console window. The Node.js runtime had monkey patched process spawning to add the flag, hiding the requirement from the porting agent; the Rust replacement omitted it. An audit fixed two additional spawn sites, though those were preexisting omissions rather than port regressions, and we added the rule to the copilot instructions.

**Lifecycle management.** The largest cluster involved lifecycle, disposal, ownership, ordering, or races. Moving state into Rust often left TypeScript holding an opaque handle to an instance stored in a native table. Unlike an object reference, that handle can outlive the instance. One hook disposed mid-request orphaned a `tool_use` block, wedging the conversation because the model API required a matching result. A shell canceled between “announced” and “started” leaked an orphan that kept the session active. A sandbox toggle updated one generation counter but not its native twin, leaving the shell stuck “reconfiguring.”

**Overlooked features.** Another group involved features the port simply missed. One port omitted SDK callbacks and deleted their end-to-end test, prompting a new rule that agents must not change E2E tests without explicit consent. A session abort kept its native half but lost the in-process cancellation needed to interrupt a turn waiting in a tool. The SDK’s ability to replace built-in tool search depended on enablement, the model-facing description and schema, and routing execution to the SDK callback. The port dropped all three (yay for consistency?), silently replacing one consumer’s natural-language search with regular-expression search.

**Different libraries with different opinions.** Several more regressions came from replacing JavaScript libraries or APIs with stricter Rust equivalents. At the time, the Rust MCP SDK (`rmcp`) responded to malformed JSON-RPC input while the TypeScript SDK didn’t. Against a server that answered errors with more malformed output, that politeness became an infinite loop that hung startup. Related libraries in different ecosystems rarely behave identically.

**Ships passing in the night.** A handful of regressions came from branch drift or rebasing. In a repo with hundreds of pull requests per week, a session open for days accumulates constant changes and conflicts. The port required thousands of rebases; even a very high success rate leaves failures.

**And the slowpokes.** The last group was functionally correct but slower. Some regressions lost existing efficiencies such as memoization, fully asynchronous waiting, or bounded log streaming. Others added migration-specific overhead at the Rust-TypeScript boundary: redundant serialization, locking, polling, unbounded native concurrency, and native-to-host crossings. These weren’t later optimization opportunities; each was a degradation introduced by the port. One read-only scan deep-copied a 260 MB event log instead of borrowing it. Under sustained event traffic, another implementation completed only a tiny fraction of the requested channel flushes while retaining an asynchronous handle per event until V8 exhausted its heap.

Dozens of regressions sounds like a lot. But in a port that generated more than 800,000 lines of code, frankly I’m surprised and pleased we didn’t encounter an order of magnitude more. We can also get a sense for whether these were broadly “felt” by looking at trends in issues in the public [github/copilot-cli](<https://github.com/github/copilot-cli>) and [github/copilot-sdk](<https://github.com/github/copilot-sdk>) repos. Here we’ve classified issues opened from January through August as quality related when they carried a bug label or when their title used common failure terms such as “bug,” “regression,” “crash,” “hang,” “timeout,” “broken,” or “incorrect.” The levels were essentially unchanged from before the porting work as compared to during and after it:

| **Repository** | **Jan–Apr, before the rewrite** | **May–Aug, during / after the rewrite** |
| --- | --- | --- |
| [`github/copilot-cli`](<https://github.com/github/copilot-cli>) | 22.9% (454 / 1,982) | 23.7% (354 / 1,496) |
| [`github/copilot-sdk`](<https://github.com/github/copilot-sdk>) | 36.2% (190 / 525) | 32.3% (135 / 418) |

That’s not an availability metric or an exact count of escaped defects… as with the regressions themselves, there can be a ton of variability in what an issue represents, its scope, and so on. But it does provide a useful check: despite the extraordinary volume of product change, the product-facing issue channels didn’t show a meaningful quality concern spike during the migration.

## If it compiles, it compiles

Earlier I referred to a popular meme that Rust is great for AI-generated code because of its strict compiler. There’s another related and popular Rust meme, that if the code compiles, it’s correct. Nope. Our known regression list provides a good response to that: every regression in the corpus was merged to `main`, which means it successfully compiled. The compiler accepted the buggy versions because, as far as the compiler was concerned, every one of them was valid Rust.

The compiler can prove that an `f64` is used consistently. It cannot know that a repository ID must be serialized as an integer or that a timestamp serialized with a trailing `.0` will be rejected by every strongly-typed SDK at the other end of the wire. The compiler can prevent unsynchronized data races in the code it can see, but it can’t prevent a perfectly synchronized state machine from encoding the wrong states. A queue can be protected by locks and still let two senders each conclude the other will drain it. Events can move safely between threads and still arrive in the wrong order. A synchronous napi function can be memory-safe and still block Node’s main thread for a minute. Compilation is also almost by definition unable to detect something that isn’t there. The compiler can’t object when a rebase quietly removes a guard and its test, or when a process spawner forgot the Windows flag that suppresses console window pop-ups. It also had no opinion about cloning a 250 MB event log on every read. A compiler checks whether the program you wrote is internally coherent. It cannot check whether you wrote the whole program, preserved the old contract, called things in the right order, met the host’s unwritten requirements, or did the work at an acceptable cost.

That’s in no way an argument against Rust’s compiler. As with any statically-typed language, the compiler eliminated a huge class of mechanical mistakes and gave the agents an exceptionally useful inner loop. But “if it compiles, it’s correct” is useful only as a joke.

## Perf, perf, and more perf

So what did we buy with all of this? The port was deliberately behavior-preserving. It did not set out to redesign algorithms or fix bugs; in fact, I repeatedly pushed the agents away from opportunistic optimization because changing language and behavior at the same time makes it much harder to know which one broke you. A key goal of the rewrite, however, was indeed performance and scalability (in addition to reliability and other factors). When folks asked me why did I set out to rewrite the runtime in Rust, my answer was often along the lines of “I didn’t set out to move to Rust, I set out to move away from Node.js and V8”. That move has indeed significantly moved our needle on performance.

I benchmarked several scenarios for the runtime via the C# SDK before and after the port (the TypeScript, Python, Go, C#, Java, and Rust SDKs all reach the same engine through the same transport architecture). The baseline against a pre-port build of the SDK and CLI, with the TypeScript runtime hosted by Node and reached over stdio. The August 21 result uses the Rust runtime, both as an out-of-process server and loaded in-process through FFI. This is an end-to-end comparison of the delivered systems rather than an attempt to isolate the effect of the language change; other changes landed during the same period, so the numbers need to be taken with some grains of salt.

Each timed turn went to a deterministic chat completion server running on localhost, producing a fixed, small response. In other words, these numbers deliberately remove model inference and network latency. They measure the part we changed: client startup, process launch, session creation, event handling, persistence, teardown, etc.

| **Scenario** | **May 12** | **Aug 21 out-of-process** | **Aug 21 in-process** |
| --- | --- | --- | --- |
| Client, session, one turn | 5.25 s | 1.33 s (4.0x) | 292 ms (18.0x) |
| Resume 32-turn session | 5.64 s | 1.52 s (3.7x) | 264 ms (21.4x) |
| Ten concurrent client lifecycles | 12.34 s | 4.18 s (3.0x) | 742 ms (16.6x) |
| 1,000 one-turn session lifecycles | 132.52 s | 22.53 s (5.9x) | 20.93 s (6.3x) |

![From May 12 to August 21, creating a client and session, completing one turn, and tearing them down improved from 5.25 seconds to 55.3 milliseconds in-process, throughput rose from 7.55 to 120.0 sessions per second, and ten-client memory fell from 1,383 MB to 126 MB.](<https://github.blog/wp-content/uploads/2026/09/performance-1.svg?resize=1024,452>)

The “client, session, one-turn” number is the easiest one to feel. It’s creating a client, creating a session, doing a single turn, and tearing everything down. A large part of the out-of-process overhead came from starting Node, initializing V8, and loading, parsing, and generating bytecode for the JavaScript application produced from the TypeScript code before the first turn could begin. The Rust runtime removes those Node/V8 and JavaScript-loading costs.

The “1,000 one-turn session lifecycles” pressure test represents a step function change in what we can build. It uses a single shared client and measures running 100 concurrent pipelines, each creating a session, taking a complete model turn, disposing the session, and doing that ten times in sequence. The pre-port TypeScript CLI completed 7.55 of those lifecycles per second. Rust out-of-process completed 57.45. Rust in-process completed 120.0.

That is a workload-specific result; the Rust runtime is not universally “15.9x faster.” But it is exactly the workload server hosts care about: many independent sessions sharing one runtime. And wall-clock time isn’t hiding the work on another core. In a separate resource-sampling pass over the same 100-by-10 workload, the pre-port process tree consumed 312 seconds of aggregate CPU. The Rust configurations consumed about 110 seconds. That is CPU capacity the host can spend on more sessions.

Memory tells the same story, with the usual warning that memory metrics are annoyingly easy to misuse. Looking at resident private memory added during the ten-client batch, the pre-port process tree peaked 1,383 MB above baseline. Rust out-of-process peaked at only 247 MB. And Rust in-process at 126 MB, an order of magnitude less. While these numbers of course will differ from use to use and machine to machine, they get to the core goal: a service can host far more clients and sessions on the same machine before memory, process count, or CPU become the limiting resources.

The best part is this is the baseline port. Much of the implementation still consists of TypeScript-shaped algorithms faithfully rendered in Rust. We haven’t yet done the broad redesign work that the new ownership model, concurrency model, and in-process architecture enable. Getting an in-process client through creation, a complete one-turn session, and teardown in about 55 milliseconds, pushing a shared client to 120 one-turn session lifecycles per second, and cutting the measured ten-client memory delta by 91% before that optimization work begins is a very good place to start.

## What the port cost

So… how much did all this cost, monetarily? Drum roll, please….

My token spend for all of the porting work was ~136.3 billion total tokens, including ~130.6 billion cached input read tokens, ~4.2 billion cached input write tokens, ~900 million fresh input tokens, and ~600 million output tokens. The monetary bill for all those tokens came to ~$120,000.

Of course, these tokens weren’t spending themselves. The cost for using a significant amount of a developer’s time guiding these agents should also be factored in. However, I wasn’t devoting all of my time just to this project. Agentic development is a lot of “hurry up and wait;” submit a prompt, let the coding agent do its thing, check in on it from time to time to possibly steer it, but in the meantime until it completes, do other things. That means developers are no longer working on just one coding task at a time; they’re overlapping that waiting to enable working on many things concurrently. During the porting window, these Rust porting PRs represented ~20% of my PRs across all repos to which I was contributing. If we wave our hands and estimate that the share of pull requests approximates the share of my time, that works out to roughly three weeks dedicated to the port.

In other words, the rough bill for the porting effort was about $120,000 in attributed token spend plus three weeks of a developer’s time.

That said, the end-to-end Rust migration was not 100% a single developer; it’s been a team effort. [@stevesandersonms](<https://github.com/stevesandersonms>) provided the design and implementation for `napi-oop`, the temporary out-of-process interoperability layer, as well as five of the six SDK FFI implementations, with [@edburns](<https://github.com/edburns>) providing the sixth. [@roji](<https://github.com/roji>) implemented the packaging for the SDK to ship and utilize the Rust binaries correctly. [@caarlos0](<https://github.com/caarlos0>) has been helping to split the Rust code into many small subcrates in order to help with build times that started to become problematic, while [@criemen](<https://github.com/criemen>) helped with improved asset caching to speed up CI and local builds. [@devm33](<https://github.com/devm33>), [@examon](<https://github.com/examon>), [@MRayermannMSFT](<https://github.com/MRayermannMSFT>), [@dereklegenzoff](<https://github.com/dereklegenzoff>), and others have helped with countless pull request reviews and approvals. And everyone contributing to the copilot-agent-runtime repo has been supportive and accommodating as the world has shifted beneath their feet.

## Lessons learned

The experience reinforced a few lessons that apply beyond this rewrite. Here are some things we’d carry into a next time:

* **The goal needs to be clearly and fully stated.** Our early instructions were too vague. “Port XYZ component to Rust” got read as only the hot paths, or only the logic, and the agents repeatedly treated I/O and orchestration as being out of scope. Once we were explicit that the end state was a native binary built from a 100% Rust codebase, with no execution environment left for TypeScript even if we wanted one, they got much better at driving autonomously to that goal.
* **End-to-end tests are absolutely, unequivocally critical.** With one exception, all of the regressions that involved missing features, and many of the others, were due to lack of sufficient end-to-end tests. For any port of this nature, you must have tests that can be used to validate the correctness of the port, and those tests can’t themselves be re-written during the porting, or else you lose your oracle. Our initial plan for this porting effort called this out, and called out that we needed to significantly improve our E2E testing posture before starting on the port. We did that, but we didn’t do it enough. We’re sure that if we’d added even more E2E tests before starting the port, really focusing on ensuring most meaningful behaviors were covered by them, we would have had fewer regressions than we did along the way.
* **Protect the oracle from the agent.** The agent changing an implementation cannot also be allowed to silently redefine correctness by weakening a test, updating a snapshot, raising a compatibility baseline, or applying an escape-hatch label, at least not without oversight. Keep the behavioral contract independent where possible, put sensitive guardrails behind separate ownership or approval, and layer checks with different failure modes so that one mistake isn’t enough to ship a big regression.
* **Translate first, redesign second.** Preserving behavior and existing algorithms kept the number of variables moving at once manageable. Once the old implementation and the transitional scaffolding are gone, ownership, concurrency, and performance can be redesigned against a stable baseline. I veered away from this a few times, out of restlessness or difficulty saying no to a peer or a conviction that this case was different, and in hindsight I regret every one of them. Each cost more regressions, more time, or more tokens than staying the course would have.
* **Turn repeated failures into future successes.** AI agents will wander off track: when they do, learn from it. When a failure mode shows up twice, it belongs in standing instructions, a reusable skill, an eval, a protected baseline, or the harness itself.
* **The developer inner loop matters more with agents in the loop, not less.** For us as developers, we live and breathe in our inner loop, how quickly we can make a change, build, test, iterate. We get frustrated when the tooling part of that takes too long. And it’d be forgivable to think that doesn’t apply when agents are doing the lower-level work. It does. More so. AI agents do the thinking and writing code parts of these tasks incredibly quickly, but they still need to build and they still need to test. And the proportion of the time they build and test actually increases as they spend less time thinking and writing and more time in fast validation inner loops. Spend some time up front optimizing the inner loop, and optimizing it for multiple things happening at the same time (e.g. as if you’re working on multiple tasks in multiple worktrees at the same time). You will thank yourself later for the investment.

## What’s next?

It worked. The execution runtime that was entirely TypeScript in May is now entirely Rust in August, and it shipped to real users continuously the whole way rather than landing as one terrifying cutover at the end.

I didn’t simply ask an AI agent to “port this whole codebase from TypeScript to Rust.” Even if that’s where we’re heading as an industry, we’re definitely not there yet. Instead, agents made an entire category of project feasible. A rewrite producing hundreds of thousands of lines of production Rust in a live system, done in place, in `main`, by one engineer supported by a team, is not a proposal that would have been accepted before agents. It would have needed a whole team and a year or two, it would have competed against every feature that team could have shipped instead, and it would have lost (and, honestly, should have lost). Agents moved the price to where the project became tenable.

The port itself is complete: the runtime’s production implementation is 100% Rust, and the temporary internal TypeScript/N-API seam is gone. There is still plenty of work we desire to do, though: further improving the build system and developer inner loop, cleaning up translated structures, redesigning around Rust’s ownership and concurrency models, and pursuing further performance wins. The port was a translation, deliberately so (prompted as such), and we kept behavior as close to 100% identical as possible, rather than opportunistically fixing additional bugs, rearchitecting components, or further improving performance and scalability (beyond what came implicitly from the rewrite). Much of the code at the micro level is idiomatic Rust, but at the macro level there’s a good deal of initially TypeScript algorithms wearing Rust’s syntax. Revisiting those decisions now that the constraints underneath them have changed is where the interesting wins will be.

I’m most excited about what the port makes possible. The SDK can be loaded directly into a host process in any of six languages, with no Node.js or V8 in the dependency chain, and no second process to supervise, which is the single most common piece of friction we’ve heard from partners adopting the SDK. A runtime instance that costs a fraction of what it used to means a host can run far more concurrent sessions before it runs out of machine. And the runtime can now go places Node.js was never going to follow it, across the spectrum of cloud to desktop to device to embedded systems. None of that is the finish line. It’s the base we now get to build the future of GitHub Copilot on top of, and after three months of watching agents rewrite the very engine that runs them, I’m looking forward to seeing just how far it goes.

Happy coding!

---

## Tags:

* [AI agents](<https://github.blog/tag/ai-agents/>)
* [developer experience](<https://github.blog/tag/developer-experience/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [Rust](<https://github.blog/tag/rust/>)
* [technical deep dive](<https://github.blog/tag/technical-deep-dive/>)

## Written by

![Stephen Toub](<https://avatars.githubusercontent.com/u/2642209?v=4&s=200>)

### [Stephen Toub](<https://github.blog/author/stephentoub/>)

[@stephentoub](<https://github.com/stephentoub>)

Stephen Toub is a Distinguished Engineer at Microsoft.


</details>
