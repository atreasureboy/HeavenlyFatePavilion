# 情报语义全局不变量

状态：Accepted

生效日期：2026-07-29

本目录续接 Skeleton v1.0 的 `INV-0001`～`INV-0015`。这些不变量约束后续数据、协议、Agent、运行和产品设计；实现形式可以变化，语义不得被绕过。

## INV-0016：有类型转化

资料、证据、主张、事实、研判、预测、风险、建议和产品之间的转化必须创建新对象、新 Revision 或有类型记录，不得原地改名。

## INV-0017：时间正交

世界有效时间、系统记录时间、来源/获取/处理时间与 Authority Domain 的认识时间必须可区分；迟到资料不得倒写历史认识。

## INV-0018：权力分离

语义形成、验证审核、接受决定、发布授权、保管强制和执行副作用权不得因同一主体兼任而被合并。自提、自审、自批、自发必须经过显式例外治理。

## INV-0019：受控转换

关键状态变化必须声明目标对象、expected revision、前后状态、Authority Domain、授权依据、主体、原因、幂等键与因果关联，并在确定性执行点校验。

## INV-0020：固定版本血缘

关键 Provenance、Derivation、Semantic Dependency、Citation、Distribution 与 Causal Lineage 必须有类型并指向精确 Revision；只指向“最新版”不构成可审计血缘。

## INV-0021：血缘完整性使用门

血缘完整性必须分级。未达到对象类型和用途要求的最低等级时，不得接受为 Fact、发布 Product Edition、发送高风险 Alert 或自动触发能力演进。

## INV-0022：非破坏性纠正

Challenge、Correction、Supersession、Withdrawal、Retraction 和 Deletion 必须分离；任何变化都不得静默覆盖历史，并必须产生作用域化影响分析。

## INV-0023：产品阶段分离

Product、Product Edition、Product Release、Rendering、Distribution 和 Delivery 必须分离；发布不等于送达，送达不等于消费，消费不等于授权行动。

## INV-0024：认知与行动断点

Feedback、Recommendation、Decision、Authorization、Action、Outcome 与 Causal Attribution 不得互相替代。系统建议不能自行产生人类或外部世界行动权。

## INV-0025：作用域组合不污染

公共基线、租户覆盖与任务工作空间通过显式 Selection Policy 组合；查询、覆盖、历史回放和影响传播均不得越过当前授权，也不得以最后写入者获胜解决语义冲突。

## INV-0026：认知独立性可证明

反证、复核和裁定的独立性由授权链、数据来源、模型/工具故障根、上下文和激励隔离证明，不以 Agent 数量或不同名称证明。

## 验证入口

- [对象权威、生命周期、血缘与不变量统一模型](../04-domain-design/object-authority-lifecycle-lineage-invariant-model.md)
- [SCN-0018：组合压力场景](../05-scenarios-and-flows/SCN-0018-semantic-baseline-composite-stress.md)
- [ADR-0016](../06-architecture-decisions/adrs/ADR-0016-adopt-scoped-versioned-intelligence-semantics-baseline.md)
