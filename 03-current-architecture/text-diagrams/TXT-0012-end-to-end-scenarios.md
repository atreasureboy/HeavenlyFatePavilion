# TXT-0012：天机阁端到端正常与失败场景图

状态：Accepted

## 这张图回答什么

一项复杂情报任务如何贯穿十个一级面；证据冲突、预算耗尽、权限撤销、事实更正和模型退化如何回流，而不是在某个 Agent 内部消失。

## 一、复杂调查正常路径

```text
仙人
 │ 目标、范围、决定
 ▼
[人机交互面 / 皇帝投影]
 │ Intent + Tenant/User Context
 ▼
[智能治理面 / 皇帝本体 + 中书]
 │ Mission + Plan + 验收/证据/预算/风险
 ├────────▶ [门下预审：权限、越界与最低质量]
 ▼
[尚书统筹]
 │ Run / Task Graph
 ▼
[智能执行与运行面] ◀──── [平台控制面：Policy / Budget / Version]
 │
 ├──▶ [领域能力面 / 六部：领域语义、Agent、Tool]
 ├──▶ [数据认知面：掌数 → 联结 → 察变]
 │       │ Evidence → Claim → Fact → World State
 │       ▼
 │    [数据底座面：原件、版本、血缘、审计]
 │       │
 │       ▼
 └──▶ [情报研判面：推势 → 卜算 → 警险]
         │ Assessment → Forecast → Risk → Recommendation
         ▼
      [门下反证 / 大理寺争议 / 都察院审计]
         │ Decision
         ▼
      [情报产品面：报告 / Alert / API]
         │
         ▼
      [皇帝解释与交付] ─────▶ 仙人
         │ Feedback
         └──────────────▶ [能力演进面]
```

## 二、低风险简化路径

```text
公共信息快速查询
→ 入口仍绑定租户与主体
→ 规则判定 R0 + Overview
→ 已有公共 Fact / Change / 有效 Assessment
→ Tool 检索、引用和模板
→ 皇帝解释

省略：完整三省审议、所有部级 Agent、重复采集
保留：作用域、权限、证据标记、预算、质量、审计和生命周期
```

## 三、证据冲突

```text
Evidence A → Claim A ┐
                     ├─▶ Conflict → Disputed
Evidence B → Claim B ┘                  │
                                       ├─▶ 补证 / 吏部解释
                                       ├─▶ 门下反证
                                       └─▶ 大理寺裁定
                                             ├─ Verified / Rejected
                                             ├─ 拆成不同 Fact
                                             └─ 保留 Unresolved
```

原始证据和双方主张始终保留；裁定不等于改写来源。

## 四、运行中控制变化

```text
Run Running
├── 预算接近耗尽
│   → Budget Pressure
│   → 暂停新分支
│   → 复用/重排/缩小/追加
│   → 门下确认未降低底线
│   → Running / Degraded / Cancelled
│
└── 权限被撤销
    → Revocation Event
    → 强制点阻止新调用与副作用
    → 撤销令牌、隔离结果、补偿评估
    → 换负责人 / 缩小范围 / Cancelled
```

预算变化由计划重构解决；权限变化在所有关键副作用点重新判定。

## 五、事实更正的反向传播

```text
Source Correction
→ Fact: Active → Challenged → Retracted
→ 血缘影响查询
   ├── Assessment → ReassessmentRequired
   ├── Forecast   → Invalidated / Rescored
   ├── Risk       → Reassessed
   ├── Alert      → Resolved / New Corrective Alert
   └── Product    → CorrectionRequired → Corrected / Withdrawn
→ 皇帝解释并通知已知下游
```

## 六、能力退化与回滚

```text
运行质量/成本异常
→ Evolution Evidence
→ Capability Gap
→ 停止扩大 Canary
→ Capability Version RolledBack
→ 定位问题版本产生的对象
→ 重算 / 拆分 / 重评 / 更正
→ 新候选实验与治理审核
→ Staged → Canary → RollingOut
```

模型不能批准自身发布，也不能删除失败版本的影响记录。

## 七、跨租户联合空间

```text
Tenant A 私有层 ──显式授权──┐
                            ├─▶ Collaboration Space C
Public Baseline ────────────┤    ├── 联合 Mission
Tenant B 私有层 ──显式授权──┘    ├── C 范围能力令牌
                                 ├── 联合派生对象
                                 └── 联合/A私有/B私有产品

协作结束
→ 撤销未来访问
→ 处置 C 派生对象
→ A、B 原租户边界继续存在
```

## 八、箭头语义与刻意省略

- `→` 表示对象、控制或状态向下一阶段传递；
- `▶` 表示跨面调用或影响传播；
- 图中把多次异步事件压缩为逻辑时序；
- 不规定 API、消息队列、数据库、模型或物理部署；
- 不在一张图中穷举九个场景的全部异常。

## 九、权威来源与场景

- [场景集与验证模板](../../05-scenarios-and-flows/README.md)
- [第一批端到端场景覆盖矩阵](../../05-scenarios-and-flows/scenario-coverage-matrix.md)
- [七维横切治理骨架](../cross-cutting/README.md)
- [核心状态机与异常路由](../../08-reference-models/state-machines/README.md)
