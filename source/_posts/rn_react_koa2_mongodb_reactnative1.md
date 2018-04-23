---
title: 【RN+react+koa2+mongodb实战随笔】5.react-native项目实战（一）
categories: 项目实战
date: 2017-10-16
tags:
  - react-native
  - 移动端
  - 实战随笔
---
项目demo ：https://github.com/shifeng1993/rndemo1
rn版本： 0.48.3
# 本节内容
1. 初始化项目，合并入口，项目目录建设
2. 引入矢量图标
3. 路由管理，tabbar 以及 navigation

注：由于之前写过demo，所以有个排坑指南，
https://www.shifeng1993.com/2017/09/07/reactnative_bug/ 问题和bug可以去里面找找

# 架构搭建
## 初始化项目
这里需要注意的是在版本0.45开始，ios需要一些新加入的第三方编译库。
这些库在国内下载都非常困难（一般的翻墙工具都很难下载）。。
未来RN不同版本可能依赖不同版本的第三方编译库，具体所需库和版本请查看
https://github.com/facebook/react-native/blob/master/scripts/ios-install-third-party.sh 文件，
注意先把左上角的branch切换到对应的版本。

解决方法：
第三方依赖库百度盘链接： http://pan.baidu.com/s/1kVDUAZ9
下下来后请放置到 `~/.rncache` 目录
```bash
cd ~
mkdir .rncache
cd ~/.rncache
open .
```
打开文件夹后复制四个下载好的压缩包到 `~/.rncache`
全部复制完成后，就可以开始init新的RN项目了
```bash
react-native init yourProjectName
cd yourProjectName && react-native run-ios || react-native run-android
```

### 合并入口
这里有一个问题，ios和android是两个不同的入口文件，我们想要一次编写多端运行。

解决方案：

新建 `yourProjectName/app.js` 也可以不叫 app，起什么名随意。

将 `index.ios.js` 和 `index.ios.js` 添加如下代码：
```javasctipt
import {AppRegistry} from 'react-native';

import App from './app'

AppRegistry.registerComponent('rndemo1', () => App);
```

将 `app.js` 添加如下代码：
```javascript
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View
} from 'react-native';

export default class App extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Welcome to React Native!
        </Text>
        <Text style={styles.instructions}>
          To get started, edit index.ios.js
        </Text>
        <Text style={styles.instructions}>
          Press Cmd+R to reload,{'\n'}
          Cmd+D or shake for dev menu
        </Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  instructions: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
});

```


### 创建目录
现在app.js就是源码输出的主入口。
新建 `src/components` 作为无状态组件的目录，
新建 `src/containers` 作为关联状态的页面目录，
新建 `src/styles` 作为公共样式的目录，
新建 `src/utils` 作为存放工具的目录，
新建 `src/store` 作为redux的目录，
新建 `src/router` 作为路由的目录，
新建 `src/common` 作为基础配置导出的目录

## 引入矢量图标
引入字体依赖 `react-native-vector-icons` ，这里先引入icon的原因是后面的路由我们要用到。
以下是命令行：

```bash
cd yourProjectName
yarn add react-native-vector-icons
react-native link react-native-vector-icons
react-native run-ios || react-native run-android
```

需注意的是：如果项目之前启动过，重新编译一次，才可以看到成功引入的icon图标

### icon图标使用
引入
```javascript
import Icon from 'react-native-vector-icons/Ionicons';
```

使用
```javascript
<Icon name="ios-home" size={28} color={'#000'}/>
```
app.js
```javascript
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View
} from 'react-native';
import Icon from 'react-native-vector-icons/Ionicons';

export default class App extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Welcome to React Native!
        </Text>
        <Text style={styles.instructions}>
          To get started, edit index.ios.js
        </Text>
        <Text style={styles.instructions}>
          <Icon name="ios-home" size={28} color={'#000'}/>
        </Text>
      </View>
    );
  }
}
```
如果看到一个小房子，表示icon引入成功，这个库一共两个字体库。

http://ionicons.com/
==>  引入方式`import Icon from 'react-native-vector-icons/Ionicons';`

http://fontawesome.io/icons/
==>  引入方式`import Icon from 'react-native-vector-icons/FontAwesome';`

## 路由管理
使用 `react-navigation` 来管理路由。 这个库是0.44以上版本官方推荐，目前是1.0beta版本，
目前还有一些小bug。比如 `goBack()`

```bash
cd yourProjectName
yarn add react-native-vector-icons
react-native link react-native-vector-icons
react-native run-ios || react-native run-android
```

### react-navigation技巧
基础：http://www.jianshu.com/p/2f575cc35780
进阶：http://www.jianshu.com/p/b877115fff1b

### 本项目使用
使用路由必须新建几个页面，然后在路由引入进行分发，再将路由引入主入口注册
#### 新建页面
我们先设计四个页面，  Home,Cart,My,Login 分别对应 主页，购物车，我的，登录四个页面

新建主页 `src/containers/Home/index.js`
```javascript
import React, {Component} from 'react';
import {StyleSheet, Text, View} from 'react-native';

export default class Home extends Component {
  render() {
    return (
      <View>
        <Text>主页</Text>
      </View>
    )
  }
}
```

新建购物车 `src/containers/Cart/index.js`
```javascript
import React, {Component} from 'react';
import {StyleSheet, Text, View} from 'react-native';

export default class Cart extends Component {
  render() {
    return (
      <View>
        <Text>购物车</Text>
      </View>
    )
  }
}
```

新建我的 `src/containers/My/index.js`
```javascript
import React, {Component} from 'react';
import {StyleSheet, Text, View} from 'react-native';

export default class My extends Component {
  render() {
    return (
      <View>
        <Text>我的</Text>
      </View>
    )
  }
}
```

新建登录页 `src/containers/Login/index.js`
```javascript
import React, {Component} from 'react';
import {StyleSheet, Text, View} from 'react-native';

export default class Login extends Component {
  render() {
    return (
      <View>
        <Text>主页</Text>
      </View>
    )
  }
}
```

新建批量导出 `src/containers/index.js` 便于引入页面的地方进行解构赋值
```javascript
import Home from './Home';
import Cart from './Cart'
import My from './My'
import Login from './common/Login'
export {
  Home,
  Cart,
  My,
  Login
};
```

#### 新建路由
新建 `src/router/index.js`
```javascript
import React, {Component} from 'react';
import {Dimensions, Platform} from 'react-native';
import {StackNavigator, TabNavigator} from 'react-navigation';
import Icon from 'react-native-vector-icons/Ionicons';

// 引入页面容器
import {
  Home,
  Cart,
  My,
  Login
} from '../containers';

// 设置常量
const {height, width} = Dimensions.get('window');

const tabbar = TabNavigator({
  Home: {
    screen: Home,
    navigationOptions: {
      tabBarLabel: '主页',
      tabBarIcon: ({tintColor}) => (<Icon name="ios-home" size={28} color={tintColor}/>)
    }
  },
  Cart: {
    screen: Cart,
    navigationOptions: {
      tabBarLabel: '购物车',
      tabBarIcon: ({tintColor}) => (<Icon name="ios-cart" size={28} color={tintColor}/>)
    }
  },
  My: {
    screen: My,
    navigationOptions: {
      tabBarLabel: '我的',
      tabBarIcon: ({tintColor}) => (<Icon name="ios-person" size={28} color={tintColor}/>)
    }
  }
}, {
  tabBarPosition: 'bottom',
  swipeEnabled: false,
  animationEnabled: false,
  lazy: true,
  backBehavior: 'initialRoute',
  tabBarOptions: {
    style: {
      height: (Platform.OS === 'ios')
        ? width / 8
        : width / 7 - 5,
      backgroundColor: '#fff',
      borderTopWidth: 0.5,
      borderColor: '#e4e4e4'
    },
    labelStyle: {
      fontSize: width / 35
    },
    iconStyle: {
      height: width / 20
    },
    indicatorStyle: {
      height: 0
    },
    scrollEnabled: false,
    activeBackgroundColor: '#fff',
    activeTintColor: '#ff6600',
    inactiveBackgroundColor: '#fff',
    inactiveTintColor: '#444444',
    showLabel: true,
    showIcon: true
  }
});

const AppNavigator = StackNavigator({
  Main: {
    screen: tabbar,
    navigationOptions: {
      header: null
    }
  },
  Login: {
    screen: Login,
    navigationOptions: {
      header: null
    }
  }
});

export default AppNavigator;
```

#### 主入口引入路由
导出路由文件到 `app.js`，并在主入口的render函数中引入。
```javascript
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View
} from 'react-native';
import Icon from 'react-native-vector-icons/Ionicons';
import AppNavigator from './src/router';

export default class App extends Component {
  render() {
    return (
      <AppNavigator></AppNavigator>
    );
  }
}
```
