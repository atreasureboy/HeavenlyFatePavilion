# v0003 输入

状态：Draft

## 已确认输入

1. 用户坚持从整体到局部，不采用“先做小系统再扩张”的路线；
2. 外层十面已形成稳定基线，但用户未确认整个骨架阶段完成；
3. 当前仍需要扩大和完善系统级骨架，暂不进行细节完善；
4. 文字架构图是用户形成完整架构心智模型的重要交付物；
5. Windows 学习用于完善运行骨架，不应带领项目跑入实现细节；
6. 整体骨架阶段的最终完成需要用户明确确认。

## 既有输入

- Skeleton v1.0 外层基线；
- 情报语义与核心对象体系 v1；
- Windows W1—W6 候选材料；
- 三省六部 Agent / Tool 混合制；
- 多租户、不可越权和全域演进原则。

## 实现反馈输入：言官权必须独立建模

来源：2026-08-11 CodeTMux 中央治理实现审计。

一次实际权限矩阵审计发现：如果“谋士可观察世界”只映射为 `world.read`，而“提交奏折”被归入项目执行者的 `project.report`，谋士会出现能调查但不能正式上奏的制度断点；若直接授予完整 `project.report`，又会连带获得 Memory 写入等不必要能力。

因此，G1-B 权威轮需要验证以下候选不变量：

1. 奏折提交是独立、可撤销的窄能力，不应隐含项目写入、Memory 写入、资源调度或执行权；
2. 观察权、陈述权、事实接受权、裁决权和执行权必须分别授权，官职名称不能替代动作级能力；
3. 拥有广域观察权的谋士，其奏折信息板默认仍按提交主体隔离；“可以看世界”不等于“可以读取其他谋士的私有上奏历史”；
4. 奏折只是 Proposal，不因提交者拥有观察权而自动成为 Fact，也不产生执行许可；仍须经过证据核验、裁决和确定性执行闸门；
5. 权限模型需要表达 `subject + action + resource + scope + conditions`，而不是仅使用 `advisor / worker` 这类粗角色包。

该输入与 ADR-0014 的“内部智能主体只拥有派生且分域的权力”一致，但补充了一个可工程验证的反例和更细的能力拆分要求。当前状态仅为 v0003 输入，待 G1-B 权威轮决定是否进入正式契约、参考权限词汇或新 ADR。

## 实现反馈输入：可替换大脑的双重能力边界

来源：2026-08-11 CodeTMux `SovereignBrainRuntime` 实现。

CodeTMux 将 Claude Code/Codex 作为皇帝稳定身份按需挂载的临时认知 Runtime 时发现：只裁剪 MCP Tool 不足以保证“有脑无兵权”。成熟 Coding Agent 同时携带内置 Shell、文件编辑、规则发现、插件和会话持久化能力；即使外接 MCP 完全只读，它仍可能通过自身 Harness 绕过控制面产生副作用。

G1-B/G2 需要验证以下候选机制：

1. 稳定治理身份、模型/Agent Provider、Runtime 进程和执行能力令牌是四个不同对象；替换 Provider 不迁移稳定身份，启动进程也不自动获得执行权；
2. 外部 Tool/MCP 授权与 Agent 内置工具沙箱必须同时生效，取两者交集，而不是任选其一；
3. 中央认知 Runtime 应运行在无项目写权限的目录或只读沙箱中，并禁用未经声明的规则、插件、额外 MCP 和会话副作用；
4. Runtime 输出的 Decision 先进入待采纳槽，`submit` 不等于 `approve`，`approve` 也不等于 `execute`；确定性执行点仍须重新核验现场并签发短时一次性能力；
5. 多 Mission 同时触发中央思考时，需要有界并发或串行队列，避免“逻辑按需”在工程上退化为模型进程风暴；
6. Provider 缺失、认证失败、限额、超时或输出无效属于认知承载故障，应允许切换 Provider 或降级路径，但不能因此扩大权限或跳过审核。

这一输入为既有“皇帝稳定身份不等于永久大模型进程”提供了实现层验证，也揭示了单纯 MCP allowlist 的负空间。当前仍作为 v0003 候选机制输入，不静默修改 Skeleton v1.0 的正式权力语义。

## 实现反馈输入：中央单一读取真值不等于集中夺取写权

来源：2026-08-11 CodeTMux `WorldStateLedger + TerminalDossier` 实现。

锦衣卫情报在工程中原本分散于资源注册表、终端上下文、现场巡检、滚动摘要、ProjectRun 和 Git 检查器。让皇帝大脑每次自行调用这些来源并拼装，会产生多种彼此不一致的“当前现场”；但把所有来源的写入职责合并到一个中央大对象，又会形成强耦合、覆盖用户修正并放大广播与持久化成本。

G1-B/G2 需要验证以下候选机制：

1. “中央单一真值”更准确地定义为单一权威读取投影，而不是单一写数据库；各领域源继续拥有自己的写入权，中央账本负责带来源地组合；
2. 机器观测、仙人/用户明确意图、Agent/臣子声明和已核验事实必须是四个不可互相覆盖的权威层级，不能只用一个 `status` 或 `summary` 字段表达；对象字段即使命名为 `verified`，只要来源仍是臣子自述，就必须停留在声明层；
3. 新鲜度属于每个来源和证据引用，而不是整份对象的单一时间戳；过期、缺失与冲突应显式进入投影，不能按“最后写入者获胜”静默消解；
4. 中央投影应由治理读取或物质事件按需触发，重复读取按 Revision 去重；不能为了“实时”再造一个周期性全量扫描器；
5. 增量账本可以只保存有界的轻量变化索引，原始证据继续由权威源持有；这样既可审计状态代次，也避免复制无限终端原文和摘要造成内存/磁盘增长；
6. 谋士与皇帝大脑优先读取单对象 Dossier，只有跨对象调度才读取全局 Snapshot；统一入口同时是认知成本和权限面收敛机制。

该实现反馈把“情报集中”进一步拆成写入权、读取权、证据所有权和投影权四种不同权威。当前作为 v0003 候选输入，等待后续权威轮判断是否提升为智能运行面的正式不变量或 ADR。

## 实现反馈输入：中央调查任务与谋士进程必须分离

来源：2026-08-11 CodeTMux `SovereignTaskBoard + AdvisorRuntime` 实现。

将 Claude Code/Codex 用作只读谋士时，若中央只向某个进程发送一段调查 Prompt，任务权属、领取关系、调查边界、奏折归属与失败恢复都会寄生在临时会话中：进程退出就丢失政务状态，重复启动可能产生多份互不关联的奏折，模型还容易把“已提交意见”误解为“任务已完成”。

G1-B/G2 需要验证以下候选机制：

1. 中央调查任务是持续存在的治理对象，Agent Runtime 只是可替换、会失败的临时承载；任务生命周期不得绑定单个模型进程或会话；
2. 调查授权应显式包含目标、问题、边界、证据要求、世界状态引用、受领主体和到期租约，不能只依赖自然语言 Prompt；
3. 领取权与上奏权可以授予谋士，但派发、核验、裁决和执行仍属于中央；谋士提交的 Proposal 不自动晋升为 Fact，也不产生任何副作用；
4. 任务提交与奏折关联必须是一个原子中央动作：成功后任务持有唯一 `proposalId`，失败则不能留下被正常流程误认为已归档的孤儿奏折；
5. 谋士进程未上奏即失败时，应撤销临时身份并重新开放任务；已经提交的奏折不能因进程退出而回滚或重复提交；
6. `verified`、`conflicted`、`needs_revision` 和最终裁决是不同状态；部分核验或证据冲突不能被中央接受为完成；
7. 用户的普通待办、项目执行工作项与中央政务调查是不同对象：前者表达“要做什么”，后者表达“中央授权谁在什么边界内查明什么并如何上奏”；
8. Agent 并发由有界 Runtime 队列治理，但队列状态不是任务真值；重启、切换 Provider 或转派主体不得改变中央任务的稳定身份。

这一输入补足了“言官权独立建模”之后的任务治理缺口：陈述权不仅需要独立能力，还需要与持续政务对象、临时身份租约、奏折相关性和证据裁决形成完整闭环。当前作为 v0003 候选输入，等待权威轮决定是否形成正式 `InvestigationMandate / AdvisoryTask` 领域对象及对应 ADR。

## 实现反馈输入：跨主机智能运行应计算下沉而非状态搬运

来源：2026-08-11 CodeTMux 远程 `AdvisorRuntime + SSH reverseForward` 实现。

当项目、tmux 和 Coding Agent 位于云主机时，如果中央让远程模型的每一次文件读取、搜索和推理工具调用都跨公网往返，成熟 Agent Loop 会把单次网络延迟放大数十至数百次；若为降低延迟直接把中央控制面和高权限 MCP 常驻复制到远端，又会破坏“所有权力归中央”的主权边界。

G1-B/G2 需要验证以下候选机制：

1. 计算位置与权力位置必须分别建模：高频认知计算可下沉到数据和项目所在节点，Goal、Task、Identity、Lease、Proposal、Evidence 与最终裁决仍归中央；
2. 跨域链路应传输低频控制语义——任务、边界、短期凭证、里程碑、奏折和核验证据——而不是持续搬运完整仓库、终端字节流或每一步推理上下文；
3. 远端 Runtime 使用本地原生只读工具完成高频调查，只有中央独有的世界状态、领取和上奏动作经过受认证反向通道；网络延迟因此不进入每次代码检索；
4. 反向通道只绑定远端回环地址，Credential 必须按任务、角色、工具集和 TTL 裁剪；远端获取的是派生能力，不是中央 Root Token 或玉玺副本；
5. 任务持续状态和远端进程状态分离。断线时优先撤销凭证、释放通道并安全重派，不能把进程消失误判为任务完成；
6. “断线后无缝续跑”不是免费能力。它需要可恢复身份、固定端点、幂等上奏和远端持久守护等更大攻击与运维面；在没有明确价值证据前，安全重派可以是更合理的基线；
7. 桌面中央接收的远端 stdout/stderr 必须有界，只保留诊断尾部；原始高容量过程数据留在执行节点或专用证据存储，避免网络与中央堆内存随任务增长。

这一输入将“权力不可转让”补充为分布式运行原则：控制集中不要求计算集中，数据局部性也不等于权力下放。当前作为 v0003 候选输入，后续应与多区域延迟、身份派生、故障恢复和证据血缘共同验证。

## 实现反馈输入：中央知情权、调查政令与持久验收证据必须闭环

来源：2026-08-12 CodeTMux 主权治理闭环审计与实现。

实际闭环暴露了四个容易被角色名掩盖的权力断点：皇帝运行实例若只能看自己上奏的内容，就无法基于臣子奏折全文裁决；Prompt 中说“派谋士调查”若没有类型化 Decision，就无法可靠进入政务任务与 Runtime；谋士虽无执行权，但广域 `world.read` 仍可能横向读取别案；终端生命周期内的完成标记一旦随终端销毁，最终验收就失去持久证据。

G1-B/G2 需要验证以下候选机制：

1. 中央全局知情权应作为独立能力存在。皇帝治理 Runtime 可以读取全部 Proposal、Evidence 与 Review，但“可读全局政务”不自动授予核验、裁决或执行权；
2. “召谋士”必须是类型化政令，而不是自然语言暗示。Decision 只表达调查目标、问题、边界和证据要求，由确定性中央 Governor 幂等转换为持续 Task、派发与临时 Runtime；
3. 智能主体建议的资源范围不能成为授权来源。实际 `host / project / terminal / goal` 范围必须由当前权威现场覆盖，并进入临时 Credential；列表、详情、快照、统计与事件返回值都要在数据面再次裁剪；
4. 角色级 Allowlist 只是第一层，具体观察权必须从 `taskId + goalId + hostId + projectPath + allowedResourceIds` 派生，且随任务终止、转派或到期撤销；
5. 最终验收证据必须独立于临时 Runtime、终端和 UI 生命周期持久存在。命令结果、机器核验结果与其来源引用进入 Evidence Ledger，验收条件逐项绑定同一 Mission/Goal 的已核验证据；
6. `Proposal submitted`、`Evidence verified`、`Decision accepted`、`Action executed` 和 `Acceptance satisfied` 是五个不同事实，不能由一次模型输出或一个状态字段合并；
7. 幂等键应来自稳定任务身份与现场代次/指纹，避免中央在相同现场重复召见多个谋士；但现场变化后必须允许形成新调查轮次。

这一输入进一步说明：治理体系的完整性不取决于“皇帝更聪明”，而取决于信息权、陈述权、调查委托权、核验权、裁决权、执行权与验收权是否分别有正式协议、确定性闸门和可恢复状态。当前保持为 v0003 候选输入，等待 G1-B 权威轮决定是否提升为治理协议参考模型或 ADR。

## 实现反馈输入：被授予行政能力的中央同时承担履职义务

来源：2026-08-12 CodeTMux OVO 与八个 Center 串联审计。

CodeTMux 已经能够让中央 Agent 控制 tmux、监督长期 Goal、读取世界状态并裁决证据，但 Project、Git、Task、Knowledge、Files、Models 等 Center 仍主要是彼此独立的 UI 投影。由此出现一种新的权力空洞：中央“有能力回答”用户，却没有统一义务把明确意图落实到对应领域真值、验证结果并传播给其它 Center。例如用户明确描述一个项目时，系统可能只把它留在对话 Memory；用户要求接入 Git 时，Git 领域服务虽已存在，中央 Tool 面却不可达。

G1-B/G2 需要验证以下候选机制：

1. 权力与责任必须成对建模。中央获得某领域的行政 Capability 后，当有权用户给出明确且可执行的领域意图时，中央同时获得“执行或明确拒绝/升级”的履职义务，不能只生成自然语言建议；
2. `Intent → Command → Effect → Read-back Verification → Domain Event → Projection` 应成为行政闭环。工具返回 `ok`、命令退出码为零或模型声称完成，都不能单独代替事后条件成立；
3. Center 是领域真值的投影和操作入口，不应成为中央直接修改的页面状态。中央调用 Project Registry、Git Service、Task Service、Knowledge Service 等领域能力，领域事件再更新 Home、Project、Git、Task、Knowledge 与 World State；
4. 长期 Project 与一次 ProjectRun 必须分离。Project 保存用户目标、背景、路径、主机、Repository、Workspace、Terminal、Task、无人值守策略及来源；ProjectRun 只表达某次执行；
5. 用户明确陈述与 Agent 推断必须保留来源、置信度和证据等级。中央可以把推断登记为候选或 inferred 字段，但不能覆盖 `user_authored` 项目背景；
6. 跨 Center 关联使用稳定领域标识和引用，不以路径字符串、页面缓存或 Memory 文本作为唯一连接。Git Repository、Task、Knowledge Note、Goal、Terminal 和 Workspace 应引用同一个 Project Identity；
7. 行政动作需要独立 Fulfillment/Responsibility Ledger，至少记录 intent、责任主体、授权、命令、事后条件、结果、失败/补偿、领域事件和最终回执，支持中断恢复与“是否真的落实”审计；
8. 不是每句话都自动写入各 Center。系统需要区分回答型意图、建议型意图、明确 Command、需要确认的高影响动作和持续 Mandate，避免“积极履职”退化为模型擅自建档或执行；
9. Renderer IPC、OVO Tool 与 MCP 不应分别复制三套业务逻辑。它们应共享同一领域 Service/Command Handler，只在身份、授权和呈现层不同；
10. Home Center 应消费跨领域事件形成态势投影，而不是成为新的中央数据库；世界状态同样是来源可追踪的读取投影，不夺取各领域写权。

这一输入把“能力可以开放，权力必须受限”补充为另一半：**已经接受的权力必须可履行、可验证、可追责**。否则系统会出现安全上没有越权、产品上却长期失职的“只会回答、不落实”中央。当前作为 v0003 候选输入，待权威轮判断是否形成行政 Command 契约、Project 领域对象和跨面履职账本 ADR。

## 实现设想输入：每台受管主机需要地方治理节点，而非依赖 tmux 充当远程协议

来源：2026-08-14 CodeTMux 跨主机开发与长期联系设想。

当中央只能通过 SSH 临时执行命令、抓取 tmux 屏幕或读取 Coding Agent 的终端文本时，远程开发现场缺少一个稳定、结构化且可恢复的主机级通信主体。tmux 是人类终端复用器，其屏幕内容可能被 TUI 重绘、滚屏截断和会话状态破坏；它适合人工观察与接管，却不应继续兼任设备控制协议、完整事件源和长期联系通道。单个 Agent Beacon 同样只描述某个 Claude/Codex/OpenCode Runtime 的事件流，无法代表整台主机的进程、目录、资源、连接和多个 Runtime。

用户提出在每台已认证主机上默认投放一个“地方官/封疆大吏”式节点。该节点安装后保持轻量常驻或休眠，不自行规划项目；中央需要使用该主机时先通过 SSH 触发或唤醒，随后由节点通过受认证的出站反向连接主动接入 OVO，建立可重连的长期协议通道。此后主机级启动、停止、进程监管、Runtime 派生和现场信息传输优先通过该节点完成，而不是让中央持续解析 tmux 字节流。

G1-B/G2 需要验证以下候选机制：

1. 地方治理节点是稳定的设备代理与通信主体，不是 Coding Agent、项目 Supervisor、皇帝副本或某个 Runtime Beacon。Claude、Codex、OpenCode 和 OVOCoding 是它按中央授权启动、观察、回收的临时工作负载；每个工作负载仍可拥有自己的 Beacon/Adapter；
2. 中央保留 Goal、Project、Task、政策、最终裁决和主权执行授权。地方节点只取得与特定主机、命令、Runtime、期限和现场代次绑定的派生 Capability/Lease，不能因为长期在线就获得独立项目决策权；
3. 项目意图、下一步方向和跨主机调度仍由 OVO 决定。地方节点只负责设备相关治理、本地能力发现、可靠命令执行、结构化信息采集与传输，以及在断联时执行已授权的有限恢复策略；
4. SSH 是安装、首次认证、唤醒、修复和紧急接管的引导/救援通道，不应成为所有高频动作的数据面。节点上线后优先建立出站反向长连接，以适应云主机 NAT、防火墙和中央无公网入口的环境；
5. 长连接协议需要设备身份、会话代次、单调序号、确认与去重、心跳、断线指数退避、带上限的本地 Outbox、重连后状态重放与中央重新授权。连接恢复不等于旧 Lease 自动恢复；过期或不确定命令必须重新裁决；
6. 设备控制应采用类型化 Command/Event，而不是任意字符串作为唯一协议。至少覆盖主机能力清单、进程与 Runtime 生命周期、工作目录、tmux 兼容会话、Git/文件现场摘要、资源指标、命令结果和结构化 Agent 事件；原始大输出留在本机有界存储，通过引用或按需分片读取；
7. tmux 从“中央唯一眼睛和手”降级为人类可见的工作负载容器与兼容适配器。中央判断 Runtime 状态应优先使用进程树、PTY/Agent 协议事件、命令结果和 Beacon，tmux capture 只作为人工接管、诊断和遗留工具兜底；
8. 地方节点需要与中央状态分离：设备身份和已确认命令可持久化，具体模型进程可替换；中央离线时不得自行扩大任务边界。允许继续的只能是先前明确授权、具备截止条件和资源上限的 Mandate；
9. 应建立独立 Host/Node Center，呈现主机认证、地方节点版本与健康、连接代次、最后心跳、能力清单、本地 Runtime、命令队列、断联 Outbox、项目目录、tmux 兼容会话、资源压力和人工接管状态。该 Center 是主机领域真值的投影，不是另一个自行维护状态的页面；
10. “默认投放”应是主机认证流程中的显式安装阶段并具有可见结果：认证成功不等于节点安装成功，安装成功不等于反向通道健康。中央必须分别验证安装、注册、激活、连接和能力探测，并保留卸载、升级、降级与救援入口；
11. C2 类比只用于解释“中央控制、远端回连、长期心跳和任务分发”的通信形态，不应把隐蔽性、无边界命令执行或规避主机所有者控制带入产品设计。正式术语应优先使用 `Host Steward / Node Governor / Host Agent` 与 `Control Channel`；
12. 主机级动作原则上经地方节点执行，以获得一致的审计、幂等、回执和生命周期管理；仍需保留受用户控制的 Break-glass SSH/本地终止路径，防止地方节点损坏时中央完全失去救援能力。

建议把关系固定为：

```text
OVO Sovereign Kernel（中央权力与项目裁决）
        │
        │ 类型化主机命令、短期授权、Project/Goal 引用
        ▼
Host Steward / Node Governor（地方设备治理与通信）
        ├── Process / PTY / Files / Git / Resource adapters
        ├── tmux compatibility adapter
        ├── Claude / Codex / OpenCode runtime adapters
        └── per-runtime Beacon（结构化事件口）
```

该输入补足了“计算下沉而权力不下放”的节点侧承载：远端不再只有临时 Agent Runtime，也存在一个不承担高级认知、但拥有稳定设备身份和受限执行职责的地方治理节点。当前保持为 `Seed`，后续需要用断联、中央重启、重复命令、远端任务继续、人工接管、节点升级和节点失陷场景验证，再决定是否形成 Host Control Protocol、设备身份模型与部署拓扑 ADR。

### 边界修正：地方官是开发联络与情报通道，不承接皇帝到 tmux 的输入权

来源：2026-08-14 用户对上述设想的进一步澄清。

上一版把地方官扩张成“主机级执行代理”，超出了用户原意。封疆大吏存在的最终目的，是帮助中央利用 Claude Code、Codex、OpenCode、OVOCoding 等成熟 Coding Agent 在远端持续开发，并把 tmux 无法完整表达的结构化现场送回 OVO；它不是为了代替 OVO 操控主机。上一版中“所有主机级动作原则上经地方节点执行”“地方节点统一启动/停止进程、操作文件和 Git”的候选解释暂不采纳，由本节修正。

修正后的权力与数据路径为：

```text
仙人输入 / OVO 决策
        │
        ▼
OVO Sovereign Kernel
        │
        │ 直接 tmux_send / 现有 SSH-tmux 执行路径
        ▼
远端 tmux Pane ────────→ Claude Code / Codex / OpenCode
        │                           │
        │ 人类可见终端输出           │ Hooks / MCP / JSONL / 状态报告
        │                           ▼
        └── tmux capture 兜底    Development Steward（地方开发联络官）
                                    │
                                    │ 结构化事件、报告、证据、心跳
                                    ▼
                              OVO World State / Goal
```

修正后的候选不变量如下：

1. **终端输入权保持中央直达。** 用户输入、OVO 指令、Agent 启动命令、继续指令和人工接管仍由 Sovereign Kernel 通过现有 tmux/SSH 执行路径写入目标 Pane；地方官不转发、不改写、不排队这些开发指令，也不成为第二道输入代理；
2. **地方官的主职责是结构化回传。** 它汇聚各 Provider 的 Hooks、MCP 回调、stream-json/JSONL、会话状态、里程碑、工具调用摘要、测试结果与显式报告，形成比 tmux 屏幕更完整的开发现场；
3. **tmux 仍是皇帝的手和人类界面。** 它没有降级为纯遗留组件；中央继续通过 tmux 控制可见会话。被替代的只是“仅靠 tmux capture 猜测 Agent 完整状态”，不是 tmux 输入和人工接管能力；
4. **地方官不是设备运维 Agent。** 它不拥有任意 Shell、文件修改、进程终止、Git 写入或项目规划权。为采集事实而读取受限的进程身份、工作目录、Git 状态或文件元数据，必须被建模为观察能力，而不是主机行政权；
5. **Coding Runtime 的启动仍可由 tmux 完成。** OVO 直接输入带有地方官连接配置、MCP 配置、Hook 配置或环境变量的 Claude/Codex/OpenCode 启动命令；Runtime 启动后主动向本机地方官登记，地方官再通过反向通道汇报中央；
6. **地方官与 Runtime Beacon 是一对多关系。** Beacon 是单个 Agent 会话的结构化事件接口；地方官维护本机多个 Beacon 的身份、Project/Goal/Pane 绑定、断联缓存和路由，但不替这些 Agent 思考；
7. **信息通道与执行通道保持分离。** 正常路径是“OVO 直接写 tmux，地方官回传结构化信息”。中央可以向地方官查询现场或要求补发报告，但不能把这种查询协议悄然扩张成任意远程执行接口；
8. **手工启动的 Agent 也必须可接管。** 若用户没有通过标准包装命令启动，地方官可以先识别 Pane、进程和 Provider，建立 `unbound` 会话并提示 OVO 采用；能动态挂载 Hook/MCP 时补充挂载，不能挂载时结合 tmux 兜底观察，并由 OVO 直接向 Pane 询问 Agent 生成结构化交接；
9. **独立 Center 的边界随之变化。** 它更适合命名为“开发节点中心/地方官中心”，展示各主机的地方官连接、Provider 能力、Agent 会话、Pane 绑定、Goal/Project 绑定、结构化事件覆盖率、最后报告、断联队列和退化到 tmux-only 的状态，而不是提供通用主机控制台；
10. **可靠反向连接仍然有价值。** 心跳、重连、序号、确认、去重、有界 Outbox 和会话代次用于保证开发情报不断链；它们不授予地方官新的执行权，也不改变皇帝直接向 tmux 下旨的路径。

因此正式术语候选从强调设备治理的 `Node Governor` 收窄为 `Development Steward / Agent Liaison`。叙事上仍可称“封疆大吏”，但其技术含义是**驻主机的开发联络官与结构化情报汇聚点**，不是地方皇帝或远程运维守护进程。

### 二次边界修正：中央直达与地方委任是并存的两条合法命令渠道

来源：2026-08-14 用户对“皇帝直达”的进一步澄清。

上一节又把地方官收窄成了纯情报通道，仍不完整。用户确认：皇帝既可以直接向 tmux/Coding Agent 下令，也可以命令封疆大吏，由封疆大吏在受托范围内统筹本机 Claude Code、Codex、OpenCode 等开发 Runtime。地方官不是通用主机控制器，但拥有中央临时授予的**本地开发指挥权**。

正式候选应采用双命令渠道：

```text
中央直达渠道：OVO ───────────────→ tmux Pane / Coding Agent

地方委任渠道：OVO → Development Steward → 本机 tmux / Coding Agent
                        ↑                         │
                        └──── 结构化报告与证据 ───┘
```

1. 中央直达适合人工接管、精确指令、紧急纠偏、单 Pane 操作和对地方官决策不满意时的覆盖；地方委任适合一台主机上的持续开发、多个 Pane/Agent 协作、断联缓冲和本地低延迟推进；
2. 地方官可以在明确 `Development Mandate` 内选择已授权 Provider、启动或续接开发 Runtime、向被租用的 Pane 发送开发指令、收集报告并组织本机迭代；它仍不能执行与该开发委任无关的任意主机管理；
3. 两条渠道不能同时无序写入同一 Pane。每个 Pane/Runtime 必须有 `controlOwner`、`controlEpoch` 和 Lease。`ovo_direct`、`steward_delegated`、`human_manual` 是互斥控制模式；
4. 皇帝与仙人的直接接管拥有更高优先级。发生直接输入前，中央先递增控制代次、暂停或撤销地方官对该 Pane 的 Lease；地方官收到过期代次的命令不得继续写入；
5. 直接接管结束后，中央可以显式 `return_control`，把最新终端现场、Goal、用户修正和新的 Lease 一并交还地方官。控制权不会因为超时结束或连接恢复而隐式回到地方；
6. 地方官对中央的结构化回传在任何控制模式下都可继续，只要观察授权仍有效；失去写入权不等于停止汇报，因此皇帝直接下令时仍能利用地方官情报；
7. `tmux_send` 是中央和地方官都可调用的执行能力，但两者取得能力的来源不同：中央通过 Sovereign Kernel 取得一次性直接执行权，地方官通过与 Host、Project、Goal、Pane、期限和控制代次绑定的派生开发 Lease 取得受托执行权；
8. Center 必须让用户一眼看出每个 Pane 当前由谁控制、为什么拥有控制权、Lease 何时到期、最近一次中央覆盖和是否存在待交还状态，避免双通道在 UI 上变成不可解释的竞态。

因此地方官既不是“地方皇帝”，也不是“只读探子”；它是中央可随时越过、暂停和收回权限的地方开发统筹者。皇权的含义不是所有命令必须亲自发送，而是**中央拥有选择直达或委任、覆盖地方决定以及最终收回控制权的唯一权力**。

### 三次边界修正：地方官是无模型的确定性传令节点，不是本地统筹 Agent

来源：2026-08-14 用户明确地方官没有 API Key、没有独立认知，只负责传达。

“地方委任”不能解释成把项目规划权交给另一个本地模型。地方官自身不运行高级模型、不持有 Claude/Codex/OpenCode API Key、不选择下一步，也不生成开发指令。所有指令内容、目标 Agent 和目标 Pane 都由 OVO 决定；地方官只在该网络路径更方便或更可靠时，确定性地把中央已经形成的指令送入指定 tmux Pane，并返回投递回执。上一节关于地方官“选择 Provider、组织本机迭代、作为地方开发统筹者”的候选语义由本节替代。

两条渠道的区别只在传输路径，不在决策主体：

```text
中央直达：OVO 决策 → 中央 SSH/tmux 连接 → tmux Pane

地方传令：OVO 决策 → 结构化 Command Envelope → 地方官
                                              → 本机 tmux Pane

两条路径的指令作者始终是 OVO；Claude/Codex 使用其自身已有登录或额度。
```

候选协议约束如下：

1. 地方官可以持有设备身份、控制通道凭证和最小 tmux 投递能力，但不得持有模型 API Key，也不得以自己的身份调用模型；
2. Command Envelope 至少包含 `commandId / hostId / paneId / controlEpoch / payload / submitMode / expiresAt / dedupeKey`。地方官只校验范围、代次、到期和幂等，然后原样投递，不解释或改写自然语言内容；
3. 对单行指令可使用 tmux literal key 注入；对多行、大段 Prompt 或包含特殊字符的内容，应通过 stdin 写入命名 tmux buffer，再使用 bracketed paste 投递，最后单独发送 Enter，禁止把内容拼接进 Shell 命令造成转义或执行歧义；
4. `delivered` 只表示字节已写入目标 Pane，不表示 Coding Agent 已接受、开始或完成任务。Agent 的结构化事件、Hook/MCP 报告或后续终端现场分别产生 `accepted / running / completed / failed`；
5. 地方官可以把 Agent 回报、协议事件与有界终端尾部送回中央，但不能根据这些内容自行形成下一条开发指令；下一轮仍由 OVO 决策后选择直达或再次传令；
6. 中央根据连接健康、延迟、Pane 所有权和故障状态选择路径。地方通道不可用时可以退回中央 SSH/tmux 直达；中央直达不可用但地方长连接健康时可以通过地方官传令；
7. 双路径共用同一 `commandId / controlEpoch / dedupeKey` 语义。中央从地方传令切到直达重试时，必须先查询投递状态或使用相同幂等键，避免同一句命令在 Claude TUI 中提交两次；
8. 地方官更准确的技术定位是 `Development Relay Daemon / Agent Communication Relay`。叙事名称仍可保留“封疆大吏”，但不应因“官员”二字推导出其拥有独立智能或地方裁量权。

因此最终边界是：**OVO 独占思考和指令生成，tmux/Coding Agent 接受指令，地方官只提供可选的可靠传令与回报路径。**

### 部署修正：本机与云主机是同一种节点，差异只在连接建立方式

来源：2026-08-14 用户明确 CodeTMux 产品默认运行在 Windows，但协议与测试不绑定 Windows。

CodeTMux 桌面中央默认面向 Windows 交付，不意味着 Development Relay 协议只能由 Windows 中央验证，也不意味着本机绕过地方官模型。中央所在主机同样登记为一个 Host，并拥有本机 Relay；云主机与本机在 Command Envelope、身份、回执、幂等、事件和 Center 投影上完全同构。

```text
本机：OVO Central ── loopback/local IPC ── Local Relay ── Delivery Adapter

远程：OVO Central ── SSH 引导 + 反向通道 ── Remote Relay ── Delivery Adapter
```

候选约束：

1. Relay Core 使用跨平台 Node 实现并支持 Windows/Linux；平台差异封装在投递与安装适配器，不分裂协议和领域对象；
2. Linux 首个投递适配器是 tmux；Windows 本机可接入 CodeTMux 已有 TerminalManager/node-pty。独立 Windows 远程节点若需要管理外部控制台，应另建受托 ConPTY Adapter，不能声称可向任意 Windows Terminal 注入；
3. 本机 Relay 通过回环 TCP、Named Pipe 或进程内协议端点接入，不需要 SSH；远程 Relay 才使用 SSH 完成投放、唤醒和反向端口建立；
4. 自动化测试可以在 Linux 开发环境同时启动测试中央与 Linux Relay，经回环通道向真实或隔离 tmux Pane 投递并验证回执、去重、重连和状态查询，不必把协议正确性推迟到 Windows 人工实测；
5. Windows 实机验收只负责证明 Windows 打包、启动、TerminalManager Adapter 和生命周期集成，不再承担整个 Relay 协议的首次真实性验证；
6. Center 不以“本地/远程”拆成两套页面；同一节点列表仅显示 `transport=local|ssh-reverse` 与不同 Adapter 能力。

这一修正使“封疆大吏”成为真正的节点级通信抽象：本地不是特权旁路，远程也不是另一套系统。

### 实现审计补充：传令、回执、运行实例与主机位置必须形成四重隔离

来源：2026-08-14 CodeTMux Development Relay / Agent Beacon 正确性审计与回归修复。

Relay 与 Runtime Beacon 的首轮实现证明，类型化协议和中央主权本身仍不足以避免“正确的命令被错误执行”。如果接收端在验证前替报文生成标识和到期时间、同一 Pane 的多条投递异步穿插、幂等重试返回旧请求的关联标识，或者退场 Runtime 的迟到事件仍能修改新任状态，那么系统在权限图上仍然属于中央，实际行为却已经串台。以下约束应成为后续 Host Control Protocol 与 Runtime Protocol 的候选硬不变量：

1. **构造与解析必须分离。** `buildCommand` 只供中央创建新命令并填充默认值；`parseIncomingCommand` 必须逐字段验证线上原文，禁止补 `commandId`、`dedupeKey`、`createdAt`、`expiresAt`，禁止改写 `protocol/type/host/target`。验证器不得替发送方补考卷；缺字段、错协议和过期命令一律 fail closed；
2. **同一投递目标必须 FIFO。** tmux buffer 的 `load → paste → Enter → delete` 是一个不可穿插的逻辑事务。同一 `host + adapter + targetId` 的 Command 必须严格串行，不同目标才可并行；队列是投递正确性边界，不只是性能实现；
3. **ACK 关联与幂等事实必须分离。** 重试请求可使用新的 `commandId` 与相同 `dedupeKey`。Relay 不重复执行，但 ACK 必须回显当前请求的 `commandId`，并以 `originalCommandId` 表达首次执行者；中央同时校验认证 Host、当前 Command 和 Dedupe Key，避免“地方已投递、中央永远超时”；
4. **Runtime 事件必须带实例与轮次栅栏。** 每个进程具有不可复用的 `runtimeInstanceId`，每次授权/索奏具有单调 `generation` 与独立 `turnId`。中央只允许与当前 Goal 的活动三元组匹配的事件修改 `streamRun`；旧实例或旧轮次的迟到 result/done/error 只能归档为 stale；
5. **退场登记按 Runtime 实例索引。** 同一 Goal 可能在旧实例尚未退出时已经启用新实例，甚至再次换任。`retiringRuntimes` 不能按 Goal 单值覆盖，必须按 `runtimeInstanceId` 分别保留宽限期、强制回收计时器和句柄；
6. **运行位置是授权事实，不是 cwd 猜测。** 明确提供但不存在的 cwd 必须报错，禁止回退到中央进程目录。远程 Project 未具备远端 Beacon 时不得在中央本机 spawn 同名 Coding Agent；应继续使用远程 tmux/Relay 通道，或明确等待该主机 Runtime 能力上线；
7. **Provider 参数不能替代权限模型。** 类似 `allowedTools` 的“免确认工具”列表不等价于“只允许这些工具”，跳过权限提示也不构成沙箱。Runtime 的 Host/Project/Goal/Path 边界必须由中央部署位置、Credential Scope、Kernel Gate 与事后证据共同保证；
8. **当前过渡拓扑必须如实表达。** 在远端 Host Beacon 尚未真正挂到 Relay 之下时，中央本地 Beacon 与远端 Relay 仍是并列通道，不能在代码或 UI 中宣称已经实现 `一台 Host Relay → 多个远端 Runtime Beacon`。过渡期的正确策略是远程走 tmux/Relay、本地可走 headless Beacon，直至 Host 侧 Runtime 协议完成。

这些约束把“玉玺归中央”进一步落实为四个可验证条件：**命令内容不被接收端修复、同一目标不乱序、回执不串请求、旧任不干预新任、臣子不出生在错误主机。**

### 四次边界修正：Relay 常态无模型，但地方节点允许显式挂载可收回主脑

来源：2026-08-14 用户对地方官承载能力与特殊时期自治需求的明确补充。

“地方官没有 API Key、只负责传达”仍然是默认运行原则，但不能被解释为节点永远不得运行成熟 Coding Agent。用户确认：地方节点可以更重，绝大部分项目现场信息和展开工作可能适合留在本机；特殊时期允许地方官使用该主机已经安装、已经登录的 Claude Code 作为临时主脑。这里不是把模型嵌进 Relay，也不是给节点保存中央 API Key，而是由 OVO 显式挂载一个可替换、可停止的成熟 Agent Runtime。

修正后的候选结构是：

```text
Development Relay（常驻确定性内核，始终存在）
        │
        ├── 默认：传令 / 去重 / 回执 / 事件 Outbox，不启动模型
        │
        └── 特殊时期：挂载 Steward Brain Runtime
                         └── 主机已有 Claude Code 登录态
                             在指定 Project cwd 使用原生 Agent Loop
```

候选约束如下：

1. **无模型是默认态，不是永久类型限制。** Relay Core 本身仍不推理、不持有 API Key；Claude Code 是可拆卸 Runtime，生命周期和身份与 Relay 分离；
2. **挂载必须是显式主权动作。** Relay 在线、Host 认证或项目存在都不能自动启动模型。OVO/仙人明确选择 Host、Project cwd、职责与 Mandate 后才能启动；
3. **优先复用主机现有登录态。** 地方主脑通过本机 Claude CLI 认证和额度工作，Relay 配置、中央登记、日志与 Memory 中不得复制 API Key；未来也可替换为 Codex/OpenCode Adapter；
4. **地方展开不等于地方拥有最终权力。** 主脑可以使用成熟 Coding Agent 的检索、规划、编辑、测试、子 Agent 和上下文压缩循环，但不能修改中央全局 Goal、跨主机资源分配和最终完成裁决；
5. **运行位置是委任边界。** 主脑必须出生在目标 Host 的指定绝对项目目录；路径不存在立即失败，禁止回退中央 cwd。一个节点当前若只支持单主脑，则切换 Project 前必须结束旧实例；
6. **OVO 保留直达、覆盖和召回。** 中央仍可直接向 tmux 下令、人工接管或停止地方主脑。主脑的 result 是奏折，不是圣旨，也不是已验证 Evidence；
7. **重型节点的价值在信息局部性。** 大型仓库扫描、Git/文件现场、测试循环和高频工具事件留在主机本地展开，只把结构化里程碑、证据引用、风险和需要中央裁决的问题送回，降低远程屏幕抓取与上下文搬运成本；
8. **断联只允许继续既有 Mandate。** 结构化事件进入有界 Outbox；地方主脑不得因中央离线自行扩大目标。重新连接后先上报状态，由中央决定续任、收回或重新授权；
9. **Center 必须展示双态。** 用户应同时看到 Relay 的连接/投递健康与可选 Brain 的 Provider、Runtime ID、Session、cwd、状态、最近事件和收回入口，不能用一个“地方官在线”混淆通信节点和认知 Runtime；
10. **成本开关必须真实。** Brain 未启用时不产生模型 Token；启用后其会话成本、静默时间和退出原因应可观察，避免“可选”能力变成不可见常驻消耗。

因此“三次边界修正”中“地方官永远不运行高级模型”的结论被本节进一步收窄：**Relay Core 永远无模型；地方节点整体可以在中央显式授权下临时挂载成熟 Coding Agent 作为地方主脑。** 这保留了默认确定性与中央主权，同时允许利用数据局部性和上游 Coding Agent 的完整演进能力。

### 五次边界深化：地方官是可发布能力、可挂载 Runtime 的主机控制面

来源：2026-08-14 用户要求增强地方官、强化其与 Claude Code 的联系，并以 OpenClaw Node Host / Gateway / Agent Runtime 源码作为对照样本。对照版本为 OpenClaw `ad400d810b3bdbbf7d7048f27c926d82b1f40f1c`；本节只吸收节点可靠性机制，不引入其多渠道助手产品边界。

地方官不能只回答“在线/离线”，也不能把“能运行 Claude”误写成固定人格。更稳定的抽象是同一主机上的两层控制面：常驻的确定性 Host Control Plane 负责盘点、观察、传令、生命周期和事件路由；可选的 Local Cognition Plane 挂载 Claude Code、Codex、OpenCode 等成熟 Runtime。前者发布能力，后者消费被中央授予的能力，两者都不因此获得主权。

```text
OVO Sovereign Kernel
        │
        ├── 中央直达 ─────────────────────────→ tmux / Runtime
        │
        └── Development Steward
                ├── Host Control Plane
                │     inventory / capability manifest
                │     bounded command queue / ACK / timeout
                │     tmux observation / runtime lifecycle
                │
                └── Optional Runtime Adapter
                      Claude Code / Codex / OpenCode
                      native agent loop + host-local context
```

新增候选约束如下：

1. **能力发布不等于权力授予。** 地方官可声明平台、资源、tmux、Git、Node 和可用 Coding Runtime 的版本与健康状态；OVO 根据清单选择路径。某项能力出现在 Manifest 中，不代表节点已获准调用它；每次动作仍需中央 Command、目标范围和到期时间；
2. **主机动作采用固定协议面。** 常态只暴露 `inspect / refresh / tmux-list / tmux-capture` 等确定性观察，以及经过既有 Sovereign Kernel 的 tmux 投递。不得因为节点更“重”就顺手开放任意 Shell 后门；文件、Git 和主机行政写操作仍走其正式领域服务与审计链；
3. **Runtime 调用具有独立关联身份。** 一个 Claude 进程拥有 `runtimeInstanceId/sessionId`，每次输入拥有独立 `turnId`，流式进度以后续 `seq` 排序。同一 Runtime 的输入严格 FIFO，不同 Runtime 才可并行；重试、迟到结果和重连事件不得串轮；
4. **高频事件在地方聚合。** 工具调用、进度与思考事件可以完整进入有界本地记录，但对中央/UI 的状态通知必须合并、限频并可按引用回查，禁止每个流式片段触发整页重建；
5. **每轮都有双重时限。** Idle Timeout 处理“长时间无任何进展事件”，Hard Timeout 限制单轮最长占用；停止、超时和中央召回必须终止完整进程树、清空未执行队列并上报退出原因，而不是只杀父进程或留下孤儿工具进程；
6. **默认认证不搬运密钥。** `host-login` 模式显式移除从中央继承的模型 API 环境变量，使用目标主机已有 CLI 登录态；只有仙人明确要求时才允许 `inherit-api`，并且仍不得把凭证写入 Relay 配置、Prompt、事件或 Memory；
7. **恢复必须指向明确会话。** 只有掌握可信 `sessionId` 时才能恢复既有 Claude 会话；不得用模糊的“最后一次会话”跨 Project 猜测恢复。工作目录、Host 与会话身份不匹配时 fail closed；
8. **中央直达始终保留。** 地方官增强后也只是 OVO 可选择的低延迟执行与情报通道。人工接管、紧急纠偏、地方官失联或中央选择精确下令时，OVO 仍可绕过它直达 tmux；
9. **成熟 Runtime 的循环属于工具，不属于皇权。** Claude Code 可以在被授权 Project 内自行检索、规划、编辑、测试和压缩上下文，但其结果仍是奏折。跨项目调度、全局 Goal、资源分配、最终验收和是否继续由 OVO 决定；
10. **借鉴边界必须可追踪。** 可借鉴 Node/Gateway 的 typed request-response-event、能力清单、连接代次、调用取消、有界串行队列、进度心跳、输出上限和进程树回收；不照搬多聊天渠道、通用个人助手、多租户治理，也不以外部 Agent Runtime 替代现有 Sovereign Kernel。

这一深化使“封疆大吏可以更重”获得精确工程含义：**重在本机能力盘点、协议可靠性、Runtime 托管和信息局部性，不重在复制另一个中央。**

### 六次边界深化：连接代次与事件确认是地方长期运行的事实边界

来源：2026-08-14 用户要求继续借鉴 OpenClaw；CodeTMux 进一步实现 Connection Generation 与持久 Event Ledger。

地方官“常驻”和“断联重试”不能只理解为 Socket 自动重新连接。若旧连接上的迟到 ACK 可以完成新连接创建的请求，或者 Runtime 事件只保存在内存 Outbox 中，那么网络抖动、Daemon 重启和中央重启都会让奏报丢失、重复或串代。持续运行的节点协议必须显式区分**连接事实**与**Runtime 事实**：连接可多次更替，Runtime/Turn 的事实身份不能随 Socket 重置。

新增候选不变量如下：

1. 每次 Host 通过设备凭证完成认证，中央为其分配单调 `connectionGeneration` 与不可复用 `connectionId`；所有该连接上创建的 pending invoke 都绑定这一代次；
2. 同一 Host 的新连接成为活动连接时，旧连接立即失去完成调用和更新在线状态的资格。旧 Socket 的迟到 ACK、响应和心跳只能忽略，不能穿越代次；
3. 连接断开或被替换时，中央立即以明确原因失败该代次的 pending 投递、Host 查询和 Runtime 命令。自动重连只恢复通信能力，不自动把未知执行结果声明为失败或成功；调用方用原 Dedupe Key 查询或重试；
4. Runtime 事件身份由 `hostId + runtimeInstanceId + seq` 构成，`turnId`用于业务轮次关联。Socket 只承载事件，不拥有事件身份；
5. 地方官应在发送前把结构化事件写入有界持久 Ledger。中央落入自身事件历史后返回该 Runtime 的累计序号 ACK；地方重连或重启后只补发检查点之后的事件；
6. 中央必须按稳定事件身份去重。重复传输是至少一次交付的正常结果，不能重复进入 UI、World State、Memory 或决策触发器；
7. ACK 只证明中央已接收并保存该事件，不代表它认同 Agent Claim，也不代表项目验收通过。Evidence Gate 和最终裁决边界不变；
8. Ledger 必须有大小、条目和文本上限，采用尾部恢复与周期压缩，禁止为了“宁多勿漏”再次形成全历史 JSONL 扫描和内存锯齿；
9. 中央重启后可恢复最近奏报和去重身份，但不得据此假设地方 Runtime 仍存活；必须结合新连接 Inventory、Session、进程现场和新鲜心跳重新判定；
10. UI 应展示连接代次、最近连接时间和恢复事件标记，使仙人能区分“当前实时上报”“断联后补报”与“中央重启恢复的历史”。

因此地方节点可靠性的准确表达是：**连接采用至多一个当前代次，事件采用可去重的至少一次交付，事实接受与事实认同继续分离。**

### 七次边界深化：Provider 可替换的是认知 Harness，不是中央身份与权力

来源：2026-08-14 CodeTMux 将地方主脑从 Claude 专用实现收敛为 Runtime Adapter，并按 OpenAI 官方非交互协议真实接入 Codex。

“未来可以换 Codex/OpenCode”若只停留在 Provider 字段或 UI 下拉框，仍然是假抽象。不同成熟 Coding Runtime 的生命周期并不相同：Claude Code 可以保持一个持久 stream-json 进程，Codex 的非交互模式则是一轮一个 `codex exec --json` 进程、用明确 Session 执行 `resume`。中央不能为了接口整齐强迫所有 Runtime 模仿同一种进程模型，也不能把 Provider 命令散落在 Center、MCP 和 Relay 协议中。

候选约束如下：

1. **统一的是主权侧生命周期语义。** 中央只依赖 `start / send / status / stop`、Runtime/Session/Turn 身份和结构化 Event；持久进程、单轮进程、会话恢复与事件解析由各 Provider Adapter 自己实现；
2. **Provider 原生循环应完整保留。** 搜索仓库、制定局部计划、编辑、测试、失败修正和上下文压缩继续由 Claude Code/Codex 的原生 Harness 负责。OVO 不重写这些循环，只提供 Mandate、边界、调度、召回和最终 Evidence 裁决；
3. **Provider 选择是主权动作。** Inventory 只发布“本机有什么”，不自动启动任何模型。OVO 结合 Project、主机能力、成本和任务性质选择 Provider，Runtime Adapter 只执行该选择；
4. **恢复禁止猜测。** 每次恢复必须带可信 Session 身份；Codex 使用明确的 `exec resume <session>`，Claude 使用明确 Session 恢复。禁止跨 Project 使用模糊“最后会话”；
5. **权限模式必须显式映射。** Codex 的只读、项目可写和完整权限是 Adapter 参数，不得用“允许某些 Tool”冒充沙箱；默认只授予完成 Mandate 所需的最小模式，扩大权限需要中央明确表达；
6. **认证留在目标 Host。** 默认复用主机 CLI 已有登录并剥离中央继承的 API 环境变量。Provider Adapter 不保存、转发或记录模型密钥；
7. **协议真实接通优先于 Provider 数量。** 只有获得稳定、可验证的非交互输入、结构化输出、Session 恢复和取消协议后，才能宣布某 Provider 已接入。仅能探测到可执行文件时只能列为 Inventory 能力，不能在 Center 中伪装为可用主脑；
8. **升级红利不改变权力边界。** 上游 Runtime 的规划、子 Agent 或长周期循环能力增强后可被 OVO 自动利用，但其输出始终是奏折；中央身份、跨项目调度、资源所有权、验收和用户最终控制权不随 Provider 升级下放。

因此可替换 Runtime 的准确含义是：**OVO 保持稳定的主权身份和治理协议，把局部认知 Harness 当作可升级的外部执行器；替换大脑能力，不替换皇帝。**

### 八次边界深化：长周期 Runtime 的最小可信控制契约

来源：2026-08-14 用户追问 OpenClaw 还可借鉴什么；对照其 Invoke Cancel、连接能力天花板、动态能力撤回、输入序号与重连暂停机制后形成的候选优先级。

可替换 Provider 只解决“由谁思考”，没有解决“中央如何长期可靠控制一次思考”。对无人值守而言，模型质量之外更先决的是一个最小可信 Invoke 契约：同一道旨令不会执行两次，取消能够端到端传播，中央重启后不会拿历史奏报冒充当前现场，节点能力失效后不能继续接受新任务。

候选不变量如下：

1. **传输请求与业务轮次分离。** `requestId` 只关联一次网络调用，`runtimeInstanceId + turnId` 才是业务幂等身份；重试可换请求 ID，不能重复创建 Turn；
2. **取消是分层主权动作。** 取消当前 Turn、停止 Runtime、撤销 Mandate 是三种不同后果。它们共享端到端取消传播和进程树回收基础，但不能用一个“stop”混淆；超时只能触发其中一种已定义动作；
3. **断连未知不等于取消成功。** 中央本地释放等待不证明远端副作用停止；恢复后必须按稳定 Invoke/Turn 身份核对；
4. **历史事件不证明当前存活。** Event Ledger 负责事实续传，Runtime Registry 负责期望状态，Inventory/进程/Session 探针负责观测状态。中央以协调器比较三者，形成 running/recoverable/lost/conflicted，而不是自动重发；
5. **能力热更新只能收窄握手天花板。** 节点连接时声明本代 Capability Ceiling；运行中可因满载、认证失效或 Runtime 缺失撤回启动能力，但新增能力需要重新认证或新连接代次。即使无法启动新工作，status/cancel/report 仍须保留；
6. **永久故障应暂停而非无限退避。** 网络抖动继续指数退避；凭据错误、协议不兼容、版本过旧和需要人工处理的状态进入 reconnect-paused，直到相关配置或身份代次变化；
7. **流式输入需要连续性。** 输入序号、重复、缺口和溢出必须具有协议语义。队列连续性失效后应 seal 并请求核对，不能继续接收看似新的输入；
8. **地方能力发布只是候选目录。** Skill/MCP 描述符必须包含版本、Hash、入口、所需权限和适用范围；经过中央审核与 Mandate 限域后才能挂载，节点不能通过热发布自行扩大权力；
9. **容量扩展晚于控制闭合。** 一节点多 Runtime 和新增 Provider 应在 Cancel、Turn 幂等、Reconciliation 与 Capability Ceiling 完成后推进，否则并发只会放大重复执行和失控恢复。

这组候选把“皇帝可召回臣子”落实为工程条件：**中央不仅拥有发令权，还必须拥有可区分的取消权、可验证的恢复权和拒绝陈旧能力的权力。**

### 九次边界深化：取消请求、取消事实与 Runtime 收回必须分离

来源：2026-08-14 CodeTMux 完成地方主脑 `cancel-turn` 的首轮工程闭合，并分别验证 Claude Code 与 Codex 的真实生命周期差异。

`cancel-turn` 不能退化为一个没有业务身份的“杀进程”按钮。中央发出的取消必须绑定当前 `turnId`，否则断连、UI 迟到操作或重复消息可能误杀已经开始的新轮次；同样，传输 ACK 只能证明地方节点接受了取消请求，不能证明目标进程已经退出。首轮落地形成以下进一步约束：

1. `cancel-turn` 必须携带并核对当前 `runtimeInstanceId + turnId`；Runtime 或轮次已经变化时 fail closed，禁止把旧取消应用到重启后的 Runtime 或新轮次；
2. `cancel-turn` 只终止当前认知轮，保留逻辑 Runtime、可信 Session 和后续有界队列；`stop-runtime` 才终止整个 Runtime，`revoke-mandate` 还需进一步撤销授权与自动恢复资格；
3. Provider Adapter 可采用不同取消机制。Codex 的一轮一进程模型直接终止当前 `exec`，Claude 的持久进程模型在没有稳定公开的流式取消帧时回收当前进程并以明确 Session 恢复；中央语义保持一致，但不得伪造相同底层机制；
4. 超时与人工取消复用同一取消入口，避免形成另一条绕开状态机的 `SIGKILL` 路径；强制进程树回收只作为优雅中断失败后的兜底；
5. 取消具有两阶段事实：`cancel_requested` 表示命令受理，`turn_cancelled` 事件与 Runtime 的 `lastCancellation` 快照表示节点已观察到进程退出；事件是否已进入持久 Ledger 仍需单独确认。断连时缺失后一事实必须进入未知与核对，不能由中央自行宣布成功；
6. 取消期间迟到的 Provider `result` 不得把该轮标记为正常完成，也不得触发向正在退出的进程派发下一条队列输入；只有恢复后的 Runtime Ready 才能继续派发；
7. Session 尚未形成或无法可信恢复时，Adapter 必须拒绝“取消后继续”的承诺，或明确把下一轮视为新 Session 并重新注入授权边界。

因此取消权的工程含义是：**皇帝可以召回当前差事，但召回圣旨、臣子真正停手以及撤销整个任命是三个可观察、不可混淆的事实。**

### 十次边界深化：传输重试不能重复创造业务轮次

来源：2026-08-14 CodeTMux 完成地方主脑在线 Runtime 的 Turn 级幂等闭合。

网络请求成功、失败和超时属于通信事实，Agent 是否已经接受、正在执行或完成某一轮属于业务事实。两者共用一个 ID 会让中央在 ACK 丢失后陷入两种危险选择：不重试则可能丢旨，换 ID 重试则可能让同一任务执行两次。因此进一步形成以下约束：

1. `requestId` 只关联一次中央到地方的协议调用；`hostId + runtimeInstanceId + turnId` 才标识一次不可重复创建的业务轮次；
2. 同一 Runtime 收到相同 `turnId` 与相同输入 Hash 时，不得再次调用 Provider，而应返回原 Turn Receipt；Receipt 至少区分 `accepted / running / cancelling / completed / cancelled / failed / runtime-stopped`；
3. 相同 `turnId` 携带不同正文不是合法重试，而是身份冲突。节点必须 fail closed 并要求中央生成新 Turn，不能以后到正文覆盖先到正文；
4. `cancel-turn` 同样具有幂等语义：已取消轮次的重复取消返回原终态，不得再次中断已经开始的新轮次；
5. Turn Ledger 必须有界，且不能保存完整 Prompt 作为去重材料；只保存稳定 Hash、身份、状态和时间等最小审计字段，降低地方长期运行的内存与隐私负担；
6. 在线 Turn Ledger 只能证明当前逻辑 Runtime 生命周期内的去重。Daemon 或中央重启后，历史事件仍不能自动重建“正在运行”；持久期望状态、现场探针与 Session 协调必须由下一阶段 Runtime Reconciliation 闭合；
7. 用户界面和 MCP 发起新轮时由中央生成稳定 `turnId`，网络层重试必须复用它；用户修改任务正文意味着创建新轮，而不是修改既有轮次历史。

因此幂等边界的准确表达是：**驿站可以重复送同一封圣旨，但臣子只能领取一次；圣旨编号相同而正文不同则是冲突，不是更新。**

### 十一次边界深化：期望状态、历史奏报与当前存活必须三分

来源：2026-08-14 CodeTMux 完成地方主脑 Runtime Reconciliation 的中央 Registry 第一阶段。

中央重启后仍记得“朕希望这个 Runtime 继续运行”，不等于该进程此刻仍存活；Event Ledger 中最后一条 `working` 也只证明过去发生过一次观察。若把三者合并，系统会在历史事件上继续派发新旨令，或者把已经更换的地方主脑误认成旧任。因此形成以下约束：

1. Runtime Registry 持久保存中央期望状态，包括 Host、Provider、cwd、`runtimeInstanceId`、可信 Session、最后 Turn 与 `running/stopped` 期望；它不保存模型密钥、完整 Prompt 或连接 Token；
2. Relay Snapshot、Inventory、连接代次和 Runtime 事件构成观察状态。观察可以更新期望记录中的 Session 与最后 Turn，但不能反向创造“中央希望它运行”的授权；
3. 中央进程重启时，磁盘恢复出的所有历史连接状态必须先降为断联。只有新连接代次上的新鲜 Snapshot 与期望 Runtime 身份一致，才能协调为 `running`；
4. `recoverable` 表示中央仍期望运行、现场没有匹配的活跃 Runtime，但存在可信 Session 且 Provider 未明确不可用；它允许显式恢复，不允许自动重发历史 Turn；
5. `lost` 表示缺少可信 Session、Provider 明确不可用或无法证明可恢复；系统应请求调查、重新启动或人工决定，不能使用“最后会话”猜测；
6. `conflicted` 表示现场活跃 Runtime 与中央期望身份不一致，或中央期望停止而现场仍运行。冲突期间禁止发送新轮，必须由 OVO或仙人选择接管、停止或重新登记；
7. `stopped` 表示中央没有运行期望，或停止期望与现场一致；`running` 只表示当前连接上的匹配 Runtime 处于 starting/ready/working/cancelling 等可管理状态；
8. Center 必须同时展示 Provider 原始状态和协调结论。可恢复记录可预填 Provider、项目目录和可信 Session，但“按 Session 恢复”仍是一项新的显式主权动作；
9. 第一阶段观察证据来自受认证 Relay 的 Runtime Snapshot 与 Inventory。未来可增加 Provider 原生进程、Session 文件和项目目录探针提升证据等级，但不能因此绕过 Runtime 身份核对。

因此协调器的核心不变量是：**中央可以持久记住自己的意志，但必须重新观察世界，才能宣称臣子仍在履职。**

### 十二次边界深化：连接能力是冻结上限下的可撤回事实

来源：2026-08-14 CodeTMux 完成地方节点 Capability Ceiling 与 reconnect-paused 的首轮工程闭合。

地方节点报告“装有 Claude、Codex 或 tmux”只是一次能力观察，不能允许它在长连接期间自行把新能力加入中央授权面；同样，认证和协议永久错误若继续按网络抖动指数退避，会制造无意义连接风暴并掩盖真正需要修正的配置。连接建立必须同时形成一个可验证的能力上限和清晰的失败终态。

1. 节点必须先完成首轮 Inventory，再在认证握手中提交版本化 Capability Envelope；`ceiling`、排序后的 Hash 与连接代次共同构成本代能力上限；
2. 后续 Heartbeat 只允许提交 `current ⊆ ceiling`。CLI 退出、登录失效、容量耗尽或本地依赖消失可以撤回能力，但不能在同代连接中增加 Provider、tmux 或观察权限；
3. 新安装工具或恢复依赖只产生“下一连接代次可申请的新能力”，不会热增当前权力。节点必须重新认证、建立新代次并由中央重新接受能力上限；
4. 能力发布与动作授权继续分离。即便能力位于 Ceiling 和 Current 中，每次写操作仍需要中央 Command、目标范围、到期、控制代次与 Sovereign Capability；Manifest 不能替代圣旨；
5. 中央在发送前检查 Current Capability，节点在执行前再次检查，形成双端 fail-closed。状态、取消、停止和报告能力应尽量独立于新建 Runtime 能力，使节点失去扩展能力后仍可被观察和收回；
6. Heartbeat 试图修改 Ceiling、Hash 或加入上限外能力属于协议扩权事件。中央拒绝该代次并记录 `capability-escalation`，不能悄悄忽略后继续使用部分连接；
7. TCP 断开、短时拒绝和网络不可达属于 transient，可有限指数退避；Token 错误、协议不兼容、版本过旧、能力契约损坏和明确需要人工处理的状态进入 `reconnect-paused`；
8. 永久拒绝必须由中央返回类型化原因，节点落入可观察暂停状态并停止计时器。只有配置、凭据、版本或操作者显式恢复后才能创建下一连接代次；
9. Center 必须同时呈现当前能力数、能力上限身份、暂停原因和修复入口。普通 Offline 与“中央明确拒绝且等待操作”不能使用相同视觉和状态语义；
10. Capability Ceiling 是连接级边界，不替代 Runtime Reconciliation。节点有 `runtime.start.claude` 只证明可以请求启动，不证明某 Runtime 已启动、仍存活或已完成任务。

因此连接能力治理的工程表达是：**地方可以因现实变化主动交还权力，却不能在任期内自行扩权；中央可以容忍短暂失联，但不会把永久错误伪装成永无止境的重试。**
