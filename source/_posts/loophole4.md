---
title: 【查漏-js】4.函数属性length，方法 apply、call、bind
categories: base
date: 2019-01-14 13:41:00
tags:
  - base
  - js
---
# 函数属性length
函数的 length 为 形参 的 `length`，
`arguments` 的 `length` 为实参的length。

例如：
```javascript
function foo(a, b) {
  console.log(arguments.length);
}

foo(); // 0
console.log(foo.length); // 2
```

# 函数方法 apply、call、bind
call()方法与 apply()方法的作用相同，它们的区别仅在于接收参数的方式不同, apply是接受数组，call是正常参数。

bind()是es5新加的方法。这个方法会创建一个函数的实例，方便后续使用，其 this 值会被绑 定到传给 bind()函数的值。

以上三个方法 都会绑定函数的作用域。

## apply 和 call 
```javascript
function construct(name, age) {
  this.name = name;
  this.age = age;
}

function people(name, age) {
  // 在call中将this作为thisArgs参数传递
  // construct方法中的this就指向了Cat中的this
  // 所以construct中的this指向的就是cat对象
  // 在construct中定义了name和age属性，就相当于在cat中定义了这些属性
  // cat对象便拥有了construct中定义的属性，从而达到了继承的目的
  // construct.call(this, name, age);
  construct.apply(this, arguments);
  this.getName = function () {
    console.log("I am " + this.name);
  }

  this.getAge = function () {
    console.log("My age is " + this.age);
  }

  // getAge.bind(this)
}

//当通过new运算符产生了cat时，Cat中的this就指向了cat对象
var girl = new people('Lily', 50);

girl.getName(); // I am Lily
girl.getAge(); // My age is 50
```

## bind
最经典的应用场景就是react组件内函数的this指向问题。或者统一使用箭头函数，不要this，或者使用bind函数，bind（this）到组件的this上
```javascript
var foo = {
  data: {
    name: '123',
    age: 321
  },
  printData: function () {
    function bar() {
      console.log(this.data); // 这里面的this是指向 foo.printData 这个function，而不是foo对象
    }
    bar.bind(this)();
  }
}

foo.printData(); // { name: '123', age: 321 }
```
