#### 学习笔记，仅供学习使用
## 一、 数据类型   

### 基本类型

* byte/8  
* char/16
S* short/16
* int/32
* float/32S
* long/64
* double/64
* boolean/~  
boolean 的返回值只有两个true和false，可以用1bit进行存储，在JVM编译时会将其转换为int类型，使用1表示true，0 来表示false。  

### 包装类型  
基本类型都有其对应的包装类型，Java是个对象对象的语言，基本类型不具有对象的性质，为了与其他的对象“接轨”就出现了包装类型，相当于将基本类型包装起来，是他们具有了对象的性质，并且给他们添加了对应的属性和方法，丰富了基本类型的操作。 

### 缓存池 
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

## 二、String  
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

## 三、运算  
### 参数传递
Java参数传递是以值传递的形式传入方法的，而不是引用传递。
传引用参数指的还是原来那个引用，但是在Java中里面的参数类型是对象复制了原来的引用到一块新的内存，两者之间没有关系。  
#### 1.传值调用指的是什么  
在方法调用时，传递的参数是按值的拷贝参数。 
```
    public class TempTest {
        private void test1(int a){
        //做点事情
        }
        public static void main(String[] args) {
            TempTest t = new TempTest();
            int a = 3;
            t.test1(a);//这里传递的参数a就是按值传递
        }
    }  
```  
按值传递，传递的是值的拷贝，传递完之后就互不相关了。 
``` 
    public class TempTest {
        private void test1(int a){
            a = 5;
            System.out.println("test1方法中的a="+a);//a=5
        }
        public static void main(String[] args) {
            TempTest t = new TempTest();
            int a = 3;
            t.test1(a);//传递后，test1方法对变量值的改变不影响这里的a
            System.out.println(”main方法中的a=”+a);//a=3
        }
    }
```  
#### 按址传递是什么  
在方法调用的时候，传递的参数是按引用的方式传递的，传递的就是地址，也就是变量所对应的地址空间。  
传递的是值的引用，在传递前后指的都是同一块内存空间  
```
    public class TempTest {
        private void test1(A a){
            a.age = 20;
            System.out.println("test1方法中的age="+a.age);// a=20
        }
        public static void main(String[] args) {
            TempTest t = new TempTest();
            A a = new A();//传递是个对象A
            a.age = 10;
            t.test1(a);
            System.out.println(”main方法中的age=”+a.age);//a=20
        }
    }
    class A{
        public int age = 0;
    }
```  
#### 说明 
（1）：“在Java里面参数传递都是按值传递”这句话的意思是：按值传递是传递的值的拷贝，按引用传递其实传递的是引用的地址值，所以统称按值传递。

（2）：在Java里面只有基本类型和按照下面这种定义方式的String是按值传递，其它的都是按引用传递。就是直接使用双引号定义字符串方式。  
### float和double  
Java不能隐式的执行向下转换，这会使得精度降低。  
1.1字面量属于double类型，不能直接将其直接赋值给float类型，因为这是向下转型。当你不声明是小数默认都是双精度，所以如果要使用float时要在后面加上f   
### 隐式类型转换 
因为字面量1是int类型，比short类型精度更高，因此不能隐式的将int类型向下转换为short类型。
```  
short s1 = 1;
// s1 = s1 + 1  
```  
但是使用+=，++ 运算符是会进行隐式类型转换  
```
s1 += 1;
s1++;  
```  
相当于s1+1的结果向下隐式类型转换了  
### switch  
在Java7之后，可以在switch判断语句中使用String对象  
```
String s = "a";
switch (s) {
 case "a":
 System.out.println("aaa");
 break;
 case "b":
 System.out.println("bbb");
 break;
}
```  
## 四、关键字  
### final  
#### 1.数据
声明数据为常数，可以时编译常量，也可以是在运行时被初始化后不能被改变的常量。  
* 对于基本类型，final使得数值不变。
* 对于引用类型，final使得引用不可变，也就是不能引用其他对象，但是可以改变被引用对象的自身。  
```java
final int x = 1;
// x = 2; // cannot assign value to final variable 'x'
final A y = new A();
```  
#### 2.方法  
声明的方法不能被子类重写
private方法被隐式的指定为fianl，如果在子类定义的一个方法和基类的一个方法签名相同，此时子类的方法不是对基类方法的重写，而是在子类重新定义一个方法。  
#### 3.类  
声明的类不允许被继承。  
### static 
#### 1.静态变量  
* 静态变量：又称之为类变量，也就是类变量属于类。类的所有实例化对象都共享静态变量，静态变量在内存中只存在一份。
* 实例变量：每创建一个实例就会产生一个实例变量，与该实例同生共死。
```java 
public class A {
 private int x; // 实例变量
 private static int y; // 静态变量
 public static void main(String[] args) {
 // int x = A.x; // Non-static field 'x' cannot be referenced from
a static context
 A a = new A();
 int x = a.x;
 int y = A.y;
 }
}
```  
#### 2.静态方法 
静态方法在类加载的时候就存在了，他不依赖任何实例。所以静态方法必须有实现，也就是说他不能是抽象方法。_*可以在没有创建对象的情况下来进行调用（方法/变量）。*_  
```java
public abstract class A {
 public static void func1(){
    }
}
```
只能访问所属类的静态字段和静态方法，方法中不能有this和super关键字，因为这两个关键字和具体的对象有关。  
#### 3.静态语句块  
静态语句块在初始化只运行一次。
```java
public class Text {
    static{
        System.out.println("123");
    }
    public static void main(String[] agrs){
        Text a1 = new Text();
        Text a2 = new Text();
    }
}
//输出:123 
```  
#### 4.静态内部类  
非静态内部类依赖外部类的实例，也就是是要在外部实例化才可以调用，然后通过这个实例化的类去创建这个非静态的内部类，而静态类不需要。
```java
public class OuterClass {
 class InnerClass {
 }
 static class StaticInnerClass {
 }
 public static void main(String[] args) {
 // InnerClass innerClass = new InnerClass(); // 'OuterClass.this'
cannot be referenced from a static context
 OuterClass outerClass = new OuterClass();//实例化
 InnerClass innerClass = outerClass.new InnerClass();
 StaticInnerClass staticInnerClass = new StaticInnerClass();
 }
}
```  
静态内部的类不能访问外部类的非静态的变量和方法。  
#### 5.初始化顺序  
静态变量和静态语句块优先于实例变量和普通语句块，静态变量和静态语句块的初始化顺序取决于它们在代码中的顺序。  
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

if (x.equals(y) && y.equals(z))			-------传递性
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