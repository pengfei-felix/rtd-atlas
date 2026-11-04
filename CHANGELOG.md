# 更新记录

## 2026-11-04

### 内容补齐

- 补齐 `CMP` 区域页：Pad 退化、conditioner、slurry、Run-to-Run 反馈、post-CMP 接续。
- 补齐 `Implant` 区域页：setup family、污染隔离、束流整定、注入后 Q-time。
- 重写 `重入流路径` 专题：回流预测、层间配平、负荷换算、资源前置。
- 重写 `指标冲突链` 专题：Little's Law、指标口径、基线实验、验收写法。
- 补齐 `Etch` 区域页：chamber 粒度、PM/clean/seasoning、Q-time 后窗。
- 补齐 `Diffusion` 区域页：组炉策略、温区切换、Boat/Quartzware、前后 Q-time。
- 补齐 `Q-time 走廊` 专题：入口限流、黄色预警、下游预约、违规归因。
- 补齐 `辅助资源流转` 专题：资源类型、位置、寿命、锁定、系统边界。

### 方法论增强

- 重写 `约束模型`：四类资源、区域映射、资源字段、权重标定。
- 重写 `规则库设计`：规则字段、冲突类型、生命周期、上线/下线机制。
- 重写 `RTS 与 RTD 分层`：覆盖原因码、偏离阈值、覆盖日志、接口边界。
- 重写 `指标体系`：三层指标、口径、基线、异常日处理、验收模板。
- 新增 `数据模型`：Lot、Tool/Chamber、辅助资源、Q-time、Rule、Decision、Event。

### 工程规范

- 重写 `README.md`，明确项目定位、目录结构、写作口径、页面组件、发布流程和后续路线。
- 新增 `CONTRIBUTING.md`。
- 新增 `LICENSE`，采用 CC BY-NC-SA 4.0。
- 新增 `CHANGELOG.md`。
- 新增视觉组件：`rtd-strip`、`flowline`、`timeline`、`code-title`。
- 删除厂商分析预留页面与 Litho 厂商横评 / 选型 Checklist 页面，项目定位收敛为 RTD 排程知识库。
- 删除根目录旧版 Litho 原稿归档，避免维护两套内容。
- 新增 `docs/method/symptom-playbook.md`，作为从现场症状反推约束、字段、规则和指标的诊断入口。

### RTD/APS 工程方法论 2.0

- 新增 `APS 与投料控制`：WIP cap、CONWIP/CONLOAD、瓶颈 buffer、Factory Physics 底线。
- 新增 `APS / RTS / RTD 闭环`：三层时间尺度、RTD 覆盖原因码、滚动节奏和偏差回灌。
- 新增 `仿真与历史回放`：事件流、回放引擎、影子模式、上线门槛和失真风险。
- 新增 `求解器与算法选型`：规则、SRB、CP、MILP、CP-SAT、rolling horizon、fallback。
- 新增 `系统架构与接口`：MES/EAP/AMHS/YMS/APS 状态源、状态冲突、写回与回滚。
- 新增 `AMHS/MCS 联动` 专题：FOUP ETA、port/stocker、pre-position、双资源同步与搬送优先级。
- 新增 `docs/examples/`：Lot 状态、派工决策日志、规则命中日志、Q-time 窗口、历史回放样例。
- 深化 Etch / Diffusion / Implant / CMP 区域页，增加典型问题深度案例库。

### Litho 同级深化专题

- 新增 Diffusion 深度页：组炉与 SRB、Q-time 与温区 Campaign、Boat 与出炉波峰、避坑清单。
- 新增 Etch 深度页：Chamber 级建模、PM/Clean/Seasoning、Recipe Family 与污染序列、Rework 与 Q-time 后窗、避坑清单。
- 新增 Implant 深度页：Setup Family 与换线矩阵、污染隔离与高能力机保护、注入后窗口与避坑。
- 新增 CMP 深度页：Pad 状态与 R2R 反馈、Post-CMP 与污染隔离。
- 新增 Q-time 深度页：窗口建模、入口限流与下游预约、违规归因与回放。
- 新增 AMHS/MCS 深度页：ETA 与 pre-position、Port 与 Stocker、搬送优先级冲突。
- 新增 APS 投料控制深度页：Little/Critical WIP、CONWIP/CONLOAD/LCONWIP、Release 与 RTD 联动。
- 新增仿真回放深度页：事件流模型、Shadow Mode、规则 A/B 与上线门槛。
