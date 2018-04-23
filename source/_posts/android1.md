---
title: 【android学习随笔】activity 活动
categories: android
date: 2018-04-18
tags:
  - android
---

android的四大组件之一，它是一种可以包含用户界面的组件，主要用于 和用户进行交互。

## 基本用法

### 创建

- 在javac包下，创建一个activity
- 在res/layout下创建布局文件。
- 在AndroidManifest.xml内注册active活动

### 销毁
- finish()

## Intent跳转
### 跳转方式
- 显式
```java
 @Override
  public void onClick(View v) {
     Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
     startActivity(intent);
  }
```

- 隐式
```java
 @Override
  public void onClick(View v) {
      Intent intent = new Intent("com.example.shifeng.helloworld.ACTION_START");
      intent.addCategory("com.example.shifeng.helloworld.MY_CATEGORY");
      startActivity(intent);
  }
```
隐式需要在AndroidManifest.xml对应的activity内添加action和category
```xml
<activity android:name=".SecondActivity">
  <intent-filter>
      <action android:name="com.example.shifeng.helloworld.ACTION_START" />
      <category android:name="android.intent.category.DEFAULT" />
      <category android:name="com.example.shifeng.helloworld.MY_CATEGORY"/>
  </intent-filter>
</activity>
```

### 跳转传值
使用intent对象下的putExtra()方法，第一个参数是键，用于后面从 Intent 对象中取值，第二个参数才是真正要传递的数据。

- 传递
```java 
@Override
public void onClick(View v) {
  String data = "Hello SecondActivity";
  Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
  intent.putExtra("extra_data", data);
  startActivity(intent);
}
```
- 取出
```java 
@Override
protected void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState); 
  setContentView(R.layout.second_layout);
  Intent intent = getIntent();
  String data = intent.getStringExtra("extra_data"); 
  Log.d("SecondActivity", data);
}
```
取出有很多方法
  - getStringExtra
  - getIntExtra
  - getBooleanExtra
等等，是根据类型来取值，切记要类型对


### 跳转到别的应用
AndroidManifest.xml
```xml
<activity android:name=".ThirdActivity">
  <intent-filter>
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.DEFAULT" />
      <data android:scheme="http" />
  </intent-filter>
</activity>
```
- 打开浏览器
```java
@Override
public void onClick(View v) {
  Intent intent = new Intent(Intent.ACTION_VIEW);
  intent.setData(Uri.parse("https://www.baidu.com"));
  startActivity(intent);
}
```
- 打电话
```java
@Override
public void onClick(View v) {
  Intent intent = new Intent(Intent.ACTION_VIEW);
  intent.setData(Uri.parse("tel:10086"));
  startActivity(intent);
}
```

## activity的生命周期
<img src="./active_period.png" title="activity的生命周期"/>

### 完整生存期
- onCreate() 活动第一次被创建，常用作加载布局、绑定事件等。
- onDestroy() 活动被销毁之前
- onRestart() 活动由停止状态变为运行状态之前
### 可见生存期
- onStart() 活动由不可见变为可见
- onStop() 活动完全不可见，如果启动的新活动是一个对话框式的活动，则不会执行
### 前台生存期
- onResume() 活动准备好和用户进行交互
- onPause() 系统准备去启动或者恢复另一个活动，如果启动的新活动是一个对话框式的活动，则会执行
