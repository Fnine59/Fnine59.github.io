---
title: 《JavaScript标准参考教程》阅读笔记之《标准库》
date: 2018-05-10 23:58:20
tags: "JavaScript"
categories: "JavaScript标准教程笔记"
---

> 整理自阮一峰《JavaScript标准参考教程》，原作者为阮一峰，[github地址](https://github.com/ruanyf/jstutorial)

# Object对象

## 概述

JavaScript原生提供`Object`对象（注意起首的`O`是大写），本章介绍该对象原生的各种方法。

**JavaScript的所有其他对象都继承自`Object`对象，即那些对象都是`Object`的实例**。

`Object`对象的原生方法分成两类：`Object`本身的方法与`Object`的实例方法。

### Object对象本身的方法

所谓“本身的方法”就是**直接定义在`Object`对象的方法**。

```
Object.print = function (o) { console.log(o) };
```
上面代码中，`print`方法就是直接定义在`Object`对象上。

### Object的实例方法

所谓实例方法就是**定义在`Object`原型对象`Object.prototype`上的方法**。它可以被`Object`实例直接使用。

```
Object.prototype.print = function () {
  console.log(this);
};

var obj = new Object();
obj.print() // Object
```
上面代码中，`Object.prototype`定义了一个`print`方法，然后生成一个`Object`的实例`obj`。`obj`直接继承了`Object.prototype`的属性和方法，可以直接使用`obj.print`调用`print`方法。也就是说，`obj`对象的`print`方法实质上就是调用`Object.prototype.print`方法。

关于原型对象`object.prototype`的详细解释，参见《面向对象编程》章节。这里只要知道，**凡是定义在`Object.prototype`对象上面的属性和方法，将被所有实例对象共享就可以了。**

以下先介绍`Object`作为函数的用法，然后再介绍`Object`对象的原生方法，分成对象自身的方法（又称为“静态方法”）和实例方法两部分。

## Object()

**`Object`本身是一个函数，可以当作工具方法使用，将任意值转为对象。这个方法常用于保证某个值一定是对象。**

**如果参数为空（或者为`undefined`和`null`），Object()返回一个空对象。**

```
var obj = Object();
// 等同于
var obj = Object(undefined);
var obj = Object(null);

obj instanceof Object // true
```
上面代码的含义，是将`undefined`和`null`转为对象，结果得到了一个空对象`obj`。

**`instanceof`运算符用来验证，一个对象是否为指定的构造函数的实例。**`obj instanceof Object`返回`true`，就表示`obj`对象是`Object`的实例。

**如果参数是原始类型的值，`Object`方法将其转为对应的包装对象的实例（**参见《原始类型的包装对象》一章）。

```
var obj = Object(1);
obj instanceof Object // true
obj instanceof Number // true

var obj = Object('foo');
obj instanceof Object // true
obj instanceof String // true

var obj = Object(true);
obj instanceof Object // true
obj instanceof Boolean // true
```
上面代码中，`Object`函数的参数是各种原始类型的值，转换成对象就是原始类型值对应的包装对象。

**如果`Object`方法的参数是一个对象，它总是返回该对象，即不用转换**。

```
var arr = [];
var obj = Object(arr); // 返回原数组
obj === arr // true

var value = {};
var obj = Object(value) // 返回原对象
obj === value // true

var fn = function () {};
var obj = Object(fn); // 返回原函数
obj === fn // true
```
上面的代码中使用严格相等运算符对对象进行判断，我们知道严格相等运算符比较两个原始类型时比较的是它们的值，而比较两个对象时比较的是它们的引用地址，因此上面的代码中返回了`true`就可以说明，如果`Object`方法的参数是一个对象，则它总是返回**原**对象。

利用这一点，可以写一个**判断变量是否为对象的函数。**

```
function isObject(value) {
  return value === Object(value);
}

isObject([]) // true
isObject(true) // false
```

## Object 构造函数

**`Object`不仅可以当作工具函数使用，还可以当作构造函数使用，即前面可以使用`new`命令。**

`Object`构造函数的**首要用途，是直接通过它来生成新对象。**

注意，**通过`var obj = new Object()`的写法生成新对象，与字面量的写法`var obj = {}`是等价的**。或者说，后者只是前者的一种简便写法。

`Object`构造函数的用法与工具方法很相似，几乎一模一样。使用时，可以接受一个参数，如果该参数是一个对象，则直接返回这个对象；如果是一个原始类型的值，则返回该值对应的包装对象（详见《包装对象》一章）。

```
var o1 = {a: 1};
var o2 = new Object(o1);
o1 === o2 // true

var obj = new Object(123);
obj instanceof Number // true
```

虽然用法相似，但是`Object(value)`与`new Object(value)`两者的语义是不同的，**`Object(value)`表示将`value`转成一个对象，`new Object(value)`则表示新生成一个对象，它的值是`value`。**

## Object 的静态方法

**所谓“静态方法”，是指部署在`Object`对象自身的方法。**

### Object.keys()，Object.getOwnPropertyNames()

`Object.keys`方法和`Object.getOwnPropertyNames`方法都**用来遍历对象的属性。**

`Object.keys`方法的参数是一个对象，返回一个数组。该数组的成员都是该对象**<font color="red">自身的（而不是继承的）</font>**所有属性名。

```
var obj = {
  p1: 123,
  p2: 456
};

Object.keys(obj) // ["p1", "p2"]
```
`Object.getOwnPropertyNames`方法与`Object.keys`类似，也是接受一个对象作为参数，返回一个数组，包含了该对象**自身**的所有属性名。

```
var obj = {
  p1: 123,
  p2: 456
};

Object.getOwnPropertyNames(obj) // ["p1", "p2"]
```

对于一般的对象来说，`Object.keys()`和`Object.getOwnPropertyNames()`返回的结果是一样的。只有涉及不可枚举属性时，才会有不一样的结果。**`Object.keys`方法只返回可枚举的属性（详见《对象属性的描述对象》一章），`Object.getOwnPropertyNames`方法还返回不可枚举的属性名。**

```
var a = ['Hello', 'World'];

Object.keys(a) // ["0", "1"]
Object.getOwnPropertyNames(a) // ["0", "1", "length"]
```
上面代码中，数组的`length`属性是不可枚举的属性，所以只出现在`Object.getOwnPropertyNames`方法的返回结果中。

由于 JavaScript 没有提供计算对象属性个数的方法，所以可以用这两个方法代替。

```
var obj = {
  p1: 123,
  p2: 456
};

Object.keys(obj).length // 2
Object.getOwnPropertyNames(obj).length // 2
```
一般情况下，**几乎总是使用`Object.keys`方法，遍历数组的属性。**

### 其他方法

除了上面提到的两个方法，`Object`还有不少其他静态方法，将在后文逐一详细介绍。

#### 对象属性模型的相关方法

> `Object.getOwnPropertyDescriptor()`：获取某个属性的描述对象。
> `Object.defineProperty()`：通过描述对象，定义某个属性。
> `Object.defineProperties()`：通过描述对象，定义多个属性。

#### 控制对象状态的方法

> `Object.preventExtensions()`：防止对象扩展。
> `Object.isExtensible()`：判断对象是否可扩展。
> `Object.seal()`：禁止对象配置。
> `Object.isSealed()`：判断一个对象是否可配置。
> `Object.freeze()`：冻结一个对象。
> `Object.isFrozen()`：判断一个对象是否被冻结。

#### 原型链相关方法

> `Object.create()`：该方法可以指定原型对象和属性，返回一个新的对象。
> `Object.getPrototypeOf()`：获取对象的`Prototype`对象。

## Object 的实例方法

**除了静态方法，还有不少方法定义在`Object.prototype`对象。它们称为实例方法，所有`Object`的实例对象都继承了这些方法。**

`Object`实例对象的方法，主要有以下六个。

`Object.prototype.valueOf()`：返回当前对象对应的值。
`Object.prototype.toString()`：返回当前对象对应的字符串形式。
`Object.prototype.toLocaleString()`：返回当前对象对应的本地字符串形式。
`Object.prototype.hasOwnProperty()`：判断某个属性是否为当前对象自身的属性，还是继承自原型对象的属性。
`Object.prototype.isPrototypeOf()`：判断当前对象是否为另一个对象的原型。
`Object.prototype.propertyIsEnumerable()`：判断某个属性是否可枚举。

本节介绍前四个方法，另外两个方法将在后文相关章节介绍。

### Object.prototype.valueOf()

`valueOf`方法的作用是返回一个对象的“值”，默认情况下返回对象本身。

```
var obj = new Object();
obj.valueOf() === obj // true
```
上面代码比较`obj.valueOf()`与`obj`本身，两者是一样的。

**`valueOf`方法的主要用途是，JavaScript 自动类型转换时会默认调用这个方法**（详见《数据类型转换》一章）。

```
var obj = new Object();
1 + obj // "1[object Object]"
```
上面代码将对象`obj`与数字`1`相加，这时 JavaScript 就会默认调用`valueOf()`方法，求出`obj`的值再与`1`相加。所以，如果自定义`valueOf`方法，就可以得到想要的结果。

```
var obj = new Object();
obj.valueOf = function () {
  return 2;
};

1 + obj // 3
```
上面代码自定义了`obj`对象的`valueOf`方法，于是`1 + obj`就得到了`3`。这种方法就相当于用自定义的`obj.valueOf`，覆盖`Object.prototype.valueOf`。

### Object.prototype.toString()

`toString`方法的作用是**返回一个对象的字符串形式，默认情况下返回类型字符串。**

```
var o1 = new Object();
o1.toString() // "[object Object]"

var o2 = {a:1};
o2.toString() // "[object Object]"
```
上面代码表示，对于一个对象调用`toString`方法，会返回字符串`[object Object]`，该字符串说明对象的类型。

字符串`[object Object]`本身没有太大的用处，但是通过自定义`toString`方法，可以让对象在自动类型转换时，得到想要的字符串形式。

```
var obj = new Object();

obj.toString = function () {
  return 'hello';
};

obj + ' ' + 'world' // "hello world"
```
上面代码表示，当对象用于字符串加法时，会自动调用`toString`方法。由于自定义了`toString`方法，所以返回字符串`hello world`。

数组、字符串、函数、`Date` 对象都分别部署了自定义的`toString`方法，覆盖了`Object.prototype.toString`方法。

```
[1, 2, 3].toString() // "1,2,3"

'123'.toString() // "123"

(function () {
  return 123;
}).toString()
// "function () {
//   return 123;
// }"

(new Date()).toString()
// "Tue May 10 2016 09:11:31 GMT+0800 (CST)"
```
上面代码中，数组、字符串、函数、`Date` 对象调用`toString`方法，并不会返回`[object Object]`，因为它们都自定义了`toString`方法，覆盖原始方法。

### toString() 的应用：判断数据类型

`Object.prototype.toString`方法返回对象的类型字符串，因此可以用来判断一个值的类型。

```
var obj = {};
obj.toString() // "[object Object]"
```
上面代码调用空对象的`toString`方法，结果返回一个字符串`object Object`，其中**第二个`Object`表示该值的构造函数。这是一个十分有用的判断数据类型的方法。**

由于实例对象可能会自定义`toString`方法，覆盖掉`Object.prototype.toString`方法，所以**为了得到类型字符串，最好直接使用`Object.prototype.toString`方法。通过函数的`call`方法，可以在任意值上调用这个方法，帮助我们判断这个值的类型**。

```
Object.prototype.toString.call(value)
```
上面代码表示对`value`这个值调用`Object.prototype.toString`方法。

不同数据类型的`Object.prototype.toString`方法返回值如下。

```
数值：返回[object Number]。
字符串：返回[object String]。
布尔值：返回[object Boolean]。
undefined：返回[object Undefined]。
null：返回[object Null]。
数组：返回[object Array]。
arguments 对象：返回[object Arguments]。
函数：返回[object Function]。
Error 对象：返回[object Error]。
Date 对象：返回[object Date]。
RegExp 对象：返回[object RegExp]。
其他对象：返回[object Object]。
```
这就是说，`Object.prototype.toString`可以看出一个值到底是什么类型。

```
Object.prototype.toString.call(2) // "[object Number]"
Object.prototype.toString.call('') // "[object String]"
Object.prototype.toString.call(true) // "[object Boolean]"
Object.prototype.toString.call(undefined) // "[object Undefined]"
Object.prototype.toString.call(null) // "[object Null]"
Object.prototype.toString.call(Math) // "[object Math]"
Object.prototype.toString.call({}) // "[object Object]"
Object.prototype.toString.call([]) // "[object Array]"
```

利用这个特性，**可以写出一个比`typeof`运算符更准确的类型判断函数。**

```
var type = function (o){
  var s = Object.prototype.toString.call(o);
  return s.match(/\[object (.*?)\]/)[1].toLowerCase();
};

type({}); // "object"
type([]); // "array"
type(5); // "number"
type(null); // "null"
type(); // "undefined"
type(/abcd/); // "regex"
type(new Date()); // "date"
```
在上面这个`type`函数的基础上，还可以加上专门判断某种类型数据的方法。

```
var type = function (o){
  var s = Object.prototype.toString.call(o);
  return s.match(/\[object (.*?)\]/)[1].toLowerCase();
};

['Null',
 'Undefined',
 'Object',
 'Array',
 'String',
 'Number',
 'Boolean',
 'Function',
 'RegExp'
].forEach(function (t) {
  type['is' + t] = function (o) {
    return type(o) === t.toLowerCase();
  };
});

type.isObject({}) // true
type.isNumber(NaN) // true
type.isRegExp(/abc/) // true
```

### Object.prototype.toLocaleString()

**`Object.prototype.toLocaleString`方法与`toString`的返回结果相同，也是返回一个值的字符串形式。**

```
var obj = {};
obj.toString(obj) // "[object Object]"
obj.toLocaleString(obj) // "[object Object]"
```

这个方法的主要作用是留出一个接口，让各种不同的对象实现自己版本的`toLocaleString`，用来返回针对某些地域的特定的值。目前，主要有三个对象自定义了`toLocaleString`方法。

> Array.prototype.toLocaleString()
> Number.prototype.toLocaleString()
> Date.prototype.toLocaleString()

举例来说，日期的实例对象的`toString`和`toLocaleString`返回值就不一样，而且`toLocaleString`的返回值跟用户设定的所在地域相关。

```
var date = new Date();
date.toString() // "Tue Jan 01 2018 12:01:33 GMT+0800 (CST)"
date.toLocaleString() // "1/01/2018, 12:01:33 PM"
```

### Object.prototype.hasOwnProperty()

`Object.prototype.hasOwnProperty`方法**接受一个字符串作为参数，返回一个布尔值，表示<font color="red">该实例对象自身是否具有该属性。</font>**

```
var obj = {
  p: 123
};

obj.hasOwnProperty('p') // true
obj.hasOwnProperty('toString') // false
```
上面代码中，对象`obj`自身具有`p`属性，所以返回`true`。`toString`属性是继承的，所以返回`false`。

# Array 对象

## 构造函数

`Array`是 JavaScript 的原生对象，同时也是一个构造函数，可以用它生成新的数组。

```
var arr = new Array(2);
arr.length // 2
arr // [ empty  x 2 ] // 这里是empty代表是空位，虽然浏览器控制台输出为undefined，但它与undefined是不同的
```
上面代码中，`Array`构造函数的参数`2`，表示生成一个两个成员的数组，每个位置都是空值。

如果没有使用`new`，运行结果也是一样的。

```
var arr = new Array(2);
// 等同于
var arr = Array(2);
```
`Array`构造函数有一个很大的缺陷，就是不同的参数，会导致它的行为不一致。

```
// 无参数时，返回一个空数组
new Array() // []

// 单个正整数参数，表示返回的新数组的长度
new Array(1) // [ empty ]
new Array(2) // [ empty x 2 ]

// 非正整数的数值作为参数，会报错
new Array(3.2) // RangeError: Invalid array length
new Array(-3) // RangeError: Invalid array length

// 单个非数值（比如字符串、布尔值、对象等）作为参数，
// 则该参数是返回的新数组的成员
new Array('abc') // ['abc']
new Array([1]) // [Array[1]]，创建了一个包含了一个元素的数组，该元素是个数组包含了一个元素1

// 多参数时，所有参数都是返回的新数组的成员
new Array(1, 2) // [1, 2]
new Array('a', 'b', 'c') // ['a', 'b', 'c']
```

可以看到，`Array`作为构造函数，行为很不一致。因此，**不建议使用它生成新数组，直接使用数组字面量是更好的做法。**

```
// bad
var arr = new Array(1, 2);

// good
var arr = [1, 2];
```

注意，如果参数是一个正整数，返回数组的成员都是空位。虽然读取的时候返回`undefined`，但实际上该位置没有任何值。虽然可以取到`length`属性，但是取不到键名。

```
var a = new Array(3);
var b = [undefined, undefined, undefined];

a.length // 3
b.length // 3

a[0] // undefined
b[0] // undefined

0 in a // false
0 in b // true
```
上面代码中，`a`是一个长度为`3`的空数组，`b`是一个三个成员都是`undefined`的数组。**读取键值的时候，`a`和`b`都返回`undefined`，但是`a`的键位都是空的，`b`的键位是有值的。**

## 静态方法

### Array.isArray()

**`Array.isArray`方法返回一个布尔值，表示参数是否为数组。**它可以弥补`typeof`运算符的不足。

```
var arr = [1, 2, 3];

typeof arr // "object"
Array.isArray(arr) // true
```

上面代码中，`typeof`运算符只能显示数组的类型是`Object`，而`Array.isArray`方法可以识别数组。

## 实例方法

### valueOf()，toString()

`valueOf`方法是一个**所有对象都拥有**的方法，**表示对该对象求值**。不同对象的`valueOf`方法不尽一致，**数组的`valueOf`方法返回数组本身。**

```
var arr = [1, 2, 3];
arr.valueOf() // [1, 2, 3]
```

`toString`方法也是对象的通用方法，**数组的`toString`方法返回数组的字符串形式**。

```
var arr = [1, 2, 3];
arr.toString() // "1,2,3"

var arr = [1, 2, 3, [4, 5, 6]];
arr.toString() // "1,2,3,4,5,6"
```

### push()，pop()

`push`方法**用于在数组的末端添加一个或多个元素，并返回添加新元素后的数组长度。注意，该方法会改变原数组**。

```
var arr = [];

arr.push(1) // 1
arr.push('a') // 2
arr.push(true, {}) // 4
arr // [1, 'a', true, {}]
```
上面代码使用`push`方法，往数组中添加了四个成员。

`pop`方法**用于删除数组的最后一个元素，并返回该元素。注意，该方法会改变原数组。**

```
var arr = ['a', 'b', 'c'];

arr.pop() // 'c'
arr // ['a', 'b']
```

**对空数组使用`pop`方法，不会报错，而是返回`undefined`。**

```
[].pop() // undefined
```

`push`和`pop`结合使用，就构成了“后进先出”的栈结构（stack）。

```
var arr = [];
arr.push(1, 2);
arr.push(3);
arr.pop();
arr // [1, 2]
```
上面代码中，`3`是最后进入数组的，但是最早离开数组。

### shift()，unshift()

`shift`方法**用于删除数组的第一个元素，并返回该元素。注意，该方法会改变原数组。**

```
var a = ['a', 'b', 'c'];

a.shift() // 'a'
a // ['b', 'c']
```

`shift`方法可以遍历并清空一个数组。

```
var list = [1, 2, 3, 4, 5, 6];
var item;

while (item = list.shift()) { // 当list中没有元素的时候，使用list.shift()返回undefined
  console.log(item);
}

list // []
```

`push`和`shift`结合使用，就构成了“先进先出”的队列结构（queue）。

```
var arr = [];
arr.push(1, 2);
arr.push(3);
arr.shift();
arr // [2, 3]
```
上面代码中，`3`是最后进入数组的，也是最后离开数组；`1`是最先进入数组的，也最先离开数组。

`unshift`方法**用于在数组的第一个位置添加元素，并返回添加新元素后的数组长度。注意，该方法会改变原数组。**

```
var a = ['a', 'b', 'c'];

a.unshift('x'); // 4
a // ['x', 'a', 'b', 'c']
```

`unshift`方法可以接受多个参数，这些参数都会添加到目标数组头部。

```
var arr = [ 'c', 'd' ];
arr.unshift('a', 'b') // 4
arr // [ 'a', 'b', 'c', 'd' ]
```

### join()

`join`方法**以指定参数作为分隔符，将所有数组成员连接为一个字符串返回。如果不提供参数，默认用逗号分隔。**

```
var a = [1, 2, 3, 4];

a.join(' ') // '1 2 3 4'
a.join(' | ') // "1 | 2 | 3 | 4"
a.join() // "1,2,3,4"
```

**如果数组成员是`undefined`或`null`或空位，会被转成空字符串。**

```
[undefined, null].join('#')
// '#'

['a',, 'b'].join('-')
// 'a--b'
```

通过`call`方法，这个方法也可以用于字符串或类似数组的对象。

```
Array.prototype.join.call('hello', '-')
// "h-e-l-l-o"

var obj = { 0: 'a', 1: 'b', length: 2 };
Array.prototype.join.call(obj, '-')
// 'a-b'
```

### concat()

`concat`方法**用于多个数组的合并。它将新数组的成员，添加到原数组成员的后部，然后返回一个新数组，原数组不变。**

```
['hello'].concat(['world'])
// ["hello", "world"]

['hello'].concat(['world'], ['!'])
// ["hello", "world", "!"]

[].concat({a: 1}, {b: 2})
// [{ a: 1 }, { b: 2 }]

[2].concat({a: 1})
// [2, {a: 1}]
```

除了数组作为参数，**`concat`也接受其他类型的值作为参数，添加到目标数组尾部。**

```
[1, 2, 3].concat(4, 5, 6)
// [1, 2, 3, 4, 5, 6]
```

**如果数组成员包括对象，`concat`方法返回当前数组的一个浅拷贝。所谓“浅拷贝”，指的是新数组拷贝的是对象的引用。**

```
var obj = { a: 1 };
var oldArray = [obj];

var newArray = oldArray.concat();

obj.a = 2;
newArray[0].a // 2
```
上面代码中，原数组包含一个对象，`concat`方法生成的新数组包含这个对象的引用。所以，改变原对象以后，新数组跟着改变。

### reverse()

`reverse`方法**用于颠倒排列数组元素，返回改变后的数组。注意，该方法将改变原数组。**

```
var a = ['a', 'b', 'c'];

a.reverse() // ["c", "b", "a"]
a // ["c", "b", "a"]
```

### slice()

`slice`方法**用于提取目标数组的一部分，返回一个新数组，原数组不变。**

```
arr.slice(start, end);
```
**它的第一个参数为起始位置（从`0`开始），第二个参数为终止位置（但<font color="red">该位置的元素本身不包括在内</font>）。如果省略第二个参数，则一直返回到原数组的最后一个成员。**

```
var a = ['a', 'b', 'c'];

a.slice(0) // ["a", "b", "c"]
a.slice(1) // ["b", "c"]
a.slice(1, 2) // ["b"]
a.slice(2, 6) // ["c"]
a.slice() // ["a", "b", "c"]
```
上面代码中，**最后一个例子`slice`没有参数，实际上等于返回一个原数组的拷贝。**

**如果`slice`方法的参数是负数，则表示倒数计算的位置。**

```
var a = ['a', 'b', 'c'];
a.slice(-2) // ["b", "c"]
a.slice(-2, -1) // ["b"]
a.slice(-3, -1) // ["a", "b"]
a.slice(-1) // ["c"]
```
上面代码中，`-2`表示倒数计算的第二个位置，`-1`表示倒数计算的第一个位置。

**如果第一个参数大于等于数组长度，或者第二个参数小于第一个参数，则返回空数组。**

```
var a = ['a', 'b', 'c'];
a.slice(4) // []
a.slice(2, 1) // []
```

**`slice`方法的一个重要应用，是将类似数组的对象转为真正的数组。**

```
Array.prototype.slice.call({ 0: 'a', 1: 'b', length: 2 })
// ['a', 'b']

Array.prototype.slice.call(document.querySelectorAll("div"));
Array.prototype.slice.call(arguments);
```
上面代码的参数都不是数组，但是通过`call`方法，在它们上面调用`slice`方法，就可以把它们转为真正的数组。

### splice()

`splice`方法**用于删除原数组的一部分成员，并可以在删除的位置添加新的数组成员，返回值是被删除的元素。注意，该方法会改变原数组。**

```
arr.splice(start, count, addElement1, addElement2, ...);
```

**`splice`的第一个参数是删除的起始位置（从`0`开始），第二个参数是被删除的元素个数。如果后面还有更多的参数，则表示这些就是要被插入数组的新元素。**

```
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(4, 2) // ["e", "f"]
a // ["a", "b", "c", "d"]
```
上面代码从原数组`4`号位置，删除了两个数组成员。

```
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(4, 2, 1, 2) // ["e", "f"]
a // ["a", "b", "c", "d", 1, 2]
```
上面代码除了删除成员，还插入了两个新成员。

起始位置如果是负数，就表示从倒数位置开始删除。

```
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(-4, 2) // ["c", "d"]

// 或
a.splice(-4, 6) // ["c", "d", "e", "f"]

// 或
a.splice(-4, 4) // ["c", "d", "e", "f"]
```
上面代码表示，从倒数第四个位置`c`开始删除两个成员。

如果只是单纯地插入元素，`splice`方法的第二个参数可以设为`0`。

```
var a = [1, 1, 1];

a.splice(1, 0, 2) // []
a // [1, 2, 1, 1]
```
上面代码表示在数组`a`下标为`1`的位置插入一个新元素`2`。

如果只提供第一个参数，等同于将原数组在指定位置拆分成两个数组。

```
var a = [1, 2, 3, 4];
a.splice(2) // [3, 4]
a // [1, 2]
```
上面的代码表示从数组`a`下标为`2`的地方开始连带它之后的元素为一个数组，`a`中只剩余它之前的元素。

### sort()

`sort`方法**对数组成员进行排序，默认是按照字典顺序排序。排序后，原数组将被改变。**

```
['d', 'c', 'b', 'a'].sort()
// ['a', 'b', 'c', 'd']

[4, 3, 2, 1].sort()
// [1, 2, 3, 4]

[11, 101].sort()
// [101, 11]

[10111, 1101, 111].sort()
// [10111, 1101, 111]
```
上面代码的最后两个例子，需要特殊注意。**`sort`方法不是按照大小排序，而是按照字典顺序。也就是说，<font color="red">数值会被先转成字符串，再按照字典顺序进行比较</font>**，所以`101`排在`11`的前面。

如果想让`sort`方法按照自定义方式排序，可以传入一个函数作为参数。

```
[10111, 1101, 111].sort(function (a, b) {
  return a - b;
})
// [111, 1101, 10111]
```
上面代码中，`sort`的参数函数本身接受两个参数，表示进行比较的两个数组成员。**如果该函数的返回值大于`0`，表示第一个成员排在第二个成员后面；其他情况下，都是第一个元素排在第二个元素前面。**

```
[
  { name: "张三", age: 30 },
  { name: "李四", age: 24 },
  { name: "王五", age: 28  }
].sort(function (o1, o2) {
  return o1.age - o2.age;
})
// [
//   { name: "李四", age: 24 },
//   { name: "王五", age: 28  },
//   { name: "张三", age: 30 }
// ]
```

### map()

`map`方法**将数组的所有成员依次传入参数函数，然后把每一次的执行结果组成一个新数组返回。**

```
var numbers = [1, 2, 3];

numbers.map(function (n) {
  return n + 1;
});
// [2, 3, 4]

numbers
// [1, 2, 3]
```
上面代码中，`numbers`数组的所有成员依次执行参数函数，运行结果组成一个新数组返回，**原数组没有变化**。

`map`方法接受一个函数作为参数。**该函数调用时，`map`方法向它传入三个参数：当前成员、当前位置和数组本身**。

```
[1, 2, 3].map(function(elem, index, arr) {
  return elem * index;
});
// [0, 2, 6]
```
上面代码中，`map`方法的回调函数有三个参数，`elem`为当前成员的值，`index`为当前成员的位置，`arr`为原数组（`[1, 2, 3]`）。

`map`方法还可以接受第二个参数，用来绑定回调函数内部的`this`变量（详见《this 变量》一章）。

```
var arr = ['a', 'b', 'c'];

[1, 2].map(function (e) {
  return this[e];
}, arr)
// ['b', 'c']
```
上面代码通过`map`方法的第二个参数，将回调函数内部的`this`对象，指向`arr`数组。

**如果数组有空位，`map`方法的回调函数在这个位置不会执行，<font color="red">会跳过数组的空位</font>**。

```
var f = function (n) { return 'a' };

[1, undefined, 2].map(f) // ["a", "a", "a"]
[1, null, 2].map(f) // ["a", "a", "a"]
[1, , 2].map(f) // ["a", , "a"]
```
上面代码中，**<font color="red">`map`方法不会跳过`undefined`和`null`，但是会跳过空位。</font>**

### forEach()

**`forEach`方法与`map`方法很相似，也是对数组的所有成员依次执行参数函数。但是，`forEach`方法不返回值，只用来操作数据。这就是说，如果数组遍历的目的是为了得到返回值，那么使用`map`方法，否则使用`forEach`方法。**

`forEach`的用法与`map`方法一致，参数是一个函数，该函数同样接受三个参数：当前值、当前位置、整个数组。

```
function log(element, index, array) {
  console.log('[' + index + '] = ' + element);
}

[2, 5, 9].forEach(log);
// [0] = 2
// [1] = 5
// [2] = 9
```
上面代码中，`forEach`遍历数组不是为了得到返回值，而是为了在屏幕输出内容，所以不必使用`map`方法。

`forEach`方法也可以接受第二个参数，绑定参数函数的`this`变量。

```
var out = [];

[1, 2, 3].forEach(function(elem) {
  this.push(elem * elem);
}, out);

out // [1, 4, 9]
```
上面代码中，空数组`out`是`forEach`方法的第二个参数，结果，回调函数内部的`this`关键字就指向`out`。

注意，**`forEach`方法无法中断执行，总是会将所有成员遍历完。如果希望符合某种条件时，就中断遍历，要使用`for`循环。**

```
var arr = [1, 2, 3];

for (var i = 0; i < arr.length; i++) {
  if (arr[i] === 2) break;
  console.log(arr[i]);
}
// 1
```
上面代码中，执行到数组的第二个成员时，就会中断执行。`forEach`方法做不到这一点。

**`forEach`方法也会跳过数组的<font color="red">空位</font>**。

```
var log = function (n) {
  console.log(n + 1);
};

[1, undefined, 2].forEach(log)
// 2
// NaN
// 3

[1, null, 2].forEach(log)
// 2
// 1
// 3

[1, , 2].forEach(log)
// 2
// 3
```
上面代码中，`forEach`方法不会跳过`undefined`和`null`，但会跳过空位。

### filter()

`filter`方法**用于过滤数组成员，满足条件的成员组成一个新数组返回。**

**它的参数是一个函数，所有数组成员依次执行该函数，返回结果为`true`的成员组成一个新数组返回。该方法不会改变原数组。**

```
[1, 2, 3, 4, 5].filter(function (elem) {
  return (elem > 3);
})
// [4, 5]
```
上面代码将大于`3`的数组成员，作为一个新数组返回。

```
var arr = [0, 1, 'a', false];

arr.filter(Boolean)
// [1, "a"]
```
上面代码中，`filter`方法返回数组`arr`里面所有布尔值为`true`的成员。

`filter`方法的参数函数可以接受三个参数：当前成员，当前位置和整个数组。

```
[1, 2, 3, 4, 5].filter(function (elem, index, arr) {
  return index % 2 === 0;
});
// [1, 3, 5]
```
上面代码返回索引值为偶数，也就是奇数位置的成员组成的新数组。

`filter`方法还可以接受第二个参数，用来绑定参数函数内部的`this`变量。

```
var obj = { MAX: 3 };
var myFilter = function (item) {
  if (item > this.MAX) return true;
};

var arr = [2, 8, 3, 4, 1, 3, 2, 9];
arr.filter(myFilter, obj) // [8, 4, 9]
```
上面代码中，过滤器`myFilter`内部有`this`变量，它可以被`filter`方法的第二个参数`obj`绑定，返回大于`3`的成员。

### some()，every()

这两个方法**类似“断言”（assert），返回一个布尔值，表示判断数组成员是否符合某种条件。**

**它们接受一个函数作为参数，所有数组成员依次执行该函数。该函数接受三个参数：当前成员、当前位置和整个数组，然后返回一个布尔值。**

**`some`方法是<font color="red">只要一个成员的返回值是`true`，则整个`some`方法的返回值就是`true`，否则返回`false`</font>。**

```
var arr = [1, 2, 3, 4, 5];
arr.some(function (elem, index, arr) {
  return elem >= 3;
});
// true
```
上面代码中，如果数组`arr`有一个成员大于等于`3`，`some`方法就返回`true`。

**`every`方法是<font color="red">所有成员的返回值都是`true`，整个`every`方法才返回`true`，否则返回`false`</font>。**

```
var arr = [1, 2, 3, 4, 5];
arr.every(function (elem, index, arr) {
  return elem >= 3;
});
// false
```
上面代码中，数组`arr`并非所有成员大于等于`3`，所以返回`false`。

注意，**对于空数组，`some`方法返回`false`，`every`方法返回`true`，回调函数都不会执行。**

```
function isEven(x) { alert("test!"); return x % 2 === 0 }

[].some(isEven) // false
[].every(isEven) // true
```
上面的代码中，那句alert不会执行，因此可以得知无论返回`true`还是`false`回调函数都不会执行。

`some`和`every`方法还可以接受第二个参数，用来绑定参数函数内部的`this`变量。

### reduce()，reduceRight()

`reduce`方法和`reduceRight`方法**依次处理数组的每个成员，最终累计为一个值。它们的差别是，`reduce`是从左到右处理（从第一个成员到最后一个成员），`reduceRight`则是从右到左（从最后一个成员到第一个成员），其他完全一样。**

```
[1, 2, 3, 4, 5].reduce(function (a, b) {
  console.log(a, b);
  return a + b;
})
// 1 2
// 3 3
// 6 4
// 10 5
//最后结果：15
```
上面代码中，`reduce`方法求出数组所有成员的和。第一次执行，`a`是数组的第一个成员`1`，`b`是数组的第二个成员`2`。第二次执行，`a`为上一轮的返回值`3`，`b`为第三个成员`3`。第三次执行，`a`为上一轮的返回值`6`，`b`为第四个成员`4`。第四次执行，`a`为上一轮返回值`10`，`b`为第五个成员`5`。至此所有成员遍历完成，整个方法的返回值就是最后一轮的返回值`15`。

`reduce`方法和`reduceRight`方法的第一个参数都是一个函数。该函数接受以下四个参数。

> 累积变量，默认为数组的第一个成员
> 当前变量，默认为数组的第二个成员
> 当前位置（从0开始）
> 原数组

这四个参数之中，只有前两个是必须的，后两个则是可选的。

如果要对累积变量指定初值，可以把它放在`reduce`方法和`reduceRight`方法的第二个参数。

```
[1, 2, 3, 4, 5].reduce(function (a, b) {
  return a + b;
}, 10);
// 25
```
上面代码指定参数`a`的初值为`10`，所以数组从`10`开始累加，最终结果为`25`。注意，**<font color="red">这时`b`是从数组的第一个成员开始遍历</font>。**

上面的**第二个参数相当于设定了默认值，处理空数组时尤其有用**。

```
function add(prev, cur) {
  return prev + cur;
}

[].reduce(add)
// TypeError: Reduce of empty array with no initial value
[].reduce(add, 1)
// 1
```
上面代码中，由于空数组取不到初始值，`reduce`方法会报错。这时，加上第二个参数，就能保证总是会返回一个值。

下面是一个`reduceRight`方法的例子。

```
function substract(prev, cur) {
  return prev - cur;
}

[3, 2, 1].reduce(substract) // 0
[3, 2, 1].reduceRight(substract) // -4
```
上面代码中，`reduce`方法相当于`3`减去`2`再减去`1`，`reduceRight`方法相当于`1`减去`2`再减去`3`。

**由于这两个方法会遍历数组，所以实际上还可以用来做一些遍历相关的操作**。比如，找出字符长度最长的数组成员。

```
function findLongest(entries) {
  return entries.reduce(function (longest, entry) {
    return entry.length > longest.length ? entry : longest;
  }, '');
}

findLongest(['aaa', 'bb', 'c']) // "aaa"
```
上面代码中，`reduce`的参数函数会将字符长度较长的那个数组成员，作为累积值。这导致遍历所有成员之后，累积值就是字符长度最长的那个成员。

### indexOf()，lastIndexOf()

`indexOf`方法**返回给定元素在数组中第一次出现的位置，如果没有出现则返回`-1`。**

```
var a = ['a', 'b', 'c'];

a.indexOf('b') // 1
a.indexOf('y') // -1
```

`indexOf`方法还可以接受第二个参数，表示搜索的开始位置。

```
['a', 'b', 'c'].indexOf('a', 1) // -1
```
上面代码从`1`号位置开始搜索字符`a`，结果为`-1`，表示没有搜索到。

`lastIndexOf`方法**返回给定元素在数组中最后一次出现的位置，如果没有出现则返回`-1`。**

```
var a = [2, 5, 9, 2];
a.lastIndexOf(2) // 3
a.lastIndexOf(7) // -1
```

注意，这两个方法不能用来搜索`NaN`的位置，即它们无法确定数组成员是否包含`NaN`。

```
[NaN].indexOf(NaN) // -1
[NaN].lastIndexOf(NaN) // -1
```
这是**因为这两个方法内部，<font color="red">使用严格相等运算符（`===`）进行比较，而`NaN`是唯一一个不等于自身的值</font>。**

### 链式使用

上面这些数组方法之中，有不少返回的还是数组，所以可以链式使用。

```
var users = [
  {name: 'tom', email: 'tom@example.com'},
  {name: 'peter', email: 'peter@example.com'}
];

users
.map(function (user) {
  return user.email;
})
.filter(function (email) {
  return /^t/.test(email);
})
.forEach(console.log);
// "tom@example.com"
```
上面代码中，先产生一个所有 Email 地址组成的数组，然后再过滤出以`t`开头的 Email 地址。

# 包装对象

## 定义

对象是 JavaScript 语言最主要的数据类型，**三种原始类型的值——数值、字符串、布尔值——在一定条件下，也会自动转为对象，也就是原始类型的“包装对象”。**

所谓“包装对象”，就是分别与数值、字符串、布尔值相对应的`Number`、`String`、`Boolean`三个原生对象。这三个原生对象可以把原始类型的值变成（包装成）对象。

```
var v1 = new Number(123);
var v2 = new String('abc');
var v3 = new Boolean(true);
```
上面代码中，基于原始类型的值，生成了三个对应的包装对象。

```
typeof v1 // "object"
typeof v2 // "object"
typeof v3 // "object"

v1 === 123 // false
v2 === 'abc' // false
v3 === true // false
```

包装对象的最大目的，首先是**使得 JavaScript 的对象涵盖所有的值，其次使得原始类型的值可以方便地调用某些方法**。

`Number`、`String`和`Boolean`**如果不作为构造函数调用（即调用时不加`new`），常常用于将任意类型的值转为数值、字符串和布尔值**。

```
Number(123) // 123
String('abc') // "abc"
Boolean(true) // true
```
上面这种数据类型的转换，详见《数据类型转换》一节。

总结一下，**这三个对象<font color="red">作为构造函数使用（带有`new`）时，可以将原始类型的值转为对象；作为普通函数使用时（不带有`new`），可以将任意类型的值，转为原始类型的值</font>**。

## 实例方法

包装对象的实例可以使用`Object`对象提供的原生方法，主要是`valueOf`方法和`toString`方法。

### valueOf()

`valueOf`方法返回包装对象实例对应的原始类型的值。

```
new Number(123).valueOf()  // 123
new String('abc').valueOf() // "abc"
new Boolean(true).valueOf() // true
```

### toString()

`toString`方法返回对应的字符串形式。

```
new Number(123).toString() // "123"
new String('abc').toString() // "abc"
new Boolean(true).toString() // "true"
```

## 原始类型与实例对象的自动转换

原始类型的值，可以自动当作对象调用，即调用各种对象的方法和参数。这时，JavaScript 引擎会自动将原始类型的值转为包装对象实例，在使用后立刻销毁实例。

比如，字符串可以调用`length`属性，返回字符串的长度。

```
'abc'.length // 3
```
上面代码中，`abc`是一个字符串，本身不是对象，不能调用`length`属性。JavaScript 引擎自动将其转为包装对象，在这个对象上调用`length`属性。调用结束后，这个临时对象就会被销毁。这就叫**原始类型与实例对象的自动转换**。

```
var str = 'abc';
str.length // 3

// 等同于
var strObj = new String(str)
// String {
//   0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"
// }
strObj.length // 3
```
上面代码中，字符串`abc`的包装对象提供了多个属性。

**自动转换生成的包装对象是<font color="red">只读</font>的，无法修改。所以，字符串无法添加新属性**。

```
var s = 'Hello World';
s.x = 123;
s.x // undefined
```
上面代码为字符串`s`添加了一个`x`属性，结果无效，总是返回`undefined`。

另一方面，调用结束后，包装对象实例会自动销毁。这意味着，**下一次调用字符串的属性时，实际是调用一个新生成的对象，而不是上一次调用时生成的那个对象，所以取不到赋值在上一个对象的属性**。如果要为字符串添加属性，只有在它的原型对象`String.prototype`上定义（参见《面向对象编程》章节）。

## 自定义方法

三种包装对象除了提供很多原生的实例方法（详见后文的介绍），还**可以在原型上添加自定义方法和属性，供原始类型的值直接调用**。

比如，我们可以新增一个`double`方法，使得字符串和数字翻倍。

```
String.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

'abc'.double()
// abcabc

Number.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

(123).double()
// 246
```

上面代码在`123`外面必须要加上圆括号，否则后面的点运算符（.）会被解释成小数点。

但是，这种自定义方法和属性的机制，**只能定义在包装对象的原型上，如果直接对原始类型的变量添加属性，则无效**。

```
var s = 'abc';

s.p = 123;
s.p // undefined
```
上面代码直接对字符串`abc`添加属性，结果无效。主要原因是上面说的，这里的包装对象是自动生成的，赋值后自动销毁，所以**最后一行实际上调用的是一个新的包装对象**。

## Boolean 对象

### 概述

`Boolean`对象是 JavaScript 的三个包装对象之一。**作为构造函数，它主要用于生成布尔值的包装对象实例**。

```
var b = new Boolean(true);

typeof b // "object"
b.valueOf() // true
```
上面代码的变量`b`是一个`Boolean`对象的实例，它的类型是对象，值为布尔值`true`。

注意，**<font color="red">`false`对应的包装对象实例，布尔运算结果也是`true`</font>**。

```
if (new Boolean(false)) {
  console.log('true');
} // true

if (new Boolean(false).valueOf()) {
  console.log('true');
} // 无输出
```
上面代码的第一个例子之所以得到`true`，是**因为`false`对应的包装对象实例是一个对象，进行逻辑运算时，被自动转化成布尔值`true`（<font color="red">因为所有对象对应的布尔值都是`true`</font>）**。而实例的`valueOf`方法，则返回实例对应的原始值，本例为`false`。

### Boolean 函数的类型转换作用

`Boolean`对象除了可以作为构造函数，还可以单独使用，将任意值转为布尔值。这时`Boolean`就是一个单纯的工具方法。

```
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean('') // false
Boolean(NaN) // false

Boolean(1) // true
Boolean('false') // true
Boolean([]) // true
Boolean({}) // true
Boolean(function () {}) // true
Boolean(/foo/) // true
```
上面代码中几种得到`true`的情况，都值得认真记住。

顺便提一下，使用双重的否运算符（`!`）也可以将任意值转为对应的布尔值。

```
!!undefined // false
!!null // false
!!0 // false
!!'' // false
!!NaN // false
!!1 // true
!!'false' // true
!![] // true
!!{} // true
!!function(){} // true
!!/foo/ // true
```

最后，对于一些特殊值，`Boolean`对象前面加不加`new`，会得到完全相反的结果，必须小心。

```
if (Boolean(false)) {
  console.log('true');
} // 无输出

if (new Boolean(false)) {
  console.log('true');
} // true

if (Boolean(null)) {
  console.log('true');
} // 无输出

if (new Boolean(null)) {
  console.log('true');
} // true
```

# Number对象

## 概述

`Number`对象是**数值对应的包装对象，<font color="red">可以作为构造函数使用，也可以作为工具函数使用</font>**。

**作为构造函数时，它用于生成值为数值的<font color="red">对象</font>。**

```
var n = new Number(1);
typeof n // "object"
```
上面代码中，`Number`对象作为构造函数使用，返回一个值为`1`的对象。

**作为工具函数时，它可以将任何类型的值转为数值**。

```
Number(true) // 1
```
上面代码将布尔值`true`转为数值`1`。`Number`作为工具函数的用法，详见《数据类型转换》一章。

## 属性

`Number`对象拥有以下一些属性。

> `Number.POSITIVE_INFINITY`：正的无限，指向`Infinity`。
> `Number.NEGATIVE_INFINITY`：负的无限，指向`-Infinity`。
> `Number.NaN`：表示非数值，指向`NaN`。
> `Number.MAX_VALUE`：表示最大的正数，相应的，最小的负数为`-Number.MAX_VALUE`。
> `Number.MIN_VALUE`：表示最小的正数（即最接近`0`的正数，在64位浮点数体系中为`5e-324`），相应的，最接近`0`的负数为`-Number.MIN_VALUE`。
> `Number.MAX_SAFE_INTEGER`：表示能够精确表示的最大整数，即`9007199254740991`。
> `Number.MIN_SAFE_INTEGER`：表示能够精确表示的最小整数，即`-9007199254740991`。

```
Number.POSITIVE_INFINITY // Infinity
Number.NEGATIVE_INFINITY // -Infinity
Number.NaN // NaN

Number.MAX_VALUE
// 1.7976931348623157e+308
Number.MAX_VALUE < Infinity
// true

Number.MIN_VALUE
// 5e-324
Number.MIN_VALUE > 0
// true

Number.MAX_SAFE_INTEGER // 9007199254740991
Number.MIN_SAFE_INTEGER // -9007199254740991
```

## 实例方法

`Number`对象有4个实例方法，都跟将数值转换成指定格式有关。

### Number.prototype.toString()

`Number`对象部署了自己的`toString`方法，**用来将一个数值转为字符串形式**。

```
(10).toString() // "10"
```

**`toString`方法可以接受一个参数，表示输出的进制**。如果省略这个参数，默认将数值先转为十进制，再输出字符串；否则，就根据参数指定的进制，将一个数字转化成某个进制的字符串。

```
(10).toString(2) // "1010"
(10).toString(8) // "12"
(10).toString(16) // "a"
```
上面代码中，`10`一定要放在括号里，这样表明后面的点表示调用对象属性。<font color="red">如果不加括号，这个点会被 JavaScript 引擎解释成小数点，从而报错</font>。

```
10.toString(2)
// SyntaxError: Unexpected token ILLEGAL
```

只要能够让 JavaScript 引擎不混淆小数点和对象的点运算符，各种写法都能用。除了为`10`加上括号，还**可以在`10`后面加两个点，<font color="red">JavaScript 会把第一个点理解成小数点（即`10.0`），把第二个点理解成调用对象属性</font>，从而得到正确结果**。

```
10..toString(2)
// "1010"

// 其他方法还包括
10 .toString(2) // "1010"
10.0.toString(2) // "1010"
```

这实际上意味着，可以直接对一个小数使用`toString`方法。

```
10.5.toString() // "10.5"
10.5.toString(2) // "1010.1"
10.5.toString(8) // "12.4"
10.5.toString(16) // "a.8"
```

通过方括号运算符也可以调用`toString`方法。
```
10['toString'](2) // "1010"
```

**`toString`方法<font color="red">只能将十进制的数，转为其他进制的字符串。如果要将其他进制的数，转回十进制，需要使用`parseInt`方法</font>**。

### Number.prototype.toFixed()

`toFixed`方法**先将一个数转为指定位数的小数，然后返回这个小数对应的字符串**。

```
(10).toFixed(2) // "10.00"
10.005.toFixed(2) // "10.01"
```
上面代码中，`10`和`10.005`转成`2`位小数，其中`10`必须放在括号里，否则后面的点会被处理成小数点。

**`toFixed`方法的参数为小数位数，<font color="red">有效范围为`0`到`20`</font>，超出这个范围将抛出 `RangeError` 错误。**

### Number.prototype.toExponential()

`toExponential`方法**用于将一个数转为科学计数法形式**。

```
(10).toExponential()  // "1e+1"
(10).toExponential(1) // "1.0e+1"
(10).toExponential(2) // "1.00e+1"

(1234).toExponential()  // "1.234e+3"
(1234).toExponential(1) // "1.2e+3"
(1234).toExponential(2) // "1.23e+3"
```

`toExponential`方法的**参数是小数点后有效数字的位数，范围为`0`到`20`，超出这个范围，会抛出一个 `RangeError` 错误**。

### Number.prototype.toPrecision()

`toPrecision`方法用于将一个数转为指定位数的有效数字。

```
(12.34).toPrecision(1) // "1e+1"
(12.34).toPrecision(2) // "12"
(12.34).toPrecision(3) // "12.3"
(12.34).toPrecision(4) // "12.34"
(12.34).toPrecision(5) // "12.340"
```

`toPrecision`方法的参数为有效数字的位数，范围是`1`到`21`，超出这个范围会抛出` RangeError `错误。

`toPrecision`方法**用于四舍五入时不太可靠**，跟浮点数不是精确储存有关。

```
(12.35).toPrecision(3) // "12.3"
(12.25).toPrecision(3) // "12.3"
(12.15).toPrecision(3) // "12.2"
(12.45).toPrecision(3) // "12.4"
```

## 自定义方法

与其他对象一样，**`Number.prototype`对象上面可以自定义方法，被`Number`的实例继承**。

```
Number.prototype.add = function (x) {
  return this + x;
};

8['add'](2) // 10
```
上面代码为`Number`对象实例定义了一个`add`方法。在数值上调用某个方法，数值会自动转为`Number`的实例对象，所以就可以调用`add`方法了。由于`add`方法返回的还是数值，所以可以链式运算。

```
Number.prototype.subtract = function (x) {
  return this - x;
};

(8).add(2).subtract(4)
// 6
```
上面代码在`Number`对象的实例上部署了`subtract`方法，它可以与`add`方法链式调用。

我们还可以部署更复杂的方法。

```
Number.prototype.iterate = function () {
  var result = [];
  for (var i = 0; i <= this; i++) {
    result.push(i);
  }
  return result;
};

(8).iterate()
// [0, 1, 2, 3, 4, 5, 6, 7, 8]
```
上面代码在`Number`对象的原型上部署了`iterate`方法，将一个数值自动遍历为一个数组。

注意，数值的自定义方法，只能定义在它的原型对象`Number.prototype`上面，**数值本身是无法自定义属性的**。

```
var n = 1;
n.x = 1;
n.x // undefined
```
上面代码中，`n`是一个原始类型的数值。直接在它上面新增一个属性`x`，不会报错，但毫无作用，总是返回`undefined`。这是**因为一旦被调用属性，`n`就自动转为`Number`的实例对象，调用结束后，该对象自动销毁。所以，下一次调用`n`的属性时，实际取到的是另一个对象，属性`x`当然就读不出来**。

# String对象

## 概述

`String`对象**是 JavaScript 原生提供的三个包装对象之一，用来生成字符串对象**。

```
var s1 = 'abc';
var s2 = new String('abc');

typeof s1 // "string"
typeof s2 // "object"

s2.valueOf() // "abc"
```
上面代码中，变量`s1`是字符串，`s2`是对象。由于`s2`是字符串对象，`s2.valueOf`方法返回的就是它所对应的原始字符串。

**字符串对象是一个类似数组的对象**（很像数组，但不是数组）。

```
new String('abc')
// String {0: "a", 1: "b", 2: "c", length: 3}

(new String('abc'))[1] // "b"
```
上面代码中，字符串`abc`对应的字符串对象，有数值键（`0`、`1`、`2`）和`length`属性，所以可以像数组那样取值。

除了用作构造函数，**`String`对象还可以当作工具方法使用，将任意类型的值转为字符串**。

```
String(true) // "true"
String(5) // "5"
```
上面代码将布尔值`ture`和数值5，分别转换为字符串。

## 静态方法

### String.fromCharCode()

`String`对象提供的**静态方法（即<font color="red">定义在对象本身，而不是定义在对象实例的方法</font>）**，主要是`String.fromCharCode()`。该方法的**参数是一个或多个数值，代表 Unicode 码点，返回值是这些码点组成的字符串**。

```
String.fromCharCode() // ""
String.fromCharCode(97) // "a"
String.fromCharCode(104, 101, 108, 108, 111)
// "hello"
```
上面代码中，`String.fromCharCode`方法的参数为空，就返回空字符串；否则，返回参数对应的 Unicode 字符串。

注意，该方法不支持 Unicode 码点大于`0xFFFF`的字符，即传入的参数不能大于`0xFFFF`（即十进制的 `65535`）。

```
String.fromCharCode(0x20BB7)
// "ஷ"
String.fromCharCode(0x20BB7) === String.fromCharCode(0x0BB7)
// true
```
上面代码中，`String.fromCharCode`参数`0x20BB7`大于`0xFFFF`，导致返回结果出错。`0x20BB7`对应的字符是汉字`𠮷`，但是返回结果却是另一个字符（码点`0x0BB7`）。这是因为`String.fromCharCode`发现参数值大于`0xFFFF`，就**会忽略多出的位**（即忽略`0x20BB7`里面的`2`）。

这种现象的根本原因在于，码点大于`0xFFFF`的字符占用四个字节，而 JavaScript 默认支持两个字节的字符。这种情况下，必须把`0x20BB7`拆成两个字符表示。

```
String.fromCharCode(0xD842, 0xDFB7)
// "𠮷"
```
上面代码中，`0x20BB7`拆成两个字符`0xD842`和`0xDFB7`（即两个两字节字符，合成一个四字节字符），就能得到正确的结果。码点大于`0xFFFF`的字符的四字节表示法，由 UTF-16 编码方法决定。

## 实例属性

### String.prototype.length

字符串实例的`length`属性返回字符串的长度。

```
'abc'.length // 3
```

## 实例方法

### String.prototype.charAt()

`charAt`方法返回指定位置的字符，参数是从`0`开始编号的位置。

```
var s = new String('abc');

s.charAt(1) // "b"
s.charAt(s.length - 1) // "c"
```

这个方法完全可以用数组下标替代。

```
'abc'.charAt(1) // "b"
'abc'[1] // "b"
```

**如果参数为负数，或大于等于字符串的长度，`charAt`返回空字符串**。

```
'abc'.charAt(-1) // ""
'abc'.charAt(3) // ""
```

### String.prototype.charCodeAt()

`charCodeAt`方法**返回字符串指定位置的 Unicode 码点（十进制表示）**，相当于`String.fromCharCode()`的逆操作。

```
'abc'.charCodeAt(1) // 98
```
上面代码中，`abc`的`1`号位置的字符是`b`，它的 Unicode 码点是`98`。

**如果没有任何参数，`charCodeAt`返回首字符的 Unicode 码点**。

```
'abc'.charCodeAt() // 97
```

**如果参数为负数，或大于等于字符串的长度，`charCodeAt`返回`NaN`。**

```
'abc'.charCodeAt(-1) // NaN
'abc'.charCodeAt(4) // NaN
```

注意，`charCodeAt`方法返回的 Unicode 码点不会大于`65536`（`0xFFFF`），也就是说，只返回两个字节的字符的码点。如果遇到码点大于 `65536 `的字符（四个字节的字符），必需连续使用两次`charCodeAt`，不仅读入`charCodeAt(i)`，还要读入`charCodeAt(i+1)`，将两个值放在一起，才能得到准确的字符。

### String.prototype.concat()

`concat`方法**用于连接两个字符串，返回一个新字符串，不改变原字符串**。

```
var s1 = 'abc';
var s2 = 'def';

s1.concat(s2) // "abcdef"
s1 // "abc"
```

该方法可以接受多个参数。

```
'a'.concat('b', 'c') // "abc"
```

**如果参数不是字符串，`concat`方法会将其先转为字符串，然后再连接。**

```
var one = 1;
var two = 2;
var three = '3';

''.concat(one, two, three) // "123"
one + two + three // "33"
```
上面代码中，`concat`方法将参数先转成字符串再连接，所以返回的是一个三个字符的字符串。作为对比，加号运算符在两个运算数都是数值时，不会转换类型，所以返回的是一个两个字符的字符串。

### String.prototype.slice()

`slice`方法**用于从原字符串取出子字符串并返回，不改变原字符串**。它的**第一个参数是子字符串的开始位置，第二个参数是子字符串的结束位置（<font color="red">不含该位置</font>）**。

```
'JavaScript'.slice(0, 4) // "Java"
```

如果**省略第二个参数，则表示子字符串一直到原字符串结束**。

```
'JavaScript'.slice(4) // "Script"
```

如果**参数是负值，表示从结尾开始倒数计算的位置，即该负值加上字符串长度**。

```
'JavaScript'.slice(-6) // "Script"
'JavaScript'.slice(0, -6) // "Java"
'JavaScript'.slice(-2, -1) // "p"
```

如果**第一个参数大于第二个参数，`slice`方法返回一个空字符串**。

```
'JavaScript'.slice(2, 1) // ""
```

### String.prototype.substring()

`substring`方法**用于从原字符串取出子字符串并返回，不改变原字符串**，跟`slice`方法很相像。它的**第一个参数表示子字符串的开始位置，第二个位置表示结束位置（<font color="red">返回结果不含该位置</font>）**。

```
'JavaScript'.substring(0, 4) // "Java"
```

如果**省略第二个参数，则表示子字符串一直到原字符串的结束**。

```
'JavaScript'.substring(4) // "Script"
```

如果**第二个参数大于第一个参数，<font color="red">`substring`方法会自动更换两个参数的位置</font>**。

```
'JavaScript'.substring(10, 4) // "Script"
// 等同于
'JavaScript'.substring(4, 10) // "Script"
```
上面代码中，调换`substring`方法的两个参数，都得到同样的结果。

如果**参数是负数，`substring`方法会<font color="red">自动将负数转为`0`</font>**。

```
'Javascript'.substring(-3) // "JavaScript"
'JavaScript'.substring(4, -3) // "Java"
```
上面代码中，第二个例子的参数`-3`会自动变成`0`，等同于`'JavaScript'.substring(4, 0)`。由于第二个参数小于第一个参数，会自动互换位置，所以返回`Java`。

**由于这些规则违反直觉，因此不建议使用`substring`方法，应该优先使用`slice`**。

### String.prototype.substr()

`substr`方法**用于从原字符串取出子字符串并返回，不改变原字符串**，跟`slice`和`substring`方法的作用相同。

`substr`方法的**第一个参数是子字符串的开始位置（从`0`开始计算），第二个参数是子字符串的长度**。

```
'JavaScript'.substr(4, 6) // "Script"
```

如果**省略第二个参数，则表示子字符串一直到原字符串的结束。**

```
'JavaScript'.substr(4) // "Script"
```

如果**第一个参数是负数，表示倒数计算的字符位置**。如果**第二个参数是负数，将被自动转为`0`，因此会返回空字符串**。

```
'JavaScript'.substr(-6) // "Script"
'JavaScript'.substr(4, -1) // ""
```
上面代码中，第二个例子的参数`-1`自动转为`0`，表示子字符串长度为`0`，所以返回空字符串。

### String.prototype.indexOf()，String.prototype.lastIndexOf()

`indexOf`方法**用于确定一个字符串在另一个字符串中第一次出现的位置，返回结果是匹配开始的位置。如果返回`-1`，就表示不匹配**。

```
'hello world'.indexOf('o') // 4
'JavaScript'.indexOf('script') // -1
```

`indexOf`方法还可以**接受第二个参数，表示从该位置开始向后匹配**。

```
'hello world'.indexOf('o', 6) // 7
```

`lastIndexOf`方法的用法跟`indexOf`方法一致，主要的**区别是`lastIndexOf`从尾部开始匹配，`indexOf`则是从头部开始匹配**。

```
'hello world'.lastIndexOf('o') // 7
```

另外，`lastIndexOf`的**第二个参数表示从该位置起向前匹配**。

```
'hello world'.lastIndexOf('o', 6) // 4
```

### String.prototype.trim()

`trim`方法**用于去除字符串两端的空格，返回一个新字符串，不改变原字符串**。

```
'  hello world  '.trim()
// "hello world"
```
该方法去除的不仅是**空格**，还包括**制表符（`\t`、`\v`）**、**换行符（`\n`）**和**回车符（`\r`）**。

```
'\r\nabc \t'.trim() // 'abc'
```

### String.prototype.toLowerCase()，String.prototype.toUpperCase()

**`toLowerCase`方法用于将一个字符串全部转为小写，`toUpperCase`则是全部转为大写**。它们都**返回一个新字符串，不改变原字符串**。

```
'Hello World'.toLowerCase()
// "hello world"

'Hello World'.toUpperCase()
// "HELLO WORLD"
```

### String.prototype.match()

`match`方法**用于确定原字符串是否匹配某个子字符串，返回一个数组，成员为匹配的第一个字符串。如果没有找到匹配，则返回`null`。**

```
'cat, bat, sat, fat'.match('at') // ["at"]
'cat, bat, sat, fat'.match('xt') // null
```

返回的数组还有`index`属性和`input`属性，分别表示匹配字符串开始的位置和原始字符串。
```
var matches = 'cat, bat, sat, fat'.match('at');
matches.index // 1
matches.input // "cat, bat, sat, fat"
```
上面的代码中，如果`match`没有找到匹配，返回了`null`，则调用`.index`和`.input`会抛出`TypeError`错误。

`match`方法还可以使用正则表达式作为参数，详见《正则表达式》一章。

### String.prototype.search()，String.prototype.replace()

`search`方法的用法基本等同于`match`，但是**返回值为匹配的第一个位置。如果没有找到匹配，则返回`-1`。**

```
'cat, bat, sat, fat'.search('at') // 1
```

`search`方法还可以使用正则表达式作为参数，详见《正则表达式》一节。

`replace`方法**用于替换匹配的子字符串，一般情况下只替换第一个匹配（<font color="red">除非使用带有g修饰符的正则表达式</font>）**。

```
'aaa'.replace('a', 'b') // "baa"
```

`replace`方法还可以使用正则表达式作为参数，详见《正则表达式》一节。

### String.prototype.split()

`split`方法**按照给定规则分割字符串，返回一个由分割出来的子字符串组成的数组**。

```
'a|b|c'.split('|') // ["a", "b", "c"]
```

**如果分割规则为空字符串，则返回数组的成员是原字符串的每一个字符。**

```
'a|b|c'.split('') // ["a", "|", "b", "|", "c"]
```

如果**省略参数，则返回数组的唯一成员就是原字符串**。

```
'a|b|c'.split() // ["a|b|c"]
```

注意，**分割规则为空字符串和省略参数是不一样的。**

如果**满足分割规则的两个部分紧邻着（即两个分割符中间没有其他字符），则返回数组之中会有一个空字符串**。

```
'a||c'.split('|') // ['a', '', 'c']
```

如果**满足分割规则的部分处于字符串的开头或结尾（即它的前面或后面没有其他字符），则返回数组的第一个或最后一个成员是一个空字符串**。

```
'|b|c'.split('|') // ["", "b", "c"]
'a|b|'.split('|') // ["a", "b", ""]
```

`split`方法还可以接受第二个参数，限定返回数组的最大成员数。

```
'a|b|c'.split('|', 0) // []
'a|b|c'.split('|', 1) // ["a"]
'a|b|c'.split('|', 2) // ["a", "b"]
'a|b|c'.split('|', 3) // ["a", "b", "c"]
'a|b|c'.split('|', 4) // ["a", "b", "c"]
```
上面代码中，`split`方法的第二个参数，决定了返回数组的成员数。

`split`方法还可以使用正则表达式作为参数，详见《正则表达式》一节。

### String.prototype.localeCompare()

`localeCompare`方法**用于比较两个字符串。它返回一个整数，如果小于`0`，表示第一个字符串小于第二个字符串；如果等于`0`，表示两者相等；如果大于`0`，表示第一个字符串大于第二个字符串**。

```
'apple'.localeCompare('banana') // -1
'apple'.localeCompare('apple') // 0
```

该方法的最大特点，就是会考虑自然语言的顺序。举例来说，**<font color="red">正常情况下，大写的英文字母小于小写字母</font>。**

```
'B' > 'a' // false
```

上面代码中，字母`B`小于字母`a`。因为 JavaScript 采用的是 Unicode 码点比较，`B`的码点是`66`，而`a`的码点是`97`。

但是，<font color="red">`localeCompare`方法会考虑自然语言的排序情况，将`B`排在`a`的前面</font>。

```
'B'.localeCompare('a') // 1
```
上面代码中，`localeCompare`方法返回整数`1`，表示`B`较大。

`localeCompare`还可以有**第二个参数，指定所使用的语言（默认是英语），然后根据该语言的规则进行比较**。

```
'ä'.localeCompare('z', 'de') // -1
'ä'.localeCompare('z', 'sv') // 1
```
上面代码中，`de`表示德语，`sv`表示瑞典语。德语中，`ä`小于`z`，所以返回`-1`；瑞典语中，`ä`大于`z`，所以返回`1`。

# Math对象

**`Math`是 JavaScript 的原生对象，提供各种数学功能**。该对象**<font color="red">不是构造函数，不能生成实例，所有的属性和方法都必须在`Math`对象上调用</font>**。

## 静态属性

`Math`对象的静态属性，**提供**以下**一些数学常数**。

> `Math.E`：常数`e`。
> `Math.LN2`：2 的自然对数。
> `Math.LN10`：10 的自然对数。
> `Math.LOG2E`：以 2 为底的`e`的对数。
> `Math.LOG10E`：以 10 为底的`e`的对数。
> `Math.PI`：常数 Pi。
> `Math.SQRT1_2`：0.5 的平方根。
> `Math.SQRT2`：2 的平方根。

```
Math.E // 2.718281828459045
Math.LN2 // 0.6931471805599453
Math.LN10 // 2.302585092994046
Math.LOG2E // 1.4426950408889634
Math.LOG10E // 0.4342944819032518
Math.PI // 3.141592653589793
Math.SQRT1_2 // 0.7071067811865476
Math.SQRT2 // 1.4142135623730951
```

这些属性都是只读的，不能修改。

## 静态方法

`Math`对象提供以下一些静态方法。

> `Math.abs()`：绝对值
> `Math.ceil()`：向上取整，比如2.3会取3
> `Math.floor()`：向下取整，比如2.8会取2
> `Math.max()`：最大值
> `Math.min()`：最小值
> `Math.pow()`：指数运算
> `Math.sqrt()`：平方根
> `Math.log()`：自然对数
> `Math.exp()`：e的指数
> `Math.round()`：四舍五入
> `Math.random()`：随机数

### Math.abs()

`Math.abs`方法**返回参数值的绝对值**。

```
Math.abs(1) // 1
Math.abs(-1) // 1
```

### Math.max()，Math.min()

`Math.max`方法**返回参数之中最大的那个值，`Math.min`返回最小的那个值。如果参数为空, `Math.min`返回`Infinity`, `Math.max`返回`-Infinity`**。

```
Math.max(2, -1, 5) // 5
Math.min(2, -1, 5) // -1
Math.min() // Infinity
Math.max() // -Infinity
```

### Math.floor()，Math.ceil()

`Math.floor`方法**返回<font color="red">小于</font>参数值的最大整数（地板值）**。

```
Math.floor(3.2) // 3
Math.floor(-3.2) // -4
```

`Math.ceil`方法返回<font color="red">大于</font>参数值的最小整数（天花板值）。

```
Math.ceil(3.2) // 4
Math.ceil(-3.2) // -3
```

这两个方法可以结合起来，实现一个**总是返回数值的整数部分的函数**。

```
function ToInteger(x) {
  x = Number(x);
  return x < 0 ? Math.ceil(x) : Math.floor(x);
}

ToInteger(3.2) // 3
ToInteger(3.5) // 3
ToInteger(3.8) // 3
ToInteger(-3.2) // -3
ToInteger(-3.5) // -3
ToInteger(-3.8) // -3
```
上面代码中，**不管正数或负数**，`ToInteger`函数**总是返回一个数值的整数部分**。

### Math.round()

`Math.round`方法**用于四舍五入**。

```
Math.round(0.1) // 0
Math.round(0.5) // 1
Math.round(0.6) // 1

// 等同于
Math.floor(x + 0.5)
```

注意，**它对负数的处理（主要是对`0.5`的处理）**。

```
Math.round(-1.1) // -1
Math.round(-1.5) // -1
Math.round(-1.6) // -2
```

### Math.pow()

`Math.pow`方法**返回以第一个参数为底数、第二个参数为幂的指数值**。

```
// 等同于 2 ** 2
Math.pow(2, 2) // 4
// 等同于 2 ** 3
Math.pow(2, 3) // 8
```

下面是计算圆面积的方法。

```
var radius = 20;
var area = Math.PI * Math.pow(radius, 2);
```

### Math.sqrt()

`Math.sqrt`方法**返回参数值的平方根。如果参数是一个负值，则返回`NaN`**。

```
Math.sqrt(4) // 2
Math.sqrt(-4) // NaN
```

### Math.log()

`Math.log`方法**返回以`e`为底的自然对数值**。

```
Math.log(Math.E) // 1
Math.log(10) // 2.302585092994046
```

如果要计算以`10`为底的对数，可以先用`Math.log`求出自然对数，然后除以`Math.LN10`；求以`2`为底的对数，可以除以`Math.LN2`。

```
Math.log(100)/Math.LN10 // 2
Math.log(8)/Math.LN2 // 3
```

### Math.exp()

`Math.exp`方法**返回常数`e`的参数次方**。

```
Math.exp(1) // 2.718281828459045
Math.exp(3) // 20.085536923187668
```

### Math.random()

`Math.random()`**返回`0`到`1`之间的一个伪随机数，<font color="red">可能等于`0`，但是一定小于`1`</font>**。

```
Math.random() // 0.7151307314634323
```

任意范围的随机数生成函数如下。

```
function getRandomArbitrary(min, max) {
  return Math.random() * (max - min) + min;
}

getRandomArbitrary(1.5, 6.5)
// 2.4942810038223864
```
上面的代码中，假设`max-min=a`，`a`即为`min`到`max`的距离，用`a`乘以`Math.random()`会得到`0~a`范围内的随机数，再加上`min`将这个范围进行偏移，就得到了`min~(a+min)`也就是`min~max`之间的随机数了。

任意范围的随机整数生成函数如下。

```
function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

getRandomInt(1, 6) // 5
```

返回随机字符的例子如下。

```
function random_str(length) {
  var ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
  ALPHABET += 'abcdefghijklmnopqrstuvwxyz';
  ALPHABET += '0123456789-_';
  var str = '';
  for (var i=0; i < length; ++i) {
    var rand = Math.floor(Math.random() * ALPHABET.length);
    str += ALPHABET.substring(rand, rand + 1);
  }
  return str;
}

random_str(6) // "NdQKOr"
```
上面代码中，`random_str`函数接受一个整数作为参数，返回变量`ALPHABET`内的随机字符所组成的指定长度的字符串。

### 三角函数方法

`Math`对象还提供一系列三角函数方法。

> Math.sin()：返回参数的正弦（参数为弧度值）
> Math.cos()：返回参数的余弦（参数为弧度值）
> Math.tan()：返回参数的正切（参数为弧度值）
> Math.asin()：返回参数的反正弦（返回值为弧度值）
> Math.acos()：返回参数的反余弦（返回值为弧度值）
> Math.atan()：返回参数的反正切（返回值为弧度值）

```
Math.sin(0) // 0
Math.cos(0) // 1
Math.tan(0) // 0

Math.sin(Math.PI / 2) // 1

Math.asin(1) // 1.5707963267948966
Math.acos(1) // 0
Math.atan(1) // 0.7853981633974483
```

# Date对象

`Date`对象是 JavaScript 原生的时间库。它以1970年1月1日00:00:00作为时间的零点，可以表示的时间范围是前后各1亿天（单位为毫秒）。

## 普通函数的用法

**`Date`对象可以作为普通函数直接调用，返回一个代表当前时间的字符串**。

```
Date()
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
```

注意，即使带有参数，`Date`作为普通函数使用时，返回的还是当前时间。

```
Date(2000, 1, 1)
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
```
上面代码说明，**<font color="red">无论有没有参数，直接调用`Date`总是返回当前时间</font>**。

## 构造函数的用法

`Date`还**可以当作构造函数使用。对它使用`new`命令，会返回一个`Date`对象的实例**。**如果不加参数，实例代表的就是当前时间**。

```
var today = new Date();
```

`Date`实例有一个独特的地方。**其他对象求值的时候，都是默认调用`.valueOf()`方法，但是`Date`实例求值的时候，默认调用的是`toString()`方法。这导致对`Date`实例求值，返回的是一个字符串，代表该实例对应的时间。**

```
var today = new Date();

today
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"

// 等同于
today.toString()
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
```
上面代码中，`today`是`Date`的实例，直接求值等同于调用`toString`方法。

**作为构造函数时，`Date`对象可以接受多种格式的参数，返回一个该参数对应的时间实例**。

```
// 参数为时间零点开始计算的毫秒数
new Date(1378218728000)
// Tue Sep 03 2013 22:32:08 GMT+0800 (CST)

// 参数为日期字符串
new Date('January 6, 2013');
// Sun Jan 06 2013 00:00:00 GMT+0800 (CST)

// 参数为多个整数，
// 代表年、月、日、小时、分钟、秒、毫秒
new Date(2013, 0, 1, 0, 0, 0, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
```

关于`Date`构造函数的参数，有几点说明。

第一点，**参数可以是负整数，代表1970年元旦之前的时间**。

```
new Date(-1378218728000)
// Fri Apr 30 1926 17:27:52 GMT+0800 (CST)
```

第二点，**只要是能被`Date.parse()`方法解析的字符串，都可以当作参数**。

```
new Date('2013-2-15')
new Date('2013/2/15')
new Date('02/15/2013')
new Date('2013-FEB-15')
new Date('FEB, 15, 2013')
new Date('FEB 15, 2013')
new Date('Feberuary, 15, 2013')
new Date('Feberuary 15, 2013')
new Date('15 Feb 2013')
new Date('15, Feberuary, 2013')
// Fri Feb 15 2013 00:00:00 GMT+0800 (CST)
```
上面多种日期字符串的写法，返回的都是同一个时间。

第三，**参数为年、月、日等多个整数时，年和月是不能省略的，其他参数都可以省略的。也就是说，这时至少需要两个参数，因为如果只使用“年”这一个参数，`Date`会将其解释为毫秒数**。

```
new Date(2013)
// Thu Jan 01 1970 08:00:02 GMT+0800 (CST)
```
上面代码中，`2013`被解释为毫秒数，而不是年份。

```
new Date(2013, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 1)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 1, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 1, 0, 0, 0, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
```
上面代码中，不管有几个参数，返回的都是2013年1月1日零点。

最后，各个参数的取值范围如下。

> 年：使用四位数年份，比如`2000`。如果写成两位数或个位数，则加上`1900`，即`10`代表`1910`年。如果是负数，表示公元前。
> 月：`0`表示一月，依次类推，`11`表示`12`月。
> 日：`1`到`31`。
> 小时：`0`到`23`。
> 分钟：`0`到`59`。
> 秒：`0`到`59`
> 毫秒：`0`到`999`。

注意，**月份从`0`开始计算，但是，天数从`1`开始计算。另外，除了日期的默认值为`1`，小时、分钟、秒钟和毫秒的默认值都是`0`**。

这些**参数如果超出了正常范围，会被自动折算**。比如，如果月设为`15`，就折算为下一年的`4`月。

```
new Date(2013, 15)
// Tue Apr 01 2014 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 0)
// Mon Dec 31 2012 00:00:00 GMT+0800 (CST)
```
上面代码的第二个例子，日期设为`0`，就代表上个月的最后一天。

**参数还可以使用负数，表示扣去的时间**。

```
new Date(2013, -1)
// Sat Dec 01 2012 00:00:00 GMT+0800 (CST)
new Date(2013, 0, -1)
// Sun Dec 30 2012 00:00:00 GMT+0800 (CST)
```
上面代码中，分别对月和日使用了负数，表示从基准日扣去相应的时间。对月使用`-1`表示扣去一个月，对日使用`-1`表示扣去一天。

## 日期的运算

**类型自动转换时，`Date`实例如果转为数值，则等于对应的毫秒数；如果转为字符串，则等于对应的日期字符串。**所以，**两个日期实例对象进行减法运算时，返回的是它们间隔的毫秒数；进行加法运算时，返回的是两个字符串连接而成的新字符串**。

```
var d1 = new Date(2000, 2, 1);
var d2 = new Date(2000, 3, 1);

d2 - d1
// 2678400000
d2 + d1
// "Sat Apr 01 2000 00:00:00 GMT+0800 (CST)Wed Mar 01 2000 00:00:00 GMT+0800 (CST)"
```

## 静态方法

### Date.now()

**`Date.now`方法返回当前时间距离时间零点（1970年1月1日 00:00:00 UTC）的毫秒数**，相当于 Unix 时间戳乘以`1000`。

```
Date.now() // 1364026285194
```

### Date.parse()

`Date.parse`方法用来解析日期字符串，返回该时间距离时间零点（1970年1月1日 00:00:00）的毫秒数。

日期字符串应该符合 RFC 2822 和 ISO 8061 这两个标准，即`YYYY-MM-DDTHH:mm:ss.sssZ`格式，其中最后的`Z`表示时区。但是，其他格式也可以被解析，请看下面的例子。

```
Date.parse('Aug 9, 1995')
Date.parse('January 26, 2011 13:51:50')
Date.parse('Mon, 25 Dec 1995 13:30:00 GMT')
Date.parse('Mon, 25 Dec 1995 13:30:00 +0430')
Date.parse('2011-10-10')
Date.parse('2011-10-10T14:48:00')
```
上面的日期字符串都可以解析。

如果解析失败，返回`NaN`。

```
Date.parse('xxx') // NaN
```

### Date.UTC()

**`Date.UTC`方法接受年、月、日等变量作为参数，返回该时间距离时间零点（1970年1月1日 00:00:00 UTC）的毫秒数**。

```
// 格式
Date.UTC(year, month[, date[, hrs[, min[, sec[, ms]]]]])

// 用法
Date.UTC(2011, 0, 1, 2, 3, 4, 567)
// 1293847384567
```

**该方法的参数用法与`Date`构造函数完全一致，比如月从`0`开始计算，日期从`1`开始计算。区别在于`Date.UTC`方法的参数，会被解释为 UTC 时间（世界标准时间），`Date`构造函数的参数会被解释为当前时区的时间**。

## 实例方法

`Date`的实例对象，有几十个自己的方法，除了`valueOf`和`toString`，可以分为以下三类。

> `to`类：从`Date`对象返回一个字符串，表示指定的时间。
> `get`类：获取`Date`对象的日期和时间。
> `set`类：设置`Date`对象的日期和时间。

### Date.prototype.valueOf()

**`valueOf`方法返回实例对象距离时间零点（1970年1月1日00:00:00 UTC）对应的毫秒数，该方法等同于`getTime`方法**。

```
var d = new Date();

d.valueOf() // 1362790014817
d.getTime() // 1362790014817
```

预期为数值的场合，`Date`实例会自动调用该方法，所以可以用下面的方法计算时间的间隔。

```
var start = new Date();
// ...
var end = new Date();
var elapsed = end - start;
```

### to 类方法

#### Date.prototype.toString()

`toString`方法返回一个完整的日期字符串。

```
var d = new Date(2013, 0, 1);

d.toString()
// "Tue Jan 01 2013 00:00:00 GMT+0800 (CST)"
d
// "Tue Jan 01 2013 00:00:00 GMT+0800 (CST)"
```

因为**`toString`是默认的调用方法**，所以如果直接读取`Date`实例，就相当于调用这个方法。

#### Date.prototype.toUTCString()

`toUTCString`方法返回对应的 UTC 时间，也就是比北京时间晚`8`个小时。

```
var d = new Date(2013, 0, 1);

d.toUTCString()
// "Mon, 31 Dec 2012 16:00:00 GMT"
```

#### Date.prototype.toISOString()

`toISOString`方法返回对应时间的 ISO8601 写法

```
var d = new Date(2013, 0, 1);

d.toISOString()
// "2012-12-31T16:00:00.000Z"
```
注意，`toISOString`方法返回的总是 UTC 时区的时间。

#### Date.prototype.toJSON()

`toJSON`方法返回一个符合 JSON 格式的 ISO 日期字符串，**与`toISOString`方法的返回结果完全相同**。

```
var d = new Date(2013, 0, 1);

d.toJSON()
// "2012-12-31T16:00:00.000Z"
```

#### Date.prototype.toDateString()

`toDateString`方法返回日期字符串（**不含小时、分和秒**）。

```
var d = new Date(2013, 0, 1);
d.toDateString() // "Tue Jan 01 2013"
```

#### Date.prototype.toTimeString()

`toTimeString`方法返回时间字符串（**不含年月日**）。

```
var d = new Date(2013, 0, 1);
d.toTimeString() // "00:00:00 GMT+0800 (CST)"
```

#### Date.prototype.toLocaleDateString()

`toLocaleDateString`方法返回一个字符串，代表**日期的当地写法**（不含小时、分和秒）。

```
var d = new Date(2013, 0, 1);

d.toLocaleDateString()
// 中文版浏览器为"2013年1月1日"
// 英文版浏览器为"1/1/2013"
```

#### Date.prototype.toLocaleTimeString()

`toLocaleTimeString`方法返回一个字符串，代表**时间的当地写法**（不含年月日）。

```
var d = new Date(2013, 0, 1);

d.toLocaleTimeString()
// 中文版浏览器为"上午12:00:00"
// 英文版浏览器为"12:00:00 AM"
```

### get 类方法

`Date`对象提供了一系列`get*`方法，用来获取实例对象某个方面的值。

> `getTime()`：返回实例距离1970年1月1日00:00:00的毫秒数，等同于`valueOf`方法。
> `getDate()`：返回实例对象对应每个月的几号（**从`1`开始**）。
> `getDay()`：返回星期几，星期日为`0`，星期一为`1`，以此类推。
> `getYear()`：返回距离`1900`的年数。
> `getFullYear()`：返回四位的年份。
> `getMonth()`：返回月份（`0`表示1月，`11`表示12月）。
> `getHours()`：返回小时（`0`-`23`）。
> `getMilliseconds()`：返回毫秒（`0`-`999`）。
> `getMinutes()`：返回分钟（`0`-`59`）。
> `getSeconds()`：返回秒（`0`-`59`）。
> `getTimezoneOffset()`：返回当前时间与 UTC 的时区差异，以**分钟**表示，返回结果考虑到了夏令时因素。

所有这些`get*`方法返回的都是整数，不同方法返回值的范围不一样。

> 分钟和秒：0 到 59
> 小时：0 到 23
> 星期：0（星期天）到 6（星期六）
> 日期：1 到 31
> 月份：0（一月）到 11（十二月）
> 年份：距离1900年的年数

```
var d = new Date('January 6, 2013');

d.getDate() // 6
d.getMonth() // 0
d.getYear() // 113
d.getFullYear() // 2013
d.getTimezoneOffset() // -480
```
上面代码中，最后一行返回`-480`，即 UTC 时间减去当前时间，单位是分钟。`-480`表示 UTC 比当前时间少`480`分钟，即当前时区比 UTC 早`8`个小时。

下面是一个例子，计算本年度还剩下多少天。
```
function leftDays() {
  var today = new Date();
  var endYear = new Date(today.getFullYear(), 11, 31, 23, 59, 59, 999);
  var msPerDay = 24 * 60 * 60 * 1000;
  return Math.round((endYear.getTime() - today.getTime()) / msPerDay);
}
```

上面这些`get*`方法返回的都是当前时区的时间，`Date`对象还提供了这些方法对应的 UTC 版本，用来返回 UTC 时间。
> `getUTCDate()`
> `getUTCFullYear()`
> `getUTCMonth()`
> `getUTCDay()`
> `getUTCHours()`
> `getUTCMinutes()`
> `getUTCSeconds()`
> `getUTCMilliseconds()`

```
var d = new Date('January 6, 2013');

d.getDate() // 6
d.getUTCDate() // 5
```
上面代码中，实例对象`d`表示当前时区（东八时区）的1月6日0点0分0秒，这个时间对于当前时区来说是1月6日，所以`getDate`方法返回6，对于 UTC 时区来说是1月5日，所以`getUTCDate`方法返回5。

### set 类方法

`Date`对象提供了一系列`set*`方法，用来设置实例对象的各个方面。

> `setDate(date)`：设置实例对象对应的每个月的几号（`1`-`31`），返回改变后毫秒时间戳。
> `setYear(year)`: 设置距离`1900`年的年数。
> `setFullYear(year [, month, date])`：设置四位年份。
> `setHours(hour [, min, sec, ms])`：设置小时（`0`-`23`）。
> `setMilliseconds()`：设置毫秒（`0`-`999`）。
> `setMinutes(min [, sec, ms])`：设置分钟（`0`-`59`）。
> `setMonth(month [, date])`：设置月份（`0`-`11`）。
> `setSeconds(sec [, ms])`：设置秒（`0`-`59`）。
> `setTime(milliseconds)`：设置毫秒时间戳。

这些方法基本是跟`get*`方法一一对应的，但是没有`setDay`方法，因为星期几是计算出来的，而不是设置的。另外，需要注意的是，凡是涉及到设置月份，都是从`0`开始算的，即`0`是1月，`11`是12月。

```
var d = new Date ('January 6, 2013');

d // Sun Jan 06 2013 00:00:00 GMT+0800 (CST)
d.setDate(9) // 1357660800000
d // Wed Jan 09 2013 00:00:00 GMT+0800 (CST)
```

**`set*`方法的参数都会自动折算**。以`setDate`为例，如果参数超过当月的最大天数，则向下一个月顺延，如果参数是负数，表示从上个月的最后一天开始减去的天数。

```
var d1 = new Date('January 6, 2013');

d1.setDate(32) // 1359648000000
d1 // Fri Feb 01 2013 00:00:00 GMT+0800 (CST)

var d2 = new Date ('January 6, 2013');

d.setDate(-1) // 1356796800000
d // Sun Dec 30 2012 00:00:00 GMT+0800 (CST)
```

`set`类方法和`get`类方法，可以结合使用，得到相对时间。

```
var d = new Date();

// 将日期向后推1000天
d.setDate(d.getDate() + 1000);
// 将时间设为6小时后
d.setHours(d.getHours() + 6);
// 将年份设为去年
d.setFullYear(d.getFullYear() - 1);
```

`set*`系列方法除了`setTime()`和`setYear()`，都有对应的 UTC 版本，即设置 UTC 时区的时间。

> `setUTCDate()`
> `setUTCFullYear()`
> `setUTCHours()`
> `setUTCMilliseconds()`
> `setUTCMinutes()`
> `setUTCMonth()`
> `setUTCSeconds()`

```
var d = new Date('January 6, 2013');
d.getUTCHours() // 16
d.setUTCHours(22) // 1357423200000
d // Sun Jan 06 2013 06:00:00 GMT+0800 (CST)
```
上面代码中，本地时区（东八时区）的1月6日0点0分，是 UTC 时区的前一天下午16点。设为 UTC 时区的22点以后，就变为本地时区的上午6点。

# RegExp对象

## 概述

**正则表达式（regular expression）是一种表达文本模式（即字符串结构）的方法**，有点像字符串的模板，**常常用来按照“给定模式”匹配文本**。比如，正则表达式给出一个 Email 地址的模式，然后用它来确定一个字符串是否为 Email 地址。JavaScript 的正则表达式体系是参照 Perl 5 建立的。

新建正则表达式有两种方法。**一种是使用字面量，以斜杠表示开始和结束**。

```
var regex = /xyz/;
```

另一种是使用`RegExp`构造函数。

```
var regex = new RegExp('xyz');
```

上面两种写法是等价的，都新建了一个内容为`xyz`的正则表达式对象。**它们的主要区别是，第一种方法在引擎编译代码时，就会新建正则表达式，第二种方法在运行时新建正则表达式，所以<font color="red">前者的效率较高</font>。而且，前者比较便利和直观，所以<font color="red">实际应用中，基本上都采用字面量定义正则表达式</font>**。

`RegExp`构造函数还可以接受第二个参数，表示修饰符（详细解释见下文）。

```
var regex = new RegExp('xyz', 'i');
// 等价于
var regex = /xyz/i;
```
上面代码中，正则表达式`/xyz/`有一个修饰符`i`。

## 实例属性

正则对象的实例属性分成两类。

一类是**修饰符相关，返回一个布尔值，表示对应的修饰符是否设置**。

> `RegExp.prototype.ignoreCase`：返回一个布尔值，表示是否设置了`i`修饰符。
> `RegExp.prototype.global`：返回一个布尔值，表示是否设置了`g`修饰符。
> `RegExp.prototype.multiline`：返回一个布尔值，表示是否设置了`m`修饰符。

上面三个属性都是只读的。

```
var r = /abc/igm;

r.ignoreCase // true
r.global // true
r.multiline // true
```

另一类是**与修饰符无关的属性**，主要是下面两个。

> `RegExp.prototype.lastIndex`：返回一个数值，表示下一次开始搜索的位置。该属性可读写，但是只在设置了`g`修饰符、进行连续搜索时有意义，详细介绍请看后文。
> `RegExp.prototype.source`：返回正则表达式的字符串形式（不包括反斜杠），该属性只读。

```
var r = /abc/igm;

r.lastIndex // 0
r.source // "abc"
```

## 实例方法

### RegExp.prototype.test()

正则实例对象的**`test`方法返回一个布尔值，表示当前模式是否能匹配参数字符串**。

```
/cat/.test('cats and dogs') // true
```
上面代码验证参数字符串之中是否包含`cat`，结果返回`true`。

**如果正则表达式带有`g`修饰符，则每一次`test`方法都<font color="red">从上一次结束的位置开始向后匹配</font>**。

```
var r = /x/g;
var s = '_x_x';

r.lastIndex // 0
r.test(s) // true

r.lastIndex // 2
r.test(s) // true

r.lastIndex // 4
r.test(s) // false
```
上面代码的正则表达式使用了`g`修饰符，表示是全局搜索，会有多个结果。接着，三次使用`test`方法，每一次开始搜索的位置都是上一次匹配的后一个位置。

**带有`g`修饰符时，可以通过正则对象的`lastIndex`属性指定开始搜索的位置**。

```
var r = /x/g;
var s = '_x_x';

r.lastIndex = 4;
r.test(s) // false
```

上面代码指定从字符串的第五个位置开始搜索，这个位置是没有字符的，所以返回`false`。

**`lastIndex`属性只对同一个正则表达式有效**，所以下面这样写是错误的。

```
var count = 0;
while (/a/g.test('babaa')) count++;
```
上面代码会导致无限循环，因为`while`循环的每次匹配条件都是一个新的正则表达式，导致`lastIndex`属性总是等于`0`。

如果正则模式是一个空字符串，则匹配所有字符串。

```
new RegExp('').test('abc')
// true
```

### RegExp.prototype.exec()

正则实例对象的**`exec`方法，用来返回匹配结果。如果发现匹配，就返回一个数组，成员是匹配成功的子字符串，否则返回`null`**。

```
var s = '_x_x';
var r1 = /x/;
var r2 = /y/;

r1.exec(s) // ["x"]
r2.exec(s) // null
```
上面代码中，正则对象`r1`匹配成功，返回一个数组，成员是匹配结果；正则对象`r2`匹配失败，返回`null`。

如果正则表示式包含圆括号（即含有“组匹配”），则返回的数组会包括多个成员。第一个成员是整个匹配成功的结果，后面的成员就是圆括号对应的匹配成功的组。也就是说，第二个成员对应第一个括号，第三个成员对应第二个括号，以此类推。整个数组的`length`属性等于组匹配的数量再加`1`。

```
var s = '_x_x';
var r = /_(x)/;

r.exec(s) // ["_x", "x"]
```
上面代码的`exec`方法，返回一个数组。**第一个成员是整个匹配的结果，第二个成员是圆括号匹配的结果。**

`exec`方法的返回数组还包含以下两个属性：

> `input`：整个原字符串。
> `index`：整个模式匹配成功的开始位置（从`0`开始计数）。

```
var r = /a(b+)a/;
var arr = r.exec('_abbba_aba_');

arr // ["abbba", "bbb"]

arr.index // 1
arr.input // "_abbba_aba_"
```
上面代码中的`index`属性等于`1`，是因为从原字符串的第二个位置开始匹配成功。

**如果正则表达式加上`g`修饰符，则可以使用多次`exec`方法，下一次搜索的位置从上一次匹配成功结束的位置开始。**

```
var reg = /a/g;
var str = 'abc_abc_abc'

var r1 = reg.exec(str);
r1 // ["a"]
r1.index // 0
reg.lastIndex // 1

var r2 = reg.exec(str);
r2 // ["a"]
r2.index // 4
reg.lastIndex // 5

var r3 = reg.exec(str);
r3 // ["a"]
r3.index // 8
reg.lastIndex // 9

var r4 = reg.exec(str);
r4 // null
reg.lastIndex // 0
```
上面代码连续用了四次`exec`方法，前三次都是从上一次匹配结束的位置向后匹配。当第三次匹配结束以后，整个字符串已经到达尾部，匹配结果返回`null`，正则实例对象的`lastIndex`属性也重置为`0`，意味着第四次匹配将从头开始。

利用`g`修饰符允许多次匹配的特点，可以用一个循环完成全部匹配。

```
var reg = /a/g;
var str = 'abc_abc_abc'

while(true) {
  var match = reg.exec(str);
  if (!match) break;
  console.log('#' + match.index + ':' + match[0]);
}
// #0:a
// #4:a
// #8:a
```
上面代码中，只要`exec`方法不返回`null`，就会一直循环下去，每次输出匹配的位置和匹配的文本。

**正则实例对象的`lastIndex`属性不仅可读，还可写。设置了`g`修饰符的时候，只要手动设置了`lastIndex`的值，就会从指定位置开始匹配**。

## 字符串的实例方法

字符串的实例方法之中，有4种与正则表达式有关。

> `String.prototype.match()`：返回一个数组，成员是所有匹配的子字符串。
> `String.prototype.search()`：按照给定的正则表达式进行搜索，返回一个整数，表示匹配开始的位置。
> `String.prototype.replace()`：按照给定的正则表达式进行替换，返回替换后的字符串。
> `String.prototype.split()`：按照给定规则进行字符串分割，返回一个数组，包含分割后的各个成员。

### String.prototype.match()

字符串实例对象的**`match`方法对字符串进行正则匹配，返回匹配结果**。

```
var s = '_x_x';
var r1 = /x/;
var r2 = /y/;

s.match(r1) // ["x"]
s.match(r2) // null
```
从上面代码可以看到，字符串的`match`方法与正则对象的`exec`方法非常类似：匹配成功返回一个数组，匹配失败返回`null`。

**如果正则表达式带有`g`修饰符，则该方法与正则对象的`exec`方法行为不同，会一次性返回所有匹配成功的结果。**

```
var s = 'abba';
var r = /a/g;

s.match(r) // ["a", "a"]
r.exec(s) // ["a"]
```

**<font color="red">设置正则表达式的`lastIndex`属性，对`match`方法无效</font>，匹配总是从字符串的第一个字符开始**。

```
var r = /a|b/g;
r.lastIndex = 7;
'xaxb'.match(r) // ['a', 'b']
r.lastIndex // 0
```
上面代码表示，设置正则对象的`lastIndex`属性是无效的。

### String.prototype.search()

字符串对象的**`search`方法，返回第一个满足条件的匹配结果在整个字符串中的位置。如果没有任何匹配，则返回`-1`。**

```
'_x_x'.search(/x/)
// 1
```
上面代码中，第一个匹配结果出现在字符串的`1`号位置。

### String.prototype.replace()

字符串对象的**`replace`方法可以替换匹配的值。它接受两个参数，第一个是正则表达式，表示搜索模式，第二个是替换的内容**。

```
str.replace(search, replacement)
```

正则表达式**如果不加`g`修饰符，就替换第一个匹配成功的值，否则替换所有匹配成功的值，即<font color="red">`g`表示全局替换</font>**。

```
'aaa'.replace('a', 'b') // "baa"
'aaa'.replace(/a/, 'b') // "baa"
'aaa'.replace(/a/g, 'b') // "bbb"
```
上面代码中，最后一个正则表达式使用了`g`修饰符，导致所有的`b`都被替换掉了。

**`replace`方法的一个应用，就是消除字符串首尾两端的空格**。

```
var str = '  #id div.class  ';

str.replace(/^\s+|\s+$/g, '')
// "#id div.class"
```

`replace`方法的第二个参数可以使用美元符号`$`，用来指代所替换的内容。

> `$&`：匹配的子字符串。
> `$``：匹配结果前面的文本。
> `$’`：匹配结果后面的文本。
> `$n`：匹配成功的第n组内容，n是从1开始的自然数。
> `$$`：指代美元符号$。

```
'hello world'.replace(/(\w+)\s(\w+)/, '$2 $1')
// "world hello"

'abc'.replace('b', '[$`-$&-$\']')
// "a[a-b-c]c"
```
上面代码中，第一个例子是将匹配的组互换位置，第二个例子是改写匹配的值。

`replace`方法的第二个参数还可以是一个函数，将每一个匹配内容替换为函数返回值。

```
'3 and 5'.replace(/[0-9]+/g, function (match) {
  return 2 * match;
})
// "6 and 10"

var a = 'The quick brown fox jumped over the lazy dog.';
var pattern = /quick|brown|lazy/ig;

a.replace(pattern, function replacer(match) {
  return match.toUpperCase();
});
// The QUICK BROWN fox jumped over the LAZY dog.
```
作为`replace`方法**第二个参数的替换函数，可以接受多个参数。其中，第一个参数是捕捉到的内容，第二个参数是捕捉到的组匹配（有多少个组匹配，就有多少个对应的参数）。此外，最后还可以添加两个参数，倒数第二个参数是捕捉到的内容在整个字符串中的位置（比如从第五个位置开始），最后一个参数是原字符串**。下面是一个网页模板替换的例子。

```
var prices = {
  'p1': '$1.99',
  'p2': '$9.99',
  'p3': '$5.00'
};

var template = '<span id="p1"></span>'
  + '<span id="p2"></span>'
  + '<span id="p3"></span>';

template.replace(
  /(<span id=")(.*?)(">)(<\/span>)/g,
  function(match, $1, $2, $3, $4){
    return $1 + $2 + $3 + prices[$2] + $4;
  }
);
// "<span id="p1">$1.99</span><span id="p2">$9.99</span><span id="p3">$5.00</span>"
```
上面代码的捕捉模式中，有四个括号，所以会产生四个组匹配，在匹配函数中用`$1`到`$4`表示。匹配函数的作用是将价格插入模板中。

### String.prototype.split()

字符串对象的**`split`方法按照正则规则分割字符串，返回一个由分割后的各个部分组成的数组**。

```
str.split(separator, [limit])
```

该方法**接受两个参数，第一个参数是正则表达式，表示分隔规则，第二个参数是返回数组的最大成员数。**

```
// 非正则分隔
'a,  b,c, d'.split(',')
// [ 'a', '  b', 'c', ' d' ]

// 正则分隔，去除多余的空格
'a,  b,c, d'.split(/, */)
// [ 'a', 'b', 'c', 'd' ]

// 指定返回数组的最大成员
'a,  b,c, d'.split(/, */, 2)
[ 'a', 'b' ]
```
上面代码使用正则表达式，去除了子字符串的逗号后面的空格。

```
// 例一
'aaa*a*'.split(/a*/)
// [ '', '*', '*' ]

// 例二
'aaa**a*'.split(/a*/)
// ["", "*", "*", "*"]
```
上面代码的分割规则是`0`次或多次的`a`，由于正则默认是贪婪匹配，所以例一的第一个分隔符是`aaa`，第二个分割符是`a`，将字符串分成三个部分，包含开始处的空字符串。例二的第一个分隔符是`aaa`，第二个分隔符是`0`个`a`（即空字符），第三个分隔符是`a`，所以将字符串分成四个部分。

**如果正则表达式带有括号，则括号匹配的部分也会作为数组成员返回**。

```
'aaa*a*'.split(/(a*)/)
// [ '', 'aaa', '*', 'a', '*' ]
```
上面代码的正则表达式使用了括号，第一个组匹配是aaa，第二个组匹配是a，它们都作为数组成员返回。

## 匹配规则

正则表达式的规则很复杂，下面一一介绍这些规则。

### 字面量字符和元字符

大部分字符在正则表达式中，就是字面的含义，比如`/a/`匹配`a`，`/b/`匹配`b`。如果在正则表达式之中，某个字符只表示它字面的含义（就像前面的`a`和`b`），那么它们就叫做“字面量字符”（literal characters）。

```
/dog/.test('old dog') // true
```
上面代码中正则表达式的`dog`，就是字面量字符，所以`/dog/`匹配`old dog`，因为它就表示`d`、`o`、`g`三个字母连在一起。

**除了字面量字符以外，还有一部分字符有特殊含义，不代表字面的意思。它们叫做“元字符”**（metacharacters），主要有以下几个。

#### 点字符（.)

点字符（`.`）匹配除回车（`\r`）、换行(`\n`) 、行分隔符（`\u2028`）和段分隔符（`\u2029`）以外的所有字符。

```
/c.t/
```
上面代码中，**`c.t`匹配`c`和`t`之间包含任意<font color="red">一个</font>字符的情况，只要这三个字符在同一行**，比如`cat`、`c2t`、`c-t`等等，但是**不匹配`coot`**。

#### 位置字符

> `^` 表示字符串的开始位置
> `$` 表示字符串的结束位置

```
// test必须出现在开始位置
/^test/.test('test123') // true

// test必须出现在结束位置
/test$/.test('new test') // true

// 从开始位置到结束位置只有test
/^test$/.test('test') // true
/^test$/.test('test test') // false
/^test$/.test('testtest') // false
```

#### 选择符（|）

**竖线符号（`|`）在正则表达式中表示“或关系”（OR）**，即`cat|dog`表示匹配`cat`或`dog`。

```
/11|22/.test('911') // true
```
上面代码中，正则表达式指定必须匹配`11`或`22`。

**多个选择符可以联合使用**。

```
// 匹配fred、barney、betty之中的一个
/fred|barney|betty/
```

**选择符会包括它前后的多个字符**，比如`/ab|cd/`指的是匹配`ab`或者`cd`，而不是指匹配`b`或者`c`。**如果想修改这个行为，可以使用圆括号**。

```
/a( |\t)b/.test('a\tb') // true
```
上面代码指的是，`a`和`b`之间有一个空格或者一个制表符。

其他的元字符还包括`\\`、`\*`、`+`、`?`、`()`、`[]`、`{}`等，将在下文解释。

### 转义符

**正则表达式中那些有特殊含义的元字符，如果要匹配它们本身，就需要在它们前面要加上反斜杠进行转义**。比如要匹配`+`，就要写成`\+`。

```
/1+1/.test('1+1')
// false

/1\+1/.test('1+1')
// true
```
上面代码中，第一个正则表达式之所以不匹配，因为加号是元字符，不代表自身。第二个正则表达式使用反斜杠对加号转义，就能匹配成功。

**正则表达式中，需要反斜杠转义的，一共有12个字符：`^`、`.`、`[`、`$`、`(`、`)`、`|`、`*`、`+`、`?`、`{`和`\\`。需要特别注意的是，<font color="red">如果使用`RegExp`方法生成正则对象，转义需要使用两个斜杠，因为字符串内部会先转义一次</font>**。

```
(new RegExp('1\+1')).test('1+1')
// false

(new RegExp('1\\+1')).test('1+1')
// true
```
上面代码中，`RegExp`作为构造函数，参数是一个字符串。但是，在字符串内部，反斜杠也是转义字符，所以它会先被反斜杠转义一次，然后再被正则表达式转义一次，因此需要两个反斜杠转义。

### 特殊字符

正则表达式对一些不能打印的特殊字符，提供了表达方法。

> `\cX` 表示`Ctrl-[X]`，其中的`X`是`A`-`Z`之中任一个英文字母，用来匹配控制字符。
> `[\b]` 匹配退格键(U+0008)，不要与`\b`混淆。
> `\n` 匹配换行键。
> `\r` 匹配回车键。
> `\t` 匹配制表符 tab（U+0009）。
> `\v` 匹配垂直制表符（U+000B）。
> `\f` 匹配换页符（U+000C）。
> `\0` 匹配`null`字符（U+0000）。
> `\xhh` 匹配一个以两位十六进制数（`\x00`-`\xFF`）表示的字符。
> `\uhhhh` 匹配一个以四位十六进制数（`\u0000`-`\uFFFF`）表示的 Unicode 字符。

### 字符类

**字符类（class）表示有一系列字符可供选择，只要匹配其中一个就可以了。所有可供选择的字符都放在方括号内**，比如`[xyz]` 表示`x`、`y`、`z`之中任选一个匹配。

```
/[abc]/.test('hello world') // false
/[abc]/.test('apple') // true
```
上面代码中，字符串`hello world`不包含`a`、`b`、`c`这三个字母中的任一个，所以返回`false`；字符串`apple`包含字母`a`，所以返回`true`。

有两个字符在字符类中有特殊含义。

#### 脱字符（^）

**如果方括号内的第一个字符是`[^]`，则表示除了字符类之中的字符，其他字符都可以匹配**。比如，`[^xyz]`表示除了`x`、`y`、`z`之外都可以匹配。

```
/[^abc]/.test('hello world') // true
/[^abc]/.test('bbc') // false
```
上面代码中，字符串`hello world`不包含字母`a`、`b`、`c`中的任一个，所以返回`true`；字符串`bbc`不包含`a`、`b`、`c`以外的字母，所以返回`false`。

**如果方括号内没有其他字符，即只有`[^]`，就表示匹配一切字符，其中包括换行符。相比之下，点号作为元字符（`.`）是不包括换行符的**。

```
var s = 'Please yes\nmake my day!';

s.match(/yes.*day/) // null
s.match(/yes[^]*day/) // [ 'yes\nmake my day']
```
上面代码中，字符串`s`含有一个换行符，点号不包括换行符，所以第一个正则表达式匹配失败；第二个正则表达式`[^]`包含一切字符，所以匹配成功。

注意，**<font color="red">脱字符只有在字符类的第一个位置才有特殊含义，否则就是字面含义</font>**。

#### 连字符（-）

某些情况下，**对于连续序列的字符，连字符（`-`）用来提供简写形式，表示字符的连续范围**。比如，`[abc]`可以写成`[a-c]`，`[0123456789]`可以写成`[0-9]`，同理`[A-Z]`表示26个大写字母。

```
/a-z/.test('b') // false
/[a-z]/.test('b') // true
```
上面代码中，**当连字号（dash）不出现在方括号之中，就不具备简写的作用，只代表字面的含义**，所以不匹配字符`b`。**<font color="red">只有当连字号用在方括号之中，才表示连续的字符序列</font>**。

以下都是合法的字符类简写形式。

```
[0-9.,]
[0-9a-fA-F]
[a-zA-Z0-9-]
[1-31]
```
**<font color="red">上面代码中最后一个字符类`[1-31]`，不代表`1`到`31`，只代表`1`到`3`</font>。**

连字符还可以用来指定 Unicode 字符的范围。

```
var str = "\u0130\u0131\u0132";
/[\u0128-\uFFFF]/.test(str)
// true
```
上面代码中，`\u0128-\uFFFF`表示匹配码点在`0128`到`FFFF`之间的所有字符。

另外，**不要过分使用连字符，设定一个很大的范围，否则很可能选中意料之外的字符。最典型的例子就是`[A-z]`，表面上它是选中从大写的`A`到小写的`z`之间52个字母，但是由于在 ASCII 编码之中，大写字母与小写字母之间还有其他字符，结果就会出现意料之外的结果**。

```
/[A-z]/.test('\\') // true
```
上面代码中，由于反斜杠（‘\’）的ASCII码在大写字母与小写字母之间，结果会被选中。

### 预定义模式

预定义模式指的是某些常见模式的简写方式。

> `\d` 匹配`0-9`之间的任一数字，相当于`[0-9]`。
> `\D` 匹配所有`0-9`以外的字符，相当于`[^0-9]`。
> `\w` 匹配任意的字母、数字和下划线，相当于`[A-Za-z0-9_]`。
> `\W` 除所有字母、数字和下划线以外的字符，相当于`[^A-Za-z0-9_]`。
> `\s` 匹配空格（包括换行符、制表符、空格符等），相等于`[ \t\r\n\v\f]`。
> `\S` 匹配非空格的字符，相当于`[^ \t\r\n\v\f]`。
> `\b` 匹配词的边界。
> `\B` 匹配非词边界，即在词的内部。

下面是一些例子。

```
// \s 的例子
/\s\w*/.exec('hello world') // [" world"]

// \b 的例子
/\bworld/.test('hello world') // true
/\bworld/.test('hello-world') // true
/\bworld/.test('helloworld') // false

// \B 的例子
/\Bworld/.test('hello-world') // false
/\Bworld/.test('helloworld') // true
```
上面代码中，`\s`表示空格，所以匹配结果会包括空格。`\b`表示词的边界，所以`world`的词首必须独立（词尾是否独立未指定），才会匹配。同理，`\B`表示非词的边界，只有`world`的词首不独立，才会匹配。由上面的代码可以看出`-`也算是词的边界。

通常，正则表达式遇到换行符（`\n`）就会停止匹配。

```
var html = "<b>Hello</b>\n<i>world!</i>";

/.*/.exec(html)[0]
// "<b>Hello</b>"
```
上面代码中，字符串`html`包含一个换行符，结果点字符（`.`）不匹配换行符，导致匹配结果可能不符合原意。这时使用`\s`字符类，就能包括换行符。

```
var html = "<b>Hello</b>\n<i>world!</i>";

/[\S\s]*/.exec(html)[0]
// "<b>Hello</b>\n<i>world!</i>"
```
上面代码中，`[\S\s]`指代一切字符。





























































































