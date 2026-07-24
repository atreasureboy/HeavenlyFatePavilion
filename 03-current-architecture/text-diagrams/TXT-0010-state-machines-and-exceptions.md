# TXT-0010：天机阁核心状态机与异常路由图

状态：Accepted

## 这张图回答什么

Mission、Run、事实、研判、风险、产品和能力版本如何变化；失败、退回、争议、降级和回滚分别流向哪里。

## 一、治理与执行主状态

```text
Mission
Draft → Proposed → UnderReview
                     ├── Approved → Active → Completed
                     │                 ├── Suspended → Active
                     │                 ├── Cancelled
                     │                 └── Superseded
                     ├── Returned → Draft
                     └── Rejected

Run
Created → Validated → Scheduled → Running
                                  ├── Waiting ─────▶ Running
                                  ├── Paused ──────▶ Running
                                  ├── DegradedRunning
                                  ├── Succeeded
                                  ├── Degraded
                                  ├── Failed
                                  ├── TimedOut
                                  └── Cancelled

Task
Created → Ready → Running
                  ├── Waiting ─────────▶ Ready
                  ├── RetryScheduled ──▶ Ready
                  ├── Compensating ────▶ Compensated
                  ├── Succeeded
                  ├── Failed
                  ├── TimedOut
                  └── Cancelled
```

## 二、事实与研判主状态

```text
Claim
Candidate → Extracted → UnderVerification
                         ├── Verified
                         ├── Disputed ───────▶ UnderVerification
                         ├── Unresolved ─────▶ UnderVerification
                         ├── Rejected
                         └── Superseded

Fact
Proposed → Active
            ├── Challenged → UnderReview
            │                 ├── Active
            │                 ├── Disputed
            │                 ├── Superseded → New Fact
            │                 └── Retracted
            ├── Expired
            ├── Superseded
            └── Retracted

Assessment
Draft → UnderReview → Accepted → Active
                        │          ├── ReassessmentRequired
                        │          ├── Superseded
                        │          └── Invalidated
                        └── EvidencePending → Draft

Forecast
Draft → UnderReview → Active → Matured → Scored → Calibrated / Archived
                        ├── Superseded
                        ├── Invalidated
                        └── Withdrawn
```

## 三、风险、告警与产品

```text
Risk
Candidate → Assessed → Open → Monitoring
                       │       ├── Escalated
                       │       ├── Mitigating
                       │       └── Closed → Reopened
                       └── Invalidated

Alert
Created
├── Merged
├── Suppressed
└── Queued → Delivered → Acknowledged → Resolved
             │             └── Escalated → Resolved
             ├── DeliveryFailed → Queued / Failed
             └── Expired

Product
Draft → UnderReview → Approved → Published
                                  ├── CorrectionRequired
                                  │    └── Corrected Product
                                  ├── Superseded
                                  ├── Withdrawn
                                  └── Archived
```

## 四、能力演进与发布

```text
Change Candidate
Draft → Proposed → Experimenting → Evaluated → GovernanceReview
         │             │              │              ├── Approved
         │             │              │              └── Rejected
         │             │              └── Revised
         │             └── FailedExperiment
         └── Deferred / Rejected

Capability Version
Built → Verified → Staged → Canary → RollingOut → Active
                              │          │           ├── UnderObservation
                              │          │           │     ├── Retained
                              │          │           │     └── RolledBack
                              │          │           └── Superseded
                              └──────────┴───────────────▶ RolledBack
```

## 五、统一异常路由

```text
                         [Exception Record]
                                  │
     ┌──────────┬──────────┬──────┼──────┬──────────┬──────────┐
     ▼          ▼          ▼      ▼      ▼          ▼          ▼
    GOV        CTX/POL     BUD    DAT    EPI/QLT    RUN      PRD/EVO
     │          │          │      │       │          │          │
     ▼          ▼          ▼      ▼       ▼          ▼          ▼
交互/治理   平台/枢密院  平台+户部 认知/底座 门下/大理寺 运行面  产品/演进

S0：记录
S1：自动恢复
S2：显式降级与尚书统筹
S3：门下审核和治理升级
S4：停止相关路径并由人类接管
```

## 六、必须区分

```text
Rejected  = 未获接受
Returned  = 可以补充后重提
Failed    = 执行尝试失败
Cancelled = 经授权终止
Degraded  = 有限结果，质量受损
Disputed  = 认识论冲突未解决
Retracted = 原有接受被撤回
Superseded= 被新版本替代
RolledBack= 发布版本被回退
```

## 七、对应正文

- [核心状态机](../../08-reference-models/state-machines/README.md)
- [异常分类与路由](../../08-reference-models/state-machines/05-exception-taxonomy-and-routing.md)
- [ADR-0012：所有关键状态通过显式事件转换](../../06-architecture-decisions/adrs/ADR-0012-explicit-event-driven-state-transitions.md)
