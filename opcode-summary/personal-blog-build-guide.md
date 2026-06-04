# 个人博客网站搭建详细步骤

本文档带你从零搭建一个适合长期维护的个人博客网站，推荐方案为：

```text
Astro + MDX + Tailwind CSS + Pagefind + Giscus + Vercel / Cloudflare Pages
```

这个方案适合技术博客、个人主页、项目展示、文章归档和后续扩展。

## 1. 准备环境

先确认本机已经安装以下工具：

- Node.js：建议使用 LTS 版本，推荐 Node.js 20 或更高版本
- pnpm：推荐的包管理器
- Git：用于版本管理和部署
- VS Code：推荐编辑器

检查版本：

```bash
node -v
npm -v
git --version
```

如果没有安装 `pnpm`，执行：

```bash
npm install -g pnpm
```

检查：

```bash
pnpm -v
```

## 2. 创建 Astro 项目

在你希望存放博客项目的位置执行：

```bash
pnpm create astro@latest my-blog
```

推荐选择：

```text
Where should we create your new project? my-blog
How would you like to start your new project? Use a minimal template
Install dependencies? Yes
Initialize a new git repository? Yes
```

进入项目：

```bash
cd my-blog
```

启动开发服务器：

```bash
pnpm dev
```

浏览器打开：

```text
http://localhost:4321
```

如果能看到 Astro 页面，说明项目创建成功。

## 3. 推荐项目结构

建议调整为以下结构：

```text
my-blog/
  public/
    favicon.svg
    images/
  src/
    assets/
    components/
      Header.astro
      Footer.astro
      PostCard.astro
    content/
      blog/
        first-post.md
    layouts/
      BaseLayout.astro
      BlogLayout.astro
    pages/
      index.astro
      about.astro
      blog/
        index.astro
        [...slug].astro
    styles/
      global.css
    content.config.ts
  astro.config.mjs
  package.json
```

## 4. 安装常用能力

安装 MDX 支持：

```bash
pnpm astro add mdx
```

安装 Tailwind CSS：

```bash
pnpm astro add tailwind
```

安装站内搜索 Pagefind：

```bash
pnpm add -D pagefind
```

安装 RSS：

```bash
pnpm add @astrojs/rss
```

## 5. 配置博客内容集合

创建文件：

```text
src/content.config.ts
```

写入：

```ts
import { defineCollection, z } from 'astro:content';

const blog = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    description: z.string(),
    date: z.date(),
    updated: z.date().optional(),
    tags: z.array(z.string()).default([]),
    draft: z.boolean().default(false),
  }),
});

export const collections = { blog };
```

## 6. 创建第一篇文章

创建目录：

```text
src/content/blog/
```

创建文章：

```text
src/content/blog/first-post.md
```

写入：

```md
---
title: 我的第一篇博客
description: 记录个人博客网站的开始。
date: 2026-06-04
tags: [博客, Astro, 前端]
draft: false
---

# 我的第一篇博客

这是我的个人博客第一篇文章。

我会在这里记录技术学习、项目实践、生活思考和长期积累。
```

## 7. 创建基础布局

创建文件：

```text
src/layouts/BaseLayout.astro
```

写入：

```astro
---
import '../styles/global.css';

interface Props {
  title: string;
  description?: string;
}

const { title, description = '个人博客' } = Astro.props;
---

<!doctype html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content={description} />
    <title>{title}</title>
  </head>
  <body>
    <header class="site-header">
      <a class="site-title" href="/">我的博客</a>
      <nav>
        <a href="/blog">文章</a>
        <a href="/about">关于</a>
      </nav>
    </header>

    <main class="container">
      <slot />
    </main>

    <footer class="site-footer">
      <p>© 2026 我的博客</p>
    </footer>
  </body>
</html>
```

## 8. 添加全局样式

创建文件：

```text
src/styles/global.css
```

写入：

```css
:root {
  color-scheme: light dark;
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  color: #172033;
  background: #f6f7fb;
}

body {
  margin: 0;
  min-height: 100vh;
}

a {
  color: inherit;
  text-decoration: none;
}

a:hover {
  color: #2563eb;
}

.container {
  width: min(920px, calc(100% - 32px));
  margin: 0 auto;
  padding: 48px 0;
}

.site-header {
  width: min(920px, calc(100% - 32px));
  margin: 0 auto;
  padding: 24px 0;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.site-title {
  font-weight: 800;
  font-size: 20px;
}

nav {
  display: flex;
  gap: 20px;
}

.site-footer {
  width: min(920px, calc(100% - 32px));
  margin: 0 auto;
  padding: 32px 0;
  color: #667085;
}

.hero {
  padding: 72px 0;
}

.hero h1 {
  font-size: clamp(40px, 8vw, 76px);
  line-height: 1;
  margin: 0 0 24px;
}

.hero p {
  max-width: 660px;
  font-size: 20px;
  line-height: 1.8;
  color: #475467;
}

.post-list {
  display: grid;
  gap: 20px;
}

.post-card {
  padding: 24px;
  border: 1px solid #e4e7ec;
  border-radius: 20px;
  background: white;
}

.post-card h2 {
  margin: 0 0 12px;
}

.post-card p {
  color: #475467;
}

.post-meta {
  color: #667085;
  font-size: 14px;
}

.prose {
  font-size: 18px;
  line-height: 1.9;
}

.prose img {
  max-width: 100%;
  border-radius: 16px;
}

@media (prefers-color-scheme: dark) {
  :root {
    color: #f2f4f7;
    background: #0b1020;
  }

  .post-card {
    background: #111827;
    border-color: #273244;
  }

  .hero p,
  .post-card p,
  .post-meta,
  .site-footer {
    color: #98a2b3;
  }
}
```

## 9. 创建首页

编辑：

```text
src/pages/index.astro
```

写入：

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="我的博客" description="记录技术、项目和思考。">
  <section class="hero">
    <h1>记录技术、项目与长期思考。</h1>
    <p>
      这里是我的个人博客。我会分享前端开发、工程实践、学习笔记、项目复盘和一些生活观察。
    </p>
  </section>

  <section>
    <h2>开始阅读</h2>
    <p><a href="/blog">查看全部文章</a></p>
  </section>
</BaseLayout>
```

## 10. 创建文章列表页

创建：

```text
src/pages/blog/index.astro
```

写入：

```astro
---
import { getCollection } from 'astro:content';
import BaseLayout from '../../layouts/BaseLayout.astro';

const posts = (await getCollection('blog'))
  .filter((post) => !post.data.draft)
  .sort((a, b) => b.data.date.valueOf() - a.data.date.valueOf());
---

<BaseLayout title="文章 - 我的博客" description="我的博客文章列表。">
  <h1>文章</h1>

  <div class="post-list">
    {posts.map((post) => (
      <article class="post-card">
        <p class="post-meta">{post.data.date.toLocaleDateString('zh-CN')}</p>
        <h2><a href={`/blog/${post.slug}`}>{post.data.title}</a></h2>
        <p>{post.data.description}</p>
      </article>
    ))}
  </div>
</BaseLayout>
```

## 11. 创建文章详情页

创建：

```text
src/pages/blog/[...slug].astro
```

写入：

```astro
---
import { getCollection, render } from 'astro:content';
import BaseLayout from '../../layouts/BaseLayout.astro';

export async function getStaticPaths() {
  const posts = await getCollection('blog');

  return posts
    .filter((post) => !post.data.draft)
    .map((post) => ({
      params: { slug: post.slug },
      props: { post },
    }));
}

const { post } = Astro.props;
const { Content } = await render(post);
---

<BaseLayout title={`${post.data.title} - 我的博客`} description={post.data.description}>
  <article class="prose">
    <p class="post-meta">{post.data.date.toLocaleDateString('zh-CN')}</p>
    <h1>{post.data.title}</h1>
    <Content />
  </article>
</BaseLayout>
```

## 12. 创建关于页

创建：

```text
src/pages/about.astro
```

写入：

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="关于我 - 我的博客" description="关于我的介绍。">
  <article class="prose">
    <h1>关于我</h1>
    <p>你好，我是这里的作者。</p>
    <p>这个博客用于记录技术学习、项目实践、读书笔记和长期思考。</p>
  </article>
</BaseLayout>
```

## 13. 配置站点地址

编辑：

```text
astro.config.mjs
```

参考配置：

```js
import { defineConfig } from 'astro/config';
import mdx from '@astrojs/mdx';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  site: 'https://你的域名.com',
  integrations: [mdx()],
  vite: {
    plugins: [tailwindcss()],
  },
});
```

如果还没有域名，可以先写部署平台给你的地址，后续再改。

## 14. 添加 RSS

创建：

```text
src/pages/rss.xml.js
```

写入：

```js
import rss from '@astrojs/rss';
import { getCollection } from 'astro:content';

export async function GET(context) {
  const posts = await getCollection('blog');

  return rss({
    title: '我的博客',
    description: '记录技术、项目和思考。',
    site: context.site,
    items: posts
      .filter((post) => !post.data.draft)
      .map((post) => ({
        title: post.data.title,
        description: post.data.description,
        pubDate: post.data.date,
        link: `/blog/${post.slug}/`,
      })),
  });
}
```

访问地址：

```text
https://你的域名.com/rss.xml
```

## 15. 添加站内搜索

先修改 `package.json` 的脚本：

```json
{
  "scripts": {
    "dev": "astro dev",
    "build": "astro build && pagefind --site dist",
    "preview": "astro preview"
  }
}
```

构建后会生成 Pagefind 搜索索引。

可以后续创建一个搜索页：

```text
src/pages/search.astro
```

基础搜索页面可以先简单留空，等网站主体完成后再做完整交互。

## 16. 添加评论系统 Giscus

Giscus 依赖 GitHub Discussions，适合静态博客。

步骤：

1. 将博客项目推送到 GitHub。
2. 在 GitHub 仓库开启 Discussions。
3. 访问 `https://giscus.app/zh-CN`。
4. 填写仓库地址。
5. 选择 Discussion 分类。
6. 复制生成的脚本。
7. 放到文章详情页底部。

示例位置：

```astro
<article class="prose">
  <p class="post-meta">{post.data.date.toLocaleDateString('zh-CN')}</p>
  <h1>{post.data.title}</h1>
  <Content />
</article>

<section>
  <!-- 粘贴 Giscus 生成的 script -->
</section>
```

## 17. 本地检查

开发时运行：

```bash
pnpm dev
```

构建检查：

```bash
pnpm build
```

本地预览构建结果：

```bash
pnpm preview
```

如果 `pnpm build` 成功，说明可以部署。

## 18. 推送到 GitHub

如果创建项目时没有初始化 Git，可以执行：

```bash
git init
```

提交代码：

```bash
git add .
git commit -m "init personal blog"
```

在 GitHub 创建一个新仓库，例如：

```text
my-blog
```

绑定远程仓库：

```bash
git remote add origin git@github.com:你的用户名/my-blog.git
git branch -M main
git push -u origin main
```

## 19. 部署到 Vercel

适合想快速上线的人。

步骤：

1. 打开 `https://vercel.com`。
2. 使用 GitHub 登录。
3. 点击 `Add New Project`。
4. 选择你的博客仓库。
5. Framework Preset 选择 `Astro`。
6. Build Command 使用 `pnpm build`。
7. Output Directory 使用 `dist`。
8. 点击 Deploy。

部署完成后会得到一个地址，例如：

```text
https://my-blog.vercel.app
```

## 20. 部署到 Cloudflare Pages

适合追求稳定、速度和免费额度的人。

步骤：

1. 打开 `https://dash.cloudflare.com`。
2. 进入 `Workers & Pages`。
3. 点击 `Create application`。
4. 选择 `Pages`。
5. 连接 GitHub 仓库。
6. Framework preset 选择 `Astro`。
7. Build command 填写 `pnpm build`。
8. Build output directory 填写 `dist`。
9. 点击部署。

## 21. 绑定自定义域名

如果你有域名，例如：

```text
example.com
```

建议绑定：

```text
www.example.com
blog.example.com
```

Vercel 或 Cloudflare Pages 都会提示你添加 DNS 记录。

常见记录类型：

```text
CNAME www -> 平台提供的地址
CNAME blog -> 平台提供的地址
```

绑定完成后，记得把 `astro.config.mjs` 里的 `site` 改成正式域名：

```js
export default defineConfig({
  site: 'https://blog.example.com',
});
```

## 22. SEO 基础优化

建议每篇文章都写好：

- `title`
- `description`
- `date`
- `tags`

建议每个页面都有：

- 页面标题
- 页面描述
- 合理的 H1
- 清晰的 URL
- RSS
- sitemap

安装 sitemap：

```bash
pnpm astro add sitemap
```

配置 `site` 后，Astro 会生成站点地图。

## 23. 文章写作规范

建议文章命名：

```text
src/content/blog/astro-blog-guide.md
src/content/blog/javascript-async-notes.md
src/content/blog/project-review-001.md
```

建议 frontmatter：

```md
---
title: Astro 博客搭建记录
description: 记录一次从零搭建 Astro 博客的过程。
date: 2026-06-04
tags: [Astro, 博客, 前端工程]
draft: false
---
```

草稿文章可以设置：

```md
draft: true
```

这样就不会出现在生产环境文章列表中。

## 24. 建议先实现的功能清单

第一阶段，先完成可上线版本：

- 首页
- 文章列表页
- 文章详情页
- 关于页
- RSS
- 基础 SEO
- 移动端适配

第二阶段，再增强体验：

- 标签页
- 归档页
- 站内搜索
- 评论系统
- 代码高亮主题
- 深色模式切换

第三阶段，做个人品牌化：

- 项目作品页
- 友链页
- 简历页
- 自定义域名
- 访问统计
- Open Graph 分享图

## 25. 最终验收清单

上线前检查：

- `pnpm build` 可以成功执行
- 首页可以打开
- 文章列表可以打开
- 文章详情可以打开
- 移动端布局正常
- RSS 可以访问
- 页面标题和描述正确
- 没有草稿文章被发布
- 外链可以正常访问
- 部署平台自动构建成功

## 26. 推荐路线

如果你想最快完成个人博客，建议按这个顺序做：

1. 创建 Astro 项目。
2. 配置 MDX 和 Tailwind。
3. 创建基础布局。
4. 创建首页、文章列表、文章详情、关于页。
5. 写 3 篇初始文章。
6. 添加 RSS 和 sitemap。
7. 推送 GitHub。
8. 部署到 Vercel 或 Cloudflare Pages。
9. 绑定域名。
10. 后续再补搜索、评论和标签页。

完成以上步骤后，你就拥有了一个简洁、快速、可长期维护的个人博客网站。
