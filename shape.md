<h1><center>shape</center></h1>

1、shape属性

shape属性示例：

```
<?xml version="1.0" encoding="utf-8"?>
<shape
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape=["rectangle" | "oval" | "line" | "ring"] > // 定义形状
    <corners //圆角属性
        android:radius="integer"
        android:topLeftRadius="integer"
        android:topRightRadius="integer"
        android:bottomLeftRadius="integer"
        android:bottomRightRadius="integer" />
    <gradient //渐变属性
        android:angle="integer"
        android:centerX="integer"
        android:centerY="integer"
        android:centerColor="integer"
        android:endColor="color"
        android:gradientRadius="integer"
        android:startColor="color"
        android:type=["linear" | "radial" | "sweep"]
        android:useLevel=["true" | "false"] />
    <padding //边距属性
        android:left="integer"
        android:top="integer"
        android:right="integer"
        android:bottom="integer" />
    <size //大小属性
        android:width="integer"
        android:height="integer" />
    <solid //填充属性
        android:color="color" />
    <stroke //描边属性
        android:width="integer"
        android:color="color"
        android:dashWidth="integer"
        android:dashGap="integer" />
</shape>
```

1）基本属性
Shape可以定义控件的一些展示效果，例如圆角，渐变，填充，描边，大小，边距；shape子标签就可以实现这些效果，shape子标签有下面几个属性：

corners，

gradient，

padding，

size，

solid，

stroke：

corners（圆角）

<corners>是用来字义圆角；

```
<?xml version="1.0" encoding="utf-8"?> 
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
  <corners    //定义圆角   
    android:radius="10dp"  //全部的圆角半径；   
    android:topLeftRadius="5dp"  //左上角的圆角半径；   
    android:topRightRadius="5dp" //右上角的圆角半径；   
    android:bottomLeftRadius="5dp"  //左下角的圆角半径；   
    android:bottomRightRadius="5dp" /> //右下角的圆角半径。
</shape>    
```

solid（填充色）

<solid>是用以指定内部填充色；

```
<?xml version="1.0" encoding="utf-8"?> 
<shape xmlns:android="http://schemas.android.com/apk/res/android" > 
    <solid android:color="#ffff00"/> //内部填充色
</shape> 
```

gradient（渐变）

<gradient>用以定义渐变色，可以定义两色渐变和三色渐变，及渐变样式；

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
  <gradient  
    android:type=["linear" | "radial" | "sweep"]   //共有3中渐变类型，线性渐变（默认）/放射渐变/扫描式渐变；
    android:angle="90"  //渐变角度，必须为45的倍数，0为从左到右，90为从上到下；   
    android:centerX="0.5"  //渐变中心X的相当位置，范围为0～1；
    android:centerY="0.5"  //渐变中心Y的相当位置，范围为0～1；   
    android:startColor="#24e9f2"  //渐变开始点的颜色；   
    android:centerColor="#2564ef" //渐变中间点的颜色，在开始与结束点之间；   
    android:endColor="#25f1ef"  //渐变结束点的颜色；   
    android:gradientRadius="5dp"  //渐变的半径，只有当渐变类型为radial时才能使用；   
    android:useLevel="false" /> //使用LevelListDrawable时就要设置为true。设为false时才有渐变效果。  
</shape> 
```

stroke（描边）

<stroke>是描边属性，可以定义描边的宽度，颜色，虚实线等；

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
  <stroke        
    android:width="1dp"   //描边的宽度   
    android:color="#ff0000"   //描边的颜色   
    // 以下两个属性设置虚线   
    android:dashWidth="1dp" //虚线的宽度，值为0时是实线   
    android:dashGap="1dp" />//虚线的间隔
</shape> 
```

padding（内边距）

<padding>是用来定义内部边距；

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
  <padding    
    android:left="10dp"  //左内边距； 
    android:top="10dp"   //上内边距；
    android:right="10dp"   //右内边距；
    android:bottom="10dp" /> //下内边距。
</shape>
```

size（大小）

<size>标签是用来定义图形的大小的；

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
  <size   
    android:width="50dp"  //宽度 
    android:height="50dp" />// 高度
</shape>
```

2）特殊属性
Shape可以定义当前Shape的形状的，比如矩形，椭圆形，线形和环形；这些都是通过shape标签属性来定义的，shape标签有下面几个属性：rectangle，oval，line，ring：

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" 
  android:shape=["rectangle" | "oval" | "line" | "ring"] //shape的形状，默认为矩形，可以设置为矩形(rectangle)、椭圆形(oval)、线性形状(line)、环形(ring)   
  //下面的属性只有在android:shape="ring"时可用：   
  android:innerRadius="10dp" //  内环的半径； 
  android:innerRadiusRatio="2"  // 浮点型，以环的宽度比率来表示内环的半径；   
  android:thickness="3dp"   // 环的厚度；   
  android:thicknessRatio="2" //  浮点型，以环的宽度比率来表示环的厚度；  
  android:useLevel="false"> //  boolean值，如果当做是LevelListDrawable使用时值为true，否则为false。
</shape>
```

rectangle（矩形）

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <solid android:color="@color/colorPrimary"/>
</shape>

```

oval（椭圆）

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">
    <solid android:color="@color/colorPrimary"/>
    <size android:height="100dp"
        android:width="100dp"/>
</shape>
```

line（线）

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="line">
    <stroke
        android:width="1dp"
         android:color="@color/colorAccent"
         android:dashGap="3dp"//虚线间距
         android:dashWidth="4dp"/>//虚线宽度
    <size android:height="3dp"/>
</shape>
```

ring（圆环）

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="ring"
    android:useLevel="false"
    android:innerRadius="20dp" // 内环的半径
    android:thickness="10dp"> // 圆环宽度
    <!--useLevel需要设置为false-->
    <solid android:color="@color/colorAccent"/>
</shape>
```

2、shape用法

1）在res/drawable下新建shape_text.xml文件；

    <?xml version="1.0" encoding="utf-8"?>
    <shape
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:shape="rectangle">
        <corners  
            android:radius="5dp"
            android:topLeftRadius="15dp"
            android:topRightRadius="15dp"
            android:bottomLeftRadius="15dp"
            android:bottomRightRadius="15dp" />
        <gradient
            android:startColor="#FF0000"
            android:endColor="#80FF00"
            android:angle="45"/> 
    
        <padding 
             android:left="10dp"
             android:top="10dp"
             android:right="10dp"
             android:bottom="10dp"/>
    
        <size
            android:width="200dp"
            android:height="200dp"/>
    
        <solid 
            android:color="#ffff9d"/>
    
        <stroke 
            android:width="2dp"
            android:color="#dcdcdc"/> 
    </shape>
2）在布局中引用shape_text.xml文件；

```
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout  
    xmlns:android="http://schemas.android.com/apk/res/android"  
    android:layout_width="match_parent"  
    android:layout_height="match_parent">  
    <TextView  
        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
        android:text="Shape设置"  
        android:background="@drawable/shape_text"  
        android:textSize="15sp"  
        android:textColor="@android:color/black"/>  
</LinearLayout>
```

