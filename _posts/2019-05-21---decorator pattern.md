---
layout: post
#标题配置
title:  装饰者模式及其实例应用
#时间配置
date:   2019-05-21 16:02:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}

---
---

## 装饰者模式
装饰模式指的是在不必改变原类文件和使用继承的情况下，动态地扩展一个对象的功能。它是通过创建一个包装对象，也就是装饰来包裹真实的对象。

在装饰模式中的各个角色有：

抽象构件（Component）角色：给出一个抽象接口，以规范准备接收附加责任的对象。

具体构件（Concrete Component）角色：定义一个将要接收附加责任的类。

装饰（Decorator）角色：持有一个构件（Component）对象的实例，并定义一个与抽象构件接口一致的接口。

具体装饰（Concrete Decorator）角色：负责给构件对象"贴上"附加的责任。

### 为什么要使用装饰者模式
在谈装饰这模式之前，我想让大家思考一下，我们开发的时候为什么要遵守开闭原则（对拓展开放，对修改关闭）？这看起来是一个非常矛盾的事情，又要拓展功能，又不能够修改已有的代码。其实，这样做最主要的原因是防止因为修改已有代码引入新的BUG,已有的代码一般都是经过检测的，很少有BUG，如果直接在上面修改的话，很可能导致未知的错误，可能引起别的组件的故障，甚至瘫痪整个系统。

在明白上述这一点之后，我们就比较容易理解那些明明两三行代码就能完成的功能，为什么要在上面做各种抽象了，主要是为了三个方面：

1、代码重用（从更长远的角度看起来，简化开发）<br>
2、易于拓展（增加功能方便）<br>
3、便于维护（不修改已有代码，结构层次清晰）<br>

### 使用要点
1． 装饰者和被装饰对象有相同的超类型。

2． 可以用一个或多个装饰者包装一个对象。

3． 装饰者可以在所委托被装饰者的行为之前或之后，加上自己的行为，以达到特定的目的。

4． 对象可以在任何时候被装饰，所以可以在运行时动态的，不限量的用你喜欢的装饰者来装饰对象。

5． 装饰模式中使用继承的关键是想达到装饰者和被装饰对象的类型匹配，而不是获得其行为。

6． 装饰者一般对组件的客户是透明的，除非客户程序依赖于组件的具体类型。在实际项目中可以根据需要为装饰者添加新的行为，做到“半透明”装饰者。

### 使用场景及优缺点
1.需要扩展一个类的功能，或给一个类增加附加责任。 

2.需要动态地给一个对象增加功能，这些功能可以再动态地撤销。 

3.需要增加由一些基本功能的排列组合而产生的非常大量的功能，从而使继承关系变得不现实。

<b>优点：</b>
1. Decorator模式与继承关系的目的都是要扩展对象的功能，但是Decorator可以提供比继承更多的灵活性。

2. 通过使用不同的具体装饰类以及这些装饰类的排列组合，设计师可以创造出很多不同行为的组合。

<b>缺点：</b>
1. 这种比继承更加灵活机动的特性，也同时意味着更加多的复杂性。

2. 装饰模式会导致设计中出现许多小类，如果过度使用，会使程序变得很复杂。

3. 装饰模式是针对抽象组件（Component）类型编程。但是，如果你要针对具体组件编程时，就应该重新思考你的应用架构，以及装饰者是否合适。当然也可以改变Component接口，增加新的公开的行为，实现“半透明”的装饰者模式。在实际项目中要做出最佳选择。

### 装饰者模式和适配器模式的区别
1、关于新职责：适配器也可以在转换时增加新的职责，但其主要目的并不在此；而装饰者模式主要目的，就是给被装饰者增加新职责用的。

2、适配器模式是用新接口来调用原接口，原接口对新系统来说是不可见或者说不可用的；而装饰者模式原封不动的使用原接口，系统对装饰的对象也通过原接口来完成使用。

3、关于其包裹的对象：适配器是知道被适配者的详细情况的（就是那个类或那个接口）；而装饰者只知道其接口是什么，至于其具体类型（是基类还是其他派生类）只有在运行期间才知道。

### 实例代码
我们假设以下情景：有一家DIY奶茶厂商，要管理很多饮品，不同的配方就是一种产品，假设主料有茶，咖啡等等，配料有红豆，糖，牛奶，等等，我们要科学的对这些材料进行管理，按照面向对象的思维，每一种产品就是一个类，我的天，无数的组合岂不是无穷无尽的类，这样设计显然是不科学的。我们这时候可以用装饰者模式，我们看看装饰者模式是如何做的

我们先来观察这些产品有何特点：都是由主料和多种辅料复合而成的，这时候我们就要想办法，如何动态的去给主料里添加辅料，而不是在编译的时候直接写死。

抽象构建接口：
```
public interface AbComponent {
	public String description();
	public float cost();

}
```
具体构建类：
```
public class Mike implements AbComponent {

	/* (non-Javadoc)
	 * @see it.mzt.zhuangshizhe.AbComponent#description()
	 */
	public String description() {
		// TODO Auto-generated method stub
		return "牛奶";
	}

	/* (non-Javadoc)
	 * @see it.mzt.zhuangshizhe.AbComponent#cost()
	 */
	public float cost() {
		// TODO Auto-generated method stub
		return 8;
	}

}
```
```
public class Coffce implements AbComponent {

	/* (non-Javadoc)
	 * @see it.mzt.zhuangshizhe.AbComponent#description()
	 */
	public String description() {
		// TODO Auto-generated method stub
		return "咖啡";
	}

	/* (non-Javadoc)
	 * @see it.mzt.zhuangshizhe.AbComponent#cost()
	 */
	public float cost() {
		// TODO Auto-generated method stub
		return 10;
	}

}

```
抽象装饰类：
```
public abstract class ComponentDecorator implements AbComponent {

}
```
具体装饰类：
```
public class BoBa extends ComponentDecorator {
	AbComponent abc;
	
	public BoBa(AbComponent abc) {
		this.abc = abc;
	}

	/* (non-Javadoc)
	 * @see it.mzt.zhuangshizhe.AbComponent#description()
	 */
	public String description() {
		// TODO Auto-generated method stub
		return abc.description()+",波霸";
	}

	/* (non-Javadoc)
	 * @see it.mzt.zhuangshizhe.AbComponent#cost()
	 */
	public float cost() {
		// TODO Auto-generated method stub
		return abc.cost()+3;
	}

}
```
```
public class Ice extends ComponentDecorator {
	AbComponent abc;
	
	public Ice(AbComponent abc) {
		this.abc = abc;
	}

	/* (non-Javadoc)
	 * @see it.mzt.zhuangshizhe.AbComponent#description()
	 */
	public String description() {
		// TODO Auto-generated method stub
		return abc.description()+",冰块";
	}

	/* (non-Javadoc)
	 * @see it.mzt.zhuangshizhe.AbComponent#cost()
	 */
	public float cost() {
		// TODO Auto-generated method stub
		return abc.cost()+2;
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
		Coffce co=new Coffce();
		Ice CoIce=new Ice(co);
		BoBa CIBoBa=new BoBa(CoIce);
		System.out.println(CIBoBa.description()+":"+CIBoBa.cost());
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/decorator/001.jpg){:align="center"}<br><br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>