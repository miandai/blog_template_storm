---
layout: post
keywords: blog
description: blog
title: "C#命名规范"
categories: [.net]
tags: [.net]
---
{% include codepiano/setup %}

1、命名方法和类型，第一个字母必须大写，并且后面的连接词的第一个字母均为大写。[Pascal]

{% highlight ruby %}
public class DataGrid			//创建一个公共类
{
	public void DataBind()		//在公共类中创建一个公共方法
	{
	}
}
{% endhighlight %}

2、命名局部变量和方法的参数，第一单词的第一个字母小写。[Camel]

<!--more-->

{% highlight ruby %}
string strUserName;											//声明一个字符串变量strUserName
public void AddUser(string strUserId,byte[] byPassword)	//创建一个具有两个参数的公共方法
{% endhighlight %}

3、所有的成员变量前加前缀“_”。

{% highlight ruby %}
public class DataBase					//创建一个公共类
{
	private string _connectionString;	//声明一个私有成员变量
}
{% endhighlight %}

4、接口的名称前加前缀“I”

{% highlight ruby %}
public interface Iconvertible
{
	byte ToByte();
}
{% endhighlight %}

5、方法的命名，一般将其命名为动宾短语

{% highlight ruby %}
public class File
{
	public void CreatePath(string filePath)
	{
	}
}
{% endhighlight %}

6、所有的成员变量声明在类的顶端，用一个换行把它和方法分开。

{% highlight ruby %}
public class Product
{
	private string _productId;
	public string _productName;
	
	public void AddProduct(string productId,string productName)
	{
	}
}
{% endhighlight %}

7、用有意义的名字命名命名空间，如公司名、产品名。

{% highlight ruby %}
namespace ERP
{
}
{% endhighlight %}

8、创建一个方法，在方法中声明一个字符串变量title，使其等于Label空间的Text值。

{% highlight ruby %}
public string GetTitle()
{
	string title = lbl_Title.Text;
	return title;
}
{% endhighlight %}


9、综合示例如下：

{% highlight ruby %}

namespace His_WestChina						//用有意义的名字命名命名空间，如公司名、产品名。
{
	public class DataBase					//类命名，第一个字母必须大写，并且后面的连接词的第一个字母均为大写
	{
		public string _connectionString;	//成员变量前加前缀“_”
	
		//所有的成员变量声明在类的顶端，用一个换行把它和方法分开。
		public void AddUser(string strUserId,byte[] byPassword)	//方法命名第一个字母必须大写，后面的连接词的第一个字母均为大写；方法的参数，第一单词的第一个字母小写；方法的命名，一般将其命名为动宾短语
		{
			string strUserName;									//局部变量，第一个单次的第一个字母小写
		}
	}
}
{% endhighlight %}


