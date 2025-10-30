# pandas入门

## 一、Series 对象创建

### 1.1 numpy 的 ndarray 转 Series 对象

```python
import numpy as np
import pandas as pd
# 创建 ndarray 对象
arr = np.array([1,2,3,4])
print(arr)

# 将上述的 ndarray 对象, 转为 Series
s1 = pd.Series(arr)
print（s1）
```

### 1.2 直接传入 Python 列表，构建 Series 对象

```python
s2 = pd.Series(["张三","男",33])
print(s2)
```

### 1.3 传入 Python 列表, 构建 Series 对象,并执行索引

```python
s2 = pd.Series(["张三","男",33],index=['name','sex','age'])
print(s2)
```

### 1.4 通过 元组 的方式创建

```python
s2 = pd.Series({'name':'张三','sex':'男','age':33})
print(s2)
```

## 2. 创建 DataFrame 对象

### 2.1 字典方式构建 df 对象

```python
dict_data = {
    "id":[1,2,3],
    "name":["张","李","王"],
    "age":[20,20,20]
}
df1 = pd.DataFrame(dict_data) # 字典方式,每组键值对 = 1列数据
print(df1)
```

### 2.2 列表 + 元组方式构建 df 对象

```python
list_data = [(1,"乔峰",33),(2,"虚竹",29),(3,"段誉",21)]
df2 = pd.DataFrame(list_data,index=["x","y","z"],colums=["id","name","age"]) # 以行的方式传入数据,columns是设置:列名
print(df2)
```



## 3. Series 的常用属性方法

```python
loc				 根据 索引行 获取某行数据
iloc			 根据 行号 获取某行数据
dtype 或 dtypes  获取 Series 元素类型
T				 专转置函数
shape			 维度
size			 大小(元素个数)
values			 获取所有的值
index			 获取所有的索引,功能类似于 keys() 方法
```

### 3.1 根据csv文件内容,创建 df 对象

```python
df = pd.read_csv("data/nobel_prizes.csv",index_col="id") # index_col: 设置表中的某列为 索引列.
df.head() # 默认获取 5 条数据
```

```python
"""
2. 从上述 df 对象中,获取第一行数据,即 Series 对象
"""
first_row = df.loc[941]
print(first_row)
# 使用 iloc 属性: 根据行号获取
first_row = df.iloc[0]
first_row
```

```python
# 3. Series 对象属性
print(first_row.dtype) # 打印 Series 对象的元素类型.object (表示字符串)
print(first_row['year'].dtype) # int64
print(first_row['firstname'].dtype) # 报错.因为 str 属性中没有 dtyoe 属性
```

### 3.2 Series 常用方法

```python
# 1. 构建 Series 对象
s1 = pd.Series([1,2,3,4,6,6],index=['A','B','C','D','E',"F"])
print(s1)
```

```python
# 2. 演示 Series 对象的 常用方法
print(len(s1))            # 长度:6
print(s1.size)            # 长度:6
print(s1.head())          # 默认获取前 5 条
print(s1.head(n=2))       # 指定获取前 2 条

print(s1.tail())            # 默认获取后 5 条
print(s1.tail(n=3))         # 指定,获取后3条

print(s1.keys())            # 获取 Series 索引
print(s1.index)             # 获取 Series 索引

print(s1.tolist())         # 转列表
print(s1.to_list())        # 转列表
print(type(s1.tolist()))   # <class 'list'>
print(s1.to_frame())       # 转成 DF 对象

print(s1.describe())       # 查看 Series 的详细信息, 例如: 最大值\最小值\平均值\标准差等
print(s1.max())
print(s1.min())
print(s1.mean())
print(s1.std())             # 标准差

print(s1.drop_duplicates())    # 去重,返回 Series 对象
print(s1.unique())              # 去重,返回数组


print(s1.sort_values())         # 根据 值 排序,默认:升序
print(s1.sort_index(ascending=False))  # 根据 值 降序

print(s1.sort_index())              # 根据 索引 排序,默认 升序
print(s1.sort_index(ascending=False)) # 根据 索引 降序

print(s1.value_counts())        # 统计每个值出现的次数


s1.hist()                   # 绘制 直方图
```

### 3.3 Series 的布尔值的操作

```python
# 1. 读取数据源,获取 df 对象
df = pd.read_csv("data/scientists.scv")
df
```

```python
# 2. 手动传入布尔值,获取内容
bool_list = [True,True,True,True,False,False,False,False]
df[bool_list]
```

```python
# 3. 根据条件传入布尔值,筛选数据
# 需求: 删选出 年龄 大于 平均年龄的科学家

# 3.3.1 获取年龄列的数据
ages_series = df['Age']

# 3.3.2 计算平均年龄
avg_age = ages_series.mean()
print(avg_age)

# 3.3.3 判断当前年龄是否大于当前平均年龄;布尔列表
data = df[df['Age'] > avg_age ]
data
```

---

### 3.3 Series 运算

- Series 和数值变量的计算时,变量会与 Series 中的每一个元素逐一进行计算
- 两个 Seroes 之间运算,如果 Series 元素个数相同,则两个 Series 对应的元素进行计算
- 元素个数不同 Series 之间进行计算,会根据索引进行. 索引不同的元素最终计算的结果会充填成缺失值 NAN 表示
- Series 之间的计算,数据会尽可能依据索引标签进行相互计算

---

## 二、DataFrame常用属性介绍

### 1. DataFrame 常用属性

```python
# 1. 加载数据源,获取 df 对象
df = pd.read_csv("data/scientists.csv")
# 2. 演示 df 对象的常用属性
print(df.ndim)			# 轴(几维数组,就是几轴) 2
print(df.shape)			# 维度(几行几列) (8,5)
print(df.size)			# 行数 * 列数,包括 NAN 40

print(df.columns)		# 获取列名
print(df.index)			# 获取 df 对象的 索引列的值
print(df.value)			# 获取数据
```

### 2. DataFrame 对象常用函数

```python
print(len(df))			# 行数 8
df.head()				# 获取前五行
df.tail()				# 获取后五位
df.keys()				# 获取所有的列名， 等于 df.columns
df.info()				# 查看 df 对象的基本信息
df.describe()			# 查看 df 的统计信息
df.describe(exclude=['int','float'])		# 查看 df 对象的统计信息,除了 int 和 float
df.describe(include="all")					# 查看 df 对象的统计信息,所有类型都看
df['Age'].mean()		# 查找各列的平均值
df.max()				# 查找各列的最大值
df.min()				# 查找各列的最小值
df.count()				# 统计各列的出现次数
df.hist()				# 绘制直方图
```

### 3. DataFrame 的布尔索引

和 series 用法基本一致

```python
"""
需求: 查找 movie.csv 文件中,电影时长 > 平均时长的 电影信息
读取数据源文件,获取 DF 对象
"""
movie_df = pd.read_csv("data/movie.csv")
movie_df.head()

movie_df[movie_df.duration > movie_df.duration.mean()]
```

### 4. DataFrame 对象计算

```python	
"""
1. DataFrame 对象和数值运算,就是把数值作用到 每个 DataFrame 身上
"""
df = pd.read_csv("data/scientists.csv")
df*2

# 2. df 和 df 运算,则对应元素直接运行即可,如果两个 df 之间索引不匹配,则用 NAN 填充
df + df
```

### 5. 更改 Series 和 DataFrame 对象的 行索引,列名

#### 5.1 读取文件后,设置行索引

```python
movie_df = pd.read_csv("data/movie.csv")
movie_df.head()

# 2. 设置 movie_title(电影名),行索引
"""
在 pandas 中,90%以上的函数,都是在源数据拷贝一份进行修改,并返回副本,而这列函数都有一个特点,即 inplace 参数
默认 inplace = False , 即: 返回副本,不修改源数据,如果  inplace = True,则是直接修改源数据
new_movie = movie.set_index('movie_title')
new_movie.head()
"""
movie_df.set_index('movie_title')
```

#### 5.2 读取文件时,设置航索引

```python
# 1. 读取数据源文件,获取 df 对象, 指定 电影名为索引列
movie_df = pd.read_csv("data/movie.csv",index_col='movie_title')
movie_df.head()
```

#### 5.3 取消设置的行索引,归为:系统自动提供的 0~n

```python
movie_df.reset_index(inplace=True)
movie_df.head()
```

### 6.  修改 DataFrame 行索引和列名

```python
# 1. 读取数据源文件,获取 df 对象,指定行索引
movie = pd.read_csv("data/movie.csv",index_col="movie_title")
movie
```

#### 方法一: rename() 函数直接修改

```python
# 2. 获取 前五个列名,为方便后续操作
movie.index[5]
# 3. 获取 前五个行索引 方便后面更改
movie.colums[5]
# 4. 具体修改列名 和 行索引的动作
idx_name = {'Avatar':'阿凡达',"Pirates of the Caribbean: At World's End":"加勒比海盗"}
col_name = {'color':"颜色"}

movie.rename(index=idx_name,columns=col_name)
```

#### 方法二：将 index 和 column 属性提取出来，修改之后在放回去

```python
movie = pd.read_csv("data/movie.csv",index_col='movie_title')
idx_list = movie.index.tolist()
col_list = movie.columns.tolist()

idx_list[0] = "阿凡达"
col_list[0] = "颜色"

movie.index = idx_list
movie.colums = col_list

movie.head()
```

### 7. DataFrame 添加 删除 插入列

```python
# 1. 添加列，格式为 df['列名'] = 列值
# 新增 1 列,has_sen=0,表示是否看过这个电影 0 没看 1 看过
movie['has_seen'] = 0
```

```python
# 2. 删除列
movie.drop("has_seen",axis='colums',inplace=True)
movie
```

```python
# 插入列 在索引 1 的位置插入 profit列,他的(总盈利) = gross(总收入) - budget(总预算)
# insert() 函数, 不是返回副本,而是在 源数据上直接修改
movie.insert(loc=1,column="profit",value=movie['gross'] - movie.budget)
movie
```

### 8. DataFrame 导入导出数据

#### 8.1 pickle 文件

如果保存的对象是计算的中间结果,或者保存的对象以后在 python 中复用,可以把对象保存为 .pickle 文件

如果保存成 pickle 文件,只能在 python 中使用

文件扩展名可以使 .p .pkl .pickle

格式

```python
scientists_name = pd.read_pickle("xxx.pickle")
print(scientists_name)
```

#### 8.2 csv 文件

数据协作和共享的首选格式

```python
names.to_csv("xxx.csv")
# 设置分隔符 \t
names.to_csv("xxx.csv",sep="\t")
# 不在 csv 文件中写行名
names.to_csv("xxx.csv",index=False)
```

#### 8.3 excel 文件

Series 这种数据不支持 to_excel 方法,需要先把 Series 转成 DataFrame

```python
name_df = names.to_frame()
import xlwt
names_df.to_excel("xxx.xls")

# 把 DataFrame 保存为 excel
scoentists.to_excel("xxx.xlsx",sheet_name="表名",index=False)
```

读取

```python
pd.read_excel("xxx.xlsx")
"""
pandas 读写 excel 需要额外安装三个包
pip install -i xlwt openpyxl xlrd
"""
```

#### 8.4 其他数据格式

feather 文件 用于存储二进制对象,同时可以在R语言中使用,通常用于中间数据格式(python和R之间的数据传递),一般不用于保存最终数据

其他导出方法: 

to_clipboard	       把数据保存在系统剪切板 \ 方便黏贴

to_dict			 把数据传承 python字典

to_hdf			  把数据保存为 HDF 格式

to_html		        把数据转成 HTML 格式

to_json			 把数据转换成 JSON 字符串

to_sql			   把数据保存到 sql 数据库 		



整体示例

```python	
# 8.1 导出数据
df = pd.read_csv("data/scientists.csv")
df

# 8.2 对上述的 df 操作,模拟实际开发中,对 df 做处理
# 筛选数 年龄 > 平均年龄的数据
new_df = df[df.Age > df.Age.mean()]

# 8.3 把 df 对象,写出到目的地中
# new_df.to_pickle("output/scientists_pickle.pkl")
# new_df.to_excel("output/scientists_xls.xlsx",sheet_name="scientists",index=False)
new_df.to_csv("output/scientists_csv.csv",index=False)

# 导入数据演示
# pd.read_pickle("output/scientists_pickle.pkl")
# pd.read_excel("output/scientists_xls.xlsx")
pd.read_csv("output/scientists_csv.csv")
```

---

