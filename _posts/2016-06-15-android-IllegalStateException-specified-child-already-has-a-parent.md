---
layout:     post
title:      "IllegalStateExceptio:The specified child already has a parent."
subtitle:   "IllegalStateExceptio、inflate()"
date:       2016-06-15
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## Bug:

		java.lang.IllegalStateException: The specified child already has a parent.
		You must call removeView() on the child's parent first.

## 产生原因： 
	- 在使用 RecyclerView 的 Adapter 的时候，在 onCreateViewHolder() 方法中，使用  inflate() 方法的时候造成的。该方法有三个参数，我在第三个参数传入 true 的时候造成的

			  @Override public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
				    View view = LayoutInflater.from(mContext).inflate(R.layout.list_item_tel_code, parent, true);
				    ViewHolder holder = new ViewHolder(view);
				    return holder;
			 }

	- 假如第三个参数改为 false，就一切正常了。
	- 然后跟踪源码发现所有的 inflate() 方法最终都是调用这个方法，代码如下：

		    public View inflate(XmlPullParser parser, @Nullable ViewGroup root, boolean attachToRoot) {
		        synchronized (mConstructorArgs) {
		            Trace.traceBegin(Trace.TRACE_TAG_VIEW, "inflate");
		
		            final Context inflaterContext = mContext;
		            final AttributeSet attrs = Xml.asAttributeSet(parser);
		            Context lastContext = (Context) mConstructorArgs[0];
		            mConstructorArgs[0] = inflaterContext;
		            View result = root;
		
		            try {
		                // Look for the root node.
		                int type;
		                while ((type = parser.next()) != XmlPullParser.START_TAG &&
		                        type != XmlPullParser.END_DOCUMENT) {
		                    // Empty
		                }
		
		                if (type != XmlPullParser.START_TAG) {
		                    throw new InflateException(parser.getPositionDescription()
		                            + ": No start tag found!");
		                }
		
		                final String name = parser.getName();
		                
		                if (DEBUG) {
		                    System.out.println("**************************");
		                    System.out.println("Creating root view: "
		                            + name);
		                    System.out.println("**************************");
		                }
		
		                if (TAG_MERGE.equals(name)) {
		                    if (root == null || !attachToRoot) {
		                        throw new InflateException("<merge /> can be used only with a valid "
		                                + "ViewGroup root and attachToRoot=true");
		                    }
		
		                    rInflate(parser, root, inflaterContext, attrs, false);
		                } else {
		                    // Temp is the root view that was found in the xml
		                    final View temp = createViewFromTag(root, name, inflaterContext, attrs);
		
		                    ViewGroup.LayoutParams params = null;
		
		                    if (root != null) {
		                        if (DEBUG) {
		                            System.out.println("Creating params from root: " +
		                                    root);
		                        }
		                        // Create layout params that match root, if supplied
		                        params = root.generateLayoutParams(attrs);
		                        if (!attachToRoot) {
		                            // Set the layout params for temp if we are not
		                            // attaching. (If we are, we use addView, below)
		                            temp.setLayoutParams(params);
		                        }
		                    }
		
		                    if (DEBUG) {
		                        System.out.println("-----> start inflating children");
		                    }
		
		                    // Inflate all children under temp against its context.
		                    rInflateChildren(parser, temp, attrs, true);
		
		                    if (DEBUG) {
		                        System.out.println("-----> done inflating children");
		                    }
		
		                    // We are supposed to attach all the views we found (int temp)
		                    // to root. Do that now.
		                    if (root != null && attachToRoot) {
		                        root.addView(temp, params);
		                    }
		
		                    // Decide whether to return the root that was passed in or the
		                    // top view found in xml.
		                    if (root == null || !attachToRoot) {
		                        result = temp;
		                    }
		                }
		
		            } catch (XmlPullParserException e) {
		                InflateException ex = new InflateException(e.getMessage());
		                ex.initCause(e);
		                throw ex;
		            } catch (Exception e) {
		                InflateException ex = new InflateException(
		                        parser.getPositionDescription()
		                                + ": " + e.getMessage());
		                ex.initCause(e);
		                throw ex;
		            } finally {
		                // Don't retain static reference on context.
		                mConstructorArgs[0] = lastContext;
		                mConstructorArgs[1] = null;
		            }
		
		            Trace.traceEnd(Trace.TRACE_TAG_VIEW);
		
		            return result;
		        }
		    }

- 关键代码1：可以看到第二个参数不为 null 的时候，返回的 view 为第二个参数

		View result = root;

- 关键代码2： temp 是根据 root 来创建的视图

		final View temp = createViewFromTag(root, name, inflaterContext, attrs);

- 关键代码3： root 不为 null 且 attachToRoot 为 true ， temp 依附于 root

    	if (root != null && attachToRoot) {
                        root.addView(temp, params);
                    }

- 关键代码4：root 为 null 或者 attachToRoot 为 false，返回的值 result 为 temp

		if (root == null || !attachToRoot) {
                        result = temp;
                    }

- 根据关键代码1和关键代码3，可以得知：当第二个参数不为 null 和第三个参数为 true，加载的布局会依附于 parent，并返回 parent。根据 Bug 的描述，得知子 view 已经有 parent 了，在依附之前应该先调用 parent 的 removeView() 方法。所以造成该 Bug 的问题就是不能依附于 parent 了。所以当 inflate() 方法参数第二个为 null 或者第三个参数为 false 的时候，就都不会出现该 Bug。


## 补充

### 类 LayoutInflater

- LayoutInflater 是用来找 layout 下 xml 布局文件，并且实例化。与 findViewById()类似,区别在于一个是实例化 layout,一个是实例化XML布局中的 widget

### 方法 inflate() 

- 将 Layout 文件转换为 View。
- 这个方法主要用于填充一个新的视图层次结构从指定的 XML 资源文件中。

### 调用inflate()的几个常见方法

- 比如 RecyclerView 的 Adapter 中加载自定义 View ID。代码如下：
	- 通过 getSystemService 获取并加载 View ID

			LayoutInflater inflater =
			        (LayoutInflater) mContext.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
			View view = inflater.inflate(R.layout.list_item_tel_code, parent);

    - 通过 Context 获取并加载 View ID

			View view = LayoutInflater.from(mContext).inflate(R.layout.list_item_tel_code, parent, false);

		或者：
		
		    View view = LayoutInflater.from(mContext).inflate(R.layout.list_item_tel_code, parent);

		或者：
	
			View view = View.inflate(mContext,R.layout.list_item_tel_code,parent); 

- 以上几种方法，不管如何使用，都会调用到 LayoutInflater 中 inflate() 方法。