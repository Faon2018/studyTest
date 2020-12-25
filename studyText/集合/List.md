# List集合

## ArrayList

### 集合遍历

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

  