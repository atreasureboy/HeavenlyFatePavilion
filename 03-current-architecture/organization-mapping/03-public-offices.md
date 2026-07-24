# 公共官署映射

状态：Accepted

## 一、总览

公共官署是叙事机构。部分官署是单一技术能力的稳定别名，部分则是跨面能力组合。

| 官署 | 主要映射 | 技术形态 | Agent 使用 |
|---|---|---|---|
| 通政司 | 人机交互、跨面契约接入 | Gateway / Adapter / Message Service | 默认不用 |
| 文渊阁 | 数据底座原始资料能力 | Object / Content Store | 不用 |
| 国史馆 | 数据底座认识论与历史能力 | Data System / Query Service | 不用 |
| 钦天监 | 运行观测、持续监测、Signal 触发 | Scheduler / Monitor / Rule / Event Service | 异常解释可选 |
| 大理寺 | 智能治理的事实冲突裁定 | Conflict Engine + Case Workflow | 复杂裁定按需 |
| 都察院 / 御史台 | 治理监察与全域审计 | Audit / Quality / Compliance Service | 重大审计按需 |
| 翰林院 | 情报产品组织与表达 | Template / Renderer / Citation Service | 复杂写作按需 |
| 枢密院 | 平台控制的身份、策略和能力强制 | IAM / Policy Engine / Tool Gateway | 策略分析可选 |

## 二、通政司

### 能力映射

- 渠道接入与消息适配；
- 请求规范化；
- Contract Envelope 建立；
- 初始租户、主体和追踪上下文绑定；
- 消息路由和交付回执。

### 技术形态

Gateway、Channel Adapter、Webhook Service、Message Bus 接入和协议校验。

### 非职责

- 不解释复杂 Intent；
- 不创建 Mission；
- 不进行领域研判；
- 不因是“入口”而拥有所有任务。

## 三、文渊阁

### 能力映射

- Source、Document、Snapshot 保存；
- 内容寻址、哈希和不可变版本；
- 原件许可、获取时间和作用域；
- 原始资料归档、保留和恢复。

### 技术形态

对象存储、内容寻址系统、归档设施和元数据服务。

### 非职责

- 不判定 Evidence；
- 不生成 Fact；
- 不解释内容；
- 不等于全部数据底座。

## 四、国史馆

### 能力映射

- Evidence、Claim、Verification、Fact；
- Entity、Relationship、Event、Change；
- 双时态、版本、争议和历史；
- 权威查询、血缘和纠正传播。

### 技术形态

逻辑权威数据系统，可由事务、图、搜索、时序和事件系统组合实现。

### 非职责

- 不等于单一数据库产品；
- 不自行验证 Claim；
- 不自行形成 Assessment；
- 不覆盖文渊阁原始资料。

## 五、钦天监

钦天监是跨面叙事组合，必须拆开技术所有权。

```text
定时与持续任务触发          → 智能执行与运行面
来源健康和数据变化监测      → 数据认知面
运行、容量和失败监控        → 智能执行与运行面 / 基础设施
Signal 与异常触发           → 情报研判面
Alert 形成与投递            → 情报产品面 / 人机交互面
监测配置和阈值版本          → 平台控制面
```

钦天监本身不是第二个 Scheduler，也不拥有 Risk 的最终研判语义。其默认实现为 Monitor、Scheduler Trigger、Rule Engine 和 Event Service；复杂异常解释可启用 Agent。

## 六、大理寺

### 能力映射

- Claim 和 Fact 冲突案件形成；
- 多来源主张保留；
- 证据包和验证历史汇集；
- 初步规则裁定；
- 复杂案件审理；
- 裁定 Decision 和更正传播请求。

### 技术形态

Conflict Detection、Case Workflow、Rule Engine，加按需裁定 Agent 和人类专家。

### 与门下省的边界

门下省审核“该计划或结果是否应通过”；大理寺裁定“这些相互冲突的主张如何处理”。门下可以把事实争议移交大理寺，大理寺不能取代门下的整体治理审核。

## 七、都察院 / 御史台

### 能力映射

- Agent、Tool 和人类行为审计；
- 证据覆盖、幻觉、质量和偏差抽检；
- 契约与权限违规检测；
- 成本、延迟、失败和部门质量分析；
- 治理问责和改进建议；
- 向能力演进面提供 Evolution Evidence。

### 技术形态

Audit Service、Quality Monitor、Trace Analysis、Sampling Engine 和报表系统；重大或新型问题可启用审计 Agent。

### 非职责

- 不直接修改被审计对象；
- 不拥有平台权限强制；
- 不替代门下省逐任务审核；
- 不因监察而自动拥有所有租户数据的业务访问权。

## 八、翰林院

### 能力映射

- 报告、简报、档案和卷宗模板；
- 引用、证据编号和可信解释；
- 多渠道格式、语言和版式；
- 已批准内容的结构化组织；
- 产品草稿、版本和更正。

### 技术形态

Template Engine、Renderer、Citation Service、Format Converter，加按需写作 Agent。

### 与皇帝的边界

```text
翰林院
→ 把已批准内容组织成产品

皇帝
→ 结合当前仙人的目标、关系和上下文进行最终解释
```

二者都不能扩大门下省已经批准的结论范围。

## 九、枢密院

### 能力映射

- 身份、角色、授权和委托策略；
- Tool、Skill、模型和来源的能力许可；
- Policy Enforcement；
- Tool Gateway；
- 调用深度、风险和隔离策略；
- 高影响动作的审批条件；
- 策略版本和强制执行记录。

### 技术形态

IAM、Policy Engine、Capability Registry、Tool Gateway、Secret Broker 和安全控制服务。

### 与户部的边界

```text
户部
→ 计量解释、成本预测、资源建议、预算压力事件

枢密院 / 平台控制
→ 不可绕过的策略、配额和调用限制

皇帝 / 三省 / 仙人
→ 在权限范围内作调整或追加决定
```

枢密院可以用 Agent 分析新型风险或解释策略，但 Agent 不能成为权限强制点。

## 十、公共官署是否常驻 Agent

```text
默认不常驻：
通政司、文渊阁、国史馆、钦天监、翰林院、枢密院

按需 Agent：
大理寺复杂裁定
都察院重大审计
钦天监异常解释
翰林院复杂写作
枢密院新型策略分析
```

稳定机构身份不等于稳定占用高级模型。
