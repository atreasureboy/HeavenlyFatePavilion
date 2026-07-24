# 治理与执行状态机

状态：Accepted

## 一、Mission 生命周期

```text
Draft
  │ submit
  ▼
Proposed
  │ review_started
  ▼
UnderReview
  ├── approve ─────────▶ Approved
  ├── return_for_change ▶ Draft
  └── reject ──────────▶ Rejected

Approved
  │ activate
  ▼
Active
  ├── suspend ─────────▶ Suspended ──resume──▶ Active
  ├── complete ────────▶ Completed
  ├── cancel ──────────▶ Cancelled
  └── replace ─────────▶ Superseded
```

### 关键守卫

- `submit`：目标、作用域、发起主体和租户明确；
- `approve`：验收、证据、预算、风险和升级条件齐备；
- `activate`：至少存在一个已批准 Plan 或持续监测模板；
- `complete`：验收条件满足或有显式的有缺口完成 Decision；
- `cancel`：记录原因、已发生副作用和未完成对象；
- `replace`：绑定新 Mission 版本及迁移关系。

### Rejected 与 Cancelled

`Rejected` 表示 Mission 未获准进入执行；`Cancelled` 表示已经获准或活动的 Mission 被授权终止。

## 二、Plan 生命周期

```text
Draft
  │ submit
  ▼
Proposed
  │ review
  ▼
UnderReview
  ├── approve ─────────▶ Approved
  ├── return ──────────▶ Draft
  └── reject ──────────▶ Rejected

Approved
  ├── instantiate ─────▶ InUse
  ├── replace ─────────▶ Superseded
  └── revoke ──────────▶ Revoked

InUse
  ├── complete_runs ───▶ Fulfilled
  └── replace ─────────▶ Superseded
```

Plan 一经用于创建 Run，其内容不原地修改；变更产生新版本。

## 三、Run 生命周期

```text
Created
  │ validate_context
  ▼
Validated
  │ schedule
  ▼
Scheduled
  │ start
  ▼
Running
  ├── wait_dependency ─▶ Waiting ──resume──▶ Running
  ├── pause ───────────▶ Paused ───resume──▶ Running
  ├── quality_degrade ─▶ DegradedRunning
  ├── succeed ─────────▶ Succeeded
  ├── partial_finish ──▶ Degraded
  ├── fail ────────────▶ Failed
  ├── timeout ─────────▶ TimedOut
  └── cancel ──────────▶ Cancelled
```

### 关键守卫

- `validate_context`：Plan、Court Profile、租户、权限、预算和版本一致；
- `start`：所需最小能力可用且租约成立；
- `quality_degrade`：治理策略允许继续，且降级影响可声明；
- `succeed`：所有必需 Task 完成并满足 Run 级验收；
- `partial_finish`：明确缺失内容、适用限制和后续补偿；
- `cancel`：停止新副作用并记录已发生副作用。

## 四、Task 生命周期

```text
Created → Ready → Running
                   ├── wait ─────────▶ Waiting ─────▶ Ready
                   ├── transient_fail ▶ RetryScheduled ─▶ Ready
                   ├── compensate ───▶ Compensating ─▶ Compensated
                   ├── succeed ──────▶ Succeeded
                   ├── permanent_fail ▶ Failed
                   ├── timeout ──────▶ TimedOut
                   └── cancel ───────▶ Cancelled
```

### 重试规则

重试只适用于可分类的瞬态失败，并必须满足：

- Task 具有幂等键，或存在显式补偿；
- 未超过次数、时间、预算和风险上限；
- 输入版本仍然有效；
- 重试不会掩盖逻辑失败。

## 五、Agent Instance 生命周期

```text
Requested
→ Provisioning
→ Ready
→ Running
→ Waiting / Paused
→ Completed / Failed / Cancelled / Terminated
→ Disposed
```

稳定 Agent 身份与 Agent Instance 生命周期分离。实例销毁不删除身份、能力注册、决定或审计记录。

## 六、治理异常路径

```text
Intent 不清
→ Mission Draft
→ Returned
→ 人机交互面澄清

门下封驳
→ Plan UnderReview
→ Returned / Rejected
→ 中书修订或皇帝升级

执行超预算
→ Run Paused / DegradedRunning
→ Budget Pressure Event
→ 户部解释 + 平台控制核验
→ 治理决定追加 / 降级 / 取消

运行不可恢复失败
→ Run Failed
→ 记录部分结果和副作用
→ 尚书省评估替代路径
→ 门下决定接受降级或退回
```
