# 当前生效架构

状态：Accepted

这里是“天机阁现在被定义成什么”的唯一主入口，不保存未经采纳的想法，也不承担解释全部历史。

当前架构只描述生效结果；形成原因进入 ADR，历史过程进入迭代记录，压力与闭合结论进入分析验证。

## 一、推荐入口

1. [Skeleton v1.0 基线声明](./skeleton-v1.0-baseline.md)
2. [整体架构骨架完善范围与阶段边界](./skeleton-completion-scope.md)
3. [权威架构骨架索引：十层阅读树](./architecture-skeleton-index.md)
4. [外层骨架终局总图](./text-diagrams/TXT-0025-outer-skeleton-final-map.md)
5. [Skeleton v1.0 基线封版图](./text-diagrams/TXT-0024-skeleton-v1.0-baseline.md)
6. [总体一级逻辑骨架](./overall-logical-skeleton.md)
7. [整体架构骨架](./overall-architecture-skeleton.md)
8. [文字架构图集](./text-diagrams/README.md)

## 二、按架构层阅读

### L1：上下文与主权

- [系统上下文与边界](./system-context.md)
- [主体生态与分域主权](./ecosystem-and-sovereignty.md)

### L2：职责与能力

- [五域十面一级骨架](./overall-logical-skeleton.md)
- [十面二级能力树](./capability-trees/README.md)
- [整体架构骨架](./overall-architecture-skeleton.md)

### L3：组织治理映射

- [皇帝、三省、六部与公共官署映射](./organization-mapping/README.md)

### L4：对象与语义

- [核心对象主干](../04-domain-design/core-object-spine.md)
- [统一语言与对象分类法](../08-reference-models/unified-language-and-object-taxonomy.md)
- [情报语义全局不变量](../08-reference-models/intelligence-semantic-invariants.md)
- [对象权威、生命周期与血缘模型](../04-domain-design/object-authority-lifecycle-lineage-invariant-model.md)

### L5：运行、流与契约

- [全局运行形态](./operating-modes.md)
- [七条全局流](./global-flows.md)
- [十面输入输出契约](./plane-contracts.md)
- [核心状态机与异常路由](../08-reference-models/state-machines/README.md)

### L6：坐标与横切治理

- [空间、时间与规模骨架](./spatiotemporal-and-scale-skeleton.md)
- [七维横切治理](./cross-cutting/README.md)

### L7：生存与负空间

- [非功能与生存性骨架](./nonfunctional-and-survivability-skeleton.md)
- [负空间、反架构与未来替换](./negative-space-and-future-replacement.md)

### L8：全域演进

- [能力演进作用全域骨架](./evolution-across-entire-architecture.md)

### L9：场景与验证

- [端到端场景与验证](../05-scenarios-and-flows/README.md)
- [骨架完整性审计](../07-analysis-and-validation/skeleton-completeness-assessment.md)
- [Skeleton v0.3 闭合与一级稳定性复核](../07-analysis-and-validation/skeleton-v0.3-closure-and-stability-review.md)
- [Skeleton v1.0 正式就绪评审](../07-analysis-and-validation/skeleton-v1.0-readiness-review.md)
- [v0002 情报语义基线就绪评审](../07-analysis-and-validation/v0002-intelligence-semantics-readiness-review.md)
- [外层架构骨架终局完成审计](../07-analysis-and-validation/outer-skeleton-final-completion-audit.md)

## 三、当前成熟度

```text
Skeleton v0.1：第一轮内部闭合
Skeleton v0.2：第二轮宏观闭合
Skeleton v0.3：第三轮宏观闭合
Skeleton v1.0：正式整体骨架基线

当前深化基线：情报语义与核心对象体系 v1 已采纳
```

G0 外层骨架已经封板；整体架构骨架仍在 G1—G3 完善阶段。当前可以补齐二级系统、关键机制主干与跨面连接，但暂不进入字段、算法、选型、参数和部署细节，也不得静默改变 v1.0 的一级职责与权威语义。
