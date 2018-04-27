---
title: 【android学习随笔】安卓常用事件写法
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