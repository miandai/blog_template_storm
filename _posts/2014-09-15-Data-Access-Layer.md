---
layout: post
keywords: blog
description: blog
title: "访问数据库接口封装"
categories: [.net]
tags: [.net]
---
{% include codepiano/setup %}

1、事务脚本模式
2、表模块模式
3、活动记录模式
4、领域模型模式

仅在业务逻辑使用领域模型模式组织时，数据访问层才可以设计成一个独立的软件模块。若使用非领域模型的模式来设计业务逻辑，哪怕是活动记录，那么持久化都会内建在业务逻辑中，数据访问层实际上也和业务层混在了一起。这时的数据访问层实际上就是一系列存储过程和SQL语句的集合。

ADO.NET是一组向.NET程序员公开数据访问服务的类。数据客户端应用程序可以使用ADO.NET来连接到这些数据源，并查询、添加、删除和更新所包含的数据。

一、Connection对象是一个连接对象，主要功能是建立与物理数据库的连接。其主要包括4种访问数据库的对象类，分别为：

1、SQL Server数据提供程序，位于System.Data.SqlClient命名空间

2、ODBC数据提供程序，位于System.Data.Odbc命名空间，ODBC是为访问关系型数据库而专门开发的

3、OLEDB数据提供程序，位于System.Data.OleDb命名空间，OLE DB则用于访问关系型和非关系型信息源

4、Oracle数据提供程序，位于System.Data.OracleClient命名空间

<!--more-->

步骤：

1、通过using System.Data.SqlClient命令引用命名空间

2、连接数据库

3、调用SqlConnection对象的Open方法打开数据库

对数据库操作完毕后，要关闭与数据库的连接，释放占用的资源。

Close方法：关闭一个连接，可以再调用Open方法打开连接，不会产生任何错误。

Dispose方法：不仅关闭一个连接，还清理连接所占用的资源，不能再直接用Open方法打开连接。必须再重新初始化连接再打开。

二、Command对象是一个数据命令对象，主要功能是向数据库发送查询、更新、删除、修改操作的SQL语句。有以下几种方式：SqlCommand、OleDbCommand、OdbcCommand、OracleCommand。

Command对象有3个重要的属性，分别是Connection、CommandText和CommandType。

Connection用于设置使用的SqlConnection。

CommandText用于设置要对数据源执行的SQL语句或存储过程。

CommandType枚举有3个枚举成员：StoreProcedure、TableDirect、Text。

SqlCommand对象中的几种执行SQL语句的方法：

1、ExecuteNonQuery，向数据库发送增、删、改命令，返回受影响的个数。

2、ExecuteReader，执行SQL语句，返回一个包含数据的SqlDataReader实例。

3、ExecuteScalar，执行SQL语句，返回结果集中的第一行的第一列。

三、DataReader对象是数据读取器对象，提供只读向前的游标。

1、HasRows，判断查询结果中是否有值

2、Read()，读取数据

DataSet与DataReader

当设计应用程序时，要考虑应用程序所需功能的等级，以确定使用DataSet或者是DataReader。

当通过应用程序执行以下操作，就要使用DataSet：

1、在结果的多个离散表之间进行导航

2、操作来自多个数据源的数据

3、在各层之间交换数据或使用XML WEB服务。与DataReader不同的是，DataSet能传递给远程客户端。

4、重用同样的记录集合，以便通过缓存获得性能改善（例如排序、搜索或筛选数据）

5、每条记录都需要执行大量处理。对使用DataReader返回的每一行进行处理会延长服务于DataReader的连接的必要时间，这影响了性能

对于下列情况，要在应用程序中使用DataReader：

1、不需要缓存数据

2、要处理的结果集太大，内存中放不下

3、一旦需要以仅向前、只读方式快速访问数据



