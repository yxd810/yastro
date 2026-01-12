---
layout: "../../layouts/MarkdownPostLayout.astro"
title: 'Astro 学习笔记（五）'
pubDate: "2025-12-16"
description: '动态页面路由'
author: '人在呢'
image:
    url: 'https://docs.astro.build/assets/rose.webp'
    alt: 'The Astro logo on a dark background with a pink glow.'
tags: ["astro", "blogging", "learning in public", "Markdown"]

---

## 动态页面路由

现在可以使用 `.astro` 文件创建整套动态页面，这些文件需要向外暴露一个 `getStaticPaths()` 函数。

1.创建一个新文件：`src/pages/tags/[tag].astro`：

```astro
    ---
    import BaseLayout from '../../layouts/BaseLayout.astro';

    export async function getStaticPaths() {
      return [
        { params: { tag: "astro" } },
        { params: { tag: "successes" } },
        { params: { tag: "community" } },
        { params: { tag: "blogging" } },
        { params: { tag: "setbacks" } },
        { params: { tag: "learning in public" } },
      ];
    }

    const { tag } = Astro.params;
    ---
    <BaseLayout pageTitle={tag}>
      <p>包含「{tag}」标签的文章</p>
    </BaseLayout>
```

`getStaticPaths` 函数返回一个页面路由数组，这些页面将使用文件中定义的相同模板。

2.如果你已经自定义了博客文章，用你自己的文章中使用的标签替换各个标签值（例如 “astro”、“successes”、“community” 等）。

3.确保每篇博客文章至少包含一个标签，以数组的形式编写，例如 tags: ["blogging"]。

4.在浏览器中访问 `http://localhost:4321/tags/astro`，看到一个页面，它是从 `[tag].astro` 动态生成的。
