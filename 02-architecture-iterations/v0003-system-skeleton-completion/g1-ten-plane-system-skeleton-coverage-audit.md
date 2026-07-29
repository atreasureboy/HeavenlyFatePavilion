# G1：十面系统骨架覆盖审计

状态：Draft

审计日期：2026-07-29

审计对象：十面能力树、平面契约、组织映射、情报语义基线、Windows W1—W6 候选材料

## 一、审计问题

天机阁已经拥有完整的十面能力树，但“能力完整”是否等于“系统骨架完整”？

答案是否定的：

```text
能力树
回答：系统必须能够做什么

系统骨架
回答：由哪些稳定系统承担、各自拥有什么状态和权力、
      怎样协作、失败时怎样退回和恢复

组件与部署
回答：用多少服务、数据库、队列和节点实现
```

因此：

```text
Capability ≠ Logical System ≠ Deployable Component
```

现有 `TXT-0008` 明确声明叶子节点只是能力，不是组织或组件。这使它成为 G1 的可靠输入，但不能被误当成 G1 已经完成的证据。

## 二、G1 系统骨架的最低判据

一个一级面只有同时具备以下六项，才可判为 G1 完成：

| 判据 | 必须回答的问题 |
|---|---|
| G1-S：稳定系统族 | 面内由哪些具有独立职责或生命周期的逻辑系统构成？ |
| G1-R：责任与非责任 | 每个系统拥有什么、明确不拥有什么？ |
| G1-A：权力与状态 | 谁能提议、决定、执行、强制、保管和撤销？权威状态在哪里？ |
| G1-C：输入输出连接 | 系统之间及跨面之间通过什么类型的契约连接？ |
| G1-F：失败与恢复 | 拒绝、退回、超时、取消、降级、恢复分别去哪里？ |
| G1-X：横切投影 | 租户、权限、证据、预算、质量、审计和生命周期如何约束？ |

补充交付判据：

- 有一张面内系统文字图；
- 每个系统都能回挂十面能力树；
- 系统边界不依赖具体技术选型；
- 不为追求对称制造没有独立责任的伪系统。

## 三、全局审计结论

```text
十面一级归属：       10 / 10 已完成
十面二级能力分类：   10 / 10 已完成
十面一级输入输出：   10 / 10 已完成
十面系统族划分：      0 / 10 正式完成
十面 G1 六项闭合：    0 / 10 正式完成
```

这里的 `0 / 10` 不表示此前没有成果，而表示当前还没有任何一个面以“逻辑系统骨架”为单位完成六项闭合并正式采纳。

现有材料的真实状态是：

```text
能力位置已经完整
对象语义部分高度成熟
一级跨面契约已经存在
组织权力已有宏观映射
但能力还没有系统化聚类成可运行、可治理、可恢复的二级逻辑系统
```

这正是 G1 阶段需要填补的结构空白。

## 四、成熟度标记

本审计使用相对成熟度，不使用虚假的精确分数：

| 标记 | 含义 |
|---|---|
| `Strong` | 已有多份相互印证的正式材料，可直接提炼系统骨架 |
| `Partial` | 已有能力与边界，但缺少系统化分组或连接 |
| `Seeded` | 已有重要候选思路，尚未进入正式架构 |
| `Gap` | 尚无足以支撑系统骨架的材料 |

## 五、十面覆盖矩阵

| 一级面 | 能力树 | 对象/状态 | 一级契约 | 权力边界 | 系统族 | 失败恢复 | 当前判断 |
|---|---|---|---|---|---|---|---|
| 人机交互 | Strong | Partial | Strong | Partial | Gap | Partial | 能力齐全，交互系统边界未成形 |
| 情报产品 | Strong | Strong | Strong | Partial | Partial | Strong | 产品语义成熟，生成/发布/分发系统未分开 |
| 数据认知 | Strong | Strong | Strong | Strong | Partial | Partial | 主干成熟，处理系统与案件系统未成形 |
| 情报研判 | Strong | Strong | Strong | Strong | Partial | Partial | 对象成熟，研判系统族与复盘闭环未成形 |
| 智能治理 | Strong | Strong | Strong | Strong | Partial | Strong | 权力最成熟，治理系统与 Agent 角色仍混写 |
| 领域能力 | Strong | Partial | Strong | Partial | Gap | Partial | 有能力模板，无部级系统骨架 |
| 智能执行与运行 | Strong | Partial | Strong | Strong | Seeded | Seeded | W1—W6 候选丰富，尚未汇总采纳 |
| 数据底座 | Strong | Strong | Strong | Strong | Partial | Strong | 语义层次成熟，逻辑保管/供应系统未分开 |
| 平台控制 | Strong | Partial | Strong | Strong | Partial | Partial | 目录和策略齐全，控制系统族尚未闭合 |
| 能力演进 | Strong | Strong | Strong | Strong | Partial | Strong | 闭环成熟，观察/实验/采用/发布系统未分开 |

## 六、逐面审计

### 6.1 人机交互面

已有：

- 皇帝关系与人格投影；
- 会话、多模态、偏好、通知、反馈和人类接管能力；
- 与治理、产品、平台控制的一级契约；
- 渠道和会话权威状态边界。

缺失的系统级问题：

- 皇帝交互中枢、会话连续性、渠道接入、身份入口、通知注意力、人类决定台是否分别成系统；
- 皇帝“统一人格”与租户 Court Profile、会话状态、治理本体如何分离；
- 多设备、多会话、多仙人同时操作时谁拥有当前交互焦点；
- 身份解析失败、渠道断连、消息重复和人类接管冲突怎样恢复。

候选系统族方向：

```text
Emperor Interaction Core
Session & Conversation System
Channel Gateway
Human Authority Console
Notification & Attention System
Interaction Preference / Projection System
```

成熟度：`Partial → G1 未完成`。

### 6.2 情报产品面

已有：

- Product、Edition、Release、Rendering、Distribution、Delivery 的严格语义分离；
- Alert、Correction、Withdrawal、Retraction 与影响传播；
- 产品与底层对象版本、证据和接收者的关系；
- 发布、撤回和分发失败路径。

缺失的系统级问题：

- 产品编排、渲染、发布、分发、订阅、告警和纠正是否独立；
- 哪个系统拥有 Product Definition、Edition、Release 和 Delivery 的不同状态；
- 即时回答与正式产品是否共用发布门；
- 多租户品牌、模板和内容权威如何组合而不污染产品。

候选系统族方向：

```text
Product Composition System
Edition & Rendering System
Publication Governance Gateway
Distribution & Delivery System
Subscription & Alert System
Correction & Recipient Impact System
```

成熟度：`Strong foundation → G1 未完成`。

### 6.3 数据认知面

已有：

- 掌数、联结、察变三条完整能力主干；
- Source、Document、Snapshot、Evidence、Claim、Entity、Relationship、Event、Diff、Change 语义；
- 同名消歧、来源变化、证据保全和事实争议场景；
- 与领域、运行、数据底座和治理的一级契约。

缺失的系统级问题：

- 来源认知、采集处理、证据工程、实体解析、事件关系、变化认知、事实验证是否形成独立系统；
- 哪些是流水处理，哪些是长期 Governance Case；
- 大规模确定性处理与高争议 Agent 判断怎样在系统边界交接；
- 迟到数据、解析升级和实体拆分怎样触发系统级重算。

候选系统族方向：

```text
Source Intelligence System
Acquisition & Content Processing System
Evidence Engineering System
Entity Resolution System
Relationship & Event Construction System
Diff & Change Cognition System
Verification & Fact Candidate System
```

成熟度：`Strong foundation → G1 未完成`。

### 6.4 情报研判面

已有：

- Signal、Assessment、Scenario、Forecast、Risk、Recommendation 完整对象主干；
- 反证、不确定性、Dissent、校准、干预和反馈语义；
- 推势、卜算、警险三类能力；
- 研判不得冒充 Fact、建议不得自动形成行动的权力断点。

缺失的系统级问题：

- Signal 管理、态势研判、情景推演、预测、风险、建议和校准是否分别成系统；
- 哪些系统保存长期模型与历史，哪些只产生一次 Assessment；
- 研判之间如何组合，又如何保留独立反证；
- Forecast 解析、风险解除和建议效果反馈由谁驱动。

候选系统族方向：

```text
Signal & Trigger System
Assessment System
Scenario & Forecast System
Risk Intelligence System
Recommendation Option System
Calibration & Outcome Learning System
```

成熟度：`Strong foundation → G1 未完成`。

### 6.5 智能治理面

已有：

- 皇帝、中书、尚书、门下的权力分离；
- Intent、Mission、Plan、Decision、Governance Case 等语义；
- 封驳、冲突、升级、人类决定、监察和问责路径；
- Agent 与确定性 Transition Guard 的边界。

缺失的系统级问题：

- 皇帝协调、议程、规划、执行统筹、审核、裁定和监察分别由什么逻辑系统承载；
- “三省 Agent”与“治理系统”如何分离，避免 Agent 自己保存权威决定；
- 一次请求何时创建 Mission、Governance Case 或 Human Decision Request；
- 多个三省实例故障或意见冲突时由什么系统维持治理连续性。

候选系统族方向：

```text
Emperor Governance Core
Intent & Mission Governance System
Planning System
Execution Orchestration Governance System
Review & Rebuttal System
Governance Case & Adjudication System
Human Decision & Escalation System
Oversight & Accountability System
```

成熟度：`Strong foundation → G1 未完成`。

### 6.6 领域能力面

已有：

- 六部领域范围；
- 统一领域能力模板；
- 本体、来源、规则、模型、Agent、Tool、评测和跨部协作能力；
- 六部不得复制共享平台的边界。

缺失最明显：

- “一部”是能力包、治理边界、逻辑系统还是三者组合；
- 部级 Agent、领域知识、领域 Tool 和评测由哪些系统承载；
- 六部共用能力与本部专有能力如何注册、继承和隔离；
- 跨部联合调查如何建立临时能力组合而不复制数据和 Runtime；
- 工部的领域技术知识与公共基础设施职责如何持续防混。

候选系统族方向：

```text
Domain Capability Registry
Domain Knowledge & Ontology System
Domain Source / Evidence Policy System
Domain Agent & Tool Pack
Domain Quality & Evaluation System
Cross-domain Collaboration System
```

每一部是上述模板的领域实例，不先复制六套系统。

成熟度：`Partial → G1 未完成`。

### 6.7 智能执行与运行面

已有：

- Run、Task、Agent、Tool、Context、Memory、Workspace、调度和恢复能力；
- 平台期望状态与运行实际状态边界；
- W1—W6 已提出 Runtime Kernel、Object Manager、Security Context、Execution Container、IPC 等候选；
- Agent 不能替代确定性运行内核。

缺失的正式系统骨架：

- 哪些 Windows 候选应合并、删除或改名；
- Runtime Kernel 内部最小可信系统族；
- Scheduler、Execution Container、Agent Host、Tool Gateway、Context System、Channel System、Checkpoint/Recovery 如何连接；
- Run、Task、Attempt、Instance、Turn、Invocation 的正式对象关系；
- 跨节点运行时怎样保持同一容器、授权、计量和终止语义。

候选系统族方向：

```text
Runtime Control Kernel
Run / Task State System
Scheduler & Admission System
Execution Container Manager
Agent / Worker Execution Host
Tool & Model Invocation Gateway
Context / Workspace System
IPC & Continuation System
Checkpoint / Recovery System
Runtime Telemetry & Accounting System
```

成熟度：`Seeded，候选最丰富但正式化缺口最大`。

### 6.8 数据底座面

已有：

- 六层数据语义；
- 对象身份、不可变 Revision、双时态、作用域、血缘和纠正；
- 公共底座、租户覆盖和 Mission Workspace；
- 索引、供应、一致性、保留和恢复能力。

缺失的系统级问题：

- 原始资料保管、语义对象登记、世界状态、索引供应、血缘影响、作用域投影和生命周期是否独立；
- 数据底座如何只保管接受结果而不取得语义接受权；
- 多种存储形态如何被逻辑系统隐藏，避免技术库成为领域边界；
- 历史重建、影响传播、租户 Overlay 查询和删除限制如何协作。

候选系统族方向：

```text
Source Material Repository
Semantic Object Registry
World State & Temporal System
Knowledge Overlay System
Index & Retrieval Supply System
Lineage & Impact System
Retention / Archive / Recovery System
```

成熟度：`Strong foundation → G1 未完成`。

### 6.9 平台控制面

已有：

- Tenant、Court Profile、Identity、Policy、Agent、Tool、Skill、Model、Workflow、Source 和 Budget 目录；
- 期望状态与实际状态分离；
- 版本、灰度、发布、回滚和协调能力；
- 确定性权威控制状态原则。

缺失的系统级问题：

- 租户控制、身份授权、能力目录、模型治理、来源连接、预算政策、配置发布和 Reconciliation 如何分系统；
- Catalog 的注册权、采用权、启用权和调用权如何分离；
- 租户专有配置、平台默认和 Mission 临时覆盖怎样合成；
- 控制面失效时运行面继续多久、使用哪个最后已知良好版本。

候选系统族方向：

```text
Tenant & Court Control System
Identity / Policy Authority
Capability Catalog System
Model & Provider Control System
Source / Connector Control System
Budget & Quota Policy System
Configuration / Release System
Desired-State Reconciler
```

成熟度：`Partial → G1 未完成`。

### 6.10 能力演进面

已有：

- 观察、缺口、候选、实验、评测、治理采用、发布、观察与回滚闭环；
- Evolution Evidence、Capability Gap、Change Candidate 等对象；
- 半径、自治等级和人类采用权；
- 演进作用于全部十面和架构本身。

缺失的系统级问题：

- 观察、实验、评测、候选登记、采用治理、发布协调和回滚分析是否独立；
- 演进系统如何读取生产证据但不能直接改写生产配置；
- 人工架构演进与系统自主候选如何共用同一卷宗；
- 评测污染、指标投机和演进系统自身失效如何被独立审计。

候选系统族方向：

```text
Evolution Observation System
Capability Gap & Candidate Registry
Experiment / Sandbox System
Evaluation & Red-team System
Adoption Governance Bridge
Evolution Release Coordinator
Post-release Attribution & Rollback System
Architecture Corpus & Decision System
```

成熟度：`Strong loop → G1 未完成`。

## 七、发现的五个全局结构缺口

### 缺口 1：能力节点尚未聚合成逻辑系统

目前树的叶子粒度混合：

- 用户可感知能力；
- 领域处理阶段；
- 管理能力；
- 运行机制；
- 数据保管能力。

下一步不能简单把每个能力节点变成一个系统。应按“独立责任、权威状态、生命周期、伸缩或故障边界”聚类。

### 缺口 2：组织角色与技术系统仍有混写风险

三省六部回答谁负责和谁制衡；系统骨架回答状态由何种逻辑系统维持。中书 Agent 可以使用 Planning System，但 Planning System 不是中书 Agent 本身。

```text
Role / Office
≠ Agent Instance
≠ Logical System
≠ Deployable Service
```

### 缺口 3：对象成熟度与系统成熟度严重不均

情报语义已经深入到 Revision、Authority、Lineage 和 Transition；交互、领域、运行、控制等面的系统边界仍较薄。继续深化对象字段会扩大失衡，应先横向补系统结构。

### 缺口 4：运行面候选深度正在领先其他九面

Windows W1—W6 很有价值，但若直接继续 W7—W10，运行面会进一步单点变深。本阶段应暂停连续下钻，先把所有十面的 G1 系统树提升到同一分辨率，再回到 G2 运行机制汇总。

### 缺口 5：现有一级契约不能替代系统间连接

`Plane Contract Envelope` 只证明面与面能够协作。一个面拆成多个逻辑系统后，还需确定：

- 哪个系统接收入口；
- 哪个系统拥有状态；
- 哪个系统可以拒绝；
- 哪个系统发出完成事实；
- 故障由谁接管；
- 跨面调用最终落到哪个系统。

## 八、推荐的 G1 完善顺序

不按“选一个面钻到底”工作，而按四轮横向扫描：

```text
G1-A 系统族轮
十面分别确定 4—8 个稳定逻辑系统
        │
        ▼
G1-B 权威轮
逐系统补责任、非责任、权威状态与决定权
        │
        ▼
G1-C 连接轮
把七条全局流和 Plane Contract 落到具体系统
        │
        ▼
G1-D 生存轮
补拒绝、超时、取消、降级、接管、恢复和横切投影
```

每轮都覆盖十面后才进入下一轮，避免局部远深于全局。

## 九、下一步

下一步执行 `G1-A 系统族轮`：

1. 以本审计中的候选系统族为输入；
2. 检查每个候选是否具有独立责任、状态或生命周期；
3. 合并只是能力步骤的伪系统；
4. 形成“十面二级逻辑系统总树”候选文字图；
5. 暂不写接口字段、算法、存储和部署；
6. 由用户审阅整体树后，再进入 G1-B 权威轮。

## 十、对当前阶段的影响

```text
G0：保持封板
G1：完成覆盖审计，系统族轮尚未完成
G2：Windows W1—W6 暂存为运行面输入
G3：等待 G1 系统树形成后再开始
```

本审计没有采纳新的当前架构系统，只建立 v0003 的缺口基线与候选方向。

## 十一、关联材料

- [v0003：整体系统骨架完善](./README.md)
- [整体架构骨架完善范围与阶段边界](../../03-current-architecture/skeleton-completion-scope.md)
- [十面二级能力树](../../03-current-architecture/capability-trees/README.md)
- [十面输入输出契约](../../03-current-architecture/plane-contracts.md)
- [组织—技术映射](../../03-current-architecture/organization-mapping/README.md)
- [Windows 作为运行体系参考](../../01-idea-lab/analogies/windows-as-agent-runtime-reference.md)
