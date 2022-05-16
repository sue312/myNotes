<h1><center>Gson</center></h1>

Gson是谷歌官方推出的支持 `JSON -- Java Object` 相互转换的 Java`序列化/反序列化` 库，之前由于没有用过，所以学习一下。

#### 1. 导入Android Studio工程

```
dependencies {
    implementation 'com.google.code.gson:gson:2.8.4'
}
```

#### 2. 简单的 Java Object 序列化/反序列化

**序列化**

假如有一个`User`类，拥有 `name`, `age`, `isStudent`, `gender` 四个属性，如下：

```
User user1 = new User("sue",22,false,"男");
```

使用Gson将它序列化：

```
Gson gson = new Gson();
String json = gson.toJson(user1);
```

 输出json得到：

```
{"age":22,"gender":"男","isStudent":false,"name":"sue"}
```

**反序列化**

Gson反序列化

```
User user2 = gson.fromJson(user1, User.class);
```

user1为序列化后得到的字符串，User.class为序列化前的类型。

`System.out.println("name:"+user2.getName()+" age:"+user2.getAge()); `得到：

```
name:sue age:22
```

#### 3. 嵌套 Java Object 的序列化/反序列化

**序列化**

假如有一个`User`类，拥有 `name`, `age`, `isStudent`, `gender`，`job` 五个属性，如下：

```
Job job1 = new Job("ningbo","sagereal");
user2 = new User("sue",22,false,"男",job1);
```

使用Gson将它序列化：

```
Gson gson = new Gson();
String json = gson.toJson(user1);
```

 输出json得到：

```
{"age":22,"gender":"男","isStudent":false,"job":{"city":"ningbo","company":"sagereal"},"name":"sue"}
```

**反序列化**

Gson反序列化

```
User user2 = gson.fromJson(user1, User.class);
```

`System.out.println("name:"+user2.getName()+" age:"+user2.getAge()+" job:"+user2.getJob()); `得到：

```
name:sue age:22 job:Job{city='ningbo', company='sagereal'}
```

> 注：此处重写了Job的toString方法

#### 4. Array 和 List 的序列化/反序列化

**序列化**

新建一个ArrayList集合：

```
ArrayList arrayList = new ArrayList();
arrayList.add("jack");
arrayList.add("sue");
arrayList.add("mary");
arrayList.add("zhangsan");
arrayList.add("lisi");
System.out.println(arrayList);
```

输出得到：

```
[jack, sue, mary, zhangsan, lisi]
```

使用Gson将它序列化：

```
Gson gson2 = new Gson();
String json2 = gson2.toJson(arrayList);
System.out.println(json2);
```

输出得到：

```
["jack","sue","mary","zhangsan","lisi"]
```

**反序列化**

然后使用Gson去反序列化它：

```
String[] nameArray = gson2.fromJson(json2, String[].class);
for (int i = 0; i < nameArray.length; i++) {
	System.out.println(nameArray[i]);
}
```

> 注：序列化前的类型改为String[]

输出得到：

```
jack
sue
mary
zhangsan
lisi
```

其实这和 **2** 里面介绍的反序列化方法仍然是一样的。可以看到，Gson的反序列化都是调用 `Gson.fromJson(...)`方法，传入JSON字符串，以及这段JSON字符串对应的Object类型。

**List的反序列化**

```
String userJson = "[{\"age\":22,\"gender\":\"男\",\"isStudent\":false,\"job\":{\"city\":\"ningbo\",\"company\":\"sagereal\"},\"name\":\"sue\"}]";

TypeToken tt = new TypeToken<ArrayList<User>>(){};
Type userListType = tt.getType();
List<User> userList = gson2.fromJson(userJson, userListType);
```

> 注：序列化前的类型改为TypeToken<ArrayList<User>>(){}

![](/home/wangxin/Pictures/122801.png)

#### 5. Map 和 Set 的序列化/反序列化

它们的序列化/反序列化方法和List都是一样的，反序列化的时候需要提供`Type`

#### 6. 变量值为null时的序列化/反序列化

仍然以User类为例，如果一个User对象，里面的某个值为null，那么其序列化出来后的结果会是什么样的呢？

**先看序列化**，我们先初始化一个User对象，并把其中的name,job变量赋值为null，再用Gson来序列化它，如下：

````
User user3 = new User(null,27, true, "男", null);
Gson gson = new Gson();
String userJson = gson.toJson(user3);
System.out.println(userJson);
````

输出结果：

```
{"age":27,"gender":"男","isStudent":true}
```

可见，当某个变量值为null时，Gson在序列化的时候直接把这个变量忽略了。

**反序列化**

```
User user4 = gson.fromJson(userJson, User.class);
System.out.println("name:"+user4.getName()+" job:"+user4.getJob());
```

输出结果：

```
name:null job:null
```

> 对于JSON字符串里没有的变量，Gson在反序列化时会给它一个默认值，int类型默认为0，bool类型默认为false，String类型默认为null。

#### 7. 控制序列化/反序列化 的变量名称

仍然以User对象为例，目前User对象里有五个变量`name`, `age`, `isStudent`, `gender`，`job`。假如，某一天，JSON字符串的变量名name变成了fullName，无需紧张，我们不用把User类里的变量name改为fullName，然后把它的get和set方法都改了，然后把用到get/set方法的地方全改过来。只需要用Gson提供的注解方法@SerializedName就行了，如下：

```
public class User {
    @SerializedName("fullName")
    private String name;
    private int age;
    private boolean isStudent;
    private String gender;
    private Job job;
```

> 注：只有JSON字符串里的变量名改为`fullName`，反序列化后`fullName`的值会映射给`name`。

