# SCN-0011：多权威域事实并存

状态：Accepted

提出日期：2026-07-27

## 一、目标

某企业监管登记显示法定代表人为甲；企业租户内部董事会决议显示乙已在当天被任命为代理负责人，但外部登记尚未更新；公开官网仍显示甲为负责人。

验证天机阁不会：

- 用租户私有材料改写公共知识；
- 用公共登记否认租户内部已经发生的管理变化；
- 把“法定代表人”“代理负责人”“实际主持工作”合并成同一命题；
- 为追求唯一答案丢弃权威域、有效时间和作用域。

## 二、参与主体

- 监管登记机构；
- 企业官网；
- 企业租户及董事会；
- 租户内有权提交者；
- 数据认知面；
- 吏部领域能力；
- 门下省；
- 大理寺；
- 平台控制和数据底座；
- 皇帝与情报产品面。

## 三、输入对象

```text
Public Snapshot P1
→ Evidence E1
→ Claim C1：
  “监管登记在 T1 时显示甲为法定代表人”

Public Snapshot P2
→ Evidence E2
→ Claim C2：
  “官网在 T1 时显示甲为负责人”

Tenant Private Snapshot P3
→ Evidence E3
→ Claim C3：
  “董事会在 T2 任命乙为代理负责人”
```

## 四、错误简化

```text
错误一：
C3 时间更新
→ 覆盖 C1

错误二：
C1 更官方
→ 拒绝 C3

错误三：
把法定代表人、负责人、代理负责人归一成同一字段
→ 只保留一个 current_leader
```

三种做法都会丢失现实结构。

## 五、正确路径

```text
1. 规范化三个 Claim 的 Proposition

C1:
role = legal_representative
authority_domain = regulatory_registry
valid/observed time = T1

C2:
role = website_displayed_leader
authority_domain = public_publisher
observed time = T1

C3:
role = acting_operational_leader
authority_domain = tenant_governance
effective time = T2

2. 实体和角色本体确认

甲、乙身份得到区分
三种角色不得静默合并

3. 分别验证并形成事实接受

F1 External Authority Fact:
监管登记在 T1 记录甲为法定代表人

F2 Public Baseline Fact:
官网在 T1 展示甲为负责人

F3 Tenant Accepted Fact:
租户董事会决议自 T2 起任命乙为代理负责人

4. 查询按上下文合成

公开查询：
→ 只能看到 F1、F2
→ 表达登记和官网当前可见状态

租户授权查询：
→ 可以同时看到 F1、F2、F3
→ 解释法定、公开展示和内部代理角色差异

5. 后续监管登记更新为乙

→ 新 Snapshot / Evidence / Claim
→ F1 新版本或新时段 Fact
→ 不回写成“乙从一开始就是法定代表人”
```

## 六、是否形成争议

本场景初始不产生真正 Logical Contradiction，因为：

- 角色定义不同；
- 权威域不同；
- 有效时间不同；
- 可见作用域不同。

如果董事会材料声称“乙已依法完成法定代表人变更”，而监管登记在同一时刻否认，则建立 Dispute Case。

## 七、Agent / Tool 分工

### Tool

- 作用域和权限强制；
- 时间、角色代码和字段校验；
- Evidence Anchor；
- Proposition 候选匹配；
- Fact 查询视图合成；
- 租户覆盖隔离；
- 版本和依赖记录。

### Agent / 人类

- 角色法律与业务语义解释；
- “负责人”模糊称谓解析；
- Claim 是否真正冲突；
- 租户决议的权威适用性；
- 产品如何表达差异而不误导。

## 八、异常分支

### A：租户提交者无权访问董事会材料

P3 与 E3 隔离，C3 不进入 Fact 接受流程；保留安全审计。

### B：租户要求公开发布 F3

重新执行用途、许可、隐私和发布审核。Tenant Accepted 不因租户要求自动变成 Public Baseline。

### C：董事会决议后来被撤销

F3 进入 Challenged / Retracted 或限定有效时间；定位依赖产品并更正。

### D：官网引用监管数据自动更新

保留来源 lineage，不能把官网与监管登记算作两个独立佐证来源。

## 九、验证不变量

- Fact 必须有 Acceptance Context；
- 权威域不同不自动形成矛盾；
- 租户 Fact 不能污染公共 Fact；
- 外部权威记录与现实业务状态可以并存；
- 角色和有效时间必须进入命题；
- Current Fact 是按查询上下文合成的视图；
- 权限控制不等于事实真假判断；
- 后续更新不重写历史有效时间。

## 十、验证结论

候选模型可以在不创造单一“全局真相槽”的情况下，保留公共登记、公开展示和租户内部事实。

场景支持：

- Fact 是带 Acceptance Context 的接受记录；
- Proposition 需要包含角色、时间和权威域；
- Public / Tenant / External Authority Fact 可以并存；
- 不同事实域的合成是查询视图，不是对象覆盖。

本场景已在 S4、S8 和 S10 完成复核并采纳。

## 十一、关联

- [Claim、争议、Fact 与裁定模型](../04-domain-design/claim-fact-and-adjudication-model.md)
- [SCN-0004：冲突证据与事实裁定](./SCN-0004-conflicting-evidence.md)
- [SCN-0008：事实撤回与产品更正](./SCN-0008-fact-retraction-and-correction.md)
