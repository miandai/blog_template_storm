---
layout: post
keywords: blog
description: blog
title: "大作业-电影推荐系统"
categories: [Data_Analysis]
tags: [Data_Analysis]
---
{% include codepiano/setup %}


## 电影推荐系统

推荐系统的文献汗牛充栋，大家对此应该都不陌生。之所以选这个题目一是简单，在一周多晚上十点以后的自由时间里，只有选简单的题目才能完成，即便如此，依然捉襟见肘；二是希望好好研究下数据，一步步推到推荐系统的设计，而不是像以前直奔算法，当然也是时间原因，这里对数据的探索也是远远不够的。

本文前面探索阶段所用的数据集太大，导致多个分析运行一天也出不了结果，所以后面在推荐系统的建模中，又换成了较小的 MovieLens 1M 数据集。

<!--more-->


```python
import pandas as pd
import numpy as np
import datetime
```


```python
%matplotlib inline
import matplotlib.pyplot as plt
```

## 数据探索 

在数据探索阶段，刚开始是漫无目的的，跟着感觉走，对数据哪一块感兴趣就统计下，排个序，画个图，这样慢慢就对数据熟悉起来，熟悉之后再来考虑如何利用用户历史行为数据来做推荐。

**读取数据**

读取评分数据：


```python
ratings = pd.read_csv('./ml-latest/ratings.csv', header=0)
```


```python
ratings.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>userId</th>
      <th>movieId</th>
      <th>rating</th>
      <th>timestamp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>169</td>
      <td>2.5</td>
      <td>1204927694</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>2471</td>
      <td>3.0</td>
      <td>1204927438</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>48516</td>
      <td>5.0</td>
      <td>1204927435</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2571</td>
      <td>3.5</td>
      <td>1436165433</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>109487</td>
      <td>4.0</td>
      <td>1436165496</td>
    </tr>
  </tbody>
</table>
</div>




```python
ratings['timestamp']=ratings.timestamp.map(datetime.datetime.utcfromtimestamp) # 时间格式转换
```


```python
ratings.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>userId</th>
      <th>movieId</th>
      <th>rating</th>
      <th>timestamp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>169</td>
      <td>2.5</td>
      <td>2008-03-07 22:08:14</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>2471</td>
      <td>3.0</td>
      <td>2008-03-07 22:03:58</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>48516</td>
      <td>5.0</td>
      <td>2008-03-07 22:03:55</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2571</td>
      <td>3.5</td>
      <td>2015-07-06 06:50:33</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>109487</td>
      <td>4.0</td>
      <td>2015-07-06 06:51:36</td>
    </tr>
  </tbody>
</table>
</div>




```python
ratings.count()
```




    userId       22884377
    movieId      22884377
    rating       22884377
    timestamp    22884377
    dtype: int64



读取电影信息数据：


```python
movies = pd.read_csv('./ml-latest/movies.csv', header=0)
```


```python
movies.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movieId</th>
      <th>title</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Jumanji (1995)</td>
      <td>Adventure|Children|Fantasy</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Grumpier Old Men (1995)</td>
      <td>Comedy|Romance</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Waiting to Exhale (1995)</td>
      <td>Comedy|Drama|Romance</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Father of the Bride Part II (1995)</td>
      <td>Comedy</td>
    </tr>
  </tbody>
</table>
</div>




```python
movies = movies.set_index("movieId")
movies.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>genres</th>
    </tr>
    <tr>
      <th>movieId</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Jumanji (1995)</td>
      <td>Adventure|Children|Fantasy</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Grumpier Old Men (1995)</td>
      <td>Comedy|Romance</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Waiting to Exhale (1995)</td>
      <td>Comedy|Drama|Romance</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Father of the Bride Part II (1995)</td>
      <td>Comedy</td>
    </tr>
  </tbody>
</table>
</div>




```python
movies.count()
```




    title     34208
    genres    34208
    dtype: int64



该数据集包含了对 34208 部电影的 22884377 个评分数据。

**流行度调查**


```python
moviefreq = ratings.movieId.value_counts() # 统计每部电影的评分人数，可看出电影的流行程度，默认是降序排列
moviefreq.count()
```




    33670




```python
sorted_byfreq = movies.loc[moviefreq.index] # 根据频次大小依次取电影信息
sorted_byfreq['ranking']=range(moviefreq.count()) # 加上排名
sorted_byfreq['freq']=moviefreq # 加上频次
sorted_byfreq.iloc[0:10] # 前十大流行电影
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>genres</th>
      <th>ranking</th>
      <th>freq</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>356</th>
      <td>Forrest Gump (1994)</td>
      <td>Comedy|Drama|Romance|War</td>
      <td>0</td>
      <td>81296</td>
    </tr>
    <tr>
      <th>296</th>
      <td>Pulp Fiction (1994)</td>
      <td>Comedy|Crime|Drama|Thriller</td>
      <td>1</td>
      <td>79091</td>
    </tr>
    <tr>
      <th>318</th>
      <td>Shawshank Redemption, The (1994)</td>
      <td>Crime|Drama</td>
      <td>2</td>
      <td>77887</td>
    </tr>
    <tr>
      <th>593</th>
      <td>Silence of the Lambs, The (1991)</td>
      <td>Crime|Horror|Thriller</td>
      <td>3</td>
      <td>76271</td>
    </tr>
    <tr>
      <th>480</th>
      <td>Jurassic Park (1993)</td>
      <td>Action|Adventure|Sci-Fi|Thriller</td>
      <td>4</td>
      <td>69545</td>
    </tr>
    <tr>
      <th>260</th>
      <td>Star Wars: Episode IV - A New Hope (1977)</td>
      <td>Action|Adventure|Sci-Fi</td>
      <td>5</td>
      <td>67092</td>
    </tr>
    <tr>
      <th>2571</th>
      <td>Matrix, The (1999)</td>
      <td>Action|Sci-Fi|Thriller</td>
      <td>6</td>
      <td>64830</td>
    </tr>
    <tr>
      <th>110</th>
      <td>Braveheart (1995)</td>
      <td>Action|Drama|War</td>
      <td>7</td>
      <td>61267</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
      <td>8</td>
      <td>60424</td>
    </tr>
    <tr>
      <th>527</th>
      <td>Schindler's List (1993)</td>
      <td>Drama|War</td>
      <td>9</td>
      <td>59857</td>
    </tr>
  </tbody>
</table>
</div>




```python
sorted_byfreq[sorted_byfreq.title.str.contains('Kill Bill')] # 查看某部电影的流行度
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>genres</th>
      <th>ranking</th>
      <th>freq</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6874</th>
      <td>Kill Bill: Vol. 1 (2003)</td>
      <td>Action|Crime|Thriller</td>
      <td>110</td>
      <td>26225</td>
    </tr>
    <tr>
      <th>7438</th>
      <td>Kill Bill: Vol. 2 (2004)</td>
      <td>Action|Drama|Thriller</td>
      <td>157</td>
      <td>22301</td>
    </tr>
  </tbody>
</table>
</div>



再来看下每部电影的评分次数分布。


```python
moviefreq1 = moviefreq.copy()
moviefreq1.index = range(moviefreq1.count()) # 对索引重新赋值，方便画图
fig, ax = plt.subplots(1, 1, figsize=(12, 4))
moviefreq1.plot(ax=ax, title='Rating times');
```


![](/image/data-analysis/586070-20160708005017764-1510427095.png)




由上可见，对于 34208 部电影来说，评分个数最多的也只有 81296 个，而一共有 247753 个用户，更不用提大量评分次数远小于 10000 的电影，可见，推荐的空间是非常大的。

**评分时间区间**

统计下评分时间的频次分布。


```python
ts = ratings.timestamp.copy()
```


```python
ts.head()
```




    0   2008-03-07 22:08:14
    1   2008-03-07 22:03:58
    2   2008-03-07 22:03:55
    3   2015-07-06 06:50:33
    4   2015-07-06 06:51:36
    Name: timestamp, dtype: datetime64[ns]




```python
ts2 = pd.Series(np.ones(ts.count()).astype('Int32'), index=ts.values).sort_index()
```


```python
ts3 = ts2.to_period("Y").groupby(level=0).count()
```


```python
fig, ax = plt.subplots(1, 1, figsize=(12, 4))
ts3.plot(ax=ax, kind='bar', title='Rating times');
```

![](/image/data-analysis/586070-20160708005034421-630640006.png)




评分次数随年份的分布。

**评分排名**

怎么根据评分来对电影排名？先试试平均分吧，这个貌似比较公平。


```python
meanrating = ratings['rating'].groupby(ratings['movieId']).mean()
```


```python
meanrating = meanrating.sort_values(ascending = False)
```


```python
meanrating.head()
```




    movieId
    95517     5.0
    148781    5.0
    141483    5.0
    136872    5.0
    139134    5.0
    Name: rating, dtype: float64




```python
sorted_byrate = movies.loc[meanrating.index] # 根据频次大小依次取电影信息
sorted_byrate['ranking']=range(meanrating.count()) # 加上排名
sorted_byrate['rating']=meanrating # 加上评分
sorted_byrate.iloc[0:10] # 前十大流行电影
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>genres</th>
      <th>ranking</th>
      <th>rating</th>
    </tr>
    <tr>
      <th>movieId</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>95517</th>
      <td>Barchester Chronicles, The (1982)</td>
      <td>Drama</td>
      <td>0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>148781</th>
      <td>Under the Electric Sky (2014)</td>
      <td>Documentary</td>
      <td>1</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>141483</th>
      <td>Lost Rivers (2013)</td>
      <td>Documentary</td>
      <td>2</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>136872</th>
      <td>Zapatlela (1993)</td>
      <td>(no genres listed)</td>
      <td>3</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>139134</th>
      <td>Soodhu Kavvum (2013)</td>
      <td>Comedy|Thriller</td>
      <td>4</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>135727</th>
      <td>Aarya (2004)</td>
      <td>Comedy|Drama|Romance</td>
      <td>5</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>103143</th>
      <td>Donos de Portugal (2012)</td>
      <td>Documentary</td>
      <td>6</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>141434</th>
      <td>My Friend Victoria (2014)</td>
      <td>Drama</td>
      <td>7</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>150268</th>
      <td>Dilwale (2015)</td>
      <td>Action|Children|Comedy|Crime|Drama|Romance</td>
      <td>8</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>148857</th>
      <td>Christmas, Again (2015)</td>
      <td>(no genres listed)</td>
      <td>9</td>
      <td>5.0</td>
    </tr>
  </tbody>
</table>
</div>



上面全 5 分的电影竟然一部都没看过！


```python
sorted_byrate[sorted_byrate.title.str.contains('Kill Bill')] # 查看某部电影的评分
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>genres</th>
      <th>ranking</th>
      <th>freq</th>
    </tr>
    <tr>
      <th>movieId</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6874</th>
      <td>Kill Bill: Vol. 1 (2003)</td>
      <td>Action|Crime|Thriller</td>
      <td>3066</td>
      <td>3.889743</td>
    </tr>
    <tr>
      <th>7438</th>
      <td>Kill Bill: Vol. 2 (2004)</td>
      <td>Action|Drama|Thriller</td>
      <td>3387</td>
      <td>3.856621</td>
    </tr>
  </tbody>
</table>
</div>



把评分人次也加上。


```python
sorted_byrate = movies.loc[meanrating.index] # 根据频次大小依次取电影信息
sorted_byrate['ranking']=range(meanrating.count()) # 加上排名
sorted_byrate['rating']=meanrating # 加上评分
sorted_byrate['freq']=moviefreq.loc[meanrating.index] # 加上评分个数
sorted_byrate.iloc[0:10] # 前十大流行电影
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>genres</th>
      <th>ranking</th>
      <th>rating</th>
      <th>freq</th>
    </tr>
    <tr>
      <th>movieId</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>95517</th>
      <td>Barchester Chronicles, The (1982)</td>
      <td>Drama</td>
      <td>0</td>
      <td>5.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>148781</th>
      <td>Under the Electric Sky (2014)</td>
      <td>Documentary</td>
      <td>1</td>
      <td>5.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>141483</th>
      <td>Lost Rivers (2013)</td>
      <td>Documentary</td>
      <td>2</td>
      <td>5.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>136872</th>
      <td>Zapatlela (1993)</td>
      <td>(no genres listed)</td>
      <td>3</td>
      <td>5.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>139134</th>
      <td>Soodhu Kavvum (2013)</td>
      <td>Comedy|Thriller</td>
      <td>4</td>
      <td>5.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>135727</th>
      <td>Aarya (2004)</td>
      <td>Comedy|Drama|Romance</td>
      <td>5</td>
      <td>5.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>103143</th>
      <td>Donos de Portugal (2012)</td>
      <td>Documentary</td>
      <td>6</td>
      <td>5.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>141434</th>
      <td>My Friend Victoria (2014)</td>
      <td>Drama</td>
      <td>7</td>
      <td>5.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>150268</th>
      <td>Dilwale (2015)</td>
      <td>Action|Children|Comedy|Crime|Drama|Romance</td>
      <td>8</td>
      <td>5.0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>148857</th>
      <td>Christmas, Again (2015)</td>
      <td>(no genres listed)</td>
      <td>9</td>
      <td>5.0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
sorted_byrate[sorted_byrate.title.str.contains('Kill Bill')] # 查看某部电影的评分
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>genres</th>
      <th>ranking</th>
      <th>rating</th>
      <th>freq</th>
    </tr>
    <tr>
      <th>movieId</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6874</th>
      <td>Kill Bill: Vol. 1 (2003)</td>
      <td>Action|Crime|Thriller</td>
      <td>3066</td>
      <td>3.889743</td>
      <td>26225</td>
    </tr>
    <tr>
      <th>7438</th>
      <td>Kill Bill: Vol. 2 (2004)</td>
      <td>Action|Drama|Thriller</td>
      <td>3387</td>
      <td>3.856621</td>
      <td>22301</td>
    </tr>
  </tbody>
</table>
</div>



原来这些全 5 分的电影都只有 1 个评分！这就把排名排上去了！看来平均分不靠谱，得把评分人次也考虑进去！

先把评分少于 30 个的剔出去。


```python
sorted_byrate2 = sorted_byrate[sorted_byrate.freq>30]
```


```python
sorted_byrate2.head(10) # 前十大评分最高电影
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>genres</th>
      <th>ranking</th>
      <th>rating</th>
      <th>freq</th>
    </tr>
    <tr>
      <th>movieId</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>318</th>
      <td>Shawshank Redemption, The (1994)</td>
      <td>Crime|Drama</td>
      <td>627</td>
      <td>4.441710</td>
      <td>77887</td>
    </tr>
    <tr>
      <th>858</th>
      <td>Godfather, The (1972)</td>
      <td>Crime|Drama</td>
      <td>641</td>
      <td>4.353639</td>
      <td>49846</td>
    </tr>
    <tr>
      <th>50</th>
      <td>Usual Suspects, The (1995)</td>
      <td>Crime|Mystery|Thriller</td>
      <td>668</td>
      <td>4.318987</td>
      <td>53195</td>
    </tr>
    <tr>
      <th>527</th>
      <td>Schindler's List (1993)</td>
      <td>Drama|War</td>
      <td>675</td>
      <td>4.290952</td>
      <td>59857</td>
    </tr>
    <tr>
      <th>140737</th>
      <td>The Lost Room (2006)</td>
      <td>Action|Fantasy|Mystery</td>
      <td>680</td>
      <td>4.280822</td>
      <td>73</td>
    </tr>
    <tr>
      <th>1221</th>
      <td>Godfather: Part II, The (1974)</td>
      <td>Crime|Drama</td>
      <td>681</td>
      <td>4.268878</td>
      <td>32247</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>Seven Samurai (Shichinin no samurai) (1954)</td>
      <td>Action|Adventure|Drama</td>
      <td>682</td>
      <td>4.262134</td>
      <td>12753</td>
    </tr>
    <tr>
      <th>904</th>
      <td>Rear Window (1954)</td>
      <td>Mystery|Thriller</td>
      <td>815</td>
      <td>4.246988</td>
      <td>19422</td>
    </tr>
    <tr>
      <th>1193</th>
      <td>One Flew Over the Cuckoo's Nest (1975)</td>
      <td>Drama</td>
      <td>816</td>
      <td>4.242451</td>
      <td>35832</td>
    </tr>
    <tr>
      <th>2959</th>
      <td>Fight Club (1999)</td>
      <td>Action|Crime|Drama|Thriller</td>
      <td>817</td>
      <td>4.233925</td>
      <td>48879</td>
    </tr>
  </tbody>
</table>
</div>



这样看着就正常多了！高分电影有点好电影的样子了。以后没电影看了，就来这排行榜上挨着找，不信你都看过！

**评分均值和评分次数的相关性**

前十大评分最高电影和前十大评分次数最高的电影中是有重合的，如《Shawshank Redemption》和《Schindler's List》，由此，我们可以验证下评分均值和评分次数的相关性。


```python
fig, ax = plt.subplots(1, 1, figsize=(12, 4))
ax.scatter(sorted_byrate['freq'],sorted_byrate['rating']);
```

![](/image/data-analysis/586070-20160708005044264-508446137.png)




由上图可见，评分少的不见得就平均分就低，从整体趋势来看，评分次数多的，平均分也高。可见，流行电影确实受人欢迎。从另一个角度看，存在不少广受大众欢迎的电影，但也存在不少看的人不多，但评分很高质量很好的电影，太流行的电影肯定大家都看过了，关键是如何找到那些还比较小众的电影，这些电影可能具备大众欢迎的元素，但因宣传做得不好没被大众发现，也可能是这些电影就是小众，在小圈子里非常受欢迎，但到更大的人群中就不行。如何把这些电影推荐给何时的人，是个性化推荐要考虑的问题。

**反作弊**

找出那些没有真实评分，只给假评分的。

先统计下每个人评分次数的分布。


```python
userfreq = ratings.userId.value_counts() # 统计每个人的评分次数，默认是降序排列
userfreq.count()
```




    247753



确实是 247753 个人的评分，一个不差，跟 README.txt 说的一样。


```python
userfreq.head()
```




    185430    9281
    46750     7515
    204165    7057
    135877    6015
    58040     5801
    Name: userId, dtype: int64




```python
timesfreq = userfreq.copy()
timesfreq.index = range(timesfreq.count()) # 对索引重新赋值，方便画图
timesfreq.head()
```




    0    9281
    1    7515
    2    7057
    3    6015
    4    5801
    Name: userId, dtype: int64




```python
fig, ax = plt.subplots(1, 1, figsize=(15, 4))
timesfreq.plot(ax=ax);
ax.set_xlabel("People ID");
ax.set_ylabel("Rating times");
```

![](/image/data-analysis/586070-20160708005055514-1491387809.png)




由图可见，人们的评分次数是呈幂律分布的，只有少数人的评分次数巨多，然后迅速过渡到绝大多数人的评分次数。


```python
timesfreq[timesfreq>2000].count()
```




    295



评分超过 2000 次的人有 295 个，这些人太爱看电影了。至于后面评分较少的，也可能是加入评分较晚，或者看过很多电影，只是没在这评分而已，所以这里面肯定也隐藏了不少电影达人。

下面分别看看评分 2000 次以上和 2000 次以下的评分次数分布。


```python
timesfreq1 = userfreq[userfreq>=2000].copy()
timesfreq1.index = range(timesfreq1.count()) # 对索引重新赋值，方便画图
fig, ax = plt.subplots(1, 1, figsize=(15, 4))
timesfreq1.plot(ax=ax);
ax.set_xlabel("People ID");
ax.set_ylabel("Rating times");
```

![](/image/data-analysis/586070-20160708005107530-848125527.png)





```python
timesfreq2 = userfreq[userfreq<2000].copy()
timesfreq2.index = range(timesfreq2.count()) # 对索引重新赋值，方便画图
fig, ax = plt.subplots(1, 1, figsize=(15, 4))
timesfreq2.plot(ax=ax);
ax.set_xlabel("People ID");
ax.set_ylabel("Rating times");
```


![](/image/data-analysis/586070-20160708005118686-336372757.png)




不得不说，幂律分布无处不在啊。

看下评分次数少于 10 次的用户个数。


```python
userfreq[userfreq<10].count()
```




    32775



竟然有三万多人。


```python
timesfreq3 = userfreq[userfreq<10].copy()
timesfreq3.index = range(timesfreq3.count()) # 对索引重新赋值，方便画图
fig, ax = plt.subplots(1, 1, figsize=(15, 4))
timesfreq3.plot(ax=ax);
ax.set_xlabel("People ID");
ax.set_ylabel("Rating times");
```


![](/image/data-analysis/586070-20160708005129171-492987960.png)




```python
userfreq[userfreq==1].count()
```




    4251



评分 1 次的就有四千多人。


```python
onerating = ratings[ratings.userId.isin(userfreq[userfreq==1].index.values.tolist())] # 这里的 isin 方法可是费了好大劲找到的
print onerating.count()
print onerating.head()
```

    userId       4251
    movieId      4251
    rating       4251
    timestamp    4251
    dtype: int64
           userId  movieId  rating   timestamp
    10137     108     2302     4.5  1352678182
    19688     215      318     3.0  1434516586
    23937     263     1029     4.5  1207138536
    30266     356     3254     4.0  1325107825
    32553     376     7153     5.0  1427304194
    


```python
fig, ax = plt.subplots(1, 1, figsize=(15, 4))
onerating.rating.value_counts().plot(ax=ax, kind='bar', title='Ratings');
```


![](/image/data-analysis/586070-20160708005141889-1964231050.png)



没发现什么异常情况，本来想着只有一次评分的是不是都是来为某电影刷分的，现在否定这种想法。

**电影时间的分布**


```python
movies.title.head()
```




    movieId
    1                      Toy Story (1995)
    2                        Jumanji (1995)
    3               Grumpier Old Men (1995)
    4              Waiting to Exhale (1995)
    5    Father of the Bride Part II (1995)
    Name: title, dtype: object




```python
movieyears = movies.title.str.extract('(\((\d{4})\))', expand=True).ix[:,1] # 使用正则表达式取出上映年份
movieyears.head()
```




    movieId
    1    1995
    2    1995
    3    1995
    4    1995
    5    1995
    Name: 1, dtype: object




```python
yearfreq = movieyears.value_counts() # 统计每部电影的上映年份，可看出电影的流行程度，默认是降序排列
yearfreq.count()
```




    129




```python
yearfreqsort = yearfreq.sort_index()
yearfreqsort.head()
```




    1874    1
    1878    1
    1887    1
    1888    2
    1890    3
    Name: 1, dtype: int64



看下这些电影的年份分布。

第一幅图太密集了，就分两幅图显示。


```python
fig, ax = plt.subplots(3, 1, figsize=(15, 12))
yearfreqsort.plot(ax=ax[0], kind='bar', title='freq');
yearfreqsort.iloc[0:60].plot(ax=ax[1], kind='bar', title='freq');
yearfreqsort.iloc[60:].plot(ax=ax[2], kind='bar', title='freq');
```


![](/image/data-analysis/586070-20160708005156139-452678667.png)




看每年的电影个数，可以感受到历史的变迁。电影个数在上个世纪 90 年代之前一直增长缓慢，到了 90 年代中期开始飞速增长，直到今天。

没想到 1900 年之前还有几部电影。看看什么名字。


```python
movies.ix[movieyears[movieyears<'1900'].index] # 1900 前的电影
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>genres</th>
    </tr>
    <tr>
      <th>movieId</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>82337</th>
      <td>Four Heads Are Better Than One (Un homme de tê...</td>
      <td>Fantasy</td>
    </tr>
    <tr>
      <th>82362</th>
      <td>Pyramid of Triboulet, The (La pyramide de Trib...</td>
      <td>Fantasy</td>
    </tr>
    <tr>
      <th>88674</th>
      <td>Edison Kinetoscopic Record of a Sneeze (1894)</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>94431</th>
      <td>Ella Lola, a la Trilby (1898)</td>
      <td>(no genres listed)</td>
    </tr>
    <tr>
      <th>94657</th>
      <td>Turkish Dance, Ella Lola (1898)</td>
      <td>(no genres listed)</td>
    </tr>
    <tr>
      <th>94951</th>
      <td>Dickson Experimental Sound Film (1894)</td>
      <td>Musical</td>
    </tr>
    <tr>
      <th>95541</th>
      <td>Blacksmith Scene (1893)</td>
      <td>(no genres listed)</td>
    </tr>
    <tr>
      <th>96009</th>
      <td>Kiss, The (1896)</td>
      <td>Romance</td>
    </tr>
    <tr>
      <th>98981</th>
      <td>Arrival of a Train, The (1896)</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>113048</th>
      <td>Tables Turned on the Gardener (1895)</td>
      <td>Comedy</td>
    </tr>
    <tr>
      <th>120869</th>
      <td>Employees Leaving the Lumière Factory (1895)</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>125978</th>
      <td>Santa Claus (1898)</td>
      <td>Sci-Fi</td>
    </tr>
    <tr>
      <th>129849</th>
      <td>Old Man Drinking a Glass of Beer (1898)</td>
      <td>(no genres listed)</td>
    </tr>
    <tr>
      <th>129851</th>
      <td>Dickson Greeting (1891)</td>
      <td>(no genres listed)</td>
    </tr>
    <tr>
      <th>140539</th>
      <td>Pauvre Pierrot (1892)</td>
      <td>Animation</td>
    </tr>
    <tr>
      <th>140547</th>
      <td>The Merry Skeleton (1898)</td>
      <td>Comedy</td>
    </tr>
    <tr>
      <th>140549</th>
      <td>Serpentine Dance: Loïe Fuller (1897)</td>
      <td>(no genres listed)</td>
    </tr>
    <tr>
      <th>142851</th>
      <td>Arab Cortege, Geneva (1896)</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>148040</th>
      <td>Man Walking Around a Corner (1887)</td>
      <td>(no genres listed)</td>
    </tr>
    <tr>
      <th>148042</th>
      <td>Accordion Player (1888)</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>148044</th>
      <td>Monkeyshines, No. 1 (1890)</td>
      <td>Comedy</td>
    </tr>
    <tr>
      <th>148046</th>
      <td>Monkeyshines, No. 2 (1890)</td>
      <td>(no genres listed)</td>
    </tr>
    <tr>
      <th>148048</th>
      <td>Sallie Gardner at a Gallop (1878)</td>
      <td>(no genres listed)</td>
    </tr>
    <tr>
      <th>148050</th>
      <td>Traffic Crossing Leeds Bridge (1888)</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>148052</th>
      <td>London's Trafalgar Square (1890)</td>
      <td>(no genres listed)</td>
    </tr>
    <tr>
      <th>148054</th>
      <td>Passage de Venus (1874)</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>148064</th>
      <td>Newark Athlete (1891)</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>148462</th>
      <td>Men Boxing (1891)</td>
      <td>Action|Documentary</td>
    </tr>
    <tr>
      <th>148703</th>
      <td>The Wave (1891)</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>148705</th>
      <td>A Hand Shake (1892)</td>
      <td>(no genres listed)</td>
    </tr>
    <tr>
      <th>148877</th>
      <td>Fencing (1892)</td>
      <td>(no genres listed)</td>
    </tr>
  </tbody>
</table>
</div>



都没看过，不过确实挺厉害的，那时我们还是大清朝啊。

下面按年代显示电影个数。


```python
movieyears1 = movieyears.str[:3] + "0s"
yearfreq1 = movieyears1.value_counts() # 统计每部电影的上映年份，可看出电影的流行程度，默认是降序排列
yearfreqsort1 = yearfreq1.sort_index()
fig, ax = plt.subplots(1, 1, figsize=(15, 4))
yearfreqsort1.plot(ax=ax, kind='bar', title='freq');
```


![](/image/data-analysis/586070-20160708005209467-2011006175.png)




可以看到电影不断增多的趋势，以后也会越来越多。

**电影基因的分布**


```python
genreslist = [] # 存储为所有电影标注的基因
genreseries = movies.genres.str.split(pat = "|")
genrecount = genreseries.count()
for i in range(genrecount):
    genreslist.extend(genreseries.iloc[i]) # 把 Series 中的所有元素展平组成一个 list
len(genreslist)
```




    66668



上面的代码运行得比较久，好在数据量不大，看个电视的功夫就完了，但下面再用这个代码就不好使了。


```python
movies.count()
```




    title     34208
    genres    34208
    dtype: int64




```python
allmoviegenres = pd.Series(genreslist)
genrestats = allmoviegenres.value_counts()
fig, ax = plt.subplots(1, 1, figsize=(15, 4))
genrestats.plot(ax=ax, kind='bar', title='freq');
```


![](/image/data-analysis/586070-20160708005222764-926761116.png)



戏剧最多，喜剧其次，接着是惊悚、浪漫、动作、犯罪、恐怖、记录、冒险、科幻、神秘、幻想、儿童、动画、战争、音乐剧、西部、黑色、IMAX。

下面我们看下所有评分的影片的基因分布。


```python
#mi = movies.ix[ratings.movieId]
#genreslist = [] # 存储为所有电影标注的基因
#genreseries = mi.genres.str.split(pat = "|")
#genrecount = genreseries.count()
#for i in range(genrecount):
#    genreslist.extend(genreseries.iloc[i]) # 把 Series 中的所有元素展平组成一个 list
#allmoviegenres = pd.Series(genreslist)
#genrestats = allmoviegenres.value_counts()
#fig, ax = plt.subplots(1, 1, figsize=(15, 4))
#genrestats.plot(ax=ax, kind='bar', title='freq');
```

上面这段代码运行了一晚上，早上起来一看，内存错误……，为了省事儿，代码写得惨不忍睹……

它实现的功能是取出所有评分涉及电影的基因，并对基因做统计，主要是为了看看观众观看的电影基因的分布，跟上面的电影基因统计还不一样。观众的观看记录代表了用户的兴趣所在，不管最后给的是高分低分，总算是因为感兴趣才看的，所以这里对这些做个统计。

上面的代码没有考虑内存，下面对代码做个优化，对 ratings 里的电影一一提取基因来统计，稍微做改动下就没有内存问题了，但运算速度就没办法了，还是要对 22884377 个评分一个一个提取电影基因。


```python
s1 = pd.Series(np.zeros(20,dtype=np.int32),index=['Drama','Comedy','Thriller','Romance','Action', \
                                                  'Crime','Horror','Documentary','Adventure','Sci-Fi', \
                                                  'Mystery','Fantasy','Children','Animation','War', \
                                                  '(no genres listed)','Musical','Western','Film-Noir','IMAX'])
rcount = ratings.count()[0]
for i in range(rcount):
    if (0 == (i%1000000)): # 相当于进度条，不然 7 个小时暗箱运行也不知道进度
        print i
    mid = ratings.movieId.iloc[i]
    grs = movies.ix[mid].genres.split("|")
    s1.ix[grs] += 1
```

    0
    1000000
    2000000
    3000000
    4000000
    5000000
    6000000
    7000000
    8000000
    9000000
    10000000
    11000000
    12000000
    13000000
    14000000
    15000000
    16000000
    17000000
    18000000
    19000000
    20000000
    21000000
    22000000
    

上面这块代码运行了七个多小时！不多说了，赶紧把结果保存下来！


```python
s1.to_csv('genres_distribution.csv')
```


```python
s1
```




    Drama                 10137200
    Comedy                 8437502
    Thriller               6123348
    Romance                4342070
    Action                 6547286
    Crime                  3803018
    Horror                 1685352
    Documentary             279609
    Adventure              5117321
    Sci-Fi                 3694890
    Mystery                1794175
    Fantasy                2449668
    Children               1923874
    Animation              1362681
    War                    1206361
    (no genres listed)        2454
    Musical                 974697
    Western                 472588
    Film-Noir               238480
    IMAX                    676420
    dtype: int32




```python
s1_sort = s1.sort_values(ascending = False) # 排序
```


```python
fig, ax = plt.subplots(1, 1, figsize=(15, 4))
s1_sort.plot(ax=ax, kind='bar', title='freq');
```


![](/image/data-analysis/586070-20160708005234311-304741335.png)




由上可见，观众的评分记录的电影基因跟电影实际存在的基因排序是一致的，可以用电影的拍摄是为了满足观众需求来解释，也可以说是观众有什么看什么。

除了纪录片电影的观看人次相比偏少，有点靠后，供过于求，当然还是建议大家多看记录片，获取知识是一个春风化雨的过程。

## 推荐系统

由于上面使用大数据集的惨痛教训，这里换成了较小的 [MovieLens 1M Dataset](http://grouplens.org/datasets/movielens/) 数据集。

**读取数据**


```python
ratings1m_train = pd.read_csv('./ml-1m/ratings.dat', sep="::", names=['userId','movieId','rating','timestamp'],engine='python')
```


```python
ratings1m_train.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>userId</th>
      <th>movieId</th>
      <th>rating</th>
      <th>timestamp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1193</td>
      <td>5</td>
      <td>978300760</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>661</td>
      <td>3</td>
      <td>978302109</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>914</td>
      <td>3</td>
      <td>978301968</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>3408</td>
      <td>4</td>
      <td>978300275</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2355</td>
      <td>5</td>
      <td>978824291</td>
    </tr>
  </tbody>
</table>
</div>




```python
ratings1m_train = ratings1m_train.drop(['timestamp','rating'], axis=1) # TopN 推荐忽略具体评分
```


```python
ratings1m_train.count()
```




    userId     1000209
    movieId    1000209
    dtype: int64



**分离训练集和测试集**


```python
from sklearn import cross_validation
from sklearn.cross_validation import train_test_split
```


```python
ratings1m_train.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>userId</th>
      <th>movieId</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1193</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>661</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>914</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>3408</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2355</td>
    </tr>
  </tbody>
</table>
</div>




```python
totalcount = ratings1m_train.count()[0]
```


```python
all_index = np.arange(totalcount)
train_index = np.random.choice(totalcount, int(0.8*totalcount), replace=False) # 从 0 到 totalcount 之间抽 80%，无放回
test_index = np.setdiff1d(all_index, train_index) # 集合的差
```


```python
train_data = ratings1m_train.iloc[train_index]
test_data = ratings1m_train.iloc[test_index]
```

**计算用户相似度**


```python
users = ratings1m_train.userId.unique()
users
```




    array([   1,    2,    3, ..., 6038, 6039, 6040], dtype=int64)




```python
movies = ratings1m_train.movieId.unique()
movies
```




    array([1193,  661,  914, ..., 2845, 3607, 2909], dtype=int64)



该数据集包含了 6040 个用户对 3706 部电影的 1000209 个评分。


```python
userSimilarity = pd.DataFrame(0, columns=users, index=users) # 用户相似度矩阵，初始化为 0
```

预测准确度是度量一个推荐系统预测用户行为的能力。这是个非常重要的离线评测指标，计算该指标时要有个离线的数据集，该数据集包含用户的历史行为记录，然后分成训练集和测试集，最后通过在训练集上建立的用户行为模型用于测试集，然后计算预测行为和在测试集的实际行为的重合度作为预测准确度。

一般认为 RMSE 比 MAE 更苛刻，通过平方项加大了对预测不准的评分的惩罚。

准确率是推荐列表中有多大比例是发生了行为的。召回率是用户实际发生的行为有多大比例是来自推荐。

评分预测一直是推荐系统研究的热点，对此，亚马逊前科学家 Greg Linden 认为，电影推荐的目的是找出用户最有可能感兴趣的电影，而不是预测用户看了电影后会给多少分，因此 TopN 更符合应用需求，也许有一部电影用户看了给分很高，但其它用户看的可能性很小，因此预测用户是否会看一部电影，比预测评分更重要。

本次作业是研究 TopN 推荐问题，忽略数据集中的具体评分。TopN 推荐的任务是预测用户会不会对某部电影评分，而不是预测评多少分。

**预测推荐**

建立物品到用户的倒排表，对于每个物品都保存对该物品产生过行为的用户列表。


```python
movie_users = pd.Series('',index=movies) # 这里 Series 不能直接存空的 list，所以只有先存个空字符串，然后用 split 把它转为 list
movie_users = movie_users.str.split()

for i in train_data.index.values: # 扫描训练数据集
    movie_users.ix[train_data.movieId.ix[i]].append(train_data.userId.ix[i])
```


```python
import math

C = dict()
N = dict()

for i in movie_users.index.values:
    for u in movie_users.ix[i]:
        N.setdefault(u,1)
        N[u] += 1
        for v in movie_users.ix[i]:
            if u == v:
                continue
            C.setdefault(u,{})
            C[u].setdefault(v,0)
            C[u][v] += 1
for u, related_users in C.items():
    for v, cuv in related_users.items():
        userSimilarity.ix[u][v]=cuv / math.sqrt(N[u]*N[v])
```

    
    


```python
from operator import itemgetter
def recommend(uid, n_sim_user, n_rec_movie):
    K = n_sim_user
    N = n_rec_movie
    rank = dict()
    watched_movies = train_data[train_data.userId == uid].movieId.values
    
    simusers = userSimilarity.ix[uid].sort_values(ascending=False)[0:K]
    for v in simusers.index.values:
        for m in train_data[train_data.userId == v].movieId.values:
            if m in watched_movies:
                    continue
            rank.setdefault(m,0)
            rank[m] += simusers.ix[v]
                
    # 返回 N 部电影
    return sorted(rank.items(), key=itemgetter(1), reverse=True)[0:N]
```

最后计算离线推荐算法的准确率、召回率和覆盖率。

令系统的用户集合为 U， R(u) 是根据用户在训练集上的行为给用户作出的推荐列表，而 T(u) 是用户在测试集上的行为列表。那么推荐结果的准确率定义为：

$$Precision=\frac{\sum_{u\in U}|R(u)\cap T(u)|}{\sum_{u\in U}|R(u)|}$$

推荐结果的召回率定义为：

$$Recall=\frac{\sum_{u\in U}|R(u)\cap T(u)|}{\sum_{u\in U}|T(u)|}$$

推荐系统的覆盖率为：

$$Recall=\frac{\sum_{u\in U}|R(u)|}{|I|}$$

代码如下：


```python
def evaluate(n_sim_user, n_rec_movie):
    N = n_rec_movie
    hit = 0
    rec_count = 0
    test_count = 0
    all_rec_movies = set()
    popular_sum = 0
    movie_count = ratings1m_train.movieId.unique().shape[0]
    
    for uid in train_data.userId.values:
        test_movies = test_data[test_data.userId == uid].movieId
        rec_movies = recommend(uid, n_sim_user, n_rec_movie)
        for movie, w in rec_movies:
            if movie in test_movies.values:
                hit += 1
            all_rec_movies.add(movie)
        rec_count += N
        test_count += test_movies.count()
        
    precision = hit / (1.0*rec_count)
    recall = hit / (1.0*test_count)
    coverage = len(all_rec_movies) / (1.0*movie_count)
    
    return (precision, recall, coverage)
```


```python
print evaluate(20, 10)
```

上面代码运行了一个晚上没有出结果，只得放弃，计算量太大。