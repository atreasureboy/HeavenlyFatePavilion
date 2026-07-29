# Windows 作为天机阁智能运行体系的参考模型

状态：Seed

记录日期：2026-07-29

来源：用户提出“学习 Windows”，结合 Microsoft 官方 Windows 架构资料

## 一、问题与观察

天机阁已经完成外层骨架，下一步不能因为“多 Agent”听起来新颖，就只研究现有 Agent Framework。Windows 长期处理了大量结构同构的问题：

- 有限可信的执行主体如何运行；
- 主体如何取得而不是自称拥有权限；
- 多个任务如何共享资源又相互隔离；
- 怎样创建、调度、暂停、终止和回收执行实例；
- 怎样访问文件、设备、事件、锁和其他对象；
- 怎样限制一组进程的资源并整体停止；
- 怎样跨进程通信而不共享全部内存和权力；
- 系统怎样观察失败、保留状态并恢复控制。

因此，Windows 应作为成熟的执行与控制系统研究，而不能把“进程 = Agent”停留在表面比喻。

## 二、第一条核心认识

Windows 最值得学习的不是进程调度本身，而是以下组合：

```text
有类型的对象
+ 不透明句柄
+ 访问令牌与安全描述符
+ 用户态 / 内核态边界
+ 受控系统调用
+ 进程 / 线程 / Job 生命周期
+ 配额、计量、通知和统一终止
+ 可观测事件与持久配置
```

Microsoft 的对象管理器把文件、设备、线程、进程、事件、互斥量、信号量、注册表键、Job、Section 和 Access Token 等表示为有类型内核对象，并负责命名空间、访问权、资源归属和生命周期。调用方通常通过不透明 Handle 访问对象，而不是直接取得对象本身。

Job Object 可以把一组进程作为单元管理，设置资源限制、汇总资源计量、接收限制通知，并终止整组进程；嵌套 Job 还能对进程树施加层级约束。

对天机阁最重要的启发是：

> 子 Agent 不是自由活动的“聪明人”，而应是携带派生权力、通过能力句柄访问对象、被 Mission / Run 容器约束、可计量且可整体撤销的执行实例。

## 三、学习路线

### W1：系统边界与执行模式

- 用户态、内核态；
- 执行体、内核、子系统、服务的区别；
- 系统调用为何是受控边界。

迁移问题：哪些能力属于 Agent 可建议的“用户态”，哪些必须由确定性运行内核强制？

研究进度：第一轮完成，见 [W1：用户态、内核态与系统调用边界](./windows-w01-user-kernel-and-system-call-boundary.md)。

### W2：对象管理器、对象类型与命名空间

- Object、Object Type、Object Header；
- 命名对象、Handle Table 与引用计数；
- 创建、打开、复制、继承和关闭。

迁移问题：Tool、Data、Policy、Mission、Run、Task、Agent Instance 和 Channel 是否都应成为可寻址受控对象？

研究进度：第一轮完成，见 [W2：Object Manager、Handle 与对象生命周期](./windows-w02-object-manager-handles-and-lifetime.md)。

### W3：安全模型

- Security Principal、Access Token；
- Security Descriptor、ACL、Privilege；
- Impersonation、Integrity Level、Restricted Token；
- 权限检查与审计。

迁移问题：Agent 的能力来自 Mandate / Token / Handle，还是来自 Prompt 中“你可以做什么”的文字？

研究进度：第一轮完成，见 [W3：Access Token、ACL、Privilege 与 Impersonation](./windows-w03-access-token-acl-privilege-and-impersonation.md)。

### W4：进程、线程与执行上下文

- Process 作为资源和地址空间容器；
- Thread 作为实际调度单位；
- Parent / Child 不自动等于安全委托；
- 创建、挂起、退出和异常。

迁移问题：Agent Role、Agent Instance、Reasoning Turn、Worker 和 Task 应如何分离？

研究进度：第一轮完成，见 [W4：Process、Thread 与 Agent 执行上下文](./windows-w04-process-thread-and-agent-execution-context.md)。

### W5：Job Object 与资源治理

- 进程组和嵌套 Job；
- CPU、内存、进程数、优先级和时间限制；
- Accounting、Notification 和 Kill-on-close；
- Breakaway 的风险和显式策略。

迁移问题：Mission / Run 是否应拥有类似 Job 的执行容器，统一约束 Token、模型费用、并发、子 Agent 树和终止传播？

### W6：IPC 与同步

- Pipe、RPC、ALPC、Shared Memory、Socket；
- Event、Mutex、Semaphore、Wait；
- 消息传递与共享状态的取舍；
- 身份、序列化、超时和断连。

迁移问题：Agent 间交流应通过版本化消息与对象引用，还是任意共享长上下文？

### W7：I/O、驱动与外部能力

- I/O Manager、Device Object、Driver Stack；
- 请求生命周期；
- 驱动位于高权力域带来的风险。

迁移问题：Connector 和高权力 Tool 是否类似驱动，怎样防止一个插件击穿整个天机阁？

### W8：服务、会话与长期状态

- Service Control Manager；
- 服务启动、停止、恢复与依赖；
- Session、用户登录和隔离；
- 持久配置与运行实例分离。

迁移问题：皇帝稳定身份、Court Profile、长期监测服务和按需 Agent 进程如何分离？

### W9：调度、资源与性能

- 优先级、公平性和亲和性；
- 前台响应与后台吞吐；
- 饥饿、优先级反转和背压；
- 系统计量与容量反馈。

迁移问题：户部的“哭穷”、任务优先级和模型资源调度如何成为确定性机制？

### W10：故障、诊断与恢复

- Exception、Crash、Hang、Deadlock；
- Event Log、ETW、Dump、Performance Counter；
- Restart、Fail-fast、隔离和恢复；
- 更新、回滚和兼容性。

迁移问题：怎样证明 Agent 做过什么、为何失败、影响了哪些对象，以及如何可控恢复？

## 四、初始映射

| Windows 概念 | 天机阁候选映射 | 暂不等同 |
|---|---|---|
| Human user / administrator | 仙人 / 有权人类 | 皇帝 |
| Logon session / user profile | Tenant Session / Court Profile | 整个租户 |
| Process | Agent / Worker 执行实例 | Agent Role |
| Thread | 一次可调度推理或执行流 | 完整 Task |
| Job Object | Mission / Run Execution Container | Mission 业务语义 |
| Access Token | 派生授权上下文 / Capability Token | Prompt |
| Handle | 受控能力或对象引用 | 原始数据库凭据 |
| Object Manager | 平台对象与能力访问内核 | 数据底座全部语义 |
| System call | Tool Gateway / Kernel API | 任意 Tool 调用文本 |
| Kernel mode | 确定性强制与可信运行内核 | 高智能 Agent |
| User mode | 受限 Agent / Tool 执行空间 | 低能力 |
| IPC | Contract Envelope / Event / Channel | 共享全部上下文 |
| Service | 长期平台或监测能力 | 永久常驻大模型 |
| Driver | Connector / 高权力 Tool Adapter | 六部 |
| Registry / configuration | 平台期望状态与版本化配置 | 权威情报数据库 |
| ETW / Event Log / Dump | Trace / Audit / Replay / Failure Artifact | 模型解释文本 |

## 五、不能照搬

1. Windows 管理可执行代码和机器资源，Agent 还会产生开放式语义、幻觉和不可重复判断；
2. 进程隔离主要处理内存与资源，Agent 还需要知识污染、提示注入和认识论隔离；
3. Windows 权限模型不能回答 Fact 接受、Assessment 审核和 Product 发布的领域权威；
4. 天机阁还要面对多租户、跨辖域和证据驱动的知识治理；
5. `Process = Agent`、`Thread = sub-agent` 只能是局部映射，不能决定正式领域模型；
6. Windows 的成熟不代表所有历史兼容设计都适合未来 Agent 系统。

## 六、架构价值

- 为智能执行与运行面提供比普通 Agent 编排框架更深的参考；
- 把 Agent 权限从 Prompt 提示提升为 Token、Handle 和确定性检查；
- 把 Mission / Run 提升为可限额、可计量、可撤销的执行容器；
- 把 Tool 调用改造成受控系统调用，而不是暴露底层凭据；
- 为 Agent 树、资源治理、终止传播、故障隔离和诊断建立成熟类比。

## 七、风险与反证

- 机械改名 Windows 术语会制造伪架构；
- 过度强调控制可能压制开放式调查；
- 单机内核类比可能低估分布式与跨租户治理；
- 安全令牌不能替代语义权威、证据审核和人类责任；
- 只研究成功机制、不研究历史包袱会得到理想化 Windows。

## 八、待验证问题

1. Mission、Plan、Run、Task 与 Job / Process / Thread 的最佳映射是什么？
2. Capability Handle 应引用 Tool、对象、动作，还是三者组合？
3. Agent 派生子 Agent 时，哪些权限默认继承、收窄或禁止转授？
4. 如何表达类似 Job Breakaway 的例外而不形成越权通道？
5. Agent Runtime 的“内核态”如何保持小、确定、可验证？
6. 哪些 Windows 机制只适合实现层，不应进入天机阁领域语言？

## 九、受影响区域

- 智能执行与运行面；
- 平台控制面与智能治理面；
- 多租户、身份、授权、预算和审计；
- Agent / Tool / Service 边界；
- 非功能、生存性和能力演进。

本研究不重新打开外层骨架；若后续发现一级职责无法容纳某机制，必须先提供跨场景证据。

## 十、第一课结论

```text
不要先问：
“要启动几个 Agent？”

先问：
“谁创建它、它携带什么派生权力、能拿到哪些句柄、
归属哪个执行容器、消耗如何计量、失败如何隔离、
谁可以终止它、退出后哪些状态必须留下？”
```

这是 Windows 能给天机阁的第一层架构教育。

## 十一、官方参考

- [Windows Kernel-Mode Object Manager](https://learn.microsoft.com/en-us/windows-hardware/drivers/kernel/windows-kernel-mode-object-manager)
- [Object Handles](https://learn.microsoft.com/en-us/windows-hardware/drivers/kernel/object-handles)
- [Job Objects](https://learn.microsoft.com/en-us/windows/win32/procthread/job-objects)
- [Job Object Limits](https://learn.microsoft.com/en-us/windows/win32/api/winnt/ns-winnt-jobobject_basic_limit_information)

## 十二、关联材料

- [项目 README：Windows 核心启发](../../README.md)
- [Agent / Tool 职责分配基础骨架](../../02-architecture-iterations/v0001-concept-baseline/agent-tool-allocation-baseline.md)
- [治理与执行能力树](../../03-current-architecture/capability-trees/03-governance-and-execution.md)
- [负空间与未来替换](../../03-current-architecture/negative-space-and-future-replacement.md)
