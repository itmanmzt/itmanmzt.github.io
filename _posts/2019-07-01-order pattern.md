---
layout: post
#标题配置
title: 命令模式及其实例应用
#时间配置
date:   2019-07-01 21:50:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

# 命令模式（order pattern)

将一个请求封装成一个对象，从而让你使用不同的请求把客户端参数化，对请求排队或者记录请求日志，可以提供命令的撤销和恢复功能。

## 结构

Command类：是一个抽象类，类中对需要执行的命令进行声明，一般来说要对外公布一个execute方法用来执行命令。
ConcreteCommand类：Command类的实现类，对抽象类中声明的方法进行实现。
Client类：最终的客户端调用类。
        
以上三个类的作用应该是比较好理解的，下面我们重点说一下Invoker类和Recevier类。

Invoker类：调用者，负责调用命令。
Receiver类：接收者，负责接收命令并且执行命令。

## 优缺点

 首先，命令模式的封装性很好：每个命令都被封装起来，对于客户端来说，需要什么功能就去调用相应的命令，而无需知道命令具体是怎么执行的。比如有一组文件操作的命令：新建文件、复制文件、删除文件。如果把这三个操作都封装成一个命令类，客户端只需要知道有这三个命令类即可，至于命令类中封装好的逻辑，客户端则无需知道。

  其次，命令模式的扩展性很好，在命令模式中，在接收者类中一般会对操作进行最基本的封装，命令类则通过对这些基本的操作进行二次封装，当增加新命令的时候，对命令类的编写一般不是从零开始的，有大量的接收者类可供调用，也有大量的命令类可供调用，代码的复用性很好。比如，文件的操作中，我们需要增加一个剪切文件的命令，则只需要把复制文件和删除文件这两个命令组合一下就行了，非常方便。

   最后说一下命令模式的缺点，那就是命令如果很多，开发起来就要头疼了。特别是很多简单的命令，实现起来就几行代码的事，而使用命令模式的话，不用管命令多简单，都需要写一个命令类来封装。

 ## 应用场景

 对于大多数请求-响应模式的功能，比较适合使用命令模式，正如命令模式定义说的那样，命令模式对实现记录日志、撤销操作等功能比较方便。

## 代码实现
假设某个公司需要设计一个多用功能的遥控器。基本的需求如下：

该遥控器有可以控制风扇，白炽灯，热水器等等的多对开关，而且可能还有其他的电器，暂时不做其功能，但是希望可以保留接口，用的时间可以方便的扩展。

除上面的需求之外，还需要有个按钮，可以撤销上一步的操作。

<br>
Receiver类：
```
public class Fan {
    public void fanOn(){
        System.out.println("风扇开启");
    }
    public void fanOff(){
        System.out.println("风扇关闭");
    }
}

```
```
public class Light {
    public void lightOn(){
        System.out.println("灯亮");
    }
    public void lightOff(){
        System.out.println("灯关");
    }
}
```
```
public class Heater {
    public void heaterOn(){
        System.out.println("加热器开");
    }
    public void heaterOff(){
        System.out.println("加热器关");
    }
}
```
Command类：
```
public interface Command {
    void excute();
    void back();
}
```
ConcreteCommand类：
```
public class FanOnCommand implements Command {
    Fan fan;
    public FanOnCommand(Fan fan) {
        this.fan=fan;
    }

    @Override
    public void excute() {
    this.fan.fanOn();
    }

    @Override
    public void back() {
        this.fan.fanOff();
    }
}
```
```
public class FanOffCommand implements Command {
    Fan fan;

    public FanOffCommand(Fan fan) {
        this.fan = fan;
    }

    @Override
    public void excute() {
        this.fan.fanOff();
    }

    @Override
    public void back() {
        this.fan.fanOn();
    }
}
```
```
public class LightOnCommand implements Command {
    Light light;
    public LightOnCommand(Light light) {
        this.light=light;
    }

    @Override
    public void excute() {
       this.light.lightOn();
    }

    @Override
    public void back() {
        this.light.lightOff();
    }
}
```
```
public class LightOffCommand implements Command {
    Light light;
    public LightOffCommand(Light light) {
        this.light=light;
    }

    @Override
    public void excute() {
        this.light.lightOff();
    }

    @Override
    public void back() {
        this.light.lightOn();
    }
}
```
```
public class HeaterOnCommand implements Command{
    Heater heater;
    public HeaterOnCommand(Heater heater) {
        this.heater=heater;
    }

    @Override
    public void excute() {
        this.heater.heaterOn();
    }

    @Override
    public void back() {
        this.heater.heaterOff();
    }
}
```
```
public class HeaterOffCommand implements Command {
    Heater heater;

    public HeaterOffCommand(Heater heater) {
        this.heater = heater;
    }

    @Override
    public void excute() {
        this.heater.heaterOff();
    }

    @Override
    public void back() {
        this.heater.heaterOn();
    }
}
```
Invoker类：
```
public class Controller {
    private Command[] onCommand;
    private Command[] offCommand;
    private Stack<Command> stack=new Stack<Command>();
    public Controller() {
        Command noCommand=new NoCommand();
        onCommand=new Command[4];
        offCommand=new Command[4];
        for(int i=0;i<4;i++){
            onCommand[i]=noCommand;
            offCommand[i]=noCommand;
        }
    }
    public void setCommand(int num,Command command1,Command command2){
        onCommand[num]=command1;
        offCommand[num]=command2;
    }
    public void on(int num){
        onCommand[num].excute();
        stack.push(onCommand[num]);
    }
    public void off(int num){
        offCommand[num].excute();
        stack.push(offCommand[num]);
    }
    public void back(){
        stack.pop().back();
    }

}
```
Client类：
```
public class Client {
    public static void main(String[] args) {
        Fan fan=new Fan();
        Light light=new Light();
        Heater heater=new Heater();

        FanOnCommand foc=new FanOnCommand(fan);
        FanOffCommand fofc=new FanOffCommand(fan);
        LightOnCommand loc=new LightOnCommand(light);
        LightOffCommand lofc=new LightOffCommand(light);
        HeaterOnCommand hoc=new HeaterOnCommand(heater);
        HeaterOffCommand hofc=new HeaterOffCommand(heater);

        Controller controller=new Controller();

        controller.setCommand(0,foc,fofc);
        controller.setCommand(1,loc,lofc);
        controller.setCommand(2,hoc,hofc);

        controller.on(0);
        controller.on(1);
        controller.on(2);
        controller.off(0);
        controller.off(1);
        controller.off(2);
        controller.back();
        controller.back();
        controller.back();
    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/mingling/001.jpg){:align="center"}<br><br>
## 扩展代码实现
下面我们讲在原来的代码的基础上扩展出一个实现遥控器对门的开关的功能。
Receiver类：
```
public class Door {
    public void doorOn(){
        System.out.println("门开");
    }
    public void doorOff(){
        System.out.println("门关");
    }
}
```
ConcreteCommand类：
```
public class DoorOnCommand implements Command {
    Door door;

    public DoorOnCommand(Door door) {
        this.door = door;
    }

    @Override
    public void excute() {
        this.door.doorOn();
    }

    @Override
    public void back() {
        this.door.doorOff();
    }
}
```
```
public class DoorOffCommand implements Command {
    Door door;

    public DoorOffCommand(Door door) {
        this.door = door;
    }

    @Override
    public void excute() {
        this.door.doorOff();
    }

    @Override
    public void back() {
        this.door.doorOn();
    }
}
```
Client类：
```
public class Client {
    public static void main(String[] args) {
        Door door=new Door();

        DoorOnCommand doc=new DoorOnCommand(door);
        DoorOffCommand dofc=new DoorOffCommand(door);

        Controller controller=new Controller();

        controller.setCommand(3,doc,dofc);

        controller.on(3);
        controller.off(3);
        controller.back();
        controller.back();

    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/mingling/002.jpg){:align="center"}<br><br>

## 总结

 对于一个场合到底用不用模式，这对所有的开发人员来说都是一个很纠结的问题。有时候，因为预见到需求上会发生的某些变化，为了系统的灵活性和可扩展性而使用了某种设计模式，但这个预见的需求偏偏没有，相反，没预见到的需求倒是来了不少，导致在修改代码的时候，使用的设计模式反而起了相反的作用，以至于整个项目组怨声载道。这样的例子，我相信每个程序设计者都遇到过。所以，基于敏捷开发的原则，我们在设计程序的时候，如果按照目前的需求，不使用某种模式也能很好地解决，那么我们就不要引入它，因为要引入一种设计模式并不困难，我们大可以在真正需要用到的时候再对系统进行一下，引入这个设计模式。

  拿命令模式来说吧，我们开发中，请求-响应模式的功能非常常见，一般来说，我们会把对请求的响应操作封装到一个方法中，这个封装的方法可以称之为命令，但不是命令模式。到底要不要把这种设计上升到模式的高度就要另行考虑了，因为，如果使用命令模式，就要引入调用者、接收者两个角色，原本放在一处的逻辑分散到了三个类中，设计时，必须考虑这样的代价是否值得。


  <br>

<br>

<center>有Marin的地方就有你的收获</center>

