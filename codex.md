## 项目概述

本仓库是一个 **Halo 博客主题（theme-Stack2）二次开发仓库**，用于长期维护和定制化开发。

### 当前开发模式
- 使用 **GitHub 管理源码**
- 日常开发在 `dev` 分支进行
- 通过前端构建流程生成 `dist` 资源
- 将完整主题部署到服务器供 Halo 使用

---

## 运行与部署环境说明

### 本地开发环境
- 开发机器：本地电脑
- 分支：`dev`
- `dist/` 目录 **被 `.gitignore` 忽略**
- `dist` 为 **构建产物，不提交 GitHub**

### 服务器环境
- Halo 使用 Docker 运行
- 主题目录挂载至：
/opt/halo/halo/themes/theme-Stack2

- 服务器上的主题目录 **必须包含 `dist` 构建结果**

---

## 主题目录结构说明（开发视角）

theme-Stack2
├── theme.yaml # 主题元信息
├── settings.yaml # 主题配置项（Halo 后台可配置）
├── annotation-setting.yaml # 注释/辅助配置
├── i18n/ # 多语言文案
│ └── zh.properties # 中文文案（常改）
├── templates/ # 页面模板（核心）
│ ├── index.html # 首页
│ ├── post.html # 文章页
│ ├── page.html # 自定义页面
│ ├── archives.html # 归档页
│ ├── categories.html # 分类页
│ ├── tags.html # 标签页
│ ├── links.html # 友链页
│ ├── photos.html # 相册页
│ ├── moments.html # 动态页
│ ├── error/404.html # 404 页面
│ ├── modules/ # 模块化模板（重点）
│ │ ├── layout.html # 页面整体结构
│ │ ├── head.html # <head>（CSS / 字体 / meta）
│ │ ├── tail.html # 页面底部（JS）
│ │ ├── common/ # 通用模块
│ │ │ ├── footer.html
│ │ │ ├── left-sidebar.html
│ │ │ ├── right-sidebar.html
│ │ │ ├── pagination.html
│ │ │ └── comment.html
│ │ └── widgets/
│ │ └── right-sidebar-widgets.html
│ └── assets/
│ ├── dist/ # 构建产物（CSS / JS，GitHub 中不存在）
│ │ ├── main.css
│ │ └── main.iife.js
│ ├── images/ # 图片资源
│ └── lib/ # 第三方库（waline / view-image 等）
└── README.md


---

## dist 目录说明（非常重要）

### dist 的定位
- `templates/assets/dist/` 是 **前端构建产物**
- 已在 `.gitignore` 中忽略
- **不提交 GitHub**
- 但 **部署到服务器时必须存在**

### 当前模板依赖
以下文件 **明确依赖 dist**：
- `templates/modules/head.html`
- `templates/modules/tail.html`

因此：

> ❗ 本主题 **不能在没有 dist 的情况下直接运行**

---

## 修改原则（开发规范）

### ✅ 推荐做法（标准流程）
1. 修改源码（模板 / 样式源文件 / JS 源文件）
2. 执行构建命令生成 `dist`
3. 将包含 `dist` 的完整主题目录部署到服务器

### ❌ 不推荐做法
- 手动修改 `templates/assets/dist/main.css`
- 手动修改 `templates/assets/dist/main.iife.js`
- 在服务器上直接编辑 dist 文件并期望长期维护

---

## 覆盖层策略（可选 / 辅助）

在以下场景下可使用覆盖层：
- 小范围样式微调
- 临时修复
- 不方便立即构建时

### 覆盖层结构
templates/assets/custom/
├── custom.css
└── custom.js

### 引入位置
- `custom.css` → `templates/modules/head.html`
- `custom.js` → `templates/modules/tail.html`

⚠️ 注意：
- 覆盖层 **不能替代 dist**
- 覆盖层只是对构建产物的补充

---

## 不同需求对应修改位置速查表

| 修改目标 | 修改位置 |
|--------|---------|
| 页面整体结构 | `templates/modules/layout.html` |
| 首页文章列表 | `templates/index.html` / `modules/index-post-list.html` |
| 文章页结构 | `templates/post.html` |
| 左右侧边栏 | `modules/common/left-sidebar.html` / `right-sidebar.html` |
| 右侧挂件 | `modules/widgets/right-sidebar-widgets.html` |
| 页脚 | `modules/common/footer.html` |
| meta / 字体 / CSS 引入 | `modules/head.html` |
| JS / 统计 / 初始化 | `modules/tail.html` |
| 文案文字 | `i18n/zh.properties` |
| 404 页面 | `templates/error/404.html` |

---

## 分支与开发流程

### 分支策略
- `main`：稳定可部署版本
- `dev`：日常开发分支

### 推荐工作流
1. 在 `dev` 分支开发
2. 小步提交，保证每次提交可回滚
3. 构建生成 `dist`
4. 验证主题可正常渲染
5. 合并到 `main`
6. 部署到服务器

---

## 部署流程（参考）

### 方式一：直接同步（推荐）
```bash
# 本地构建
npm install
npm run build

# 同步到服务器
rsync -av --delete theme-Stack2/ user@server:/opt/halo/halo/themes/theme-Stack2/
方式二：打包发布
bash
复制代码
tar czf theme-Stack2-release.tar.gz theme-Stack2/
服务器解压后直接使用。