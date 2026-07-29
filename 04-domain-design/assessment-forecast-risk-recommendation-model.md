# Signal、Assessment、Forecast、Scenario、Risk 与 Recommendation 语义模型

状态：Accepted

提出日期：2026-07-27

所属迭代：`v0002-intelligence-semantics`

## 一、模型目的

本文细化天机阁“推势、卜算、警险”的语义主干：

```text
Fact / World State / Change
→ Signal
→ Assessment
→ Scenario / Forecast
→ Risk
→ Recommendation
```

它要保证：

- 检测到模式不等于已经理解局势；
- 当前判断不冒充事实；
- 情景不冒充预测；
- 预测能够到期、解析、评分和校准；
- 风险表达可能性、影响和暴露，而不是一个无解释红黄绿；
- 建议不自动变成决定或行动；
- 采取建议改变现实后，系统不会用被干预的结果给原预测错误打分；
- 同一模型不能通过选择自己的证据和评价规则完成自证。

## 二、研判对象主链不是单向流水线

```text
Facts / Relationships / Events / Changes
        │
        ▼
Signal ──▶ Investigation / Monitoring
        │
        ▼
Assessment
   ├──▶ Hypothesis / Alternative Explanation
   ├──▶ Scenario
   ├──▶ Forecast
   └──▶ New Mission
        │
        ▼
Risk
        │
        ▼
Recommendation
        │
        ├──▶ Decision
        ├──▶ No Action
        └──▶ More Evidence / Reassessment
```

任一阶段都可以反向要求补充资料、重新消歧、挑战 Fact 或改变监测目标。

## 三、Signal：值得进一步处理的模式

### 3.1 正式定义

Signal 表示从 Fact、Event、Change、指标、关系图或外部输入中检测到的、值得解释、验证、监测或响应的模式。

Signal 至少表达：

- detection target；
- detection rule / model / version；
- observed features；
- baseline；
- detection window；
- threshold and sensitivity；
- source objects；
- scope；
- detected time；
- novelty / strength；
- quality and missingness；
- current disposition。

### 3.2 Signal 不是什么

```text
Signal ≠ Fact
Signal ≠ Trend
Signal ≠ Assessment
Signal ≠ Forecast
Signal ≠ Risk
Signal ≠ Alert
```

例如“招聘数量连续三周上升”可以是 Signal；“公司正在扩张”是 Assessment；“未来季度收入增长概率为 65%”是 Forecast。

### 3.3 Signal 生命周期

```text
Detected
→ Qualified
→ Investigating / Monitoring
→ Explained / Confirmed / Dismissed / Stale
→ Reopened
```

Signal 被 Dismissed 不表示检测器无价值；可能只是本次模式不具有业务意义。

## 四、Assessment：面向当前的综合判断

### 4.1 正式定义

Assessment 表示在明确 `as-of` 时间、问题、事实域和假设下，对当前态势、含义、影响链或解释作出的版本化判断。

它至少包含：

- assessment question；
- conclusion；
- as-of / valid interval；
- accepted Facts and World State；
- Signals and Changes；
- assumptions；
- analytic method；
- supporting and opposing Evidence；
- alternative explanations；
- unknowns and information gaps；
- uncertainty dimensions；
- analyst / Agent / model versions；
- review and dissent；
- scope and intended use；
- invalidation conditions。

### 4.2 Assessment 不是什么

```text
Assessment ≠ Fact
Assessment ≠ Claimant 原话
Assessment ≠ 模型自由文本
Assessment ≠ Forecast
Assessment ≠ Recommendation
```

### 4.3 Hypothesis

Hypothesis 表示一个待验证的解释或机制候选。

S6 候选判断：

- Hypothesis 可以作为 Assessment 内的有身份组成；
- 它必须关联可证伪条件、支持/反对 Evidence 和替代解释；
- 当前不立即升格为全局核心对象；
- 若长期调查、多人协作和跨对象验证需要独立生命周期，再升格。

### 4.4 Dissent

高影响 Assessment 必须能够保留：

- 主判断；
- 少数意见；
- 不同假设；
- 不同模型族结果；
- 未解决证据冲突；
- 谁接受何种版本。

共识 View 不能删除分歧对象。

## 五、Scenario：条件化的可能世界

### 5.1 正式定义

Scenario 表示：

> 在一组明确前提、外部驱动、决策选择和演化规则下，构造的条件化可能世界或路径。

Scenario 至少表达：

- focal question；
- initial World State；
- assumptions；
- drivers and uncertainties；
- branch conditions；
- possible Events / Changes；
- horizon；
- internal consistency；
- participating Entities；
- consequences；
- relation to Forecast / Risk；
- scope and author；
- version。

### 5.2 Scenario 不是什么

```text
Scenario ≠ Forecast
Scenario ≠ Prediction
Scenario ≠ Plan
Scenario ≠ Recommendation
Scenario ≠ 必然发生的未来
```

Scenario 可以完全不分配概率，用于探索尾部、压力和对抗情况。

### 5.3 是否成为核心对象

S6 候选将 Scenario 提升为研判家族核心对象，因为它：

- 有独立身份和版本；
- 被 Forecast、Risk、Recommendation 和 Product 引用；
- 拥有假设、分支和时间范围；
- 可被审核、比较、失效和复用；
- 失败模式不同于 Assessment 和 Forecast。

## 六、Forecast：可解析、可评分的未来判断

### 6.1 正式定义

Forecast 表示对未来可观察目标在明确时间窗和条件下的概率分布、区间或有序判断。

Forecast 至少包含：

- forecast question；
- target variable / event；
- outcome space；
- origin time；
- valid horizon / resolution time；
- conditioning information cutoff；
- base rate / reference class；
- assumptions and Scenario；
- probability distribution / interval；
- model / Agent / analyst version；
- supporting Facts and Assessment；
- uncertainty decomposition；
- resolution criteria and source；
- review / dissent；
- intended use；
- supersession relation。

### 6.2 Forecast 不是什么

```text
Forecast ≠ Scenario
Forecast ≠ Risk
Forecast ≠ Target
Forecast ≠ Promise
Forecast ≠ Recommendation
Forecast ≠ 模型生成的一句“可能”
```

### 6.3 可解析性

Forecast 在创建时必须定义：

- 何种观察算发生；
- 何种观察算未发生；
- 谁拥有解析权；
- 数据迟到如何处理；
- 条件改变是否使问题无效；
- 外部干预如何标记；
- 何时判定 Ambiguous / Unresolvable。

没有解析规则的“预测”只能是开放式 Assessment。

### 6.4 生命周期

```text
Draft
→ Reviewed
→ Active
→ Updated / Superseded
→ Due
→ Resolved / Ambiguous / Invalidated / InterventionAffected
→ Scored
→ Archived
```

新 Forecast 不覆盖旧 Forecast；每个版本按其信息截点独立保留。

### 6.5 评分与校准

评分必须记录：

- outcome；
- resolution source；
- score method；
- reference class；
- forecast version；
- intervention status；
- late information；
- scorer identity；
- model / analyst cohort。

单个预测结果不能证明模型好坏。校准需要跨同类预测的长期统计，并区分选择偏差、幸存者偏差和问题难度。

## 七、不确定性模型

禁止一个 `confidence` 同时表示全部不确定性。至少区分：

- data uncertainty：资料缺失、噪声、迟到；
- identity uncertainty：Entity / Event 解析不确定；
- epistemic uncertainty：知识不足，可通过调查降低；
- aleatory uncertainty：现实随机性；
- model uncertainty：模型结构和参数；
- scenario uncertainty：未来路径和假设；
- measurement uncertainty：指标和观察误差；
- disagreement：分析主体分歧；
- unknown unknown exposure：未建模风险；
- resolution uncertainty：未来是否能够明确评分。

概率只适合部分对象和部分不确定性。

## 八、Risk：对目标的潜在损失结构

### 8.1 正式定义

Risk 表示某种 Hazard / Event / Change 在特定时间范围内，通过明确暴露和脆弱性，对指定目标造成不利结果的可能性与影响结构。

候选结构：

```text
Risk Subject / Objective
+ Hazard or adverse Scenario
+ Exposure
+ Vulnerability
+ Likelihood / plausibility
+ Impact dimensions
+ Time horizon
+ Trigger / leading indicators
+ Existing controls
+ Residual uncertainty
= Risk
```

### 8.2 Risk 不是什么

```text
Risk ≠ Forecast
Risk ≠ Alert
Risk ≠ Incident
Risk ≠ Task Risk Tier
Risk ≠ 单一 probability × impact 数字
Risk ≠ Recommendation
```

### 8.3 Intelligence Risk 与 Governance Risk Context

必须使用限定术语：

- `Intelligence Risk`：外部世界、业务或研究对象面临的潜在损失；
- `Mission Risk Tier`：执行某项任务所需的治理与审核等级；
- `Operational Risk`：天机阁自身运行失败的风险；
- `Security / Compliance Risk`：权限、隐私和制度风险。

研究一个高危地缘事件不必然意味着查询操作高权限；处理普通人物信息也可能因隐私和外部行动而拥有高 Mission Risk Tier。

### 8.4 影响维度

至少支持：

- financial；
- operational；
- legal / compliance；
- security；
- human / safety；
- reputation；
- strategic；
- rights / privacy；
- systemic / contagion；
- reversibility；
- duration and distribution。

不能把不同维度强行压成一个不可解释总分。

### 8.5 Risk 生命周期

```text
Identified
→ Assessed
→ Monitoring
→ Escalated / Deescalated
→ Materialized / Mitigated / Expired
→ Closed / Reopened
```

`Materialized` 产生 Incident / Event，但 Risk 历史继续保留用于校准。

## 九、Recommendation：建议选择

### 9.1 正式定义

Recommendation 表示基于 Fact、Assessment、Forecast、Scenario 和 Risk，对有权主体提出的可选择行动、等待、调查或不行动建议。

Recommendation 至少表达：

- decision context；
- addressee；
- objective；
- recommended option；
- considered alternatives；
- expected benefits；
- costs and resource needs；
- risks and side effects；
- assumptions；
- dependencies；
- reversibility；
- evidence and analysis lineage；
- urgency / decision deadline；
- authority required；
- monitoring and stop conditions；
- dissent。

### 9.2 Recommendation 不是什么

```text
Recommendation ≠ Decision
Recommendation ≠ Authorization
Recommendation ≠ Command
Recommendation ≠ Action
Recommendation ≠ Guaranteed Outcome
```

“建议不行动”仍是 Recommendation；“信息不足，先调查”也可以是合法建议。

### 9.3 选择架构

```text
Recommendation
→ 有权主体审议
→ Decision
→ Authorization
→ Action / No Action
→ Effect
→ Feedback
```

建议生成者不能因建议被采用而自动获得执行权。

## 十、因果、反事实与干预

### 10.1 因果判断

明示来源的因果陈述可以成为 Causal Claim；跨资料综合得到的因果结论通常属于 Assessment。

因果对象必须表达：

- cause / effect；
- mechanism；
- temporal ordering；
- confounders；
- alternative explanations；
- counterfactual；
- Evidence；
- uncertainty；
- scope。

### 10.2 干预改变预测评价

如果 Forecast 或 Risk 导致 Recommendation 被采纳，并改变了现实：

```text
Forecast
→ Recommendation
→ Decision / Action
→ Effect
→ Outcome
```

最终结果不再是“无干预世界”的直接观察。Forecast Resolution 必须标记：

- InterventionAffected；
- intervention time；
- action and scope；
- expected causal pathway；
- whether target definition conditions on intervention；
- whether scoring remains valid。

不能因为预警成功避免事故，就把风险预测记为“错误”。

### 10.3 自我实现

情报发布也可能使预测自我实现，例如市场参与者看到报告后共同采取行动。产品、分发、行动和效果必须进入因果血缘。

## 十一、多租户与风险偏好

- 公共 Fact 可以支持不同租户 Assessment；
- Assessment、Forecast、Scenario、Risk、Recommendation 默认属于创建它们的作用域；
- 租户的目标、风险承受、时间窗和资源会产生不同 Recommendation；
- 不同建议不意味着底层 Fact 冲突；
- 平台不能用一个租户的风险偏好重写公共 Assessment；
- 联合空间必须声明共享假设、预测和风险是否可带出；
- Forecast 校准可以聚合匿名统计，但不能泄漏租户问题或策略；
- 租户可以提高审核门槛，不能关闭平台强制安全和权限控制。

## 十二、认知独立性与反自证

### 12.1 危险闭环

```text
同一模型形成 Assessment
→ 选择支持 Evidence
→ 生成 Forecast
→ 自己审核
→ 自己定义解析结果
→ 宣布自己准确
```

### 12.2 分级独立性

```text
低风险 / 可逆 / 已知模式
→ Tool + 单路径分析 + 抽样复核

中风险 / 开放式判断
→ 反证搜索 + 替代解释 + 来源独立性

高风险 / 不可逆 / 跨主权
→ 不共享失败根的模型/Agent/人类
→ 门下审核
→ 明确保留 Dissent
```

独立性按风险购买，不按 Agent 数量表演。

### 12.3 评价独立性

- Forecast 解析来源在创建时确定；
- 评分器版本独立记录；
- 生产模型不能静默修改历史问题；
- 能力演进读取结果，但不能删掉不利样本；
- 发布后的现实干预必须进入评分上下文；
- 评测集与生产检索污染需要检测。

## 十三、权威与组织分工

| 对象/行为 | 主要责任 | 边界 |
|---|---|---|
| Signal 检测 | Tool + 数据认知 / 研判 | 不自动成为 Risk |
| Assessment / Forecast / Scenario | 情报研判面 + 领域能力 | 不改写 Fact |
| Intelligence Risk | 情报研判面，刑/兵/户等提供领域语义 | 不等于 Mission Risk Tier |
| Recommendation | 情报研判面 | 不拥有采用和执行权 |
| 高影响审核 | 门下省 | 不代替领域模型生产 |
| 采用 Decision | 皇帝、仙人或其他有权主体 | 不改写原分析历史 |
| 解析、评分、校准 | Tool + 独立评测能力 | 不由预测生成器单独垄断 |
| 版本和血缘 | 数据底座面 | 不取得研判权 |
| 阈值与治理策略 | 平台控制面 | 不创造外部风险 |

## 十四、Agent / Tool 分工

### Tool / Script 优先

- 指标和模式检测；
- 时间序列、统计和基准计算；
- 规则化风险识别；
- Forecast 到期、解析和评分；
- 概率校准和 cohort 统计；
- 情景树一致性检查；
- 血缘、版本和依赖追踪；
- 阈值、订阅和监测；
- 已知风险模型；
- 低风险模板化 Assessment。

### Agent / 人类按需

- 弱 Signal 的现实含义；
- 跨域态势和影响链；
- 替代解释与反证；
- 新型 Scenario；
- Forecast 问题定义与参考类；
- 尾部 Risk 和系统性外溢；
- Recommendation 权衡；
- 因果和反事实；
- 高影响 Dissent；
- 无法形式化的不确定性。

### Token 经济性

Tool 完成高吞吐检测、计算、到期和评分；Agent 只处理语义、假设、冲突、跨域和高影响判断。缓存可复用 Fact、Signal 和已审核 Assessment，但新时间窗、租户目标或事实版本必须重判适用性。

## 十五、关键失败模式

| 失败 | 错误做法 | 正确处理 |
|---|---|---|
| 检测阈值越过 | 直接发 Risk | 先 Signal / Assessment |
| 当前判断 | 写成 Fact | Assessment |
| 情景故事 | 填伪概率 | Scenario |
| 模糊预测 | 永不评分 | 改为 Assessment 或定义解析规则 |
| 单次命中 | 宣布模型可靠 | cohort 校准 |
| 风险高 | 自动外部行动 | Recommendation → Decision |
| 建议被采纳 | 建议者获得执行权 | 独立授权 |
| 干预避免事故 | 预测记错 | InterventionAffected |
| 多租户不同建议 | 当事实冲突 | 保留目标/偏好差异 |
| 模型自评 | 删除不利样本 | 独立解析与审计 |

## 十六、S6 不变量候选

- Signal、Assessment、Scenario、Forecast、Risk、Recommendation 必须类型分离；
- 每个 Assessment 必须区分 Fact、假设、方法、反证和未知；
- Scenario 可以无概率，Forecast 必须有可解析目标和时间；
- Forecast 必须保留信息截点、模型版本和解析规则；
- Forecast 更新不得覆盖旧版本；
- 不确定性不得压缩成无解释的单一 confidence；
- Intelligence Risk 与 Mission Risk Tier 必须分离；
- Risk 必须声明目标、Hazard、Exposure、Impact 和时间；
- Risk 与 Alert 不得合并；
- Recommendation 不产生 Decision、Authorization 或 Action；
- 因果不得由时间先后或相关性自动生成；
- 现实干预必须进入 Forecast Resolution 与校准；
- Dissent 和替代解释不得被共识 View 删除；
- 高影响研判必须购买与风险相称的认知独立性；
- 预测生成者不得垄断解析、评分和历史样本；
- 租户事实、目标和风险偏好不得污染公共研判；
- 研判对象必须可回溯到 Fact、World State、模型和版本。

## 十七、S6 验收结果

候选层面通过：

- 六类研判对象形成清晰边界；
- Scenario 满足核心对象准入条件；
- Hypothesis 暂作为 Assessment 的有身份组成；
- Forecast 的解析、评分、校准和干预状态获得外层位置；
- Intelligence Risk 与治理/运行风险语义分离；
- Recommendation 到 Decision / Action 的权力断点明确；
- 不确定性、Dissent、因果和反自证机制存在；
- Agent / Tool 分级兼顾 Token 与功能；
- `SCN-0014` 证明成功干预后的预测不能被朴素判错；
- 未发现需要修改 Skeleton v1.0 的一级缺口。

仍需后续验证：

- Hypothesis 是否升格；
- Scenario 与 Forecast 的组合/继承方式；
- Intelligence Risk 是否允许机会型正向对象；
- Forecast Resolution 和 Score 是否独立对象；
- Intervention / Action / Effect 的正式对象主干；
- 风险偏好与 Mission Risk Tier 的策略连接；
- S7 产品、告警和反馈如何保留这些语义。

下一步：`S7 产品、告警、反馈与影响纠正`。

## 十八、关联

- [世界状态、事件与变化模型](./world-state-event-and-change-model.md)
- [Claim、争议、Fact 与裁定模型](./claim-fact-and-adjudication-model.md)
- [统一语言与对象分类法](../08-reference-models/unified-language-and-object-taxonomy.md)
- [SCN-0014：预测干预与风险重校准](../05-scenarios-and-flows/SCN-0014-forecast-intervention-and-recalibration.md)
