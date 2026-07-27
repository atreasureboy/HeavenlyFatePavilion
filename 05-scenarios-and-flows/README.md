# 场景与流程

状态：Accepted

场景用于验证分散在各卷中的设计能否形成闭环。每个场景应包含参与者、正常路径、权限传播、数据读写、Agent 与工具调用、预算消耗、审核要求、异常分支、审计记录及不变量检查。

场景分为用户查询、持续监测、平台治理、安全攻击、突发容量和灾难恢复等类型。

## 场景验证模板

每个骨架验证场景固定回答：

1. 目标和触发；
2. 参与主体、一级面及官署；
3. 前置上下文；
4. 主路径和对象状态变化；
5. Agent 与确定性组件分工；
6. 七个横切维度如何传播；
7. 异常、补偿和人类升级；
8. 审计及验证结果；
9. 被验证的架构不变量。

场景不是产品需求故事。它的任务是主动寻找架构断点：如果某个步骤找不到权威所有者、契约、对象、状态、失败路由或控制证据，就说明骨架尚未闭合。

## 第一批骨架闭合场景

| 场景 | 主验证目标 | 结果 |
|---|---|---|
| [SCN-0001 公共信息快速查询](./SCN-0001-public-information-query.md) | 低风险路径不必启动完整 Agent 朝廷 | 通过 |
| [SCN-0002 租户私有企业尽调](./SCN-0002-private-due-diligence.md) | 十面、三省六部与证据主链完整协作 | 通过 |
| [SCN-0003 持续监测与风险告警](./SCN-0003-continuous-monitoring-alert.md) | 无用户同步请求时的长期闭环 | 通过 |
| [SCN-0004 冲突证据与事实裁定](./SCN-0004-conflicting-evidence.md) | Disputed Claim、反证、裁定与保留分歧 | 通过 |
| [SCN-0005 运行中预算耗尽](./SCN-0005-budget-exhaustion.md) | 预算压力、重规划和显式降级 | 通过 |
| [SCN-0006 长时任务权限撤销](./SCN-0006-authorization-revocation.md) | 撤销传播、关键点重判和中止补偿 | 通过 |
| [SCN-0007 跨租户联合调查](./SCN-0007-cross-tenant-collaboration.md) | 联合空间而非租户边界消失 | 通过 |
| [SCN-0008 事实撤回与产品更正](./SCN-0008-fact-retraction-and-correction.md) | 纠正血缘贯穿事实、研判、告警和产品 | 通过 |
| [SCN-0009 模型退化与能力回滚](./SCN-0009-model-degradation-and-rollback.md) | 运行证据驱动全域能力演进 | 通过 |

## 覆盖结论

[第一批场景覆盖矩阵](./scenario-coverage-matrix.md)表明：

- 十个一级面都至少在一个主路径中承担实质责任；
- 七个横切维度都存在正常传播与至少一个失败验证；
- Mission、Run、Task、Claim、Fact、Assessment、Forecast、Risk、Alert、Product 和 Capability Version 状态均被触达；
- 正常、退回、争议、降级、停止、补偿、更正、回滚和人类接管路径均有场景；
- 高层 Agent 与确定性 Tool/Service 的边界在不同风险等级下保持一致。

这使整体骨架达到“场景闭合第一版”，不代表容量、安全、部署和各领域专题已经完成。

## Skeleton v0.3 宏观压力场景

[第一轮宏观压力测试](../07-analysis-and-validation/skeleton-v0.3-macro-stress-test.md)进一步用八个极端方向攻击整体骨架：

- 跨辖域规则冲突；
- 外部联邦事实污染；
- 热点事件全域拥塞；
- 区域中断与最低生存内核；
- 来源、Tool 与模型供应链联合污染；
- 超级模型合并治理角色；
- 外部响应成功但现实效果失败；
- 自我演进改变跨年知识解释。

这些是宏观结构测试，不替代后续独立场景的详细时序。第一轮结果没有产生新的一级面、全局流、尺度轴或横切维度。

## 专题深化场景

| 场景 | 所属专题 | 状态 |
|---|---|---|
| [SCN-0010 来源变化与证据保全](./SCN-0010-source-mutation-and-evidence-preservation.md) | v0002 / S2 资料与证据对象 | Proposed |
| [SCN-0011 多权威域事实并存](./SCN-0011-multiple-authority-domain-facts.md) | v0002 / S3 主张与事实接受 | Proposed |
| [SCN-0012 同名实体误合并与拆分纠正](./SCN-0012-entity-merge-split-correction.md) | v0002 / S4 实体身份与消歧 | Proposed |
| [SCN-0013 四类变化分离](./SCN-0013-four-kinds-of-change-separation.md) | v0002 / S5 世界状态与变化 | Proposed |
| [SCN-0014 预测干预与风险重校准](./SCN-0014-forecast-intervention-and-recalibration.md) | v0002 / S6 研判与预测 | Proposed |
| [SCN-0015 部分事实撤回、影响裁剪与接收者纠正](./SCN-0015-partial-retraction-impact-and-recipient-correction.md) | v0002 / S7 产品、反馈与纠正 | Proposed |
