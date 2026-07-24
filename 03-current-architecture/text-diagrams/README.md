# 天机阁文字架构图集

状态：Accepted

## 一、用途

这里保存专门用于人类阅读、思考和审阅的文字架构图。

文字架构图不是正文中的装饰，而是天机阁的一等架构视图。它们用于：

- 让设计者在一个屏幕内形成整体空间感；
- 快速看出层级、边界、主干和反馈关系；
- 在深入正文前建立心智模型；
- 暴露职责重复、层级错位和遗漏；
- 作为后续专题讨论的共同底图；
- 为面试讲解提供从总览到局部的浏览路径。

## 二、图集索引

图号表示稳定身份，不再作为推荐阅读顺序。图集按架构问题分为六组。

### A：总览与一级职责

建议从这里建立心智模型：

- [TXT-0023：Skeleton v0.3 一屏全景图](./TXT-0023-skeleton-v0.3-one-screen-panorama.md)
- [TXT-0001：整体全景图](./TXT-0001-overall-landscape.md)
- [TXT-0002：三条核心闭环图](./TXT-0002-three-core-loops.md)
- [TXT-0003：十面关系与依赖图](./TXT-0003-ten-plane-relationships.md)
- [TXT-0008：十面二级能力总树](./TXT-0008-capability-trees.md)

### B：主体、组织与主权

回答谁参与、权力来自哪里、古代官制怎样映射：

- [TXT-0004：仙人—皇帝—三省六部图](./TXT-0004-emperor-and-court.md)
- [TXT-0005：系统上下文图](./TXT-0005-system-context.md)
- [TXT-0009：组织—技术映射图](./TXT-0009-organization-technology-mapping.md)
- [TXT-0013：主体生态与主权边界图](./TXT-0013-ecosystem-and-sovereignty.md)

### C：对象、运行与协作

回答信息怎样成为情报、任务怎样运行、各面怎样交互：

- [TXT-0006：核心对象流转图](./TXT-0006-core-object-spine.md)
- [TXT-0007：十面输入输出契约图](./TXT-0007-plane-contracts.md)
- [TXT-0010：核心状态机与异常路由图](./TXT-0010-state-machines-and-exceptions.md)
- [TXT-0012：端到端正常与失败场景图](./TXT-0012-end-to-end-scenarios.md)
- [TXT-0014：全局运行形态与转换图](./TXT-0014-operating-modes.md)
- [TXT-0015：七条全局流分离图](./TXT-0015-global-flow-separation.md)

### D：横切、尺度与生存

回答系统在什么范围运行、始终受什么约束、失控时保住什么：

- [TXT-0011：七维横切治理图](./TXT-0011-cross-cutting-governance.md)
- [TXT-0016：空间、时间与规模坐标图](./TXT-0016-spatiotemporal-and-scale.md)
- [TXT-0017：非功能与生存性总图](./TXT-0017-nonfunctional-and-survivability.md)
- [TXT-0018：负空间与未来替换图](./TXT-0018-negative-space-and-future-replacement.md)

### E：演进、风险与未来

回答怎样避免自证、怎样面对超级模型、整个架构怎样安全改变：

- [TXT-0021：循环权威与单点认知图](./TXT-0021-authority-cycles-and-cognitive-concentration.md)
- [TXT-0022：能力演进作用全域图](./TXT-0022-evolution-across-entire-architecture.md)

### F：闭合与压力验证

回答为什么当前骨架可以称为阶段闭合：

- [TXT-0019：Skeleton v0.2 闭合总图](./TXT-0019-skeleton-v0.2-closure.md)
- [TXT-0020：Skeleton v0.3 宏观压力测试图](./TXT-0020-skeleton-v0.3-macro-stress.md)

完整分层阅读树见[权威架构骨架索引](../architecture-skeleton-index.md)。

## 三、图示规范

### 编号

文字架构图使用 `TXT-NNNN` 编号。编号表示稳定身份，不表示重要程度。

### 每张图必须说明

- 这张图回答什么问题；
- 图中节点属于职责、状态、主体还是承载设施；
- 箭头表示控制、数据、依赖还是反馈；
- 哪些内容被有意省略；
- 对应的权威架构正文。

### 符号

```text
──▶  主要正向流
◀──  反馈或回流
══▶  控制、批准或期望状态下发
··▶  观察、审计或弱依赖
×    横切作用
[ ]  逻辑职责面或主体
( )  状态、产物或事件
{ }  横切维度或约束
```

具体图可以简化符号，但必须在图前说明。

## 四、维护规则

- 当前图只表达已接受架构；
- 候选设计应先留在迭代文档，不直接混入当前图集；
- 架构决定发生变化时，同一轮更新相关文字图；
- 一张图只回答一种主要问题，不强迫总览图承载全部细节；
- 树表达归属，关系图表达依赖，时序图表达过程，矩阵表达横切；
- 图与正文冲突时，以当前架构正文和已接受 ADR 为准，并立即修复图；
- 旧图若具有历史价值，标记为 `Superseded` 后移入对应迭代或归档，而不是直接删除。

## 五、与正文的关系

- 权威一级骨架：[总体一级逻辑骨架](../overall-logical-skeleton.md)
- 权威详细骨架：[整体架构骨架](../overall-architecture-skeleton.md)
- 决策依据：[架构决策](../../06-architecture-decisions/README.md)

文字图负责让架构“看得见”，正文负责让架构“说得清”，ADR 负责让架构“讲得出为什么”。
