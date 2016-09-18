---
layout:     post
title:      "Java 多线程: 线程安全、同步、互斥"
subtitle:   "线程安全、同步、互斥"
date:       2016-09-18
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## Java 多线程

### 线程安全的产生

- 在单线程的情况下，不会出现线程安全。当运行在多线程的情况下，需要访问同一个资源的情况下，可能会存在线程安全。
- 当多个线程同时访问临界资源（也称为共享资源，可以是一个对象，对象中的属性，一个文件，一个数据库等）时，就可能会产生线程安全问题。


### 线程安全的解决-同步互斥

- 基本上所有的开发模式在解决线程安全问题时，都采用“序列化访问临界资源”的方案，即**在同一时刻，只能有一个线程访问临界资源，也称作同步互斥访问**。
- 通常来说，是在访问临界资源的代码前面加上一个锁，当访问完临界资源后释放锁，让其他线程继续访问。
- 在 Java 中，提供了两种方式来实现同步互斥访问： **synchronized** 和 **Lock**

### synchronized 

- 互斥锁：在临界资源上加上互斥锁，当一个线程在访问该临界资源时，其他线程便只能等待。
- 在 Java 中，**每一个对象都拥有一个锁标记（ monitor），也成为监视器**，多线程同时访问某个对象时，线程只有获取了该对象的锁才能访问
- 在 Java 中，可以使用 synchronized 关键字来标记一个方法或者代码块，当某个线程调用该对象的 synchronized 方法或者访问 synchronized 代码块时，这个线程便获取了该对象的锁，其他线程暂时无法访问这个方法，只有等待这个方法执行完毕或者代码块执行完毕，这个线程才会释放该对象的锁，其他线程才能执行这个方法或者代码块。
- 没有使用 synchronized 关键字修饰方法
	
	- InsertData.java

			public class InsertData {
			    private ArrayList<Integer> datas = new ArrayList<>();
			
			    public void insert(Thread thread) {
			        for (int i = 0; i < 5; i++) {
			            System.out.println(thread.getName() + "插入数据：" + i);
			            datas.add(i);
			            try {
			                Thread.sleep(1000);
			            } catch (InterruptedException e) {
			                e.printStackTrace();
			            }
			        }
			    }
			}


	- SynchronizedTest.java
	
			public class SynchronizedTest {
			    public static void main(String[] args) {
			
			       InsertData insertData = new InsertData();
			        new Thread("t1"){
			            @Override
			            public void run() {
			                insertData.insert(Thread.currentThread());
			            }
			        }.start();
			
			        new Thread("t2"){
			            @Override
			            public void run() {
			                insertData.insert(Thread.currentThread());
			            }
			        }.start();
			    }
			}	

	- 测试结果：（说明: 2 个线程同时对 ArrayList 进行了插入数据的操作,数据在某个时刻可能会出现预期以外的结果）

				t2插入数据：0
				t1插入数据：0
				t1插入数据：1
				t2插入数据：1
				t1插入数据：2
				t2插入数据：2
				t1插入数据：3
				t2插入数据：3
				t1插入数据：4
				t2插入数据：4


- 使用 synchronized 关键字修饰方法

	- InsertData.java

			public class InsertData {
			    private ArrayList<Integer> datas = new ArrayList<>();
			
			    public synchronized void insert(Thread thread) {
			        for (int i = 0; i < 5; i++) {
			            System.out.println(thread.getName() + "插入数据：" + i);
			            datas.add(i);
			            try {
			                Thread.sleep(1000);
			            } catch (InterruptedException e) {
			                e.printStackTrace();
			            }
			        }
			    }
			}

	- 测试结果：(说明: t1 线程获取到锁，执行完毕以后，释放锁， t2才执行插入操作)
	
			t1插入数据：0
			t1插入数据：1
			t1插入数据：2
			t1插入数据：3
			t1插入数据：4
			t2插入数据：0
			t2插入数据：1
			t2插入数据：2
			t2插入数据：3
			t2插入数据：4

- 注意点：
	1. 当一个线程正在访问一个对象的 synchronized 方法，那么其他线程不能访问该对象的其他的 synchronized 方法，因为**一个对象只有一个锁，当一个线程获取了该对象的锁之后，其他线程无法获取该对象的锁**，所以无法访问该对象的其他的 synchronized 方法。
	2. 当一个线程访问一个对象的 sychronized 方法，其他线程可以访问非 synchronized 方法，因为**访问非 synchronized 方法不需要获取该对象的锁**。
	3. 访问同一类型的不同对象的 synchronized 方法，不存在互斥对象，因为访问的不是同一个对象。

- synchronized 代码块

	- 格式：
		
	        synchronized (synObject) {
	
	        }

	- 当在某个线程中执行这段代码块，该线程获取对象 synObject 的锁，从而使得其他线程无法同时访问该代码块
	- synObject 可以是 this，代表获取当前对象的锁，也可以是类中的一个属性，代表获取该属性的锁。
	- InsertData.java(版本一)

			public class InsertData {
			    private ArrayList<Integer> datas = new ArrayList<>();
			
			    public void insert(Thread thread) {
			        synchronized (this) {
			            for (int i = 0; i < 5; i++) {
			                System.out.println(thread.getName() + "插入数据：" + i);
			                datas.add(i);
			                try {
			                    Thread.sleep(1000);
			                } catch (InterruptedException e) {
			                    e.printStackTrace();
			                }
			            }
			        }
			    }
			}

	- InsertData.java(版本二)

			public class InsertData {
			    private ArrayList<Integer> datas = new ArrayList<>();
			    private Object synObject = new Object();
			
			    public void insert(Thread thread) {
			        synchronized (synObject) {
			            for (int i = 0; i < 5; i++) {
			                System.out.println(thread.getName() + "插入数据：" + i);
			                datas.add(i);
			                try {
			                    Thread.sleep(1000);
			                } catch (InterruptedException e) {
			                    e.printStackTrace();
			                }
			            }
			        }
			    }
			}

- synchronized 代码块使用起来比 synchronized 方法要灵活得多，一个方法可能只有一部分是需要同步的，如果此时对整个方法用 synchronized 进行同步，会影响程序的执行效率，而使用 synchronized 代码块就可以避免这个问题，synchronized 代码块可以只对需要同步的地方进行同步
- 每个类也会有一个锁，它可以用来控制对 static 数据成员的并发访问
- 如果一个线程执行一个对象的非 static synchronized 方法，另外一个线程执行这个对象所属类的 static synchronized 方法，此时不会发生互斥现象，因为访问 static synchronized 方法占用的是类锁，而访问非 static synchronized 方法占用的是对象锁，所以不存在互斥现象

	- InsertData.java

			public class InsertData {
			
			    private static int staticNumber = 1;
			
			    public static synchronized void insert(Thread thread) {
			        for (int i = 0; i < 5; i++) {
			            System.out.println(thread.getName() + "数据：" + staticNumber);
			            staticNumber++;
			            try {
			                Thread.sleep(1000);
			            } catch (InterruptedException e) {
			                e.printStackTrace();
			            }
			        }
			    }
			
			    public synchronized void insert1() {
			        System.out.println("insert1执行");
			    }
			}

	- SynchronizedTest.java

			public class SynchronizedTest {
			    public static void main(String[] args) {
			
			        InsertData insertData = new InsertData();
			        new Thread("t1") {
			            @Override
			            public void run() {
			                InsertData.insert(Thread.currentThread());
			            }
			        }.start();
			
			        new Thread("t2") {
			            @Override
			            public void run() {
			                insertData.insert1();
			            }
			        }.start();
			    }
			}

	- 测试结果：（第一个线程执行的是 insert() 方法，不会导致第二个线程执行 insert1() 方法发送阻塞现象）

			t1数据：1
			insert1执行
			t1数据：2
			t1数据：3
			t1数据：4
			t1数据：5

### 注意：

- 对于 synchronized 方法或者 synchronized 代码块，当出现异常时， JVM 会自动释放当前线程占用的锁，因此不会由于异常导致出现死锁现象。


