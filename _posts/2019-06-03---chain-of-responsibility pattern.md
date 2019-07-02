---
layout: post
#标题配置
title: 责任链模式及其实例应用
#时间配置
date:   2019-06-03 18:50:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

# chain of responsibility pattern
责任链模式：将能够处理同一类请求的对象连成一条链，使这些对象都有机会处理请求，所提交的请求沿着链传递。从而避免请求的发送者和接受者之间的耦合关系。链上的对象逐个判断是否有能力处理该请求，如果能则就处理，如果不能，则传给链上的下一个对象。直到有一个对象处理它为止。

## 实现方式

1、链表方式：比如刚才的请假审批
<br>
2、非链表方式：通过集合，数组生成责任链更加实用，将链表上的各个对象都添加到集合中，然后通过反射给构建出来。然后在容器里一个个的处理。（也就是说把测试代码中除了请假的其他代码都给用一个类来处理）
<br>
## 开发中常见场景

1、Java的异常机制就是一个责任链模式，一个try可以对应多个cathc。如果某一个catch不匹配，则跳到下一个catch中
<br>
2、JavaScript语言中的事件的冒泡和捕获机制
<br>
3、Servlet开发中，过滤器的链式处理
<br>
4、Struts2中，拦截器的调用也是典型的责任链模式
<br>
## 责任链的好处

1、接受者和发送者都没有对方的明确信息，且链中的对象也并不知道链的结构，结果是责任链可简化对象的相互连接，它们仅需保持一个指向其后继者的引用，而不需要保持它所有的候选继承者，大大的降低了耦合度。请求者不用管具体哪个对象会处理，反正该请求肯定会被处理就行了
<br>
2、可以随时增加或者修改处理一个请求的结构，增加了给对象指派职责的灵活性
<br>

## 实例代码
下面将以请假流程为例，进行责任链模式的实例应用
<br>
请假信息类：
```
public class Leave {
	private String name;
	private int days;
	private String reason;
	public Leave(String name,int days,String reason) {
		this.name = name;
		this.days=days;
		this.reason=reason;
	}
	public int getDays() {
		return days;
	}
	public void setDays(int days) {
		this.days = days;
	}
	public String getReason() {
		return reason;
	}
	public void setReason(String reason) {
		this.reason = reason;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	
	
}

```
请假处理人抽象类：
```
public abstract class Leader {
	protected String name;
	protected Leader next;
	public Leader(String name) {
		super();
		this.name = name;
	}

	public void setNext(Leader next) {
		this.next = next;
	}
	public abstract void handleRequest(Leave leave);
	
}
```
请假处理人实现类：
```
public class Instructor extends Leader {
	public Instructor(String name){
		super(name);
	}
	/* (non-Javadoc)
	 * @see it.mzt.respChain.Leader#handleRequest(it.mzt.respChain.Leave)
	 */
	@Override
	public void handleRequest(Leave leave) {
		// TODO Auto-generated method stub
		  System.out.println("请假人："+leave.getName()+",天数："+leave.getDays()+",理由："+leave.getReason());
		 if (leave.getDays()<=3) {
	          
	            System.out.println("审批人："+this.name+" 辅导员，审批通过！");
	        }else{
	        	System.out.println("审批人："+this.name+" 辅导员，达不到审批权限！");
	            if (this.next != null ) {//如果有下一个继承者
	                //让下一个继承者处理请求
	                this.next.handleRequest(leave);
	            }
	        }
	}

	
}
```
```
public class Director extends Leader {
	public Director(String name){
		super(name);
	}
	/* (non-Javadoc)
	 * @see it.mzt.respChain.Leader#handleRequest(it.mzt.respChain.Leave)
	 */
	@Override
	public void handleRequest(Leave leave) {
		// TODO Auto-generated method stub
		  System.out.println("请假人："+leave.getName()+",天数："+leave.getDays()+",理由："+leave.getReason());
		 if (leave.getDays()<=7) {
	          
	            System.out.println("审批人："+this.name+" 主任，审批通过！");
	        }else{
	        	System.out.println("审批人："+this.name+" 主任，达不到审批权限！");
	            if (this.next != null ) {//如果有下一个继承者
	                //让下一个继承者处理请求
	                this.next.handleRequest(leave);
	            }
	        }
	}

}
```
```
public class Headmaster extends Leader {
	public Headmaster(String name){
		super(name);
	}
	/* (non-Javadoc)
	 * @see it.mzt.respChain.Leader#handleRequest(it.mzt.respChain.Leave)
	 */
	@Override
	public void handleRequest(Leave leave) {
		// TODO Auto-generated method stub
		System.out.println("请假人："+leave.getName()+",天数："+leave.getDays()+",理由："+leave.getReason());
		 if (leave.getDays()<=30) {
	            
	            System.out.println("审批人："+this.name+" 校长，审批通过！");
	        }else{
	        	System.out.println("审批人："+this.name+" 校长，不符合请假条件！");
	            if (this.next != null ) {//如果有下一个继承者
	                //让下一个继承者处理请求
	                this.next.handleRequest(leave);
	            }
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
		Leader l1=new Instructor("武老师");
		Leader l2=new Director("林主任");
		Leader l3=new Headmaster("李校长");
		l1.setNext(l2);
		l2.setNext(l3);
		Leave leave=new Leave("马同学",11,"旅游");
		l1.handleRequest(leave);
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/zerenlian/001.jpg){:align="center"}<br><br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>