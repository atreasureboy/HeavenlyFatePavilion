# TXT-0006：天机阁核心对象流转图

状态：Accepted

## 这张图回答什么

仙人的意图如何经过治理、执行、资料处理、事实验证和情报研判，最终形成产品与反馈；十个一级面通过哪些对象连接。

箭头表示创建新的有类型对象及血缘，不代表把原对象原地改名。

## 一、核心对象总主干

```text
┌──────────────────────────── 人机交互 ────────────────────────────┐
│                                                                  │
│  (原始表达) ──捕获──▶ [Intent]                                   │
│     用户目标              │                                      │
│     问题 / 纠正           │                                      │
└───────────────────────────┼──────────────────────────────────────┘
                            ▼
┌──────────────────────────── 智能治理 ────────────────────────────┐
│                                                                  │
│  [Intent] ──解释──▶ [Mission] ──规划──▶ [Plan] ──审议──▶ [Decision]│
│                      为什么做          怎样做          是否批准  │
└──────────────────────────────────────────┬───────────────────────┘
                                           ▼
┌────────────────────────── 智能执行与运行 ────────────────────────┐
│                                                                  │
│  [Plan Version] ──实例化──▶ [Run] ──分解──▶ [Task]                │
│                                │             │                   │
│                                └──产生──▶ [Execution Event]      │
│                                              [Artifact]           │
└──────────────────────────────────────────┬───────────────────────┘
                                           ▼
┌──────────────────────────── 数据认知 ────────────────────────────┐
│                                                                  │
│  [Source] ──获取──▶ [Document] ──捕获──▶ [Snapshot]               │
│                                              │                   │
│                                              ▼                   │
│  [Evidence] ◀──定位提取── [Snapshot]                              │
│      │                                                           │
│      └──支持 / 反驳──▶ [Claim] ──验证──▶ [Verification Record]   │
│                                           │                      │
│                                           ▼                      │
│                                        [Fact]                    │
│                                           │                      │
│               ┌───────────────────────────┼──────────────────┐   │
│               ▼                           ▼                  ▼   │
│           [Entity]                 [Relationship]        [Event] │
│               └───────────────────────────┬──────────────────┘   │
│                                           ▼                      │
│                                        [Change]                  │
└──────────────────────────────────────────┬───────────────────────┘
                                           ▼
┌──────────────────────────── 情报研判 ────────────────────────────┐
│                                                                  │
│  Fact / Entity / Event / Change ──检测──▶ [Signal]                │
│                                                │                 │
│                                                ▼                 │
│                                          [Assessment]            │
│                                           当前态势判断            │
│                                           │          │           │
│                                           ▼          ▼           │
│                                      [Forecast]    [Risk]         │
│                                       未来判断      风险判断       │
│                                           └────┬─────┘           │
│                                                ▼                 │
│                                         [Recommendation]         │
└──────────────────────────────────────────┬───────────────────────┘
                                           ▼
┌──────────────────────────── 情报产品 ────────────────────────────┐
│                                                                  │
│ Fact / Assessment / Forecast / Risk                              │
│                    ──组织与审核──▶ [Intelligence Product]         │
│                                         │                        │
│                         Risk / Change ──触发──▶ [Alert]           │
└──────────────────────────────────────────┬───────────────────────┘
                                           ▼
┌────────────────────────── 反馈与能力演进 ────────────────────────┐
│                                                                  │
│  [Feedback] ───────────────┬──────────▶ 修正 Intent / Mission     │
│                            ├──────────▶ 质疑 Claim / Fact         │
│                            ├──────────▶ 校准 Forecast / Risk      │
│                            └──────────▶ 改进 Product              │
│                                                                  │
│  质量 / 成本 / 失败 / 反馈 ──形成──▶ [Evolution Evidence]         │
│                                             │                    │
│                                             ▼                    │
│                               [Capability Change Candidate]      │
│                                  实验 → 评测 → 治理 → 发布       │
└──────────────────────────────────────────────────────────────────┘
```

## 二、十面与对象连接图

```text
[人机交互面]
    Intent / Feedback
          │
          ▼
[智能治理面]
    Mission / Plan / Decision
          │
          ▼
[智能执行与运行面] ◀──── Template / Policy ──── [平台控制面]
    Run / Task / Execution Event
          │
          ▼
[数据认知面] ◀──── 领域本体与规则 ───────────── [领域能力面]
    Evidence / Claim / Fact
    Entity / Relationship / Event / Change
          │
          ▼
[情报研判面] ◀──── 领域判断能力 ─────────────── [领域能力面]
    Signal / Assessment / Forecast / Risk
          │
          ▼
[情报产品面]
    Product / Alert
          │
          ▼
      User Feedback

[数据底座面]
    保存所有需要持久化的权威对象、版本与血缘

[能力演进面]
    Evolution Evidence / Experiment / Change Candidate
```

## 三、认识论分层

```text
外部世界
   │
   ▼
Source
   │ 获取
   ▼
Document
   │ 在特定时点捕获
   ▼
Snapshot
   │ 定位
   ▼
Evidence
   │ 支持或反驳
   ▼
Claim
   │ 验证、反证、裁定
   ▼
Fact / Disputed Claim
   │ 综合与解释
   ▼
Assessment
   │ 面向未来推演
   ▼
Forecast
   │ 影响与可能性分析
   ▼
Risk
   │ 面向受众组织
   ▼
Intelligence Product
```

不得跨越中间语义：

```text
Document ─X─▶ Fact
模型输出 ─X─▶ Fact
Claim ─X─▶ Assessment
Forecast ─X─▶ 已发生事实
Recommendation ─X─▶ 已授权行动
```

## 四、对象不会被原地“升级”

```text
Snapshot ──creates──▶ Evidence
Evidence ──supports──▶ Claim
Verification ──accepts──▶ Fact
Fact ──informs──▶ Assessment
Assessment ──supports──▶ Forecast

每一步：
保留输入对象
+ 创建新对象
+ 建立关系
+ 记录主体、方法、版本、时间和作用域
```

## 五、纠正与争议

```text
                   ┌── challenged_by ──▶ [Disputed Claim]
                   │
[Old Fact] ────────┼── superseded_by ──▶ [New Fact]
                   │
                   └── retracted_by ───▶ [Retraction Decision]

历史保留，引用传播，受影响的 Assessment、Forecast、Risk 和 Product
进入重新评估，而不是静默改写。
```

## 六、有意省略

- 完整字段 Schema；
- ID 编码；
- 证据等级算法；
- 详细状态转移条件；
- 存储引擎；
- 外部 Action 和补偿对象；
- 跨租户联合调查协议。

## 七、对应正文

- [核心对象主干](../../04-domain-design/core-object-spine.md)
- [系统上下文与边界](../system-context.md)
- [ADR-0008：采用有类型、可追溯的核心对象主干](../../06-architecture-decisions/adrs/ADR-0008-typed-traceable-core-object-spine.md)
