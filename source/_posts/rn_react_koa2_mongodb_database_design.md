---
title: 【RN+react+koa2+mongodb实战随笔】2.数据库和M层设计
categories: 项目实战
date: 2017-10-08
tags:
  - nodejs
  - 后端
  - 实战随笔
---
这两天，在进行demo数据库设计的时候由于基础不扎实，导致设计结构混乱。没办法，只能硬着头皮又去研究了一遍关联关系，mongodb数据库设计。

参考文章：

[关联关系](http://www.cnblogs.com/lgxlsm/archive/2013/05/15/3080994.html "关联关系")

[MongoDB进阶模式设计](http://www.mongoing.com/mongodb-advanced-pattern-design "MongoDB进阶模式设计")

对于waterline个人感觉不太适合mongodb，就又换成了mongoose。换了之后感觉好多了。

# 数据库设计
数据库设计分为两个步骤，一个是逻辑设计，一个是物理设计

# 逻辑设计

## 模块列表
- 用户
- 商品
- 订单
- 购物车
- 供货商

## 模块关系
- 用户<一--多>订单
- 订单<一--多>商品
- 用户<一--一>购物车
- 购物车<一--多>商品
- 商品<一--一>供货商
- 商品<多--多>用户

## 逻辑设计原则
mongodb逻辑设计是和mysql不一样的，mongodb用复杂的写入换取了简单的读取，并且采用灵活模式，^_^。赞！

我理解的mongodb设计原则：
- 为应用服务，为性能设计，而不是为了存储优化，
- 灵活方式大于规范方式，
- 优先嵌套
- 对于有实体的对象，采用引入，对于无实体的对象，采用嵌套。
- 嵌套适合一对一，一对多
- 引用适合多对多

## 逻辑设计实施
模型和实体关系是息息相关的，er图是必须的，但是此demo比较简单，就省去了。
```
用户：{
  订单: [{
    商品: [{}],
  }],
  购物车: [商品]
}
商品{
  商品信息，
  供货商：“”
}
供货商：{供货商信息} 
```

一个用户对应多个订单，订单内嵌在用户下

一个订单可以对应多个商品，商品内嵌在订单下

一个用户对应一个购物车，购物车内嵌在用户下

一个购物车对应多个商品，商品内嵌在购物车下

一个商品对应一个供货商，供货商是单独存在的实体，商品可以没有和下架，但是供货商一直都在，所以商品引用供货商信息。

注：关于用户下商品的内嵌，如果采用引用，则不能记录购买时的信息。所以必须单独放在用户下

# 物理设计
既然逻辑设计出了物理设计就好办了，先制定规范，后填字段就可以

## 物理设计规范
1. 采用驼峰命名，能简洁清楚表达字段名称意义，
2. 类别类型，统一用int来存储，
3. 时间格式，统一用Date来存储
4. 二进制，统一用Buffer来存储

## 物理设计实施

### 用户模型
```javascript
const mongoose = require('mongoose');
const ObjectId = mongoose.Schema.Types.ObjectId;

const UserSchema = new mongoose.Schema({
  username: {
    type: String,
    unique: true
  }, // 账号
  userphone: {
    type: String,
    unique: true
  }, // 手机
  password: String, // 密码
  nickname: String, // 昵称
  summary: String, //简介
  userImg: String, // 头像
  editTime: {
    type: Date,
    default: Date.now
  },
  orders: [
    {
      type: ObjectId,
      ref: 'Order'
    }
  ],
  shoppingCart: [
    {
      type: ObjectId,
      ref: 'ShoppingCart'
    }
  ]
}, {versionKey: false})
module.exports = mongoose.model('User', UserSchema);
```

### 订单模型
```javascript
const mongoose = require('mongoose');
const OrderSchema = new mongoose.Schema({
  orderState: Number,  //订单状态
  createTime: Date,  // 订单创建时间
  dealTime: Date,   // 订单成交时间
  orderTotal: Number,  // 订单总价
  actuaPayment: Number, // 实付款
  // 订单中商品详情
  goods: [{
    goodsCode: String, //商品id
    goodsName: String, //商品名称
    goodsSummary: String, // 商品简介
    goodsImg: String,  //商品图片
    goodsPrice: Number, // 商品标价
    goodsNumber: Number, // 商品数量
    supplierUsername: String, // 供货商账号
    supplierNickname: String, //供货商名称
    goodsType: Number, // 商品分类
    goodsAttrs: [String]  // 商品属性
  }]
}, {versionKey: false})
module.exports = mongoose.model('Order', OrderSchema);
```

### 购物车模型
```javascript
const mongoose = require('mongoose');
const ShoppingCartSchema = new mongoose.Schema({
  goodsCode: String, //商品id
  goodsName: String, //商品名称
  goodsSummary: String, // 商品简介
  goodsImg: String,  //商品图片
  goodsType: Number, // 商品分类
  goodsPrice: Number, // 商品标价
  supplierUsername: String, // 供货商id
  supplierNickname: String, //供货商名称
  goodsAttrs: [String],  // 商品属性
  goodsNumber: Number, // 商品数量
  editTime: {
    type: Date,
    default: Date.now
  }
}, {versionKey: false})
module.exports = mongoose.model('ShoppingCart', ShoppingCartSchema);
```

### 商品模型
```javascript
const mongoose = require('mongoose');
const ObjectId = mongoose.Schema.Types.ObjectId;

const GoodsSchema = new mongoose.Schema({
  goodsName: String, //商品名称
  goodsSummary: String, // 商品简介
  goodsImg: String, //商品图片
  goodsPrice: Number, // 商品标价
  goodsType: Number, // 商品分类
  goodsStock: Number, // 商品库存
  editTime: {
    type: Date,
    default: Date.now
  },
  // 商品属性
  goodsAttrs: [
    {
      name: String,
      value: [String]
    }
  ],
  // 供货商
  supplier: {
    type: ObjectId,
    ref: 'Supplier'
  }
}, {versionKey: false})
module.exports = mongoose.model('Goods', GoodsSchema);
```

### 供货商模型
```javascript
const mongoose = require('mongoose');
const SupplierSchema = new mongoose.Schema({
  username: {
    type: String,
    unique: true
  }, // 账号
  userphone: {
    type: String,
    unique: true
  }, // 手机
  password: String, // 密码
  nickname: String, // 昵称
  summary: String, //简介
  userImg: String, // 头像
  editTime: {
    type: Date,
    default: Date.now
  }
}, {versionKey: false})
module.exports = mongoose.model('Supplier', SupplierSchema);
```


# 项目链接
`https://github.com/shifeng1993/mongo-server`