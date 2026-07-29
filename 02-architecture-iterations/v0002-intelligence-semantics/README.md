# v0002：情报语义与核心对象体系

状态：Draft

启动日期：2026-07-24

## 一、为什么先做这个专题

`Skeleton v1.0` 已经回答“系统有哪些稳定位置”，下一步要回答“这些位置之间传递的东西究竟是什么”。

首个深化专题不选择数据库、Agent Runtime 或六部内部组织，而选择情报语义与核心对象体系，原因是：

```text
证据语义
→ 决定事实如何成立
→ 决定实体、关系、事件和变化如何关联
→ 决定趋势、预测和风险如何与事实分离
→ 决定产品如何引用、撤回和纠正
→ 决定协议、状态机、数据架构和审核机制如何设计
```

如果对象语义不稳定，越早设计存储、协议和 Agent 协作，返工范围越大。

## 二、本轮目标

把现有[核心对象主干](../../04-domain-design/core-object-spine.md)从“骨架级对象列表”提升为可支撑后续专题的正式语义基线：

1. 建立统一语言和对象分类体系；
2. 划清资料、证据、主张、事实、推断和产品；
3. 设计实体、身份、别名和消歧语义；
4. 设计关系、事件、状态和变化的区别；
5. 定义时间、版本、有效性和知识时间；
6. 定义公共知识、租户覆盖和任务工作空间；
7. 定义对象权威、生命周期、纠正和血缘；
8. 用正常、冲突、撤回、迟到和重解释场景验证。

## 三、本轮明确不做

- 不选择关系库、图库、向量库、湖仓或搜索引擎；
- 不定义物理表、索引、分区和部署；
- 不设计完整 API、消息协议和序列化格式；
- 不展开 Agent Runtime、模型路由和调度算法；
- 不把六部各自的领域本体一次性设计完；
- 不用“知识图谱”一个词吞并所有对象语义。

## 四、设计顺序

```text
S1 统一语言与对象分类【候选层完成】
→ S2 来源、资料、快照与证据【候选层完成】
→ S3 主张、争议、事实与裁定【候选层完成】
→ S4 实体、身份、别名与消歧【候选层完成】
→ S5 关系、事件、状态、Diff 与 Change【候选层完成】
→ S6 Signal、Assessment、Forecast、Risk、Recommendation【候选层完成】
→ S7 产品、告警、反馈与影响纠正【候选层完成】
→ S8 双时态、版本、作用域与知识覆盖【候选层完成】
→ S9 对象权威、生命周期、血缘与不变量【候选层完成】
→ S10 场景压力与语义基线评审
```

每一步只提高语义分辨率；发现一级职责问题时，才按 v1.0 变更纪律重新打开骨架。

## 五、完成门槛

- 同一个术语在全项目只保留一种正式含义；
- 每类核心对象都有身份、权威、作用域、时间和版本语义；
- 每次对象转化创建新对象或新版本，不靠原地改名；
- “观察到”“有人主张”“系统接受”“系统推断”“系统建议”可机器区分；
- 同名消歧、冲突事实、事实撤回、迟到资料和历史重解释均有语义落点；
- 公共事实与租户私有判断既可复用又不相互污染；
- 任何情报产品都能回溯到资料、证据、裁定、推断和生成版本；
- 后续数据、协议、审核和运行专题可以直接引用本轮对象基线。

## 六、本轮工作文件

- [输入](./inputs.md)
- [候选变化](./proposed-changes.md)
- [影响分析](./impact-analysis.md)
- [开放问题](./open-questions.md)
- [迭代结果](./outcome.md)
- [情报语义深化计划](../../04-domain-design/intelligence-semantics-deepening-plan.md)
- [统一语言与对象分类法](../../08-reference-models/unified-language-and-object-taxonomy.md)
- [Source、Document、Snapshot 与 Evidence 语义模型](../../04-domain-design/source-document-snapshot-evidence-model.md)
- [SCN-0010：来源变化与证据保全](../../05-scenarios-and-flows/SCN-0010-source-mutation-and-evidence-preservation.md)
- [Claim、争议、Fact 与裁定语义模型](../../04-domain-design/claim-fact-and-adjudication-model.md)
- [SCN-0011：多权威域事实并存](../../05-scenarios-and-flows/SCN-0011-multiple-authority-domain-facts.md)
- [Entity、身份、别名与消歧语义模型](../../04-domain-design/entity-identity-and-resolution-model.md)
- [SCN-0012：同名实体误合并与拆分纠正](../../05-scenarios-and-flows/SCN-0012-entity-merge-split-correction.md)
- [Relationship、Event、State、Diff 与 Change 语义模型](../../04-domain-design/world-state-event-and-change-model.md)
- [SCN-0013：四类变化分离](../../05-scenarios-and-flows/SCN-0013-four-kinds-of-change-separation.md)
- [Signal、Assessment、Forecast、Scenario、Risk 与 Recommendation 语义模型](../../04-domain-design/assessment-forecast-risk-recommendation-model.md)
- [SCN-0014：预测干预与风险重校准](../../05-scenarios-and-flows/SCN-0014-forecast-intervention-and-recalibration.md)
- [Intelligence Product、Alert、Feedback 与影响纠正语义模型](../../04-domain-design/intelligence-product-alert-feedback-correction-model.md)
- [SCN-0015：部分事实撤回、影响裁剪与接收者纠正](../../05-scenarios-and-flows/SCN-0015-partial-retraction-impact-and-recipient-correction.md)
- [时间、版本、作用域与知识覆盖语义模型](../../04-domain-design/temporal-version-scope-knowledge-overlay-model.md)
- [SCN-0016：迟到资料、差异知识时间与历史重建](../../05-scenarios-and-flows/SCN-0016-late-evidence-divergent-knowledge-time-and-reconstruction.md)
- [对象权威、生命周期、血缘与不变量统一模型](../../04-domain-design/object-authority-lifecycle-lineage-invariant-model.md)
- [SCN-0017：越权事实晋升、血缘断裂与自审发布阻断](../../05-scenarios-and-flows/SCN-0017-unauthorized-fact-promotion-lineage-gap-and-self-publication.md)

## 七、当前进度

S1 已完成候选分类：

- 建立叙事语言、领域语言和实现语言三层边界；
- 区分对象类型、生命周期状态、角色/视图、过程记录和对象关系；
- 将情报主干整理为八个对象家族；
- 建立权威、作用域、时间、版本四个正交问题；
- 建立新增核心对象准入测试；
- 提出候选判断：`Disputed Claim` 是状态/视图，Diff 不自动等于 Change。

这些结论保持 `Proposed`，将在 S2～S10 中接受逐类对象和异常场景验证。

S2 已完成候选语义模型：

- 分离 Source、Document、Snapshot、Evidence 四种身份；
- Source 主体、系统、端点不再默认压成一个 URL；
- Document 身份不由 URL、标题或内容哈希单独决定；
- Snapshot 表示实际取得的不可变内容，并显式表达捕获完整性；
- Acquisition Record 与 Snapshot 分离；
- Evidence 必须具有 Anchor、上下文和派生变换链；
- 共享物理资料不等于共享租户可见性、用途或判断；
- Content Diff、Extraction Diff 与 World Change 得到可执行的语义分界。

S2 未改变 Skeleton v1.0。候选模型已通过来源静默修改、半截抓取、解析升级、许可撤销和多租户复用场景的第一轮验证。

S3 已完成候选语义模型：

- Claimant、Extractor、Verifier、Acceptor 四种主体不再混写；
- Claim 保留原始表达，并通过规范化 Proposition 参与比较；
- Verification Record 与 Verification Package 分离；
- Fact 被定义为指定 Acceptance Context 内的接受记录，而不是全局真理；
- Public、Tenant、Joint、Mission Working 和 External Authority Fact 可以并存；
- 真冲突前必须先排除实体、时间、定义、角色、辖域和作用域错配；
- Dispute Case 被提出为独立治理案件，而非新的世界事实；
- 门下、大理寺、领域能力、平台强制、数据底座和人类裁定权得到区分。

S3 未改变 Skeleton v1.0。候选模型已通过公共登记、官网展示和租户内部决议并存场景的第一轮验证。

S4 已完成候选语义模型：

- 现实指称对象、Entity Record、Source-local Record 和 Mention 分层；
- Mention 被提出为材料到 Entity 的一等认识对象；
- Name、Alias、Identifier、Identity Claim 不再混写；
- Identifier 必须绑定 issuer、namespace 和有效时间；
- 身份解析被拆为候选生成、硬约束、验证、决定和可逆链接；
- `same_as`、规范视图、数据整合和现实并购严格分离；
- 误合并采用非破坏性 Split，沿血缘重评 Claim、Fact、关系、事件、研判和产品；
- 公共 Entity 与 Tenant Overlay 分离，技术可识别不产生关联权限。

S4 未改变 Skeleton v1.0。候选模型已通过同名同集团人物误合并、租户私有反证、拆分和产品纠正场景的第一轮验证。

S5 已完成候选语义模型：

- Relationship 表示区间内持续联系，Event 表示有时间边界的发生项；
- Event 可以建立、修改或终止 Relationship，但二者分别保存；
- Lifecycle / World / Execution / Desired State 严格区分；
- World State 默认是带事实域和双时间上下文的 View；
- Diff 是版本比较记录，不自动成为现实变化；
- Content Diff、Extraction Diff、Knowledge Revision、World Change 四类变化分离；
- Change 保持一等对象，具有前后态、时间、Evidence、重要性和消费者；
- World Change 必须经过 Claim / Fact 接受链；
- 时序相邻不自动形成因果关系。

S5 未改变 Skeleton v1.0。候选模型已通过同一监测轮次中页面、解析、认识和现实四类变化并发场景的第一轮验证。

S6 已完成候选语义模型：

- Signal、Assessment、Scenario、Forecast、Risk、Recommendation 六类对象分离；
- Assessment 必须保留 Fact、假设、反证、替代解释、不确定性和 Dissent；
- Scenario 被提出为条件化可能世界的一等研判对象；
- Forecast 必须具有目标、结果空间、信息截点、时间窗、解析规则和评分；
- 不确定性被拆为数据、身份、认识、随机、模型、情景、测量、分歧等维度；
- Intelligence Risk 与 Mission Risk Tier / Operational Risk 分离；
- Recommendation 到 Decision、Authorization、Action 之间保留权力断点；
- 干预、自我实现、解析与校准进入因果血缘；
- 认知独立性按风险启用，不按 Agent 数量表演。

S6 未改变 Skeleton v1.0。候选模型已通过预警促成行动、行动改变暴露、最终结果无法朴素评分的干预场景。

S7 已完成候选语义模型：

- Intelligence Product、Product Edition、Product Release、Rendering、Distribution 和 Delivery 分离；
- Product Edition 冻结信息截点、精确对象版本、生成版本与 Dependency Manifest；
- 生成、审核、发布、分发、送达、消费、决定和行动不再压成单一状态；
- Alert 与 Signal / Change / Risk 分离，并拆开 severity、confidence、urgency 和 priority；
- Feedback 必须锚定目标与版本，不能直接成为 Evidence、Fact 或演进决定；
- Provenance、Derivation Lineage、Semantic Dependency、Citation、Distribution 和 Causal Lineage 分型；
- 影响传播采用固定版本、有类型依赖边和逐分支停止理由，不按跳数盲目级联；
- Update、Correction、Supersession、Withdrawal、Retraction 和 Deletion 分离；
- Impact Case 覆盖发现、分类、重算、产品修复、接收者通知和剩余风险；
- 共享 Fact 可触发各租户私有纠正，但不能泄露租户依赖和产品。

S7 未改变 Skeleton v1.0。候选模型已通过一项 Fact 在同一报告中分别充当关键前提、历史 Claim 和背景引用，并已分发到 PDF、仪表板和 API 下游的部分撤回场景。

S8 已完成候选语义模型：

- 世界有效时间与系统记录时间形成对象 Revision 的二时态基础；
- 认识时间不采用全局第三时间戳，而建模为 Authority Domain 对对象 Revision 的 Epistemic Record；
- observed、available、considered、accepted、challenged 和 withdrawn 分离；
- 来源时间、观察时间、获取时间、处理时间不再冒充系统记录时间；
- Object Identity、不可变 Revision、生命周期事件和查询 View 分离；
- 跨作用域 Revision 允许分支，不存在隐含全局总序或 last-write-wins；
- Ownership、Visibility、Semantic Authority、Purpose、Jurisdiction 和 Retention 分开表达；
- Tenant Overlay 通过候选发现、作用域过滤、时间截取和 Selection Policy 合成，不改写公共对象；
- “现在看过去”“还原当时所知”“回放当时系统记录”成为三类不同查询；
- 历史回放必须先经过当前授权门，旧策略不能恢复已撤销权限；
- 迟到资料可以改变当前对过去的认识，但不能倒写历史认识；
- Revision 日志、认识记录、风险检查点和 Product Manifest 共同支撑历史重建，不全量物化所有时点。

S8 未改变 Skeleton v1.0。候选模型已通过内部任命 2 月生效、租户 A 早期获知、公共域 5 月才接受、租户 B 始终不可见，以及 4 月产品需要历史复现的迟到资料场景。

S9 已完成候选收敛模型：

- 使用 Core Object、First-class Supporting Record、Typed Relation / Value、View、Deferred 五级分类；
- Mention、Scenario、Governance Case 和 Product Edition 进入核心对象采纳候选；
- Product Release、Epistemic Record、Semantic Edge Record、Forecast Resolution / Score 等被裁定为一等支撑记录；
- Fact 保持带 Authority Domain 的核心聚合，不成为脱离接受上下文的全局命题；
- Governance Case 统一 Dispute、Entity Resolution、Impact 等案件生命周期，同时保留子型制度；
- 语义形成、验证审核、接受决定、发布授权、保管强制和执行副作用权分离；
- 数据底座拥有 ID、Revision、Transition、血缘和审计保管权，但不取得 Fact、Assessment 或 Product 的语义决定权；
- 生命周期拆为 Revision、认识、治理、可用性、质量和执行/交付等正交状态轴；
- 统一 Transition Envelope 支持提交前授权复判、乐观并发、幂等、原子提交和外部 Saga；
- Semantic Edge Record 注册来源、推导、论证、接受、身份、依赖、治理、版本、分发、因果和演进十一类边；
- 建立 L0—L5 血缘完整性等级和按对象/用途的最低发布门；
- 形成 28 条 S9 候选跨对象不变量，等待 S10 与既有 INV 合并；
- 高影响独立性按 Mandate、证据责任和共同失效根判断，不按 Agent 实例数量判断。

S9 未改变 Skeleton v1.0。候选模型已通过抽取 Agent 越级晋升 Fact、自填审核、跨租户广播、血缘断裂、授权撤销竞态和数据库管理员语义撤回等组合场景。

## 八、与 Skeleton v1.0 的关系

本轮默认不改变十面，只细化：

```text
L4 情报语义与对象主干
× L5 契约、状态与异常
× L6 租户、证据、审计和生命周期
× L7 认识完整性与纠正
```

任何候选设计都必须保留：

```text
Document ≠ Evidence ≠ Claim ≠ Fact
Fact ≠ Inference ≠ Recommendation
Agent Memory ≠ Intelligence Database
```
