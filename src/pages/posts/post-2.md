---
    layout: ../../layouts/MarkdownPostLayout.astro
    title: 'Astro 学习笔记（二）'
    author: '人在呢'
    pubDate: '2025-12-13'
    description: 'astro的页面'
    image:
        url: "https://docs.astro.build/assets/arc.webp"
        alt: "The Astro logo on a dark background with a purple gradient arc."
    tags: ["astro", "blogging", "learning in public", "successes`   12QZW3S56YU7", "css"]
---

## 今天的内容

**在 Astro 页面中使用变量**

在Astro 脚本中使用 JavaScript 或 TypeScript 表达式定义变量。如：`const pageTitle = "About"`;在Astro 模板中使用这些大括号内的变量来告诉 Astro 你正在使用一些 JavaScript。如：`<h1>{pageTitle}</h1>`
这样做的目的是可以使页面显示动态内容。

编写 Astro 模板非常像编写 HTML，但可以在其中加入 JavaScript 表达式。
Astro 的 frontmatter 脚本只包含 JavaScript。
可以在 .astro 文件的任何部分使用所有现代的 JavaScript 逻辑运算符、表达式和函数。但是，大括号仅在 HTML 模板主体中是必要的。

**条件渲染元素**

可以使用脚本变量选择是否渲染HTML `<body>...</body>` 内容中的个别元素。

例如，在页面的 frontmatter 脚本区有如下代码：

```astro
    ---
    const operatingSystem = "Linux";
    const quantity = 3;
    const footwear = "靴子";
    const student = false;
    ---
```

对于以下 Astro 模板表达式，可以预测一下对应 HTML 在浏览器中的显示什么？

```astro
    <p>{operatingSystem}</p>
    {student && <p>我仍然在学校。</p>}
    <p>我有 {quantity + 8} 双{footwear}</p>
    {operatingSystem === "MacOS" ? <p>我在使用 Mac。</p> : <p>我没有使用 Mac。</p>}
```

答案分别是：“Linux”、“ ”、 “11双靴子”、 “我没有使用 Mac。”

**页面样式及全局样式**

有几种方法可以在 Astro 页面中定义样式。在astro页面文件中，可以象普通HTML文件一样，引入 `<style>...</style>` 标签并在其中定义样式，也可以定义全局样式文件，并在astro文件的脚本区引入全局样式。如果两者都使用，页面样式标签的优先级高于全局样式文件。比较特别的是：astro页面样式可以使用变量来动态改变样式，详见 [css动态变量](https://docs.astro.build/zh-cn/tutorial/2-pages/4/)。

现在创建 `src\styles\global.css` 全局样式文件,并在所有页面的脚本区用  `import '../styles/global.css'` 指令引入样式。

## 今天做了什么

 1. **添加 about.astro 的动态内容：**

 2. **给 about.astro 添加样式：**
 
 3. **创建了全局样式文件，为所有页面引入了全局样式：**
