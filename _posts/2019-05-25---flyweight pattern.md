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
1、系统有大量相似对象。 2、需要缓冲池的场景。

## 优缺点
优点：大大减少对象的创建，降低系统的内存，使效率提高。<br><br>
缺点：提高了系统的复杂度，需要分离出外部状态和内部状态，而且外部状态具有固有化的性质，不应该随着内部状态的变化而变化，否则会造成系统的混乱。

## 注意事项
1、注意划分外部状态和内部状态，否则可能会引起线程安全问题。 2、这些类必须有一个工厂对象加以控制。

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