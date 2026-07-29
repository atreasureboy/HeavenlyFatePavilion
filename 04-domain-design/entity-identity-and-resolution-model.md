# Entity、身份、别名与消歧语义模型

状态：Accepted

提出日期：2026-07-27

所属迭代：`v0002-intelligence-semantics`

## 一、模型目的

本文定义天机阁怎样回答：

> 不同资料中提到的“这个人、这家公司、这个项目”，究竟是不是同一个现实主体？

它覆盖：

- Entity 与现实对象、名字和来源记录的关系；
- Mention、Alias、Identifier 和 Identity Claim；
- 候选匹配、身份接受、争议、合并与拆分；
- 人物、组织、项目、地点等不同实体类别；
- 时间、辖域、租户和隐私对身份的影响；
- 误合并如何撤销并传播纠正；
- Agent、Tool、吏部、大理寺和数据底座怎样分权。

本文不选择图库、搜索引擎或向量算法，也不定义具体字段 Schema。

## 二、不要把四种东西叫成一个 Entity

```text
现实指称对象 Real-world Referent
→ 系统试图认识的外部人、组织、项目或概念

Entity Record
→ 天机阁对该指称对象建立的稳定、可版本化身份记录

Source-local Record
→ 某个来源自己的人员、企业或项目记录

Mention
→ 某份材料中一次具体的名字、代词、编号或描述性指称
```

关系是：

```text
Mention / Source-local Record
        │ refers_to? / identifies?
        ▼
Identity Resolution
        │ accepts / disputes / rejects
        ▼
Entity Record
        │ intends to represent
        ▼
Real-world Referent
```

天机阁不能直接拥有现实对象，只能维护对它的可纠正认识。

## 三、Entity：稳定身份记录

### 3.1 正式定义

Entity 表示天机阁在明确作用域和身份接受规则下，为持续识别某个现实或概念指称对象而建立的稳定记录。

Entity 至少需要表达：

- entity identity；
- entity kind and ontology version；
- current lifecycle；
- identity acceptance scope；
- accepted identifiers and aliases；
- supporting / opposing Identity Claims；
- resolution decisions；
- valid-time and system-time；
- merge / split / succession history；
- provenance and authority；
- visibility and sensitivity。

### 3.2 Entity 不是什么

```text
Entity ≠ 名字字符串
Entity ≠ 数据库一行
Entity ≠ 某个 Source-local Record
Entity ≠ 一组相似向量
Entity ≠ 搜索结果
Entity ≠ “same_as” 永久真理
Entity ≠ IAM Principal
```

IAM Principal 表示能够在系统中行动的身份；情报 Entity 表示被研究或描述的对象。一个人可能同时具有两种身份，但二者不能共用同一权威模型。

### 3.3 Entity Kind

共享上位类型候选：

- Person；
- Organization；
- Project / Initiative；
- Product / Asset；
- Location；
- Legal / Policy Instrument；
- Topic / Concept；
- Infrastructure / System；
- Other Domain Entity。

领域能力可以扩展类型和属性，但不能绕开共享身份、版本、作用域和纠正语义。

### 3.4 Entity Existence 与 Entity Identity

必须分开：

```text
“存在这样一个人”
≠
“资料 A 和资料 B 指的是这个人”
```

前者是存在 Claim / Fact；后者是 Identity Claim / Resolution Decision。系统可能接受对象存在，但尚无法判断两个 Mention 是否同一。

## 四、Mention：材料中的一次指称

### 4.1 正式定义

Mention 表示 Snapshot、Evidence、Artifact 或结构化记录中，一次可定位的实体指称。

示例：

- 新闻里的“王伟”；
- 表格中某行企业名称；
- “该公司”“其首席执行官”等代词；
- 一张图片中的人脸区域；
- 视频中某一说话人；
- API 记录中的 company_id。

### 4.2 Mention 必须保留

- material anchor；
- surface form；
- surrounding context；
- mention type；
- source-local identifier；
- language / script；
- asserted role；
- time and location context；
- extraction method and version；
- candidate Entity set；
- resolution status。

### 4.3 Mention 是否是核心对象

S4 候选判断：

- Mention 具有独立 Anchor、候选集合、解析状态和纠正需求；
- 它被抽取、消歧、图谱、Claim 和审计多方消费；
- 因此 Mention 应成为数据认知域的一等认识对象；
- 它不是世界状态 Entity，而是材料到 Entity 的桥接对象。

该判断需要在 S5 关系与事件抽取中继续验证。

## 五、Alias、Name 与 Identifier

### 5.1 Name

Name 是 Entity 在某语言、文字系统、时间和语境下使用或被称呼的名称。

Name 需要区分：

- official；
- legal；
- preferred；
- historical；
- transliterated；
- translated；
- abbreviated；
- nickname；
- pseudonym；
- source-reported。

### 5.2 Alias

Alias 是“某个名称或描述在给定上下文中指向某 Entity”的身份关系，不是简单字符串数组。

Alias 至少具有：

- alias value；
- language / script；
- alias type；
- valid time；
- jurisdiction；
- source and Evidence；
- scope；
- acceptance status。

同一个字符串可以同时是多个 Entity 的 Alias；同一 Entity 也可以没有全局唯一名称。

### 5.3 Identifier

Identifier 是某个签发域为对象分配的标识：

```text
issuer
+ identifier namespace
+ identifier value
+ valid time
```

必须三者共同确定含义。单独的号码不能被视为全局唯一。

Identifier 类型包括：

- 政府或监管标识；
- 企业内部员工号；
- 证券、产品或项目编号；
- 平台账号；
- 来源局部主键；
- 设备和系统标识。

### 5.4 标识权威有限

一个标识只能证明其签发域中的身份关系：

- 员工号不能证明公开人物身份；
- 社媒 handle 不一定由本人控制；
- 公司注册号可以识别法律实体，但不能识别品牌、集团或业务部门；
- 同一标识可能被回收或复用；
- 身份证件也可能伪造、过期或受权限限制。

## 六、Identity Claim：身份主张

### 6.1 正式定义

Identity Claim 表达：

> 某个 Mention、Source-local Record、Identifier 或 Entity 与另一个 Entity 指向同一、不同、继承或关联的现实对象。

候选关系：

```text
same_as
different_from
possibly_same_as
represents
member_of
successor_of
predecessor_of
renamed_from
split_from
merged_into
operated_by
```

只有 `same_as / different_from` 直接参与同一性；组织关系、继承和控制不能偷换成同一性。

### 6.2 Identity Claim 仍是 Claim

它必须具备：

- Claimant；
- Proposition；
- Evidence；
- valid time；
- scope；
- Verification Record；
- Acceptance Context。

不能因为由消歧模型产生就跳过 S3 事实接受模型。

### 6.3 负身份约束

`different_from` 不是次要标签。高质量负约束可以阻止灾难性误合并：

- 同时出现在不同地点；
- 不同法定标识；
- 出生日期冲突；
- 组织注册主体不同；
- 生命周期不可能重叠；
- 明确的来源声明；
- 人类裁定。

## 七、Identity Resolution：身份解析

### 7.1 解析不是一次相似度计算

身份解析包含：

```text
Candidate Generation
→ Feature / Evidence Collection
→ Hard Constraint Check
→ Contextual Comparison
→ Identity Claim
→ Verification
→ Resolution Decision
→ Entity Link / New Entity / Dispute
```

### 7.2 候选生成

Tool 可以根据：

- 名称和别名；
- 标识；
- 联系方式；
- 组织、角色和项目；
- 时间与地点；
- 关系邻域；
- 文本、图像和多模态特征；
- 来源局部映射；
- 历史解析结果；

生成候选集。

候选排序只决定“先验证谁”，不决定“就是谁”。

### 7.3 解析证据维度

禁止一个无解释 `identity_confidence` 吞并全部信息。至少分开：

- identifier agreement；
- name similarity；
- temporal compatibility；
- geographic compatibility；
- role / organization compatibility；
- relationship-neighborhood compatibility；
- biometric or media match；
- source independence；
- negative evidence；
- ontology compatibility；
- data freshness；
- model / rule provenance。

### 7.4 硬约束与软证据

```text
Hard Constraint
→ 一旦可信成立，阻止自动合并

Soft Evidence
→ 增加或降低候选可能性
```

硬约束必须按实体类型和辖域配置。例如同一姓名、同一雇主和相似履历仍不能覆盖可信出生日期冲突。

### 7.5 解析结果

```text
Unresolved
Candidate
Provisional Link
Accepted Link
Rejected Link
Disputed
Split Required
```

`Provisional Link` 可以支持低风险探索，但不能进入高影响事实、产品或外部行动。

## 八、Resolution Decision 与 Entity Resolution Case

### 8.1 Resolution Decision

Resolution Decision 是有权主体对 Identity Claim 的结构化决定：

- link；
- reject；
- keep separate；
- create new Entity；
- merge view；
- split；
- defer；
- escalate。

决定必须引用规则、Evidence、模型/Agent、领域意见、作用域和风险等级。

### 8.2 Entity Resolution Case

复杂身份争议需要独立案件对象，承载：

- 多个 Mention / Record / Entity；
- competing Identity Claims；
- 正负 Evidence；
- 候选集变化；
- 冲突与回避；
- 领域意见；
- 决定、上诉、重开；
- 下游影响范围。

它属于智能治理对象，可由吏部提供领域意见，由大理寺式能力处理争议；数据认知面拥有日常解析能力。

## 九、不要做破坏性实体合并

### 9.1 错误模型

```text
Entity A + Entity B
→ 删除 B
→ 把所有记录改写为 A
```

这种做法会丢失：

- 原始来源如何指称；
- 谁决定合并；
- 合并前的不同历史；
- 被错误迁移的 Claim / Fact；
- 后续拆分依据；
- 租户私有覆盖。

### 9.2 候选模型：身份链接 + 规范视图

```text
Entity A ─┐
          ├── Identity Resolution Decision
Entity B ─┘
          │
          ▼
Canonical Entity View
```

来源局部记录、Mention 和原 Entity 历史保持稳定。规范视图可以把已接受同一对象聚合展示，但不能抹除组成和决定。

### 9.3 Merge 与 Same-as

- `same_as`：身份语义主张；
- `merge view`：查询和展示优化；
- `record consolidation`：数据治理操作；
- `legal merger`：现实世界 Event；
- `organization merged_into`：Relationship / Event。

四者不得使用同一个 `merge` 操作表达。

## 十、Split 与误合并纠正

### 10.1 触发条件

- 新 Identifier 冲突；
- 不可能的时间或地点重叠；
- 不兼容的关系网络；
- 来源明确区分；
- 生物特征或图像反证；
- 租户或数据主体申诉；
- 模型版本退化；
- 领域专家复核。

### 10.2 拆分不是复制当前状态

```text
发现误合并
→ 冻结高风险使用
→ 打开 Resolution Case
→ 重建 Mention / Claim / Relationship 归属候选
→ Resolution Decision
→ 创建或恢复独立 Entity
→ 迁移“归属关系”，不改写原始对象
→ 沿血缘定位下游
→ Fact / Assessment / Product 重评
→ 发布纠正
```

每个下游对象需要重新判断，而不是把合并实体的一半字段随机分给两个新实体。

### 10.3 拆分影响分类

| 下游对象 | 处置 |
|---|---|
| Mention | 重新解析 |
| Identity Claim | 确认、拒绝或争议 |
| 普通 Claim | 重新绑定 about Entity |
| Fact | Challenge / Reverify |
| Relationship | 重新确认两端 |
| Event | 重新确认参与者 |
| Change | 重新计算前后主体 |
| Assessment / Risk | 影响评估 |
| Product / Alert | 更正、撤回或无影响确认 |

## 十一、时间与身份连续性

### 11.1 人物

- 姓名变化不创建新人；
- 证件变化不自动创建新人；
- 同名、同生日也不自动同一；
- 化名与真实身份的链接可能有不同作用域；
- 死亡、失踪或身份保护不删除历史；
- 数字账号和自然人不能默认同一。

### 11.2 组织

必须区分：

- 改名；
- 法律实体变更；
- 并购；
- 分拆；
- 重组；
- 品牌迁移；
- 子公司与集团；
- 业务部门；
- 实际控制变化；
- 空壳复用。

“网站和品牌没变”不证明法律 Entity 连续；“注册号变化”也不一定表示业务、资产和关系完全断裂。

### 11.3 项目、产品与地点

- 项目换代可能是新版本，也可能是新 Entity；
- 产品系列与具体型号不能混合；
- 地点改名、行政区划变化和物理位置需要分离；
- 虚拟组织、匿名团体和临时联盟可以使用不完整身份。

### 11.4 Identity Valid Time

Identity Claim 和 Alias 必须支持有效时间。

同一来源局部标识在不同时期可能指向不同对象，不能假定永久稳定。

## 十二、多租户与隐私

### 12.1 公共 Entity + 私有覆盖

```text
Public Entity
├── 公共 Alias / Identifier / Identity Claims
└── Tenant Overlay
    ├── 私有员工号
    ├── 私有别名
    ├── 私有 Identity Claims
    └── 私有 Resolution Decision
```

租户可以提高自身视图的解析度，但不能把私有标识或判断写回公共 Entity。

### 12.2 跨租户不可推断

两个租户分别知道同一人的不同私有标识，平台不能仅因底层物理系统看见两者就向任一租户泄漏关联。

共享解析只能使用：

- 公共 Evidence；
- 明确贡献到联合空间的材料；
- 法律和合同允许的平台级安全处理；
- 不向租户暴露的合规派生控制结果。

### 12.3 敏感身份

对未成年人、受保护人员、匿名举报人、卧底身份、医疗或生物特征等，需要：

- 更高解析门槛；
- 目的限制；
- 最小可见性；
- 不可逆展示屏蔽；
- 人类审批；
- 可撤销映射；
- 访问审计。

“识别得出来”不等于“有权建立或展示关联”。

## 十三、权威与组织分工

| 能力 | 主要责任 | 边界 |
|---|---|---|
| Mention 抽取 | 数据认知面 | 不自动创建公共 Entity |
| 候选生成和相似度 | Tool / 专用模型 | 不拥有合并权 |
| 日常身份解析 | 数据认知面 | 遵循平台策略和作用域 |
| 人物/组织语义 | 吏部及领域能力 | 提供专业意见，不拥有底座强制写权 |
| 高风险身份审核 | 门下省 | 审核用途和证据，不替代所有领域解析 |
| 复杂身份争议 | 大理寺式治理能力 | 形成 Resolution Decision，不改原 Evidence |
| 权限与敏感性 | 平台控制面 | 强制访问与用途，不创造身份事实 |
| 版本、血缘和视图 | 数据底座面 | 保存和供应，不自行决定 same_as |
| 最终保留事项 | 有权人类/外部权威 | 仍需记录依据、范围和责任 |

## 十四、Agent / Tool 分工

### Tool / Script 优先

- 文本、表格、图片和记录 Mention 抽取；
- 名称规范化和转写；
- Identifier 校验；
- 候选召回；
- 规则、硬约束和负约束检查；
- 相似度与关系邻域计算；
- 已知映射查询；
- Resolution 状态、版本和血缘；
- 合并视图计算；
- Split 影响图查询；
- 作用域和权限强制。

### Agent / 人类按需

- 复杂语境和代词指称；
- 跨语言、化名和历史名称；
- 组织连续性、并购和控制关系；
- 多项弱证据综合；
- 替代解释与反证；
- 高影响人物身份；
- 敏感身份关联；
- 误合并拆分方案；
- 跨领域身份争议。

### Token 经济性

```text
高召回候选生成
→ Tool / Index

确定性排除
→ Rule / Identifier / Time Constraint

剩余歧义
→ 小模型或领域模型

高风险、冲突、长尾
→ 部级 Agent / 门下 / 人类
```

高级 Agent 只处理“工具不能安全决定”的剩余集合。

## 十五、关键失败模式

| 失败 | 错误做法 | 正确处理 |
|---|---|---|
| 同名同公司 | 自动 same_as | 保留候选，检查时间、角色、标识 |
| 不同写法 | 创建多个永久 Entity | Alias / transliteration + 解析 |
| 组织改名 | 总是新 Entity | 判断法律与业务连续性 |
| 公司并购 | 直接 same_as | Event + succession / control 关系 |
| 来源 ID 相同 | 全局唯一 | 绑定 issuer 和 namespace |
| 相似度高 | 自动高影响合并 | 检查硬约束和负证据 |
| 私有员工号 | 写入公共实体 | Tenant Overlay |
| 误合并 | 覆盖主键后难以恢复 | 可逆 Identity Link + Split |
| 模型升级 | 静默改写所有映射 | 新版本候选与影响分析 |
| 匿名人物 | 强制实名 | 保持 Pseudonymous Entity |

## 十六、S4 不变量候选

- Entity Record 与现实指称对象、Mention、Source-local Record 必须区分；
- Mention 必须保留材料 Anchor 和解析历史；
- Name、Alias、Identifier 不是同一概念；
- Identifier 必须绑定 issuer、namespace 和有效时间；
- 同名不产生 same_as，不同名不产生 different_from；
- 候选分数不等于 Resolution Decision；
- 负身份约束必须作为一等解析输入；
- Identity Claim 仍需 Evidence、Verification 和 Acceptance Context；
- `same_as`、规范视图、数据整合和现实并购不得混用；
- Entity 合并必须逻辑可逆，不能删除组成与决策历史；
- Split 必须沿血缘重评 Claim、Fact、关系、事件、研判和产品；
- 公共 Entity 不得吸收租户私有 Identifier 或 Identity Claim；
- 能够技术识别不产生身份关联权限；
- 数据底座保存 Entity 不产生身份裁定权；
- 高风险解析不得由候选生成模型自我接受；
- Unresolved / Provisional 是合法结果。

## 十七、S4 验收结果

候选层面通过：

- Entity、Referent、Source-local Record 和 Mention 四层可区分；
- Name、Alias、Identifier 和 Identity Claim 获得独立语义；
- 身份解析被建模为候选、验证、决定和可逆链接；
- Mention 初步满足一等认识对象准入条件；
- 复杂争议可以进入 Entity Resolution Case；
- 非破坏性合并和可回放 Split 获得完整路径；
- 时间连续性、组织演变、多租户和隐私都有外层位置；
- Agent / Tool 分级可以减少 Token 且不牺牲可逆性；
- `SCN-0012` 验证误合并能够被隔离、拆分和纠正；
- 未发现需要修改 Skeleton v1.0 的一级缺口。

仍需后续验证：

- Mention 是否正式进入核心对象主干；
- Proposition 是否因引用 Entity 而升格；
- Identity Link 是关系还是对象；
- Canonical Entity View 的作用域和缓存；
- Entity 与组织 succession 的边界；
- 生物特征和隐私策略；
- Split 的完整影响传播协议；
- S5 Relationship / Event 如何引用和重绑定 Entity。

下一步：`S5 关系、事件、状态、Diff 与 Change`。

## 十八、关联

- [Claim、争议、Fact 与裁定模型](./claim-fact-and-adjudication-model.md)
- [统一语言与对象分类法](../08-reference-models/unified-language-and-object-taxonomy.md)
- [核心对象主干](./core-object-spine.md)
- [SCN-0011：多权威域事实并存](../05-scenarios-and-flows/SCN-0011-multiple-authority-domain-facts.md)
- [SCN-0012：同名实体误合并与拆分纠正](../05-scenarios-and-flows/SCN-0012-entity-merge-split-correction.md)
