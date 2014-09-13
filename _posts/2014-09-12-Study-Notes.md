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

《Microsoft .NET企业级应用架构设计》

为什么要完全手工编写数据访问层呢？是因为某个严格的功能性需求不允许使用O/RM，还是因为你觉得手工编写的数据访问层会比任何商业的O/RM工具还要强大？

我们已经知道数据访问层的4种主要职责：持久化、查询、管理事务和维护并发，至少后两个职责可以委托给专门的工具来完成。在我们的查询服务实现中，还使用了一个非常通用的接口，即查询对象模式，该模式也可以由专门的工具直接提供。唯一有意义的手工编写代码部分可能就是数据持久化了，不过O/RM工具内建的功能也可以完成这些任务。

O/RM工具的主要目标是解决对象模型和存储层之间的对象-关系阻抗失调问题。

虽然市面上存在着一些面向对象数据库，但是我们从未有幸在任何一个客户的应用中见过它。

在不完美的现实世界中，我们仅能使用关系型数据库，因为O/R映射层就会变得必不可少。在决定如何表示领域逻辑时，决定就已经做出了。也就是说，若是你选择了面向对象的方式（特别是领域模式），自然会用到O/R映射层。

O/RM工具将把对象模型转换成关系型数据库可以存储的格式，同时，O/RM工具也提供了从数据库中获取同样的数据并将其转换成对象图的功能。

O/RM工具：Entity Framework、EntitySpaces、Genome、LINQ-to-SQL、LLBLGen Pro、NHibernate




