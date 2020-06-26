# Pandas组队学习

[返回](./index.md)
[toc]

## 第6章 缺失数据

```python
In[1]:  import pandas as pd
        import numpy as np
        df = pd.read_csv('data/table_missing.csv')
        df.head()
Out[1]:   School Class      ID Gender   Address  Height  Weight  Math Physics
        0    S_1   C_1     NaN      M  street_1     173     NaN  34.0      A+
        1    S_1   C_1     NaN      F  street_2     192     NaN  32.5      B+
        2    S_1   C_1  1103.0      M  street_2     186     NaN  87.2      B+
        3    S_1   NaN     NaN      F  street_2     167    81.0  80.4     NaN
        4    S_1   C_1  1105.0    NaN  street_4     159    64.0  84.8      A-
```

### 一、缺失观测及其类型

#### 1. 了解缺失信息

1. 使用isna方法和notna方法检查pandas数据是否有缺失值

    对Series使用会返回布尔列表

    ```python
    In[2]: df['Physics'].isna().head()
    Out[2]: 0    False
            1    False
            2    False
            3     True
            4    False
            Name: Physics, dtype: bool
    ```

    对DataFrame使用会返回布尔表

    ```python
    In[3]: df.isna().head()
    Out[3]:    School  Class     ID  Gender  Address  Height  Weight   Math  Physics
            0   False  False   True   False    False   False    True  False    False
            1   False  False   True   False    False   False    True  False    False
            2   False  False  False   False    False   False    True  False    False
            3   False   True   True   False    False   False   False  False     True
            4   False  False  False    True    False   False   False  False    False
    ```

    但对于DataFrame我们更关心到底每列有多少缺失值

    ```python
    In[4]: df.isna().sum()
    In[4]:  School      0
            Class       4
            ID          6
            Gender      7
            Address     0
            Height      0
            Weight     13
            Math        5
            Physics     4
            dtype: int64
    ```

    要统计每列有多少非缺失值可以用`df.notna().sum()` 或 **`df.count()`**。

2. 可以用返回的布尔数组当索引来查看缺失值所在行

    ```python
    In[5]: df[df['Physics'].isna()]
    Out[5]:    School Class      ID Gender   Address  Height  Weight  Math Physics
            3     S_1   NaN     NaN      F  street_2     167    81.0  80.4     NaN
            8     S_1   C_2  1204.0      F  street_5     162    63.0  33.8     NaN
            13    S_1   C_3  1304.0    NaN  street_2     195    70.0  85.2     NaN
            22    S_2   C_2  2203.0      M  street_4     155    91.0  73.8     NaN
    ```

3. 挑选出所有非缺失值列
    使用all就是全部非缺失值，如果是any就是至少有一个不是缺失值

    ```python
    In[5]: df[df.notna().all(1)]
    Out[5]:   School Class      ID Gender   Address  Height  Weight  Math Physics
        5     S_1   C_2  1201.0      M  street_5     159    68.0  97.0      A-
        6     S_1   C_2  1202.0      F  street_4     176    94.0  63.5      B-
        12    S_1   C_3  1303.0      M  street_7     188    82.0  49.7       B
        17    S_2   C_1  2103.0      M  street_4     157    61.0  52.5      B-
        21    S_2   C_2  2202.0      F  street_7     194    77.0  68.5      B+
        25    S_2   C_3  2301.0      F  street_4     157    78.0  72.3      B+
        27    S_2   C_3  2303.0      F  street_7     190    99.0  65.9       C
        28    S_2   C_3  2304.0      F  street_6     164    81.0  95.5      A-
        29    S_2   C_3  2305.0      M  street_4     187    73.0  48.9       B
    ```

#### 2. 三种缺失符号

|　np.nan | None | NaT| 
|----| ---- | ---- |
| np.nan是一个麻烦的东西，首先它不等与任何东西，甚至不等于自己 | None会等于自身 |  NaT是针对时间序列的缺失值，是Pandas的内置类型，可以完全看做时序版本的np.nan | 
| 在用equals函数比较时，自动略过两侧全是np.nan的单元格，因此结果不会影响 | None的布尔值为False | Nat与自己不等 |
| 其次，它在numpy中的类型为浮点，由此导致数据集读入时，即使原来是整数的列，只要有缺失值就会变为浮点型 | 修改布尔列表不会改变数据类型 | Nat使用equals时也会被跳过 |
| 此外，对于布尔类型的列表，如果是np.nan填充，那么它的值会自动变为True而不是False | 在传入数值类型后，会自动变为np.nan | 
| 但当修改一个布尔列表时，会改变列表类型，而不是赋值为True | 只有当传入object类型是保持不动，几乎可以认为，除非人工命名None，它基本不会自动出现在Pandas中 |
| 在所有的表格读取后，无论列是存放什么类型的数据，默认的缺失值全为np.nan类型 | 在使用equals函数时不会被略过，因此下面的情况下返回False | 

#### 3. Nullable类型与NA符号

这是Pandas在1.0新版本中引入的重大改变，其目的就是为了（在若干版本后）解决之前出现的混乱局面，统一缺失值处理方法

Nullable是一系列可以表示缺失值的数据类型

Nullable类似的缺失值在数据里面被设置为pd.NA

1. Nullable整形: 'Int'

    对于该种类型而言，它与原来标记int上的符号区别在于首字母大写：
    它的好处就在于，其中前面提到的三种缺失值都会被替换为统一的NA符号，且不改变数据类型

2. Nullable布尔: boolean

    需要注意的是，在1.0.2之前的版本含有pd.NA的布尔列表作为索引时会报错， 已修复

3. string类型: string

    string和object类型的一点重要区别就在于，在调用字符方法后，string类型返回的是Nullable类型，object则会根据缺失类型和数据类型而改变

#### 4. NA的特性

1. pd.NA做逻辑运算时只需看该逻辑运算的结果是否依赖pd.NA的取值，如果依赖，则结果还是NA，如果不依赖，则直接计算结果

    ```python
    In [6]: type(pd.NA)
    Out[6]: pandas._libs.missing.NAType

    In [7]: True | pd.NA
    Out[7]: True

    In [8]: pd.NA | True
    Out[8]: True

    In [9]: False | pd.NA
    Out[9]: <NA>

    In [10]: False & pd.NA
    Out[10]: False

    In [11]: True & pd.NA
    Out[11]: <NA>
    ```

    pd.NA 不能使用 and 和 or
    np.nan, None, Nat 不能用 | 和 &， 只能用 and 和 or

2. 算术运算和比较运算

    除了下面两种情况， 其他都是pd.NA

    ```python
    In [12]: pd.NA ** 0
    Out[12]: 1

    In [13]: 1 ** pd.NA
    Out[13]: 1
    ```

#### 5. convert_dtypes方法

这个函数的功能在读取数据时，就把数据列转为Nullable类型，是1.0的新函数

```python
In [13]: pd.read_csv('data/table_missing.csv').dtypes
Out[13]:
School      object
Class       object
ID         float64
Gender      object
Address     object
Height       int64
Weight     float64
Math       float64
Physics     object
dtype: object

In [14]: pd.read_csv('data/table_missing.csv').convert_dtypes().dtypes
Out[14]:
School      string
Class       string
ID           Int64
Gender      string
Address     string
Height       Int64
Weight       Int64
Math       float64
Physics     string
dtype: object
```

### 二、缺失数据的运算与分组

### 五、问题与练习

1. 加号与乘号规则
    使用加法时，缺失值为0
    使用乘法时，缺失值为1
    使用累计函数时，缺失值自动略过

2. groupby方法中的缺失值自动忽略为缺失值的组

### 三、填充与剔除

1. fillna方法
    （a）值填充与前后向填充（分别与ffill方法和bfill方法等价）

    ```python
    In[15]: df['Physics'].fillna('missing').head()
    ...
    In[16]: df['Physics'].fillna(method='ffill').head()
    ...
    In[17]: df['Physics'].fillna(method='backfill').head()
    ...
    ```

    (b) 填充中的对齐特性: 传入的参数中没有C，根据对齐特点不会被填充

    ```python
    In [18]: df_f.fillna(df_f.mean()[['A','B']])
    Out[18]:      A    B    C
            0  1.0  2.0  3.0
            1  3.0  4.0  5.0
            2  2.0  3.0  NaN
    ```

2. dropna方法
    （a）axis参数：设置删除存在na值的行或列
    （b）how参数：可以选all或者any，表示全为缺失去除和存在缺失去除
    （c）subset参数（即在某一组列范围中搜索缺失值）

    ```python
    In[19]: df_d.dropna(axis=0,subset=['B','C'])
    Out[19]:    A    B  C
            1 NaN  3.0  2
            2 NaN  2.0  1

    ```

### 四、插值（interpolation）

#### 1. 线性插值

（a）索引无关的线性插值
    默认状态下，interpolate会对缺失的值进行线性插值
（b）与索引有关的插值
    method中的index和time选项可以使插值线性地依赖索引，即插值为索引的线性函数

#### 2. 高级插值方法

此处的高级指的是与线性插值相比较，例如样条插值、多项式插值、阿基玛插值等（需要安装Scipy），方法详情请看[这里](https://pandas.pydata.org/pandas-docs/version/1.0.0/reference/api/pandas.DataFrame.interpolate.html#pandas.DataFrame.interpolate)

#### 3. interpolate中的限制参数

（a）limit表示最多插入多少个
（b）limit_direction表示插值方向，可选forward,backward,both，默认前向
（c）limit_area表示插值区域，可选inside,outside，默认None




### 五、问题与练习

#### 1. 问题

【问题一】 如何删除缺失值占比超过25%的列？  

`df.loc[:, df.isna().sum()/df.shape[0] <= 0.25]`

【问题二】 什么是Nullable类型？请谈谈为什么要引入这个设计？

Nullable是一系列可以表示缺失值的数据类型, 引入这个设计的目的就是为了（在若干版本后）解决之前出现的混乱局面，统一缺失值处理方法。

【问题三】 对于一份有缺失值的数据，可以采取哪些策略或方法深化对它的了解？
    1. 将平均数或者前后的值补充到缺失值
    2. 删除有缺失值的行或列

#### 2. 练习

【练习一】现有一份虚拟数据集，列类型分别为string/浮点/整型，请解决如下问题：

（a）请以列类型读入数据，并选出C为缺失值的行。

```python
In[20]: df = pd.read_csv('data/Missing_data_one.csv').convert_dtypes()
        df.head()
Out[20]:         A      B     C
        0  not_NaN  0.922     4
        1  not_NaN  0.700  <NA>
        2  not_NaN  0.503     8
        3  not_NaN  0.938     4
        4  not_NaN  0.952    10
In[21]: df[df['C'].isna()]
Out[21]:          A      B     C
        1   not_NaN  0.700  <NA>
        5   not_NaN  0.972  <NA>
        11  not_NaN  0.736  <NA>
        19  not_NaN  0.684  <NA>
        21  not_NaN  0.913  <NA>
```

（b）现需要将A中的部分单元转为缺失值，单元格中的最小转换概率为25%，且概率大小与所在行B列单元的值成正比。

```python
In[22]: df = pd.read_csv('data/Missing_data_one.csv').convert_dtypes()
        total_b = df['B'].sum()
        min_b = df['B'].min()
        df['A'] = pd.Series(list(zip(df['A'].values
                            ,df['B'].values))).apply(lambda x:x[0] if np.random.rand()>0.25*x[1]/min_b else np.nan)
        df.head()
Out[22]:         A      B     C
        0      NaN  0.922     4
        1  not_NaN  0.700  <NA>
        2  not_NaN  0.503     8
        3  not_NaN  0.938     4
        4      NaN  0.952    10
```

【练习二】 现有一份缺失的数据集，记录了36个人来自的地区、身高、体重、年龄和工资，请解决如下问题：
（a）统计各列缺失的比例并选出在后三列中至少有两个非缺失值的行。

```python
In [23]: df = pd.read_csv('data/Missing_data_two.csv')
         df.head()
Out[23]:    编号    地区        身高     体重        年龄       工资
        0   1       A       157.50      NaN         47.0    15905.0
        1   2       B       202.00      91.80       25.0    NaN
        2   3       C       169.09      62.18       NaN     NaN
        3   4       A       166.61      59.95       77.0    5434.0
        4   5       B       185.19      NaN         62.0    4242.0

In [24]: df_not2na = df[df.iloc[:,-3:].isna().sum(1)<=1]
         df_not2na.head()
Out[24]:    编号     地区        身高     体重        年龄       工资
        0   1       A       157.50      NaN         47.0    15905.0
        1   2       B       202.00      91.80       25.0    NaN
        3   4       A       166.61      59.95       77.0    5434.0
        4   5       B       185.19      NaN         62.0    4242.0
        5   6       A       187.13      78.42       55.0    13959.0
```

（b）请结合身高列和地区列中的数据，对体重进行合理插值。

```python
In [25]: df_method_1 = df.copy()
         for name,group in df_method_1.groupby('地区'):
             df_method_1.loc[group.index,'体重'] = group[['身高','体重']].sort_values(by='身高').interpolate()['体重']
         df_method_1['体重'] = df_method_1['体重'].round(decimals=2)
         df_method_1.head()
Out[25]:    编号    地区        身高      体重        年龄       工资
        0   1       A       157.50      53.58       47.0    15905.0
        1   2       B       202.00      91.80       25.0    NaN
        2   3       C       169.09      62.18       NaN     NaN
        3   4       A       166.61      59.95       77.0    5434.0
        4   5       B       185.19      81.75       62.0    4242.0
```
