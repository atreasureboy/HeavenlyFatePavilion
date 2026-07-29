# 时间、版本、作用域与知识覆盖语义模型

状态：Accepted

形成日期：2026-07-27

所属迭代：`v0002-intelligence-semantics / S8`

## 一、问题

天机阁既要回答世界问题，也要回答认识问题：

```text
现实在 3 月 1 日是什么状态？
天机阁现在认为 3 月 1 日是什么状态？
租户 A 在 3 月 15 日已经知道什么？
租户 B 当时为什么得到不同答案？
一份 4 月发布的产品使用了哪些当时可用信息？
5 月迟到的资料是否说明 4 月产品有错？
撤回、删除或权限变化后，还能否重建当时所见？
```

如果只保存一个 `updated_at`，这些问题会被混成“最新一行”。后果包括：

- 迟到资料被伪装成当时已知；
- 当前知识覆盖历史产品；
- 租户私有判断污染公共知识；
- 系统曾经记录但尚未验证的内容被算作“已经知道”；
- 撤回后历史版本消失；
- 查询旧政策时意外恢复当前无权访问的数据；
- 缓存遗漏租户、用途或知识截点，造成跨租户泄露；
- “当时合理但后来被新资料推翻”与“当时已经忽略已知反证”无法区分。

S8 建立一个实现无关的语义模型：

> 世界有效时间与系统记录时间构成对象版本的二时态基础；认识时间不是第三个全局时间戳，而是某个权威域对对象执行可审计认识动作的历程。作用域、可见性、语义权威和用途再通过显式查询上下文合成知识视图。

## 二、总模型

```text
现实发生 / 状态成立
        │
        │ valid time
        ▼
Claim / Fact / Relationship / Event / Change Revision
        │
        │ transaction / record time
        ▼
天机阁不可变版本历史
        │
        ├── Public Authority Domain 的认识历程
        ├── Tenant A Authority Domain 的认识历程
        ├── Tenant B Authority Domain 的认识历程
        ├── Joint Space Authority Domain 的认识历程
        └── Mission Working Context 的临时认识历程
                     │
                     │ Query Context
                     ▼
              Knowledge Projection
        ┌────────────┼────────────┐
        ▼            ▼            ▼
   当前看过去   还原当时所知   指定产品知识截点
```

必须始终保持：

```text
Valid Time ≠ Record Time
Record Time ≠ Knowledge Time
Observed ≠ Available ≠ Considered ≠ Accepted
Object Identity ≠ Object Revision
New Revision ≠ New Object
Lifecycle State ≠ World State
Scope ≠ Storage Location
Visibility ≠ Semantic Authority
Tenant Overlay ≠ Copy-and-overwrite Public Knowledge
Mission Assumption ≠ Tenant Fact
Historical Policy Replay ≠ Historical Permission Resurrection
Current View ≠ Current Row
```

## 三、时间不是一个字段

### 3.1 有效时间 Valid Time

有效时间回答：

> 该陈述、关系、状态或事件在现实世界的什么时间成立或适用？

常见形式：

```text
valid_from
valid_to
valid_time_precision
valid_time_uncertainty
calendar_and_timezone
```

有效时间可以是：

- 时间点；
- 闭区间、开区间或半开区间；
- `before T`；
- `after T`；
- 只知道某月、某季或某年；
- 开始已知、结束未知；
- 发生时间未知但在两个观察之间。

系统不得把“2026 年 3 月”强行编码成“2026-03-01 00:00:00”而不保存精度，否则排序看似精确、语义实际造假。

### 3.2 记录时间 Record / Transaction Time

记录时间回答：

> 天机阁从什么时候开始保存并认为某个对象修订是系统历史的一部分？

建议使用不可变系统控制字段：

```text
recorded_from
recorded_to
ingest_or_commit_id
```

`recorded_to` 表示该修订在系统版本历史中何时被后续修订关闭，不表示现实世界何时结束。

记录时间必须由可信系统时间和提交顺序控制，不能由来源任意声明。来源提供的 `published_at`、邮件发送时间或文件元数据是领域数据，不是天机阁记录时间。

### 3.3 来源时间与获取时间

以下时间经常有价值，但不能代替前两者：

| 时间 | 含义 |
|---|---|
| source_authored_at | 来源称内容何时写成 |
| source_published_at | 来源称何时发布 |
| source_modified_at | 来源称何时修改 |
| observed_at | 采集器何时观察到 |
| acquired_at | 天机阁何时完成获取 |
| processed_at | 某处理步骤何时运行 |
| received_at | 某主体何时收到 |

这些时间必须带 provenance、可靠性和时钟可信度。恶意来源可以伪造发布时间，采集器时钟也可能漂移。

### 3.4 认识时间 Knowledge Time

认识时间回答：

> 某个明确权威域何时对某个对象处于何种认识状态？

它不能只有一个全局 `known_at`：

- 公共域可能 5 月才获知；
- 租户 A 可能 2 月已收到私有材料；
- 租户 B 可能永远无权知道；
- 运行系统可能 2 月记录了加密资料，但业务域直到 3 月授权后才可用；
- 某 Claim 可能已被看到，但尚未验证或接受。

因此认识时间应建模为 `Epistemic Record`：

```text
EpistemicRecord {
  epistemic_record_id
  authority_domain
  subject_object_ref
  subject_revision_ref
  epistemic_state
  effective_from
  effective_to?
  basis_refs[]
  decision_or_process_ref
  recorded_at
  scope
}
```

候选认识状态：

```text
Observed
→ Available
→ Considered
→ Accepted / Rejected / Unresolved
→ Challenged
→ Confirmed / Superseded / Withdrawn
```

这不是所有对象统一生命周期。它表达“某权威域与某对象版本的认识关系”。

### 3.5 为什么不是简单三时态表

有效时间和记录时间适用于对象修订，通常可以形成二时态版本。

认识时间具有：

- 多个权威域；
- 多种认识状态；
- 接受、挑战、撤回和复核理由；
- 不同作用域和用途；
- 同一修订并行存在的多条认识历程。

把它压成第三对 `known_from/known_to` 会错误暗示只有一个全局认识主体。候选方案因此是：

```text
Bitemporal Object Revision
+
Scoped Epistemic Records
```

这不强迫物理数据库采用某种表结构，但必须保留该语义分离。

## 四、不同对象如何使用时间

不是每个对象都拥有同样的时间：

| 对象 | 主要时间语义 |
|---|---|
| Source | 主体/系统/端点存在与可用时间 |
| Document | 逻辑作品的创作、发布与修订时间 |
| Snapshot | observed/acquired/recorded 时间；内容本身不可变 |
| Evidence | 所锚定材料时间、证据所支持命题的适用时间 |
| Claim | 主张内容的有效时间 + Claimant 何时提出 |
| Fact | Proposition 有效时间 + Acceptance Context 的认识历程 |
| Entity | 身份记录生命周期，不等于现实主体生灭时间 |
| Identifier / Alias | 标识或名称适用区间 |
| Relationship | 联系成立的有效区间 |
| Event | 发生时间或时间窗 |
| Change | 现实变化时间、检测时间、接受时间 |
| Assessment | information cutoff、as-of time、形成和审核时间 |
| Forecast | origin、cutoff、horizon、resolution time |
| Risk | 风险评估有效窗、监测和关闭时间 |
| Product Edition | world as-of、knowledge cutoff、生成、审核与发布时间 |
| Alert | 触发、有效、到期、送达和确认时间 |

### 4.1 关于未来的对象

Forecast 和 Scenario 可以引用未来有效时间，但它们当前是研判对象，而不是提前写入未来 Fact。

### 4.2 关于过去的新认识

今天接受“某人在一年前离任”：

```text
Fact.valid_from = 一年前
Fact.recorded_from = 今天
EpistemicRecord(Accepted).effective_from = 今天
```

不得把 `recorded_from` 回填为一年前，也不得宣称租户在一年前已经知道。

## 五、对象身份、修订与生命周期

### 5.1 Object Identity

Object Identity 回答：

> 跨时间和修改，我们是否仍在谈同一个概念对象？

它应尽量稳定，不包含“当前”内容。

### 5.2 Object Revision

Object Revision 是对象在一次语义修订后的不可变表达：

```text
ObjectRevision {
  object_id
  revision_id
  revision_number_or_branch
  content
  valid_time
  recorded_from
  recorded_to?
  predecessor_refs[]
  revision_reason
  change_type
  author_or_generator
  method_versions
  scope
  content_hash
}
```

创建新 Revision 的条件包括：

- 语义内容改变；
- 有效时间改变；
- 作用域或权威语义改变；
- 关键血缘或接受依据改变；
- 更正、替代、撤回；
- 处理版本改变且输出语义变化。

纯查询缓存、重新序列化或无语义格式变化不应创建领域 Revision。

### 5.3 Revision Graph，不是强制单链

版本可能分支：

```text
Public Revision P3
├── Tenant A interpretation TA1
├── Tenant B challenge TB1
└── Joint Space adjudication J1
```

这些不是全局 `v4、v5、v6` 的覆盖顺序。每个修订必须声明：

- same object or derived object；
- predecessor；
- branch / authority domain；
- supersedes、corrects、challenges 或 specializes；
- 是否可合并；
- 合并决定和冲突。

跨作用域版本不存在天然全序。

### 5.4 Revision 与生命周期状态分离

生命周期变化有时只需新状态记录，有时必须新 Revision：

| 变化 | 默认表达 |
|---|---|
| Draft → UnderReview | 生命周期事件 |
| 文本内容改变 | 新 Revision |
| Accepted → Challenged | 认识/生命周期事件 |
| 有效区间被纠正 | 新 Revision |
| 权限临时暂停 | Policy / Grant 状态，不改对象内容 |
| Fact 被撤回 | 撤回决定 + 新状态/修订关系，保留旧 Revision |
| Product 重新渲染但内容相同 | 新 Rendering，不新建 Edition |

S9 将终审每类对象的具体规则。

### 5.5 并发与因果顺序

记录时间只能提供系统提交顺序，不能证明现实因果顺序。

跨区域或联邦节点可使用：

- source sequence；
- causal / correlation id；
- vector or logical clock；
- ingestion watermark；
- reconciliation record。

这些是运行与重建辅助，不替代有效时间和领域裁定。

## 六、作用域不是单一层级

### 6.1 Scope 的组成

对象作用域至少需要区分：

```text
ownership_scope
visibility_scope
semantic_authority_domain
purpose_constraints
jurisdiction
retention_and_residency
sharing_grants
```

“Tenant Private”只回答其中一部分。一个对象可能：

- 由租户拥有；
- 只对联合调查空间可见；
- 由外部监管机构具有语义权威；
- 仅允许尽调用途；
- 必须留在指定地域；
- 到期后只能保留最小审计证明。

### 6.2 候选作用域类型

```text
Public
Tenant Private
User Private
Joint Investigation
Mission Working
Platform Internal
External Authority Domain
```

这些不是简单从“最小到最大”的一条格：

- Joint Space 不属于任一单租户；
- User Private 不一定能向整个租户提升；
- Mission Working 可能引用公共与私有对象，但其假设不能外溢；
- External Authority 可能公开可见，却只对特定命题有权威。

### 6.3 作用域与权威分离

公开可见不等于公共事实：

- 一篇公开博客可以公开可见；
- 其中对公司内部决议的说法仍只是 Claim；
- 私有董事会决议可能只对租户 A 可见；
- 对“租户 A 内部已批准预算”却具有更直接权威。

同样，平台能存储对象不代表平台拥有其语义决定权。

## 七、Knowledge Overlay 的正式含义

### 7.1 Overlay 不是覆盖写

`Tenant Overlay` 是在查询时，把特定租户有权使用的公共、私有、联合和任务对象合成为一个带来源说明的知识投影。

它不能：

- 修改公共对象；
- 把租户私有 Fact 提升为公共；
- 用 `last_updated_at` 覆盖其他权威域；
- 隐藏真实冲突；
- 把 Mission 假设持久化为 Tenant Fact；
- 因物理共库而共享可见性。

### 7.2 合成不是简单优先级

错误方案：

```text
Mission > User > Tenant > Public
最后写入者获胜
```

正确候选流程：

```text
1. Resolve Query Context
2. 找到当前主体有权发现的候选对象
3. 按命题、实体、有效时间、辖域和定义对齐
4. 应用当前授权、许可和用途限制
5. 选择指定 knowledge cutoff 前已处于可用认识状态的版本
6. 保留各 Authority Domain 的接受、争议和未知
7. 按查询用途应用显式 Selection Policy
8. 输出 Selected View + Alternatives + Conflict + Provenance
```

Selection Policy 可以针对：

- 法律登记问题优先显示相应监管权威域；
- 租户内部状态优先显示租户授权记录；
- 公共报道显示公共基线并附租户私有差异；
- Mission 探索允许使用工作假设，但必须标记 `Working`；
- 高风险产品不得把未裁定冲突压成单值。

这不是“谁级别高谁永远正确”，而是“哪个权威域对当前命题、用途、时间和辖域适用”。

### 7.3 Overlay 操作

租户覆盖允许：

- `add`：增加私有 Evidence、Claim、Fact、Assessment；
- `challenge`：挑战公共或私有对象；
- `specialize`：在更窄作用域或定义下形成结论；
- `annotate`：添加不改变语义权威的注释；
- `select_for_context`：在指定用途选择适用版本；
- `restrict_projection`：因权限或最小披露隐藏字段；
- `link`：在不泄露内容的前提下引用公共对象。

不允许：

- 原地改写公共版本；
- 无裁定地 `override`；
- 把不可见对象的存在作为侧信道返回；
- 将一个租户的挑战传播为另一个租户的事实。

## 八、Query Context：任何“当前”都必须有坐标

### 8.1 最小查询上下文

```text
KnowledgeQueryContext {
  principal
  tenant
  user_scope?
  joint_space?
  mission_scope?
  purpose
  jurisdiction
  world_as_of
  knowledge_as_of
  recorded_as_of
  policy_evaluation_time
  authority_selection_policy
  semantic_schema_version
  projection_mode
}
```

没有这些坐标，“当前事实”只是一句不完整问题。

### 8.2 三种常见查询

#### A. Current knowledge about past

```text
world_as_of = 2026-03-01
knowledge_as_of = now
recorded_as_of = now
```

回答：按今天所知，3 月 1 日发生了什么。

#### B. What was known then

```text
world_as_of = 2026-03-01
knowledge_as_of = 2026-03-15
recorded_as_of = now
projection_mode = epistemic_reconstruction
```

回答：按现在保留的历史记录，还原该权威域在 3 月 15 日已经知道和接受什么。

#### C. System state as recorded then

```text
world_as_of = 2026-03-01
recorded_as_of = 2026-03-15
knowledge_as_of <= recorded_as_of
projection_mode = transaction_replay
```

回答：系统在 3 月 15 日的提交历史里实际保存了什么。

B 与 C 不同：资料可能已保存但尚未授权给该权威域，也可能已被人类知道但尚未进入系统。

### 8.3 禁止不可能坐标

默认拒绝或显式标记：

- `knowledge_as_of > recorded_as_of` 的事务回放；
- 使用未来才产生的 Revision 回答过去知识；
- 以当前模型重算后冒充当时原始结果；
- 使用历史授权恢复当前已撤销主体的访问；
- 把未知时区和日期精度强行排序。

## 九、历史回放与当前授权

### 9.1 两道授权门

历史查询至少经过：

```text
Current Authorization Gate
→ 当前主体今天是否有权请求历史回放

Historical Projection Policy
→ 在获准后，按哪个历史政策和知识边界重建
```

`policy_as_of = 2025` 只能决定历史投影如何解释，不能让今天已离职的用户恢复 2025 年权限。

### 9.2 历史策略的两种模式

- `historical_behavior_replay`：还原当时系统会如何显示或决定；
- `current_policy_safe_reconstruction`：按当前更严格规则裁剪后展示历史。

输出必须声明采用哪种模式。敏感内容通常采用第二种；合规审计可在额外授权下使用第一种。

### 9.3 权限撤销与知识历史

权限撤销：

- 不改写对象曾经存在；
- 不证明用户忘记已经看到的内容；
- 阻止后续访问和派生；
- 触发缓存、索引和工作副本失效；
- 在合法范围内保留最小审计。

系统不能承诺“收回人类已经知道的知识”。

## 十、迟到、回填和重解释

### 10.1 Late-arriving Evidence

迟到 Evidence 可能描述很早的现实：

```text
event valid time = 2 月 1 日
source authored = 1 月 28 日
acquired / recorded = 5 月 10 日
Tenant A accepted = 5 月 12 日
```

正确行为：

- 新建今天记录的版本；
- 有效时间指向 2 月；
- 认识时间从 5 月开始；
- 重算当前对 2 月的知识；
- 不改写 2～5 月期间的历史认识；
- 对受影响产品按其知识截点和产品承诺判断 Update 或 Correction。

### 10.2 Retroactive Decision

法律、合同或组织决定可能今天作出、追溯生效：

- Decision time；
- stated effective time；
- recorded time；
- acceptance time；

必须分别保存。追溯生效不证明历史主体当时已经知道，也不自动抹除当时合法行动。

### 10.3 Reinterpretation

本体、定义、模型或法规解释变化时：

```text
same historical Snapshot
+ new semantic schema / interpretation policy
→ new derived Claim / Assessment
```

必须标为“按当前语义重解释”，不能冒充原始认识。

### 10.4 Late Data 与 Product 纠正

迟到资料到达后，旧产品至少评估两个问题：

| 维度 | 问题 |
|---|---|
| World accuracy | 按今天所知，旧版对现实的陈述是否错误 |
| Epistemic/process correctness | 在旧版知识截点，系统是否正确使用了当时有权获得的资料并诚实表达不确定性 |

候选处理：

- 当时资料不可得、旧版明确知识截点且表达审慎：`Update / Supersession`，必要时通知，不一定归责为 Correction；
- 当时资料已可得但流水线漏用、隐藏反证或截点虚假：`Correction` + 质量事件；
- 无论是否归责，若旧版继续使用会造成实质风险，都必须限制或通知；
- 历史审计同时保留“当时为何如此判断”和“现在知道它不准确”。

## 十一、知识快照、检查点与可重建性

### 11.1 不全量物化每一时刻

无限保存每个对象、每个租户、每个时间点的完整视图不可行。候选策略：

```text
不可变 Revision / Epistemic / Decision 日志
+ 周期性或风险触发检查点
+ 已发布 Product Edition 的精确 Manifest
+ 确定性查询规则和语义版本
= 可重建历史投影
```

### 11.2 何时必须检查点

- 高影响 Product 发布；
- 高风险 Decision 或外部 Action；
- 事实大规模裁定或撤回；
- 租户/联合空间关闭；
- 语义 Schema、Selection Policy 或模型大版本切换；
- 数据迁移、保留或删除前；
- 法律留置与监管要求；
- 动态仪表板被用于正式决策时。

### 11.3 Knowledge Snapshot

`Knowledge Snapshot` 是派生检查点候选：

```text
KnowledgeSnapshot {
  snapshot_id
  query_context
  authority_domains
  input_revision_watermarks
  selected_object_refs[]
  alternative_and_conflict_refs[]
  schema_and_policy_versions
  created_at
  completeness
  reproducibility_level
}
```

它不取得新的 Fact 权威。

### 11.4 可重建等级

| 等级 | 含义 |
|---|---|
| Exact | 原版本、规则、策略和依赖均可精确回放 |
| Semantically Equivalent | 实现不同，但领域结果可证明等价 |
| Partial | 部分内容因删除、外部不可用或缺失日志无法恢复 |
| NonReconstructible | 不能可信还原，只能说明缺口 |

系统不得把当前模型重跑结果标为 Exact 历史结果。

## 十二、删除、保留与历史真实性

### 12.1 删除会限制重建

合法删除、许可到期、密钥销毁或外部源消失可能使历史无法完整还原。

正确表达：

- 保留允许的最小 tombstone；
- 记录删除权威、原因、范围和时间；
- 更新 Knowledge Snapshot 的可重建等级；
- 对受影响 Product 和审计声明缺口；
- 不从其他租户私有副本偷偷恢复。

### 12.2 历史真实性不高于合法边界

“为了审计永久保留一切”不是合法架构。历史可重建与隐私、许可、地域和删除义务冲突时，必须通过治理决定选择最小合法证明，而不是让数据底座自行裁定。

## 十三、缓存、索引与物化视图

### 13.1 缓存键必须完整

任何 Knowledge Projection 缓存至少绑定：

```text
tenant / principal class
purpose
scope set
world_as_of
knowledge_as_of
recorded_as_of
authority selection policy
schema version
policy version
input revision watermark
```

遗漏租户或用途会泄露，遗漏时间会给出错误历史，遗漏版本会造成陈旧。

### 13.2 失效触发

- 新 Revision；
- Epistemic Record 状态变化；
- Fact challenge / retract；
- Entity split / merge revision；
- Scope / Grant / License 变化；
- Selection Policy 或 Schema 变化；
- Joint Space 成员变化；
- Mission 结束；
- 删除或地域迁移。

失效必须按作用域路由，不得广播私有对象详情。

### 13.3 防止“缓存成为事实库”

缓存和搜索索引是可重建投影：

- 不拥有语义权威；
- 不可成为无血缘 Product 输入；
- 与权威版本不一致时失效或降级；
- 无法确认 freshness 时不能假装当前。

## 十四、联邦与外部知识

外部知识节点可能只提供：

- 当前快照；
- 带版本的查询；
- 签名断言；
- 有限历史；
- 无法验证的“最后更新”。

天机阁必须保存：

- 外部 authority domain；
- query and response time；
- remote version / watermark；
- freshness guarantee；
- signature / integrity；
- allowed purpose；
- 是否可缓存和再分发；
- 历史回放能力。

外部节点说“当前”时，天机阁不能假设它与本地 `now`、记录水位或知识截点相同。

## 十五、多租户知识合成示例

同一命题：

> 某公司 CEO 是谁？

可能存在：

```text
Public Authority:
  Registry Fact → CEO = 张三，valid until unknown

Tenant A:
  Private board resolution → 李四自 3 月 1 日起代理

Tenant B:
  无私有资料

Joint Investigation:
  Unresolved Claim → 张三仍保留法定代表权限

Mission M:
  Working Hypothesis → 李四将在 4 月正式接任
```

输出：

- 公共查询：显示登记口径的张三；
- 租户 A 内部运营查询：显示李四代理，并附登记口径差异；
- 法律签署权查询：按相应法律权威域，不自动使用运营角色；
- 租户 B：不能知道租户 A 的私有决议存在；
- Mission M：可以推演李四接任，但不能把 Hypothesis 写为 Tenant Fact。

不存在一个脱离角色、辖域、时间和用途的全局 `CEO = ?` 槽。

## 十六、Agent 与 Tool 的边界

### 16.1 Tool / Service

- 二时态区间维护和不可变 Revision；
- 查询坐标校验；
- 时间精度、时区和区间运算；
- Scope / Grant / Purpose 强制；
- 候选版本发现与作用域过滤；
- Selection Policy 的确定性部分；
- 版本图、watermark 和检查点；
- 缓存键、失效和租户隔离；
- 历史回放与可重建等级计算；
- 迟到数据影响候选发现。

### 16.2 Agent / Human

- 判断迟到资料是否改变历史世界解释；
- 解决不同权威域、定义和辖域的适用性；
- 解释冲突而非粗暴选一；
- 判断当前知识回看与当时认识之间的差异；
- 判断迟到资料导致 Update、Correction 还是仅注释；
- 评估重解释是否语义等价；
- 在合法删除与审计需求间形成治理建议。

### 16.3 Token 经济

```text
确定性时间/作用域查询
→ 生成紧凑 Knowledge Context Package
→ Agent 只处理冲突、适用性和解释
→ Tool 固化版本、状态和缓存失效
```

Agent 不应扫描全量版本历史，也不应凭 Prompt 猜测租户可见范围。

## 十七、权力与职责

| 职责 | 主要所有者 | 明确不拥有 |
|---|---|---|
| Revision、二时态、检查点与重建 | 数据底座面 | Fact 接受和作用域授权 |
| Evidence、Claim、Fact 的时间语义 | 数据认知面 + 对应权威域 | 平台权限策略 |
| Assessment 的 as-of / cutoff | 情报研判面 | 修改底层 Fact 时间 |
| Product Edition 的知识截点 | 情报产品面 | 伪造当时可用信息 |
| Scope、Grant、Purpose、地域与策略版本 | 平台控制面 | 领域事实权威 |
| 争议、例外和高影响历史回放 | 智能治理面 / 人类 | 静默改写历史 |
| Query Context 捕获与解释呈现 | 人机交互面 / 皇帝 | 自行扩大权限 |
| 时间、辖域和角色适用规则 | 领域能力面 | 跨域全局优先级 |
| 历史回放 Task 与缓存失效 | 智能执行与运行面 | 语义选择权 |
| Schema / Selection Policy 演进 | 能力演进 + 治理 + 平台发布 | 用当前语义冒充历史语义 |

## 十八、失败模式

### 18.1 只有 updated_at

无法区分现实何时发生、系统何时知道以及谁何时接受。

### 18.2 全局 known_at

把不同租户、公共域和任务域的认识错误合并。

### 18.3 迟到数据回填记录时间

制造“系统早就知道”的虚假历史。

### 18.4 Tenant Overlay 使用 last-write-wins

私有判断污染公共基线，冲突被静默隐藏。

### 18.5 历史回放使用当前最新对象

产品和决定无法复现，造成事后诸葛式审核。

### 18.6 历史策略恢复历史权限

已撤销用户通过指定旧时间读取当前无权数据。

### 18.7 所有视图全部物化

版本与租户组合爆炸，成本失控且失效不可维护。

### 18.8 所有视图都实时重算

无法保证历史复现、查询性能和产品冻结。

### 18.9 当前模型重跑冒充当时输出

能力升级反向改写系统历史。

### 18.10 缓存键缺少 scope / purpose / cutoff

跨租户泄露或返回错误时间截面的知识。

### 18.11 公开可见等于公共 Fact

跳过 Claim、Evidence、Authority Domain 和接受语义。

### 18.12 删除后静默声称 Exact replay

掩盖历史不可重建和合规删除的真实取舍。

## 十九、候选不变量

- 每个有现实适用性的对象必须表达有效时间或明确“不适用/未知”；
- 每个持久 Revision 必须有系统控制的记录时间；
- 认识状态必须绑定 Authority Domain，禁止无主体全局 known_at；
- observed、available、considered、accepted 不得合并；
- 迟到资料可以改变当前对过去的认识，但不能改写过去的认识历史；
- Object Identity 与不可变 Revision 必须分离；
- 跨作用域 Revision 不建立隐含全局总序；
- Scope 必须显式，不能从数据库位置推断；
- Visibility、Ownership、Semantic Authority、Purpose 和 Jurisdiction 必须可分别表达；
- Tenant Overlay 通过合成与选择形成，不改写 Public 对象；
- Mission Working 对象不能未经接受提升为 Tenant / Public Fact；
- 任何“当前”查询必须有 principal、purpose、时间和权威选择坐标；
- 历史回放先通过当前授权门；
- 当前重解释必须标注当前 Schema / Model，不能冒充当时结果；
- Product Edition 必须固定 knowledge cutoff 和输入 Revision；
- 缓存、索引和 Knowledge Snapshot 不取得 Fact 权威；
- 合法删除造成的不可重建必须显式降级；
- 多租户缓存和影响路由不得泄露其他租户对象存在；
- 外部节点的 `current` 必须带远端版本、水位和新鲜度；
- 时间精度和不确定性必须保留，不得伪造精确值。

## 二十、S8 对对象分类的候选裁定

### 20.1 正式支持

- `Object Revision` 是跨核心对象共享的版本语义，不是新的世界对象家族；
- `Current Fact / Current State / Canonical Entity` 是带 Query Context 的 View；
- `Tenant Overlay` 是作用域合成 View，不是物理副本或对象类型；
- `Epistemic Record` 是“权威域—对象修订”的有身份认识记录候选；
- `Knowledge Snapshot` 是可重建性检查点 / 派生 Snapshot 候选；
- `Selection Policy` 属于平台控制对象，不属于情报对象主干；
- `Query Context` 是请求/投影值对象；
- `Authority Domain` 是权威与作用域模型中的正式概念。

### 20.2 认识时间裁定

S8 支持认识时间独立建模，但拒绝单一全局第三时间轴：

```text
Object Revision:
  valid time + record time

Epistemic Record:
  authority domain + epistemic state + effective interval
```

S9 将判断 Epistemic Record 是否进入永久核心对象目录，或作为 Fact Acceptance、Verification 和治理记录的共享上位语义。

## 二十一、开放问题

- Epistemic Record 是否统一覆盖 Fact Acceptance、Assessment Acceptance 和 Product Awareness；
- Authority Domain 应是正式对象、配置实体还是作用域值对象；
- 对同一命题的大量 Fact Acceptance 是否应与 Fact 内容拆分；
- 世界有效时间修订时，旧 Revision 的 valid interval 是关闭、纠正还是并存争议；
- 不同时间精度的区间比较应如何表达 `possibly overlaps`；
- Knowledge Snapshot 的风险分级检查点策略；
- 历史模型、Prompt、Tool 不可执行时，何时仍可判定语义等价；
- 当前授权门与法律留置、调查特权冲突时的治理顺序；
- Joint Space 关闭后，成员各自能保留哪些派生知识；
- 匿名私有 Evidence 如何影响公共结论而不泄露存在；
- Scope / Authority / Purpose 组合是否需要通用 Policy Decision Record；
- 动态 Product 的访问时检查点何时形成正式 Edition。

## 二十二、验收门槛

- 能区分现实时间、系统记录时间、来源时间和权威域认识时间；
- 能回答“现在看过去”与“还原当时所知”并解释差异；
- 迟到资料不会被回填成当时已知；
- 同一命题可在公共、租户、联合和任务域保留不同状态；
- 知识合成不会使用全局 last-write-wins；
- 查询输出包含选中版本、替代版本、冲突、时间和 provenance；
- 历史回放不会绕过当前权限；
- Product Edition 可以精确复现其知识截点；
- 当前模型重解释与历史原始结果可机器区分；
- 删除或许可失效后能够诚实声明 Partial / NonReconstructible；
- 缓存与索引不会跨租户、用途或时间截点复用；
- 场景中能够判断迟到资料导致 Update、Correction 还是无影响；
- 不需要为每个租户每个时刻全量物化完整世界。

## 二十三、与 Accepted 骨架的关系

本模型深化现有的：

- 公共基线 + 租户私有覆盖；
- 双时态与版本保留；
- 当前授权强制；
- Product Edition 知识截点；
- 数据底座的版本、血缘与审计职责。

它不改变十个一级面。S9/S10 评审通过后，Epistemic Record、Knowledge Snapshot 和查询坐标已进入 Accepted 基线，并同步到正式文字架构图。

## 二十四、关联

- [v0002 迭代入口](../02-architecture-iterations/v0002-intelligence-semantics/README.md)
- [统一语言与对象分类法](../08-reference-models/unified-language-and-object-taxonomy.md)
- [Source、Document、Snapshot 与 Evidence 模型](./source-document-snapshot-evidence-model.md)
- [Claim、争议、Fact 与裁定模型](./claim-fact-and-adjudication-model.md)
- [Entity、身份、别名与消歧模型](./entity-identity-and-resolution-model.md)
- [Relationship、Event、State、Diff 与 Change 模型](./world-state-event-and-change-model.md)
- [产品、告警、反馈与影响纠正模型](./intelligence-product-alert-feedback-correction-model.md)
- [SCN-0016：迟到资料、差异知识时间与历史重建](../05-scenarios-and-flows/SCN-0016-late-evidence-divergent-knowledge-time-and-reconstruction.md)
- [租户、身份与授权](../03-current-architecture/cross-cutting/01-tenancy-identity-authorization.md)
