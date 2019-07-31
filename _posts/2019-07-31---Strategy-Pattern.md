---
layout: post
#标题配置
title: 策略模式及其实例应用
#时间配置
date:   2019-07-31 16:54:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

# Strategy Pattern
定义算法族，分别封装起来，让他们之间可以互相替换，此模式让算法变化独立于使用算法的客户；<br>
核心思想：这算法体现了几个设计原则，第一，把变化的代码从不变的代码中分离出来；第二，针对接口编程而不是具体类（定义了策略接口）；第三，多用组合/聚合，少用继承（客户通过组合方式使用策略）；<br>

## 结构
Context客户类：封装需要使用的策略接口作为成员变量；<br>
Strategy策略接口：定义抽象策略方法；<br>
ConcreteStrategy具体策略实现类：实现具体的策略方法；<br>

## 注意事项
1、策略模式的关键：分析项目中变化部分与不变部分；<br>
2、提供了可以替换继承关系的方法：策略模式将算法封装在独立的strategy类中使得你可以独立于其context改变它，使得它易于切换、易于理解、易于扩展；<br>
3、每添加一个策略就要增加一个类，当策略过多时会导致类数目庞大；<br>

## 实例代码
客户类：
```
public abstract class Duck {

	//属性, 策略接口
	FlyBehavior flyBehavior;
	//其它属性<->策略接口
	QuackBehavior quackBehavior;
	
	public Duck() {
	
	}

	public abstract void display();//显示鸭子信息
	
	public void quack() {
		System.out.println("鸭子嘎嘎叫~~");
	}
	
	public void swim() {
		System.out.println("鸭子会游泳~~");
	}
	
	public void fly() {
		
		//改进
		if(flyBehavior != null) {
			flyBehavior.fly();
		}
	}

	public void setFlyBehavior(FlyBehavior flyBehavior) {
		this.flyBehavior = flyBehavior;
	}
	
	
	public void setQuackBehavior(QuackBehavior quackBehavior) {
		this.quackBehavior = quackBehavior;
	}
	
	
	
}
```
```
public class WildDuck extends Duck {

	
	//构造器，传入FlyBehavor 的对象
	public  WildDuck() {
		// TODO Auto-generated constructor stub
		flyBehavior = new GoodFlyBehavior();
	}
	
	
	@Override
	public void display() {
		// TODO Auto-generated method stub
		System.out.println(" 这是野鸭 ");
	}

}
```
```
public class ToyDuck extends Duck{

	
	public ToyDuck() {
		// TODO Auto-generated constructor stub
		flyBehavior = new NoFlyBehavior();
	}
	
	@Override
	public void display() {
		// TODO Auto-generated method stub
		System.out.println("玩具鸭");
	}

	//需要重写父类的所有方法
	
	public void quack() {
		System.out.println("玩具鸭不能叫~~");
	}
	
	public void swim() {
		System.out.println("玩具鸭不会游泳~~");
	}
	
	
}
```
```
public class PekingDuck extends Duck {

	
	//假如北京鸭可以飞翔，但是飞翔技术一般
	public PekingDuck() {
		// TODO Auto-generated constructor stub
		flyBehavior = new BadFlyBehavior();
		
	}
	
	@Override
	public void display() {
		// TODO Auto-generated method stub
		System.out.println("~~北京鸭~~~");
	}
	
	

}
```
策略接口：
```
public interface FlyBehavior {
	
	void fly(); // 子类具体实现
}
```
具体策略实现类：
```
public class NoFlyBehavior implements FlyBehavior{

	@Override
	public void fly() {
		// TODO Auto-generated method stub
		System.out.println(" 不会飞翔  ");
	}

}
```
```
public class BadFlyBehavior implements FlyBehavior {

	@Override
	public void fly() {
		// TODO Auto-generated method stub
		System.out.println(" 飞翔技术一般 ");
	}

}
```
```
public class GoodFlyBehavior implements FlyBehavior {

	@Override
	public void fly() {
		// TODO Auto-generated method stub
		System.out.println(" 飞翔技术高超 ~~~");
	}

}
```
测试类：
```
public class Client {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		WildDuck wildDuck = new WildDuck();
		wildDuck.fly();//
		
		ToyDuck toyDuck = new ToyDuck();
		toyDuck.fly();
		
		PekingDuck pekingDuck = new PekingDuck();
		pekingDuck.fly();
		
		//动态改变某个对象的行为, 北京鸭 不能飞
		pekingDuck.setFlyBehavior(new NoFlyBehavior());
		System.out.println("北京鸭的实际飞翔能力");
		pekingDuck.fly();
	}

}
```
实现结果：<br>
![](https://itmanmzt.github.io/styles/images/celue/001.jpg){:align="center"}

<br>

<br>

<center>有Marin的地方就有你的收获</center>