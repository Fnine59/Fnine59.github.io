---
title: 《You don't know JS》——作用域和闭包
date: 2018-09-25 14:28:55
tags: "JavaScript"
categories: "You don't know JS"
---

	本文来自《You don't know JS》一书，所用引用内容禁止商用。

## 内容概述

本书上卷的第一部分主要讲解了作用域和闭包。作者深入解析了作用域、编译器以及引擎在JavaScript代码运行前及运行时具体做了哪些事情，在读者对各部分的职责有所了解之后，深入讲解了JavaScript中的各种作用域以及JavaScript中的提升，最终在读者对作用域有了深入的理解之后，引出了闭包。这一部分在内容安排上层层递进，前一部分为后一部分的理解做了很好的铺垫，有助于读者真正理解类似“提升”、“闭包”等概念的本质。

这一部分的英语原文见[github地址](https://github.com/getify/You-Dont-Know-JS)。

## 引擎、编译器与作用域

### 关于JavaScript

首先要明确的一个概念是，尽管通常将JavaScript归类为“动态”或“解释执行”语言，但JavaScript实际上是一门编译语言，但它与传统的编译语言不同，JavaScript不是提前编译的，编译结果也不能在分布式系统中进行移植。

尽管如此，JavaScript引擎进行编译的步骤和传统的编译语言非常相似。

通常来讲，传统编译语言的流程分为三部分：分词/词法分析、解析/语法分析、代码生成。与传统编译过程相比，JavaScript引擎要复杂的多，例如在语法分析和代码生成阶段有特定的步骤来对运行性能进行优化。对于JavaScript来说，大部分情况下编译发生在代码执行前的几微秒内。

### 引擎、编译器与作用域

现有一句代码`var a = 2`，现针对这句代码梳理一下JavaScript执行的过程以及引擎、编译器和作用域在这个过程中发挥的作用。

首先来看一下这三部分在代码执行过程中的职责：

- 引擎：从头到尾负责整个JavaScript程序的编译及执行过程。
- 编译器：负责语法分析及代码生成等。
- 负责收集并维护由所有声明的标识符（变量）组成的一系列查询，并实施一套非常严格的规则，确定当前执行的代码对这些标识符的访问权限。

理解了这三部分的具体职责之后，可以想到代码`var a = 2`的执行过程如下：

首先，这段代码会分为两部分进行处理，一部分（`var a`）由编译器在编译时处理，另一部分(`a = 2`)则由引擎在运行时处理。

遇到`var a`，编译器会询问作用域是否已经有一个该名称的变量存在于同一个作用域的集合中。如果是，编译器会忽略该声明，继续进行编译；否则它会要求作用域在当前作用域的集合中声明一个新的变量，并命名为`a`。

接下来编译器会为引擎生成运行时所需的代码，这些代码用于处理`a = 2`这个赋值操作。而引擎运行时会首先询问作用域，在当前作用域集合中是否存在一个叫作`a`的变量。如果是，引擎就会使用这个变量；如果否，引擎会继续向上级作用域询问查找该变量，直到抵达最外层的作用域（也就是全局作用域）为止。

如果引擎最终找到了`a`变量，就会将2赋值给它；否则就会抛出异常。

### LHS与RHS

在上面的案例中，引擎会针对变量`a`进行查询，这种查询实际上是在查找`a`这个“容器”并为其赋值，我们将这种查询称为LHS查询。而有了LHS，不难想到还有一种查询称为RHS，那么“L”和“R”指代的是什么呢？

    是一个赋值操作的左侧和右侧。

需要注意的是，这里的“赋值操作”不只是一个`=`那么简单，代码中还会存在一些隐式的赋值操作，比如函数的形参。

下面详细的解释一下LHS和RHS。

当变量出现在赋值操作的左侧时进行LHS查询，出现在非左侧时进行RHS查询。换句话说，**RHS查询与简单地查找某个变量的值别无二致，而LHS查询则是视图找到变量的容器本身，从而可以对其赋值**。

举一个RHS的例子：`console.log(a);`。这句代码中对`a`的引用就是一个RHS引用，因为这里a并没有被赋予任何值。

### 异常

为什么说区分LHS和RHS是十分重要的，这是因为这两种查询如果出现错误，相应的处理方式是不同的。

如果RHS查询在所有嵌套的作用域中找不到所需的变量，引擎就会抛出一个`ReferenceError`异常。

而当引擎执行LHS查询时，如果在顶层（全局作用域）中也无法找到目标变量，全局作用域中会创建一个具有该名称的变量，并将其返还给引擎，前提是程序运行在非严格模式下。而如果是严格模式下，严格模式禁止自动或隐式地创建全局变量，因此在严格模式中LHS查询失败时，引擎会抛出同RHS查询失败时类似的`ReferenceError`异常。

还有一种异常情况是，如果RHS找到了一个变量，但是代码中尝试对这个变量的值进行不合理的操作，比如试图对一个非函数类型的值进行函数调用，那么引擎会抛出一个`TypeError`异常。


### 作用域的嵌套

上文中提到，如果引擎在当前作用域内找不到需要的变量，就会继续向上级作用域询问查找该变量。

这里有一个小技巧，我们可以将作用域想象成几个**逐级包含的气泡**。没有任何函数的气泡可以（部分地）同时出现在两个外部作用域的气泡中，就如同没有任何函数可以部分地同时出现在两个父级函数中一样。

还有一点要注意的是，作用域查找会在找到第一个匹配的标识符时停止。在多层的嵌套作用域中可以定义同名的标识符，这叫作“遮蔽效应”，内部的标识符“遮蔽”了外部的标识符。关于全局变量，我们可以通过`window.xxx`来访问被同名变量所遮蔽的全局变量。

## 词法作用域

### 词法作用域的简介

简单地说，词法作用域就是定义在词法阶段的作用域。换句话说，词法作用域是由你在写代码时将变量和块作用域写在哪里来决定的。**无论函数在哪里被调用，也无论它如何被调用，它的词法作用域都只由函数被<font color="red">声明</font>时所处的位置决定**。

让词法作用域根据词法关系保持书写时的自然关系不变，是一个非常好的最佳实践。

词法作用域查找只会查找一级标识符，类似`foo.bar.baz`，词法作用域只会试图查找`foo`标识符，找到这个变量后，对象属性访问规则会分别接管对`bar`和`baz`属性的访问。

### 欺骗词法作用域

如果词法作用域完全由写代码期间函数所声明的位置来定义，怎样才能在运行时来“修改”词法作用域呢？

JavaScript中有两种机制来实现这个目的。但一定要注意的是，**欺骗词法作用域会导致性能下降**。

这两种机制分别是`eval()`函数和`with`关键字。

`eval()`函数的原理是，它接受一个字符串作为参数，将其中的内容视为好像在书写时就存在于程序中这个位置的代码。案例如下。

```
function foo(str, a) {
    eval(str); // 欺骗
    console.log(a, b);
}
var b = 2;

foo( "var b = 3;", 1); // 1, 3
```

默认情况下，如果`eval()`中所执行的代码包含有一个或多个声明（无论是变量还是函数），就会对`eval()`所处的词法作用域进行修改。

在严格模式下，`eval()`在运行时有自己的词法作用域，意味着其中的声明无法修改所在的作用域。

JavaScript中还有其他一些功能效果和`eval()`很相似，`setTimeout()`和`setInterval()`的第一个参数可以是字符串，字符串的内容可以被解释为一段动态生成的函数代码。这些功能也已经过时并且不被提倡，不要使用它们。`new Function()`函数的行为也很类似，最后一个参数可以接受代码字符串，并将其转化为动态生成的函数（前面的参数是这个新生成函数的形参）。这种构建函数的语法比`eval()`略微安全一些，但也要尽量避免使用。

`with`的原理是，它可以将一个没有或有多个属性的对象处理为一个完全隔离的词法作用域，因此这个对象的属性也会被处理为定义在这个作用域中的词法标识符。

需要注意的是，尽管`with`块可以将一个对象处理为词法作用域，但这个块内部正常的`var`声明并不会被限制在这个块的作用域中，而是被添加到`with`所处的函数作用域中。

案例如下：

```
var obj = {
    a: 1,
    b: 2,
    c: 3
}

with (obj){
    a = 3;
    b = 4;
    c = 5;
}
```

在严格模式下，`with`会被完全禁止。

上面的两种方案都极度不推荐使用，原因有三。

- 安全性过低
- 会受到严格模式的影响
- 降低性能

关于性能方面作些解释，JavaScript引擎会在编译阶段进行数项的性能优化。其中有些优化依赖于能够根据代码的词法进行静态分析，并预先确定所有变量和函数的定义位置，才能在执行过程中快速找到标识符。但如果引擎在代码中发现了`eval()`和`with`，它只能简单地假设关于标识符位置的判断都是无效的，因为它无法知道`eval()`和`with`会对词法作用域作出什么修改。最悲观的情况是，如果出现了`eval()`和`with`，所有的优化可能都是无意义的，因此最简单的做法就是完全不做任何优化。

## 函数作用域和块作用域

### 函数作用域

函数作用域的含义是指，属于这个函数的全部变量都可以在整个函数的范围内使用及复用（事实上在嵌套的作用域中也可以使用）。

关于函数作用域，因为概念比较清楚，这里不再过多赘述。

目前来讲需要注意的一点是，函数的形参也是归属于当前函数作用域范围内的。

### 关于隐藏

软件设计中有这样一个原则，我们应该最小限度地暴露必要内容，而将其他内容都“隐藏”起来。

这个原则，通俗点说，就是某个函数中本应该私有的变量，就不要将其暴露在外部作用域中。具体思想类似于Java中的`getter`和`setter`，以及`private`变量等。

“隐藏”还能够带来一个好处，就是可以避免同名标识符之间的冲突。两个标识符可能具有相同的名字但用途却不一样，无意间可能造成命名冲突，而冲突会导致变量的值被意外覆盖。

从很多第三方的库中可以看出这一点，通常来讲，这些库会在全局作用域中声明一个名字足够独特的变量，通常是一个对象。这个对象被用作库的命名空间，所有需要暴露给外界的功能都会成为学这个对象（命名空间）的属性，而不是将自己的标识符暴露在顶级的词法作用域中。

### 函数声明与函数表达式

区分函数声明和函数表达式最简单的方法是看`function`关键字出现在声明中的位置（不仅仅是一行代码，而是整个声明中的位置）。如果`function`是声明中的第一个词，那么就是一个函数声明，否则就是一个函数表达式。它们之间最重要的区别就是名称标识符会绑定在何处，函数声明会将名称标识符绑定在所在作用域中，而函数表达式则会将名称标识符绑定在函数表达式自身的函数中而不是所在作用域中，被隐藏在自身中意味着不会非必要地污染外部作用域。

### 函数的匿名和具名

本书的作者不推荐使用匿名函数，原因有三：

1. 匿名函数在栈追踪中不会显示出有意义的函数名，使得调试很困难。
2. 如果没有函数名，当函数需要引用自身时只能使用已经过期的arguments.callee引用，比如在递归中。另一个函数需要引用自身的例子，是在事件触发后事件监听器需要解绑自身。
3. 匿名函数省略了对于代码可读性/可理解性很重要的函数名。一个描述性的名称可以让代码不言自明。

作者根据以上观点，认为具名函数优于匿名函数，但匿名函数书写起来也确实简单快捷，很多库和工具也倾向于鼓励使用匿名函数风格的代码，个人习惯来讲比起过多的具名函数，也更喜欢匿名函数多一些，因此具体使用匿名函数还是具名函数有待进一步考究、积累与确认。

### IIFE

IIFE，代表立即执行函数表达式。

IIFE通常使用匿名函数表达式，但本书作者认为使用具名函数的IIFE也是一个值得推广的实践。

IIFE的原理是，由于函数被包含在一对`( )`内部，因此成为了一个表达式，通过在末尾加上另外一个`( )`可以立即执行这个函数。

关于IIFE有一个注意点，`(function(){ ... })()`和`(function(){ ... }())`这两种写法在功能上是一致的，选择哪种全凭个人喜好。

### 块作用域

#### try/catch

在使用`var`声明变量的时候，它写在哪里都是一样的，因为它们最终都会属于外部作用域。表面上来看，JavaScript似乎没有块作用域的相关功能。

但非常少人会注意到，JavaScript的ES3规范中规定`try/catch`的`catch`分句会创建一个块作用域，其中声明的变量仅在`catch`内部有效。也因此，它可以在ES6之前的环境中作为块作用域的替代方案。举例如下：

```
{
    try{
        throw undefined;
    } catch(a){
        a = 2;
        console.log(a); // 2
    }
}
console.log(a); // undefined
```

最后简单地看一下`try/catch`带来的性能问题。

首先，`try/catch`的性能的确很糟糕，但技术层面上没有合理的理由来说明它必须这么慢。从ES6转换器开始使用`try/catch`后，Traceur团队已经要求Chrome对`try/catch`的性能进行改进。其次，如果想要用IIFE来替代`try/catch`，但会涉及到一些问题，如果将一段代码中的任意一部分拿出来用函数进行包裹，会改变这段代码的含义，其中的`this`、`return`、`break`、`continue`都会发生变化。IIFE并不是一个普适的解决方案，它只适合在某些情况下进行手动操作。

#### with

除了`try/catch`块之外，`with`关键字也会创建一个块作用域。

#### ES6中的let和const

ES6中的`let`关键字可以将变量绑定到所在的任意作用域中（通常是`{ ... }`内部）。

而除了`let`之外，ES6还引入了`const`，同样可以用来创建块作用域变量，但其值是固定的（常量），之后任何视图修改值的操作都会引起错误。

这里需要理解的一点是，`let`和`const`并不是形成了一个块作用域，而是将变量绑定到了所在的块作用域中，换句话说，`let`和`const`为其声明的变量隐式地劫持了所在的块作用域。

## 提升

至此，有了块作用域这个概念之后，其实函数作用域和块作用域的行为是一样的，因此可以总结为：**任何声明在某个作用域内的变量，都将附属于这个作用域**。

这里，我们从原理层面深入的理解一下“变量提升”。

首先回顾一下编译阶段，编译阶段中的一部分工作就是找到所有的声明，并用合适的作用域将它们关联起来。因此，我们可以这样考虑，包括变量和函数在内的所有声明都会在任何代码被执行前**首先被处理**。

举个例子，当看到`var a = 2`时，我们可能会认为这是一个声明，但JavaScript实际上会将其看成两个声明：`var a`和`a = 2`，第一个定义声明是在编译阶段进行的，第二个赋值声明会被留在原地等待执行阶段。因此，这个过程就好像变量和函数声明从它们在代码中出现的位置被“移动”到了最上面面，这个过程就叫作提升。

一定要注意的是，**只有声明本身会被提升，而赋值或其他运行逻辑会留在原地**。因此区分好声明和赋值是十分重要的，对于函数来说，区分好是函数声明还是函数表达式也是十分重要的，看下面的例子。

```
foo();

function foo() {
    console.log(a); // undefined
    var a = 2;
}
```

上面的代码中，`foo()`是可以正常执行的，这是因为`function foo()`是一个函数**声明**，被提升了。另外值得注意的是，每个作用域都会进行提升操作，上面的代码中除了函数声明`function foo()`被提升之外，对变量`a`的声明也被提升到该函数作用域的顶部了。因此打印`a`出现的是`undefined`，而不是`ReferenceError`（RHS查询未找到该值时抛出的异常）。

捋清了上面的代码之后，我们再来看下面的代码。

```
foo(); // 不是ReferenceError，而是TypeError

var foo = function bar() {
    // ...
}
```

这段代码中，函数并不是使用函数声明的方式，而是使用了函数表达式。可以发现函数表达式是没有被提升的。`foo()`能够被正常调用，没有返回`ReferenceError`，说明`foo()`找到了声明好的变量，这个变量就是下面的`var foo`部分，但`foo()`这句代码抛出了`TypeError`异常，通过前面的异常部分我们得知`TypeError`异常是试图对某个值进行非法操作，这里的非法操作就是试图对`undefined`进行函数调用，因此可以得知，函数表达式是不会被提升的。

### 函数优先

函数声明和变量声明都会被提升，但是一个值得注意的细节是，函数会首先被提升，然后才是变量。

如果同时出现一个函数声明和一个变量声明，而该函数与该变量同名，那么函数声明会被首先提升，提升变量声明时会判定该声明为重复声明因而忽略。如果再出现下一个同名的函数声明，则该函数声明会覆盖掉之前的函数声明。

因此，这也说明了在同一个作用域中进行重复定义是非常糟糕的，而且经常会导致各种奇怪的问题，不要这样使用。

### 小结

提升的本质：JavaScript是一门编译语言，它的编译过程是在执行之前，JavaScript的编译器负责语法分析及代码生成，在这个过程中，有一部分工作就是需要找到所有的声明，并将它们联系到作用域（词法作用域）上。因此，JavaScript代码中的所有声明都会被首先处理，而这一过程看起来就像是声明部分的代码被提升到各自作用域的顶端。

`var a = 2`这一句代码分为两部分：

- `var a`声明部分，会被提升（首先处理）
- `a = 2`赋值部分，不会被提升（留在原地）

`function a() { ... }`是函数声明，会被提升；
`(function a() { ... })`是函数表达式，不会被提升。

## 闭包

当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

下面我们来看一段代码，清晰地展示了闭包：
```
function foo() {
    var a = 2;
    
    function bar() {
        console.log(a);
    }
    
    return bar;
}

var baz = foo();

baz(); // 2
```

我们看到，函数`bar()`的词法作用域能够访问`foo()`的内部作用域，然后我们将`bar()`函数本身当做一个值类型进行传递。当我们调用`baz()`时，实际上只是通过不同的标识符引用调用了内部的函数`bar()`。

在`foo()`执行后，通常会期待`foo()`的整个内部作用域都被销毁，因为我们知道引擎有垃圾回收器来释放不再使用的内存空间，但“闭包”则可以阻止这件事情的发生，事实上内部作用域依然存在，而在使用这个内部作用域导致其一直存活的就是`bar()`。换句话说，因为`bar()`声明的位置特殊，它拥有涵盖`foo()`内部作用域的闭包，从而使得该作用域能够一直存活，以供`bar()`在之后任何时间进行引用。

`bar()`依然持有对该作用域的引用，而这个引用就叫作闭包。

当然，由此我们可以想到，无论使用何种方式对函数类型的值进行传递，当函数在别处被调用时都可以观察到闭包。即，**无论通过何种手段将内部函数传递到所在的词法作用域以外，它都会持有对原始定义作用域的引用，无论在何处执行这个函数都会使用闭包**。本质上，无论何时何地，如果将（访问它们各自词法作用域的）函数到处传递，就可以看到闭包在这些函数中的应用。在定时器、事件监听器、Ajax请求、跨窗口通信、Web Workers或者任何其他的异步（或同步）任务中，只要使用了回调函数，实际上就是在使用闭包！

### 闭包与IIFE

通常认为IIFE是典型的闭包例子，但作者认为严格来讲它并不是闭包。因为IIFE并不是在它本身的作用域以外执行的，它在定义时所在的作用域中执行。

作者认为，尽管IIFE本身并不是观察闭包的恰当例子，但它的确创建了闭包，并且也是最常用来创建可以被封闭起来的闭包的工具。因此IIFE的确同作用域息息相关，即时它本身并不会真的创建作用域。

### 闭包与循环

看下面的代码。

```
for (var i=1; i<=5; i++){
    setTimeout(function timer(){ // 这里是按照作者推荐的具名函数的方式使用的
        cosole.log(i);
    }, i*1000);
}
```

看到这段代码，我们的预期是分别输出数字1~5，每秒一次，每次一个。但实际上，这段代码会以每秒一次的频率输出五次6。

这是因为，根据作用域的工作原理，实际情况是尽管循环中的五个函数是在各个迭代中分别定义的，但是它们都被封闭在一个共享的全局作用域中，因此实际上只有一个`i`。因此，实际上所有函数共享一个`i`的引用，而循环结构让我们误认为背后还有更复杂的机制在起作用，但其实没有。如果将延迟函数的回调重复定义五次，完全不使用循环，那它同这段代码是完全等价的。

因此，我们需要借助闭包，为每一次循环赋予一个独立的作用域，也就是赋予每次循环一个独立的`i`。

```
for (var i=1; i<=5; i++){
    (function() {
        setTimeout(function timer(){ // 这里是按照作者推荐的具名函数的方式使用的
            cosole.log(i);
        }, i*1000);
    })();
}
```

那么上面这段代码可行吗？

答案是否定的，这是因为我们的IIFE只是一个什么都没有的空座用于，它需要有自己的变量，用来在每个迭代中储存`i`的值才可以为我们所用。

```
for (var i=1; i<=5; i++){
    (function() {
        var j = i;
        setTimeout(function timer(){ // 这里是按照作者推荐的具名函数的方式使用的
            cosole.log(j);
        }, j*1000);
    })();
}
```

这段代码就可以正常工作了，我们可以再进行一些优化。

```
for (var i=1; i<=5; i++){
    (function(j) {
        setTimeout(function timer(){ // 这里是按照作者推荐的具名函数的方式使用的
            cosole.log(j);
        }, j*1000);
    })(i);
}
```

在迭代内使用IIFE会为每个迭代都生成一个新的作用域，使得延迟函数的回调可以将新的作用域封闭在每个迭代内部，每个迭代中都会含有一个具有正确值的变量供我们访问。

但其实这段代码还可以更简洁。

本质上我们在做的事情是，将一个块转换为一个可以被关闭的作用域，这就会让我们联想到之前介绍过的`let`声明，它可以用来劫持块作用域，并在这个块作用域中声明一个变量。

```
funtion (let i=1; i<=5; i++){
    setTimeout(function timer(){ // 这里是按照作者推荐的具名函数的方式使用的
        cosole.log(i);
    }, i*1000);
}
```

上面这段代码，完全可以实现我们的预期，但并没有过多冗余代码，只是将`var`换为`let`即可。

### 闭包与模块

模块模式需要具备两个必要条件：

1. 必须有外部的封闭函数，该函数必须至少被调用一次（每次调用都会创建一个新的模块实例）。
2. 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态。

根据上面的定义，我们可以联想到，一个从函数调用所返回的，只有数据属性而没有闭包函数的对象并不是真正的模块。

## 动态作用域

我们知道JavaScript中的作用域是词法作用域，而与之相对的是动态作用域，这里大致介绍一些动态作用域与词法作用域的区别。

主要的区别在于：词法作用域是在写代码或者说定义时确定的，而动态作用域时在运行时确定的。**词法作用域关注函数在何处声明，而动态作用域关注函数从何处调用**。

JavaScript中的`this`机制，某种程度上很像动态作用域。

## 关于this

这里简单的提一下`this`，之后的第二部分还会有详细的说明。

首先需要明确的一点是，**`this`是在运行时确定指向的**。

我们知道，ES6中添加了一个特殊的语法形式用于函数的声明，叫做箭头函数。值得注意的一点是，箭头函数在涉及`this`绑定时的行为和普通函数的行为完全不一致。它放弃了所有普通`this`绑定的规则，取而代之的是用当前的词法作用域覆盖了`this`本来的值。因此，箭头函数并非是以某种不可预测的方式同所属的`this`进行了解绑定，而只是“继承”了函数的`this`绑定。

作者认为，箭头函数将程序员们经常犯的一个错误给标准化了，也就是混淆了`this`绑定规则和词法作用域规则。但在代码中将两种风格混在一起使用，这种做法不值得提倡。因此作者更推荐使用`bind()`函数对`this`的指向进行绑定。

总的来说，ES6中的箭头函数，将原有的`this`绑定规则放弃掉了，而改为与词法作用域相同的规则，将程序员经常犯的错误标准化。`bind`函数保留了原来的`this`绑定规则，但依旧能够解决`this`指向在函数运行过程中改变的问题，因此作者更为推荐使用`bind`函数。

总之，无论以后的编程过程中使用哪种方式，心里一定要清楚箭头函数不仅仅是简化代码这么简单。