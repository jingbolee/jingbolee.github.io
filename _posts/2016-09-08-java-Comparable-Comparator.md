---
layout:     post
title:      "Java 中的 Comparable 接口和 Comparator 接口"
subtitle:   "Comparable 、 Comparator"
date:       2016-09-08
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## Comparable 接口

- 接口简介：
	- 实现该接口的类可以进行排序，通过实现该接口的 comparaTo() 方法进行比较来排序。
	- List 或者 Array 中的对象实现了该接口，可以通过工具类 Collections.sort(List) 或者 Arrays.sort(Object[]) 进行排序。
	- 实现该接口的对象，可以作为 SortedMap 实现类的 key 或者作为 SortedSet 实现类的元素，不需要再指定 Comparator 接口的实现类。
	- 用来作为默认比较规则

- 排序
	- List:Collections.sort(List)
	- Array:Arrays.sort(Object[])

- 实现方法
	- int compareTo(T o):返回值为负数、0、正数，表示小于、等于、大于

## Comparator 接口

- 接口简介：
	- 该接口主要是用于 List 或者 Array 中的对象没有实现 Comparable 接口但是需要排序的情况；同时也可以用来对已经实现了 Comparable 接口的对象按照 Comparator 接口实现规则进行排序。
	- 对 List 或 Array 中的对象进行排序
	- List 排序通过工具类 Collections.sort(List,Comparator);Array 排序通过工具类 Arrays.sort(Object[], Comparator)，List 或者 Array 中的对象即可以实现 Comparable 接口也可以不实现 Comparable 接口。
	- 一般用于临时比较规则

- 排序
	- List:Collections.sort(List,Comparator)
	- Array:Arrays.sort(Object[], Comparator)

- 实现方法
	- int compare(T o1, T o2)：比较两个对象的大小，返回值为负数、0、正数，表示小于、等于、大于

## 两个接口相同点

- 都是通过工具类 Collections.sort() 和 Arrays.sort() 对 List 和 Array 进行排序。
- 都是 Java 集合框架的成员

## 总结

- Comparator 接口主要用于临时的比较和排序，比较的对象不需要实现该接口，只需要在工具类调用 sort() 方法的时候，传入一个实现了该接口的对象或者传入一个 new Comparator() 匿名内部类就可以了；
- Comparable 接口的对象实现该接口，实现 comparaTo() 方法，实现了该接口的对象，也可以通过 Comparator 接口重新进行排序而不必按照 Comparable 接口实现的排序方式进行排序。


## 示例

### 实现 Comparable 接口进行排序

- 依据公司的名称进行排序

	- Company.java

			public class Company implements Comparable {
			
			    private String name;
			    private int money;
			
			    public Company() {
			    }
			
			    public Company(String name, int money) {
			        this.name = name;
			        this.money = money;
			    }
			
			    public String getName() {
			        return name;
			    }
			
			    public void setName(String name) {
			        this.name = name;
			    }
			
			    public int getMoney() {
			        return money;
			    }
			
			    public void setMoney(int money) {
			        this.money = money;
			    }
			
			    /**
			     * 依据公司的名称进行排序
			     */
			    @Override
			    public int compareTo(Object o) {
			        Company company = (Company) o;
			        return this.getName().compareTo(company.getName());
			    }
			}

	- TestComparable.java

			public class TestComparable {
			
			    List<Company> companyList;
			
			    public void testAddData() {
			        companyList = new ArrayList<>();
			        Company google = new Company("Google", 900000);
			        Company ibm = new Company("IBM", 500000);
			        Company jingdong = new Company("JingDong", 2000);
			        Company amazon = new Company("Amazon", 20000);
			        Company tencent = new Company("Tencent", 2000);
			        Company apple = new Company("Apple", 2000);
			        companyList.add(google);
			        companyList.add(ibm);
			        companyList.add(jingdong);
			        companyList.add(amazon);
			        companyList.add(tencent);
			        companyList.add(apple);
			    }
			
			    public void testCompara() {
			        System.out.println("-----------排序前-----------");
			        for (Company c : companyList) {
			            System.out.println("公司：" + c.getName() + ",市值:" + c.getMoney());
			        }
			        Collections.sort(companyList);
			        System.out.println("-----------排序后-----------");
			        for (Company c : companyList) {
			            System.out.println("公司：" + c.getName() + ",市值:" + c.getMoney());
			        }
			    }
			
			    public static void main(String[] args) {
			        TestComparable tc = new TestComparable();
			        tc.testAddData();
			        tc.testCompara();
			    }
			}

	- 测试结果（排序以后，按照公司的名称进行了排序）

			-----------排序前-----------
			公司：Google,市值:900000
			公司：IBM,市值:500000
			公司：JingDong,市值:2000
			公司：Amazon,市值:20000
			公司：Tencent,市值:2000
			公司：Apple,市值:2000
			-----------排序后-----------
			公司：Amazon,市值:20000
			公司：Apple,市值:2000
			公司：Google,市值:900000
			公司：IBM,市值:500000
			公司：JingDong,市值:2000
			公司：Tencent,市值:2000	


### 通过 Comparator 接口进行排序

- 依据公司市值进行排序

	- Company.java（与上面的 Company.java 一致）

			public class Company implements Comparable {
			
			    private String name;
			    private int money;
			
			    public Company() {
			    }
			
			    public Company(String name, int money) {
			        this.name = name;
			        this.money = money;
			    }
			
			    public String getName() {
			        return name;
			    }
			
			    public void setName(String name) {
			        this.name = name;
			    }
			
			    public int getMoney() {
			        return money;
			    }
			
			    public void setMoney(int money) {
			        this.money = money;
			    }
			
			    /**
			     * 依据公司的名称进行排序
			     */
			    @Override
			    public int compareTo(Object o) {
			        Company company = (Company) o;
			        return this.getName().compareTo(company.getName());
			    }
			}

	- TestComparator.java（实现了 Comparator 接口，重写 compare() ,对公司市值进行比较）

			public class TestComparator implements Comparator {
			
			    /**
			     * 按照公司的市值进行排序
			     */
			    @Override
			    public int compare(Object o1, Object o2) {
			        int result = 0;
			        Company c1 = (Company) o1;
			        Company c2 = (Company) o2;
			        if (c1.getMoney() > c2.getMoney()) {
			            result = 1;
			        } else if (c1.getMoney() == c2.getMoney()) {
			            result = 0;
			        } else if (c1.getMoney() < c2.getMoney()) {
			            result = -1;
			        }
			        return result;
			    }
			
			    List<Company> companyList;
			
			    public void testAddData() {
			        companyList = new ArrayList<>();
			        Company google = new Company("Google", 900000);
			        Company ibm = new Company("IBM", 500000);
			        Company jingdong = new Company("JingDong", 2000);
			        Company amazon = new Company("Amazon", 20000);
			        Company tencent = new Company("Tencent", 2000);
			        Company apple = new Company("Apple", 2000);
			        companyList.add(google);
			        companyList.add(ibm);
			        companyList.add(jingdong);
			        companyList.add(amazon);
			        companyList.add(tencent);
			        companyList.add(apple);
			    }
			
			    public void testCompara() {
			        System.out.println("-----------排序前-----------");
			        for (Company c : companyList) {
			            System.out.println("公司：" + c.getName() + ",市值:" + c.getMoney());
			        }
			        Collections.sort(companyList, new TestComparator());
			        System.out.println("-----------排序后-----------");
			        for (Company c : companyList) {
			            System.out.println("公司：" + c.getName() + ",市值:" + c.getMoney());
			        }
			    }
			
			
			    public static void main(String[] args) {
			        TestComparator tc = new TestComparator();
			        tc.testAddData();
			        tc.testCompara();
			    }
			}

	- 测试结果（排序以后，按照公司的市值进行了排序）

			-----------排序前-----------
			公司：Google,市值:900000
			公司：IBM,市值:500000
			公司：JingDong,市值:2000
			公司：Amazon,市值:20000
			公司：Tencent,市值:2000
			公司：Apple,市值:2000
			-----------排序后-----------
			公司：JingDong,市值:2000
			公司：Tencent,市值:2000
			公司：Apple,市值:2000
			公司：Amazon,市值:20000
			公司：IBM,市值:500000
			公司：Google,市值:900000