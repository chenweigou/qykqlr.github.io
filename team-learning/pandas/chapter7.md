# Pandas组队学习

[返回](./index.md)
[toc]

## 第7章 文本数据

```python
In[1]:  import pandas as pd
        import numpy as np
```

### 一、string类型的性质

#### 1. string与object的区别

string类型和object不同之处有三：
① 字符存取方法（string accessor methods，如str.count）会返回相应数据的Nullable类型，而object会随缺失值的存在而改变返回类型
② 某些Series方法不能在string上使用，例如： Series.str.decode()，因为存储的是字符串而不是字节
③ string类型在缺失值存储或运算时，类型会广播为pd.NA，而不是浮点型np.nan
其余全部内容在当前版本下完全一致，但迎合Pandas的发展模式，我们仍然全部用string来操作字符串

#### 2. string类型的转换
如果将一个其他类型的容器直接转换string类型可能会出错, 正确的方法是分两部转换，先转为str型object，在转为string类型：

```python
In[2]: pd.Series([1,'1.']).astype('str').astype('string')
Out[2]: 0    1
        1    1.
        dtype: string

In[3]: pd.Series([1,2]).astype('str').astype('string')
Out[3]: 0    1
        1    2
        dtype: string

In[4]: pd.Series([True,False]).astype('str').astype('string')
Out[4]: 0     True
        1    False
        dtype: string
```

### 二、拆分与拼接

#### 1. str.split方法

（a）分割符与str的位置元素选取
根据某一个元素分割，默认为空格

```python
In[5]: pd.Series(['a_b_c', 'c_d_e', np.nan, 'f_g_h'], dtype="string").str.split('_')
Out[5]: 0       b
        1       d
        2    <NA>
        3       g
        dtype: object
```

这里需要注意split后的类型是object，因为现在Series中的元素已经不是string，而包含了list，且string类型只能含有字符串
对于str方法可以进行元素的选择，如果该单元格元素是列表，那么str[i]表示取出第i个元素，如果是单个元素，则先把元素转为列表在取出

```python
In[6]:  pd.Series(['a_b_c', ['a','b','c']], dtype="object").str[1]
        #第一个元素先转为['a','_','b','_','c']
Out[6]: 0    _
        1    b
        dtype: object
```

（b）其他参数
expand参数控制了是否将列拆开，n参数代表最多分割多少次

```python
In[7]: pd.Series(['a_b_c', 'c_d_e', np.nan, 'f_g_h'], dtype="string").str.split('_',expand=True,n=1)
Out[7]: 0     1
        0     a   b_c
        1     c   d_e
        2  <NA>  <NA>
        3     f   g_h
```

#### 2. str.cat方法

（a）不同对象的拼接模式
cat方法对于不同对象的作用结果并不相同，其中的对象包括：单列、双列、多列
① 对于单个Series而言，就是指所有的元素进行字符合并为一个字符串
其中可选sep分隔符参数，和缺失值替代字符na_rep参数

② 对于两个Series合并而言，是对应索引的元素进行合并
同样也有相应的sep分隔符参数，和缺失值替代字符na_re参数，需要注意的是两个缺失值会被同时替换

③ 多列拼接可以分为表的拼接和多Series拼接

```python
In[8]: # 表的拼接
        s.str.cat(pd.DataFrame({0:['1','3','5'],1:['5','b',None]}, dtype='string'),na_rep='*')
Out[8]: 0    ab15
        1     *3b
        2     d5*
        dtype: string

In[9]: # 多个Series拼接
        s.str.cat([s+'0',s*2])
Out[9]: 0    abab0abab
        1         <NA>
        2        dd0dd
        dtype: string
```

（b）cat中的索引对齐
当前版本中，如果两边合并的索引不相同且未指定join参数，默认为左连接，设置join='left'

### 三、替换

广义上的替换，就是指str.replace函数的应用，fillna是针对缺失值的替换，上一章已经提及
提到替换，就不可避免地接触到正则表达式，这里默认读者已掌握常见正则表达式知识点，若对其还不了解的，可以通过[这份资料](https://regexone.com/)来熟悉

#### 1. str.replace的常见用法

第一个值写r开头的正则表达式，后一个写替换的字符串
```python
In[10]: s = pd.Series(['A', 'B', 'C', 'Aaba', 'Baca','', np.nan, 'CABA', 'dog', 'cat'],dtype="string")

In[11]: s.str.replace(r'^[AB]','***')
Out[11]:0       ***
        1       ***
        2         C
        3    ***aba
        4    ***aca
        5
        6      <NA>
        7      CABA
        8       dog
        9       cat
        dtype: string
```

#### 2. 子组与函数替换

```python
In[12]: # 通过正整数调用子组（0返回字符本身，从1开始才是子组）
        s.str.replace(r'([ABC])(\w+)',lambda x:x.group(2)[1:]+'*')
Out[12]:0       A
        1       B
        2       C
        3     ba*
        4     ca*
        5        
        6    <NA>
        7     BA*
        8     dog
        9     cat
        dtype: string

In[13]: # 利用?P<....>表达式可以对子组命名调用
        s.str.replace(r'(?P<one>[ABC])(?P<two>\w+)',lambda x:x.group('two')[1:]+'*')
Out[13]:0       A
        1       B
        2       C
        3     ba*
        4     ca*
        5        
        6    <NA>
        7     BA*
        8     dog
        9     cat
        dtype: string
```

#### 3. 关于str.replace的注意事项

首先，要明确str.replace和replace并不是一个东西：
str.replace针对的是object类型或string类型，默认是以正则表达式为操作，目前暂时不支持DataFrame上使用
replace针对的是任意类型的序列或数据框，如果要以正则表达式替换，需要设置regex=True，该方法通过字典可支持多列替换
但现在由于string类型的初步引入，用法上出现了一些问题，这些issue有望在以后的版本中修复
（a）str.replace赋值参数不得为pd.NA
这听上去非常不合理，例如对满足某些正则条件的字符串替换为缺失值，直接更改为缺失值在当下版本就会报错, 此时，可以先转为object类型再转换回来，曲线救国
（b）对于string类型Series，在使用replace函数时不能使用正则表达式替换
该bug现在还未修复
（c）string类型序列如果存在缺失值，不能使用replace替换

### 四、子串匹配与提取

####　1. str.extract方法

（a）常见用法

```python
In[14]: pd.Series(['10-87', '10-88', '10-89'],dtype="string").str.extract(r'([\d]{2})-([\d]{2})')
Out[14]:    0   1
        0   10  87
        1   10  88
        2   10  89

In[15]: # 使用子组名作为列名
        pd.Series(['10-87', '10-88', '-89'],dtype="string").str.extract(r'(?P<name_1>[\d]{2})-(?P<name_2>[\d]{2})')
Out[15]:  name_1 name_2
        0     10     87
        1     10     88
        2   <NA>   <NA>

In[16]: # 利用?正则标记选择部分提取
        pd.Series(['10-87', '10-88', '-89'],dtype="string").str.extract(r'(?P<name_1>[\d]{2})?-(?P<name_2>[\d]{2})')
Out[16]:  name_1 name_2
        0     10     87
        1     10     88
        2   <NA>     89

```

b）expand参数（默认为True）
对于一个子组的Series，如果expand设置为False，则返回Series，若大于一个子组，则expand参数无效，全部返回DataFrame
对于一个子组的Index，如果expand设置为False，则返回提取后的Index，若大于一个子组且expand为False，报错

#### 2. str.extractall方法

与extract只匹配第一个符合条件的表达式不同，extractall会找出所有符合条件的字符串，并建立多级索引（即使只找到一个）

#### 3. str.contains和str.match

前者的作用为检测是否包含某种正则模式, 可选参数为na，设置当遇到na时返回值是什么， 默认为na
str.match与其区别在于，match依赖于python的re.match，检测内容为是否从头开始包含该正则模式

### 五、常用字符串方法

#### 1. 过滤型方法

（a）str.strip: 常用于过滤空格
（b）str.lower和str.upper: 将字母全部转为大写和小写
（c）str.swapcase和str.capitalize: 分别表示交换字母大小写和大写首字母

#### 2. isnumeric方法

检查每一位是否都是数字

### 六、问题与练习

#### 1. 问题

【问题一】 str对象方法和df/Series对象方法有什么区别？
① 字符存取方法（string accessor methods，如str.count）会返回相应数据的Nullable类型，而object会随缺失值的存在而改变返回类型
② 某些Series方法不能在string上使用，例如： Series.str.decode()，因为存储的是字符串而不是字节
③ string类型在缺失值存储或运算时，类型会广播为pd.NA，而不是浮点型np.nan
其余全部内容在当前版本下完全一致，但迎合Pandas的发展模式，我们仍然全部用string来操作字符串

【问题二】 给出一列string类型，如何判断单元格是否是数值型数据？
`str.match(r'^[-+]?[0-9]*\.?[0-9]+$')` 
不过这个会匹配首尾有额外的零的情况， 以及无法匹配首尾是`.`的情况

【问题三】 rsplit方法的作用是什么？它在什么场合下适用？
从后往前拆分字符串， 当需要拆分后面的指定数目的字符串时可以适用

【问题四】 在本章的第二到第四节分别介绍了字符串类型的5类操作，请思考它们各自应用于什么场景？
拆分:
拼接：
替换：
匹配：
提取：

2. 练习
【练习一】 现有一份关于字符串的数据集，请解决以下问题：

```python
In[17]: df = pd.read_csv('data/String_data_one.csv',index_col='人员编号').astype('str').convert_dtypes()
        df.head()
Out[17]:        姓名    国籍    性别   出生年    出生月 出生日
        人员编号
        1      aesfd    2       男      1942    8       10
        2     fasefa    5       女      1985    10      4
        3      aeagd    4       女      1946    10      15
        4        aef    4       男      1999    5       13
        5        eaf    1       女      2010    6       24
```

（a）现对字符串编码存储人员信息（在编号后添加ID列），使用如下格式：“×××（名字）：×国人，性别×，生于×年×月×日”

```python
In[18]:df['ID'] = (df['姓名']+':'+df['国籍']+'国人，性别'
                +df['性别']+'，生于'
                +df['出生年']+'年'
                +df['出生月']+'月'+df['出生日']+'日')
        df.head()
Out[18]:        姓名 国籍 性别   出生年 出生月 出生日                           ID
        人员编号
        1      aesfd  2  男     1942   8    10      aesfd:2国人，性别男，生于1942年8月10日
        2     fasefa  5  女     1985  10    4       fasefa:5国人，性别女，生于1985年10月4日
        3      aeagd  4  女     1946  10    15      aeagd:4国人，性别女，生于1946年10月15日
        4        aef  4  男     1999   5    13      aef:4国人，性别男，生于1999年5月13日
        5        eaf  1  女     2010   6    24      eaf:1国人，性别女，生于2010年6月24日
```


（b）将（a）中的人员生日信息部分修改为用中文表示（如一九七四年十月二十三日），其余返回格式不变。

```python
In[19]: L_year = list('零一二三四五六七八九')
L_one = [s.strip() for s in list('  二三四五六七八九')]
L_two = [s.strip() for s in list(' 一二三四五六七八九')]
df['ID'] = (df['姓名']+':'+df['国籍']+'国人，性别'+df['性别']+'，生于'
          +df['出生年'].str.replace(r'\d',lambda x:L_year[int(x.group(0))])+'年'
          +df['出生月'].apply(lambda x:x if len(x)==2 else '0'+x)\
                      .str.replace(r'(?P<one>[\d])(?P<two>\d?)',lambda x:L_one[int(x.group('one'))]
                      +bool(int(x.group('one')))*'十'+L_two[int(x.group('two'))])+'月'
          +df['出生日'].apply(lambda x:x if len(x)==2 else '0'+x)\
                      .str.replace(r'(?P<one>[\d])(?P<two>\d?)',lambda x:L_one[int(x.group('one'))]
                      +bool(int(x.group('one')))*'十'+L_two[int(x.group('two'))])+'日')
df.head()
Out[19]:         姓名 国籍 性别   出生年 出生月 出生日                           ID
        人员编号
        1      aesfd  2  男     1942   8    10      aesfd:2国人，性别男，生于一九四二年八月十日
        2     fasefa  5  女     1985  10    4       fasefa:5国人，性别女，生于一九八五年十月四日
        3      aeagd  4  女     1946  10    15      aeagd:4国人，性别女，生于一九四六年十月十五日
        4        aef  4  男     1999   5    13      aef:4国人，性别男，生于一九九九年五月十三日
        5        eaf  1  女     2010   6    24      eaf:1国人，性别女，生于二零一零年六月二十四日
```
**学到一个新技能： `True*'abc'`返回`'abc'`, `False*abc`返回空字符串**

（c）将（b）中的ID列结果拆分为原列表相应的5列，并使用equals检验是否一致。

```python
In[20]: dic_year = {i[0]:i[1] for i in zip(list('零一二三四五六七八九'),list('0123456789'))}
        dic_two = {i[0]:i[1] for i in zip(list('十一二三四五六七八九'),list('0123456789'))}
        dic_one = {'十':'1','二十':'2','三十':'3',None:''}
        df_res = df_new['ID'].str.extract(r'(?P<姓名>[a-zA-Z]+):(?P<国籍>[\d])国人，性别(?P<性别>[\w])，生于(?P<出生年>[\w]{4})年(?P<出生月>[\w]+)月(?P<出生日>[\w]+)日')
        df_res['出生年'] = df_res['出生年'].str.replace(r'(\w)+',lambda x:''.join([dic_year[x.group(0)[i]] for i in range(4)]))
        df_res['出生月'] = df_res['出生月'].str.replace(r'(?P<one>\w?十)?(?P<two>[\w])',lambda x:dic_one[x.group('one')]+dic_two[x.group('two')]).str.replace(r'0','10')
        df_res['出生日'] = df_res['出生日'].str.replace(r'(?P<one>\w?十)?(?P<two>[\w])',lambda x:dic_one[x.group('one')]+dic_two[x.group('two')]).str.replace(r'^0','10')
        df_res.head()
Out[20]:         姓名    国籍    性别   出生年    出生月 出生日
        人员编号
        1      aesfd    2       男      1942    8       10
        2     fasefa    5       女      1985    10      4
        3      aeagd    4       女      1946    10      15
        4        aef    4       男      1999    5       13
        5        eaf    1       女      2010    6       24

In[21]: df_res.equals(df.drop('ID', axis=1))
Out[21]: True
```

【练习二】 现有一份半虚拟的数据集，第一列包含了新型冠状病毒的一些新闻标题，请解决以下问题：

```python
In[21]: df = pd.read_csv('data/String_data_two.csv')
        df.head()
Out[21]:                col1               col2     col3
        0   鄂尔多斯市第2例确诊患者治愈出院   19   363.6923
        1     云南新增2例，累计124例         -67   -152.281
        2   武汉协和医院14名感染医护出院     -86   325.6221
        3     山东新增9例，累计307例         -74  -204.9313
        4        上海开学日期延至3月         -95       4.05
```

（a）选出所有关于北京市和上海市新闻标题的所在行。

```python
In[22]: df[df['col1'].str.contains(r'[北京]{2}|[上海]{2}')].head()
Out[22]:                        col1          col2      col3
        4             上海开学日期延至3月       -95       4.05
        5   北京新增25例确诊病例，累计确诊253例   -4  -289.1719
        6         上海新增10例，累计243例         2   -73.7105
        36         上海新增14例累计233例        -55        -83
        40         上海新增14例累计233例        -88        -99
```

（b）求col2的均值。

```python
In[23]: df['col2'][~(df['col2'].str.replace(r'-?\d+','True')=='True')] #这三行有问题
Out[23]: 309     0-
         396     9`
         485    /7
        Name: col2, dtype: object

In[24]: df.loc[[309,396,485],'col2'] = [0,9,7]
        df['col2'].astype('int').mean()
Out[24]: -0.984
```

（c）求col3的均值。

```python
In[23]: df.columns
Out[23]: Index(['col1', 'col2', 'col3  '], dtype='object')

In[24]: df.columns = df.columns.str.strip()
        df.columns
Out[24]: Index(['col1', 'col2', 'col3'], dtype='object')

In[25]: df['col3'].head()
Out[25]: 0     363.6923
         1     -152.281
         2     325.6221
         3    -204.9313
         4         4.05
         Name: col3, dtype: object

In[26]: df['col3'][~(df['col3'].str.replace(r'-?\d+\.?\d+','True')=='True')]
Out[26]: 28      355`.3567
         37             -5
         73              1
         122    9056.\2253
         332    3534.6554{
         370             7
         Name: col3, dtype: object

In[27]: df.loc[[28,122,332],'col3'] = [355.3567,9056.2253, 3534.6554]
        df['col3'].astype('float').mean()
Out[27]: 24.707484999999988
```