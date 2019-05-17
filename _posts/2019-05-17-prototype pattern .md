---
layout: post
#标题配置
title:  原型模式及其实例应用
#时间配置
date:   2019-05-17 20:12:00 +0800
#大类配置
categories: 设计模式

---

* content
{:toc}

---
---

## 原型模式
原型模式在java中的应用的核心就是利用java中cloneable接口的clone方法，实现对原型对象的拷贝来创建新的对象。
### 条件
原型模式主要用于对象的复制，它的核心是就是类图中的原型类Prototype。Prototype类需要具备以下两个条件：

1、实现Cloneable接口。在java语言有一个Cloneable接口，它的作用只有一个，就是在运行时通知虚拟机可以安全地在实现了此接口的类上使用clone方法。在java虚拟机中，只有实现了这个接口的类才可以被拷贝，否则在运行时会抛出CloneNotSupportedException异常。

2、重写Object类中的clone方法。Java中，所有类的父类都是Object类，Object类中有一个clone方法，作用是返回对象的一个拷贝，但是其作用域protected类型的，一般的类无法调用，因此，Prototype类需要将clone方法的作用域修改为public类型。
### 注意事项
1、在实际应用中，原型模式很少单独出现。经常与其他模式混用，他的原型类Prototype也常用抽象类来替代。<br>
2、使用原型模式复制对象不会调用类的构造方法。因为对象的复制是通过调用Object类的clone方法来完成的，它直接在内存中复制数据，因此不会调用到类的构造方法。不但构造方法中的代码不会执行，甚至连访问权限都对原型模式无效。
### 优缺点
优点

      1、如果创建新的对象比较复杂时，可以利用原型模式简化对象的创建过程，同时也能够提高效率。
    
      2、可以使用深克隆保持对象的状态。
    
      3、原型模式提供了简化的创建结构。

缺点 

      1、在实现深克隆的时候可能需要比较复杂的代码。
    
      2、需要为每一个类配备一个克隆方法，而且这个克隆方法需要对类的功能进行通盘考虑，这对全新的类来说不是很难，但对已有的类进行改造时，不一定是件容易的事，必须修改其源代码，违背了“开闭原则”。

### 实现代码
原型类：
```
public class YuanXing implements Cloneable {
	/* (non-Javadoc)
	 * @see java.lang.Object#clone()
	 */
	@Override
	protected Object clone() throws CloneNotSupportedException {
		YuanXing yuanXing=null;
		try{
		yuanXing=(YuanXing) super.clone();
		}catch(CloneNotSupportedException e){
			e.printStackTrace();
		}
		// TODO Auto-generated method stub
		return yuanXing;
	}
}
```
原型实现类：
```
public class YuanXingPro extends YuanXing{
	public void show(){
		System.out.println("原型实现类！");
	}
}
```
测试类：
```
public class test {

	public static void main(String[] args) throws CloneNotSupportedException {
		// TODO Auto-generated method stub
		YuanXingPro yxp=new YuanXingPro();
		for(int i=0;i<5;i++){
			YuanXingPro yy=(YuanXingPro) yxp.clone();
			yy.show();
		}
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/yuanxing/001.jpg){:align="center"}<br><br>

<b>测试没有实现cloneable的情况：</b>
```
public class YuanXing {
	/* (non-Javadoc)
	 * @see java.lang.Object#clone()
	 */
	@Override
	protected Object clone() throws CloneNotSupportedException {
		YuanXing yuanXing=null;
		try{
		yuanXing=(YuanXing) super.clone();
		}catch(CloneNotSupportedException e){
			e.printStackTrace();
		}
		// TODO Auto-generated method stub
		return yuanXing;
	}
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/yuanxing/002.jpg){:align="center"}<br><br>

### 深拷贝和浅拷贝
深拷贝与浅拷贝。Object类的clone方法只会拷贝对象中的基本的数据类型，对于数组、容器对象、引用对象等都不会拷贝，这就是浅拷贝。如果要实现深拷贝，必须将原型模式中的数组、容器对象、引用对象等另行拷贝。<br>
浅拷贝实例代码：<br>
原型类：
```
public class YuanXing implements Cloneable {
	/* (non-Javadoc)
	 * @see java.lang.Object#clone()
	 */
	@Override
	protected Object clone() throws CloneNotSupportedException {
		YuanXing yuanXing=null;
		try{
		yuanXing=(YuanXing) super.clone();
		}catch(CloneNotSupportedException e){
			e.printStackTrace();
		}
		// TODO Auto-generated method stub
		return yuanXing;
	}
}
```
实现类：
```
public class YuanXingPro extends YuanXing{
	private String name;
	private Integer id;
	private ArrayList list=new ArrayList();
	
	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public ArrayList getList() {
		return list;
	}

	public void setList(ArrayList list) {
		this.list = list;
	}

	public void show(){
		System.out.println("原型实现类！"+":"+name+":"+id+":"+list);
		
	}
}
```
测试类：
```
public class test {

	public static void main(String[] args) throws CloneNotSupportedException {
		// TODO Auto-generated method stub
		YuanXingPro yxp=new YuanXingPro();
		yxp.setId(1);
		yxp.setName("marin");
		ArrayList list=new ArrayList();
		list.add("001");
		yxp.setList(list);
		YuanXingPro yy=(YuanXingPro) yxp.clone();
		yy.show();
		list.add("002");
		yxp.setList(list);
		yxp.setId(2);
		yy.show();
		
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/yuanxing/003.jpg){:align="center"}<br><br>
可以看出，浅拷贝后，原型对象中的引用类型值改变，拷贝的对象引用类型的值也跟着变化，实际上浅拷贝的对象内部引用类型对象和原型对象内部的引用类型对象指向同一引用。clone这种对象的时候需要注意，如果希望拷贝的对象不受原型对象的影响，浅拷贝就不适用了。

深拷贝实例代码：<br>
原型类：
```
public class YuanXing implements Cloneable {
	private String name;
	private Integer id;
	private ArrayList list=new ArrayList();
	
	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public ArrayList getList() {
		return list;
	}

	public void setList(ArrayList list) {
		this.list = list;
	}

	/* (non-Javadoc)
	 * @see java.lang.Object#clone()
	 */
	@Override
	protected Object clone() throws CloneNotSupportedException {
		YuanXing yuanXing=null;
		try{
		yuanXing=(YuanXing) super.clone();
		yuanXing.list=(ArrayList)this.list.clone();
		}catch(CloneNotSupportedException e){
			e.printStackTrace();
		}
		// TODO Auto-generated method stub
		return yuanXing;
	}
	public void show(){
		System.out.println("原型实现类！"+":"+name+":"+id+":"+list);
		
	}
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/yuanxing/004.jpg){:align="center"}<br><br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>
