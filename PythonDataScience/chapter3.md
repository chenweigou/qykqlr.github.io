# Python 数据科学手册

[返回](./index.md)

[toc]

## 第三章 Pandas数据处理

### 3.1 安装并使用Pandas

我们将导入 Pandas 并使用别名 pd：

```python
In[2]: import pandas as pd
```

### 3.2 Pandas对象简介

Pandas 的三个基本数据结构： Series、 DataFrame 和 Index。

#### 3.2.1 Pandas的Series对象

Pandas 的 Series 对象是一个带索引数据构成的一维数组。 

```python
In[2]:  data = pd.Series([0.25, 0.5, 0.75, 1.0])
        data
Out[2]: 0 0.25
        1 0.50
        2 0.75
        3 1.00
dtype: float64
```

我们可以通过 values 属性和 index 属性获取数据， 也可以通过 Python 的中括号索引标签获取。

```python
In[3]: data.values
Out[3]: array([ 0.25, 0.5 , 0.75, 1. ])

In[4]: data.index
Out[4]: RangeIndex(start=0, stop=4, step=1)

In[5]: data[1]
Out[5]: 0.5
In[6]: data[1:3]
Out[6]: 1 0.50
        2 0.75
dtype: float64
```

01. Serise是通用的NumPy数组
    Series 对象和一维 NumPy 数组两者间的本质差异其实是索引： NumPy 数组通过隐式定义的整数索引获取数值， 而 Pandas 的 Series 对象用一种显式定义的索引与数值关联。

    ```python
    In[7]:  data = pd.Series([0.25, 0.5, 0.75, 1.0],
            index=['a', 'b', 'c', 'd'])data
    Out[7]: a 0.25
            b 0.50
            c 0.75
            d 1.00
            dtype: float64
    ```

02. Series是特殊的字典
    可以把 Pandas 的 Series 对象看成一种特殊的 Python 字典，可以直接用 Python 的字典创建一个 Series 对象。

    ```python
    In[11]:  population_dict = {'California': 38332521,
                                'Texas': 26448193,
                                'New York': 19651127,
                                'Florida': 19552860,
                                'Illinois': 12882135}
            population = pd.Series(population_dict)
            population
    Out[11]: California 38332521
             Florida 19552860
             Illinois 12882135
             New York 19651127
             Texas 26448193
             dtype: int64
    ```

03. 创建Series对象

    ```python
        pd.Series(data, index=index)
    ```

    其中， index 是一个可选参数， data 参数支持多种数据类型。

    * data 可以是列表或 NumPy 数组， 这时 index 默认值为整数序列
    * data 也可以是一个标量， 创建 Series 对象时会重复填充到每个索引上
    * data 还可以是一个字典， index 默认是排序的字典键

    每一种形式都可以通过显式指定索引筛选需要的结果：

    ```python
    In[17]: pd.Series({2:'a', 1:'b', 3:'c'}, index=[3, 2])
    Out[17]: 3 c
             2 a
             dtype: object
    ```

#### 3.2.2 Pandas的DataFrame对象

01. DataFrame是通用的NumPy数组
    如果将 Series 类比为带灵活索引的一维数组， 那么 DataFrame就可以看作是一种既有灵活的行索引， 又有灵活列名的二维数组。也可以把 DataFrame 看成是有序排列的若干 Series 对象。

    ```python
    In[19]: states = pd.DataFrame({'population': population,'area': area})
            states
    Out[19]:            area        population
             California 423967      38332521
             Florida    170312      19552860
             Illinois   149995      12882135
             New York   141297      19651127
             Texas      695662      26448193
    ```

02. DataFrame是特殊的字典
    也可以把 DataFrame 看成一种特殊的字典。 字典是一个键映射一个值， 而 DataFrame 是一列映射一个Series 的数据。

    ```python
    In[22]: states['area']
    Out[22]: California 423967
             Florida 170312
             Illinois 149995
             New York 141297
             Texas 695662
             Name: area, dtyp
    ```

03. 创建DataFrame对象：

    (1). 通过单个 Series 对象创建。

    ```python
    In[23]: pd.DataFrame(population, columns=['population'])
    Out[23]: population
             California 38332521
             Florida 19552860
             Illinois 12882135
             New York 19651127
             Texas 26448193
    ```

    (2). 通过字典列表创建。

    ```python
    In[24]: data = [{'a': i, 'b': 2 * i}
                    for i in range(3)]
            pd.DataFrame(data)
    Out[24]:  a b
            0 0 0
            1 1 2
            2 2 4
    ```

    如果字典中有些键不存在， Pandas 会用缺失值 NaN（不是数字，not a number） 来表示

    (3). 通过 Series 对象字典创建。

    ```python
    In[26]: pd.DataFrame({'population': population,'area': area})
    Out[26]: area population
             California 423967 38332521
             Florida 170312 19552860
             Illinois 149995 12882135
             New York 141297 19651127
             Texas 695662 26448193
    ```

    (4). 通过 NumPy 二维数组创建。

    ```python
    In[27]: pd.DataFrame(np.random.rand(3, 2),
            columns=['foo', 'bar'],
            index=['a', 'b', 'c'])
    Out[27]: foo bar
             a 0.865257 0.213169
             b 0.442759 0.108267
             c 0.047110 0.905718
    ```

    (5). 通过 NumPy 结构化数组创建。

    ```python
    In[28]: A = np.zeros(3, dtype=[('A', 'i8'), ('B', 'f8')])
            A
    Out[28]: array([(0, 0.0), (0, 0.0), (0, 0.0)],
                    dtype=[('A', '<i8'), ('B', '<f8')])

    In[29]: pd.DataFrame(A)
    Out[29]:  A B
            0 0 0.0
            1 0 0.0
            2 0 0.0
    ```

#### 3.2.3 Pandas的Index对象

01. 将Index看作不可变数组
    Index 对象的许多操作都像数组。 

    ```python
    In[31]: ind[1]
    Out[31]: 3

    In[32]: ind[::2]
    Out[32]: Int64Index([2, 5, 11], dtype='int64')
    ```

    Index 对象还有许多与 NumPy 数组相似的属性：`ind.size, ind.shape, ind.ndim, ind.dtype`， 不同在于， Index 对象的索引是不可变的，`ind[1] = 0` 这样的操作是不可行的。

02. 将Index看作有序集合

    Pandas 对象被设计用于实现许多操作， 如连接（join） 数据集， 其中会涉及许多集合操作。 Index 对象遵循 Python 标准库的集合（set） 数据结构的许多习惯用法， 包括并集、 交集、 差集等：

    ```python
    In[35]: indA = pd.Index([1, 3, 5, 7, 9])
            indB = pd.Index([2, 3, 5, 7, 11])

    In[36]: indA & indB # 交集
    Out[36]: Int64Index([3, 5, 7], dtype='int64')

    In[37]: indA | indB # 并集
    Out[37]: Int64Index([1, 2, 3, 5, 7, 9, 11], dtype='int64')

    In[38]: indA ^ indB # 异或
    Out[38]: Int64Index([1, 2, 9, 11], dtype='int64')
    ```

这些操作还可以通过调用对象方法来实现， 例如indA.intersection(indB)。

### 3.3 数据取值与选择

#### 3.3.1 Series数据选择方法

01. 将Series看作字典
02. 将Series看作一维数组
03. 索引器： loc、 iloc和i
    如果你的Series 是显式整数索引， 那么 data[1] 这样的取值操作会使用显式索引， 而 data[1:3] 这样的切片操作却会使用隐式索引。

    ```python
    In[11]: data = pd.Series(['a', 'b', 'c'], index=[1, 3, 5])
            data
    Out[11]: 1 a
             3 b
             5 c
             dtype: object
    In[12]: # 取值操作是显式索引
            data[1]
    Out[12]: 'a'
    In[13]: # 切片操作是隐式索引
            data[1:3]
    Out[13]: 3 b
             5 c
             dtype: object
    ```

    由于整数索引很容易造成混淆， 所以 Pandas 提供了一些索引器（indexer） 属性来作为取值的方法。

    第一种索引器是 loc 属性， 表示取值和切片都是显式的：

    ```python
    In[14]: data.loc[1]
    Out[14]: 'a'
    In[15]: data.loc[1:3]
    Out[15]: 1 a
             3 b
             dtype: object
    ```

    第二种是 iloc 属性， 表示取值和切片都是 Python 形式的 隐式索引：

    ```python
    In[16]: data.iloc[1]
    Out[16]: 'b'
    In[17]: data.iloc[1:3]
    Out[17]: 3 b
             5 c
             dtype: object
    ```

    第三种取值属性是 ix， 它是前两种索引器的混合形式， 在 Series对象中 ix 等价于标准的 []（Python 列表） 取值方式。

#### 3.3.2 DataFrame数据选择方法

01. 将DataFrame看作字典
    可以通过对列名进行字典形式（dictionary-style） 的取值获取数据， 也可以用属性形式（attribute-style） 选择纯字符串列名的数据、
    应该避免对用属性形式选择的列直接赋值
02. 将DataFrame看作二维数组
03. 其他取值方法
    如果对单个标签取值就选择列， 而对多个标签用切片就选择行，切片也可以不用索引值， 而直接用行数来实现。与之类似， 掩码操作也可以直接对每一行进行过滤， 而不需要使用loc 索引器。

### 3.4 Pandas数值运算方法

#### 3.4.1 通用函数： 保留索引

因为 Pandas 是建立在 NumPy 基础之上的， 所以 NumPy 的通用函数同样适用于 Pandas 的 Series 和 DataFrame 对象， 并且可以保留Pandas的索引和标签。

#### 3.4.2 通用函数： 索引对齐

01. Series索引对齐

    结果数组的索引是两个输入数组索引的并集。对于缺失位置的数据， Pandas 会用 NaN 填充， 表示“此处无数”。

02. DataFrame索引对齐

    在计算两个 DataFrame 时， 类似的索引对齐规则也同样会出现在共同（并集） 列中。

两个对象的行列索引可以是不同顺序的， 结果的索引会自动按顺序排列。
在 Series 中， 我们可以通过运算符方法的fill_value 参数自定义缺失值。

表3-1： Python运算符与Pandas方法的映射关系

| Python运算符 | Pandas方法 |
| ----- | ----- | 
| + | add() |
| - | sub()、 subtract() |
| * | mul()、 multiply() |
| / | truediv()、 div()、 divide() |
| // | floordiv() |
| % | mod() |
| ** |  pow() |

#### 3.4.3 通用函数： DataFrame与Series的运算

DataFrame 和 Series 的运算规则， 与NumPy 中二维数组与一维数组的运算规则是一样的。会根据 NumPy 的广播规则。

### 3.5 处理缺失值

本节以及全书涉及的缺失值主要有三种形式： null、 NaN 或 NA。

#### 3.5.1 选择处理缺失值的方法

一种方法是通过一个覆盖全局的掩码表示缺失值， 另一种方法是用一个标签值（sentinel value） 表示缺失值。

#### 3.5.2 Pandas的缺失值

Pandas 最终选择用标签方法表示缺失值，包括两种 Python 原有的缺失值： 浮点数据类型的 NaN 值， 以及 Python的 None 对象。

01. None： Python对象类型的缺失值
    Pandas 可以使用的第一种缺失值标签是 None， 它是一个 Python 单体对象，只能用于 'object' 数组类型。

02. NaN： 数值类型的缺失值
    另一种缺失值的标签是 NaN。无论和 NaN 进行何种操作， 最终结果都是NaN。

03. Pandas中NaN与None的差异

虽然 NaN 与 None 各有各的用处， 但是 Pandas 把它们看成是可以等价交换的， 在适当的时候会将两者进行替换。

表3-2： Pandas对不同类型缺失值的转换规则

| 类型 | 缺失值转换规则 | NA标签值
| ----- | ----- | ----- |
| floating 浮点型 | 无变化 | np.nan |
| object 对象类型 | 无变化 | None 或 np.nan |
| integer 整数类型 | 强制转换为 float64 | np.nan |
| boolean 布尔类型 | 强制转换为 object | None 或 np.nan |

#### 3.5.3 处理缺失值

Pandas 提供了一些方法来发现、 剔除、 替换数据结构中的缺失值:

1. `isnull()`: 创建一个布尔类型的掩码标签缺失值。
2. `notnull()`: 与 isnull() 操作相反。
3. `dropna()`: 返回一个剔除缺失值的数据。
    我们没法从 DataFrame 中单独剔除一个值， 要么是剔除缺失值所在的整行， 要么是整列。 
4. `fillna()`: 返回一个填充了缺失值的数据副本。

### 3.6 层级索引

#### 3.6.1 多级索引Series

01. 笨办法：用一个 Python 元组来表示索引

02. 好办法：Pandas多级索引

    ```python
    In[5]:  index = [('California', 2000), ('California', 2010),
                    ('New York', 2000), ('New York', 2010),
                    ('Texas', 2000), ('Texas', 2010)]
            index = pd.MultiIndex.from_tuples(index)
            index
    Out[5]: MultiIndex(levels=[['California', 'New York', 'Texas'], [2000, 2010]],
                    labels=[[0, 0, 1, 1, 2, 2], [0, 1, 0, 1, 0, 1]])

    In[6]:  pop = pop.reindex(index)
            pop
    Out[6]: California  2000 33871648
                        2010 37253956
            New York    2000 18976457
                        2010 19378102
            Texas       2000 20851820
                        2010 25145561
            dtype: int64

    In[7]: pop[:, 2010]
    Out[7]: California  37253956
            New York    19378102
            Texas       25145561
            dtype: int64
    ```

03. 高维数据的多级索引

    我们可以用一个带行列索引的简单DataFrame 代替前面的多级索引。unstack() 方法可以快速将一个多级索引的 Series 转化为
    普通索引的 DataFrame， stack() 方法则实现相反的效果：

    ```python
    In[8]:  pop_df = pop.unstack()
            pop_df
    Out[8]:             2000        2010
            California  33871648    37253956
            New York    18976457    19378102
            Texas       20851820    25145561

    In[9]:  pop_df.stack()
    Out[9]: California  2000 33871648
                        2010 37253956
            New York    2000 18976457
                        2010 19378102
            Texas       2000 20851820
                        2010 25145561
            dtype: int64
    ```

#### 3.6.2 多级索引的创建方法

为 Series 或 DataFrame 创建多级索引最直接的办法就是将 index 参数设置为至少二维的索引数组, 或者将元组作为键的字典传递给 Pandas

```python
In[12]: df = pd.DataFrame(np.random.rand(4, 2),
        index=[['a', 'a', 'b', 'b'], [1, 2, 1, 2]],columns=['data1', 'data2'])
        df
Out[12]:        data1       data2
        a   1   0.554233    0.356072
            2   0.925244    0.219474
        b   1   0.441759    0.610054
            2   0.171495    0.886688

In[13]: data = {('California', 2000): 33871648,
                ('California', 2010): 37253956,
                ('Texas', 2000): 20851820,
                ('Texas', 2010): 25145561,
                ('New York', 2000): 18976457,
                ('New York', 2010): 19378102}
        pd.Series(data)
Out[13]: California 2000    33871648
                    2010    37253956
         New York   2000    18976457
                    2010    19378102
         Texas      2000    20851820
                    2010    25145561
         dtype: int64
```

1. 显式地创建多级索引

    ```python
    In[14]: pd.MultiIndex.from_arrays([['a', 'a', 'b', 'b'], [1, 2, 1, 2]])
    Out[14]: MultiIndex(levels=[['a', 'b'], [1, 2]],
                        labels=[[0, 0, 1, 1], [0, 1, 0, 1]])

    In[15]: pd.MultiIndex.from_tuples([('a', 1), ('a', 2), ('b', 1), ('b', 2)])
    Out[15]: MultiIndex(levels=[['a', 'b'], [1, 2]],
                        labels=[[0, 0, 1, 1], [0, 1, 0, 1]])

    In[16]: pd.MultiIndex.from_product([['a', 'b'], [1, 2]])
    Out[16]: MultiIndex(levels=[['a', 'b'], [1, 2]],
                        labels=[[0, 0, 1, 1], [0, 1, 0, 1]])

    In[17]: pd.MultiIndex(levels=[['a', 'b'], [1, 2]],
                            labels=[[0, 0, 1, 1], [0, 1, 0, 1]])
    Out[17]: MultiIndex(levels=[['a', 'b'], [1, 2]],
                        labels=[[0, 0, 1, 1], [0, 1, 0, 1]])
    ```

2. 多级索引的等级名称
    给 MultiIndex 的等级加上名称会为一些操作提供便利。 你可以在前面任何一个 MultiIndex 构造器中通过 names 参数设置等级名称， 也可以在创建之后通过索引的 names 属性来修改名称。

03. 多级列索引

    每个 DataFrame 的行与列都是对称的， 也就是说既然有多级行索引， 那么同样可以有多级列索引。 

    ```python
    In[19]:
    # 多级行列索引
    index = pd.MultiIndex.from_product([[2013, 2014], [1, 2]],
                                        names=['year', 'visit'])
    columns = pd.MultiIndex.from_product([['Bob', 'Guido', 'Sue'], ['HR', 'Temp']],
                                        names=['subject', 'type'])

    # 模拟数据
    data = np.round(np.random.randn(4, 6), 1)
    data[:, ::2] *= 10
    data += 37

    # 创建DataFrame
    health_data = pd.DataFrame(data, index=index, columns=columns)
    health_data
    Out[19]: subject    Bob             Guido           Sue
             type       HR      Temp    HR      Temp    HR      Temp
             year   visit
             2013   1   31.0    38.7    32.0    36.7    35.0    37.2
                    2   44.0    37.7    50.0    35.0    29.0    36.7
             2014   1   30.0    37.4    39.0    37.8    61.0    36.9
                    2   47.0    37.8    48.0 3  7.3     51.0    36.5
    ```

#### 3.6.3 多级索引的取值与切片

对 MultiIndex 的取值和切片操作很直观， 你可以直接把索引看成额外增加的维度。 

#### 3.6.4 多级索引行列转换

01. 有序的索引和无序的索引

    如果 MultiIndex 不是有序的索引， 那么大多数切片操作都会失败。 
    Pandas 提供了许多便捷的操作完成排序， 如 sort_index()和 sortlevel() 方法。
    索引排序之后， 局部切片就可以正常使用了。

02. 索引stack与unstack

    前文曾提过， 我们可以将一个多级索引数据集转换成简单的二维形式， 可以通过 level 参数设置转换的索引层级：

    ```python
    In[38]: pop.unstack(level=0)
    Out[38]:    state   California  New York    Texas
                year
                2000    33871648    18976457    20851820
                2010    37253956    19378102    25145561
    In[39]: pop.unstack(level=1)
    Out[39]:    year        2000        2010
                state
                California  33871648    37253956
                New York    18976457    19378102
                Texas       20851820    25145561
    ```

    unstack() 是 stack() 的逆操作， 同时使用这两种方法让数据保持不变。

    ```python
    In[40]: pop.unstack().stack()
    Out[40]:    state       year
                California  2000    33871648
                            2010    37253956
                New York    2000    18976457
                            2010    19378102
                Texas       2000    20851820
                            2010    25145561
    dtype: int64
    ```

03. 索引的设置与重置

层级数据维度转换的另一种方法是行列标签转换， 可以通过reset_index 方法实现。
如果在上面的人口数据 Series 中使用该方法， 则会生成一个列标签中包含之前行索引标签 state 和 year 的DataFrame:

```python
In[41]: pop_flat = pop.reset_index(name='population')
        pop_flat
Out[41]:    state       year    population
        0   California  2000    33871648
        1   California  2010    37253956
        2   New York    2000    18976457
        3   New York    2010    19378102
        4   Texas       2000    20851820
        5   Texas       2010    25145561
```

也可以通过DataFrame 的 set_index将类似上面的原始输入数据的列直接转换成 MultiIndex：

```python
In[42]: pop_flat.set_index(['state', 'year'])
Out[42]:                        population
            state       year
            California  2000    33871648
                        2010    37253956
            New York    2000    18976457
                        2010    19378102
            Texas       2000    20851820
                        2010    2514556
```

### 3.6.5 多级索引的数据累计方法

前面我们已经介绍过一些 Pandas 自带的数据累计方法， 比如mean()、 sum() 和 max()。 对于层级索引数据， 可以设置参数level 实现对数据子集的累计操作。

### 3.7 合并数据集： Concat与Append操作

#### 3.7.1 知识回顾： NumPy数组的合并

合并 NumPy 数组通过np.concatenate 函数即可完成。 

#### 3.7.2 通过pd.concat实现简易合并

Pandas 有一个 pd.concat() 函数与 np.concatenate 语法类似， 但是配置参数更多， 功能也更强大：

```python
# Pandas 0.18版中的函数签名
pd.concat(objs, axis=0, join='outer', join_axes=None, gnore_index=False,keys=None, levels=None, names=None, verify_integrity=False,copy=True)
```

01. 索引重复
    np.concatenate 与 pd.concat 最主要的差异之一就是 Pandas 在合并时会保留索引， 即使索引是重复的！ 
    如果不想要重复的索引，可以通过设置参数来处理
    (1) 捕捉索引重复的错误。 `verify_integrity=True`
    (2) 忽略索引。 `ignore_index=True`
    (3) 增加多级索引。 通过 keys 参数为数据源设置多级索引标签

02. 类似join的合并
    可以用 join 和 join_axes 参数设置合并方式。 默认的合并方式是对所有输入列进行并集合并（join='outer'） ， 当然也可以用 join='inner' 实现对输入列的交集合并
    另一种合并方式是直接确定结果使用的列名， 设置 join_axes 参数， 里面是索引对象构成的列表（是列表的列表） 。 

03. append()方法
    因为直接进行数组合并的需求非常普遍， 所以 Series 和DataFrame 对象都支持 append 方法。
    如果你需要进行多个 append 操作， 还是建议先创建一个 DataFrame列表， 然后用 concat() 函数一次性解决所有合并任务。

### 3.8 合并数据集： 合并与连接

Pandas 的基本特性之一就是高性能的内存式数据连接（join） 与合并（merge） 操作，主接口是 pd.merge 函数。

#### 3.8.1 关系代数

pd.merge() 实现的功能基于关系代数（relational algebra） 的一部分。

#### 3.8.2 数据连接的类型

pd.merge() 函数实现了三种数据连接的类型： 一对一、 多对一和多对多。

01. 一对一连接

    ```python
    In[3]:  df3 = pd.merge(df1, df2)
            df3
    Out[3]:     employee    group           hire_date
            0   Bob         Accounting      2008
            1   Jake        Engineering     2012
            2   Lisa        Engineering     2004
            3   Sue         HR              2014
    ```

    pd.merge() 会默认丢弃原来的行索引， 不过也可以自定义。

02. 多对一连接

    多对一连接是指， 在需要连接的两个列中， 有一列的值有重复。通过多对一连接获得的结果 DataFrame 将会保留重复值。

03. 多对多连接

    多对多连接是个有点儿复杂的概念， 不过也可以理解。 如果左右两个输入的共同列都包含重复值， 那么合并的结果就是一种多对多连接。

#### 3.8.3 设置数据合并的键

`pd.merge()`会将两个输入的一个或多个共同列作为键进行合并， 但由于两个输入要合并的列通常都不是同名的， 因此 pd.merge() 提供了一些参数处理这个问题。

01. 参数on的用法

    最简单的方法就是直接将参数 on 设置为一个列名字符串或者一个包含多列名称的列表, 这个参数只能在两个 DataFrame 有共同列名的时候才可以使用。

02. left_on与right_on参数

    有时你也需要合并两个列名不同的数据集， 在这种情况下， 就可以
    用 left_on 和 right_on 参数来指定列名。

03. left_index与right_index参数

    除了合并列之外， 你可能还需要合并索引。你可以通过设置 pd.merge() 中的 left_index 和 / 或right_index 参数将索引设置为键来实现合并。

    ```python
    In[10]:
    print(df1a); print(df2a)
    print(pd.merge(df1a, df2a, left_index=True, right_index=True))

    df1a                                    df2a
                    group                                  hire_date
    employee                                employee
    Bob             Accounting              Lisa            2004
    Jake            Engineering             Bob             2008
    Lisa            Engineering             Jake            2012
    Sue             HR                      Sue              2014
    pd.merge(df1a, df2a, left_index=True, right_index=True)
                    group           hire_date
    employee
    Lisa            Engineering     2004
    Bob             Accounting      2008
    Jake            Engineering     2012
    Sue             HR              2014
    ```

#### 3.8.4 设置数据连接的集合操作规则

我们合并两个数据集， 默认情况下， 结果中只会包含两个输入集合的交集， 这种连接方式被称为内连接（inner join） 。 我们可以用 how 参数设置连接方式， 默认值为'inner'。

how 参数支持的数据连接方式还有 'outer'、 'left' 和 'right'。 外连接（outer join） 返回两个输入列的交集， 所有缺失值都用 NaN 填充，左连接（left join） 和右连接（right join） 返回的结果分别只包含左列和右列。

#### 3.8.5 重复列名： suffixes参数

当输出结果中有两个重复的列名， pd.merge() 函数会自动为它们增加后缀 _x 或 _y， 也可以通过 suffixes 参数自定义后缀名。

### 3.9 累计与分组

#### 3.9.1 行星数据

#### 3.9.2 Pandas的简单累计功能

Pandas 的 Series 和 DataFrame 支持所有 2.4 节中介绍的常用累计函
数。 另外， 还有一个非常方便的 describe() 方法可以计算每一列的若
干常用统计值。

表3-3： Pandas的累计方法
| 指标 | 描述 |
| ----- | ----- |
| count() | 计数项
| first()、 last() | 第一项与最后一项 |
| mean()、 median() | 均值与中位数 |
| min()、 max() | 最小值与最大值 |
| std()、 var() | 标准差与方差 |
| mad() | 均值绝对偏差（mean absolute deviation） |
| prod() | 所有项乘积 |
| sum() | 所有项求和 |

#### 3.9.3 GroupBy： 分割、 应用和组合

01. 分割、 应用和组合
    我们可以用 DataFrame 的 groupby() 方法进行绝大多数常见的分割 - 应用 - 组合操作， 将需要分组的列名传进去即可。
    这里的返回值是一个 DataFrameGroupBy 对象，里面隐藏着若干组数据， 但是在没有应用累计函数之前不会计算，你可以用 Pandas 或 NumPy 的任意一种累计函数。

    ```python
    In[13]: df.groupby('key').sum()
    Out[13]:            data
                key
                A       3
                B       5
                C       7
    ```

02. GroupBy对象

    (1) 按列取值。 GroupBy 对象与 DataFrame 一样， 也支持按列取值， 并返回一个修改过的 GroupBy 对象。与GroupBy 对象一样， 直到我们运行累计函数， 才会开始计。

    (2) 按组迭代。 GroupBy 对象支持直接按组进行迭代， 返回的每一组都是 Series 或 DataFrame

    ```python
    In[17]: for (method, group) in planets.groupby('method'):
                print("{0:30s} shape={1}".format(method, group.shape))
    Astrometry shape=(2, 6)
    Eclipse Timing Variations shape=(9, 6)
    Imaging shape=(38, 6)
    Microlensing shape=(23, 6)
    Orbital Brightness Modulation shape=(3, 6)
    Pulsar Timing shape=(5, 6)
    Pulsation Timing Variations shape=(1, 6)
    Radial Velocity shape=(553, 6)
    Transit shape=(397, 6)
    Transit Timing Variations shape=(4, 6)
    ```

    (3) 调用方法。 借助 Python 类的魔力（@classmethod） ， 可以让任何不由 GroupBy 对象直接实现的方法直接应用到每一组， 无论是 DataFrame 还是 Series 对象都同样适用。 

    ```python
    In[18]: planets.groupby('method')['year'].describe().unstack()
    ```

03. 累计、 过滤、 转换和应用

    (1) 累计。 `df.groupby('key').aggregate(['min', np.median, max])`
    (2) 过滤。 `df.groupby('key').filter(filter_func)`
    (3) 转换。 `df.groupby('key').transform(lambda x: x - x.mean())`
    (4) apply() 方法。`df.groupby('key').apply(norm_by_data2)`

04. 设置分割的键

    (1) 将列表、 数组、 Series 或索引作为分组键。 
    (2) 用字典或 Series 将索引映射到分组名称。另一种方法是提供一个字典， 将索引映射到分组键。
    (3) 任意 Python 函数。 与前面的字典映射类似， 你可以将任意Python 函数传入 groupby， 函数映射到索引， 然后新的分组输出。

### 3.10 数据透视表

数据透视表将每一列数据作为输入， 输出将数据不断细分成多个维度累计信息的二维数据表。

#### 3.10.1 演示数据透视表

```python
In[1]:  import numpy as np
        import pandas as pd
        import seaborn as sns
        titanic = sns.load_dataset('titanic')
In[2]:  titanic.head()
Out[2]:
    survived    pclass      sex         age     sibsp   parch   fare    embarked    class   who     adult_male  deck    embark_town     alive   alone
0   0           3           male        22.0    1       0       7.2500      S       Third   man     True        NaN     Southampton     no      False
1   1           1           female      38.0    1       0       71.2833     C       First   woman   False       C       Cherbourg       yes     False
2   1           3           female      26.0    0       0       7.9250      S       Third   woman   False       NaN     Southampton     yes     True
3   1           1           female      35.0    1       0       53.1000     S       First   woman   False       C       Southampton     yes     False
4   0           3           male        35.0    0       0       8.0500      S       Third   man     True        NaN     Southampton     no      True
```

#### 3.10.2 手工制作数据透视表

统计不同性别乘客的生还率：

```python
In[3]: titanic.groupby('sex')[['survived']].mean()
Out[3]:             survived
        sex
        female      0.742038
        male        0.188908
```

观察不同性别与船舱等级的生还情况:

```python
In[4]: titanic.groupby(['sex', 'class'])['survived'].aggregate('mean').unstack()
Out[4]: class       First       Second      Third
        sex
        female      0.968085    0.921053    0.500000
        male        0.368852    0.157407    0.135447
```

####　3.10.3 数据透视表语法

用 DataFrame 的 pivot_table 实现的效果等同于上一节的管道命令的代码：

```python
In[5]: titanic.pivot_table('survived', index='sex', columns='class')
Out[5]: class       First       Second      Third
        sex
        female      0.968085    0.921053    0.500000
        male        0.368852    0.157407    0.135447
```

01. 多级数据透视表
     数据透视表中的分组也可以通过各种参数指定多个等级。

     ```python
     In[6]: age = pd.cut(titanic['age'], [0, 18, 80])
            titanic.pivot_table('survived', ['sex', age], 'class')
    Out[6]: class                   First       Second      Third
            sex         age
            female      (0, 18]     0.909091    1.000000    0.511628
                        (18, 80]    0.972973    0.900000    0.423729
            male        (0, 18]     0.800000    0.600000    0.215686
                        (18, 80]    0.375000    0.071429    0.133663
     ```

    对某一列也可以使用同样的策略——使用 `pd.qcut`

02. 其他数据透视表选项

    DataFrame 的 pivot_table 方法的完整签名如下所示：

    ```python
    # Pandas 0.18版的函数签名
    DataFrame.pivot_table(data, values=None, index=None, columns=None,
                            aggfunc='mean', fill_value=None, margins=False,
                            dropna=True, margins_name='All')
    ```

### 3.11 向量化字符串操作

#### 3.11.1 Pandas字符串操作简介

Pandas 为包含字符串的 Series 和 Index 对象提供的 str 属性既可以满足向量化字符串操作的需求， 又可以正确地处理缺失值。 

```python
In[5]: names.str.capitalize()
Out[5]: 0 Peter
        1 Paul
        2 None
        3 Mary
        4 Guido
        dtype: object

```

#### 3.11.2 Pandas字符串方法列表

01. 与Python字符串方法相似的方法

    ```python
    len()       lower()         translate()     islower()
    ljust()     upper()         startswith()    isupper()
    rjust()     find()          endswith()      isnumeric()
    center()    rfind()         isalnum()       isdecimal()
    zfill()     index()         isalpha()       split()
    strip()     rindex()        isdigit()       rsplit()
    rstrip()    capitalize()    isspace()       partition()
    lstrip()    swapcase()      istitle()       rpartition()
    ```

02. 使用正则表达式的方法

    表3-4： Pandas向量化字符串方法与Python标准库的re模块函数的对应关系

    | 方法 | 描述 |
    | ----- | ----- |
    | match() | 对每个元素调用 re.match()， 返回布尔类型值 |
    | extract() | 对每个元素调用 re.match()， 返回匹配的字符串组（groups） |
    | findall() | 对每个元素调用 re.findall() |
    | replace() | 用正则模式替换字符串 |
    | contains() | 对每个元素调用 re.search()， 返回布尔类型值 |
    | count() | 计算符合正则模式的字符串的数量 |
    | split() | 等价于 str.split()， 支持正则表达式 |
    | rsplit() | 等价于 str.rsplit()， 支持正则表达式 |

03. 其他字符串方法

    | 方法 | 描述 |
    | ----- | ----- |
    | get() | 获取元素索引位置上的值， 索引从 0 开始 |
    | slice() | 对元素进行切片取值 |
    | slice_replace() | 对元素进行切片替换 |
    | cat() | 连接字符串（此功能比较复杂， 建议阅读文档） |
    | repeat() | 重复元素 |
    | normalize() | 将字符串转换为 Unicode 规范形式 |
    | pad() | 在字符串的左边、 右边或两边增加空格 |
    | wrap() | 将字符串按照指定的宽度换行 |
    | join() | 用分隔符连接 Series 的每个元素 |
    | get_dummies() | 按照分隔符提取每个元素的 dummy 变量， 转换为独热（one-hot） 编码的 DataFrame |

### 3.12 处理时间序列

#### 3.12.1 Python的日期与时间工具

01. 原生Python的日期与时间工具： datetime与dateutil
    datetime 和 dateutil 模块在灵活性与易用性方面都表现出色，你可以用这些对象及其相应的方法轻松完成你感兴趣的任意操作。但如果你处理的时间数据量比较大， 那么速度就会比较慢。
02. 时间类型数组： NumPy的datetime64类型

    ```python
        In[4]:  import numpy as np
            date = np.array('2015-07-04', dtype=np.datetime64)
            date
        Out[4]: array(datetime.date(2015, 7, 4), dtype='datetime64[D]')
    ```

    表3-6： 日期与时间单位格式代码
    | 代码 | 含义 | 时间跨度 (相对) | 时间跨度 (绝对) |
    | ----- | ----- | ----- | ----- |
    | Y | 年（year） | ± 9.2e18 年 | [9.2e18 BC, 9.2e18 AD] |
    | M | 月（month） | ± 7.6e17 年 | [7.6e17 BC, 7.6e17 AD] |
    | W | 周（week） | ± 1.7e17 年 | [1.7e17 BC, 1.7e17 AD] |
    | D | 日（day） | ± 2.5e16 年 | [2.5e16 BC, 2.5e16 AD] |
    | h | 时（hour） | ± 1.0e15 年 | [1.0e15 BC, 1.0e15 AD] |
    | m | 分（minute） | ± 1.7e13 年 | [1.7e13 BC, 1.7e13 AD] |
    | s | 秒（second） | ± 2.9e12 年 | [ 2.9e9 BC, 2.9e9 AD] |
    | ms | 毫秒（millisecond） | ± 2.9e9 年 | [ 2.9e6 BC, 2.9e6 AD] |
    | us | 微秒（microsecond） | ± 2.9e6 年 | [290301 BC, 294241 AD] |
    | ns | 纳秒（nanosecond） | ± 292 年 | [ 1678 AD, 2262 AD] |
    | ps | 皮秒（picosecond） | ± 106 天 | [ 1969 AD, 1970 AD] |
    | fs | 飞秒（femtosecond） | ± 2.6 小时 | [ 1969 AD, 1970 AD] |
    | as | 原秒（attosecond） | ± 9.2 秒 | [ 1969 AD, 1970 AD] |

03. Pandas的日期与时间工具： 理想与现实的最佳解决方案

    Pandas 所有关于日期与时间的处理方法全部都是通过 Timestamp对象实现的， 它利用 numpy.datetime64 的有效存储和向量化接口将 datetime 和 dateutil 的易用性有机结合起来。 

    ```python
    In[9]:  import pandas as pd
            date = pd.to_datetime("4th of July, 2015")
            date
    Out[9]: Timestamp('2015-07-04 00:00:00')

    In[10]: date.strftime('%A')
    Out[10]: 'Saturday
    ```

#### 3.12.2 Pandas时间序列： 用时间作索引

Pandas 时间序列工具非常适合用来处理带时间戳的索引数据。

```python
In[12]: index = pd.DatetimeIndex(['2014-07-04', '2014-08-04',
                                    '2015-07-04', '2015-08-04'])
        data = pd.Series([0, 1, 2, 3], index=index)
        data
Out[12]: 2014-07-04 0
         2014-08-04 1
         2015-07-04 2
         2015-08-04 3
         dtype: int64
```

有了一个带时间索引的 Series 之后， 可以直接用日期进行切片取值, 甚至直接通过年份切片获取该年的数据：

```python
In[13]: data['2014-07-04':'2015-07-04']
Out[13]: 2014-07-04 0
         2014-08-04 1
         2015-07-04 2
         dtype: int64

In[14]: data['2015']
Out[14]: 2015-07-04 2
         2015-08-04 3
         dtype: int64
```

#### 3.12.3 Pandas时间序列数据结构

* 针对时间戳数据， Pandas 提供了 Timestamp 类型。
* 针对时间周期数据， Pandas 提供了 Period 类型。
* 针对时间增量或持续时间， Pandas 提供了 Timedelta 类型。 

最基础的日期 / 时间对象是 Timestamp 和 DatetimeIndex。 这两种对象可以直接使用， 最常用的方法是 pd.to_datetime() 函数， 它可以解析许多日期与时间格式。
任何 DatetimeIndex 类型都可以通过 to_period() 方法和一个频率代码转换成 PeriodIndex 类型。 
当用一个日期减去另一个日期时， 返回的结果是 TimedeltaIndex 类型。

为了能更简便地创建有规律的时间序列， Pandas 提供了一些方法： pd.date_range() 可以处理时间戳、 pd.period_range() 可以处理周期、 pd.timedelta_range() 可以处理时间间隔。 

#### 3.12.4 时间频率与偏移量

表3-7： Pandas频率代码

| 代码 | 描述 | 代码 | 描述 |
| ----- |  ----- | ----- |----- |
| D | 天（calendar day， 按日历算， 含双休日） | B | 天（business day， 仅含工作日） |
| W | 周（weekly）|  |
| M | 月末（month end） | BM | 月末（business month end， 仅含工作日） |
| Q | 季末（quarter end） | BQ | 季末（business quarter end） ， 仅含工作日 |
| A | 年末（year end） | BA | 年末（business year end， 仅含工作日） |
| H | 小时（hours） | BH | 小时（business hours， 工作时间） |
| T | 分钟（minutes）| |  |
| S | 秒（seconds）| |  |
| L | 毫秒（milliseonds）| |  |
| U | 微秒（microseconds）| |  |
| N | 纳秒（nanoseconds）| | |

月、 季、 年频率都是具体周期的结束时间（月末、 季末、 年末） ， 而有一些以 S（start， 开始） 为后缀的代码表示日期开始。

表3-8： 带开始索引的频率代码

代码 | 频率 |
| ----- | ----- |
| MS | 月初（month start） |
| BMS | 月初（business month start， 仅含工作日） |
| QS | 季初（quarter start） |
| BQS | 季初（business quarter start， 仅含工作日） |
| AS | 年初（year start） |
| BAS | 年初（business year start， 仅含工作日） |

另外，可以在频率代码后面加三位月份缩写字母来改变季、 年频率的开始时间。
Q-JAN、 BQ-FEB、 QS-MAR、 BQS-APR 等。
A-JAN、 BA-FEB、 AS-MAR、 BAS-APR 等。
同理， 也可以在后面加三位星期缩写字母来改变一周的开始时间。
W-SUN、 W-MON、 W-TUE、 W-WED 等

在这些代码的基础上， 还可以将频率组合起来创建的新的周期。

```python
In[23]: pd.timedelta_range(0, periods=9, freq="2H30T")
Out[23]:
TimedeltaIndex(['00:00:00', '02:30:00', '05:00:00', '07:30:00', '10:00:00',
                '12:30:00', '15:00:00', '17:30:00', '20:00:00'],
                dtype='timedelta64[ns]', freq='150T')
```

#### 3.12.5 重新取样、 迁移和窗口

01. 重新取样与频率转换
    处理时间序列数据时， 经常需要按照新的频率（更高频率、 更低频率） 对数据进行重新取样。 你可以通过 resample() 方法解决这个问题， 或者用更简单的 asfreq() 方法。 

02. 时间迁移
    另一种常用的时间序列操作是对数据按时间进行迁移。 Pandas 有两种解决这类问题的方法： shift() 和 tshift()。 

03. 移动时间窗口

    Pandas 处理时间序列数据的第 3 种操作是移动统计值（rollingstatistics）。 这些指标可以通过 Series 和 DataFrame 的rolling() 属性来实现， 它会返回与 groupby 操作类似的结果

### 3.13 高性能Pandas： eval()与query()

#### 3.13.1 query()与eval()的设计动机： 复合代数式

NumPy 与 Pandas 都支持快速的向量化运算, 但是这种运算在处理复合代数式问题, 如`mask = (x > 0.5) & (y < 0.5)`时的效率
比较低, 因为NumPy 会计算每一个代数子式，也就是说， 每段中间过程都需要显式地分配内存。

#### 3.13.2 用pandas.eval()实现高性能运算

Pandas 的 eval() 函数用字符串代数式实现了 DataFrame 的高性能运算。

如果要用普通的 Pandas 方法计算四个 DataFrame 的和， 可以这么写：

```python
In[7]: %timeit df1 + df2 + df3 + df4
10 loops, best of 3: 87.1 ms per loop
```

也可以通过 pd.eval 和字符串代数式计算并得出相同的结果：

```python
In[8]: %timeit pd.eval('df1 + df2 + df3 + df4')
10 loops, best of 3: 42.2 ms per loop
```

这个 eval() 版本的代数式比普通方法快一倍（而且内存消耗更少） 。

pd.eval()支持的运算：
(1) 算术运算符。 pd.eval() 支持所有的算术运算符。
(2) 比较运算符。 pd.eval() 支持所有的比较运算符， 包括链式代数式。
(3) 位运算符。 pd.eval() 支持 &（与） 和|（或） 等位运算符。
你还可以在布尔类型的代数式中使用 and 和 or 等字面值。
(4) 对象属性与索引。 pd.eval() 可以通过 obj.attr 语法获取对象属
性， 通过 obj[index] 语法获取对象索引。

```python
In[15]: result1 = df2.T[0] + df3.iloc[1]
        result2 = pd.eval('df2.T[0] + df3.iloc[1]')
        np.allclose(result1, result2)
Out[15]: True
```

(5) 其他运算。 目前 pd.eval() 还不支持函数调用、 条件语句、 循环以
及更复杂的运算。

#### 3.13.3 用DataFrame.eval()实现列间运算

由于 pd.eval() 是 Pandas 的顶层函数， 因此 DataFrame 有一个eval() 方法可以做类似的运算。

使用 eval() 方法的好处是可以借助列名称进行运算：

```python
In[18]: result2 = pd.eval("(df.A + df.B) / (df.C - 1)")
        result3 = df.eval('(A + B) / (C - 1)')
        np.allclose(result2, result3)
Out[18]: True
```

01. 用DataFrame.eval()新增列

    ```python
    In[20]: df.eval('D = (A + B) / C', inplace=True)
            df.head()
    Out[20]:    A           B           C           D
            0   0.375506    0.406939    0.069938    11.187620
            1   0.069087    0.235615    0.154374    1.973796
            2   0.677945    0.433839    0.652324    1.704344
            3   0.264038    0.808055    0.347197    3.087857
            4   0.589161    0.252418    0.557789    1.508776
    ```

02. DataFrame.eval()使用局部变量
    DataFrame.eval() 方法还支持通过 @ 符号使用 Python 的局部变量:

    ```python
    In[22]: column_mean = df.mean(1)
            result1 = df['A'] + column_mean
            result2 = df.eval('A + @column_mean')
            np.allclose(result1, result2)
    Out[22]: True
    ```

     符号表示“这是一个变量名称而不是一个列名称”。

#### 3.13.4 DataFrame.query()方法

DataFrame 基于字符串代数式的运算实现了另一个方法， 被称为query()， 可用于过滤运算：

```python

In[24]: result1 = df[(df.A < 0.5) & (df.B < 0.5)]
        result2 = df.query('A < 0.5 and B < 0.5')
        np.allclose(result1, result2)
Out[24]: True
```

除了计算性能更优之外， 这种方法的语法也比掩码代数式语法更好理解。 需要注意的是， query() 方法也支持用 @ 符号引用局部变量。

#### 3.13.5 性能决定使用时机

普通的计算方法与 eval/query 计算方法在计算时间上的差异并非总是那么明显， 普通方法在处理较小的数组时反而速度更快！ eval/query 方法的优点主要是节省内存， 有时语法也更加简洁。
可以通过下面的方法大概估算一下变量的内存消耗：

```python
In[28]: df.values.nbytes
Out[28]: 32000
```

### 3.14 参考资料

Pandas 在线文档 ([http://pandas.pydata.org/](http://pandas.pydata.org/))

《利用 Python 进行数据分析》

Stack Overflow 网站的 Pandas 话题（[http://stackoverflow.com/questions/tagged/pandas](http://stackoverflow.com/questions/tagged/pandas)）

PyVideo 上关于 Pandas 的教学视频（[http://pyvideo.org/tag/pandas/](http://pyvideo.org/tag/pandas/)）
