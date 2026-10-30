---
title: 首页
---

# RTD 派工知识库

面向 8"/12" 前道晶圆厂的实时派工（RTD / APS）问题档案。按**区域**横向铺开，每个区域套同一套结构：约束 → 问题 → 解法 → 指标 → 落地。

---

## 两条轴

**区域轴** —— 每个区的约束结构不同，派工逻辑不能通用：

| 区域 | 核心矛盾 | 状态 |
|---|---|---|
| [Litho 光刻](litho/index.md) | Reticle / Pod 双层辅助资源争抢 | 已完成 |
| [Etch 刻蚀](etch/index.md) | Chamber 级资质 + Q-time | 骨架 |
| [Diffusion 扩散](diffusion/index.md) | 批量组炉 + 超长驻留 | 骨架 |
| [Implant 离子注入](implant/index.md) | 换靶 setup 成本极高 | 骨架 |
| [CMP 研磨](cmp/index.md) | Pad 寿命 + 量测闭环 | 骨架 |

**主题轴** —— 跨区域复用的方法：

- [约束模型](method/constraints.md)：怎么把一个区的约束拆成可计算的集合
- [规则库设计](method/rule-library.md)：规则膨胀与优先级冲突的控制
- [RTS 与 RTD 分层](method/rts-rtd.md)：谁听谁的，阈值怎么设
- [指标体系](method/metrics.md)：验收用什么数，日常盯什么数

---

## 怎么读

- **第一次来** → 先看 [Litho 核心结论速览](litho/00-结论速览.md)，六条结论能建立起 RTD 派工的问题感
- **要做选型** → [厂商方案横评](vendor/index.md) + [选型 Checklist](litho/07-选型-checklist.md)
- **要落地一个区** → [Litho 落地路线图](litho/06-落地路线图.md) 的 Phase 0–4 可以直接套到其他区

!!! warning "关于数据的边界"
    文中引用的改善幅度（利用率 +1%~2%、吞吐 +2%~9.4% 等）来自公开文献与厂商案例，**基线各不相同、年份横跨 2011–2025，只能当量级参考，不能当供应商承诺值**。写进合同的应该是验证方法与对赌条款。
