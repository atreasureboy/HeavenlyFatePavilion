# Windows W3：Access Token、ACL、Privilege 与 Impersonation

状态：Seed

记录日期：2026-07-29

来源：Microsoft 官方 Windows Access Control 与 Impersonation 资料

## 一、本课问题

W2 已回答“怎样通过 Handle 间接访问对象”，但仍缺少授权判定：

```text
谁在请求？
它代表谁？
对象允许谁做什么？
请求的是哪种操作？
是否存在系统级特权？
服务代用户执行时使用谁的身份？
授权结果如何审计？
```

Windows 将这些问题拆为 Access Token、Security Descriptor、ACL / ACE、Access Mask、Privilege 和 Impersonation。天机阁需要学习这种分离，而不是复制 Windows 的 SID 格式。

## 二、Windows 访问控制基本模型

Windows 的访问控制有两个基本输入：

```text
Access Token
描述进程或线程的安全上下文
包含用户 SID、组 SID、登录会话、Privilege、限制等

Security Descriptor
保护一个 securable object
包含 Owner、DACL、SACL 等
```

请求方指定 Desired Access Mask。系统使用调用线程的有效 Token 与对象 Security Descriptor 执行 Access Check：

```text
Effective Token
+ Desired Access
+ Object Security Descriptor
+ Object Type Semantics
+ applicable privileges / policy
→ Granted Access 或 Denied
```

DACL 中的 ACE 允许或拒绝特定 trustee 的操作；SACL 决定哪些访问尝试需要审计。对象所有权、访问授权与审计要求是不同维度。

## 三、Token 描述主体，不描述对象全部权限

Windows Access Token 可以包含：

- 用户 SID；
- 组 SID；
- Logon SID；
- Privilege；
- Token 类型；
- 默认 Owner、Primary Group 与默认 DACL；
- Restricted SID；
- Token Source 和统计信息。

Token 不是“全系统权限清单”。同一个 Token 对不同对象的结果可能不同，因为每个对象拥有自己的 Security Descriptor。

天机阁对应原则：

```text
Actor Context
≠ Object Policy
≠ Requested Operation
≠ Granted Capability Handle
```

只有完成访问检查后，才为精确对象与操作签发 Handle。

## 四、候选天机阁主体令牌

暂称 `Actor Security Context`，避免过早与模型 Token 混淆：

```text
ActorSecurityContext
├── token_id / token_type
├── tenant_id / court_id / collaboration_space
├── subject_id
├── actor_instance_id
├── human_authority_root
├── authenticated_session
├── roles / groups
├── enabled_privileges
├── deny_only_roles
├── restricting_scopes
├── mission_id / mandate_revision
├── delegation_chain
├── purpose / jurisdiction
├── risk_ceiling
├── issued_at / expires_at
├── revocation_generation
├── authentication_strength
└── token_source / issuer
```

这里的 Token 描述一次执行主体的安全上下文，不是 Agent Memory，也不是把租户所有权限复制进 Prompt。

## 五、Primary Token 与 Impersonation Token

Windows 进程拥有 Primary Token；线程可以临时拥有 Impersonation Token。正在模拟客户端的服务线程同时存在：

- 服务进程自身的 Primary Token；
- 当前线程代表客户端使用的 Impersonation Token。

天机阁候选映射：

```text
Primary Actor Context
Agent / Worker / Broker 自身长期或实例级身份

On-Behalf-Of Context
本次请求所代表的用户、Mission 或上游主体
```

执行时必须明确选择：

```text
Use service identity
Use caller identity
Use intersection(service, caller, mandate)
Use explicitly delegated capability
```

默认推荐使用最小交集或显式 Capability Handle，而不是让能力代理随意使用自身更高权限。

## 六、Confused Deputy

典型风险：

```text
低权限 Agent
→ 请求高权限 Tool Broker 读取某对象
→ Broker 使用自己的平台权限访问
→ 未验证请求者是否有权
→ 低权限主体借 Broker 越权
```

防线：

1. Broker 必须知道调用者身份和委托链；
2. 访问检查针对请求者、Mission、Purpose 和目标对象；
3. Broker 自身权限只用于实现动作，不自动转授给调用者；
4. 结果按调用者作用域过滤；
5. 下游调用继续传播 On-Behalf-Of Context；
6. 审计同时记录 caller、broker 与 effective authority。

## 七、Impersonation 等级

Windows 定义 Anonymous、Identification、Impersonation、Delegation 等等级。它们表达服务可以知道客户端多少信息，以及能在多大范围代表客户端行动。

天机阁候选映射：

| 等级 | 能力代理可以做什么 | 典型用途 |
|---|---|---|
| Anonymous | 不知道真实主体，只处理公开、无状态请求 | 公共健康检查 |
| Identify | 识别主体并做访问判断，但不能代其调用下游 | 数据发现、策略预检 |
| Impersonate | 在当前信任域内代表主体访问资源 | 同租户受控查询 |
| Delegate | 跨服务、区域或外部系统继续代表主体 | 严格审批的跨域调用 |

`Delegate` 风险最高，不能因调用链变长而自动获得。每跨一个信任边界都要重新判断：

- 是否允许继续委托；
- 下游是否可信；
- Purpose 是否仍一致；
- 凭据能否进一步转授；
- 是否需要换成服务自己的、范围更窄的 Capability。

## 八、Privilege 不是普通对象权限

Windows Privilege 允许执行某些系统级操作，甚至在特定条件下绕过普通对象 DACL，例如备份或调试能力。

天机阁应把以下候选视为高危 Privilege，而不是普通角色权限：

- 创建或签发 Actor Security Context；
- 更换 Agent Instance 的 Primary Context；
- 绕过普通读取策略执行合规保全；
- 强制终止 Execution Container；
- 进入紧急恢复模式；
- 查看隔离区审计证据；
- 执行跨租户平台级调查；
- 修改策略根、信任根或审计配置；
- 创建不可被普通回收的系统对象。

Privilege 规则：

```text
assigned ≠ enabled
enabled ≠ should be used
used → mandatory audit
```

高危 Privilege 应默认关闭，按操作短时启用，使用后立即恢复，并记录理由、批准者和影响。

## 九、Restricted Token 的启发

Windows 可以从既有 Token 创建 Restricted Token：

- 移除 Privilege；
- 把某些 SID 变成 deny-only；
- 增加 restricting SID；
- 访问必须同时通过普通身份集合和限制集合。

天机阁可以用同样思想创建 Agent 子上下文：

```text
Parent Context
→ remove privileges
→ disable irrelevant roles
→ add mission / task restricting scope
→ reduce purpose / jurisdiction / risk ceiling
→ reduce delegation depth
→ shorten expiry
→ Child Restricted Context
```

候选不变量：

```text
derive(child, parent) 只能收窄
不能通过“复制 Token”增加新权力
恢复被移除权力必须回到原始授权主体重新签发
```

## 十、对象 Security Descriptor 候选

```text
ObjectSecurityDescriptor
├── object_id / object_type
├── owner_authority
├── policy_revision
├── allow_entries[]
├── deny_entries[]
├── audit_entries[]
├── inherited_entries[]
├── purpose_constraints
├── jurisdiction_constraints
├── authority_domain_constraints
├── retention / legal_hold_constraints
└── policy_source / decision_reference
```

每条 Entry：

```text
AccessEntry
├── trustee selector
├── allow / deny / audit
├── operation mask
├── conditions
├── scope
├── effective interval
├── inheritance rule
└── obligation
```

这只是逻辑模型，不等于采用 Windows DACL 的顺序算法或 SDDL。

## 十一、访问检查管线

```text
1. Authentication
   验证主体和会话

2. Effective Context
   选择 Primary / On-Behalf-Of / Restricted Context

3. Request Normalization
   解析目标对象类型、精确操作和参数

4. Context Integrity
   校验 Tenant / Mission / Mandate / Purpose / Jurisdiction

5. Object Policy
   读取目标对象 Security Descriptor 与 Policy Revision

6. Explicit Deny
   检查禁止主体、作用域、用途和状态

7. Allow Evaluation
   检查角色、组、属性、关系和 Capability

8. Privilege Evaluation
   只在操作声明允许时评估系统级 Privilege

9. Cross-cutting Guards
   预算、风险、证据、质量、生命周期与撤销

10. Decision
   Granted / Denied / ApprovalRequired / StepUpRequired

11. Handle Issuance
   仅签发已授予 access mask 的 Handle

12. Audit
   记录请求、有效身份、策略版本、结果和义务
```

## 十二、权限与语义权威必须分离

这是天机阁比普通操作系统更复杂的地方。

```text
拥有 Fact Store WRITE
≠ 有权接受 Fact

拥有 Product Repository WRITE
≠ 有权批准 Product Release

拥有 Model Endpoint INVOKE
≠ 有权改变 Mission

拥有 Audit READ
≠ 有权查看所有租户内容
```

Windows 风格 Access Check 只解决资源访问；Fact 接受、Assessment 审核、Product 发布与外部 Action 还必须检查对应 Authority Domain、Decision 与 Transition Envelope。

最终允许：

```text
Resource Access Granted
∩ Semantic Authority Valid
∩ State Transition Valid
∩ Cross-cutting Guards Pass
= Authoritative Operation Allowed
```

## 十三、DACL 与 SACL 的启发

天机阁应把授权政策和审计政策分开：

```text
Authorization Policy
谁能做什么

Audit Policy
哪些成功、失败、特权使用、跨域访问必须记录或告警
```

拒绝请求也可能是重要审计事件，尤其是：

- 跨租户对象探测；
- 已撤销 Handle 重用；
- 多次请求高危 Privilege；
- 试图以 Broker 身份绕过 Caller；
- 试图把 Identify 提升为 Delegate；
- 尝试写入不属于自身 Authority Domain 的状态。

## 十四、缓存授权结果

Windows Authz 支持缓存访问检查结果，但天机阁缓存必须绑定：

```text
actor_context_hash
+ object_security_descriptor_revision
+ policy_revision
+ mandate_revision
+ operation
+ purpose / jurisdiction
+ revocation_generation
+ maximum_staleness
```

以下情况立即失效：

- 租户成员或角色变化；
- Mandate 撤销；
- 对象 Policy 更新；
- 来源许可或法律留置变化；
- 风险等级上升；
- Handle 派生链撤销；
- 从读操作升级到写、发布或外部副作用。

高影响操作不能仅依赖过期缓存。

## 十五、多租户与联合空间

安全上下文必须能同时表达：

```text
Platform Identity
Tenant Membership
Court Role
Mission Mandate
Collaboration Space Membership
Purpose / Jurisdiction
```

加入联合空间不会合并租户 Token。应签发新的空间限制 Context 与 Handle：

```text
Tenant A Context
∩ Collaboration Decision
∩ Space C Policy
→ A-in-C Restricted Context
```

联合空间结束后，该 Context 和派生 Handle 撤销；原租户身份不变。

## 十六、服务身份使用规则

能力代理需要自身身份来：

- 连接后端；
- 读取平台配置；
- 写入自身健康与审计；
- 使用实现所需的系统能力。

但返回给调用者的数据与副作用必须以 caller / mandate 为授权依据。

候选规则：

```text
Broker Primary Context
用于运行 Broker

Caller Effective Context
用于判断调用者能否请求

Downstream Execution Capability
用于完成一项精确、收窄、可审计的动作
```

三者不得合并成一个万能服务账号。

## 十七、失败与异常

| 情况 | 结果 |
|---|---|
| 无 Token 或 Token 无法验证 | Rejected |
| Token 已撤销或过期 | Rejected + cancel descendants |
| 对象显式 Deny | Denied，不进入 Allow 覆盖 |
| 缺少普通对象权限 | Denied |
| 缺少语义 Authority | GovernanceRejected |
| 需要高危 Privilege | ApprovalRequired / StepUp |
| Impersonation 超级别 | Denied + security audit |
| Policy 版本不可用 | 高影响 fail closed |
| SACL / Audit 承诺不可满足 | 高影响 Stop / Degraded |
| 下游不能接受 On-Behalf-Of | 改用收窄 Capability 或拒绝 |

## 十八、对现有骨架的影响

仍不需要新增一级面。

候选强化：

1. W2 Handle 的发行必须来源于明确 Access Decision；
2. Actor Context、Object Security Descriptor、Capability Handle 与 Semantic Authority 分离；
3. Agent 子上下文默认使用 Restricted Token 思想，只能收窄；
4. Broker 必须防止 Confused Deputy；
5. Impersonation / Delegation 应成为跨服务权限传播的显式等级；
6. 高危 Privilege 与普通对象权限分开治理；
7. 授权与审计策略分离但共享同一访问事件；
8. 权限缓存必须绑定策略版本与撤销代数。

以上保持 `Seed`，等待 W4～W6 与正式运行、安全专题验证。

## 十九、本课结论

> 权力不是 Agent 的属性，也不是角色名称的附赠品；权力来自可验证主体上下文、对象政策、明确操作、有效 Mandate、语义 Authority 和横切守卫在某一时刻的共同裁定。

最小授权公式：

```text
Who are you?
∩ On whose behalf?
∩ Which object?
∩ Which operation?
∩ Under which Mission / Purpose / Jurisdiction?
∩ Does object policy allow it?
∩ Do semantic authority and transition rules allow it?
∩ Can audit and obligations be satisfied?
= Decision
```

## 二十、官方参考

- [Access Tokens](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-tokens)
- [Parts of the Access Control Model](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-control-components)
- [Access Rights and Access Masks](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-rights-and-access-masks)
- [Restricted Tokens](https://learn.microsoft.com/en-us/windows/win32/secauthz/restricted-tokens)
- [Impersonation Tokens](https://learn.microsoft.com/en-us/windows/win32/secauthz/impersonation-tokens)
- [Client Impersonation](https://learn.microsoft.com/en-us/windows/win32/secauthz/client-impersonation)
- [Impersonation Levels](https://learn.microsoft.com/en-us/windows/win32/com/impersonation-levels)

## 二十一、关联材料

- [Windows 学习总路线](./windows-as-agent-runtime-reference.md)
- [W1：用户态与内核边界](./windows-w01-user-kernel-and-system-call-boundary.md)
- [W2：Object Manager 与 Handle](./windows-w02-object-manager-handles-and-lifetime.md)
- [租户、身份与授权](../../03-current-architecture/cross-cutting/01-tenancy-identity-authorization.md)
- [ADR-0014：派生且分域的内部权力](../../06-architecture-decisions/adrs/ADR-0014-derived-domain-scoped-authority.md)
