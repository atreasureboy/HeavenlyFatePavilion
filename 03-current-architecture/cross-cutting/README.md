# 天机阁横切治理骨架

状态：Accepted

生效日期：2026-07-24

## 一、目的

横切维度不是十个一级面之外的“第十一个面”，而是每次交互、每个对象和每条执行路径都必须携带并接受强制检查的治理语义。

第一版固定七个横切维度：

1. 多租户；
2. 身份与权限；
3. 证据与血缘；
4. 预算与资源；
5. 风险与质量；
6. 审计与追踪；
7. 数据生命周期。

## 二、统一落位模型

每个横切维度都必须回答六个问题：

```text
Authority   谁拥有规则和权威状态
Context     哪些语义随调用传播
Enforcement 在哪些位置强制检查
Evidence    如何证明检查确实发生
Failure     失败后阻断、退回、降级还是升级
Evolution   规则如何版本化、评测和变更
```

统一控制链：

```text
平台控制面发布版本化政策与期望状态
        │
        ▼
入口解析并绑定不可缺省的 Context Envelope
        │
        ▼
每次跨面契约传播上下文引用
        │
        ▼
运行时在数据、Tool、模型、产品等边界执行策略
        │
        ▼
各面产出领域结果及控制证据
        │
        ▼
数据底座保存血缘、账本、审计与生命周期记录
        │
        ▼
治理面审核高风险例外，演进面评估规则效果
```

## 三、控制角色

| 角色 | 含义 | 典型归属 |
|---|---|---|
| Policy Authority | 定义规则、阈值和版本 | 平台控制面；证据语义由数据认知/底座共同定义 |
| Policy Decision Point | 对上下文和请求作确定性判定 | 平台控制面策略服务 |
| Policy Enforcement Point | 在动作发生前强制执行结果 | 入口、运行时、数据访问、模型网关、Tool 网关、产品分发 |
| Domain Validator | 检查领域语义是否满足标准 | 数据认知、研判、领域能力、产品面 |
| Governance Reviewer | 对高影响例外、封驳和升级作决定 | 智能治理面 |
| Record Authority | 保存不可静默篡改的权威记录 | 数据底座面 |
| Observer / Assessor | 汇总质量、成本和异常并提出改进 | 能力演进面 |

Agent 可以解释政策、发现新型风险和提出例外申请，但权限、租户隔离、硬预算、保留期限和审计写入不能依赖 Agent 自觉执行。

## 四、统一横切上下文

`Context Envelope` 与 `Plane Contract Envelope` 共享以下横切语义：

```text
Cross-cutting Context
├── scope
│   ├── platform_scope
│   ├── tenant_id
│   ├── workspace_id / collaboration_space_id
│   └── data_scope
├── identity
│   ├── subject_id / actor_type
│   ├── delegation_chain
│   └── credential / session assurance
├── authorization
│   ├── policy_set_version
│   ├── granted_capabilities
│   └── obligations / approval_refs
├── evidence
│   ├── evidence_requirement
│   ├── provenance_refs
│   └── freshness / confidence constraints
├── resource
│   ├── budget_envelope
│   ├── consumption_ledger_ref
│   └── deadline / priority
├── risk_quality
│   ├── risk_tier
│   ├── quality_profile
│   └── degradation_policy
├── trace
│   ├── trace_id / causation_id
│   ├── decision_refs
│   └── audit_class
└── lifecycle
    ├── classification
    ├── retention_policy
    ├── residency
    └── legal_hold / deletion_status
```

字段可以通过不可变引用解析，不要求在每条消息中完整复制；任何接收方都不得把不可解析当作“采用默认宽松值”。

## 五、七条全局失败原则

1. 租户或作用域无法确定：拒绝，不进入业务处理。
2. 身份、授权或委托无法证明：拒绝或请求显式批准。
3. 证据不满足承诺标准：退回、标记争议或显式降级，不能提升为事实。
4. 预算不足：缩小计划、申请追加或返回有限结果，不能静默超支。
5. 风险或质量超过阈值：提高审核、隔离结果、停止相关路径或交人类接管。
6. 审计不可写或 trace 断裂：高影响动作停止；只读低风险路径也必须显式降级。
7. 生命周期或地域策略冲突：阻止读写、分发、训练或删除动作，进入策略裁定。

## 六、文档索引

1. [多租户、身份与授权](./01-tenancy-identity-authorization.md)
2. [证据、血缘与质量](./02-evidence-lineage-quality.md)
3. [预算、资源与风险](./03-budget-resource-risk.md)
4. [审计、追踪与数据生命周期](./04-audit-trace-lifecycle.md)
5. [七维度—十面责任矩阵](./05-seven-dimensions-ten-planes-matrix.md)

总览文字图：[TXT-0011：七维横切治理图](../text-diagrams/TXT-0011-cross-cutting-governance.md)
