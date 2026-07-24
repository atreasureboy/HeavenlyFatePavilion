# SCN-0008：事实撤回与产品更正

状态：Accepted

## 目标

一个已发布报告依赖的关键 Fact 因来源承认错误而被撤回。验证纠正可以沿血缘传播到研判、风险、告警和产品。

## 主路径

```text
来源发布更正 → 新 Snapshot
→ 数据认知面建立纠正 Evidence / Claim
→ 原 Fact Active → Challenged → UnderReview → Retracted
→ 数据底座沿派生血缘定位：
   Assessment / Forecast / Risk / Recommendation
   Product / Alert / API Consumer
→ 按影响等级创建重评 Task
→ 研判对象进入 ReassessmentRequired / Invalidated
→ 风险与告警重新计算
→ 已发布 Product 进入 CorrectionRequired
→ 门下审核更正范围和措辞
→ 产品面发布 Corrected Product，并将旧版标为 Superseded/Withdrawn
→ 皇帝向受影响仙人解释原结论、变化原因和当前结论
→ 下游渠道收到机器可识别的纠正事件
```

## 保留原则

- 旧 Snapshot、Fact 版本和报告不被静默覆盖；
- 普通用户默认看到当前有效版本，同时可以按权限查看更正历史；
- 撤回 Fact 不等于删除原始来源；
- 若新证据只影响部分结论，只重评受影响分支。

## 异常

- 下游不可达：重试并记录未确认消费者；
- 某报告引用被复制到租户外：按分发许可尽最大能力通知，记录不可控边界；
- 血缘断裂：隔离疑似受影响产品，进入 S3 补链；
- 更正本身存在争议：保持 Disputed，不用第二个未验证 Claim 覆盖第一个。

## 验证结果

通过。纠错是版本化状态转换和影响传播，不是数据库原地修改；产品生命周期与事实生命周期解耦但可联动。

验证 `INV-0003`、`INV-0005`、`INV-0006`、`INV-0007`。
