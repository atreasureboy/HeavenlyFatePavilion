# SCN-0014：预测干预与风险重校准

状态：Accepted

提出日期：2026-07-27

## 一、目标

天机阁监测到某关键供应商出现异常：

- 交付延迟；
- 招聘冻结；
- 上游原料受限；
- 管理层频繁变动。

系统形成“未来 60 天发生重大供应中断”的 Forecast，并据此产生 Risk 和供应商切换 Recommendation。租户采纳建议后提前转移订单，最终该租户没有发生供应中断。

验证系统不会简单得出：

> 预测的中断没有发生，所以预测错误。

## 二、对象主链

```text
Facts / Events / Changes
→ Signal S1：供应商异常模式
→ Assessment A1：供应稳定性显著下降
→ Scenario SC1：原料限制持续且供应商未获得替代来源
→ Forecast F1：
   60 天内发生重大中断概率 0.70
→ Risk R1：
   租户关键生产线面临高影响供应风险
→ Recommendation REC1：
   将 40% 订单迁移到第二供应商
```

## 三、Forecast 定义

```text
target:
供应商对租户关键物料连续 7 天无法满足最低交付量

origin_time:
T0

horizon:
T0 + 60 days

information_cutoff:
T0

probability:
0.70

resolution_source:
租户采购和交付记录

condition:
基准为不实施重大供应切换
```

## 四、干预路径

```text
REC1
→ 租户有权主体形成 Decision D1
→ Action X1：迁移 40% 订单
→ 供应暴露下降
→ 租户未发生定义中的重大中断
```

现实结果受到天机阁建议引发的干预影响。

## 五、正确解析

```text
F1.status = InterventionAffected

记录：
- intervention_time
- Decision / Action
- exposure change
- target definition
- causal assumptions
- observed supplier outcome
- observed tenant outcome
```

分别解析：

1. 原供应商自身是否发生供应能力恶化；
2. 租户是否因迁移避免中断；
3. F1 的 target 是否条件化于“不干预”；
4. 是否仍能获得反事实旁证；
5. 本样本是否进入常规概率评分、单独干预 cohort 或只作定性复盘。

不能把 `InterventionAffected` 强制记为 `False`。

## 六、风险重校准

```text
Action X1
→ Exposure 降低
→ Risk R1 新版本：
   likelihood may remain
   tenant impact reduced
   new supplier concentration risk added
```

Risk 更新不改写 F1，也不把 Recommendation 的成本隐藏掉。

## 七、Agent / Tool 分工

### Tool

- Signal 指标检测；
- Forecast 到期调度；
- 交付数据解析；
- Forecast score 计算；
- Action / Effect 血缘；
- risk exposure 重算；
- cohort 校准；
- 干预标签强制。

### Agent / 人类

- Forecast 问题与条件定义；
- Scenario 假设；
- 供应链影响链；
- 替代解释；
- Recommendation 权衡；
- 干预是否改变 target；
- 反事实证据；
- 校准样本是否可比。

## 八、异常分支

### A：租户未采纳建议，供应中断发生

F1 正常解析为 True；Recommendation Outcome 另行评估，不能把“未采纳”当建议无效。

### B：租户采纳建议，但原供应商最终完全正常

F1 仍是 InterventionAffected 或可按原供应商独立结果解析；评估是否过度反应和迁移成本。

### C：供应商看到市场告警后主动改善

形成系统外的反馈干预，记录产品分发可能导致的自我否定效应。

### D：预测问题定义含糊

若“重大中断”无法按预设规则解析，状态为 Ambiguous / Unresolvable，不允许事后修改规则迎合结果。

### E：预测模型自行选择解析数据

解析进入独立评测路径，保留数据截点和评分器版本。

## 九、验证不变量

- Forecast 创建时定义 target、horizon、cutoff 和 resolution；
- Scenario 与 Forecast 分离；
- Risk 与 Forecast 分离；
- Recommendation 不产生行动权；
- Action 和 Effect 进入血缘；
- 干预样本不能朴素判错；
- Risk 更新不覆盖原 Forecast；
- 评分器和预测生成器不能形成自证闭环；
- 单次结果不能证明整体校准。

## 十、验证结论

候选模型可以区分：

```text
未来判断是否合理
建议是否被采纳
行动是否执行
行动是否改变暴露
现实结果如何发生
预测是否仍可公平评分
```

场景证明 Action / Effect 虽尚未进入正式核心对象主干，但必须在产品、反馈和外部行动边界中拥有稳定位置。

本场景已在 S7、S9 和 S10 完成语义复核并采纳；外部 Action 的最终边界延期。

## 十一、关联

- [研判、预测、风险与建议模型](../04-domain-design/assessment-forecast-risk-recommendation-model.md)
- [SCN-0003：持续监测与风险告警](./SCN-0003-continuous-monitoring-alert.md)
- [SCN-0009：模型退化与能力回滚](./SCN-0009-model-degradation-and-rollback.md)
