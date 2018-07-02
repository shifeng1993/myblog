---
title: 【flutter】用于React Native开发者的flutter参考
categories: flutter
date: 2018-07-02
tags:
  - flutter
---
本文为机翻加人工矫正，原文链接：https://flutter.io/flutter-for-react-native/

本文档适用于React Native（RN）开发人员，他们希望利用现有的RN知识，使用Flutter构建移动应用程序。如果您了解RN框架的基础知识，那么您可以使用此文档作为开始学习Flutter开发的方法。

您可以利用此文档来查找与您的需求最相关的问题。

---------------------------------
# 针对JavaScript开发人员的Dart简介
像React Native一样，Flutter使用响应的view。但是，不同点是RN需要编译为native代码。而Flutter则控制屏幕上的每个像素进行渲染，从而避免因需要JavaScript桥而导致的性能问题。

Dart是一种易于学习的语言，并提供以下功能：

- 提供用于构建Web，服务器和移动应用程序的开源，可伸缩编程语言。
- 提供面向对象的单继承语言，该语言使用AOT编译为native的C风格语法。
- 可选地编译为JavaScript。
- 支持接口和抽象类。

下面介绍JavaScript和Dart之间差异的几个示例。

## 入口函数
JavaScript是没有入口函数的 - 可以随意定义入口。
```javascript
// JavaScript
function startHere() {
  // Can be used as entry point
}
```
在Dart中，每个应用都必须有一个最顶层的`main()`函数，作为应用的入口。
```dart
// Dart
main() {
}
```
在[DartPad](https://dartpad.dartlang.org/0df636e00f348bdec2bc1c8ebc7daeb1)中尝试一下。

## 打印到控制台
要在Dart中打印到控制台，请使用`print`。
```javascript
// JavaScript
console.log("Hello world!");
```
```dart
// Dart
print('Hello world!');
```
在[DartPad](https://dartpad.dartlang.org/cf9e652f77636224d3e37d96dcf238e5)中尝试一下。

## 变量
Dart是类型安全的, 它使用静态类型检查和运行时检查的组合来确保变量的值始终与变量的静态类型相匹配。尽管类型是强制性的，但某些类型注释是可选的，因为Dart执行类型推断。

### 创建和分配变量
在JavaScript中，变量没有类型。
```javaScript
// JavaScript
var name = "JavaScript";
```

在Dart中，变量必须是明确类型的，或者类型系统必须自动推断正确的类型。
```dart
// Dart
String name = 'dart'; // Explicitly typed as a string.
var otherName = 'Dart'; // Inferred string.
// Both are acceptable in Dart.
```

在[DartPad](https://dartpad.dartlang.org/3f4625c16e05eec396d6046883739612)中尝试一下。

有关更多信息，请参阅[Dart的类型系统](https://www.dartlang.org/guides/language/sound-dart)。

### 默认值
在JavaScript中，未初始化的变量是undefined。
```javascript
// JavaScript
var name; // == undefined
```

在Dart中，未初始化的变量的初始值为null。由于数字是Dart中的对象，因此即使数字类型的未初始化变量也具有该值null。
```dart
// Dart
var name; // == null
int x; // == null
```

在[DartPad](https://dartpad.dartlang.org/57ec21faa8b6fe2326ffd74e9781a2c7)中尝试一下。

有关更多信息，请参阅有关[变量](https://www.dartlang.org/guides/language/language-tour#variables)的文档。

## 检查null或零
在JavaScript中，1或任何非空对象的值都视为true。
```javascript
// JavaScript
var myNull = null;
if (!myNull) {
  console.log("null is treated as false");
}
var zero = 0;
if (!zero) {
  console.log("0 is treated as false");
}
```

在Dart中，只有布尔值`true`被视为true。
```dart
// Dart
var myNull = null;
if (myNull == null) {
  print('use "== null" to check null');
}
var zero = 0;
if (zero == 0) {
  print('use "== 0" to check zero');
}
```

在[DartPad](https://dartpad.dartlang.org/c85038ad677963cb6dc943eb1a0b72e6)中尝试一下。

## 函数
Dart和JavaScript的函数大致相同。主要区别在于声明。
```javascript
// JavaScript
function fn() {
  return true;
}
```
```dart
// Dart
fn() {
  return true;
}
// can also be written as
bool fn() {
  return true;
}
```
在[DartPad](https://dartpad.dartlang.org/5454e8bfadf3000179d19b9bc6be9918)中尝试一下。

有关更多信息，请参阅有关[函数](https://www.dartlang.org/guides/language/language-tour#functions)的文档。

## 异步编程
### Futures
像JavaScript一样，Dart支持单线程执行。在JavaScript中，Promise对象表示异步操作的最终完成（或失败）及其结果值。

而Dart使用 `Future` 对象来处理这个问题。
```javascript
// JavaScript
_getIPAddress = () => {
  const url="https://httpbin.org/ip";
  return fetch(url)
    .then(response => response.json())
    .then(responseJson => {
      console.log(responseJson.origin);
    })
    .catch(error => {
      console.error(error);
    });
};
```
```dart
// Dart
_getIPAddress() {
  final url = 'https://httpbin.org/ip';
  HttpRequest.request(url).then((value) {
      print(json.decode(value.responseText)['origin']);
  }).catchError((error) => print(error));
}
```
在[DartPad](https://dartpad.dartlang.org/b68eb981456c5eec03daa3c05ee59486)中尝试一下。

有关更多信息，请参阅有关[Futures](https://www.dartlang.org/tutorials/language/futures)的文档。

### `async` 和 `await`
两者都使用`async`函数声明定义异步函数。

在JavaScript中，该`async`函数返回一个`Promise`对象。该`await`操作是用来等待Promise。
```javascript
// JavaScript
async _getIPAddress() {
  const url="https://httpbin.org/ip";
  const response = await fetch(url);
  const json = await response.json();
  const data = await json.origin;
  console.log(data);
}
```
在Dart中，一个`async`函数返回一个`Future`对象，函数的主体被安排在稍后执行。该`await`操作是用来等待`Future`.

```dart
// Dart
_getIPAddress() async {
  final url = 'https://httpbin.org/ip';
  var request = await HttpRequest.request(url);
  String ip = json.decode(request.responseText)['origin'];
  print(ip);
}
```
在[DartPad](https://dartpad.dartlang.org/96e845a844d8f8d91c6f5b826ef38951)中尝试一下。

有关更多信息，请参阅有关[async和await](https://www.dartlang.org/guides/language/language-tour#asynchrony-support)的文档。

# 基础
## 如何创建Flutter应用程序？
要使用React Native创建应用程序，您可以从命令行运行`react-native init`。
```bash
$ react-native init <projectname>
```
要在Flutter中创建应用程序，请执行以下操作之一：

使用`flutter create`命令行中的命令。确保Flutter SDK位于PATH中。
使用安装了Flutter和Dart插件的IDE。
```bash
$ flutter create <projectname>
```

有关更多信息，请参阅"[入门：概述](https://flutter.io/get-started/)"教程，该教程将引导您创建按钮单击计数器应用程序。创建一个Flutter项目会构建在Android和iOS设备上运行示例应用程序所需的所有文件。

## 如何运行应用程序？
在React Native中，您将运行`react-native run-ios`或`react-native run-android`从项目目录运行。

您可以通过几种方式运行Flutter应用程序：

- 使用`flutter run`从项目的根目录。
- 在带有Flutter和Dart插件的IDE中使用“run”选项。

需注意：在启动应用之前，请确保有一个设备是在连接的设备，iOS模拟器或Android模拟器上运行。

有关更多信息，请参阅[Flutter入门](https://flutter.io/get-started/)文档。

## 如何导入`widget`？
在React Native中，您需要导入每个必需的组件。
```javascript
//React Native
import React from "react";
import { StyleSheet, Text, View } from "react-native";
```

在Flutter中，要使用Material Design库中的widget，请导入该`material.dart`包。要使用iOS样式widget，请导入`Cupertino`库。要使用更基本的`widget`，请导入`widget`库。或者，您可以编写自己的`widget`库并导入它。
```dart
import 'package:flutter/material.dart';
import 'package:flutter/cupertino.dart';
import 'package:flutter/widget.dart';
import 'package:flutter/my_widget.dart';
```

无论您导入哪个Widget软件包，Dart都只会引入应用中使用的widget。

有关更多信息，请参阅[Flutter widget Catalog](https://flutter.io/widget/)。

## Flutter中的`"Hello world!"`在React Native里怎么写？
在React Native中，`HelloWorldApp`类`React.Component`通过返回View组件来扩展和实现渲染方法。
```javascript
// React Native
import React from "react";
import { StyleSheet, Text, View } from "react-native";

export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>Hello world!</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#fff",
    alignItems: "center",
    justifyContent: "center"
  }
});
```

在flutter，您可以使用创建一个相同的"Hello world!"的应用程序`Center`和`Text`小工具从核心部件库。该`Center`widget成为widget树的根，并且有一个`Text`子部件。
```dart
// Flutter
import 'package:flutter/material.dart';

void main() {
  runApp(
    Center(
      child: Text(
        'Hello, world!',
        textDirection: TextDirection.ltr,
      ),
    ),
  );
}
```

以下图片显示了基本Flutter"Hello world!"应用程序的Android和iOS UI。
<img src="./pic1.png" height="300" title="android和ios对比"/>

现在您已经看到了最基本的Flutter应用程序，下一节将介绍如何利用Flutter丰富的widget库来创建一个现代的，优雅的应用程序。

## 如何使用widget并将其嵌套成widget树？
在Flutter中，几乎所有东西都是一个widget。

widget是应用程序用户界面的基本构建块。您将窗口widget组成一个层次结构，称为窗口widget树。每个widget嵌套在父窗口widget中，并从其父窗口继承属性。即使应用程序对象本身也是一个widget。没有单独的“应用程序”对象。相反，根部件扮演着这个角色。

一个widget可以定义：

- 结构元素 - 按钮或菜单
- 风格元素 - 字体或颜色方案
- 布局 - padding或alignment

以下示例使用`Material`库中的widget显示"Hello world!"应用程序。在此示例中，widget树嵌套在`MaterialApp`根widget中。
```dart
// Flutter
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Welcome to Flutter',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Welcome to Flutter'),
        ),
        body: Center(
          child: Text('Hello world'),
        ),
      ),
    );
  }
}
```
<img src="./pic2.png" height="300" title="android和ios对比"/>

## 如何创建可重用组件？

# 项目结构和资源目录
## 我从哪里开始编写代码？
## 如何在Flutter应用程序中构建文件？
## 我在哪里放置我的资源和资产以及如何使用它们？
## 如何通过网络加载图像？
## 我如何安装软件包和软件包插件？

# Flutter widget

# Views
## 什么相当于`View`容器？
## 什么相当于FlatList或SectionList？
## 我如何使用Canvas尽情绘画？

# Layouts
## 如何使用widget来定义布局属性？
## 如何分层widget？

# Styling
## 如何设置组件的样式？
## 如何使用`Icons`和`Colors`？
## 我如何添加风格主题？

# State管理
## StatelessWidget
## StatefulWidget
## 什么是StatefulWidget和StatelessWidget最佳实践？
### 1.确定一个widget应该是一个StatefulWidget还是一个StatelessWidget
### 2.确定哪个对象管理widget的状态（对于StatefulWidget）
### 3.子类StatefulWidget和State
### 4.将StatefulWidget添加到widget树中

# Props

# 本地存储
## 如何在应用内持久化存储一个键值对？

# 路由
## 如何在屏幕之间使用`navigate`？
## 如何使用`Tab navigation`和`Drawer navigation`？
### Tab navigation
### Drawer navigation

# 手势检测和触摸事件处理
## 如何添加`click`或`press`监听器到`widget`？

# 发出HTTP网络请求
## 我如何从API调用中获取数据？

# 表单输入
## 如何使用文本字段widget？
## 我如何使用表单widget？

# 判断平台特定的代码

# 调试
## 如何访问应用内开发人员菜单？
## 如何执行热重载？
## 我可以用什么工具在Flutter中调试我的应用程序？

# 动画
## 如何添加简单的淡入动画？
## 如何给卡片添加滑动动画？

# React Native 和 Flutter Widget 相同作用的组件