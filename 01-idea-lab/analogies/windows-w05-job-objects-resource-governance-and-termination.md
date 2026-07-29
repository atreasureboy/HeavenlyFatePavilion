# Windows W5：Job Object、资源治理与终止传播

状态：Seed

记录日期：2026-07-29

来源：Microsoft 官方 Windows Job Object / Nested Job 资料与天机阁映射研究

## 一、本课问题

W4 已经把 Role、Definition Revision、Agent Instance、Reasoning Turn、Model Invocation 与 Task 分开，但单个执行实例仍不足以回答：

- 一次 Run 启动上百个 Agent 与 Tool 时，谁约束整棵执行树？
- Token、费用、并发、时间和外部调用怎样逐级分配与汇总？
- 子任务能否自行放宽父级预算？
- 父级取消后，后台子任务是否还能悄悄运行？
- 实例退出或重试后，已经消耗的资源是否会被“清零”？
- 预算接近上限时，户部“哭穷”究竟是 Agent 情绪，还是确定性系统事件？
- 强制终止执行容器时，领域对象、证据、外部副作用和审计是否也会消失？

## 二、Windows Job Object 提供了什么

Windows Job Object 可以把一组进程作为一个单元管理。它能够：

- 对整组进程设置资源限制；
- 汇总当前与已经退出进程的资源计量；
- 通过 Completion Port 接收进程与限制事件；
- 终止 Job 中的全部进程；
- 让新建子进程默认加入父进程所在 Job；
- 通过嵌套 Job 建立逐级约束与汇总关系。

嵌套 Job 中，子 Job 的进程集合属于父 Job 的进程集合；父级限制继续生效，子级只能增加更严格的约束，不能借嵌套逃离父级上限。计量与通知可以向上汇总，终止则向下传播。

这不是一般意义上的“任务标签”，而是可强制执行的资源与生命周期边界。

## 三、第一项关键分离：Mission 不是 Job

Mission 是有业务语义的长期使命：

```text
Mission
├── 目标与问题
├── 人类授权与责任
├── 允许的数据范围
├── 交付标准
├── 风险等级
├── 时间跨度
└── 多次 Plan / Run / Product
```

Job Object 只关心一组执行实例怎样被约束、计量和终止。它不理解使命为何存在、证据是否充分、推断是否成立。

因此候选模型必须坚持：

```text
Mission（业务授权与目标）
≠ Mission Budget Envelope（使命级预算包络）
≠ Run（一次持久执行记录）
≠ Run Execution Container（一次实际执行容器）
```

一个 Mission 可以长期存在而没有任何活动容器；一次 Run 可以因恢复、迁移或重试使用多个有版本的执行容器，但资源账必须连续。

## 四、候选容器层级

```text
Platform Capacity Pool
平台总容量与全局保留量
        │ allocate
        ▼
Tenant Resource Envelope
租户总配额、公平份额与突发额度
        │ allocate
        ▼
Mission Budget Envelope
使命可消耗的长期预算与风险额度
        │ authorize one execution
        ▼
Run Execution Container
一次 Run 的实际资源、成员和终止边界
        │ subdivide
        ├── Task-group Child Container
        │       ├── Agent Instance
        │       ├── Worker Assignment
        │       └── Tool / Model Invocation
        └── Task-group Child Container
                └── ...
```

不是每个 Task 都必须创建独立容器。只有需要独立配额、并发控制、隔离、终止或归属统计的子树，才建立 Child Container。

容器树也不等于其他树：

```text
业务治理树
谁提出、批准、审核和负责

权力委托树
谁把哪些能力派生给谁

执行包含树
哪些实例必须一同限制与终止

资源计量树
消耗向哪些父账户汇总
```

四者可以关联，但不能用一个 `parent_id` 混为一谈。

## 五、执行容器对象

候选：

```text
ExecutionContainer
├── container_id
├── container_revision
├── tenant_id
├── mission_id / run_id
├── parent_container_id
├── owner_control_lease
├── effective_limit_set
├── delegated_budget_slice
├── member_instances[]
├── child_containers[]
├── cumulative_accounting
├── threshold_subscriptions[]
├── termination_policy
├── lifecycle_state
├── created_by / authorized_by
└── audit_stream
```

容器是运行内核的受控对象，不是 Agent Prompt 中的一段约定。Agent 可以请求调整预算或终止子树，但不能直接修改强制限制。

## 六、限制不只有 Token

候选限制目录：

| 维度 | 示例 |
|---|---|
| 智能计算 | 输入/输出 Token、推理 Token、模型调用次数、模型等级 |
| 金钱 | 总费用、时段费用、外部付费 API |
| 并发 | Agent Instance、Worker、模型请求、Tool 调用、来源抓取 |
| 时间 | Wall-clock、CPU/GPU 时间、截止期、单次调用超时 |
| 数据 | 扫描字节、读取对象数、上下文体积、索引查询量 |
| 存储 | 临时空间、制品、快照、日志保留量 |
| 网络 | 带宽、请求率、目标域、数据出口量 |
| 行为 | 外部副作用次数、消息发送、写操作、重试次数 |
| 质量 | 最低证据覆盖、审核次数、禁止因省钱跳过的控制 |

限制至少分三类：

```text
Hard Limit
达到即拒绝、节流或终止，由运行内核强制

Soft Threshold
接近上限时发出事件，允许重规划、降级或请求增额

Quality / Safety Floor
无论预算多紧，都不得绕过的最低审核、安全和证据要求
```

成本优化不能削弱授权、安全与证据不变量。

## 七、层级限制的组合规则

子容器获得的是父级预算的切片，不是新的独立预算。

```text
Effective Limit(child)
= intersection(
    platform policy,
    tenant envelope,
    mission envelope,
    parent effective limits,
    child requested limits,
    task risk policy
  )
```

候选不变量：

1. 子级有效限制不得弱于任何父级限制；
2. 子级消耗必须计入全部祖先的累计账；
3. 分配给多个子级的可消费额度不得无约束超卖；
4. 父级额度被收紧时，所有后代必须重新计算有效限制；
5. 重试、迁移和实例退出不能清除历史消耗；
6. 高风险能力不能仅因仍有 Token 预算就被允许。

可允许“共享突发池”提高利用率，但超卖必须由确定性调度器控制，并且不能突破租户或平台硬上限。

## 八、先入容器，再开始执行

Windows 中可以先创建挂起进程、把它分配给 Job，再恢复执行。对应到天机阁：

```text
Create Instance Record
        ↓
Bind Security Context
        ↓
Assign Execution Container
        ↓
Install Handle Table / Sandbox / Limits
        ↓
Start First Reasoning Turn
```

核心不变量：

> 不允许任何 Agent、Worker 或高权力 Tool 在归属执行容器尚未确定时开始实质执行。

否则会产生无法计量、无法统一终止、无法定位责任的孤儿执行。

## 九、Breakaway 必须是新授权，而不是逃逸

Windows Job 可以在显式配置下允许子进程脱离 Job。Agent 系统里的“后台继续”“另起调查”“脱离当前 Run”具有同样危险：

```text
父 Run 已取消
        │
        └── 子 Agent 声称任务重要，继续调用 Tool
```

天机阁默认禁止静默 Breakaway。确需脱离时，应建立：

```text
DetachedExecutionDecision
├── reason
├── approving_actor
├── new_mission / run ownership
├── new_parent_container
├── new authority context
├── new budget source
├── retained object handles
├── expiry / review point
└── audit linkage to origin
```

“脱离旧容器”不等于“脱离治理”；它必须先进入另一个有效容器，并重新完成授权与资源分配。

## 十、Accounting 必须跨死亡、重试与迁移

资源账属于 Run / Container 的持久记录，不属于短命 Agent Instance：

```text
Attempt 1 消耗 100k Token 后崩溃
        ↓
Attempt 2 从检查点恢复
        ↓
Run 累计消耗仍从 100k 继续
```

至少保留：

- 请求、预留、实际消耗与释放；
- 按模型、Tool、Agent、Task、数据源和时间窗口的分项；
- 当前成员与已退出成员的消耗；
- 失败、取消、浪费和有效产出的成本；
- 父子容器汇总关系；
- 计量版本、估算误差和最终账单修正。

这使成本治理从“模型 API 账单统计”升级为可解释的执行经济系统。

## 十一、户部“哭穷”应是结构化预算压力协议

“哭穷”不应该由户部 Agent 凭感觉发言，而应由计量系统产生 `BudgetPressureEvent`：

```text
BudgetPressureEvent
├── container_id
├── dimension
├── current / reserved / forecast
├── soft_threshold / hard_limit
├── time_to_exhaustion
├── top_consumers
├── confidence
└── permitted_responses
```

响应阶梯：

```text
提醒
→ 降低非关键并发
→ 切换低成本模型或确定性 Tool
→ 缩小数据范围
→ 重规划
→ 请求仙人 / 有权主体增额
→ 有序排空
→ 到达硬限制后拒绝或终止
```

降级必须在 Mission 的质量底线内；如果剩余预算无法满足最低证据和审核要求，应停止并明确报告“无法可靠完成”，而不是输出廉价但不可信的答案。

## 十二、通知是信号，不是权威状态

Windows Job 可以经 Completion Port 报告事件，但通知机制与强制限制并不完全相同。对分布式天机阁尤其要区分：

```text
强制控制
运行内核必须执行，不能依赖消费者在线

遥测通知
可能延迟、重复、乱序或丢失

持久事实
通过状态查询、账本与事件日志重建
```

Scheduler / Reconciler 收到通知后仍应查询权威容器状态。即使监控消费者崩溃，硬上限和终止策略也必须继续生效。

## 十三、取消、排空与强杀不是同一动作

候选生命周期：

```text
Proposed
→ Authorized
→ Allocated
→ Active
→ Draining
→ Terminating
→ Terminated
→ Reconciled
```

终止协议至少区分：

1. `CancellationRequested`：业务请求停止，不代表执行已经停止；
2. `Draining`：拒绝新工作，允许安全点、结果提交和补偿；
3. `ForceTerminate`：撤销 Lease / Handle，停止全部后代；
4. `Reconcile`：核对最终账、未决副作用、孤儿资源和审计；
5. `BusinessClosure`：由领域流程决定 Task、Run 或 Mission 的最终业务状态。

关闭控制者 Handle 也不应默认等于取消业务。可以借鉴 Kill-on-close，设计显式的 `terminate_on_owner_loss` 策略，用于临时高风险容器；长期监测任务则应转移控制 Lease 或进入安全暂停，不能因一个调度进程重启而消失。

## 十四、向下终止，状态与副作用不能抹除

强制终止容器时：

```text
停止新调用
→ 撤销可撤销 Handle
→ 取消子容器与成员实例
→ 标记在途 Tool / Model 请求
→ 保存检查点与退出记录
→ 盘点外部副作用
→ 执行可用补偿
→ 汇总最终资源账
→ 保留证据、制品与完整审计
```

必须坚持：

```text
终止 Execution Container
≠ 删除 Mission / Run / Task
≠ 回滚已经发生的外部世界
≠ 删除已经取得的证据
≠ 抹除失败与成本记录
```

容器终止属于运行事实；业务取消、失败、部分完成和补偿失败属于领域状态。

## 十五、需要验证的失败场景

| 场景 | 应验证的性质 |
|---|---|
| 子 Agent 风暴 | 并发与模型调用上限能否在监控离线时强制生效 |
| 父 Run 取消 | 所有后代是否停止，是否存在孤儿 Worker |
| 子任务请求增额 | 是否只能由有权父级或人类批准 |
| 调度器崩溃 | Owner Lease 是否接管、暂停或触发显式终止策略 |
| 静默后台任务 | 是否被 Breakaway 默认拒绝 |
| Attempt 重试 | 历史 Token 与费用是否继续累计 |
| 嵌套预算耗尽 | 子级、同级与父级的影响是否可预测 |
| 通知丢失 | Reconciler 能否从权威状态与账本恢复 |
| 强杀有外部写操作的 Tool | 是否留下副作用清单、幂等键和补偿状态 |
| 预算不足但结论高风险 | 是否停止而非跳过门下审核 |

## 十六、候选架构不变量

```text
INV-W5-01 任何可执行实例在首次执行前必须归属一个有效执行容器。
INV-W5-02 子容器不能放宽父容器的有效限制。
INV-W5-03 资源消耗跨实例退出、失败、重试和迁移持续累计。
INV-W5-04 终止向执行包含树下方传播，但不会删除持久领域记录。
INV-W5-05 Breakaway 默认禁止，脱离必须同时获得新容器、新预算和新授权。
INV-W5-06 遥测通知不能替代确定性限制与权威状态。
INV-W5-07 成本降级不能突破安全、授权、证据和审核底线。
INV-W5-08 容器控制权丢失必须进入显式接管、暂停或终止策略。
```

这些仍是候选，不自动进入正式不变量目录。

## 十七、Windows 类比的边界

1. Job 管机器资源，不理解 Mission、证据质量和情报风险；
2. Windows 主要面对一台主机上的进程，而天机阁容器可能跨节点、模型供应商和外部 Tool；
3. Token、模型费用、数据扫描和外部副作用不是 Windows 原生资源；
4. Job 的进程成员关系不能替代 W3 的主体授权与 Handle 权限；
5. 强杀进程不能自动撤销邮件、交易、发布或数据写入等外部副作用；
6. 分布式计量可能延迟，预算预留和最终结算必须处理误差与超卖。

所以应该迁移的是“层级约束、累计计量、显式成员、通知、递归终止”这些原则，而不是照搬 Windows API。

## 十八、本课结论

```text
Mission 说明为什么做、谁授权、何时算完成；
Execution Container 说明谁正在运行、能消耗多少、如何整体停止。

Agent 可以规划资源，
但资源边界必须由确定性运行内核强制。

任何后代都不能凭聪明逃离父级预算、权限和终止传播。
```

本轮没有增加新的一级架构面。候选机制分别落入智能执行与运行面、平台控制面，以及预算、安全、审计等横切机制；它用于深化已封板骨架，而不是重开外层骨架。

## 十九、官方参考

- [Microsoft：Job Objects](https://learn.microsoft.com/en-us/windows/win32/procthread/job-objects)
- [Microsoft：Nested Jobs](https://learn.microsoft.com/en-us/windows/win32/procthread/nested-jobs)
- [Microsoft：JOBOBJECT_CPU_RATE_CONTROL_INFORMATION](https://learn.microsoft.com/en-us/windows/win32/api/winnt/ns-winnt-jobobject_cpu_rate_control_information)
- [Microsoft：Job Object Limits](https://learn.microsoft.com/en-us/windows/win32/api/winnt/ns-winnt-jobobject_basic_limit_information)

## 二十、关联材料

- [Windows 总学习路线](./windows-as-agent-runtime-reference.md)
- [W3：Access Token、ACL、Privilege 与 Impersonation](./windows-w03-access-token-acl-privilege-and-impersonation.md)
- [W4：Process、Thread 与 Agent 执行上下文](./windows-w04-process-thread-and-agent-execution-context.md)
- [治理与执行能力树](../../03-current-architecture/capability-trees/03-governance-and-execution.md)
