# numpy入门

## 基本用法

```python
import numpy as  np

arr = np.array([1,2,3,4,5])
print(arr)
print(type(arr))
"""
[1 2 3 4 5]
<class 'numpy.ndarray'>
"""
```

## 常用属性

```python
arr = np.arange(15).reshape(3,5)
print(f'数组的形状(维度):{np.shape(arr)}')
print(f'数组的轴:{np.ndim(arr)}')
print(f'数组的长度:{np.size(arr)}')
print(f'数组的每个元素的大小(字节数):{arr.itemsize}')
print(f'数组的类型:{type(arr)}')

"""
数组的形状(维度):(3, 5)
数组的轴:2
数组的长度:15
数组的每个元素的大小(字节数):4
数组的类型:<class 'numpy.ndarray'>
"""
```

## 创建 ndarray 对象

### 3.1 把 python 列表封装成 ndarray 对象

```python
# int 数组
arr1 = np.array([1,2,3,4,5])
print(arr1)
# float 数组
arr2 = np.array([1.2,1.3,1.4])
print(arr2)
"""
[1 2 3 4 5]
[1.2 1.3 1.4]
"""
```

### 3.2 zeros() / ones() / empty() 函数创建 ndarray 对象

```python
# zeros() 创建全是 0 的数组,即 ndarray 对象
arr3 = np.zeros((2,3))
print(arr3)
"""
[[0. 0. 0.]
 [0. 0. 0.]]
"""
```

```python
# ones() 创建全是 1 的数组,即 ndarray对象
arr4 = np.ones((2,3))
print(arr4)
"""
[[1. 1. 1.]
 [1. 1. 1.]]
"""
```

```python
arr5 = np.empty((2,3))
print(arr5)
"""
[[1. 1. 1.]
 [1. 1. 1.]]
"""
```

### 3.3 arange() 函数,创建 ndarray 对象

```python
# arange(起始值,结束值,步长,类型) 类似 python 的 range()
arr6 = np.arange(1,5,3,dtype=np.int64)
print(arr6)
"""
[1 4]
"""
```

### 3.4 matrix() 生成二位数组

```python	
# matrix() 属于 ndarray 的子集,生成 二维数组
arr7 = np.mat('1 2; 3 4; 5 6')
print(arr7)
"""
[[1 2]
 [3 4]
 [5 6]]
"""
```

### 3.5 rand() / randint() / uniform() 生成 ndarray 对象

```python
# rand() 生成 0.0 到 1.0 之间的随机数组,包左不包右
arr8 = np.random.rand(2,3)
print(f'数组对象:{arr8}')
"""
数组对象:[[0.43059567 0.7529338  0.69109607]
 [0.14225813 0.69636405 0.83518308]]
"""
```

```python
# randint() 生成指定范围之间的随机数组,包左不包右
arr8 = np.random.randint(-1,5,size=(2,3)) # 2行3列 ,-1 ~ 5 之前的随机整数
print(f'数组对象:{arr8}')
"""
数组对象:[[-1  2  2]
 [ 4  0 -1]]
"""
```

```python
# uniform() 生成指定范围之间的随机小数,包左不包右
arr9 = np.random.uniform(-1,5,size=(2,3))
print(arr9)
"""
[[ 1.60984547 -0.06724437 -0.44424915]
 [ 3.81285357  3.7335037   2.46819611]]
"""
```

### 3.6 通过 astype() 函数,把 ndarray => ndarray

```python
# 1. 创建1个 float 类型的数据
arr11 = np.ones((2,3),dtype=np.float32)
print(arr11)
"""
[[1. 1. 1.]
 [1. 1. 1.]]
"""
```

```python
# 2. 把 arr11 的元素类型,从 float32 => int64
arr12 = arr11.astype(np.int64)
print(arr12)
[[1 1 1]
 [1 1 1]]
```

### 3.7 通过 logspace 创建等比数列 和 linspace() 等差数列

```python
# logspace(开始幂值,结束幂值,元素个数,base=底数)  生成 10^开始幂值 ~ -10^的结束幂值范围内的,指定个数的数据,底数默认是10,可以自定义
arr13 = np.logspace(0,5,10,base=2) # 10^0 ~10^5之间,10个元素,等比数列
print(arr13)
"""
[ 1.          1.46973449  2.16011948  3.1748021   4.66611616  6.85795186
 10.0793684  14.8139954  21.77264    32.        ]
"""
```

```python
# linspace(开始值,结束值,元素个数,endpoint=True|False)  生成起始值 ~ 结束值之间的,指定元素个数的值,等差数列,endpoint=True(默认),包含结束值 . False:不包含
arr13 = np.linspace(0,5,5) # 0-5之间,5个数,等差数列
print(arr13)
"""
[0.   1.25 2.5  3.75 5.  ]
"""
```

## ndarray 内置函数

### 4.1 基本函数

```python
"""
基本函数如下:
    np.ceil() 向上取整
    np.floor() 向下取整
    np.rint() 四舍五入
    np.isnan() 判断是否是nan
    np.multiply() 元素相乘
    np.divide() 元素相除
    np.abs() 绝对值
    np.where(condition,x,y) 三元运算符 x is condition else y
"""
```

### 4.2 统计函数

```python
 np.mean(),np.sum(): 所有元素的平均值,所有元素的和,参数是 number 或 array
 np.max(),np.min() 所有元素的最大值,所有元素的最小值,参数是 number 或 array
 np.std() np.var() 所有元素的标准差,所有元素的方差,参数是 number 或 array
 np.argmax() np.argmin() 最大值的下标索引值,最小的值下标索引值,参数是 number 或 array
 np.cumsum() np.cumprod() 返回一个一维数组,每个元素都是之前所有元素的 累加和 和 累乘积,参数是 number 或 array
```

示例

```python
arr = np.arange(12).reshape(3,4)
print(arr)
print(np.mean(arr))
print(np.sum(arr))
print(np.max(arr))
print(np.min(arr))
print(np.std(arr))
print(np.var(arr))
print(np.argmax(arr))
print(np.argmin(arr))
print(np.cumsum(arr))
print(np.cumprod(arr))
"""
 [ 4  5  6  7]
 [ 8  9 10 11]]
5.5
66
11
0
3.452052529534663
11.916666666666666
11
0
[ 0  1  3  6 10 15 21 28 36 45 55 66]
[0 0 0 0 0 0 0 0 0 0 0 0]
"""
```

### 4.3比较函数

```python
# any() 任意一个元素即可,返回true
# all() 所有元素都要满足,返回False
arr = np.random.randn(2,3) # 两行三列的 正态分布的数据
print(arr)
print(np.any(arr > 0))
print(np.all(arr > 0))
"""
[[ 0.52488608 -0.00660269 -0.5005525 ]
 [-0.21095703  0.71877865 -1.38563466]]
True
False
"""
```

### 4.4内置函数排序函数

```python
# 方式1: np.sort(arr) 排序返回新的副本
# 方式2: arr.sort() 直接修改原数组

arr = np.array([1,4,5,2,3])
# print(np.sort(arr))
arr.sort()
print(arr)
"""
[1 2 3 4 5]
"""
```

### 4.5 内置函数--去重函数

```python
arr = np.array([[1,2,1,6],[1,2,3,4]])
print(np.unique(arr))
"""
[1 2 3 4 6]
"""
```

## 基本运算

### 5.1 基本运算,即对应元素直接运算即可

```python
import numpy as np
arr1 = np.array([10,20,30,40])
print(arr1)
arr2 = np.arange(4)
print(arr2)

arr3 = arr1 + arr2
print(arr3)

arr4 = np.array([[1,2],[3,4]])
arr5 = np.array([[1,2],[3,4]])
arr6 = arr4 * arr5
print(arr6)
"""
[10 20 30 40]
[0 1 2 3]
[10 21 32 43]
[[ 1  4]
 [ 9 16]]
"""
```

### 5.3 行数相反

```python
arr1 = np.array([[1,2,3],[4,5,6]])
arr2 = np.array([[9,8],[1,2],[0,0]])

arr3 = arr1.dot(arr2)
print(arr3)
"""
[[11 12]
 [41 42]]
"""
```

