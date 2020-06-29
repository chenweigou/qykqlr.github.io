# Pandas组队学习

[返回](./index.md)
[toc]

## 第9章 时序数据

### 五、问题与练习

#### 1. 问题

【问题一】 如何对date_range进行批量加帧操作或对某一时间段加大时间戳密度？

【问题二】 如何批量增加TimeStamp的精度？

【问题三】 对于超出处理时间的时间点，是否真的完全没有处理方法？

可以缩小时间的精度来把可处理时间范围扩大

【问题四】 给定一组非连续的日期，怎么快速找出位于其最大日期和最小日期之间，且没有出现在该组日期中的日期？

使用 pd.bdate_range 方法

#### 2. 练习

练习一】 现有一份关于某超市牛奶销售额的时间序列数据，请完成下列问题：

```python
In [1]: df = pd.read_csv('data/time_series_one.csv', parse_dates=['日期'])
        df.head()
Out[1]:          日期   销售额
        0 2017-02-17  2154
        1 2017-02-18  2095
        2 2017-02-19  3459
        3 2017-02-20  2198
        4 2017-02-21  2413
```

（a）销售额出现最大值的是星期几？（提示：利用dayofweek函数）

```python
In [2]: df['日期'].dt.dayofweek[df['销售额'].idxmax()]
Out[2]: 6
```

（b）计算除去春节、国庆、五一节假日的月度销售总额

```python
In [3]: holiday = pd.date_range(start='20170501', end='20170503').append(
                   pd.date_range(start='20171001', end='20171007')).append(
                   pd.date_range(start='20180215', end='20180221')).append(
                   pd.date_range(start='20180501', end='20180503')).append(
                   pd.date_range(start='20181001', end='20181007')).append(
                   pd.date_range(start='20190204', end='20190224')).append(
                   pd.date_range(start='20190501', end='20190503')).append(
                   pd.date_range(start='20191001', end='20191007'))
        result = df[~df['日期'].isin(holiday)].set_index('日期').resample('MS').sum()
        result.head()
Out[3]:              销售额
        日期
        2017-02-01  31740
        2017-03-01  80000
        2017-04-01  74734
        2017-05-01  76237
        2017-06-01  80750
```

（c）按季度计算周末（周六和周日）的销量总额

```python
In [4]: result = df[df['日期'].dt.dayofweek.isin([5,6])].set_index('日期').resample('QS').sum()
        result.head()
Out[4]:
              销售额
        日期
        2017-01-01  32894
        2017-04-01  66692
        2017-07-01  69099
        2017-10-01  70384
        2018-01-01  74671
```

（d）从最后一天开始算起，跳过周六和周一，以5天为一个时间单位向前计算销售总和

```python
In [5]: df_temp = df[~df['日期'].dt.dayofweek.isin([5,6])].set_index('日期').iloc[::-1]
        L_temp,date_temp = [],[0]*df_temp.shape[0]
        for i in range(df_temp.shape[0]//5):
            L_temp.extend([i]*5)
        L_temp.extend([df_temp.shape[0]//5]*(df_temp.shape[0]-df_temp.shape[0]//5*5))
        date_temp = pd.Series([i%5==0 for i in range(df_temp.shape[0])])
        df_temp['num'] = L_temp
        result = pd.DataFrame({'5天总额':df_temp.groupby('num')['销售额'].sum().values},
                               index=df_temp.reset_index()[date_temp]['日期']).iloc[::-1]
        result.head()
Out[5]:             5天总额
        日期
        2017-02-22   9855
        2017-03-01  12296
        2017-03-08  13323
        2017-03-15  13845
        2017-03-22  11356
```

（e）假设现在发现数据有误，所有同一周里的周一与周五的销售额记录颠倒了，请计算2018年中每月第一个周一的销售额（如果该周没有周一或周五的记录就保持不动）

```python
In [6]: from datetime import datetime
        df_temp = df.copy()
        df_fri = df.shift(4)[df.shift(4)['日期'].dt.dayofweek==1]['销售额']
        df_mon = df.shift(-4)[df.shift(-4)['日期'].dt.dayofweek==5]['销售额']
        df_temp.loc[df_fri.index,['销售额']] = df_fri
        df_temp.loc[df_mon.index,['销售额']] = df_mon
        df_temp.loc[df_temp[df_temp['日期'].dt.year==2018]['日期'][
                df_temp[df_temp['日期'].dt.year==2018]['日期'].apply(
                lambda x:True if datetime.strptime(str(x).split()[0],'%Y-%m-%d').weekday() == 0
                and 1 <= datetime.strptime(str(x).split()[0],'%Y-%m-%d').day <= 7 else False)].index,:]
Out[6]:           日期     销售额
        318 2018-01-01  2863.0
        353 2018-02-05  2321.0
        381 2018-03-05  2705.0
        409 2018-04-02  2487.0
        444 2018-05-07  3204.0
        472 2018-06-04  2927.0
        500 2018-07-02  2574.0
        535 2018-08-06  2504.0
        563 2018-09-03  2483.0
        591 2018-10-01  2431.0
        626 2018-11-05  2395.0
        654 2018-12-03  2373.0
```

【练习二】 继续使用上一题的数据，请完成下列问题：

（a）以50天为窗口计算滑窗均值和滑窗最大值（min_periods设为1）

```python
In [7]: df['销售额'].rolling(window=50,min_periods=1).mean().head()
Out[7]: 日期
        2017-02-17    2154.000000
        2017-02-18    2124.500000
        2017-02-19    2569.333333
        2017-02-20    2476.500000
        2017-02-21    2463.800000
        Name: 销售额, dtype: float64

In [8]: df['销售额'].rolling(window=50,min_periods=1).max().head()
Out[8]: 日期
        2017-02-17    2154.0
        2017-02-18    2154.0
        2017-02-19    3459.0
        2017-02-20    3459.0
        2017-02-21    3459.0
        Name: 销售额, dtype: float64
```

（b）现在有如下规则：若当天销售额超过向前5天的均值，则记为1，否则记为0，请给出2018年相应的计算结果

```python
In [9]: def f(x):
            if len(x) == 6:
            return 1 if x[-1]>np.mean(x[:-1]) else 0
            else:
                return 0
        result_b = df.loc[pd.date_range(start='20171227',end='20181231'),:].rolling(
                                                            window=6,min_periods=1).agg(f)[5:].head()
        result_b.head()
Out[9]:            销售额
        2018-01-01  1.0
        2018-01-02  0.0
        2018-01-03  0.0
        2018-01-04  0.0
        2018-01-05  0.0
```

（c）将(c)中的“向前5天”改为“向前非周末5天”，请再次计算结果

```python

In [10]: def f(x):
             if len(x) == 8:
                 return 1 if x[-1]>np.mean(x[:-1][pd.Series([
                     False if i in [5,6] else True for i in x[:-1].index.dayofweek],index=x[:-1].index)]) else 0
             else:
                 return 0
         result_c = df.loc[pd.date_range(start='20171225',end='20181231'),:].rolling(
                                             window=8,min_periods=1).agg(f)[7:].head()
         result_c.head()
Out[10]:            销售额
         2018-01-01  1.0
         2018-01-02  0.0
         2018-01-03  0.0
         2018-01-04  0.0
         2018-01-05  0.0
```