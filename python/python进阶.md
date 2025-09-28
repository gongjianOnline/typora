# python进阶范式

## 面向对象

```python
class Car:
    def run(self):
        print('run')

    def open(self):
        print('open')
        self.run()

if __name__ == "__main__":
	car = Car()
	car.open()
```

---

## 魔法方法注释

```python
"""
魔法方法注释
	概述
		在 python 中,有一类方法是用来 对 python 类的功能做 加强(扩展)的,且这一来方法都无需手动调用
		在满足特定情况下的场景,会被自动调用
	格式
		__魔法方法名__() 
	常用的魔法方法:
		__init__() 用于初始化对象的属性值，在创建对象的时候，会被自动调用
		__str__() 用于快速打印对象的各个属性值, 在输出语句打印对象的时候会自动调用
		__del__() 当删除对象的时候,或者 main 函数执行完毕后,会自动调用
"""
# 实例代码
class Car:
    def __init__(self,color,number):
        self.color = color
        self.number = number
    def __str__(self):
        return f"{self.color} {self.number}"
    def __del__(self):
        print(f"{self}被释放了")
if __name__ == '__main__':
    car = Car('red',10)
    print(car.color)
    print(car.number)
    print(car)
```

## 继承

```python
"""
python继承
	格式:
		class 子类名(父类名):
			pass
	例如:
		class A(B):
			pass
	叫法:
		类A: 子类\派生类\扩展类
		类B: 父类\基类\超类
	好处:
		提高代码的复用性
	细节:
		所有的类都直接或间接继承自 Object 类,它是所有类的父类,基类
"""
```

```python
"""
多继承
	格式:
		class P1:
		class P2:
		class C1 (P1,P2):
	细节:
		如果一类继承了多个类出现重复的方法和属性,优先继承第一个父类的同名属性和方法(父类私有成员除外)
"""
```

```python
"""
方法重写(如同 JS 原型链)
	概述:
		子类中出现和父亲一样的属性和行为(函数)时,称为:方法重写
	应用场景:
		当子类需要从父类沿袭一些功能,但是功能主体又有自己独有需求的时候,就可以考虑使用方法重写
"""
# 示例
class Father(object):
    def __init__(self):
        self.gender = "男"
    def walk(self):
        print("抽烟喝酒烫头")
class Son(Father):
    def __init__(self):
        self.gender = "劳资蜀道山"
if __name__ == "__main__":
    s = Son()
    print(s.gender)
    s.walk()
```

```python
"""
	问题: 重写后,子类,如何访问父类的成员
	答案:	
		方式一: 父类名.父类方法名(self)
		方式二: super().父类方法名()
	super 关键字介绍:
		概述:
			他代表 本类当前对象 父类的引用
		简单理解:
			self 代表字节 super 代表父类
		细节:
			super() 只能初始化一个父类的成员,所以 super 写法不适用于多继承
			在单继承关系中,可以把 super().父类方法名(self) 简写成 super.父类方法名()
			多继承关系中,如果想精确的初始化某个父类成员,要通过 父类名.父类方法名(self)的方式实现
"""

"""
	私有化属性:
		私有化 __变量/方法名
	公共访问:
		通过提供的对外访问内部成员的接口(方式) 例如 getxxx(),setxxx();
"""
 # 示例
 class Parent:
    def __init__(self):
        self.skill  = "大招"
        self.__money = "2000"

    def getMoney(self):
        return self.__money
    def setMoney(self, money):
        self.__money = money

class Child(Parent):
    def getSkill(self):
        print(Parent().skill)
    def getMoney(self):
        print(super().getMoney())


if __name__ == '__main__':
    c = Child()
    c.getSkill()
    c.setMoney(100)
    c.getMoney()
```

---

## 多态（同JS）

多态介绍：

- 概述：

  - 多态指的是同一个事务在不同场景下表现的不同形态/状态

  - python 中的多态指的是，同一个函数，传入不同的对象，会实现不同的结果

- 多态的前提：
  - 要有继承关系
  - 要有方法重写
  - 要有弗雷引用指向子类对象
- 好处
  - 提高代码的可维护性，实现 1 个函数多种效果
- 应用场景：
  - 父类冲到函数形参的类型，这样可以接受其任意子类型对象，实现：传入什么（子类）对象，就调用其对应的功能

```python
# 案例 动物类案例
class Animal:
    def __init__(self):
        self.skill = ""
    def speak(self):
        print("叫")

class Dog(Animal):
    def __init__(self):
        self.skill=""
    def speak(self):
        print("王")

class Cat(Animal):
    def __init__(self):
        self.skill=""
    def speak(self):
        print("秒 ")


def make_noise(an:Animal):
    an.speak()



if __name__ == '__main__':
    d = Dog()
    c = Cat()
    make_noise(d)
```

---

## 抽象类：（同JS）

- 概述
  - 在 python 中，抽象类姐叫做接口，指的是：有的抽象方法的类，就叫做抽象类
- 抽象方法
  - 没有方法体的方法 即：空实现的方法，用 pass 修饰，就叫做抽象方法
  - 例如：
    - `get fun(): pass`
  - 用法：
    - 抽象类一般充当父类，即：指定整个继承体系的 标准（规范）
    - 具体的体现，实现交由 子类完成
- 类属性和对象属性
  - 概述：
    - 属性 = 名词，就是描述事物的外在特征
  - 分类：
    - 对象属性：数据每个对象的属性，即：A对象修改了他的属性值，不会影响 B 对象的属性值
    - 类属性：属于该类的所有对象，所有共享的，即：A对象修改类属性，B对象用的也是修改后的属性值
  - 对象属性格式：
    - 定义格外：
      - 类外：对象名.属性名 = 属性值
      - 类内：self.属性名 = 属性值
  - 类属性：
    - 定义：
      - 定义来类中的，函数外的变量，也叫做类变量，他能被该类似的所有对象共享
    - 调用格式：
      - 方式一：类名.类属性
      - 方式二：对象名.类属性名
    - 细节
      - 什么时候定义类属性，啥时候定义对象属性
        - 1 个变量是被该类下的所有对象共享定义成类属性

---

## 类方法和静态方法详解

- 概述：
  - 类方法：	
    - 第一个参数必须是 当前类的对象，一般用 cls 当作变量名（即：class）
    - 类方法必须通过 @classmethod 类修饰
    - 类方法是数属于 类的方法 ， 能被该类的所有对象共享
    - 可以通过 类名，或者 对象名 的方式调用，推荐：前者
  - 静态方法
    - 静态方法没有参数的硬性要求
    - 静态方法必须通过 @staticmethod 来修饰
    - 静态方法内部的函数体中，访问不到任何类内的共享属性和函数
    - 可以通过类名，或者 对象名 的方式调用，推荐：前者

```python
class MyClass:
    class_variable = 0
    def __init__(self):
        self.instance_variable = 1

    @classmethod
    def class_method(cls):
        print("class_method",cls.class_variable)

    @staticmethod
    def static_method():
        print("static_method")

if __name__ == '__main__':
    MyClass.static_method()
    obj = MyClass()
    obj.class_method()
    obj.static_method()
```

---

## 闭包

- 闭包：
  - 概念即原理和 JS 一直
- 区别：
  - nonlocal 关键字介绍
    - 是一个关键字，可以实现 让内部函数 去修改 外边函数的变量值
- 回顾：
  - global 声明变量为全局变量
  - nonlocal 声明变量可以被内部函数修改

```python
def fn_outer():
    a = 100
    def fn_inner():
        nonlocal a # 闭包关键字
        a = a + 1
        print(a)
    return fn_inner
if __name__ == "__main__":
    fn = fn_outer()
    fn()
    fn()
```

---

## 装饰器

- 概念
  - 装饰器 = 闭包函数 即装饰器是闭包函数的一种写法
- 目的及作用
  - 在不改变原有函数的基础上，对原有函数功能做 增强
- 前提作用：
  - 有嵌套
  - 有引用
  - 有返回
  - 有额外的功能
- 装饰器的用法：
  - 写法1：传统写法
    - 变量名 = 装饰器名（要被装饰的原函数名）
    - 变量名() `执行的就是装饰器后的 原函数`
  - 写法2: 语法糖
    - 在定义原函数的时候,加上 @ 装饰器名即可,之后就正常调用  改原函数即可

```python	
def check_login(call_back):
    def inner():
        print("登录中,,,登录完成")
        call_back()
    return inner

@check_login
def commit():
    print("评论成功")

if __name__ == "__main__":
    # checkLogin(commit)

    # 方式一
    # check_login(commit)()
    # 方式二
    commit()
```

### 装饰器传参情况

无参数有返回值

```python
def check_log(call_back):
    def inner():
        print("登录中")
        call_back()
        return print("登录成功")
    return inner

@check_log
def commit():
    print("评论")


if __name__ == '__main__':
    commit()
```

有参数有返回值

```python
def check_log(call_back):
    def inner(a,b):
        print("登录中")
        call_back(a,b)
        return print("登录成功")
    return inner

@check_log
def commit(a,b):
    print("评论",a+b)

if __name__ == '__main__':
    commit(1,2)
```

可变参数

```python
def check_log(call_back):
    def inner(*arg,**kwargs):
        print("登录中")
        call_back(*arg,**kwargs)
        return print("登录成功")
    return inner

@check_log
def commit(*arg,**kwargs):
    print("评论",arg,kwargs)
    arg_sum = 0
    kwargs_sum = 0
    for item in arg:
        arg_sum += item
    for item in kwargs:
        kwargs_sum += kwargs[item]

    print(arg_sum, kwargs_sum)
if __name__ == '__main__':
    commit(1,2,3,a=1,b=2,c=3)
```

### 多个装饰

多个装饰器装饰一个函数,细节如下:

- 多个装饰器 装饰一个函数 装饰的顺序是 由内向外的
- 但是多个装饰器的执行顺序是,由上往下的

```python
# 需求: 发表评论只,需要 先登录用户.在进行验证码验证,在不改变原有函数基础上,对功能做增强
def check_login(call_back):
    def inner():
        print("登录")
        call_back()
    return inner

def check_code(call_back):
    def inner():
        print("验证")
        call_back()
    return inner

@check_login
@check_code
def commit():
    print("评论")

if __name__ == '__main__':
    commit()
    
"""
登录
验证
评论
"""
```

---

## scoket协议

示例

```python
import socket
"""
参数1: Address Family 地址族,即: 表示用何种 IP 规则来解析,AF_INET 代表 IPV4
参数2: 表示传输方式 Stream(流) , 用字节流(二进制形式)传输数据
"""
cli_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
cli_socket.connect(('127.0.0.1', 3000))
cli_socket.close()
```

### 字符串和二进制数据相互转换

- 格式:
  - 字符串.encode(encoding="码表")
  - 二进制字符串.decode(encoding="码表")
- 细节:
  - 编解码时,码表要一致,否则可能出现 乱码的情况
  - 数字,英文字母,特殊符号,无论在什么码表中,都只能占一个字节
    - 中文在 GBK码表(国内常用)中占两个字节,在 utf8 中占3个字节
  - b"内容" 这种写法是二进制形式的字符串,只针对: 数字,特殊字符有效,中文无效

```python	
print("你好".encode("utf-8").decode("utf-8"))
```

### socket基础实例

客户端代码

```python
import socket

client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client_socket.connect(("10.1.68.179",12306))

recv_data_bytes = client_socket.recv(1024)
recv_data = recv_data_bytes.decode()
print("客户端收到",recv_data)

client_socket.send("卷起来".encode())

client_socket.close()
```

服务端代码实现

```python
import socket

server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# 绑定 IP 和 端口号
server_socket.bind(("10.1.68.179",12306))
# 设置最大监听数(允许挂载,挂起的数量)
server_socket.listen(5)
# 具体的监听动作,接受客户端信息,并获取 1 个socket对象,负责和客户端交互
# accept_socket 负责和客户端交互的对象
# client_info 客户端的IP信息
print(1)
accept_socket,client_info = server_socket.accept()
print(2,client_info)
# 给客户端法一句话,二进制形式
accept_socket.send("干啥呢".encode('utf-8'))
# 接受客户端信息
# 一次性接受客户端数据的1024字节,超出则无法接受
recv_data_bytes = accept_socket.recv(1024)
recv_data = recv_data_bytes.decode('utf-8')
print("服务器端打印",recv_data)

# 和客户端交互的 scoket 关闭
accept_socket.close()
```

端口复用

```python
"""
服务端端口复用
    当客户端和服务端建立连接后,服务端退出后端口号不会立即释放,需要等待大概1-2分钟
    格式:
        server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        ...
        server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
    细节:
        SOL_SOCKET      当前的 scoket 对象
        SO_REUSEADDR    端口重用(属性名)
        True 成立(属性值)
"""
import socket
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# 绑定 IP 和 端口号
server_socket.bind(("10.1.68.179",12306))
# 设置最大监听数(允许挂载,挂起的数量)
server_socket.listen(5)
# 具体的监听动作,接受客户端信息,并获取 1 个socket对象,负责和客户端交互
# accept_socket 负责和客户端交互的对象
# client_info 客户端的IP信息
print(1)
accept_socket,client_info = server_socket.accept()
print(2,client_info)
# 给客户端法一句话,二进制形式
accept_socket.send("干啥呢".encode('utf-8'))
# 接受客户端信息
# 一次性接受客户端数据的1024字节,超出则无法接受
recv_data_bytes = accept_socket.recv(1024)
recv_data = recv_data_bytes.decode('utf-8')
print("服务器端打印",recv_data)
# 和客户端交互的 scoket 关闭
accept_socket.close()
# 设置端口号适用
server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
```

接受多客户端信息

```python
import socket

server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# 绑定 IP 和 端口号
server_socket.bind(("10.1.68.179",12306))
# 设置最大监听数(允许挂载,挂起的数量)
server_socket.listen(5)

while True:
    # 具体的监听动作,接受客户端信息,并获取 1 个socket对象,负责和客户端交互
    # accept_socket 负责和客户端交互的对象
    # client_info 客户端的IP信息
    accept_socket,client_info = server_socket.accept()
    # 给客户端法一句话,二进制形式
    accept_socket.send("干啥呢".encode('utf-8'))
    # 接受客户端信息
    # 一次性接受客户端数据的1024字节,超出则无法接受
    recv_data_bytes = accept_socket.recv(1024)
    recv_data = recv_data_bytes.decode('utf-8')
    print("服务器端打印",recv_data)

    # 和客户端交互的 scoket 关闭
    accept_socket.close()
```

## 多任务

- 概述:
  - 多任务指的是 多任务的执行方式 按照执行方式不同 分为:并行和并发
- 面试题: 并行和并发的区别
  - 并发: 多个任务同时请求执行,但是同意瞬间 CPU只能执行 1 个任务,于是安排他们交替执行, 看起来好像是同步执行的 其实不是 (CPU 在做着高效的切换)
  - 并行: 多个任务同时执行,前提 需要多核CPU
- 线程和进程的区别
  - 进程：指的是 可执行文件 如 exe 1个 = 1个进程
  - 线程：值得是 进程的执行路径 执行的单元
  - 便于理解
    - 车 = 进程 多辆车 = 多进程
    - 车道 = 线程 单车道 = 单进程

### 多进程

- 进程介绍：
  - 进程值得是（Process），它是由 CPU 分配资源的最小单位 例如 CPU给微信多少
  - 多进程的基本工作方式： 1 个 exe = 1 个进程，程序运行起来形成主进程，在主进程上创建子进程
- 实现步骤：
  - 导包 `import multiprocessing`
  - 创建进程对象，关联：要执行的任务 `p1 = multiprocessing.Process()`
  - 开启进程 `p1.start()`

```python
# 案例: 使用多进程模拟一边写代码一边听音乐
import time
import multiprocessing

def codeing():
    for i in range(10):
        print(f"写代码...{i}")
        time.sleep(1)

def music():
    for i in range(10):
        print(f"听音乐。。。{i}")
        time.sleep(1)

if __name__ == '__main__':
    # codeing()
    # music()

    p1 = multiprocessing.Process(target=music)
    p2 = multiprocessing.Process(target=codeing)
    p1.start()
    p2.start()
```

进程涉及到的参数如下:

​	target 关联的是  当前进程要执行的函数

​	name 设置当前进程的名字

​	args 以元组的形式 给 当前进程关联的函数传参

​	kwargs 以字典的形式 给当前进行关联的函数传参

- 细节:
  - args 方式传参, 实参的个数和数据类型 \ 顺序 必须和 进程关联的函数形参列表一致
  - kwargs 方式传参, 实参的个数 和 数据类型 必须和 进程关联的函数的形参列表一致, 顺序无所谓 

案例

```python
import multiprocessing
import time
def codeing(name, num):
    for i in range(num):
        print(f"{name} 正在写第 {i} 行代码")
        time.sleep(1)

def music(name,num):
    for i in range(num):
        print(f"{name} 正在听第 {i} 首歌")
        time.sleep(1)

if __name__ == '__main__':
    p1 = multiprocessing.Process(target=codeing,name="xiaoming",args=("xiaoming",10))
    p2 = multiprocessing.Process(target=music,name="xiaomi",kwargs={"name":"xiaomi","num":10})
    print(p1.name,p2.name)
    p1.start()
    p2.start()
```

获取进程编号:

细节:

- 1 个进程拥有一个唯一的 进程id 当该进程被关闭的时候 进程 id 会同步释放,即: 进程 id 是可以重复使用的
- 知道了进程 ID 就可以锁定到唯一的进程 方便我们管理和维护,以及梳理 子进程 和 父进程之间的关系
- 获取当前进程的 id 有两种方式
  - 方式1: os 模块 getpid() 函数
  - 方式2: multiprocessing.current_process().pid 模块的 pid 属性
- 获取当前进程的父 id ,方式如下:
  - os 模块的 getppid() 函数

```python
import multiprocessing
import time
import os
def codeing(name, num):
    print("codeing的pid",os.getpid(),"父进程",os.getppid())
    for i in range(num):
        print(f"{name} 正在写第 {i} 行代码")
        time.sleep(1)

def music(name,num):
    print("music的pid", multiprocessing.current_process().pid,"父进程",os.getppid())
    for i in range(num):
        print(f"{name} 正在听第 {i} 首歌")
        time.sleep(1)

if __name__ == '__main__':
    p1 = multiprocessing.Process(target=codeing,name="xiaoming",args=("xiaoming",10))
    p2 = multiprocessing.Process(target=music,name="xiaomi",kwargs={"name":"xiaomi","num":10})
    print(p1.name,p2.name)
    p1.start()
    p2.start()
```

多进程-进程间相互隔离

```python	
"""
细节:
    1. 进程之间数据是相互隔离的 不能共享
    2. 子进程相当于父进程的副本,即 把父进程的内容拷贝一遍单独执行,注意 main外资源
    3. 默认情况下 主进程会等待子进程结束后再结束
"""

# 案例: 在不同进程中修改列表 my_list[] 并新增元素,设置在个进程中观察列表的变化
import time
import multiprocessing

my_list = []
def write_data():
    for i in range(10):
        my_list.append(i)
        print(f'add:{i}')
    print(f"写入结果:{my_list}")

def read_data():
    time.sleep(1)
    print(f'读取结果:{my_list}')


if __name__ == '__main__':
    p1 = multiprocessing.Process(target=write_data)
    p2 = multiprocessing.Process(target=read_data)

    p1.start()
    p2.start()
```

进程守护

当 主进程结束的时候 如果想让 子进程同步结束 方式如下

方式一: 设置 子进程为 守护进程(推荐)

​	格式: 

​		子进程名.daemon = True

​	特点:

​		当非守护进程关闭的时候,他的守护进程也会被同步关闭

方式二: "强制"销毁子进程

​	格式:

​		进程变量.terminate()

​	可能会出现问题.子进程会变成 "僵尸进程",即资源不会被释放,而是交给 init 进程来维护管理,在合适的时机释放资源

​	子进程 => main进程 (main进程关闭后,内部的正在执行的子进程会变成僵尸进程,僵尸进程会交给 init 管理)	

​	僵尸进程 => init 进程

```python
import multiprocessing
import time
# 需求: 创建 1 个子进程,执行完大概需要 2 秒. 而主进程执行需要1秒,实现该需求,观察结果
def work():
    for i in range(100):
        print(f"work {i} ...")
        time.sleep(1)

if __name__ == "__main__":
    p1 = multiprocessing.Process(target=work)
    # 方式1：设置子进程为守护进程，当非守护进程（main）结束的时候，守护进程会同步结束
    # p1.daemon = True
    p1.start()
    time.sleep(2)
    # 方式2：销毁子进程
    p1.terminate()
```

---

### 多线程

多线程实现步骤(步骤和参数和进程一致)

- 导包 `import threading`
- 创建线程对象,关键函数 `t1 = threading.Thread(target=目标函数)`
- 开启线程 `t1.start()`

参数说明:

​	target: 目标函数

​	name: 线程名称

​	args: 元组参数

​	kwargs: map参数

```python
# 需求: 使用多线程模拟-遍写代码,一边投影云月
import threading
def codeing():
    for i in range(10):
        print("正在写代码")

def music():
    for i in range(10):
        print("正在听音乐")

if __name__ == '__main__':
    p1 = threading.Thread(target=codeing)
    p2 = threading.Thread(target=music)
    p1.start()
    p2.start()
```

线程守护 (执行逻辑和进程一致)

​	主进程会等待所有子进程执行结束在结束

​	守护主线程就是主线程退出子线程销毁不在执行

​	格式:

​		方式一:(推荐) `threading.Thread(target=函数名,daemon=True)`

​		方式二:(废弃)  `线程对象.setDaemon(True)`

```python
import threading,time
def work():
    for i in range(10):
        print(f"worker {i}")
        time.sleep(1)

if __name__ == "__main__":
    p1 = threading.Thread(target=work,daemon=True)
    p1.start()
    time.sleep(2)
    print("主进程执行结束")
```

多线程见可以共享数据

```python
import threading
# 案例：定义个列表类型的全局变量，创建两个子线程分别执行向全局变量添加数据的任务和想全局变量读取的任务，查看线程之间是否共享全局变量数据
import time
my_list = []
def write_data():
    for item in range(10):
        my_list.append(item)
        time.sleep(0.5)

def read_data():
    print(my_list)

if __name__ == "__main__":
    t1 = threading.Thread(target=write_data)
    t2 = threading.Thread(target=read_data)
    t1.start()

    time.sleep(3)
    t2.start()
```

多线程操作共享变量时会出现非法值( 类似于事务的概念 )

解决方法 使用 锁变量 `mutex = threading.Lock()` 在函数里面使用 `mutex.acquire()`



```python
# 导包
import threading

mutex = threading.Lock()
# 定义全局变量
g_num = 0
# 定义 get_sum1() 函数,实现对 g_num 全局变量.累加1000次
def get_num1():
    global g_num
    mutex.acquire()
    for i in range(10000000):
        g_num += 1
    mutex.release()
    print(f"get_num1函数,累加后结果为: {g_num}")

def get_num2():
    mutex.acquire()
    global g_num
    for i in range(10000000):
        g_num += 1
    mutex.release()
    print(  f"get_num2,累加后结果为: {g_num}")


if __name__ == '__main__':
    t1 = threading.Thread(target=get_num1)
    t2 = threading.Thread(target=get_num2)
    # 启动线程
    t1.start()
    t2.start()

```

