---
title: 【react-native】RN 排坑指南
categories: 移动端
date: 2017-11-05
tags:
  - react-native
  - bug
  - 移动端
---
- react-native version: 0.48.2

#### 不能改原生应用名称，但可以移动对应的源码
- 解决方案：
  新建一个rn项目把源码和package.json移过去。

#### ios和android样式兼容问题
- 解决方案：
  使用 `Platform.OS` 来判断 `ios` 和 `android` ，再利用变量载入样式。可根据需求封装基础无状态组件`

#### 一个源码两个入口
- 解决方案：
  需要同时在 `ios` 和 `android` 主入口进行引入，注册

#### 矢量icon使用npm link 不行
- 解决方案：
  使用 `react-native link react-native-vector-icons`
  icon第三方组件 https://github.com/oblador/react-native-vector-icons

#### tabbarios不兼容安卓
- 解决方案：
  使用第三方组件 `react-native-tab-navigator`

#### 安卓在输入状态时候的键盘会顶起tabbar组件react-native-tab-navigator
- 解决方案：
  将 `/android/app/src/main/AndroidManifest.xml` 打开
  找到 `android:windowSoftInputMode="adjustResize"` 属性
  替换为 `android:windowSoftInputMode="stateAlwaysHidden|adjustPan"`

#### textinput安卓会比ios多出一个padding，和一个默认的底边框
- 解决方案：
  设置 `style`为`padding:0`
  给textinput添加属性 `underlineColorAndroid="transparent"` 去除底边框

#### ios和安卓的状态栏兼容，ios默认为沉浸式，安卓则不是。
- 解决方案：
  封装一个statusbar基础组件
  ```javascript
  import React, {Component} from 'react';
  import {
    View,
    StatusBar,
    Platform
  } from 'react-native';

  class Status extends Component {
    render() {
      const {backgroundColor, barStyle} = this.props
      if (Platform.OS === 'ios') {
        return (
          <View style = {{ height: 20,backgroundColor: backgroundColor}}>
            <StatusBar barStyle={barStyle}/ >
          </View>
        )
      } else if (Platform.OS === 'android') {
        return(
          <View style = {{ height: StatusBar.currentHeight,backgroundColor: backgroundColor}}>
            <StatusBar backgroundColor={'rgba(0, 0, 0, 0.0)'} barStyle={barStyle} translucent={true}/ >
          </View>
        )
      }
    }
  }

  export default Status;
  ```
  注：
  开启安卓沉浸式状态栏 `translucent={true}`
  透明的背景色 `backgroundColor={'rgba(0, 0, 0, 0.0)'}`

#### no bundle url present 错误
- 解决方案：
  执行 `npm install && react-native run-ios`

#### 安卓请求不了数据：
- 解决方案：
  将请求地址 `127.0.0.1` or `localhost`
  改为本机局域网或广域网IP
  例如 `api.haise.com` or` 192.168.1.233`

#### flatlist 出现上拉加载的时候一直转圈。
- 解决方案：
  原因：没有给容器高度
  https://www.questarter.com/q/flatlist-39-s-onendreached-not-fire-unless-setting-height-explicitly-in-react-native-27_43444690.html

#### flatlist属性onEndReachedThreshold大于0.1安卓会出现多次加载onEndReached内方法
- 解决方案：
  无法解决，只能调整小于0.1

#### flatlist正在加载的时候，用户的滑动速度超过了渲染速度回看到空白的一大片
- 解决方案：
  已知是官方组件问题，无法解决，等待版本更新

#### app样式因为设备的自动旋转，出现样式bug
- 解决方案：
  禁止屏幕旋转，可以用原生写也可以引入第三方组件
  这里为了代码的可移植性。故使用第三方组件，`react-native-orientation`
  使用 `react-native link react-native-orientation` 加载到native中，即可引用和调用api
  https://github.com/yamill/react-native-orientation

#### 写完需要写Navigator导航，发现官方已经把原来的废了，改成官推 react-navigation，
- 解决方案：
  重构入口文件。
  ```javascript
  import React, {Component} from 'react';
  import {StyleSheet, Dimensions,Platform} from 'react-native';
  import {StackNavigator, TabNavigator} from 'react-navigation';
  import Icon from 'react-native-vector-icons/Ionicons';
  import storage from './utils/storage'

  // 引入页面容器
  import {Home, Cart, My, Login} from './containers';

  // 设置常量
  const {height, width} = Dimensions.get('window');

  const tabbar = TabNavigator({
    Home: {
      screen: Home,
      navigationOptions: {
        tabBarLabel: '主页',
        tabBarIcon: ({ tintColor }) => (
          <Icon name="ios-home" size={28} color={tintColor}/>
        )
      }
    },
    Cart: {
      screen: Cart,
      navigationOptions: {
        tabBarLabel: '购物车',
        tabBarIcon: ({ tintColor }) => (
          <Icon name="ios-cart" size={28} color={tintColor}/>
        )
      }
    },
    My: {
      screen: My,
      navigationOptions: {
        tabBarLabel: '我的',
        tabBarIcon: ({ tintColor }) => (
          <Icon name="ios-person" size={28} color={tintColor}/>
        )
      }
    }
  }, {
    tabBarPosition: 'bottom',
    swipeEnabled: true,
    animationEnabled: false,
    tabBarOptions: {
      style: {
        height: (Platform.OS === 'ios') ? width/8 : width/7-5,
        backgroundColor: '#fff',
        borderTopWidth: 0.5,
        borderColor: '#e4e4e4'
      },
      labelStyle: {
        fontSize: width/35
      },
      iconStyle: {
        height: width/20
      },
      indicatorStyle: {
        height:0
      },
      activeBackgroundColor: '#fff',
      activeTintColor: '#ff6600',
      inactiveBackgroundColor: '#fff',
      inactiveTintColor: '#444444',
      showLabel: true,
      showIcon: true
    }
  });

  const App = StackNavigator({
    Home: {
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

  export default App;
  ```
#### react-navigation 中 StackNavigator头部实际项目想重写。
- 解决方案：
添加navigationOptions: {header: null}，
react-navigation 使用技巧： http://www.jianshu.com/p/2f575cc35780 

#### 基础组件Text在android上出现不会垂直居中的情况
- 解决方案：
includeFontPadding bool
Android在默认情况下会为文字额外保留一些padding，以便留出空间摆放上标或是下标的文字。
对于某些字体来说，这些额外的padding可能会导致文字难以垂直居中。
如果你把textAlignVertical设置为center之后，文字看起来依然不在正中间，
那么可以尝试设置设置`includeFontPadding: false`

#### react-navigation 与 redux集成的问题，
- 解决方案：
http://www.jianshu.com/p/8063ba79d08f  比react少一个react-router-redux，路由状态同步得自己写函数进去。
以上链接需修改地方为 store 中`react-promise ` 中间件 更改为 `redux-thunk` 中间件 

#### react-navigation关于快速点击会导致多次跳转的问题
- 解决方案：
`修改node_modules/react-navigation目录下，scr文件夹中的addNavigationHelpers.js文件`,可直接替换成下面的文本
```javascript
export default function<S: *>(navigation: NavigationProp<S, NavigationAction>) {
  // 添加点击判断
  let debounce = true;
  return {
      ...navigation,
      goBack: (key?: ?string): boolean =>
          navigation.dispatch(
              NavigationActions.back({
                  key: key === undefined ? navigation.state.key : key,
              }),
          ),
      navigate: (routeName: string,
                 params?: NavigationParams,
                 action?: NavigationAction,): boolean => {
          if (debounce) {
              debounce = false;
              navigation.dispatch(
                  NavigationActions.navigate({
                      routeName,
                      params,
                      action,
                  }),
              );
              setTimeout(
                  () => {
                      debounce = true;
                  },
              500,
              );
              return true;
          }
          return false;
      },
    /**
     * For updating current route params. For example the nav bar title and
     * buttons are based on the route params.
     * This means `setParams` can be used to update nav bar for example.
     */
    setParams: (params: NavigationParams): boolean =>
      navigation.dispatch(
        NavigationActions.setParams({
          params,
          key: navigation.state.key,
        }),
      ),
  };
}
```
#### flatlist相关问题的暂时解决方法，
- 解决方案：
使用第三方组件 `react-native-ultimate-listview` 3.2.0+版本
这个第三方组件很好的掩盖了下拉速度过快会变空白的问题，至于底层问题等待官方修复。

#### ios9以上不能显示出公网图片不能发出公网请求的问题，
- 解决方案：
1. 全站https化，启用nginx做反向代理。
    先将node服务或tomcat服务代理在https443端口。
    再将 http 80端口301重定向到443端口、后面记得加动态参数。
2. 禁用https
    打开

#### react-navigation 中 tabNavigation切换会有几百毫秒的延迟，。
- 解决方案：
在debug版本中会有几百毫秒的延迟，在创建了release版本后会自动消失。

#### 第三方图片选择组件react-native-image-picker link成功后图片或者照相没有出来，或者应用崩溃
- 解决方案：
是应为没有请求相对应的权限。
android：
打开 `android/app/src/main/AndroidManifest.xml`
添加：
```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
ios10+:
打开 `ios/yourProject/Info.plist`
在根的 `<dict></dict>` 内添加：
```xml
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSPhotoLibraryUsageDescription</key>
<string></string>
```

#### 关于代码重构，有时间就去做把，毕竟如果排坑的话，代码最后会变成乱糟糟一团

#### 未完，待续，不定时更新。