<h1><center>Gestures</center></h1>

## 1.Android中手势交互的执行顺序

- 1.手指触碰屏幕时，触发MotionEvent事件！
- 2.该事件被OnTouchListener监听，可在它的onTouch()方法中获得该MotionEvent对象！
- 3.通过GestureDetector转发MotionEvent对象给OnGestureListener
- 4.我们可以通过OnGestureListener获得该对象，然后获取相关信息，以及做相关处理！

## 2.GestureListener详解+3.简单的例子:下滑关闭Activity，上滑启动新的Activity

- 按下（onDown）： 刚刚手指接触到触摸屏的那一刹那，就是触的那一下。
- 抛掷（onFling）： 手指在触摸屏上迅速移动，并松开的动作。
- 长按（onLongPress）： 手指按在持续一段时间，并且没有松开。
- 滚动（onScroll）： 手指在触摸屏上滑动。
- 按住（onShowPress）： 手指按在触摸屏上，它的时间范围在按下起效，在长按之前。
- 抬起（onSingleTapUp）：手指离开触摸屏的那一刹那。

知道了GestureListener的相关方法后，实现手势检测也很简单，步骤如下：

> - Step 1: 创建GestureDetector对象，创建时需实现GestureListener传入
> - Step 2: 将Activity或者特定组件上的TouchEvent的事件交给GestureDetector处理即可！ 我们写个简单的代码来验证这个流程，即重写对应的方法：

代码如下：

```
package com.wangxin.gestures;

import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.util.Log;
import android.view.GestureDetector;
import android.view.MotionEvent;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    private MyGestureListener mgListener;
    private MyGestureListener2 mgListener2;
    private GestureDetector mDetector;
    private GestureDetector mDetector2;
    private final static String TAG = "MyGesture";
    private final static int MIN_MOVE = 50;   //最小距离

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //实例化GestureListener与GestureDetector对象
        mgListener = new MyGestureListener();
        mDetector = new GestureDetector(this, mgListener);
        mgListener2 = new MyGestureListener2();
        mDetector2 = new GestureDetector(this, mgListener2);
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        return mDetector2.onTouchEvent(event);
    }

    //自定义一个GestureListener,这个是View类下的，别写错哦！！！
    private class MyGestureListener implements GestureDetector.OnGestureListener {

        @Override
        public boolean onDown(MotionEvent motionEvent) {
            Log.d(TAG, "onDown:按下");
            return false;
        }

        @Override
        public void onShowPress(MotionEvent motionEvent) {
            Log.d(TAG, "onShowPress:手指按下一段时间,不过还没到长按");
        }

        @Override
        public boolean onSingleTapUp(MotionEvent motionEvent) {
            Log.d(TAG, "onSingleTapUp:手指离开屏幕的一瞬间");
            return false;
        }

        @Override
        public boolean onScroll(MotionEvent motionEvent, MotionEvent motionEvent1, float v, float v1) {
            Log.d(TAG, "onScroll:在触摸屏上滑动");
            return false;
        }

        @Override
        public void onLongPress(MotionEvent motionEvent) {
            Log.d(TAG, "onLongPress:长按并且没有松开");
        }

        @Override
        public boolean onFling(MotionEvent motionEvent, MotionEvent motionEvent1, float v, float v1) {
            Log.d(TAG, "onFling:迅速滑动，并松开");
            return false;
        }
    }

    //下滑关闭Activity，上滑启动新的Activity
    private class MyGestureListener2 extends GestureDetector.SimpleOnGestureListener {

        @Override
        public boolean onFling(MotionEvent e1, MotionEvent e2, float v, float v1) {
            if(e1.getY() - e2.getY() > MIN_MOVE){
                startActivity(new Intent(MainActivity.this, MainActivity.class));
                Toast.makeText(MainActivity.this, "通过手势启动Activity", Toast.LENGTH_SHORT).show();
            }else if(e1.getY() - e2.getY()  < MIN_MOVE){
                finish();
                Toast.makeText(MainActivity.this,"通过手势关闭Activity",Toast.LENGTH_SHORT).show();
            }
            return true;
        }
    }

}
```

> **结果分析：** 从上面的对比就可以知道，相比起SimpleOnGestureListener使用OnGestureListener 显得更加的简单，想重写什么方法就重写什么方法

## 4.手势添加与识别：

*除了上面讲解的手势检测外，Android还运行我们将手势进行添加，然后提供了相关的识别API； Android中使用GestureLibrary来代表手势库，提供了GestureLibraries工具类来创建手势库！*

**四个加载手势库的静态方法：**

获得GestureLibraries对象后，就可以使用该对象提供的下述方法来做相应操作了：

**相关方法：**

- public void **addGesture** (String entryName, Gesture gesture)：添加一个名为entryName的手势
- public Set<String> **getGestureEntries** ()：获得手势库中所有手势的名称
- public ArrayList<Gesture> **getGestures** (String entryName)：获得entryName名称对应的全部手势
- public ArrayList<Prediction> **recognize** (Gesture gesture): 从当前手势库中识别与gesture匹配的全部手势
- public void **removeEntry** (String entryName)：删除手势库中entryName名称对应的手势
- public void **removeGesture** (String entryName, Gesture gesture)：删除手势库中entryName和gesture都匹配的手势
- public abstract boolean **save** ()：想手势库中添加手势或从中删除手势后调用该方法保存手势库

**GestureOverlayView手势编辑组件：**

Android为GestureOverlayView提供了三种监听器接口，如下，一般常用的是:**OnGesturePerformedListener**;用于手势完成时提供响应！

![img](https://www.runoob.com/wp-content/uploads/2015/07/55343440.jpg)

