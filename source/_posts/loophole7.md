---
title: 【查漏-js】7.面向对象之继承
date: 2019-01-16 12:07:00
tags:
  - base
  - js
---
由于 es 函数没有签名， 在函数中无法实现OO语言的接口继承，只支持实现继承。
# 原型链继承
原型链继承，就是把一个函数的原型等于另一个函数的实例。

原型链继承的缺点:
- 在通过原型来实现继承时，原型实际上会变成另一个类型的实例。于是，原先的实例属性也就顺理成章地变成了现在的原型属性了。
- 创建子类型的实例时，不能向父类型的构造函数中传递参数。

示例：
```javascript
// 找老婆函数
function LookingForAWife() {
  // 一个女孩
  function Girl() {
    this.name = 'Arutoria';
    this.age = 20;
    this.job = '护士';
    this.dating = false; // 是否在恋爱中
  }

  // 一个女朋友
  function GirlFriend() {
    this.belong = 'haise';
  }

  // 一个老婆
  function Wife() {
    this.husband = 'haise';
  }

  // 获取女孩信息
  Girl.prototype.getGirlInfo = function () {
    return {
      name: this.name,
      age: this.age,
      job: this.job,
      dating: this.dating // 是否在恋爱中
    };
  }

  var girl = new Girl();

  // 动态原型模式
  if (!girl.dating) {
    GirlFriend.prototype = girl; // 如果女孩没对象，就做女朋友的原型
  } else {
    console.log('没戏');
    // 清空看中的女孩
    return girl = null;
  }

  // 追求做女朋友
  var girlFriend = new GirlFriend();
  girlFriend.__proto__.dating = true // 如果追求上就女孩就是在恋爱过程中了

  // 动态原型模式
  if (girlFriend.belong === 'haise') {
    Wife.prototype = girlFriend; // 判断女朋友是不是爱自己，如果是就作为妻子的原型
  } else {
    console.log('结婚没戏');
    // 清空女朋友节省内存
    return girlFriend = null;
  }

  var wife = new Wife(); // 结婚做老婆

  console.log(wife.name); // 老婆的名字
  console.log(wife.age); // 老婆的年龄
  console.log(wife.job); // 老婆的工作
  console.log('爱我？', wife.dating); // 老婆是否爱我
  console.log('属于', wife.belong); // 老婆的归属
  console.log('丈夫', wife.husband); // 老婆的丈夫
  console.log(wife.getGirlInfo()); // { name: 'Arutoria', age: 20, job: '护士', dating: true }
}

LookingForAWife();
```

# 借用构造函数
在子类型构造函数的内部调用父类型构造函数。
问题：
- 方法都在构造函数中定义，因此函数复用就无从谈起了
- 在超类型的原型中定义的方法，对子类型而言也是不可见的，结果所有类型都只能使用构造函数模式

```javascript
function Girl(name) {
  this.name = name;
}

function GirlFriend() {
  //继承了 Girl，同时还传递了参数 
  Girl.call(this, "Arutoria");
  //实例属性
  this.age = 20;
}
var instance = new GirlFriend();
console.log(instance.name); //"Arutoria";
console.log(instance.age); //20
```

# 组合继承
组合继承是将原型链和借用构造函数组合在一起，既通过在原型上定义方法实现了函数复用，又能够保证每个实例都有它自己的属性。

这种继承方式比较常用，原因是结合了原型链继承和借用构造函数继承，各自的优点。

示例：
```javascript
// 添加女孩构造函数
function Girl(name) {
  this.name = name;
  this.job = ["护士", "空姐", "教师"];
}

// 给女孩构造函数的原型对象上添加方法，打印this下的name属性
Girl.prototype.sayName = function () {
  console.log(this.name);
};

// 添加女朋友构造函数，借用女孩构造函数并call this，这样就可以把女孩的this指向女朋友。
function GirlFriend(name, age) {
  //继承属性 
  Girl.call(this, name);
  this.age = age;
}

GirlFriend.prototype = new Girl(); // 使用原型链
GirlFriend.prototype.constructor = GirlFriend; // 重写原型丢失的构造函数
// 女朋友原型对象上添加说年龄函数，
GirlFriend.prototype.sayAge = function () {
  console.log(this.age);
};

// 使用女朋友构造函数
var girl1 = new GirlFriend("Arutoria", 20);
console.log('Arutoria的职业是：' + girl1.job); // Arutoria的职业是：护士,空姐,教师
girl1.sayName(); // Arutoria
girl1.sayAge(); // 20
```

# 原型式继承
原型式在es5被规范化， 使用`Object.create()`方法，方法接收两个参数:一 个用作新对象原型的对象和(可选的)一个为新对象定义额外属性的对象。

示例：
```javascript
var Girl = {
  name: 'Arutoria',
  job: ["护士", "空姐", "教师"],
}

var girlFriend = Object.create(Girl, {
  name: {
    value: "Amia"
  }
});

console.log(girlFriend.name); // Amia
console.log(girlFriend.job) // [ '护士', '空姐', '教师' ]
```

# 寄生式继承
寄生式继承的思路与寄生构造函数和工厂模式类似，即创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后再像真地是它做了所有工作一样返回对象。

示例：
```javascript
function createGirlFriend(original) {
  var clone = new Object(original); //通过调用函数创建一个新对象
  clone.sayHi = function () {
    console.log("hi");
  };
  return clone;
}

var Girl = {
  name: 'Arutoria',
  job: ["护士", "空姐", "教师"],
}

var girl = createGirlFriend(Girl);
girl.sayHi(); //"hi"
console.log(girl.name); // Arutoria
console.log(girl.job); // [ '护士', '空姐', '教师' ]
```

# 寄生组合式继承
组合继承是js最常用的继承模式，组合继承最大的问题就是无论在什么情况下，都会调用两次构造函数：一次是在创建子类型原型时，另一次是在子类型构造函数内部。

```javascript
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}

function inheritPrototype(subType, suberType) {
  var prototype = object(suberType.prototype); // 创建对象
  prototype.constructor = subType; // 增强对象
  subType.prototype = prototype; // 指定对象
}

function SuperType(name) {
  this.name = name;
  this.colors = ['red', 'blue', 'green'];
}
SuperType.prototype.sayName = function () {
  console.log(this.name);
}

function SubType(name, age) {
  SuperType.call(this, name);
  this.age = age;
}
inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function () {
  console.log(this.age);
}

// 使用构造函数
var girl1 = new SubType("Arutoria", 20);
girl1.sayName(); // Arutoria
girl1.sayAge(); // 20
```
