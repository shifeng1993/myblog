---
title: 【android学习随笔】安卓常用UI控件
categories: android
date: 2018-04-27
tags:
  - android
---
# TextView
```xml
<TextView
            android:id="@+id/text_view"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textSize="32sp"
            android:textColor="#00ff00"
            android:text="This is TextView" />
```

# Button
```xml
<Button
            android:id="@+id/button"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Button"
            android:textAllCaps="false" />
```
# EditText
```xml
<EditText
            android:id="@+id/edit_text"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Type something here"
            android:maxLines="2"
            />
```
# ImageView
```xml
<ImageView
            android:id="@+id/image_view"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@drawable/img_1 "
            />
```
# ProgressBar
```xml
<ProgressBar
            android:id="@+id/progress_bar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            style="?android:attr/progressBarStyleHorizontal"
            android:max="100"
            />
```
# 控件属性：
## android:id="@+id/XXXX"
- 控件唯一标识符

## android:layout_width="XXX" && android:layout_height="XXX"
- 控件宽高
- 父容器决定尺寸：match_parent（推荐） && fill_parent
- 内容决定尺寸：wrap_content

## android:gravity="XXX"
- 控件对齐方式
- top、bottom、left、right、 center
- 可以用“|”来指定多个值

## android:textSize="XXXsp"
- 控件字体大小
- android使用sp做大小单位

## android:textColor="#00ff00"
- 控件字体颜色

## android:textAllCaps="false"
- button属性
- 小写到大写转换（button默认开启）

## android:text="helo"
- 文本内容

## android:hint="Type something here"
- editText属性
- 类似web的placeholder

## android:maxLines="2"
- edittext属性
- 最大行数

## android:src="@drawable/img_1"
- imageView属性
- 图片路径

## style="?android:attr/progressBarStyleHorizontal"
- 进度条样式，有圆形进度条和水平进度条两种

## android:max="100"
- 进度条最大值