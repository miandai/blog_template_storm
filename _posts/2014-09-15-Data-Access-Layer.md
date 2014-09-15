---
layout: post
keywords: blog
description: blog
title: "数据访问层"
categories: [.net]
tags: [.net]
---
{% include codepiano/setup %}

ADO.NET是一组向.NET程序员公开数据访问服务的类。数据客户端应用程序可以使用ADO.NET来连接到这些数据源，并查询、添加、删除和更新所包含的数据。

一、Connection对象是一个连接对象，主要功能是建立与物理数据库的连接。其主要包括4种访问数据库的对象类，分别为：

1、SQL Server数据提供程序，位于System.Data.SqlClient命名空间

2、ODBC数据提供程序，位于System.Data.Odbc命名空间，ODBC是为访问关系型数据库而专门开发的

3、OLEDB数据提供程序，位于System.Data.OleDb命名空间，OLE DB则用于访问关系型和非关系型信息源

4、Oracle数据提供程序，位于System.Data.OracleClient命名空间

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

<!--more-->



