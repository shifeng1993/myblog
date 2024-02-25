---
title: 【查漏-js】10.js包装对象与类型转换
date: 2019-10-31 21:32:00
tags:
  - base
  - js
---
# 前言
本次查漏，因为都是属于类型，所以二合一。

- 包装对象（`Number`、`String`、`Boolean`）

  对于包装对象这个概念，我以前是真的不知道。一直以为是跟对象一样，字面量可以直接继承prototype上的方法。

- 类型转换 

  类型转换这部分，以前只是记住了常用部分的特殊值，并没有仔细研究规则，不会活用。

以上两点，是在今天公司的培训会上提及，应该是我漏掉了。

# 包装对象
## 定义
对象是 JavaScript 语言最主要的数据类型，三种原始类型的值——数值、字符串、布尔值——在一定条件下，也会自动转为对象，也就是原始类型的“包装对象”（`wrapper`）。

所谓“包装对象”，指的是与数值、字符串、布尔值分别相对应的`Number`、`String`、`Boolean`三个原生对象。这三个原生对象可以把原始类型的值变成（包装成）对象。

```js
var v1 = new Number(123);
var v2 = new String('abc');
var v3 = new Boolean(true);

typeof v1 // "object"
typeof v2 // "object"
typeof v3 // "object"

v1 === 123 // false
v2 === 'abc' // false
v3 === true // false
```

上面代码中，基于原始类型的值，生成了三个对应的包装对象。可以看到，`v1`、`v2`、`v3`都是对象，且与对应的简单类型值不相等。

包装对象的设计目的，首先是使得“对象”这种类型可以覆盖 JavaScript 所有的值，整门语言有一个通用的数据模型，其次是使得原始类型的值也有办法调用自己的方法。

`Number`、`String`和`Boolean`这三个原生对象，如果不作为构造函数调用（即调用时不加`new`），而是作为普通函数调用，常常用于将任意类型的值转为数值、字符串和布尔值。

```js
// 字符串转为数值
Number('123') // 123

// 数值转为字符串
String(123) // "123"

// 数值转为布尔值
Boolean(123) // true
```

总结一下，这三个对象作为构造函数使用（带有`new`）时，可以将原始类型的值转为对象；作为普通函数使用时（不带有`new`），可以将任意类型的值，转为原始类型的值。

## 实例方法
三种包装对象各自提供了许多实例方法。这里介绍两种它们共同具有、从`Object`对象继承的方法：`valueOf()`和`toString()`。

### valueOf()
valueOf()方法返回包装对象实例对应的原始类型的值。
```js
new Number(123).valueOf()  // 123
new String('abc').valueOf() // "abc"
new Boolean(true).valueOf() // true
```

### toString()
toString()方法返回对应的字符串形式。
```js
new Number(123).toString() // "123"
new String('abc').toString() // "abc"
new Boolean(true).toString() // "true"
```
## 原始类型与实例对象的自动转换
某些场合，原始类型的值会自动当作包装对象调用，即调用包装对象的属性和方法。这时，JavaScript 引擎会自动将原始类型的值转为包装对象实例，并在使用后立刻销毁实例。

比如，字符串可以调用`length`属性，返回字符串的长度。

```js
'abc'.length // 3
```

上面代码中，`abc`是一个字符串，本身不是对象，不能调用`length`属性。JavaScript 引擎自动将其转为包装对象，在这个对象上调用`length`属性。调用结束后，这个临时对象就会被销毁。这就叫<b>原始类型与实例对象的自动转换</b>。

```js
var str = 'abc';
str.length // 3

// 等同于
var strObj = new String(str)
// String {
//   0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"
// }
strObj.length // 3
```

自动转换生成的包装对象是只读的，无法修改。所以，字符串无法添加新属性。

```js
var s = 'Hello World';
s.x = 123;
s.x // undefined
```

上面代码为字符串`s`添加了一个`x`属性，结果无效，总是返回`undefined`。

另一方面，调用结束后，包装对象实例会自动销毁。这意味着，下一次调用字符串的属性时，实际是调用一个新生成的对象，而不是上一次调用时生成的那个对象，所以取不到赋值在上一个对象的属性。<b>如果要为字符串添加属性，只有在它的原型对象`String.prototype`上定义</b>。

## 自定义方法
除了原生的实例方法，包装对象还可以自定义方法和属性，供原始类型的值直接调用。

比如，我们可以新增一个`double`方法，使得字符串和数字翻倍。

```js
String.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

'abc'.double() // abcabc

Number.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

(123).double() // 246
```
上面代码在`String`和`Number`这两个对象的原型上面，分别自定义了一个方法，从而可以在所有实例对象上调用。注意，最后的`123`外面必须要加上圆括号，否则后面的点运算符（`.`）会被解释成小数点。

# 类型转换
## 抽象相等比较算法
在比较`x == y`，其中 `x` 和 `y` 是值，结果返回 `true` 或 `false`。比较规则如下：

### 类型相同
当 `x` 的类型与 `y` 的类型相同时：
```
1. 如果`x`的类型为`Undefined`，返回`true`。
2. 如果`x`的类型为`Null`，返回`true`。
3. 如果`x`的类型为`Number`，则如下:
  - 如果 x 为 NaN, 返回 false.(NaN == NaN 为false)
  - 如果 y 为 NaN, 返回 false.	
  - 如果 x 和 y 的值相同, 返回 true.
  - 如果x为+0，y为−0，返回 true.
  - 如果x为−0，y为+0，返回 true.
  - 其他返回 false.
4. 如果x是string类型，那么假如x和y是完全相同的字符序列（相同的长度和相应位置的相同字符），则返回true。否则，返回false。
5. 如果x是布尔类型，假如x和y都为true或都为false，则返回true。否则，返回false。
6. 如果x和y引用同一对象，则返回true。否则，返回false。
```

### 类型不同
x 和 y 类型不相同的情况:
```
1. 如果x为null，y为undefined，则返回true。
2. 如果x为undefined，y为null，则返回true。(其余的任何类型与undefined和null相比都为false)
3. 如果x是数字类型，y是字符串类型，则返回x == ToNumber(y)的比较结果.
4. 如果x是字符串类型，y是数字类型，则返回ToNumber(x) == y的比较结果.
5. 如果x是布尔类型，则返回ToNumber（x）==y的比较结果.
6. 如果y是布尔类型，则返回x == ToNumber（y)的比较结果.
7. 如果x是字符串或数字类型，y 是对象类型，返回比较结果x== ToPrimitive（y）。
8. 如果x是对象类型，y是字符串或数字类型，返回比较结果ToPrimitive（x）==y。
9. 其他则返回false
```

上述规则看起来很复杂，但总结起来其实只有以下几点：

- 两个引用类型比较，只需判断它们是不是引用了同一个对象，是返回`true`，否则为`false`。
- `undefined` 和 `null` 两者互相比较或者与自身比较，结果是`true`。它俩与其他任何值比较的都为`false`。
- `NaN`与任何值比较包括它自身结果都是`false`。
- 引用类型和基本数据类型(`String`、`Number`)进行比较，引用类型会转换成与之所比较的基本数据的类型，再进行比较。
- 引用类型和基本数据类型(`Boolean`)进行比较，`Boolean`类型先转换为数字类型，引用类型再转换成与之所比较的基本数据的类型进行比较。
- `String`，`Boolean`，`Number`中的任意两个进行比较，最后都会转为`Number`类型再进行比较。

现在我们再来看一下具体的类型转换过程

## 抽象值操作 
### ToString
抽象操作`ToString`负责处理非字符串到字符串的强制类型转换。
基本类型值的字符串化规则为：`null`转换为`"null"`，`undefined`转换为`"undefined"`，`true`转换为`"true"`。数字的字符串化遵循通用规则，那些极小和极大的数字使用指数形式：
```js
var a = 1.07*1000*1000*1000*1000*1000*1000*1000
a.toString()    //"1.07e21"
```

对<b>普通对象</b>来说，除非自行定义，否则`toString()`返回内部属性`[[Class]]`的值，如`"[object Object]"`。
数组的默认`toString()`方法经过了重新定义，将所有单元字符串化以后再用`","`连接起来：
```js
var a = [1,2,3]
a.toString()    //"1,2,3"
```

### ToNumber
抽象操作`ToNumber`将非数字值转换为数字值。
其中`true`转换为`1`，`false`转换为`0`，`undefined`转换为`NaN`,`null`转换为`0`。
`ToNumber`对字符串的处理基本遵循数字常量的相关规则，`""`(空字符串)转为`0`，`'123'` 转为 `123`，(字符串中含有非数字类型字符返回`NaN`，`NaN`与任何类型比较都为`false`，此时就不需要再考虑其他情况)。
引用类型，会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其强制转换为数字。

### ToPrimitive
抽象方法`ToPrimitive(input, PreferredType?)`将对象值转换为相应的基本类型值。
`input`是要转换的值，`PreferredType`是可选参数，可以是`Number`或`String`类型。他只是一个转换标志，转化后的结果并不一定是这个参数所值的类型，但是转换结果一定是一个原始值（或者报错）
#### 如果`PreferredType`被标记为Number
1. 如果输入的值已经是一个原始值，则直接返回它
2. 否则，如果输入的值是一个对象，则调用该对象的`valueOf()`方法，
   如果`valueOf()`方法的返回值是一个原始值，则返回这个原始值。
3. 否则，调用这个对象的`toString()`方法，如果`toString()`方法返回的是一个原始值，则返回这个原始值。
4. 否则，抛出TypeError异常。

#### 如果`PreferredType`被标记为String
1. 如果输入的值已经是一个原始值，则直接返回它
2. 否则，调用这个对象的`toString()`方法，如果`toString()`方法返回的是一个原始值，则返回这个原始值。
3. 否则，如果输入的值是一个对象，则调用该对象的`valueOf()`方法，
   如果`valueOf()`方法的返回值是一个原始值，则返回这个原始值。
4. 否则，抛出`TypeError`异常。

#### 没有`PreferredType`这个参数时，怎么转换呢？
`PreferredType`的值会按照这样的规则来自动设置：
1. 该对象为`Date`类型，则`PreferredType`被设置为`String`
2. 否则，`PreferredType`被设置为Number

### ToBoolean
抽象操作`ToBoolean`将非布尔值转换为布尔值。
对于布尔类型转换来说，假值(`undefined`、`null`、`fals`e、`+0`、`-0`、`NaN`、`""`)会被转换为false，除假值外的所有值都会被转换成true。

## 常见的隐式强制类型转换
- `+ - * / %` 这几个运算符都可以将结果转为<b>数字类型</b>
- `+` 运算符常用来进行<b>数字类型强制转换</b>，因为它不会像`-`运算符一样影响结果
- `+` 运算符可以将日期类型`new Date()`转换成<b>数字类型的时间戳</b>
- `!` 逻辑运算符<b>可以将任何类型转为布尔类型</b>，因为`!`运算符转换后的结果是被反转的，我们也可以使用两个`!`来得出正确的结果
- `if()、 while()`括号内可以对所有数据类型<b>默认执行强制布尔类型转换</b>
- 三元运算符的条件也会执行<b>强制布尔类型转换</b>
