---
title: 【flutter】用于React Native开发者的flutter参考文档
categories: flutter
date: 2018-07-04
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

在flutter，您可以使用创建一个相同的"Hello world!"的应用程序`Center`和`Text`小工具从核心部件库。该`Center`widget成为widget树的根，并且有一个`Text`widget。
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
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic1.png" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic2.png" width="350" title="iOS"/></div>
</div>

现在您已经看到了最基本的Flutter应用程序，下一节将介绍如何利用Flutter丰富的widget库来创建一个现代的，优雅的应用程序。

## 如何使用widget并将其嵌套成widget树？
在Flutter中，几乎所有东西都是一个widget。

widget是应用程序用户界面的基本构建块。您将widget组成一个层次结构，称为widget树。每个widget嵌套在父widget中，并从其父窗口继承属性。即使应用程序对象本身也是一个widget。没有单独的“应用程序”对象。相反，根部件扮演着这个角色。

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
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic3.png" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic4.png" width="350" title="iOS"/></div>
</div>

在编写应用程序时，您将使用两种类型的widget：[StatelessWidget](https://docs.flutter.io/flutter/widgets/StatelessWidget-class.html)或 [StatefulWidget](https://docs.flutter.io/flutter/widgets/StatefulWidget-class.html)。StatelessWidget听起来就像是一个没有状态的widget。一个StatelessWidget被创建一次，永远不会改变它的外观。StatefulWidget根据收到的数据或用户输入动态改变状态。

无状态和有状态widget之间的重要区别在于，有状态widget具有一个状态对象，用于存储状态数据并跨树重建执行，因此不会丢失。

在简单或基本应用程序中，嵌套widget很容易，但随着代码库变得越来越大，应用程序变得越来越复杂，您应该将嵌套widget深入分解为返回widget或更小类的函数。创建单独的函数和widget允许您在应用程序内重用这些组件。

## 如何创建可重用组件？
在React Native中，您可以定义一个类来创建可重用组件，然后使用`props`方法来设置或返回所选元素的属性和值。在下面的例子中，`CustomCard`该类是定义的，然后在父类中使用。
```javascript
// React Native
class CustomCard extends React.Component {
  render() {
    return (
      <View>
        <Text > Card {this.props.index} </Text>
        <Button
          title="Press"
          onPress={() => this.props.onPress(this.props.index)}
        />
      </View>
    );
  }
}

// Usage
<CustomCard onPress={this.onPress} index={item.key} />
```

在Flutter中，定义一个类来创建自定义widget，然后重用widget。您还可以定义和调用返回可重用widget的`build`函数，如以下示例中的函数中所示。
```dart
// Flutter
class CustomCard extends StatelessWidget {
  CustomCard({@required this.index, @required this.onPress});

  final index;
  final Function onPress;

  @override
  Widget build(BuildContext context) {
    return Card(
      child: Column(
        children: <Widget>[
          Text('Card $index'),
          FlatButton(
            child: const Text('Press'),
            onPressed: this.onPress,
          ),
        ],
      )
    );
  }
}
...
// Usage
CustomCard(
  index: index,
  onPress: () { 
    print('Card $index');
  },
)
...
```
在前面的例子中，`CustomCard`该类的构造函数使用Dart的大括号语法来表示命名的可选参数。`{}`

要求这些字段，请从构造函数中删除花括号，或者添加`@required`到构造函数中。

以下屏幕截图显示了可重用的`CustomCard`类的示例。
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic5.png" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic6.png" width="350" title="iOS"/></div>
</div>

# 项目结构和资源目录
## 我从哪里开始编写代码？
从`main.dart`文件开始。它是在您创建Flutter应用程序时自动生成的。
```dart
// Dart
void main(){
 print("Hello, this is the main function.");
}
```
在Flutter中，入口点文件是`<projectname>/lib/main.dart`从`main`函数开始执行。

## 如何在Flutter应用程序中构建文件？
当您创建一个新的Flutter项目时，它会构建以下目录结构。您可以稍后自定义，但这是您开始的地方。
```
┬
└ projectname
  ┬
  ├ android      - android源码。
  ├ build        - 存储iOS和Android的build文件。
  ├ ios          - iOS源码。
  ├ lib          - 外部可访问的Dart源文件。
    ┬
    └ src        - 额外的源文件。
    └ main.dart  - Flutter入口点和新应用程序的开始。
  ├ test         - 自动化测试文件。
  └ pubspec.yaml - Flutter应用程序的元数据。
                   这相当于React Native中的package.json文件。
```

## 我在哪里放置我的资源和资产以及如何使用它们？
Flutter资源或资产是与您的应用程序捆绑并部署的文件，可在运行时访问。扑动应用程序可以包含以下资产类型：

- 静态数据，如JSON文件
- 配置文件
- 图标和图像（JPEG，PNG，GIF，动画GIF，WebP，动画WebP，BMP和WBMP）

Flutter使用`pubspec.yaml`位于项目根目录的文件来识别应用程序所需的资产。
```
flutter:
  assets:
    - assets/my_icon.png
    - assets/background.png
```

该assets 节点指定应包含在应用程序中的文件。每个资产都由相对于`pubspec.yaml` 资产文件所在文件的显式路径标识。声明资产的顺序无关紧要。使用的实际目录（assets 在本例中）无关紧要。但是，虽然资产可以放在任何app目录中，但最好将它们放在assets目录中。

在构建期间，Flutter将资产放入称为资产包的特殊存档中，应用程序在运行时读取这些存档。在资产部分中指定资产的路径时pubspec.yaml，构建过程会查找相邻子目录中具有相同名称的任何文件。这些文件也与指定的资产一起包含在资产包中。flutter使用资产变型时，为您的应用程序选择合适分辨率的图像。

在React Native中，您可以通过将图像文件放在源代码目录中并引用它来添加静态图像。
```javascript
<Image source={require("./my-icon.png")} />
```
在Flutter中，使用AssetImagewidget构建方法中的类向应用程序添加静态图像。
```dart
image: AssetImage('assets/background.png'),
```

有关更多信息，请参阅[在Flutter中添加资产和图像](https://flutter.io/assets-and-images/)。

## 如何通过网络加载图像？
在React Native中，您可以在`Image`组件使用props属性`source`的`uri`方式进行指定，并在Image需要时提供大小。

在Flutter中，使用`Image.network`构造函数来包含URL中的图像。
```dart
// Flutter
body: Image.network('https://flutter.io/images/owl.jpg'),
```

## 我如何安装软件包和软件包插件？
Flutter支持使用由其他开发者贡献给Flutter和Dart生态系统的公用package。这使您可以快速构建应用程序，而无需从头开始开发所有内容。包含平台特定代码的软件包称为package plugins。

在React Native中，您可以使用`yarn add {package-name}`或`npm install {package-name} --save`从命令行安装package。

在Flutter中，使用以下说明安装包装：

1.将软件包名称和版本添加到`pubspec.yaml`依赖项部分。下面的例子展示了如何将·`google_sign_inDart`包添加到`pubspec.yaml`文件中。在YAML文件中工作时检查缩进，因为缩进空格很重要！
```
dependencies:
  flutter:
 sdk: flutter
  google_sign_in: ^3.0.3
```

2.通过使用命令行来安装软件包`flutter packages get`。如果使用IDE，它通常会自动运行`flutter packages get`，或者它可能会提示您这样做。

3.将软件包导入您的应用程序代码，如下所示：
```dart
import 'package:flutter/cupertino.dart';
```

有关更多信息，请参阅[使用包](https://flutter.io/using-packages/)和[开发包和插件](https://flutter.io/developing-packages/)。

您可以在[pub.dartlang.org的Flutter Packages部分](https://pub.dartlang.org/flutter/)找到Flutter开发人员共享的许多packages。

# Flutter widget
在Flutter中，您可以使用widget构建您的用户界面，这些widget根据当前的配置和状态描述他们的视图应该是什么样子。

widget通常由许多嵌套的小型单一用途widget组成，以产生强大的效果。例如，`Container`widget由几个widget组成，负责布局，绘制，定位和大小调整。具体地，`Container`widget包括`LimitedBox`，`ConstrainedBox`，`Align`，`Padding`，`DecoratedBox`，和`Transform` widgets。Container您可以用新的和独特的方式组合这些和其他简单的widget，而不是创建自定义效果的子类。

该`Center`部件是如何控制布局的另一个例子。要将widget居中，将其包装在`Center`widget中，然后使用布局widget进行对齐，行，列和网格。这些布局widget没有自己的可视化表示。相反，他们唯一的目的是控制另一个widget布局的某些方面。要了解widget以某种方式呈现的原因，检查相邻widget通常很有帮助。

有关更多信息，请参阅[Flutter技术概述](https://flutter.io/technical-overview/)。

有关Widgets包中的核心widget的更多信息，请参阅[Flutter Basic Widgets](https://flutter.io/widgets/basics/)，[Flutter Widget 目录](https://flutter.io/widgets/)或 [Flutter Widget 索引](https://flutter.io/widgets/widgetindex/)。

# Views
## 什么相当于`View`容器？
在React Native中，`View`是一个支持布局的容器`Flexbox`，样式，触摸处理和可访问性控件。

在Flutter中，您可以使用Widgets库中的核心布局widget，例如 [Container](https://docs.flutter.io/flutter/widgets/Container-class.html)，[Column](https://docs.flutter.io/flutter/widgets/Column-class.html)，[Row](https://docs.flutter.io/flutter/widgets/Row-class.html)和 [Center](https://docs.flutter.io/flutter/widgets/Center-class.html)。

有关更多信息，请参阅[Layout Widgets](https://flutter.io/widgets/layout/)目录。

## 什么相当于`FlatList`或`SectionList`？
`List`是垂直排列的可滚动组件列表。

在React Native中，`FlatList`或`SectionList`用于呈现简单或分段的列表。
```javascript
// React Native
<FlatList
  data={[ ... ]}
  renderItem={({ item }) => <Text>{item.key}</Text>}
/>
```
[ListView](https://docs.flutter.io/flutter/widgets/ListView-class.html)是Flutter最常用的滚动widget。默认的构造函数需要一个明确的子列表。[ListView](https://docs.flutter.io/flutter/widgets/ListView-class.html)对于少数widget是最合适的。对于一个大的或无限的列表，使用`ListView.builder`，它根据需要构建它的子项，并只构建那些可见的子项。
```dart
// Flutter
var data = [ ... ];
ListView.builder(
  itemCount: data.length,
  itemBuilder: (context, int index) {
    return Text(
      data[index],
    );
  },
)
```
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic7.gif" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic8.gif" width="350" title="iOS"/></div>
</div>
要了解如何实现无限滚动列表，请参阅 [编写您的第一个flutter应用程序，第1部分](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt1/#0) codelab。

## 我如何使用Canvas绘制？
在React Native中，画布组件不存在，所以`react-native-canvas`使用第三方库。
```javascript
// React Native
handleCanvas = canvas => {
  const ctx = canvas.getContext("2d");
  ctx.fillStyle = "skyblue";
  ctx.beginPath();
  ctx.arc(75, 75, 50, 0, 2 * Math.PI);
  ctx.fillRect(150, 100, 300, 300);
  ctx.stroke();
};

render() {
  return (
    <View>
      <Canvas ref={this.handleCanvas} />
    </View>
  );
}
```
在Flutter中，您可以使用CustomPaint 和CustomPainter类绘制到画布。

以下示例显示了如何在绘制阶段使用`CustomPaint`widget进行绘制。它实现了抽象类CustomPainter，并将其传递给CustomPaint的画家属性。CustomPaint子类必须实现`paint`和`shouldRepaint`方法。
```dart
// Flutter
class MyCanvasPainter extends CustomPainter {

  @override
  void paint(Canvas canvas, Size size) {
    Paint paint = Paint();
    paint.color = Colors.amber;
    canvas.drawCircle(Offset(100.0, 200.0), 40.0, paint);
    Paint paintRect = Paint();
    paintRect.color = Colors.lightBlue;
    Rect rect = Rect.fromPoints(Offset(150.0, 300.0), Offset(300.0, 400.0));
    canvas.drawRect(rect, paintRect);
  }

  bool shouldRepaint(MyCanvasPainter oldDelegate) => false;
  bool shouldRebuildSemantics(MyCanvasPainter oldDelegate) => false;
}
class _MyCanvasState extends State<MyCanvas> {

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: CustomPaint(
        painter: MyCanvasPainter(),
      ),
    );
  }
}
```
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic9.png" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic10.png" width="350" title="iOS"/></div>
</div>

# Layouts(布局)
## 如何使用widget来定义布局属性？
在React Native中，大部分布局可以通过传递给特定组件的道具完成。例如，您可以使用`View`组件的props属性`style`来指定flexbox属性。要在列中排列组件，您需要指定一个属性，如：`flexDirection:'column'`。

在Flutter中，布局主要由widget定义，widget专门用于提供布局，并结合控件和其样式属性。

例如，[`Column`](https://docs.flutter.io/flutter/widgets/Column-class.html) 和 [`Row`](https://docs.flutter.io/flutter/widgets/Row-class.html) 将获取一组子对象，并分别垂直和水平对齐。[`Container`](https://docs.flutter.io/flutter/widgets/Container-class.html) 采用布局和样式属性的组合，而[`Center`](https://docs.flutter.io/flutter/widgets/Center-class.html)控件将其子控件居中。
```dart
// Flutter
Center(
  child: Column(
    children: <Widget>[
      Container(
        color: Colors.red,
        width: 100.0,
        height: 100.0,
      ),
      Container(
        color: Colors.blue,
        width: 100.0,
        height: 100.0,
      ),
      Container(
        color: Colors.green,
        width: 100.0,
        height: 100.0,
      ),
    ],
  ),
)
```
Flutter在其核心部件库中提供了各种布局部件。例如，[`Padding`](https://docs.flutter.io/flutter/widgets/Padding-class.html)和[`Align`](https://docs.flutter.io/flutter/widgets/Align-class.html)，和[`Stack`](https://docs.flutter.io/flutter/widgets/Stack-class.html)。

有关完整列表，请参阅[Layout Widgets](https://flutter.io/widgets/layout/)。
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic11.gif" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic12.gif" width="350" title="iOS"/></div>
</div>

## 如何布局widget？
在React Native中，组件可以使用absolute定位进行布局。

Flutter使用[Stack](https://docs.flutter.io/flutter/widgets/Stack-class.html)widget在图层中排列子widget。widget可以完全或部分重叠基本widget。

该`Stack`widget的子widget相对于它的框的边缘进行定位。如果你只是想重叠几个子部件，这个类很有用。
```dart
// Flutter
Stack(
  alignment: const Alignment(0.6, 0.6),
  children: <Widget>[
    CircleAvatar(
      backgroundImage: NetworkImage(
        "https://avatars3.githubusercontent.com/u/14101776?v=4"),
    ),
    Container(
      decoration: BoxDecoration(
          color: Colors.black45,
      ),
      child: Text('Flutter'),
    ),
  ],
)
```
前面的示例用于`Stack`在`Container`上覆盖`Container`（`Text` 在半透明的黑色背景上显示它）`CircleAvatar`。`Stack`使用对齐属性和对齐坐标偏移文本。
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic13.png" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic14.png" width="350" title="iOS"/></div>
</div>

有关更多信息，请参阅[Stack](https://docs.flutter.io/flutter/widgets/Stack-class.html)类文档。

# Styling
## 如何设置组件的样式？
在React Native中，内联样式并`Stylesheets.create`用于对组件进行样式设置。
```javascript
// React Native
<View style={styles.container}>
  <Text style={{ fontSize: 32, color: "cyan", fontWeight: "600" }}>
    This is a sample text
  </Text>
</View>

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#fff",
    alignItems: "center",
    justifyContent: "center"
  }
});
```
在Flutter中，一个`Text`widget可以`TextStyle`为其样式属性选择一个类。如果您想在多个位置使用相同的文本样式，则可以创建一个`TextStyle`类并将其用于多个`Text`widget。
```dart
// Flutter
var textStyle = TextStyle(fontSize: 32.0, color: Colors.cyan, fontWeight:
   FontWeight.w600);
	...
Center(
  child: Column(
    children: <Widget>[
      Text(
        'Sample text',
        style: textStyle,
      ),
      Padding(
        padding: EdgeInsets.all(20.0),
        child: Icon(Icons.lightbulb_outline,
          size: 48.0, color: Colors.redAccent)
      ),
    ],
  ),
)
```
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic15.gif" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic16.gif" width="350" title="iOS"/></div>
</div>

## 如何使用`Icons`和`Colors`？
React Native不包含对图标的支持，因此使用第三方库。

在Flutter中，导入`Material`库也引入了丰富的 Material [Icons](https://docs.flutter.io/flutter/material/Icons-class.html) 和 [Colors](https://docs.flutter.io/flutter/material/Colors-class.html)。
```dart
Icon(Icons.lightbulb_outline, color: Colors.redAccent)
```

使用`Icons`类时，请确保`uses-material-design: true`在项目`pubspec.yaml`文件中设置。这可确保`MaterialIcons`显示图标的字体包含在您的应用程序中。
```
name: my_awesome_application
flutter: uses-material-design: true
```

Flutter的[Cupertino（iOS风格）](https://flutter.io/widgets/cupertino/)包为当前的iOS设计语言提供了高保真的widgets。要使用该`CupertinoIcons`字体，请在项目 `pubspec.yaml` 文件中添加一个依赖项 `cupertino_icons`。
```
name: my_awesome_application
dependencies:
  cupertino_icons: ^0.1.0
```

要全局自定义组件的颜色和样式，请使用ThemeData为主题的各个方面指定默认颜色。将主题属性设置`MaterialApp`为该`ThemeData`对象。该 `Colors` 类从Material Design提供的主题配色。

以下示例将主题颜色设置为blue，并将文本选择设置为red。

```dart
class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        textSelectionColor: Colors.red
      ),
      home: SampleAppPage(),
    );
  }
}
```

## 我如何添加风格主题？
在React Native中，通用主题是为样式表中的组件定义的，然后在组件中使用。

在Flutter中，通过在[`ThemeData`](https://docs.flutter.io/flutter/material/ThemeData-class.html)类中定义样式并将其传递给[MaterialApp](https://docs.flutter.io/flutter/material/MaterialApp-class.html)widget中的`theme`属性，为几乎所有内容创建统一样式。
```dart
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData(
        primaryColor: Colors.cyan,
        brightness: Brightness.dark,
      ),
      home: StylingPage(),
    );
  }
```
即使不使用`MaterialApp`widget，也可以用Theme。[`Theme`](https://docs.flutter.io/flutter/material/Theme-class.html)在其数据参数中接受一个`ThemeData`，并将`ThemeData`应用于其所有子部件。
```dart
 @override
  Widget build(BuildContext context) {
    return Theme(
      data: ThemeData(
        primaryColor: Colors.cyan,
        brightness: brightness,
      ),
      child: Scaffold(
         backgroundColor: Theme.of(context).primaryColor,
              ...
              ...
      ),
    );
  }
```

# State管理
状态是可以在构建widgets时同步读取的信息，也可以是在widgets的生命周期内可能更改的信息。要在Flutter中管理应用程序状态，请使用与状态对象配对的 [StatefulWidget](https://docs.flutter.io/flutter/widgets/StatefulWidget-class.html)。
## StatelessWidget
StatelessWidget是一个不需要状态更改的widget，它没有内部状态可管理。

如果所描述的用户界面部分不依赖于对象本身中的配置信息和widget填充的BuildContext，则无状态widget非常有用。

[`AboutDialog`](https://docs.flutter.io/flutter/material/AboutDialog-class.html)、[`CircleAvator`](https://docs.flutter.io/flutter/material/CircleAvatar-class.html)和[`Text`](https://docs.flutter.io/flutter/widgets/Text-class.html)是无状态widget的示例，它们是[`StatelessWidget`](https://docs.flutter.io/flutter/widgets/StatelessWidget-class.html)的子类。
```dart
// Flutter
import 'package:flutter/material.dart';

void main() => runApp(MyStatelessWidget(text: "StatelessWidget Example to show immutable data"));

class MyStatelessWidget extends StatelessWidget {
  final String text;
  MyStatelessWidget({Key key, this.text}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Text(
        text,
        textDirection: TextDirection.ltr,
      ),
    );
  }
}
```
在前面的例子中，您使用了`MyStatelessWidget`类的构造函数来传递一个`final`的`text`。这个类扩展了`StatelessWidget`,它包含不可变数据。

`build`通常仅在三种情况下调用无状态widget的方法：

- 当widget插入到树中时
- 当widget的父部件更改其配置时
- 当依赖于[`InheritedWidget`](https://docs.flutter.io/flutter/widgets/InheritedWidget-class.html)时，更改

## StatefulWidget
[StatefulWidget](https://docs.flutter.io/flutter/widgets/StatefulWidget-class.html)是一个状态变化的widget。使用`setState`方法来管理`StatefulWidget`的状态变更。调用 `setState` 会告诉Fltter框架，某个状态发生了变化，这会导致应用程序重新运行`build`方法，以便应用程序能够反映变更。

state是在构建widget时可以同步读取的信息，在widget的生命周期内可能会发生更改。widget实现者有责任确保状态变化时立即通知状态。当widget可以动态更改时，使用`StatefulWidget`。例如，通过在表单中键入或移动滑块来更改widget的状态。或者，它可以随着时间的推移而改变-也许是一个数据提要更新了UI。

[Checkbox](https://docs.flutter.io/flutter/material/Checkbox-class.html)，[Radio](https://docs.flutter.io/flutter/material/Radio-class.html)，[Slider](https://docs.flutter.io/flutter/material/Slider-class.html)，[InkWell](https://docs.flutter.io/flutter/material/InkWell-class.html)，[Form](https://docs.flutter.io/flutter/widgets/Form-class.html)和[TextField](https://docs.flutter.io/flutter/material/TextField-class.html)是有状态widget的示例，它们是[StatefulWidget](https://docs.flutter.io/flutter/widgets/StatefulWidget-class.html)的子类。

下面的示例声明一个StatefulWidget，它需要`createState()`方法。此方法创建用于管理widget状态的状态对象`_MyStatefulWidgetState`。
```dart
class MyStatefulWidget extends StatefulWidget {
  MyStatefulWidget({Key key, this.title}) : super(key: key);
  final String title;

  @override
  _MyStatefulWidgetState createState() => _MyStatefulWidgetState();
}
```

下面的状态类`_MyStatefulWidgetState`实现了widget的`build()`方法。例如，当状态更改时，当用户切换按钮时，使用新的切换值调用`setState`。这会导致框架在UI中重新生成这个widget。
```dart
class _MyStatefulWidgetState extends State<MyStatefulWidget> {
  bool showtext=true;
  bool toggleState=true;
  Timer t2;

  void toggleBlinkState(){
    setState((){
      toggleState=!toggleState;
    });
    var twenty = const Duration(milliseconds: 1000);
    if(toggleState==false) {
      t2 = Timer.periodic(twenty, (Timer t) {
        toggleShowText();
      });
    } else {
      t2.cancel();
    }
  }

  void toggleShowText(){
    setState((){
      showtext=!showtext;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          children: <Widget>[
            (showtext
              ?(Text('This execution will be done before you can blink.'))
              :(Container())
            ),
            Padding(
              padding: EdgeInsets.only(top: 70.0),
              child: RaisedButton(
                onPressed: toggleBlinkState,
                child: (toggleState
                  ?( Text('Blink'))
                  :(Text('Stop Blinking'))
                )
              )
            )
          ],
        ),
      ),
    );
  }
}
```
## 什么是StatefulWidget和StatelessWidget最佳实践？
在设计widget时，需要考虑以下几点。
### 1.确定一个widget应该是一个StatefulWidget还是一个StatelessWidget
在Flutter中，widget是有状态的还是无状态的 - 取决于它们是否依赖于状态变化。

- 如果widget发生更改 - 用户与其进行交互或数据源中断UI，则它是有状态的。
- 如果一个widget是只读的或不可变的，那么它就是无状态。

### 2.确定哪个对象管理widget的状态（对于StatefulWidget）
在Flutter中，管理状态有三种主要方式：

- widget管理自己的状态
- 父widget管理widget的状态
- 混合搭配的方法

在决定使用哪种方法时，请考虑以下原则：

- 如果所涉及的状态是用户数据，例如复选框的选中或未选中模式，或者滑块的位置，那么状态最好由父widget管理。
- 如果所涉及的状态是图形的，例如动画，那么widget本身就可以最好地管理状态。
- 如果有疑问，让父widget管理子widget的状态。

### 3.子类StatefulWidget和State
`MyStatefulWidget`类管理自己的状态-它继承自StatefulWidget，它覆盖createState()方法来创建`State`对象，框架调用`createState()`来构建widget。在本例中，createState()创建了一个`_MyStatefulWidgetState`的实例，该实例在下一个最佳实践中实现。
```dart
class MyStatefulWidget extends StatefulWidget {
  MyStatefulWidget({Key key, this.title}) : super(key: key);
  final String title;

  @override
  _MyStatefulWidgetState createState() => _MyStatefulWidgetState();
}

class _MyStatefulWidgetState extends State<MyStatefulWidget> {

  @override
  Widget build(BuildContext context) {
    ...
  }
}
```
### 4.将StatefulWidget添加到widget树中
`StatefulWidget`在app的构建方法中将自定义添加到widget树。
在应用程序`build`方法中的widget树中添加自定义`StatefulWidget`。
```dart
class MyStatelessWidget extends StatelessWidget {
  // This widget is the root of your application.

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyStatefulWidget(title: 'State Change Demo'),
    );
  }
}
```
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic17.gif" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic18.gif" width="350" title="iOS"/></div>
</div>

# Props
在React Native中，大多数组件在使用不同的参数或属性创建时都可以自定义`props`。这些参数可以在子组件中使用`this.props`。
```javascript
// React Native
class CustomCard extends React.Component {
  render() {
    return (
      <View>
        <Text> Card {this.props.index} </Text>
        <Button
          title="Press"
          onPress={() => this.props.onPress(this.props.index)}
        />
      </View>
    );
  }
}
class App extends React.Component {

  onPress = index => {
    console.log("Card ", index);
  };

  render() {
    return (
      <View>
        <FlatList
          data={[ ... ]}
          renderItem={({ item }) => (
            <CustomCard onPress={this.onPress} index={item.key} />
          )}
        />
      </View>
    );
  }
}
```

在Flutter中，您可以`final`为参数化构造函数中的属性指定标记的局部变量或函数。
```dart
// Flutter
class CustomCard extends StatelessWidget {

  CustomCard({@required this.index, @required this.onPress});
  final index;
  final Function onPress;

  @override
  Widget build(BuildContext context) {
  return Card(
    child: Column(
      children: <Widget>[
        Text('Card $index'),
        FlatButton(
          child: const Text('Press'),
          onPressed: this.onPress,
        ),
      ],
    ));
  }
}
    ...
//Usage
CustomCard(
  index: index,
  onPress: () {
    print('Card $index');
  },
)
```
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic5.png" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic6.png" width="350" title="iOS"/></div>
</div>


# 本地存储
如果您不需要存储大量数据并且不需要结构，则可以使用`shared_preferences`它来读取和编写基本数据类型的持久键值对：布尔值，浮点数，整数，长整数和字符串。

## 如何在应用内持久化存储一个键值对？
在React Native中，您可以使用组件的`setItem`和`getItem`函数  `AsyncStorage`来存储和检索应用程序的持久性和全局性数据。
```javascript
// React Native
await AsyncStorage.setItem( "counterkey", json.stringify(++this.state.counter));
AsyncStorage.getItem("counterkey").then(value => {
  if (value != null) {
    this.setState({ counter: value });
  }
});
```

在Flutter中，使用[`shared_preferences`](https://github.com/flutter/plugins/tree/master/packages/shared_preferences)插件来存储和检索应用程序持久化的全局键值数据。`shared_preferences`插件是iOS的`NSUserDefaults`和Android的`SharedPreferences`的封装，为简单数据提供持久存储。要使用该插件，请`shared_preferences`在`pubspec.yaml`文件中添加依赖项，然后在Dart文件中导入该包。
```
dependencies:
  flutter:
    sdk: flutter
  shared_preferences: ^0.3.0
```
```dart
// Dart
import 'package:shared_preferences/shared_preferences.dart';
```

要实现持久数据，请使用`SharedPreferences`类提供的setter方法 。setter方法可用于各种原始类型，例如`setInt`，`setBool`，和`setString`。要读取数据，请使用`SharedPreferences`类提供的相应getter方法。对于每一个setter有相应的getter方法，例如，`getInt`，`getBool`，和`getString`。

```dart
SharedPreferences prefs = await SharedPreferences.getInstance();
_counter = prefs.getInt('counter');
prefs.setInt('counter', ++_counter);
setState(() {
  _counter = _counter;
});
```

# 路由
大多数应用程序都包含几个屏幕，用于显示不同类型的信息。例如，您可能有一个产品屏幕，该屏幕显示用户可以点击产品图片以在新屏幕上获取有关该产品的更多信息的图片。

在Android中，新的屏幕是新的活动。在iOS中，新屏幕是新的ViewController。在flutter中，屏幕只是widgets！要导航到flutter中的新屏幕，请使用`Navigator`widget。

## 如何在屏幕之间使用`navigate`？
在React Native中，有三个主要导航器：StackNavigator，TabNavigator和DrawerNavigator。每种都提供了配置和定义屏幕的方法。
```javascript
// React Native
const MyApp = TabNavigator(
  { Home: { screen: HomeScreen }, Notifications: { screen: tabNavScreen } },
  { tabBarOptions: { activeTintColor: "#e91e63" } }
);
const SimpleApp = StackNavigator({
  Home: { screen: MyApp },
  stackScreen: { screen: StackScreen }
});
export default (MyApp1 = DrawerNavigator({
  Home: {
    screen: SimpleApp
  },
  Screen2: {
    screen: drawerScreen
  }
}));
```

在Flutter中，有两个用于在屏幕之间导航的主要widget：
- [Route](https://docs.flutter.io/flutter/widgets/Route-class.html)是应用程序屏幕或页面的抽象。
- [Navigator](https://docs.flutter.io/flutter/widgets/Navigator-class.html)是一个管理路由的widget

`Navigator`被定义为一个widget，它管理一组具有`stack`规则的子widget。`Navigator`管理路由对象的`stack`，并提供管理`stack`的方法，如[`Navigator.push`](https://docs.flutter.io/flutter/widgets/Navigator/push.html)和[`Navigator.pop`](https://docs.flutter.io/flutter/widgets/Navigator/pop.html)。路径列表可以在[`MaterialApp`](https://docs.flutter.io/flutter/material/MaterialApp-class.html)widget中指定，或者可以在动态中构建，例如在hero动画中。以下示例指定`MaterialApp`widget中的命名路由。
```dart
// Flutter
class NavigationApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
            ...
      routes: <String, WidgetBuilder>{
        '/a': (BuildContext context) => usualNavscreen(),
        '/b': (BuildContext context) => drawerNavscreen(),
      }
            ...
  );
  }
}
```

要导航到命名路由，widget`of()`方法`Navigator`用于指定`BuildContext`。路由的名称被传递给该`pushNamed`函数以导航到指定的路由。
```dart
Navigator.of(context).pushNamed('/a');
```

您还可以使用`Navigator`的`push`方法，该方法将给定的数据添加一个[`Route`](https://docs.flutter.io/flutter/widgets/Route-class.html)到`Navigator`的历史记录中，并跳转到它。在下面的示例中，[`MaterialPageRouting`](https://docs.flutter.io/flutter/material/MaterialPageRoute-class.html)widget是一种模态路由，它会对平台自适应的转换替换整个屏幕，并需要一个`WidgetBuilder`必需参数。
```dart
Navigator.push(context, MaterialPageRoute(builder: (BuildContext context) => UsualNavscreen()));
```

## 如何使用选项卡导航(Tab navigation)和抽屉导航(Drawer navigation)？
在Material Design应用程序中，Flutter导航有两个主要选项：选项卡和抽屉。当没有足够的空间支持选项卡时，抽屉提供了一个很好的选择。

### 标签导航（Tab navigation）
在react-native，`createBottomTabNavigator`和`TabNavigation`用于显示选项卡和选项卡导航。
```javascript
// React Native
import { createBottomTabNavigator } from 'react-navigation';

const MyApp = TabNavigator(
  { Home: { screen: HomeScreen }, Notifications: { screen: tabNavScreen } },
  { tabBarOptions: { activeTintColor: "#e91e63" } }
);
```

Flutter为抽屉和标签导航提供了几个专门的widget：
- [TabController](https://docs.flutter.io/flutter/material/TabController-class.html)
- [TabBar](https://docs.flutter.io/flutter/material/TabBar-class.html)
- [Tab](https://docs.flutter.io/flutter/material/Tab-class.html)
- [TabBarView](https://docs.flutter.io/flutter/material/TabBarView-class.html)

```dart
// Flutter
TabController controller=TabController(length: 2, vsync: this);

TabBar(
  tabs: <Tab>[
    Tab(icon: Icon(Icons.person),),
    Tab(icon: Icon(Icons.email),),
  ],
  controller: controller,
),
```
`TabBar`需要一个`TabController`来控制`TabBarView`之间的选项卡选择。`TabController`构造函数的 `Length`参数是tabs的总数。每当框架触发状态更改时，`TickerProvider`都需要触发通知。`TickerProvider`是`vsync`。每当创建一个`TabController`，都将此参数传递给TabController构造函数。

[`TickerProvider`](https://docs.flutter.io/flutter/scheduler/TickerProvider-class.html) 是一个接口，可以通过类来实现[`Ticker`](https://docs.flutter.io/flutter/scheduler/Ticker-class.html)对象。当帧触发时，必须通知的任何对象都可以使用`Tickers`，但是它们通常是通过[`AnimationController`](https://docs.flutter.io/flutter/animation/AnimationController-class.html)间接使用的。`AnimationControllers`需要一个`TickerProvider`获得他们的`Ticker`, 如果你从一个状态创建一个`AnimationController`，那么你可以使用`TickerProviderStateMixin`类或者`SingleTickerProviderStateMixin`类来获得合适的`TickerProvider`。

[`Scaffold`](https://docs.flutter.io/flutter/material/Scaffold-class.html)widget包装了一个新的`TabBar`widget，并创建了两个选项卡。`TabBarView`widget作为`Scaffold`widget`body`属性的参数传递。与`TabBar`widget的选项卡对应的所有屏幕都是`TabBarView`widget和同一`TabController`的子窗口。
```dart
// Flutter
class _NavigationHomePageState extends State<NavigationHomePage> with SingleTickerProviderStateMixin {
  TabController controller=TabController(length: 2, vsync: this);
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      bottomNavigationBar: Material (
        child: TabBar(
          tabs: <Tab> [
            Tab(icon: Icon(Icons.person),)
            Tab(icon: Icon(Icons.email),),
          ],
          controller: controller,
        ),
        color: Colors.blue,
      ),
      body: TabBarView(
        children: <Widget> [
          home.homeScreen(),
          tabScreen.tabScreen()
        ],
        controller: controller,
      )
    );
  }
}
```

### Drawer navigation
在React Native中，导入所需的react-navigation包，然后使用`createDrawerNavigator`和`DrawerNavigation`。
```javascript
// React Native
export default (MyApp1 = DrawerNavigator({
  Home: {
    screen: SimpleApp
  },
  Screen2: {
    screen: drawerScreen
  }
}));
```

在Flutter中，我们可以使用`Drawer`widget与`Scaffold`组合来创建具有Material Design抽屉的布局。要将`Drawer`添加到应用中，请将其包装在一个`Scaffold`widget中。该`Scaffold`widget为遵循Material Design指南的应用程序提供了一致的可视化结构。它还支持特殊Material Design的组件，如`Drawers`，`AppBars`和`SnackBars`。

`Drawer`widget是一个Material Design面板，它水平地从脚手架边缘滑入，以显示应用程序中的导航链接。可以向`Drawer`widget提供`Button`、`Text`widget或作为子widget显示的项列表。在下面的示例中，`ListTile`widget在点击时提供导航。
```dart
// Flutter
Drawer(
  child:ListTile(
    leading: Icon(Icons.change_history),
    title: Text('Screen2'),
    onTap: () {
      Navigator.of(context).pushNamed("/b");
    },
  ),
  elevation: 20.0,
),
```

`Scaffold`widget还包括一个`AppBar`widget，它自动显示适当的`IconButton`，以便在`Scaffold`中的`Drawer`可用时显示`Drawer`。脚手架自动处理边缘滑动手势，以显示`Drawer`。
```dart
// Flutter
@override
Widget build(BuildContext context) {
  return Scaffold(
    drawer: Drawer(
      child: ListTile(
        leading: Icon(Icons.change_history),
        title: Text('Screen2'),
        onTap: () {
          Navigator.of(context).pushNamed("/b");
        },
      ),
      elevation: 20.0,
    ),
    appBar: AppBar(
      title: Text("Home"),
    ),
    body: Container(),
  );
}
```
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic19.gif" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic20.gif" width="350" title="iOS"/></div>
</div>


# 手势检测和触摸事件处理
为了监听和响应手势，Flutter支持点击，拖动和缩放。Flutter中的手势系统有两个独立的层。第一层包括原始指针事件，它描述屏幕上指针的位置和移动（例如触摸，鼠标和手写笔移动）。第二层包括手势，其描述由一个或多个指针移动组成的语义动作。

## 如何添加`click`或`press`监听器到`widget`？
在React Native中，使用`PanResponder`或组件将监听添加到`Touchable`组件中。
```javascript
// React Native
<TouchableOpacity
  onPress={() => {
    console.log("Press");
  }}
  onLongPress={() => {
    console.log("Long Press");
  }}
>
  <Text>Tap or Long Press</Text>
</TouchableOpacity>
```
对于更复杂的手势并将多个触摸组合成单个手势，`PanResponder`使用。
```javascript
// React Native
class App extends Component {

  componentWillMount() {
    this._panResponder = PanResponder.create({
      onMoveShouldSetPanResponder: (event, gestureState) =>
        !!getDirection(gestureState),
      onPanResponderMove: (event, gestureState) => true,
      onPanResponderRelease: (event, gestureState) => {
        const drag = getDirection(gestureState);
      },
      onPanResponderTerminationRequest: (event, gestureState) => true
    });
  }

  render() {
    return (
      <View style={styles.container} {...this._panResponder.panHandlers}>
        <View style={styles.center}>
          <Text>Swipe Horizontally or Vertically</Text>
        </View>
      </View>
    );
  }
}
```

在Flutter中，要向widget添加click（或press）监听，请使用具有`onPress: field`字段的按钮或可触摸widget。或者，将手势检测添加到任何widget中[`GestureDetector`](https://docs.flutter.io/flutter/widgets/GestureDetector-class.html)。
```dart
// Flutter
GestureDetector(
  child: Scaffold(
    appBar: AppBar(
      title: Text("Gestures"),
    ),
    body: Center(
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: <Widget>[
          Text('Tap, Long Press, Swipe Horizontally or Vertically '),
        ],
      )
    ),
  ),
  onTap: () {
    print('Tapped');
  },
  onLongPress: () {
    print('Long Pressed');
  },
  onVerticalDragEnd: (DragEndDetails value) {
    print('Swiped Vertically');
  },
  onHorizontalDragEnd: (DragEndDetails value) {
    print('Swiped Horizontally');
  },
);
```
有关更多信息，包括Flutter `GestureDetector`回调列表，请参阅[GestureDetector](https://docs.flutter.io/flutter/widgets/GestureDetector-class.html#Properties)类。

<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic21.gif" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic22.gif" width="350" title="iOS"/></div>
</div>

# 发出HTTP网络请求
在大多数应用程序中，从互联网上获取数据是很常见的。在Fltter中，`http`package提供了从互联网上获取数据的最简单方法。

## 如何从API调用中获取数据？
React Native为网络提供了Fetch API - 您可以进行获取请求，然后接收响应以获取数据。
```javascript
// React Native
_getIPAddress = () => {
  fetch("https://httpbin.org/ip")
    .then(response => response.json())
    .then(responseJson => {
      this.setState({ _ipAddress: responseJson.origin });
    })
    .catch(error => {
      console.error(error);
    });
};
```

Flutter使用`http`package。要安装`http`，请将其添加到`pubspec.yaml`的`dependencies`部分。
```
dependencies:
  flutter:
    sdk: flutter
  http: <latest_version>
```

Flutter使用[`dart:io`](https://docs.flutter.io/flutter/dart-io/dart-io-library.html)核心HTTP支持客户端。要创建HTTP客户端，请导入`dart:io`。
```dart
import 'dart:io';
```

客户端支持以下HTTP操作：GET，POST，PUT和DELETE。
```dart
// Flutter
final url = Uri.https('httpbin.org', 'ip');
final httpClient = HttpClient();
_getIPAddress() async {
  var request = await httpClient.getUrl(url);
  var response = await request.close();
  var responseBody = await response.transform(utf8.decoder).join();
  String ip = json.decode(responseBody)['origin'];
  setState(() {
    _ipAddress = ip;
  });
}
```
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic23.gif" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic24.gif" width="350" title="iOS"/></div>
</div>

# 表单输入
Text fields允许用户在应用程序中键入文本，以便用于生成表单、消息应用程序、搜索体验等。Fltter提供了两个核心文本字段widget：[`TextField`](https://docs.flutter.io/flutter/material/TextField-class.html)和[`TextFormField`](https://docs.flutter.io/flutter/material/TextFormField-class.html)。

## 如何使用文本字段widget？
在React Native中，要输入文本，可以使用TextInput组件显示文本输入框，然后使用回调将值存储在变量中。
```javascript
// React Native
<TextInput
  placeholder="Enter your Password"
  onChangeText={password => this.setState({ password })}
 />
<Button title="Submit" onPress={this.validate} />
```

在Flutter中，使用[`TextEditingController`](https://docs.flutter.io/flutter/widgets/TextEditingController-class.html)该类来管理`TextField`widget。每当文本字段被修改时，控制器都会通知其监听者。

监听器读取文本和选择属性，以了解用户在字段中键入的内容。可以通过`controller`的`Text`属性访问`TextField`中的文本。
```dart
// Flutter
final TextEditingController _controller = TextEditingController();
      ...
TextField(
  controller: _controller,
  decoration: InputDecoration(
    hintText: 'Type something', labelText: "Text Field "
  ),
),
RaisedButton(
  child: Text('Submit'),
  onPressed: () {
    showDialog(
      context: context,
        child: AlertDialog(
          title: Text('Alert'),
          content: Text('You typed ${_controller.text}'),
        ),
     );
   },
 ),
)
```
在此示例中，当用户单击`Submit`按钮时，dialog将显示在`TextField`中输入的文本。这是使用alertDialog显示警报消息的widget实现的 ，而[TextEditingController](https://docs.flutter.io/flutter/widgets/TextEditingController-class.html)的`text`属性访问了`TextField`输入的文本。

## 如何使用表单widget？
在Fltter中，使用[`Form`](https://docs.flutter.io/flutter/widgets/Form-class.html)widget，其中[`TextFormField`](https://docs.flutter.io/flutter/material/TextFormField-class.html)widget和Submit按钮作为子元素传递。`TextFormField`widget有一个名为`onSaved`[onSaved](https://docs.flutter.io/flutter/widgets/FormField/onSaved.html)的参数，它接受回调并在保存表单时执行。`FormState`对象用于保存、重置或验证每个`FormField`，这些`FormField`是此`Form`的后代。要获得`FormState`，可以使用`Form.of`和以表单为祖先的上下文，或者将`GlobalKey`传递给表单构造函数并调用`GlobalKey.currentState`。
```dart
final formKey = GlobalKey<FormState>();

...

Form(
  key:formKey,
  child: Column(
    children: <Widget>[
      TextFormField(
        validator: (value) => !value.contains('@') ? 'Not a valid email.' : null,
        onSaved: (val) => _email = val,
        decoration: const InputDecoration(
          hintText: 'Enter your email',
          labelText: 'Email',
        ),
      ),
      RaisedButton(
        onPressed: _submit,
        child: Text('Login'),
      ),
    ],
  ),
)
```

以下示例显示如何`Form.save()`和`formKey`（哪些是 `GlobalKey`）用于在提交时保存表单。
```dart
void _submit() {
  final form = formKey.currentState;
  if (form.validate()) {
    form.save();
    showDialog(
      context: context,
      child: AlertDialog(
        title: Text('Alert'),
        content: Text('Email: $_email, password: $_password'),
      )
    );
  }
}
```
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic25.gif" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic26.gif" width="350" title="iOS"/></div>
</div>

# 判断平台特定的代码
在构建跨平台应用程序时，您希望跨平台重用尽可能多的代码。但是，可能会出现这样的情况：根据操作系统的不同，代码是不同意义的。这需要通过声明特定平台来单独实现。

在React Native中，将使用以下实现：
```javascript
// React Native
if (Platform.OS === "ios") {
  return "iOS";
} else if (Platform.OS === "android") {
  return "android";
} else {
  return "not recognised";
}
```

在Flutter中，使用以下实现：
```dart
// Flutter
if (Theme.of(context).platform == TargetPlatform.iOS) {
  return "iOS";
} else if (Theme.of(context).platform == TargetPlatform.android) {
  return "android";
} else if (Theme.of(context).platform == TargetPlatform.fuchsia) {
  return "fuchsia";
} else {
  return "not recognised ";
}
```
# 调试
在运行您的应用程序之前，请使用`flutter analyze`。Flutter分析器（它是`dartanalyzer`工具的封装）检查你的代码并帮助识别可能出现的问题。如果您正在使用支持Flutter的IDE，则会自动执行此操作。

## 如何访问应用内开发人员菜单？
在React Native中，可以通过摇动设备来访问开发人员菜单：`⌘D`用于iOS模拟器，`⌘M`用于Android模拟器。

在Flutter中，如果您使用的是IDE，则可以使用IDE工具。如果您使用启动应用程序，`flutter run`也可以通过`h`在终端窗口中键入来访问菜单，或键入以下快捷方式：

| Action | 终端快捷键 | debug函数和属性 |
| ----- | -----| ----- |
| Widget hierarchy of the app | w | debugDumpApp() |
| Rendering tree of the app | t | debugDumpRenderTree() |
| Layers | L | debugDumpLayerTree() |
| Accessibility | S (traversal order) or U (inverse hit test order) | debugDumpSemantics() |
| To toggle the widget inspector | i | WidgetsApp. showWidgetInspectorOverride |
| To toggle the display of construction lines	 | p | debugPaintSizeEnabled |
| To simulate different operating systems | o | defaultTargetPlatform |
| To display the performance overlay | P | WidgetsApp. showPerformanceOverlay |
| To save a screenshot to flutter. png | s |  |
| To quit | q |  |

## 如何执行热重载？
Flutter的热重载功能可帮助您快速轻松地进行实践，构建用户界面，添加功能和修复错误。您不必每次重新编译应用程序，程序会自动进行热重载，以更新应用反映您的更改，并保留应用程序的当前状态。

在React Native中，快捷键是iOS模拟器的`⌘R`，并在Android模拟器上双击`R`

在Flutter中，如果您使用的是IntelliJ IDE或Android Studio，则可以选择全部保存（`⌘ + s`/ `ctrl + s`），也可以单击工具栏上的热重新加载按钮。如果使用命令行运行应用程序flutter run，请在"终端"窗口中键入`r`。您也可以通过`R`在终端窗口中输入完成重启。

## 我可以用什么工具在Flutter中调试我的应用程序？
当您需要调试Flutter应用程序时，可以使用多个选项和工具。

除了Flutter分析仪之外，该工具还可用于分析和调试您的Dart应用程序。如果您flutter run在终端中使用了您的应用程序 ，则可以打开打印到终端窗口的天文台URL的网页，例如：  http://127.0.0.1:8100/。

除了`flutter analyze`之外，[Dart Observatory](https://dart-lang.github.io/observatory/)也是一个用来分析和调试Dart应用程序的工具。如果您在终端中使用`flutter run`启动应用程序，则可以在输出到终端窗口的看到对应的 `Observatory` 的URL，例如：`http://127.0.0.1:8100/`。

Observatory支持分析，检查堆，观察已执行的代码行，调试内存泄漏和内存碎片。欲了解更多信息，请参阅[Observatory文件](https://dart-lang.github.io/observatory/)。当您下载并安装Dart SDK时，Observatory将免费提供。

如果您使用的是IDE，则可以使用IDE调试器调试应用程序。

如果您使用的是IntelliJ和Android Studio，则可以使用Flutter Inspector。Flutter Inspector使您更容易理解应用程序的呈现方式。它允许你：

- 将应用程序的UI结构视为widget树
- 在您的设备或模拟器上选择一个点，然后找到渲染这些像素的相应widget
- 查看各个widget的属性
- 快速识别布局问题并确定其原因

可以从"View > Tool Windows > Flutter Inspector"打开"Flutter Inspector"视图。内容仅在应用程序运行时显示。

若要检查特定widget，请在工具栏中选择"Toggle inspect mode"操作，然后在真机或模拟器上单击所需的widget。widget在应用程序的UI中被突出显示。您将在IntelliJ中的widget层次结构中看到widget，以及该widget的各个属性。

有关更多信息，请参阅 [调试flutter应用程序](https://flutter.io/debugging/)。

# 动画
设计良好的动画使UI感觉更直观，有助于完善的应用程序的外观和感受，并改善用户体验。Flutter的动画支持可以轻松实现简单和复杂的动画。Flutter SDK包含许多包含标准运动效果的Material Design Widget，您可以轻松地自定义这些效果来个性化应用程序。

在React Native中，Animated API用于创建动画。

在Flutter中，使用[Animation](https://docs.flutter.io/flutter/animation/Animation-class.html)类和[AnimationController](https://docs.flutter.io/flutter/animation/AnimationController-class.html)类。

`Animation`是一个抽象类，了解其当前值及其状态（completed 或者 dismissed）。`AnimationController`类允许您正向或反向播放动画，或者停止动画，并将动画设置为特定的值以自定义动画。

## 如何添加简单的淡入动画？
在下面的React Native示例中，`FadeInView`使用`Animated` API创建了一个动画组件。定义初始不透明度状态，结束状态和动画开始的持续时间。动画组件添加到`Animated`组件内部，透明度状态`fadeAnim`映射到我们要设置动画的`Text`组件的透明度，然后调用`start()`启动动画。
```javascript
// React Native
class FadeInView extends React.Component {
  state = {
    fadeAnim: new Animated.Value(0) // Initial value for opacity: 0
  };
  componentDidMount() {
    Animated.timing(this.state.fadeAnim, {
      toValue: 1,
      duration: 10000
    }).start();
  }
  render() {
    return (
      <Animated.View style={{...this.props.style, opacity: this.state.fadeAnim }} >
        {this.props.children}
      </Animated.View>
    );
  }
}
    ...
<FadeInView>
  <Text> Fading in </Text>
</FadeInView>
    ...
```

要在Flutter中创建相同的动画，请创建名为`controller`的[`AnimationController`](https://docs.flutter.io/flutter/animation/AnimationController-class.html)对象，并指定持续时间。默认情况下，`AnimationController`在给定的持续时间内线性生成范围从0.0到1.0的值。每当运行应用程序的设备准备显示新的帧时，动画控制器就会生成一个新值。通常情况下，这个速率大约是每秒60帧。

定义一个时，你必须传入一个vsync对象。存在vsync防止屏幕外动画消耗不必要的资源。您可以vsync通过添加 TickerProviderStateMixin到类定义来使用有状态的对象。一个AnimationController 需要TickerProvider，其使用配置vsync在构造参数。

定义`AnimationController`时，必须传入一个`vsync`对象。`vsync`的存在可以防止屏幕外动画消耗不必要的资源。通过将`TickerProviderStateMixin`添加到类定义中，可以将有状态对象用作`vsync`。一个动画控制器需要一个`TickerProvider`，它是使用构造函数上的`vsync`参数配置的。

A Tween描述了开始和结束值之间的插值或从输入范围到输出范围的映射。要通过Tween动画使用对象，请调用Tween对象的animate方法并将其传递给Animation您要修改的 对象。

[Tween](https://docs.flutter.io/flutter/animation/Tween-class.html)描述开始值和结束值之间的插值或从输入范围到输出范围的映射。若要在动画中使用`Tween`对象，请调用该对象的`animate`方法，并将要修改的`Animation`对象传递给该对象。

对于这个示例，使用了一个[`FadeTransition`](https://docs.flutter.io/flutter/widgets/FadeTransition-class.html)widget，并将该`opacity`属性映射到该`animation`对象。

要开始动画，请使用`controller.forward()`。其他操作也可以使用`controller`执行，例如`fling()`或`repeat()`。对于这个示例，该[FlutterLogo](https://docs.flutter.io/flutter/material/FlutterLogo-class.html)widget在`FadeTransition`widget内部使用。

```dart

// Flutter
import 'package:flutter/material.dart';

void main() {
  runApp(Center(child: LogoFade()));
}

class LogoFade extends StatefulWidget {
  _LogoFadeState createState() => _LogoFadeState();
}

class _LogoFadeState extends State<LogoFade> with TickerProviderStateMixin {
  Animation animation;
  AnimationController controller;

  initState() {
    super.initState();
    controller = AnimationController(
        duration: const Duration(milliseconds: 3000), vsync: this);
    final CurvedAnimation curve =
    CurvedAnimation(parent: controller, curve: Curves.easeIn);
    animation = Tween(begin: 0.0, end: 1.0).animate(curve);
    controller.forward();
  }

  Widget build(BuildContext context) {
    return FadeTransition(
      opacity: animation,
      child: Container(
        height: 300.0,
        width: 300.0,
        child: FlutterLogo(),
      ),
    );
  }

  dispose() {
    controller.dispose();
    super.dispose();
  }
}
```
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic27.gif" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic28.gif" width="350" title="iOS"/></div>
</div>

## 如何给卡片添加swipe动画？
在React Native中，swipe动画使用第三方库实现，例如`PanResponder`。

在Flutter中，要添加一个swipe动画，请使用[`Dismissible`](https://docs.flutter.io/flutter/widgets/Dismissible-class.html)widget并嵌套子widget。
```dart
child: Dismissible(
  key: key,
  onDismissed: (DismissDirection dir) {
    cards.removeLast();
  },
  child: Container(
    ...
  ),
),
```

<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic29.gif" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic30.gif" width="350" title="iOS"/></div>
</div>

# React Native 和 Flutter Widget 功能相等的组件

| React Native Component | Flutter Widget | 
| ---------------------- | -------------- |
| Button                 | [RaisedButton](https://docs.flutter.io/flutter/material/RaisedButton-class.html) |
| Button                 | [FlatButton](https://docs.flutter.io/flutter/material/FlatButton-class.html) |
| ScrollView             | [ListView](https://docs.flutter.io/flutter/widgets/ListView-class.html)|
| FlatList               | [ListView.builder()](https://docs.flutter.io/flutter/widgets/ListView/ListView.builder.html) |
| Image                  | [Image](https://facebook.github.io/react-native/docs/image.html) |
| Modal                  | [ModalRoute](https://docs.flutter.io/flutter/widgets/ModalRoute-class.html) |
| Activity Indicator     | [CircularProgressIndicator](https://docs.flutter.io/flutter/material/CircularProgressIndicator-class.html) |
| Activity Indicator     | [LinearProgressIndicator](https://docs.flutter.io/flutter/material/LinearProgressIndicator-class.html) |
| Refresh Control        | [RefreshIndicator](https://docs.flutter.io/flutter/material/RefreshIndicator-class.html) |
| View                   | [Container](https://docs.flutter.io/flutter/widgets/Container-class.html) |
| View                   | [Column](https://docs.flutter.io/flutter/widgets/Column-class.html) |
| View                   | [Row](https://docs.flutter.io/flutter/widgets/Row-class.html) |
| View                   | [Center](https://docs.flutter.io/flutter/widgets/Center-class.html) |
| View                   | [Padding](https://docs.flutter.io/flutter/widgets/Padding-class.html) |
| Touchable Opacity      | [GestureDetector](https://docs.flutter.io/flutter/widgets/GestureDetector-class.html) |
| Text Input             | [TextField](https://docs.flutter.io/flutter/material/TextField-class.html) |
| Text                   | [Text](https://docs.flutter.io/flutter/widgets/Text-class.html) |
| Switch                 | [Switch](https://docs.flutter.io/flutter/material/Switch-class.html) |
| Slider                 | [Slider](https://docs.flutter.io/flutter/material/Slider-class.html) |