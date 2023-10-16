# 07. Java多态

父类的引用指向子类的对象

示例:

```java
父类名 对象名 = new 子类名();
父类接口名 对象名 = new 子类名();
```

- 访问变量时，编译和运行时根据等号左边的类来界定对象的类型
- 访问方法时，编辑根据等号左边的类界定对象类型，运行时根据等号右边实例化对象来访问相应的成员方法

完整示例：

```java
public class Animal {
  public String name = "动物";
  public void eat(){
    System.out.println("这是动物类");
  }
}

public class Dog extends Animal{
  public String name = "狗";
  public void eat(){
    System.out.println("这是狗");
  }
}

public class Demo01 {
  public static void main(String[] args) {
    Animal dog = new Dog();
    System.out.println(dog.name); /*动物*/
    dog.eat(); /*这是狗*/
  }
}
```

---

## 向下和向上转型

- 向上转型就是创建子类对象，把他当作父类使用（向上转是安全的，但无法调用子类的特有方法）
- 向下转型就是还原子对象，要使用强制转换的方式（需要使用 **对象名 instanceof 类名** 判断防止出错）

示例：

```java
public abstract class USB {
  public abstract void on();
  public abstract void off();
}

public class Click extends USB {

  @Override
  public void on() {
    System.out.println("usb已插入");
  }

  @Override
  public void off() {
    System.out.println("usb已拔出");
  }

  public void onClick(){
    System.out.println("点击了关机键");
  }
}

public class Demo02 {
  public static void main(String[] args) {
    USB click = new Click();
    if(click instanceof Click){
      ((Click) click).onClick();
    }
  }
}
```

---

## 内部类

简单说就是类的嵌套

示例：

```java
public class 类名 {
    public class 内部类名{
        
    }
}
```

- 内部类可以直接访问外部类的成员，包括私有成员
- 外部类访问内部类的成员，必须要实例化内部类对象

---

## 局部内部类

简单说就是函数里嵌套一个类

- 局部内部类，不用写修饰符

```java
public void 方法名(){
    class 类名{
        public void show(){}
    }
}
```

