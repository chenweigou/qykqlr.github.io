# Pandas组队学习

[返回](./index.md)
[toc]

## 第8章 分类数据

```python
In[1]: import pandas as pd
       import numpy as np
       df = pd.read_csv('data/table.csv')
       df.head()
Out[1]：  School Class    ID Gender   Address  Height  Weight  Math Physics
        0    S_1   C_1  1101      M  street_1     173      63  34.0      A+
        1    S_1   C_1  1102      F  street_2     192      73  32.5      B+
        2    S_1   C_1  1103      M  street_2     186      82  87.2      B+
        3    S_1   C_1  1104      F  street_2     167      81  80.4      B-
        4    S_1   C_1  1105      F  street_4     159      64  84.8      B+
```

### 一、category的创建及其性质

#### 1. 分类变量的创建

（a）用Series创建

```python
In [2]: pd.Series(["a", "b", "c", "a"], dtype="category")
Out[2]: 0    a
        1    b
        2    c
        3    a
        dtype: category
        Categories (3, object): [a, b, c]
```

（b）对DataFrame指定类型创建

```python
In [3]: temp_df = pd.DataFrame({'A':pd.Series(["a", "b", "c", "a"], dtype="category"),'B':list('abcd')})
        temp_df.dtypes
Out[3]: A    category
        B      object
        dtype: object
```

（c）利用内置Categorical类型创建

```python
In [4]: cat = pd.Categorical(["a", "b", "c", "a"], categories=['a','b','c'])
        pd.Series(cat)
Out[4]: 0    a
        1    b
        2    c
        3    a
        dtype: category
        Categories (3, object): [a, b, c]
```

（d）利用cut函数创建

```python
In [5]: # 默认使用区间类型为标签
        pd.cut(np.random.randint(0,60,5), [0,10,30,60])
Out[5]: [(30, 60], (30, 60], (10, 30], (0, 10], (10, 30]]
        Categories (3, interval[int64]): [(0, 10] < (10, 30] < (30, 60]]

In [6]: # 可指定字符为标签
        pd.cut(np.random.randint(0,60,5), [0,10,30,60], right=False, labels=['0-10','10-30','30-60'])
Out[6]: [10-30, 10-30, 30-60, 30-60, 30-60]
        Categories (3, object): [0-10 < 10-30 < 30-60]
```

#### 2. 分类变量的结构

一个分类变量包括三个部分，元素值（values）、分类类别（categories）、是否有序（order）
从上面可以看出，使用cut函数创建的分类变量默认为有序分类变量
下面介绍如何获取或修改这些属性

（a）describe方法
该方法描述了一个分类序列的情况，包括非缺失值个数、元素值类别数（不是分类类别数）、最多次出现的元素及其频数

```python
In [7]: s = pd.Series(pd.Categorical(["a", "b", "c", "a",np.nan], categories=['a','b','c','d']))
        s.describe()
Out[7]: count     4
        unique    3
        top       a
        freq      2
        dtype: object
```

（b）categories和ordered属性

```python
In [8]: s.cat.categories
Out[8]: Index(['a', 'b', 'c', 'd'], dtype='object')

In [9]: s.cat.ordered
Out[9]: False
```

`s`的值是`["a", "b", "c", "a",np.nan]`， 是未排序的

#### 3. 类别的修改

（a）利用set_categories修改

```python
In [10]: # 修改分类，但本身值不会变化
         s = pd.Series(pd.Categorical(["a", "b", "c", "a",np.nan], categories=['a','b','c','d']))
         s.cat.set_categories(['new_a','c'])
Out[10]: 0    NaN
         1    NaN
         2      c
         3    NaN
         4    NaN
         dtype: category
         Categories (2, object): [new_a, c]

In [11]: # 类别不再存在的值会被修改为`NAN`，再恢复对应的类别值也不会恢复
         s = pd.Series(pd.Categorical(["a", "b", "c", "a",np.nan], categories=['a','b','c','d']))
         s.cat.set_categories(['new_a','c']).cat.set_categories(['a', 'b'])
Out[11]: 0    NaN
         1    NaN
         2    NaN
         3    NaN
         4    NaN
         dtype: category
         Categories (2, object): [a, b]
```

（b）利用rename_categories修改

```python
In [12]: # 需要注意的是该方法会把值和分类同时修改
         s = pd.Series(pd.Categorical(["a", "b", "c", "a",np.nan], categories=['a','b','c','d']))
         s.cat.rename_categories([1, 2, 3, 4])
Out[12]: 0      1
         1      2
         2      3
         3      1
         4    NaN
         dtype: category
         Categories (4, int64): [1, 2, 3, 4]

In [13]: # 利用字典修改值
         s.cat.rename_categories({'a':'new_a','b':'new_b'})
Out[13]: 0    new_a
         1    new_b
         2        c
         3    new_a
         4      NaN
         dtype: category
         Categories (4, object): [new_a, new_b, c, d]
```

（c）利用add_categories添加

```python
In [14]: s = pd.Series(pd.Categorical(["a", "b", "c", "a",np.nan], categories=['a','b','c','d']))
         s.cat.add_categories(['e'])
Out[14]: 0      a
         1      b
         2      c
         3      a
         4    NaN
         dtype: category
         Categories (5, object): [a, b, c, d, e]
```

（d）利用remove_categories移除

```python
In [15]: s = pd.Series(pd.Categorical(["a", "b", "c", "a",np.nan], categories=['a','b','c','d']))
         s.cat.remove_categories(['c'])
Out[15]: 0      a
         1      b
         2    NaN
         3      a
         4    NaN
         dtype: category
         Categories (3, object): [a, b, d]
```

（e）删除元素值未出现的分类类型

```python
In [16]: s = pd.Series(pd.Categorical(["a", "b", "c", "a",np.nan], categories=['a','b','c','d']))
         s.cat.remove_unused_categories()
Out[16]: 0      a
         1      b
         2      c
         3      a
         4    NaN
         dtype: category
         Categories (3, object): [a, b, c]
```

### 二、分类变量的排序

前面提到，分类数据类型被分为有序和无序，这非常好理解，例如分数区间的高低是有序变量，考试科目的类别一般看做无序变量

#### 1. 序的建立

（a）一般来说会将一个序列转为有序变量，可以利用as_ordered方法

```python
In [17]: s = pd.Series(["a", "d", "c", "a"]).astype('category').cat.as_ordered()
         s
Out[17]: 0    a
         1    d
         2    c
         3    a
         dtype: category
         Categories (3, object): [a < c < d]

In [18]: # 退化为无序变量，只需要使用as_unordered
         s.cat.as_unordered()
Out[18]: 0    a
         1    d
         2    c
         3    a
         dtype: category
         Categories (3, object): [a, c, d]
```

（b）利用set_categories方法中的order参数

```python
In [19]: pd.Series(["a", "d", "c", "a"]).astype('category').cat.set_categories(['a','c','d'],ordered=True)
Out[19]: 0    a
         1    d
         2    c
         3    a
         dtype: category
         Categories (3, object): [a < c < d]
```

（c）利用reorder_categories方法
这个方法的特点在于，新设置的分类必须与原分类为同一集合

```python
In [20]: s = pd.Series(["a", "d", "c", "a"]).astype('category')
         s.cat.reorder_categories(['a','c','d'],ordered=True)
Out[20]: 0    a
         1    d
         2    c
         3    a
         dtype: category
         Categories (3, object): [a < c < d]
```

#### 2. 排序

可以使用第一章介绍的值排序`sort_values`和索引排序`sort_index`

### 三、分类变量的比较操作

#### 1. 与标量或等长序列的比较

（a）标量比较

（b）等长序列比较

#### 2. 与另一分类变量的比较

（a）等式判别（包含等号和不等号）
两个分类变量的等式判别需要满足分类完全相同

（b）不等式判别（包含>=,<=,<,>）
两个分类变量的不等式判别需要满足两个条件：① 分类完全相同 ② 排序完全相同

### 四、问题与练习

#### 1. 问题

【问题一】 如何使用union_categoricals方法？它的作用是什么？

union_categoricals可以用来连接两个Category并且保存分类数据

```python
In [21]: from pandas.api.types import union_categoricals
         a = pd.Categorical(["b", "c"])
         b = pd.Categorical(["a", "b"])
         union_categoricals([a, b])
Out[21]  [b, c, a, b]
         Categories (3, object): [b, c, a]
```

【问题二】 利用concat方法将两个序列纵向拼接，它的结果一定是分类变量吗？什么情况下不是？

不是分类变量

【问题三】 当使用groupby方法或者value_counts方法时，分类变量的统计结果和普通变量有什么区别？

【问题四】 下面的代码说明了Series创建分类变量的什么“缺陷”？如何避免？（提示：使用Series中的copy参数）

Serial 创建分类变量会在原地址上创建， 修改创建后的Serial会改变原分类变量的值，使用`s = pd.Series(cat.copy(), name="cat")`

#### 2.练习

【练习一】 现继续使用第四章中的地震数据集，请解决以下问题：

```python
In [22]: df = pd.read_csv('data/Earthquake.csv')
         df.head()
Out[22]:          日期       时间     维度    经度       方向  距离   深度 烈度
        0  2003.05.20  12:17:44 AM  39.04  40.38        west  0.1  10.0  0.0
        1  2007.08.01  12:03:08 AM  40.79  30.09        west  0.1   5.2  4.0
        2  1978.05.07  12:41:37 AM  38.58  27.61  south_west  0.1   0.0  0.0
        3  1997.03.22  12:31:45 AM  39.47  36.44  south_west  0.1  10.0  0.0
        4  2000.04.02  12:57:38 AM  40.80  30.24  south_west  0.1   7.0  0.0
```

（a）现在将深度分为七个等级：[0,5,10,15,20,30,50,np.inf]，请以深度等级Ⅰ,Ⅱ,Ⅲ,Ⅳ,Ⅴ,Ⅵ,Ⅶ为索引并按照由浅到深的顺序进行排序。

```python
In [23]: df_a = df.copy()
         df_a['深度'] = pd.cut(df_a['深度'], [-1e-10,5,10,15,20,30,50,np.inf],labels=['Ⅰ','Ⅱ','Ⅲ','Ⅳ','Ⅴ','Ⅵ','Ⅶ'])
         df_a.set_index('深度').sort_index().head()
Out[23]:           日期          时间   维度   经度        方向   距离  烈度
        深度
        Ⅰ   2009.09.09  12:54:13 AM  42.42  43.03  north_east  95.4  0.0
        Ⅰ   1997.06.16  12:18:04 AM  37.92  29.17  north_east   3.2  0.0
        Ⅰ   2011.10.25  12:29:45 AM  38.96  43.64  south_east   1.6  3.9
        Ⅰ   1995.07.23  12:05:04 AM  37.61  29.29  north_east   3.2  0.0
        Ⅰ   2013.06.10  12:39:19 AM  38.53  43.85  south_east   1.6  3.7
```

（b）在（a）的基础上，将烈度分为4个等级：[0,3,4,5,np.inf]，依次对南部地区的深度和烈度等级建立多级索引排序。

```python
In [24]: df_a['烈度'] = pd.cut(df_a['烈度'], [-1e-10,3,4,5,np.inf],labels=['Ⅰ','Ⅱ','Ⅲ','Ⅳ'])
         df_a.set_index(['深度','烈度']).sort_index().head()
Out[24]:               日期          时间   维度   经度       方向   距离
        深度 烈度
        Ⅰ  Ⅰ   1978.05.07  12:41:37 AM  38.58  27.61  south_west  0.1
            Ⅰ   2000.02.07  12:11:45 AM  40.05  34.07  south_east  0.1
            Ⅰ   1971.05.20  12:08:46 AM  37.72  30.00  north_east  0.1
            Ⅰ   1985.01.28  12:20:56 AM  38.85  29.06  north_east  0.1
            Ⅰ   1990.07.05  12:43:04 AM  37.87  29.18        east  0.1
```

【练习二】 对于分类变量而言，调用第4章中的变形函数会出现一个BUG（目前的版本下还未修复）：例如对于crosstab函数，按照官方文档的说法，即使没有出现的变量也会在变形后的汇总结果中出现，但事实上并不是这样，比如下面的例子就缺少了原本应该出现的行'c'和列'f'。基于这一问题，请尝试设计my_crosstab函数，在功能上能够返回正确的结果。

```python
In [41]: foo = pd.Categorical(['a', 'b'], categories=['a', 'b', 'c'])
    ...: bar = pd.Categorical(['d', 'e'], categories=['d', 'e', 'f'])
    ...: pd.crosstab(foo, bar)
Out[41]: col_0  d  e
         row_0
         a      1  0
         b      0  1

In [42]: def my_crosstab(foo,bar):
    ...:     num = len(foo)
    ...:     s1 = pd.Series([i for i in list(foo.categories.union(set(foo)))],name='1nd var')
    ...:     s2 = [i for i in list(bar.categories.union(set(bar)))]
    ...:     df = pd.DataFrame({i:[0]*len(s1) for i in s2},index=s1)
    ...:     for i in range(num):
    ...:         df.at[foo[i],bar[i]] += 1
    ...:     return df.rename_axis('2st var',axis=1)
    ...: my_crosstab(foo,bar)
Out[42]: 2st var  d  e  f
         1nd var
         a        1  0  0
         b        0  1  0
         c        0  0  0
```