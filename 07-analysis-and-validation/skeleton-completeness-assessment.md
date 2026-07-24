# 天机阁整体架构骨架完整性审计

状态：Accepted

审计日期：2026-07-24

当前扩展轮次：`Skeleton v0.2`

## 一、结论

`Skeleton v0.2` 已完成 S2～S7 广度扫描及集中复核，可以称为“当前已知宏观骨架完成第二轮闭合”，但不能称为“整体骨架阶段已经完成”。

已经具备：

- 五个视觉分组；
- 十个一级逻辑面；
- 第一批二级能力域；
- 三条核心闭环；
- 七个横切维度；
- Agent / Tool 默认分工方向；
- 基础设施的共同承载位置；
- 第一批文字架构图。

这里的“第二轮闭合”表示主体生态、运行形态、全局流、时空规模、生存性和负空间加入后，十面仍能承载新增问题，并重新通过七道完整性门的骨架级检查。它仍不能证明整个宏观问题空间已经全部被发现。

## 二、骨架完善的判定标准

当前一轮骨架只有同时通过以下七道门，才能称为“本轮内部闭合”。这些门验证结构连接性，不单独证明宏观问题空间完备。

| 完整性门 | 判定问题 | 当前状态 |
|---|---|---|
| G1 顶层覆盖 | 所有核心问题能否归入稳定一级面，系统内外主体与边界是否明确 | 通过 |
| G2 二级覆盖 | 每个一级面是否拥有完整且类型一致的二级能力树 | 骨架级通过 |
| G3 接口闭合 | 每个面是否明确输入、输出、调用方、被调用方和契约 | 骨架级通过 |
| G4 对象闭合 | 核心对象是否拥有唯一语义、所有者和状态层级 | 通过，保留候选对象 |
| G5 状态闭合 | 任务、事实、研判、产品、告警和版本是否有生命周期 | 通过，保留细化项 |
| G6 横切闭合 | 多租户、权限、证据、预算、质量、审计如何落到每个面 | 通过 |
| G7 场景闭合 | 正常、退回、冲突、失败和演进场景能否贯穿全骨架 | 骨架级通过，待压力场景 |

## 三、当前最重要的结构缺口

### 3.1 系统上下文边界已经建立

已经确定：

- 仙人、租户、平台运营者、外部协作者和人类专家；
- 外部数据源、模型提供者、消息渠道和企业系统；
- 天机阁系统边界内外的责任；
- 共享平台、租户逻辑朝廷、皇帝稳定身份与按需运行实例的关系；
- 第一版全链路 Context Envelope。
- 需求/消费/行动责任主体的区分；
- 权利人、被研究主体和受影响第三方；
- 监管、司法、标准、供应链、同行和对抗生态；
- 人类目标、平台、租户、数据、事实、产品和外部行动的分域主权。

仍待后续骨架扫描确认联邦同行、外部行动、跨辖区和无明确权威场景；角色授权和 Court Profile Schema 属于后续细化。

### 3.2 核心对象主干已经建立

已经建立贯穿十面的对象链：

```text
Intent
→ Mission
→ Plan
→ Run / Task
→ Source / Document / Snapshot
→ Evidence / Claim / Fact
→ Entity / Relationship / Event / Change
→ Assessment / Forecast / Risk
→ Intelligence Product
→ Feedback / Evolution Evidence
```

同时已经确定六组对象、十面所有权、显式转化、纠正血缘、作用域和最小生命周期骨架。

仍需后续完成完整 Schema、详细状态转移、ID 规范和跨面接口协议，但对象类型和基本所有权不再阻塞骨架连接。

### 3.3 十面的输入输出契约已经建立

已经逐面定义：

- 接受什么输入；
- 产生什么输出；
- 拥有什么持久状态；
- 调用谁；
- 谁可以拒绝它；
- 失败后向哪里反馈。

同时建立 Command、Query、Result、Event、Decision、Feedback 六类交互、通用 Contract Envelope、八种统一响应结果和十条关键跨面契约。

仍需后续完成字段 Schema、授权矩阵、超时重试、版本兼容和服务级目标，但接口方向和失败路由不再缺失。

### 3.4 三省、六部与公共官署映射已经建立

已经建立“叙事机构→逻辑职责→能力→技术形态→实例与状态”的五层映射，并完成：

- 皇帝稳定身份、交互投影、治理 Agent 和运行实例的分离；
- 中书、尚书、门下与规划、统筹、审核能力的映射；
- 六部统一能力模板及逐部实例；
- 通政司、文渊阁、国史馆、钦天监、大理寺、都察院、翰林院和枢密院映射；
- 户部预算顾问与平台预算强制点的分离；
- 工部组织叙事与跨面技术所有权的分离；
- 钦天监跨面组合与 Scheduler 的分离。

后续仍需用场景和 RACI 验证协作细节，但组织名称不再阻塞技术归属。

### 3.5 十面二级能力树已经建立

已经按统一规则为十个一级面建立能力树，并明确：

- 节点只表达“系统能够做什么”；
- 组织、对象、组件、流程和质量属性不混入能力分类；
- 每个能力只有一个主要所有面；
- 每个能力标记 Agent-led、Mixed 或 Tool-led 默认形态；
- 五域十面的能力树已经分卷维护。

能力树仍需由官署映射和端到端场景验证，因此当前为“基本通过”，而不是最终完成。

### 3.6 六部能力模板已经完成第一版实例化

部级能力模板已经固定为：

```text
领域使命
领域本体
领域 Agent
领域 Tool / Skill
领域来源
领域数据产品
领域质量标准
与共享主干的扩展点
```

吏、户、礼、兵、刑、工已经分别映射领域使命、部级 Agent、Tool 倾向、共享主干扩展和非职责。吏部 Agent 任免职责标记为 `Reserved`，避免为了历史对称而强行启用。

### 3.7 状态机和异常骨架已经建立

已经建立：

- Mission / Plan / Run / Task / Agent Instance 的治理与执行生命周期；
- Claim / Fact / Entity / Relationship / Event / Change 的知识生命周期；
- Assessment / Forecast 的复核、校准和失效生命周期；
- Risk / Alert / Intelligence Product 的状态机；
- Capability Gap / Change Candidate / Capability Version 的演进状态机；
- 统一 Transition Event / Decision 规则、状态变更审计字段和终态保留原则；
- 治理、上下文、策略、预算、数据、认知、质量、运行、产品和演进十大异常类别；
- 封驳、冲突、超预算、无证据、模型退化、回滚和人工升级路由。

后续仍需为关键对象补齐机器可校验的转移 Schema、并发一致性规则、超时参数和逐场景补偿细节，但 G5 的骨架连接已经闭合。

### 3.8 横切治理骨架已经建立

已经把多租户、身份权限、证据血缘、预算资源、风险质量、审计追踪和数据生命周期七个维度落实为：

- `Authority—Context—Enforcement—Evidence—Failure—Evolution` 统一模型；
- 可跨契约解析的 Cross-cutting Context；
- 入口、数据访问、检索缓存、运行时、模型/Tool 网关、质量门、产品分发和生命周期执行等确定性强制点；
- 七维度—十面责任矩阵；
- INV-0001 至 INV-0008 第一批全局不变量；
- 各维度失败、降级、隔离、升级和人类接管路径；
- 预算建议与强制权、Agent 判断与系统强制、日志与审计、业务终态与数据处置等关键分离。

后续仍需深化角色授权矩阵、策略 Schema、跨租户联合空间协议、地域与合规规则及具体保留周期，但 G6 的骨架覆盖已经闭合。

### 3.9 文字图集仍缺关键视图

当前已有总览、闭环、十面关系、朝廷、系统上下文、核心对象、十面契约、能力总树、组织映射、状态异常、横切治理、端到端场景、主体生态、运行形态、七流分离、时空规模坐标、生存性、负空间、压力测试、循环权威和能力演进全域图。

能力演进作用全域图已经补齐。当前剩余的是把这些视图压缩为一张可一屏阅读的 v0.3 全景图，并执行一级稳定性复核。

### 3.10 端到端场景已经完成第一版闭合验证

已经建立九个标准场景：

- 公共信息快速查询；
- 租户私有企业尽调；
- 持续监测与风险告警；
- 冲突证据与事实裁定；
- 运行中预算耗尽；
- 长时任务权限撤销；
- 跨租户联合调查；
- 事实撤回与产品更正；
- 模型退化与能力回滚。

覆盖矩阵确认十面、七个横切维度、主要对象状态及 Returned、Disputed、Degraded、Cancelled、Correction 和 Rollback 等异常语义均有落点。G7 因此达到“基本通过”。

仍待后续专题覆盖安全攻击、基础设施灾难、区域故障、极端容量和更细粒度的人类协作场景。

### 3.11 Skeleton v0.2 扩展进度

`S2：主体、生态与主权边界` 至 `S7：负空间、反架构与未来替换` 已完成第一版扫描。

S3 建立十种运行形态；S4 将七条全局流分开；S5 建立四条正交扩展轴；S6 建立四类完整性、最低生存内核、六阶段防线和八类失效域；S7 补齐负职责、禁止权力组合、默认不可信边界与未来替换判断。详细对象、状态、协议和实现均未在这些扫描中展开。

集中回看已经完成：

```text
十面保留，不增、不减、不重划
→ 新增结构均归入职责、关系、坐标、约束、状态或承载
→ Skeleton v0.2 形成第二轮宏观闭合
```

下一步进入 `Skeleton v0.3` 的宏观压力场景与全局视图闭合，仍不进入局部专题深化。完整裁定见 [Skeleton v0.2 集中闭合复核](./skeleton-v0.2-closure-review.md)。

## 四、建议完成顺序

```text
第一步：系统上下文【完成】
    ↓
第二步：核心对象主干【完成第一版】
    ↓
第三步：十面输入输出契约【完成第一版】
    ↓
第四步：二级能力树补全【完成第一版】
    ↓
第五步：三省、六部、公共官署映射【完成第一版】
    ↓
第六步：核心状态机与异常路径【完成第一版】
    ↓
第七步：横切维度落位【完成第一版】
    ↓
第八步：端到端场景验证【完成第一版】
    ↓
当前已知骨架第一轮内部闭合【Skeleton v0.1】
    ↓
全域盲区扫描与骨架扩展【Skeleton v0.2】
    ↓
全局视图闭合与反复压力测试【Skeleton v0.3～v0.x】
    ↓
整体骨架基线【Skeleton v1.0】
```

这个顺序仍然是从整体到局部。它先补骨架的连接处，再深入任何单个部门或技术专题。

## 五、当前阶段命名

```text
已经完成：Skeleton v1.0 正式就绪评审与整体骨架基线
正在进行：从骨架分区选择专题，提高设计分辨率
继续禁止：专题设计静默改变一级职责、主权和全局关系
```

因此，当前可以宣布“天机阁 `Skeleton v1.0` 整体架构骨架正式成立”。v0.2 与 v0.3 连续两轮没有改变十个一级面，v1.0 又以必要性、充分性、互斥性、连接性和演进性完成终审。

`Skeleton v0.3` 第一轮八类宏观压力测试已经通过：十面、七流、四轴和七个横切维度保持稳定，发现的相关失效、派生义务传播和跨能力代际可回放均可回写现有结构。

循环权威与单点认知复核也已完成：识别 8 类危险回路和 10 类认知集中点，通过权威来源、独立挑战、分歧保留、现实锚点、确定性强制及按风险启用反证断环。未发现新一级面。

能力演进全域图现已完成：九类演进对象、五级变更半径、三层嵌套循环和采用权分离均能由现有十面承载。能力演进面继续保持独立，但不取得他面采用权。

一屏全景与一级稳定性复核已经完成：v0.3 七道门在宏观/骨架级通过，十面稳定性保持。随后 v1.0 正式评审完成十面必要性 10/10 和八项门槛 8/8 终审，允许已有稳定归属但尚未细化的 Schema、协议、授权矩阵、部署和容量设计带入基线。

## 六、关联文档

- [整体架构骨架](../03-current-architecture/overall-architecture-skeleton.md)
- [系统上下文与边界](../03-current-architecture/system-context.md)
- [核心对象主干](../04-domain-design/core-object-spine.md)
- [十面输入输出契约](../03-current-architecture/plane-contracts.md)
- [十面二级能力树](../03-current-architecture/capability-trees/README.md)
- [三省六部与公共官署映射](../03-current-architecture/organization-mapping/README.md)
- [核心状态机与异常路由](../08-reference-models/state-machines/README.md)
- [状态机与异常路径总图](../03-current-architecture/text-diagrams/TXT-0010-state-machines-and-exceptions.md)
- [七维横切治理骨架](../03-current-architecture/cross-cutting/README.md)
- [七维横切治理总图](../03-current-architecture/text-diagrams/TXT-0011-cross-cutting-governance.md)
- [第一批端到端场景](../05-scenarios-and-flows/README.md)
- [场景覆盖矩阵](../05-scenarios-and-flows/scenario-coverage-matrix.md)
- [端到端正常与失败场景总图](../03-current-architecture/text-diagrams/TXT-0012-end-to-end-scenarios.md)
- [空间、时间与规模骨架](../03-current-architecture/spatiotemporal-and-scale-skeleton.md)
- [空间、时间与规模坐标图](../03-current-architecture/text-diagrams/TXT-0016-spatiotemporal-and-scale.md)
- [非功能与生存性骨架](../03-current-architecture/nonfunctional-and-survivability-skeleton.md)
- [非功能与生存性总图](../03-current-architecture/text-diagrams/TXT-0017-nonfunctional-and-survivability.md)
- [负空间、反架构与未来替换骨架](../03-current-architecture/negative-space-and-future-replacement.md)
- [负空间与未来替换图](../03-current-architecture/text-diagrams/TXT-0018-negative-space-and-future-replacement.md)
- [Skeleton v0.2 集中闭合复核](./skeleton-v0.2-closure-review.md)
- [Skeleton v0.2 闭合总图](../03-current-architecture/text-diagrams/TXT-0019-skeleton-v0.2-closure.md)
- [Skeleton v0.3 第一轮宏观压力测试](./skeleton-v0.3-macro-stress-test.md)
- [Skeleton v0.3 宏观压力测试图](../03-current-architecture/text-diagrams/TXT-0020-skeleton-v0.3-macro-stress.md)
- [循环权威与单点认知复核](./authority-cycles-and-cognitive-concentration-review.md)
- [循环权威与单点认知图](../03-current-architecture/text-diagrams/TXT-0021-authority-cycles-and-cognitive-concentration.md)
- [能力演进作用全域骨架](../03-current-architecture/evolution-across-entire-architecture.md)
- [能力演进作用全域图](../03-current-architecture/text-diagrams/TXT-0022-evolution-across-entire-architecture.md)
- [Skeleton v0.3 一屏全景图](../03-current-architecture/text-diagrams/TXT-0023-skeleton-v0.3-one-screen-panorama.md)
- [Skeleton v0.3 闭合与一级稳定性复核](./skeleton-v0.3-closure-and-stability-review.md)
- [Skeleton v1.0 正式就绪评审](./skeleton-v1.0-readiness-review.md)
- [Skeleton v1.0 基线声明](../03-current-architecture/skeleton-v1.0-baseline.md)
- [文字架构图集](../03-current-architecture/text-diagrams/README.md)
- [阶段 1：整体架构骨架访谈](../02-architecture-iterations/v0001-concept-baseline/phase-01-overall-skeleton-interview.md)
- [阶段 1：整体骨架扩展与完善计划](../02-architecture-iterations/v0001-concept-baseline/phase-01-skeleton-expansion-plan.md)
- [主体生态与分域主权骨架](../03-current-architecture/ecosystem-and-sovereignty.md)
- [主体生态与主权边界图](../03-current-architecture/text-diagrams/TXT-0013-ecosystem-and-sovereignty.md)
- [全局运行形态骨架](../03-current-architecture/operating-modes.md)
- [全局运行形态与转换图](../03-current-architecture/text-diagrams/TXT-0014-operating-modes.md)
- [七条全局流骨架](../03-current-architecture/global-flows.md)
- [七条全局流分离图](../03-current-architecture/text-diagrams/TXT-0015-global-flow-separation.md)
