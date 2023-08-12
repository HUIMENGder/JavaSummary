#### 学习笔记，仅供学习使用
## 一、 数据类型   

### 基本类型

* byte/8  
* char/16
* short/16
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
```java
    Integer x = new Integer(123);  
    Integer y = new Integer(123);  
    System.out.println(x == y); // false  
    Integer z = Integer.valueOf(123);
    Integer k = Integer.valueOf(123);
    System.out.println(z == k);   // true  
    Integer z = Integer.valueOf(200);
    Integer k = Integer.valueOf(200);  
    System.out.println(z == k); //false,200超过了int上限127，每次都需要创建一个新的对象  
```  
其中第三点我们可以通过ValueOf()源码可以明白：  
```java
public static Integer valueOf(int i){
        if(i >= IntegerCache.low && i <= IntegerCache.high)  
        return IntegerCache.cache[i+ (-IntegerCache.low)]; //如果在范围内直接调用缓存池的地址   
        return new Integer(i); 
}
```  
尤其源码可以知道，需要先判断是不是在范围内，如果在在范围内，直接调用缓存池的对象，如果不在要创建一个新的对象。  

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

## 三、运算  
### 参数传递
Java参数传递是以值传递的形式传入方法的，而不是引用传递。
传引用参数指的还是原来那个引用，但是在Java中里面的参数类型是对象复制了原来的引用到一块新的内存，两者之间没有关系。  
#### 1.传值调用指的是什么  
在方法调用时，传递的参数是按值的拷贝参数。 
```JAVA
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
```java
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
```JAVA  
short s1 = 1;
// s1 = s1 + 1  
```  
但是使用+=，++ 运算符是会进行隐式类型转换  
```JAVA
s1 += 1;
s1++;  
```  
相当于s1+1的结果向下隐式类型转换了  
### switch  
在Java7之后，可以在switch判断语句中使用String对象  
```JAVA
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
## 继承  
### 访问权限  
Java中有三种访问权限的修饰符：private，protect，public，如果不加修饰字符，表示包级可见。  
可以对类或者类中的成员变量加上访问修饰符。  
* 类可见表示其他类可以用这个类创建实例化对象  
* 成员可见表示其他类可以通过这个类的实例化对象来访问到该成员。

良好的设计模块会隐藏所有的实现细节，它把API和类的实现清晰的隔离开来，模块之前只通过API之间联系，一个模块不需要知道其他模块的内部工作情况。这个概念被称之为信息的隐藏或者封装，因此访问权限应该尽量使得每个类或者成员不被外界访问，   

如果⼦类的⽅法重写了⽗类的⽅法，那么⼦类中该⽅法的访问级别不允许低于⽗类的访问级别。  

字段绝不能是共有的，因为这么做会失去对这个字段修改行为的控制，客户端可以随意的修改。  
### 抽象类与接口  
#### 1.抽象类  
抽象类和抽象方法都是用abstract关键字来声明，如果一个类包含了抽象方法，那么这个类必须被声明成为抽象类。  

抽象类和普通类最大的区别在于：普通类可以被实例化，抽象类不可以被实例化，只能被继承。  
***实现：***  
```java
修饰符列表 abstract class 类名{ 
	类体=成员变量+方法+抽象方法;
}
```

#### 2.接口 
接口（英文：Interface），在JAVA编程语言中是一个抽象类型，是抽象方法的集合，接口通常以interface来声明。一个类通过继承接口的方式，从而来继承接口的抽象方法。
 
接口并不是类，编写接口的方式和类很相似，但是它们属于不同的概念。类描述对象的属性和方法。接口则包含类要实现的方法。

除非实现接口的类是抽象类，否则该类要定义接口中的所有方法。  

接口无法被实例化，但是可以被实现。一个实现接口的类，必须实现接口内所描述的所有方法，否则就必须声明为抽象类。另外，在 Java 中，接口类型可用来声明一个变量，他们可以成为一个空指针，或是被绑定在一个以此接口实现的对象。  
**实现：**
```java
修饰符列表 [abstract] class subclass [extends superclass] implements 接口名｛
	//非抽象类需要实现接口的全部抽象方法
	//若要实现接口中的抽象方法，需要在修饰符列表加上 public 关键字
	//无指定接口则默认接口为java.lang.Object
｝
```

**接口特性** :
* 接口的每一个方法也是隐式抽象的，接口的每一个方法被隐式的指定为public abstract   
* 接口中可以含有变量 ，但是接口中变量会被隐式的指定为public static final
* 接口中的方法是不能在接口中实现的，只能由接口的类进行实现接口的方法。
* 接口的实现使用关键字implements。  
***..implements 接口名称[, 其他接口名称, 其他接口名称..., ...] ...***
  
#### 3.抽象类和接口的区别  
* 抽象类的方法可以有方法体，就是能实现方法的具体功能，但是接口中的不行。
* 抽象类的成员变量可以是各种类型的，但是接口中的只能是public static final。
* 接口中不能含有静态代码块以及静态方法(static实现)，但是抽线类中是可以有的。
* 一个类只能继承一个抽象类，但是可以实现多个接口。  
*  ***注：JDK 1.8 以后，接口里可以有静态方法和方法体了。***  
***注：JDK 1.8 以后，接口允许包含具体实现的方法，该方法称为"默认方法"，默认方法使用 default 关键字修饰。更多内容可参考 Java 8 默认方法。***  
***注：JDK 1.9 以后，允许将方法定义为 private，使得某些复用的代码不会把方法暴露出去。更多内容可参考 Java 9 私有接口方法。***  
#### 4.选择使用  
##### 使用接口  
* 需要让不相关的类都实现一个方法，
* 需要使用多重继承 
##### 使用抽象类  
* 需要在几个相关的类共享代码  
* 需要能控制继承来的成员的访问权限，而不是都是public
* 需要继承非静态和非常量字段
### super  
* 访问父类的构造函数：可以使用super()函数访问父类的构造函数，从而委托父类完成一些初始化的工作。应该注意到，子类一定会调用父类的构造函数来完成初始化工作，一般是父类的默认的构造函数，如果子类需要调用父类的其他构造函数就需要调用super()函数。
* 访问父类的成员：如果子类重写了父类的某个方法，可以通过关键字super来引用父类的方法来实现。  
```java
public class SuperExample {
 protected int x;
 protected int y;
 public SuperExample(int x, int y) {
 this.x = x;
 this.y = y;
 }
 public void func() {
 System.out.println("SuperExample.func()");
 }
}
```
```  java
public class SuperExtendExample extends SuperExample {
 private int z;
 public SuperExtendExample(int x, int y, int z) {
 super(x, y);
 this.z = z;
 }
 public void func() {
 super.func();
 System.out.println("SuperExtendExample.func()");
 }
}
```
```java
SuperExample e = new SuperExtendExample(1, 2, 3);
e.func();
```  
### 重写与重载  
#### 1.重写  
存在于继承体系中，指子类实现了一个与父类在方法声明完全相同的一个方法。  
为了满足里是替换原则，重写要满足三个限制：
* 子类方法的访问权限必须大于等于父类的访问权限  
* 子类方法的返回值必须是父类方法的返回值或者子类；
* ⼦类⽅法抛出的异常类型必须是⽗类抛出异常类型或为其⼦类型。  
#### 2.实现多态是函数调用顺序  
&emsp;在调⽤⼀个⽅法时，先从本类中查找看是否有对应的⽅法，如果没有再到⽗类中查看，看是否从⽗类继承来。否则就要对参数进⾏转型，转成⽗类之后看是否有对应的⽅法。总的来说，⽅法调⽤的优先级为：  
* this.func(this)
* super.func(this)
* this.func(super)
* super.func(super)
#### 3.重载  
 存在于同一个类中，指一个方法已经与另一个方法名称相同，但是参数顺序，个数，类型至少有一个不同。  
 但是返回值不同，其他都相同的不是重载。  
 ``` java 
 class OverloadingExample {
 public void show(int x) {
 System.out.println(x);
 }
 public void show(int x, String y) {
 System.out.println(x + " " + y);
 }
}
```  

## 七、反射 
### 1.反射的概念
&emsp;每一个类都有一个Class对象，包含了类的相关信息。当编译产生了一个新的类时，会产生一个同名的.class文件，该文件保存着Class对象。    
&emsp;JAVA反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。  
***反射就是把java类中的各种成分映射成一个个的Java对象***  
&emsp;例如：一个类有：成员变量、方法、构造方法、包等等信息，利用反射技术可以对一个类进行解剖，把个个组成部分映射成一个个对象。  
***Class对象的由来是将class文件读入内存，并为之创建一个Class对象***  


![Class对象来源](https://img-blog.csdn.net/20170513133210763)  

反射可以提供运⾏时的类信息，并且这个类可以在运⾏时才加载进来，甚⾄在编译时期该类的 .class 不
存在也可以加载进来。 
### 2.反射的使用  
先写一个Student类  
**一、获取Class对象的三种方法**  
* Object->getClass()  
* 任何数据类型(包括基本数据类型)都有一个静态"class"属性 
* ***通过Class类的静态方法：froName(常用)***    
```java
package fanshe;
/**
 * 获取Class对象的三种方式
 * 1 Object ——> getClass();
 * 2 任何数据类型（包括基本数据类型）都有一个“静态”的class属性
 * 3 通过Class类的静态方法：forName（String  className）(常用)
 *
 */
public class Fanshe {
	public static void main(String[] args) {
		//第一种方式获取Class对象  
		Student stu1 = new Student();//这一new 产生一个Student对象，一个Class对象。
		Class stuClass = stu1.getClass();//获取Class对象
		System.out.println(stuClass.getName());
		
		//第二种方式获取Class对象
		Class stuClass2 = Student.class;
		System.out.println(stuClass == stuClass2);//判断第一种方式获取的Class对象和第二种方式获取的是否是同一个
		
		//第三种方式获取Class对象
		try {
			Class stuClass3 = Class.forName("fanshe.Student");//注意此字符串必须是真实路径，就是带包名的类路径，包名.类名
			System.out.println(stuClass3 == stuClass2);//判断三种方式是否获取的是同一个Class对象
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
		
	}
}
```  
Class 和 java.lang.reflect ⼀起对反射提供了⽀持，java.lang.reflect 类库主要包含了以下三个类：  
* Field：Field ：可以使⽤ get() 和 set() ⽅法读取和修改 Field 对象关联的字段   
* Method ：可以使⽤ invoke() ⽅法调⽤与 Method 对象关联的⽅法；
* Constructor ：可以⽤ Constructor 的 newInstance() 创建新的对象  

### 3.反射的优点  
* 可扩展性 ：应⽤程序可以利⽤全限定名创建可扩展对象的实例，来使⽤来⾃外部的⽤户⾃定义
类。
* 类浏览器和可视化开发环境 ：⼀个类浏览器需要可以枚举类的成员。可视化开发环境（如 IDE）
可以从利⽤反射中可⽤的类型信息中受益，以帮助程序员编写正确的代码。
* 调试器和测试⼯具 ： 调试器需要能够检查⼀个类⾥的私有成员。测试⼯具可以利⽤反射来⾃动地
* 调⽤类⾥定义的可被发现的 API 定义，以确保⼀组测试中有较⾼的代码覆盖率  
### 反射的缺点  
* 性能开销 ：反射涉及了动态类型的解析，所以 JVM ⽆法对这些代码进⾏优化。因此，反射操作的
* 效率要⽐那些⾮反射操作低得多。我们应该避免在经常被执⾏的代码或对性能要求很⾼的程序中使
⽤反射。
* 安全限制 ：使⽤反射技术要求程序必须在⼀个没有安全限制的环境中运⾏。如果⼀个程序必须在
有安全限制的环境中运⾏，如 Applet，那么这就是个问题了。
* 内部暴露 ：由于反射允许代码执⾏⼀些在正常情况下不被允许的操作（⽐如访问私有的属性和⽅
法），所以使⽤反射可能会导致意料之外的副作⽤，这可能导致代码功能失调并破坏可移植性。反射代码破坏了抽象性，因此当平台发⽣改变的时候，代码的⾏为就有可能也随着变化。  
## 八、异常  
Throwable可以表示作为任何异常抛出的类(父类)，分为两类 Error(错误)和Expection(异常)，  
Throwable 对象中包含了其线程创建时线程执行堆栈的快照，它还包含了给出有关错误更多信息的消息字符串。  
### Error  
Error 是程序无法处理的错误，表示运行应用程序中较严重问题。大多数错误与代码编写者执行的操作无关，而表示代码运行时 JVM（Java 虚拟机）出现的问题。  
### Expection  
Expection以及他的子类，代表在运行时发生各种不期望发生的事件，可以被Java的异常处理机制使用，是异常处理的核心。  
Exception 异常可以分为两类：
* 1.非检查性异常(unchecke exception)    
  是Error和RuntimeException以及他们的子类。在Java语言编译中，不会提示的发现这类异常，不要求在编程中方处理这类异常。所以我们可以在编程中处理这类异常(使用try...catch.finally).对于这些错误或异常，我们应该修正代码，而不是去通过异常处理器处理。这样的异常发生的原因多半是由于我们的代码逻辑出现了问题。  
例如：  
  * 当程序中用数字除以0时，就会抛出ArithmeticException异常；
* 检查性异常(checked Exception)  
Java语言强制要求程序员为这样的异常做预备处理工作（使用try…catch…finally或者throws）。在方法中要么用try-catch语句捕获它并处理，要么用throws子句声明抛出它，否则编译不会通过。这样的异常一般是由程序的运行环境导致的。因为程序可能被运行在各种未知的环境下，而程序员无法干预用户如何使用他编写的程序，于是程序员就应该为这样的异常时刻准备着。  
## 九、泛型  
```java
public class Box<T> {
 // T stands for "Type"
 private T t;
 public void set(T t) { this.t = t; }
 public T get() { return t; }
}
```
## 十、Java版本新特性
