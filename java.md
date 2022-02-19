# Java

## 1.Java基础编程知识

### 1.1 Java关键字

特点：关键字中所有字母都是**小写**

> 用于定义数据类型的关键字

|  class  | interface | enum  |  byte  | short |
| :-----: | :-------: | :---: | :----: | :---: |
|   int   |   long    | float | double | char  |
| boolean |   void    |       |        |       |

> 用于定义流程控制的关键字

|   if   | else | switch | case  | default  |
| :----: | :--: | :----: | :---: | :------: |
| while  |  do  |  for   | break | continue |
| return |      |        |       |          |

> 其他关键字

| native | strictfp | transient | volatile | assert |
| ------ | -------- | --------- | -------- | ------ |

标识符的使用

+ 由26个英文字符大小写，0-9，_和$组成
+ 数字不可以开头
+ 不可以使用关键字和保留字，但是可以包含关键字和保留字
+ Java中严格区分大小写，长度无限制
+ 标识符中不能包含空格

### 1.2 变量的分类

**八种基本数据类型**

+ 基本数据类型
  + 数值型
    + 整数型：byte, short, int, long
    + 浮点型：float, double
  + 字符型：char
  + 布尔型：boolean
+ 引用数据类型
  + 类(class)
  + 接口(interface)
  + 数组([])

详细说明：

+ 整型：byte(1字节=8bit) , short(2字节), int(4字节), long(8字节)

byte: -2^7 ~ 2^7 - 1

short: -2^15 ~ 2^15 - 1

int：-2^31 ~ 2^31 - 1

long： -2^63 ~ 2^63 - 1

+ 浮点型：float(4字节), double(8字节)

  + float：2^-149 ~ 2^128 - 1 **(-1)^S\*[2^(E-127)]\*1.M**

    其中-149的来历，指数位全为0，即指数值为-127，这个时候尾数取最小，2^(-23)，即-127-23=-150，全0和全1为特殊值，不作为范围内的值，故取-149

  + double：2^-1074 ~ 2^1024 - 1 **(-1)^S\*[2^(E-1023)]\*1.M**

  ![avatar](javaImage\float_double.png)

**关于浮点型float和double的补充说明：**

+ 在java编程语言中浮点数默认的是double型，如果要使用float需要在小数后加上f
+ double和float在运算中，**可能**会出现舍入误差

补充：

![avatar](javaImage\base.jpg)



### 1.3 对面向对象的理解

**面向对象的三大特性：封装，继承，多态**

①为什么要设计封装性？

首先，我们的程序设计追求“高内聚，低耦合”

+ 高内聚：类的内部数据操作细节自己完成，不允许外部干涉
+ 低耦合：仅对外暴露少量的方法用于调用

其次，隐藏对象内部的复杂性，只对外公开简单的接口，便于外界调用，从而提高系统的可扩展性和可维护性。

最后，封装性的具体体现？

+ 将类的属性私有化，同时提供公共的获取和设置此属性的方法
+ 不对外暴露私有化方法，即外界无法调用类的内部私有方法，隐藏类的实现细节
+ 单例模式，即构造器私有化

②为什么要设计继承性？

​       继承是描述类与类之间的关系，通过继承，可以在无须重新编写原有类的情况下，对原有类的功能进行扩展。继承性不仅增强了代码的复用性，提高了开发效率，而且为程序的修改补充提供了便利。同时也为多态性的实现提供前提。

+ 子类A继承父类B后，类A就获取了父类B中的所有属性和方法。但是因为封装性，使得子类不能直接调用父类中的部分结构。
+ Java中的类是单继承：一个类只能有一个父类。

③为什么要设计多态性？

多态是父类的对象指向子类的对象，表现为方法的重写（注意与重载的区别），**是运行时行为**

+ 实现代码的通用性

具体体现：

+ 抽象类、接口的使用（抽象类和接口不能实例化）
+ Object类中定义的public boolean equals(Object obj){}

### 1.4 equals和==的区别

> equals和==区别(null哪个能用), 如果没有重写equals方法,那么a==b和a.equals(b)一样吗(2020字节提前批)

equals(): 方法

1. 是一个方法，而非运算符
2. 只能适用于引用数据类型
3. Object类中equals()的定义： public boolean equals(Object obj) { return (this == obj); }

- 说明：Object类中定义的equals()和==的作用是相同的：比较两个对象的地址值是否相同.即两个引用是否指向同一个对象实体

1. 像String、Date、File、包装类等都重写了Object类中的equals()方法。重写以后，比较的不是两个引用的地址是否相同，而是比较两个对象的"实体内容"是否相同。
2. 通常情况下，我们自定义的类如果使用equals()的话，也通常是比较两个对象的"实体内容"是否相同。那么，我们就需要对Object类中的equals()进行重写.重写的原则：比较两个对象的实体内容是否相同.

== ：运算符

1. 可以使用在基本数据类型变量和引用数据类型变量中
2. 如果比较的是基本数据类型变量：比较两个变量保存的数据是否相等。（不一定类型要相同）

- 如果比较的是引用数据类型变量：比较两个对象的地址值是否相同.即两个引用是否指向同一个对象实体
- 补充： == 符号使用时，必须保证符号左右两边的变量类型一致。

所以, ==可以判断对象为null, equals()不能, 如果该类没有重写equals方法, 则使用Object类的equals的方法, 而Object中的equals使用就是==判断

### 1.5 hashCode()和equals()函数

两个函数都是用来对比两个对象是否相等一致。

hashCode()

+ 在应用程序执行期间对同一个对象多次调用hashCode()方法时，返回的值必须相同。

+ 如果两个对象根据equals()方法相等，则对两个对象中的每个调用hashCode()方法必须产生相同的整数结果。
+ 如果根据equals()方法，两个对象不相等，则不需要对两个对象中的每个hashCode()方法都产生不同的整数结果（即两个对象hashCode值不同，两个对象也可能相等）

equals()：

```java
// Object对象中的equals()方法
public boolean equals(Object obj) {
        return (this == obj);
}
```

两个函数之间的区别：（性能和可靠性）

+ 重写Object对象中的equals()方法比较两个对象是否相等时，需要逐一对比对象中每个属性值是否相等，这样效率较低，而利用hashCode()方法只需要生成一个hash值即可，效率较高。
+ hashCode()方法的效率较高，为什么还需要equals()方法，因为hashCode()并不是绝对可靠的，所以我们在比较两个对象是否相等时，先比较两个对象的hashCode值是否相等，如果不相等则不需要进行下面比较了，如果相等再继续使用equals()判断对象的内部属性是否相等。在提高效率的同时，也保证了绝对的可靠性。

### 1.6 String的理解

> String三姐妹

String：不可变字符序列，底层采用final char[]存储

StringBuffer：可变字符序列， 线程安全，效率低， 底层采用transient char[]存储

StringBuilder：可变字符序列， jdk5.0新增， 线程不安全， 效率高， 底层采用char[]存储

**String**

+ String类声明为final, 不可被继承；可序列化并且可以比较大小
+ String内部定义了final char[] value用于存储字符串数据
+ 通过字面量的方式赋值(区别于new给一个字符串赋值，此时的字符串声明在字符串常量池中)
+ 字符串常量池中不会存储相同的字符串

**不可变性的说明**

+ 当对字符串重新进行赋值时，需要重新指定内存区域进行赋值，不能在原有的value内存区域覆盖
+ 当对现有的字符串进行连接操作时，也需要重新指定新的内存区域，不能使用原有的value进行赋值
+ 当调用String的replace()方法时修改指定的字符或者字符串时，也需要重新指定新的内存区域。

**字符串常量池中不会存储相同内容的字符串**

+ String的String Pool是一个固定大小的HashTable，默认值大小长度是1009。如果放进String Pool的String非常多，就会造成Hash冲突严重，从而导致链表非常长，而链表长了后直接造成的影响就是当调用String.intern时的性能下降。
+ 使用**-XX:StringTableSize**可以设置StringTable的长度

**字符串拼接操作**

+ 常量与常量的拼接结果放在常量池中，原理是编译期优化
+ 常量池中不会存在相同内容的常量
+ 只要其中一个是变量，结果就在堆中。变量拼接的原理是StringBuilder
+ 如果拼接的结果调用intern()方法，则主动将常量池中还没有的字符串对象放入池中，并返回此对象地址。

```java
@Test
public void test3(){
    String s1 = "a";
    String s2 = "b";
    String s3 = "ab";
    /*
    如下的s1 + s2的细节
    1、StringBuilder s = new StringBuilder();
    2、s.append("a");
    3、s.append("b");
    4、s.toString(); // 约等于new String("ab")
    */
    String s4 = s1 + s2;
    System.out.println(s3 == s4); // false
}
/*
通过StringBuilder的append方法的方式添加字符串的效率要远高于使用String的字符串拼接(+)方式
详情：① StringBuilder的append方法中，自始至终只创建了一个StringBuilder对象，
使用String拼接的方法过程中，会创建多个StringBuilder对象
②使用String拼接的方法过程：内存中创建了较多的StringBuilder和String对象，内存占用大；如果要进行垃圾回收会占用较多时间。

改进的方式：使用String的带参构造(基本知道字符串长度)，减少扩容带来的开销，提高效率。
*/
```

> intern的理解

如何保证变量s指向的是字符串常量池中的数据？

方式一：String s = "johncarraway";// 字面量定义的方式

方式二：String s = new String("johncarraway").intern();



> new String("ab")到底创建了几个对象

两个对象，看字节码

```java
public class StringNewTest{
    
    public static void main(String[] args) throws IOException{
        
        String str = new String("ab");
        /*
        共两个对象，一是行号0的new String()，在堆空间创建的
        二是行号4中的 字符串常量池中的对象"ab"
        */
    }
}
/*对应的字节码
 0 new #2 <java/lang/String>
 3 dup
 4 ldc #3 <ab>
 6 invokespecial #4 <java/lang/String.<init> : (Ljava/lang/String;)V>
 9 astore_1
10 return
*/
```

> new String("a") + new String("b")一共造了几个对象

```java
public class StringNewTest{
    
    public static void main(String[] args) throws IOException{
        
        String str = new String("a") + new String("b");
        /*
        对象1：new StringBuilder()
        对象2：new String("a")
        对象3：常量池中的对象"a"
        对象4：new String("b")
        对象5：常量池中的"b"
        如果有等号左边，则还会有第六个对象：StringBuilder的toString()方法
        对象6：return new String()
        强调，toString()的调用，在字符串常量池中，没有生成"ab"
        */
    }
}
/*对应的字节码
 0 new #2 <java/lang/StringBuilder>
 3 dup
 4 invokespecial #3 <java/lang/StringBuilder.<init> : ()V>
 7 new #4 <java/lang/String>
10 dup
11 ldc #5 <a>
13 invokespecial #6 <java/lang/String.<init> : (Ljava/lang/String;)V>
16 invokevirtual #7 <java/lang/StringBuilder.append : (Ljava/lang/String;)Ljava/lang/StringBuilder;>
19 new #4 <java/lang/String>
22 dup
23 ldc #8 <b>
25 invokespecial #6 <java/lang/String.<init> : (Ljava/lang/String;)V>
28 invokevirtual #7 <java/lang/StringBuilder.append : (Ljava/lang/String;)Ljava/lang/StringBuilder;>
31 invokevirtual #9 <java/lang/StringBuilder.toString : ()Ljava/lang/String;>
34 astore_1
35 return
*/
```

> 面试题

```java
public class StringIntern1{
    
    public static void main(String[] args) throws IOException{
        
        String s = new String("1");// 在堆空间和字符串常量池中均有一个对象"1"
        s.intern();// 此时调用此方法没有太大意义，在字符串常量池中已经存在对象"1"了
        String s2 = "1";
        System.out.println(s == s2);// jdk6:false  jdk7/8:false
        
        String s3 = new String("1") + new String("1");// s3记录的地址为：new String("11")在堆空间的地址
        // 上一步执行为在字符串常量池中是没有对象"11"的
        s3.intern();// 此步骤中字符串常量池中存在对象"11"了,如何理解：
        /*
        在jdk6中，使用上一行代码执行时，在常量池中生成"11"的地址
        在jdk7/8中，字符串常量池在堆空间中，我们在字符串常量池中创建"11"时，为了节省空间，保存的是堆空间中new String("11")的地址，所以s3==s4为true
        */
        System.out.println(s3 == s4);// jdk6:false  jdk7/8:true
    }
    
}
```

### 1.7 final和static关键字

static和final都不能被重写, 静态方法在编译期就被确定了, 所以不能被重写.

**final**

1. final 用来修饰一个类:此类不能被其他类所继承。比如：String类、System类、StringBuffer类
2. final 用来修饰方法：表明此方法不可以被重写比如：Object类中getClass()
3. final 用来修饰变量：此时的"变量"就称为是一个常量
4. final修饰属性：可以考虑赋值的位置：显式初始化、代码块中初始化、构造器中初始化
5. final修饰局部变量：尤其是使用final修饰形参时，表明此形参是一个常量。当我们调用此方法时，给常量形参赋一个实参。一旦赋值以后，就只能在方法体内使用此形参，但不能进行重新赋值。

**static**

**方便在没有创建对象的情况下进行调用(方法/变量)**

+ static修饰属性：静态变量（或类变量）
+ static修饰方法：静态方法
+ static修饰代码块：静态代码块
+ static修饰类：静态类(一般为静态内部类)

1. 修饰属性，是否使用static修饰，又分为：静态属性 vs 非静态属性(实例变量)
   + 实例变量(非静态)：我们创建了类的多个对象，每个对象都独立的拥一套类中的非静态属性。当修改其中一个对象中的非静态属性时，不会导致其他对象中同样的属性值的修改。 
   + 静态变量(静态)：我们创建了类的多个对象，多个对象共享同一个静态变量。当通过某一个对象修改静态变量时，会导致其他对象调用此静态变量时，是修改过了的。
   + **static是不允许用来修饰局部变量**
2. 修饰方法：static方法一般称作静态方法，由于静态方法不依赖于任何对象就可以进行访问，因此对于静态方法来说，是没有this的，因为它不依附于任何对象，既然都没有对象，就谈不上this了。
3. 修饰代码块：static块可以置于类中的任何地方，类中可以有多个static块。在类初次被加载的时候，会按照static块的顺序来执行每个static块，并且只会执行一次。
4. static修饰属性的其他说明： 
   + 静态变量随着类的加载而加载。可以通过"类.静态变量"的方式进行调用 
   + 静态变量的加载要早于对象的创建。
   + 由于类只会加载一次，则静态变量在内存中也只会存在一份：存在方法区的静态域中。

### 1.8 深拷贝与浅拷贝

> 浅拷贝：引用类型只复制引用

创建一个新对象，然后将当前对象的非静态字段复制到该新对象，如果字段是值类型的，那么对该字段执行复制；如果该字段是引用类型的话，则复制引用但不复制引用的对象。**因此，原始对象及其副本引用同一个对象**

>  深拷贝：所有属性都复制独立一份

创建一个新对象，然后将当前对象的非静态字段复制到该新对象，无论该字段是值类型的还是引用类型，都复制独立的一份。当你修改其中一个对象的任何内容时，都不会影响另一个对象的内容。

如何实现深拷贝？

Object类中提供的clone()方法只能实现浅拷贝

+ 让每个引用类型都重写clone()方法
+ 利用序列化：序列化是将对象写到流中便于传输，而反序列化则是把对象从流中读取出来。这里写到流中的对象则是原始对象的一个拷贝，因为原始对象还存在 JVM 中，所以我们可以利用对象的序列化产生克隆对象，然后通过反序列化获取这个对象。

```java
//深度拷贝
public Object deepClone() throws Exception{
    // 序列化
    ByteArrayOutputStream bos = new ByteArrayOutputStream();
    ObjectOutputStream oos = new ObjectOutputStream(bos);

    oos.writeObject(this);

    // 反序列化
    ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
    ObjectInputStream ois = new ObjectInputStream(bis);

    return ois.readObject();
}
```

### 1.9 值传递与引用传递的区别

|          | 值传递                 | 引用传递               |
| -------- | ---------------------- | ---------------------- |
| 根本区别 | 会创建副本(copy)       | 不创建副本             |
| 因此     | 函数中无法改变原始对象 | 函数中可以改变原始对象 |



### 1.10 接口和抽象类的区别

> abstract：可以修饰类或者方法

修饰类：抽象类

+ 此类不能实例化
+ 抽象类中一定有构造器，便于子类实例化时调用（涉及：子类对象实例化的全过程）
+ 开发中，都会提供抽象类的子类，让子类对象实例化，完成相关的操作 --->抽象的使用前提：继承性

修饰方法：抽象方法

+ 抽象方法只方法的声明，没方法体
+ 包含抽象方法的类，一定是一个抽象类。反之，抽象类中可以没有抽象方法的。
+ 若子类重写了父类中的所的抽象方法后，此子类方可实例化；若子类没重写父类中的所有的抽象方法，则此子类也是一个抽象类，需要使用abstract修饰

注意：

+ abstract不能用来修饰：属性、构造器等结构

+ abstract不能用来修饰私方法、静态方法、final的方法、final的类

具体应用：

IO流中涉及到的抽象类：InputStream/OutputStream / Reader /Writer。在其内部定义了抽象的read()、write()方法。

>  interface：接口

JDK7及以前：只能定义全局常量和抽象方法
 * 			全局常量：public static final的.但是书写时，可以省略不写
 * 			抽象方法：public abstract的

JDK8：除了定义全局常量和抽象方法之外，还可以定义静态方法、默认方法

**接口中不能定义构造器的！意味着接口不可以实例化**

Java类可以实现多个接口   --->弥补了Java单继承性的局限性

接口与接口之间可以继承，而且可以多继承

**接口的具体使用，体现多态性**



相同点：不能实例化；都可以包含抽象方法的。
不同点：

+ 把抽象类和接口(java7,java8,java9)的定义、内部结构解释说明

+ 类：单继承性    接口：多继承

   类与接口：多实现

### 1.11 重载和重写的区别

① 概念

重载:

**同一个类中**,相同的方法名;

不同的参数列表,包括**不同的参数类型**和**不同的参数个数**

重写:子类继承父类以后，可以对父类中同名同参数的方法，进行覆盖操作. 重写以后，当创建子类对象以后，通过子类对象调用子父类中的同名同参数的方法时，实际执行的是子类重写父类的方法。

② 重载和重写的规则

③ 重载: 不表现为多态性
重写: 表现为多态性

重载，是指允许存在多个同名方法，而这些方法的参数不同。编译器根据方法不同的参数表，对同名方法的名称做修饰。对于编译器而言，这些同名方法就成了不同的方法。它们的调用地址在编译期就绑定了。Java的重载是可以包括父类和子类的，即子类可以重载父类的同名不同参数的方法。 所以：对于重载而言，在方法调用之前，编译器就已经确定了所要调用的方法，这称为“早绑定”或“静态绑定”； 而对于多态，只等到方法调用的那一刻，解释运行器才会确定所要调用的具体方法，这称为“晚绑定”或“动态绑定”。



### 1.12 包装类

为什么引入包装类？

**为了使基本数据类型的变量具有类的特征，引入包装类。**

包装类是什么？

| 基本数据类型 | 包装类    |
| ------------ | --------- |
| byte         | Byte      |
| short        | Short     |
| int          | Integer   |
| long         | Long      |
| float        | Float     |
| Double       | Double    |
| char         | Character |
| boolean      | Boolean   |

其中，Byte,Short,Integer,Long,Float,Double的父类都是Number

类型间的转换：(基本数据类型，包装类，String)

![avatar](javaImage\class_change.png)

装箱：基本数据类型 -> 包装类，通过调用对应的包装类的构造方法，如Integer t = new Integer(1);

自动装箱：如Integer s = 10;

拆箱：包装类 -> 基本数据类型，通过调用对应包装类的xxxValue()方法

自动拆箱：int s = new Integer(1);

**String--->基本数据类型、包装类:调用包装类的parseXxx(String s)**



### 1.13 java.lang.Object

1.Object类是所Java类的根父类

2.如果在类的声明中未使用extends关键字指明其父类，则默认父类为java.lang.Object类 

3.Object类中的功能(属性、方法)就具通用性。

+ 属性：无

+ 方法：equals() / toString() / getClass() /hashCode() / clone() / finalize() / wait() 、 notify()、notifyAll()

+ Object类只声明了一个空参的构造器

拓展：final, finally, finalize的区别？

+ final 是Java语言中的一个关键字

+ finally 是try catch finally中的一部分

+ finalize 是java.lang.Object类中的一个方法,用来销毁内存中没有指针指向的对象,一般不显示调用,而是有Java的垃圾回收器自行调用.

### 1.14 异常类型及编程中常见的异常有哪些

```java
java.lang.Throwable
  |-----java.lang.Error:一般不编写针对性的代码进行处理。
  |-----java.lang.Exception:可以进行异常的处理
      |------编译时异常(checked)
        |-----IOException
        |-----FileNotFoundException
        |-----ClassNotFoundException
      |------运行时异常(unchecked,RuntimeException)
        |-----NullPointerException
        |-----ArrayIndexOutOfBoundsException
        |-----ClassCastException
        |-----NumberFormatException
        |-----InputMismatchException
        |-----ArithmeticException
```

try catch能捕获到java.lang.Exception

编程中常见的异常有哪些？

- java.lang.OutOfMemoryError：内存不足错误。当可用内存不足以让Java虚拟机分配给一个对象时抛出该错误。 
- java.lang.StackOverflflowError：堆栈溢出错误。当一个应用递归调用的层次太深而导致堆栈溢出或者陷入死循环时抛出该错误。

## 2.Java高级编程知识