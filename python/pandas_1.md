
# pandas--基本数据结构介绍

## Pandas介绍
在处理实际的金融数据时，一个条数据通常包含了多种类型的数据，例如，股票的代码是字符串，收盘价是浮点型，而成交量是整型等。在C++中可以实现为一个给定结构体作为单元的容器，如向量（vector，C++中的特定数据结构）。在Python中，pandas包含了高级的数据结构Series和DataFrame，使得在Python中处理数据变得非常方便、快速和简单。

pandas主要的两个数据结构是Series和DataFrame，随后两节将介绍如何由其他类型的数据结构得到这两种数据结构，或者自行创建这两种数据结构，我们先导入它们以及相关模块：

```python
import numpy as np
from pandas import Series, DataFrame
```

## Pandas数据结构：Series
从一般意义上来讲，Series可以简单地被认为是**一维的数组**。Series和一维数组最主要的区别在于Series类型**具有索引**（index），**可以和另一个编程中常见的数据结构哈希（Hash）联系起来**。

### 创建series
`s = Series(data, index=index, name=name)`
可以在创建Series时添加index，并可使用Series.index查看具体的index。需要注意的一点是，当从数组创建Series时，若指定index，**那么index长度要和data的长度一致：**
创建Series的另一个可选项是name，可指定Series的名称，可用Series.name访问。在随后的DataFrame中，每一列的列名在该列被单独取出来时就成了Series的名称
```python
s = Series(np.random.randn(5), index=['a', 'b', 'c', 'd', 'e'], name='my_series')
print s
print s.name
```
```
a   -1.898245
b    0.172835
c    0.779262
d    0.289468
e   -0.947995
Name: my_series, dtype: float64
my_series
```

s还可以从字典中创造（index长度不必和字典相同）：
```python
d = {'a': 0., 'b': 1, 'c': 2}
print "d is a dict:"
print d
s = Series(d)
print "s is a Series:"
```
```
d is a dict:
{'a': 0.0, 'c': 2, 'b': 1}
s is a Series:
a    0
b    1
c    2
dtype: float64
```
我们可以观察到两点：
-  字典创建的Series，数据将按index的顺序重新排列；
-  index长度可以和字典长度不一致，如果多了的话，pandas将自动为多余的index分配NaN（not a number，pandas中数据缺失的标准记号)，当然index少的话就截取部分的字典内容。
- 如果数据就是一个单一的变量，如数字4，那么Series将重复这个变量：

### series 数据的访问
访问Series数据可以
- 和数组一样使用下标，
- 和字典一样使用索引，
- 使用一些条件过滤：

```python
s = Series(np.random.randn(10),index=['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j'])s[0]
s[:2]
s[[2,0,4]]
s[['e', 'i']]
s[s > 0.5]
'e' in s
```

## dataframe
DataFrame是将数个Series按列合并而成的二维数据结构，每一列单独取出来是一个Series，这和SQL数据库中取出的数据是很类似的。所以，按列对一个DataFrame进行处理更为方便，用户在编程时注意培养按列构建数据的思维。DataFrame的优势在于可以方便地处理不同类型的列，因此，就不要考虑如何对一个全是浮点数的DataFrame求逆之类的问题了，处理这种问题还是把数据存成NumPy的matrix类型比较便利一些。
### 创建dataframe

#### 从字典创建
首先来看如何从字典创建DataFrame。DataFrame是一个二维的数据结构，是多个Series的集合体。我们先创建一个值是Series的字典，并转换为DataFrame
```python
d = {'one': Series([1., 2., 3.], index=['a', 'b', 'c']), 'two': Series([1., 2., 3., 4.], index=['a', 'b', 'c', 'd'])}
df = DataFrame(d)
print df
```
```
   one  two
a    1    1
b    2    2
c    3    3
d  NaN    4
```
可以指定所需的行和列，若字典中不含有对应的元素，则置为NaN：

```
df = DataFrame(d, index=['r', 'd', 'a'], columns=['two', 'three'])
print df
```
```
   two three
r  NaN   NaN
d    4   NaN
a    1   NaN
```
可以使用dataframe.index和dataframe.columns来查看DataFrame的行和列，dataframe.values则以数组的形式返回DataFrame的元素：


DataFrame也可以从值是数组的字典创建，但是各个数组的长度需要相同：

```python
d = {'one': [1., 2., 3., 4.], 'two': [4., 3., 2., 1.]}
df = DataFrame(d, index=['a', 'b', 'c', 'd'])
print df
```

#### 从列表中创建
值非数组时，没有这一限制，并且缺失值补成NaN：
```python
d = {'one': [1., 2., 3., 4.], 'two': [4., 3., 2., 1.]}
df = DataFrame(d, index=['a', 'b', 'c', 'd'])
print df
```

