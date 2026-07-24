# v0002 输入

状态：Draft

## 已接受输入

- `Skeleton v1.0` 九层架构骨架；
- 数据认知面“掌数、联结、察变”；
- 情报研判面“推势、卜算、警险”；
- 数据底座面的长期权威记录职责；
- Document、Evidence、Claim、Fact、Inference、Product 分层不变量；
- 现有核心对象主干、对象所有权和最小生命周期；
- 公共知识、租户私有覆盖和联合空间的作用域骨架；
- 证据血缘、审计和数据生命周期横切治理；
- 事实冲突、撤回、产品更正和能力回滚场景。

## 问题输入

- “数据关联”必须区分实体同一性、语义关系、事件参与和统计相关；
- “数据 Diff”必须区分资料变化、抽取变化、现实变化和知识状态变化；
- “事实”不能只是数据库中的一行，也不能由单个 Agent 自行宣布；
- 趋势与预测必须能表达假设、不确定性、适用时间和失效条件；
- 同一公共资料可以被多个租户复用，但租户判断、权限和产品不能泄漏；
- 事实被撤回后，所有依赖它的关系、研判、风险和产品需要可定位影响。

## 既有依据

- [核心对象主干](../../04-domain-design/core-object-spine.md)
- [对象流转图](../../03-current-architecture/text-diagrams/TXT-0006-core-object-spine.md)
- [十面输入输出契约](../../03-current-architecture/plane-contracts.md)
- [核心状态机](../../08-reference-models/state-machines/README.md)
- [事实撤回与产品更正场景](../../05-scenarios-and-flows/README.md)
