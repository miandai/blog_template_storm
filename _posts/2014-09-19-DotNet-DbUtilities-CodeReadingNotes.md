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

如果不采用SqlParameter，那么当输入的Sql语句出现歧义时，如字符串中含有单引号，程序就会发生错误，并且他人可以轻易地通过拼接Sql语句来进行注入攻击。我们常规的sql操作语句当中一般会是这样
{% highlight ruby %}
string StuName=TextBox1.Text;
string str="select * from StudentInfo where stuName='"+StuName+"'";
{% endhighlight %}
StuId是别人来输入的。假设我输入的格式为StuName是 ' and stuid='12
那么连起来 str=select * from StudentInfo where stuName='' and stuid='12'

再如：
{% highlight ruby %}
//未采用SqlParameter
stirng nn="aa or 1=1"; //加" or 1=1" 还算好的，如果加上";delete from tb"就可能把数据库表删除了
string sql = "select * from tb where t1='" + nn + "'"; //"select * from tb where t1='aa' or 1=1"，这就是典型的登录注入


//采用SqlParameter
"select * from tb where t1=@N";
cmd.Parameters.Add(new SqlParameter(@N,aa or 1=1)); // select * from tb where t1='aa' or 1=1'
{% endhighlight %}

除了存在安全性问题，该方法还无法解决二进制流的更新，如图片文件，
请参考：http://www.cnblogs.com/pioneerlc/archive/2011/05/21/2053052.html
.NET防SQL注入方法：http://www.cnblogs.com/smhy8187/articles/824071.html，
http://blog.csdn.net/stilling2006/article/details/8526458