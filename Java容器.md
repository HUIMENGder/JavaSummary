# 一、概念  
容器包括两部分Collection和Map两种，Collection存储对象的集合，Map存储着键值对(两个对象)的映射表。
## Collection 
#### Collection的集合类的继承树如下：

![](https://img-blog.csdnimg.cn/20190717224652123.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2phdmFlZV9nYW8=,size_16,color_FFFFFF,t_70)  
Collection接口有三种子类型集合：_*List*_,_*Set*_,_*Queue*_,在下面是一些抽象类，最后是具体的实现，常用的有：ArrayList，LinkedList ,HashSet, LinkedHashSet,  ArrayBlockingQueue 等。  
#### 1.Set  
* TreeSet：基于红黑树实现，支持有序性实现，例如根据一个范围查找元素。TreeSet可以保证插入以后的排序大小，即输出时会按元素的排列大小关系来输出。HashSet查找时间复杂度为O(1),TreeSet的时间复杂度为O(lgN),效率较低。  
* HashSet基于哈希表的实现，支持快速查找，但不支持有序操作,并且失去了元素的插⼊顺序信息，也就是说使⽤ Iterator 遍历 HashSet 得到的结果是不确定的.
* LinkedHashSet ：具有HashSet的查找效率，并且内部使用双向链表维护元素的插入顺序。  
  
#### List  
* ArrayList :基本动态数组的实现，支持随机访问
* Vector： 和ArrayList基本想同，但是他的线程是安全的
* LinkedList ：基于双向链表的实现，只能顺序访问，但是可以在链表中快速的插入和删除元素，此外，LinkedList还可以用做栈，队列，还有双向队列。  
* List 常见操作：  
``` 
A:添加功能
boolean add(E e):向集合中添加一个元素
void add(int index, E element):在指定位置添加元素
boolean addAll(Collection<? extends E> c)：向集合中添加一个集合的元素。
    
B:删除功能
void clear()：删除集合中的所有元素
E remove(int index)：根据指定索引删除元素，并把删除的元素返回
boolean remove(Object o)：从集合中删除指定的元素
boolean removeAll(Collection<?> c):从集合中删除一个指定的集合元素。

C:修改功能
E set(int index, E element):把指定索引位置的元素修改为指定的值，返回修改前的值。

D:获取功能
E get(int index)：获取指定位置的元素
Iterator iterator():就是用来获取集合中每一个元素。

E:判断功能
boolean isEmpty()：判断集合是否为空。
boolean contains(Object o)：判断集合中是否存在指定的元素。
boolean containsAll(Collection<?> c)：判断集合中是否存在指定的一个集合中的元素。

F:长度功能
int size():获取集合中的元素个数

G:把集合转换成数组
Object[] toArray():把集合变成数组。  
```  
#### Queue  
* LinkedList:可以用它来实现双向队列，其内部是通过双向链表来实现的。
##### Queue 的方法
```
   Queue<String>  queue = new LinkedList<String>();  
         queue.offer()       将元素添加到队列末尾，若添加成功则返回true，该操作主要是给容量受限的队列设计的。
         queue.poll()         从队首删除并返回该元素
         queue.peek()       返回队首元素，但是不删除 
```

##### 实现栈
```
import java.util.LinkedList;
class  MyStack{
 private LinkedList ll = new LinkedList();
 public void push(Object o){   // 入栈
  ll.addFirst(o);
 }
 public Object pop(){    // 出栈,指从栈顶删除该元素并返回
  return ll.removeFirst();
 public Object peek(){    // 查看栈顶元素
  return ll.getFirst();
 }
 public boolean isEmpty(){   // 判断栈是否为空
  return ll.isEmpty();
 }
 public int getSize(){    // 得到栈的大小
  return ll.size();
 }
}
```  
##### 实现队列  
```  
import java.util.LinkedList;

class MyQueue{
 private LinkedList ll = new LinkedList();
 
 public void push(Object o){  //进队列
  ll.addLast(o);
 }
 
 public Object get(){ //出队列
  return ll.removeFirst();
 }
 
 public boolean isEmpty(){
  return ll.isEmpty();
 }
}  
```    
##### 双向队列的实现  
```
 Deque<String> deque  =  new LinkedList<String>();
``` 
双向队列(Deque)实际上是Queue的一个子接口，双向队列指的是队列两端的元素既能入队(offer)，也能出队(poll).如果将Deque限制为只有一端可以操作，则可以实现栈的数据结构。对于栈而言，有入栈(push)和出战(pop)两种操作。
##### 优先队列的实现  
```
PriorityQueue<Integer>  p = new PriorityQueue<Integer>();  
``` 
## Map  
Map集合没有继承Collection接口，其提供的是键到值的映射。Map不能包含相同的键，每个键只能映射一个值。键还决定了储存对象在映射中的储存位置。
### 1.HashMap集合  
集合特点：单一，无序  
Map<key,value>(键-值对)  
功能 ：
* 添加功能；V put(K key , V value) 
* 获取功能；V get(Object key) 
* 判断功能；boolean containsKey（object key），boolean containsValue（Object value），boolean isEmpty（）
* 删除功能：void clean(),V remove (Object key)  
* 遍历功能；Set<Map.Entry<K,V>> entrySet()  
### 总结  
* TreeMap：基于红黑树实现  
* HashMap；基于哈希表实现
* HashTable；与HashMap相似，但是他的线程是安全的，这意味这可以多个线程同时写入HashTable不会导致数据不一致。但是他是历史遗留类，不应该去使用，而应该使用ConcurrentHashMap来支持线程安全，ConcurrentHashMap的魈率会更高，因为他引入了分段锁。  
* LinkedHashMap：使⽤双向链表来维护元素的顺序，顺序为插⼊顺序或者最近最少使⽤（LRU）顺
序。  
# 容器中的设计模式  
## 1.迭代器模式  
* 迭代器模式是常用的设计模式，属于行为型模式。
* 如果我们的集合元素是用不同的方式来实现的，比如数组，Java集合类等其他方式，当客户端需要遍历这些元素集合时就要实现很多种遍历方式，而且还会暴露元素的内部结构，可以考虑有迭代器模式解决。  
