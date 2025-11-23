
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