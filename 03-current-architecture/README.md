# 当前生效架构

状态：Accepted

这里是“天机阁现在被定义成什么”的唯一主入口，不保存未经采纳的想法，也不承担解释全部历史。

当前架构只描述生效结果；形成原因进入 ADR，历史过程进入迭代记录，压力与闭合结论进入分析验证。

## 一、推荐入口

1. [Skeleton v1.0 基线声明](./skeleton-v1.0-baseline.md)
2. [权威架构骨架索引：九层阅读树](./architecture-skeleton-index.md)
3. [Skeleton v1.0 基线封版图](./text-diagrams/TXT-0024-skeleton-v1.0-baseline.md)
4. [总体一级逻辑骨架](./overall-logical-skeleton.md)
5. [整体架构骨架](./overall-architecture-skeleton.md)
6. [文字架构图集](./text-diagrams/README.md)

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

## 三、当前成熟度

```text
Skeleton v0.1：第一轮内部闭合
Skeleton v0.2：第二轮宏观闭合
Skeleton v0.3：第三轮宏观闭合
Skeleton v1.0：正式整体骨架基线

当前下一步：按骨架分区逐步提高专题设计分辨率
```

骨架扩展阶段已经结束。后续专题可以进入对象、协议、运行、安全、容量和组织内部设计，但不得静默改变 v1.0 的一级职责与权威语义。
