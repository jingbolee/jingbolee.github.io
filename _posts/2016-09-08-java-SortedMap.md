---
layout:     post
title:      "Java SortedMap "
subtitle:   "SortedMap"
date:       2016-09-08
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## SortedMap 接口

- SortedMap<K,V> 接口继承 Map<K,V> 接口，并定义了排序的方法，实现该接口的子类的 key 都可以进行排序。
- TreeMap<K,V> 是实现了该接口的类，所以该类可以实现排序


## SortedMap 定义的方法

- Comparator<? super K> comparator()：返回比较器对象

- K firstKey()：返回第一个元素的 key

- K lastKey()：返回最后一个元素的 key

- SortedMap<K,V> headMap(K toKey)：返回小于等于指定 key 的部分集合

- SortedMap<K,V> subMap(K fromKey, K toKey)：返回指定 key 范围的集合

- SortedMap<K,V> tailMap(K fromKey)：返回大于指定 key 的部分集合

## 示例

- TestSortedMap.java

		public class TestSortedMap {
		    private Map<String, String> map;
		    private SortedMap<String, String> sortedMap;
		
		    public void testAddData() {
		        map = new HashMap<>();
		        map.put("1", "Tom");
		        map.put("Hi", "World");
		        map.put("Hello", "World");
		        map.put("a", "B");
		        map.put("A", "qwe");
		        map.put("D", "asd");
		        map.put("cv", "da");
		        map.put("qwe", "B");
		        map.put("a", "B111");
		        //对 Map 进行排序
		        sortedMap = new TreeMap<>(map);
		    }
		
		    public void testCompara() {
		        System.out.println("-----------排序前------------");
		        testIterator(map);
		        System.out.println();
		        System.out.println("-----------排序后------------");
		        testIterator(sortedMap);
		        System.out.println();
		        System.out.println("SortedMap 中第一个 key:" + sortedMap.firstKey() + ",值为:" + sortedMap.get(sortedMap.firstKey()));
		        System.out.println("SortedMap 中最后一个 key:" + sortedMap.lastKey() + ",值为:" + sortedMap.get(sortedMap.lastKey()));
		
		        SortedMap<String, String> sortedMap2 = sortedMap.headMap("cv");
		        System.out.println("-----------返回小于指定范围的集合------------");
		        testIterator(sortedMap2);
		        System.out.println();
		        System.out.println("sortedMap2 中第一个 key:" + sortedMap2.firstKey() + ",值为:" + sortedMap2.get(sortedMap2.firstKey()));
		        System.out.println("sortedMap2 中最后一个 key:" + sortedMap2.lastKey() + ",值为:" + sortedMap2.get(sortedMap2.lastKey()));
		
		        SortedMap<String, String> sortedMap3 = sortedMap.subMap("A", "a");
		        System.out.println("-----------部分集合------------");
		        testIterator(sortedMap3);
		        System.out.println();
		        System.out.println("sortedMap3 中第一个 key:" + sortedMap3.firstKey() + ",值为:" + sortedMap3.get(sortedMap3.firstKey()));
		        System.out.println("sortedMap3 中最后一个 key:" + sortedMap3.lastKey() + ",值为:" + sortedMap3.get(sortedMap3.lastKey()));
		
		
		        SortedMap<String, String> sortedMap4 = sortedMap.tailMap("D");
		        System.out.println("-----------返回大于指定范围的集合------------");
		        testIterator(sortedMap4);
		        System.out.println();
		        System.out.println("sortedMap4 中第一个 key:" + sortedMap4.firstKey() + ",值为:" + sortedMap4.get(sortedMap4.firstKey()));
		        System.out.println("sortedMap4 中最后一个 key:" + sortedMap4.lastKey() + ",值为:" + sortedMap4.get(sortedMap4.lastKey()));
		    }
		
		    //遍历 Map 
		    public void testIterator(Map<String, String> sortedMap) {
		        Iterator<Map.Entry<String, String>> iterators = sortedMap.entrySet().iterator();
		        while (iterators.hasNext()) {
		            Map.Entry<String, String> next = iterators.next();
		            System.out.println("SortedMap中的元素：" + next.getKey() + ",值为:" + next.getValue());
		        }
		    }
		
		    public static void main(String[] args) {
		        TestSortedMap tsm = new TestSortedMap();
		        tsm.testAddData();
		        tsm.testCompara();
		    }
		}

- 测试结果

		-----------排序前------------
		SortedMap中的元素：1,值为:Tom
		SortedMap中的元素：Hi,值为:World
		SortedMap中的元素：a,值为:B111
		SortedMap中的元素：A,值为:qwe
		SortedMap中的元素：cv,值为:da
		SortedMap中的元素：Hello,值为:World
		SortedMap中的元素：D,值为:asd
		SortedMap中的元素：qwe,值为:B
		
		-----------排序后------------
		SortedMap中的元素：1,值为:Tom
		SortedMap中的元素：A,值为:qwe
		SortedMap中的元素：D,值为:asd
		SortedMap中的元素：Hello,值为:World
		SortedMap中的元素：Hi,值为:World
		SortedMap中的元素：a,值为:B111
		SortedMap中的元素：cv,值为:da
		SortedMap中的元素：qwe,值为:B
		
		SortedMap 中第一个 key:1,值为:Tom
		SortedMap 中最后一个 key:qwe,值为:B
		-----------返回小于指定范围的集合------------
		SortedMap中的元素：1,值为:Tom
		SortedMap中的元素：A,值为:qwe
		SortedMap中的元素：D,值为:asd
		SortedMap中的元素：Hello,值为:World
		SortedMap中的元素：Hi,值为:World
		SortedMap中的元素：a,值为:B111
		
		sortedMap2 中第一个 key:1,值为:Tom
		sortedMap2 中最后一个 key:a,值为:B111
		-----------部分集合------------
		SortedMap中的元素：A,值为:qwe
		SortedMap中的元素：D,值为:asd
		SortedMap中的元素：Hello,值为:World
		SortedMap中的元素：Hi,值为:World
		
		sortedMap3 中第一个 key:A,值为:qwe
		sortedMap3 中最后一个 key:Hi,值为:World
		-----------返回大于指定范围的集合------------
		SortedMap中的元素：D,值为:asd
		SortedMap中的元素：Hello,值为:World
		SortedMap中的元素：Hi,值为:World
		SortedMap中的元素：a,值为:B111
		SortedMap中的元素：cv,值为:da
		SortedMap中的元素：qwe,值为:B
		
		sortedMap4 中第一个 key:D,值为:asd
		sortedMap4 中最后一个 key:qwe,值为:B