# 事实、世界状态与研判状态机

状态：Accepted

## 一、Claim 生命周期

```text
Candidate
  │ evidence_bound
  ▼
Extracted
  │ verification_started
  ▼
UnderVerification
  ├── accept ──────────▶ Verified
  ├── conflict ────────▶ Disputed
  ├── insufficient ────▶ Unresolved
  ├── reject ──────────▶ Rejected
  └── replace ─────────▶ Superseded

Disputed / Unresolved
  ├── new_evidence ────▶ UnderVerification
  └── replace ─────────▶ Superseded
```

`Verified` Claim 可以参与形成 Fact，但 Claim 自身仍保留来源主体和原始陈述语义。

## 二、Fact 生命周期

```text
Proposed
  │ verification_accepted
  ▼
Active
  ├── challenge ───────▶ Challenged
  ├── expire ──────────▶ Expired
  ├── replace ─────────▶ Superseded
  └── retract ─────────▶ Retracted

Challenged
  │ review_started
  ▼
UnderReview
  ├── confirm ─────────▶ Active
  ├── correct ─────────▶ Superseded ──by──▶ New Active Fact
  ├── retract ─────────▶ Retracted
  └── unresolved ──────▶ Disputed

Disputed
  ├── new_evidence ────▶ UnderReview
  └── retain_dispute ──▶ Disputed
```

### 传播要求

Fact 进入 `Challenged`、`Superseded` 或 `Retracted` 时，必须产生影响事件，定位依赖它的：

- Entity、Relationship、Event 和 Change；
- Assessment、Forecast 和 Risk；
- Intelligence Product 和 Alert；
- 评测集和 Evolution Evidence。

受影响对象进入重新评估，不自动删除。

## 三、世界状态对象生命周期

### Entity

```text
Candidate → Resolved → Active
Active → Merged / Split / Superseded / Retired
```

Entity 合并与拆分必须保留旧 ID、映射、证据和影响传播。

### Relationship

```text
Proposed → Verified → Active
Active → Changed / Ended / Disputed / Superseded
```

### Event

```text
Candidate → Corroborated → Active
Active → Updated / Disputed / Superseded
```

### Change

```text
Detected → Validated → Significant / Routine / Noise
Significant → Linked → Consumed
```

`Consumed` 表示已被下游研判或产品处理，不表示 Change 消失。

## 四、Assessment 生命周期

```text
Draft
  │ submit
  ▼
UnderReview
  ├── accept ──────────▶ Accepted
  ├── return ──────────▶ Draft
  ├── reject ──────────▶ Rejected
  └── request_evidence ▶ EvidencePending

EvidencePending
  └── evidence_ready ──▶ Draft

Accepted
  ├── publish_use ─────▶ Active
  ├── new_evidence ────▶ ReassessmentRequired
  ├── replace ─────────▶ Superseded
  └── invalidate ──────▶ Invalidated

ReassessmentRequired
  ├── confirm ─────────▶ Active
  ├── revise ──────────▶ Superseded ──by──▶ New Assessment
  └── invalidate ──────▶ Invalidated
```

## 五、Forecast 生命周期

```text
Draft → UnderReview → Active
Active
  ├── update ──────────▶ Superseded ──by──▶ New Forecast
  ├── invalid_assumption▶ Invalidated
  ├── horizon_reached ─▶ Matured
  └── cancel ──────────▶ Withdrawn

Matured
  │ score
  ▼
Scored
  ├── calibrate ───────▶ Calibrated
  └── archive ─────────▶ Archived
```

Forecast 必须在预测时间窗结束后进入评分，而不是只保留成功案例。

## 六、认识论异常路径

```text
Claim 缺少 Evidence
→ Unresolved
→ 补证任务

多个来源冲突
→ Disputed
→ 大理寺案件
→ 规则 / Agent / 人类裁定

Fact 被撤回
→ Impact Event
→ 下游 Assessment / Product 重新评估

Assessment 证据不足
→ EvidencePending
→ 新 Mission 或数据认知 Task

Forecast 假设失效
→ Invalidated
→ 风险和产品更正
→ Evolution Evidence
```
