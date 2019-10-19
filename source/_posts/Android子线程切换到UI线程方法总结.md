---
title: Android子线程切换到UI线程方法总结
date: 2019-10-19 13:10:59
tags: 线程切换
categories: 
- Android
- 知识点
---

#### 线程切换
> 通过消息发送（发布）和接收（订阅）的方式切换的。
##### 1 .Handler 
> 子线程（非UI线程）调用handler对象sendMessage（msg）方法，将消息发送给关联Looper，Looper将消息存储在MessageQueue消息队列里面。
> 
> 然后轮巡取出MessageQueue中的消息给UI线程中handler处理，handler得到消息调用handleMessage方法处理消息，从而可以更新Ui。

```
 private Handler handler2=new Handler(){
        @Override
        public void handleMessage(Message msg) {
                switch (msg.what){
                    case 1:
	                    //UI线程
                        textView.setText(msg.obj.toString());
                }
        }
    };

//开启线程执行耗时操作
new Thread(){
            @Override
            public void run() {
	            //执行相关耗时操作等等...
	            //休眠3s
	            sleep(3000);
                Message message = new Message();
                //区分标志
                message.what=1;
                //传参到ui线程
                message.obj=123123;
                handler2.sendMessage(message);
            }
        }.start();
```
** 扩展注意：**
*  1 . Handler机制与Activity生命周期不一致的原因，容易导致内存泄漏。
*  2 . 实例化 handler
```
//实例化handler在其他线程的时候要下面这样写（UI线程内部已经实现，无需写）
 private Handler handler = new Handler(Looper.getMainLooper());
```
##### 2. Activity.runOnUiThread
> 用Activity对象的runOnUiThread方法更新，在子线程中通过runOnUiThread()方法更新UI 
```
new Thread() {
        public void run() {
            while (i++ < 1000) {
                try {
                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            textView.setText("#" + i);
                        }
                    });
                    Thread.sleep(300);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }.start();
```

##### 3. View.post(Runnable r)
> View.post(Runnableaction)，View获得当前线程（即UI线程）的Handler，然后将action对象post到Handler里。
>
> 在Handler里，它将传递过来的action对象包装成一个Message（Message的callback为action），然后将其投入UI线程的消息循环中。
>
> 在Handler再次处理该Message时，直接调用runnable的run方法。而此时，已经路由到UI线程里，因此，我们可以毫无顾虑的来更新UI。
```
	new Thread(){
            @Override
            public void run() {
                super.run();
                for (int i=0;i<3;i++){
                    try {
                        sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                tv.post(new Runnable() {
                    @Override
                    public void run() {
                        tv.setText("通过View.post(Runnable r) 方法");
                    }
                });
            }
        }.run();
```
**扩展注意：**

子线程能不能更新UI，答案是**肯定**的。具体阅读下这篇文章：[Android 子线程更新UI？](https://juejin.im/post/5da14e8ae51d45782b0c1c20)

上面就是为了实现用一个Thread来更新tv，可以实现这个功能，刷新UI界面。但是这样是不对的，因为它违背了单线程模型：Android UI操作并不是线程安全的并且这些操作必须在UI线程中执行。

##### 4. AsyncTask
> 内部就是一个Handler和线程池的封装。在线程池中执行后台任务，并在执行过程中将执行进度传递给主线程，当任务执行完毕后，将最终结果传递给主线程。
```
private class MyAsyncTask extends AsyncTask{
		//第一阶段————准备阶段
          @Override
          protected void onPreExecute() {
              super.onPreExecute();
          } 	
        //第二阶段————耗时操作
        @Override
        protected Object doInBackground(Object[] objects) {
            for (int i=0;i<3;i++){
                try {
                    sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            return objects[0].toString();
        }
       //第三阶段———— 更新UI
        @Override
        protected void onPostExecute(Object o) {
            super.onPostExecute(o);
            tv.setText(o.toString());
        }
    }
```
##### 5. Rxjava
> RXJAVA的实现，是一种扩展式的观察者模式。
>
> RXJAVA中有四种概念。observable（被观察者），observer（观察者），subscribe（订阅），事件。
>
> Observable和Observer通过subscribe来实现订阅关系。
>
> 与传统的观察者模式不同，除了onNext事件外，rxjava还提供了onCompleted和onError。当不再有onNext事件发送时，将以onCompleted事件作为结束。当处理过程中出现异常时，会触发onError，同时队列自动终止，不允许再有事件发出。onCompleted和onError在一个序列中有且只有一个，二者互斥，只能出现一个。

源码来自：[RxJava线程切换代替Thread和Handler](https://www.jianshu.com/p/9d4e39a83a74)
```
 public static <T> void doTask(final Task<T> task) {
        //创建被观察者 Observable，它决定什么时候触发事件以及触发怎样的事件。
        Observable.create(new Observable.OnSubscribe<T>() {
            @Override
            public void call(Subscriber<? super T> subscriber) {
                // 线程执行耗时操作
//                task.doOnIOThread();
//                subscriber.onNext(task.getT());
                subscriber.onCompleted();
            }
        })
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                //订阅
                .subscribe(new Action1<T>() {
                    @Override
                    public void call(T t) {
                        //主线程更新UI
//                        task.doOnUIThread();
                    }
                }, new Action1<Throwable>() {
                    @Override
                    public void call(Throwable throwable) {
                        throwable.printStackTrace();
                    }
                });
    }
```