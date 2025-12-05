# 02. KNN近邻算法

## KNN概念

一个样本最相似的 K 个样本中大多数属于某一个类别，则该样本业数据这个类别

- 解决问题：分类问题、回归问题
- 相似性：欧氏距离

##  KNN 分类流程

1. 计算位置样本到每一个训练样本的距离
2. 将训练样本根据距离大小升序排列
3. 取出距离最近的K个训练样本
4. 进行多数表决，统计K个样本中那个列别的样本个数最多
5. 将未知的样本归属到出现次数最多的类别

## KNN回归流程

1. 计算未知样本到每一个训练样本的距离
2. 将训练样本根据距离大小升序排列
3. 取出距离最近的K个训练样本
4. 把这个 K个样本的目标值计算器平均值
5. 将未知的样本预测的值了

## K值的选择

- K值过小：过拟合
  - 以为这模型更容易受到异常点影响，更已学习到嘈杂数据，模型有过拟合的风险
- K值过大：欠拟合
  - 意味着模型变得简单了，不易受到异常点影响，模型有欠拟合低风险

---

## KNN算法API使用

### KNN 分类 API

```python
sklearn.neighbors.KNeighborsClassifier(n_neighbors=5)
```

n_neighbors: int 可选（默认=5）， n_neighbors 查询默认使用的邻居数

完整实例

```python
from sklearn.neighbors import KNeighborsClassifier

def demo01():
    estimator = KNeighborsClassifier(n_neighobors=3)
    x = [[39, 0, 31], [3, 2, 65], [2, 3, 55], [9, 38, 2], [8, 34, 17], [5, 2, 57], [21, 17, 5], [45, 2, 9]]
    y = [0, 1, 2, 2, 2, 1, 0, 0]
    /*训练*/
    estimator.fix(x,y)
    /*推理*/
    mypre = estimator.predict([[23,3,17]])
    print("mypre--->",mypre)
    
if __name__ == "__main__":
    demo01();
```

### KNN回归问题

```python
sklearn.neighbors.KNeighborsRegressor(n_neighbors=5)
```

完整实例

```python
from sklearn.neighbors import KNeighborsRegressor

def demo02_knnapi():
    estimator = KNeighborsRegressor(n_neighbors=5)
    x = [[80, 86], [82, 80], [85, 78], [90, 90], [86, 82], [82, 90], [78, 80], [92, 94]]
    y = [84.2, 80.6, 80.1, 90, 83.2, 87.6, 79.4, 93.4]
    /*训练*/
    estimator.fit(x, y)
	/*推理*/
    result = estimator.predict([[20,20]])
    print(result)
if __name__ == '__main__':
    demo02_knnapi()
```

---

## 特征处理

为啥做归一化和标准化：

特征的单位或者大小相差较大，或者某特征的方差相比其他的特证要大出几个数量级，容易影响（支配）目标结果，是的一些模型（算法）无法学习到其他的特征

例如

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/e26667c23c660593cbe480ece0e24aea.png)

### 归一化处理

归一化：通过对原始数据进行变换把数据映射到【min，mix】（默认为（0,1））之间

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/5049da31-6a7f-4332-9acc-dd59aa42cb4f.png)

使用场景：

- 如果出现异常点，影响了最大值和最小值，那么结果显然会发生改变
- 应用场景：最大值与最小值非常容易受异常点影响，鲁棒性交叉，只适合传统精确小数据场景

归一化API：

```python
sklearn.preprocessing.MinMaxScaler(feature_range=(0,1))
```

feature_range 缩放区间

完整示例

```python
from sklearn.preprocessing import MinMaxScaler

def demo03():
    # 1. 数据准备
    data = [[90,2,10,40],[60,4,15,45],[75,3,13,46]]
    # 初始化归一对象
    transformer = MinMaxScaler()
    # 原始特征进行变化
    data = transformer.fit_transform(data)
    # 打印归一化结果
    print(data)
if __name__ == "__main__":
    demo03()
```

### 数据标准化

通过对原始数据进行标准化，转换为均值为 0 标准差为 1 的标准正态分布的数据

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/3c602e8d-2569-43e5-aad4-9ae1abdc5d9e.png)

使用场景：

- 如果出现异常点，由于具体一定数量，少量异常点对于平均值的影响并不大
- 应用场景: 适合现代嘈杂大数据场景

标准化 API

`sklearn.preprocessing.StandardScaler()`

`flt_transform(x)` 将特征进行归一化缩放

完整实例

```python
from sklearn.preprocessing import StandardScaler
def demo04():
    # 1. 数据准备
    data = [[90,2,10,40],[60,4,15,45],[75,3,13,46]]
    # 2. 初始化标准差对象
    transformer = StandardScaler()
    # 3. 对原始特征进行变换
    data = transformer.fir_transform(data)
    # 4. 打印归一结果
    print(data)
    
    # 5. 打印每一列数据的均值和方差
    print("transformer.mean--->",transformer.mean_) # 均值
    print("transformer.var-->",transformer.var_) # 方差（标准差的平方）
    
if __name__ == '__main__':
    demo04()
```

