# 架构决策

状态：Reference

这里保存架构决策记录（ADR）。ADR 回答“为什么这样设计”，而不是重复当前架构。

每份 ADR 至少包含背景、驱动因素、候选方案、最终决定、正负影响、风险、重新评估条件及关联场景。

ADR 一经接受不直接改写；需要改变决定时，创建新的 ADR 并将旧 ADR 标记为 `Superseded`。

## 已接受决策

- [ADR-0001：拆分数据认知面与情报研判面](./adrs/ADR-0001-split-cognition-and-assessment-planes.md)
- [ADR-0002：建立覆盖全系统的能力演进面](./adrs/ADR-0002-system-wide-evolution-plane.md)
- [ADR-0003：多租户采用控制子域与横切上下文双重表达](./adrs/ADR-0003-multitenancy-dual-representation.md)
- [ADR-0004：固定皇帝、数据底座与基础设施的顶层位置](./adrs/ADR-0004-emperor-data-foundation-infrastructure-placement.md)
- [ADR-0005：拆分交互、产品、治理与领域并划定控制运行边界](./adrs/ADR-0005-separate-interaction-products-governance-domains-and-runtime.md)
- [ADR-0006：采用五域分组、认知存储分离与共享情报主干](./adrs/ADR-0006-group-planes-separate-cognition-storage-and-share-intelligence-spine.md)
- [ADR-0007：共享平台承载租户级逻辑朝廷](./adrs/ADR-0007-shared-platform-tenant-logical-courts.md)
- [ADR-0008：采用有类型、可追溯的核心对象主干](./adrs/ADR-0008-typed-traceable-core-object-spine.md)
- [ADR-0009：一级面通过版本化契约协作](./adrs/ADR-0009-versioned-cross-plane-contracts.md)
- [ADR-0010：能力树只按稳定业务能力分类](./adrs/ADR-0010-capability-only-taxonomy.md)
- [ADR-0011：官制组织与技术所有权分离](./adrs/ADR-0011-separate-narrative-organization-from-technical-ownership.md)
- [ADR-0012：所有关键状态通过显式事件转换](./adrs/ADR-0012-explicit-event-driven-state-transitions.md)
