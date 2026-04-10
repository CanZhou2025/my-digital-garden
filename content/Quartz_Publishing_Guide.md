# 🚀 实战手册：使用 Quartz 将 Obsidian 笔记发布为 GitHub Pages 博客

> **作者**：Claire (AI Researcher)
> **技术栈**：Obsidian + Quartz 4.0 + GitHub Actions + GitHub Pages

---

## 📋 核心流程概览

将 Obsidian 笔记转化为在线“数字花园”主要分为四个阶段：**环境准备**、**本地初始化**、**GitHub 仓库配置**、**自动化部署优化**。

---

## 第一阶段：环境准备 (Mac/Windows)

1.  **安装 Node.js**：
    *   访问 [nodejs.org](https://nodejs.org/) 下载并安装 **LTS 版本**（推荐 v20 或更高）。
    *   验证安装：在终端输入 `node -v` 和 `npm -v`。
2.  **安装 Git**：确保您的电脑已安装 Git，用于代码同步。

---

## 第二阶段：本地初始化 Quartz

1.  **克隆 Quartz 仓库**：
    ```bash
    git clone https://github.com/jackyzha0/quartz.git
    cd quartz
    ```
2.  **安装依赖与初始化**：
    ```bash
    npm install
    npx quartz create  # 按照提示选择基础配置
    ```
3.  **同步 Obsidian 笔记**：
    *   将您的 Obsidian 笔记（`.md` 文件）复制到 `quartz/content` 文件夹中。
    *   **关键点**：确保 `content/` 目录下有一个 `index.md` 作为首页。
4.  **本地预览**：
    ```bash
    npx quartz build --serve
    ```
    访问 `http://localhost:8080` 查看效果。

---

## 第三阶段：GitHub 仓库配置与关联

1.  **创建 GitHub 仓库**：
    *   在 GitHub 上新建一个 **Public** 仓库（如 `my-digital-garden`）。
    *   **不要**勾选初始化 README 或 .gitignore。
2.  **关联远程仓库**：
    ```bash
    git remote set-url origin https://github.com/您的用户名/仓库名.git
    ```
3.  **生成 Personal Access Token (PAT)**：
    *   路径：`Settings -> Developer settings -> Personal access tokens -> Tokens (classic)`。
    *   **必须勾选权限**：`repo` 和 **`workflow`**（后者用于同步部署脚本）。

---

## 第四阶段：自动化部署 (GitHub Actions)

### 1. 修复部署脚本 (deploy.yaml)
在 `.github/workflows/` 目录下创建或覆盖 `deploy.yaml`，确保使用最新的 Node 环境：

```yaml
name: Deploy Quartz site to GitHub Pages
on:
  push:
    branches:
      - v4  # 确保分支名称正确
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 24  # 推荐使用最新稳定版
      - run: npm install
      - run: npx quartz build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: public
  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/deploy-pages@v4
```

### 2. 开启 GitHub Pages 服务
*   进入仓库 `Settings -> Pages`。
*   在 **Build and deployment -> Source** 中，将选项改为 **`GitHub Actions`**。

---

## 🛠 常见报错排查 (Troubleshooting)

| 报错信息 | 原因分析 | 解决方法 |
| :--- | :--- | :--- |
| `command not found: npm` | 未安装 Node.js | 重新安装 Node.js 并重启终端。 |
| `remote origin already exists` | 默认关联了原作者仓库 | 使用 `git remote set-url origin [您的URL]`。 |
| `Password authentication failed` | GitHub 不再支持密码登录 | 使用生成的 **Personal Access Token** 作为密码。 |
| `refusing to allow PAT to update workflow` | Token 缺少 `workflow` 权限 | 在 GitHub Token 设置中勾选 `workflow` 权限并更新。 |
| `404 Not Found` | Pages 服务未开启或 Source 错误 | 在 `Settings -> Pages` 中将 Source 改为 `GitHub Actions`。 |

---

## 🔄 日常更新三部曲

每当您在 Obsidian 中写完新笔记，只需在终端执行：
```bash
git add .
git commit -m "Update research notes"
git push origin v4
```
**GitHub Actions 会自动完成构建，您的博客将在 2 分钟内自动更新。**
