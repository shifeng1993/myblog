---
title: 【android学习随笔】安卓常用事件和控件
categories: android
date: 2018-04-27
tags:
  - android
---
# 点击事件 onClick()
第一种写法 匿名类方式
```java
public class MainActivity extends AppCompatActivity {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState); 
    setContentView(R.layout.activity_main);
    Button button = (Button) findViewById(R.id.button); 
    button.setOnClickListener(new View.OnClickListener() {
      @Override
      public void onClick(View v) {
        // 在此处添加逻辑 
      }
    });
  } 
}
```
第二种写法 接口写法
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState); 
    setContentView(R.layout.activity_main);
    Button button = (Button) findViewById(R.id.button); 
    button.setOnClickListener(this);
  }
  @Override
  public void onClick(View v) {
    switch (v.getId()) {
      case R.id.button:
        // 在此处添加逻辑
        break;
      default:
        break; 
    }
  }
}
```
# 获取输入内容
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {
  private EditText editText;
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState); setContentView(R.layout.activity_main);
    Button button = (Button) findViewById(R.id.button); 
    editText = (EditText) findViewById(R.id.edit_text); 
    button.setOnClickListener(this);
  }
  @Override
  public void onClick(View v) {
    switch (v.getId()) {
      case R.id.button:
        String inputText = editText.getText().toString(); 
        Toast.makeText(MainActivity.this, inputText, 11 Toast.LENGTH_SHORT).show();
        break;
      default:
        break;
    } 
  }
}
```

# 控制圆形进度条可见和不可见
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener { private EditText editText;
  private ImageView imageView;
  private ProgressBar progressBar;
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Button button = (Button) findViewById(R.id.button);
    editText = (EditText) findViewById(R.id.edit_text);
    imageView = (ImageView) findViewById(R.id.image_view);
    progressBar = (ProgressBar) findViewById(R.id.progress_bar);
    button.setOnClickListener(this);
  }
  @Override
  public void onClick(View v) {
    switch (v.getId()) {
      case R.id.button:
        if (progressBar.getVisibility() == View.GONE) {
          progressBar.setVisibility(View.VISIBLE);
        } else {
          progressBar.setVisibility(View.GONE);
        }
        break;
      default:
        break;
    }
  } 
}
```

# 控制水平进度条进度
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener { private EditText editText;
  private ImageView imageView;
  private ProgressBar progressBar;
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Button button = (Button) findViewById(R.id.button);
    editText = (EditText) findViewById(R.id.edit_text);
    imageView = (ImageView) findViewById(R.id.image_view);
    progressBar = (ProgressBar) findViewById(R.id.progress_bar);
    button.setOnClickListener(this);
  }
  @Override
  public void onClick(View v) {
    switch (v.getId()) {
      case R.id.button:
        int progress = progressBar.getProgress(); 
        progress = progress + 10; 
        progressBar.setProgress(progress);
        break;
      default:
        break;
    }
  } 
}
```

# 显示AlertDialog

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    ...

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.button:
                AlertDialog.Builder dialog = new AlertDialog.Builder(MainActivity.this);
                dialog.setTitle("This is Dialog");
                dialog.setMessage("Something important.");
                dialog.setCancelable(false);
                dialog.setPositiveButton("OK", new DialogInterface.
                        OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                    }
                });
                dialog.setNegativeButton("Cancel", new DialogInterface.
                        OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                    }
                });
                dialog.show();
                break;
            default:
                break;
        }
    }
}
```

# 显示ProgressDialog

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    ...

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.button:
                ProgressDialog progressDialog = new ProgressDialog(MainActivity.this);
                progressDialog.setTitle("This is ProgressDialog"); 
                progressDialog.setMessage("Loading..."); 
                progressDialog.setCancelable(true);
                progressDialog.show();
                break;
            default:
                break;
        }
    }
}
```

## Toast
- 第一个实参是Context，也就是 Toast 要求的上下文，由于活动本身就是一个 Context 对象，因此这里直 接传入 FirstActivity.this 即可。
- 第二个参数是 Toast 显示的文本内容。
- 第三个参数是 Toast 显示的时长，有两个内置常量可以选择 Toast.LENGTH_SHORT 和 Toast.LENGTH_LONG。
```java
@Override
public void onClick(View v) {
             Toast.makeText(FirstActivity.this, "You clicked Button 1",
                Toast.LENGTH_SHORT).show();
}
```

## menu
在res内创建 Menu resource file 创建各item，用title给名称
```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/add_item"
        android:title="Add" />
    <item
        android:id="@+id/remove_item"
        android:title="Remove" />
</menu>
```
然后在activity用onCreateOptionsMenu()方法重写，getMenuInflater()方法能够得到 MenuInflater 对象，再调用它的 inflate()，
inflate()方法接收两个参数，第一个参数用于指定我们通过哪 一个资源文件来创建菜单，这里当然传入 R.menu.main。第二个参数用于指定我们的菜单项将 添加到哪一个 Menu 对象当中，这里直接使用 onCreateOptionsMenu()方法中传入的 menu 参数。 然后给这个方法返回 true，表示允许创建的菜单显示出来，如果返回了 false，创建的菜单将 无法显示。
```java
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main, menu);
return true;
}
```
在重写选择菜单项的点击方法
```java
@Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()){
            case R.id.add_item:
                Toast.makeText(this, "your click add", Toast.LENGTH_SHORT).show();
                break;
            case R.id.remove_item:
                Toast.makeText(this, "your click remove", Toast.LENGTH_SHORT).show();
                break;
            default:
                break;
        }
        return true;
    }
```