---
layout:     post
title:      "Java注解的理解和使用以及原理"
subtitle:   "Annotation"
date:       2016-05-11 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---


- 什么是注解
  - 提供除了程序本身逻辑外的额外的数据信息。Annotation对于标注的代码没有直接的影响，它不可以直接与标注的代码产生交互，但其他组件可以使用这些信息
  - 注解是一种类型，与类class,接口interface,枚举enum一样，可以定义，使用，定义自己的属性和方法
  - 注解不会改变程序的编译方式
  - 注解是当作一个修饰符来使用，它被置于被注解项之前，每一个注解的名称前面加@符号
  - 每个注解都必须通过一个注解接口进行定义
  - 注解有元注解和自定义注解
  - 所有的注解接口都隐式的扩展自java.lang.annotation.Annotation接口
  - 无法扩展注解接口
- 注解的语法
  - 通过注解接口定义：

	     	public @interface BugReport{  
				String assignedTo() default "[none]"
	  			int severity() = 0;	
	 		}

  - 注解使用：
    
            @BugReport(assignedTo="Harry",severity=10)
			public void method(...){
				...}

  - 

- Java中的注解，java.lang包中预定义了三个注解
   - @Override：标识某一个方法是否覆盖了它的父类的方法
   - @Deprecated：这是一个标记注解，警告这个方法以后是要被删除的
   - @SuppressWarnings：抑制编译器产生警告信息
      - unchecked
      - path
      - serial
      - finally
      - fallthrough


| 注解接口        | 应用场合    | 目的 |
| -------------|:------------:| ------: |
| Deprecated   | 全部	      | 标记为过时的 |
| SupportWarings| 除了包和注解之外的所有情况 |  阻止某个给定类型的警告信息 |
| Override     | 方法	      | 检查该方法是否覆盖了某一个父类方法|
| Target      | 注解       |  指明可以应用这个注解的那些项 |
| Retention      | 注解       |  指明这个注解可以保留多久 |
| Document      | 注解       |  指明这个注解包含在注解项的文档中 |
| Inherited      | 注解       |  指明当这个注解应用于一个类的时候，能够自动被它的子类继承 |

- 注解的类型
   - 标记注解
      - 注解的内部没有属性
      - 使用：@注解名
      - 例子：@MarkAnnotation 
   - 单值注解
      - 注解的内部只有一个属性
      - 使用：@注解名（属性名=属性值）
      - 例子：@SingleAnnotation(value="abc") 可以写成@SingleAnnotation("abc")
      - 特性：(属性名=属性值)可以简化为(属性值)，但是需要满足以下两个条件：
1、该注解必须为单值注解
2、该注解的属性名必须为value
   - 多只注解
      - 注解的内部有多个属性
      - 使用：@注解名(属性名1=属性值1, 属性名2=属性值2……)
      - 例子：@MultipliedAnnotation(value1 = "abc", value2 = 30……)
- 元注解，元注解是指注解的注解。包括  @Retention @Target @Document @Inherited四种。
  - Target：限制该注解可以使用范围
     - @Target(ElementType.ANNOTATION_TYPE)：注解类型声明
     - @Target(ElementType.PACKAGE)：包
     - @Target(ElementType.TYPE)：类，枚举，接口，注解
     - @Target(ElementType.METHOD)：方法
     - @Target(ElementType.CONSTRUCTOR)：构造方法
     - @Target(ElementType.FIELD)：字段、枚举常量
     - @Target(ElementType.PARAMETER)：方法或构造方法的参数
     - @Target(ElementType.LOCAL_VARIABLE)：局部变量

   
  - Retention：注解的保留策略
     - @Retention(RetentionPolicy.SOURCE)：注解仅存在于源码中，在class字节码文件中不包含
     - @Retention(RetentionPolicy.CLASS)：默认的保留策略，注解会在class字节码文件中存在，但运行时无法获得，
     - @Retention(RetentionPolicy.RUNTIME)：注解会在class字节码文件中存在，在运行时可以通过反射获取到
  - Document：说明该注解将被包含在javadoc中
  - Inherited：说明子类可以继承父类中的该注解
- 注解的使用
  
		@Retention(RetentionPolicy.RUNTIME)
		@Target(ElementType.METHOD)
		public @interface MethodAnnotation {
		
		}
下面的Person对象使用了自定义的MethodAnnotation。

		public class Person {
	
	    public void eat() {
	        System.out.println("eating");
	    }
	
	    @MethodAnnotation
	    public void walk() {
	        System.out.print("walking");
	    }
	
		}
通过反射获取Annotation的信息。

		Class<Person> personClass = Person.class;
        Method[] methods = personClass.getMethods();
        for(Method method : methods){
            if (method.isAnnotationPresent(MethodAnnotation.class)){
                method.invoke(personClass.newInstance());
            }
        }

输出

		walking

也可以给自定义的Annotation加方法

		@Target(ElementType.TYPE)
		public @interface personAnnotation {
		    int id() default 1;
		    String name() default "bowen";
		}

对personAnnotation的使用。

		@personAnnotation(id = 8, name = "john")
		public class Person {
		
		    public void eat() {
		        System.out.println("eating");
		    }
		
		    @MethodAnnotation
		    public void walk() {
		        System.out.print("walking");
		    }
		
		}
- 注解原理
  - 当Java源代码被编译时，编译器的一个插件annotation处理器则会处理这些annotation。处理器可以产生报告信息，或者创建附加的Java源文件或资源。如果annotation本身被加上了RententionPolicy的运行时类，则Java编译器则会将annotation的元数据存储到class文件中。然后，Java虚拟机或其他的程序可以查找这些元数据并做相应的处理。

  - 当然除了annotation处理器可以处理annotation外，我们也可以使用反射自己来处理annotation。Java SE 5有一个名为AnnotatedElement的接口，Java的反射对象类Class,Constructor,Field,Method以及Package都实现了这个接口。这个接口用来表示当前运行在Java虚拟机中的被加上了annotation的程序元素。通过这个接口可以使用反射读取annotation。AnnotatedElement接口可以访问被加上RUNTIME标记的annotation，相应的方法有getAnnotation,getAnnotations,isAnnotationPresent。由于Annotation类型被编译和存储在二进制文件中就像class一样，所以可以像查询普通的Java对象一样查询这些方法返回的Annotation。
