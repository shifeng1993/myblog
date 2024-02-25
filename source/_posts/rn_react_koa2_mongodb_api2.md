---
title: 【RN+react+koa2+mongodb实战随笔】4.API和C层逻辑
categories: 项目实战
date: 2017-10-14
tags:
  - nodejs
  - 后端
  - 实战随笔
---

上回设计了路由，，已经搭好了架构，下面就要进行业务逻辑的实施了。

# 业务逻辑部分

## 用户类

```javascript
const mongoose = require('mongoose');
const UserModel = mongoose.model('User');
const SupplierModel = mongoose.model('Supplier');
const utils = require('../../utils')
const Data = utils.data()
class userController {

  static async signIn(ctx, next) {
    const body = await utils.getParams(ctx.request.body);
    if (!body.username || !body.password) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const userdoc = await UserModel.findOne({username: body.username});
      if (!userdoc) {
        const supplierdoc = await SupplierModel.findOne({username: body.username});
        if (!supplierdoc) {
          obj = {
            message: 'signIn failed, user nothing'
          }
        } else {
          obj = {
            message: 'signIn success',
            useruuid: utils.setUuid(supplierdoc._id, 2)
          }
        }
      } else {
        if (userdoc.password == body.password) {
          obj = {
            message: 'signIn success',
            useruuid: utils.setUuid(userdoc._id, 1)
          }
        } else {
          obj = {
            message: 'signIn failed, password error'
          }
        }
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  static async signUp(ctx, next) {
    const body = await utils.getParams(ctx.request.body);
    if (!body.username || !body.password || !body.userphone || !body.nickname || !body.userType || body.userType === '系统管理员') {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      let userType = await Data.userTypes.indexOf(body.userType);
      switch (userType) {
        case 1:
          const userdoc = await UserModel.create(body);
          obj = {
            message: 'signUp success'
          }
          break;
        case 2:
          const supplierdoc = await SupplierModel.create(body);
          obj = {
            message: 'signUp success'
          }
          break;
        default:
          obj = {
            message: 'user type nothing~'
          }
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  static async getUserInfo(ctx, next) {
    if (!ctx.params.useruuid) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const useruuid = await utils.getUuid(ctx.params.useruuid)[0]
      const userType = await parseInt(utils.getUuid(ctx.params.useruuid)[1])
      switch (userType) {
        case 1:
          const userdoc = await UserModel.findOne({
            _id: useruuid
          }, {
            password: 0,
            _id: 0
          });
          obj = JSON.stringify(userdoc);
          break;
        case 2:
          const supplierdoc = await SupplierModel.findOne({
            _id: useruuid
          }, {
            password: 0,
            _id: 0
          });
          obj = JSON.stringify(supplierdoc);
          break;
        default:
          obj = {
            message: 'user nothing~'
          }
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  static async setUserInfo(ctx, next) {
    const body = await utils.getParams(ctx.request.body);
    if (!body.useruuid) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const useruuid = await utils.getUuid(body.useruuid)[0]
      const userType = await parseInt(utils.getUuid(body.useruuid)[1])
      switch (userType) {
        case 1:
          const userdoc = await UserModel.update({
            _id: useruuid
          }, body);
          obj = {
            message: 'setUserInfo success'
          };
          break;
        case 2:
          const supplierdoc = await SupplierModel.update({
            _id: useruuid
          }, body);
          obj = {
            message: 'setUserInfo success'
          };
          break;
        default:
          obj = {
            message: 'user nothing~'
          }
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
}
module.exports = userController;
```


## 订单类

```javascript
const mongoose = require('mongoose');
const UserModel = mongoose.model('User');
const OrderModel = mongoose.model('Order');
const utils = require('../../utils')
const Data = utils.data()

class orderController {
  static async getOrder(ctx, next) {
    const params = await utils.getParams(ctx.params);
    if (!params.useruuid) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      if (!params.orderuuid) {
        const useruuid = await utils.getUuid(params.useruuid)[0]
        const getuserdoc = await UserModel.findOne({
          _id: useruuid
        }, {password: 0}).populate('orders', {_id: 0});
        obj = JSON.stringify(getuserdoc.orders)
      } else {
        const getorderdoc = await OrderModel
          .findOne({_id: params.orderuuid})
          .populate('goods', {_id: 0});
        obj = JSON.stringify(getorderdoc)
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  // 算钱的
  static async addOrder(ctx, next) {
    const body = await utils.getParams(ctx.request.body);
    if (!body.goods) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const useruuid = await utils.getUuid(body.useruuid)[0]
      let userType = await parseInt(utils.getUuid(body.useruuid)[1])
      let orderTotal = 0;
      for (let i = 0; i < body.goods.length; i++) {
        orderTotal += body.goods[i].goodsPrice * body.goods[i].goodsNumber
      }
      body.orderTotal = orderTotal; // 订单总价
      body.actuaPayment = orderTotal; // 实付款
      switch (userType) {
        case 1:
          obj = JSON.stringify(body);
          break;
        default:
          obj = {
            message: 'user type nothing~'
          }
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  // 提交订单的
  static async saveOrder(ctx, next) {
    const body = await utils.getParams(ctx.request.body);
    if (!body.goods) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const useruuid = await utils.getUuid(body.useruuid)[0]
      let userType = await parseInt(utils.getUuid(body.useruuid)[1])
      body.orderState = 0 // 订单状态
      body.createTime = new Date(); // 订单创建时间
      switch (userType) {
        case 1:
          const orderdoc = await OrderModel.create(body);
          const updateuserdoc = await UserModel.update({
            _id: useruuid
          }, {
            '$push': {
              'orders': orderdoc.id
            }
          });
          obj = {
            message: 'saveOrder success'
          }
          break;
        default:
          obj = {
            message: 'user type nothing~'
          }
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  static async removeOrder(ctx, next) {
    const params = await utils.getParams(ctx.params);
    const getorderdoc = await OrderModel.findOne({_id: params.orderuuid})
    if (!params.useruuid || !params.orderuuid || !getorderdoc) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const useruuid = await utils.getUuid(params.useruuid)[0]
      const orderdoc = await OrderModel.remove({_id: params.orderuuid});
      const updateuserdoc = await UserModel.update({
        _id: useruuid
      }, {
        '$pull': {
          'orders': params.orderuuid
        }
      });
      obj = {
        message: 'saveOrder success'
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
}
module.exports = orderController;
```

## 购物车类

```javascript
const mongoose = require('mongoose');
const ShoppingCartModel = mongoose.model('ShoppingCart');
const UserModel = mongoose.model('User');
const utils = require('../../utils')
const Data = utils.data()

class shoppingCartController {
  static async getGoods(ctx, next) {
    const params = await utils.getParams(ctx.params);
    if (!params.useruuid) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const useruuid = await utils.getUuid(params.useruuid)[0]
      let userType = await parseInt(utils.getUuid(params.useruuid)[1])
      switch (userType) {
        case 1:
          const doc = await UserModel.findOne({
            _id: useruuid
          }, {
            password: 0,
            _id: 0
          }).populate('shoppingCart');
          obj = JSON.stringify(doc.shoppingCart);
          break;
        default:
          obj = {
            message: 'user type nothing~'
          }
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  static async addGoods(ctx, next) {
    const body = await utils.getParams(ctx.request.body);
    if (!body.goods) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const useruuid = await utils.getUuid(body.useruuid)[0]
      let userType = await parseInt(utils.getUuid(body.useruuid)[1])
      switch (userType) {
        case 1:
          const getuserdoc = await UserModel.findOne({_id: useruuid});
          if (getuserdoc.shoppingCart.length < 100) {
            const ShoppingCartdoc = await ShoppingCartModel.create(body.goods)
            const updateuserdoc = await UserModel.update({
              _id: useruuid
            }, {
              '$push': {
                'shoppingCart': ShoppingCartdoc.id
              }
            });
          } else {
            await ShoppingCartModel.remove({_id: getuserdoc.shoppingCart[0]})
            const ShoppingCartdoc = await ShoppingCartModel.create(body.goods)
            await UserModel.update({
              _id: useruuid
            }, {
              '$pull': {
                'shoppingCart': getuserdoc.shoppingCart[0]
              }
            });
            await UserModel.update({
              _id: useruuid
            }, {
              '$push': {
                'shoppingCart': ShoppingCartdoc.id
              }
            });
          }
          obj = {
            message: 'addGoods success'
          }
          break;
        default:
          obj = {
            message: 'user type nothing~'
          }
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  static async editGoods(ctx, next) {
    const body = await utils.getParams(ctx.request.body);
    if (!body.useruuid) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      for (let i = 0; i < body.goods.length; i++) {
        await ShoppingCartModel.update({
          _id: body.goods[i].shoppingCartId
        }, body.goods[i]);
      }
      obj = {
        message: 'editGoods success'
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  static async removeGoods(ctx, next) {
    const body = await utils.getParams(ctx.request.body);
    const getgoodsdoc = await ShoppingCartModel.find({
      '_id': {
        $in: body.goodsuuids
      }
    })
    if (!body.goodsuuids || !getgoodsdoc || getgoodsdoc.length === 0 || !body.useruuid) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const useruuid = await utils.getUuid(body.useruuid)[0]
      let userType = await parseInt(utils.getUuid(body.useruuid)[1])
      const userdoc = await UserModel.update({
        _id: useruuid
      }, {
        '$pull': {
          'shoppingCart': body.goodsuuids
        }
      });
      const ShoppingCartdoc = await ShoppingCartModel.remove({
        '_id': {
          $in: body.goodsuuids
        }
      });
      obj = {
        message: 'removeGoods success'
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
}
module.exports = shoppingCartController;
```

## 商品类

```javascript
const mongoose = require('mongoose');
const GoodsModel = mongoose.model('Goods');
const utils = require('../../utils')
const Data = utils.data()

class goodsController {
  static async getGoodsList(ctx, next) {
    if (!ctx.request.query.currentPage || !ctx.request.query.pageSize) {
      return next(ctx.throw(400, 'params error'));
    }
    const currentPage = parseInt(ctx.request.query.currentPage)
    const pageSize = parseInt(ctx.request.query.pageSize);
    let obj = null;
    try {
      const query = await utils.getParams(ctx.request.query);
      const doc = await GoodsModel
        .find(query)
        .populate('goodsAttrs', {_id: 0})
        .populate('supplier', {
          password: 0,
          _id: 0,
          editTime: 0
        })
        .skip((currentPage - 1) * pageSize)
        .limit(pageSize)
        .sort({'_id': -1})
        .exec();
      const total = await GoodsModel.count();
      obj = JSON.stringify({data: doc, currentPage: currentPage, pageSize: pageSize, pageTotal: total})
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  static async getGoods(ctx, next) {
    if (!ctx.params.goodsuuid) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const doc = await GoodsModel.findOne({
        _id: ctx.params.goodsuuid
      }, {
          password: 0,
          _id: 0
        })
        .populate('goodsAttrs', {_id: 0})
        .populate('supplier', {
          _id: 0,
          password: 0,
          editTime: 0
        });
      obj = JSON.stringify(doc)
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  static async addGoods(ctx, next) {
    const body = await utils.getParams(ctx.request.body);
    if (!body.goodsName || !body.goodsSummary || !body.goodsImg || !body.goodsPrice || !body.goodsType || !body.goodsStock || !body.goodsAttrs || !body.supplier) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      let userType = await parseInt(utils.getUuid(body.supplier)[1])
      body.supplier = await utils.getUuid(body.supplier)[0]
      if (userType === 2) {
        const doc = await GoodsModel.create(body);
        obj = {
          message: 'addGoods success'
        }
      } else {
        obj = {
          message: 'userType error'
        }
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  static async editGoods(ctx, next) {
    const body = await utils.getParams(ctx.request.body);
    if (!body.goodsuuid) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const doc = await GoodsModel.update({
        _id: body.goodsuuid
      }, body);
      obj = {
        message: 'editGoods success'
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
  static async removeGoods(ctx, next) {
    const body = await utils.getParams(ctx.request.body);
    const getgoodsdoc = await GoodsModel.find({
      '_id': {
        $in: body.goodsuuids
      }
    })
    if (!body.goodsuuids || !getgoodsdoc) {
      return next(ctx.throw(400, 'params error'));
    }
    let obj = null;
    try {
      const doc = await GoodsModel.remove({
        '_id': {
          $in: body.goodsuuids
        }
      });
      obj = {
        message: 'removeGoods success'
      }
    } catch (error) {
      console.log(error)
      obj = {
        error: error,
        msg: 'server error'
      };
    }
    ctx.body = obj
  }
}
module.exports = goodsController;
```

# server端完结感言

反正真的是不容易，从10号开始陆陆续续，一共十八个api，，写了十八天。。各种查资料，问人，算是把逻辑部分的工程写法大概弄明白了。，剩下就是一些规范和联调部分。后续会随着移动端和pc浏览器端的进展进行项目更新的。

PS：这篇文章发的早。现在已经是七夕了。。


# 项目链接
`https://github.com/shifeng1993/mongo-server`
