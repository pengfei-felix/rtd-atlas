---
title: 指标体系
---

# 指标体系

<div class="keyline" markdown>

**指标体系不是展示板，是取舍协议。** RTD 上线后到底算不算成功，不能靠“感觉变顺了”，也不能靠单日漂亮曲线。必须提前写清主指标、约束指标、观测指标和基线口径。

</div>

## 三层结构

| 层 | 作用 | 典型指标 | 是否考核 |
|---|---|---|---|
| **主指标** | 项目验收的唯一核心目标 | 有效产出、CT、OTD | 是 |
| **约束指标** | 不允许恶化的红线 | Q-time、返工率、WIP、move 次数 | 是否决项 |
| **观测指标** | 诊断问题 | 利用率、白空间、setup、队列长度 | 不直接考核 |

最常见的翻车方式，是把观测指标当 KPI。比如考核利用率，现场就会倾向于把机台塞满，牺牲 setup、Q-time 和下游节奏。

## 指标口径

| 指标 | 推荐口径 | 注意 |
|---|---|---|
| Throughput | 固定窗口内 good wafer / lot | 区分 gross 与 good |
| CT | release 到 complete，按产品族统计 | 不同 mix 不可直接比 |
| X-factor | CT / raw process time | 适合跨产品归一化 |
| WIP | 区域 / route step / 全厂分层统计 | 粒度必须固定 |
| Q-time violation | 超窗批数 / 进入窗口批数 | 分严重度、分归因 |
| White space | 可生产但未生产时间 / 可用时间 | 拆无料、无资源、无人员 |
| Setup loss | setup 时间 / 可用生产时间 | 区分计划内和异常切换 |
| Move count | 单位产出或单位时间搬送次数 | 要对照 AMHS 容量 |
| Reservation hit rate | 预约资源后成功接上的比例 | 验证辅助资源前置有效性 |

没有分母、没有窗口、没有取数源的指标，不进入验收。

## 阶段化选指标

| 阶段 | 主指标 | 约束指标 |
|---|---|---|
| 新厂爬坡 | 有效产出 | Q-time、Scrap、关键资源 move |
| 产能瓶颈 | 瓶颈有效产出 | WIP、setup loss、Q-time |
| 稳定量产 | CT / X-factor | 吞吐不下降、返工率不升 |
| 多品种小批量 | OTD | Hot Lot 影响面、普通批 CT |
| 质量敏感期 | 返工率 / 稳定性 | 产出下限、CT 上限 |

同一个系统，在不同阶段应该换主指标。否则指标会逼着系统做错事。

## 基线设计

### 1. 先定义可比窗口

比较 RTD 上线前后，至少要控制：

- 产品 mix；
- 投料节奏；
- 设备可用率；
- PM / 宕机分布；
- Hot Lot 比例；
- route / recipe 版本；
- 数据采集完整性。

### 2. 推荐验证链路

<div class="flowline" markdown>

<div class="flow-step" markdown>
<strong>历史回放</strong><br>
安全可重复
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>影子模式</strong><br>
只建议不执行
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>局部接管</strong><br>
一个区/一组机
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>扩区上线</strong><br>
纳入日常监控
</div>

</div>

不要从 PPT 直接跳到全区接管。RTD 是现场系统，必须逐级放权。

## 异常日处理

异常剔除规则必须上线前写清，不能事后挑数据。

| 可剔除 | 不建议剔除 |
|---|---|
| 全厂停电、厂务异常 | 普通设备短停 |
| 数据采集中断 | 正常 PM |
| 大规模 hold/release 事故 | 正常 Hot Lot 波动 |
| route / recipe 大版本切换 | 普通 mix 波动 |

剔除清单要保留，保证结论能复算。

## 日常监控和项目验收分开

| 用途 | 看什么 | 特点 |
|---|---|---|
| 项目验收 | 相对基线改善 | 窗口长、口径固定、可复算 |
| 日常监控 | 异常漂移 | 看趋势、阈值、告警 |
| 规则复盘 | 单条规则效果 | 看命中、冲突、副作用 |
| 运维看板 | 系统健康 | 数据延迟、接口失败、override |

不要用日常看板替代验收报告。看板好看，不代表系统带来收益。

## 告警阈值

建议用三段式：

| 状态 | 含义 | 动作 |
|---|---|---|
| Green | 正常波动 | 记录 |
| Yellow | 进入风险缓冲 | 提醒、升权、预约资源 |
| Red | 触发硬风险 | 抢救、hold、人工介入 |

阈值不能只用绝对值，要结合区域、产品、窗口长度和历史波动。

## 验收写法模板

```text
在产品 mix、投料节奏、设备可用率可比的 N 周窗口内，
以 <区域/机群> 为对象，
主指标 <指标名> 较基线改善 X%，
同时约束指标 <A/B/C> 不超过基线 +Y%，
异常日按预定义规则剔除，
所有指标可由 <数据源> 复算。
```

这类写法比“提升效率 X%”更硬，也更能挡住后期争议。

---

相关：[指标冲突链](../topics/metric-conflict.md) · [规则库设计](rule-library.md) · [数据模型](data-model.md)
