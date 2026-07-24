# 领域设计

状态：Draft

这里深入设计天机阁的业务语义，包括：

- 限界上下文；
- Source、Document、Snapshot、Evidence、Claim、Fact；
- Person、Organization、Project、Event、Relationship；
- Signal、Assessment 和 Intelligence Product；
- 公共情报与租户私有覆盖层；
- 事实版本、有效时间和系统记录时间；
- 领域事件与业务不变量；
- 三省六部的领域职责边界。

领域模型不依赖具体数据库或消息中间件。

## 当前领域模型

- [核心对象主干](./core-object-spine.md)
