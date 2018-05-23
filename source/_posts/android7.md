---
title: 【android学习随笔】android广播机制
categories: android
date: 2018-05-16
tags:
  - android
---
# 广播类型简介

## 标准广播
异步执行，无序，效率高，但是无法截断

## 有序广播
同步执行，有序，效率低，优先级高会先收到消息，并且可以截断正在传递的广播

# 接受系统广播
例如：电池低电量，手机开启，时间时区变化，网络变化

## 动态注册监听网络变化
优点：可以灵活使用，自由的在周期内控制监听的注册和注销
缺点：只能在程序的周期内使用
```java
public class MainActivity extends AppCompatActivity {

    private IntentFilter intentFilter;

    private NetworkChangeReceiver networkChangeReceiver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        intentFilter = new IntentFilter();                              // 创建IntentFilter实例
        intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE"); // 添加 android.net.conn.CONNECTIVITY_CHANGE 监听action为网络发生变化的广播
        networkChangeReceiver = new NetworkChangeReceiver();            // 创建 NetworkChangeReceiver 实例
        registerReceiver(networkChangeReceiver, intentFilter);          // 进行注册，完成监听功能
    }

    // 在活动销毁周期，注销掉已注册的监听，动态注册的必做此步骤
    @Override
    protected void onDestroy() {
        super.onDestroy();
        unregisterReceiver(networkChangeReceiver);
    }

    // 定义内部类NetworkChangeReceiver 继承自 BroadcastReceiver 广播，每当有广播来就触发onReceive执行方法内语句
    class NetworkChangeReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            // 获取系统服务类的实例 getSystemService
            ConnectivityManager connectionManager = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
            // 获取 NetworkInfo 实例
            NetworkInfo networkInfo = connectionManager.getActiveNetworkInfo();
            // 用NetworkInfo实例下的方法isAvailable()是否开启进行判断输出
            if (networkInfo != null && networkInfo.isAvailable()) {
                Toast.makeText(context, "network is available",
                        Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(context, "network is unavailable", Toast.LENGTH_SHORT).show();
            }
        }
    }
}
```

## 静态注册实现开机启动
优点：可以在程序外启动
缺点：不灵活
注：8.0以上静态注册收到非常多的限制，除开机启动之外，尽量使用动态注册
### 创建广播接收器
可以使用 Android Studio 提供的快捷方式来创建一个广播接收器，右击 com.example.broadcasttest 包→New→Other→Broadcast Receiver，Exported 属性表示是否 允许这个广播接收器接收本程序以外的广播，Enabled 属性表示是否启用这个广播接收器。
```java
public class BootCompleteReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "Boot Complete", Toast.LENGTH_LONG).show();
    }
}
```
### 在AndroidManifest.xml添加权限和动作
添加权限 `<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />`
添加自动添加的receiver内添加 intentFilter 动作
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.shifeng.broadcasttest">

    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <receiver
            android:name=".BootCompleteReceiver"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
            </intent-filter>
        </receiver>
    </application>

</manifest>
```

# 接受自定义广播
## 发送标准广播
### 定义广播接收器
```java

```
## 发送有序广播