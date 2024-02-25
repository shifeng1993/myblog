---
title: 【RN+react+koa2+mongodb实战随笔】3.API和C层结构
categories: 项目实战
date: 2017-10-10
tags:
  - nodejs
  - 后端
  - 实战随笔
---
上次的数据库设计完了，对应的orm映射也做了，下来就是控制器的部分，

# 操作流程
首先我们要来根据用户需求来统计操作流程，并画出流程图。

## 用户类
- 用户登录
<img src="./用户登录@2x.png" title="用户登录"/>
- 用户注册
<img src="./用户注册@2x.png" title="用户注册" />
- 查看用户个人资料
- 用户编辑个人资料
<img src="./用户编辑个人资料@2x.png" title="用户编辑个人资料" />

## 购物车类
- 查看购物车
- 加入购物车
<img src="./将商品加入购物车@2x.png" title="将商品加入购物车"/>
- 修改：在购物车内修改已选属性，包括数量
<img src="./修改购物车内商品@2x.png" title="修改购物车内商品" />
- 删除：删除购物车内商品
<img src="./删除购物车内商品@2x.png" title="删除购物车内商品" />

## 订单类
- 查看订单
- 单个下单
<img src="./直接购买商品@2x.png" title="直接购买商品"/>
- 批量下单：将商品加入自己购物车，在购物车内批量下订单
<img src="./在购物车内下单购买商品@2x.png" title="在购物车内下单购买商品" />
- 删除：单个订单删除，在订单状态为待付款和已收货才可以删除。
<img src="./删除订单@2x.png" title="删除订单" />

## 商品类
- 查询售卖商品
<img src="./查询售卖商品@2x.png" title="查询售卖商品" />
- 添加售卖商品
<img src="./添加售卖商品@2x.png" title="添加售卖商品" />
- 修改售卖商品
<img src="./修改售卖商品@2x.png" title="修改售卖商品" />
- 删除售卖商品
<img src="./删除售卖商品@2x.png" title="删除售卖商品" />

## 供货商类
- 供货商登录
<img src="./用户登录@2x.png" title="供货商登录"/>
- 供货商注册
<img src="./用户注册@2x.png" title="供货商注册" />
- 供货商编辑个人资料
<img src="./用户编辑个人资料@2x.png" title="供货商编辑个人资料" />


# 路由设计以及传参设计
流程图画完了，大致清楚了需要api的地方，下面做api设计，然后在做路由的优化和合并。
 - 一级路由为对象，二级路由为对这些对象的操作方法
 - 合并一些路由，从传参判断
 - 以下接口默认标注为axios的默认标准

## 枚举：

- 用户类型
userTypes: ['系统管理员', '用户', '商户'],

- 订单状态
orderStates: ['待付款', '已付款', '待发货', '已发货', '待收货', '已收货']


## 用户类
- 用户登录
  - url: /user/signIn
  - Type: POST
  - request:
  ```javascript
    {
      username: "user1",
      password: "123"
    }
  ```

- 用户注册
  - url: /user/signUp
  - Type: POST
  - request:
  ```javascript
    {
      username: 'user1',
      userphone: '123',
      password: '123',
      nickname: '321',
      userType: '用户'
    }
  ```

- 查看用户资料
  - url: /user/getUserInfo/:useruuid
  - Type: GET

- 编辑用户资料
  - url: /user/setUserInfo
  - Type: POST
  ```javascript
    {
      useruuid: '599f99f2f7ca72710fbc9b67_2',
      username: '',
      userphone: '你特么到底想要几个大傻逼',
      password: '',
      nickname: '第二个大傻逼',
      summary: '握草，干他',
      userImg: ''
    }
  ```

## 购物车类
- 查看购物车
  - url: /shoppingCart/getGoods/:useruuid,
  - Type: GET

- 加入购物车
  - url: /shoppingCart/addGoods,
  - Type: POST
  - request:
  ```javascript
    {
      goods: {
        goodsCode: '599fc0ef7975f910877e6e36',
        goodsName: '矿泉水', //商品名称
        goodsSummary: '一瓶矿泉水', // 商品简介
        goodsImg: '目前没有', //商品图片
        goodsPrice: 300, // 商品标价
        goodsType: 2, // 商品分类
        goodsNumber: 2, // 商品数量
        goodsAttrs: ['300ML'], // 商品属性
        supplierUsername: 'admin', //供货商名称
        supplierNickname: '第二个大傻逼', //供货商名称
      },
      useruuid: useruuid
    }
  ```

- 修改：在购物车内修改已选属性，包括数量, 支持批量
  - url: /shoppingCart/editGoods,
  - Type: POST
  - request:
  ```javascript
    {
      goods: [{
        "shoppingCartId": "59a39ced278ddd1f9dd7edda",
        "goodsCode": "599fc0ef7975f910877e6e36",
        "goodsName": "这瓶矿泉水真的难喝"
      },{
        "shoppingCartId": "59a39ced278ddd1f9dd7eddb",
        "goodsCode": "599fc0ef7975f910877e6e36",
        "goodsName": "我真的是日了狗了"
      }],
      useruuid: useruuid
    }
  ```

- 删除：删除购物车内商品，支持批量
  - url: /shoppingCart/removeGoods,
  - Type: POST
  - request:
  ```javascript
    {
      goodsuuids: [
        '59a39cef278ddd1f9dd7ede4', '59a39cef278ddd1f9dd7ede5'
      ],
      useruuid: useruuid
    }
  ```

## 订单类
- 查看订单列表
  - url: /order/getOrder/:useruuid
  - Type: GET

- 查看单个订单
  - url: /order/getOrder/:useruuid/:orderid
  - Type: GET

- 新增单个订单和新增批量订单，交给S端计算
  - url: /order/addOrder
  - Type: POST
  - request:
  ```javascript
    {
      goods: [
        {
          goodsCode: '599fc0ef7975f910877e6e36',
          goodsName: '矿泉水', //商品名称
          goodsSummary: '一瓶矿泉水', // 商品简介
          goodsImg: '目前没有', //商品图片
          goodsPrice: 300, // 商品标价
          goodsType: 2, // 商品分类
          goodsNumber: 2, // 商品数量
          goodsAttrs: ['300ML'], // 商品属性
          supplierUsername: 'admin', //供货商名称
          supplierNickname: '第二个大傻逼', //供货商名称
        }
      ],
      useruuid: useruuid
    }
  ```

- 提交订单
  - url: /order/saveOrder
  - Type: POST
  - request:
  ```javascript
    {
      goods: [
        {
          goodsCode: "599fc0ef7975f910877e6e36",
          goodsName: "矿泉水",
          goodsSummary: "一瓶矿泉水",
          goodsImg: "目前没有",
          goodsPrice: 300,
          goodsType: 2,
          goodsNumber: 2,
          goodsAttrs: ["300ML"],
          supplierUsername: "admin",
          supplierNickname: "第二个大傻逼"
        }
      ],
      useruuid: useruuid,
      orderTotal: 600,
      actuaPayment: 600
    }
  ```

- 删除订单
  - url: /order/removeOrder/:useruuid/:orderid
  - Type: GET

## 商品类
- 查询售卖商品
  - url: /goods/getGoodsList
  - Type: GET
  - query:
  ```javascript
    {
      currentPage = 1;
      pageSize = 10;
      goods：id，
      name：string
    }==> ?currentPage=1&pageSize=10&goods=id&name=string
  ```

- 查询售卖商品单个信息
  - url: /goods/getGoods/:goodsuuid
  - Type: GET

- 添加售卖商品
  - url: /goods/addGoods
  - Type: POST
  - request:
  ```javascript
    {
      goodsName: '矿泉水', //商品名称
      goodsSummary: '一瓶矿泉水', // 商品简介
      goodsImg: '目前没有', //商品图片
      goodsPrice: 300, // 商品标价
      goodsType: 2, // 商品分类
      goodsStock: 9999, // 商品库存
      // 商品属性
      goodsAttrs: [
        {
          name: '规格',
          value: ['300ML', '500ML', '1L', '3L']
        }
      ],
      supplier: useruuid
    }
  ```
- 修改售卖商品
  - url: /goods/editGoods
  - Type: POST
  - request:
  ```javascript
    {
      goodsuuid: '599fc0ef7975f910877e6e36',
      goodsName: '矿泉水', //商品名称
      goodsSummary: '这真的是他妈一瓶矿泉水', // 商品简介
      goodsImg: '', //商品图片
      goodsPrice: null, // 商品标价
      goodsType: null, // 商品分类
      goodsStock: null // 商品库存
    }
  ```
- 删除售卖商品, 支持批量
  - url: /goods/removeGoods
  - Type: POST
  - request:
  ```javascript
    {
      goodsuuids: ['599fc57e148833122ae1df20', '599fc575148833122ae1df1e']
    }
  ```


# 项目链接
`https://github.com/shifeng1993/mongo-server`