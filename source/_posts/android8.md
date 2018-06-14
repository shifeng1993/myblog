---
title: 【android学习随笔】android数据存储-持久化
categories: android
date: 2018-06-14
tags:
  - android
---
数据持久化就是指将那些内存中的瞬时数据保存到存储设备中，保证即使在手机或电脑关机 的情况下，这些数据仍然不会丢失。

# 文件存储
原理大概就是用 Context 类中提供的 openFileInput()和 openFileOutput()方法，之后就是利用 Java 的各种流来进行读写操作。

## 布局文件
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <EditText
        android:id="@+id/edit"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Type something here" />
</LinearLayout>
```
## java逻辑部分
```java
public class MainActivity extends AppCompatActivity {
    private EditText edit;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        edit = (EditText) findViewById(R.id.edit);
        String inputText = load();
        if (!TextUtils.isEmpty(inputText)) {
            edit.setText(inputText);
            edit.setSelection(inputText.length());
            Toast.makeText(this, "Restoring succeeded", Toast.LENGTH_SHORT).show();
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        String inputText = edit.getText().toString();
        save(inputText);
    }

    //
    public void save(String inputText) {
        FileOutputStream out = null;
        BufferedWriter writer = null;
        try {
            out = openFileOutput("data", Context.MODE_PRIVATE);
            writer = new BufferedWriter(new OutputStreamWriter(out));
            writer.write(inputText);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (writer != null) {
                    writer.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    public String load() {
        FileInputStream in = null;
        BufferedReader reader = null;
        StringBuilder content = new StringBuilder();
        try {
            in = openFileInput("data");
            reader = new BufferedReader(new InputStreamReader(in));
            String line = "";
            while ((line = reader.readLine()) != null) {
                content.append(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        return content.toString();
    }
}

```

# SharedPreferences 存储
也叫键值对存储。使用xml进行存储对应的键值对，SharedPreferences 存储要比文本存储简单方便许多，应用场景也多了不少，比如很多应用程序中的偏好设置功能其实都使用到了 SharedPreferences 技术，还有记住密码等功能，需要注意正式项目需要对明文密码进行加密。

## 布局文件
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" android:layout_width="match_parent" android:layout_height="match_parent"
    android:orientation="vertical" >
    <Button
        android:id="@+id/save_data"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="保存数据"
        />
    <Button
        android:id="@+id/read_data"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="读取数据"
        />
</LinearLayout>
```

## java逻辑部分
```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 以下是保存数据按钮
        Button saveData = (Button) findViewById(R.id.save_data);
        saveData.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                SharedPreferences.Editor editor = getSharedPreferences("data", MODE_PRIVATE).edit();
                editor.putString("name", "hello");
                editor.putInt("age", 4);
                editor.apply();
                Toast.makeText(MainActivity.this, "保存完成", Toast.LENGTH_SHORT).show();
            }
        });

        // 以下是读取数据按钮
        Button readData = (Button) findViewById(R.id.read_data);
        readData.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                SharedPreferences pref = getSharedPreferences("data", MODE_PRIVATE);
                String name = pref.getString("name", "");
                int age = pref.getInt("age", 0);
                Toast.makeText(MainActivity.this, "name is " + name + " and age is " + age, Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```

# SQLite 数据库存储
android 内置数据库
## 创建数据库

### 创建数据库适配器
重写`onCreate()`和`onUpgrade()`
```java
public class MyDatabaseHelper extends SQLiteOpenHelper {

    public static final String CREATE_BOOK = "create table Book (id integer primary key autoincrement, author text, price real, pages integer, name text)";

    private Context mContext;

    public MyDatabaseHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version) {
        super(context, name, factory, version);
        mContext = context;
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(CREATE_BOOK);
        Toast.makeText(mContext, "Create succeeded", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
    }
}
```
### 用一个按钮触发数据库实例
getReadableDatabase()和 get- WritableDatabase()。这两个方法都可以创建或打开一个现有的数据库(如果数据库已存在则直接打开，否则创建一个新的数据库)，并返回一个可对数据库进行读写操作的对象。不同的是， 当数据库不可写入的时候(如磁盘空间已满)，getReadableDatabase()方法返回的对象将以只读的方式去打开数据库，而 getWritableDatabase()方法则将出现异常。

SQLiteOpenHelper中有两个构造方法可供重写，一般使用参数少一点的那个构造方法即可。

构造函数第一个参数是 Context。第二个参数是数据库名。第三个参 数允许我们在查询数据的时候返回一个自定义的 Cursor，一般都是传入 null。第四个参数表示当前数据库的版本号，可用于对数据库进行升级操作。

```java
public class MainActivity extends AppCompatActivity {

    private MyDatabaseHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
        Button createDatabase = (Button) findViewById(R.id.create_database);
        createDatabase.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                dbHelper.getWritableDatabase();
            }
        });
    }
}
```

## sqlite常用操作
### 进入设备控制台
```
$ adb shell
```
### 找到对应包的数据库
```
# cd xxxxx 
# ls
```
### 打开数据库
```
# sqlite3 xxx.db
```
### 操作数据库
- .table 查看表
- .schema 查看建表语句
- .exit/.quit 退出
### 退出设备控制台
```
exit
```