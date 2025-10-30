# pandas数据可视化

目前主流可视化包 如 pandas 自带 plot 函数 \ seaborn 包 都是基于 Matplotib 这个工具的二次封装和简化 , 所以在使用 这些包的时候 都需要先配置 Matplotib  

## Matplotib 绘图

```python
import pandas as pd
import matplotlib.pyplot as plt

plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False
```

### 方式一：状态接口方式绘制

```python
x = [-3,5,7]
y = [10,2,5]
# 创建画布
plt.figure(figsize=(16,8))
# 2. 具体的绘图
plt.plot(x,y)
# 3.设置 x y轴范围
plt.xlim(-1,10)
plt.ylim(0,10)
# 4. 设置 x 轴,y 轴显示内容
plt.xlabel("x轴",fontsize=20)
plt.ylabel("y轴")
plt.grid(True)
```

### 方式二：面向对象方式

```python
fig,ax = plt.subplots(figsize=(16,8))
ax.plot(x,y)
ax.set_xlim(-4,10)
ax.set_ylim(0,10)
ax.set_xlabel("x轴"，fontsize=20)
ax.set_ylabel("y轴"，fontsize=20)
ax.set_title("面向对象方式直方图")
ax.grid()
ax.show()
```

### 1.2. 绘制变量散点图

```python
# 加载数据
anscombe = pd.read_csv("data/anscombe.csv")
anscombe

# 查看各组数据
anscombe[anscombe['dataset']=="I"]
anscombe[anscombe['dataset']=="II"]
anscombe[anscombe['dataset']=="III"]
anscombe[anscombe['dataset']=="IV"]

# 查看上述各组数据的详情
anscombe.groupby('dataset').describe().T

# 通过查看分析统计结果,发现上述数据高度相似,在次数据上进行图标创建
# 创建画布,指定画布的宽高
fig = plt.figure(figsize=(16,8))
# 创建四个组
ax1 = fig.add_subplot(2,2,1)
ax2 = fig.add_subplot(2,2,2)
ax3 = fig.add_subplot(2,2,3)
ax4 = fig.add_subplot(2,2,4)
# 4.3 具体的绘制四个子图
ax1.scatter(anscombe[anscombe['dataset'] === "I"]['x'],anscombe[anscombe['dataset'] === "I"]['y'])
ax2.scatter(anscombe[anscombe['dataset'] == "II"]["x"], anscombe[anscombe['dataset'] == "II"]["y"])
ax3.scatter(anscombe[anscombe['dataset'] == "III"]["x"], anscombe[anscombe['dataset'] == "III"]["y"])
ax4.scatter(anscombe[anscombe['dataset'] == "IV"]["x"], anscombe[anscombe['dataset'] == "IV"]["y"])
# 设置子标题
ax1.set_title("数据I")
ax2.set_title("数据II")
ax3.set_title("数据III")
ax4.set_title("数据IV")
# 设置大标题
fig.suptitle("Anscombe数据集",fontSize=20)
# 生成绘制
plt.show()
```

### 1.3 Matplotib绘图-单变量-直方图

```python
# 加载数据
tips = pd.read_csv("data/tips.csv")
tips
# 绘制图标
fig = plt.figure(figsize=(16,8))
# x ,表示 x 轴要显示的内容, bins 表示几个区间(划分10个区间),其实就是 起始值,结束值然后生成区间 +1 个的等差数列
plt.hist(x=tips['total_bill'],bins=10)
plt.show()
```

### 1.4 Matplotib双变量-散点图

```python
def recode_sex(sex):
    if sex == "Female":
        return "pink"
    else:
        return "blue"
    
# 创建一个新的列,存放性别的颜色
tips.loc[:,"sexColor"] = tips['sex'].apply(recode_sex)

# 生成画布
fig = plt.figure(figsize=(16,8))
plt.scatter(tips['totall_bill'],tips['tip'],c=tips.sexColor,s=tips['size']*40,alpha=0.5)
```

---

## 2.  pandas 绘图

导入示例数据集

```python
review = pd.read_csv("data/winwmag-data_first150k.csv")
review
```

### 2.1 单变量-柱状图

```python
kwargs = dict(figzise=(16,8),color=['red','pink','blue'],grid=True)
review['province'].value_counts().head(10).plot.bar(**kwargs)
```

### 2.2 单变量绘制折线图

```python
review['points'].value_counts().sort_index().plot.line(**kwargs)
```

---

## 3. seaborn 绘图

### 3.1 Seaborn -- 单变量-直方图

```python
tips = pd.read_csv("data/tips.csv")
tips

# 绘制 总小费金额的 直方图
# 创建画布,坐标轴
fig,ax = plt.subplots(figsize=(16,8))
# 绘图kde : 核密度折线图,用于分析数据的正太分布情况,样本数据,数据分布...
sns.histplot(data=tips,x="totab_bill",kde=True)
```

### 3.2 Seaborn -- 正态分布图

```python 
# 绘制总消费的密度图
fig,ax = plt.subplots(figsize=(16,8))
sns.kdeplot(data=tips,x='total_bill')
```

### 3.3 seabor 散点 + 拟合回归线

```python
fig,ax = plt.subplots(figsize=(16,8))
sns.regplot(x=tips['total_bill'],y=tips['tip'],data=tips)
```

### 3.4 seabor 蜂巢图

```python
# 蜂巢图
sns.jointplot(x='total_bill',y='tip',data=tips,kind='hex')
plt.show()
```

### 3.5 seabor 2D 密度图

```python
fig,ax = plt.subplots(figsize=(16,8))
sns.kdeplot(data=tips,x="total_bill",y="tip",fill=True,char=True)
```

### 3.6 seabor 箱体图

```python
fig,ax = plt.subplots(figsize=(16,8))
ax = sns.boxplot(data=tips,x="time",y='tip')
```

### 3.7 seabor 鲍鱼图

```python
plt.subplots(figsize=(16,8))
sns.violinplot(data=tips,x="time",y="tip",hue="sex")
```

