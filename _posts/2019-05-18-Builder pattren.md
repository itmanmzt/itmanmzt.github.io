---
layout: post
#标题配置
title:  建造者模式及其实例应用
#时间配置
date:   2019-05-18 11:40:00 +0800
#大类配置
categories: 设计模式
#小类配置
tag: 
---

* content
{:toc}


---
---

# 建造者模式
将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。<br>
在建造者模式中，有如下四个角色：

Product 产品类<br>
      通常是实现了模板方法模式，也就是有模板方法和基本方法。

Builder 抽象建造者<br>
      规范产品的组建，一般是由子类实现。

ConcreteBuilder 具体建造者<br>
      实现抽象类定义的所有方法，并且返回一个组件好的对象。

Director 导演<br>
      负责安排已有模块的顺序，然后告诉Builder开始建造，。
      建造者模式的通用源代码也比较简单，先看Product类，通常它是一个组合或继承（如模板方法模式）产生的类。

## 应用

1.建造者模式的优点<br>
<b>封装性</b><br>
使用建造者模式可以使客户端不必知道产品内部组成的细节，如例子中我们就不需要关心每一个具体的模型内部是如何实现的，产生的对象类型就是CarModel。<br>

<b>建造者独立，容易扩展</b><br>
不同建造类之间是相互独立的，对系统的扩展非常有利。<br>

<b>便于控制细节风险</b><br>
由于具体的建造者是独立的，因此可以对建造过程逐步细化，而不对其他的模块产生任何影响。<br>

2.建造者模式的使用场景<br>

1、相同的方法，不同的执行顺序，产生不同的事件结果时，可以采用建造者模式。<br>
2、多个部件或零件,都可以装配到一个对象中，但是产生的运行结果又不相同时，则可以使用该模式。<br>
3、产品类非常复杂，或者产品类中的调用顺序不同产生了不同的效能，这个时候使用建造者模式是非常合适。<br>
4、在对象创建过程中会使用到系统中的一些其它对象，这些对象在产品对象的创建过程中不易得到时，也可以采用建造者模式封装该对象的创建过程。该种场景，只能是一个补偿方法，因为一个对象不容易获得，而在设计阶段竟然没有发觉，而要通过创建者模式柔化创建过程，本身已经违反设计最初目标。

3.建造者模式和工厂模式的区别<br>

建造者模式最主要功能是基本方法的调用顺序安排，也就是这些基本方法已经实现了，通俗的说就是零件的装配，顺序不同产生的对象也不同；而工厂方法则重点是创建，创建零件时它的主要职责，你要什么对象我创造一个对象出来，组装顺序则不是他关心的。

## 交通工具生产实例
产品类：
```
public class Producer {
	public static void setMain(){
		System.out.println("主题构建完成");
	}
	public static void setTyre(){
		System.out.println("装上一个轮胎");
	}

}
```
抽象建造类：
```
public abstract class Builder {
	
	public abstract void design();
	public abstract void produce(); 

}
```
具体建造类：
```
public class BikeBuilder extends Builder{
	private Producer pro=new Producer();
	/* (non-Javadoc)
	 * @see it.mzt.jianzaozhe.Builder#design()
	 */
	@Override
	public void design() {
		// TODO Auto-generated method stub
		pro.setMain();
		pro.setTyre();
		pro.setTyre();
	}

	/* (non-Javadoc)
	 * @see it.mzt.jianzaozhe.Builder#produce()
	 */
	@Override
	public void produce() {
		// TODO Auto-generated method stub
		System.out.println("自行车生产完毕");
	}

}
```
```
public class TricycleBuilder extends Builder {
	private Producer pro=new Producer();

	/* (non-Javadoc)
	 * @see it.mzt.jianzaozhe.Builder#design()
	 */
	@Override
	public void design() {
		// TODO Auto-generated method stub
		pro.setMain();
		pro.setTyre();
		pro.setTyre();
		pro.setTyre();
	}

	/* (non-Javadoc)
	 * @see it.mzt.jianzaozhe.Builder#produce()
	 */
	@Override
	public void produce() {
		// TODO Auto-generated method stub
		System.out.println("三轮车生产完毕");
	}

}
```
```
public class CarBuilder extends Builder {
	private Producer pro=new Producer();

	/* (non-Javadoc)
	 * @see it.mzt.jianzaozhe.Builder#design()
	 */
	@Override
	public void design() {
		// TODO Auto-generated method stub
		pro.setMain();
		pro.setTyre();
		pro.setTyre();
		pro.setTyre();
		pro.setTyre();
	}

	/* (non-Javadoc)
	 * @see it.mzt.jianzaozhe.Builder#produce()
	 */
	@Override
	public void produce() {
		// TODO Auto-generated method stub
		System.out.println("小汽车生产完毕");
	}


}
```
导演类：
```
public class Directer {
	
	public void action(Builder builder){
		builder.design();
		builder.produce();
	}

}
```
测试类：
```
public class Test {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Builder builder1=new BikeBuilder();
		Builder builder2=new TricycleBuilder();
		Builder builder3=new CarBuilder();
		
		Directer dir=new Directer();
		dir.action(builder1);
		dir.action(builder2);
		dir.action(builder3);

	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/jianzaozhe/001.jpg){:align="center"}<br><br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>