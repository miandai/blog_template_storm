---
layout: post
keywords: blog
description: blog
title: "Pandas 练习题"
categories: [Data_Analysis]
tags: [Data_Analysis]
---
{% include codepiano/setup %}

**1. 使用 pandas 中的函数，下载上证综指过去一段时间的数据，进行数据探索。**

上证综指，全称是上海证券综合指数，是以上证所挂牌上市的全部股票为计算范围，以发行量为权数的加权综合股价指数。这一指数自1991年7月15日起开始实时发布，基日定为1990年12月19日，基日指数定为100点。

以上证综指等为核心的上证指数体系，科学表征上海证券市场层次丰富、行业广泛、品种拓展的市场结构和变化特征，便于市场参与者的多维度分析，增强样本企业知名度，引导市场资金的合理配置。

因为上综指包括**全部**上海证券交易所的股票，而且它编制时间很长，所以在股民中影响较大。大盘一般就是指上证综合指数。


<!--more-->

Pandas 提供了[远程访问财经数据](http://pandas.pydata.org/pandas-docs/stable/remote_data.html)的多个网络源接口，包括：

- Yahoo! Finance
- Google Finance
- St.Louis FED (FRED)
- Kenneth French’s data library
- World Bank
- Google Analytics

这里我们选择雅虎财经。先载入要用到的包。


```python
!pip install pandas_datareader
```


```python
%matplotlib inline
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
from datetime import datetime
from pandas_datareader import data, wb # 需要先安装 pandas_datareader
```

**数据获取**

下载上证综指 2015 年 1 月 1 日到 2016 年 5 月 27 日的数据。沪市股票为「股票代码.ss」，深市股票为「股票代码.sz」。如上证指数为「000001.ss」，深证指数为「399001.sz」。


```python
start = datetime(2015, 1, 1)
end = datetime(2016, 5, 27)
sc = data.DataReader("000001.SS", 'yahoo', start, end) # 000001.SS 表示上证综指，返回 DataFrame
sc.head() # 纵轴是日期，横轴是开盘价、最高价、最低价、收盘价、成交量、复权收盘价。因上证综指并非具体某支股票，所以交易量为 0。
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Volume</th>
      <th>Adj Close</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-05</th>
      <td>3350.52</td>
      <td>3350.52</td>
      <td>3350.52</td>
      <td>3350.52</td>
      <td>0</td>
      <td>3350.52</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>3351.45</td>
      <td>3351.45</td>
      <td>3351.45</td>
      <td>3351.45</td>
      <td>0</td>
      <td>3351.45</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>3373.95</td>
      <td>3373.95</td>
      <td>3373.95</td>
      <td>3373.95</td>
      <td>0</td>
      <td>3373.95</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>3293.46</td>
      <td>3293.46</td>
      <td>3293.46</td>
      <td>3293.46</td>
      <td>0</td>
      <td>3293.46</td>
    </tr>
    <tr>
      <th>2015-01-09</th>
      <td>3285.41</td>
      <td>3285.41</td>
      <td>3285.41</td>
      <td>3285.41</td>
      <td>0</td>
      <td>3285.41</td>
    </tr>
  </tbody>
</table>
</div>




```python
sc.info() # 数据质量很好，没有空值
```

    <class 'pandas.core.frame.DataFrame'>
    DatetimeIndex: 326 entries, 2015-01-05 to 2016-05-27
    Data columns (total 6 columns):
    Open         326 non-null float64
    High         326 non-null float64
    Low          326 non-null float64
    Close        326 non-null float64
    Volume       326 non-null int64
    Adj Close    326 non-null float64
    dtypes: float64(5), int64(1)
    memory usage: 17.8 KB



```python
sc.describe() # 数据概览，共有 326 个上证综指的股价数据
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Volume</th>
      <th>Adj Close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>326.000000</td>
      <td>326.000000</td>
      <td>326.000000</td>
      <td>326.000000</td>
      <td>326.0</td>
      <td>326.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>3505.384049</td>
      <td>3505.384049</td>
      <td>3505.384049</td>
      <td>3505.384049</td>
      <td>0.0</td>
      <td>3505.384049</td>
    </tr>
    <tr>
      <th>std</th>
      <td>591.216168</td>
      <td>591.216168</td>
      <td>591.216168</td>
      <td>591.216168</td>
      <td>0.0</td>
      <td>591.216168</td>
    </tr>
    <tr>
      <th>min</th>
      <td>2655.660000</td>
      <td>2655.660000</td>
      <td>2655.660000</td>
      <td>2655.660000</td>
      <td>0.0</td>
      <td>2655.660000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>3019.815000</td>
      <td>3019.815000</td>
      <td>3019.815000</td>
      <td>3019.815000</td>
      <td>0.0</td>
      <td>3019.815000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>3365.290000</td>
      <td>3365.290000</td>
      <td>3365.290000</td>
      <td>3365.290000</td>
      <td>0.0</td>
      <td>3365.290000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>3792.875000</td>
      <td>3792.875000</td>
      <td>3792.875000</td>
      <td>3792.875000</td>
      <td>0.0</td>
      <td>3792.875000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>5166.350000</td>
      <td>5166.350000</td>
      <td>5166.350000</td>
      <td>5166.350000</td>
      <td>0.0</td>
      <td>5166.350000</td>
    </tr>
  </tbody>
</table>
</div>



如果觉得在线获取数据的方式不保险，也可直接在浏览器地址栏输入网址下载 csv 文件，如 http://table.finance.yahoo.com/table.csv?s=000001.SS ，然后再读取到 DataFrame，设置 Date 为 index，并过滤，一样的效果。


```python
sc1 = pd.read_csv('000001ss.csv') # 000001ss.csv 包含了上证综指从 1990-12-19 至今的数据
sc1 = sc1.set_index("Date") # 用 Date 来作为索引
sc1 = sc1.sort_index() # 根据 Date 来排序
sc1 = sc1["2015-01-01":"2016-05-27"] # 根据日期过滤
print(sc1.head())
print(sc1.describe()) # 可以看到跟上面在线读取的 sc 效果等同
```

                   Open     High      Low    Close  Volume  Adj Close
    Date                                                             
    2015-01-05  3350.52  3350.52  3350.52  3350.52       0    3350.52
    2015-01-06  3351.45  3351.45  3351.45  3351.45       0    3351.45
    2015-01-07  3373.95  3373.95  3373.95  3373.95       0    3373.95
    2015-01-08  3293.46  3293.46  3293.46  3293.46       0    3293.46
    2015-01-09  3285.41  3285.41  3285.41  3285.41       0    3285.41
                  Open         High          Low        Close  Volume    Adj Close
    count   326.000000   326.000000   326.000000   326.000000   326.0   326.000000
    mean   3505.384049  3505.384049  3505.384049  3505.384049     0.0  3505.384049
    std     591.216168   591.216168   591.216168   591.216168     0.0   591.216168
    min    2655.660000  2655.660000  2655.660000  2655.660000     0.0  2655.660000
    25%    3019.815000  3019.815000  3019.815000  3019.815000     0.0  3019.815000
    50%    3365.290000  3365.290000  3365.290000  3365.290000     0.0  3365.290000
    75%    3792.875000  3792.875000  3792.875000  3792.875000     0.0  3792.875000
    max    5166.350000  5166.350000  5166.350000  5166.350000     0.0  5166.350000


**数据清理**

上证综指并非具体股票，所以交易量 Volume 为 0，在对上证综指的分析中，该字段无效，所以先删除该数据。


```python
sc = sc.drop('Volume',axis=1)
sc.head(2)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-05</th>
      <td>3350.52</td>
      <td>3350.52</td>
      <td>3350.52</td>
      <td>3350.52</td>
      <td>3350.52</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>3351.45</td>
      <td>3351.45</td>
      <td>3351.45</td>
      <td>3351.45</td>
      <td>3351.45</td>
    </tr>
  </tbody>
</table>
</div>



**数据探索**

绘图，看下上证综指这一年多的变化。


```python
fig, ax = plt.subplots(1, 1, figsize=(12, 4))
sc.plot(ax=ax) # sc 有 5 列数据，自动使用 5 种不同的颜色画 5 条 line
fig.tight_layout();
```


![](/image/data-analysis/586070-20160530180547258-1939879864.png)




从上图的大盘走势可看出，股市在去年上半年的一连串上涨，及下半年的一连串暴跌，股市低迷持续至今。相信炒股的朋友对去年的股市印象深刻。

**计算涨跌额**，涨跌额是指当日股票价格与前一日收盘价格相比的涨跌数值。


```python
change = sc.Close.diff()
change.iloc[0] = 0
sc['Change'] = change
sc.head(3)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Change</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-05</th>
      <td>3350.52</td>
      <td>3350.52</td>
      <td>3350.52</td>
      <td>3350.52</td>
      <td>3350.52</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>3351.45</td>
      <td>3351.45</td>
      <td>3351.45</td>
      <td>3351.45</td>
      <td>3351.45</td>
      <td>0.93</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>3373.95</td>
      <td>3373.95</td>
      <td>3373.95</td>
      <td>3373.95</td>
      <td>3373.95</td>
      <td>22.50</td>
    </tr>
  </tbody>
</table>
</div>




```python
pd.set_option('display.mpl_style', 'default')
fig, ax = plt.subplots(1, 1, figsize=(12, 4))
sc.Change.plot(ax=ax, kind='line', title='Rise and fall');

```


![](/image/data-analysis/586070-20160530180600571-582227727.png)




可以看出，上证综指在 6 月到 9 月间的剧烈波动。

再来看下上证综指随月份的变化。


```python
sc_month = sc.Close.to_period("M").groupby(level=0).mean()
sc_month.head()
```




    Date
    2015-01    3293.873000
    2015-02    3186.547333
    2015-03    3483.941364
    2015-04    4186.230952
    2015-05    4467.845000
    Freq: M, Name: Close, dtype: float64




```python
pd.set_option('display.mpl_style', 'default')
fig, ax = plt.subplots(1, 1, figsize=(12, 4))
sc_month.plot(ax=ax, kind='bar', title='Rise and fall');
```


![](/image/data-analysis/586070-20160530180612508-415871577.png)




** 2. 将[中国地震台网最近一周的地震数据](http://data.earthquake.cn/datashare/globeEarthquake_csn.html)抓取下来，分析你感兴趣的分析点。 **

Pandas 提供了 [pandas.read_html](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_html.html#pandas.read_html) 函数会读取 HTML tables 到 DataFrame 对象的 list 中。

使用 pandas.read_html 抓取数据之前，需要先安装 html5lib、lxml 和 beautifulsoup。前两个使用 pip install 安装即可，而 beautifulsoup 的安装如 [@lyltj2010](https://github.com/lyltj2010) 同学所说需要指定版本：pip install beautifulsoup4==4.0.5，如果已经安装了其它版本，先 pip uninstall beautifulsoup4 卸载再重新安装。

**抓取数据**


```python
import pandas as pd

url = 'http://data.earthquake.cn/datashare/globeEarthquake_csn.html'
eqs = pd.read_html(io=url,header=0,encoding='gb2312') # encoding 是通过查看网页源代码中的 charset 值得到
eqs[4].head() # 该页面有 4 个大 table，但只有第 4 个才是我们需要的地震数据表格
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>发震日期</th>
      <th>发震时刻</th>
      <th>纬度(°)</th>
      <th>经度(°)</th>
      <th>深度(km)</th>
      <th>震级</th>
      <th>事件类型</th>
      <th>参考地点</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2016-05-29</td>
      <td>10:14:37.7</td>
      <td>31.5</td>
      <td>104.3</td>
      <td>13</td>
      <td>Ms4.3</td>
      <td>天然地震</td>
      <td>四川绵阳市安县</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2016-05-28</td>
      <td>17:47:00.9</td>
      <td>-56.2</td>
      <td>-27.1</td>
      <td>70</td>
      <td>Ms7.2</td>
      <td>天然地震</td>
      <td>南桑威奇群岛地区</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2016-05-28</td>
      <td>17:39:03.8</td>
      <td>27.5</td>
      <td>85.1</td>
      <td>8</td>
      <td>Ms4.2</td>
      <td>天然地震</td>
      <td>尼泊尔</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2016-05-28</td>
      <td>13:38:49.0</td>
      <td>-22.0</td>
      <td>-178.2</td>
      <td>400</td>
      <td>Ms6.5</td>
      <td>天然地震</td>
      <td>斐济群岛地区</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2016-05-28</td>
      <td>03:08:14.1</td>
      <td>36.3</td>
      <td>78.0</td>
      <td>91</td>
      <td>Ms3.2</td>
      <td>天然地震</td>
      <td>新疆和田地区皮山县</td>
    </tr>
  </tbody>
</table>
</div>



上面抓取数据的代码很简洁，但需要查看网页源代码才能得到编码的做法使得代码不够通用，毕竟换个页面又要去查看页面 charset，[@lyltj2010](https://github.com/lyltj2010) 同学提供的代码就很通用，非常值得学习。


```python
url = 'http://data.earthquake.cn/datashare/globeEarthquake_csn.html'
html = requests.get(url)
html.encoding =  html.apparent_encoding
html_text = html.text
dfs = pd.read_html(html_text,header=0) # 返回的是一个 list,list里是表格
dfs[4].head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>发震日期</th>
      <th>发震时刻</th>
      <th>纬度(°)</th>
      <th>经度(°)</th>
      <th>深度(km)</th>
      <th>震级</th>
      <th>事件类型</th>
      <th>参考地点</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2016-05-28</td>
      <td>17:47:00.9</td>
      <td>-56.20</td>
      <td>-27.10</td>
      <td>70</td>
      <td>Ms7.2</td>
      <td>天然地震</td>
      <td>南桑威奇群岛地区</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2016-05-28</td>
      <td>17:39:03.8</td>
      <td>27.50</td>
      <td>85.10</td>
      <td>8</td>
      <td>Ms4.2</td>
      <td>天然地震</td>
      <td>尼泊尔</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2016-05-28</td>
      <td>13:38:49.0</td>
      <td>-22.00</td>
      <td>-178.20</td>
      <td>400</td>
      <td>Ms6.5</td>
      <td>天然地震</td>
      <td>斐济群岛地区</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2016-05-28</td>
      <td>03:08:14.1</td>
      <td>36.30</td>
      <td>78.00</td>
      <td>91</td>
      <td>Ms3.2</td>
      <td>天然地震</td>
      <td>新疆和田地区皮山县</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2016-05-27</td>
      <td>23:06:33.2</td>
      <td>39.85</td>
      <td>98.82</td>
      <td>15</td>
      <td>ML1.2</td>
      <td>天然地震</td>
      <td>甘肃酒泉</td>
    </tr>
  </tbody>
</table>
</div>




```python
eq = eqs[4]
type(eq) # 地震数据表格转成了一个 DataFrame
```




    pandas.core.frame.DataFrame



**数据整理**


```python
eq.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 932 entries, 0 to 931
    Data columns (total 8 columns):
    发震日期      932 non-null object
    发震时刻      932 non-null object
    纬度(°)     932 non-null float64
    经度(°)     932 non-null float64
    深度(km)    932 non-null int64
    震级        932 non-null object
    事件类型      932 non-null object
    参考地点      932 non-null object
    dtypes: float64(2), int64(1), object(5)
    memory usage: 58.3+ KB


可以看到，数据比较干净，没有 null 数据。为方便操作，先修改列名为英文单词。


```python
eq.columns=['Date','Time','Latitude','Longitude','Depth','Magnitude','EventType','Place'] # 修改列名
eq.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Time</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Depth</th>
      <th>Magnitude</th>
      <th>EventType</th>
      <th>Place</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2016-05-29</td>
      <td>10:14:37.7</td>
      <td>31.5</td>
      <td>104.3</td>
      <td>13</td>
      <td>Ms4.3</td>
      <td>天然地震</td>
      <td>四川绵阳市安县</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2016-05-28</td>
      <td>17:47:00.9</td>
      <td>-56.2</td>
      <td>-27.1</td>
      <td>70</td>
      <td>Ms7.2</td>
      <td>天然地震</td>
      <td>南桑威奇群岛地区</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2016-05-28</td>
      <td>17:39:03.8</td>
      <td>27.5</td>
      <td>85.1</td>
      <td>8</td>
      <td>Ms4.2</td>
      <td>天然地震</td>
      <td>尼泊尔</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2016-05-28</td>
      <td>13:38:49.0</td>
      <td>-22.0</td>
      <td>-178.2</td>
      <td>400</td>
      <td>Ms6.5</td>
      <td>天然地震</td>
      <td>斐济群岛地区</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2016-05-28</td>
      <td>03:08:14.1</td>
      <td>36.3</td>
      <td>78.0</td>
      <td>91</td>
      <td>Ms3.2</td>
      <td>天然地震</td>
      <td>新疆和田地区皮山县</td>
    </tr>
  </tbody>
</table>
</div>



把发震日期和发震时刻合并转成 datetime 格式，并设为 index。


```python
# 清理时间数据，把时间转换成datetime64认可的形式
eq['Time'] = eq['Time'].map(lambda x : x[0:8])
eq['Datetime'] = eq['Date']+' '+eq['Time']
eq.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Time</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Depth</th>
      <th>Magnitude</th>
      <th>EventType</th>
      <th>Place</th>
      <th>Datetime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2016-05-29</td>
      <td>10:14:37</td>
      <td>31.5</td>
      <td>104.3</td>
      <td>13</td>
      <td>Ms4.3</td>
      <td>天然地震</td>
      <td>四川绵阳市安县</td>
      <td>2016-05-29 10:14:37</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2016-05-28</td>
      <td>17:47:00</td>
      <td>-56.2</td>
      <td>-27.1</td>
      <td>70</td>
      <td>Ms7.2</td>
      <td>天然地震</td>
      <td>南桑威奇群岛地区</td>
      <td>2016-05-28 17:47:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2016-05-28</td>
      <td>17:39:03</td>
      <td>27.5</td>
      <td>85.1</td>
      <td>8</td>
      <td>Ms4.2</td>
      <td>天然地震</td>
      <td>尼泊尔</td>
      <td>2016-05-28 17:39:03</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2016-05-28</td>
      <td>13:38:49</td>
      <td>-22.0</td>
      <td>-178.2</td>
      <td>400</td>
      <td>Ms6.5</td>
      <td>天然地震</td>
      <td>斐济群岛地区</td>
      <td>2016-05-28 13:38:49</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2016-05-28</td>
      <td>03:08:14</td>
      <td>36.3</td>
      <td>78.0</td>
      <td>91</td>
      <td>Ms3.2</td>
      <td>天然地震</td>
      <td>新疆和田地区皮山县</td>
      <td>2016-05-28 03:08:14</td>
    </tr>
  </tbody>
</table>
</div>




```python
eq.Datetime = pd.to_datetime(eq.Datetime) # to_datetime 方法可以解析多种不同的日期表示形式
eq.Datetime[0] # 转换成功
```




    Timestamp('2016-05-29 10:14:37')




```python
eq = eq.drop('Date',axis=1) # 可以去除掉 Date 和 Time 列了
eq = eq.drop('Time',axis=1)
eq = eq.set_index("Datetime") # 设置 Datetime 为 index
eq = eq.sort_index() # 根据 Datetime 来排序
eq.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Depth</th>
      <th>Magnitude</th>
      <th>EventType</th>
      <th>Place</th>
    </tr>
    <tr>
      <th>Datetime</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-05-23 00:24:16</th>
      <td>27.12</td>
      <td>102.96</td>
      <td>6</td>
      <td>ML2.1</td>
      <td>天然地震</td>
      <td>云南巧家</td>
    </tr>
    <tr>
      <th>2016-05-23 00:43:46</th>
      <td>35.69</td>
      <td>111.40</td>
      <td>13</td>
      <td>ML1.5</td>
      <td>天然地震</td>
      <td>山西侯马</td>
    </tr>
    <tr>
      <th>2016-05-23 00:48:15</th>
      <td>30.35</td>
      <td>102.93</td>
      <td>14</td>
      <td>ML1.0</td>
      <td>天然地震</td>
      <td>四川宝兴</td>
    </tr>
    <tr>
      <th>2016-05-23 00:50:55</th>
      <td>26.08</td>
      <td>99.56</td>
      <td>10</td>
      <td>ML0.6</td>
      <td>天然地震</td>
      <td>云南洱源</td>
    </tr>
    <tr>
      <th>2016-05-23 01:11:00</th>
      <td>23.24</td>
      <td>117.27</td>
      <td>13</td>
      <td>ML1.0</td>
      <td>天然地震</td>
      <td>广东南澳海域</td>
    </tr>
  </tbody>
</table>
</div>



参考地点 Place 的具体地址并不重要，经纬度已经能够给出详细信息，不过 Place 的省份信息可以用来统计最近一周各省的地震频次。所以这里对 Place 字段做下处理。


```python
eq['Place'] = eq['Place'].map(lambda x : x[0:2])
eq.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Depth</th>
      <th>Magnitude</th>
      <th>EventType</th>
      <th>Place</th>
    </tr>
    <tr>
      <th>Datetime</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-05-23 00:24:16</th>
      <td>27.12</td>
      <td>102.96</td>
      <td>6</td>
      <td>ML2.1</td>
      <td>天然地震</td>
      <td>云南</td>
    </tr>
    <tr>
      <th>2016-05-23 00:43:46</th>
      <td>35.69</td>
      <td>111.40</td>
      <td>13</td>
      <td>ML1.5</td>
      <td>天然地震</td>
      <td>山西</td>
    </tr>
    <tr>
      <th>2016-05-23 00:48:15</th>
      <td>30.35</td>
      <td>102.93</td>
      <td>14</td>
      <td>ML1.0</td>
      <td>天然地震</td>
      <td>四川</td>
    </tr>
    <tr>
      <th>2016-05-23 00:50:55</th>
      <td>26.08</td>
      <td>99.56</td>
      <td>10</td>
      <td>ML0.6</td>
      <td>天然地震</td>
      <td>云南</td>
    </tr>
    <tr>
      <th>2016-05-23 01:11:00</th>
      <td>23.24</td>
      <td>117.27</td>
      <td>13</td>
      <td>ML1.0</td>
      <td>天然地震</td>
      <td>广东</td>
    </tr>
  </tbody>
</table>
</div>



这样数据看起来就清爽多了。数据清理并非一步到位，在数据探索阶段也会根据需要随时对数据进行处理。现在就进入数据探索分析阶段。

**数据探索分析**

先来看下最近一周各省的地震频次。


```python
%matplotlib inline 
import matplotlib
import matplotlib.pyplot as plt

fig, ax = plt.subplots(1, 1, figsize=(12, 4))
provinces = eq.Place.value_counts() # 计算各省的地震频次
provinces[0:15].plot(ax=ax, rot=0, kind='bar'); # 只看前 10 频繁的省份
```


![](/image/data-analysis/586070-20160530180626133-1217699381.png)




图画出来了，但 x 轴的标签却是方块，因为 Matplotlib 默认不支持中文，所以这里需要一些额外设置。我用的这台机器是 ubuntu，先确认系统拥有的中文字体文件：


```python
!fc-list :lang=zh
```

    /usr/share/fonts/X11/misc/18x18ja.pcf.gz: Fixed:style=ja
    /usr/share/fonts/truetype/droid/DroidSansFallbackFull.ttf: Droid Sans Fallback:style=Regular
    /usr/share/fonts/X11/misc/18x18ko.pcf.gz: Fixed:style=ko


从中选择 Droid Sans Fallback 字体，在 python 脚本中手动加载中文字体。


```python
%matplotlib inline 
import matplotlib
import matplotlib.pyplot as plt
from matplotlib.font_manager import FontProperties 
from matplotlib.ticker import MultipleLocator, FormatStrFormatter 
font = FontProperties(fname='/usr/share/fonts/truetype/droid/DroidSansFallbackFull.ttf') # 加载系统拥有的 Droid Sans Fallback 字体

fig, ax = plt.subplots(1, 1, figsize=(12, 4))
provinces = eq.Place.value_counts() # 计算各省的地震频次
provinces[0:15].plot(ax=ax, rot=0, kind='bar') # 只看前 10 频繁的省份

for label in ax.get_xticklabels(): 
    label.set_fontproperties(font) 
```


![](/image/data-analysis/586070-20160530180637914-414547233.png)




可以看出云南是地震大省，接着就是四川、新疆。这几个省都处于几个地震带上，所以地震比较频繁。

再来看下每天的频次统计。


```python
fig, ax = plt.subplots(1, 1, figsize=(12, 4))
days = eq.index.to_period("D").value_counts()
days.plot(ax=ax, rot=0, kind='bar')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f6e0e74dd10>




![](/image/data-analysis/586070-20160530180651367-1542083519.png)




上图显示地震次数这七天是递减的，当然这不能说明任何问题，这七天在以往所有的日子里仅属于个案，还有可能是最近的地震还没来得及更新。

下面来绘制地震分布图，对最近的地震情况做一个更全面、直观的展现。

首先要安装 basemap。basemap 的[官方安装文档](http://matplotlib.org/basemap/users/installing.html)看得我头皮发麻，安装这么麻烦，总觉得哪不对劲。pip install basemap 也不管用。后来看有人建议用 sudo apt-get install python-mpltoolkits.basemap 来安装，安装是成功了，但在使用时还是提示找不到 basemap……。后来找到了[答案](http://askubuntu.com/questions/555474/problem-importing-a-module-mpl-toolkits-basemap-in-python)，就是执行下面的命令，因为我用的是 Anaconda，需执行 conda 命令来安装 basemap。

<code>conda install basemap</code>


```python
from mpl_toolkits.basemap import Basemap
import numpy as np

Ms = eq.Magnitude.map(lambda x: not x.startswith('Ms')) # boolean Series
eq = eq[Ms]
import re
get_num = lambda x: float(re.findall('\d+\.\d+', x)[0])
temp =  eq['Magnitude'].map(get_num)
eq.loc[:,('mag_num')] = temp

# 左下角
llcrnrlon, llcrnrlat = eq['Longitude'].min(), eq['Latitude'].min()
# 右上角
urcrnrlon, urcrnrlat = eq['Longitude'].max(), eq['Latitude'].max()

lons, lats = list(eq['Longitude']), list(eq['Latitude'])
lons1, lats1 = eq_map(lons, lats)
mags = eq['mag_num']

fig = plt.figure(figsize=(12,12))
ax = plt.subplot(1,1,1)
eq_map = Basemap(projection='merc', resolution = 'l', area_thresh = 1000.0,
              lat_0=0, lon_0=120,
              llcrnrlon=llcrnrlon-5, llcrnrlat=llcrnrlat-8,
              urcrnrlon=urcrnrlon+10, urcrnrlat=urcrnrlat+3)

eq_map.drawmapboundary(fill_color='lightblue')
eq_map.drawcountries()
eq_map.drawcoastlines()
# 如不设置zorder=0，画图内容将无法显示
eq_map.fillcontinents(color='ivory',lake_color='lightslategrey',zorder=0)

eq_map.scatter(lons1, lats1,s=mags*50, c='Red',marker="o", alpha=0.7) # 画散点图

plt.title("Earthquake Info of China in near week", size=20)
plt.legend()
    
plt.show()
```


![](/image/data-analysis/586070-20160530180705680-420781119.png)




**3. 基于 QQ 群的数据（qqdata.csv），分析你感兴趣的分析点。**

**读取数据**


```python
chats = pd.read_csv("qqdata.csv") #先读取 csv 文件到 DataFrame，默认文件中列之间用逗号分隔
chats.head() # 看头几行
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8cha0</td>
      <td>2011/7/8 12:11:13</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2cha061</td>
      <td>2011/7/8 12:11:49</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6cha437</td>
      <td>2011/7/8 12:13:36</td>
    </tr>
    <tr>
      <th>3</th>
      <td>7cha1</td>
      <td>2011/7/8 12:16:01</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7cha1</td>
      <td>2011/7/8 12:16:05</td>
    </tr>
  </tbody>
</table>
</div>



**数据整理**


```python
chats.time = pd.to_datetime(chats.time) # to_datetime 方法可以解析多种不同的日期表示形式
chats = chats.set_index("time") # 设置 Datetime 为 index
chats = chats.sort_index() # 根据 Datetime 来排序
chats.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
    </tr>
    <tr>
      <th>time</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2011-07-08 12:11:13</th>
      <td>8cha0</td>
    </tr>
    <tr>
      <th>2011-07-08 12:11:49</th>
      <td>2cha061</td>
    </tr>
    <tr>
      <th>2011-07-08 12:13:36</th>
      <td>6cha437</td>
    </tr>
    <tr>
      <th>2011-07-08 12:16:01</th>
      <td>7cha1</td>
    </tr>
    <tr>
      <th>2011-07-08 12:16:05</th>
      <td>7cha1</td>
    </tr>
  </tbody>
</table>
</div>




```python
chats.tail()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
    </tr>
    <tr>
      <th>time</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2012-11-30 18:26:58</th>
      <td>acha@vip.qq.co</td>
    </tr>
    <tr>
      <th>2012-11-30 18:27:27</th>
      <td>6cha437</td>
    </tr>
    <tr>
      <th>2012-11-30 18:27:43</th>
      <td>6cha437</td>
    </tr>
    <tr>
      <th>2012-11-30 18:28:24</th>
      <td>7cha1</td>
    </tr>
    <tr>
      <th>2012-11-30 18:28:28</th>
      <td>7cha1</td>
    </tr>
  </tbody>
</table>
</div>




```python
chats.info()
```

    <class 'pandas.core.frame.DataFrame'>
    DatetimeIndex: 11562 entries, 2011-07-08 12:11:13 to 2012-11-30 18:28:28
    Data columns (total 1 columns):
    id    11562 non-null object
    dtypes: object(1)
    memory usage: 180.7+ KB


可以看出，这个文件是从 2011 年 7 月 8 日中午 12 点到 2012 年 11 月 30 日傍晚 18 点半的一个群聊天记录文件，共有 11562 次发言，为保护隐私，这里没有聊天内容，只有聊天时间和聊天人，聊天人基本上都是做了处理，已经看不出真实名称。

**数据探索分析**

先来看下这一年多来都有谁在聊天，以及发言次数。


```python
person_count = chats.id.value_counts() # 对 id 进行频次统计
person_count.describe()
```




    count     144.000000
    mean       80.291667
    std       207.400659
    min         1.000000
    25%         2.750000
    50%        10.000000
    75%        56.500000
    max      1511.000000
    Name: id, dtype: float64



由上可见，这一年多来该群共有 144 人参与聊天，平均每人发言 80 次，最多的一个人发言 1511 次，最少的一个发言才 1 次，发言次数排最中间的人发言才 10 次，可见该群的聊天全员参与度不高，还是少数人在发言。

**群活跃度随时间变化**


```python
%matplotlib inline 
import matplotlib
import matplotlib.pyplot as plt

# sr = pd.Series(chats.index.strftime('%Y-%m-%d')).value_counts() # 把聊天时间转成日期，即去掉具体时间，然后统计日期频次

sr = chats.resample('D').count() # 将数据聚合到规整的低频率，被称为降采样
fig, ax = plt.subplots(1, 1, figsize=(12, 4))
sr.plot(ax=ax, kind='line'); #
```


![](/image/data-analysis/586070-20160530180718664-1636371255.png)




从上图可以看到几个聊天的高峰期。

**前 15 大话唠**


```python
fig, ax = plt.subplots(1, 1, figsize=(12, 4))
person_count[0:15].plot(ax=ax, rot=90, kind='bar'); # 前 15 大话唠
```


![](/image/data-analysis/586070-20160530180730727-1855947767.png)



现在谁是话唠一目了然。

**聊天兴致在一天中的分布**


```python
hours = pd.Series(chats.index.hour) # 从聊天时间中取出小时
hours_counts = hours.value_counts() # 统计每小时的发言频数，这里返回的 hours_counts 中只有有聊天的小时
hours_counts = hours_counts.sort_index() # 对小时排序

hc = pd.Series(np.zeros(24).astype('Int32').tolist()) # 这里生成了一天 24 个小时，为了显示完整的一天
hc[hours_counts.index]= hours_counts # 把有聊天记录的小时频数覆盖新生成的 Series

fig, ax = plt.subplots(1, 1, figsize=(12, 4))
hc.plot(ax=ax, rot=0, kind='bar'); # 画出完整的一天中的聊天频数
```


![](/image/data-analysis/586070-20160530180744430-1686523426.png)




从上图可以看到该群聊天兴致在一天中的分布，聊天兴致最高的是上午 10、11 点和下午 16 点，在这两个小时达到顶峰，上午可能是快到饭点了，就聊兴大发，下午可能是 16 点工作累了，中途聊天休息下，然后又继续工作了。早上 8、9 点竟然没有人聊天，个人认为可能是数据有缺失，或者确实大家刚上班在认真工作。

**聊天兴致在一星期中的分布**


```python
weeks = pd.Series(chats.index.weekday) # 聊天时间转为星期
weeks_counts = weeks.value_counts() # 统计一星期中每天的发言频数
weeks_counts = weeks_counts.sort_index() # 从周一到周日

weeks_counts = pd.Series(weeks_counts, index=[1, 2, 3, 4, 5, 6, 7]) # 因为原 weeks_counts 中是从 0 到 6，其实应是星期一到星期天

fig, ax = plt.subplots(1, 1, figsize=(12, 4))
weeks_counts.plot(ax=ax, rot=0, kind='bar'); # 画出完整的一天中的聊天频数
```


![](/image/data-analysis/586070-20160530180754946-1624561618.png)




可以看出，该群的聊天兴致从周一到周日是逐日下降的，周一最精神，周二周三略冷却，周四保持，周五周六话就比较少。

**寻找聊友**

所谓聊友，就是经常在一块聊天的人。这里我们定义 1 分钟之内的聊天算一个对话，聊友就是对话次数比较多的人。


```python
perids = chats.id.value_counts() # 统计 id 频次
percount = perids.count() # id 个数
perarr = np.zeros((percount, percount)).astype('Int32') # 生成个长宽都为 id 个数的矩阵
perdf = pd.DataFrame(perarr, columns=perids.index.values.tolist(), index=perids.index.values.tolist()) # 矩阵生成数据框

# 这里 perdf 是个人物数据框，存储的是任意两个人的对话次数

chatcount = chats.count().id # 聊天记录数
interval = 1 # 如果间隔小于 1 分钟，就认为是一次对话

for i in range(1, chatcount): # 遍历所有的发言
    diffseconds  = (chats.index[i]-chats.index[i-1]).seconds; # 计算每次发言跟上一个发言的间隔，这里只能取秒数，再除以 60 就有分钟了
    diffminutes = diffseconds/60;
    if(diffminutes<interval):  # 对话间隔小于 1 分钟的则认为这两个人有 1 次对话
        perdf[chats.id[i]][chats.id[i-1]]+=1 # 对话次数加 1
        perdf[chats.id[i-1]][chats.id[i]]+=1
        
perdf.head() # 每个 id 跟其它 id 的对话次数就都存在这个数据框里了
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>7cha1</th>
      <th>6cha437</th>
      <th>4cha387</th>
      <th>8cha08</th>
      <th>4cha69</th>
      <th>1cha6531</th>
      <th>acha@vip.qq.co</th>
      <th>2cha</th>
      <th>5cha8</th>
      <th>3cha423</th>
      <th>...</th>
      <th>wchajbewwtkbx@qq.co</th>
      <th>3cha2320</th>
      <th>2cha4365</th>
      <th>8cha2638</th>
      <th>wchaaster@socd.ne</th>
      <th>)chailed (104: Connection reset by pee</th>
      <th>4cha9992</th>
      <th>nchaquzhgx@qq.co</th>
      <th>1cha76447</th>
      <th>6cha555</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>7cha1</th>
      <td>1214</td>
      <td>286</td>
      <td>237</td>
      <td>34</td>
      <td>65</td>
      <td>33</td>
      <td>75</td>
      <td>74</td>
      <td>84</td>
      <td>40</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6cha437</th>
      <td>286</td>
      <td>568</td>
      <td>238</td>
      <td>32</td>
      <td>90</td>
      <td>24</td>
      <td>59</td>
      <td>18</td>
      <td>22</td>
      <td>54</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4cha387</th>
      <td>237</td>
      <td>238</td>
      <td>492</td>
      <td>22</td>
      <td>38</td>
      <td>4</td>
      <td>33</td>
      <td>41</td>
      <td>16</td>
      <td>25</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8cha08</th>
      <td>34</td>
      <td>32</td>
      <td>22</td>
      <td>364</td>
      <td>98</td>
      <td>106</td>
      <td>42</td>
      <td>65</td>
      <td>4</td>
      <td>13</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4cha69</th>
      <td>65</td>
      <td>90</td>
      <td>38</td>
      <td>98</td>
      <td>158</td>
      <td>21</td>
      <td>25</td>
      <td>7</td>
      <td>15</td>
      <td>25</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 144 columns</p>
</div>



比如我们要查看该群第一话唠（该文中话唠仅指发言次数多，并无贬义）的十大聊友。


```python
perdf['7cha1'].sort_values(ascending=False)[1:11]
```




    6cha437           286
    4cha387           237
    5cha8              84
    acha@vip.qq.co     75
    2cha               74
    4cha69             65
    1cha5900           42
    3cha423            40
    2cha4028           34
    8cha08             34
    Name: 7cha1, dtype: int32



## 参考资料

- [利用 Python 进行数据分析](https://book.douban.com/subject/25779298)