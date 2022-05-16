<h1><center>获取Context</center></h1>

​		不同的地方需要用不同的方法，下面这种适用于绝大部分。先自定义一个类（MyApplication）继承Application，重写onCreate( )在其中利用getApplicationContext()获取context。然后来一个get方法让用的地方获取。此方法需要在AndroidMainfest.xml中初始化。

```
package com.example.servicedemo;

import android.app.Application;
import android.content.Context;

public class MyApplication extends Application {
    private static Context context;

    @Override
    public void onCreate() {
        super.onCreate();
        context = getApplicationContext();
    }

    public static Context getContext() {
        return context;
    }
}

```

```
android:name="com.example.servicedemo.MyApplication"//完整包名

MyApplication.getContext()//使用
```

```
//其他方法
public class MyActivity extends Activity {
    public void method() {
       mContext = this;    // since Activity extends Context
       mContext = getApplicationContext();
       mContext = getBaseContext();
    }
}
```

```
以下是我在使用context方面的发现：
1)。在Activity中，使用this对布局和菜单进行充气、注册上下文菜单、实例化小部件、启动其他活动、在Activity中创建新的Intent、实例化首选项或Activity中提供的其他方法。

充气布局：
View mView = this.getLayoutInflater().inflate(R.layout.myLayout, myViewGroup);

充气菜单：
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    super.onCreateOptionsMenu(menu);
    this.getMenuInflater().inflate(R.menu.mymenu, menu);
    return true;
}

注册上下文菜单：
this.registerForContextMenu(myView);

实例化小部件：
TextView myTextView = (TextView) this.findViewById(R.id.myTextView);

启动Activity：
Intent mIntent = new Intent(this, MyActivity.class);
this.startActivity(mIntent);

实例化首选项：
SharedPreferences mSharedPreferences = this.getPreferenceManager().getSharedPreference

2)。对于应用程序范围的类，使用getApplicationContext()，因为在应用程序的生命周期中存在此上下文。

检索当前Android软件包的名称：
public class MyApplication extends Application {    
    public static String getPackageName() {
        String packageName = null;
        try {
            PackageInfo mPackageInfo = getApplicationContext().getPackageManager().getPackageInfo(getApplicationContext().getPackageName(), 0);
            packageName = mPackageInfo.packageName;
        } catch (NameNotFoundException e) {
            // Log error here.
        }
        return packageName;
    }
}

绑定应用程序范围类：
Intent mIntent = new Intent(this, MyPersistent.class);
MyServiceConnection mServiceConnection = new MyServiceConnection();
if (mServiceConnection != null) {
    getApplicationContext().bindService(mIntent, mServiceConnection, Context.BIND_AUTO_CREATE);
}

3)。对于侦听器和其他类型的Android类(例如ContentObserver)，使用上下文替换，如：
mContext = this;    // Example 1
mContext = context; // Example 2
其中this或context是类(活动等)的上下文。

Activity上下文替换：
public class MyActivity extends Activity {
    private Context mContext;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);        
        mContext = this;
    }
}

侦听器上下文替换：
public class MyLocationListener implements LocationListener {
    private Context mContext;
    public MyLocationListener(Context context) {
        mContext = context;
    }
}

ContentObserver上下文替换：
public class MyContentObserver extends ContentObserver {
    private Context mContext;
    public MyContentObserver(Handler handler, Context context) {
        super(handler);
        mContext = context;
    }
}

4)。对于BroadcastReceiver(包括内联/嵌入式接收器)，使用接收器自己的上下文。

外部BroadcastReceiver：
public class MyBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        final String action = intent.getAction();
        if (action.equals(Intent.ACTION_SCREEN_OFF)) {
            sendReceiverAction(context, true);
        }
        private static void sendReceiverAction(Context context, boolean state) {
            Intent mIntent = new Intent(context.getClass().getName() +"." + context.getString(R.string.receiver_action));
            mIntent.putExtra("extra", state);
            context.sendBroadcast(mIntent, null);
        }
    }
}

嵌入式/嵌入式BroadcastReceiver：
public class MyActivity extends Activity {
    private BroadcastReceiver mBroadcastReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            final boolean connected = intent.getBooleanExtra(context.getString(R.string.connected), false);
            if (connected) {
                // Do something.
            }
        }
    };
}

5)。对于服务，使用服务自己的上下文。
public class MyService extends Service {
    private BroadcastReceiver mBroadcastReceiver;
    @Override
    public void onCreate() {
        super.onCreate();
        registerReceiver();
    }
    private void registerReceiver() {
        IntentFilter mIntentFilter = new IntentFilter();
        mIntentFilter.addAction(Intent.ACTION_SCREEN_OFF);
        this.mBroadcastReceiver = new MyBroadcastReceiver();
        this.registerReceiver(this.mBroadcastReceiver, mIntentFilter);
    }
}

6)。对于祝酒词，通常使用getApplicationContext()，但在可能的情况下，使用从活动、服务等传递的上下文。

使用应用程序上下文：
Toast mToast = Toast.makeText(getApplicationContext(), message, Toast.LENGTH_LONG);
mToast.show();

使用从源传递的上下文：
public static void showLongToast(Context context, String message) {
    if (context != null && message != null) {
        Toast mToast = Toast.makeText(context, message, Toast.LENGTH_LONG);
        mToast.show();
    }
}
最后，不要按照安卓框架开发人员的建议使用getBaseContext()。
```

