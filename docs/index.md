---
title: 首页
---

<div class="hero" markdown>

# RTD 派工图志

九个工艺区，两层规则。把每个区的约束拆清楚，再把各区共用的那层基础规则提炼出来。

半导体 Fab 实时派工（RTD / APS）· 8"/12" 前道

</div>

## 两层规则

**通用层是每个区都要用的基础规则，区域层加载它、再补本区的工艺特化。** 不是两套并列的规则，是一层托着一层。

<div class="flowline" markdown>

<div class="flow-step" markdown>
<strong>通用层</strong><br>
基础排序：谁优先 · 老化 · setup
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>区域层</strong><br>
加载 + 特化：上哪台 · 哪个腔
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>派工决策</strong><br>
带理由 · 可回放
</div>

</div>

## 通用规则 · 每个区都加载的基础规则

九个区都要排序，都要处理优先级继承、老化升权、setup 损失、下游可接性——**这部分逻辑是一样的，只有参数不同**，所以提炼成一份，九个区共用。另外一类是单个区看不到全貌的（时间窗走廊、资源池、全局唯一的优先级），也只能放这里。

- [**G1 优先级与排序**](common/01-priority.md) — Hot Lot 跨区继承、瓶颈漂移、P0–P4 仲裁序列
- [**G2 时间窗走廊**](common/02-qtime-corridor.md) — 硬窗软窗、余量分配、下游预留、超时降级
- [**G3 资源与污染**](common/03-resource-contamination.md) — Reticle/Pod/Boat/Pad 跨区资源池、载具池
- [**G4 稳定性治理**](common/04-stability.md) — 冻结期、改派门槛、override 回写

[通用层总览](common/index.md){ .md-button }

!!! note "一条硬规矩"
    区域层**只能改参数，不能改逻辑**。阈值、权重、时间常数，各区按自己的工艺调；要改判断逻辑，就是通用层升版本，对九个区一起生效。在区里复制一份改逻辑，就是双轨的起点。

## 区域规则 · 加载通用层，再补本区特化

九个区都在通用层之上跑，各自追加**通用层算不出来的东西**：Litho 要锁 Reticle 和 Pod，Diffusion 要组炉，CMP 要跟 pad 退化，Clean 要跟 bath 寿命。划分依据是**约束签名**，不是工艺名。

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

[区域总览](zones/index.md){ .md-button }
[工艺基础](basics/index.md){ .md-button }

## 怎么读

- **先建立地图感** → [区域总览](zones/index.md)，再看 [Litho 核心结论速览](litho/00-结论速览.md)（六条结论）
- **要建模型** → [约束模型](method/constraints.md) → [数据模型](method/data-model.md) → [规则库设计](method/rule-library.md)
- **要落地一个区** → [瓶颈判定](zones/bottleneck.md) → [Litho 落地路线图](litho/06-落地路线图.md) → [避坑清单](litho/08-避坑清单.md)
- **遇到具体问题** → [症状诊断手册](method/symptom-playbook.md)：[Q-time 超时](topics/qtime-corridor.md) · [机台空转](topics/support-resource.md) · [频繁改派](common/04-stability.md) · [指标打架](topics/metric-conflict.md)

其余：[跨幅专题](topics/index.md) · [约束四类](legend/constraint-types.md) · [方法论](method/index.md) · [样例](examples/index.md) · [术语表](legend/glossary.md)

---

!!! warning "关于文中所有数字"
    改善幅度来自公开文献、公开案例或理论推导，基线各不相同，只能当量级参考，不能当项目承诺值。本图志只收公开资料与个人分析，不含任何具体公司的产线数据、产能、良率与报价。
