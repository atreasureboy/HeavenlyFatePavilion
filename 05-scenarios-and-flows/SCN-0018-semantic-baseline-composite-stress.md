# SCN-0018：情报语义基线组合压力场景

状态：Accepted

验证日期：2026-07-29

## 一、场景目的

本场景不是增加新的业务细节，而是把 S2～S9 分别验证过的异常压入同一条端到端链路，检查语义骨架在并发、迟到、纠正、隔离和越权同时出现时是否仍然成立。

## 二、复合输入

一个公共来源公布甲公司收购乙公司的消息。随后发生：

1. 来源页面静默修改了交易日期；
2. 租户 A 的内部材料表明交易附带未公开条件；
3. 租户 B 只有公开信息，无权获知租户 A 的材料；
4. 页面解析器升级，产生 Extraction Diff；
5. 同名企业导致一次错误实体合并；
6. 兵部形成风险 Forecast，并触发用户采取规避行动；
7. 行动改变了风险暴露，使 Forecast 不能按朴素命中率评分；
8. 已发布报告的一项关键 Fact 随后被撤回；
9. 一个无接受授权的 Agent 尝试把 Claim 直接晋升为公共 Fact；
10. 更正过程中同时发生旧 Revision 的并发发布和删除请求。

## 三、期望处理链

```text
Source
→ Acquisition Record
→ immutable Snapshot
→ anchored Evidence
→ Claim / Proposition
→ Verification Package
→ scoped Acceptance Decision
→ Fact Aggregate
→ Entity / Relationship / Event / Change
→ Signal / Assessment / Scenario / Forecast / Risk
→ Product Edition
→ Product Release
→ Distribution / Delivery
→ Feedback / Governance Case / Impact Case
```

每条箭头都创建新对象、新 Revision 或有类型记录，不允许把上游对象原地改名为下游对象。

## 四、关键裁定

### 4.1 来源和变化

- 原页面与修改后页面分别形成不可变 Snapshot；
- Content Diff、Extraction Diff、Knowledge Revision、World Change 分型保存；
- 解析升级不得自动制造 World Change；
- 迟到材料只改变“现在对过去的认识”，不得倒写当时知识状态。

### 4.2 权威与多租户

- 公共域、租户 A 域和租户 B 域分别维护 Epistemic Record；
- 租户 A 的私有 Fact 可以覆盖其查询视图，但不得改写公共 Fact；
- 租户 B 的候选发现、查询、历史回放和影响通知均不能泄露租户 A 的存在；
- 无授权 Agent 的晋升请求被 Transition Guard 拒绝，并生成审计事件与 Governance Case。

### 4.3 实体和世界模型

- Mention 与 Source-local Record 先于 Entity Resolution；
- 错误合并通过 Split Decision 和新 Revision 纠正；
- 依赖错误实体的 Claim、Fact、Relationship、Assessment 和 Product 进入影响重评；
- 世界变化必须经过 Evidence—Claim—Fact 接受链，不能由 Diff 直接产生。

### 4.4 研判和干预

- Assessment、Scenario、Forecast、Risk 和 Recommendation 保持独立身份；
- Forecast 冻结信息截点、条件、结果空间、时间窗和解析规则；
- 用户行动作为 Intervention 进入因果血缘；
- 评分区分“预测错误”和“预警促成干预后风险未发生”。

### 4.5 产品和纠正

- Product、Edition、Release、Rendering、Distribution、Delivery 分离；
- Fact 撤回先建立 Impact Case，再沿固定 Revision 的 typed dependency edge 传播；
- 只有实质依赖该 Fact 的 Edition 被纠正、替代或撤回；
- 已送达接收者获得对应更正，未授权租户不获得旁路信息；
- 删除请求不能破坏法定保留、审计、血缘或历史版本；删除语义与撤回语义分离。

### 4.6 并发与状态

所有关键转换必须携带：

```text
Transition Envelope
= object identity
+ expected revision
+ from / to state
+ authority domain
+ mandate / authorization
+ actor
+ reason
+ evidence / decision reference
+ event time
+ idempotency key
+ correlation / causation
```

旧 Revision 发布因 expected revision 不匹配而失败；重试保持幂等，不以最后写入者覆盖语义冲突。

## 五、必须成立的观察结果

| 检查 | 期望 |
|---|---|
| 来源被修改 | 原始 Snapshot 和证据定位仍可复现 |
| 多权威域冲突 | 并存且可解释，不产生伪全局真理 |
| 实体误合并 | 可逆、可传播影响、不复用错误身份 |
| 四类变化同时出现 | 可机器区分，World Change 有接受链 |
| Forecast 触发干预 | 解析与评分保留干预上下文 |
| Fact 撤回 | 产品按实质依赖裁剪影响 |
| 越权晋升 | 在状态转换点被确定性拒绝 |
| 跨租户查询 | 不泄露对象、存在性、依赖或通知 |
| 并发旧版发布 | 乐观并发保护生效 |
| 历史重建 | 可区分现在看过去、还原当时所知和回放当时记录 |

## 六、验证结论

组合场景没有要求新增一级面，也没有发现必须合并的核心语义。S2～S9 的模型能够共同解释输入、权威、版本、时间、状态、血缘、研判、产品和纠正。

通过条件是：后续数据、协议和运行设计必须实现本场景依赖的 Transition Guard、作用域过滤、固定版本血缘、影响传播与历史查询语义。具体存储、消息格式和算法属于后续专题，不阻塞语义基线采纳。

## 七、关联材料

- [情报语义基线就绪评审](../07-analysis-and-validation/v0002-intelligence-semantics-readiness-review.md)
- [对象权威、生命周期、血缘与不变量统一模型](../04-domain-design/object-authority-lifecycle-lineage-invariant-model.md)
- [ADR-0016](../06-architecture-decisions/adrs/ADR-0016-adopt-scoped-versioned-intelligence-semantics-baseline.md)
- [情报语义全局不变量](../08-reference-models/intelligence-semantic-invariants.md)
