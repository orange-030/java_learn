# 反射

## 概念

Java反射机制的核心是在程序运行时动态加载类以及获取类的信息，从而使用类和对象的数据域和方法。



## 何为反射

反射之中包含了一个「反」字，所以想要解释反射就必须理解何为「正」。

一般情况下，我们使用某个类时必定知道它是什么类，是用来做什么的。于是我们直接对这个类进行实例化，之后使用这个类对象进行操作。

这样进行类对象的初始化，编译时就确定好了对象的类型，我们可以理解为「正」。

```java
Person person = new Person();
person.eat();
```

而反射则是一开始并不知道我要初始化的类是什么，自然也无法使用 `new` 关键字来创建对象。

```java
Class cls = Class.forName("Reflection.Person");
Method method = cls.getMethod("eat");
Constructor constructor = cls.getConstructor();
Object obj = constructor.newInstance();
method.invoke(obj);
```

以上两段代码的执行结果是完全一样的，但是其思路完全不同。

第一段代码在未运行时就已经确定了要运行的类（Person），而第二段代码则是在运行时通过字符串的全限定类名才得知要运行的类（"Reflection.Person"）。

所以说什么是反射？

**反射就是在运行时才知道要操作的类是什么，并且可以在运行时获取类的完整构造，并调用对应的方法。**



## Java代码运行机制

Java代码在计算机中从编写到运行，主要经历三个阶段：

1. Source 源代码阶段

   ```
     编写类文件（Person.java）  -> 编译（javac） -> 字节码文件（Person.class）
   ```

   

2. Class 类对象阶段

   通过类加载器 「ClassLoader」将字节码文件加载到内存，成为类对象「Class」

   java中的Class类，用于描述字节码文件共同的行为和特征，指成员变量、构造方法和成员方法，并将这三个部分分别封装为Field、Constructor 和 Method 对象，之后就可以通过这三个对象 ，创建出 Runtime 阶段中的对象

   

3. Runtime 运行时阶段

   ```
   Person person = new Person();
   ```





每个类都有一个 Class 对象，包含了与类有关的信息。当编译一个新类时，会产生一个同名的 `.class` 文件，该文件内容保存着 Class 对象。

类加载相当于 Class 对象的加载，类在第一次使用时才动态加载到 Java 虚拟机中。也可以使用 `Class.forName("com.mysql.jdbc.Driver")` 这种方式来控制类的加载，该方法会返回一个 Class 对象。

反射可以提供运行时的类信息，并且这个类可以在运行时才加载进来，甚至在编译时期该类的字节码文件不存在也可以加载进来。



## java.lang.Class

Java 程序在运行时，系统一直对所有的对象进行 运行时类型标识。这项信息纪录了每个对象所属的类。虚拟机通常使用运行时类型信息寻找正确的方法去执行，用来保存这些类型信息的类是 Class 类，

Class 类是在 Java 中定义一个特定类的实现，Class 类封装一个对象和接口运行时的状态，当各种类被调用时，Class 类的对象由 Java虚拟机自动创建。

<img src="http://store.secretcamp.cn/uPic/image-20210109152004738202101091520041610176804TgQb65TgQb65.png" alt="image-20210109152004738" style="zoom:50%;" />



所有的 Java 类都继承了 `Object` 这个类，在 `Object` 类中有一个方法：`getclass()` ，这个方法是用来取得 当前类已经被实例化的对象的该类的引用，这个引用指向的是 `Class` 类的对象。



## 获取Class对象

1. class.forName("全类名")：将字节码文件加载进内存，返回class对象

   处于第一阶段：java 代码仅有字节码文件

   > 该方式多用于配置文件中

2. 类名.class：通过类名的 class 属性获取

   处于第二阶段：字节码文件已经加载进了内存

   > 这种方法只适合在编译前就已知操作的 Class

3. 对象.getclass()：通过对象的方法获取，该方法在Object类中定义

   处于第三阶段：运行时阶段已经存在对象

   > 该方式用于存在对象的情况下获取字节码文件

```java
@Test
public void test01() throws ClassNotFoundException {
    // way1
    Class cls01 = Class.forName("Reflect.Person");
    System.out.println(cls01);

    // way2
    Class cls02 = Person.class;
    System.out.println(cls02);

    // way3
    Person person = new Person();
    Class cls03 = person.getClass();
    System.out.println(cls03);

    // 比较是否是同一个对象
    System.out.println(cls01 == cls02);
    System.out.println(cls01 == cls03);
}
```



结论：同一个字节码文件 `*.class` 在一次程序运行过程中，只会被加载一次，不论通过哪一种方式获取的 Class对象 都是同一个，一个字节码文件对应一个 Class对象



## Class对象功能

`Class` 类的作用是在程序运行时保存每个对象所属的类的信息，在程序运行时分析类。

### 获取成员变量

- `Field[] getFields()`：获取所有public修饰的成员变量
- `Field getField(String name)`：获取某个public修饰的成员变量
- `Field[]getDeclaredFields()`：获取所有的成员变量
- `Field getDeclaredField(String name)`：获取某个成员变量





### 获取构造方法

- `Constructor<?>[] getConstructors()`
- `Constructor<T> getConstructor(类<?>...parameterTypes)`
- `Constructor<T> getDeclaredConstructor(类<?>...parameterTypes)`
- `Constructor<?>[]getDeclaredConstructors()`

- `T newInstance()`：获取空参的构造方法



### 获取成员方法

- `Method[] getMethods()`
- `Method getMethod(string name，类<?>...parameterTypes)`
- `Method[] getDeclaredMethods()`
- `Method getDeclaredMethod(String name，类<?>...parameterTypes)`



### 获取类名

- `String getName()`



### 获得类加载器





## Field：成员变量

### 设置值

- `void set(Object obj, Object value)`

### 获取值

- `get(Object obj)`

### 忽略访问权限修饰符的安全检查

- `setAccessible(true)`：暴力反射



```java
@Test
public void test01() throws Exception {
    // 获取Person的Class对象
    Class<Person> personClass = Person.class;

    // 获取单个public修饰的成员变量
    Field address = personClass.getField("address");
    System.out.println(address);

    // 获得成员变量，不考虑访问控制符
    Field name = personClass.getDeclaredField("name");
    System.out.println(name);

}

@Test
public void test02(){
    // 获取Person的Class对象
    Class<Person> personClass = Person.class;

    // 获取public修饰的所有成员变量
    Field[] fields = personClass.getFields();
    for (Field field : fields) {
        System.out.println(field);
    }
}


@Test
public void test03() throws Exception {
    // 获取Person的Class对象
    Class<Person> personClass = Person.class;

    // 获取单个public修饰的成员变量
    Field address = personClass.getField("address");

    // 获得成员变量，不考虑访问控制符
    Field name = personClass.getDeclaredField("name");

    // 创建对象
    Person person = new Person("KIANA", 18);

    // 获取public修饰的成员变量的值
    System.out.println(address.get(person));  // 获取person对象的address值
    address.set(person, "圣芙蕾雅学园");  // 设置person对象address的值

    // 获取成员变量的值，不考虑访问修饰符
    name.setAccessible(true);  // 忽略访问权限修饰符的安全检查
    System.out.println(name.get(person));
    name.set(person, "YING");

    System.out.println(person);
}
```



## Constructer：构造方法

- `T newinstance (object.. Initargs)`：创建对象



```java
@Test
public void test02() throws Exception {
    Class<Person> personClass = Person.class;
    Person person = new Person();

    // 空参数的构造方法
    Constructor constructor01 = personClass.getConstructor();
    // 有参数的构造方法
    Constructor constructor02 = personClass.getConstructor(String.class, int.class);
  
    // 创建对象
    Object obj1 = constructor01.newInstance();
    Object obj2 = constructor02.newInstance("KIANA", 16);

    // Class提供了空参构造的简便方法，直接使用Class对象的方法创建
    Object obj3 = personClass.newInstance();
  
}
```



## Method：方法对象

执行方法

- `Object invoke(Object obj, Object ... args)`：执行方法
- `String getName`：获取方法名 

```java
@Test
public void test01() throws Exception {
    Class<Person> personClass = Person.class;
    Person person = new Person();

    // 获取无参方法
    Method eatMethod1 = personClass.getMethod("eat");
    eatMethod1.invoke(person);

    // 获取有参方法
    Method eatMethod2 = personClass.getMethod("eat", String.class);
    eatMethod2.invoke(person, "peach");
}

@Test
public void test02(){
    Class<Person> personClass = Person.class;
    // 获得所有public修饰的方法
    Method[] methods = personClass.getMethods();
    // 打印方法对象
    for (Method method : methods) {
        System.out.println(method);
    }
}
```



## 反射加载配置文件

步骤：

1. 将需要创建的对象的全类名和需要执行的方法定义在配置文件中

2. 在程序中加载读取配置文件

3. 使用反射技术来加载类文件进内存

4. 创建对象

5. 执行方法

---

定义一个配置文件

```JAVA
// prop.properties
className=Reflect.Person
methodName=eat
```

加载配置文件

```java
@Test
public void test() throws Exception {
    // 创建Properties对象
    Properties prop = new Properties();
    // 获取字节码文件对应的类加载器
    ClassLoader classLoader = ref_06_LoadConfig.class.getClassLoader();
    // 直接将资源转为字节输入流
    InputStream stream = classLoader.getResourceAsStream("Reflect/prop.properties"); 
    prop.load(stream);

    // 获取配置文件中定义的数据
    String className = prop.getProperty("className");
    String methodName = prop.getProperty("methodName");

    // 将该类加载进内存
    Class cls = Class.forName(className);
    // 用空参构造方法创建对象
    Object obj = cls.newInstance();
    // 获取方法对象
    Method method = cls.getMethod(methodName);
    // 执行方法
    method.invoke(obj);
}
```





# 注解

## 概念

Java 注解是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性的作用。

注解的作用：

- 编写文档：通过代码里标识的注解生成文档【生成文档doc文档】

- 代码分析：通过代码里标识的注解对代码进行分析【使用反射】

- 编译检查：通过代码里标识的注解让编译器能够实现基本的编译检查【override】



## 预定义注解

### @Override

当子类重写父类方法时，子类可以加上这个注解，可以确保子类确实重写了父类的方法。

### @Deprecated

这个注解用于表示某个程序类或方法等**已过时**，当使用已过时的类或方法时编译器会给出警告（删除线）。

### @SuppressWarnings

被该注解修饰的元素以及该元素的所有子元素都会 **取消显示编译器警告**

```java
@SuppressWarnings("all")  // 压制所有警告
```

### @FunctionalInterface

保证这个接口只有一个抽象方法，是一个函数式接口。



## 自定义注解

### 格式

```java
元注解
public @interface myAnno {
}
```

### 本质

对 `myAnno.java` 先进行编译 `javac myAnno.java` ，再进行反编译：`javap myAnno.class`

```
Compiled from "myAnno.java"
public interface myAnno extends java.lang.annotation.Annotation {
}
```

注解本质上是一个接口，该接口默认继承 `java.lang.annotation.Annotation`

### 属性

属性：接口中的抽象方法

- 属性的返回值：

  1. 基本数据类型

  2. String

  3. 枚举

  4. 注解

  5. 以上类型的数组

```java
public @interface myAnno {
    public abstract int age();
    // 设置默认值，这样使用注释时就不必给这个属性赋值
    public abstract String name() default "Yorua";
    public abstract Person person();
    public abstract otherAnno anno();
    public abstract int[] arr();
}
```



- 定义属性后，使用时需要给属性赋值

  - 设置过默认值的属性可以不用赋值
  - 注解赋值需要加 @

  - 数组赋值需要用{}，如果数组只有一个元素，那么{}可以省略

```java
@myAnno0(age = 18, person = Person.P1, anno = @otherAnno, arr = {1, 2, 3})
public void show01(){

}
```



### 特殊值：value

定义一个注释接口

```java
public @interface myAnno {
    // value是一个特殊的属性
    public abstract int value();
}
```

如果只有一个属性需要值，并且属性的名称是value，那么value可以省略，直接定义值即可

```java
@myAnno(100)
public void show(){

}
```



## 元注解

元注解：用于描述注解的注解

1. @Target：描述注解能够作用的位置

   - TYPE：可以作用类上  

   - METHOD：可以作用于方法上  

   - FIELD：可以作用于成员变量上

2. @Retention：描述注解被保留的阶段

   - Source：字节码文件中不会存在注解

   - Class：当前被描述的注解，会保留到class字节码文件中
   - Runtime：当前被描述的注解，会保留到class字节码文件中，并被JVM读取到

3. @Documented：描述注解是否可以被抽取到javadoc文档中

4. @Inherited：描述注解是否被子类继承

   即如果父类Animal被添加了注解，那么子类Dog也会自动继承父类的注解





## 使用注解

定义一个注解：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface Prop {
    String className();
    String Method();
}
```

使用注解的方式代替加载配置文件的方式，测试类：

```java
@Prop(className = "Annotation.Person", Method = "eat")
public class anno {
		// 解析注解
    public static void main(String[] args) {
    
      	// 获得当前类的字节码文件
        Class<anno> reflectClass = anno.class;
        // 获得注解对象
        Prop annotation = reflectClass.getAnnotation(Prop.class);
        // 调用注解对象中定义的抽象方法，获取返回值
        String className = annotation.className();
        String method = annotation.Method();
    }

}
```

其中，`reflectClass.getAnnotation(Prop.class)` 的作用相当于在内存中生成了一个实现该注解接口的子类对象，即：

```java
class PropImplement implements Prop {
    public String className() {
        return "Annotation.Person";
    }

    public String Method() {
        return "eat";
    }
}
```

因此，只要在注解中传入className和method，就可以利用注解在代码中获得其具体值，相当于加载了配置文件。





