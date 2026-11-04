---
title: 首页
---

<div class="hero" markdown>

# RTD 派工图志

一本持续补页的工程图志。**每个工艺区一张分幅图**，横向铺开；一套体例管住所有图幅的坐标；一层通用规则管住跨区的仲裁；几条跨幅专题画出区域之间怎么咬合。

半导体 Fab 实时派工（RTD / APS）· 8"/12" 前道

</div>

## 两层规则，正交不并列

整个派工逻辑拆成两层，**一层管跨区的事，一层管区内的事**。两层同时生效，缺一层都不完整：

- 只有区域层 —— 每个区都做对了，全厂还是不对，因为没人仲裁冲突；
- 只有通用层 —— 优先级定得再清楚，也没人算得出这个 Lot 到底上哪台机。

<div class="flowline" markdown>

<div class="flow-step" markdown>
<strong>① 通用层</strong><br>
谁先走 · 还能等多久 · 资源给谁
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>② 区域层</strong><br>
上哪台 · 哪个腔 · 哪一炉
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>③ 派工决策</strong><br>
带理由的推荐 + 可回放的日志
</div>

</div>

<div class="rtd-strip" markdown>

<span>分工依据</span> 判断标准只有一条：**这条规则的作用对象跨不跨区**  
<span>不跨区</span> 留在区域层，九个区各管自己  
<span>跨两区以上</span> 放进通用层，全厂唯一一份

</div>

### 第一层 · 通用规则（跨区生效）

管的是**跨区仲裁**：优先级在全厂只能有一个口径，Q-time 预算要横穿整条链，辅助资源和污染等级不能各区自说自话，派工的稳定性（改派成本）必须全局统一。**这一层不是第十个区，它和区域层正交。**

<div class="grid cards" markdown>

-   :material-sort-variant:{ .lg .middle } __G1 优先级与排序__ <span class="badge badge--wip">0.1</span>

    ---

    Hot Lot 分级与跨区继承、瓶颈识别与漂移、下游可接性、老化升权，以及 **P0–P4 仲裁序列**——两层冲突时按什么顺序压。

    [:octicons-arrow-right-24: 展开](common/01-priority.md)

-   :material-timer-sand:{ .lg .middle } __G2 时间窗走廊__ <span class="badge badge--wip">0.1</span>

    ---

    跨区 Q-time 串联、硬窗与软窗分离、余量怎么分、下游产能预留、超时降级四个分支。

    [:octicons-arrow-right-24: 展开](common/02-qtime-corridor.md)

-   :material-package-variant:{ .lg .middle } __G3 资源与污染__ <span class="badge badge--wip">0.1</span>

    ---

    Reticle / Pod / Boat / Pad 的跨区资源池、污染等级矩阵、载具池分配。**"计划可行、现场做不到"多半出在这。**

    [:octicons-arrow-right-24: 展开](common/03-resource-contamination.md)

-   :material-shield-sync:{ .lg .middle } __G4 稳定性治理__ <span class="badge badge--wip">0.1</span>

    ---

    冻结期、改派门槛、override 分型与回写、规则生命周期。RTD 每 30 秒重算一次，没有这一层，最优计划会把现场逼疯。

    [:octicons-arrow-right-24: 展开](common/04-stability.md)

</div>

[:octicons-arrow-right-24: 通用层总览：准入标准、四个模块怎么分工、落地节奏](common/index.md){ .md-button }

### 第二层 · 区域规则（区内生效）

管的是**区内选机**：这个 Lot 上哪台机、哪个腔体、哪一炉。九个区按**约束签名**划分，不是按工艺名——粒度不同、辅助资源不同，规则骨架就得分开配。

<div class="grid cards" markdown>

-   :material-flash:{ .lg .middle } __Litho 光刻__ <span class="badge badge--done">1.0</span>

    ---

    `Lot → Tool` · **Reticle + Pod 双独占**

    八重约束、九层重入、九类派工问题。内容最厚的一区，8 页。

    [:octicons-arrow-right-24: 展开](litho/index.md)

-   :material-weather-windy:{ .lg .middle } __Etch 刻蚀__ <span class="badge badge--done">1.0</span>

    ---

    `Lot → Chamber` · **腔体资质 / matching**

    chamber 级建模、PM 与 seasoning、recipe family 污染序列、Q-time 后窗。

    [:octicons-arrow-right-24: 展开](etch/index.md)

-   :material-thermometer:{ .lg .middle } __Diffusion 扩散__ <span class="badge badge--done">1.0</span>

    ---

    `Batch 组炉` · **Boat / 石英件**

    组炉与 SRB、温区 campaign、出炉波峰、前后双窗。RTP 归在本区。

    [:octicons-arrow-right-24: 展开](diffusion/index.md)

-   :material-arrow-collapse-down:{ .lg .middle } __Implant 注入__ <span class="badge badge--done">1.0</span>

    ---

    `Batch` · **气体 / 靶材**

    setup family、污染隔离与高能力机、束流整定、注入后退火窗口。

    [:octicons-arrow-right-24: 展开](implant/index.md)

-   :material-layers-triple:{ .lg .middle } __TF 薄膜__ <span class="badge badge--wip">0.1</span>

    ---

    `Lot → Chamber` · **靶材 / 前驱体**

    chamber dedication、靶材寿命分段、膜厚与应力反馈、返工回路。ECP 归在本区。

    [:octicons-arrow-right-24: 展开](tf/index.md)

-   :material-circle-slice-8:{ .lg .middle } __CMP 研磨__ <span class="badge badge--done">1.0</span>

    ---

    `Lot → Tool` · **Pad（连续退化）**

    pad 退化与分段、Run-to-Run 闭环、post-CMP 接续与污染隔离。

    [:octicons-arrow-right-24: 展开](cmp/index.md)

-   :material-water:{ .lg .middle } __Clean / Wet 清洗__ <span class="badge badge--wip">0.1</span>

    ---

    `Batch → Bath` · **Bath（化学消耗）**

    bath 寿命与批式合流、走廊起点、出口限流。**Q-time 超时的主要来源。**

    [:octicons-arrow-right-24: 展开](clean/index.md)

-   :material-ruler:{ .lg .middle } __Metrology 量测__ <span class="badge badge--wip">0.1</span>

    ---

    `Lot → Tool` · **校准片 / 标准片**

    采样规则、recipe 时长陷阱、数据紧急度。缺陷检测（Inspection）归在本区。

    [:octicons-arrow-right-24: 展开](metrology/index.md)

-   :material-swap-horizontal:{ .lg .middle } __Sorter / Stocker__ <span class="badge badge--wip">0.1</span>

    ---

    `Carrier → Slot` · **载具**

    载具池、污染分区、slot 与 port、8 寸状态可见性。

    [:octicons-arrow-right-24: 展开](sorter/index.md)

</div>

[:octicons-arrow-right-24: 区域总览：九区速查表、A/B/C 三组粒度、读一个区的五步法](zones/index.md){ .md-button }
[:octicons-arrow-right-24: 工艺基础：九个区在晶圆上各做什么](zones/process-basics.md){ .md-button }

## 其余入口

两层规则之上还有"怎么看全厂"，之下还有"怎么把它变成工程"：

<div class="grid cards" markdown>

-   :material-map:{ .lg .middle } __总图 · 全厂视野__

    ---

    全厂派工不是九个区各自最优的加总。三条耦合通道、瓶颈三层判定、决策空间。

    [:octicons-arrow-right-24: 全厂派工全景](overview/index.md)

-   :material-transit-connection-variant:{ .lg .middle } __跨幅专题__

    ---

    不属于任何单个区的图。Q-time 走廊、辅助资源流转、重入流、指标冲突链、AMHS 联动。

    [:octicons-arrow-right-24: 专题总览](topics/index.md)

-   :material-format-list-bulleted-square:{ .lg .middle } __图例__

    ---

    全库的符号系统。约束必须归到四类资源之一，没标类型就没法做横向对照。

    [:octicons-arrow-right-24: 约束四类](legend/constraint-types.md)

-   :material-cog-sync:{ .lg .middle } __方法论__

    ---

    APS/投料、RTS/RTD、规则库、求解器、仿真回放、系统接口，把区域知识变成工程闭环。

    [:octicons-arrow-right-24: 方法论总览](method/index.md)

-   :material-file-code-outline:{ .lg .middle } __样例__

    ---

    脱敏样例数据：Lot 状态、派工日志、规则命中、Q-time 窗口、历史回放。

    [:octicons-arrow-right-24: 样例总览](examples/index.md)

</div>

## 图幅与版本

### 区域层

| 图幅 | 能看清到哪一层 | 内容 | 版本 |
|---|---|---|---|
| [Litho 光刻](litho/index.md) | 区内 → 单台机、Reticle、温层 | 八重约束、九类问题、技术谱系、指标、路线图（8 页） | <span class="badge badge--done">1.0</span> |
| [Etch 刻蚀](etch/index.md) | 区内 → 单台机的每个腔体 | chamber 粒度、PM/seasoning、Q-time 后窗 | <span class="badge badge--done">1.0</span> |
| [Diffusion 扩散](diffusion/index.md) | 区内 → 炉管、温区、Boat | 批量组炉、温区切换、Boat/Quartzware | <span class="badge badge--done">1.0</span> |
| [Implant 注入](implant/index.md) | 区内 → 机台群与 setup 组 | setup family、污染隔离、注入后 Q-time | <span class="badge badge--done">1.0</span> |
| [TF 薄膜](tf/index.md) | 区内 → 腔体、靶材状态 | Chamber dedication、靶材分段、膜厚与应力反馈 | <span class="badge badge--wip">0.1</span> |
| [CMP 研磨](cmp/index.md) | 区内 → 单台机、Pad 状态 | Pad 退化、R2R 反馈、post-CMP 接续 | <span class="badge badge--done">1.0</span> |
| [Clean / Wet 清洗](clean/index.md) | 区内 → 槽体、bath 状态 | Bath 退化、批式合流、走廊起点、出口限流 | <span class="badge badge--wip">0.1</span> |
| [Metrology 量测](metrology/index.md) | 区内 → 单台机、recipe | 采样规则、recipe 时长陷阱、数据紧急度 | <span class="badge badge--wip">0.1</span> |
| [Sorter / Stocker](sorter/index.md) | 区内 → 载具、slot、分区 | 载具池、污染分区、8 寸状态可见性 | <span class="badge badge--wip">0.1</span> |

### 通用层

| 图幅 | 能看清到哪一层 | 内容 | 版本 |
|---|---|---|---|
| [通用规则层](common/index.md) | 跨区 → 规则的分层与仲裁 | 准入标准、P0–P4 仲裁序列、落地节奏 | <span class="badge badge--wip">0.1</span> |
| [G1 优先级与排序](common/01-priority.md) | 跨区 → 全厂唯一优先级 | Hot Lot 继承、瓶颈漂移、下游可接性、老化升权 | <span class="badge badge--wip">0.1</span> |
| [G2 时间窗走廊](common/02-qtime-corridor.md) | 跨区 → 整条链的时间预算 | 硬窗软窗、余量分配、下游预留、超时降级 | <span class="badge badge--wip">0.1</span> |
| [G3 资源与污染](common/03-resource-contamination.md) | 跨区 → 资源池与污染矩阵 | 四类共享模式、跨区资源池、载具池分配 | <span class="badge badge--wip">0.1</span> |
| [G4 稳定性治理](common/04-stability.md) | 跨区 → 派工本身的稳定性 | 冻结期、改派门槛、override 回写、规则下线 | <span class="badge badge--wip">0.1</span> |

### 总图、专题与方法

| 图幅 | 能看清到哪一层 | 内容 | 版本 |
|---|---|---|---|
| [全厂派工全景](overview/index.md) | 全厂 → 只看工艺区之间 | 区域角色、三条耦合通道、决策空间 | <span class="badge badge--done">1.0</span> |
| [瓶颈判定](overview/bottleneck.md) | 全厂 → 只看工艺区之间 | 三层判定、TOC 推论、动态瓶颈与四个隐性瓶颈 | <span class="badge badge--done">1.0</span> |
| [Q-time 走廊](topics/qtime-corridor.md) | 跨区 → 一进一出两条腿 | 入口限流、黄色预警、下游预约 | <span class="badge badge--done">1.0</span> |
| [辅助资源流转](topics/support-resource.md) | 跨区 → 跟着资源走 | 资源状态、位置、寿命、锁定机制 | <span class="badge badge--done">1.0</span> |
| [重入流路径](topics/reentrant-flow.md) | 跨区 → 跟着 Lot 回环走 | 回流预测、层间配平、负荷换算 | <span class="badge badge--done">1.0</span> |
| [指标冲突链](topics/metric-conflict.md) | 跨区 → 只看指标之间 | 指标口径、基线实验、验收写法 | <span class="badge badge--done">1.0</span> |
| [AMHS/MCS 联动](topics/amhs-mcs-coupling.md) | 跨区 → 机台与搬送之间 | ETA、port、stocker、pre-position、双资源同步 | <span class="badge badge--done">1.0</span> |
| [APS 与投料控制](method/release-control.md) | 不分区 → 全厂一个水池 | WIP cap、CONWIP/CONLOAD、瓶颈 buffer | <span class="badge badge--done">1.0</span> |
| [仿真与历史回放](method/simulation-replay.md) | 不分区 → 沿时间轴回看 | 历史回放、影子模式、局部接管 | <span class="badge badge--done">1.0</span> |
| [求解器与算法选型](method/solver-playbook.md) | 不分区 → 一个问题一层算法 | 规则、SRB、CP、MILP、CP-SAT、fallback | <span class="badge badge--done">1.0</span> |
| [系统架构与接口](method/system-architecture.md) | 不分区 → 系统与系统之间 | MES/EAP/AMHS/YMS/APS 状态源与写回 | <span class="badge badge--done">1.0</span> |

*读法：这一列说的是每页往下能看清到哪一层——想先建立地图感看总图那两张，想改现场规则就看区域层那九行，想处理"每个区都对、全厂不对"就看通用层。*

**版本规则。** 全站按页维护版本号，只反映内容成熟度，不反映页面数量：

- `1.0` — 内容完整，可直接照做：有约束结构、现场症状、可验证字段和指标；
- `1.x` — 在原结构上补案例、修错误、补来源，结构没动；
- `2.0` — 结构性重写或扩成多页专题，老读者需要重看一遍。

每次实质更新同步打版本，并在 `CHANGELOG.md` 记一条；只改错别字和排版不升版本。

## 按目的找路

=== "第一次来"

    1. [全厂派工全景](overview/index.md) — 先建立"九个区是什么关系"的地图感
    2. [Litho 核心结论速览](litho/00-结论速览.md) — 六条结论，看懂 RTD 真正难在哪
    3. [约束四类](legend/constraint-types.md) — 全库的符号系统，后面每页都用
    4. [通用规则层](common/index.md) — 搞清"哪条规则该放哪一层"

=== "要建模型"

    - [约束模型](method/constraints.md) — 先把现场约束翻译成资源集合
    - [APS 与投料控制](method/release-control.md) — 判断问题是不是 WIP 和 release 造成的
    - [数据模型](method/data-model.md) — 再确认 Lot、资源、Q-time、规则、决策日志字段
    - [规则库设计](method/rule-library.md) — 决定规则如何上线、回放和下线
    - [通用规则层](common/index.md) — 判断这条规则该放区域层还是通用层
    - [求解器与算法选型](method/solver-playbook.md) — 判断该用规则、SRB、CP 还是 MILP

=== "要落地一个区"

    - [瓶颈判定](overview/bottleneck.md) — 先确认这个区是不是瓶颈
    - [区域总览](zones/index.md) — 用五步法读这个区：辅助资源 → 粒度 → setup → Q-time → 闭环
    - [Litho 落地路线图](litho/06-落地路线图.md) — Phase 0–4 可直接套用
    - [避坑清单](litho/08-避坑清单.md) — 别人的坑，别重踩

=== "遇到具体问题"

    - Q-time 老是超 → [Q-time 走廊](topics/qtime-corridor.md) / [G2 时间窗走廊](common/02-qtime-corridor.md)
    - 机台空转但队列有料 → [辅助资源流转](topics/support-resource.md)
    - 该提吞吐还是压周期 → [指标冲突链](topics/metric-conflict.md)
    - 规则越加越多调不动 → [规则库设计](method/rule-library.md)
    - 同一个 Lot 各区优先级不一致 → [G1 优先级与排序](common/01-priority.md)
    - 计划可行但现场总做不到 → [G3 资源与污染](common/03-resource-contamination.md)
    - 系统频繁改派、操作员不听了 → [G4 稳定性治理](common/04-stability.md)
    - 字段不知道该怎么建 → [数据模型](method/data-model.md)
    - 现场症状不知道怎么查 → [症状诊断手册](method/symptom-playbook.md)
    - WIP 高、CT 压不下去 → [APS 与投料控制](method/release-control.md)
    - 规则不敢上线 → [仿真与历史回放](method/simulation-replay.md)
    - 求解器不知道怎么选 → [求解器与算法选型](method/solver-playbook.md)
    - 派工推荐现场执行不了 → [系统架构与接口](method/system-architecture.md) / [AMHS/MCS 联动](topics/amhs-mcs-coupling.md)

## 数据边界

!!! warning "关于文中所有数字"
    引用的改善幅度来自公开文献、公开案例或理论推导，**基线各不相同、年份和厂型也不相同**。只能当量级参考，不能当项目承诺值。正式落地要写的是"在本厂基线上实测改善 X%"的验证方法和约束指标。

本图志只收公开文献与个人分析，不含任何具体公司的产线数据、产能、良率、报价与未公开评估材料。
