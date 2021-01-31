# JDK

## HashMap

### HashMap的底层实现

- HashMap的底层实现是Node数组，数组的每个元素Node可能是一个链表，也可能是一个红黑树；
- 当Node的节点数超过8时，其被转换为红黑树，当Node的节点数小于6时，其被转换为链表；

### 为什么引入红黑树来实现HashMap

- hash值相同的Node都存储在一个桶了，如果是链表，其根据key依次查找的时间复杂度为O(N)，引入红黑树，时间复杂度为O(log(N))，提高了查询效率；
- 但是因为红黑树的特性，在插入删除元素时需要左旋和右旋，影响插入删除的效率，所以引入了两个阈值来根据节点数量调整数据结构；

### HashMap的的容量为什么是2的幂次方

- HashMap中的tableSizeFor方法用于计算它的容量，这个方法返回大于等于输入值的最小2的幂次方的值，如果输入值超过HashMap的最大允许容量，则返回最大容量；
- 把容量设置为2的幂次方是为了优化计算key对应的数组的位置，因为数组位置index =(table.length - 1) & hash(key)，这样计算出的值实际上就是hash(key) % table.length，这样可以使key的分布更均匀，而且mod运算改为位运算，效率更高；

### HashMap的扩容

- 当元素数量大于容量 * 负载因子时会进行扩容，扩容后的容量为现在的两倍；
- 扩容时HashMap会遍历所有元素，重新hash并计算元素的位置，扩容后元素的分布会更均匀；

### HashMap为什么是线程不安全的

- 在多线程的情况下，HashMap的扩容resize()会导致链表形成环形数据结构，造成死循环；
- fail-fast机制：在使用迭代器的过程中有其他线程修改了map，那么将抛出ConcurrentModificationException；

### HashMap和Hashtable的区别

- **线程安全性**：Hashtable的方法都使用了synchronized关键字，所以是线程安全的，HashMap不是线程安全的，HashMap的效率好很多；
- **null的支持**：Hashtable既不支持null key也不支持null value，而HashTable支持；

### Java集合的fail-fast机制

- Java集合在使用迭代器的过程中，如果发现集合被其他线程修改了，则会抛出ConcurrentModificationException，这是一种错误检测机制；
- HashMap在使用迭代器时，会设置迭代器的expectedModCount为HashMap的modCount，modCount记录了HashMap被修改的次数，迭代器每次遍历一个元素时都会比较这两个值，如果不同则抛出ConcurrentModificationException；

## ConcurrentHashMap

### ConcurrentHashMap是如何保证线程安全的

- 通过CAS和synchronized关键字保证了put操作的安全，在进行put操作时，如果key对应的数组元素为null，则通过CAS操作将其设置为当前值，如果不为null，则通过synchronized锁住该元素，然后进行put操作；
- 对于读操作，则是通过volatile关键字，保证了元素的可见性；

## ArrayList

### ArrayList与LinkedList对比

|          | ArrayList                           | LinkedList     |
| -------- | ----------------------------------- | -------------- |
| 底层实现 | 数组                                | 链表           |
| 性能     | 增删元素慢，通过索引查元素快        | 增删快，查询慢 |
| 扩容     | 初始容量为10，扩容之后容量变为1.5倍 | /              |

### Vector、LinkedList和ArrayList有什么区别

- 三者都实现了List接口，Vector public的方法都使用了synchronized关键字，所以是线程安全的，LinkedList与ArrayList都不是线程安全的；
- LinkedList是用链表实现的，Vector和ArrayList是用数组实现的，所以前者插入、删除效率更高，后两者查询效率更高；
- Vector默认构造一个容量为10的数组，扩容时如果没有在创建时指定增量大小，则每次扩容的策略是容量翻倍，如果指定了则按指定的值扩容。ArrayList默认构造一个空数组，ArrayList首次添加元素的时候将容量设置为10，后续扩容时每次增加50%的容量。所以ArrayList更节省空间；

## int和Integer的区别

- int是基本数据类型，Integer是int的包装类；
- Integer会缓存-128 - 127之间的数；

## JUC











