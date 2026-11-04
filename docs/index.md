---
title: 首页
---

<div class="hero" markdown>

# RTD 派工图志

九个工艺区，两层规则。把每个区的约束拆清楚，再把跨区的仲裁单独拎出来。

半导体 Fab 实时派工（RTD / APS）· 8"/12" 前道

</div>

## 两层规则

**区域层管区内，通用层管跨区。** 两层同时生效：只做区域层，每个区都对、全厂还是不对；只做通用层，优先级定得再清楚，也没人算得出能不能接。

<div class="flowline" markdown>

<div class="flow-step" markdown>
<strong>通用层</strong><br>
谁先走 · 等多久 · 资源给谁
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>区域层</strong><br>
上哪台 · 哪个腔 · 哪一炉
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>派工决策</strong><br>
带理由 · 可回放
</div>

</div>

## 通用规则 · 作用对象跨区

作用对象跨两个区以上，或必须在全厂唯一确定的规则，才放这一层。**它不是第十个区，和区域层正交。**

- [**G1 优先级与排序**](common/01-priority.md) — Hot Lot 跨区继承、瓶颈漂移、P0–P4 仲裁序列
- [**G2 时间窗走廊**](common/02-qtime-corridor.md) — 硬窗软窗、余量分配、下游预留、超时降级
- [**G3 资源与污染**](common/03-resource-contamination.md) — Reticle/Pod/Boat/Pad 跨区资源池、载具池
- [**G4 稳定性治理**](common/04-stability.md) — 冻结期、改派门槛、override 回写

[通用层总览](common/index.md){ .md-button }

## 区域规则 · 作用对象在区内

九个区按**约束签名**划分，不按工艺名。粒度不同、辅助资源不同，规则骨架就得分开配。

| 区域 | 决策粒度 | 一句话 |
|---|---|---|
| [Litho 光刻](litho/index.md) | `Lot → Tool` | Reticle + Pod 双独占，八重约束、九层重入 |
| [Etch 刻蚀](etch/index.md) | `Lot → Chamber` | 腔体资质与 matching，Q-time 后窗 |
| [Diffusion 扩散](diffusion/index.md) | `Batch 组炉` | 组炉与 SRB，前后双窗（RTP 归此区） |
| [Implant 注入](implant/index.md) | `Batch` | setup family、污染隔离、退火窗口 |
| [TF 薄膜](tf/index.md) | `Lot → Chamber` | chamber dedication、靶材寿命（ECP 归此区） |
| [CMP 研磨](cmp/index.md) | `Lot → Tool` | Pad 连续退化、R2R 闭环 |
| [Clean / Wet 清洗](clean/index.md) | `Batch → Bath` | bath 寿命、走廊起点、出口限流 |
| [Metrology 量测](metrology/index.md) | `Lot → Tool` | 采样规则、recipe 时长陷阱（缺陷检测归此区） |
| [Sorter / Stocker](sorter/index.md) | `Carrier → Slot` | 载具池、污染分区 |

[九区速查](zones/index.md){ .md-button }
[工艺基础](zones/process-basics.md){ .md-button }

## 怎么读

- **先建立地图感** → [全厂派工全景](overview/index.md)，再看 [Litho 核心结论速览](litho/00-结论速览.md)（六条结论）
- **要建模型** → [约束模型](method/constraints.md) → [数据模型](method/data-model.md) → [规则库设计](method/rule-library.md)
- **要落地一个区** → [瓶颈判定](overview/bottleneck.md) → [Litho 落地路线图](litho/06-落地路线图.md) → [避坑清单](litho/08-避坑清单.md)
- **遇到具体问题** → [症状诊断手册](method/symptom-playbook.md)：[Q-time 超时](topics/qtime-corridor.md) · [机台空转](topics/support-resource.md) · [频繁改派](common/04-stability.md) · [指标打架](topics/metric-conflict.md)

其余：[跨幅专题](topics/index.md) · [约束四类](legend/constraint-types.md) · [方法论](method/index.md) · [样例](examples/index.md) · [术语表](legend/glossary.md)

---

!!! warning "关于文中所有数字"
    改善幅度来自公开文献、公开案例或理论推导，基线各不相同，只能当量级参考，不能当项目承诺值。本图志只收公开资料与个人分析，不含任何具体公司的产线数据、产能、良率与报价。
