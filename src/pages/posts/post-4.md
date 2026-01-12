---
layout: "../../layouts/MarkdownPostLayout.astro"
title: 'Astro 学习笔记（四）'
author: '人在呢'
description: "动态展示文章列表"
image:
  url: "https://docs.astro.build/default-og-image.png"
  alt: "The word astro against an illustration of planets and stars."
pubDate: "2025-12-15"
tags: ["astro", "markdown", "blog"]
---
## 动态展示文章列表

import.meta.glob() 将返回一个对象数组，每个博客文章对应一个对象。

创建 `src\pages\blog.astro` ，以返回关于所有 Markdown 文件的信息。

```astro
  ---
  import BaseLayout from "../layouts/BaseLayout.astro";
  const allPosts = Object.values(import.meta.glob('./posts/*.md', { eager: true }));// 获取所有文章的元数据。返回一个对象数组，每个博客文章对应一个对象。

  const pageTitle="笔记";
  ---
  <BaseLayout pageTitle={pageTitle}>
    <p> —— 在这里，我将分享我的 Astro 学习之旅。</p>
    <hr/>
    <ul>
    <!---将个别的 <li> 标签替换为以下 Astro 代码--->
      {allPosts.map((post: any) => <li><a href={post.url}>{post.frontmatter.title}</a></li>)}
    </ul>
  </BaseLayout>
```

通过对 import.meta.glob() 返回的数组进行映射，整个博客文章列表现在通过 Astro 内置的 TypeScript 支持而动态生成。
