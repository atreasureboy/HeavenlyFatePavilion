# Windows W6：IPC、消息契约与同步机制

状态：Seed

记录日期：2026-07-29

来源：Microsoft 官方 Windows IPC、RPC、Named Pipe、Shared Memory 与 Synchronization 资料，以及天机阁映射研究

## 一、本课问题

W1—W5 已经建立了受控调用、对象 Handle、安全上下文、执行实例与资源容器，但多个 Agent、Tool 和 Service 怎样协作仍未回答：

- Agent 之间应该传递自然语言、完整上下文，还是有版本的消息？
- 大量证据和数据应该复制进消息，还是传递对象引用？
- 请求、响应、事件、流和共享状态是否应使用同一种协议？
- 接收者怎样确认发送者、租户、授权范围与委托链？
- 等待 Tool、审核或外部事件时，是否要让大模型会话一直占用资源？
- 超时、断连、重复投递、乱序和部分失败怎样处理？
- Mutex、Semaphore、Event 和 Wait 能否直接映射成 Agent 协作概念？
- 怎样避免 Agent A 等 Agent B、Agent B 又等 Agent A 的分布式死锁？

## 二、Windows 给出的第一项教育：IPC 不是一种东西

Windows 提供多种 IPC 机制，是因为通信需求并不相同：

| Windows 机制 | 擅长的问题 | 关键代价 |
|---|---|---|
| Anonymous Pipe | 相关进程之间的单向字节流，常用于父子进程标准输入输出 | 本机、关系紧密、协议需自定义 |
| Named Pipe | 无关进程或跨机器的双向流 / 消息通信 | 连接、命名、访问控制与背压 |
| RPC | 以有类型接口调用另一进程或机器上的服务 | 客户端与服务端契约更紧 |
| File Mapping / Shared Memory | 大数据量、高性能共享 | 必须自行同步，污染与竞态风险高 |
| Socket | 跨平台、跨网络、协议级通信 | 网络不可靠性与协议治理 |
| Event / Mutex / Semaphore | 发信号、互斥与并发计数 | 只解决协调，不携带业务语义 |
| Wait | 等待对象变为可用、完成或超时 | 阻塞、死锁与资源占用 |

Windows 官方资料明确指出：实际应用往往会同时使用多种 IPC，而不是寻找一种万能通信方式。

对天机阁的对应结论是：

> Agent Communication 不是“大家共享一个聊天记录”，而是多类交互语义共用一套身份、对象、权限、追踪与生命周期底座。

## 三、通信必须先按语义分类

候选通信分类：

```text
Command
要求某主体执行动作；可能改变状态；必须明确授权、幂等和结果

Query
读取或计算结果；不应暗含外部副作用

Reply
回应特定 Command / Query；携带结果、错误或接受状态

Domain Event
已经发生且值得其他领域感知的持久事实

Runtime Signal
实例就绪、心跳、容量变化、取消等短生命周期控制信号

Stream
有顺序、有流控的连续数据或增量结果

Object Transfer / Reference
授予接收方访问某受控对象的 Handle，而非复制对象本身

Bulk Payload
大型文档、模型上下文、证据集和制品；经存储对象传递
```

这些语义不能互相伪装。例如：

- `Domain Event` 不是命令，消费者可以为零或多个；
- `CommandAccepted` 不等于命令已经完成；
- `Runtime Signal` 不应成为长期领域事实的唯一载体；
- `Query` 不得因为“模型觉得有必要”偷偷执行写操作；
- 大型 `Bulk Payload` 不应塞进每一跳消息。

## 四、天机阁的三层通信模型

```text
语义层
Command / Query / Event / Reply / Stream 的业务含义
        │
        ▼
契约层
Contract Envelope、Schema、身份、授权、追踪、幂等、期限
        │
        ▼
传输层
RPC、Message Bus、Queue、Stream、Socket、Local Channel、Object Store
```

同一条语义消息可以迁移传输实现；传输成功不代表语义处理成功。

例如：

```text
“门下审核任务已被 Queue 接收”
≠ “门下已经审核”
≠ “审核通过”
≠ “结果已经持久提交”
```

## 五、Contract Envelope 应成为 IPC 的统一外壳

现有当前架构已定义 `Plane Contract Envelope` 的方向。Windows 研究进一步建议候选字段至少包括：

```text
ContractEnvelope
├── message_id
├── message_kind / operation
├── schema_name / schema_version
├── tenant_id / court_id
├── sender_actor_context
├── effective_delegation_context
├── mission_id / run_id / task_id / attempt_id
├── source_container_id
├── destination_service / channel
├── correlation_id / causation_id
├── idempotency_key
├── created_at / deadline
├── priority / budget_slice
├── risk_class / data_classification
├── trace_context
├── object_handle_references[]
├── payload_manifest
├── reply_channel / expected_reply_schema
├── integrity_proof
└── protocol_extensions
```

消息正文只承载该交互所需的最小语义。租户、权力、期限、风险和对象引用不应隐藏在自然语言 Prompt 中。

## 六、Agent 不应共享整段长上下文

把整个聊天历史、全部证据和所有内部推理复制给每个子 Agent，会造成：

- Token 成本随扇出成倍增长；
- 不相关的敏感信息扩散；
- Prompt Injection 在执行树中传播；
- 每个 Agent 看到不同时间点的可变长上下文；
- 无法证明某次判断究竟使用了哪些输入；
- 数据更新后难以判断哪些结论已经过期；
- 子 Agent 容易把父 Agent 的猜测误当事实。

候选替代：

```text
ContextCapsule
├── immutable capsule_revision
├── task objective
├── scoped facts / claims / evidence references
├── explicit assumptions
├── policy and authority references
├── allowed object handles
├── excluded information classes
├── freshness boundary
└── provenance manifest
```

通信时传递：

```text
小型指令与摘要
+ ContextCapsule 引用
+ Evidence / Document Handle
+ 接收者按需读取的最小权限
```

这类似“通过 Handle 访问对象”，而不是把整个对象复制到每个进程。

## 七、Payload 与对象引用的选择

候选规则：

| 数据形态 | 推荐传递方式 |
|---|---|
| 小型不可变参数 | 消息内联 |
| 大型 Document / Evidence Set | 对象存储引用 + 受控 Handle |
| 高频连续增量 | 有背压的 Stream |
| 可复用查询结果 | 版本化 Snapshot 引用 |
| 机密数据 | 不透明引用，接收方按 Handle 授权读取 |
| 临时同机大块数据 | 受隔离的共享区或零拷贝机制 |
| 跨租户结果 | 重新投影后的安全对象，禁止直接共享私有 Handle |

共享内存的高性能来自避免复制，但它同时引入同步、所有权和数据破坏风险。对分布式 Agent 系统，默认应采用不可变对象与消息传递；共享可变状态只作为被证明必要的局部优化。

## 八、Channel 也应是受控对象

结合 W2，候选 `Channel`：

```text
Channel
├── channel_id / type
├── endpoint identities
├── allowed message schemas
├── directionality
├── tenant / mission scope
├── security descriptor
├── capacity / rate limit
├── ordering guarantee
├── delivery guarantee
├── retention
├── backpressure policy
├── closure / expiry
└── audit policy
```

主体通过 Channel Handle 发送或接收，而不是只知道一个全局 Topic 名称就能通信。

这可以防止：

- 任意 Agent 向皇帝控制通道注入消息；
- 低权限任务订阅整个租户的事件；
- 发送者使用未注册 Schema；
- 接收者把运行信号误当领域事件；
- 已撤销 Agent 继续持有永久通信能力。

## 九、RPC 的启发：接口调用必须携带身份

Windows RPC 支持客户端身份验证、服务端身份验证、Impersonation 与 Delegation。关键教育不是“把所有通信做成 RPC”，而是：

```text
接收请求
≠ 自动拥有调用者的一切权力
```

候选处理步骤：

```text
Authenticate Channel Peer
        ↓
Validate Envelope and Schema
        ↓
Resolve Sender Actor Context
        ↓
Evaluate delegated operation scope
        ↓
Bind request-scoped effective context
        ↓
Perform object-level Handle checks
        ↓
Execute
        ↓
Revert request-scoped context
```

接收服务默认使用自己的服务身份；只有确需“代表调用者访问资源”时，才建立受限、短时、可审计的 Impersonation Context。跨下一跳 Delegation 应更严格，不能让一个普通 RPC 自动扩散仙人的全部权力。

## 十、身份验证不只发生在连接建立时

长连接或消息总线连接通过认证后，仍不能认为连接中的所有未来消息都有效。每条重要消息都应验证：

- 消息属于哪个租户与 Mission；
- 发送主体当前是否仍有效；
- Capability / Handle 是否过期或已撤销；
- Operation 是否在委托范围内；
- Schema 与策略版本是否受支持；
- Deadline 是否已过；
- 重放窗口与幂等键是否合法；
- 消息完整性是否被破坏。

连接身份、消息身份、业务授权和对象访问权是四层不同检查。

## 十一、Event、Mutex、Semaphore 的正确迁移

### 1. Event：状态变化信号

Windows Event Object 具有 signaled / nonsignaled 状态，Manual-reset Event 可以释放多个等待者，Auto-reset Event 通常释放一个等待者。

天机阁可借鉴为运行协调信号：

- 某 Tool 调用已完成；
- 审核结果已就绪；
- 容器进入取消状态；
- 数据快照已发布；
- 外部审批已经到达。

但必须区分：

```text
Synchronization Event（可消费的就绪信号）
≠ Domain Event（已经发生的持久业务事实）
```

前者可能短暂、可重置；后者必须可追溯、可重放。

### 2. Mutex：局部互斥所有权

Mutex 可保护共享资源同一时间只被一个执行者修改。候选用途：

- 单节点临界区；
- 本地缓存结构；
- 同一不可并发适配器。

不应把跨服务业务一致性寄托在一个进程 Mutex 上。分布式写入更需要对象版本、Compare-and-Swap、幂等提交、Lease 或事务边界。

### 3. Semaphore：有限容量许可

Semaphore 的计数代表可同时取得的许可数。它适合映射：

- 某模型供应商最大并发；
- 某来源抓取并发；
- GPU Slot；
- 高风险 Tool 的同时执行数量；
- 租户公平份额。

Semaphore 管“最多多少个”，不回答“谁有权做”和“这项任务是否值得做”。

## 十二、Wait 不应占着模型等待

Windows Wait 可以等待一个或多个对象变为 signaled，也可以设置超时。Agent 系统中若让模型会话一直“思考并等待”，会浪费 Token、连接和 Worker。

候选模式：

```text
Reasoning Turn 发出异步请求
        ↓
持久化 Continuation / Checkpoint
        ↓
释放 Model Invocation 与 Worker
        ↓
注册 Wait Condition + Deadline
        ↓
收到结果 / 取消 / 超时事件
        ↓
Scheduler 创建新的 Reasoning Turn 恢复
```

候选 `WaitCondition`：

```text
WaitCondition
├── wait_id
├── owner_task / continuation
├── any_of[] / all_of[]
├── predicate revisions
├── deadline
├── cancellation_scope
├── resume policy
├── timeout policy
└── durable state
```

默认禁止无限等待。长期监测不是一个永不返回的 Wait，而是持久 Subscription、定时触发与多次短 Run。

## 十三、交付保证必须写清楚

传输系统无法凭一句“消息已发送”保证业务完成。候选协议应显式声明：

```text
Ordering
无序 / 分区内有序 / 因果相关

Delivery
至多一次 / 至少一次

Durability
瞬时 / 持久 / 可重放

Acknowledgement
收到 / 接受 / 执行完成 / 持久提交

Deduplication
message_id / idempotency_key / effect key
```

对跨服务重要操作，默认按“至少一次投递 + 幂等处理 + 持久结果”设计。所谓端到端“恰好一次”通常只能在严格限定的事务边界内成立；一旦涉及邮件、外部 API、模型供应商或现实世界副作用，就必须面对未知完成状态。

## 十四、请求状态不能只有成功和失败

候选交互状态：

```text
Created
→ Sent
→ Received
→ Validated
→ Accepted / Rejected
→ Executing
→ Committed / Failed / Cancelled
→ Acknowledged
```

断连时：

- 请求可能从未到达；
- 请求可能到达但响应丢失；
- 请求可能正在执行；
- 外部副作用可能已经发生；
- 响应可能已经提交但调用者未知。

因此重试前必须依靠 `idempotency_key` 查询或合并旧执行，而不是盲目再做一次。

## 十五、背压是协议，不是性能补丁

Named Pipe、Socket、Stream 和消息队列都存在消费者速度低于生产者的情况。Agent 能快速派生大量任务，使背压成为架构核心：

```text
Channel Capacity
→ Producer Credit
→ Queue Depth Threshold
→ Admission Control
→ Rate Limit
→ Priority Scheduling
→ Spill / Delay / Reject
```

背压信号必须能够向上穿过：

```text
Tool / Model Adapter
→ Task Scheduler
→ 尚书省
→ 中书省重规划
→ 必要时皇帝向仙人解释范围、时间或成本变化
```

不能让队列无限堆积，也不能由下游 Tool 私自丢弃高价值任务。

## 十六、取消与超时必须端到端传播

候选 Envelope 应携带 Deadline 与 Cancellation Scope：

```text
父 Task 取消
→ 停止产生新子请求
→ 向下游传播 Cancellation
→ 撤销等待与未使用预算预留
→ 标记无法取消的在途外部调用
→ 丢弃或隔离迟到结果
→ 保留最终审计与副作用状态
```

局部 Timeout 不一定等于业务失败。接收方可能继续执行，所以调用者需要：

- 查询 Operation Status；
- 使用幂等键恢复；
- 明确是否允许迟到结果；
- 必要时发出独立 Cancel Command；
- 将“不知道是否完成”作为正式状态。

## 十七、死锁与活锁防护

Agent 系统的死锁不只来自 Mutex，还来自治理等待：

```text
中书等待门下先批准计划
门下等待中书先补齐计划

Agent A 持有 Tool Handle，等待 Agent B 的数据
Agent B 持有数据 Lease，等待 Agent A 释放 Tool
```

候选约束：

1. 禁止跨长时模型调用持有排他 Lock；
2. 所有 Wait 必须有 Deadline、取消路径和所有者；
3. 定义全局资源获取顺序，或避免同时持有多个排他资源；
4. 审核退回必须产生新状态，而不是双方互相同步阻塞；
5. Scheduler 维护 Wait-for Graph，检测循环依赖；
6. 重试采用退避、抖动与上限，防止活锁和惊群；
7. 超时后释放本地资源，但不假定远端副作用已撤销；
8. 人类审批不能占用 Agent Instance 与 Worker。

## 十八、通信拓扑建议

```text
仙人
  │ Conversation / Product Interaction
  ▼
皇帝
  │ Governed Command / Reply
  ▼
三省
  ├── Command：规划、调度、审核请求
  ├── Reply：方案、执行与封驳结果
  └── Domain Event：已提交的关键治理事实
        │
        ▼
六部与公共官署
  ├── RPC / Query：有类型同步短调用
  ├── Queue：可重试异步工作
  ├── Event Bus：已发生事实的传播
  ├── Stream：连续变化与大规模增量
  └── Object Handle：证据、资料、模型与 Tool 能力
        │
        ▼
数据底座 / 平台服务 / 外部适配器
```

同步 RPC 只用于能在明确短期限内完成、调用方确实需要立即结果的操作。开放式调查、模型推理、大规模数据加工和人工审批默认异步化。

## 十九、候选架构不变量

```text
INV-W6-01 通信成功不等于语义处理成功或状态已经持久提交。
INV-W6-02 任何重要消息必须显式携带租户、主体、作用域、期限和追踪语义。
INV-W6-03 大型上下文和证据默认通过不可变对象引用与受控 Handle 传递。
INV-W6-04 Channel 是受权限、Schema、容量、生命周期约束的运行对象。
INV-W6-05 接收方不得因收到请求而自动取得发送方全部权力。
INV-W6-06 等待外部结果时应检查点化并释放模型与 Worker。
INV-W6-07 至少一次交付的副作用操作必须幂等、可查询并记录未知完成状态。
INV-W6-08 Runtime Signal 不得成为领域事实的唯一权威记录。
INV-W6-09 所有长时 Wait 必须有 Deadline、取消路径和恢复策略。
INV-W6-10 背压必须向规划与调度层传播，不能只在传输层静默丢弃。
```

这些仍是候选，不自动进入正式不变量目录。

## 二十、Windows 类比的边界

1. Windows 本机 IPC 的低延迟假设不适用于跨区域、跨云和模型供应商；
2. Shared Memory 适合受控同机进程，不适合作为天机阁跨领域知识共享模型；
3. Event Object 是同步原语，不等于 Event Sourcing 中的领域事件；
4. Mutex 不能替代分布式一致性、领域不变量与审批权；
5. RPC 接口类型安全不能保证模型输出的语义正确；
6. Windows IPC 的身份模型不能替代天机阁的租户、Mission、证据与领域权威；
7. ALPC 是重要的 Windows 内部机制，但不应把未作为稳定公共应用契约的内部细节直接升格为天机阁设计依据。

## 二十一、架构价值

- 把“Agent 聊天”提升为有类型、有身份、有权限、有期限的协议；
- 通过对象引用降低 Token 复制、敏感信息扩散与上下文污染；
- 把同步等待转成可恢复 Continuation，释放昂贵模型资源；
- 为重复、乱序、断连、背压、取消与未知完成状态建立统一语义；
- 让三省六部的协作可以被审计、重放、限流和独立演进；
- 为未来跨模型、跨节点、跨租户运行保留传输替换空间。

## 二十二、风险与待验证问题

风险：

- Envelope 过重可能拖慢简单本地调用；
- 过度异步化会增加状态机和可观测性复杂度；
- Schema 治理不善会形成版本爆炸；
- 对象引用过细会导致大量往返和 Handle 管理成本；
- 过度禁止共享状态可能牺牲高吞吐数据处理性能；
- 自动摘要 Context Capsule 可能遗漏关键证据。

待验证：

1. 哪些字段属于所有消息的强制核心，哪些按协议扩展？
2. 皇帝与三省之间哪些交互必须同步，哪些必须异步？
3. Context Capsule 应由中书、尚书还是运行内核构造与审核？
4. Channel Handle 的转授规则如何与 W3 Impersonation 对齐？
5. 分布式 Wait-for Graph 的检测成本是否可接受？
6. 大型证据集的按需读取如何避免 N+1 调用？
7. 迟到的高价值结果应进入原 Run、形成新 Revision，还是成为独立 Evidence？

## 二十三、本课结论

```text
Agent 协作的基础不是共享意识，
而是受治理的消息、对象引用、等待条件和提交协议。

自然语言负责表达开放语义；
Contract Envelope 负责身份、权限、作用域、期限和追踪；
Channel 负责受控传输；
持久对象负责事实；
运行内核负责等待、取消、背压与恢复。
```

本轮没有增加新的一级架构面，也没有改变已封板骨架。它深化智能执行与运行面、平台控制面、平面契约及横切身份/审计机制。

## 二十四、官方参考

- [Microsoft：Interprocess communications](https://learn.microsoft.com/en-us/windows/win32/ipc/interprocess-communications)
- [Microsoft：Named Pipes](https://learn.microsoft.com/en-us/windows/win32/ipc/pipes)
- [Microsoft：Microsoft RPC](https://learn.microsoft.com/en-us/windows/win32/rpc/rpc-start-page)
- [Microsoft：Client Impersonation (RPC)](https://learn.microsoft.com/en-us/windows/win32/rpc/client-impersonation)
- [Microsoft：Quality of Service (RPC)](https://learn.microsoft.com/en-us/windows/win32/rpc/quality-of-service)
- [Microsoft：Event Objects](https://learn.microsoft.com/en-us/windows/win32/sync/event-objects)
- [Microsoft：Mutex Objects](https://learn.microsoft.com/en-us/windows/win32/sync/mutex-objects)
- [Microsoft：Semaphore Objects](https://learn.microsoft.com/en-us/windows/win32/sync/semaphore-objects)
- [Microsoft：Wait Functions](https://learn.microsoft.com/en-us/windows/win32/sync/wait-functions)

## 二十五、关联材料

- [Windows 总学习路线](./windows-as-agent-runtime-reference.md)
- [W2：Object Manager、Handle 与对象生命周期](./windows-w02-object-manager-handles-and-lifetime.md)
- [W3：Access Token、ACL、Privilege 与 Impersonation](./windows-w03-access-token-acl-privilege-and-impersonation.md)
- [W4：Process、Thread 与 Agent 执行上下文](./windows-w04-process-thread-and-agent-execution-context.md)
- [W5：Job Object、资源治理与终止传播](./windows-w05-job-objects-resource-governance-and-termination.md)
- [当前架构：平面间契约](../../03-current-architecture/plane-contracts.md)
- [当前架构：横切机制](../../03-current-architecture/cross-cutting/README.md)
