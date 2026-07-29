# SCN-0015：部分事实撤回、影响裁剪与接收者纠正

状态：Accepted

所属专题：`v0002-intelligence-semantics / S7`

## 一、验证目标

一个租户已发布的供应商风险报告使用了十七项 Fact。其中：

- `Fact F-07 v3` 是“供应商核心工厂已通过某强制认证”；
- 它既是“短期停产风险较低”的关键前提；
- 也在公司历史章节中作为某次公开声明的引用；
- 同一报告的财务风险判断不依赖该 Fact；
- 报告已通过 PDF、仪表板和 API 分发给不同接收者；
- 部分用户下载过旧版，部分尚未送达，另有一个 API 消费者已产生二次派生。

认证机构随后撤回认证记录，并说明旧记录由数据录入错误造成。

本场景验证：

1. 上游 Fact 撤回不会让所有引用它的内容无差别失效；
2. 依赖类型能区分关键前提、历史 Claim 和背景引用；
3. 影响传播能够局部停止，同时保留停止理由；
4. 已发布产品的内容修复与接收者纠正分别闭环；
5. 不可控外部复制被记录为剩余风险，而非虚假宣称全部召回；
6. 多租户共享 Fact 的撤回不会泄露各租户私有产品；
7. 反馈和后续行动不会被直接当成 Fact 或因果效果。

## 二、参与者与对象

```text
仙人 / 租户产品所有者
皇帝 / 统一解释与状态呈现
数据认知面 / Fact 状态与变更通知
情报研判面 / Assessment、Forecast、Risk 重评
情报产品面 / Edition、Release、Alert 与纠正
智能治理面 / 更正、限制和撤回决定
人机交互面 / 通知、确认和用户 Feedback
数据底座面 / 版本依赖图与影响查询
运行面 / 重算、重渲染和通知 Task
平台控制面 / 权限、许可、渠道和时限策略
门下 / 高影响纠正复核
API 消费者 / 外部受控下游
```

关键对象：

```text
Fact F-07 v3                 Active → Retracted
Product P-01                 供应商持续风险档案
Product Edition PE-12        已发布旧版
Product Release PR-12-I      内部完整版
Product Release PR-12-C      客户裁剪版
Assessment A-21 v4           premise(F-07 v3)
Assessment A-30 v2           财务风险，不依赖 F-07
Alert AL-09                  低停产风险监测通知
Impact Case IC-15
Product Edition PE-13        更正版
Correction Notices[]
```

## 三、前置依赖图

```text
F-07 v3 --premise--------▶ A-21 v4
F-07 v3 --quoted_as_claim▶ PE-12 / 历史声明章节
F-07 v3 --context--------▶ PE-12 / 公司概览
A-21 v4 --summarized_by--▶ PE-12 / 运营风险章节
A-30 v2 --summarized_by--▶ PE-12 / 财务风险章节
PE-12   --published_as---▶ PR-12-I / PR-12-C
PR-12-I --delivered_via--▶ PDF recipients
PR-12-C --delivered_via--▶ Dashboard / API consumers
```

每条边都固定上游版本、章节、作用域和审核状态。

## 四、主路径

### 4.1 上游纠正

```text
认证机构发布更正
→ 新 Snapshot 与纠正 Evidence
→ 新 Claim：旧认证记录由录入错误造成
→ 验证与事实域裁定
→ F-07 v3: Active → Challenged → Retracted
→ 保留原版本、撤回原因和权威决定
→ 产生作用域受控的 Change Notice CN-15
```

Change Notice 不携带其他租户产品信息。

### 4.2 创建影响案件

```text
CN-15
→ 数据底座按精确版本查询反向依赖
→ 创建 IC-15
→ 冻结依赖 F-07 的高风险新发布
→ 记录查询快照，防止扫描期间图继续变化造成遗漏
```

确定性查询先返回候选消费者，不直接宣布所有消费者失效。

### 4.3 影响分类

| 下游 | 依赖语义 | 分类 | 理由 |
|---|---|---|---|
| A-21 v4 | `premise` | `Invalidated` | 关键前提被撤回，无等价替代证据 |
| A-30 v2 | 无依赖 | `Unaffected` | 财务判断使用独立事实链 |
| 历史声明章节 | `quoted_as_claim` | `PresentationOnly` | “公司曾声称已认证”仍是可证历史 Claim，但必须补充其后被撤回 |
| 公司概览 | `context` | `CorrectionRequired` | 当前认证状态表述错误 |
| AL-09 | 由 A-21 派生 | `Retracted` | 通知依据不再成立 |
| PE-12 运营风险章节 | `summarizes A-21` | `CorrectionRequired` | 实质结论错误 |
| PE-12 财务风险章节 | 仅 A-30 | `Unaffected` | 保留原结论与证据 |

每个 `Unaffected` 都保存机器规则或审核者的停止理由。

### 4.4 局部重评

```text
运行面只重算受影响分支
→ 情报研判面形成 A-21 v5
   “认证状态未知；短期停产风险由低调整为中高”
→ 更新 Risk 与不确定性
→ 门下审查前提、替代证据和措辞
→ 智能治理面批准更正并限制旧版继续分发
```

系统不重新生成无关财务分析，也不为节省工作而静默复制旧结论。

### 4.5 产品更正

```text
PE-13 correction_of PE-12
├── 修改：认证状态
├── 修改：运营风险 Assessment
├── 修改：Risk 与 Recommendation
├── 注记：历史声明后来被撤回
└── 不变：财务风险章节

PR-12-I / PR-12-C → WithdrawnForCorrection
新建 PR-13-I / PR-13-C
```

PE-12 保持可审计，不被覆盖。默认界面指向 PE-13。

### 4.6 接收者纠正

分发账本发现：

| 接收者 | 旧版暴露 | 纠正动作 |
|---|---|---|
| 董事会成员甲 | PDF 已送达并确认 | 发送高优先更正，要求确认 |
| 风险经理乙 | PDF 已送达，未确认 | 多渠道通知并升级 |
| 分析师丙 | 邮件仍在队列 | 取消旧版，只发送新版 |
| 仪表板用户 | 曾查看旧版 | 登录时显著显示版本纠正 |
| API Consumer X | 拉取旧版并确认二次派生 | 发送机器可读撤回事件，等待下游 closure |
| 下载后外部转发 | 无法确定全部接收者 | 记录不可控传播与尽力通知 |

纠正通知明确说明：

- 旧版哪项结论错误；
- 为什么错误；
- 哪些章节仍有效；
- 当前风险等级如何变化；
- 是否需要暂停基于旧版的行动；
- 新版与机器可读变更位置。

### 4.7 Alert 与用户行动

风险上调产生新的 Alert 候选，但不会复用已撤回 `AL-09`：

```text
Risk R-22 v2
→ 新 Alert AL-10
→ 去重和责任路由
→ 发送给供应链责任人
→ Acknowledged
```

责任人随后暂停订单：

```text
Acknowledgement ≠ Decision
Decision D-31: 暂停新订单
Action ACT-08: 采购系统执行冻结
Outcome Observation: 暴露下降
Effect Assessment: 该行动可能降低短期损失
```

系统不把 `AL-10 Delivered` 当成风险已缓解，也不把订单冻结自动归因于天机阁。

## 五、Feedback 路径

董事会成员甲回复：“我们另有一份尚未公开的新认证函。”

正确路径：

```text
Feedback FB-19
→ target = PE-13 / certification section
→ 提取 Claim
→ 请求原始函件和使用授权
→ 建立 Snapshot / Evidence
→ 验证签发主体、有效时间和适用工厂
→ 新 Fact 或保持 Unresolved
→ 必要时重开 IC-15
```

错误路径：

```text
高权重用户 Feedback
→ 直接恢复 F-07
→ 自动撤销更正
```

## 六、多租户路径

公共 `F-07 v3` 同时被租户 B 引用。

```text
公共 Change Notice
→ 租户 A 私有 IC-15
→ 租户 B 私有 IC-B-04
```

租户 A 只知道自己的影响对象。平台可以知道“存在两个需路由的租户作用域”，但不得向任一租户披露另一个租户的产品、受众或调查目标。

租户 B 若只把 F-07 当作历史 Claim，则可以判定其产品无需实质更正；不同租户结果不构成冲突。

## 七、异常分支

### 7.1 依赖边缺失

PE-12 某图表显示“已认证”，但 Manifest 没有引用 F-07：

```text
血缘完整性检查发现孤立表达
→ 隔离该章节
→ 创建补链与质量事件
→ 保守纳入 CorrectionRequired
→ 形成 Evolution Evidence Candidate
```

不得因图上查不到边就断言无影响。

### 7.2 更正通知无法送达

```text
DeliveryFailed
→ 重试备用授权渠道
→ 超过产品纠正时限
→ 人工升级
→ 记录未闭合接收者和剩余风险
```

Impact Case 保持 `ClosurePending`。

### 7.3 API 下游不支持撤回协议

停止继续提供旧版，并：

- 提供机器可读 tombstone / supersession；
- 通知合同责任主体；
- 标记不可验证的二次派生；
- 记录外部主权边界；
- 必要时暂停该消费者后续访问。

### 7.4 撤回决定后来被推翻

不恢复 PE-12 为当前版本：

```text
新 Evidence / 新裁定
→ 新 Fact 版本
→ 重开 IC-15
→ 形成 PE-14 Update
```

历史上的撤回和更正仍然成立于当时知识时间。

### 7.5 影响扫描期间出现新依赖

Impact Case 固定发现快照，同时订阅后续新增依赖：

- 扫描前存在的依赖按快照处理；
- 扫描期间新增但引用已撤回版本的写入被阻止或即时纳入；
- 不允许新 Product Release 引用 Retracted Fact。

## 八、Agent 与 Tool 分工

### Tool / Service

- 反向依赖图查询；
- 版本与作用域裁剪；
- 已撤回版本发布门；
- 分发暴露查询；
- 无关分支规则停止；
- 确定性重算和重渲染；
- 新旧 Edition Diff；
- 通知扇出、重试和确认；
- 时限、状态和审计验证。

### Agent / Human

- 判断 F-07 对 A-21 是否是必要前提；
- 评估替代证据是否足够；
- 区分历史 Claim 与当前 Fact 的叙述；
- 重写运营风险和 Recommendation；
- 审核更正严重度、措辞和行动含义；
- 处理未知外部传播的剩余风险；
- 判断用户新函件是否值得重开案件。

Agent 只接收已裁剪的 Impact Package，不遍历整张跨租户图。

## 九、审计证据

场景完成后至少存在：

- 来源更正 Snapshot、Evidence、Claim 和 Fact 撤回决定；
- Change Notice 与作用域路由记录；
- IC-15 的发现快照；
- 每个依赖分支的类型、分类和停止理由；
- A-21 v4 到 v5 的重评记录；
- PE-12 与 PE-13 的内容及依赖 Manifest；
- 发布、更正、限制和撤回 Decision；
- Distribution、Delivery、Receipt 和 Correction Notice；
- API 下游确认或未闭合状态；
- Feedback FB-19 及其验证路径；
- Decision、Action、Outcome Observation 和 Effect Assessment；
- 不可控复制、未知消费者和未送达通知的剩余风险。

## 十、验证结论

候选通过。

该场景证明：

- “沿血缘传播”必须升级为有类型、固定版本的语义依赖分析；
- 纠正不是全库盲目级联，也不是只改当前报告；
- Product Edition、Product Release 和 Impact Case 具有独立生命周期；
- 内容纠正、发布控制、分发暴露和接收者闭环是四个不同问题；
- Feedback 只能启动验证，不取得 Fact 改写权；
- 产品和 Alert 的现实影响必须进入 Decision / Action / Effect 血缘；
- 外部不可控传播只能形成剩余风险，不能被架构虚构为完全召回。

本场景不改变 Skeleton v1.0，只深化情报产品面、数据底座面、智能治理面和纠正横切语义。

## 十一、验证的不变量

- 精确版本依赖；
- 非破坏性纠正；
- 影响传播逐分支留痕；
- 生成权、审核权、发布权和分发执行权分离；
- 送达、确认、决定和行动分离；
- 反馈不自动成为事实；
- 风险与告警生命周期分离；
- 跨租户影响路由不泄露私有依赖；
- 不可控边界显式记录；
- Impact Case 不在接收者义务未处理时虚假关闭。

## 十二、关联

- [Intelligence Product、Alert、Feedback 与影响纠正语义模型](../04-domain-design/intelligence-product-alert-feedback-correction-model.md)
- [事实撤回与产品更正](./SCN-0008-fact-retraction-and-correction.md)
- [Claim、争议、Fact 与裁定语义模型](../04-domain-design/claim-fact-and-adjudication-model.md)
- [研判、预测、风险与建议语义模型](../04-domain-design/assessment-forecast-risk-recommendation-model.md)
- [情报产品面契约](../03-current-architecture/plane-contracts.md)
