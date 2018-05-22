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

## Node 接口的方法

### Node.appendChild()

**`appendChild`方法接受一个节点对象作为参数，将其作为<font color="red">最后一个子节点</font>，插入当前节点。该方法的返回值就是插入文档的子节点**。

```
var p = document.createElement('p');
document.body.appendChild(p);
```

上面代码新建一个`<p>`节点，将其插入`document.body`的尾部。

**<font color="red">如果参数节点是 DOM 已经存在的节点，`appendChild`方法会将其从原来的位置，<font color="green">移动</font>到新位置。</font>**

```
var element = document
  .createElement('div')
  .appendChild(document.createElement('b'));
```
上面代码的返回值是`<b></b>`，而不是`<div></div>`。

如果`appendChild`方法的参数是DocumentFragment节点，那么插入的是DocumentFragment的所有子节点，而不是DocumentFragment节点本身。返回值是一个空的DocumentFragment节点。

关于DocumentFragment节点：我们经常使用Javascript来操作DOM元素，比如使用`appendChild()`方法。每次调用该方法时，浏览器都会重新渲染页面。如果大量的更新DOM节点，则会非常消耗性能，影响用户体验。JavaScript提供了一个文档片段DocumentFragment的机制。如果将文档中的节点添加到文档片段中，就会从文档树中移除该节点。把所有要构造的节点都放在文档片段中执行，这样可以不影响文档树，也就不会造成页面渲染。当节点都构造完成后，再将文档片段对象添加到页面中，这时所有的节点都会一次性渲染出来，这样就能减少浏览器负担，提高页面渲染速度。

```
// 使用DocumentFragment
var list1 = document.getElementById('list1');
console.time("time");
var fragment = document.createDocumentFragment();
for(var i = 0; i < 500000; i++){
    fragment.appendChild(document.createElement('li'));
}
list1.appendChild(fragment);
console.timeEnd('time');

// 不使用DocumentFragment
var list = document.getElementById('list');
console.time("time");
for(var i = 0; i < 500000; i++){
    list.appendChild(document.createElement('li'));
}
console.timeEnd('time');
```

循环50万次的各浏览器结果如下：
```
           　使用文档片段        　不使用文档片段
firefox        402.04ms              469.31ms
chrome         429.800ms             729.634ms
```

循环10万次的各浏览器结果如下：
```
             使用文档片段        　不使用文档片段
IE11        　　2382.15ms             2204.47ms
IE10        　　2404.239ms            2225.721ms
IE9             2373ms                 2255ms
IE8             4464ms                 4210ms
IE7             5887ms                 5394ms
```
从以上结果可以看出，如果使用IE浏览器，则使用文档片段DocumentFragment的性能并不会更好，反而变差；若使用chrome和firefox浏览器，使用文档片段DocumentFragment可以提升性能。

### Node.hasChildNodes()

**`hasChildNodes`方法返回一个布尔值，表示当前节点是否有子节点**。

```
var foo = document.getElementById('foo');

if (foo.hasChildNodes()) {
  foo.removeChild(foo.childNodes[0]);
}
```
上面代码表示，如果`foo`节点有子节点，就移除第一个子节点。

注意，**<font color="red">子节点包括所有节点，哪怕节点只包含一个空格，`hasChildNodes`方法也会返回`true`</font>**。

判断一个节点有没有子节点，有许多种方法，下面是其中的三种。

> `node.hasChildNodes()`
> `node.firstChild !== null`
> `node.childNodes && node.childNodes.length > 0`

`hasChildNodes`方法结合`firstChild`属性和`nextSibling`属性，可以遍历当前节点的所有<font color="red">后代节点</font>。

```
function DOMComb(parent, callback) {
  if (parent.hasChildNodes()) {
    for (var node = parent.firstChild; node; node = node.nextSibling) {
      DOMComb(node, callback);
    }
  }
  callback(parent);
}

// 用法
DOMComb(document.body, console.log)
```
上面代码中，`DOMComb`函数的第一个参数是某个指定的节点，第二个参数是回调函数。这个回调函数会依次作用于指定节点，以及指定节点的所有后代节点。

### Node.cloneNode()

**`cloneNode`方法用于克隆一个节点。它接受一个布尔值作为参数，表示是否同时克隆子节点。它的返回值是一个克隆出来的新节点**。

```
var cloneUL = document.querySelector('ul').cloneNode(true);
```

该方法有一些使用注意点。

1. 克隆一个节点，会拷贝该节点的所有属性，但是会丧失`addEventListener`方法和`on-`属性（即`node.onclick = fn`）添加在这个节点上的事件回调函数。

2. 该方法返回的节点不在文档之中，即没有任何父节点，必须使用诸如`Node.appendChild`这样的方法添加到文档之中。

3. 克隆一个节点之后，DOM 有可能出现两个有相同`id`属性（即`id="xxx"`）的网页元素，这时应该修改其中一个元素的`id`属性。如果原节点有`name`属性，可能也需要修改。

### Node.insertBefore()

**`insertBefore`方法用于将某个节点插入父节点内部的指定位置**。

```
var insertedNode = parentNode.insertBefore(newNode, referenceNode);
```

**`insertBefore`方法接受两个参数，第一个参数是所要插入的节点`newNode`，第二个参数是父节点`parentNode`内部的一个子节点`referenceNode`。`newNode`将插在`referenceNode`这个子节点的前面。返回值是插入的新节点`newNode`。**

```
var p = document.createElement('p');
document.body.insertBefore(p, document.body.firstChild);
```
上面代码中，新建一个`<p>`节点，插在`document.body.firstChild`的前面，也就是成为`document.body`的第一个子节点。

**<font color="red">如果`insertBefore`方法的第二个参数为`null`，则新节点将插在当前节点内部的最后位置，即变成最后一个子节点</font>**。

```
var p = document.createElement('p');
document.body.insertBefore(p, null);
```
上面代码中，`p`将成为`document.body`的最后一个子节点。这也说明`insertBefore`的第二个参数不能省略。

注意，**如果所要插入的节点是当前 DOM 现有的节点，则<font color="red">该节点将从原有的位置移除，插入新的位置</font>**。

**<font color="red">由于不存在`insertAfter`方法，如果新节点要插在父节点的某个子节点后面，可以用`insertBefore`方法结合`nextSibling`属性模拟</font>**。

```
parent.insertBefore(s1, s2.nextSibling);
```
上面代码中，`parent`是父节点，`s1`是一个全新的节点，`s2`是可以将`s1`节点，插在`s2`节点的后面。**如果`s2`是当前节点的最后一个子节点，则`s2.nextSibling`返回`null`，这时`s1`节点会插在当前节点的最后，变成当前节点的最后一个子节点，等于紧跟在`s2`的后面**。

**如果要插入的节点是`DocumentFragment`类型，那么插入的将是`DocumentFragment`的所有子节点，而不是`DocumentFragment`节点本身。返回值将是一个空的`DocumentFragment`节点**。

### Node.removeChild()

**`removeChild`方法接受一个子节点作为参数，用于从当前节点移除该子节点。返回值是移除的子节点**。

```
var divA = document.getElementById('A');
divA.parentNode.removeChild(divA);
```
上面代码移除了`divA`节点。注意，这个方法是在`divA`的父节点上调用的，不是在`divA`上调用的。

下面是如何移除当前节点的所有子节点。
```
var element = document.getElementById('top');
while (element.firstChild) {
  element.removeChild(element.firstChild);
}
```
**<font color="red">被移除的节点依然存在于内存之中，但不再是 DOM 的一部分。所以，一个节点移除以后，依然可以使用它，比如插入到另一个节点下面</font>**。

**如果参数节点不是当前节点的子节点，`removeChild`方法将报错**。

### Node.replaceChild()

**`replaceChild`方法用于将一个新的节点，替换当前节点的某一个子节点**。

```
var replacedNode = parentNode.replaceChild(newChild, oldChild);
```
上面代码中，`replaceChil`方法接受两个参数，第一个参数`newChild`是用来替换的新节点，第二个参数`oldChild`是将要替换走的子节点。**返回值是替换走的那个节点`oldChild`**。

```
var divA = document.getElementById('divA');
var newSpan = document.createElement('span');
newSpan.textContent = 'Hello World!';
divA.parentNode.replaceChild(newSpan, divA);
```
上面代码是如何将指定节点`divA`替换走。

### Node.contains()

**`contains`方法返回一个布尔值，表示参数节点是否满足以下三个条件之一**。

> 参数节点为当前节点。
> 参数节点为当前节点的子节点。
> 参数节点为当前节点的后代节点。

```
document.body.contains(node)
```
上面代码检查参数节点`node`，是否包含在当前文档之中。

注意，当前节点传入`contains`方法，返回`true`。

```
nodeA.contains(nodeA) // true
```

### Node.compareDocumentPosition()

`compareDocumentPosition`方法的用法，与`contains`方法完全一致，返回一个七个比特位的二进制值，**表示参数节点与当前节点的关系**。

<table>
	<tr>
		<td>二进制值</td>
		<td>十进制值</td>
		<td>含义</td>
	</tr>
	<tr>
		<td>000000</td>
		<td>0</td>
		<td>两个节点相同</td>
	</tr>
	<tr>
		<td>000001</td>
		<td>1</td>
		<td>两个节点不在同一个文档（即有一个节点不在当前文档）</td>
	</tr>
	<tr>
		<td>000010</td>
		<td>2</td>
		<td>参数节点在当前节点的前面</td>
	</tr>
	<tr>
		<td>000100</td>
		<td>4</td>
		<td>参数节点在当前节点的后面</td>
	</tr>
	<tr>
		<td>001000</td>
		<td>8</td>
		<td>参数节点包含当前节点</td>
	</tr>
	<tr>
		<td>010000</td>
		<td>16</td>
		<td>当前节点包含参数节点</td>
	</tr>
	<tr>
		<td>100000</td>
		<td>32</td>
		<td>浏览器内部使用</td>
	</tr>
</table>

注意，表格中的这些值是可以叠加的，如果某两个节点同时满足上面两种情况，则等于两个二进制值相加。

```
// HTML 代码如下
// <div id="mydiv">
//   <form><input id="test" /></form>
// </div>

var div = document.getElementById('mydiv');
var input = document.getElementById('test');

div.compareDocumentPosition(input) // 20
input.compareDocumentPosition(div) // 10
```
上面代码中，节点`div`包含节点`input`（二进制`010000`），而且节点`input`在节点`div`的后面（二进制`000100`），所以第一个`compareDocumentPosition`方法返回`20`（二进制`010100`，即`010000 + 000100`），第二个`compareDocumentPosition`方法返回`10`（二进制`001010`）。

由于·compareDocumentPosition·返回值的含义，定义在每一个比特位上，所以如果要检查某一种特定的含义，就需要使用比特位运算符。

```
var head = document.head;
var body = document.body;
if (head.compareDocumentPosition(body) & 4) {
  console.log('文档结构正确');
} else {
  console.log('<body> 不能在 <head> 前面');
}
```
上面代码中，`compareDocumentPosition`的返回值与`4`（又称掩码）进行与运算（`&`），得到一个布尔值，表示`<head>`是否在`<body>`前面。
采用与运算其实也就是检查`compareDocumentPosition`的返回值中值为4的那一个二进制位是否为1，因为与4相与，4即`000100`，如果返回值中值为4的那一位等于1返回值就不为0，否则就为0。

### Node.isEqualNode()，Node.isSameNode()

**`isEqualNode`方法返回一个布尔值，用于检查两个节点是否相等。所谓<font color="red">相等的节点，指的是两个节点的类型相同、属性相同、子节点相同</font>。**

```
var p1 = document.createElement('p');
var p2 = document.createElement('p');

p1.isEqualNode(p2) // true
```

**`isSameNode`方法返回一个布尔值，表示两个节点是否为<font color="red">同一个节点</font>**。

```
var p1 = document.createElement('p');
var p2 = document.createElement('p');

p1.isSameNode(p2) // false
p1.isSameNode(p1) // true
```

### Node.normalize()

**`normailize`方法用于整理当前节点内部的所有文本节点（`text`）。它会<font color="red">去除空的文本节点，并且将毗邻的文本节点合并成一个</font>，也就是说不存在空的文本节点，以及毗邻的文本节点**。

```
var wrapper = document.createElement('div');

wrapper.appendChild(document.createTextNode('Part 1 '));
wrapper.appendChild(document.createTextNode('Part 2 '));

wrapper.childNodes.length // 2
wrapper.normalize();
wrapper.childNodes.length // 1
```
上面代码使用`normalize`方法之前，`wrapper`节点有两个毗邻的文本子节点。使用`normalize`方法之后，两个文本子节点被合并成一个。

该方法是`Text.splitText`的逆方法，可以查看《Text 节点对象》一章，了解更多内容。

### Node.getRootNode()

**`getRootNode`方法返回当前节点所在文档的根节点。**

```
document.body.firstChild.getRootNode() === document // true
```

## NodeList 接口

节点都是单个对象，有时需要一种数据结构，能够容纳多个节点。**DOM 提供两种节点集合，用于容纳多个节点：`NodeList`和`HTMLCollection`**。

这两种集合都属于接口规范。许多 DOM 属性和方法，返回的结果是`NodeList`实例或`HTMLCollection`实例。

### 概述

**`NodeList`实例是一个类似数组的对象，它的成员是节点对象**。通过以下方法可以得到`NodeList`实例。

> `Node.childNodes`
> `document.querySelectorAll()`、`document.getElementsByTagName()`等节点搜索方法

```
document.body.childNodes instanceof NodeList // true
```

`NodeList`实例很像数组，<font color="red">可以使用`length`属性和`forEach`方法</font>。但是，它不是数组，不能使用`pop`或`push`之类数组特有的方法。

```
var children = document.body.childNodes;

Array.isArray(children) // false

children.length // 34
children.forEach(console.log)
```
上面代码中，`NodeList`实例`children`不是数组，但是具有`length`属性和`forEach`方法。

如果`NodeList`实例要使用数组方法，可以将其转为真正的数组。

```
var children = document.body.childNodes;
var nodeArr = Array.prototype.slice.call(children);
```
除了使用`forEach`方法遍历`NodeList`实例，还可以使用`for`循环。

```
var children = document.body.childNodes;

for (var i = 0; i < children.length; i++) {
  var item = children[i];
}
```

注意，NodeList 实例可能是动态集合，也可能是静态集合。所谓动态集合就是一个活的集合，DOM 删除或新增一个相关节点，都会立刻反映在 NodeList 实例。目前，**只有`Node.childNodes`返回的是一个动态集合，其他的 NodeList 都是静态集合**。

```
var children = document.body.childNodes;
children.length // 18
document.body.appendChild(document.createElement('p'));
children.length // 19
```
上面代码中，文档增加一个子节点，NodeList 实例`children`的`length`属性就增加了1。

### NodeList.prototype.length

**`length`属性返回 NodeList 实例包含的节点数量***。

```
document.getElementsByTagName('xxx').length
// 0
```
上面代码中，`document.getElementsByTagName`返回一个 NodeList 集合。对于那些不存在的 HTML 标签，`length`属性返回0。

### NodeList.prototype.forEach()

**`forEach`方法用于遍历 NodeList 的所有成员。它接受一个回调函数作为参数，每一轮遍历就执行一次这个回调函数**，用法与数组实例的`forEach`方法完全一致。

```
var children = document.body.childNodes;
children.forEach(function f(item, i, list) {
  // ...
}, this);
```
上面代码中，**<font color="red">回调函数`f`的三个参数依次是当前成员、位置和当前 NodeList 实例</font>**。
<font color="red">`forEach`方法的第二个参数，用于绑定回调函数内部的`this`</font>，该参数可省略。

### NodeList.prototype.item()

**`item`方法接受一个整数值作为参数，表示成员的位置，返回该位置上的成员**。

```
document.body.childNodes.item(0)
```
上面代码中，`item(0)`返回第一个成员。

**如果参数值大于实际长度，或者索引不合法（比如负数），`item`方法返回`null`。如果省略参数，`item`方法会报错。**

注意，如果传入奇怪的值，则相当于传入0。

**<font color="red">所有类似数组的对象，都可以使用方括号运算符取出成员</font>。一般情况下，都是使用方括号运算符，而不使用`item`方法**。

```
document.body.childNodes[0]
```

### NodeList.prototype.keys()，NodeList.prototype.values()，NodeList.prototype.entries()

这三个方法都返回一个 ES6 的遍历器对象，可以通过`for...of`循环遍历获取每一个成员的信息。区别在于，**`keys()`返回键名的遍历器，`values()`返回键值的遍历器，`entries()`返回的遍历器同时包含键名和键值的信息。**

```
var children = document.body.childNodes;

for (var key of children.keys()) {
  console.log(key);
}
// 0
// 1
// 2
// ...

for (var value of children.values()) {
  console.log(value);
}
// #text
// <script>
// ...

for (var entry of children.entries()) {
  console.log(entry);
}
// Array [ 0, #text ]
// Array [ 1, <script> ]
// ...
```

## HTMLCollection 接口

### 概述

**`HTMLCollection`是一个节点对象的集合，只能包含元素节点（`element`），不能包含其他类型的节点。它的返回值是一个类似数组的对象，但是与`NodeList`接口不同，`HTMLCollection`没有`forEach`方法，只能使用`for`循环遍历。**

**返回`HTMLCollection`实例的，主要是一些Document对象的集合属性**，比如`document.links`、`docuement.forms`、`document.images`等。

```
document.links instanceof HTMLCollection // true
```

**HTMLCollection实例<font color="red">都是动态集合，节点的变化会实时反映在集合中</font>**。

如果元素节点有`id`或`name`属性，那么`HTMLCollection`实例上面，可以使用`id`属性或`name`属性引用该节点元素。如果没有对应的节点，则返回`null`。

```
// HTML 代码如下
// <img id="pic" src="http://example.com/foo.jpg">

var pic = document.getElementById('pic');
document.images.pic === pic // true
```
上面代码中，`document.images`是一个`HTMLCollection`实例，可以通过`<img>`元素的`id`属性值，从`HTMLCollection`实例上取到这个元素。

### HTMLCollection.prototype.length

**`length`属性返回HTMLCollection实例包含的成员数量**。

```
document.links.length // 18
```

### HTMLCollection.prototype.item()

**`item`方法接受一个整数值作为参数，表示成员的位置，返回该位置上的成员**。

```
var c = document.images;
var img0 = c.item(0);
```
上面代码中，`item(0)`表示返回`0`号位置的成员。**由于方括号运算符也具有同样作用，而且使用更方便，所以一般情况下，总是使用方括号运算符。**

**如果参数值超出成员数量或者不合法（比如小于0），那么`item`方法返回`null`。如果不传入参数，则`item`方法报错。**

### HTMLCollection.prototype.namedItem()

**`namedItem`方法的参数是一个字符串，表示`id`属性或`name`属性的值，返回对应的元素节点。如果没有对应的节点，则返回`null`**。

```
// HTML 代码如下
// <img id="pic" src="http://example.com/foo.jpg">

var pic = document.getElementById('pic');
document.images.namedItem('pic') === pic // true
```

## ParentNode 接口

节点对象除了继承 `Node` 接口以外，还会继承其他接口。`ParentNode`接口表示当前节点是一个父节点，提供一些处理子节点的方法。`ChildNode`接口表示当前节点是一个子节点，提供一些相关方法。

**如果当前节点是父节点，就会继承`ParentNode`接口。由于只有元素节点（`element`）、文档节点（`document`）和文档片段节点（`documentFragment`）拥有子节点，因此只有这三类节点会继承`ParentNode`接口**。

### ParentNode.children

**`children`属性返回一个`HTMLCollection`实例，成员是当前节点的所有元素子节点。该属性只读**。

下面是遍历某个节点的所有元素子节点的示例。

```
for (var i = 0; i < el.children.length; i++) {
  // ...
}
```

注意，**`children`属性只包括元素子节点，不包括其他类型的子节点（比如文本子节点）。如果没有元素类型的子节点，返回值`HTMLCollection`实例的`length`属性为0**。

另外，`HTMLCollection`是动态集合，会实时反映 DOM 的任何变化。

### ParentNode.firstElementChild

**`firstElementChild`属性返回当前节点的第一个元素子节点。如果没有任何元素子节点，则返回`null`**。

```
document.firstElementChild.nodeName
// "HTML"
```
上面代码中，`document`节点的第一个元素子节点是`<HTML>`。

### ParentNode.lastElementChild

**`lastElementChild`属性返回当前节点的最后一个元素子节点，如果不存在任何元素子节点，则返回`null`**。

```
document.lastElementChild.nodeName
// "HTML"
```
上面代码中，`document`节点的最后一个元素子节点是`<HTML>`（因为**`document`只包含这一个元素子节点**）。

### ParentNode.childElementCount

**`childElementCount`属性返回一个整数，表示当前节点的所有元素子节点的数目。如果不包含任何元素子节点，则返回0**。

```
document.body.childElementCount // 13
```

### ParentNode.append()，ParentNode.prepend()

**`append`方法为当前节点追加一个或多个子节点，位置是最后一个元素子节点的后面**。

**该方法不仅可以添加元素子节点，还可以添加文本子节点**。

```
var parent = document.body;

// 添加元素子节点
var p = document.createElement('p');
parent.append(p);

// 添加文本子节点
parent.append('Hello');

// 添加多个元素子节点
var p1 = document.createElement('p');
var p2 = document.createElement('p');
parent.append(p1, p2);

// 添加元素子节点和文本子节点
var p = document.createElement('p');
parent.append('Hello', p);
```
注意，该方法没有返回值。

**`prepend`方法为当前节点追加一个或多个子节点，位置是第一个元素子节点的前面**。它的用法与`append`方法完全一致，也是没有返回值。

## ChildNode 接口

如果一个节点有父节点，那么该节点就继承了`ChildNode`接口。

### ChildNode.remove()

**`remove`方法用于从父节点移除当前节点**。

```
el.remove()
```
上面代码在 DOM 里面移除了`el`节点。

### ChildNode.before()，ChildNode.after()

**`before`方法用于在当前节点的前面，插入一个或多个同级节点。两者拥有相同的父节点。**

注意，**该方法不仅可以插入元素节点，还可以插入文本节点**。

```
var p = document.createElement('p');
var p1 = document.createElement('p');

// 插入元素节点
el.before(p);

// 插入文本节点
el.before('Hello');

// 插入多个元素节点
el.before(p, p1);

// 插入元素节点和文本节点
el.before(p, 'Hello');
```

**`after`方法用于在当前节点的后面，插入一个或多个同级节点，两者拥有相同的父节点**。用法与`before`方法完全相同。

### ChildNode.replaceWith()

**`replaceWith`方法使用参数节点，替换当前节点。参数可以是元素节点，也可以是文本节点**。

```
var span = document.createElement('span');
el.replaceWith(span);
```
上面代码中，`el`节点将被`span`节点替换。

# document 对象

## 概述

**`document`对象是文档的根节点，每张网页都有自己的`document`对象。`window.document`属性就指向这个对象。只要浏览器开始载入 HTML 文档，该对象就存在了，可以直接使用。**

`document`对象有不同的办法可以获取。

> 正常的网页，直接使用`document`或`window.document`。
> `iframe`框架里面的网页，使用`iframe`节点的`contentDocument`属性。
> Ajax 操作返回的文档，使用`XMLHttpRequest`对象的`responseXML`属性。
> 内部节点的`ownerDocument`属性。

补充，想在`iframe`中获取父窗口的`document`可以使用`window.parent.document`。

**`document`对象继承了`EventTarget`接口、`Node`接口、`ParentNode`接口。这意味着，这些接口的方法都可以在`document`对象上调用**。除此之外，`document`对象还有很多自己的属性和方法。

## 属性

### 快捷方式属性

以下属性是指向文档内部的某个节点的快捷方式。

#### document.defaultView

**`document.defaultView`属性返回`document`对象所属的`window`对象。如果当前文档不属于`window`对象，该属性返回`null`**。

```
document.defaultView === window // true
```

#### document.doctype

对于 HTML 文档来说，`document`对象一般有两个子节点。第一个子节点是`document.doctype`，指向`<DOCTYPE>`节点，即文档类型（Document Type Declaration，简写DTD）节点。HTML 的文档类型节点，一般写成`<!DOCTYPE html>`。如果网页没有声明 DTD，该属性返回`null`。

```
var doctype = document.doctype;
doctype // "<!DOCTYPE html>"
doctype.name // "html"
```
`document.firstChild`通常就返回这个节点。

#### document.documentElement

**`document.documentElement`属性返回当前文档的根节点（root）**。它通常是`document`节点的第二个子节点，紧跟在`document.doctype`节点后面。HTML网页的该属性，一般是`<html>`节点。

#### document.body，document.head

**`document.body`属性指向`<body>`节点，`document.head`属性指向`<head>`节点**。

**这两个属性总是存在的，如果网页源码里面省略了`<head>`或`<body>`，浏览器会自动创建。另外，这两个属性是可写的，<font color="red">如果改写它们的值，相当于移除所有子节点</font>。**

#### document.scrollingElement

**`document.scrollingElement`属性返回文档的滚动元素。也就是说，当文档整体滚动时，到底是哪个元素在滚动**。

标准模式下，这个属性返回的文档的根元素`document.documentElement`（即`<html>`）。兼容（quirk）模式下，返回的是`<body>`元素，如果该元素不存在，返回`null`。

```
// 页面滚动到浏览器顶部
document.scrollingElement.scrollTop = 0;
```

#### document.activeElement

**`document.activeElement`属性返回获得当前焦点（focus）的 DOM 元素**。通常，这个属性返回的是`<input>`、`<textarea>`、`<select>`等表单元素，**如果当前没有焦点元素，返回`<body>`元素或`null`。**

#### document.fullscreenElement

**`document.fullscreenElement`属性返回当前以全屏状态展示的 DOM 元素。如果不是全屏状态，该属性返回`null`。**

```
if (document.fullscreenElement.nodeName == 'VIDEO') {
  console.log('全屏播放视频');
}
```
上面代码中，通过`document.fullscreenElement`可以知道`<video>`元素有没有处在全屏状态，从而判断用户行为。

### 节点集合属性

以下属性返回一个`HTMLCollection`实例，表示文档内部特定元素的集合。<font color="red">这些集合都是动态的，原节点有任何变化，立刻会反映在集合中</font>。

#### document.links

**`document.links`属性返回当前文档所有设定了`href`属性的`<a>`及`<area>`节点**。

```
// 打印文档所有的链接
var links = document.links;
for(var i = 0; i < links.length; i++) {
  console.log(links[i]);
}
```

#### document.forms

`document.forms`属性返回所有`<form>`表单节点。

```
var selectForm = document.forms[0];
```
上面代码获取文档第一个表单。

#### document.images

`document.images`属性返回页面所有`<img>`图片节点。

```
var imglist = document.images;

for(var i = 0; i < imglist.length; i++) {
  if (imglist[i].src === 'banner.gif') {
    // ...
  }
}
```
上面代码在所有`img`标签中，寻找某张图片。

#### document.embeds，document.plugins

`document.embeds`属性和`document.plugins`属性，都返回所有`<embed>`节点。

#### document.scripts

**`document.scripts`属性返回所有`<script>`节点**。

```
var scripts = document.scripts;
if (scripts.length !== 0 ) {
  console.log('当前网页有脚本');
}
```

#### document.styleSheets

`document.styleSheets`属性返回文档内嵌或引入的样式表集合，详细介绍请看《CSS 对象模型》一章。

#### 小结

除了`document.styleSheets`，以上的集合属性返回的都是`HTMLCollection`实例。

```
document.links instanceof HTMLCollection // true
document.images instanceof HTMLCollection // true
document.forms instanceof HTMLCollection // true
document.embeds instanceof HTMLCollection // true
document.scripts instanceof HTMLCollection // true
document.styleSheets instanceof HTMLCollection // false
```

**`HTMLCollection`实例是类似数组的对象，所以这些属性都有`length`属性，都可以使用方括号运算符引用成员。如果成员有`id`或`name`属性，还可以用这两个属性的值，在`HTMLCollection`实例上引用到这个成员**。

```
// HTML 代码如下
// <form name="myForm">
document.myForm === document.forms.myForm // true
```

### 文档静态信息属性

以下属性返回文档信息。

#### document.documentURI，document.URL

**`document.documentURI`属性和`document.URL`属性都返回一个字符串，表示当前文档的网址。不同之处是它们继承自不同的接口，`documentURI`继承自`Document`接口，可用于所有文档；`URL`继承自`HTMLDocument`接口，只能用于 HTML 文档。**

```
document.URL
// http://www.example.com/about

document.documentURI === document.URL
// true
```
如果文档的锚点（`#anchor`）变化，这两个属性都会跟着变化。

#### document.domain

**`document.domain`属性返回当前文档的域名，不包含协议和接口**。比如，网页的网址是`http://www.example.com:80/hello.html`，那么`domain`属性就等于`www.example.com`。如果无法获取域名，该属性返回`null`。

`document.domain`基本上是一个只读属性，只有一种情况除外。次级域名的网页，可以把`document.domain`设为对应的上级域名。比如，当前域名是`a.sub.example.com`，则`document.domain`属性可以设置为`sub.example.com`，也可以设为`example.com`。修改后，`document.domain`相同的两个网页，可以读取对方的资源，比如设置的 `Cookie`。

另外，设置`document.domain`会导致端口被改成`null`。因此，如果通过设置`document.domain`来进行通信，双方网页都必须设置这个值，才能保证端口相同。

#### document.location

**`Location`对象是浏览器提供的原生对象，提供 URL 相关的信息和操作方法**。通过`window.location`和`document.location`属性，可以拿到这个对象。

关于这个对象的详细介绍，请看《浏览器模型》部分的《Location 对象》章节。

#### document.lastModified

**`document.lastModified`属性返回一个字符串，表示当前文档最后修改的时间**。不同浏览器的返回值，日期格式是不一样的。

```
document.lastModified
// "03/07/2018 11:18:27"
```
注意，**`document.lastModified`属性的值是字符串，所以不能直接用来比较。`Date.parse`方法将其转为`Date`实例，才能比较两个网页**。

```
var lastVisitedDate = Date.parse('01/01/2018');
if (Date.parse(document.lastModified) > lastVisitedDate) {
  console.log('网页已经变更');
}
```

**如果页面上有 JavaScript 生成的内容，`document.lastModified`属性返回的总是当前时间。**

#### document.title

**`document.title`属性返回当前文档的标题。默认情况下，返回`<title>`节点的值。但是该属性是可写的，一旦被修改，就返回修改后的值**。

```
document.title = '新标题';
document.title // "新标题"
```

#### document.characterSet

**`document.characterSet`属性返回当前文档的编码**，比如UTF-8、ISO-8859-1等等。

#### document.referrer

**`document.referrer`属性返回一个字符串，表示当前文档的访问者来自哪里**。

```
document.referrer
// "https://example.com/path"
```
**如果无法获取来源，或者用户直接键入网址而不是从其他网页点击进入，`document.referrer`返回一个空字符串。**

`document.referrer`的值，总是与 HTTP 头信息的`Referer`字段保持一致。但是，`document.referrer`的拼写有两个r，而头信息的`Referer`字段只有一个`r`。

#### document.dir

`document.dir`返回一个字符串，表示文字方向。它只有两个可能的值：`rtl`表示文字从右到左，阿拉伯文是这种方式；`ltr`表示文字从左到右，包括英语和汉语在内的大多数文字采用这种方式。

#### document.compatMode

`compatMode`属性返回浏览器处理文档的模式，可能的值为`BackCompat`（向后兼容模式）和`CSS1Compat`（严格模式）。

一般来说，如果网页代码的第一行设置了明确的DOCTYPE（比如`<!doctype html>`），`document.compatMode`的值都为`CSS1Compat`。

### 文档状态属性

#### document.hidden

**`document.hidden`属性返回一个布尔值，表示当前页面是否可见。如果窗口最小化、浏览器切换了 Tab，都会导致导致页面不可见，使得`document.hidden`返回`true`**。

这个属性是 Page Visibility API 引入的，一般都是配合这个 API 使用。

#### document.visibilityState

**`document.visibilityState`返回文档的可见状态**。

它的值有四种可能。

> visible：页面可见。注意，页面可能是部分可见，即不是焦点窗口，前面被其他窗口部分挡住了。
> hidden： 页面不可见，有可能窗口最小化，或者浏览器切换到了另一个 Tab。
> prerender：页面处于正在渲染状态，对于用于来说，该页面不可见。
> unloaded：页面从内存里面卸载了。

这个属性**可以用在页面加载时，防止加载某些资源；或者页面不可见时，停掉一些页面功能。**

#### document.readyState

**`document.readyState`属性返回当前文档的状态**，共有三种可能的值。

> loading：加载 HTML 代码阶段（尚未完成解析）
> interactive：加载外部资源阶段
> complete：加载完成

这个属性变化的过程如下。

1. 浏览器开始解析 HTML 文档，`document.readyState`属性等于`loading`。
2. 浏览器遇到 HTML 文档中的`<script>`元素，并且没有`async`或`defer`属性，就暂停解析，开始执行脚本，这时`document.readyState`属性还是等于`loading`。
3. HTML 文档解析完成，`document.readyState`属性变成`interactive`。
4. 浏览器等待图片、样式表、字体文件等外部资源加载完成，一旦全部加载完成，`document.readyState`属性变成`complete`。

下面的代码用来检查网页是否加载成功。

```
// 基本检查
if (document.readyState === 'complete') {
  // ...
}

// 轮询检查
var interval = setInterval(function() {
  if (document.readyState === 'complete') {
    clearInterval(interval);
    // ...
  }
}, 100);
```
另外，每次状态变化都会触发一个`readystatechange`事件。

### document.cookie

document.cookie属性用来操作浏览器 Cookie，详见《浏览器模型》部分的《Cookie》章节。

### document.designMode

**`document.designMode`属性控制当前文档是否可编辑，通常用在所见即所得编辑器**。该属性只有两个值`on`和`off`，默认值为`off`。

下面代码打开`iframe`元素内部文档的`designMode`属性，就能将其变为一个所见即所得的编辑器。

```
// HTML 代码如下
// <iframe id="editor" src="about:blank"></iframe>
var editor = document.getElementById('editor');
editor.contentDocument.designMode = 'on';
```
上面代码会使一个空白的`iframe`变为一个文本框一般的编辑器。

### document.implementation

`document.implementation`属性返回一个`DOMImplementation`对象。该对象有三个方法，**主要用于创建独立于当前文档的新的 Document 对象**。

> `DOMImplementation.createDocument()`：创建一个 XML 文档。
> `DOMImplementation.createHTMLDocument()`：创建一个 HTML 文档。
> `DOMImplementation.createDocumentType()`：创建一个 DocumentType 对象。

下面是创建 HTML 文档的例子。

```
var doc = document.implementation.createHTMLDocument('Title');
var p = doc.createElement('p');
p.innerHTML = 'hello world';
doc.body.appendChild(p);

document.replaceChild(
  doc.documentElement,
  document.documentElement
);
```
上面代码中，第一步生成一个新的 HTML 文档`doc`，然后用它的根元素`document.documentElement`替换掉`document.documentElement`。这会使得当前文档的内容全部消失，变成`hello world`。

## 方法

### document.open()，document.close()

**`document.open`方法清除当前文档所有内容，使得文档处于可写状态，供`document.write`方法写入内容**。

**`document.close`方法用来关闭`document.open()`打开的文档**。

### document.write()，document.writeln()

**`document.write`方法用于向当前文档写入内容**。

**在网页的首次渲染阶段，只要页面没有关闭写入（即没有执行`document.close()`），`document.write`写入的内容就会追加在已有内容的后面。**

```
// 页面显示“helloworld”
document.open();
document.write('hello');
document.write('world');
document.close();
```
注意，`document.write`会当作 HTML 代码解析，不会转义。

```
document.write('<p>hello world</p>');
```
上面代码中，`document.write`会将`<p>`当作 HTML 标签解释。

**如果页面已经解析完成（DOMContentLoaded事件发生之后），再调用`write`方法，它会先调用`open`方法，擦除当前文档所有内容，然后再写入**。

```
document.addEventListener('DOMContentLoaded', function (event) {
  document.write('<p>Hello World!</p>');
});

// 等同于
document.addEventListener('DOMContentLoaded', function (event) {
  document.open();
  document.write('<p>Hello World!</p>');
  document.close();
});
```

如果在页面渲染过程中调用`write`方法，并不会自动调用`open`方法。（可以理解成，`open`方法已调用，但`close`方法还未调用。）

```
<html>
<body>
hello
<script type="text/javascript">
  document.write("world")
</script>
</body>
</html>
```
在浏览器打开上面网页，将会显示`hello world`。

`document.write`是JavaScript语言标准化之前就存在的方法，现在完全有更符合标准的方法向文档写入内容（比如对`innerHTML`属性赋值）。所以，**除了某些特殊情况，应该尽量避免使用`document.write`这个方法。**

**`document.writeln`方法与`write`方法完全一致，除了会在输出内容的尾部添加换行符**。

```
document.write(1);
document.write(2);
// 12

document.writeln(1);
document.writeln(2);
// 1
// 2
//
```
注意，`writeln`方法添加的是 ASCII 码的换行符，渲染成 HTML 网页时不起作用，即在网页上显示不出换行。**网页上的换行，必须显式写入`<br>`**。

### document.querySelector()，document.querySelectorAll()

**`document.querySelector`方法接受一个 CSS 选择器作为参数，返回匹配该选择器的元素节点。<font color="red">如果有多个节点满足匹配条件，则返回第一个匹配的节点</font>。如果没有发现匹配的节点，则返回`null`**。

```
var el1 = document.querySelector('.myclass');
var el2 = document.querySelector('#myParent > [ng-click]');
```

**`document.querySelectorAll`方法与`querySelector`用法类似，区别是返回一个`NodeList`对象，包含所有匹配给定选择器的节点**。

```
elementList = document.querySelectorAll('.myclass');
```

这两个方法的**参数，可以是<font color="red">逗号分隔的多个 CSS 选择器</font>，返回匹配<font color="red">其中一个选择器</font>的元素节点**，这与 CSS 选择器的规则是一致的。

```
var matches = document.querySelectorAll('div.note, div.alert');
```
上面代码返回`class`属性是`note`或`alert`的`div`元素。

这两个方法都支持复杂的 CSS 选择器。

```
// 选中 data-foo-bar 属性等于 someval 的元素
document.querySelectorAll('[data-foo-bar="someval"]');

// 选中 myForm 表单中所有不通过验证的元素
document.querySelectorAll('#myForm :invalid');

// 选中div元素，那些 class 含 ignore 的除外
document.querySelectorAll('DIV:not(.ignore)');

// 同时选中 div，a，script 三类元素
document.querySelectorAll('DIV, A, SCRIPT');
```

但是，它们不支持 CSS 伪元素的选择器（比如`:first-line`和`:first-letter`）和伪类的选择器（比如`:link`和`:visited`），即**无法选中伪元素和伪类**。

如果`querySelectorAll`方法的参数是字符串`*`，则会返回文档中的所有元素节点。另外，<font color="red">`querySelectorAll`的返回结果不是动态集合，不会实时反映元素节点的变化</font>。

最后，这两个方法除了定义在`document`对象上，还定义在元素节点上，即**在元素节点上也可以调用**。

#### 关于伪元素和伪类的补充

伪元素：（pseudo-element）是HTML 中并不存在的元素。**用于将特殊的效果添加到某些选择器。**

W3C：

<table>
	<tr>
		<td>属性</td>
		<td>描述</td>
		<td>CSS</td>
	</tr>
	<tr>
		<td>:first-letter</td>
		<td>向文本的第一个字母添加特殊样式。</td>
		<td>1</td>
	</tr>
	<tr>
		<td>:first-line</td>
		<td>向文本的首行添加特殊样式。</td>
		<td>1</td>
	</tr>
	<tr>
		<td>:before</td>
		<td>在元素之前添加内容。</td>
		<td>2</td>
	</tr>
	<tr>
		<td>:after</td>
		<td>在元素之后添加内容。</td>
		<td>2</td>
	</tr>
</table>

以下是CSS3中对伪元素的描述：

伪元素的由两个冒号`::`开头，然后是伪元素的名称 。

使用两个冒号`::`是为了区别伪类和伪元素（CSS2中并没有区别）。当然，考虑到兼容性，CSS2中已存的伪元素仍然可以使用一个冒号`:`的语法，但是CSS3中新增的伪元素必须使用两个冒号`::`

例如：
```
// 选择每个 <p> 元素的首字母，并为其设置样式：
p:first-letter{ 
  font-size:200%;
  color:#8A2BE2;
}
```


`:after`与`:before`的一个用处在于，可以减少`html`代码里的节点个数及内容，优化代码阅读。

`:after`还有一个妙用，那就是清除浮动，给父元素追加`:after`之后，设置`content："";clear:both;`就可以清除浮动。

下面看看伪类：

伪类：(pseudo-class）则应用于一组HTML元素。**用于向某些选择器添加特殊的效果**。

<table>
	<tr>
		<td>属性</td>
		<td>描述</td>
		<td>CSS</td>
	</tr>
	<tr>
		<td>:active</td>
		<td>向被激活的元素添加样式。</td>
		<td>1</td>
	</tr>
	<tr>
		<td>:focus</td>
		<td>向拥有键盘输入焦点的元素添加样式。</td>
		<td>2</td>
	</tr>
	<tr>
		<td>:hover</td>
		<td>当鼠标悬浮在元素上方时，向元素添加样式。</td>
		<td>1</td>
	</tr>
	<tr>
		<td>:link</td>
		<td>向未被访问的链接添加样式。</td>
		<td>1</td>
	</tr>
	<tr>
		<td>:visited</td>
		<td>向已被访问的链接添加样式。</td>
		<td>1</td>
	</tr>
	<tr>
		<td>:first-child</td>
		<td>向元素的第一个子元素添加样式。</td>
		<td>2</td>
	</tr>
	<tr>
		<td>:lang</td>
		<td>向带有指定 lang 属性的元素添加样式。</td>
		<td>2</td>
	</tr>
</table>

css3中定义：

伪类存在的意义是为了通过选择器找到那些不存在于DOM树中的信息以及不能被常规CSS选择器获取到的信息。

伪类由一个冒号`:`开头，冒号后面是伪类的名称和包含在圆括号中的可选参数。

任何常规选择器可以在任何位置使用伪类。伪类语法不区别大小写。一些伪类的作用会互斥，另外一些伪类可以同时被同一个元素使用。

并且，为了满足用户在操作DOM时产生的DOM结构改变，伪类也可以是动态的。

从定义得知，伪类的作用是获取页面中不存在的信息。比如`<a></a>`
 标签中的`:linked ``:visited`,这些内容不存在页面当中，只能通过css选择器来获取。

且一个元素可以同时设置多个伪类效果。其中，运用伪类最多的是`<a></a>`添加链接样式。

```
a:link
 {color: #FF0000}		/* 未访问的链接 */
a:visited
 {color: #00FF00}	/* 已访问的链接 */
a:hover
 {color: #FF00FF}	/* 鼠标移动到链接上 */
a:active
 {color: #0000FF}	/* 选定的链接 */
```
**这里要注意的是，这几个伪类如果同时出现在一个元素的操作上，顺序不能改变，否则很大程度上会产生紊乱，效果不生效。**

伪类与伪元素的区别：
伪类选择元素基于的是当前元素处于的状态，或者说元素当前所具有的特性，而不是元素的`id`、`class`、属性等静态的标志。由于状态是动态变化的，所以一个元素达到一个特定状态时，它可能得到一个伪类的样式；当状态改变时，它又会失去这个样式。由此可以看出，它的功能和`class`有些类似，但它是基于文档之外的抽象，所以叫伪类。
与伪类针对特殊状态的元素不同的是，伪元素是对元素中的特定内容进行操作，它所操作的层次比伪类更深了一层，也因此它的动态性比伪类要低得多。实际上，设计伪元素的目的就是去选取诸如元素内容第一个字（母）、第一行，选取某些内容前面或后面这种普通的选择器无法完成的工作。它控制的内容实际上和元素是相同的，但是它本身只是基于元素的抽象，并不存在于文档中，所以叫伪元素。

### document.getElementsByTagName()

**`document.getElementsByTagName`方法搜索 HTML 标签名，返回符合条件的元素。它的返回值是一个类似数组对象（`HTMLCollection`实例），可以实时反映 HTML 文档的变化。如果没有任何匹配的元素，就返回一个空集**。

```
var paras = document.getElementsByTagName('p');
paras instanceof HTMLCollection // true
```
上面代码返回当前文档的所有`p`元素节点。

HTML 标签名是大小写不敏感的，因此`getElementsByTagName`方法的参数也是大小写不敏感的。另外，返回结果中，各个成员的顺序就是它们在文档中出现的顺序。

**如果传入`*`，就可以返回文档中所有 HTML 元素**。

```
var allElements = document.getElementsByTagName('*');
```
注意，元素节点本身也定义了`getElementsByTagName`方法，返回该元素的后代元素中符合条件的元素。也就是说，这个方法不仅可以在`document`对象上调用，也可以在任何元素节点上调用。

```
var firstPara = document.getElementsByTagName('p')[0];
var spans = firstPara.getElementsByTagName('span');
```
上面代码选中第一个`p`元素内部的所有`span`元素。

### document.getElementsByClassName()

**`document.getElementsByClassName`方法返回一个类似数组的对象（`HTMLCollection`实例），包括了所有`class`名字符合指定条件的元素，元素的变化实时反映在返回结果中**。

```
var elements = document.getElementsByClassName(names);
```

由于`class`是保留字，所以 JavaScript 一律使用`className`表示 CSS 的`class`。

**参数可以是多个`class`，它们之间<font color="red">使用空格分隔</font>**。

```
var elements = document.getElementsByClassName('foo bar');
```

上面代码**返回<font color="red">同时具有foo和bar两个class的元素，foo和bar的顺序不重要</font>**。

注意，正常模式下，CSS 的`class`是大小写敏感的。（quirks mode下，大小写不敏感。）

与`getElementsByTagName`方法一样，`getElementsByClassName`方法不仅可以在`document`对象上调用，也可以在任何元素节点上调用。

```
// 非document对象上调用
var elements = rootElement.getElementsByClassName(names);
```

### document.getElementsByName()

**`document.getElementsByName`方法用于选择拥有`name`属性的 HTML 元素（比如`<form>`、`<radio>`、`<img>`、`<frame>`、`<embed>`和`<object>`等），返回一个类似数组的的对象（`NodeList`实例），因为`name`属性相同的元素可能不止一个**。

```
// 表单为 <form name="x"></form>
var forms = document.getElementsByName('x');
forms[0].tagName // "FORM"
```

### document.getElementById()

**`document.getElementById`方法返回匹配指定`id`属性的元素节点。如果没有发现匹配的节点，则返回`null`。**

```
var elem = document.getElementById('para1');
```
注意，**该方法的参数是大小写敏感的**。比如，如果某个节点的`id`属性是`main`，那么`document.getElementById('Main')`将返回`null`。

`document.getElementById`方法与`document.querySelector`方法都能获取元素节点，不同之处是`document.querySelector`方法的参数使用 CSS 选择器语法，`document.getElementById`方法的参数是元素的`id`属性。

```
document.getElementById('myElement')
document.querySelector('#myElement')
```
上面代码中，两个方法都能选中`id`为`myElement`的元素，但是**`document.getElementById()`比`document.querySelector()`效率高得多**。

另外，**这个方法只能在`document`对象上使用，不能在其他元素节点上使用**。

### document.elementFromPoint()，document.elementsFromPoint()

**`document.elementFromPoint`方法返回位于页面指定位置最上层的元素节点**。

```
var element = document.elementFromPoint(50, 50);
```
上面代码选中在`(50, 50)`这个坐标位置的最上层的那个 HTML 元素。

`elementFromPoint`方法的两个参数，依次是相对于当前视口左上角的横坐标和纵坐标，单位是像素。如果位于该位置的 HTML 元素不可返回（比如文本框的滚动条），则返回它的父元素（比如文本框）。如果坐标值无意义（比如负值或超过视口大小），则返回`null`。

**`document.elementsFromPoint()`返回一个数组，成员是位于指定坐标（相对于视口）的所有元素**。

```
var elements = document.elementsFromPoint(x, y);
```

### document.caretPositionFromPoint()

`document.caretPositionFromPoint()`返回一个 CaretPosition 对象，包含了指定坐标点在节点对象内部的位置信息。**CaretPosition 对象就是光标插入点的概念，用于确定光标点在文本对象内部的具体位置**。

```
var range = document.caretPositionFromPoint(clientX, clientY);
```
上面代码中，`range`是指定坐标点的 CaretPosition 对象。该对象有两个属性。

> CaretPosition.offsetNode：该位置的节点对象
> CaretPosition.offset：该位置在offsetNode对象内部，与起始位置相距的字符数。

### document.createElement()

**`document.createElement`方法用来生成元素节点，并返回该节点**。

```
var newDiv = document.createElement('div');
```
**`createElement`方法的参数为元素的标签名，即元素节点的`tagName`属性**，对于 HTML 网页大小写不敏感，即参数为`div`或`DIV`返回的是同一种节点。如果参数里面包含尖括号（即`<`和`>`）会报错。

```
document.createElement('<div>');
// DOMException: The tag name provided ('<div>') is not a valid name
```

注意，`document.createElement`的参数可以是自定义的标签名。

```
document.createElement('foo');
```

### document.createTextNode()

**`document.createTextNode`方法用来生成文本节点（`Text`实例），并返回该节点。它的参数是文本节点的内容**。

```
var newDiv = document.createElement('div');
var newContent = document.createTextNode('Hello');
newDiv.appendChild(newContent);
```
上面代码新建一个`div`节点和一个文本节点，然后将文本节点插入`div`节点。

**这个方法可以确保返回的节点，被浏览器当作文本渲染，而不是当作 HTML 代码渲染。因此，可以用来展示用户的输入，避免 XSS 攻击**。

```
var div = document.createElement('div');
div.appendChild(document.createTextNode('<span>Foo & bar</span>'));
console.log(div.innerHTML)
// &lt;span&gt;Foo &amp; bar&lt;/span&gt;
```
上面代码中，`createTextNode`方法对大于号和小于号进行转义，从而保证即使用户输入的内容包含恶意代码，也能正确显示。

需要注意的是，该方法不对单引号和双引号转义，所以不能用来对 HTML 属性赋值。

```
function escapeHtml(str) {
  var div = document.createElement('div');
  div.appendChild(document.createTextNode(str));
  return div.innerHTML;
};

var userWebsite = '" onmouseover="alert(\'derp\')" "';
var profileLink = '<a href="' + escapeHtml(userWebsite) + '">Bob</a>';
var div = document.getElementById('target');
div.innerHTML = profileLink;
// <a href="" onmouseover="alert('derp')" "">Bob</a>
```
上面代码中，由于`createTextNode`方法不转义双引号，导致`onmouseover`方法被注入了代码。

### document.createAttribute()

**`document.createAttribute`方法生成一个新的属性节点（Attr实例），并返回它**。

```
var attribute = document.createAttribute(name);
```

`document.createAttribute`方法的参数`name`，是属性的名称。

```
var node = document.getElementById('div1');

var a = document.createAttribute('my_attrib');
a.value = 'newVal';

node.setAttributeNode(a);
// 或者
node.setAttribute('my_attrib', 'newVal');
```
上面代码为`div1`节点，插入一个值为`newVal`的`my_attrib`属性。

### document.createComment()

**`document.createComment`方法生成一个新的注释节点，并返回该节点**。

```
var CommentNode = document.createComment(data);
```

`document.createComment`方法的**参数是一个字符串，会成为注释节点的内容**。

### document.createDocumentFragment()

**`document.createDocumentFragment`方法生成一个空的文档片段对象**（`DocumentFragment`实例）。

```
var docFragment = document.createDocumentFragment();
```

`DocumentFragment`是一个存在于内存的 DOM 片段，不属于当前文档，常常用来生成一段较复杂的 DOM 结构，然后再插入当前文档。**这样做的好处在于，因为`DocumentFragment`不属于当前文档，对它的任何改动，都不会引发网页的重新渲染，比直接修改当前文档的 DOM 有更好的性能表现。**

```
var docfrag = document.createDocumentFragment();

[1, 2, 3, 4].forEach(function (e) {
  var li = document.createElement('li');
  li.textContent = e;
  docfrag.appendChild(li);
});

var element  = document.getElementById('ul');
element.appendChild(docfrag);
```
上面代码中，文档片断`docfrag`包含四个`<li>`节点，这些子节点被一次性插入了当前文档。

### document.createEvent()

**`document.createEvent`方法生成一个事件对象（Event实例），该对象可以被`element.dispatchEvent`方法使用，触发指定事件**。

```
var event = document.createEvent(type);
```

`document.createEvent`方法的参数是事件类型，比如`UIEvents`、`MouseEvents`、`MutationEvents`、`HTMLEvents`。

```
var event = document.createEvent('Event');
event.initEvent('build', true, true);
document.addEventListener('build', function (e) {
  console.log(e.type); // "build"
}, false);
document.dispatchEvent(event);
```
上面代码新建了一个名为`build`的事件实例，然后触发该事件。

### document.addEventListener()，document.removeEventListener()，document.dispatchEvent()

这三个方法用于处理`document`节点的事件。它们都继承自`EventTarget`接口，详细介绍参见《EventTarget 接口》一章。

```
// 添加事件监听函数
document.addEventListener('click', listener, false);

// 移除事件监听函数
document.removeEventListener('click', listener, false);

// 触发事件，相当于jQuery中的trigger
var event = new Event('click');
document.dispatchEvent(event);
```

### document.hasFocus()

**`document.hasFocus`方法返回一个布尔值，表示当前文档之中是否有元素被激活或获得焦点**。

```
var focused = document.hasFocus();
```

注意，**有焦点的文档必定被激活（active），反之不成立，激活的文档未必有焦点**。比如，用户点击按钮，从当前窗口跳出一个新窗口，该新窗口就是激活的，但是不拥有焦点。

### document.adoptNode()，document.importNode()

**`document.adoptNode`方法将某个节点及其子节点，从原来所在的文档或`DocumentFragment`里面移除，归属当前`document`对象，返回插入后的新节点。插入的节点对象的`ownerDocument`属性，会变成当前的`document`对象，而`parentNode`属性是`null`**。

```
var node = document.adoptNode(externalNode);
document.appendChild(node);
```
注意，**`document.adoptNode`方法只是改变了节点的归属，并没有将这个节点插入新的文档树，也就是刚才说到的`parentNode`属性是`null`。所以，还要再用`appendChild`方法或`insertBefore方法`，将新节点插入当前文档树。**

**`document.importNode`方法则是从原来所在的文档或`DocumentFragment`里面，拷贝某个节点及其子节点，让它们归属当前`document`对象。拷贝的节点对象的`ownerDocument`属性，会变成当前的`document`对象，而`parentNode`属性是`null`。**

也就是说，`adoptNode()`方法相当于剪切，`importNode()`方法相当于复制。

```
var node = document.importNode(externalNode, deep);
```

`document.importNode`方法的第一个参数是外部节点，**第二个参数是一个布尔值，表示对外部节点是深拷贝还是浅拷贝，默认是浅拷贝（`false`）。虽然第二个参数是可选的，但是<font color="red">建议总是保留这个参数，并设为`true`</font>**。

注意，`document.importNode`方法只是拷贝外部节点，这时该节点的父节点是`null`。下一步还必须将这个节点插入当前文档树。

```
var iframe = document.getElementsByTagName('iframe')[0];
var oldNode = iframe.contentWindow.document.getElementById('myNode');
var newNode = document.importNode(oldNode, true);
document.getElementById("container").appendChild(newNode);
```
上面代码从`iframe`窗口，拷贝一个指定节点`myNode`，插入当前文档。

扩展：关于浅拷贝和深拷贝

深复制和浅复制最根本的区别在于是否是真正获取了一个对象的复制实体，而不是引用。
1. 深复制在计算机中开辟了一块内存地址用于存放复制的对象。
2. 而浅复制仅仅是指向被复制的内存地址，如果原地址中对象被改变了，那么浅复制出来的对象也会相应改变。

所谓的浅复制，只是拷贝了基本类型的数据，而引用类型数据，复制后会发生引用，我们把这种拷贝叫做“（浅复制）浅拷贝”。

### document.createNodeIterator()

**`document.createNodeIterator`方法返回一个子节点遍历器**。

```
var nodeIterator = document.createNodeIterator(
  document.body,
  NodeFilter.SHOW_ELEMENT
);
```
上面代码返回`<body>`元素子节点的遍历器。

**`document.createNodeIterator`方法第一个参数为所要遍历的根节点，第二个参数为所要遍历的节点类型， 默认遍历所有节点**，这里指定为元素节点（`NodeFilter.SHOW_ELEMENT`）。几种主要的节点类型写法如下。

> 所有节点：NodeFilter.SHOW_ALL
> 元素节点：NodeFilter.SHOW_ELEMENT
> 文本节点：NodeFilter.SHOW_TEXT
> 评论/注释节点：NodeFilter.SHOW_COMMENT

`document.createNodeIterator`方法返回一个“遍历器”对象（`NodeFilter`实例）。**该实例的`nextNode()`方法和`previousNode()`方法，可以用来遍历所有子节点。**

```
var nodeIterator = document.createNodeIterator(document.body);
var pars = [];
var currentNode;

while (currentNode = nodeIterator.nextNode()) {
  pars.push(currentNode);
}
```
上面代码中，使用遍历器的`nextNode`方法，将根节点的所有子节点，依次读入一个数组。**`nextNode`方法先返回遍历器的内部指针所在的节点，然后会将指针移向下一个节点**。所有成员遍历完成后，返回`null`。**`previousNode`方法则是先将指针移向上一个节点，然后返回该节点**。

```
var nodeIterator = document.createNodeIterator(
  document.body,
  NodeFilter.SHOW_ELEMENT
);

var currentNode = nodeIterator.nextNode();
var previousNode = nodeIterator.previousNode();

currentNode === previousNode // true
```
上面代码中，`currentNode`和`previousNode`都指向同一个的节点。

注意，**遍历器返回的第一个节点，总是根节点。**

```
pars[0] === document.body // true
```

### document.createTreeWalker()

**`document.createTreeWalker`方法返回一个 DOM 的子树遍历器**。它与`document.createNodeIterator`方法基本是类似的，区别在于它返回的是`TreeWalker`实例，后者返回的是`NodeIterator`实例。另外，**它的第一个节点不是根节点。**

**`document.createTreeWalker`方法的第一个参数是所要遍历的根节点，第二个参数指定所要遍历的节点类型（与`document.createNodeIterator`方法的第二个参数相同）**。

```
var treeWalker = document.createTreeWalker(
  document.body,
  NodeFilter.SHOW_ELEMENT
);

var nodeList = [];

while(treeWalker.nextNode()) {
  nodeList.push(treeWalker.currentNode);
}
```
上面代码遍历`<body>`节点下属的所有元素节点，将它们插入`nodeList`数组。

### document.getSelection()

这个方法指向`window.getSelection()`，参见`window`对象一节的介绍。

# Element对象

`Element`对象对应网页的 HTML 元素。每一个 HTML 元素，在 DOM 树上都会转化成一个`Element`节点对象（以下简称元素节点）。

元素节点的`nodeType`属性都是`1`。

```
var p = document.querySelector('p');
p.nodeName // "P"
p.nodeType // 1
```

`Element`对象继承了`Node`接口，因此`Node`的属性和方法在`Element`对象都存在。此外，不同的 HTML 元素对应的元素节点是不一样的，浏览器使用不同的构造函数，生成不同的元素节点，比如`<a>`元素的节点对象由`HTMLAnchorElement`构造函数生成，`<button>`元素的节点对象由`HTMLButtonElement`构造函数生成。因此，元素节点不是一种对象，而是一组对象，这些对象除了继承`Element`的属性和方法，还有各自构造函数的属性和方法。

## 实例属性

### 元素特性的相关属性

#### Element.id（可读写）

**`Element.id`属性返回指定元素的`id`属性，该属性可读写**。

```
// HTML 代码为 <p id="foo">
var p = document.querySelector('p');
p.id // "foo"
```

注意，**`id`属性的值是大小写敏感**，即浏览器能正确识别`<p id="foo">`和`<p id="FOO">`这两个元素的`id`属性，但是最好不要这样命名。

#### Element.tagName

**`Element.tagName`属性返回指定元素的大写标签名，与`nodeName`属性的值相等**。

```
// HTML代码为
// <span id="myspan">Hello</span>
var span = document.getElementById('myspan');
span.id // "myspan"
span.tagName // "SPAN"
```

#### Element.dir

`Element.dir`属性用于读写当前元素的文字方向，可能是从左到右（`"ltr"`），也可能是从右到左（`"rtl"`）。

#### Element.accessKey

**`Element.accessKey`属性用于读写分配给当前元素的快捷键。**

```
// HTML 代码如下
// <button accesskey="h" id="btn">点击</button>
var btn = document.getElementById('btn');
btn.accessKey // "h"
```
上面代码中，`btn`元素的快捷键是`h`，按下`Alt + h`就能将焦点转移到它上面。

**为元素指定快捷键，以下元素支持 accesskey 属性：`<a>`,`<area>`, `<button>`, `<input>`, `<label>`, `<legend>` 以及 `<textarea>`，使用`Alt + accessKey` (或者 `Shift + Alt + accessKey`) 来访问带有指定快捷键的元素**。

#### Element.draggable（可读写）

**`Element.draggable`属性返回一个布尔值，表示当前元素是否可拖动。该属性可读写**。

#### Element.lang（可读写）

**`Element.lang`属性返回当前元素的语言设置。该属性可读写**。

```
// HTML 代码如下
// <html lang="en">
document.documentElement.lang // "en"
```

#### Element.tabIndex

**`Element.tabIndex`属性返回一个整数，表示当前元素在 Tab 键遍历时的顺序。该属性可读写。**

**`tabIndex`属性值如果是负值（通常是`-1`），则 Tab 键不会遍历到该元素**。如果是正整数，则按照顺序，从小到大遍历。如果两个元素的`tabIndex`属性的正整数值相同，则按照出现的顺序遍历。遍历完所有`tabIndex`为正整数的元素以后，再遍历所有`tabIndex`等于`0`、或者属性值是非法值、或者没有`tabIndex`属性的元素，顺序为它们在网页中出现的顺序。

#### Element.title

**`Element.title`属性用来读写当前元素的 HTML 属性`title`。该属性通常用来指定，鼠标悬浮时弹出的文字提示框。**

### 元素状态的相关属性

#### Element.hidden（可读写）

**`Element.hidden`属性返回一个布尔值，表示当前元素的`hidden`属性，用来控制当前元素是否可见。该属性可读写**。

```
var btn = document.getElementById('btn');
var mydiv = document.getElementById('mydiv');

btn.addEventListener('click', function () {
  mydiv.hidden = !mydiv.hidden;
}, false);
```
注意，该属性与 CSS 设置是互相独立的。CSS 对这个元素可见性的设置，`Element.hidden`并不能反映出来。也就是说，这个属性并不难用来判断当前元素的实际可见性。

CSS 的设置高于`Element.hidden`。如果 CSS 指定了该元素不可见（`display: none`）或可见（`display: hidden`），那么`Element.hidden`并不能改变该元素实际的可见性。换言之，**这个属性只在 CSS 没有明确设定当前元素的可见性时才有效。**

#### Element.contentEditable（可读写），Element.isContentEditable

HTML 元素**可以设置`contentEditable`属性，使得元素的内容可以编辑**。

```
<div contenteditable>123</div>
```
上面代码中，`<div>`元素有`contenteditable`属性，因此用户可以在网页上编辑这个区块的内容。

`Element.contentEditable`属性返回一个字符串，表示是否设置了`contenteditable`属性，有三种可能的值。该属性可读写。

> `"true"`：元素内容可编辑
> `"false"`：元素内容不可编辑
> `"inherit"`：元素是否可编辑，继承了父元素的设置

**`Element.isContentEditable`属性返回一个布尔值，同样表示是否设置了`contenteditable`属性。该属性只读。**

### Element.attributes

**`Element.attributes`属性返回一个类似数组的对象，成员是当前元素节点的所有属性节点**，详见《Attr 对象》一章。

```
var p = document.querySelector('p');
var attrs = p.attributes;

for (var i = attrs.length - 1; i >= 0; i--) {
  console.log(attrs[i].name + '->' + attrs[i].value);
}
```
上面代码遍历`p`元素的所有属性。











