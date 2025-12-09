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

## 超参数选择

交叉验证 & 网格搜索

### 交叉验证

是一种数据集的分割方法，将训练集划分为几份，拿一份做验证集（测试集），其他 n-1 份作为训练集

原理：将数据集分为 CV=4 份

1. 把第一份数据做测试，其他数据做训练
2. 把第二份数据做测试，其他做训练
3. 。。。依次类推，总共训练4次，评估4次
4. 使用训练集 + 验证集 多次评估模型，取平均值做交叉验证为模型得分
5. 若 K= 5 模型得分好，在使用全部数据集（训练集+测试集）对 K=5 模型再练一遍。在使用测试集对 k=5模型做评估

交叉验证是划分数据的一种方法，目的就是为了得到更加准确可信的模型评分

**CV：确定数据集分割得到最优方式**

### 网格搜索

模型调参的工具，寻找最优超参数的工具

一种需要若个参数传递给网格搜索对象，他会自动完成不同参数的组合，模型训练，模型评估后返回最优的超参数

网格搜索 + 交叉验证的强力组合（模型选择和调优）

交叉验证解决模型的数据输入问题（数据集划分）得到更可靠的模型

模型搜索解决超参数的组合

两个组合在一起形成模型参数调优的解决方案

#### API 应用

```python
sklearn.model_selection.GridSearchcv(estimator,params_grid=None,cv=None)
# estimator  评估器对象
# params_grid: 估计器参数（dict）{n_neighbors:[1,3,5]}
# cv: 指定几折交叉验证
--------
# fit：输入训练数据
# score：准确率
# 结果分析
"""
	best_score_:交叉验证中验证最好的结果
	best_estimator_: 最好的参数模型
	cv_result: 每次交叉验证的验证集准确结果和训练集准确率结果
	best_params: 最好的模型参数
"""
```

