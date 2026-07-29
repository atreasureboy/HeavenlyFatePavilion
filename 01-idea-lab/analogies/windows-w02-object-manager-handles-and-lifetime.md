# Windows W2：Object Manager、Handle 与对象生命周期

状态：Seed

记录日期：2026-07-29

来源：Microsoft 官方 Windows Object Manager、Handle 与对象接口资料

## 一、本课问题

Windows 为什么不把文件、进程、事件等资源的内部结构直接交给调用者，而是：

```text
定义对象类型
→ 创建或打开对象
→ 返回不透明 Handle
→ 通过受支持操作访问
→ 关闭 Handle
→ 引用归零后回收对象
```

对天机阁而言，本课要回答：

1. Agent 应怎样持有能力，而不是持有原始凭据；
2. 对象发现、授权、访问和生命周期怎样分离；
3. 子 Agent 怎样得到收窄后的能力；
4. Agent 退出后哪些运行资源必须自动回收；
5. Windows 对象与天机阁领域对象为何不能粗暴合并。

## 二、Windows 的对象模型

Windows Object Manager 管理内核对象的创建、销毁、命名空间、访问权、资源归属和生命周期。进程、线程、文件、事件、互斥量、Job、Section、Access Token 和符号链接等都可以作为有类型对象存在。

Windows 是 object-based，而不是要求所有对象遵循 C++ 类层次。定义对象类型的系统组件掌握内部结构，其他组件通过公开的支持例程操作；一些对象结构保持 opaque，可以在不破坏调用方的情况下替换内部实现。

用户态通常不能直接取得对象本体，而是得到一个 Handle：

```text
Handle
→ 调用进程 Handle Table 中的条目
→ 指向对象
+ 已授予的访问权
+ Handle 属性
```

Handle 的数值只在对应进程上下文中有意义。继承或 DuplicateHandle 得到的新 Handle 值可以不同，但仍指向同一对象；复制时可以决定新访问权和是否允许继续继承。

## 三、五个必须分开的概念

```text
Object Type
定义对象允许哪些状态与操作

Object Identity
某个运行资源实例本身

Object Name
可选的发现路径，不是访问权

Handle
某主体在特定上下文中访问对象的间接引用

Reference / Lease
保证对象在某段操作期间仍然有效的生命周期关系
```

关键不等式：

```text
知道名称 ≠ 拥有 Handle
拥有 Handle ≠ 拥有所有操作权
复制 Handle ≠ 复制对象
关闭 Handle ≠ 删除领域历史
对象不可见 ≠ 对象不存在
名称空间 ≠ 授权系统
```

## 四、为什么不透明 Handle 很重要

### 4.1 隐藏内部结构

Agent 不需要知道 Tool Gateway、数据存储、模型端点或秘密租约的内部实现。对象提供者可以替换实现，只要对象类型和操作契约保持兼容。

### 4.2 绑定访问权

Handle 不只是对象 ID。它表达“某主体通过哪种已授权方式访问该对象”。

### 4.3 限制传播

跨进程继承或复制需要显式行为。天机阁可据此设计子 Agent 能力委托，而不是把父 Agent 的全部工具和凭据复制进 Prompt。

### 4.4 管理生命周期

Handle 关闭、主体退出、租约到期或权限撤销可以释放运行资源。对象若仍被其他主体或内核操作引用，则不能被过早销毁。

### 4.5 统一审计

每次操作都能从 Handle 追踪到持有者、对象、操作掩码、授予依据和委托链。

## 五、天机阁必须区分两种对象体系

### 5.1 领域与权威对象

已有情报语义基线中的：

- Source、Snapshot、Evidence；
- Claim、Proposition、Fact Aggregate；
- Entity、Relationship、Event、Change；
- Assessment、Scenario、Forecast、Risk；
- Mission、Plan、Decision、Governance Case；
- Product Edition、Feedback、Evolution Evidence。

这些对象表达业务、知识、治理和历史语义，具有不可变 Revision、Authority Domain 与血缘。

### 5.2 运行时受控对象

Windows 类比主要适用于：

- Agent Instance；
- Worker / Sandbox；
- Tool Endpoint；
- Model Endpoint；
- Connector Session；
- Data View / Query Session；
- Secret Lease；
- IPC Channel；
- Budget Reservation；
- Execution Container；
- Cancellation / Stop Token；
- Object Subscription；
- Capability Handle 本身。

运行时对象管理器可以为领域对象签发访问 Handle，但不能吞并领域对象的身份、Revision 和语义权威。

```text
Fact Aggregate
→ 是长期领域对象

ReadFactRevision Handle
→ 是某主体在某条件下读取精确 Revision 的运行时能力
```

## 六、候选 Runtime Object Manager

```text
Runtime Object Manager
├── Type Registry
├── Object Directory / Namespace Resolver
├── Object Instance Registry
├── Handle Table / Capability Registry
├── Access Mask Validator
├── Delegation / Duplication Controller
├── Reference / Lease Manager
├── Revocation Index
├── Cleanup / Reaper
└── Audit Adapter
```

它是平台控制面、智能执行与运行面和横切强制机制的协作能力，不自动成为新的一级面，也不要求一个中央单体服务。

## 七、候选运行时对象类型

| 对象类型 | 代表资源 | 典型操作 |
|---|---|---|
| AgentInstance | 一次受控模型执行主体 | inspect、signal、cancel |
| ExecutionContainer | Run / 子任务执行树容器 | assign、limit、terminate |
| ToolEndpoint | 可调用工具能力 | invoke、inspect |
| ModelEndpoint | 模型服务和策略化版本 | infer、embed |
| DataView | 授权过滤后的数据视图 | query、subscribe |
| ObjectRevisionView | 精确领域对象 Revision | read、cite |
| SecretLease | 短期秘密或连接能力 | use，不允许读取明文 |
| Channel | Agent / Tool 间通信通道 | send、receive、close |
| BudgetReservation | 已预留资源 | consume、release |
| TransitionPermit | 指定对象与状态转换许可 | commit、cancel |
| ArtifactBuffer | 临时中间产物 | append、seal、discard |
| CancellationToken | 停止信号 | observe、trigger |

一个类型必须先定义合法操作、访问掩码、状态、创建者、可复制性、可命名性、生命周期和销毁规则。

## 八、Capability Handle 骨架

```text
CapabilityHandle
├── opaque_handle_id
├── holder_actor_instance
├── runtime_object_id
├── object_type
├── allowed_operations / access_mask
├── tenant_scope / authority_domain
├── purpose / jurisdiction
├── mission_id / run_id / task_id
├── mandate_revision
├── valid_from / expires_at
├── delegation_depth / duplication_policy
├── revocation_generation
├── use_limit / rate_limit / budget_binding
├── audit_class
└── issued_by / issuance_decision
```

Handle 应是不透明、不可猜测或具备完整性保护的引用。Agent 上下文只获得 Handle 及其最小可解释元数据，不获得底层 Token、API Key、数据库连接串或内部对象指针。

## 九、访问掩码必须按类型定义

不能只有 `read / write / admin` 三个笼统权限。

示例：

```text
DataView:
DISCOVER / QUERY / EXPORT / SUBSCRIBE

ToolEndpoint:
INSPECT_SCHEMA / INVOKE / CANCEL / VIEW_USAGE

ProductEdition:
READ / CITE / PROPOSE_REVISION / SUBMIT_REVIEW

ProductRelease:
PROPOSE / AUTHORIZE / PUBLISH / REVOKE

AgentInstance:
INSPECT / SIGNAL / PAUSE / CANCEL / TERMINATE
```

同一个主体可以持有 `PROPOSE_REVISION`，但没有 `AUTHORIZE`；持有 `QUERY`，但没有 `EXPORT`。

## 十、名称空间

候选逻辑目录：

```text
/
├── platform/
│   ├── capabilities/
│   ├── models/
│   └── policies/
├── public/
│   ├── sources/
│   └── knowledge-views/
├── tenants/{tenant_id}/
│   ├── courts/{court_id}/
│   ├── missions/{mission_id}/
│   ├── runs/{run_id}/
│   └── private-capabilities/
├── collaborations/{space_id}/
└── ephemeral/{execution_container_id}/
```

这只是发现命名空间，不是物理目录，也不代表知道路径就能访问。

名称解析必须产生：

```text
Name
→ Candidate Object Identity
→ current scope / policy check
→ Open Decision
→ Handle
```

公共对象必须显式位于公共作用域，不能用缺失 tenant 表示公共。

## 十一、Open、Create 与 Resolve 必须分离

```text
Resolve
按名称或类型发现候选对象，不授予使用权

Open
对既有对象执行访问检查并签发 Handle

Create
创建新对象实例，再按创建结果签发初始 Handle

Use
校验 Handle 类型、操作权、状态、租约和撤销代数

Close
释放该主体的 Handle 和相关引用
```

Agent 说“帮我打开所有数据”不能直接映射成 Open。必须先收敛对象类型、作用域、用途、查询半径和最小访问掩码。

## 十二、委托、复制与继承

Windows Handle 继承需要 Handle 可继承且创建子进程时显式允许。天机阁应采取更严格的默认：

```text
默认：
子 Agent 继承零 Handle

父主体请求委托：
选择精确 Handle
→ 收窄 operations
→ 收窄 scope / purpose / time
→ 降低 delegation depth
→ 绑定 child actor + task
→ 记录 Delegation Decision
→ 签发新的 child-local Handle
```

候选不变量：

```text
child_rights ⊆ parent_delegable_rights
child_scope ⊆ parent_scope
child_expiry ≤ parent_expiry
child_delegation_depth < parent_delegation_depth
```

不得把父 Handle 的字符串直接复制给子 Agent；不同主体的 Handle 应具有独立身份、撤销和审计记录。

## 十三、引用、租约与销毁

Windows 中关闭最后一个 Handle 通常会使对象可被销毁，但内核引用或正在进行的等待仍可能维持对象生命。

天机阁需要至少三种生命周期关系：

```text
Ownership Reference
对象由哪个执行容器负责清理

Handle Reference
哪些主体仍持有访问能力

In-flight Lease
哪些操作正在使用对象，不能立即回收
```

候选销毁条件：

```text
no durable retention requirement
+ no active owner
+ no open handle
+ no in-flight lease
+ no legal / audit hold
+ cleanup policy satisfied
= eligible for runtime destruction
```

领域对象的撤回、删除和保留另有语义，不能因为运行时 Handle 归零就删除历史 Fact、Decision 或 Audit。

## 十四、关闭、撤销、过期与删除

```text
Close
某持有者主动释放 Handle

Expire
Handle 或 Lease 到达时间边界

Revoke
发行者或权威主体使 Handle 立即失效

Destroy
回收运行时对象实例

Delete
按领域与保留政策处理持久对象
```

五者必须分别记录。Agent 退出时运行时自动 Close，不表示删除其产生的 Artifact、Execution Event 或审计记录。

## 十五、撤销传播

若 Mandate、租户成员资格、来源许可或 Tool 版本被撤销：

```text
Authority Change
→ Revocation Index 定位相关 Handle
→ revocation_generation 增长
→ 新调用拒绝
→ in-flight operation 按策略取消 / 隔离 / 完成
→ 子 Handle 递归失效
→ Execution Container 重评
→ 记录影响与未完成副作用
```

高风险调用必须在线或以可证明新鲜度检查撤销状态；低风险缓存路径也必须有最大陈旧窗口。

## 十六、Handle 安全失败

| 失败 | 处置 |
|---|---|
| Handle 类型不匹配 | 拒绝，记录 capability misuse |
| Handle 属于另一 Actor | 拒绝，按冒充调查 |
| Handle 已过期或撤销 | 拒绝，传播取消 |
| 操作不在 access mask | 拒绝，不自动升级 |
| 目标 Revision 已变化 | 返回 conflict，不重定向到最新版 |
| 对象正在销毁 | 拒绝新租约，等待清理 |
| Handle 泄露 | 撤销该句柄族，轮换底层秘密 |
| 重复 Close | 幂等成功或显式 already-closed |
| Handle 表不可用 | 高影响调用 fail closed |

## 十七、对象泄露与 Handle 泄露

两类泄露不同：

```text
Object Leak
运行时对象因引用或清理失败长期存活

Handle Leak
主体持有不再需要的访问能力和资源引用
```

天机阁除了内存与连接泄露，还会出现：

- Token 预算预留不释放；
- 沙箱和模型会话长期存活；
- Secret Lease 未撤销；
- Subscription 持续消费；
- 子 Agent 已结束但权限仍有效；
- 临时 Data View 变成事实上的长期旁路。

因此每个 Execution Container 结束时必须执行 Handle Census、Lease Drain 和强制清理。

## 十八、对象管理器不应拥有的权力

Runtime Object Manager：

- 不决定 Mission 目标；
- 不判断 Fact 真伪；
- 不批准 Product 内容；
- 不自主扩大 Handle 权限；
- 不因对象存在就允许发现；
- 不把名称空间层级解释成组织权力；
- 不用最后引用者身份决定历史删除；
- 不成为跨租户万能查询入口。

它只管理运行资源身份、类型、访问中介、引用、租约和回收。

## 十九、对现有骨架的影响

本课仍不要求新增一级面。

候选强化：

1. W1 的 `Capability Handle` 需要正式的类型、访问掩码、委托和生命周期模型；
2. `Runtime Object` 与 `Domain Object` 必须分离；
3. 名称发现、Open 授权与 Use 检查必须分离；
4. 子 Agent 默认零继承，通过收窄复制取得能力；
5. Execution Container 负责 Handle 和 Lease 的结构化回收；
6. 撤销必须通过 Handle 派生图传播；
7. Tool、Model、Data、Secret 和 Channel 都应通过受控对象访问，而不是 Prompt 注入凭据。

以上保持 `Seed`，后续需与 W3 安全令牌、W4 进程模型和 W5 Job Object 一起评审。

## 二十、本课结论

> 天机阁不应把资源直接交给 Agent；它应把“在特定使命、作用域、用途、时间和操作集合下访问某个受控对象的能力”交给 Agent。

最小心智模型：

```text
Object = 资源或能力本体
Name   = 可选发现路径
Handle = 主体局部、受权限约束的间接访问能力
Lease  = 单次操作期间的有效使用权
Audit  = 谁以什么依据如何使用
```

## 二十一、官方参考

- [Windows Kernel-Mode Object Manager](https://learn.microsoft.com/en-us/windows-hardware/drivers/kernel/windows-kernel-mode-object-manager)
- [Managing Kernel Objects](https://learn.microsoft.com/en-us/windows-hardware/drivers/kernel/managing-kernel-objects)
- [Object-Based](https://learn.microsoft.com/en-us/windows-hardware/drivers/kernel/object-based)
- [Object Handles](https://learn.microsoft.com/en-us/windows-hardware/drivers/kernel/object-handles)
- [Handle Inheritance](https://learn.microsoft.com/en-us/windows/win32/sysinfo/handle-inheritance)
- [DuplicateHandle](https://learn.microsoft.com/en-us/windows/win32/api/handleapi/nf-handleapi-duplicatehandle)
- [Object Interface](https://learn.microsoft.com/en-us/windows/win32/sysinfo/object-interface)

## 二十二、关联材料

- [Windows 学习总路线](./windows-as-agent-runtime-reference.md)
- [W1：用户态、内核态与系统调用边界](./windows-w01-user-kernel-and-system-call-boundary.md)
- [核心对象主干](../../04-domain-design/core-object-spine.md)
- [时间、版本、作用域与知识覆盖模型](../../04-domain-design/temporal-version-scope-knowledge-overlay-model.md)
- [授权与横切治理](../../03-current-architecture/cross-cutting/01-tenancy-identity-authorization.md)
