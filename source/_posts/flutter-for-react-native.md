---
title: 【flutter】用于React Native开发者的flutter参考文档
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
<div style="display:table-row;margin:0 auto">
<div style="display:table-cell"><img src="./pic3.png" width="350" title="android" /></div>
<div style="display:table-cell"><img src="./pic4.png" width="350" title="iOS"/></div>
</div>

在编写应用程序时，您将使用两种类型的weiget：[StatelessWidget](https://docs.flutter.io/flutter/widgets/StatelessWidget-class.html)或 [StatefulWidget](https://docs.flutter.io/flutter/widgets/StatefulWidget-class.html)。StatelessWidget听起来就像是一个没有状态的widget。一个StatelessWidget被创建一次，永远不会改变它的外观。StatefulWidget根据收到的数据或用户输入动态改变状态。

无状态和有状态weiget之间的重要区别在于，有状态weiget具有一个状态对象，用于存储状态数据并跨树重建执行，因此不会丢失。

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

在Flutter中，定义一个类来创建自定义窗口weiget，然后重用窗口weiget。您还可以定义和调用返回可重用weiget的`build`函数，如以下示例中的函数中所示。
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
在Flutter中，使用AssetImage窗口weiget构建方法中的类向应用程序添加静态图像。
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

该`Center`部件是如何控制布局的另一个例子。要将widget居中，将其包装在`Center`widget中，然后使用布局widget进行对齐，行，列和网格。这些布局widget没有自己的可视化表示。相反，他们唯一的目的是控制另一个widget布局的某些方面。要了解widget以某种方式呈现的原因，检查相邻窗口widget通常很有帮助。

有关更多信息，请参阅[Flutter技术概述](https://flutter.io/technical-overview/)。

有关Widgets包中的核心weiget的更多信息，请参阅[Flutter Basic Widgets](https://flutter.io/widgets/basics/)，[Flutter Widget 目录](https://flutter.io/widgets/)或 [Flutter Widget 索引](https://flutter.io/widgets/widgetindex/)。

# Views
## 什么相当于`View`容器？
在React Native中，`View`是一个支持布局的容器`Flexbox`，样式，触摸处理和可访问性控件。

在Flutter中，您可以使用Widgets库中的核心布局weiget，例如 [Container](https://docs.flutter.io/flutter/widgets/Container-class.html)，[Column](https://docs.flutter.io/flutter/widgets/Column-class.html)，[Row](https://docs.flutter.io/flutter/widgets/Row-class.html)和 [Center](https://docs.flutter.io/flutter/widgets/Center-class.html)。

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
[ListView](https://docs.flutter.io/flutter/widgets/ListView-class.html)是Flutter最常用的滚动weiget。默认的构造函数需要一个明确的子列表。[ListView](https://docs.flutter.io/flutter/widgets/ListView-class.html)对于少数weiget是最合适的。对于一个大的或无限的列表，使用`ListView.builder`，它根据需要构建它的子项，并只构建那些可见的子项。
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

以下示例显示了如何在绘制阶段使用`CustomPaint`weiget进行绘制。它实现了抽象类CustomPainter，并将其传递给CustomPaint的画家属性。CustomPaint子类必须实现`paint`和`shouldRepaint`方法。
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