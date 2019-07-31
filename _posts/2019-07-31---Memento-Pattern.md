---
layout: post
#标题配置
title: 备忘录模式及其实例应用
#时间配置
date:   2019-07-31 14:20:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

# Memento Pattern
在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样以后就可将该对象恢复到原先保存的状态；<br>

## 结构
Originator被保存对象；<br>
Memento备忘录对象：负责保存好记录，即Originator内部状态；<br>
Caretaker守护者对象，负责保存多个备忘录对象的多个状态，可以通过map+list实现，使用集合管理提高效率；<br>

## 注意事项
1、实现了信息的封装，使得用户不需要关系状态的保存细节；<br>
2、如果类的成员变量过多，势必会占用比较大的资源，而且每一次保存都会消耗一定的内存，这个需要注意；<br>
3、未来了节约内存，备忘录模式可以配合原型模式使用；<br>

## 实例代码
被保存对象：
```
public class GameRole {

	private int vit;
	private int def;
	
	//创建Memento ,即根据当前的状态得到Memento
	public Memento createMemento() {
		return new Memento(vit, def);
	}
	
	//从备忘录对象，恢复GameRole的状态
	public void recoverGameRoleFromMemento(Memento memento) {
		this.vit = memento.getVit();
		this.def = memento.getDef();
	}
	
	//显示当前游戏角色的状态
	public void display() {
		System.out.println("游戏角色当前的攻击力：" + this.vit + " 防御力: " + this.def);
	}

	public int getVit() {
		return vit;
	}

	public void setVit(int vit) {
		this.vit = vit;
	}

	public int getDef() {
		return def;
	}

	public void setDef(int def) {
		this.def = def;
	}
		
}
```
备忘录对象：
```
public class Memento {

	//攻击力
	private int vit;
	//防御力
	private int def;
	public Memento(int vit, int def) {
		super();
		this.vit = vit;
		this.def = def;
	}
	public int getVit() {
		return vit;
	}
	public void setVit(int vit) {
		this.vit = vit;
	}
	public int getDef() {
		return def;
	}
	public void setDef(int def) {
		this.def = def;
	}
	
}
```
守护者对象类：
```
//守护者对象, 保存游戏角色的状态
public class Caretaker {

	//如果只保存一次状态
	private Memento  memento;
	//对GameRole 保存多次状态
	//private ArrayList<Memento> mementos;
	//对多个游戏角色保存多个状态
	//private HashMap<String, ArrayList<Memento>> rolesMementos;

	public Memento getMemento() {
		return memento;
	}

	public void setMemento(Memento memento) {
		this.memento = memento;
	}
		
}
```
测试类：
```
public class Client {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		//创建游戏角色
		GameRole gameRole = new GameRole();
		gameRole.setVit(100);
		gameRole.setDef(100);
		
		System.out.println("和boss大战前的状态");
		gameRole.display();
		
		//把当前状态保存caretaker
		Caretaker caretaker = new Caretaker();
		caretaker.setMemento(gameRole.createMemento());
		
		System.out.println("和boss大战~~~");
		gameRole.setDef(30);
		gameRole.setVit(30);
		
		gameRole.display();
		
		System.out.println("大战后，使用备忘录对象恢复到站前");
		
		gameRole.recoverGameRoleFromMemento(caretaker.getMemento());
		System.out.println("恢复后的状态");
		gameRole.display();
	}

}
```
实现结果：<br>
![](https://itmanmzt.github.io/styles/images/beiwanglu/001.jpg){:align="center"}
<br>

<br>

<center>有Marin的地方就有你的收获</center>
