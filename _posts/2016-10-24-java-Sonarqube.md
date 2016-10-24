---
layout:     post
title:      "SonarQube Bug 总结"
subtitle:   "SonarQube Bug"
date:       2016-10-24
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## SonarQube Bug 总结


### Conditions should not unconditionally evaluate to "TRUE" or to "FALSE"

- Change this condition so that it does not always evaluate to "true" 

### Null pointers should not be dereferenced

### "equals(Object obj)" and "hashCode()" should be overridden in pairs

### Math operands should be cast before assignment

- 常见：Cast one of the operands of this multiplication operation to a "long". 
	- 错误示例：

			public static final long DELETE_MILLIS = 2 * 60 * 1000;

	- 正确示例：

			public static final long DELETE_MILLIS = 2L * 60L * 1000L;


### Raw byte values should not be used in bitwise operations in combination with shifts

- Prevent "int" promotion by adding "& 0xff" to this expression.
	- 错误示例：

            byte val1 = ((b1 & SIGN) == 0) ? (byte) (b1 >> 2) : (byte) ((b1) >> 2 ^ 0xc0);
            byte val2 = ((b2 & SIGN) == 0) ? (byte) (b2 >> 4) : (byte) ((b2) >> 4 ^ 0xf0);
            byte val3 = ((b3 & SIGN) == 0) ? (byte) (b3 >> 6) : (byte) ((b3) >> 6 ^ 0xfc);
 
            if ( fDebug ) {
                System.out.println("val2 = " + val2);
                System.out.println("k4   = " + (k << 4));
                System.out.println("vak  = " + (val2 | (k << 4)));
            }

	- 正确示例：

            byte val1 = ((b1 & SIGN) == 0) ? (byte) (b1 >> 2) : (byte) ((b1) >> 2 ^ 0xc0);
            byte val2 = ((b2 & SIGN) == 0) ? (byte) (b2 >> 4) : (byte) ((b2) >> 4 ^ 0xf0);
            byte val3 = ((b3 & SIGN) == 0) ? (byte) (b3 >> 6) : (byte) ((b3) >> 6 ^ 0xfc);
 
            if ( fDebug ) {
                System.out.println("val2 = " + val2);
                System.out.println("k4   = " + (k << 4));
                System.out.println("vak  = " + (val2 & 0xff | (k << 4)));
            }


### Exception classes should be immutable

- Make this "retId" field final.  

	- 错误示例：

			public class RXSecurityException extends Exception {
			
			    private static final long serialVersionUID = -8983732178211417434L;
			
			    private int retId; // 返回错误ID
			    private String msgDes; // 返回错误消息
			    private String exceptionType; //异常类型
			
			
			    public String getExceptionType() {
			        return exceptionType;
			    }
			
			    public void setExceptionType(String exceptionType) {
			        this.exceptionType = exceptionType;
			    }
			
			    public int getRetId() {
			        return retId;
			    }
			
			    public void setRetId(int retId) {
			        this.retId = retId;
			    }
			
			    public String getMsgDes() {
			        return msgDes;
			    }
			
			    public void setMsgDes(String msgDes) {
			        this.msgDes = msgDes;
			    }
			
			    public RXSecurityException() {
			        super();
			    }
			
			    public RXSecurityException(String msg) {
			        super(msg);
			        this.msgDes = msg;
			    }
			
			    public RXSecurityException(int rtId, String exceptionType, String msg) {
			        this.retId = rtId;
			        this.msgDes = msg;
			        this.exceptionType = exceptionType;
			    }
			}

	- 正确示例：


			public class RXSecurityException extends Exception {
			 
			    private static final long serialVersionUID = -8983732178211417434L;
			 
			    private final int retId; // 返回错误ID
			    private final String msgDes; // 返回错误消息
			    private final String exceptionType; //异常类型
			 
			 
			    public String getExceptionType() {
			        return exceptionType;
			    }
			 
			    public int getRetId() {
			        return retId;
			    }
			 
			    public String getMsgDes() {
			        return msgDes;
			    }
			 
			    public RXSecurityException(int rtId, String exceptionType, String msg) {
			        this.retId = rtId;
			        this.msgDes = msg;
			        this.exceptionType = exceptionType;
			    }
			 
			}


### "hashCode" and "toString" should not be called on array instances

- Use "Arrays.toString(array)" instead
	- 错误示例：
	
			 MLog.e(TAG, "创建讨论组%s失败,%s", roomName, e.getStackTrace().toString());

	- 正确示例：

			MLog.e(TAG, "创建讨论组%s失败,%s", roomName, Arrays.toString(e.getStackTrace()));


### Identical expressions should not be used on both sides of a binary operator


- Identical sub-expressions on both sides of operator "||" 

### Synchronized classes Vector, Hashtable, Stack and StringBuffer should not be used

- Early classes of the Java API, such as Vector, Hashtable and StringBuffer, were synchronized to make them thread-safe. Unfortunately, synchronization has a big negative impact on performance, even when using these collections from a single thread.

- It is better to use their new unsynchronized replacements:

	- **ArrayList** or **LinkedList** instead of **Vector**
	- **Deque** instead of **Stack**
	- **HashMap** instead of **Hashtable**
	- **StringBuilder** instead of **StringBuffer**


### Return values should not be ignored when function calls don't have any side effects

- The return value of "replaceAll" must be used.