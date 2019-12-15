



# Kaggle 学习笔记

[TOC]



## Pandas使用笔记

### describe()

- [参考链接](https://blog.csdn.net/xckkcxxck/article/details/84799220)

- 参数：

![](https://img-blog.csdnimg.cn/20181204213449577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hja2tjeHhjaw==,size_16,color_FFFFFF,t_70)



- 默认显示5行



### .DataFrame()

- [参考链接](https://www.cnblogs.com/IvyWong/p/9203981.html)

- Python中Pandas库中的一种数据结构，它类似excel，是一种**二维表**

```python
  df1=pd.DataFrame(np.random.randn(4,4),index=list('ABCD'),columns=list('ABCD'))
  
  dic1={'name':['小明','小红','狗蛋','铁柱'],'age':[17,20,5,40],'gender':['男','女','女','男']}
  df3=pd.DataFrame(dic1)
```

  - ![img](https://rivers19-1300325434.cos.ap-beijing.myqcloud.com/2019-09-26-095746.png)
  - ![img](https://rivers19-1300325434.cos.ap-beijing.myqcloud.com/2019-09-26-095732.png)

#### DataFrame.dtypes

Return the dtypes in the DataFrame.

This returns a Series with the data type of each column. The result’s index is the original DataFrame’s columns. Columns with mixed types are stored with the `object` dtype. See [the User Guide](https://pandas.pydata.org/pandas-docs/stable/getting_started/basics.html#basics-dtypes) for more.

| Returns: | pandas.SeriesThe data type of each column. |
| :------- | ------------------------------------------ |
|          |                                            |

See also

- [`DataFrame.ftypes`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.ftypes.html#pandas.DataFrame.ftypes)

  Dtype and sparsity information.

Examples

```python
>>> df = pd.DataFrame({'float': [1.0],
...                    'int': [1],
...                    'datetime': [pd.Timestamp('20180310')],
...                    'string': ['foo']})
>>> df.dtypes
float              float64
int                  int64
datetime    datetime64[ns]
string              object
dtype: object
```

#### DataFrames.apply()

```python
>>> df = pd.DataFrame([[4, 9],] * 3, columns=['A', 'B'])
>>> df
   A  B
0  4  9
1  4  9
2  4  9
```

```python
>>> df = pd.DataFrame([[4, 9],] * 3, columns=['A', 'B'])
>>> df
   A  B
0  4  9
1  4  9
2  4  9

>>> df.apply(np.sum, axis=0)
A    12
B    27
dtype: int64
  
>>> df.apply(np.sum, axis=1)
0    13
1    13
2    13
dtype: int64
```

```python
## Retuning a list-like will result in a Series
>>> df.apply(lambda x: [1, 2], axis=1)
0    [1, 2]
1    [1, 2]
2    [1, 2]
dtype: object
```

#### .dropna

```python
>>> df = pd.DataFrame({"name": ['Alfred', 'Batman', 'Catwoman'],
...                    "toy": [np.nan, 'Batmobile', 'Bullwhip'],
...                    "born": [pd.NaT, pd.Timestamp("1940-04-25"),
...                             pd.NaT]})
>>> df
       name        toy       born
0    Alfred        NaN        NaT
1    Batman  Batmobile 1940-04-25
2  Catwoman   Bullwhip        NaT

# Drop the rows where at least one element is missing.
>>> df.dropna()
     name        toy       born
1  Batman  Batmobile 1940-04-25

# Drop the columns where at least one element is missing.
>>> df.dropna(axis='columns')
       name
0    Alfred
1    Batman
2  Catwoman

# Drop the rows where all elements are missing
>>> df.dropna(how='all')
       name        toy       born
0    Alfred        NaN        NaT
1    Batman  Batmobile 1940-04-25
2  Catwoman   Bullwhip        NaT

# Keep only the rows with at least 2 non-NA values.
>>> df.dropna(thresh=2)
       name        toy       born
1    Batman  Batmobile 1940-04-25
2  Catwoman   Bullwhip        NaT

# Define in which columns to look for missing values.
>>> df.dropna(subset=['name', 'born'])
       name        toy       born
1    Batman  Batmobile 1940-04-25

# Keep the DataFrame with valid entries in the same variable.
>>> df.dropna(inplace=True)
>>> df
     name        toy       born
1  Batman  Batmobile 1940-04-25
```

### .concat()

- [参考链接](https://www.jianshu.com/p/ab419cf9a4e5)





### cut()与qcut()

- cut()和qcut()的主要作用都是对若干连续变量进行分箱操作。但有所不同的是， cut()是按变量的值进行划分， 而qcut()是按照变量的个数进行划分。

    ```python
    # cut：按照数据值由小到大的顺序将数据分成4份， 并且使每组值的范围大致相等。
    d_cut = d.copy()
    d_cut['cut_group'] =pd.cut(d_cut['number'], 4)
    d_cut
    
    # 查看每个分组里变量的个数
    d_cut['cut_group'].value_counts()
    ```

    ```python
    # 把变量由小到大分成四组，并且让每组变量的数量相同
    d_qcut = d.copy()
    d_qcut['qcut_group'] = pd.qcut(d_qcut['number'], 4)
    d_qcut
    
    # 查看每个分组里变量的个数
    d_qcut['qcut_group'].value_counts()
    ```

- 参考
    - pandas的cut，qcut函数的使用和区别 - 晓伟的文章 - 知乎 https://zhuanlan.zhihu.com/p/68194655
    - pandas的cut，qcut函数的使用和区别 - 晓伟的文章 - 知乎 https://zhuanlan.zhihu.com/p/68194655

## Numpy 使用笔记



## matplotlib使用笔记

#### .rcParams

```python
1 plt.rcParams['figure.figsize'] = (8.0, 4.0) # 设置figure_size尺寸
2 plt.rcParams['image.interpolation'] = 'nearest' # 设置 interpolation style
3 plt.rcParams['image.cmap'] = 'gray' # 设置 颜色 style
```

```python
firgure.figsize(12.5, 4) # 设置 figsize
plt.rcParams['savefig.dpi'] = 300 #图片像素
plt.rcParams['figure.dpi'] = 300 #分辨率
# 默认的像素：[6.0,4.0]，分辨率为100，图片尺寸为 600&400
# 指定dpi=200，图片尺寸为 1200*800
# 指定dpi=300，图片尺寸为 1800*1200
# 设置figsize可以在不改变分辨率情况下改变比例
```





### seaborn使用笔记

- Python可视化 | Seaborn5分钟入门(三)——boxplot和violinplot - 易执的文章 - 知乎 https://zhuanlan.zhihu.com/p/34059825



## Tricks

- while small is arbitrary, we'll use the common minimum in statistics: http://nicholasjjackson.com/2012/03/08/sample-size-is-10-a-magic-number/
- lambda函数
    - 匿名函数本质上是一个函数，没有函数名称，因此使用匿名函数不用担心函数名冲突；
    - 匿名函数一般适用于创建一些临时性的，小巧的函数；



### 数据格式转换

#### [1. Categorical Encoding](http://pbpython.com/categorical-encoding.html)

- Find and Replace
- Label Encoding
- One Hot Encoding
    - 缺点：cause the number of columns to expand greatly
- Custom Binary Encoding
- Scikit-Learn

#### [2. Sklearn LabelEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.LabelEncoder.html)

#### [3. Sklearn OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html)

#### [4. Pandas Categorical dtype](https://pandas.pydata.org/pandas-docs/stable/categorical.html)

#### [5. pandas.get_dummies](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.get_dummies.html)

