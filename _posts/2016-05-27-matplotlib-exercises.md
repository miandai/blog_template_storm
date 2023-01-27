---
layout: post
keywords: blog
description: blog
title: "Matplotlib 练习题"
categories: [Data Analysis]
tags: [Data Analysis]
---
{% include codepiano/setup %}

**1. 绘制一个二维随机漫步的图形**

直接上代码：


```python
%pylab inline
nsteps = 1000
draws = np.random.randint(-1,2,size=(2,nsteps))
walks = draws.cumsum(1)
plot(walks[0,:],walks[1,:]);
```

    Populating the interactive namespace from numpy and matplotlib



![](http://images2015.cnblogs.com/blog/586070/201605/586070-20160527105930022-1129557327.png)

<!--more-->


先生成 1000 个随机漫步方向，方向是从 {-1, 0, 1} 中随机挑两个值（两个值也可相等）作为移动方向，所以每次移动有 3×3=9 种选择，初始位置也是 9 种选择，cumsum 函数是将每次的移动累加，最后通过 plot 画出来。

**2. 画出一个二次函数，同时画出梯形法求积分时的各个梯形**

这里使用 IPython.html.widgets 模块中的 interact 函数，绘制一个交互式的函数图形。可以手动调整梯形个数，看到函数面积随梯形个数而变化。


```python
def Quadratic(x): # 定义二次函数
    return 2*x**2 +3*x +4

import numpy as np
import matplotlib.pyplot as plt
from IPython.html.widgets import interact

def plot_ladder(laddernum):
    x = np.linspace(-5, 5, num=100)
    y = Quadratic(x)
    plot(x,y) # 先画出原函数的图形
    
    a = np.linspace(-5, 5, num=laddernum)
    for i in range(laddernum):
        plot([a[i],a[i]],[0,Quadratic(a[i])],color="black") # 画梯形的上底和下底

    ladders = [];
    for i in range(laddernum):
        ladders.append([a[i],Quadratic(a[i])]) # 因为梯形的腰是呈一条直线，所以这里存下各点坐标
    
    npladders = np.array(ladders)
    plot(npladders[:,0],npladders[:,1]); # 把梯形的斜腰连起来

interact(plot_ladder, laddernum=(1, 30, 1)) # 滑动模块在 1 和 30 之间变化，变化区间是 1
```


![](http://images2015.cnblogs.com/blog/586070/201605/586070-20160527105939069-542551999.png)




**3. 研究 IPython.html.widgets 模块中的 interact 函数，绘制一个交互式的函数图形**

2014 年 4 月，IPython 增加了 interactive widgets，提供了可以交互的界面组件，如下例：


```python
from IPython.html.widgets import interact
%matplotlib inline
import matplotlib.pyplot as plt
import numpy as np
def plot_sine(n):
    x = np.arange(0, 20, 0.1)
    y = np.sin(x/n)
    plot(x,y)
interact(plot_sine, n=(1, 30, 0.1))
```


![](http://images2015.cnblogs.com/blog/586070/201605/586070-20160527105946038-704075999.png)




还可以这样用。


```python
from IPython.html.widgets import interact, IntSlider # 把之前的画图函数改写成依赖于参数的函数
```


```python
# 引入 interact 模块
from IPython.html.widgets import interact, IntSlider

# 把之前的画图函数改写成依赖于参数的函数
def test_interact(min_, max_, steps_):
    x = np.linspace(min_, max_, steps_)
    y = np.sin(x)
    z = np.cos(x)
    plot(x, y)
    scatter(x, y)

# 使用 interact 来交互式的调试参数
interact(test_interact,
         # 为每一个参数设定一个 interact 控件
         min_=IntSlider(min=1,        # 最小值
                              max=10,       # 最大值
                              step=1,       # 每次调节的步长
                              value=1),     # 初始值
         max_=IntSlider(min=10, max=20, step=1, value=10),
         steps_=IntSlider(min=10, max=100, step=10, value=50))
```


![](http://images2015.cnblogs.com/blog/586070/201605/586070-20160527105954397-1592272870.png)




**4. Seaborn**

Matplotlib 是 Python 主要的绘图库，但它本身很复杂，它的图经过大量的调整才能变精致。因此，作为替代，推荐使用Seaborn。Seaborn本质上使用Matplotlib作为核心库（就像Pandas对NumPy一样）。它可以：

- 默认情况下就能创建赏心悦目的图表。
- 创建具有统计意义的图。
- 能理解pandas的DataFrame类型，所以它们一起可以很好地工作。


```python
import seaborn as sns
 
# Load one of the data sets that come with seaborn
tips = sns.load_dataset("tips")
 
sns.jointplot("total_bill", "tip", tips, kind='reg');
```


![](http://images2015.cnblogs.com/blog/586070/201605/586070-20160527110001788-839020034.png)




**参考资料**

- [用 IPython notebook + nvd3 实现交互式调试](https://blog.laisky.com/p/ipython-notebook/)
- [Python 和数据科学的起步指南](http://python.jobbole.com/80853/)