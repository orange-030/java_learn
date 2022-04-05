# JavaScript

##  浏览器执行JS

浏览器分成两部分，渲染引擎和JS引擎

- 渲染引擎：用来解析HTML与CSS，俗称内核，比如chrome浏览器的blink，老版本的webkit

- JS引擎：也称为JS解释器。用来读取网页中的JavaScript代码，对其处理后运行，比如chrome浏览器的V8

浏览器本身并不会执行JS代码，而是通过内置JavaScript引擎(解释器)来执行JS代码。JS引擎执行代码时逐行解释每一句源码(转换为机器语言)，然后由计算机去执行，所以JavaScript语言归为脚本语言，会逐行解释执行。



##  JS组成

- **ECMAScript**

  ECMAScript是由ECMA国际(原欧洲计算机制造商协会)进行标准化的一门编程语言，这种语言在万维网上应用广泛，它往往被称为JavaScript或JScript，但实际上后两者是ECMAScript语言的实现和扩展。

  ECMAScript规定了JS的编程语法和基础核心知识，是所有浏览器厂商共同遵守的一套JS语法工业标准。

- **DOM**

  DOM(Document Object Model，简称**文档对象模型**)，是W3C组织推荐的处理可扩展标记语言的标准编程接口。通过DOM提供的接口可以对页面上的各种元素进行操作(大小、位置、颜色等)。

- **BOM**

  BOM(Browser Object Model，简称BOM)是指**浏览器对象模型**，它提供了独立于内容的、可以与浏览器窗口进行互动的对象结构。通过BOM可以操作浏览器窗口，比如弹出框、控制浏览器跳转、获取分辨率等。



##  JS引入方式

**内部引入**

```js
<script>
    alert('你吼🌶么大声干什么啦')
</script>
```

**外部引入**

```js
<script src="script.js"></script>
```





##  JS注释方法

```javascript
// way1 单行注释

/*
way2 多行注释
注释一
注释二
注释三
*/
```





------

##  JS输入输出语句

| 方法             | 说明                           | 归属   |
| ---------------- | ------------------------------ | ------ |
| prompt(msg)      | 浏览器弹出输入框，用户可以输入 | 浏览器 |
| alert(msg)       | 浏览器弹出警示框               | 浏览器 |
| console.log(msg) | 浏览器控制台打印输出信息       | 浏览器 |
| console.dir(msg) | 查看属性和方法                 | 浏览器 |



------

# 变量

**变量**是用于**存放数据的容器**。我们通过**变量名**获取数据，甚至数据可以修改。

**本质：**变量是程序在内存中申请的一块用来存放数据的空间

变量在使用时分为两步：声明变量 & 赋值

##  声明变量

### var

```javascript
//声明变量
var age;  //声明一个名称为age的变量
```

- var是一个JS关键字，用来声明变量(variable)，使用该关键字声明变量后，计算机会自动为变量分配内存空间
- age是程序员定义的变量名，我们要通过变量名来访问内存中分配的空间

### const



### let







##  变量的初始化

```javascript
var age = 18;  // 声明变量同时赋值为18

/* 初始化多个变量*/
var name = 'kzq', age = 18;
```





# 数据类型

JavaScript是一种弱类型或者说动态语言。这意味着不用提前声明变量的类型，JS的变量数据类型是只有程序在运行过程中，根据等号右边的值来确定的



##  Number

数字前面加0表示八进制，数字前面加0x表示十六进制

```js
var num1 = 010  // 十进制的8
var num2 = 0x10 // 十进制的16
```

```js
Number.MAX_VALUE  // 数字型的最大值
Number.MIN_VALUE  // 数字型的最大值
Infinity         // 无穷大
-Infinity       // 无穷小
NaN 	  // Not a Number 代表任意一个非数值
```

```js
isNaN()    // Number --> False | NaN --> True
```





##  String

JS中字符串型推荐使用单引号

| 转义符 | 说明      |
| ------ | --------- |
| `\n`   | 换行符    |
| `\\`   | 斜杠      |
| `\'`   | 单引号    |
| `\"`   | 双引号    |
| `\t`   | tab缩进   |
| `\b`   | blank空格 |

length属性：字符串长度

```js
console.log('string'.lendth)  // 6
```

```js
// js中字符串型可以和其他类型拼接，最终得到字符串类型
console.log('字符串' + true)  // 字符串true
console.log(12 + 12)  // 24
console.log('12' + 12)  // 1212
```



------

##  Boolean

js中的布尔型为true和false



------

##  Undefined & Null

var 声明变量后没有定义具体数值则为undefined

```js
var value;  
console.log(value + 'string')  // undefinedstring
console.log(value + 100)	   // NaN
```

null表示空值

```js
var value = null;
console.log(value + 'string')  // nullstring
console.log(value + 100)	   // 100
```



##  获取数据类型

**typeof方法**

```js
var string = 'string'
console.log(typeof var)
```



##  数据类型转换

### 转换成字符串类型

```js
var value = 1;
console.log(toString(value);    // 1
console.log(String(value));     // 1
console.log('String' + value);  // String1  与字符串拼接则转载字符串
```

| 方式              | 说明                         |
| ----------------- | ---------------------------- |
| toString()        | 转成字符串                   |
| String() 强制转换 | 转成字好串                   |
| 加号拼接字符串    | 和字符串拼接的结果都是字符串 |



### 转换成数值型

```js
console.log(parseInt('120px'));  // 120
console.log(parseInt('rem120px'));  // NaN
console.log('100' + 99);  // 10099
console.log('100' - 99);  // 1
```

| 方式                   | 说明                         |
| ---------------------- | ---------------------------- |
| parselnt(string)函数   | 将string类型转成整数数值型   |
| parseFloat(string)函数 | 将string类型转成浮点数数值型 |
| Number() 强制转换函数  | 将string类型转换为数值型     |
| 隐式转换(`-` `*` `/`)  | 利用算术运算隐式转换为数值型 |



### 转换成布尔型

```js
// 以下五种会转换成flase，其余全部转换成true
console.log(Boolean(''));
console.log(Boolean(0));
console.log(Boolean(NaN));
console.log(Boolean(null));
console.log(Boolean(undefined));
```

| 方式      | 说明               |
| --------- | ------------------ |
| Boolean() | 其他类型转为布尔型 |





# 运算符

##  递增运算符

### 前序递增

先自增1，再返回值

```js
var num = 10
console.log(++num + 10);  // 21
console.log(num);  // 11
```

### 后序递增

先返回原值，后自增1

```js
var num = 10
console.log(num++ + 10);  // 20
console.log(num);  // 11 
```



##  比较运算符

```js
// 判断运算符，只判断值相等，在运算时会发生隐式的数据类型转换
console.log(3 >= '10');   // false
console.log(10 == '10');  // true

// 全等运算符，要求值和数据类型都一致
console.log(10 === 10)    // true
console.log(10 === '10')  // false
console.log(10 !== '10')  // true
```



##  逻辑运算符

逻辑运算符是用来进行布尔值运算的运算符，其返回值也是布尔值。

```js
// and
console.log(3<5 && 3>2)  // true
// or
console.log(3>5 || 3<2)  // false
// not
console.log(!3);	 	 // false
console.log(!3==0);	 	 // true
console.log(!(3>5));	 // true
```



| 逻辑运算符 | 说明       | 案例            |
| ---------- | ---------- | --------------- |
| &&         | 逻辑与 and | true && false   |
| \|\|       | 逻辑或 or  | true \|\| false |
| ！         | 逻辑非 not | ! true          |



##  逻辑中断

当有多个表达式（值）时，左边的表达式值可以确定结果时，就不再继续运算右边的表达式的值

### 逻辑与

- 表达式1结果为true，则直接返回表达式2
- 表达式1结果为false，则直接返回表达式1

```js
console.log(123 && 456);  // 456
console.log(0 && 456);  // 0
console.log(0 && 1 + 2 && 345 * 678);  // 0
```



### 逻辑或

- 表达式1结果为true，则直接返回表达式1
- 表达式1结果为false，则直接返回表达式2

```js
console.log(123 || 456);  // 123
console.log(0 || 456);  // 456 
console.log(0 || 1 + 2 || 345 * 678);  // 3
```



##  运算符优先级

| 优先级 | 运算符     | 顺序          |
| ------ | ---------- | ------------- |
| 1      | 小括号     | 0             |
| 2      | 一元运算符 | `++ -- !`     |
| 3      | 算数运算符 | 先`*/%`后`+-` |
| 4      | 关系运算符 | `> >= < <=`   |
| 5      | 相等运算符 | `==-=====---` |
| 6      | 逻辑运算符 | 先&&后\|\|    |
| 7      | 赋值运算符 | =             |
| 8      | 逗号运算符 | ,             |



# 控制流

##  多分支语句

```js
// 多分支语句
var value = 10
if (0 < value <= 1) {
    alert('执行语句A')
} else if (1 < value <= 3){
    alert('执行语句B')
} else if (3 < value <= 6){
    alert('执行语句C')
} else {
    alert('执行语句D')
}
```



##  三元表达式

```
// 条件表达式 ? 表达式1 ; 表达式2
```



##  switch语句

switch语句也是多分支语句，它用于基于不同的条件来执行不同的代码。当要针对变量设置一系列的特定值的选项时，就可以使用switch

num的值和case的值必须是**全等**匹配

```js
var num = 1;
switch (num) {
    case 1:
        alert(1);
        break;
    case 2:
        alert(2);
        break;
    default:
        alert('default')
}
```

##  for循环

```js
// 操作表达式最后执行
for (初始化变量;条件表达式;操作表达式) {
    // 循环体
}

// example
var i = 1;
for (var i = 1; i <= 100; i++) {
	console.log(i);    
}
```

断点调试：`浏览器--> F12 -->source -->点击行号`



##  while循环

```js
while (条件表达式) {
    // 循环体
}

// example
var num = 1;
while (num <= 100){
    console.log(num); 
    num++;
}
```



##  dowhile循环

```js
// 先执行循环体，再判断条件，满足条件则再执行循环体
do {
    // 循环体
} while(条件表达式)

// example
var num = 0;
do { 
    num++;
    console.log(num);
} while(num <= 100)    
```







# 函数

##  创建函数

```js
// 1. 声明函数

// way1 命名函数
function demo(arg1, arg2) {
 
}fu
// way2 匿名函数
var demo = function(arg1, arg2){
    
}

// 2. 调用函数
demo();
```



##  形参与实参

```js
function demo(arg1, arg2) {
    console.log(arg1 + arg2);
}

demo(3, 6, 7);  // 9
// 实参个数大于形参个数，只会取到形参个数，不会报错

demo(3);  // NaN ( 3 + undefined )
// 实参个数小于形参个数，则未接收到值的形参相当于undefined


```



##  arguments

在JavaScript中，argument是当前函数的一个内置对象。所有函数都内置了一个arguments对象，arguments对象中存储了传递的所有实参。

```js
function demo() {
    console.log(arguments)
}

demo(1,2,3)
demo(1,2,3,4,5)
```

arguments展示形式是一个伪数组，因此可以进行遍历。伪数组具有以下特点：

- 具有length属性

- 按索引方式储存数据

- 不具有数组的push, pop等方法



##  作用域

**全局作用域**

- 整个script标签  
- 单独的js文件

**局部作用域**

- 函数内部

**块级作用域**

JS在ES6中新增了块级作用域



##  预解析

JavaScript代码是由浏览器中的JavaScript解析器来执行的。

JavaScript解析器在运行JavaScript代码的时候分为两步：**预解析**和**代码执行**。

1. 预解析

   预解析js引擎会把js里面所有的var还有function提升到当前作用域的最前面

   - 变量提升，是指将所有的变量声明提升到当前作用域的最前面，但不提升赋值操作

   - 函数提升，是指将所有的函数声明提升到当前作用域的最前面，但不调用函数

2. 代码执行

   代码执行按照代码书写的顺序从上往下执行

匿名函数的声明由于存在一个赋值操作，所以函数调用必须写在函数表达式的下方，否则会报错

```js
fun();  // 报错
var fun = function() {
console.1og('hello');
}
```

一个小案例：输出结果是多少？

```js
var num = 10；
fun();
function fun() {
	console.log(num);
	var num = 20;
}

/* 预解析的实际执行顺序 */

// 变量提升 
var num;
// 函数提升
function fun() {
    var num;
    console.log(num);
    num = 20;
}
num = 10;
fun();  // 结果为undefined，输出得是函数内部的num，还没有赋值
```





# 对象

在JavaScript中，对象是一组无序的相关属性和方法的集合，所有的事物都是对象，例如字符串、数值、数组、函数等。

js中的**构造函数和对象**类似于python中的**类和实例**，js中创建对象的过程也叫做对象的实例化



##  创建对象

### 字面量创建对象

对象宇面量：就是花括号{}里面包含了表达这个具体事物（对象）的属性和方法。

```js
var obj = {}  // 创建一个空对象
var obj = {   // 创建含有属性和方法的对象
    username = 'kzq',  // 属性
    age: 18,
    sex: '男',
    sayHi: function(){  // 方法
        console.log('hi~');
    }
}
// 对象中定义的属性和方法采用键值对的形式
```

### new Object 创建对象

```js
var obj = new Object();  // 创建一个空对象
obj.name = 'kzq';
obj.age = 18;
obj.sayHi = function(){
	console.log('hi~');
}
// 对象中定义的属性和方法采用等号赋值的形式
```

### 构造函数创建对象 

前两种方式的局限性是每次只能创建一个对象

构造函数就是把我们对象里面一些相同的属性和方法抽象出来封装到函数里面

```js
function Person(name, age, sex) {
	this.name = name;
	this.age = age;
	this.sex = sex;
	this.hit = function(obj) {
		console.log('锤' + obj);
	}
}

var link = new Person('Link', 112, '男');
link.hit('猪猪怪');  // 锤猪猪怪
```



new 关键字的作用：

1. new 在内存中创建了一个空的对象
2. this 指向创建的空对象
3. 执行构造函数中的代码，给空对象添加属性和方法
4. 返回当前对象



##  调用对象的属性和方法

```js
// 调用属性
obj.age;
obj['age'];

// 调用方法
obj.sayHi();
```



##  遍历对象的属性

```js
// for in 遍历对象
for(var key in obj) {
	console.log(key, obj[key])
}
```





# 内置对象

- JavaScript中的对象分为3种：自定义对象、内置对象、浏览器对象

- 前面两种对象是JS基础内容，属于ECMAScript；第三个浏览器对象属于JS独有的

- 内置对象就是指JS语言自带的一些对象，这些对象供开发者使用，并提供了一些常用的或是最基本而必要的功能（属性和方法）

##  Math

**`Math`** 是一个内置对象，它拥有一些数学常数属性和数学函数方法。`Math` 不是一个函数对象。



##  Date

```js
var date = new Date()
```



##  Array

数组是指一组数据的集合，其中的每个数据被称作元素，在数组中可以存放任意类型的元素。数组是一种将一组数据存储在单个变量名下的优雅方式。

### 创建数组

```js
// way1
var arr2 = []

// way2
var arr1 = new Array();

```

```js
var arr = new Array(2)  // 长度为2，含有两个空元素 [empty x 2] 
var arr = new Array(2, 3)  // [2, 3]
```



### 修改数组

```js
var arr = [1, 2, 3];
arr.length = 5;  // 此时增加两个undefined元素
```



### 检测是否为数组

```js
console.log(arr instanceof Array);  // True
```



### 添加&删除元素

```js
var arr = [1, 2, 3];
arr.push(4, 'pink');  // [1,2,3,4,'pink']  返回值为添加后的数组长度
arr.unshift('red', 'blue')  // ['red', 'blue', 1,2,3,4,'pink']  返回值为添加后的数组长度
arr.pop()  // ['red','blue',1,2,3,4]  返回值为删除的元素
arr.shift()  // ['blue',1,2,3,4]  返回值为删除的元素

```



### 数组排序

```js
var arr = [3, 5, 2, 6, 10, 1, 32];

arr.reverse();
arr.sort();  // [1, 10, 2, 3, 32, 5, 6]
arr1.sort(function(a,b) {
	return a-b  
})  		 //   [1, 2, 3, 5, 6, 10, 32]
```



### 索引

```js
var arr = ['blue', 'green', 'red', 'yellow', 'pink', 'blue']
arr.indexOf('purple')   // -1 不存在
arr.indexOf('blue')     // 0 从索引1开始查
arr.indexOf('blue', 1)  // 5 从索引1开始查
arr.lastlndex('blue')   // 5 反向查询
```



### 去重

```js
var arr = [1, 2, 3]
arr.toString()  // 1,2,3
arr.join('|')   // 1|2|3
```



### 切片

```js
var arr = [1, 2, 3, 4, 5]
arr.slice(0, 2)  	// [1, 2] 返回切片后的新数组 
arr 				// [1, 2, 3, 4, 5]  
arr.splice(0, 2)  	// [1, 2] 返回被切除的数字
arr 				// [3, 4, 5]  影响原数组
```



##  String

基本包装类型：就是把简单数据类型包装成为了复杂数据类型

```js
// 初始化
var str = 'andy'
// 基本包装类型
var temp = new string('andy');
str = temp
```

 除了`String`，基本包装类型还有`Number`和`Boolean`



### 索引方法

```js
var str = 'apple'
str.charAt(3)     // l   返回相应索引号的字符
str.charCodeAt(0) // 97	 返回相应索引号的ASCII码
str[0]			  // 'a'
```



### 拼接方法

```js
var str = 'apple'
str.concat('?')  // apple?
str.substr(1, 2) // pp  从索引1开始取两个字符
str.split()
```



### 切片方法

```
str.trim();
str.trimLeft();
```



## JSON

```javascript
let products = [
    {name: 'macbook', price: 9998},
    {name: 'iphone', price: 8999},
    {name: 'ipad pro', price: 6999}
]

JSON.stringify(products)  // 对象转为JSON字符串
JSON.parse(JSON.stringify(products))  // JSON字符串转为对象
```



# JS内存分析

简单类型又叫做基本数据类型或者值类型，复杂类型又叫做引用类型。

值类型：简单数据类型/基本数据类型，在存储时变量中存储的是值本身，因此叫做值类型。

其中null比较特殊

```js
var timer = null;
console.1og(typeof timer) // 返回的是一个空object
```

引用类型：复杂数据类型，在存储时变量中存储的仅仅是地址（引用），因此叫做引用数据类型通过new关键字创建的对象（系统对象、自定义对象），如Object、Array、Date等。

##  堆和栈

堆栈空间分配区别：

- 栈：存储简单数据类型，由操作系统自动分配与释放存放函数的参数值、局部变量的值等。其操作方式类似于数据结构中的栈；

- 堆：存储复杂数据类型，一般由程序员分配释放，若程序员不释放，由垃圾回收机制回收。

  对于复杂数据类型，首先在栈中存放地址（十六进制），指定堆中的数据。



##  底层参数传递机制

**简单数据类型**

`x=10`在栈中开辟一块内存地址保存变量`x`的值10

调用fn赋值`a=x`在栈中开辟一块内存地址保存变量`a`的值10

`a++`栈中变量a的值增加1

```js
function fn(a) {
	a++;
	console.log(a);
}

var x = 10;
fn(x)
console.log(x)

/*
11
10
*/
```



**复杂数据类型**

堆中开辟一块内存空间保存`Person`对象；栈中内存开辟一块内存空间保存`Person`对象的地址；

调用trans函数赋值`x=p`，此时栈中会新开辟一块内存保存地址，这个地址与p指向的地址相同，且指向同一个Person对象。

```js
function Person(name) {
	this.name = name;
}
function trans(x) {
    console.1og(x.name);
    x.name = 'Theresa';
    console.log(x.name);
} 

var person = new Person("kiana”);
console.log(p.name);   // kiana
trans(person)
console.log(p.name);   // Theresa

/*
kiana
kiana
Theresa
Theresa
*/
```



