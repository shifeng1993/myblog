---
title: 【reaact-native】react-native 通过webview 桥接网页
categories: react-native
date: 2018-8-28
tags:
  - react-native
  - webView
---

webview 可以看做是react-native内置的浏览器，在rn使用过程中经常为了效率封装一些web的库，作展现使用。

下面是 react native webview 的文档

https://reactnative.cn/docs/webview/

因为具体的用法，文档内都有示例，下面说一些文档中间没有的bug处理方法。

# webView不能设置宽高
- 解决方案
做一个容器, 使用props传下去宽度和高度，分别在webview的父容器，和网页内部，标明宽高

```javascript
<View style={{flexDirection: 'row',width: this.props.width}}>
  <View style={{flex:1,height: this.props.height || 400}}>
    <WebView
      ...
      injectedJavaScript={renderChart(this.props)}
      ...
    />
  </View>
</View>
```

```javascript
const renderChart = (props) => {
  const height = `${props.height || 400}px`;
  const width = props.width ? `${props.width}px` : 'auto';
  return `
      document.getElementById('main').style.height = "${height}";
      document.getElementById('main').style.width = "${width}";
      ...
    `
}

export default renderChart;
```

# webView不能根据传参的变化进行刷新
- 解决方案
在webview所在的类中，使用类的 componentWillReceiveProps 周期进行监听props的变化，如果变化则刷新webview，注（此方法刷新会有闪烁）
```javascript
class Echarts extends Component {
  constructor(props) {
    super(props);
    this.state = {
      data: {}
    }
  }
  componentWillReceiveProps(nextProps) {
    if(nextProps.option !== this.props.option) {
      this.refs.chart.reload();
    }
  }

 ...
  
  render() {
   ...
    return (
      <View style={{flexDirection: 'row',width: this.props.width}}>
        <View style={{flex:1,height: this.props.height || 400}}>
          <WebView
            ref="chart"
          ...
          />
        </View>
      </View>
    );
  }
}
```

# webview在暗色系背景下，会呈现白色背景
- 解决方案
设置背景色为 透明 rgba(0,0,0,0)
```javascript
class Echarts extends Component {
  ...
  static defaultProps = {
    backgroundColor: '#00000000',
    ...
  } 
  
  render() {
    ...
    return (
      <View style={{flexDirection: 'row',width: this.props.width}}>
        <View style={{flex:1,height: this.props.height || 400}}>
          <WebView
            ref="chart"
            style={{backgroundColor:this.props.backgroundColor}} // 设置背景色透明，修复android闪白
            ...
          />
        </View>
      </View>
    );
  }
}
```

# webview在暗色系背景下，ios加载会出现白色遮罩闪烁
- 解决方案
设置renderLoading 为透明的空View
```javascript
class Echarts extends Component {
  ...
  static defaultProps = {
    backgroundColor: '#00000000',
    ...
  } 
  
  render() {
    ...
    return (
      <View style={{flexDirection: 'row',width: this.props.width}}>
        <View style={{flex:1,height: this.props.height || 400}}>
          <WebView
            ref="chart"
            originWhitelist={['*']}
            renderLoading={this.props.renderLoading || (()=><View style={{backgroundColor:this.props.backgroundColor}}/>)} // 设置空View，修复ioswebview闪白
            ...
          />
        </View>
      </View>
    );
  }
}
```

# webview在android release版本中不渲染。
原因是因为android 打包后静态资源会在系统绝对路径，而不是项目相对路径。
- 解决方案

对html 区别 ios 和 android 分别引用 ios还是使用require方式，android使用uri方式，并将html文件放到绝对路径下的资产目录
```javascript
class Echarts extends Component {
  ...

  static defaultProps = {
    ...
    isMap: false
  } 
  
  render() {
    const bmapSource = (Platform.OS == 'ios') ? require('./Bmap.html') : {'uri':'file:///android_asset/echarts/Bmap.html'} // 修复android release路径问题
    const indexSource = (Platform.OS == 'ios') ? require('./index.html') : {'uri':'file:///android_asset/echarts/index.html'} // 修复android release路径问题
    let source = this.props.isMap ? bmapSource : indexSource;
    return (
      <View style={{flexDirection: 'row',width: this.props.width}}>
        <View style={{flex:1,height: this.props.height || 400}}>
          <WebView
            ref="chart"
            ...
            source={source}
          />
        </View>
      </View>
    );
  }
}
```

# webview在ios release版本中不渲染。
原因是因为ios在0.56版本后新增了白名单列表，白名单默认是 `http://` 和 `https://` 所以本地html要进行通配，添加进白名单内
- 解决方案
添加白名单通配属性 originWhitelist={['*']}
```javascript
class Echarts extends Component {
  ...

  static defaultProps = {
    ...
    isMap: false
  } 
  
  render() {
    const bmapSource = (Platform.OS == 'ios') ? require('./Bmap.html') : {'uri':'file:///android_asset/echarts/Bmap.html'} // 修复android release路径问题
    const indexSource = (Platform.OS == 'ios') ? require('./index.html') : {'uri':'file:///android_asset/echarts/index.html'} // 修复android release路径问题
    let source = this.props.isMap ? bmapSource : indexSource;
    return (
      <View style={{flexDirection: 'row',width: this.props.width}}>
        <View style={{flex:1,height: this.props.height || 400}}>
          <WebView
            ref="chart"
            ...
            originWhitelist={['*']}
            source={source}
          />
        </View>
      </View>
    );
  }
}
```

# webview ios 出现滚动
- 解决方案
禁用scrollEnabled

```javascript
class Echarts extends Component {
  ...

  static defaultProps = {
    ...
    isMap: false
  } 
  
  render() { 
    ...
    return (
      <View style={{flexDirection: 'row',width: this.props.width}}>
        <View style={{flex:1,height: this.props.height || 400}}>
          <WebView
            ref="chart"
            ...
            scrollEnabled={false}
          />
        </View>
      </View>
    );
  }
}
```

# webview ios web页面不进行移动端视口的自适配
- 解决方案
在本地html中加入viewport meta标签
```html
<!DOCTYPE html>
<html>
  <head>
    <title>echarts</title>
    <meta http-equiv="content-type" content="text/html; charset=utf-8">
    <!-- webView ios适配 start -->
    <meta name="viewport" content="initial-scale=1, maximum-scale=3, minimum-scale=1, user-scalable=no"> 
    <!-- webView ios适配 end -->
    ...
  </head>
  
  <body>
    <div id="main" ></div>
  </body>
</html>
```

# 
