# List集合

存储的元素是有序的、可重复的。继承collection接口

## RandomAccess接口

标识实现这个接口的类具有随机访问功能。

## Cloneable 接口

实现了 **`Cloneable` 接口** ，即覆盖了函数`clone()`，能被克隆。

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

## ArrayList

`Object[]`数组,适用于频繁的查找工作，

线程不安全,

支持高效的随机元素访问(实现了RandomAccess接口)

能被克隆

在添加大量元素前，应用程序可以使用`ensureCapacity`操作来增加 `ArrayList` 实例的容量。这可以减少递增式再分配的数量

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    /**
     * 默认初始容量大小
     */
    private static final int DEFAULT_CAPACITY = 10;

    /**
     * 空数组（用于空实例）。
     */
    private static final Object[] EMPTY_ELEMENTDATA = {};
    /下面是ArrayList的扩容机制
//ArrayList的扩容机制提高了性能，如果每次只扩充一个，
//那么频繁的插入会导致频繁的拷贝，降低性能，而ArrayList的扩容机制避免了这种情况。
    /**
     * 如有必要，增加此ArrayList实例的容量，以确保它至少能容纳元素的数量
     * @param   minCapacity   所需的最小容量
     */
    public void ensureCapacity(int minCapacity) {
        //如果是true，minExpand的值为0，如果是false,minExpand的值为10
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            // any size if not default element table
            ? 0
            // larger than default for default empty table. It's already
            // supposed to be at default size.
            : DEFAULT_CAPACITY;
        //如果最小容量大于已有的最大容量
        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }
   //得到最小扩容量
    private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
              // 获取“默认的容量”和“传入参数”两者之间的最大值
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }
  //判断是否需要扩容
    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            //调用grow方法进行扩容，调用此方法代表已经开始扩容了
            grow(minCapacity);
    }

    /**
     * 要分配的最大数组大小
     */
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

    /**
     * ArrayList扩容的核心方法。
     */
    private void grow(int minCapacity) {
        // oldCapacity为旧容量，newCapacity为新容量
        int oldCapacity = elementData.length;
        //将oldCapacity 右移一位，其效果相当于oldCapacity /2，
        //我们知道位运算的速度远远快于整除运算，整句运算式的结果就是将新容量更新为旧容量的1.5倍，
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        //然后检查新容量是否大于最小需要容量，若还是小于最小需要容量，那么就把最小需要容量当作数组的新容量，
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        //再检查新容量是否超出了ArrayList所定义的最大容量，
        //若超出了，则调用hugeCapacity()来比较minCapacity和 MAX_ARRAY_SIZE，
        //如果minCapacity大于MAX_ARRAY_SIZE，则新容量则为Interger.MAX_VALUE，否则，新容量大小则为 MAX_ARRAY_SIZE。
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
    //比较minCapacity和 MAX_ARRAY_SIZE
    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
     /**
     * 返回此ArrayList实例的浅拷贝。 （元素本身不被复制。）
     * 克隆列表的修改会影响原始列表，深度拷贝则相反
     */
    public Object clone() {
        try {
            ArrayList<?> v = (ArrayList<?>) super.clone();
            //Arrays.copyOf功能是实现数组的复制，返回复制后的数组。参数是被复制的数组和复制的长度
            v.elementData = Arrays.copyOf(elementData, size);
            v.modCount = 0;
            return v;
        } catch (CloneNotSupportedException e) {
            // 这不应该发生，因为我们是可以克隆的
            throw new InternalError(e);
        }
    }
    /**
     * 从列表中的指定位置开始，返回列表中的元素（按正确顺序）的列表迭代器。
     *指定的索引表示初始调用将返回的第一个元素为next 。 初始调用previous将返回指定索引减1的元素。
     *返回的列表迭代器是fail-fast 。
     */
    public ListIterator<E> listIterator(int index) {
        if (index < 0 || index > size)
            throw new IndexOutOfBoundsException("Index: "+index);
        return new ListItr(index);
    }

    /**
     *返回列表中的列表迭代器（按适当的顺序）。
     *返回的列表迭代器是fail-fast 。
     */
    public ListIterator<E> listIterator() {
        return new ListItr(0);
    }

    /**
     *以正确的顺序返回该列表中的元素的迭代器。
     *返回的迭代器是fail-fast 。
     */
    public Iterator<E> iterator() {
        return new Itr();
    }
}
```



## 集合遍历

+ for循环

  ```java
  List<Obj> list = new ArrayList<Obj>();
  for(int i=0;i<list.size();i++){
      Obj obj =(Obj)list.get(i);
  }
  ```

+ 增强for循环

  ```java
  List<Obj> list = new ArrayList<Obj>();
  for(Obj ob : list){
      //ob即为其中元素
  }
  ```

+ 迭代器遍历

  ```java
  List<Obj> list = new ArrayList<Obj>();
  Iterator<Obj> iter = list.iterator();
  while(iter.hasNext()){
      Obj ob = (Obj)iter.next();
  }
  ```

### 集合合并

+ addAll()方法

  ```java
  List<Obj> listA = new ArrayList<Obj>();
  List<Obj> listB = new ArrayList<Obj>();
  listA.addAll(listB);
  ```


## 排序

```java
ArrayList<Integer> arrayList = new ArrayList<Integer>();
//add若干数据后
// void sort(List list),按自然排序的升序排序
Collections.sort(arrayList);
 // 定制排序的用法
Collections.sort(arrayList, new Comparator<Integer>() {

    @Override
    public int compare(Integer o1, Integer o2) {
        return o2.compareTo(o1);
    }
});
comparable 接口实际上是出自java.lang包 它有一个 compareTo(Object obj)方法用来排序
comparator接口实际上是出自 java.util 包它有一个compare(Object obj1, Object obj2)方法用来排序
```

## 		Collections工具类

1. 排序

   ```java
   void reverse(List list)//反转
   void shuffle(List list)//随机排序
   void sort(List list)//按自然排序的升序排序
   void sort(List list, Comparator c)//定制排序，由Comparator控制排序逻辑
   void swap(List list, int i , int j)//交换两个索引位置的元素
   void rotate(List list, int distance)//旋转。当distance为正数时，将list后distance个元素整体移到前面。当distance为负数时，将 list的前distance个元素整体移到后面
   ```

   

2. 查找,替换操作

   ```java
   int binarySearch(List list, Object key)//对List进行二分查找，返回索引，注意List必须是有序的
   int max(Collection coll)//根据元素的自然顺序，返回最大的元素。 类比int min(Collection coll)
   int max(Collection coll, Comparator c)//根据定制排序，返回最大元素，排序规则由Comparatator类控制。类比int min(Collection coll, Comparator c)
   void fill(List list, Object obj)//用指定的元素代替指定list中的所有元素。
   int frequency(Collection c, Object o)//统计元素出现次数
   int indexOfSubList(List list, List target)//统计target在list中第一次出现的索引，找不到则返回-1，类比int lastIndexOfSubList(List source, list target).
   boolean replaceAll(List list, Object oldVal, Object newVal), 用新元素替换旧元素
   ```

   

## vector

`Object[]`数组,线程安全的

## LinkedList

双向链表(JDK1.6 之前为循环链表，JDK1.7 取消了循环),

线程不安全,

不支持高效的随机元素访问

### 双向链表

包含两个指针，一个 prev 指向前一个节点，一个 next 指向后一个节点。

![](..\images\68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d362f2545352538462538432545352539302539312545392539332542452545382541312541382e7.png)