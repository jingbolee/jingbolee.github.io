---
layout:     post
title:      "Java 多线程：同步互斥-Lock "
subtitle:   "Lock ，锁的一些概念"
date:       2016-09-19
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---


## Java 多线程

- 在 Java 中，提供了两种方式来实现同步互斥访问： synchronized 和 Lock

## synchronized 缺陷

- 被 synchronized 修饰的方法或者代码块在竞争资源非常激烈时，效率低
- 无法确定是否获取到了锁
- 无法让等待锁的线程中断

## Lock

- Lock 源码：

		package java.util.concurrent.locks;
		import java.util.concurrent.TimeUnit;
		
		public interface Lock {
		
		    void lock();
		
		    void lockInterruptibly() throws InterruptedException;
		
		    boolean tryLock();
		
		    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
		
		    void unlock();
		
		    Condition newCondition();
		}

- Lock:位于 java.util.concurrent.locks 包下，是一个接口
- 多线程中采用 Lock ，需要主动去释放锁，当发生异常时，并不会主动释放锁，使用 Lock 必须在 try{}catch{} 块中进行，并且将释放锁的操作放在 finally 块中进行，以保证锁一定被释放，防止死锁的发生。

- 获取锁的 4 个方法：
	- lock()：平常使用最多的一个方法，就是获取锁，当锁被其他线程获取，就等待。
	- tryLock()：有返回值，它表示用来尝试获取锁，如果获取成功，就返回 true，获取失败，则返回 false
	- tryLock(long time, TimeUnit unit):与 tryLock() 方法区别是 等待一定时间获取锁，获取到锁就返回 true,拿不到就返回 false.
	- lockInterruptibly():当通过这个方法去获取锁时，如果线程正在等待获取锁，则这个线程能够响应中断，即中断线程的等待状态。当两个线程同时通过 lock.lockInterruptibly() 想获取某个锁时，假如当时线程 A 获取到了锁，而线程 B 在等待，那么对线程 B 调用 threadB,interrupt() 方法能够中断线程 B 的等待过程。
- 释放锁：unlock()

### 实现类：ReentrantLock，意思是：可重入锁，（即当前线程获取到锁，在执行带有锁的方法1的时候，需要获取带有锁的方法2，可以直接使用方法2，而不需要重新获取锁），ReentrantLock 是唯一实现了 Lock 接口的类。

- lock() 方法的使用：
	- InsertData2.java


			public class InsertData2 {
			    private List<Integer> datas = new ArrayList<>();
			    Lock lock = new ReentrantLock();
			
			    public void insert(Thread thread) {
			        lock.lock();
			        System.out.println(thread.getName() + "获取锁");
			        try {
			            for (int i = 0; i < 10; i++) {
			                System.out.println(thread.getName() + "插入数据:" + i);
			                datas.add(i);
			                System.out.println("datas 长度：" + datas.size());
			            }
			        } catch (Exception e) {
			            e.printStackTrace();
			        } finally {
			            lock.unlock();
			            System.out.println(thread.getName() + "释放锁");
			        }
			    }
			} 

	- LockTest.Java

			public class LockTest {
			
			    public static void main(String[] args){
			
			        InsertData2 insertData2 = new InsertData2();
			
			        new Thread("t1"){
			            @Override
			            public void run() {
			                insertData2.insert(Thread.currentThread());
			            }
			        }.start();
			
			        new Thread("t2"){
			            @Override
			            public void run() {
			                insertData2.insert(Thread.currentThread());
			            }
			        }.start();
			    }
			}

	- 测试结果：

			t1获取锁
			t1插入数据:0
			t1插入数据:1
			t1插入数据:2
			t1插入数据:3
			t1插入数据:4
			t1插入数据:5
			t1插入数据:6
			t1插入数据:7
			t1插入数据:8
			t1插入数据:9
			t1释放锁
			t2获取锁
			t2插入数据:0
			t2插入数据:1
			t2插入数据:2
			t2插入数据:3
			t2插入数据:4
			t2插入数据:5
			t2插入数据:6
			t2插入数据:7
			t2插入数据:8
			t2插入数据:9
			t2释放锁

- tryLock() 方法的使用：
	- InsertData3.java


			public class InsertData3 {
			    private Lock lock = new ReentrantLock();
			
			    public void insert(Thread thread) {
			        if (lock.tryLock()) {
			            System.out.println(thread.getName() + " 获取锁成功");
			            try {
			                //增加CPU计算复杂度
			                for (int i = 0; i < 1000000; i++) {
			                    Math.hypot(Math.pow(924526789, i), Math.cos(i));
			                }
			            } catch (Exception e) {
			            } finally {
			                lock.unlock();
			                System.out.println(thread.getName() + " 释放锁");
			            }
			        } else {
			            System.out.println(thread.getName() + " 获取锁失败");
			        }
			    }
			}

	- LockTest2.java


			public class LockTest2 {
			
			    public static void main(String[] args){
			        InsertData3 insertData3 = new InsertData3();
			
			        Runnable runnable = new Runnable() {
			            @Override
			            public void run() {
			                insertData3.insert(Thread.currentThread());
			            }
			        };
			        new Thread(runnable).start();
			        new Thread(runnable).start();
			    }
			}

	- 测试结果：（说明：当使用 2 个线程，遍历 5 个数字的时候，没有出现获取锁失败的情况，把遍历修改为 100000 或者在线程中加入 sleep（）以后，可以出现获取锁失败的情况，估计是硬件计算能力比较强造成的，需要进一步测试； 使用复杂的计算方法，可以实现获取锁失败的情况。）


			Thread-1 获取锁成功
			Thread-0 获取锁失败
			Thread-1 释放锁

- lockInterrupt() 方法的使用：

	- InsertData3.java


			public class InsertData3 {
			    private Lock lock = new ReentrantLock();
			
			    public void insert(Thread thread) {
			        try {
			            lock.lockInterruptibly();
			            System.out.println(thread.getName() + " 获取锁成功");
			            //增加CPU计算复杂度
			            for (int i = 0; i < 1000000; i++) {
			                Math.hypot(Math.pow(924526789, i), Math.cos(i));
			            }
			        } catch (Exception e) {
			        } finally {
			            lock.unlock();
			            System.out.println(thread.getName() + " 释放锁");
			        }
			    }
			}

	- LockTest2.java


			public class LockTest2 {
			
			    public static void main(String[] args) {
			        InsertData3 insertData3 = new InsertData3();
			
			        Runnable runnable = new Runnable() {
			            @Override
			            public void run() {
			                try {
			                    insertData3.insert(Thread.currentThread());
			                } catch (Exception e) {
			                    System.out.println(Thread.currentThread().getName() + " 被中断");
			                }
			            }
			        };
			        Thread t1 = new Thread(runnable);
			        t1.start();
			        Thread t2 = new Thread(runnable);
			        t2.start();
			        try {
			            Thread.sleep(10);
			        } catch (InterruptedException e) {
			            e.printStackTrace();
			        }
			        t2.interrupt();
			    }
			}

	- 测试结果：(说明：获取到锁的线程会一直循环下去，当前 t1 获取到了锁，没有释放锁， t2 无法获取到锁，一直处于等待中，2秒以后调用线程 t2 的 interrupt() 方法，等待中的线程 t2 会响应中断)
	

			Thread-0 获取锁成功
			Thread-1 被中断
			Thread-0 释放锁

## ReadWriteLock

### 源码


	package java.util.concurrent.locks;
	
	public interface ReadWriteLock {
	    /**
	     * @return the lock used for reading
	     */
	    Lock readLock();
	
	    /**
	     * @return the lock used for writing
	     */
	    Lock writeLock();
	}

- ReadWriteLock:位于 java.util.concurrent.locks 包
- ReadWriteLock:是一个接口，定义了两个方法。
	- Lock readLock():用来获取读锁
	- Lock writeLock():用来获取写锁
- 特点：
	- 将文件的读写操作分开，分为 2 个锁来分配给线程，从而使得多个线程可以同时进行读操作。
	- 一个线程已经占有了读锁，则此时其他线程申请写锁，则申请写锁的线程会一直等待是否读锁
	- 一个线程已经占有了写锁，则此时其他线程申请写锁或者读锁，则申请的线程会一直等待是否写锁
- ReentrantReadWriteLock 是 ReadWriteLock 接口的实现类。

## ReentrantReadWriteLock 类

- 使用 synchronized 实现多线程读操作
	- UnReadWriteLockTest.java


			public class UnReadWriteLockTest {
			
			    private Object object = new Object();
			
			    public void get() {
			        synchronized (object) {
			            long startTime = System.currentTimeMillis();
			            while (System.currentTimeMillis() - startTime < 1) {
			                System.out.println(Thread.currentThread().getName()+" 执行读操作");
			            }
			            System.out.println(Thread.currentThread().getName()+" 读操作完成");
			        }
			    }
			}

	- Test3.java


			public class Test3 {
			
			    public static void main(String[] args) {
			        UnReadWriteLockTest unReadWriteLockTest = new UnReadWriteLockTest();
			        Runnable runnable = new Runnable() {
			            @Override
			            public void run() {
			                unReadWriteLockTest.get();
			            }
			        };
			        new Thread(runnable).start();
			        new Thread(runnable).start();
			    }
			}

	- 测试结果:(说明：直到 Thread-0 执行完读操作之后，Thread-1 才能获取到锁，执行读操作)


			Thread-0 执行读操作
			Thread-0 读操作完成
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 读操作完成


- 使用 ReentrantReadWriteLock 实现多线程读操作
	- ReadWriteLockTest.java


			public class ReadWriteLockTest {
			    private ReadWriteLock rwl = new ReentrantReadWriteLock();
			
			    public void get() {
			        try {
			            rwl.readLock().lock();
			            long startTime = System.currentTimeMillis();
			            while (System.currentTimeMillis() - startTime < 1) {
			                System.out.println(Thread.currentThread().getName() + " 执行读操作");
			            }
			            System.out.println(Thread.currentThread().getName() + " 读操作完成");
			        } catch (Exception e) {
			        } finally {
			            rwl.readLock().unlock();
			        }
			    }
			}

	- Test4.java


			public class Test4 {
			
			    public static void main(String[] args) {
			        ReadWriteLockTest rwlt = new ReadWriteLockTest();
			
			        Runnable runnable = new Runnable() {
			            @Override
			            public void run() {
			                rwlt.get();
			            }
			        };
			        new Thread(runnable).start();
			        new Thread(runnable).start();
			        new Thread(runnable).start();
			    }
			}

	- 测试结果：（说明：可以看到 Thread-0,Thread-1,Thread-2 三个线程在同时执行读操作，大大的提供了效率）


			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-0 执行读操作
			Thread-2 执行读操作
			Thread-0 执行读操作
			Thread-2 读操作完成
			Thread-0 读操作完成
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 执行读操作
			Thread-1 读操作完成

- 使用 ReentrantReadWriteLock 实现多线程读写操作

	- ReadWriteLockTest.java


			public class ReadWriteLockTest {
			    private ReadWriteLock rwl = new ReentrantReadWriteLock();
			
			    public void get() {
			        try {
			            rwl.readLock().lock();
			            System.out.println(Thread.currentThread().getName() + " 获取到读锁");
			            long startTime = System.currentTimeMillis();
			            while (System.currentTimeMillis() - startTime < 1) {
			                System.out.println(Thread.currentThread().getName() + " 执行读操作");
			            }
			            System.out.println(Thread.currentThread().getName() + " 读操作完成");
			        } catch (Exception e) {
			        } finally {
			            rwl.readLock().unlock();
			            System.out.println(Thread.currentThread().getName() + " 释放读锁");
			        }
			    }
			
			    public void write() {
			        try {
			            rwl.writeLock().lock();
			            System.out.println(Thread.currentThread().getName() + " 获取到写锁");
			            for (int i = 0; i < 1000000; i++) {
			                Math.hypot(Math.pow(924526789, i), Math.cos(i));
			            }
			            System.out.println(Thread.currentThread().getName() + " 写操作完成");
			        } catch (Exception e) {
			        } finally {
			            rwl.writeLock().unlock();
			            System.out.println(Thread.currentThread().getName() + " 释放写锁");
			        }
			    }
			}


	- Test5.java


			public class Test5 {
			    static ReadWriteLockTest rwlt;
			    static Runnable readRunable;
			    static Runnable writeRunable;
			
			    static {
			        rwlt = new ReadWriteLockTest();
			        readRunable = new Runnable() {
			            @Override
			            public void run() {
			                rwlt.get();
			            }
			        };
			
			        writeRunable = new Runnable() {
			            @Override
			            public void run() {
			                rwlt.write();
			            }
			        };
			    }
			
			    /*
			    * 当前线程获取到读锁，其他线程需要获取写锁，需要等待当前显示释放读锁
			     */
			    public void readWriteWait() {
			        Thread readThread = new Thread(readRunable);
			        Thread writeThread =new Thread(writeRunable);
			        readThread.start();
			        writeThread.start();
			    }
			
			    /*
			     * 当前线程获取到写锁，其他线程需要获取读锁，需要等待当前显示释放写锁
			    */
			    public void writeReadWait() {
			        Thread readThread = new Thread(readRunable);
			        Thread writeThread =new Thread(writeRunable);
			        writeThread.start();
			        readThread.start();
			    }
			
			    /*
			     * 当前线程获取到写锁，其他线程需要获取写锁，需要等待当前显示释放写锁
			    */
			    public void writeWriteWait() {
			        Thread writeThread = new Thread(writeRunable);
			        Thread writeThread1 =new Thread(writeRunable);
			        writeThread.start();
			        writeThread1.start();
			    }
			
			    public static void main(String[] args) {
			        Test5 t5 = new Test5();
			        t5.readWriteWait();
			        t5.writeReadWait();
			        t5.writeWriteWait();
			    }
			}


	- 测试结果：
		1. 当前线程获取到读锁，其他线程需要写锁，则需要等待当前显示是否读锁


				Thread-0 获取到读锁
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 读操作完成
				Thread-0 释放读锁
				Thread-1 获取到写锁
				Thread-1 写操作完成
				Thread-1 释放写锁


		2. 当前线程获取到写锁，其他线程需要写锁，则需要等待当前线程释放写锁


				Thread-1 获取到写锁
				Thread-1 写操作完成
				Thread-1 释放写锁
				Thread-0 获取到写锁
				Thread-0 写操作完成
				Thread-0 释放写锁


		3. 当前线程获取到写锁，其他线程需要读锁，则需要等待当前线程释放写锁


				Thread-1 获取到写锁
				Thread-1 写操作完成
				Thread-1 释放写锁
				Thread-0 获取到读锁
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 执行读操作
				Thread-0 读操作完成
				Thread-0 释放读锁


- 注意: 读锁和写锁的策略
	1. 当前线程获取到读锁，其他线程需要读锁，可以直接获取到
	2. 当前线程获取到读锁，其他线程需要写锁，则需要等待当前线程释放读锁
	3. 当前线程获取到写锁，其他线程需要写锁，则需要等待当前线程释放写锁
	4. 当前线程获取到写锁，其他线程需要读锁，则需要等待当前线程释放写锁


## Lock 接口和 synchronized 关键字在锁上的差异


- Lock 是一个接口，synchronized 是 Java 的关键字，synchronized 是内置的语言实现
- synchronized 发生异常的时候，会自动释放线程占有的锁，因此不会导致死锁现象发生；Lock 发生异常的时候，不会主动调用 unLock() 方法释放锁，可能会造成死锁现象，因此使用 Lock 的时候需要使用 try{} catch{} 然后在 finally 中调用 unLock() 方法释放锁。
- Lock 的 lockInterrupt() 方法可以让等待锁的线程响应中断，而 syonchronized 不可以，使用 synchronized 时，线程会一直等待下去，不能响应中断
- 通过 Lock 可以知道有没有成功获取锁，而 synchronized 却无法知道
- Lock 可以提供多个线程进行读操作的效率
- 性能上，在资源竞争不激烈的时候，效率差不多；当资源竞争激烈的时候，synchronized 性能会下降，而 Lock 的性能不变。


## 锁的相关概念

### 可重入锁

- 如果锁具备可重入性，则称作为可重入锁，像 synchronized 和 ReentrantLock 都是可重入锁，可重入性是基于线程的分配，意思是：当前线程获执行带有锁 method1() 的时候，需要调用带有锁的 method2,因为可重入性，可以直接执行带有锁的 method2()，而不需要等待或者重新获取锁。
- 示例代码
	- Foo1.java
	

			public class Foo1 {
			
			    public synchronized void m1() {
			        for (int i = 0; i < 1000000; i++) {
			            Math.hypot(Math.pow(924526789, i), Math.cos(i));
			        }
			        System.out.println("m1执行完毕");
			        m2();
			    }
			
			    public synchronized void m2() {
			        System.out.println("m2执行完毕");
			    }
			
			    public static void main(String[] args){
			        Foo1 f1 = new Foo1();
			        Thread t = new Thread(new Runnable() {
			            @Override
			            public void run() {
			                f1.m1();
			            }
			        });
			        t.start();
			    }
			}


	- 测试结果


			m1执行完毕
			m2执行完毕


	- 分析：方法 m1() 和 m2() 都用 synchronized 修饰，当前线程获取到对象锁执行 m1() 方法，由于 m1() 调用 m2() 而 m2() 也是 synchronized 修饰的，因为 synchronized 的可重入性，当前线程不需要重新申请锁，就可以直接执行 m2() 方法。


### 可中断锁

- 可中断锁：可以响应中断的锁
- synchronized 不是可中断所；Lock 是可中断锁，Lock 的 lockInterruptibly() 方法提示了可中断性


### 公平锁

- 公平锁：尽量以请求锁的顺序来获取锁，比如多个线程等待一个锁，当这个锁释放的时候，等待时间最久的线程（最先请求的线程）会获得锁
- 非公平锁无法保证锁的获取是按照请求锁的顺序进行的，可能导致一些锁永远获取不到锁
- synchronized 是不公平锁，它无法保证等待的线程获取锁的顺序
- ReentrantLock 和 ReentrantReadWriteLock 默认不是公平锁，但是可以设置成公平锁
	- 源码：


		    /**
		     * Sync object for non-fair locks
		     */
		    static final class NonfairSync extends Sync {
		        private static final long serialVersionUID = 7316153563782823691L;
		
		        /**
		         * Performs lock.  Try immediate barge, backing up to normal
		         * acquire on failure.
		         */
		        final void lock() {
		            if (compareAndSetState(0, 1))
		                setExclusiveOwnerThread(Thread.currentThread());
		            else
		                acquire(1);
		        }
		
		        protected final boolean tryAcquire(int acquires) {
		            return nonfairTryAcquire(acquires);
		        }
		    }
		
		    /**
		     * Sync object for fair locks
		     */
		    static final class FairSync extends Sync {
		        private static final long serialVersionUID = -3000897897090466540L;
		
		        final void lock() {
		            acquire(1);
		        }
		
		        /**
		         * Fair version of tryAcquire.  Don't grant access unless
		         * recursive call or no waiters or is first.
		         */
		        protected final boolean tryAcquire(int acquires) {
		            final Thread current = Thread.currentThread();
		            int c = getState();
		            if (c == 0) {
		                if (!hasQueuedPredecessors() &&
		                    compareAndSetState(0, acquires)) {
		                    setExclusiveOwnerThread(current);
		                    return true;
		                }
		            }
		            else if (current == getExclusiveOwnerThread()) {
		                int nextc = c + acquires;
		                if (nextc < 0)
		                    throw new Error("Maximum lock count exceeded");
		                setState(nextc);
		                return true;
		            }
		            return false;
		        }
		    }


	- ReentrantLock 的两个构造方法


		    public ReentrantLock() {
		        sync = new NonfairSync();
		    }
		
		    /**
		     * Creates an instance of {@code ReentrantLock} with the
		     * given fairness policy.
		     *
		     * @param fair {@code true} if this lock should use a fair ordering policy
		     */
		    public ReentrantLock(boolean fair) {
		        sync = fair ? new FairSync() : new NonfairSync();
		    }


	- 在 ReentrantLock 中定义了 2 个静态内部类，一个是 NotFairSync 一个是 FairSync，分别用来实现非公平锁和公平锁
	- 通过构造方法实现公平锁


			ReentrantLock lock = new ReentrantLock(true);


	- 如果参数为 true 表示公平锁，为 false 表示非公平锁。默认无参构造方法表示非公平锁
	- 可以通过方法:final boolean isFair() 来判断是不是公平锁


### 读写锁


- 读写锁将对一个资源的访问分为 2 个锁，一个读锁和一个写锁
- 有个读锁，可以使得多线程在读锁时提供效率
- ReadWriteLock 是一个读写锁接口，实现类为 ReentrantReadWriteLock
- 通过 readLock() 获取读锁，通过 writeLock() 获取写锁



> 转自：（海子：http://www.cnblogs.com/dolphin0520/p/3923167.html）