# v0002 候选变化

状态：Draft

以下内容是待论证候选，不是当前架构决定。

## 候选一：建立四层语义区

```text
资料层：Source / Document / Snapshot / Artifact
认识层：Evidence / Claim / Fact / Entity / Event / Change
研判层：Signal / Assessment / Forecast / Risk / Recommendation
交付层：Product / Alert / Feedback / Effect
```

目的：防止存储对象、世界对象、推断对象和交付对象混为一层。

## 候选二：把 Diff 拆成四类

- `Content Diff`：同一资料不同版本的内容差异；
- `Extraction Diff`：处理模型或规则变化造成的抽取差异；
- `World Change`：现实世界对象、关系或状态发生变化；
- `Knowledge Revision`：系统因新证据、裁定或纠错改变既有认识。

目的：避免看到网页变化就直接宣布现实发生了变化。

## 候选三：采用双时态并保留认识时间

- 有效时间：对象在现实世界何时成立；
- 系统时间：天机阁何时记录或修改它；
- 认识时间：某主体何时知道、接受或撤回该结论。

是否将认识时间独立于系统时间，需要场景验证。

## 候选四：事实采用显式接受语义

`Fact` 不是“绝对真理”，而是某个权威域在指定证据标准、作用域和版本下接受的事实状态。

事实必须能表达接受者、接受依据、适用作用域、有效时间以及争议、撤回和替代关系。

## 候选五：对象身份与陈述身份分离

现实对象的身份、关于对象的陈述、支持陈述的证据和系统对陈述的接受状态分别建模。

目的：同一个人或企业可以存在多个冲突主张，而不需要复制或污染实体身份。

## S1 补充候选：区分对象、状态、视图、记录和关系

正式对象分类不再把所有名词放在同一个平面：

- `Disputed Claim` 默认是 Claim 的状态或查询视图；
- `Verification Record` 是过程记录，不是世界事实；
- `Current Fact` 是查询视图，不是新的 Fact 类型；
- `Diff` 是检测或比较结果，只有经过语义接受的差异才成为 Change；
- `Execution Event` 与现实世界 Event 必须使用不同类型。

完整候选见[统一语言与对象分类法](../../08-reference-models/unified-language-and-object-taxonomy.md)。

## S2 补充候选：资料保真与证据锚定

- Source、Document、Snapshot、Evidence 是四种对象身份，不是同一对象的四个状态；
- Source Principal、Source System、Source Endpoint 必须可分别表达；
- Document 归并不能只依赖 URL、标题或内容哈希；
- Snapshot 表示实际取得的不可变内容，必须声明捕获完整性；
- Acquisition Record 证明怎样获取，Snapshot 表示获得了什么；
- Evidence 必须锚定 Snapshot 或其他可审计材料根；
- OCR、转录、翻译和解析形成派生链，不得改写原 Snapshot；
- 内容变化、抽取变化、现实变化和知识修订不得合并；
- Rights / Usage Envelope 必须随资料与证据传播，并在租户访问时重新判定。

完整候选见[Source、Document、Snapshot 与 Evidence 语义模型](../../04-domain-design/source-document-snapshot-evidence-model.md)。

## S3 补充候选：作用域事实与争议案件

- Claimant、Extractor、Verifier、Acceptor 必须是可区分主体；
- Claim 保留原始表达，规范化 Proposition 用于去重与冲突；
- Verification Record 是不可变过程记录，Verification Package 是审核视图；
- Fact 是某个 Acceptance Context 内的接受记录，不是全局永恒真理；
- Public、Tenant、Joint、Mission Working 和 External Authority Fact 可以并存；
- Current Fact 是结合主体、租户、用途、辖域和时间计算的查询视图；
- 真冲突前先排除实体、角色、时间、定义、辖域和作用域错配；
- Dispute Case 作为智能治理案件承载多方举证、裁定、上诉与重开；
- Unresolved 是合法知识状态；
- Fact 撤回不等于物理删除，且必须触发依赖影响分析。

完整候选见[Claim、争议、Fact 与裁定语义模型](../../04-domain-design/claim-fact-and-adjudication-model.md)。

## S4 补充候选：可逆实体身份解析

- Entity Record 不是现实对象、名字、来源记录或 IAM Principal；
- Mention 作为材料到 Entity 的一等认识对象候选；
- Name、Alias、Identifier 和 Identity Claim 使用不同语义；
- Identifier 必须绑定 issuer、namespace 和有效时间；
- 候选相似度只决定验证顺序，不产生身份接受权；
- 负身份约束是一等输入，可阻止自动合并；
- Identity Claim 继续遵循 Evidence、Verification、Acceptance Context；
- Entity 合并采用可逆 Identity Link 和 Canonical View，不删除组成历史；
- `same_as`、展示聚合、数据整合与现实并购不得共用一个 merge；
- Split 沿血缘重新解析 Mention，并重评 Claim、Fact、Relationship、Event、Assessment 和 Product；
- Public Entity 与 Tenant Overlay 隔离；
- 技术上能够识别不意味着有权建立或展示身份关联。

完整候选见[Entity、身份、别名与消歧语义模型](../../04-domain-design/entity-identity-and-resolution-model.md)。

## S5 补充候选：世界状态与四类变化

- Relationship 是带角色、方向、时间和 Evidence 的持续联系；
- Event 是有时间边界的发生项，可以建立、修改或终止 Relationship；
- World Event、Execution Event、Domain Event 和 Alert 必须分型；
- State 默认是按事实域、作用域、有效时间和认识时间计算的 View；
- Diff 是声明左右版本和方法的比较记录；
- Content Diff、Extraction Diff、Knowledge Revision、World Change 不得混写；
- Change 作为一等对象连接前态、后态、Evidence、Event、监测和研判；
- World Change 必须经 Claim / Verification / Fact Acceptance；
- 首次观察不等于刚刚出现，来源消失不等于现实终止；
- Change 的有效、检测和接受时间必须分开；
- Event 先于 Change 不产生因果权威；
- Entity Split、Fact Retract 和本体升级必须触发世界模型影响分析。

完整候选见[Relationship、Event、State、Diff 与 Change 语义模型](../../04-domain-design/world-state-event-and-change-model.md)。

## S6 补充候选：可校准研判与干预反馈

- Signal、Assessment、Scenario、Forecast、Risk、Recommendation 必须类型分离；
- Scenario 是条件化可能世界，可以无概率；
- Forecast 必须具有目标、结果空间、信息截点、时间窗和解析规则；
- Assessment 必须显式保存假设、反证、替代解释、未知和 Dissent；
- 不确定性按数据、身份、认识、随机、模型、情景、测量、分歧等分面；
- Intelligence Risk 与 Mission Risk Tier / Operational Risk 严格分离；
- Recommendation 不产生 Decision、Authorization 或 Action；
- Forecast 到期后进入独立解析、评分和 cohort 校准；
- 建议触发的 Action / Effect 必须进入因果血缘；
- 干预改变现实后，Forecast 标记 InterventionAffected，不能朴素判错；
- 高影响研判按风险购买来源、模型、审核和人类独立性；
- 预测生成器不得垄断解析、评分和历史样本。

完整候选见[Signal、Assessment、Forecast、Scenario、Risk 与 Recommendation 语义模型](../../04-domain-design/assessment-forecast-risk-recommendation-model.md)。
