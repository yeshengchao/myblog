+++
date = '2026-04-15T17:29:05+08:00'
draft = false
title = '胜超日常：Hugo + GitHub + Cloudflare Pages 建博客流程'
tags = ["hugo", "github", "cloudflare"]
+++


**Hugo + GitHub + Cloudflare Pages 建博客流程**

## 整体架构

```
本地写文章 → push 到 GitHub → Cloudflare Pages 自动构建 → 绑定域名访问
```

---

## 第一步：本地搭建 Hugo

```bash
# 安装 Hugo（Mac 示例）
brew install hugo

# 创建新站点
hugo new site my-blog
cd my-blog

# 初始化 git
git init

# 安装主题（以 PaperMod 为例）
git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod

# 在 hugo.toml 中配置主题
echo 'theme = "PaperMod"' >> hugo.toml
```

本地预览：
```bash
hugo server -D
# 访问 http://localhost:1313
```

---

## 第二步：推送到 GitHub

```bash
# 在 GitHub 创建新仓库，比如 my-blog

git remote add origin https://github.com/你的用户名/my-blog.git
git add .
git commit -m "init blog"
git push -u origin main
```

---

## 第三步：Cloudflare Pages 部署

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com) → **Workers & Pages** → **Create** → **Pages**
2. 连接 GitHub，选择你的 `my-blog` 仓库
3. 构建配置填写：

   | 项目 | 值 |
   |------|-----|
   | 框架预设 | Hugo |
   | 构建命令 | `hugo` |
   | 输出目录 | `public` |
   | 环境变量 | `HUGO_VERSION` = `0.147.0`（填你的版本） |

4. 点击 **Save and Deploy**，等待构建完成，会得到一个 `xxx.pages.dev` 的地址

---

## 第四步：绑定自定义域名

1. 先确保 `shengchao.vip` 的 DNS 已经托管在 Cloudflare（在 Cloudflare 添加该域名）
2. 进入 Pages 项目 → **Custom domains** → **Set up a custom domain**
3. 输入 `shengchao.vip`，Cloudflare 会自动添加 DNS 记录
4. 等待几分钟生效，HTTPS 证书也会自动签发 ✅

---

## 日常写作流程

```bash
# 新建文章
hugo new posts/my-first-post.md

# 编辑内容后推送
git add .
git commit -m "新文章：xxx"
git push
# → Cloudflare 自动触发重新构建，几分钟后上线
```

---

## 关键点提示

- **Hugo 版本**：本地版本和 Cloudflare 环境变量里的版本要一致，否则构建可能报错
- **主题用 submodule**：用 `git submodule add` 方式添加主题，Cloudflare 才能正确拉取
- **草稿**：文章头部 `draft: true` 时本地可预览但不会发布，改为 `false` 再 push

整个流程完全免费，有问题随时问！