# Source、Document、Snapshot 与 Evidence 语义模型

状态：Proposed

提出日期：2026-07-27

所属迭代：`v0002-intelligence-semantics`

## 一、模型目的

本文细化天机阁从外部世界取得资料、保留原貌并形成可用证据的第一段语义链：

```text
Source
→ Document
→ Snapshot
→ Evidence
```

这条链必须回答：

- 来源是谁或是什么；
- 一份逻辑资料怎样跨版本保持身份；
- 某次实际获取到了什么；
- 哪一部分材料可以支持或反驳哪项主张；
- 来源变化、抓取失败、解析升级、许可撤销和租户复用时怎样处理；
- Agent 与确定性 Tool 分别可以做什么；
- 为什么“抓到了网页”不能直接变成“拥有证据”。

本文保持 `Proposed`，将在后续 Claim / Fact、时间、作用域和纠正专题中继续验证。

## 二、四对象关系

```text
┌──────────────┐
│ Source       │  来源主体、系统或端点
└──────┬───────┘
       │ publishes / provides / exposes
       ▼
┌──────────────┐
│ Document     │  跨版本可识别的逻辑资料
└──────┬───────┘
       │ captured_as
       ▼
┌──────────────┐
│ Snapshot     │  某次获取到的不可变内容
└──────┬───────┘
       │ anchored_as
       ▼
┌──────────────┐
│ Evidence     │  可定位、可解释、可用于论证的材料单元
└──────┬───────┘
       │ supports / opposes / contextualizes
       ▼
     Claim
```

四个对象不是同一内容的四个状态，而是四种不同身份：

- Source 可以存在而没有任何 Document；
- Document 可以暂时无法成功捕获；
- 一个 Document 可以有多个 Snapshot；
- 一个 Snapshot 可以产生多个 Evidence；
- 同一 Evidence 可以参与多个 Claim，但每条论证关系独立存在；
- Evidence 不能脱离 Snapshot 或其他可审计材料根。

## 三、Source：来源身份

### 3.1 正式定义

Source 表示天机阁能够识别、评估和治理的资料来源主体、信息系统或发布端点。

典型 Source：

- 新闻机构、公司、政府部门、研究机构；
- 网站、API、数据库、文件库、RSS；
- 人类提交者、租户业务系统；
- 传感器、事件流和外部情报服务。

### 3.2 Source 不是什么

```text
Source ≠ URL
Source ≠ Document
Source ≠ Publisher 永久可信背书
Source ≠ Credential
Source ≠ 抓取任务
Source ≠ 事实权威
```

一个机构可以控制多个端点；一个端点也可能托管多个发布主体的内容。URL 只是定位属性或端点标识，不能自动等于来源身份。

### 3.3 Source 的三层身份

候选模型：

```text
Source Principal
→ 对内容承担发布或提供责任的主体

Source System
→ 实际承载、生成或供应内容的系统

Source Endpoint
→ 天机阁用于发现或获取内容的技术端点
```

S2 暂不要求把三者都实现成独立核心对象，但必须能够分别表达，避免把“某公司”“其官网”“一个网页 URL”压成同一 Source。

### 3.4 来源质量必须分面

禁止使用单一 `source_trust_score` 吞并全部质量。至少区分：

- 身份确定度；
- 内容来源链完整度；
- 历史准确性；
- 独立性；
- 时效性；
- 完整性；
- 稳定性；
- 许可与可使用性；
- 针对特定 Claim 类型的专业适用性。

来源质量只能影响验证策略，不能让某个 Source 的全部内容自动成为 Fact。

### 3.5 Source 生命周期骨架

```text
Discovered
→ Registered
→ Assessed
→ Active
→ Degraded / Suspended
→ Retired

任意状态
→ Compromised
→ Quarantined
→ Reassessed
```

Source 被停用不删除历史 Snapshot 和 Evidence，但会影响后续获取和依赖它的结论复核。

## 四、Document：逻辑资料身份

### 4.1 正式定义

Document 表示一个可跨获取、格式和版本持续识别的逻辑内容单元。

例如：

- 一篇持续修改的网页文章；
- 一份年度报告的特定发布版；
- 一条可被编辑或删除的社媒帖子；
- 一项法规文本；
- 一条数据库业务记录；
- 一个租户上传文件的逻辑身份。

### 4.2 Document 身份不由单个 URL 决定

以下情况必须允许：

```text
同一 Document → 多个 URL
同一 URL → 随时间承载不同 Document
同一内容哈希 → 多个独立 Document
同一标题 → 多个不同 Document
转载 / 镜像 → 与原 Document 有 lineage，但不自动同一
格式转换 → 可能仍属于同一逻辑 Document 的 Representation
```

因此需要区分：

- 内容身份；
- 发布身份；
- 技术定位；
- 版本身份；
- 表示格式；
- 派生或转载关系。

### 4.3 Document Identity 决策

Document 的归并或拆分必须保留判断依据：

- 发布主体；
- 稳定外部标识；
- canonical / permalink；
- 标题与内容结构；
- 发布时间；
- 版本或修订号；
- 上下游引用；
- 内容相似性；
- 领域规则；
- 人工或 Agent 裁定。

归并结果可以是候选、接受、争议或拆分，不能只保留最终 ID。

### 4.4 Document 关系

候选关系包括：

```text
revises
supersedes
retracts
translates
mirrors
syndicates
quotes
summarizes
derives_from
is_representation_of
```

这些关系不自动继承事实可信度。十个网站转载同一篇稿件，来源独立性仍可能只有一。

## 五、Snapshot：不可变捕获

### 5.1 正式定义

Snapshot 表示天机阁在一次明确获取行为中实际得到并保存的不可变内容版本。

Snapshot 的核心不是“完整”，而是“对实际取得内容诚实”。

它至少必须表达：

- 所属 Document；
- 来源和端点；
- 获取开始与完成时间；
- 原始字节或不可变对象引用；
- 内容哈希；
- 媒体类型、编码和大小；
- 获取方法、Tool 和版本；
- HTTP/API/文件元数据；
- 捕获完整性；
- 许可、可见性和保留约束；
- 前后 Snapshot 或派生 Snapshot 关系；
- acquisition trace。

### 5.2 Snapshot 的不可变性

```text
已保存 Snapshot
≠ 可以原地修正文案
≠ 可以被新抓取覆盖
≠ 可以因解析升级重写
```

重新获取创建新 Snapshot。

解压、OCR、转码、去噪和正文提取产生 Derived Artifact 或派生表示，并引用原 Snapshot；不能替换原始捕获。

### 5.3 捕获完整性

Snapshot 必须显式声明捕获状况，例如：

```text
Complete
Partial
Truncated
Corrupted
AccessDenied
MetadataOnly
DynamicUnresolved
Unknown
```

`Partial` Snapshot 仍可具有价值，但由它产生的 Evidence 必须继承相应限制。

### 5.4 Acquisition Record

一次获取过程需要独立 Process Record，记录：

- 请求和响应；
- 重试、重定向和认证；
- 机器人协议或访问政策；
- Tool、代理、区域和网络上下文；
- 错误和降级；
- 实际写入的 Snapshot；
- 调用成本和审计信息。

Acquisition Record 证明“怎样获得”，Snapshot 表示“获得了什么”。两者不能合并。

## 六、Evidence：可用于论证的材料单元

### 6.1 正式定义

Evidence 是锚定在 Snapshot 或其他可审计材料根上的、具有足够上下文、能够支持、反驳或限定一个或多个 Claim 的材料单元。

Evidence 可以是：

- 文本区间；
- 表格行列与表头组合；
- 图片区域；
- 音视频时间区间；
- 数据记录的字段集合；
- 文件签名或可验证元数据；
- 人类证词的受控记录。

### 6.2 Evidence 必须有锚

Evidence Anchor 至少需要：

- material root；
- Snapshot / Artifact 版本；
- 定位方式；
- 定位范围；
- 上下文窗口；
- 提取方法和版本；
- 完整性校验；
- 可重放或不可重放原因。

正文字符串副本不能替代锚。内容相同也可能来自不同上下文并表达不同含义。

### 6.3 Evidence 不等于 Claim

```text
Evidence
→ “材料中实际有什么”

Claim
→ “某主体借此对世界陈述什么”
```

Evidence 本身不携带“真/假”结论。它通过有类型论证关系参与 Claim：

```text
supports
opposes
qualifies
contextualizes
corroborates
duplicates
```

关系必须记录建立者、时间、方法、适用 Claim 版本和理由。

### 6.4 Evidence 的上下文完整性

截取必须防止：

- 断章取义；
- 表头和单位丢失；
- 否定词或限定条件被裁掉；
- 图片说明与正文分离；
- 音视频说话人错配；
- 翻译替换原文；
- OCR 错误被当成原文；
- 动态页面的隐藏状态丢失。

必要时 Evidence 应保存最小片段与扩展上下文两个范围。

### 6.5 原生证据与派生证据

候选区分：

```text
Direct Evidence
→ 直接锚定原始或权威材料

Derived Evidence
→ 由 OCR、解析、转录、翻译、计算或聚合产生
```

派生证据必须保留完整变换链，不能伪装成直接证据。

模型摘要默认是 Artifact 或 Claim 候选，不因“引用了原文”自动成为 Evidence。

### 6.6 Evidence 生命周期骨架

```text
Candidate
→ Anchored
→ QualityChecked
→ Available
→ Restricted / Challenged
→ Superseded / Unavailable
```

Evidence 的可用性变化不抹除它曾经支持过哪些 Claim。下游需要根据变化重新评估，而不是重写历史。

## 七、权威与所有权

| 行为 | 语义责任 | 确定性强制 |
|---|---|---|
| Source 注册与准入 | 数据认知面 + 领域能力 | 平台策略、许可和身份规则 |
| Document 身份归并 | 数据认知面 | 候选索引、唯一性和审计 |
| Snapshot 获取 | 智能执行与运行面执行，数据认知面定义语义 | 采集 Tool、哈希、不可变写入 |
| Snapshot 持久保存 | 数据底座面 | 对象存储、版本和保留策略 |
| Evidence 选取与解释 | 数据认知面，必要时领域 Agent | Anchor 校验、作用域和血缘 |
| Evidence 接受使用 | Claim 验证或治理流程 | 策略门、质量门和审计 |
| 许可/可见性强制 | 平台控制与外部权威 | IAM、Policy Engine、数据网关 |

数据底座拥有持久状态，不因此取得“这段材料能证明什么”的语义裁定权。

## 八、Agent / Tool 分工

### 8.1 Tool / Script 优先

- Source 健康检查和端点探测；
- 抓取、API 调用、校验和重试；
- 内容哈希、去重候选和格式识别；
- 不可变 Snapshot 写入；
- 文本定位、表格坐标、时间码和图像区域 Anchor；
- 许可标签传播；
- 已知格式解析、OCR 和转码；
- 完整性与血缘机械校验。

### 8.2 Agent / 人类按需

- 复杂 Source 身份和发布责任解释；
- Document 归并、转载、修订和撤回关系判断；
- 非结构化 Evidence 选择；
- 上下文是否足以支撑 Claim；
- 来源独立性和利益关系解释；
- 讽刺、隐喻、领域术语和多模态语境；
- 高影响或争议 Evidence 的复核。

### 8.3 不可越过的边界

- Agent 不得修改已保存 Snapshot；
- Agent 选择 Evidence 时必须生成 Anchor；
- Tool 的成功返回不等于 Evidence 质量通过；
- 来源声誉不允许跳过 Claim 验证；
- 为节省 Token 可以跳过 Agent，但不能跳过不可变捕获、Anchor、作用域和血缘。

## 九、多租户、许可与复用

### 9.1 三种权利不能混用

```text
能够获取
≠ 能够保存
≠ 能够向某租户展示
≠ 能够用于模型或推断
≠ 能够写入公开产品
```

每个 Snapshot 和 Evidence 必须携带或引用可计算的 Rights / Usage Envelope。

### 9.2 共享与覆盖

候选原则：

- 公共 Snapshot 可以被多租户复用一次物理捕获；
- 每个租户对 Evidence 的可见性和使用目的独立判定；
- 租户私有 Snapshot 默认不能进入公共知识；
- 联合空间只获得明确贡献对象的派生可见性；
- Evidence Anchor 可以共享，但租户注释、Claim 和 Assessment 默认私有；
- 缓存命中不能绕过当前租户权限复判。

### 9.3 权利撤销

权利撤销可能产生不同效果：

- 禁止未来获取；
- 禁止新用途；
- 禁止继续展示；
- 要求删除可删除副本；
- 因审计、法律留置或责任追踪保留受限记录；
- 触发下游产品撤回或重新生成。

撤销不能用一个 `deleted=true` 表达全部语义。

## 十、时间与版本

### 10.1 Source 时间

- Source 首次发现时间；
- Source 身份确认时间；
- Source 状态变化时间。

### 10.2 Document 时间

- 声称发布时间；
- 实际可观察发布时间；
- 修订、撤回或替代时间；
- 时间可信度和来源。

### 10.3 Snapshot 时间

- 获取请求时间；
- 首字节/开始捕获时间；
- 完成捕获时间；
- 系统记录时间。

### 10.4 Evidence 时间

- 所指材料的现实有效时间；
- Anchor 建立时间；
- Evidence 可用性变化时间；
- 某权威域开始知道或使用它的时间。

来源声称时间不能被抓取时间覆盖；未知发布时间也不能用抓取时间伪造。

## 十一、变化、重处理与纠正

```text
新 Snapshot - 旧 Snapshot
→ Content Diff
→ 是否具有语义意义？
→ 可能形成 Document Revision / Retraction / World Change 候选
```

```text
同一 Snapshot
+ 新解析器 / 新 OCR / 新模型
→ 新 Derived Artifact
→ Extraction Diff
→ Evidence Anchor 可能修正
→ 不代表来源内容变化
```

```text
Evidence Anchor 或上下文被判无效
→ Evidence Challenged / Unavailable
→ 查找依赖 Claim
→ 进入验证与影响传播
→ 不删除旧论证历史
```

因此，Snapshot 版本、处理版本和知识版本必须能够分别回放。

## 十二、关键失败模式

| 失败 | 不安全行为 | 正确语义 |
|---|---|---|
| 页面更新 | 覆盖旧内容 | 创建新 Snapshot 和 Content Diff |
| 抓取不完整 | 当成完整正文 | 标记 Partial 并限制 Evidence |
| OCR 错误 | 改写原 Snapshot | 新建派生 Artifact 和 Extraction Diff |
| URL 复用 | 沿用旧 Document ID | 重新判断 Document 身份 |
| 十站转载 | 当十个独立来源 | 保留 lineage 和独立性判断 |
| 来源被攻陷 | 删除全部历史 | 隔离 Source，保留并复核历史依赖 |
| 许可撤销 | 无差别物理删除 | 按权利、留置和影响分类处置 |
| 租户缓存命中 | 直接返回共享内容 | 重判可见性和用途 |
| 摘要引用原文 | 摘要升级成 Evidence | 摘要保持 Artifact / Claim 候选 |
| Anchor 失效 | 静默重新定位 | 建新版本并触发依赖复核 |

## 十三、S2 不变量候选

- Source 身份、Document 身份、Snapshot 身份和 Evidence 身份必须分离；
- Snapshot 一经持久化不可原地覆盖；
- 解析、OCR、翻译和摘要不得重写原 Snapshot；
- Evidence 必须锚定可审计材料根；
- Evidence 片段必须保留足以解释其含义的上下文；
- Source 可信度不能让内容跳过 Claim 与 Fact 验证；
- 捕获成功不等于证据可用；
- 内容哈希相同不等于来源独立；
- Content Diff、Extraction Diff 和 World Change 必须区分；
- 作用域和使用权不能由存储位置隐式推断；
- 共享物理资料不意味着共享租户判断；
- 权利撤销、来源攻陷和 Anchor 失效必须触发依赖影响分析；
- 所有派生材料必须能回放到原始 Snapshot 和变换版本。

## 十四、S2 验收结果

候选层面通过：

- 四对象身份和转换关系清晰；
- Source 主体、系统和端点可以分别表达；
- Document 身份不依赖 URL 或内容哈希；
- Snapshot 不可变性、捕获完整性和获取记录分离；
- Evidence Anchor、上下文和派生链得到定义；
- 权威、Agent / Tool、多租户、许可、时间、变化和失败均有外层位置；
- 现有十面和七维足以承载，没有发现一级骨架缺口。

仍需后续验证：

- Source 三层身份是否都需独立对象；
- Representation 是否升格为正式对象；
- Evidence Relation 是否需要独立身份；
- Rights / Usage Envelope 的正式结构；
- 认识时间是否独立保存；
- 删除、法律留置和历史审计的冲突策略；
- S3 Claim / Fact 如何接受 Evidence。

下一步：`S3 主张、争议、事实与裁定`。

## 十五、关联

- [统一语言与对象分类法](../08-reference-models/unified-language-and-object-taxonomy.md)
- [核心对象主干](./core-object-spine.md)
- [v0002 迭代入口](../02-architecture-iterations/v0002-intelligence-semantics/README.md)
- [SCN-0010：来源变化与证据保全](../05-scenarios-and-flows/SCN-0010-source-mutation-and-evidence-preservation.md)
- [七维横切治理](../03-current-architecture/cross-cutting/README.md)
