# ADR-0008：采用有类型、可追溯的核心对象主干

状态：Accepted

日期：2026-07-24

## 背景

十个一级面已经建立，但如果它们只交换自然语言文本、通用 JSON 或模型上下文，就无法稳定区分用户意图、执行记录、资料、证据、事实、研判、预测和产品，也无法证明结论如何形成。

## 驱动因素

- 十个一级面需要稳定语义契约；
- 模型输出不能直接晋升为事实；
- 争议、更正、时间变化和预测校准必须保留历史；
- Agent、Tool、人类和外部服务产生的结果需要采用同一追踪框架；
- 产品必须能回溯到事实、证据、执行和原始资料。

## 候选方案

1. 各面交换自然语言和自由结构对象；
2. 使用一种通用 Knowledge Item，通过标签表达所有语义；
3. 建立有类型的核心对象主干，以显式转化和血缘连接对象。

## 决定

采用方案 3。

核心对象按六组组织：

- 意图与治理：Intent、Mission、Plan、Decision；
- 执行：Run、Task、Execution Event、Artifact；
- 资料与认识论：Source、Document、Snapshot、Evidence、Claim、Verification Record、Fact、Disputed Claim；
- 世界状态：Entity、Relationship、Event、Change；
- 研判：Signal、Assessment、Forecast、Risk、Recommendation；
- 交付与演进：Intelligence Product、Alert、Feedback、Evolution Evidence、Capability Change Candidate。

对象转化创建新的有类型对象和血缘关系，不把原对象原地改名。更正采用新版本、争议、撤回或失效关系，不静默覆盖历史。

## 影响

正面影响：

- 十个一级面拥有可讨论和可测试的接口语言；
- Fact、Assessment、Forecast 和 Risk 的边界可被系统强制表达；
- 任一产品结论可以回溯到 Evidence、Snapshot、执行主体和版本；
- 人类、Agent 和 Tool 可以在统一对象契约下协作；
- 更正传播和预测校准获得稳定基础。

代价与风险：

- 对象数量和关系数量显著增加；
- 需要避免为了类型纯洁而制造过细对象；
- 对象 Schema、状态机、版本和作用域治理成为后续必需工作；
- 通用对象与领域扩展之间需要清晰继承或组合机制。

## 重新评估条件

- 某些对象长期无法形成稳定语义边界；
- 通用对象主干阻碍领域表达或造成不可接受的转换成本；
- 新型模型或知识表示证明存在更强且同样可审计的统一表达；
- 外部行动成为核心能力，需要扩展 Action、Authorization 和 Compensation 对象主干。
