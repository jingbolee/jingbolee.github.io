---
layout:     post
title:      "Android LruCache源码解析"
subtitle:   "LruCache"
date:       2016-06-03
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

# Andoid内存缓存方案LruCache使用和源码解析

## LRU算法

- Least Recent Used的缩写，最近最少使用，

## Andoid的LruCache

- Android的LruCache使用LinkedHashMap来实现的，使用了第三个构造方法，更改该map的迭代顺序由插入顺序变成为访问顺序，然后设置一个缓存最大的容量。
	- LinkedHashMap原理，依据访问顺序进行迭代的时候，最新访问的key，会排到尾部，然后按照访问顺序的时间早晚依次从尾部开始排序，最早访问key或者没有被访问的key，会被排到头部。因为LinkedHashMap是在HashMap的基础上使用了nxt,prv双链构造。
	- 对于插入操作，会先进行最大容量的判断，假如达到了最大容量，就迭代出map中的第一个键值对，然后执行删除，然后继续进行判断，直到当前缓存容量小于最大容量，把当前的键值对插入到map中，把当前值的大小加入到当前容量。
	- 对于读取操作，读取key的时候，key会挪动到map的尾部
	- LruCache使用了LinkedHashMap迭代出第一个元素，这个元素就是最近最少使用的key，然后remove掉就可以了，这样就清理了内存缓存了。


- 构造方法
	- 只有一个构造方法，每次使用的时候，需要传入一个缓存的大小。一般设置当前内存的1/8
	- 在该构造方法中，会初始化LinkedHahMap,调用了LinkedHashMap的第三个构造方法，设置了初始化容量大小为0，负载因子为0.75f,迭代顺序为访问顺序（默认为false，即迭代顺序为插入顺序）

			public LruCache(int maxSize) {
		        if (maxSize <= 0) {
		            throw new IllegalArgumentException("maxSize <= 0");
		        }
				//初始化最大缓存大小.
		        this.maxSize = maxSize;
				// 初始化LinkedHashMap.其中:
			    // 1. initialCapacity, 初始大小.
			    // 2. loadFactor, 负载因子.
			    // 3. accessOrder, true:基于访问顺序排序, false:基于插入顺序排序.
		        this.map = new LinkedHashMap<K, V>(0, 0.75f, true);
		    }

- 计算是否达到最大值的方法并对LinkedHashMap第一个值执行删除：trimToSize(maxSize)
	
	    public void trimToSize(int maxSize) {
	        while ( true ) {  死循环，不断的查看size是否大于了maxSize了，大于了，就执行删除LinkedHashMap第一个值的操作。
	            K key;
	            V value;
	            synchronized ( this ) {
	                if ( size < 0 || (map.isEmpty() && size != 0) ) {
	                    throw new IllegalStateException(getClass().getName()
	                            + ".sizeOf() is reporting inconsistent results!");
	                }
	
	                if ( size <= maxSize ) {  //size小于设定的最大值，跳出
	                    break;
	                }
	
					//获取最近最久没访问过的元素
					//eldest()方法应该是lru算法的核心，返回最近最少使用的entry，然后移出。
	                Map.Entry< K, V > toEvict = map.eldest();
	                if ( toEvict == null ) {
	                    break;
	                }
	
	                key = toEvict.getKey();
	                value = toEvict.getValue();
	                map.remove(key);
	                size -= safeSizeOf(key, value);
	                evictionCount++;        //移出的数量加1
	            }
	
	            entryRemoved(true, key, value, null);
	        }
	    }

	---
		map的eldest()方法。当链表为null的时候，header.nxt==header并且header==header.prv。header.nxt为第一个entry,header.prv为最后一个entry.

	    public Entry<K, V> eldest() {
	        LinkedEntry<K, V> eldest = header.nxt;  // 获取到链表第一个元素
	        return eldest != header ? eldest : null; //map不为null的时候，返回第一个元素。
  		  }

> Picasso的LruCache的trimToSize(maxSize)源码

	private void trimToSize(int maxSize) {
	    while (true) {
	      String key;
	      Bitmap value;
	      synchronized (this) {
	        if (size < 0 || (map.isEmpty() && size != 0)) {
	          throw new IllegalStateException(
	              getClass().getName() + ".sizeOf() is reporting inconsistent results!");
	        }
	
	        if (size <= maxSize || map.isEmpty()) {
	          break;
	        }
	
	        Map.Entry<String, Bitmap> toEvict = map.entrySet().iterator().next();
	        key = toEvict.getKey();
	        value = toEvict.getValue();
	        map.remove(key);
	        size -= Utils.getBitmapBytes(value);
	        evictionCount++;
	      }
	    }
  	}

- 访问操作

 	    public final V get(K key) {
	        if ( key == null ) {    //key不能为null,为null的时候，抛出异常
	            throw new NullPointerException("key == null");
	        }
	
	        V mapValue;
	        synchronized ( this ) {
				// LinkedHashMap线程不安全，LinkedHashMap每次get会对访问顺序进行排序
	            mapValue = map.get(key);   
	            if ( mapValue != null ) {
	                hitCount++;          //get()，获取到值的情况下，加1
	                return mapValue;
	            }
	            missCount++;			//没有获取到值的情况下，加1
	        }
	
	        /*
	         * Attempt to create a value. This may take a long time, and the map
	         * may be different when create() returns. If a conflicting value was
	         * added to the map while create() was working, we leave that value in
	         * the map and release the created value.
	         */
	
	        V createdValue = create(key);
	        if ( createdValue == null ) {
	            return null;
	        }
	
	        synchronized ( this ) {
	            createCount++;
	            mapValue = map.put(key, createdValue);
	
	            if ( mapValue != null ) {
	                // There was a conflict so undo that last put
	                map.put(key, mapValue);
	            } else {
	                size += safeSizeOf(key, createdValue);
	            }
	        }
	
	        if ( mapValue != null ) {
	            entryRemoved(false, key, createdValue, mapValue);
	            return mapValue;
	        } else {
	            trimToSize(maxSize);
	            return createdValue;
	        }
        }

- 插入操作

	    public final V put(K key, V value) {
	        if ( key == null || value == null ) {  
	            throw new NullPointerException("key == null || value == null");
	        }
	
	        V previous;
	        synchronized ( this ) {
	            putCount++;
	            size += safeSizeOf(key, value);  
	            previous = map.put(key, value);
	            if ( previous != null ) {
	                size -= safeSizeOf(key, previous);
	            }
	        }
	
	        if ( previous != null ) {
	            entryRemoved(false, key, previous, value);
	        }
	
	        trimToSize(maxSize);  //计算当前的最大容量
	        return previous;
  		  }

- 计算key的value的大小

	    private int safeSizeOf(K key, V value) {
	        int result = sizeOf(key, value);     //会调用sizeOf()方法，比如存储BitMap的时候，需要重写该方法，。
	        if ( result < 0 ) {
	            throw new IllegalStateException("Negative size: " + key + "=" + value);
	        }
	        return result;
  		  }

- 系统默认的sizeOf()方法，一般使用LruCache的时候，需要重写该方法，重新计算需要存储value的大小。

	    protected int sizeOf(K key, V value) {
        	return 1;
    	}

- LruCache的使用
	- 就按照HashMap的样子使用就可以了。只不过初始化的时候，需要指定一个大小值。

			int maxSize=(int) (Runtime.getRuntime().maxMemory()/8);
	        LruCache<String ,Bitmap > lruCache=new LruCache<String ,Bitmap>(maxSize){
	            @Override
	            protected int sizeOf(String key, Bitmap bitmap) {
	                return bitmap.getByteCount();
					//return bitmap.getRowBytes()*bitmap.getHeight();
		            }
		        };

> 知识补充

- 线性结构：在数据元素的非空有限集合中，1.存在唯一的一个被称为“第一个”的数据元素；2.存在唯一的一个被称为“最后一个”的数据元素；3.除第一个之外，集合中的每个数据元素均只有一个前驱；4.除最后一个之外，集合中的每个数据元素均只有一个后继。
- LinkedHashMap中双链结构，第一个entry叫header.nxt,最后一个entry叫header.prv；假如map为null的话，header.nxt==header && header==header.prv