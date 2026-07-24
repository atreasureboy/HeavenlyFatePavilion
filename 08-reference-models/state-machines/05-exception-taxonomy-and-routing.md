# 异常分类与跨面路由

状态：Accepted

## 一、异常对象

所有需要跨面处理的异常都形成结构化 `Exception Record`：

```text
Exception Record
├── exception_id
├── category / code
├── severity
├── source_plane / source_subject
├── affected_object_refs
├── tenant_scope
├── mission_id / run_id / task_id
├── detected_at
├── symptoms
├── known_side_effects
├── retryability
├── degradation_allowed
├── recommended_route
├── owner_plane
├── status
└── trace_id / causation_id
```

## 二、异常严重度

| 等级 | 含义 | 默认处理 |
|---|---|---|
| S0 | 信息性偏差，不影响验收 | 记录和观察 |
| S1 | 局部轻微异常，可自动恢复 | 重试、替代或局部降级 |
| S2 | 影响部分结果或时间 | 显式降级、尚书统筹 |
| S3 | 影响 Mission 验收、事实或高价值产品 | 门下审核、治理升级 |
| S4 | 跨租户、不可逆、高影响或系统性风险 | 停止相关路径、人类接管 |

严重度不等于安全分类；后续安全专题可以增加专用维度。

## 三、异常类别

| 类别 | 典型代码 | 例子 | 默认所有面 |
|---|---|---|---|
| Intent / Governance | GOV | 意图不清、Plan 被封驳、决定冲突 | 人机交互 / 智能治理 |
| Context / Scope | CTX | tenant 丢失、版本错配、作用域不明 | 平台控制 |
| Authorization / Policy | POL | 无权调用、策略拒绝、能力未许可 | 平台控制 / 枢密院 |
| Budget / Capacity | BUD | Token 不足、配额耗尽、容量拥堵 | 平台控制；户部解释 |
| Source / Data | DAT | 来源不可用、Snapshot 损坏、Schema 不兼容 | 数据认知 / 数据底座 |
| Evidence / Knowledge | EPI | 无证据、Claim 冲突、Fact 被挑战 | 数据认知 / 智能治理 |
| Assessment / Quality | QLT | 推断越界、预测失校、风险误报 | 情报研判 / 门下 |
| Runtime / Dependency | RUN | 超时、循环、死锁、模型失败、外部依赖失败 | 智能执行与运行 |
| Product / Delivery | PRD | 引用失效、产品需更正、告警未送达 | 情报产品 / 人机交互 |
| Evolution / Release | EVO | 回归、实验污染、Canary 失败 | 能力演进 / 平台控制 |

## 四、异常状态

```text
Detected
→ Classified
→ Routed
→ Handling
→ Resolved / Degraded / Escalated / Deferred / Unresolved
→ Closed

Closed
→ Reopened  （出现新证据或复发）
```

## 五、路由图

```text
GOV 目标不清 ─────────────▶ 人机交互 / 中书
GOV 审核冲突 ─────────────▶ 门下 / 皇帝 / 人类
CTX 租户或版本错配 ───────▶ 平台控制
POL 策略拒绝 ─────────────▶ 枢密院 / 智能治理
BUD 预算压力 ─────────────▶ 平台控制 + 户部解释
DAT 来源与解析失败 ───────▶ 数据认知 / 工部能力
DAT 持久化与一致性失败 ───▶ 数据底座
EPI Claim / Fact 冲突 ────▶ 大理寺 / 门下
QLT 研判不确定或越界 ─────▶ 情报研判 / 门下
RUN 超时、循环、崩溃 ─────▶ 智能执行与运行
PRD 内容更正 ─────────────▶ 情报产品 / 智能治理
PRD 交付失败 ─────────────▶ 人机交互 / 渠道
EVO 回归或发布失败 ───────▶ 能力演进 / 平台控制
S4 任意类别 ──────────────▶ 停止相关路径 + 人类接管
```

## 六、常见异常骨架

### 无证据

```text
Claim → Unresolved
→ Returned(EPI-EVIDENCE-MISSING)
→ 数据认知补证
→ 仍不足则降级为未知或争议
```

### 超预算

```text
Runtime 检测预算阈值
→ Run Paused / DegradedRunning
→ BUD-PRESSURE
→ 户部解释消耗与选项
→ 平台控制核验上限
→ 治理决定追加 / 降级 / 取消
```

### 模型退化

```text
质量监测发现回归
→ QLT-MODEL-REGRESSION
→ 受影响结果标记
→ 能力演进形成 Evolution Evidence
→ 平台控制暂停或回滚版本
→ 相关对象重新评估
```

### 跨租户上下文错误

```text
发现 tenant / scope 不一致
→ S4 CTX-TENANT-MISMATCH
→ 停止相关路径
→ 保全审计记录
→ 不自动重试
→ 人类与平台治理介入
```

## 七、异常不变量

- 异常不能只存在于日志文本；
- Failed、Rejected、Returned、Cancelled 和 Degraded 不能混用；
- 自动重试只处理可重试异常；
- 重试不能绕过预算、权限和风险限制；
- 降级必须声明质量损失和允许用途；
- S4 默认停止相关路径，不扩大到无关租户或任务；
- 已发生副作用必须记录并进入补偿判断；
- 异常关闭不删除证据；
- 重复异常应形成能力演进证据，而不是无限重试；
- Agent 可以分类和解释异常，确定性策略负责不可绕过的停止和隔离。
