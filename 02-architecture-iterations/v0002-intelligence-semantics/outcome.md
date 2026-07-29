# v0002 迭代结果

状态：Accepted

本轮于 2026-07-29 完成并通过语义基线采纳门，记录：

- 采纳的语义与不变量；
- 被拒绝或延期的候选；
- 新增或替代的 ADR；
- 对当前架构、状态机、场景和文字图的更新；
- 未解决但允许进入下一专题的问题。

当前里程碑：

- 迭代已启动，完成专题范围、顺序、门槛和第一批开放问题；
- S1 统一语言与对象分类已达到候选层完成；
- S2 资料与证据对象已达到候选层完成，并新增来源变化与证据保全场景；
- S3 主张、争议、事实与裁定已达到候选层完成，并新增多权威域事实并存场景；
- S4 实体、身份、别名与消歧已达到候选层完成，并新增误合并与拆分纠正场景；
- S5 关系、事件、状态、Diff 与 Change 已达到候选层完成，并新增四类变化分离场景；
- S6 研判、预测、风险与建议已达到候选层完成，并新增预测干预与风险重校准场景；
- S7 产品、告警、反馈与影响纠正已达到候选层完成，并新增部分事实撤回、影响裁剪与接收者纠正场景；
- S8 双时态、版本、作用域与知识覆盖已达到候选层完成，并新增迟到资料、差异知识时间与历史重建场景；
- S9 对象权威、生命周期、血缘与不变量已达到候选层完成，并新增越权事实晋升、血缘断裂与自审发布阻断场景；
- S10 组合压力场景与就绪评审通过；
- ADR-0016 采纳作用域化、版本化的情报语义基线；
- 统一分类法、S2～S9 领域模型和 SCN-0010～SCN-0018 转为 `Accepted`；
- INV-0016～INV-0026 成为后续专题必须遵守的全局不变量；
- 当前核心对象主干、状态机、文字图和导航已同步。

## 采纳结果

本轮采纳八个对象家族、五级对象分类、Authority Domain、不可变 Revision、双时态与认识记录、Transition Envelope、typed revision-fixed lineage、Product Edition 和 Governance Case。

## 延期项目

Action 的最终边界、物理 Schema、协议字段、存储与索引、具体策略矩阵、跨租户联合调查协议、领域本体及具体算法进入后续专题，不阻塞当前基线。

## 拒绝项目

- 把 Document、Claim 或模型输出直接提升为 Fact；
- 把 Fact 解释为无作用域的全局真理；
- 用 last-write-wins 解决知识冲突；
- 以 Agent 数量代替认知独立性；
- 把 Product、Release、Delivery 和 Action 压成一个状态；
- 通过静默覆盖完成纠正、撤回或历史重写。

## 正式证据

- [ADR-0016](../../06-architecture-decisions/adrs/ADR-0016-adopt-scoped-versioned-intelligence-semantics-baseline.md)
- [就绪评审](../../07-analysis-and-validation/v0002-intelligence-semantics-readiness-review.md)
- [组合压力场景](../../05-scenarios-and-flows/SCN-0018-semantic-baseline-composite-stress.md)
- [情报语义全局不变量](../../08-reference-models/intelligence-semantic-invariants.md)
