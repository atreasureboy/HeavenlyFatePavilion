# 平台与知识域能力树

状态：Accepted

## 一、数据底座面

核心问题：天机阁的资料、事实、世界状态、研判和运行历史如何可靠存在并被供应。

```text
数据底座面
├── 1. Source、Document 与 Snapshot 保存能力    [T]
├── 2. Evidence、Claim、Verification 与 Fact 能力[T]
├── 3. Entity、Relationship 与知识图谱能力      [T]
├── 4. Event、Change、时间线与双时态能力        [T]
├── 5. Assessment、Forecast、Risk 与产品记录能力[T]
├── 6. Mission、Decision、Run 与审计记录能力    [T]
├── 7. 全文、结构化、图、向量与时序索引能力    [T]
├── 8. 查询、订阅、变更流与数据供应能力         [T]
├── 9. 版本、血缘、纠正与影响传播能力           [T]
├── 10. 公共底座、租户覆盖与作用域能力          [T]
├── 11. 分区、复制、一致性、缓存与热点治理能力  [T]
└── 12. 保留、归档、删除、备份与恢复能力        [T]
```

### 数据分层

```text
原始资料层
→ Source / Document / Snapshot

认识论层
→ Evidence / Claim / Verification / Fact

世界状态层
→ Entity / Relationship / Event / Change

研判层
→ Assessment / Forecast / Risk / Recommendation

交付层
→ Product / Alert

系统记录层
→ Mission / Decision / Run / Event / Evolution Evidence
```

数据底座保存权威状态，但不自行决定对象的业务语义。

## 二、平台控制面

核心问题：共享多租户平台长期应该以什么身份、能力、策略和版本运行。

```text
平台控制面
├── 1. 租户、组织、成员与生命周期管理能力      [T]
├── 2. Court Profile 与皇帝身份配置能力         [T]
├── 3. 身份、角色、策略与授权定义能力           [T]
├── 4. Agent Template 与能力注册能力            [T]
├── 5. Tool、Skill、连接器与凭证目录能力        [T]
├── 6. 模型目录、网关、路由与供应策略能力       [T]
├── 7. Workflow、Mission 类型与产品模板能力     [T]
├── 8. 来源登记、许可与采集策略配置能力         [T]
├── 9. 预算、配额、计量与资源政策能力           [T]
├── 10. 配置、功能开关与策略组合能力            [T]
├── 11. 版本、灰度、发布、回滚与生效能力        [T]
└── 12. 期望状态与实际状态协调能力              [M]
```

### 控制面边界

```text
平台控制面：
模板、策略、目录、预算、版本、期望状态

智能执行与运行面：
实例、调用、进度、失败、实际状态
```

Agent 可以辅助产生配置候选，但权威控制状态必须版本化、可验证、可回滚。
