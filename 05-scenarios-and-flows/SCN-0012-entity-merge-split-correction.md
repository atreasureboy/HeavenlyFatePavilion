# SCN-0012：同名实体误合并与拆分纠正

状态：Accepted

提出日期：2026-07-27

## 一、目标

公开资料中存在两名都叫“王伟”的人物：

- 王伟 A：某集团财务负责人；
- 王伟 B：同集团子公司的高级工程师。

两人都曾在上海工作，英文名都被转写为 `Wei Wang`。早期低质量模型把两者合并成一个 Entity，导致履历、项目和风险事件混合。后来租户私有 HR 资料和公开会议视频提供了强反证。

验证天机阁能否：

- 不用破坏性主键合并；
- 识别误合并并暂停高风险使用；
- 把 Mention、Claim、Fact、Relationship 和 Event 重新归属；
- 保护租户私有员工号和 HR Evidence；
- 更正已发布产品；
- 保留原合并决定和错误影响历史。

## 二、前置对象

```text
Entity EA = “王伟 / Wei Wang”

EA 当前聚合：
├── Mention M1：集团年报中的财务负责人王伟
├── Mention M2：子公司专利中的工程师王伟
├── Mention M3：会议视频中的 Wei Wang
├── Relationship R1：任职于集团
├── Relationship R2：参与工程项目
├── Event V1：财务风险事件
└── Product P1：人物综合档案
```

早期 Resolution Decision D1 依据：

- 同名；
- 同集团；
- 同城市；
- 英文转写相同。

没有可靠 Identifier，也没有记录足够负证据。

## 三、新证据

```text
Tenant Private HR Snapshot H1
→ Evidence EH1：
  员工号、部门、入职时间显示存在两名不同员工

Public Video Snapshot V2
→ Evidence EV2：
  两人在同一会议不同时段出现，人物图像和职位不同

Patent Snapshot P2
→ Evidence EP2：
  工程师王伟拥有与财务负责人不兼容的持续技术履历
```

私有员工号只能在租户范围内使用，不能写入公共 Entity。

## 四、主路径

```text
1. 负约束检测

EH1 + EV2 + EP2
→ same_as 硬约束冲突
→ EA.identity_status = Challenged
→ 高风险产品和告警暂停使用 EA 聚合视图

2. 打开 Entity Resolution Case RC1

输入：
M1 / M2 / M3
Identity Claims
D1
全部正负 Evidence
受影响对象清单

3. 候选重建

创建/恢复：
Entity E-A：财务负责人王伟
Entity E-B：工程师王伟

4. Mention 重新解析

M1 → E-A
M2 → E-B
M3 → 根据视频时间和说话人重新解析

5. 下游逐项重评

R1 → E-A
R2 → E-B
V1 → 只关联 E-A
Claims / Facts → 重新验证 about Entity
Assessment / Risk → 重新计算

6. Resolution Decision D2

reject old same_as
accept different_from(E-A, E-B)
supersede D1
preserve D1 history

7. 产品纠正

P1 → CorrectionRequired
→ 拆成两个人物档案
→ 解释旧版误合并原因和受影响结论
→ 通知已知消费者
```

## 五、多租户与隐私处理

- HR Snapshot H1、员工号和部门细节保持 Tenant Private；
- 平台可以在授权范围内用 EH1 支持租户 Resolution Decision；
- 公共 Entity 只发布由公共 Evidence 足以支持的区分结果；
- 若公共证据不足，公共层可以保持两名候选 Entity，而不披露私有原因；
- 其他租户不能推断 HR 系统存在或员工号；
- 联合调查需要显式贡献和用途授权。

## 六、Agent / Tool 分工

### Tool

- Mention 候选召回；
- Identifier namespace 校验；
- 时间、地点和职位硬约束；
- 图关系影响查询；
- 可逆 Identity Link；
- Split 后重新排队；
- 作用域和隐私强制；
- 产品依赖定位。

### Agent / 人类

- 同集团不同组织层级解释；
- 会议视频人物与说话人语境；
- 履历是否可能兼容；
- 私有证据能否支持公共区分；
- 拆分对事实与风险结论的影响；
- 更正措辞和责任说明。

## 七、异常分支

### A：公开视频无法可靠区分人物

租户层接受拆分，公共层保持 `Provisional / Unresolved` 两候选，不用私有结论强迫公共接受。

### B：某些 Mention 仍无法归属

Mention 保持 Unresolved，不能随机分配给 E-A 或 E-B。

### C：旧产品已在租户外传播

按分发记录通知；无法控制的副本记录为外部影响边界。

### D：后续证明其实有三名王伟

重开 RC1 或创建新 Resolution Case；现有两实体不是永久封闭集合。

### E：模型升级建议重新合并

新模型只能创建候选 Identity Claim，不能覆盖 D2；必须重新经过强约束和高风险审核。

## 八、验证不变量

- 相似度和同名不能成为最终合并权；
- Identity Link 必须可逆；
- 原始 Mention、Evidence 和旧 Decision 不能被删除；
- Split 必须重评下游，而不是只改 Entity 表；
- 私有 Identifier 不能泄漏到公共视图；
- Unresolved Mention 是合法结果；
- 模型升级不能静默改写身份历史；
- 高风险事实在身份被挑战时必须受到用途限制。

## 九、验证结论

候选模型可以完成：

```text
发现误合并
→ 限制使用
→ 打开身份案件
→ 非破坏性拆分
→ 重绑认识对象
→ 重评世界状态和研判
→ 更正产品
```

场景支持 Mention 成为一等认识对象，并证明 Entity 合并不能实现为不可逆主键覆盖。

本场景已在 S5、S9 和 S10 完成复核并采纳。

## 十、关联

- [Entity、身份、别名与消歧模型](../04-domain-design/entity-identity-and-resolution-model.md)
- [SCN-0008：事实撤回与产品更正](./SCN-0008-fact-retraction-and-correction.md)
- [SCN-0011：多权威域事实并存](./SCN-0011-multiple-authority-domain-facts.md)
