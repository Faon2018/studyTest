# set

存储的元素是无序的、不可重复的。继承collection接口

常用API：

| API                                       | 描述                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| boolean add(E e)                          | 添加元素                                                     |
| boolean addAll(Collection<? extends E> c) | 添加集合元素                                                 |
| void clear()                              | 清除集合中的所有元素                                         |
| boolean remove(Object o)                  | 删除集合中的某一个元素，通过元素的equals方法判断是否是要删除的那个元素 |
| boolean removeAll(Collection<?> c)        | 删除多个元素，也就是取当前集合的差集                         |
| boolean isEmpty()                         | 判断集合中的元素是否为空                                     |
| boolean contains(Object o)                | 判断集合中是否包含该元素，是通过元素的equals方法来判断是否是同一个对象 |
| boolean containsAll(Collection c)         | 也是调用元素的equals方法来比较的。拿两个集合的元素挨个比较。 |
| Iterator<E> iterator()                    | 返回迭代器对象，用于集合遍历                                 |
| int size()                                | 获取集合中元素个数                                           |
| Object[] toArray()                        | 集合转换数组                                                 |
| Arrays.asList(T… t)                       | 数组转换集合                                                 |

```java
1.迭代遍历：
Set set = new HashSet();
Iterator it = set.iterator();
while (it.hasNext()) {
String str = it.next();
System.out.println(str);
}

2.for循环遍历：
for (String str : set) {
System.out.println(str);
}
```



## HashSet

（无序，唯一）: 基于 `HashMap` 实现的，底层采用 `HashMap` 来保存元素,把数据存在了Key里。`hashcode` 值和` equals()`结果均为true时才认为时一样的元素。

线程不安全的，可以存储 null 值；

使用成员对象来计算 `hashcode` 值，` equals()`方法用来判断对象的相等性，想要让两个不同的Person对象视为相等的，就必须覆盖Object继下来的hashCode方法和equals方法，因为Object hashCode方法返回的是该对象的内存地址，所以必须重写hashCode方法，才能保证两个不同的对象具有相同的hashCode，同时也需要两个不同对象比较equals方法会返回true。

HashSet在第一次调用的时候就是new HashMap();

## LinkedHashSet

`LinkedHashSet` 是 `HashSet` 的子类，并且其内部是通过 `LinkedHashMap` 来实现的

能够按照添加的顺序遍历；

## TreeSet

（有序，唯一）： 红黑树(自平衡的排序二叉树)，以TreeMap作为存储结构

能够按照添加元素的顺序进行遍历，排序的方式有自然排序和定制排序

存入对象时，对象实现comparable接口，重写compareTo(Object obj)方法。或者创建集合时添加comparator接口的匿名实现，并重写其中的compare()方法.

