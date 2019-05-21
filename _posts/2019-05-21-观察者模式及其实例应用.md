---
layout: post
#标题配置
title:  观察者模式及其实例应用
#时间配置
date:   2019-05-21 20:13:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}

---
---

## 观察者模式
观察者模式（Observer Pattern）就是一种 “发布者-订阅者” 的模式。有时也被称为 “模型-视图”模式、“源-监听者”模式等。在这种模式中，由一个目标对象来管理所有依赖与它的观察者对象，并且当这个目标对象自身发生改变时，会主动向它的观察者们发出通知。<br>

该模式包含以下几种角色：<br>

抽象被观察者角色：也就是一个抽象主题，它把所有对观察者对象的引用保存在一个集合中，每个主题都可以有任意数量的观察者。抽象主题提供一个接口，可以增加和删除观察者角色。一般用一个抽象类和接口来实现。<br>
抽象观察者角色：为所有的具体观察者定义一个接口，在得到主题通知时更新自己。<br>
具体被观察者角色：也就是一个具体的主题，在集体主题的内部状态改变时，所有登记过的观察者发出通知。<br>
具体观察者角色：实现抽象观察者角色所需要的更新接口，一边使本身的状态与制图的状态相协调。<br>

### 实例代码
以微信订阅号的模式来进行实例演示。<br>
抽象被观察者接口：
```
public interface Observerable {
	public void registerObserver(Observer o);
	public void removeObserver(Observer o);
	public void notifyAllObserver();
	
}
```
抽象观察者接口：
```
public interface Observer {
	public void update(String message);
}
```
具体被观察类：
```
public class WeixinObserverable implements Observerable {
	private ArrayList<Observer> list;
	private String message;
	
	public WeixinObserverable() {
		list=new ArrayList<Observer>();
	}

	/* (non-Javadoc)
	 * @see it.mzt.observer.Observerable#registerObserver(it.mzt.observer.Observer)
	 */
	public void registerObserver(Observer o) {
		// TODO Auto-generated method stub
		list.add(o);
	}

	/* (non-Javadoc)
	 * @see it.mzt.observer.Observerable#removeObserver(it.mzt.observer.Observer)
	 */
	public void removeObserver(Observer o) {
		// TODO Auto-generated method stub
		if(!list.isEmpty())
			list.remove(o);
	}

	/* (non-Javadoc)
	 * @see it.mzt.observer.Observerable#notifyAllObserver()
	 */
	public void notifyAllObserver() {
		// TODO Auto-generated method stub
		for(int i=0;i<list.size();i++){
			Observer o=list.get(i);
			o.update(message);
		}
	}
	
	public void setMessage(String message){
		this.message=message;
		this.notifyAllObserver();
	}

}
```
具体观察类：
```
public class WeiXinUser implements Observer{
	private String name;
	private String message;

	public WeiXinUser(String name) {
		this.name = name;
	}


	/* (non-Javadoc)
	 * @see it.mzt.observer.Observer#update(java.lang.String)
	 */
	public void update(String message) {
		// TODO Auto-generated method stub
		this.message=message;
		this.read();
		
	}


	/**
	 * 
	 */
	private void read() {
		// TODO Auto-generated method stub
		System.out.println(this.name+"阅读了"+this.message);
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
		Observer o1=new WeiXinUser("001");
		Observer o2=new WeiXinUser("001");
		Observer o3=new WeiXinUser("001");
		
		WeixinObserverable oa=new WeixinObserverable();
		oa.registerObserver(o1);
		oa.registerObserver(o2);
		oa.registerObserver(o3);
		oa.setMessage("really l zyl");
		
		oa.removeObserver(o1);
		oa.setMessage("always l zyl");
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/observer/001.jpg){:align="center"}<br><br>

### 小结
这个模式是松偶合的。改变主题或观察者中的一方，另一方不会受到影像。<br>
JDK中也有自带的观察者模式。但是被观察者是一个类而不是接口，限制了它的复用能力。<br>
在JavaBean和Swing中也可以看到观察者模式的影子。<br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>