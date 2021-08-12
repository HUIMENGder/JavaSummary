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