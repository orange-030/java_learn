# CSS

CSS 是层叠样式表（Cascading Style Sheets）的简称，也称之为CSS样式表或级联样式表。

HTML只关心**内容的语义**，CSS则关注于**网页内容的外观与布局**。

CSS主要用于设置HTML页面中的文本内容（字体、大小、对齐方式等）、图片的外形（宽高、边框样式、边距等）以及版面的布局和外观显示样式。

主要的部分构成：选择器以及一条或多条声明。



## CSS引入方式

### 内部样式表

内部样式是写到html页面内部，是将所有的CSS代码抽取出来，单独放到个`<style>`标签中。



### 行内样式表

```
<div style="color:red；font-size:12px;">今天不上课</div>
```



### 外部样式表 ✔

实际开发中使用外部样式表，适合于样式比较多的情况。核心是：样式单独写到CSS文件中，之后把CSS文件引入到HTML 页面中使用。

```html
# 在HTML中引用CSS文件
<link rel="stylesheet" href="css/style.css">
```

| 属性 | 作用                                                         |
| ---- | :----------------------------------------------------------- |
| rel  | 定义当前文档与被链接文档之间的关系。<br>指定“styleshesheet"，表示被链接的文档是一个css文件。 |
| href | 定义所链接外部样式表文件的URL，可以是相对路径，也可以是绝对路径。 |



# 选择器

## 基础选择器

基础选择器是由单个选择器组成的，包括：标签选择器、类选择器、id选择器和通配符选择器



### 标签选择器

```html
    <style>
        p {
            color: green;
            font-size: 12px
        }
        div {
            color:pink
        }
    </style>
```



### 类选择器

```html
<style>
    .red {
        color:red;
    }
    .bg {
        background-color: pink;
    }
</style>


<ul>
    <li class="red bg">北京</li>
    <li>上海</li>
</ul>
```



### id选择器

id选择器可以为标有特定id的HTML元素指定特定的样式。

HTML元素以id属性来设置id选择器，CSS中id选择器以“#”来定义

id选择器只能调用一次，也就是说页面中只有唯一的元素可以调用某个id选择器

```html
<style>
    #blue {
        color: blue;
    }
</style>
<div id="blue">你好啊</div>
```



### 通配符选择器

在CSS中，通配符选择器使用 `*`定义，它表示选取页面中所有元素（标签）。

```html
<style>
    * {
        font-size: 30px;
    }
</style>
```





## 复合选择器

复合选择器是建立在基础选择器之上，对基本选择器进行组合形成的。



### 后代选择器

后代选择器又称为包含选择器，可以选择父元素里面子元素。其写法就是把外层标签写在前面，内层标签写在后面，中间用空格分隔。当标签发生嵌套时，内层标签就成为外层标签的后代。

```html
<style>
    ol li {
        color: pink;
    }
    ol li a {
        color: aqua;
    }
</style>
```



### 子元素选择器

子元素选择器（子选择器）只能选择作为某元素的最近一级子元素。

```html
<style>
    .nav>a {
       color: aqua;
    }
</style>
```



### 并集选择器

并集选择器可以选择多组标签同时为他们定义相同的样式。通常用于集体声明，任何形式的选择器都可以作为并集选择器的一部分。

```html
<style>
    div,
    p,
    .pig li {
        color:blue;
    }
</style>
```



### 伪类选择器

1. 链接伪类选择器

```css
a:link		/*选择所有未被访问的链接*/
a:visited	/*选择所有已被访问的链接*/
a:hover		/*选择鼠标指针位于其上的链接*/
a:active	/*选拌活动链接（鼠标按下末弹起的链接）*/
```

2. focus 伪类选择器

focus伪类选择器用于选取获得焦点的表单元素。

焦点就是光标，一般情况`<input>`类表单元素才能获取，因此这个选择器也主要针对于表单元素来说。

```html
<style>
    input:focus {
        background-color: #c8eeff;
    }
</style>
```





# 元素属性

## 字体属性

```
font-family: "方正宋刻本秀楷简体", Consolas, 'Times New Roman';
font-size: 16px;
font-weight: normal(400) | bold(700) | bolder | lighter | number;
font-style: normal | italic | 
```

```css
// 复合写法
font: font-style font-weight font-size/line-height font-family;
font: normal 400 18px 'Microsoft YaHei';
```



## 文本属性

```markdown
color: red | #ff0000✔ | rgb(255,0,0)
text-align: center | left | right
text-decoration: none | underline | overline | line-through
text-indent: 10px | 2em # 指定文本第一行的缩进，em为相对大小的单位
line-height: 26px  # 行间距
```



## 背景属性

**background-color**

一般情况下元素背景颜色默认值是transparent（透明），我们也可以手动指定背景颜色为透明色。

```
background-color: color
```

**background-image**

background-image属性描述了元素的背景图像。实际开发常见于logo或者一些装饰性的小图片或者是超大的背景图片，优点是非常便于控制位置（精灵图也是一种运用场景）

```html
background-image: url(images/logo.png)
```

**background-repeat**

```
background-repeat:repeat | no-repeat | repeat-x |repeat-y
```

**background-position**

利用background-position属性可以改变图片在背景中的位置。

```
background-position: x y;
background-position: center top;
```

- position参数可以组合使用

| 参数值   | 说明                                        |
| -------- | ------------------------------------------- |
| length   | 百分数\|由浮点数字和单位标识符维成的长度值  |
| position | top \| center \| bottom \| left \| right \| |

1. 参数是方位名词

如果指定的两个值都是方位名词，则两个值前后顺序无关，比如left top和top left效果一致

如果只指定了一个方位名词，另一个值省略，则第二个值默认居中对齐

2. 参数是精确单位

如果参数值是精确坐标，那么第一个肯定是x坐标，第二个一定是y坐标

如果参数值是精确坐标，那么第一个肯定是x坐标，第二个一定是y坐标是x坐标，另一个默认垂直居中

3. 参数是混合单位

如果指定的两个值是精确单位和方位名词混合使用，则第一个值是x坐标，第二个值是y坐标

**background-attachment**

```
background-attachment: scroll | fixed
```

| 参数   | 作用                     |
| ------ | ------------------------ |
| scroll | 背景图像是随对象内容滚动 |
| fixed  | 背景图像固定             |

**background**

background属性指用一行属性表示background相关的属性设置

```
background：背景颜色 背景图片地址 背景平铺 背景图像滚动 背景图片位置;
background:transparent url("images/logo.jpg") no-repeat fixed top;
```

**背景色透明度**

```
background: rgba(0,0,0,0.3);
```

- 最后一个参数是alpha透明度，取值范围在0~1之间

- 背景半透明是指盒子背景半透明，盒子里面的内容不受影响



## 元素的显示与隐藏

### display

display属性用于设置一个元素应如何显示。

display:none；隐藏对象

display:block；除了转换为块级元素之外，同时还有显示元素的意思（display），隐藏元素后，不再占有原来的位置。

后面应用及其广泛，搭配JS可以做很多的网页特效。



### visibility

visibility 属性用于指定一个元素应可见还是隐藏。

visibility:visible；元素可视

visibility:hidden；元素隐藏

visibility 隐藏元素后，继续占有原来的位置。



### overflow

```
overflow: visible | auto | hidden | scroll

visible: 显示溢出部分
hidden: 不显示溢出部分
scroll: 添加滚动条
auto: 溢出时添加滚动条
```



## 属性书写顺序

建议遵循以下顺序：

1. 布局定位属性：display/position/float/clear/visibility/overflow

2. 自身属性：width/height/margin/padding/border/background

3. 文本属性：color/font/text-decoration/text-align/vertical-align/white-space/break-word

4. CSS3属性：content/cursor/border-radius/box-shadow/text-shadow/background:linear-gradient...





# 元素显示模式 display

## 显示模式

元素显示模式就是元素（标签）以什么方式进行显示，比如`<div>`自己占一行，比如一行可以放多个`<span>`

HTML元素一般分为**块元素**和**行内元素**两种类型。

1. **块元素**

   `<p>`标签内不允许包含`<div>`。

2. **行内元素**

   默认宽度是本身内容的宽度，无法设置宽高；

   `<a>`中不可以包含`<a>`，但可以包含块元素，需要转换；

3. **行内块元素**

   既有行内元素的特点，又有块元素的特点。

   `<img><input><td>`

4. **总结**

   | 元素模式    | 元素排列                | 设置样式                | 默认宽度          | 包含                     |
   | ----------- | ----------------------- | ----------------------- | ----------------- | ------------------------ |
   | 块级元素    | 一行只能放一个块级 元素 | 可以设置宽度高度        | 容器的100%        | 容器级可以包含任何标 签  |
   | 行内元素    | 一行可以放多个行内 元素 | 不可以直接设置宽度 高度 | 它本身内容的宽 度 | 容纳文本或则其他行内元素 |
   | 行内块元 素 | 一行放多个行内块元 素   | 可以设置宽度和高度      | 它本身内容的宽度  |                          |

5. **显示模式的转换**

   特殊情况下，我们需要元素模式的转换，简单理解一个模式的元素需要另外一种模式的特性比如想要增加链接`<a>`的触发范围。

   ```html
   <style>
       a {
           width: 100px;
           height: 100px;
           background-color: pink;
           display: block;  <!-- 行内元素转块元素，之后width和height设置会有效 -->
       }
   </style>
   ```

   ```html
   <style>
       div {
           width: 100px;
           height: 100px;
           background-color: pink;
           display: inline;  <!-- 块元素转行内元素，之后width和height设置会无效 -->
       }
   </style>
   ```

   ```html
   <style>
       span {
           width: 100px;
           height: 100px;
           background-color: pink;
           display: inline-block;  
           <!-- 行内元素转行内块元素，之后width和height设置会有效 -->
       }
   </style>
   ```

   

**垂直居中**

令行高等于容器的高度，则文本会处于容器的垂直居中位置。

```
line-height = height (块元素的高)
```

![](http://store.secretcamp.cn/uPic/line-height2021053114544416224440842ZWGCW2ZWGCW.png)





## 三大特性

**层叠性**

相同选择器设置相同的样式，此时一个样式就会覆盖另一个冲突的样式，**后赋值的属性覆盖先赋值的属性**

**继承性**

子标签会继承父标签的某些样式，如文本颜色和字号，主要是和文字相关的样式。

**优先级**

当同一个元素指定多个选择器，就会有优先级的产生。

- 选择器相同，则执行层叠性

- 选择器不同，则根据**选择器权重**执行

| 选择器               | 选择器权重 |
| -------------------- | ---------- |
| 继承或者 *           | 0,0,0,0    |
| 元素选择器           | 0,0,0,1    |
| 类选择器，伪类选择器 | 0,0,1,0    |
| ID选择器             | 0,1,0,0    |
| 行内样式style=""     | 1,0,0,0    |
| limportant重要的     | 无穷大     |

权重叠加：如果是复合选择器则会有权重叠加，需要计算权重。



## 网页布局

一个完整的网页，是标准流、浮动、定位一起完成布局的，每个都有自己的专门用法。

### 标准流

所谓的标准流：就是标签按照规定好默认方式排列。

盒子上下排列或者左右排列，垂直的块级盒子显示就用标准流布局。

- 块级元素会独占一行，从上向下顺序排列。

  常用元素：div、hr、p、h1~h6、ul、ol、dl、form、table

- 行内元素会按照顺序，从左到右顺序排列，碰到父元素边缘则自动换行。

  常用元素：span、a、i、em等



### 浮动

可以让多个块级元素一行显示或者左右对齐盒子，多个块级盒子水平显示就用浮动布局。

float 属性用于创建浮动框，将其移动到一边，直到左边缘或右边缘触及包含块或另一个浮动框的边缘。



### 定位

定位最大的特点是有层叠的概念，就是可以让多个盒子前后叠压来显示。如果元素自由在某个盒子内移动就用定位布局。







# 盒子模型

盒子模型：HTML页面中的布局元素看作是一个矩形的盒子，也就是一个盛装内容的容器。

CSS盒子模型本质上是一个盒子，封装周围的HTML元素，它包括：边框、外边距、内边距、和实际内容

border 边框

content 内容

padding 内边距

margin 外边距

```
border ：border-width | border-style | border-color
```

| 属性         | 作用                   |
| ------------ | ---------------------- |
| border-width | 定义边框粗细，单位是px |
| border-style | 边框的样式             |
| border-color | 边框颜色               |



## 边框

```
border: 1px solid pink;  没有顺序
```



## 内边距

| padding复合属性               | 表达意思                                                 |
| ----------------------------- | -------------------------------------------------------- |
| padding：5px；                | 1个值，代表上下左右都有5像素内边距；                     |
| padding：5px10px；            | 2个值，代表上下内边距是5像素左右内边距是10像素；         |
| padding：5px 10px 20px；      | 3个值，代表上内边距5像素左右内边距10像素下内边距20像素； |
| padding：5px 10px 20px 30px； | 4个值，上是5像素右10像素下20像素左是30像素顺时针 1       |



## 外边距

margin属性用于设置外边距，即控制盒子和盒子之间的距离。

| 属性          | 作用     |
| ------------- | -------- |
| margin-left   | 左外边距 |
| margin-right  | 右外边距 |
| margin-top    | 上外边距 |
| margin-bottom | 下外边距 |

复合写法与padding一致

外边距可以让块级盒子**水平居中**，但是必须满足两个条件：

- 盒子必须定了宽度（width）。

- 盒子左右的外边距都设置为auto。

**嵌套块元素垂直外边距的塌陷**

对于两个嵌套关系（父子关系）的块元素，父元素有上外边距同时子元素也有上外边距，此时父元素会塌陷较大的外边距值。

**解决方案：**

①可以为父元素定义上边框。

②可以为父元素定义上内边距。

可以为父元素添加overflow:hidden。



## 清除内外边距

网页元素很多都带有默认的内外边距，而且不同浏览器默认的也不一致。因此我们在布局前，首先要清除下网页元素的内外边距。

```html
* {
	padding: 0; /*清除内边距*/
	margin: 0;  /*清除外边距*/
}
```

**表格边框**

```html
<style>
	table {
		width：500px;
		height：249px;
	}
    th{
        height: 35px;
    }
	table,td,th {
		border：1px solid;
		border-collapse:collapse;   <!-- 合并相邻的边框 -->
        text-align: center;
	}
</style >
```



## 圆角边框

border-radius属性用于设置元素的外边框圆角。如果要做圆角矩形，将border-radius设置为高度的一般

`border-radius: 15px | 25%`

```html
<style>
    .box {
        width: 300px;
        height: 150px;
        background: #c8eeff;  
        border-radius: 15px;   /* 圆角边框 */
    }
</style>
```



## 盒子阴影

CSS3中新增了盒子阴影，我们可以使用box-shadow属性为盒子添加阴影。

`box-shadow: h-shadow v-shadow blur spread color inset;`

| 值       | 描述                                     |
| -------- | ---------------------------------------- |
| h-shadow | 必需。水平阴影的位置，允许负值。         |
| v-shadow | 必需。垂直阴影的位置，允许负值。         |
| blur     | 可选，模糊距离。                         |
| spread   | 可选。阴影的尺寸。                       |
| color    | 可选。阴影的颜色。请参阅 CSS 颜色值。    |
| inset    | 可选，将外部阴影（outset）改为内部阴影。 |



## 文字阴影

在CSS3中，我们可以使用text-shadow属性将阴影应用于文本。

`text-shadow:h-shadow v-shadow blur color;`

| 值       | 描述                                |
| -------- | ----------------------------------- |
| h-shadow | 必需。水平阴影的位置。允许负值。    |
| v-shadow | 必需。垂直阴影的位置。允许负值。    |
| blur     | 可选。模糊的距离。                  |
| color    | 可选。阴影的颜色。参阅 CSS 颜色值。 |



# 浮动 float

## 浮动属性

| 属性值 | 描述                 |
| ------ | -------------------- |
| none   | 元素不浮动（默认值） |
| left   | 元素向左浮动         |
| right  | 元素向右浮动         |



## 浮动的特性

- 浮动元素会脱离标准流（脱标）

- 浮动的元素会一行内显示并且元素顶部对齐3.浮动的元索会具有行内块元索的特性

- 对行内元素添加浮动会使其具有行内块元素的特性



## 清除浮动

一般而言，父级元素不设置高度时，高度由随内容增加自适应高度。当父级元素内部的子元素全部都设置浮动之后，子元素会脱离标准流，不占位，父级元素检测不到子元素的高度，父级元素高度为0。

- 清除浮动的本质是清除浮动元素造成的影响
- 如果父盒子本身有高度，则不需要清除浮动
- 清除浮动之后，父级就会根据浮动的子盒子自动检测高度。父级有了高度，就不会影响下面的标准流了

```css
选择器 {clear: 属性值;}
```

| 属性值 | 描述                                       |
| ------ | ------------------------------------------ |
| left   | 不允许左侧有浮动元素（清除左侧浮动的影响） |
| right  | 不允许右侧有浮动元素（清除右侧浮动的影响） |
| both   | 同时清除左右两侧浮动的影响                 |

清除浮动的方法

1. 额外标签法也称为隔墙法，是W3C推荐的做法。

   新增的盒子要求必须是块级元素

   ```css
   .clear {
       clear: both;
   }
       
   <div class="clear"></div>    <!--新增的盒子要求必须是块级元素-->
   ```

   

2. 父级添加overflow属性

   可以给父级添加overflow属性，将其属性值设置为hidden、auto或scroll

   ```css
   .box {
       width: 900px;
       margin: 0 auto;
       background-color: #c8eeff;
       overflow: hidden;
   }
   ```

   

3. 父级添加after伪元素

   ```css
   .clearfix:after {
       content: "";
       display: block;
       height: 0;
       clear: both;
       visibility: hidden;
   }
   .clearfix {
       *zoom :1;
   }
   ```

4. 父级添加双伪元素

   ```css
   .clearfix:before,.clearfix:after {
       content: "";
       display: table;
   }
   .clearfix:after {
       clear:both
   }
   .clearfix {
       *zoom :1;
   }
   ```

   

# 定位 position

浮动可以让多个块级盒子一行没有缝隙排列显示，经常用于横向排列盒子。

定位则是可以让盒子自由的在某个盒子内移动位置或者固定屏幕中某个位置，并且可以压住其他盒子。

定位：将盒子定在某一个位置，所以定位也是在摆放盒子，按照定位的方式移动盒子。

`定位 = 定位模式 + 边偏移`

**定位模式**用于指定一个元素在文档中的定位方式。**边偏移**则决定了该元素的最终位置。



## 定位模式

定位模式决定元素的定位方式，它通过CSS的position属性来设置，其值可以分为四个：

| 值       | 语义     |
| -------- | -------- |
| static   | 静态定位 |
| relative | 相对定位 |
| absolute | 绝对定位 |
| fixed    | 固定定位 |

**静态定位**

```
选择器 { position: static;}
```

- 静态定位按照标准流特性摆放位置，它没有边偏移

- 静态定位在布局时很少用到

**相对定位**

```
选择器 { position: relative;}
```

- 相对定位是元素在移动位置的时候，是相对于它原来的位置
- 相对定位之后，原标准流位置继续占有

**绝对定位**

```
选择器 { position: absolute;}
```

- 如果没有祖先元素或者祖先元素没有定位，则以浏览器为准定位（Document文档）。
- 如果祖先元素有定位（相对、绝对、固定定位），则以**最近一级的有定位的祖先元素**为参考点移动位置。
- 绝对定位不再占有原先的位置。（脱标）

**子绝父相：子级使用绝对定位，父级则需要相对定位**

- 子级绝对定位，不会占有位置，可以放到父盒子里面的任何一个地方，不会影响其他的兄弟盒子

- 父盒子需要加定位限制子盒子在父盒子内显示
- 父盒子布局时，需要占有位置，因此父亲只能是相对定位。

**固定定位**

固定定位是元素固定于浏览器可视区的位置。主要使用场景：可以在浏览器页面滚动时元素的位置不会改变。

```
选择器 { position: fixed;}
```

- 以浏览器的可视窗口为参照点移动元素。跟父元素没有任何关系，不随滚动条滚动。

- 固定定位不在占有原先的位置。

**粘性定位**

```
选择器 { position: sticky;top: 10px;}
```

- 以浏览器的可视窗口为参照点移动元素（固定定位特点）

- 粘性定位占有原先的位置（相对定位特点）

- 必须添加top、left、right、bottom其中一个才有效

## 7.2 边偏移

边偏移就是定位的盒子移动到最终位置。有top、bottom、left和right4个属性。

| 边偏移属性 | 示例         | 描述                                             |
| ---------- | ------------ | ------------------------------------------------ |
| top        | top：80px    | 顶端偏移量，定义元素相对于其父元素上边线的距离。 |
| bottom     | bottom：80px | 底端偏移量，定义元素相对于其父元素下边线的距离。 |
| left       | left：80px   | 左侧偏移量，定义元素相对于其父元素左边线的距离。 |
| right      | right：80px  | 右侧偏移量，定义元素相对于其父元素右边线的距离   |



## 定位叠放次序

在使用定位布局时，可能会出现盒子重叠的情况。此时，可以使用z-index来控制盒子的前后次序（z轴）

```
选择器 {z-index: 1;}
```

- 数值可以是正整数、负整数或0，默认是auto，数值越大，盒子越靠上

- 如果属性值相同，则按照书写顺序，后来居上

- 数字后面不能加单位

- 只有定位的盒子才有z-index属性



## 定位技巧

**固定定位到版心右侧**

```
left: 50%
margin-left: 1/2 * width (版心宽度的一半)
```

**绝对定位居中**

```
/* 水平居中 */
left: 50%
margin-left: - 1/2 * width (自身宽度的一半)

/* 水平居中 */
top: 50%;
margin-top: -1/2 * height (自身高度的一半)
```



## 定位特性

绝对定位和固定定位也和浮动类似。

- 行内元素添加绝对或者固定定位，可以直接设置高度和宽度，无需转化为块元素或行内块元素。

- 块级元素添加绝对或者固定定位，如果不给宽度或者高度，默认大小是内容的大小。

- 浮动元素、绝对定位（固定定位）元素的都不会触发外边距合并的问题。

- 浮动元素只会压住它下面标准流的盒子，不会压住下面标准流盒子里面的文字（图片）；但是绝对定位（固定定位）会压住下面标准流所有的内容。

  > 浮动之所以不会压住文字，因为浮动产生的目的最初是为了做文字环绕效果的，文字会围绕浮动元素





# 使用技巧

## 字体图标 

### 字体图标库

- icomoon字库 http://icomoon.io 

- 阿里iconfont字库 http://www.iconfontcn/



### 引入字体声明

```css
@font-face {
font-family：'icomoon';
src: url('fonts/icomoon.eot?7kkyc2');
src: url('fonts/icomoon.eot?7kkyc2#iefix') format('embedded-opentype'), 				url('fonts/icomoon.ttf?7kkyc2') format('truetype'), 
	url('fonts/icomoon.woff?7kkyc2') format('woff'),
	url('fonts/icomoon.svg?7kkyc2#icomoon') format('svg');
font-weight: normal;
font-style: normal;
```

### 使用字体图标



### 追加字体图标

如果工作中，原来的字体图标不够用了，我们需要添加新的字体图标到原来的字体文件中。

把压缩包里面的selection.json从新上传，然后选中自己想要新的图标，从新下载压缩包，并替换原来的文件即可。



## 精灵图

一个网页中往往会应用很多小的背景图像作为修饰，当网页中的图像过多时，服务器就会频繁地接收和发送请求图片，造成服务器请求压力过大，这将大大降低页面的加载速度。为了有效地减少服务器接收和发送请求的次数，提高页面的加载速度，出现了CSS精灵技术（CSS Sprites）。

核心原理：将网页中的一些小背景图像整合到一张大图中，这样服务器只需要一次请求就可以了。

精灵技术目的：**为了有效地减少服务器接收和发送请求的次数，提高页面的加载速度**



## 用户界面样式

### 鼠标样式

```
1i {cursor: pointer;}
```

| 属性值      | 描述     |
| ----------- | -------- |
| default     | 小白默认 |
| pointer     | 小手     |
| move        | 移动     |
| text        | 文本     |
| not-allowed | 禁止     |



### 轮廓线

```
input {outline: none;}
```



### 防止拖拽文本域

```
textarea {resize: none;}
```



## vertical-align 属性应用

CSS的vertical-align属性使用场景：经常用于设置图片或者表单（行内块元素）和文字垂直对齐。官方解释：用于设置一个元素的垂直对齐方式，但是它只针对于行内元素或者行内块元素有效。

```
vertical-align: baseline | top | middle | bottom
```

| 值       | 描述                                 |
| -------- | ------------------------------------ |
| baseline | 默认。元素放置在父元素的基线上。     |
| top      | 把元素的顶端与行中最高元素的顶端对齐 |
| middle   | 把此元素放置在父公素的中部。         |



## 溢出的文字省略号

### 单行溢出

```css
/*1.文字显示不开不自动换行 */
white-space:nowrap；
/*2.溢出的部分隐藏起来 */
overflow:hidden；
/*3.文字溢出的时候用省略号来显示 */
text-overflow:ellipsis; 
```



### 多行溢出

多行文本溢出显示省略号，有较大兼容性问题，适合于webKit浏览器或移动端

```css
overflow: hidden;
text-overflow: ellipsis;
/*弹性伸缩盒子模型显示*/
display:-webkit-box;
/*限制在一个块元素显示的文本的行数*/
-webkit-line-clamp: 2;
/*设置或检索伸缩盒对象的子元素的排列方式*/
-webkit-box-orient: vertical;
```





