---
title: JavaScript标准参考教程》阅读笔记之 《DOM模型》
date: 2018-05-21 22:32:33
tags: "JavaScript"
categories: "JavaScript标准教程笔记"
---

> 整理自阮一峰《JavaScript标准参考教程》，原作者为阮一峰，[github地址](https://github.com/ruanyf/jstutorial)

# DOM 模型概述

## 基本概念

### DOM

**DOM 是 JavaScript 操作网页的接口，全称为“文档对象模型”（Document Object Model）。它的作用是将网页转为一个 JavaScript 对象，从而可以用脚本进行各种操作（比如增删内容）**。

浏览器会根据 DOM 模型，将结构化文档（比如 HTML 和 XML）解析成一系列的节点，再由这些节点组成一个树状结构（DOM Tree）。所有的节点和最终的树状结构，都有规范的对外接口。

DOM 只是一个接口规范，可以用各种语言实现。所以**严格地说，DOM 不是 JavaScript 语法的一部分，但是 DOM 操作是 JavaScript 最常见的任务，离开了 DOM，JavaScript 就无法控制网页。另一方面，JavaScript 也是最常用于 DOM 操作的语言**。后面介绍的就是 JavaScript 对 DOM 标准的实现和用法。

### 节点

**DOM 的最小组成单位叫做节点（node）。文档的树形结构（DOM 树），就是由各种不同类型的节点组成。**每个节点可以看作是文档树的一片叶子。

节点的类型有七种。

> `Document`：整个文档树的顶层节点
> `DocumentType`：doctype标签（比如<!DOCTYPE html>）
> `Element`：网页的各种HTML标签（比如<body>、<a>等）
> `Attribute`：网页元素的属性（比如class="right"）
> `Text`：标签之间或标签包含的文本
> `Comment`：注释
> `DocumentFragment`：文档的片段

浏览器提供一个原生的节点对象`Node`，上面这七种节点都继承了`Node`，因此具有一些共同的属性和方法。

### 节点树

**一个文档的所有节点，按照所在的层级，可以抽象成一种树状结构。这种树状结构就是 DOM 树。**它有一个顶层节点，下一层都是顶层节点的子节点，然后子节点又有自己的子节点，就这样层层衍生出一个金字塔结构，倒过来就像一棵树。

**<font color="red">浏览器原生提供`document`节点，代表整个文档</font>**。

```
document
// 整个文档树
```

**<font color="red">文档的第一层只有一个节点，就是 HTML 网页的第一个标签`<html>`</font>**，它构成了树结构的根节点（root node），其他 HTML 标签节点都是它的下级节点。

除了根节点，其他节点都有三种层级关系。

> 父节点关系（parentNode）：直接的那个上级节点
> 子节点关系（childNodes）：直接的下级节点
> 同级节点关系（sibling）：拥有同一个父节点的节点

DOM 提供操作接口，用来获取这三种关系的节点。比如，子节点接口包括`firstChild`（第一个子节点）和`lastChild`（最后一个子节点）等属性，同级节点接口包括`nextSibling`（紧邻在后的那个同级节点）和`previousSibling`（紧邻在前的那个同级节点）属性。

## Node 接口的属性

所有 DOM 节点都继承了 `Node` 接口，拥有一些共同的属性和方法。这是 DOM 操作的基础。

### Node.nodeType

**`nodeType`属性返回一个整数值，表示节点的类型**。

```
document.nodeType // 9
```
上面代码中，文档节点的类型值为9。

Node 对象定义了几个常量，对应这些类型值。

```
document.nodeType === Node.DOCUMENT_NODE // true
```
上面代码中，文档节点的`nodeType`属性等于常量`Node.DOCUMENT_NODE`。

不同节点的`nodeType`属性值和对应的常量如下。

> 文档节点（document）：9，对应常量`Node.DOCUMENT_NODE`
> 元素节点（element）：1，对应常量`Node.ELEMENT_NODE`
> 属性节点（attr）：2，对应常量`Node.ATTRIBUTE_NODE`
> 文本节点（text）：3，对应常量`Node.TEXT_NODE`
> 文档片断节点（DocumentFragment）：11，对应常量`Node.DOCUMENT_FRAGMENT_NODE`
> 文档类型节点（DocumentType）：10，对应常量`Node.DOCUMENT_TYPE_NODE`
> 注释节点（Comment）：8，对应常量`Node.COMMENT_NODE`

确定节点类型时，使用`nodeType`属性是常用方法。

```
var node = document.documentElement.firstChild;
if (node.nodeType === Node.ELEMENT_NODE) {
  console.log('该节点是元素节点');
}
```

### Node.nodeName

**`nodeName`属性返回节点的名称**。

```
// HTML 代码如下
// <div id="d1">hello world</div>

var div = document.getElementById('d1');
div.nodeName // "DIV"
```
上面代码中，元素节点`<div>`的`nodeName`属性就是大写的标签名`DIV`。

不同节点的`nodeName`属性值如下。

> 文档节点（document）：`#document`
> 元素节点（element）：大写的标签名
> 属性节点（attr）：属性的名称
> 文本节点（text）：`#text`（这里就是`#text`，`text`不用于替换）
> 文档片断节点（DocumentFragment）：`#document-fragment`
> 文档类型节点（DocumentType）：文档的类型
> 注释节点（Comment）：`#comment`

### Node.nodeValue（可读写）

**`nodeValue`属性返回一个字符串，表示当前节点本身的文本值，该属性可读写**。

<font color="red">只有文本节点（text）和注释节点（comment）有文本值，因此这两类节点的`nodeValue`可以返回结果，其他类型的节点一律返回`null`</font>。同样的，也只有这两类节点可以设置`nodeValue`属性的值，其他类型的节点设置无效。

```
var p = document.getElementsByTagName("p")[0];
p.nodeValue; // null

// HTML 代码如下
// <div id="d1">hello world</div>
var div = document.getElementById('d1');
div.nodeValue // null
div.firstChild.nodeValue // "hello world"
```
上面代码中，`div`是元素节点，`nodeValue`属性返回`null`。<font color="red">`div.firstChild`是文本节点，所以可以返回文本值</font>。

### Node.textContent（可读写）

**`textContent`属性返回<font color="red">当前节点和它的所有后代节点</font>的<font color="red">文本内容</font>**。

```
// HTML 代码为
// <div id="divA">This is <span>some</span> text</div>

document.getElementById('divA').textContent
// This is some text
```

`textContent`属性**<font color="red">自动忽略当前节点内部的 HTML 标签，返回所有文本内容</font>**。

**该属性是可读写的，设置该属性的值，会用一个新的文本节点，替换所有原来的子节点**。它还有一个好处，就是自动对 HTML 标签转义。这很适合用于用户提供的内容。

```
document.getElementById('foo').textContent = '<p>GoodBye!</p>';
```
上面代码在插入文本时，会将`<p>`标签解释为文本，而不会当作标签处理。

对于文本节点（text）和注释节点（comment），`textContent`属性的值与`nodeValue`属性相同。对于其他类型的节点，该属性会将每个子节点的内容连接在一起返回，但是不包括注释节点。如果一个节点没有子节点，则返回空字符串。

文档节点（document）和文档类型节点（doctype）的`textContent`属性为`null`。**<font color="red">如果要读取整个文档的内容，可以使用`document.documentElement.textContent`</font>**。

### Node.baseURI

**`baseURI`属性返回一个字符串，表示当前网页的绝对路径**。浏览器根据这个属性，计算网页上的相对路径的 URL。该属性为只读。

```
// 当前网页的网址为
// http://www.example.com/index.html
document.baseURI
// "http://www.example.com/index.html"

// 当前网页的网址为
// http://javascript.ruanyifeng.com/dom/node.html#toc0
document.baseURI
// "http://javascript.ruanyifeng.com/dom/node.html#toc0"
```

**如果无法读到网页的 URL，`baseURI`属性返回`null`**。

该属性的值一般由当前网址的 URL（即`window.location`属性）决定，但是可以使用 HTML 的`<base>`标签，改变该属性的值。

```
// 在HTML代码中任意位置插入下面的代码
// <base href="http://www.baidu.com">

document.baseURI;
// "https://www.baidu.com/"
```
设置了以后，`baseURI`属性就返回`<base>`标签设置的值。

### Node.ownerDocument

**`Node.ownerDocument`属性返回当前节点所在的顶层文档对象，即`document`对象。**

```
var d = p.ownerDocument;
d === document // true
```

`document`对象本身的`ownerDocument`属性，返回`null`。

### Node.nextSibling

**`Node.nextSibling`属性返回紧跟在当前节点后面的第一个同级节点。如果当前节点后面没有同级节点，则返回`null`。**

```
// HTML 代码如下
// <div id="d1">hello</div><div id="d2">world</div>

var div1 = document.getElementById('d1');
var div2 = document.getElementById('d2');

d1.nextSibling === d2 // true
```
上面代码中，`d1.nextSibling`就是紧跟在`d1`后面的同级节点`d2`。

注意，**<font color="red">该属性还包括文本节点和评论节点。因此如果当前节点后面有空格，该属性会返回一个文本节点，内容为空格</font>**。

`nextSibling`属性可以用来遍历所有子节点。

```
var el = document.getElementById('div1').firstChild;

while (el !== null) {
  console.log(el.nodeName);
  el = el.nextSibling;
}
```
上面代码遍历`div1`节点的所有子节点。

### Node.previousSibling

**`previousSibling`属性返回当前节点前面的、距离最近的一个同级节点。如果当前节点前面没有同级节点，则返回`null`**。

```
// HTML 代码如下
// <div id="d1">hello</div><div id="d2">world</div>
var div1 = document.getElementById('d1');
var div2 = document.getElementById('d2');

d2.previousSibling === d1 // true
```
上面代码中，`d2.previousSibling`就是`d2`前面的同级节点`d1`。

注意，**<font color="red">该属性还包括文本节点和评论节点。因此如果当前节点前面有空格，该属性会返回一个文本节点，内容为空格</font>**。

### Node.parentNode

**`parentNode`属性返回当前节点的父节点。对于一个节点来说，它的父节点只可能是三种类型：元素节点（element）、文档节点（document）和文档片段节点（documentfragment）**。

```
if (node.parentNode) {
  node.parentNode.removeChild(node);
}
```
上面代码中，通过`node.parentNode`属性将`node`节点从文档里面移除。

**文档节点（document）和文档片段节点（documentfragment）的父节点都是`null`。另外，对于那些生成后还没插入 DOM 树的节点，父节点也是`null`。**

### Node.parentElement

**`parentElement`属性返回当前节点的父元素节点。如果当前节点没有父节点，或者父节点类型<font color="red">不是元素节点</font>，则返回`null`**。

```
if (node.parentElement) {
  node.parentElement.style.color = 'red';
}
```
上面代码中，父元素节点的样式设定了红色。

由于父节点只可能是三种类型：元素节点、文档节点（document）和文档片段节点（documentfragment）。**`parentElement`属性相当于把后两种父节点都排除了**。

### Node.firstChild，Node.lastChild

**`firstChild`属性返回当前节点的第一个子节点，如果当前节点没有子节点，则返回`null`**。

```
// HTML 代码如下
// <p id="p1"><span>First span</span></p>
var p1 = document.getElementById('p1');
p1.firstChild.nodeName // "SPAN"
```
上面代码中，`p`元素的第一个子节点是`span`元素。

注意，**<font color="red">`firstChild`返回的除了元素节点，还可能是文本节点或评论节点</font>**。

```
// HTML 代码如下
// <p id="p1">
//   <span>First span</span>
//  </p>

var p1 = document.getElementById('p1');
p1.firstChild.nodeName // "#text"
```
上面代码中，`p`元素与`span`元素之间有空白字符，这导致`firstChild`返回的是文本节点。

**`lastChild`属性返回当前节点的最后一个子节点，如果当前节点没有子节点，则返回`null`。用法与`firstChild`属性相同**。

### Node.childNodes

**`childNodes`属性返回一个类似数组的对象（`NodeList`集合），成员包括当前节点的所有子节点。成员可以为空。**

```
var children = document.querySelector('ul').childNodes;
```
上面代码中，`children`就是`ul`元素的所有子节点。

使用该属性，可以遍历某个节点的所有子节点。

```
var div = document.getElementById('div1');
var children = div.childNodes;

for (var i = 0; i < children.length; i++) {
  // ...
}
```
上面代码中可以使用`children`的`length`属性是因为`children`是一个类似数组的对象，是具备`length`属性的。

**文档节点（document）就有两个子节点：文档类型节点（docType）和 HTML 根元素节点。**

```
var children = document.childNodes;
for (var i = 0; i < children.length; i++) {
  console.log(children[i].nodeType);
}
// 10
// 1
```
上面代码中，文档节点的第一个子节点的类型是10（即文档类型节点），第二个子节点的类型是1（即元素节点）。

注意，**<font color="red">除了元素节点，`childNodes`属性的返回值还包括文本节点和注释节点。如果当前节点不包括任何子节点，则返回一个空的`NodeList`集合</font>**。

**<font color="red">由于`NodeList`对象是一个动态集合，一旦子节点发生变化，立刻会反映在返回结果之中</font>**。

### Node.isConnected

**`isConnected`属性返回一个布尔值，表示当前节点是否在文档之中**。

```
var test = document.createElement('p');
test.isConnected // false

document.body.appendChild(test);
test.isConnected // true
```
上面代码中，`test`节点是脚本生成的节点，没有插入文档之前，`isConnected`属性返回`false`，插入之后返回`true`。

## 


























