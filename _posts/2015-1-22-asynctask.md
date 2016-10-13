---
layout: post
title: 'AsyncTask'
date: '2015-1-23 01:10:00'
description:
image: http://images.cnitblog.com/blog/107067/201302/06145440-4e5f6ae87edd430ea2ce38c19b61a8e0.png
image-sm: http://images.cnitblog.com/blog/107067/201302/06145440-4e5f6ae87edd430ea2ce38c19b61a8e0.png
categories:
  - Android

---

 异步任务类


 - 前段时间，用到AsyncTask， 发现自己有些忘了，温习了一下，顺便做个笔记。



实际业务中，如果访问网络需求多，频繁开启线程比较耗费资源，因此，我们需要使用一些线程池。
Google已经很好的封装好了一个异步任务类，AsyncTask。
<!--more-->
AsyncTask 一般用于做一些耗时的操作，其中内部封装了子线程，帮我们开启子线程，帮我们去做一些事情.
打开AsyncTask源文件，首先看到的，它是一个抽象类，我们要使用它，要么写一个内部类，要么写个类继承它。

先简单的创建一个对象出来看一下主要的三个方法：

``` java

new AsyncTask<Void, Void, Void>() {

		/**
		 * 执行之前调用, 执行在主线程中.
		 */
		@Override
		protected void onPreExecute() {
			System.out.println("onPreExecute: " + Thread.currentThread().getName());
		}

		/**
		 * 在onPreExecute执行完之后，执行, 运行在子线程中.
		 */
		@Override
		protected Void doInBackground(Void... params) {

			System.out.println("doInBackground: " + Thread.currentThread().getName());
			return null;
		}

		/**
		 * 在doInBackground方法执行之后, 调用.
		 * 运行在主线程中
		 */
		@Override
		protected void onPostExecute(Void result) {
			System.out.println("onPostExecute: " + Thread.currentThread().getName());
		}
	}.execute(new Void[]{}); // 调用onPreExecute

```

在AsyncTask源文件中看到我需要的线程池：

``` java
/**
 *An {@link Executor} taht can be used to execute tasks in parallel.
 */
public static final Executor THREAD_POOL_EXECUTOR
		=new ThreadPoolExecutor(CORE_POOL_SIZE,MAXIMUM_POOL_SIZE,KEEP_ALIVE,
			TimeUnit.SECONDS,sPoolWorkQueue,sThreadFactory);
```

如果我需要更新主线程UI如何在子线程中操作呢？
这里就需要再看一下源文件，它使用的是Handler消息机制：

``` java

private static class InternalHandler extends Handler{
	@SuppressWarnings({"unchecked","RawUseOfParameteriazedType"})
	@Override
	public void handleMessage(Message msg){
		AsyncTaskResult result = (AsyncTaskResult) msg.obj;
		switch(msg.what){
			case: MESSAGE_POST_RESULT:
				//There is only one result
				result.mTask.finish(result.mData[0]);
				break;
			case: MESSAGE_POST_PROGRESS:
				result.mTask.onProgressUpdate(result.mData);
				break;
		}
	}
}
```

### 我们如果要用它的话，需要做的工作就是写一个类继承它，这时候可能会问，如果我继承了它，会不会有新的线程被创建了呢？
- 不会的，因为`static`修饰符决定的，除了子类，但凡用的时候，都是用的同一个线程池。
- 通过看`CORE_POOL_SIZE`常量，它默认开启了5个线程。

### 通过看源文件我们知道，AsyncTask主要做了两件事：

1.  使用了线程池
2.  用Handler进行通知消息




>   在业务需求中，由于很多界面需要网络操作，这里我就把它放在父类BaseUi里边。
>   这样我很多子类就不需要新创建AsyncTask了。由于我的业务操作中很多结果都封装成了Message，所以这边父类BaseUi里边返回参数是Message,
而且，我也不需要进度提示，所以把Progress改成Void,
>   但是我不知道Params传的是什么，有可能是用户名，也有可能是数据，所以我需要在`MyAsyncTask<Params> `加一个泛型。

我把MyAsyncTask定义成抽象类，子类只需要重写父类`doInBackground`方法就好了，
如果我需要在执行之前加一些操作，比如当前没有网络，我需要加一个网络判断该怎么做？

``` java
protected abstract class MyAsyncTask<Params> extends AsyncTask<Params,Void,Message>{
}
```

这个时候，应该想到的是把这个execute()给重写了，但是通过查看源文件`execute`方法:

``` java
public final AsyncTask<Params,Progress,Result>execute(Params... params){
	return executeOnExecutor(sDefaulExecutor,params);
}
```

它是一个final类，这个时候，我需要这么做，
它不能`override`父类方法，我就重新写一个方法包装一下：

``` java
protected abstract class MyAsyncTask<Params> extends AsyncTask<Params,Void,Message>{
	public final AsyncTask<Params,Void,Message> executeProxy(Params... params){
		return super.execute(params);
	}
}
```

下面是改装版的AsyncTask完整代码：

``` java
protected abstract class MyAsyncTask<Params> extends AsyncTask<Params,Void,Message>{
	public final AsyncTask<Params,Void,Message> executeProxy(Params... params){
		if(NetworkUtil.checkNet(mContext){
			return super.execute(params);
		}else{
			PromUtil.showNoNetwork(mContext);
		}
		return null;
	}
}
```

> 之所以这么做是因为我想在这个线程执行之前做一些操作，如果有网络就执行父类`execute`方法，如果没网络就做一个提示。

[图片来源](http://www.cnblogs.com/leolcao/archive/2013/02/06/2906239.html)
