---
layout: post
keywords: blog
description: blog
title: "学习笔记"
categories: [C#]
tags: [C#]
---
{% include codepiano/setup %}

《Asp.Net MVC4入门指南》

Entity Framework Code First 代码优先，如果检测到不存在一个数据库连接字符串指向了Movies数据库，会自动的创建数据库。

给电影和模型添加新字段

1、在 软件包管理器控制台 窗口中 PM> 提示符下输入" Enable-Migrations –ContextTypeName MvcMovie.Models.MovieDBContext "

2、在 Visual Studio 中打开 Configuration.cs 文件。把 Configuration.cs 文件中的 Seed 方法，替换为下面的代码……

3、在 软件包管理器控制台 窗口中，输入" add-migration Initial "命令来创建初始迁移。" Initial " 的名称是任意，是用于创建迁移文件的名称。

4、在 软件包管理器控制台 中，输入命令" update-database "

5、给现有的 Movie 类，添加新的 Rating 属性。打开 Models\Movie.cs 文件并添加如下Rating 属性……

<!--more-->

6、Build 应用程序  Build > Build Move 或 CTRL-SHIFT-B.

7、更新 \Views\Movies\Index.cshtml 和\Views\Movies\Create.cshtml 视图模板，以便能在浏览器中显示新的 Rating 属性。

8、打开 Migrations\Configuration.cs 文件，并将 Rating 字段添加到影片的每个对象。

9、Build 解决方案，然后打开  软件包管理器控制台  窗口，并输入以下命令：add-migration AddRatingMig

10、Build 解决方案，然后在 程序包管理器控制台 窗口中输入"update-database"命令。



