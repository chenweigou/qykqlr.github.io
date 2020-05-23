# Python 数据科学手册

**[返回](./index.md)**

## 第一章 IPython

### 1.1 shell还是Notebook

#### 1.1.1 启动IPython shell

在终端输入ipython启动IPython解释器

#### 1.1.2 启动Jupyter Notebook

```shell
$ jupyter notebook
[NotebookApp] Serving notebooks from local directory: /Users/jakevdp/...
[NotebookApp] 0 active kernels
[NotebookApp] The IPython Notebook is running at: http://localhost:8888/
[NotebookApp] Use Control-C to stop this server and shut down all kernels...
```

在终端输入 jupyter notebook启动
你的默认浏览器将会自动打开， 并且自动导航到localhost 网址
如果没有自动打开, 可以自己打开浏览器， 输入上面的网址 <http://localhost:8888/>

###　1.2 IPython的帮助和文档

#### 1.2.1 用符号?获取文档

使用help(cmd) 或 cmd? 来获得命令的详细描述

#### 1.2.2 通过符号??获取源代码

它有时不能显示源代码。 这是因为你查询的对象并不是用 Python 实现的

#### 1.2.3 用Tab补全的方式探索模块

1. 对象内容的Tab自动补全

2. 导入时的Tab自动补全

3. 超越Tab自动补全： 通配符匹配
    当你不确定对象或属性的第一个或前几个字符时, IPython 提供了用 * 符号来实现的通配符匹配方法

    ```python
    In [10]: str.*find*?
    str.find
    str.rfin
    ```

### 1.3 IPython shell中的快捷键

####　1.3.1 导航快捷键

| 快捷键  | 动作  |
|  ----  | ----  |
| Ctrl + a  | 将光标移到本行的开始处 |
| Ctrl + b(或左箭头键) | 将光标回退一个字符 |
| Ctrl + f (或右箭头键) | 将光标前进一个字符 |

#### 1.3.2 文本输入快捷键

| 快捷键 | 动作 |
|  ----  | ----  |
| Backspace 键 | 删除前一个字符 |
| Ctrl + d | 删除后一个字符 |
| Ctrl + k | 从光标开始剪切至行的末尾 |
| Ctrl + u | 从行的开头剪切至光标 |
| Ctrl + y | yank (即粘贴)之前剪切的文本 |
| Ctrl + t | transpose (即交换)前两个字符 |

#### 1.3.3 命令历史快捷键

| 快捷键 | 动作 |
|  ----  | ----  |
| Ctrl + p（或向上箭头） | 获取前一个历史命令 |
| Ctrl + n（或向下箭头） | 获取后一个历史命令 |
| Ctrl + r | 对历史命令的反向搜索 |

也可以用 Ctrl + p / Ctrl + n 或者上下方向键查找历史， 但是仅仅是匹配每行的前几个字符。 也就是说， 如果你输入 def 然后按下Ctrl + p， 则会在你的命令历史中找到以 def 开头的最近的命令(如果有的话)

### 1.4 IPython魔法命令

#### 1.4.1. 粘贴代码块： %paste和%cpaste

解决从其他地方复制代码产生的格式错误问题

```python
In [3]: %paste
>>> def donothing(x):
...     return x

## -- End pasted text --
```

```python
In [4]: donothing(10)
Out[4]: 10
```

#### 1.4.2. 执行外部代码： %run

直接运行py文件

```python
#-------------------------------------
# file: myscript.py
def square(x):
    """求平方"""
    return x ** 2
for N in range(1, 4):
    print(N, "squared is", square(N))
```

你可以在像下面这样在 IPython 会话中运行该程序

```python
In [6]: %run myscript.py
1 squared is 1
2 squared is 4
3 squared is 9
```

请注意， 当你运行了这段代码之后， 该代码中包含的所有函数都可以在IPython 会话中使用：

```python
In [7]: square(5)
Out[7]: 25
```

#### 1.4.3. 计算代码运行时间： %timeit

自动计算接下来一行的Python 语句的执行时间

```python
In [8]: %timeit L = [n ** 2 for n in range(1000)]
1000 loops, best of 3: 325 μs per loop
```

它会自动多次执行简短的命令， 以获得更稳定的结果
对于多行语句， 可以加入第二个 % 符号将其转变成单元魔法， 以处理多行输入

```python
In [9]: %%timeit
    ...: L = []
    ...: for n in range(1000):
    ...:    L.append(n ** 2)
    ...:
1000 loops, best of 3: 373 μs per loop
```

#### 1.4.4. 魔法函数的帮助： ?、 %magic和%lsmagic

为了读到 %timeit魔法函数的文档注释， 可以简单地输入以下命令：

```python
In [10]: %timeit?
```

为了获得可用魔法函数的通用描述以及一些示例， 可以输入以下命令：

```python
In [11]: %magic
```

为了快速而简单地获得所有可用魔法函数的列表， 可以输入以下命令：

```python
In [12]: %lsmagic
```

### 1.5 输入和输出历史

#### 1.5.1 IPython的输入和输出对象

IPython 实际上创建了叫作 In 和 Out 的 Python 变量， 这些变量自动更新以反映命令历史:

```python
In [4]: print(In)
['', 'import math', 'math.sin(2)', 'math.cos(2)', 'print(In)']
In [5]: Out
Out[5]: {2: 0.9092974268256817, 3: -0.4161468365471424}
```

In 对象是一个列表， 按照顺序记录所有的命令
Out 对象不是一个列表， 而是一个字典。 它将输入数字映射到相应的输出(如果有的话)

#### 1.5.2 下划线快捷键和以前的输出

标准的 Python shell仅仅包括一个用于获取以前的输出的简单快捷键。
使用变量 _（单下划线） 用于更新以前的输出
IPython可以用两条下划线获得倒数第二个历史输出， 用三条下划线获得倒数第三个历史输出（跳过任何没有输出的命令）

```python
In [9]: print(_)
1.0
In [10]: print(__)
-0.4161468365471424
In [11]: print(___)
0.9092974268256817
```

你还可以使用Out[X]的简写形式 _X

```python
In [12]: Out[2]
Out[12]: 0.9092974268256817
In [13]: _2
Out[13]: 0.9092974268256817
```

#### 1.5.3 禁止输出

可以通过在语句的末尾添加一个分号来禁止这条语句输出， 同时也不会存储到Out对象中

```python
In [14]: math.sin(2) + math.cos(2);

In [15]: 14 in Out
Out[15]: False
```

#### 1.5.4 相关的魔法命令
使用%history 魔法命令一次性获取此前所有的输入历史，使用 -n 参数限制想要获取的范围

```python
In [16]: %history -n 1-4
1: import math
2: math.sin(2)
3: math.cos(2)
4: print(In)
```

%rerun 重新执行部分历史命令
%save将部分历史命令保存到一个文件中
可以使用%cmd? 来获得详细描述

### 1.6 IPython和shell命令

IPython提供了在 IPython 终端直接执行 shell 命令的语法

#### 1.6.1 shell快速入门

echo类似于Python的打印函数
pwd=打印工作路径
ls=列出当前路径的内容
cd=改变路径
mkdir=创建新的文件夹
mv=移动文件
cp=复制文件

#### 1.6.2 IPython中的shell命令

可以通过将 ! 符号作为前缀在 IPython 中执行任何命令行命令

#### 1.6.3 在shell中传入或传出值

可以通过一个赋值操纵符将任何 shell 命令的输出保存到一个 Python 列表

```python
In [4]: contents = !ls
In [5]: print(contents)
['myproject.txt']
In [6]: directory = !pwd
In [7]: print(directory)
['/Users/jakevdp/notebooks/tmp/myproject']
```

这些结果并不以列表的形式返回， 而是以 IPython 中定义的一
个特殊 shell 返回类型的形式返回

```python
In [8]: type(directory)
IPython.utils.text.SList
```

将 Python 变量传入 shell， 可以通过 {varname}语法实现

```python
In [9]: message = "hello from Python"
In [10]: !echo {message}
hello from Python
```

### 1.7 与shell相关的魔法命令

你不能通过 `!cd` 来导航文件系统, 原因是 Notebook 中的 shell 命令是在一个临时的分支 shell 中执行的。

```python
In [11]: !pwd
/home/jake/projects/myproject
In [12]: !cd ..
In [13]: !pwd
/home/jake/projects/myproject
```

可以使用 `%cd` 魔法命令, 甚至可以不用 % 符号

```python
In [14]: %cd ..
/home/jake/projects
In [15]: cd myproject
/home/jake/projects/myproject
```

这种方式被称作自动魔法（automagic） 函数， 可以通过 %automagic魔法函数进行翻转。
除了 `%cd`， 其他可用的类似 shell 的魔法函数还有`%cat`、 `%cp`、 `%env`、 `%ls`、 `%man`、 `%mkdir`、 `%more`、 `%mv`、 `%pwd`、 `%rm` 和 `%rmdir`。 如果 automagic 被打开， 以上任何一个魔法命令都可以省略 % 符号， 这使得你可以将 IPython 提示符当作普通 shell 一样使用

### 1.8 错误和调试

#### 1.8.1 控制异常： %xmode

利用 `%xmode` 魔法函数， IPython 允许你在异常发生时控制打印信息的数量

`%xmode` 有一个输入参数， 即模式。 模式有 3 个可选项： Plain、 Context 和 Verbose。 默认情况下是 Context， 该模式的输出结果我们已经见过。 Plain 更紧凑， 给出的信息更少:

```python
In[3]: %xmode Plain
Exception reporting mode: Plain
In[4]: func2(1)
------------------------------------------------------------
Traceback (most recent call last):
File "<ipython-input-4-b2e110f6fc8f>", line 1, in <module>
func2(1)
File "<ipython-input-1-d849e34d61fb>", line 7, in func2
return func1(a, b)
File "<ipython-input-1-d849e34d61fb>", line 2, in func1
return a / b
ZeroDivisionError: division by zero
```

Verbose 模式加入了一些额外的信息， 包括任何被调用的函数的参数：

```python
In[5]: %xmode Verbose
Exception reporting mode: Verbose
In[6]: func2(1)
---------------------------------------------------------------------------
ZeroDivisionError Traceback (most recent call last)
<ipython-input-6-b2e110f6fc8f> in <module>()
----> 1 func2(1)
global func2 = <function func2 at 0x103729320>
<ipython-input-1-d849e34d61fb> in func2(x=1)
5 a = x
6 b = x - 1
----> 7 return func1(a, b)
global func1 = <function func1 at 0x1037294d0>
a = 1
b = 0
<ipython-input-1-d849e34d61fb> in func1(a=1, b=0)
1 def func1(a, b):
----> 2 return a / b
a = 1
b = 0
34
def func2(x):
5 a = x
ZeroDivisionError: division by zero
```

#### 1.8.2 调试： 当阅读轨迹追溯不足以解决问题时

IPython增强版本的调试器是 ipdb， 它是 IPython 专用的调试器。
部分调试命令

| 命令 | 描述
|  ----  | ---- |
|　list | 显示文件的当前路径 |
| h(elp) | 显示命令列表， 或查找特定命令的帮助信息 |
| q(uit) | 退出调试器和程序 |
| c(ontinue) | 退出调试器， 继续运行程序 |
| n(ext) | 跳到程序的下一步 |
| &lt;enter&gt; | 重复前一个命令 |
| p(rint) | 打印变量s(tep) 步入子进程 |
| r(eturn) | 从子进程跳出 |

### 1.9 代码的分析和计时

#### 1.9.1 代码段计时： %timeit和%time

`%time` 对单个语句的执行时间进行计时。
`%timeit` 对单个语句的重复执行进行计时， 以获得更高的准确度

`%%timeit` 可以让代码段重复运行来计算代码的运行时间， `%%timeit` 可以让代码段重复运行来计算代码的运行时间

有时候重复一个操作并不是最佳选择。 例如， 如果有一个列表需要排序， 我们可能会被重复操作误导。 对一个预先排好序的列表进行排序，比对一个无序的列表进行排序要快， 所以重复运行将使结果出现偏差, 对于这种情况， %time 魔法函数可能是更好的选择。

#### 1.9.2 分析整个脚本： %prun

`%prun` 利用分析器运行代码。

```python
In[7]:  def sum_of_lists(N):
            total = 0
            for i in range(5):
                L = [j ^ (j >> i) for j in range(N)]
                total += sum(L)
            return total
In[8]: %prun sum_of_lists(1000000)
14 function calls in 0.714 seconds
    Ordered by: internal time
    ncalls tottime percall cumtime percall filename:lineno(function)
        5 0.599 0.120 0.599 0.120 <ipython-input-19>:4(<listcomp>)
        5 0.064 0.013 0.064 0.013 {built-in method sum}
        1 0.036 0.036 0.699 0.699 <ipython-input-19>:1(sum_of_lists)
        1 0.014 0.014 0.714 0.714 <string>:1(<module>)
        1 0.000 0.000 0.714 0.714 {built-in method exec}
```

结果是一个表格， 该表格按照每个函数调用的总时间， 显示了哪里的执行时间最长。

#### 1.9.3 用%lprun进行逐行分析

`%lprun` 利用逐行分析器运行代码。
用`pip`安装`line_profiler`包, 用 `%load_ext line_profiler`导入

```python
In[10]: %lprun -f sum_of_lists sum_of_lists(5000)
Timer unit: 1e-06 s
Total time: 0.009382 s
File: <ipython-input-19-fa2be176cc3e>
Function: sum_of_lists at line 1
Line #     Hits   Time   Per Hit    %Time   Line Contents
==============================================================
    1                                       def sum_of_lists(N):
    2       1       2      2.0       0.0        total = 0
    3       6       8      1.3       0.1        for i in range(5):
    4       5    9001   1800.2      95.9            L = [j ^ (j >> i) ...5 5 371 74.2 4.0 total += sum(L)
    5       5     371     74.2       4.0            total += sum(L)
    6       1       0      0.0       0.0        return total
```

#### 1.9.4 用%memit和%mprun进行内存分析

`%memit` 测量单个语句的内存使用。
`%mprun` 通过逐行的内存分析器运行代码。
用`pip`安装`memory_profiler`, 用`%load_ext memory_profiler`导入

`%memit` 函数用起来很简单

```python
In[13]: %memit sum_of_lists(1000000)
peak memory: 100.08 MiB, increment: 61.36 MiB
```

对于逐行代码的内存消耗描述， 可以用 `%mprun` 魔法函数。 但这个魔法函数仅仅对独立模块内部的函数有效， 而对于 Notebook本身不起作用。 所以首先用 %%file 魔法函数创建一个简单的模块， 将该模块命名为 mprun_demo.py。 它包含 sum_of_lists 函数， 该函数中包含一次加法， 能使内存分析结果更清晰：

```python
In[14]: %%file mprun_demo.py
        def sum_of_lists(N):
            total = 0
            for i in range(5):
                L = [j ^ (j >> i) for j in range(N)]
                total += sum(L)
                del L # remove reference to L
            return total
Overwriting mprun_demo.py
In[15]: from mprun_demo import sum_of_lists
        %mprun -f sum_of_lists sum_of_lists(1000000)

Filename: ./mprun_demo.py
Line #   Mem usage     Increment    Line Contents
================================================
     4   71.9 MiB        0.0 MiB           L = [j ^ (j >> i) for j in range(N)]

Filename: ./mprun_demo.py
Line #   Mem usage     Increment    Line Contents
================================================
    1    39.0 MiB        0.0 MiB    def sum_of_lists(N):
    2    39.0 MiB        0.0 MiB        total = 0
    3    46.5 MiB        7.5 MiB        for i in range(5):
    4    71.9 MiB       25.4 MiB            L = [j ^ (j >> i) for j in range(N)]
    5    71.9 MiB        0.0 MiB            total += sum(L)
    6    46.5 MiB      -25.4 MiB            del L # remove reference to L
    7    39.1 MiB       -7.4 MiB        return total
```

Increment 列告诉我们每行代码对总内存预算的影响

<!-- 
```python

```
-->
