# Relationship、Event、State、Diff 与 Change 语义模型

状态：Accepted

提出日期：2026-07-27

所属迭代：`v0002-intelligence-semantics`

## 一、模型目的

本文定义天机阁怎样把已接受的事实组织成可追踪的世界状态，并严格区分：

- 持续存在的 Relationship；
- 在时间中发生的 Event；
- 某个观察上下文下的 State；
- 两个版本之间的 Diff；
- 现实世界的 World Change；
- 天机阁自身的 Knowledge Revision。

它要阻止以下混写：

```text
网页变了
≠ 现实变了

抽取结果变了
≠ 来源变了

天机阁改正了旧认识
≠ 现实在改正时刻发生变化

一次任命 Event
≠ 持续任职 Relationship

两个对象先后发生
≠ 前者导致后者
```

## 二、世界模型不是绕过 Fact 的第二套真相

```text
Accepted Facts
        │ project / compose
        ▼
Entity / Relationship / Event
        │ derive / compare
        ▼
State View / World Change
```

Entity、Relationship、Event 和 Change 是便于持续关联、查询、监测和研判的世界模型对象。它们必须引用支撑它们的 Fact、Claim 和 Evidence，不能成为绕过事实接受制度的快捷写入路径。

候选原则：

- 世界模型对象拥有自己的身份和生命周期；
- 世界模型对象的成立范围来自 Acceptance Context；
- 同一世界对象可以存在公共、租户、联合或任务视图；
- 世界对象被纠正时，原始 Fact 和 Evidence 历史不被覆盖；
- 事实域冲突可以使世界对象进入 Disputed 或形成多个作用域视图。

## 三、Relationship：持续语义联系

### 3.1 正式定义

Relationship 表示两个或多个 Entity 在一段有效时间内成立的、有类型、有角色、有方向且有证据的语义联系。

示例：

- 任职于；
- 控制；
- 持股；
- 隶属于；
- 位于；
- 合作；
- 供应；
- 亲属；
- 依赖；
- 参与项目。

### 3.2 Relationship 不是什么

```text
Relationship ≠ 图数据库的一条裸边
Relationship ≠ 两个名字共同出现
Relationship ≠ 统计相关
Relationship ≠ 因果关系
Relationship ≠ 一次发生行为
Relationship ≠ 永久有效
```

### 3.3 一等关系判据

当关系具有以下任一重要特征时，应作为可引用 Relationship，而不是 Entity 的内嵌字段：

- 独立有效时间；
- 独立 Evidence / Fact；
- 角色、方向或份额；
- 生命周期和状态；
- 多个参与方；
- 争议或更正；
- 被 Event、Change、Assessment 和 Product 独立引用；
- 需要租户作用域和权限。

`employee.company_id` 只能是实现投影，不能替代任职 Relationship 的完整语义。

### 3.4 Relationship Roles

关系端点必须使用有语义的角色：

```text
Employment:
person / employer / position

Ownership:
owner / owned_entity / instrument / share

Supply:
supplier / customer / item / contract
```

多元关系不能为适应二元图边而丢掉中间角色。必要时使用 Relationship Instance 或领域关联实体，但不预先绑定某种图实现。

### 3.5 有效时间

至少支持：

- start known / estimated / unknown；
- end known / open / estimated / unknown；
- time precision；
- asserted interval；
- accepted interval；
- recorded time。

“当前任职”是按查询时点计算的 View，不是另一个 Relationship 类型。

### 3.6 生命周期

```text
Candidate
→ Accepted
→ Active
→ Challenged
→ Confirmed / Ended / Superseded / Retracted / Disputed
```

`Ended` 表示现实关系结束；`Retracted` 表示系统撤回接受。两者不能混用。

## 四、Event：发生过的有边界事件

### 4.1 正式定义

Event 表示在明确时间或时间范围内发生、涉及一个或多个参与者、可能改变世界状态的现实发生项。

Event 至少表达：

- event type and ontology version；
- participants and roles；
- occurrence time / interval / precision；
- location / jurisdiction；
- trigger or initiating conditions；
- accepted Fact and Evidence；
- scope and authority；
- lifecycle and uncertainty；
- related Events / Relationships / Changes。

### 4.2 Event 不是什么

```text
World Event ≠ Execution Event
World Event ≠ Domain Event Message
World Event ≠ Alert
World Event ≠ Change
World Event ≠ News Article
World Event ≠ 任何状态字段更新
```

需要在正式协议中使用限定名称：

- `World Event`：现实世界发生项；
- `Execution Event`：Run / Task 行为记录；
- `Domain Event`：领域状态转换通知；
- `Control Event`：平台或治理控制变化；
- `Alert`：可投递通知。

### 4.3 Event 身份

多个来源报道同一现实事件时，需要 Event Resolution：

```text
多个 Event Mentions
→ 候选聚类
→ 时间/参与者/地点/类型对齐
→ Evidence 与 Fact 接受
→ Accepted Event
```

不能按报道数量创建多个现实事件，也不能因标题相似自动合并。

### 4.4 复合与层级事件

长期事件可以由多个子事件构成：

```text
企业并购
├── 意向公布
├── 签约
├── 监管审批
├── 交割
└── 整合
```

每个子事件拥有不同时间和法律效果。“并购发生时间”必须说明所指阶段。

## 五、Relationship 与 Event 的边界

候选规则：

```text
Event
→ 描述“发生了什么”

Relationship
→ 描述“在一段时间内什么联系成立”
```

典型转换：

```text
Appointment Event
→ establishes
→ HoldsRole Relationship

Resignation Event
→ terminates
→ HoldsRole Relationship

Share Transfer Event
→ modifies
→ Ownership Relationship

Merger Closing Event
→ terminates / establishes / succeeds
→ Organization Relationships
```

Event 可以建立、修改或终止 Relationship，但两者分别保留。并非每个 Relationship 都能找到单一建立 Event，也并非每个 Event 都产生长期 Relationship。

## 六、State：时点视图而非默认核心对象

### 6.1 四种“状态”

必须限定：

```text
Lifecycle State
→ 对象自身处于 Candidate / Active / Retracted 等阶段

World State
→ 某时点世界对象与属性的组合视图

Execution State
→ Run / Task 的即时运行状态

Desired State
→ 平台控制面维护的期望配置
```

四者不能使用同一 `state` 语义。

### 6.2 World State View

候选定义：

> 在指定主体、事实域、有效时点、记录时点、辖域和政策版本下，对可见 Entity、Relationship、Event 和 Fact 进行组合得到的查询视图。

```text
scope
+ valid_at
+ known_at
+ policy_version
+ ontology_version
→ World State View
```

S5 暂不把 State 升格为核心世界对象，因为它通常是已有对象的时态投影。

### 6.3 World State Snapshot

当监测、回放、法律证据或性能需要冻结视图时，可以物化 `World State Snapshot`：

- 它是派生快照；
- 必须记录查询上下文和输入版本；
- 不取得新的事实权威；
- 输入被纠正后不会被静默重写；
- 可以被标记为“当时所知”或“按当前知识重建”。

## 七、Diff：比较结果

### 7.1 正式定义

Diff 表示对两个明确版本、状态或结果执行比较后产生的结构化 Difference Record。

它必须记录：

- left / right identity and version；
- comparison type；
- comparison method and version；
- normalization；
- detected differences；
- confidence / completeness dimensions；
- performed and recorded time；
- scope and provenance。

Diff 是派生/处理对象，不自动表示现实变化。

### 7.2 四类 Diff / Change

#### A. Content Diff

比较同一或相关 Document 的 Snapshot 内容：

```text
Snapshot v1 ↔ Snapshot v2
```

回答“来源材料内容哪里不同”。

#### B. Extraction Diff

比较同一 Snapshot 经不同解析器、模型、规则或版本得到的 Artifact / Mention / Claim：

```text
same Snapshot
+ processor v1 / v2
```

回答“天机阁的处理结果哪里不同”。

#### C. Knowledge Revision

天机阁因新 Evidence、解析、裁定或模型纠正而改变对 Claim、Fact、Entity、Relationship、Event 的接受状态：

```text
old accepted knowledge
→ new accepted knowledge
```

回答“天机阁现在认识得与以前不同”。

#### D. World Change

现实世界中 Entity 属性、Relationship、Event 或状态在有效时间上的有意义变化：

```text
world state at t1
→ world state at t2
```

回答“现实中发生了什么变化”。

### 7.3 它们可以独立发生

| 情况 | Content | Extraction | Knowledge | World |
|---|---:|---:|---:|---:|
| 网页改排版 | ● | 可能 |  |  |
| OCR 模型升级 |  | ● | 可能 |  |
| 发现旧事实一直错误 |  | 可能 | ● |  |
| 高管今天离任 | 可能 | 可能 | ● | ● |
| 来源删除真实旧公告 | ● |  | 可能 | 不一定 |
| 天机阁首次发现一年前事件 |  |  | ● | 事件发生在过去 |

## 八、Change：值得独立追踪的语义变化

### 8.1 正式定义

Change 表示在明确对象、变化维度、前后状态和时间语义下，被系统识别并接受为有意义的变化对象。

Change 之所以需要独立身份，是因为它可以：

- 触发监测和调查；
- 被 Alert、Assessment、Risk 和 Product 引用；
- 拥有重要性、影响范围和状态；
- 被确认、否定、合并、拆分或重解释；
- 连接前态、后态、Event 和 Evidence；
- 进入趋势和因果分析。

### 8.2 Change 组成

- subject；
- change type；
- before / after references；
- valid-time interval；
- detection time；
- acceptance time；
- Evidence / Fact；
- detector and method；
- scope and authority；
- materiality；
- affected objects；
- related Event；
- lifecycle and revision lineage。

### 8.3 Change 类型

正式对象可以使用上位 `Change`，但必须标记 semantic class：

- `World Change`；
- `Knowledge Revision`；
- `Content Change`；
- `Processing Change`；
- `Control / Capability Change`。

情报生产主干中的 `Change` 默认指 World Change 或 Knowledge Revision；Content / Processing Diff 只有在具有监测价值时才晋升为 Change。

### 8.4 Change 生命周期

```text
Detected
→ Classified
→ UnderVerification
→ Confirmed / Rejected / Disputed
→ Material / NonMaterial
→ Superseded / Reinterpreted
```

检测时间、现实有效时间和系统接受时间必须分开。

## 九、World Change 的形成

```text
New Snapshot / Signal / External Event
→ Content / Extraction Diff
→ Claim about change
→ Evidence + Verification
→ Fact(s)
→ before / after World State alignment
→ World Change Candidate
→ accept / dispute / reject
```

Tool 可以发现差异，Agent 可以解释语义，但 World Change 仍需走 Claim / Fact 接受链。

### 9.1 没有显式前态

首次观察到某状态时，不能自动产生“从不存在变为存在”的 Change。需要证明：

- 之前确实不存在；
- 或观察机制覆盖充分；
- 或来源明确声明建立时间；
- 或 Event 提供变化证据。

否则只能说“首次观察到”。

### 9.2 没有显式后态

关系或实体消失于来源，不证明现实终止。应区分：

- source removal；
- no longer observed；
- relationship ended；
- entity dissolved；
- access lost；
- identity changed。

## 十、事件、变化与因果

### 10.1 相关不等于因果

```text
Event A precedes Change B
≠ A caused B
```

因果需要独立 Causal Claim / Assessment，表达：

- cause and effect；
- mechanism；
- temporal order；
- counterfactual or alternative explanations；
- supporting and opposing Evidence；
- uncertainty；
- acceptance / review state。

### 10.2 Event–Change 关系

允许：

```text
Event --establishes--> Relationship
Event --modifies--> Relationship
Event --terminates--> Relationship
Event --produces--> World Change
Change --detected_by--> Signal
Change --may_be_explained_by--> Assessment
```

`caused_by` 不得由简单时序规则自动写入。

## 十一、关联与 Diff 的版本影响

### 11.1 Entity Split

S4 的 Entity Split 会触发：

- Relationship 端点重新验证；
- Event 参与者重新解析；
- State View 失效；
- Change 前后态重新计算；
- Assessment 和 Product 影响分析。

### 11.2 Fact Retract

Fact 撤回可能使：

- Relationship 进入 Challenged；
- Event 进入 Disputed；
- Change 被 Rejected / Reinterpreted；
- State Snapshot 标记“基于当时已接受事实”；
- 下游重新研判。

### 11.3 Ontology Change

类型体系变化可能造成 Extraction / Knowledge Revision，但不自动表示现实变化。新旧本体之间必须有映射和重放记录。

## 十二、多租户与时态视图

- 公共 Relationship / Event 可被租户引用；
- 租户私有 Fact 可以形成 Tenant World View；
- 租户 Change 不得回写公共 Change；
- 联合空间只组合明确贡献对象；
- 相同 before / after 在不同事实域可能得到不同 Change；
- 查询必须声明 valid_at 与 known_at；
- 缓存 Key 必须包含作用域、事实域、策略和时间；
- 私有 Change 的存在本身也可能敏感。

## 十三、权威与组织分工

| 能力 | 主要责任 | 非责任 |
|---|---|---|
| Diff 计算 | Tool / 数据认知面 | 不自动宣布 World Change |
| Relationship / Event 语义 | 数据认知面 + 领域能力 | 不绕过 Fact 接受 |
| 变化分类与解释 | 数据认知面，复杂项由领域 Agent | 不拥有高影响发布权 |
| 冲突与事实裁定 | 大理寺式治理能力 | 不修改原 Evidence |
| 结果审核 | 门下省 | 不把所有状态变更都变成人工审核 |
| 持久世界模型 | 数据底座面 | 不因存储拥有语义权 |
| 监测触发 | 钦天监 + Tool | 不把触发信号当事实 |
| 策略、作用域和生命周期 | 平台控制面 | 不创造 World Event |

## 十四、Agent / Tool 分工

### Tool / Script 优先

- Snapshot、Artifact、对象版本 Diff；
- 时间区间和单位规范化；
- 已知 Event / Relationship 模式抽取；
- Event 候选聚类；
- Relationship 状态投影；
- State View 物化与缓存；
- Change 候选检测和重要性初筛；
- 版本、血缘和影响查询；
- 监测触发；
- 低风险规则验证。

### Agent / 人类按需

- 关系角色和领域含义；
- Event 边界、层级与阶段；
- 来源删除是否对应现实终止；
- Content / Knowledge / World Change 分类；
- 复杂前态、后态和反事实；
- 组织继承、并购和控制变化；
- 变化重要性与外溢；
- 因果假设和替代解释；
- 高影响争议与更正。

### Token 经济性

先由 Diff / Index / Rule 缩小候选，再只把语义歧义、跨域组合和高影响 Change 交给 Agent。不得用“节省 Token”为由把所有内容差异直接升级为现实变化。

## 十五、关键失败模式

| 失败 | 错误做法 | 正确处理 |
|---|---|---|
| 网页文字变化 | 直接 World Change | 先 Content Diff |
| 解析器升级 | 伪造历史变化 | Extraction Diff |
| 迟到发现旧事件 | 记为今日事件 | 分开发生/发现/接受时间 |
| 关系从网页消失 | 标记现实终止 | no longer observed |
| 任命公告 | 只建任职边 | Event + Relationship |
| 公司并购 | Entity same_as | Event + succession/control 关系 |
| 多篇报道 | 多个现实 Event | Event Resolution |
| 时序相邻 | 写 caused_by | Causal Claim / Assessment |
| Entity Split | 只换端点 ID | 重评关系、事件和 Change |
| 租户私有变化 | 写公共图谱 | Tenant World View |

## 十六、S5 不变量候选

- 世界模型对象必须能回溯到 Fact、Claim 和 Evidence；
- Relationship 必须有角色、方向、作用域和有效时间；
- World Event、Execution Event、Domain Event 和 Alert 必须类型分离；
- Event 与 Relationship 分别保存发生和持续语义；
- State 默认是带上下文的时态 View，不是万能核心对象；
- World State Snapshot 不产生新的事实权威；
- Diff 必须声明左右版本、方法和完整性；
- Content Diff、Extraction Diff、Knowledge Revision、World Change 必须分离；
- 首次观察不等于对象刚刚出现；
- 来源不再展示不等于现实关系结束；
- World Change 必须经过 Claim / Fact 接受链；
- Change 必须分离有效时间、检测时间和接受时间；
- 先后发生不产生因果关系；
- Entity Split、Fact Retract 和本体变化必须触发世界模型影响分析；
- 租户世界视图不得污染公共世界模型；
- Tool 可以检测差异，不能独自拥有高影响变化接受权。

## 十七、S5 验收结果

候选层面通过：

- Relationship、Event、State View、Diff 和 Change 边界可区分；
- Event 建立/修改/终止 Relationship 的双对象模型成立；
- State 暂不需要升格为通用核心对象；
- Change 具有独立身份、生命周期和跨面消费者，继续作为核心对象；
- 四类 Diff / Change 得到正式外层位置；
- World Change 不绕过 S3 Claim / Fact 接受模型；
- 时间、因果、多租户、版本和纠正连接均存在；
- S4 Mention / Entity 在 Event 和 Relationship 中有继续保留的必要；
- `SCN-0013` 证明同一次监测可同时产生四种不同变化；
- 未发现需要修改 Skeleton v1.0 的一级缺口。

仍需后续验证：

- Relationship 是否统一采用一等对象；
- Event Mention 是否独立于 Mention；
- State Snapshot 的长期保留和重建；
- Change 上位类型与具体子类的 Schema；
- Proposition 是否因 Event / Relationship 规范化而升格；
- World Change 与 Knowledge Revision 的跨域链接；
- S6 Signal、Assessment 和 Forecast 如何消费 Change。

下一步：`S6 Signal、Assessment、Forecast、Risk、Recommendation`。

## 十八、关联

- [Entity、身份、别名与消歧模型](./entity-identity-and-resolution-model.md)
- [Claim、争议、Fact 与裁定模型](./claim-fact-and-adjudication-model.md)
- [Source、Document、Snapshot 与 Evidence 模型](./source-document-snapshot-evidence-model.md)
- [统一语言与对象分类法](../08-reference-models/unified-language-and-object-taxonomy.md)
- [SCN-0013：四类变化分离](../05-scenarios-and-flows/SCN-0013-four-kinds-of-change-separation.md)
