<h1><center>Handler</center></h1>

## 一、定义

> Handler：子线程与主线程之间的沟通中介，用于传递处理消息。

在 Android 中，为保证处理 UI 工作的线程稳定安全，规定只有主线程才能更新处理 UI 工作。所以当子线程想处理 UI 工作时，就需要通过 Handler 来通知主线程作出相对应的 UI 处理工作。 **如下图所示：**

![Handler是子线程与主线程的沟通中介](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/2/4/1700f1d829f236e6~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

## 二、 Handler 的三种使用方法

分别是：

1. Handler.sendMessage(Message)
2. Handler.post(Runnable)
3. Handler.obtainMessage(what).sendToTarget();

### 1. Handler.sendMessage()方法

​	Handler.sendMessage(Msg) 方法是最为常见的一种方法。

#### 1.1 使用步骤说明

其使用步骤分四步，如下所示：

1. 步骤一：新建 Handler 对象，覆写 handleMessage(Message) 方法。
2. 步骤二：新建 Message 对象，设置其携带的数据。
3. 步骤三：在子线程中通过 Handler.sendMessage(Message) 方法发送信息。
4. 步骤四：在 Handler 的 handleMessage(Message msg) 方法中处理消息，通知主线程作出相对应的 UI 工作。



- **步骤一：新建 Handler 对象，覆写 handleMessage(Message) 方法**

```
//创建 Handler对象，并关联主线程消息队列
public Handler mHandler = new Handler(Looper.getMainLooper()) {
    @Override
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
            ······
        }
    }
};
```

- **步骤二：新建 Message 对象，设置其携带的数据**

```
Bundle bundle = new Bundle();
bundle.putInt(CURRENT_PROCESS_KEY, i);
Message progressBarProcessMsg = new Message();
progressBarProcessMsg.setData(bundle);
progressBarProcessMsg.obj = "modify_i";
```

- **步骤三：在子线程中通过 Handler.sendMessage(Message) 方法发送信息**

```
mHandler.sendMessage(progressBarProcessMsg);
```

- **步骤四：在 Handler 的 handleMessage(Message msg) 方法中处理消息，通知主线程作出相对应的 UI 工作**

```
	public Handler mHandler = new Handler(Looper.getMainLooper()) {
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            String type = (String) msg.obj;
            switch (type) {
                case "modify_ui":
                    //更新 TextView UI
                    mDisplayTv.setText("CustomChildThread starting!");
                    break;
                case "modify_i":
                    //获取 ProgressBar 的进度，然后显示进度值
                    Bundle bundle = msg.getData();
                    int process = bundle.getInt(CURRENT_PROCESS_KEY);
                    mProgressBar.setProgress(process);
                    break;
                default:
                    break;
            }
        }
    };
```

子线程:

```
package com.wangxin.myhandler;

import static com.wangxin.myhandler.MainActivity.CURRENT_PROCESS_KEY;

import android.content.Context;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;

/**
 * 子线程，用于处理耗时工作
 */
public class CustomChildThread extends Thread {

    private Handler mHandler;

    public CustomChildThread(Handler handler) {
        this.mHandler = handler;
    }

    @Override
    public void run() {
        //在子线程中创建一个消息对象
        Message childThreadMessage = new Message();
        childThreadMessage.obj = "modify_ui";
        //将该消息放入主线程的消息队列中
        mHandler.sendMessage(childThreadMessage);

        //模拟耗时进度，将进度值传给主线程用于更新 ProgressBar 进度。
        for (int i = 1; i <= 100; i++) {
            try {
                //让当前执行的线程（即 CustomChildThread）睡眠 1s
                Thread.sleep(100);

                //Message 传递参数
                Bundle bundle = new Bundle();
                bundle.putInt(CURRENT_PROCESS_KEY, i);
                Message progressBarProcessMsg = new Message();
                progressBarProcessMsg.setData(bundle);
                progressBarProcessMsg.obj = "modify_i";
                mHandler.sendMessage(progressBarProcessMsg);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 2. Handler.post()方法

除了使用 Handler.sendMessage(Message) 来发送信息，Handler 还支持 post(Runnable) 方法来传递消息，通知主线程做出相对应的 UI 工作。使用方法如下：

```
/**
 * 将可运行的 Runnable 添加到消息队列。Runnable 将在该 Handler 相关的线程上运行处理。
 * The runnable will be run on the thread to which this handler is attached.
 */
new Handler().post(new Runnable() {
    @Override
    public void run() {
        //更新处理 UI 工作
    }
});
```

### 3. obtainMessage()方法

obtainMessage() 方法与 sendMessage() 方法很相似，通过 mHandler.obtainMessage().sendToTarget() 发送信息。该方法与 sendMessage() 的区别就是你不用额外去创建一个 Message 对象。

obtainMessage() 方法有三种，分别是：

```
//指定 what 用于区分，通过 Message.what 获得
public final Message obtainMessage(int what);

//传递obj参数，通过 Message.obj 获得
public final Message obtainMessage(int what, @Nullable Object obj)

//传递arg1 arg2参数，通过 Message.arg1 Message.arg2 获得
public final Message obtainMessage(int what, int arg1, int arg2)	
```

