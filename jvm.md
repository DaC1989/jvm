# **关于jvm的知识**

## java编译

### 分解释执行和即时编译（Just In Time compilation,JIT）

解释执行，逐条将字节码翻译成机器码并执行；优点，无需等待编译时间。

即时编译，将一个方法里包含的所有字节码翻译成机器码再执行；优点，实际运行更快。

## java类加载

从class文件，到内存中的类，按先后顺序，需要经过**加载、链接、初始化**三步。

**加载**，指查找字节流，并据此创建类。

加载，由类加载器完成。分为启动类加载器（boot class loader）、扩展类加载器（extension class loader）、应用类加载器（application class loader）。以及自定义类加载器。

每当一个类加载器收到加载请求，会先将请求转给父级类加载器，判断有无，再决定是否加载。

![](C:\Users\Administrator\Desktop\jvm.jpg)

类的唯一性，由类加载器和类的全名一起确定。

加载机制为“双亲委派”。其实就叫“委派”比较合适。

**链接**，略过。

**初始化**，为标记为常量值的字段赋值，以及执行 < clint >  方法。在java中，被static final 修饰的字段，且其类型为基本类型或字符串时，称为常量值（ConstantValue），常量值的赋值操作，由java虚拟机直接完成；除此之外的直接赋值操作，以及所有静态代码块中的代码，则会被放入< clint >  方法中。

类的初始化时间：

​	1.当虚拟机启动时，初始化用户指定的主类；

​	2.new 实例时，初始化new的目标类；

​	3.访问静态方法或静态字段时，初始化静态方法或静态字段所在的类；

​	4.子类的初始化会触发父类的初始化；

​	5.如果一个接口定义了default 方法，那么直接或者间接实现该接口的类的初始化，也会触发该接口的初始化；

​	6.对某个类进行反射调用时，会触发该类的初始化；

​	7.当初次调用MethodHandle实例时，会初始化该MethodHandle指向的方法所在的类。

```java
public class Singleton{
    private SingleTon(){}
    private static class LazyHolder{
        static final SingleTon INSTANCE = new SingleTon();
    }
    public static SingleTon getInstance(){
        return LazyHolder.INSTANCE;
    }
}
```

上面的代码，只有当调用SingleTon.getInstance 方法时，程序才会访问LazyHolder.INSTANCE，才会触发对LazyHolder的初始化，才会新建一个Singleton的实例。

类的初始化时线程安全的，且仅会被执行一次，因此程序可以保证多线程下的Singleton的单例。

## java异常机制

java异常类：

![](C:\Users\Administrator\Desktop\异常.jpg)

### try-with-resource语法

```java
public static void main(String[] args) {
    try (FileInputStream inputStream = new FileInputStream(new File("test"))) {
        System.out.println(inputStream.read());
    } catch (IOException e) {
        throw new RuntimeException(e.getMessage(), e);
    }
}
```

1.catch 块中，看不到 try-with-recourse 声明中的变量。
2.try-with-recourse 中，try 块中抛出的异常，在 e.getMessage() 可以获得，而调用 close() 方法抛出的异常在3.e.getSuppressed() 获得。try-with-recourse 中定义多个变量时，由反编译可知，关闭的顺序是从后往前。