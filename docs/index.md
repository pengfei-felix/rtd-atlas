---
title: 首页
---

<div class="hero" markdown>

# RTD 派工图志

一层工艺事实，两层派工规则。九个区各跑什么、规则怎么分层、决策怎么回放。

半导体 Fab 实时派工（RTD / APS）· 8"/12" 前道

</div>

## 这张图志怎么组织

从"晶圆上发生了什么"开始，到"每个区多管什么"，再到"这次派工为什么这么排"——四段，一段比一段贴近现场。

<div class="flowline" markdown>

<div class="flow-step" markdown>
<strong>工艺基础</strong><br>
九个区在晶圆上做什么
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>通用规则</strong><br>
九个区都加载的基础规则
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>区域分幅</strong><br>
每个区多管什么
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>派工决策</strong><br>
带理由 · 可回放
</div>

</div>

| 层 | 是什么 | 规模 |
|---|---|---|
| **工艺基础** | 九个区在晶圆上做了什么，为什么这些动作会变成派工约束 | 9 节 |
| **通用规则** | 九个区都加载的基础规则，区域层只改参数、不改逻辑（6 个功能域） | 57 项 |
| **区域分幅** | 九个区各自多管什么：流传关系、瓶颈判定、区内工序流 | 9 区 / 53 项 |
| **派工决策** | 带理由、可回放的排程结果——专题、方法论与样例都服务这一端 | — |

## 从哪里进去

[区域总览](zones/index.md){ .md-button }
[通用规则](common/index.md){ .md-button }
[工艺基础](basics/index.md){ .md-button }
[瓶颈判定](zones/bottleneck.md){ .md-button }

区域分幅覆盖：Litho · Etch · Diffusion · Implant · TF · CMP · Clean / Wet · Metrology · Sorter / Stocker

## 三条阅读路径

- **第一次来** → [区域总览](zones/index.md) 看九个区怎么流传，再挑一个区进它的派工档案
- **要建模型** → [约束模型](method/constraints.md) → [数据模型](method/data-model.md) → [规则库设计](method/rule-library.md)
- **遇到具体问题** → [症状诊断手册](method/symptom-playbook.md)：[Q-time 超时](topics/qtime-corridor.md) · [机台空转](topics/support-resource.md) · [频繁改派](common/index.md#manual) · [指标打架](topics/metric-conflict.md)

其余：[跨幅专题](topics/index.md) · [图例](legend/constraint-types.md) · [方法论](method/index.md) · [样例](examples/index.md) · [关于](about.md)

---

!!! warning "关于文中所有数字"
    改善幅度来自公开文献、公开案例或理论推导，基线各不相同，只能当量级参考，不能当项目承诺值。本图志只收公开资料与个人分析，不含任何具体公司的产线数据、产能、良率与报价。
