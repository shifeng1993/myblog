---
title: 【android学习随笔】android列表组件RecyclerView
categories: android
date: 2018-05-07
tags:
  - android
---
## 添加依赖
`app/build.gradle`
```java
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:24.2.1'
    compile 'com.android.support:recyclerview-v7:24.2.1'
    testCompile 'junit:junit:4.12'
}
```
然后点击sync now

## 添加xml布局
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" android:layout_width="match_parent" android:layout_height="match_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycler_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
``` 