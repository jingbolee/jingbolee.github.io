---
layout:     post
title:      "Java 反射的理解和使用"
subtitle:   "reflect"
date:       2016-08-25 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## Java 反射

- 反射，reflect,位于 Java 的 java.lang.reflect 包下，跟反射有关的类都在这个包里面。
- 通过反射，功能很强大，包括以下：
	- 获取到类中的所有的方法，包括 private 的方法，可以获取到方法的返回值，方法的参数等信息；
	- 获取到构造方法，并通过构造方法获得该类的实例；
	- 获取到成员变量，并可以可以通过反射修改成员变量的属性；
	- 获取到数组信息
	- 获取到实现的接口
	- 获取到继承的父类
	- 调用类的方法
- 在实际开发中会经常使用 Java 的反射，比如在做 Android 开发的时候，需要用到拒绝来电的功能，但是 Android 中该功能是隐藏的，没有办法直接调用，这个时候就用上了反射。

> http://stackoverflow.com/questions/18065144/end-call-in-android-programmatically

- 

- Class 类
	- 反射需要用到 Class 类，就涉及到面向对象了。在 Java 中，一切皆是对象，包括基础数据类型和类文件。
	- 类文件，是 Class 类的对象。
	
- 获取 Class 对象的三种方式
	1. 在知道类的实例的情况下，使用 **getClass()** 方法
	
	        Student s1 = new Student();
	        Class<? extends Student> c1 = s1.getClass();

	2. 在知道类的情况下，使用静态字段 **class**

			Class<? extends Student> c2 = Student.class;

	3. 在知道类的情况下，使用 **Class.forName()** 方法，这种情况会出现异常，需要使用 try...catch 捕获异常

			try {
	            Class<?> c3 = Class.forName("cc.lijingbo.orm.bean.Student");
	        } catch (ClassNotFoundException e) {
	            e.printStackTrace();
	        }

	4. 其中 c1,c2,c3 被称为 **class type ,类类型**

- 反射实例
	- 获取到类完整的包名和类名

			public class ReflectTest {
			    public static void main(String[] args) {
			        Class<?> c = Man.class;
			        String name = c.getName();
			        System.out.println(name);
			    }
			}

		
	- 三种方法获取到类类型

			public class ReflectTest {
			    public static void main(String[] args) {
			        Class<?> c1 = null;
			        Class<?> c2 = null;
			        Class<?> c3 = null;
			
			        c1 = Man.class;
			        Man man = new Man();
			        c2 = man.getClass();
			        try {
			            c3 = Class.forName("cc.lijingbo.script.reflect.Man");
			        } catch (ClassNotFoundException e) {
			            e.printStackTrace();
			        }
			
			        System.out.println("类的名称：" + c1.getName());
			        System.out.println("类的名称：" + c2.getName());
			        System.out.println("类的名称：" + c3.getName());
			    }
			}

	- 通过类的无参构造方法获取到类的实例

			public class ReflectTest {
			    public static void main(String[] args) {
			        Class<?> c3 = null;
			        try {
			            c3 = Class.forName("cc.lijingbo.script.reflect.Man");
			        } catch (ClassNotFoundException e) {
			            e.printStackTrace();
			        }
			        try {
			            Man man = (Man) c3.newInstance();
			            man.say();
			            man.eat();
			            man.sleep();
			        } catch (InstantiationException e) {
			            e.printStackTrace();
			        } catch (IllegalAccessException e) {
			            e.printStackTrace();
			        }
			    }
			}
	
	- 在没有无参构造方法的类中，使用上面那种方法会报错

			java.lang.InstantiationException: cc.lijingbo.script.reflect.Man
		    at java.lang.Class.newInstance(Class.java:423)
		    at cc.lijingbo.script.reflect.ReflectTest.main(ReflectTest.java:15)
		    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
		    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
		    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
		    at java.lang.reflect.Method.invoke(Method.java:483)
		    at com.intellij.rt.execution.application.AppMain.main(AppMain.java:147)
			Caused by: java.lang.NoSuchMethodException: cc.lijingbo.script.reflect.Man.<init>()
			    at java.lang.Class.getConstructor0(Class.java:3074)
			    at java.lang.Class.newInstance(Class.java:408)
			    ... 6 more

	- 获取到所有的构造方法

			public class ReflectTest {
			    public static void main(String[] args) {
			        Class<?> c = null;
			        try {
			            c = Class.forName("cc.lijingbo.script.reflect.Man");
			        } catch (ClassNotFoundException e) {
			            e.printStackTrace();
			        }
			        Constructor<?>[] constructors = c.getConstructors();
			        for (Constructor constructor : constructors) {
			            System.out.println(constructor);
			        }
			    }
			}

	- 获取到所有构造方法并实例化

			public class ReflectTest {
			    public static void main(String[] args) {
			        Class<?> c = null;
			        try {
			            c = Class.forName("cc.lijingbo.script.reflect.Man");
			        } catch (ClassNotFoundException e) {
			            e.printStackTrace();
			        }
			        Man m1 = null;
			        Man m2 = null;
			        Man m3 = null;
			        Man m4 = null;
			        Constructor<?>[] constructors = c.getConstructors();
			        try {
			            m1 = (Man) constructors[3].newInstance();
			            m2 = (Man) constructors[2].newInstance("Jerome");
			            m3 = (Man) constructors[1].newInstance(20);
			            m4 = (Man) constructors[0].newInstance("Jerome1", 21);
			        } catch (InstantiationException e) {
			            e.printStackTrace();
			        } catch (IllegalAccessException e) {
			            e.printStackTrace();
			        } catch (InvocationTargetException e) {
			            e.printStackTrace();
			        }
			        System.out.println(m1);
			        System.out.println(m2);
			        System.out.println(m3);
			        System.out.println(m4);
			    }
			}

	- 获取类实现的接口

			public class ReflectTest {
			    public static void main(String[] args) {
			        Class<?> c = null;
			        try {
			            c = Class.forName("cc.lijingbo.script.reflect.Man");
			        } catch (ClassNotFoundException e) {
			            e.printStackTrace();
			        }
			        Class<?>[] interfaces = c.getInterfaces();
			        for (Class in : interfaces) {
			            System.out.println(in.getName());
			        }
			    }
			}

	- 获取类继承的父类

			public class ReflectTest {
			    public static void main(String[] args) {
			        Class<?> c = null;
			        try {
			            c = Class.forName("cc.lijingbo.script.reflect.Child");
			        } catch (ClassNotFoundException e) {
			            e.printStackTrace();
			        }
			        Class<?> superclass = c.getSuperclass();
			        System.out.println(superclass.getName());
			    }
			}

	- 获取构造方法的修饰符和参数

			public class ReflectTest {
			    public static void main(String[] args) {
			        Class<?> c = null;
			        try {
			            c = Class.forName("cc.lijingbo.script.reflect.Man");
			        } catch (ClassNotFoundException e) {
			            e.printStackTrace();
			        }
			        Constructor<?>[] constructors = c.getConstructors();
			        for (int i = 0; i < constructors.length; i++) {
			            Class[] parameterTypes = constructors[i].getParameterTypes();
			            int modifiers = constructors[i].getModifiers();
			            System.out.print(Modifier.toString(modifiers) + " ");
			            System.out.print(constructors[i].getName());
			            System.out.println("(");
			            for (int j = 0; j < parameterTypes.length; ++j) {
			                System.out.print(parameterTypes[j].getName() + " arg" + i);
			                if (j < parameterTypes.length - 1) {
			                    System.out.print(",");
			                }
			            }
			            System.out.println("){}");
			        }
			    }
			}

	- 获取到类中的成员变量

			public class ReflectTest {
			    public static void main(String[] args) {
			        Class<?> c = null;
			        try {
			            c = Class.forName("cc.lijingbo.script.reflect.Man");
			        } catch (ClassNotFoundException e) {
			            e.printStackTrace();
			        }
			        Field[] declaredFields = c.getDeclaredFields();
			        for (Field field : declaredFields) {
			            int modifiers = field.getModifiers();
			            String type = Modifier.toString(modifiers);
			            Class<?> fieldType = field.getType();
			            System.out.println(type + " " + fieldType + " " + field.getName());
			        }
			        System.out.println("..........");
			        Field[] fields = c.getFields();
			        for (Field field : fields) {
			            int modifiers = field.getModifiers();
			            String type = Modifier.toString(modifiers);
			            Class<?> fieldType = field.getType();
			            System.out.println(type + " " + fieldType + " " + field.getName());
			        }
			    }
			}

	- 调用静态方法，非静态方法，有参和无参的方法

			public class ReflectTest {
			    public static void main(String[] args) {
			        Class<?> c = null;
			        try {
			            c = Class.forName("cc.lijingbo.script.reflect.Man");
			        } catch (ClassNotFoundException e) {
			            e.printStackTrace();
			        }
			
			        //调用带参的方法
			        try {
			            Method sayName = c.getMethod("sayName", String.class);
			            sayName.invoke(c.newInstance(), "Jerome");
			        } catch (NoSuchMethodException e) {
			            e.printStackTrace();
			        } catch (IllegalAccessException e) {
			            e.printStackTrace();
			        } catch (InstantiationException e) {
			            e.printStackTrace();
			        } catch (InvocationTargetException e) {
			            e.printStackTrace();
			        }
			
			        //调用无参的方法
			        try {
			            Method say = c.getMethod("say");
			            say.invoke(c.newInstance());
			        } catch (NoSuchMethodException e) {
			            e.printStackTrace();
			        } catch (IllegalAccessException e) {
			            e.printStackTrace();
			        } catch (InstantiationException e) {
			            e.printStackTrace();
			        } catch (InvocationTargetException e) {
			            e.printStackTrace();
			        }
			
			        //调用静态无参方法
			        try {
			            Method sayStatic = c.getMethod("sayStatic");
			            sayStatic.invoke(null);
			        } catch (NoSuchMethodException e) {
			            e.printStackTrace();
			        } catch (InvocationTargetException e) {
			            e.printStackTrace();
			        } catch (IllegalAccessException e) {
			            e.printStackTrace();
			        }
			
			        //调用静态带参方法
			        try {
			            Method sayStatic2 = c.getMethod("sayStatic2", String.class);
			            sayStatic2.invoke(null,"Jerome");
			        } catch (NoSuchMethodException e) {
			            e.printStackTrace();
			        } catch (InvocationTargetException e) {
			            e.printStackTrace();
			        } catch (IllegalAccessException e) {
			            e.printStackTrace();
			        }
			
			    }
			}

	- 获取类加载器
			
			public class ReflectTest2 {
			    public static void main(String[] args) {
			        Class<?> c = Man.class;
			        //获取到 classloader
			        ClassLoader classLoader = c.getClassLoader();
			        //通过获取到classloader的类类型，得到classloader的名称
			        String name = classLoader.getClass().getName();
			        System.out.println(name);
			    }
			}

	- 修改成员变量的值

			public class ReflectTest3 {
			    public static void main(String[] args) {
			        Class<?> c = Man.class;
			        try {
			            Field name = c.getDeclaredField("name");
			            name.setAccessible(true);
			            Object o = c.newInstance();
			            name.set(o,"Haha");
			            System.out.println(name.get(o));
			        } catch (NoSuchFieldException e) {
			            e.printStackTrace();
			        } catch (InstantiationException e) {
			            e.printStackTrace();
			        } catch (IllegalAccessException e) {
			            e.printStackTrace();
			        }
			    }
			}

	- 获取数组相关信息

			public class ReflectTest4 {
			    public static void main(String[ ] args){
			        int[] temp = {1,2,4,5,7};
			        Class<?> c = temp.getClass();
			        Class<?> componentType = c.getComponentType();
			        System.out.println("数组的类型："+componentType.getName());
			        System.out.println("数组的长度："+Array.getLength(temp));
			        System.out.println("数组第三个位置："+Array.get(temp,2));
			        Array.set(temp,2,44);
			        System.out.println("修改后的数组第三个位置："+Array.get(temp,2));
			    }
			}

