## Object通用语法 
### 概述；
java类的层次结构的顶层是Object类，所有的其他类型都隐式的继承于它。  
### equals() 
#### 性质与用法
equals()方法的实现必须满足以下几个条件。   
* 自反性：对象x必须与其自身相等，equals(x)返回ture
* 对称性；如果equals(y)为ture，那么y.equals(x)也为ture
* 传递性；如果equals(y)为true，并且y.equals(z)也为true，则x.equals(z)也要为true  
* 一致性；多次调用equals()方法应该返回相同值，除非对用于判等的任何一个属性进行了修改  
* 与null判断：equals(null)一定返回false  
```java
x.equals(x); // true     -------自反性

x.equals(y) == y.equals(x); // true      -------对称性

if (x.equals(y) && y.equals(z))			 -------传递性
    x.equals(z); // true;
  
x.equals(y) == x.equals(y); // true			-------一致性

x.equals(null); // false;			-------与null判等
```  
#### 等价与相等  
* 对于基本类型，== 判断两个值是否相等，基本类型没有equals()方法。
* 对于引用类型，== 判断两个值是否引用一个对象，而equals()判断引用的对象是否等价。  
```java
Integer x = new Integer(1);
Integer y = new Integer(1);
System.out.println(x.equals(y)); // true
System.out.println(x == y);      // false
```
### hashCode  
hashCode()返回的是哈希值(散列值)，equals()用来判断两个对象是否等价。等价的两个对象散列值一定相等，散列值相等的两个对象不一定等价，这是因为计算哈希值具有随机性，两个值不同的对象可能
计算出相同的哈希值。  
在覆盖 equals() ⽅法时应当总是覆盖 hashCode() ⽅法，保证等价的两个对象哈希值也相等。  
HashSet 和 HashMap 等集合类使⽤了 hashCode() ⽅法来计算对象应该存储的位置，因此要将对象添
加到这些集合类中，需要让对应的类实现 hashCode() ⽅法  
### toString  
默认情况下，toString()的结果仅仅返回以@符分隔的全类名与对象哈希值串。（ToStringExample@4554617c）其中 @ 后面的数值为散列码的无符号十六进制表示。  
### clone()  
clone() 是 Object 的 protected 方法，它不是 public，一个类不显式去重写 clone()，其它类就不能直接去调用该类实例的 clone() 方法。

clone()方法的目的很简单——返回对象实例的拷贝    

注意：clone() 方法并不是 Cloneable 接口的方法，而是 Object 的一个 protected 方法。Cloneable 接口只是规定，如果一个类没有实现 Cloneable 接口又调用了 clone() 方法，就会抛 CloneNotSupportedException。