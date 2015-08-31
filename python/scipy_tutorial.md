
# scipy
原文 : <https://uqer.io/community/share/54d83bb3f9f06c276f651a6e>

NumPy替我们搞定了向量和矩阵的相关操作，基本上算是一个高级的科学计算器。SciPy基于NumPy提供了更为丰富和高级的功能扩展，在统计、优化、插值、数值积分、时频转换等方面提供了大量的可用函数，基本覆盖了基础科学计算相关的问题。

在量化分析中，运用最广泛的是统计和优化的相关技术
```
import numpy as np
import scipy.stats as stats
import scipy.optimize as opt
```
## 统计

###　生成随机数
生成n个随机数可用rv_continuous.rvs(size=n)或rv_discrete.rvs(size=n)，其中rv_continuous表示连续型的随机分布，如均匀分布（uniform）、正态分布（norm）、贝塔分布（beta）等；rv_discrete表示离散型的随机分布，如伯努利分布（bernoulli）、几何分布（geom）、泊松分布（poisson）等。我们生成10个[0, 1]区间上的随机数和10个服从参数a=4，b=2的贝塔分布随机数：
```python
rv_unif = stats.uniform.rvs(size=10)
print rv_unif
rv_beta = stats.beta.rvs(size=10, a=4, b=2)
print rv_beta
```
在每个随机分布的生成函数里，都内置了默认的参数，如均匀分布的上下界默认是0和1。可是一旦需要修改这些参数，每次生成随机都要敲这么老长一串有点麻烦，能不能简单点？SciPy里头有一个Freezing的功能，可以提供简便版本的命令。SciPy.stats支持定义出某个具体的分布的对象，我们可以做如下的定义，让beta直接指代具体参数a=4和b=2的贝塔分布。为让结果具有可比性，这里指定了随机数的生成种子，由NumPy提供。
```python
np.random.seed(seed=2015)
rv_beta = stats.beta.rvs(size=10, a=4, b=2)
print "method 1:"
print rv_beta

np.random.seed(seed=2015)
beta = stats.beta(a=4, b=2)
print "method 2:"
print beta.rvs(size=10)
```

### 假设检验
```python
stat_val, p_val = stats.kstest(dat, 'norm', (mu, sigma))
print 'KS-statistic D = %6.3f p-value = %6.4f' % (stat_val, p_val)
```

假设这个数据是我们获取到的实际的某些数据，如股票日涨跌幅，我们对数据进行简单的分析。最简单的是检验这一组数据是否服从假设的分布，如正态分布。这个问题是典型的单样本假设检验问题，最为常见的解决方案是采用K-S检验（ Kolmogorov-Smirnov test）。单样本K-S检验的原假设是给定的数据来自和原假设分布相同的分布，在SciPy中提供了kstest函数，参数分别是数据、拟检验的分布名称和对应的参数

### 其他函数

#### cdf和pdf
PDF是概率密度（函数）
CDF是（累积）分布函数
有时需要知道某数值在一个分布中的分位，或者给定了一个分布，求某分位上的数值。这可以通过cdf和ppf函数完成：
```python
g_dist = stats.gamma(a=2)
print "quantiles of 2, 4 and 5:"
print g_dist.cdf([2, 4, 5])
print "Values of 25%, 50% and 90%:"
print g_dist.pdf([0.25, 0.5, 0.95])
```

#### describe
describe函数提供对数据集的统计描述分析，包括数据样本大小，极值，均值，方差，偏度和峰度：


```python
norm_dist = stats.norm(loc=0, scale=1.8)
dat = norm_dist.rvs(size=100)
info = stats.describe(dat)
print "Data size is: " + str(info[0])
print "Minimum value is: " + str(info[1][0])
print "Maximum value is: " + str(info[1][1])
print "Arithmetic mean is: " + str(info[2])
print "Unbiased variance is: " + str(info[3])
print "Biased skewness is: " + str(info[4])
print "Biased kurtosis is: " + str(info[5])
```
#### moment
对于一个给定的分布，可以用moment很方便的查看分布的矩信息

```
stats.norm.moment(6, loc=0, scale=1)
```
#### fit
们知道一组数据服从某些分布的时候，可以调用fit函数来得到对应分布参数的极大似然估计（MLE, maximum-likelihood estimation）。以下代码示例了假设数据服从正态分布，用极大似然估计分布参数

#### pearsonr和spearmanr
pearsonr和spearmanr可以计算Pearson和Spearman相关系数，这两个相关系数度量了两组数据的相互线性关联程度[^相关系数]
[^相关系数]: pearson相关和 spearman 的区别 <http://blog.sina.com.cn/s/blog_497b91d70101h41b.html>
```python
cor, pval = stats.pearsonr(dat1, dat2)
print "Pearson correlation coefficient: " + str(cor)
cor, pval = stats.pearsonr(dat1, dat2)
print "Spearman's rank correlation coefficient: " + str(cor)
```

#### 线性回归
```python
x = stats.chi2.rvs(3, size=50)
y = 2.5 + 1.2 * x + stats.norm.rvs(size=50, loc=0, scale=1.5)
slope, intercept, r_value, p_value, std_err = stats.linregress(x, y)
print "Slope of fitted model is:" , slope
print "Intercept of fitted model is:", intercept
print "R-squared:", r_value**2
```

在[官方wiki](http://wiki.scipy.org/)，可以查到大部分stat中的函数，本节权作简单介绍，挖掘更多功能的最好方法还是直接读原始的文档。另外，[StatsModels](http://statsmodels.sourceforge.net)模块提供了更为专业，更多的统计相关函数。若在SciPy没有满足需求，可以采用StatsModels。

## 优化问题
### 无约束优化问题

作为寻优的目标函数来简要介绍在SciPy中使用优化模块scipy.optimize。
首先需要定义一下这个Rosenbrock函数：
$$\sum_0^{N-1}100(x_i-x_{i-1}^2)^2+(1-x_{i-1})^2$$
```python
def rosen(x):
    """The Rosenbrock function"""
    return sum(100.0*(x[1:]-x[:-1]**2.0)**2.0 + (1-x[:-1])**2.0)
```

#### Nelder-Mead单纯形法
```python
x_0 = np.array([0.5, 1.6, 1.1, 0.8, 1.2])
res = opt.minimize(rosen, x_0, method='nelder-mead', options={'xtol': 1e-8, 'disp': True})
print "Result of minimizing Rosenbrock function via Nelder-Mead Simplex algorithm:"
print res
```
Rosenbrock函数的性质比较好，简单的优化方法就可以处理了，还可以在minimize中使用method='powell'来指定使用Powell's method。这两种简单的方法并不使用函数的梯度，在略微复杂的情形下收敛速度比较慢，下面让我们来看一下用到函数梯度进行寻优的方法。


#### Broyden-Fletcher-Goldfarb-Shanno法(BFGS)
（BFGS）法用到了梯度信息，首先求一下Rosenbrock函数的梯度：
$$
{\partial f \over \partial x_j} =  200(x_j-x_{j-1}^2)	- 400 x_j(x_{j+1}	-	x_j^2)-2*(1-x_j)$$

边界的梯度是特例
$$
{\partial f \over \partial x_0} = - 400 x_j(x_{1}	-	x_0^2)-2*(1-x_0)\\
{\partial f \over \partial N-1} =  200(x_{N-1}-x_{N-2}^2)
$$
们可以如下定义梯度向量的计算函数了：
```python
def rosen_der(x):
    xm = x[1:-1]
    xm_m1 = x[:-2]
    xm_p1 = x[2:]
    der = np.zeros_like(x)
    der[1:-1] = 200*(xm-xm_m1**2) - 400*(xm_p1 - xm**2)*xm - 2*(1-xm)
    der[0] = -400*x[0]*(x[1]-x[0]**2) - 2*(1-x[0])
    der[-1] = 200*(x[-1]-x[-2]**2)
    return der
```
梯度信息的引入在minimize函数中通过参数jac指定：
```python
res = opt.minimize(rosen, x_0, method='BFGS', jac=rosen_der, options={'disp': True})
print "Result of minimizing Rosenbrock function via Broyden-Fletcher-Goldfarb-Shanno algorithm:"
print res
```
#### 牛顿共轭梯度法
用到梯度的方法还有牛顿法，牛顿法是收敛速度最快的方法，其缺点在于要求Hessian矩阵（二阶导数矩阵）。牛顿法大致的思路是采用泰勒展开的二阶近似：
$$
f(x) \approx f(x_0)+\nabla f(x_0)(x-x_0) + \frac12(x-x_0)^TH(x_0)(x-x_0)
$$
若Hessian矩阵是正定的，函数的局部最小值可以通过使上面的**二次型的一阶导数**等于0来获取，我们有:
$$
x_{opt} = x_0-H^{-1}\nabla f
$$
**这里可使用共轭梯度近似**Hessian矩阵的逆矩阵。
 为使用牛顿共轭梯度法，我们需要提供一个计算Hessian矩阵的函数：
```python
 def rosen_hess(x):
    x = np.asarray(x)
    H = np.diag(-400*x[:-1],1) - np.diag(400*x[:-1],-1)
    diagonal = np.zeros_like(x)
    diagonal[0] = 1200*x[0]**2-400*x[1]+2
    diagonal[-1] = 200
    diagonal[1:-1] = 202 + 1200*x[1:-1]**2 - 400*x[2:]
    H = H + np.diag(diagonal)
    return H
```

```python
res = opt.minimize(rosen, x_0, method='Newton-CG', jac=rosen_der, hess=rosen_hess, options={'xtol': 1e-8, 'disp': True})
print "Result of minimizing Rosenbrock function via Newton-Conjugate-Gradient algorithm (Hessian):"
print res
```

对于一些大型的优化问题，Hessian矩阵将异常大，牛顿共轭梯度法用到的仅是Hessian矩阵和一个任意向量的乘积，为此，用户可以提供两个向量，一个是Hessian矩阵和一个任意向量p的乘积，另一个是向量p，这就减少了存储的开销。
```python
def rosen_hess_p(x, p):
    x = np.asarray(x)
    Hp = np.zeros_like(x)
    Hp[0] = (1200*x[0]**2 - 400*x[1] + 2)*p[0] - 400*x[0]*p[1]
    Hp[1:-1] = -400*x[:-2]*p[:-2]+(202+1200*x[1:-1]**2-400*x[2:])*p[1:-1] \
               -400*x[1:-1]*p[2:]
    Hp[-1] = -400*x[-2]*p[-2] + 200*p[-1]
    return Hp

res = opt.minimize(rosen, x_0, method='Newton-CG', jac=rosen_der, hessp=rosen_hess_p, options={'xtol': 1e-8, 'disp': True})
print "Result of minimizing Rosenbrock function via Newton-Conjugate-Gradient algorithm (Hessian times arbitrary vector):"
print res
```
##  约束优化问题

我们考察如下一个例子：
$$\begin{align}
min\quad	f(x,y)&= 2xy+2x-x^2-2y^2\\
 s.t. \quad 
 x^3-y&=0\\
y-1&\ge 0
\end{align}
$$

定义目标函数及其导数为：
```python
def func(x, sign=1.0):
    """ Objective function """
    return sign*(2*x[0]*x[1] + 2*x[0] - x[0]**2 - 2*x[1]**2)

def func_deriv(x, sign=1.0):
    """ Derivative of objective function """
    dfdx0 = sign*(-2*x[0] + 2*x[1] + 2)
    dfdx1 = sign*(2*x[0] - 4*x[1])
    return np.array([ dfdx0, dfdx1 ])
```

```python
cons = ({'type': 'eq',  'fun': lambda x: np.array([x[0]**3 - x[1]]), 'jac': lambda x: np.array([3.0*(x[0]**2.0), -1.0])},
      {'type': 'ineq', 'fun': lambda x: np.array([x[1] - 1]), 'jac': lambda x: np.array([0.0, 1.0])})
  res = opt.minimize(func, [-1.0, 1.0], args=(-1.0,), jac=func_deriv, method='SLSQP', options={'disp': True})
print "Result of unconstrained optimization:"
print res
res = opt.minimize(func, [-1.0, 1.0], args=(-1.0,), jac=func_deriv, constraints=cons, method='SLSQP', options={'disp': True})
print "Result of constrained optimization:"
print res
```
和统计部分一样，Python也有专门的优化扩展模块，CVXOPT（http://cvxopt.org ）专门用于处理凸优化问题，在约束优化问题上提供了更多的备选方法。CVXOPT是著名的凸优化教材convex optimization的作者之一，加州大学洛杉矶分校Lieven Vandenberghe教授的大作，是处理优化问题的利器。

SciPy中的优化模块还有一些特殊定制的函数，专门处理能够转化为优化求解的一些问题，如方程求根、最小方差拟合等，可到SciPy优化部分的指引页面查看。
