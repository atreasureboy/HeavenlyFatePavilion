# 对象权威、生命周期、血缘与不变量统一模型

状态：Proposed

形成日期：2026-07-29

所属迭代：`v0002-intelligence-semantics / S9`

## 一、目的

S1—S8 已经建立资料、证据、事实、实体、变化、研判、产品、时间和知识覆盖语义。S9 不再横向增加业务能力，而是回答五个收敛问题：

1. 哪些概念属于核心对象，哪些只是支撑记录、关系、状态或视图；
2. 谁可以创建候选，谁可以接受、发布、撤回或纠正；
3. 状态转换怎样在 Agent、Tool、数据底座和治理主体之间安全发生；
4. 血缘边怎样表达来源、推导、论证、治理、分发和因果，而不退化成 `related_to`；
5. 哪些规则必须成为跨对象、跨面且不可绕过的不变量。

如果缺少这一层，系统即使拥有精细对象名，也仍会出现：

- 采集 Agent 直接把 Snapshot 写成 Fact；
- 数据库管理员因为能改记录而取得事实撤回权；
- 产品生成 Agent 自己审核、批准并发布；
- 模型创建一条 `supports` 边就把自己的结论变成有证据；
- 状态字段在“已验证、可访问、已发布、质量良好”之间混用；
- 血缘只记录对象 ID，不固定 Revision；
- 权限在规划时检查一次，实际提交时已经被撤销；
- 纠正只改对象状态，却遗漏已经发布和送达的下游。

本模型的核心结论是：

> 语义形成权、接受/发布决定权、持久化保管权、策略强制权和执行权必须分别建模；任何主体拥有其中一项，都不能自动推出另一项。

## 二、统一总图

```text
候选生产者
Agent / Tool / Human / External Source
        │
        │ propose / observe / derive
        ▼
Candidate Object Revision
        │
        ├── Provenance / Derivation / Argument Edges
        │
        ▼
Verification / Review Package
        │
        │ 有权 Decision
        ▼
Accepted / Active / Approved Revision
        │
        ├── 数据底座：保管版本、状态、血缘、审计
        ├── 平台控制：强制 Scope、Purpose、Policy、Risk
        ├── 运行系统：执行已授权 Transition / Side Effect
        └── 领域所有者：解释语义与异常
        │
        ▼
Consumer Objects / Product Release / External Action
        │
        ▼
Challenge / New Evidence / Revocation / Outcome
        │
        ▼
Governance Case + Impact Analysis
        │
        ▼
Confirm / Correct / Supersede / Withdraw / Retract
```

任何箭头都不是自然语言暗示，而应对应：

- 一个有类型对象关系；
- 一个有权状态转换；
- 或一个可审计过程记录。

## 三、五级对象分类

S9 采用五级分类，避免“所有重要概念都升格成核心对象”。

| 级别 | 名称 | 判断 |
|---|---|---|
| `C` | Core Semantic Object | 跨面共享，拥有稳定身份、独立生命周期、语义权威和纠正需求 |
| `S` | First-class Supporting Record | 有身份、版本和审计价值，但主要服务于核心对象的认识、治理、交付或评测 |
| `R` | Typed Relation / Value Object | 连接或限定对象，语义依赖端点，不独立构成业务主干 |
| `V` | Role / Projection / View | 由 Query Context 或状态计算，不创造新的语义实体 |
| `D` | Deferred Domain Object | 已证明需要表达，但所属专题尚不足以完成核心裁定 |

`S` 不等于“不重要”。Epistemic Record、Semantic Edge Record 和 Product Release 即使不进入顶层对象家族，也必须作为一等记录存在，不能压成无审计字段。

后文的 `C subtype`、`S/process`、`R/value` 等写法以前缀字母为正式级别，斜杠后的文字只说明该级别中的职责子类。

## 四、S1—S8 对象准入裁定

### 4.1 F1：意图、授权与治理

| 概念 | S9 分类 | 裁定 |
|---|---:|---|
| Intent | C | 保留原始目标、纠正或决定表达 |
| Mission | C | 稳定使命、边界、承诺和验收身份 |
| Plan | C | 可评审、替代和执行的治理方案 |
| Decision | C | 有权主体产生的结构化决定 |
| Governance Case | C | 统一承载争议、身份解析、影响纠正和例外审理 |
| Dispute Case | C subtype | Governance Case 的事实争议子型 |
| Entity Resolution Case | C subtype | Governance Case 的身份裁定子型 |
| Impact Case | C subtype | Governance Case 的影响与纠正子型 |
| Authority Domain | S/control | 有身份的权威制度记录，属于治理/控制语义，不是情报结论 |
| Acceptance Context | R/value | Authority Domain、标准、用途、作用域、时间和决定的组合 |

为何引入通用 `Governance Case`：

- 三类案件都有打开、举证、分派、审理、决定、上诉、关闭和重开；
- 都需要独立 owner、deadline、scope、participants 和 audit；
- 但案件子型拥有不同证据标准和裁定主体，不能只用一个无类型工单。

### 4.2 F2：执行与过程

| 概念 | S9 分类 | 裁定 |
|---|---:|---|
| Run | C | 一次有边界执行实例 |
| Task | C | 可调度、重试、取消、委托和审计的工作单元 |
| Execution Event | S/process | 执行事实记录，不是现实 Event |
| Artifact | C/working | 有身份的工作产物，但默认没有 Evidence / Fact 权威 |
| Agent Instance / Worker | 平台运行对象 | 执行主体，不是 Task 或领域结论 |
| Transition Request | S/process | 请求状态变化，不证明变化已发生 |
| Transition Record | S/process | 证明有权状态变化已经提交 |

Artifact 保持核心工作对象，是因为它会跨 Task、Run 和恢复边界被引用；但它必须经过显式晋升链才能成为 Evidence、Assessment 或 Product 内容。

### 4.3 F3：来源与资料

| 概念 | S9 分类 | 裁定 |
|---|---:|---|
| Source | C | 可持续识别的来源身份 |
| Document | C | 跨版本识别的逻辑资料 |
| Snapshot | C | 一次不可变捕获 |
| Acquisition Record | S/process | 证明如何获取，不是获得的内容 |
| Document Representation | R/S | 默认是 Document—Snapshot 的表示关系；有独立许可和生命周期时可为支撑记录 |
| Source Principal / System / Endpoint | R/role | 必须可分别表达；只有独立身份和生命周期成立时才实例化为独立 Source 节点 |
| Evidence Anchor | R/value | Evidence 的必要定位值，不单独成为 Evidence |
| Rights / Usage Envelope | R/control | 横切约束，不进入情报对象主干 |

### 4.4 F4：认识、陈述与接受

| 概念 | S9 分类 | 裁定 |
|---|---:|---|
| Mention | C | 材料到 Entity / Event 的一等认识对象 |
| Evidence | C | 可定位、可审计的证明材料单元 |
| Proposition | S/semantic | 可版本化规范命题，用于对齐 Claim 和 Fact，但自身不表示真 |
| Claim | C | 某主体对 Proposition 的有来源陈述 |
| Verification Record | S/process | 一次验证行为和结果 |
| Fact | C aggregate | 指定 Authority Domain 内被接受的 Proposition / Claim 组合 |
| Epistemic Record | S/relation | Authority Domain 对 Revision 的认识状态历程 |
| Evidence Relation | S/semantic edge | Semantic Edge Record 的 argument 子型 |
| Disputed Claim | V/state | Claim 状态或查询视图 |
| Verified Fact | V/alias | Fact 的强调性称呼 |
| Verification Package | V/package | 面向审核的组合投影 |

#### Fact 与 Acceptance 的收敛

领域语言保留：

```text
Fact
= Proposition Revision
+ Acceptance Context
+ Acceptance Decision
+ Epistemic History
```

Fact 仍是核心聚合对象，而不是全局真理。

物理实现可以规范化为 Proposition、Fact Identity、Acceptance Decision 和 Epistemic Record，但不得把“内容行”脱离接受域后称为 Fact。不同 Authority Domain 对同一 Proposition 的接受形成不同 Fact 身份或作用域化 Fact 分支。

### 4.5 F5：世界模型

| 概念 | S9 分类 | 裁定 |
|---|---:|---|
| Entity | C | 持续识别的现实或概念指称对象记录 |
| Relationship | C | 有方向、角色、时间、Evidence 和状态的语义联系 |
| Event | C | 有时间边界的现实发生项 |
| Change | C | 有意义的前态—后态变化 |
| World Change | C subtype | Change 的现实变化子型 |
| Knowledge Revision | C subtype | Change 的认识修订子型 |
| Content / Processing Change | C subtype when promoted | 只有具备监测价值时成为 Change |
| State | V | 指定 Query Context 下的世界状态投影 |
| World State Snapshot | S/derived | 可重建检查点，不取得事实权威 |
| Difference Record | S/derived | 对两个版本的比较结果 |
| Identity Link | S/semantic edge | 有状态的身份关系，不是数据库 merge |
| Alias | R/semantic relation | 带时间、Evidence 与作用域的名称关系 |
| Name / Identifier | R/value | 值对象；Identifier 必须绑定 issuer/namespace/time |
| Canonical Entity | V | 作用域化实体聚合视图 |
| Event Mention | R/composition | 默认由 Mention 集合、角色和 Anchor 组合；未来可在事件抽取专题重开 |

任何被称为 `Relationship` 的跨 Entity 联系都采用一等对象；简单固有属性可作为 Entity Revision 的值，但不能同时又以“轻量 Relationship”绕过时间、Evidence 和纠正规则。

### 4.6 F6：研判与未来

| 概念 | S9 分类 | 裁定 |
|---|---:|---|
| Signal | C | 值得解释、调查或监测的模式 |
| Assessment | C | 带依据、假设、反证和不确定性的判断 |
| Scenario | C | 条件化可能世界，允许无概率 |
| Forecast | C | 有目标、时间窗、解析和评分规则的未来判断 |
| Risk | C | 对 Hazard、Exposure、Likelihood、Impact 和时限的判断 |
| Recommendation | C | 建议性选择，不等于 Decision / Action |
| Hypothesis | R/contained | Assessment 中有身份的组成，可独立引用但暂不升格顶层对象 |
| Dissent | S/semantic edge | 针对指定 Revision 的反对/保留关系 |
| Forecast Resolution | S/evaluation | 有身份的到期解析记录 |
| Forecast Score | S/evaluation | 指定评分方法和 cohort 的评测记录 |
| Effect Assessment | C subtype | Assessment 的因果效果子型 |
| Opportunity / Upside | D | 交由领域研判专题决定是否建立 Risk 对称对象 |

Scenario 在 S6—S9 已通过身份、版本、独立失效和跨面消费测试，进入 S10 的核心对象采纳候选。

### 4.7 F7：交付、反馈与影响

| 概念 | S9 分类 | 裁定 |
|---|---:|---|
| Intelligence Product | C | 长期产品身份、目的和承诺 |
| Product Edition | C child | 冻结内容、知识截点和依赖的不可变产品版本 |
| Product Release | S/governance | 某 Edition 对受众、用途、渠道和时间的发布授权记录 |
| Alert | C | 有触发、受众、时效和响应跟踪的通知对象 |
| Feedback | C | 指向目标与版本的有来源回应 |
| Rendering | S/delivery | Edition / Release 的格式表达 |
| Distribution Attempt | S/delivery | 一次发送尝试 |
| Delivery Record | S/delivery | 渠道送达结果 |
| Receipt / Consumption Signal | S/delivery | 接收或使用观测，不等于 Decision |
| Change Notice | S/process | 上游变化的受控路由记录 |
| Correction Notice | S/delivery | 面向既有暴露者的更正交付记录 |
| Alert Thread | V/case view | 关联多个 Alert 的聚合视图；需要处置案件时创建 Governance Case |
| Published / Corrected Product | V/state | Edition / Release 的生命周期视图 |
| Action | D | 外部行动专题终审；现阶段必须以可引用 Action Record 表达已观察行动 |
| Effect | V/assessment | 使用 Effect Assessment，不建立无依据客观 Effect |

Product Edition 是核心内容子对象；Product Release 是一等治理记录。这样既保留独立发布撤回语义，又避免把同一知识内容复制成多个“产品”。

### 4.8 F8：能力演进

| 概念 | S9 分类 | 裁定 |
|---|---:|---|
| Evolution Evidence | C | 证明能力缺口、退化或效果的演进证据 |
| Capability Change Candidate | C | 版本化变更提案 |
| Experiment | C/evolution | 有假设、隔离、预算和停止条件的实验 |
| Evaluation | S/evaluation | 指定版本、数据集、指标和结果的评测记录 |
| Capability Version | C/control | 可采用、发布、回滚和观察的能力版本 |
| Release / Rollback Record | S/governance | 能力发布与回滚决定/执行记录 |

生产 Evidence 与 Evolution Evidence 必须类型分离；同一材料可分别被引用，但不能因进入演进流程而改变原领域语义。

## 五、最终候选对象主干

S9 提议 S10 评审以下主干：

```text
F1 意图与治理
Intent → Mission → Plan → Decision
                    └── Governance Case

F2 执行
Run → Task → Artifact

F3 来源资料
Source → Document → Snapshot

F4 认识与接受
Mention / Evidence / Claim / Fact

F5 世界模型
Entity / Relationship / Event / Change

F6 研判未来
Signal / Assessment / Scenario / Forecast / Risk / Recommendation

F7 交付反馈
Intelligence Product → Product Edition
Alert / Feedback

F8 能力演进
Evolution Evidence / Experiment / Capability Change Candidate / Capability Version
```

以下作为跨家族一等支撑记录：

```text
Object Revision
Epistemic Record
Semantic Edge Record
Verification / Evaluation Record
Transition Record
Product Release / Rendering / Delivery Records
Knowledge Snapshot / World State Snapshot
Authority Domain / Acceptance Context
Authority Mandate Evaluation / Snapshot
```

这套划分不要求“一对象一张表”，也不允许用一张 `knowledge_item` 表抹掉语义差异。

## 六、权威不是一个 owner 字段

### 6.1 七种权力

每类对象至少分别回答：

| 权力 | 含义 |
|---|---|
| Propose / Produce | 创建候选、观察或生成新 Revision |
| Interpret | 定义领域含义、适用角色、时间和边界 |
| Verify / Review | 检查证据、逻辑、质量和合规 |
| Accept / Decide | 使候选在指定 Authority Domain 获得有效状态 |
| Publish / Authorize Use | 允许产品、告警、数据或能力用于指定受众和用途 |
| Challenge / Correct / Retract | 发起或决定争议、纠正、替代与撤回 |
| Custody / Enforce / Execute | 保存、强制政策、执行转换或副作用 |

任何一项都不自动推出另一项。

### 6.2 Authority Grant / Mandate

有权操作必须能够解析为：

```text
AuthorityMandate {
  mandate_id
  principal_or_role
  authority_domain
  allowed_operations[]
  object_types[]
  scope
  purpose
  jurisdiction
  risk_ceiling
  delegation_depth
  valid_from
  valid_to
  constraints[]
  issuer
  policy_version
  revocation_state
}
```

它可以由 Role、Grant、Court Profile、Mission 委托和平台政策共同计算。模型能力、上下文长度、官职称谓或对数据库的写权限都不是 Authority Mandate。

Authority Mandate 是某次操作时对既有权力来源求交后的可审计授权投影或快照，不取代 Role、Grant、Policy 和 Decision 的原始身份，也不凭自身创造新权力。

## 七、核心对象权威矩阵

### 7.1 治理与执行

| 对象 | 候选生产 | 接受/激活 | 挑战/撤回 | 保管与执行 |
|---|---|---|---|---|
| Intent | 用户、外部入口、皇帝代录 | 原始表达无需事实接受；高影响解释需用户确认 | 原表达者可纠正，审计保留旧版 | 交互面 / 数据底座 |
| Mission | 中书、皇帝、授权人类 | 有权仙人或治理规则 | 授权者、门下、风险/政策守卫 | 治理面；运行面执行 |
| Plan | 中书或规划 Agent | 治理面；高风险由门下/人类 | 治理面重规划、授权者取消 | 尚书调度，底座保管 |
| Decision | 有权主体提出/作出 | 决定者权威本身 + Policy Gate | 原决定者、上级权威或法定机制 | 治理面记录，执行面落实 |
| Governance Case | 任一合法挑战/规则触发 | 对应案件制度 | 上诉、重开、外部权威 | 治理面拥有，底座保管 |
| Run / Task | 尚书、Scheduler | 已批准 Plan / Mission + Runtime Policy | 治理取消、策略/预算/安全中止 | 运行面权威状态 |

### 7.2 来源、认识与世界

| 对象 | 候选生产 | 接受/激活 | 挑战/撤回 | 保管与执行 |
|---|---|---|---|---|
| Source | 来源管理 Tool / 人类 | 来源准入制度 | 许可、风险、供应链治理 | 数据认知形成；底座保管 |
| Document | 采集/归并 Tool | 文档身份规则 | 归并纠正、来源争议 | 数据认知 / 底座 |
| Snapshot | 采集 Tool | 捕获成功和完整性规则 | 标记损坏/部分；内容不可改写 | 数据底座不可变保管 |
| Mention | 抽取 Tool / Agent | 解析流程接受候选链接 | 身份反证、Split | 数据认知 |
| Evidence | Tool / Agent / Human 锚定 | Evidence 资格规则；高风险复核 | Anchor/许可/完整性挑战 | 数据认知形成，底座保管 |
| Claim | 来源主体、用户、Agent、Tool | Verification 不改写 Claimant | 任何有权主体可挑战 | 数据认知 / 底座 |
| Fact | 验证流程提出 | 指定 Authority Domain 的 Acceptor | 合法挑战者提出；该 Authority Domain 的复核/申诉制度或适用外部权威裁定 | 数据底座只保管，不接受 |
| Entity / Relationship / Event | 数据认知 + 领域能力 | 对应解析/事实接受制度 | 争议案件、身份拆分、新 Fact | 数据认知语义，底座保管 |
| Change | Detector / Tool / Agent | 变化接受规则/领域复核 | 新 Evidence、分类纠正 | 数据认知语义，底座保管 |

### 7.3 研判与交付

| 对象 | 候选生产 | 接受/激活 | 挑战/撤回 | 保管与执行 |
|---|---|---|---|---|
| Signal | 规则、统计、模型、Agent | 资格/重要性规则 | 去噪、解释、失效 | 研判面 |
| Assessment | 领域 Agent / 专家 | 研判审核主体 | Dissent、新 Evidence、失效条件 | 研判面形成，底座保管 |
| Scenario | Agent / 专家 | 评审其假设与用途 | 假设失效、替代 | 研判面 |
| Forecast | 预测 Agent / 模型 / 专家 | 独立审核 + 发布用途决定 | 假设失效、新预测；到期解析独立 | 研判面，评测主体评分 |
| Risk | 研判主体 | 风险接受/分级制度 | 新 Evidence、缓解结果、复开 | 研判面 |
| Recommendation | Agent / 专家 | Review 使其 Available，不授权行动 | 新事实、决定者拒绝、撤回 | 研判面 |
| Intelligence Product / Edition | 产品 Agent / 翰林 / 人类 | 内容审核；Edition 不等于发布 | 影响案件、更正/替代 | 产品面 |
| Product Release | 产品面提出 | 有权 Publication Decision + 分发 Policy | 发布权威撤回、政策强制 | 产品面记录，运行/渠道执行 |
| Alert | 规则/产品面形成 | Trigger / Policy / 风险门 | 抑制、合并、撤回 | 产品面生命周期，交互面投递 |
| Feedback | 用户/专家/系统/现实结果 | 不需事实接受；分类和验证另行发生 | 作者补充/撤回，原反馈保留 | 交互面入口，目标面处理 |

### 7.4 演进与控制

| 对象 | 候选生产 | 接受/激活 | 挑战/撤回 | 保管与执行 |
|---|---|---|---|---|
| Evolution Evidence | 全系统观察者 | 演进资格规则 | 数据/方法挑战 | 演进面 |
| Experiment | 演进主体提出 | 治理、隔离、预算和风险批准 | 安全停止、实验无效 | 演进面；运行面执行 |
| Capability Change Candidate | Agent / Tool / Human | 演进治理 | 评测反证、拒绝 | 演进面 |
| Capability Version | 评测通过候选 | 治理采用 + 平台发布 | 回滚决定、供应链撤销 | 平台控制期望状态 |
| Authority Domain / Policy | 平台或租户治理者 | 对应宪制权威 | 上级/外部制度或合法变更 | 平台控制面 |

## 八、数据底座的特殊权力边界

数据底座拥有：

- 分配 ID；
- 验证 Schema 和引用完整性；
- 原子提交 Revision、Transition Record 和关键边；
- 维护记录时间、版本图和不可变日志；
- 执行保留、删除、地域和加密策略；
- 提供查询、血缘和影响候选。

数据底座不拥有：

- 判断 Claim 为真；
- 接受 Fact；
- 判定 Assessment 合理；
- 批准 Product Release；
- 关闭 Risk；
- 宣布外部 Action 成功；
- 因存储方便而改变 Scope 或 Authority Domain。

`UPDATE fact SET status='Active'` 只有在携带可验证 Decision、Mandate、Transition 和不变量证明时才是合法提交；数据库写权限不能成为领域权威捷径。

## 九、生命周期不是单一 status

### 9.1 六个正交状态轴

同一对象可能同时拥有：

| 状态轴 | 示例 |
|---|---|
| Content / Revision | Draft、CurrentRevision、SupersededRevision |
| Epistemic | Observed、Accepted、Challenged、Withdrawn |
| Governance | Proposed、UnderReview、Approved、Rejected |
| Availability | Available、Restricted、Quarantined、Deleted |
| Quality | Complete、Partial、Degraded、Corrupted |
| Delivery / Execution | Queued、Running、Delivered、Failed |

例如：

```text
Fact Revision F7
Epistemic = Accepted
Availability = Restricted
Quality = Partial
```

这不矛盾。把三者压进一个 `status = active` 会丢失关键信息。

### 9.2 对象状态机仍然专用

S9 不用一条通用：

```text
Draft → Active → Closed
```

替代现有对象状态机。统一的是 Transition Envelope、权威检查和不变量，具体状态仍由：

- Claim / Fact；
- Entity / Relationship / Event / Change；
- Assessment / Forecast；
- Risk / Alert；
- Product；
- Mission / Run / Task；
- Capability；

各自定义。

### 9.3 状态、修订和新对象

| 情况 | 正确动作 |
|---|---|
| 同一内容进入审核 | 状态转换 |
| 内容、有效时间或关键血缘改变 | 新 Revision |
| 语义身份改变 | 新 Object |
| 新 Revision 取代旧 Revision | Revision + supersedes edge |
| Fact 被挑战 | Epistemic / lifecycle transition，不重写旧内容 |
| Product 更正 | 新 Product Edition + correction edge |
| 重新渲染 | 新 Rendering，不新建 Edition |
| 权限撤销 | Grant / Availability 变化，不删除语义历史 |

## 十、统一 Transition Envelope

```text
TransitionRequest {
  transition_request_id
  target_object_id
  expected_revision_id
  expected_state_vector
  transition_type
  proposed_new_revision?
  actor_principal
  authority_mandate_ref
  decision_ref?
  reason
  evidence_and_input_refs[]
  scope
  purpose
  risk_tier
  requested_at
  idempotency_key
  trace_id
}

TransitionRecord {
  transition_record_id
  request_ref
  previous_revision_and_state
  resulting_revision_and_state
  evaluated_policy_versions[]
  invariant_check_results[]
  committed_at
  commit_sequence
  side_effect_status?
}
```

### 10.1 转换守卫顺序

```text
1. Resolve current Principal / Tenant / Scope / Purpose
2. Validate expected Revision and state vector
3. Re-evaluate current Mandate and revocation
4. Validate object-specific preconditions
5. Validate evidence / review / lineage completeness
6. Validate risk, budget, license and policy
7. Detect forbidden concentration or self-approval
8. Commit Revision + Transition + mandatory edges atomically
9. Publish outbox event
10. Execute external side effects
11. Record actual outcome / compensation
```

第 3 步必须在提交前重新执行。规划时有权不等于提交时仍有权。

### 10.2 乐观并发与幂等

- `expected_revision_id` 防止在过期对象上提交；
- `expected_state_vector` 防止某状态轴已变化却只检查单字段；
- `idempotency_key` 防止重试重复接受、发布或撤回；
- 冲突返回结构化 `StaleRevision / StateConflict / AuthorityRevoked`；
- 不允许 Agent 遇到冲突后静默覆盖。

### 10.3 原子边界

同一权威存储内，应原子提交：

- 新 Revision；
- Transition Record；
- Decision 引用；
- 强制的 provenance / governance / version edges；
- outbox event。

外部发送或行动无法与本地提交原子化，使用 Saga：

```text
Authorized Intent
→ Local Pending Side Effect
→ External Attempt
→ Receipt / Actual State
→ Complete / Retry / Compensate / Human Escalation
```

## 十一、分权和自审规则

### 11.1 逻辑独立，不表演多 Agent

独立性由以下因素判断：

- 不同 Authority Mandate；
- 不同证据选择责任；
- 不同模型/Tool/数据失效根；
- 不同审核目标；
- 能够真实封驳；
- 失败和异议被保留。

启动三个共享同一上下文、同一模型、同一提示和同一来源的 Agent，不产生三份独立证据。

### 11.2 禁止的闭环

高影响路径不得由同一认知主体同时：

```text
选择 Evidence
→ 形成 Assessment / Forecast
→ 审核自己
→ 批准发布
→ 解析自己的预测
→ 删除失败记录
```

风险越高，需要分离的权力越多；低风险路径可以合并执行角色，但 Decision、Mandate 和审计语义仍保持可分。

### 11.3 紧急路径

Break-glass 不绕过模型：

- 使用专门、短时、限域 Mandate；
- 只允许明确操作；
- 产生更高审计等级；
- 尽快独立复核；
- 到期自动撤销；
- 不能用于回填常规合法性。

## 十二、Semantic Edge Record

### 12.1 为什么边也必须有语义

关键边如果只是：

```text
A related_to B
```

就无法知道：

- A 是否真正依赖 B；
- B 撤回是否应使 A 失效；
- 谁创建并审核了该关系；
- 边引用的是哪个 Revision；
- 边是否只在一个租户和用途内可见；
- 它表达来源、论证、治理、版本、分发还是因果。

因此采用有身份的 `Semantic Edge Record`：

```text
SemanticEdgeRecord {
  edge_id
  edge_class
  edge_type
  from_object_revision
  to_object_revision
  direction
  scope
  purpose
  valid_time?
  recorded_time
  creator
  method_and_version?
  authority_basis?
  status
  strength_or_necessity?
  replacement_edge?
}
```

它是一等支撑记录，不是新的世界对象家族。

### 12.2 边类型注册表

#### A. Provenance

```text
published_by
captured_from
anchored_in
observed_in
submitted_by
```

回答“来自哪里”，不证明内容正确。

#### B. Transformation / Derivation

```text
extracted_from
transformed_from
derived_from
computed_from
summarizes
visualizes
```

回答“怎样生成”，需要方法和版本。

#### C. Argument / Evidence

```text
supports
contradicts
qualifies
bounds
negative_evidence_for
```

回答“材料如何影响 Claim / Assessment”，不能由模型单方面创建后自动获得接受。

#### D. Acceptance / Epistemic

```text
asserts
verified_by
accepted_as
challenged_by
rejected_by
withdrawn_by
```

必须引用 Authority Domain、Decision 或 Epistemic Record。

#### E. Identity / World

```text
mentions
resolved_to
same_as
different_from
participates_in
establishes
modifies
ends
before_state
after_state
```

不能把 `same_as` 当成数据库物理 merge。

#### F. Semantic Dependency

```text
premise
supports_conclusion
contradicts_conclusion
bounds_conclusion
context_only
quoted_as_claim
method_input
```

用于影响分析和纠正裁剪。

#### G. Governance

```text
proposed_by
reviewed_by
decided_by
authorized_by
executed_by
appealed_by
case_for
```

回答权力怎样行使。

#### H. Version / Correction

```text
revision_of
supersedes
corrects
retracts
splits_from
merges_view_of
reinterprets
```

版本与纠正图不得产生自指或循环替代。

#### I. Delivery

```text
edition_of
release_of
rendered_from
distributed_as
delivered_to
acknowledged_by
correction_for
```

送达不推出接受、决定或行动。

#### J. Causal / Intervention

```text
recommended_option
decision_considered
authorized_action
action_observed
outcome_observed
causal_claim_about
effect_assessed_as
```

边名必须保留认识论强度。`followed_by`、`associated_with` 不能伪装成 `caused`。

#### K. Evolution

```text
motivated_by
experimented_as
evaluated_by
approved_as
released_as
rolled_back_by
```

生产结果不能由能力生成器自己删选后证明自身改进。

### 12.3 图结构约束

- Revision derivation 在一次生成因果中必须是 DAG；
- Version / supersession / correction 边禁止自环与循环；
- Semantic world graph 可以有现实循环关系；
- Causal hypothesis graph可以有反馈环，但必须带时间和认识论类型；
- 同一 edge type 只能连接注册表允许的对象类型；
- 关键边固定 Revision，不只引用逻辑 Object ID；
- 跨 Scope 边必须通过当前策略强制；
- 删除端点后保留允许的最小 tombstone 或声明 lineage gap。

## 十三、血缘完整性等级

### 13.1 Completeness Profile

| 等级 | 含义 | 允许用途 |
|---|---|---|
| L0 Unknown | 没有可验证输入链 | 仅隔离调试，不得当结论 |
| L1 Origin | 可定位直接来源/提交者 | 原始资料展示 |
| L2 Reproducible Transform | 可定位输入、方法和版本 | 工作产物、低风险派生 |
| L3 Epistemically Grounded | Evidence、Claim、验证与接受链完整 | Fact 与普通研判 |
| L4 Governance Complete | 审核、Decision、Authority、作用域完整 | 高影响发布/决定 |
| L5 Impact Accountable | 分发、纠正、结果和影响消费者可追踪 | 高影响产品、告警、外部行动 |

级别不是“质量分数”，而是某类必要边是否齐全。

### 13.2 各对象最低门槛

| 对象/用途 | 最低要求 |
|---|---|
| Snapshot 原样保存 | L1 |
| Derived Artifact | L2 |
| Evidence | L2 + Anchor / Rights |
| Fact Active | L3 |
| Assessment Accepted | L3；高影响需 L4 |
| Forecast Active | L3 + cutoff / assumptions / resolution |
| Product Edition Reviewed | L3 |
| Product Release 普通 | L4 |
| 高影响 Product / Alert | L4 + L5 路由准备 |
| 外部 Action | L4，执行后进入 L5 |
| Capability Version 发布 | L4 + 独立 Evaluation |

### 13.3 血缘断裂

```text
Lineage Gap detected
→ 标记受影响 Revision 与用途
→ 禁止或限制不满足门槛的晋升/发布
→ 建立补链 Task 或 Governance Case
→ 无法恢复时降级、撤回或声明 NonReconstructible
→ 形成质量与 Evolution Evidence
```

不得用模型“推测可能来源”补成已验证血缘。

## 十四、核心转换模板

### 14.1 Snapshot → Evidence

```text
Snapshot Revision
+ Anchor
+ Context Window
+ Transformation Chain
+ Rights / Scope
→ Evidence Candidate
→ qualification
→ Evidence Active
```

采集成功不自动产生 Evidence。

### 14.2 Evidence → Claim → Fact

```text
Claimant asserts Proposition
→ Claim
Evidence --supports/contradicts/qualifies--> Claim
→ Verification Records
→ Acceptance Decision in Authority Domain
→ Fact
→ Epistemic Record(Accepted)
```

Verifier 不因验证行为自动成为 Acceptor。

### 14.3 Fact → Assessment → Forecast / Risk

```text
Selected Fact Revisions + Unknowns + Assumptions
→ Assessment Revision
→ independent review when required
→ Forecast / Scenario / Risk
```

Assessment 不把输入 Fact 复制成自己的事实权威。

### 14.4 Assessment → Product Edition → Release

```text
Reviewed semantic inputs
→ Product Edition Draft
→ Content Review
→ Publication Decision
→ Product Release
→ Distribution / Delivery
```

生成、审核、发布和发送必须可区分。

### 14.5 Challenge → Correction

```text
Challenge / New Evidence
→ Governance Case
→ Fact / Identity / Assessment Decision
→ New Revision or State Transition
→ Change Notice
→ Impact Case
→ Recompute / Correct / Withdraw / Notify
```

语义纠正与合法删除分离。

## 十五、候选跨对象不变量

S9 使用临时编号，只有 S10 采纳后才分配或映射全局 `INV`：

| 编号 | 候选不变量 |
|---|---|
| S9-I01 | 候选生产权不产生接受权。 |
| S9-I02 | 验证权不产生事实接受权。 |
| S9-I03 | 内容生成权不产生审核或发布权。 |
| S9-I04 | 持久化保管权不产生语义决定权。 |
| S9-I05 | 策略强制点不能自行创造业务授权。 |
| S9-I06 | Agent、Tool 和内部服务的权力必须可追到当前有效 Mandate。 |
| S9-I07 | 关键状态转换在提交前重新检查授权、Revision 和状态向量。 |
| S9-I08 | 状态只能通过命名 Transition 和不可变 Transition Record 改变。 |
| S9-I09 | 内容、有效时间或关键血缘变化必须创建新 Revision。 |
| S9-I10 | 终态、撤回、删除、不可用和归档不得合并。 |
| S9-I11 | Fact 必须绑定 Proposition、Acceptance Context、Decision 和 Epistemic History。 |
| S9-I12 | 模型输出不能绕过 Evidence / Claim / Verification 晋升为 Fact。 |
| S9-I13 | 每条关键血缘边必须固定端点 Revision、类型、Scope 和创建依据。 |
| S9-I14 | Provenance、Derivation、Argument、Dependency、Governance、Delivery 与 Causality 不得使用同一无类型边。 |
| S9-I15 | `supports` 表达论证关系，不自动证明 Claim 被接受。 |
| S9-I16 | `same_as` 表达身份判断，不执行不可逆物理合并。 |
| S9-I17 | 时序相邻、产品发布或行动发生不自动产生因果权威。 |
| S9-I18 | Product Release 必须引用已审核 Edition 和独立 Publication Decision。 |
| S9-I19 | Delivery、Acknowledgement、Decision、Action 和 Effect 必须分离。 |
| S9-I20 | 上游 Challenge / Retract 必须产生有作用域的影响候选，不得静默批量改写。 |
| S9-I21 | Lineage Completeness 不足时必须按用途阻止、隔离或显式降级。 |
| S9-I22 | 跨租户边、查询和影响路由不得泄露不可见对象存在。 |
| S9-I23 | 当前模型重算不能覆盖或冒充历史 Revision。 |
| S9-I24 | 自我审核与认知集中限制由失效根和权威独立性决定，不由 Agent 数量决定。 |
| S9-I25 | 外部副作用必须记录 Attempt、Receipt、Actual State 和必要 Compensation。 |
| S9-I26 | 紧急授权必须限域、限时、可撤销并接受事后独立复核。 |
| S9-I27 | 任何对象都不能用单一 status 吞并语义、治理、可用性、质量和执行状态。 |
| S9-I28 | 无法恢复的血缘和历史必须声明缺口，不能由推测填充。 |

这些候选细化但不取代当前 Accepted 的 `INV-0001`—`INV-0015`。

## 十六、Agent 与 Tool 的边界

### 16.1 Tool / Service

- Schema、边类型和端点类型验证；
- Transition precondition、乐观锁与幂等；
- Mandate、Scope、Purpose、Risk 与 Policy 强制；
- 状态向量和 Revision 提交；
- 必须边完整性检查；
- DAG / 循环约束；
- 血缘等级计算；
- 影响候选查询；
- outbox、重试、补偿和审计；
- 过期、撤销和风险门自动阻断。

### 16.2 Agent / Human

- 对候选对象进行领域解释；
- 评估 Evidence 论证强度和替代解释；
- 决定复杂身份、事实和因果争议；
- 判断哪些 Revision 具有实质语义变化；
- 审核高影响 Assessment / Product；
- 判断血缘缺口能否降级使用；
- 处理权威域冲突和不可控外部影响。

Agent 只能提出 TransitionRequest 或在明确 Mandate 下执行被允许的低风险 Transition；关键提交仍由确定性守卫验证。

## 十七、失败模式

### 17.1 CRUD 等于领域权力

谁能调用数据库更新接口，谁就能接受或撤回 Fact。

### 17.2 一个 owner 包办所有权力

对象所有者同时生成、审核、发布、纠正和删除，无法形成真实封驳。

### 17.3 所有状态共用枚举

`Active` 同时表示已接受、可访问、质量完整和正在分发。

### 17.4 边无类型

纠正时无法区分关键前提、背景引用和反证。

### 17.5 边只引用 Object ID

历史产品被当前最新 Revision 反向污染。

### 17.6 先授权后长时执行

Mission 启动时有权，数小时后副作用提交时授权已经撤销。

### 17.7 Agent 自填审核字段

同一模型把 `reviewed_by=self` 写入即可通过发布门。

### 17.8 缺边默认完整

找不到依赖被解释为“无影响”，而不是血缘断裂。

### 17.9 自动补链制造证据

模型根据相似文本猜测来源并把猜测标为 verified provenance。

### 17.10 纠正只改当前状态

旧 Product Release、Delivery 和外部派生没有得到通知。

## 十八、与现有 Accepted 状态机的关系

当前状态机继续生效。S9 提议的统一规则用于解释和未来修订：

- 对象专用状态机不被通用状态机替代；
- Product 的 Draft/Review 更精确地归于 Product Edition，Published 投影自 Product Release；
- Fact 的 Active/Challenged 等结合 Epistemic Record 表达；
- Delivery 状态从 Alert / Product 的语义状态中逐步分离；
- 新增 Transition Envelope 和正交状态轴需在 S10 采纳后更新正式状态机；
- Accepted 文字架构图本轮不修改。

## 十九、S9 完成判据

- S1—S8 每个候选概念都有 C/S/R/V/D 分类；
- 核心对象和支撑记录的边界可解释；
- Fact、Product Edition、Product Release、Epistemic Record 和 Governance Case 完成裁定；
- 每个核心对象家族具有候选生产、接受、挑战和保管权威；
- 数据底座不再被模糊称为语义 owner；
- 生命周期、Revision 和六个状态轴明确分离；
- 状态转换拥有统一 Envelope、授权复判、并发和幂等规则；
- 血缘边注册表覆盖来源、推导、论证、接受、世界、依赖、治理、版本、分发、因果和演进；
- 关键用途拥有最低血缘完整性门槛；
- 候选不变量覆盖越权、静默覆盖、无类型边、自审和历史重写；
- 压力场景证明低权力主体无法越级晋升 Fact 或自行发布 Product。

## 二十、仍待 S10 终审

- S9 新增/细化对象是否整体采纳；
- Scenario、Mention、Governance Case、Product Edition 是否进入正式 ADR 主干；
- Product Release、Epistemic Record、Semantic Edge Record 的正式公共类别；
- Fact 聚合与 Proposition / Acceptance 的逻辑边界；
- Action 是否在本轮语义基线只保留 Deferred；
- S9 候选不变量与 `INV-0001`—`INV-0015` 的合并、去重和编号；
- 哪些 Accepted 状态机、契约和文字图需要升级；
- v0002 是否达到 Accepted 基线发布门槛。

## 二十一、关联

- [v0002 迭代入口](../02-architecture-iterations/v0002-intelligence-semantics/README.md)
- [统一语言与对象分类法](../08-reference-models/unified-language-and-object-taxonomy.md)
- [核心对象主干](./core-object-spine.md)
- [Claim、争议、Fact 与裁定模型](./claim-fact-and-adjudication-model.md)
- [实体身份与可逆消歧模型](./entity-identity-and-resolution-model.md)
- [世界状态、事件与变化模型](./world-state-event-and-change-model.md)
- [研判、预测、风险与建议模型](./assessment-forecast-risk-recommendation-model.md)
- [产品、告警、反馈与影响纠正模型](./intelligence-product-alert-feedback-correction-model.md)
- [时间、版本、作用域与知识覆盖模型](./temporal-version-scope-knowledge-overlay-model.md)
- [SCN-0017：越权事实晋升、血缘断裂与自审发布阻断](../05-scenarios-and-flows/SCN-0017-unauthorized-fact-promotion-lineage-gap-and-self-publication.md)
- [核心状态机](../08-reference-models/state-machines/README.md)
- [主体生态与分域主权](../03-current-architecture/ecosystem-and-sovereignty.md)
