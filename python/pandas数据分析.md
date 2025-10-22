# pandas数据分析

导入包

```python
import pandas as pd
```

## 1. DataFrame 加载行\列数据

### 1.1 df加载列

```python
df = pd.read_csv("data/gapminder.tsv",sep="\t")
df.head()
df['列名'] # 加载一列
df[['列名1','列名2']] # 加载多列
```

### 1.2 df加载行

```python
# 通过 行索引获取 指定的行
df.loc[0]	# 根据 行索引 获取数据,行索引可以自定义  默认是 0 1 2 3 
df.iloc[0]	# 根据 行号 获取数据,行号相当于数组的下标,不可自定义

df.tail(n=1)	# 获取最后一行信息
df.head(n=1)	# 获取第一行信息
```

### 1.3 df加载中指定行 \ 列 数据(重点示例)

```python
# 1. loc 方式,获取指定多行数据
df.loc[[0,1,2]]

# 2/ iloc 方式,获取多条数据
df.iloc[[0,1,2,3]]

# 3. 加载指定行的指定列
df.loc[[20,21,22],['country',"year"]]   # 行索引 + 列名
df.iloc[[20,21,22],[0,2,4]]  # 行号 + 列的编号

# 4. 上述的 行,列 部分,不仅可以传入固定值,还可以通过 range(),切片等方式实现
df.loc[:,['country']]
df.loc[:,['country','continent']]
```

## 2. 分组和聚合介绍

```python
# 1. 分组统计格式: df.groupby('分组字段')['聚合操作字段'].具体的聚合函数名()
# 2. 分组统计格式: df.groupby(['分组字段1','分组字段2'])['聚合操作字段1','聚合操作字段2'].具体的聚合函数名()

# 2. 基于上述的格式,完成需求

# 需求1: 统计每一年预期寿命,平均人口,平均GDP
df.grounpby('year')['lifeExp'].mean() # 平均寿命
df.groupby('year')['gdpPercap'].mean() # 平均GDP
df.groupby('year')['pop'].mean()    # 平均人口

# 合并写法
df.groupby(['year','continent'])[['lifeExp','gdpPercap','pop']].mean()
# 扩展,如果计算的是 多个字段 不同聚合需求,可以用 agg函数实现
# 格式: agg({'列名':'聚合函数名','列名':'聚合函数ming'})
df.groupby(['year','continent']).agg({'lifeExp':'mean','pop':'max'}) # 平均寿命 最大人口

# 需求3: 每个达州,列出了多少国家和地区
df.groupby('continent')['country'].nunique()
```

## 3. DataFrame 基本制图

```python
# 把上述的每年.平均寿命 结果绘制成图形(折线图)
df.groupby('year')['lifeExp'].mean().plot(figsize=(10,5))

# 绘制柱状图
df.groupby('year')['lifeExp'].plot(kind='bar',figsize=(100,50))
```

## 4. DataFrame 基本统计方式

```python
# 1. 加载数据源,获取到 df 对象
movie = pd.read_csv('data/movie.csv')
movie.head()

# 2. 演示常用的 统计值的方式
movie.shape             # 行列
movie.dtypes            # 每列的数据类型
movie.info()            # 每列信息基本信息
movie.describe()
```

## 5. DataFarm 对象常见的排序方式

```python
# 2. 完成如下的需求
nlargest(n,'列名')	# 基于某列,找出最大的 n 个
nsmallest(n,'别名')	# 基于某列,找出最小的 n 个

# 需求1: 找到小成本,高口碑的电影
# step1: 找到该需求中,涉及到的列
movie2 = movie[['movie_title','budget','imdb_score']]
movie2.head()

# 找到评分最高的 100 部电影
movie2.nlargest(100,'imdb_score')

# 基于上一步找到 预算最小的 10 个电影
movie2.nlargest(100,'imdb_score').nsmallest(10,'budget')
```

```python
# 需求2: 找到每年 imdb_score 评分最高的电影
# 1. 从原始数据中,找到要的几列数据
movie3 = movie[['movie_title','title_year','imdb_score']]
movie3.head()
# 方式一: 分组的形式实现
movie3.groupby(['title_year'])[['imdb_score','movie_title']].max() # 按照分组的方式实现
# 方式二: 按照年 , 评分 降序实现
movie3.sort_values(['title_year','imdb_score'],ascending=False)
# 基于上述的数据,只获取每条的 第一条
# 参数解释 subset 表示子集,即参考某列的进行删除,进行去重操作
movie3.sort_values(['title_year','imdb_score'],ascending=False).drop_duplicates(subset="title_tear")
```

## 6. 数据组合

### 6.1 数据组合--concat() 

```python
# 1. 加载数据源,获取 df 对象
df1 = pd.read_csv("data/concat_1.csv")
df2 = pd.read_csv("data/concat_2.csv")
df3 = pd.read_csv("data/concat_3.csv")

# 2. concat() 把 df 对象连接在一起
# 格式: pd.concat([])
# 细节: concat() 函数 按 行拼接 row,参数:列名, 按列拼接是 行索引 columns
pd.concat([df1,df2,df3],axis="rows")		# 按照行拼接
pd.concat([df1,df2,df3],axis="columns")		# 按列拼接

# 3. 把 DataFrame 和 Series 对象拼接到一起
# 细节: 由于 Series 是列数据, concat 方法是默认添加行, 但是 Series 没有行索引, 所以添加了第一个新列,缺失的数据用 NaN 填充
pd.concat([df1,pd.Series(['a','b','c'])])

# 4. 如果将将 ['n1','n2','n3','n4'] 作为 行链接到 df1后,如何实现. (添加行)
df5 = pd.concat([['n1','n2','n3','n4']],columns = df2.columns)
pd.concat([df2,df5],ignore_index=True)		# 忽略行索引,即: 会自动重置索引


# 5. 添加列
pd.concat([df2,df5],axis="columns")
```

### 6.2 数据组合--merge() 

- 在使用 concat 连接数据时, 涉及到参数 join = inner / outer
- 数据库中可以以来共有数据那两个或者多个表组合起来,即 join 操作
- DataFrame 中也可以实现类似的 join
- Pandas 也可以用过 pd.join 命令组合数据,也可通过 pd.merge 组合
- merge 更灵活,如果想依据行索引来合并 DataFrame 可以考虑使用 join 函数

#### 6.2.1 一对一合并

```python
# 从 sqlit 中读取数据
data_db = sqlite3.connect('data/chinook.db')
tracks = pd.read_sql_query("select * from tracks", data_db)
genres = pd.read_sql_query("select * from genres", data_db)

# 从 tracks 表(歌曲表) 提取部分数据,使其不含重复的 GenreID 值
tracks_subset = tracks.loc([0,62,76,98,110,193,204,281,322,359])

# 3. 通过 merge() 函数,实现 tracks_subset (歌曲表子集) 和 genres (歌曲分类表) 连接操作
# 格式: df.merge(df,on="关键字",how="连接方式")
# 细节1: 如果两个 df 对象关联字段意向, on 直接连接,如果不一样,用 left_on="左 df 字段名" right_on="右 df 字段名"
genres.merge(tracks_subset[['TrackId',"GenreId",'Milliseconds']],on="GenreId",how="left")  # left= 左表连接 坐标全集+交集
genres.merge(tracks_subset[['TrackId',"GenreId",'Milliseconds']],on="GenreId",how="right") # right= 右表连接 右表全集+交集
genres.merge(tracks_subset[['TrackId',"GenreId",'Milliseconds']],on="GenreId",how="inner") # inner = 内连接,交集
# 细节2: 如果有两个 df 的字段重名了,则 suffixes=("_x","_y") 会分别给 左df 和 右df 加后缀
genres.merge(tracks_subset,on="GenreId",how="outer") # outer = 满外连接,全部

# 可自定义重名的后缀
genres.merge(tracks_subset,on="GenreId",how="outer",suffixes=('_左表','_右表'))

```

#### 6.2.1 多对一合并

```python
# 需求: 计算每个类型 歌曲的平均时长
#1. 把 歌曲表 tracks 和 歌曲类别表 genres 关联到一起
gener_track = genres.merge(tracks,on="GenreId",how="left")
# 2. 基于上述的数据,按照 歌曲类别分组,计算平均时长
tmp_series = gener_track.groupby(["GenreId","Name_x"])['Milliseconds'].mean()

# 3. 基于上述的数据 转成 日期格式
pd.to_timedelta(tmp_series,unit='ms').dt.floor('s').sort_values( )
```

## 7.缺失值处理

在 pandas 中 , 缺失值用 NaN nan NAN 表示,其他的 flsy 值不能代表空

```python
# 判断某个值是否为空 notnull() 和 notna() 和 isnull()
print(pd.notna(np.nan))
print(pd.notnull(np.nan))

print(pd.isnull(10))
print(pd.notnull(10))
```

```python
# 1. 加载数据,包括缺失值
pd.read_csv("data/survey_visited.csv")
# 2. 加载数据时,忽略缺失值,用 "" 填充
pd.read_csv("data/survey_visited.csv",keep_default_na=False) # keep_default_na 是否加载缺失值
# 3. 加载数据时,手动指定某些值为空之
pd.read_csv("data/survey_visited.csv",keep_default_na=False,na_values=["619"])
```

### 如何处理缺失值

#### 3.1 加载数据源及缺失值的可视化

```python
df = pd.read_csv("data/titanic_train.csv")
# 3. 查看是否获取数据
df['Survived'].value_counts()

# 4. 查看缺失值的分布情况和关联性
# 需要单独安装 missingno 
# 绘制柱状图
msno.bar(df)
msno.heatmap(df)
```

#### 3.2 缺失值处理方式: 删除缺失值

```python
# 1. 查看各列控制数据统计情况
df.isnull().sum()

# 2. 删除缺失值
"""
	参数解释
		axis = 0 默认 代表行
		how =  any 任意一个为空  all 都为空
"""
df.dropna(how="any",subset=['Age'])
```

#### 3.3 缺失值处理方式: 非时间序列数据

```python
df.isnull().sum()

# 2. dillna() 函数,实现填充缺失值
df.fillna(0).isnull().sum()  # 将缺失值全部填充为0

# 3. 实际开发中可能会填充为 平均数 中位数 标准差等
df["Age"] = df.Age.fillna(df.Age.mean())

# 4. 填充后查看统计信息
df.isnull().sum()
```

#### 3.4 缺失值处理方式: 线性填充

```python
# 1. 获取数据源,获取 df 对象
city_day = pd.read_csv("data/city_day.csv",parse_dates=["Date"],index_col="Date")
city_day

# 2. 获取有空的某一段数据
city_day['Xylene'][50:64]

# 思路1: 用 空值的上一个或者下一个值来填充
city_day["Xylene"].ffill()[50:64]
city_day["Xylene"].bfill()[50:64]

#思路3: 线性填充 结合 前后数据,推到数一个填充值
# limit_direction 参数有三个值  both:参数前边 + 后边数据 推导出 ; forward: 只参考前边的值  backward 只参考后边的值
city_day["Xylene"].interpolate(limit_direction='both')[50:64]
```

