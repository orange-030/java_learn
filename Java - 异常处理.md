# 异常

**异常机制本质**

当程序出现错误，程序安全退出的机制。

Java是采用面向对象的方式来处理异常的

1. 抛出异常：在执行一个方法时，如果发生异常，则这个方法生成代表该异常的一个对象，停止当前执行路径，并把异常对象提交给JRE。

2. 捕获异常：JRE得到该异常后，寻找相应的代码来处理该异常。JRE在方法的调用栈中查找，从生成异常的方法开始回溯，直到找到相应的异常处理代码为止。

```
Throwable --- Expection --- Checked Expection
		 |			   |--- Runtime Expection --- Unchecked Expection 
		 |--- Error	    --- Unchecked Expection 
```

- Checked Expection：编译器需要处理的异常

- Unchecked Expection ：编译器不需要处理的异常



## Error

Error是程序无法处理的错误，表示运行应用程序中较严重问题。大多数错误与代码编写者执行的操作无关，而表示代码运行时JVM出现的问题。

## Expection

Exception是程序本身能够处理的异常，`Exception类`是所有异常类的父类，其子类对应了各种各样可能出现的异常事件。

通常Java的异常可分为：

1. RuntimeException 运行时异常
2. CheckedException 已检查异常

### RuntimeException运行时异常

### CheckedException已检查异常

所有不是RuntimeException的异常，统称为Checked Exception，又被称为“已检查异常”。

如`IOException`、`SQLException`等以及用户自定义的Exception异常，这类异常在编译时就必须做出处理，否则无法通过编译。

处理方式有两种：

1. 使用`try/catch`捕获异常
2. 使用`throws`声明异常

## 捕获异常

捕获异常是通过3个关键词来实现的：`try-catch-finally`

用`try`来执行一段程序，如果出现异常，系统抛出一个异常，可以通过它的类型`catch`并处理它，最后是通过`finally`语句为异常处理提供一个统一的出口，`finally`所指定的代码都要被执行。



## 声明异常

当`CheckedException`产生时，不一定立刻处理它，可以再把异常throws出去。
在方法中可以使用`try-catch-finally`来处理异常，但是在一些情况下，当前方法并不需要处理发生的异常，而是向上传递给调用它的方法处理。
如果一个方法中可能产生某种异常，但是并不能确定如何处理这种异常，则应根据异常规范**在方法的首部声明该方法可能抛出的异常**。

## 自定义异常

1. 自定义异常类只需从Exception类或者它的子类派生一个子类即可。
2. 自定义异常类如果继承`Exception`类，则为`CheckedException`，必须对其进行处理；如果不想在编译时处理，可以继承`RuntimeException`类。
3. 习惯上，自定义异常类应该包含2个构造器：一个是默认的构造器，另一个是带有详细信息的构造器。

```java
class InvalidAgeExpection extends RuntimeException{
    public InvalidAgeExpection() {
    }
    public InvalidAgeExpection(String msg) {
        super(msg);
    }
}
```

## 手动抛出异常

```java
throw new FileNotFoundException();
```



## 常见异常

### nullpointerexception

`java.lang.nullpointerexception` 表示空指针异常，就是调用了未经初始化的对象或者是不存在的对象。



### arithmeticexception

`java.lang.arithmeticexception` 是由于数学运算导致的异常，比如除以零。





### StackOverflowError

线程请求的栈深度大于虚拟机所允许的最大深度，将抛出 `StackOverflowError` 异常，递归调用方法，如果没有方法出口的方法会抛出异常。



### OutOfMemoryError

Java堆用于存储对象实例，只要不断地创建对象，并且保证 GC Roots 到对象之间有可达路径来避免垃圾回收机制清除这些对象，那么在对象数量到达最大堆的容量限制后就会产生内存溢出异常。



# 二、Junit单元测试

## 2.1 测试分类

- 黑盒测试：不需要写代码，给输入值，看程序是否能够输出期望的值。

- 白盒测试：需要写代码，关注程序具体的执行流程。



## 2.2 @Test

首先创建 `Calculator` 类

```java
public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }
}
```

创建测试类

```java
public class CalculatorTest {
    @Test  // 无需main方法
    public void testAdd() {
        Calculator cal = new Calculator();
        int result = cal.add(3, 4);
        // 断言
        Assert.assertEquals(8, result);
    }
}

// java.lang.AssertionError: 
// 预期:8
// 实际:7
```



## 2.3 @Before 和 @After

- `@Before`：修饰的方法会在测试方法之前被自动执行

- `@After`：修饰的方法会在测试方法执行之后自动被执行。

```java
public class CalculatorTest {
    @Before
    public void init() {
        System.out.println("init...");
    }
    @After
    public void close() {
        System.out.println("close...");
    }

    @Test
    public void testAdd() {
        System.out.println("testAdd 测试方法执行");
        Calculator cal = new Calculator();
        int result = cal.add(3, 4);
        // 断言
        Assert.assertEquals(7, result);
    }
}

// init...
// testAdd 测试方法执行
// close...
```

