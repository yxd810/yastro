---
layout: ../../layouts/MarkdownPostLayout.astro
title: "Astro 学习笔记（三）"
author: "人在呢"
pubDate: "2022-12-14" 
description: "Astro 组件"
image:
    url: "https://docs.astro.build/assets/rays.webp"
    alt: "The Astro logo on a dark background with rainbow rays."
tags: ["astro", "compoent", "setbacks", "community"]
---

## 组件是什么

组件是把不同astro页面的相同部分的HTML代码分离出来,组成一个可以复用的代码片段.它实际上也是 astro 页面文件。

**今天的内容**

1. 显示页面链接菜单的导航组件

2. 包含在每个页面底部的页脚组件
    
3. 在页脚中使用的社交媒体组件，链接到个人资料页面
    
4. 页面脚本。一个可交互的菜单组件，用于在移动设备上切换导航

### 1. 创建一个可重复使用的导航组件

创建一个新文件：`src/components/Navigation.astro`。

```astro
---
import "../styles/global.css";
---
<div id="main-menu" class="nav-links">
  <a href="/">首页</a>
  <a href="/about/">关于</a>
  <a href="/blog/">博客</a>
</div>
```
 在 index.astro 页面中导入并使用 Navigation.astro，`src\pages\index.astro`:
```astro
---
import Navigation from '../components/Navigation.astro';//引入导航组件
import "../styles/global.css";

const pageTitle = "首页";
---
<!---在模板区直接运用导航组件--->
<Navigation />

```
在浏览器中会看到了内容为：“首页”、“关于”和“博客”的导航条。可以运用此方法将导航条添加到所有页面上。

### 2. 创建页脚组件

 创建一个新组件 `src\compontes\Footer.astro`，实现在页面上显示页脚， 文件内容如下：
```astro
---
const { platform, username } = Astro.props;
---
<a href={`https://www.${platform}.com/${username}`}>{platform}</a>
```
运用与导航组件一样的方法，可以把页脚组件引入到所有页面中，以`src\pages\index.astro`为例，引入页脚后的完整代码如下：
```astro
---
import Navigation from '../components/Navigation.astro';//引入导航组件
import Footer from '../components/Footer.astro';//引入页脚组件
import "../styles/global.css";

const pageTitle = "首页";
---
<!---在模板区直接运用导航组件--->
<Navigation />

<div>页面其他内容</div>

<!---在模板区直接运用页脚组件--->
<Footer/>
    
```
### 3. 组件嵌套

为了说明组件是如何嵌套的，这里再创建一个组件`src\components\social.astro`,它的内容如下：

```astro
---
const { platform, username } = Astro.props; //  前置元数据。接收来自父组件的属性
---
<a href={`https://www.${platform}.com/${username}`}>{platform}</a>

<style>
  a {
    padding: 0.5rem 1rem;
    color: white;
    background-color: #4c1d95;
    text-decoration: none;
  }
</style>
    
```
从这个组件的内容不难看出，它实际是显示个人的社交媒体平台账户链接。下面我们把这个被称为social.astro的组件嵌套进Footer.astro组件中，再看效果。
重新编辑`src\components\Footer.astro`组件文件，引入social.astro 组件：
```astro
---
import "../styles/global.css";
import Social from './Social.astro'; //引入social.astro组件
---
<style>
  footer {
    display: flex;
    gap: 1rem;
    margin-top: 2rem;
  }
</style>

<footer>
  <!---在页脚部分运用组件--->
  <Social platform="github" username="withastro" />
  <Social platform="youtube" username="astrodotbuild" />
</footer>
     
```
此时刷浏览器首页，应该看到社交账户链接显示在了主页的页脚部分。说明组件完美嵌套。

***自测题***

1. 你需要在 Astro 组件的前置元数据中写入什么代码来将 title、author 和 date 的值作为 props 接收？

    A.`const { title, author, date } = Astro.props`

    B. `import BlogPost from '../components/BlogPost.astro'`

    C.`<BlogPost title="My First Post" author="Dan" date="12 Aug 2022" />`
     
2. 如何将值作为 props 传递给 Astro 组件？

    A. `const { title, author, date } = Astro.props`

    B. `import BlogPost from '../components/BlogPost.astro'`

    C. `<BlogPost title="My First Post" author="Dan" date="12 Aug 2022" />`

*正确答案*：1.A；2.C

### 4. 编写第一个浏览器脚本，使网站访问者可以打开和关闭导航菜单

- 创建 Menu 组件，用于打开和关闭移动端菜单；
- 将组件`<Menu/>`嵌套在组件 Header.astro 之中，以实现所有页面都具有 Menu 功能；（关于Header组件的[参考内容](https://docs.astro.build/zh-cn/tutorial/3-components/3/)）
- 创建脚本文件:`src\scripts\menu.js`

```javascript
    
const menu = document.querySelector('.menu');

menu?.addEventListener('click', () => {
  const isExpanded = menu.getAttribute('aria-expanded') === 'true';
  menu.setAttribute('aria-expanded', `${!isExpanded}`);
});
    
```
- 在index.astro页面中运用 Menu 脚本
```astro
<!--index.astro其他部分--->
<body>
  <!--index页面模板的其他内容--->
  ...  
  <!---引入menu.js脚本--->
  <script>
    import "../scripts/menu.js";
  </script>
</body>
```    
这时改变浏览器窗口大小，可以看到Menu效果
