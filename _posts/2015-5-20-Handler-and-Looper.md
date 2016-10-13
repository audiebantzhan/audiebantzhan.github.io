---
layout: post
title: 'Handler 和 Looper 原理分析'
date: '2015-5-20'
description:
image: http://area.sinaapp.com/bingImg
image-sm: http://area.sinaapp.com/bingImg
categories:
  - Android
---
#### 前言
> 就应用程序而言，Android系统中的`Java`应用程序和系统程序相同，都是以消息驱动来工作的，大致原理如下：
> 1. 有一个消息队列，可以在消息队列中投递消息。
> 2. 有一个消息循环，可以不断从消息队列中取出消息，并做处理。
![][1]
> 把待处理的消息加处消息队列中，一直加到队列尾，一些优先级高的也可以加到队列头，提交的消息可以是按键，触屏等物理产生的消息，也可以是系统或者是应用本身发出的请求消息。
> 处理线程不断的从消息队列中取出消息并处理。请求消息可以把优先级高的放到队列头，这样就可以优先处理。

*  Looper类用于封装消息循环，有一个消息队列。
* Handler类封闭的消息投递，消息处理等接口。

### Looper 分析

``` java
//定义一个LooperThread
class LooperThread extends Thread{
	public Handler mHandler;
	public void run(){
		//调用prepare
		Looper.prepare();
		...
		//进入消息循环
		Looper.loop();
	}

}

//应用程序使用LooperThread
{
....
new LooperThread().start();//启动线程
}
```
#### 第一个调用函数是`Looper`的`prepare()`，它具体做了哪些工作：
`Looper.java`

``` java
public static final void prepare(){
	//一个Looper只调用一次prepare
	if(sThreadLocal.get()!=null){
		throw new RuntimeException("Only one looper maybe create per thread");
	}
	//构造一个Looper对象，并设置到调用线程的局部变量中。
	sThreadLocal.set(new Looper());
}
//sThreadLocal的定义
public static final ThreadLocal sThreadLocal=new ThreadLocal();
```
> ThreadLocal是`Java`中线程局部变量类，该类和两个关键函数：
> `set` 设置调用线程的局部变量
> `get` 获取调用线程的局部变量

根据上面分析可知，perpare会在调用线程的局部变量中设置一个`Looper`对象，这个调用线程就是`LooperThread`的`Run`线程,`Looper`对象的构造如下所示：

``` java
private Looper(){
	//构造一个消息队列
	mQueue=new MessageQueue();
	mRun=true;
	mThread=Thread.currentThread();
	//得到当前线程的Thread对象
}
```

###### `prepare`主要做了一件事：
> 在调用`prepare`的线程中设置了一个`Looper`对象，这个`Looper`对象就保存在调用线程的ThreadLocal中，而且`Looper`对象中封装了一个消息队列。
`prepare`通过`ThreadLocal`机制,巧妙的把`Looper`和调用线程关联到一起了。想要了解这么做的目的，就需要了解第二个函数

#### 第二个调用函数 Looper循环
`Looper.java`

``` java
public static final void loop(){
	//myLooper返回保存在ThreadLocal调用线程中返回的Looper对象
	Looper me=myLooper();
	//取出Looper的消息队列
	MessageQueue queue=me.mQueue;
	while(true){
		Message msg=queue.netxt();
		//处理消息，Message消息中有一个target，target是Handler类型
		//如果target为空，则退出消息循环
		if(msg!=null){
			if(target==null){
				return;
			}
			//调用该消息的Handler，交给它的dispatchMessage函数处理
			msg.target.dispatchMessage();
			msg.recycle();
		}
	}
}
//myLooper函数返回调用线程的线程局部变量，也就是保存在其中的Looper对象
public static final Looper myLooper(){
	return (Looper)sThreadLocal.get();
}
```
通过上面的代码发现,`Looper`的作用：
* 封装了一个消息队列。
* `Looper`的`prepare`函数把当前这个`Looper`和调用这个`prepare`的线程绑定在一起了(也就是处理线程)。
* 处理线程调用`loop`函数，处理来自该消息队列的消息。
> 当消息源向Looper发送消息的时候，其实是把消息添加到这个Looper的消息队列中，那么该消息是由与Looper绑定的调用线程来处理。消息是怎么向Looper的消息队列里面添加消息呢？

#### Mesage和Looper、Handler的关系
* Looper中有一个Message消息队列，里面存储的待处理的Message。
* Message中有一个Handler，这个Handler是用来处理消息的。
Handler封装了很多工作，首先来了解一下。

### Handler分析
Handler中的成员
`Handler.java`

``` java
final MessageQueue mQueue;//Handler中也有一个消息队列
final Looper mLooper;//也有一个Looper
final Callback mCallback;//有一个回调的类
```
这几个成员变量是怎么使用的呢?首先得分析一下Handler的构造函数，Handler一共有四个构造函数，它们主要的区别是对上面这三个重要的成员的初始化上，逐个分析：
`Handler.java`

``` java
//构造函数
public Handler(){
	//获得调用线程的Looper
	mLooper=Looper.myLooper();
	if(mLooper==null){
		throw new RuntimeException(...);
	}
	//得到Looper的消息队列
	mQueue=mLooper.mQueue;
	//无callback设置
	mCallback=null;
}
//构造函数2
public Handler(Callback callback){
	mLooper=Looper.myLooper();
	if(mLooper==null){
		throw new RuntimeException(...);
	}
	mQueue=mLooper.mQueue;
	//和构造1类似，多了个设置callback
	mCallback=callback;
}

//构造函数3
public Handler(Looper looper){
	//由外部传入的looper,哪个线程的Looper不确定
	mLooper=looper;
	mQueue=looper.mQueue;
	mCallback=null;
}

//构造函数4
public Handler(Looper looper,Callback callback){
	//和构造3类似，多了callback参数
	mLooper=looper;
	mQueue=looper.mQueue;
	mCallback=callback;
}
```
> 在上述构造函数中，Handler中的消息队列变量始终会指向Looper的消息队列，Handler为什么要这样做？

###### 在提出这个问题之前，再提一个问题，怎样向消息队列中插入消息?
> 如果不知道Handler，有一个很原始的方法解决上面的问题：
* 调用Looper中的mQueue，它将返回消息队列MessageQueue的对象
* 构造一个Message,填充它的成员，尤其是target变量
* 调用MessageQueue中的enqueueMessage,将消息插入队列
> 这种原始的方法很麻烦，而且容易出错。但是有了Handler了之后，我们的工作就变得简单了。Handler更像一个辅助类，简化了编程工作。

##### Handler和Message
Handler提供了一系列的API，帮助我们创建发送消息和插入消息队列，详情还要阅读[API文档][2]。

``` java
//查看消息队列中是否有消息码是what的消息码
final boolean hasMessages(int what)
//从Handler中创建一个消息码是what的消息
final Message obtainMessage(int what)
//从消息队列中移除消息码是what的消息
final void removeMessage(int what)
//发送一个只填充消息码的消息
final boolean sendEmptyMessage(int what)
//发送一个消息，添加到队列尾
final void sendMessage(int what)
//发送一个消息，插入到队列头，所以优先度高
final void sendMessageAtFrontOfQueue(int what)

```
对这些函数稍作分析就能明白其它函数，以`sendMessage`为例：
`Handler.java`

``` java
public final boolean sendMessage(Message msg){
	return sendMessageDelayed(msg,0);//调用sendMessageDelayed
}

//delayMillis 是以当前时间为基础的相对时间
public final boolean sendMessageDelayed(Message msg,long delayMillis){
	if(delayMillis<0){
		delayMillis=0;
	}
	//调用sendMessageAtTime,加上当前时间
	return sendMessageAtTime(msg,SystemClock.uptimeMillis+delayMillis);
}

//uptimeMillis是绝对时间，即sendMessageAtTime处理的是绝对时间
public final boolean sendMessageAtTime(Message msg,long uptimeMillis){
	boolean sent=false;
	MessageQueue queue=mQueue;
	if(queue!=null){
	//把Message的target设置为自己，并插入到消息队列
	msg.target=this;
	sent=queue.enqueueMessage(msg,uptimeMillis);
	}
	return sent;
}
```
> Handler把Message的target设置为自己，是因为Handler除了封装消息添加功能外，还封装了消息处理的接口。

###### 我们在往Looper的消息队列中添加一条消息，按照Looper的处理规则，它会调用target的dispatchMessage，再把这个消息派发给Handler处理，Handler这块是怎么处理的呢？
`Handler.java`

``` java
public void dispatchMessage(Message msg){
	//如果message本身有callback,就交给Message本身的callback处理
	if(msg.callback!=null){
		handleCalback(msg);
	}else{
		//如果Handler 设置了mCallback,则交给mCallback处理
		if(mCallback!=null){
			if(mCallback.handleMessage(msg)){
				return;
			}
		}
		//最后才交给子类处理
		handleMessage(msg);
	}
}
```
`dispatchMessage`定义了一套消息处理优先机制：
* `Message`如果自带的Callback，则交给Callback处理
* `Handler`如果设置了全局的mCallback，则交给mCallback处理
* 如果以上都没有，则交给`Handler`子类实现的`handleMessage`来处理，需要`override`该函数












[1]:/images/2015/5/20/handlerandlooper.png
[2]:http://developer.android.com/reference/android/os/Handler.html
