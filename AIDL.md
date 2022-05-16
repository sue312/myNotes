<h1><center>AIDL</center></h1>

### 一、定义

AIDL是一个缩写，全称是Android Interface Definition Language，也就是Android接口定义语言。

设计这门语言的目的是为了实现**进程间通信**。

### 二、使用

在服务端先创建出service，然后在java同级目录创建aidl文件夹，new一个A.aidl文件。写完A.aidl后，make project一下。然后在用Bbinder继承A.Stub，重写

A.aidl中的方法。再在service中new Bbinder，并在onbinder中返回它。

```
package com.wangxin.myservice;

interface IPowerManagerInterface {
    void open();
    void close();
}
```

```
public class PowerManagerBinder extends IPowerManagerInterface.Stub {
    @Override
    public void open() throws RemoteException {
        Log.d("wangxin666", "PowerManagerBinder: open()");
    }

    @Override
    public void close() throws RemoteException {
        Log.d("wangxin666", "PowerManagerBinder: close");
    }
}
```

```
	@Override
    public IBinder onBind(Intent intent) {
        // TODO: Return the communication channel to the service.
        return powerManagerBinder;
    }

    @Override
    public void onCreate() {
        super.onCreate();
        if (powerManagerBinder == null) {
            powerManagerBinder = new PowerManagerBinder(this);
        }
    }
```



在客户端，先在java同级目录创建aidl文件夹，然后创建A.aidl，注意包名要和服务端A.aidl相同。使用Intent绑定服务。在mServiceConnection中获得 stub 的实例，IPowerManagerInterface.Stub.asInterface(service)。最后就可以调用service中的部分方法了。

```
Intent intent = new Intent("com.wangxin.myservice.IPowerManagerInterface");
intent.setPackage("com.wangxin.myservice") ;
this.bindService(intent,mServiceConnection, Context.BIND_AUTO_CREATE);
```

```
    //    private IPowerManagerInterface powerManagerInterface;
    ServiceConnection mServiceConnection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            powerManagerInterface = IPowerManagerInterface.Stub.asInterface(service);
            Log.d("wangxin666", "onServiceConnected: ");
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {
            powerManagerInterface = null;
            Log.d("wangxin666", "onServiceDisconnected: ");
        }
    };
```

### 三、省电模式权限

服务端androidManifest添加系统权限和需求所需权限。

```
android:sharedUserId="android.uid.system"
xmlns:tools="http://schemas.android.com/tools"

<uses-permission android:name="android.permission.DEVICE_POWER"
        tools:ignore="ProtectedPermissions" />
```

客户APK的Android.mk,预置位置设置为priv-app。服务端和客户端权限都设置为platform。

```
LOCAL_MODULE_PATH := $(TARGET_OUT)/priv-app

LOCAL_CERTIFICATE := platform
```

客户端APK，需要进行系统签名。并且要预置到手机中，androidManifest也需要添加权限。

```
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore release-key.keystore signed.apk alias_name
```



