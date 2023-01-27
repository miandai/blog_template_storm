---
layout: post
keywords: blog
description: blog
title: "最优化学习笔记"
categories: [Data_Analysis]
tags: [Data_Analysis]
---
{% include codepiano/setup %}

**注：该文是上了开智学堂数据科学基础班的课后做的笔记，主讲人是肖凯老师。**

# 最优化

为什么要做最优化呢？因为在生活中，人们总是希望幸福值或其它达到一个极值，比如做生意时希望成本最小，收入最大，所以在很多商业情境中，都会遇到求极值的情况。


<!--more-->

## 函数求根

这里「函数的根」也称「方程的根」，或「函数的零点」。

先把我们需要的包加载进来。


```python
import numpy as np
import scipy as sp
import scipy.optimize as opt
import matplotlib.pyplot as plt
%matplotlib inline
```

**函数求根和最优化的关系？什么时候函数是最小值或最大值？**

两个问题一起回答：最优化就是求函数的最小值或最大值，同时也是极值，在求一个函数最小值或最大值时，它所在的位置肯定是导数为 0 的位置，所以要求一个函数的极值，必然要先求导，使其为 0，所以函数求根就是为了得到最大值最小值。

**scipy.optimize 有什么方法可以求根？**

可以用 scipy.optimize 中的 bisect 或 brentq 求根。


```python
f = lambda x: np.cos(x) - x  # 定义一个匿名函数
```


```python
x = np.linspace(-5, 5, 1000) # 先生成 1000 个 x
y = f(x)  # 对应生成 1000 个 f(x)
plt.plot(x, y); # 看一下这个函数长什么样子
plt.axhline(0, color='k'); # 画一根横线，位置在 y=0
```


![](/image/data-analysis/586070-20160616101807713-1732491249.png)





```python
opt.bisect(f, -5, 5) # 求取函数的根
```




    0.7390851332155535




```python
plt.plot(x, y)
plt.axhline(0, color='k')
plt.scatter([_], [0], c='r', s=100); # 这里的 [_] 表示上一个 Cell 中的结果，这里是 x 轴上的位置，0 是 y 上的位置
```

![](/image/data-analysis/586070-20160616101820745-1739535331.png)




求根有两种方法，除了上面介绍的 bisect，还有 brentq，后者比前者快很多。


```python
%timeit opt.bisect(f, -5, 5)
%timeit opt.brentq(f, -5, 5)
```

    10000 loops, best of 3: 157 µs per loop
    The slowest run took 11.65 times longer than the fastest. This could mean that an intermediate result is being cached.
    10000 loops, best of 3: 35.9 µs per loop


## 函数求最小化

求最小值就是一个最优化问题。求最大值时只需对函数做一个转换，比如加一个负号，或者取倒数，就可转成求最小值问题。所以两者是同一问题。

**初始值对最优化的影响是什么？**

举例来说，先定义个函数。


```python
f = lambda x: 1-np.sin(x)/x
x = np.linspace(-20., 20., 1000)
y = f(x)
```

当初始值为 3 值，使用 minimize 函数找到最小值。minimize 函数是在新版的 scipy 里，取代了以前的很多最优化函数，是个通用的接口，背后是很多方法在支撑。


```python
x0 = 3
xmin = opt.minimize(f, x0).x # x0 是起始点，起始点最好离真正的最小值点不要太远
```


```python
plt.plot(x, y)
plt.scatter(x0, f(x0), marker='o', s=300); # 起始点画出来，用圆圈表示
plt.scatter(xmin, f(xmin), marker='v', s=300); # 最小值点画出来，用三角表示
plt.xlim(-20, 20);
```

![](/image/data-analysis/586070-20160616101837745-1551390189.png)




初始值为 3 时，成功找到最小值。

现在来看看初始值为 10 时，找到的最小值点。


```python
x0 = 10
xmin = opt.minimize(f, x0).x
```


```python
plt.plot(x, y)
plt.scatter(x0, f(x0), marker='o', s=300)
plt.scatter(xmin, f(xmin), marker='v', s=300)
plt.xlim(-20, 20);
```


![](/image/data-analysis/586070-20160616101849010-1654891594.png)




由上图可见，当初始值为 10 时，函数找到的是局部最小值点，可见 minimize 的默认算法对起始点的依赖性。

那么怎么才能不管初始值在哪个位置，都能找到全局最小值点呢？

**如何找到全局最优点？**

可以使用 basinhopping 函数找到全局最优点，相关背后算法，可以看帮助文件，有提供论文的索引和出处。

我们设初始值为 10 看是否能找到全局最小值点。


```python
x0 = 10
from scipy.optimize import basinhopping 
xmin = basinhopping(f,x0,stepsize = 5).x
```


```python
plt.plot(x, y);
plt.scatter(x0, f(x0), marker='o', s=300);
plt.scatter(xmin, f(xmin), marker='v', s=300);
plt.xlim(-20, 20);
```


![](/image/data-analysis/586070-20160616101900995-713266258.png)




当起始点在比较远的位置，依然成功找到了全局最小值点。

**如何求多元函数最小值？**

以二元函数为例，使用 minimize 求对应的最小值。


```python
def g(X):
    x,y = X
    return (x-1)**4 + 5 * (y-1)**2 - 2*x*y
```


```python
X_opt = opt.minimize(g, (8, 3)).x # (8,3) 是起始点
print X_opt
```

    [ 1.88292611  1.37658521]



```python
fig, ax = plt.subplots(figsize=(6, 4)) # 定义画布和图形
x_ = y_ = np.linspace(-1, 4, 100) 
X, Y = np.meshgrid(x_, y_)
c = ax.contour(X, Y, g((X, Y)), 50) # 等高线图
ax.plot(X_opt[0], X_opt[1], 'r*', markersize=15) # 最小点的位置是个元组
ax.set_xlabel(r"$x_1$", fontsize=18)
ax.set_ylabel(r"$x_2$", fontsize=18)
plt.colorbar(c, ax=ax) # colorbar 表示颜色越深，高度越高
fig.tight_layout()
```


![](/image/data-analysis/586070-20160616101936713-1754311942.png)




画 3D 图。


```python
from mpl_toolkits.mplot3d import Axes3D
from matplotlib import cm
fig = plt.figure()
ax = fig.gca(projection='3d')
x_ = y_ = np.linspace(-1, 4, 100) 
X, Y = np.meshgrid(x_, y_)
surf = ax.plot_surface(X, Y, g((X,Y)), rstride=1, cstride=1, cmap=cm.coolwarm,
                       linewidth=0, antialiased=False)
cset = ax.contour(X, Y, g((X,Y)), zdir='z',offset=-5, cmap=cm.coolwarm)
fig.colorbar(surf, shrink=0.5, aspect=5);
```

![](/image/data-analysis/586070-20160616102006901-898100768.png)




## 曲线拟合

**曲线拟合和最优化有什么关系？**

曲线拟合的问题是，给定一组数据，它可能是沿着一条线散布的，这时要找到一条最优的曲线来拟合这些数据，也就是要找到最好的线来代表这些点，这里的最优是指这些点和线之间的距离是最小的，这就是为什么要用最优化问题来解决曲线拟合问题。

举例说明，给一些点，找到一条线，来拟合这些点。

先给定一些点：


```python
N = 50 # 点的个数
m_true = 2 # 斜率
b_true = -1 # 截距
dy = 2.0 # 误差

np.random.seed(0)
xdata = 10 * np.random.random(N) # 50 个 x，服从均匀分布
ydata = np.random.normal(b_true + m_true * xdata, dy) # dy 是标准差

plt.errorbar(xdata, ydata, dy,
             fmt='.k', ecolor='lightgray');
```


![](/image/data-analysis/586070-20160616102020354-526451002.png)




上面的点整体上呈现一个线性关系，要找到一条斜线来代表这些点，这就是经典的一元线性回归。目标就是找到最好的线，使点和线的距离最短。要优化的函数是点和线之间的距离，使其最小。点是确定的，而线是可变的，线是由参数值，斜率和截距决定的，这里就是要通过优化距离找到最优的斜率和截距。

点和线的距离定义如下：


```python
def chi2(theta, x, y):
    return np.sum(((y - theta[0] - theta[1] * x)) ** 2)
```

上式就是误差平方和。

**误差平方和是什么？有什么作用？**

误差平方和公式为：

$$\sum_{i=1}^{N}(y^{(i)}-\theta_{0}-\theta_{1}x^{(i)})^2$$

误差平方和大，表示真实的点和预测的线之间距离太远，说明拟合得不好，最好的线，应该是使误差平方和最小，即最优的拟合线，这里是条直线。

误差平方和就是要最小化的目标函数。

找到最优的函数，即斜率和截距。


```python
theta_guess = [0, 1] # 初始值
theta_best = opt.minimize(chi2, theta_guess, args=(xdata, ydata)).x
print(theta_best)
```

    [-1.01442005  1.93854656]


上面两个输出即是预测的直线斜率和截距，我们是根据点来反推直线的斜率和截距，那么真实的斜率和截距是多少呢？-1 和 2，很接近了，差的一点是因为有噪音的引入。


```python
xfit = np.linspace(0, 10)
yfit = theta_best[0] + theta_best[1] * xfit

plt.errorbar(xdata, ydata, dy,
             fmt='.k', ecolor='lightgray');
plt.plot(xfit, yfit, '-k');
```


![](/image/data-analysis/586070-20160616102110495-391797955.png)




**最小二乘（Least Square）是什么？**

上面用的是 minimize 方法，这个问题的目标函数是误差平方和，这就又有一个特定的解法，即最小二乘。

最小二乘的思想就是要使得观测点和估计点的距离的平方和达到最小，这里的“二乘”指的是用平方来度量观测点与估计点的远近（在古汉语中“平方”称为“二乘”），“最小”指的是参数的估计值要保证各个观测点与估计点的距离的平方和达到最小。

关于最小二乘估计的计算，涉及更多的数学知识，这里不想详述，其一般的过程是用目标函数对各参数求偏导数，并令其等于 0，得到一个线性方程组。具体推导过程可参考[斯坦福机器学习讲义](http://cs229.stanford.edu/notes/cs229-notes1.pdf) 第 7 页。


```python
def deviations(theta, x, y):
    return (y - theta[0] - theta[1] * x)

theta_best, ier = opt.leastsq(deviations, theta_guess, args=(xdata, ydata))
print(theta_best)
```

    [-1.01442016  1.93854659]


最小二乘 leastsq 的结果跟 minimize 结果一样。注意 leastsq 的第一个参数不再是误差平方和 chi2，而是误差本身 deviations，即没有平方，也没有和。


```python
yfit = theta_best[0] + theta_best[1] * xfit

plt.errorbar(xdata, ydata, dy,
             fmt='.k', ecolor='lightgray');
plt.plot(xfit, yfit, '-k');
```

![](/image/data-analysis/586070-20160616102131010-1628578612.png)




**非线性最小二乘**

上面是给一些点，拟合一条直线，拟合一条曲线也是一样的。


```python
def f(x, beta0, beta1, beta2):  # 首先定义一个非线性函数，有 3 个参数
    return beta0 + beta1 * np.exp(-beta2 * x**2)

beta = (0.25, 0.75, 0.5) # 先猜 3 个 beta
xdata = np.linspace(0, 5, 50)
y = f(xdata, *beta)
ydata = y + 0.05 * np.random.randn(len(xdata)) # 给 y 加噪音

def g(beta):
    return ydata - f(xdata, *beta)  # 真实 y 和 预测值的差，求最优曲线时要用到

beta_start = (1, 1, 1)
beta_opt, beta_cov = opt.leastsq(g, beta_start)
print beta_opt # 求到的 3 个最优的 beta 值
```

    [ 0.25525709  0.74270226  0.54966466]


拿估计的 beta_opt 值跟真实的 beta = (0.25, 0.75, 0.5) 值比较，差不多。


```python
fig, ax = plt.subplots()
ax.scatter(xdata, ydata)  # 画点
ax.plot(xdata, y, 'r', lw=2) # 真实值的线
ax.plot(xdata, f(xdata, *beta_opt), 'b', lw=2) # 拟合的线
ax.set_xlim(0, 5)
ax.set_xlabel(r"$x$", fontsize=18)
ax.set_ylabel(r"$f(x, \beta)$", fontsize=18)
fig.tight_layout()
```


![](/image/data-analysis/586070-20160616102143463-1679381260.png)




除了使用最小二乘，还可以使用曲线拟合的方法，得到的结果是一样的。


```python
beta_opt, beta_cov = opt.curve_fit(f, xdata, ydata)
print beta_opt
```

    [ 0.25525709  0.74270226  0.54966466]


## 有约束的最小化

有约束的最小化是指，要求函数最小化之外，还要满足约束条件，举例说明。

**边界约束**


```python
def f(X):
    x, y = X
    return (x-1)**2 + (y-1)**2 # 这是一个碗状的函数
```


```python
x_opt = opt.minimize(f, (0, 0), method='BFGS').x # 无约束最优化
```

假设有约束条件，x 和 y 要在一定的范围内，如 x 在 2 到 3 之间，y 在 0 和 2 之间。


```python
bnd_x1, bnd_x2 = (2, 3), (0, 2) # 对自变量的约束
```


```python
x_cons_opt = opt.minimize(f, np.array([0, 0]), method='L-BFGS-B', bounds=[bnd_x1, bnd_x2]).x # bounds 矩形约束
```


```python
fig, ax = plt.subplots(figsize=(6, 4))
x_ = y_ = np.linspace(-1, 3, 100)
X, Y = np.meshgrid(x_, y_)
c = ax.contour(X, Y, f((X,Y)), 50)
ax.plot(x_opt[0], x_opt[1], 'b*', markersize=15) # 没有约束下的最小值，蓝色五角星
ax.plot(x_cons_opt[0], x_cons_opt[1], 'r*', markersize=15) # 有约束下的最小值，红色星星
bound_rect = plt.Rectangle((bnd_x1[0], bnd_x2[0]), 
                           bnd_x1[1] - bnd_x1[0], bnd_x2[1] - bnd_x2[0],
                           facecolor="grey")
ax.add_patch(bound_rect)
ax.set_xlabel(r"$x_1$", fontsize=18)
ax.set_ylabel(r"$x_2$", fontsize=18)
plt.colorbar(c, ax=ax)
fig.tight_layout()
```

![](/image/data-analysis/586070-20160616102157745-690406391.png)



**不等式约束**

介绍下相关理论，先来看下存在等式约束的极值问题求法，比如下面的优化问题。

$$min_{w}f(w)$$
$$s.t. h_{i}(w)=0,i=1,...,l.$$

目标函数是 f(w)，下面是等式约束，通常解法是引入拉格朗日算子，这里使用 $\beta$ 来表示算子，得到拉格朗日公式为

$$L(w,\beta)=f(w)+\sum_{i=1}^{l}\beta_{i}h_{i}(w)$$

l 是等式约束的个数。

然后分别对 w 和 $\beta$ 求偏导，使得偏导数等于 0，然后解出 w 和 $\beta_{i}$，至于为什么引入拉格朗日算子可以求出极值，原因是 f(w) 的 dw 变化方向受其他不等式的约束，dw的变化方向与f(w)的梯度垂直时才能获得极值，而且在极值处，f(w) 的梯度与其他等式梯度的线性组合平行，因此他们之间存在线性关系。（参考《最优化与KKT条件》）

对于不等式约束的极值问题

$$min_{w}f(w)$$
$$s.t. g_{i}(w)\leq0,i=1,...,k.$$
$$s.t. h_{i}(w)=0,i=1,...,l.$$

常常利用拉格朗日对偶性将原始问题转换为对偶问题，通过解对偶问题而得到原始问题的解。该方法应用在许多统计学习方法中。有兴趣的可以参阅相关资料，这里不再赘述。


```python
def f(X):
    return (X[0] - 1)**2 + (X[1] - 1)**2

def g(X):
    return X[1] - 1.75 - (X[0] - 0.75)**4

x_opt = opt.minimize(f, (0, 0), method='BFGS').x
constraints = [dict(type='ineq', fun=g)] # 约束采用字典定义，约束方式为不等式约束，边界用 g 表示
x_cons_opt = opt.minimize(f, (0, 0), method='SLSQP', constraints=constraints).x
```


```python
fig, ax = plt.subplots(figsize=(6, 4))
x_ = y_ = np.linspace(-1, 3, 100)
X, Y = np.meshgrid(x_, y_)
c = ax.contour(X, Y, f((X, Y)), 50)
ax.plot(x_opt[0], x_opt[1], 'b*', markersize=15) # 蓝色星星，没有约束下的最小值

ax.plot(x_, 1.75 + (x_-0.75)**4, 'k-', markersize=15)
ax.fill_between(x_, 1.75 + (x_-0.75)**4, 3, color="grey")
ax.plot(x_cons_opt[0], x_cons_opt[1], 'r*', markersize=15) # 在区域约束下的最小值

ax.set_ylim(-1, 3)
ax.set_xlabel(r"$x_0$", fontsize=18)
ax.set_ylabel(r"$x_1$", fontsize=18)
plt.colorbar(c, ax=ax)
fig.tight_layout()
```


![](/image/data-analysis/586070-20160616102210917-1161511278.png)




scipy.optimize.minimize 中包括了多种最优化算法，每种算法使用范围不同，详细参考[官方文档](http://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html)。

## 补充阅读

- [scipy.optimize 官方文档](http://docs.scipy.org/doc/scipy/reference/tutorial/optimize.html)
- [《Scipy Lectures》 第 13 章](http://www.scipy-lectures.org/advanced/mathematical_optimization/)
- [《Numerical Python》](https://book.douban.com/subject/26643233/) 第 6 章
- [《最优化导论》](https://book.douban.com/subject/3061693/)