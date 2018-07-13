---
title: 【flutter】flutter开发备忘
categories: flutter
date: 2018-07-12
tags:
  - flutter
---

本帖是为了开发做的一个备忘，随时不定期更新。

---------------------------------
#### 获取设备宽高
```dart
double width = MediaQuery.of(context).size.width;
double height = MediaQuery.of(context).size.height;
```

#### 开启android沉浸式
判断是否为安卓设备必须添加，要不然会导致ios崩溃。
```dart
// 输出渲染
void main() {
  runApp(App());
  if (Platform.isAndroid) {
    // 以下两行 设置android状态栏为透明的沉浸。写在组件渲染之后，是为了在渲染后进行set赋值，覆盖状态栏，写在渲染之前MaterialApp组件会覆盖掉这个值。
    SystemUiOverlayStyle systemUiOverlayStyle = SystemUiOverlayStyle(statusBarColor: Colors.transparent);
    SystemChrome.setSystemUIOverlayStyle(systemUiOverlayStyle);
  }
}

```

#### AppBar标题居中和去掉AppBar下侧阴影

```dart
Widget _tabbar(BuildContext context) {
    return AppBar(
      ...
      centerTitle: true, // 标题居中
      elevation: 0.0, // 去掉appbar下面的阴影
    );
  }
```

#### 有状态组件一定要使用createState

```dart
class HomeScreen extends StatefulWidget {
  const HomeScreen();

  @override
  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> with SingleTickerProviderStateMixin {
  // 使用TickerProvider 记得加 with SingleTickerProviderStateMixin 不然会报错
}
```

#### 使用ios的动画交互 和 MaterialApp 主题颜色配置不生效的问题
检查是否存在子级MaterialApp。如果有子级MaterialApp请删除，保证全局只有一个MaterialApp，theme就会生效

```dart
class App extends StatelessWidget {
  final store = createStore();
  // App();

  @override
  Widget build(BuildContext context) {
    return StoreProvider<AppState>(
      store: createStore(),
      child: MaterialApp(
        theme: ThemeData(
          platform: TargetPlatform.iOS, // 使用ios的界面动画方式
          primaryColor: Colors.red, // 应用主要部分的背景颜色（工具栏，标签栏等）
          accentColor: Colors.red, // 前景色：旋钮，文本，过度滚动边缘效果等
        ),
        routes: <String, WidgetBuilder>{
          '/': (BuildContext context) => HomeScreen(),
        },
      ),
    );
  }
}
```



