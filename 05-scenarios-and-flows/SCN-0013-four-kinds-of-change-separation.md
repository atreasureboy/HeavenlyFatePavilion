# SCN-0013：四类变化分离

状态：Proposed

提出日期：2026-07-27

## 一、目标

天机阁持续监测某企业高管页面。在同一轮监测中发生：

1. 官网将页面模板和排序方式修改；
2. 天机阁升级正文抽取模型；
3. 新资料证明系统过去把两名同名高管误合并；
4. 企业今天真实发布任命公告，高管职位发生变化。

验证系统不会把四种变化混成一个 `changed=true`。

## 二、输入状态

```text
Document D1：企业高管页面
Snapshot P1：昨日页面
Artifact A1：parser v1 提取结果
Entity E1：旧聚合人物
Relationship R1：E1 holds_role CFO
World State W1：昨日可见世界状态
```

## 三、四条变化路径

### 路径 A：Content Diff

```text
P1 ↔ P2
→ 页面模板、DOM 和排序变化
→ 文本语义基本相同
→ Content Diff CD1
→ Classified NonMaterial
→ 不产生 World Change
```

### 路径 B：Extraction Diff

```text
same Snapshot P2
parser v1 → Artifact A2
parser v2 → Artifact A3
→ 新模型正确识别职位生效日期
→ Extraction Diff XD1
→ 创建新的 Claim Candidate
→ 不把解析差异写成来源修改
```

### 路径 C：Knowledge Revision

```text
新 Evidence 证明 E1 实际包含两名同名人物
→ Entity Resolution Case
→ E1 Split 为 E-A / E-B
→ 原 Relationship / Fact 被挑战
→ Knowledge Revision KR1
→ 历史世界视图按新知识重建
→ 现实并未在发现当天“分裂成两个人”
```

### 路径 D：World Change

```text
任命公告 Snapshot P3
→ Evidence
→ Claim：
  “E-A 自今日起卸任 CFO，E-C 接任”
→ Verification / Fact Acceptance
→ Resignation Event EV1
→ Appointment Event EV2
→ R1 Ended
→ 新 HoldsRole Relationship R2 Active
→ World Change WC1
```

## 四、统一时间线

```text
现实有效时间：
KR1 所纠正的两人身份一直不同
WC1 在今日发生

来源内容时间：
P2 模板变化在本轮监测前发生
P3 任命公告在今日发布

处理时间：
parser v2 在今日启用

认识时间：
天机阁在今日发现旧误合并并接受 KR1
```

四条时间不能压成一个 `updated_at`。

## 五、对象与触发结果

| 对象 | 是否触发告警 | 说明 |
|---|---|---|
| CD1 | 否或技术告警 | 页面结构变化，无业务语义 |
| XD1 | 质量复核 | 处理版本改变 |
| KR1 | 纠正告警 | 历史知识和产品可能受影响 |
| WC1 | 业务风险/订阅告警 | 现实任职变化 |

技术消费者、知识治理消费者和业务用户接收不同事件，不能订阅同一个无类型 Change。

## 六、Agent / Tool 分工

### Tool

- Snapshot 哈希和 DOM Diff；
- 同 Snapshot 多解析版本比较；
- Entity Split 影响查询；
- 时间线和 Relationship 状态投影；
- Change 类型路由；
- 订阅、缓存和依赖失效。

### Agent / 人类

- 页面变化是否具有业务意义；
- 抽取差异是否修复错误；
- 身份误合并的拆分决定；
- 任命公告的职位与生效语义；
- Change 重要性和产品影响。

## 七、异常分支

### A：P3 只是官网删除旧高管名字，没有任命公告

只能形成 Content Diff 和 “no longer observed”，不能直接形成卸任 Event。

### B：parser v2 产生更多错误

形成 Extraction Diff 和能力质量 Evidence，必要时回滚模型；不改来源历史。

### C：身份 Split 仍有 Mention 无法归属

保持 Unresolved，相关 Fact 和 Relationship 进入限制使用。

### D：任命公告后来被撤回

触发新的 Content Diff、Claim、Knowledge Revision 和可能的 World Change 复核，不能删除 EV1 / EV2 历史。

## 八、验证不变量

- 四种变化使用不同类型和时间；
- Diff 不自动成为 World Change；
- 处理版本变化不改写来源历史；
- 认识修订不伪装成现实在今日变化；
- Event 与 Relationship 同时保留；
- Entity Split 触发世界模型重评；
- 告警按 Change 类型和用途路由；
- 因果不能从时间先后自动推断。

## 九、验证结论

候选模型可以在一次监测 Run 中同时处理：

```text
Content Diff
Extraction Diff
Knowledge Revision
World Change
```

它们共享血缘和调度，但不共享语义、权威、时间或消费者。

本场景将在 S6 研判、S8 时间与 S9 血缘专题后复核，当前保持 `Proposed`。

## 十、关联

- [世界状态、事件与变化模型](../04-domain-design/world-state-event-and-change-model.md)
- [SCN-0010：来源变化与证据保全](./SCN-0010-source-mutation-and-evidence-preservation.md)
- [SCN-0012：实体误合并与拆分](./SCN-0012-entity-merge-split-correction.md)
