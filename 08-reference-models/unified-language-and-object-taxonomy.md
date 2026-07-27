# 统一语言与对象分类法

状态：Proposed

提出日期：2026-07-25

所属迭代：`v0002-intelligence-semantics`

## 一、目的

本文建立天机阁情报语义专题的第一层公共语言。

它首先解决的不是“有哪些字段”，而是：

1. 什么可以被称为正式对象；
2. 对象类型、状态、视图、记录和关系怎样区分；
3. 现有核心对象分别属于哪一类语义；
4. 哪些词只能作为上位概念，不能直接进入协议；
5. 后续增加新对象时需要通过什么判据。

本文当前为候选分类法。它需要经过 S2～S10 的资料、事实、实体、变化、研判、时间和纠正场景验证后，才能成为 Accepted 公共参考。

## 二、统一语言的三个层次

### 2.1 叙事语言

面向人类理解系统使命：

```text
掌数 → 联结 → 察变 → 推势 → 卜算 → 警险
```

叙事语言用于讲解能力，不直接充当对象类型。

例如“卜算结果”在正式语义中需要进一步表示为 Forecast、Scenario 或 Risk，不能成为一个含义不清的通用字段。

### 2.2 领域语言

面向架构、业务规则和跨面协作：

```text
Document / Evidence / Claim / Fact
Entity / Relationship / Event / Change
Assessment / Forecast / Risk / Product
```

领域语言必须具有唯一含义，并能说明身份、权威、时间、作用域和生命周期。

### 2.3 实现语言

面向 Schema、API、消息、索引和存储：

```text
object_type
object_id
version_id
status
scope
valid_time
recorded_time
...
```

实现语言必须从领域语言派生，不能反过来用数据库表名决定领域含义。

## 三、先区分五种“东西”

### 3.1 对象类型 Object Type

表示一种具有独立身份和生命周期的领域事物。

示例：Document、Claim、Entity、Forecast、Intelligence Product。

### 3.2 生命周期状态 Lifecycle State

表示同一对象在其生命周期中的阶段，不创造新的对象种类。

示例：

```text
Claim.status = Disputed
Fact.status = Retracted
Product.status = Corrected
```

`Disputed Claim` 默认应理解为 Claim 的争议状态或查询视图，不自动成为与 Claim 并列的新核心对象。

### 3.3 角色或查询视图 Role / View

表示对象在特定上下文中的用途、投影或集合。

示例：

- `Current Fact`：当前有效 Fact 的查询视图；
- `Tenant Overlay`：按租户作用域合成的知识视图；
- `Watchlist Entity`：在监测任务中承担观察目标角色的 Entity；
- `Published Product`：已发布 Product 的视图。

视图可以缓存或物化，但不因此取得独立领域身份。

### 3.4 过程记录 Process Record

表示某次处理、验证、裁定、调用或状态转移发生过。

示例：Verification Record、Execution Event、Decision Record。

过程记录证明“做过什么、由谁做、依据什么”，不等于被处理的世界对象或结论。

### 3.5 对象关系 Object Relation

表示两个对象之间的有类型连接。

示例：

```text
Evidence --supports--> Claim
Claim --accepted_as--> Fact
Fact --superseded_by--> Fact
Product --cites--> Assessment
```

关系本身是否需要独立对象，取决于它是否拥有独立身份、时间、证据、生命周期和跨面消费者。

## 四、八个对象家族

### F1：意图、授权与治理对象

回答：

> 为什么做、允许做什么、采用了什么决定？

| 对象 | 正式含义 | 明确不等于 |
|---|---|---|
| Intent | 原始目标、问题、纠正或决定表达 | 可执行任务 |
| Mission | 经解释后形成的稳定使命与验收边界 | 一次执行 |
| Plan | 服务于 Mission 的版本化治理方案 | Run |
| Decision | 有权主体对指定对象作出的结构化决定 | Recommendation、Action |

该家族的核心是权威与约束，不是世界事实。

### F2：执行与过程对象

回答：

> 哪一次工作怎样实际发生？

| 对象 | 正式含义 | 明确不等于 |
|---|---|---|
| Run | Plan 或持续定义的一次执行实例 | Mission |
| Task | Run 中可调度、重试、取消和审计的工作单元 | Agent |
| Execution Event | 执行期间发生的状态变化或行为记录 | 现实世界 Event |
| Artifact | 尚未获得证据、事实或研判语义的中间产物 | Evidence、Fact |

Agent、Tool、Worker 和人类是执行主体类型，不是 Task 类型。

### F3：来源与资料对象

回答：

> 内容从哪里来、原貌是什么？

| 对象 | 正式含义 | 明确不等于 |
|---|---|---|
| Source | 来源主体或来源端点 | 一份具体内容 |
| Document | 可跨版本识别的逻辑内容对象 | 某次不可变捕获 |
| Snapshot | Document 在指定获取时刻的不可变捕获 | Evidence |

该家族负责保真和来源身份，不直接宣布陈述正确。

### F4：证据、陈述与接受对象

回答：

> 谁说了什么、依据是什么、系统接受了什么？

| 对象 | 正式含义 | 明确不等于 |
|---|---|---|
| Evidence | 可定位且可审计、用于支持或反驳 Claim 的材料单元 | Document、结论 |
| Claim | 某个主体对世界作出的可验证陈述 | Fact |
| Verification Record | 一次验证过程及其结果的记录 | Fact 本身 |
| Fact | 在明确权威域、标准、作用域和时间下被接受的事实记录 | 永恒真理、Inference |

`Verified Fact` 是 Fact 的强调性称呼，不另设对象类型。

`Disputed Claim` 暂定为 Claim 的状态/视图；若 S3 证明“争议案件”拥有独立生命周期，再考虑引入 Dispute 对象。

### F5：世界模型对象

回答：

> 天机阁认为世界里有哪些东西、怎样关联、发生了什么？

| 对象 | 正式含义 | 明确不等于 |
|---|---|---|
| Entity | 被持续识别和追踪的现实或概念主体 | 名字字符串 |
| Relationship | Entity 之间可被陈述、验证和定时的语义联系 | 图数据库边 |
| Event | 在时间范围内发生并涉及主体的现实事件 | Execution Event |
| Change | 被接受为有意义的前态—后态变化 | 原始文本 Diff |

State 暂作为 Entity、Relationship 或其他世界对象在指定时间的状态投影，不在 S1 直接升格为核心对象。

### F6：研判与未来对象

回答：

> 已知信息意味着什么，未来可能怎样？

| 对象 | 正式含义 | 明确不等于 |
|---|---|---|
| Signal | 值得解释、调查或监测的模式 | 已成立趋势或风险 |
| Assessment | 面向当前态势的有依据判断 | Fact |
| Forecast | 面向未来、可到期验证和校准的判断 | Scenario |
| Risk | 对不利结果的可能性、影响、范围和时限判断 | Alert |
| Recommendation | 基于事实和研判提出的建议选择 | Decision、Action |

Scenario 暂作为条件、假设和可能世界的结构化研判容器，是否成为独立核心对象留给 S6。

### F7：交付、反馈与影响对象

回答：

> 结论如何被交付、被回应并影响外部？

| 对象 | 正式含义 | 明确不等于 |
|---|---|---|
| Intelligence Product | 面向特定受众、目的、时间和渠道组织的正式交付物 | 底层事实副本 |
| Alert | 由 Risk、Signal、Change 或规则触发的可投递通知 | Risk |
| Feedback | 对指定目标、事实、研判、预测、风险或产品的反馈 | 无归属自由文本 |

Action 和 Effect 已有骨架位置，但在外部行动边界明确前继续作为候选对象。

### F8：能力演进对象

回答：

> 怎样证明系统能力需要改变，改变是否有效？

| 对象 | 正式含义 | 明确不等于 |
|---|---|---|
| Evolution Evidence | 证明能力缺口、退化或改进效果的结构化证据 | 生产事实的 Evidence |
| Capability Change Candidate | 对能力或架构提出的版本化变更候选 | 已采用版本 |

Experiment、Evaluation 和 Capability Version 已有演进骨架位置，具体边界在能力演进专题细化。

## 五、邻接但不属于情报对象主干的对象

以下对象非常重要，但属于平台、控制或安全语义，不能与情报对象混成一个平面列表：

```text
Tenant / User / Principal / Role / Grant
Court Profile / Policy / Template / Catalog
Budget / Quota / Capability Version
Credential / Consent / License / Retention Rule
Model / Agent Definition / Tool Definition
```

它们通过作用域、权限、策略、预算、许可和版本引用约束情报对象。

这一区分并不降低它们的架构等级，只防止“核心对象主干”膨胀成全系统数据字典。

## 六、对象分类的四个正交问题

八个家族回答“它是什么”。每个对象还必须分别回答四个问题：

### 6.1 权威 Authority

- 谁可以创建候选；
- 谁可以接受；
- 谁可以争议、撤回或替代；
- 谁只拥有存储权而没有语义决定权。

### 6.2 作用域 Scope

```text
Public
Tenant Private
User Private
Joint Investigation
Mission / Run Working Scope
Platform Internal
```

作用域不是对象类型，也不能只靠物理存储位置推断。

### 6.3 时间 Time

- 有效时间：现实世界何时成立；
- 记录时间：天机阁何时保存该版本；
- 认识时间：某个权威域何时知道、接受或撤回。

认识时间是否独立建模留给 S8 终审。

### 6.4 版本与生命周期 Version / Lifecycle

- 对象身份跨版本保持；
- 版本内容不可静默覆盖；
- 状态变化必须可审计；
- 撤回不等于物理删除；
- 新版本不自动使所有旧版本无效。

## 七、正式对象准入测试

新增核心对象类型前，必须回答：

| 判据 | 问题 |
|---|---|
| 独立身份 | 它是否需要被单独引用，而不是依附于另一个对象？ |
| 独立生命周期 | 它是否有自己的创建、审核、有效、失效或撤回过程？ |
| 独立权威 | 是否存在不同于相邻对象的创建、接受或撤回主体？ |
| 独立时间 | 它是否拥有不能由宿主对象表达的有效时间或记录时间？ |
| 独立血缘 | 它是否需要单独追溯输入、版本和转化过程？ |
| 跨面消费 | 是否至少有两个架构面以不同目的读取或操作它？ |
| 失败差异 | 它失败、冲突或被撤回时，处理方式是否与相邻对象不同？ |

裁定规则：

```text
满足大多数关键判据
→ 候选核心对象

只表达阶段
→ Lifecycle State

只表达查询用途
→ Role / View

只证明处理发生
→ Process Record

只连接两个对象
→ Typed Relation

只服务单个实现
→ 实现结构，不进入领域语言
```

这不是机械计分。独立权威、生命周期和纠正需求具有更高权重。

## 八、禁止使用的模糊词

以下词可以用于自然语言，但进入架构正文或协议时必须限定含义：

| 模糊词 | 必须改写或限定为 |
|---|---|
| 数据 | Source、Document、Snapshot、Fact、对象版本或明确数据集 |
| 信息 | 资料、Claim、Fact、Assessment 或 Product |
| 知识 | 指定作用域下的 Fact、世界模型和其权威版本 |
| 记忆 | Agent 工作记忆、会话记忆或权威数据底座，三者不可混用 |
| 结论 | Fact、Assessment、Forecast、Risk、Recommendation 或 Decision |
| 事件 | 现实 Event、Execution Event 或 Domain Event |
| 状态 | 生命周期状态、世界状态、运行状态或期望状态 |
| 变化 | Content Diff、Extraction Diff、World Change 或 Knowledge Revision |
| 置信度 | 来源可靠性、证据充分度、实体匹配概率或预测概率 |
| 报告 | Intelligence Product 的具体类型 |

禁止在同一契约字段中用一个 `confidence` 同时表达上述不同含义。

## 九、第一批统一命名候选

| 原表达 | S1 候选统一表达 |
|---|---|
| 原始数据 | Source Material 或 Snapshot，按语境选择 |
| 证据文档 | Document + 被定位的 Evidence，不合成一个类型 |
| 已验证事实 | Fact；验证过程由 Verification Record 表达 |
| 争议事实 | Disputed Claim，或处于 Challenged 状态的 Fact |
| 模型结论 | 必须具体化为 Claim、Assessment、Forecast、Risk 或 Recommendation |
| 实体匹配结果 | Entity Resolution Candidate，不直接等于 Entity |
| 数据变化 | 必须选择四类 Diff / Change 之一 |
| 风险告警 | Risk + Alert，不合并为一个对象 |
| Agent 记忆 | Working Memory，不得称为 Fact Store |
| 最终决定 | Decision；若涉及外部执行，另需 Action 授权 |

## 十、S1 验收

S1 在候选层面完成：

- 建立叙事、领域、实现三层语言；
- 区分对象、状态、视图、过程记录和关系；
- 将现有对象整理为八个家族；
- 建立四个正交语义维度；
- 建立新增对象准入测试；
- 列出第一批必须消歧的模糊词。

尚未完成：

- 各对象字段；
- 事实接受模型；
- Entity Resolution 正式对象；
- Diff / Change 四分法终审；
- Scenario、Dispute、State、Action、Effect 是否升格；
- 时间与知识覆盖模型；
- 分类法的 Accepted 晋级。

下一步进入 S2：Source、Document、Snapshot 与 Evidence。

## 十一、S2 验证回写

S2 资料与证据模型及 `SCN-0010` 对 F3、F4 执行第一轮验证，结果支持：

- Source、Document、Snapshot、Evidence 具有不同身份、权威和失败模式；
- Acquisition Record 属于 Process Record，不应与 Snapshot 合并；
- Snapshot 的 `Partial`、`Corrupted` 等属于生命周期/质量状态，不形成新对象；
- Source Principal、System、Endpoint 当前作为可分角色，是否独立成对象继续开放；
- Evidence Anchor 是 Evidence 的必要构成，而非另一种 Evidence 类型；
- Direct / Derived Evidence 当前作为来源链角色，不因角色差异自动拆成两个对象类型；
- Content Diff 和 Extraction Diff 是比较/处理产物，不自动取得 World Change 身份；
- Rights / Usage Envelope 属于横切约束，不进入情报对象家族。

S2 未发现分类法元规则冲突。分类法继续保持 `Proposed`，等待 S3～S10 完整验证。

## 十二、S3 验证回写

S3 对 F4“证据、陈述与接受对象”执行第一轮验证，结果支持：

- Claim 是核心对象，Claim Candidate / Fact Candidate 是流程角色；
- Verification Record 是 Process Record；
- Verification Package 是 View / Package；
- `Disputed Claim` 仍是 Claim 的状态/视图；
- 多对象、多轮次的争议需要独立 Dispute Case，但它属于治理对象家族；
- Fact 是带 Acceptance Context 的接受记录；
- Current Fact 是查询视图，不是新的 Fact 类型；
- Proposition 当前是 Claim 的规范化组成，是否升格继续开放；
- Fact 的 `Challenged`、`Retracted` 等是生命周期状态，不形成新对象。

S3 未发现分类法元规则冲突。分类法继续保持 `Proposed`，等待 S4～S10 完整验证。

## 十三、S4 验证回写

S4 对 F5 世界模型对象中的 Entity 执行第一轮验证，结果支持：

- Entity 是世界模型对象；
- Mention 是独立的认识对象候选，不应作为 Entity 的别名字段吞并；
- Name 和 Identifier 当前作为值对象；
- Alias 是带时间、Evidence 和作用域的身份关系；
- Identity Claim 是 Claim 的领域子类型；
- Resolution Decision 是治理 Decision；
- Entity Resolution Case 是治理案件对象；
- Canonical Entity 是查询/聚合视图，不是新的现实对象；
- Provisional、Disputed、Split Required 是解析或生命周期状态；
- `same_as`、`different_from` 是有类型身份关系，不是数据库合并操作。

S4 未发现分类法元规则冲突。Mention 是否正式加入核心对象主干等待 S5 验证。

## 十四、关联

- [v0002 迭代入口](../02-architecture-iterations/v0002-intelligence-semantics/README.md)
- [情报语义深化计划](../04-domain-design/intelligence-semantics-deepening-plan.md)
- [核心对象主干](../04-domain-design/core-object-spine.md)
- [Source、Document、Snapshot 与 Evidence 模型](../04-domain-design/source-document-snapshot-evidence-model.md)
- [Claim、争议、Fact 与裁定模型](../04-domain-design/claim-fact-and-adjudication-model.md)
- [Entity、身份、别名与消歧模型](../04-domain-design/entity-identity-and-resolution-model.md)
- [SCN-0010：来源变化与证据保全](../05-scenarios-and-flows/SCN-0010-source-mutation-and-evidence-preservation.md)
- [SCN-0011：多权威域事实并存](../05-scenarios-and-flows/SCN-0011-multiple-authority-domain-facts.md)
- [SCN-0012：同名实体误合并与拆分纠正](../05-scenarios-and-flows/SCN-0012-entity-merge-split-correction.md)
- [核心对象流转图](../03-current-architecture/text-diagrams/TXT-0006-core-object-spine.md)
- [ADR-0008：有类型、可追溯的核心对象主干](../06-architecture-decisions/adrs/ADR-0008-typed-traceable-core-object-spine.md)
