---
layout: post
#标题配置
title: 享元模式及其实例应用
#时间配置
date:   2019-05-25 13:07:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

# flyweight pattern
享元模式（Flyweight Pattern）主要用于减少创建对象的数量，以减少内存占用和提高性能。这种类型的设计模式属于结构型模式，它提供了减少对象数量从而改善应用所需的对象结构的方式。

## 使用要求
1、系统中有大量对象。 2、这些对象消耗大量内存。 3、这些对象的状态大部分可以外部化。 4、这些对象可以按照内蕴状态分为很多组，当把外蕴对象从对象中剔除出来时，每一组对象都可以用一个对象来代替。 5、系统不依赖于这些对象身份，这些对象是不可分辨的。

## 使用场景
1、系统有大量相似对象。 2、需要缓冲池的场景。3、系统中有大量对象，这些对象消耗大量内存，并且对象的状态大部分可以外部化。

## 优缺点
优点：大大减少对象的创建，降低系统的内存，使效率提高。<br><br>
缺点：提高了系统的复杂度，需要分离出外部状态和内部状态，而且外部状态具有固有化的性质，不应该随着内部状态的变化而变化，否则会造成系统的混乱。

## 注意事项
1、注意划分外部状态和内部状态，否则可能会引起线程安全问题。 <br>
2、这些类必须有一个工厂对象加以控制。<br>
3、对享元模式的理解，享指的是共享，元指的是对象；<br>
4、用唯一标识码判断，如果内存中有，则返回这个唯一标识码对应的对象，用HashMap存储；<br>
5、享元模式提高了系统的复杂度，需要分离出内部状态和外部状态，内部状态具有固化特性，不应该随着外部状态的改变而改变；

## 角色定义
抽象的享元角色(FlyWeight)：产品的抽象类，同时定义出对象的外部状态和内部状态的接口和实现；<br>
具体的享元角色(ConcreteFlyWeight)：具体的产品类，实现抽象角色定义的相关业务；<br>
不可共享的角色(UnSharedConcreteFlyWeight)：不可共享的具体产品类，一般不会出现在享元工厂中；<br>
享元工厂类(FlyWeightFactory)：用于构建一个池容器(集合)，同时提供从池中获取对象的方法；<br><br>
享元模式提出了两个要求：细粒度和共享对象；这里涉及到内部状态和外部状态，即将对象的信息分为两个部分：内部状态和外部状态；<br>
内部状态指对象共享出来的信息，存储在享元对象内部是不会随环境的变化而改变的；<br>
外部状态指对象得以依赖的一个标记，是随环境改变而改变的，是不可共享的状态；<br><br>
举个例子：围棋理论上有361个空位可以放棋子，每盘棋都可以有两三百分棋子对象产生，因为内存空间有限，一台服务器很难支撑更多的玩家玩围棋游戏，如果用享元模式来处理棋子，那么棋子对象可以减少到只有两个实例（黑白棋子，属于我们的内部状态），而不同对局里面下棋的位置不同属于我们的外部状态，是不能共享的，这样子做就很好的解决了对象的开销问题；

## 实例代码
抽象接口：
```
public interface Brush {
	public void draw();
}
```
实现类：
```
public class Board implements Brush {
	private String color;
	private int x;
	private int y;
	private int radius;
	
	public Board(String color) {
		this.color = color;
	}
	

	public void setX(int x) {
		this.x = x;
	}


	public void setY(int y) {
		this.y = y;
	}


	public void setRadius(int radius) {
		this.radius = radius;
	}


	/* (non-Javadoc)
	 * @see it.mzt.flyweight.Brush#draw()
	 */
	public void draw() {
		// TODO Auto-generated method stub
		System.out.println("Board: Draw() [Color : " + color 
		         +", x : " + x +", y :" + y +", radius :" + radius);
	}


}
```
工厂类：
```
public class ColorFactory {
	private static final HashMap<String,Brush> map=new HashMap<String,Brush>();
	
	public static Brush getBrush(String color){
		Board board=(Board)map.get(color);
		if(board==null){
			board=new Board(color);
			map.put(color, board);
			 System.out.println("Creating Board of color : " + color);
		}
		return board;
	}
}
```
测试类：
```
public class Test {
	private static final String colors[]={"red","grean","blue","yellow","white"};
	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		for(int i=0;i<20;i++){
			Board bb=(Board) ColorFactory.getBrush(getRandomColor());
			bb.setX(getX());
			bb.setY(getY());
			bb.setRadius(100);
			bb.draw();
		}
	}
	/**
	 * @return
	 */
	private static int getY() {
		// TODO Auto-generated method stub
		return (int)(Math.random()*100);
	}
	/**
	 * @return
	 */
	private static int getX() {
		// TODO Auto-generated method stub
		return (int)(Math.random()*100);
	}
	/**
	 * @return
	 */
	private static String getRandomColor() {
		// TODO Auto-generated method stub
		return colors[(int)(Math.random()*colors.length)];
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/xiangyuan/001.jpg){:align="center"}<br><br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>