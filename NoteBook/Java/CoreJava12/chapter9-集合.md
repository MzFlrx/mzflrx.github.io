<!--
 * @Author: MzFlrx lifangyi01@outlook.com
 * @Date: 2025-11-23 22:03:35
 * @LastEditors: MzFlrx lifangyi01@outlook.com
 * @LastEditTime: 2025-12-02 22:47:32
 * @FilePath: \mzflrx.github.io\NoteBook\Java\CoreJava12\chapter9-集合.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
## 9.1 Java集合框架
### 口与实现分离
### 9.1.2 Collection 接口
```java
public interface Collection<E>{
    boolean add(E element);
    Iterator<E> iterator();
    ...
}
```
### 9.1.3 迭代器
### 9.1.4 泛型实用方法
```java
int size()
boolean IsEmpty()
boolean contains (Object obj) 
boolean containsAll(Collection<?> c) 
boolean equals(Object other)
boolean addAll(Collection<? extends E> from) 
boolean remove(Object obj)
boolean removeAll(Collection<?> c) 
void clear()
boolean retainAll(CoUectiorx?> c) 
Object[] toArray()
```
## 9.2 集合框架中的接口

## 9.3 具体集合

ArrayList
LinkedList
ArrayDeque
HashSet
TreeSet
EnumSet
LinkedHashSet
PriorityQueue
HashMap
TreeMap
EnumMap
LinkedHashMap
WeakHashMap
IdentityHashMap
> 上面的集合，都是非同步的。

### 9.3.2 数组列表

*Java中的Vector是同步的，可用于多线程访问。*

### 9.3.3 散列集

### 9.3.4 树集

### 9.3.5 队列与双端队列

### 9.3.6 优先队列

```java
PriorityQueue<>()
```

## 9.4 映射
```java
HashMap
TreeMap
```
### 9.4.2 更新映射条目


### 9.4.3 映射视图
Java中集合框架不认为映射本身是一个集合。遍历需要将其转换成集合再遍历

### 9.4.4 弱散列映射
没有除过map之外的强引用，就会被回收。

### 9.4.5 链接散列与映射
LinkedHashSet和LinkedHashMap类会记住插入元素项的顺序。

### 9.4.6 枚举集与映射
EnumSet内部实现为一个位序列。如果对应的值在集中出现，对应位置为1。
EnumSet类没有公共构造器。要使用静态工厂方法构造这个集。

### 9.4.7 标识散列映射
IdentityHashMap，键对象地址不同，则作为不同的对象，键的值可以相同。

## 9.5 副本与视图

### 9.5.1 小集合
使用静态对象创建Map

```java
import static java.util.Map.*;

Map<String, Integer> scores = ofEntries(
    entry("Peter", 2),
    entry("Paul", 3),
    entry("Mary", 5)
);
```

### 9.5.2 不可修改的副本和视图
建立集合的不可修改副本：copyOf()

建立不可修改视图：
Collection.unmodifiableCollection
...

### 9.5.3 子范围

### 9.5.3 检查型视图

List<String> safeStrings = Collections.checkedList(Strings, String.class);

检查型视图会在插入的时候，检查泛型类内部类型和当前插入类型是否匹配。

### 9.5.5. 同步视图
多个线程访问同一个集合，可以通过`Collections.synchronizedMap(new HashMap<String, Employee>())`将映射转换成有同步访问方法的Map

### 9.5.6. 关于可选操作的说明

## 9.6 算法
标准C++库有十几个有用的算法，每个算法都可以用于处理泛型集合，但是Java的算法没有那么丰富，包含基本算法：排序、二分查找和一些实用算法。

### 9.6.2. 排序与混排

Collections.sort()
Java的排序算法比较慢，对于List，先放到数组中排序，再将排序后的数组复制回列表。
Java集合的排序算法是稳定的。
可以将可修改的列表传递给sort。

定义：
* 如果列表支持set则是可修改的
* 如果列表支持add和remove，则是可改变大小的。

### 9.6.3. 二分查找

### 9.6.4. 简单算法


java.util.Collections 类包含许多简单算法
### 9.6.5. 批操作

Collections.removeAll()
Collections.retrainAll()
Collections.addAll()

### 9.6.6. 集合和数组的转换

数组转换为集合：List.of()
集合转换为数组：String[] names = staff.toArray(String[]::new);

## 9.7. 遗留的集合
### 9.7.1. Hashtable类
Hashtable是遗留类和HashMap类的作用一样，接口也类似。

如果要并发访问，应该使用CuncurrentHashMap

### 9.7.2. 枚举

### 9.7.3. 属性映射
类名：Properties
特性：
* 键与值都是字符串
* 这个映射可以很容易地保存到文件以及从文件加载
* 有一个二级表存放默认值
