<h1><center>Fragment</center></h1>

### 一、基本概念

Fragment，简称碎片，是Android 3.0（API 11）提出的，为了兼容低版本，support-v4库中也开发了一套Fragment API，最低兼容Android 1.6。

过去support-v4库是一个jar包，24.2.0版本开始，将support-v4库模块化为多个jar包，包含：support-fragment, support-ui, support-media-compat等，这么做是为了减少APK包大小，你需要用哪个模块就引入哪个模块。

如果想引入整个support-v4库，则`compile 'com.android.support:support-v4:24.2.1'`，如果只想引入support-fragment库，则`com.android.support:support-fragment:24.2.1`。

> ```
> 因为support库是不断更新的，因此建议使用support库中的android.support.v4.app.Fragment，而不要用系统自带的android.app.Fragment。而如果要使用support库的Fragment，Activity必须要继承FragmentActivity（AppCompatActivity是FragmentActivity的子类）
> ```

Fragment官方的定义是：

> A Fragment represents a behavior or a portion of user interface in an Activity. You can combine multiple fragments in a single activity to build a multi-pane UI and reuse a fragment in multiple activities. You can think of a fragment as a modular section of an activity, which has its own lifecycle, receives its own input events, and which you can add or remove while the activity is running.

**根据上面的定义可知：**

- Fragment是依赖于Activity的，不能独立存在的。
- 一个Activity里可以有多个Fragment。
- 一个Fragment可以被多个Activity重用。
- Fragment有自己的生命周期，并能接收输入事件。
- 我们能在Activity运行时动态地添加或删除Fragment。

**Fragment的优势有以下几点：**

- 模块化（Modularity）：我们不必把所有代码全部写在Activity中，而是把代码写在各自的Fragment中。
- 可重用（Reusability）：多个Activity可以重用一个Fragment。
- 可适配（Adaptability）：根据硬件的屏幕尺寸、屏幕方向，能够方便地实现不同的布局，这样用户体验更好。

**Fragment核心的类有：**

- Fragment：Fragment的基类，任何创建的Fragment都需要继承该类。
- FragmentManager：管理和维护Fragment。他是抽象类，具体的实现类是FragmentManagerImpl。
- FragmentTransaction：对Fragment的添加、删除等操作都需要通过事务方式进行。他是抽象类，具体的实现类是BackStackRecord。

> Nested Fragment（Fragment内部嵌套Fragment的能力）是Android 4.2提出的，support-fragment库可以兼容到1.6。通过`getChildFragmentManager()`能够获得子Fragment的FragmentManager，在子Fragment中可以通过`getParentFragment()`获得父Fragment。

### 二、基本使用

这里给出Fragment最基本的使用方式。首先，创建继承Fragment的类，名为Fragment1：

```
public class Fragment1 extends Fragment {
    private static String ARG_PARAM = "param_key";
    private String mParam;
    private Activity mActivity;
    public void onAttach(Context context) {
        super.onAttach(context);
        mActivity = (Activity) context;
        mParam = getArguments().getString(ARG_PARAM);  //获取参数
    }
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View root = inflater.inflate(R.layout.fragment_1, container, false);
        TextView view = root.findViewById(R.id.text);
        view.setText(mParam);
        return root;
    }
    public static Fragment1 newInstance(String str) {
        Fragment1 fragment = new Fragment1();
        Bundle bundle = new Bundle();
        bundle.putString(ARG_PARAM, str);
        fragment.setArguments(bundle);   //设置参数
        return fragment;
    }
}
```

Fragment有很多可以复写的方法，其中最常用的就是`onCreateView()`，该方法返回Fragment的UI布局，需要注意的是`inflate()`的第三个参数是false，因为在Fragment内部实现中，会把该布局添加到container中，如果设为true，那么就会重复做两次添加，则会抛如下异常：

```
Caused by: java.lang.IllegalStateException: The specified child already has a parent. You must call removeView() on the child's parent first.
```

如果在创建Fragment时要传入参数，必须要通过`setArguments(Bundle bundle)`方式添加，而不建议通过为Fragment添加带参数的构造函数，因为通过`setArguments()`方式添加，在由于内存紧张导致Fragment被系统杀掉并恢复（re-instantiate）时能保留这些数据。官方建议如下：

> It is strongly recommended that subclasses do not have other constructors with parameters, since these constructors will not be called when the fragment is re-instantiated.

我们可以在Fragment的`onAttach()`中通过`getArguments()`获得传进来的参数，并在之后使用这些参数。如果要获取Activity对象，不建议调用`getActivity()`，而是在`onAttach()`中将Context对象强转为Activity对象。



创建完Fragment后，接下来就是**把Fragment添加到Activity中**。在Activity中添加Fragment的方式有两种：

- 静态添加：在xml中通过`<fragment>`的方式添加，缺点是一旦添加就不能在运行时删除。
- 动态添加：运行时添加，这种方式比较灵活，因此建议使用这种方式。

虽然Fragment能在XML中添加，但是这只是一个语法糖而已，Fragment并不是一个View，而是和Activity同一层次的。



**动态添加的方式：**

首先Activity需要有一个容器存放Fragment，一般是FrameLayout，因此在Activity的布局文件中加入FrameLayout：

```
<FrameLayout
	android:id="@+id/container"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
/>
```

然后在`onCreate()`中，通过以下代码将Fragment添加进Activity中。

```
if (bundle == null) {
	getSupportFragmentManager().beginTransaction()
        .add(R.id.container, Fragment1.newInstance("hello world"), "f1")
        //.addToBackStack("fname")
        .commit();
}
```

```
fram = new Fram(this);
FragmentTransaction transaction = getFragmentManager().beginTransaction();
transaction.replace(R.id.fr_1, fram);//替换
//transaction.add(R.id.fr_1,fram,"f1");//添加
transaction.commit();
```

```
getFragmentManager().beginTransaction().replace(R.id.fr_1,fragment1).commitNowAllowingStateLoss();
```

### Fragment生命周期

解释如下：

- onAttach()：Fragment和Activity相关联时调用。可以通过该方法获取Activity引用，还可以通过getArguments()获取参数。
- onCreate()：Fragment被创建时调用。
- onCreateView()：创建Fragment的布局。
- onActivityCreated()：当Activity完成onCreate()时调用。
- onStart()：当Fragment可见时调用。
- onResume()：当Fragment可见且可交互时调用。
- onPause()：当Fragment不可交互但可见时调用。
- onStop()：当Fragment不可见时调用。
- onDestroyView()：当Fragment的UI从视图结构中移除时调用。
- onDestroy()：销毁Fragment时调用。
- onDetach()：当Fragment和Activity解除关联时调用。