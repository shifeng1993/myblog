---
title: 【react分享】2016年底学习react经验谈
categories: react
date: 2016-12-05
tags:
  - react
  - 前端
---
马上2017年了，年底努了一把力，把react以及react生态圈终于是入了个门。从11月底开始零零散散的学了一个月，最大的感触就是得`上手多练，多请教人。`

分享一下一路走过来的经历吧，希望对新入坑的同学会有一些帮助。至于老司机们，不愿意看的可以无视，愿意看的各位老哥，可以留下意见或和建议。我会努力的改进的。

## 第一个阶段
掌握react语法，等基础知识。最好的中文react入门教程，首推阮一峰老哥的react系列教程，跟着走一遍，包括react组件生命周期，react基本语法以及props，state用法。阮一峰老哥react系列教程

链接[http://www.ruanyifeng.com/blog/2015/03/react.html](http://www.ruanyifeng.com/blog/2015/03/react.html "http://www.ruanyifeng.com/blog/2015/03/react.html")

看完这些会有一个严重的问题，就是阮一峰老哥用的是便于入门和理解的es5代码。但是现在工程级别的demo都是es6，单纯看es6文档太费时间，又找不到问题所在，怎么办？这里推荐一个视频。

链接: [https://pan.baidu.com/s/1bo2nJxt](https://pan.baidu.com/s/1bo2nJxt "https://pan.baidu.com/s/1bo2nJxt") 密码: `dgd2`

这个视频主要是讲react-native中es5和es6代码的区别，但是虽然是react-native，也非常好的诠释了react代码的区别，而且能为以后的学习打下基础。

## 第二个阶段
掌握webpack以及简单语法练习，这里推荐慕课网的react画廊应用实战教程。

链接：[http://www.imooc.com/learn/507](http://www.imooc.com/learn/507 "http://www.imooc.com/learn/507")

这个实战教程非常好的展示了工程级别webpack的配置，以及react的具体代码写法，加上之前看过的es5与es6代码区别的视频。就可以更好的理解react以及webpack的用法。
## 第三个阶段
开始实战以及熟悉react生态周边，阮一峰老哥说过，学习react并不只是单纯的学习react，包括一整套周边和技术栈都要进行学习。
### react-router
关于react-router视频，几乎没找到，因为太简单了，看文档就可以分分钟解决

链接：[https://react-guide.github.io/react-router-cn/](https://react-guide.github.io/react-router-cn/ "https://react-guide.github.io/react-router-cn/")


在这个阶段我遇到了一些不深不浅的坑。第一个是map()做列表渲染的时候，会有一个关于key的报错，仔细搜了一下，有教程来详解

[http://taobaofed.org/blog/2016/08/24/react-key/](http://taobaofed.org/blog/2016/08/24/react-key/ "http://taobaofed.org/blog/2016/08/24/react-key/")


第二个就是关于 history这个属性，是必须要加的，这个可以看react-router文档。


### redux
这个东西，真的是，又爱又恨，函数式表达听起来非常牛逼。但是一直没有合适的教程，学了几乎半个月。。。才勉强看懂。要理解的主要是数据的流向以及action，reducers，store 这三大部分的用处。
这里可以先看阮一峰老哥的redux入门教程，大概有个概念。

链接：[http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html "http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html")

看完阮一峰老哥的教程之后，推荐的是在一个偶然的机会从优酷网找到的视频教程，发现看了好几遍很好理解，然后从大神的git上fork下来demo，顺了一遍，之后加进工程项目里面，忽然变得很好理解。redux最后一道坎就被我迈过去了。

[http://i.youku.com/i/UNjEzMjQzOTgw/videos](http://i.youku.com/i/UNjEzMjQzOTgw/videos "http://i.youku.com/i/UNjEzMjQzOTgw/videos")  这位大神的优酷连接
[https://github.com/shifeng1993/fast-react-redux-demo](https://github.com/shifeng1993/fast-react-redux-demo "https://github.com/shifeng1993/fast-react-redux-demo")   代码案例连接