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
代理模式（Proxy Pattern）:一个类代表另一个类的功能。在代理模式中，我们创建具有现有对象的对象，以便向外界提供功能接口。<br>
被代理对象可以是远程对象、创建开销大的对象和需要安全控制的对象；<br>
代理模式有三种形式：静态代理、动态代理（JDK代理和Cglib代理（可以在内存动态的创建对象，而不需要实现接口））<br>
静态代理：需要定义接口或父类，被代理对象与代理对象一起实现相同的接口或者继承相同父类；调用时通过调用代理对象的方法来调用目标对象（实现相同接口后通过调用相同的方法实现）<br>
静态代理的确定是一旦接口增加方法，目标对象和代理对象都要维护；<br><br>
JDK动态代理：代理对象不需要实现接口，但是目标对象（被代理对象）需要实现接口；代理对象的声称是利用JDK中的java.lang.reflect.proxy包里的newproxyinstance方法，动态的在内存中构建代理对象；<br><br>
Cglib动态代理：都不需要实现接口，它是在内存中构建一个子类对象从而实现对目标对象功能扩展；Cglib是一个强大的高性能的代码生成包，它可以在运行期间扩展java类与实现java接口，其底层是通过使用字节码处理框架ASM来转换字节码并生成新的类；<br><br>


## 解决问题
在直接访问对象时带来的问题，比如说：要访问的对象在远程的机器上。在面向对象系统中，有些对象由于某些原因（比如对象创建开销很大，或者某些操作需要安全控制，或者需要进程外的访问），直接访问会给使用者或者系统结构带来很多麻烦，我们可以在访问此对象时加上一个对此对象的访问层。

## 优缺点
优点： 1、职责清晰。 2、高扩展性。 3、智能化。<br><br>
缺点： 1、由于在客户端和真实主题之间增加了代理对象，因此有些类型的代理模式可能会造成请求的处理速度变慢。 2、实现代理模式需要额外的工作，有些代理模式的实现非常复杂。

## 使用场景
按职责来划分，通常有以下使用场景： 1、远程代理。 2、虚拟代理。 3、Copy-on-Write 代理。 4、保护（Protect or Access）代理。 5、Cache代理。 6、防火墙（Firewall）代理。 7、同步化（Synchronization）代理。 8、智能引用（Smart Reference）代理。

## 注意事项
1、和适配器模式的区别：适配器模式主要改变所考虑对象的接口，而代理模式不能改变所代理类的接口。 2、和装饰器模式的区别：装饰器模式为了增强功能，而代理模式是为了加以控制。

## 静态代理实例代码
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
## JDK代理实例代码
抽象接口：
```
public interface ITeacherDao {

	void teach(); // 授课方法
	void sayHello(String name);
}

```
被代理类：
```
public class TeacherDao implements ITeacherDao {

	@Override
	public void teach() {
		// TODO Auto-generated method stub
		System.out.println(" 老师授课中.... ");
	}

	@Override
	public void sayHello(String name) {
		// TODO Auto-generated method stub
		System.out.println("hello " + name);
	}
	
}
```
代理工厂类：
```
public class ProxyFactory {

	//维护一个目标对象 , Object
	private Object target;

	//构造器 ， 对target 进行初始化
	public ProxyFactory(Object target) {
		
		this.target = target;
	} 
	
	//给目标对象 生成一个代理对象
	public Object getProxyInstance() {
		
		//说明
		/*
		 *  public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h)
                                          
            //1. ClassLoader loader ： 指定当前目标对象使用的类加载器, 获取加载器的方法固定
            //2. Class<?>[] interfaces: 目标对象实现的接口类型，使用泛型方法确认类型
            //3. InvocationHandler h : 事情处理，执行目标对象的方法时，会触发事情处理器方法, 会把当前执行的目标对象方法作为参数传入
		 */
		return Proxy.newProxyInstance(target.getClass().getClassLoader(), 
				target.getClass().getInterfaces(), 
				new InvocationHandler() {
					
					@Override
					public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
						// TODO Auto-generated method stub
						System.out.println("JDK代理开始~~");
						//反射机制调用目标对象的方法
						Object returnVal = method.invoke(target, args);
						System.out.println("JDK代理提交");
						return returnVal;
					}
				}); 
	}
	
	
}
```
实现类：
```
public class Client {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		//创建目标对象
		ITeacherDao target = new TeacherDao();
		
		//给目标对象，创建代理对象, 可以转成 ITeacherDao
		ITeacherDao proxyInstance = (ITeacherDao)new ProxyFactory(target).getProxyInstance();
	
		// proxyInstance=class com.sun.proxy.$Proxy0 内存中动态生成了代理对象
		System.out.println("proxyInstance=" + proxyInstance.getClass());
		
		//通过代理对象，调用目标对象的方法
		//proxyInstance.teach();
		
		proxyInstance.sayHello(" tom ");
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/daili/002.jpg){:align="center"}<br><br>
## Cglib代理实例代码
需要先导入Cglib所需的相关jar包<br>
被代理类：
```
public class TeacherDao {

	public String teach() {
		System.out.println(" 老师授课中  ， 我是cglib代理，不需要实现接口 ");
		return "hello";
	}
}
```
代理工厂类：
```
public class ProxyFactory implements MethodInterceptor {

	//维护一个目标对象
	private Object target;
	
	//构造器，传入一个被代理的对象
	public ProxyFactory(Object target) {
		this.target = target;
	}

	//返回一个代理对象:  是 target 对象的代理对象
	public Object getProxyInstance() {
		//1. 创建一个工具类
		Enhancer enhancer = new Enhancer();
		//2. 设置父类
		enhancer.setSuperclass(target.getClass());
		//3. 设置回调函数
		enhancer.setCallback(this);
		//4. 创建子类对象，即代理对象
		return enhancer.create();
		
	}
	

	//重写  intercept 方法，会调用目标对象的方法
	@Override
	public Object intercept(Object arg0, Method method, Object[] args, MethodProxy arg3) throws Throwable {
		// TODO Auto-generated method stub
		System.out.println("Cglib代理模式 ~~ 开始");
		Object returnVal = method.invoke(target, args);
		System.out.println("Cglib代理模式 ~~ 提交");
		return returnVal;
	}

}
```
实现类：
```
public class Client {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		//创建目标对象
		TeacherDao target = new TeacherDao();
		//获取到代理对象，并且将目标对象传递给代理对象
		TeacherDao proxyInstance = (TeacherDao)new ProxyFactory(target).getProxyInstance();

		//执行代理对象的方法，触发intecept 方法，从而实现 对目标对象的调用
		String res = proxyInstance.teach();
		System.out.println("res=" + res);
	}

}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/daili/003.jpg){:align="center"}<br><br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>
