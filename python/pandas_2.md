
# pandas--快速进阶
在上一篇中我们介绍了如何创建并访问pandas的Series和DataFrame类型的数据，本篇将介绍如何对pandas数据进行操作，掌握这些操作之后，基本可以处理大多数的数据了。

创建dataframe的条件很宽松
```
df2 = pd.DataFrame({ 'A' : 1., 'B': pd.Timestamp('20150214'), 'C': pd.Series(1.6,index=list(range(4)),dtype='float64'), 'D' : np.array([4] * 4, dtype='int64'), 'E' : 'hello pandas!' })
print df2
```

## 数据查看
- `dataframe.head()`和`dataframe.tail()`可以查看数据的头五行和尾五行，若需要改变行数，可在括号内指定：
- `dataframe.describe()`提供了DataFrame中纯数值数据的统计信息：

### 排序
对数据的排序将便利我们观察数据，DataFrame提供了两种形式的排序。
- 一种是按行列排序，即按照索引（行名）或者列名进行排序，可调用dataframe.sort_index，指定axis=0表示按索引（行名）排序，axis=1表示按列名排序，并可指定升序或者降序：
- 第二种排序是按值排序，可指定列名和排序方式，默认的是升序排序：`df = df.sort(columns=['tradeDate', 'secID'], ascending=[False, True])`

## 数据访问与操作
上篇中已经介绍了使用loc、iloc、at、iat、ix以及[]访问DataFrame数据的几种方式，这里再介绍一种方法，使用":"来获取部行或者全部列：`print df.iloc[1:4][:]`

我们可以扩展上篇介绍的使用布尔类型的向量获取数据的方法，可以很方便地过滤数据，例如，我们要选出收盘价在均值以上的数据：`print df[df.closePrice > df.closePrice.mean()].head()`

isin()函数可方便地过滤DataFrame中的数据：

```
print df[df['secID'].isin(['601628.XSHG', '000001.XSHE', '600030.XSHG'])].head()
print df.shape
```

## 处理缺失数据

```
df['turnoverVol'][df['secID'] == '601111.XSHG'] = np.nan
```

原始数据的中很可能存在一些数据的缺失，就如同现在处理的这个样例数据一样，处理缺失数据有多种方式。通常使用`dataframe.dropna()，dataframe.dropna()`可以按行丢弃带有nan的数据；若指定how='all'（默认是'any'），则只在整行全部是nan时丢弃数据；若指定thresh，则表示当某行数据非缺失列数超过指定数值时才保留；要指定根据某列丢弃可以通过subset完成。


有数据缺失时也未必是全部丢弃，`dataframe.fillna(value=value)`可以指定填补缺失值的数值

## 数据操作

`Series`和`DataFrame`的类函数提供了一些函数，如`mean()`、`sum()`等，指定0按列进行，指定1按行进行：`value_counts`函数可以方便地统计频数：



在panda中，Series可以调用map函数来对每个元素应用一个函数，DataFrame可以调用apply函数对每一列（行）应用一个函数，applymap对每个元素应用一个函数。这里面的函数可以是用户自定义的一个lambda函数，也可以是已有的其他函数。下例展示了将收盘价调整到[0, 1]区间：

```
print df[['closePrice']].apply(lambda x: (x - x.min()) / (x.max() - x.min())).head()
```

**使用append可以在Series后添加元素，以及在DataFrame尾部添加一行：**


DataFrame可以像在SQL中一样进行**合并**，在上篇中，我们介绍了使用concat函数创建DataFrame，这就是一种合并的方式。另外一种方式使用merge函数，需要指定依照哪些列进行合并，下例展示了如何根据security ID和交易日合并数据：

```
dat1 = df[['secID', 'tradeDate', 'closePrice']]
dat2 = df[['secID', 'tradeDate', 'turnoverVol']]
dat = dat1.merge(dat2, on=['secID', 'tradeDate'])
```

DataFrame另一个强大的函数是**groupby**，可以十分方便地对数据分组处理，我们对2015年一月内十支股票的开盘价，最高价，最低价，收盘价和成交量求平均值：


如果希望取每只股票的最新数据，应该怎么操作呢？**drop_duplicates**可以实现这个功能，首先对数据按日期排序，再按security ID去重：
