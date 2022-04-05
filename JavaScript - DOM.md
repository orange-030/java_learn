# DOM概述

Web APl是浏览器提供的一套操作浏览器功能和页面元素的API（BOM和DOM）。

**文档对象模型**（Document Object Model，简称DOM），是W3C组织推荐的处理可扩展标记语言（HTML或者XML）的标准编程接口。

W3C已经定义了一系列的DOM接口，通过这些DOM接口可以改变网页的内容、结构和样式。



# DOM树

<img src="http://store.secretcamp.cn/uPic/domTree%20.png" style="zoom:60%;float:left" />

- 文档：一个页面就是一个文档，DOM中使用document表示
- 元素：页面中的所有标签都是元素，DOM中使用element表示
- 节点：网页中的所有内容都是节点（标签、属性、文本、注释等），DOM中使用node表示

DOM将以上内容视为对象



------

#  获取元素

## 根据ID获取

```js
<div id="time">2020-08-03</div>

var element = document.getElementById('time');  // 返回一个元素对象
```

## 根据标签名获取

```js
var elements = element.getElementsByTagName(tagName)  // 字符 "*" 代表所有元素
```

```js
// 获取ol中的li
var ol = document.getElementById('ul');
var lis = ol.getElementsByTagName('li');
console.log(lis)
```

## 通过HTML5新增的方法获取

```js
// getElementsByClassName 通过类名获取元素集合
var boxs = document.getElementsByClassName('box')

// querySelector 返回选择器的第一个元素对象
var firstbox = document.querySelector('.box')  // 通过类获取需要加"."
var nav = document.querySelector('#nav')  // 获取id获取需要加"#"
var firstli = document.querySelector('li')

// querySelectorAll 返回选择器中的所有对象集合
var allbox = document.querySelectorAll('.box')
var lis = document.querySelectorAll('li')
```

## 特殊元素获取

```js
// 获取body元素
var bodyEle = document.body;
// 获取html元素
var htmlEle = document.documentElement;
```





------

#  事件基础

## 事件的三要素

事件由三部分组成：事件源、事件类型、事件处理程序

- 事件源：事件触发的对象

- 事件类型：鼠标点击、鼠标经过和键盘按键等

- 事件处理程序：函数

## 常用的鼠标事件 

| 鼠标事件      | 触发条件                                                     |
| ------------- | ------------------------------------------------------------ |
| onclick       | 鼠标点击左键触发                                             |
| onmouseover   | 鼠标经过触发                                                 |
| onmouseout    | 鼠标离开触发                                                 |
| onfocus       | 获得鼠标焦点触发                                             |
| onblur        | 失去鼠标焦点触发                                             |
| onmousemove   | 鼠标移动触发                                                 |
| onmouseup     | 鼠标弹起触发                                                 |
| onmousedown   | 鼠标按下触发                                                 |
| onselect      | 目标对象被开始选中时触发<br>(选中动作刚开始，尚未实质性被选中） |
| onselect      | 文本框中的文本被选中时触发                                   |
| oncontextmenu | 鼠标右键触发                                                 |

#  操作元素

## innerText和innerHTML

- `innerText`

  不识别html标签，去除空格和换行

- `innerHTML`

  识别html标签，不去除空格和换行

## 常用元素的属性操作

1. `innerText、innerHTML` 改变元素内容

2. `src、href`

3. `id、alt、title`

## 表单元素的属性操作

利用DOM可以操作如下表单元素的属性：

`type、value、checked、selected、disabled`	

## 样式属性操作

`element.style` 行内样式操作

`element.className` 类名样式操作

## 自定义属性

**获取属性值**

```js
element.attribute;    //获取属性值。
element.getAttribute('attribute');  // 获取属性值 （包括自定义属性）
```

**设置属性值**

```js
element.attribute = value;
element.setAttribute('attribute', 'value'); 

// 设置class比较特殊，直接写class即可，不需要写className
element.setAttribute('class', 'value');  
```

**移除属性**

```js
element.removeAttribute('attribute'); 
```



## H5自定义属性

自定义属性目的：是为了保存并使用数据。有些数据可以保存到页面中而不用保存到数据库中。

但是有些自定义属性很容易引起歧义，不容易判断是元素的内置属性还是自定义属性。

H5给我们新增了自定义属性：

H5规定自定义属性`data-`开头做为自定义属性名

```html
<div data-index="2" data-date="2020-01-01" data-list-nama = 'dmz' class="test"></div>
```

```js
console.log(test.getAttribute('data-index'));  // 2
console.log(test.dataset.index);  			   // 2
console.log(test.dataset.date);	 			   // 2020-01-01
// 多个"-"连接需要用驼峰命名法获取值(少用)
console.log(test.dataset.listName);            // dmz	
// dataset是一个集合里面存放了所有以data开头的自定义属性
console.log(test.dataset);  // DOMStringMap {date: "2020-01-01", index: "2"}

```

```
DOMStringMap {date: "2020-01-01", index: "2"}
```





------

#  节点操作

## 节点操作的优势

获取元素通常使用两种方式：

**1. 利用DOM提供的方法获取元素**

- document.getElementByld

- document.getElementsByTagName

- document.querySelector

- 逻辑性不强、繁琐

**2. 利用节点层级关系获取元素**

- 利用父子兄节点关系获取元素

- 逻辑性强，但是兼容性稍差

## 节点概述

节点至少拥有nodeType（节点类型）、nodeName（节点名称）和nodeValue（节点值）这三个基本属性。

- 元素节点 nodeType为1（节点操作的主要对象）

- 属性节点 nodeType为2

- 文本节点nodeType为3（文本节点包含文字、空格、换行等）

## 父级节点

```js
// parentNode属性可以返回某节点的最近的父节点，如果没有父节点则返回null
node.parentNode
```

## 子节点

```js
// 返回所有的元素节点和文本节点
parentNode.childNodes
// 返回所有的元素节点
parentNode.children ✔
// 返回第一个子节点，包括文本节点
parentNode.firstChild
//返回第一个子元素节点(>IE9)
parentNode.firstElementChild✔
// 返回最后一个子节点，包括文本节点
parentNode.lastChild
// 返回最后一个子元素节点(>IE9)
parentNode.lastElementChild✔
```

## 兄弟节点

```js
// 返回下一个兄弟节点，包括文本节点
node.nextSiblings
// 返回下一个元素兄弟节点
node.nextElementSiblings✔
// 返回下一个兄弟节点，包括文本节点
node.previousSiblings
// 返回下一个元素兄弟节点
node.previousElementSiblings✔
```

## 创建节点

```js
var li = document.createElement('li');
```

## 添加节点

node.appendchild方法将一个节点添加到指定父节点的子节点列表末尾，类似于CSS的after伪元素。

```js
node.appendchild(child);
```

node.insertBefore方法将一个节点添加到指定父节点的子节点前面，类似于CSS的before伪元素。

```js
node.insertBefore(child, element);
```

## 删除节点

```js
// 删除父节点中的子节点
node.removeChild(child);
```

## 复制节点

```js
// 复制节点  （复制后需要添加节点）
// 参数为false，浅拷贝 --> 不复制子节点 
// 参数为true， 深拷贝 --> 复制子节点 
node.cloneNode()
```



------

#  事件高级

## 注册事件

| 传统注册方式                             | 方法监听注册方式                       |
| ---------------------------------------- | -------------------------------------- |
| 利用on开头的事件onclick                  | addEventListener()                     |
| 同一个元素同一个事件只能设置一个处理函数 | 同一个元素同一个事件可以注册多个监听器 |

### addEventListener✔

```js
eventTarget.addEventListener(type, listener[, usecapture])

btn.addEventListener('click', function() {
    console.log('test'); 
})
```

`eventrarget.addEventListener`方法将指定的监听器注册到eventrarget（目标对象）上，当该对象触发指定的事件时，就会执行事件处理函数。

- type：事件类型字符串，比如click、mouseover，**注意不需要加on**

- listener：事件处理函数，事件发生时，调用该监听函数

- useCapture：可选参数，布尔值，false或置空表示冒泡阶段，true表示捕获阶段

### attachEvent 

```js
// 适用于IE9之前
eventTarget.attachEvent(eventNamewithon, callback)
```

eventNameWithOn：事件类型字符串，比如onclick、onmouseover，这里要带on

callback：事件处理函数，当目标触发事件时回调函数被调用

## 删除事件

### 传统事件方式

```js
eventTarget.onclick = null;
```

### 方法监听方式

```js
eventTarget.removeEventListener(type, listener[, usecapture]);
```

如果要移除事件，注册时间时不能使用匿名函数

```js
// example
btn = document.querySelector('btn');
btn.addEventListener('click', fn)
function fn() {
    alert('test');
    btn.removeEventListener('click', fn);  // 移除事件
}
```

## DOM事件流

事件流描述的是从页面中接收事件的顺序。

事件发生时会在元素节点之间按照特定的顺序传播，这个传播过程即DOM事件流。

<img src="C:\Users\11200\Desktop\Notebook\Image\JavaScript\事件流.png" alt="事件流"  />

1. Js代码中只能执行捕获或者冒泡其中的一个阶段。
2. onclick和attachEvent只能得到冒泡阶段。
3. addEventListener(type, listener[, usecapture])第三个参数如果是true，表示在事件捕获阶段调用事件处理程序；如果是false或者为空，表示在事件冒泡阶段调用事件处理程序。
4. 实际开发中很少使用事件捕获，更关注事件冒泡。
5. 有些事件是没有冒泡的，比如onblur、onfocus、onmouseenter、onmouseleave

## 事件对象

event对象代表事件的状态，比如键盘按键的状态、鼠标的位置、鼠标按钮的状态。

简单理解：事件发生后，跟事件相关的一系列信息数据的集合都放到这个对象里面，这个对象就是事件对象event，它有很多属性和方法。

```js
// 注册事件时，event对象就会被系统自动创建，并依次传递给事件监听器（事件处理函数）。
var div = document.querySelector('div');
div.addEventListener('click', function (event) {
    console.log(event)
})
```

**常见的属性和方法**

| 事件对象属性方法    | 说明                                     |
| ------------------- | ---------------------------------------- |
| e.target            | 返回触发事件的对象                       |
| e.srcElement        | W 返回触发事件的对象（ie6-8）            |
| e.type              | 返回事件的类型比如click mouseover 不带on |
| e.preventDefault()  | 该**方法**阻止默认事件，例如阻止链接跳转 |
| e.returnValue       | 该**属性**阻止默认事件（ie6-8）          |
| e.stopPropagation() | 该**方法**阻止冒泡                       |
| e.cancelBubble      | 该**属性**阻止冒泡（ie6-8使用）          |

## 事件委托

**事件委托**

事件委托也称为事件代理，在jQuery里面称为事件委派。

**事件委托的原理**

不是每个子节点单独设置事件监听器，而是事件监听器设置在其父节点上，然后利用冒泡原理影响设置每个子节点。



## 鼠标事件

event对象代表事件的状态，跟事件相关的一系列信息的集合。现阶段我们主要是用鼠标事件对象MouseEvent 和键盘事件对象 KeyboardEvent。

### 常用的鼠标事件

[常用的鼠标事件](#常用的鼠标事件 ♥)



### 鼠标事件对象

| 鼠标事件对象 | 说明                                  |
| ------------ | ------------------------------------- |
| e.clientX    | 返回鼠标相对于浏览器窗口可视区的X坐标 |
| e.clientY    | 返回鼠标相对于浏览器窗口可视区的Y坐标 |
| e.pageX      | 返回鼠标相对于文档页面的X坐标（IE9+） |
| e.pageY      | 返回鼠标相对于文档页面的Y坐标（IE9+） |
| e.screenX    | 返回鼠标相对于电脑屏幕的X坐标         |
| e.screenY    | 返回鼠标相对于电脑屏幕的Y坐标         |

## 键盘事件

### 常用键盘事件

```js
document.addEventListener('keyup', function (event) {
    console.log('弹起');
})
```

| 键盘事件   | 触发条件                                                     |
| ---------- | ------------------------------------------------------------ |
| onkeyup    | 某个键盘按键被松开时触发                                     |
| onkeydown  | 某个键盘按键被按下时触发 （识别所有键）                      |
| onkeypress | 某个键盘按键被按下时触发（不识别Ctrl、Shift、方向键等功能键） |

执行顺序：`keydown  --->  keypress  --->  keyup`

### 键盘事件对象

| 键盘事件对象属性 | 说明              |
| ---------------- | ----------------- |
| keyCode          | 返回该键的ASCII值 |

- keyup和keydown不区分大小写，keypress区分大小写





------



#  offset 偏移量

## offset 概述

offset系列相关属性可以动态的得到该元素的位置、大小等。

获得元素距离**带有定位**父元素的位置，元素自身的大小（包含padding）

注意：返回的数值都不带单位

| offset系列属性       |                                                              |
| -------------------- | ------------------------------------------------------------ |
| element.offsetParent | 返回作为该元素带有定位的父级元素，如果父级都没有定位则返回body |
| element.offsetTop    | 返回元素相对带有定位父公素上方的偏移                         |
| element.offsetLeft   | 返回元素相对带有定位父公素左边框的偏移                       |
| element.offsetWidth  | 返回自身包括padding、边框、内容区的宽度                      |
| element.offsetHeight | 返回自身包括padding、边框、内容区的高度                      |

`element.offsetParent`返回的必须是带有定位的父级元素，`element.parentNode`返回的是最近一级的父级元素。

## offset与style

| offset                                        | style                                       |
| --------------------------------------------- | ------------------------------------------- |
| offset可以得到任意样式表中的样式值            | style只能得到行内样式表中的样式值           |
| offset系列获得的数值是没有单位的              | style.width获得的是带有单位的字符串         |
| offsetWidth包含padding+border+width           | style.width获得不包含padding和border的值    |
| offsetWidth等属性是只读属性，只能获取不能赋值 | style.width是可读写属性，可以获取也可以赋值 |

