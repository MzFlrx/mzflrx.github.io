# 8.1 为什么要使用泛型程序涉及
泛型程序涉及（generic programming）意味着编写的代码可以对多种不同类型的对象重用。
## 8.1.1 类型参数的好处
## 8.1.2 谁想成为泛型程序员
# 8.2 定义简单泛型类
```java
public class Pair<T, U> {
    private T first; //uses the type varibale
    ...
}
```
# 8.3 泛型方法
泛型方法和C++不同，C++将尖括号放在类型名后面，Java放在前面。
```java
class ArrayAlg{
    public static <T> T getMiddle(T... a){
        return a[a.length / 2];
    }
}
```
# 8.4 类型变量的限定
某些情况下，泛型类中，会使用某些泛型方法，如何确定泛型拥有该方法？
解决这个问题的办法是限制T只能是实现了Comparable接口（包含一个方法compareTo的标准接口）的一个类。
```java
public static <T extends Comparable> T min(T[] a)...
```
> C++中，不能对模板参数的类型加以限制。如果程序员用一个不恰当的类型实例化一个模板，将会在模板代码中报告一个（通常含糊不清的）错误消息。

一个类型变量活通配符可以又多个限定，例如：
```java
T extends Comparable & Serializable
```
按照Java继承机制，可以根据需要拥有多个接口超类型，但最多又一个限定可以是类。限定类必须是限定列表的第一个。
```java
package pair2;

import java.time.*;

/**
 * @version 1.02 2015-06-21
 * @author Cay Horstmann
 */
public class PairTest2
{
   public static void main(String[] args)
   {
      LocalDate[] birthdays = 
         { 
            LocalDate.of(1906, 12, 9), // G. Hopper
            LocalDate.of(1815, 12, 10), // A. Lovelace
            LocalDate.of(1903, 12, 3), // J. von Neumann
            LocalDate.of(1910, 6, 22), // K. Zuse
         };
      Pair<LocalDate> mm = ArrayAlg.minmax(birthdays);
      System.out.println("min = " + mm.getFirst());
      System.out.println("max = " + mm.getSecond());
   }
}

class ArrayAlg
{
   /**
      Gets the minimum and maximum of an array of objects of type T.
      @param a an array of objects of type T
      @return a pair with the min and max value, or null if a is 
      null or empty
   */
   public static <T extends Comparable> Pair<T> minmax(T[] a) 
   {
      if (a == null || a.length == 0) return null;
      T min = a[0];
      T max = a[0];
      for (int i = 1; i < a.length; i++)
      {
         if (min.compareTo(a[i]) > 0) min = a[i];
         if (max.compareTo(a[i]) < 0) max = a[i];
      }
      return new Pair<>(min, max);
   }
}
```
# 8.5 泛型代码和虚拟机
## 8.5.1 类型擦除
无论何时定义一个泛型类，都会自动提供一个相应的原始类型。这个原始类型的名字即使去掉类型参数后的泛型类型名。类型编号会被擦除，并替换为其限定类型，或者对于无限定的变量则替换为Object。
```java
// 以下类中，T被Object替换
public class Pair{
    private Object first;
    private Object second;

    public Pair(Object first, Object second){
        this.first = first;
        this.second = second;
    }

    public Object getFirst(){
        return first;
    }
    public Object getSecond(){
        return second;
    }

    public void setFirst(Object newValue){first = newValue;}
    public void setSecond(Object newValue)(second = new Value;)
}
```
## 8.5.2 转换泛型表达式
编写一个泛型方法调用时，如果擦除了返回类型，编译器会插入强制类型转换。
```java
Pair<Employee> buddies = ...;
Employee buddy = buddies.getFirst();
```
编译器会把这个方法调用转换为两条虚拟指令：
* 调用原始方法Pair.getFirst。
* 将返回的Object类型强制转换为Employee类型。
访问一个泛型字段也会插入强制类型转换。

## 8.5.3 转换泛型方法
当出现需要转换的泛型方法时，为避免出现冲突。会生成桥方法，桥方法只有返回值类型不同。程序员不能写参数类型一致，名称一致，但是返回类型不同的方法，但是虚拟机可以生成。
有协变的返回类型（convariant return type）。
* 虚拟机中没有泛型，只有普通的类和方法。
* 所有的类型参数都会替换为他们的限定类型。
* 合成桥方法来保持多态。
* 为保持类型安全性，必要时会插入强制类型转换。

## 8.5.4 调用遗留代码
java.lang.reflect包中有以下类型，还有更多类型未列出：
* Class类，描述具体类型。
* TypeVaribale接口，描述类型变量（如`T extends Comparable<? super T>`）。
* WidlcardType接口，描述通配符（如`? super T`）。
* ParameterizedType接口，描述泛型类或接口类型（如`Comparable<? super T>`）。
* GenericArrayType接口，描述泛型数组（如`T []`）。


# 8.6 限制与局限性
## 8.6.1 不能用基本类型实例化类型参数
## 8.6.2 运行时类型查询只适用于原始类型
```java
Pair<String> stringPair = ...;
Pair<Employee> employeePair = ...;
if (stringPair.getClass() == employeePair.getClass()) // they are equal
```

## 8.6.3 不能创建参数化类型的数组
```java
var table = new Pair<String>[10]; // ERROR
```
需要说明的是，只是不允许创建这些数组，而声明类型为Pair<String>[]的变量仍是合法的。不过不能用new Pair<String>[10]初始化这个变量。

## 8.6.4 Vararg警告
```java
public static <T> void addAll(Collection<T> coll, T... ts){
    for(T t: ts) coll.add(t);
}
```
Java虚拟机会建立一个Pair<String>数组。会得到一个警告，而不是错误。
可以有两种方式来抑制该警告：
* 为addAll调用方法增加注解`@SuppressWarnings("unchecked")`
* 或者使用`@SafeVarargs`直接注解addAll方法。
`@SafeVarargs`只能用于声明为static、final或private的构造器和方法。

## 8.6.5 不能实例化类型变量
不能在类似new T(...)的表达式中使用类型变量。
Java8之后，最好的解决办法是让调用者提供一个构造器表达式。
例如：
```java
Pair<String> p  = Pair.makePair(String::new);

public static <T> Pair<T> makePair(Supplier<T> constr){
    return new Pair<>(constr.get(), constr.get());
}

public static <T> Pair<T> makePair(class<T> cl){
    try{
        return new Pair<>(cl.getConsturctor().newInstance(), cl.getConstructor().newInstance());
    }catch(Exception e){
        return null;
    }
}
```
## 8.6.7 泛型类的静态上下文中类型变量无效
```java
public class Singleton<T>
{
    private static T singleInstance; // ERROR
    public static T getSingleInstance() //ERROR
    {
        if (singleInstance == null ) construct new instance of T
        return singleInstance;
    }
}
```
以上内容行不通。
类型会擦除。Singleton<Random>、Singleton<JFIleChooser>会冲突。

## 8.6.8 不能抛出或捕获泛型类的实例
```java
public class Problem<T> extends Exception { /* ... */}
    // ERROR -- can't extend Throwable
```
catch 子句中不能使用类型变量，但是可以将泛型变量抛出。

## 8.6.9 可以取消对检查型异常的检查
可以利用泛型机制取消对检查型异常的检查。
```java
@SuppressWarning("unchaecked")
static <T extends Throwable> void throwAs(Throwable t) throws T
{
    throw (T) t;
}
```
对于Runnable的run方法，不允许抛出任何异常。
```java
interface Task
{
    void run() throws Exception;

    @SupressWarnings("unchecked")
    static <T extend Throwable> void throws(Throwable t) throws T
    {
        throw (T) t;
    }

    static Runnable asRunnable(Task task)
    {
        return() ->
        {
            try {
                task.run();
            }catch (Exception e){
                Task.<RuntimeException> throwAs (e);
            }
        }
    }
}
```
抛出异常，并“哄骗”编译器，让他相信这不是一个检查型异常。
```java
public class Test{
    public static void main(String[] args){
        var thread = new Thread(Task.asRunnable(()->
        {
            Thread.sleep(1000);
            System.out.println("Hello, Wrold!");
            throw new Exception("Check this out!");
        }));

        thread.star();
    }
}
```
上面的线程中，会抛出检查型异常，但是因为通过泛型处理了Exception就变成了非检查异常。
通过使用泛型类、擦除和@SuppressWarnings注解，我们就能消除Java类型系统的一个基本限制。


## 8.6.10 注意擦除后的冲突

泛型规范知乎了另外一个规则：“为了支持擦除转换，我们要世家一个限制；倘若两个接口类型是同一接口的不同参数化，一个类或类型变量就不能同时作为这两个接口类型的子类。
以下代码非法：
```java
class Employee implements Comparable<Emloyee> {...}
class Manager extends Employee implements Comparable<Manager> { ... } // ERROR
```

# 8.7 泛型类型的继承规则
```java
Pair<Employee> buddies = new Pair<Manager>(ceo, cfo); // illegal
```
一般来讲，无论S与T有什么关系，Pair<S>与Pair<T>都没有任何关系。

# 8.8 通配符类型
```java
public static void printBuddies(Pair<Employee> p){
    Employee first = p.getFirst();
    Employee first = p.getSecond();
    System.out.println(first.getName() + " and " + second.getName() + " are buddies.");
}
```
通配符类型：
```java
public static void printBuddies(Pair<? extends Employee> p)
```
限制：对于这样的p对象，只能使用get方法，不能使用set方法，因为不确定p究竟是`