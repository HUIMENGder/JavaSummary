#### 学习笔记，仅供学习使用
## 一· 数据类型   

### 一. 基本类型

* byte/8  
* char/16
* short/16
* int/32
* float/32
* long/64
* double/64
* boolean/~  

boolean 的返回值只有两个true和false，可以用1bit进行存储，在JVM编译时会将其转换为int类型，使用1表示true，0 来表示false。  

### 二.包装类型  
基本类型都有其对应的包装类型，Java是个对象对象的语言，基本类型不具有对象的性质，为了与其他的对象“接轨”就出现了包装类型，相当于将基本类型包装起来，是他们具有了对象的性质，并且给他们添加了对应的属性和方法，丰富了基本类型的操作。 

### 三.缓存池 
缓一些常用的数据类型可以提高程序的运行效率，Java为一些基本类型提供了缓存池来提高效率，一些基本元素以经被加入。
#### 拆箱和装箱 
基本类型转换成为包装类型的时候需要调用包装类型静态ValueOf()函数的，而包装类型转换成为基本类型需要调用以基本类型开头的Value()的函数，比如intvalue(),floatvalue()等。  
一般情况下，Java会自动装箱和拆箱 ：  
```
        Integer x = 10 ; //自动装箱，实际应为给 Integer x = Integer.valueOf(10)    
        int x = y ; //自动拆箱，int x = intvalue(y) 
```  
#### 缓存池介绍  
当使用上面显示的包装类型变量时，如果其已经存在于缓存池中，则可以直接调用，如果没有则需要新创建一个对象存储相应的数据。
```
boolean values true and false                //布尔类型中的两个取值  true和false   
all byte values                              //byte类型所有数据，即-128～127  
short values between -128 and 127            //short类型大小范围-128~127  
int values between -128 and 127              //int类型大小范围  -128~127  
char in the range \u0000 to \u007F           //char类型所有数据，即所有字符  
```  
创建包装类型都有两种方式，两种方式都可以将基本类型传入，两种方法的区别在于：  
new Integer(123) 每次都会创建一个新的对象  
Integer.ValueOf(123)  每次都会调用缓存池的对象，多次调用会取得同一个对象的引用。  
```
        Integer x = new Integer(123);  
        Integer y = new Integer(123);  
        System.out.println(x == y); // false  
        Integer z = Integer.valueOf(123);
        Integer k = Integer.valueOf(123);
        System.out.println(z == k);   // true  
        nteger z = Integer.valueOf(200);
        Integer k = Integer.valueOf(200);  
        System.out.println(z == k); //false,200超过了int上限127，每次都需要创建一个新的对象  
```  
其中第三点我们可以通过ValueOf()源码可以明白：  
```
public static Integer valueOf(int i){
        if(i >= IntegerCache.low && i <= IntegerCache.high)  
        return IntegerCache.cache[i+ (-IntegerCache.low)]; //如果在范围内直接调用缓存池的地址   
        return new Integer(i); 
}
```  
尤其源码可以知道，需要先判断是不是在范围内，如果在在范围内，直接调用缓存池的对象，如果不在要创建一个新的对象。  

## 二.String  
### String类的定义  
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
从这个定义可以看出String类是final类型的，他的所有成员变量也是final类型的，所以他是不可以被继承的。(包括包装类型等都是不可以被继承的)  
以上代码为Java8所定义的String，可以看出内部使用char[]来存储数据，而在Java9中：  
```
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
字符串在分配的时候需要消耗高昂的时间和空间成本，JVM 为了提高效率和减小内存消耗，在字符串实例化的时候进行了优化:使用字符串常量池。每当我们创建字符串常量时，JVM会检查字符串常量池，如果该字符串已经在其中，就直接引用