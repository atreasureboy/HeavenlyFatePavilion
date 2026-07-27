# v0002 开放问题

状态：Draft

## Q-0020：Fact 是全局事实还是域内接受事实

需要确定公共事实、租户事实、任务工作假设和领域裁定之间的正式关系。

S3 候选答案：Fact 是带 `Acceptance Context` 的域内接受记录，不存在无作用域的全局真相槽。该答案仍需 S8 时间与知识覆盖专题终审。

S8 继续支持该答案：同一 Proposition 可在不同 Authority Domain 拥有各自的接受、挑战和撤回历程。Current Fact 是 Query Context 下的选择 View，不是跨域唯一槽。具体内容记录与 Acceptance Record 是否物理/逻辑拆分留给 S9。

## Q-0021：认识时间是否独立于系统时间

需要用迟到资料和撤回场景判断某主体“何时知道”是否值得独立建模。

S8 候选答案：必须独立建模，但不是一个全局第三时间戳。对象 Revision 使用 Valid + Record Time；Authority Domain 通过 Epistemic Record 表达 observed、available、considered、accepted、challenged 和 withdrawn 的有效区间。

## Q-0022：Entity 身份由谁权威确认

需要区分候选匹配、已接受同一性和争议身份。

S4 候选答案：数据认知面负责日常解析，领域能力提供语义，平台强制作用域与用途，复杂争议进入 Entity Resolution Case，由大理寺式治理能力和必要人类形成 Resolution Decision。候选模型和相似度不拥有接受权。

## Q-0023：Relationship 与 Event 的边界

需要确定持续状态、发生行为和派生关系的统一规则。

S5 候选答案：Event 表达有边界发生项，Relationship 表达区间内持续联系；Event 可以建立、修改或终止 Relationship，但两者分别保存并共享 Fact / Evidence 血缘。

## Q-0024：Change 是独立对象还是对象版本差异

Change 需要支持监测、告警、解释和因果链，但并非每个版本差异都是现实变化。

S5 候选答案：Diff 是比较记录；具有独立身份、生命周期、重要性和跨面消费者的语义变化才成为 Change。Content、Processing、Knowledge、World Change 必须分型。

## Q-0025：Forecast 与 Scenario 的边界

需要确定概率未来判断与条件化可能世界是否共享假设和失效条件模型。

S6 候选答案：Scenario 是基于假设和分支的条件化可能世界，可以无概率；Forecast 是有目标、结果空间、时间窗和解析规则的可评分未来判断。二者共享假设但保持独立对象。

## Q-0026：公共知识与租户覆盖如何合并

需要定义共享原件、公共裁定、租户私有证据、租户判断和任务临时假设的可见合成顺序。

S8 候选答案：不采用层级覆盖或 last-write-wins。先按当前授权发现候选，再对齐命题、实体、定义、有效时间和辖域，按 knowledge cutoff 过滤，保留各 Authority Domain 的接受与冲突，最后由用途相关 Selection Policy 输出 Selected View、Alternatives、Conflict 和 Provenance。

## Q-0027：纠正传播到哪里停止

事实撤回后，需要区分必须失效、必须复核、只需标记风险和无需变化的下游对象。

S7 候选答案：不按固定跳数停止。先用固定版本、有类型的依赖边发现候选，再依据依赖语义、替代证据、作用域、产品暴露和纠正义务逐分支分类；每个停止分支必须保存机器规则或审核理由，缺失血缘按保守原则隔离和补链。

## Q-0028：Source Principal、System 与 Endpoint 是否独立成对象

三者需要分别表达，但未证明都需要独立身份和生命周期。将在来源迁移、托管平台和供应链攻陷场景中验证。

## Q-0029：Document Representation 是否成为正式对象

同一逻辑资料可能同时存在 HTML、PDF、打印版、翻译版和 API 表示。需要判断 Representation 是 Document 关系、Snapshot 属性还是独立对象。

## Q-0030：Evidence Relation 是否需要独立身份

同一 Evidence 对不同 Claim 可能分别支持、反驳或限定。若论证关系需要独立审核、版本和撤回，可能需要成为一等关系对象。

## Q-0031：Rights / Usage Envelope 的权威来源与合成规则

平台策略、来源许可、数据主体权利、租户合同和法律留置可能冲突，需要在权限与数据生命周期专题建立确定性优先级。

## Q-0032：派生证据的最大允许变换链

OCR、翻译、转录、聚合和计算可以连续派生。需要定义何时必须回到直接证据、何时必须由人类复核，以及如何表达累积误差。

## Q-0033：Proposition 是否升格为核心对象

多个 Claim 需要共享规范化命题以便去重、冲突和事实接受。需要在实体、事件和时间模型完成后判断它是否具有独立身份与生命周期。

## Q-0034：Fact 与 Fact Acceptance 是否拆分

当前候选让 Fact 自身携带 Acceptance Context。若同一命题存在大量事实域接受记录，可能需要拆为 Proposition / Fact Assertion / Acceptance Record。

S8 候选倾向拆分共享命题/陈述内容与作用域化 Acceptance / Epistemic Record，否则无法表达多个 Authority Domain 在不同时间对同一 Revision 的认识历程。领域语言中的 Fact 仍表示“被指定域接受的事实”，S9 终审逻辑对象边界。

## Q-0035：Dispute Case 是否进入正式核心对象主干

S3 已证明争议案件具有多方、审理、裁定、上诉和重开生命周期，但需要验证它属于通用治理对象还是只属于大理寺内部模型。

## Q-0036：事实域之间是否存在默认优先级

法律登记、公共基线、租户内部事实和任务工作事实的权威针对不同命题。需要避免建立粗暴的全局权威排序，同时定义查询和冲突时的可解释选择规则。

S8 候选答案：不存在全局默认优先级。Authority Selection Policy 必须结合命题类型、角色、辖域、用途、时间和证据门选择适用权威；高风险冲突应返回多值与 Unresolved，而非强制选一。

## Q-0037：事实被挑战时默认允许哪些用途

高风险产品可能立即冻结，低风险查询可能继续展示并附警告。需要在产品、风险和权限专题建立用途状态矩阵。

## Q-0038：Mention 是否正式进入核心对象主干

S4 已证明 Mention 具有 Anchor、解析状态、候选集合、纠正历史和多个消费者。需要由 S5 关系与事件抽取进一步验证后裁定。

## Q-0039：Identity Link 是关系还是独立对象

它需要状态、证据、决定、时间和作用域，但也可能由 Identity Claim + Resolution Decision 完整表达。需要避免重复建模。

## Q-0040：Canonical Entity View 如何跨作用域计算

公共解析、租户私有覆盖、联合空间和 Mission 临时候选可能产生不同聚合视图，需要在 S8 知识覆盖专题定义缓存和失效语义。

S8 候选答案：Canonical Entity 是 Query Context View。缓存键至少包含 tenant/principal class、purpose、scope set、world/knowledge/record time、Selection Policy、Schema 和输入水位；Entity Split、授权、联合成员、任务结束或策略变化均触发作用域化失效。

## Q-0041：组织改名、继承、并购和分拆怎样判定 Entity 连续性

法律实体、品牌、资产、控制权和业务连续性可能分别变化。S5 Relationship / Event 需要提供正式判据。

## Q-0042：敏感身份解析可以使用但不能展示到什么程度

生物特征、受保护人员、匿名举报人和跨租户隐藏关联需要在身份权限专题定义目的限制、人类审批与派生结果最小化。

## Q-0043：Relationship 是否统一采用一等对象

复杂关系明确需要独立对象，但简单、不可争议且无独立时间的关系是否允许作为属性投影，需要在数据架构专题确定一致规则。

## Q-0044：Event Mention 是否独立于普通 Mention

事件报道中的触发词、参与者和时间片段具有组合解析状态。需要判断 Event Mention 是 Mention 集合/视图还是独立认识对象。

## Q-0045：World State Snapshot 应保留多久

“当时所知”对审计和产品复现重要，但全量物化所有时点成本极高。需要在 S8/S9 与数据架构专题确定按需物化和重建策略。

S8 候选答案：以不可变 Revision / Epistemic / Decision 日志为基础，只在高影响产品、决定、外部行动、重大撤回、Schema 切换、空间关闭、迁移/删除和监管要求时形成检查点。Snapshot 记录 Exact / Equivalent / Partial / NonReconstructible，不取得事实权威。

## Q-0046：Change 使用单一上位对象还是多个对象类型

统一 Change 有利于监测和订阅，但可能让 Content、Processing、Knowledge、World 语义再次混合。需要在 Schema 和契约专题裁定继承、组合或事件类型方案。

## Q-0047：因果主张属于 Claim 还是 Assessment

简单明示因果可以是 Claim，综合因果通常是 Assessment。需要在 S6 明确两者的接受和审核边界。

S6 候选答案：外部主体明确表达的因果陈述是 Causal Claim；天机阁跨证据综合形成的机制判断属于 Assessment。两者都必须表达机制、替代解释和不确定性。

## Q-0048：Hypothesis 是否升格为核心对象

它具有可证伪条件、Evidence、替代解释和长期调查价值，但当前可以作为 Assessment 的有身份组成。需由协作和长期 Mission 场景继续验证。

## Q-0049：Scenario 与 Forecast 如何组合

Forecast 可以条件化于一个或多个 Scenario，但二者的版本、概率和失效关系不能重复存储。后续 Schema/契约专题裁定组合模型。

## Q-0050：Forecast Resolution 与 Score 是否独立对象

解析结果、评分方法、干预状态和校准 cohort 具有独立版本与审计价值，需要在 S9 血缘和评测专题验证对象准入。

## Q-0051：Intelligence Risk 是否只表达负面结果

当前定义保持风险为潜在损失；商业机会和正向上行可用 Opportunity / Upside Assessment 表达。是否建立对称 Opportunity 对象留给领域专题。

## Q-0052：Action 与 Effect 何时进入核心对象主干

S6 干预场景证明它们对预测评分、风险更新和建议评估不可缺失；S7 又证明产品暴露、接收者决定和实际行动必须分别追踪。外部行动专题最终裁定。

S7 候选答案：Action 必须作为可引用外部行动表达，但不能从 Recommendation 或 Acknowledgement 推导；Effect 当前更适合作为带反事实、替代解释和不确定性的 Effect Assessment / Causal Claim 家族。是否进入永久核心对象主干继续留给外部行动专题。

## Q-0053：Product、Edition 与 Release 是否都是核心对象

S7 已证明长期产品身份、冻结内容版本和受众/用途发布授权具有不同生命周期、权威与纠正语义。S9 需要决定采用三个永久对象，还是使用 Product + Version + Release Record 的实现无关组合。

## Q-0054：Dependency Edge 是否成为一等对象

premise、supports、contradicts、bounds、context、quoted_as_claim 和 derived_from 需要独立来源、审核、版本、替代与纠正。S9 需判断它是一等关系对象还是有身份的 Lineage Record。

## Q-0055：Alert Thread 是对象还是关联视图

持续风险会产生多个 Trigger 和 Alert。线程需要聚合、升级、责任转移和重开，但不能吞并底层 Signal、Risk 或 Alert 历史。后续告警和运行专题裁定。

## Q-0056：动态产品怎样保存“当时看见的内容”

仪表板和查询投影持续变化，全部时点物化成本过高。S8 需要联合知识时间、产品风险、访问事件和纠正义务确定检查点与重建策略。

S8 候选答案：日常交互依赖版本日志和确定性查询重建；一旦动态投影被正式发布、用于高影响决定/行动、触发纠正义务或跨越重大 Schema/Policy 边界，就固定 Query Context、输入水位、依赖 Manifest 和必要 Rendering 检查点。

## Q-0057：不可追踪消费时如何关闭纠正义务

隐私、离线下载、截图和外部转发可能使接收者集合不可知。需要以受控渠道处理、尽力通知、合同边界和显式剩余风险替代“完全召回”的虚假保证。

## Q-0058：Product Release 的受众裁剪何时需要新 Edition

若只应用确定性字段屏蔽，可能共享 Edition 并建立不同 Release/Rendering；若裁剪改变叙事、上下文或结论含义，则必须创建新 Edition。S8/S9 需要给出正式判据。

S8 候选答案：仅当裁剪是可逆、确定性、不会改变结论含义和不确定性表达的投影时可共享 Edition；若移除 Evidence、反证、限定条件或上下文会改变受众理解，则必须建立新的 Edition 和独立知识 Manifest。S9 终审。

## Q-0059：Correction Notice 与 Change Notice 是否进入正式对象主干

两者具有分发、确认、时限和审计价值，但可能属于过程记录。S9 根据独立生命周期、消费者和纠正失败语义终审。

## Q-0060：Epistemic Record 是否成为核心对象

S8 已证明多 Authority Domain、迟到资料、接受/挑战/撤回历程和历史重建需要独立记录。S9 需判断它是共享核心对象，还是 Fact Acceptance、Assessment Acceptance、Product Awareness 等关系的统一上位语义。

## Q-0061：Authority Domain 怎样建模

它可能由公共登记机构、租户内部治理、联合调查、任务工作空间或领域裁定机制形成。需决定它是正式对象、配置实体还是 Scope / Policy 值对象，并定义生命周期和委托。

## Q-0062：时间不确定性采用何种共享模型

点、区间、before/after、年月精度、开放边界、时区未知和 possibly-overlaps 必须跨 Event、Relationship、Fact 与 Forecast 一致表达。后续 Schema 专题裁定值模型。

## Q-0063：历史语义等价由谁证明

旧模型、Tool 或 Prompt 可能不可执行。需要定义 Exact replay、Semantically Equivalent replay 的证明主体、评测门槛和审计证据。

## Q-0064：Joint Space 关闭后的派生知识归属

联合空间中的共享输入、联合 Fact、各租户既有知识和新派生 Assessment 可能有不同保留权。需要在联合协作与数据生命周期专题定义退出、降级、删除和再利用规则。

## Q-0065：匿名私有证据能否影响公共结论

公共结论可能需要吸收不可公开的租户 Evidence，但不能泄露证据或其存在。需要设计可信裁定、最小披露和可审计但不越权的证明方式。
