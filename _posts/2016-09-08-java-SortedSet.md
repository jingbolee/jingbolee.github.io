---
layout:     post
title:      "Java SortedSet"
subtitle:   "SortedSet"
date:       2016-09-12
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## SortedSet 接口

- SortedSet<E> 接口继承 Set<E> 接口，实现该接口的子类或者实现类都可以进行排序。
- 一个提供对元素排序的 Set（Set 特点：无序，不重复）,元素按照接口 Comparable 的自然顺序或者通过接口 Comparator 的实现类提供一个排序器进行排序。Set 的迭代器按照元素升序进行遍历。
- 插入的元素必须实现必须实现了 Comparable 接口（或者通过 Comparator 接口实现类），此外，元素实现 Comparable 接口的必须实现 compareTo() 方法 或者 Comparator 接口的 compare() 方法，否则会抛出 ClassCastException 异常
- TreeSet<E> ，实现了该接口的类，可以对元素做到排序和不重复。 

## 定义的方法

- Comparator<? super E> comparator():返回这个 Set 的排序器，假如使用的是 Comparable 的自然排序就返回 null。
 
- E first()：返回第一个元素
 
- SortedSet<E> headSet(E toElement)：返回小于等于指定元素的部分 Set
 
- E last()：返回最后一个元素
 
- Spliterator<E> spliterator()：
 
- SortedSet<E> subSet(E fromElement, E toElement):返回指定元素范围的 Set
 
- SortedSet<E> tailSet(E fromElement)：返回大于指定元素的部分 Set


## 示例

- Book.java

		class Book implements Comparable {
		    private String name;
		    private double price;
		
		    public Book() {
		    }
		
		    public Book(String name, double price) {
		        this.name = name;
		        this.price = price;
		    }
		
		    public String getName() {
		        return name;
		    }
		
		    public void setName(String name) {
		        this.name = name;
		    }
		
		    public double getPrice() {
		        return price;
		    }
		
		    public void setPrice(double price) {
		        this.price = price;
		    }
		
		    @Override
		    public boolean equals(Object o) {
		        if (this == o) return true;
		        if (o == null || getClass() != o.getClass()) return false;
		
		        Book book = (Book) o;
		
		        return name.equals(book.name);
		
		    }
		
		    @Override
		    public int hashCode() {
		        return name.hashCode();
		    }
		
		    @Override
		    public String toString() {
		        return "书名：" + name + ", 价值：" + price;
		    }
		
		    @Override
		    public int compareTo(Object o) {
		        Book b = (Book) o;
		        return this.getName().compareTo(b.getName());
		    }
		}

- TestSortedSet.java

		public class TestSortedSet {
		    private Set<Book> set;
		    private SortedSet<Book> sortedSet;
		
		    public void testAddData() {
		        set = new HashSet<>();
		        Book b1 = new Book("大话设计模式", 59.88);
		        Book b2 = new Book("HTML5 与 CSS ", 49.99);
		        Book b3 = new Book("第一行代码", 49.00);
		        Book b4 = new Book("Head First 设计模式", 39.00);
		        Book b5 = new Book("人月神话", 89.79);
		        Book b6 = new Book("Android 群英传", 59.99);
		        Book b7 = new Book("Android 开发实战", 29.88);
		        Book b8 = new Book("Java 入门实践", 19.88);
		        Book b9 = new Book("股市风险", 9.88);
		        Book b0 = new Book("HTTP 详解", 19.88);
		        set.add(b1);
		        set.add(b2);
		        set.add(b3);
		        set.add(b4);
		        set.add(b5);
		        set.add(b6);
		        set.add(b7);
		        set.add(b8);
		        set.add(b9);
		        set.add(b0);
		        set.add(b1);
		
		    }
		
		    public void testCompare() {
		        System.out.println("-----------排序前-------------");
		        testIterator(set);
		        sortedSet = new TreeSet<>(set);
		        System.out.println("-----------排序后-------------");
		        testIterator(sortedSet);
		        System.out.println("-----------第一个元素-------------");
		        System.out.println(sortedSet.first());
		        System.out.println("-----------最后一个元素-------------");
		        System.out.println(sortedSet.last());
		        System.out.println("-----------返回小于某个元素的 Set-------------");
		        testIterator(sortedSet.headSet(new Book("Java 入门实践", 19.88)));
		        System.out.println("-----------返回指定元素范围的 Set-------------");
		        testIterator(sortedSet.subSet(new Book("HTML5 与 CSS ", 49.99), new Book("Java 入门实践", 19.88)));
		        System.out.println("-----------返回大于指定元素的 Set-------------");
		        testIterator(sortedSet.tailSet(new Book("HTML5 与 CSS ", 49.99)));
		    }
		
		
		    //遍历 Set
		    public void testIterator(Set<Book> set) {
		        Iterator<Book> iterator = set.iterator();
		        while (iterator.hasNext()) {
		            Book next = iterator.next();
		            System.out.println("书名：" + next.getName() + ", 价值：" + next.getPrice());
		        }
		    }
		
		    public static void main(String[] args) {
		        TestSortedSet tss = new TestSortedSet();
		        tss.testAddData();
		        tss.testCompare();
		    }
		}

- 测试结果

		-----------排序前-------------
		书名：HTML5 与 CSS , 价值：49.99
		书名：股市风险, 价值：9.88
		书名：Android 群英传, 价值：59.99
		书名：第一行代码, 价值：49.0
		书名：Head First 设计模式, 价值：39.0
		书名：人月神话, 价值：89.79
		书名：Java 入门实践, 价值：19.88
		书名：HTTP 详解, 价值：19.88
		书名：Android 开发实战, 价值：29.88
		书名：大话设计模式, 价值：59.88
		-----------排序后-------------
		书名：Android 开发实战, 价值：29.88
		书名：Android 群英传, 价值：59.99
		书名：HTML5 与 CSS , 价值：49.99
		书名：HTTP 详解, 价值：19.88
		书名：Head First 设计模式, 价值：39.0
		书名：Java 入门实践, 价值：19.88
		书名：人月神话, 价值：89.79
		书名：大话设计模式, 价值：59.88
		书名：第一行代码, 价值：49.0
		书名：股市风险, 价值：9.88
		-----------第一个元素-------------
		书名：Android 开发实战, 价值：29.88
		-----------最后一个元素-------------
		书名：股市风险, 价值：9.88
		-----------返回小于某个元素的 Set-------------
		书名：Android 开发实战, 价值：29.88
		书名：Android 群英传, 价值：59.99
		书名：HTML5 与 CSS , 价值：49.99
		书名：HTTP 详解, 价值：19.88
		书名：Head First 设计模式, 价值：39.0
		-----------返回指定元素范围的 Set-------------
		书名：HTML5 与 CSS , 价值：49.99
		书名：HTTP 详解, 价值：19.88
		书名：Head First 设计模式, 价值：39.0
		-----------返回大于指定元素的 Set-------------
		书名：HTML5 与 CSS , 价值：49.99
		书名：HTTP 详解, 价值：19.88
		书名：Head First 设计模式, 价值：39.0
		书名：Java 入门实践, 价值：19.88
		书名：人月神话, 价值：89.79
		书名：大话设计模式, 价值：59.88
		书名：第一行代码, 价值：49.0
		书名：股市风险, 价值：9.88