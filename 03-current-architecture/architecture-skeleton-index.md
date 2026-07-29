# 天机阁权威架构骨架索引

状态：Accepted

整理日期：2026-07-24

当前骨架成熟度：`Skeleton v1.0` 正式基线

## 一、索引定位

本文是天机阁当前架构骨架的稳定导航树。

它不重新定义架构，也不复制所有正文，而是解决三个问题：

1. 当前骨架究竟由哪些层组成；
2. 每一层回答什么问题；
3. 哪份文件是该问题的权威入口。

整理原则：

```text
先看边界
→ 再看职责
→ 再看组织映射
→ 再看对象与运行
→ 再叠加坐标和约束
→ 再看生存与演进
→ 最后用验证证明闭合
```

已有文件保持原位，编号和链接不变。骨架整理只重组导航，不抹除迭代历史。

## 二、骨架总树

```text
天机阁架构骨架
├── L0 定位与不可变前提
│   ├── 项目性质、愿景、边界与质量属性
│   └── 人类主权、证据分层与越权禁区
│
├── L1 外部上下文与主权
│   ├── 仙人、租户、权利人、监管、供应链、同行和对抗主体
│   ├── 系统边界与责任边界
│   └── 人类、平台、租户、数据、事实、产品和行动主权
│
├── L2 一级逻辑职责
│   ├── 五个视觉域
│   ├── 十个一级面
│   └── 共同基础设施承载
│
├── L3 组织治理映射
│   ├── 皇帝：统一智能中枢
│   ├── 三省：规划、执行统筹、审核封驳
│   ├── 六部：领域能力
│   └── 公共官署：入口、知识、审计、裁定、安全和产品
│
├── L4 情报语义与对象主干
│   ├── Source / Document / Evidence / Claim / Fact
│   ├── Entity / Relationship / Event / Change
│   ├── Assessment / Forecast / Risk / Product
│   └── 版本、血缘、纠正与演进证据
│
├── L5 全局运行与协作
│   ├── 十种运行形态
│   ├── 七条全局流
│   ├── 十面契约
│   └── 状态、异常与恢复路由
│
├── L6 架构坐标与横切治理
│   ├── 作用域、辖域、时间、规模四轴
│   └── 租户、权限、证据、预算、质量、审计、生命周期七维
│
├── L7 生存、负空间与信任边界
│   ├── 四类完整性、六阶段防线、八类失效域
│   ├── 最低生存内核
│   ├── 非职责与禁止权力组合
│   └── 默认不可信与未来可替换边界
│
├── L8 全域能力演进
│   ├── 九类演进对象
│   ├── 五级变更半径
│   ├── 三层嵌套演进循环
│   └── 提议、验证、采用、发布和生效权分离
│
└── L9 骨架验证与版本闭合
    ├── 标准场景与覆盖矩阵
    ├── v0.2 集中闭合
    ├── v0.3 压力、循环权威与单点认知
    ├── v0.3 一屏全景和一级稳定性复核
    └── v1.0 正式就绪评审与基线冻结
```

## 三、九层权威来源

| 层 | 核心问题 | 权威正文 | 首选文字图 |
|---|---|---|---|
| L0 定位 | 为什么设计、什么绝不能变 | [架构总纲](../00-project-foundation/architecture-charter.md) | [TXT-0023 一屏全景](./text-diagrams/TXT-0023-skeleton-v0.3-one-screen-panorama.md) |
| L1 上下文 | 谁在系统内外、权力来自哪里 | [系统上下文](./system-context.md)、[生态与主权](./ecosystem-and-sovereignty.md) | [TXT-0005](./text-diagrams/TXT-0005-system-context.md)、[TXT-0013](./text-diagrams/TXT-0013-ecosystem-and-sovereignty.md) |
| L2 职责 | 系统由哪些稳定一级责任组成 | [总体一级逻辑骨架](./overall-logical-skeleton.md)、[整体架构骨架](./overall-architecture-skeleton.md) | [TXT-0001](./text-diagrams/TXT-0001-overall-landscape.md)、[TXT-0003](./text-diagrams/TXT-0003-ten-plane-relationships.md) |
| L3 组织 | 皇帝、三省六部和官署如何映射 | [组织—技术映射](./organization-mapping/README.md) | [TXT-0004](./text-diagrams/TXT-0004-emperor-and-court.md)、[TXT-0009](./text-diagrams/TXT-0009-organization-technology-mapping.md) |
| L4 语义 | 数据如何成为事实、趋势和产品 | [核心对象主干](../04-domain-design/core-object-spine.md)、[统一分类法](../08-reference-models/unified-language-and-object-taxonomy.md)、[语义不变量](../08-reference-models/intelligence-semantic-invariants.md) | [TXT-0006](./text-diagrams/TXT-0006-core-object-spine.md) |
| L5 运行 | 系统怎样协作、流转、失败和恢复 | [运行形态](./operating-modes.md)、[七流](./global-flows.md)、[契约](./plane-contracts.md)、[状态机](../08-reference-models/state-machines/README.md) | [TXT-0014](./text-diagrams/TXT-0014-operating-modes.md)、[TXT-0015](./text-diagrams/TXT-0015-global-flow-separation.md) |
| L6 坐标治理 | 在什么范围和约束下运行 | [时空规模](./spatiotemporal-and-scale-skeleton.md)、[横切治理](./cross-cutting/README.md) | [TXT-0016](./text-diagrams/TXT-0016-spatiotemporal-and-scale.md)、[TXT-0011](./text-diagrams/TXT-0011-cross-cutting-governance.md) |
| L7 生存边界 | 失控时保什么、什么组合必须禁止 | [生存性](./nonfunctional-and-survivability-skeleton.md)、[负空间](./negative-space-and-future-replacement.md) | [TXT-0017](./text-diagrams/TXT-0017-nonfunctional-and-survivability.md)、[TXT-0018](./text-diagrams/TXT-0018-negative-space-and-future-replacement.md) |
| L8 演进 | 整个架构怎样改变而不自我授权 | [全域演进](./evolution-across-entire-architecture.md) | [TXT-0022](./text-diagrams/TXT-0022-evolution-across-entire-architecture.md) |
| L9 验证 | 为什么相信骨架已经正式闭合 | [完整性审计](../07-analysis-and-validation/skeleton-completeness-assessment.md)、[v0.3 稳定性复核](../07-analysis-and-validation/skeleton-v0.3-closure-and-stability-review.md)、[v1.0 正式评审](../07-analysis-and-validation/skeleton-v1.0-readiness-review.md) | [TXT-0019](./text-diagrams/TXT-0019-skeleton-v0.2-closure.md)、[TXT-0020](./text-diagrams/TXT-0020-skeleton-v0.3-macro-stress.md)、[TXT-0024](./text-diagrams/TXT-0024-skeleton-v1.0-baseline.md) |

## 四、不要混淆的三种结构

### 4.1 垂直职责

五域十面回答：

> 哪一类稳定责任由谁拥有？

只有十面是一级职责单元。

### 4.2 横向关系

组织映射、运行形态、七流、四轴、七维、生存防线和负空间回答：

> 十面怎样组合、处于什么环境、受什么约束？

它们不能被误升格成新的职责面。

### 4.3 深度分辨率

能力、对象、契约、状态、场景、部署和参数回答：

> 一个已经确定的位置内部要设计到多细？

当前骨架已经形成 v1.0 基线。后续可以提高设计分辨率，但不能因为局部实现便利而静默改写一级职责。

## 五、骨架的六条主线

### 5.1 权力线

```text
外部原生权威
→ 皇帝统一承接
→ 三省治理与制衡
→ 平台确定性强制
→ 运行限域执行
→ 审计、人类接管与责任
```

### 5.2 情报线

```text
掌数 → 联结 → 察变 → 推势 → 卜算 → 警险 → 产品
```

### 5.3 对象线

```text
Document → Evidence → Claim → Fact
→ Entity / Event / Change
→ Assessment / Forecast / Risk → Product
```

### 5.4 运行线

```text
Mandate / Mission → Plan → Run → Task / Agent / Tool
→ Product / Action / Effect → Feedback
```

### 5.5 生存线

```text
预防 → 感知 → 遏制 → 延续 → 恢复 → 纠正与演进
```

### 5.6 演进线

```text
观察 → 缺口 → 候选 → 实验 → 评测 → 采用
→ 发布 → 实际结果 → 保留 / 调整 / 回滚 / 影响纠正
```

六条线相互交叉，但任何一条都不能代替另一条。

## 六、骨架稳定中心

```text
仙人 / 有权人类
→ 原生目标、授权、停止与最终责任

皇帝
→ 统一体验与全局协调
≠ 无限权限超级 Agent

三省
→ 规划、执行统筹、审核三种可区分权力
≠ 三个固定模型调用

六部
→ 领域能力包
≠ 六套独立平台

数据底座
→ 长期知识与权威记录
≠ Agent Memory

基础设施
→ 共同承载
≠ 一级逻辑权威

能力演进
→ 覆盖全域
≠ 拥有全域采用权
```

## 七、三种阅读路线

### 7.1 五分钟总览

1. [TXT-0024 Skeleton v1.0 基线封版图](./text-diagrams/TXT-0024-skeleton-v1.0-baseline.md)
2. [总体一级逻辑骨架](./overall-logical-skeleton.md)
3. [核心对象主干](../04-domain-design/core-object-spine.md)
4. [v0002 情报语义基线就绪评审](../07-analysis-and-validation/v0002-intelligence-semantics-readiness-review.md)

### 7.2 三十分钟架构理解

1. L1 外部上下文与主权；
2. L2 十面职责；
3. L3 三省六部映射；
4. L4 对象主干、统一分类与语义不变量；
5. L5 运行形态与七流；
6. L6 横切治理；
7. L7 生存与负空间；
8. L8 全域演进。

### 7.3 面试讲解

```text
项目为什么存在
→ 一屏全景
→ 十面为何这样拆
→ 三省六部为何不是装饰
→ Agent / Tool 如何划分
→ 数据怎样成为可信情报
→ 多租户、成本、失控和未来模型如何处理
→ 用压力测试和 ADR 解释取舍
```

## 八、文件角色

| 文件类型 | 作用 | 不应该承担 |
|---|---|---|
| 骨架索引 | 稳定导航与层级关系 | 重复全部正文 |
| 当前架构正文 | 当前生效定义 | 保存历史争论 |
| 文字图 | 建立心智模型 | 静默创造新决定 |
| ADR | 解释为什么这样选 | 代替当前架构 |
| 迭代记录 | 保存演进过程 | 冒充当前结论 |
| 验证报告 | 攻击并证明骨架 | 直接拥有架构职责 |
| 场景 | 暴露跨面断点 | 代替领域和协议 |

## 九、当前阶段

```text
已完成：
Skeleton v0.1 第一轮内部闭合
Skeleton v0.2 第二轮宏观闭合
Skeleton v0.3 第三轮宏观闭合
Skeleton v1.0 正式就绪评审与基线冻结

当前下一步：
按骨架分区逐步提高专题设计分辨率

继续禁止：
专题设计静默改变一级职责、主权、不变量或全局关系
```

## 十、维护规则

1. 新宏观结构先判断属于九层中的哪一层；
2. 只有稳定职责所有者才考虑新增一级面；
3. 新正文必须从本索引或所在分区 README 可达；
4. 新文字图继续使用 `TXT-NNNN`，但按问题分组阅读；
5. 历史复核留在分析与迭代目录，不与当前定义混写；
6. 当前权威发生变化时，同轮更新本索引、一屏图和受影响专题图；
7. 不为“整理目录”移动已有文件，除非专门迭代证明收益大于链接与历史成本。

## 十一、关联

- [Skeleton v0.3 一屏全景图](./text-diagrams/TXT-0023-skeleton-v0.3-one-screen-panorama.md)
- [当前架构入口](./README.md)
- [文字架构图集](./text-diagrams/README.md)
- [文档与迭代骨架](../DOCUMENTATION-STRUCTURE.md)
- [Skeleton v0.3 闭合与一级稳定性复核](../07-analysis-and-validation/skeleton-v0.3-closure-and-stability-review.md)
- [Skeleton v1.0 基线声明](./skeleton-v1.0-baseline.md)
- [Skeleton v1.0 正式就绪评审](../07-analysis-and-validation/skeleton-v1.0-readiness-review.md)
