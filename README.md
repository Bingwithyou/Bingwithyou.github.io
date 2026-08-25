# Bingwithyou 个人博客

基于 [Jekyll](https://jekyllrb.com/) 与 [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 主题构建，托管在 GitHub Pages 上。

## 目录结构

- `_posts/`：博客文章，文件名格式为 `YYYY-MM-DD-title.md`
- `_tabs/`：侧栏页面（关于、归档、分类、标签）
- `_data/`：联系与分享配置
- `_config.yml`：站点主配置
- `assets/img/`：站点图标与社交封面

## 本地预览

推荐使用 Dev Container（需安装 Docker 与 VS Code 的 Dev Containers 扩展），在容器内运行：

```bash
bundle exec jekyll serve
```

然后在浏览器访问 <http://127.0.0.1:4000>。

也可以在本机安装 Ruby 与 Jekyll 后直接运行上述命令。

## 部署

站点通过 GitHub Actions 自动构建并部署到 GitHub Pages。推送 `main` 分支后，`.github/workflows/pages-deploy.yml` 会执行构建与发布。

在仓库 Settings → Pages 中，Source 需要选择 **GitHub Actions**。

## 写文章

在 `_posts/` 下新建 `YYYY-MM-DD-title.md`，Front Matter 至少包含：

```yaml
---
title: 文章标题
date: 2026-08-25 12:00:00 +0800
categories: [分类]
tags: [标签]
---
```

更多用法见 [Chirpy 文档](https://chirpy.cotes.page/)。
