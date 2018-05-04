---
title: 【android学习随笔】安卓常用布局以及自定义控件
categories: android
date: 2018-05-04
tags:
  - android
---
今天五四青年节，五四青年节为啥不放假，性质不是和三八妇女节一样吗。。。

# 四种基本布局
## 线性布局（LinearLayout）
从左到右或者从上到下，按顺序排列。
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" 
  android:orientation="vertical"
  android:layout_width="match_parent"
  android:layout_height="match_parent">
  <Button
    android:id="@+id/button1"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Button 1" />
  <Button
    android:id="@+id/button2"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Button 2" />
   <Button
    android:id="@+id/button3"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Button 3" />
</LinearLayout>
```

### android:orientation="horizontal"
- horizontal 水平（默认）
- vertical 垂直
### android:layout_gravity="top"
- 控件在布局中的对齐方式
### android:layout_weight
- 允许使用flex布局方式，值可以为比例，例如flex=1 在layout_weight里面为1
- 设定layout_weight比例后，水平布局中的宽度就会失去作用，垂直布局中的的高度就会失去作用，一般用0dp书写以规范化


## 相对布局（RelativeLayout）
可以通过相对定位的方式让控件出现在布局的任何位置
```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="match_parent"
  android:layout_height="match_parent">
    <Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentLeft="true"
        android:layout_alignParentTop="true"
        android:text="Button 1" />
    <Button
        android:id="@+id/button2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true"
        android:layout_alignParentTop="true"
        android:text="Button 2" />
    <Button
        android:id="@+id/button3"
        android:layout_width="wrap_content"
         android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:text="Button 3" />
    <Button
        android:id="@+id/button4"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_alignParentLeft="true"
        android:text="Button 4" />
    <Button
        android:id="@+id/button5"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"
        android:text="Button 5" />
</RelativeLayout>
```
### 相对父容器边缘对齐（boolen）
- android:layout_alignParentLeft
- android:layout_alignParentRight
- android:layout_alignParentTop
- android:layout_alignParentBottom
- android:layout_centerInParent
### 相对于控件的对齐（@id/xxxxx）
- android:layout_above
- android:layout_below
- android:layout_toLeftOf
- android:layout_toRightOf
### 相对于控件边缘的对齐（）
- android:layout_alignLeft
- android:layout_alignRight
- android:layout_alignTop
- android:layout_alignBottom

## 帧布局（FrameLayout）
没有方便的定位方式，所有的控件都会默认摆放在布局的左上角。
```xml
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android" android:layout_width="match_parent" android:layout_height="match_parent">
    <TextView
        android:id="@+id/text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="This is TextView"
        />
    <ImageView
        android:id="@+id/image_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher"
        />
</FrameLayout>
```
### 对齐
- layout_gravity
## 百分比布局(PercentFrameLayout)
### 使用百分比布局
打开 app/build.gradle 文件，在 dependencies 闭包中添加如下内容:
```java
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:24.2.1'
    compile 'com.android.support:percent:24.2.1'
    testCompile 'junit:junit:4.12'
}
```
### 百分比帧布局
```xml
<android.support.percent.PercentFrameLayout 
    xmlns:android="http://schemas.android.com/apk/res/android" 
    xmlns:app="http://schemas.android.com/apk/res-auto" 
    android:layout_width="match_parent" 
    android:layout_height="match_parent">
    <Button
        android:id="@+id/button1"
        android:text="Button 1"
        android:layout_alignParentLeft="true"
        android:layout_alignParentTop="true"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="50%"
        />
    <Button
        android:id="@+id/button2"
        android:text="Button 2"
        android:layout_alignParentRight="true"
        android:layout_alignParentTop="true"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="50%"
        />
    <Button
        android:id="@+id/button3"
        android:text="Button 3"
        android:layout_alignParentLeft="true"
        android:layout_alignParentBottom="true"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="50%"
        />
    <Button
        android:id="@+id/button4"
        android:text="Button 4"
        android:layout_alignParentRight="true"
        android:layout_alignParentBottom="true"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="50%"
        />
</android.support.percent.PercentFrameLayout>
```

### 百分比相对布局
```xml
<android.support.percent.PercentRelativeLayout 
    xmlns:android="http://schemas.android.com/apk/res/android" 
    xmlns:app="http://schemas.android.com/apk/res-auto" 
    android:layout_width="match_parent" 
    android:layout_height="match_parent">
    <Button
        android:id="@+id/button1"
        android:text="Button 1"
        android:layout_gravity="left|top"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="50%"
        />
    <Button
        android:id="@+id/button2"
        android:text="Button 2"
        android:layout_gravity="right|top"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="50%"
        />
    <Button
        android:id="@+id/button3"
        android:text="Button 3"
        android:layout_gravity="left|bottom"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="50%"
        />
    <Button
        android:id="@+id/button4"
        android:text="Button 4"
        android:layout_gravity="right|bottom"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="50%"
        />
</android.support.percent.PercentRelativeLayout>
```
### 百分比布局属性（相对于父容器）
- app:layout_widthPercent
- app:layout_heightPercent
- layout_gravity 定位

## 绝对定位布局（AbsoluteLayout）
## 表格布局（TableLayout）

# 自定义控件
<img src="./android布局控件继承结构.png" title="android布局控件继承结构"/>
如上图我们所用的所有控件都是直接或间接继承自 View 的，所用的所有布局都是直接 或间接继承自 ViewGroup 的。有时候控件满足不了需求。就会用自定义控件。
## 先新建一个组件比如导航栏， 命名为navigation
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" 
    android:layout_height="wrap_content" 
    android:background="@drawable/title_bg">
    <Button
        android:id="@+id/title_back"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_margin="5dp"
        android:background="@drawable/back_bg"
        android:text="Back"
        android:textColor="#fff" />
    <TextView
        android:id="@+id/title_text"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_weight="1"
        android:gravity="center"
        android:text="Title Text"
        android:textColor="#fff"
        android:textSize="24sp" />
    <Button
        android:id="@+id/title_edit"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_margin="5dp"
        android:background="@drawable/edit_bg"
        android:text="Edit"
        android:textColor="#fff" />
</LinearLayout>
```
## 在使用到导航栏的xml中引入
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <include layout="@layout/navigation" />
</LinearLayout>
```
## 如果是导航栏还需要去掉活动本身自带的导航栏
```java
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState); 
        setContentView(R.layout.activity_main); 
        ActionBar actionbar = getSupportActionBar(); 
        if (actionbar != null) {
            actionbar.hide();
        }
    }
}
```