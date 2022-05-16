<h1><center>SQLite</center></h>

- ### 简介

  ​		SQLite是一款轻型的嵌入式数据库,它占用资源非常的低，在嵌入式设备中，可能只需要几百K的内存就足够了。它的处理速度比Mysql、PostgreSQL这两款著名的数据库都还快。SQLite提供一些C函数接口，可以用这些函数操作数据库。通过使用这些接口，传递一些标准 sql 语句（以 char * 类型）给 SQLite函数，SQLite就会为你操作数据库。

- ### 数据库的安装

```
本地安装：sudo dpkg -i *.deb
在线安装：sudo apt-get install sqlite3
```

- ### 数据库sql命令

```
//sql命令一定要以“;”结束
//以下以表stdinfo为例
创建数据库：sqlite3 student.db
创建表：	 create table stuinfo(id integer, name text, age integer, score float); 
插入记录：insert into stuinfo values(1, 'Andy', 30, 99.9);
         insert into stuinfo(id, name, score) values(2, 'Amy', 100);
查看记录：select * from stuinfo;
         select * from stuinfo where score = 100;
         select * from stuinfo where score = 99.9 and name = 'Andy';
         select * from stuinfo where score = 99.9 or name = 'Amy';
         select * from stuinfo where score > 80 and score < 100;
         select name,score from stuinfo; //查询指定的字段
删除记录：delete from stuinfo where id = 1 and name = 'Andy';
更新记录：update stuinfo set age = 20,score = 100 where id = 1;
增加一列：alter table stuinfo add column sex char;
删除一列：create table stu as select id, name, score from stuinfo;	//从stuinfo中复制新表（不包含要删除的列）
         drop table stuinfo; //删除老的表stuinfo
         alter table stu rename to stuinfo;	//将新表stu重命名为stuinfo,完成删除列的操作
删除表：  drop table stuinfo;
/***设置主键***/
设置主键： create table table_name(column1 datatype PRIMARY KEY, column2 datatype, ……);
//PRIMARY KEY表示该列为该表的“主关键字”，主关键字用于唯一索引表内的某一条记录，主关键字必须唯一且主关键字的列值不能为空
//一张表允许省略主关键字
```

- ### 查看手机内的sqlite

  ​		首先你需要下载一个数据库管理系统，(DB Browser for SQLite)就是一个不错的选择。然后你需要刷一个ud的版本，不然没有权限查看到手机内的数据库

  执行以下操作后就能能把某个应用的数据拿出来了，然后选择用(DB Browser for SQLite)打开就能看到数据库中的内容了。

```
//添加权限
adb root
//进入手机
adb shell
//进入数据库文件
cd data/data
ls
cd com.example.exam/databases
//查看数据表
ls
复制路径，去电脑本地建一个文件夹。
adb pull /data/data/com.example.exam/databases
```

- ### 数据储存

  ##### 一.特点

  ​	  1-轻型的关系型数据库

    	 2-支持多种编程语言

     	3-适合在移动设备中使用

     	4-安装文件小

     	5-支持多个操作系统

     	6-运行速度快(数据量小)

    	 7-数据库本身是单个文件

    	 8-存储具有一定结构的数据

    	 9-文件类型  .db

  #####  二.数据类型

     	1-整数：INT  、INTEGER；

     	2-浮点数：FLOAT  、DOUBLE  ；

     	3-字符串文本

  ​    		①CHAR，定长的字符串；

  ​    		②WARCHAR，变长的字符串；

  ​		    ③TEXT，大文本；

     	4-时间日期：DATE、DATETIME；

    	 5-文件：BLOB，不常用，影响查询效率。
