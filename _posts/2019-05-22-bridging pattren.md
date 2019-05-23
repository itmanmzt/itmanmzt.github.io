---
layout: post
#标题配置
title: 桥接模式及其实例应用
#时间配置
date:   2019-05-22 16:32:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

## 桥接模式
桥接模式是一种很实用的结构型设计模式，如果软件系统中某个类存在两个独立变化的维度，通过该模式可以将这两个维度分离出来，使两者可以独立扩展，让系统更加符合“单一职责原则”。与多层继承方案不同，它将两个独立变化的维度设计为两个独立的继承等级结构，并且在抽象层建立一个抽象关联，该关联关系类似一条连接两个独立继承结构的桥，故名桥接模式。<br><br>
桥接模式用一种巧妙的方式处理多层继承存在的问题，用抽象关联取代了传统的多层继承，将类之间的静态继承关系转换为动态的对象组合关系，使得系统更加灵活，并易于扩展，同时有效控制了系统中类的个数。
<br><br>
在桥接模式结构图中包含如下几个角色：<br>
Abstraction（抽象类）：用于定义抽象类的接口，它一般是抽象类而不是接口，其中定义了一个Implementor（实现类接口）类型的对象并可以维护该对象，它与Implementor之间具有关联关系，它既可以包含抽象业务方法，也可以包含具体业务方法。<br>
RefinedAbstraction（扩充抽象类）：扩充由Abstraction定义的接口，通常情况下它不再是抽象类而是具体类，它实现了在Abstraction中声明的抽象业务方法，在RefinedAbstraction中可以调用在Implementor中定义的业务方法。<br>
Implementor（实现类接口）：定义实现类的接口，这个接口不一定要与Abstraction的接口完全一致，事实上这两个接口可以完全不同，一般而言，Implementor接口仅提供基本操作，而Abstraction定义的接口可能会做更多更复杂的操作。Implementor接口对这些基本操作进行了声明，而具体实现交给其子类。通过关联关系，在Abstraction中不仅拥有自己的方法，还可以调用到Implementor中定义的方法，使用关联关系来替代继承关系。<br>
ConcreteImplementor（具体实现类）：具体实现Implementor接口，在不同的ConcreteImplementor中提供基本操作的不同实现，在程序运行时，ConcreteImplementor对象将替换其父类对象，提供给抽象类具体的业务操作方法。<br>

### 优缺点
1.桥接模式的优点

（1）实现了抽象和实现部分的分离

桥接模式分离了抽象部分和实现部分，从而极大的提供了系统的灵活性，让抽象部分和实现部分独立开来，分别定义接口，这有助于系统进行分层设计，从而产生更好的结构化系统。对于系统的高层部分，只需要知道抽象部分和实现部分的接口就可以了。

（2）更好的可扩展性

由于桥接模式把抽象部分和实现部分分离了，从而分别定义接口，这就使得抽象部分和实现部分可以分别独立扩展，而不会相互影响，大大的提供了系统的可扩展性。

（3）可动态的切换实现

由于桥接模式实现了抽象和实现的分离，所以在实现桥接模式时，就可以实现动态的选择和使用具体的实现。

（4）实现细节对客户端透明，可以对用户隐藏实现细节。

2.桥接模式的缺点

（1）桥接模式的引入增加了系统的理解和设计难度，由于聚合关联关系建立在抽象层，要求开发者针对抽象进行设计和编程。

（2）桥接模式要求正确识别出系统中两个独立变化的维度，因此其使用范围有一定的局限性。

### 使用场景
（1）如果一个系统需要在构件的抽象化角色和具体化角色之间增加更多的灵活性，避免在两个层次之间建立静态的继承联系，通过桥接模式可以使它们在抽象层建立一个关联关系。

（2）抽象化角色和实现化角色可以以继承的方式独立扩展而互不影响，在程序运行时可以动态将一个抽象化子类的对象和一个实现化子类的对象进行组合，即系统需要对抽象化角色和实现化角色进行动态耦合。

（3）一个类存在两个独立变化的维度，且这两个维度都需要进行扩展。

（4）虽然在系统中使用继承是没有问题的，但是由于抽象化角色和具体化角色需要独立变化，设计要求需要独立管理这两者。

（5）对于那些不希望使用继承或因为多层次继承导致系统类的个数急剧增加的系统，桥接模式尤为适用

### 实例代码
假设现在要你建立一套电脑的样式以及品牌的系统，电脑可以分为台式机、笔记本、平板电脑三种样式，可以有苹果、联想、华硕等多种品牌，如果按单纯的抽象实现来建立的话那么你需要建立3*n个实现类，但通过桥接模式你只需要建立对应品牌数量（n）的实现类即可。<br>
其中Computer是一个抽象类，不是接口，其中Brand（品牌）是其中的成员变量，我们就完成了一个电脑具有不同品牌，那么如果我们想添加一个品牌，就只是添加一个具体的实现类即可，就不需要添加三个了。
抽象类：
```
public abstract class Computer {

	private Brand brand;

	public Computer(Brand brand) {
		this.brand = brand;
	}

	public void sale(){
		this.brand.sale();
	}
	
}
```
实现类接口：
```
public interface Brand {

	public void sale();
}
```
扩充抽象类：
```
public class Desktop extends Computer {

	/**
	 * @param brand
	 */
	public Desktop(Brand brand) {
		super(brand);
		// TODO Auto-generated constructor stub
	}
	
	/* (non-Javadoc)
	 * @see it.mzt.bridging.Computer#sale()
	 */
	@Override
	public void sale() {
		// TODO Auto-generated method stub
		super.sale();
		System.out.println("销售台式机");
	}

}
```
```
public class Notebook extends Computer {

	/**
	 * @param brand
	 */
	public Notebook(Brand brand) {
		super(brand);
		// TODO Auto-generated constructor stub
	}
	
	/* (non-Javadoc)
	 * @see it.mzt.bridging.Computer#sale()
	 */
	@Override
	public void sale() {
		// TODO Auto-generated method stub
		super.sale();
		System.out.println("销售笔记本");
	}

}
```
```
public class Tablet extends Computer {

	/**
	 * @param brand
	 */
	public Tablet(Brand brand) {
		super(brand);
		// TODO Auto-generated constructor stub
	}
	
	/* (non-Javadoc)
	 * @see it.mzt.bridging.Computer#sale()
	 */
	@Override
	public void sale() {
		// TODO Auto-generated method stub
		super.sale();
		System.out.println("销售平板电脑");
	}

}
```
具体实现类：
```
public class Apple implements Brand {

	/* (non-Javadoc)
	 * @see it.mzt.bridging.Brand#sale()
	 */
	public void sale() {
		// TODO Auto-generated method stub
		System.out.println("销售苹果电脑");
	}

}
```
```
public class Lenovo implements Brand {

	/* (non-Javadoc)
	 * @see it.mzt.bridging.Brand#sale()
	 */
	public void sale() {
		// TODO Auto-generated method stub
		System.out.println("销售联想电脑");
	}

}
```
```
public class Asus implements Brand {

	/* (non-Javadoc)
	 * @see it.mzt.bridging.Brand#sale()
	 */
	public void sale() {
		// TODO Auto-generated method stub
		System.out.println("销售华硕电脑");
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
		Brand b1=new Apple();
		Brand b2=new Lenovo();
		Brand b3=new Asus();
		
		Computer c1=new Desktop(b1);
		Computer c2=new Notebook(b2);
		Computer c3=new Tablet(b3);
		
		c1.sale();
		c2.sale();
		c3.sale();
	}

}

```
运行结果：
![](https://itmanmzt.github.io/styles/images/qiaojie/001.jpg){:align="center"}<br><br>

<br>

<br>

<center>有Marin的地方就有你的收获</center>