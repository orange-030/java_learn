# Collection☕

集合类：集合是 java 中提供的一种容器，可以用来存储多个数据。

集合和数组和数组的区别：

* 数组的长度是固定的，存储的是同一类型的元素，可以存储基本数据类型值。
* 集合的长度是可变的，存储的都是对象。而且对象的类型可以不一致。

集合按照其存储结构可以分为两大类：

- 单列集合： `java.util.Collection` 
- 双列集合： `java.util.Map`



## 方法

`java.util.Collection` 是 java 集合类的顶级接口，有以下抽象方法：

<img src="http://store.secretcamp.cn/uPic/image-20210713225339088202107132253391626188019f4vdYFf4vdYF.png" alt="image-20210713225339088" style="zoom:50%;" />



## 继承体系

`java.util.Collection` 是单列集合类的根接口，用于存储一系列符合某种规则的元素，它有三个重要的子接口

- `java.util.List` 
-  `java.util.Set`
- `java.util.Queue`

`List` 的特点是元素有序、元素可重复。

`Set` 的特点是元素无序，而且不可重复。

![image-20210312142414739](http://store.secretcamp.cn/uPic/image-20210312142414739202103121424151615530255mPMeHLmPMeHL.png)







# 泛型☕

泛型：可以在类或方法中预知地使用未知的类型，例如在创建集合时指定该集合存储对象或值的具体类型。

`Collection` 虽然可以存储各种对象，但实际上通常 `Collection` 只存储同一类型对象，例如都是存储 `String` 对象。因此在 *JDK 1.5* 之后，新增了 泛型（Generic） 语法，在设计 API 时可以指定类或方法支持泛型，这样使用 API 的时候也变得更为简洁，并可以得到编译时期的语法检查。

泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。



## 泛型的使用

定义格式：

~~~
修饰符 class 类名<代表泛型的变量> {  }
~~~

例如，API 中的 `ArrayList` 集合：

~~~java
class ArrayList<E>{ 
    public boolean add(E e){ }

    public E get(int index){ }
   	....
}
~~~

使用泛型： 即什么时候确定泛型。

在创建对象的时候确定泛型

例如： `ArrayList<String> list = new ArrayList<String>();`

此时，变量E的值就是 String 类型，那么我们的类型就可以理解为：

~~~java 
class ArrayList<String>{ 
     public boolean add(String e){ }

     public String get(int index){  }
     ...
}
~~~

再例如，`ArrayList<Integer> list = new ArrayList<Integer>();`



## 泛型通配符

当使用泛型类或者接口时，传递的数据中，泛型类型不确定，可以通过通配符 `<?>` 表示。但是一旦使用泛型的通配符后，只能使用 Object 类中的共性方法，集合中元素自身方法无法使用。

### 基本使用

泛型的通配符：*不知道使用什么类型来接收的时候，此时可以使用 ? 表示未知通配符*

此时只能接受数据，不能往该集合中存储数据。

~~~java
public static void main(String[] args) {
    Collection<Intger> list1 = new ArrayList<Integer>();
    getElement(list1);
    Collection<String> list2 = new ArrayList<String>();
    getElement(list2);
}

// ？代表可以接收任意类型
public static void getElement(Collection<?> collection){
  
}

~~~



### 受限泛型

之前设置泛型的时候，实际上是可以任意设置的，只要是类就可以设置。但是在JAVA的泛型中可以指定一个泛型的**上限**和**下限**。

泛型的上限：

* 格式： `类型名称 <? extends 类 > 对象名称`
* 意义： `只能接收该类型及其子类`

泛型的下限：

- 格式： `类型名称 <? super 类 > 对象名称`
- 意义： `只能接收该类型及其父类型`

比如：现已知Object类，String类，Number类，Integer类，其中Number是Integer的父类

~~~java
public static void main(String[] args) {
    Collection<Integer> list1 = new ArrayList<Integer>();
    Collection<String> list2 = new ArrayList<String>();
    Collection<Number> list3 = new ArrayList<Number>();
    Collection<Object> list4 = new ArrayList<Object>();
    
    getElement01(list1);
    getElement01(list2);  //报错
    getElement01(list3);
    getElement01(list4);  //报错
  
    getElement02(list1);  //报错
    getElement02(list2);  //报错
    getElement02(list3);
    getElement02(list4);  
}

// 泛型的上限：此时的泛型?，必须是Number类型或者Number类型的子类
public static void getElement1(Collection<? extends Number> coll){}
// 泛型的下限：此时的泛型?，必须是Number类型或者Number类型的父类
public static void getElement2(Collection<? super Number> coll){}
~~~



## 类型擦除

Java 的泛型是伪泛型，这是因为 Java 在编译期间，所有的泛型信息都会被擦掉，这也就是通常所说 “类型擦除” 。

Java 的泛型是在编译器这个层次上实现的，在生成的字节码中是不包含泛型中的类型信息的，使用泛型的时候加上类型参数，在编译器编译的时候会去掉，这个过程成为类型擦除。

例如，在代码中定义 `List<String>` 等类型，在编译后都会变成 `List` ，JVM 看到的只是 `List` ，而由泛型附加的类型信息对 JVM是 看不到的。



# List☕

`List` 作为 `Collection` 集合的子接口，不但继承了 `Collection` 接口中的全部方法，而且还增加了一些根据元素索引来操作集合的特有方法，如下：

- `public void add(int index, E element)`： 将指定的元素，添加到该集合中的指定位置上。
- `public E get(int index)`：返回集合中指定位置的元素。
- `public E remove(int index)`：移除列表中指定位置的元素, 返回的是被移除的元素。
- `public E set(int index, E element)`：用指定元素替换集合中指定位置的元素,返回值的更新前的元素。

`List` 的常用实现类有 `ArrayList`、`LinkedList `和 `Vector`



## ArrayList

### 设计思想

`java.util.ArrayList` 数据存储的结构是数组结构。

特点：查询效率高，增删效率低，线程不安全。

不支持并发读写，内部使用变量 `modCount` 记录操作数，并发下 `modCount` 对不上会抛出 `ConcurrentModificationException`



### 源码分析

#### add

`add` 方法的主要操作：

1. 判断列表的 capacity 是否足够，是否需要扩容
2. 将元素放在列表的数组 `elementData` 里面

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
```



`calculateCapacity(elementData, minCapacity)` ：确定执行 `add` 操作后所需要的具体容积

```java
private static int calculateCapacity(Object[] elementData, int minCapacity) {
  	// 如果elementData为空，则扩容后的值为DEFAULT_CAPACITY，即首次扩容的容积为10
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}
```



`ensureExplicitCapacity`：执行扩容

```java
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // 如果所需空间大于目前数组长度，则需要扩容
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```



#### grow

`grow`：具体的扩容操作

```java
private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
  	// 一般用不上
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // 进行数组拷贝操作，扩容的原理是 Arrays.copyOf 方法
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```











## LinkedList



### 设计思想

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```

`java.util.LinkedList` 数据存储的结构是链表结构，底层用双向链表实现存储。

特点：查询效率低，增删效率高，线程不安全。

<img src="http://store.secretcamp.cn/uPic/image-20210908144718274202109081447181631083638EawakqEawakq.png" alt="image-20210908144718274" style="zoom:50%;" />

双向链表也叫双链表，是链表的一种，它的每个数据节点中都有两个指针，分别指向前一个节点和后一个节点。

`LinkedList` 提供了大量首尾操作的方法：

* `public void addFirst(E e)`:将指定元素插入此列表的开头。
* `public void addLast(E e)`:将指定元素添加到此列表的结尾。
* `public E getFirst()`:返回此列表的第一个元素。
* `public E getLast()`:返回此列表的最后一个元素。
* `public E removeFirst()`:移除并返回此列表的第一个元素。
* `public E removeLast()`:移除并返回此列表的最后一个元素。
* `public E pop()`:从此列表所表示的堆栈处弹出一个元素。
* `public void push(E e)`:将元素推入此列表所表示的堆栈。
* `public boolean isEmpty()`：如果列表不包含元素，则返回true。



### 源码分析





## Vector

### 设计思想

`Vector` 底层是用数组实现的 `List`，相关的方法都加了同步检查，因此 “线程安全，效率低”。

例如，indexOf 方法通过 *synchronized* 修饰。



### 源码分析

和 `ArrayList` 基本一样



## CopyOnWriteArrayList

### 设计思想

设计思想的核心是读写分离，即写操作在一个复制的数组上进行，读操作还是在原始数组中进行，读写分离，互不影响。

写操作需要加锁，防止并发写入时导致写入数据丢失。写操作结束之后需要把原始数组指向新的复制数组。



`CopyOnWriteArrayList` 在写操作的同时允许读操作，大大提高了读操作的性能，因此很适合读多写少的应用场景，但也有一些缺点：

1. 内存占用：在写操作时需要复制一个新的数组，使得内存占用为原来的两倍左右；
2. 数据不一致：读操作不能读取实时性的数据，因为部分写操作的数据还未同步到读数组中。



### 源码分析





### FAQ

#### 为什么没有ConcurrentArrayList？

很难去开发一个通用并且没有 “并发瓶颈” 的线程安全的 List，`ConcurrentHashMap` 的价值不并不在于并发安全，而是保证并发安全的同时不存在并发瓶颈。

对于 List 结构，很难去规避并发瓶颈，例如 `contains()` 操作，很难去避免锁整个 List 

`CopyOnWriteArrayList` 规避了读操作并发的瓶颈，但是写操作仍存在并发瓶颈。



# Set☕

`java.util.Set` 接口和 `java.util.List` 接口一样，同样继承自 `Collection` 接口，它与 `Collection` 接口中的方法基本一致，并没有对 `Collection` 接口进行功能上的扩充，只是比 `Collection` 接口更加严格了。

`Set` 容器特点：无序、不可重复。

- 无序：指 Set 中的元素没有索引，只能遍历查找；
- 不可重复：指不允许加入重复的元素。更确切地讲，新元素如果和 Set 中某个元素通过 `equals()` 方法对比为true，则不能加入。甚至，Set 中也只能放入一个null元素。

<img src="http://store.secretcamp.cn/uPic/collection01202105311456271622444187tBoOn8tBoOn8.png" style="zoom:87%;" />





Set 集合有多个子类，常用的有 `java.util.HashSet`、`java.util.LinkedHashSet` 这两个集合。







## HashSet

### 设计思想

`java.util.HashSet` 是 `Set` 接口的一个实现类，它所存储的元素是不可重复的，并且元素都是无序的（即存取顺序不一致）。`java.util.HashSet  `底层的实现其实是通过 `java.util.HashMap` 实现。



`HashSet` 是根据对象的哈希值来确定元素在集合中的存储位置，因此具有良好的存取和查找性能。保证元素唯一性的方式依赖于：`hashCode` 与 `equals` 方法。

```java
public class demo_HashSet {
    public static void main(String[] args) {
        Set set = new HashSet<>();
        set.add("block");
        set.add("thread");
        set.add("process");
        for (Iterator iter = set.iterator(); iter.hasNext(); ) {
            System.out.println(iter.next());
        }
    }
}

// process
// block
// thread
```





### 源码分析

`HashSet` 的 `add` 方法实际上调用了 HashMap 的 put 方法，`put` 方法需要两个参数 key - value ， `HashSet` 的 `add` 方法传递的参数，则对应了 `key` ，而 `value` 则是一个常量。

```java
private static final Object PRESENT = new Object();
```







## TreeSet

### 设计思想

`TreeSet` 底层实际是用 `TreeMap` 实现的，内部维持了一个简化版的 `TreeMap`，通过key来存储 `Set` 的元素。`TreeSet` 内部需要对存储的元素进行排序，因此，我们对应的类需要实现 `Comparable` 接口。这样，才能根据 `compareTo()` 方法比较对象之间的大小，才能进行内部排序。



### 源码分析



## LinkedHashSet

### 设计思想

`HashSet` 保证元素唯一，可是元素存放进去是没有顺序的，在 `HashSet` 下面有一个子类 `java.util.LinkedHashSet` ，它是链表和哈希表组合的一个数据存储结构。

```java
public class demo_LinkedHashSet {
    public static void main(String[] args) {
        Set set = new LinkedHashSet<>();
        set.add("c");
        set.add("b");
        set.add("a");
        for (Iterator iter = set.iterator(); iter.hasNext(); ) {
            System.out.println(iter.next());
        }
    }
}

// c
// b
// a
```



### 源码分析





# Queue☕

```java
public interface Queue<E> extends Collection<E> {
		// 抛出异常
    boolean add(E e);
  
	  // 不抛出异常，返回false
    boolean offer(E e);
  
		// 抛出异常
    E remove();
  
		// 不抛出异常，返回null
    E poll();
  
		// 抛出异常
    E element();
  
    // 不抛出异常
    E peek();
}

```





## Deque（Interface）

双端队列 `Deque` 继承自 `Queue` ，该接口中不仅定义了双端队列相关的方法，也定义了栈相关的方法

```java
public interface Deque<E> extends Queue<E> {
		// ...
}
```

<img src="http://store.secretcamp.cn/uPic/image-20210713232318350202107132323181626189798Cjn2mqCjn2mq.png" alt="image-20210713232318350" style="zoom:50%;" />







## BlockingQueue（Interface）

`BlockingQueue` 是 `java.util.concurrent` 包中定义的一个阻塞队列的接口，相对于 `Queue` 而言多了几个抽象方法。





## PriorityQueue

Java 中的 `PriorityQueue` 通过二叉小顶堆实现，可以用一棵完全二叉树表示。

<img src="http://store.secretcamp.cn/uPic/image-202107132328351802021071323283516261901155plvBB5plvBB.png" alt="image-20210713232835180" style="zoom:50%;" />







# Map☕

`java.util.Map ` 接口用来存放 "键值对" 关系的对象，Map类中存储的 "键值对" 通过键来标识，所以 "键对象" 不能重复。

Map 接口的实现类有 `HashMap`、`TreeMap`、`HashTable`、`Properties ` 等。

`Map` 接口中定义了很多方法，常用的如下：

* `public V put(K key, V value)`:  把指定的键与指定的值添加到Map集合中。
* `public V remove(Object key)`: 把指定的键 所对应的键值对元素 在Map集合中删除，返回被删除元素的值。
* `public V get(Object key)` 根据指定的键，在Map集合中获取对应的值。
* `boolean containsKey(Object key)  ` 判断集合中是否包含指定的键。
* `public Set<K> keySet()`: 获取Map集合中所有的键，存储到Set集合中。
* `public Set<Map.Entry<K,V>> entrySet()`: 获取到Map集合中所有的键值对对象的集合(Set集合)。





## HashMap🐚

### 设计思想

`Map<K,V>` 是一种以键值对存储数据的容器，而 `HashMap` 则是借助了键值 Key 的 hashcode 值来组织存储结构，使得可以非常快速和高效地地根据键值 key 进行数据的存取。

对于键值对 <key,value>，`HashMap` 内部会将其封装成一个对应的节点对象，即 `Node<key,value>` 

对于每个对象，JVM 都会为其生成一个 hashcode 值。`HashMap` 在存储键值对 `Node<Key,Value>` 的时候，会根据 Key 的hashcode 值，以某种映射关系，决定应当将这对键值对 `Node<Key,Value>` 存储在 `HashMap` 中的什么位置上；

当通过 Key 值取数据的时候，然后根据 Key 值的hashcode，以及内部映射条件，直接定位到 Key 对应的 Value 值存放在什么位置，可以非常高效地将 Value 值取出。



### 源码分析（JDK8）

#### 基本元素

`Node` 是 `HashMap` 中的基本数据存储结构

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
  
    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }
}
```



#### 构造方法

`HashMap` 的构造方法一共有四种，可以手动指定初始数组容量和负载因子，如果不指定，则为默认值。

`DEFAULT_LOAD_FACTOR`：0.75

由于 `HashMap` 的懒加载机制，创建 `HashMap` 对象后并不会立刻创建数组，而是在第一次调用 `put()` 方法时，会检测数组有没有初始化，如果没有初始化，则调用 `resize()` 方法进行初始化。

```java
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
}

public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }

public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal initial capacity: " +
                                           initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " +
                                           loadFactor);
    this.loadFactor = loadFactor;
    this.threshold = tableSizeFor(initialCapacity);
    }

public HashMap(Map<? extends K, ? extends V> m) {
    this.loadFactor = DEFAULT_LOAD_FACTOR;
    putMapEntries(m, false);
}
```



#### hash

`hash` 方法对 hashCode 进行一个扰动，其目的是进一步减少散列冲突，对某些特殊的数据，如果不进行扰动，则很容易发生散列冲突，返回后的新值参与 `put()` 相关方法的运算。 

`hash()` 对 `hashCode()` 方法的结果制造一个扰动，使hash值二进制的高十六位也能参与运算，从而产生一个扰动后的新值。

```java
static final int hash(Object key) {
    int h;
    // hashCode 异或 hashCode 的高十六位
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

举例：

```
	String s = "hello"

  101111010010001100011010010  -> 99162322  ->   s.hashCode()
  000000000000000010111101001  -> 1513      ->   s.hashCode() >>> 16
^ ---------------------------
  101111010010001110100111011  -> 99163451  ->   s.hashCode() ^ ((s.hashCode()) >>> 16)
```



#### put

##### 计算存放的位置

`put` 方法内部调用了 `putVal` 方法，`putVal` 方法中并没有使用原始的 hashCode，而是用 `hash()` 方法对 hashCode 的值做了一个扰动。

```java
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
```

首先会判断散列表是否初始化，如果没有则调用 `resize()` 进行初始化，延迟初始化的逻辑仅在第一次存数据时进行初始化

```java
if ((tab = table) == null || (n = tab.length) == 0)
    n = (tab = resize()).length;
```



如何根据根据元素的哈希值确定元素应该放置于哪个位桶之中？ 这里需要根据寻址公式计算。

寻址公式： *(table.length - 1) & node.hash*

`table.length` 一定是二的次方数，那么 `table.length - 1` 的二进制全为 1

因此，寻址公式的与运算结果一定是小于等于 `table.length - 1` ，（例如任何一个数 & 1111，结果一定小于 1111）



##### 存储

如果桶位为空（无散列冲突），则直接存储数据

如果桶位不为空（发生散列冲突）：

- 桶位中的第一个元素和待添加元素一致，则标记 “替换”
- 桶位中的第一个元素和待添加元素不一致，则沿着链表寻找，如果知道链表末尾还没有发现相同 key 的元素，则向位桶中添加新元素，如果发现了，则标记 “替换”
- 桶位中的第一个元素是一个树节点，则调用 `putTreeVal()` ， 使用红黑树添加元素的逻辑

下一步，如果存在标记了为 “替换” 的情况，则执行替换操作，本次 `put` 操作没有加入新元素，仅仅替换了同 key 的数据项

最后，如果散列表中元素个数超过了阈值，则执行 `resize` 



```java
// 位桶为空，则直接放入
if ((p = tab[i = (n - 1) & hash]) == null)
    tab[i] = newNode(hash, key, value, null);


// 位桶不为空
else {
    // e: 临时node，如果e存在，则表示找到了与当前要插入的key一致的元素
    // k: 临时key
    Node<K,V> e; K k;

    // 表示桶位中的元素，与插入的元素完全一致
    if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
        e = p;

    // 当前元素是一个树的节点
    else if (p instanceof TreeNode)
        e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);

    // 链表的情况，且链表的头元素与插入项不一致
    else {
        for (int binCount = 0; ; ++binCount) {
            // 遍历到末尾，还没有找到一致的元素，则创建新的Node加入链表末尾
            if ((e = p.next) == null) {
                p.next = newNode(hash, key, value, null);
                // 插入后链表长度达到8，则转为红黑树
                if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                    treeifyBin(tab, hash);
                break;
            }
            // 在链表中找到了相同的元素
            if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                break;
            p = e;
        }
    }

    // e不为空，表示当前桶位中找到了一个与插入项相同的元素，执行替换
    if (e != null) { // existing mapping for key
        V oldValue = e.value;
        if (!onlyIfAbsent || oldValue == null)
            e.value = value;
        afterNodeAccess(e);
        return oldValue;
    }
}
```





#### get

`get()` 方法会调用 `getNode()` 方法

```java
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;

    if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {

        // 定位的桶位元素直接是要get的数据
        if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
            return first;

        // 桶位中不止一个元素，可能是链表或者红黑树
        if ((e = first.next) != null) {
          	// 如果是红黑树
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            // 遍历链表查找
            do {
                if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```



#### resize

`resize` 方法的调用时机：

- 调用 `put()` 方法，发现散列表没有初始化，此时设置初始容量为 16
- 调用 `put()` 方法，发现散列表中元素超过了阈值（$threshold=0.75\times length $），设置新容量为原容量的两倍

于是对原数组进行扩容，将原数组中的数据重新计算并存放在新数组中



例如将容量为 16 的数组扩容为 32，这里将 0-15 称为低位数组，16-31 称为高位数组，扩容的过程就是将部分低位数组中的数据移至高位数组，使得整个散列表中的数据分布均匀。

- 如果低位位桶中仅有一个数据，则直接移至高位位桶

- 如果低位位桶中的数据是一个树节点，则调用红黑树的 `split()` 方法

- 如果低位位桶中的数据是一个链表，其中有多个数据项，使用尾插法转移节点：
  
  > *jdk1.7* 使用的是头插法转移节点，高并发下有成环问题
  
  - 根据 `hash & oldCapacity ?= 0 ` 计算哪些数据项应该被移至高位位桶中
  - `oldCapacity` 是二的次方数，假设是 16 即 10000 ，那么则要看当前节点 hash 值的第五位是否为 0
  - 如果该位为0，则计算结果为 0，该数据项仍保留在低位，如果为 1，计算结果不为 0，将该数据项移至高位，总体来说效果是随机的

![2019021916223252](http://store.secretcamp.cn/uPic/2019021916223252202101120938471610415527CW7c53CW7c53.png)





```java
for (int j = 0; j < oldCap; ++j) {
    // e 临时节点元素
    Node<K,V> e;
    // 当前桶位有数据，并用变量e去引用
    if ((e = oldTab[j]) != null) {
        // 置空
        oldTab[j] = null;

        // 当前桶位是单个元素，将该元素移至高位数组
        if (e.next == null)
            newTab[e.hash & (newCap - 1)] = e;

        // 当前桶位是一个树节点
        else if (e instanceof TreeNode)
            ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);

        // 当前桶位有多个数据，是一个链表
        else { // preserve order
            // 低位链表：在扩容之后数组的下标位置，与扩容前下标位置一致
            // loHead 指向低位链表头部，loTail 指向低位链表尾部
            Node<K,V> loHead = null, loTail = null;
            // 高位链表：在扩容之后数组的下标位置，是扩容前下标位置 + 扩容前数组长度
            // loHead指向高位链表头部，loTail指向高位链表尾部
            Node<K,V> hiHead = null, hiTail = null;
            Node<K,V> next;

            do {
                // 假设 oldCap = 16
                // oldCap -> 0b ...1 0000
                // hash -> 随机值
                // 所以就看hash的第五位，如果是1，则 e.hash & oldCap 不为0，如果是0，则为0

                next = e.next;
                if ((e.hash & oldCap) == 0) {
                    if (loTail == null)
                        loHead = e;
                    else
                        loTail.next = e;
                    loTail = e;
                }

                else {
                    if (hiTail == null)
                        hiHead = e;
                    else
                        hiTail.next = e;
                    hiTail = e;
                }
            } while ((e = next) != null);
						
          	// 原链表被分为了两个链表，低位链表和高位链表
						// 将分配后的新低位链表放在原来的索引位置
            if (loTail != null) {
                loTail.next = null;
                newTab[j] = loHead;
            }
						
            // 将分配后的新高位链表放在扩容后的索引位置
            if (hiTail != null) {
                hiTail.next = null;
                newTab[j + oldCap] = hiHead;
            }
        }
    }
}
```



### FAQ

#### 为什么数组长度总是二的次方数？

上文中可以知道，`hashMap` 中的寻址公式为 *(table.length - 1) & node.hash*

如果 table 的长度为二的次方数，那么 table.length 的二进制一定全部为 "1"

那么 *(table.length - 1) & node.hash* 的计算结果一定是小于该二的次方数的，计算结果一定在 [0 ~ length - 1] ，这样保证了不会出现数组越界的情况发生。

例如上文中，hash 的值为 1122，散列表的长度为16，则转为十进制的最后结果为2，根据寻址算法，

```
1000 1100 0010
0000 0000 1111
--------------
0000 0000 0010
```

于是寻找索引为 2 的桶位



#### 为什么引入红黑树？

在 JDK1.8 以及以后的版本中，hashMap 的底层结构，由原来的「数组+链表」，更改为「数组+链表+红黑树」

链表的查询效率是 $O(n)$，红黑树的查询效率是$O(logn)$

1. 既然红黑树的查询效率远高于链表，那为什么不使用「数组+红黑树」的结构？

   A：树节点所占空间是普通节点的两倍，所以只有当节点足够多的时候，才会使用树节点。也就是说，节点少的时候，尽管时间复杂度上，红黑树比链表好一点，但是红黑树所占空间比较大，综合考虑，认为只在节点太多的时候，红黑树占空间大这一劣势不太明显的时候，才会舍弃链表，使用红黑树。

2. 为什么在链表长度为 8 时转变为红黑树？

   事实上链表长度很难达到 8 ，受 hash 算法的影响，链表中的节点遵循泊松分布，根据统计，链表中节点数为 8 的概率已经接近千分之一，此时链表的性能已经很差了。

   在理想的的情况下，例如存放的数据在 bins 中是均匀分布的，那节点数不到 8 ，一般就会触发扩容机制了。此外，链表转红黑树的操作也要花费大量的性能。因此，转红黑树只是为了应对一些极端情况。



#### 为什么并发下会成环？

在 *JDK 1.7* 中，`HashMap` 在并发环境下可能会出现环形链表，造成死循环

`HashMap` 扩容的时候会调用 `transfer()` 方法，在 while 循环中，单个计算好数组索引位置后，单个的插入数组中，在多线程情况下，会有成环问题

```java
void resize(int newCapacity) {
    Entry[] oldTable = table;
    int oldCapacity = oldTable.length;
    if (oldCapacity == MAXIMUM_CAPACITY) {
        threshold = Integer.MAX_VALUE;
        return;
    }

    Entry[] newTable = new Entry[newCapacity];
    transfer(newTable, initHashSeedAsNeeded(newCapacity));
    table = newTable;
    threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
}

void transfer(Entry[] newTable, boolean rehash) {
    int newCapacity = newTable.length;
    for (Entry<K,V> e : table) {
        while(null != e) {
          	// 先用临时变量 next 保存 e.next
            Entry<K,V> next = e.next; 
            if (rehash) {
                e.hash = null == e.key ? 0 : hash(e.key);
            }
            // 计算新数组中的位置i
            int i = indexFor(e.hash, newCapacity);
          	// pos  头插法的关键，也是成环的问题所在
            e.next = newTable[i];
            newTable[i] = e;
            e = next;
        }
    }
}
```



在 *JDK 1.7* 的实现中，链表数据迁移使用的是头插法，例如下方链表的长度为 3，先将 a 迁移到新位置，再将 b 迁移到新位置，此时 b.next = a ，以此类推。

```
迁移前 oldTable
| a -> b - > c
|
|

迁移后 newTable
|
| c -> b -> a
|
```



假设当前有两个线程，线程一进入 `transfer` 后线程中断，然后线程二进入 `transfer()` 方法并成功执行，此时新表中的某个位置已经有了节点，然后线程一继续执行，当执行到 pos 的时候，由于线程二的数据迁移导致 newTable[i] 的位置已经有节点存在了，所以线程一执行 pos 的语句就会把 next 指向自己，造成死循环。

<img src="http://store.secretcamp.cn/uPic/image-20210806194410400202108061944101628250250OboSTfOboSTf.png" alt="image-20210806194410400" style="zoom:50%;" />



在 *JDK 1.7* 的实现中，正是多线程操作了共同内存，再加上述描述的情形，才导致了成环。

在 *JDK 1.8* 的实现中，当桶位中链表长度大于 1 时，使用局部变量 loHead 和 hiHead 来保存链表的值，因为局部变量是线程私有的，所以不会出现成环的问题。

```java
// 低位链表：存放在扩容之后数组的下标位置，与扩容前下标位置一致
// loHead指向低位链表头部，loTail指向低位链表尾部
Node<K,V> loHead = null, loTail = null;
// 高位链表：存放在扩容之后数组的下标位置，扩容前下标位置 + 扩容前数组长度
// loHead指向高位链表头部，loTail指向高位链表尾部
Node<K,V> hiHead = null, hiTail = null;
Node<K,V> next;
```

在整个 while 循环结束后，才将 loHead 和 hiHead 的值赋给数组

```java
if (loTail != null) {
    loTail.next = null;
    newTab[j] = loHead;
}

if (hiTail != null) {
    hiTail.next = null;
    newTab[j + oldCap] = hiHead;
}  
```

所以多线程情况下，也不会成环。



#### 同一桶位hash相同吗？

散列表中同一位桶中链表所有节点的 hash 值并不是相同的，只要经过寻址算法后得到的结果相同即可。

寻址公式用的 `node.hash` 是扰动后的 hash 值，根据寻址公式，只需要根据 hash 值的后几位即可确定在数组中的位置，所以同一数组中的链表的元素，它们的 hash 值整体不一定相同，但是后几位一定是相同的。

例如 1111 & 1100 = 1100 = 12，不管是 xxxx xxxx xxxx 1100，只要按位与 1111 ，最终一定分配到桶位 12

```
假设 table.length = 16
那么 table.length - 1 = 15
15 的二进制为 1111

以下两个hash值，运算的结果相同：

	0000 1111
	1010 1100  
& ---------
	0000 1100 --> 12
	
	0000 1111
	1111 1100  
& ---------
	0000 1100 --> 12	
```



#### 哪些数据应该被迁移到高位？

根据 `hash & oldCapacity ?= 0 ` 计算哪些数据项应该被移至高位位桶中

`oldCapacity` 是二的次方数，假设是 16 即 10000 ，那么则要看当前节点 hash 值的第五位是否为 0

如果该位为0，则计算结果为 0，该数据项仍保留在低位，如果为 1，计算结果不为 0，将该数据项移至高位，总体来说效果是随机的



#### 线程不安全的原因

- 死循环 & 数据丢失：JDK1.7 中扩容时的头插法导致，JDK1.8 不存在该问题

- 数据覆盖

  1. 假设两个线程 A、B 都在进行 put 操作，并且 hash 函数计算出的插入下标是相同的，当线程 A 执行完第六行代码后由于时间片耗尽导致被挂起，而线程 B 得到时间片后在该下标处插入了元素，成了正常的插入。

  2. 然后线程 A 获得时间片，由于之前已经进行了 hash 碰撞的判断，所有此时不会再进行判断，而是直接进行插入，这就导致了线程 B 插入的数据被线程 A 覆盖了，从而线程不安全。



## HashTable🐚

### 设计思想

`HashTable` 使用 *synchronized* 来保证线程安全，但在线程竞争激烈的情况下的效率非常低下。

当一个线程访问 `HashTable` 的同步方法，其他线程也访问同步方法时会被阻塞。如线程 A 使用 `put()` 进行元素添加，线程 B 不但不能使用 `put()` ，也不能使用 `get()` 方法来获取元素。

`HashMap`：线程不安全，效率高，允许 key 或 value 为 null

`HashTable`：线程安全，效率低，不允许 key 或 value 为 null





### 源码分析

从源码可知，如果 value 为 null，直接报空指针异常，如果 key 为 null，在执行 `hashCode` 方法时也会报空指针异常。

```java
public synchronized V put(K key, V value) {
    // Make sure the value is not null
    if (value == null) {
        throw new NullPointerException();
    }
  
    Entry<?,?> tab[] = table;
    int hash = key.hashCode();  // Make sure the key is not null
    int index = (hash & 0x7FFFFFFF) % tab.length;
    // ...
}
```





## LinkedHashMap🐚

### 设计思想

`LinkedHashMap` 是链表和哈希表组合的一个数据存储结构，将所有 `Entry` 节点链入一个双向链表保证了迭代顺序，该迭代顺序可以是插入顺序，也可以是访问顺序（LRU）。

`HashMap` 和双向链表的密切配合和分工合作造就了 `LinkedHashMap` ，在下图中，如果去掉红色虚线，那么就是一个 `HashMap` ，用红色虚线串起来则形成了双向链表。

next 指针保持了数组对各个桶位的 `Entry` 的引用，before 和 after 指针则保持了对双向链表的引用，两者互不干扰。

<img src="http://store.secretcamp.cn/uPic/image-20210420210101916202104202101021618923662vqvOervqvOer.png" alt="image-20210420210101916" style="zoom:40%;" />



### 源码分析

#### 构造函数

```java
// accessOrder = false  按照插入顺序 
// accessOrder = true  按照访问顺序

public LinkedHashMap(int initialCapacity, float loadFactor, boolean accessOrder) {
    super(initialCapacity, loadFactor);
    this.accessOrder = accessOrder;
}
```



#### 成员变量

```java
/**
 * The head of the doubly linked list.
 */
private transient Entry<K,V> header;  // 双向链表的表头元素

/**
 * The iteration ordering method for this linked hash map: <tt>true</tt>
 * for access-order, <tt>false</tt> for insertion-order.
 *
 * @serial
 */
private final boolean accessOrder;  // true表示按照访问顺序迭代，false时表示按照插入顺序 
```



#### 基本元素

`LinkedHashMap` 中的 `Entry` 增加了两个指针 before 和 after，它们分别用于维护双向链接列表。

next 用于维护 `LinkedHashMap` 各个桶中 Entry 的连接顺序，before、after 用于维护 `Entry` 插入的先后顺序

```java
private static class Entry<K,V> extends HashMap.Entry<K,V> {

    // These fields comprise the doubly linked list used for iteration.
    Entry<K,V> before, after;

    Entry(int hash, K key, V value, HashMap.Entry<K,V> next) {
        super(hash, key, value, next);
    }
    ...
}
```









## TreeMap🐚

`TreeMap` 是用红黑树实现 `Map` 的一种实现。

`HashMap` 的效率高于 `TreeMap`，在 key 需要排序时可以使用 `TreeMap`

自定义类通过实现 `Comparable` 接口可以实现排序，`compareTo ` 的返回值中，负数表示小于，零表示等于，正数表示大于。

```java
class Student implements Comparable<Student> {  // 实现Comparable接口
    int id;
    double score;
    
    // score第一优先，id第二优先
    @Override
    public int compareTo(Student student) {
        if (this.score > student.score) {    
            return 1;
        } else if (this.score < student.score) {
            return -1;
        } else {
            if (this.id > student.id) {
                return 1;
            } else if (this.id < student.id) {
                return -1;
            } else {
                return 0;
            }
        }
    }
```



### 方法

![image-20210714103634166](http://store.secretcamp.cn/uPic/image-20210714103634166FH4bnF1626230194561.png)





### 源码分析

```java
public class TreeMap<K,V>
        extends AbstractMap<K,V>
        implements NavigableMap<K,V>, Cloneable, java.io.Serializable {
  // ...
}
```



#### 成员变量

```java
    /**
     * The comparator used to maintain order in this tree map, or
     * null if it uses the natural ordering of its keys.
     *
     * @serial
     */
    private final Comparator<? super K> comparator;

    private transient Entry<K,V> root;

    /**
     * The number of entries in the tree
     */
    private transient int size = 0;

    /**
     * The number of structural modifications to the tree.
     */
    private transient int modCount = 0;
```



#### 基本元素

```java
static final class Entry<K,V> implements Map.Entry<K,V> {
    K key;
    V value;
    Entry<K,V> left;
    Entry<K,V> right;
    Entry<K,V> parent;
    boolean color = BLACK;

    /**
     * Make a new cell with given key, value, and parent, and with
     * {@code null} child links, and BLACK color.
     */
    Entry(K key, V value, Entry<K,V> parent) {
        this.key = key;
        this.value = value;
        this.parent = parent;
    }
}
```



#### 构造方法

```java
public TreeMap() {
    comparator = null;
}

public TreeMap(Comparator<? super K> comparator) {
    this.comparator = comparator;
}

public TreeMap(Map<? extends K, ? extends V> m) {
    comparator = null;
    putAll(m);
}

public TreeMap(SortedMap<K, ? extends V> m) {
    comparator = m.comparator();
    try {
        buildFromSorted(m.size(), m.entrySet().iterator(), null, null);
    } catch (java.io.IOException cannotHappen) {
    } catch (ClassNotFoundException cannotHappen) {
    }
}
```



#### put





## ConcurrentHashMap🐚

### 设计思想

`ConcurrentHashMap` 主要就是为了应对 `HashMap` 在并发环境下不安全而诞生，大量的利用了 来减少锁竞争对于性能的影响。

<img src="http://store.secretcamp.cn/uPic/image-20210421153727450202104211537271618990647t7t8Y4t7t8Y4.png" alt="image-20210421153727450" style="zoom:50%;" />



*JDK 1.7* 中，使用了锁分段的技术，容器中有多把锁，每一把锁锁一段数据，这样在多线程访问时不同段的数据时，就不会存在锁竞争了。在理想状态下 `ConcurrentHashMap` 可以支持 16 个线程执行并发写操作（默认并发级别为16），及任意数量线程的读操作。`Segement` 的个数可以通过构造函数改变，但一经创建不可更改。



*JDK 1.8* 中，取消了锁分段技术，使用了大量 `volatile` 、 `final`、 `CAS   ` 等无锁并发技术提高性能，将锁的粒度控制在 table 数组元素级别，理想情况下 table 数组元素的大小就是其支持并发的最大个数。只要锁住一个链表的 head 节点，并不会影响其他的 table 元素的读写，好处在于并发的粒度更细，影响更小，从而并发效率更高，但不足之处在于并发扩容的时候，由于操作的 table 都是同一个，需要等扩容完之后，所有的读写操作才能进行，所以扩容的效率就成为了整个并发的一个瓶颈点。

Doug lea 对扩容部分进行了优化，原本在一个线程扩容的时候，如果影响了其他线程的数据，那么其他的线程的读写操作都应该阻塞。它在 *JDK 1.8* 中引入了一个 `ForwardingNode` 类来优化扩容过程。



### 源码分析（JDK7）

#### 基本结构

`ConcurrentHashMap` 是由 `Segment` 数组结构和 `HashEntry` 数组结构组成，`ConcurrentHashMap` 本质上是一个 `Segment` 数组，`Segment` 的结构和 `HashMap` 类似，是一种数组和链表结构。

`Segment` 是一种可重入锁，在 `ConcurrentHashMap` 里扮演锁的角色。

```java
static class Segment<K,V> extends ReentrantLock implements Serializable {
    private static final long serialVersionUID = 2249069246763182397L;
    final float loadFactor;
    Segment(float lf) { this.loadFactor = lf; }
}
```



一个 `Segment` 里包含一个 `HashEntry` 数组，每个 `HashEntry` 是一个链表结构的元素，每个 `Segment` 守护着一个 `HashEntry` 数组里的元素。`HashEntry` 则用于存储键值对数据，当对 `HashEntry` 数组的数据进行修改时， 必须首先获得与它对应的 `Segment` 锁。

![image-20210824193439814](http://store.secretcamp.cn/uPic/image-20210824193439814202108241934401629804880z2zingz2zing.png)



`Segment` 的个数一旦初始化就不能改变，默认 `Segment` 的个数是 16 个，可以认为 `ConcurrentHashMap` 默认支持最多 16 个线程并发。



#### hash

既然 `ConcurrentHashMap` 使用分段锁 `Segment` 来保护不同段的数据，那么在插入和获取元素的时候，必须先通过散列算法定位到 `Segment` 。

首先进行再散列，减少散列冲突，使元素能够均匀地分布在不同的 `Segment` 上



#### get

`Segment` 的 `get()` 操作实现非常简单和高效。先经过一次再散列，然后使用这个散列值通过散 列运算定位到 `Segment`，再通过散列算法定位到元素。

`get()` 操作的高效之处在于整个过程不需要加锁，因为  `get()`  方法中将要使用的共享变量都用了 *volatile* 修饰，如用于统计当前 `Segement` 大小的 count 字段和用于存储值的 `HashEntry` 的 value。

定义成 *volatile* 的变量，能够在线程之间保持可见性，能够被多线程同时读，并且保证不会读到过期的值，但是只能被单线程写。

```java
transient volatile int count;
volatile V value;
```



#### put





#### size

整个 `ConcurrentHashMap` 里元素的大小是统计所有 `Segment` 里元素的大小后求和，即将所有的 count 累加，尽管 count 是一个 *volatile* 变量，但在累加的过程中 count 也有可能变化。

最安全的做法是在统计 size 的时候把所有 `Segment` 的 `put()` 、`remove()` 和 `clean()` 方法全部锁住，但是这种做法显然非常低效。

`ConcurrentHashMap` 的做法是先尝试 2 次通过不锁住 `Segment` 的方式来统计各个 `Segment` 大小，如果统计的过程中，容器的 count发生了变化，则再采用加锁的方式来统计所有 `Segment` 的大小。通过变量 `modCount` 可以得知是否进行了元素增删的操作。





### 源码分析（JDK8）

在 *JDK1.7* 之前，`ConcurrentHashMap` 是通过分段锁机制来实现的，所以其最大并发度受 `Segment` 的个数限制。

在 *JDK1.8* 中，`ConcurrentHashMap` 的实现原理摒弃了这种设计，而是选择了与 `HashMap` 类似的 「数组+链表+红黑树」 的方式实现，而加锁则采用 CAS 和 *synchronized* 实现。



#### 成员变量

`sizeCtl` 是一个控制标识符，用来控制 table 的初始化和扩容操作。

- sizeCtl = 0 ，表示未初始化
- sizeCtl = -1 ，表示正在初始化
- sizeCtl = -N ，高 16 位表示扩容标识戳，低 16 表示参与扩容操作的线程数量 + 1

- sizeCtl > 0
  - 如果 table 未初始化，表示 table 需要初始化的大小。
  - 如果 table 初始化完成，表示 table 的容量，默认是 table 大小的 0.75 倍，即下次触发扩容的阈值

```java
/**
 * Table initialization and resizing control.  When negative, the
 * table is being initialized or resized: -1 for initialization,
 * else -(1 + the number of active resizing threads).  Otherwise,
 * when table is null, holds the initial table size to use upon
 * creation, or 0 for default. After initialization, holds the
 * next element count value upon which to resize the table.
 */
private transient volatile int sizeCtl;
```





```java
/**
 * Base counter value, used mainly when there is no contention,
 * but also as a fallback during table initialization
 * races. Updated via CAS.
 */
private transient volatile long baseCount;

/**
 * Table of counter cells. When non-null, size is a power of 2.
 */
private transient volatile CounterCell[] counterCells;
```





#### 基本结构

##### Node

与 `HashMap` 大体保持一致，使用 `Node` 作为基本结构，用 *volatile* 修饰了一些属性保证并发环境下的可见性

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    volatile V val;
    volatile Node<K,V> next;
    // ...
}
```



##### ForwardingNode

`ForwardingNode` 结构用于协助扩容，是一个标识类。

当桶位数据迁移完成后，当前线程会将桶位头节点修改为 `ForwardingNode` ，标识要么就容迁移正在进行，要么已经完成扩容迁移。

`ForwardingNode` 的 hash 值为 -1 ，其属性 nextTable 指向扩容后的新数组



```java
static final class ForwardingNode<K,V> extends Node<K,V> {
    final Node<K,V>[] nextTable;
    ForwardingNode(Node<K,V>[] tab) {
        super(MOVED, null, null, null);
        this.nextTable = tab;
    }

    Node<K,V> find(int h, Object k) {
        // loop to avoid arbitrarily deep recursion on forwarding nodes
        outer: for (Node<K,V>[] tab = nextTable;;) {
            Node<K,V> e; int n;
            if (k == null || tab == null || (n = tab.length) == 0 ||
                    (e = tabAt(tab, (n - 1) & h)) == null)
                return null;
            for (;;) {
                int eh; K ek;
                if ((eh = e.hash) == h &&
                        ((ek = e.key) == k || (ek != null && k.equals(ek))))
                    return e;
                if (eh < 0) {
                    if (e instanceof ForwardingNode) {
                        tab = ((ForwardingNode<K,V>)e).nextTable;
                        continue outer;
                    }
                    else
                        return e.find(h, k);
                }
                if ((e = e.next) == null)
                    return null;
            }
        }
    }
}

```



#### spread

`spread()` 方法用户获取 key 值的 hash 值，作用类似于 `HashMap` 中的 `hash()` 方法

```java
static final int spread(int h) {
    return (h ^ (h >>> 16)) & HASH_BITS;
}
```



#### put

`put()` 方法会直接调用 `putVal()` 方法

`putVal()` 操作大致可分为以下几个步骤：

1. 计算 key 的 hash 值，根据 hashCode 即调用 `speed()` 方法计算再散列的 hash 值；

2. 获取 hash 值对应的 Node 节点位置，此时通过一个循环实现。有以下几种情况：

   - table 为空，则首先进行初始化操作，初始化之后再次进入循环获取 Node 节点的位置；
   - table 不为空，但对应桶位为空，则直接调用 `casTabAt()` 方法插入一个新节点，此时使用了 CAS 操作，不用加锁，失败则通过自旋保证成功；

   - table 不为空，且 key 对应的 Node 节点也不为空，但 Node 头结点的 hash 值为 `MOVED -1` ，表示这是一个 `ForwardingNode` ， 该桶位正在扩容，此时调用 `helpTransfer()` 方法协助扩容；

   - table 不为空的其他情况下（对应桶位不为空），则直接向桶位中插入一个新 Node 节点，此时需要对这个 Node 链表或红黑树通过 `synchronized` 加锁。

3. 插入元素后，判断对应的 Node 结构是否需要改变结构，如果 bitCount 大于等于 TREEIFY_THRESHOLD，需要则调用 `treeifyBin()` 方法将 Node 链表升级为红黑树；

4. 最后，调用 `addCount()` 方法记录 table 中元素的数量。



```java
final V putVal(K key, V value, boolean onlyIfAbsent) {
    if (key == null || value == null) throw new NullPointerException();
    int hash = spread(key.hashCode());
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh;
        // 如果尚未初始化，则进行初始化操作
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
        // 如果桶位为空 通过CAS添加头节点
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            if (casTabAt(tab, i, null,
                    new Node<K,V>(hash, key, value, null)))
                break;                   // no lock when adding to empty bin
        }

        // 如果头结点的哈希值为 MOVED，则扩容
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);

        // 插入新的 Node 节点
        else {
            V oldVal = null;
          	// 使用了 synchronized 
            synchronized (f) {
                if (tabAt(tab, i) == f) {
                    if (fh >= 0) {
                        binCount = 1;
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            if (e.hash == hash &&
                                    ((ek = e.key) == key ||
                                            (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                        value, null);
                                break;
                            }
                        }
                    }
                    else if (f instanceof TreeBin) {
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
          	// 判断是否要修改结构
            if (binCount != 0) {
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    addCount(1L, binCount);
    return null;
}

```



#### get

查询时，首先通过 `tabAt()` 方法找到 key 对应的 `Node` 链表或红黑树，然后遍历该结构便可以获取 key 对应的 value 值。

其中，`tabAt()` 方法主要通过 `Unsafe` 类的 `getObjectVolatile()` 方法获取 value 值，通过 volatile 读获取 value 值，可以保证 value 值的可见性，从而保证其是当前最新的值。

```java
static final <K,V> Node<K,V> tabAt(Node<K,V>[] tab, int i) {
    return (Node<K,V>)U.getObjectVolatile(tab, ((long)i << ASHIFT) + ABASE);
}
```









#### helpTransfer

迁移时的操作：

- 未迁移到的桶位：可以正常进行 `get()` 、`put()` 操作
- 正在迁移的桶位
  - get 操作：迁移中的 loHead 、hiHead 链是复制出来的，原本的链表没有受到影响，可以正常进行 get 操作不会阻塞。
  - put 操作：由于每个桶位在扩容时会使用同步锁， `put()` 方法也需要获取同一把锁，所以会阻塞。

如果发现这个节点的元素类型是 `ForwardingNode` 的话，就帮助正在扩容的线程一起扩容，提高速度。



```java
final Node<K,V>[] helpTransfer(Node<K,V>[] tab, Node<K,V> f) {
    Node<K,V>[] nextTab; int sc;
    // 如果 table 不是空 且 Node 节点是转移类型，数据检验
    // 且 Node 节点的 nextTable（新 table） 不是空，同样也是数据校验
    if (tab != null && (f instanceof ForwardingNode) &&
            (nextTab = ((ForwardingNode<K,V>)f).nextTable) != null) {
        int rs = resizeStamp(tab.length);

        // 如果 nextTab 没有被并发修改，且 tab 也没有被并发修改，且 sizeCtl  < 0 （说明还在扩容）
        while (nextTab == nextTable && table == tab &&
                (sc = sizeCtl) < 0) {
            if ((sc >>> RESIZE_STAMP_SHIFT) != rs || sc == rs + 1 ||
                    sc == rs + MAX_RESIZERS || transferIndex <= 0)
                break;
            if (U.compareAndSwapInt(this, SIZECTL, sc, sc + 1)) {
                // 迁移
                transfer(tab, nextTab);
                break;
            }
        }
        return nextTab;
    }
    return table;
}
```





#### transfer

如果是触发扩容的线程：

1. 创建容量为原来两倍的新数组
2. 分配任务

如果是协助扩容的线程：



迁移结束后，将 table 设置为 nextTable，将 nextTable 设置为空。



#### size

size 的实现方法和 `LongAdder` 的实现完全相同。

首先在添加和删除元素时，会通过 CAS 操作更新 `ConcurrentHashMap` 的 `baseCount` 属性来统计元素个数。但是 CAS 操作可能会失败，因此，`ConcurrentHashMap` 又定义了一个 `CounterCell` 数组来记录 CAS 操作失败时的元素个数。

因此，`ConcurrentHashMap` 中元素的个数为： *baseCount + sum(CounterCell[])*

```java
public int size() {
    long n = sumCount();
    return ((n < 0L) ? 0 :
            (n > (long)Integer.MAX_VALUE) ? Integer.MAX_VALUE :
                    (int)n);
}

final long sumCount() {
    CounterCell[] as = counterCells; CounterCell a;
    long sum = baseCount;
  	// 读取 CAS 操作失败的次数，并累加到 sum
    if (as != null) {
        for (int i = 0; i < as.length; ++i) {
            if ((a = as[i]) != null)
                sum += a.value;
        }
    }
    return sum;
}
```





`mappingCount()` 方法和 `size()` 含义相同，但是 `size()` 只能获取 int 范围内的元素个数，而如果表中的数据过多，超过了 int 类型的最大值，则需要使用 `mappingCount()` 方法获取其元素个数。

```java
public long mappingCount() {
    long n = sumCount();
		return (n < 0L) ? 0L : n; // ignore transient negative values
}
```



### 扩展问题

#### Node的hash字段有什么规律？

hash 字段的值都是大于等于 0 的，因为小于 0 有特殊含义。

`ForwardingNode` 节点的 hash 值为 -1 ，红黑树代理节点 `TreeBin` 的 hash值为 -2



#### put方法如何保证线程安全？

当桶位中有数据时，使用 *synchronized* 锁当前桶位的头节点，再 put 数据

当桶位为空时，通过 CAS 来 put 数据，如果失败，说明发生了竞争，于是自旋后重新来到当前桶位，此时该桶位已经有数据了，再用 *synchronized* 锁头节点。



#### 为什么get方法不需要加锁？

并发情况下，各线程中的数据可能不是最新的。

但 `get()` 方法全程不需要加锁是因为 `Node` 的 成员变量 `val` 是用 *volatile* 修饰的 。





#### 扩容期间在未迁移到的桶位可以插入数据吗？

可以，只要插入的位置扩容线程还未迁移到，就可以插入，当迁移到该插入的位置时，就会阻塞等待插入操作完成再继续迁移 。





#### put操作遇到ForwardingNode可以插入数据吗？

分情况讨论，桶位的头节点是 `ForwardingNode` 表示当前桶位正在迁移或者迁移完成，此时会去检查。

如果桶位正在迁移，当前线程会调用 `helpTransfer()`  方法协助扩容，完成扩容之后  `helpTransfer()`  返回扩容后的新数组 nextTable，然后方法会回到 `putVal()` 方法中的 for 循环，尝试进行插入操作。

如果桶位已经迁移完成了，那么 `ForwardingNode` 会指向迁移后的新数组，直接到新数组去执行 put 操作就可以。



#### 正在迁移的hash桶遇到get操作会发生什么？

在扩容过程期间形成的 hn 和 ln 链表是使用的类似于复制引用的方式，也就是说 ln 和 hn 链表是复制出来的，而非原来的链表迁移，所以原来 hash 桶上的链表并没有受到影响，因此从迁移开始到迁移结束这段时间都是可以正常访问原数组桶位上面的链表。

桶位迁移结束后头节点被修改为 `ForwardingNode`，迁移时的查询请求会通过 `ForwardingNode` 的 `find()` 方法转发到 nextTable 中去查询。





#### 为什么key不能为null？

`HashMap` 的 key 可以为 null，但 `ConcurrentHashMap` 不可以

1. 源码层面不支持，存在非空约束
2. 如果允许 key 为 null，并发环境下会带来二义性



#### 为什么有的CAS，有的用synchronized？

 `ConcurrentHashMap`  执行 put 操作时，如果桶位为空，则使用 CAS ，如果不为空，则使用 synchronized 。

获取锁竞争过高时，重量级锁相对来说更好一些，因为可以避免过多无用自旋导致的 CPU 使用率增高。

put 头节点是一个轻量级的操作，用 CAS 即可，但是 put 中间节点时需要先遍历、再替换等一系列操作，如果使用 CAS，并发量大会导致 CPU 空转，而 synchronized 可以让没获得锁的线程阻塞，不会浪费 CPU 性能 。



## ConcurrentSkipListMap🐚

### 跳跃表总体结构

原则：

- 最低层的链表维护了跳表内所有的元素，每上面一层链表都是下面一层的子集。
- 跳表内的所有链表的元素都是排序的，查找时，可以从顶级链表开始找。一旦发现被查找的元素大于当前链表中的取值，就会转入下一层链表继续找。



<img src="http://store.secretcamp.cn/uPic/image-20210824182048104202108241820481629800448EpthIjEpthIj.png" alt="image-20210824182048104" style="zoom:40%;" />



### 优缺点分析

跳表可以实现对数据平均时间复杂度 O(logn) 的查找效率，有点类似于二叉平衡树，它们都可以对元素进行快速的查找。

优点：

- 实现更加简单
- 插入和删除往往不会导致平衡树进行全局调整







## WeakHashMap🐚

### 设计思想

`WeakHashMap` 的 `Entry` 继承自 `WeakReference`，被 `WeakReference` 关联的对象在下一次垃圾回收时会被回收。

`WeakHashMap` 主要用来实现缓存，通过使用 `WeakHashMap` 来引用缓存对象，由 JVM 对这部分缓存进行回收。

```java
private static class Entry<K,V> extends WeakReference<Object> implements Map.Entry<K,V>
```



### 源码分析





# Collections☕

`java.util.Collections` 是一个工具类，提供了对 Set 、List 、Map 进行排序、填充、查找元素的辅助方法。

工具类中的方法都是静态方法，不需要实例化对象。



## 相关方法

- `public static <T> boolean addAll(Collection<T> c, T... elements)  `：往集合中添加一些元素。
- `public static void shuffle(List<?> list) `：打乱集合顺序。
- `public static <T> void sort(List<T> list)`：将集合中元素按照默认规则排序。
- `public static <T> void sort(List<T> list，Comparator<? super T> )`：将集合中元素按照指定规则排序。



## Comparable

首先创建 `Person` 类，实现 `Comparable `接口，重写 `Comparable ` 接口的 `compareTo` 方法

```java
class Person implements Comparable<Person> {
    private String name;
    private int age;

    Person(String name, int age){
        this.name = name;
        this.age = age;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    @Override
    public int compareTo(Person o) {
        return this.getAge() - o.getAge();
    }
}
```

测试类：

```java
public class demo_Comparable{
    public static void main(String[] args) {
        Person p1 = new Person("祖尔金", 38);
        Person p2 = new Person("古尔丹", 58);
        Person p3 = new Person("阿尔萨斯", 28);
        List<Person> list = new ArrayList<>();
        list.add(p1);
        list.add(p2);
        list.add(p3);
        Collections.sort(list);

        for (Person p: list) {
            System.out.println(p);
        }
    }
}

// Person{name='阿尔萨斯', age=28}
// Person{name='祖尔金', age=38}
// Person{name='古尔丹', age=58}
```



## Comparator

`Comparable` 和 `Comparator` 的区别

- `Comparable`：强行对实现它的每个类的对象进行整体排序。这种排序被称为类的自然排序，类的compareTo方法被称为它的自然比较方法。只能在类中实现 `compareTo()` 一次，不能经常修改类的代码实现自己想要的排序。实现此接口的对象列表（和数组）可以通过`Collections.sort` 和 `Arrays.sort` 进行自动排序，对象可以用作有序映射中的键或有序集合中的元素，无需指定比较器。

- `Comparator`：强行对某个对象进行整体排序。可以将 `Comparator` 传递给 `Collections.sort`或 `Arrays.sort` 方法，从而允许在排序顺序上实现精确控制。



```java
public class demo_Comparator {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("python");
        list.add("java");
        list.add("golang");
        list.add("c++");
        list.add("ruby");
        
        // 排序
        Collections.sort(list, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o1.length() - o2.length();
            }
        });
        
        // 打印
        for (Iterator iter = list.iterator(); iter.hasNext(); ) {
            System.out.println(iter.next());
        }
    }
}
```



# 迭代器☕

## Iterator

JDK 专门提供了一个接口 `java.util.Iterator` ，主要用于迭代访问 `Collection` 中的元素，因此 `Iterator` 对象也被称为迭代器。

```java
public interface Iterator<E> {
		boolean hasNext();
		
		E next();
}
```

`public Iterator iterator()`：获取集合对应的迭代器，用来遍历集合中的元素

Iterator接口的常用方法如下：

* `public E next()`：返回迭代的下一个元素。
* `public boolean hasNext()`：如果仍有元素可以迭代，则返回 true。

```java
public class demo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("a");
        list.add("b");
        list.add("c");
        for (Iterator<String> iter = list.iterator(); iter.hasNext();) {
            String temp = iter.next();
            System.out.println(temp);
        }
    }
}
```



## Iterator实现原理

Iterator 迭代器对象在遍历集合时，内部采用指针的方式来跟踪集合中的元素，在调用 `Iterator` 的 `next` 方法之前，迭代器的索引位于第一个元素之前，不指向任何元素，当第一次调用迭代器的 `next` 方法后，迭代器的索引会向后移动一位，指向第一个元素并将该元素返回，当再次调用 `next` 方法时，迭代器的索引会指向第二个元素并将该元素返回，依此类推，直到 `hasNext` 方法返回 false ，表示到达了集合的末尾，终止对元素的遍历。



## 遍历List和Set

1. 普通for循环

   ```java
   for (int i=0;i < list.size();i++) {
       String temp = list.get(i);
       System.out.println(temp);
   }
   ```

   

2. foreach 循环

   ```java
   for (String temp:list) {
       System.out.println(temp);
   }
   ```



3. 迭代器和for循环

   ```java
   for (Iterator<String> iter = list.iterator(); iter.hasNext();) {
       String temp = iter.next();
       System.out.println(temp);
   }
   ```

   

4. 迭代器和while循环

   ```java
   Iterator<String > iter = list.iterator();
   while(iter.hasNext()) {
       String temp = iter.next();
       iter.remove();  // 一边遍历一边删除推荐使用这种方式
       System.out.println(temp);
   }
   ```

   



## 遍历Map

1. 通过 `keySet()` 方法和 foreach 循环

   ```java
   for (Integer key:map.keySet()){
       System.out.println(key + " --- " + map.get(key));
   }
   ```



2. 通过 `keySet()` 方法和迭代器

   ```java
   Set<Integer> keyset = map.keySet();  // 生成存储key的集合
   for(Iterator<Integer> iter = keyset.iterator();iter.hasNext();) {
       Integer key =  iter.next();
       System.out.println(key + " --- " + map.get(key));
   }
   ```

   

3. 通过 `entrySet()` 方法和迭代器（推荐）

   ```java
   Set<Map.Entry<Integer, String>> kv = map.entrySet();  // 生成存储Entry的集合
   for(Iterator<Map.Entry<Integer, String>> iter = kv.iterator(); iter.hasNext();) {
       Map.Entry<Integer, String> temp = iter.next();
       System.out.println(temp.getKey() + " --- " + temp.getValue());
   }
   ```

   



## Iterable

JDK5 引入了新接口 `Iterable` ，该接口包含一个能够产生 `Iterator` 接口的 `iterator` 方法，并且 `Iterable` 对象被 `foreach` 用来在序列中移动，因此创建的任何实现了 `Iterable` 接口的类都可以将它用于 `foreach` 。

`Iterable` 接口是 `Collection` 的顶层接口

```java
public interface Iterable<T> {
    Iterator<T> iterator();
  
    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    // ...
}
```





# 容器操作☕

## 迭代的同时删除元素

```java
ArrayList<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list.add("b");
list.add("c");
```

错误的方法：

这种方式不能删除所有满足条件的元素  删除元素后原本后来位置的元素会前移，导致跳过。

例如第二次遍历时 i = 1 对应 b，第三次遍历时 i = 2 对应的本来还是 b，但因为刚刚删除了 b，导致索引前移。

```java
@Test
public void test01() {
    // 这种方式不能删除所有满足条件的元素  删除元素后原本后来位置的元素会前移，导致跳过
    for (int i = 0; i < list.size(); i++) {
        if (list.get(i).equals("b")) list.remove("b");
    }
}

@Test 
public void test04() {
    // 报错 ConcurrentModificationException
    for (String s : list) {
        if (s.equals("b")) list.remove(s);
    }
}
```



正确的方法： 使用迭代器遍历，并调用迭代器的 `remove` 方法

注意不要在 foreach 循环里进行元素的 `remove/add` 操作，因为这里调用的是集合自己的方法，而不是迭代器的 `remove/add`方法，这就导致迭代器莫名其妙地发现自己有元素被 `remove/add` ，然后，它就会抛出一个 `ConcurrentModificationException` 来提示用户发生了并发修改异常。

```java
@Test
public void test0() {
    // 正确的做法是使用迭代器 way1
    Iterator<String> iter = list.iterator();
    while (iter.hasNext()) {
        String s = iter.next();
        if (s.equals("b")) {
            iter.remove();
        }
    }
}

@Test
public void test0() {
  // 正确的做法是使用迭代器 way2
    for (Iterator<String> iter = list.iterator(); iter.hasNext(); ) {
        String s = iter.next();
        if (s.equals("b")) {
            iter.remove();
        }
    }
}
```





## 集合元素去重

去重有两种方式：

1. 利用 `HashSet` 去重，复杂度 O(n)
2. 遍历 `List` ，将不重复的元素添加到新 `List`，复杂度 O(n<sup>2</sup>)

两者的核心差别在于 `contains()` 方法的实现：

添加元素的时间复杂度都是  O(n) ，但是 `HashSet`  的 `contains()` 方法复杂度是 O(1) ，总体来说效率更高。



## 集合转数组







## 数组转集合

