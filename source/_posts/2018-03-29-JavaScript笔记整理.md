---
title: JavaScript笔记及技巧整理
date: 2018-03-29 14:50:03
tags: JavaScript
categories: 技术
---
### 写在前面

马上要面试了，重新温习一下基础，把慕课网上的前端工程师课程走了一遍，整理了一些平时不太注意到的、或是个人印象不够深刻的JavaScript相关知识，希望可以巩固一下自己的印象。也能保证自己随时可以查看这些笔记。

### 关于大小写

**JS是大小写敏感的**

### document.write()

直接向HTML输出流写内容，也将就是直接在网页中输出内容。如果要使用write方法输出一个HTML标签，则要使用`""`将标签扩起来。
	
### confirm对话框

alert()对话框经常被用于调试程序，它包含一个确定按钮。而confirm对话框包括一个确定按钮和一个取消按钮，通常用于允许用户做选择的动作，语法为`confirm(str);`。confirm的返回值，当用户点击“确定”按钮时，返回true；当用户点击“取消”按钮时，返回false。可以定义一个变量来接收confirm的返回值。
	
### prompt消息对话框

除了上述两种对话框，还有一种prompt消息对话框。它通常用于询问一些需要与用户交互的信息。弹出的消息对话框包括一个确定按钮，一个取消按钮和一个文本输入框。语法为`prompt(str1, str2);`，其中str1是要显示在消息对话框中的文本，对用户来说是不可修改的；str2是文本框中的内容，用户是可以修改的。prompt的返回值，如果用户点击确定按钮，那么文本框中的内容将作为函数返回值；如果用户点击取消按钮，则返回null。
	
### window.open()

可以查找一个已经存在或者新建的浏览器窗口。语法`window.open([URL], [窗口名称], [参数字符串])`
	
### window.close()

用于关闭窗口。直接调用则关闭本窗口，使用`<窗口对象>.close()`则可以关闭指定的窗口。窗口对象可以是在打开窗口时建立一个变量进行存储。
	
### Object.innerHTML

用于获取或是替换HTML元素的内容。Object是获取的元素对象，比如通过`document.getElementById("ID")`获取的元素。
	
### 改变HTML样式

使用`Object.style.property = new style;`来改变HTML元素的样式。注意，property是CSS样式属性，new style是属性的新值。

### 显示和隐藏

语法：`Object.style.display = value`
	
### 控制类名（className属性）

className属性设置或返回元素的class属性。语法：`Object.className = classname`
	
### 创建数组

`var arrayname = new Array()`可以用来创建一个数组。创建的新数组是空数组，没有值，如输出，则显示undefined。并且，虽然创建数组时，指定了长度，但实际上数组都是变长的，也就是说即使指定了长度，仍然可以将元素存储在规定长度以外。
	
### 函数传参

js属于弱类型语言，所以在定义方法传入参数的时候并不需要指定其类型。
	
### 事件

* 鼠标点击事件onmouseclick
* 鼠标经过事件onmouseover
* 鼠标移开事件onmouseout
* 光标聚焦事件onfocus
* 失去焦点事件onblur
* 内容选中事件onselect（当文本框或者文本域中的文字被选中时触发）
* 文本框内容改变事件onchange
* 加载事件onload（该事件在页面加载完成后立即发生，如果不使用js代码则写在body标签中）
* 卸载事件onunload（用户退出页面时触发，比如页面关闭、页面刷新等）
	
### 时间

* Date日期对象可以储存任意一个日期，并且可以精确到毫秒数（1/1000秒）。定义时间对象的语法为：`var Udate = new Date()`
* 日期对象提供了以下方法：
	+ 返回/设置年份方法get/setFullYear()
	+ getDay()返回星期（返回是0-6的数字，其中0表示星期天）
	+ get/setTime()返回/设置时间方法（单位为毫秒数，计算从1970年1月1日零时到日期对象所指的日期的毫秒数，即时间戳）
	
### 字符串对象

* .length可以获取字符串对象的长度
* toLowerCase()方法可以将所有大写字母都转为小写字母
* toUpperCase()方法则可以将所有小写字母转换为大写字母
* charAt()方法返回指定位置的字符
* indexOf方法返回指定的字符串首次出现的位置
* split()方法分割字符串为字符串数组并返回（两个参数，第一个参数separator必需，用来指定分隔符；第二个参数limit可选，用来指定分割次数）
* substring()方法用于提取字符串中介于两个指定下标之间的字符（指定开始位置的参数必需，指定结束位置的参数可选）
* substr()方法从字符串中提取从startPos位置开始指定书目的字符串，语法：`stringObj.substr(startPos,length)`，其中length可选，默认值到字符串结尾

### Math对象

* ceil()函数用于对一个数向上取整，返回的是一个大于或等于x并且与x最接近的整数，语法`Math.ceil(x)`
* 向下取整函数floor()，语法`Math.floor(x)`，返回的是一个小于或等于x并且与x最接近的整数
* 四舍五入函数round()，语法`Math.round()`
* 随机数random()，可以返回一个**大于等于**0但**小于**1的随机数，语法`Math.random()`
	
### 数组对象

* concat()方法用于连接两个或多个数组，语法：`arrayObject.concat(array1,array2...arrayN)`此方法返回一个新数组，不改变原来的数组
* join()方法用于把数组中的所有元素都放入一个字符串，元素是通过指定的分隔符进行分隔的，语法`arrayObject.join(分隔符)`，该方法返回一个字符串
* reverse()方法用于颠倒数组中元素的顺序，该方法会改变原来的数组而不会创建新数组
* slice()方法可以从已有的数组中返回选定的元素，语法`arrayObject.slice(start,end)`，其中start参数必需，end参数可选，默认到数组结束，该方法返回一个新的数组，包括从start到end（不包括end）的arrayObject中的元素，不会修改原有数组
* sort()方法使数组中的元素按照一定的顺序排列，语法`arrayObject.sort(方法函数名)`，如果不规定方法函数，则按unicode码顺序排列，如果指定方法函数，则按方法函数所指定的排序方法进行排序，这个方法函数要比较两个值，然后返回一个用于说明这两个值的相对顺序的数字，比较函数应该具有两个参数a和b，其返回值如下：
	* 若返回值<=-1，则表示a在排序后的序列中出现b之前
	* 若返回值>-1&&<1，则表示a和b具有相同的排序顺序
	* 若返回值>=1，则表示a在排序后的序列中出现在b之后

### 计时器
* 计时器有两种：
	* 一次性计时器，仅在指定的延迟时间之后触发一次
	* 间隔性触发计时器，每隔一定的时间间隔就触发一次
* setInterval()计时器执行时，从载入页面后每隔指定的时间执行代码，语法`setInterval(代码, 交互时间);`，其中，交互事件的单位是毫秒。
* clearInterval()方法用于取消由setInterval()设置的交互时间，语法`clearInterval(id_of_setInterval)`，传入的id_of_setInterval是由setInterval()返回的ID值，可以使用一个变量来接收这个值。
* setTimeout()计时器，在载入后延迟指定时间后，去执行一次表达式并且只执行一次，语法`setTimeout(代码, 延迟时间);`，其中延迟时间以毫秒为单位。
* clearTimeout()计时器，和setTimeout()计时器一起使用，用于停止计时器，语法`clearTimeout(id_of_setTimeout)`，其中传入的id_of_setTimeout是由setTimeout()返回的ID值，可以使用一个变量来接收这个值。

### History对象
* history对象记录了用户曾经浏览过的页面（URL），并可以实现与浏览器前进和后退相似的功能。
* 注意：**从窗口被打开的那一刻开始记录，每个浏览器窗口、每个标签乃至每个框架，都有自己的history对象与特定的window对象关联。**
* 语法`window.history.[属性|方法]`，其中window可以省略。
* 属性：length，返回浏览器历史列表中的URL总数量。
* 方法：
	* back()，用于加载history列表中的前一个URL。back()相当于go(-1)。
	* forward()，用于加载history列表中的后一个URL。forward()相当于go(1)。
	* go()，用于加载history列表中某个具体的页面，语法`window.history.go(number);`，其中number是指要访问的URL在history的URL列表中的**相对位置**，比如返回当前页面之后浏览过的第三个历史页面，number为3；返回当前页面之前浏览过的第二个历史页面，number为-2。

### Location对象
* location对象用于获取或设置窗体的URL，并且可以用于解析URL。
* 语法`location.[属性|方法]`
* location对象属性示意图：![location对象示意图](https://s1.ax1x.com/2018/03/30/9xFuJx.png "location对象示意图")
* location对象方法：
	* assign()，用于加载新的文档
	* reload()，重新加载当前文档
	* replace()，用新的文档替换当前文档

### Navigator对象
* navigator对象包含有关浏览器的信息，通常用于检测浏览器与操作系统的版本。
* 语法`navigator.属性`
* 属性如下![navigator属性](https://s1.ax1x.com/2018/03/30/9xFUYt.jpg "navigator属性")

### userAgent
* 用于返回用户代理头的字符串表示，就是包括浏览器版本信息等的字符串
* 语法`navigator.userAgent`

### screen对象
* screen对象用于获取用户的屏幕信息。
* 语法`window.screen.属性`
* 属性如下：![screen属性](https://s1.ax1x.com/2018/03/30/9xFDOg.jpg "screen属性")
* window.screen对象还包含有关用户屏幕的信息
	* screen.height返回屏幕分辨率的高
	* screen.width返回屏幕分辨率的宽
	* 注意，单位以像素计，window这个前缀可以省略。
* availwidth和availHeight属性是可用的宽度和高度，会减去界面特性，如任务栏。

### 文件对象模型DOM
* getElement**s**ByName()方法返回带有指定名称的节点对象的集合。
	* 语法：`document.getElement**s**ByName(name)`
	* 与getElementById()不同的地方在于这个方法使通过元素的name属性查询元素而不是通过id属性。
	* 因为文档中的name属性有可能不唯一，所以这个方法返回的是元素的数组而不是一个元素。
* getElement**s**ByTagName()方法返回带有指定标签名的节点对象的集合，返回元素的顺序是它们在文档中的顺序。
	* 语法`document.getElement**s**ByTagName(Tagname)`
	* Tagname是标签的名称，如p，a，img等标签名。
* getAttribute()方法通过元素节点的属性名称获取属性的值。
	* 语法`elementNode.getAttribute(name)`
	* elementNode是通过上述几个方法获取到的元素节点。
* setAttribute()方法增加一个指定名称和值的性属性，或者把一个现有的属性设定为指定的值。
	* 语法`elementNode.setAttribute(name,value)`
	* 类似getAttribute()方法，此方法也是只能通过元素节点对象调用的函数。

* 节点属性：
	* 在文档对象模型 (DOM) 中，每个节点都是一个对象。DOM 节点有三个重要的属性 ：
		* nodeName : 节点的名称，是只读的
			* 元素节点的 nodeName 与标签名相同
			* 属性节点的 nodeName 是属性的名称
			* 文本节点的 nodeName 永远是 #text
			* 文档节点的 nodeName 永远是 #document
		* nodeValue ：节点的值
			* 元素节点的 nodeValue 是 undefined 或 null
			* 文本节点的 nodeValue 是文本自身
			* 属性节点的 nodeValue 是属性的值
		* nodeType ：节点的类型，是只读的。以下常用的几种结点类型
			* 元素    ->      1
			* 属性    ->      2
			* 文本    ->      3
			* 注释    ->      8
			* 文档    ->      9

* childNodes用于访问选定元素节点下的所有子节点的列表，返回的值可以看作是一个数组，具有length属性。
	* 语法`elementNode.childNodes`
	* 如果选定的节点没有子节点，则该属性返回不包含节点的NodeList。
	
* firstChild属性返回‘childNodes’数组的第一个子节点，如果选定的节点没有子节点，则该属性返回NULL。
	* 语法`node.firstChild`
	* firstChild属性与elementNode.childNodes[0]效果相同。
	
* lastChild属性返回‘childNodes’数组的最后一个子节点，如果选定的节点没有子节点，则该属性返回NULL。
	* 语法`node.lastChild`
	* lastChild属性与elementNode.childNodes[elementNode.childNodes.length-1]效果相同。
	
* parentNode用于获取指定节点的父节点
	* 语法`elementNode.parentNode`
	* 父节点只能有一个
	
* 访问兄弟节点
	* nextSibling属性可以返回某个节点之后紧跟的节点（处于同一树层级中）
		* 语法`nodeObject.nextSibling`
		* 如果无此节点，则该属性返回null
	* previousSibling属性可以返回某个节点之前紧跟的节点（处于同一树层级中）
		* 语法`nodeObject.previousSibling`
		* 如果无此节点，则该属性返回null

* 插入节点
	* appendChild()用于在指定节点的最后一个子节点列表之后添加一个新的子节点
		* 语法`appendChild(newnode)`
		* 例子：
			``` 
			var otest = document.getElementById("test");
			var newnode = document.createElement("p");
			newnode.innerHTML = "This is a new p";
			otest.appendChild(newnode);
			```
		* 注意：应当由**父节点**来调用这个方法
	* insertBefore()方法用于在已有的**子节点**之前插入一个新的子节点
		* 语法`insertBefore(newnode,node);`
		* 例子：
			```
			var otest = document.getElementById("test");  
			var newnode = document.createElement("li");
			newnode.innerHTML = "PHP";
			otest.insertBefore(newnode,otest.firstChild);
			```
		* 注意：应当是**父节点**来调用这个方法，并定义在某个**子节点**之前插入新节点

* 删除节点
	* removeChild()方法用于从子节点列表中删除某个节点，如果删除成功，此方法返回被删除的节点，如果删除失败则返回NULL。
	* 语法`nodeObject.removeChild(node);`
	* 注意: 把删除的子节点赋值给 x，这个子节点不在DOM树中，但是还存在内存中，可通过 x 操作。如果要完全删除对象，给 x 赋 null 值，代码如下:
		```
		var otest = document.getElementById("div1");
		var x = otest.removeChild(otest.childNodes[1]);
		x = null;
		```

* 替换元素节点
	* replaceChild方法实现子节点（对象）的替换，返回被替换对象的引用。
	* 语法`node.replaceChild(newnode,oldnode)`
	* 注意，两个参数都是必须的，当oldnode被替换时，所有与之相关的属性内容都将被移除，并且newnode必须先被建立。
	* 需要**父节点**调用这个方法，因此可以使用`oldnode.parentNode.replaceChild(newnode,oldnode);`来进行替换。

* 创建元素节点
	* createElement()方法可以创建元素节点，此方法可以返回一个element对象。
		* 语法`document.createElement(tagName);`
	
	* createTextNode()方法创建新的文本节点，返回新创建的Text节点。
		* 语法`document.createTextNode(data);`
		* 参数：data是字符串值，可以规定此节点的文本。
		* 注意：innerHTML和createTextNode都可以把一段内容添加到一个节点中，区别是如果这段内容中有html标签（如例子中的<strong></strong>）时表现就不同了，在createTextNode中会当作文本处理，不会被浏览器解析，但用innerHTML就会被当作HTML代码处理（如你的例子中Hello会被加粗显示）。
		* 例子：
			```
			var element = document.createElement("p");
			var textnode = document.createTextNode("I love Javascript!");
			element.appendChild(textnode);
			document.body.appendChild(element);
			```

### 浏览器窗口可视区域大小

* 获得浏览器窗口的尺寸（浏览器的视口，不包括工具栏和滚动条）的方法：
	* **对于IE9+、Chrome、Firefox、Opera 以及 Safari：**
		* window.innerHeight - 浏览器窗口的内部高度
		* window.innerWidth - 浏览器窗口的内部宽度

	* **对于 Internet Explorer 8、7、6、5：**
		* 直接求
			* `document.documentElement.clientHeight`表示HTML文档所在窗口的当前高度。
			* `document.documentElement.clientWidth`表示HTML文档所在窗口的当前宽度。
		* 或者Document对象的body属性对应HTML文档的<body>标签
			* `document.body.clientHeight`
			* `document.body.clientWidth`
	* **在不同浏览器都实用的 JavaScript 方案：**
		```
		var w= document.documentElement.clientWidth(或 document.body.clientWidth);
		var h= document.documentElement.clientHeight(或 document.body.clientHeight);
		```

### 网页尺寸

* scrollHeight和scrollWidth，分别用于获取网页内容的高度和宽度
	* **针对IE、Opera:**scrollHeight 是网页内容实际高度，可以小于 clientHeight。
	* **针对NS、FF:**scrollHeight 是网页内容高度，不过最小值是 clientHeight。也就是说网页内容实际高度小于 clientHeight 时，scrollHeight 返回 clientHeight 。
	* 浏览器兼容写法
		```
		var w=document.documentElement.scrollWidth
		   || document.body.scrollWidth;
		var h=document.documentElement.scrollHeight
		   || document.body.scrollHeight;
		```
		* 注意:区分大小写。并且，scrollHeight和scrollWidth还可获取Dom元素中内容实际占用的高度和宽度。
* offsetHeight和offsetWidth，分别用于获取网页内容的高度和宽度（包括滚动条和等边线，会随窗口的显示大小改变）
	* 值：offsetHeight = clientHeight + 滚动条 + 边框。
	* 浏览器兼容性
	```
	var w= document.documentElement.offsetWidth
		|| document.body.offsetWidth;
	var h= document.documentElement.offsetHeight
		|| document.body.offsetHeight;
	```

### height、clientHeight、scrollHeight、offsetHeight区别
![height、clientHeight、scrollHeight、offsetHeight区别](https://s1.ax1x.com/2018/03/31/9xY2qJ.gif "height、clientHeight、scrollHeight、offsetHeight区别")