# 构建通用无障碍 Agent 的经验

介绍实验性无障碍审查系统，讨论子 Agent、任务顺序、结构化结果、上下文效率与复杂代码检查的局限。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-05-15](<https://github.blog/ai-and-ml/github-copilot/building-a-general-purpose-accessibility-agent-and-what-we-learned-in-the-process/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/building-a-general-purpose-accessibility-agent-and-what-we-learned-in-the-process/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/构建通用无障碍 Agent 的经验.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 构建通用的可访问性 agent——以及我们在这个过程中学到的东西

轻描淡写地说 agents 已成为一种流行的代码工作方式。 GitHub 在其许多举措中采用了基于 agent 的代码创建和编辑，包括 ** 试点 agent 来帮助 ** [** 我们对可访问性的承诺 **](<https://github.blog/news-insights/company-news/github-accessibility-and-the-disability-divide/>)。

GitHub 目前正在试点实验性通用辅助功能 agent，以实现两个主要目标：

1. 在 GitHub Copilot CLI 和 Copilot VS Code 集成中为工程师提供 ** 可靠、及时的辅助功能问题 ** 答案。
2. 在投入生产之前捕获并**自动修复简单、客观的可访问性问题**。

对于第二个目的，可访问性 agent 设置为自动评估修改我们前端代码的更改。

迄今为止，agent已**审查了3,535拉取请求，分辨率为68%**。按照发生的顺序，排名前五的问题类型集中在：

1. [让辅助技术的结构和关系变得清晰](<https://www.w3.org/WAI/WCAG22/Understanding/info-and-relationships.html>)
2. [为交互控件提供清晰简洁的名称](<https://www.w3.org/WAI/WCAG22/Understanding/name-role-value.html>)
3. [确保用户了解重要公告](<https://www.w3.org/WAI/WCAG22/Understanding/status-messages.html>)
4. [确保非文本内容有文本替代品](<https://www.w3.org/WAI/WCAG22/Understanding/non-text-content.html>)
5. [按逻辑顺序在页面和视图中移动键盘焦点](<https://www.w3.org/WAI/WCAG22/Understanding/focus-order.html>)

这些问题类型中的每一个都代表自动消除的摩擦和障碍，否则这些摩擦和障碍将抑制使用和依赖辅助技术的人们使用 GitHub。这是它的实际运行截图：

![A GitHub Actions 机器人对 Pull 请求中的一行代码进行评论，建议修复内容顺序可访问性问题。注释如下：“WCAG 1.3.2 有意义的序列：.header CSS 类使用 flex-direction: row-reverse，这会导致关闭按钮首先出现在 DOM（和屏幕阅读器阅读顺序）中，但在视觉上呈现在标题之后。这会造成程序化阅读顺序和视觉布局之间的不匹配。一种更简单的方法是交换 DOM 中的元素顺序并使用 CSS 中的常规 flex-direction: 行，以便阅读顺序与有视力的用户看到的内容相匹配：' 接下来是重新排序标题和侧面板工具栏的代码建议，并可以选择将建议提交到代码中。之后是最后一条评论，内容为“这还需要更新agent-task-content.module.css中的标题以更改flex-direction：row-reverse→flex-direction：row。”裁剪屏幕截图。](<https://github.blog/wp-content/uploads/2026/05/image1.png?resize=1024%2C908>)

有兴趣吗？我们将**概述此实验的成功经验和经验教训**，希望它能够帮助其他团队的无障碍之旅。

## 心态

[残疾的社会模型](<https://en.wikipedia.org/wiki/Social_model_of_disability>)告诉我们，由于环境的构建方式，可能会造成进入障碍以及由此产生的损害。同样的想法也适用于数字体验。

对于可访问性agent、**，我们并不试图孤立地“解决”可访问性**。相反，我们正在尝试**增强同行的努力**，以更好地帮助他们消除因我们构建GitHub用户界面而可能产生的障碍。

**可访问性agent并不是可以自动解决每个假设场景的“灵丹妙药”**。更好地理解、尊重和社交化这一点有助于确定 agent 的责任范围。这加快了实验的启动，从而获得了更多的支持。

## 过去的努力

[《欧洲无障碍法案》](<https://commission.europa.eu/strategy-and-policy/policies/justice-and-fundamental-rights/disability/european-accessibility-act-eaa_en>) 现已生效。 [美国残疾人法案](<https://www.ada.gov/law-and-regs/regulations/title-ii-2010-regulations/>)的标题II将建立会议WCAG2.1AA作为2027四月份完成的法律定义。 LLM agents 可以读取 [ 可访问性树 ](<https://www.nngroup.com/articles/ai-agents-as-users/#:~:text=Accessibility%2DTree%20Parsing>) 并对其执行操作。

简单地说：**如果组织尚未投资于手动识别和修复可访问性问题，那么他们将处于不利地位**。造成这种情况的原因有很多，包括构建可访问性 agent。

到目前为止，GitHub 拥有一个成熟的系统来记录可访问性问题，并验证问题的修复是否按预期工作。这包括：

* 用于报告问题的结构化模板
* 重现问题的步骤
* 关于问题的严重性级别、服务范围和适用的 [WCAG 成功标准 ](<https://www.w3.org/TR/WCAG22/>) 的丰富元数据层
* 与解决该问题的 Pull Request 的交叉链接
* 验收标准

此外，所有问题都集中到一个存储库中。虽然此问题记录工作早于 LLM 工具的流行，但其高度一致和结构​​化的性质使其 ** 成为可供参考的可访问性 agent 的理想内容语料库 **。

因此，我们指示 agent 调查这些问题，看看是否有相关的代码和语言片段可以从中推断。在这一领域，法学硕士的非确定性“模糊匹配”行为成为一种资产，而不是一种可能的负债。

### 老金

就像任何其他专业领域一样，技能文件中的模糊说明并不能解决问题。通过简短的示例列表告诉 LLM“使用可访问性最佳实践”效果并不好。

在生成代码时，[**LLM 不幸地偏向于生成可访问性反模式 **](<https://microsoft.github.io/a11y-llm-eval-report/>)，因为当前可用的每个主要 LLM 都经过数十年的不可访问代码的训练。

为了解决这个问题，agent 需要更好的内容来借鉴。

因此，**我热情建议投资于手动编目和修复可访问性问题**。经过一些进展后，该数据可以合并到agent中。

这些问题及其相应的拉取请求为 LMM 提供了高度相关的示例以供参考，这些示例是使用其部署所在组织设置的约定编写的。这个问题和代码的集合是迄今为止 agent 从中汲取的最强大的资产之一。

## 高效的代币消耗

**可访问性是一个整体问题**，与代码、设计、文案以及涉及创建用户界面的许多其他学科相交叉。

许多辅助功能工作也是**高度上下文相关的**，这意味着有人通常需要问题的完整工作情况才能提供适当的建议。

由于这两个因素，通用可访问性 agent 在执行工作时可能会消耗大量令牌。这会带来三个负面结果：

1. 不可靠输出的数量增加
2. 响应时间较慢
3. 运营成本增加

构建 agent 时要勤奋，这一点很重要。以下是我们如何做到这一点的。

### 使用子agents

可访问性 agent 最初是作为单个整体 agent，但很快就突破了这种方法的局限性。因此，我们将其演变为使用 [ 子 agent 架构 ](<https://dev.to/goose_oss/agents-subagents-and-multi-agents-what-they-are-and-when-to-use-them-39na#:~:text=The%20Subagent%20Setup%3A%20Orchestrator%20With%20a%20Crew>)。

许多指南建议创建一个大的子套件agents，每个子套件都有自己特定的职责范围。在这里，子 agents 并行执行，主 agent 协调其输出。

令人惊讶的是，**这种方法对我们**的可访问性agent不利。相反，我们最终使用了两个专用子agents：

1. 第一个子agent充当**被动审阅者和研究员。**
2. 第二个子agent充当**主动实施者。**

两个子agents被沙盒化，不能直接互相传递内容。相反，它们生成结构化、模板化的输出。然后，将此输出提供给父编排可访问性 agent 进行使用、验证和路由。

![该图演示了父级可访问性 agent 如何将工作按顺序从自身传递到只读审阅者子 agent，然后返回到父级 agent，再传递到具有写入和读取功能的实现者子 agent，然后再次返回到父级agent。父 agent 包含在标记为“层 1 - 编排”的列中，两个子 agents 包含在标记为“层 2 - 专家”的列中。显示父级 agent 将工作传递给审阅者子 agent 的第一条连接线被标记为“运行子 agent”。将工作传递回父级 agent 的第二行标记为“结构化结果”。第三行的父级 agent 将工作传递给实施者子 agent，并标记为“使用结构化结果运行子 agent”。第四行也是最后一行将工作从实施者子 agent 传递回父级 agent 并标记为“生成的更改或指导”。父级和子级agents 也有责任清单。父级可访问性 agent 路由请求、查找代码和技能、运行复杂性评分、验证输出、管理升级门、管理重新审核循环并回答研究问题。审核员子 agent 执行代码审核、WCAG 研究、检测升级触发器并生成结构化结果。实施者子agent有两种模式：默认代码更改模式和仅后备指导模式。代码更改模式首先修复关键问题，然后解决其余问题。仅指导模式生成指导文档。两种模式都会验证更改。](<https://github.blog/wp-content/uploads/2026/05/image2.png?resize=1024%2C731>)

采用这种方法有几个原因：

* **升级检查点**。审核者检查可能需要人工干预的区域。这包括多个高严重性的 WCAG 故障，以及已知难以访问的模式列表。
* **基于复杂性的行为**。如果认为底层代码太复杂，则 agent 被指示以专门的仅指导模式运行。在这里，家长可访问性 agent 充当仲裁者，而审阅者 agent 则“没有意见”，只是按照指示报告结果。
* **过滤**。审稿人展示了它发现的所有内容。然后，家长可访问性 agent 利用资源和技能来确定与请求相关的内容。审核者将其所有发现传递给实施者的成本高昂，并且可能会使其执行不相关且适得其反的任务。
* **可追溯性**。子agents之间的直接通信将消除创建和审查用户审计跟踪和agent决策的能力。考虑到 agent 围绕复杂模式的说明，以及辅助功能工作的高度上下文性质，这一点很重要。

### 按线性顺序执行指令

除了全面关注之外，有效的数字无障碍工作还需要有条不紊、注重细节的方法。

使用子agents来提高LLM回复速度的担忧与我们对其结果准确的需求相平衡。我们发现，**强制agent以固定顺序**执行其子agent指令是关键。

我们首先建立有序阶段的父集。每个阶段本身都包含儿童有序的说明步骤，并附有相关资源和技能：

![该图演示了研究子agent如何使用有序阶段和每个阶段内的有序步骤来产生结构化输出。第一阶段标记为“阶段 1 - 研究”，并包含 5 步骤。第一步标记为“WCAG SC”，并使用名为“wcag-2.2-level-a-aa-success-criteria”的技能。第二步被标记为“GitHub 的 SC 解释”，并使用一种名为“accessibility-check-wcag-sc-interpretation”的技能。第三步被标记为“辅助技术支持”，并使用一种名为“支持时可访问性检查”的技能。第四步被标记为“事先可访问性审核”，并使用名为“可访问性搜索优先审核一般”的技能。此阶段的第五步也是最后一步被标记为“外部 W3C 引用”，并受名为“仅当本地搜索不足时”的规则管辖。箭头将第一阶段连接到第二阶段，第二阶段标记为“阶段 2 - 代码审核”。阶段 2 的第一步标记为“按需读取源文件”。第二步被标记为“合并用户提供的 URL”，并有一个名为强制其始终获取的角色。第三步标记为“调查提供的 URL 链接”，并受“搜索 1 深度”规则的约束。第四步标记为“运行验证技能”，并使用称为“决策表”的资源。第五步标记为“交叉参考结果”，并使用称为“使用阶段 1 研究”的技能。此阶段的第六步也是最后一步被标记为“重新审查与之交互的所有内容”。箭头将第二相连接到第三相，标记为“相 3 - 结构化输出”。第三阶段包含一个标记为“结果报告，输出模式审阅者”的步骤。它分为三个小节：“摘要”、“发现严重性评分”和“每个发现包括”。摘要小节包含一个有序列表，内容为“1”。总结果'，'2。先前审核', '3。需要升级', '4。升级范围'和'5。升级调查结果'。发现和严重性评分分为三个级别：“严重”、“警告”和“信息”。每项发现均包括适用的 WCAG SC、适用的文件和行号、当前的面向人的体验、预期的面向人的体验、修复建议以及升级摘要（如果存在）。](<https://github.blog/wp-content/uploads/2026/05/image3.png?resize=1024%2C592>)

这种线性顺序的有趣之处在于，它反映了我个人如何执行审计、补救和报告职责。

### 使用模板模式传递子agent内容

沙盒子 agents 的整个操作都是围绕模板模式文件构建的。这些文件**创建一致性**，这对于保持agent专注并步入正轨至关重要。

这两个架构模板是：

1. **审阅者模板架构：** 这重点关注要审核的内容以及如何查找相关的适用信息。
2. **实现者模板架构：**这重点关注要修复什么以及如何修复它。

如果没有适当的模式文件，agents 都会尝试任意地相互通信。这将导致代币支出增加、不良幻觉、不必要的代码更改以及 agent 审计目的难以实现的行为。

## 承认局限性

创建可访问性 agent 的另一个重要方面是 ** 了解 agents 可能不足的领域 **。

由于 agent 不是可访问性的统包“解决方案”，我们希望避免使用 agent 的错误输出可能无法被使用它的人员充分询问的情况。当某人不熟悉数字可访问性注意事项和实践时，这一点尤其重要。

以下是我们为适应 agent 的限制而采取的措施：

### 评估代码复杂性

我们希望避免出现这样的情况：我们需要执行昂贵且耗时的工作来重新访问 agent“认为”可以访问的无法访问的解决方案。

为了解决这个问题，可访问性 agent 使用一个小的 shell 脚本来分析它要处理的代码。脚本本身很简单，使用一小组基本启发法来评估相对复杂性并将其提炼为分数。

然后该分数被 agent 摄取。如果分数超过设定阈值，则指示 agent ** 而不是 ** 执行代码更改。相反，它会通知使用 LLM 的人，他们应该联系辅助功能团队，咨询他们正在尝试执行的操作。

### 识别高风险模式

这是一件很难理解的事情，但要知道**完全有可能拥有通过自动可访问性检查的代码，但在功能上不可用**。

作为代码复杂性的伴随，可访问性 agent 被指示避免尝试为可访问性团队识别为高风险的模式生成代码。这包括但不限于：拖放、[toasts](<https://primer.style/accessibility/patterns/accessible-notifications-and-messages/#toasts>)、富文本编辑器、[树视图](<https://github.blog/engineering/user-experience/considerations-for-making-a-tree-view-component-accessible/>)和[数据网格](<https://www.w3.org/WAI/ARIA/apg/patterns/grid/>)。

这些模式需要大量的集中注意力和细节，并且目前超出了 LLM 目前的能力，无法以实际与辅助技术配合使用的方式进行生产。

不禁止高风险模式和高复杂性代码环境会导致每个人都需要花费不必要的时间来重新解决问题，并且还会给可访问性团队带来声誉风险。我们通过关闭法学硕士走这条路的能力来避免这种情况。

### 减少行动偏见

我讨厌将法学硕士拟人化，但他们似乎都有一个共同的品质，那就是**迫切希望制作内容**。对于 Copilot，这通常意味着生成代码。

我们必须创建 [ 反游戏 ](<https://en.wiktionary.org/wiki/game_the_system>) 指令，以防止 LLM 创建偷偷摸摸的方法来绕过其指令，在需要人类专业知识时不生成代码。这防止了它违反自己的干预指令。

### 了解以编程方式确定的问题并不能涵盖所有问题

Agent 成功指标存在于更大的背景下。

在 55 总共 WCAG A 级和 AA 成功标准中，只有 [35 可以通过确定性自动代码检查器 ](<https://www.w3.org/WAI/standards-guidelines/act/rules/?requirements=a%2Caa&status=approved&implement=auto&display=no-rules>) 检测到。这意味着A级的**~36%和AA成功标准无法自动发现**。

![标题为“WCAG A 和 AA 成功标准”的饼图。两个切片中的第一个标记为“36% 需要手动评估”。两个切片中的第二个被标记为“64%可以自动检测”。](<https://github.blog/wp-content/uploads/2026/05/image4.png?resize=729%2C807>)

LLM-动力agent操作正在解决这个〜36%差距，但这并不是一个完美的科学。因此，在设计和原型制作过程中**尽早手动识别可访问性障碍变得很重要**-[大多数可访问性问题起源的区域](<https://www.deque.com/blog/auditing-design-systems-for-accessibility/#design-should-consider-accessibility>)。

这种想法也反映在 agent 的升级逻辑中，即辅助功能团队的成员可以与设计师合作，帮助考虑替代方法并集体讨论解决方案，以在不影响辅助功能的情况下实现业务目标。

进行这种干预和援助是为了阻止潜在的下游问题——以及昂贵且耗时的重新设计——在它们有机会启动之前就停止。

### 手动评估 agent 输出并调整未按预期工作的内容

我们定期对 agent 输出进行手动审核，以确定其准确性和有效性。此外，我们还拥有适当的工具来捕获拉取请求审阅者的情绪。两者都为 agent 需要更好指导 ** 以及新资源和技能的领域提供 ** 强烈信号。

## 开放学习

回顾一下，我们了解到 agent 是：

* 用于帮助和增强现有的无障碍工作，而不是取代它们。
* 针对您的特定体验进行手动审核和修复可访问性问题的培训后，效果会显着提高。
* 使用子agents时，代币消耗效率更高。
* 以有条不紊、线性的方式执行指令时更加准确和有效。
* 当设置为使用预格式化模板来传递信息时更加一致。
* 了解其局限性并将人们引导至替代支持系统。
* 当定期审查其输出以确定需要更好指导的领域时，就会得到改进。

这段旅程还没有结束。可访问性 agent 继续迭代，希望 ** 有助于确保 GitHub 成为所有开发人员 ** 的可访问且包容的平台。

我们希望我们最终能够开源 agent，作为 [ 我们承诺帮助大规模提高开源软件的可访问性 ](<https://github.blog/open-source/social-impact/our-pledge-to-help-improve-the-accessibility-of-open-source-software-at-scale/>) 的一部分。在那之前，我们希望通过与这项事业分享我们的经验教训，其他团队可以为自己的无障碍工作提供参考资源。

---

## 标签：

* [辅助功能](<https://github.blog/tag/accessibility/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## 撰写者

![埃里克·贝利](<https://avatars.githubusercontent.com/u/634191?v=4&s=200>)

### [埃里克·贝利](<https://github.blog/author/ericwbailey/>)

[@ericwbailey](<https://github.com/ericwbailey>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Building a general-purpose accessibility agent—and what we learned in the process

It is an understatement to say agents have become a popular way of working with code. GitHub has adopted agent-based code creation and editing for many of its initiatives, including **piloting an agent to help with** [**our commitment to accessibility**](<https://github.blog/news-insights/company-news/github-accessibility-and-the-disability-divide/>).

GitHub is currently piloting an experimental general-purpose accessibility agent to achieve two main goals:

1. Providing engineers with **reliable, just-in-time answers to accessibility questions** in the GitHub Copilot CLI and the Copilot VS Code integration.
2. Catching and **automatically remediating simple, objective accessibility issues** before they go to production.

For purpose number two, the accessibility agent is set to automatically evaluate changes that modify our front-end code.

To date, the agent has **reviewed 3,535 pull requests, with a 68% resolution rate**. In order of occurrence, the top five issue types center around:

1. [Making structure and relationships clear to assistive technologies](<https://www.w3.org/WAI/WCAG22/Understanding/info-and-relationships.html>)
2. [Providing clear and concise names for interactive controls](<https://www.w3.org/WAI/WCAG22/Understanding/name-role-value.html>)
3. [Ensuring users are aware of important announcements](<https://www.w3.org/WAI/WCAG22/Understanding/status-messages.html>)
4. [Ensuring there are text alternatives for non-text content](<https://www.w3.org/WAI/WCAG22/Understanding/non-text-content.html>)
5. [Moving keyboard focus through pages and views in a logical order](<https://www.w3.org/WAI/WCAG22/Understanding/focus-order.html>)

Each of these issue types represents friction and barriers automatically removed that would have otherwise inhibited use of GitHub for people who use and rely on assistive technology. Here’s a screenshot of it in action:

![A GitHub Actions bot comment on a line of code in a Pull Request that suggests a fix to a content order accessibility issue. The comment reads, 'WCAG 1.3.2 Meaningful Sequence: The .header CSS class uses flex-direction: row-reverse, which causes the close button to appear first in the DOM (and screen reader reading order) but visually renders after the heading. This creates a mismatch between the programmatic reading sequence and the visual layout. A simpler approach is to swap the element order in the DOM and use regular flex-direction: row in the CSS, so the reading order matches what sighted users see:' Following that is a code suggestion that re-orderes the heading and side panel toolbar, with the option to commit the suggestion to code. After that is a final comment that reads, ''This also requires updating •header in agent-task-content.module.css to change flex-direction: row-reverse → flex-direction: row." Cropped screenshot.](<https://github.blog/wp-content/uploads/2026/05/image1.png?resize=1024%2C908>)

Interested? We’ll be **outlining successes and lessons learned** with this experiment, with the hopes that it can help with other teams’ accessibility journeys.

## Mindset

[The social model of disability](<https://en.wikipedia.org/wiki/Social_model_of_disability>) teaches us that access barriers—and consequently impairment—can be created because of how an environment is built. The same thinking applies to digital experiences.

With the accessibility agent, **we are not attempting to “solve” accessibility** in isolation. We are instead attempting to **augment our peers’ efforts**, to better help them remove the barriers that may be created as a result of how we construct GitHub’s user interfaces.

**The accessibility agent is not a “silver bullet”** that can automatically address every hypothetical scenario. Understanding, honoring, and socializing this better helps set the agent’s scope of responsibility. This sped up the experiment’s launch, leading to more buy-in for the effort.

## Past efforts

[The European Accessibility Act](<https://commission.europa.eu/strategy-and-policy/policies/justice-and-fundamental-rights/disability/european-accessibility-act-eaa_en>) is now in effect. [Title II of the Americans with Disabilities Act](<https://www.ada.gov/law-and-regs/regulations/title-ii-2010-regulations/>) is set to establish meeting WCAG 2.1 AA as the legal definition of done in April of 2027. LLM agents can read and take action on [the accessibility tree](<https://www.nngroup.com/articles/ai-agents-as-users/#:~:text=Accessibility%2DTree%20Parsing>).

To say it plainly: **Organizations will be at a disadvantage if they have not already invested in manually identifying and remediating accessibility issues**. There are many reasons for this, including building an accessibility agent.

To that point, GitHub has a mature system in place for logging accessibility issues, as well as verifying fixes to issues are working as intended. This includes:

* A structured template for reporting problems
* Steps to reproduce the issue
* A rich layer of metadata about the issue’s severity level, service area, and applicable [WCAG success criterion](<https://www.w3.org/TR/WCAG22/>)
* Crosslinks to the Pull Request that addressed the issue
* Acceptance criteria

In addition, all the issues are centralized to a single repository. While this issue-logging effort predated the explosion in popularity of LLM tooling, its highly consistent and structured nature made it **an ideal corpus of content** for the accessibility agent to reference.

Because of this, we instructed the agent to investigate these issues to see if there are related code and language snippets it can extrapolate from. This is one area where the non-deterministic “fuzzy matching” behavior of LLMs acts as an asset rather than a possible liability.

### Old gold

Much like with any other specialized domain area, vague instructions in a skill file won’t cut it. Telling an LLM to “use accessibility best practices” with a short list of examples won’t work well.

When generating code, [**LLMs have an unfortunate bias towards producing accessibility antipatterns**](<https://microsoft.github.io/a11y-llm-eval-report/>) since every major LLM currently available is trained on decades of inaccessible code.

To counteract this, the agent needs better content to draw from.

So, **I enthusiastically recommend investing in manually cataloging and remediating accessibility issues**. After some progress, this data can be incorporated into the agent.

The issues and their corresponding pull requests provide highly contextual examples for the LMM to reference, written using the conventions set up by the organization it is deployed in. This collection of issues and code is by far one of the strongest assets the agent draws from.

## Efficient token consumption

**Accessibility is a holistic concern**, intersecting with code, design, copywriting, and numerous other disciplines involved with creating user interfaces.

A lot of accessibility work is also **highly contextual**, meaning that someone typically needs the full working picture of a problem before they’re able to give the appropriate advice for what to do.

Because of these two factors, a general-purpose accessibility agent can consume a ton of tokens when it performs work. This has three negative outcomes:

1. An increased amount of unreliable output
2. Slower response times
3. Increased operational costs

It’s important to be diligent when structuring the agent. Here’s how we went about doing just that.

### Use sub-agents

The accessibility agent started as a single monolithic agent, but quickly grew past the limitations of this approach. Because of this, we evolved it to use a [sub-agent architecture](<https://dev.to/goose_oss/agents-subagents-and-multi-agents-what-they-are-and-when-to-use-them-39na#:~:text=The%20Subagent%20Setup%3A%20Orchestrator%20With%20a%20Crew>).

A lot of guides recommend creating a large suite of sub-agents, each with its own specific area of responsibility. Here, the sub-agents are executed in parallel, with the main agent reconciling their output.

Surprisingly, **this approach worked against us** for the accessibility agent. Instead, we wound up using two dedicated sub-agents:

1. The first sub-agent acts as a **passive reviewer and researcher.**
2. The second sub-agent acts as an **active implementer.**

The two sub-agents are sandboxed and cannot directly pass content to each other. Instead, they generate a structured, templatized output. This output is then served to the parent orchestrating accessibility agent to consume, validate, and route.

![A diagram demonstrating how the parent accessibility agent passes work sequentially from itself to a read-only reviewer sub-agent, then back to the parent agent, to a write and read-capable implementer sub-agent, then back again to the parent agent. The parent agent is contained in a column labeled, 'Tier 1 - Orchestration', and the two sub-agents are contained in a column labeled, 'Tier 2 - Specialists'. The first connecting line that shows the parent agent passing work off to the reviewer sub-agent is labeled, 'run sub-agent'. The second line that passes work back to the parent agent is labeled, 'structured findings'. The third line has the parent agent passing work to the implementer sub-agent, and is labeled 'Run sub-agent with structured findings'. The fourth and final line passes work from the implementer sub-agent back to the parent agent and is labeled, 'Changes or guidance generated'. The parent and sub-agents also have lists of responsibilities. The parent accessibility agent routes requests, locates code and skills, runs complexity scoring, validates outputs, manages escalation gates, manages re-audit loops, and answers research questions. The reviewer sub-agent  performs code audits, WCAG research, detects escalation triggers, and produces structured findings. The implementer sub-agent has two modes: a default code-change mode and a fallback guidance-only mode. The code-change mode fixes critical issues first, then addresses the rest. The guidance-only mode generates guidance docs. Both modes validates changes.](<https://github.blog/wp-content/uploads/2026/05/image2.png?resize=1024%2C731>)

There are a few reasons for this approach:

* **Escalation checkpoints**. The reviewer checks for areas where human intervention will likely be needed. This includes multiple high-severity WCAG failures, as well as a list of patterns that are known to be difficult to make accessible.
* **Complexity-based behavior**. The agent is instructed to operate in a specialized guidance-only mode if the underlying code is deemed too complicated. Here, the parent accessibility agent acts as an arbiter, while the reviewer agent is “opinionless” and just reports the findings as instructed.
* **Filtering**. The reviewer presents everything it finds. The parent accessibility agent then utilizes resources and skills to determine what is relevant to the request. The reviewer passing all its findings to the implementer would be costly and potentially set it on irrelevant and counter-productive tasks.
* **Traceability**. Direct communication between sub-agents would remove the ability to create and review an audit trail of user and agent decisions. This is important given the agent’s instruction around complex patterns, as well as the highly contextual nature of accessibility work.

### Execute instructions in a linear order

In addition to being a holistic concern, effective digital accessibility work also demands a methodical, detail-oriented approach.

The concern of using sub-agents to increase the speed of the LLM’s reply is counterbalanced by our need for its results to be accurate. We found that **compelling the agent to execute its sub-agent instructions in a fixed order** was key.

We first establish a parent set of ordered phases. Each phase itself contains child ordered steps of instructions, which are accompanied by relevant resources and skills:

![A diagram demonstrating how the research sub-agent uses ordered phases and ordered steps within each phase to produce structured output. The first phase is labeled, 'Phase 1 - Research', and contains 5 steps. The first step is labeled, 'WCAG SCs' and uses a skill called 'wcag-2.2-level-a-aa-success-criteria'. The second step is labeled, 'GitHub’s SC interpretation' and uses a skill called 'accessibility-check-wcag-sc-interpretation'. The third step is labeled, 'Assistive technology support' and uses a skill called 'accessibility-check-at-support'. The fourth step is labeled, 'Prior accessibility audits' and uses a skill called 'accessibility-search-prior-audits-general'. The fifth and final step for this phase is labeled, 'External W3C references' and is governed by a rule called 'Only if local searching is insufficient'. An arrow connects the first phase to the second phase, which is labeled, 'Phase 2 - Code audit'. The first step of phase 2 is labeled, 'Read source files on demand'. The second step is labeled, 'Incorporate user-provided URLs' and has a role called that compels it to always fetch. The third step is labeled, 'Investigate provided URLs’ links' and is governed by a rule called 'search 1 level deep'. The fourth step is labeled, 'Run validation skills' and uses a resource called 'decision table'. The fifth step is labeled, 'Cross-reference findings' and uses a skill called 'use phase 1 research'. The sixth and final step of this phase is labeled, 'Re-review all content interacted with'. An arrow connects the second phase to the third phase, which is labeled, 'Phase 3 - Structured output'. The third phase contains a single step labeled, 'Findings report, output-schema-reviewer'. It has three subsections, 'Summary', 'Finding severity scoring', and 'Each finding includes'. The summary subsection contains an ordered list that reads, '1. total findings', '2. prior audits', '3. escalation needed', '4. escalation scope', and '5. Escalated findings'. Finding and severity scoring has three levels, 'critical', 'warning', and 'info'. Each finding includes applicable WCAG SCs, applicable files and line numbers, current human-facing experience, expected human-facing experience, suggestion for remediation, and an escalation summary (if present).](<https://github.blog/wp-content/uploads/2026/05/image3.png?resize=1024%2C592>)

The interesting bit about this linear order is that it mirrors how I would personally approach performing auditing, remediating, and reporting duties.

### Use a template schema pass around sub-agent content

The entire operation of the sandboxed sub-agents is built around template schema files. These files **create consistency** that is vital to keeping the agent focused and on track.

The two schema templates are:

1. **Reviewer template schema:** This focuses on what to audit, and how to find applicable information about it.
2. **Implementer template schema:** This focuses on what to fix and how to fix it.

Without the schema files in place, the agents would all attempt to arbitrarily communicate with each other. This would create increased token expenditure, undesirable hallucinations, unnecessary code changes, and difficult-to-impossible behavior for agent auditing purposes.

## Acknowledging limitations

Another vital aspect of creating the accessibility agent is **understanding areas where agents can fall short**.

As the agent is not a turnkey “solution” for accessibility, we want to avoid situations where the agent’s output in error may not be sufficiently interrogated by the human using it. This is especially relevant when someone is not well-versed in digital accessibility considerations and practices.

Here’s what we did to accommodate the agent’s limitations:

### Evaluate code complexity

We want to avoid scenarios where we would need to perform costly and time-intensive work to revisit an inaccessible solution that the agent “thinks” is accessible.

To aid with this problem, the accessibility agent uses a small shell script to analyze the code it is set to work on. The script itself is simple, using a small set of basic heuristics to evaluate the relative complexity and distill it down into a score.

This score is then ingested by the agent. If the score passes a set threshold, the agent is instructed to **not** execute code changes. Instead, it will inform the person using the LLM that they should reach out to the accessibility team to consult on what they are attempting to do.

### Identify high-risk patterns

It is a subtle thing to understand, but know that **it is entirely possible to have code that passes automated accessibility checks, yet is functionally unusable**.

As a companion to code complexity, the accessibility agent is instructed to avoid attempting code generation for patterns the accessibility team has identified as high-risk. This includes, but is not limited to: drag and drop, [toasts](<https://primer.style/accessibility/patterns/accessible-notifications-and-messages/#toasts>), rich text editors, [tree views](<https://github.blog/engineering/user-experience/considerations-for-making-a-tree-view-component-accessible/>), and [data grids](<https://www.w3.org/WAI/ARIA/apg/patterns/grid/>).

These patterns require a ton of focused attention and detail and currently sit outside of an LLM’s current capabilities to produce in a way that actually works with assistive technology.

Not prohibiting high-risk patterns and high-complexity code environments would lead to unnecessary demands of everyone’s time to readdress, and also represents reputational risk for the accessibility team. We avoid this by shutting off the LLMs capability to go down this pathway.

### Reduce bias to action

I am loathe to anthropomorphize LLMs, but one quality they all seem to share is **desperately wanting to produce content**. For Copilot, that often means generating code.

We had to create [anti-gaming](<https://en.wiktionary.org/wiki/game_the_system>) instructions to prevent the LLM from creating sneaky ways to get around its instructions to not generate code when human expertise is needed. This prevented it from violating its own intervention instructions.

### Know that programmatically determinable issues don’t cover everything

Agent success metrics live within a larger context.

Of the 55 total WCAG level A and AA Success Criterion, only [35 of them can be detected via deterministic automated code checkers](<https://www.w3.org/WAI/standards-guidelines/act/rules/?requirements=a%2Caa&status=approved&implement=auto&display=no-rules>). This means that **~36% of level A and AA Success Criterion cannot be discovered automatically**.

![A pie chart titled, 'WCAG A and AA Success Criterion'. The first of two slices is labeled, '36% require manual evaluation'. The second of two slices is labeled, '64% can be detected automatically'.](<https://github.blog/wp-content/uploads/2026/05/image4.png?resize=729%2C807>)

LLM-powered agent operation is making inroads on this ~36% gap, but it is not a perfect science. Because of this, it becomes important to **manually identify accessibility barriers earlier during design and prototyping efforts**—[the area where the majority of accessibility issues originate](<https://www.deque.com/blog/auditing-design-systems-for-accessibility/#design-should-consider-accessibility>).

This thinking is also reflected in the agent’s escalation logic, in that members of the Accessibility team can pair with designers to help consider alternate approaches and brainstorm solutions that achieve business goals without compromising on accessibility.

This intervention and assistance is done to thwart potential downstream issues—and costly and time-consuming redesigns—are stopped before they ever have a chance to get off the ground.

### Manually evaluate agent output and adjust things that aren’t working as expected

We periodically perform manual review of agent output to determine its accuracy and efficacy. In addition, we have tooling in place to capture pull request reviewer sentiment. Both serve as **strong signals for areas where the agent needs better instruction**, as well as new resources and skills.

## Learning in the open

To recap, we learned that the agent is:

* Used to aid and augment existing accessibility efforts, not to replace them.
* Significantly more effective when trained on manually audited and remediated accessibility issues for your specific experience.
* Far more efficient with token consumption when utilizing sub-agents.
* More accurate and effective when executing instructions in a methodical, linear fashion.
* More consistent when set to use preformatted templates to pass information around.
* Set to understand its limitations and route people to alternative support systems.
* Improved when its output is periodically reviewed to identify areas it needs better instruction.

This journey is also not yet complete. The accessibility agent continues to be iterated upon in the hopes of **helping ensure GitHub is an accessible and inclusive platform for all developers**.

We hope that we can eventually open source the agent as part of [our pledge to help improve the accessibility of open source software at scale](<https://github.blog/open-source/social-impact/our-pledge-to-help-improve-the-accessibility-of-open-source-software-at-scale/>). Until then, we hope that in sharing our learnings with this undertaking that other teams can have a resource to reference for their own accessibility efforts.

---

## Tags:

* [accessibility](<https://github.blog/tag/accessibility/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)

## Written by

![Eric Bailey](<https://avatars.githubusercontent.com/u/634191?v=4&s=200>)

### [Eric Bailey](<https://github.blog/author/ericwbailey/>)

[@ericwbailey](<https://github.com/ericwbailey>)


</details>
