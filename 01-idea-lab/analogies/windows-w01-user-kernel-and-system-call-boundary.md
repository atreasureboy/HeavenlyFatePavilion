# Windows W1：用户态、内核态与系统调用边界

状态：Seed

记录日期：2026-07-29

来源：Microsoft 官方 Windows 文档与天机阁架构映射研究

## 一、本课问题

Windows 为什么把普通应用放在用户态，把少量高权力机制放在内核态？这条边界对 Agent 系统意味着什么？

本课不讨论具体 CPU 指令和 NT 系统调用编号，而讨论三个稳定架构问题：权力边界、故障半径与受控跨界协议。

## 二、Windows 的基本事实

Windows 让应用在用户态进程中运行。每个用户态进程拥有私有虚拟地址空间和私有 Handle Table，不能直接访问为操作系统保留的地址；一个普通应用崩溃通常不会破坏其他进程或操作系统。

内核态代码共享系统地址空间，能够访问受保护资源。该权力带来更大的故障半径：错误的内核驱动可能破坏系统或其他驱动的数据，崩溃也可能导致整个系统崩溃。

Windows 也证明“驱动能力”不必天然进入内核。WDF 同时提供 KMDF 和 UMDF；Microsoft 对多数不需要内核专有能力的驱动建议优先选择 UMDF。需要受保护能力时，也可以把功能拆为用户态部分和更小的内核态部分。

因此：

```text
内核态 ≠ 更高级
内核态 = 更大的直接权力 + 更大的共同故障半径

用户态 ≠ 能力低
用户态 = 默认隔离 + 通过受控边界请求能力
```

## 三、对天机阁最重要的纠偏

不能把“最聪明的模型”称为内核。模型越聪明，仍然可能误解目标、受提示注入、产生幻觉、被上下文污染或输出不可重复判断。

天机阁的内核态应按可信强制能力定义，而不是按智商定义：

```text
皇帝 / 三省 / 部级 Agent / 调查 Agent
→ 智能用户态

身份验证 / 授权检查 / 句柄解析 / 配额保留
状态转换守卫 / 沙箱启动 / 撤销与停止 / 审计落账
→ 确定性内核态
```

高级 Agent 可以建议内核采取动作，不能因为推理能力更强就进入内核信任域。

## 四、候选三域模型

Windows 的二态边界迁移到分布式 Agent 平台时，可以先形成三个逻辑信任域：

```text
┌──────────────────────── 智能用户域 ────────────────────────┐
│ 皇帝 / 三省 / 六部 Agent / 调查 Agent / 写作 Agent          │
│ 规划、解释、比较、反证、综合、提出候选                       │
│ 默认：无原始凭据、无直接权威写入、可失败、可替换             │
└────────────────────────────┬───────────────────────────────┘
                             │ Capability Request
                             ▼
┌──────────────────────── 能力代理域 ────────────────────────┐
│ Tool Gateway / Data Broker / Model Gateway / Connector Host │
│ 把开放式请求收敛为有类型动作，隔离供应商和外部副作用          │
│ 默认：只获得完成单次操作所需的最小权限                        │
└────────────────────────────┬───────────────────────────────┘
                             │ Kernel Contract
                             ▼
┌──────────────────────── 确定性内核域 ──────────────────────┐
│ Identity / Mandate / Policy / Handle / Budget / Transition  │
│ Sandbox / Lease / Audit / Revocation / Stop / Recovery      │
│ 默认：代码小、状态明确、可测试、无开放式自主目标              │
└────────────────────────────────────────────────────────────┘
```

三域是逻辑信任边界，不预先规定三个进程、三个服务或三套集群。

## 五、智能用户域与内核域

智能用户域负责：

- Intent 解释、Mission 和 Plan 候选；
- 领域语义理解、Claim 与实体匹配候选；
- Assessment、Scenario、Forecast、Risk 候选；
- 反证、审核意见、产品草稿与演进候选。

确定性内核域负责：

1. 身份与 tenant、actor、delegation chain 绑定；
2. Mandate 有效性和授权半径检查；
3. Capability Handle 与精确对象、动作解析；
4. 权限、用途、辖域、保留和来源政策强制；
5. Token、费用、时间、并发和调用预算预留；
6. expected revision、状态、Authority Domain 和幂等校验；
7. 沙箱、Worker、网络和秘密暴露范围选择；
8. 租约、暂停、撤销与停止传播；
9. 请求、裁定、结果、副作用和因果审计；
10. 超时、执行树终止、结果隔离和补偿触发。

内核执行“某主体是否可以实施该转换”，不替主体决定“这个 Fact 是否真实”或“这个 Assessment 是否合理”。

## 六、什么绝不能塞进内核

- 皇帝人格和对话风格；
- 开放式规划与领域本体；
- 搜索策略和报告写作；
- Claim、Fact、Assessment 的内容判断；
- 产品叙事和模型偏好；
- 自主演进目标生成；
- 为了方便而加入的任意业务例外。

这些能力很重要，但变化快、非确定、难以证明。将它们塞进内核会扩大共同故障半径，并降低安全、恢复和演进能力。

## 七、天机阁的“系统调用”

普通 LLM Tool Call 只表示模型输出了工具调用意图，还不是系统调用。

```text
Agent 形成 Capability Request
→ 用户域 SDK 结构化与本地预检
→ Kernel Gateway 验证 Contract Envelope
→ 绑定 Actor + Tenant + Mission + Run + Mandate
→ 解析 Capability Handle
→ 检查 Object + Action + Scope + Purpose
→ 校验参数、版本、预算、风险、幂等与因果
→ 预留资源并签发短期执行租约
→ 能力代理或沙箱执行
→ 捕获 Typed Result / Side Effect / Failure
→ 权威审计落账与资源结算
→ 返回结果或显式异常
```

正式系统调用的成立条件：

```text
Tool Intent
+ Valid Identity
+ Valid Mandate
+ Valid Capability Handle
+ Policy Decision
+ Budget Reservation
+ Transition / Side-effect Guard
+ Audit Commitment
= Governed Capability Invocation
```

Prompt 中写着“你可以调用搜索”不构成能力；真正能力来自运行时发放且可撤销的 Handle。

## 八、系统调用请求骨架

```text
KernelCall
├── call_id / idempotency_key
├── tenant_scope / authority_domain
├── actor_instance / delegation_chain
├── mission_id / run_id / task_id
├── mandate_revision
├── capability_handle
├── target_object + expected_revision
├── operation + typed_arguments
├── purpose / jurisdiction / time_context
├── budget_reservation
├── risk_tier / evidence_requirement
├── trace_id / correlation_id / causation_id
└── requested_deadline / cancellation_token
```

这只是研究骨架，不是已经采纳的协议 Schema。

## 九、跨界检查

| 检查点 | 防止的问题 |
|---|---|
| 类型和大小验证 | 模糊参数、注入、超大上下文 |
| 身份与委托链 | 冒充皇帝、伪造上级授权 |
| Handle 类型与访问掩码 | 用读权限执行写入或发布 |
| 作用域与用途 | 跨租户读取、目的外使用 |
| Revision 与状态前置条件 | 旧版本并发覆盖 |
| 预算预留 | 先执行后哭穷、无限递归 |
| 风险与审核门 | 高影响动作绕过门下 |
| 幂等与副作用声明 | 重试造成重复外部动作 |
| 审计承诺 | 成功但无法追责 |
| 撤销与截止时间 | 已失效权限继续运行 |

## 十、失败半径

| 失败 | 默认影响范围 | 处理 |
|---|---|---|
| 单次推理失败 | 当前 reasoning turn | 丢弃候选、重试或换模型 |
| Agent 实例失控 | 当前 Task / sandbox | 撤销 Handle、终止实例 |
| Tool Adapter 崩溃 | 当前能力代理 | 熔断、隔离、替代实现 |
| Run 失控 | 当前执行容器和子树 | 类 Job 整体终止与补偿 |
| Guard 不可信 | 相关高影响写路径 | fail closed、停止写入 |
| 审计或身份根失效 | 最低生存内核 | 停止高影响运行并由人类接管 |

用户态失败应局部恢复；内核态失败必须更少、更显式，并触发更大范围的保守停止。

## 十一、最小内核原则

```text
Mechanism in kernel
Policy version outside but deterministically enforced
Open-ended intelligence in user domain
```

继续验证：

- 哪些策略可在外部 Policy Engine 计算、由内核只验证签名结果；
- 数据底座写入是否通过同一 Kernel Gateway；
- Fact 接受和 Product 发布怎样分别绑定 Authority Domain；
- 高吞吐读路径怎样安全缓存授权；
- 分布式“内核”怎样避免成为不可用单点。

## 十二、对现有骨架的影响

本课没有发现需要新增一级面：

- 智能用户域落在治理、领域、认知、研判与产品面；
- 能力代理域落在运行、平台控制和工部工具体系；
- 确定性内核域由平台控制、智能运行、数据底座和横切强制点共同形成；
- 它不是第十一个一级面，也不是皇帝的新身份。

候选强化：

1. 在 Agent / Tool 之外增加 Agent / Broker / Kernel Mechanism 信任域视角；
2. Tool Call 与 Governed Capability Invocation 分离；
3. Agent Instance 默认是用户态执行主体；
4. 最小内核成为运行专题首要约束；
5. 内核权力越高，允许的开放式智能越少。

以上保持 `Seed`，等待后续课程和运行专题综合验证。

## 十三、反例

```text
错误：
皇帝是最聪明的 Agent
→ 皇帝就是内核
→ 皇帝可以直接访问所有数据和工具

正确：
皇帝是统一智能中枢和治理主体
→ 仍属于受约束的智能用户域
→ 每次能力访问都经过 Mandate、Handle、Policy 和 Audit
```

另一个错误是把所有强制机制放进中央“超级内核服务”。逻辑内核可以由多个确定性执行点共同实现，只要权威、协议和一致性边界明确。

## 十四、本课结论

> 天机阁的根本边界不是“Agent 与非 Agent”，而是“可以提出开放式判断的有限可信主体”与“能够产生权威状态和现实副作用的确定性强制机制”之间的边界。

Windows 教给我们的第一条运行原则：

```text
默认把新能力放在用户域
→ 只有确实需要直接权力、确定性强制或最低生存能力时
→ 才把最小机制放入内核信任域
```

## 十五、官方参考

- [User mode and kernel mode](https://learn.microsoft.com/en-us/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)
- [What is a driver?](https://learn.microsoft.com/en-us/windows-hardware/drivers/gettingstarted/what-is-a-driver-)
- [Windows Driver Frameworks](https://learn.microsoft.com/en-us/windows-hardware/drivers/wdf/)
- [UMDF frequently asked questions](https://learn.microsoft.com/en-us/windows-hardware/drivers/wdf/user-mode-driver-framework-frequently-asked-questions)

## 十六、关联材料

- [Windows 学习总路线](./windows-as-agent-runtime-reference.md)
- [治理与执行能力树](../../03-current-architecture/capability-trees/03-governance-and-execution.md)
- [七维横切治理](../../03-current-architecture/cross-cutting/README.md)
- [最低生存内核](../../03-current-architecture/nonfunctional-and-survivability-skeleton.md)
- [ADR-0013：确定性强制点](../../06-architecture-decisions/adrs/ADR-0013-mandatory-cross-cutting-context-and-enforcement.md)
