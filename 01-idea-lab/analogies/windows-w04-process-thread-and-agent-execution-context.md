# Windows W4：Process、Thread 与 Agent 执行上下文

状态：Seed

记录日期：2026-07-29

来源：Microsoft 官方 Windows Process / Thread 资料与天机阁映射研究

## 一、本课问题

“一个子 Agent 就像一个进程”只够做启发，不能直接成为架构模型。必须继续追问：

- Agent Role 是进程、程序还是配置？
- 一次 Agent 实例和一次模型调用是否相同？
- Task 与 Thread 的关系是什么？
- Worker、Sandbox、Run 和 Mission 谁拥有资源？
- 父 Agent 创建子 Agent 是否自动产生授权？
- Agent 崩溃后 Task、状态和审计是否消失？
- 多个推理步骤共享上下文时怎样避免竞态？

## 二、Windows 的基本分离

Windows Process 提供程序运行所需的资源容器，包括：

- 虚拟地址空间；
- 可执行代码和数据；
- 打开的对象 Handle；
- 安全上下文；
- Process ID；
- 环境变量；
- 优先级和工作集；
- 至少一个执行 Thread。

Thread 才是被调度执行的实体。一个 Process 中的 Threads 共享地址空间和大部分系统资源，但每个 Thread 拥有：

- Thread ID；
- 寄存器与保存的执行上下文；
- 用户栈和内核栈；
- Thread Local Storage；
- 调度优先级；
- 异常处理状态；
- 可选的 Impersonation Token。

因此：

```text
Process
= 隔离、资源、安全与生命周期容器

Thread
= 容器内实际可调度执行流
```

CPU 调度 Thread，不调度抽象的“应用意图”。

## 三、Windows 还区分 Work Item

Windows 可以使用 Thread Pool，把多个短时 Work Item 排队给有限数量的 Worker Thread，而不是为每个工作项创建专属 Thread。

这对 Agent 系统极其重要：

```text
逻辑工作项数量
≠ 必须创建的执行主体数量
```

很多确定性 Task、短推理和模型调用可以由共享 Worker 池承载；只有需要独立安全上下文、故障隔离、长生命周期或专用资源时，才值得创建更重的 Agent Instance / Sandbox。

## 四、天机阁正式概念不应一一硬映射

| 天机阁概念 | Windows 最接近概念 | 为什么不完全等同 |
|---|---|---|
| Agent Role | Program / service definition | Role 是持久职责与配置，不是运行实例 |
| Agent Template / Version | Executable image + configuration | 还包含模型、Prompt、Skill 和治理版本 |
| Agent Instance | Process-like security/resource subject | 可能不拥有独立 OS 进程 |
| Reasoning Turn | Thread activation / work item | 通常是一次模型调用或恢复片段 |
| Model Invocation | External compute request | 可由一个 Turn 发起多次，也可被多个角色复用 |
| Worker | Process / thread-pool worker | 承载执行但不拥有业务任务 |
| Task | Durable work item | 可跨实例重试、暂停和迁移 |
| Run | Job-like execution scope | 包含多个 Task 与实例 |
| Mission | Business mandate | 不是运行资源容器 |
| Context Capsule | Process environment + task-local state snapshot | 还含作用域、证据与语义上下文 |
| Agent Memory | Process-local cache / state | 不能替代持久知识与事件 |
| Execution Event | Process/thread lifecycle event | 必须持久化并与业务状态分开 |

## 五、建议的七层执行身份

```text
Agent Role
稳定职责：皇帝、中书、门下、兵部尚书……
        │ instantiate with version
        ▼
Agent Definition Revision
模型策略、Prompt、Skill、Tool Profile、风险限制
        │ bind to Task / Mandate
        ▼
Agent Instance
安全主体、Handle Table、预算、Sandbox、生命周期
        │ activate
        ▼
Reasoning Turn
一次有输入截点的可恢复推理片段
        │ invoke
        ▼
Model Invocation
对精确模型版本的一次外部或本地计算请求
        │ produce
        ▼
Candidate Output / Tool Intent
        │ governed commit
        ▼
Durable Object / Decision / Execution Event
```

每层都有独立 ID，不能用一个 `agent_id` 吞并全部身份。

## 六、Agent Role 不运行

`皇帝`、`中书省`、`门下省`首先是稳定逻辑 Role：

```text
Role
├── responsibility
├── authority boundary
├── non-responsibility
├── allowed capability classes
├── review obligations
├── default risk ceiling
└── compatible definition versions
```

Role 本身不占 Token、不持有运行时 Handle、不发生崩溃。真正执行的是加载某个 Definition Revision 的 Agent Instance。

这解释了：

```text
皇帝长期存在
≠ 皇帝模型进程永久常驻
```

## 七、Agent Instance 作为 Process-like Container

候选：

```text
AgentInstance
├── instance_id
├── role_id / definition_revision
├── actor_security_context
├── mission_id / run_id / task_id
├── execution_container_id
├── context_capsule_revision
├── capability_handle_table
├── budget_account
├── sandbox_profile
├── model_session_binding
├── parent_execution_instance
├── creation_event
├── lifecycle_state
├── heartbeat / lease
└── exit_record
```

Agent Instance 是逻辑安全与资源主体。它可以实现为：

- 独立进程或容器；
- 共享 Worker 中的隔离会话；
- 远程模型调用序列；
- 持久 Actor 的一次 activation；
- 可恢复状态机实例。

实现变化不能消除其身份、权限、预算和审计边界。

## 八、Reasoning Turn 不是完整 Agent

一次 Turn 应具有冻结的输入截点：

```text
ReasoningTurn
├── turn_id
├── agent_instance_id
├── input_context_manifest
├── knowledge_cutoff
├── handle_snapshot
├── policy / model / prompt / skill revisions
├── budget_slice
├── started_at / deadline
├── model_invocations[]
├── tool_intents[]
├── candidate_outputs[]
└── completion / failure
```

同一 Agent Instance 可以产生多个 Turn；Turn 可以在等待 Tool、审核或外部事件时结束，之后以新 Turn 恢复。

## 九、Task 不是 Thread

Task 是持久、可调度、可重试和可审计的工作单元：

```text
Task
→ 可以排队等待
→ 可以由不同 Worker 尝试
→ 可以启动零个、一个或多个 Agent Instance
→ 可以包含确定性步骤与模型调用
→ 可以在实例崩溃后重试
→ 可以暂停数天后恢复
→ 可以因授权撤销而终止
```

Thread 是一次运行载体；Task 是业务执行承诺。

候选分离：

```text
Task Definition
Task Attempt
Execution Lease
Worker Assignment
Agent Instance
Reasoning Turn
```

不能因为 Attempt 失败就把 Task 直接标记为失败，也不能因为模型请求成功就把 Task 标记为完成。

## 十、Worker 不拥有业务权威

Worker 类似承载 Process / Thread Pool：

- 获取可执行 Task Attempt；
- 加载上下文与 Handle；
- 调用 Agent、Tool 或模型；
- 报告实际状态和资源；
- 响应取消、超时和撤销；
- 释放本地资源。

Worker 不拥有：

- Mission 目标；
- Task 最终业务状态；
- Fact 接受权；
- Product 发布权；
- 自行扩大重试次数和预算；
- 将本地缓存升级为持久知识的权力。

## 十一、创建不等于就绪

Windows 创建 Process / Thread 是异步生命周期的一部分。线程可以以 suspended 状态创建，创建成功也不证明后续初始化与入口执行一定成功。

天机阁候选状态：

```text
Agent Instance
Requested
→ Authorized
→ Allocating
→ Created
→ Initializing
→ Ready
→ Running / Waiting
→ Completing
→ Exited

异常：
AuthorizationDenied
AllocationFailed
InitializationFailed
StartupTimedOut
Crashed
Killed
LeaseLost
```

`Created` 不能被解释成 `Ready`，`model endpoint accepted` 不能被解释成推理成功。

## 十二、父子执行关系不是权力关系

Windows 父进程创建子进程，不意味着子进程天然继承父进程所有 Handle 或拥有父进程的安全权限。Handle 继承需要对象可继承且创建时显式启用。

天机阁必须保存三种不同关系：

```text
Execution Parent
谁创建或派生了实例

Governance Delegator
谁授予 Mission / Task 权力

Authority Root
权力最终来自哪个人类、租户或制度主体
```

三者可以不同。

```text
中书 Agent 建议创建调查 Agent
尚书治理角色批准执行编排
运行内核实际创建实例
仙人授权的 Mission 是权力根
```

不得用 `parent_agent_id` 推导全部授权。

## 十三、上下文与地址空间类比

Process 私有地址空间对应 Agent 的上下文隔离启发：

```text
一个 Agent Instance 的工作上下文
≠ 全租户知识
≠ 其他 Agent 的隐藏推理
≠ 数据底座的直接镜像
```

Context Capsule 应按任务最小化组装：

- Mission、Plan 与验收条件；
- 当前 Task 与 Attempt；
- 允许读取的对象 Revision；
- 必要证据和已知分歧；
- Tool / Model Handle；
- 预算、风险与截止时间；
- 上一 Turn 的显式 Checkpoint。

不应把“共享同一个超长上下文”当作 Agent 协作。

## 十四、共享状态与竞态

Windows 同进程 Threads 共享内存，需要锁、事件、原子操作等同步。Agent 若共享可变 Memory，同样会产生：

- 丢失更新；
- 读取半成品；
- 不同 Agent 覆盖对方结论；
- 旧 Revision 写回；
- 审核对象在审核中被修改；
- 撤销后继续使用旧上下文。

候选规则：

```text
默认共享不可变对象 Revision
→ 通过 Event / Message 传播变化
→ 通过 expected revision 提交
→ 冲突显式进入重试或治理
```

不要让多个 Agent 直接编辑同一块无版本共享 Memory。

## 十五、Thread-local 与 Turn-local

Thread Local Storage 的启发是把只属于当前执行流的状态留在局部：

- 临时解析缓存；
- 当前调用栈；
- 本次重试计数；
- 当前模型响应流；
- 局部计时和性能数据；
- 尚未提交的候选。

Turn-local 状态不能被误认为：

- Agent 长期记忆；
- Mission 状态；
- Fact；
- 审计记录；
- 其他 Turn 的输入。

需要跨 Turn 保留的内容必须形成显式 Checkpoint 或持久对象。

## 十六、等待与挂起

Agent 经常等待：

- Tool 结果；
- 其他 Task；
- 门下审核；
- 人类决定；
- 时间触发；
- 外部事件；
- 预算或资源。

等待时不应让高级模型持续占用：

```text
Turn produces Wait Condition
→ persist checkpoint
→ release model / worker resources
→ retain Task and Handle policy state
→ scheduler waits on event
→ revalidate authorization and context
→ start new Turn
```

这正是逻辑常驻不等于模型进程常驻的实现基础。

## 十七、取消与终止

需要分离：

```text
Cancel Requested
协作式要求停止

Cancellation Observed
执行流开始清理

Force Terminate
运行内核强制终止实例或容器

Task Cancelled
业务状态经治理确认终止

Compensation Required
处理已经发生的副作用
```

关闭 Agent Handle 不会自动终止 Agent；类似 Windows Thread Handle 关闭后线程仍可能运行。终止必须通过明确的 Signal / Cancellation / Terminate 操作。

## 十八、退出与对象寿命

Windows Thread 终止后，其 Thread Object 可以因仍有 Handle 而继续存在。天机阁也应分离：

```text
Execution has stopped
≠ Execution Record has been deleted
```

Agent Instance 退出后仍保留：

- Definition、模型和上下文版本；
- 创建、授权和委托链；
- Handle 使用摘要；
- Token、费用、时间和 Tool 计量；
- Tool Intent 与结果；
- Checkpoint 与 Artifact；
- Exit Reason；
- 已知副作用和未完成补偿；
- Trace / Audit / Causation。

运行资源可回收，审计身份与影响记录继续存在。

## 十九、重试与实例身份

```text
Task
├── Attempt 1
│   └── Agent Instance A → Crashed
├── Attempt 2
│   └── Agent Instance B → TimedOut
└── Attempt 3
    └── Deterministic Tool → Succeeded
```

每个 Attempt、Instance 和 Turn 使用新身份。幂等键可以关联同一业务意图，但不能复用旧实例 ID 冒充连续执行。

重试必须重新检查：

- Mandate；
- Handle 和撤销代数；
- 对象 Revision；
- 预算；
- 风险等级；
- 已发生副作用；
- 上一 Attempt 是否仍在运行。

## 二十、并行性不等于认知独立性

在同一 Worker、同一模型、同一 Context Capsule 中并行启动多个 Turn，只增加并发，不自动增加认知独立性。

认知独立性取决于：

- 不同数据和证据路径；
- 不同模型或故障根；
- 独立 Prompt / Context；
- 不共享中间结论；
- 独立 Mandate 和审核责任；
- 输出在汇合前保持隔离。

Process / Thread 数量只证明执行并行度。

## 二十一、候选执行不变量

```text
Role ≠ Definition Revision ≠ Instance ≠ Turn ≠ Model Invocation
Task ≠ Attempt ≠ Worker Assignment
Execution Parent ≠ Governance Delegator ≠ Authority Root
Created ≠ Initialized ≠ Ready ≠ Succeeded
Close Handle ≠ Cancel ≠ Terminate
Process-local Memory ≠ Durable Knowledge
Parallel Execution ≠ Independent Cognition
Instance Exit ≠ Record Deletion
```

## 二十二、对现有骨架的影响

不新增一级面。

候选强化：

1. Agent Role、Definition Revision、Instance、Turn、Model Invocation 五级身份分离；
2. Task、Attempt、Lease、Worker Assignment 与 Agent Instance 分离；
3. 父子执行树与权力委托树分离；
4. Agent Instance 作为 Process-like 安全与资源主体，不绑定物理进程；
5. Context Capsule 采用最小、版本化、不可变输入；
6. 等待时释放高级模型资源，通过 Checkpoint 恢复；
7. 重试产生新 Attempt / Instance，并重新验证权限与副作用；
8. Agent 退出后保留完整执行与影响记录。

以上保持 `Seed`，等待 W5 Job Object、W6 IPC 与运行专题共同验证。

## 二十三、本课结论

> Agent 是逻辑职责、版本化定义、运行实例、推理激活和模型调用的组合，不是一个不可拆分的“智能进程”。

最小映射：

```text
Role       → 谁负责
Definition → 用什么能力版本负责
Instance   → 哪个受控安全与资源主体在运行
Turn       → 当前执行到哪一段推理
Invocation → 调用了哪个精确模型
Task       → 哪项持久工作承诺需要完成
```

## 二十四、官方参考

- [About Processes and Threads](https://learn.microsoft.com/en-us/windows/win32/procthread/about-processes-and-threads)
- [Creating Threads](https://learn.microsoft.com/en-us/windows/win32/procthread/creating-threads)
- [Thread Handles and Identifiers](https://learn.microsoft.com/en-us/windows/win32/procthread/thread-handles-and-identifiers)
- [Handle Inheritance](https://learn.microsoft.com/en-us/windows/win32/sysinfo/handle-inheritance)
- [Process Creation Flags](https://learn.microsoft.com/en-us/windows/win32/procthread/process-creation-flags)

## 二十五、关联材料

- [Windows 学习总路线](./windows-as-agent-runtime-reference.md)
- [W1：用户态与内核边界](./windows-w01-user-kernel-and-system-call-boundary.md)
- [W2：Object Manager 与 Handle](./windows-w02-object-manager-handles-and-lifetime.md)
- [W3：Access Token 与授权](./windows-w03-access-token-acl-privilege-and-impersonation.md)
- [治理与执行能力树](../../03-current-architecture/capability-trees/03-governance-and-execution.md)
- [ADR-0015：持久授权与临时执行实例分离](../../06-architecture-decisions/adrs/ADR-0015-separate-durable-mandates-from-execution.md)
