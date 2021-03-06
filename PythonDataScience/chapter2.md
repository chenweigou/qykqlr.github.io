# Python 数据科学手册

[返回](./index.md)

[toc]

## 第二章 NumPy入门

SciPy / PyData 社区中的大多数人都用 np 作为别名导入 NumPy

```python
In[2]: import numpy as np
```

### 2.1 理解Python中的数据类型

Python 变量不仅是它们的值， 还包括了关于值的类型的一些额外信息

#### 2.1.1 Python整型不仅仅是一个整型

Python 3.4 中的一个整型实际上包括 4 个部分。

* ob_refcnt 是一个引用计数， 它帮助 Python 默默地处理内存的分配和回收。
* ob_type 将变量的类型编码。
* ob_size 指定接下来的数据成员的大小。
* ob_digit 包含我们希望 Python 变量表示的实际整型值。

```C
struct _longobject {
long ob_refcnt;
PyTypeObject *ob_type;
size_t ob_size;
long ob_digit[1];
};
```

#### 2.1.2 Python列表不仅仅是一个列表

列表中的每一项必须包含各自的类型信息、 引用计数和其他信息；也就是说， 每一项都是一个完整的 Python 对象。
固定类型的 NumPy 式数组缺乏这种灵活性， 但是能更有效地存储和操作数据。

#### 2.1.3 Python中的固定类型数组

内置的数组（array） 模块（在 Python 3.3 之后可用） 可以用于创建统一类型的密集数组

```python
In[6]:  import array
        L = list(range(10))
        A = array.array('i', L)
        A
Out[6]: array('i', [0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

这里的 'i' 是一个数据类型码， 表示数据为整型。

更实用的是 NumPy 包中的 ndarray 对象。

#### 2.1.4 从Python列表创建数组

NumPy 要求数组必须包含同一类型的数据。

可以用 np.array 从 Python 列表创建数组

```python
In[8]:  # 整型数组:
        np.array([1, 4, 2, 5, 3])
Out[8]: array([1, 4, 2, 5, 3])
```

如果类型不匹配， NumPy 将会向上转换(如果可行)。
如果希望明确设置数组的数据类型， 可以用 dtype 关键字：

```python
In[10]: np.array([1, 2, 3, 4], dtype='float32')
Out[10]: array([ 1., 2., 3., 4.], dtype=float32)
```

NumPy 数组可以被指定为多维的

```python
In[11]: # 嵌套列表构成的多维数组
        np.array([range(i, i + 3) for i in [2, 4, 6]])
Out[11]: array([[2, 3, 4],
                [4, 5, 6],
                [6, 7, 8]])
```

#### 2.1.5 从头创建数组

```python
In[12]: # 创建一个长度为10的数组， 数组的值都是0
        np.zeros(10, dtype=int)
Out[12]: array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0])

In[13]: # 创建一个3×5的浮点型数组， 数组的值都是1
        np.ones((3, 5), dtype=float)
Out[13]: array([[ 1., 1., 1., 1., 1.],
                [ 1., 1., 1., 1., 1.],
                [ 1., 1., 1., 1., 1.]])

In[14]: # 创建一个3×5的浮点型数组， 数组的值都是3.14
        np.full((3, 5), 3.14)
Out[14]: array([[ 3.14, 3.14, 3.14, 3.14, 3.14],
                [ 3.14, 3.14, 3.14, 3.14, 3.14],
                [ 3.14, 3.14, 3.14, 3.14, 3.14]])

In[15]: # 创建一个3×5的浮点型数组， 数组的值是一个线性序列
        # 从0开始， 到20结束， 步长为2
        # （它和内置的range()函数类似）
        np.arange(0, 20, 2)
Out[15]: array([ 0, 2, 4, 6, 8, 10, 12, 14, 16, 18])

In[16]: # 创建一个5个元素的数组， 这5个数均匀地分配到0~1
        np.linspace(0, 1, 5)
Out[16]: array([ 0. , 0.25, 0.5 , 0.75, 1. ])

In[17]: # 创建一个3×3的、 在0~1均匀分布的随机数组成的数组
        np.random.random((3, 3))
Out[17]: array([[ 0.99844933, 0.52183819, 0.22421193],
                [ 0.08007488, 0.45429293, 0.20941444],
                [ 0.14360941, 0.96910973, 0.946117 ]])

In[18]: # 创建一个3×3的、 均值为0、 方差为1的
        # 正态分布的随机数数组
        np.random.normal(0, 1, (3, 3))
Out[18]: array([[ 1.51772646, 0.39614948, -0.10634696],
                [ 0.25671348, 0.00732722, 0.37783601],
                [ 0.68446945, 0.15926039, -0.70744073]])

In[19]: # 创建一个3×3的、 [0, 10)区间的随机整型数组
        np.random.randint(0, 10, (3, 3))
Out[19]: array([[2, 3, 4],
                [5, 7, 8],
                [0, 5, 0]])

In[20]: # 创建一个3×3的单位矩阵
        np.eye(3)
Out[20]: array([[ 1., 0., 0.],
                [ 0., 1., 0.],
                [ 0., 0., 1.]])

In[21]: # 创建一个由3个整型数组成的未初始化的数组
        # 数组的值是内存空间中的任意值
        np.empty(3)
Out[21]: array([ 1., 1., 1.])
```

#### 2.1.6 NumPy标准数据类型

构建一个数组时， 你可以用一个字符串参数来指定数据类型, 或者用相关的 NumPy 对象来指定：

```python
np.zeros(10, dtype='int16')
np.zeros(10, dtype=np.int16)
```

| 数据类型 | 描述 |
| ---- | ---- |
| bool_ | 布尔值（真、 True 或假、 False） ， 用一个字节存储 |
| int_ | 默认整型（类似于 C 语言中的 long， 通常情况下是 int64 或 int32） |
| intc | 同 C 语言的 int 相同（通常是 int32 或 int64） |
| intp | 用作索引的整型（和 C 语言的 ssize_t 相同， 通常情况下是 int32或 int64） |
| int8 | 字节（byte， 范围从–128 到 127） |
| int16 | 整型（范围从–32768 到 32767） |
| int32 | 整型（范围从–2147483648 到 2147483647） |
| int64 | 整型（范围从–9223372036854775808 到 9223372036854775807） |
| uint8 | 无符号整型（范围从 0 到 255） |
| uint16 | 无符号整型（范围从 0 到 65535） |
| uint32 | 无符号整型（范围从 0 到 4294967295） |
| uint64 | 无符号整型（范围从 0 到 18446744073709551615） |
| float_ | float64 的简化形式 |
| float16 | 半精度浮点型： 符号比特位， 5 比特位指数（exponent） ， 10 比特位尾数（mantissa） |
| float32 | 单精度浮点型： 符号比特位， 8 比特位指数， 23 比特位尾数 |
| float64 | 双精度浮点型： 符号比特位， 11 比特位指数， 52 比特位尾数 |
| complex_ | complex128 的简化形式 |
| complex64 | 复数， 由两个 32 位浮点数表示 |
| complex128 | 复数， 由两个 64 位浮点数表示 |

###　2.2 NumPy数组基础

#### 2.2.1 NumPy数组的属性

每个数组有 nidm（数组的维度） 、 shape（数组每个维度的大小） 和size（数组的总大小） 属性
另外一个有用的属性是 dtype， 它是数组的数据类型
其他的属性包括表示每个数组元素字节大小的 itemsize， 以及表示数组总字节大小的属性 nbytes
**一般来说**， 可以认为 nbytes 跟 itemsize 和 size 的乘积大小相等。

#### 2.2.2 数组索引： 获取单个元素

在一维数组中， 你也可以通过中括号指定索引获取第 i 个值（从 0 开始计数）
为了获取数组的末尾索引， 可以用负值索引
在多维数组中， 可以用逗号分隔的索引元组获取元素
也可以用以上索引方式修改元素值

请注意， NumPy 数组是固定类型的。

#### 2.2.3 数组切片： 获取子数组

我们也可以用切片（slice） 符号获取子数组， 切片符号用冒号（:） 表示。`x[start:stop:step]`
当step为负时， start和stop默认是被交换的

你可以将索引与切片组合起来获取数组的单行和单列， 用一个冒号（:） 表示空切片, 也可以省去空切片

```python
In[28]: print(x2[:, 0]) # x2的第一列
[12 7 1]
In[29]: print(x2[0, :]) # x2的第一行
[12 5 2 4]
In[30]: print(x2[0]) #等于x2[0, :]
[12 5 2 4]
```

数组切片返回的是数组数据的视图， 而不是数值数据的副本。 在 Python 列表中， 切片是值的副本。

可以通过 copy()方法明确地复制数组里的数据或子数组

```python
In[35]: x2_sub_copy = x2[:2, :2].copy()
        print(x2_sub_copy)
[[99 5]
 [ 7 6]]
```

#### 2.2.4 数组的变形

数组变形最灵活的实现方式是通过 reshape() 函数来实现。

```python
In[38]: grid = np.arange(1, 10).reshape((3, 3))
        print(grid)
[[1 2 3]
 [4 5 6]
 [7 8 9]]
```

另外一个常见的变形模式是将一个一维数组转变为二维的行或列的矩阵。 你也可以通过 reshape 方法来实现， 或者更简单地在一个切片操作中利用 newaxis 关键字：

```python
In[39]: x = np.array([1, 2, 3])
        # 通过变形获得的行向量
        x.reshape((1, 3))
Out[39]: array([[1, 2, 3]])

In[40]: # 通过newaxis获得的行向量
        x[np.newaxis, :]
Out[40]: array([[1, 2, 3]])

In[41]: # 通过变形获得的列向量
        x.reshape((3, 1))
Out[41]: array([[1],
                [2],
                [3]])

In[42]: # 通过newaxis获得的列向量
        x[:, np.newaxis]
Out[42]: array([[1],
                [2],
                [3]])
```

#### 2.2.5 数组拼接和分裂

1. 数组的拼接
    拼接或连接 NumPy 中的两个数组主要由np.concatenate、 np.vstack 和 np.hstack 例程实现。 np.concatenate 将数组元组或数组列表作为第一个参数

    ```python
    In[43]: x = np.array([1, 2, 3])
            y = np.array([3, 2, 1])
            np.concatenate([x, y])
    Out[43]: array([1, 2, 3, 3, 2, 1])
    ```

    你也可以一次性拼接两个以上数组, np.concatenate 也可以用于二维数组的拼接

    ```python
    In[44]: z = [99, 99, 99]
            print(np.concatenate([x, y, z]))
    [ 1 2 3 3 2 1 99 99 99]

    In[45]: grid = np.array([[1, 2, 3],
                             [4, 5, 6]])

    In[46]: # 沿着第一个轴拼接
            np.concatenate([grid, grid])

    Out[46]: array([[1, 2, 3],
                   [4, 5, 6],
                   [1, 2, 3],
                   [4, 5, 6]])

    In[47]: # 沿着第二个轴拼接（从0开始索引）
            np.concatenate([grid, grid], axis=1)
    Out[47]: array([[1, 2, 3, 1, 2, 3],
                    [4, 5, 6, 4, 5, 6]])
    ```

    沿着固定维度处理数组时， 使用 np.vstack（垂直栈） 和np.hstack（水平栈） 函数会更简洁

    ```python
    In[48]: x = np.array([1, 2, 3])
            grid = np.array([[9, 8, 7],
                             [6, 5, 4]])
            # 垂直栈数组
            np.vstack([x, grid])
    Out[48]: array([[1, 2, 3],
                    [9, 8, 7],
                    [6, 5, 4]])

    In[49]: # 水平栈数组
            y = np.array([[99],
                          [99]])
            np.hstack([grid, y])
    Out[49]: array([[ 9, 8, 7, 99],
                    [ 6, 5, 4, 99]])
    ```

    与之类似， np.dstack 将沿着第三个维度拼接数组。

2. 数组的分裂

    与拼接相反的过程是分裂。 分裂可以通过 np.split、 np.hsplit和 np.vsplit 函数来实现。

    ```python
    In[50]: x = [1, 2, 3, 99, 99, 3, 2, 1]
            x1, x2, x3 = np.split(x, [3, 5])
            print(x1, x2, x3)
    [1 2 3] [99 99] [3 2 1]
    ```

    相关的np.hsplit 和 np.vsplit 的用法也类似：

    ```python
    In[51]: grid = np.arange(16).reshape((4, 4))
            grid
    Out[51]: array([[ 0, 1, 2, 3],
                    [ 4, 5, 6, 7],
                    [ 8, 9, 10, 11],
                    [12, 13, 14, 15]])

    In[52]: upper, lower = np.vsplit(grid, [2])
            print(upper)
            print(lower)
    [[0 1 2 3]
     [4 5 6 7]]
    [[ 8 9 10 11]
     [12 13 14 15]]

    In[53]: left, right = np.hsplit(grid, [2])
            print(left)
            print(right)
    [[ 0 1]
     [ 4 5]
     [ 8 9]
     [12 13]]
    [[ 2 3]
     [ 6 7]
     [10 11]
     [14 15]]
    ```

同样， np.dsplit 将数组沿着第三个维度分裂

### 2.3 NumPy数组的计算： 通用函数

#### 2.3.1 缓慢的循环

CPython 在每次循环时必须做数据类型的检查和函数的调度

#### 2.3.2 通用函数介绍

通过通用函数用向量的方式进行计算几乎总比用 Python 循环实现的计算更加有效， 尤其是当数组很大时。 

#### 2.3.3 探索NumPy的通用函数

01. 数组的运算
    NumPy 通用函数的使用方式非常自然， 因为它用到了 Python 原生的算术运算符， 标准的加、 减、 乘、 除都可以使用, 还有逻辑非、 ** 表示的指数运算符和 % 表示的模运算符的一元通用函数

    表2-2： NumPy实现的算术运算符

    | 运算符 | 对应的通用函数 | 描述 |
    | ----- | ----- | ----- |
    | + | np.add | 加法运算（即 1 + 1 = 2） |
    | - | np.subtract | 减法运算（即 3 - 2 = 1）|
    | - | np.negative | 负数运算（ 即 -2） |
    | * | np.multiply | 乘法运算（即 2 \* 3 = 6） |
    | / | np.divide | 除法运算（即 3 / 2 = 1.5） |
    | // | np.floor_divide | 地板除法运算（floor division， 即 3 // 2 = 1） |
    | ** | np.power | 指数运算（即 2 ** 3 = 8） |
    | % | np.mod | 模 / 余数（ 即 9 % 4 = 1） |

02. 绝对值

    正如 NumPy 能理解 Python 内置的运算操作， NumPy 也可以理解Python 内置的绝对值函数。NumPy 通用函数是 np.absolute， 该函数也可以用别名np.abs 来访问

03. 三角函数

    NumPy 提供了大量好用的通用函数， 其中对于数据科学家最有用的就是三角函数。

04. 指数和对数

    NumPy 中另一个常用的运算通用函数是指数运算。

    ```python
    In[18]: x = [1, 2, 3]
            print("x =", x)
            print("e^x =", np.exp(x))
            print("2^x =", np.exp2(x))
            print("3^x =", np.power(3, x))
    x = [1, 2, 3]
    e^x = [ 2.71828183 7.3890561 20.08553692]
    2^x = [ 2. 4. 8.]
    3^x = [ 3 9 27]
    ```

    指数运算的逆运算， 即对数运算`np.log`也是可用的。

    ```python
    In[19]: x = [1, 2, 4, 10]
            print("x =", x)
            print("ln(x) =", np.log(x))
            print("log2(x) =", np.log2(x))
            print("log10(x) =", np.log10(x))x = [1, 2, 4, 10]
    ln(x) = [ 0. 0.69314718 1.38629436 2.30258509]
    log2(x) = [ 0. 1. 2. 3.32192809]
    log10(x) = [ 0. 0.30103 0.60205999 1. ]
    ```

    还有一些特殊的版本， 对于非常小的输入值可以保持较好的精度：`np.expm1` 和 `np.log1p`

    ```python
    In[20]: x = [0, 0.001, 0.01, 0.1]
            print("exp(x) - 1 =", np.expm1(x))
            print("log(1 + x) =", np.log1p(x))
    exp(x) - 1 = [ 0. 0.0010005 0.01005017 0.10517092]
    log(1 + x) = [ 0. 0.0009995 0.00995033 0.09531018]
    ```

05. 专用的通用函数

    NumPy 还提供了很多通用函数， 包括双曲三角函数、 比特位运算、 比较运算符、 弧度转化为角度的运算、 取整和求余运算，等等。
    还有一个更加专用， 也更加晦涩的通用函数优异来源是子模块scipy.special。 

    ```python
    In[21]: from scipy import special

    In[22]: # Gamma函数（广义阶乘， generalized factorials） 和相关函数
            x = [1, 5, 10]
            print("gamma(x) =", special.gamma(x))
            print("ln|gamma(x)| =", special.gammaln(x))
            print("beta(x, 2) =", special.beta(x, 2))
    gamma(x) = [ 1.00000000e+00 2.40000000e+01 3.62880000e+05]ln|gamma(x)| = [ 0. 3.17805383 12.80182748]
    beta(x, 2) = [ 0.5 0.03333333 0.00909091]

    In[23]: # 误差函数（高斯积分）
            # 它的实现和它的逆实现
            x = np.array([0, 0.3, 0.7, 1.0])
            print("erf(x) =", special.erf(x))
            print("erfc(x) =", special.erfc(x))
            print("erfinv(x) =", special.erfinv(x))
    erf(x) = [ 0. 0.32862676 0.67780119 0.84270079]
    erfc(x) = [ 1. 0.67137324 0.32219881 0.15729921]
    erfinv(x) = [ 0. 0.27246271 0.73286908 inf]
    ```

#### 2.3.4 高级的通用函数特性

01. 指定输出

    在进行大量运算时， 有时候指定一个用于存放运算结果的数组是非常有用的。 所有的通用函数都可以通过 out 参数来指定计算结果的存放位置。
    这个特性也可以被用作数组视图， 例如可以将计算结果写入指定数组的每隔一个元素的位置：

    ```python
    In[25]: y = np.zeros(10)
            np.power(2, x, out=y[::2])
            print(y)
    [ 1. 0. 2. 0. 4. 0. 8. 0. 16. 0.]
    ```

02. 聚合

    二元通用函数有些非常有趣的聚合功能， 这些聚合可以直接在对象上计算。
    一个 reduce 方法会对给定的元素和操作重复执行， 直至得到单个的结果。

    ```python
    In[26]: x = np.arange(1, 6)
            np.add.reduce(x)
    Out[26]: 15

    In[27]: np.multiply.reduce(x)
    Out[27]: 120
    ```

    如果需要存储每次计算的中间结果， 可以使用 accumulate：

    ```python
    In[28]: np.add.accumulate(x)
    Out[28]: array([ 1, 3, 6, 10, 15])

    In[29]: np.multiply.accumulate(x)
    Out[29]: array([ 1, 2, 6, 24, 120])

    ```

    在一些特殊情况中， NumPy 提供了专用的函数（np.sum、 np.prod、 np.cumsum、 np.cumprod ） ， 它们也可以实现以上 reduce 的功能。

03. 外积

    最后， 任何通用函数都可以用 outer 方法获得两个不同输入数组所有元素对的函数运算结果。

    ```python
    In[30]: x = np.arange(1, 6)
            np.multiply.outer(x, x)
    Out[30]: array([[ 1, 2, 3, 4, 5],
                    [ 2, 4, 6, 8, 10],
                    [ 3, 6, 9, 12, 15],
                    [ 4, 8, 12, 16, 20],
                    [ 5, 10, 15, 20, 25]])
    ```

#### 2.3.5 通用函数： 更多的信息

### 2.4 聚合： 最小值、 最大值和其他值

#### 2.4.1 数组值求和

Python和Numpy都有函数`sum`用于计算一个数组中所有元素的和, 但是，因为 NumPy 的 `sum` 函数在编译码中执行操作，所以 NumPy 的 操作计算得更快一些:

```python
In[4]:  big_array = np.random.rand(1000000)
        %timeit sum(big_array)
        %timeit np.sum(big_array)
10 loops, best of 3: 104 ms per loop
1000 loops, best of 3: 442 μs per loop
```

#### 2.4.2 最小值和最大值

Python和Numpy都有`min`和`max`函数, 同样, NumPy的操作计算得更快:

```python
In[6]: np.min(big_array), np.max(big_array)
Out[6]: (1.1717128136634614e-06, 0.9999976784968716)

In[7]:  %timeit min(big_array)
        %timeit np.min(big_array)
10 loops, best of 3: 82.3 ms per loop 1000 loops, best of 3: 497 μs per loop
```

对于 min、max、sum 和其他 NumPy 聚合，一种更简洁的语法形式是数 组对象直接调用这些方法:

```python
In[8]: print(big_array.min(), big_array.max(), big_array.sum())
1.17171281366e-06 0.999997678497 499911.628197
```

#### 2.4.4 多维度聚合

聚合函数还有一个参数，用于指定沿着哪个轴的方向进行聚合。

```python
In[11]: M.min(axis=0)
Out[11]: array([ 0.66859307, 0.03783739, 0.19544769, 0.06682827])
```

axis 关键字指定的 是数组将会被折叠的维度，而不是将要返回的维度。

#### 2.4.5 其他聚合函数


| 函数名称 | 描述 |
| ----- | ----- |
| np.sum | 计算元素的和 |
| np.prod | 计算元素的积 |
| np.mean | 计算元素的平均值 |
| np.std | 计算元素的标准差 |
| np.var | 计算元素的方差 |
| np.min | 找出最小值 |
| np.max | 找出最大值 |
| np.argmin | 找出最小值的索引 |
| np.argmax | 找出最大值的索引 |
| np.median | 计算元素的中位数 |
| np.percentile | 计算基于元素排序的统计值(分位数) |
| np.any | 验证任何一个元素是否为真 |
| np.all | 验证所有元素是否为真 |

### 2.5 数组的计算:广播

广播可以简单理解为用于不同大小数组的二元运算通用函数(加、 减、乘等)的一组规则。

#### 2.5.1 广播的介绍

广播允许二元运算操作可以用于不同大小的数组。

```python
In[2]: a = np.array([0, 1, 2])

In[3]: a + 5
Out[3]: array([5, 6, 7])

In[4]: M = np.ones((3, 3))
In[5]: M + a
Out[5]: array([[ 1., 2., 3.],
               [ 1., 2., 3.],
               [ 1., 2., 3.]])

In[6]: b = np.arange(3)[:, np.newaxis]
       b
Out[6]:[[0]
        [1]
        [2]]
In[7]: a + b
Out[7]: array([[0, 1, 2],
               [1, 2, 3],
               [2, 3, 4]])
```

#### 2.5.2 广播的规则

* 规则 1:如果两个数组的维度数不相同，那么小维度数组的形状将会在最左边补1。
* 规则 2:如果两个数组的形状在任何一个维度上都不匹配，那么数组的形状会沿着维度为1的维度扩展以匹配另外一个数组的形状。
* 规则 3:如果两个数组的形状在任何一个维度上都不匹配并且没有任何一个维度等于1，那么会引发异常。

### 2.6 比较、掩码和布尔逻辑

#### 2.6.1 示例:统计下雨天数

#### 2.6.2 和通用函数类似的比较操作

NumPy 还实 现了如 <(小于)和 >(大于)的逐元素比较的通用函数。这些比较运 算的结果是一个布尔数据类型的数组。

```python
In[4]: x = np.array([1, 2, 3, 4, 5])
In[5]:x<3 #小于
Out[5]: array([ True, True, False, False, False], dtype=bool)
```

另外，利用复合表达式实现对两个数组的逐元素比较也是可行的:

```python
In[11]: (2 * x) == (x ** 2)
Out[11]: array([False, True, False, False, False], dtype=bool)
```

| 运算符 | 对应的通用函数 |
| ----- | ----- |
| == | np.equal |
| != | np.not_equal |
| < | np.less |
| <= | np.less_equal |
| &gt; | np.greater |
| &gt;= | np.greater_equal |

#### 2.6.3 操作布尔数组
    
我们有一个二维数组x：

```python
In[14]: print(x)
[[5 0 3 3]
 [7 9 3 5]
 [2 4 7 6]]
```

01. 统计记录的个数

    `np.count_nonzero` 函数可以统计布尔数组中 True 记录的个数，另外一种实现方式是利用`np.sum`， `sum`的好处是，它也可以沿着行或列进行

    ```python
    In[15]: # 有多少值小于6？
            np.count_nonzero(x < 6)
    Out[15]: 8

    In[16]: np.sum(x < 6)
    Out[16]: 8

    In[17]: # 每行有多少值小于6？
    np.sum(x < 6, axis=1)Out[17]: array([4, 2, 2])
    ```

    可以用 `np.any()` 或 `np.all()` 快速检查任意或者所有这些值是否为 True， 这两个函数也可以沿着特定的坐标轴

02. 布尔运算符

    NumPy 用通用函数重载了逻辑运算符 &、 |、 ^ 和 ~ 

    | 运算符 | 对应通用函数 |
    | ----- | ----- |
    | & | np.bitwise_and |
    | &#124; | np.bitwise_or |
    | ^ | np.bitwise_xor |
    | ~ | np.bitwise_not |

    and 和 or 对整个对象执行单个布尔运算， 而 & 和 | 对一个对象的内容（单个比特或字节） 执行多个布尔运算。

#### 2.6.4 将布尔数组作为掩码

利用布尔数组作为掩码可以从数组中取出需要的数据

```python
In[28]: x[x < 5]
Out[28]: array([0, 3, 3, 3, 2, 4])
```

### 2.7 花哨的索引

我们将介绍另外一种数组索引， 也称作**花哨的索引**（fancy indexing） 。 花哨的索引和前面那些简单的索引非常类似， 但是传递的是索引数组， 而不是单个标量。 花哨的索引让我们能够快速获得并修改复杂的数组值的子数据集。

#### 2.7.1 探索花哨的索引

花哨的索引即传递一个索引数组来一次性获得多个数组元素。

```python
In[1]:  import numpy as np
        rand = np.random.RandomState(42)
        x = rand.randint(100, size=10)
        print(x)
[51 92 14 71 60 20 82 86 74 74]
```

花哨的索引也对多个维度适用。

```python
In[5]:  X = np.arange(12).reshape((3, 4))
        X
Out[5]: array([[ 0, 1, 2, 3],
               [ 4, 5, 6, 7],
               [ 8, 9, 10, 11]])

In[6]:  row = np.array([0, 1, 2])
        col = np.array([2, 1, 3])
        X[row, col]
Out[6]: array([ 2, 5, 11])

In[7]: X[row[:, np.newaxis], col]
Out[7]: array([[ 2, 1, 3],
               [ 6, 5, 7],
               [10, 9, 11]])
```

利用广播，当我们将一个列向量和一个行向量组合在一个索引中时， 会得到一个二维的结果。

#### 2.7.2 组合索引

花哨的索引可以和其他索引方案结合起来形成更强大的索引操作：

```python
In[9]: print(X)
[[ 0 1 2 3]
 [ 4 5 6 7]
 [ 8 9 10 11]]
```

可以将花哨的索引和简单的索引组合使用：

```python
In[10]: X[2, [2, 0, 1]]
Out[10]: array([10, 8, 9])
```

也可以将花哨的索引和切片组合使用：

```python
In[11]: X[1:, [2, 0, 1]]
Out[11]: array([[ 6, 4, 5],
                [10, 8, 9]])
```

更可以将花哨的索引和掩码组合使用：

```python
In[12]: mask = np.array([1, 0, 1, 0], dtype=bool)
        X[row[:, np.newaxis], mask]
Out[12]: array([[ 0, 2],
                [ 4, 6],
                [ 8, 10]])
```

#### 2.7.4 用花哨的索引修改值

花哨的索引也可以被用于修改部分数组。

```python
In[18]: x = np.arange(10)
        i = np.array([2, 1, 8, 4])
        x[i] = 99
        print(x)
[ 0 99 99 3 99 5 6 7 99 9]
```

操作中重复的索引会导致一些出乎意料的结果产生:

```python
In[20]: x = np.zeros(10)
        x[[0, 0]] = [4, 6]
        print(x)
[ 6. 0. 0. 0. 0. 0. 0. 0. 0. 0.]

In[21]: i = [2, 3, 3, 4, 4, 4]
        x[i] += 1
        x
Out[21]: array([ 6., 0., 1., 1., 1., 0., 0., 0., 0., 0.])
```

### 2.8 数组的排序

#### 2.8.1 NumPy中的快速排序： np.sort和np.argsort

np.sort 的排序算法是 快速排序， 其算法复杂度为[N log N]， 另外也可以选择归并排序和堆排序。
如果想在不修改原始输入数组的基础上返回一个排好序的数组， 可以使用 np.sort。
如果希望用排好序的数组替代原始数组， 可以使用数组的 sort 方法。
另外一个相关的函数是 argsort， 该函数返回的是原始数组排好序的索引值。
NumPy 排序算法的一个有用的功能是通过 axis 参数， 沿着多维数组的行或列进行排序。

#### 2.8.2 部分排序： 分隔

np.partition 函数的输入是数组和数字 K， 输出结果是一个新数组， 最左边是第 K 小的
值， 往右是任意顺序的其他值。
与排序类似， 也可以沿着多维数组任意的轴进行分隔。
正如 np.argsort 函数计算的是排序的索引值， 也有一个np.argpartition 函数计算的是分隔的索引值。

### 2.9 结构化数据： NumPy的结构化数组

通过指定复合数据类型， 可以构造一个结构化数组：

```python
In[4]:  # 使用复合数据结构的结构化数组
        data = np.zeros(4, dtype={'names':('name', 'age', 'weight'),'formats':('U10', 'i4', 'f8')})
        print(data.dtype)
[('name', '<U10'), ('age', '<i4'), ('weight', '<f8')]
```

 可以将列表数据放入数组中：

 ```python
In[5]:  data['name'] = name
        data['age'] = age
        data['weight'] = weight
        print(data)
[('Alice', 25, 55.0) ('Bob', 45, 85.5) ('Cathy', 37, 68.0)
('Doug', 19, 61.5)]
 ```

#### 2.9.1 生成结构化数组

结构化数组的数据类型有多种制定方式。

1. 采用字典的方法

    ```python
    In[10]: np.dtype({'names':('name', 'age', 'weight'),'formats':('U10', 'i4', 'f8')})
    Out[10]: dtype([('name', '<U10'), ('age', '<i4'), ('weight', '<f8')])
    ```

2. 数值数据类型可以用 Python 类型或 NumPy 的 dtype 类型指定

    ```python
    In[11]: np.dtype({'names':('name', 'age', 'weight'), 'formats':((np.str_, 10), int, np.float32)})
    Out[11]: dtype([('name', '<U10'), ('age', '<i8'), ('weight', '<f4')])
    ```

3. 复合类型也可以是元组列表

    ```python
    In[12]: np.dtype([('name', 'S10'), ('age', 'i4'), ('weight', 'f8')])
    Out[12]: dtype([('name', 'S10'), ('age', '<i4'), ('weight', '<f8')])
    ```

4. 可以仅仅用一个字符串来指定类型

    ```python
    In[13]: np.dtype('S10,i4,f8')
    Out[13]: dtype([('f0', 'S10'), ('f1', '<i4'), ('f2', '<f8')])
    ```

NumPy的数据类型

| 符号 | 描述 | 示例 |
| ----- | ----- | ----- |
| 'b' | 字节型 | np.dtype('b') |
| 'i' | 有符号整型 | np.dtype('i4') == np.int32 |
| 'u' | 无符号整型 | np.dtype('u1') == np.uint8 |
| 'f' | 浮点型 | np.dtype('f8') == np.int64 |
| 'c |' 复数浮点型 | np.dtype('c16') == np.complex128 |
| 'S'、 'a' | 字符串 | np.dtype('S5') |
| 'U' | Unicode 编码字符串 | np.dtype('U') == np.str_ |
| 'V' | 原生数据， raw data（空，void） | np.dtype('V') == np.void |

#### 2.9.2 更高级的复合类型

NumPy 中也可以定义更高级的复合数据类型。

```python
In[14]: tp = np.dtype([('id', 'i8'), ('mat', 'f8', (3, 3))])
        X = np.zeros(1, dtype=tp)
        print(X[0])
        print(X['mat'][0])
(0, [[0.0, 0.0, 0.0], [0.0, 0.0, 0.0], [0.0, 0.0, 0.0]])
[[ 0. 0. 0.]
 [ 0. 0. 0.]
 [ 0. 0. 0.]]
```

#### 2.9.3 记录数组： 结构化数组的扭转

NumPy 还提供了 np.recarray 类。 它和前面介绍的结构化数组几乎相同， 但是它有一个独特的特征： 域可以像属性一样获取， 而不是像字典的键那样获取。

```python
    In[15]: data['age']
    Out[15]: array([25, 45, 37, 19], dtype=int32)

    In[16]: data_rec = data.view(np.recarray)
            data_rec.age
    Out[16]: array([25, 45, 37, 19], dtype=int32)
```

记录数组获取域时会有一些额外的开销， 速度更慢。


