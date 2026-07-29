# Claim、争议、Fact 与裁定语义模型

状态：Accepted

提出日期：2026-07-27

所属迭代：`v0002-intelligence-semantics`

## 一、模型目的

本文定义天机阁如何从“某个主体作出了一项陈述”走到“某个权威域接受了一项可使用事实”：

```text
Evidence
→ Claim
→ Verification
→ Fact Candidate
→ Acceptance / Dispute / Rejection
→ Fact
```

它必须阻止以下危险跳跃：

```text
来源写了
≠ 现实一定如此

模型抽取了
≠ Claim 已经成立

多个来源重复
≠ 多份独立证据

验证通过
≠ 对所有租户和用途都成为全局真理

某权威作出裁定
≠ 可以删除异议、反证和历史
```

本文不试图解决哲学上的绝对真理，而是建立可追溯、可争议、可修订的事实接受制度。

## 二、语义主链

```text
Snapshot / Evidence
        │
        ▼
Claim Candidate
        │ normalize / anchor / identify claimant
        ▼
Claim
        │
        ├── supporting Evidence
        ├── opposing Evidence
        ├── contextual Evidence
        └── related Claims
        │
        ▼
Verification Run
        │ produces
        ▼
Verification Record + Verification Package
        │
        ▼
Fact Candidate
        │
        ├── accept ─────▶ Fact
        ├── dispute ────▶ Dispute Case
        ├── return ─────▶ More Evidence / Clarification
        └── reject ─────▶ Rejected Claim View
```

`Fact Candidate` 是 Claim 在验证流程中的角色/视图，不是新的核心对象类型。

`Verification Run` 是验证任务或流程实例；`Verification Record` 是其不可变过程记录。

## 三、Claim：带主体的可验证陈述

### 3.1 正式定义

Claim 表示一个明确主体在明确上下文中，对现实世界、过去状态或可验证关系作出的陈述。

Claim 至少需要表达：

- claimant：谁作出陈述；
- proposition：陈述的规范化命题；
- original expression：原始表达；
- about：涉及的 Entity / Event / Relationship；
- asserted time：何时作出陈述；
- valid-time assertion：声称在何时成立；
- context：语境、条件、单位和定义；
- source lineage：从何处观察到该陈述；
- scope：公共、租户、联合或任务作用域；
- status：候选、验证中、争议、接受、拒绝等；
- version and provenance。

### 3.2 Claimant 与提取者必须分离

```text
公司公告说“甲已离任”

Claimant = 公司
Extractor = 解析 Tool / Agent
Recorder = 天机阁
```

模型或 Tool 把句子抽取出来，不意味着模型成为该 Claim 的 Claimant。

如果模型根据材料自行推断“甲可能已离任”，该对象应是 Assessment 或 Claim Candidate，不能冒充公司原始 Claim。

### 3.3 命题身份

多个 Claim 可能表达同一规范化命题：

```text
“甲在 7 月 1 日离任”
“A stepped down on July 1”
“甲已于本月首日卸任”
```

为了比较支持、重复和冲突，需要 `Proposition Identity`。

S3 候选规则：

- Proposition 是 Claim 的规范化语义核心；
- 可以拥有稳定引用键；
- 当前先作为 Claim 的可版本化组成，而不立即升格为全局核心对象；
- 若 S4 / S5 证明其具有独立身份、生命周期和跨面消费，再考虑升格。

### 3.4 Claim 类型

类型表达命题结构，不表达真假：

- 属性 Claim；
- 关系 Claim；
- 事件 Claim；
- 数值 Claim；
- 分类 Claim；
- 存在 Claim；
- 不存在 Claim；
- 因果 Claim；
- 引用/转述 Claim。

观点、预测和建议即使采用陈述句，也不能因为语法相似而塞入 Fact 主链。

### 3.5 否定与缺失

```text
“资料中没有发现 X”
≠ “X 不存在”
```

必须区分：

- `Absence of Evidence`：当前材料未提供支持；
- `Evidence of Absence`：观察机制足以支持“不存在”命题；
- `Negative Claim`：某主体明确主张 X 不存在；
- `Unknown`：系统无法判断。

## 四、Verification：验证不是一次布尔判断

### 4.1 验证目标

验证需要分别回答：

1. Claim 是否准确表达了 Claimant 的原意；
2. 命题涉及的 Entity 是否正确；
3. 时间、单位、辖域和定义是否一致；
4. Evidence 是否真实锚定且上下文充分；
5. 支持和反对 Evidence 是否独立；
6. 来源对该命题是否具有适用权威；
7. 是否存在反证、替代解释或未知条件；
8. 当前用途需要什么证据标准；
9. 验证主体是否具有相应权限和独立性。

### 4.2 Verification Record

每次验证产生不可变 Verification Record：

- target Claim and version；
- verification type；
- verifier identity and capability version；
- policy / standard version；
- Evidence set and relation versions；
- source independence analysis；
- entity / time / definition resolution；
- supporting, opposing and missing factors；
- outcome；
- reason codes and explanation；
- performed / recorded time；
- tenant / jurisdiction / purpose；
- trace and decision lineage。

后续验证追加新记录，不覆盖旧记录。

### 4.3 Verification Package

Verification Package 是用于审核或裁定的版本化装配视图，包含：

- Claim；
- Proposition；
- Evidence Relations；
- Verification Records；
- 冲突 Claim；
- 领域解释；
- 不确定性；
- 当前政策；
- 推荐处置。

它是 Package / View，不取得独立真值。

### 4.4 证据充分性不是单一分数

至少分别表达：

- 来源身份确定度；
- Evidence Anchor 完整性；
- 直接性；
- 来源独立性；
- 时效与有效时间匹配；
- 领域适用性；
- 反证覆盖；
- 实体解析质量；
- 定义和单位一致性；
- 许可与可使用性。

禁止用一个 `confidence=0.93` 同时代替上述所有判断。

## 五、Fact：作用域内被接受的事实记录

### 5.1 正式定义

Fact 表示：

> 某个明确权威域，依据指定事实接受标准，在特定作用域、辖域、用途和有效时间内，对某项 Proposition 作出的当前可使用接受记录。

因此：

```text
Fact ≠ Claim 的真假标签
Fact ≠ 全局永恒真理
Fact ≠ 数据库当前值
Fact ≠ 多数来源投票结果
Fact ≠ 高置信模型输出
```

### 5.2 Fact Acceptance Context

每个 Fact 必须携带或引用：

- accepting authority；
- acceptance domain；
- acceptance standard and version；
- accepted Proposition / Claim；
- supporting and opposing Evidence；
- Verification Records；
- purpose / allowed use；
- tenant / joint / public scope；
- jurisdiction；
- valid time；
- accepted time and recorded time；
- limitations and unresolved objections；
- current lifecycle state。

没有 Acceptance Context 的“Fact”只能是未限定的展示词，不能进入权威数据底座。

### 5.3 事实域

候选事实域：

```text
Public Baseline
→ 天机阁公共知识域接受的事实

Tenant Accepted
→ 某租户依据其合法资料和标准接受的事实

Joint Investigation
→ 联合空间按共同规则接受的事实

Mission Working
→ 为任务推进临时采用的工作事实

External Authority
→ 法律、监管、官方登记或其他外部权威的正式记录
```

`Mission Working` 必须显式标记临时性，不得在未经重新接受的情况下传播成公共 Fact。

### 5.4 外部权威记录与现实事实

外部权威对某事项具有制度权威，不意味着其记录描述了所有现实层面。

例如：

```text
工商登记仍显示法定代表人为甲
公司内部已决定由乙实际主持工作
```

两项可以同时成立，因为命题、有效时间和权威域不同。天机阁不能粗暴地让“更官方”覆盖“更现实”，也不能反过来用内部消息改写法定登记。

### 5.5 Fact 身份与版本

同一 Proposition 在不同 Acceptance Context 下可以形成不同 Fact。

同一 Fact 身份的后续复核可以产生新版本：

```text
Fact F1 v1 Active
→ challenged
→ F1 v2 Confirmed / Limited / Superseded / Retracted
```

何时创建新 Fact、何时创建同一 Fact 新版本，将在 S8 时间与版本专题终审。

## 六、冲突不一定是矛盾

两个 Claim 进入争议前，先执行冲突归因：

| 表面冲突 | 实际可能原因 |
|---|---|
| A 任职 / A 未任职 | 有效时间不同 |
| 营收 10 亿 / 12 亿 | 币种、口径或期间不同 |
| 公司属于 X / 属于 Y | 分类体系不同 |
| 人物甲 / 人物乙 | 实体误匹配 |
| 来源说法变化 | 来源修订或撤回 |
| 多个来源相同 | 同一上游稿件的转载 |
| 公共登记与内部事实不同 | 权威域和命题不同 |
| 未观察到事件 / 事件不存在 | 观察能力不足 |

只有在实体、命题、时间、定义、辖域和作用域都对齐后，才形成真正的 Logical Contradiction。

## 七、Dispute Case：争议治理对象

### 7.1 为什么需要独立案件

`Claim.status = Disputed` 只能说明某项 Claim 处于争议状态，无法承载：

- 多个 Claim / Fact；
- 多轮举证和反证；
- 领域意见；
- 回避与利益冲突；
- 审理主体；
- 审限与优先级；
- 裁定、上诉和重开；
- 下游冻结或限制策略。

因此 S3 候选引入 `Dispute Case`，将其定义为智能治理面的案件对象，而不是新的世界事实对象。

### 7.2 生命周期

```text
Detected
→ Triaged
→ Open
→ EvidenceGathering
→ UnderAdjudication
→ Resolved / Unresolved / Dismissed
→ Appealed / Reopened
→ Closed
```

### 7.3 可能裁定

- 两个 Claim 实际不冲突；
- 一项 Claim 被接受，另一项被拒绝；
- 多项 Claim 在不同作用域或时间下分别成立；
- 既有 Fact 被确认；
- 既有 Fact 被限制、替代或撤回；
- 证据不足，保留 Unresolved；
- 裁定主体无权限，移交外部权威或人类。

`Unresolved` 是合法终局，不是系统失败。

## 八、事实接受与裁定权

### 8.1 权力分离

| 角色 | 可以做什么 | 不可以做什么 |
|---|---|---|
| 数据认知面 | 形成 Claim、验证材料、Fact Candidate | 独自批准高影响 Fact |
| 领域能力面 | 解释领域定义、口径、来源适用性 | 因专业意见直接覆盖 Evidence |
| 大理寺 | 组织 Claim / Fact 冲突案件并形成裁定 Decision | 修改原始 Claim、Evidence |
| 门下省 | 审核任务和结果是否满足治理、证据和风险要求 | 取代所有领域事实判断 |
| 平台控制面 | 强制事实接受政策、权限和用途限制 | 用策略创造事实内容 |
| 数据底座面 | 保存 Fact、版本、案件、血缘和供应视图 | 因持久化而取得事实裁定权 |
| 有权人类/外部权威 | 对保留事项作最终采用或制度裁定 | 免除事实主张的证据与历史记录 |

### 8.2 风险分级路径

```text
低风险 + 明确规则 + 充分证据
→ 确定性验证与接受门

中风险 / 领域歧义
→ 领域 Agent + 规则 + 抽样复核

高影响 / 冲突 / 新型命题
→ 门下反证 + 大理寺案件 + 必要人类裁定
```

“低风险”可以减少 Agent Token，不可以取消 Anchor、作用域、验证记录、政策门和审计。

### 8.3 裁定 Decision

裁定是 Decision 对 Claim、Fact 或 Dispute Case 的作用，不应把 Decision 文本复制进 Fact 并失去身份。

```text
Decision --accepts--> Fact Candidate
Decision --rejects--> Claim
Decision --limits--> Fact
Decision --retracts--> Fact
Decision --resolves--> Dispute Case
```

## 九、多租户事实叠加

### 9.1 不采用单一全局真相槽

查询某命题时，系统可能看到：

```text
External Authority Fact
Public Baseline Fact
Tenant Accepted Fact
Joint Investigation Fact
Mission Working Fact
Disputed Claims
```

返回结果必须按当前主体、用途和权限合成视图，而不是把它们覆盖成一行。

### 9.2 合成原则

- 先过滤不可见 Evidence 和 Fact；
- 保留每个 Fact 的 Acceptance Context；
- 公共基线不能读取租户私有证据；
- 租户可以引用公共 Fact 并添加私有覆盖；
- 租户覆盖不能反向修改公共 Fact；
- 联合空间只使用明确贡献和授权的对象；
- 工作事实默认不跨 Mission 传播；
- 冲突必须显式呈现，不按“最近更新时间”静默覆盖。

### 9.3 查询视图

`Current Fact` 是在指定查询上下文中计算的视图：

```text
principal
+ tenant
+ purpose
+ jurisdiction
+ as-of time
+ valid time
+ policy version
→ visible and usable Fact set
```

它不是全局唯一对象。

## 十、Fact 生命周期与使用控制

### 10.1 生命周期

```text
Candidate
→ Accepted
→ Active
→ Challenged
→ Confirmed / Limited / Superseded / Retracted / Expired
```

`Disputed` 更适合 Claim / Case 的状态。Fact 被挑战后可以：

- 暂停高风险使用；
- 允许低风险使用但附警告；
- 继续作为历史时点事实；
- 被新版本替代；
- 被撤回。

使用策略必须与生命周期状态分离，避免一个状态同时表达知识判断和访问控制。

### 10.2 撤回与删除

```text
Retracted
≠ 物理删除
≠ 从未存在
≠ 原始 Claim 被抹除
```

Fact 撤回需要保留：

- 谁撤回；
- 依据；
- 生效范围和时间；
- 受影响对象；
- 通知和纠正状态；
- 历史查询可见性。

物理删除由数据生命周期和权利策略另行决定。

## 十一、纠正与影响传播

```text
新 Evidence / Claim / Source Correction
→ Challenge Fact
→ 创建 Verification Run 或 Dispute Case
→ Decision
→ Confirm / Limit / Supersede / Retract
→ 沿血缘定位：
   Entity / Relationship / Event / Change
   Assessment / Forecast / Risk / Recommendation
   Product / Alert / API Consumer
→ 按影响等级重评
→ 发布纠正或限制
```

不是所有下游对象都立即失效：

| 依赖类型 | 典型处置 |
|---|---|
| 唯一关键前提 | 立即失效或隔离 |
| 多证据之一 | 重新计算充分性 |
| 背景引用 | 标记并排队复核 |
| 历史叙述 | 保留当时所知并追加更正 |
| 不受影响 | 记录分析后维持 |

## 十二、Agent / Tool 分工

### Tool / Script 优先

- Claim 结构化和类型校验；
- Evidence Anchor 与 Claim 引用完整性；
- 时间、单位和定义的机械规范化；
- 重复 Claim 和已知冲突检测；
- 来源 lineage 与独立性图计算；
- Acceptance Policy 强制；
- 状态转移、版本和血缘记录；
- 依赖影响查询；
- 低风险规则验证。

### Agent / 人类按需

- 复杂命题规范化；
- 隐含条件、语用和转述解释；
- 实体、定义、口径和时态歧义；
- 反证寻找与替代解释；
- 领域权威适用性；
- 多项冲突 Claim 的综合；
- 高影响事实接受、限制和撤回建议；
- 无法规则化的争议裁定。

### 禁止路径

- 同一 Agent 生成 Claim、选择唯一证据、审核并写入高影响 Fact；
- 用模型自信度代替事实接受标准；
- 用来源数量代替来源独立性；
- 用“官方”标签跳过命题和时间对齐；
- 为节省 Token 把所有 Claim 自动升级为 Fact；
- 为得到唯一答案而删除 Unresolved。

## 十三、关键失败模式

| 失败 | 错误做法 | 正确处理 |
|---|---|---|
| Claimant 错配 | 把抽取模型当发言者 | 分离 Claimant / Extractor |
| 时间错配 | 将两个时期说法判冲突 | 对齐有效时间 |
| 定义错配 | 多数投票 | 先统一口径 |
| 重复转载 | 当独立佐证 | 追溯共同上游 |
| 无证据 | 推断“不存在” | 保持 Unknown |
| 外部权威滞后 | 覆盖内部现实 | 分开命题和事实域 |
| 租户私有证据 | 写入公共 Fact | 仅形成租户覆盖 |
| 事实受挑战 | 立即删除 | 限制使用并进入复核 |
| 高影响自动接受 | 单 Agent 决定 | 独立审核/案件/人类 |
| 无法裁定 | 编造折中答案 | 保留 Unresolved |

## 十四、S3 不变量候选

- Claimant、Extractor、Verifier、Acceptor 必须可区分；
- Claim 必须保留原始表达和规范化命题；
- Fact 必须具有 Acceptance Context；
- Fact 只在声明的作用域、用途、辖域和时间内成立；
- Public、Tenant、Joint、Mission Working Fact 不得静默覆盖；
- Verification Record 必须不可变且可重复追加；
- Verification Package 是视图，不是事实；
- 证据充分性不得压缩成一个无解释分数；
- `Absence of Evidence` 不得自动变成 `Evidence of Absence`；
- 冲突前必须先排除实体、时间、定义和作用域错配；
- Dispute Case 属于治理对象，不属于世界事实；
- 大理寺裁定不得改写 Claim 和 Evidence；
- 门下审核不得取代全部事实裁定；
- 数据底座保存 Fact 不产生事实接受权；
- 高影响事实不得由同一生成者完成自证闭环；
- Unresolved 是合法知识状态；
- Fact 撤回必须传播影响但不能抹除历史。

## 十五、S3 验收结果

候选层面通过：

- Claim、Verification Record、Fact 和 Dispute Case 的身份与职责可区分；
- Fact 被定义为作用域内接受记录，而不是全局真理；
- 公共、租户、联合、任务和外部权威事实获得并存位置；
- 门下、大理寺、领域能力、平台强制和人类裁定边界可表达；
- 事实接受可以按风险使用 Tool / Agent / 人类分级；
- 冲突、未知、限制、撤回和影响传播均有合法状态；
- `SCN-0011` 证明不同权威域的事实不必互相覆盖；
- 未发现需要修改 Skeleton v1.0 的一级缺口。

仍需后续验证：

- Proposition 是否升格为核心对象；
- Dispute Case 是否进入正式对象主干；
- Fact 与 Fact Acceptance 是否拆成两个对象；
- 跨事实域的默认优先级；
- 认识时间与接受时间的关系；
- Fact 身份跨作用域、时间和版本的判定；
- 完整纠正传播协议。

下一步：`S4 实体、身份、别名与消歧`。

## 十六、关联

- [Source、Document、Snapshot 与 Evidence 模型](./source-document-snapshot-evidence-model.md)
- [统一语言与对象分类法](../08-reference-models/unified-language-and-object-taxonomy.md)
- [核心对象主干](./core-object-spine.md)
- [SCN-0004：冲突证据与事实裁定](../05-scenarios-and-flows/SCN-0004-conflicting-evidence.md)
- [SCN-0008：事实撤回与产品更正](../05-scenarios-and-flows/SCN-0008-fact-retraction-and-correction.md)
- [SCN-0011：多权威域事实并存](../05-scenarios-and-flows/SCN-0011-multiple-authority-domain-facts.md)
