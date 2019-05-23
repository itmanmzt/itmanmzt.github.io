---
layout: post
#标题配置
title: 组合模式及其实例应用
#时间配置
date:   2019-05-23 15:16:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

## 组合模式
组合模式(Composite Pattern)：组合多个对象形成树形结构以表示具有“整体—部分”关系的层次结构。组合模式对单个对象（即叶子对象）和组合对象（即容器对象）的使用具有一致性，组合模式又可以称为“整体—部分”(Part-Whole)模式，它是一种对象结构型模式。<br><br>

   在组合模式结构图中包含如下几个角色：

Component（抽象构件）：它可以是接口或抽象类，为叶子构件和容器构件对象声明接口，在该角色中可以包含所有子类共有行为的声明和实现。在抽象构件中定义了访问及管理它的子构件的方法，如增加子构件、删除子构件、获取子构件等。
    
Leaf（叶子构件）：它在组合结构中表示叶子节点对象，叶子节点没有子节点，它实现了在抽象构件中定义的行为。对于那些访问及管理子构件的方法，可以通过异常等方式进行处理。
    
 Composite（容器构件）：它在组合结构中表示容器节点对象，容器节点包含子节点，其子节点可以是叶子节点，也可以是容器节点，它提供一个集合用于存储子节点，实现了在抽象构件中定义的行为，包括那些访问及管理子构件的方法，在其业务方法中可以递归调用其子节点的业务方法。
<br><br>
组合模式的关键是定义了一个抽象构件类，它既可以代表叶子，又可以代表容器，而客户端针对该抽象构件类进行编程，无须知道它到底表示的是叶子还是容器，可以对其进行统一处理。同时容器对象与抽象构件类之间还建立一个聚合关联关系，在容器对象中既可以包含叶子，也可以包含容器，以此实现递归组合，形成一个树形结构。

### 优缺点
1、主要优点
<br>
组合模式的主要优点如下：
<br>
(1) 组合模式可以清楚地定义分层次的复杂对象，表示对象的全部或部分层次，它让客户端忽略了层次的差异，方便对整个层次结构进行控制。

(2) 客户端可以一致地使用一个组合结构或其中单个对象，不必关心处理的是单个对象还是整个组合结构，简化了客户端代码。

(3) 在组合模式中增加新的容器构件和叶子构件都很方便，无须对现有类库进行任何修改，符合“开闭原则”。

(4) 组合模式为树形结构的面向对象实现提供了一种灵活的解决方案，通过叶子对象和容器对象的递归组合，可以形成复杂的树形结构，但对树形结构的控制却非常简单。

2、主要缺点
<br>
组合模式的主要缺点如下：
<br>
在增加新构件时很难对容器中的构件类型进行限制。有时候我们希望一个容器中只能有某些特定类型的对象，例如在某个文件夹中只能包含文本文件，使用组合模式时，不能依赖类型系统来施加这些约束，因为它们都来自于相同的抽象层，在这种情况下，必须通过在运行时进行类型检查来实现，这个实现过程较为复杂。

### 适用场景
在以下情况下可以使用组合模式：<br>
（1）需要表示一个对象整体或部分层次，在具有整体和部分的层次结构中，希望通过一种方式忽略整体与部分的差异，可以一致地对待它们。<br>
（2）让客户能够忽略不同对象层次的变化，客户端可以针对抽象构件编程，无须关心对象层次结构的细节。<br>
（3）对象的结构是动态的并且复杂程度不一样，但客户需要一致地处理它们。<br>

### 实例代码
以我们常见的文件系统为例，进行组合模式的实例展示。
<br>
抽象构件：
```
public abstract class AbstractCom {
	public abstract void add(AbstractCom ac);
	public abstract void delete(AbstractCom ac);
	public abstract AbstractCom get(int i);
	public abstract void killVirus();
}
```
叶子构件：
```
public class Leaf extends AbstractCom {
	private String name;
	
public Leaf(String name) {
		this.name = name;
	}


	/* (non-Javadoc)
	 * @see it.mzt.composite.AbstractCom#add(it.mzt.composite.AbstractCom)
	 */
	@Override
	public void add(AbstractCom ac) {
		// TODO Auto-generated method stub
		System.out.println("error");
	}

	/* (non-Javadoc)
	 * @see it.mzt.composite.AbstractCom#delete(it.mzt.composite.AbstractCom)
	 */
	@Override
	public void delete(AbstractCom ac) {
		// TODO Auto-generated method stub
		System.out.println("error");
	}

	/* (non-Javadoc)
	 * @see it.mzt.composite.AbstractCom#get(int)
	 */
	@Override
	public AbstractCom get(int i) {
		// TODO Auto-generated method stub
		System.out.println("error");
		return null;
	}

	/* (non-Javadoc)
	 * @see it.mzt.composite.AbstractCom#killVirus()
	 */
	@Override
	public void killVirus() {
		// TODO Auto-generated method stub
		System.out.println("----对文件"+name+"进行杀毒");
	}

}
```
容器构件：
```
public class Floder extends AbstractCom {
	private String name;
	private ArrayList<AbstractCom> list=new ArrayList<AbstractCom>();
	public Floder(String name) {
		this.name = name;
	}

	/* (non-Javadoc)
	 * @see it.mzt.composite.AbstractCom#add(it.mzt.composite.AbstractCom)
	 */
	@Override
	public void add(AbstractCom ac) {
		// TODO Auto-generated method stub
		list.add(ac);
	}

	/* (non-Javadoc)
	 * @see it.mzt.composite.AbstractCom#delete(it.mzt.composite.AbstractCom)
	 */
	@Override
	public void delete(AbstractCom ac) {
		// TODO Auto-generated method stub
		list.remove(ac);
	}

	/* (non-Javadoc)
	 * @see it.mzt.composite.AbstractCom#get(int)
	 */
	@Override
	public AbstractCom get(int i) {
		// TODO Auto-generated method stub
		AbstractCom ac=(AbstractCom) list.get(i);
		return ac;
	}

	/* (non-Javadoc)
	 * @see it.mzt.composite.AbstractCom#killVirus()
	 */
	@Override
	public void killVirus() {
		System.out.println("----对文件夹"+name+"进行杀毒");
		// TODO Auto-generated method stub
		for(AbstractCom ac:list){
			ac.killVirus();
		}
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
		AbstractCom file1=new Leaf("文件1");
		AbstractCom file2=new Leaf("文件2");
		AbstractCom file3=new Leaf("文件3");
		
		AbstractCom floder=new Floder("文件夹1");
		
		floder.add(file1);
		floder.add(file2);
		floder.add(file3);
		
		floder.killVirus();
		System.out.println();
		
		AbstractCom file=floder.get(1);
		file.killVirus();
		System.out.println();
		
		floder.delete(file1);
		floder.killVirus();
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/zuhe/001.jpg){:align="center"}<br><br>

### 透明组合模式和安全组合模式
1、透明组合模式<br>
透明组合模式中，抽象构件Component中声明了所有用于管理成员对象的方法，包括add()、remove()以及getChild()等方法，这样做的好处是确保所有的构件类都有相同的接口。在客户端看来，叶子对象与容器对象所提供的方法是一致的，客户端可以相同地对待所有的对象。透明组合模式也是组合模式的标准形式，虽然下面的解决方案在客户端可以有不透明的实现方法，但是由于在抽象构件中包含add()、remove()等方法，因此它还是透明组合模式。
```
public abstract class AbstractCom{  
    public void add(AbstractFile file) {  
        System.out.println("error");  
    }  
      
    public void remove(AbstractFile file) {  
        System.out.println("error");  
    }  
      
    public AbstractFile get(int i) {  
        System.out.println("error");  
        return null;  
    }  
      
    public abstract void killVirus();  
}  
```
```
public class Test {  
    public static void main(String args[]) {  
        //不能透明处理叶子构件  
        Leaf file1,file2;  
        AbstractCom folder1;  
        //其他代码省略  
      }  
}  
```
透明组合模式的缺点是不够安全，因为叶子对象和容器对象在本质上是有区别的。叶子对象不可能有下一个层次的对象，即不可能包含成员对象，因此为其提供add()、remove()以及getChild()等方法是没有意义的，这在编译阶段不会出错，但在运行阶段如果调用这些方法可能会出错（如果没有提供相应的错误处理代码）。
<br><br>
2、安全组合模式<br>
安全组合模式中，在抽象构件Component中没有声明任何用于管理成员对象的方法，而是在Composite类中声明并实现这些方法。这种做法是安全的，因为根本不向叶子对象提供这些管理成员对象的方法，对于叶子对象，客户端不可能调用到这些方法，这就是下面方案所采用的实现方式。
```
public abstract class AbstractCom {  
    public abstract void killVirus();  
}  
```
```
public class Client {  
    public static void main(String args[]) {  
          
        AbstractCom file1,file2,file3;  
        Folder folder1; //不能透明处理容器构件  
        //其他代码省略  
    }  
}  
```
安全组合模式的缺点是不够透明，因为叶子构件和容器构件具有不同的方法，且容器构件中那些用于管理成员对象的方法没有在抽象构件类中定义，因此客户端不能完全针对抽象编程，必须有区别地对待叶子构件和容器构件。在实际应用中，安全组合模式的使用频率也非常高，在Java AWT中使用的组合模式就是安全组合模式。

<br>

<br>

<center>有Marin的地方就有你的收获</center>

