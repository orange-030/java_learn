#  BOM概述

BOM（BrowserObject Model）即浏览器对象模型，它提供了独立于内容而与浏览器窗口进行交互的对象，其核心对象是window.

BOM由一系列相关的对象构成，并且每个对象都提供了很多方法与属性。

| DOM                                     | BOM                                             |
| --------------------------------------- | ----------------------------------------------- |
| 文档对象模型                            | 浏览器对象模型                                  |
| DOM就是把**文档**当做一个**对象**来看待 | 把**浏览器**当做一个**对象**来看待              |
| DOM的顶级对象是document                 | BOM的顶级对象是window                           |
| DOM主要学习的是操作页面元素             | BOM学习的是浏览器窗口交互的一些对象             |
| DOM是W3C标准规范                        | BOM是浏览器厂商在各自浏览器上定义的，兼容性较差 |



------

#  BOM的构成

window 对象是浏览器的顶级对象，它具有双重角色。

1.它是JS访问浏览器窗口的一个接口。

2.它是一个全局对象。定义在全局作用域中的变量、函数都会变成window对象的属性和方法。

在调用的时候可以省略window，前面学习的对话框都属于window对象方法

```js
// 等价
alert('hello js!');
window.alert('hello js!')
```



------

#  window对象事件

## 窗口加载事件

**window.onload**

window.onload是窗口加载事件，当文档内容完全加载完成（包括图像、脚本文件、CSS文件等）会触发该事件，然后调用处理函数。

```js
window.onload = function () {
    var btn = document.querySelector('button');
    btn.addEventListener("click", function () {
            alert('hello BOM 1');
        }
    )
}

// 与上面的写法等价，但会覆盖之前的窗口加载事件
window.addEventListener('load', function() {
    alert('hello BOM 2');
})
```

- window.onload可以把JS代码写到页面元素上方，因为onload是等页面内容全部加载完毕，再去执行处理函数。

- window.onload传统注册事件方式只能写一次，如果有多个，会以最后一个window.onload为准。

------

**DOMContentLoaded**

DOMContentLoaded 是DOM 加载完毕，不包含图片 falsh css 等就可以执行 加载速度比 load更快一些

```js
document.addEventListener('DOMContentLoaded', function () {
    alert('hello BOM 3');
})
```

## 调整窗口大小时间

window.onresize是调整窗口大小加载事件，当触发时就调用的处理函数。

- 只要窗口大小发生像素变化，就会触发这个事件。

- 可以利用这个事件完成响应式布局。

```js
window.addEventListener('load', function() {
    window.addEventListener('resize', function() {
        console.log(window.innerWidth);  // window.innerWidth表示当前屏幕的宽度
    })
})
```



#  定时器

window对象给我们提供了2个非常好用的方法-定时器。

- setTimeout()

- setlnterval()

## setTimeout()

```js
// way1
setTimeout(function () {
    alert('50已到')
}, 2000);  // 延迟2s

// way2
function calback(){
    alert('50已到') ;
}
setTimeout(calback, 2000);  // 延迟2s

// way3
setTimeout('calback()', 2000);  // 延迟2s
```

区别多个定时器

```js
var timer1 = setTimeout(calback1, 2000);
var timer2 = setTimeout(calback2, 5000);
```

**回调函数**

setTimeout()中的调用函数称为回调函数callback

普通函数是按照代码顺序直接调用。回调函数需要等待时间，到达设定时间后才去调用函数。

`element.onclick=function(){}`或者`element.addEventListener("click",fn)`中也是回调函数。

**清除定时器**

```js
var stop = document.querySelector('.stop')
timer = setTimeout(function () {
    alert('boom!!!');
}, 1000);
stop.addEventListener('click', function () {
    clearTimeout(timer)
})
```

## setInterval()

setinterval0方法重复调用一个函数，每隔一定时间，就去调用一次回调函数。

**清除定时器**

```js
var timer = setInterval(function () {console.log('create')}, 1000)  // 创建定时器
clearInterval(timer)  // 清除定时器
```



## this

**1. 全局作用域**

全局作用域或者普通函数中this指向全局对象window，全局作用域下的函数实际上是window的方法

```js
function fn() {
    console.log(this)
}
fn()  // window
window.fn()  // window
```

定时器里面的this指向window，因为全局作用域下是window调用了定时器

```js
window.setInterval(function () {
    console.log(this)
}, 1000);  // window
```

**2. 方法调用**

```js
// 方法调用中，this指向调用方法者
var obj ={
    sayHi: function () {
        console.log(this); // this指向的是btn这个按钮对象
    }
}
obj.sayHi()
```

```js
var btn = document.querySelector('button');
btn.addEventListener('click', function() {
                console.log(this); // this指向的是btn这个按钮对象
            })
```

**3. 构造函数中**

```js
// 构造函数中this指向构造函数的实例
function Fun() {
    console.log(this); // this指向fun实例对象
}
var fun = new Fun(); // fun
```

#  JS执行机制

## JS是单线程执行

JavaScript语言的一大特点就是**单线程**，也就是说，同一个时间只能做一件事。这是因为Javascript是为处理页面中用户的交互，以及操作DOM而诞生的。比如对某个DOM元素进行添加和删除操作，不能同时进行。应该先进行添加，之后再删除。

## 同步和异步

为了解决单线程的缺陷，利用多核CPU的计算能力，HTML5提出WebWorker标准，允许JavaScript脚本创建多个线程。于是，JS中出现了同步和异步。

### 同步任务

同步任务都在主线程上执行，形成一个执行栈。

### 异步任务

JS的异步是通过回调函数实现的，一般而言，异步任务有以下三种类型

1、普通事件，如click、resize等

2、资源加载，如load、error等

3、定时器，包括setinterval、setTimeout等

**异步任务相关回调函数添加到任务队列中（任务队列也称为消息队列）。**

## 执行机制

1. 先执行执行栈中的同步任务。

2. 异步任务（回调函数）放入任务队列中。

3. 一旦执行栈中的所有同步任务执行完毕，系统就会按次序读取任务队列中的异步任务，于是被读取的异步任务结束等待状态，进入执行栈，开始执行。

由于主线程不断的重复获得任务、执行任务、再获取任务、再执行，所以这种机制被称为**事件循环(eventloop)**



## location对象

window对象给我们提供了一个location属性用于获取或设置窗体的URL，并且可以用于解析URL。因为这个属性返回的是一个对象，所以我们将这个属性也称为location对象。

**location对象**

| location.href     | 获取或者设置整个URL             |
| ----------------- | ------------------------------- |
| location.host     | 返回主机（域名）www.itheima.com |
| location.port     | 返回端口号 如果未写返回空字符串 |
| location.pathname | 返回路径                        |
| location.search   | 返回参数                        |
| location.hash     | 返回片段#后面内容常见于链接描点 |

**location对象方法**

| location对象方法   | 返回值                                                  |
| ------------------ | ------------------------------------------------------- |
| location.assign()  | 重定向页面  （记录历史 可以后退页面）                   |
| location.replace() | 替换当前页面  （不记录历史 不能后退页面）               |
| location.reload()  | 重新加载页面，相当于刷新，参数为true表示强制刷新Ctrl+F5 |

## navigator对象

navigator对象包含有关浏览器的信息，它有很多属性，我们最常用的是userAgent，该属性可以返回由客户机发送服务器的user-agent头部的值。

下面前端代码可以判断用户那个终端打开页面，实现跳转

```js
if((navigator.userAgent.match(/(phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i))) {
            window.location.href = "../H5/index.html"; // 跳转到手机端页面
        }
```

## history对象

window 对象给我们提供了一个history对象，与浏览器历史记录进行交互。该对象包含用户（在浏览器窗口中）访问过的URL。

| history对象方法 | 作用                                                 |
| --------------- | ---------------------------------------------------- |
| back()          | 后退功能                                             |
| forward0        | 前进功能                                             |
| go（参数）      | 前进后退功能   参数为1前进1个页面，为-1则后退1个页面 |

