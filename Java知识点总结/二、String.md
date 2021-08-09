## 二、String  
### String类的定义
```JAVA  
        public final class String
        implements java.io.Serializable, Comparable<String>, CharSequence
        {
        /** The value is used for character storage. */
        private final char value[];

        /** The offset is the first index of the storage that is used. */
        private final int offset;

        /** The count is the number of characters in the String. */
        private final int count;

        /** Cache the hash code for the string */
        private int hash; // Default to 0

        /** use serialVersionUID from JDK 1.0.2 for interoperability */
        private static final long serialVersionUID = -6849794470754667710L;

        ........
        }
```  
从这个定义可以看出String类是final类型的，他的所有成员变量也是final类型的，所以他是不可以被继承的。(包括包装类型等都是不可以被继承的)  
以上代码为Java8所定义的String，可以看出内部使用char[]来存储数据，而在Java9中：  
```JAVA
public final class String
 implements java.io.Serializable, Comparable<String>, CharSequence {
 /** The value is used for character storage. */
 private final byte[] value;
 /** The identifier of the encoding used to encode the bytes in {@code
value}. */
 private final byte coder;
}
```  
从以上代码我们可以看出内部是用byte[]来存储字符串的，使用codeer来标识使用哪种编码。  
String内部被设置成为final，且内部没有设置修改value的方法，因此可以保证String不变。  
Sting类一旦被创建就不会被改变，对于Sting类的任何操作都是不影响原对象的，任何相关的change都会产生新的对象。  
#### 字符串常量池  
字符串在分配的时候需要消耗高昂的时间和空间成本，JVM 为了提高效率和减小内存消耗，在字符串实例化的时候进行了优化:使用字符串常量池。每当我们创建字符串常量时，JVM会检查字符串常量池，如果该字符串已经在其中，就直接引用.由于字符串的不可变性，我们可以十分肯定的是常量池一定不存在两个相同的字符串。

#### 不可变好处  
因为Sting的hash值经常被使用，例如Sting用作HashMap的key。不可变的性质是的hash的值也不可改变，因此只计算一次。  
    
#### 安全性  
Sting经常被用来作为参数，Sting的不可变性可以保证参数不变。

#### 线程安全  
String的不可变性天然具有安全性，可以在多线程安全中使用。  
  
#### String，StingBuffer and StingBuilder 
* 可变性  
  * String不可变  
  * StringBuffer和StirngBuilder可变  
* 线程安全  
  * String不可变，因此线程是安全的  
  * StringBuffer不是线程安全的  
  * StringBuilder是线程安全的，内部使用synchronized进行同步  
#### String Pool  
字符串常量池(String Pool)保存着所有的字符串面量（literal strings），不仅如此，还可以使⽤ String 的 intern() ⽅法在运⾏过程将字符串添加到 String Pool 中，当⼀个字符串调⽤ intern() ⽅法时，如果 String Pool 中已经存在⼀个字符串和该字符串值相等（使⽤equals() ⽅法进⾏确定），那么就会返回 String Pool 中字符串的引⽤；否则，就会在 String Pool 中添加⼀个新的字符串，并返回这个新字符串的引⽤。  
#### new String("abc")  
使用这种方式创建字符串会创建两个对象(前提是String Pool 不存在"abc"字符串对象)。  
* "abc"属于字符串面量，因此在编译的过程中会创建一个新的字符串对象，指向"abc"这个字符串面量  
* 使用new会直接在堆区创建一个新的字符串对象  
