# ADR-0016：采纳作用域化、版本化的情报语义基线

状态：Accepted

决定日期：2026-07-29

## 背景

Skeleton v1.0 已固定十个一级面，但旧核心对象主干只给出了名词序列，尚不足以回答多权威域事实、迟到资料、实体误合并、预测干预、产品纠正和历史回放。

v0002 的 S1～S9 已形成完整候选模型，并通过分项场景验证。现在需要决定这些模型是否成为后续数据、协议、运行、治理和领域专题的共同语义前提。

## 驱动因素

- 观察、主张、接受、推断、建议和行动必须可机器区分；
- 不存在脱离 Authority Domain、时间、作用域和版本的全局事实；
- 任何产品结论必须可追溯和可纠正；
- 公共知识与租户私有知识可以组合，但不能相互污染；
- 模型和 Agent 能力升级不能绕过权威、审核和审计；
- 语义骨架应独立于具体数据库、消息系统和模型供应商。

## 备选方案

1. 继续仅使用线性对象主干，细节留给实现；
2. 采用单一全局知识图谱，以属性表示所有差异；
3. 采纳作用域化、版本化、证据驱动的对象与记录体系；
4. 把所有中间项都提升为核心对象。

## 决定

采纳方案 3，并作以下约束：

1. 采用 Core Object、First-class Supporting Record、Typed Relation / Value、View、Deferred 五级分类；
2. 核心对象按资料证据、主张事实、世界模型、研判、治理执行、产品、交互反馈、演进八个家族组织；
3. Mention、Scenario、Governance Case、Product Edition 纳入核心对象；
4. Product Release、Epistemic Record、Semantic Edge Record、Forecast Resolution / Score 等作为一等支撑记录；
5. Fact 是 Proposition Revision、Acceptance Context、Acceptance Decision 与认识历史组成的聚合，不是全局真理标签；
6. 对象身份、不可变 Revision、状态事件、认识记录和查询 View 分离；
7. 采用世界有效时间与系统记录时间；认识时间由 Authority Domain 的 Epistemic Record 表达；
8. 语义形成、验证审核、接受决定、发布授权、保管强制和执行副作用权彼此分离；
9. 所有关键状态变化经过带 expected revision、授权和因果信息的 Transition Envelope；
10. 关键血缘使用有类型、固定 Revision 的边，并具有完整性等级和使用门；
11. 纠正、撤回、替代和删除不得静默改写历史；
12. 多租户知识通过公共基线、租户覆盖和任务工作空间组合，禁止 last-write-wins。

## 明确延期

- Action 是否成为天机阁边界内核心对象；
- 物理 Schema、索引、分区、存储和消息格式；
- 各 Authority Domain 的具体政策矩阵；
- 跨租户联合调查与授权交换协议；
- 六部各领域本体和专用证据等级；
- 影响传播、实体解析和 Forecast 评分的具体算法。

这些问题影响实现或领域扩展，但不改变当前语义骨架。

## 影响

正面影响：

- 后续专题获得统一对象、时间、权威、状态和血缘语言；
- 能够解释冲突知识而不伪造单一真相；
- 产品、告警和预测具有可复现、可纠正和可审计基础；
- Agent 能力升级不会自动取得更高语义权力。

代价：

- 对象和支撑记录数量增加；
- 查询必须显式提供作用域、时间和选择策略；
- 状态转换、血缘和纠正链需要额外治理成本；
- 简单场景也不能用原地覆盖缩短路径。

## 重新评估条件

- 新场景无法用现有对象家族和 Governance Case 表达；
- 双时态加 Epistemic Record 无法重建关键历史；
- 固定版本血缘在规模上不可接受且无可验证降级方案；
- 多租户覆盖模型无法满足合法联合分析；
- Action 被正式纳入系统职责边界。

## 验证

- [SCN-0010～SCN-0018](../../05-scenarios-and-flows/README.md)
- [v0002 情报语义基线就绪评审](../../07-analysis-and-validation/v0002-intelligence-semantics-readiness-review.md)
