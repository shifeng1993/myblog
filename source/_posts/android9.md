---
title: 【android学习随笔】android数据持久化(二)-sqlite
categories: android
date: 2018-06-15
tags:
  - android
---
sqlite适用于大量复杂的关系型数据的存储。

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
## 升级数据库
在原有sqlite上新增表。
### 更改MyDatabaseHelper类
- 添加sql语句，并在onCreate()内引入，
- 添加onUpgrade()内DROP语句，如果发现数据库中已经存在 Book 表或 Category 表了，就将这两张表删除掉，然后再调用 onCreate()方法重新创建
```java
public class MyDatabaseHelper extends SQLiteOpenHelper {

    ...
    public static final String CREATE_CATEGORY = "create table Category (id integer primary key autoincrement, category_name text, category_code integer)";
    ...
    @Override
    public void onCreate(SQLiteDatabase db) {
        ...
        db.execSQL(CREATE_CATEGORY);
        ...
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("drop table if exists Book");
        db.execSQL("drop table if exists Category");
        onCreate(db);
    }
}
```
### 执行onUpgrade()
修改数据库实例版本，必须大于当前版本。之后就会执行数据库的onUpgrade()方法
```java
public class MainActivity extends AppCompatActivity {

    private MyDatabaseHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
-       dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
+       dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 2);
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

## 添加数据

### 添加触发按钮
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    ...
    <Button
        android:id="@+id/add_data"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Add data" />
</LinearLayout>
```
### 监听按钮重写点击事件
实例化一个ContentValues对象，然后使用put方法给对象存储键值对，最后插入，，插入完成清空ContentValues对象。

insert()，接受三个参数，第一个参数是表名，第二个参数用于在未指定添加数据的情 况下给某些可为空的列自动赋值 NULL，一般我们用不到这个功能，直接传入 null 即可。第三个 参数是一个 ContentValues 对象。
```java
public class MainActivity extends AppCompatActivity {

    private MyDatabaseHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
        ...
        // 新增
        Button addData = (Button) findViewById(R.id.add_data);
        addData.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                SQLiteDatabase db = dbHelper.getWritableDatabase();
                ContentValues values = new ContentValues();
                // 开始组装第一条数据
                values.put("name", "The Da Vinci Code");
                values.put("author", "Dan Brown");
                values.put("pages", 454);
                values.put("price", 16.96);
                db.insert("Book", null, values); // 插入第一条数据
                values.clear();
                // 开始组装第二条数据
                values.put("name", "The Lost Symbol");
                values.put("author", "Dan Brown");
                values.put("pages", 510);
                values.put("price", 19.95);
                db.insert("Book", null, values); // 插入第二条数据
            }
        });
        ...
    }
}
```

## 查询数据
有写入，必然有读取。所以查询是必要的，
### 添加触发按钮
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    ...
    <Button
        android:id="@+id/query_data"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Query data" />
</LinearLayout>
```

### 监听按钮重写点击事件
调用query()方法会返回一个Cursor对象，查询到的数据都在这个对象内。

query()，接受七个参数，第一个参数是表名，第二个参数用于指定去查询哪几列，如果不指定则默认查询所有列。第三、第四个参数用于约束查 询某一行或某几行的数据，不指定则默认查询所有行的数据。第五个参数用于指定需要去 group by 的列，不指定则表示不对查询结果进行 group by 操作。第六个参数用于对 group by 之后的数据进 行进一步的过滤，不指定则表示不进行过滤。第七个参数用于指定查询结果的排序方式，不指定 则表示使用默认的排序方式。

| query()方法参数  |对应SQL部分                   | 描 述                       |
| -------------   | -------------------------- | --------------------------- |
| table           | from table_name            |  指定查询的表名               |
| columns         | select column1, column2    |  指定查询的列名               |
| selection       | where column = value       |  指定where的约束条件          |
| selectionArgs   | -                          |  为where中的占位符提供具体的值  |
| groupBy         | group by column            |  指定需要group by的列         |
| having          | having column = value      |  对group by后的结果进一步约束  |
| orderBy         | order by column1, column2  |  指定查询结果的排序方式         |

```java
public class MainActivity extends AppCompatActivity {

    private MyDatabaseHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
        ...
        // 查询
        Button queryData = (Button) findViewById(R.id.query_data);
        queryData.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                SQLiteDatabase db = dbHelper.getWritableDatabase();
                // 查询 Book 表中所有的数据
                Cursor cursor = db.query("Book", null, null, null, null, null, null);
                if (cursor.moveToFirst()) {
                    do {
                        // 遍历 Cursor 对象，取出数据并打印
                        String name = cursor.getString(cursor.getColumnIndex
                                ("name"));
                        String author = cursor.getString(cursor.getColumnIndex
                                ("author"));
                        int pages = cursor.getInt(cursor.getColumnIndex("pages"));
                        double price = cursor.getDouble(cursor.getColumnIndex
                                ("price"));
                        Log.d("MainActivity", "book name is " + name);
                        Log.d("MainActivity", "book author is " + author);
                        Log.d("MainActivity", "book pages is " + pages);
                        Log.d("MainActivity", "book price is " + price);
                    } while (cursor.moveToNext());
                }
                cursor.close();
            }
        });
    }
}
```

## 修改数据

### 添加触发按钮
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    ...
    <Button
        android:id="@+id/update_data"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Update data" />
</LinearLayout>
```

### 监听按钮重写点击事件
update同样是使用ContentValues来保存值
然后使用update()来更新，第一个参数是表名，第二个参数是需要修改的ContentValues对象，第三个参数是对应sql语句的where部分，可以使用占位符
```java
public class MainActivity extends AppCompatActivity {

    private MyDatabaseHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
        ...
        // 修改
        Button updateData = (Button) findViewById(R.id.update_data);
        updateData.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                SQLiteDatabase db = dbHelper.getWritableDatabase();
                ContentValues values = new ContentValues();
                values.put("price", 10.99);
                String[] params = {"The Da Vinci Code"};   // 字符串数组
                db.update("Book", values, "name = ?", params);
            }
        });
    }
}
```

## 删除数据
### 添加触发按钮
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    ...
    <Button
        android:id="@+id/update_data"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Update data" />
</LinearLayout>
```

### 监听按钮重写点击事件
delete()方法接受三个参数，第一个参数是表名，第二、第三个参数又是用于约束删除某一行 或某几行的数据，不指定的话默认就是删除所有行。
```java
public class MainActivity extends AppCompatActivity {

    private MyDatabaseHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
        ...
        // 删除
        Button deleteData = (Button) findViewById(R.id.delete_data);
        deleteData.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                SQLiteDatabase db = dbHelper.getWritableDatabase();
                String[] params = {"500"};
                db.delete("Book", "pages > ?", params);
            }
        });
    }
}
```

## 使用sql语句操作数据库
读取操作用`rawQuery()`， 有写入操作时用`execSQL()`
### 增
```java
db.execSQL("insert into Book (name, author, pages, price) values(?, ?, ?, ?)", new String[]{"The Da Vinci Code", "Dan Brown", "454", "16.96" });
```
### 查
```java
db.rawQuery("select * from Book", null);
```
### 改
```java
db.execSQL("update Book set price = ? where name = ?", new String[] { "10.99", "The Da Vinci Code" });
```
### 删
```java
db.execSQL("delete from Book where pages > ?", new String[] { "500" });
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