# DesignPattern

## 1. 七种设计原则

设计模式原则, 其实就是程序员在编程时, 应当遵守的原则, 也是各种设计模式的基础(即设计模式为什么是这样的设计依据)

**设计模式常用的七大原则**

1. 单一职责原则
2. 接口隔离原则
3. 依赖倒转(置)原则
4. 里氏替换原则
5. 开闭原则
6. 迪米特法则
7. 合成复用原则

### 1.1 单一职责原则

对类来说的, 即一个类应该只负责一项职责. 如果类A负责两个不同的职责: 职责1, 职责2; 当职责1需求变更而修改类A时, 可能造成职责2执行错误, 所以将类A的粒度分解为A1, A2

代码分析1
```java
public class SingleResponsibility01 {
    public static void main(String[] args) {
        Vehicle vehicle = new Vehicle();
        vehicle.run("汽车");
        vehicle.run("摩托车");
        vehicle.run("飞机");
    }
}
/*
交通工具类
1. 在方式1中的run方法中, 违反了单一职责原则
2. 解决方案非常简单, 根据交通工具运行的方法不同, 分解成不同的类即可
 */
class Vehicle{
    public void run(String vehicle){
        System.out.println(vehicle + "在公路上跑...");
    }
}

```
代码分析2
```java
public class SingleResponsibility02 {

    public static void main(String[] args) {
        RoadVehicle roadVehicle = new RoadVehicle();
        roadVehicle.run("摩托车");
    }
}
/*
方案2分析:
1. 遵守单一职责原则
2. 但是改动很大, 即将类分解, 同时修改客户端
3. 直接修改Vehicle类, 改动的代码较少
 */
class RoadVehicle{
    public void run(String vehicle){
        System.out.println(vehicle + "在公路上运行...");
    }
}
class AirVehicle{
    public void run(String vehicle){
        System.out.println(vehicle + "在天空上运行...");
    }
}
class WaterVehicle{
    public void run(String vehicle){
        System.out.println(vehicle + "在水上运行...");
    }
}
```
代码分析3
```java
public class SingleResponsibility03 {

    public static void main(String[] args) {
        Vehicle2 vehicle = new Vehicle2();

        vehicle.runRoad("摩托车");
        vehicle.runRoad("汽车");
        vehicle.runAir("飞机");
    }
}

/*
方式3分析:
1. 这种修改方法没有对原来的类做大的修改, 只是增加方法
2. 这里虽然没有在类级别上没有违反单一职责原则, 但是在方法级别上违反了单一职责原则
 */
class Vehicle2 {
    public void runRoad(String vehicle){
        System.out.println(vehicle + "在公路上跑...");
    }

    public void runAir(String vehicle){
        System.out.println(vehicle + "在天空上跑...");
    }

    public void runWater(String vehicle){
        System.out.println(vehicle + "在水上跑...");
    }
}
```
> 单一职责原则的注意事项和细节

1) 降低类的复杂度, 一个类只负责一项职责
2) 提高类的可读性, 可维护性
3) 降低变更引起的风险
4) 通常情况下, 我们应当遵守单一职责原则, 只有逻辑足够简单, 才可以在代码级违反单一职责原则; 只有类中的方法数量足够少, 才可以在方法级别保持单一职责原则.

### 1.2 接口隔离原则

什么是接口隔离原则: 即客户端不应该依赖它不需要的接口, **即一个类对另一个类的依赖应该建立在最小的接口上.**

代码分析1
```java
interface Interface1{
    void operation1();
    void operation2();
    void operation3();
    void operation4();
    void operation5();
}

class B implements Interface1{
    @Override
    public void operation1() {
        System.out.println("B中实现了" + " operation1");
    }
    @Override
    public void operation2() {
        System.out.println("B中实现了" + " operation2");
    }
    @Override
    public void operation3() {
        System.out.println("B中实现了" + " operation3");
    }
    @Override
    public void operation4() {
        System.out.println("B中实现了" + " operation4");
    }
    @Override
    public void operation5() {
        System.out.println("B中实现了" + " operation5");
    }
}

class D implements Interface1{
    @Override
    public void operation1() {
        System.out.println("D中实现了" + " operation1");
    }
    @Override
    public void operation2() {
        System.out.println("D中实现了" + " operation2");
    }
    @Override
    public void operation3() {
        System.out.println("D中实现了" + " operation3");
    }
    @Override
    public void operation4() {
        System.out.println("D中实现了" + " operation4");
    }
    @Override
    public void operation5() {
        System.out.println("D中实现了" + " operation5");
    }
}

/*
A 类通过Interface1接口依赖(使用)B类, 但是只会使用1, 2, 3方法
 */
class A{
    public void depend1(Interface1 interface1){
        interface1.operation1();
    }
    public void depend2(Interface1 interface1){
        interface1.operation2();
    }
    public void depend3(Interface1 interface1){
        interface1.operation3();
    }
}

/*
C 类通过Interface1接口依赖(使用)D类, 但是只会使用1, 4, 5方法
 */
class C{
    public void depend1(Interface1 interface1){
        interface1.operation1();
    }
    public void depend4(Interface1 interface1){
        interface1.operation4();
    }
    public void depend5(Interface1 interface1){
        interface1.operation5();
    }
}
```
代码分析2
```java
public class Segregation02 {

    public static void main(String[] args) {
        A a = new A();
        a.depend1(new B()); // A类通过接口依赖B类
        a.depend2(new B());
        a.depend3(new B());

        C c = new C();
        c.depend1(new D());
        c.depend4(new D());
        c.depend5(new D());
    }
}
/*
拆分接口: 将原来的Interface拆分为三个接口
*/
interface Interface1{
    void operation1();
}

interface Interface2{
    void operation2();
    void operation3();
}

interface Interface3{
    void operation4();
    void operation5();
}

class B implements Interface1, Interface2 {
    @Override
    public void operation1() {
        System.out.println("B中实现了" + " operation1");
    }
    @Override
    public void operation2() {
        System.out.println("B中实现了" + " operation2");
    }
    @Override
    public void operation3() {
        System.out.println("B中实现了" + " operation3");
    }
}

class D implements Interface1, Interface3 {
    @Override
    public void operation1() {
        System.out.println("D中实现了" + " operation1");
    }
    @Override
    public void operation4() {
        System.out.println("D中实现了" + " operation4");
    }
    @Override
    public void operation5() {
        System.out.println("D中实现了" + " operation5");
    }
}

/*
A 类通过Interface1, Interface2接口依赖(使用)B类, 但是只会使用1, 2, 3方法
 */
class A{
    public void depend1(Interface1 interface1){
        interface1.operation1();
    }
    public void depend2(Interface2 interface2){
        interface2.operation2();
    }
    public void depend3(Interface2 interface2){
        interface2.operation3();
    }
}
/*
C 类通过Interface1, Interface3接口依赖(使用)D类, 但是只会使用1, 4, 5方法
 */
class C{
    public void depend1(Interface1 interface1){
        interface1.operation1();
    }
    public void depend4(Interface3 interface1){
        interface1.operation4();
    }
    public void depend5(Interface3 interface1){
        interface1.operation5();
    }
}
```

### 1.3 依赖倒转原则

依赖倒转原则是什么?
1. 高层模块不应该依赖于底层模块, 二者应该依赖抽象对象
2. 抽象对象不应该依赖细节, 细节应该依赖对象
3. 依赖倒转的中心思想是面向接口编程
4. 依赖倒转原则是基于这样的设计思想: 相对于细节的多变性, 抽象的东西要稳定的多. 以抽象为基础搭建出来的架构比以细节为基础的架构要稳定的多, 在java中, 抽象是指接口或者抽象类, 细节指的是具体的实现类. 
5. 使用接口或者抽象类的目的是制定好规范, 而不涉及具体的操作, 把细节全部交给具体的实现类去完成. 

代码分析1
```java
public class DependencyInversion {

    public static void main(String[] args) {
        Person person = new Person();
        person.receive(new Email());
    }
}

class Email{
    public String getInfo(){
        return "Hello, this is email";
    }
}

/* 完成Person类接收消息功能
1. 简单, 比较容易想到
2. 如果我们获取的消息是wechat或者短信等, 则需要新增类, 同时Person也需要新增相应的接收方法
3. 解决思路: 引入一个IReceiver, 表示接受者, 这样Person和IReceiver发生依赖
    因为Email、WeChat等等都属于接收的范畴, 他们各自实现IReceiver接口就行, 这就符合我们的
    依赖倒转原则
*/
class Person{
    public void receive(Email email){
        System.out.println(email.getInfo());
    }
}
```
代码分析2
```java
public class DependencyInversion {
    public static void main(String[] args) {
        // 此时客户端无需改变
        Person person = new Person();
        person.receive(new Email());

        person.receive(new WeChat());
    }
}

// 定义一个接口
interface IReceiver{
    public String getInfo();
}
class Email implements IReceiver{
    public String getInfo(){
        return "Hello, this is email IReceiver";
    }
}
class WeChat implements IReceiver{
    public String getInfo() {
        return "Hello, this is wechat IReceiver";
    }
}
/* 完成Person类接收消息功能
*/
class Person{
    public void receive(IReceiver receiver){
        System.out.println(receiver.getInfo());
    }
}
```

> 依赖关系传递的三种方式

1. 接口传递
2. 构造方法传递
3. setter方法传递

代码分析
```java
public class DependencyPass {

    public static void main(String[] args) {
    	// 1. 通过接口传递依赖关系
        ChangHong changHong = new ChangHong();
//		OpenAndClose openAndClose = new OpenAndClose();
//		openAndClose.open(changHong);

        // 2.通过构造方法传递
//		OpenAndClose openAndClose = new OpenAndClose(changHong);
//		openAndClose.open();
        // 3.通过setter方法传递
        OpenAndClose openAndClose = new OpenAndClose();
        openAndClose.setTv(changHong);
        openAndClose.open();

    }

}

// interface IOpenAndClose {
// public void open(ITV tv); //抽象类接口
// }
//
// interface ITV { //ITV接口
// public void play();
// }
// 
// class ChangHong implements ITV {
//
//	@Override
//	public void play() {
//		System.out.println("ChangHong TV is opening");
//	}
//	 
// }

// class OpenAndClose implements IOpenAndClose{
// public void open(ITV tv){
// tv.play();
// }
// }

// interface IOpenAndClose {
// public void open();
// }
// interface ITV { //ITV接口
// public void play();
// }
// class OpenAndClose implements IOpenAndClose{
// public ITV tv;
// public OpenAndClose(ITV tv){
// this.tv = tv;
// }
// public void open(){
// this.tv.play();
// }
// }


// 3. 通过setter方法传递依赖关系
interface IOpenAndClose {
    public void open();

    public void setTv(ITV tv);
}

interface ITV { // ITV接口
    public void play();
}

class OpenAndClose implements IOpenAndClose {
    private ITV tv;

    public void setTv(ITV tv) {
        this.tv = tv;
    }

    public void open() {
        this.tv.play();
    }
}
class ChangHong implements ITV {

    @Override
    public void play() {
        System.out.println("ChangHong is opening");
    }
}
```