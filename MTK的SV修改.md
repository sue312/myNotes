<h1><center>MTK的SV修改</center></h1>

1.先拉两套代码，一套项目代码一套对应的modem代码，都要进行切换分支。

![](/home/wangxin/Pictures/100901.png)

2.在projectconfig.mk中找到CUSTOM_MODEM对应的项目名（VQ558_3GB1B2B5B8_4GB1B3B7B8B20B28B_OTP_VQ558_GH5581_VDF_N12），然后去modem中找到这个项目名（VQ558_3GB1B2B5B8_4GB1B3B7B8B20B28B_OTP_VQ558_GH5581_VDF_N12）.mak文件，打开该文件搜索CUSTOM_OPTION，

然后你会看到下图，如果你是MR1你需要自己添加一个，如果不是将其中的内容拿去找就好了（只有一个会找到）。

![](/home/wangxin/Pictures/100902.png)

3.去/mcu/common/interface/service/nvram/nvram_data_items.h和/mcu/pcore/custom/service/nvram/nvram_data_items.c下找到对应的内容。nvram_data_items.h中005加一，nvram/nvram_data_items.c中0x20改为0x30（根据需求来）。

![100903](/home/wangxin/Pictures/100903.png)


![](/home/wangxin/Pictures/100905.png)


![100904](/home/wangxin/Pictures/100904.png)



4.改完提交modem修改。

然后在alps同级目录下新建一个modem文件夹，将modem文件夹下的三个文件拷贝到这个新建的文件夹中。

在projectconfig.mk中找到CUSTOM_MODEM对应的项目名（VQ558_3GB1B2B5B8_4GB1B3B7B8B20B28B_OTP_VQ558_GH5581_VDF_N12），复制项目名的值到/modem/mcu/script/project.mk中。

再找到mcu下面的mk脚本文件。如果if(! -e {"make/projects/" . $bldmak})中有{}，则删除{}。没有不做处理。

![](/home/wangxin/Pictures/100906.png)

5.编译modem，我们将使用Dock容器进行modem的编译。

```
1.安装(这个是在ubuntu16上的安装包,通用的)：
   ①解压docker1806ce-ubuntu16.rar
   ②安装
   sudo dpkg -i *.deb
   sudo docker --version

2.Ubuntu下docker使用非root权限运行docker
   ①sudo usermod -aG docker $USER          将用户加入该 group 内      
   ②sudo reboot                            重启电脑     

3.导入镜像：
 ①解压rar
   rar x wangxiaoxi-docker-mtk-aosp.rar
 ②载入镜像
   docker load -i wangxiaoxi-docker-mtk-aosp.tar
   docker images  //可以看到镜像已经导入

4.运行容器：
   docker run -idt -v /:/data/hosts -p 1111:22 --name kaios_container wangxiaoxi/mtk_aosp
   docker run -idt -v /:/data/hosts \

5.连接容器(编modem从这里开始)
   docker start `docker ps -aq`
   ssh wangxiaoxi@localhost -p 1111
   密码123

6.编译
cd hosts/home/wangxin/sueproject/项目名/modem/mcu
./mk

ls 找到需要编译的路径
```

![](/home/wangxin/Pictures/100908.jpg)

6.将output里面生成的文件放入sagereal/custom/modem中。
