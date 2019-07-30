---
layout: post
#标题配置
title: 访问者模式及其实例应用
#时间配置
date:   2019-07-30 21:03:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

# Visitor Pattern
封装一些作用于某种数据结构的各元素的操作，它可以在不改变数据结构的前提下定义作用于这些元素的新的操作，主要是将数据结构与数据操作分离，解决数据结构和操作耦合性问题；<br>
基本工作原理：在被访问的类里面加一个对外提供接待访问者的接口；

## 结构
Visitor抽象访问类：为该对象结构中的每一个类声明一个visit操作；<br>
ConcreteVisitor具体的访问类：实现每个有visitor声明的操作，是每个操作的实现部分；<br>
ObjectStructure数据结构类：能枚举它的元素，可以提供一个高层的接口，用来允许访问者访问元素；<br>
Element抽象接收类：定义一个accept方法，接收一个访问者对象；<br>
ConcereteElement具体接收类：为具体元素实现了accept方法；<br>

## 实例代码
抽象访问类：
```
public abstract class Action {
	private String type;

	public String getType() {
		return type;
	}

	public Action(String type) {
		this.type = type;
	}
	
	//得到男性 的测评
	public abstract void getManResult(Man man);
	
	//得到女的 测评
	public abstract void getWomanResult(Woman woman);
}
```
具体实现访问类：
```
public class Success extends Action {

	public Success(String type) {
		super(type);
	}

	@Override
	public void getManResult(Man man) {
		// TODO Auto-generated method stub
		System.out.println(man.getName()+"——赞成票");
	}

	@Override
	public void getWomanResult(Woman woman) {
		// TODO Auto-generated method stub
		System.out.println(woman.getName()+"——赞成票");
	}

}
```
```
public class Fail extends Action {

	public Fail(String type) {
		super(type);
	}

	@Override
	public void getManResult(Man man) {
		// TODO Auto-generated method stub
		System.out.println(man.getName()+"——反对票");
	}

	@Override
	public void getWomanResult(Woman woman) {
		// TODO Auto-generated method stub
		System.out.println(woman.getName()+"——反对票");
	}

}
```
抽象接收类：
```
public abstract class Person {
	private String name;
	private String act;
	
	public Person(String name) {
		this.name = name;
	}

	public String getAct() {
		return act;
	}

	public void setAct(String act) {
		this.act = act;
	}

	public String getName() {
		return name;
	}
	
	//提供一个方法，让访问者可以访问
	public abstract void accept(Action action);
}
```
具体实现接收类：
```
public class Man extends Person {

	public Man(String name) {
		super(name);
	}

	@Override
	public void accept(Action action) {
		// TODO Auto-generated method stub
		action.getManResult(this);
		this.setAct(action.getType());
	}

}
```
```
//说明
//1. 这里我们使用到了双分派, 即首先在客户端程序中，将具体状态作为参数传递Woman中(第一次分派)
//2. 然后Woman 类调用作为参数的 "具体方法" 中方法getWomanResult, 同时将自己(this)作为参数
//   传入，完成第二次的分派
public class Woman extends Person{

	public Woman(String name) {
		super(name);
	}

	@Override
	public void accept(Action action) {
		// TODO Auto-generated method stub
		action.getWomanResult(this);
		this.setAct(action.getType());
	}

}
```
数据结构类：
```
//数据结构，管理很多人（Man , Woman）
public class ObjectStructure {

	//维护了一个集合
	private List<Person> persons = new LinkedList<>();
	
	//增加到list
	public void attachSuc(Person p) {
		p.accept(new Success("成功"));
		persons.add(p);
	}
	public void attachFail(Person p){
		p.accept(new Fail("失败"));
		persons.add(p);
	}
	//移除
	public void detach(Person p) {
		persons.remove(p);
	}
	
	//显示测评情况
	public void display() {
		for(Person p: persons) {
			System.out.println(p.getName()+"——"+p.getAct());
		}
	}
}
```
测试类：
```
public class Client {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		ObjectStructure objectStructure = new ObjectStructure();
		System.out.println("------开始投票--------");
		objectStructure.attachSuc(new Man("男1"));
		objectStructure.attachSuc(new Woman("女1"));
		objectStructure.attachFail(new Man("男2"));
		objectStructure.attachFail(new Woman("女2"));
		System.out.println("-----投票结果统计-------");
		objectStructure.display();
	}

}
```
实现结果：<br>
![](https://itmanmzt.github.io/styles/images/fangwenzhe/001.jpg){:align="center"}

## 优缺点
优点 <br>
1、访问者模式符合单一职责原则、让程序具有优秀的扩展性、灵活性非常高 ；<br>
2、访问者模式可以对功能进行统一，可以做报表、UI、拦截器与过滤器，适用于数据结构相对稳定的系统；<br> 
缺点 <br>
1、具体元素对访问者公布细节，也就是说访问者关注了其他类的内部细节，这是迪米特法则所不建议的，这样造 成了具体元素变更比较困难 ；<br>
2、违背了依赖倒转原则。访问者依赖的是具体元素，而不是抽象元素 ；<br>
3、因此，如果一个系统有比较稳定的数据结构，又有经常变化的功能需求，那么访问者模式就是比较合适的 ；<br>

<br>

<br>

<center>有Marin的地方就有你的收获</center>