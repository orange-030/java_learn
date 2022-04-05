# Lambda表达式☕

## 函数式编程思想概述

**函数**就是有输入量、输出量的一套计算方案，也就是“拿什么东西做什么事情”。相对而言，面向对象过分强调“必须通过对象的形式来做事情”，而函数式思想则尽量忽略面向对象的复杂语法——**强调做什么，而不是以什么形式做**。

面向对象的思想：做一件事情，找一个能解决这个事情的对象，调用对象的方法完成事情

函数式编程思想：只要能获取到结果，谁去做的，怎么做的都不重要，重视的是结果，不重视过程

## 冗余的Runnable代码

```java
public class RunnableImpl implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
```

```java
    public static void main(String[] args) {
        // way1 实现Runnable接口，再传递给Thread的构造方法
        Runnable run = new RunnableImpl();
        Thread t = new Thread(run);
        t.start();

        // way2 匿名内部类重写run方法，再传递给Thread的构造方法
        Runnable r = new Runnable() {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName());
            }
        };
        new Thread(r).start();

        // way3 简化代码
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName());
            }
        }).start();
    }
```



## Lambda标准格式

Lambda省去面向对象的条条框框，格式由**3个部分**组成：

* 一些参数
* 一个箭头
* 一段代码

Lambda表达式的**标准格式**为：

```java
(参数类型 参数名称) -> { 代码语句 }
```

格式说明：

* 小括号内的语法与传统方法参数列表一致：无参数则留空；多个参数则用逗号分隔。
* `->` 是新引入的语法格式，代表指向动作。
* 大括号内的语法与传统方法体要求基本一致。



## Lambda表达式

Lambda表达式是可推导，可省略的，凡事根据上下文可推导的内容都是可省略的。

首先自定义一个函数式接口：

```java
public interface Printer {
    public abstract void justPrint(String string);
}
```

测试类中定义一个静态方法，接受String和Printer类型的参数：

```java
public static void invokePrint(String str, Printer printer) {
    printer.justPrint(str);
}
```



可以省略的内容：

1. (参数列表)：括号中参数列表的**数据类型**可以省略

   ```java
   invokePrint("hello", (string) -> {
       System.out.println(string);
   });
   ```

   

2. (参数列表)：括号中的参数如果**只有一个**，那么参数类型和括号都可以省略

   ```java
   invokePrint("hello", string -> {
       System.out.println(string);
   });   
   ```

   

3. {一些代码}：如果花括号中的代码只有一行，无论是否有返回值，都可以省略`{}`、`return`和`;` 

```java
invokePrint("hello", str -> System.out.println(str));
```



## Lambda表达式的使用前提

Lambda的语法非常简洁，完全没有面向对象复杂的语法束缚，但是使用时有几个问题需要注意：

1. 使用Lambda必须具有接口，且要求接口中有且仅有一个抽象方法。
   无论是JDK内置的 `Runnable`、`Comparator` 接口还是自定义的接口，只有当接口中的抽象方法存在且唯一时才可以使用Lambda。

2. 使用Lambda必须具有**上下文推断**。
   也就是方法的参数或局部变量类型必须为Lambda对应的接口类型，才能使用Lambda作为该接口的实例。

> 有且仅有一个抽象方法的接口，称为“函数式接口”。



## Lambda实现

首先定义一个含有唯一抽象方法的接口对象 `Cook` ，且无参数，无返回值

```java
public interface Cook {
    public abstract void makeFood();
}
```

使用**匿名内部类**和**Lambda的标准格式**调用`invokeCook`方法

```java
public class demo_lambda {
    public static void main(String[] args) {
        // 匿名内部类实现
        invokeCook(new Cook() {
            @Override
            public void makeFood() {
                System.out.println("吃饭了");
            }
        });
        
        // lambda表达式实现
        invokeCook(() -> {  
            System.out.println("吃饭了");
        });

    }
  
	// 定义invokeCook方法，参数为Cook接口
    public static void invokeCook(Cook cook) {
        cook.makeFood();
    }
}
```

> 小括号代表`Cook`接口`makeFood`抽象方法的参数为空，大括号代表`makeFood`的方法体。



## Lambda的参数和返回值

定义一个接口，其中有一个抽象方法接收两个int类型参数

```java
public interface Calculator {
    public abstract int calc(int a, int b);
}
```

测试类

```java
public class demo {
    public static void main(String[] args) {
        // 匿名内部类
        invokeCalc(10, 20, new Calculator() {
            @Override
            public int calc(int a, int b) {
                return a + b;
            }
        });

        // lambda简化匿名内部类（有参数有返回值）
        invokeCalc(20, 30, (int a, int b) -> {
            return a * b;
        });
    }
  	
        // 省略参数类型以及花括号
        invokeCalc(20, 30, (a, b) -> a / b);
		
    // 测试类中定义一个静态方法
    // 参数传递两个int类型整数，以及Calculator接口，方法内部调用Calculator接口中的方法
    public static void invokeCalc(int a, int b, Calculator c) {
        int sum = c.calc(a, b);
        System.out.println(sum);
    }
}

```



> 



# 函数式接口☕

函数式接口在 Java 中是指：有且仅有一个抽象方法的接口。
函数式接口，即适用于函数式编程场景的接口。而 Java 中的函数式编程体现就是 Lambda，所以函数式接口就是可以适用于 Lambda 使用的接口。只有确保接口中有且仅有一个抽象方法，Java 中的 Lambda 才能顺利地进行推导。 

> 备注：“语法糖”是指使用更加方便，但是原理不变的代码语法。例如在遍历集合时使用的for-each语法，其实底层的实现原理仍然是迭代器，这便是“语法糖”。从应用层面来讲，Java中的Lambda可以被当做是匿名内部类的“语法糖”，但是二者在原理上是不同的。 



## 格式

只要确保接口中有且仅有一个抽象方法即可：

```java
修饰符 interface 接口名称 {
    public abstract 返回值类型 方法名称(可选参数信息);
    // 其他非抽象方法
}
```

其中，抽象方法的 `public abstract` 是可以省略的。



## @FunctionalInterface 

`@override`：检查该方法是否是重写的方法，是则编译成功，否则编译失败。

与 `@override` 注解类似，使用 ` @FunctionalInterface` 定义接口，编译器将会强制检查该接口是否确实有且仅有一个抽象方法，否则将会报错。

即使不使用该注解，只要满足函数式接口的定义，这仍然是一个函数式接口，使用起来都一样。 

```java
@FunctionalInterface
public interface MyInterface {
    public abstract void method();
}
```



# 函数式编程☕

## Lambda的延迟执行

### 性能浪费的日志案例

以下的代码中存在性能浪费的情况，`showLog`方法传递的第二个参数是拼接后的字符串，如果传递的日志等级不符合要求，那么拼接字符串就白传了。

```java
public class demo {
    public static void main(String[] args) {
        String msg1 = "Hello";
        String msg2 = "World";
        String msg3 = "Java";
        
        // 调用showLog方法，传递日志级别和日志信息
        showLog(1, msg1 + msg2 + msg3);
        showLog(2, msg1 + msg2 + msg3);
    }

    public static void showLog(int level, String msg) {
        if (level == 1) {
            System.out.println(msg);
        }
    }
}
```

**优化后：**

定义一个函数式接口：

```java
@FunctionalInterface
public interface MessageBuilder {
    public abstract String buildMessage();
}
```

使用Lambda表达式作为参数传递，仅仅是将参数传递到 `showLog` 方法中。

由于Lambda表达式的特性，这里的 `msg1 + msg2 + msg3` 作为重写 MessageBuilder接口中buildMessage方法的函数体，只有满足条件，才会调用接口中的方法，进行字符串的拼接，避免了性能浪费。

```java
public class demo {
    public static void main(String[] args) {
        String msg1 = "Hello";
        String msg2 = "World";
        String msg3 = "Java";

        // 调用showLog方法，第二个参数是函数式接口，可以传递lambda表达式
        showLog(1, () -> msg1 + msg2 + msg3);
        showLog(2, () -> msg1 + msg2 + msg3);
    }

    public static void showLog(int level, MessageBuilder mb) {
        if (level == 1) {
            System.out.println(mb.buildMessage());
        }
    }
}
```



## 使用Lambda作为参数和返回值

**1. 使用函数式接口作为方法的参数：**

```java
public class demo {
    public static void main(String[] args) {
        // 传递匿名内部类
        startThread(new Runnable() {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName());
            }
        });

        // 方法的参数是函数式接口，可以传递lambda表达式
        startThread(
                () -> System.out.println(Thread.currentThread().getName()));
    }

    public static void startThread(Runnable runnable) {
        new Thread(runnable).start();
    }
}
```



**2. 使用函数式接口作为方法的返回值：**

```java
public class demo {
    public static void main(String[] args) {

    }

    public static Comparator<String> getComparator01() {
        // 1. 返回一个匿名内部类
        return new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                // 按字符串长度降序排列
                return o2.length() - o1.length();
            }
        };
    }

    // 2. 返回Lambda表达式
    public static Comparator<String> getComparator02() {
        return (String o1, String o2) -> {
            return o2.length() - o1.length();
        };
    }

    // 3. 返回简化后的Lambda表达式
    public static Comparator<String> getComparator03() {
        return (o1, o2) -> o2.length() - o1.length();
    }
}
```



# 常用的函数式接口☕

## Supplier接口

抽象方法：get

`java.util.function.Supplier<T>` 接口仅包含一个无参的方法： `T get()` ，用来获取一个泛型参数指定类型的对象数据。

```java
public class supplier_01 {
    public static void main(String[] args) {
        int[] arr = {50, 25, -100, 99, 32, 8, -20};
        // 调用getMax方法获取数组的最大值
        // 逻辑具体实现由传入的Supplier接口实现类决定
        int maxValue = getMax(arr, () -> {
            int max = arr[0];
            for (int i : arr) {
                if (i > max) {
                    max = i;
                }
            }
            return max;
        });
        System.out.println("数组中的最大值为：" + maxValue);
    }

    public static int getMax(int[] arr, Supplier<Integer> supplier) {
        return supplier.get();
    }
}

```



## Consumer接口

`java.util.function.Consumer<T>` 接口则正好与 `Supplier` 接口相反，它不是生产一个数据，而是消费一个数据，其数据类型由泛型决定。

**抽象方法：accept**

`Consumer` 接口中包含抽象方法 `void accept(T t)` ，意为消费一个指定泛型的数据。基本使用如：

```java
public class consumer_demo {
    public static void main(String[] args) {
        method("kiana", (String name)->{
          
            // 自定义消费方式，对传递的字符串进行消费
            System.out.println("这里省略n项操作...");
            System.out.println(name + "已经被成功消费");
        });
    }

    public static void method(String name, Consumer<String> consumer){
        consumer.accept(name);
    }
}
```



**默认方法：andThen**

如果一个方法的参数和返回值全都是 `Consumer` 类型，那么就可以实现效果：消费数据的时候，首先做一个操作，然后再做一个操作，实现组合。而这个方法就是 `Consumer` 接口中的default方法 `andThen` 。

```java
public class consumer_demo {
    public static void main(String[] args) {
        // 调用method1方法，传递一个字符串，两个lambda表达式
        method1("Hello",
                (t) -> System.out.println(t.toUpperCase()),
                (t) -> System.out.println(t.toLowerCase()));
        
        // 使用andThen方法，将两个Comsumer接口连接到一起再进行消费
        method2("World",
                (t) -> System.out.println(t.toUpperCase()),
                (t) -> System.out.println(t.toLowerCase()));
    }

    public static void method1(String s, 
                               Consumer<String> con1, 
                               Consumer<String> con2) {
        con1.accept(s);
        con2.accept(s);
    }

    public static void method2(String s, 
                               Consumer<String> con1, 
                               Consumer<String> con2) {
        con1.andThen(con2).accept(s);
    }
}
```



## Preducate接口

有时候我们需要对某种类型的数据进行判断，从而得到一个 boolean 值结果。这时可以使用
`java.util.function.Predicate<T>` 接口。

**抽象方法：test**

`Predicate` 接口中包含一个抽象方法： `boolean test(T t)` ，用于条件判断的场景。

```java
public class predicate_demo {
    public static void main(String[] args) {
        String s = "Hello";
        boolean b = checkString(s, (s1) -> s1.length() > 5);
        System.out.println(b);
    }

    public static boolean checkString(String s, Predicate<String> predicate) {
        return predicate.test(s);
    }
}
```



默认方法：and

```java
public class predicate_demo02_and {
    public static void main(String[] args) {
        String s = "Hello";
        boolean b1 = checkString1(s,
                (s1) -> s1.contains("a"),
                (s1) -> s1.contains("b"));
        System.out.println(b1);

        boolean b2 = checkString2(s,
                (s1) -> s1.contains("a"),
                (s1) -> s1.contains("b"));
        System.out.println(b2);
    }

    public static boolean checkString1(String s, 
                                       Predicate<String> pred1, 
                                       Predicate<String> pred2) {
        return pred1.test(s) && pred2.test(s);
    }

    public static boolean checkString2(String s, 
                                       Predicate<String> pred1, 
                                       Predicate<String> pred2) {
        return pred1.and(pred2).test(s);
    }
}
```



默认方法：or

```java
public class predicate_or {
    public static void main(String[] args) {
        String s = "Hello";
        boolean b1 = checkString1(s,
                (s1) -> s1.contains("o"),
                (s1) -> s1.contains("b"));
        System.out.println(b1);

        boolean b2 = checkString2(s,
                (s1) -> s1.contains("o"),
                (s1) -> s1.contains("b"));
        System.out.println(b2);
    }

    public static boolean checkString1(String s,
                                       Predicate<String> pred1,
                                       Predicate<String> pred2) {
        return pred1.test(s) || pred2.test(s);
    }

    public static boolean checkString2(String s, 
                                       Predicate<String> pred1, 
                                       Predicate<String> pred2) {
        return pred1.or(pred2).test(s);
    }
}
```



默认方法：negate

```java
public class predicate_negate {
    public static void main(String[] args) {
        String s = "Hello";
        boolean b1 = checkString1(s,
                (s1) -> s1.contains("o"));
        System.out.println(b1);

        boolean b2 = checkString2(s,
                (s1) -> s1.contains("o"));
        System.out.println(b2);
    }

    public static boolean checkString1(String s, Predicate<String> pred1) {
        return !pred1.test(s);
    }

    public static boolean checkString2(String s, Predicate<String> pred1) {
        return pred1.negate().test(s);
    }
}
```



## Function接口

`java.util.function.Function<T,R>` 接口用来根据一个类型的数据得到另一个类型的数据，前者称为前置条件，后者称为后置条件。

**抽象方法：apply**

`Function` 接口中最主要的抽象方法为： `R apply(T t)` ，根据类型 `T` 的参数获取类型 `R`的结果。
使用的场景例如：将 `String` 类型转换为 `Integer` 类型。

Function的泛型为 `<String, Integer>`，则 `apply`方法接收String类型的参数，返回Integer类型的参数。

```java
public class function_demo {
    public static void main(String[] args) {
        method("15", (String s) -> Integer.parseInt(s));
    }

    public static void method(String s, Function<String, Integer> function) {
        Integer integer = function.apply(s);
        System.out.println(integer.getClass());
    }
}
```

**默认方法：andThen**

```java
public class function_demo {
    public static void main(String[] args) {
        method("15",
                (String s) -> Integer.parseInt(s),
                (Integer i) -> i * 10);
    }

    public static void method(String s,
                              Function<String, Integer> fun1,
                              Function<Integer, Integer> fun2) {
        Integer integer = fun1.andThen(fun2).apply(s);
        System.out.println(integer);
    }
}
```





# 方法引用☕

## 冗余的lambda表达式

之前了解了匿名内部类实现接口的方法比较冗余，所以学习了写法上更简洁的 lambda 表达式，但如果我们在 Lambda 中所指定的操作方案，已经有地方存在相同方案，那是否还有必要再写重复逻辑？

首先定义一个函数式接口

```java
@FunctionalInterface
public interface Printable {
    void print(String s);
}
```

用 lambda 表达式实现接口具体功能

```java
public class demo {
    public static void1 main(String[] args) {
        // 使用lambda表达式传递具体实现
        printString(s -> System.out.println(s));
    }
  	
    // 该方法需要传递一个接口的实现类
    public static void printString(Printable printable) {
        printable.print("Hello World");
    }
}
```



## 用方法引用改进代码

问题分析：

`printString` 方法调用 `Printable` 接口的 `print` 方法，`main` 方法通过 lambda 表达式指定了函数式接口 `Printable` 的具体实现，然而，对字符串进行打印输出的操作，已经有了现成的方案，即 `System.out`对象中的 `println(String)` 方法。

既然 lambda 做的事情就是调用 `println(String)` 方法，那么可以采用「方法引用」的方式进行优化。

```java
public class demo {
    public static void main(String[] args) {
        // 方法引用
        printString(System.out::println);
    }

    public static void printString(Printable printable) {
        printable.print("Hello World");
    }
}
```



## 方法引用符

双冒号 `::` 为引用运算符，而它所在的表达式被称为方法引用，如果 lambda 要表达的函数方案已经存在于某个方法的实现中，那么则可以通过双冒号来引用该方法作为 lambda 的替代者。



### 语义分析

上例中， `System.out` 对象中有一个重载的 `println(String)` 方法恰好就是我们所需要的。那么对于`printString` 方法的函数式接口参数，对比下面两种写法，完全等效：

- Lambda表达式写法： `s -> System.out.println(s);`

- 方法引用写法： `System.out::println`

第一种语义是指：拿到参数之后经 lambda 之手，继而传递给 `System.out.println` 方法去处理。

第二种等效写法的语义是指：直接让 `System.out` 中的 `println` 方法来取代 lambda。两种写法的执行效果完全一样，而第二种方法引用的写法复用了已有方案，更加简洁。

> lambda 中 传递的参数 一定是方法引用中的方法可以接收的类型，否则会抛出异常

### 推导与省略

如果使用 lambda，那么根据 “可推导就是可省略” 的原则，无需指定参数类型，也无需指定的重载形式，它们都将被自动推导。而如果使用方法引用，也是同样可以根据上下文进行推导。



## 通过对象名引用成员方法

定义一个类：

```java
class MethodRefObject {
    public void printUpperString(String string) {
        System.out.println(string.toUpperCase());
    }
}
```

测试类：

```java
public class demo {
    public static void main(String[] args) {
        // 使用匿名内部类
        printString(new Printable() {
            @Override
            public void print(String s) {
                MethodRefObject obj = new MethodRefObject();
                obj.printUpperString(s);
            }
        });

        // 使用lambda表达式
        printString(s -> {
            MethodRefObject obj = new MethodRefObject();
            obj.printUpperString(s);
        });

        // 使用方法引用
        MethodRefObject obj = new MethodRefObject();
        printString(obj::printUpperString);
    }

    public static void printString(Printable printable) {
        printable.print("Hello World");
    }
}
```



## 通过类名称引用静态方法

首先定义一个函数式接口：

```java
@FunctionalInterface
public interface Calcable {
    public abstract int calAbs(int number);
}
```

测试类：

```java
public class demo {
    public static void main(String[] args) {
        int num1 = method(-10, number -> Math.abs(number));
        int num2 = method(-10, Math::abs);
        System.out.println(num1);
        System.out.println(num2);
    }

    public static int method(int number, Calcable calcable) {
        return calcable.calAbs(number);
    }
}
```



## 通过super引用父类成员方法



## 通过this引用本类成员方法







# Stream Flow☕

Stream（流）是一个来自数据源的元素队列

- 元素是特定类型的对象，形成一个队列。 Java中的 Stream 并不会存储元素，而是按需计算。
- 数据源流的来源：可以是集合、数组等



和以前的 Collection 操作不同，Stream 操作还有两个基础的特征：

- Pipelining：中间操作都会返回流对象本身。 这样多个操作可以串联成一个管道，如同流式风格（fluentstyle）。 这样做可以对操作进行优化， 比如延迟执行（laziness）和短路（short-circuiting）。
- 内部迭代： 以前对集合遍历都是通过 Iterator 或者增强 for 循环的方式，显式的在集合外部进行迭代， 这叫做外部迭代。 Stream 提供了内部迭代的方式，流可以直接调用遍历方法。



当使用一个流的时候，通常包括三个基本步骤：

1. 获取一个数据源（source）
2.  数据转换
3. 执行操作获取想要的结果

每次转换原有 Stream 对象不改变，返回一个新的 Stream 对象（可以有多次转换），这就允许对其操作可以像链条一样排列，变成一个管道。



## 流的类型

`stream` ：是顺序流，由主线程按顺序对流执行操作

`parallelStream`：是并行流，内部以多线程并行执行的方式对流进行操作，但前提是流中的数据处理没有顺序要求，如果流中的数据量足够大，并行流可以加快处速度。

<img src="http://store.secretcamp.cn/uPic/image-20210713000932487202107130009331626106173uVazNNuVazNN.png" alt="image-20210713000932487" style="zoom:40%;" />



## 获取流

`java.util.stream.Stream<T>` 是 Java 8 新加入的最常用的流接口。（并不是一个函数式接口）
获取一个流非常简单，有以下几种常用的方式：

- 所有的 `Collection` 集合都可以通过 `stream()` 默认方法获取流；
- `Stream` 接口的静态方法 `of()` 可以获取数组对应的流；
- Arrays 工具类的 `stream()` 方法可以获取数组对应的流；



## Optional

`Optional    `类是一个可以为 `null` 的容器对象。如果值存在则 `isPresent()` 方法会返回 `true`，调用 `get()` 方法会返回该对象。

它是一个容器：它可以保存类型 T 的值，或者仅仅保存 null 。

- `<T> Optional<T> of(T value)` ：返回一个 `Optional`

- `boolean isPresent()`：返回值是否存在

- `T get()` ：获取值
- `T orElse(T other)`：值存在则返回，否则返回 other



## 延迟方法

延迟方法：返回值类型仍然是 `Stream` 接口自身类型的方法，因此支持链式调用。（除了终结方法外，其余方法均为延迟方法。

### filter

可以通过 `filter` 方法将一个流转换成另一个子集流。

该接口接收一个 `Predicate` 函数式接口参数（可以是一个Lambda或方法引用）作为筛选条件。

```java
Stream<T> filter(Predicate<? super T> predicate);
```

基本使用：

```java
public class demo {
    public static void main(String[] args) {
        String[] arr = {"刘备", "曹操", "吕布", "刘三刀", "刘封", "刘邦"};
        Stream<String> stream = Stream.of(arr);
        stream.filter(name -> name.startsWith("刘"))
                .filter(name -> name.length() == 2)
                .forEach(name -> System.out.println(name));
    }
}
```



### map

如果需要将流中的元素映射到另一个流中，可以使用 `map` 方法。

该接口需要一个 `Function` 函数式接口参数，可以将当前流中的 `T` 类型数据转换为另一种 `R` 类型的流。

```java
<R> Stream<R> map(Function<? super T, ? extends R> mapper);
```

基本使用：

```java
public class demo {
    public static void main(String[] args) {
        Stream<String> stream1 = Stream.of("96", "97", "98");
        // 使用map方法，将字符串型整数，映射为Integer类型的整数
        Stream<Integer> stream2 = stream1.map(s -> Integer.parseInt(s));
        // 遍历stream流
        stream2.forEach(integer-> System.out.println(integer));
    }
}
```



### limit

`limit` 方法可以对流进行截取，只取用前n个。

参数是一个long型，如果集合当前长度大于参数则进行截取；否则不进行操作。

基本使用：

```java
public class demo {
    public static void main(String[] args) {
        String[] arr = {"刘备", "曹操", "吕布", "刘三刀", "刘封", "刘邦"};
        Stream<String> stream1 = Stream.of(arr);
        Stream<String> stream2 = stream1.limit(5).limit(4).limit(3);
        stream2.forEach(str -> System.out.println(str));
    }
}
```



### skip

如果希望跳过前几个元素，可以使用 `skip` 方法获取一个截取之后的新流。

如果流的当前长度大于n，则跳过前n个；否则将会得到一个长度为0的空流。

基本使用：

```java
public class demo {
    public static void main(String[] args) {
        String[] arr = {"刘备", "曹操", "吕布", "刘三刀", "刘封", "刘邦"};
        Stream<String> stream1 = Stream.of(arr);
        Stream<String> stream2 = stream1.skip(1).skip(2);
        stream2.forEach(str -> System.out.println(str));
    }
}
```



## 终结方法

终结方法：返回值类型不再是 `Stream` 接口自身类型的方法，因此不再支持类似 `StringBuilder` 那样的链式调用。本小节中，终结方法包括 `count()` 和 `forEach()` 方法。

### forEach

该方法接收一个 `Consumer` 接口函数，会将每一个流元素交给该函数进行处理。

```java
void forEach(Consumer<? super T> action);
```



基本使用：

```java
public class demo {
    public static void main(String[] args) {
        Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5);
        stream.forEach(integer -> System.out.println(integer + 1));
    }
}
```



### count

与旧集合 `Collection` 当中的 `size` 方法一样，流提供 `count` 方法来数一数其中的元素个数

该方法返回一个long值代表元素个数

```java
public class demo {
    public static void main(String[] args) {
        String[] arr = {"刘备", "曹操", "吕布", "刘三刀", "刘封", "刘邦"};
        Stream<String> stream = Stream.of(arr);
        long count = stream.count();
        System.out.println(count);
    }
}
```





### concat

如果有两个流，希望合并成为一个流，那么可以使用 `Stream` 接口的静态方法 `concat` ：

```java
static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b)
```

> 这是一个静态方法，与 java.lang.String 当中的 concat 方法是不同的。



基本使用：

```java
public class demo {
    public static void main(String[] args) {
        String[] arr1 = {"刘备", "曹操", "吕布"};
        String[] arr2 = {"刘三刀", "刘封", "刘邦"};
        Stream<String> stream1 = Stream.of(arr1);
        Stream<String> stream2 = Stream.of(arr2);
        Stream<String> stream3 = Stream.concat(stream1, stream2);
        stream3.forEach(str -> System.out.println(str));
    }
}
```



### collect

`collect()` 方法可以优雅的将一个对象的集合转化成另一个对象的集合

`java.util.stream.Collectors` 

```java
<R, A> R collect(Collector<? super T, A, R> collector);
```



### reduce





### orElse

如果当前值存在则返回，否则返回other

```java
public T orElse(T other) {
    return value != null ? value : other;
}
```







# Stream Flow原理☕