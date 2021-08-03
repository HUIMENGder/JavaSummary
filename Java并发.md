* # 一、使用线程  
## 有三种使用线程的方法  
* 实现Runnable接口；
* 实现Callable接口；
* 继承Thread方法；  
实现Runnale和Callable接口的类只能当作可以在一个线程中可以运行的任务，并不是真正意义上的线程，因此最后还需要通过Thread来调用。可以理解为任务是通过线程驱动来执行的。
### 实现Runnable接口
Runnable只定义了一个run函数，这个函数就是在执行是调用的函数，  
```java
public interface Runnable {
    public abstract void run();
}
```  
使用Runnable实例在创建一个Thread实例，然后在调用Thread中的start()的方法来启动线程。  
```java
public static void main(String[] args) {
 MyRunnable instance = new MyRunnable();
 Thread thread = new Thread(instance);
 thread.start();
}
``` 
#### 实现Collable接口  
&emsp;与Runnable接口相比，Collable接口是有返回值的，在实现一些小的工具时，比如计时等，不需要那么复杂的线程，异步获取结果。需要配合feature进行使用，返回值通过 FutureTask 进⾏封.  
&emsp;Callable的定义也非常简单，只有一个call函数，和Runnable中的run不同的是，call是带返回值的。单纯Callable是不能被Thread执行的（因为Thread调用的是run函数），所以还需要Future， Future的get函数可以获取到call的返回值。
```java
public class MyCallable implements Callable<Integer> {
 public Integer call() {
 return 123;
 }
}
```
```java
public static void main(String[] args) throws ExecutionException,
InterruptedException {
 MyCallable mc = new MyCallable();
 FutureTask<Integer> ft = new FutureTask<>(mc);
 Thread thread = new Thread(ft);
 thread.start();
 System.out.println(ft.get());
}
```

#### 继承Thread类  
同样是要实现run()方法，因此Thread类也实现了Runnable接口。  
当调用了start()方法时虚拟机将会将该线程放入就绪队列中等待被调度，当一个线程被调度的时候就会执行run()方法。

#### 实现接口 VS 继承Thread
实现接口更好一点，原因：  
* Java不支持多重继承，因此继承了Thread类就无法继承其他的类了， 但是可以实现多个接口。
* 类只要求可执行就可以，继承整个Thread的开销太过高昂。  

# 二、基础线程机制  
## Executor
Executor管理多个异步任务的执行，无需程序员显示的管理线程的生命周期。这里的异步是指多个任务的执行互不干扰，不需要同步的操作。  
主要有三种Executor：  
* CachedThreadPool：⼀个任务创建⼀个线程；
* FixedThreadPool：所有任务只能使⽤固定⼤⼩的线程；
* SingleThreadExecutor：相当于⼤⼩为 1 的 FixedThreadPool；
```JAVA
public static void main(String[] args) {
 ExecutorService executorService = Executors.newCachedThreadPool();
 for (int i = 0; i < 5; i++) {
 executorService.execute(new MyRunnable());
 }
 executorService.shutdown();
}
```  
## Deamom  
守护线程十程序运