# SCN-0003：持续监测与风险告警

状态：Accepted

## 目标

租户要求持续监测一家供应商的经营、制裁和关键人员变化。验证没有同步用户请求时，天机阁仍可“察变—推势—警险”。

## 前置

- 已批准的长期 Mission；
- Court Profile 中的监测目标、来源、频率和告警阈值；
- R1 常态、触发重大制裁时提升为 R2；
- Standard 证据，重大告警使用 Strict。

## 主路径

```text
钦天监按计划或来源事件触发 Run
→ 工部 Tool 拉取新 Snapshot 并计算 Document Diff
→ 数据认知面把字段变化联结为 Event / Change
→ 与既有 Entity、Relationship 和 Fact 做影响分析
→ 情报研判面更新 Assessment / Forecast / Risk
→ 阈值和规则判断是否创建 Alert
→ 高影响 Alert 经门下复核
→ 产品面按订阅、受众和渠道投递
→ 皇帝主动向仙人解释“发生了什么、为何重要、证据如何”
→ 回执、风险状态和反馈持续写回
```

## 状态

```text
Run Scheduled → Running → Succeeded / Degraded
Change Detected → Validated → Significant → Linked → Consumed
Risk Monitoring → Escalated / Mitigating / Closed
Alert Created → Queued → Delivered → Acknowledged → Resolved
```

## Agent / Tool 分工

- 定时、采集、Diff、阈值、去重和投递：Tool/Service；
- 变化含义、影响链和风险解释：领域/研判 Agent；
- 门下只在风险提升或证据冲突时启用；
- 皇帝负责主动汇报，不承担底层常驻轮询。

## 异常

- 来源长期不可用：产生来源健康事件和 `Degraded` Run；
- 同一事件多源重复：合并事件与告警，不重复轰炸；
- 告警投递失败：重试、备用渠道、升级，保留投递状态；
- 风险阈值配置过敏：反馈进入能力演进，不静默抑制历史告警；
- 监测授权到期：停止新的采集和私有数据使用。

## 验证结果

通过。长期状态属于 Mission、订阅、风险和数据底座；Agent 实例可按事件创建并退出。

验证 `INV-0002`、`INV-0003`、`INV-0005`、`INV-0006`、`INV-0007`。
