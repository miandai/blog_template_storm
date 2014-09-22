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

抽象类BaseDbHelper中使用的是DbConnection实例，我们平时经常用的是ADO.NET的SqlConnection，该类继承自DbConnection接口。由SqlHelper.cs中SqlBulkCopyData函数中conn = (SqlConnection)GetDbConnection()可知，其实程序中调用的还是SqlConnection。

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

Connection打开调用过程：

DbHelper.cs：ExecuteReader————>BaseDbHelp.cs：Open————>DbHelperFactory.cs：IDbHelper dbHelper = (IDbHelper)Assembly.Load(DbHelperAssmely).CreateInstance(DbHelperClass, true);

创建了DataAccessTest.DbProvider.SqlHelper实例返回给IDbHelper dbHelper

调用SqlHelper：GetInstance获得SqlClientFactory.Instance;

DbProviderFactory _dbProviderFactory=SqlClientFactory.Instance;

最终相当于：
SqlClientFactory.Instance.CreateConnection().Open();

CreateConnection()返回DbConnection

调用示例：

SqlDataReader myDataReader = (SqlDataReader)(DbHelper.ExecuteReader("select * from ypxm"));


DbHelper类的成员函数：

{% highlight ruby %}
public DataBaseType CurrentDataBaseType
public static string GetDBNow()
public static string SqlSafe(string value)
public static string PlusSign(params string[] values)
public static string GetParameter(string parameter)
public static DbParameter MakeInParam(string targetFiled, object targetValue)
public static DbParameter[] MakeParameters(string targetFiled, object targetValue)
public static DbParameter[] MakeParameters(string[] targetFileds, Object[] targetValues)
public static DbParameter MakeParam(string paramName, DbType DbType, Int32 size, ParameterDirection Direction,object Value)
public static int ExecuteNonQuery(string commandText)
public static int ExecuteNonQuery(string commandText, DbParameter[] dbParameters)
public static int ExecuteNonQuery(CommandType commandType, string commandText, DbParameter[] dbParameters)
public static object ExecuteScalar(string commandText)
public static object ExecuteScalar(string commandText, DbParameter[] dbParameters)
public static object ExecuteScalar(CommandType commandType, string commandText, DbParameter[] dbParameters)
public static IDataReader ExecuteReader(string commandText)
public static IDataReader ExecuteReader(string commandText, DbParameter[] dbParameters)
public IDataReader ExecuteReader(string commandText, List<DbParameter> dbParameters)
public static IDataReader ExecuteReader(CommandType commandType, string commandText, DbParameter[] dbParameters)
public static IDataReader ExecuteReader(CommandType commandType, string commandText,
public static DataTable Fill(string commandText)
public static DataTable Fill(DataTable dataTable, string commandText)
public static DataTable Fill(string commandText, DbParameter[] dbParameters)
public static DataTable Fill(DataTable dataTable, string commandText, DbParameter[] dbParameters)
public static DataTable Fill(DataTable dataTable, CommandType commandType, string commandText, DbParameter[] dbParameters)
public static DataSet Fill(DataSet dataSet, string commandText, string tableName)
public static DataSet Fill(DataSet dataSet, string commandText, string tableName, DbParameter[] dbParameters)
public static DataSet Fill(DataSet dataSet, CommandType commandType, string commandText, string tableName, DbParameter[] dbParameters)
public static int ExecuteProcedure(string procedureName)
public static int ExecuteProcedure(string procedureName, DbParameter[] dbParameters)
public static DataTable ExecuteProcedureForDataTable(string procedureName, string tableName, DbParameter[] dbParameters)
{% endhighlight %}