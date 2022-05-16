<h1><center>Service</center></h1>

- #### 创建一个MyService

  像新建一个Activity一样创建，Android studio会自动帮你生成以下代码。并在AndroidMainfest.xml中注册。

```
public class MyService extends Service {
    public MyService() {
    }

    @Override
    public IBinder onBind(Intent intent) {
        // TODO: Return the communication channel to the service.
        throw new UnsupportedOperationException("Not yet implemented");
    }
}
在AndroidMainfest.xml中
<service
    android:name=".MyService"
    android:enabled="true"
    android:exported="true" />
```

- #### MyService中的生命周期

  第一次被创建（绑定）时调用onCreate( ),每次开启服务都会调用onStartCommand(Intent intent, int flags, int startId)，死亡时调用onDestroy( )。

```
@Override
public void onCreate() {
	super.onCreate();
}

@Override
public int onStartCommand(Intent intent, int flags, int startId) {
    new Thread(new Runnable() {
        @RequiresApi(api = Build.VERSION_CODES.O)
        @Override
        public void run() {
            // 开始执行后台任务
            Log.d(TAG, "run: onStartCommand");
        }
    }).start();
    return super.onStartCommand(intent, flags, startId);
}

@Override
public void onDestroy() {
	super.onDestroy();
}
```

- #### 开启服务的两种方式

```
第一种：第一次开启服务后调用onCreate()和onStartCommand()，后面再开启服务只调用onStartCommand()，通过onStartCommand()执行想要的操作，关闭服务调用onDestroy()。注：如果开启服务后又绑定必须结束服务和解除绑定才会调用onDestroy()，服务中并不能做工作量很大的工作，可以通过Thread开个线程来做。
	//开启服务
	Intent startIntent = new Intent(this, MyService.class);
	startService(startIntent);
	//关闭服务
	Intent stopIntent = new Intent(this, MyService.class);
    stopService(stopIntent);
第二种：每次绑定服务都只调用onCreate()，解除绑定调用onDestroy()。
	//绑定
	Intent bindIntent = new Intent(this, MyService.class);
    bindService(bindIntent, connection, BIND_AUTO_CREATE);
    //解绑
    try {
    	unbindService(connection);
    	Log.d(TAG, "unbindService: ");
    }catch (Exception e){
    	Log.d(TAG, "onClick: ");
    }
```

```
绑定服务的运行流程：绑定服务会先执行onCreate()，然后执行onServiceConnected()建立关联，通过MyBinder执行需要的操作。运行onServiceDisconnected()解除关联（非正常死亡时会调用），然后调用onDestroy()。
	//MyService
	@Override
    public IBinder onBind(Intent intent) {
        return mBinder;
    }

    public static class MyBinder extends Binder {

        public void startDownload() {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    // 执行具体的下载任务
                    Log.d(TAG, "run: startDownload()");
                }
            }).start();
        }
    }
	
	//MainActivity
	private ServiceConnection connection = new ServiceConnection() {
        @Override
        public void onServiceDisconnected(ComponentName name) {
            Log.d(TAG, "onServiceDisconnected");
        }
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            myBinder = new MyService.MyBinder();
            //Log.d(TAG, "onServiceConnected myBinder: "+myBinder);
            myBinder.startDownload();
        }
    };
```

- #### 远程服务

在AndroidMainfest.xml中加一条android:process

## IntentService的使用

如果我们直接把 耗时线程放到Service中的onStart()方法中，虽然可以这样做，但是很容易 会引起ANR异常(Application Not Responding)，而Android的官方在介绍 Service有下面这样一段话：

> *1.Service不是一个单独的进程,它和它的应用程序在同一个进程中*
> *2.Service不是一个线程,这样就意味着我们应该避免在Service中进行耗时操作*

于是乎，Android给我们提供了解决上述问题的替代品，就是下面要讲的**IntentService**； IntentService是继承与Service并处理异步请求的一个类,在IntentService中有 一个工作线程来处理耗时操作,请求的Intent记录会加入队列

**工作流程：**

> 客户端通过startService(Intent)来启动IntentService; 我们并不需要手动地区控制IntentService,当任务执行完后,IntentService会自动停止; 可以启动IntentService多次,每个耗时操作会以工作队列的方式在IntentService的 onHandleIntent回调方法中执行,并且每次只会执行一个工作线程,执行完一，再到二这样!

**小结：**

> 当一个后台的任务,需要分成几个子任务,然后按先后顺序执行,子任务 (简单的说就是异步操作),此时如果我们还是定义一个普通Service然后 在onStart方法中开辟线程,然后又要去控制线程,这样显得非常的繁琐; 此时应该自定义一个IntentService然后再onHandleIntent()方法中完成相关任务！

## Activity与Service通信

我们前面的操作都是通过Activity启动和停止Service，假如我们启动的是一个下载 的后台Service，而我们想知道Service中下载任务的进度！那么这肯定是需要Service 与Activity进行通信的，而他们之间交流的媒介就是Service中的onBind()方法！ 返回一个我们自定义的Binder对象！

基本流程如下：

- 1.自定义Service中，自定义一个Binder类，然后将需要暴露的方法都写到该类中！
- 2.Service类中，实例化这个自定义Binder类，然后重写onBind()方法，将这个Binder对象返回！
- 3.Activity类中实例化一个ServiceConnection对象，重写onServiceConnected()方法，然后 获取Binder对象，然后调用相关方法即可！

## 一个简单前台服务的实现

我们都知道Service一般都是运行在后台的，但是Service的系统优先级 还是比较低的，当系统内存不足的时候，就有可能回收正在后台运行的Service， 对于这种情况我们可以使用前台服务，从而让Service稍微没那么容易被系统杀死， 当然还是有可能被杀死的...所谓的前台服务就是状态栏显示的Notification！