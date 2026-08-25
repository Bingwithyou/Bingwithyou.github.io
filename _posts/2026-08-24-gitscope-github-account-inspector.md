---
title: "GitScope：我做了一个免费的 GitHub 账户信息查询工具"
date: 2026-08-24 17:22:20 +0800
description: "介绍 GitScope（GitHub Account Inspector）：一个纯前端、无需登录的 GitHub 账户查询工具，可查看注册时间、数字用户 ID、公开资料与代表仓库。"
categories: [项目分享]
tags: [GitHub, 项目分享, JavaScript, GitHub Pages, Web 开发]
image:
  path: https://bingwithyou.github.io/GitHub-Account-Inspector/assets/social-preview.png
  alt: GitScope GitHub 账户信息查询工具
---

最近我做了一个小工具：[GitScope](https://bingwithyou.github.io/GitHub-Account-Inspector/)。它的仓库名称是 [GitHub Account Inspector](https://github.com/Bingwithyou/GitHub-Account-Inspector)，主要解决一个很具体的问题：**输入 GitHub 用户名，快速查看这个账户的注册时间、数字用户 ID、公开资料和代表仓库。**

整个项目使用原生 HTML、CSS 和 JavaScript 编写，直接部署在 GitHub Pages 上，不需要后端服务，也不会要求访问者登录或提供 Token。

> 在线体验：[打开 GitScope](https://bingwithyou.github.io/GitHub-Account-Inspector/)
>
> 项目源码：[Bingwithyou/GitHub-Account-Inspector](https://github.com/Bingwithyou/GitHub-Account-Inspector)

## 为什么要做 GitHub 账户查询工具

GitHub 个人主页会展示仓库、关注者和简介，但有些公开信息并不容易在页面上直接找到，例如：

- 账户具体注册于哪一天；
- 这个账户已经存在了多少天或多少年；
- 不会随用户名一起改变的数字用户 ID；
- GitHub GraphQL 使用的 Node ID；
- 一个账户有哪些值得快速了解的公开仓库。

这些数据其实都可以通过 GitHub REST API 获得。于是我把它们整理成一个简单的查询页面，希望用户不需要打开开发者工具，也不必自己发送 API 请求。

## GitScope 可以查询哪些信息

输入一个 GitHub 用户名后，GitScope 会展示以下公开信息。

![GitScope 查询 Bingwithyou 的完整结果：账户资料、注册时间、数字用户 ID、统计信息与代表仓库](/assets/img/gitscope/gitscope-result-overview.png)
_输入 `Bingwithyou` 后的完整查询结果_

### 1. GitHub 注册时间与账户年龄

页面会读取账户的 `created_at` 字段，显示完整注册日期，并计算账户从注册到现在经历的天数。用户也可以把天数切换为年数，更直观地了解一个 GitHub 账户存在了多久。

![GitScope 显示的账户注册时间与年龄：2018 年 4 月 30 日，共 3038 天，约合 8.3 年](/assets/img/gitscope/gitscope-account-age.png)
_注册时间与账户年龄_

如果你只是想查询“GitHub 账号注册时间”，这也是 GitScope 最直接的使用场景。

### 2. 数字用户 ID 与 Node ID

GitHub 用户名可以修改，但数字用户 ID 更适合持续识别同一个账户。GitScope 会同时展示：

- REST API 返回的数字用户 ID；
- GraphQL 全局对象使用的 Node ID；
- 对应的复制按钮和字段说明。

GitHub 官方文档也说明了数字 ID 可用于查询账户，而 `login` 可能随时间改变。相关字段可以在 [REST API 用户接口文档](https://docs.github.com/en/rest/users/users#get-a-user)中查看。

![GitScope 显示的数字用户 ID 38858402、Node ID 与账户统计](/assets/img/gitscope/gitscope-ids.png)
_数字用户 ID、Node ID 与账户统计_

### 3. 公开资料与账户统计

除了注册信息，页面还会整理展示：

- 头像、昵称和用户名；
- 账户类型与个人简介；
- 公司、位置和个人网站；
- 公开仓库数量；
- 关注者与正在关注的数量；
- 资料最后更新时间。

所有内容都来自 GitHub 的公开接口，不会获取私有仓库、私有邮箱或其他需要授权的数据。

### 4. 代表仓库

为了让查询结果不只是数字，GitScope 还会请求该用户最近更新的公开仓库，并从中选择最多 6 个代表仓库。

选择逻辑会优先排除 Fork；如果没有原创仓库，则回退到全部公开仓库。候选仓库会按照 Star 数量排序，Star 相同时再比较 Fork 数量。每张仓库卡片会展示名称、描述、主要语言、Star 和 Fork。

这不是一个复杂的“开发者评分”，而是帮助访问者快速了解账户公开作品的轻量摘要。

![GitScope 展示的 Bingwithyou 代表仓库列表，按 Star 数量排序](/assets/img/gitscope/gitscope-repositories.png)
_按 Star 排序的代表仓库_

## 纯前端架构：不需要服务器

GitScope 的数据流很简单：

```text
浏览器
  ├─ GET /users/{username}          获取账户公开资料
  ├─ GET /users/{username}/repos    获取公开仓库
  └─ localStorage                   保存短期缓存与最近查询
```

页面直接请求 GitHub REST API，静态文件则由 GitHub Pages 托管。这样做有几个好处：

- 没有需要维护的应用服务器和数据库；
- 部署成本低，仓库推送后即可发布；
- 查询历史不会上传到额外的服务；
- 项目结构简单，任何静态文件服务器都能运行。

我没有把 GitHub Personal Access Token 写入前端代码。浏览器中的前端代码对所有访问者可见，把 Token 放进去等同于公开凭据，并不安全。

## 缓存、请求限额与隐私

未登录调用 GitHub REST API 时，请求额度与来源 IP 关联，主要限额通常为每小时 60 次，具体规则可以查看 [GitHub REST API 限额文档](https://docs.github.com/en/rest/using-the-rest-api/rate-limits-for-the-rest-api)。

为了减少重复请求，GitScope 在浏览器中实现了：

- 15 分钟的账户查询缓存；
- 最多 12 个账户的缓存结果；
- 最多 6 条最近查询记录；
- 当前 API 请求余额提示；
- 一键清空本地缓存与查询历史。

缓存命中时，页面会明确标注结果来自本地缓存，而且不会消耗新的 API 请求。所有缓存都保存在访问者自己的浏览器中，没有额外的数据收集服务。

## 我在交互细节上做的处理

这个项目虽然不大，但我不希望它只是一个“能请求接口的输入框”。因此还补充了一些实际使用时容易忽略的细节：

- 自动去掉用户名开头的 `@`；
- 在请求前验证 GitHub 用户名格式；
- 新查询开始时取消尚未完成的旧请求；
- 区分账户不存在、请求额度耗尽和网络错误；
- 仓库请求失败时仍然保留账户基础资料；
- 支持复制数字 ID、Node ID 和账户摘要；
- 将查询用户名写入 URL，方便复制链接分享；
- 提供键盘跳转、ARIA 状态提示与清晰的焦点反馈。

例如，可以直接通过下面的地址查看我的公开账户信息：

[使用 GitScope 查询 Bingwithyou](https://bingwithyou.github.io/GitHub-Account-Inspector/?user=Bingwithyou)

## 自动化测试与 GitHub Pages 部署

项目使用 Playwright 编写浏览器端测试。测试会拦截 GitHub API，并返回固定数据，因此不会消耗真实 API 请求额度，也不会因为外部数据变化而产生不稳定结果。

目前测试覆盖了这些关键流程：

- 查询账户并展示公开资料与代表仓库；
- 重复查询使用本地缓存；
- 复制账户摘要后的反馈；
- 不存在的账户显示明确错误；
- 键盘用户跳转到主要内容；
- SEO、社交分享元数据与自定义 404 页面。

GitHub Actions 会先安装 Chromium 并运行测试。只有测试通过后，`main` 分支才会部署到 GitHub Pages；Pull Request 则只运行测试，不执行正式部署。

## 项目本身也做了 SEO

除了功能页面，我还为 GitScope 补充了完整的搜索与分享信息：

- 唯一且清晰的页面标题与 Meta Description；
- Canonical URL；
- Open Graph 与 Twitter Card；
- `WebApplication` 类型的 JSON-LD 结构化数据；
- `robots`、`sitemap.xml` 和社交分享封面；
- 与主站视觉一致的 404 页面。

这些内容不会替代真正有价值的页面正文，但可以帮助搜索引擎正确理解页面，也能让链接分享到社交平台时获得更完整的预览。

## 如何在本地运行

项目没有构建步骤。克隆仓库后，在项目目录启动任意静态文件服务器即可：

```bash
git clone https://github.com/Bingwithyou/GitHub-Account-Inspector.git
cd GitHub-Account-Inspector
npx serve .
```

如需运行自动化测试：

```bash
npm install
npx playwright install chromium
npm test
```

不建议直接双击 `index.html`，因为部分浏览器会限制 `file://` 页面发起网络请求。

## 常见问题

### 如何查询 GitHub 账号的注册时间？

打开 [GitScope](https://bingwithyou.github.io/GitHub-Account-Inspector/)，输入 GitHub 用户名并点击“查询账户”。结果会显示注册日期、注册总天数，以及换算后的年数。

### GitHub 用户 ID 和用户名有什么区别？

用户名是公开登录名，可以被修改；数字用户 ID 是 GitHub 为账户分配的数字标识，更适合用于持续关联同一个账户。Node ID 则是 GraphQL 使用的全局对象标识。

### 使用 GitScope 需要 GitHub Token 吗？

不需要。GitScope 只读取公开信息，并使用 GitHub 的匿名 REST API 请求额度。它不会要求、保存或上传 Personal Access Token。

### 查询记录会上传到服务器吗？

不会。最近查询和 15 分钟缓存只存储在当前浏览器的 `localStorage` 中，用户可以随时点击“清空”。

### 为什么有时无法查询？

常见原因包括用户名不存在、网络暂时无法访问 GitHub，或者当前 IP 的匿名 API 请求额度已经耗尽。GitScope 会根据响应状态显示对应的错误信息和预计恢复时间。

## 写在最后

GitScope 是一个范围很小但相对完整的项目：它从一个明确的问题出发，包含公开数据查询、缓存、错误处理、可访问性、自动化测试、SEO 和持续部署。

如果你正好需要查询 GitHub 注册时间、GitHub 用户 ID，或者想快速了解一个公开账户，可以直接试试：

- [在线使用 GitScope](https://bingwithyou.github.io/GitHub-Account-Inspector/)
- [查看 GitHub 源码](https://github.com/Bingwithyou/GitHub-Account-Inspector)

如果你发现问题或有功能建议，也欢迎通过仓库的 Issue 提交反馈。
