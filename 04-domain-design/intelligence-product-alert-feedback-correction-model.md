# Intelligence Product、Alert、Feedback 与影响纠正语义模型

状态：Proposed

形成日期：2026-07-27

所属迭代：`v0002-intelligence-semantics / S7`

## 一、问题

天机阁的价值链不能停在形成 Fact、Assessment、Forecast 和 Risk。结论还要：

- 被组织成适合特定受众和目的的产品；
- 经过审核与发布决定；
- 在权限和用途约束下分发；
- 被实际送达、阅读、确认或忽略；
- 接收用户、专家、系统和现实结果的反馈；
- 在上游知识变化时定位受影响结论；
- 对已经外发的错误或过时内容履行纠正义务。

如果把这些过程都压成一个 `Product.status`，系统将无法回答：

- 内容已经写好，但是否获准发布；
- 获准发布的版本是否真的送达；
- 同一产品的 PDF、API 和仪表板是否表达同一版结论；
- 某条撤回 Fact 是报告的关键前提，还是仅出现在参考资料中；
- 一个产品需要纠正、更新、替代、撤回，还是完全不受影响；
- 哪些收件人看过旧版，哪些只收到新版；
- 用户反馈是新的 Evidence、主张、偏好、投诉，还是能力演进信号；
- 产品发布是否改变了现实，从而污染 Forecast 的事后评分。

S7 的核心不是报告排版，而是建立：

> 从知识依赖、产品冻结、发布授权、分发暴露、反馈接收，到影响分析和纠正闭环的可审计语义。

## 二、总模型

```text
Fact / Change / Assessment / Forecast / Risk / Recommendation
                              │
                              ▼
                     Product Composition
                              │
                    ┌─────────┴─────────┐
                    ▼                   ▼
             Product Identity      Product Edition
                                    内容清单与依赖冻结
                                             │
                                      Review / Decision
                                             │
                                             ▼
                                     Product Release
                                      获准发布的版本
                                             │
                            ┌────────────────┼────────────────┐
                            ▼                ▼                ▼
                      Rendering         Distribution      API Projection
                   PDF / HTML / JSON       Attempt
                                             │
                                             ▼
                                    Delivery / Receipt
                                             │
                              Feedback / Decision / Action
                                             │
                                             ▼
                                           Effect

上游对象新版本 / Challenge / Supersession / Retraction
                              │
                              ▼
                         Change Notice
                              │
                              ▼
                          Impact Case
                              │
             ┌────────────────┼──────────────────┐
             ▼                ▼                  ▼
         Unaffected      Review Required   Invalid / Correction Required
                                                   │
                                                   ▼
                              Correct / Update / Supersede / Withdraw / Retract
                                                   │
                                                   ▼
                                      Recipient Correction Closure
```

必须始终保持：

```text
Intelligence Product ≠ Product Edition
Product Edition ≠ Product Release
Product Release ≠ Rendering
Distribution Attempt ≠ Delivery
Delivery ≠ Consumption
Risk ≠ Alert
Alert ≠ Delivery Channel Message
Feedback ≠ Evidence
Feedback ≠ Fact
Correction ≠ Silent Overwrite
Dependency ≠ Any Citation
```

## 三、产品不是底层知识的副本

### 3.1 Intelligence Product

`Intelligence Product` 是为持续目的、受众和产品承诺建立的逻辑交付身份。

建议最小字段：

```text
IntelligenceProduct {
  product_id
  tenant_scope
  product_type
  purpose
  intended_audience_class
  standing_questions[]
  product_owner
  semantic_owner
  correction_policy_ref
  default_distribution_policy_ref
  lifecycle_status
}
```

它回答：

- 这是什么产品；
- 为什么长期存在；
- 服务哪类问题和受众；
- 谁对产品语义和纠正义务负责。

它不直接回答某次发布说了什么。

例如：

- “甲企业持续尽调档案”是一个逻辑产品；
- “2026-07-27 第 12 版尽调报告”是一个 Product Edition；
- “经门下复核后允许向董事会分发的第 12 版”是 Product Release。

### 3.2 Product Type 不是独立知识对象

报告、简报、档案、图谱、时间线、风险卡片、机会卡片和仪表板是产品类型或呈现模式，不应各自复制一套 Fact / Assessment 语义。

产品类型可以约束：

- 必填章节；
- 更新频率；
- 审核门；
- 最大陈旧度；
- 引用覆盖；
- 渠道和格式；
- 纠正时限。

但不能改变底层对象的真实性或权威。

## 四、Product Edition：冻结“当时交付了什么”

`Product Edition` 是某个逻辑产品在指定信息截点形成的不可变内容版本。

建议最小字段：

```text
ProductEdition {
  edition_id
  product_id
  edition_number
  as_of_valid_time
  knowledge_cutoff_time
  generated_at
  tenant_scope
  purpose
  audience_assumptions
  content_manifest
  dependency_manifest
  uncertainty_summary
  omitted_information_manifest
  generator_versions
  review_status
  supersedes_edition_id?
}
```

### 4.1 Content Manifest

Content Manifest 至少列出：

- 产品章节和结构；
- 每个结论引用的对象 ID 与精确版本；
- 原文引用、图表和计算产物；
- 生成 Agent、Tool、模型、Prompt、模板和策略版本；
- 信息截点与数据新鲜度；
- 人工编辑与审核记录；
- 因权限、许可、最小披露或篇幅而省略的内容。

产品不能只保存一段最终文本。否则无法复现当时版本，也无法判断上游变化究竟影响哪一句。

### 4.2 Dependency Manifest

每条依赖边必须有语义类型：

| 依赖类型 | 含义 | 上游变化的默认处理 |
|---|---|---|
| `premise` | 产品结论成立所必需的前提 | 进入实质重评 |
| `supports` | 提高结论可信度的支持项 | 重算证据充分性 |
| `contradicts` | 产品主动呈现的反证 | 检查结论是否反而增强或叙述失真 |
| `bounds` | 限定时间、范围、定义或适用条件 | 检查边界是否变化 |
| `context` | 解释背景，不参与核心推断 | 通常只检查叙述 |
| `quoted_as_claim` | 仅报告某主体曾如此主张 | 上游 Fact 撤回不必自动删除该历史 Claim |
| `derived_from` | 直接派生关系 | 按派生规则重算 |
| `visualizes` | 图表或呈现投影 | 重渲染，不自动改变结论 |
| `negative_evidence` | 缺失或反向证据 | 检查是否改变不确定性 |
| `method_input` | 模型、规则、参数或基准 | 方法版本变化时重算 |

只有声明 `premise`、`derived_from` 并不够。边还应记录：

- 使用了上游对象哪个版本；
- 作用于哪个章节、命题或输出；
- 依赖强度与必要性；
- 替代支持是否存在；
- 创建边的主体与方法；
- 审核状态；
- 作用域与用途限制。

### 4.3 冻结与动态投影

天机阁同时需要：

- `Frozen Edition`：可审计地保存当时发布内容；
- `Current Projection`：面向界面动态显示最新有效结论。

两者不能混写：

```text
Frozen Edition
→ 回答“当时发布了什么”

Current Projection
→ 回答“现在按当前知识应展示什么”
```

动态仪表板也必须定期形成可重建检查点；否则无法证明历史上某个时间点向用户显示了什么。

## 五、审核、发布与表达分离

### 5.1 Product Review

产品审核至少检查：

- 引用覆盖和依赖边是否正确；
- 事实、推断、预测和建议是否明确标型；
- 不确定性和反证是否被诚实表达；
- 受众、目的和行动风险是否匹配；
- 权限、许可、地域和最小披露；
- 是否存在过期、争议、挑战或待重评对象；
- 更正机制和接收者追踪是否可用。

审核通过不等于发布。

### 5.2 Publication Decision

发布是治理决定，不是文件生成器的副作用。

```text
Edition Ready
→ Review Package
→ Publication Decision
   ├── Approve
   ├── ApproveWithConditions
   ├── ReturnForRevision
   ├── RestrictAudience
   ├── Delay
   └── Reject
```

高影响产品必须记录：

- 决定主体；
- 审核依据；
- 允许的受众、用途、渠道和时间；
- 必须附带的警告；
- 有效期和重新审核触发器；
- 是否允许机器继续分发。

### 5.3 Product Release

`Product Release` 是“某个 Product Edition 在某组发布条件下获准对外使用”的不可变记录。

同一 Edition 可以有多个 Release，例如：

- 完整内部版；
- 经最小披露处理的客户版；
- 可公开摘要版。

不同 Release 必须具有独立作用域和内容披露清单，但不得伪装成同一份无差别报告。

### 5.4 Rendering

PDF、HTML、邮件正文、JSON、图像和语音是 Rendering / Representation。

Rendering 必须引用：

- release_id；
- edition_id；
- 模板和渲染器版本；
- 内容哈希；
- 生成时间；
- 水印、敏感标记和许可约束。

格式错误可以要求重新渲染，不应自动创建新知识版本；语义内容变化必须创建新 Edition。

## 六、分发、送达与消费

### 6.1 Distribution Attempt

每次分发是有身份的尝试：

```text
DistributionAttempt {
  distribution_id
  release_id
  audience_selector
  resolved_recipients[]
  channel
  requested_at
  authorization_decision_ref
  policy_snapshot_ref
  status
  retry_of?
}
```

在实际发送时必须重新判定：

- 接收者身份和租户；
- 当前授权；
- 数据作用域与用途；
- 来源许可和地域；
- 产品是否仍可发布；
- 是否存在未处理的纠正或撤回；
- 渠道是否满足敏感等级。

生成时获准，不代表三小时后的发送仍获准。

### 6.2 Delivery Record

`Delivery Record` 记录渠道能够证明的送达结果：

```text
Queued → Sent → Delivered / Failed / Expired / Cancelled
```

它不能证明用户已经阅读或理解。

### 6.3 Receipt 与 Consumption Signal

- `Receipt`：接收主体明确确认收到；
- `Consumption Signal`：打开、查看、下载、查询或 API 拉取等观测；
- `Acknowledgement`：接收主体确认已理解或处理指定告警；
- `Decision / Action`：接收者在其权威域内采取的独立对象。

点击率不是决策，阅读也不是接受结论。

隐私与最小监测策略必须限制消费遥测，不能为了证明送达而建立无限用户监控。

## 七、Alert：可行动的时效性交付对象

### 7.1 Alert 的正式含义

`Alert` 是满足监测、风险或变化触发条件后，面向指定责任主体形成的、有时效要求的可跟踪通知对象。

```text
Alert {
  alert_id
  tenant_scope
  triggering_object_refs[]
  trigger_rule_ref
  alert_subject
  audience_or_duty_owner
  severity
  confidence
  urgency
  valid_from
  expires_at
  required_response?
  deduplication_key
  correlation_key?
  status
}
```

Alert 必须区分：

- `severity`：若判断成立，潜在影响多大；
- `confidence`：触发判断有多可信；
- `urgency`：多快需要处理；
- `priority`：结合责任、资源和策略后的处理顺序。

高严重度、低置信度是合法状态，不能被压成一个“红色分数”。

### 7.2 Trigger 不等于 Alert

```text
Signal / Change / Risk / Rule Match
→ Trigger Evaluation
→ Suppress / Correlate / Create Alert / Escalate
```

规则命中可能因为：

- 去重；
- 抑制窗口；
- 已存在关联 Alert；
- 低于租户阈值；
- 权限或许可不允许分发；
- 缺少最低证据；
- 已过时；

而不创建新 Alert。

抑制也必须留下决定记录，避免“没有告警”被误解为“没有检测到”。

### 7.3 Alert Thread / Incident Correlation

同一持续风险可能每分钟产生新 Signal。系统应通过关联键把多个触发聚合到同一 Alert Thread 或外部事件关联中，而不是制造告警风暴。

关联只影响通知管理，不合并底层 Signal、Change、Risk 或 Evidence。

### 7.4 Alert 生命周期

候选生命周期：

```text
Candidate
→ Qualified
→ Approved / Suppressed
→ Dispatched
→ Delivered / DeliveryFailed
→ Acknowledged
→ Monitoring
→ Resolved / Expired / Retracted
→ Reopened
```

必须避免：

- `Delivered` 被当成风险已解决；
- `Acknowledged` 被当成建议已采纳；
- `Resolved` 被当成底层 Risk 永久关闭；
- `Retracted` 删除错误告警历史。

## 八、Feedback：有目标的输入，不是真相捷径

### 8.1 Feedback 的正式含义

`Feedback` 是某主体针对指定对象、版本、交付、任务或能力结果给出的有来源回应。

```text
Feedback {
  feedback_id
  author_principal
  tenant_scope
  target_ref
  target_version
  feedback_type
  content_or_payload
  submitted_at
  claimed_observation_time?
  author_role_and_basis
  visibility
  requested_remedy?
  validation_status
}
```

Feedback 类型至少区分：

- 事实纠错；
- 身份纠错；
- 缺失信息；
- 结论异议；
- 预测结果反馈；
- 风险处置结果；
- 产品相关性、清晰度和可用性；
- 分发、权限或隐私投诉；
- 偏好和订阅调整；
- 能力质量、成本或失败反馈。

### 8.2 Feedback 不是 Evidence 或 Fact

用户说“这个人不是他”，首先创建 Feedback。随后可以：

```text
Feedback
→ 提取 Claim
→ 请求或锚定 Evidence
→ 验证
→ Identity Resolution / Fact Adjudication
```

只有当反馈携带可审计材料并满足 Evidence 规则时，其中的材料才能成为 Evidence；反馈作者的陈述仍是 Claim。

系统不能因为用户地位高、次数多或措辞强烈就自动改写 Fact。

### 8.3 Feedback 的多个去向

```text
事实或身份纠错
→ 数据认知 / 事实裁定

研判异议
→ 研判复核 / Dissent

送达或表达问题
→ 产品与交互改进

真实结果
→ Forecast Resolution / Risk Outcome

质量或能力缺口
→ Evolution Evidence Candidate

偏好
→ Tenant / User Preference
```

同一 Feedback 可以产生多个有类型派生对象，但不能被一个“用户反馈库”吞并所有语义。

### 8.4 反馈投毒与权力集中

Feedback 必须保留：

- 来源身份和可能利益冲突；
- 机器人、协同行为和重复提交；
- 权限范围；
- 独立来源数量；
- 处理决定；
- 是否被用于训练、评测或生产判断。

产品生成器不能挑选有利反馈评估自己；反馈数量也不能代替代表性。

## 九、Action 与 Effect 的边界

### 9.1 为什么需要记录

产品和告警会改变现实：

```text
Product / Alert
→ Human or External Decision
→ Authorized Action
→ Observed Outcome
→ Effect Assessment
```

如果不记录这条链：

- 成功预警促成规避后，Forecast 会被误判为错误；
- 市场报告引发共同交易后，系统会把自己造成的变化当作独立验证；
- 用户没有行动时，系统无法区分未送达、不相信、无权行动或资源不足；
- Recommendation 的效果无法复盘。

### 9.2 Action

Action 是某权威主体实际执行或明确授权执行的行为，不是 Recommendation 的状态。

天机阁只记录其有权观察和保存的外部 Action；无权时应保存“不知道是否行动”，不能根据结果反推。

### 9.3 Outcome 与 Effect

- `Outcome Observation`：观察到后续发生了什么；
- `Effect Assessment`：判断某 Action 或 Product 对结果贡献了什么；
- `Causal Claim`：某主体声称存在因果；
- `Effect` 不应在没有因果依据时伪装为客观事实。

因此 S7 不把 Effect 简单定义为世界事实。它是带因果方法、反事实、替代解释和不确定性的 Assessment 家族候选。

## 十、知识谱系与影响图

### 10.1 Provenance、Lineage、Dependency、Causality 分离

| 图关系 | 回答的问题 |
|---|---|
| Provenance | 这个材料或陈述来自谁、何处、何时 |
| Derivation Lineage | 这个对象由哪些输入和转换生成 |
| Semantic Dependency | 这个结论是否依赖另一个对象成立 |
| Citation | 产品在哪里提到或展示某对象 |
| Governance Lineage | 谁审核、接受、发布、撤回 |
| Distribution Lineage | 哪个版本发给了谁、是否送达 |
| Causal Lineage | 产品、决定、行动与现实效果可能如何相连 |

它们可以共享图基础设施，但不能只用一条无类型 `related_to` 边。

### 10.2 Dependency 必须固定版本

```text
Assessment A v3 --premise--> Fact F v7
Product Edition P v12 --summarizes--> Assessment A v3
Release R2 --publishes--> Product Edition P v12
Delivery D9 --delivers--> Release R2
```

引用“Fact F 当前版本”不足以复现历史。影响分析可以从 F 的新状态出发，但必须知道旧产品实际使用的是 F v7。

### 10.3 知识谱系不是无限可见图

跨租户影响查询必须：

- 在各自租户或联合空间内执行；
- 对共享物理对象返回去标识化影响计数时仍遵守策略；
- 不因知道“另一个租户有依赖”而泄露其对象、调查目标或产品；
- 由各租户自己的纠正义务处理器接收可授权的变更通知。

共享底座负责路由影响，不获得跨租户披露权。

## 十一、Change Notice 与 Impact Case

### 11.1 Change Notice

当上游对象发生可能影响消费者的状态变化时，产生结构化通知：

```text
Challenge
Supersession
Retraction
Scope Restriction
Validity Change
Entity Split / Merge Revision
Method or Model Invalidated
Rights or License Change
Security Compromise
```

Change Notice 不是广播全文。它携带：

- 变化对象和版本；
- 变化类型；
- 生效时间和认识时间；
- 权威决定引用；
- 允许披露的原因摘要；
- 建议影响等级；
- 作用域和路由约束。

### 11.2 Impact Case

每次具有实质传播可能的变化创建 `Impact Case`：

```text
ImpactCase {
  impact_case_id
  trigger_notice_ref
  affected_scope
  discovery_cursor_or_snapshot
  candidate_dependents[]
  classification_records[]
  required_actions[]
  notification_obligations[]
  owner
  deadlines
  status
}
```

它具有独立价值，因为影响发现、语义分类、重算、通知和关闭可能持续数小时至数月，并允许重开。

候选生命周期：

```text
Opened
→ Discovering
→ Classifying
→ Recomputing / AwaitingReview
→ Correcting / Notifying
→ ClosurePending
→ Closed
→ Reopened
```

## 十二、影响分类与停止规则

### 12.1 每个下游对象的候选结果

| 分类 | 含义 | 典型动作 |
|---|---|---|
| `Unaffected` | 变化与下游语义无关 | 记录理由并停止该分支 |
| `PresentationOnly` | 只影响格式、引用或措辞 | 重渲染或小版本修订 |
| `Stale` | 仍可能成立，但信息截点已过时 | 标记陈旧并排期更新 |
| `ReviewRequired` | 影响不确定，需要语义复核 | 冻结高风险用途 |
| `RecomputeRequired` | 确定性派生可重算 | 调用 Tool / Rule |
| `ConfidenceChanged` | 结论仍成立但置信或不确定性变化 | 新版本与必要通知 |
| `ScopeRestricted` | 结论只在更窄边界成立 | 限制使用和分发 |
| `Invalidated` | 核心结论不再可支持 | 禁止继续作为有效输入 |
| `CorrectionRequired` | 已发布内容存在实质错误 | 形成更正版并通知 |
| `WithdrawalRequired` | 继续分发不可接受 | 停止发布并撤回 |
| `NoLongerAuthorized` | 权限或许可而非知识发生变化 | 停止访问/分发，保留审计 |

### 12.2 不能按跳数停止

“传播三层就停止”没有语义依据。停止条件必须是：

- 依赖边不是语义依赖；
- 新版本仍满足原结论的最低证据门；
- 替代前提完整替代了撤回输入；
- 变化只影响已明确排除的时间、作用域或定义；
- 下游对象已由更新版本替代且没有旧版暴露义务；
- 权威审核明确判定无影响；
- 到达不可控外部边界，并已记录尽力通知与剩余风险。

### 12.3 也不能无限级联

影响传播应采用：

1. 版本固定的有类型依赖边发现候选；
2. 确定性规则先做范围裁剪；
3. 对关键语义边使用 Agent 或专家复核；
4. 按产品暴露、风险和纠正时限排序；
5. 每个分支留下停止理由；
6. 新发现的缺失血缘进入隔离和补链，而不是假装无影响。

## 十三、更新、更正、替代、撤回与删除

这些词必须严格分离：

| 操作 | 使用条件 | 旧版 |
|---|---|---|
| `Update` | 新信息到达，但旧版在当时信息截点并非错误 | 保留并标记新版本 |
| `Correction` | 旧版存在可识别错误或重要遗漏 | 保留、显著关联更正版 |
| `Supersession` | 新版成为默认有效版本 | 旧版不再默认展示 |
| `Withdrawal` | 停止继续分发或使用旧版 | 保留审计，可能限制访问 |
| `Retraction` | 发布主体明确表示核心结论不能继续成立 | 保留原因和责任链 |
| `Deletion` | 数据生命周期或合法删除动作 | 不替代语义纠正记录 |

删除不能用来伪装从未发布过错误产品；保留纠正审计也不能违反合法删除义务。必要时保留最小、不可反推出受保护内容的合规证明。

### 13.1 更正版不是原地覆盖

```text
Product Edition v12
  status = Corrected / Superseded
  corrected_by = v13

Product Edition v13
  correction_of = v12
  correction_reason
  changed_sections
  unchanged_sections
  knowledge_cutoff
```

接收者默认看到 v13，但审计者可按权限复现 v12。

## 十四、纠正义务与接收者闭环

### 14.1 纠正对象不只是一份新报告

需要计算：

- 哪些 Release 暴露了错误；
- 哪些 Distribution Attempt 实际执行；
- 哪些接收者已送达、已确认或可能消费；
- 哪些 API 消费者拉取了旧版；
- 哪些下游系统再次派生；
- 哪些渠道已经超出控制；
- 适用合同、监管或产品承诺要求何种通知时限。

### 14.2 Correction Notice

更正通知至少包含：

- 受影响产品与旧版；
- 哪部分错误；
- 原因和权威变化；
- 当前有效结论；
- 风险和建议是否改变；
- 用户是否需要采取动作；
- 新版位置；
- 通知时间和确认要求。

不能只发送“报告已更新”，迫使收件人自行寻找变化。

### 14.3 关闭条件

Impact Case 不能因为新版生成就自动关闭。至少需要：

- 所有已知依赖分支均有影响分类；
- 必须重算的对象已有结果；
- 需要冻结的用途已强制；
- 所有受控分发渠道已停止旧版；
- 纠正通知已按义务发送；
- 未送达、不可控复制和未知消费者已形成剩余风险；
- 必要的确认、人工升级和监管记录已完成；
- 数据、研判、产品、运行和审计状态一致。

## 十五、多租户与多仙人

### 15.1 产品与纠正作用域

同一公共 Fact 可以被多个租户产品引用，但：

- 租户 A 的 Product Edition 不向租户 B 可见；
- 公共 Fact 撤回可以触发各租户私有 Impact Case；
- 公共层只提供最小变更通知，不泄露其他租户依赖；
- 各租户使用自己的目的、风险、产品承诺和通知义务判断影响；
- Joint Investigation 的产品只在联合空间授权下纠正和分发。

### 15.2 多仙人意见不是同一 Feedback

不同仙人可以：

- 对同一 Product 提供独立 Feedback；
- 形成不同 Assessment 或 Dissent；
- 拥有不同订阅与告警阈值；
- 在各自授权范围内作出 Decision。

系统不能用“租户总体偏好”抹掉个人异议，也不能让一位仙人的接受自动成为另一位仙人的确认。

## 十六、权力与职责

| 职责 | 主要所有者 | 明确不拥有 |
|---|---|---|
| 产品组合、版本和表达语义 | 情报产品面 | Fact 与 Assessment 的接受权 |
| 发布、限制、更正和撤回决定 | 智能治理面 / 授权人类 | 底层渠道发送细节 |
| Alert 形成、生命周期与责任路由 | 情报产品面 | Risk 的研判权 |
| 用户入口与消费投影 | 人机交互面 | 产品版本真相 |
| Fact / Entity / Change 更正 | 数据认知与裁定机制 | 产品发布权 |
| Assessment / Forecast / Risk 重评 | 情报研判面 | 外部 Decision |
| 血缘、依赖、版本和影响查询 | 数据底座面 | 语义影响裁定权 |
| 分发和重算 Task 执行 | 智能执行与运行面 | 发布授权 |
| 权限、许可、地域和渠道策略 | 平台控制面 | 具体结论内容 |
| 高影响复核、封驳和例外 | 门下 / 智能治理面 | 自行改写证据 |
| 领域相关性与纠正严重度 | 领域能力 | 跨域全局发布权 |
| 反馈转化为演进证据 | 能力演进面 | 将反馈直接部署为能力 |

## 十七、Agent 与 Tool 的边界

### 17.1 Tool / Service 优先

- Product Edition 与 Manifest 版本管理；
- 精确版本引用和依赖图查询；
- 模板填充、格式渲染和内容哈希；
- 权限与许可强制；
- 收件人解析、分发、重试和送达记录；
- Alert 规则求值、去重、抑制和关联候选；
- 影响候选发现和确定性裁剪；
- 确定性派生重算；
- 纠正通知扇出与确认追踪；
- 生命周期、时限和审计检查。

### 17.2 Agent / Human 适用

- 为受众和目的组织复杂结论；
- 检查叙事是否越过 Evidence；
- 判断撤回前提对综合 Assessment 的实质影响；
- 区分更正、更新、限制和撤回；
- 处理依赖缺失、冲突和替代证据；
- 解释不确定性、Dissent 和行动含义；
- 对高影响产品独立审核；
- 在不可控外部传播时判断通知和剩余风险。

### 17.3 Token 经济

不能让 Agent 遍历整张依赖图：

```text
图查询 + 规则裁剪
→ 形成紧凑 Impact Package
→ Agent 只审语义不确定和高影响分支
→ 确定性执行器落实状态、重算和通知
```

Feedback 也先结构化分类、去重和路由，再把需要判断的内容交 Agent。

## 十八、失败模式

### 18.0 与现有 Accepted 状态机的关系

当前[风险、告警与产品状态机](../08-reference-models/state-machines/03-risk-alert-and-product.md)仍是生效基线，S7 不在评审前静默替代它。

S7 提出的语义拆分可先理解为对现有状态的内部投影：

```text
Product.Draft / UnderReview / Approved
→ 主要对应 Product Edition 的内容与审核状态

Product.Published
→ 表示该 Edition 至少存在一个有效 Product Release

Product.CorrectionRequired / Superseded / Withdrawn
→ 由 Edition 内容状态与 Release 发布状态组合形成的兼容视图

Alert.Created
→ 已经通过候选触发评估并创建正式 Alert

Alert.Queued / Delivered / DeliveryFailed
→ 可由 Alert 状态与 Delivery Record 投影
```

候选模型中的 `Candidate`、`Qualified`、`Approved`、`Dispatched`、`Monitoring`、`Retracted` 以及 Edition / Release 分离，需要在 S9/S10 评审通过后再修改正式状态机和当前架构图。此前所有新增状态只属于 Proposed 模型。

### 18.1 只保存最终报告文本

后果：无法复现依赖、无法局部纠正、只能重写整份报告。

### 18.2 把任何引用都当成强依赖

后果：上游一处撤回触发告警风暴和全库无意义重算。

### 18.3 只沿直接父子传播

后果：漏掉跨产品复制、API 派生、图表和外部下游。

### 18.4 更正即覆盖

后果：无法证明旧版曾经发布，也无法知道谁看过错误内容。

### 18.5 生成即发布

后果：内容生成器取得隐含发布权，审核与权限失效。

### 18.6 已发送即已处理

后果：系统虚构用户已知、已理解或已采取行动。

### 18.7 用户反馈即事实

后果：权威用户、刷票或投毒者可以绕过 Evidence 和裁定。

### 18.8 Alert 与 Risk 合并

后果：一个风险无法对应多个受众和生命周期，告警关闭也会错误关闭风险。

### 18.9 产品影响现实却不进入血缘

后果：预测评分、自我实现、干预效果和建议评估全部失真。

## 十九、候选不变量

- Product 必须服务明确目的和受众；
- Product Edition 必须冻结对象精确版本和知识截点；
- 产品文本不能成为底层 Fact 的无血缘副本；
- 生成、审核、发布、分发、送达和消费必须分离；
- 每次分发在副作用发生前重新检查授权与许可；
- Alert 必须引用触发对象，但不等于 Signal、Change 或 Risk；
- Alert severity、confidence、urgency 和 priority 不得压成单值；
- Feedback 必须引用目标和版本，且不自动成为 Evidence 或 Fact；
- 上游纠正不得静默覆盖下游对象；
- 影响传播必须使用有类型、固定版本的依赖边；
- 每个传播分支必须记录继续或停止理由；
- 已发布错误的纠正义务由实际暴露决定，不只由当前版本决定；
- Correction、Update、Supersession、Withdrawal、Retraction 和 Deletion 必须分离；
- Delivery 不证明 Consumption，Consumption 不证明 Decision；
- Product / Alert 引发的 Decision、Action 和 Effect 必须在有权观察时进入因果血缘；
- 跨租户影响路由不能泄露私有依赖和产品；
- Impact Case 关闭必须覆盖知识修复、产品修复和接收者通知三类状态。

## 二十、S7 对对象分类的候选裁定

### 20.1 保持核心对象

- Intelligence Product；
- Alert；
- Feedback。

### 20.2 新增一等对象候选

- `Product Edition`：具有独立内容、版本、审核和纠正生命周期；
- `Product Release`：具有独立发布授权、受众、用途和撤回语义；
- `Impact Case`：具有跨面发现、分类、重算、通知和关闭生命周期。

### 20.3 过程记录或值对象候选

- Rendering；
- Distribution Attempt；
- Delivery Record；
- Receipt / Consumption Signal；
- Correction Notice；
- Change Notice；
- Dependency Edge；
- Product Manifest。

它们是否全部升格为核心对象，留给 S9 权威、生命周期和血缘终审；但实现语义中必须可分别表达。

### 20.4 暂不作为独立世界事实

- `Effect`：暂作为 Effect Assessment / Causal Claim 家族；
- `Action`：保留为外部行动对象候选，不能由 Recommendation 推导为已执行；
- `Published Product`：是 Product Release / Edition 的视图，不是新对象类型；
- `Corrected Product`：是版本关系和生命周期视图。

## 二十一、开放问题

- Product、Edition、Release 是否需要三个永久核心身份，还是 Product + Version + Release Record 足够；
- 动态仪表板检查点的最低保留频率怎样按风险确定；
- Dependency Edge 是否因独立审核、失效和替代关系而升格；
- Alert Thread 是独立对象还是 correlation view；
- 未授权保存个人阅读遥测时，纠正义务如何证明尽到；
- 外部下载、截图和转发超出控制后，尽力通知的关闭标准是什么；
- Feedback 如何在匿名保护与防投毒之间平衡；
- Effect Assessment 应归入 Assessment 子型还是独立因果对象家族；
- 影响传播的服务等级如何由产品承诺、危害和暴露共同决定；
- Product Release 的受众裁剪是否总需新 Edition。

## 二十二、验收门槛

- 能从任一 Product Edition 回溯精确输入对象版本和生成版本；
- 能区分逻辑产品、内容版本、发布授权、表达、分发和送达；
- 能证明一个 Alert 从何触发、为何抑制或发给谁；
- 能把 Feedback 路由到事实、研判、产品、偏好或演进，而不越过验证；
- 上游撤回时能找到直接和间接消费者；
- 能依据依赖语义裁剪无关分支；
- 能区分更新、更正、替代、撤回和删除；
- 能找出所有已知收到错误版本的受众并追踪纠正状态；
- 能表达未知消费者和不可控传播的剩余风险；
- 能记录产品或告警对后续 Decision、Action 和 Forecast 评分的干预；
- 多租户传播既能触发私有纠正，又不泄露租户依赖；
- 场景中不存在生成器自行批准发布、反馈直接改写事实或新版本覆盖历史。

## 二十三、与后续步骤的关系

S7 建立交付与纠正语义，但不提前解决：

- S8：双时态、版本、知识覆盖和动态投影；
- S9：全部对象的权威、生命周期、血缘边和不变量终审；
- S10：迟到、撤回、跨租户、历史重解释和外部不可控传播压力评审；
- 后续专题：物理图存储、事件总线、通知基础设施和具体 API。

## 二十四、关联

- [v0002 迭代入口](../02-architecture-iterations/v0002-intelligence-semantics/README.md)
- [统一语言与对象分类法](../08-reference-models/unified-language-and-object-taxonomy.md)
- [核心对象主干](./core-object-spine.md)
- [Claim、争议、Fact 与裁定模型](./claim-fact-and-adjudication-model.md)
- [研判、预测、风险与建议模型](./assessment-forecast-risk-recommendation-model.md)
- [事实撤回与产品更正](../05-scenarios-and-flows/SCN-0008-fact-retraction-and-correction.md)
- [SCN-0015：部分事实撤回、影响裁剪与接收者纠正](../05-scenarios-and-flows/SCN-0015-partial-retraction-impact-and-recipient-correction.md)
- [Risk、Alert 与 Product 状态机](../08-reference-models/state-machines/03-risk-alert-and-product.md)
- [情报产品面契约](../03-current-architecture/plane-contracts.md)
