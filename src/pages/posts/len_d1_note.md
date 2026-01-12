---
layout: ../../layouts/MarkdownPostLayout.astro
title: 'Cloudflare D1数据库 学习（一）'
pubDate: '2025-12-20'
description: '学习 Cloudflare D1数据库的创建、与cloudfalare worker的绑定及查询'
author: '人在呢 '
image:
    url: 'https://docs.astro.build/assets/rose.webp'
    alt: 'The Astro logo on a dark background with a pink glow.'
tags: ["cloudflare", "database", "D1"]
---

本文将：
* 使用 D1（Cloudflare原生的无服务器 SQL 数据库），创建一个数据库。
* 在数据库中创建一个 Cutomers 表单，添加数据，并通过命令行查询数据库。
* 将Cloudflare Worker与D1数据库绑定，并用程序查询 D1 数据库。    

有关D1更详细的使用教程请[阅读原文](https://developers.cloudflare.com/d1/get-started/#prerequisites)

## *前提条件*:

1. 注册账号

2. 安装 Node.js

## 1.  创建 worker

创建一个新的 worker 作为查询数据库的手段。

*仪表板（Cloudflare dashboard）*

1. 在cloudflare 仪表板左边栏找到 **Workers & Pages** ，[选择 **Workers & Pages**](https://dash.cloudflare.com/?to=/:account/workers-and-pages)
2. 选择 **Create application**.
3. 选择 **Start with Hello World!** > **Get started**.
4. 为你的Worker命名.本文将创建的 Worker 命名为 ：`d1-tutorial`
5. 选择 **Deploy**.

## 2.创建一个数据库

D1数据库在概念上类似于许多其他 SQL 数据库：数据库可能包含一个或多个表，并有可选索引。D1 使用了熟悉的 [SQL 查询语言↗](https://www.sqlite.org/lang.html)，与 SQLite 相同。

创建你的第一个D1数据库：

*仪表板（Cloudflare dashboard）*

1. In the Cloudflare dashboard, go to the **D1 SQL database** page.
   [Go to**D1 SQL database**](https://dash.cloudflare.com/?to=/:account/workers/d1)

2. Select **Create Database**.

3. Name your database. For this tutorial, name your D1 database .`prod-d1-tutorial`

4. (Optional) Provide a location hint. Location hint is an optional parameter you can provide to indicate your desired geographical location for your database. Refer to [Provide a location hint](https://developers.cloudflare.com/d1/configuration/data-location/#provide-a-location-hint) for more information.

5. Select **Create**.

**注释**

数据库名称应该是：

* 使用ASCII字符组合，字符数少于32个，并使用破折号（-）代替空格。
* 描述用例和环境。例如，“staging-db-web”或“production-db-backend”。
* 仅描述数据库，代码中不直接引用。

## 3. 将 worker 与 D1 数据库绑定

你必须为你的worker创建一个绑定，才能连接到你的 D1 数据库。[绑定](https://developers.cloudflare.com/workers/runtime-apis/bindings/)。这样才能允许你的worker访问 Cloudflare 开发者平台上的资源，比如 D1。

要将你的D1数据库绑定到Worker：

*仪表板（Cloudflare dashboard）*

You create bindings by adding them to the Worker you have created.

1. Select the Worker you created in [step 1](https://developers.cloudflare.com/d1/get-started/#1-create-a-worker).`d1-tutorial`
2. Go to the **Bindings** tab.
3. Select **Add binding**.
4. Select **D1 database** > **Add binding**.
5. Name your binding in **Variable name**, then select the D1 database you created in [step 2](https://developers.cloudflare.com/d1/get-started/#2-create-a-database) from the dropdown menu. For this tutorial, name your binding .`prod-d1-tutorial``prod_d1_tutorial`
6. Select **Add binding**.

## 4. 对你的 D1 数据库运行查询

**首先在D1数据库中创建表单**

*仪表板（Cloudflare dashboard）*

Use the Dashboard to create a table and populate it with data.

1. Select the database you created in [step 2](https://developers.cloudflare.com/d1/get-started/#2-create-a-database).`prod-d1-tutorial`

2. Select **Console**.

3. Paste the following SQL snippet.
   
   ```
   DROP TABLE IF EXISTS Customers;
   CREATE TABLE IF NOT EXISTS Customers (CustomerId INTEGER PRIMARY KEY, CompanyName TEXT, ContactName TEXT);
   INSERT INTO Customers (CustomerID, CompanyName, ContactName) VALUES (1, 'Alfreds Futterkiste', 'Maria Anders'), (4, 'Around the Horn', 'Thomas Hardy'), (11, 'Bs Beverages', 'Victoria Ashworth'), (13, 'Bs Beverages', 'Random Name');
   ```

4. Select **Execute**. This creates a table called in your database.`Customers``prod-d1-tutorial`

5. Select **Tables**, then select the table to view the contents of the table.`Customers`

**在仪表板中查询数据**

1. 选择D1 SQL 数据库

2. 选择你建立的数据库 prod-d1-tutorial

3. 点击 Explore Data 按钮

4. 在左侧栏选择刚才建立的 Customers 表
   在右侧窗口区你会看到新的数据已经添加到了 Customers 表中。



**在 worker 中写查询**



你可以通过在 Worker 中编写 Javascript 代码 对 D1数据库进行查询.

1. In the Cloudflare dashboard, go to the **Workers & Pages** page.
   [Go to**Workers & Pages**](https://dash.cloudflare.com/?to=/:account/workers-and-pages)

2. Select the Worker you created.`d1-tutorial`

3. Select the **Edit code** icon (**</>**).

4. Clear the contents of the file, then paste the following code:`worker.js`
   
   ```JavaScript
   export default {  
       async fetch(request, env) {
       const { pathname } = new URL(request.url);
   
       if (pathname === "/api/beverages") {
         // If you did not use `DB` as your binding name, change it here
         const { results } = await env.prod_d1_tutorial.prepare(
           "SELECT * FROM Customers WHERE CompanyName = ?"
         )
           .bind("Bs Beverages")
           .run();
         return new Response(JSON.stringify(results), {
           headers: { 'Content-Type': 'application/json' }
         });
        }
         return new Response(
         "Call /api/beverages to see everyone who works at Bs Beverages"
           );
      },
   };
   ```

5. Select **Save**.  

## 5. 部署应用程序

将您的应用部署到Cloudflare的全球网络上。

1. In the Cloudflare dashboard, go to the **Workers & Pages** page. 
2. Select your Worker.`d1-tutorial`
3. Select **Deployments**.
4. From the **Version History** table, select **Deploy version**.
5. From the **Deploy version** page, select **Deploy**.

This deploys the latest version of the Worker code to production.

## 6.（可选）用Wrangler本地开发

如果你用的是带Wrangler的D1，可以在本地测试数据库。在你的项目目录中：

1. 运行：Wrangler 

```bash
      wrangler dev
      npx wrangler dev`
```

   运行 时，Wrangler 会提供一个 URL（很可能）用于审核你的 Worker。`rangler dev``localhost:8787

2. 访问网址。
   页面显示 。`Call /api/beverages to see everyone who works at Bs Beverages`

3. 测试你的数据库是否运行良好。添加提供的Wrangler网址。例如， 。`/api/beverages``localhost:8787/api/beverages`

如果成功，浏览器会显示你的数据。

注释

只有在使用Wrangler时，才能本地开发。你不能通过Cloudflare仪表盘本地开发。

## 7.（可选）删除你的数据库

*仪表板（Cloudflare dashboard）*

1. In the Cloudflare dashboard, go to the **D1 SQL database** page.
   [Go to**D1 SQL database**](https://dash.cloudflare.com/?to=/:account/workers/d1)

2. Select your D1 database.`prod-d1-tutorial`

3. Select **Settings**.

4. Select **Delete**.

5. Type the name of the database () to confirm the deletion.`prod-d1-tutorial`

警告：请注意，删除你的D1数据库会阻止你的应用正常运行。

## 摘要

本文中：

* 创建了一个D1数据库
* 创建了一个 worker 来访问那个数据库
* 把项目部署在 Cloudflare 云服务器上




































