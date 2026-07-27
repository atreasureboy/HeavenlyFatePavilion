# SCN-0010：来源变化与证据保全

状态：Proposed

提出日期：2026-07-27

## 一、目标

某公司官网最初宣布一名高管已经离任，天机阁据此形成 Snapshot、Evidence 和 Claim。两天后网页在原 URL 上被静默修改，离任表述消失；同时新版正文因动态加载只被抓取了一半。

验证系统能否：

- 保留旧资料而不被覆盖；
- 区分来源内容变化、抓取不完整和现实变化；
- 防止新半截页面成为虚假反证；
- 找到所有依赖旧 Evidence 的对象；
- 在多租户复用和许可约束下正确处置。

## 二、参与者与架构面

- Source Principal：公司；
- Source System / Endpoint：官网与原 URL；
- 钦天监：变化触发；
- 工部 Tool：抓取、哈希、完整性和 Diff；
- 数据认知面：Document 身份、Evidence 和 Change 候选；
- 吏部：任职语义解释；
- 门下省：证据充分性复核；
- 数据底座面：不可变资料、血缘和依赖记录；
- 平台控制面：租户、许可、缓存和保留策略；
- 情报产品面：受影响档案和告警。

## 三、前置状态

```text
Source S1 = Active
Document D1 = “高管变动公告”
Snapshot P1 = 完整捕获，hash=h1
Evidence E1 = 锚定“已离任”段落
Claim C1 = “甲于 T 日离任”
多个租户产品引用 C1 或其后续 Fact
```

## 四、主路径

```text
钦天监发现 URL 内容哈希变化
→ 创建新的获取任务和 Acquisition Record
→ 工部得到 P2，hash=h2
→ 完整性检查发现 DynamicUnresolved + Partial
→ P2 仍被不可变保存，但标记 Partial
→ 产生 Content Diff：
   “离任段落在 P2 捕获中不存在”
→ 不直接产生 World Change
→ 数据认知面判断：
   缺失可能来自页面真实修改，也可能来自捕获不完整
→ 创建 Document Revision 候选和补采任务
→ 第二获取路径得到完整 P3，hash=h3
→ P3 确认原 URL 内容已修改
→ 建立 P1 → P3 的 revises 关系
→ 从 P3 提取 Evidence E2：
   “当前正文不再包含离任表述”
→ E2 只证明新版正文内容，不直接反驳离任事实
→ 吏部检查公告时间、任职口径和其他独立来源
→ 门下决定 C1 保持、进入争议或要求进一步验证
→ 数据底座保存全部版本、处理链和决定
→ 受影响产品按结论进入无变化、复核或更正队列
```

## 五、Agent 与确定性组件

### Tool / Script

- 哈希与不可变保存；
- 动态页面捕获完整性检测；
- Snapshot 版本和 Content Diff；
- Anchor 有效性检查；
- 依赖图查询；
- 租户权限与许可复判。

### Agent / 人类

- 判断 Document 是否仍是同一逻辑资料；
- 判断段落消失的语义；
- 区分网页改稿与现实任职变化；
- 解释来源独立性；
- 对高影响产品决定复核深度。

## 六、异常分支

### A：后续始终无法完整捕获

P2 保持 `Partial`；系统不得把“未抓到”表达为“页面不存在该内容”。产品只能披露捕获受损和不确定性。

### B：公司声明旧页面被错误发布

创建 Source Correction / Retraction 关系，保留 P1；进入 Claim / Fact 重新验证，不删除旧 Evidence。

### C：许可撤销

禁止新用途和展示，按策略决定删除可删除副本或转入受限留置；依赖对象保留可审计的不可见引用和影响状态。

### D：租户私有镜像仍保留旧页面

镜像建立独立 Source / Document lineage；不能因内容相同就假定来源独立，但可作为捕获完整性的旁证。

### E：解析器升级后旧 P1 产生不同正文

创建新 Derived Artifact 和 Extraction Diff；原 P1 不变，不得伪装成来源在过去发生变化。

## 七、验证的不变量

- Snapshot 不可覆盖；
- Partial 捕获不能冒充完整证据；
- Content Diff 不等于 World Change；
- Evidence 必须锚定明确版本；
- 新来源内容不能静默改写旧 Claim；
- 共享资料必须按租户重新判权；
- 权利变化和 Anchor 失效必须触发影响分析；
- Agent 判断不能绕过原始材料和处理血缘。

## 八、验证结论

候选模型能够区分：

```text
页面内容变化
抓取完整性变化
解析结果变化
现实世界变化
知识接受状态变化
```

没有发现新的一级架构面，但确认需要：

- Acquisition Record；
- Snapshot 捕获完整性；
- Document lineage；
- Evidence Anchor；
- Rights / Usage Envelope；
- 依赖影响传播。

本场景将在 S3 和 S8 后再次复核，当前保持 `Proposed`。

## 九、关联

- [Source、Document、Snapshot 与 Evidence 模型](../04-domain-design/source-document-snapshot-evidence-model.md)
- [统一语言与对象分类法](../08-reference-models/unified-language-and-object-taxonomy.md)
- [SCN-0008：事实撤回与产品更正](./SCN-0008-fact-retraction-and-correction.md)
