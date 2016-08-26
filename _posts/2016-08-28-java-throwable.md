---
layout:     post
title:      "Java 异常"
subtitle:   "Throwable"
date:       2016-08-26
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## Java 异常

- Java 中所有异常类的父类是 Throwable 类
- Java 中直接继承 Throwable 类的子类有2个
	- Error:一般是系统异常，无法捕获，出现该类异常时，都是比较严重的，系统级别的。该异常是 unchecked exceptions
	- Exception:在程序中一般都是 try..catch 捕获这类异常。可以通过修改代码逻辑来处理这类异常。自定义异常都是继承 Exception 类或者其子类。该类分为非运行时异常和运行时异常。
		- 非运行时异常：该异常是 checked exceptions。
		- 运行时异常：该异常是 非检查异常，无法捕获。

- Java异常类的层次结构。
	- 图中红色部分为受检查异常。它们必须被捕获，或者在函数中声明为抛出该异常。

		![](http://i.imgur.com/iYWZJmn.jpg)

- try...catch 和 try...catch...finally
	- 都是可以捕获异常的。
	- try...catch...finally ，无论是否发生异常，可以在 finally 中处理一些比如关闭网络，IO 流等情况，释放资源。

- throw 和 throws
	- throw:表示一个动作，用在方法的内部，抛出一个异常
	- throws：用在方法名的后面，表示该方法抛出异常，使用者需要捕获异常或者继续抛出该异常。

- 自定义异常
	-  自定义异常，继承 Exception 类或者其子。
	-  示例：自定义了异常类：DrunkException，继承 Exception。

			public class DrunkException extends Exception {
			
			    public DrunkException() {
			        super();
			    }
			
			    public DrunkException(String message) {
			        super(message);
			    }
			}

	- 定义一个方法，抛出该异常

		    public void test1() throws DrunkException {
		        throw new DrunkException("喝酒喝大了吧");
		    }

	- test1() 的调用者 test2() 捕获该方法

		    public void test2() {
		        try {
		            test1();
		        } catch (DrunkException e) {
		            e.printStackTrace();
		        }
		    }

	- 代码：
			
			public class ThrowableTest {
			
			    public static void main(String[] args) {
			        ThrowableTest tt = new ThrowableTest();
			        tt.test2();
			    }
			
			    public void test1() throws DrunkException {
			        throw new DrunkException("喝酒喝大了吧");
			    }
			
			    public void test2() {
			        try {
			            test1();
			        } catch (DrunkException e) {
			            e.printStackTrace();
			        }
			    }
			
			}

	- 结果：

			cc.lijingbo.script.throwabletest.DrunkException: 喝酒喝大了吧
				at cc.lijingbo.script.throwabletest.ThrowableTest.test1(ThrowableTest.java:15)
				at cc.lijingbo.script.throwabletest.ThrowableTest.test2(ThrowableTest.java:20)
				at cc.lijingbo.script.throwabletest.ThrowableTest.main(ThrowableTest.java:10)

	- 在捕获到自定义异常以后，抛出其他类型异常，如运行时异常

			public class ThrowableTest {
			
			    public static void main(String[] args) {
			        ThrowableTest tt = new ThrowableTest();
			        tt.test2();
			    }
			
			    public void test1() throws DrunkException {
			        throw new DrunkException("喝酒喝大了吧");
			    }
			
			    public void test2() {
			        try {
			            test1();
			        } catch (DrunkException e) {
			            RuntimeException runtimeException = new RuntimeException("这是自定义异常转运行时异常");
			            runtimeException.initCause(e);
			            throw runtimeException;
			        }
			    }
			}

	- 结果：

			Exception in thread "main" java.lang.RuntimeException: 这是自定义异常转运行时异常
				at cc.lijingbo.script.throwabletest.ThrowableTest.test2(ThrowableTest.java:21)
				at cc.lijingbo.script.throwabletest.ThrowableTest.main(ThrowableTest.java:10)
				at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
				at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
				at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
				at java.lang.reflect.Method.invoke(Method.java:483)
				at com.intellij.rt.execution.application.AppMain.main(AppMain.java:147)
			Caused by: cc.lijingbo.script.throwabletest.DrunkException: 喝酒喝大了吧
				at cc.lijingbo.script.throwabletest.ThrowableTest.test1(ThrowableTest.java:14)
				at cc.lijingbo.script.throwabletest.ThrowableTest.test2(ThrowableTest.java:19)
				... 6 more

	