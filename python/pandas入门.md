# pandas入门

## Series 对象创建

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

