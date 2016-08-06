---
layout:     post
title:      "Android 序列化"
subtitle:   "Parcelable Serializable"
date:       2016-08-05 
author:     "Jerome"
header-img: "img/post-bg-02.jpg"
---


## Android 序列化

在 Android 中 Serializable 接口和 Parcelable 接口都可以完成对象的序列化过程。Serializable 是 Java 的序列化接口，主要用于网络传输和存储到设备上。Parcelable 是 Android 的序列化接口，主要用于 Android 在内存序列化上。


### Serializable

- Serializable 是一个空接口，是由 Java 提供的一个序列化接口，可以为对象提供标准的序列化和反序列化操作。使用该接口非常简单，只需要自己的类实现该接口就可以了，没有什么方法需要实现的。一般使用该接口的时候，最好声明一个 serialVersionUID ,手动指定的话一般为 1L 。以上就可以完成一个对象的序列化和反序列化操作。

- serialVersionUID 用来辅助序列化和反序列化过程的，原则上序列化后的数据中的 serialVersionUID 只有和当前类的 serialVersionUID 相同才能够正常地被反序列化。
- 通过 Serializable 方式来实现对象的序列化，使用 ObjectOutputStream 和 ObjectInputStream。
	
- 代码：
	- User.java

			public class User implements Serializable {
			    private static final long serialVersionUID = 1L;
			
			    private int age;
			    private String name;
			    private boolean isMale;
			
			    public User(int age, String name, boolean isMale) {
			        this.age = age;
			        this.name = name;
			        this.isMale = isMale;
			    }
			
			    public int getAge() {
			        return age;
			    }
			
			    public void setAge(int age) {
			        this.age = age;
			    }
			
			    public String getName() {
			        return name;
			    }
			
			    public void setName(String name) {
			        this.name = name;
			    }
			
			    public boolean isMale() {
			        return isMale;
			    }
			
			    public void setMale(boolean male) {
			        isMale = male;
			    }
			}

	- MainActivity.java

			public class MainActivity extends AppCompatActivity {
			    Button button, button1;
			    private File file;
			
			    @Override
			    protected void onCreate(Bundle savedInstanceState) {
			        super.onCreate(savedInstanceState);
			        setContentView(R.layout.activity_main);
			
			        button = (Button) findViewById(R.id.button);
			        button1 = (Button) findViewById(R.id.button1);
			
			        file = new File(Environment.getExternalStorageDirectory(), "jerome111");
			        if (!file.exists()) {
			            try {
			                file.createNewFile();
			            } catch (IOException e) {
			                e.printStackTrace();
			            }
			        }
			        button.setOnClickListener(new View.OnClickListener() {
			            @Override
			            public void onClick(View v) {
			                User user = new User(20, "HEi", false);
			                try {
			                    FileOutputStream outputStream1 = new FileOutputStream(file);
			                    ObjectOutputStream outputStream = new ObjectOutputStream(outputStream1);
			                    outputStream.writeObject(user);
			                    outputStream.close();
			                    Toast.makeText(MainActivity.this, "序列化完成", Toast.LENGTH_SHORT).show();
			                } catch (IOException e) {
			                    e.printStackTrace();
			                }
			            }
			        });
			
			        button1.setOnClickListener(new View.OnClickListener() {
			            @Override
			            public void onClick(View v) {
			                try {
			                    ObjectInputStream inputStream = new ObjectInputStream(new FileInputStream
			                            (file));
			                    User user = (User) inputStream.readObject();
			                    System.out.println("反序列化后的 user：" + user.getName() + ", " + user.getAge() + ", " + user.isMale());
			                } catch (IOException e) {
			                    e.printStackTrace();
			                } catch (ClassNotFoundException e) {
			                    e.printStackTrace();
			                }
			            }
			        });
			    }
			}

### Parcelable

- Parcelable 接口是由 Android 提供的序列化接口，自己的类实现该接口，可以实现对象的序列化和反序列化操作，并可以通过 Intent 和 Binder 传递。
- 实现 Parcelable 接口，比实现 Serializable 接口要复杂一些。需要实现的方法
	- describeContents()：返回当前对象的内容描述，如果含有文件描述符，返回 1 否则返回 0 几乎所有情况都返回 0 。
	- writeToParcel(Parcel dest, int flags)：将当前对象写入序列化结构中，其中 flags 标识有两种值，0 或者 1 。为 1 时标识当前对象需要作为返回值返回，不能立即释放资源，几乎所有情况都为 0 。
	- Student(Parcel in)：从序列化后的对象中创建原始对象
	- createFromParcel(Parcel in)：从序列化后的对象中创建原始对象
	- newArray(int size)：创建指定长度的原始对象数组

- 代码
	- Student.java

			public class Student implements Parcelable {
			    private static final String TAG = "Student";
			
			    private int age;
			    private String name;
			    private boolean isMale;
			
			    public Student(int age,String name,boolean isMale){
			        this.age = age;
			        this.name = name;
			        this.isMale = isMale;
			    }
			
			    public int getAge() {
			        return age;
			    }
			
			    public void setAge(int age) {
			        this.age = age;
			    }
			
			    public String getName() {
			        return name;
			    }
			
			    public void setName(String name) {
			        this.name = name;
			    }
			
			    public boolean isMale() {
			        return isMale;
			    }
			
			    public void setMale(boolean male) {
			        isMale = male;
			    }
			
			    @Override
			    public int describeContents() {
			        return 0;
			    }
			
			    @Override
			    public void writeToParcel(Parcel dest, int flags) {
			        dest.writeInt(this.age);
			        dest.writeString(this.name);
			        dest.writeByte(this.isMale ? (byte) 1 : (byte) 0);
			    }
			
			
			
			    protected Student(Parcel in) {
			        this.age = in.readInt();
			        this.name = in.readString();
			        this.isMale = in.readByte() != 0;
			    }
			
			    public static final Parcelable.Creator<Student> CREATOR = new Parcelable.Creator<Student>() {
			        @Override
			        public Student createFromParcel(Parcel source) {
			            return new Student(source);
			        }
			
			        @Override
			        public Student[] newArray(int size) {
			            return new Student[size];
			        }
			    };
			}

	- Student 在 Intent 中传递

			public class MainActivity extends AppCompatActivity {
			
			    @Override
			    protected void onCreate(Bundle savedInstanceState) {
			        super.onCreate(savedInstanceState);
			        setContentView(R.layout.activity_main);
			
			        button2 = (Button) findViewById(R.id.button2);
			        final Student student = new Student(28, "jerome", true);
			        button2.setOnClickListener(new View.OnClickListener() {
			            @Override
			            public void onClick(View v) {
			                Intent intent = new Intent(MainActivity.this, ParcelActivity.class);
			                intent.putExtra("student", student);
			                startActivity(intent);
			            }
			        });
			    }
			}

			----------------------------
			public class ParcelActivity extends AppCompatActivity {
			
			    @Override
			    protected void onCreate(Bundle savedInstanceState) {
			        super.onCreate(savedInstanceState);
			        setContentView(R.layout.activity_parcel);
			       TextView testview  = (TextView) findViewById(R.id.testview);
			        Student student = getIntent().getParcelableExtra("student");
			        testview.setText(student.getName()+", "+student.getAge()+", "+student.isMale());
			    }
			}

	- 结果


		 ![](http://i.imgur.com/2lN50SQ.png)

### 两种序列化方式用途

- Parcelable : 主要就是 Android 上使用了，针对移动设备的特点进行了优化。主要用在 Android 中的 Intent 和 Binder 中。实现比较的麻烦点。在 Android 上性能要好一些。
- Serializable : Java 提供的，主要用在存储设备或者网络传输中，也可以用在 Intent 中。实现比较的简单。在 Android 上比 Parcelable 接口性能差一些。