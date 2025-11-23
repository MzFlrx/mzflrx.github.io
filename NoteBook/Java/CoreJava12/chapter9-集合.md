<!--
 * @Author: MzFlrx lifangyi01@outlook.com
 * @Date: 2025-11-23 22:03:35
 * @LastEditors: MzFlrx lifangyi01@outlook.com
 * @LastEditTime: 2025-11-23 23:17:51
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

