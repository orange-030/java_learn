# HTML💬

HTML 指的是 超文本标记语言（Hyper Text Markup Language），它是用来描述网页的一种语言。HTML不是一种编程语言，而是一种标记语言（markup language）。

标记语言是一套标记标签（markuptag）。

head中除了title都不会在网页中显示,title是网页标题

html不区分大小写



## Emmet

1. 生成标签直接输入标签名按tab键即可比如div然后tab键，就可以生成`<div></div>`
2. 如果想要生成多个相同标签加上*就可以了比如div\*3就可以快速生成3个div
3. 如果有父子关系的标签，可以用 `>`，比如 `ul>li` 就可以了
4. 如果有兄弟关系的标签，用`+`就可以了比如 `div+p`
5. 如果生成带有class或者id的，直接写.demo或者#two
6. 如果生成的div类名是有顺序的，可以用自增符号$，如.demo$5
7. 如果想要在生成的标签内部写内容可以用{}表示



## Web标准

Web 标准是由 W3C 组织和其他标准化组织制定的一系列标准的集合。W3C（万维网联盟）是国际最著名的标准化组织。

主要包括结构（Structure）、表现（Presentation）和行为（Behavior）三个方面。





# HTML语法💬

## 概念

1. HTML 标签是由尖括号包围的关键词，例如`<html>`。

2. HTML标签通常是成对出现的，例如`<html>`和`</html>`，我们称为双标签。标签对中的第一个标签是开始标签，第二个标签是结束标签。

3. 有些特殊的标签必须是单个标签（极少情况），例`<br/>`，我们称为单标签。

![](Image\HTML & CSS\html_01.png)

| 标签名            | 定义       | 说明                                                   |
| ----------------- | ---------- | ------------------------------------------------------ |
| `<html></html>`   | HTML标签   | 页面中最大的标签，我们称为根标签                       |
| `<head></head>`   | 文档的头部 | 注意在head标签中我们必须要设置的标签是title            |
| `<title></title>` | 文档的标题 | 让页面拥有一个属于自己的网页标题                       |
| `<body></body>`   | 文档的主体 | 元素包含文档的所有内容，页面内容基本都是放到body里面的 |



------

## 注释方法

```markdown
- way1
<!--
	第一种注释方法，这种方式在渲染后会显示在源代码中
-->

- way2
{# 第二种注释方法，不会显示在源代码中 #}

- way3
{% comment %}
	第三种注释方法
	适用于多行注释
{% endcomment %}
```



```html
<!doctype html>  <!-- 文档类型声明标签，表示采用HTML5版本显示网页 -->
<html lang="en">  <!-- 显示语言，zh-CN表示中文 -->
<head>
    <meta charset="UTF-8">  <!-- 字符集 -->
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>

</body>
</html>
```



------

## 常用标签

**标题标签**

```
<h1>一级标题</h1>
<h2>二级标题</h2>
......
<h6>六级标题</h6>
```

**段落标签**

```
<p>这是一个段落标签</p>
```

**换行标签**

```
<br/> br指break  
```

**文本格式化标签**

```html
<strong>加粗</strong> or <b>加粗</b>
<em>倾斜</em> or <i>倾斜</i>
<del>删除线</del> or <s>删除线</s>
<ins>下划线</ins> or <u>下划线</u>
```

**布局标签**

`<div></div>`标签单独占一行

多个`<span></span>`标签可以在一行显示

**图像标签**

src是`<img>`标签的必须属性，它用于指定图像文件的路径和文件名。

```im
<img src="url " />
```

所有属性

| 属性   | 属性值   | 说明                                             |
| ------ | -------- | ------------------------------------------------ |
| scr    | 图片路径 | 必须属性                                         |
| alt    | 文本     | 替换文本，如果图像不能正常显示，则用文字进行替换 |
| title  | 文本     | 鼠标放到图像上显示的文本                         |
| width  | 像素     | 设置图像宽度                                     |
| height | 像素     | 设置图像高度                                     |
| border | 像素     | 设置图像边框粗细                                 |

**超链接标签**

`<a href=""></a> `表示超链接标签，a表示anchor

```html
<a href="跳转目标" target="目标窗口的弹出方式"> 文本或图像 </a> 
<!-- href="#" 空链接，表示占位 -->
<!-- href="source.zip" 如果地址是一个文件，则会下载 -->
```

| 属性    | 作用                                                         |
| ------- | ------------------------------------------------------------ |
| href    | 用于指定链接目标的url地址，（必须属性）当为标签应用href属性时，它就具有了超链接的功能 |
| targer  | 用于指定链接页面的打开方式，其中_self为默认值，__blank为在新窗口中打开方式。 |
| methods | HTTP请求方法                                                 |

利用`#`可以实现超链接标签向本页面中指定位置的跳转，首先给想要跳转的位置加一个id属性，然后在a标签中的href属性中用#指明跳转位置。

```html
<a href="#跳转标识">
<h3 id="跳转标识">
```



------

##  特殊字符

`&nbsp;`空格

`&lt;`小于号

`&gt;`大于号 

`&amp;`和号



------

## 表格标签

表格主要用于显示、展示数据，因为它可以让数据显示的非常的规整，可读性非常好。特别是后台展示数据的时候，能够熟练运用表格就显得很重要。一个清爽简约的表格能够把繁杂的数据表现得很有条理。

1. `<table></table>`是用于定义表格的标签。

2. `<tr></tr>`标签用于定义表格中的行，必须嵌套在`<table></table>`标签中。

3. `<td></td>`用于定义表格中的单元格，必须嵌套在`<tr></tr>`标签中。

4. `<td>`指表格数据（table data），即数据单元格的内容。
5. `<th>`标签表示HTML表格的表头部分（table head的缩写）

```php+HTML
<table border="1" align="center" cellpadding="0" cellspacing="0" width="500">
    <thead>
    <tr>
        <th>姓名</th>
        <th>性别</th>
        <th>年龄</th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td>琪亚娜</td>
        <td>女</td>
        <td>16</td>
    </tr>
    <tr>
        <td>八重樱</td>
        <td>女</td>
        <td>18</td>
    </tr>
    </tbody>
</table>
```

| 属性名      | 属性值             | 描述                                            |
| ----------- | ------------------ | ----------------------------------------------- |
| align       | left,center、right | 规定表格相对周围元素的对齐方式                  |
| border      | "1" 或 ”           | 规定表格单元是否拥有边框，默认为“，表示没有边框 |
| cellpadding | 像素值             | 规定单元边沿与其内容之间的空白，默认1像素。     |
| cellspacing | 像素值             | 规定单元格之间的空白，默认2像素。               |
| width       | 像素或百分比       | 规定表格宽度                                    |
| height      | 像素或百分比       | 规定表格高度                                    |

**表格结构标签**

使用场景：因为表格可能很长，为了更好的表示表格的语义，可以将表格分割成表格头部和表格主体两大部分。

在表格标签中，分别用：`<thead>`标签表示表格的头部区域，`<tbody>`标签表示表格的主体区域。这样可以更好的分清表格结构。

**合并单元格**

跨行合并：rowspan=”合并单元格的个数”

跨列合并：colspan=”合并单元格的个数”



------

## 列表标签

列表是用来布局的。根据使用情景不同，列表可以分为三大类：无序列表、有序列表和自定义列表。

### 无序列表

`<ul>`标签表示HTML页面中项目的无序列表，一般会以项目符号呈现列表项，列表项使用`<li>`标签定义。无序列表的基木语法格式如下：

```html
<ul>
    <li>臭豆腐</li>
    <li>俘虏</li>
    <li>老干妈</li>
</ul>
```

1. 无序列表的各个列表项之间没有顺序级别之分，是并列的。

2. `<ul></ul>` 中只能嵌套 `<li></li>` ，不允许输入其他标签或者文字。

3. `<li>` 与 `</li>` 之间相当于一个容器，可以容纳所元素。

### 有序列表

有序列表即为有排列顺序的列表，其各个列表项会按照一定的顺序排列定义。

在HTML标签中，`<ol>`标签用于定义有序列表，列表排序以数字来显示，并且使用`<li>`标签来定义列表项，有序列表的语法和无序列表相似。

```html
<ol>
    <li>臭豆腐</li>
    <li>俘虏</li>
    <li>加🍋</li>
</olhtml>
```

### 自定义列表

在HTML标签中，`<dl>`标签用于定义描述列表（或定义列表），该标签会与`<dt>`（定义项目/名字）和`<dd>`（描述每一个项目/名字）一起使用。

```html
<dl>
    <dt>关注我们</dt>
    <dd>微信</dd>
    <dd>微博</dd>
    <dd>QQ</dd>
</dl>
```

1.`<dl></dl>`里面只能包含`<dt>`和`<dd>`。

2.`<dt>`和`<dd>`个数没有限制，一个`<dt>`对应多个`<dd>`。



------

# 表单标签

在HTML中，一个完整的表单通常由表单域、表单控件（也称为表单元素）和提示信息 3 个部分构成。

## 表单域

表单域是一个包含表单元素的区域。

在HTML标签中，`<form>` 标签用于定义表单域，以实现用户信息的收集和传递。   `<form>`会把它范围内的表单元素信息提交给服务器。



| 属性   | 属性值   | 作用                                                    |
| ------ | -------- | -------------------------------------------   --------- |
| action | url地址  | 用于指定接收并处理表单数据的服务器程序的url地址。       |
| method | get/post | 用于设置表单数据的提交方式，其取值为get或post。      |
| name   | 名称     | 用于指定表单的名称，以区分同一个页面中的多个表单域。    |



## 表单控件

在表单域中可以定义各种表单元素，这些表单元素就是允许用户在表单中输入或者选择的内容控件。



### input 表单输入元素

1. type属性

   表单元素中 `<input>` 标签用于收集用户信息。在 `<input>` 标签中，`type` 是最主要的属性。

   | type属性值   | 描述                                                 |
   | ------------ | ---------------------------------------------------- |
   | button       | 可点击按钮（多数情况下，用于通过JavaScript启动脚本） |
   | radio        | 单选按钮                                             |
   | checkbox     | 多选按钮                                             |
   | file         | 输入字段和“刘览”按钮，供文件上传                     |
   | hidden       | 隐藏的输入字段                                       |
   | image        | 图像形式的提交按钮                                   |
   | password     | 密码字段。该字段中的字符被掩码                       |
   | reset        | 重置按钮，重置按钮会清除表单中的所有数据             |
   | submit       | 提交按钮，提交按钮会把表单数据发送到服务器           |
   | text         | 单行的输入字段，用户可在其中输入文本                 |
   | autocomplete | 是否自动输入提示，默认开启，关闭传递 "off"           |

   

2. 其他属性

   除 type 属性外，`<input>  ` 标签还有其他很多属性，其常用属性如下：

   - name 和 value 是每个表单元素都有的属性值，主要给后台人员使用。
   - name 表单元素的名字，要求单选按钮和复选框要有相同的 name 值。

   | 属性      | 属性值     | 描述                                              |
   | --------- | ---------- | ------------------------------------------------- |
   | id        | 用户自定义 | 作为标签的唯一标识。                              |
   | name      | 用户自定义 | 定义 input 元素的名称，可作为与后台交换数据的标识 |
   | value     | 用户自定义 | 规定 input 元素的值                               |
   | checked   | checked    | 规定此 input 元素首次加载时应当被选中             |
   | maxlength | 正整数     | 规定输入字段中的字符的最大长度                    |



2. label标签

   `<label> `标签为 input 元素定义标注

   `<label>` 标签用于绑定一个表单元素当点击 `<label>` 标签内的文本时，浏览器就会自动将焦点转到对应的表单元素上。

   ```html
   <!-- label for 和 id 的值相同 -->
   <label for="birth">生日</label> 
   <input type="text" id="birth">
   ```

   



### select下拉表单元素

```html
地点
<select>
    <option>北京</option>
    <option>上海</option>
    <option selected="selected">南京</option>
</select>
```

- `<select>`中至少包含一对`<option>`

- `<option>`中定义selected="selected”时，当前项即为默认选中项





### textarea文本域元素

```html
<textarea>文本域</textarea>
```
