# 天机阁核心状态机

状态：Accepted

## 一、用途

本目录定义跨专题共享的核心对象生命周期和异常路由。状态机是权威业务语义，不依赖某个 Agent 的记忆、Prompt 或某种工作流引擎。

## 二、共同规则

- 状态只能通过有名称的 Transition Event 或 Decision 改变；
- 每次转换记录旧状态、新状态、触发者、原因、时间、版本和 trace；
- Agent 可以建议转换，不能因输出文本而隐式改变权威状态；
- 状态转换必须满足前置条件和权限；
- 终态不等于删除，历史状态和转换必须保留；
- 重试、退回、降级、争议、撤回和回滚具有不同语义；
- 跨对象影响通过事件传播，不直接批量静默改写；
- 定时到期也是显式事件；
- 人类接管不绕过状态机，而是成为有权转换主体；
- 状态机定义业务状态，技术进程状态另行映射。
- 生命周期、认识、审核、发布和运行是正交状态轴，不得压入单个万能状态；
- 关键转换使用 Transition Envelope，并以 expected revision 防止旧版本并发覆盖；
- 状态转换权来自 Authority Domain 和有效 Mandate，不来自 Agent 自述能力；
- 数据底座保管转换记录，但不因此获得语义接受或发布权。

## 三、状态类别

```text
Draft / Created
对象已存在，但尚未提交或执行

Under Review / Scheduled / Waiting
对象等待治理、资源、依赖或时间条件

Active / Running / Published
对象正在生效、执行或对外提供

Challenged / Degraded / Suspended
对象仍存在，但有效性或完整性受到限制

Completed / Confirmed / Scored / Retained
生命周期目标已经正常完成

Rejected / Failed / Cancelled / Retracted / Rolled Back
因不同原因停止，不得混成一种“失败”

Superseded / Archived / Expired
历史仍有效，但不再是当前活动版本
```

## 四、索引

1. [治理与执行状态机](./01-governance-and-execution.md)
2. [事实、世界状态与研判状态机](./02-knowledge-and-assessment.md)
3. [风险、告警与产品状态机](./03-risk-alert-and-product.md)
4. [能力候选、版本与发布状态机](./04-capability-evolution.md)
5. [异常分类与跨面路由](./05-exception-taxonomy-and-routing.md)

总览文字图：[TXT-0010：核心状态机与异常路由](../../03-current-architecture/text-diagrams/TXT-0010-state-machines-and-exceptions.md)

共同语义约束：[情报语义全局不变量](../intelligence-semantic-invariants.md)
