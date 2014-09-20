---
layout: post
keywords: blog
description: blog
title: "DotNet.DbUtilities阅读笔记"
categories: [.net]
tags: [.net]
---
{% include codepiano/setup %}

DbProvider文件夹：不同的数据库，需要有不同的实现方式，例如获得当前日期时间，每种数据库都不一样，所以你需要一个能自定义数据库函数功能模块，可以任意扩展。

DbHelper.cs：这个是轻量级别的数据库访问类，数据库的打开关闭等不用管了，想执行一个SQL语句什么的，直接调用一下DbHelper.ExecuteNonQuery(sqlQuery)一行代码就可以了，写程序很快，简单快捷。

DbHelperFactory.cs：这个是个数据库访问工厂，就是负责调用数据库组件的，它会通过读取配置文件，自动调用相应的数据库访问组件。比如本程序调用的是DataAccessTest.DbProvider.SqlHelper。

<!--more-->

抽象类BaseDbHelper中使用的是DbConnection实例，我们平时经常用的是ADO.NET的SqlConnection，该类继承自DbConnection接口。



{% highlight ruby %}
namespace System.Data.Common
{
	public sealed class SqlConnection : DbConnection, ICloneable
	{
		protected DbConnection();
		}
	}
}
{% endhighlight %}

DbConnection继承了IDBConnection接口。

