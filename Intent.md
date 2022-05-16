<h1><center>Intent</center></h1>

### 一、什么是Intent

#### 1、Intent的概念：

- Android中提供了Intent机制来协助应用间的交互与通讯，或者采用更准确的说法是，Intent不仅可用于应用程序之间，也可用于应用程序内部的activity, service和broadcast receiver之间的交互。Intent这个英语单词的本意是“目的、意向、意图”。
- Intent是一种运行时绑定（runtime binding)机制，它能在程序运行的过程中连接两个不同的组件。通过Intent，你的程序可以向Android表达某种请求或者意愿，Android会根据意愿的内容选择适当的组件来响应。

activity、service和broadcast receiver之间是通过Intent进行通信的，而另外一个组件Content Provider本身就是一种通信机制，不需要通过Intent。我们来看下面这个图就知道了：

![](/home/wangxin/Pictures/typora001.png)

如果Activity1需要和Activity2进行联系，二者不需要直接联系，而是通过Intent作为桥梁。通俗来讲，Intnet类似于中介、媒婆的角色。

#### 2、对于向这三种组件发送intent有不同的机制：

- 使用Context.startActivity() 或 Activity.startActivityForResult()，传入一个intent来启动一个activity。使用 Activity.setResult()，传入一个intent来从activity中返回结果。

- 将intent对象传给Context.startService()来启动一个service或者传消息给一个运行的service。将intent对象传给 Context.bindService()来绑定一个service。

- 将intent对象传给 Context.sendBroadcast()，Context.sendOrderedBroadcast()，或者Context.sendStickyBroadcast()等广播方法，则它们被传给 broadcast receiver。

### 二、Intent的相关属性：

Intent由以下各个组成部分：

- component(组件)：目的组件
- action（动作）：用来表现意图的行动
- category（类别）：用来表现动作的类别
- data（数据）：表示与动作要操纵的数据
- type（数据类型）：对于data范例的描写
- extras（扩展信息）：扩展信息
- Flags（标志位）：期望这个意图的运行模式

Intent类型分为显式Intent（直接类型）、隐式Intent（间接类型）。官方建议使用隐式Intent。上述属性中，component属性为直接类型，其他均为间接类型。

相比与显式Intent，隐式Intnet则含蓄了许多，它并不明确指出我们想要启动哪一个活动，而是指定一系列更为抽象的action和category等信息，然后交由系统去分析这个Intent，并帮我们找出合适的活动去启动。

**Activity 中 Intent Filter 的匹配过程 ：**

![](/home/wangxin/Pictures/typora002.png)

#### 1、component(组件)：目的组件

Component属性明确指定Intent的目标组件的类名称。（属于直接Intent）

如果 component这个属性有指定的话，将直接使用它指定的组件。指定了这个属性以后，Intent的其它所有属性都是可选的。

> 例如，启动第二个Activity时，我们可以这样来写：
>
> ```
> 		button1.setOnClickListener(new OnClickListener() {
>             @Override
>             public void onClick(View v) {
>                 //创建一个意图对象
>                 Intent intent = new Intent();
>                 //创建组件，通过组件来响应
>                 ComponentName component = new ComponentName(MainActivity.this, SecondActivity.class);
>                 intent.setComponent(component);
>                 startActivity(intent);
>             }
>         });
> ```
>
> 如果写的简单一点，监听事件onClick()方法里可以这样写：
>
> ```
> Intent intent = new Intent();
> //setClass函数的第一个参数是一个Context对象
> //Context是一个类，Activity是Context类的子类，也就是说，所有的Activity对象，都可以向上转型为Context对象
> //setClass函数的第二个参数是一个Class对象，在当前场景下，应该传入需要被启动的Activity类的class对象
> intent.setClass(MainActivity.this, SecondActivity.class);
> startActivity(intent);
> ```
>
> 再简单一点，可以这样写：（当然，也是最常见的写法）
>
> ```
> Intent intent = new Intent(MainActivity.this,SecondActivity.class);
> startActivity(intent);
> ```

#### 2、Action（动作）：用来表现意图的行动

当日常生活中，描述一个意愿或愿望的时候，总是有一个动词在其中。比如：我想“做”三个俯卧撑；我要“写” 一封情书，等等。在Intent中，Action就是描述做、写等动作的，当你指明了一个Action，执行者就会依照这个动作的指示，接受相关输入，表现对应行为，产生符合的输出。在Intent类中，定义了一批量的动作，比如ACTION_VIEW，ACTION_PICK等， 基本涵盖了常用动作。加的动作越多，越精确。

Action 是一个用户定义的字符串，用于描述一个 Android 应用程序组件，一个 Intent Filter 可以包含多个 Action。在 AndroidManifest.xml 的Activity 定义时，可以在其 <intent-filter >节点指定一个 Action列表用于标识 Activity 所能接受的“动作”。

#### 3、category（类别）：用来表现动作的类别

> Category属性也是作为<intent-filter>子元素来声明的。例如：
>
> ```
> <intent-filter>
> 　　<action android:name="com.vince.intent.MY_ACTION"></action>
> 　　<category android:name="com.vince.intent.MY_CATEGORY"></category> 
> 　　<category android:name="android.intent.category.DEFAULT"></category> 
> </intent-filter>  
> 
> <intent-filter>
> 	<action android:name="android.intent.action.MAIN" />
> 	<category android:name="android.intent.category.LAUNCHER" />
> </intent-filter>
> ```
>
> Action 和category通常是放在一起用的

如果在其他的Acticity通过这个action的条件来查找，那SecondActicity就具备了这个条件。类似于相亲时，我要求对方有哪些条件，然后对方这个SecondActicity恰巧满足了这个条件（够通俗了吧）。

> 注：如果没有指定的category，则必须使用默认的DEFAULT（即上方第5行代码）。

也就是说：只有<action>和<category>中的内容同时能够匹配上Intent中指定的action和category时，这个活动才能响应Intent。如果使用的是DEFAULT这种默认的category，在稍后调用startActivity()方法的时候会自动将这个category添加到Intent中。

> ````
> button1.setOnClickListener(new OnClickListener() {
> 	@Override
> 	public void onClick(View v) {
> 		//启动另一个Activity，（通过action属性进行查找）
> 		Intent intent = new Intent();
> 		//设置动作（实际action属性就是一个字符串标记而已）
> 		intent.setAction("com.example.smyh006intent01.MY_ACTION"); //方法：Intent android.content.Intent.setAction(String action)
> 		startActivity(intent);
> 	}
> });
> ````
>
> 上方代码中，也可以换成下面这种简洁的方式：
>
> ```
> //启动另一个Activity，（通过action属性进行查找）
> Intent intent = new Intent("com.google.android.apps.photos.CREATE_LA");//方法： android.content.Intent.Intent(String action)
> startActivity(intent);
> ```
>

在这个Intent中，我并没有指定具体哪一个Activity，我只是指定了一个action的常量。所以说，隐式Intent的作用就表现的淋漓尽致了。此时，点击MainActicity中的按钮，就会跳到SecondActicity中去。

上述情况只有SecondActicity匹配成功。如果有多个组件匹配成功，就会以对话框列表的方式让用户进行选择。

此时，运行程序，当点击MainActivity中的按钮时，弹出如下界面：

![](/home/wangxin/Pictures/typora003.png)

相信大家看到了这个界面，应该就一目了然了。于是我们可以做出如下总结：

在自定义动作时，使用activity组件时，必须添加一个默认的类别

> 如果有多个组件被匹配成功，就会以对话框列表的方式让用户进行选择。
>
> 每个Intent中只能指定一个action，但却能指定多个category；类别越多，动作越具体，意图越明确（类似于相亲时，给对方提了很多要求）。



> 目前我们的Intent中只有一个默认的category，现在可以通过intent.addCategory()方法来实现。修改MainActivity中按钮的点击事件，代码如下：
>
> ```
> button1.setOnClickListener(new OnClickListener() {
> 	@Override
> 	public void onClick(View v) {
> 		//启动另一个Activity，（通过action属性进行查找）
> 		Intent intent = new Intent();
> 		//设置动作（实际action属性就是一个字符串标记而已）
> 		intent.setAction("com.example.smyh006intent01.MY_ACTION"); //方法：Intent android.content.Intent.setAction(String action)
> 		//添加类别
> 		intent.addCategory("com.example.smyh006intent01.MY_CATEGORY");
> 		startActivity(intent);
> 	}
> });
> ```
>
> 既然在Intent中增加了一个category，那么我们要在清单文件中去声明这个category，不然程序将无法运行。代码如下：
>
> ```
> <android:name=".SecondActivity">
> 	<intent-filter>
>         <action android:name="com.example.smyh006intent01.MY_ACTION"/>
>         <category android:name="android.intent.category.DEFAULT" />
>         <category android:name="com.example.smyh006intent01.MY_CATEGORY" />
> 	</intent-filter>
> </activity>
> ```
>
> 此时，点击MainActicity中的按钮，就会跳到SecondActicity中去。
>
> 总结如下：
>
> > 自定义类别： 在Intent添加类别可以添加多个类别，那就要求被匹配的组件必须同时满足这多个类别，才能匹配成功。操作Activity的时候，如果没有类别，须加上默认类别

#### 4、data（数据）：表示与动作要操纵的数据

- Data属性是Android要访问的数据，和action和Category声明方式相同，也是在<intent-filter>中。
- 多个组件匹配成功显示优先级高的； 相同显示列表。

Data是用一个uri对象来表示的，uri代表数据的地址，属于一种标识符。通常情况下，我们使用action+data属性的组合来描述一个意图：做什么

使用隐式Intent，我们不仅可以启动自己程序内的活动，还可以启动其他程序的活动，这使得Android多个应用程序之间的功能共享成为了可能。比如应用程序中需要展示一个网页，没有必要自己去实现一个浏览器（事实上也不太可能），而是只需要条用系统的浏览器来打开这个网页就行了。

> 【实例】打开指定网页：
>
> MainActivity.java中，监听器部分的核心代码如下：
>
> ````
> button1.setOnClickListener(new OnClickListener() {
>     @Override
>     public void onClick(View v) {
>         Intent intent = new Intent();
>         intent.setAction(Intent.ACTION_VIEW);
>         Uri data = Uri.parse("http://www.baidu.com");
>         intent.setData(data);
>         startActivity(intent);
> 	}
> });
> ````
>
> 当然，上方代码也可以简写成：
>
> ```
> button1.setOnClickListener(new OnClickListener() {
>     @Override
>     public void onClick(View v) {
>         Intent intent = new Intent(Intent.ACTION_VIEW);
>         intent.setData(Uri.parse("http://www.baidu.com"));
>         startActivity(intent);
> 	}
> });
> ```
>
> 指定了Intent的action是 **Intent.ACTION_VIEW**，表示查看的意思，这是一个Android系统内置的动作；
>
> 通过Uri.parse()方法，将一个网址字符串解析成一个Uri对象，再调用intent的setData()方法将这个Uri对象传递进去。

> 打开某张照片
>
> ```
> Intent intent = new Intent(Intent.ACTION_VIEW);
> Uri data = Uri.parse("content://media/external/images/media/");
> long thumbNailsId = 449;
> data = ContentUris.withAppendedId(data, thumbNailsId);
> intent.setData(data);
> startActivity(intent);
> ```
>
> thumbNailsId:某张照片对应的id
>
> 通过Uri.parse()方法，将照片地址解析成一个Uri对象，再调用intent的setData()方法将这个Uri对象传递进去。

> 注：Data属性的声明中要指定访问数据的Uri和MIME类型。可以在<data>元素中通过一些属性来设置：
>
> android:scheme、android:path、android:port、android:mimeType、android:host等，通过这些属性来对应一个典型的Uri格式scheme://host:port/path。例如：http://www.google.com。

- 当Intent匹配成功的组件有多个时，显示优先级高的组件，如果优先级相同，显示列表让用户自己选择
- 优先级从-1000至1000，并且其中一个必须为负的才有效
- 通过来android:priority设置优先级 

> ```
> <intent-filter android:priority="-1">
> ```

#### 5、type（数据类型）：对于data范例的描写

如果Intent对象中既包含Uri又包含Type，那么，在<intent-filter>中也必须二者都包含才能通过测试。

Type属性用于明确指定Data属性的数据类型或MIME类型，但是通常来说，当Intent不指定Data属性时，Type属性才会起作用，否则Android系统将会根据Data属性值来分析数据的类型，所以无需指定Type属性。

data和type属性一般只需要一个，通过setData方法会把type属性设置为null，相反设置setType方法会把data设置为null，如果想要两个属性同时设置，要使用Intent.setDataAndType()方法。

> 【实例】：播放指定路径的mp3文件。
>
> MainActivity.java中按钮监听事件部分的代码如下：
>
> ```
> button.setOnClickListener(new OnClickListener(){
>     @Override
>     public void onClick(View v) {
>         Intent intent = new Intent();
>         intent.setAction(Intent.ACTION_VIEW);
>         Uri data = Uri.parse("file:///storage/sdcard0/平凡之路.mp3");
>         //设置data+type属性
>         intent.setDataAndType(data, "audio/mp3"); //方法：Intent android.content.Intent.setDataAndType(Uri data, String type)
>         startActivity(intent);
> 	}
> });
> ```
>
> "file://"表示查找文件，后面再加上我的小米手机存储卡的路径：/storage/sdcard0，再加上具体歌曲的路径。
>
> intent.setDataAndType:设置data+type属性

#### 6、extras（扩展信息）：扩展信息

是其它所有附加信息的集合。使用extras可以为组件提供扩展信息，比如，如果要执行“发送电子邮件”这个

动作，可以将电子邮件的标题、正文等保存在extras里，传给电子邮件发送组件。

#### 7、Flags（标志位）：期望这个意图的运行模式

一个程序启动后系统会为这个程序分配一个task供其使用，另外同一个task里面可以拥有不同应用程序的activity。那么，同一个程序能不能拥有多个task？这就涉及到加载activity的启动模式，这个需要单独讲一下。

> 注：android中一组逻辑上在一起的activity被叫做task，自己认为可以理解成一个activity堆栈。







````
    @Override
    protected void onResume() {
        super.onResume();
        //检查写权限
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(this, PERMISSIONS_STORAGE, REQUEST_PERMISSION_CODE);
        }
        //检查电话权限
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.CALL_PHONE) != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(this, PERMISSIONS_PHONE, REQUEST_PERMISSION_CODE);
        }
        //检查电话权限
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.RECORD_AUDIO) != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(this, PERMISSIONS_MICROPHONE, REQUEST_PERMISSION_CODE);
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
        //通过requestCode来识别是否同一个请求
        if (requestCode == REQUEST_PERMISSION_CODE) {
            for (int i = 0; i < permissions.length; i++) {
                Log.d("MainActivity1", "申请的权限为：" + permissions[i] + ",申请结果：" + grantResults[i]);
            }
        }
    }
````

