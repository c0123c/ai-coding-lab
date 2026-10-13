# USAspending 业务术语样例数据

保存支出分析示例自动获取的官方术语释义；属于业务数据附件，与 AI 编程方法关系较弱。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-09-20](<https://github.com/openai/openai-agents-python/commit/2f66876419fae7d9b0d431ed8829aae17727a3ed>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/daytona/usaspending_text2sql/schema/glossary.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/USAspending 业务术语样例数据.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 美国支出词汇表

来自 [USAspending.gov](<https://www.usaspending.gov>).
的官方定义由 setup_db.py 自动检索（149 术语）。

## 账户余额（文件A）

每个月结束后（或者在某些特定情况下每个季度），各机构都会向 USAspending 报告其财务系统中的余额，并标记为“文件 A”。  由于此数据基于国库账户 (TAS)，因此通常称为“账户数据”或“账户支出”。

**官方定义：** 帐户余额数据在文件 A 中报告，这是每个机构每月（或某些机构为季度）提交的财务数据中向 USAspending.gov 发布的三个文件之一。该文件源自该机构经过审计的财务系统，并根据政府财政账户符号调整试算表系统（GTAS）进行验证。文件 A 包含按国库账户符号 (TAS) 列出的总预算资源和总支出（包括债务和支出）的数据。它还提供与支出相关的相关预算功能。 
当您在网站上看到帐户余额（文件 A）的引用时，该引用是指包含所有机构文件 A 提交的数据集，而不是一个特定的机构文件。

## 按奖项划分的账户明细（文件 C）

按奖项划分的账户明细（文件 C）是每个机构每月（或某些机构为季度）提交的财务数据中向 USAspending.gov 发布的三个文件之一。该文件源自该机构经过审计的财务系统，仅包含奖励支出的数据（即不包括非奖励支出）。按奖项划分的帐户明细（文件 C）提供了每个奖项的时间、类型和获奖者等详细信息。 
当您在网站上看到对按奖项划分的帐户细分（文件 C）的引用时，该引用是指包含所有机构文件 C 而不是一个特定机构文件的数据集。

## 按计划活动和对象类别划分的帐户明细（文件 B）

按计划活动和对象类别划分的账户明细（文件 B）是每个机构每月（或某些机构为季度）提交的财务数据中向 USAspending.gov 发布的三个文件之一。该文件源自该机构经过审计的财务系统，包括按国库账户符号列出的总预算支出数据，包括债务和支出。与帐户余额（文件 A）一样，该文件提供与支出相关的相关预算功能。与帐户余额（文件 A）相比，该文件还包括相关的对象类和程序活动。 
当您在网站上看到按计划活动和对象类划分的帐户细分（文件 B）的引用时，该引用是指包含所有机构文件 B 而不是一个特定机构文件的数据集。

## 资产收购

此主要对象类别包括机构采购的资产，包括那些已经失去价值（折旧）的资产。根据这个定义，资产的一些例子包括设备、土地、实体结构、投资和贷款。

**官方定义：**该主要对象类涵盖对象类31.0到33.0。包括
资本化（折旧）资产和非资本化资产。其中包括：
31.0 设备
32.0 土地和建筑物
33.0 投资和贷款

每个特定的对象类在OMB循环A-11部分83.6中定义。

## 行动日期

政府发布或签署所报告的行动（针对主要奖励交易或子奖励）或达成具有约束力的协议的日期。由于奖励义务与行动日期相关，因此对 USAspending 支出数据的任何搜索都将按此数据元素而不是按绩效期间日期进行搜索。

## 动作类型

提供有关奖项更改类型的信息。例如，变更可能是对已完成项目的延续、修订、and/or 调整的结果。

**官方定义：**提供有关联邦主要奖励所做任何更改的信息的描述（和相应的代码）。每个奖项通常有多个行动。

（注：此定义包含财务援助的当前数据元素“行动类型”和采购的“修改原因”）

## 代理

在本网站上，我们使用“机构”一词来指任何联邦部门、委员会或其他美国政府实体。机构可以有多个子机构。例如，国家公园管理局是美国内政部的下属机构。

## 机构标识符

确定负责国库账户的机构。这是一个 3 位数字，是国库账户符号 (TAS) 的一部分。

**官方定义：**机构代码标识负责该账户的部门或机构。

## 分配转让机构（ATA）标识符

标识通过分配（非支出）转移接收资金的机构。这是一个 3 位数字，是国库账户符号 (TAS) 的一部分。

**官方定义：**拨款机构标识通过拨款（非支出）转移接收资金的部门或机构。

## 拨款

国会指定和批准用于特定目的（例如项目或计划）的支出的过程。大多数政府支出是通过每年的拨款法案确定的。这些法案必须由国会通过并由总统签署。

当国会在财政年度开始之前未通过拨款时，可能会颁布“持续决议”（通常称为“CR”）以避免政府关闭。 CR 是一项为各机构提供临时资金直至其常规拨款获得通过的法律。

## 拨款账户

当国会通过一项法律时，通常会授予一个机构执行项目的权力。当这种情况发生时，国会可能会为该项目拨出资金。拨款账户跟踪资金，就像银行账户一样。拨款账号（如银行账号）称为国库账户符号（TAS）。

**官方定义：**拨款的基本单位，通常反映拨款法案中的每个未编号的段落。拨款账户通常包含许多活动或项目，并且可能受到仅适用于该账户、拨款法案、拨款法案中的所有权、其他拨款法案或整个政府的限制或条件。

拨款账户由财政部与 OMB 协商创建的 TAFS 表示。

（在OMB循环A-11中定义）

## 援助清单（CFDA 计划）

援助列表以前称为“CFDA 计划”，提供了可供组织、政府机构（州、地方、部落）、美国领地以及被授权与政府开展业务的个人使用的联邦计划的完整列表。援助清单计划可以是项目、服务或活动。每个程序都有一个唯一的 5 位数字，格式为 XX.XXX。前两位数字代表资助机构。最后三位数字代表程序。

援助清单的示例包括：

* 社会保障退休保险（96.002）
* 医疗保险补充医疗保险（93.774）
* 补充营养援助计划 (10.551)
* 公路规划与建设（20.205）
* 全国学校午餐计划 (10.555)

**官方定义：** 分配给联邦国内援助目录 (CFDA) 和 SAM.gov 中的援助清单的编号。

联邦国内援助目录 (CFDA) 和 SAM.gov 中资助联邦补助的援助清单的标题。

## 可用性类型代码

在国库账户符号 (TAS) 中，这一单字母代码标识拨款账户上履行义务的可用性（或时间段）。如果无限期或无限期地承担新义务，TAS 将有一个“X”。

**官方定义：**在拨款账户中，可用性类型代码标识无限期承担新义务；这由字母 X 表示。

## 奖项

联邦政府承诺向受助人支付的款项。资金可以授予公司、组织、政府实体（即州、地方、部落、联邦或外国）或个人。它可以以合同、赠款、贷款、保险、直接付款等形式承担（承诺）义务。

## 奖励金额

联邦政府因签署合同、授予赠款等而承诺向（义务）支付接受者的金额。

**官方定义：** 联邦政府承担的奖励金额累计金额，由 USAspending.gov 计算。

对于除贷款之外的采购和财务援助奖励，这是联邦行动义务的总和。

对于贷款或贷款担保，这是原始补贴成本。

## 奖项ID

每个奖项都有一个唯一的识别号。

**官方定义：**所报告的特定奖励的唯一标识符，即用于财务援助的联邦奖励识别号（FAIN）和用于采购的采购工具标识符（PIID）。

## 奖项类型

联邦政府可以通过多种形式分配资金，包括合同、赠款、贷款、保险和直接付款。奖项类型是一种分类，提供有关奖项结构的更多信息。示例包括：

- 采购订单（合同的一种）
- 最终合同（合同的一种）
- 整笔拨款（拨款的一种）
- 直接贷款（贷款的一种）

**官方定义：**描述（和相应的代码）提供信息来区分合同、赠款或贷款的类型，并为用户提供更详细的结果交付方法。

## 颁奖机构

颁奖机构是颁发和管理奖项的机构。该机构通常从自己的预算中支付资金。在某些情况下，这笔钱由另一个称为资助机构的机构提供资金。

**官方定义：**国库账户基金符号(TAFS)中使用的与政府部门或机构相关的名称和代码。

## 颁奖办公室

颁发和管理奖项的机构内的办公室。

**官方定义：**授予、执行或以其他方式负责交易的n级组织的名称和标识符。

## 授予子机构

奖励子机构是颁发和管理奖项的子机构。例如，国税局 (IRS) 是财政部的下属机构。

**官方定义：**授予、执行或以其他方式负责交易的级别2组织的名称和标识符。

## 奖项数据（文件D）

奖项数据每天从政府系统中获取，各机构在这些系统中提交财务援助和采购数据。由于它包含两个独立的数据集，因此有时称为采购数据（文件 D1）和援助数据（文件 D2）。奖项数据与各机构每月或每季度向 USAspending.gov 提交的财务数据（包括文件 A、B 和 C 的提交）分开。文件 D1/D2 中的数据补充了按奖励划分的帐户细分（文件 C）中的奖励数据，以提供奖励支出的全貌。 
当您在网站上看到对文件 D 的引用时，它指的是所有机构的采购（文件 D1）和援助（文件 D2）数据集的最新集合，而不是某个特定机构的文件。

## 余额提前

往年未支出（承付或支出）但授权在本年度支出的资金。

**官方定义：**该元素的定义出现在6月发布的OMB通函A-11的附录F中2015；下面是 A-11 的简短摘要。对于未到期账户：上一年结转的未承付拨款余额或其他预算资源金额，无需国会采取新行动即可用于承付。对于过期账户：可用于上调债务的过期未承付余额金额。

## 基本交易行动日期

Prime 奖励摘要的原始 Prime 奖励交易的执行日期。请注意，该日期可能与绩效期间开始日期不同。由于奖励义务与行动日期相关，因此对 USAspending 支出数据的任何搜索都将按此数据元素而不是按绩效期间日期进行搜索。

## 基础交易说明

简要描述该奖项的目的。

**官方定义：**对于采购奖励：根据FPDS数据字典，简短、概括、简单的英语，对合同、奖励或修改的描述。附加信息：描述字段还可能包括缩写词、首字母缩略词或非简单英语的其他信息，例如 OMB 政策（CARES 法案等）所要求的信息。

对于经济援助奖励：联邦奖励目的的简单语言描述；将要进行的活动；可交付成果和预期成果；预期受益人；以及子受资助人活动（如果授予时 known/specified）。

## 基本订购协议（BOA）

基本订购协议 (BOA) 是一种无限期交付工具 (IDV)。它不是一份合同；它是一份合同。这是政府和承包商之间的书面谅解。它详细说明了所提供的用品或服务。它还详细说明了未来订单的定价和交付。

BOA 可以在需求不确定时加快签约速度。例如，当规格、数量和价格尚不清楚时。

这些协议还可以帮助政府实现零件订单的规模经济。对于承包商来说，他们可以缩短交货时间，实现更大的库存投资，并减少旧库存。

## 可用起始期限

确定拨款账户可能产生新债务的第一年。这仅适用于年度和多年期基金。这是代表年份的 4 位数字（例如 2017）。它是国库账户符号 (TAS) 的一部分。

**官方定义：**在年度和多年期基金中，可用起始期指根据法律规定拨款账户可能产生新义务的可用第一年。

## 一揽子购买协议 (BPA)

一揽子购买协议 (BPA) 是联邦机构用于重复购买用品或服务的一种方法。 BPA 是一种无限期交付工具 (IDV)，通过与受信任的供应商建立“收费账户”来运行。机构和供应商通常更喜欢 BPA，因为它们有助于加快重复购买的过程。一旦设置了BPA，双方都可以轻松重复购买。

BPA 是与单个机构签订的协议，这意味着只有少数办事处可以在 BPA 上下订单。可以将 BPA 授予一组供应商，然后他们将能够对即将到来的订单进行投标。 BPA 可以在有或没有总务管理局 (GSA) 时间表的情况下设置。如果没有 GSA 计划，订单的上限为 $100,000 的简化获取阈值 (SAT)。

BPA 示例：

- A 机构与计算机制造商建立了 BPA，用于重复购买笔记本电脑
- B机构与平面设计机构成立BPA，负责宣传册和活动标识的设计

## 整笔拨款

联邦政府向州和地方政府提供整笔拨款，用于广泛定义的目的，例如社会服务或社区发展。

**官方定义：** 整笔拨款主要根据法定公式给予通用政府单位。此类赠款可用于广泛职能领域内的各种活动。联邦整笔拨款计划的示例包括 1968 的综合犯罪控制和安全街道法案、1974 的住房和社区发展法案，以及根据《社会保障法》标题 XX 向各州提供社会服务的拨款。

## 预算局

只有国会依法授予联邦机构支出的权力，联邦机构才可以支出。这种支出许可称为“预算授权”。

预算权力可以通过拨款法授予，该法规定了目的（通常是最高金额）和设定的时间段。预算授权还可用于支出上一年未使用的资金，或支出该机构收取的资金（例如，国家公园管理局有权支出公园门票收取的费用，无论金额多少）。

**官方定义：**截至报告日期的所有债务预算授权总额，包括结转的未承付余额、结转的未承付余额调整、拨款金额和其他预算资源。

## 预算授权拨款

授权账户承担义务并为特定目的进行支出的法律条款（不一定在拨款法案中）。通常，但并非总是，拨款提供预算权力。

（在OMB循环A-11中定义）

## 预算功能

联邦预算大约分为 20 个类别，称为预算功能。这些类别根据支出的主要目的（例如国防、交通、健康）将联邦支出组织成主题。

这些进一步细分为预算子职能。

## 预算子职能

联邦预算分为职能和子职能。这些类别根据支出的主要目的将联邦支出组织成主题。主要职能大约有20个（例如国防、交通、卫生）。这些功能中的大多数进一步分为子功能。

例如，卫生预算职能分为卫生保健服务、卫生研究和培训以及消费者和职业健康与安全等子职能。

## 预算资源

预算资源是指在特定年份可用于承担债务的金额。预算资源包括新的预算授权（来自拨款、借款授权、合同授权或抵消收款）和前几年提供的预算授权的未承付余额。在本网站上，预算资源不包括融资账户，融资账户是一种用于资助联邦贷款的国库账户，根据管理和预算办公室 (OMB) 政策，不被视为支出。就 USASpending.gov 而言，“资金”代表“预算资源”。

预算资源包括政府账户之间的财政转移。金融转移是联邦政府账户之间的金融交换，不是商品和服务的交换。例如，在普通基金账户（例如向公路信托基金付款）和信托基金（例如公路信托基金）之间转移预算资源的支出转移被视为财务转移。  对于财政转移，预算资源显示在两个账户中。

## 克林格-科恩法案

1996 的克林格-科恩法案 (CCA) 是一项联邦法律，旨在改善联邦政府获取、使用和处置 IT 的方式。它努力使 IT 采购更具战略性。

**官方定义：**资金办公室证明信息技术采购满足40USC11312和40USC规划要求的代码11313。

## 建筑工资率要求

指示交易是否受建筑工资率要求的约束。该条款是52.222-6“建筑工资率要求”-与工资率要求（建筑业）（以前的《戴维斯-培根法案》）一致。

## 合同

联邦政府与主要接受者之间签订的有偿提供商品和服务的协议。

**官方定义：**合同是指一种相互约束的法律关系，卖方有义务提供物资或服务（包括建筑），买方有义务支付费用。它包括要求政府支出拨款资金的所有类型的承诺，除非另有授权，这些承诺都是书面的。除双边文书外，合同还包括（但不限于）授予和授予通知；根据基本订购协议发出的工作订单或任务函；信函合同；订单，例如采购订单，根据该订单，合同通过书面接受或履行而生效；以及双边合同修改。合同不包括 31 U.S.C. 涵盖的赠款和合作协议。 6301 等。

## 合同定价类型

合同的付款模式。每个都有不同的成本、费用和利润核算方法。合同定价类型包括：

- 固定价格重新确定
- 固定的努力价格水平
- 固定价格
- 固定价格与经济价格调整
- 固定价格激励
- 固定价格奖励费
- 成本加奖励费
- 成本 无费用
- 成本分摊
- 成本加成
- 固定费用
- 成本加奖励费
- 时间和材料
- 工时

**官方定义：**FAR部分16中定义的适用于本次采购的合同类型。

## 承包商

接受资金 and/or 的企业、组织或机构根据合同开展工作。承包商可以是公司、小型企业、大学、非营利组织、独资经营者或其他实体。当一家公司与美国政府签订合同时，他们可能会聘请另一家公司来完成部分工作。发生这种情况时，获得该奖项的公司称为主承包商。甲方聘请的公司称为分包商。

## 合同服务和供应

该主要对象类别包括为支持在指定合同期内履行政府活动而购买的服务或用品。一些例子包括政府人员和物资的运输、租金和其他公用事业、向 GSA 支付的租金、印刷和复制成本以及联邦设施的 operations/maintenance 成本。

这些项目并不等同于《联邦采购条例》(FAR) 联邦合同授予支出，也不符合 USAspending.gov 上的总合同授予支出。

**官方定义：**该主要对象类别涵盖通过26.0购买对象类别21.0中的合同服务和用品，包括：
21.0人员旅行和运输
22.0物品运输、租金、通信和运输公用事业
23 租金、通信和公用事业
23.1 向他人支付的租金GSA
23.2 向他人支付的租金
23.3 通信、公用事业和杂费
24.0 打印和复制
25 其他合同费用服务
25.1 咨询和协助服务
25.2 来自非联邦来源的其他服务
25.3 来自联邦来源的其他商品和服务
25.4 设施的运营和维护
25.5 研究和开发合同
25.6 医疗保健
25.7 运营和维护设备维护
25.8 人员的生活和支持
26.0 用品和材料

每个特定的对象类在OMB循环A-11部分83.6中定义。

## 合作协议

授予拨款以提供援助。它的特点是接受者和机构之间的广泛参与。它需要该机构的大量监督，并包括报告要求。

## 当前奖励金额

政府承诺（有义务）向合同接收者支付的金额。这意味着基本金额和任何已行使的期权。

**官方定义：**对于采购而言，合同中迄今为止所承担的总金额，包括基础金额和已行使的期权。

## 最终合同

最终合同是一种相互约束的法律关系，卖方有义务提供物资或服务（包括建筑），而买方则有义务支付费用。它包括政府有义务支出拨款的所有类型的承诺，除非另有授权，这些承诺都是书面的。除双边文书外，合同还包括（但不限于）授予和授予通知；根据基本订购协议发出的工作订单或任务函；信函合同；订单，例如采购订单，根据该订单，合同通过书面接受或履行而生效；以及双边合同修改。

## 交货订单合同

供应品（而非服务）的不定数量合同有时称为交货单合同。通过这种类型的合同，政府承诺在一段时间内从供应商那里购买物资。它设置的不是确切的数量，而是具有最小值和最大值的数量范围。

## 解除义务

取消或下调先前承担的资金。各机构取消资金义务，以减少奖励下的可用金额。  已解除债务的资金可在拨款可用期间重新用于债务。

## 直接贷款

直接贷款是指政府根据合同向非联邦借款人支付资金，并要求偿还此类资金（带息或不带息）。  该术语还包括某些提供信贷的同等交易。

## 直接付款

联邦政府向个人、私人公司或其他私人机构支付的现金。

## 指定用途直接付款

联邦政府直接向个人、私营公司和其他私人机构提供特定活动的财政援助。要获得此援助，接受者必须执行某些商定的活动并达到某些里程碑。直接付款不包括为政府采购商品和服务的招标合同。

**官方定义：** 包括联邦政府直接向个人、私营公司和其他私人机构提供的财政援助，以鼓励或补贴特定活动，并根据接受者的特定表现来确定接受援助的条件。

## 直接支付，使用无限制

联邦政府直接向符合某些联邦资格要求的受益人提供经济援助。这种类型的援助不会对受助人如何花钱施加任何限制。直接支付的一些例子包括退休金、养老金和补偿计划。

## 灾害应急基金代码（DEFC）

灾害应急基金代码（DEFC）用于跟踪灾害和紧急情况（例如COVID-19）的资金支出。每个代码都链接到一项或多项授权资助的立法法案。

**官方定义：** 管理和预算办公室 (OMB) 与财政部财政服务部合作，确定了一个政府范围内的国库账户符号调整试算表系统 (GTAS) 属性，称为“灾难应急基金代码 (DEFC)”来跟踪归类为灾难或紧急情况的拨款。本守则适用于预算资源、已发生的债务、未承付和承付余额以及这些拨款产生的支出。


根据备忘录 M-18-08 中的规定，为 DEFC 设置的域值是从“A”到“Z”的单个字母。所有未指定灾难或紧急情况的资金的默认域值为“Q”。 OMB 将集合中的新 DEFC 域值分配给每项已颁布的灾难或紧急资金拨款。每个 DEFC 域值的相应域名标题标识相关的公法编号以及资金是否属于灾难或紧急情况。


备忘录 M-20-21 修改了上述内容，允许各机构使用 DEFC 来满足 COVID-19 补充资金的报告要求，该要求需要跟踪未指定为紧急情况的资金。


各机构使用以下 DEFC 域值和标题来获取 COVID-19 补充资金：

- **DEFC ‘L’** 公法 116-123，指定为紧急情况
- **DEFC ‘M’** 公法 116-127，指定为紧急情况
- **DEFC ‘N’** 公法 116-136，指定为紧急情况
- **DEFC ‘O’** 公法 116-136、公法 116-139 和公法 116-260，未指定为紧急情况
- **DEFC ‘P’** 公法 116-139，指定为紧急状态
- **DEFC ‘U’** 公法 116-260，指定为紧急状态
- **DEFC ‘V’** 公法 117-2，美国救援计划法案 2021，未指定为紧急状态


请注意，国家利益行动 (NIA) 代码也用于跟踪 COVID-19 支出。然而，它仅适用于采购行动（即合同），不一定与 COVID-19 补充拨款挂钩。因此，具有 COVID-19 NIA 值的奖励可能没有 COVID-19 DEFC 值，反之亦然。

## DOD 索赔人程序代码

国防部 (DOD) 代码，指定一组物资、建筑或其他服务。每个代码都有字母和数字。

**官方定义：**索赔人计划编号指定一组供应品、建筑或其他服务。

## DUNS

DUNS 代表数据通用编号系统。它是 Dun & Bradstreet, Inc. 分配给公司或组织的唯一 9 数字标识号。需要在奖励管理系统 (SAM) 中注册 DUNS。组织必须在 SAM 注册（并获得 DUNS）才能与联邦政府开展业务。 Dun & Bradstreet 数据库中的每个营业地点都有一个单独的 DUNS 编号。 DUNS号码是随机的，具体数字没有意义。

**官方定义：**获奖者或获奖者的唯一标识号。目前，该标识符是 Dun & Bradstreet 分配的 9 数字，称为 DUNS® 号码。

## 可用期限结束

确定拨款账户可能产生新债务的最后一年。这仅适用于年度和多年期基金。这是代表年份的 4 位数字（例如 2018）。它是国库账户符号 (TAS) 的一部分。

**官方定义：**在年度和多年期基金中，可用期限是指根据法律规定，拨款账户可能产生新义务的资金可用的最后一年。

## 竞争程度

代表合同竞争性质的代码。值包括：

- A = 充分、公开的竞争（竞争性提案，不排除任何来源）
- B = 不可参加比赛
- C = 未参加比赛
- D = 排除来源后的充分、公开竞争
- E = 已完成行动的后续行动（现有已完成合同的后续行动）
- F = 在简化获取阈值下竞争 (SAP)
- G = 未在简化获取阈值下竞争 (SAP)

**官方定义：**代表合同竞争性质的代码。
[阅读联邦采购数据系统定义](<https://www.fpds.gov/help/Extent_Competed.htm>)。

## 贷款面值

贷款的面值是贷款总额，以及机构直接发放的金额（对于直接贷款）或在借款人违约时通过补偿贷方而促成的金额（对于贷款担保）。

由于贷款预计会被偿还，因此在预算方面，贷款的面值不被视为支出，也不包含在任何债务或支出数字中。然而，由于并非所有贷款都会得到偿还，因此政府确实需要付出成本。政府对这些成本的计算称为补贴成本。

**官方定义：**直接贷款或贷款担保的面值。

## FAIN

由机构分配给特定经济援助奖励的识别码，用于跟踪目的。 FAIN 在该奖项的整个生命周期内与该奖项（以及该奖项的所有未来修改）相关联。在一个机构内，FAIN 是独一无二的；新奖项必须颁发新的FAIN。 FAIN 代表联邦奖励识别号，尽管数字可以是字母和数字。

**官方定义：** 联邦奖励识别号 (FAIN) 是联邦机构内每个经济援助奖励的唯一 ID。

## 联邦账户

联邦账户是指按照给定的“联邦账户符号”分组的一组财政部支出账户。在此网站上，我们按机构标识符（3 数字代码）和主帐户代码（4 数字代码）对它们进行分组。

## 联邦行动义务

联邦政府对于奖励交易的义务、解除义务或责任的金额（以美元为单位）。

## 联邦供应计划 (FSS)

联邦供应计划 (FSS) 是已被 GSA 授予合同的承包商列表，可供所有联邦机构使用。这也称为多重奖励计划 (MAS)。

## 经济援助

直接帮助组织、个人或 state/local/tribal 政府的联邦计划、服务或活动。行业包括教育、卫生、公共安全和公共福利等。经济援助以多种形式分配，包括赠款、贷款、直接付款或保险。

## 财政年度（FY）

会计年度是跨 12 个月的会计期间。对于联邦政府来说，它的运行时间为 10 月 1 至 9 月 30。例如，财政年度 2017 (FY 2017) 从 10 月 1、2016 开始，到 9 月 30、2017 结束。
一个财政年度可能被分成四等分。对于联邦政府来说，这些季度是：

- Q1：十月 - 十二月
- Q2：一月至三月
- Q3：四月至六月
- Q4：七月 - 九月

## 公式补助金

依法向州（或其分支机构，包括县和地方政府等实体）进行的分配。这些赠款用于奖励不限于特定项目的持续活动——例如医疗补助。

**官方定义：**根据法律或行政法规对州（或其分支机构）进行的分配。这些赠款用于奖励不限于特定项目的持续活动。

## 资助机构

资助机构从其预算中支付大部分资助资金。通常，资助机构与授予机构相同。在某些情况下，一个机构将管理奖项（授予机构），另一机构将支付费用（资助机构）。

**官方定义：**为奖励提供大部分资金的政府部门或机构的名称和3数字CGAC机构代码and/or与奖励相关的个人交易。

## 资金义务

机构承诺支付的金额，通常是因为该机构已签署合同、授予赠款或订购商品或服务。

在奖励摘要页面的“财务系统详细信息”选项卡中，该金额是指机构财务系统中承担的资金。

**官方定义：**该元素的定义出现在6月发布的OMB通函A-11的20节中2015；下面是 A-11 的简短摘要。

义务是指具有约束力的协议，将立即或将来产生支出。在合法承担债务之前必须有预算资源。

## 资助办公室

机构内的办公室，从其预算中支付大部分奖励资金。

**官方定义：**提供本次交易所承担资金优势的n级组织的名称和标识符。

## 资助机会目标文本

与资助机会通知相关的预期结果的简要概述。

## 资助机会数量

联邦机构分配给其资助机会公告的字母数字标识符，作为发布在 OMB- 指定政府网站（目前为 grants.gov）上的资助机会通知的一部分，用于查找和申请联邦财政援助。

## 资助子机构

较大部门或机构的一个组成部分，从其预算中支付大部分奖励资金。也称为次级代理机构。例如，印第安事务局是内政部的一个下属机构。

**官方定义：**提供本次交易所需资金的主要资金的级别2组织的名称和标识符。

## 政府范围采购合同（GWAC）

政府范围采购合同 (GWAC) 是一份多机构合同。它为政府各机构提供信息技术 (IT) 服务。它是用于某些类型的 IT 工作的无限期交付工具 (IDV)：

- 系统设计
- 软件工程
- 信息保障
- 企业架构

供应商争夺初始合同。一旦被选中，他们就有资格进一步竞争机构特定的任务。

## 政府范围支出数据模型（GSDM）

政府范围支出数据模型 (GSDM) 以前称为 DATA 法案信息模型架构 (DAIMS)，是建立政府范围支出数据及其后续透明度发布数据标准的数据元素的权威来源。

**官方定义：**政府范围支出数据模型（GSDM），以前称为DATA法案信息模型架构（DAIMS），是根据2014数字问责和透明度法案创建的（DATA法案）。 GSDM 是数百个不同数据元素的术语、定义、格式和结构的权威来源，这些数据元素为支出数据及其后续发布的透明度建立了政府范围的数据标准。

管理和预算办公室 (OMB) 和财政部（财政部）收集了联邦机构的公众意见和反馈，并实施了敏捷开发方法来创建 DAIMS。最终定稿的DAIMS于四月首次发布2016。此后，财政部定期发布更新，以反映超出 DATA 法案范围的立法和政策。

11 月，2023、DAIMS 更名为 GSDM，以反映新立法和政策的纳入。 GSDM 包含的工件为联邦机构提供有关向财政部报告哪些数据的技术指导，包括数据元素的权威来源和提交格式。 GSDM 文档还为数据使用者提供信息和上下文，以更好地理解数据固有的复杂性。

## 格兰特

联邦机构向受助人提供的财政援助，用于执行美国法律授权的公共项目或服务。与贷款不同，赠款不需要偿还。大多数赠款都授予州和地方政府。在此网站上，您将看到多种类型的资助的参考，包括整笔资助、公式资助、项目资助和合作协议。

**官方定义：**为特定目的以现金或实物形式支付的联邦财政援助奖励。在进行预期活动时，预计联邦政府不会与州或地方政府或其他接受者进行实质性参与。 “赠款”一词使用广泛，可能包括向非政府接受者以及向州或地方政府提供的赠款，而“援助赠款”一词通常仅指向州或地方政府提供的赠款。 （有关更详细的说明，请参阅 1977、31 U.S.C. §§ 6301–6308 的联邦拨款和合作协议法案。）联邦援助拨款的两种主要形式是集体援助和分类援助。

## 补助金和固定费用

这一主要对象类别包括对外国的赠款、补贴和捐款；保险索赔；赔偿金（例如，向退伍军人支付死亡或伤残费用，或补偿财产损失）；利息和股息；并退款。

**官方定义：**该主要对象类涵盖对象类41.0到44.0。这包括：
41.0赠款、补贴和
捐款
42.0保险索赔和
赔偿
43.0利息和股息
44.0退款

每个特定的对象类在OMB循环A-11部分83.6中定义。

## 担保/保险贷款

贷款担保是指非联邦借款人向非联邦贷款人支付任何债务的全部或部分本金或利息的任何担保、保险或其他质押。  该术语不包括金融机构的存款、股票或其他可提取账户的保险。

## 高薪官员姓名

名字：被确定为薪酬最高的五位“高管”之一的个人的名字。 “高管”是指管理人员、管理合伙人或担任管理职位的任何其他员工。

中间名首字母：被确定为薪酬最高的五名“高管”之一的个人的中间名首字母。 “高管”是指管理人员、管理合伙人或担任管理职位的任何其他员工。

姓氏：被确定为薪酬最高的五位“高管”之一的个人的姓氏。 “高管”是指管理人员、管理合伙人或担任管理职位的任何其他员工。

## 高薪官员薪酬总额

获奖者上一财年中薪酬最高的五名“高管”之一所赚取的现金和非现金美元价值，包括以下内容（更多信息请参阅 17 C.F.R. § 229.402(c)(2)）：工资和奖金、股票奖励、股票期权和股票增值权、以下服务的收入非股权激励计划、养老金价值变化、不符合纳税资格的递延薪酬的高于市场收益以及其他薪酬。

## 无限期交货/确定数量合同

无限期交付合同 (IDC) 有助于在设定的时间范围内交付供应和服务订单。此类合同授予一个或多个供应商。

确定数量合同是 IDC 的一种，规定在固定期限内交付确定数量的物资或服务，并根据订单安排在指定地点进行交付。

## 无限期交货/无限数量 (IDIQ) 合同

不定数量合同是不定交货合同 (IDC) 的一种。有时，政府会签订合同，在一段时间内从供应商那里购买物资或服务。如果政府不知道所需的确切数量，则不定数量合同会设置一个具有最小值和最大值的数量范围。它没有指定确切的数字。对于服务，这通常称为任务订单合同。对于供应品，这通常称为交货订单合同。

## 无限期交付/需求合同

需求合同是为了在指定的合同期限内满足指定政府活动的所有物资或服务的采购要求，并通过向承包商下订单来安排交付。

## 无限期交货合同（IDC）

无限期交付合同 (IDC) 有助于在设定的时间范围内交付供应和服务订单。此类合同授予一个或多个供应商。

IDC 的类型包括：

- 无限期交货/确定数量合同
- 无限期交付/需求合同
- 无限期交货/无限数量 (IDIQ) 合同

## 无限期交付车辆（IDV）

车辆方便交付供应和服务订单。 IDV 类型包括：

- 一揽子购买协议 (BPA)
- 基本订购协议（BOA）
- 政府范围采购合同（GWAC）
- 多机构合同
- 无限期交货合同（IDC）
- 联邦供应计划 (FSS)

## 间接成本联邦份额金额

根据获奖者批准的资助预算，分配给间接成本的任何单一联邦资助行动的总金额。

## 保险

提供财政援助以确保补偿在特定条件下遭受的损失。承保范围可能由联邦政府直接提供或通过私人承运人提供，并且可能涉及也可能不涉及保费支付。请参阅联邦家庭援助目录 (CFDA)。

## 劳工标准

表明交易是否符合劳工标准。劳工标准条款是52.222-41“劳工标准” - 与服务合同劳工标准（以前的服务合同法）一致。

## 最近交易行动日期

Prime 奖励摘要的最新 Prime 奖励交易的执行日期。请注意，该日期可能与履行期结束日期（当前或潜在）不同。由于奖励义务与行动日期相关，因此对 USAspending 支出数据的任何搜索都将按此数据元素而不是按绩效期间日期进行搜索。

## 法人实体国家名称和代码

获奖者或接受者所在国家/地区的名称和代码，使用 ISO 3166-1 Alpha-3 GENC 配置文件，而不是为已标识为“州”的美国领土和属地列出的代码。

## 贷款

政府颁发的联邦奖励，借款人最终必须偿还。直接贷款是指在特定期限内提供的、具有合理还款预期的贷款；他们可能需要也可能不需要支付利息。担保贷款要求联邦政府向银行付款，并在借款人违约时接管贷款。

## 贷款补贴成本

当政府提供直接贷款或担保贷款时，它期望贷款得到偿还。然而，对于任何特定的贷款计划（例如学生贷款、小企业贷款担保），某些个人贷款不会得到偿还。补贴成本是政府根据贷款规模（即面值）、利率、全部或部分违约风险模型以及其他因素来估计政府可能承担的贷款成本的方法。补贴成本按贷款价值的百分比计算，不包括管理成本。

赠款或合同的奖励金额是接受者获得的金额，而对于贷款，奖励金额是补贴成本。这是因为补贴成本是政府的实际成本（估计）。贷款补贴成本对预算有直接影响，当为正数时，会计入债务和支出中。补贴成本可以是正数（表明政府可能在贷款中亏损）或负数（表明政府可能在贷款中赚钱）。正的贷款补贴成本通常小于相应的面值，但在某些边缘情况下，如果整个贷款被注销并且政府向银行支付发放贷款的费用（也包含在补贴成本中），则它可能会超过面值的 100%。运行贷款或贷款担保计划本身的管理成本不包括在贷款补贴成本计算中。

**官方定义：**政府直接贷款或贷款担保或其修改的估计长期成本，按净现值计算，不包括管理成本。

## 预留本地区域

在发生重大灾难或总统宣布紧急状态期间授予应急响应合同时，政府试图优先考虑当地公司。可以通过局部区域预留或评估偏好来给予优先权。

**官方定义：**在重大灾害期间或美国总统根据《罗伯特·T·斯塔福德救灾和紧急援助法案》（42 U.S.C. 5121等）的授权授予应急响应合同时，应优先考虑在可行和可行的范围内，向当地企业提供帮助。可以通过局部区域预留或评估偏好来给予优先权。注意：当参考 IDV 上数据元素“多重或单一奖励 IDV”的值为“单一”时，“预留本地区域”的值将从 BPA 传播。当值为“多个”时，需要用户输入。

## 主账户代码

这是一个 4 位数字，是财务账户符号 (TAS) 的一部分，用于标识 TAS 类型和用途。它不能为空。

**官方定义：**主账户代码在法规中标识账户。

## 材料、用品、物品和设备

表明交易是否受材料、供应品、物品和设备的约束。该条款是 52.222-20“超过 $15,000 的材料、供应品、物品和设备的合同” - 与超过 15,000 美元的材料、供应品、物品和设备的合同一起使用（以前称为 Walsh-Healey）。

## 修改编号

所报告的操作的标识符，指示初始奖励的具体后续更改。

## 多机构合同 (MAC)

多机构合同 (MAC) 是由一个机构建立的任务订单或交货订单合同，供政府机构用来获取物资和服务。

## 多项奖励时间表（MAS）

已被 GSA 授予合同的承包商列表，可供所有联邦机构使用。这也称为联邦供应计划 (FSS)。

## 多个收件人

收件人姓名“MULTIPLE RECIPIENTS”表示经济援助奖励已汇总以保护一组个人的个人身份信息 (PII)。禁止机构在 USAspending 上发布 PII。汇总涉及按县（对于国内奖项）、州（对于国内奖项）或国家（对于国外奖项）对个人（通常来自同一计划和时间段）的奖项进行分组。这些记录省略了通常存在的位置信息（街道地址和 ZIP 代码的最后 4 数字），并将收件人姓名替换为“MULTIPLE RECIPIENTS”。这些记录的奖励摘要页面指定了聚合级别。

## NAICS

NAICS 代表北美工业分类系统。这个 6 数字代码告诉您该作品属于哪个行业。每条合约记录都有一个NAICS代码。这意味着您可以查看美国政府在特定行业花费了多少钱。

行业和代码列表每 5 年更新一次。

**官方定义：** 代表北美工业分类系统代码的标识符和标题，分配给招标和由此产生的奖项，标识通常执行合同要求的行业

## 国家利益行动（NIA）

国家利益行动 (NIA) 代码对与紧急响应或其他国家重大事件相关的联邦合同进行分类。

**官方定义：**国家利益行动值用于对与紧急事故响应或其他国家重大事件相关的采购行动进行分类。值的长度不超过4个字符。创建了一个新的 NIA 值来应对 COVID-19 大流行，该值对于 3/13/2020 之间签署的操作有效9/30/2020。

以下是 NIA 值的示例：
 - H19M – 飓风迈克尔 2019
 - H19D – 飓风多里安 2019
 - P20C – COVID-19 2020

请注意，灾难应急基金代码 (DEFC) 也用于跟踪 COVID-19 支出。然而，它不限于合同，并且必然与 COVID-19 补充拨款挂钩。因此，具有 COVID-19 NIA 值的奖励可能没有 COVID-19 DEFC 值，反之亦然。

## 非联邦资助金额

由非联邦来源资助的奖励金额（以美元为单位）。计划收入（如 2 CFR § 200.1 中定义）在计划收入生成并记入协议之前不包括在内。

奖励义务和奖励支出金额（来自文件 C、D1 和 D2）仅计算联邦资金支出的美元，而不计算非联邦资金支出的任何美元。

## 项目等级

对象类是对联邦预算中的财务数据进行分类的一种方法。对象类按联邦政府购买的物品或服务的类型对义务进行分组。示例：“人员补偿”和“设备”

**官方定义：**分类系统中的类别，表示联邦政府购买的物品或服务的义务。每个特定的对象类在 OMB Circular A-11 § 83.6 中定义。

（在OMB循环A-11中定义）

## 义务

在授予资金时，美国政府会签订一项具有约束力的协议，称为义务。政府承诺立即或将来花掉这笔钱。例如，当一个机构下订单、签署合同、授予赠款、购买服务或采取其他需要其付款的行动时，它就承担了义务。

贷款补贴成本对预算有直接影响，当为正数时，会计入债务和支出中。

**官方定义：**义务是指具有法律约束力的协议，将立即或将来产生支出。当您下订单、签署合同、授予赠款、购买服务或采取其他行动要求政府向公众付款或从一个政府账户向另一个政府账户付款时，您就承担了义务。除非法律授权，否则在拨款之前让联邦政府参与合同或付款义务的行为违反了《反缺陷法》（31 U.S.C. § 1341(a)）。这意味着您不能在真空中承担义务；您对属于您机构的国库账户中的预算权力负有义务。承担金额超过国库账户可用金额的义务违反了《反赤字法》。这意味着该账户必须拥有足以支付债务发生时的此类债务总额的预算权限。此外，您承担的义务必须符合其他适用的法律规定，并且您必须能够支持 31 U.S.C. 要求的文件证据所报告的金额。 § 1501。此外，您还需要保留证明和记录，表明已承担该金额（31 U.S.C. § 1108）。以下小节提供了有关何时记录不同类型的商品和服务的义务或金额的额外指导。

 

通告 A-11 中提供了更多详细信息。

## 订购期结束日期

对于采购，对于所报告的行动所涉及的授予，不得下达与其相关的其他订单的日期。该日期仅适用于采购无限期交付车辆（例如无限期交付合同或一揽子采购协议）。在此日期之后，与该奖项相关的行政诉讼可能会继续发生。根据无限期交付工具发出的采购订单的履约结束日期可能会超出此日期。

## 其他预算资源

预算权力的一个子集。机构的大部分支出都是由拨款法授权的；一小部分可能来自上一年未花的钱。其余的以其他方式授权，并在 USAspending.gov 上作为其他预算资源进行分组。

**官方定义：**新的借款权、合同权和支出权，来自抵消国会在拨款法案或其他立法中提供的资金，或先前立法中提供的预算资源的未承付余额，以承担债务和支出。

（在OMB循环A-11中定义）

## 其他经济援助

联邦政府提供的财政援助未在任何先前定义的援助类型中描述。

## 其他项目类别

该主要对象类别包括其他杂项费用。

**官方定义：**该主要对象类涵盖对象类91.0到99.5。其中包括：
91.0 未凭证
92.0 未分配
94.0 财务转移
99.0 义务小计
99.5 四舍五入调整

每个特定的对象类在OMB循环A-11部分83.6中定义。

## 其他交易 (OT) 无限期交付车辆 (IDV)

其他交易 (OT) 无限期交付工具是除采购合同、赠款或合作协议之外的交易。由于该交易是负面定义的，因此它可以采取无限的潜在形式。该术语通常用于指代旨在以下目的的交易：

- 支持国土安全的研究与开发。
- 推进关键国土安全技术的开发、测试和部署。
- 加快解决国土安全漏洞的技术原型设计和部署。

国土安全部 (DHS) 经常将 OT 的使用分开用于研究和原型项目。

## 支出

当联邦资金实际支付，而不仅仅是承诺支付（“义务”）时，就会发生支出。

**官方定义：**为清算债务而支付的款项（债务本金的偿还或属于“融资手段”交易的其他支出除外）。支出通常等于现金支出，但也记录现金等价物交易，例如发行债券以支付保险索赔，并且在少数情况下按权责发生制记录，例如公共债务公共发行的利息。支出是政府支出的衡量标准。

（在OMB循环A-11中定义）

## 家长奖励识别号（ID）

颁发特定合同的采购合同的标识符，例如联邦供应计划。该数据元素目前仅适用于采购行动。

## 家长 DUNS

获奖者或接受者最终父母的唯一标识号。目前，该标识符是由 Dun & Bradstreet 作为全球母公司 DUNS® 号码维护的 9 位号码。

## 履行期限当前结束日期

裁决结束的日期，由有关各方商定，无需行使任何预先确定的延期选项。请注意，奖励的最新交易（称为“最新交易行动日期”）可能与此日期不同。

**官方定义：**对于采购奖励：根据合同中的时间表确定的合同完成日期。对于初始授予，这是基本合同和授予时行使的任何期权的预定完成日期。对于行使选择权或缩短（例如终止）或延长合同履约期限的修改，这是包括已行使选择权在内的基础合同的修订后的计划完成日期。如果中标仅用于购买要交付的物资，则完成日期应与基础合同和任何已行使期权上的最晚交付日期相对应。完成日期不会更改以反映结算日期。

对于赠款和合作协议：履行期在 CFR 200 中定义为初始联邦资助开始与计划结束日期之间的总估计时间间隔，其中可能包括一个或多个资助部分或预算周期。如果由于延期、终止、缺乏可用资金或其他原因而修改结束日期，则当前的结束日期将被修改。

对于所有其他经济援助奖励：对于所报告的行动所涉及的奖励，获奖者的努力完成或奖励以其他方式结束的当前日期。在此日期之后，与该奖项相关的行政诉讼可能会继续发生。

请注意，奖励的最新交易（称为“最新交易行动日期”）可能与履约期间当前结束日期不同。

## 绩效期间 潜在结束日期

裁决结束的日期，由有关各方在行使任何预先确定的延期选项后商定。请注意，奖励的最新交易（称为“最新交易行动日期”）可能与此日期不同。

与该奖项相关的行政诉讼可能会在绩效潜在结束日期之后继续发生。

履约期潜在结束日期不适用于可授予最终合同的无限期合同交付工具。

## 演出期间开始日期

裁决开始的日期，由有关各方商定。  请注意，奖励的第一笔交易（称为基本交易行动日期）可能与此日期不同。

**官方定义：**对于采购授予：根据FPDS数据字典，双方同意的日期将是合同要求的开始日期。这是整个合同期限的履约开始日期，该日期并不反映每次修改的履约期限，而是整个合同履约期限的开始日期。此数据元素 NOT 对应于 FAR 43.101 或 52.243，不应映射到合同书写系统中的这些字段。

对于赠款和合作协议：履行期在 2 CFR 200 中定义为初始联邦资助开始与计划结束日期之间的总估计时间间隔，其中可能包括一个或多个资助部分或预算期。

对于所有其他经济援助奖励：对于所报告的行动所涉及的奖励，受奖者开始努力或奖励生效的日期。

请注意，奖励的第一笔交易（称为基本交易行动日期）可能与绩效期开始日期不同。

## 人员薪酬和福利

该主要对象类别包括联邦雇员的雇员补偿，包括工资、工资和健康福利。人员薪酬和福利适用于全职和兼职员工以及军事人员。

**官方定义：**该主要对象类由对象类11、12和13组成。其中包括：
11人员报酬
11.1全职永久
11.3除全职
永久
11.5其他人员
报酬
11.6军事人员-
基本津贴
住房
11.7军人
11.8特殊个人服务
付款
11.9人员总数
补偿
12人员福利
12.1 文职人员
福利
12.2 军事人员
福利
13.0 前
人员的福利

每个特定的对象类在OMB循环A-11部分83.6中定义。

## 潜在奖励金额

合同中可能承担的总金额。该总额包括基本金额加上期权金额。例如，如果接收者在具有 3 个期权年的基本合同中获得 1000 万美元，每年 100 万美元，则潜在奖励金额为 1300 万美元。

**官方定义：**对于采购来说，如果基本和所有选择权均被行使，则合同中可能承担的总金额。

## 主要演出地点

主要营业地点，大部分工作在此进行。例如，在制造合同中，这将是生产物品的主要工厂。

**官方定义：**该奖项的主要表演将完成的地址。地址由四个部分组成：城市、州代码和 ZIP+4 或邮政编码。

## 主要表演地点国会区

主要营业地点所在的国会选区，大部分工作都在这里进行。例如，在制造合同中，这将是生产物品的主要工厂。

**官方定义：**该奖项主要表现所在的美国国会选区。该数据元素将从主要演出地点地址导出。

## 主要演出地点国家

主要营业地点所在的国家/地区，大部分工作都在该国家/地区进行。例如，在制造合同中，这将是生产物品的主要工厂。

**官方定义：** 奖项主要表现所在的国家/地区代码。

## 最高奖

主要奖励是政府为了执行联邦计划而与非联邦实体签订的协议。获得主要奖项的实体称为主要获奖者。

术语“主要奖励”可以用作描述交易或主要奖励摘要的通用术语。

**官方定义：** Prime 奖励是一种联邦奖励，可以是：
(1) 非联邦实体直接从联邦奖励机构获得的联邦财政援助；或
(2)非联邦实体直接从联邦资助机构收到的《联邦采购条例》下的费用报销合同。
（改编自2 CFR §200.38)

## 主要奖项摘要

主要奖励摘要包括共享相同主要奖励唯一密钥的所有相关主要奖励交易。 USAspending.gov 上的奖项简介页面允许用户浏览各个主要奖项摘要，包括构成主要奖项摘要的交易列表、由主要奖项摘要资助的子奖项列表以及为主要奖项摘要提供资金的联邦账户列表。

一般来说，来自最近的主要奖励交易的信息被应用于主要奖励摘要中的摘要级信息。例如，奖项的获奖者姓名、颁奖机构和摘要级别的绩效期限均取自该奖项的最新交易。

## 主要收件人

直接从美国政府获得资金的公司、组织、个人或政府实体（即州、地方、部落或外国）。他们通过一项称为“主要奖励”的协议获得这笔资金。例如，如果交通部正在修建一座桥梁，他们可以将施工合同授予桥梁公司 A。桥梁公司 A 将是主要受益者。

**官方定义：**直接从联邦奖励机构获得联邦奖励以开展联邦计划下的活动的非联邦实体。

## 采购仪器标识符（PIID）

分配给联邦合同、采购订单、基本订购协议、基本协议和一揽子采购协议的唯一标识符。它用于跟踪合同以及与其相关的任何修改或交易。

**官方定义：**所报告的特定奖项的唯一标识符。

[了解更多信息，请参阅《联邦采购条例》](<https://www.acquisition.gov/far/subpart-4.16>)。

## 产品或服务代码 (PSC)

产品或服务代码 (PSC) 是一个 4 字符代码，用于标识所购买的产品、服务或研发 (R&D) 的类型。虽然 NAICS 代码标识与合同最相关的行业，但 PSC 告诉您合同具体采购的是什么。例如，合同的 NAICS 代码可能指向“工业建筑施工”行业，而同一合同的 PSC 则指向“建设医院和医务室”。 PSC（超过 2,900）的数量几乎是 NAICS 代码（略高于 1000）的三倍，在许多情况下，对于给定合约，这允许比 NAICS 代码指定更细粒度的 PSC 指定。

所有 PSC 的长度均为 4 个字符，但代码中存在嵌入的层次结构。

- **R&D**：以“A”开头（表示 R&D），后跟第二个字母，后跟数字，再后跟数字（四级层次结构）。示例：AA11。

- **服务**：以“B”到“Z”开头（表示服务的子类别），后跟一个数字，再后跟两个字母（如果包含“服务”名称，则为四个层次结构）。示例：C1AA

- **产品**：以两个数字开头（表示产品的子类别），后跟另外两个数字（如果包含“产品”名称，则为三级层次结构）。示例：1005

**官方定义：**最能识别所采购的产品或服务的代码。代码在产品和服务代码手册中定义。

## 计划活动

计划活动是拨款账户中的一个类别。计划活动是美国政府年度预算的计划和融资计划中列出的特定活动或项目。

**官方定义：**美国政府年度预算的计划和融资计划中列出的特定活动或项目。

根据OMB通告A-11，活动应：
- 清楚地表明要提供的服务或要进行的项目；
- 资助不超过一个战略目标；
- 区分投资、开发、赠款和补贴以及运营计划；和
- 与机构的行政控制和运作有关。

## 程序、系统和设备代码

系统生成的国防部 (DOD) 代码，也称为采办计划 (AP) 代码。此代码标识正在获取的 DOD 程序、武器系统或设备。它可以归类为主要国防采办计划（MDAP）或主要自动化信息系统（MAIS）。

**官方定义：**两个代码共同识别DOD机构购买的程序和武器系统或设备。第一个字符是数字 1-4，标识 DOD 组件。最后的 3 字符标识该组件的程序、系统或设备。

[在总务管理局网站上了解有关此代码的更多信息](<https://www.fpds.gov/help/SystemEquipment.htm>)。

## 项目资助

在固定时间内为特定项目提供资金。一些例子包括研究金、奖学金、研究补助金、调查补助金和建设补助金。

**官方定义：**项目补助金为特定项目或特定服务或产品的交付提供固定或已知期限的联邦资金。

## 采购订单

采购订单是政府根据指定的条款和条件，使用简化的采购程序提出的购买物资或服务（包括建筑和研发）的要约。

## 修改原因

提供有关奖项更改类型的信息。

**官方定义：**提供有关联邦主要奖励所做任何更改的信息的描述（和相应的代码）。每个奖项通常有多个行动。

（注：此定义包含财务援助的当前数据元素“行动类型”和采购的“修改原因”）

## 收件人

接受美国政府资助的公司、组织、个人或政府实体（即州、地方、部落、联邦或外国）。

## 受援国国会选区

收件人所在的国会选区。

**官方定义：**获奖者或获奖者所在的国会选区。对于非美国地址，这不是必需的数据元素。

## 收件人位置

收件人的合法营业地址。

**官方定义：**获奖者或接受者的合法营业地址，其中唯一实体标识符（在奖励管理系统中注册）代表的办公室所在。在大多数情况下，如果需要，这应与该实体在其组织文件中向国家提交的内容相符。地址由五个部分组成：地址行 1 和 2、城市、州代码以及 ZIP+4 或邮政编码。

## 收件人姓名

接受者是指接受美国政府资助的公司、组织、个人或政府实体（即州、地方、部落、联邦或外国）。获奖者姓名与奖励管理系统（SAM.gov）中注册的姓名相同。这通常是企业的正式名称。对于个人，“多个收件人”一词用作收件人姓名，以保护个人隐私。

**官方定义：**与唯一标识符相关的获奖者或接受者的姓名。对于美国公司，该名称是企业通常在向各个州提交的成立文件中提交的名称（如果需要）。

## Recipient/Business 类型

Recipient/Business 类型是社会经济和其他 organizational/business 特征，用于对联邦承包商和其他资金接受者进行分类。有许多不同的 recipient/business 类型，涵盖营利性企业、非营利组织、政府实体、个人和外国实体。一些例子是：

- 历史上的黑人学院或大学
- 退伍军人拥有的企业
- 历史上未充分利用的商业区 (HUBZone) 公司
- 独资企业
- 基金会

您可以搜索和过滤此站点上的所有收件人类型。

**官方定义：**基于社会经济地位和组织/业务领域的不同类型接收者的指标集合。

## 记录类型

指示操作是聚合记录（记录类型 = 1）、非聚合记录（记录类型 = 2）还是具有已编辑个人身份信息的个人接收者的非聚合记录（记录类型 = 3）的代码。

## 由于 PII 已编辑

收件人姓名“REDACTED DUE TO PII”表示相关经济援助奖励发放给了根据法律要求其姓名和其他个人身份信息 (PII) 已被编辑的个人。除了用“REDACTED DUE TO PII”掩盖个人姓名之外，这些记录还忽略了原本存在的位置信息（街道地址和ZIP代码的最后4数字）。

## 预留类型

用于向特定类型的企业授予合同的工具。大多数企业都为小企业预留了储备合同。其他则更为具体，以支持具有特定名称的小型企业，例如退伍军人拥有的企业或小型弱势企业类型。

**官方定义：**为合约行为确定的撤销类型的指示符。

## 简化的采集程序 (SAP)

对于某些类型的政府采购，介于 $3,000 和 $150,000 之间。这些采购可能需要较少的批准和文件。

## 征集

当机构需要完成工作时，它可以要求提供有关工作的信息或投标。这些请求称为招揽。它们通常以 RFI（信息请求）或 RFP（提案请求）形式出现。

## 支出

在此网站上，“支出”一词可以描述义务（授予的金额）或支出（支付的金额）。

## 子账户代码

子账户代码 (SUB) 是 TAS 的组成部分，用于标识财政部定义的联邦账户细分 (AID + MAIN)。大多数联邦账户没有细分。 000为默认SUB；如果 000 是给定联邦账户下唯一的 SUB，则它尚未被细分

**官方定义：**这是TAS的一个组件。标识财政部定义的主账户细分。该字段不能为空。子账户000表示父账户。

## 子奖

子奖励是主要接受者与另一个实体签订的协议，以履行其奖励的一部分。在我们的网站上，这些收件人被称为子收件人。分授予也可能称为分包合同或分赠款。子奖励金额由主奖励义务和支出提供资金。理论上，任何给定主要奖项的所有子奖项金额的总价值是该主要奖项当前奖项金额的子集；子奖励金额一般不应超过其相关主奖励的当前奖励金额。为避免重复计算主要奖励的总价值，请勿将子奖励金额与主要奖励义务或支出相加。

**官方定义：**由传递实体向子受资助人提供的奖励，供子受资助人执行该传递实体收到的联邦奖励的一部分。  它不包括向承包商付款或向联邦计划受益人的个人付款。  子奖励可以通过任何形式的法律协议提供，包括转手实体视为合同的协议。 (2CFR)

## 子接收者

从另一个联邦资金接受者（主要接受者）而不是直接从美国政府获得资金的公司、组织、个人或政府实体（即州、地方、部落或外国）。次级接受者可以是分包商或次级受让人。例如，交通部授予A桥梁公司一份桥梁建设合同。 A 桥梁公司需要 B 桥梁公司供应钢材，因此 A 桥梁公司授予 B 桥梁公司一个分奖。  桥梁公司 B 是分包商。  在资助方面，A 大学获得美国国家科学基金会的研发资助。  A大学需要B大学进行研究的第一步，因此A大学授予B大学一个子奖。 B 大学是子受资助者。

**官方定义：**从传递实体获得子奖励以执行部分联邦计划的非联邦实体；但不包括此类计划受益人的个人。 （赠款.gov）

## 提交期限

提交期限显示联邦机构提交财务数据的时间。它显示为会计年度（例如，会计年度 2020 的“FY 2020”或“FY20”，涵盖 10 月 2019 到 9 月 2020），后跟一个月（例如， “P01”表示 10 月，即会计年度的第一个月）或季度（例如，“Q1”表示会计年度的第一季度，涵盖 10 月至 12 月）。例如，“FY19 P10”表示提交的数据涵盖 7 月 2019。

从 6 月 2020 报告期开始，大多数联邦机构开始按月向财政部 DATA 法案经纪人提交账户数据（文件 A、B 和 C），而不是按照之前的季度计划。自 10 月 2021 (FY22 Q1) 起，所有机构都必须按月报告。有关机构账户数据报告政策的更多信息，请参阅 OMB 的备忘录 M-20-21（附录 A，第 III 节）。

## 任务订单合同

服务（而非供应）的不定数量合同有时称为任务订单合同。通过这种类型的合同，政府承诺在一段时间内从供应商那里购买服务。它设置了一个具有最小值和最大值的范围，而不是确切的金额。

## 交易

交易可以是初始合同、赠款、贷款或保险奖励或对该奖励的任何修订或修改。

## 交易说明

交易目的的简要描述。

## 国库账户代码 (TAS)

财政部和 OMB 为每个拨款、收据或资金账户分配一个代码。此代码类似于银行帐号。它有助于识别联邦政府的金融交易。它还有助于报告的准确性。 TAS 在立法中有时被称为“程序源”。在此网站上，我们将共享机构标识符和主账户代码的每组国库账户分组为“联邦账户”。

TAS 由七个组件组成：

- 分配转让机构标识符（例如089）
- 机构标识符（例如 020）
- 可用开始期限（例如2017）
- 可用期结束（例如2018）
- 可用性类型代码（如果没有特定的 beginning/ending 年份则使用）（例如 X）
- 主帐户代码（例如0114）
- 子账户代码（例如000）

示例TAS：

- 089-020-2017/2018-0114-000
- 089-020-2017/2017-0114-000
- 089-020-X-0114-000

**官方定义：**国库账户符号：财政部为个人拨款、收据或其他资金账户分配的账户识别码。联邦政府的所有金融交易均按 TAS 分类，以便向财政部和管理与预算办公室报告。

（在OMB循环A-11中定义）

## 最终母公司法律实体名称

获奖者或接受者最终父母的姓名。

## 唯一实体标识符（UEI）

获奖者或接受者的唯一实体标识符 (UEI) 是在奖励管理系统 (SAM.gov) 中创建的字母数字代码，用于唯一标识注册与联邦政府开展业务的特定商业、非营利或商业实体。

## 无关联奖

有两个不同的数据集传输到 USAspending 以获得机构奖励 — 文件 C 和文件 D。文件 C 每月或每季度从经审计的机构财务系统提交并在网站上发布。文件 D1（采购）和文件 D2（财务援助）数据是根据各机构向其他系统提交的奖励报告数据生成的，并每天在 USAspending 上更新。  由于这些数据来自机构内的不同社区和系统，受到不同政策和报告要求的约束，因此每个数据集中捕获的奖项之间有时存在差距。
 
未链接的奖项缺乏允许财务系统数据和奖项报告数据之间匹配的共享奖项 ID。因此，此类奖项仅出现在网站的某些部分，并且缺少完整的上下文。例如，在文件 C 中找到但在文件 D 中未找到的奖项缺少收件人和 CFDA 计划信息，因此不会有奖项摘要页面。

## 非承付余额

账户中尚未授予或承担（承诺支出）的金额。

**官方定义：**未偿付余额是指在某一时点未到期账户中根据法律规定仍可用于偿付债务的预算授权的累计金额。 “仅可调整的过期余额”一词是指过期账户中的未承付金额。

 

通告 A-11 中提供了更多详细信息。

## 未报告的数据

机构未在特定时间报告财务或奖励数据或以其他方式向 USAspending.gov 提供财务或奖励数据的原因有多种。这些包括但不限于数据可用性的时间或无需提交的敏感数据。在可能的情况下，USAspending.gov 建议读者还存在其他无法详细说明的信息。

## URI

URI 代表唯一记录标识符。 URI 是机构定义的标识符，对于该机构报告的每项财务援助行动都是唯一的。 USAspending.gov 使用 URI 作为汇总记录的奖励 ID。


## 原文对照

<details>
<summary>展开完整原文</summary>

# USAspending Glossary

Official definitions from [USAspending.gov](<https://www.usaspending.gov>).
Retrieved automatically by setup_db.py (149 terms).

## Account Balance (File A)

After the end of every month (or in some select cases every quarter), agencies report the balances that are in their financial systems to USAspending in what is labeled “File A.”  Because this data is based on Treasury Accounts (TAS), it is often referred to as “Account Data” or “Account Spending.”

**Official definition:** Account Balance data is reported in File A, one of the three files that each agency publishes to USAspending.gov in its financial data submission each month (or quarter for some agencies). The file stems from the agency’s audited financial system and is validated against the Governmentwide Treasury Account Symbol Adjusted Trial Balance System (GTAS). File A includes data on total budgetary resources and total spending, including obligations and outlays, by Treasury Account Symbol (TAS). It also provides the relevant budget function associated with spending. 
When you see a reference to Account Balance (File A) on the site, the reference is to the dataset comprising all agency Files A submissions and not one specific agency file.

## Account Breakdown by Award (File C)

Account Breakdown by Award (File C) is one of the three files that each agency publishes to USAspending.gov in its financial data submission each month (or quarter for some agencies). The file stems from the agency’s audited financial system and includes data on award spending only (i.e., excludes non-award spending). Account Breakdown by Award (File C) provides details such as the timing, type, and recipient for each award. 
When you see a reference to Account Breakdown by Award (File C) on the site, the reference is to the dataset comprising all agency Files C and not one specific agency file.

## Account Breakdown by Program Activity & Object Class (File B)

Account Breakdown by Program Activity & Object Class (File B) is one of the three files that each agency publishes to USAspending.gov in its financial data submission each month (or quarter for some agencies). The file stems from the agency’s audited financial system and includes data on total budgetary spending, including obligations and outlays, by Treasury Account Symbol. Like Account Balances (File A), this file provides the relevant budget function associated with spending. In contrast with Account Balances (File A) this file also includes the relevant object class and program activity. 
When you see a reference to Account Breakdown by Program Activity & Object Class (File B) on the site, the reference is to the dataset comprising all agency Files B and not one specific agency file.

## Acquisition of Assets

This major object class includes an agency’s procurement of assets, including those that have lost value (depreciated). Some examples of assets, according to this definition, include equipment, land, physical structures, investments, and loans.

**Official definition:** This major object class covers object classes 31.0 through 33.0. Include
capitalized (depreciated) assets and non-capitalized assets. This includes:
31.0 Equipment
32.0 Land and structures
33.0 Investments and loans

Each specific object class is defined in OMB Circular A-11 Section 83.6.

## Action Date

The date the action being reported (for prime award transactions or sub-awards) was issued or signed by the Government, or a binding agreement was reached. Because award obligations are tied to action dates, any search for spending data on USAspending will search by this data element rather than by Period of Performance dates.

## Action Type

Provides information on the type of change made to an award. For example, the change may be the result of a continuation, revision, and/or adjustment to completed project.

**Official definition:** Description (and corresponding code) that provides information on any changes made to the Federal prime award. There are typically multiple actions for each award.

(Note: This definition encompasses current data elements ‘Type of Action’ for financial assistance and ‘Reason for Modification’ for procurement)

## Agency

On this website, we use the term agency to mean any federal department, commission, or other U.S. government entity. Agencies can have multiple sub-agencies. For example, the National Park Service is a sub-agency of the U.S. Department of the Interior.

## Agency Identifier

Identifies the agency responsible for a Treasury account. This is a 3-digit number that is a part of a Treasury Account Symbol (TAS).

**Official definition:** The agency code identifies the department or agency that is responsible for the account.

## Allocation Transfer Agency (ATA) Identifier

Identifies an agency that receives funds through an allocation (non-expenditure) transfer. This is a 3-digit number that is a part of a Treasury Account Symbol (TAS).

**Official definition:** The allocation agency identifies the department or agency that is receiving funds through an allocation (non-expenditure) transfer.

## Appropriation

The process by which Congress designates and approves spending for a specific purpose (e.g., a project or program). Most government spending is determined through appropriation bills each year. These bills must be passed by Congress and signed by the President. 

When an appropriation is not passed by Congress before the beginning of the fiscal year, a “continuing resolution” (often referred to as a “CR”) may be enacted to avoid a government shutdown. A CR is a law that provides stopgap funding for agencies until their regular appropriations are passed.

## Appropriation Account

When Congress passes a law, it often gives an agency authority to carry out a project. When this happens, Congress may set aside money for the project. An appropriation account tracks the money, much like a bank account. The appropriation account number (like a bank account number) is called a Treasury Account Symbol (TAS).

**Official definition:** The basic unit of an appropriation generally reflecting each unnumbered paragraph in an appropriation act. An appropriation account typically encompasses a number of activities or projects and may be subject to restrictions or conditions applicable to only the account, the appropriation act, titles within an appropriation act, other appropriation acts, or the Government as a whole.

An appropriations account is represented by a TAFS created by Treasury in consultation with OMB.

(defined in OMB Circular A-11)

## Assistance Listings (CFDA Program)

Assistance Listings, previously known as "CFDA programs", provide a full listing of federal programs that are available to organizations, government agencies (state, local, tribal), U.S. territories, and individuals who are authorized to do business with the government. An Assistance Listing program can be a project, service, or activity. Each program has a unique, 5-digit number in the form of XX.XXX. The first two digits represent the funding agency. The last three digits represent the program.

Examples of Assistance Listings include: 

* Social Security Retirement Insurance (96.002) 
* Medicare Supplementary Medical Insurance (93.774) 
* Supplemental Nutrition Assistance Program (10.551) 
* Highway Planning and Construction (20.205) 
* National School Lunch Program (10.555)

**Official definition:** The number assigned to an Assistance Listing in the Catalog of Federal Domestic Assistance (CFDA) and SAM.gov.

The title of the Assistance Listing under which the Federal award was funded in the Catalog of Federal Domestic Assistance (CFDA) and SAM.gov.

## Availability Type Code

Within a Treasury Account Symbol (TAS), this one-letter code Identifies the availability (or time period) for obligations to be made on the appropriation account. A TAS will have an “X” if there is an unlimited or indefinite period to incur new obligations.

**Official definition:** In appropriations accounts, the availability type code identifies an unlimited period to incur new obligations; this is denoted by the letter X.

## Award

Money the federal government has promised to pay a recipient. Funding may be awarded to a company, organization, government entity (i.e., state, local, tribal, federal, or foreign), or individual. It may be obligated (promised) in the form of a contract, grant, loan, insurance, direct payment, etc.

## Award Amount

The amount that the federal government has promised to pay (obligated) a recipient, because it has signed a contract, awarded a grant, etc.

**Official definition:** The cumulative amount obligated by the Federal Government for an award, which is calculated by USAspending.gov.

For procurement and financial assistance awards except loans, this is the sum of Federal Action Obligations.

For loans or loan guarantees, this is the Original Subsidy Cost.

## Award ID

A unique identification number for each individual award.

**Official definition:** The unique identifier of the specific award being reported, i.e. Federal Award Identification Number (FAIN) for financial assistance and Procurement Instrument Identifier (PIID) for procurement.

## Award Type

The federal government can distribute funding in several forms, including contracts, grants, loans, insurance, and direct payments. Award Type is a classification that provides more information about the structure of the award. Examples include:

- Purchase Order (a type of contract)
- Definitive Contract (a type of contract)
- Block Grant (a type of grant)
- Direct Loan (a type of loan)

**Official definition:** Description (and corresponding code) that provides information to distinguish type of contract, grant, or loan and providers the user with more granularity into the method of delivery of the outcomes.

## Awarding Agency

The Awarding Agency is the agency that issues and administers the award. This agency usually pays for the funding out of its own budget. In some cases, the money is financed by another agency, called the Funding Agency.

**Official definition:** The name and code associated with a department or establishment of the Government as used in the Treasury Account Fund Symbol (TAFS).

## Awarding Office

The office within an agency that issues and administers the award.

**Official definition:** Name and identifier of the level n organization that awarded, executed or is otherwise responsible for the transaction.

## Awarding Sub-Agency

The Awarding Sub Agency is the sub agency that issues and administers the award. For example, the Internal Revenue Service (IRS) is a sub agency of the Department of the Treasury.

**Official definition:** Name and identifier of the level 2 organization that awarded, executed or is otherwise responsible for the transaction.

## Awards Data (File D)

Awards Data is ingested up to daily from government-wide systems where agencies submit financial assistance and procurement data. Because it comprises two separate datasets, it is sometimes referred to as Procurement Data (File D1) and Assistance Data (File D2). Awards Data is separate from the financial data submissions that agencies publish to USAspending.gov each month or quarter (the submissions that include Files A, B, and C). Data from File D1/D2 supplements award data found in Account Breakdown by Award (File C) to provide a full picture of award spending. 
When you see a reference to File D on the site, it refers to the up-to-date set of all agencies’ procurement (File D1) and assistance (File D2) datasets and not one specific agency’s files.

## Balance Brought Forward

Funds that were not spent (obligated or outlaid) in previous years and are authorized to be spent in the current year.

**Official definition:** The definition for this element appears in Appendix F of OMB Circular A-11 issued June 2015; a brief summary from A-11 appears below. For unexpired accounts: Amount of unobligated balance of appropriations or other budgetary resources carried forward from the preceding year and available for obligation without new action by Congress. For expired accounts: Amount of expired unobligated balances available for upward adjustments of obligations.

## Base Transaction Action Date

The action date of the original Prime Award Transaction of a Prime Award Summary. Note that this date may be different from the Period of Performance Start Date. Because award obligations are tied to action dates, any search for spending data on USAspending will search by this data element rather than by Period of Performance dates.

## Base Transaction Description

A brief description of the purpose of the award.

**Official definition:** For procurement awards: Per the FPDS data dictionary, a brief, summary level, plain English, description of the contract, award, or modification. Additional information: the description field may also include abbreviations, acronyms, or other information that is not plain English such as that required by OMB policies (CARES Act, etc).

For financial assistance awards: A plain language description of the Federal award purpose; activities to be performed; deliverables and expected outcomes; intended beneficiary(ies); and subrecipient activities if known/specified at the time of award.

## Basic Ordering Agreement (BOA)

A Basic Ordering Agreement (BOA) is a type of Indefinite Delivery Vehicle (IDV). It is not a contract; it is a written understanding between government and contractor. It details the supplies or services offered. It also details pricing and delivery for future orders.

BOA's can speed up contracting when requirements are uncertain. For instance, when specifications, quantities, and prices are not yet known.

These agreements can also help the government achieve economies of scale for part orders. For the contractor, they can lessen lead-time, enable a larger inventory investment, and lessen old inventory.

## Beginning Period of Availability

Identifies the first year that an appropriation account may incur new obligations. This is for annual and multi-year funds only. This is a 4-digit number representing the year (e.g., 2017). It is a part of a Treasury Account Symbol (TAS).

**Official definition:** In annual and multi-year funds, the beginning period of availability identifies the first year of availability under law that an appropriation account may incur new obligations.

## Blanket Purchase Agreement (BPA)

A Blanket Purchase Agreement (BPA) is a method federal agencies use to make repeat purchases of supplies or services. A type of Indefinite Delivery Vehicle (IDV), a BPA operates by setting up a "charge account" with trusted vendors. Both agencies and vendors often prefer BPAs because they help speed up the process of repeated purchases. Once a BPA is set up, repeat purchases are easy for both sides.

A BPA is an agreement with an individual agency, meaning only a handful of offices can place orders on a BPA. A BPA can be awarded to a set of vendors, who will then be able to bid on upcoming orders. A BPA can be set up with or without General Services Administration (GSA) schedules. Without GSA schedules, orders are capped at the Simplified Acquisition Threshold (SAT) of $100,000.

Examples of BPAs:

- Agency A establishes a BPA with a computer manufacturer for repeat laptop purchases
- Agency B establishes a BPA with a graphic design agency for design of brochures and event signage

## Block Grant

Block grants are awarded by the federal government to state and local governments for broadly defined purposes — for example, social services or community development.

**Official definition:** Block grants are given primarily to general purpose governmental units in accordance with a statutory formula. Such grants can be used for a variety of activities within a broad functional area. Examples of federal block grant programs are the Omnibus Crime Control and Safe Streets Act of 1968, the Housing and Community Development Act of 1974, and the grants to states for social services under title XX of the Social Security Act.

## Budget Authority

A federal agency is only allowed to spend money if Congress provides the authority by law for that spending. That permission to spend is called “budget authority.”  

Budget authority can be granted through an appropriation law, which specifies a purpose, usually a maximum amount of money, and a set time period. Budget authority can also be granted for spending unused funds from a previous year, or to spend money that the agency takes in (e.g., the National Park Service is authorized to spend fees collected for park admission regardless of the amount).

**Official definition:** The total amount of all obligation budget authority including unobligated balances carried forward, adjustments to unobligated balances carried forward, appropriated amounts, and other budgetary resources, as of the reported date.

## Budget Authority Appropriated

A provision of law (not necessarily in an appropriations act) authorizing an account to incur obligations and to make outlays for a given purpose. Usually, but not always, an appropriation provides budget authority.

(defined in OMB Circular A-11)

## Budget Function

The federal budget is divided into approximately 20 categories, known as budget functions. These categories organize federal spending into topics based on the major purpose the spending serves (e.g., National Defense, Transportation, Health).

These are further broken down into budget sub functions.

## Budget Sub-Function

The federal budget is divided into functions and sub functions. These categories organize federal spending into topics based on the major purpose the spending serves. There are about 20 major functions (e.g., National Defense, Transportation, Health). Most of these functions are further divided into sub functions.

For example, the budget function for Health is divided into sub functions for Health care services, Health research and training, and Consumer and occupational health and safety.

## Budgetary Resources

Budgetary resources mean amounts available to incur obligations in a given year. Budgetary resources consist of new budget authority (from appropriations, borrowing authority, contract authority, or offsetting collections) and unobligated balances of budget authority provided in previous years. On this website, budgetary resources do not include financing accounts, which are a type of treasury account used to finance federal loans and are not considered spending per Office of Management and Budget (OMB) policy. For the purposes of USASpending.gov, “funding” represents “budgetary resources”. 

Budgetary resources include financial transfers between Government accounts. Financial transfers are financial interchanges between Federal Government accounts that are not an exchange for goods and services. For example, an expenditure transfer that shifts budgetary resources between a General Fund account, (e.g., Payment to Highway Trust Fund) and a trust fund (e.g., Highway Trust Fund) is considered a financial transfer.  For financial transfers, budgetary resources are shown in both accounts.

## Clinger-Cohen Act

The Clinger-Cohen Act (CCA) of 1996 is a federal law designed to improve the way the federal government acquires, uses, and disposes of IT. It strives to make IT purchases more strategic.

**Official definition:** A code indicating the funding office has certified that the information technology purchase meets the planning requirements in 40 USC 11312 and 40 USC 11313.

## Construction Wage Rate Requirements

Indicates whether the transaction is subject to the Construction Wage Rate Requirements. The clause is 52.222-6 "Construction Wage Rate Requirements" -that goes with Wage Rate Requirements (Construction) (formerly Davis-Bacon Act).

## Contract

An agreement between the federal government and a prime recipient to provide goods and services for a fee.

**Official definition:** Contract means a mutually binding legal relationship obligating the seller to furnish the supplies or services (including construction) and the buyer to pay for them. It includes all types of commitments that obligate the government to an expenditure of appropriated funds and that, except as otherwise authorized, are in writing. In addition to bilateral instruments, contracts include (but are not limited to) awards and notices of awards; job orders or task letters issued under basic ordering agreements; letter contracts; orders, such as purchase orders, under which the contract becomes effective by written acceptance or performance; and bilateral contract modifications. Contracts do not include grants and cooperative agreements covered by 31 U.S.C. 6301, et seq.

## Contract Pricing Type

Payment model for a contract. Each has a different way of accounting for costs, fees, and profits. Contract pricing types include:

- Fixed Price Redetermination 
- Fixed Price Level of Effort 
- Firm Fixed Price 
- Fixed Price with Economic Price Adjustment 
- Fixed Price Incentive 
- Fixed Price Award Fee 
- Cost Plus Award Fee 
- Cost No Fee 
- Cost Sharing 
- Cost Plus 
- Fixed Fee 
- Cost Plus Incentive Fee 
- Time and Materials 
- Labor Hours

**Official definition:** The type of contract as defined in FAR Part 16 that applies to this procurement.

## Contractor

A business, organization, or agency that receives funding and/or performs work on a contract. A contractor may be a corporation, small business, university, non-profit, sole proprietor, or other entity. When a company has a contract with the U.S. government, they may hire another company to perform part of the work. When this happens, the company who received the award is called the prime contractor. The company hired by the prime is called the sub-contractor.

## Contractual Services and Supplies

This major object class includes services or supplies purchased to support the fulfillment of government activities during a specified contract period. Some examples include transportation of government personnel and supplies, rent and other utilities, rental payments made to GSA, printing and reproduction costs, and operations/maintenance costs for federal facilities. 

These items are not equivalent to the Federal Acquisition Regulation (FAR) federal contract award spending and will not match total contract award spending on USAspending.gov.

**Official definition:** This major object class covers purchases of contractual services and supplies in object classes 21.0 through 26.0, including:
21.0 Travel and transportation of persons
22.0 Transportation of things, Rent, Communications, and Utilities
23 Rent, Communications, and Utilities
23.1 Rental payments to GSA
23.2 Rental payments to others
23.3 Communications, utilities, and miscellaneous charges
24.0 Printing and reproduction
25 Other contractual services
25.1 Advisory and assistance services
25.2 Other services from non-Federal sources
25.3 Other goods and services from Federal sources
25.4 Operation and maintenance of facilities
25.5 Research and development contracts
25.6 Medical care
25.7 Operation and maintenance of equipment
25.8 Subsistence and support of persons
26.0 Supplies and materials

Each specific object class is defined in OMB Circular A-11 Section 83.6.

## Cooperative Agreement

Grant awarded to provide assistance. It is characterized by extended involvement between recipient and agency. It requires substantial oversight by the agency, and includes reporting requirements.

## Current Award Amount

The amount of money that the government has promised (obligated) to pay a recipient for a contract. This means the base amount and any exercised options.

**Official definition:** For procurement, the total amount obligated to date on a contract, including the base and exercised options.

## Definitive Contract

A Definitive Contract is a mutually binding legal relationship obligating the seller to provide the supplies or services (including construction) and the buyer to pay for them. It includes all types of commitments that obligate the Government to an expenditure of appropriated funds and that, except as otherwise authorized, are in writing. In addition to bilateral instruments, contracts include (but are not limited to) awards and notices of awards; job orders, or task letters, issued under basic ordering agreements; letter contracts; orders, such as purchase orders, under which the contract becomes effective by written acceptance or performance; and bilateral contract modifications.

## Delivery Order Contract

An Indefinite Quantity Contract for supplies (not services) is sometimes referred to as a Delivery Order Contract. With this type of contract, the government promises to buy supplies over a period of time from a vendor. Instead of an exact amount, it sets a quantity range with a minimum and maximum.

## Deobligation

The cancellation or downward adjustment of previously obligated funds. Agencies deobligate funds to decrease the amount available under an award.  Deobligated funds may be reobligated within the period of availability of the appropriation.

## Direct Loan

Direct loan means a disbursement of funds by the Government to a non-Federal borrower under a contract that requires the repayment of such funds with or without interest.  The term also includes certain equivalent transactions that extend credit.

## Direct Payment

A cash payment made by the federal government to an individual, a private firm, or another private institution.

## Direct Payment for Specified Use

Financial assistance provided by the federal government directly to individuals, private firms, and other private institutions for a particular activity. To receive this assistance, the recipient must perform certain agreed-upon activities and meet certain milestones. Direct payments don’t include solicited contracts for the procurement of goods and services for the government.

**Official definition:** Includes financial assistance from the Federal government provided directly to individuals, private firms, and other private institutions to encourage or subsidize a particular activity by conditioning the receipt of the assistance on a particular performance by the recipient.

## Direct Payment with Unrestricted Use

Financial assistance provided by the federal government directly to beneficiaries who meet certain federal eligibility requirements. This type of assistance doesn’t place any restrictions on how the recipient spends the money. Some examples of direct payments include retirement, pension, and compensatory programs.

## Disaster Emergency Fund Code (DEFC)

Disaster Emergency Fund Code (DEFC) is used to track the spending of funding for disasters and emergencies such as COVID-19. Each code links to one or more legislative bills that authorized the funding.

**Official definition:** The Office of Management and Budget (OMB), working with the Department of Treasury’s Fiscal Service, has identified a Government-wide Treasury Account Symbol Adjusted Trial Balance System (GTAS) attribute called ‘Disaster Emergency Fund Code (DEFC)’ to track appropriations classified as disaster or emergency. This code applies to the budgetary resources, obligations incurred, unobligated and obligated balances, and outlays that result from these appropriations.


As established in Memorandum M-18-08,  the domain value set for DEFC is a single letter from ‘A’ to ‘Z’. The default domain value for all funding without disaster or emergency designation is ‘Q’. OMB assigns a new DEFC domain value from the set to each enacted appropriation with disaster or emergency funding. The corresponding domain title for each DEFC domain value identifies the associated public law number(s) and whether the funding is disaster or emergency.


Memorandum M-20-21 amended the above to allow agencies to use DEFC to meet reporting requirements for COVID-19 supplemental funding, which required tracking of funds not designated as emergency.


Agencies use the following DEFC domain values and titles for COVID-19 supplemental funding:

- **DEFC ‘L’**  Public Law 116-123, designated as emergency
- **DEFC ‘M’**  Public Law 116-127, designated as emergency
- **DEFC ‘N’**  Public Law 116-136, designated as emergency
- **DEFC ‘O’**  Public Law 116-136, Public Law 116-139, and Public Law 116-260, not designated as emergency
- **DEFC ‘P’**  Public Law 116-139, designated as emergency
- **DEFC ‘U’**  Public Law 116-260, designated as emergency
- **DEFC ‘V’**  Public Law 117-2, American Rescue Plan Act of 2021, not designated as emergency


Note that the National Interest Action (NIA) code is also used to track COVID-19 spending. However, it only applies to procurement actions (i.e., contracts) and is not necessarily tied to COVID-19 supplemental appropriations. Thus, awards with the COVID-19 NIA value may not have a COVID-19 DEFC value, and vice versa.

## DOD Claimant Program Code

Department of Defense (DOD) code that designates a grouping of supplies, construction, or other services. Each code has letters and numbers.

**Official definition:** A claimant program number designates a grouping of supplies, construction, or other services.

## DUNS

DUNS stands for Data Universal Numbering System. It is a unique 9-digit identification number assigned to a company or organization by Dun & Bradstreet, Inc. A DUNS is required to register in the System for Award Management (SAM). An organization must be registered in SAM (and obtain a DUNS) to do business with the federal government. There is a separate DUNS number for each business location in the Dun & Bradstreet database. The DUNS number is random, and specific digits have no significance.

**Official definition:** The unique identification number for an awardee or recipient. Currently the identifier is the 9-digit number assigned by Dun & Bradstreet referred to as the DUNS® number.

## Ending Period of Availability

Identifies the last year that an appropriation account may incur new obligations. This is for annual and multi-year funds only. This is a 4-digit number representing the year (e.g., 2018). It is a part of a Treasury Account Symbol (TAS).

**Official definition:** In annual and multi-year funds, the end period of availability identifies the last year of funds availability under law that an appropriation account may incur new obligations.

## Extent Competed

A code that represents the competitive nature of the contract. Values include:

- A = Full and open competition (competitive proposal, no sources excluded)
- B = Not available for competition
- C = Not competed
- D = Full and open competition after exclusion of sources
- E = Follow-on to competed action (a follow-on to an existing competed contract)
- F = Competed under Simplified Acquisition Threshold (SAP)
- G = Not competed under Simplified Acquisition Threshold (SAP)

**Official definition:** A code that represents the competitive nature of the contract.
[Read the Federal Procurement Data System definition](<https://www.fpds.gov/help/Extent_Competed.htm>).

## Face Value of Loan

Face value of a loan is the total amount of the loan, and the amount that agencies have directly issued (for direct loans) or facilitated by compensating the lender if the borrower defaults (for loan guarantees). 

Since loans are expected to be paid back, in budgetary terms, the face value of a loan is not considered spending and is not included in any obligation or outlay figure. However, because not all loans are repaid, they do have costs to the government. The government’s calculation of these costs is called subsidy cost.

**Official definition:** The face value of the direct loan or loan guarantee.

## FAIN

An identification code assigned to a specific financial assistance award by an agency for tracking purposes. The FAIN is tied to that award (and all future modifications to that award) throughout the award's life. Within an agency, FAINs are unique; a new award must be issued a new FAIN. FAIN stands for Federal Award Identification Number, though the digits may be both letters and numbers.

**Official definition:** The Federal Award Identification Number (FAIN) is the unique ID within the Federal agency for each financial assistance award.

## Federal Account

Federal Accounts refer to the set of Treasury spending accounts that are grouped under a given "Federal Account Symbol." On this website we group them by their agency identifier (3-digit code) and Main Account code (4-digit code).

## Federal Action Obligation

Amount of Federal Government’s obligation, de-obligation, or liability, in dollars, for an award transaction.

## Federal Supply Schedule (FSS)

The Federal Supply Schedule (FSS) is a listing of contractors that have been awarded a contract by GSA that can be used by all federal agencies. This is also known as a Multiple Award Schedule (MAS).

## Financial Assistance

A federal program, service, or activity that directly aids organizations, individuals, or state/local/tribal governments. Sectors include education, health, public safety and public welfare - to name a few. Financial assistance is distributed in many forms, including grants, loans, direct payments, or insurance.

## Fiscal Year (FY)

The fiscal year is an accounting period that spans 12 months. For the federal government, it runs from October 1 to September 30. For example, Fiscal Year 2017 (FY 2017) starts October 1, 2016 and ends September 30, 2017.
A fiscal year may be broken down into quarters. For the federal government, these quarters are:

- Q1: October - December 
- Q2: January - March 
- Q3: April - June
- Q4: July - September

## Formula Grant

An allocation made to states (or their subdivisions, which include county and local governments, among other entities) according to law. These grants are awarded for continuing activities that aren’t confined to a specific project — for example, Medicaid.

**Official definition:** Allocations made to states (or their subdivisions) according to law or administrative regulation. These grants are awarded for continuing activities that aren’t confined to a specific project.

## Funding Agency

A Funding Agency pays for the majority of funds for an award out of its budget. Typically, the Funding Agency is the same as the Awarding Agency. In some cases, one agency will administer an award (Awarding Agency) and another agency will pay for it (Funding Agency).

**Official definition:** Name and 3-digit CGAC agency code of the department or establishment of the Government that provided the preponderance of the funds for an award and/or individual transactions related to an award.

## Funding Obligated

The amount of money that an agency has promised to pay, usually because the agency has signed a contract, awarded a grant, or placed an order for goods or services. 

In the "Financial Systems Details" tab on an award summary page, this amount refers to the funding obligated in an agency's financial system.

**Official definition:** The definition for this element appears in Section 20 of OMB Circular A-11 issued June 2015; a brief summary from A-11 appears below. 

Obligation means a binding agreement that will result in outlays, immediately or in the future. Budgetary resources must be available before obligations can be incurred legally.

## Funding Office

The office within an agency that pays the majority of funds for an award out of its budget.

**Official definition:** Name and identifier of the level n organization that provided the preponderance of the funds obligated by this transaction.

## Funding Opportunity Goals Text

A brief summary of the intended outcomes associated with the notice of funding opportunity.

## Funding Opportunity Number

An alphanumeric identifier that a Federal agency assigns to its funding opportunity announcement as part of the Notice of Funding Opportunity posted on the OMB-designated government-wide web site (currently grants.gov) for finding and applying for Federal financial assistance.

## Funding Sub-Agency

A component of a larger department or agency that pays for the majority of funds for an award out of its budget. Also known as a sub-tier agency. For example, Bureau of Indian Affairs is a sub-agency of Department of Interior.

**Official definition:** Name and identifier of the level 2 organization that provided the preponderance of the funds obligated by this transaction.

## Government wide Acquisition Contract (GWAC)

Government-Wide Acquisition Contract (GWAC) is a multi-agency contract. It offers Information Technology (IT) services to agencies across the government. It is an Indefinite Delivery Vehicle (IDV) for certain types of IT work:

- Systems design
- Software engineering
- Information assurance
- Enterprise architecture

Vendors compete for the initial contracts. Once selected, they are eligible to compete further for agency-specific tasks.

## Governmentwide Spending Data Model (GSDM)

The Governmentwide Spending Data Model (GSDM), formerly called the DATA Act Information Model Schema (DAIMS), is the authoritative source for the data elements that establish government-wide data standards for spending data and their subsequent publication for transparency.

**Official definition:** The Governmentwide Spending Data Model (GSDM), formerly called the DATA Act Information Model Schema (DAIMS), was created as a result of the Digital Accountability and Transparency Act of 2014 (DATA Act). The GSDM is the authoritative source for the terms, definitions, formats and structures for hundreds of distinct data elements that establish government-wide data standards for spending data and their subsequent publication for transparency.

The Office of Management and Budget (OMB) and Department of the Treasury (Treasury) collected public input and feedback from federal agencies and implemented an agile development methodology to create the DAIMS. The finalized DAIMS first published in April 2016. Since then, Treasury has periodically published updates to reflect the inclusion of legislation and policies that go beyond the DATA Act. 

In November 2023, DAIMS was rebranded as the GSDM to reflect the inclusion of new legislation and policies. The GSDM includes artifacts that provide technical guidance for federal agencies about what data to report to Treasury including the authoritative sources of the data elements and the submission format. The GSDM documents also provide data consumers with information and context to better understand the inherent complexity of the data.

## Grant

An award of financial assistance from a federal agency to a recipient to carry out a public project or service authorized by a United States law. Unlike loans, grants do not need to be repaid. Most grants are awarded to state and local governments. On this site, you’ll see reference to several types of grants, including block grants, formula grants, project grants, and cooperative agreements.

**Official definition:** A federal financial assistance award making payment in cash or in kind for a specified purpose. The federal government is not expected to have substantial involvement with the state or local government or other recipient while the contemplated activity is being performed. The term “grant” is used broadly and may include a grant to nongovernmental recipients as well as one to a state or local government, while the term “grant-in-aid” is commonly used to refer only to a grant to a state or local government. (For a more detailed description, see the Federal Grant and Cooperative Agreement Act of 1977, 31 U.S.C. §§ 6301–6308.) The two major forms of federal grants-in-aid are block and categorical.

## Grants and Fixed Charges

This major object class includes grants, subsidies, and contributions to foreign countries; insurance claims; indemnities (for example, payments to veterans for death or disability, or to compensate for loss of property); interest and dividends; and refunds.

**Official definition:** This major object class covers object classes 41.0 through 44.0. This includes:
41.0 Grants, subsidies, and
contributions
42.0 Insurance claims and
indemnities
43.0 Interest and dividends
44.0 Refunds

Each specific object class is defined in OMB Circular A-11 Section 83.6.

## Guaranteed / Insured Loans

Loan guarantee means any guarantee, insurance, or other pledge with respect to the payment of all or a part of the principal or interest on any debt obligation of a non-Federal borrower to a non-Federal lender.  The term does not include the insurance of deposits, shares, or other withdrawable accounts in financial institutions.

## Highly Compensated Officer Name

First Name: The first name of an individual identified as one of the five most highly compensated “Executives.” “Executive” means officers, managing partners, or any other employees in management positions.

Middle Initial: The middle initial of an individual identified as one of the five most highly compensated “Executives.” “Executive” means officers, managing partners, or any other employees in management positions.

Last Name: The last name of an individual identified as one of the five most highly compensated “Executives.” “Executive” means officers, managing partners, or any other employees in management positions.

## Highly Compensated Officer Total Compensation

The cash and noncash dollar value earned by the one of the five most highly compensated “Executives” during the awardee's preceding fiscal year and includes the following (for more information see 17 C.F.R. § 229.402(c)(2)): salary and bonuses, awards of stock, stock options, and stock appreciation rights, earnings for services under non-equity incentive plans, change in pension value, above-market earnings on deferred compensation which is not tax qualified, and other compensation.

## Indefinite Delivery / Definite Quantity Contract

An indefinite delivery contract (IDC) facilitates the delivery of supply and service orders during a set timeframe. This type of contract is awarded to one or more vendors.

Definite Quantity Contracts, which are a type of IDC, provide for delivery of a definite quantity of supplies or services for a fixed period, with deliveries to be scheduled at designated locations upon order.

## Indefinite Delivery / Indefinite Quantity (IDIQ) Contract

An Indefinite Quantity Contract is a type of Indefinite Delivery Contract (IDC). Sometimes the government contracts to buy supplies or services from a vendor over a period of time. For instances that government does not know the exact quantity it will need, an Indefinite Quantity Contract sets a quantity range with a min and max. It does not specify an exact number. For services, this is often called a Task Order Contract. For supplies, this is often called a Delivery Order Contract.

## Indefinite Delivery / Requirements Contract

Requirements contracts are for the fulfillment of all purchase requirements of supplies or services for designated government activities during a specified contract period, with deliveries to be scheduled by placing orders with the contractor.

## Indefinite Delivery Contract (IDC)

Indefinite Delivery Contract (IDC) facilitates the delivery of supply and service orders during a set timeframe. This type of contract is awarded to one or more vendors. 

Types of IDC's Include:

- Indefinite Delivery / Definite Quantity Contract
- Indefinite Delivery / Requirements Contract
- Indefinite Delivery / Indefinite Quantity (IDIQ) Contract

## Indefinite Delivery Vehicle (IDV)

Vehicle to facilitate the delivery of supply and service orders. IDV Types include:

- Blanket Purchase Agreement (BPA)
- Basic Ordering Agreement (BOA)
- Government-Wide Acquisition Contract (GWAC)
- Multi-Agency Contract
- Indefinite Delivery Contract (IDC)
- Federal Supply Schedule (FSS)

## Indirect Cost Federal Share Amount

The total amount of any single Federal award action that is allocated, per the award recipient’s approved award budget, to indirect costs.

## Insurance

Financial assistance provided to assure reimbursement for losses sustained under specified conditions. Coverage may be provided directly by the Federal government or through private carriers and may or may not involve the payment of premiums. See Catalog for Federal Domestic Assistance (CFDA).

## Labor Standards

Indicates whether the transaction is subject to the Labor Standards. The clause for Labor Standards is 52.222-41 "Labor Standards" - that goes with the Service Contract Labor Standards (formerly Service Contract Act).

## Latest Transaction Action Date

The action date of the most recent Prime Award Transaction of a Prime Award Summary. Note that this date may be different from the Period of Performance End Date (Current or Potential). Because award obligations are tied to action dates, any search for spending data on USAspending will search by this data element rather than by Period of Performance dates.

## Legal Entity Country Name and Code

The Name and Code for the country in which the awardee or recipient is located, using the ISO 3166-1 Alpha-3 GENC Profile, and not the codes listed for those territories and possessions of the United States already identified as “states.”

## Loan

A federal award from the government that the borrower will eventually have to pay back. Direct loans are those made for a specific time period with a reasonable expectation of repayment; they may or may not require interest payments. Guaranteed loans require the federal government to pay the bank and take over the loan if the borrower defaults.

## Loan Subsidy Cost

When the government makes a direct loan or guarantees a loan, it expects the loan to be repaid. However, for any given loan program (e.g., student loans, small business loan guarantees) some individual loans are not repaid. Subsidy cost is the government’s way to estimate a loan’s likely cost to the government based on the size of the loan (i.e., its Face value), interest rate, the modeled risk of default in full or in part, and other factors. Subsidy cost is computed as a percentage of the loan value and does not include administrative costs.

While the award amount for a grant or contract is the amount that the recipient gets, for a loan, the award amount is the subsidy cost. This is because the subsidy cost is the actual cost to the government (estimated). Loan Subsidy Cost has a direct budgetary impact and is factored into obligations and outlays when it is positive. Subsidy costs can be positive (indicating that the government is likely to lose money on the loan) or negative (indicating that the government is likely to make money on the loan). A positive Loan Subsidy Cost is usually smaller than the corresponding Face Value, but in certain edge cases it can be over 100% of the face value if the entire loan is written off and the government paid fees to a bank to issue the loan (which are also included in the subsidy cost). Administrative costs of running the loan or loan guarantee program itself are excluded from Loan Subsidy Cost calculation.

**Official definition:** The estimated long-term cost to the Government of a direct loan or loan guarantee, or modification thereof, calculated on a net present value basis, excluding administrative costs.

## Local Area Set Aside

When awarding emergency response contracts during a major disaster or emergency declaration by the President, the government attempts to give preference to local firms. Preference may be given through a local area set-aside or an evaluation preference.

**Official definition:** When awarding emergency response contracts during the term of a major disaster or emergency declaration by the President of the United States under the authority of the Robert T. Stafford Disaster Relief and Emergency Assistance Act (42 U.S.C. 5121, et seq.), preference shall be given, to the extent feasible and practicable, to local firms. Preference may be given through a local area set-aside or an evaluation preference. Note: When the value for the data element 'Multiple or Single Award IDV' is 'Single' on the Referenced IDV, the value for 'Local Area Set Aside' is propagated from the BPA. When the value is 'Multiple' user input is required.

## Main Account Code

This is a 4-digit number that is part of a Treasury Account Symbol (TAS) and Identifies the TAS type and purpose. It cannot be blank.

**Official definition:** The main account code identifies the account in statute.

## Materials, Supplies, Articles & Equip

Indicates whether the transaction is subject to the Materials, Supplies, Articles, & Equip. The clause is 52.222-20 "Contracts for Materials, Supplies, Articles, and Equipment Exceeding $15,000" - that goes with Contracts for Materials, Supplies, Articles, and Equipment Exceeding $15,000 (formerly Walsh-Healey).

## Modification Number

The identifier of an action being reported that indicates the specific subsequent change to the initial award.

## Multi-Agency Contract (MAC)

A Multi-Agency Contract (MAC) is a task-order or delivery-order contract established by one agency for use by government agencies to obtain supplies and services.

## Multiple Award Schedule (MAS)

A listing of contractors that have been awarded a contract by GSA that can be used by all federal agencies. This is also known as a Federal Supply Schedule (FSS).

## Multiple Recipients

A recipient name of "MULTIPLE RECIPIENTS" indicates that the financial assistance award has been aggregated to protect the Personally Identifiable Information (PII) of a collection of individuals. Agencies are prohibited from publishing PII on USAspending. Aggregating involves grouping awards to individuals (typically from the same program and time period) by county (for domestic awards), state (for domestic awards), or country (for foreign awards). These records omit location information that would normally be present (street address and the last 4 digits of the ZIP code) and replace the recipient name with “MULTIPLE RECIPIENTS.” The award summary pages for these records specify the level of aggregation.

## NAICS

NAICS stands for the North American Industrial Classification System. This 6-digit code tells you what industry the work falls into. Each contract record has a NAICS code. That means you can look up how much money the U.S. government spent in a specific industry. 

The list of industries and codes is updated every 5 years.

**Official definition:** The identifier and title that represents the North American Industrial Classification System Code assigned to the solicitation and resulting award identifying the industry in which the contract requirements are normally performed

## National Interest Action (NIA)

The National Interest Action (NIA) code categorizes federal contracts that are related to emergency responses or other nationally significant events.

**Official definition:** The National Interest Action values are used to categorize procurement actions related to emergency contingency responses or other nationally significant events. The length of the value is no more than 4 characters. A new NIA value was created to address the COVID-19 pandemic and this value is valid for actions signed between 3/13/2020 and 9/30/2020.

Below are examples of NIA values:
 - H19M – Hurricane Michael 2019
 - H19D – Hurricane Dorian 2019
 - P20C – COVID-19 2020

Note that the Disaster Emergency Fund Code (DEFC) is also used to track COVID-19 spending. However, it is not limited to contracts and is necessarily tied to COVID-19 supplemental appropriations. Thus, awards with the COVID-19 NIA value may not have a COVID-19 DEFC value, and vice versa.

## Non-Federal Funding Amount

The amount of the award funded by non-Federal source(s), in dollars. Program Income (as defined in 2 CFR § 200.1) is not included until such time that Program Income is generated and credited to the agreement.

Award obligation and award outlay amounts (from Files C, D1, and D2) only count dollars spent from federal funding, not any dollars spent from non-federal funding.

## Object Class

Object class is one way to classify financial data in the federal budget. An object class groups obligations by the types of items or services purchased by the federal government. Examples: "Personnel Compensation" and "Equipment"

**Official definition:** Categories in a classification system that presents obligations by the items or services purchased by the Federal Government. Each specific object class is defined in OMB Circular A-11 § 83.6.

(defined in OMB Circular A-11)

## Obligation

When awarding funding, the U.S. government enters a binding agreement called an obligation. The government promises to spend the money, either immediately or in the future. An agency incurs an obligation, for example, when it places an order, signs a contract, awards a grant, purchases a service, or takes other actions that require it to make a payment.

Loan Subsidy Cost has a direct budgetary impact and is factored into obligations and outlays when it is positive.

**Official definition:** Obligation means a legally binding agreement that will result in outlays, immediately or in the future. When you place an order, sign a contract, award a grant, purchase a service, or take other actions that require the Government to make payments to the public or from one Government account to another, you incur an obligation. It is a violation of the Antideficiency Act (31 U.S.C. § 1341(a)) to involve the Federal Government in a contract or obligation for payment of money before an appropriation is made, unless authorized by law. This means you cannot incur obligations in a vacuum; you incur an obligation against budget authority in a Treasury account that belongs to your agency. It is a violation of the Antideficiency Act to incur an obligation in an amount greater than the amount available in the Treasury account that is available. This means that the account must have budget authority sufficient to cover the total of such obligations at the time the obligation is incurred. In addition, the obligation you incur must conform to other applicable provisions of law, and you must be able to support the amounts reported by the documentary evidence required by 31 U.S.C. § 1501. Moreover, you are required to maintain certifications and records showing that the amounts have been obligated (31 U.S.C. § 1108). The following subsections provide additional guidance on when to record obligations for the different types of goods and services or the amount.

 

Additional detail is provided in Circular A‐11.

## Ordering Period End Date

For procurement, the date on which, for the award referred to by the action being reported, no additional orders referring to it may be placed. This date applies only to procurement indefinite delivery vehicles (such as indefinite delivery contracts or blanket purchase agreements). Administrative actions related to this award may continue to occur after this date. The period of performance end dates for procurement orders issued under the indefinite delivery vehicle may extend beyond this date.

## Other Budgetary Resources

A subset of budget authority. Most spending by agencies is authorized by appropriation laws; a small amount may come from money not spent in the previous year. The rest is authorized in other ways and grouped together on USAspending.gov as Other Budgetary Resources.

**Official definition:** New borrowing authority, contract authority, and spending authority from offsetting collections provided by Congress in an appropriations act or other legislation, or unobligated balances of budgetary resources made available in previous legislation, to incur obligations and to make outlays.

(defined in OMB Circular A-11)

## Other Financial Assistance

Financial assistance from the Federal Government that is not described by any of the previously-defined assistance types.

## Other Object Class

This major object class includes other miscellaneous charges.

**Official definition:** This major object class covers object classes 91.0 through 99.5. This includes:
91.0 Unvouchered
92.0 Undistributed
94.0 Financial transfers
99.0 Subtotal, obligations
99.5 Adjustment for rounding

Each specific object class is defined in OMB Circular A-11 Section 83.6.

## Other Transaction (OT) Indefinite Delivery Vehicle (IDV)

An Other Transaction (OT) Indefinite Delivery Vehicle is a transaction other than a procurement contract, grant, or cooperative agreement. Since this transaction is defined in the negative, it could take unlimited potential forms. This term is often used to refer to transactions designed to:

- Support research & development for homeland security.
- Advance the development, testing, and deployment of critical homeland security technologies.
- Speed up prototyping and deployment of technologies addressing homeland security vulnerabilities.

The Department of Homeland Security (DHS) often splits its use of OT's for Research and Prototype Projects.

## Outlay

An outlay occurs when federal money is actually paid out, not just promised to be paid ("obligated").

**Official definition:** Payments made to liquidate an obligation (other than the repayment of debt principal or other disbursements that are “means of financing” transactions). Outlays generally are equal to cash disbursements but also are recorded for cash-equivalent transactions, such as the issuance of debentures to pay insurance claims, and in a few cases are recorded on an accrual basis such as interest on public issues of the public debt. Outlays are the measure of Government spending.

(defined in OMB Circular A-11)

## Parent Award Identification (ID) Number

The identifier of the procurement award under which the specific award is issued, such as a Federal Supply Schedule. This data element currently applies to procurement actions only.

## Parent DUNS

The unique identification number for the ultimate parent of an awardee or recipient. Currently the identifier is the 9-digit number maintained by Dun & Bradstreet as the global parent DUNS® number.

## Period of Performance Current End Date

The date that the award ends, as agreed upon by the parties involved without exercising any pre-determined extension options. Note that the latest transaction for the award (known as the Latest Transaction Action Date) may be different than this date.

**Official definition:** For procurement awards: The contract completion date based on the schedule in the contract. For an initial award, this is the scheduled completion date for the base contract and for any options exercised at time of award. For modifications that exercise options or that shorten (such as termination) or extend the contract period of performance, this is the revised scheduled completion date for the base contract including exercised options. If the award is solely for the purchase of supplies to be delivered, the completion date should correspond to the latest delivery date on the base contract and any exercised options. The completion date does not change to reflect a closeout date. 

For grants and cooperative agreements: The Period of Performance is defined in the CFR 200 as the total estimated time interval between the start of an initial Federal award and the planned end date, which may include one or more funded portions, or budget periods. If the end date is revised due to an extension, termination, lack of available funds, or other reason, the current end date will be amended.

For all other financial assistance awards: The current date on which, for the award referred to by the action being reported, awardee effort completes or the award is otherwise ended. Administrative actions related to this award may continue to occur after this date.

Note that the latest transaction for the award (known as the Latest Transaction Action Date) may be different than Period of Performance Current End Date.

## Period of Performance Potential End Date

The date that the award ends, as agreed upon by the parties involved after exercising any pre-determined extension options. Note that the latest transaction for the award (known as the Latest Transaction Action Date) may be different than this date.

Administrative actions related to this award may continue to occur after the Period of Performance Potential End Date.

The Period of Performance Potential End Date does not apply to Contract Indefinite Delivery Vehicles under which Definitive Contracts may be awarded.

## Period of Performance Start Date

The date that the award begins, as agreed upon by the parties involved.  Note that the first transaction for the award (known as the Base Transaction Action Date) may be different than this date.

**Official definition:** For procurement awards: Per the FPDS data dictionary, the date that the parties agree will be the starting date for the contract's requirements. This is the period of performance start date for the entire contract period, this date does not reflect period of performance per modification, but rather the start of the entire contract period of performance. This data element does NOT correspond to FAR 43.101 or 52.243 and should not be mapped to those fields in your contract writing systems. 

For grants and cooperative agreements: The Period of Performance is defined in the 2 CFR 200 as the total estimated time interval between the start of an initial Federal award and the planned end date, which may include one or more funded portions, or budget periods.

For all other financial assistance awards: The date on which, for the award referred to by the action being reported, awardee effort begins or the award is otherwise effective. 

Note that the first transaction for the award (known as the Base Transaction Action Date) may be different than the Period of Performance Start Date.

## Personnel Compensation and Benefits

This major object class includes employee compensation, including salaries, wages, and health benefits, for federal employees. Personnel compensation and benefits apply to full-time and part-time employees, along with military personnel.

**Official definition:** This major object class consists of object classes 11, 12, and 13. This includes:
11 Personnel compensation
11.1 Full-time permanent
11.3 Other than full-time
permanent
11.5 Other personnel
compensation
11.6 Military personnel -
basic allowance for
housing
11.7 Military personnel
11.8 Special personal services
payments
11.9 Total personnel
compensation
12 Personnel benefits
12.1 Civilian personnel
benefits
12.2 Military personnel
benefits
13.0 Benefits for former
personnel

Each specific object class is defined in OMB Circular A-11 Section 83.6.

## Potential Award Amount

The total amount that could be obligated on a contract. This total includes the base plus options amount. For example, if a recipient is awarded $10M on a base contract with 3 option years at $1M each, the potential award amount is $13M.

**Official definition:** For procurement, the total amount that could be obligated on a contract, if the base and all options are exercised.

## Primary Place of Performance

The principal place of business, where the majority of the work is performed. For example, in a manufacturing contract, this would be the main plant where items are produced.

**Official definition:** The address where the predominant performance of the award will be accomplished. The address is made up of four components: City, State Code, and ZIP+4 or Postal Code.

## Primary Place of Performance Congressional District

The congressional district where the principal place of business, where the majority of the work is performed. For example, in a manufacturing contract, this would be the main plant where items are produced.

**Official definition:** U.S. congressional district where the predominant performance of the award will be accomplished. This data element will be derived from the Primary Place of Performance Address.

## Primary Place of Performance Country

The country where the principal place of business, where the majority of the work is performed. For example, in a manufacturing contract, this would be the main plant where items are produced.

**Official definition:** Country code where the predominant performance of the award will be accomplished.

## Prime Award

A prime award is an agreement that the government makes with a non-federal entity for the purpose of carrying out a federal program. The entities receiving the prime award are known as prime recipients. 

The term “prime award” can be used as a generic term to describe either  transactions or prime award summaries.

**Official definition:** A Prime Award is a federal award that is either:
(1) Federal financial assistance that a non-Federal entity receives directly from a Federal awarding agency; or
(2) The cost-reimbursement contract under the Federal Acquisition Regulations that a non-Federal entity receives directly from a Federal awarding agency.
(Adapted from 2 CFR §200.38)

## Prime Award Summary

A prime award summary includes all related prime award transactions that share the same prime award unique key. Award Profile pages on USAspending.gov allow users to browse individual prime award summaries, including the list of transactions that constitute the prime award summary, the list of sub-awards funded by the prime award summary, and the list of federal accounts which have funded the prime award summary. 

Generally speaking, information from the most recent prime award transaction is applied to the summary-level information in the prime award summary. For example, the award’s recipient name, awarding agency, and period of performance at the summary level is drawn from the latest transaction of that award.

## Prime Recipient

A company, organization, individual, or government entity (i.e., state, local, tribal, or foreign) that receives funding directly from the U.S. government. They receive this funding through an agreement called a prime award. For example, if the Dept. of Transportation is building a bridge, they can award Bridge Company A the contract to carry out the construction. Bridge Company A would be the prime recipient.

**Official definition:** A non-Federal entity that receives a Federal award directly from a Federal awarding agency to carry out an activity under a Federal program.

## Procurement Instrument Identifier (PIID)

A unique identifier assigned to a federal contract, purchase order, basic ordering agreement, basic agreement, and blanket purchase agreement. It is used to track the contract and any modifications or transactions related to it.

**Official definition:** The unique identifier of the specific award being reported. 

[Read more in the Federal Acquisition Regulation](<https://www.acquisition.gov/far/subpart-4.16>).

## Product or Service Code (PSC)

A Product or Service Code (PSC) is a 4-character code that identifies the type of product, service, or research & development (R&D) purchased. While NAICS codes identify the industry most relevant to a contract, PSCs tell you what the contract is specifically purchasing. For example, a contract’s NAICS code might point to the “Industrial Building Construction” industry, while that same contract’s PSC points to “Construct Hospitals and Infirmaries.” There are nearly three times as many PSCs (over 2,900) as there are NAICS codes (just over 1000), which in many cases allows a more granular PSC designation than NAICS code designation for a given contract.

All PSC are 4 characters long, but there is an embedded hierarchy in the codes. 

- **R&D**: begin with ‘A’ (indicating R&D), followed by a second letter, followed by a number, followed by a number (four levels of hierarchy). Example: AA11.

- **Services**: begin with ‘B’ to ‘Z’ (indicating the subcategory of Service), followed by a number, followed by two letters (four levels of hierarchy if you include the “Service” designation). Example: C1AA

- **Products**: begin with two numbers (indicating the subcategory of Product), followed by two more numbers (three levels of hierarchy if you include the “Product” designation). Example: 1005

**Official definition:** The code that best identifies the product or service procured. Codes are defined in the Product and Service Codes Manual.

## Program Activity

A program activity is a category within an appropriation account. A program activity is a specific activity or project, as listed in the program and financing schedules of the annual budget of the U.S. government.

**Official definition:** A specific activity or project as listed in the program and financing schedules of the annual budget of the United States Government. 

According to OMB Circular A-11, The activities should:
- Clearly indicate the services to be performed or the programs to be conducted; 
- Finance no more than one strategic goal or objective; 
- Distinguish investment, developmental, grant and subsidy, and operating programs; and
- Relate to administrative control and operation of the agency.

## Program, System, and Equipment Code

A system-generated Department of Defense (DOD) code, also known as the Acquisition Program (AP) Code. This code identifies the DOD program, weapons system, or equipment being acquired. It can be categorized as a Major Defense Acquisition Program (MDAP) or a Major Automated Information System (MAIS).

**Official definition:** Two codes that together identify the program and weapons system or equipment purchased by a DOD agency. The first character is a number 1-4 that identifies the DOD component. The last 3 characters identify that  component's program, system, or equipment.

[Read more about this code](<https://www.fpds.gov/help/SystemEquipment.htm>) on the General Services Administration website.

## Project Grant

Funding of specific projects for a fixed amount of time. Some examples include fellowships, scholarships, research grants, survey grants, and construction grants.

**Official definition:** Project grants provide federal funding for fixed or known periods for specific projects or the delivery of specific services or products.

## Purchase Order

A Purchase Order is an offer by the government established to buy supplies or services, including construction and research and development, upon specified terms and conditions, using simplified acquisition procedures.

## Reason for Modification

Provides information on the type of change made to an award.

**Official definition:** Description (and corresponding code) that provides information on any changes made to the Federal prime award. There are typically multiple actions for each award.

(Note: This definition encompasses current data elements ‘Type of Action’ for financial assistance and ‘Reason for Modification’ for procurement)

## Recipient

A company, organization, individual, or government entity (i.e., state, local, tribal, federal, or foreign), that receives funding from the U.S. government.

## Recipient Congressional District

The congressional district in which the recipient is located.

**Official definition:** The congressional district in which the awardee or recipient is located. This is not a required data element for non-U.S. addresses.

## Recipient Location

Legal business address of the recipient.

**Official definition:** The awardee or recipient’s legal business address where the office represented by the Unique Entity Identifier (as registered in the System for Award Management) is located. In most cases, this should match what the entity has filed with the State in its organizational documents, if required. The address is made up of five components: Address Lines 1 and 2, City, State Code, and ZIP+4 or Postal Code.

## Recipient Name

A recipient is a company, organization, individual, or government entity (i.e., state, local, tribal, federal, or foreign), that received funding by the U.S. government. The recipient name is the same as what's registered in the System for Award Management (SAM.gov). This is usually the official name of the business. For individuals, the term 'Multiple Recipients' is used as the Recipient Name to protect individuals' privacy.

**Official definition:** The name of the awardee or recipient that relates to the unique identifier. For U.S. based companies, this name is what the business ordinarily files in formation documents with individual states (when required).

## Recipient/Business Types

Recipient/Business types are socio-economic and other organizational/business characteristics that are used to categorize federal contractors and other funding recipients. There are many different recipient/business types, and they span for-profit businesses, non-profits, government entities, individuals, and foreign entities. Some examples are:

- Historically Black College or University
- Veteran-Owned Business 
- Historically Underutilized Business Zone (HUBZone) Firm 
- Sole Proprietorship 
- Foundation

You can search and filter on all recipient types on this site.

**Official definition:** A collection of indicators of different types of recipients based on socio-economic status and organization / business areas.

## Record Type

Code indicating whether an action is an Aggregate Record (Record Type = 1), a Non-aggregate Record (Record Type = 2), or a Non-Aggregate Record to an Individual Recipient with Redacted Personally Identifiable Information (Record Type = 3).

## Redacted Due To PII

A recipient name of "REDACTED DUE TO PII" indicates that the associated financial assistance award was issued to an individual whose name and other Personally Identifiable Information (PII) were redacted, as required by law. Along with masking the individual’s name with “REDACTED DUE TO PII,” these records omit location information that would otherwise be present (street address and the last 4 digits of the ZIP code).

## Set Aside Type

A tool used to award contracts to specific types of businesses. Most set asides reserve contracts for small businesses. Others are more specific, to support small businesses with specific designations, such as veteran owned business or small disadvantaged business types.

**Official definition:** The designator for type of set aside determined for the contract action.

## Simplified Acquisition Procedures (SAP)

For certain types of government purchases between $3,000 and $150,000. These purchases may require less approval and less documentation.

## Solicitation

When an agency needs work done, it can ask for information or bids on the work. These requests are called solicitations. They often come as a RFI (Request for Information) or RFP (Request for Proposal).

## Spending

On this site, the term spending could either describe obligations (amount awarded) or outlays (amount paid out).

## Sub Account Code

Sub Account Code (SUB) is a component of the TAS that identifies a Treasury-defined subdivision of a Federal Account (AID + MAIN). Most Federal Accounts do not have subdivisions. 000 is the default SUB; if 000 is the only SUB under a given Federal Account, it has not been subdivided

**Official definition:** This is a component of the TAS. Identifies a Treasury-defined subdivision of the main account. This field cannot be blank. Sub Account 000 indicates the Parent account.

## Sub-Award

A sub-award is an agreement that a prime recipient makes with another entity to perform a portion of their award. On our website, these recipients are known as sub-recipients. Sub-awards might also be referred to as a sub-contract or a sub-grant. Sub-award amounts are funded by prime award obligations and outlays. In theory, the total value of all sub-award amounts for any given prime award is a subset of the Current Award Amount for that prime award; sub-award amounts generally should not exceed the Current Award Amount for their associated prime award. To avoid double-counting the overall value of a prime award, do not sum up sub-award amounts and prime award obligations or outlays.

**Official definition:** An award provided by a pass-through entity to a subrecipient for the subrecipient to carry out part of a federal award received by the pass-through entity.  It does not include payments to a contractor or payments to an individual that is a beneficiary of a federal program.  A subaward may be provided through any form of legal agreement, including an agreement that the pass-through entity considers a contract. (2CFR)

## Sub-Recipient

A company, organization, individual, or government entity (i.e., state, local, tribal, or foreign) that receives funding from another recipient of federal funds (a prime recipient), rather than directly from the U.S. government. The sub-recipient may be a sub-contractor or a sub-grantee. For example,  the Dept. of Transportation awards Bridge Company A a bridge construction contract. Bridge Company A needs Bridge Company B to supply the steel, so  Bridge Company A awards Bridge Company B a sub-award.  Bridge Company B is the sub-contractor.  On the grants side, University A receives an R&D grant from the National Science Foundation.  University A needs University B to perform the initial step in the research, so University A awards University B a sub-award. University B is the sub-grantee.

**Official definition:** A non-Federal entity that receives a sub-award from a pass-through entity to carry out part of a federal program; but does not include an individual that is the beneficiary of such program. (grants.gov)

## Submission Period

The submission period shows when federal agencies submit their financial data. It is displayed as a fiscal year (e.g., “FY 2020” or “FY20” for fiscal year 2020, covering October 2019 through September 2020) followed by a month (e.g., “P01” for October, which is the first month of the fiscal year) or quarter (e.g., “Q1” for the first quarter of the fiscal year, covering October through December). For example, “FY19 P10” indicates a submission whose data covers the period of July 2019.

Starting with the June 2020 reporting period, most federal agencies began submitting their account data (Files A, B, and C) to the Treasury DATA Act Broker on a monthly basis rather than on the previous quarterly schedule. As of October 2021 (FY22 Q1), all agencies are required to report on a monthly basis. More information about the agency account data reporting policy is found in OMB’s Memorandum M-20-21 (Appendix A, Section III).

## Task Order Contract

An Indefinite Quantity Contract for services (not supplies) is sometimes referred to as a Task Order Contract. With this type of contract, the government promises to buy services over a period of time from a vendor. Instead of an exact amount, it sets a range with a minimum and maximum.

## Transaction

A transaction can be the initial contract, grant, loan, or insurance award or any amendment or modification to that award.

## Transaction Description

A brief description of the purpose of the transaction.

## Treasury Account Symbol (TAS)

Treasury and OMB assign a code to each appropriation, receipt, or fund account. This code is similar to a bank account number. It helps identify financial transactions in the federal government. It also aids in reporting accuracy. TAS are sometimes referred as ‘program source’ in legislation. On this website, we group each set of Treasury Accounts that share an Agency Identifier and Main Account Code into a "Federal Account". 

Seven components make up the TAS:

- Allocation Transfer Agency Identifier (ex. 089)
- Agency Identifier (ex. 020)
- Beginning Period of Availability (ex. 2017)
- Ending Period of Availability (ex. 2018)
- Availability Type Code (used if there are not specific beginning/ending years) (ex. X)
- Main Account Code (ex. 0114)
- Sub Account Code (ex. 000)

Example TAS:

- 089-020-2017/2018-0114-000
- 089-020-2017/2017-0114-000
- 089-020-X-0114-000

**Official definition:** Treasury Account Symbol: The account identification codes assigned by the Department of the Treasury to individual appropriation, receipt, or other fund accounts. All financial transactions of the Federal Government are classified by TAS for reporting to the Department of the Treasury and the Office of Management and Budget.

(defined in OMB Circular A-11)

## Ultimate Parent Legal Entity Name

The name of the ultimate parent of the awardee or recipient.

## Unique Entity Identifier (UEI)

The Unique Entity Identifier (UEI) for an awardee or recipient is an alphanumeric code created in the System for Award Management (SAM.gov) that is used to uniquely identify specific commercial, nonprofit, or business entities registered to do business with the federal government.

## Unlinked Award

There are two distinct datasets transmitted to USAspending for agency awards—File C and Files D. File C is submitted and published on the site on a monthly or quarterly basis from audited agency financial systems. File D1 (procurement) and File D2 (financial assistance) data is generated from award reporting data submitted by agencies to other systems and updated on USAspending as frequently as daily.  Because these data originate from different communities and systems within agencies that are subject to different policies and reporting requirements, there are sometimes gaps between the awards captured in each dataset.
 
Unlinked awards lack a shared award ID that allows a match between financial system data and award reporting data. As a result, such awards only show up in some parts of the site and are missing their full context. For example, awards found in File C but not in File D lack recipient and CFDA Program information and thus, will not have an Award Summary page.

## Unobligated Balance

The amount of money out of an account that has yet to be awarded or obligated (promised to be spent).

**Official definition:** Unobligated balance means the cumulative amount of budget authority that remains available for obligation under law in unexpired accounts at a point in time. The term “expired balances available for adjustment only” refers to unobligated amounts in expired accounts.

 

Additional detail is provided in Circular A‐11.

## Unreported Data

There are various reasons financial or award data is not reported by agencies or otherwise available to USAspending.gov at a given time. These include, but are not limited to, timing of data availability, or sensitive data that is not subject to submission. Where possible, USAspending.gov advises readers that other information exists that cannot be detailed.

## URI

URI stands for Unique Record Identifier. A URI is an agency-defined identifier that is unique for every financial assistance action reported by that agency. USAspending.gov uses URI as the Award ID for aggregate records.


</details>
