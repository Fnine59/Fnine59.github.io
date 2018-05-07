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