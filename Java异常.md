<h1><center>Java异常</center></h1>

#### 什么是异常？

​		异常就是有异于常态，和正常情况不一样，有错误出错。在java中，阻止当前方法或作用域的情况，称之为异常。

#### java中异常的体系是怎么样的呢？

1. Java中的所有不正常类都继承于Throwable类。Throwable主要包括两个大类，一个是Error类，另一个是Exception类；

   ![img](https://images2015.cnblogs.com/blog/1189312/201707/1189312-20170703132225237-1149163416.png)

2. 其中Error类中包括虚拟机错误和线程死锁，一旦Error出现了，程序就彻底的挂了，被称为程序终结者；

   ![img](https://images2015.cnblogs.com/blog/1189312/201707/1189312-20170703132317972-421341844.png)

3. Exception类，也就是通常所说的“异常”。主要指编码、环境、用户操作输入出现问题，Exception主要包括两大类，非检查异常（RuntimeException）和检查异常（其他的一些异常）

   ![img](https://images2015.cnblogs.com/blog/1189312/201707/1189312-20170703132540112-2053625299.png)

4. RuntimeException异常主要包括以下四种异常（其实还有很多其他异常，这里不一一列出）：空指针异常、数组下标越界异常、类型转换异常、算术异常。RuntimeException异常会由java虚拟机自动抛出并自动捕获**（就算我们没写异常捕获语句运行时也会抛出错误！！）**，此类异常的出现绝大数情况是代码本身有问题应该从逻辑上去解决并改进代码。

   ![img](https://images2015.cnblogs.com/blog/1189312/201707/1189312-20170703132648456-1067022743.png)

5. 检查异常，引起该异常的原因多种多样，比如说文件不存在、或者是连接错误等等。跟它的“兄弟”RuntimeException运行异常不同，**该异常我们必须手动在代码里添加捕获语句来处理该异常**，这也是我们学习java异常语句中主要处理的异常对象。

![img](https://images2015.cnblogs.com/blog/1189312/201707/1189312-20170703133422940-109704620.png)




### try-catch-finally语句

1. try块：负责捕获异常，一旦try中发现异常，程序的控制权将被移交给catch块中的异常处理程序。

   **【try语句块不可以独立存在，必须与 catch 或者 finally 块同存】**

2. catch块：如何处理？比如发出警告：提示、检查配置、网络连接，记录错误等。执行完catch块之后程序跳出catch块，继续执行后面的代码。

   【**编写catch块的注意事项：多个catch块处理的异常类，要按照先catch子类后catch父类的处理方式，因为会【就近处理】异常（由上自下）。**】

3. finally：最终执行的代码，用于关闭和释放资源。

   ```
   //语法格式如下
   try{
   	//一些会抛出的异常
   }catch（Exception e）{
       //第一个catch
       //处理该异常的代码块
   }catch（Exception e）{
       //第二个catch，可以有多个catch
       //处理该异常的代码块
   }finally{
   	//最终要执行的代码
   }
   ```

    **总结：**

   　　1、不管有木有出现异常或者try和catch中有返回值return，finally块中代码都会执行；

   　　2、finally中最好不要包含return，否则程序会提前退出，返回会覆盖try或catch中保存的返回值。

   　　3. e.printStackTrace()可以输出异常信息。

   　　4. return值为-1为抛出异常的习惯写法。

   　　5. 如果方法中try,catch,finally中没有返回语句，则会调用这三个语句块之外的return结果。

   　　6. finally 在try中的return之后 在返回主调函数之前执行。



### throw和throws关键字

**java中的异常抛出通常使用throw和throws关键字来实现。**

​		1、throw ----将产生的异常抛出，是抛出异常的一个**动作**。

```
public static void main(String[] args) {
    String s = "abc";
    if(s.equals("abc")) {
      throw new NumberFormatException();
    } else {
      System.out.println(s);
    }
    //function();
}
```

​		2、throws----声明将要抛出何种类型的异常（**声明**）。

```
 public void 方法名（参数列表）
    throws 异常列表{
 		//调用会抛出异常的方法或者：
 		throw new Exception（）；
 	}
```

**throw与throws的比较**

​		1、throws出现在方法函数头；而throw出现在函数体。
​		2、throws表示出现异常的一种可能性，并不一定会发生这些异常；throw则是抛出了异常，执行throw则一定抛出了某种异常对象。
​		3、两者都是消极处理异常的方式（这里的消极并不是说这种方式不好），只是抛出或者可能抛出异常，但是不会由函数去处理异常，真正的处理异常由函数的上层调用处理。



**使用throw和throws关键字需要注意以下几点：**

​		1.throws的异常列表可以是抛出一条异常，也可以是抛出多条异常，每个类型的异常中间用逗号隔开

​		2.方法体中调用会抛出异常的方法或者是先抛出一个异常：用throw new Exception（） throw写在方法体里，表示“抛出异常”这个动作。

​		3.如果某个方法调用了抛出异常的方法，那么必须添加try catch语句去尝试捕获这种异常， 或者添加声明，将异常抛出给更上一层的调用者进行处理



**总结**

​		1、处理运行时异常时，采用逻辑去合理规避同时辅助try-catch处理

​		2、在多重catch块后面，可以加一个catch（Exception）来处理可能会被遗漏的异常

​		3、对于不确定的代码，也可以加上try-catch，处理潜在的异常

​		4、尽量去处理异常，切记只是简单的调用printStackTrace（）去打印

​		5、具体如何处理异常，要根据不同的业务需求和异常类型去决定

​		6、尽量添加finally语句块去释放占用的资源
