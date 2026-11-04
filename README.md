# RTD 派工图志

半导体 Fab 实时派工（RTD / APS）的工程图志。

它不是方案商资料汇编，也不是算法名词表。它要回答的是现场问题：**某个 Lot 为什么该上这台机？为什么现在不能上？哪些约束会把派工结果推翻？**

当前内容覆盖 8"/12" 前道典型区域与跨区专题，使用 MkDocs Material 构建，可发布到 GitHub Pages。

## 项目定位

RTD 派工图志按“地图集”的方式组织内容：

| 层级 | 作用 | 已有内容 |
|---|---|---|
| 总图 | 看全厂派工关系和瓶颈逻辑 | 全厂派工全景、瓶颈判定 |
| 区域分幅 | 拆每个工艺区的约束结构 | Litho、Etch、Diffusion、Implant、CMP |
| 跨幅专题 | 讲跨区流动与冲突 | Q-time 走廊、辅助资源流转、重入流、指标冲突、AMHS/MCS 联动 |
| 方法论 | 沉淀可复用工程闭环 | 症状诊断、约束模型、APS/投料、APS/RTS/RTD、规则库、求解器、仿真回放、系统架构、指标体系、数据模型 |
| 样例 | 给字段和日志一个可复用参照 | Lot 状态、派工日志、规则命中、Q-time 窗口、回放案例 |

核心立场：**RTD 的难点不只是排序算法，而是约束建模、辅助资源同步、规则治理和现场异常闭环。**

## 当前状态

| 图幅 | 状态 | 说明 |
|---|---|---|
| Litho 光刻 | 已完成 | 10 页完整拆解：八重约束、九类问题、技术谱系、指标、路线图 |
| Etch 刻蚀 | 已绘 | chamber 级派工、PM/clean/seasoning、Q-time 后窗 |
| Diffusion 扩散 | 已绘 | 组炉策略、温区切换、Boat / Quartzware、前后 Q-time |
| Q-time 走廊 | 已绘 | 入口限流、黄色预警、下游预约、违规归因 |
| 辅助资源流转 | 已绘 | 资源类型、位置、寿命、锁定、APS/RTD/AMHS/MES 边界 |
| 重入流路径 | 已绘 | 回流预测、层间配平、负荷换算 |
| 指标冲突链 | 已绘 | 指标口径、基线实验、验收写法 |
| AMHS/MCS 联动 | 已绘 | ETA、port、stocker、pre-position、双资源同步 |
| Implant 注入 | 已绘 | setup family、污染隔离、束流整定、注入后 Q-time |
| CMP 研磨 | 已绘 | Pad 退化、R2R 反馈、post-CMP 接续 |
| RTD/APS 方法论 2.0 | 已绘 | APS/投料、三层闭环、求解器、仿真回放、系统接口、样例日志 |
| 深化专题 | 已绘 | Diffusion、Etch、Implant、CMP、Q-time、AMHS/MCS、APS 投料、仿真回放均已拆分深度页 |

## 目录结构

```text
docs/                         唯一编辑源，日常只改这里
├── index.md                  首页：门户页、图幅状态、阅读路径
├── about.md                  关于项目与内容边界
├── overview/                 总图层：全厂派工全景、瓶颈判定
├── zones/                    区域总览：五区横向对比
├── litho/                    Litho 光刻，已完成 10 页
├── etch/                     Etch 刻蚀，已绘
├── diffusion/                Diffusion 扩散，已绘
├── implant/                  Implant 离子注入，已绘
├── cmp/                      CMP 研磨，已绘
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
- **新增区域**：建 `docs/<zone>/index.md`，并同步更新 `docs/zones/index.md` 与首页状态表。
- **状态变更**：页面从骨架变成已绘时，同步改 `docs/index.md`、相关总览页和徽章。
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
