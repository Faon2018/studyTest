# map

使用键值对（kye-value）存储，类似于数学上的函数 y=f(x)，“x”代表 key，"y"代表 value，Key 是无序的、不可重复的，value 是无序的、可重复的，每个键最多映射到一个值。

## HashMap

一个个Entry（Key-Value键值对）存储在一个哈希数组上（Entry是HashMap的内部类）。链表的长度大于8时。链表会重构成一个红黑树

负载因子为0.75

`HashMap` 默认的初始化大小为 16。之后每次扩充，容量变为原来的 2 倍。

创建时如果给定了容量初始值，而 `HashMap` 会将其扩充为 2 的幂次方大小

HashMap` 使用键（Key）计算 `hashcode

**HashMap 通过 key 的 hashCode 经过扰动函数处理过后得到 hash 值，然后通过 (n - 1) & hash 判断当前元素存放的位置（这里的 n 指的是数组的长度），如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。**

所谓扰动函数指的就是 HashMap 的 hash 方法。使用 hash 方法也就是扰动函数是为了防止一些实现比较差的 hashCode() 方法 换句话说使用扰动函数之后可以减少碰撞。

```java
static final int hash(Object key) {
      int h;
      // key.hashCode()：返回散列值也就是hashcode
      // ^ ：按位异或
      // >>>:无符号右移，忽略符号位，空位都以0补齐
      return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
  }
```

**拉链法”** 就是：将链表和数组相结合。也就是说创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可。

HashMap **遍历**

+ 使用迭代器（Iterator）EntrySet 的方式进行遍历；

  ```java
   // 创建并赋值 HashMap
          Map<Integer, String> map = new HashMap();
          map.put(1, "Java");
          map.put(2, "JDK");
          map.put(3, "Spring Framework");
          map.put(4, "MyBatis framework");
          map.put(5, "Java中文社群");
          // 遍历
          Iterator<Map.Entry<Integer, String>> iterator = map.entrySet().iterator();
          while (iterator.hasNext()) {
              Map.Entry<Integer, String> entry = iterator.next();
              System.out.print(entry.getKey());
              System.out.print(entry.getValue());
          }
  ```

  

+ 使用迭代器（Iterator）KeySet 的方式进行遍历；

  ```java
   // 创建并赋值 HashMap
          Map<Integer, String> map = new HashMap();
          map.put(1, "Java");
          map.put(2, "JDK");
          map.put(3, "Spring Framework");
          map.put(4, "MyBatis framework");
          map.put(5, "Java中文社群");
          // 遍历
          Iterator<Integer> iterator = map.keySet().iterator();
          while (iterator.hasNext()) {
              Integer key = iterator.next();
              System.out.print(key);
              System.out.print(map.get(key));
          }
  ```

  

+ 使用 For Each EntrySet 的方式进行遍历；

  ```java
   // 创建并赋值 HashMap
          Map<Integer, String> map = new HashMap();
          map.put(1, "Java");
          map.put(2, "JDK");
          map.put(3, "Spring Framework");
          map.put(4, "MyBatis framework");
          map.put(5, "Java中文社群");
          // 遍历
          for (Map.Entry<Integer, String> entry : map.entrySet()) {
              System.out.print(entry.getKey());
              System.out.print(entry.getValue());
          }
  ```

  

+ 使用 For Each KeySet 的方式进行遍历；

  ```java
  // 创建并赋值 HashMap
          Map<Integer, String> map = new HashMap();
          map.put(1, "Java");
          map.put(2, "JDK");
          map.put(3, "Spring Framework");
          map.put(4, "MyBatis framework");
          map.put(5, "Java中文社群");
          // 遍历
          for (Integer key : map.keySet()) {
              System.out.print(key);
              System.out.print(map.get(key));
          }
  ```

  

+ 使用 Lambda 表达式的方式进行遍历；

  ```java
  // 创建并赋值 HashMap
          Map<Integer, String> map = new HashMap();
          map.put(1, "Java");
          map.put(2, "JDK");
          map.put(3, "Spring Framework");
          map.put(4, "MyBatis framework");
          map.put(5, "Java中文社群");
          // 遍历
          map.forEach((key, value) -> {
              System.out.print(key);
              System.out.print(value);
          });
  ```

  

+ 使用 Streams API 单线程的方式进行遍历；

  ```java
  // 创建并赋值 HashMap
          Map<Integer, String> map = new HashMap();
          map.put(1, "Java");
          map.put(2, "JDK");
          map.put(3, "Spring Framework");
          map.put(4, "MyBatis framework");
          map.put(5, "Java中文社群");
          // 遍历
          map.entrySet().stream().forEach((entry) -> {
              System.out.print(entry.getKey());
              System.out.print(entry.getValue());
          });
  ```

  

+ 使用 Streams API 多线程的方式进行遍历。

  ```java
    // 创建并赋值 HashMap
          Map<Integer, String> map = new HashMap();
          map.put(1, "Java");
          map.put(2, "JDK");
          map.put(3, "Spring Framework");
          map.put(4, "MyBatis framework");
          map.put(5, "Java中文社群");
          // 遍历
          map.entrySet().parallelStream().forEach((entry) -> {
              System.out.print(entry.getKey());
              System.out.print(entry.getValue());
          });
  ```

  

### TreeMap 

实现 `NavigableMap` 接口让 `TreeMap` 有了对集合内元素的搜索的能力。

实现`SortMap`接口让 `TreeMap` 有了对集合中的元素根据键排序的能力。默认是按 key 的升序排序，不过我们也可以指定排序的比较器。

```java
TreeMap<Person, String> treeMap = new TreeMap<>(new Comparator<Person>() {
            @Override
            public int compare(Person person1, Person person2) {
                int num = person1.getAge() - person2.getAge();
                return Integer.compare(num, 0);
            }
        });
```

