# 天机阁核心对象主干

状态：Accepted

生效日期：2026-07-29

## 一、目的

核心对象主干定义信息在天机阁中以什么语义存在、如何从用户意图逐步转化为可追溯情报，以及每类对象由哪个架构面拥有。

它解决的不是数据库表结构，而是以下架构问题：

- 十个一级面通过什么对象连接；
- 什么是输入、计划、执行记录、资料、证据、事实和研判；
- 哪些对象描述现实世界，哪些对象描述系统自身；
- 哪些转化可以确定性完成，哪些需要 Agent 判断或人类决定；
- 如何防止模型输出直接越级成为事实；
- 更正、争议和演进如何保留历史。

## 二、对象主干不是单向流水线

简化阅读路径是：

```text
Intent
→ Mission
→ Plan
→ Run / Task
→ Source / Document / Snapshot
→ Evidence / Claim / Fact
→ Entity / Relationship / Event / Change
→ Signal / Assessment / Forecast / Risk
→ Intelligence Product
→ Feedback / Evolution Evidence
```

正式模型是一张有类型、有反馈的对象图：

- 一个 Mission 可以产生多个 Plan 和多个 Run；
- 一个 Run 可以并行产生许多 Task；
- 一个 Fact 可以由多个 Claim 和 Evidence 支持；
- 一个 Claim 可以支持、反驳或修正另一个 Claim；
- Entity、Relationship、Event 和 Change 形成世界状态图；
- Assessment、Forecast 和 Risk 可以反向创建新的 Mission；
- Product 只是对象的交付视图，不吞并底层对象；
- Feedback 可以修正目标、事实、研判、产品或系统能力。

## 三、正式对象分类与八个家族

对象不再只按一条线性流水线分类。正式分类为：

```text
Core Object
First-class Supporting Record
Typed Relation / Value
View
Deferred
```

八个核心对象家族为：

| 家族 | 代表对象 |
|---|---|
| 资料与证据 | Source、Document、Snapshot、Evidence |
| 主张与事实 | Claim、Proposition、Fact |
| 实体与世界模型 | Mention、Entity、Relationship、Event、Change |
| 研判 | Signal、Assessment、Scenario、Forecast、Risk、Recommendation |
| 治理与执行 | Intent、Mission、Plan、Decision、Run、Task、Governance Case |
| 产品与告警 | Intelligence Product、Product Edition、Alert |
| 交互与反馈 | Feedback、Correction Request |
| 能力演进 | Evolution Evidence、Experiment、Capability Change Candidate |

Product Release、Epistemic Record、Semantic Edge Record、Acquisition Record、Verification Record、Acceptance Decision、Forecast Resolution / Score 等具有独立身份、生命周期和审计价值，但属于一等支撑记录，不与业务核心对象混写。

旧文档中的 `Disputed Claim` 是 Claim 的状态或查询视图；`World State` 是在 Authority Domain、作用域和时间截点下合成的 View；Diff 是比较记录，不自动等于 Change。

### 3.1 正式认识论主链

```text
Source
→ Document
→ immutable Snapshot
→ anchored Evidence
→ Claim + normalized Proposition
→ Verification Package
→ scoped Acceptance Decision
→ Fact Aggregate
→ Assessment / Scenario / Forecast / Risk
→ Product Edition
→ Product Release / Distribution / Delivery
```

Fact Aggregate 由 Proposition Revision、Acceptance Context、Acceptance Decision 和 Epistemic Record 历史构成。它只表示某 Authority Domain 在指定条件下接受某命题，不宣称无作用域的全局真理。

### 3.2 世界认知主链

```text
Snapshot / Evidence
→ Mention / Source-local Record
→ Entity Resolution Decision
→ Entity
→ Relationship / Event
→ World State View
→ typed Diff
→ accepted Change
```

Content Diff、Extraction Diff、Knowledge Revision 与 World Change 必须分离。世界变化必须经过 Evidence—Claim—Fact 接受链。

### 3.3 权威、版本与转换

对象身份、不可变 Revision、生命周期事件、Authority Domain 的 Epistemic Record 和查询 View 相互分离。关键状态转换必须经过 Transition Envelope，至少携带 expected revision、授权依据、主体、原因、幂等键和因果关联。

数据底座拥有 ID、Revision、Transition、血缘和审计的保管强制权，但不拥有 Fact 接受、Assessment 采纳或 Product 发布的语义决定权。

### 3.4 纠正与影响

Challenge、Correction、Supersession、Withdrawal、Retraction 和 Deletion 是不同语义。任何纠正都保留旧 Revision，通过固定版本、有类型的依赖边建立 Impact Case，并在授权作用域内重评下游对象。

### 3.5 多租户知识组合

```text
Public Baseline
+ Tenant Overlay
+ Mission Workspace
-- current authorization / purpose / jurisdiction filters
→ scoped knowledge view
```

任何覆盖都不改写公共对象，不使用 last-write-wins；历史回放也必须经过当前授权门。

## 四、原六组对象详解（兼容说明）

### 4.1 意图与治理对象

#### Intent

含义：人类或受认可系统主体表达的原始目标、问题、监测意图、纠正或决定。

主要来源：人机交互面。

必须保留：

- 表达主体；
- 原始表达；
- 时间；
- 租户与会话；
- 解释状态；
- 与后续 Mission 的关系。

Intent 不是可以直接执行的任务。

#### Mission

含义：经皇帝和治理体系解释后形成的稳定任务使命，声明“为什么做、期望得到什么、边界是什么”。

主要所有者：智能治理面。

必须包含：

- 目标和范围；
- 发起主体；
- 验收条件；
- 证据要求；
- 时间范围；
- 风险与预算上限；
- 优先级；
- 当前治理状态。

一个 Intent 可以形成多个 Mission；Mission 不能静默改变原始 Intent。

#### Plan

含义：完成 Mission 的版本化方案，说明需要哪些能力、数据、步骤、并行关系和审核闸门。

主要所有者：智能治理面。

Plan 是治理对象，不是实际执行状态。每个 Plan 必须关联版本、起草者、审议结果和所服务的 Mission。

#### Decision

含义：仙人、三省或其他有权主体对 Mission、Plan、事实争议、研判、发布或演进候选作出的结构化决定。

主要所有者：智能治理面。

Decision 必须记录决定者、依据、作用对象、决定类型、时间和后续效果，不能只存在于聊天文本。

### 4.2 执行对象

#### Run

含义：某个 Plan 或持续监测定义的一次具体执行实例。

主要所有者：智能执行与运行面。

Run 固定绑定：

- tenant_id；
- Mission 和 Plan 版本；
- Court Profile 版本；
- 执行上下文；
- 预算与风险上下文；
- 开始和终止状态。

#### Task

含义：Run 内可调度、可重试、可取消和可审计的工作单元。

主要所有者：智能执行与运行面。

Task 可以由 Agent、Tool、Worker、人类或外部服务执行，但执行者类型不改变 Task 的身份。

#### Execution Event

含义：Run、Task、Agent、Tool 或模型调用在运行期间产生的状态变化和行为记录。

主要所有者：智能执行与运行面，持久记录进入数据底座和审计体系。

典型事件包括创建、开始、暂停、重试、调用、失败、取消、降级、接管和完成。

#### Artifact

含义：执行产生但尚未被赋予事实或研判语义的中间产物，例如解析结果、OCR 文本、候选实体、模型草稿和临时计算结果。

Artifact 不能因为由高级模型产生就自动成为 Evidence、Fact 或 Assessment。

### 4.3 资料与认识论对象

#### Source

含义：资料来源或来源端点，例如网站、API、文件提供者、数据库、传感器或人类提交者。

Source 记录来源身份、获取方式、许可、信誉、时效和健康状态，不等于具体内容。

#### Document

含义：从 Source 获得的一个逻辑内容对象，例如网页、PDF、帖子、记录或上传文件。

Document 可以随时间变化，不应直接覆盖历史内容。

#### Snapshot

含义：Document 或外部记录在特定获取时间的不可变捕获版本。

Snapshot 至少关联：

- Source 和 Document；
- 获取时间；
- 内容摘要或对象引用；
- 获取方法和执行主体；
- 内容哈希；
- 许可与作用域；
- 前后版本关系。

#### Evidence

含义：从 Snapshot 或其他可审计材料中定位出来、能够支持或反驳某个 Claim 的证据单元。

Evidence 不是整份 Document，也不是结论。它必须能够回到具体原始材料、位置、时间和提取过程。

#### Claim

含义：某个主体对世界作出的可验证陈述。

Claim 必须区分：

- 陈述内容；
- 陈述主体；
- 所指对象；
- 有效时间；
- 支持和反对 Evidence；
- 验证状态；
- 租户或公共作用域。

模型抽取出的句子首先是 Claim 候选，不是 Fact。

#### Verification Record

含义：对 Claim 进行验证的过程和结果，记录规则、证据、验证者、反证、冲突和决定。

一个 Claim 可以拥有多次 Verification Record；新验证不能抹除旧验证历史。

#### Fact

含义：在明确作用域、有效时间和验证标准下，被系统接受为当前事实的结构化记录。

Fact 必须引用：

- 被接受的 Claim；
- 支持与反对 Evidence；
- Verification Record；
- 有效时间和记录时间；
- 验证等级；
- 当前状态。

Fact 不是永恒真理。它可以被争议、纠正、失效或新版本替代，但不能被直接改写而不保留历史。

#### Disputed Claim

含义：存在未解决冲突、证据不足或多方主张无法裁定的 Claim 视图。

争议不是错误状态。系统必须能保留多个相互冲突的 Claim，而不是强迫生成虚假唯一事实。

### 4.4 世界状态对象

#### Entity

含义：被持续识别和追踪的现实或概念主体，例如人物、机构、项目、产品、地点或政策。

Entity 身份来自消歧和证据，不等于某个来源中的名字字符串。

#### Relationship

含义：Entity 之间带类型、方向、有效时间、证据和置信度的关系。

#### Event

含义：在特定时间或时间范围内发生、涉及一个或多个 Entity 的结构化事件。

#### Change

含义：Document、Claim、Fact、Entity、Relationship、Event、观点或趋势在版本或时间上的有意义差异。

Change 必须引用前态、后态、检测方法、时间和重要性，不等于简单文本 Diff。

世界状态对象由数据认知面形成语义，由数据底座面持久保存和供应。

### 4.5 研判对象

#### Signal

含义：从事实、事件、变化或指标中检测到，值得进一步解释或监测的模式。

Signal 是研判入口，不是已经成立的趋势或风险。

#### Assessment

含义：在明确时间点，基于事实、关系、事件和变化形成的当前态势判断。

Assessment 必须保留：

- 结论；
- 输入事实和信号；
- 推理或分析方法；
- 支持、反对和缺失证据；
- 假设；
- 不确定性；
- 形成者和审核状态。

Assessment 不是 Fact。

#### Forecast

含义：面向未来的概率化判断，包括预测目标、时间范围、情景、关键变量、概率或区间及失效条件。

Forecast 必须能够在未来被评分、校准和复盘。

#### Risk

含义：某个已发生、正在形成或预测事件可能造成的负面结果，包含可能性、影响、范围、时限和触发条件。

Risk 可以引用 Assessment 和 Forecast，但不与二者合并。

#### Recommendation

含义：基于事实、研判和风险提出的建议性选择。

Recommendation 不等于 Decision，更不等于已经授权的 Action。

### 4.6 交付、反馈与演进对象

#### Intelligence Product

含义：为特定受众、目的、时间和渠道组织的情报交付物。

Product 可以引用 Fact、Assessment、Forecast、Risk 和 Recommendation，但不能复制后丢失其身份、证据和版本。

#### Alert

含义：当 Risk、Signal、Change 或监测规则满足触发条件时产生的可投递通知对象。

Alert 是交付和跟踪对象，不等于 Risk 本身。

#### Feedback

含义：用户、专家、系统评测或后续事实对 Intent、Mission、Fact、Assessment、Forecast、Risk 或 Product 提出的评价、纠正和结果反馈。

Feedback 必须声明作用对象，不能作为无归属的自由文本堆积。

#### Evolution Evidence

含义：用于证明能力缺口、退化、改进效果或时代变化的结构化证据，包括质量、成本、失败、用户反馈、评测和外部能力变化。

Evolution Evidence 可以触发能力演进候选，但不能直接修改生产系统。

#### Capability Change Candidate

含义：对模型、Agent、Tool、Workflow、Schema、组织或架构提出的版本化改进候选。

候选必须经过实验、评测、治理决定和平台发布，才成为新的期望状态。

## 五、十面对象所有权

| 一级面 | 拥有的核心语义或状态 | 主要读取 |
|---|---|---|
| 人机交互面 | Intent 的捕获、会话投影、用户 Feedback 入口 | Mission、Product、Alert、Decision |
| 情报产品面 | Intelligence Product、Alert 的组织、版本与分发 | Fact、Assessment、Forecast、Risk |
| 数据认知面 | Evidence、Claim、Entity、Relationship、Event、Change 的形成语义 | Source、Snapshot、Fact、领域规则 |
| 情报研判面 | Signal、Assessment、Forecast、Risk、Recommendation 的形成语义 | Fact、世界状态、领域知识 |
| 智能治理面 | Mission、Plan、Decision、审核与裁定语义 | Intent、执行状态、证据、研判 |
| 领域能力面 | 领域本体、领域规则及对象的领域解释 | 全部情报对象 |
| 智能执行与运行面 | Run、Task、Execution Event、临时 Artifact | Plan、控制面期望状态 |
| 数据底座面 | 全部持久对象的权威记录、版本、索引和血缘 | 各面的持久化请求 |
| 平台控制面 | Court Profile、Template、Policy、Catalog、Budget、Capability Version | 治理决定、运行实际状态 |
| 能力演进面 | Evolution Evidence、Experiment、Evaluation、Capability Change Candidate | 全系统质量、成本、失败与反馈 |

“形成语义”和“持久状态所有权”可以属于不同面。例如数据认知面决定一个 Change 的语义，数据底座面保存它的权威版本。

## 六、对象转化规则

### 6.1 转化必须显式

```text
Snapshot ──提取定位──▶ Evidence
Evidence ──形成陈述──▶ Claim
Claim ──验证与裁定──▶ Fact
Fact + 世界状态 ──综合──▶ Assessment
Assessment + 模型与假设 ──推演──▶ Forecast
Assessment / Forecast ──风险化──▶ Risk
Fact / Assessment / Forecast / Risk ──组织──▶ Intelligence Product
```

箭头代表创建新的有类型对象及其血缘，不代表把原对象原地改名。

### 6.2 每次转化至少记录

- 输入对象 ID 和版本；
- 输出对象 ID 和类型；
- 转化类型；
- 执行主体；
- 使用的 Agent、Tool、模型或规则版本；
- 参数、Prompt 或策略引用；
- 发生时间；
- 租户与作用域；
- 置信度或质量结果；
- trace_id 和 causation_id。

### 6.3 纠正与争议

纠正采用新版本、反驳关系、撤回或失效记录，不直接抹除历史。

```text
旧 Fact
├── superseded_by ──▶ 新 Fact
├── challenged_by ──▶ Disputed Claim
└── retracted_by ───▶ Retraction Decision
```

## 七、核心对象最小生命周期骨架

```text
Mission:
Draft → Proposed → Approved → Active → Completed / Cancelled / Superseded

Run:
Created → Scheduled → Running → Succeeded / Failed / Cancelled / Degraded

Claim:
Extracted → Under Verification → Verified / Disputed / Rejected / Superseded

Fact:
Active → Challenged → Confirmed / Superseded / Retracted / Expired

Assessment:
Draft → Under Review → Accepted → Superseded / Invalidated

Forecast:
Active → Updated → Expired → Scored → Archived

Risk:
Open → Monitoring → Escalated / Mitigated → Closed / Reopened

Product:
Draft → Reviewed → Published → Corrected / Withdrawn / Superseded

Capability Change Candidate:
Proposed → Experimenting → Evaluated → Approved / Rejected
→ Released → Observed → Retained / Rolled Back
```

这些只是骨架状态，详细转移条件将在状态机专题定义。

## 八、公共与租户作用域

每个持久对象必须显式声明作用域：

```text
Public
Tenant Private
User Private
Joint Investigation
Platform Internal
```

对象作用域不由存储位置隐式推断。公共 Evidence 可以被多个租户引用；租户私有 Assessment、Risk 和 Product 默认不因引用公共 Fact 而变为公共。

## 九、对象不变量

- Intent 不等于 Mission；
- Plan 不等于 Run；
- Artifact 不等于 Evidence；
- Document 不等于 Evidence；
- Evidence 不等于 Claim；
- Claim 不等于 Fact；
- Fact 不等于 Assessment；
- Assessment 不等于 Forecast；
- Forecast 不等于 Risk；
- Risk 不等于 Alert；
- Recommendation 不等于 Decision；
- Decision 不等于 Action；
- Intelligence Product 不拥有其引用事实和研判的唯一副本；
- Agent Memory 不等于任何权威情报对象存储；
- 模型输出不能越过验证转化直接写成 Fact；
- 所有派生对象必须能追溯输入、转化主体、版本和时间；
- 所有更正必须保留历史和传播关系；
- 事实、研判和预测必须具有明确的时间语义；
- 对象类型不能因展示方便而在产品层被模糊。

## 十、明确不在本阶段确定

- 每个对象的完整字段 Schema；
- ID 编码格式；
- 存储引擎和表结构；
- 证据等级与置信度计算公式；
- 每个状态转移的授权规则；
- 图谱本体的完整类型体系；
- 外部 Action 对象和补偿模型；
- 跨租户联合调查的详细作用域协议。

## 十一、关联文档

- [TXT-0006：核心对象流转图](../03-current-architecture/text-diagrams/TXT-0006-core-object-spine.md)
- [系统上下文与边界](../03-current-architecture/system-context.md)
- [整体架构骨架](../03-current-architecture/overall-architecture-skeleton.md)
- [ADR-0008：采用有类型、可追溯的核心对象主干](../06-architecture-decisions/adrs/ADR-0008-typed-traceable-core-object-spine.md)
