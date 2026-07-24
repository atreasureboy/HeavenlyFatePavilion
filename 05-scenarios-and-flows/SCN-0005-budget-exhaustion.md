# SCN-0005：运行中预算耗尽

状态：Accepted

## 目标

跨域调查执行到一半时，外部搜索成本和模型 Token 接近 Mission 上限。验证“户部哭穷”、预算强制与功能底线。

## 主路径

```text
运行面计量发现剩余预算低于完成估算
→ 产生 Budget Pressure Event
→ 暂停创建新的非必要 Task，已预留任务按策略继续
→ 户部汇总消耗、剩余目标和成本预测
→ 尚书省给出可执行选项：
   A. 复用事实、缓存和确定性 Tool
   B. 取消低价值分支并重排 Task
   C. 缩小调查范围，返回 Degraded 产品
   D. 请求仙人追加预算
→ 中书省检查选项是否仍满足 Mission 最低验收
→ 门下检查是否偷偷降低证据、安全或审核标准
→ 有权主体选择方案
→ 更新预算包、Plan 和 Run 状态后继续或停止
```

## 状态

```text
Run Running → Paused / DegradedRunning → Running / Degraded / Cancelled
Task Ready → Cancelled（低价值分支）
Mission Active → Suspended / Active / Completed
```

## 不允许的行为

- Agent 超额调用后再补账；
- 删除审计或跳过权限检查节省成本；
- 把 Strict 证据任务静默改为 Overview；
- 把未完成范围包装成完整结果；
- 户部建议直接改写平台预算账本。

## 异常

- 追加预算被拒：显式缩小范围或取消；
- 最低证据标准已无法满足：停止并返回缺口；
- 并行预留造成账本不一致：暂停相关消费并对账；
- 外部调用已经产生不可撤销费用：记录实际消费，不伪造回滚。

## 验证结果

通过。预算是有状态的治理对象；“哭穷”是结构化压力事件和方案解释，而不是情绪化 Agent 文本。

验证 `INV-0004`、`INV-0005`、`INV-0006`、`INV-0008`。
