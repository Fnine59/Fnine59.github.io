---
title: 《JavaScript标准参考教程》阅读笔记之《语法》
date: 2018-05-06 20:53:23
tags: JavaScript
---

> 整理自阮一峰《JavaScript标准参考教程》，原作者为阮一峰，[github地址](https://github.com/ruanyf/jstutorial)

# 基本语法
## 语句
JavaScript程序的执行单位为行（line），也就是一行一行地执行。一般情况下，每一行就是一个语句。

语句以分号结尾，一个分号就表示一个语句结束。多个语句可以写在一行内。

分号前面可以没有任何内容，JavaScript引擎将其视为空语句。

表达式不需要分号结尾。一旦在表达式后面添加分号，则 JavaScript引擎就将表达式视为语句，这样会产生一些没有任何意义的语句。

语句和表达式的区别在于，前者主要为了进行某种操作，一般情况下不需要返回值；后者则是为了得到返回值，一定会返回一个值。凡是 JavaScript语言中预期为值的地方，都可以使用表达式。比如，赋值语句的等号右边，预期是一个值，因此可以放置各种表达式。

## 变量
### 概念
变量是对“值”的具名引用。变量就是为“值”起名，然后引用这个名字，就等同于引用这个值。变量的名字就是变量名。

**注意，JavaScript 的变量名区分大小写，`A`和`a`是两个不同的变量。**

变量的声明和赋值，是分开的两个步骤。如果只是声明变量而没有赋值，则该变量的值是`undefined`。`undefined`是一个 JavaScript 关键字，表示“无定义”。

如果一个变量没有声明就直接使用，JavaScript 会报错，告诉你变量未定义。

JavaScript 是一种**动态类型语言**，也就是说，变量的类型没有限制，变量可以随时更改类型。

**如果使用`var`重新声明一个已经存在的变量，是无效的。但是，如果第二次声明的时候还进行了赋值，则会覆盖掉前面的值。**

### 变量提升
JavaScript引擎的工作方式是，先解析代码，获取所有被声明的变量，然后再一行一行地运行。这造成的结果，就是所有的变量的声明语句，都会被提升到代码的头部，这就叫做变量提升（hoisting）。

```
console.log(a);
var a = 1;
```
上面代码首先使用`console.log`方法，在控制台（console）显示变量`a`的值。这时变量`a`还没有声明和赋值，所以这是一种错误的做法，但是实际上不会报错。因为存在变量提升，真正运行的是下面的代码。
```
var a;
console.log(a);
a = 1;
```
最后的结果是显示`undefined`，表示变量`a`已声明，但还未赋值。

## 标识符
标识符（identifier）指的是用来识别各种值的合法名称。最常见的标识符就是变量名，以及后面要提到的函数名。JavaScript 语言的标识符对大小写敏感，所以`a`和`A`是两个不同的标识符。

标识符有一套命名规则，不符合规则的就是非法标识符。JavaScript 引擎遇到非法标识符，就会报错。

简单说，标识符命名规则如下。

> 第一个字符，可以是任意 Unicode字母（包括英文字母和其他语言的字母），以及美元符号（`$`）和下划线（`_`）。
> 第二个字符及后面的字符，除了 Unicode 字母、美元符号和下划线，还可以用数字0-9。

注意：中文是合法的标识符，可以用作变量名。

**JavaScript有一些保留字，不能用作标识符**：arguments、break、case、catch、class、const、continue、debugger、default、delete、do、else、enum、eval、export、extends、false、finally、for、function、if、implements、import、in、instanceof、interface、let、new、null、package、private、protected、public、return、static、super、switch、this、throw、true、try、typeof、var、void、while、with、yield。

## 注释

源码中被 JavaScript 引擎忽略的部分就叫做注释，它的作用是对代码进行解释。Javascript 提供两种注释的写法：一种是单行注释，用`//`起头；另一种是多行注释，放在`/*`和`*/`之间。

此外，由于历史上 JavaScript 可以兼容 HTML 代码的注释，所以`<!--`和`-->`也被视为合法的单行注释。

需要注意的是，`-->`只有在行首，才会被当成单行注释，否则会当作正常的运算。

## 区块
JavaScript 使用大括号，将多个相关的语句组合在一起，称为“区块”（block）。

**对于`var`命令来说，JavaScript 的区块不构成单独的作用域（scope）**。

## 条件语句

条件语句包括`if`和`switch`。

### if结构
`if`结构先判断一个表达式的布尔值，然后根据布尔值的真伪，执行不同的语句。所谓布尔值，往往由一个条件表达式产生，指的是 JavaScript 的两个特殊值，`true`表示真，`false`表示伪。

**注意，if后面的表达式之中，不要混淆赋值表达式（`=`）、严格相等运算符（`===`）和相等运算符（`==`）。尤其是赋值表达式不具有比较作用。**

```
var x = 1;
var y = 2;
if (x = y) {
  console.log(x);
}
// "2"
```
上面代码的原意是，当`x`等于`y`的时候，才执行相关语句。但是，不小心将严格相等运算符写成赋值表达式，结果变成了将`y`赋值给变量`x`，再判断变量`x`的值（等于2）的布尔值（结果为`true`）。

这种错误可以正常生成一个布尔值，因而不会报错。**为了避免这种情况，有些开发者习惯将常量写在运算符的左边，这样的话，一旦不小心将相等运算符写成赋值运算符，就会报错，因为常量不能被赋值。**

```
if (x = 2) { // 不报错
if (2 = x) { // 报错
```
至于为什么优先采用“严格相等运算符”（`===`），而不是“相等运算符”（`==`），请参考《运算符》章节。

### if…else 结构
`if`代码块后面，还可以跟一个`else`代码块，表示不满足条件时，所要执行的代码。

对同一个变量进行多次判断时，多个`if…else`语句可以连写在一起。

`else`代码块总是与离自己最近的那个`if`语句配对。

### switch结构
`switch`结构会根据`switch`关键字后紧跟的变量或表达式的值，选择执行相应的`case`。如果所有`case`都不符合，则执行最后的`default`部分。需要注意的是，每个case代码块内部的`break`语句不能少，否则会接下去执行下一个`case`代码块，而不是跳出`switch`结构。

多个`if…else`连在一起使用的时候，可以转为使用更方便的`switch`结构。

`switch`语句部分和`case`语句部分，都可以使用表达式。

需要注意的是，**switch语句后面的表达式，与case语句后面的表示式比较运行结果时，采用的是严格相等运算符（===），而不是相等运算符（==），这意味着比较时不会发生类型转换。**

### 三元运算符 ?:
JavaScript还有一个三元运算符（即该运算符需要三个运算子）`?:`，也可以用于逻辑判断。

```
(条件) ? 表达式1 : 表达式2
```
上面代码中，如果“条件”为`true`，则返回“表达式1”的值，否则返回“表达式2”的值。

这个三元运算符可以被视为`if…else…`的简写形式，因此可以用于多种场合。

```
var myVar;
console.log(
  myVar ?
  'myVar has a value' :
  'myVar do not has a value'
)
// myVar do not has a value
```
上面代码利用三元运算符，输出相应的提示。

```
var msg = '数字' + n + '是' + (n % 2 === 0 ? '偶数' : '奇数');
```
上面代码利用三元运算符，在字符串之中插入不同的值。

## 循环语句

循环语句用于重复执行某个操作，它有多种形式。

### while 循环
`while`语句包括一个循环条件和一段代码块，只要条件为真，就不断循环执行代码块。

```
while (条件)
  语句;

// 或者
while (条件) 语句;
```

`while`语句的循环条件是一个表达式，必须放在圆括号中。代码块部分，如果只有一条语句，可以省略大括号，否则就必须加上大括号。

```
while (条件) {
  语句;
}
```

### for 循环

`for`语句是循环命令的另一种形式，可以指定循环的起点、终点和终止条件。它的格式如下。

```
for (初始化表达式; 条件; 递增表达式)
  语句

// 或者

for (初始化表达式; 条件; 递增表达式) {
  语句
}
```
for语句后面的括号里面，有三个表达式。

> 初始化表达式（initialize）：确定循环变量的初始值，只在循环开始时执行一次。
> 条件表达式（test）：每轮循环开始时，都要执行这个条件表达式，只有值为真，才继续进行循环。
> 递增表达式（increment）：每轮循环的最后一个操作，通常用来递增循环变量。

**所有for循环，都可以改写成while循环。**

**for语句的三个部分（initialize、test、increment），可以省略任何一个，也可以全部省略。**

```
for ( ; ; ){
  console.log('Hello World');
}
```
上面代码省略了for语句表达式的三个部分，结果就导致了一个无限循环。

### do…while 循环
`do...while`循环与`while`循环类似，唯一的区别就是**先运行一次循环体，然后判断循环条件。**

```
do
  语句
while (条件);

// 或者
do {
  语句
} while (条件);
```
不管条件是否为真，do…while循环至少运行一次，这是这种结构最大的特点。另外，**`while`语句后面的分号注意不要省略。**

### break 语句和 continue 语句

`break`语句和`continue`语句都具有跳转作用，可以让代码不按既有的顺序执行。

`break`语句用于**跳出代码块或循环**。`for`循环也可以使用`break`语句跳出循环。

`continue`语句用于**立即终止本轮循环**，返回循环结构的头部，开始下一轮循环。

如果存在多重循环，不带参数的`break`语句和`continue`语句都只针对最内层循环。

### 标签（label）
JavaScript 语言允许，**语句的前面有标签（label），相当于定位符，用于跳转到程序的任意位置**，标签的格式如下。
```
label:
  语句
```

标签可以是任意的标识符，但不能是保留字，语句部分可以是任意语句。

标签通常与break语句和continue语句配合使用，跳出特定的循环。

```
top:
  for (var i = 0; i < 3; i++){
    for (var j = 0; j < 3; j++){
      if (i === 1 && j === 1) break top;
      console.log('i=' + i + ', j=' + j);
    }
  }
// i=0, j=0
// i=0, j=1
// i=0, j=2
// i=1, j=0
```
上面代码为一个双重循环区块，`break`命令后面加上了`top`标签（注意，top不用加引号），满足条件时，直接跳出双层循环。如果`break`语句后面不使用标签，则只能跳出内层循环，进入下一次的外层循环。

`continue`语句也可以与标签配合使用。

```
top:
  for (var i = 0; i < 3; i++){
    for (var j = 0; j < 3; j++){
      if (i === 1 && j === 1) continue top;
      console.log('i=' + i + ', j=' + j);
    }
  }
// i=0, j=0
// i=0, j=1
// i=0, j=2
// i=1, j=0
// i=2, j=0
// i=2, j=1
// i=2, j=2
```
上面代码中，`continue`命令后面有一个标签名，满足条件时，会跳过当前循环，直接进入下一轮外层循环。如果`continue`语句后面不使用标签，则只能进入下一轮的内层循环。

# 数据类型
## 概述
JavaScript 语言的每一个值，都属于某一种数据类型。JavaScript的数据类型，共有六种，具体如下。（ES6 又新增了第七种 Symbol 类型的值）

> 数值（number）：整数和小数（比如1和3.14）
> 字符串（string）：文本（比如Hello World）。
> 布尔值（boolean）：表示真伪的两个特殊值，即`true`（真）和`false`（假）。
> undefined：表示“未定义”或不存在，即由于目前没有定义，所以此处暂时没有任何值。
> null：表示空值，即此处的值为空。
> 对象（object）：各种值组成的集合。

通常，数值、字符串、布尔值这三种类型，合称为原始类型（primitive type）的值，即它们是最基本的数据类型，不能再细分了。对象则称为合成类型（complex type）的值，因为一个对象往往是多个原始类型的值的合成，可以看作是一个存放各种值的容器。至于`undefined`和`null`，一般将它们看成两个特殊值。

其中，对象是最复杂的数据类型，又可以分成三个子类型。

> 狭义的对象（object）
> 数组（array）
> 函数（function）

狭义的对象和数组是两种不同的数据组合方式，除非特别声明，本教程的”对象“都特指狭义的对象。函数其实是处理数据的方法，JavaScript把它当成一种数据类型，可以赋值给变量，这为编程带来了很大的灵活性，也为 JavaScript 的“函数式编程”奠定了基础。

## typeof 运算符

JavaScript 有三种方法，可以确定一个值到底是什么类型。

> `typeof`运算符
> `instanceof`运算符
> `Object.prototype.toString`方法

`instanceof`运算符和`Object.prototype.toString`方法，将在后文介绍。这里介绍`typeof`运算符。

`typeof`运算符可以返回一个值的数据类型。数值、字符串、布尔值分别返回`number`、`string`、`boolean`，函数返回`function`，`undefined`返回`undefined`（利用这一点，`typeof`可以用来检查一个没有声明的变量，而不报错），对象返回`object`，`null`返回`object`。

```
v
// ReferenceError: v is not defined

typeof v
// "undefined"
```
上面代码中，变量`v`没有用`var`命令声明，直接使用就会报错。但是，放在`typeof`后面，就不报错了，而是返回`undefined`。实际编程中，这个特点通常用在判断语句，可以提高程序的健壮性。

```
// 错误的写法
if (v) {
  // ...
}
// ReferenceError: v is not defined

// 正确的写法
if (typeof v === "undefined") {
  // ...
}
typeof window // "object"
typeof {} // "object"
typeof [] // "object"
```
上面代码中，空数组（`[]`）的类型也是`object`，这表示在 JavaScript 内部，**数组本质上只是一种特殊的对象**。这里顺便提一下，`instanceof`运算符可以区分数组和对象。`instanceof`运算符的详细解释，请见《面向对象编程》一章。

```
var o = {};
var a = [];

o instanceof Array // false
a instanceof Array // true
```

另外，`null`的类型是`object`，这是由于历史原因造成的。1995年的 JavaScript 语言第一版，只设计了五种数据类型（对象、整数、浮点数、字符串和布尔值），没考虑`null`，只把它当作object的一种特殊值。后来`null`独立出来，作为一种单独的数据类型，为了兼容以前的代码，`typeof null`返回`object`就没法改变了。

## null 和 undefined

### 概述

`null`与`undefined`都可以表示“没有”，含义非常相似。将一个变量赋值为`undefined`或`null`，实际上在语法效果上几乎没区别。在`if`语句中，它们都会被自动转为`false`，相等运算符（`==`）甚至直接报告两者相等。

```
if (!undefined) {
  console.log('undefined is false');
}
// undefined is false

if (!null) {
  console.log('null is false');
}
// null is false

undefined == null
// true
```
含义与用法都差不多，同时设置两个这样的值其实与历史原因有关。
1995年 JavaScript 诞生时，最初像 Java 一样，只设置了`null`表示”无”。根据 C 语言的传统，`null`可以自动转为`0`。
但是，JavaScript 的设计者 Brendan Eich，觉得这样做还不够。首先，第一版的 JavaScript 里面，`null`就像在 Java 里一样，被当成一个对象，Brendan Eich 觉得表示“无”的值最好不是对象。其次，那时的 JavaScript 不包括错误处理机制，Brendan Eich 觉得，如果`null`自动转为`0`，很不容易发现错误。
因此，他又设计了一个`undefined`。区别是这样的：**`null`是一个表示“空”的对象，转为数值时为`0`；`undefined`是一个表示”此处无定义”的原始值，转为数值时为`NaN`**。

### 用法和含义

对于`null`和`undefined`，大致可以像下面这样理解。

`null`表示空值，即该处的值现在为空。调用函数时，某个参数未设置任何值，这时就可以传入`null`，表示该参数为空。比如，某个函数接受引擎抛出的错误作为参数，如果运行过程中未出错，那么这个参数就会传入`null`，表示未发生错误。

`undefined`表示“未定义”，下面是返回`undefined`的典型场景。

```
// 变量声明了，但没有赋值
var i;
i // undefined

// 调用函数时，应该提供的参数没有提供，该参数等于 undefined
function f(x) {
  return x;
}
f() // undefined

// 对象没有赋值的属性
var o = new Object();
o.p // undefined

// 函数没有返回值时，默认返回 undefined
function f() {}
f() // undefined
```

## 布尔值

布尔值代表“真”和“假”两个状态。“真”用关键字`true`表示，“假”用关键字`false`表示。布尔值只有这两个值。

下列运算符会返回布尔值：

> 两元逻辑运算符： && (And)，|| (Or)
> 前置逻辑运算符： ! (Not)
> 相等运算符：===，!==，==，!=
> 比较运算符：>，>=，<，<=

**如果JavaScript预期某个位置应该是布尔值（比如`if`后的判断语句），会将该位置上现有的值自动转为布尔值**。转换规则是除了下面六个值被转为`false`，其他值都视为`true`。

> undefined
> null
> false
> 0
> NaN
> “”或’’（空字符串）

**注意，空数组（`[]`）和空对象（`{}`）对应的布尔值，都是true。**

# 数值

## 概述

### 整数和浮点数

JavaScript内部，**所有数字都是以64位浮点数形式储存**，即使整数也是如此。所以，1与1.0是相同的，是同一个数。

```
1 === 1.0 // true
```

这就是说，JavaScript语言的底层根本没有整数，所有数字都是小数（64位浮点数）。容易造成混淆的是，某些运算只有整数才能完成，此时JavaScript会自动把64位浮点数，转成32位整数，然后再进行运算，参见《运算符》一章的”位运算“部分。

由于浮点数不是精确的值，所以涉及小数的比较和运算要特别小心。

```
0.1 + 0.2 === 0.3
// false

0.3 / 0.1
// 2.9999999999999996

(0.3 - 0.2) === (0.2 - 0.1)
// false
```

### 数值精度

根据国际标准 IEEE 754，JavaScript 浮点数的64个二进制位，从最左边开始，是这样组成的。

> 第1位：符号位，0表示正数，1表示负数
> 第2位到第12位（共11位）：指数部分
> 第13位到第64位（共52位）：小数部分（即有效数字）

符号位决定了一个数的正负，指数部分决定了数值的大小，小数部分决定了数值的精度。

JavaScript提供的有效数字最长为53个二进制位，即精度最多只能到53个二进制位，这意味着，绝对值小于等于2的53次方的整数，即-2的53次方到2的53次方，都可以精确表示。大于2的53次方的数值，都无法保持精度。由于2的53次方是一个16位的十进制数值，所以简单的法则就是，JavaScript对15位的十进制数都可以精确处理。

```
Math.pow(2, 53)
// 9007199254740992

// 多出的三个有效数字，将无法保存
9007199254740992111
// 9007199254740992000
```
上面示例表明，大于2的53次方以后，多出来的有效数字（最后三位的`111`）都会无法保存，变成`0`。

### 数值范围

根据标准，64位浮点数的指数部分的长度是11个二进制位，意味着指数部分的最大值是2047（2的11次方减1）。也就是说，64位浮点数的指数部分的值最大为2047，分出一半表示负数，则** JavaScript 能够表示的数值范围为21024到2-1023（开区间），超出这个范围的数无法表示。**

如果一个数大于等于2的1024次方，那么就会发生“正向溢出”，即 JavaScript 无法表示这么大的数，这时就会返回`Infinity`。

```
Math.pow(2, 1024) // Infinity
```

如果一个数小于等于2的-1075次方（指数部分最小值-1023，再加上小数部分的52位），那么就会发生为“负向溢出”，即 JavaScript 无法表示这么小的数，这时会直接返回0。
```
Math.pow(2, -1075) // 0
```

下面是一个实际的例子。
```
var x = 0.5;

for(var i = 0; i < 25; i++) {
  x = x * x;
}

x // 0
```
上面代码中，对0.5连续做25次平方，由于最后结果太接近0，超出了可表示的范围，JavaScript 就直接将其转为0。

JavaScript提供**Number对象的`MAX_VALUE`和`MIN_VALUE`属性**，返回可以表示的具体的最大值和最小值。

```
Number.MAX_VALUE // 1.7976931348623157e+308
Number.MIN_VALUE // 5e-324
```

## 数值的表示法
JavaScript的数值有多种表示方法，可以用字面形式直接表示，比如`35`（十进制）和`0xFF`（十六进制）。数值也可以采用科学计数法表示，例子如下。

```
123e3 // 123000
123e-3 // 0.123
-3.1E+12
.1e-23
```

科学计数法允许字母`e`或`E`的后面，跟着一个整数，表示这个数值的指数部分。

以下两种情况，JavaScript 会自动将数值转为科学计数法表示，其他情况都采用字面形式直接表示。

**（1）小数点前的数字多于21位。**

```
1234567890123456789012
// 1.2345678901234568e+21

123456789012345678901
// 123456789012345680000
```

**（2）小数点后的零多于5个。**

```
// 小数点后紧跟5个以上的零，
// 就自动转为科学计数法
0.0000003 // 3e-7

// 否则，就保持原来的字面形式
0.000003 // 0.000003
```


## 数值的进制
使用字面量（literal）直接表示一个数值时，JavaScript对整数提供四种进制的表示方法：十进制、十六进制、八进制、二进制。

> 十进制：没有前导`0`的数值。
> 八进制：有前缀`0o`或`0O`的数值，或者有前导`0`、且只用到0-7的八个阿拉伯数字的数值。
> 十六进制：有前缀`0x`或`0X`的数值。
> 二进制：有前缀`0b`或`0B`的数值。

默认情况下，JavaScript 内部会自动将八进制、十六进制、二进制转为十进制。下面是一些例子。

```
025 // 21，因为有前导并且后边的阿拉伯数字没有超过8
029 // 29，有前导但是后边的阿拉伯数字超过了8
0xff // 255
0o377 // 255
0b11 // 3
```

通常来说，有前导`0`的数值会被视为八进制，但是如果前导`0`后面有数字8和9，则该数值被视为十进制。

如果八进制、十六进制、二进制的数值里面，出现不属于该进制的数字，就会报`SyntaxError`错误。前导`0`表示八进制，处理时很容易造成混乱。ES5的严格模式和ES6，已经废除了这种表示法，但是浏览器为了兼容以前的代码，目前还继续支持这种表示法。

## 特殊数值

### 正零和负零

前面说过，JavaScript的64位浮点数之中，有一个二进制位是符号位。这意味着，**任何一个数都有一个对应的负值，就连0也不例外。**

JavaScript内部实际上存在2个`0`：一个是`+0`，一个是`-0`，区别就是64位浮点数表示法的符号位不同。它们是等价的。

```
-0 === +0 // true
0 === -0 // true
0 === +0 // true
```

几乎所有场合，正零和负零都会被当作正常的`0`。**唯一有区别的场合是，`+0`或`-0`当作分母，返回
的值是不相等的。**

```
(1 / +0) === (1 / -0) // false
```
上面的代码之所以出现这样结果，是因为除以正零得到`+Infinity`，除以负零得到`-Infinity`，这两者是不相等的（关于Infinity详见下文）。

### NaN

#### 含义

`NaN`是JavaScript的特殊值，表示“非数字”（Not a Number），主要出现在将字符串解析成数字出错的场合，比如。

```
5 - 'x' // NaN
```
上面代码运行时，会自动将字符串`x`转为数值，但是由于`x`不是数值，所以最后得到结果为`NaN`，表示它是“非数字”（`NaN`）。

另外，一些数学函数的运算结果会出现`NaN`。

```
Math.acos(2) // NaN
Math.log(-1) // NaN
Math.sqrt(-1) // NaN
```

`0`除以`0`也会得到`NaN`。

需要注意的是，**`NaN`不是独立的数据类型，而是一个特殊数值，它的数据类型依然属于`Number`**，使用`typeof`运算符可以看得很清楚。

```
typeof NaN // 'number'
```


#### 运算规则

`NaN`不等于任何值，包括它本身。
```
NaN === NaN // false
```

数组的`indexOf`方法内部使用的是严格相等运算符，所以该方法对`NaN`不成立。
```
[NaN].indexOf(NaN) // -1
```

`NaN`在布尔运算时被当作`false`。
```
Boolean(NaN) // false
```

`NaN`与任何数（包括它自己）的运算，得到的都是`NaN`。
```
NaN + 32 // NaN
NaN - 32 // NaN
NaN * 32 // NaN
NaN / 32 // NaN
```

### Infinity

#### 含义

`Infinity`表示“无穷”，用来表示两种场景。一种是一个正的数值太大，或一个负的数值太小，无法表示；另一种是非`0`数值除以`0`，得到`Infinity`。

**`Infinity`有正负之分**，`Infinity`表示正的无穷，`-Infinity`表示负的无穷，它们是不相等的。

由于数值正向溢出（overflow）、负向溢出（underflow）和被`0`除，JavaScript都不报错，而是返回`Infinity`，所以单纯的数学运算几乎没有可能抛出错误。

**`Infinity`大于一切数值（除了`NaN`）**，**`-Infinity`小于一切数值（除了`NaN`）**。`Infinity`与`NaN`比较，总是返回`false`。
```
Infinity > 1000 // true
-Infinity < -1000 // true
```

#### 运算规则

`Infinity`的四则运算，符合无穷的数学计算规则。

```
5 * Infinity // Infinity
5 - Infinity // -Infinity
Infinity / 5 // Infinity
5 / Infinity // 0

// 0乘以Infinity，返回NaN；0除以Infinity，返回0；Infinity除以0，返回Infinity。
0 * Infinity // NaN
0 / Infinity // 0
Infinity / 0 // Infinity

// Infinity加上或乘以Infinity，返回的还是Infinity。
Infinity + Infinity // Infinity
Infinity * Infinity // Infinity

// Infinity减去或除以Infinity，得到NaN。
Infinity - Infinity // NaN
Infinity / Infinity // NaN

// Infinity与null计算时，null会转成0，等同于与0的计算。
null * Infinity // NaN
null / Infinity // 0
Infinity / null // Infinity

// Infinity与undefined计算，返回的都是NaN。
undefined + Infinity // NaN
undefined - Infinity // NaN
undefined * Infinity // NaN
undefined / Infinity // NaN
Infinity / undefined // NaN
```

## 与数值相关的全局方法

### parseInt()

#### 基本用法

`parseInt`方法**用于将字符串转为整数**。如果字符串头部有空格，空格会被自动去除。

```
parseInt('123') // 123
```

如果parseInt的参数不是字符串，则会先转为字符串再转换。字符串转为整数的时候，是一个个字符依次转换，**如果遇到不能转为数字的字符，就不再进行下去，返回已经转好的部分**。

```
parseInt(1.23) // 1
// 等同于
parseInt('1.23') // 1

parseInt('8a') // 8
parseInt('12**') // 12
parseInt('12.34') // 12
parseInt('15e2') // 15
parseInt('15px') // 15
```

而如果字符串的第一个字符不能转化为数字（后面跟着数字的正负号除外），返回`NaN`。

```
parseInt('abc') // NaN
parseInt('.3') // NaN
parseInt('') // NaN
parseInt('+') // NaN

parseInt('+1') // 1
```
所以，**`parseInt`的返回值只有两种可能，要么是一个十进制整数，要么是`NaN`。**

如果字符串以`0x`或`0X`开头，`parseInt`会将其按照十六进制数解析。如果字符串以`0`开头，将其按照`10`进制解析。而对于那些会自动转为科学计数法的数字，`parseInt`会将科学计数法的表示方法视为字符串，因此导致一些奇怪的结果。

```
parseInt(1000000000000000000000.5) // 1
// 等同于
parseInt('1e+21') // 1

parseInt(0.0000008) // 8
// 等同于
parseInt('8e-7') // 8
```

#### 进制转换

`parseInt`方法还可以接受第二个参数（2到36之间），表示被解析的值的进制，返回该值对应的十进制数。默认情况下，`parseInt`的第二个参数为10，即默认是十进制转十进制。

```
parseInt('1000') // 1000
// 等同于
parseInt('1000', 10) // 1000

parseInt('1000', 2) // 8
parseInt('1000', 6) // 216
parseInt('1000', 8) // 512
```

上面代码中，二进制、六进制、八进制的1000，分别等于十进制的8、216和512。这意味着，可以用`parseInt`方法进行进制的转换。

如果第二个参数不是数值，会被自动转为一个整数。这个整数只有在2到36之间，才能得到有意义的结果，超出这个范围，则返回`NaN`。**如果第二个参数是`0`、`undefined`和`null`，则直接忽略。**

```
parseInt('10', 37) // NaN
parseInt('10', 1) // NaN

parseInt('10', 0) // 10
parseInt('10', null) // 10
parseInt('10', undefined) // 10
```

如果字符串包含对于指定进制无意义的字符，则从最高位开始，只返回可以转换的数值。如果最高位无法转换，则直接返回`NaN`。比如要将一个字符串转换为二进制，但是字符串中出现了大于等于2的值。

```
parseInt('1546', 2) // 1
parseInt('546', 2) // NaN
```

前面说过，如果`parseInt`的第一个参数不是字符串，会被先转为字符串。因此使用的时候一定要十分小心。下面举出几个例子：

```
parseInt(0x11, 36) // 43
parseInt(0x11, 2) // 1

parseInt('0x11', 2) // 0

// 等同于
parseInt(String(0x11), 36)
parseInt(String(0x11), 2)

// 等同于
parseInt('17', 36)
parseInt('17', 2)
```
上面代码中，十六进制的`0x11`会被先转为十进制的17，再转为字符串。然后，再用36进制或二进制解读字符串17，最后返回结果43和1。

这种处理方式，对于八进制的前缀`0`，尤其需要注意。

```
parseInt(011, 2) // 011首先被转换为字符串9，9不是二进制的有效字符，返回NaN

// 等同于
parseInt(String(011), 2)

// 等同于
parseInt(String(9), 2)


parseInt('011', 2)  // 011被当作二进制处理，返回3
```

### parseFloat()

`parseFloat`方法**用于将一个字符串转为浮点数**，会自动过滤字符串前导的空格。

如果字符串符合科学计数法，则会进行相应的转换。而`parseInt`是不会对符合科学记数法的字符串进行转换的。

如果字符串包含不能转为浮点数的字符，则不再进行往后转换，返回已经转好的部分。

如果参数不是字符串，或者字符串的第一个字符不能转化为浮点数，则返回`NaN`。

```
parseFloat('3.14') // 3.14

parseFloat('314e-2') // 3.14
parseFloat('0.0314E+2') // 3.14

parseFloat('3.14more non-digit characters') // 3.14

parseFloat([]) // NaN
parseFloat('FF2') // NaN
parseFloat('') // NaN
```
上面代码中，尤其值得注意，**`parseFloat`会将空字符串转为`NaN`**。

这些特点使得`parseFloat`的转换结果不同于`Number`函数。

```
parseFloat(true)  // NaN
Number(true) // 1

parseFloat(null) // NaN
Number(null) // 0

parseFloat('') // NaN
Number('') // 0

parseFloat('123.45#') // 123.45
Number('123.45#') // NaN
```


### isNaN()

`isNaN`方法可以用来判断一个值是否为`NaN`。

```
isNaN(NaN) // true
isNaN(123) // false
```

但是，`isNaN`只对数值有效，**如果传入其他值，会被先转成数值**。比如，传入字符串的时候，字符串会被先转成`NaN`，所以最后返回`true`，这一点要特别引起注意。也就是说，`isNaN`为`true`的值，有可能不是`NaN`，而是一个字符串。出于同样的原因，对于对象和数组，`isNaN`也返回`true`。

```
isNaN('Hello') // true
// 相当于
isNaN(Number('Hello')) // true

isNaN({}) // true
// 等同于
isNaN(Number({})) // true

isNaN(['xzy']) // true
// 等同于
isNaN(Number(['xzy'])) // true
```

但是，**对于空数组和只有一个数值成员的数组，`isNaN`返回`false`。**

```
isNaN([]) // false
isNaN([123]) // false
isNaN(['123']) // false
```
上面代码之所以返回`false`，原因是这些数组能被`Number`函数转成数值。

总结来说，**能被`Number`转换为数值类型的数据，使用`isNaN`就是有效的**。因此，使用`isNaN`之前，最好判断一下数据类型。
```
function myIsNaN(value) {
  return typeof value === 'number' && isNaN(value);
}
```

判断`NaN`更可靠的方法是，利用**`NaN`为唯一不等于自身的值**的这个特点，进行判断。
```
function myIsNaN(value) {
  return value !== value;
}
```

### isFinite()

`isFinite`方法返回一个布尔值，表示某个值是否为正常的数值。

```
isFinite(Infinity) // false
isFinite(-Infinity) // false
isFinite(NaN) // false
isFinite(undefined) // false
isFinite(null) // true
isFinite(-1) // true
```

除了`Infinity`、`-Infinity`、`NaN`和`undefined`这几个值会返回`false`，`isFinite`对于其他的数值都会返回`true`。

# 字符串

## 概述

### 定义

字符串就是零个或多个排在一起的字符，放在单引号或双引号之中。

单引号字符串的内部，可以使用双引号。双引号字符串的内部，可以使用单引号。如果要在单引号字符串的内部，使用单引号，就必须在内部的单引号前面加上反斜杠`\`，用来转义。双引号字符串内部使用双引号，也是如此。

由于 HTML 语言的属性值使用双引号，所以很多项目约定 JavaScript 语言的字符串只使用单引号，本教程遵守这个约定。当然，只使用双引号也完全可以。重要的是坚持使用一种风格，不要一会使用单引号表示字符串，一会又使用双引号表示。

字符串默认只能写在一行内，分成多行将会报错。如果长字符串必须分成多行，可以在每一行的尾部使用反斜杠`\`。注意，**反斜杠的后面必须是换行符，而不能有其他字符**（比如空格），**否则会报错**。

连接运算符（+）可以连接多个单行字符串，将长字符串拆成多行书写，输出的时候也是单行。


如果想输出多行字符串，有一种利用多行注释的变通方法。
```
(function () { /*
line 1
line 2
line 3
*/}).toString().split('\n').slice(1, -1).join('\n')
// "line 1
// line 2
// line 3"
```

### 转义

反斜杠（`\`）在字符串内有特殊含义，用来表示一些特殊字符，所以又称为转义符。

需要用反斜杠转义的特殊字符，主要有下面这些。
> \0 ：null（\u0000）
> \b ：后退键（\u0008）
> \f ：换页符（\u000C）
> \n ：换行符（\u000A）
> \r ：回车键（\u000D）
> \t ：制表符（\u0009）
> \v ：垂直制表符（\u000B）
> \' ：单引号（\u0027）
> \" ：双引号（\u0022）
> \\ ：反斜杠（\u005C）

反斜杠还有三种特殊用法。
* \HHH ：反斜杠后面紧跟三个八进制数（000到377），代表一个字符。HHH对应该字符的 Unicode 码点，比如\251表示版权符号。显然，这种方法只能输出256种字符。

* \xHH ：\x后面紧跟两个十六进制数（00到FF），代表一个字符。HH对应该字符的 Unicode 码点，比如\xA9表示版权符号。这种方法也只能输出256种字符。

* \uXXXX ：\u后面紧跟四个十六进制数（0000到FFFF），代表一个字符。XXXX对应该字符的 Unicode 码点，比如\u00A9表示版权符号。

如果**在非特殊字符前面使用反斜杠，则反斜杠会被省略**。

### 字符串与数组

字符串可以被视为字符数组，因此可以使用数组的方括号运算符，用来返回某个位置的字符（位置编号从0开始）。如果方括号中的数字超过字符串的长度，或者方括号中根本不是数字，则返回`undefined`。
```
var s = 'hello';
s[0] // "h"
s[1] // "e"
s[4] // "o"

// 直接对字符串使用方括号运算符
'hello'[1] // "e"
```
但是，字符串与数组的相似性仅此而已。实际上，**无法改变字符串之中的单个字符**。
```
var s = 'hello';

delete s[0];
s // "hello"

s[1] = 'a';
s // "hello"

s[5] = '!';
s // "hello"
```

### length 属性

`length`**属性**返回字符串的长度，该属性也是无法改变的。因为是属性所以不用加`()`。改变length属性不会生效，也不会报错。

## 字符集

JavaScript 使用 Unicode 字符集。JavaScript 引擎内部，所有字符都用 Unicode 表示。JavaScript 不仅以 Unicode 储存字符，还允许直接在程序中使用 Unicode 码点表示字符，即将字符写成`\uxxxx`的形式，其中`xxxx`代表该字符的 Unicode 码点。比如，`\u00A9`代表版权符号。

解析代码的时候，JavaScript 会自动识别一个字符是字面形式表示，还是 Unicode 形式表示。输出给用户的时候，所有字符都会转成字面形式。
```
var f\u006F\u006F = 'abc';
foo // "abc"
```
上面代码中，第一行的变量名foo是 Unicode 形式表示，第二行是字面形式表示。JavaScript 会自动识别。

我们还需要知道，每个字符在 JavaScript 内部都是以16位（即2个字节）的 UTF-16 格式储存。也就是说，JavaScript 的单位字符长度固定为16位长度，即2个字节。

## Base64 转码

有时，文本里面包含一些不可打印的符号，比如 ASCII 码0到31的符号都无法打印出来，这时可以使用 Base64 编码，将它们转成可以打印的字符。另一个场景是，有时需要以文本格式传递二进制数据，那么也可以使用 Base64 编码。

所谓 Base64 就是一种编码方法，可以将任意值转成 0～9、A～Z、a-z、+和/这64个字符组成的可打印字符。使用它的主要目的，不是为了加密，而是为了不出现特殊字符，简化程序的处理。

JavaScript 原生提供两个 Base64 相关的方法。

> btoa()：任意值转为 Base64 编码
> atob()：Base64 编码转为原来的值

```
var string = 'Hello World!';
btoa(string) // "SGVsbG8gV29ybGQh"
atob('SGVsbG8gV29ybGQh') // "Hello World!"
```
注意，这两个方法不适合非 ASCII 码的字符，会报错。
```
btoa('你好') 
```

要将非 ASCII 码字符转为 Base64 编码，必须中间插入一个转码环节，再使用这两个方法。encodeURIComponent方法不会对 ASCII 字母和数字进行编码，也不会对ASCII 标点符号进行编码。
```
function b64Encode(str) {
  return btoa(encodeURIComponent(str));
}

function b64Decode(str) {
  return decodeURIComponent(atob(str));
}

b64Encode('你好') // "JUU0JUJEJUEwJUU1JUE1JUJE"
b64Decode('JUU0JUJEJUEwJUU1JUE1JUJE') // "你好"
```

# 对象

## 概述

### 生成方法

对象（object）是 JavaScript 语言的核心概念，也是最重要的数据类型。

什么是对象？简单说，对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合。

```
var obj = {
  foo: 'Hello',
  bar: 'World'
};
```
上面代码中，大括号就定义了一个对象，它被赋值给变量·obj·，所以变量·obj·就指向一个对象。

### 键名

**对象的所有键名都是字符串**（ES6又引入了Symbol值也可以作为键值），所以**加不加引号都可以**。**如果键名是数值，会被自动转为字符串。**

```
var obj = {
  1: 'a',
  3.2: 'b',
  1e2: true,
  1e-2: true,
  .234: true,
  0xFF: true
};

obj
// Object {
//   1: "a",
//   3.2: "b",
//   100: true,
//   0.01: true,
//   0.234: true,
//   255: true
// }

obj['100'] // true
```
上面代码中，对象`obj`的所有键名虽然看上去像数值，实际上都被自动转成了字符串。

**如果键名不符合标识名的条件**（比如第一个字符为数字，或者含有空格或运算符），**且也不是数字**，**则必须加上引号**，否则会报错。

```
// 报错
var obj = {
  1p: 'Hello World'
};

// 不报错
var obj = {
  '1p': 'Hello World',
  'h w': 'Hello World',
  'p+q': 'Hello World'
};
```

对象的每一个键名又称为“属性”（property），它的“键值”可以是任何数据类型。**如果一个属性的值为函数，通常把这个属性称为“方法”，它可以像函数那样调用**。
```
var obj = {
  p: function (x) {
    return 2 * x;
  }
};

obj.p(1) // 2
```

如果属性的值还是一个对象，就形成了链式引用。
```
var o1 = {};
var o2 = { bar: 'hello' };

o1.foo = o2;
o1.foo.bar // "hello"
```
上面代码中，对象`o1`的属性`foo`指向对象`o2`，就可以链式引用`o2`的属性。

**对象的属性之间用逗号分隔**，最后一个属性后面可以加逗号（trailing comma），也可以不加。

**属性可以动态创建，不必在对象声明时就指定。**

### 对象的引用

如果不同的变量名指向同一个对象，那么它们都是这个对象的引用，也就是说指向同一个内存地址。**修改其中一个变量，会影响到其他所有变量。**

```
var o1 = {};"hello world"
var o2 = o1;

o1.a = 1;
o2.a // 1

o2.b = 2;
o1.b // 2
```
上面代码中，`o1`和`o2`指向同一个对象，因此为其中任何一个变量添加属性，另一个变量都可以读写该属性。

此时，如果取消某一个变量对于原对象的引用，不会影响到另一个变量。

```
var o1 = {};
var o2 = o1;

o1 = 1;
o2 // {}
```
上面代码中，`o1`和`o2`指向同一个对象，然后`o1`的值变为1，这时不会对`o2`产生影响，`o2`还是指向原来的那个对象。

但是，**这种引用只局限于对象，如果两个变量指向同一个原始类型的值，那么，变量这时都是值的拷贝。**

```
var x = 1;
var y = x;

x = 2;
y // 1
```
上面的代码中，当`x`的值发生变化后，`y`的值并不变，这就表示`y`和`x`并不是指向同一个内存地址。

### 表达式还是语句？

对象采用大括号表示，这导致了一个问题：**如果行首是一个大括号，它到底是表达式还是语句？**

```
{ foo: 123 }
```

JavaScript 引擎读到上面这行代码，会发现可能有两种含义。第一种可能是，这是一个表达式，表示一个包含`foo`属性的对象；第二种可能是，这是一个语句，表示一个代码区块，里面有一个标签`foo`，指向表达式`123`。

为了避免这种歧义，JavaScript 规定，**如果行首是大括号，一律解释为语句（即代码块）。如果要解释为表达式（即对象），必须在大括号前加上圆括号。**

```
{ foo: 123 } // 解释为语句
({ foo: 123}) // 解释为对象
```

这种差异在`eval`语句（作用是对字符串求值）中反映得最明显。
```
eval('{foo: 123}') // 123
eval('({foo: 123})') // {foo: 123}
```

## 属性的操作

### 读取属性

读取对象的属性，有两种方法，一种是**使用点运算符**，还有一种是**使用方括号运算符**。

```
var obj = {
  p: 'Hello World'
};

obj.p // "Hello World"
obj['p'] // "Hello World"
```
上面代码分别采用点运算符和方括号运算符，读取属性`p`。

请注意，如果**使用方括号运算符，键名必须放在引号里面，否则会被当作变量处理**。

```
var foo = 'bar';

var obj = {
  foo: 1,
  bar: 2
};

obj.foo  // 1
obj[foo]  // 2
```
上面代码中，引用对象`obj`的`foo`属性时，如果使用点运算符，`foo`就是字符串；如果使用方括号运算符，但是不使用引号，那么`foo`就是一个变量，指向字符串`bar`。

方括号运算符内部还可以使用表达式。
```
obj['hello' + ' world']
obj[3 + 3]
```

**数字键可以不加引号，因为会自动转成字符串。**
```
var obj = {
  0.7: 'Hello World'
};

obj['0.7'] // "Hello World"
obj[0.7] // "Hello World"
```
上面代码中，对象`obj`的数字键`0.7`，加不加引号都可以，因为会被自动转为字符串。

注意，**数值键名不能使用点运算符（因为会被当成小数点），只能使用方括号运算符**。
```
var obj = {
  123: 'hello world'
};

obj.123 // 报错
obj[123] // "hello world"
```

### 属性的赋值

点运算符和方括号运算符，不仅可以用来读取值，还可以用来赋值。

JavaScript 允许属性的“后绑定”，也就是说，你**可以在任意时刻新增属性，没必要在定义对象的时候，就定义好属性**。

```
var obj = { p: 1 };

// 等价于

var obj = {};
obj.p = 1;
```

### 查看所有属性

查看一个对象本身的所有属性，可以使用`Object.keys`方法。

```
var obj = {
  key1: 1,
  key2: 2
};

Object.keys(obj);
// ['key1', 'key2']
```

### delete 命令

`delete`命令用于删除对象的属性，删除成功后返回`true`。如果再次访问被删除的属性，会返回`undefined`。

```
var obj = { p: 1 };
Object.keys(obj) // ["p"]

delete obj.p // true
obj.p // undefined
Object.keys(obj) // []
```
上面代码中，`delete`命令删除对象`obj`的`p`属性。删除后，再读取`p`属性就会返回`undefined`，而且`Object.keys`方法的返回值也不再包括该属性。

注意，**删除一个不存在的属性，`delete`不报错，而且返回`true`。**

```
var obj = {};
delete obj.p // true
```
上面代码中，对象`obj`并没有`p`属性，但是`delete`命令照样返回`true`。因此，**不能根据`delete`命令的结果，认定某个属性是存在的。**

**只有一种情况，`delete`命令会返回`false`，那就是该属性存在，且不得删除。**

```
var obj = Object.defineProperty({}, 'p', {
  value: 123,
  configurable: false
});

obj.p // 123
delete obj.p // false
```

上面代码之中，对象`obj`的p属性是不能删除的，所以`delete`命令返回`false`（关于`Object.defineProperty`方法的介绍，请看《标准库》的Object对象一章）。

另外，需要注意的是，**`delete`命令只能删除对象本身的属性，无法删除继承的属性**（关于继承参见《面向对象编程》章节）。

```
var obj = {};
delete obj.toString // true
obj.toString // function toString() { [native code] }
```
上面代码中，`toString`是对象`obj`继承的属性，虽然`delete`命令返回`true`，但该属性并没有被删除，依然存在。这个例子还说明，**即使`delete`返回`true`，该属性依然可能读取到值**。

### in 运算符

`in`运算符**用于检查对象是否包含某个属性**（注意，**检查的是键名，不是键值**），如果包含就返回`true`，否则返回`false`。

```
var obj = { p: 1 };
'p' in obj // true，检查的是键名
```

**`in`运算符的一个问题是**，它**不能识别哪些属性是对象自身的，哪些属性是继承的**。

```
var obj = {};
'toString' in obj // true
```
上面代码中，`toString`方法不是对象`obj`自身的属性，而是继承的属性。但是，`in`运算符不能识别，对继承的属性也返回`true`。

### for…in 循环

`for...in`循环**用来遍历一个对象的全部属性**。

```
var obj = {a: 1, b: 2, c: 3};

for (var i in obj) {
  console.log(obj[i]);
}
// 1
// 2
// 3
```

下面是一个使用`for...in`循环，提取对象属性名的例子。

```
var obj = {
  x: 1,
  y: 2
};
var props = [];
var i = 0;

for (var p in obj) {
  props[i++] = p
}

props // ['x', 'y']
```

`for...in`循环有两个使用注意点。
> 它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性。
> 它不仅遍历对象自身的属性，还遍历继承的属性。

举例来说，对象都继承了`toString`属性，但是`for...in`循环不会遍历到这个属性。
```
var obj = {};
// toString 属性是存在的
obj.toString // toString() { [native code] }

for (var p in obj) {
  console.log(p);
} // 没有任何输出
```
上面代码中，对象`obj`继承了`toString`属性，该属性不会被`for...in`循环遍历到，因为**它默认是“不可遍历”的**。关于对象属性的可遍历性，参见《标准库》章节中 Object 一章的介绍。

如果继承的属性是可遍历的，那么就会被`for...in`循环遍历到。但是，**一般情况下，都是只想遍历对象自身的属性，所以使用`for...in`的时候，应该结合使用`hasOwnProperty`方法，在循环内部判断一下，某个属性是否为对象自身的属性。**
```
var person = { name: '老张' };

for (var key in person) {
  if (person.hasOwnProperty(key)) {
    console.log(key);
  }
}
// name
```

## with 语句

`with`语句的格式如下：
```
with (对象) {
  语句;
}
```

它的作用是操作同一个对象的多个属性时，提供一些书写的方便。

```
// 例一
var obj = {
  p1: 1,
  p2: 2,
};
with (obj) {
  p1 = 4;
  p2 = 5;
}
// 等同于
obj.p1 = 4;
obj.p2 = 5;

// 例二
with (document.links[0]){
  console.log(href);
  console.log(title);
  console.log(style);
}
// 等同于
console.log(document.links[0].href);
console.log(document.links[0].title);
console.log(document.links[0].style);
```

注意，**如果`with`区块内部有变量的赋值操作，必须是当前对象已经存在的属性，否则会创造一个当前作用域的全局变量。**

```
var obj = {};
with (obj) {
  p1 = 4;
  p2 = 5;
}

obj.p1 // undefined
p1 // 4
```
上面代码中，对象`obj`并没有`p1`属性，对`p1`赋值等于创造了一个全局变量`p1`。正确的写法应该是，先定义对象`obj`的属性`p1`，然后在`with`区块内操作它。

这是因为`with`区块没有改变作用域，它的内部依然是当前作用域。这造成了**`with`语句的一个很大的弊病，就是绑定对象不明确。**
```
with (obj) {
  console.log(x);
}
```
单纯从上面的代码块，根本无法判断`x`到底是全局变量，还是对象`obj`的一个属性。这非常不利于代码的除错和模块化，编译器也无法对这段代码进行优化，只能留到运行时判断，这就拖慢了运行速度。因此，**建议不要使用`with`语句，可以考虑用一个临时变量代替`with`**。

```
with(obj1.obj2.obj3) {
  console.log(p1 + p2);
}

// 可以写成
var temp = obj1.obj2.obj3;
console.log(temp.p1 + temp.p2);
```

# 数组

## 定义

数组（array）是按次序排列的一组值。每个值的位置都有编号（从0开始），整个数组用方括号表示。

数组的赋值除了在定义时赋值，也可以先定义后赋值。

```
var arr = ['a', 'b', 'c'];

// 或者

var arr = [];

arr[0] = 'a';
arr[1] = 'b';
arr[2] = 'c';
```

任何类型的数据，都可以放入数组。

```
var arr = [
  {a: 1},
  [1, 2, 3],
  function() {return true;}
];

arr[0] // Object {a: 1}
arr[1] // [1, 2, 3]
arr[2] // function (){return true;}
```
上面数组arr的3个成员依次是对象、数组、函数。而如果数组的元素还是数组，就形成了多维数组。

```
var a = [[1, 2], [3, 4]];
a[0][1] // 2
a[1][1] // 4
```

## 数组的本质

本质上，数组属于一种特殊的对象。`typeof`运算符会返回数组的类型是`object`。数组的特殊性体现在，它的键名是按次序排列的一组整数`（0，1，2…）`。

```
var arr = ['a', 'b', 'c'];

Object.keys(arr)
// ["0", "1", "2"]
```
上面代码中，**`Object.keys`方法返回数组的所有键名**。可以看到数组的键名就是整数0、1、2。

由于数组成员的键名是固定的（默认总是0、1、2…），因此数组不用为每个元素指定键名，而对象的每个成员都必须指定键名。JavaScript 语言规定，对象的键名一律为字符串，所以，数组的键名其实也是字符串。之所以可以用数值读取，是因为非字符串的键名会被转为字符串。

```
var arr = ['a', 'b', 'c'];

arr['0'] // 'a'
arr[0] // 'a'
```

上面代码分别用数值和字符串作为键名，结果都能读取数组。原因是数值键名被自动转为了字符串。

注意，这点在赋值时也成立。例子如下：

```
var a = [];

a[1.00] = 6;
a[1] // 6
```

上面代码中，由于`1.00`转成字符串是`1`，所以通过数字键`1`可以读取值。

我们知道，对象有两种读取成员的方法：点结构（`object.key`）和方括号结构（`object[key]`）。但是，对于数值的键名，不能使用点结构。

```
var arr = [1, 2, 3];
arr.0 // SyntaxError
```
上面代码中，`arr.0`的写法不合法，因为单独的数值不能作为标识符（identifier）。所以，数组成员只能用方括号`arr[0]`表示（方括号是运算符，可以接受数值）。

## length 属性

数组的`length`属性，返回数组的成员数量。

JavaScript 使用一个32位整数，保存数组的元素个数。这意味着，数组成员最多只有 4294967295 个（232 - 1）个，也就是说`length`属性的最大值就是 4294967295。
只要是数组，就一定有`length`属性。该属性是一个动态的值，等于键名中的最大整数加上`1`。

```
var arr = ['a', 'b'];
arr.length // 2

arr[2] = 'c';
arr.length // 3

arr[9] = 'd';
arr.length // 10

arr[1000] = 'e';
arr.length // 1001
```

上面代码表示，数组的数字键不需要连续，length属性的值总是比最大的那个整数键大1。另外，这也表明数组是一种动态的数据结构，可以随时增减数组的成员。

`length`属性是可写的。如果人为设置一个小于当前成员个数的值，该数组的成员会自动减少到`length`设置的值。

```
var arr = [ 'a', 'b', 'c' ];
arr.length // 3

arr.length = 2;
arr // ["a", "b"]
```
上面代码表示，当数组的length属性设为2（即最大的整数键只能是1）那么整数键2（值为c）就已经不在数组中了，被自动删除了。

因此，**清空数组的一个有效方法，就是将`length`属性设为0。**

如果人为设置length大于当前元素个数，则数组的成员数量会增加到这个值，新增的位置都是空位。

```
var a = ['a'];

a.length = 3;
a[1] // undefined
```
上面代码表示，当`length`属性设为大于数组个数时，读取新增的位置都会返回`undefined`。

但是，如果人为设置length为不合法的值，JavaScript 会报错。

```
// 设置负值
[].length = -1
// RangeError: Invalid array length

// 数组元素个数大于等于2的32次方
[].length = Math.pow(2, 32)
// RangeError: Invalid array length

// 设置字符串
[].length = 'abc'
// RangeError: Invalid array length
```

值得注意的是，由于数组本质上是一种对象，所以可以为数组添加属性，但是这不影响length属性的值，例子如下。

```
var a = [];

a['p'] = 'abc';
a.length // 0

a[2.1] = 'abc';
a.length // 0
```

上面代码将数组的键分别设为字符串和小数，结果都不影响`length`属性。因为，`length`属性的值就是等于最大的**数字键**加`1`，而这个数组没有整数键，所以`length`属性保持为`0`。

如果**数组的键名是添加超出范围的数值，该键名会自动转为字符串**。

```
var arr = [];
arr[-1] = 'a';
arr[Math.pow(2, 32)] = 'b';

arr.length // 0
arr[-1] // "a"
arr[4294967296] // "b"
```
上面代码中，我们为数组arr添加了两个不合法的数字键，结果**length属性没有发生变化**。这些数字键都变成了字符串键名。最后两行之所以会取到值，是因为取键值时，数字键名会默认转为字符串。

## in 运算符

检查某个**键名**是否存在的运算符`in`，适用于对象，也适用于数组。一定要注意检查的是键名。

```
var arr = [ 'a', 'b', 'c' ];
2 in arr  // true
'2' in arr // true
4 in arr // false
```
上面代码表明，数组存在键名为2的键。由于键名都是字符串，所以数值2会自动转成字符串。

注意，**如果数组的某个位置是空位，`in`运算符返回`false`**。

```
var arr = [];
arr[100] = 'a';

100 in arr // true
1 in arr // false
```
上面代码中，数组`arr`只有一个成员`arr[100]`，其他位置的键名都会返回`false`。

## for…in 循环和数组的遍历

`for...in`循环不仅可以遍历对象，也可以遍历数组，毕竟数组只是一种特殊对象。

```
var a = [1, 2, 3];

for (var i in a) {
  console.log(a[i]);
}
// 1
// 2
// 3
```

但是，`for…in`不仅会遍历数组所有的数字键，还会遍历非数字键。

```
var a = [1, 2, 3];
a.foo = true;

for (var key in a) {
  console.log(key);
}
// 0
// 1
// 2
// foo
```
上面代码在遍历数组时，也遍历到了非整数键`foo`。所以，**不推荐使用`for…in`遍历数组。数组的遍历可以考虑使用`for`循环或`while`循环**。**数组的`forEach`方法，也可以用来遍历数组。**

```
var colors = ['red', 'green', 'blue'];
colors.forEach(function (color) {
  console.log(color);
});
// red
// green
// blue
```

## 数组的空位

当数组的某个位置是空元素，即两个逗号之间没有任何值，我们称该数组存在空位（hole）。

```
var a = [1, , 1];
a.length // 3
```

上面代码表明，数组的空位个数影响`length`属性。

需要注意的是，**如果最后一个元素后面有逗号，并不会产生空位。**也就是说，有没有这个逗号，结果都是一样的。

数组的空位是可以读取的，返回`undefined`。

```
var a = [, , ,];
a[1] // undefined
```

**使用delete命令删除一个数组成员，会形成空位**，并且不会影响`length`属性。

```
var a = [1, 2, 3];
delete a[1];

a[1] // undefined
a.length // 3
```
上面代码用`delete`命令删除了数组的第二个元素，这个位置就形成了空位，但是对`length`属性没有影响。也就是说，`length`属性不过滤空位。所以，**使用`length`属性进行数组遍历，一定要非常小心。**

**数组的某个位置是空位，与某个位置是`undefined`，是不一样的。如果是空位，使用数组的`forEach`方法、`for...in`结构、以及`Object.keys`方法进行遍历，空位都会被跳过。**

```
var a = [, , ,];

a.forEach(function (x, i) {
  console.log(i + '. ' + x);
})
// 不产生任何输出

for (var i in a) {
  console.log(i);
}
// 不产生任何输出

Object.keys(a)
// []
```

但如果某个位置是`undefined`，遍历的时候就不会被跳过。

```
var a = [undefined, undefined, undefined];

a.forEach(function (x, i) {
  console.log(i + '. ' + x);
});
// 0. undefined
// 1. undefined
// 2. undefined

for (var i in a) {
  console.log(i);
}
// 0
// 1
// 2

Object.keys(a)
// ['0', '1', '2']
```

这就是说，**空位就是数组没有这个元素，所以不会被遍历到，而`undefined`则表示数组有这个元素，值是undefined，所以遍历不会跳过。**

## 类似数组的对象

如果一个对象的所有键名都是正整数或零，并且有`length`属性，那么这个对象就很像数组，语法上称为“类似数组的对象”（array-like object）。

```
var obj = {
  0: 'a',
  1: 'b',
  2: 'c',
  length: 3
};

obj[0] // 'a'
obj[1] // 'b'
obj.length // 3
obj.push('d') // TypeError: obj.push is not a function
```

上面代码中，对象`obj`就是一个类似数组的对象。但是，“类似数组的对象”并不是数组，因为**它们不具备数组特有的方法**。对象`obj`没有数组的`push`方法，使用该方法就会报错。“类似数组的对象”的根本特征，就是具有`length`属性。只要有`length`属性，就可以认为这个对象类似于数组。但是有一个问题，**这种`length`属性不是动态值，不会随着成员的变化而变化。**

```
var obj = {
  length: 0
};
obj[3] = 'd';
obj.length // 0
```

上面代码为对象`obj`添加了一个数字键，但是`length`属性没变。这就说明了`obj`不是数组。

典型的“类似数组的对象”是函数的`arguments`对象，以及大多数 DOM 元素集，还有字符串。

```
// arguments对象
function args() { return arguments }
var arrayLike = args('a', 'b');

arrayLike[0] // 'a'
arrayLike.length // 2
arrayLike instanceof Array // false

// DOM元素集
var elts = document.getElementsByTagName('h3');
elts.length // 3
elts instanceof Array // false

// 字符串
'abc'[1] // 'b'
'abc'.length // 3
'abc' instanceof Array // false
```
上面代码包含三个例子，它们都不是数组（`instanceof`运算符返回`false`），但是看上去都非常像数组。

关于arguments作一个简短的概述：在函数代码中，使用特殊对象`arguments`，开发者无需明确指出参数名，就能访问它们。

例如，在函数 `sayHi()` 中，第一个参数是`message`。用 `arguments[0]`也可以访问这个值，即第一个参数的值（第一个参数位于位置 0，第二个参数位于位置 1，依此类推）。
因此，无需明确命名参数，就可以重写函数：
```
function sayHi() {
  if (arguments[0] == "bye") {
    return;
  }

  alert(arguments[0]);
}
```

还可以用 `arguments` 对象检测函数的参数个数，引用属性 `arguments.length `即可。

用`arguments`对象判断传递给函数的参数个数，即可模拟函数重载：

```
function doAdd() {
  if(arguments.length == 1) {
    alert(arguments[0] + 5);
  } else if(arguments.length == 2) {
    alert(arguments[0] + arguments[1]);
  }
}

doAdd(10);	//输出 "15"
doAdd(40, 20);	//输出 "60"
```
当只有一个参数时，`doAdd()`函数给参数加5。如果有两个参数，则会把两个参数相加，返回它们的和。所以，`doAdd(10)`输出的是”15”，而 doAdd(40, 20) 输出的是”60”。
虽然不如重载那么好，不过已足以避开 ECMAScript 的这种限制。

回到正题，**使用数组的slice方法可以将“类似数组的对象”变成真正的数组。**
```
var arr = Array.prototype.slice.call(arrayLike);
```

除了转为真正的数组，“类似数组的对象”还有一个办法可以使用数组的方法，就是通过`call()`把数组的方法放到对象上面。

```
function print(value, index) {
  console.log(index + ' : ' + value);
}

Array.prototype.forEach.call(arrayLike, print);
```
上面代码中，`arrayLike`代表一个类似数组的对象，本来是不可以使用数组的`forEach()`方法的，但是通过`call()`，可以把`forEach()`嫁接到`arrayLike`上面调用。

下面的例子就是通过这种方法，在`arguments`对象上面调用`forEach`方法。

```
// forEach 方法
function logArgs() {
  Array.prototype.forEach.call(arguments, function (elem, i) {
    console.log(i + '. ' + elem);
  });
}

// 等同于 for 循环
function logArgs() {
  for (var i = 0; i < arguments.length; i++) {
    console.log(i + '. ' + arguments[i]);
  }
}
```

字符串也是类似数组的对象，所以也可以用`Array.prototype.forEach.call`遍历。

```
Array.prototype.forEach.call('abc', function (chr) {
  console.log(chr);
});
// a
// b
// c
```
注意，**这种方法比直接使用数组原生的`forEach`要慢**，所以**最好还是先将“类似数组的对象”转为真正的数组，然后再直接调用数组的`forEach`方法。**

```
var arr = Array.prototype.slice.call('abc');
arr.forEach(function (chr) {
  console.log(chr);
});
// a
// b
// c
```

# 函数

函数是一段可以反复调用的代码块。函数还能接受输入的参数，不同的参数会返回不同的值。

## 概述

### 函数的声明

JavaScript 有三种声明函数的方法。

**（1）function 命令**

`function`命令声明的代码区块，就是一个函数。**`function`命令后面是函数名，函数名后面是一对圆括号，里面是传入函数的参数。函数体放在大括号里面**。比如。
```
function print(s) {
  console.log(s);
}
```

**（2）函数表达式**

除了用function命令声明函数，还**可以采用变量赋值的写法**。

```
var print = function(s) {
  console.log(s);
};
```
**这种写法将一个匿名函数赋值给变量**。这时，**这个匿名函数又称函数表达式**（Function Expression），因为赋值语句的等号右侧只能放表达式。

**采用函数表达式声明函数时，function命令后面不带有函数名**。如果加上函数名，该函数名只在函数体内部有效，在函数体外部无效。

```
var print = function x(){
  console.log(typeof x);
};

x
// ReferenceError: x is not defined

print()
// function
```
上面代码在函数表达式中，加入了函数名`x`。这个`x`只在函数体内部可用，指代函数表达式本身，其他地方都不可用。**这种写法的用处有两个，一是可以在函数体内部调用自身，二是方便除错（除错工具显示函数调用栈时，将显示函数名，而不再显示这里是一个匿名函数）**。因此，下面的形式声明函数也非常常见。
```
var f = function f() {};
```
需要注意的是，**函数的表达式需要在语句的结尾加上分号，表示语句结束**。**而函数的声明在结尾的大括号后面不用加分号**。总的来说，这两种声明函数的方式，差别很细微，可以近似认为是等价的。

**（3）Function 构造函数**

第三种声明函数的方式是`Function`构造函数。

```
var add = new Function(
  'x',
  'y',
  'return x + y'
);

// 等同于
function add(x, y) {
  return x + y;
}
```
上面代码中，Function构造函数接受三个参数，除了最后一个参数是`add`函数的“函数体”，其他参数都是`add`函数的参数。
你**可以传递任意数量的参数给Function构造函数，只有最后一个参数会被当做函数体，如果只有一个参数，该参数就是函数体**。

```
var foo = new Function(
  'return "hello world"'
);

// 等同于
function foo() {
  return 'hello world';
}
```
Function构造函数可以不使用`new`命令，返回结果完全一样。

总的来说，**这种声明函数的方式非常不直观，几乎无人使用**。

### 函数的重复声明

如果同一个函数被多次声明，后面的声明就会覆盖前面的声明。

```
function f() {
  console.log(1);
}
f() // 2

function f() {
  console.log(2);
}
f() // 2
```
上面代码中，后一次的函数声明覆盖了前面一次。而且，**<span style="color:red;">由于函数名的提升（参见下文），前一次声明在任何时候都是无效的，这一点要特别注意。</span>**

### 圆括号运算符，return 语句和递归

调用函数时，要使用圆括号运算符。圆括号之中，可以加入函数的参数。

```
function add(x, y) {
  return x + y;
}

add(1, 1) // 2
```
函数体内部的`return`语句，表示返回。JavaScript 引擎遇到`return`语句，就直接返回`return`后面的那个表达式的值，后面即使还有语句，也不会得到执行。也就是说，`return`语句所带的那个表达式，就是函数的返回值。`return`语句不是必需的，如果没有的话，该函数就不返回任何值，或者说返回`undefined`。

函数可以调用自身，这就是递归（recursion）。下面就是**通过递归，计算斐波那契数列**的代码。
```
function fib(num) {
  if (num === 0) return 0;
  if (num === 1) return 1;
  return fib(num - 2) + fib(num - 1);
}

fib(6) // 8
```

### 第一等公民

JavaScript 语言将函数看作一种值，与其它值（数值、字符串、布尔值等等）地位相同。凡是可以使用值的地方，就能使用函数。比如，可以把函数赋值给变量和对象的属性，也可以当作参数传入其他函数，或者作为函数的结果返回。函数只是一个可以执行的值，此外并无特殊之处。

由于函数与其他数据类型地位平等，所以在 JavaScript 语言中又称函数为第一等公民。
```
function add(x, y) {
  return x + y;
}

// 将函数赋值给一个变量
var operator = add;

// 将函数作为参数和返回值
function a(op){
  return op;
}
a(add)(1, 1)
```

### 函数名的提升

JavaScript 引擎将函数名视同变量名，所以**采用`function`命令声明函数时，整个函数会像变量声明一样，被提升到代码头部**。所以，下面的代码不会报错。

```
f();

function f() {}
```
表面上，上面代码好像在声明之前就调用了函数`f`。但是实际上，由于“变量提升”，函数`f`被提升到了代码头部，也就是在调用之前已经声明了。但是，**如果采用赋值语句定义函数，JavaScript 就会报错。**

这也就是说，只有函数声明形式才能被提升，赋值形式是无法提升的。

```
f();
var f = function (){};
// TypeError: undefined is not a function

// 等同于
var f;
f();
f = function () {};
```
上面代码第二行，调用`f`的时候，`f`只是被声明了，还没有被赋值，等于`undefined`，所以会报错。

因此，**如果同时采用`function`命令和赋值语句声明同一个函数，最后总是采用赋值语句的定义，因为function命令声明的函数被提升了，而后才执行到赋值语句的定义，此时函数已经被覆盖了**。

```
var f = function () {
  console.log('1');
}

function f() {
  console.log('2');
}

f() // 1
```

### 不能在条件语句中声明函数

根据 ES5 的规范，不得在非函数的代码块中声明函数，最常见的情况就是if和try语句。

```
if (foo) {
  function x() {}
}

try {
  function x() {}
} catch(e) {
  console.log(e);
}
```
上面代码分别在`if`代码块和`try`代码块中声明了两个函数，按照语言规范，这是不合法的。但是，实际情况是各家浏览器往往并不报错，能够运行。

但是由于存在函数名的提升，所以在条件语句中声明函数，可能是无效的，这是非常容易出错的地方。

```
if (false) {
  function f() {}
}

f() // 不报错
```
上面代码的原始意图是不声明函数`f`，但是由于`f`的提升，导致`if`语句无效，所以上面的代码不会报错。**要达到在条件语句中定义函数的目的，只有使用函数表达式**。如下。

```
if (false) {
  var f = function () {};
}

f() // undefined
```

## 函数的属性和方法

### name 属性

函数的`name`属性返回函数的名字。

```
function f1() {}
f1.name // "f1"
```

如果是通过变量赋值定义的函数，那么`name`属性返回变量名。

```
var f2 = function () {};
f2.name // "f2
```
但是，上面这种情况，**只有在变量的值是一个匿名函数时`name`属性才会返回变量名。如果变量的值是一个具名函数，那么name属性返回function关键字之后的那个函数名**。

```
var f3 = function myName() {};
f3.name // 'myName'
```
上面代码中，`f3.name`返回函数表达式的名字。注意，**真正的函数名还是`f3`，而`myName`这个名字只在函数体内部可用。**

`name`属性的一个用处，就是获取参数函数的名字。

```
var myFunc = function () {};

function test(f) {
  console.log(f.name);
}

test(myFunc) // myFunc
```
上面代码中，函数`test`内部通过`name`属性，就可以知道传入的参数是什么函数。

### length 属性

**函数的`length`属性返回函数预期传入的参数个数**，即**函数定义之中的参数个数**。

```
function f(a, b) {}
f.length // 2
```
上面代码定义了空函数`f`，它的**`length`属性就是定义时的参数个数。不管调用时输入了多少个参数，`length`属性始终不变**，等于`2`。

`length`属性提供了一种机制，判断定义时和调用时参数的差异，以便实现面向对象编程的”方法重载“（overload）。

### toString()

函数的`toString`方法返回一个字符串，内容是函数的源码。

```
function f() {
  a();
  b();
  c();
}

f.toString()
// function f() {
//  a();
//  b();
//  c();
// }
```

函数内部的注释也可以返回。

```
function f() {/*
  这是一个
  多行注释
*/}

f.toString()
// "function f(){/*
//   这是一个
//   多行注释
// */}"
```

利用这一点，可以变相实现多行字符串。
```
var multiline = function (fn) {
  var arr = fn.toString().split('\n');
  return arr.slice(1, arr.length - 1).join('\n');
};

function f() {/*
  这是一个
  多行注释
*/}

multiline(f);
// " 这是一个
//   多行注释"
```

## 函数作用域

### 定义

**作用域（scope）指的是变量存在的范围**。

在<font color="red">**ES5**</font>的规范中，<font color="red">**Javascript只有两种作用域：一种是全局作用域，变量在整个程序中一直存在，所有地方都可以读取；另一种是函数作用域，变量只在函数内部存在**</font>。<font color="red">**ES6 **</font>又新增了块级作用域，本教程不涉及。

**函数外部声明的变量就是全局变量**（global variable），它**可以在函数内部读取**。

```
var v = 1;

function f() {
  console.log(v);
}

f()
// 1
```
上面的代码表明，函数`f`内部可以读取全局变量`v`。

在**函数内部定义的变量，外部无法读取，称为“局部变量”**（local variable）。

```
function f(){
  var v = 1;
}

v // ReferenceError: v is not defined
```
上面代码中，变量`v`在函数内部定义，所以是一个局部变量，函数之外就无法读取。

**<font color="red">函数内部定义的变量，会在该作用域内覆盖同名全局变量</font>**。

```
var v = 1;

function f(){
  var v = 2;
  console.log(v);
}

f() // 2
v // 1
```
上面代码中，变量`v`同时在函数的外部和内部有定义。结果，在函数内部定义，局部变量`v`覆盖了全局变量`v`。

注意，**<font color="red">对于`var`命令来说，局部变量只能在函数内部声明，在其他区块中声明，一律都是全局变量</font>**。

```
if (true) {
  var x = 5;
}
console.log(x);  // 5
```

上面代码中，变量`x`在条件判断区块之中声明，结果就是一个全局变量，可以在区块之外读取。

### 函数内部的变量提升

与全局作用域一样，**函数作用域内部也会产生“变量提升”现象**。**`var`命令声明的变量，不管在什么位置，变量声明都会被提升到函数体的头部**。

```
function foo(x) {
  if (x > 100) {
    var tmp = x - 100;
  }
}

// 等同于
function foo(x) {
  var tmp;
  if (x > 100) {
    tmp = x - 100;
  };
}
```

### 函数本身的作用域

**函数**本身也是一个值，也有自己的作用域。它**的作用域**与变量一样，**就是其声明时所在的作用域，与其运行时所在的作用域无关**。

```
var a = 1;
var x = function () {
  console.log(a);
};

function f() {
  var a = 2;
  x();
}

f() // 1
```
上面代码中，函数`x`是在函数`f`的外部声明的，所以它的作用域绑定外层，内部变量`a`不会到函数`f`体内取值，所以输出`1`，而不是`2`。

总之，**函数执行时所在的作用域，是定义时的作用域，而不是调用时所在的作用域**。

很容易犯错的一点是，**如果函数`A`调用函数`B`，却没考虑到函数`B`不会引用函数`A`的内部变量**。

```
var x = function () {
  console.log(a);
};

function y(f) {
  var a = 2;
  f();
}

y(x)
// ReferenceError: a is not defined
```
上面代码将函数`x`作为参数，传入函数`y`。但是，函数`x`是在函数`y`体外声明的，作用域绑定外层，因此找不到函数`y`的内部变量`a`，导致报错。

同样的，**函数体内部声明的函数，作用域绑定函数体内部**。

```
function foo() {
  var x = 1;
  function bar() {
    console.log(x);
  }
  return bar;
}

var x = 2;
var f = foo();
f() // 1
```
上面代码中，函数`foo`内部声明了一个函数`bar`，`bar`的作用域绑定`foo`。当我们在`foo`外部取出`bar`执行时，变量`x`指向的是`foo`内部的`x`，而不是`foo`外部的`x`。正是这种机制，构成了下文要讲解的“闭包”现象。

## 参数

### 概述

函数运行的时候，有时需要提供外部数据，不同的外部数据会得到不同的结果，这种外部数据就叫参数。

```
function square(x) {
  return x * x;
}

square(2) // 4
square(3) // 9
```
上式的`x`就是`square`函数的参数。每次运行的时候，需要提供这个值，否则得不到结果。

### 参数的省略

函数参数不是必需的，Javascript 允许省略参数。

```
function f(a, b) {
  return a;
}

f(1, 2, 3) // 1
f(1) // 1
f() // undefined

f.length // 2
```
上面代码的函数`f`定义了两个参数，但是运行时无论提供多少个参数（或者不提供参数），JavaScript 都不会报错。省略的参数的值就变为`undefined`。需要注意的是，**<font color="red">函数的`length`属性与实际传入的参数个数无关，只反映函数预期传入的参数个数</font>**。

但是，没有办法只省略靠前的参数，而保留靠后的参数。如果一定要省略靠前的参数，只有显式传入`undefined`。

```
function f(a, b) {
  return a;
}

f( , 1) // SyntaxError: Unexpected token ,(…)
f(undefined, 1) // undefined
```
上面代码中，如果省略第一个参数，就会报错。

### 传递方式

**函数参数如果是原始类型的值（数值、字符串、布尔值），传递方式是传值传递（passes by value）。这意味着，在函数体内修改参数值，不会影响到函数外部**。

```
var p = 2;

function f(p) {
  p = 3;
}
f(p);

p // 2
```
上面代码中，变量`p`是一个原始类型的值，传入函数`f`的方式是传值传递。因此，在函数内部，`p`的值是原始值的拷贝，无论怎么修改，都不会影响到原始值。

但是，**如果函数参数是复合类型的值（数组、对象、其他函数），传递方式是传址传递（pass by reference）。也就是说，传入函数的原始值的地址，因此在函数内部修改参数，将会影响到原始值**。

```
var obj = { p: 1 };

function f(o) {
  o.p = 2;
}
f(obj);

obj.p // 2
```
上面代码中，传入函数`f`的是参数对象`obj`的地址。因此，在函数内部修改`obj`的属性`p`，会影响到原始值。

注意，如果函数内部修改的，不是参数对象的某个属性，而是替换掉整个参数，这时不会影响到原始值。

```
var obj = [1, 2, 3];

function f(o) {
  o = [2, 3, 4];
}
f(obj);

obj // [1, 2, 3]
```
上面代码中，在函数`f`内部，参数对象`obj`被整个替换成另一个值。这时不会影响到原始值。这是因为，**形式参数（`o`）的值实际是参数`obj`的地址，重新对`o`赋值导致`o`指向另一个地址，保存在原地址上的值当然不受影响**。

### 同名参数

**如果有同名的参数，则取最后出现的那个值**。

```
function f(a, a) {
  console.log(a);
}

f(1, 2) // 2
f(1) // undefined
```
上面代码中，函数`f`有两个参数，且参数名都是`a`。取值的时候，以后面的`a`为准，**<font color="red">即使后面的`a`没有值或被省略，也是以其为准</font>**。

调用函数`f`的时候，如果没有提供第二个参数，`a`的取值就变成了`undefined`。这时，如果要获得第一个`a`的值，可以使用`arguments`对象。
```
function f(a, a) {
  console.log(arguments[0]);
}

f(1) // 1
```

### arguments 对象

#### 定义

由于JavaScript允许函数有不定数目的参数，所以**需要一种机制，可以在函数体内部读取所有参数**。这就是`arguments`对象的由来。

**`arguments`对象包含了<font color="red">函数运行时</font>的所有参数**，`arguments[0]`就是第一个参数，`arguments[1]`就是第二个参数，以此类推。**这个对象只有在函数体内部，才可以使用**。

```
var f = function (one) {
  console.log(arguments[0]);
  console.log(arguments[1]);
  console.log(arguments[2]);
}

f(1, 2, 3)
// 1
// 2
// 3
```

**正常模式下**，**`arguments`对象可以在运行时修改**。

```
var f = function(a, b) {
  arguments[0] = 3;
  arguments[1] = 2;
  return a + b;
}

f(1, 1) // 5
```
上面代码中，函数`f`调用时传入的参数，在函数内部被修改成3和2。

**严格模式下**，**`arguments`对象是一个只读对象，修改它是无效的，但不会报错**。<font color="red">使用`'use strict';`开启严格模式</font>。

```
var f = function(a, b) {
  'use strict'; // 开启严格模式
  arguments[0] = 3; // 无效
  arguments[1] = 2; // 无效
  return a + b;
}

f(1, 1) // 2
```
上面代码中，函数体内是严格模式，这时修改`arguments`对象就是无效的。

**通过`arguments`对象的`length`属性，可以判断函数调用时到底带几个参数。**

```
function f() {
  return arguments.length;
}

f(1, 2, 3) // 3
f(1) // 1
f() // 0
```

#### 与数组的关系

需要注意的是，虽然`arguments`很像数组，但它**是一个对象**。**数组专有的方法（比如`slice`和`forEach`），不能在`arguments`对象上直接使用**。

如果要让`arguments`对象使用数组方法，真正的解决方法是将`arguments`转为真正的数组。**<font color="red">下面是两种常用的转换方法：`slice`方法和逐一填入新数组</font>**。

```
var args = Array.prototype.slice.call(arguments);

// 或者
var args = [];
for (var i = 0; i < arguments.length; i++) {
  args.push(arguments[i]);
}
```

#### callee 属性

**`arguments`对象带有一个`callee`属性，返回它所对应的原函数**。

```
var f = function () {
  console.log(arguments.callee === f);
}

f() // true
```
可以通过`arguments.callee`，达到调用函数自身的目的。这个属性在严格模式里面是禁用的，因此**不建议使用**。

## 函数的其他知识点

### <font color="red">闭包</font>

闭包（closure）是 Javascript 语言的一个难点，也是它的特色，很多高级应用都要依靠闭包实现。

理解闭包，首先必须理解变量作用域。前面提到，**JavaScript 有两种作用域：全局作用域和函数作用域。函数内部可以直接读取全局变量**。

```
var n = 999;

function f1() {
  console.log(n);
}
f1() // 999
```
上面代码中，函数`f1`可以读取全局变量`n`。

但是，函数外部无法读取函数内部声明的变量。

```
function f1() {
  var n = 999;
}

console.log(n)
// Uncaught ReferenceError: n is not defined
```
上面代码中，函数`f1`内部声明的变量`n`，函数外是无法读取的。

如果出于种种原因，需要得到函数内的局部变量。正常情况下，这是办不到的，只有通过变通方法才能实现。那就是在函数的内部，再定义一个函数。

```
function f1() {
  var n = 999;
  function f2() {
　　console.log(n); // 999
  }
  return f2;
}

var result = f1();
result(); // 999
```
上面代码中，函数`f2`就在函数`f1`内部，这时`f1`内部的所有局部变量，对`f2`都是可见的。但是反过来就不行，`f2`内部的局部变量，对`f1`就是不可见的。这就是 **<font color="red">JavaScript 语言特有的”链式作用域”结构（chain scope），子对象会一级一级地向上寻找所有父对象的变量</font>**。所以，父对象的所有变量，对子对象都是可见的，反之则不成立。

上面代码中，函数`f1`的返回值就是函数`f2`，由于`f2`可以读取`f1`的内部变量，所以就可以在外部获得`f1`的内部变量了。

**<font color="red">闭包就是函数`f2`，即能够读取其他函数内部变量的函数</font>**。由于在 JavaScript 语言中，只有函数内部的子函数才能读取内部变量，因此**可以把闭包简单理解成“定义在一个函数内部的函数”**。闭包最大的特点，就是它可以“记住”诞生的环境，比如`f2`记住了它诞生的环境`f1`，所以从`f2`可以得到`f1`的内部变量。***在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁**。

**闭包的最大用处有两个，一个是<font color="red">可以读取函数内部的变量</font>，另一个就是<font color="red">让这些变量始终保持在内存中，即闭包可以使得它诞生环境一直存在</font>。**

请看下面的例子，**闭包使得内部变量记住上一次调用时的运算结果**。

```
function createIncrementor(start) {
  return function () {
    return start++;
  };
}

var inc = createIncrementor(5);

inc() // 5
inc() // 6
inc() // 7
```
上面代码中，`start`是函数`createIncrementor`的内部变量。通过闭包，`start`的状态被保留了，每一次调用都是在上一次调用的基础上进行计算。从中可以看到，闭包`inc`使得函数`createIncrementor`的内部环境，一直存在。所以，闭包可以看作是函数内部作用域的一个接口。

为什么会这样呢？原因就在于`inc`始终在内存中，而`inc`的存在依赖于`createIncrementor`，因此也始终在内存中，不会在调用结束后，被垃圾回收机制回收。

这也就是说，`start`作为`createIncreamentor`的内部变量，一直没有被回收，所以这个`start`一直占用内存，可以想象，从第一次声明`inc`时传入5，`start`的值为5，因为`inc`的作用就是返回`start`的值，然后再将`start`自增1，所以第一次调用时会返回5，而第二次调用时，`start`在上次调用时已经自增了1，并且没有被回收，所以第二次调用的结果为6，第二次返回6之后`start`的值又变为7了。依次类推。

闭包的另一个用处，是**<font color="red">封装对象的私有属性和私有方法</font>**。

```
function Person(name) {
  var _age;
  function setAge(n) {
    _age = n;
  }
  function getAge() {
    return _age;
  }

  return {
    name: name,
    getAge: getAge, // 注意这里返回的getAge是getAge函数
    setAge: setAge // 注意这里返回的setAge是setAge函数
  };
}

var p1 = Person('张三');
p1.setAge(25);
p1.getAge() // 25
```
上面代码中，函数`Person`的内部变量`_age`，通过闭包`getAge`和`setAge`，变成了返回对象`p1`的私有变量。

注意，**外层函数每次运行，都会生成一个新的闭包，而这个闭包又会保留外层函数的内部变量，所以内存消耗很大**。因此不能滥用闭包，否则会造成网页的性能问题。

### 立即调用的函数表达式（IIFE）

在 Javascript 中，**<font color="red">圆括号`()`是一种运算符，跟在函数名之后，表示调用该函数</font>**。比如，print`()`就表示调用print函数。

有时，我们需要在定义函数之后，立即调用该函数。这时，你不能在函数的定义之后加上圆括号，这会产生语法错误。

```
function(){ /* code */ }();
// SyntaxError: Unexpected token (
```

产生这个错误的原因是，`function`这个关键字即可以当作语句，也可以当作表达式。
```
// 语句
function f() {}

// 表达式
var f = function f() {}
```

为了避免解析上的歧义，JavaScript 引擎规定，如果`function`关键字出现在行首，一律解释成语句。因此，JavaScript引擎看到行首是`function`关键字之后，认为这一段都是函数的定义，不应该以圆括号结尾，所以就报错了。

解决方法就是不要让`function`出现在行首，让引擎将其理解成一个表达式。最简单的处理，就是将其放在一个圆括号里面。

```
(function(){ /* code */ }());
// 或者
(function(){ /* code */ })();
```
上面两种写法都是**以圆括号开头，引擎就会认为后面跟的是一个表示式，而不是函数定义语句，所以就避免了错误**。这就叫做“立即调用的函数表达式”（Immediately-Invoked Function Expression），简称 IIFE。

注意，上面两种写法最后的分号都是必须的。如果省略分号，遇到连着两个 IIFE，可能就会报错。
```
// 报错
(function(){ /* code */ }())
(function(){ /* code */ }())
```
上面代码的两行之间没有分号，JavaScript 会将它们连在一起解释，将第二行解释为第一行的参数。

推而广之，任何让解释器以表达式来处理函数定义的方法，都能产生同样的效果，比如下面三种写法。
```
var i = function(){ return 10; }();
true && function(){ /* code */ }();
0, function(){ /* code */ }();
```

甚至像下面这样写，也是可以的。
```
!function () { /* code */ }();
~function () { /* code */ }();
-function () { /* code */ }();
+function () { /* code */ }();
```

**通常情况下，只对匿名函数使用这种“立即执行的函数表达式”。它的目的有两个：一是<font color="red">不必为函数命名，避免了污染全局变量</font>；二是<font color="red">IIFE内部形成了一个单独的作用域，可以封装一些外部无法读取的私有变量</font>**。

```
// 写法一
var tmp = newData;
processData(tmp);
storeData(tmp);

// 写法二
(function () {
  var tmp = newData;
  processData(tmp);
  storeData(tmp);
}());
```
上面代码中，写法二比写法一更好，因为完全避免了污染全局变量。

## eval 命令

`eval`命令的作用是，将字符串当作语句执行。

```
eval('var a = 1;');
a // 1
```
上面代码将字符串当作语句运行，生成了变量`a`。

放在`eval`中的字符串，应该有独自存在的意义，不能用来与`eval`以外的命令配合使用。举例来说，下面的代码将会报错。

```
eval('return;');
```

**`eval`没有自己的作用域，都在当前作用域内执行，因此可能会修改当前作用域的变量的值**，造成安全问题。

```
var a = 1;
eval('a = 2');

a // 2
```
上面代码中，`eval`命令修改了外部变量`a`的值。**由于这个原因，`eval`有安全风险**。

为了防止这种风险，JavaScript 规定，**如果使用严格模式，`eval`内部声明的变量，不会影响到外部作用域**。

```
(function f() {
  'use strict';
  eval('var foo = 123');
  console.log(foo);  // ReferenceError: foo is not defined
})()
```
上面代码中，函数`f`内部是严格模式，这时`eval`内部<font color="red">声明</font>的`foo`变量，就不会影响到外部。

不过，**即使在严格模式下，`eval`依然可以读写当前作用域的变量**。

```
(function f() {
  'use strict';
  var foo = 1;
  eval('foo = 2');
  console.log(foo);  // 2
})()
```

上面代码中，**严格模式下，`eval`内部还是改写了外部变量，可见安全风险依然存在**。

此外，`eval`的命令字符串不会得到 JavaScript 引擎的优化，**运行速度较慢。这也是一个不应该使用它的理由**。

通常情况下，`eval`最常见的场合是**解析 JSON 数据字符串**，不过**正确的做法应该是使用浏览器提供的`JSON.parse`方法**。

JavaScript 引擎内部，`eval`实际上是一个引用，默认调用一个内部方法。这使得`eval`的使用分成两种情况，**一种是像上面这样的调用`eval(expression)`，这叫做“直接使用”，这种情况下`eval`的作用域就是当前作用域。除此之外的调用方法，都叫“间接调用”，此时`eval`的作用域总是全局作用域**。

```
var a = 1;

function f() {
  var a = 2;
  var e = eval;
  e('console.log(a)');
}

f() // 1
```
上面代码中，`eval`是间接调用，所以即使它是在函数中，它的作用域还是全局作用域，因此输出的`a`为全局变量。

`eval`的间接调用的形式五花八门，**只要不是直接调用，即只要不是`eval(expression)`的形式，都属于间接调用**。

```
eval.call(null, '...')
window.eval('...')
(1, eval)('...')
(eval, eval)('...')
```
上面这些形式都是`eval`的间接调用，因此它们的作用域都是全局作用域。

与`eval`作用类似的还有`Function`构造函数。利用它生成一个函数，然后调用该函数，也能将字符串当作命令执行。

```
var jsonp = 'foo({"id": 42})';

var f = new Function( 'foo', jsonp );
// 相当于定义了如下函数
// function f(foo) {
//   foo({"id":42});
// }

f(function (json) {
  console.log( json.id ); // 42
})
```
上面代码中，`jsonp`是一个字符串，`Function`构造函数将这个字符串，变成了函数体。调用该函数的时候，`jsonp`就会执行。这种写法的实质是将代码放到函数作用域执行，避免对全局作用域造成影响。

对于本例来说就是将`f`函数接收的参数，也就是作为参数的函数代码放在函数`f`的作用域里执行。函数`f`的作用就是接收一个函数，向这个函数传参，参数为`{"id":42}`，并且调用。本例中对`f`传入了一个函数，传入的函数的作用是接收一个`json`值，并且打印这个`json`值的`id`属性的值。总的来说，就是在函数`f`的内部调用了打印`json`数据的函数。

不过，`new Function()`的写法也可以读写全局作用域，所以也是应该避免使用它。

# 运算符

## 加法运算符

### 基本规则

加法运算符（`+`）是最常见的运算符，**用来求两个数值的和**。

需要注意的是，**JavaScript 允许非数值的相加**。
```
true + true // 2
1 + true // 2
```
上面代码中，第一行是两个布尔值相加，第二行是数值与布尔值相加。这两种情况，**布尔值都会自动转成数值，然后再相加**。

比较特殊的是，**如果是两个字符串相加，这时加法运算符会变成连接运算符，返回一个新的字符串，将两个原字符串连接在一起**。

```
'a' + 'bc' // "abc"
```

**如果一个运算子是字符串，另一个运算子是非字符串，这时<font color="red">非字符串会转成字符串</font>，再连接在一起**。

```
1 + 'a' // "1a"
false + 'a' // "falsea"
```

**加法运算符是在运行时决定，到底是执行相加，还是执行连接**。也就是说，**<font color="red">运算子的不同，导致了不同的语法行为，这种现象称为“重载”（overload）</font>**。由于加法运算符存在重载，可能执行两种运算，使用的时候必须很小心。

```
'3' + 4 + 5 // "345"，先执行'3'+4的连接操作，再执行了‘34’+5的连接操作
3 + 4 + '5' // "75"，先执行了3+4等于7，再执行了7+‘5’的连接操作
```
上面代码中，**由于从左到右的运算次序，字符串的位置不同会导致不同的结果**。

**除了加法运算符，其他算术运算符（比如减法、除法和乘法）都不会发生重载**。**它们的规则是：<font color="red">所有运算子一律转为数值</font>，再进行相应的数学运算**。

```
1 - '2' // -1
1 * '2' // 2
1 / '2' // 0.5
```
上面代码中，减法、除法和乘法运算符，都是将字符串自动转为数值，然后再运算。

### 对象的相加

**如果运算子是对象，必须先转成原始类型的值，然后再相加**。

```
var obj = { p: 1 };
obj + 2 // "[object Object]2"
```

上面代码中，对象`obj`转成原始类型的值是`[object Object]`，再加`2`就得到了上面的结果。

对象转成原始类型的值，规则如下。

首先，自动调用对象的`valueOf`方法。

```
var obj = { p: 1 };
obj.valueOf() // { p: 1 }
```

**一般来说，对象的`valueOf`方法总是返回对象自身**，这时再自动调用对象的`toString`方法，将其转为字符串。

```
var obj = { p: 1 };
obj.valueOf().toString() // "[object Object]"
```

**对象的`toString`方法默认返回`[object Object]`**，所以就得到了最前面那个例子的结果。

知道了这个规则以后，就可以自己定义`valueOf`方法或`toString`方法，得到想要的结果。

```
var obj = {
  valueOf: function () {
    return 1;
  }
};

obj + 2 // 3
```
上面代码中，我们定义`obj`对象的`valueOf`方法返回`1`，于是`obj + 2`就得到了`3`。这个例子中，**由于`valueOf`方法直接返回一个原始类型的值，所以不再调用`toString`方法**。

下面是自定义`toString`方法的例子。

```
var obj = {
  toString: function () {
    return 'hello';
  }
};

obj + 2 // "hello2"
```
上面代码中，对象`obj`的`toString`方法返回字符串`hello`。前面说过，**只要有一个运算子是字符串，加法运算符就变成连接运算符，返回连接后的字符串**。

注意，toString方法不一定非要返回字符串类型的值，也可以返回一个数值
```
var obj = {
  toString: function () {
    return 1;
  }
};

obj+2 // 3
```

同理，valueOf方法也不一定非要返回数值类型的值，也可以返回字符串或其他类型的值。
```
var obj = {
  valueOf: function () {
    return 'hello';
  }
};

obj + 2 // "hello2"
```

我们回到正题，这里有一个特例，**<font color="red">如果运算子是一个`Date`对象的实例，那么会优先执行`toString`方法</font>**。

```
var obj = new Date();
obj.valueOf = function () { return 1 };
obj.toString = function () { return 'hello' };

obj + 2 // "hello2"
```
上面代码中，对象`obj`是一个`Date`对象的实例，并且自定义了`valueOf`方法和`toString`方法，结果`toString`方法优先执行。

## 算术运算符

包括加法运算符在内，JavaScript 共提供10个算术运算符，用来完成基本的算术运算。

> 加法运算符：`x + y`
> 减法运算符： `x - y`
> 乘法运算符：`x * y`
> 除法运算符：`x / y`
> 指数运算符：`x ** y`
> 余数运算符：`x % y`
> 自增运算符：`++x` 或者 `x++`
> 自减运算符：`--x` 或者 `x--`
> 数值运算符： `+x`
> 负数值运算符：`-x`

减法、乘法、除法运算法比较单纯，就是执行相应的数学运算。下面介绍其他几个算术运算符。

### 余数运算符

余数运算符（`%`）**返回前一个运算子被后一个运算子除，所得的余数**。

```
12 % 5 // 2，即12除以5得到的余数
```

需要注意的是，**<font color="red">余数运算的运算结果的正负号由第一个运算子的正负号决定</font>**。

```
-1 % 2 // -1
1 % -2 // 1
```

余数运算符还可以用于浮点数的运算。但是，由于浮点数不是精确的值，无法得到完全准确的结果。

```
6.5 % 2.1
// 0.19999999999999973
```

### 自增和自减运算符

自增和自减运算符，是一元运算符，只需要一个运算子。它们的作用是**将运算子首先转为数值，然后加上1或者减去1。它们会修改原始变量**。

```
var x = 1;
++x // 2
x // 2

--x // 1
x // 1
```
上面代码的变量`x`自增后，返回`2`，再进行自减，返回`1`。这两种情况都会使得，原始变量`x`的值发生改变。

**自增和自减运算符有一个需要注意的地方，就是放在变量之后，会先返回变量操作前的值，再进行自增/自减操作；放在变量之前，会先进行自增/自减操作，再返回变量操作后的值**。

```
var x = 1;
var y = 1;

x++ // 1
++y // 2
```
上面代码中，`x`是先返回当前值，然后自增，所以得到`1`；`y`是先自增，然后返回新的值，所以得到`2`。

### 数值运算符，负数值运算符

数值运算符（`+`）同样使用加号，但它是一元运算符（只需要一个操作数），而加法运算符是二元运算符（需要两个操作数）。

**数值运算符的作用在于可以将任何值转为数值（与`Number`函数的作用相同）**。

```
+true // 1
+[] // 0
+{} // NaN
```
上面代码表示，非数值经过数值运算符以后，都变成了数值（最后一行NaN也是数值）。具体的类型转换规则，参见《数据类型转换》一章。

**负数值运算符（`-`），也同样具有将一个值转为数值的功能，只不过得到的值正负相反。连用两个负数值运算符，等同于数值运算符，但是连用的时候记得加括号，否则会变成自减运算符**。

```
var x = 1;
-x // -1
-(-x) // 1
```
上面代码最后一行的圆括号不可少，否则会变成自减运算符。

**数值运算符号和负数值运算符，都会返回一个新的值，而不会改变原始变量的值**。

### 指数运算符

**指数运算符（`**`）完成指数运算，前一个运算子是底数，后一个运算子是指数**。

```
1 ** 3 // 1，即1的3次方
```

## 赋值运算符

赋值运算符（Assignment Operators）**用于给变量赋值**。

最常见的赋值运算符，当然就是等号（`=`）。

```
// 将 1 赋值给变量 x
var x = 1;

// 将变量 y 的值赋值给变量 x
var x = y;
```

**赋值运算符还可以与其他运算符结合，形成变体**。下面是与算术运算符的结合。

```
// 等同于 x = x + y
x += y

// 等同于 x = x - y
x -= y

// 等同于 x = x * y
x *= y

// 等同于 x = x / y
x /= y

// 等同于 x = x % y
x %= y

// 等同于 x = x ** y
x **= y
```

下面是与位运算符的结合（关于位运算符，请见后文的介绍）。
```
// 等同于 x = x >> y
x >>= y

// 等同于 x = x << y
x <<= y

// 等同于 x = x >>> y
x >>>= y

// 等同于 x = x & y
x &= y

// 等同于 x = x | y
x |= y

// 等同于 x = x ^ y
x ^= y
```

这些**复合的赋值运算符，都是先进行指定运算，然后将得到值返回给左边的变量**。

## 比较运算符

比较运算符**用于比较两个值的大小，然后返回一个布尔值，表示是否满足指定的条件**。

```
2 > 1 // true
```

注意，**比较运算符可以比较各种类型的值，不仅仅是数值**。

JavaScript 一共提供了8个比较运算符。

> `<` 小于运算符
> `>` 大于运算符
> `<=` 小于或等于运算符
> `>=` 大于或等于运算符
> `==` 相等运算符
> `===` 严格相等运算符
> `!=` 不相等运算符
> `!==` 严格不相等运算符

这八个比较运算符分成两类：相等比较和非相等比较。两者的规则是不一样的，**对于非相等的比较，算法是先看两个运算子是否都是字符串，如果是的，就按照字典顺序比较（实际上是比较 Unicode 码点）；否则，将两个运算子都转成数值，再比较数值的大小**。

### 字符串的比较

字符串按照字典顺序进行比较。

```
'cat' > 'dog' // false
'cat' > 'catalog' // false
'cat' > 'apple' // true
```

JavaScript 引擎内部首先比较首字符的 Unicode 码点。如果相等，再比较第二个字符的 Unicode 码点，以此类推。

```
'cat' > 'Cat' // true'
```
上面代码中，小写的`c`的 Unicode 码点（99）大于大写的C的 `Unicode` 码点（67），所以返回`true`。

总体上来讲，小写字母都在大写字母的后边，都比大写字母大。

由于所有字符都有 Unicode 码点，因此汉字也可以比较。

```
'大' > '小' // false
```
上面代码中，“大”的 Unicode 码点是`22823`，“小”是`23567`，因此返回false。

### 非字符串的比较

#### 原始类型的值

**两个原始类型的值的比较，除了相等运算符（`==`）和严格相等运算符（`===`），其他比较运算符都是先转成数值再比较。**

```
5 > '4' // true
// 等同于 5 > Number('4')
// 即 5 > 4

true > false // true
// 等同于 Number(true) > Number(false)
// 即 1 > 0

2 > true // true
// 等同于 2 > Number(true)
// 即 2 > 1
```
上面代码中，字符串和布尔值都会先转成数值，再进行比较。

这里有一个特殊情况，即任何值（包括`NaN`本身）与`NaN`比较，返回的都是`false`。

```
1 > NaN // false
1 <= NaN // false
'1' > NaN // false
'1' <= NaN // false
NaN > NaN // false
NaN <= NaN // false
```

#### 对象

**如果运算子是对象，会转为原始类型的值，再进行比较**。

对象转换成原始类型的值，算法是先调用`valueOf`方法；如果返回的还是对象，再接着调用`toString`方法，详细解释参见《数据类型的转换》一章。

```
var x = [2];
x > '11' // true
// 等同于 [2].valueOf().toString() > '11'
// 即 '2' > '11'

x.valueOf = function () { return '1' };
x > '11' // false
// 等同于 [2].valueOf() > '11'
// 即 '1' > '11'
```

两个对象之间的比较也是如此。
```
[2] > [1] // true
// 等同于 [2].valueOf().toString() > [1].valueOf().toString()
// 即 '2' > '1'

[2] > [11] // true
// 等同于 [2].valueOf().toString() > [11].valueOf().toString()
// 即 '2' > '11'

{x: 2} >= {x: 1} // true
// 等同于 {x: 2}.valueOf().toString() >= {x: 1}.valueOf().toString()
// 即 '[object Object]' >= '[object Object]'
```

注意，`Date` 对象实例用于比较时，是先调用`toString`方法。如果返回的不是原始类型的值，再接着对返回值调用`valueOf`方法。

### 严格相等运算符

JavaScript 提供两种相等运算符：`==`和`===`。

简单说，它们的区别是**相等运算符（`==`）比较两个值是否相等**，**严格相等运算符（`===`）比较它们是否为“同一个值”**。**如果两个值不是同一类型，严格相等运算符（`===`）直接返回`false`，而相等运算符（`==`）会将它们转换成同一个类型，再用严格相等运算符进行比较**。

```
2 == '2' // true
2 === '2' // false
```

严格相等运算符的算法如下。

#### 不同类型的值

如果两个值的类型不同，直接返回`false`。

```
1 === "1" // false
true === "true" // false
```
上面代码比较数值的`1`与字符串的`“1”`、布尔值的`true`与字符串`"true"`，因为类型不同，结果都是`false`。

#### 同一类的原始类型值

同一类型的原始类型的值（数值、字符串、布尔值）比较时，值相同就返回`true`，值不同就返回`false`。

```
1 === 0x1 // true
```
上面代码比较十进制的`1`与十六进制的`1`，因为类型和值都相同，返回`true`。

需要注意的是，**`NaN`与任何值都不相等（包括自身）**。另外，**正0等于负0**。

```
NaN === NaN  // false
+0 === -0 // true
```

#### 复合类型值

**两个复合类型（对象、数组、函数）的数据比较时，不是比较它们的值是否相等，而是比较它们是否指向同一个地址**。

```
{} === {} // false
[] === [] // false
(function () {} === function () {}) // false
```
上面代码分别比较两个空对象、两个空数组、两个空函数，结果都是不相等。原因是对于复合类型的值，严格相等运算比较的是，它们是否引用同一个内存地址，而运算符两边的空对象、空数组、空函数的值，都存放在不同的内存地址，结果当然是`false`。

如果两个变量引用同一个对象，则它们相等。
```
var v1 = {};
var v2 = v1;
v1 === v2 // true
```

注意，**对于两个对象的比较，严格相等运算符比较的是地址，而大于或小于运算符比较的是值**。

```
new Date() > new Date() // false
new Date() < new Date() // false
new Date() === new Date() // false
```
上面的三个表达式，前两个比较的是值，最后一个比较的是地址，所以都返回`false`。

#### undefined 和 null

**`undefined`和`null`与自身严格相等。`NaN`与任何值都不相等（包括自身）**

```
undefined === undefined // true
null === null // true
```

**<font color="red">由于变量声明后默认值是`undefined`，因此两个只声明未赋值的变量是相等的。</font>**

```
var v1;
var v2;
v1 === v2 // true
```

#### 严格不相等运算符

**严格相等运算符有一个对应的“严格不相等运算符”（!==），它的算法就是先求严格相等运算符的结果，然后返回相反值。**

```
1 !== '1' // true
```

### 相等运算符

**相等运算符用来比较相同类型的数据时，与严格相等运算符完全一样**。

**比较不同类型的数据时，相等运算符会先将数据进行类型转换，然后再用严格相等运算符比较**。类型转换规则如下。

#### 原始类型的值

**原始类型的数据会转换成数值类型再进行比较**。

```
1 == true // true
// 等同于 1 === Number(true)

0 == false // true
// 等同于 0 === Number(false)

2 == true // false
// 等同于 2 === Number(true)

2 == false // false
// 等同于 2 === Number(false)

'true' == true // false
// 等同于 Number('true') === Number(true)
// 等同于 NaN === 1

'' == 0 // true
// 等同于 Number('') === 0
// 等同于 0 === 0

'' == false  // true
// 等同于 Number('') === Number(false)
// 等同于 0 === 0

'1' == true  // true
// 等同于 Number('1') === Number(true)
// 等同于 1 === 1

'\n  123  \t' == 123 // true
// 因为字符串转为数字时，省略前置和后置的空格
```
上面代码将字符串和布尔值都转为数值，然后再进行比较。具体的字符串与布尔值的类型转换规则，参见《数据类型转换》一章。

#### 对象与原始类型值比较

对象（这里指广义的对象，包括数组和函数）与原始类型的值比较时，对象转化成原始类型的值，再进行比较。

```
[1] == 1 // true
// 等同于 Number([1]) == 1

[1] == '1' // true
// 等同于 Number([1]) == Number('1')

[1] == true // true
// 等同于 Number([1]) == Number(true)
```
上面代码中，数组`[1]`与数值进行比较，会先转成数值，再进行比较；与字符串进行比较，会先转成数值，然后再与字符串进行比较，这时字符串也会转成数值；与布尔值进行比较，两个运算子都会先转成数值，然后再进行比较。

#### undefined 和 null

`undefined`和`null`与其他类型的值比较时，结果都为`false`，它们互相比较时结果为`true`。

```
false == null // false
false == undefined // false

0 == null // false
0 == undefined // false

undefined == null // true
```
绝大多数情况下，对象与`undefined`和`null`比较，都返回`false`。只有在对象转为原始值得到`undefined`时，才会返回`true`，这种情况是非常罕见的。

#### 相等运算符的缺点

**相等运算符隐藏的类型转换，会带来一些违反直觉的结果**。

```
0 == ''             // true
0 == '0'            // true

2 == true           // false
2 == false          // false

false == 'false'    // false
false == '0'        // true

false == undefined  // false
false == null       // false
null == undefined   // true

' \t\r\n ' == 0     // true
```
上面这些表达式都很容易出错，**因此不要使用相等运算符（==），最好只使用严格相等运算符（===）**。

#### 不相等运算符

相等运算符有一个对应的“不相等运算符”（`!=`），两者的运算结果正好相反。

```
1 != '1' // false
```

## 布尔运算符

**布尔运算符用于将表达式转为布尔值**，一共包含四个运算符。

> 取反运算符：`!`
> 且运算符：`&&`
> 或运算符：`||`
> 三元运算符：`?:`

### 取反运算符（!）

**取反运算符是一个感叹号，用于将布尔值变为相反值**，即`true`变成`false`，`false`变成`true`。

```
!true // false
!false // true
```

**对于非布尔值，取反运算符会将其转为布尔值**。可以这样记忆，**以下六个值取反后为`true`，其他值都为`false`。**

> undefined
> null
> false
> 0
> NaN
> 空字符串（''）

```
!undefined // true
!null // true
!0 // true
!NaN // true
!"" // true

!54 // false
!'hello' // false
![] // false
!{} // false
```
上面代码中，不管什么类型的值，经过取反运算后，都变成了布尔值。

**如果对一个值连续做两次取反运算，等于将其转为对应的布尔值，与`Boolean`函数的作用相同。这是一种常用的类型转换的写法**。

```
!!x
// 等同于
Boolean(x)
```
上面代码中，不管`x`是什么类型的值，经过两次取反运算后，变成了与`Boolean`函数结果相同的布尔值。所以，**<font color="red">两次取反就是将一个值转为布尔值的简便写法</font>**。

### 且运算符（&&）

且运算符（`&&`）往往用于多个表达式的求值。

它的运算规则是：**如果第一个运算子的布尔值为`true`，则返回第二个运算子的<font color="red">值（注意是值，不是布尔值）</font>；如果第一个运算子的布尔值为`false`，则直接返回第一个运算子的<font color="red">值</font>，且不再对第二个运算子求值。**

```
't' && '' // ""
't' && 'f' // "f"
't' && (1 + 2) // 3
'' && 'f' // ""
'' && '' // ""

var x = 1;
(1 - 1) && ( x += 1) // 0
x // 1
```
上面代码的最后一个例子，由于且运算符的第一个运算子的布尔值为`false`，则直接返回它的值`0`，而不再对第二个运算子求值，所以变量`x`的值没变。

**这种跳过第二个运算子的机制，被称为<font color="red">“短路”</font>**。有些程序员喜欢用它取代`if`结构，比如下面是一段`if`结构的代码，就可以用且运算符改写。

```
if (i) {
  doSomething();
}

// 等价于

i && doSomething();
```
上面代码的两种写法是等价的，但是**后一种不容易看出目的，也不容易除错，建议谨慎使用**。

**且运算符可以多个连用，这时返回第一个布尔值为`false`的表达式的<font color="red">值</font>，如果没有布尔值为`false`的表达式，则返回最后一个表达式的值**。

```
true && 'foo' && '' && 4 && 'foo' && true
// ''
```
上面代码中，第一个布尔值为`false`的表达式为第三个表达式，所以得到一个空字符串。

注意，使用且运算符的表达式如果放在if语句的括号内时，if语句会求出整个表达式的最终返回值的布尔值，并依此来决定下一步的操作。

### 或运算符（||）

或运算符（`||`）也**用于多个表达式的求值**。它的运算规则是：**如果第一个运算子的布尔值为`true`，则返回第一个运算子的值，且不再对第二个运算子求值；如果第一个运算子的布尔值为`false`，则返回第二个运算子的值。**

```
't' || '' // "t"
't' || 'f' // "t"
'' || 'f' // "f"
'' || '' // ""
```

短路规则对这个运算符也适用。

```
var x = 1;
true || (x = 2) // true
x // 1
```
上面代码中，且运算符的第一个运算子为`true`，所以直接返回`true`，不再运行第二个运算子。所以，`x`的值没有改变。**这种只通过第一个表达式的值，控制是否运行第二个表达式的机制，就称为“短路”**（short-cut）。

**或运算符可以多个连用，这时返回第一个布尔值为`true`的表达式的值，如果没有布尔值为`true`的表达式，则返回最后一个表达式的值**。

```
false || 0 || '' || 4 || 'foo' || true
// 4
```
上面代码中第一个布尔值为`true`的表达式是第四个表达式，所以得到数值`4`。

或运算符常用于为一个变量设置默认值。
```
function saveText(text) {
  text = text || '';
  // ...
}

// 或者写成
saveText(this.text || '')
```
上面代码表示，如果函数调用时，没有提供参数，则该参数默认设置为空字符串。

### 三元条件运算符（?:）

三元条件运算符由问号（`?`）和冒号（`:`）组成，分隔三个表达式。它是 JavaScript 语言唯一一个需要三个运算子的运算符。**如果第一个表达式的布尔值为`true`，则返回第二个表达式的值，否则返回第三个表达式的值。**

```
't' ? 'hello' : 'world' // "hello"
0 ? 'hello' : 'world' // "world"
```
上面代码的`t`和`0`的布尔值分别为`true`和`false`，所以分别返回第二个和第三个表达式的值。

通常来说，三元条件表达式与`if...else`语句具有同样表达效果，前者可以表达的，后者也能表达。但是两者具有一个重大差别，**<font color=
red">`if...else`是语句，没有返回值；三元条件表达式是表达式，具有返回值</font>**。所以，**在需要返回值的场合，只能使用三元条件表达式，而不能使用`if..else`**。

```
console.log(true ? 'T' : 'F');
```
上面代码中，`console.log`方法的参数必须是一个表达式，这时就只能使用三元条件表达式。如果要用`if...else`语句，就必须改变整个代码写法了。

## 位运算符

### 概述

位运算符用于直接对二进制位进行计算，一共有7个。

> 二进制或运算符（or）：符号为`|`，表示若两个二进制位都为0，则结果为0，否则为1。
> 二进制与运算符（and）：符号为`&`，表示若两个二进制位都为1，则结果为1，否则为0。
> 二进制否运算符（not）：符号为`~`，表示对一个二进制位取反。
> 异或运算符（xor）：符号为`^`，表示若两个二进制位不相同，则结果为1，否则为0。
> 左移运算符（left shift）：符号为`<<`，详见下文解释。
> 右移运算符（right shift）：符号为`>>`，详见下文解释。
> 带符号位的右移运算符（zero filled right shift）：符号为`>>>`，详见下文解释。

这些位运算符直接处理每一个比特位（bit），所以是非常底层的运算，**好处是速度极快，缺点是很不直观，许多场合不能使用它们，否则会使代码难以理解和查错**。

有一点需要特别注意，**<font color="red">位运算符只对整数起作用，如果一个运算子不是整数，会自动转为整数后再执行</font>**。另外，虽然在 JavaScript 内部，数值都是以64位浮点数的形式储存，但是**做位运算的时候，是以32位带符号的整数进行运算的，并且返回值也是一个32位带符号的整数**。

```
i = i | 0;
```
上面这行代码的意思，就是将`i`（不管是整数或小数）转为32位整数。

**利用这个特性，可以写出一个函数，将任意数值转为32位整数**。
```
function toInt32(x) {
  return x | 0;
}
```
上面这个函数将任意值与`0`进行一次或运算，这个位运算会自动将一个值转为32位整数。下面是这个函数的用法。

```
toInt32(1.001) // 1
toInt32(1.999) // 1
toInt32(1) // 1
toInt32(-1) // -1
toInt32(Math.pow(2, 32) + 1) // 1
toInt32(Math.pow(2, 32) - 1) // -1
```
上面代码中，`toInt32`可以将小数转为整数。对于一般的整数，返回值不会有任何变化。对于大于2的32次方的整数，大于32位的数位都会被舍去。

**技巧：<font color="red">可以使用`a|0`将a（是一个数字，不管是整数或小数）转为32位整数</font>**。

### 二进制或运算符

二进制或运算符（`|`）逐位比较两个运算子，两个二进制位之中只要有一个为`1`，就返回`1`，否则返回`0`。

```
0 | 3 // 3
```
上面代码中，`0`和`3`的二进制形式分别是`00`和`11`，所以进行二进制或运算会得到11（即3）。

位运算只对整数有效，遇到小数时，会将小数部分舍去，只保留整数部分。所以，将一个小数与0进行二进制或运算，等同于对该数去除小数部分，即取整数位。与0进行二进制或运算，则二进制位保持不变，原本为1的二进制位仍然为1，原本为0的二进制位仍然为0。

```
2.9 | 0 // 2
-2.9 | 0 // -2
```
需要注意的是，**这种取整方法不适用超过32位整数最大值`2147483647`的数。**

```
2147483649.4 | 0;
// -2147483647
```

### 二进制与运算符

二进制与运算符（`&`）的规则是逐位比较两个运算子，两个二进制位之中只要有一个位为`0`，就返回`0`，否则返回`1`。

```
0 & 3 // 0
```
上面代码中，`0`（二进制`00`）和`3`（二进制`11`）进行二进制与运算会得到`00`（即`0`）。

### 二进制否运算符

二进制否运算符（`~`）**将每个二进制位都变为相反值（0变为1，1变为0）**。它的返回结果有时比较难理解，因为涉及到计算机内部的数值表示机制。

```
~ 3 // -4
```
上面表达式对`3`进行二进制否运算，得到`-4`。之所以会有这样的结果，是因为位运算时，JavaScirpt内部将所有的运算子都转为32位的二进制整数再进行运算。

3的32位整数形式是`00000000000000000000000000000011`，二进制否运算以后得到`11111111111111111111111111111100`。**由于第一位（符号位）是`1`，所以这个数是一个负数**。**JavaScript 内部采用补码形式表示负数，即需要将这个数减去1，再取一次反，然后加上负号，才能得到这个负数对应的10进制值**。这个数减去1等于`11111111111111111111111111111011`，再取一次反得到`00000000000000000000000000000100`，再加上负号就是`-4`。

考虑到这样的过程比较麻烦，可以简单记忆成，**<font color="red">一个数与自身的取反值相加，等于`-1`。则取反后的结果等于`-1`减去这个数本身。</font>**

```
~ -3 // 2
```
上面表达式可以这样算，`-3`的取反值等于`-1`减去`-3`，结果为`2`。

对一个整数连续两次二进制否运算，得到它自身。

```
~~3 // 3
```

**<font color="red">所有的位运算都只对整数有效</font>**。二进制否运算遇到小数时，也会将小数部分舍去，只保留整数部分。所以，**对一个小数连续进行两次二进制否运算，能达到取整效果**。

```
~~2.9 // 2
~~47.11 // 47
~~1.9999 // 1
~~3 // 3
```

**使用二进制否运算取整，是所有取整方法中<font color="red">最快</font>的一种**。

**对字符串进行二进制否运算，JavaScript 引擎会先调用Number函数，将字符串转为数值**。

```
// 相当于~Number('011')
~'011'  // -12

// 相当于~Number('42 cats')
~'42 cats' // -1

// 相当于~Number('0xcafebabe')
~'0xcafebabe' // 889275713

// 相当于~Number('deadbeef')
~'deadbeef' // -1
```

`Number`函数将字符串转为数值的规则，参见《数据的类型转换》一章。

**对于其他类型的值，二进制否运算也是先用`Number`转为数值，然后再进行处理**。

```
// 相当于 ~Number([])
~[] // -1

// 相当于 ~Number(NaN)
~NaN // -1

// 相当于 ~Number(null)
~null // -1
```

### 异或运算

异或运算（`^`）在**两个二进制位不同时返回`1`，相同时返回`0`**。

```
0 ^ 3 // 3
```
上面表达式中，`0`（二进制`00`）与`3`（二进制`11`）进行异或运算，它们每一个二进制位都不同，所以得到`11`（即`3`）。

“异或运算”有一个特殊运用，**<font color="red">连续对两个数a和b进行三次异或运算，a^=b; b^=a; a^=b;，可以互换它们的值</font>**。这意味着，**<font color="red">使用“异或运算”可以在不引入临时变量的前提下，互换两个变量的值，这是互换两个变量的值的最快方法</font>**。

```
var a = 10;
var b = 99;

a ^= b, b ^= a, a ^= b;

a // 99
b // 10

// 或者写为

a = a^b; // 105
b = a^b; // 10
a = a^b; // 99

a // 99
b // 10
```

**<font color="red">异或运算也可以用来取整</font>**。

```
12.9 ^ 0 // 12
```

### 左移运算符

**左移运算符（`<<`）表示将一个数的二进制值向左移动指定的位数，尾部补0，即乘以2的指定次方（<font color="red">最高位即符号位不参与移动</font>）**。

**<font color="red">即一个数字a，左移n位，相当于a乘以2的n次方</font>**。

```
// 4 的二进制形式为100，
// 左移一位为1000（即十进制的8）
// 相当于乘以2的1次方
4 << 1
// 8

-4 << 1
// -8
```
上面代码中，`-4`左移一位得到`-8`，是因为`-4`的二进制形式是`11111111111111111111111111111100`，左移一位后得到`11111111111111111111111111111000`，该数转为十进制（减去`1`后取反，再加上负号）即为`-8`。

**<font color="red">如果左移0位</font>，就相当于将该数值转为32位整数，<font color="red">等同于取整</font>，对于正数和负数都有效**。

```
13.5 << 0
// 13

-13.5 << 0
// -13
```

左移运算符用于二进制数值非常方便。

```
var color = {r: 186, g: 218, b: 85};

// RGB to HEX
// (1 << 24)的作用为保证结果是6位数
var rgb2hex = function(r, g, b) {
  return '#' + ((1 << 24) + (r << 16) + (g << 8) + b)
    .toString(16) // 先转成十六进制，然后返回字符串
    .substr(1);   // 去除字符串的最高位，返回后面六个字符串
}

rgb2hex(color.r, color.g, color.b)
// "#bada55"
```
上面代码使用左移运算符，**将颜色的 RGB 值转为 HEX 值**。

### 右移运算符

**右移运算符（`>>`）表示将一个数的二进制值向右移动指定的位数，头部补0，即<font color="red">除以2的指定次方</font>（<font color="red">最高位即符号位不参与移动</font>）。**

**<font color="red">即一个数字a，右移n位，相当于a除以2的n次方</font>**。

```
4 >> 1
// 2
/*
// 因为4的二进制形式为 00000000000000000000000000000100，
// 右移一位得到 00000000000000000000000000000010，
// 即为十进制的2
*/

-4 >> 1
// -2
/*
// 因为-4的二进制形式为 11111111111111111111111111111100，
// 右移一位，头部补1，得到 11111111111111111111111111111110,
// 即为十进制的-2
*/
```

右移运算可以模拟 2 的整除运算。

```
5 >> 1
// 2
// 相当于 5 / 2 = 2

21 >> 2
// 5
// 相当于 21 / 4 = 5

21 >> 3
// 2
// 相当于 21 / 8 = 2

21 >> 4
// 1
// 相当于 21 / 16 = 1
```

### 带符号位的右移运算符

**带符号位的右移运算符（`>>>`）表示将一个数的二进制形式向右移动，包括符号位也参与移动，头部补0**。所以，**该运算总是得到正值**。**对于正数，该运算的结果与右移运算符（>>）完全一致，区别主要在于负数**。

```
4 >>> 1
// 2

-4 >>> 1
// 2147483646
/*
// 因为-4的二进制形式为11111111111111111111111111111100，
// 带符号位的右移一位，得到01111111111111111111111111111110，
// 即为十进制的2147483646。
*/
```

这个运算**实际上将一个值转为32位无符号整数**。

**查看一个负整数在计算机内部的储存形式，最快的方法就是使用这个运算符，进行右移`0`的运算**。

```
-1 >>> 0 // 4294967295
```
上面代码表示，`-1`作为32位整数时，内部的储存形式使用无符号整数格式解读，值为 `4294967295`（即`(2^32)-1`，等于`11111111111111111111111111111111`）。

### 开关作用

**位运算符可以用作设置对象属性的开关**。

假定某个对象有四个开关，每个开关都是一个变量。那么，可以设置一个四位的二进制数，它的每个位对应一个开关。

```
var FLAG_A = 1; // 0001
var FLAG_B = 2; // 0010
var FLAG_C = 4; // 0100
var FLAG_D = 8; // 1000
```

上面代码设置 `A`、`B`、`C`、`D` 四个开关，每个开关分别占有一个二进制位。

然后，就可以用二进制与运算检验，当前设置是否打开了指定开关。

```
var flags = 5; // 二进制的0101

if (flags & FLAG_C) {
  // ...
}
// 0101 & 0100 => 0100 => true
```
上面代码检验是否打开了开关`C`。如果打开，会返回`true`，否则返回`false`。

现在假设需要打开`A`、`B`、`D`三个开关，我们可以构造一个掩码变量。

```
var mask = FLAG_A | FLAG_B | FLAG_D;
// 0001 | 0010 | 1000 => 1011
```
上面代码对`A`、`B`、`D`三个变量进行二进制或运算，得到掩码值为二进制的`1011`。

有了掩码，二进制或运算可以确保打开指定的开关。

```
flags = flags | mask;
```

二进制与运算可以将当前设置中凡是与开关设置不一样的项，全部关闭。

```
flags = flags & mask;
```

异或运算可以切换（toggle）当前设置，即第一次执行可以得到当前设置的相反值，再执行一次又得到原来的值。

```
flags = flags ^ mask;
```

二进制否运算可以翻转当前设置，即原设置为`0`，运算后变为`1`；原设置为`1`，运算后变为`0`。

```
flags = ~flags;
```

## 其他运算符

### void 运算符

`void`运算符的作用是执行一个表达式，然后不返回任何值，或者说返回`undefined`。

```
void 0 // undefined
void(0) // undefined
```

上面是`void`运算符的两种写法，都正确。**建议采用后一种形式，即总是使用圆括号。因为`void`运算符的优先性很高，如果不使用括号，容易造成错误的结果**。比如，`void 4 + 7`实际上等同于`(void 4) + 7`。

下面是void运算符的一个例子。
```
var x = 3;
void (x = 5) //undefined
x // 5
```

这个运算符的主要用途是浏览器的书签工具（bookmarklet），以及在超级链接中插入代码防止网页跳转。

请看下面的代码。
```
<script>
function f() {
  console.log('Hello World');
}
</script>
<a href="http://example.com" onclick="f(); return false;">点击</a>
```
上面代码中，点击链接后，会先执行`onclick`的代码，由于`onclick`返回`false`，所以浏览器不会跳转到 `example.com`。

void运算符可以取代上面的写法。
```
<a href="javascript: void(f())">文字</a>
```

下面是一个更实际的例子，用户点击链接提交表单，但是不产生页面跳转。
```
<a href="javascript: void(document.form.submit())">
  提交
</a>
```

### 逗号运算符

**逗号运算符用于对两个表达式求值，并<font color="red">返回后一个表达式的值</font>**。

```
'a', 'b' // "b"

var x = 0;
var y = (x++, 10);
x // 1
y // 10
```

## 运算顺序

### 优先级

JavaScript 各种运算符的优先级别（Operator Precedence）是不一样的。**优先级高的运算符先执行，优先级低的运算符后执行。**

```
4 + 5 * 6 // 34
```
上面的代码中，乘法运算符（`*`）的优先性高于加法运算符（`+`），所以先执行乘法，再执行加法，相当于下面这样。
```
4 + (5 * 6) // 34
```

如果多个运算符混写在一起，常常会导致令人困惑的代码。

```
var x = 1;
var arr = [];

var y = arr.length <= 0 || arr[0] === undefined ? x : arr[0];
```
上面代码中，变量`y`的值就很难看出来，因为这个表达式涉及5个运算符，到底谁的优先级最高，实在不容易记住。

根据语言规格，这五个运算符的优先级从高到低依次为：小于等于（`<=`)、严格相等（`===`）、或（`||`）、三元（`?:`）、等号（`=`）。因此上面的表达式，实际的运算顺序如下。
```
var y = ((arr.length <= 0) || (arr[0] === undefined)) ? x : arr[0];
```

**记住所有运算符的优先级，是非常难的，也是没有必要的。**

### 圆括号的作用

**圆括号（`()`）可以用来提高运算的优先级，因为它的优先级是最高的**，即圆括号中的表达式会第一个运算。

```
(4 + 5) * 6 // 54
```
上面代码中，由于使用了圆括号，加法会先于乘法执行。

运算符的优先级别十分繁杂，且都是硬性规定，因此**建议总是使用圆括号，保证运算顺序清晰可读，这对代码的维护和除错至关重要**。

顺便说一下，**圆括号不是运算符，而是一种语法结构**。它一共有两种用法：**一种是把表达式放在圆括号之中，提升运算的优先级；另一种是跟在函数的后面，作用是调用函数**。

注意，**因为圆括号不是运算符，所以不具有求值作用，只改变运算的优先级**。

```
var x = 1;
(x) = 2;
```
上面代码的第二行，如果圆括号具有求值作用，那么就会变成`1 = 2`，这是会报错了。但是，上面的代码可以运行，这验证了圆括号只改变优先级，不会求值。

这也意味着，如果整个表达式都放在圆括号之中，那么不会有任何效果。

```
(exprssion)
// 等同于
expression
```

**函数放在圆括号中，会返回函数本身。如果圆括号紧跟在函数的后面，就表示调用函数**。

```
function f() {
  return 1;
}

(f) // function f(){return 1;}
f() // 1
```
上面代码中，函数放在圆括号之中会返回函数本身，圆括号跟在函数后面则是调用函数。

**<font color="red">圆括号之中，只能放置表达式，如果将语句放在圆括号之中，就会报错</font>**。

```
(var a = 1)
// SyntaxError: Unexpected token var
```

### 左结合与右结合

**对于优先级别相同的运算符，大多数情况，计算顺序总是从左到右，这叫做运算符的“左结合”（left-to-right associativity），即从左边开始计算**。

```
x + y + z
```
上面代码先计算最左边的`x`与`y`的和，然后再计算与`z`的和。

但是**少数运算符的计算顺序是从右到左，即从右边开始计算，这叫做运算符的“右结合”**（right-to-left associativity）。其中，**最主要的是赋值运算符（`=`）和三元条件运算符（`?:`）**。
```
w = x = y = z;
q = a ? b : c ? d : e ? f : g;
```
上面代码的运算结果，相当于下面的样子。
```
w = (x = (y = z));
q = a ? b : (c ? d : (e ? f : g));
```
上面的两行代码，各有三个等号运算符和三个三元运算符，都是先计算最右边的那个运算符。

# 数据类型转换

JavaScript 是一种**动态类型语言**，变量没有类型限制，可以随时赋予任意值。

虽然变量的数据类型是不确定的，但是各种运算符对数据类型是有要求的。如果运算符发现，运算子的类型与预期不符，就会自动转换类型。比如，减法运算符预期左右两侧的运算子应该是数值，如果不是，就会自动将它们转为数值。

## 强制转换

强制转换主要指使用Number、String和Boolean三个函数，手动将各种类型的值，分布转换成数字、字符串或者布尔值。

### Number()

使用Number函数，可以将任意类型的值转化成数值。

下面分成两种情况讨论，一种是参数是原始类型的值，另一种是参数是对象。

#### 原始类型值

原始类型值的转换规则如下。

```
// 数值：转换后还是原来的值
Number(324) // 324

// 字符串：如果可以被解析为数值，则转换为相应的数值
Number('324') // 324

// 字符串：如果不可以被解析为数值，返回 NaN
Number('324abc') // NaN

// 空字符串转为0
Number('') // 0

// 布尔值：true 转成 1，false 转成 0
Number(true) // 1
Number(false) // 0

// undefined：转成 NaN
Number(undefined) // NaN

// null：转成0
Number(null) // 0

Number(NaN) // NaN
```

Number函数将字符串转为数值，要比parseInt函数严格很多。基本上，只要有一个字符无法转成数值，整个字符串就会被转为NaN。

```
parseInt('42 cats') // 42
Number('42 cats') // NaN
```

上面代码中，parseInt逐个解析字符，而Number函数整体转换字符串的类型。

另外，parseInt和Number函数都会自动过滤一个字符串前导和后缀的空格。

```
parseInt('\t\v\r12.34\n') // 12
Number('\t\v\r12.34\n') // 12.34
```

#### 对象

简单的规则是，Number方法的参数是对象时，将返回NaN，除非是包含**单个数值**的数组。

```
Number({a: 1}) // NaN
Number([1, 2, 3]) // NaN
Number([5]) // 5
Number([]) // 0，因为toString方法转化为""，即空字符串
```

之所以会这样，是因为Number背后的转换规则比较复杂。

1. 调用对象自身的valueOf方法。如果返回原始类型的值，则直接对该值使用Number函数，不再进行后续步骤。

2. 如果valueOf方法返回的还是对象，则改为调用对象自身的toString方法。如果toString方法返回原始类型的值，则对该值使用Number函数，不再进行后续步骤。

3. 如果toString方法返回的是对象，就报错。

请看下面的例子。

```
var obj = {x: 1};
Number(obj) // NaN

// 等同于
if (typeof obj.valueOf() === 'object') {
  Number(obj.toString());
} else {
  Number(obj.valueOf());
}
```
上面代码中，`Number`函数将`obj`对象转为数值。背后发生了一连串的操作，首先调用`obj.valueOf`方法, 结果返回对象本身；于是，继续调用`obj.toString`方法，这时返回字符串`[object Object]`，对这个字符串使用Number函数，得到NaN。

默认情况下，对象的`valueOf`方法返回对象本身，所以一般总是会调用`toString`方法，而`toString`方法返回对象的类型字符串（比如`[object Object]`）。所以，会有下面的结果。

```
Number({}) // NaN
```

如果`toString`方法返回的不是原始类型的值，结果就会报错。

```
var obj = {
  valueOf: function () {
    return {};
  },
  toString: function () {
    return {};
  }
};

Number(obj)
// TypeError: Cannot convert object to primitive value
```

上面代码的`valueOf`和`toString`方法，返回的都是对象，所以转成数值时会报错。

从上例还可以看到，`valueOf`和`toString`方法，都是可以自定义的。

```
Number({
  valueOf: function () {
    return 2;
  }
})
// 2

Number({
  toString: function () {
    return 3;
  }
})
// 3

Number({
  valueOf: function () {
    return 2;
  },
  toString: function () {
    return 3;
  }
})
// 2
```
上面代码对三个对象使用`Number`函数。第一个对象返回`valueOf`方法的值，第二个对象返回`toString`方法的值，第三个对象表示`valueOf`方法先于`toString`方法执行。

### String()

`String`函数可以将**任意类型**的值转化成字符串，转换规则如下。

#### 原始类型值

> 数值：转为相应的字符串。
> 字符串：转换后还是原来的值。
> 布尔值：true转为字符串”true”，false转为字符串”false”。
> undefined：转为字符串”undefined”。
> null：转为字符串”null”。


```
String(123) // "123"
String('abc') // "abc"
String(true) // "true"
String(undefined) // "undefined"
String(null) // "null"
```

#### 对象

`String`方法的参数如果是对象，返回一个类型字符串；如果是数组，返回该数组的字符串形式。

```
String({a: 1}) // "[object Object]"
String([1, 2, 3]) // "1,2,3"
```

`String`方法背后的转换规则，与`Number`方法基本相同，只是互换了`valueOf`方法和`toString`方法的执行顺序。

1. 先调用对象自身的`toString`方法。如果返回原始类型的值，则对该值使用`String`函数，不再进行以下步骤。

2. 如果`toString`方法返回的是对象，再调用原对象的`valueOf`方法。如果`valueOf`方法返回原始类型的值，则对该值使用`String`函数，不再进行以下步骤。

3. 如果`valueOf`方法返回的是对象，就报错。

下面是一个例子。

```
String({a: 1})
// "[object Object]"

// 等同于
String({a: 1}.toString())
// "[object Object]"
```
上面代码先调用对象的`toString`方法，发现返回的是字符串`[object Object]`，就不再调用`valueOf`方法了。

如果`toString`法和`valueOf`方法，返回的都是对象，就会报错。

```
var obj = {
  valueOf: function () {
    return {};
  },
  toString: function () {
    return {};
  }
};

String(obj)
// TypeError: Cannot convert object to primitive value
```

下面是通过自定义toString方法，改变返回值的例子。

```
String({
  toString: function () {
    return 3;
  }
})
// "3"

String({
  valueOf: function () {
    return 2;
  }
})
// "[object Object]"

String({
  valueOf: function () {
    return 2;
  },
  toString: function () {
    return 3;
  }
})
// "3"
```

上面代码对三个对象使用`String`函数。第一个对象返回`toString`方法的值（数值3），第二个对象返回的还是`toString`方法的值（`[object Object]`），第三个对象表示`toString`方法先于`valueOf`方法执行。

### Boolean()

Boolean函数可以将任意类型的值转为布尔值。

它的转换规则相对简单：除了以下五个值的转换结果为`false`，其他的值全部为`true`。

> undefined
> null
> -0或+0
> NaN
> ‘’（空字符串）

```
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean(NaN) // false
Boolean('') // false
```

注意，所有对象（包括空对象）的转换结果都是true，甚至连**`false`对应的布尔对象`new Boolean(false)`也是`true`**（详见《原始类型值的包装对象》一章）。

```
Boolean({}) // true
Boolean([]) // true
Boolean(new Boolean(false)) // true
```

所有对象的布尔值都是`true`，这是因为 JavaScript 语言设计的时候，出于性能的考虑，如果对象需要计算才能得到布尔值，对于`obj1 && obj2`这样的场景，可能会需要较多的计算。为了保证性能，就统一规定，对象的布尔值为`true`。


## 自动转换

下面介绍自动转换，它是以强制转换为基础的。

遇到以下三种情况时，JavaScript 会自动转换数据类型，即转换是自动完成的，用户不可见。

第一种情况，**不同类型的数据互相运算。**
```
123 + 'abc' // "123abc"
```

第二种情况，**对非布尔值类型的数据求布尔值。**
```
if ('abc') {
  console.log('hello')
}  // "hello"
```

第三种情况，**对非数值类型的值使用一元运算符（即+和-）。**
```
+ {foo: 'bar'} // NaN
- [1, 2, 3] // NaN
```

自动转换的规则是这样的：**预期什么类型的值，就调用该类型的转换函数**。比如，某个位置预期为字符串，就调用String函数进行转换。**如果该位置即可以是字符串，也可能是数值，那么默认转为数值。**

由于自动转换具有不确定性，而且不易除错，**建议在预期为布尔值、数值、字符串的地方，全部使用Boolean、Number和String函数进行显式转换。**

### 自动转换为布尔值
JavaScript 遇到预期为布尔值的地方（比如`if`语句的条件部分），就会将非布尔值的参数自动转换为布尔值。系统内部会自动调用`Boolean`函数。

下面两种写法，有时也用于将一个表达式转为布尔值。它们内部调用的也是Boolean函数。

```
// 写法一
expression ? true : false

// 写法二
!! expression
```

### 自动转换为字符串
JavaScript 遇到预期为字符串的地方，就会将非字符串的值自动转为字符串。具体规则是，先将复合类型的值转为原始类型的值，再将原始类型的值转为字符串。

字符串的自动转换，主要发生在字符串的加法运算时。**当一个值为字符串，另一个值为非字符串，则后者转为字符串。 **

```
'5' + 1 // '51'
'5' + true // "5true"
'5' + false // "5false"
'5' + {} // "5[object Object]"
'5' + [] // "5"
'5' + function (){} // "5function (){}"
'5' + undefined // "5undefined"
'5' + null // "5null"
```

这种自动转换很容易出错。

```
var obj = {
  width: '100'
};

obj.width + 20 // "10020"
```
上面代码中，开发者可能期望返回120，但是由于自动转换，实际上返回了一个字符10020。

### 自动转换为数值
JavaScript 遇到预期为数值的地方，就会将参数值自动转换为数值。系统内部会自动调用`Number`函数。

**除了加法运算符（`+`）有可能把运算子转为字符串，其他运算符都会把运算子自动转成数值。**

下面是几个特殊例子。

```
true - 1  // 0
false - 1 // -1
'abc' - 1   // NaN
'5' * []    // 0
false / '5' // 0，可以计算0除以几，只不过都等于0
null + 1 // 1
undefined + 1 // NaN
```
上面代码中，运算符两侧的运算子，都被转成了数值。

注意：**`null`转为数值时为`0`，而`undefined`转为数值时为`NaN`。**

一元运算符也会把运算子转成数值。

```
+'abc' // NaN
-'abc' // NaN
+true // 1
-false // 0
```

# 错误处理机制

## Error 实例对象

JavaScript 解析或运行时，一旦发生错误，引擎就会抛出一个错误对象。JavaScript 原生提供Error构造函数，所有抛出的错误都是这个构造函数的实例。

```
var err = new Error('出错了');
err.message // "出错了"
```

上面代码中，我们调用`Error`构造函数，生成一个实例对象`err`。`Error`构造函数接受一个参数，表示错误提示，可以从实例的`message`属性读到这个参数。抛出`Error`实例对象以后，整个程序就中断在发生错误的地方，不再往下执行。

JavaScript 语言标准只提到，`Error`实例对象必须有`message`属性，表示出错时的提示信息，没有提到其他属性。大多数 JavaScript 引擎，对`Error`实例还提供`name`和`stack`属性，分别表示错误的名称和错误的堆栈，但它们是非标准的，不是每种实现都有。

使用`name`和`message`这两个属性，可以对发生什么错误有一个大概的了解。

```
if (error.name) {
  console.log(error.name + ': ' + error.message);
}
```

stack属性用来查看错误发生时的堆栈。
```
function throwit() {
  throw new Error('');
}

function catchit() {
  try {
    throwit();
  } catch(e) {
    console.log(e.stack); // print stack trace
  }
}

catchit()
// Error
//    at throwit (~/examples/throwcatch.js:9:11)
//    at catchit (~/examples/throwcatch.js:3:9)
//    at repl:1:5
```
上面代码中，错误堆栈的最内层是`throwit`函数，然后是`catchit`函数，最后是函数的运行环境。

## 原生错误类型

`Error`实例对象是最一般的错误类型，在它的基础上，JavaScript 还定义了其他6种错误对象。也就是说，存在Error的6个派生对象。

### SyntaxError 对象

`SyntaxError`对象是**解析代码时发生的语法错误**。

```
// 变量名错误
var 1a;
// Uncaught SyntaxError: Invalid or unexpected token

// 缺少括号
console.log 'hello');
// Uncaught SyntaxError: Unexpected string
```
上面代码的错误，都是在语法解析阶段就可以发现，所以会抛出`SyntaxError`。第一个错误提示是“token 非法”，第二个错误提示是“字符串不符合要求”。

### ReferenceError 对象

`ReferenceError`对象是**引用一个不存在的变量**时发生的错误。

```
// 使用一个不存在的变量
unknownVariable
// Uncaught ReferenceError: unknownVariable is not defined
```

另一种触发场景是，将一个值分配给无法分配的对象，比如对函数的运行结果或者this赋值。

```
// 等号左侧不是变量
console.log() = 1
// Uncaught ReferenceError: Invalid left-hand side in assignment

// this 对象不能手动赋值
this = 1
// ReferenceError: Invalid left-hand side in assignment
```

### RangeError 对象

`RangeError`对象是一个**值超出有效范围**时发生的错误。

主要有几种情况，一是数组长度为负数，二是Number对象的方法参数超出范围，以及函数堆栈超过最大值。
```
// 数组长度不得为负数
new Array(-1)
// Uncaught RangeError: Invalid array length
```

### TypeError 对象

`TypeError`对象是**变量或参数不是预期类型**，或者**调用了不存在的方法**时发生的错误。比如，对字符串、布尔值、数值等原始类型的值使用new命令，就会抛出这种错误，因为new命令的参数应该是一个构造函数。

```
new 123
// Uncaught TypeError: number is not a func

var obj = {};
obj.unknownMethod()
// Uncaught TypeError: obj.unknownMethod is not a function
```

### URIError 对象
`URIError`对象是URI相关函数的参数不正确时抛出的错误，主要涉及`encodeURI()`、`decodeURI()`、`encodeURIComponent()`、`decodeURIComponent()`、`escape()`和`unescape()`这六个函数。

```
decodeURI('%2')
// URIError: URI malformed
```

### EvalError 对象

`eval`函数没有被正确执行时，会抛出`EvalError`错误。该错误类型已经不再使用了，只是为了保证与以前代码兼容，才继续保留。
`eval`是求值函数，实际上用的并不多。

### 总结

以上这6种派生错误，连同原始的Error对象，都是构造函数。开发者可以使用它们，手动生成错误对象的实例。这些构造函数都接受一个参数，代表错误提示信息（message）。

```
var err1 = new Error('出错了！');
var err2 = new RangeError('出错了，变量超出有效范围！');
var err3 = new TypeError('出错了，变量类型无效！');

err1.message // "出错了！"
err2.message // "出错了，变量超出有效范围！"
err3.message // "出错了，变量类型无效！"
```

## 自定义错误
除了 JavaScript 原生提供的七种错误对象，还可以定义自己的错误对象。

```
function UserError(message) {
  this.message = message || '默认信息';
  this.name = 'UserError';
}

UserError.prototype = new Error();
UserError.prototype.constructor = UserError;
```

上面代码自定义一个错误对象UserError，让它继承Error对象。然后，就可以生成这种自定义类型的错误了。

```
new UserError('这是自定义的错误！');
```

对上例中的prototype进行简短的解释，**prototype 属性使用户有能力向对象添加属性和方法**，语法为`object.prototype.name=value`。
例子如下：

```
function employee(name,job,born)
{
  this.name=name;
  this.job=job;
  this.born=born;
}

var bill=new employee("Bill Gates","Engineer",1985);

employee.prototype.salary=null;
bill.salary=20000;
document.write(bill.salary);

// 20000
```
对于上例中的constructor，**constructor 属性返回对创建此对象的数组函数的引用**，语法`object.constructor`。
例子如下：

```
var test=new Array();

if (test.constructor==Array)
{
  document.write("This is an Array");
}
if (test.constructor==Boolean)
{
  document.write("This is a Boolean");
}
if (test.constructor==Date)
{
  document.write("This is a Date");
}
if (test.constructor==String)
{
  document.write("This is a String");
}
```

使用constructor属性。
```
function employee(name,job,born)
{
  this.name=name;
  this.job=job;
  this.born=born;
}
var bill=new employee("Bill Gates","Engineer",1985);
document.write(bill.constructor);

// function employee(name, job, born)
// {this.name = name; this.job = job; this.born = born;}
```

## throw语句

`throw`语句的作用是手动中断程序执行，抛出一个错误。

```
if (x < 0) {
  throw new Error('x 必须为正数');
}
// Uncaught ReferenceError: x is not defined
```

上面代码中，如果变量`x`小于0，就手动抛出一个错误，告诉用户`x`的值不正确，整个程序就会在这里中断执行。可以看到，`throw`抛出的错误就是它的参数，这里是一个`Error`实例。

throw也可以抛出自定义错误。

```
function UserError(message) {
  this.message = message || '默认信息';
  this.name = 'UserError';
}

throw new UserError('出错了！');
// Uncaught UserError {message: "出错了！", name: "UserError"}
```

上面代码中，`throw`抛出的是一个`UserError`实例。

实际上，throw可以抛出任何类型的值。也就是说，它的参数可以是任何值。

```
// 抛出一个字符串
throw 'Error！';
// Uncaught Error！

// 抛出一个数值
throw 42;
// Uncaught 42

// 抛出一个布尔值
throw true;
// Uncaught true

// 抛出一个对象
throw {
  toString: function () {
    return 'Error!';
  }
};
// Uncaught {toString: ƒ}
```

对于 JavaScript 引擎来说，遇到`throw`语句，程序就中止了。引擎会接收到`throw`抛出的信息，可能是一个错误实例，也可能是其他类型的值。

## try…catch 结构

一旦发生错误，程序就中止执行了。JavaScript提供了`try...catch`结构，允许对错误进行处理，选择是否往下执行。

```
try {
  throw new Error('出错了!');
} catch (e) {
  console.log(e.name + ": " + e.message);
  console.log(e.stack);
}
// Error: 出错了!
//   at <anonymous>:3:9
//   ...
```
上面代码中，`try`代码块抛出错误（上例用的是`throw`语句），JavaScript 引擎就立即把代码的执行，转到`catch`代码块，或者说错误被`catch`代码块捕获了。`catch`接受一个参数，表示`try`代码块抛出的值。

如果你不确定某些代码是否会报错，就可以把它们放在`try…catch`代码块之中，便于进一步对错误进行处理。

`catch`代码块捕获错误之后，**程序不会中断，会按照正常流程继续执行下去**，可以避免程序崩溃。

`catch`代码块之中，还可以再抛出错误，甚至使用嵌套的`try...catch`结构，如下。

```
var n = 100;

try {
  throw n;
} catch (e) {
  if (e <= 50) {
    // ...
  } else {
    throw e;
  }
}
// Uncaught 100
```

为了捕捉不同类型的错误，`catch`代码块之中可以加入判断语句，如下。

```
try {
  foo.bar();
} catch (e) {
  if (e instanceof EvalError) {
    console.log(e.name + ": " + e.message);
  } else if (e instanceof RangeError) {
    console.log(e.name + ": " + e.message);
  }
  // ...
}
```

## finally 代码块
`try...catch`结构允许在最后添加一个`finally`代码块，表示不管是否出现错误，都必需在最后运行的语句。

```
function cleansUp() {
  try {
    throw new Error('出错了……');
    console.log('此行不会执行');
  } finally {
    console.log('完成清理工作');
  }
}

cleansUp()
// 完成清理工作
// Error: 出错了……
```

上面代码中，由于没有catch语句块，所以错误没有捕获。执行finally代码块以后，程序就中断在错误抛出的地方，注意是**执行完finally代码块以后**。

```
function idle(x) {
  try {
    console.log(x);
    return 'result';
  } finally {
    console.log("FINALLY");
  }
}

idle('hello')
// hello
// FINALLY
// "result"
```

上面代码说明，`try`代码块没有发生错误，而且里面还包括`return`语句，但是`finally`代码块依然会执行。注意，只有**在`finally`块执行完毕后，才会显示`return`语句的值**。

下面的例子说明，`return`语句的执行是排在`finally`代码之前，只是等`finally`代码执行完毕后才返回。

```
var count = 0;
function countUp() {
  try {
    return count;
  } finally {
    count++;
  }
}

countUp()
// 0
count
// 1
```
上面代码说明，`return`语句的`count`的值，是在`finally`代码块运行之前就获取了。

下面的例子充分反映了`try...catch...finally`这三者之间的执行顺序。

```
function f() {
  try {
    console.log(0);
    throw 'bug';
  } catch(e) {
    console.log(1);
    return true; // 这句原本会延迟到 finally 代码块结束再执行
    console.log(2); // 不会运行
  } finally {
    console.log(3);
    return false; // 这句会覆盖掉前面那句 return
    console.log(4); // 不会运行
  }

  console.log(5); // 不会运行
}

var result = f();
// 0
// 1
// 3

result
// false
```
`catch`代码块之中，触发转入`finally`代码快的标志，不仅有`return`语句，还有`throw`语句。

# JavaScript 编程风格

## 概述

“编程风格”（programming style）指的是编写代码的样式规则。不同的程序员，往往有不同的编程风格。

有人说，编译器的规范叫做”语法规则“（grammar），这是程序员必须遵守的；而编译器忽略的部分，就叫”编程风格“（programming style），这是程序员可以自由选择的。这种说法不完全正确，程序员固然可以自由选择编程风格，但是好的编程风格有助于写出质量更高、错误更少、更易于维护的程序。

所以，编程风格的选择不应该基于个人爱好、熟悉程度、打字量等因素，而要考虑如何尽量使代码清晰易读、减少出错。你选择的，不是你喜欢的风格，而是一种能够清晰表达你的意图的风格。这一点，对于 JavaScript 这种语法自由度很高的语言尤其重要。

必须牢记的一点是，如果你选定了一种“编程风格”，就应该坚持遵守，切忌多种风格混用。如果你加入他人的项目，就应该遵守现有的风格。

## 缩进

行首的空格和 Tab 键，都可以产生代码缩进效果（indent）。

Tab 键可以节省击键次数，但不同的文本编辑器对 Tab 的显示不尽相同，有的显示四个空格，有的显示两个空格，所以有人觉得，空格键可以使得显示效果更统一。

无论你选择哪一种方法，都是可以接受的，要做的就是始终坚持这一种选择。不要一会使用 Tab 键，一会使用空格键。

## 区块

如果循环和判断的代码体只有一行，JavaScript 允许该区块（block）省略大括号。

```
if (a)
  b();
  c();
```

上面代码的原意可能是下面这样。

```
if (a) {
  b();
  c();
}
```

但是，实际效果却是下面这样。

```
if (a) {
  b();
}
  c();
```
因此，**建议总是使用大括号表示区块**。

另外，区块起首的大括号的位置，有许多不同的写法。**最流行的有两种，一种是起首的大括号另起一行**。
```
block
{
  // ...
}
```

另一种是起首的大括号跟在关键字的后面。

```
block {
  // ...
}
```

一般来说，这两种写法都可以接受。但是，**JavaScript 要使用后一种，因为 JavaScript 会自动添加句末的分号，导致一些难以察觉的错误**。

```
return
{
  key: value
};

// 相当于
return;
{
  key: value
};
```
上面的代码的原意，是要返回一个对象，但实际上返回的是`undefined`，因为 JavaScript 自动在`return`语句后面添加了分号。为了避免这一类错误，需要写成下面这样。

```
return {
  key : value
};
```
因此，**<font color="red">表示区块起首的大括号，不要另起一行</font>**。

## 圆括号

圆括号（parentheses）在 JavaScript 中有两种作用，一种表示函数的调用，另一种表示表达式的组合（grouping）用于提高表达式的优先级。

```
// 圆括号表示函数的调用
console.log('abc');

// 圆括号表示表达式的组合
(1 + 2) * 3
```

建议**可以用空格，区分这两种不同的括号**。

> 表示函数调用时，函数名与左括号之间没有空格。

> 表示函数定义时，函数名与左括号之间没有空格。

> 其他情况时，前面位置的语法元素与左括号之间，都有一个空格。

但是要注意创建一个匿名函数的时候，`function`和`()`之间应该有一个空格。

```
function (x) {...}
```
上面的代码是一个匿名函数，`function`是语法关键字，不是函数名，所以与左括号之间应该要有一个空格。

## 行尾的分号

分号表示一条语句的结束。**JavaScript 允许省略行尾的分号**。事实上，确实有一些开发者行尾从来不写分号。但是，由于下面要讨论的原因，**建议还是不要省略这个分号**。

### 不使用分号的情况

首先，以下三种情况，语法规定本来就不需要在结尾添加分号。

#### for 和 while 循环
```
for ( ; ; ) {
} // 没有分号

while (true) {
} // 没有分号
```

注意，**`do...while`循环是有分号的。**

```
do {
  a--;
} while(a > 0); // 分号不能省略
```

#### 分支语句：if，switch，try

```
if (true) {
} // 没有分号

switch () {
} // 没有分号

try {
} catch {
} // 没有分号
```

#### 函数的声明语句

```
function f() {
} // 没有分号
```

注意，**函数表达式仍然要使用分号。**

```
var f = function f() {
};
```

以上三种情况，**如果使用了分号，并不会出错。因为，解释引擎会把这个分号解释为空语句**。

### 分号的自动添加

除了上一节的三种情况，所有语句都应该使用分号。但是，如果没有使用分号，大多数情况下，JavaScript 会自动添加。

```
var a = 1
// 等同于
var a = 1;
```

这种语法特性被称为“分号的自动添加”（Automatic Semicolon Insertion，简称 ASI）。

因此，有人提倡省略句尾的分号。麻烦的是，**如果下一行的开始可以与本行的结尾连在一起解释，JavaScript 就不会自动添加分号。**

```
// 等同于 var a = 3
var
a
=
3

// 等同于 'abc'.length
'abc'
.length

// 等同于 return a + b;
return a +
b;

// 等同于 obj.foo(arg1, arg2);
obj.foo(arg1,
arg2);

// 等同于 3 * 2 + 10 * (27 / 6)
3 * 2
+
10 * (27 / 6)
```
上面代码都会多行放在一起解释，不会每一行自动添加分号。这些例子还是比较容易看出来的，但是下面这个例子就不那么容易看出来了。

```
x = y
(function () {
  // ...
})();

// 等同于
x = y(function () {...})();
```

下面是更多不会自动添加分号的例子。
```
// 引擎解释为 c(d+e)
var a = b + c
(d+e).toString();

// 引擎解释为 a = b/hi/g.exec(c).map(d)
// 正则表达式的斜杠，会当作除法运算符
a = b
/hi/g.exec(c).map(d);

// 解释为'b'['red', 'green']，
// 即把字符串当作一个数组，按索引取值
var a = 'b'
['red', 'green'].forEach(function (c) {
  console.log(c);
})

// 解释为 function (x) { return x }(a++)
// 即调用匿名函数，结果f等于0
var a = 0;
var f = function (x) { return x }
(a++)
```

只有下一行的开始与本行的结尾，无法放在一起解释，JavaScript 引擎才会自动添加分号。

```
if (a < 0) a = 0
console.log(a)

// 等同于下面的代码，
// 因为 0console 没有意义
if (a < 0) a = 0;
console.log(a)
```

另外，**如果一行的起首是“自增”（`++`）或“自减”（`--`）运算符，则它们的前面会自动添加分号**。

```
a = b = c = 1

a
++
b
--
c

console.log(a, b, c)
// 1 2 0
```
上面代码之所以会得到`1 2 0`的结果，原因是自增和自减运算符前，自动加上了分号。上面的代码实际上等同于下面的形式。

```
a = b = c = 1;
a;
++b;
--c;
```

**如果`continue`、`break`、`return`和`throw`这四个语句后面，直接跟换行符，则会自动添加分号**。这意味着，如果`return`语句返回的是一个对象的字面量，起首的大括号一定要写在同一行，否则得不到预期结果。

```
return
{ first: 'Jane' };

// 解释成
return;
{ first: 'Jane' };
```

由于解释引擎自动添加分号的行为难以预测，因此**编写代码的时候不应该省略行尾的分号**。

不应该省略结尾的分号，还有一个原因。有些 JavaScript 代码压缩器（uglifier）不会自动添加分号，因此遇到没有分号的结尾，就会让代码保持原状，而不是压缩成一行，使得压缩无法得到最优的结果。

另外，不写结尾的分号，可能会导致脚本合并出错。所以，有的代码库在第一行语句开始前，会加上一个分号。

```
;var a = 1;
// ...
```
上面这种写法就可以避免与其他脚本合并时，排在前面的脚本最后一行语句没有分号，导致运行出错的问题。

## 全局变量

JavaScript 最大的语法缺点，可能就是全局变量对于任何一个代码块，都是可读可写。这对代码的模块化和重复使用，非常不利。

因此，**建议避免使用全局变量。如果不得不使用，可以考虑用大写字母表示变量名，这样更容易看出这是全局变量**，比如UPPER_CASE。

## 变量声明

JavaScript 会自动将变量声明”提升“（hoist）到代码块（block）的头部。

```
if (!x) {
  var x = {};
}

// 等同于
var x;
if (!x) {
  x = {};
}
```

这意味着，变量`x`是`if`代码块之前就存在了。为了避免可能出现的问题，**最好把变量声明都放在代码块的头部。**

```
for (var i = 0; i < 10; i++) {
  // ...
}

// 写成
var i;
for (i = 0; i < 10; i++) {
  // ...
}
```
上面这样的写法，就容易看出存在一个全局的循环变量`i`。

另外，**所有函数都应该在使用之前定义。函数内部的变量声明，都应该放在函数的头部**。

## with语句

`with`可以减少代码的书写，但是会造成混淆。

```
with (o) {
　foo = bar;
}
```
上面的代码，可以有四种运行结果：

```
o.foo = bar;
o.foo = o.bar;
foo = bar;
foo = o.bar;
```
这四种结果都可能发生，取决于不同的变量是否有定义。因此，**不要使用`with`语句**。

## 相等和严格相等

JavaScript 有两个表示相等的运算符：”相等“（`==`）和”严格相等“（`===`）。

相等运算符会自动转换变量类型，造成很多意想不到的情况。

```
0 == ''// true
1 == true // true
2 == true // false
0 == '0' // true
false == 'false' // false
false == '0' // true
' \t\r\n ' == 0 // true
```

因此，**建议不要使用相等运算符（`==`），只使用严格相等运算符（`===`）**。

## 语句的合并

有些程序员追求简洁，喜欢合并不同目的的语句。比如，原来的语句是

```
a = b;
if (a) {
  // ...
}
```
他喜欢写成下面这样。
```
if (a = b) {
  // ...
}
```
上面代码是将`a = b`这个赋值语句放在了`if`这个判断语句中。

虽然语句少了一行，但是可读性大打折扣，而且会造成误读，让别人误解这行代码的意思是下面这样。

```
if （a === b）{
  // ...
}
```
建议不要将不同目的的语句，合并成一行。

## 自增和自减运算符

自增（`++`）和自减（`--`）运算符，**放在变量的前面或后面，返回的值不一样**，很容易发生错误。事实上，所有的`++`运算符都可以用`+= 1`代替。

```
++x
// 等同于
x += 1;
```
改用`+= 1`，代码变得更清晰了。

**建议自增（`++`）和自减（`--`）运算符尽量使用`+=`和`-=`代替**。

## switch…case 结构

`switch...case`结构要求，在每一个`case`的最后一行必须是`break`语句，否则会接着运行下一个`case`。这样不仅容易忘记，还会造成代码的冗长。

而且，`switch...case`不使用大括号，不利于代码形式的统一。此外，这种结构类似于`goto`语句，容易造成程序流程的混乱，使得代码结构混乱不堪，不符合面向对象编程的原则。

```
function doAction(action) {
  switch (action) {
    case 'hack':
      return 'hack';
      break;
    case 'slash':
      return 'slash';
      break;
    case 'run':
      return 'run';
      break;
    default:
      throw new Error('Invalid action.');
  }
}
```
上面的代码建议改写成对象结构。

```
function doAction(action) {
  var actions = {
    'hack': function () {
      return 'hack';
    },
    'slash': function () {
      return 'slash';
    },
    'run': function () {
      return 'run';
    }
  };

  if (typeof actions[action] !== 'function') {
    throw new Error('Invalid action.');
  }

  return actions[action]();
}
```
上面的代码中最后返回的`actions[action]`如果不加`()`则是一个函数，加了`()`代表调用返回的函数。

因此，**建议`switch...case`结构可以用对象结构代替**。













