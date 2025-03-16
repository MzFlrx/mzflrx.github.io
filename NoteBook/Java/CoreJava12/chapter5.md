
## 5.3 泛型数组列表

* ArrayList - 自有类型参数的泛型类

在较早的Java版本中，使用vector实现动态数组，不过现在过时了。

* ArrayList.ensureCapacity() - 设置初始大小。

* ArrayList.size() - 返回包含的世纪元素个数。

* ArrayList.trimToSize() - 将内存块的大小调整为保存当前元素数量所需要的存储空间。垃圾回收器将回收多余空间。

ArrayList和C++的vector类似，但是在复制时，C++是按值复制的，Java是引用复制。

* ArrayList.get() - 获取某个元素

* ArrayList.set(i, Object) - 只能设置已经被添加的值的value。

> 没有泛型类时ArrayList类提供的get方法，只能返回Object。因此，get方法的调用者必须将返回值强制类型转换为所需类型。

 * ArrayList.add(i, value) - 在i元素位置后面插入一个元素，其余元素整体后移。

 * ArrayList.remove(n) - 删除第n个元素，后方元素前移。

## 5.7枚举类
比较枚举类，可以直接使用==来比较。

* Enum.toString() - 方法可以获得枚举的名字。

valueOf可以将逆转sting到对应的枚举
```java
Size s = Enum.valueOf(Size.class, "SMALL");
```

* Enum.ordinal() - 返回一个枚举常量在enum声明中的位置。

## 密封类
一个抽象类可以控制哪些类可以继承它，这种方式叫做密封类，Java 15引入。
```Java
public abstract sealed class JSONValue
    permits JSONArray, JSONNumber, JSONString, JSONBoolean, JSONObject, JSONNull
```

### 5.9.7 调用任意方法和构造器

## 5.10 继承的设计技巧
1. 将公共操作和字段放在超类中.
    * 第一, 子类集合是无限制的，任何一个人都能够由你的类派生一个子类，然后编写代码直接访问protected 实例字段，从而破坏封装性。
    * 第二，在Java 中，同一个包中的所有类都可以访问p「otKed 字段，而不论它们是否为这个类的子类口*
2. 不要使用受保护的字段。
3. 使用继承实现“is-a"关系。
4. 除非所有继承的方法都有意义，否则不要使用继承。
5. 覆盖方法时，不要改变预期的行为。
6. 使用多态，而不要使用类型信息。
7. 不要滥用反射。