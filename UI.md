<h1><center>UI</center></h1>

## 定义几种颜色（res/values/colors.xml）

使用颜色资源，可以方便地在一处定义各种颜色值，然后在整个应用里引用。

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="purple_200">#FFBB86FC</color>
    <color name="purple_500">#FF6200EE</color>
    <color name="purple_700">#FF3700B3</color>
    <color name="teal_200">#FF03DAC5</color>
    <color name="teal_700">#FF018786</color>
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>

</resources>
```

## 添加样式（res/values/styles.xml）

样式可以为很多组件共用，更新修改属性时，只修改公共样式定义就行了。

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<!-- 系统样式 -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- 工具栏 -->
        <item name="colorPrimary">#AB5ACD</item>
        <!-- 屏幕顶部的状态栏 -->
        <item name="colorPrimaryDark">#7A7A7A</item>
        <!-- 主题色 (给 EditText 这样的组件着色)-->
        <item name="colorAccent">@color/colorAccent</item>
        <!-- 背景 -->
        <item name="android:windowBackground">@drawable/back1</item>
        <!-- 按钮 -->
        <item name="buttonStyle">@style/BeatBoxButton.Strong</item>
    </style>

	<!-- 自定义样式 -->
    <style name="BeatBoxButton" parent="Widget.AppCompat.Button">
        <item name="android:background">?android:attr/textColorHintInverse</item>
    </style>
    <style name="BeatBoxButton.Strong">
        <item name="android:textStyle">bold</item>
    </style>

</resources>
```

#### 使用样式的方法：

> ***style="@style/BeatBoxButton"***

 系统样式(主题)会应用于整个系统，自定义样式会应用于调用它的地方。

### 样式继承

BeatBoxButton.Strong 的 命 名 表 明 ， 这 个 新 样 式 继 承 了BeatBoxButton 样式的属性。

也可以采用指定父样式的方式：

> <style name="StrongBeatBoxButton" parent="@style/BeatBoxButton">

### 在代码中使用主题属性

```
Resources.Theme theme = getActivity().getTheme();
int[] attrsToFetch = { R.attr.colorAccent };
TypedArray a = theme.obtainStyledAttributes(R.style.AppTheme, attrsToFetch);
int accentColor = a.getInt(0, 0);
a.recycle();
```

先取得 Theme 对象，然后要求它找到定义在 AppTheme （ 即 R.style.AppTheme） 中的 R.attr.colorAccent 属性。结果得到一个持有数据的 TypedArray 对象。接着，向 TypedArray 对象索要int 值以取出颜色。颜色值取出之后就可以使用了，比如，用来更改按钮背景色。