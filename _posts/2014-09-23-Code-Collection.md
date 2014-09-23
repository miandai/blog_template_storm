---
layout: post
keywords: blog
description: blog
title: "代码收集"
categories: [.net]
tags: [.net]
---
{% include codepiano/setup %}

{% highlight ruby %}
List<DbParameter> list = new List<DbParameter>();
list.AddRange(new DbParameter[]{new SqlParameter("@commonname", "注射用青霉素钠"),new SqlParameter("@dose","普通粉针")});
myDataReader = (SqlDataReader)(DbHelper.ExecuteReader(CommandType.Text,sql, list));
{% endhighlight %}

<!--more-->

{% highlight ruby %}
using System;
using System.Data;
using System.Data.SqlClient;

namespace His_WestChina
{
    public class DataBase
    {
        //定义一个公用成员
        //public SqlConnection _conn;

        private SqlConnection _conn1;
        private SqlCommand _cmd;

        public DataBase()
        {
            _cmd = new SqlCommand();
        }
        private void SetCmd(string sql,SqlConnection conn)
        {
            _cmd.CommandText = sql;
            _cmd.Connection = conn;
        }

        public SqlConnection Conn1 
        {
            get { return _conn1; }
            set { _conn1 = value; }
        }

        //建立数据库连接
        public void OpenConn()
        {
            
            String strconn = System.Configuration.ConfigurationManager.AppSettings["sqlconn"].ToString();
            Conn1 = new SqlConnection(strconn);
            if (Conn1.State != ConnectionState.Open)
            {
                //连接为关闭时
                Conn1.Open();
            }
        }

        //关闭并释放连接
        public void CloseConn()
        {
            if (Conn1.State == ConnectionState.Open)
            {
                //连接为打开时
                Conn1.Close();
                Conn1.Dispose();//释放资源
            }
        }

        //返回DataReader，用于读取数据
        public SqlDataReader ReturnDataReader(string sql)
        {
            OpenConn();
            SetCmd(sql, Conn1);
            SqlDataReader dr = _cmd.ExecuteReader();
            return dr;
        }

        //返回一个数据集
        public DataSet ReturnDataSet(string Sql, string tableName="tb")
        {
            OpenConn();
            SqlDataAdapter dap;
            DataSet ds = new DataSet();
            dap = new SqlDataAdapter(Sql, Conn1);
            dap.Fill(ds, tableName);
            CloseConn();
            return ds;
        }

        //执行一个SQL操作:添加、删除、更新操作，返回受影响的行
        public int ExecuteNonQueryDb(string sql)
        {
            OpenConn();
            SetCmd(sql, Conn1);
            int result = _cmd.ExecuteNonQuery();
            CloseConn();
            return result;
        }

        // 返回一个数据集的记录数
        public int ReturnExecuteScalar(string sql)
        {
            //注：Sql 语句必须是一个统计查询
            OpenConn();
            SetCmd(sql, Conn1);
            int result=Convert.ToInt32(_cmd.ExecuteScalar());
            CloseConn();
            return result;
        }
    }
}
{% endhighlight %}

<!--more-->


