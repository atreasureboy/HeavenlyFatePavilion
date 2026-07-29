# Skeleton v1.0 基线声明

状态：Accepted

生效日期：2026-07-29

## 一、基线声明

天机阁整体架构骨架正式进入 `Skeleton v1.0`。

该基线由以下结构共同构成：

```text
L0 定位与不可变前提
L1 外部上下文与主权
L2 五域十面一级职责
L3 皇帝、三省六部与公共官署映射
L4 情报语义和对象主干
L5 运行形态、七流、契约、状态与异常
L6 四轴坐标和七维横切治理
L7 生存、负空间与信任边界
L8 全域能力演进
L9 验证与版本闭合
```

具体权威来源由[架构骨架索引](./architecture-skeleton-index.md)维护。

## 二、基线核心

- 十个一级面是稳定职责所有者；
- 五个域只用于视觉分组；
- 皇帝是统一智能中枢，但不是无限权限超级 Agent；
- 三省表达规划、执行统筹和审核封驳三种可区分权力；
- 六部表达专业领域能力，不复制共享平台；
- 数据底座独立于 Agent Memory 和基础设施；
- 基础设施是共同承载，不是一级逻辑权威；
- Agent 处理规划、判断、反证与综合，确定性组件处理强制、存储和高吞吐执行；
- 多租户、权限、证据、预算、质量、审计和生命周期始终横切；
- 能力演进覆盖全域，但不拥有全域采用权。

## 三、基线不变量

```text
能力可以开放，权力必须受限
统一体验不等于统一权力
规划、执行、审核不得静默合一
Document ≠ Snapshot ≠ Evidence ≠ Claim ≠ Proposition
Proposition + scoped Acceptance Decision → Fact Aggregate
Fact Aggregate ≠ Assessment ≠ Forecast ≠ Risk ≠ Recommendation
Product ≠ Edition ≠ Release ≠ Delivery ≠ Action
Agent Memory ≠ Intelligence Database
期望状态 ≠ 即时执行状态
提出变更 ≠ 采用变更 ≠ 发布变更 ≠ 实际生效
```

## 四、基线不承诺

`Skeleton v1.0` 不承诺：

- 当前技术能够实现天机阁；
- 已经选定数据库、协议、模型、框架或部署平台；
- 所有二级和三级能力已经详细设计；
- 所有安全、容量、合规和失败场景已经穷举；
- 十面在未来永远不可调整。

## 五、变更纪律

局部设计默认只能提高既有骨架的分辨率。

如果新发现要求改变一级职责、主权、不变量或全局关系，必须重新打开骨架，通过：

```text
新问题族证据
→ 架构候选
→ ADR
→ 影响分析
→ 压力场景
→ 全局重新闭合
→ 有权人类采用
```

任何模型升级、Agent 合并或工具替换都不能绕过这一过程静默改变权力语义。

## 六、阶段出口

Skeleton v1.0 标志 G0 外层定位骨架完成，不标志整体架构骨架阶段结束。项目继续完成 G1 一级面内部系统骨架、G2 关键机制骨架和 G3 全局连接闭合：

1. 先横向补齐十面的同级系统结构；
2. 再为关键系统补对象、协议、状态、异常和质量属性主干；
3. 用跨面场景验证系统级连接；
4. 将新发现反向检查是否触及 G0 基线；
5. 整体骨架完成后，才转入字段、算法、选型、参数和部署等详细设计。

阶段分辨率与完成门槛见[整体架构骨架完善范围与阶段边界](./skeleton-completion-scope.md)。

## 七、依据

- [Skeleton v1.0 正式就绪评审](../07-analysis-and-validation/skeleton-v1.0-readiness-review.md)
- [Skeleton v0.3 闭合与一级稳定性复核](../07-analysis-and-validation/skeleton-v0.3-closure-and-stability-review.md)
- [总体一级逻辑骨架](./overall-logical-skeleton.md)
- [Skeleton v1.0 基线封版图](./text-diagrams/TXT-0024-skeleton-v1.0-baseline.md)
- [外层架构骨架终局完成审计](../07-analysis-and-validation/outer-skeleton-final-completion-audit.md)
- [TXT-0025：外层骨架终局总图](./text-diagrams/TXT-0025-outer-skeleton-final-map.md)
- [整体架构骨架完善范围与阶段边界](./skeleton-completion-scope.md)
