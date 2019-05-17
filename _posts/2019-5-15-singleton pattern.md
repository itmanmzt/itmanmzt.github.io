---
layout: post
#标题配置
title:  单例模式及其实例应用
#时间配置
date:   2019-05-15 21:41:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}

---
---

## 单例模式
　单例模式有以下特点：
　　1、单例类只能有一个实例。
　　2、单例类必须自己创建自己的唯一实例。
　　3、单例类必须给所有其他对象提供这一实例。
　　单例模式确保某个类只有一个实例，而且自行实例化并向整个系统提供这个实例。

### 饿汉单例模式
饿汉模式是在类中直接进行静态的初始化，类一加载便创建类的实例化对象。这种模式是线程安全的。
```
public class Ehan(){
	private Ehan(){}
    private static final Ehan ehan=new Ehan();
    public static Ehan getInstance(){
        return ehan;
    }
}
```

### 懒汉单例模式

懒汉模式是在类中进行静态初始化但赋了null值，但在类中创建对应的静态工厂方法进行对象的实例化，只有当有线程调用了这个类才进行对象的实例化，这种模式是线程不安全的。

```
public class LanHan(){
    private LanHan(){}
    private static LanHan lanHan=null;
    public static LanHan getInstance(){
    	if(lanHan==null){
        lanHan=new LanHan();
        }
        return lanHan;
    }
}
```

有三种常见的方法来实现懒汉模式的线程安全。
1、直接在静态工厂方法上加上synchronized同步方法，但这种方法调用都需要进行同步，严重影响了性能。

```
public class LanHan(){
    private LanHan(){}
    private static LanHan lanHan=null;
    public static synchronized LanHan getInstance(){
        if(lanHan==null){
            lanHan=new LanHan();
        }
        return lanHan;
    }
}
```

2、双重检查锁定。通过双重的if判断保障只有一个线程进行初始化操作，避免了每次都进行同步，大大提升了性能，但双重if在一定程度上也会影响性能。

```
public class LanHan(){
    private LanHan(){}
    private static LanHan lanHan=null;
    public static LanHan getInstance(){
        if(lanHan==null){
            syschronized(LanHan.class){
                if(lanHan==null){
                lanHan=new LanHan();
                }
       		 }
        }
        return lanHan;
    }
}
```

3、通过定义静态内部类并在类中进行静态初始化，利用classloader的机制只有一个线程能进入到初始化的程序，实现了线程安全，是三种方法里面性能最高的方法。

```
public class LanHan(){
    private static class Instance(){
        private static final LanHan lanHan=new LanHan();
    }
    public static LanHan getInstance(){
        return Instance.lanHan;
    }
}
```

### 实例测试

```
public class DanLi {
	private String name;
	private DanLi(){}
	private static DanLi danLi=null;
	
	public static DanLi getInstance(){
		if(danLi==null){
			synchronized(DanLi.class){
				if(danLi==null){
				danLi=new DanLi();
				}
			}
		}
		return danLi;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
	
	public void printResult(){
		System.out.println(name+"输出");
	}

}
```

测试用例：
```
public class Test {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		DanLi dl1=DanLi.getInstance();
		dl1.setName("001");
		
		DanLi dl2=DanLi.getInstance();
		dl2.setName("007");
		dl1.printResult();
		dl2.printResult();
		
		if(dl1==dl2){
			System.out.println("true");
			
		}else{
			System.out.println("false");
		}
	}

}
```
### 测试懒汉模式的线程不安全性
```
public class DanLi{
	private long name;
	private DanLi(long name){
		this.name =name;
	}
	private static DanLi danLi=null;
	
	public static DanLi getInstance(long l){
				if(danLi==null){
				danLi=new DanLi(l);
				}
		return danLi;
	}

	public long getName() {
		return name;
	}

	public void setName(long name) {
		this.name = name;
	}
	
	public void printResult(){
		System.out.println(name+"输出");
	}

}
```
线程类：
```
public class DanLiThread implements Runnable{

	public void run() {
		// TODO Auto-generated method stub
		DanLi danLi=DanLi.getInstance(System.currentTimeMillis());
		System.out.println(danLi.getName());
	}

}
```
测试类：
```
public class Test1 {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		DanLiThread dlt=new DanLiThread();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		new Thread(dlt).start();
		
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/danli/001.jpg){:align="center"}<br><br>

### 模拟打印机工作原理
原理：当有打印工作进来，就把单例对象锁上，等打印工作结束之后再释放掉锁让其他线程获取单例对象进行打印，我们后面将对比多例模式。<br>
<br>
<b>单例模式</b><br>
实体类：
```
public class DanLi{
	private String name;
	private DanLi(){
		
	}
	private static DanLi danLi=null;
	
	public static DanLi getInstance(){
		if(danLi==null){
			synchronized(DanLi.class){
				if(danLi==null){
				danLi=new DanLi();
				}
			}
		}
		return danLi;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
	
	public void printResult(){
		System.out.println(name+"输出");
	}

}
```

线程类：
```
public class DanLiThread implements Runnable{

	
	
	public void run() {
		// TODO Auto-generated method stub
		DanLi danLi=DanLi.getInstance();
		try {
			synchronized(danLi){
				Thread.sleep(5000);
			}
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		danLi.setName("001");
		System.out.println(danLi.getName());
		System.out.println(new Date());
	}

}

```
测试类：
```
public class Test1 {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		DanLiThread dlt=new DanLiThread();
		
		new Thread(dlt).start();
		new Thread(dlt).start();
		
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/danli/002.jpg){:align="center"}<br><br>

<b>多例模式</b><br>
实体类：
```
public class DanLi{
	private String name;
	private DanLi(){
		
	}
	private static DanLi danLi=null;
	
	public static DanLi getInstance(){
		
		danLi=new DanLi();
		return danLi;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
	
	public void printResult(){
		System.out.println(name+"输出");
	}

}
```

线程类：
```
public class DanLiThread implements Runnable{

	
	
	public void run() {
		// TODO Auto-generated method stub
		DanLi danLi=DanLi.getInstance();
		try {
			synchronized(danLi){
				Thread.sleep(5000);
			}
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		danLi.setName("001");
		System.out.println(danLi.getName());
		System.out.println(new Date());
	}

}

```
测试类：
```
public class Test1 {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		DanLiThread dlt=new DanLiThread();
		
		new Thread(dlt).start();
		new Thread(dlt).start();
		
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/danli/003.jpg){:align="center"}<br><br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>