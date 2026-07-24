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
