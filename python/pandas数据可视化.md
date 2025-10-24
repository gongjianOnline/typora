# pandas数据可视化

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

