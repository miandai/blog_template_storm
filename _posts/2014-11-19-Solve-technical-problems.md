---
layout: post
keywords: blog
description: blog
title: "开发代码"
categories: [.net]
tags: [.net]
---
{% include codepiano/setup %}

解决的技术难题：
1、GridView转DataTable
{% highlight ruby %}
public static DataTable GetDataTableFromGridView(GridView gv, DataTable dt)
{
	GridViewRow headerRow = gv.HeaderRow;
	int columnCount = headerRow.Cells.Count;
	for (int i = 0; i < columnCount; i++)
	{
		DataColumn dc = dt.Columns.Add();
		dc.ColumnName = headerRow.Cells[i].Text.ToString().Trim();
		dc.DataType = typeof(string);
	}

	for (int i = 0; i < gv.Rows.Count; i++)
	{
		DataRow dr = dt.NewRow();

		for (int j = 0; j < gv.Columns.Count; j++)
		{
			if (gv.Rows[i].Cells[j].Text.Trim() == "&nbsp;")
			{
				dr[j] = "";
			}
			else
			{
				dr[j] = gv.Rows[i].Cells[j].Text.Trim();
			}
		}

		dt.Rows.Add(dr);
	}
	return dt;
}
{% endhighlight %}
<!--more-->

2、Javascript获取和设置ASP.NET控件的值，可用于母版页（这点代码也花了半天时间，⊙﹏⊙b汗）
{% highlight ruby %}
<script type="text/javascript" >
	function Print_Click() {
		var a = document.getElementById('<%=TotalLabel.ClientID%>').innerHTML;
		var b = document.getElementById('<%=ReceiveTextBox.ClientID%>').value;
		document.getElementById('<%=ReturnCashLabel.ClientID%>').innerHTML = b-a;
	}
</script>

结算费用：<asp:Label ID="TotalLabel" runat="server" Text="0"></asp:Label>
收取现金：<asp:TextBox ID="ReceiveTextBox" runat="server" OnClick="Print_Click()" Width="46px"></asp:TextBox>
应找现金：<asp:Label ID="ReturnCashLabel" runat="server" Text="0"></asp:Label>
{% endhighlight %}

3、javascript数据验证，js代码写在母版页中，改变内容页的控件内容
{% highlight ruby %}
//母版页js代码
<script type="text/javascript" >
	function CardNumCheck(cardNumCtr, num) {
		var patCard = cardNumCtr.value;
		var patCardlength = patCard.toString().length;
		var i;
		if (patCardlength != 0 && patCardlength < num) {
			for (i = patCardlength; i < num; i++) {
				patCard = "0" + patCard;
			}
		}
		cardNumCtr.value = patCard;
	}
</script>

//内容页控件代码
<asp:TextBox ID="PatientCardNum" runat="server" TabIndex="1" onblur="CardNumCheck(this,12)"></asp:TextBox>
{% endhighlight %}