# RTD 派工图志

半导体 Fab 实时派工（RTD / APS）的区域问题档案，MkDocs Material 构建，GitHub Pages 托管。

## 目录结构

```
docs/                     唯一编辑源，改这里
├── index.md              首页（门户页，卡片式）
├── about.md              关于 / 内容边界
├── overview/             总图层：全厂派工全景、瓶颈判定
├── zones/index.md        区域横向对比
├── litho/                Litho 光刻（已完成，10 页）
├── etch/ diffusion/ implant/ cmp/    其他区域（骨架）
├── topics/               跨幅专题：Q-time 走廊、辅助资源、重入流、指标冲突
├── legend/               图例层：约束四类、术语表
├── method/               横向方法论
├── vendor/               厂商与选型
└── stylesheets/extra.css 视觉增强（卡片、徽章、表格）

litho/Litho区派工问题与解决方案深度分析.md    拆页前的原稿，只作存档，不再编辑
mkdocs.yml                站点配置 + 导航
requirements.txt          Python 依赖
.github/workflows/deploy.yml   推送自动构建发布
```

**只改 `docs/`。** 根目录的 `litho/*.md` 是 2026-10-23 那版原稿，拆页后内容已全部搬进 `docs/litho/`。

## 页面写作约定

站点用了 Material 的卡片网格与自定义徽章，写新页时可以直接用：

```html
<div class="grid cards" markdown>

-   :material-map:{ .lg .middle } __卡片标题__

    ---

    卡片正文

    [:octicons-arrow-right-24: 链接文字](target.md)

</div>
```

状态徽章：`<span class="badge badge--done">已绘</span>`，另有 `badge--wip`（骨架/初稿）、`badge--todo`（待填）、`badge--new`（标记）。

强调条：`<div class="keyline" markdown>...</div>`，左侧带主题色竖线。

## 本地预览

```bash
.venv/Scripts/python.exe -m mkdocs serve
```

打开 http://127.0.0.1:8000/rtd-atlas/ （路径带 `/rtd-atlas/`，因为站点是 GitHub 项目页，挂在子路径下）

首次在新机器上：

```bash
python -m venv .venv
./.venv/Scripts/python.exe -m pip install -i https://pypi.org/simple -r requirements.txt
```

## 发布到 GitHub Pages

1. **建仓库**：名字用 `rtd-atlas`，可见性选 **Public**（私有仓库发布 Pages 需要 Pro）。
2. **改 site_url**：把 `mkdocs.yml` 里的 `site_url` 换成你的实际地址
   `https://<你的GitHub用户名>.github.io/rtd-atlas/`
3. **推送**：

   ```bash
   git init
   git add .
   git commit -m "init: RTD 派工图志"
   git branch -M main
   git remote add origin https://github.com/<你的用户名>/rtd-atlas.git
   git push -u origin main
   ```

4. **开 Pages**：仓库 Settings → Pages → Source 选 **GitHub Actions**（不是 Deploy from a branch）。
5. 推完等 Actions 跑完，访问 `https://<用户名>.github.io/rtd-atlas/`。

之后每次 `git push` 到 main 自动重新发布。

## 几个约定

- **新加一个区域**：建 `docs/<zone>/index.md`，然后在 `mkdocs.yml` 的 `nav` 里加一行。
- **中文搜索**：靠 `jieba` 分词（已加进 requirements）。缺了它中文会整段当一个词，等于搜不到。
- **少用右上角「编辑此页」**：没配 `repo_url`，要加的话在 `mkdocs.yml` 里写 `repo_url` 和 `edit_uri`。
- 构建用了 `--strict`，**坏链接会让 Actions 失败**。本地先 `mkdocs build --strict` 验一遍更省时间。

## 内容边界

公开站，只放公开文献与个人分析。产线数据、产能、良率、客户与供应商报价一律不进仓库。
