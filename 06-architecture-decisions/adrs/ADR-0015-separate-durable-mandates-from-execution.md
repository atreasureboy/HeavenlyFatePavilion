# ADR-0015：持久授权与使命同临时执行实例分离

状态：Accepted

日期：2026-07-24

## 背景

如果把天机阁理解成一次用户请求启动一组 Agent，长期监测、事件响应、批处理、协作案件、能力演进和灾难恢复都无法自然表达。反之，如果让 Agent 永久常驻并自行决定后续工作，又会造成上下文漂移、无限循环、预算失控和权力扩张。

## 决定

所有运行形态采用分层生命周期：

```text
Mandate / Authority
→ Mission / Incident / Evolution Goal
→ Run
→ Task / Agent Instance / Tool Invocation
→ Product / State Change / External Effect
```

其中：

1. Mandate 表达为什么系统被允许持续工作；
2. Mission 等持久对象表达长期目标、验收与责任；
3. Run 表达一次有版本、预算和上下文的执行尝试；
4. Agent、Worker 与 Tool 是按需创建和释放的执行主体；
5. Trigger 只能提出候选工作，不能自动创造无限授权；
6. 运行形态转换必须通过有类型事件或决定；
7. 即时查询、后台批处理、恢复与演进遵守同一横切约束。

## 理由

- 长期目标不再依赖模型上下文存活；
- Agent 可以按需启动，降低 Token 和资源消耗；
- 人类请求、时间、事件、政策和事故可以共享运行语义；
- 监测、恢复和演进不会成为治理之外的后台特权通道；
- 未来模型更强时，可以改变执行主体而不改变责任骨架。

## 被拒绝方案

### 所有工作都必须来自当前用户会话

无法表达主动告警、持续监测、数据回填、演进和事故恢复。

### 让常驻皇帝或 Agent 自己维持长期目标

长期状态受模型上下文和进程寿命约束，难以审计、暂停、迁移和撤销。

### 每种运行形态建立独立任务模型

会让查询、监测、批处理、事故和演进拥有不兼容的权限、预算与状态语义。

## 后果

正向：

- 十种运行形态可以共用一套责任骨架；
- 长期 Mission 与短期 Run、Agent 生命周期明确分离；
- 后台和主动运行具有可追溯授权来源。

代价：

- 需要区分 Mandate、Mission、Incident、Run 和 Trigger；
- 长期任务必须处理授权、策略与版本变化；
- 不同形态仍需各自的验收和终止语义。

## 关联

- [全局运行形态骨架](../../03-current-architecture/operating-modes.md)
- [治理与执行状态机](../../08-reference-models/state-machines/01-governance-and-execution.md)
- [ADR-0012：所有关键状态通过显式事件转换](./ADR-0012-explicit-event-driven-state-transitions.md)
