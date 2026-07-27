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
→ S4 实体、身份、别名与消歧
→ S5 关系、事件、状态、Diff 与 Change
→ S6 Signal、Assessment、Forecast、Risk、Recommendation
→ S7 产品、告警、反馈与影响纠正
→ S8 双时态、版本、作用域与知识覆盖
→ S9 对象权威、生命周期、血缘与不变量
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
