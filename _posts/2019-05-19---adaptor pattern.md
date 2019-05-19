---
layout: post
#标题配置
title:  适配者模式及其实例应用
#时间配置
date:   2019-05-19 14:35:00 +0800
#大类配置
categories: 设计模式

---

* content
{:toc}

---
---

## 适配者模式
将一个类的接口，转换成客户期望的另一个接口。适配器将原本接口不兼容的类也可以互相配合使用。想象手机充电器，将220直流电转化为5V的直流电，适配器就是充当这个充电器的角色。

### 实现方式
1.实现了客户的接口，因为这样可以转换为客户的接口。<br>
2.应该通过传入或者其他的办法将另一个接口或者类传入这个适配器，在这个适配器复写了接口的方法了做相应的处理，已便达到客户想要的需求。这样就可以将一个类或者接口转换成了客户的接口了。

### 实例代码
英国版本的充电器插口和国版充电器插口之间的适配。<br>

英版充电器类：
```
public class EnAdaptor {
	public void plugin(){
		System.out.println("charging.....");
	}
}
```
国版充电器接口：
```
public interface CnAdaptor {
	public void plugin();
}
```
适配器类：
```
public class EnToCnAdaptor implements CnAdaptor{
	private EnAdaptor ea=new EnAdaptor();
	/* (non-Javadoc)
	 * @see it.mzt.adaptor.CnAdaptor#plugin()
	 */
	public void plugin() {
		// TODO Auto-generated method stub
		ea.plugin();
		System.out.println("充电中....");
	}

}
```
中国家庭类：
```
public class Home {
	public void chazhuo(CnAdaptor ca){
		ca.plugin();
	}

}
```
测试类:
```
public class Test {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		CnAdaptor ca=new EnToCnAdaptor();
		Home home=new Home();
		home.chazhuo(ca);
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/shipeizhe/001.jpg){:align="center"}<br><br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>