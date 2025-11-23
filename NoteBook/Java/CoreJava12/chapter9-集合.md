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
