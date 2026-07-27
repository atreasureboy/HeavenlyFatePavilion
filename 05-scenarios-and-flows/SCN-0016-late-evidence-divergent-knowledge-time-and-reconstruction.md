# SCN-0016：迟到资料、差异知识时间与历史重建

状态：Proposed

所属专题：`v0002-intelligence-semantics / S8`

## 一、验证目标

某公司董事会于 2026-02-01 任命李四为代理 CEO：

- 决议于 2026-01-28 签署；
- 2026-02-03，租户 A 通过合法私有渠道取得决议；
- 2026-02-05，租户 A 完成真实性验证并接受该内部事实；
- 租户 B 无权访问此资料；
- 公共登记直到 2026-05-10 才更新；
- 天机阁于 2026-05-11 采集公共更新，并于 2026-05-12 接受公共 Fact；
- 2026-04-01，公共产品和租户 A 私有产品都曾回答“当前 CEO 是谁”。

本场景验证：

1. 现实有效时间、系统记录时间与不同权威域认识时间可以同时存在；
2. “现在看 3 月 1 日”与“还原 3 月 1 日当时所知”产生不同答案；
3. 租户 A 的早期私有知识不污染公共基线或租户 B；
4. 迟到公共资料不被回填成 2 月已知；
5. 4 月产品能按知识截点重建；
6. 新资料导致 Update 还是 Correction 取决于产品契约和当时可用信息；
7. 历史回放不能恢复已撤销用户权限；
8. 当前模型重解释与历史原始结果保持分离。

## 二、关键对象与时间

```text
Board Resolution Document BR-1
  source_authored_at = 2026-01-28
  stated_effective_at = 2026-02-01

Tenant A Snapshot SA-1
  acquired_at = 2026-02-03
  recorded_from = 2026-02-03

Tenant A Fact FA-1
  proposition = 李四担任代理 CEO
  valid_from = 2026-02-01
  recorded_from = 2026-02-05
  authority_domain = Tenant A Internal
  epistemic Accepted from = 2026-02-05

Public Registry Snapshot SP-1
  source_published_at = 2026-05-10
  acquired_at = 2026-05-11
  recorded_from = 2026-05-11

Public Fact FP-1
  proposition = 李四担任代理 CEO
  valid_from = 2026-02-01
  recorded_from = 2026-05-12
  authority_domain = Public Registry
  epistemic Accepted from = 2026-05-12
```

注意：

```text
FP-1.valid_from = 2026-02-01
不推出
Public knew FP-1 on 2026-02-01
```

## 三、4 月的两个产品

### 3.1 公共产品 PE-PUB-04

```text
world_as_of = 2026-04-01
knowledge_cutoff = 2026-04-01
authority_selection = Public Registry
```

当时公共登记仍显示张三，产品写：

> 截至 2026-04-01，公开登记仍列张三为 CEO；未发现已验证的公开变更。

依赖：

```text
Public Fact: 张三任 CEO
Public Registry Snapshot available by cutoff
Explicit absence-of-public-change qualification
```

### 3.2 租户 A 私有产品 PE-A-04

```text
world_as_of = 2026-04-01
knowledge_cutoff = 2026-04-01
tenant = A
purpose = internal operational assessment
```

产品写：

> 租户内部已验证李四自 2026-02-01 起代理 CEO；公共登记仍为张三，两种口径存在差异。

它同时引用：

- Tenant A Fact FA-1；
- 当时 Public Fact；
- 角色定义：内部运营角色不自动等同法定签署权。

### 3.3 租户 B

租户 B 在 4 月只能看到公共登记口径。查询系统不得：

- 显示李四；
- 返回“存在一个你无权查看的私有冲突”；
- 通过候选数量、缓存命中或延迟暴露租户 A 私有知识。

## 四、5 月迟到公共资料进入

```text
2026-05-10 Registry publishes update
→ 2026-05-11 Snapshot SP-1 acquired
→ Evidence anchored
→ Claim extracted
→ Valid time aligned to 2026-02-01
→ 2026-05-12 Public Authority accepts FP-1
→ Knowledge Revision created
→ Public Current Projection changes
```

系统不做：

```text
FP-1.recorded_from = 2026-02-01
Public Epistemic Accepted from = 2026-02-01
```

## 五、四类查询

### 5.1 现在问：2026-03-01 谁是代理 CEO

```text
world_as_of = 2026-03-01
knowledge_as_of = 2026-05-20
recorded_as_of = 2026-05-20
authority = Public Registry
```

答案：

> 按 5 月 20 日已接受的公共资料，李四自 2 月 1 日起担任代理 CEO。

### 5.2 还原公共域在 2026-03-01 所知

```text
world_as_of = 2026-03-01
knowledge_as_of = 2026-03-01
recorded_as_of = now
projection_mode = epistemic_reconstruction
```

答案：

> 截至 3 月 1 日，公共权威域尚未接受李四任命；当时可见登记仍为张三。后来 5 月资料追溯表明现实状态已于 2 月改变。

### 5.3 还原租户 A 在 2026-03-01 所知

```text
tenant = A
world_as_of = 2026-03-01
knowledge_as_of = 2026-03-01
```

答案：

> 租户 A 已于 2 月 5 日接受李四代理任命，同时知道公共登记尚未更新。

### 5.4 还原租户 B 在 2026-03-01 所知

答案只包含租户 B 有权使用的公共状态。即使今天执行历史回放，也不能把租户 A 的私有知识注入租户 B 历史。

## 六、Product Update / Correction 判断

### 6.1 公共产品

PE-PUB-04 的陈述严格限定为“公开登记仍列张三，未发现公开变更”，且其 Manifest 证明：

- 4 月 1 日前公共更新确实不可得；
- 没有遗漏当时可访问的反证；
- 产品没有声称内部现实绝对未变化；
- 知识截点明确。

候选裁定：

```text
PE-PUB-04
→ Historical edition remains epistemically correct
→ World interpretation is now stale
→ Update / Supersession to PE-PUB-05
→ Materiality-based recipient notice
```

它不因后来的迟到资料自动成为流程错误。

### 6.2 反事实：公共产品使用绝对措辞

若旧产品写：

> 张三确定仍是公司唯一 CEO。

但证据只支持“公开登记如此显示”，则即使私有决议当时不可得，旧产品也越过 Evidence 和不确定性。

候选裁定：

```text
CorrectionRequired
+ quality event for overclaim
```

### 6.3 反事实：租户 A 产品遗漏已知私有 Fact

若 PE-A-04 的知识截点为 4 月 1 日，却只显示公共登记，依赖 Manifest 证明 FA-1 当时已获授权且被接受：

```text
CorrectionRequired
→ Pipeline / selection policy failure
→ Impact Case
→ Evolution Evidence Candidate
```

迟到公共登记不是错误原因；错误是系统没有使用当时已知且适用的租户事实。

## 七、历史原始结果与当前重解释

2026-06-01，角色本体升级，把 `acting_ceo` 与 `registered_ceo` 更严格分离。

系统需要保留：

```text
Original PE-A-04
  schema_version = role-schema-v2
  exact historical rendering

Current reinterpretation
  same source revisions
  schema_version = role-schema-v3
  label = reinterpreted_with_current_schema
```

当前重解释可以更准确，但不能替换 PE-A-04 或声称它是当时模型输出。

## 八、权限撤销分支

租户 A 的前员工甲在 4 月有权查看 SA-1，5 月离职并被撤销权限。6 月请求：

> 请把系统恢复到 3 月 1 日，我当时有权限。

正确路径：

```text
Current Authorization Gate
→ 当前无权访问 Tenant A 历史私有资料
→ 拒绝
→ 不执行 Historical Projection
```

`policy_as_of = 2026-03-01` 不恢复其旧权限。

授权审计员乙在当前具备历史审计权限时，才可以在审计目的和最小披露约束下重建。

## 九、删除与部分重建分支

假设 SA-1 因合法删除义务在 2027 年被加密销毁，只保留允许的最小审计 tombstone：

```text
Historical PE-A-04 request
→ Product Edition text and manifest still存在最小证明
→ underlying private Snapshot unavailable
→ reproducibility = Partial
→ 明确说明无法重新验证原 Evidence 内容
```

系统不得：

- 从租户 B 或其他未授权副本恢复；
- 把当前公共 Snapshot 当作原私有 Evidence；
- 声称 Exact replay。

## 十、缓存隔离分支

以下查询的缓存键必须不同：

```text
Tenant A / operational / world=03-01 / knowledge=03-01
Tenant B / public / world=03-01 / knowledge=03-01
Public / current-lookback / world=03-01 / knowledge=05-20
Public / historical / world=03-01 / knowledge=03-01
```

租户 A 的缓存命中不能被租户 B 观察。公共 Fact 5 月更新后：

- 当前回看缓存失效；
- 3 月历史认识缓存不应被改写；
- 若 Selection Policy 版本变化，则生成新重解释缓存，而不覆盖旧检查点。

## 十一、异常分支

### 11.1 决议时间只有月份

如果决议只说“2 月起生效”，系统保存：

```text
valid_time = 2026-02
precision = month
```

不得强行设置 2 月 1 日，除非领域规则明确且可引用。

### 11.2 来源回填发布日期

公共登记页面在 5 月更新后声称“2 月 1 日发布”。天机阁保留：

- source claimed publish time = 2 月 1 日；
- first observed = 5 月 11 日；
- captured Snapshot = 5 月 11 日；
- 发布历史真实性 = 未验证。

不能由来源自报时间改写采集历史。

### 11.3 Tenant A 只是收到、未验证

若 2 月 3 日收到资料但直到 5 月才验证：

```text
Available from 02-03
Accepted from 05-xx
```

3 月查询应显示“存在未验证私有 Claim”，不能显示 Tenant Fact，也不能说完全未见。

### 11.4 联合空间关闭

若租户 A 曾在 Joint Space 分享派生结论：

- 关闭后按协议决定各成员可保留的派生对象；
- Joint Fact 不自动变为各 Tenant Fact；
- 缓存、搜索和工作副本按关闭策略失效；
- 历史审计仅在当前授权下可重建。

### 11.5 系统时钟异常

记录时间来自受控提交顺序：

- 检测时钟回拨；
- 保留逻辑提交序号；
- 标记时间可信度；
- 不以损坏时钟决定现实先后；
- 高影响时间线进入人工复核。

## 十二、Agent 与 Tool 分工

### Tool / Service

- 有效区间和精度运算；
- 不可变 Revision 与系统记录区间；
- Epistemic Record 过滤；
- Query Context 校验；
- Scope、Purpose 和当前授权强制；
- 缓存键、watermark 和失效；
- Product Manifest 精确回放；
- 历史/当前投影 Diff；
- 可重建等级计算。

### Agent / Human

- 解释公开登记角色和内部代理角色；
- 判断不同 Authority Domain 对当前问题的适用性；
- 说明当前回看与当时所知为何不同；
- 判断旧产品是 Update 还是 Correction；
- 处理时间不确定、来源回填和本体重解释；
- 决定删除后允许保留的最小审计解释。

Agent 只接收已按当前权限裁剪的 Knowledge Context Package。

## 十三、审计证据

完成后至少存在：

- BR-1、SA-1、SP-1 的来源、获取和记录时间；
- FA-1、FP-1 的有效时间和 Revision；
- Public、Tenant A 的独立 Epistemic Records；
- 4 月两个 Product Edition 的 Query Context 与 Manifest；
- 四类查询的选中版本、替代版本和冲突解释；
- 5 月 Knowledge Revision；
- Update / Correction 分类依据；
- role-schema-v2 原始结果与 v3 当前重解释；
- 离职用户的当前授权拒绝；
- 删除 tombstone 与 Partial replay 声明；
- 缓存隔离和失效记录。

## 十四、验证结论

候选通过。

场景证明：

- 二时态 Revision 无法单独表达多权威域认识，Epistemic Record 有独立必要性；
- 认识时间必须绑定 Authority Domain，不能使用全局 `known_at`；
- 租户覆盖是查询合成，不是复制后覆盖公共事实；
- “现实当时是什么”和“当时知道什么”必须同时保留；
- 迟到资料改变当前对过去的认识，但不能倒写历史知识；
- Product 的世界准确性与当时流程/认识正确性是两个评价维度；
- 历史回放永远不能绕过当前授权；
- 当前本体或模型重解释不能冒充原始历史输出；
- 全量物化所有视图没有必要，但高影响 Product 必须固定 Manifest 和检查点。

本场景不改变 Skeleton v1.0，只深化时间、版本、租户覆盖、产品复现与授权横切语义。

## 十五、验证的不变量

- Valid Time、Record Time、Knowledge Time 分离；
- Knowledge Time 绑定 Authority Domain；
- Late Evidence 不回填系统记录历史；
- Public、Tenant、Joint、Mission 认识隔离；
- Overlay 不使用 last-write-wins；
- Product 固定 world as-of 与 knowledge cutoff；
- Historical Replay 经过 Current Authorization Gate；
- 原始结果与当前重解释分离；
- 缓存键包含租户、用途、时间和版本；
- 删除后诚实降级可重建性；
- 时间精度不被伪造。

## 十六、关联

- [时间、版本、作用域与知识覆盖语义模型](../04-domain-design/temporal-version-scope-knowledge-overlay-model.md)
- [Claim、争议、Fact 与裁定语义模型](../04-domain-design/claim-fact-and-adjudication-model.md)
- [Relationship、Event、State、Diff 与 Change 模型](../04-domain-design/world-state-event-and-change-model.md)
- [产品、告警、反馈与影响纠正模型](../04-domain-design/intelligence-product-alert-feedback-correction-model.md)
- [租户、身份与授权](../03-current-architecture/cross-cutting/01-tenancy-identity-authorization.md)
