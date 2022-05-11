
<!-- TOC -->

- [1. 依赖倒置](#1-%E4%BE%9D%E8%B5%96%E5%80%92%E7%BD%AE)
    - [1.1. 控制反转](#11-%E6%8E%A7%E5%88%B6%E5%8F%8D%E8%BD%AC)
    - [1.2. 依赖注入](#12-%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5)
    - [1.3. 依赖注入框架](#13-%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5%E6%A1%86%E6%9E%B6)
    - [1.4. 依赖反转原则](#14-%E4%BE%9D%E8%B5%96%E5%8F%8D%E8%BD%AC%E5%8E%9F%E5%88%99)

<!-- /TOC -->

# 1. 依赖倒置
<a id="markdown-%E4%BE%9D%E8%B5%96%E5%80%92%E7%BD%AE" name="%E4%BE%9D%E8%B5%96%E5%80%92%E7%BD%AE"></a>



了解依赖倒置原则之前，先说一下，控制反转（`inversion of control` 简称 `IOC`)、依赖注入（`dependency injection` 简称 `DI`)、依赖注入框架



## 1.1. 控制反转
<a id="markdown-%E6%8E%A7%E5%88%B6%E5%8F%8D%E8%BD%AC" name="%E6%8E%A7%E5%88%B6%E5%8F%8D%E8%BD%AC"></a>

理解控制反转，要理解”控制“是什么？”反转“是什么？

- 控制是指对程序流程的控制
- 反转，指将这种对程序流程的控制权交给了程序



- 实现控制反转的手段
  - 依赖注入
  - 模板模式

- 指导意义
  - 控制反转并不是一种具体的实现技巧，而是一个比较笼统的设计思想，一般用来指导框架层面的设计。
- 我的理解
  - 这条规则在业务代码中，好像没什么作用
- 重要程度
  - :star:



## 1.2. 依赖注入
<a id="markdown-%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5" name="%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5"></a>



- 什么是依赖注入
  - 依赖的对象，不是通过 `new` 在内部生成，而是通过构造参数、方法将已经创建好的对象传递给类使用



**例子**

Notification 类负责消息推送，依赖MessageSender 类实现推送商品促销、验证码等消息给用户。分别用依赖注入和非依
赖注入两种方式来实现一下

```java
// 非依赖注入实现方式
public class Notification {
	private MessageSender messageSender;
	public Notification() {
		this.messageSender = new MessageSender(); // 此处有点像 hardcode
	}
    
	public void sendMessage(String cellphone, String message) {
		//... 省略校验逻辑等...
		this.messageSender.send(cellphone, message);
	}
}

public class MessageSender {
	public void send(String cellphone, String message) {
   		//....
	}
}
// 使用 Notification
Notification notification = new Notification();

// 依赖注入的实现方式
public class Notification {
	private MessageSender messageSender;
	// 通过构造函数将 messageSender 传递进来
	public Notification(MessageSender messageSender) {
		this.messageSender = messageSender;
	}
	public void sendMessage(String cellphone, String message) {
		//... 省略校验逻辑等...
		this.messageSender.send(cellphone, message);
	}
}
// 使用 Notification
MessageSender messageSender = new MessageSender();
Notification notification = new Notification(messageSender);
```



## 1.3. 依赖注入框架
<a id="markdown-%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5%E6%A1%86%E6%9E%B6" name="%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5%E6%A1%86%E6%9E%B6"></a>



就像争哥说的，一个大的业务系统，每个类可能会依赖更多的其它的对象，如果这些依赖注入的代码交给程序员自己控制，会极大的增加负担



如果有了依赖注入框架，这些繁重的依赖注入逻辑完全交给框架就好了。我想，这就是依赖注入框架的一个非常好的功能





## 1.4. 依赖反转原则
<a id="markdown-%E4%BE%9D%E8%B5%96%E5%8F%8D%E8%BD%AC%E5%8E%9F%E5%88%99" name="%E4%BE%9D%E8%B5%96%E5%8F%8D%E8%BD%AC%E5%8E%9F%E5%88%99"></a>

 依赖反转的英文翻译是 Dependency Inversion Principle，缩写为 DIP。依赖反转的英文描述

>High-level modules shouldn’t depend on low-level modules. Both modules
>should depend on abstractions. In addition, abstractions shouldn’t depend on
>details. Details depend on abstractions.



- 指导意义
  - 这条原则主要还是用来指导框架层面的设计
- 对我写代码的启示
  - 如何写好依赖代码
  - 实现类依赖抽象的接口或抽象类（这条规则和面向接口而非实现编程的原则一致，个人感觉，依赖倒置原则说的更具体，要依赖抽象，指导怎么写好依赖相关的代码，顺着这个思路想起了里氏替换）
- 重要程度
  - :star:

