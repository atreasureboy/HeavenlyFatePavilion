# TXT-0009：天机阁组织—技术映射图

状态：Accepted

## 这张图回答什么

皇帝、三省、六部和公共官署分别映射到哪些一级面和技术形态，官制组织为什么不能直接当作技术分层。

## 一、五层映射

```text
[叙事机构]
     │
     ▼
[逻辑职责与权力]
     │
     ▼
[十面能力节点]
     │
     ▼
[Agent / Tool / Service / Policy / Store]
     │
     ▼
[按需实例 + 持久状态 + 审计记录]
```

## 二、皇帝与三省

```text
仙人
  │
  ▼
┌────────────────────────────────────────────────────────────┐
│ 皇帝                                                       │
│ Stable Identity + Court Profile                            │
│                                                            │
│ 人机交互面：皇帝交互投影                                   │
│ 智能治理面：皇帝治理 Agent Role                            │
│ 运行面：按会话 / 事件 / Mission 创建实例                   │
└───────────────────────┬────────────────────────────────────┘
                        │
        ┌───────────────┼───────────────────┐
        ▼               ▼                   ▼
┌──────────────┐ ┌──────────────┐  ┌────────────────┐
│ 中书省        │ │ 尚书省        │  │ 门下省          │
│ Planning      │ │ Orchestration │  │ Review          │
│ Agent Role    │ │ Agent Role    │  │ Agent + Gates   │
│ Mission/Plan  │ │ 跨部统筹      │  │ 反证/封驳/降级  │
└──────────────┘ └──────┬───────┘  └────────────────┘
                        │
                        ▼
                 Runtime Scheduler

尚书省决定“调什么、为什么”
Scheduler 决定“何时跑、在哪跑、如何重试”
```

## 三、六部

```text
                         领域能力面
┌────────────────────────────────────────────────────────────┐
│                  Ministry Capability Template               │
│ 本体 · 来源 · 规则 · Agent · Tool · 评测 · 产品扩展       │
└───────────────────────────┬────────────────────────────────┘
                            │
       ┌────────┬────────┬──┴─────┬────────┬────────┐
       ▼        ▼        ▼        ▼        ▼        ▼
    [吏部]   [户部]   [礼部]   [兵部]   [刑部]   [工部]
    人物     商业     观点     地缘     法规     技术
    组织     资源     传播     安全     风险     工程

每部：
部级 Agent Role + Tool/Skill/Model + 领域本体与规则

共享：
数据认知主干 + 情报研判主干 + Runtime + 数据底座 + 控制面
```

## 四、容易跨面的户部和工部

```text
户部
├── 商业与资源领域判断             → 领域能力面
├── 成本解释与资源建议             → 户部 Agent
├── Token / 算力计量               → 平台控制能力
├── 预算和配额权威状态             → 平台控制面
└── “哭穷”                         → Budget Pressure Event

工部
├── 技术与工程领域判断             → 领域能力面
├── 采集和解析                     → 数据认知面
├── Agent / Tool 执行              → 智能运行面
├── 存储和索引                     → 数据底座面
├── Tool / Model 目录              → 平台控制面
└── 计算、网络和部署               → 基础设施
```

组织可以协调跨面建设，不能吞并各面的逻辑所有权。

## 五、公共官署

```text
通政司
→ Gateway / Adapter / Contract Ingress
→ 人机交互面

文渊阁
→ Object / Content Store
→ 数据底座面：原始资料

国史馆
→ Evidence / Fact / World State Data System
→ 数据底座面：权威历史

钦天监
→ Monitor / Trigger / Scheduler Rule / Signal Event
→ 运行、认知、研判、产品、控制的跨面组合

大理寺
→ Conflict Engine + Case Workflow + 可选 Agent
→ 智能治理面

都察院 / 御史台
→ Audit / Quality / Trace Analysis + 可选 Agent
→ 智能治理面，横切观察全域

翰林院
→ Template / Renderer / Citation + 可选 Agent
→ 情报产品面

枢密院
→ IAM / Policy Engine / Tool Gateway
→ 平台控制面
```

## 六、核心反等式

```text
皇帝身份 ≠ 永久常驻模型
三省权力 ≠ 三个固定流水线步骤
尚书省 ≠ Scheduler
六部 ≠ 六套平台
吏部 ≠ Agent Registry
户部建议 ≠ 预算强制点
工部 ≠ 基础设施所有逻辑
钦天监 ≠ 第二套运行时
国史馆 ≠ 单一数据库
枢密院 ≠ 安全 Agent
翰林院润色 ≠ 扩大结论
```

## 七、对应正文

- [组织—技术映射](../organization-mapping/README.md)
- [皇帝与三省](../organization-mapping/01-emperor-and-three-departments.md)
- [六部映射](../organization-mapping/02-six-ministries.md)
- [公共官署](../organization-mapping/03-public-offices.md)
- [ADR-0011：官制组织与技术所有权分离](../../06-architecture-decisions/adrs/ADR-0011-separate-narrative-organization-from-technical-ownership.md)
