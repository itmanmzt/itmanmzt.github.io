---
layout: post
#标题配置
title: 外观模式及其实例应用
#时间配置
date:   2019-05-23 21:18:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

# 外观模式
外观模式（Facade）,他隐藏了系统的复杂性，并向客户端提供了一个可以访问系统的接口。这种类型的设计模式属于结构性模式。为子系统中的一组接口提供了一个统一的访问接口，这个接口使得子系统更容易被访问或者使用。
<br><br>
外观模式包含如下两个角色：

 (1) Facade（外观角色）：在客户端可以调用它的方法，在外观角色中可以知道相关的（一个或者多个）子系统的功能和责任；在正常情况下，它将所有从客户端发来的请求委派到相应的子系统去，传递给相应的子系统对象处理。

(2) SubSystem（子系统角色）：在软件系统中可以有一个或者多个子系统角色，每一个子系统可以不是一个单独的类，而是一个类的集合，它实现子系统的功能；每一个子系统都可以被客户端直接调用，或者被外观角色调用，它处理由外观类传过来的请求；子系统并不知道外观的存在，对于子系统而言，外观角色仅仅是另外一个客户端而已。

## 使用场景及使用好处
使用场景：
<br>
1- 为复杂的模块或子系统提供外界访问的模块；
<br>
2- 子系统相互独立；
<br>
3- 在层析结构中，可以使用外观模式定义系统的每一层的入口。
<br>
使用好处：
<br>
 松散耦合。使得客户端和子系统之间解耦，让子系统内部的模块功能更容易扩展和维护；
<br>
简单易用。客户端根本不需要知道子系统内部的实现，或者根本不需要知道子系统内部的构成，它只需要跟Facade类交互即可。
<br>
更好的划分访问层次。有些方法是对系统外的，有些方法是系统内部相互交互的使用的。子系统把那些暴露给外部的功能集中到门面中，这样就可以实现客户端的使用，很好的隐藏了子系统内部的细节。
<br>

## 实例代码
外观角色类：
```
public class Computer {
	private Cpu cpu;
	private Disk disk;
	private Menory menory;
	public Computer() {
		cpu=new Cpu();
		disk=new Disk();
		menory=new Menory();
	}
	
	/**
	 * 
	 */
	public void start() {
		// TODO Auto-generated method stub
		System.out.println("computer start...");
		cpu.start();
		disk.start();
		menory.start();
	}
	/**
	 * 
	 */
	public void shutdown() {
		// TODO Auto-generated method stub
		System.out.println("computer shutdown...");
		cpu.shutdown();
		disk.shutdowm();
		menory.shutdowm();
	}
	
	
}
```
子系统角色类：
```
public class Cpu {
	
	/**
	 * 
	 */
	public void start() {
		// TODO Auto-generated method stub
		System.out.println("cpu start...");
	}
	
	/**
	 * 
	 */
	public void shutdown() {
		// TODO Auto-generated method stub
		System.out.println("cpu shutdown...");
	}

}
```
```
public class Disk {
	/**
	 * 
	 */
	public void start() {
		// TODO Auto-generated method stub
		System.out.println("disk start...");
	}
	/**
	 * 
	 */
	public void shutdowm() {
		// TODO Auto-generated method stub
		System.out.println("disk shutdown...");
	}
}
```
```
public class Menory {
	/**
	 * 
	 */
	public void start() {
		// TODO Auto-generated method stub
		System.out.println("menory start...");
	}
	/**
	 * 
	 */
	public void shutdowm() {
		// TODO Auto-generated method stub
		System.out.println("menory shutdown...");
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
		Computer c=new Computer();
		
		c.start();
		System.out.println();
		
		c.shutdown();
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/waiguan/001.jpg){:align="center"}<br><br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>




