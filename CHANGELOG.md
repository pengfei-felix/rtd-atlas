# 更新记录

## 2026-11-04

### 通用规则层（结构性新增）

- 新增 `docs/common/` 目录：`index.md`、`01-priority.md`、`02-qtime-corridor.md`、`03-resource-contamination.md`、`04-stability.md`，共 5 页，标记 `0.1`。
- 定位：通用层与区域层是**正交**的两层，不是并列的第十个区。`scope` 为 fab，区域层为 area。
- 三条独立存在的理由：冲突仲裁必须集中、瓶颈会漂移、跨区规则无处安放。
- 准入标准：作用对象跨两个区以上，或需全局唯一确定，或需跨区数据才能计算。
- 四个模块：G1 优先级与排序（Hot Lot 继承、瓶颈漂移、下游可接性、老化升权）、G2 时间窗走廊（硬窗软窗、余量分配、下游预留、超时降级）、G3 资源与污染（四类共享模式、跨区资源池、污染矩阵、载具池）、G4 稳定性治理（冻结期、改派门槛、override 回写、规则下线）。
- 新增显式仲裁序列 P0–P4，写死在 G1 里，约束全部九区。
- `method/rule-library.md` 补充交叉引用，说明「全局规则」这一层已展开成独立模块；两页分工为「一条规则怎么管」vs「规则分几层、谁管谁」。
- `method/index.md` 新增通用层入口。

### 区域补齐（四区）

- 新增 `docs/tf/`：TF 薄膜。腔体 dedication 三层、靶材寿命分段、膜厚/均匀性/应力三反馈、返工系数 2~3 倍。
- 新增 `docs/clean/`：Clean / Wet 清洗。Bath 退化分段、批式合流（欠载可走）、Q-time 走廊起点必须按事件计时、出口限流优先于吞吐。
- 新增 `docs/metrology/`：Metrology 量测。采样规则四类、按 recipe 时长算产能、数据紧急度（消费者身份）、缺陷检测并入但单列。
- 新增 `docs/sorter/`：Sorter / Stocker。决策粒度为 carrier、载具三维状态、污染分区三规则、8 寸状态可见性建模。
- 三个归并项写入文档：RTP → Diffusion（区内分子模型）、Inspection → Metrology（区内分机台类型）、ECP → TF（区内留 bath 子类型）。
- 明确 Multichamber 不属于区域，是决策粒度维度的一个取值，挂 Etch / TF / Implant 之下。

### 结构校验（避免断层）

- `zones/index.md` 重构：五个问题定区、九区速查表、按决策粒度分 A/B/C 三组（说明哪几组可共用规则骨架）、九区档案、通用层入口。
- `zones/process-basics.md`：对照表与正文从五区扩到九区，每区新增「做什么 / 为什么是派工难点 / 待补」三段。
- `overview/index.md`：区域角色卡从 5 张扩为 9 张，每张保持「派工角色 + 工艺」双段结构，入口保留「进入分幅 / 工艺基础」两行（工艺基础指向 `process-basics.md` 对应锚点）；决策空间表加 4 行；新增「九个区之上还有一层」。
- `overview/bottleneck.md`：「五个区什么时候成为瓶颈」改为九区，补 TF/Clean/Metrology/Sorter 四个隐性瓶颈，并给出识别方法。
- `legend/constraint-types.md`：全区域对照表补 4 行，新增两条分布规律（辅助资源强的区最难配；时间窗起点集中在 Clean）。
- `docs/index.md`：五条主线改六条（加通用规则），版本表补 9 行，「要建模型」与「遇到具体问题」补通用层入口。
- `topics/index.md`、`legend/glossary.md`：同步「五区」表述。
- `mkdocs.yml`：区域分幅导航补 4 个区及子页，新增「通用规则」一级导航。
- `README.md`：层级表、版本表、目录结构、维护约定同步；「新增区域」改为五处联动清单。
- **校验**：`mkdocs build --strict` 通过，无坏链。

### 版本体系

- 全站改为按页维护版本号：首页「图幅状态」升级为「图幅与版本」，16 个图幅统一标记 `1.0`。
- 版本语义：`1.0` 内容完整可照做；`1.x` 补案例、修错、补来源，结构不动；`2.0` 结构性重写或扩成多页专题。只改错别字和排版不升版本。
- 首页表格「比例尺」列改为「能看清到哪一层」，用直白的视野描述替代 `1:100 万 / 1:20 万 / 1:5 万`，首次阅读不再需要先懂地图分幅。
- `README.md`「当前状态」表同步为「当前版本」，维护约定中的「状态变更」改为「版本变更」，并修正 Litho 正文页数（10 页 → 8 页）。

### 区域工艺基础

- `overview/index.md` 的区域角色卡片改成双段结构：`派工角色` 讲这个区在派工网络里的位置，`工艺` 讲晶圆上实际发生了什么，两类信息不再混在一段里。
- 每张区域卡片增加第二个入口「工艺基础」，分别指向新增页的对应锚点。
- 新增 `docs/zones/process-basics.md`（骨架 v0.1）：五个区「做什么 / 为什么是派工难点 / 待补清单」，加一张五区一句话对照表；详细工艺内容后续逐节补。
- `zones/index.md` 各区档案补工艺基础入口，状态字样统一为 `1.0`。
- `mkdocs.yml` 区域分幅导航新增「工艺基础」。

### 内容补齐

- 补齐 `CMP` 区域页：Pad 退化、conditioner、slurry、Run-to-Run 反馈、post-CMP 接续。
- 补齐 `Implant` 区域页：setup family、污染隔离、束流整定、注入后 Q-time。
- 重写 `重入流路径` 专题：回流预测、层间配平、负荷换算、资源前置。
- 重写 `指标冲突链` 专题：Little's Law、指标口径、基线实验、验收写法。
- 补齐 `Etch` 区域页：chamber 粒度、PM/clean/seasoning、Q-time 后窗。
- 补齐 `Diffusion` 区域页：组炉策略、温区切换、Boat/Quartzware、前后 Q-time。
- 补齐 `Q-time 走廊` 专题：入口限流、黄色预警、下游预约、违规归因。
- 补齐 `辅助资源流转` 专题：资源类型、位置、寿命、锁定、系统边界。

### 方法论增强

- 重写 `约束模型`：四类资源、区域映射、资源字段、权重标定。
- 重写 `规则库设计`：规则字段、冲突类型、生命周期、上线/下线机制。
- 重写 `RTS 与 RTD 分层`：覆盖原因码、偏离阈值、覆盖日志、接口边界。
- 重写 `指标体系`：三层指标、口径、基线、异常日处理、验收模板。
- 新增 `数据模型`：Lot、Tool/Chamber、辅助资源、Q-time、Rule、Decision、Event。

### 工程规范

- 重写 `README.md`，明确项目定位、目录结构、写作口径、页面组件、发布流程和后续路线。
- 新增 `CONTRIBUTING.md`。
- 新增 `LICENSE`，采用 CC BY-NC-SA 4.0。
- 新增 `CHANGELOG.md`。
- 新增视觉组件：`rtd-strip`、`flowline`、`timeline`、`code-title`。
- 删除厂商分析预留页面与 Litho 厂商横评 / 选型 Checklist 页面，项目定位收敛为 RTD 排程知识库。
- 删除根目录旧版 Litho 原稿归档，避免维护两套内容。
- 新增 `docs/method/symptom-playbook.md`，作为从现场症状反推约束、字段、规则和指标的诊断入口。

### RTD/APS 工程方法论 2.0

- 新增 `APS 与投料控制`：WIP cap、CONWIP/CONLOAD、瓶颈 buffer、Factory Physics 底线。
- 新增 `APS / RTS / RTD 闭环`：三层时间尺度、RTD 覆盖原因码、滚动节奏和偏差回灌。
- 新增 `仿真与历史回放`：事件流、回放引擎、影子模式、上线门槛和失真风险。
- 新增 `求解器与算法选型`：规则、SRB、CP、MILP、CP-SAT、rolling horizon、fallback。
- 新增 `系统架构与接口`：MES/EAP/AMHS/YMS/APS 状态源、状态冲突、写回与回滚。
- 新增 `AMHS/MCS 联动` 专题：FOUP ETA、port/stocker、pre-position、双资源同步与搬送优先级。
- 新增 `docs/examples/`：Lot 状态、派工决策日志、规则命中日志、Q-time 窗口、历史回放样例。
- 深化 Etch / Diffusion / Implant / CMP 区域页，增加典型问题深度案例库。

### Litho 同级深化专题

- 新增 Diffusion 深度页：组炉与 SRB、Q-time 与温区 Campaign、Boat 与出炉波峰、避坑清单。
- 新增 Etch 深度页：Chamber 级建模、PM/Clean/Seasoning、Recipe Family 与污染序列、Rework 与 Q-time 后窗、避坑清单。
- 新增 Implant 深度页：Setup Family 与换线矩阵、污染隔离与高能力机保护、注入后窗口与避坑。
- 新增 CMP 深度页：Pad 状态与 R2R 反馈、Post-CMP 与污染隔离。
- 新增 Q-time 深度页：窗口建模、入口限流与下游预约、违规归因与回放。
- 新增 AMHS/MCS 深度页：ETA 与 pre-position、Port 与 Stocker、搬送优先级冲突。
- 新增 APS 投料控制深度页：Little/Critical WIP、CONWIP/CONLOAD/LCONWIP、Release 与 RTD 联动。
- 新增仿真回放深度页：事件流模型、Shadow Mode、规则 A/B 与上线门槛。
