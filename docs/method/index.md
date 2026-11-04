---
title: 横向方法论
---

# 横向方法论

区域档案讲「这个区有什么约束」，这里讲「约束怎么变成可执行的东西」。方法跨区复用，区域只换输入。

| 章节 | 解决什么问题 |
|---|---|
| [症状诊断手册](symptom-playbook.md) | 从机台空转、Q-time 超时、Hot Lot 雪崩等现场症状反推约束和规则 |
| [约束模型](constraints.md) | 把一个区的现场约束翻译成可计算的资源集合与目标函数 |
| [通用规则层](../common/index.md) | 哪些规则该提炼成各区共用的基础层、区域层怎么加载与覆写、两层冲突时怎么仲裁 |
| [APS 与投料控制](release-control.md) | 用 WIP cap、CONWIP/CONLOAD、瓶颈 buffer 把 RTD 放回全厂控制闭环 |
| [APS / RTS / RTD 闭环](aps-rts-rtd-loop.md) | 三层时间尺度如何分工，RTD 何时覆盖计划、如何回灌偏差 |
| [规则库设计](rule-library.md) | 规则越加越多、互相打架，怎么控制 |
| [求解器与算法选型](solver-playbook.md) | 规则、SRB、CP、MILP、CP-SAT、仿真优化分别该用在哪 |
| [仿真与历史回放](simulation-replay.md) | 历史事件回放、影子模式、局部接管和上线门槛 |
| [系统架构与接口](system-architecture.md) | MES/EAP/AMHS/YMS/APS 与 RTD 的状态源、写回和冲突处理 |
| [RTS 与 RTD 分层](rts-rtd.md) | 调度和派工谁听谁的，阈值设在哪里 |
| [指标体系](metrics.md) | 验收看什么数，日常盯什么数，怎么避免指标互相拆台 |
| [数据模型](data-model.md) | Lot、资源、Q-time、规则、派工决策日志最少要有哪些字段 |

<div class="keyline" markdown>

**读法建议：** 先看症状诊断手册定位问题，再看约束模型确定资源粒度；接着看 APS/投料控制和三层闭环，确认问题该由哪层解决；然后用数据模型、规则库、求解器、仿真回放和指标体系完成上线闭环。

</div>
