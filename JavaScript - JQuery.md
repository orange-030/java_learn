# 一、jQuery介绍



## 1.1 JavaScript库

仓库：可以把很多东西放到这个仓库里面。找东西只需要到仓库里面查找到就可以了。

Javascript库：即library，是一个封装好的特定的集合（方法和函数）。从封装一大堆函数的角度理解库，就是在这个库中，封装了很多预先定义好的函数在里面，比如动画animate、hide、show，比如获取元素等。

常见的JavaScript库

- jQuery

- Prototype

- YUI

- Dojo

- Ext JS

- 移动端的zepto

这些库都是对原生JavaScript的封装，内部都是用JavaScript实现的。



## 1.2 原生JS的劣势

- 原生JS不能添加多个入口函数（window.onload），后面的会覆盖前面的

- 原生js的api名字都太长太难记。

- 原生js有的时候代码冗余。

- 原生s中有些属性或者方法，有浏览器兼容问题。

- 原生js容错率比较低，前面的代码出了问题，后面的代码执行不了。

## 1.3 jQuery的优势

- jQuery可以写多个入口函数的。

- jQuery的api名字都容易记忆。

- jQuery代码简洁（隐式迭代）。

- jQuery解决了浏览器兼容问题。

- jQuery容错率较高，前面的代码出来了问题，后面的代码不受影响。

## 1.4 jQuery的版本

jQuery 1.X 兼容IE低版本浏览器，如IE6、7、8

- `jquery-3.5.1.js`是未压缩版，体积较大，方便阅读，用于测试、学习和开发。

- `jquery-3.5.1.min.js`是压缩版本，去除格式，体积小、难以阅读，用于发布。



# 二、 jQuery的基本使用

## 2.1 引入JS文件

```js
<script src="https://code.jquery.com/jquery-3.5.1.min.js">
```

## 2.2 入口函数

```js
// 等待页面DOM加载完毕再执行代码
// way1
$(document).ready(function () {
})

// way2
$(function () {
})
```

- DOM结构渲染完毕即可执行内部代码，不必等到所有外部资源加载完成，jQuery帮我们完成了封装。

- 相当于原生js中的DOMContentLoaded。

- 不同于原生js中的load 事件是等页面文档、外部的s文件、css文件、图片加载完毕才执行内部代码。

## 2.3 顶级对象$

$是jQuery的别称，在代码中可以使用jQuery代替，但一般为了方便，通常都直接使用。

$是jQuery的顶级对象，相当于原生JavaScript中的window。把元素利用包装成jQuery对象，就可以调用jQuery的方法。

```js
// 两种写法等价
$(function () {
})

jQuery(function () {
})
```

## 2.4 jQuery对象和DOM对象

### 1）区别

1. 用原生JS获取来的对象就是DOM对象

2. jQuery方法获取的元素就是jQuery对象。

3. jQuery对象本质是：利用$对DOM对象包装后产生的对象（伪数组形式存储）。

4. jQuery对象只能使用jQuery方法，DOM对象只能使用原生JS方法

### 2）转换

DOM对象与jQuery对象之间是可以相互转换的。
因为原生js比jQuery更大，原生的一些属性和方法jQuery没有给我们封装。要想使用这些属性和方法需要把jQuery对象转换为DOM对象才能使用。

```js
// DOM --> jQuery
$(DOM对象)

// jQuery -- DOM
$('div')[0]
$('div')[0].get(0)
```



# 三、jQuery API

## 3.1 选择器

```js
$("选择器")  //里面选择器直接写CSS选择器即可，但是要加引号
```

### 1）基本选择器

| 名称       |      用法       | 描述                     |
| ---------- | :-------------: | ------------------------ |
| ID选择器   |    $("#id")     | 获取指定ID的元素         |
| 全选选择器 |      $(**)      | 匹配所有元素             |
| 类选择器   |   $(".class")   | 获取同一类class的元素    |
| 标签选择器 |    $("div")     | 获取同一类标签的所有元素 |
| 并集选择器 |  $("div,p,li")  | 选取多个元素             |
| 交集选择器 | $("li.current") | 交集元素                 |

### 2）层级选择器

| 名称       | 用法       | 描述                                                         |
| ---------- | ---------- | ------------------------------------------------------------ |
| 子代选择器 | $("ul>li") | 使用>号，获取亲儿子层级的元素；注意，并不会获取孙子层级的元素 |
| 后代选择器 | $("ul li") | 使用空格，代表后代选择器，获取ul下的所有li元素，包括孙子等   |

### 3）筛选选择器

| 语法       | 用法           | 描述                                             |
| ---------- | -------------- | ------------------------------------------------ |
| :first     | $("li:first"） | 获取第一个li元素                                 |
| :last      | $("li:last"）  | 获取最后一个li元素                               |
| :eq(index) | $("li:eq(2)")  | 获取到的li远素中，选择索引为2的元素，索引从0开始 |
| :odd       | $("li:odd"）   | 获取到的li元素中，选择索引为奇数的元素           |
| :even      | $("li:even"）  | 获取到的li元素中，选择索引为偶数的元素           |

### 4）筛选方法

| 语法                  | 用法                           | 说明                                                   |
| --------------------- | ------------------------------ | ------------------------------------------------------ |
| `Parent()`✔           | $("li").parent()               | 查找最近一级的父级元素                                 |
| `children($elector)`✔ | $("ul").children("li")         | 相当于$("ul>1i")，最近一级(亲儿子)                     |
| `find(selector)`✔     | $("ul").find("li")             | 相当于$("ul li")，后代选择器                           |
| `siblings(selector)`✔ | $(".first").siblings("li")     | 查找兄弟节点，不包括自己本身                           |
| `nextAll([expr])`     | $(".first").nextAll()          | 查找当前元素之后所有的同辈元素                         |
| `prevtAll([expr])`    | $(".last").prevAll()           | 查找当前元素之前所有的同辈元素                         |
| `hasClass(class)`     | $('div').hasClass("protected") | 检查当前的元素是否含有某个特定的类，如果有，则返回true |
| `eq(index)✔`          | $("li").eq(2)                  | 相当于$("li:eq(2)")，index从0开始                      |

### 5）隐式迭代

**遍历内部DOM元素**（伪数组形式存储）的过程就叫做**隐式选代**。
简单理解：给匹配到的所有元素进行循环遍历，执行相应的方法，而不用我们再进行循环，简化我们的操作，方便我们调闹。

### 6）排他思想



## 3.2 样式操作

### 1）操作css方法

jQuery可以使用css方法来修改简单元素样式；也可以操作类，修改多个样式。

1. 参数只写属性名，则是返回属性值

```js
$(this).css("color");
```

2. 参数是属性名，属性值，逗号分隔，是设置一组样式，属性必须加引号，值如果是数字可以不用跟单位和引号

```js
$(this).css("color", "red");
```

3. 参数可以是**对象**形式，方便设置多组样式，属性名和属性值用冒号隔开，属性可以不用加引号

```js
$(this).css({"color":"white","font-size":"20px"});

$(this).css({
    width: 400,
    height: 400,
    backgroundColor: "red"  // 复合属性必须用驼峰命名法
})
```

### 2）设置类样式方法

**添加类**

```css
.current {
	background-color: "pink"
}
```

```js
$("div").addClass("current")
```

**删除类**

```js
$("div").removeClass("current")
```

**切换类**

```js
$("div").toggleClass("current")
```

**类操作与className区别**

原生JS中className会覆盖元素原先里面的类名。
jQuery里面类操作只是对指定类进行操作，不影响原先的类名。

## 3.3 效果

### 1）显示隐藏

```js
// speed: 速度  slow | normal | fast
// easing: swing | linear
// fn: 回调函数
show([speed], [easing], [fn])   // 显示
hide([speed], [easing], [fn])	// 隐藏
toggle([speed], [easing], [fn]) // 在显示和隐藏之间切换
```

### 2）滑动

```js
slideDown([speed], [easing], [fn])   // 上拉
slideUp([speed], [easing], [fn])	 // 下拉	
slideToggle([speed], [easing], [fn]) // 在上拉和下拉之间切换
```

### 3）事件切换

```
hover(over, out)
```

- over：鼠标移到元素上要触发的函数（相当于mouseenter）

- out：鼠标移出元素要触发的函数（相当于mouseleave）

- 如果只写一个参数，那么鼠标移出和移入都会触发同一个函数

### 4）动画队列

**动画队列**



**停止排队**

```js
// stop方法用于停止动画效果
stop()

// stop方法必须写在动画的前面
$(".nav>li").hover(function () {
    $(this).children("ul").stop().toggle("fast")
})
```



### 5）淡入淡出

```js
fadeIn([speed], [easing], [fn])      // 淡入
fadeOut([speed], [easing], [fn])	 // 淡出
fadeTo([speed], [easing], [fn])		 // 切换
fadeToggle([speed], [easing], [fn])	 // 修改不透明度
```

### 6）自定义动画

```js
// params: 想要更改的样式属性，以对象形式传递，必须写。
// 属性名可以不用带引号，如果是复合属性则需要采取驼峰命名法，其余参数都可以省略。
animate(params, [speed], [easing], [fn])
```

## 3.4 属性操作

### 1）固有属性

```js
prop("attribute")  // 获取属性
prop("attribute", "value")  // 设置属性
```

### 2）自定义属性

```js
attr("custom_attribute")  		   // 获取属性
attr("custom_attribute", "value")  // 设置属性
```

### 3）数据缓存data

data方法可以在指定的元素上存取数据，并不会修改DOM元素结构。一旦页面刷新，之前存放的数据都将被移除。

```js
$("span").data("username", "root");
console.log($("span").data("username"))  // root
```

**打印H5自定义属性**

```html
<span data-index=1> </span>
```

```js
console.log($("span").attr("data-index"))  // 1 
console.log($("span").data("index"))  	   // 1  不用写"data-"
```

### 4）检测是否拥有类

```js
$('div').hasClass('redColor')
```



## 3.5 内容文本值

```
html()              // 获取元素的内容
html("content")		// 设置元素的内容
text()              // 获取元素的文本内容
text("content")		// 设置元素的文本内容
$("input").val()    // 获取表单的value
$("input").val(content)  // 设置表单的value
```



## 3.6 元素操作

### 1）遍历元素

```js
// 语法1
$("div").each(function(index, domElement) {})
```

1. each方法遍历配的每一个元素，主要用DOM处理。
2. 回调函数有两个参数：index是每个元素的索引号；`domElement`是每个DOM元素对象，不是jQuery对象
3. 所以要想使用jQuery方法，需要给这个dom元素转换为jQuery对象`$(domElement)`

```js
// 语法2
$.each(object, function(index, element) {})
```

1. `$.each()`方法可用于遍历任何对象。主要用于数据处理，比如数组，对象

2. 函数有2个参数：index是每个元素的索引号，element遍历内容

### 2）创建元素

```js
// 动态创建
var li = $("<li>动态创建的li标签</li>");
```

### 3）添加元素

#### 内部添加

```html
<ul>
    <li>原来的li</li>
</ul>
```

```js
var li = $("<li>动态创建的li标签</li>");
// 把内容放入匹配元素内部最后面，类似原生appendChild.
$("ul").append(li);
// 把内容放入匹配元素内部最前面
$("ul").preappend(li);
```

内部添加的元素，生成之后是**父子关系**

#### 外部添加

```html
<div class="test"> 原来的div </div>
```

```js
var div = $("<div>动态添加的div</div>");
// 放在原来元素的后面
$(".test").after(div);
// 放在原来元素的前面
$(".test").before(div);
```

外部添加元素，生成之后是**兄弟关系**

### 4）删除元素

```js
$("ul").remove();  // 删除匹配的元素
$("ul").empty();   // 删除匹配的元素集合中的子节点
$("ul").html("");  // 清空匹配的元素内容
```



## 3.7 尺寸、位置操作

### 1）尺寸

| 语法                               | 用法                                                  |
| ---------------------------------- | ----------------------------------------------------- |
| width()/height()                   | 取得匹配元素宽度和高度值 只算 width/height            |
| innerWidth()/innerHeight()         | 取得匹配元素宽度和高度值 包含 padding                 |
| outerWidth()/outerHeight()         | 取得匹配元素宽度和高度值 包含 padding、border         |
| outerWidth(true)/outerHeight(true) | 取得匹配元素宽度和高度值 包含 padding、border、margin |

- 以上参数为空，则是获取相应值，返回的是数字型。

- 如果参数为数字，则是修改相应值。

- 参数可以不必写单位。

### 2）位置

位置主要有三个函数：`offset()`、`position()`、`scrollTop()/scrollLeft()`

# 四、jQuery事件

## 4.1 事件注册

单个事件注册

```js
$("div").click(function() {})
```



## 4.2 事件处理

### 1）事件绑定

on方法在匹配元素上绑定一个或多个事件的事件处理函数，on也可以为**动态创建**的元素绑定事件。

- events：一个或多个用空格分隔的事件类型，如”click"或"keydown”。

- selector：元素的子元素选择器。
- fn：回调函数

```
element.on(events, [selector], fn)
```

```js
// 添加多个事件
$("div").on({
        click: function () {
            $(this).toggleClass("current")
        },
        mouseenter: function () {
            console.log("~~~")
        }
    }
)
```

```js
// 多个事件使用同一个回调函数
$("div").on("mouseenter mouseleave", function () {
    $(this).toggleClass("current")
})
```

```js
// one只能触发一次时间
$("div").one("click", function() {})
```

**事件委托**

可以事件委派操作。事件委派的定义就是，把原来加给子元素身上的事件绑定在父元素身上，就是把事件委派给父元素。

```js
$("ul").on("click", "li", function () {
    alert("test")
})
```

### 2）事件解绑

```
$("div").off()         		// 解除所有时间
$("div").off("click")  		// 解除点击事件
$("ul").off("click", "li")  // 解除事件委托 
```

### 3）自动触发事件

自动触发时间需要先绑定时间

```js
// way1
$("div").click()

// way2
$("div").trigger("click")

// way3 不会触发元素的默认行为
$("div").triggerHandler("click")
```



## 4.3 事件对象

```js
element.on(events, [selector], function(event) {})
```

阻止默认行为：`event.preventDefault()`或者`return false`
阻止冒泡：`event.stopPropagation()`



# 五、其他方法

## 5.1 对象拷贝

```js
$.extend([deep], target, object1, [objectN])
```

- deep：如果设为true为深拷贝，默认为false浅拷贝
- target：要拷贝的目标对象
- object1：待拷贝到第一个对象的对象。
- objectN：待拷贝到第N个对象的对象。
- 浅拷贝是把被拷贝的对象复杂数据类型中的地址拷贝给目标对象，修改目标对象会影响被拷贝对象。
- 深拷贝，前面加true，完全克隆（拷贝的对象而不是地址），修改目标对象不会影响被考贝对象。

## 5.2 多库共存

jQuery使用`$`作为标示符，随着jQuery的流行，其他js库也会用`$`作为标识符，这样一起使用会引起冲突。

**解决方案1**

把`$`符号统一改为`jQuery`。比如`jQuery("div")`

**解决方案1**

jQuery变量规定新的名称：`var $$ = $.noConflict();`

这样就可以用`$$`代替`$`调用jQuery属性和方法了

## 5.3 jQuery插件

jQuery插件也是依赖于jQuery来完成的，所以必须要先引入jQuery文件，因此也称为jQuery插件。

插件地址

www.jq22.com

www.htmleaf.com

# 六、Ajax

## 6.1 概念

**AJAX 即 Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）**，是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。

## 6.2 实现

```js
// ajax最常用的请求参数
$.ajax({
    url: '/index/',
    type: 'post',
    data: {'username': 'alice'},
    success: function () {
        alert("完成后执行的回调函数")
    }
})
```

```
ur1: 请求地址
type:请求方式，GET、POST（1.9.0之后用method）
headers:请求头
data:要发送的数据
contentType:即将发送信息至服务器的内容编码类型（默认: "application/x-www-form-urlencoded；charset=UTF-8）
async:是否异步
timeout:设置请求超时时间（毫秒）
beforesend:发送请求前执行的函数（全局）
complete:完成之后执行的回调函数（全局）
success:成功之后执行的回调函数（全局）
error:失败之后执行的回调函数（全局）
accepts:通过请求头发送给服务器，告诉服务器当前客户端课接受的数据类型
datarype:将服务器端返回的数据转换成指定类型
"xml”:将服务器端返回的内容转换成xml格式
"text”:将服务器端返回的内容转换成普通文本格式
"htm1”:将服务器端返回的内容转换成普通文本格式，在插入DoM中时，如果包含Javascript标签，则会尝试去执行
"script":尝试将返回值当作Javascript去执行，然后再将服务器端返回的内容转换成普通文本格式
“json”:将服务器端返回的内容转换成相应的Javascript对象
“jsonp”:JSONP 格式使用JSONP 形式调用函数时，如“myur1?callback=?"，jQuery 将自动替换?为正确的函数名，以执行回调函数
```

## 6.3 Tips

阻止表单提交：

```
<form  method="post" action=""  onsubmit="return false;">
```

CSFR：

开启了CSFR时用AJAX提交会被拦截，表单提交时可以添加`{% csrf_token %}`，

如果是AJAX提交，则在`data`中添加键值对`csrfmiddlewaretoken: '{{ csrf_token }}'`