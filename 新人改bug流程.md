<h1><center>学习日报</center></h1>

### 一、学习内容

新人改bug流程

步骤：

1.克隆代码，在Gerrit中克隆下对应平台代码后,切换到对应的分支(项目1)

假如拉o平台的代码：

终端输入对应指令：

git clone ssh://your name@192.168.3.79:29418/O1_MP1/alps-release-o1.mp1-default.git  克隆代码

git branch -a  查看所有远程分支

git checkout xxx   切换分支 xxx代表对应分支名称



备注：

需要clone两份代码，一份代码用来编译(项目1)，项目1放在固态硬盘，另一份代码用来提交(项目2)，项目2放在机械硬盘。在项目1下面修改好bug，然后用对比工具(compare)把修改好的代码从项目1移植到到项目2，最后，在项目2中提交代码到服务器。



2.切换分支，编译代码(项目1)

切完分支后，不要修改任何内容，先编译一遍软件

编译指令:

./mk l 查看当前分支下的项目

./mk -u  项目名 new    编译一版user软件

./mk -ud 项目名 new   编译一版userdebug软件,ud软件可以方便开发人员调试



3.编译完成，修改代码，解决问题(项目1)

解决问题途径：

(1).在Gerrit平台上搜索解决方案

Gerrit o平台地址:http://192.168.3.79:8084/#/q/status:open

Gerrit p平台地址:http://192.168.3.79:8084/#/dashboard/self

Gerrit q平台地址:http://192.168.3.78:7080/q/status:open

(2).在wcp上查找相关知识

http://192.168.3.77:8989/wcp/login/webPage.html

(3).询问师傅、公司老人

(4).自身分析

利用查找关键字的方法，慢慢寻找关键代码，读懂代码，解决问题等



4.修改代码后，编译软件，验证是否已经解决问题(项目1)

指令：

./mk l 查看当前分支下的项目

如果你仅仅修改了alps目录下的代码，可以用r编译

./mk -ud 项目名 r

./mk -u 项目名 r

如果你修改了alps目录外的代码，如sagereal目录等，那么需要R编译

./mk -ud 项目名 R

./mk -u  项目名 R

当然，如果你因为其他情况，clean了代码，那么则需要new编译

./mk -ud 项目名 new

./mk -u  项目名 new



备注：

(1).此时再次编译代码（首次是user编译，则第二次也要user编译，ud同理）

(2).我们的调试方法有模块调试(mm),r(remake),R,new 四种方式,mm的作用范围是修改的所在包必须有Android.mk文件才可以使用,否则不可以使用,r和R的区别还是作用范围不同,r是编译整个alps目录下的所有东西,这时如果你修改了sagereal下的东西,是编译不到的,就需要我们使用R/new 的方式来编译

(3).软件编译程度：new(n或者N)>mm>R>r ，程度越高，花费的时间越多，编译的越彻底



5.编译完代码，将编译生成的刷机包刷进手机，进行验证

MTK使用SP_Flash_Tool_exe_Windows_v5.1944.00.000等工具进行刷机验证

展讯使用 ResearchDownload_R24.0.0003等工具刷机验证

刷机成功后，打开手机，找到任务需要修改的地方，查看是否已经修改好



6.验证成功，解决问题后，需要移植代码(项目1)

在手机上确认自己的bug已经修改成功，那么则使用对比工具(compare)，把修改过的代码从项目1移植到到项目2



7.移植成功，提交代码(项目2)

提交代码指令:

git pull

(1)、git status //查看项目修改的文件
(2)、git diff [文件路径]  //查看对应文件的修改点，可以使用.代替文件路径，则列出所有的修改点
(3)、git add [文件路径] / //确认修改点无误后添加修改的文件，可以添加多次，将所有修改的文件都添加进去
(4)、git status //查看是否将修改点全部添加进去
(5)、git commit -m "提交的标题" //提交到本地，提交标题一般格式为"test bug号 自己的名字 修改信息"

例子: git commit -m "test 33083 huangtongjun modify verno
(6)、git push origin HEAD:refs/for/分支名 //将代码从本地提交到远程服务器

备注：

(1).我们看到的项目目录即是工作区，使用add命令，则是将修改的代码从工作区更新到了暂存区(项目下的隐藏目录， .git目录下的index文件)，使用commit命令，则是将修改的代码从暂存区更新到了本地仓库(项目下的隐藏目录，.git目录下的objects目录)，使用push命令，则是将代码从本地仓库更新到了远程仓库(服务器)

(2).可以多次add，再一次commit提交到本地，但是只能一次commit ， 再一次push到服务器

(3).撤销最近的一次提交(push到了远程仓库)：  git reset HEAD^

如果撤销最近的五次提交，则git reset HEAD^^^^^



8.提交代码后，需要在Gerrit平台上添加代码审核者

Gerrit o平台地址：http://192.168.3.79:8084/#/dashboard/self

点击My-->Changes>--点击自己刚才提交的代码-->进入提交代码的详细页面

点击页面左下角的ADD REVIEWER按钮-->Reviewers选择组长-->Code-Review 选择+2-->再点击send

然后告诉你提交的审核者，让他给你合并代码



9.添加完审核者后，需要在我的工作台上提交Bug

进入我的工作台：http://192.168.3.78:8078/my/page

选中刚才修改的那个Bug，打开页面，选择编辑

状态改为Fixed

指派给测试负责人

完成改为100%

解决者改为自己

修改情况改为 http://192.168.3.78:7080/c/Q0_MP1/alps-release-q0.mp1-default/+/22954 这就是提交到服务器的那条提交的地址

其他信息，按实际情况填写

最后，点击提交



ok，一切都完成了



### 二、学习总结

​		首先拉一套代码到自己的电脑上，然后将它编译成user或者ud，可以让后面编译变快。要是想清除编译可以用./clean 加 tab。然后就是修改错误了，可以去gerrit、wcp等地方搜索解决方案。修改完之后再编译一次，刷机验证是否解决问题。解决问题后移植代码再进行提交，在gerrit中添加审核者，最后在我的工作台上修改完成状态即可。



### 三、学习问题

​		刷机包刷机后机子坏了，不知道什么原因也不知道怎么找原因。（不是该手机的刷机包）