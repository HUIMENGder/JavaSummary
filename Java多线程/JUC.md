### 一、概念
* 进程（Process），是系统分配资源的单位  
* 线程（Thread）：是cup调度和执行的单位
* 
### 二、线程的创建
#### 继承Thread类
* 自定义线程继承Thread类
* 重写run()方法，编写线程执行体
* 创建线程对象，调用start()方法启动线程  
```Java
public class TestThread1 extends Thread{
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println("我在看代码---" + i);
        }
    }
    public static void main(String[] args) {
        //创建一个线程对象
        TestThread1 testThread1 = new TestThread1();
        testThread1.start();
        // 两个线程会同时运行，交替并行，CPU调度执行
        for (int i = 0; i < 20; i++) {
            System.out.println("我在学习多线程--" + i);
        }
    }
}
```
注意 ：线程开始不一定立即执行，由CPU调度执行

**实例:实现多张图片下载**
```Java
import org.apache.commons.io.FileUtils;

import java.io.File;
import java.io.IOException;
import java.net.URL;

/**
 * Created with IntelliJ IDEA.
 * 联系Thread，实现多线程下载图片
 * @Author: 回梦
 * @Date: 2021/11/07/22:37
 */
public class TestThread2 extends Thread {
    private String url;
    private String name;

    public TestThread2(String url, String name){
        this.url = url;
        this.name = name;
    }
    //下载线程执行体
    @Override
    public void run() {
        WebDownLoader webDownLoader = new WebDownLoader();
        webDownLoader.downLoader(url,name);
        System.out.println("下载了名为:" + name + "的图片");
    }

    public static void main(String[] args) {
        //
        TestThread2 t1 = new TestThread2("https://img-blog.csdnimg.cn/20181115113136127.png","test1.jpg");
        TestThread2 t2 = new TestThread2("https://img-blog.csdnimg.cn/20181115113136127.png","test2.jpg");
        t1.start();
        t2.start();
    }
}
//下载器
class WebDownLoader{
    //下载方法
    public void downLoader(String url, String name){
        try {
            FileUtils.copyURLToFile(new URL(url), new File(name));
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println("IO异常，downLoader方法问题！");
        }
    }
}
```

#### 实现 Runnable 接口
* 定义 MyRunnable 类实现Runnable接口
* 实现run()方法，编写线程执行体
* 创建线程对象，调用start()方法
```Java
/**
 * Created with IntelliJ IDEA.
 * 实现Runnable接口，创建线程
 * @Author: 回梦
 * @Date: 2021/11/08/9:51
 */
public class TestThread3 implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println("我在看代码---" + i);
        }
    }
    public static void main(String[] args) {
        //创建Runnable接口实现类
        TestThread3 testThread3 = new TestThread3();
        // 创建线程对象，通过线程对象开始我们的线程，代理
        Thread thread = new Thread(testThread3);
        thread.start();
        /*new Thread(testThread3).start();*/
        // 两个线程会同时运行，交替并行，CPU调度执行
        for (int i = 0; i < 20; i++) {
            System.out.println("我在学习多线程--" + i);
        }
    }
}
```
**实例：创建多个线程控制一个对象**
```Java
import javax.sound.midi.Soundbank;

/**
 * Created with IntelliJ IDEA.
 * 多个线程同时操作一个对象，买车票
 * @Author: 回梦
 * @Date: 2021/11/08/10:11
 */
public class TestThread4 implements Runnable{
    private int ticketNums = 10;
    @Override
    public void run() {
        while (true){
            if (ticketNums <= 0){
                break;
            }
            System.out.println(Thread.currentThread().getName() + "----> 拿到了第" + ticketNums + "张票");
        }
    }

    public static void main(String[] args) {
        TestThread4 ticket = new TestThread4();
        new Thread(ticket,"张三").start();
        new Thread(ticket,"李四").start();
        new Thread(ticket,"王五").start();
    }
}
```

#### 实现Callable接口
* 实现Callable接口，需要返回值类型
* 重写call()方法，需要抛出异常
* 创建目标对象
* 创建执行  
```Java
  ExecutorService ser = Executors.newFixedThreadPool();
```
* 提交执行
```Java
Future<Boolean> r1 = ser.submit(t1);
```
* 获取结果
```Java
boolean rs1 = r1.get();
```
* 关闭服务
```Java
ser.shutdown();
```
**实例：**
```Java
import org.apache.commons.io.FileUtils;

import java.io.File;
import java.io.IOException;
import java.net.URL;
import java.util.concurrent.*;

/**
 * Created with IntelliJ IDEA.
 * 实现Callable接口，创建线程
 * @Author: 回梦
 * @Date: 2021/11/08/10:29
 */
public class TestCallable implements Callable<Boolean> {
    private String url;
    private String name;

    public TestCallable(String url, String name){
        this.name = name;
        this.url = url;
    }
    @Override
    public Boolean call(){
        WebDownLoad webDownLoad = new WebDownLoad();
        webDownLoad.downLoader(url,name);
        System.out.println("下载了文件名为：" + name);
        return true;
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        TestCallable t1 = new TestCallable("https://img-blog.csdnimg.cn/20181115113136127.png","test1.jpg");
        TestCallable t2 = new TestCallable("https://img-blog.csdnimg.cn/20181115113136127.png","test2.jpg");
        //创建执行服务
        ExecutorService ser = Executors.newFixedThreadPool(3);
        //提交执行
        Future<Boolean> r1 = ser.submit(t1);
        Future<Boolean> r2 = ser.submit(t2);
        //获取结果
        boolean rs1 = r1.get();
        boolean rs2 = r2.get();
        //关闭服务
        ser.shutdown();

    }
}
class WebDownLoad {
    //下载方法
    public void downLoader(String url, String name){
        try {
            FileUtils.copyURLToFile(new URL(url), new File(name));
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println("IO异常，downLoader方法问题！");
        }
    }
}
```
### 静态代理模式
* 真实对象和代理对象都要实现同一个接口
* 代理对象中有真实的角色，参数传入  

**优点：**
* 代理对象可以做很多真实对象做不了的事情
* 真实对象可以专注做自己的事情
  
### lambda表达式
```Java
/**
 * Created with IntelliJ IDEA.
 * 推导 lambda 表达式
 * @Author: 回梦
 * @Date: 2021/11/09/10:06
 */
public class TestLambda {
    // 3.0 静态内部类
   static  class Like2 implements ILike{
        @Override
        public void lambda() {
            System.out.println("I like lambda2!");
        }
    }

    public static void main(String[] args) {
        Like like1 = new Like();
        like1.lambda();

        Like2 like2 = new Like2();
        like2.lambda();

        // 4.0局部内部类
        class Like3 implements ILike{
            @Override
            public void lambda() {
                System.out.println("I like lambda3!");
            }
        }
       Like3 like3 = new Like3();
       like3.lambda();

       //5.0匿名内部类
        ILike like4 = new ILike() {
            @Override
            public void lambda() {
                System.out.println("I like lambda4!");
            }
        };
        like4.lambda();

        //6.0用lambda简化
        ILike like5 = () -> {
            System.out.println("I like lambda5!");
        };
        like5.lambda();
    }
}
//1.定义一个函数式接口
interface ILike{
    void lambda();
}

//2.实现类
class Like implements ILike{
    @Override
    public void lambda() {
        System.out.println("I like lambda1!");
    }
}
```  
### 线程的状态
* new(新生状态)：线程一旦创建就进入到新生状态
* 就绪状态：调用start()方法，线程立即进入就绪状态，但是不意味立即进行调度执行
* 阻塞状态，当调用sleep，wait或同步锁定时线程进入阻塞状态，程序不在运行，阻塞停止后，重新进入就绪状态，等待CPU调度。
* 运行状态：线程真正执行线程体的代码块
* 死亡状态：线程中断或者结束，一旦进入死亡状态就不能再次启动

#### 线程方法
|方法|说明|
| --| --|
|setPriority(int newPriority)|更改线程的优先级|
|static void sleep(long millis)|在指定的毫秒时间内让正在执行的线程体休眠|
|void jion()|等待该线程终止|
|static void yield()|暂停当前的线程体，并执行其他线程|
|void interrupt()|中断这个线程，别用|
|boolean isAlive()|测试线程是否在活跃状态|
**利用标志位，停止线程**
```Java
/**
 * Created with IntelliJ IDEA.
 * 测试线程停止
 * 1.建议线程自动停止---> 利用次数，不建议死循环
 * 2.建议使用标志位，--->设置一个标志位
 * 3.不要使用stop，destroy等JDK不建议使用的方法
 * @Author: 回梦
 * @Date: 2021/11/10/20:03
 */
public class TestStop implements Runnable{
    //1.设置一个标志位

    private boolean flag = true;

    @Override
    public void run() {
        int i = 0;
        while (flag){
            System.out.println("run.....Thread" + i++);
        }
    }
    //2.设置一个公开的方法停止线程，转换标志位

    public void stop(){
        this.flag = false;
    }
    public static void main(String[] args) {
        TestStop testStop = new TestStop();
        new Thread(testStop).start();
        for (int i = 0; i < 1000; i++) {
            System.out.println("main" + i);
            if(i == 900){
                //调用stop方法切换标志位，停止线程
                testStop.stop();
                System.out.println("线程停止了....");
            }
        }
    }
}
```
**Sleep测试，获取当前时间**
```Java
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * Created with IntelliJ IDEA.
 * 测试sleep，获取当前系统时间
 * @Author: 回梦
 * @Date: 2021/11/10/20:21
 */
public class TestSleep {
    public static void main(String[] args) {
        Date StartTime = new Date(System.currentTimeMillis());
        while (true){
            try {
                Thread.sleep(1000);
                System.out.println(new SimpleDateFormat("HH:mm:ss").format(StartTime));
                StartTime = new Date(System.currentTimeMillis());//更新当前时间
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }
    }
}
```
**线程礼让：**
* 让当前的线程停止，但不阻塞
* 将线程从运行状态转化为就绪状态
* **让CPU重新调度，礼让不一定成功！**
```Java
/**
 * Created with IntelliJ IDEA.
 * 测试礼让线程
 * @Author: 回梦
 * @Date: 2021/11/10/20:32
 */
public class TestYield {
    public static void main(String[] args) {
        MyYield myYield = new MyYield();
        new Thread(myYield,"a").start();
        new Thread(myYield,"b").start();
    }
}
class MyYield implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"线程开始执行！");
        Thread.yield();
        System.out.println(Thread.currentThread().getName()+"线程停止执行！");
    }
}
```
**Join()合并线程**
* 待此线程执行完后，在执行其他的线程，其他线程阻塞
* 可以想象为插队
```Java
/**
 * Created with IntelliJ IDEA.
 * 测试Join()
 * @Author: 回梦
 * @Date: 2021/11/10/20:41
 */
public class TestJoin implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 1000; i++) {
            System.out.println("线程vip来了..." + i);
        }
    }

    public static void main(String[] args) throws InterruptedException {
        TestJoin testJoin = new TestJoin();
        Thread thread = new Thread(testJoin);
        thread.start();
        // 主线程
        for (int i = 0; i < 500; i++) {
            if(i == 200){
                thread.join();//插队
            }
            System.out.println("main" + i);
        }
    }
}
```
**线程的状态(Thread.State)：**
```Java
import javax.swing.plaf.nimbus.State;

/**
 * Created with IntelliJ IDEA.
 * 观察并测试线程状态
 * @Author: 回梦
 * @Date: 2021/11/10/21:40
 */
public class TestState {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() ->{
            for (int i = 0; i < 5; i++) {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("//////");
        });
        // 观察状态
        Thread.State state = thread.getState();
        System.out.println(state); //New

        //观察启动
        thread.start();
        state = thread.getState();
        System.out.println(state);

        while(state != Thread.State.TERMINATED){
            Thread.sleep(100);
            state = thread.getState(); //跟新线程状态
            System.out.println(state);
        }
    }
}
```

**线程优先级：**
* getPriority()
* setPriority()
```Java
/**
 * Created with IntelliJ IDEA.
 * 测试线程的优先级
 * @Author: 回梦
 * @Date: 2021/11/12/10:23
 */
public class TestPriority {
    public static void main(String[] args) {
        System.out.println(Thread.currentThread().getName() + "----->" + Thread.currentThread().getPriority());
        MyPriority myPriority = new MyPriority();
        Thread t1 = new Thread(myPriority);
        Thread t2 = new Thread(myPriority);
        Thread t3 = new Thread(myPriority);
        Thread t4 = new Thread(myPriority);

        //先设置优先级在启动
        t1.setPriority(1);
        t2.setPriority(2);
        t3.setPriority(3);
        t4.setPriority(4);
        t1.start();
        t2.start();
        t3.start();
        t4.start();

    }
}
class MyPriority implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "----->" + Thread.currentThread().getPriority());
    }
}
```
**守护线程：**
* GC垃圾回收，日志管理等
```Java
/**
 * Created with IntelliJ IDEA.
 * 测试守护线程
 * @Author: 回梦
 * @Date: 2021/11/12/10:34
 */
public class TestDaemon {
    public static void main(String[] args) {
        God god = new God();
        You you = new You();

        Thread thread = new Thread(god);
        thread.setDaemon(true); //默认是false是用户线程，正常的线程都是用户线程
        thread.start();
        new Thread(you).start();
    }
}

class God implements Runnable{
    @Override
    public void run() {
        while (true){
            System.out.println("上帝活着！");
        }
    }
}

class You implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 36500; i++) {
            System.out.println("活着就很开心！");
        }
        System.out.println("goodbye!");
    }
}
```
### 线程同步
* 线程同步其实就是一种等待机制，多个需要同时访问此对象的线程进入这个对象的等待池，等前面一个线程使用完，下一个线程在使用。
* 解决访问冲入问题，加入的锁机制 ***synchornized***,当一个线程获得了对象的排他锁，独占资源，其他线程必须等待，使用后释放锁即可。但是会存在以下问题：
  * 一个线程持有锁会导致其他需要使用该锁的进程挂起
  * 在多线竞争的情况下，加锁，释放锁会导致比较多的上下文切换和调度时延，引起性能问题。
  * 如果一个优先级较高的线程等待一个优先级较低的线程释放锁会导致性能倒置。    

***synchornized:***
* 锁的是this
* **synchornized(Obj){}** 同步块
  * Obj 称之为同步监视器
    *  Obj可以是任何对象
    *  同步方法中无需指定同步监视器，因为同步方法的同步监视器就是this，就是对象本身。
    * 锁的对象就是变化的量，需要增删改的对象
```Java
/**
 * Created with IntelliJ IDEA.
 * synchronized保证线程安全
 * @Author: 回梦
 * @Date: 2021/11/12/20:04
 */
public class UnsafeBuyTicket {
    public static void main(String[] args) {
         BuyTicket station = new BuyTicket();
         new Thread(station,"1").start();
         new Thread(station,"2").start();
         new Thread(station,"3").start();
    }
}

class BuyTicket implements Runnable{
    private int ticketNums = 20;
    boolean flag = true;
    @Override
    public void run() {
        //买票
        while (flag){
            try {
                buy();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    //synchronized 同步方法，锁的是this

    public synchronized void buy() throws InterruptedException {
        //判断是否有票
        if(ticketNums <= 0){
            flag = false;
            return;
        }
        Thread.sleep(100);
        System.out.println(Thread.currentThread().getName() + "拿到" + ticketNums --);
    }
}
```

### 死锁
* 多线程各自占用一些共享资源，并且相互等待其他线程占用的资源才能运行，而导致两个或者多个线程都在等待对方释放资源，都停止运行的情况，某一个同步块同时拥有两个对象以上的锁时，就可能发生死锁现象。
  
### Lock


### 线程通信(生产者消费者模型)
**Java中线程通信的方法：**
|方法名|作用|
| -- | -- |
|wait()|表示线程一直等待，等到其他线程通知，与sleep不同，会释放锁|
|wait(long timeout)|等待指定的毫秒数|
|notify()|唤醒一个处于等待状态的线程|
|notifyAll()|唤醒同一个对象上所有调用wait()方法的线程,优先级别高的线程优|

**实现生产者消费者模型方法一：管程法**
```Java
/**
 * Created with IntelliJ IDEA.
 * 测试生产者消费者模型--->利用缓冲区解决：管程法
 * @Author: 回梦
 * @Date: 2021/11/15/15:01
 */
public class TestPc {
    public static void main(String[] args) {
        SynContainer synContainer = new SynContainer();
        new Productor(synContainer).start();
        new Consumer(synContainer ).start();
    }
}

//生产者

class Productor extends Thread{
    SynContainer synContainer;
    public Productor(SynContainer synContainer){
        this.synContainer = synContainer;
    }

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            synContainer.push(new Chicken(i));
            System.out.println("生产了-->" + i + "只鸡");
        }
    }
}

//消费者

class Consumer extends Thread{
    SynContainer synContainer;
    public Consumer(SynContainer synContainer){
        this.synContainer = synContainer;
    }

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("消费了-->" + synContainer.pop().id + "只鸡");

        }
    }
}

//产品

class Chicken{
    int id;

    public Chicken(int id) {
        this.id = id;
    }
}

//缓冲区

class SynContainer{
    // 容器大小和计数器

    Chicken[] chickens = new Chicken[10];
    int count = 0;
    // 生产者放入产品

    public synchronized void  push(Chicken chicken){
        //如果容器满了。就需要等待消费者消费
        if(count == chickens.length){
            //通知消费者消费，生产者等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        //如果容器没满，我们就需要丢入产品
        chickens[count] = chicken;
        count ++;
        //可以通知消费者消费
        this.notifyAll();
    }

    public synchronized Chicken pop(){
        //判断能否消费
        if(count == 0){
            //等待生产者生产，消费者等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        //count是指向最新可插入的地方，上面加入是先加入在count++，所以count要先减再取
        count --;
        Chicken chicken = chickens[count];
        //吃完通知生产者生产
        this.notifyAll(); 
        return chicken;
    }
}
```
**实现生产者消费者模型方法二：信号灯法**
```Java
/**
 * Created with IntelliJ IDEA.
 *  测试生产者消费者问题2--->信号灯法,标志位解决
 * @Author: 回梦
 * @Date: 2021/11/15/21:48
 */
public class TestPc2 {
    public static void main(String[] args) {
        Tv tv = new Tv();
        new Player(tv).start();
        new Watcher(tv).start();
    }
}

/**
 * 生产者--->演员
 */
class Player extends Thread{
    Tv tv;
    public Player(Tv tv){
        this.tv = tv;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            if(i%2 == 0){
                this.tv.play("hhhh播放中");
            }else {
                this.tv.play("aaaa");
            }
        }
    }
}

/**
 * 消费者--->观众
 */
class Watcher extends Thread{
    Tv tv;
    public Watcher(Tv tv){
        this.tv = tv;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            tv.watch();
        }
    }
}

/**
 * 产品--->节目
 */
class Tv{

    //演员表演，观众等待
    //观众等待，演员表演

    String voice;
    boolean flag = true;

    //表演
    public synchronized void play(String voice){
        if (!flag){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("演员表演了:" + voice);
        //通知观众观看
        this.notifyAll();
        this.flag = !this.flag;
        this.voice = voice;
    }
    // 观看
    public synchronized void watch(){
        if (flag){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("观看了：" + voice);
        //通知演员表演
        this.notifyAll();
        this.flag = !this.flag;
    }
}
```
### 线程池
```Java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * Created with IntelliJ IDEA.
 * 测试线程池
 * @Author: 回梦
 * @Date: 2021/11/16/19:27
 */
public class TestPool {
    public static void main(String[] args) {
        //1.创建服务，创建线程
        //newFixedThreadPool参数为u线程池的大小
        ExecutorService service = Executors.newFixedThreadPool(10);
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());

        service.shutdown();
    }
}

class MyThread implements Runnable {
    @Override
    public void run() {
            System.out.println(Thread.currentThread().getName());
        }
}
```