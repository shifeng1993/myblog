---
title: 【android学习随笔】android数据持久化(一)-文件存储和xml存储
categories: android
date: 2018-05-31
tags:
  - android
---
这两种适合存储一些简单的数据。非关系，量小。例如记住密码，本地设置等。

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
## 文件流读写部分
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

## SharedPreferences操作部分
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
