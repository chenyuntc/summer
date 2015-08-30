# numpy 

## 基础篇
NumPy的主要对象是**同种元素的多维数组**。这是一个**所有的元素都是一种类型**、通过一个正整数元组索引的元素表格(通常是元素是数字)。在NumPy中维度(dimensions)叫做轴(axes)，轴的个数叫做秩(rank)。

下面数组的秩为2(它有两个维度).第一个维度长度为2,第二个维度长度为3.
```
[[ 1., 0., 0.],
 [ 0., 1., 2.]]
```

NumPy的数组类被称作 ndarray 
更多重要ndarray对象属性有：
- ndarray.ndim
	数组轴的个数，在python的世界中，轴的个数被称作秩
- ndarray.shape
	数组的维度。这是一个指示数组在每个维度上大小的整数元组。例如一个n排m列的矩阵，它的shape属性将是(2,3),这个元组的长度显然是秩，即维度或者ndim属性
- ndarray.size
	数组元素的总个数，等于shape属性中元组元素的乘积。
- ndarray.dtype
	一个用来描述数组中元素类型的对象，可以通过创造或指定dtype使用标准Python类型。另外NumPy提供它自己的数据类型。
- ndarray.itemsize
	数组中每个元素的字节大小。例如，一个元素类型为float64的数组itemsiz属性值为8(=64/8),又如，一个元素类型为complex32的数组item属性为4(=32/8).
- ndarray.data
	包含实际数组元素的缓冲区，通常我们不需要使用这个属性，因为我们总是通过索引来使用数组中的元素。

### 创建数组

- `np.array(list)`或者`np.array(tuple)`
- `zeros`和`ones`以及`empty`
- `arrange(start,end,step)`
- `linspace(start,end,number)`
- `rand`	以及`randn`
- `fromfunction`
```
>>> np.fromfunction(lambda i, j: i + j, (3, 3), dtype=int)
array([[0, 1, 2],
       [1, 2, 3],
       [2, 3, 4]])
```

### 矩阵运算
数组的算术运算是按元素的。新的数组被创建并且被结果填充。
不像许多矩阵语言，NumPy中的乘法运算符 * 指示按元素计算，矩阵乘法可以使用 dot 函数或创建矩阵对象实现(参见教程中的矩阵章节)
当运算的是不同类型的数组时，结果数组和更普遍和精确的已知(这种行为叫做upcast)。

这些运算默认应用到数组好像它就是一个数字组成的列表，无关数组的形状。然而，指定 axis 参数你可以吧运算应用到数组指定的轴上(axis=0 是对一列元素做运算)

### 通用函数
NumPy提供常见的数学函数如 `sin` , `cos` 和 `exp` 。在NumPy中，这些叫作“通用函数”(`ufunc`)。在NumPy里这些函数作用按数组的元素运算，产生一个数组作为输出。

### 索引切片和迭代

#### 一维数组
与列表用法一样 比如`a[2:5]`还有`a[ : :-1]  `还有`a[:6:2] = -1000 `

### 多维数组
多维 数组可以每个轴有一个索引。这些索引由一个逗号分割的元组给出。
当少于轴数的索引被提供时，确失的索引被认为是整个切片：

    b[-1]                                  # the last row. Equivalent to b[-1,:]
array([40, 41, 42, 43])

`b[i]` 中括号中的表达式被当作 i 和一系列 : ，来代表剩下的轴。NumPy也允许你使用“点”像 `b[i,...]` 。**点 (…)代表许多产生一个完整的索引元组必要的分号。**

迭代 多维数组是就第一个轴而言的: 
然而，如果一个人想对每个数组中元素进行运算，我们可以使用**flat属性**，该属性是数组元素的一个迭代器:

### 形状操作
#### 更改形状
- `ravel()`和`reshape`以及`resize`
	reshape 函数改变参数形状并返回它，而 resize 函数改变数组自身。
	如果在改变形状操作中一个维度被给做-1，其维度将自动被计算

####　组合(组合不同的数组)

- `vstack` 和`hstack` 
-  函数 column_stack 以列将一维数组合成二维数组，它等同与 vstack 对一维数组。row_stack 函数，另一方面，将一维数组以行组合成二维数组。
-  对那些维度比二维更高的数组， hstack 沿着第二个轴组合， vstack 沿着第一个轴组合, concatenate 允许可选参数给出组合时沿着的轴。
-  在复杂情况下， r_[] 和 c_[] 对创建沿着一个方向组合的数很有用，它们允许范围符号(“:”):
```
>>> r_[1:4,0,4]
array([1, 2, 3, 0, 4])
```
#### 将一个数组分割(split)成几个小数组

- ` hsplit(a,3)   # Split a into 3`
-  ` hsplit(a,(3,4))   # Split a after the third and the fourth column`
-  vsplit 沿着纵向的轴分割， array split 允许指定沿哪个轴分割。

### 复制和视图
#### 不完全拷贝
简单的赋值不拷贝数组对象或它们的数据。

Python 传递不定对象作为参考  ，所以函数调用不拷贝数组。
```
>>> a = arange(12)
>>> b = a            # no new object is created
>>> b is a           # a and b are two names for the same ndarray object
True
>>> b.shape = 3,4    # changes the shape of a
>>> a.shape
(3, 4)
```

#### 视图(view)和浅复制
不同的数组对象分享同一个数据。视图方法创造一个新的数组对象指向同一数据。
```
>>> c = a.view()
>>> c is a
False
>>> c.base is a                        # c is a view of the data owned by a
True
>>> c.flags.owndata
False
>>>
>>> c.shape = 2,6                      # a's shape doesn't change
>>> a.shape
(3, 4)
>>> c[0,4] = 1234                      # a's data changes
>>> a
array([[   0,    1,    2,    3],
       [1234,    5,    6,    7],
       [   8,    9,   10,   11]])
```
切片数组返回它的一个视图：

### 深复制

这个复制方法完全复制数组和它的数据。

```
>>> d = a.copy()                          # a new array object with new data is created
>>> d is a
False
>>> d.base is a                           # d doesn't share anything with a
False
>>> d[0,0] = 9999
>>> a
array([[   0,   10,   10,    3],
       [1234,   10,   10,    7],
       [   8,   10,   10,   11]])
```

### 函数总览

函数和方法(method)总览

- 创建数组
```
arange, array, copy, empty, empty_like, eye, fromfile, fromfunction, identity, linspace, logspace, mgrid, ogrid, ones, ones_like, r , zeros, zeros_like 
```
- 转化

```
astype, atleast 1d, atleast 2d, atleast 3d, mat 
```

- 操作

```
array split, column stack, concatenate, diagonal, dsplit, dstack, hsplit, hstack, item, newaxis, ravel, repeat, reshape, resize, squeeze, swapaxes, take, transpose, vsplit, vstack 
```

- 询问

```
all, any, nonzero, where 
```

- 排序

```
argmax, argmin, argsort, max, min, ptp, searchsorted, sort 
```

- 运算

```
choose, compress, cumprod, cumsum, inner, fill, imag, prod, put, putmask, real, sum 
```

- 基本统计

```
cov, mean, std, var 
```

- 基本线性代数

```
cross, dot, outer, svd, vdot
```

## 进阶
### 广播法则
广播法则能使**通用函数**有意义地处理**不具有相同形状的输入**。

- 广播第一法则是，如果所有的输入数组维度不都相同，一个“1”将被重复地添加在维度较小的数组上直至所有的数组拥有一样的维度。

- 广播第二法则确定长度为1的数组沿着特殊的方向表现地好像它有沿着那个方向最大形状的大小。对数组来说，沿着那个维度的数组元素的值理应相同。

应用广播法则之后，所有数组的大小必须匹配。

### 花哨的索引和索引技巧

#### 数组索引

##### 一维数组索引
索引结果和索引数组的维数是一样的
```
>>> a = arange(12)**2  
>>> j = array( [ [ 3, 4], [ 9, 7 ] ] )         # a bidimensional array of indices
>>> a[j]  # the same shape as j
array([[ 9, 16],
       [81, 49]])
```

##### 多维数组索引

当被索引数组a是多维的时，**每一个唯一的索引数列指向a的第一维**  。以下示例通过将图片标签用调色版转换成色彩图像展示了这种行为。
`a[j]`

我们也可以给出不不止一维的索引，每一维的索引数组必须有相同的形状。
`a[i,j]`
`a[i,2]`
`a[:,j]`
**注意一维数组的长度必须和你想要切片的维度或轴的长度一致**
可以写成`l=[i,j];a[l]`但是不能写成`l=array([i,j]);a[l]`


你也可以使用数组索引作为目标来赋值：
然而，当一个索引列表包含重复时，赋值被多次完成，保留最后的值

```
>>> a = arange(5)
>>> a[[0,0,2]]=[1,2,3]
>>> a
array([2, 1, 3, 3, 4])
```

##### 通过布尔数组索引


### ix\_函数
ix_ 函数可以为了获得 多元组 的结果而用来结合不同向量。例如，如果你想要用**所有向量a、b和c元素**组成的三元组来计算 a+b*c ：
```
>>> a = array([2,3,4,5])
>>> b = array([8,5,4])
>>> c = array([5,4,6,8,3])
>>> ax,bx,cx = ix_(a,b,c)
>>> ax.shape, bx.shape, cx.shape
((4, 1, 1), (1, 3, 1), (1, 1, 5))
>>> result = ax+bx*cx
>>> result
array([[[42, 34, 50, 66, 26],
        [27, 22, 32, 42, 17],
        [22, 18, 26, 34, 14]],
       [[43, 35, 51, 67, 27],
        [28, 23, 33, 43, 18],
        [23, 19, 27, 35, 15]],
       [[44, 36, 52, 68, 28],
        [29, 24, 34, 44, 19],
        [24, 20, 28, 36, 16]],
       [[45, 37, 53, 69, 29],
        [30, 25, 35, 45, 20],
        [25, 21, 29, 37, 17]]])
>>> result[3,2,4]
17
>>> a[3]+b[2]*c[4]
17
```

### 线性代数

### 矩阵类

对二维数组使用一个冒号产生一个一维数组，然而矩阵产生了一个二维矩阵。  例如，一个 M[2,:] 切片产生了一个形状为(1,4)的矩阵，相比之下，一个数组的切片总是产生一个最低可能维度 11 的数组。例如，如果C是一个三维数组， C[...,1] 产生一个二维的数组而 C[1,:,1] 产生一个一维数组。

更改数组的维度，你可以省略一个尺寸，它将被自动推导出来。


## 高级
### 自定义结构数组

通过 NumPy 也可以定义像 C 语言那样的结构类型。在 NumPy 中定义结构的方法如下：

定义结构类型名称；定义字段名称，标明字段数据类型。
```
student= dtype({'names':['name', 'age', 'weight'], 'formats':['S32', 'i','f']}, align = True)
```
这里 student 是自定义结构类型的名称，使用 dtype 函数创建，在第一个参数中， 'names' 和 'formats' 不能改变， names 中列出的是结构中字段名称， formats 中列出的是对应字段的数据类型。 S32 表示 32 字节长度的字符串， i 表示 32 位的整数， f 表示 32 位长度的浮点数。最后一个参数为 True 时，表示要求进行内存对齐。
在定义好结构类型之后，就可以定义以该类型为元素的数组了 ：

```
a= array([(“Zhang”, 32, 75.5), (“Wang”, 24, 65.2)], dtype =student)
```



