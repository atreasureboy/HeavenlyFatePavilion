# SCN-0002：租户私有企业尽调

状态：Accepted

## 目标

仙人要求结合公开资料和租户上传文件，对一家目标企业进行严格尽调。验证十面、三省六部和完整情报主链。

## 参与者

- 仙人、皇帝、中书省、尚书省、门下省；
- 户部、吏部、刑部、工部及其工具群；
- 十个一级面；
- 文渊阁、国史馆、大理寺、都察院、翰林院、枢密院。

## 前置上下文

```text
Tenant Private Scope
Risk Tier R2
Evidence Requirement Strict
Mission Budget + Deadline
允许的公共来源与租户文件
禁止外部传播
```

## 主路径

```text
1. 人机交互面把目标、范围、截止期和上传资料形成 Intent。
2. 皇帝交中书省形成 Mission 与 Plan，明确验收、证据和预算。
3. 门下省预审权限、利益冲突、数据范围和高风险结论标准。
4. 尚书省把 Plan 编译为 Run / Task 图：
   ├── 工部工具：采集、OCR、解析、去重、索引
   ├── 吏部：人物、任职、关联机构和同名消歧
   ├── 户部：融资、经营、机会和资源变化
   └── 刑部：法规、诉讼、制裁和合规风险
5. 运行面预留预算并并行执行 Tool；认知中断才升级部级 Agent。
6. 数据认知面形成 Evidence → Claim → Fact，并建立实体、关系、事件和 Change。
7. 数据底座保存公共基线与租户私有覆盖，保持血缘与作用域。
8. 情报研判面形成 Assessment、Forecast、Risk 与 Recommendation。
9. 门下省反证；大理寺处理事实冲突；都察院抽查调用链和引用。
10. 情报产品面生成尽调报告，翰林院只润色已批准内容。
11. 皇帝向仙人解释结论、证据、不确定性和剩余风险。
12. Feedback 进入后续监测和能力演进。
```

## 关键状态

```text
Mission UnderReview → Approved → Active → Completed
Claim UnderVerification → Verified / Disputed / Rejected
Fact Proposed → Active / Disputed
Assessment Draft → UnderReview → Active
Risk Candidate → Assessed → Open / Monitoring
Product Draft → UnderReview → Approved → Published
```

## Agent / Tool 分工

- Agent：目标拆解、跨域研判、反证、冲突解释和报告叙事；
- Tool：采集、解析、搜索、消歧候选、规则匹配、预算计量、权限、血缘和审计；
- 人类：R2 例外、专家争议和最终业务决定；
- Recommendation 不等于外部行动。

## 异常与补偿

- OCR 低质量：换解析器或人工复核，不直接形成 Claim；
- 人物同名：保留候选实体，不安全合并；
- 私有文件许可不明：隔离该来源并退回澄清；
- 某部超时：尚书省重排或返回明确缺口；
- 结论证据不足：门下封驳，回中书补充调查；
- 报告引用失效：阻止发布。

## 验证结果

通过。十面均有明确输入输出，官署叙事没有覆盖技术权威，三省形成权力闭环而非固定流水线。

验证 `INV-0002` 至 `INV-0008`。
