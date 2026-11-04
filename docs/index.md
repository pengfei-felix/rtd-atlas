---
title: 首页
---

<div class="hero" markdown>

# RTD 派工图志

一本持续补页的工程图志。**每个工艺区一张分幅图**，横向铺开；一套体例管住所有图幅的坐标；几条跨幅专题画出区域之间怎么咬合。

半导体 Fab 实时派工（RTD / APS）· 8"/12" 前道

</div>

## 五条主线

<div class="grid cards" markdown>

-   :material-map:{ .lg .middle } __总图__

    ---

    全厂派工不是五个区各自最优的加总。先看瓶颈在哪、区域之间怎么互相拉扯。

    [:octicons-arrow-right-24: 全厂派工全景](overview/index.md)

-   :material-map-legend:{ .lg .middle } __区域分幅__

    ---

    每个区一套约束结构。光刻是三体问题，炉管是组炉问题，注入是换线问题，CMP 是闭环问题。

    [:octicons-arrow-right-24: 区域对比](zones/index.md)

-   :material-transit-connection-variant:{ .lg .middle } __跨幅专题__

    ---

    不属于任何单个区的图。Q-time 走廊、辅助资源流转、重入流、指标冲突链。

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

## 图幅状态

| 图幅 | 比例尺 | 内容 | 状态 |
|---|---|---|---|
| [全厂派工全景](overview/index.md) | 1:100 万 | 区域角色、三条耦合通道、决策空间 | <span class="badge badge--done">已绘</span> |
| [瓶颈判定](overview/bottleneck.md) | 1:100 万 | 三层判定、TOC 推论、动态瓶颈 | <span class="badge badge--done">已绘</span> |
| [Litho 光刻](litho/index.md) | 1:5 万 | 八重约束、九类问题、技术谱系、指标、路线图 | <span class="badge badge--done">8 页</span> |
| [Etch 刻蚀](etch/index.md) | 1:20 万 | chamber 粒度、PM/seasoning、Q-time 后窗 | <span class="badge badge--done">已绘</span> |
| [Diffusion 扩散](diffusion/index.md) | 1:20 万 | 批量组炉、温区切换、Boat/Quartzware | <span class="badge badge--done">已绘</span> |
| [Implant 注入](implant/index.md) | 1:20 万 | setup family、污染隔离、注入后 Q-time | <span class="badge badge--done">已绘</span> |
| [CMP 研磨](cmp/index.md) | 1:20 万 | Pad 退化、R2R 反馈、post-CMP 接续 | <span class="badge badge--done">已绘</span> |
| [Q-time 走廊](topics/qtime-corridor.md) | 跨幅 | 入口限流、黄色预警、下游预约 | <span class="badge badge--done">已绘</span> |
| [辅助资源流转](topics/support-resource.md) | 跨幅 | 资源状态、位置、寿命、锁定机制 | <span class="badge badge--done">已绘</span> |
| [重入流路径](topics/reentrant-flow.md) | 跨幅 | 回流预测、层间配平、负荷换算 | <span class="badge badge--done">已绘</span> |
| [指标冲突链](topics/metric-conflict.md) | 跨幅 | 指标口径、基线实验、验收写法 | <span class="badge badge--done">已绘</span> |
| [AMHS/MCS 联动](topics/amhs-mcs-coupling.md) | 跨幅 | ETA、port、stocker、pre-position、双资源同步 | <span class="badge badge--done">已绘</span> |
| [APS 与投料控制](method/release-control.md) | 方法 | WIP cap、CONWIP/CONLOAD、瓶颈 buffer | <span class="badge badge--done">2.0</span> |
| [仿真与历史回放](method/simulation-replay.md) | 方法 | 历史回放、影子模式、局部接管 | <span class="badge badge--done">2.0</span> |
| [求解器与算法选型](method/solver-playbook.md) | 方法 | 规则、SRB、CP、MILP、CP-SAT、fallback | <span class="badge badge--done">2.0</span> |
| [系统架构与接口](method/system-architecture.md) | 方法 | MES/EAP/AMHS/YMS/APS 状态源与写回 | <span class="badge badge--done">2.0</span> |

## 按目的找路

=== "第一次来"

    1. [全厂派工全景](overview/index.md) — 先建立"五个区是什么关系"的地图感
    2. [Litho 核心结论速览](litho/00-结论速览.md) — 六条结论，看懂 RTD 真正难在哪
    3. [约束四类](legend/constraint-types.md) — 全库的符号系统，后面每页都用

=== "要建模型"

    - [约束模型](method/constraints.md) — 先把现场约束翻译成资源集合
    - [APS 与投料控制](method/release-control.md) — 判断问题是不是 WIP 和 release 造成的
    - [数据模型](method/data-model.md) — 再确认 Lot、资源、Q-time、规则、决策日志字段
    - [规则库设计](method/rule-library.md) — 决定规则如何上线、回放和下线
    - [求解器与算法选型](method/solver-playbook.md) — 判断该用规则、SRB、CP 还是 MILP

=== "要落地一个区"

    - [瓶颈判定](overview/bottleneck.md) — 先确认这个区是不是瓶颈
    - [Litho 落地路线图](litho/06-落地路线图.md) — Phase 0–4 可直接套用
    - [避坑清单](litho/08-避坑清单.md) — 别人的坑，别重踩

=== "遇到具体问题"

    - Q-time 老是超 → [Q-time 走廊](topics/qtime-corridor.md)
    - 机台空转但队列有料 → [辅助资源流转](topics/support-resource.md)
    - 该提吞吐还是压周期 → [指标冲突链](topics/metric-conflict.md)
    - 规则越加越多调不动 → [规则库设计](method/rule-library.md)
    - 字段不知道该怎么建 → [数据模型](method/data-model.md)
    - 现场症状不知道怎么查 → [症状诊断手册](method/symptom-playbook.md)
    - WIP 高、CT 压不下去 → [APS 与投料控制](method/release-control.md)
    - 规则不敢上线 → [仿真与历史回放](method/simulation-replay.md)
    - 求解器不知道怎么选 → [求解器与算法选型](method/solver-playbook.md)
    - 派工推荐现场执行不了 → [系统架构与接口](method/system-architecture.md) / [AMHS/MCS 联动](topics/amhs-mcs-coupling.md)

## 数据边界

!!! warning "关于文中所有数字"
    引用的改善幅度来自公开文献、公开案例或理论推导，**基线各不相同、年份和厂型也不相同**。只能当量级参考，不能当项目承诺值。正式落地要写的是“在本厂基线上实测改善 X%”的验证方法和约束指标。

本图志只收公开文献与个人分析，不含任何具体公司的产线数据、产能、良率、报价与未公开评估材料。
