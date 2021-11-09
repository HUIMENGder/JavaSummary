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