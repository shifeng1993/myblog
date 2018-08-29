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
做一个容器, 使用props传下去宽度和高度，分别在webview的父容器，和网页内部，设置宽高

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

# webView android 内嵌html 外联js失效
因为android的web使用的是okhttp发送请求的，okhttp会默认将不安全的https过滤掉
- 解决方案
使用安全的 https:// 的外联，或者就使用内联
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
    <script type="text/javascript" src="https://www.echartsjs.com/gallery/vendors/echarts/extension/bmap.min.js"></script>
    <script type="text/javascript" src="https://api.map.baidu.com/api?v=2.0&ak=ZUONbpqGBsYGXNIYHicvbAbM"></script>
  </head>
  
  <body>
    <div id="main" ></div>
  </body>
</html>
```

# rn 引入web库报错 没找到 userAgent 是个未定义
这是因为在rn中没有浏览器的一些信息。当然，在webview内嵌的网页中是没问题的
- 解决方案
找到对应判断的代码，将其注释掉
```javascript
else {
    // env = detect(navigator.userAgent);
    env = {
        browser: {},
        os: {},
        node: true,
        worker: false,
        // Assume canvas is supported
        canvasSupported: true,
        svgSupported: true
    };
}
```

# rn与内嵌网页 用webView桥接
- 解决方案
在 `injectedJavaScript` 内事先添加对 `message` 的监听事件，
然后使用webView props属性 `onMessage` 和 实例方法 `postMessage` 进行一收一发。
需要注意的是，收发，都是收发字符串类型的数据，可以使用json字符串

```javascript
const renderChart = (props) => {
  const height = `${props.height || 400}px`;
  const width = props.width ? `${props.width}px` : 'auto';
  const backgroundColor = props.backgroundColor;
  return `
      document.getElementById('main').style.height = "${height}";
      document.getElementById('main').style.width = "${width}";
      document.getElementById('main').style.backgroundColor = "${backgroundColor}";
      var myChart = echarts.init(document.getElementById('main'));
      myChart.setOption(${JSON.stringify(props.option)});
      window.document.addEventListener('message', function(e) {
        var req = JSON.parse(e.data);
        switch (req.types) {
          case "SET_OPTION":
            myChart.setOption(req.payload.option,req.payload.notMerge,req.payload.lazyUpate);
            break;
          case "GET_IMAGE":
            var base64 = myChart.getDataURL();
            window.postMessage(JSON.stringify({"types":"GET_IMAGE","payload": base64}));
            break;
          case "CLEAR":
            myChart.clear();
            break;
          default:
            break;
        }
      });
      myChart.on('click', function(params) {
        var seen = [];
        var paramsString = JSON.stringify(params, function(key, val) {
          if (val != null && typeof val == "object") {
            if (seen.indexOf(val) >= 0) {
              return;
            }
            seen.push(val);
          }
          return val;
        });
        window.postMessage(JSON.stringify({"types":"ON_PRESS","payload": paramsString}));
      });
    `
}

export default renderChart;
```


```javascript
class Echarts extends Component {
  ...
  
  render() {
    ...
    return (
      <View style={{flexDirection: 'row',width: this.props.width}}>
        <View style={{flex:1,height: this.props.height || 400}}>
          <WebView
            ref="chart"
            ...
            onMessage={this._handleMessage}
            injectedJavaScript={renderChart(this.props)}
            ...
          />
        </View>
      </View>
    );
  }

  _handleMessage = (e) => {
    if (!e) return null;
    const data = JSON.parse(e.nativeEvent.data)
    switch (data.types) {
      case 'ON_PRESS':
        this.props.onPress(JSON.parse(data.payload))
        break;
      case 'GET_IMAGE':
        this.setState({data})
        break;
      default:
        break;
    }
  };

  _postMessage(data){
    this.refs.chart.postMessage(JSON.stringify(data));
  }

  setOption = (option,notMerge, lazyUpdate) => {
    let data = {
      types: 'SET_OPTION',
      payload: {
        option:option,
        notMerge:notMerge || false,
        lazyUpdate:lazyUpdate || false
      }
    }
    this._postMessage(data);
  }
  
  clear=()=>{
    let data = {
      types: 'CLEAR'
    }
    this._postMessage(data);
  }

  getImage = (callback) => {
    let data = {
      types: 'GET_IMAGE',
      payload: null
    }
    this._postMessage(data);
    setTimeout(() => {
      if(this.state.data.types === 'GET_IMAGE') {
        callback(this.state.data)
      } else {
        callback(null)
      }
    }, 500);
  }  
}
```

# postMessage 传递对象 JSON.stringify 后函数对象变为空字符串
- 解决方案
对function进行特殊处理
```javascript
const toString = (obj) => {
  let result = JSON.stringify(obj, function(key, val) {
        // 对function进行特殊处理
        if (typeof val === 'function') {
            return `~ha~${val}~ha~`;
        }
        return val;
    });
    // 再进行还原
    do {
        result = result.replace('\"~ha~', '').replace('~ha~\"', '').replace(/\\n/g, '').replace(/\\\"/g,"\"");//最后一个replace将release模式中莫名生成的\"转换成"
    } while (result.indexOf('~ha~') >= 0);
    return result;
}
const renderChart = (props) => {
  const height = `${props.height || 400}px`;
  const width = props.width ? `${props.width}px` : 'auto';
  const backgroundColor = props.backgroundColor;
  return `
      document.getElementById('main').style.height = "${height}";
      document.getElementById('main').style.width = "${width}";
      document.getElementById('main').style.backgroundColor = "${backgroundColor}";
      var myChart = echarts.init(document.getElementById('main'));
      myChart.setOption(${toString(props.option)});
      window.document.addEventListener('message', function(e) {
        var req = JSON.parse(e.data);
        switch (req.types) {
          case "SET_OPTION":
            myChart.setOption(req.payload.option,req.payload.notMerge,req.payload.lazyUpate);
            break;
          case "GET_IMAGE":
            var base64 = myChart.getDataURL();
            window.postMessage(JSON.stringify({"types":"GET_IMAGE","payload": base64}));
            break;
          case "CLEAR":
            myChart.clear();
            break;
          default:
            break;
        }
      });
      myChart.on('click', function(params) {
        var seen = [];
        var paramsString = JSON.stringify(params, function(key, val) {
          if (val != null && typeof val == "object") {
            if (seen.indexOf(val) >= 0) {
              return;
            }
            seen.push(val);
          }
          return val;
        });
        window.postMessage(JSON.stringify({"types":"ON_PRESS","payload": paramsString}));
      });
    `
}

export default renderChart;
```
