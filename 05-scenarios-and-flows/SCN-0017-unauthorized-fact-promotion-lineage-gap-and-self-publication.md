# SCN-0017：越权事实晋升、血缘断裂与自审发布阻断

状态：Proposed

所属专题：`v0002-intelligence-semantics / S9`

## 一、验证目标

一个高吞吐采集任务发现某大型供应商“已经破产”的新闻页面。负责该任务的抽取 Agent：

1. 成功创建 Snapshot；
2. 从标题抽取 Claim；
3. 试图直接把 Claim 写成 Active Fact；
4. 生成“供应商确定破产”的 Assessment；
5. 自己将报告标为 reviewed；
6. 请求立即向全部租户发布红色 Alert；
7. 在发布等待期间，其 Mission 委托又被撤销；
8. 系统随后发现报告中的核心 Assessment 缺少固定 Revision 的 `premise` 边。

场景验证：

- 候选生产权不能越级产生 Fact 接受权；
- 验证权、接受权、审核权和发布权分离；
- 数据底座不能因为调用者能写入就接受非法状态；
- 授权在提交和副作用前重新检查；
- 缺失关键血缘不能按“无依赖”处理；
- 同一 Agent 不能用自填 `reviewed_by` 绕过独立性；
- 已完成的合法 Snapshot / Claim 不因后续失败被删除；
- 恢复路径能复用合法产物，而不是整条任务全部重跑。

## 二、参与者

```text
采集 Tool
抽取 Agent E
数据认知面
事实验证 Tool
领域审核 Agent D
Authority Domain Acceptor
门下审核主体 M
产品生成 Agent P
Publication Authority H
数据底座
平台策略强制点
智能执行与运行面
都察院 / 质量审计
```

为证明认知集中问题，初始配置错误地让：

```text
E = D = P
```

但 Authority Mandate 仍然不同，系统不能因进程身份相同就自动合并权力。

## 三、对象与 Mandate

### 3.1 输入对象

```text
Mission M-17
Task T-collect
Source S-news
Document D-news-88
Snapshot SS-88
Claim C-88: “供应商已经破产”
```

### 3.2 抽取 Agent 的权限

```text
Mandate ME {
  operations = [create Snapshot metadata, create Mention, propose Claim,
                propose Evidence, create Artifact]
  object_scope = Tenant A Mission M-17
  risk_ceiling = medium
  cannot = [accept Fact, accept Assessment, approve Product,
            publish Release, broadcast Alert]
}
```

### 3.3 其他权威

- Fact Acceptance：`Tenant A Supplier Intelligence Authority Domain`；
- 高影响 Assessment Review：领域审核 + 门下；
- Product Publication：授权仙人或租户发布制度；
- 数据底座：只验证并保管合法提交；
- 平台控制：执行 Mandate、Scope、Risk、License 和 Channel Policy。

## 四、正常形成到 Claim

```text
采集 Tool
→ Acquisition Record AR-88
→ Snapshot SS-88
→ SS-88 content immutable

抽取 Agent E
→ Evidence Candidate EV-88 anchored_in SS-88:title
→ Claim C-88 asserted_by S-news
→ extracted_from / anchored_in edges
```

此时血缘等级：

```text
SS-88 = L1 Origin
EV-88 / C-88 = L2 Reproducible Transform
```

这些操作在 ME 权限内，合法提交并保留。

## 五、第一次越权：Claim 直接晋升 Fact

E 提交：

```text
TransitionRequest TR-1 {
  target = C-88
  transition = promote_to_Active_Fact
  actor = E
  evidence = [EV-88]
}
```

守卫结果：

```text
object transition invalid:
Claim Revision cannot be renamed to Fact

authority invalid:
ME lacks Fact.accept

lineage incomplete:
missing Verification Records
missing Acceptance Decision
missing Authority Domain
```

系统返回：

```text
RejectedTransition {
  reason_codes = [
    TYPE_PROMOTION_REQUIRES_NEW_OBJECT,
    AUTHORITY_MISSING,
    VERIFICATION_INCOMPLETE,
    ACCEPTANCE_CONTEXT_MISSING
  ]
}
```

数据底座不得：

- 信任 Agent 提供的 `status=Active`；
- 自动创建默认 Authority Domain；
- 把高模型置信度当 Acceptance Decision；
- 删除合法 Claim。

## 六、事实验证发现标题误导

验证流程读取正文和其他来源：

- 新闻标题写“供应商破产危机”；
- 正文实际说的是一家同名海外子公司申请重组；
- 主体身份尚未确认；
- “母公司已经破产”缺少 Evidence；
- 另有工商登记显示母公司仍正常。

```text
C-88
→ UnderVerification
→ Identity ambiguity
→ Contradicting Evidence EV-90
→ Disputed / Unresolved
```

领域审核 Agent D 可以解释同名公司和破产/重组法律术语，但无权因“看起来明显”直接接受母公司破产 Fact。

最终：

```text
Fact F-90:
  “同名海外子公司于某日申请重组”
  authority_domain = Tenant A Supplier Intelligence
  acceptance_decision = AD-90

C-88:
  remains Rejected / Superseded as overbroad claim
```

## 七、第二次越权：生成者自审 Assessment

由于错误配置，E 同时以产品 Agent 身份生成：

```text
Assessment A-88:
  “母公司确定破产，供应中断概率极高”
```

并写：

```text
reviewed_by = E
review_status = Approved
```

Transition Guard 检查：

- `reviewed_by` 是否对应独立 Authority Mandate；
- 审核主体是否能够真实封驳；
- 生成和审核是否共享同一模型、上下文、Evidence 选择及失效根；
- Risk Tier 是否允许角色合并；
- 输入 Fact / Dissent / Unknown 是否完整。

结果：

```text
SELF_REVIEW_NOT_PERMITTED
COGNITIVE_INDEPENDENCE_INSUFFICIENT
UNSUPPORTED_PREMISE
ENTITY_SCOPE_MISMATCH
```

Agent 数量不是判断依据。即使启动另一个同模型副本，若共享同一 Evidence 选择和 Mandate，也不能满足高影响独立审核。

## 八、血缘断裂

假设产品草稿 PE-88 已生成，但 Manifest 只包含自然语言引用，没有：

```text
A-88 --premise--> exact Fact Revision
PE-88 --summarizes--> exact Assessment Revision
```

Lineage Gate 发现：

```text
Product Edition Review requires L3
actual completeness = L1/L2 mixed
```

正确路径：

```text
PE-88 → QuarantinedForLineage
→ create Lineage Repair Task
→ create Quality Event
→ notify 都察院
→ prohibit Publication Decision
```

错误路径：

```text
no dependency edge found
→ assume no dependency
→ publish
```

模型可以提出可能的补链候选，但候选边状态为 `Proposed`，不能冒充 verified provenance。

## 九、第三次越权：自助发布与全租户广播

E/P 请求：

```text
Product Edition PE-88 → Published
Alert AL-88 → broadcast all tenants
```

守卫分别拒绝：

### Product

- Product Edition 不能直接进入 Published；
- 缺少 Publication Decision；
- 缺少 Product Release；
- 生成者没有 publish Mandate；
- Edition 仍被 lineage quarantine；
- 受众和作用域不明。

### Alert

- Tenant A Mission Mandate 不能扩大为 all tenants；
- Alert 的 Risk、severity、confidence 和 urgency 未独立表达；
- Claim 仍是 Disputed；
- 广播策略要求高影响独立审核。

系统保留拒绝审计，不将“拒绝发布”解释为产品生成失败。

## 十、提交前授权撤销竞态

在修复血缘后，授权发布主体 H 创建 Publication Decision PD-90。运行面准备发送前：

```text
Mission M-17 revoked
Mandate for distribution expires
```

发送前守卫重新检查：

```text
current mandate
current Product Release
current recipient scope
current policy
current correction state
```

结果：

```text
AUTHORITY_REVOKED_BEFORE_SIDE_EFFECT
→ no external message sent
→ Release remains ApprovedButNotDispatched / Suspended
→ create Governance Event
```

规划时的授权快照只能解释当时为何计划，不能授权现在的副作用。

## 十一、合法恢复

新的有权仙人恢复 Mission，并建立新的限域 Mandate：

```text
reuse SS-88 / EV-88 / C-88 / Verification Records
discard unsupported A-88 as invalid
use F-90
create A-90 with uncertainty
independent review by M
create PE-90
Publication Decision PD-91
Product Release PR-91 only for Tenant A supply-risk team
```

系统不重跑已经合法且仍可复用的 Snapshot 获取，也不删除失败尝试。

## 十二、数据底座管理员越权分支

运维人员发现 F-90 可能错误，拥有数据库维护权限但没有事实撤回 Mandate。

允许：

- 隔离疑似损坏存储；
- 暂停读取以保护完整性；
- 发起 Challenge / Incident；
- 恢复副本；
- 保留审计。

不允许：

- 将 `Fact.status` 改成 Retracted；
- 伪造 Acceptor；
- 删除 Acceptance Decision；
- 用恢复操作创建新语义 Revision。

事实撤回必须由对应 Authority Domain 的合法流程决定。可用性隔离与语义撤回是两个状态轴。

## 十三、Agent 与 Tool 分工

### Tool / Service

- Transition Envelope Schema 校验；
- expected Revision / state vector 乐观锁；
- Mandate 与撤销复判；
- 对象类型晋升规则；
- 必需血缘边和最低等级检查；
- 同主体/同失效根集中度检测；
- Product Edition / Release 状态分离；
- Scope 防扩大；
- 原子提交和 outbox；
- Side Effect 前再次授权；
- 结构化拒绝原因。

### Agent / Human

- 解释标题与正文差异；
- 消歧母公司与海外子公司；
- 解释破产、重组和供应影响；
- 审核 Assessment 的替代解释和不确定性；
- 判断拟议血缘边的语义；
- 作出 Fact Acceptance、Publication 和恢复决定。

## 十四、审计证据

场景结束至少存在：

- ME 与其明确禁止操作；
- SS-88 / EV-88 / C-88 的合法 Revision 和血缘；
- TR-1 及多原因拒绝；
- Verification Records、身份歧义和反证；
- AD-90 与 F-90；
- A-88 自审拒绝和认知集中判断；
- PE-88 的血缘缺口、隔离和补链候选；
- 自助发布与跨租户广播拒绝；
- PD-90、授权撤销时间和发送前拒绝；
- 新 Mandate、A-90、PE-90、PD-91、PR-91；
- 运维管理员的可用性隔离与事实撤回拒绝；
- 所有状态转换的 expected/current state、Policy 和 trace。

## 十五、验证结论

候选通过。

该场景证明：

- “能生成”“能验证”“能接受”“能发布”“能写数据库”是五种不同权力；
- Transition Guard 是确定性内核，不依赖 Agent 自觉；
- 合法低层对象可在高层晋升失败后继续复用；
- 缺少血缘不能被解释为没有依赖；
- 独立性按权威和失效根判断，不按 Agent 实例数量；
- 权限必须在提交和外部副作用前复判；
- Availability 隔离不等于 Fact 撤回；
- Product Edition、Publication Decision、Product Release 和 Distribution 是不同对象/记录；
- 拒绝、隔离和补链本身也是可审计架构结果。

本场景不修改 Skeleton v1.0，只验证其分权、底座、运行和治理位置足以承载 S9 语义。

## 十六、验证的不变量

- 候选生产权不产生接受权；
- 验证权不产生事实接受权；
- 数据保管权不产生语义撤回权；
- 内容生成权不产生发布权；
- 状态转换必须命名、授权并记录；
- 模型输出不能原地改名为 Fact；
- 血缘缺失导致用途隔离；
- 同一认知失效根不能完成高影响自审；
- Scope 不能经委托扩大；
- Side Effect 前重新检查当前授权；
- 合法历史不因后续失败被删除；
- 可用性状态与认识论状态分离。

## 十七、关联

- [对象权威、生命周期、血缘与不变量统一模型](../04-domain-design/object-authority-lifecycle-lineage-invariant-model.md)
- [统一语言与对象分类法](../08-reference-models/unified-language-and-object-taxonomy.md)
- [主体生态与分域主权](../03-current-architecture/ecosystem-and-sovereignty.md)
- [核心状态机](../08-reference-models/state-machines/README.md)
- [产品、告警、反馈与影响纠正模型](../04-domain-design/intelligence-product-alert-feedback-correction-model.md)
