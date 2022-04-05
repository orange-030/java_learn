# Java🔥

JavaSE（Java Standard Edition）：标准版，定位在个人计算机上的应用。

JavaEE（Java Enterprise Edition）：企业版，定位在服务器端的应用。

JavaME（Java Micro Edition）：微型版，定位在消费性电子产品的应用上





## 相关概念



JVM（Java Virtual Machine）就是一个虚拟的用于执行bytecode字节码的虚拟计算机

JRE（Java Runtime Environment ）包含：Java虚拟机、库函数、运行Java应用程序所必须的文件。

JDK（Java Development Kit）：包含JRE，以及增加编译器和调试器等用于程序开发的文件。 

JDK 包含 JRE，JRE 包含 JVM，`JDK >= JRE >= JVM`

👉  [JDK 下载](https://www.oracle.com/java/technologies/javase-downloads.html)

```sh
# 查看jdk版本
java -version
```



## Java编译执行

1. 编写Java代码，保存为 `Welcome.java` ，文件名必须和类名一致

```java
public class Welcome {
	public static void main (String[] args){
		System.out.println("hello world!");
	}
}
```

  

- 关键字public被称作访问修饰符（access modifier），用于控制程序的其它部分对这段代码的访问级别。
- 关键字class的意思是类，Java是面向对象的语言，所有代码必须位于类里面。
- 一个源文件中至多只能声明一个public的类，其它类的个数不限，如果源文件中包含一个public类，源文
- 文件名必须和其中定义的`public`的类名相同，且以“java”为扩展名一个源文件可以包含多个类class。
- 正确编译后的源文件，会得到相应的字节码文件，编译器为每个类生成独立的字节码文件，且将字节码文件自动命名为类的名字且以“.class”为扩展名。
- main方法是Java应用程序的入口方法，有固定的书写格式：`public static void main(String[] args){...}`



2. 编译文件

```
javac Test.java
```

会生成一个 `Welcome.class` 文件



3. 执行文件

```
java Welcome.class
```



## 注释方法

Java注释的内容不会出现在字节码文件中

```java
//  1. 单行注释

/*
    2. 多行注释
*/

/**
    3. 文档注释
*/
```



## 环境变量

1. Linux

   ```sh
   tar -xzvf jdk-8u131-linux-x64.tar.gz
   vim ~/.bashrc
   
   # 文件中加入如下内容
   export JAVA_HOME=/home/parallels/Applications/jdk1.8.0_131 
   export PATH=$JAVA_HOME/bin:$PATH 
   ```

   

2. Windows

   新建一个变量 `JAVA_HOME` ： `C:\Program Files\Java\jdk1.8.0_261`

   将该路径配置到 `PATH` ： `%JAVA_HOME%\bin`

   不要直接配置绝对路径，如 `C:\Program Files\Java\jdk1.8.0_261\bin`，否则某些程序会出错



## Java的工作方式

1. 编写源代码（.java）
2. 编译器编译编代码，并生成字节码文件（.class）
3. JVM读取和执行字节码文件



# Java命令行工具🔥

## jstat

监听虚拟机运行状态



## jinfo

查看和修改虚拟机参数



## jmap

用于查看堆信息以及生成堆的转储快照。



## jhat

分析dump文件的命令



## jstack

堆栈追踪工具，用于生成线程快照，常用于定位死锁问题



# 常量🔥

常量是指在 Java 程序中固定不变的数据。

| 类型       | 含义                                     | 数据举例                  |
| ---------- | ---------------------------------------- | ------------------------- |
| 整数常量   | 所有的整数                               | 0，1，567，-9             |
| 小数常量   | 所有的小数                               | 0.0，-0.1，2.55           |
| 字符常量   | 单引号引起来，只能写一个字符，必须有内容 | 'a'，' '，'可'            |
| 字符串常量 | 双引号引起来，可以写多个字符，也可以不写 | "A"，"Hello"，"你好"， "" |
| 布尔常量   | 只有两个值（流程控制中讲解）             | true，false               |
| 空常量     | 只有一个值（引用数据类型中讲解）         | null                      |

使用 `final` 修饰的变量称为「符号常量」

```java
final int DECIDE = 1;
// DECIDE = 2;  // 编译错误，转常量后无法再对DECIDE进行赋值
System.out.println(DECIDE);  // 1
```



# 变量与数据类型🔥

## 概述

变量：在程序中可以变化的量称为变量。

变量本质上就是代表一个可操作的存储空间，空间位置是确定的，但是里面放置什么值不确定。我们可通过变量名来访问对应的存储空间，从而操纵这个 “存储空间” 存储的值。

Java是一种强类型语言，每个变量都必须声明其数据类型。变量的数据类型决定了变量占据存储空间的大小。比如，`int a = 3;` ，表示a变量的空间大小为4个字节。

变量作为程序中最基本的存储单元，其要素包括 **变量类型**，**变量名**和**作用域** 。变量在使用前必须对其声明，只有在变量声明以后，才能为其分配相应长度的存储空间。



## 变量的分类

- 局部变量（local variable）

  方法或语句块内部定义的变量，从属于方法，生命周期是从声明位置开始到到方法或语句块执行完毕为止。

- 成员变量（实例变量 member variable）

  方法外部、类的内部定义的变量，从属于对象，生命周期伴随对象始终。

  对于实例变量，如果声明变量后没有赋值，则会被自动初始化。

  | 实例变量的默认初始值 |          |
  | -------------------- | -------- |
  | 数据类型             | 实始值   |
  | int                  | 0        |
  | double               | 0.0      |
  | char                 | '\u0000' |
  | boolean              | false    |



- 静态变量（类变量static variable）

  使用static定义，从属于类，生命周期伴随类始终，从类加载到卸载。



## 数据类型

Java的数据类型分为两大类：
- 基本数据类型：包括 `整数` 、 `浮点数` 、 `字符` 、 `布尔` 。
- 引用数据类型：包括 `类` 、 `数组` 、 `接口` 。

 Java将内存分成两种，一种叫做栈内存，一种叫做堆内存。

在函数中定义的一些 基本类型的变量 和 对象的引用变量 都是在函数的 栈内存 中分配。 

- 当在一段代码块中定义一个变量时，java就在栈中为这个变量分配内存空间，当超过变量的作用域后，java会自动释放掉为该变量分配的内存空间，该内存空间可以立刻被另作他用。

- 堆内存用于存放由 new 创建的对象和数组，在堆中分配的内存，由java虚拟机的垃圾回收器来管理。



## 二进制表示

### 原码

原码就是该数字不进行其他操作时数字最原始的二进制表示

对于整数而言，其原码格式为最高位为符号位，该位上 1 表示负数而 0 表示正数，剩余位数为该数字的二进制表示。

```
 5 的原码： 00000000 00000000 00000000 00000101
-5 的原码： 10000000 00000000 00000000 00000101
```

对于原码来说，绝对值相等的正数和负数只有符号位不同。



### 反码

正数的反码是其本身;

负数的反码是在其原码的基础上， 符号位不变，其余各个位取反;

```
 5 的反码： 00000000 00000000 00000000 00000101
-5 的反码： 11111111 11111111 11111111 11111010
```



### 补码

正数的补码就是其本身

负数的补码是在其反码的基础上加一

```
 5 的补码： 00000000 00000000 00000000 00000101
-5 的补码： 11111111 11111111 11111111 11111011
```

⚠️ Java 中所有基本数据类型均使用该数字的补码进行表示



## 基本数据类型

### 整形

| 类型  | 占用存储空间 | 表数范围（十进制）                            |
| ----- | ------------ | --------------------------------------------- |
| byte  | 1 字节       | -128 ~ 127                                    |
| short | 2字节        | -2<sup> 15 </sup>~ 2<sup>15</sup>-1           |
| int   | 4 字节       | -2<sup>31</sup> ~ 2<sup>31</sup>-1 （约21亿） |
| long  | 8 字节       | -2<sup>63</sup> ~ 2<sup>63</sup>-1            |

整形常量的四种表示形式：

- 二进制类型：要求以 0b 或 0B 开头
- 八进制整数：要求以 0 开头
- 十进制整数：不显式指定开头则为十进制
- 十六进制数：要求 0x 或 0X 开头



#### int

int 在 Java 内部是用 32 位来表示的，而最高位是表示符号，因此真正可以表示数字的是 31 位。Java 的 int 类型取值范围是 $[-2^{31} \sim 2^{31} -1]$

`Integer.MAX_VALUE = 0x7fffffff`

二进制表示为 `01111111111111111111111111111111` ，一共 32 位，第一位为 0 表示正数

十进制表示为 `2147483647`

如果大于 2147483647 则会溢出，例如 2147483647 + 1变成 `10000000000000000000000000000000` ，变成一个负值  `-2147483648`





`Integer.MIN_VALUE = 0x80000000`

二进制表示为 `10000000000000000000000000000000` ，一共 32 位，第一位为 1 表示负数

十进制表示为 `-2147483648`

如果小于 -2147483648 则会溢出，例如 -2147483648 - 1变成  `01111111111111111111111111111111` ，变成一个正值  `2147483647`



#### long

`Long.MAX_VALUE = 0x7fffffffffffffff`

二进制表示为 `0100000000000000000000000000000000000000000000000000000000000000` ，一共 64 位，第一位为 0 表示正数

十进制表示为 `9223372036854775807`

`Integer.MIN_VALUE = 0x8000000000000000`

二进制表示为 `1000000000000000000000000000000000000000000000000000000000000000` ，一共 64 位，第一位为 1 表示负数

十进制表示为 `-9223372036854775808`



#### short



#### byte





### 浮点型

float的0-22位表示尾数，23-30位（8位）表示指数，31位表示符号位。

double的0-51表示尾数，52-62（11位）表示指数，63位表示符号位。

| 类型   | 占用存储空间 | 表数范围               | 后缀 |
| ------ | ------------ | ---------------------- | ---- |
| float  | 4字节        | -3.403E38 ~ 3.403E38   | F    |
| double | 8字节        | -1.798E308 ~ 1.798E308 | D    |

浮点数不精确，不要用于比较。`java.math` 包下面的两个有用的类：`BigInteger` 和 `BigDecimal`，这两个类可以处理任意长度的数值。 `BigInteger` 实现了任意精度的整数运算，`BigDecimal` 实现了任意精度的浮点运算。

科学计数法：

314E2 = 314&times;10<sup>2</sup>

314E-2 = 314&times;10<sup>-2</sup>





### 字符型

char 类型表示在 Unicode 编码表中的字符，char 类型占用固定的 2byte 空间来表示字符。

Unicode 编码被设计用来处理各种语言的所有文字，它占2个字节，可允许有 65536 个字符；ASCI 码占 1 个字节，可允许有 128 个字符（最高位是0），是 Unicode 编码表中前 128 个字符。

字符型还可以用 Unicode 来表示

```java
char Str = '\u0061';
```

| 转义符 | 含义？ | Unicode值 |
| ------ | ------ | --------- |
| `\b`   | 退格   | \u0008    |
| `\n`   | 换行   | \u000a    |
| `\r`   | 回车   | \u000d    |
| `\t`   | 制表符 | \u0009    |
| `\"`   | 双引号 | \u0022    |
| `\'`   | 单引号 | \u0027    |
| `\\`   | 反斜杠 | \u005c    |



### 布尔型

boolean类型有两个常量值 true 和 false ，在内存中占一位而不是一个字节，所以不可以使用0或非0的整数替代 true 和 false 。



## 引用数据类型

java的引用类型只有三种，分别是类（class）、接口（interface）、数组（array）

在堆中产生了一个数组或者对象后，还可以栈中定义一个特殊的变量，这个变量的取值等于数组或对象在堆内存中的地址，在栈中的特殊变量就变成了数组或者对象的引用变量，以后就可以在程序中使用栈内存中的引用变量来访问堆中的数组或者对象，引用变量相当于为数组或者对象起的一个别名。实际上，栈中的变量指向堆内存中的变量，这就是指针在Java中的体现。 

```java
// p就是一个引用变量
Person p = new Person();
```



## 数据类型转换

### 自动类型转换

自动转换：将 取值范围小的类型 自动提升为 取值范围大的类型 。

这里的 "大" 与 "小"，并不是指占用字节的多少，而是指表示值的范围的大小。

float（4字节）表数范围（-10<sup>38</sup> ~ 10<sup>38</sup>），比 long（8字节）表数范围（-2<sup>-63</sup> ~ 2<sup>63</sup>-1）大

整型常量可以直接赋值给byte、short、char等类型变量，而不需要进行强制类型转换，只要不超出其表数范围即可。



1）表述范围小向表数范围大转换

```java
byte、short、char ‐‐> int ‐‐> long ‐‐> float ‐‐> double
    
short a = 12;    // ✔ 合法
short a = 1234;  // ✖ 不合法 超出了表数范围
```

| 操作数1类型                          | 操作数2类型 | 转换后类型 |
| ------------------------------------ | :---------- | ---------- |
| byte，short，char                    | int         | int        |
| byte，short，char，int               | long        | long       |
| byte，short， char，int，long        | float       | float      |
| byte，short，char，int， long，float | double      | double     |



2）char向整型转换时会转为对应ASCII码

```java
char c = 'c';
int num1 = c;    // 99
int num2 = 'a';  // 97
```



3）byte、short、char 运算时直接提升为 int 

分析：

- `byte result1 = 40 + 50` ：`40` 和 `50` 是固定不变的常量，编译器确定 `40+50` 的结果不超过byte的表数范围，所以编译通过。

- `byte result = num1 + num2`，`num1` 和 `num2` 是变量，编译器不确定 `num1 + num2` 的结果，因此会将结果以int类型处理，但int类型不能赋值给 byte 类型，所以编译不通过。

```java
/*
	byte + byte → int + int → int
*/
byte num1 = 40;
byte num2 = 50;
byte result1 = 40 + 50  // 90
byte result = num1 + num2  // 编译不通过，int无法自动转换成byte     
```



### 强制类型转换

将 “大” 数据转换为 “小” 数据时，你可以使用强制类型转换。

浮点型转整型会损失小数位

```java
double x = 3.99;
int y = (int)x;  // 3
```

byte、short、char 三种类型是平级的，因此不能相互自动转换，可以使用下述的强制类型转换

```java
short i = 99; 
char c = (char)i; 
System.out.println(c); // c
```





# Java运算符🔥

## 算术运算符

### 整形运算

- 如果两个操作数有一个为 long，则结果也为 long

- 没有 long 时，结果为 int 。即使操作数全为 short、byte，结果也是 int



### 浮点运算

- 如果两个操作数有一个为double, 则结果为double
- 只有两个操作数都是float, 则结果才为float



### 整除运算

```java
System.out.println(-10/3);  // -3
```



### 取模运算

- “余数”的符号和左边的操作数相同

```java
System.out.println(-10%3);  // -1
```



### 自增运算

```java
int a = 3;
- int b = a++  // 先赋值，后自增
- int b = ++a  // 先自增，后赋值
```



### += 符号的扩展

下面的程序有问题吗？

```java
public static void main(String[] args){
	short s = 1;
	s += 1;
	System.out.println(s);
}
```

分析： `s += 1` 逻辑上看作是 `s = s + 1` ，计算结果被提升为int类型，再向short类型赋值时发生错误，因为不能将取值范围大的类型赋值到取值范围小的类型。但是，  `s = s + 1` 是进行了两次运算 ， 但 `+=` 是一个运算符，只运算一次，并带有强制转换的特点，也就是说 `s += 1` 等价于 `s = (short)(s + 1)` ，因此程序没有问题编译通过，运行结果是2。



## 赋值运算符

```java
int a = 3;
int b = 4;
a += b;   // a = a + b;
a *= b+3  // a = a * (b+3); 
```



## 关系运算符 

字符型也可以比较

```java
char a = 'a';
char b = 'c';
System.out.println(a < b)  // true 根据Unicode对应数值进行比较

System.out.println((int)a)  // 97
System.out.println(0+a)     // 97  自动转为int
```



## 逻辑运算符

- 短路与和短路或采用短路的方式。从左到右计算，如果只通过运算符左边的操作数就能够确定该逻辑表达式的值，则不会继续计算运算符右边的操作数，提高效率。

```java
System.out.println(!false);  // true
System.out.println(true^true);    // false
System.out.println(false^false);  // false
```



运算符优先级：`逻辑非 > 逻辑与 > 逻辑或`

| 运算符   | 说明 |                                             |
| -------- | ---- | ------------------------------------------- |
| 逻辑与   | &    | 两个操作数为true，结果才是true，否则是false |
| 逻辑或   | \|   | 两个操作数只要有一个是true，结果就是true    |
| 短路与   | &&   | 只要有一个为false，则直接返回false          |
| 短路或   | \|\| | 只要有一个为true，则直接返回true            |
| 逻辑非   | ！   | 取反：false为true，true为false              |
| 逻辑异或 | ^    | 相同为 false，不同为 true                   |



## 位运算符

| 运算符 | 含义                                           | 示例        |
| ------ | ---------------------------------------------- | ----------- |
| `~`    | 按位非（NOT）\|取反                            | b = ~a      |
| `&`    | 按位（AND）                                    | c = a & b   |
| `|`    | 按位或（OR）                                   | c = a \| b  |
| `^`    | 按位异或（相同为0相异为1）                     | c = a ^ b   |
| `<<`   | 将运算数的二进制整体左移指定位数，低位用0补齐  | b = a << 1  |
| `>>`   | 有符号右移动，整数高位用0补齐，负数高位用1补齐 | b = a >> 2  |
| `>>>`  | 无符号右移，不管正数还是负数，高位都用0补齐    | b = a >>> 2 |







###  << 运算

左移操作，不分正负数，舍弃高位，低位补0

```
100 << 2

 1100100
    ↓
110010000


-100 << 2

11111111111111111111111110011100
							↓
11111111111111111111111001110000
```



### >> 运算

右移操作，舍弃低位，高位补上符号位

```
100 >> 2

01100100
   ↓
 11001
 
 
-100 >> 2

11111111111111111111111110011100
					  	↓
11111111111111111111111111100111
```



### >>> 运算

无符号右移运算符操作，舍弃低位，高位补0

对于正数而言，和右移操作相同

对于负数而言，无符号右移可以将负数转为正数

```
100 >> 2

01100100
   ↓
 11001


-100 >> 2

11111111111111111111111110011100
							↓
00111111111111111111111111100111
```



## 字符串连接符

```java
System.out.println(3+"5");  // 35
```



## instanceof

查看对象是否是某个类的实例



## 三元条件运算符

```java
// 如果x为true，返回y，否则返回z
x ? y : z
```



# 控制流🔥

## if

```java
public static void main(String[] args) {
    int score = (int) (Math.random() * 100);
    System.out.println(score);
    if (score >= 90) {
        System.out.println("优秀");
    } else if (score >= 80) {
        System.out.println("良好");

    } else if (score >= 60) {
        System.out.println("及格");
    } else {
        System.out.println("不及格");
    }
}
```



## switch

```java
public static void main(String[] args) {
    int month = 2;
    switch (month) {
        case 1:
            System.out.println("一月");
            break;
        case 2:
            System.out.println("二月");
            break;
        case 3:
            System.out.println("三月");
    }
}
```



## while

```java
// 先判断再循环
public static void main(String[] args) {
    int i = 0;
    int sum = 0;
    while (i <= 100){
        sum += i;
        i += 1;
    }
    System.out.println(sum);
}
```



## dowhile

```java
// 先循环再判断
public static void main(String[] args) {
    int i = 0;
    int sum = 0;
    do {
        sum += i;
        i++;
    } while (i <= 100);
    System.out.println(sum);

}
```



## for

```java
public static void main(String[] args) {
    int sum = 0;
    for (int i = 0; i <= 100; i++) {
        sum += i;
    }
    System.out.println(sum);
}
```



## label & continue

```java
public static void main(String[] args) {
    outer:  // 设置标签outer
    for (int i = 101; i < 150; i++) {
        for (int j = 2; j <= i / 2; j++) {
            if (i % j == 0) {
                continue outer;  // 跳转到outer
            }
        }
        System.out.println(i);
    }
}
```



## 语句块

语句块（有时叫做复合语句），是用花括号扩起来的任意数量的简单Java语句。

块确定了局部变量的作用域。块中的程序代码，作为一个整体，是要被一起执行的。快可以被嵌套在另一个块中，但是不能在两个嵌套的块内声明同名的变量。语句块可以使用外部的变量，而外部不能使用语句块中定义的变量，因为语句块中定义的变量作用域只限于语句块。



# 方法🔥

## 定义方法

Java中进行方法调用中传递参数时，遵循值传递的原则（传递的都是数据的副本）：

- 基本类型传递的是该数据值的copy值，如果在方法中修改了参数的值，对原数据不起作用。
- 引用类型传递的是该对象引用的copy值，但指向的是同一个对象，方法中如果修改了参数的值，相应的原数据也会发生改变。

```java
public static void main(String[] args) {
    TestMethod tm = new TestMethod();
    tm.print();
    int res = tm.add(1, 2, 3);
    System.out.println(res);
}

void print() {  // 无返回值使用void
    System.out.println('?');
}

int add(int a, int b, int c) {  // 返回值为int类型
    return a + b + c;
}
```



## 方法的重载

```java
public class demo {
    public static void main(String[] args) {
        System.out.println(add(1, 5));
        System.out.println(add(1.5, 5));
        System.out.println(add(1, 5.9));
        System.out.println(add(6.3, 5.9));
        System.out.println(add(1, 5, 9));
    }

    static int add(int a, int b) {    // static关键字 在没有创建对象的情况下来进行调用
        return a + b;
    }

    static int add(int a, int b, int c) {
        return a + b + c;
    }

    static double add(double a, int b) {
        return a + b;
    }

    static double add(int a, double b) {
        return a + b;
    }

    static double add(double a, double b) {
        return a + b;
    }
}
```



## 可变参数

使用前提：当方法的参数列表数据类型已经确定，但是参数的个数不确定，就可以使用可变参数。

可变参数的原理：可变参数底层就是一个数组，根据传递参数个数不同，会创建不同长度的数组，来存储这些参数
传递的参数个数，可以是任意个。

可变参数的注意事项：

- 一个方法的参数列表，只能有一个可变参数
-  如果方法的参数有多个，那么可变参数必须写在参数列表的末尾

```java
public class demo {
    public static void main(String[] args) {
        add(7, 8, 9);
    }

    public static int add(int... arr) {
        int sum = 0;
        for (int i : arr) {
            sum += i;
        }
        System.out.println(sum);
        return sum;
    }
}
```



# 数组🔥

## 数组的定义

**数组**： 数组就是存储数据长度固定的容器，保证多个数据的数据类型要一致。



## 生成数组

数组是相同类型数据的有序集合。数组描述的是相同类型的若干个数据，按照一定的先后次序排列组合而成。其中，每一个数据称作一个元素，每个元素可以通过一个索引（下标）来访问它们。

1. 声明的时候并没有实例化任何对象，只有在实例化数组对象时，JVM才分配空间，这时才与长度有关。
2. 声明一个数组的时候并没有数组真正被创建。
3. 构造一个数组，必须指定长度。

```java
int[] arr01 = new int[10];  // 声明数组，并分配空间（指定长度）
```



### 静态初始化

初始化时显式指定每个数组元素的初始值，由系统决定数组的长度

```java
int[] a = new int[] {1, 3, 9};

// 简化写法
int[] a = {1, 3, 9};
User[] u = {new User("kiana"), new User("teresa")}
```



### 动态初始化

初始化时由程序员指定数组的长度，由系统初始化每个数组元素的默认值。

```java
// 数组存储的数据类型[] 数组名字 = new 数组存储的数据类型[长度];
int[] arr = new int[3];
arr[0] = 1;
arr[0] = 3;
arr[0] = 9;
```

> 数组有定长特性，长度一旦指定，不可更改



## 遍历数组

```java
for (int min = 0, max = arr.length-1; min < max; min++, max--) {
    int temp = arr[min];
    arr[min] = arr[max];
    arr[max] = temp;
}
```

`for-each` 专门用于读取数组或集合中所有的元素，即对数组进行遍历。

```java
int[] arr = {1, 2, 3};
for (int i : arr) {
    System.out.println(i);
}
```



## 数组的拷贝

数组元素的删除与增加：先定义一个容量更小或更大的数组，将原数组的元素拷贝到新数组中

```java
int[] oldArr = new int[]{1, 2, 3};        
int[] newArr = Arrays.copyOf(oldArr, 5);  // [1, 2, 3, 0, 0]
```



## 反转数组

```java
public class demo {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5, 6};
        // 反转数组 空间复杂度O(n)
        for (int min = 0, max = arr.length-1; min < max; min++, max--) {
            int temp = arr[min];
            arr[min] = arr[max];
            arr[max] = temp;
        }
        // 遍历数组
        for (int i : arr) {
            System.out.println(i);
        }
    }
}
```







# 拷贝🔥

## 浅拷贝

浅拷贝是按位拷贝对象，它会创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值；如果属性是内存地址（引用类型），拷贝的就是内存地址 ，因此如果其中一个对象改变了这个地址对应的对象，就会影响到另一个对象。

实现对象拷贝的类，需要实现 `Cloneable` 接口，并覆写 `clone()` 方法。

```java
public class Subject {

    private String name;

    public Subject(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "[Subject: " + this.hashCode() + ",name:" + name + "]";
    }
}
```



```java
public class Student implements Cloneable {

    //引用类型
    private Subject subject;
    //基础数据类型
    private String name;
    private int age;


    public Subject getSubject() {
        return subject;
    }

    public void setSubject(Subject subject) {
        this.subject = subject;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    /**
     * 重写clone()方法
     *
     * @return
     */
    @Override
    public Student clone() {
        //浅拷贝
        try {
            // 直接调用父类的clone()方法
            return (Student)super.clone();
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }

    @Override
    public String toString() {
        return "[Student: " + this.hashCode() + ",subject:" + subject + ",name:" + name + ",age:" + age + "]";
    }
}
```



## 深拷贝

深拷贝，在拷贝引用类型成员变量时，为引用类型的数据成员另辟了一个独立的内存空间，实现真正内容上的拷贝。

对于 `Student` 的引用类型的成员变量 `Subject` ，需要实现 `Cloneable` 并重写 `clone()` 方法。

```java
public class Subject implements Cloneable {

    private String name;

    public Subject(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        //Subject 如果也有引用类型的成员属性，也应该和 Student 类一样实现
        return super.clone();
    }

    @Override
    public String toString() {
        return "[Subject: " + this.hashCode() + ",name:" + name + "]";
    }
}
```



在 `Student` 的 `clone()` 方法中，需要拿到拷贝自己后产生的新的对象，然后对新的对象的引用类型再调用拷贝操作，实现对引用类型成员变量的深拷贝。

```java
public class Student implements Cloneable {

    //引用类型
    private Subject subject;
    //基础数据类型
    private String name;
    private int age;

    public Subject getSubject() {
        return subject;
    }

    public void setSubject(Subject subject) {
        this.subject = subject;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    /**
     *  重写clone()方法
     * @return
     */
    @Override
    public Object clone() {
        //深拷贝
        try {
            // 直接调用父类的clone()方法
            Student student = (Student) super.clone();
            student.subject = (Subject) subject.clone();
            return student;
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }

    @Override
    public String toString() {
        return "[Student: " + this.hashCode() + ",subject:" + subject + ",name:" + name + ",age:" + age + "]";
    }
}
```

