# TXT-0024：Skeleton v1.0 基线封版图

状态：Accepted

## 一、这张图回答什么

它回答：

> 天机阁 Skeleton v1.0 究竟冻结了什么，后续专题又从哪里继续展开？

## 二、箭头语义

- `→`：进入下一层或形成下一阶段；
- `│`：共同约束；
- `⊣`：拒绝、制衡或阻止越权；
- `≠`：必须保持的语义分离；
- `…`：允许继续提高内部设计分辨率。

## 三、基线封版图

```text
                    仙人 / 有权人类
             目标、授权、停止、采用与最终责任
                           │
                           ▼
                  皇帝：统一智能中枢
                           │
               ┌───────────┼───────────┐
               ▼           ▼           ▼
          中书：规划   尚书：执行统筹   门下：审核封驳
               │           │           ⊣
               └───────────┼───────────┘
                           ▼
┌──────────────── Skeleton v1.0：五域十面 ────────────────┐
│                                                        │
│  体验与交付域     人机交互面 ─────── 情报产品面         │
│                                                        │
│  情报生产域       数据认知面 → 情报研判面               │
│                         ↘ 领域能力面 / 六部 ↗            │
│                                                        │
│  治理与执行域     智能治理面 ≠ 智能执行与运行面          │
│                                                        │
│  平台与知识域     数据底座面 ≠ 平台控制面                │
│                                                        │
│  演进与适应域     能力演进面                             │
│                                                        │
└────────────────────────────────────────────────────────┘
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
       六条架构主线      四轴七维      生存与负空间
    权力/情报/对象     作用域/辖域    最低生存内核
    运行/生存/演进     时间/规模      禁止权力组合
                        七维治理       未来替换边界
              │            │            │
              └────────────┼────────────┘
                           ▼
                 共同基础设施承载
         计算 / 存储 / 网络 / 部署 / 可观测底层

全程语义主干：

Document ≠ Snapshot ≠ Evidence ≠ Claim ≠ Proposition
Proposition + scoped Acceptance Decision → Fact Aggregate
Fact Aggregate ≠ Assessment ≠ Forecast ≠ Risk ≠ Recommendation
Product ≠ Edition ≠ Release ≠ Delivery ≠ Action
Agent Memory ≠ Intelligence Database

后续专题深化：

基线位置
→ 二级能力 …
→ 对象与 Schema …
→ 契约与状态 …
→ 运行与部署 …
→ 安全、容量、成本与场景 …
→ 反向验证基线
```

## 四、基线冻结与可变范围

```text
冻结语义：
定位、主权、十面、权威边界、对象分层、
六条主线、七流、四轴、七维、生存底线和演进权分离

允许变化：
模型、Agent 数量、Tool、Skill、Script、服务、
Schema、协议、数据库、部署、算法、参数和供应商
```

## 五、有意省略

本图不展开：

- 十面二级能力；
- 六部与官署内部结构；
- 对象字段和协议；
- 部署拓扑与容量；
- 详细角色权限；
- 专题设计优先级。

这些内容属于 v1.0 之后的设计分辨率提升，不是骨架缺口。

## 六、权威来源

- [Skeleton v1.0 基线声明](../skeleton-v1.0-baseline.md)
- [Skeleton v1.0 正式就绪评审](../../07-analysis-and-validation/skeleton-v1.0-readiness-review.md)
- [权威架构骨架索引](../architecture-skeleton-index.md)
- [总体一级逻辑骨架](../overall-logical-skeleton.md)
- [ADR-0016：情报语义基线](../../06-architecture-decisions/adrs/ADR-0016-adopt-scoped-versioned-intelligence-semantics-baseline.md)
- [TXT-0025：外层骨架终局总图](./TXT-0025-outer-skeleton-final-map.md)
