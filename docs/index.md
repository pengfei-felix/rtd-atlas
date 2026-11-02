---
title: 首页
---

<div class="hero" markdown>

# RTD 派工图志

一本持续补页的工程图志。**每个工艺区一张分幅图**，横向铺开；一套体例管住所有图幅的坐标；几条跨幅专题画出区域之间怎么咬合。

半导体 Fab 实时派工（RTD / APS）· 8"/12" 前道

</div>

## 三条主线

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

</div>

## 图幅状态

| 图幅 | 比例尺 | 内容 | 状态 |
|---|---|---|---|
| [全厂派工全景](overview/index.md) | 1:100 万 | 区域角色、三条耦合通道、决策空间 | <span class="badge badge--done">已绘</span> |
| [瓶颈判定](overview/bottleneck.md) | 1:100 万 | 三层判定、TOC 推论、动态瓶颈 | <span class="badge badge--done">已绘</span> |
| [Litho 光刻](litho/index.md) | 1:5 万 | 八重约束、九类问题、厂商横评 | <span class="badge badge--done">10 页</span> |
| [Etch 刻蚀](etch/index.md) | 1:50 万 | 腔体粒度、Q-time | <span class="badge badge--wip">骨架</span> |
| [Diffusion 扩散](diffusion/index.md) | 1:50 万 | 批量组炉、长驻留 | <span class="badge badge--wip">骨架</span> |
| [Implant 注入](implant/index.md) | 1:50 万 | 换线成本、序列相关 | <span class="badge badge--wip">骨架</span> |
| [CMP 研磨](cmp/index.md) | 1:50 万 | Pad 退化、闭环反馈 | <span class="badge badge--wip">骨架</span> |
| [Q-time 走廊](topics/qtime-corridor.md) | 跨幅 | 累积挤压、走廊容量 | <span class="badge badge--wip">初稿</span> |
| [辅助资源流转](topics/support-resource.md) | 跨幅 | 四种共享模式 | <span class="badge badge--wip">初稿</span> |
| [重入流路径](topics/reentrant-flow.md) | 跨幅 | 队列构成、回流预测 | <span class="badge badge--wip">初稿</span> |
| [指标冲突链](topics/metric-conflict.md) | 跨幅 | Little's Law、四条链 | <span class="badge badge--wip">初稿</span> |

## 按目的找路

=== "第一次来"

    1. [全厂派工全景](overview/index.md) — 先建立"五个区是什么关系"的地图感
    2. [Litho 核心结论速览](litho/00-结论速览.md) — 六条结论，看懂 RTD 真正难在哪
    3. [约束四类](legend/constraint-types.md) — 全库的符号系统，后面每页都用

=== "要做选型"

    - [厂商方案横评](litho/04-厂商横评.md) — 国际 + 国产逐家对比
    - [选型 Checklist](litho/07-选型-checklist.md) — 可直接进技术标
    - [差距判断](vendor/gap.md) — 国产差距到底在哪

=== "要落地一个区"

    - [瓶颈判定](overview/bottleneck.md) — 先确认这个区是不是瓶颈
    - [Litho 落地路线图](litho/06-落地路线图.md) — Phase 0–4 可直接套用
    - [避坑清单](litho/08-避坑清单.md) — 别人的坑，别重踩

=== "遇到具体问题"

    - Q-time 老是超 → [Q-time 走廊](topics/qtime-corridor.md)
    - 机台空转但队列有料 → [辅助资源流转](topics/support-resource.md)
    - 该提吞吐还是压周期 → [指标冲突链](topics/metric-conflict.md)
    - 规则越加越多调不动 → [规则库设计](method/rule-library.md)

## 数据边界

!!! warning "关于文中所有数字"
    引用的改善幅度（利用率 +1%~2%、吞吐 +2%~9.4%、Reticle Move −5%、Q-time −4%）来自公开文献与厂商案例，**基线各不相同、年份横跨 2011–2025**。只能当量级参考，不能当供应商承诺值。合同里要写的是"在贵厂基线上实测改善 X%"的验证方法与对赌条款。

本图志只收公开文献与个人分析，不含任何具体公司的产线数据、产能、良率与报价。
