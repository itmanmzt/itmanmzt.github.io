---
layout: post
#标题配置
title: 模板方法模式及其实例应用
#时间配置
date:   2019-07-30 19:27:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

# Template Method Pattern
模板方法模式是在一个抽象类公开定义了执行它的方法的模板，它的子类可以按需要重写方法实现，但调用将以抽象类中定义的方式进行；它是属于行为型模式<br>

## 结构
AbstractClass抽象类：类中实现了模板方法(可以通过final关键字防止子类覆盖)，定义了算法的骨架，里面调用了类的其他方法，这些方法可以是抽象方法也可以是具体的实现方法，具体子类需要去实现这些抽象方法；<br>
Concrete实现类：实现抽象方法以完成算法中特定子类的步骤；

## 实例代码
抽象类:
```
//抽象类，表示豆浆
public abstract class SoyaMilk {

	//模板方法, make , 模板方法可以做成final , 不让子类去覆盖.
	final void make() {
		
		select(); 
		addCondiments();
		soak();
		beat();
		
	}
	
	//选材料
	void select() {
		System.out.println("第一步：选择好的新鲜黄豆  ");
	}
	
	//添加不同的配料， 抽象方法, 子类具体实现
	abstract void addCondiments();
	
	//浸泡
	void soak() {
		System.out.println("第三步， 黄豆和配料开始浸泡， 需要3小时 ");
	}
	 
	void beat() {
		System.out.println("第四步：黄豆和配料放到豆浆机去打碎  ");
	}
}
```
实现子类：
```
public class RedBeanSoyaMilk extends SoyaMilk {

	@Override
	void addCondiments() {
		// TODO Auto-generated method stub
		System.out.println(" 加入上好的红豆 ");
	}

}
```
```
public class PeanutSoyaMilk extends SoyaMilk {

	@Override
	void addCondiments() {
		// TODO Auto-generated method stub
		System.out.println(" 加入上好的花生 ");
	}

}
```
测试类：
```
public class Client {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		//制作红豆豆浆
		
		System.out.println("----制作红豆豆浆----");
		SoyaMilk redBeanSoyaMilk = new RedBeanSoyaMilk();
		redBeanSoyaMilk.make();
		
		System.out.println("----制作花生豆浆----");
		SoyaMilk peanutSoyaMilk = new PeanutSoyaMilk();
		peanutSoyaMilk.make();
	}

}
```
实现结果：<br>
![](https://itmanmzt.github.io/styles/images/moban/001.jpg){:align="center"}

## 模板方法的钩子方法
在模板方法模式的父类中，我们可以定义一个方法，它默认不做任何事，子类可以视情况决定要不要覆盖它，这种方法称为“钩子”；<br><br>
添加钩子方法对上面代码进行改造：<br>
抽象类：
```
//抽象类，表示豆浆
public abstract class SoyaMilk {

	//模板方法, make , 模板方法可以做成final , 不让子类去覆盖.
	final void make() {
		
		select(); 
		if(customerWantCondiments()) {
			addCondiments();
		}
		soak();
		beat();
		
	}
	
	//选材料
	void select() {
		System.out.println("第一步：选择好的新鲜黄豆  ");
	}
	
	//添加不同的配料， 抽象方法, 子类具体实现
	abstract void addCondiments();
	
	//浸泡
	void soak() {
		System.out.println("第三步， 黄豆和配料开始浸泡， 需要3小时 ");
	}
	 
	void beat() {
		System.out.println("第四步：黄豆和配料放到豆浆机去打碎  ");
	}
	
	//钩子方法，决定是否需要添加配料
	boolean customerWantCondiments() {
		return true;
	}
}
```
钩子方法覆盖的实现子类：
```
public class PureSoyaMilk extends SoyaMilk{

	@Override
	void addCondiments() {
		// TODO Auto-generated method stub
		//空实现
	}
	
	@Override
	boolean customerWantCondiments() {
		// TODO Auto-generated method stub
		return false;
	}
 
}
```
测试类：
```
public class Client {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		//制作红豆豆浆
		
		System.out.println("----制作红豆豆浆----");
		SoyaMilk redBeanSoyaMilk = new RedBeanSoyaMilk();
		redBeanSoyaMilk.make();
		
		System.out.println("----制作花生豆浆----");
		SoyaMilk peanutSoyaMilk = new PeanutSoyaMilk();
		peanutSoyaMilk.make();
		
		System.out.println("----制作纯豆浆----");
		SoyaMilk pureSoyaMilk = new PureSoyaMilk();
		pureSoyaMilk.make();
	}

}
```
实现结果：<br>
![](https://itmanmzt.github.io/styles/images/moban/002.jpg){:align="center"}

## 使用场景
1、一次性实现一个算法的不变的部分，并将可变的行为留给子类来实现；<br>
2、各子类中公共的行为应被提取出来并集中到一个公共父类中以避免代码重复；<br>
3、控制子类的扩展；<br>

## 优缺点
优点：<br>
1、提高代码复用性<br>
将相同部分的代码放在抽象的父类中<br>
2、提高了拓展性<br>
将不同的代码放入不同的子类中，通过对子类的扩展增加新的行为<br>
3、实现了反向控制<br>
通过一个父类调用其子类的操作，通过对子类的扩展增加新的行为，实现了反向控制 & 符合“开闭原则”<br><br>
缺点：<br>
1、引入了抽象类，每一个不同的实现都需要一个子类来实现，导致类的个数增加，从而增加了系统实现的复杂度。

<br>

<br>

<center>有Marin的地方就有你的收获</center>

