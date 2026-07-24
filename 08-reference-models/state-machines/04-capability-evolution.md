# 能力候选、版本与发布状态机

状态：Accepted

## 一、Capability Gap 生命周期

```text
Observed
→ Triaged
→ Confirmed / Dismissed / Deferred

Confirmed
→ CandidateRequested
→ Addressed / AcceptedRisk / Superseded
```

缺口必须关联 Evolution Evidence、受影响能力、严重性、时代变化和重新评估条件。

## 二、Capability Change Candidate 生命周期

```text
Draft
  │ submit
  ▼
Proposed
  ├── reject ──────────▶ Rejected
  ├── defer ───────────▶ Deferred
  └── approve_experiment▶ Experimenting

Experimenting
  ├── experiment_fail ─▶ FailedExperiment
  └── evaluate ────────▶ Evaluated

Evaluated
  ├── reject ──────────▶ Rejected
  ├── revise ──────────▶ Draft
  └── governance_review▶ UnderGovernanceReview

UnderGovernanceReview
  ├── approve ─────────▶ Approved
  ├── reject ──────────▶ Rejected
  └── request_more ────▶ Experimenting

Approved
  └── package ─────────▶ ReleaseReady
```

## 三、Capability Version 生命周期

```text
Built
  │ verify
  ▼
Verified
  │ stage
  ▼
Staged
  ├── canary ──────────▶ Canary
  └── abort ───────────▶ Rejected

Canary
  ├── promote ─────────▶ RollingOut
  ├── hold ────────────▶ Held
  └── rollback ────────▶ RolledBack

RollingOut
  ├── complete ────────▶ Active
  └── rollback ────────▶ RolledBack

Active
  ├── observe ─────────▶ UnderObservation
  ├── replace ─────────▶ Superseded
  └── emergency_rollback▶ RolledBack

UnderObservation
  ├── retain ──────────▶ Retained
  ├── adjust ──────────▶ Held
  └── rollback ────────▶ RolledBack

Retained → Active
```

## 四、发布守卫

进入 `Canary` 前至少需要：

- 明确变更范围和能力版本；
- 可复现评测；
- 回归和对抗结果；
- 租户与数据作用域；
- 成本和容量影响；
- 风险、回滚和停止条件；
- 智能治理面的批准 Decision；
- 平台控制面的版本化发布配置。

## 五、自主演进与人类演进

两种模式使用同一状态机：

```text
系统自主生成候选
→ proposer = System Agent

人类设计候选
→ proposer = Human Architect
```

发起主体不同，不改变实验、评测、治理、发布和回滚要求。

## 六、演进异常路径

```text
实验不可复现
→ FailedExperiment
→ 保留证据，不进入治理审核

评测提升但成本失控
→ Evaluated
→ Rejected / Revised / 特定租户限定

Canary 出现回归
→ RolledBack
→ Evolution Evidence
→ 新 Capability Gap

发布后时代变化使能力过时
→ Capability Gap Observed
→ 新候选
→ 旧版本继续服务直到替代或退役
```
