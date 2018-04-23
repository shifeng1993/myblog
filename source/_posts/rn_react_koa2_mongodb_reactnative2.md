---
title: 【RN+react+koa2+mongodb实战随笔】6.react-native项目实战（二）
categories: 项目实战
date: 2017-10-26
tags:
  - react-native
  - 移动端
  - 实战随笔
---
项目demo ：https://github.com/shifeng1993/rndemo2
rn版本： 0.48.3
# 本节内容
1. 状态管理(redux)
2. 请求库封装 (axios)

# 未完成内容
3. 公共样式管理，与兼容 (ios&android)
4. 兼容性基础组件的封装 (baseView)

注：由于之前写过demo，所以有个排坑指南，
https://www.shifeng1993.com/2017/09/07/reactnative_bug/ 问题和bug可以去里面找找

# 架构搭建
因为请求库属于工具的一部分，而状态管理会用到请求库，所以先封装请求库。
## 请求库封装 (axios)
注：封装请求库尽量根据实际项目情况来。例如查询，批量修改，批量删除，等非常规操作。

添加axios 和querystring依赖
`yarn add axios && yarn add querystring`

新建 `src/common/httpBaseConfig.js`
```javascript
export default httpBaseConfig = {
  baseUrl: 'http://192.168.212.60',
  port: '3000',
  prefix: ''
}
```

新建 `src/utils/http/index.js`
```javascript
import axios from 'axios';
import qs from 'querystring';
import baseConfig from '../../common/httpBaseConfig.js';
axios.defaults.baseURL = baseConfig.baseUrl + ':' + baseConfig.port + baseConfig.prefix;

// fetch感觉略麻烦，不清爽，直接引了个axios，用es7写的。
export default class http {
  static async get(url, params) {
    /**
     * params{
     *  goods：id，
     *  name：string
     * } ==> ?goods=id&name=string
     */
    try {
      let query = await qs.stringify(params)
      let res = null;
      if (!params) {
        res = await axios.get(url)
      } else {
        res = await axios.get(url + '?' + query)
      }
      return res
    } catch (error) {
      return error
    }
  }
  static async post(url, params) {
    try {
      let res = await axios.post(url, params)
      return res
    } catch (error) {
      return error
    }
  }
  static async patch(url, params) {
    try {
      let res = await axios.patch(url, params)
      return res
    } catch (error) {
      return error
    }
  }
  static async put(url, params) {
    try {
      let res = await axios.put(url, params)
      return res
    } catch (error) {
      return error
    }
  }
  static async delete(url, params) {
    /**
     * params默认为数组
     */
    try {
      let res = await axios.post(url, params)
      return res
    } catch (error) {
      return error
    }
  }
}
```

引入到入口文件并注册为全局 `app.js`
```javascript
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View
} from 'react-native';
import Icon from 'react-native-vector-icons/Ionicons';
import AppNavigator from './src/router';

// 引入http 并注册到全局
import http from './src/utils/http'
global.http = http

export default class App extends Component {
  render() {
    return (
      <AppNavigator></AppNavigator>
    );
  }
}
```
在Home页面render函数中打印全局http，如果不为未定义，则引入成功


## 状态管理(redux)
react-native集成redux，0.44以后得带上react-navigation路由参数；
然而路由参数不能像B端的react-router-redux一样同步。
所以必须得手写一个函数进行同步，还好官方出了这个，虽然有些麻烦，但是还是可以搞的。

### 首先，安装redux依赖
`yarn add redux && yarn add react-redux && yarn add redux-thunk`

### 添加store
新建 `src/store/index.js` 存放store
```javascript
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';

import getReducers from './reducers';

//thunk为异步action的一个中间件。少了会报个异步的错误。
export default function getStore(navReducer) {
    return createStore(
        getReducers(navReducer),
        undefined,
        applyMiddleware(thunk)
    );
}
```

### 添加初始state
新建 `src/store/states/goods/index.js`
```javascript
export default {
  // 商品数组
  goodsList: {
    data:[]
  },
  goods: {}
};
```

### 添加reducer 

新建 `src/store/reducers/index.js` 合并单个类的reducer
```javascript
import { combineReducers } from 'redux';

import goods from './goods'

const reducers = {
  goods
};

//和导航相关的reducer通过从调用出传递进来
export default function getReducers(navReducer) {
    return combineReducers({
        ...reducers,
        nav: navReducer
    });
}
```

新建商品类的reducer `src/store/reducers/goods/index.js`
```javascript
import * as types from '../../constants/actionTypes';
import goodsState from '../../states/goods';

export default function goods(state = goodsState, action) {
  switch (action.type) {
    case types.SET_GOODSLIST:
      return {
        ...state,
        goodsList: action.data
      };
    case types.SET_GOODS:
      return {
        ...state,
        goods: action.data
      };
    default:
      return state;
  }
}
```

### 添加actions
#### 添加actionTypes
新建action类型 `src/store/constants/actionTypes.js` 
```javascript
/* ******** 用户类 *************/
// 商品列表动作
export const SET_GOODSLIST = 'SET_GOODSLIST';

// 单个商品动作
export const SET_GOODS = 'SET_GOODS';

```
#### 添加action
新建商品类动作 `src/store/actions/goods/index.js` 
```javascript
import * as types from '../../constants/actionTypes';

/*设置常量*/
// 每页个数
const PAGE_SIZE = 10

const fetchGoodsList = (currentPage) => {
  let params = {}
  params.currentPage = currentPage;
  params.pageSize = PAGE_SIZE;
  return http.get('/goods/getGoodsList', params)
}

export const getGoodsList = (currentPage) => {
  return (dispatch, getState) => {
    return fetchGoodsList(currentPage).then((res) => {
      if (res.status === 200) {
        let goodsList = {}
        if (currentPage === 1) {
          goodsList = res.data
        } else {
          goodsList.data = getState().goods.goodsList.data.concat(res.data.data)
          goodsList.pageTotal = parseInt(res.data.pageTotal)
          goodsList.currentPage = parseInt(res.data.currentPage)
          goodsList.pageSize = parseInt(res.data.pageSize)
        }
        dispatch(setGoodsList(goodsList));
      } else {
        alert(res)
      }
    });
  };
};

export const setGoodsList = (data) => {
  return {type: types.SET_GOODSLIST, data};
};

```

### 将store加载到主入口处
`app.js`

```javascript
import React, {Component} from "react";
import {Provider, connect} from "react-redux";
import {AsyncStorage, BackHandler} from "react-native";
import {NavigationActions, addNavigationHelpers} from "react-navigation";
import Ionicons from 'react-native-vector-icons/Ionicons';
import Faicon from 'react-native-vector-icons/FontAwesome';

import getStore from "./src/store";
import AppNavigator from './src/router';
import http from './src/utils/http'

// 注册全局变量
global.http = http
global.storage = AsyncStorage;
global.Ionicon = Ionicons
global.Faicon = Faicon

const navReducer = (state, action) => {
  const newState = AppNavigator
    .router
    .getStateForAction(action, state);
  return newState || state;
};

const mapStateToProps = (state) => ({nav: state.nav});

class App extends Component {
  /*处理安卓硬件返回按键 开始*/
  componentDidMount() {
    BackHandler.addEventListener("hardwareBackPress", this.onBackPress);
  }
  componentWillUnmount() {
    BackHandler.removeEventListener("hardwareBackPress", this.onBackPress);
  }
  onBackPress = () => {
    const {dispatch, nav} = this.props;
    if (nav.index === 0) {
      return false;
    }
    dispatch(NavigationActions.back());
    return true;
  };
  /*处理安卓硬件返回按键 结束*/
  
  render() {
    console.log(this.props.nav)
    const {dispatch, nav} = this.props;
    const navigation = addNavigationHelpers({dispatch, state: nav});
    return (<AppNavigator navigation={navigation}/>);
  }
}

const AppWithNavigationState = connect(mapStateToProps)(App);

const store = getStore(navReducer);

export default function Root() {
  return (
    <Provider store={store}>
      <AppWithNavigationState/>
    </Provider>
  );
}

```
### 测试store
打开js debugging，进行tabbar切换，如果有路由信息打印出来，redux则添加成功
注： console.log 在app.js 的第46行，不需要则删除

### 测试action
注：如按上述写那么还需要启动mongo-server服务。
文档： https://www.shifeng1993.com/2017/08/21/rn_react_koa2_mongodb_api1/
项目： https://github.com/shifeng1993/mongo-server

修改src/containers/Home/index.js
```javascript
import React, {Component} from 'react';
import {StyleSheet, Text, View} from 'react-native';
import {bindActionCreators} from 'redux'
import {connect} from 'react-redux'

// 引入action
import * as goodsAction from '../../store/actions/goods';

// 常量设置
const INITIAL_PAGE = 1;

class Home extends Component {
  constructor(props) {
    super(props);
  }
  componentWillMount() {
    const {actions} = this.props;
    actions.getGoodsList(INITIAL_PAGE)
  }
  render() {
    console.log(this.props.goodsList.data)
    return (
      <View>
        <Text>主页</Text>
      </View>
    );
  }
}

// 同步store中的state，状态改变，实时更新
const mapStateToProps = state => {
  return {goodsList: state.goods.goodsList};
}
// 同步store中的action
const mapDispatchToProps = (dispatch) => ({
  actions: bindActionCreators(goodsAction, dispatch),
  dispatch: dispatch
});

export default connect(mapStateToProps, mapDispatchToProps)(Home)
```

刷新应用，这时如果有console.log打印出来空数组或者10个商品的数组，则代表action添加成功。

如果mongo-server没有添加商品也是为空数组。
