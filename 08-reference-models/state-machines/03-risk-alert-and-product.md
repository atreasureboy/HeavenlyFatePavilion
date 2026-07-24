# 风险、告警与产品状态机

状态：Accepted

## 一、Risk 生命周期

```text
Candidate
  │ assess
  ▼
Assessed
  ├── accept ──────────▶ Open
  ├── reject ──────────▶ Rejected
  └── need_evidence ───▶ EvidencePending

Open
  ├── monitor ─────────▶ Monitoring
  ├── escalate ────────▶ Escalated
  ├── mitigate ────────▶ Mitigating
  ├── invalidate ──────▶ Invalidated
  └── close ───────────▶ Closed

Monitoring
  ├── escalate ────────▶ Escalated
  ├── mitigate ────────▶ Mitigating
  └── close ───────────▶ Closed

Escalated
  ├── mitigate ────────▶ Mitigating
  ├── downgrade ───────▶ Monitoring
  └── close ───────────▶ Closed

Mitigating
  ├── residual_risk ───▶ Monitoring
  └── resolved ────────▶ Closed

Closed
  └── new_evidence ────▶ Reopened

Reopened → Assessed
```

Risk 状态描述风险判断和跟踪，不表示外部缓解行动已经获得授权。

## 二、Alert 生命周期

```text
Created
  ├── deduplicate ─────▶ Merged
  ├── suppress ────────▶ Suppressed
  └── queue ───────────▶ Queued

Queued
  ├── deliver ─────────▶ Delivered
  ├── delivery_fail ───▶ DeliveryFailed
  └── expire ──────────▶ Expired

DeliveryFailed
  ├── retry ───────────▶ Queued
  └── abandon ─────────▶ Failed

Delivered
  ├── acknowledge ─────▶ Acknowledged
  ├── escalate ────────▶ Escalated
  └── expire ──────────▶ Expired

Acknowledged
  ├── resolve ─────────▶ Resolved
  └── escalate ────────▶ Escalated

Escalated
  └── resolve ─────────▶ Resolved
```

`Suppressed`、`Merged` 和 `Expired` 不等于风险解除；它们只描述 Alert 的交付生命周期。

## 三、Intelligence Product 生命周期

```text
Draft
  │ submit_review
  ▼
UnderReview
  ├── approve ─────────▶ Approved
  ├── return ──────────▶ Draft
  └── reject ──────────▶ Rejected

Approved
  │ publish
  ▼
Published
  ├── evidence_changed ▶ CorrectionRequired
  ├── replace ─────────▶ Superseded
  ├── withdraw ────────▶ Withdrawn
  └── archive ─────────▶ Archived

CorrectionRequired
  ├── correct ─────────▶ Superseded ──by──▶ Corrected Product
  └── withdraw ────────▶ Withdrawn
```

发布后的更正必须明确：

- 哪些结论变化；
- 变化原因；
- 影响的受众和渠道；
- 是否需要重新通知；
- 旧版本是否仍可阅读；
- 底层 Fact、Assessment、Forecast 或 Risk 的新版本。

## 四、Recommendation 与 Decision

```text
Recommendation:
Draft → Reviewed → Available → Superseded / Withdrawn

Decision:
Proposed → Made → Effective → Fulfilled / Revoked / Superseded
```

Recommendation 可以被 Decision 引用，但不能自动转换为 Decision。

## 五、风险与产品异常路径

```text
Risk 证据不足
→ EvidencePending
→ 数据认知补证

Alert 风暴
→ Merged / Suppressed
→ 保留原始触发和 Risk 关系

高风险未确认送达
→ DeliveryFailed
→ 重试和备用渠道
→ 治理升级

已发布产品依赖 Fact 被撤回
→ CorrectionRequired
→ 影响分析
→ Correct / Withdraw
→ 通知既有受众
```
