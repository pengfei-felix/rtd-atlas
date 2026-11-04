---
title: 规则库设计
---

# 规则库设计

<div class="keyline" markdown>

**RTD 的规则库一定会膨胀。** 这不是管理不善，而是 Fab 现场的结构性结果：产品变多、异常变多、工程限制变细，每一次例外都会长出一条新规则。专业能力不在“规则少”，而在“规则可控”。

</div>

## 先给结论

国际一线 RTD 的差距不主要在算法，而在规则 Know-How：

- 什么场景该加规则；
- 规则该加在哪里；
- 加完怎么验证；
- 什么时候下线；
- 冲突后谁压谁。

如果这些靠工程师脑子记，规则库迟早会变成黑箱。

<div class="rtd-strip" markdown>

<span>核心目标</span> 规则可解释、可回放、可下线  
<span>最低要求</span> 每条规则有对象、条件、动作、指标、有效期

</div>

## 规则分层

| 层级 | 作用 | 例子 | 管理方式 |
|---|---|---|---|
| **硬约束** | 不允许违反 | 污染隔离、qualification、Q-time hard limit | 不能被权重覆盖 |
| **全局规则** | 跨区通用原则 | Hot Lot、瓶颈优先、下游不可接不放行 | 版本化发布 |
| **区域规则** | 区域特有逻辑 | Litho Reticle、Etch chamber、Diffusion 组炉 | 区域 owner 维护 |
| **产品/层规则** | 特定产品或关键层 | critical layer dedication | 工艺 owner 审核 |
| **临时规则** | 异常期间兜底 | 某设备降级、某材料短缺 | 必须有过期时间 |

最危险的是临时规则长期不下线。它们通常最缺验证，也最容易和正式规则冲突。

!!! note "「全局规则」这一层已经单独展开"
    上表里的**全局规则**，现在有独立的模块：[通用规则层](../common/index.md)。

    它拆成四个子模块——G1 优先级与排序、G2 时间窗走廊、G3 资源与污染、G4 稳定性治理，并配了一条 P0–P4 的仲裁序列。

    两页的分工：这页讲**一条规则怎么管**（字段、生命周期、上线门槛、下线机制）；通用层那页讲**规则分几层、谁管谁、冲突怎么裁**。

## 规则字段标准

| 字段 | 说明 |
|---|---|
| rule_id | 唯一编号，便于追踪 |
| name | 人能读懂的名称 |
| scope | fab / area / tool group / product / layer |
| object_type | lot / tool / chamber / resource / qtime |
| condition | 触发条件 |
| action | filter / boost / penalty / lock / reserve / alert |
| priority | 与其他规则冲突时的顺序 |
| owner | 业务负责人，不是开发人员 |
| valid_from / valid_until | 生效和过期时间 |
| reason_code | 命中后写入日志的原因码 |
| metric | 验证该规则是否有效的指标 |
| rollback | 出问题时如何回滚 |

没有 owner、metric、valid_until 的规则，不应进入正式库。

## 冲突类型

| 冲突 | 例子 | 处理 |
|---|---|---|
| 过滤冲突 | A 规则允许，B 规则禁止 | 硬约束优先，禁止优先 |
| 优先级倒挂 | Hot Lot 升权压过 Q-time 红区 | 定义风险等级，红区优先 |
| 范围重叠 | 全局规则和产品规则同时命中 | scope 更细者优先，但需留痕 |
| 时间冲突 | 临时规则过期后仍生效 | 自动失效，不允许无限期 |
| 指标冲突 | 提吞吐规则造成 move 暴涨 | 约束指标触线后降级或回滚 |

规则上线前至少做两件事：静态冲突扫描、历史回放。

## 规则生命周期

<div class="flowline" markdown>

<div class="flow-step" markdown>
<strong>提出</strong><br>
现场问题 + 证据
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>建模</strong><br>
对象 / 条件 / 动作
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>回放</strong><br>
历史事件验证
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>灰度</strong><br>
影子 / 小范围
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>复盘</strong><br>
保留 / 调整 / 下线
</div>

</div>

## 上线前检查

- [ ] 规则要解决的现场症状是否明确？
- [ ] 是否已有数据证明症状存在？
- [ ] 规则作用对象是否能被系统识别？
- [ ] 是否违反硬约束？
- [ ] 是否和现有规则冲突？
- [ ] 是否有回放结果？
- [ ] 是否有上线后观测指标？
- [ ] 是否有过期时间或复审时间？

## 下线机制

规则下线比上线更重要。没有下线机制，规则库只会越来越厚。

| 下线条件 | 动作 |
|---|---|
| 临时异常解除 | 自动过期 |
| 指标无改善 | 回滚或降权 |
| 副作用超阈值 | 立即停用 |
| 被更高层模型替代 | 标记 deprecated |
| 长期未命中 | 归档复审 |

建议每月做一次 rule audit：看命中次数、指标贡献、冲突次数、人工 override 次数。

## 规则 vs 优化

规则不是低级，优化也不是高级。两者分工不同。

| 适合规则 | 适合优化 |
|---|---|
| 硬约束、污染隔离、资格过滤 | 多目标排序、组批、campaign |
| 现场解释要求强 | 候选组合多，人工难判断 |
| 异常兜底 | 稳态重复场景 |
| 临时策略 | 可回放可评估的问题 |

可行路线是：**规则做边界，优化做排序，日志做解释。**

## 日志要求

每次派工至少记录：

| 字段 | 用途 |
|---|---|
| candidate_set | 当时有哪些候选 Lot / resource |
| filtered_by | 哪些规则过滤掉了候选 |
| boosted_by / penalized_by | 哪些规则改变了分数 |
| selected_reason | 最终选择原因 |
| override_reason | 人工覆盖原因 |
| outcome | 实际执行结果、等待、超时、返工 |

没有日志，规则库无法学习，只能靠人吵。

---

相关：[数据模型](data-model.md) · [指标体系](metrics.md) · [RTS 与 RTD 分层](rts-rtd.md)
