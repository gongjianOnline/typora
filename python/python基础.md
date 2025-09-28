# python基础范式

## 循环

### for

```python
for index in range(10):
    if index % 2 == 0:
        print(index)
```

## while

```python
i = 1
while i <= 10:
    print("xx")
    i += 1
```

---

## 三元运算符

```python
print( 10 if 11>10 else 0)
```

## 字符串操作

字符串切片 [起始索引:结束索引:步长] (左开右闭)

### 细节

- 正索引从前往后,从 0 开始

- 如果不写其实索引, 从 -1 开始

- 如果索引 和 步长的方向相反,获取不到数据

- 不写结束索引, 默认到随后

- [::-1] 表示字符串反转

### 常用API

- find(子串,[开始位置下标],[结束位置下标])  监测字符串是否包含,返回下标,否则返回 -1
- index(子串,[开始位置下标],[结束位置下标]) 监测字符串是否包含,返回下标,否则返回异常
- replace(新串,旧串,替换次数) 返回替换后的字符串
- split() 字符串分割 (同 js)
- len(str) 返回字符串长度
- 分隔符.join(str) 返回隔开字符串

---

## 列表(array\list)操作

CURD操作

- 增
  - append(单个值/多个值)  在末尾添加, 如果添加 列表 整个添加(二维数组)
  - extend(单个值/多个值) 在末尾添加, 如果添加 列表 拍平后添加(一维数组)
  - insert(索引值,要插入的元素) 在指定位置插入元素,如果索引不存在,默认在最后添加
- 查
  - index(数据,[开始下标],[结束下标]) 返回下标,没有返回异常
  - count() 出现的顺序
  - in  判断指定数据是否在列表中,返回 booler
  - not in 判断指定数据是否在列表中
- 删
  - del 列表名  删除列表
  - del 列表名[index] 根据索引删除元素
  - 列表名.clear[] 返回一个空列表
  - 列表名.pop(index) 根据下标删除
  - 列表名.remove(元素值) 根据元素删除
- 改
  - list.reverse()  反转列表元素
  - list.sort() 升序
  - list.sort(reverse=True)

---

## 元组

​	概述: 不可改变类型,可以存储多个元素和不同类型的值(时机开发中,建议存储相同类型的值)

​	特点：元素不可修改

​	定义格式：`t1=(1,2,3)`

​	细节：元组定义时，如果只有一个值，末尾必须加 逗号

- 同样支持 API

  index \ count \ len

```python
t1 = (1,2,3,4)
print(type(t1))
print(t1.index(3))
```

---

## 字典(object\map)

存储键值对数据,数据可变类型

定义格式: `{key:value}`

- 查
  - obj.get(key,[默认值])
  - obj.keys() 获取所有key
  - obj.values() 获取所有value
  - obj.items() 把键值对变成元组,返回列表

---

## 集合(set)

集合里边的元素不可重复,等同于 JS 中的 Set , 主要用于去重操作

定义格式

```python
set1 = {1,2,3,"10"}
set2 = set([1,2,3,"10"])
```

---

## 容器类型公共操作

公共运算符

- "+" 表示: 合并(拼接) 可用于: 字符串\列表\元祖
- "*" 表示: 复制 可用于: 字符串\列表\元组
- in 表示: 是否在 可用于: 字符串\列表\元组\字典
- not in 表示: 是否不在,可用于:字符串\列表\元组\字典

公共函数:

- len() 容器长度

- del 删除

- max() 返回最大值
- min() 返回最小值
- range(start.end,step) 从生成 start 到 end 的数字,步长为 setp ,供 for 循环使用
- enumerate() 将一个可以便利的数据对象(如 列表\元组或者字符串) 组合为一个索引序列,同时列出的数据和下标

```python
list1 = [1,2,3]
print(enumerate(list1))
for index,item in enumerate(list1):
    print(index,item)
    
"""
<enumerate object at 0x000001F14318C630>
0 1
1 2
2 3
	
"""
```

---

## 推导式(类似于JAVA的**Lambda**)

- 格式
  - 变量名 = [变量名 for ... in ... if 判断条件]
  - 变量名 = {变量名 for ... in ... if 判断条件}
  - 变量名 = {变量名1:变量名2 for ... in ... if 判断条件}

---

## 函数

格式

```python
def 函数名(形参1,形参2):
    return
```

关键词参数:

```python
def fun(name,age,sex):
    return 
fun(name="xxx",age="10",sex='nan')
```

省略关键词：

```python
def fun(name,sex,age=10):
	return
fun("xxx","男")
```

不定长参数:

```python
"""
	*args 只能接收所有的 位置参数,封装到: 元组中
	**kwargs 只能接收所有的 关键字参数,封装到:字典中
	格式:
		def fun(*args):
			print(*args)
		def fun(**kwargs):
			print(**kwargs)
	细节:
		关于实参,位置参数在前,关键字参数在后
		关于形参,如果两种 可变参数都有,则 *args在前,**kwargs在后
		关于行咱,如果既有 缺省参数 又有不定长参数 则编写顺序为 *args,缺省参数,**kwargs
"""
```

---

## 组包&拆包(等于JS析构语法)

```python
"""
格式:
	把 多个值 => 1个变量的过程 为组包
	把 1个(容器)变量 => 多个变量值的过程 为拆包
应用场景:
	1. 一次性获取到 元组,列表,字典中的每个数据
	2. 交换变量
"""
# 组包
list1 = [1,2,3]
tuple1 = (1,2,3)
dict1 = {'a':1,'b':2}
# 列表拆包
a,b,c = list1
# 元组拆包
x,y,z = tuple1
# 字典拆包
k1,k2,k3 = dict1
```

---

## 匿名函数

```python
"""
格式:
	变量名 = lamdba 形参列表:return 返回值
"""
test = lanbda a,b:a+b
print(test(1,2))
```

---

## 读&写&备份文件

```python
"""
文件打开
	open("url","打开模式",码表)
文件读取
	read(num) 一次读取 num 字节,不写都全部
	readline() 一次读一行
	readlines() 读全部,且会把每行数据封装的列表中
关闭文件
	文件对象.close()
"""
f = open("./files/b.txt","r",encoding="utf-8")
print(f'文件对象{f}')
print(f.read())
f.close()

"""
写文件
	write(data) 文件中写数据
	writelines() 一次写多行
细节
	write 覆盖写
	读的时候,如果文件不存在则报错
	写的时候,如果目的地文件不存在,会自动创建
"""
f = open("./files/c.txt","w",encoding="utf-8")
f.write("hello world")
f.write("hello python")
f.close()
"""文件备份"""
targetF = open("./files/b.txt","rb")
fileData = targetF.readlines()

writeF = open("./files/D.txt","wb")

for line in fileData:
    writeF.write(line)

writeF.close()
targetF.close()

```

扩展 with-open 语句

```python
"""
主要用于对文件的操作,即 不需要手动 close() 释放资源,该语句执行完成后,自动释放
格式:
	with open("路径","模式","码表") as 别名:
		语句体
"""
with  open("./files/b.txt","rb") as  read_f , open("./files/E.txt","wb") as write_f:
    write_f.write(read_f.read())
```

---

## 捕获异常

```python
"""
    基本句法
        try:
            可能出问题的语法
        except Exception as err:
            出问题后的解决方案
        else:
            如果没出问题执行的语法
        finally:
            怎么都会执行的语法

"""
```

---

## 模块导入

```python
"""
    导入方式
        import 模块名                          后续通过 模块名.函数名() 调用
        import 模块名 as 别名                   后续通过 别名.函数名() 调用
        from 模块名 import 函数名               后续通过 函数名() 调用
        from 模块名 import 函数名 as 别名        后续通过 别名() 调用
        from 模块名 import *                   后续通过 函数名() 调用

    细节:
        1. 1个 .py 文件可以看做一个模块，文件名 = 模块名。所以：文件名也要符合的命名规范
        2. __name__属性，当前模块打印的结果是 __main__，在调用这中打印的结果是：调用的模块名
        3. 如果导入的多个模块中，有同名函数，默认会使用 最后导入的 模块的函数
        4. __all__ 属性只是针对 from 模块 import* 这种写法有效，他只会导入 __all__ 记录的内容

"""
```

创建自定义包

```python
"""
    包:
        概述
            包 = 文件夹 = 一堆的 .pu 文件(模块) + __init__.py.初始化文件

"""
```

---

## os模块

```python
"""
os 模块介绍:
    概述:
        全称: Opearting System 系统模块,主要是操作 文件夹\文件\路径等
    常用函数:
        getcwd() 获取当前的工作空间目录
        chdir() 改变工作空间路径
        rmdir() 删除文件夹,必须是空文件
        rename() 改名,文件/目录都可以
        listdir() 获取指定目录下 所有的子集文件或者文件夹(不包括子集的子集)
"""
```

---

## pymysql

```python
"""
    pymysql
        操作步骤
            1. 获取连接对象. python 连接 MySQL 的对象
            2. 获取游标对象  可以执行 SQL 语句的对象


"""
import pymysql

conn = pymysql.connect(
    host='localhost',
    port=3306,
    user='root',
    passwd='gong93692',
    database='world',
    charset='utf8'
)

#  游标对象
cus = conn.cursor()

# 执行SQL
sql = 'select * from city'
cus.execute(sql)

# 操作结果集
data = cus.fetchall()
print(data)

cus.close()
conn.close()
```

