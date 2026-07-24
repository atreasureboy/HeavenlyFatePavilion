# TXT-0011：天机阁七维横切治理图

状态：Accepted

## 这张图回答什么

七个横切维度如何从政策进入每一次调用，在哪里强制，如何贯穿十个一级面，以及失败后流向哪里。

## 一、从政策到证据

```text
                       ┌──────── 平台控制面 ────────┐
                       │ Tenant / Identity / Policy │
                       │ Budget / Risk / Retention  │
                       └─────────────┬───────────────┘
                                     │ 发布版本化期望状态
                                     ▼
┌──────────────┐      ┌──────────────────────────────────────────┐
│ 仙人 / 渠道  │─────▶│ 入口强制点：租户、主体、会话、授权绑定   │
└──────────────┘      └──────────────────┬───────────────────────┘
                                         │ Cross-cutting Context
                                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                    十个一级逻辑面及跨面契约                     │
│                                                                 │
│ 人机交互 → 智能治理 → 执行运行 → 数据认知 → 情报研判 → 产品   │
│                    ↕           ↕          ↕                     │
│                 领域能力    数据底座    能力演进                │
│                    ↕                                            │
│                 平台控制                                        │
└──────────────────────────────┬──────────────────────────────────┘
                               │ 每一步产生结果 + 控制证据
                               ▼
                   ┌───────────────────────────┐
                   │ 血缘 · 账本 · 审计 · 生命周期 │
                   │       数据底座权威记录       │
                   └────────────┬──────────────┘
                                │
                 ┌──────────────┴──────────────┐
                 ▼                             ▼
        智能治理审核高影响例外          能力演进评估控制效果
```

## 二、七个维度

```text
┌───────────────┬──────────────────┬──────────────────────────┐
│ 横切维度      │ 集中权威         │ 关键强制边界             │
├───────────────┼──────────────────┼──────────────────────────┤
│ 多租户        │ 平台控制/数据底座│ 入口、查询、索引、缓存   │
│ 身份与权限    │ 平台控制         │ 每次副作用、委托、分发   │
│ 证据与血缘    │ 认知/底座        │ Claim、Fact、研判、产品  │
│ 预算与资源    │ 平台控制         │ 计划、预留、每次调用     │
│ 风险与质量    │ 平台+各领域      │ 验收、审核、发布、演进   │
│ 审计与追踪    │ 数据底座         │ 决定、调用、读写、发布   │
│ 数据生命周期  │ 平台控制/底座    │ 存储、索引、分发、删除   │
└───────────────┴──────────────────┴──────────────────────────┘
```

## 三、横切上下文

```text
Cross-cutting Context
├── Scope       : platform / public / tenant / workspace / collaboration
├── Identity    : subject / actor / delegation / assurance
├── Authorization: policy version / capability / obligation
├── Evidence    : requirement / provenance / freshness
├── Resource    : budget / ledger / deadline / priority
├── RiskQuality : risk tier / quality profile / degradation
├── Trace       : trace / causation / decisions / audit class
└── Lifecycle   : classification / retention / residency / hold / deletion
```

语义可以通过引用解析，但不得因跨面、异步、缓存或模型调用而消失。

## 四、强制点拓扑

```text
[入口 PEP]
    │
    ▼
[跨面契约检查]
    │
    ├────▶ [数据查询/写入 PEP]
    ├────▶ [检索/索引/缓存 PEP]
    ├────▶ [Agent/Tool/模型网关 PEP]
    ├────▶ [预算预留与计量 PEP]
    ├────▶ [Fact/研判/产品质量门]
    ├────▶ [产品分发 PEP]
    └────▶ [生命周期/删除执行 PEP]

所有 PEP
→ 查询版本化政策
→ 产生结构化 Decision
→ 写入 trace / audit / ledger
→ 在长时任务关键副作用前重新判定
```

## 五、失败总路由

```text
租户/作用域不明 ───────▶ Rejected
身份/授权不足 ─────────▶ Rejected / ApprovalRequired
证据不满足 ────────────▶ Returned / Disputed / Degraded
预算不足 ──────────────▶ Replan / AdditionalBudget / Degraded / Stop
风险或质量越线 ───────▶ StrongerReview / Isolate / HumanTakeover
审计或 trace 断裂 ─────▶ StopHighImpact / ExplicitDegradation
生命周期策略冲突 ──────▶ Block / PolicyAdjudication / Deferred
跨租户泄露迹象 ───────▶ S4 Isolation + EvidencePreservation
```

## 六、关键不等式

```text
共享平台 ≠ 共享租户数据
Agent 建议 ≠ 权限决定
预算不足 ≠ 可以降低安全与证据底线
日志 ≠ 审计
业务终态 ≠ 数据删除
Legal Hold ≠ 新的读取权限
公共作用域 ≠ tenant_id 缺失
```

## 七、箭头语义与刻意省略

- `→` 表示控制或上下文传播；
- `↕` 表示双向契约和反馈；
- 图中 PEP 表示逻辑强制点，不规定一个物理服务；
- 本图不展开角色权限矩阵、策略语言、存储技术和部署拓扑；
- 本图不替代七维度—十面责任矩阵。

## 八、权威正文

- [横切治理骨架](../cross-cutting/README.md)
- [七维度—十面责任矩阵](../cross-cutting/05-seven-dimensions-ten-planes-matrix.md)
- [ADR-0013：强制上下文传播与确定性执行点](../../06-architecture-decisions/adrs/ADR-0013-mandatory-cross-cutting-context-and-enforcement.md)
