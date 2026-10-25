# Overlay —— TUI 组件层覆盖包

> 本目录**不是** Atom 主题包。它是直接替换站点组件层文件的覆盖包，让站点获得完整的 TUI 终端版式。
>
> **前提**：必须先完成主 [README 的路径 A](../README.md#路径-a只要配色主题包零配置)（把 `atom-geek/` 装进 `src/themes/`）。overlay 只提供版式，配色仍由主题包提供。

---

## 为什么需要 overlay

Atom 的主题契约规定主题目录只能放 CSS token，因此**主题包在结构上无法改变版式**：

| 想要的视觉效果 | token 能表达吗 | 需要什么 |
|---|---|---|
| 磷光绿 / 磷光纸配色 | ✅ | 主题包 |
| 零圆角 | ❌ | 组件层（`border-radius` 是选择器级决策） |
| 等宽字体只管数据 | ❌ | 组件层（`font-family` 角色重分配） |
| 终端窗口外壳 / 两行 Header | ❌ | 组件层（HTML 结构） |
| `ls -la` 列表 / `cat` 文章页 / 行号槽 | ❌ | 组件层 |
| `grep` 搜索命中 / command not found 404 | ❌ | 组件层 |
| 网格底纹 / 刻度尺配色 / 辉光 | ✅ | 主题包 |

换句话说：**overlay 提供"版式"，主题包提供"配色"**。两者叠加才是完整 TUI。

---

## 文件对照表

把下表「上游文件」一列的路径理解为**相对于你的 Atom 站点根目录**。

### 目录结构

```
overlay/
├── components/     → src/components/       （9 个 Astro 组件）
├── layouts/        → src/layouts/          （2 个 Astro 布局）
├── pages/          → src/pages/            （10 个页面）
├── styles/         → src/styles/           （global.css + _theme-geek.css + 21 个 CSS 模块）
└── scripts/        → src/scripts/          （app.js）
```

### 逐文件清单

#### `components/` → `src/components/`

| 文件 | 动作 | 说明 |
|---|---|---|
| `CommandHeader.astro` | **新增** | 两行 Header 的 status bar（站名 / 路径 / 状态指示点） |
| `TerminalBoot.astro` | **新增** | 首页 Hero 的终端启动序列输出 |
| `TerminalAscii.astro` | **新增** | ASCII 装饰图形 |
| `GrepHit.astro` | **新增** | `grep` 式搜索命中标记组件 |
| `IndexSummary.astro` | **新增** | 首页词频 / 统计摘要面板 |
| `Header.astro` | **覆盖** | 改为终端标题栏形态，接入 CommandHeader |
| `Footer.astro` | **覆盖** | 改为状态栏形态 |
| `PostCard.astro` | **覆盖** | 列表项改为 `ls -la` 式排布 |
| `TableOfContents.astro` | **覆盖** | 改为命令树 / 目录树形态 |

#### `layouts/` → `src/layouts/`

| 文件 | 动作 | 说明 |
|---|---|---|
| `BaseLayout.astro` | **覆盖** | 接入 TUI 组件；`<html>` 标签带 `data-theme` / `data-mode` |
| `BlogPost.astro` | **覆盖** | 文章页改为 `cat` 输出形态，含行号槽（gutter） |

#### `pages/` → `src/pages/`

| 文件 | 动作 | 说明 |
|---|---|---|
| `index.astro` | **覆盖** | 首页：终端启动 + 词频面板 + `ls -la` 列表 |
| `404.astro` | **覆盖** | command not found 形态 |
| `about.astro` | **覆盖** | 终端 `about` 面板形态 |
| `blog/index.astro` | **覆盖** | 文章列表 |
| `blog/[...page].astro` | **覆盖** | 分页列表 |
| `blog/[...slug].astro` | **覆盖** | 文章详情（`cat` 形态） |
| `tags/index.astro` | **覆盖** | 标签页 → 词频面板 |
| `tag/[...tag].astro` | **覆盖** | 标签详情列表 |
| `categories/index.astro` | **覆盖** | 分类页 → 目录树面板 |
| `category/[...category].astro` | **覆盖** | 分类详情列表 |

#### `styles/` → `src/styles/`

> ⚠️ **这一节必须整表覆盖**。上游 v1.1.0 的 CSS 里**一个 TUI class 都没有**
> （`.term-window` / `.footer-status` / `.footer-mid` / `.cmd-tree` … 全部缺失），
> 只覆盖组件不覆盖样式 = TUI 组件配旧 CSS，版式会直接塌。

**TUI 新增模块（上游没有，需要新建）**

| 文件 | 动作 | 说明 |
|---|---|---|
| `_modules/_terminal.css` | **新增** | TUI 原语：终端外壳 / 标题栏 / ASCII 装饰（280 行） |
| `_modules/_ls.css` | **新增** | `ls -la` 文章列表样式（174 行） |
| `_modules/_index.css` | **新增** | 索引面板样式（分类页 / 标签页，419 行） |

**覆盖模块（上游有同名文件，但内容是旧设计，必须整份替换）**

| 文件 | 动作 | 说明 |
|---|---|---|
| `_modules/_header.css` | **覆盖** | 终端窗口外壳形态的 Header（349 行） |
| `_modules/_footer.css` | **覆盖** | TUI 系统状态栏页脚（110 行） |
| `_modules/_hero.css` | **覆盖** | 首页终端启动序列（226 行） |
| `_modules/_post.css` | **覆盖** | 文章页 `cat` 输出形态 + 行号槽（507 行） |
| `_modules/_toc.css` | **覆盖** | 命令树目录（180 行） |
| `_modules/_search.css` | **覆盖** | 终端 `grep` 搜索（145 行） |
| `_modules/_about.css` | **覆盖** | `cat README.md` 形态的关于页（200 行） |
| `_modules/_post-extras.css` | **覆盖** | 打赏码与上下篇导航（210 行） |
| `_modules/_responsive.css` | **覆盖** | 全站响应式（含页脚窄屏规则，457 行） |
| `_modules/_variables.css` | **覆盖** | 共享 CSS 变量（104 行） |
| `_modules/_layout.css` | **覆盖** | 主布局（137 行） |
| `_modules/_pages.css` | **覆盖** | 页面外壳（107 行） |
| `_modules/_pagination.css` | **覆盖** | 分页（77 行） |
| `_modules/_sections.css` | **覆盖** | 区块标题（63 行） |
| `_modules/_tags.css` | **覆盖** | 标签与分类（67 行） |
| `_modules/_background.css` | **覆盖** | 背景（33 行） |
| `_modules/_buttons.css` | **覆盖** | 按钮（49 行） |
| `_modules/_reset.css` | **覆盖** | CSS Reset（123 行） |
| `global.css` | **覆盖** | 导入顺序需包含上面 3 个新模块 |
| `_theme-geek.css` | **覆盖** | 主题作用域增补（直角化 / 等宽角色 / 焦点环 / 降级） |

> `global.css` 的 `@import` 顺序必须与上游一致，否则层叠次序会错。覆盖后请确认下列 3 行存在且位置正确：
> ```css
> @import './_modules/_ls.css';        /* 在 _sections.css 之后 */
> @import './_modules/_terminal.css';   /* 在 _ls.css 之后 */
> @import './_modules/_index.css';      /* 在 _terminal.css 之后 */
> ```
>
> 上游的 `_modules/_progress.css` 与 `_modules/_scrollbar.css` 未变，无需覆盖。

#### `scripts/` → `src/scripts/`

| 文件 | 动作 | 说明 |
|---|---|---|
| `app.js` | **覆盖** | 增加 `grep` 命中标记、行号槽渲染等交互逻辑 |

> `app.js` 是源码，**不是**构建产物。仓库构建时会经 `scripts/minify-app-js.mjs` 从 `src/scripts/app.js` 生成 `public/js/app.js`。请覆盖源码，不要改 `public/js/app.js`。

---

## 安装步骤

```bash
# 前提：Atom 站点根目录，例 ~/blog
BLOG=~/blog
OVERLAY=/path/to/atom-geek/overlay

# 1. 先装主题包（路径 A）
cp -r /path/to/atom-geek/atom-geek "$BLOG/src/themes/"

# 2. 覆盖组件层
cp -r "$OVERLAY/components/." "$BLOG/src/components/"
cp -r "$OVERLAY/layouts/."    "$BLOG/src/layouts/"
cp -r "$OVERLAY/pages/."      "$BLOG/src/pages/"
cp -r "$OVERLAY/styles/."     "$BLOG/src/styles/"
cp -r "$OVERLAY/scripts/."    "$BLOG/src/scripts/"

# 3. 设为默认主题
#    编辑 $BLOG/src/consts.ts
#    export const DEFAULT_THEME_ID = 'atom-geek';

# 4. 构建
cd "$BLOG" && npm run dev
```

### 建议先备份

```bash
cd "$BLOG"
git checkout -b feat/tui-theme   # 或先确保工作区干净
```

---

## 与上游的差异

overlay 相对上游 Atom（`main` 分支）的差异共 **45 个文件**，分三类：

| 类型 | 数量 | 内容 |
|---|---|---|
| 新增 | 5 | `TerminalBoot.astro` `TerminalAscii.astro` `CommandHeader.astro` `GrepHit.astro` `IndexSummary.astro` + 3 个 CSS 模块 |
| 删除 | 3 | 旧的 `.hero-image` 相关样式与通用 Hero 区块 |
| 改写 | 37 | 9 个组件、2 个布局、10 个页面、样式模块与 `app.js` |

设计细节见 [`../docs/TUI-REDESIGN.md`](../docs/TUI-REDESIGN.md)（逐板块对照：原来是什么 → 现在是什么）。

### 与上游的主要行为差异

| 维度 | 上游 Atom | overlay 后 |
|---|---|---|
| 导航 | 普通文字链接 | `~/` 前缀等宽导航，激活项显示 `>` |
| 文章列表 | 卡片列表 | `ls -la` 式排布（序号 / 日期 / 标题） |
| 文章页 | 常规排版 | `cat` 输出 + 左侧行号槽 |
| 搜索结果 | 普通高亮 | `grep` 式命中标记 |
| 标签页 | 卡片网格 | 词频面板 |
| 分类页 | 卡片网格 | 目录树面板 |
| 404 | 通用错误页 | command not found |
| 代码块 | 纯代码块 | 终端标题栏 + 状态点 + 复制按钮 |
| 圆角 | `--radius*` 圆角 | 全部归零 |

---

## 回退方式

overlay 是**纯文件覆盖**，没有构建期耦合、没有插件注册、没有依赖变更，因此回退就是恢复原文件。

### 方式 1：git 回退（推荐）

```bash
cd ~/blog
git checkout -- src/components/ src/layouts/ src/pages/ src/styles/ src/scripts/
# 删除新增文件
git clean -fd src/components/ src/styles/_modules/
```

### 方式 2：只回退样式（保留版式）

若只想去掉直角化/扫描线等视觉增补，删除这一个文件即可：

```bash
rm ~/blog/src/styles/_theme-geek.css
# 并从 global.css 移除对应 @import
```

主题包仍会生效，站点退回普通版式 + 磷光配色。

### 方式 3：完全回到原站

```bash
rm -r ~/blog/src/themes/atom-geek
git checkout -- src/components/ src/layouts/ src/pages/ src/styles/ src/scripts/
```

---

## 注意事项

1. **overlay 绑定上游版本**。它是针对特定 Atom 版本的组件层快照，上游组件结构变化后可能需要手工适配。发布时的主站版本：**Atom v1.4.0 / v1.3.0** 时期。
2. **overlay 会覆盖上游的 bug 修复**。如果上游修复了某个组件的 bug，你的 overlay 会把它盖回去。长期使用建议定期对比上游。
3. **不要在 overlay 里放主题包内容**。配色改动请改 `atom-geek/light.css` 与 `dark.css`，不要塞进 overlay 的 CSS 模块——那会破坏「主题包 = token」的契约。
4. **overlay 的 CSS 预算**：CSS 全内联进每个 HTML（`inlineStylesheets: 'always'`），新增 CSS 会被全站 99+ 页每个页面承担。overlay 新增的 3 个模块合计约 873 行，请勿继续膨胀。
5. **零新依赖**：overlay 不引入任何 npm 包、不新增字体请求、不引入框架运行时。

---

## 相关文档

- [`../README.md`](../README.md) — 主文档：演示图、安装、能力边界、token 契约
- [`../docs/TUI-REDESIGN.md`](../docs/TUI-REDESIGN.md) — TUI 重做设计说明
- [`../docs/ARCHITECTURE-ATOM-GEEK.md`](../docs/ARCHITECTURE-ATOM-GEEK.md) — token 契约与架构（含单主题化勘误 CN-01）
- <https://github.com/zz3656/Atom/blob/main/docs/THEMING.md> — 上游完整主题开发指南
