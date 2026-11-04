# RTD 派工图志

半导体 Fab 实时派工（RTD / APS）的工程图志。

它不是方案商资料汇编，也不是算法名词表。它要回答的是现场问题：**某个 Lot 为什么该上这台机？为什么现在不能上？哪些约束会把派工结果推翻？**

当前内容覆盖 8"/12" 前道典型区域与跨区专题，使用 MkDocs Material 构建，可发布到 GitHub Pages。

## 项目定位

RTD 派工图志按“地图集”的方式组织内容：

| 层级 | 作用 | 已有内容 |
|---|---|---|
| 区域分幅 | 拆每个工艺区的约束结构：总览（九区速查 + 区域角色）与瓶颈判定 | Litho、Etch、Diffusion、Implant、TF、CMP、Clean/Wet、Metrology、Sorter |
| 工艺基础 | 九个区在晶圆上各做什么，为什么变成派工约束 | 九区工艺对照表 + 逐区「做什么 / 为什么是派工难点 / 待补」 |
| 通用规则 | 各区加载的基础排序规则 + 跨区仲裁；区域层只覆写参数 | G1 优先级与排序、G2 时间窗走廊、G3 资源与污染、G4 稳定性治理 |
| 跨幅专题 | 讲跨区流动与冲突 | Q-time 走廊、辅助资源流转、重入流、指标冲突、AMHS/MCS 联动 |
| 方法论 | 沉淀可复用工程闭环 | 症状诊断、约束模型、APS/投料、APS/RTS/RTD、规则库、求解器、仿真回放、系统架构、指标体系、数据模型 |
| 样例 | 给字段和日志一个可复用参照 | Lot 状态、派工日志、规则命中、Q-time 窗口、回放案例 |

核心立场：**RTD 的难点不只是排序算法，而是约束建模、辅助资源同步、规则治理和现场异常闭环。**

## 当前版本

全站按页维护版本号，只反映内容成熟度，不反映页面数量。**这张表是唯一的版本台账**（首页只保留入口，不再重复列表）：

- `1.0` — 内容完整，可直接照做：有约束结构、现场症状、可验证字段和指标；
- `1.x` — 在原结构上补案例、修错误、补来源，结构没动；
- `2.0` — 结构性重写或扩成多页专题，老读者需要重看一遍。

只改错别字和排版不升版本。

| 图幅 | 版本 | 说明 |
|---|---|---|
| Litho 光刻 | 1.0 | 8 页完整拆解：八重约束、九类问题、技术谱系、指标、路线图 |
| Etch 刻蚀 | 1.0 | chamber 级派工、PM/clean/seasoning、Q-time 后窗 |
| Diffusion 扩散 | 1.0 | 组炉策略、温区切换、Boat / Quartzware、前后 Q-time |
| Q-time 走廊 | 1.0 | 入口限流、黄色预警、下游预约、违规归因 |
| 辅助资源流转 | 1.0 | 资源类型、位置、寿命、锁定、APS/RTD/AMHS/MES 边界 |
| 重入流路径 | 1.0 | 回流预测、层间配平、负荷换算 |
| 指标冲突链 | 1.0 | 指标口径、基线实验、验收写法 |
| AMHS/MCS 联动 | 1.0 | ETA、port、stocker、pre-position、双资源同步 |
| Implant 注入 | 1.0 | setup family、污染隔离、束流整定、注入后 Q-time |
| CMP 研磨 | 1.0 | Pad 退化、R2R 反馈、post-CMP 接续 |
| TF 薄膜 | 0.1 | Chamber dedication、靶材分段、膜厚与应力反馈、返工回路 |
| Clean / Wet 清洗 | 0.1 | Bath 退化、批式合流、Q-time 走廊起点、出口限流 |
| Metrology 量测 | 0.1 | 采样规则、recipe 时长陷阱、数据紧急度、缺陷检测 |
| Sorter / Stocker | 0.1 | 载具池、污染分区、slot/port、8 寸状态可见性 |
| 通用规则层 | 0.1 | 准入标准、P0–P4 仲裁序列、四个子模块、落地节奏 |
| G1 优先级与排序 | 0.1 | Hot Lot 跨区继承、瓶颈漂移、下游可接性、老化升权 |
| G2 时间窗走廊 | 0.1 | 硬窗软窗分离、余量分配、下游预留、超时降级 |
| G3 资源与污染 | 0.1 | 四类共享模式、跨区资源池、污染矩阵、载具池 |
| G4 稳定性治理 | 0.1 | 冻结期、改派门槛、override 分型与回写、规则下线 |
| RTD/APS 方法论 | 1.0 | APS/投料、三层闭环、求解器、仿真回放、系统接口、样例日志 |
| 深化专题 | 1.0 | Diffusion、Etch、Implant、CMP、Q-time、AMHS/MCS、APS 投料、仿真回放均已拆分深度页 |

## 目录结构

```text
docs/                         唯一编辑源，日常只改这里
├── index.md                  首页：两层入口（通用规则 / 区域规则）、阅读路径
├── about.md                  关于项目与内容边界
├── zones/                    区域分幅：区域总览（九区速查、区域角色）、瓶颈判定
├── basics/                   工艺基础：九区工艺对照表与逐区说明
├── litho/                    Litho 光刻，已绘 8 页
├── etch/                     Etch 刻蚀，已绘
├── diffusion/                Diffusion 扩散，已绘
├── implant/                  Implant 离子注入，已绘
├── tf/                       TF 薄膜，骨架 0.1
├── cmp/                      CMP 研磨，已绘
├── clean/                    Clean / Wet 清洗，骨架 0.1
├── metrology/                Metrology 量测，骨架 0.1
├── sorter/                   Sorter / Stocker，骨架 0.1
├── common/                   通用规则层：G1 优先级、G2 时间窗、G3 资源污染、G4 稳定性
├── topics/                   跨幅专题：Q-time、辅助资源、重入流、指标冲突、AMHS/MCS
├── legend/                   图例层：约束四类、术语表、参考来源
├── method/                   横向方法论：症状、约束、APS、规则库、求解器、仿真、架构、指标、数据
├── examples/                 脱敏样例：Lot 状态、派工日志、规则命中、Q-time、回放
└── stylesheets/extra.css     视觉增强：卡片、徽章、流程条、时间线

mkdocs.yml                    站点配置、主题、导航
requirements.txt              Python 依赖
.github/workflows/deploy.yml  GitHub Pages 自动构建流程
```

原则：**正式内容只改 `docs/`。** 拆页前旧稿不再保留在站点工程内，避免维护两套内容。

## 写作口径

### 1. 先讲现场问题，再讲方法

每页都尽量按这个顺序写：

1. 这一区 / 这个专题的核心矛盾是什么；
2. 约束来自哪里；
3. 现场会看到什么症状；
4. RTD 能做什么，不能做什么；
5. 用哪些指标验证。

不要写成“概念解释大全”。读者要能拿去判断方案、追问厂商、改规则。

### 2. 少写空话，多写可验证字段

优先写：

- 约束粒度：tool / chamber / lot / wafer / batch / resource；
- 数据字段：位置、状态、寿命、锁定、释放时间、窗口余量；
- 派工动作：过滤、升权、预约、锁定、释放、回放；
- 验证指标：idle time、violation、setup loss、load balance、reservation hit rate。

少写：

- “智能化”“先进算法”“提升效率”这类没有口径的话；
- 不带场景的宣传句；
- 没有分母和取数口径的改善百分比。

### 3. 公开边界

这个仓库只放公开文献、公开案例和个人分析。

不放：

- 具体产线产能、良率、客户信息；
- 未公开采购材料、报价、POC 细节；
- 能反推出公司经营或工艺能力的内部数据。

## 页面组件

站点用了 Material for MkDocs，常用组件如下。

## 开源规范

- License：`LICENSE`，采用 CC BY-NC-SA 4.0。
- 贡献说明：`CONTRIBUTING.md`。
- 更新记录：`CHANGELOG.md`。
- 公开来源边界：`docs/legend/sources.md`。



### 卡片网格

```html
<div class="grid cards" markdown>

-   :material-map:{ .lg .middle } __卡片标题__

    ---

    卡片正文

    [:octicons-arrow-right-24: 链接文字](target.md)

</div>
```

### 状态徽章

```html
<span class="badge badge--done">已绘</span>
<span class="badge badge--wip">初稿</span>
<span class="badge badge--todo">待填</span>
<span class="badge badge--new">新增</span>
```

### 关键结论条

```html
<div class="keyline" markdown>

**一句关键判断。** 后面接简短解释。

</div>
```

### 标签判断条

```html
<div class="rtd-strip" markdown>

<span>决策粒度</span> Lot → Chamber  
<span>核心矛盾</span> Matching vs Q-time vs Setup

</div>
```

### 轻量流程图

```html
<div class="flowline" markdown>

<div class="flow-step" markdown>
<strong>资源实体</strong><br>
Reticle / Pod / Boat
</div>

<div class="flow-arrow">→</div>

<div class="flow-step" markdown>
<strong>派工动作</strong><br>
锁定 / 搬送 / 释放
</div>

</div>
```

这些组件只靠 `docs/stylesheets/extra.css`，不引入额外前端框架。

## 本地使用

### 首次安装

```bash
python -m venv .venv
./.venv/Scripts/python.exe -m pip install -i https://pypi.org/simple -r requirements.txt
```

### 本地预览

```bash
./.venv/Scripts/python.exe -m mkdocs serve
```

浏览器打开：

```text
http://127.0.0.1:8000/rtd-atlas/
```

路径带 `/rtd-atlas/`，因为站点按 GitHub Project Pages 方式配置。

### 严格构建检查

```bash
./.venv/Scripts/python.exe -m mkdocs build --strict
```

本地改完先跑这一条。坏链、导航错误、构建问题会直接暴露，省得推上去以后 Actions 失败。

## 发布到 GitHub Pages

1. 仓库名建议用 `rtd-atlas`。
2. 仓库可见性建议 Public。私有仓库发布 Pages 可能需要 Pro。
3. 修改 `mkdocs.yml` 里的 `site_url`：

   ```yaml
   site_url: https://<你的GitHub用户名>.github.io/rtd-atlas/
   ```

4. 推送到 main：

   ```bash
   git add .
   git commit -m "update: RTD atlas content"
   git push
   ```

5. GitHub 仓库 Settings → Pages → Source 选择 **GitHub Actions**。

之后每次 push 到 main，Actions 会自动构建并发布。

## 维护约定

- **新增页面**：先建 `docs/<section>/<page>.md`，再到 `mkdocs.yml` 的 `nav` 增加导航。
- **新增区域**：建 `docs/<zone>/index.md`，并同步更新 `docs/zones/index.md`（九区速查表 + 区域角色卡）、`docs/basics/index.md`（对照表 + 该区一节）、`docs/zones/bottleneck.md`（瓶颈触发条件表）、README「当前版本」表与 `mkdocs.yml` 导航、首页区域表。少一处就会断层。
- **新增通用规则**：先判断是否满足通用层准入标准——① 每个区都要用且逻辑相同（共性基础）、② 作用对象跨两个区以上、③ 必须全厂唯一、④ 需跨区数据才能算。任一条满足就放 `docs/common/`，否则留在区域层。
- **两层界线**：区域层只许**覆写参数**（阈值、权重、时间常数），不许改判断逻辑；要改逻辑就回通用层升版本，对九个区一起生效。在区里复制一份改逻辑 = 双轨的起点。
- **版本变更**：页面内容有实质更新时升版本号——补案例、修错、补来源升 `1.x`，结构性重写升 `2.0`；同步改 README「当前版本」表与相关页徽章，并在 `CHANGELOG.md` 记一条。只改错别字和排版不升版本。
- **中文搜索**：依赖 `jieba` 分词，已在 `requirements.txt` 中配置。
- **链接检查**：任何提交前跑 `mkdocs build --strict`。
- **外部资料**：只用公开资料，涉及商业方案时尽量抽象成工程问题，不写广告口径。

## 后续路线

建议优先级：

1. 给关键页面补公开来源脚注，尤其是投料控制、仿真、求解器和 AMHS/MCS 相关论文。
2. 继续把 Litho 的深度体例拆给其他区域，为每区扩展更多“问题 → 根因 → 解法 → 指标”案例。
3. 基于 `docs/examples/` 增加可下载的 CSV / JSON 样例，用于演示回放和规则命中分析。
4. 给症状诊断手册增加更多区域化案例，形成“症状 → 约束 → 字段 → 指标”的案例库。
5. 长期维护版本记录和贡献规范，保证开源协作质量。
