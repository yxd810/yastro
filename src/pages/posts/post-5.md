---
layout: "../../layouts/MarkdownPostLayout.astro"
title: 'Astro 学习笔记（五）'
pubDate: "2025-12-12"
description: '这是我学习 Astro 的第五篇笔记 '
author: 'Yin '
image:
    url: 'https://docs.astro.build/assets/rose.webp'
    alt: 'The Astro logo on a dark background with a pink glow.'
tags: ["astro", "blogging", "learning in public", "Markdown"]

---

## 动态页面路由

现在可以使用 `.astro` 文件创建整套动态页面，这些文件需要向外暴露一个 `getStaticPaths()` 函数。

1.创建一个新文件：`src/pages/tags/[tag].astro`。（你需要创建一个新文件夹。）注意文件名（`[tag].astro`）使用方括号。将以下代码粘贴到文件中：

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

4.在浏览器中访问 `http://localhost:4321/tags/astro`，你应该能看到一个页面，它是从 `[tag].astro` 动态生成的。检查是否还为每个标签创建了页面，例如 `/tags/successes`、`/tags/community`、`/tags/learning%20in%20public` 等，或者你自定义的标签。你可能需要先退出并重新启动开发服务器才能看到这些新页面。
