---
layout: ../../layouts/MarkdownPostLayout.astro
title: 'Astro 学习笔记（一）'
pubDate: '2025-12-12'
description: '环境准备和项目创建 '
author: '人在呢 '
image:
    url: 'https://docs.astro.build/assets/rose.webp'
    alt: 'The Astro logo on a dark background with a pink glow.'
tags: ["astro", "blogging", "learning in public","准备工作"]
---

 欢迎来到 **YAstro** 的博客区！在这里，我将分享我用 Astro 框架建立网站的学习历程。

## 前期准备

### —— 需要了解的了解的知识

 **什么是 Astro**：
Astro 是适合构建像博客、营销网站、电子商务网站这样的以内容驱动的网站的 Web 框架。

**准备开发环境**

**三个工具**

- 终端：CMD/Powershell
- Node.js 
- 代码编辑器: VS Code

**注意** 

要在系统上运行 Astro，需要安装一个兼容版本的 [Node.js](https://nodejs.org/zh-cn) 。
Astro 支持偶数版本号的 Node.js。当前各自的最低支持版本为：v18.20.8、v20.3.0 和 v22.0.0。（注意：v19 和 v21 版本不受支持。）

查看node.js版本号：

```cmd
    node -v
    v22.14.0
```

**启动 Astro 设置向导**

```cmd
    npm create astro@latest
```

**在开发模式下运行 Astro**

```cmd
    npm run dev
```

**查看网站预览**

打开浏览器，在地址栏输入 `http://localhost:4321。`, 看到了 Astro 的欢迎页面。

**创建第一个自己的博客网站Yastro**

 1. astro 页面

    astro 页面实际是一个特殊的 HTML 页面，它有如下结构：
    ``` astro
    ---
        // 代码区 （Javascript/Typescript 代码）
    ---
        <!---模板区--->
          
    ```

 2. 删除或者重命名系统创建的`src\components\Welcome.astro`、`src\pages\index.astro`、`src\layouts\Layout.astro`文件。

 3. 创建自己的 index.astro 页面文件

 `src\pages\index.astro`:

 ```vscode
    ---
    ---

    <html lang="zh-cn">
      <head>
        <meta charset="utf-8" />
        <link rel="icon" type ="image/svg+xml" href="/favicon.svg" />
        <meta name="viewport" content="width=device-width" />
        <meta name="generator" content = {Astro.generator} >
        <title>Yastro</title>
      </head>
      <body>
        <h1>Hi Astro!</h1>
      </body>
    </html>
```

浏览器地址栏输入`http:localhost:4321` 看到这个页面。

创建一个 about.astro 页面
`src\pages\about.astro`:

```vscode

<body>
  <h1> Y Astro 网站</h1>
  <h1>关于我</h1>
  <h2>……和我的新 Astro 网站！</h2>

  <p>我正在学习 Astro 的入门教程。这是我网站上的第二个页面，也是我自己建立的第一页面！</p>

  <p>随着我完成更多教程，该站点将更新，所以请继续查看我的旅程将如何进行吧！</p>
</body>
```

打开浏览器，地址栏输入`http://localhost:4321/about` 就看到了这个页面。

astro不仅支持 HTML 页面，还支持在浏览器中直接渲染Markdown文本。
创建一个post-1.md文本文件： 
`src\pages\posts\post-1.md`：

是的，就是这篇博客文章，它是一个 Markdown 格式的文本文件。值得注意的是这个文件与普通 Markdown 有一点区别之处是，文件顶部的 “前言” 信息，也就是用`---` 分隔开的区域内的信息，称为 frontmatter。此数据（包括标签和文章图像）是 Astro 可以使用的有关该文章的信息。它不会自动出现在页面上，但可以用于组织astro页面，后续会看到它的作用。

## 今天做了什么

 1. **安装 Astro**：首先，创建了一个新的 Astro 项目。

 2. **制作页面**：然后是如何通过创建新的 `.astro` 文件，并将它们保存在 `src/pages/` 文件夹。

 3. **浏览博客文章**：启动开发服务器，用浏览器查看自己创建的页面文件。

## 下一步计划

按照 [**Astro教程**](https://docs.astro.build/zh-cn/tutorial/) 的学习进度，继续学习和编写更多内容。

