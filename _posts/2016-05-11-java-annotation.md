---
layout:     post
title:      "Java 注解的理解和使用以及原理"
subtitle:   "Annotation"
date:       2016-05-11 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---


- 什么是注解

	- 从 java 1.5 开始加入的一个特性
  - 注解是一种类型，与类class,接口interface,枚举enum一样，可以定义，使用，定义自己的成员属性。
  - 注解是当作一个修饰符来使用，它被置于被注解项之前，每一个注解的名称前面加@符号
  - 每个注解都必须通过一个注解接口进行定义
  - 所有的注解接口都隐式的扩展自java.lang.annotation.Annotation接口
  
- 注解分类
	- 根据谁定义的注解
		- JDK 注解
			- @Override：标识某一个方法是否覆盖了它的父类的方法
			- @Deprecated：
			- @SuppressWarnings：
		- 元注解
			- @Target：使用范围
			- @Retention：生命周期
			- @Inherited：可继承
			- @Documented：可生成 java 文档
		- 自定义注解

	- 根据注解在 java 中的作用范围(由元注解 @Retention 决定)
		- source：只存在于源文件中
		- class：存在于源文件和编译后的文件中
		- Runtime：存在于运行时，甚至会影响到代码的逻辑

	- 根据是否有成员分为标识注解和值注解

- 注解的语法
  - 通过注解接口定义：
			
			@Target({Element.TYPE,Element.METHOD}) //使用范围为类、接口、方法
			@Retention(RetentionPolicy.RUNTIME)//作用范围为运行时
			@Inherited  //可被继承
			@Documented  //可生成文档
	     	public @interface BugReport{  
				String assignedTo() default "[none]"
	  			int severity() = 0;	
	 		}

  - 注解使用：
    
            @BugReport(assignedTo="Harry",severity=10)
			public void method(...){
				...}

  
- 注解中的元注解
	- @Target：定义该注解的使用范围
		- TYPE：类或者接口
		- METHOD：方法
		- FIELD：成员
		- PARAMETER：参数
		- LOCAL-VARIABLE：局部变量
		- PACKAGE：包
		- ANNOTATION_TYPE：注解类型
		- CONSTRUCTOR：构造方法
	- @Retention：定义该注解的作用范围
		- SOURCE：源文件
		- CLASS：编译后的文件，及class文件
		- RUNTIME：运行时
	- @Inherited：该注解可继承
		- 只有类的注解可以被继承
		- 接口的注解无法继承
		- 子类中没有重写的方法，可以继承注解
	- @Documented：该注解可生成文档



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
	      - 	该注解必须为单值注解  
		  - 该注解的属性名必须为value

   - 多值注解


      - 注解的内部有多个属性
      - 使用：@注解名(属性名1=属性值1, 属性名2=属性值2……)
      - 例子：@MultipliedAnnotation(value1 = "abc", value2 = 30……)


- 注解示例

	- 需求：
		1. 有一张用户表，字段包括用户ID,用户名，昵称，年龄，性别，所在城市，邮箱，手机号
		2. 方便的对每个字段或字段的组合条件进行检索，并打印出SQL
		3. 使用方式要足够简单，见代码示例

	- 代码：
		- 定义一个表名注解：Table.java
		
				@Target({ElementType.TYPE})
				@Retention(RetentionPolicy.RUNTIME)
				@Documented
				public @interface Table {
				    String value();
				}

		- 定义一个列名注解：Culoum.java
		 
				@Target({ElementType.FIELD})
				@Retention(RetentionPolicy.RUNTIME)
				@Documented
				public @interface Column {
				    String value();
				}

		- Bean 文件使用注解：User.java

				@Table("user")
				public class User {
				    @Column("id")
				    private int id;
				    @Column("user_name")
				    private String userName;
				    @Column("nick_name")
				    private String nickName;
				    @Column("age")
				    private int age;
				    @Column("is_male")
				    private boolean isMale;
				    @Column("city")
				    private String city;
				    @Column("email")
				    private String email;
				    @Column("phone_number")
				    private int phoneNumber;
				
				    public int getId() {
				        return id;
				    }
				
				    public void setId(int id) {
				        this.id = id;
				    }
				
				    public String getUserName() {
				        return userName;
				    }
				
				    public void setUserName(String userName) {
				        this.userName = userName;
				    }
				
				    public String getNickName() {
				        return nickName;
				    }
				
				    public void setNickName(String nickName) {
				        this.nickName = nickName;
				    }
				
				    public int getAge() {
				        return age;
				    }
				
				    public void setAge(int age) {
				        this.age = age;
				    }
				
				    public boolean isMale() {
				        return isMale;
				    }
				
				    public void setMale(boolean male) {
				        isMale = male;
				    }
				
				    public String getCity() {
				        return city;
				    }
				
				    public void setCity(String city) {
				        this.city = city;
				    }
				
				    public String getEmail() {
				        return email;
				    }
				
				    public void setEmail(String email) {
				        this.email = email;
				    }
				
				    public int getPhoneNumber() {
				        return phoneNumber;
				    }
				
				    public void setPhoneNumber(int phoneNumber) {
				        this.phoneNumber = phoneNumber;
				    }
				}
		
		- 通过反射解析注解 AnnotationTest.java
		
				public class AnnotationTest {
				
				    public static void main(String[] args) {
				        User u1 = new User();
				        u1.setId(1023);
				        u1.setMale(false);
				        u1.setAge(22);
				        u1.setEmail("jerome@android.com");
				        u1.setPhoneNumber(1378902);
				        u1.setCity("beijing");
				
				        System.out.println(query(u1));
				    }
				
				    public static String query(Object student) {
				        StringBuffer sb = new StringBuffer();
				        sb.append("select * from ");
				        Class c = student.getClass();
				        boolean isExit = c.isAnnotationPresent(Table.class);
				        if (!isExit) {
				            return null;
				        }
				        Table table = (Table) c.getAnnotation(Table.class);
				        String value = table.value();
				        sb.append(value).append(" where 1=1 ");
				
				        Field[] fields = c.getDeclaredFields();
				        for (Field field : fields) {
				            if (!field.isAnnotationPresent(Column.class)) {
				                continue;
				            }
				            Column column = field.getAnnotation(Column.class);
				            String columnName = column.value();
				            String fieldName = field.getName();
				
				            StringBuffer tempSb = new StringBuffer();
				            if (fieldName.startsWith("is")) {
				                tempSb.append(fieldName);
				            } else {
				                tempSb.append("get").append(fieldName.substring(0, 1).toUpperCase()).append(fieldName.substring(1));
				            }
				
				            String methodName = tempSb.toString();
				            try {
				                Method method = c.getMethod(methodName);
				                Object fieldValue = method.invoke(student);
				                if (fieldValue instanceof Integer && ((Integer) fieldValue != 0)) {
				                    sb.append(" and ").append(columnName).append(" = ").append(fieldValue);
				                } else if (fieldValue instanceof String && fieldValue != null) {
				                    sb.append(" and ").append(columnName).append(" = ").append("'").append(fieldValue).append("'");
				                } else if (fieldValue instanceof Boolean) {
				                    sb.append(" and ").append(columnName).append(" = ").append(fieldValue);
				                }else if (fieldValue instanceof Float && (Float)fieldValue != 0){
				                    sb.append(" and ").append(columnName).append(" = ").append(fieldValue);
				                }else if (fieldValue instanceof Double && (Double)fieldValue != 0){
				                    sb.append(" and ").append(columnName).append(" = ").append(fieldValue);
				                }else if (fieldValue instanceof Character && (Character)fieldValue != null){
				                    sb.append(" and ").append(columnName).append(" = ").append("'").append(fieldValue).append("'");
				                }
				            } catch (NoSuchMethodException e) {
				                e.printStackTrace();
				            } catch (InvocationTargetException e) {
				                e.printStackTrace();
				            } catch (IllegalAccessException e) {
				                e.printStackTrace();
				            }
				        }
				        return sb.toString();
				    }
				}

		- 结果：
		
	
				select * from user where 1=1  and id = 1023 and age = 22 and is_male = false and city = 'beijing' and email = 'jerome@android.com' and phone_number = 1378902
  


- 注解原理


  - 当 Java 源代码被编译时，编译器的一个插件 annotation 处理器则会处理这些 annotation 。处理器可以产生报告信息，或者创建附加的 Java 源文件或资源。如果annotation 本身被加上了 RententionPolicy 的运行时类，则 Java 编译器则会将annotation 的元数据存储到 class 文件中。然后，Java 虚拟机或其他的程序可以查找这些元数据并做相应的处理。

  - 当然除了 annotation 处理器可以处理 annotation 外，我们也可以使用反射自己来处理annotation 。Java SE 5 有一个名为 AnnotatedElement 的接口，Java 的反射对象类Class , Constructor , Field , Method 以及 Package 都实现了这个接口。这个接口用来表示当前运行在 Java 虚拟机中的被加上了 annotation 的程序元素。通过这个接口可以使用反射读取 annotation 。AnnotatedElement 接口可以访问被加上 RUNTIME 标记的annotation，相应的方法有 getAnnotation , getAnnotations , isAnnotationPresent 。由于 Annotation 类型被编译和存储在二进制文件中就像 class 一样，所以可以像查询普通的 Java 对象一样查询这些方法返回的 Annotation。
