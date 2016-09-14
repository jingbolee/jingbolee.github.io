---
layout:     post
title:      "Java 多线程使用 "
subtitle:   "多线程生命周期，属性和方法的使用"
date:       2016-09-12
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## 多线程

### 创建多线程

- 继承 Thread 类，重写 run() 方法，在 run() 方法中定义需要执行的任务，然后通过 start() 方法启动线程。注意：调用 run() 方法相当于在主线程中执行 run() 方法，跟普通调用方法没有区别，并不会创建新的线程来执行任务的。

		public class TestThread {
		
		    public static void main(String[] args) {
		        MyThread mt = new MyThread();
		        mt.start();
		    }
		}
		
		class MyThread extends Thread {
		    private static int num = 0;
		
		    public MyThread() {
		        num++;
		    }
		
		    @Override
		    public void run() {
		        System.out.println("继承 Thread 实现多线程,创建的"+num+"线程");
		    }
		}

- 实现 Runnable 接口，重写 run() 方法。然后把 Runnable 作为 Thread 的参数，通过 Thread 的 start() 方法执行该任务。

		public class TestRunnable {
		
		    public static void main(String[] args){
		        System.out.println("主线程ID:"+Thread.currentThread().getId());
		        MyRunnable mr = new MyRunnable();
		        Thread thread = new Thread(mr);
		        thread.start();
		    }
		}
		
		class MyRunnable implements Runnable{
		    @Override
		    public void run() {
		        System.out.println("子线程ID:"+Thread.currentThread().getId());
		    }
		}

- 查看 Thread 类的实现源码，发现 Thread 类实现了 Runnable 接口
- Java 中，两种方式都可以用来创建多线程去执行子任务，看具体需求实现。

### Thread 类的使用

#### 线程生命周期

- 线程的生命周期分为：
	- 创建（new）
	- 就绪（runnable）
	- 运行（running）
	- 阻塞（blocked）
	- time waiting
	- waiting
	- 消亡（dead）

- 当需要新起一个线程来执行某个子任务时，就创建了一个线程，但是线程创建之后，不会立即进入就绪状态，因为线程的运行需要一些条件（比如内存资源），只有线程运行需要的所有条件满足了，才进入就绪状态。
- 当线程进入就绪状态后，不代表立刻就能获取 CPU 执行时间，也许此时 CPU 正在执行其他的事情，因此要等待，当得到 CPU 执行时间之后，线程便真正进入运行状态。
- 线程在运行状态过程中，可能有多个原因导致当前线程不继续运行下去，比如用户主动让线程睡眠（睡眠一定时间之后再重新执行），用户主动让线程等待，或者被同步块给阻塞，此时就对应着多个状态：time waiting(睡眠或等待一定的时间)，waiting(等待被唤醒)，blocked(阻塞).
- 当由于突然中断或者子任务执行完毕，线程就会被消亡。
- time waiting(睡眠或等待一定的时间)，waiting(等待被唤醒)，blocked(阻塞) 都是阻塞状态。

	![](http://i.imgur.com/g6kZWc8.jpg)


#### 上下文切换

- 对于单核 CPU 来说，CPU 在一个时刻只能运行一个线程，当在运行一个线程的过程中转去运行另外一个线程，这个叫做线程上下文切换。
- 对于线程的上下文切换实际上就是 存储和恢复 CPU 状态的过程，它使得线程执行能够从中断点恢复执行。
- 多线程可以使得任务执行的效率得到提示，但是由于在线程切换时同样会带来一定的开销代价，并且多个线程会导致系统资源占用的增加，所以在多线程开发时，注意该问题。



#### Thread 类

	public
	class Thread implements Runnable {
	    /* Make sure registerNatives is the first thing <clinit> does. */
	    private static native void registerNatives();
	    static {
	        registerNatives();
	    }
	
	    private volatile char  name[];
	    private int            priority;
	    private Thread         threadQ;
	    private long           eetop;
	
	    /* Whether or not to single_step this thread. */
	    private boolean     single_step;
	
	    /* Whether or not the thread is a daemon thread. */
	    private boolean     daemon = false;
	
	    /* JVM state */
	    private boolean     stillborn = false;
	
	    /* What will be run. */
	    private Runnable target;
	
	    /* The group of this thread */
	    private ThreadGroup group;

- Thread 类实现了 Runnable 接口，其中有一些关键属性，如 	
	- name 表示 Thread 的名字，可以通过 Thread 类的构造器中的参数来设置
	- priority 表示线程优先级（最大10，最小1，默认5）
	- daemon 表示线程是否是守护线程
	- target 表示要执行的任务。

- 与属性相关的方法

	- getId()：用来得到线程ID

	- getName() 和 setName()：用来得到或者设置线程名称。

	- getPriority() 和 setPriority()：用来获取和设置线程优先级。优先级：最低1，最大10，一般是5

	- setDaemon() 和 isDaemon()：用来设置线程是否成为守护线程和判断线程是否是守护线程。

	- currentThread()：Thread 类的静态方法，获取当前进程。

- 与运行状态相关的方法

	- start()：启动一个线程，当调用 start() 方法以后，系统才会开启一个新的线程来执行用户定义的子任务，在这个过程中，会为相应的线程分配需要的资源。
	- run()：在多线程中，run() 方法不需要用户调用，用户通过 start() 方法启动一个线程之后，当线程获取到 CPU 执行时间，便进入 run() 方法执行具体的任务。
	- sleep()：相当于让线程睡眠，交出 CPU，让 CPU 去执行其他的任务。会抛出 InterruptedException 异常。
		- 有2个重载方法：
			- sleep(long millis)
			- sleep(long millis, int nanos)
	
		> 注意： sleep() 方法不会释放锁，也就是说如果当前线程持有某个对象的锁，即使调用了 sleep() 方法，其他线程也无法获取这个对象。当线程睡眠时间满后，不一定会立即得到执行，CPU 有可能正在执行其他的任务，所以说 sleep 方法相当于让线程进入阻塞状态。

		- 测试代码：
		
				public class SleepTest {
				    private int i = 10;
				
				    private Object object = new Object();
				
				    public static void main(String[] args) {
				        SleepTest st = new SleepTest();
				        MyThread myThread1 = st.new MyThread();
				        MyThread myThread2 = st.new MyThread();
				        myThread1.start();
				        myThread2.start();
				    }
				
				    class MyThread extends Thread {
				        @Override
				        public void run() {
				            synchronized (object) {
				                i++;
				                System.out.println("i:" + i);
				                try {
				                    System.out.println("线程：" + Thread.currentThread().getName() + "执行睡眠操作");
				                    Thread.sleep(1000);
				                } catch (InterruptedException e) {
				                    e.printStackTrace();
				                }
				                System.out.println("线程：" + Thread.currentThread().getName() + "睡眠结束");
				                i++;
				                System.out.println("i:" + i);
				            }
				        }
				    }
				}

		- 测试结果：（因为加锁的原因，Thread-1 无法获取到对象锁，无法执行，所以只能等待 Thread-0 执行完成并释放锁以后才能执行 Thread-1 的任务）

				i:11
				线程：Thread-0执行睡眠操作
				线程：Thread-0睡眠结束
				i:12
				i:13
				线程：Thread-1执行睡眠操作
				线程：Thread-1睡眠结束
				i:14


	- yield()：让当前线程交出 CPU 权限，让 CPU 去执行其他的线程，它和 sleep() 方法类似，同样无法释放锁，但是 yield() 不能控制具体的交出 CPU 的时间，另外，yield() 方法只能让拥有相同优先级的线程有获取 CPU 执行时间的机会。
		
		> 调用 yield() 方法不会让线程进入阻塞状态，而是让线程重回就绪状态，它只需要等待重新获取 CPU 执行时间。


	- join()：假如在 main 线程中，调用 thead.join() 方法，则 main 线程会等待 thread 线程执行完毕或者等待一定时间。假如调用无参 join() 方法，则等待 thread 执行完毕，如果调用了指定时间参数的 join 方法，则等待一定的时间。会抛出 InterruptedException 异常。
		- 有三个重载方法：
			- join()
			- join(long millis)
			- join(long millis, int nanos)

		- 测试代码

				public class JoinTest {
				
				    public static void main(String[] args) {
				        System.out.println("线程：" + Thread.currentThread().getName() + "开始执行");
				        JoinTest jt = new JoinTest();
				        MyThread thread1 = jt.new MyThread();
				        thread1.start();
				        try {
				            thread1.join(1000);
				        } catch (InterruptedException e) {
				            e.printStackTrace();
				        }
				        System.out.println("线程：" + Thread.currentThread().getName() + "开始执行");
				    }
				
				
				    class MyThread extends Thread {
				        @Override
				        public void run() {
				                try {
				                    System.out.println("线程：" + Thread.currentThread().getName() + "执行睡眠操作");
				                    Thread.sleep(2000);
				                } catch (InterruptedException e) {
				                    e.printStackTrace();
				                }
				                System.out.println("线程：" + Thread.currentThread().getName() + "睡眠结束");
				        }
				    }
				}

		- 测试结果：
	
				线程：main开始执行
				线程：Thread-0执行睡眠操作
				线程：Thread-0睡眠结束
				线程：main开始执行

		- 测试代码2（与 sleep 结合了解）

				public class JoinTest {
				
				    private Object object = new Object();
				
				    public static void main(String[] args) {
				        System.out.println("线程：" + Thread.currentThread().getName() + "开始执行");
				        JoinTest jt = new JoinTest();
				        MyThread thread1 = jt.new MyThread();
				        MyThread thread2 = jt.new MyThread();
				        thread1.start();
				        thread2.start();
				        try {
				            thread1.join();
				        } catch (InterruptedException e) {
				            e.printStackTrace();
				        }
				        System.out.println("线程：" + Thread.currentThread().getName() + "开始执行");
				    }
				
				
				    class MyThread extends Thread {
				        @Override
				        public void run() {
				            synchronized (object) {
				                try {
				                    System.out.println("线程：" + Thread.currentThread().getName() + "执行睡眠操作");
				                    Thread.sleep(2000);
				                } catch (InterruptedException e) {
				                    e.printStackTrace();
				                }
				                System.out.println("线程：" + Thread.currentThread().getName() + "睡眠结束");
				            }
				        }
				    }
				}

		- 测试结果：

				线程：main开始执行
				线程：Thread-0执行睡眠操作
				线程：Thread-0睡眠结束
				线程：Thread-1执行睡眠操作
				线程：main开始执行
				线程：Thread-1睡眠结束

		- 通过以上2个测试用例，可以看到当调用了 thread.join() 方法后，main 线程会进入等待，等待 thread 执行完之后继续执行。
		- 实际上调用 join() 方法是调用了 Object 的 wait() 方法
		- wait() 方法会让线程进入阻塞状态，并且会释放线程占有的锁，并交出 CPU 执行权限。
		- join() 方法与 wait() 方法一样，会让线程是否对象锁。
		- 源码

			    public final synchronized void join(long millis)
			    throws InterruptedException {
			        long base = System.currentTimeMillis();
			        long now = 0;
			
			        if (millis < 0) {
			            throw new IllegalArgumentException("timeout value is negative");
			        }
			
			        if (millis == 0) {
			            while (isAlive()) {
			                wait(0);
			            }
			        } else {
			            while (isAlive()) {
			                long delay = millis - now;
			                if (delay <= 0) {
			                    break;
			                }
			                wait(delay);
			                now = System.currentTimeMillis() - base;
			            }
			        }
			    }


	- interrupt()：中断的意思，
		- 单独调用 interrupt() 方法可以使得处于阻塞状态的线程抛出一个异常，也就是说，它可以用来中断一个正处于阻塞状态的线程；		
		- 当线程处于运行状态，调用 interrupt() 时，不会中断该线程。
		- 通过方法 interrupt() 和 isInterrupted() 来停止正在运行的线程。

		- 测试代码（中断处于阻塞状态的线程）：

				public class InterruptTest {
				
				    public static void main(String[] args){
				        InterruptTest it = new InterruptTest();
				        MyThread thread = it.new MyThread();
				        thread.start();
				        try {
				            Thread.sleep(2000);
				        } catch (InterruptedException e) {
				            e.printStackTrace();
				        }
				        thread.interrupt();
				    }
				
				    class MyThread extends Thread{
				        @Override
				        public void run() {
				            try {
				                System.out.println("睡眠操作");
				                Thread.sleep(10000);
				                System.out.println("睡眠结束");
				            } catch (InterruptedException e) {
				                System.out.println("抛出异常");
				            }finally {
				                System.out.println("run() 方法执行完毕"); 
				            }
				        }
				    }
				}

		- 测试结果

				睡眠操作
				抛出异常
				run() 方法执行完毕

		- 测试代码2（无法中断处于运行状态的线程）

				public class InterruptTest {
				
				    public static void main(String[] args){
				        InterruptTest it = new InterruptTest();
				        MyThread thread = it.new MyThread();
				        thread.start();
				        try {
				            Thread.sleep(1000);
				        } catch (InterruptedException e) {
				            e.printStackTrace();
				        }
				        thread.interrupt();
				    }
				
				    class MyThread extends Thread{
				        @Override
				        public void run() {
				            while(true){
				                System.out.println("while 循环操作");
				            }
				        }
				    }
				}

		- 测试结果：

				没有中断，一直打印"while 循环操作" 

		- 测试代码3（通过把 isInterrupt() 设置为一个标识位，使正处于运行状态的线程处于中断，当调用 interrupt() 方法时，会设置状态为 true，然后通过 isInterrupt() 方法获取到的属性值为 true了，然后 while 循环就无法循环了。）
			
				public class InterruptTest {
				
				    public static void main(String[] args){
				        InterruptTest it = new InterruptTest();
				        MyThread thread = it.new MyThread();
				        thread.start();
				        try {
				            Thread.sleep(1000);
				        } catch (InterruptedException e) {
				            e.printStackTrace();
				        }
				        thread.interrupt();
				    }
				
				    class MyThread extends Thread{
				        @Override
				        public void run() {
				            while(!isInterrupted()){
				                System.out.println("while 循环操作");
				            }
				        }
				    }
				}

		- 测试结果：
			
				执行一段时间以后，不再打印。

		- 测试代码4（通过设置一个标识位，然后在外部通过修改这个标识位的状态来改变循环状态，从而停止子线程的运行）：

				public class InterruptTest {
				
				    public static void main(String[] args) {
				        InterruptTest it = new InterruptTest();
				        MyThread thread = it.new MyThread();
				        thread.start();
				        try {
				            Thread.sleep(1000);
				        } catch (InterruptedException e) {
				            e.printStackTrace();
				        }
				        thread.isRunning = false;
				    }
				
				    class MyThread extends Thread {
				        volatile boolean isRunning = true;
				
				        @Override
				        public void run() {
				            while (isRunning) {
				                System.out.println("while 循环操作");
				            }
				        }
				    }
				}


	- stop()：是一个已经废弃的方法，用来停止线程。不建议使用该方法
	- destory()：也是一个废弃的方法，不建议使用。

#### 正确结束线程

- 当子线程采用循环的时候，可以设置一个标识位，通过修改该标识位的属性，来改变是否结束子线程的循环。

- 测试代码：

		public class InterruptTest {
		
		    public static void main(String[] args) {
		        InterruptTest it = new InterruptTest();
		        MyThread thread = it.new MyThread();
		        thread.start();
		        try {
		            Thread.sleep(1000);
		        } catch (InterruptedException e) {
		            e.printStackTrace();
		        }
		        thread.isRunning = false;
		    }
		
		    class MyThread extends Thread {
		        volatile boolean isRunning = true;
		
		        @Override
		        public void run() {
		            while (isRunning) {
		                System.out.println("while 循环操作");
		            }
		        }
		    }
		}


### 多线程生命周期概括

![](http://i.imgur.com/mv055hy.jpg)



> 转自：（海子：http://www.cnblogs.com/dolphin0520/category/602384.html）