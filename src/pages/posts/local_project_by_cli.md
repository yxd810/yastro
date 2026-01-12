---
layout: ../../layouts/MarkdownPostLayout.astro
title: 'Cloudflare D1数据库（二）'
pubDate: '2025-12-20'
description: 'Cloudflare D1 数据库笔记（二）：D1的本地应用 '
author: '人在呢 '
image:
    url: 'https://docs.astro.build/assets/rose.webp'
    alt: 'The Astro logo on a dark background with a pink glow.'
tags: ["cloudflare", "database", "D1"]
---
## Cloudflare D1数据库 本地开发

**前提条件**

1. 注册cloudfare账号
2. 安装 Node.js
3. [安装 wrangler](https://developers.cloudflare.com/workers/wrangler/install-and-update/)

## 1. 创建worker

创建一个新的 worker 作为查询数据库的手段。

1. 创建一个新项目，命名为：`d1-tutorial`

```cmd
npm create cloudflare@latest -- d1-tutorial
```

设置时，请选择以下选项：

* 对于_你想从哪种方式开始？_请选择。`Hello World example`
* _你想使用哪个模板？_选择 。`Worker only`
* _对于你想使用哪种语言？_，选择 。`TypeScript`
* 对于“_你想用git做版本控制吗？”_，请选择。`Yes`
* 对于“_你想部署你的应用？”_，请选择（我们将在部署前做一些调整）。`No`

这会创建一个新的目录，如下图所示。`d1-tutorial`

![c88c879c-c225-4b8d-bcea-5d0cf8026f34](file:///C:/Users/XD/Pictures/Typedown/c88c879c-c225-4b8d-bcea-5d0cf8026f34.png)

新目录包括：`d1-tutorial`

* 一个[worker](https://developers.cloudflare.com/workers/get-started/guide/#3-write-code)。`"Hello World"``index.ts`
* 一个[Wrangler配置文件](https://developers.cloudflare.com/workers/wrangler/configuration/)。这个文件是你的工人访问你的D1数据库的方式。`d1-tutorial`
  
## 2. 创建一个数据库

1. 切换到你刚为Workers项目创建的目录：
   ` cd d1-tutorial `
2. 执行以下命令，给数据库命名为：
   `wrangler@latest d1 local-d1-tutorial `
```cmd
npx wrangler@latest d1 create local-d1-tutorial
```
```cmd
✅ Successfully created DB 'prod-d1-tutorial' in region WEUR
Created your new D1 database.

{
  "d1_databases": [
    {
      "binding": "local_d1_tutorial",
      "database_name": "local-d1-tutorial",
      "database_id": "babd90c0-3fcf-4920-bb58-6bb9bb478278"
    }
  ]
}
√ Would you like Wrangler to add it on your behalf? ... yes
√ What binding name would you like to use? ... local_d1_tutorial
√ For local dev, do you want to connect to the remote resource instead of a local resource? ... no

```
这会创建一个新的 D1 数据库，并输出下一步所需的[绑定](https://developers.cloudflare.com/workers/runtime-apis/bindings/)配置。

1. 当提示：`Would you like Wrangler to add it on your behalf?` 选择 `Yes`。
这样会自动把绑定添加到你的Wrangler配置文件中。

## 3. 将 worker 绑定到 D1 数据库

你必须为你的 worker 创建一个绑定，以便连接到你的D1数据库。[绑定](https://developers.cloudflare.com/workers/runtime-apis/bindings/)允许你的工作者访问资源，比如 Cloudflare 开发者平台上的 D1。

要将你的 D1 数据库绑定到Worker：

你可以在执行命令时自动将绑定添加到你的Wrangler配置文件中（步骤3，共[2步。建立数据库](https://developers.cloudflare.com/d1/get-started/#2-create-a-database)）。`wrangler d1 create`

但如果你想手动添加绑定，请按照以下步骤作：

1. 复制第二步（共两步）获得的台词[。从终端创建一个数据库](https://developers.cloudflare.com/d1/get-started/#2-create-a-database)。

2. 把它们加到你的 Wrangler.jsonc 文件末尾。

```cmd
{
  "$schema": "./node_modules/wrangler/config-schema.json",
  "d1_databases": [
    {
      "binding": "local_d1_tutorial",
      "database_name": "local-d1-tutorial",
      "database_id": "<unique-ID-for-your-database>"
    }
  ]
}
```

具体来说：

* 你设置的值（字符串）是**绑定名**，用于在你的工作器中引用这个数据库。在这个教程中，给你的绑定命名。`binding``local_d1_tutorial`
* 绑定名必须是[有效的JavaScript变量名↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#variables)。例如，或者这两个名字都可能是绑定的有效名称。`binding = "MY_DB"``binding = "productionDB"`
* 你的绑定可以在你的 worker 中访问，并且该绑定中暴露了 D1  [Workers Binding API](https://developers.cloudflare.com/d1/worker-api/)。`env.<BINDING_NAME>`

*注释*

当你执行命令时，客户端API包（实现D1 API和数据库类）会自动安装。有关D1 Workers Binding API的更多信息，请参见[Workers Binding API](https://developers.cloudflare.com/d1/worker-api/)。`wrangler d1 create`

## 4. 对 D1 数据库查询

首先需要给D1数据库填充一些数据

在正确准备好[Wrangler配置文件](https://developers.cloudflare.com/workers/wrangler/configuration/)后，建立你的数据库表。
使用下面的 SQL 语法创建一个 schema.sql 文件来初始化你的数据库。

```cmd
DROP TABLE IF EXISTS Customers;
CREATE TABLE IF NOT EXISTS Customers (CustomerId INTEGER PRIMARY KEY, CompanyName TEXT, ContactName TEXT);
INSERT INTO Customers (CustomerID, CompanyName, ContactName) VALUES (1, 'Alfreds Futterkiste', 'Maria Anders'), (4, 'Around the Horn', 'Thomas Hardy'), (11, 'Bs Beverages', 'Victoria Ashworth'), (13, 'Bs Beverages', 'Random Name');
```

先让你的数据库在本地运行和测试。通过运行以下程序启动你的新D1数据库：

```cmd
npx wrangler d1 execute local-d1-tutorial --local --file=./schema.sql
```

```cmd
⛅️ wrangler 4.13.2
-------------------

🌀 Executing on local database prod-d1-tutorial (<DATABASE_ID>) from .wrangler/state/v3/d1:
🌀 To execute on your remote database, add a --remote flag to your wrangler command.
🚣 3 commands executed successfully.
```

注释

该命令会在本地初始化你的数据库，而不是在远程数据库上。`npx wrangler d1 execute`

通过运行以下作验证你的数据是否在数据库中：

```cmd
npx wrangler d1 execute local-d1-tutorial --local --command="SELECT * FROM Customers"
```

```cmd
 🌀 Executing on local database jun-d1-db-gs-2025 (cf91ec5c-fa77-4d49-ad8e-e22921b996b2) from .wrangler/state/v3/d1:
 🌀 To execute on your remote database, add a --remote flag to your wrangler command.
 🚣 1 command executed successfully.
 ┌────────────┬─────────────────────┬───────────────────┐
 │ CustomerId │ CompanyName         │ ContactName       │
 ├────────────┼─────────────────────┼───────────────────┤
 │ 1          │ Alfreds Futterkiste │ Maria Anders      │
 ├────────────┼─────────────────────┼───────────────────┤
 │ 4          │ Around the Horn     │ Thomas Hardy      │
 ├────────────┼─────────────────────┼───────────────────┤
 │ 11         │ Bs Beverages        │ Victoria Ashworth │
 ├────────────┼─────────────────────┼───────────────────┤
 │ 13         │ Bs Beverages        │ Random Name       │
 └────────────┴─────────────────────┴───────────────────┘
```

## 在 worker 中写查询

 建立好数据库后，在你的工作者内部运行SQL查询。

1. 进入你的工作者并打开文件。文件是你配置工人与D1交互的地方。d1-tutorialindex.tsindex.ts

2. 清除 的内容。index.ts

3. 将以下代码片段粘贴到你的文件中：index.ts

```cmd
export default {
  async fetch(request, env) {
    const { pathname } = new URL(request.url);

    if (pathname === "/api/beverages") {
      // If you did not use `DB` as your binding name, change it here
      const { results } = await env.prod_d1_tutorial
        .prepare("SELECT * FROM Customers WHERE CompanyName = ?")
        .bind("Bs Beverages")
        .run();
      return Response.json(results);
    }

    return new Response(
      "Call /api/beverages to see everyone who works at Bs Beverages",
    );
  },
};
```

在上述代码中，你：

1. 在代码中定义一个绑定到你的D1数据库的绑定。这个绑定与你在 Wrangler 配置文件中设置的值相匹配。bindingd1_databases

2. 用 来发送带有占位符（查询中的 ）的预备查询。env.prod_d1_tutorial.prepare?

3. 调用以安全且稳妥地将一个值绑定到该占位符上。在真实应用中，你会允许用户传递他们想要列出的结果。使用函数可以防止用户对你的应用执行任意的SQL（称为“SQL注入”），并删除或修改你的数据库。bind()CompanyNamebind()

4. 通过调用run（）来返回所有行（如果查询返回为无，则返回行不返回）。

5. 如果有查询结果，请以 JSON 格式返回。Response.json(results)
   
配置好 Worker 后，你可以在本地测试项目，再进行全局部署。
   
## 5.部署您的应用程序

将您的应用部署到Cloudflare的全球网络上。

要用 Wrangler 将 Worker 部署到生产环境，你必须先重复数据库配置步骤，并用 flag 替换为 flag，以便读取 Worker 数据。这会创建数据库表并将数据导入到生产版本的数据库中。--local --remote

1. 创建表格，并用步骤4创建的文件添加到远程数据库的条目。输入以确认您的决定。schema.sql  y
   
   ```cmd
   npx wrangler d1 execute prod-d1-tutorial --remote --file=./schema.sql
   ```
   
   ```cmd
   🌀 Executing on remote database prod-d1-tutorial (<DATABASE_ID>):
   🌀 To execute on your local development database, remove the --remote flag from your wrangler command.
   Note: if the execution fails to complete, your DB will return to its original state and you can safely retry.
   ├ 🌀 Uploading <DATABASE_ID>.a7f10c4651cc3a26.sql
   │ 🌀 Uploading complete.
   │
   🌀 Starting import...
   🌀 Processed 3 queries.
   🚣 Executed 3 queries in 0.00 seconds (5 rows read, 6 rows written)
   Database is currently at bookmark 00000000-0000000a-00004f6d-b85c16a3dbcf077cb8f258b4d4eb965e.
   ┌────────────────────────┬───────────┬──────────────┬────────────────────┐
   │ Total queries executed │ Rows read │ Rows written │ Database size (MB) │
   ├────────────────────────┼───────────┼──────────────┼────────────────────┤
   │ 3                      │ 5         │ 6            │ 0.02               │
   └────────────────────────┴───────────┴──────────────┴────────────────────┘
   ```

2. 通过运行以下程序验证数据是否处于生产状态：
   
   ```cmd
   npx wrangler d1 execute prod-d1-tutorial --remote --command="SELECT * FROM Customers"
   ```
   
   ```
   ⛅️ wrangler 4.33.1
   ───────────────────
   🌀 Executing on remote database jun-d1-db-gs-2025 (cf91ec5c-fa77-4d49-ad8e-e22921b996b2):
   🌀 To execute on your local development database, remove the --remote flag from your wrangler command.
   🚣 Executed 1 command in 0.1797ms
   ┌────────────┬─────────────────────┬───────────────────┐
   │ CustomerId │ CompanyName         │ ContactName       │
   ├────────────┼─────────────────────┼───────────────────┤
   │ 1          │ Alfreds Futterkiste │ Maria Anders      │
   ├────────────┼─────────────────────┼───────────────────┤
   │ 4          │ Around the Horn     │ Thomas Hardy      │
   ├────────────┼─────────────────────┼───────────────────┤
   │ 11         │ Bs Beverages        │ Victoria Ashworth │
   ├────────────┼─────────────────────┼───────────────────┤
   │ 13         │ Bs Beverages        │ Random Name       │
   └────────────┴─────────────────────┴───────────────────┘
   ```

3. 部署你的Worker，使你的项目在互联网上可访问。运行：
   
   ```cmd
   npx wrangler deploy
   ```
   
   ```cmd
   ⛅️ wrangler 4.33.1
   ────────────────────
   Total Upload: 0.52 KiB / gzip: 0.33 KiB
   Your Worker has access to the following bindings:
   Binding                                        Resource
   env.prod_d1_tutorial (prod-d1-tutorial)        D1 Database
   Uploaded prod-d1-tutorial (4.17 sec)
   Deployed prod-d1-tutorial triggers (3.49 sec)
   https://prod-d1-tutorial.pcx-team.workers.dev
   Current Version ID: 42c82f1c-ff2b-4dce-9ea2-265adcccd0d5
   ```

你现在可以访问新创建项目的网址查询你的在线数据库。

例如，如果你的新工作者的URL是，访问会向你的工作者发送一个请求，直接查询你的实时数据库。d1-tutorial.<YOUR_SUBDOMAIN>.workers.devhttps://d1-tutorial.<YOUR_SUBDOMAIN>.workers.dev/api/beverages

4. 测试你的数据库是否运行良好。添加提供的Wrangler网址。例如， 。/api/beverageshttps://d1-tutorial.<YOUR_SUBDOMAIN>.workers.dev/api/beverages
   
   

## 6.（可选）用Wrangler本地开发

如果你用的是带Wrangler的D1，可以在本地测试数据库。在你的项目目录中：

1. 运行：`wrangler dev`

```npx
npx wrangler dev
```

运行 时，Wrangler 会提供一个 URL（很可能）用于审核你的 Worker。`wrangler dev``localhost:8787`

2. 访问网址。
   页面显示 。`Call /api/beverages to see everyone who works at Bs Beverages`

3. 测试你的数据库是否运行良好。添加提供的Wrangler网址。例如， 。`/api/beverages``localhost:8787/api/beverages`

如果成功，浏览器会显示你的数据。



## 7. （可选）删除你的数据库

要删除你的数据库：

运行：
    npx wrangler d1 delete prod-d1-tutorial

如果你想删除你的工人：

运行
    npx wrangler delete d1-tutorial
