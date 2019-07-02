---
layout: post
#标题配置
title: 代理模式及其实例应用
#时间配置
date:   2019-05-25 14:10:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

# proxy pattern
代理模式（Proxy Pattern）:一个类代表另一个类的功能。在代理模式中，我们创建具有现有对象的对象，以便向外界提供功能接口。

## 解决问题
在直接访问对象时带来的问题，比如说：要访问的对象在远程的机器上。在面向对象系统中，有些对象由于某些原因（比如对象创建开销很大，或者某些操作需要安全控制，或者需要进程外的访问），直接访问会给使用者或者系统结构带来很多麻烦，我们可以在访问此对象时加上一个对此对象的访问层。

## 优缺点
优点： 1、职责清晰。 2、高扩展性。 3、智能化。<br><br>
缺点： 1、由于在客户端和真实主题之间增加了代理对象，因此有些类型的代理模式可能会造成请求的处理速度变慢。 2、实现代理模式需要额外的工作，有些代理模式的实现非常复杂。

## 使用场景
按职责来划分，通常有以下使用场景： 1、远程代理。 2、虚拟代理。 3、Copy-on-Write 代理。 4、保护（Protect or Access）代理。 5、Cache代理。 6、防火墙（Firewall）代理。 7、同步化（Synchronization）代理。 8、智能引用（Smart Reference）代理。

## 注意事项
1、和适配器模式的区别：适配器模式主要改变所考虑对象的接口，而代理模式不能改变所代理类的接口。 2、和装饰器模式的区别：装饰器模式为了增强功能，而代理模式是为了加以控制。

## 实例代码
抽象接口：
```
public interface Image {
	public void display();
}
```
抽象实现类：
```
public class RealImage implements Image {
	private String filename;
	
	

	public RealImage(String filename) {
		this.filename = filename;
		load();
	}
	/* (non-Javadoc)
	 * @see it.mzt.proxy.Image#display()
	 */
	public void load(){
		System.out.println(filename+" loading...");
	}
	public void display() {
		// TODO Auto-generated method stub
		System.out.println(filename+" display...");
	}

}
```
代理类：
```
public class ImageProxy implements Image{
	private RealImage realImage;
	private String filename;
	
	public ImageProxy(String filename) {
		this.filename = filename;
	}

	/* (non-Javadoc)
	 * @see it.mzt.proxy.Image#display()
	 */
	public void display() {
		// TODO Auto-generated method stub
		if(realImage==null){
			realImage=new RealImage(filename);
		}
		realImage.display();
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
		 Image image = new ImageProxy("test_10mb.jpg");
		 
	      // 图像将从磁盘加载
	      image.display(); 
	      System.out.println("");
	      // 图像不需要从磁盘加载
	      image.display();  
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/daili/001.jpg){:align="center"}<br><br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>
